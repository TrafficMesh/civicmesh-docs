# Violation Detection Models (AI/CV)

## Overview
On-device edge AI detects violations in real-time, running TensorFlow Lite quantized models on Raspberry Pi. Only evidence clips + metadata are uploaded (not raw video), reducing bandwidth and preserving privacy.

---

## Violation Types & Detection Methods

### 1. Speeding (GPS + OBD Delta)
| Aspect | Detail |
|--------|--------|
| **Detection** | Compare OBD vehicle speed vs. posted speed limit (geofence-based) |
| **Confidence** | High (95%+) — speed is ground truth from OBD |
| **Threshold** | 10+ km/h over limit (tunable per jurisdiction) |
| **False Positive Risk** | Low. OBD speed is reliable; risk is geofence error (e.g., wrong speed zone loaded) |
| **Tier Deployment** | All (T1, T2, T3, T4) |
| **Evidence** | 5-second clip (2 sec before, 3 sec after threshold crossed) + OBD log |

**Implementation:**
- Maintain speed zone database (geofence polygons + speed limits, sourced from city GIS)
- Poll OBD at 10 Hz; detect when speed > limit + 10 km/h
- Trigger incident capture; hold 2 sec pre-clip in rolling buffer
- Log frame count, timestamp, OBD speed, GPS location

**Confidence scoring:**
- 99%: Clear OBD speed reading, stable 5+ frames above threshold
- 95%: Momentary spike (valid, e.g., accelerating through intersection)
- 85%: GPS-only speed estimate (fallback if OBD unavailable) — lower confidence

---

### 2. Lane Violations (Lane Marking Segmentation)
| Aspect | Detail |
|--------|--------|
| **Detection** | Vehicle crosses single/double yellow, fails to stay in lane |
| **Model** | Semantic segmentation (UNet-style, quantized to INT8) |
| **Confidence** | Medium (85%+) — sensitive to lighting, lane marking visibility |
| **Threshold** | >30% of vehicle bounding box outside lane >1 second |
| **False Positive Risk** | Medium. Rain, snow, faded markings cause false detections. Weather-aware thresholding required. |
| **Tier Deployment** | T1, T2, T3, T4 (all) |
| **Evidence** | 3-second clip (1 sec before, 2 sec after crossing) + lane segmentation heatmap overlay |

**Implementation:**
- TFLite model trained on dashcam footage (OpenStreetMap road database augmented with synthetic lane data)
- Input: 416×416 RGB frame, output: 416×416 probability map (lane pixels vs. non-lane)
- Post-process: Find vehicle bounding box (YOLO), check overlap with non-lane pixels
- Temporal smoothing: Require 3+ consecutive frames outside lane to trigger incident (reduce flicker from shadows)

**Confidence scoring:**
- 95%: Clear lane markings, high pixel confidence in segmentation
- 85%: Partial visibility (rain, glare), but vehicle clearly off-center
- 75%: Faded markings or weather; human review needed
- <75%: Discarded

**Tuning:** Model retraining every quarter as city road conditions change (snow, new paint, etc.).

---

### 3. ALPR (Automatic License Plate Recognition)
| Aspect | Detail |
|--------|--------|
| **Detection** | Extract license plate from vehicle in frame |
| **Model** | Two-stage: detection (locate plate in frame) + recognition (OCR on plate) |
| **Detection Model** | YOLOv5-nano (plate bounding box) |
| **OCR Model** | Tesseract-OCR or OpenALPR engine (both quantized for edge) |
| **Confidence** | Very High (98%+) — plate text is critical for enforcement |
| **False Positive Risk** | Low, but OCR ambiguity (I/1, 0/O) requires post-processing |
| **Tier Deployment** | All (critical for enforcement) |
| **Evidence** | Plate region crop + full frame + metadata |

**Implementation:**
- YOLOv5-nano runs on every frame; detects license plates
- For plates with >80% detection confidence, crop region and run Tesseract OCR
- Post-process OCR: Check plate format (Ontario: 4 letters + 3 numbers), reject malformed
- Cross-check against MTO vehicle registration database in real-time (if connectivity available, else batch later)
- Log plate text, confidence, and vehicle/person biometrics (NOT stored; used only for this frame)

**Confidence scoring:**
- 99%: Clear plate, all characters recognized with high confidence
- 95%: One ambiguous character (e.g., I/1), but context disambiguates
- 85%: Partial plate or reflective glare; human review helpful
- <85%: Discarded or flagged for manual review

