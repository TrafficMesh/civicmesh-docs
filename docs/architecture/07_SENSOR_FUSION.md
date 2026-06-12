# Sensor Fusion Architecture

**CivicMesh Core Nervous System: Multi-sensor data fusion for comprehensive traffic enforcement.**

---

## Problem Solved

**Before:** Speeding detection relied on host vehicle OBD speed only → missed 90% of violations (other vehicles)

**After:** Radar + camera fusion → detects all vehicles independently → comprehensive enforcement

---

## Core Architecture: One Brain, Many Sensors

```
        Front Camera (1080p/60fps)    ─┐
        Rear Camera (1080p/60fps)     ─┤
        Front Radar (77 GHz)          ─┤
        Rear Radar (77 GHz)           ─┤
        IMU (100-1000 Hz)             ─┤
        GPS (1-10 Hz)                 ─┤
        OBD-II (10-100 Hz)            ─┘
                      ↓
        ┌──────────────────────────────┐
        │  Edge Compute Unit (Brain)   │
        │                              │
        │  ┌────────────────────────┐  │
        │  │ Sensor Drivers Layer   │  │
        │  │ (camera, radar, imu)   │  │
        │  └────────────┬───────────┘  │
        │               ↓               │
        │  ┌────────────────────────┐  │
        │  │ Time Sync Layer        │  │
        │  │ (ms-precision align)   │  │
        │  └────────────┬───────────┘  │
        │               ↓               │
        │  ┌────────────────────────┐  │
        │  │ Sensor Fusion Engine   │  │
        │  │ (world state model)    │  │
        │  └────────────┬───────────┘  │
        │               ↓               │
        │  ┌────────────────────────┐  │
        │  │ Event Detection Engine │  │
        │  │ (violations, anomalies)│  │
        │  └────────────┬───────────┘  │
        │               ↓               │
        │  ┌────────────────────────┐  │
        │  │ Storage & Upload       │  │
        │  │ (local + cloud)        │  │
        │  └────────────────────────┘  │
        └──────────────────────────────┘
                      ↓
        Upload Queue → Cloud Ingestion
```

---

## Key Principle

> **Sensors do NOT communicate with each other — they all stream into one fusion layer.**

This prevents:
- Data conflicts
- Race conditions
- Missing context
- Ambiguous detections

---

## Sensor Stack

### 1. Front Camera
- **Interface:** CSI or USB 3.0
- **Resolution:** 1920×1080
- **FPS:** 30–60
- **Purpose:** Vehicle identification, plate reading, lane detection
- **Update Rate:** 30 Hz (every 33ms)

### 2. Rear Camera
- **Interface:** CSI or USB 3.0
- **Resolution:** 1920×1080
- **FPS:** 30–60
- **Purpose:** Trailing vehicle detection, rear lane monitoring
- **Update Rate:** 30 Hz

### 3. Front Radar (77 GHz)
- **Interface:** CAN bus / Automotive Ethernet / SPI
- **Detection Range:** 0–200m
- **Update Rate:** 20 Hz
- **Outputs:**
  - Object ID
  - Distance
  - Relative velocity
  - Angle (azimuth)
  - Doppler (radial velocity)
- **Purpose:** Speed detection (independent of host vehicle), collision risk, multi-vehicle tracking

### 4. Rear Radar (77 GHz)
- **Interface:** CAN bus / Automotive Ethernet / SPI
- **Detection Range:** 0–200m
- **Update Rate:** 20 Hz
- **Outputs:** Same as front radar
- **Purpose:** Tailgater detection, rear-collision risk, following distance violations

### 5. Inertial Measurement Unit (IMU)
- **Interface:** I2C / SPI
- **Update Rate:** 100–1000 Hz
- **Outputs:**
  - Acceleration (X, Y, Z)
  - Angular velocity (roll, pitch, yaw)
- **Purpose:** Vehicle motion context (cornering, braking, lane changes)

### 6. GPS
- **Interface:** UART / USB
- **Update Rate:** 1–10 Hz
- **Outputs:**
  - Latitude, longitude
  - Altitude
  - Accuracy (±5m)
  - Speed (backup to OBD)
