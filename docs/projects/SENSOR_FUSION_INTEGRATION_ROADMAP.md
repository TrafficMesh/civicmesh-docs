# Global Documentation Update Roadmap: Sensor Fusion Integration

**Track all documentation changes needed to fully integrate radar-based sensor fusion architecture.**

---

## Overview

**Architectural Shift:**
- Before: OBD-only speeding (misses 90% of violations)
- After: Radar doppler + camera fusion (detects all vehicles independently)

**Impact Scope:** 14 core documents + testing framework

---

## Documentation Update Checklist

### ✓ Core Architecture (COMPLETED)

- [x] **docs/architecture/07_SENSOR_FUSION.md** ← NEW
  - Complete sensor fusion nervous system
  - 7-sensor stack specification
  - Edge compute unit specs (Jetson Orin)
  - Time synchronization layer
  - Fusion engine architecture
  - Track association (Kalman filter)
  - Calibration framework

### 🔄 PHASE 1: Critical Updates (This Week)

#### 1. Hardware Stack
**File:** `docs/architecture/01_HARDWARE_STACK.md`

**Updates needed:**
- [ ] Add Front Radar (77 GHz) specs
  - Interface: CAN bus / Automotive Ethernet
  - Range: 0–200m
  - Update rate: 20 Hz
  - Detection: distance, velocity, angle

- [ ] Add Rear Radar (77 GHz) specs
  - Same as front, for trailing vehicle detection

- [ ] Add IMU (Inertial Measurement Unit) specs
  - Interface: I2C / SPI
  - Update rate: 100–1000 Hz
  - Acceleration + angular velocity

- [ ] Update edge compute requirements
  - Replace Raspberry Pi with Jetson Orin (recommended)
  - Specify CAN bus (2+ channels)
  - GPU acceleration (192+ CUDA cores)
  - 8–12 GB RAM minimum

- [ ] Update power budget
  - Add radar power consumption (0.5–2W per unit)
  - Update thermal management (4 sensors + GPU)

**Estimated hours:** 3

---

#### 2. Violation Models
**File:** `docs/architecture/02_VIOLATION_MODELS.md`

**Updates needed:**
- [ ] **Speeding Detection (update)**
  - Add Mode 2: Radar-based detection
  - Explain radar doppler velocity measurement
  - Update confidence formula (now depends on radar + ALPR)
  - Add track association (radar object → camera detection)
  - Update test scenarios (multi-vehicle)

- [ ] **New detection models (header only)**
  - Tailgating (rear radar + IMU)
  - Aggressive acceleration (IMU)
  - Lane change without signal (camera + OBD)
  - Collision risk (radar + velocity)

- [ ] **Sensor fusion section**
  - World state model
  - Data association algorithm
  - Confidence scoring (multi-sensor)

**Estimated hours:** 4

---

#### 3. Feature Tests
**File:** `features/violation-detection/01_SPEEDING.md`

**Updates needed:**
- [x] **Overview** — Already updated with radar modes

- [ ] **Test Scenarios (ADD)**
  - `test_data/obd/radar_vehicle_speeding.json`
    - Host vehicle @ 50 km/h
    - Radar detects other vehicle @ 75 km/h
    - Should flag violation on OTHER vehicle

  - `test_data/radar/multi_vehicle.json`
    - Multiple vehicles at different speeds
    - Deduplication across radar objects

- [ ] **Unit Tests (ADD)**
  - Radar velocity extraction
  - Camera-radar association (Hungarian algorithm)
  - Confidence fusion formula
  - Doppler shift validation

- [ ] **Integration Tests (UPDATE)**
  - Radar object → camera detection matching
  - Multi-vehicle detection
  - False positive on shadows/reflections

- [ ] **Performance Tests (ADD)**
  - Track association latency (<100ms)
  - Fusion latency (<50ms)
  - Memory usage (multi-sensor buffering)

**Estimated hours:** 6

---

### 📋 PHASE 2: Data & Pipeline (Weeks 2-3)

#### 4. Data Pipeline
**File:** `docs/architecture/04_DATA_FLOW.md`

**Updates needed:**
- [ ] **Add sensor streams section**
  - Camera stream → frame buffer
  - Radar stream → detection buffer
  - IMU stream → motion buffer
  - GPS stream → location buffer
  - OBD stream → telemetry buffer

