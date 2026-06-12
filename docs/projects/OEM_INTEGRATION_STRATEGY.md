# OEM Integration Strategy: Native Vehicle Platform Integration

**Ship CivicMesh as a native app/service on new vehicles. Leverage existing OEM sensors (cameras, radar, LiDAR, OBD/CAN, cellular) for zero-retrofit deployment at scale.**

---

## Vision

CivicMesh transforms from a retrofit aftermarket product into a native vehicle service. Modern vehicles (2020+) already have:
- Front + rear cameras (all vehicles)
- 77 GHz radar (80%+ of new vehicles)
- LiDAR (Tesla, Volvo, BMW, soon Audi, Cadillac, others)
- OBD/CAN bus (all vehicles)
- Built-in cellular (GM OnStar, Tesla Connectivity, Ford SYNC, Volvo, others)
- Edge compute platforms (Tesla MCU3, Ford Sync 4, GM IVI, etc.)

**Opportunity:** One OEM contract = millions of vehicles over 5–7 years. No hardware deployment costs. Instant network effects.

**Market Reality:**
- OEMs are desperate for new revenue streams (EV margins compressed 50%+)
- Fleet managers already pay for telematics (Geotab, Samsara, Motive)
- Insurance companies want native integrations (not third-party dongles)
- Autonomous vehicle development requires real-world sensor data
- CivicMesh's DAO governance + driver-friendly splits = differentiation vs. traditional fleet management

---

## Market Opportunity

### Scale Comparison

**Retrofit model (current):**
- OC Transpo: 50 buses (2025)
- City of Toronto: 200 vehicles (2025–2026)
- Fleets: 500–1000 vehicles/year by 2026
- **Total by 2026: ~2000 vehicles**

**OEM native model (Phase 3+):**
- Ford F-Series (top-selling truck, 700K units/year) → 1 contract = 700K vehicles available
- Tesla (1M+ vehicles/year, already have computing + cellular)
- GM (OnStar 9M+ vehicles)
- Toyota (10M+ vehicles/year globally, but conservative)
- **Total by 2028: 10M+ vehicles** (if 3 major OEMs commit)

**Revenue impact:**
- Retrofit: $550/operator/month (limited by hardware availability)
- Native OEM: $2–5/vehicle/month (telematics licensing) + enforcement revenue split

---

## OEM Target Strategy

### Tier 1: Fast-Follower OEMs (Easiest Entry Points)

#### Tesla
**Why:** No retrofit needed, already have infrastructure for scale
- **Sensors:** Cameras (8x), radar, compute (MCU3 with Dojo AI chip)
- **Cellular:** Built-in Tesla cellular (unlimited data in many markets)
- **Compute:** MCU3 capable of edge inference (already run computer vision models)
- **OTA Updates:** Already deploy software globally weekly
- **Vehicle fleet:** 1.8M+ vehicles globally, 400K+ in North America
- **Revenue need:** Supercharger network margins declining, looking for software revenue
- **Approach:** Pitch to VP Engineering: "Monetize unused compute. Driver-friendly (85% take-home). Insurance partnerships (Intact, TD). Global scale."
- **Timeline:** Partnership discussion Q1 2027, pilot Q3 2027, rollout 2028+

#### Ford
**Why:** Struggling with EV profitability, has SYNC ecosystem
- **Sensors:** Cameras (F-150, Mustang), radar, OBD/CAN access
- **Cellular:** Some models have built-in 4G (via AT&T), Ford Connect Platform
- **Compute:** Ford Sync IVI (Android-based in newer models)
- **Vehicle fleet:** F-Series 700K/year (most profitable Ford vehicles)
- **Revenue need:** High (EV margins -$6K+/vehicle initially)
- **Approach:** Pitch to VP Connected Services: "Add $200/vehicle lifetime value. Fleet customers already pay for Geotab/Samsara—we offer better driver splits. Intact partnership."
- **Timeline:** Partnership Q2 2027, pilot Q4 2027, rollout 2028+

