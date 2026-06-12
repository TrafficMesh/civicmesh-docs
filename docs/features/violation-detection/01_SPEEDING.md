# Feature: Speeding Detection (Radar-Enhanced)

**Core violation type for MVP. Detects speeding of ANY vehicle via radar + camera fusion.**

---

## Feature Overview

Speeding detection now operates in TWO modes:

### Mode 1: Host Vehicle Speed (OBD-based)
- Compares own vehicle speed vs. posted limit
- Confidence: 99% (ground truth)
- Use case: Bylaw/police vehicle enforcement

### Mode 2: Other Vehicle Speed (Radar-based) ⭐ NEW
- Detects speed of ANY vehicle via radar doppler
- Independent of host vehicle motion
- Confidence: 95% (radar + ALPR fusion)
- Use case: Comprehensive traffic enforcement (detect all violators)

**Why Radar Speeding is Transformative:**
- OBD-only misses 90% of violations (doesn't measure other vehicles)
- Radar doppler = independent speed measurement (no host vehicle dependency)
- Camera ALPR = vehicle identification
- Sensor fusion = confidence scoring
- Now detects ALL speeding, not just host vehicle

---

## Subfeatures

### OBD-Based (Host Vehicle)

| Subfeature | Description | Priority |
|-----------|-------------|----------|
| **Speed Polling** | Read OBD speed at 10 Hz | CRITICAL |
| **Geofence Matching** | Find current speed limit based on GPS location | CRITICAL |
| **Speed Comparison** | Check if speed > limit + 10 km/h buffer | CRITICAL |
| **OBD Confidence** | Always 99% (ground truth) | MEDIUM |

### Radar-Based (Any Vehicle) ⭐ NEW

| Subfeature | Description | Priority |
|-----------|-------------|----------|
| **Radar Polling** | Read radar detections at 20 Hz | CRITICAL |
| **Doppler Velocity** | Extract relative velocity from radar | CRITICAL |
| **Camera Fusion** | Match radar object to camera detection | CRITICAL |
| **Plate Identification** | ALPR → vehicle identity | HIGH |
| **Speed Confidence** | Fuse radar + ALPR + geofence confidence | HIGH |
| **Track Association** | Link radar objects across frames (Kalman?) | MEDIUM |

### Shared (Both Modes)

| Subfeature | Description | Priority |
|-----------|-------------|----------|
| **Incident Packaging** | Extract 5-sec clip with context | HIGH |
| **Metadata Assembly** | Collect timestamp, coords, speed, limit | HIGH |
| **Edge Buffering** | Store locally if upload fails | MEDIUM |
| **Upload Queue** | Send to cloud API | HIGH |

---

## Requirements

### Software
```
Python 3.8+
├── opencv-python         # Camera frame processing
├── numpy                 # Multi-sensor data processing
├── scipy                 # Kalman filter (track association)
├── requests              # HTTP uploads
├── python-can            # CAN bus interface (radar, OBD)
├── paho-mqtt             # MQTT for sensor streaming
└── pytest                # Testing framework

Hardware Interface Libraries:
├── libcamera / gstreamer  # Camera drivers
├── radar-driver           # Manufacturer-specific radar library
├── smbus-cffi             # I2C (IMU)
└── pyserial               # GPS UART
```

### Data / Test Assets

#### Test Videos
- `test_data/videos/normal_traffic.mp4` (1 min)
  - Vehicle at constant 45 km/h in 50 zone
  - No violations expected
  
- `test_data/videos/gradual_speedup.mp4` (1 min)
  - Vehicle accelerates: 40 → 60 km/h in 50 zone
  - Violation expected at ~55 km/h mark

- `test_data/videos/speeding_burst.mp4` (30 sec)
  - Vehicle briefly exceeds 65 km/h in 50 zone
  - Short violation burst

- `test_data/videos/multiple_violations.mp4` (2 min)
  - Multiple speeding instances
  - Different speeds and durations

#### Test OBD Data (JSON)
```json
// test_data/obd/normal_traffic.json
{
  "description": "Steady speed, no violations",
  "speed_limit": 50,
  "events": [
    {"time": 0.0, "speed": 45, "heading": 180},
    {"time": 5.0, "speed": 45, "heading": 180},
    {"time": 10.0, "speed": 45, "heading": 180}
  ]
}

// test_data/obd/gradual_speedup.json
{
  "description": "Gradual acceleration into violation",
  "speed_limit": 50,
  "events": [
    {"time": 0.0, "speed": 40},
    {"time": 2.0, "speed": 50},
    {"time": 4.0, "speed": 55},  // VIOLATION STARTS
    {"time": 6.0, "speed": 60},  // PEAK
    {"time": 8.0, "speed": 55},  // Still violating
    {"time": 10.0, "speed": 45}  // VIOLATION ENDS
  ]
}

// test_data/obd/speeding_burst.json
{
  "description": "Brief speeding burst",
  "speed_limit": 50,
  "events": [
    {"time": 0.0, "speed": 45},
    {"time": 2.0, "speed": 65},  // VIOLATION
    {"time": 4.0, "speed": 45}
  ]
}
```

#### Expected Results (Ground Truth)
```json
// test_data/results/gradual_speedup_expected.json
{
  "violations": [
    {
      "violation_type": "speeding",
      "start_time": 3.8,
      "end_time": 8.2,
      "max_speed": 60,
      "speed_limit": 50,
      "confidence": 0.99,
      "excess_speed": 10,
      "frames_affected": 152  // 4.4 sec @ 30 fps
    }
  ]
}
```

#### GPS Test Data
```json
// test_data/gps/ottawa_intersection.json
{
  "location_name": "King St @ Bay St, Ottawa",
  "latitude": 45.4215,
  "longitude": -75.6972,
  "geohash": "f24wxyz",
  "speed_limit": 50
}
```

#### Geofence Configuration
```json
// test_data/geofences/ottawa_speed_zones.json
{
  "zones": [
    {
      "id": "f24wxyz",
      "name": "King St @ Bay St",
      "speed_limit": 50,
      "polygon": [[45.42, -75.69], [45.42, -75.71], [45.43, -75.71], [45.43, -75.69]]
    },
    {
      "id": "school_zone_001",
      "name": "Parkdale Public School",
      "speed_limit": 40,
      "hours": "7:00-9:00,15:00-16:00",
      "polygon": [...]
    }
  ]
}
```

### Hardware (For Real Deployment)

**Recommended: NVIDIA Jetson Orin** (Handles sensor fusion)
- 12-core ARM CPU
- NVIDIA GPU (192 CUDA cores)
- 8–12 GB RAM
- CAN bus (2 channels for radar + OBD)
- CSI camera interfaces
- 15–25W power consumption

**Minimum for Tier 1 (OC Transpo):**
- Front Camera (1920×1080, 30–60 fps)
- Rear Camera (1920×1080, 30–60 fps)
- Front Radar (77 GHz, 0–200m range)
- OBD-II dongle (CAN interface)
- GPS module (UART)
- IMU (I2C, 100+ Hz)

**For MVP Testing:** 
- Use simulated radar data (JSON)
- Use camera simulator (mock ALPR)
- Use OBD simulator (no hardware needed)

---

## Testing Methodology

### Unit Tests
**Goal:** Test individual components in isolation

1. **Speed Calculation**
   - Test: Compare OBD speed vs. limit
   - Input: speed=65, limit=50
   - Expected: violation=True, excess=15

2. **Geofence Lookup**
   - Test: Find speed limit for GPS coordinate
   - Input: lat=45.4215, lon=-75.6972
   - Expected: speed_limit=50, geohash='f24wxyz'

3. **Violation Detection**
   - Test: Detect when threshold crossed
   - Input: speeds [45, 50, 55, 65, 60, 45]
   - Expected: violation_start=frame_2, violation_end=frame_5

4. **Metadata Assembly**
   - Test: Correct JSON structure
   - Expected: Has timestamp, speed, limit, excess, confidence

### Integration Tests
**Goal:** Test end-to-end flow with realistic data

1. **Full Workflow: OBD → Detection → Incident**
   - Load: OBD scenario + corresponding video
   - Run: Speed detection pipeline
   - Assert: Detects violations at correct times
   - Verify: Metadata is complete and accurate

2. **Multiple Violations**
   - Load: Video with 3 separate speeding instances
   - Run: Detection pipeline
   - Assert: Detects all 3 violations
   - Verify: Each has distinct incident record

3. **Edge Cases**
   - No OBD data (fallback to GPS-only speed estimate)
   - No GPS (use last known location)
   - Speed limit zone boundary (entering/exiting)
   - GPS jitter (small coordinate jumps)

### Performance Tests
**Goal:** Verify latency and throughput requirements

1. **Polling Latency**
   - Test: Time to read OBD speed
   - Target: <50 ms
   - Measured: Average of 1,000 reads

2. **Geofence Lookup Latency**
   - Test: Time to find speed limit from GPS
   - Target: <100 ms
   - Measured: Average of 1,000 lookups

3. **Detection Latency**
   - Test: Time from frame capture to violation flag
   - Target: <200 ms
   - Measured: Per-frame average

4. **Throughput**
   - Test: Sustained 30 fps
   - Target: All 30 frames processed per second
   - Measured: Frame processing rate over 1 min

### End-to-End Tests
**Goal:** Verify against real test scenarios**

1. **Real Dashcam Video + OBD Data**
   - Load: Actual video from OC Transpo bus
   - Simulate: OBD speeds from scenario files
   - Run: Full detection pipeline
   - Verify: Violations detected correctly

2. **False Positive Measurement**
   - Load: Normal traffic video (no violations)
   - Run: Detection pipeline
   - Measure: False positive rate
   - Target: <5%

3. **False Negative Measurement**
   - Load: Video with known violations (ground truth)
   - Run: Detection pipeline
   - Compare: Detected vs. expected violations
   - Target: <5% missed violations

---

## Test Suite Code

### Test Setup
```python
# features/violation-detection/test_speeding.py

import pytest
import json
import numpy as np
from pathlib import Path

from src.speed_detector import SpeedDetector
from src.geofence_manager import GeofenceManager
from src.obd_simulator import OBDSimulator
from src.camera_simulator import CameraSimulator

class TestSpeedingDetection:
    """Unit and integration tests for speeding detection."""
    
    @pytest.fixture
    def speed_detector(self):
        """Initialize speed detector."""
        return SpeedDetector()
    
    @pytest.fixture
    def geofence_manager(self):
        """Load geofence configuration."""
        return GeofenceManager("test_data/geofences/ottawa_speed_zones.json")
    
    @pytest.fixture
    def obd_simulator(self):
        """Initialize OBD simulator."""
        return OBDSimulator()
    
    @pytest.fixture
    def test_data_dir(self):
        """Path to test data."""
        return Path("features/test-data")
```

### Unit Tests
```python
    def test_speed_calculation_simple(self, speed_detector):
        """Test: Simple speeding detection (65 in 50 zone)."""
        result = speed_detector.check_violation(
            obd_speed=65.0,
            speed_limit=50,
            location_id="f24wxyz"
        )
        
        assert result['violation'] == True
        assert result['speed'] == 65.0
        assert result['limit'] == 50
        assert result['excess'] == 15
        assert result['confidence'] == 0.99
    
    def test_speed_calculation_no_violation(self, speed_detector):
        """Test: No violation when speed within threshold."""
        result = speed_detector.check_violation(
            obd_speed=55.0,
            speed_limit=50,
            location_id="f24wxyz"
        )
        
        assert result['violation'] == False
        assert result['excess'] == 0
    
    def test_speed_buffer_10kmh(self, speed_detector):
        """Test: 10 km/h buffer before violation triggered."""
        # Exactly at limit
        result1 = speed_detector.check_violation(50.0, 50, "test")
        assert result1['violation'] == False
        
        # 5 km/h over (within buffer)
        result2 = speed_detector.check_violation(55.0, 50, "test")
        assert result2['violation'] == False
        
        # 10 km/h over (at threshold)
        result3 = speed_detector.check_violation(60.0, 50, "test")
        assert result3['violation'] == True
        
        # 11 km/h over (exceeds threshold)
        result4 = speed_detector.check_violation(61.0, 50, "test")
        assert result4['violation'] == True
    
    def test_geofence_lookup(self, geofence_manager):
        """Test: Find speed limit from GPS coordinate."""
        limit = geofence_manager.get_speed_limit(
            latitude=45.4215,
            longitude=-75.6972
        )
        
        assert limit == 50
    
    def test_geofence_boundary(self, geofence_manager):
        """Test: Correct limit at zone boundary."""
        # Just inside zone
        limit1 = geofence_manager.get_speed_limit(45.4215, -75.6972)
        assert limit1 == 50
        
        # Outside zone (default limit)
        limit2 = geofence_manager.get_speed_limit(45.50, -75.50)
        assert limit2 == 60  # Default
    
    def test_metadata_structure(self, speed_detector):
        """Test: Metadata JSON has all required fields."""
        metadata = speed_detector.build_metadata(
            obd_speed=65.0,
            speed_limit=50,
            timestamp=1000.5,
            latitude=45.4215,
            longitude=-75.6972,
            frame_num=30
        )
        
        required_fields = [
            'timestamp', 'speed', 'limit', 'excess', 'confidence',
            'latitude', 'longitude', 'frame_num'
        ]
        
        for field in required_fields:
            assert field in metadata
            assert metadata[field] is not None
```

### Integration Tests
```python
    def test_full_workflow_gradual_speedup(self, speed_detector, geofence_manager):
        """Integration: Detect speeding violation from OBD scenario."""
        
        # Load test scenario
        with open("test_data/obd/gradual_speedup.json") as f:
            scenario = json.load(f)
        
        # Load expected results
        with open("test_data/results/gradual_speedup_expected.json") as f:
            expected = json.load(f)
        
        # Simulate OBD readings
        detected_violations = []
        for event in scenario['events']:
            result = speed_detector.check_violation(
                obd_speed=event['speed'],
                speed_limit=scenario['speed_limit'],
                location_id="test"
            )
            
            if result['violation']:
                detected_violations.append({
                    'time': event['time'],
                    'speed': event['speed']
                })
        
        # Verify violations detected at correct times
        assert len(detected_violations) > 0, "Expected violations not detected"
        
        # Verify first violation starts around expected time
        first_violation_time = detected_violations[0]['time']
        expected_start = expected['violations'][0]['start_time']
        
        assert abs(first_violation_time - expected_start) < 1.0, \
            f"Violation start time mismatch: {first_violation_time} vs {expected_start}"
    
    def test_multiple_violations(self, speed_detector):
        """Integration: Detect multiple separate violations."""
        
        speeds = [
            45, 45, 65, 60, 45,  # Violation 1
            45, 45, 50, 50, 45,  # No violation
            45, 70, 75, 45, 45   # Violation 2
        ]
        
        violations = []
        for frame, speed in enumerate(speeds):
            result = speed_detector.check_violation(
                obd_speed=speed,
                speed_limit=50,
                location_id="test"
            )
            
            if result['violation']:
                violations.append({
                    'frame': frame,
                    'speed': speed
                })
        
        # Should detect 2 separate violation clusters
        assert len(violations) >= 2, f"Expected 2+ violations, got {len(violations)}"
    
    def test_false_positive_rate_normal_traffic(self, speed_detector):
        """Integration: Measure false positive rate on normal traffic."""
        
        # Simulate 5 minutes of normal traffic (no violations)
        normal_speeds = [45] * (5 * 60 * 30)  # 5 min at 30 fps, constant 45 km/h
        
        false_positives = 0
        for speed in normal_speeds:
            result = speed_detector.check_violation(
                obd_speed=speed,
                speed_limit=50,
                location_id="test"
            )
            
            if result['violation']:
                false_positives += 1
        
        false_positive_rate = false_positives / len(normal_speeds)
        
        assert false_positive_rate < 0.05, \
            f"False positive rate {false_positive_rate:.2%} exceeds 5% target"
```

### Performance Tests
```python
    def test_geofence_lookup_latency(self, geofence_manager):
        """Performance: Geofence lookup <100ms."""
        import time
        
        times = []
        for _ in range(100):
            start = time.perf_counter()
            geofence_manager.get_speed_limit(45.4215, -75.6972)
            elapsed = time.perf_counter() - start
            times.append(elapsed * 1000)  # Convert to ms
        
        avg_latency = np.mean(times)
        max_latency = np.max(times)
        
        print(f"Geofence lookup: avg={avg_latency:.2f}ms, max={max_latency:.2f}ms")
        
        assert avg_latency < 100, f"Average latency {avg_latency:.2f}ms exceeds 100ms target"
        assert max_latency < 500, f"Max latency {max_latency:.2f}ms exceeds 500ms target"
    
    def test_detection_throughput(self, speed_detector):
        """Performance: Process 30 fps sustained."""
        import time
        
        # Simulate 30 frames
        times = []
        for frame_num in range(30):
            start = time.perf_counter()
            
            result = speed_detector.check_violation(
                obd_speed=55.0,
                speed_limit=50,
                location_id="test"
            )
            
            elapsed = time.perf_counter() - start
            times.append(elapsed * 1000)
        
        avg_latency = np.mean(times)
        
        print(f"Detection latency: avg={avg_latency:.2f}ms per frame")
        
        assert avg_latency < 200, f"Average latency {avg_latency:.2f}ms exceeds 200ms target"
```

### Running Tests
```bash
# All speeding detection tests
cd features/violation-detection
pytest 01_SPEEDING.md -v

# Just unit tests
pytest 01_SPEEDING.md::TestSpeedingDetection::test_speed_calculation -v

# With coverage
pytest 01_SPEEDING.md --cov=src --cov-report=html

# Verbose output
pytest 01_SPEEDING.md -vv -s
```

---

## Acceptance Criteria Checklist

MVP speeding detection must pass ALL of:

- [ ] **Unit Tests**
  - [ ] Speed calculation: correct violation flag
  - [ ] Speed buffer (10 km/h): enforced
  - [ ] Geofence lookup: correct speed limit
  - [ ] Metadata: all fields present and valid

- [ ] **Integration Tests**
  - [ ] End-to-end: OBD → detection → incident
  - [ ] Multiple violations: detects all
  - [ ] Edge cases: handles missing OBD/GPS gracefully

- [ ] **Performance Tests**
  - [ ] Geofence lookup: <100ms
  - [ ] Detection latency: <200ms per frame
  - [ ] Throughput: sustained 30 fps
  - [ ] Memory: <500 MB on Raspberry Pi

- [ ] **Accuracy Tests**
  - [ ] False positive rate: <5% on normal traffic
  - [ ] False negative rate: <5% on known violations
  - [ ] Confidence score: always 99% for valid OBD

- [ ] **Field Tests**
  - [ ] Works on real OC Transpo bus
  - [ ] Survives 24-hour operation
  - [ ] Handles network disconnection
  - [ ] Accurate with real GPS/OBD hardware

---

## Known Issues & Workarounds

| Issue | Cause | Workaround |
|-------|-------|-----------|
| False positives in school zone | Speed limit changes at specific times | Implement time-gated activation (not in MVP) |
| GPS jitter (±5m) | Satellite signal variation | Buffer zone: use 50m geofence radius |
| OBD unavailable | Old vehicle without OBD-II | Fall back to GPS speed estimate (50% confidence) |
| Slow geofence lookup | Large number of zones | Pre-compile zone indices; use spatial hashing |

---

## Success Metrics

By end of Week 2:
- [ ] All unit tests passing
- [ ] <1% false positive rate on test data
- [ ] <200ms latency confirmed
- [ ] Ready for integration with incident builder

By end of Week 4:
- [ ] Full end-to-end workflow tested
- [ ] Field trial on 5 buses
- [ ] <5% false positive rate on real data
- [ ] Ready for Phase 1 expansion

---

## Next Steps

1. **Create test data** (videos, OBD scenarios) — 2 hours
2. **Implement speed detector** (src/speed_detector.py) — 4 hours
3. **Write unit tests** — 4 hours
4. **Write integration tests** — 3 hours
5. **Run tests & debug** — 4 hours
6. **Performance tuning** — 3 hours

**Total: ~20 hours**

Then move on to:
→ `02_LANE_VIOLATIONS.md` (Phase 1)
→ `03_ALPR.md` (MVP)