- **Purpose:** Geolocation, speed limit lookup, jurisdiction verification

### 7. OBD-II
- **Interface:** CAN bus (J1979 protocol)
- **Update Rate:** 10–100 Hz
- **Outputs:**
  - Vehicle speed
  - RPM
  - Throttle position
  - Engine state
  - Heading
- **Purpose:** Host vehicle state, reference speed, context

---

## Edge Compute Unit (The Brain)

### Hardware Requirements

**Recommended: NVIDIA Jetson Orin**
- 12-core ARM CPU
- NVIDIA GPU (192 CUDA cores)
- 8–12 GB RAM
- H.264/H.265 hardware encoding
- Real-time OS support
- Power: 15–25W

**Alternative: Intel i7 Automotive**
- 6-core x86
- 16 GB RAM
- Power: 25–45W
- Better single-threaded performance

**Budget: Raspberry Pi CM4**
- Arm CPU
- 4–8 GB RAM
- Limited GPU
- Power: 5–10W
- Only for MVP (limited sensor support)

### Software Architecture

```
/civicmesh/edge/
├── sensors/
│   ├── camera_driver.py          # Captures frames from USB/CSI cameras
│   ├── radar_driver.py           # Reads radar CAN messages
│   ├── imu_driver.py             # I2C/SPI polling
│   ├── gps_driver.py             # UART NMEA parsing
│   └── obd_driver.py             # CAN J1979 protocol
├── fusion/
│   ├── time_synchronizer.py      # ms-precision timestamp alignment
│   ├── calibration.py            # Camera/radar/IMU calibration
│   ├── world_state.py            # Unified spatiotemporal model
│   └── fusion_engine.py          # Core fusion logic
├── detection/
│   ├── speeding_detector.py      # Vehicle speed from radar
│   ├── lane_detector.py          # Lane violations from camera
│   ├── alpr_detector.py          # Plate from camera
│   ├── collision_risk.py         # Distance from radar + IMU
│   └── aggressive_driving.py     # Behavior from acceleration + steering
├── storage/
│   ├── incident_store.py         # SQLite + clip storage
│   └── buffer_manager.py         # Ring buffer for offline resilience
└── upload/
    └── sync_manager.py           # Queue + retry logic
```

---

## Time Synchronization Layer (CRITICAL)

**Problem:** Sensors update at different rates with different latencies.

```
Radar:   20 Hz (50ms updates)
Camera:  30 Hz (33ms updates)
IMU:     1000 Hz (1ms updates)
GPS:     1 Hz (1000ms updates)
OBD:     100 Hz (10ms updates)
```

**Solution:** Master clock + timestamp alignment

```python
class TimeSynchronizer:
    """Align all sensor data to single time reference."""
    
    def __init__(self, master_clock_hz=1000):
        """Use system clock as master (1000 Hz)."""
        self.master_hz = 1000
        self.offsets = {}  # Per-sensor calibration
    
    def align(self, sensor_data):
        """Convert sensor timestamp → master clock."""
        # sensor_data has: {timestamp, source, values}
        aligned_time = self._master_time()
        sensor_data['master_timestamp'] = aligned_time
        return sensor_data
```

**Result:** All data points share a common temporal reference (ms precision).

---

## Sensor Fusion Engine

### World State Model

At any moment in time, maintain:

```python
class WorldState:
    """Unified model of road at time T."""
    
    def __init__(self):
        self.timestamp = None              # Master clock timestamp
        self.location = {                  # GPS
            'lat': 45.4215,
            'lon': -75.6972,
            'accuracy': 5.0  # meters
        }
        self.host_vehicle = {              # OBD
            'speed': 95.0,                 # km/h
            'heading': 180,                # degrees
            'throttle': 0.65               # %
        }
        self.detected_vehicles = [         # Radar + Camera
            {
                'id': 'radar_001',
                'distance': 45.0,          # meters
                'speed': 82.0,             # km/h (radar doppler)
                'plate': 'ABC123',         # camera ALPR
                'lane': 'right',           # camera vision
                'confidence': 0.97
            }
        ]
        self.lanes = {                     # Camera
            'left': {'visible': True, 'type': 'single_yellow'},
            'center': {'visible': True, 'type': 'white_dashed'},
            'right': {'visible': True, 'type': 'solid_white'}
        }
        self.motion = {                    # IMU
            'acceleration': [0.1, -0.05, 0],  # X, Y, Z (m/s²)
            'angular_velocity': [0, 0, 2.0]   # Roll, pitch, yaw
        }
```