#### Volvo (Trucks + Cars)
**Why:** Heavy investment in autonomous vehicles, wants sensor data
- **Sensors:** Multiple cameras, radar, some LiDAR (Volvo Cars)
- **Cellular:** Built-in 4G/5G
- **Compute:** Volvo Connect platform (growing)
- **Vehicle fleet:** 700K+ new vehicles/year
- **Revenue need:** High (autonomous development is expensive)
- **Approach:** Pitch to VP Autonomous Development: "Real-world sensor data from millions of vehicles. Federated learning (decentralized, privacy-preserving). Help train autonomous models."
- **Timeline:** Partnership Q1 2027, pilot Q2 2027, rollout 2028+

### Tier 2: Conservative OEMs (Longer Sales Cycle, Higher Payoff)

#### General Motors
**Why:** OnStar ecosystem (9M+ connected vehicles), commitment to EVs
- **Sensors:** Cameras, radar (most GM vehicles 2020+)
- **Cellular:** OnStar built-in 4G/5G (Verizon-backed)
- **Compute:** GM Digital Services platform
- **Revenue:** OnStar subscription model already mature ($200–300/year per vehicle)
- **Approach:** Pitch to OnStar executive: "CivicMesh as OnStar premium tier. Driver safety + earnings. Insurance partnerships. Integrated with existing billing."
- **Timeline:** Partnership Q3 2027, pilot Q1 2028, rollout 2028+

#### Toyota
**Why:** #1 global automaker, conservative but massive scale
- **Sensors:** Standard (cameras, radar) in high-end models
- **Cellular:** Toyota Connected (growing 4G integration)
- **Compute:** T-Connect ecosystem (but less open than Ford/GM)
- **Revenue:** Conservative on new services, but interested in data
- **Approach:** Pitch to Toyota Connected exec: "Fleet safety data + autonomous development research. Long-term partnership. Proven by Tesla/Ford pilots."
- **Timeline:** Partnership Q4 2027, pilot Q3 2028, rollout 2029+

### Tier 3: Opportunity OEMs (Competitive Advantage Seekers)

#### Chinese OEMs (BYD, Li Auto, NIO)
**Why:** No legacy constraints, aggressive software integration, growing North American presence
- **Sensors:** Often have better camera + LiDAR than Western OEMs
- **Cellular:** Native 5G integration (Chinese carriers)
- **Compute:** Advanced (Nvidia Orin, custom chips)
- **Revenue:** Need differentiation (safety, connectivity)
- **Approach:** Pitch: "Safety is a differentiator in North America. DAO governance = trust. Community-driven (not Big Brother)."
- **Timeline:** Partnership Q2 2027, rollout 2027–2028

---

## Native Integration Architecture

### Sensor Access (Read-Only APIs)

CivicMesh runs as a native app on OEM vehicle compute platform. Access to sensors via APIs:

