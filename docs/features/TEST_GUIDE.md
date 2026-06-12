# CivicMesh Feature Testing Guide

**Master guide for testing all CivicMesh platform features as independent modules.**

---

## Overview

This guide provides a standardized approach to testing each feature in isolation, then integrating them into the complete platform.

**Testing Philosophy:**
- Test each feature module independently first
- Verify subfeatures within each module
- Use mock data / simulators where hardware isn't available
- Create repeatable, automated test suites
- Document acceptance criteria for each feature

---

## Directory Structure

```
features/
├── TEST_GUIDE.md                    # This file
├── TEST_INFRASTRUCTURE.md           # Common testing tools & setup
├── INTEGRATION_TESTS.md             # Cross-module integration tests
├── violation-detection/
│   ├── 00_OVERVIEW.md               # Feature overview & roadmap
│   ├── 01_SPEEDING.md               # Speeding detection feature
│   ├── 02_LANE_VIOLATIONS.md        # Lane violation feature
│   ├── 03_ALPR.md                   # License plate recognition
│   ├── 04_BUS_LANE.md               # Bus lane obstruction
│   ├── 05_STOP_SIGN.md              # Stop sign running
│   ├── 06_ACCESSIBLE_PARKING.md     # Accessible parking
│   ├── 07_SCHOOL_ZONE.md            # School zone violations
│   ├── 08_EXPIRED_PLATES.md         # Expired/suspended plates
│   ├── 09_CONSTRUCTION_ZONE.md      # Construction zone violations
│   └── 10_SNOW_ROUTE.md             # Snow route parking
├── enforcement/
│   ├── 00_OVERVIEW.md
│   ├── 01_REVIEW_QUEUE.md
│   ├── 02_OFFICER_APPROVAL.md
│   ├── 03_CITATION_GENERATION.md
│   └── 04_MTO_INTEGRATION.md
├── digital-twin/
│   ├── 00_OVERVIEW.md
│   ├── 01_ROAD_CONDITION.md
│   ├── 02_SIGN_INVENTORY.md
│   ├── 03_SIGNALS.md
│   ├── 04_LANE_MARKINGS.md
│   ├── 05_CONSTRUCTION_ZONES.md
│   ├── 06_ACCESSIBILITY.md
│   └── 07_PEDESTRIAN_CYCLISTS.md
├── data-pipeline/
│   ├── 00_OVERVIEW.md
│   ├── 01_EDGE_CAPTURE.md
│   ├── 02_ENCRYPTION.md
│   ├── 03_UPLOAD.md
│   ├── 04_INGESTION.md
│   └── 05_DEDUPLICATION.md
├── officer-portal/
│   ├── 00_OVERVIEW.md
│   ├── 01_QUEUE_MANAGEMENT.md
│   ├── 02_VIDEO_PLAYBACK.md
│   ├── 03_APPROVAL_WORKFLOW.md
│   └── 04_AUDIT_LOGGING.md
├── hardware/
│   ├── 00_OVERVIEW.md
│   ├── 01_TIER1_PERMANENT.md
│   ├── 02_TIER2_SEASONAL.md
│   ├── 03_TIER3_CONTRACTOR.md
│   └── 04_TIER4_CIVILIAN.md
├── business/
│   ├── 00_OVERVIEW.md
│   ├── 01_REVENUE_CALCULATION.md
│   ├── 02_REVENUE_SHARING.md
│   └── 03_MSSP_BILLING.md
├── insurance/
│   ├── 00_OVERVIEW.md
│   ├── 01_DRIVING_RECORDS.md
│   ├── 02_RISK_SCORING.md
│   └── 03_CLAIMS_FAST_TRACK.md
├── blockchain/
│   ├── 00_OVERVIEW.md
│   ├── 01_DID.md
│   ├── 02_VERIFIABLE_CREDENTIALS.md
│   └── 03_ON_CHAIN_ANCHORING.md
├── federation/
│   ├── 00_OVERVIEW.md
│   ├── 01_MULTI_CITY.md
│   └── 02_DATA_SHARING.md
├── training/
│   ├── 00_OVERVIEW.md
│   ├── 01_TECHNICIAN_CERT.md
│   ├── 02_MSSP_CERT.md
│   └── 03_COMMUNITY_MANAGER_CERT.md
├── governance/
│   ├── 00_OVERVIEW.md
│   ├── 01_TRANSPARENCY_REPORTS.md
│   ├── 02_AUDIT_FRAMEWORK.md
│   └── 03_OPEN_GOVERNANCE.md
├── api/
│   ├── 00_OVERVIEW.md
│   ├── 01_INGESTION_API.md
│   ├── 02_OFFICER_API.md
│   ├── 03_INSURANCE_API.md
│   └── 04_PUBLIC_API.md
└── operations/
    ├── 00_OVERVIEW.md
    ├── 01_FLEET_HEALTH.md
    ├── 02_INCIDENT_MANAGEMENT.md
    ├── 03_COMPLIANCE.md
    └── 04_MONITORING.md
```