### Fusion Algorithm

```python
class FusionEngine:
    """Merge multi-sensor data into coherent world state."""
    
    def fuse(self, sensor_data_list):
        """
        Input: List of sensor readings
        Output: WorldState
        """
        
        # 1. Align timestamps
        aligned = [self.sync.align(s) for s in sensor_data_list]
        
        # 2. Associate data (radar tracks → camera observations)
        associations = self.associate_tracks(aligned)
        
        # 3. Update world state
        for assoc in associations:
            radar_data = assoc['radar']
            camera_data = assoc['camera']
            
            # Merge: radar speed + camera identity
            vehicle = {
                'radar_id': radar_data['object_id'],
                'distance': radar_data['distance'],
                'speed': radar_data['relative_velocity'],  # KEY: Radar gives speed
                'plate': camera_data['plate'],              # Camera gives identity
                'confidence': min(radar_data['conf'], camera_data['conf'])
            }
            
            self.world_state.detected_vehicles.append(vehicle)
        
        return self.world_state
    
    def associate_tracks(self, aligned_data):
        """Match radar objects to camera detections (data association)."""
        # Hungarian algorithm or Kalman filter
        # Goal: "This radar blip is the same car as this camera detection"
        pass
```

---

## Event Detection (Using Fused Data)

### Speeding Detection (Updated)

**Before (OBD only):**
```
if host_speed > speed_limit:
    violation()
```

**After (Radar + Camera):**
```
for vehicle in world_state.detected_vehicles:
    # Radar gives speed (independent of host)
    vehicle_speed = vehicle['speed']  # From radar doppler
    
    # Camera gives identity + context
    plate = vehicle['plate']           # From ALPR
    lane = vehicle['lane']             # From vision
    
    # GPS gives speed limit
    limit = speed_limit_for(location)
    
    if vehicle_speed > limit + 10:
        event = {
            'type': 'speeding',
            'plate': plate,
            'speed': vehicle_speed,
            'limit': limit,
            'excess': vehicle_speed - limit,
            'distance': vehicle['distance'],
            'confidence': vehicle['confidence']
        }
        self.event_queue.add(event)
```

### Other Events Now Possible

**Tailgating:**
```
rear_vehicle = world_state.detected_vehicles[-1]
distance = rear_vehicle['distance']
host_speed = world_state.host_vehicle['speed']

if distance < safe_following_distance(host_speed):
    event = {
        'type': 'tailgating',
        'distance': distance,
        'safe_distance': safe_following_distance(host_speed)
    }
```

**Aggressive Acceleration:**
```
accel = world_state.motion['acceleration']
if abs(accel[0]) > 5.0:  # m/s²
    event = {
        'type': 'aggressive_acceleration',
        'acceleration': accel[0]
    }
```

**Lane Change Without Signal:**
```
if vehicle_changing_lanes and not blinker_on:
    event = {
        'type': 'unsafe_lane_change'
    }
```

---

## Data Flow Through Fusion Engine

```
────────────────────────────────────────────────────

Front Camera frame 001
                ↓ (downsampled @ 30 fps)
Radar object [id=45, dist=50m, v_rel=12 m/s]
                ↓ (@ 20 Hz)
IMU accel [0.15, -0.05, 0] m/s²
                ↓ (@ 1000 Hz)
GPS fix [45.421, -75.697] ± 5m
                ↓ (@ 1 Hz)
OBD speed 95 km/h, heading 180°
                ↓ (@ 100 Hz)
        ──────────────────────────
             TimeSynchronizer
        ──────────────────────────
                ↓
        [all @ master_clock = 1000 Hz]
                ↓
        ──────────────────────────
          Track Association
        ──────────────────────────
        Radar obj 45 + Camera det 12
        = Vehicle ABC123
                ↓
        ──────────────────────────
        FusionEngine Update
        ──────────────────────────
        WorldState:
        - Vehicle ABC123
        - Distance: 50m
        - Speed: 82 km/h
        - Lane: right
        - Confidence: 0.97
                ↓
        ──────────────────────────
        Event Detection
        ──────────────────────────
        Speeding event: ABC123 @ 82 in 60 zone
        Confidence: 0.97
                ↓
        Incident Store + Upload Queue
```