```
┌─────────────────────────────────────────────────┐
│        CivicMesh Native App (Sandbox)           │
│                                                 │
│  ┌──────────────────────────────────────────┐   │
│  │  Violation Detection Engine               │   │
│  │  - Front/rear camera analysis             │   │
│  │  - Radar-based following distance         │   │
│  │  - Lane detection                         │   │
│  │  - Speed verification                     │   │
│  │                                           │   │
│  │  Edge Inference (TPU/GPU if available)    │   │
│  │  - License plate recognition              │   │
│  │  - Face blurring (PII protection)         │   │
│  │  - Object detection                       │   │
│  │                                           │   │
│  │  Data Upload Pipeline                     │   │
│  │  - Evidence clips (compressed)            │   │
│  │  - Metadata (speed, GPS, timestamp)       │   │
│  │  - Blockchain attestation                 │   │
│  └──────────────────────────────────────────┘   │
│                     │                           │
└─────────────────────┼───────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────┐
│      OEM Vehicle Operating System Layer          │
│                                                 │
│  ┌──────────────────────────────────────────┐   │
│  │  Sensor Access APIs (Read-Only)          │   │
│  │                                          │   │
│  │  - getCameraFrame(cam_id) → JPEG        │   │
│  │  - getRadarObjects() → [targets]        │   │
│  │  - getLiDARPointCloud() → points        │   │
│  │  - getOBD(pid) → value                  │   │
│  │  - getGPS() → location                  │   │
│  │  - getIMU() → accel/rotation            │   │
│  │  - getVehicleSpeed() → kmh              │   │
│  │  - getBrakeState() → bool               │   │
│  │                                          │   │
│  └──────────────────────────────────────────┘   │
│                     │                           │
│  ┌──────────────────┼──────────────────────┐   │
│  │                  ▼                      │   │
│  │  ┌──────────┐  ┌────────┐  ┌───────┐  │   │
│  │  │ Cameras  │  │ Radar  │  │ LiDAR │  │   │
│  │  └──────────┘  └────────┘  └───────┘  │   │
│  │  ┌──────────┐  ┌────────┐  ┌───────┐  │   │
│  │  │  OBD     │  │  GPS   │  │  IMU  │  │   │
│  │  └──────────┘  └────────┘  └───────┘  │   │
│  │                                        │   │
│  └────────────────────────────────────────┘   │
│                                                 │
│  ┌──────────────────────────────────────────┐   │
│  │  Vehicle Connectivity                     │   │
│  │  - Cellular modem (OEM-built or partner) │   │
│  │  - Wi-Fi (optional, home charging)       │   │
│  │  - Data usage quota management           │   │
│  └──────────────────────────────────────────┘   │
│                                                 │
└─────────────────────────────────────────────────┘
```

### OEM Platform Support

| OEM | Platform | SDK | Languages | Cellular | Compute |
|-----|----------|-----|-----------|----------|---------|
| **Tesla** | Custom (MCU3) | Proprietary | C++/Python | Built-in | Dojo-capable |
| **Ford** | Android Automotive | SYNC SDK | Java/Kotlin | Ford Connect (AT&T) | IVI processor |
| **GM** | Android/Proprietary | OnStar API | Java/C++ | OnStar (Verizon) | IVI processor |
| **Volvo** | Proprietary | Volvo SDK | C++/Java | Built-in 5G | Nvidia Tegra |
| **Toyota** | Proprietary | T-Connect API | Java/C++ | Toyota Connected | Proprietary |
| **VW Group** | Proprietary | VW.OS SDK | C++/Java | Built-in cellular | Proprietary |

---

## API Specification (High-Level)

### Sensor Input APIs

```python
# Camera Access
camera_frame = vehicle.getSensorData(
    sensor_id="CAMERA_FRONT",
    resolution=(1440, 720),  # 720p for bandwidth efficiency
    format="JPEG",
    quality=85  # compression quality
)

# Radar Objects
radar_objects = vehicle.getSensorData(
    sensor_id="RADAR_FRONT",
    return_type="objects"  # [distance, velocity, azimuth, tracking_id]
)
# Example output:
# {
#   "objects": [
#     {"distance_m": 45, "velocity_kmh": -12, "azimuth_deg": 4},
#     {"distance_m": 90, "velocity_kmh": -25, "azimuth_deg": -2}
#   ]
# }

# LiDAR Point Cloud
lidar_cloud = vehicle.getSensorData(
    sensor_id="LIDAR_FRONT",
    return_type="point_cloud",
    max_points=10000  # downsample if needed
)

# OBD-II / CAN Data
obd_data = vehicle.getSensorData(
    sensor_id="OBD",
    pids=["VEHICLE_SPEED", "ENGINE_RPM", "THROTTLE_POSITION", "FUEL_LEVEL"]
)
# Returns: {"VEHICLE_SPEED": 65, "ENGINE_RPM": 2000, ...}

# GPS
gps_data = vehicle.getSensorData(
    sensor_id="GPS"
)
# {"latitude": 43.6629, "longitude": -79.3957, "accuracy_m": 5, "heading_deg": 180}

# Vehicle State
vehicle_state = vehicle.getVehicleState()
# {"speed_kmh": 65, "gear": "D", "brake_pedal_pressed": False, "turning_indicator": "LEFT"}
```