---

## Feature Test Document Template

Each feature test document follows this structure:

### 1. Feature Overview
- What is it?
- Why is it important?
- Where does it fit in the platform?

### 2. Subfeatures
- List all subfeatures
- Interdependencies
- Acceptance criteria for each

### 3. Requirements

#### Software
- Languages (Python, Node.js, etc.)
- Libraries/dependencies
- Version requirements

#### Data/Media
- Sample videos (dashcam footage)
- Test data (OBD logs, GPS coordinates)
- Ground truth datasets

#### Hardware (if applicable)
- Physical components needed
- Simulation alternatives

#### Environment
- OS requirements
- Database requirements
- Network requirements

### 4. Testing Methodology

#### Unit Testing
- Test individual functions
- Mock external dependencies
- Target: >80% code coverage

#### Integration Testing
- Test subfeature interactions
- Simulate realistic data flows
- Mock upstream/downstream systems

#### End-to-End Testing
- Full feature from start to finish
- Real data where possible
- Acceptance criteria verification

#### Performance Testing
- Latency requirements
- Throughput targets
- Resource usage (CPU, memory)

### 5. Test Suite

#### Test Data
- Sample inputs
- Expected outputs
- Edge cases

#### Acceptance Criteria Checklist
- Clear pass/fail criteria
- Measurable thresholds

#### Test Code
- Python test class (pytest)
- Setup/teardown
- Individual test methods

---

## Testing Workflow

### Week 1: Unit Tests
- Create test data generators
- Write unit tests for each module
- Achieve >80% code coverage
- Run daily

### Week 2: Integration Tests
- Test feature interactions
- Mock external systems
- Verify data flows
- Daily test runs

### Week 3: End-to-End Tests
- Full feature workflows
- Use realistic test data
- Verify acceptance criteria
- Manual + automated testing

### Week 4: Performance Tests
- Measure latency
- Verify throughput
- Optimize bottlenecks
- Load test (if applicable)

### Week 5: Module Integration
- Combine 2–3 features
- Test interactions
- Identify missing pieces
- Refine architecture

---

## Test Data Management

### Video Assets
Store in `features/test-data/videos/`:
- `dashcam_normal_traffic.mp4` (1 min)
- `dashcam_speeding.mp4` (30 sec)
- `dashcam_lane_violation.mp4` (30 sec)
- `dashcam_night_vision.mp4` (30 sec)
- `dashcam_rain_weather.mp4` (30 sec)

**Where to get:**
- YouTube dashcam clips (creative commons)
- Cityscapes dataset (academic)
- Your own phone camera

### OBD Test Data
Store in `features/test-data/obd/`:
- `speeding_scenario.json` — Vehicle goes 65 in 50 zone
- `normal_traffic.json` — Steady speed, no violations
- `multiple_events.json` — Several violations in sequence

### Expected Results
Store in `features/test-data/results/`:
- `speeding_expected.json` — Ground truth detections
- `lane_violation_expected.json` — Expected violations
- Confidence scores, timestamps, etc.

---

## Running Tests

### All Features
```bash
cd features
pytest -v --cov=. --cov-report=html
```

### Single Feature
```bash
cd features/violation-detection
pytest 01_SPEEDING.md -v
```

### With Coverage Report
```bash
pytest --cov=features --cov-report=term-missing
```

### Continuous Testing
```bash
# Watch for file changes, re-run tests
pytest-watch
```

---

## Acceptance Criteria (Global)

Every feature must pass these tests before moving to next phase:

- [ ] All unit tests pass (100% of test suite)
- [ ] Code coverage ≥80%
- [ ] Integration tests pass (all subfeatures)
- [ ] No known bugs (critical/high severity)
- [ ] Performance targets met
  - API latency <200ms
  - Inference <200ms (edge compute)
  - Upload success >99%
- [ ] Security requirements met
  - Encryption in transit (TLS 1.3)
  - Signed audit logs
  - No hardcoded secrets
- [ ] Documentation complete
  - Feature overview
  - API documentation
  - User guide (if applicable)
- [ ] Code review approved
  - Peer review completed
  - Architecture sound
  - No technical debt introduced

---

## Test Phases

### Phase 0: MVP (Weeks 1–12)
**Focus:** Violation detection (speeding only) + enforcement workflow + data pipeline

