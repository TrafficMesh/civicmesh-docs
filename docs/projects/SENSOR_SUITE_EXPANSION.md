# Sensor Suite Expansion: Cameras, Radar, OBD, LiDAR Integration

**Comprehensive roadmap for expanding CivicMesh detection and analytics capabilities through native vehicle sensor integration.**

---

## Vision

CivicMesh's power comes from **sensor fusion**: combining multiple sensor modalities to create complete understanding of road conditions, vehicle behavior, and violations.

Modern vehicles (2020+) provide:
- **Cameras:** Front, rear, side (understanding what things are)
- **Radar:** 77 GHz MMW (measuring distance + velocity)
- **LiDAR:** (Tesla, Volvo, others) High-precision 3D mapping
- **OBD/CAN:** Vehicle telemetry (speed, RPM, engine state)
- **IMU:** Acceleration, rotation, braking detection
- **GPS:** Location + heading

**Integration philosophy:** Use what's already there. No roof mounts. No visible external hardware.

---

## Part 1: Multi-Camera Architecture

### 1.1 Camera Placement & Use Cases

Modern vehicles typically have:
- **Front (Primary):** Road ahead, lane markings, traffic lights, pedestrians, vehicles
- **Rear:** Vehicles behind, reverse traffic, follow-distance violations
- **Left Side:** Lane change detection, cyclist in blind spot, adjacent traffic
- **Right Side:** Same as left, mirror for right-hand traffic or parking
- **Interior:** Ride-sharing (passenger safety, driver verification)
- **Roof (Optional for OEM):** 360° coverage, bird's-eye parking view

**CivicMesh uses:**

| Camera | Detection Tasks | Output |
|--------|-----------------|--------|
| **Front** | Lane violations, speeding, red lights, pedestrian hazards | Evidence for speeding/lane/traffic light violations |
| **Rear** | Tailgating, hit-and-run fleeing vehicles, reverse hazards | Following-distance violations, incident documentation |
| **Side** | Blind spot vehicles, lane changes, cyclists, parking violations | Reckless lane change detection, HOV/bus lane violations |
| **Interior** | Driver behavior, passenger safety (ride-sharing), seatbelt | Driver fatigue, phone distraction, ride-share safety scoring |
| **Roof** | Panoramic context, construction zones, hazards | Advanced scene understanding (future) |

---

### 1.2 Camera Specifications (OEM Native)

| Aspect | Specification | Purpose |
|--------|---------------|---------|
| **Resolution** | 1440p (2560×1440) minimum | Read license plates at 80+ km/h |
| **Frame Rate** | 30 fps (60 fps if bandwidth) | Smooth motion, collision detection |
| **FOV (Front)** | 160°+ wide angle | Full lane width + adjacent lanes |
| **FOV (Rear)** | 160°+ | Following traffic detection |
| **Low-Light** | f/1.6 aperture or better | Night operation, tunnels, low-light conditions |
| **Codec** | H.264 (MP4 container) | Standard, hardware-accelerated on OEM SoCs |
| **Bitrate** | 4–8 Mbps (2 Mbps for compression) | ~500 KB per 10-sec clip at 2 Mbps |

**OEM integration:** Vehicles built after 2020 typically have these specs or better.

---

### 1.3 Camera-Based Detection

**Edge inference on-device (TPU/GPU if available):**

1. **License Plate Recognition (ALPR)**
   - TensorFlow Lite model (quantized)
   - 100 ms per frame
   - Output: Plate string + confidence (95%+ accuracy)
   - Privacy: Hash plate before network transmission

2. **Face Detection & Blurring**
   - Detect faces in violation evidence
   - Blur in real-time (before upload)
   - Output: Redacted video (privacy-preserving)

3. **Lane Detection**
   - Semantic segmentation (lane markings)
   - Detect lane changes, crossings
   - Output: Lane position + change events

4. **Traffic Light Recognition**
   - Red/yellow/green state detection
   - Timestamp of signal change
   - Output: Red-light violation (running red light)

5. **Pedestrian & Cyclist Detection**
   - YOLO-based object detection
   - Track bounding boxes
   - Output: Pedestrian strike risk alerts

6. **Scene Classification**
   - School zone detection (signs)
   - Construction zone detection (cones, barriers)
   - Speed-limited area detection
   - Output: Context for violation classification

7. **Object Detection (YOLO v5)**
   - Vehicles, pedestrians, cyclists, motorcycles
   - Emergency vehicles (priority)
   - Traffic cones, barriers, debris
   - Output: Scene context for violation assessment