### Data Upload API

```python
# Upload evidence to CivicMesh backend
upload_result = vehicle.uploadData(
    data=evidence_package,  # bytes
    endpoint="https://civicmesh.com/api/v1/ingest",
    priority="high",  # prioritize over other data (optional)
    background=False  # wait for completion (or async)
)
# Returns: {"success": True, "incident_id": "CM-2027-00421"}

# Check connectivity status
network_status = vehicle.getNetworkStatus()
# {"connected": True, "signal_strength": 3, "type": "4G", "data_available_mb": 500}
```

### Vehicle-to-App Notifications

```python
# CivicMesh can notify driver of violations detected/issued
vehicle.showNotification(
    title="Speed Violation Detected",
    message="Ticket issued: $110 fine. Your $5.50 deposit is pending.",
    icon="CIVICMESH",
    urgency="INFO"  # INFO, WARNING, ALERT
)

# Log event in vehicle history
vehicle.logEvent(
    category="CIVICMESH_VIOLATION",
    severity="NOTICE",
    description="Speed violation: 65 km/h in 50 km/h zone"
)
```

---

## Software Architecture: Multi-Tier Integration

### Layer 1: OEM Vehicle OS (Untouched)
- OEM retains full control of safety, steering, braking, throttle
- CivicMesh has read-only sensor access
- CivicMesh cannot affect vehicle operation (safety-first)

### Layer 2: CivicMesh Sandbox Application
- Runs as isolated containerized app (Docker or OEM equivalent)
- Resource limits: CPU 10%, RAM 256MB (minimal overhead)
- Network isolation: Only uploads to CivicMesh endpoints
- Permissions: Read sensors, upload data, display notifications
- **No permissions:** Control vehicle, modify OEM software, access driver personal data

### Layer 3: Edge Inference (If Available)
- Tesla vehicles: Use MCU3 GPU for inference (~50 TFLOPS)
- Ford SYNC 4: Can offload inference to cloud or use quantized models
- Volvo: Nvidia Tegra-capable (can run full TensorFlow models)
- Others: Cloud-based inference (slower but acceptable)

**Inference models shipped with app:**
- ALPR (license plate recognition): 100ms / plate
- Face blurring: 200ms / frame
- Object detection: 150ms / frame
- Lane detection: 100ms / frame

### Layer 4: Data Upload & Queuing
- OEM manages cellular quota (CivicMesh respects battery + data budget)
- Offline queueing: If no connectivity, buffer evidence locally (encrypted)
- Sync when connected (opportunistic: charge time, home Wi-Fi, cellular available)
- Compression: H.264 video → 500 KB per 10-second clip

---

## Revenue Models

### Model 1: Licensing Fee (Per-Vehicle Annual)

**CivicMesh charges OEM for integration:**
- $5–10 per vehicle per year (recurring)
- OEM can recoup via subscription to end-user or pass to enforcement revenue split

**Example (Ford F-Series):**
- 700K vehicles/year × $7/vehicle = $4.9M/year revenue
- Ford passes cost to customers ($0.50/month added to payment)
- Customers recoup via $5–10/violation earnings + insurance discounts

---

### Model 2: Revenue Share (Enforcement Fines)

**Split enforcement revenue:**
```
Fine: $110
├─ City/Municipality: 72% = $79.20
├─ CivicMesh platform: 15% = $16.50
├─ OEM (licensing/hosting): 3% = $3.30
├─ CCSC (community oversight): 8% = $8.80
└─ Driver/Operator: 5% = $5.50

Alternative (aggressive OEM pitch):
├─ City: 68% = $74.80
├─ CivicMesh: 12% = $13.20
├─ OEM: 7% = $7.70
├─ CCSC: 8% = $8.80
└─ Driver: 5% = $5.50
```

---

### Model 3: Insurance Data Licensing

**OEM can negotiate insurance partnerships:**
- Intact, TD, Aviva pay for real-time telematics
- $3–5 per vehicle per month (aggregated data)
- OEM takes 50%, CivicMesh 50% (or OEM negotiates)