**MTO Integration:**
- Query MTO registration DB: plate registered, vehicle type, owner address
- Return immediately if available; log cache hit
- Fallback: Queue for batch MTO lookup if no connectivity (process when vehicle docks)

---

### 4. Bus Lane Obstruction (Fixed Location + Occupancy)
| Aspect | Detail |
|--------|--------|
| **Detection** | Vehicle present in bus lane (geofence) when not authorized |
| **Model** | Object detection (vehicle bounding box) + geofence check |
| **Confidence** | High (95%+) — geofence + presence is reliable |
| **Threshold** | Vehicle in lane >2 seconds (exclude brief lane changes) |
| **False Positive Risk** | Low. Main risk: geofence misconfiguration or authorized vehicles (e.g., police, ambulance) |
| **Tier Deployment** | T1 (OC Transpo only, Tier 1–3) |
| **Evidence** | 5-second clip + bus lane geofence polygon overlay |

**Implementation:**
- Load bus lane geofences (from city GIS, updated weekly)
- YOLO detects all vehicles in frame
- Check if vehicle bounding box center overlaps bus lane geofence
- Temporal smoothing: Require 10+ consecutive frames (3+ seconds) in lane to trigger
- Exception list: Police, ambulance, TPS, city vehicles (plate-based or geofence-aware authorization)

**Confidence scoring:**
- 99%: Vehicle clearly in lane, stable presence >5 frames
- 95%: Vehicle straddling lane; mostly outside
- <95%: Vehicle briefly entering lane (e.g., lane change); human review if near threshold

---

### 5. Stop Sign Running (Traffic Signal State + Vehicle Motion)
| Aspect | Detail |
|--------|--------|
| **Detection** | Vehicle fails to stop at traffic signal/stop sign |
| **Model** | Two-stage: signal state detection (red light) + vehicle motion (moving through stop) |
| **Signal Detection** | MobileNet-v2 classifier (red vs. green vs. yellow) |
| **Motion Detection** | Optical flow (compare frame-to-frame motion) |
| **Confidence** | High (95%+) — signal state + motion is reliable |
| **Threshold** | Vehicle motion >5 km/h when signal is red |
| **False Positive Risk** | Medium. Misidentified signal color (sun glare, wrong signal in frame) |
| **Tier Deployment** | T1, T2, T3, T4 |
| **Evidence** | 3-second clip (1 sec before red, 2 sec after running) + signal state label |

**Implementation:**
- Traffic light detector model: 3-class MobileNet-v2 (red/yellow/green) on cropped light region
- Detections are per-frame; require 5+ consecutive frames of same color
- Optical flow: Compute motion vectors between frames using Lucas-Kanade algorithm
- Trigger: Red light detected >3 frames + vehicle motion vectors >5 km/h
- Temporal: Must cross stop line while light is red (frame-by-frame check)

**Confidence scoring:**
- 99%: Clear red light, stable color, clear vehicle motion into intersection
- 95%: Red light slightly obscured (glare), but motion clear
- 85%: Ambiguous light color (yellow?) or moderate motion
- <85%: Discarded

**Tuning:** Reposition camera if signal detection fails repeatedly (misaligned to signals).

---

### 6. Accessible Parking Violations (ALPR + Provincial DB)
| Aspect | Detail |
|--------|--------|
| **Detection** | Vehicle parked in accessible spot without valid permit |
| **Model** | ALPR (read plate) + MTO database lookup (permit status) |
| **Confidence** | Very High (99%+) — database lookup is authoritative |
| **Threshold** | Vehicle stationary (OBD speed = 0) in accessible spot >30 seconds |
| **False Positive Risk** | Very low. DB lookup is ground truth. Risk: geofence error (accessible spot misconfigured). |
| **Tier Deployment** | T1, T2, T3 (city vehicles in motion) |
| **Evidence** | Photo of plate + accessible spot sign + metadata |

**Implementation:**
- Maintain database of accessible parking spot locations (geofences, 10m radius)
- When vehicle detected stationary in geofence: run ALPR
- Query MTO database: does this plate have valid accessible permit?
- If no permit, or permit expired: flag as violation
- Require confirmation (vehicle still there after 30+ seconds) to reduce false positives from temporary parking

