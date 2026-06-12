# Violation Detection Module - Overview

**Master feature for real-time AI-powered traffic violation detection on node vehicles.**

---

## Feature Summary

The violation detection module is the **core intelligence** of CivicMesh. It runs on-device (Raspberry Pi) to detect traffic violations in real-time using computer vision and vehicle telemetry.

**Key Characteristics:**
- On-device inference (no cloud dependency for detection)
- Low latency (<200ms per frame)
- Multiple detection models (ALPR, lane detection, speed, signal state)
- Confidence scoring (enables human officer review)
- Edge buffering (survives connectivity loss)

---

## Subfeatures (10 Violation Types)

| # | Violation | Model Type | Confidence | Phase | Priority |
|---|-----------|-----------|-----------|-------|----------|
| 1 | **Speeding** | OBD + geofence | 99% | MVP | CRITICAL |
| 2 | **Lane Violations** | CV segmentation | 85% | Phase 1 | HIGH |
| 3 | **ALPR** (plate reading) | YOLO + OCR | 98% | MVP | CRITICAL |
| 4 | **Bus Lane Obstruction** | CV + geofence | 95% | Phase 1 | HIGH |
| 5 | **Stop Sign Running** | CV + signal state | 95% | Phase 1 | HIGH |
| 6 | **Accessible Parking** | ALPR + DB | 99% | Phase 2 | MEDIUM |
| 7 | **School Zone Violations** | Geofence + time | 95% | Phase 2 | MEDIUM |
| 8 | **Expired Plates** | ALPR + MTO DB | 99% | Phase 2 | HIGH |
| 9 | **Construction Zones** | Geofence + speed | 85% | Phase 3 | MEDIUM |
| 10 | **Snow Route Parking** | Geofence + time | 97% | Phase 2 | HIGH |

---

## Architecture Overview

```
Edge Node (Raspberry Pi)
├── Camera Capture (1440p/30fps)
│   └── Frame buffering (10 sec rolling buffer)
├── Edge AI Models (TensorFlow Lite)
│   ├── YOLO (vehicle, plate, sign detection)
│   ├── UNet (lane segmentation)
│   ├── MobileNet (signal classifier)
│   └── Tesseract (plate OCR)
├── Telemetry Polling (10 Hz)
│   ├── OBD (speed, RPM, heading)
│   ├── GPS (coordinates, accuracy)
│   └── Timestamp sync (NTP)
├── Violation Detection Logic
│   ├── Speed check (OBD > limit?)
│   ├── Lane check (CV heatmap analysis)
│   ├── Plate check (ALPR + DB lookup)
│   └── Signal check (signal state + motion)
├── Incident Packaging
│   ├── Clip extraction (context)
│   ├── Metadata assembly
│   ├── Compression (H.264)
│   └── Encryption (AES-256)
└── Upload Queue
    ├── Local buffering (100 incidents)
    └── Cellular upload (LTE)
```

---

## MVP Scope (Phase 0)

For the first 12 weeks, focus on:
- ✓ **Speeding detection ONLY**
- ✓ ALPR (plate reading)
- ✓ Basic incident packaging
- ✓ Upload to cloud

**Why this scope?**
- OBD speed is ground truth (easiest to validate)
- No model training required (use pre-trained ALPR)
- Proves concept with single, high-confidence violation
- Fast to build & test

**Defer to Phase 1:**
- Lane detection (requires training data)
- Signal state detection (complex edge cases)
- All other violation types

---

## Test Coverage Map

### Unit Tests
- [ ] Speed calculation (OBD value vs. limit)
- [ ] ALPR confidence scoring
- [ ] Geofence containment (point-in-polygon)
- [ ] Timestamp accuracy
- [ ] Metadata JSON structure

### Integration Tests
- [ ] End-to-end: frame → detection → incident → upload
- [ ] Multiple violations in sequence
- [ ] Concurrent detections (multiple violation types)
- [ ] Fallback modes (no GPS, no OBD)

### Performance Tests
- [ ] Inference latency <200ms per frame
- [ ] Throughput: 30 fps sustained
- [ ] Memory usage <500 MB (Raspberry Pi constraint)
- [ ] Storage: <1 GB buffer for 100 incidents