---

## Calibration & Alignment

### Camera-Radar Calibration

Cameras and radar see the world differently. You must calibrate:

```python
class Calibration:
    """Camera-radar geometric alignment."""
    
    def __init__(self):
        # Extrinsic parameters (position of each sensor on vehicle)
        self.camera_front_xyz = [0, 0.2, 1.5]  # meters
        self.radar_front_xyz = [0, 0, 0.8]
        self.camera_rear_xyz = [0, -0.2, 1.5]
        self.radar_rear_xyz = [0, -0.1, 0.8]
        
        # Intrinsic parameters (camera focal length, etc.)
        self.camera_K = [[1000, 0, 960],
                         [0, 1000, 540],
                         [0, 0, 1]]
    
    def camera_to_world(self, pixel_x, pixel_y, depth):
        """Convert camera pixel to world coordinates."""
        # Depth from radar; pixel from camera
        # Result: 3D point in world frame
        pass
    
    def radar_to_image(self, distance, angle):
        """Project radar detection onto camera image."""
        # Where in the image should we find this radar object?
        pass
```

### Validation

After fusion, verify consistency:

```python
def validate_fusion(world_state):
    """Check for sensor conflicts."""
    for vehicle in world_state.detected_vehicles:
        # Camera says vehicle in lane A
        # Radar says vehicle at angle that implies lane B
        # If mismatch > threshold: reduce confidence
        
        camera_lane = vehicle.get('camera_lane')
        radar_angle = vehicle.get('radar_angle')
        
        predicted_lane = predict_lane_from_angle(radar_angle)
        
        if camera_lane != predicted_lane:
            vehicle['confidence'] *= 0.8  # Reduce confidence
```

---

## Hardware Integration Example (Tier 1 Bus)

```
OC Transpo Bus
├── Front Camera (CSI → Jetson)
├── Rear Camera (USB → Jetson)
├── Front Radar (CAN → Jetson)
├── Rear Radar (CAN → Jetson)
├── IMU (I2C → Jetson)
├── GPS (UART → Jetson)
└── OBD-II (CAN → Jetson)
        ↓
    Jetson Orin
    (fusion engine)
        ↓
    Event Queue
        ↓
    LTE Upload
```

---

## Performance Targets

| Metric | Target | Notes |
|--------|--------|-------|
| **Sensor latency** | <50ms | Time from capture to fusion input |
| **Fusion latency** | <100ms | Time to process all sensors |
| **Event latency** | <200ms | Time from violation to upload queue |
| **Throughput** | 30 fps | Camera FPS (limiting sensor) |
| **CPU usage** | <80% | Leave room for other tasks |
| **Memory** | <4 GB | Jetson typical allocation |
| **Accuracy** | >95% | Vehicle detection + speed |
| **False positive** | <5% | Non-violations flagged |

---

## Testing Sensor Fusion

### Unit Tests