**Confidence scoring:**
- 99%: Clear plate reading, confirmed permit lookup shows expired or no permit
- 95%: Permit lookup inconclusive; require manual review

---

### 7. School Zone Violations (Time-Gated Speed + Location)
| Aspect | Detail |
|--------|--------|
| **Detection** | Speed >40 km/h in school zone during school hours |
| **Model** | Geofence (school zone) + time check (7–9 AM, 2–4 PM weekdays) + speed check |
| **Confidence** | High (95%+) |
| **Threshold** | Speed >40 km/h (20% buffer below posted limit) during active hours |
| **False Positive Risk** | Low. Time-gating ensures enforcement only during school activity. |
| **Tier Deployment** | T1, T2, T3 |
| **Evidence** | 5-second clip + school zone geofence overlay |

**Implementation:**
- Load school zone geofences and hours (from city GIS)
- Check if vehicle GPS inside geofence AND current time is within active hours
- If yes: enable lowered speed threshold (e.g., 40 km/h for 60 km/h zone)
- Trigger incident if speed exceeded; otherwise suppress detection

**Confidence scoring:**
- 99%: Clear speed violation during confirmed school hours
- 95%: Borderline time (e.g., 3:59 PM vs. 4:00 PM); human discretion

---

### 8. Expired / Suspended Plates (ALPR + MTO DB)
| Aspect | Detail |
|--------|--------|
| **Detection** | Vehicle plate is expired or suspended |
| **Model** | ALPR + MTO registration database lookup |
| **Confidence** | Very High (99%+) — DB lookup is authoritative |
| **Threshold** | Vehicle detected with expired/suspended plate |
| **False Positive Risk** | Very low. DB is ground truth. |
| **Tier Deployment** | All (flagged to police, not municipal enforcement) |
| **Evidence** | Plate, timestamp, location, MTO lookup result |

**Implementation:**
- Every vehicle ALPR triggers MTO lookup (if connectivity available)
- MTO returns plate status (active, expired, suspended, unregistered)
- Expired: Registration lapsed; vehicle should not be driven
- Suspended: Unpaid fines or criminal hold
- Flag for police queue (not municipal enforcement); include location, timestamp, and photo

**Confidence scoring:**
- 99%: Confirmed lookup shows expired/suspended status

**Routing:** Alert goes to Ottawa Police dispatch (separate queue from municipal enforcement).

---

### 9. Construction Zone Violations (Geofence-Activated)
| Aspect | Detail |
|--------|--------|
| **Detection** | Speed violation or lane violation within active construction zone |
| **Model** | Geofence activation + speed/lane detection |
| **Confidence** | High (95%+) — geofence is authoritative |
| **Threshold** | Speed >20 km/h over construction limit OR lane violation in zone |
| **False Positive Risk** | Low. Geofence is managed by contractor. |
| **Tier Deployment** | T3 (contractor kits only) |
| **Evidence** | 5-second clip + construction zone geofence overlay |

**Implementation:**
- When contractor node kit is deployed, city provides active geofence (zone boundaries, speed limits, duration)
- Geofence is time-limited (e.g., expires after 30 days or upon kit return)
- All detections (speed, lanes) within this geofence are flagged
- Contractor (or city) reviews flagged incidents, issues citations to protect crew

**Confidence scoring:**
- 95%: Violation clearly within active geofence; speed/lane confirmed

---

### 10. Snow Route Violations (Geofence + Time + Parked Vehicle)
| Aspect | Detail |
|--------|--------|
| **Detection** | Vehicle parked in no-parking snow route zone during enforcement window |
| **Model** | Geofence (snow route) + time check (e.g., 2 AM–6 AM, Nov–Mar) + stationary vehicle |
| **Confidence** | High (95%+) |
| **Threshold** | Vehicle stationary (OBD = 0) in snow route geofence during enforcement window >1 minute |
| **False Positive Risk** | Low. Time-gating and geofence are authoritative. |
| **Tier Deployment** | T2 (snow plows pass every route) |
| **Evidence** | Photo of plate + snow route sign + timestamp |

**Implementation:**
- Load snow route geofences and enforcement windows (city GIS, updated annually pre-winter)
- When OC Transpo plow drives route: continuously check for stationary vehicles in geofence
- OBD speed = 0 for >1 minute: stationary vehicle detected
- ALPR captures plate; flag for bylaw
- After plow passes, vehicle still parked = citation candidate