### Acceptance Tests
- [ ] Real dashcam video (test data)
- [ ] Known violations detected
- [ ] Known non-violations not flagged
- [ ] False positive rate <5%

---

## Dependencies

### Software Libraries
```
tensorflow-lite          # Edge AI inference
opencv-python          # Computer vision
pytesseract            # OCR (plate text)
requests               # HTTP uploads
numpy, pillow          # Image processing
```

### Pre-trained Models
- `alpr.tflite` — Plate detection (YOLOv5-nano)
- `plate_ocr` — Tesseract (pre-trained)
- `object_detection.tflite` — Vehicle/sign detection
- `lane_detection.tflite` — (Phase 1) Lane segmentation

### Test Data
- Dashcam video (MP4): 5–10 min samples
- OBD scenarios (JSON): Speeding, normal, edge cases
- Ground truth (JSON): Expected detections per video

### Hardware (For Real Deployment)
- Raspberry Pi CM4 (4GB+ RAM)
- Dashcam (USB or CSI)
- OBD-II dongle (serial/USB)
- GPS module (UART)
- LTE modem (USB)

**For MVP Testing: Simulators (no hardware required)**

---

## Success Criteria

### Phase 0 (MVP)
- ✓ Speeding detection: <5% false positive rate
- ✓ ALPR: 95%+ plate reading accuracy
- ✓ Inference latency: <200ms
- ✓ Uptime: 99%+
- ✓ Works on real OC Transpo buses

### Phase 1
- ✓ All Phase 0 criteria maintained
- ✓ Lane detection: 85%+ confidence
- ✓ Bus lane obstruction: 95%+ confidence
- ✓ Stop sign running: 95%+ confidence

### Phase 2
- ✓ All above + 7 additional violation types
- ✓ False positive rate still <5% across all types
- ✓ Scales to 200+ vehicles

---

## Integration Points

**Upstream:**
- Camera (video frames)
- OBD (vehicle telemetry)
- GPS (location)

**Downstream:**
- Incident Builder (packages violations)
- Upload Manager (sends to cloud)
- Local Buffer (survives disconnection)

**External:**
- MTO Database (plate validation)
- Geofence Data (speed zones)
- Digital Twin (road condition context)

---

## Known Constraints & Workarounds

### Constraint: Limited Edge Compute (Raspberry Pi)
**Problem:** Can't run heavy models like full YOLOv8  
**Solution:** Use quantized TFLite models (4x smaller, 2–3x faster)

### Constraint: Variable Lighting (night, rain, glare)
**Problem:** CV models trained on sunny dashcam footage  
**Solution:** Collect diverse training data; adjust thresholds per weather

### Constraint: Hardware lead time (4–6 weeks)
**Problem:** Can't build/test on real hardware yet  
**Solution:** Use camera/OBD simulators; swap drivers when hardware arrives

### Constraint: MTO Database availability
**Problem:** Real-time plate lookup might fail  
**Solution:** Cache results locally; fall back to plate text only

---

## Next Documents

1. **01_SPEEDING.md** — Detailed speeding detection test suite
2. **02_LANE_VIOLATIONS.md** — Lane violation detection (Phase 1)
3. **03_ALPR.md** — License plate recognition
4. **04_BUS_LANE.md** — Bus lane obstruction detection
5. **05_STOP_SIGN.md** — Stop sign running
6. **[etc.]** — Other violation types

---

## Quick Reference

**Violation Detection Quick Facts:**
- **MVP:** Speeding + ALPR only
- **Edge inference:** TensorFlow Lite on Raspberry Pi
- **Latency:** <200ms per frame
- **Throughput:** 30 fps sustained
- **Accuracy:** >95% for all types
- **False positive rate:** <5%
- **Training required?** Lane detection only (Phase 1)

**Testing Approach:**
1. **Week 1-2:** Unit tests (speed logic, ALPR confidence)
2. **Week 3-4:** Integration tests (frame → detection → incident)
3. **Week 5-6:** E2E tests (real video + OBD data)
4. **Week 7-8:** Field trial (5 buses, live data)

**Start Here:**
→ Read `01_SPEEDING.md` for MVP implementation