---

## Part 2: Radar Integration

### 2.1 Radar Architecture (OEM Native)

Modern vehicles have:
- **Front long-range radar:** 150–300m range (Adaptive Cruise Control)
- **Corner radars:** Front left/right (blind spot monitoring)
- **Rear radars:** Parking + cross-traffic alerts

**Frequency:**
- 77 GHz (most modern, Western manufacturers)
- 24 GHz (older vehicles, may be phased out)

**Data output per frame:**
```
Object 1:
 - Distance: 45 m
 - Relative velocity: -12 km/h (closing)
 - Azimuth: +4° (slightly right of center)
 - Tracking ID: 123 (same object across frames)

Object 2:
 - Distance: 90 m
 - Relative velocity: -25 km/h
 - Azimuth: -2° (slightly left)
```

---

### 2.2 Radar-Based Detection

1. **Following Distance Monitoring**
   - Track vehicle ahead via front radar
   - Calculate following distance (m)
   - Time-to-collision (TTC) estimation
   - Alert if <2 seconds (standard safety threshold)
   - Output: Tailgating violation (if distance < regulation minimum)

2. **Collision Risk Scoring**
   - Combine radar (distance) + camera (classification)
   - Estimate time-to-collision
   - Track hard-braking events (radar velocity changes)
   - Output: Collision risk percentile (for insurance)

3. **Speed Verification**
   - Radar speed of own vehicle (CAN bus provides OBD speed)
   - Cross-check: CAN speed vs. radar targets vs. GPS
   - Detect speed spoofing attempts (invalid combos)
   - Output: Verified vehicle speed (more defensible in court)

4. **Blind Spot Vehicle Detection**
   - Corner radars track adjacent vehicles
   - Detect vehicles in blind spots
   - Alert if driver attempts unsafe lane change
   - Output: Reckless lane change violation (context)

5. **Rear Traffic Monitoring**
   - Rear radar detects vehicles approaching
   - Track closing speed
   - Detect rear-end collision risk
   - Output: Incident severity scoring (hit-and-run likelihood)

---

### 2.3 Radar Hardware Details

**For OEM-native vehicles (no retrofit needed):**

| Manufacturer | Module | Frequency | Range | Integration |
|--------------|--------|-----------|-------|-------------|
| **Tesla** | Custom 77 GHz | 77 GHz | 0–200m | CAN bus (direct) |
| **Ford** | Bosch MRR301 | 77 GHz | 0–160m | CAN bus (SYNC IVI) |
| **GM** | Delphi ESR | 77 GHz | 0–160m | CAN bus (OnStar) |
| **Volvo** | Bosch or Conti | 77 GHz | 0–200m | CAN bus (Volvo Connect) |
| **Toyota** | Denso 3D | 77 GHz | 0–200m | CAN bus (optional) |

**For retrofit vehicles (Phase 2+):**
- Bosch MRR311 (aftermarket, $150–200)
- Infineon XENSIV (developer boards, $120–180)
- Integration: USB or CAN adapter to Raspberry Pi

---

## Part 3: OBD/CAN Integration

### 3.1 OBD Data Points

CivicMesh accesses (via CAN bus API):

| Parameter | PID | Use Case | Update Rate |
|-----------|-----|----------|-------------|
| **Vehicle Speed** | 010D | Primary speed verification | 10 Hz (100 ms) |
| **Engine RPM** | 010C | Acceleration severity, idle detection | 5 Hz |
| **Throttle Position** | 0111 | Aggressive acceleration detection | 10 Hz |
| **Brake Status** | Custom | Hard-braking detection, event severity | 20 Hz |
| **Fuel Level** | 012F | Vehicle health monitoring | 1 Hz |
| **Coolant Temp** | 0105 | Engine stress detection | 1 Hz |
| **Fuel Consumption** | Custom | Fleet efficiency (not enforcement) | 1 Hz |
| **Steering Angle** | Custom (if available) | Lane change validation | 10 Hz |
| **Gear Selection** | Custom | Reverse, neutral detection | 5 Hz |
| **Odometer** | 0131 | Trip distance (some vehicles) | 0.1 Hz |

---

### 3.2 OBD-Based Detection

1. **Speed Verification**
   - OBD speed (engine ECU, most reliable)
   - GPS speed (less accurate, +/- 5 km/h in urban)
   - Radar speed (if available, cross-check)
   - Output: Speeding violation (verified across 3 sources)