**Confidence scoring:**
- 99%: Vehicle clearly stationary during enforcement window, in marked snow route
- 95%: Borderline time or geofence edge

---

## Confidence Thresholds & Review Queue Routing

### Fast-Track Queue (Auto-Issue)
- **Confidence:** 95%+ 
- **Violation types:** Speeding (OBD-based), ALPR (permit lookups), snow route violations
- **Officer action:** Minimal (spot-check; usually auto-approved)
- **Target review time:** <30 seconds per citation
- **Volume:** ~60% of incidents

### Manual Review Queue
- **Confidence:** 80–95%
- **Violation types:** Lane violations, school zones, construction zones, stop sign running
- **Officer action:** Watch clip, confirm violation, approve or reject
- **Target review time:** 1–2 minutes per citation
- **Volume:** ~35% of incidents

### Reject / Discard
- **Confidence:** <80%
- **Action:** Automatically discarded; no officer review
- **Volume:** ~5% of incidents
- **Reason:** Too much uncertainty; risk of false positive citation

---

## Model Retraining & Versioning

### Schedule
- **Daily:** Aggregate field detections (confidence distribution, false positive feedback from officers)
- **Weekly:** Retrain models on week's new data (incremental learning)
- **Monthly:** Formal model evaluation on test set; decide go/no-go for production
- **Quarterly:** Major model versions (e.g., switch from YOLO v5 to v8) with extended testing

### Metrics to Track
- **Precision:** Of flagged violations, what % are valid? (Target: >95%)
- **Recall:** Of actual violations on road, what % are detected? (Target: >90%)
- **False positive rate:** Incidents rejected by officers / total incidents (Target: <5%)
- **Inference latency:** Time to process single frame (Target: <200 ms on Raspberry Pi)

### Version Control
- Each model tagged with timestamp + training data snapshot
- Firmware includes model hash; automatic fallback if new model underperforms
- Canary rollout: Deploy to 10% of fleet first, monitor metrics before full rollout

---

## Edge Compute Performance

### Hardware Constraints (Raspberry Pi CM4, 4GB RAM)

| Task | Latency | Notes |
|------|---------|-------|
| ALPR (YOLOv5-nano + Tesseract) | ~150 ms | Bottleneck; run only on selected frames |
| Lane segmentation (UNet) | ~100 ms | Run every frame |
| Stop sign detection (MobileNet) | ~50 ms | Run every frame |
| Speed + OBD poll | <5 ms | Lightweight |
| **Total per frame (30 fps)** | ~30 ms (frame rate allows) | Feasible at 30 fps |

### Optimization Strategies
- **Model quantization:** INT8 quantization reduces model size 4x, inference latency 2–3x
- **Selective inference:** Run expensive models (ALPR) only on frames with vehicle detected
- **Batching:** Not applicable in real-time; process frame-by-frame
- **GPU acceleration:** Not available on Raspberry Pi; CPU-bound

### Memory Budget
- OS + Python runtime: ~800 MB
- TFLite models (4 loaded): ~400 MB
- Rolling video buffer (10 frames): ~50 MB
- Free for inference: ~2.7 GB (sufficient)

---

## Training Data & Model Sources

### Open Source Models
- **YOLO:** YOLOv5/v8 (Ultralytics, MIT license)
- **Lane detection:** OpenLaneDetection (Tusimple, Apache 2.0)
- **ALPR:** OpenALPR (Dual license: open + commercial)
- **Tesseract OCR:** Open source (Apache 2.0)
- **MobileNet:** TensorFlow Hub (Apache 2.0)

### Training Data Sources
- **City dashcam footage:** Use pilot phase footage to retrain locally
- **Public datasets:** 
  - KITTI (autonomous driving, lanes, vehicles)
  - BDD100K (street scenes, traffic, night)
  - COCO (general object detection)
  - YouTube (license plates, creative commons)

### Model Ownership
- All models trained on city data are city-owned (per MSSP agreement)
- Open source base models remain under original licenses (AGPL, Apache 2.0)
- Commercial models (Plate Recognizer API, licensed) are third-party

---

## Future Model Extensions

- **mmWave radar:** Following distance detection (Year 3–4)
- **Multi-spectral (IR):** Night vision, pedestrian detection
- **Acoustic:** Exhaust noise violations, sirens
- **Behavioral:** Aggressive driving (hard braking, sudden acceleration)