- [ ] **Add time synchronization step**
  - Timestamp alignment (all → master clock)
  - Latency compensation
  - Dropout handling (missing sensor)

- [ ] **Add fusion engine step** (NEW)
  - World state construction
  - Track association
  - Confidence calculation

- [ ] **Update incident packaging**
  - Now includes radar data
  - Multi-sensor metadata
  - Fusion confidence score

**Estimated hours:** 4

---

#### 5. Edge Capture & Sensors (NEW)
**File:** `features/data-pipeline/01_EDGE_CAPTURE.md` ← CREATE

**Content:**
- [ ] Sensor driver architecture
- [ ] Camera driver specs (CSI, USB 3.0)
- [ ] Radar driver specs (CAN, SPI)
- [ ] IMU driver specs (I2C)
- [ ] GPS driver specs (UART, binary protocol)
- [ ] OBD driver specs (CAN J1979)
- [ ] Frame capture pipeline
- [ ] Radar object parsing
- [ ] Buffering strategy (per-sensor)
- [ ] Error handling (sensor dropout)

**Estimated hours:** 5

---

#### 6. Sensor Drivers Architecture (NEW)
**File:** `docs/architecture/08_SENSOR_DRIVERS.md` ← CREATE

**Content:**
- [ ] Driver interface spec
- [ ] CAN bus protocol (radar, OBD)
- [ ] CSI/USB camera handling
- [ ] I2C/SPI device drivers
- [ ] UART GPS parser
- [ ] Error recovery
- [ ] Driver testing framework

**Estimated hours:** 4

---

### 🔧 PHASE 3: Integration & Testing (Weeks 3-4)

#### 7. Test Infrastructure
**File:** `features/TEST_GUIDE.md`

**Updates needed:**
- [ ] Add sensor fusion tests section
- [ ] Mock radar data generator
- [ ] Mock multi-sensor scenarios
- [ ] Track association test suite
- [ ] Fusion validation tests

**Estimated hours:** 3

---

#### 8. Feature Index
**File:** `features/FEATURE_INDEX.md`

**Updates needed:**
- [ ] Update speeding detection status (OBD + Radar modes)
- [ ] Add sensor fusion feature to index
- [ ] Update architecture links
- [ ] Add new test documents

**Estimated hours:** 1

---

### 📚 PHASE 4: Advanced Features (Weeks 5+)

#### 9. Digital Twin (UPDATE)
**File:** `docs/architecture/03_DIGITAL_TWIN.md`

**Updates needed:**
- [ ] Add multi-sensor data sources
- [ ] Radar data → twin fusion
- [ ] Confidence updates from multiple sensors
- [ ] Track persistence (correlate across time)

**Estimated hours:** 2

---

#### 10. API Documentation (UPDATE)
**File:** `features/api/01_INGESTION_API.md`

**Updates needed:**
- [ ] Add multi-sensor incident schema
- [ ] Radar data fields
- [ ] Fusion confidence fields
- [ ] Track association IDs

**Estimated hours:** 2

---

#### 11. Operations & Monitoring (UPDATE)
**File:** `features/operations/01_FLEET_HEALTH.md`

**Updates needed:**
- [ ] Radar health checks
- [ ] Time sync monitoring
- [ ] Fusion engine latency
- [ ] Track association success rate

**Estimated hours:** 2

---

#### 12. Hardware Tiers (UPDATE)
**File:** `features/hardware/01_TIER1_PERMANENT.md`

**Updates needed:**
- [ ] Update Tier 1 (OC Transpo) with full sensor stack
- [ ] Tier 2/3 sensor variations (optional rear radar?)
- [ ] Tier 4 civilian kit (basic setup, maybe no radar?)

**Estimated hours:** 2

---

#### 13. Project Registry (UPDATE)
**File:** `docs/architecture/00_PROJECT_REGISTRY.md`

**Updates needed:**
- [ ] Update architecture section
- [ ] Add sensor fusion layer
- [ ] Update hardware BOM (add radars)
- [ ] Update cost breakdown (+$400–600 for 2x radar)
- [ ] Update feature matrix (speeding now detects all vehicles)

**Estimated hours:** 2