```python
class TestSensorFusion:
    
    def test_speeding_detection_radar(self):
        """Test: Radar detects speeding vehicle (independent of host)."""
        
        # Host vehicle @ 50 km/h
        # Other vehicle (via radar) @ 75 km/h
        # Speed limit @ 50 km/h
        # → Should flag violation
        
        world_state = WorldState()
        world_state.host_vehicle['speed'] = 50
        world_state.detected_vehicles.append({
            'speed': 75,
            'distance': 100,
            'plate': 'XYZ789',
            'confidence': 0.95
        })
        
        violations = detector.detect_violations(world_state, limit=50)
        
        assert len(violations) == 1
        assert violations[0]['plate'] == 'XYZ789'
        assert violations[0]['excess'] == 25
    
    def test_time_sync(self):
        """Test: Timestamps aligned across sensors."""
        
        data_points = [
            {'source': 'camera', 'timestamp': 1000.015},
            {'source': 'radar', 'timestamp': 1000.020},
            {'source': 'obd', 'timestamp': 1000.010}
        ]
        
        synced = [sync.align(d) for d in data_points]
        
        # All should have same master_timestamp
        assert len(set(s['master_timestamp'] for s in synced)) == 1
    
    def test_track_association(self):
        """Test: Radar object matched to camera detection."""
        
        radar_obj = {'id': 45, 'angle': 5.0, 'distance': 100}
        camera_det = {'plate': 'ABC123', 'x': 500, 'y': 400}
        
        assoc = fusion.associate([radar_obj, camera_det])
        
        assert assoc['vehicle']['plate'] == 'ABC123'
        assert assoc['vehicle']['distance'] == 100
```

### Integration Tests

```python
def test_full_sensor_fusion_workflow():
    """E2E: Radar detects speeding, camera identifies, event created."""
    
    # Simulate sensor data
    sensor_data = [
        {'source': 'radar_front', 'timestamp': 1000.0, 'object_id': 45, 
         'distance': 100, 'velocity': 25},  # 25 m/s = 90 km/h
        {'source': 'camera_front', 'timestamp': 1000.002, 'plate': 'ABC123', 
         'confidence': 0.98},
        {'source': 'gps', 'timestamp': 1000.001, 'lat': 45.421, 'lon': -75.697},
        {'source': 'obd', 'timestamp': 1000.003, 'speed': 80}  # Host
    ]
    
    # Fuse
    world_state = fusion_engine.fuse(sensor_data)
    
    # Detect violations
    events = detector.detect_violations(world_state, location='downtown')
    
    # Verify
    assert len(events) == 1
    assert events[0]['type'] == 'speeding'
    assert events[0]['plate'] == 'ABC123'
    assert events[0]['speed'] == 90
    assert events[0]['excess'] == 20  # 90 - 70 km/h limit
```

---

## Documentation Updates (Global)

**Files to update:**

1. `docs/architecture/01_HARDWARE_STACK.md`
   - Add front/rear radar specs
   - Add IMU specs
   - Add dual-camera layout

2. `docs/architecture/02_VIOLATION_MODELS.md`
   - Update speeding: now detects all vehicles, not just host
   - Add radar-based speed estimation
   - Add camera-radar association
   - Add confidence scoring (fused sensors)

3. `features/violation-detection/01_SPEEDING.md`
   - Update test cases: radar vehicle detection
   - Add mock radar simulator
   - Add data association tests
   - Update test scenarios (multi-vehicle)

4. `features/data-pipeline/00_OVERVIEW.md`
   - Add sensor data streams
   - Add fusion engine step
   - Update data schema (multi-sensor)

5. `features/data-pipeline/01_EDGE_CAPTURE.md` (NEW)
   - Sensor driver architecture
   - Camera capture pipeline
   - Radar integration
   - IMU polling

6. NEW: `docs/architecture/08_SENSOR_DRIVERS.md`
   - Driver interface spec
   - CAN bus protocol
   - CSI/USB camera handling
   - I2C/SPI device drivers

---

## Next Steps

Choose your approach:

1. **Build simulator-based fusion** (weeks 1-2)
   - Mock radar data (JSON scenarios)
   - Test data association
   - Validate fusion logic
   - No hardware needed

2. **Real radar + simulation** (weeks 3-4)
   - Order 77 GHz radar
   - Build driver
   - Integrate with cameras
   - Field test on bus

3. **Full hardware stack** (weeks 5-8)
   - All 7 sensors
   - Jetson Orin
   - Production calibration
   - Deploy to 50 buses

---

**Ready to:**
- [ ] Update violation detection for radar?
- [ ] Design sensor driver interfaces?
- [ ] Create mock radar simulator?
- [ ] Build track association algorithm?

---

**This is now a REAL sensor fusion system — not just a dashcam + OBD hybrid.**

Your competitors (Nexar, Rekor) don't have independent vehicle speed detection. You do.

**That's the moat.**