2. **Aggressive Driving**
   - Hard acceleration: Throttle position jump + RPM spike
   - Hard braking: Brake pedal + vehicle deceleration
   - Aggressive cornering: Steering angle + lateral acceleration (IMU)
   - Output: Reckless driving warning

3. **Idle & Emissions**
   - Detect prolonged idle (engine running, vehicle stopped)
   - Flag for emissions violation or fuel waste
   - Output: Idle violation (Toronto bylaw)

4. **Vehicle Diagnostics**
   - Engine trouble codes (DTC)
   - Sensor failures (lambda, O2)
   - Emissions system health
   - Output: Vehicle fitness for service (advisory, not enforcement)

---

## Part 4: LiDAR Integration (Premium OEMs)

### 4.1 LiDAR Availability

Not all vehicles have LiDAR, but emerging OEM lineup includes:
- **Tesla (Roadster 2 onwards, some S/X):** Custom solid-state LiDAR
- **Volvo (next-gen XC90, EX90):** Luminar Iris
- **BMW (iX M60+):** Luminar LiDAR
- **Audi:** Luminar partnership (2025+)
- **Cadillac:** Super Cruise (some LiDAR models)

**CivicMesh support:** If available, use it; if not, operate without.

---

### 4.2 LiDAR-Based Detection

1. **High-Precision Distance Measurement**
   - 3D point cloud of scene
   - Accurate distance ±2–5 cm (vs. radar ±10–20 cm)
   - Detect exact vehicle boundaries

2. **3D Scene Reconstruction**
   - Build 3D map of intersection
   - Detect obstacles (construction cones, barriers, debris)
   - Lane boundary detection (precision)

3. **Pedestrian & Cyclist Detection**
   - Precise height + shape classification
   - Distinguish pedestrian from object
   - Detect near-miss collisions

4. **Autonomous Vehicle Training Data**
   - Collect real-world sensor data for AV programs
   - Generate labeled datasets (violations, hazards)
   - Federated learning: Train models locally (privacy-preserving)

---

## Part 5: IMU & Motion Sensors

### 5.1 IMU Data Points

Inertial Measurement Unit provides:
- **Acceleration (3-axis):** Longitudinal (forward/back), lateral (left/right), vertical (up/down)
- **Gyroscope (3-axis):** Roll, pitch, yaw (rotation rates)
- **Update rate:** 100+ Hz (fine-grained motion detection)

---

### 5.2 IMU-Based Detection

1. **Hard-Braking Detection**
   - Negative longitudinal acceleration spike
   - Combine with brake pedal (OBD) for validation
   - Severity: Classify as "hard" (>0.5g), "emergency" (>0.8g)
   - Output: Incident severity scoring

2. **Aggressive Cornering**
   - Lateral acceleration spike (>0.6g = aggressive turn)
   - Combine with steering angle
   - Output: Reckless driving warning

3. **Collision Detection**
   - Sudden large deceleration (>-2g typically = collision)
   - Spike in acceleration change rate
   - Immediately trigger incident recording (high priority)
   - Output: Collision event (emergency reporting)

4. **Lane Change Maneuver**
   - Lateral acceleration + steering angle + roll rate
   - Detect reckless lane changes (high speed + quick lateral)
   - Combine with camera lane detection
   - Output: Unsafe lane change violation

---

## Part 6: GPS + Geofencing

### 6.1 GPS Data

- **Location:** Latitude/longitude (±5m accuracy urban)
- **Altitude:** Elevation (some GPS receivers)
- **Heading:** Direction of travel (0–360°)
- **Speed:** GPS-based speed (less accurate than OBD)
- **DOP:** Dilution of precision (how reliable is this fix?)

---

### 6.2 GPS-Based Detection

1. **Geofencing (Speed Zones)**
   - School zone: Reduced speed limit applies
   - Construction zone: Variable speed limits
   - School crossing: Pedestrian-protective limits
   - Output: Speeding violation in restricted zone

2. **Location Correlation**
   - Match violation location with traffic light position
   - Verify pedestrian crossing location
   - Cross-reference with incident database

3. **Route Tracking**
   - Track vehicle path over time (anonymized)
   - Detect pattern violations (always speeding on street X)
   - Output: Habitual violator flag (advisory, not enforcement)

---

## Part 7: Sensor Fusion Engine

### 7.1 Multi-Sensor Consensus

**Philosophy:** A single sensor can be wrong. Multiple sensors must agree.

Example: **Speeding violation**