---

#### 14. Main Architecture Guide (UPDATE)
**File:** `CLAUDE.md`

**Updates needed:**
- [ ] Update tech stack (add sensor fusion libraries)
- [ ] Update hardware requirements
- [ ] Add sensor driver modules to architecture
- [ ] Update data pipeline description

**Estimated hours:** 2

---

## Summary of Changes

### New Documents Created
1. `docs/architecture/07_SENSOR_FUSION.md` ✓ (750 lines)
2. `docs/architecture/08_SENSOR_DRIVERS.md` (TO CREATE)
3. `features/data-pipeline/01_EDGE_CAPTURE.md` (TO CREATE)

### Documents Updated
- `docs/architecture/01_HARDWARE_STACK.md` (add radar + IMU)
- `docs/architecture/02_VIOLATION_MODELS.md` (radar-based speeding)
- `docs/architecture/00_PROJECT_REGISTRY.md` (sensor fusion section)
- `docs/architecture/04_DATA_FLOW.md` (multi-sensor streams)
- `CLAUDE.md` (tech stack, hardware)
- `features/violation-detection/01_SPEEDING.md` ✓ (radar modes)
- `features/TEST_GUIDE.md` (sensor fusion tests)
- `features/FEATURE_INDEX.md` (update links)
- `features/hardware/01_TIER1_PERMANENT.md` (sensor stack)
- `features/data-pipeline/00_OVERVIEW.md` (multi-sensor)
- `features/operations/01_FLEET_HEALTH.md` (monitoring)

### Test Documentation
- Create mock radar simulator
- Create multi-vehicle test scenarios
- Add track association tests
- Add sensor fusion validation tests

---

## Timeline

**Week 1 (Critical Path):**
- [ ] Hardware stack updated (3h)
- [ ] Violation models updated (4h)
- [ ] Feature tests updated (6h)
- **Total: 13 hours**

**Week 2:**
- [ ] Edge capture documentation (5h)
- [ ] Sensor drivers architecture (4h)
- [ ] Test infrastructure (3h)
- [ ] Feature index (1h)
- **Total: 13 hours**

**Week 3+:**
- [ ] Advanced features (Digital Twin, APIs, etc.)
- [ ] Integration & validation

**Grand Total: ~50 hours for full documentation**

---

## Implementation Priority

### MUST HAVE (Before field trial)
1. Sensor fusion architecture ✓
2. Hardware stack (with radar)
3. Speeding detection (radar mode)
4. Test scenarios (multi-vehicle)
5. Edge capture documentation

### SHOULD HAVE (Before Phase 1)
6. Sensor drivers architecture
7. Test infrastructure (radar)
8. Operations monitoring
9. API documentation

### NICE TO HAVE (After Phase 1)
10. Digital twin updates
11. Advanced features
12. Advanced hardware tiers

---

## Validation Checklist

Once all updates are complete:

- [ ] All documents reference sensor fusion
- [ ] Hardware requirements consistent across docs
- [ ] Test scenarios match architecture
- [ ] APIs support multi-sensor data
- [ ] Monitoring covers fusion health
- [ ] Feature timeline reflects new architecture
- [ ] Hardware costs updated
- [ ] Links between documents working
- [ ] No stale references to OBD-only approach

---

## Key Metrics After Integration

**Before Sensor Fusion:**
- Violations detected: Host vehicle only (~10% of total)
- Confidence: 99% (OBD ground truth)
- Scalability: Limited to vehicle telemetry

**After Sensor Fusion:**
- Violations detected: ALL vehicles (~90% improvement)
- Confidence: 95% (radar + ALPR fusion)
- Scalability: Independent of host vehicle motion
- Competitive advantage: Detection beyond Nexar/Rekor

---

## Next Steps

1. **This week:** Complete Phase 1 updates (13 hours)
   - Hardware stack + violation models + feature tests

2. **Next week:** Complete Phase 2 (13 hours)
   - Edge capture + sensor drivers + test infrastructure

3. **Week 3:** Validation & field trial prep
   - All docs consistent
   - Test suite ready
   - Hardware ordered

---

**The sensor fusion architecture transforms CivicMesh from a dashcam-based system to a true multi-sensor enforcement platform.**

This is the moat.