**Example (Ford + Intact partnership):**
- 500K Ford vehicles with CivicMesh
- $4/vehicle/month × 500K = $2M/month (Intact pays)
- Ford gets $1M/month, CivicMesh $1M/month

---

### Model 4: Premium Telematics (Driver-Facing)

**CivicMesh offers premium in-app features:**
- Driver safety dashboard: "Your vehicle is in top 10% for safe driving"
- Earnings tracking: "This month: $45 in enforcement revenue"
- Community impact: "Your reports helped reduce speeding 23%"
- Insurance optimization: "Switch to Intact and save $180/year"

**Premium subscription:** $4.99/month → OEM 20%, CivicMesh 80%

---

### Model 5: Data Aggregation & Urban Planning

**CivicMesh anonymizes and sells insights:**
- City traffic patterns (anonymized): Sold to municipal planning
- Insurance risk maps: Sold to underwriters
- Autonomous vehicle training data: Sold to AV companies

**Privacy-first:** No individual driver identification, only aggregated statistics.

---

## Phased Integration Rollout

### Phase 3a: Pilot & Partnership (2027–Q1 2028)

**Goal:** Prove viability with 1–2 OEMs

**Partners:** Tesla (tech-forward) + Ford (scale + profitability need)

**Vehicle segments:**
- Tesla: Model 3/Y (100K vehicles in pilot)
- Ford: F-150 Electric (50K vehicles in pilot)

**Features deployed:**
- Core enforcement: Speed, lane violations
- Radar-based following distance (if available)
- Edge inference: ALPR, face blurring
- Insurance data: Real-time telematics upload
- Driver earnings: Pay-per-violation

**Success criteria:**
- 50K+ active users
- $3M+ enforcement revenue (2027–Q1 2028)
- 99% uptime (vehicle OEM expectation)
- <0.5% false positive rate
- 1000+ violations detected/day across fleet

---

### Phase 3b: OEM Expansion (Q2 2028–Q4 2028)

**Goal:** Expand to 5–10 OEMs globally

**Partners:** Volvo, GM, Toyota, Volkswagen, BYD, NIO

**Geographic expansion:** Canada → USA → Mexico → Europe → Asia

**Vehicle segments:** Full lineup (trucks, cars, SUVs, vans)

**Deployment scale:** 2M+ vehicles

**New features:**
- Multi-camera support (rear, side cameras)
- LiDAR integration (for OEMs with LiDAR)
- Ride-sharing safety (interior cameras where available)
- Insurance premium optimization
- DAO governance (vehicle owners vote on enforcement priorities)

**Success criteria:**
- 5M+ potential vehicles (contractual)
- $50M+ annual enforcement revenue run-rate
- 3+ major insurers partnered
- 5+ cities integrated (311 APIs)

---

### Phase 3c: Market Dominance (2029+)

**Goal:** CivicMesh becomes standard in 50%+ of new vehicles globally

**Deployment scale:** 10M+ vehicles

**Market position:** CivicMesh is the de facto enforcement network for North America

**Network effects:**
- Insurance discounts available everywhere
- Driver reputation (safety score) portable across vehicle brands
- City enforcement unified (same system in every vehicle)
- Autonomous vehicle development accelerated (real-world sensor data)

---

## OEM Concerns & Mitigation

### Concern 1: Data Privacy & Liability

**OEM worry:** "Is CivicMesh collecting driver personal data? Am I liable if data is breached?"

**Mitigation:**
- CivicMesh is data processor, OEM is data controller (under GDPR/PIPEDA)
- Drivers own their data; can request deletion anytime
- No personal data is collected (only anonymized events, speed, violations)
- CivicMesh publishes annual security audit (third-party)
- Driver location is never logged (only violation metadata + GPS timestamp)

**Proof:** Point to [[docs/PRIVACY_DATA_GOVERNANCE.md]] and [[docs/COMMUNITY_EVIDENCE_VIEWING.md]]

---

### Concern 2: Safety & Liability

**OEM worry:** "What if CivicMesh software causes a vehicle to malfunction?"