| Sensor | Data | Confidence |
|--------|------|------------|
| **OBD (CAN)** | 65 km/h | ⭐⭐⭐⭐⭐ (Ground truth) |
| **GPS** | 64 km/h | ⭐⭐⭐ (±5 m/s error) |
| **Radar** | 64 km/h | ⭐⭐⭐⭐ (±2 km/h, cross-vehicle) |
| **Camera** | Lane visible, clear road | ⭐⭐⭐⭐ (confirms not obstructed) |
| **Consensus** | Speed violation (65 > 50) | ⭐⭐⭐⭐⭐ (98% confident) |

---

### 7.2 Conflict Resolution

**When sensors disagree:**

Example: **Following distance**
- Radar says: 25m behind vehicle
- Camera says: Vehicle partially visible (maybe 20m)
- OBD says: Vehicle speed 50 km/h
- Resolution: Average to 22.5m, confidence reduced 10%

**Output:** "Marginal following distance (22.5m @ 50 km/h = 1.6 seconds, below 2-second threshold)"

---

## Part 8: Privacy-First Processing

All sensor processing happens **before upload:**

### 8.1 On-Device Redaction

1. **License Plates:** Blur in original frame
2. **Faces:** Detect + blur all human faces
3. **Vehicle Identification:** Never log VIN or plate as-is (hash only)
4. **GPS Location:** Store only geohash (10m precision), not exact coordinates
5. **Driver Identity:** Never collect; only vehicle ID + timestamp

### 8.2 Data Retention

- **Raw video frames:** Deleted immediately after inference (not stored)
- **Metadata:** Stored 30 days locally, then deleted unless incident
- **Incident evidence:** Stored 3 years (Ontario statute of limitations), then deleted
- **Driver telematics:** Deleted after driver opts out or vehicle de-enrolled

---

## Part 9: Deployment Phases

### Phase 1: Retrofit (Current)
- Single front camera + OBD dongle
- Basic ALPR + speed detection
- ~$507 per unit

### Phase 2: OEM Single Model
- Leverage vehicle's front + rear cameras
- OBD/CAN bus (native vehicle)
- 77 GHz radar (if equipped)
- Edge inference (if vehicle compute available)
- ~$0 hardware cost (software license only)

### Phase 3: OEM Multi-Sensor
- Front + rear + side cameras
- Multiple radars (front, corners, rear)
- LiDAR (if OEM equipped)
- Full IMU + GPS + OBD/CAN fusion
- Interior camera (ride-sharing)
- ~$0 hardware cost + $5–10/vehicle/year licensing

### Phase 4: Advanced Analytics
- Federated learning (train models on distributed fleet data)
- Autonomous vehicle development data
- Real-time 3D scene reconstruction
- Multi-vehicle coordination (platoon safety)

---

## Part 10: Competitive Advantage

| Aspect | CivicMesh | Traditional Fleet Mgmt (Geotab/Samsara) |
|--------|-----------|----------------------------------------|
| **Sensor diversity** | Camera+Radar+LiDAR+OBD+IMU fusion | OBD only (limited) |
| **Privacy** | On-device processing, driver owns data | Cloud-centralized (vendor owns) |
| **Enforcement** | Native, integrated (not add-on) | Add-on (third-party camera integration) |
| **Insurance integration** | Deep, native (Intact, TD partnerships) | Secondary, bolted-on |
| **Decentralization** | DAO-governed (community can challenge) | Corporate (vendor controls) |
| **Driver incentives** | Earn 85% (ride-sharing), 75% (delivery) | None (fleet owner benefits) |

---

## Part 11: Future Expansions

**Vision for 2030+:**

1. **V2V (Vehicle-to-Vehicle):** Vehicles communicate safety warnings (collision imminent, road hazard)
2. **V2I (Vehicle-to-Infrastructure):** Exchange data with roadside systems, traffic signals
3. **Federated Learning:** Train models on distributed fleet without sending raw data to cloud
4. **Autonomous Integration:** CivicMesh data feeds autonomous vehicle development (safety-critical)
5. **Radar Interference Mitigation:** Advanced signal processing for dense urban environments

---

## Summary

**CivicMesh evolves from single-sensor aftermarket to multi-sensor native OEM platform.**

**Key insight:** Modern vehicles already have the sensors. CivicMesh is just software that makes them work harder—for the community.

**Next steps:**
1. Document OEM camera + radar + OBD integration specs
2. Begin partnership discussions with OEMs (Q1 2027)
3. Finalize sensor fusion API (Q2 2027)
4. Deploy pilot (Q3 2027)