Features to test:
- [ ] Speeding detection
- [ ] Officer portal (basic)
- [ ] Data ingestion
- [ ] MTO integration

**Success Criteria:**
- 5 buses operational 24/7
- <5% false positive rate
- 99%+ uptime

### Phase 1: OC Transpo Pilot (Months 4–9)
**Focus:** Lane violations, bus lane obstruction, stop signs

Features to test:
- [ ] All above
- [ ] Lane violation detection
- [ ] Bus lane detection
- [ ] Stop sign detection
- [ ] Digital twin (foundation)

**Success Criteria:**
- 50 buses operational
- <5% false positive rate
- $100K+/month revenue

### Phase 2: City Fleet (Months 10–18)
**Focus:** Accessible parking, school zones, snow routes

Features to test:
- [ ] All above
- [ ] Accessible parking detection
- [ ] School zone enforcement
- [ ] Snow route parking
- [ ] Digital twin (full)
- [ ] APIs (Public Works, OC Transpo)

**Success Criteria:**
- 200+ city vehicles
- <5% false positive rate
- $500K+/month revenue

### Phase 3: Civilian Opt-In (Months 19–24)
**Focus:** Insurance, blockchain, federation

Features to test:
- [ ] All above
- [ ] Insurance API
- [ ] Revenue sharing
- [ ] Blockchain/DID
- [ ] Federation (multi-city)

**Success Criteria:**
- 5,000+ civilian participants
- Insurance partnerships active
- $1M+/month revenue

---

## Debugging Test Failures

### Common Issues & Solutions

| Issue | Cause | Solution |
|-------|-------|----------|
| ALPR confidence too low | Poor image quality | Use better dashcam footage, adjust model threshold |
| Lane detection misses violations | Model trained on sunny days | Add rainy/night test videos |
| Dedup doesn't merge duplicates | Geofence too small | Increase radius to 100m, adjust time window |
| False positives high | Model overfitting | Retrain with more diverse data |
| Upload failures | Mock API down | Check mock server logs, restart service |

---

## Test Data Generation

### Synthetic Video Generation
```python
# Generate fake dashcam footage for testing
import cv2
import numpy as np

def generate_test_video(filename, duration_sec=30):
    """Generate synthetic traffic video."""
    fourcc = cv2.VideoWriter_fourcc(*'mp4v')
    out = cv2.VideoWriter(filename, fourcc, 30.0, (1920, 1440))
    
    for frame_num in range(duration_sec * 30):
        # Create blank frame
        frame = np.ones((1440, 1920, 3), dtype=np.uint8) * 100
        
        # Draw road elements
        # - Lane markings (white lines)
        # - Road surface
        # - Sky
        # - Vehicles
        # - Signs
        
        out.write(frame)
    
    out.release()
```

### Mock OBD Data
```python
# Generate realistic OBD scenarios
import json

scenario = {
    "description": "Speeding in residential zone",
    "speed_limit": 50,
    "events": [
        {"time": 0, "speed": 45, "heading": 180},
        {"time": 5, "speed": 65, "heading": 180},
        {"time": 10, "speed": 60, "heading": 180},
    ]
}

with open("speeding_scenario.json", "w") as f:
    json.dump(scenario, f)
```

---

## Reporting Test Results

Create a test report template:

```markdown
# Test Report: [Feature Name]

**Date:** 2026-06-03  
**Tester:** [Name]  
**Environment:** [OS, Python version, etc.]  

## Test Execution

| Feature | Unit Tests | Integration Tests | E2E Tests | Status |
|---------|-----------|------------------|-----------|--------|
| Speeding | 12/12 ✓ | 5/5 ✓ | 3/3 ✓ | PASS |
| Lane Detection | 10/10 ✓ | 4/4 ✓ | 2/3 ✗ | FAIL |

## Issues Found

- [ ] **CRITICAL:** Lane detection fails at night
- [ ] **HIGH:** False positive rate 8% (target <5%)
- [ ] **MEDIUM:** Latency 250ms (target <200ms)

## Recommendations

1. Retrain lane detection model with night footage
2. Adjust confidence thresholds
3. Optimize inference code (consider GPU acceleration)

## Sign-Off

- [ ] Ready for Phase 1
- [ ] Needs fixes before deployment
```

---

## Next Steps

1. **Review this guide** (30 min)
2. **Create feature test docs** (one per feature, 4–6 hours total)
3. **Set up test infrastructure** (mock APIs, test data generators, 2 hours)
4. **Write unit tests** (2 weeks)
5. **Write integration tests** (1 week)
6. **Run full test suite** (ongoing)

---

**Start with:** Violation detection (speeding) → Enforcement workflow → Data pipeline

These three features cover the MVP and are independent enough to test in parallel.