**Mitigation:**
- CivicMesh runs in isolated sandbox (no access to steering, braking, throttle)
- OEM safety systems are completely independent
- CivicMesh is read-only (sensor access only)
- Formal safety certification: ISO 26262 ASIL B (if needed)
- Insurance: CivicMesh carries $50M liability umbrella

---

### Concern 3: Competition

**OEM worry:** "What if Ford uses CivicMesh, but GM wants to develop competing system?"

**Mitigation:**
- Non-exclusive partnership (OEM can have competing systems)
- 5-year contract minimum (gives CivicMesh time to entrench)
- Revenue share incentivizes OEM to promote CivicMesh (better margins than OEM-developed system)
- DAO governance prevents "pulling the rug" (OEM can't cut driver earnings)

---

### Concern 4: Regulatory Backlash

**OEM worry:** "What if cities ban CivicMesh for privacy reasons (like Ring/Flock)?"

**Mitigation:**
- CivicMesh is community-governed (DAO), not corporate surveillance
- Transparency-first: All violations publicly logged, community can challenge
- Privacy-first: No tracking, only violation detection
- Police can't secretly use CivicMesh (unlike Ring/Flock integration)
- Track record: [[docs/TRANSPARENCY_LAYER.md]] explains why CivicMesh avoids Flock backlash

---

## Competitive Analysis

### vs. Geotab (Fleet Management)

| Aspect | Geotab | CivicMesh |
|--------|--------|-----------|
| **Fleet focus** | Fleet mgmt (fuel, maintenance) | Fleet + consumer enforcement |
| **Insurance** | Limited partnerships | Deep insurance integrations |
| **OEM partnerships** | Some (Hyundai, Kia) | Targeting Tesla, Ford, Volvo |
| **Driver earnings** | None (fleet owns data) | Drivers earn $5–10/violation |
| **Governance** | Corporate (Viasat owns) | DAO (community-governed) |

**CivicMesh advantage:** Drivers earn money + have governance rights. OEMs like this (appeals to consumers).

---

### vs. Verizon Connect (Connected Vehicles)

| Aspect | Verizon | CivicMesh |
|--------|---------|-----------|
| **Focus** | Telematics + fleet ops | Enforcement + multi-service |
| **OEM relationships** | Deep (Verizon owns OnStar partnership) | Building (targeting Ford, Volvo) |
| **Insurance** | Available but secondary | Primary revenue stream |
| **Enforcement** | Not a focus | Core product |
| **Decentralization** | Centralized (Verizon owned) | Decentralized (DAO) |

**CivicMesh advantage:** Enforcement data creates new revenue stream. Decentralization appeals to privacy advocates.

---

### vs. Motive (Fleet + ADAS)

| Aspect | Motive | CivicMesh |
|--------|--------|-----------|
| **Fleet** | Strong (compliance, safety) | Growing (through OEMs) |
| **OEM partnerships** | Limited | Targeting major OEMs |
| **Consumer app** | None | In-app driver earnings |
| **Insurance** | Available | Deep partnerships |
| **Decentralization** | Centralized | DAO-governed |

**CivicMesh advantage:** Consumer-friendly (earnings + governance). OEM scale faster than retrofit.

---

## Implementation Roadmap

### Q1–Q2 2027: Partnership Development

**Activities:**
1. Formal pitch to Tesla, Ford, Volvo executive teams
2. Proof of concept: Integrate CivicMesh with Ford SYNC SDK
3. Regulatory alignment: Ensure compliance (no conflicts with OEM liability)
4. Contract negotiation: Revenue share, exclusivity, data ownership

**Deliverables:**
- Partnership LOI signed with ≥1 OEM
- Technical spec document (OEM + CivicMesh)
- API specification (finalized)
- Sample implementation (proof of concept)

### Q3 2027: Pilot Deployment

**Activities:**
1. Deploy to 100K vehicles (Tesla or Ford pilot fleet)
2. Monitor: Uptime, false positive rate, user adoption
3. Insurance partnership: Align with Intact/TD
4. Regulatory approval: If needed (jurisdiction-specific)

**Success metrics:**
- 99.9% uptime (OEM standard)
- <0.5% false positive rate
- 50K+ active users
- $500K+ enforcement revenue

### Q4 2027–Q1 2028: Scaling

**Activities:**
1. Expand to 1M+ vehicles (full model year rollout)
2. Add second OEM (Ford or Volvo)
3. Insurance partnerships: 3+ major insurers
4. City API integrations: Toronto, Ottawa, Vancouver

**Success metrics:**
- 1M+ vehicles deployed
- 5M+ potential vehicles under contract
- $3M+ enforcement revenue run-rate
- Zero safety incidents

---

## Technical Challenges & Solutions

### Challenge 1: Bandwidth Efficiency

**Problem:** Uploading video evidence over cellular is expensive. OEMs have monthly data quotas.

**Solution:**
- Compress video: H.264 @ 1 Mbps = 7.5 MB per minute
- Upload only violation clips (10–30 seconds = 75–225 MB per violation)
- Batch uploads: Queue evidence, upload during charging or home Wi-Fi
- Smart compression: Reduce bitrate based on signal strength

**Data budget:** 500 MB/month per vehicle (industry standard) = ~10 violations/month affordable

---

### Challenge 2: Edge Inference Variability

**Problem:** Different OEM platforms have different compute capabilities.

**Solution:**
- Model quantization: 4-bit quantized models run on low-end hardware (Ford, Toyota)
- Tiered inference:
  - **Tier 1 (Tesla, Volvo):** Full TensorFlow models on-device (100 ms/frame)
  - **Tier 2 (Ford, GM):** Quantized models (300 ms/frame)
  - **Tier 3 (Toyota, others):** Cloud inference (1–2 second latency, acceptable for post-event processing)

---

### Challenge 3: OEM Update Cycles vs. CivicMesh Updates

**Problem:** OEMs update vehicle software once per model year. CivicMesh may need updates monthly.

**Solution:**
- **Decoupled updates:** CivicMesh app updates independently (via cellular)
- **OEM approval:** First update per model year requires OEM sign-off; subsequent updates auto-approved
- **Fallback:** If CivicMesh app fails, vehicle operation unaffected (app is sandboxed)

---

### Challenge 4: Privacy in Edge Inference

**Problem:** On-device inference processes video; how to ensure privacy?

**Solution:**
- **Local blurring:** ALPR and face detection happen on-device; only redacted metadata uploaded
- **Hash-based matching:** Plate is hashed (SHA-256) before network transmission
- **Deletion:** Raw frames are deleted immediately after inference (not stored)

---

## Documentation & Feature Registration

Create three files:

1. **docs/OEM_INTEGRATION_STRATEGY.md** — This document
2. **features/market/07_OEM_NATIVE_INTEGRATION.md** — Implementation specs
3. Update **docs/DOCUMENTATION_INDEX.md** and **features/FEATURE_INDEX.md**

---

## Summary

**OEM integration is the 10x multiplier:**

| Model | Vehicles (2028) | Revenue/Year | Time-to-Market |
|-------|-----------------|--------------|-----------------|
| **Retrofit (current)** | 5K–10K | $30M | 5 years |
| **OEM Native (Phase 3)** | 2M–5M | $300M–500M | 3 years |

**Key insight:** One Ford contract (F-Series alone = 700K vehicles/year) = more scale than 10 years of retrofit deployment.

**Competitive moat:** Decentralized (DAO) + driver-friendly revenue (85%/75% split) + deep insurance partnerships = differentiation no traditional fleet management company can match.

**Next step:** Begin partnership discussions with Tesla + Ford (Q1 2027). Proof of concept with Ford SYNC SDK (Q2 2027). Pilot deployment (Q3 2027).

---

**Next:** See [[MULTI_SERVICE_NETWORK_STRATEGY.md]] (ride-sharing, delivery integrations via OEM platforms) and [[GO_TO_MARKET_STRATEGY.md]] (add OEM channel as Tier 0 foundational).
