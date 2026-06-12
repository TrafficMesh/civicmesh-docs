# Privacy & Data Governance Framework

**Comprehensive privacy and consent management for multi-role ecosystem.**

---

## Overview

CivicMesh collects extensive data (dashcam, telematics, sensor fusion) across multiple user roles. This document defines:

1. **Data Governance** — what data is collected, stored, and retained
2. **Role-Based Access Control** — who sees what, when, and why
3. **Consent Management** — how users control their data
4. **Privacy By Design** — anonymization, encryption, audit trails
5. **Regulatory Compliance** — PIPEDA, Ontario HTA, municipal bylaws

---

## Data Inventory

### Collected Data by Source

#### Edge Device (Vehicle Node)
```
COLLECTED:
  • Dashcam video (front/rear, continuous 30fps)
  • OBD telemetry (speed, RPM, heading, fuel, throttle)
  • GPS location (every 1-10 seconds)
  • Radar detections (20 Hz, distance/velocity/angle)
  • IMU acceleration (1000 Hz, x/y/z axes)
  • Camera metadata (timestamps, calibration data)
  • Incident clips (5-10 sec around violation)

STORAGE (Edge):
  • Rolling buffer: 1TB SSD (last 24-48 hours)
  • Evidence buffer: Automatically saved
  • Crash logs: Sensor failures, error traces

PRIVACY:
  ✓ All encrypted at rest (AES-256)
  ✓ No transmission unless incident detected
  ✓ Local processing only (no raw video streaming)
  ✓ Civilian: Data stays on device unless submitted
```

#### Cloud Database (PostgreSQL)
```
STORED (Incidents Only):
  • Violation records (type, timestamp, location, confidence)
  • Incident metadata (device ID, geofence, speed, etc.)
  • Citation records (plate, officer, signature, timestamp)
  • Device telemetry aggregates (NOT raw OBD stream)
  • Digital twin updates (road conditions, not vehicles)
  • User audit logs (officer actions, decisions)

RETENTION:
  • Incidents: 3 years (Ontario statute of limitations)
  • Citations: 7 years (MTO requirement)
  • Device logs: 1 year
  • User audit: 2 years
  • Then: Anonymization or deletion (per PIPEDA)

PRIVACY:
  ✓ NO personal data linked to civilians (unless opted-in)
  ✓ NO location history retained (aggregated only)
  ✓ NO OBD continuous streaming (incident summaries only)
  ✓ Encrypted at rest (AES-256)
  ✓ Encrypted in transit (TLS 1.3)
```

#### Evidence Storage (MinIO)
```
STORED:
  • H.264 video clips (5-10 sec, incident-related)
  • Frame backups (still images for evidence)
  • Metadata logs (timestamps, geofence, sensors)

RETENTION:
  • Active incidents: Until citation issued (or 30 days)
  • Closed incidents: 3 years (statute of limitations)
  • Appeals/disputes: 7 years
  • Then: Secure deletion (NIST guidelines)

PRIVACY:
  ✓ Encrypted with city-controlled key (not platform provider)
  ✓ Access logs (who viewed, when, why)
  ✓ Chain of custody: Immutable audit trail
  ✓ No third-party access without explicit consent
```

---

## Role-Based Access Control (RBAC)

### Data Visibility Matrix

| Data | Officer | Fleet Mgr | City Admin | Civilian | Insurance |
|------|---------|-----------|-----------|----------|-----------|
| **Dashcam (Own)** | All (enforcement) | Own vehicle | None | Own only | Only consented |
| **Dashcam (Others)** | Specific incident only | Own fleet | None | None | None |
| **OBD Telematics (Own)** | Specific incident | Real-time aggregate | None | Own only | Speed + safety only |
| **OBD (Others)** | Incident detail | Fleet summary | None | None | Consented only |
| **GPS Location (Own)** | Incident location only | Real-time (own fleet) | Aggregated heatmaps | Own trips | Never |
| **GPS (Others)** | None | Fleet routes (work) | Anonymized | None | None |
| **Driver Identity** | Yes (enforcement) | Own drivers | Aggregated | Own | Consented only |
| **Plate Number** | Yes (citation) | Own vehicles | None | Reported incidents | None |
| **Incident Location** | Yes (enforcement) | Own vehicles | Aggregated | Reported | Consented location |
| **Speed Data** | Yes (citation) | Aggregate + coaching | Heatmaps | Own + reported | Consented speed |
| **Violation Type** | Yes | Yes (fleet coaching) | Aggregated | Own incidents | Risk profile only |
| **Video Evidence** | Yes (incident) | Own vehicles only | None | Own submitted | None |
| **Audit Logs** | Own actions | Fleet actions | Own system | Own account | Access logs only |
| **Citation History** | Yes (enforcement) | Employees | Anonymized | Reported violations | Risk scoring |

### Data Access Controls by Role

#### Officer (Law Enforcement)
```
ACCESS LEVELS:
  • Own assigned incident: Full (video, telematics, location)
  • Any incident in jurisdiction: Metadata only (type, location, time)
  • Any vehicle: Only during active enforcement
  • Personal data: NOT visible (plate → person via MTO only)

RESTRICTIONS:
  ✗ Cannot access civilian telematics
  ✗ Cannot download raw video (only on-screen viewing)
  ✗ Cannot share incident with public (except official report)
  ✗ Cannot access other officers' notes (privacy)
  ✗ Audit logged: All access recorded + timestamp + reason

CONSENT REQUIRED:
  ✓ To view civilian-submitted evidence (implicit via submission)
  ✓ To contact driver (via MTO, not direct)
```

#### Fleet Operator (OC Transpo, City)
```
ACCESS LEVELS:
  • Own fleet vehicles: Real-time telematics + video
  • Own drivers: Performance metrics (speed, acceleration, braking)
  • Own routes: Aggregated congestion, traffic patterns
  • Own maintenance: Vehicle health, vibration analysis

RESTRICTIONS:
  ✗ Cannot access other fleets' data
  ✗ Cannot see civilian vehicle data
  ✗ Cannot download continuous video (only incident clips)
  ✗ Cannot override privacy settings
  ✗ All access logged + purpose tracked

DRIVER COACHING:
  ✓ "You hard-braked 5 times today" (aggregate, not tracking)
  ✓ "Route 7 average speed: 32 km/h" (fleet, not individual)
  ✓ "Fuel efficiency up 8%" (performance trend)
  ✗ Not location tracking (routes visible, specific locations aggregate)
```

#### City Administrator (Planning, Policy)
```
ACCESS LEVELS:
  • Violation heatmaps (anonymized location density)
  • Infrastructure intelligence (road conditions, signals, signs)
  • Traffic patterns (volume, speed, congestion by corridor)
  • Vision Zero data (collision data, aggregated)
  • Parking patterns (occupancy, turnover by curb segment)

RESTRICTIONS:
  ✗ Cannot see individual driver identity
  ✗ Cannot see vehicle locations (aggregate only)
  ✗ Cannot see telematics (only safety metrics)
  ✗ Cannot access raw video (heatmaps only)
  ✗ Cannot contact drivers or vehicles directly

DATA ANONYMIZATION:
  ✓ Heatmaps: Violation count per zone (no individual data)
  ✓ Traffic patterns: Corridor-level aggregates
  ✓ Time-of-day analysis: Rush hour vs. off-peak
  ✓ Seasonal trends: Month-over-month patterns
```

#### Civilian Participant (Phase 3)
```
ACCESS LEVELS:
  • Own dashcam footage: 24-hour rolling buffer
  • Own telematics: Speed, RPM, fuel, throttle
  • Own submitted incidents: Video + metadata
  • Own earnings: Per-incident rewards, monthly totals
  • Own insurance profile: Risk score, shared data status
  • Own driving record: Violations observed/reported

RESTRICTIONS:
  ✗ Cannot access other drivers' data
  ✗ Cannot see incident data (deleted after 30 days unless submitted)
  ✗ Cannot download others' submissions
  ✗ Cannot see city heatmaps (aggregate only)
  ✗ Cannot modify consent after submission (immutable)

CONSENT CONTROLS:
  ✓ Granular sharing: Select which data to share with insurance
  ✓ Time-limited: "Share last 90 days" or "Share 12 months"
  ✓ Type-limited: "Share speed data only" (not location)
  ✓ Revocable: Can stop sharing anytime (future data only)
  ✓ Transparent: See exactly what's been shared, when, to whom
  ✓ Delete request: Request permanent deletion (legal compliance)
```

#### Insurance Partner
```
ACCESS LEVELS (Consented Data Only):
  • Driver risk profile: Multi-sensor safety metrics
  • Violation history: Speeding, aggressive driving (12 months)
  • Collision risk: Near-miss frequency, dangerous corridors
  • Claims data: Video evidence for filed claims
  • Corridor risk: Heatmaps of high-incident areas
  
RESTRICTIONS:
  ✗ Cannot access non-consented drivers
  ✗ Cannot access location history (speed + behavior only)
  ✗ Cannot see personal telematics (aggregated metrics)
  ✗ Cannot access video except for claims validation
  ✗ Cannot re-share data with third parties
  ✗ Cannot use data for discriminatory purposes
  ✗ All access logged + audited quarterly

CONSENT REQUIREMENT:
  ✓ EXPLICIT opt-in per driver (not default)
  ✓ SPECIFIC: "Share speed data for 90 days"
  ✓ REVOCABLE: Can stop at any time
  ✓ TRANSPARENT: See exact benefit (e.g., "9% discount")
```

---

## Consent Management

### Consent Model (Civilian Phase 3)

#### Consent Tiers

**Tier 1: Basic Participation**
```
What you're sharing:
  ✓ Incident video (only when you submit)
  ✓ Incident location (to prevent duplicates)
  ✓ Incident type (what violation was observed)

What happens:
  ✓ CivicMesh verifies video
  ✓ Officers review + decide
  ✓ You earn reward if approved
  ✓ Video deleted after 30 days (no approval)

Default: This tier is OPT-IN (you choose)
Consent: Implicit on first submission
Revocation: Can stop anytime (future submissions only)
```

**Tier 2: Insurance Integration**
```
What you're sharing:
  ✓ Speed data (last 90 days)
  ✓ Aggressive driving score
  ✓ Violation history (not location)
  ✓ Collision risk metrics

What happens:
  ✓ Insurance scores your risk
  ✓ Determines premium discount (e.g., 9%)
  ✓ Data retained for 12 months
  ✓ You see exact benefit + can opt-out anytime

Default: OPT-IN (you choose)
Consent: Explicit per insurer (e.g., "Share with TD Insurance")
Revocation: Stop sharing anytime (data deleted after 90 days)
Duration: You control ("Share 12 months" or "Share 6 months")
```

**Tier 3: Location Tracking** (NOT Default)
```
What you're sharing:
  ✓ GPS coordinates (all trips)
  ✓ Time of day patterns
  ✓ Frequented locations
  ✓ Commute routes

When this is used:
  ✗ NOT for enforcement against you
  ✗ NOT for third-party marketing
  ✗ ONLY for route optimization + hazard warnings
  
Why you'd opt-in:
  ✓ "Avoid this pothole" warnings
  ✓ "Route 7 congested, try Highway 403" suggestions
  ✓ Emergency location (911 auto-dispatch)

Default: OPT-OUT (you must explicitly enable)
Consent: Explicit per use case
Duration: Can limit ("While in Ontario only")
Revocation: Immediate deletion from active systems
```

### Consent Management UI (Civilian App)

```
┌─ Privacy & Consent Center ────────────────┐
│                                            │
│  Your Data Sharing Status                  │
│                                            │
│  ┌─ Basic Participation ─────────────────┐│
│  │ Status: ACTIVE                         ││
│  │ You're sharing: Incident videos        ││
│  │ Earning: $0.50–$100 per submission    ││
│  │ Data retention: 30 days (auto-delete) ││
│  │ [Stop Sharing] [Learn More]            ││
│  └────────────────────────────────────────┘│
│                                            │
│  ┌─ Insurance Integration ───────────────┐│
│  │ Status: ACTIVE (TD Insurance)          ││
│  │ You're sharing: Speed + safety metrics ││
│  │ Duration: Last 90 days                 ││
│  │ Your benefit: 9% discount ($540/year) ││
│  │ [Stop Sharing] [View Your Risk Score] ││
│  │ [Switch Insurers] [Manage Duration]    ││
│  └────────────────────────────────────────┘│
│                                            │
│  ┌─ Location Tracking ───────────────────┐│
│  │ Status: INACTIVE                       ││
│  │ Enable for: Route optimization + hazard ││
│  │              warnings                  ││
│  │ Privacy: Only aggregated patterns used ││
│  │ [Enable] [Learn More]                  ││
│  └────────────────────────────────────────┘│
│                                            │
│  ┌─ Data Access Audit ───────────────────┐│
│  │ Who has accessed YOUR data:            ││
│  │                                        ││
│  │ Officer #4521: June 1, 14:32           ││
│  │   Reason: Speeding enforcement (King)  ││
│  │   Data: Video, location, speed         ││
│  │                                        ││
│  │ TD Insurance: June 3, 08:15            ││
│  │   Reason: Risk profile update          ││
│  │   Data: Speed, driving behavior        ││
│  │                                        ││
│  │ [Download Full Audit]                  ││
│  └────────────────────────────────────────┘│
│                                            │
│  ┌─ Delete My Data ──────────────────────┐│
│  │ Request permanent deletion:             ││
│  │ ⚠️  This will delete:                  ││
│  │   • Past submissions (cannot undo)     ││
│  │   • Insurance history (affects score)  ││
│  │   • Earnings record (no payout)        ││
│  │   • Account (cannot rejoin)            ││
│  │                                        ││
│  │ [Request Deletion] [Download My Data]  ││
│  └────────────────────────────────────────┘│
│                                            │
└────────────────────────────────────────────┘
```

---

## Privacy By Design

### Encryption

#### In Transit
```
API Endpoints:
  ✓ TLS 1.3 (minimum)
  ✓ Perfect forward secrecy (ephemeral keys)
  ✓ Certificate pinning (prevent MITM)

Evidence Upload:
  ✓ HTTPS + TLS 1.3
  ✓ Chunk-based upload (5MB chunks)
  ✓ Each chunk encrypted with AES-256-GCM
  ✓ Integrity check (HMAC-SHA256)

WebSocket (Real-Time):
  ✓ WSS (WebSocket Secure)
  ✓ TLS 1.3 + message-level encryption
```

#### At Rest
```
Database:
  ✓ Transparent Data Encryption (TDE)
  ✓ Column-level encryption for PII:
    - Driver plate numbers
    - Officer identities
    - Civilian names
  ✓ Key management: Dedicated KMS (HashiCorp Vault)
  ✓ Key rotation: Quarterly

Evidence Storage (MinIO):
  ✓ AES-256 object encryption
  ✓ Key stored separately (city-controlled)
  ✓ Bucket encryption enabled
  ✓ No key derivation from passwords

Logs:
  ✓ Sensitive data redacted (PII, keys)
  ✓ Encrypted at rest
  ✓ Separate access control (audit team only)
```

### Anonymization

#### Data Aggregation
```
Individual → Aggregate:
  Location:    43.6630, -79.3960 → Zone "King & York"
  Driver:      "Jane Smith" → Anonymized ID "D_8234"
  Time:        14:32:15 June 1 → "Morning rush (8am-10am)"
  Speed:       48 km/h → "Corridor avg: 45 km/h"
  
Heatmaps:
  ✓ Only counts shown (no individual incidents)
  ✓ Zone-level granularity (not block-level)
  ✓ Time-aggregated (hourly, not per-incident)
  ✓ No driver identification possible

Public Reports:
  ✓ "57 speeding violations on King St this month"
  ✗ Not: "License plate ABC1234 on June 1"
  ✓ "Speed violations up 12% during rush hour"
  ✗ Not: "Officer Smith issued 23 citations"
  ✓ "Residential zones: 4% violation rate"
  ✗ Not: "School zone near [specific address]"
```

#### Differential Privacy
```
When reporting statistics, add small random noise:
  True: "45 violations this week in Zone A"
  Report: "43-47 violations this week in Zone A" (±5% noise)
  
Benefit: Cannot identify specific incidents from aggregates
Privacy: Individual incidents indistinguishable from noise
```

### Audit Logging (Immutable)

```
Every data access logged:
  ✓ WHO (user ID, role, badge number)
  ✓ WHAT (data accessed, incident ID)
  ✓ WHEN (timestamp, ±1 sec precision)
  ✓ WHERE (system, IP address)
  ✓ WHY (authorization reason)
  ✓ RESULT (successful, denied, or error)

Immutability:
  ✓ Write-once database (append-only)
  ✓ Cryptographic hash chain (tamper-detection)
  ✓ Quarterly archival (100-year retention)
  ✓ SIEM alerts (unauthorized access)

Civilian View:
  ✓ "Officer #4521 viewed your speeding incident (June 1, 14:32)"
  ✓ "TD Insurance queried your risk profile (June 3, 08:15)"
  ✓ "System maintenance (database backup, June 2, 02:00)"
```

---

## Regulatory Compliance

### PIPEDA (Canada's Privacy Law)

#### Data Collection
```
Requirement: Collect only what's needed for stated purpose
CivicMesh:
  ✓ Purpose: Traffic enforcement, city safety intelligence
  ✓ Collects: Video, telemetry, location (for enforcement)
  ✗ Does NOT collect: Age, gender, income, medical data
  ✓ Transparent: Privacy notice on every device
```

#### Consent
```
Requirement: Explicit consent for data collection
CivicMesh:
  ✓ Fleet: Employer consent (OC Transpo)
  ✓ Civilian: Explicit opt-in per tier
  ✓ Insurance: Explicit opt-in (not default)
  ✓ Revocable: Can stop sharing anytime
```

#### Access & Correction
```
Requirement: Individuals can access + correct their data
CivicMesh:
  ✓ Citizens can download their own data (reports, submissions)
  ✓ Can request correction (if error found)
  ✓ Can request deletion (GDPR-style "right to be forgotten")
  ✓ 30-day response time (PIPEDA requirement)
```

#### Retention & Deletion
```
Requirement: Keep data only as long as needed
CivicMesh:
  ✓ Incidents: 3 years (statute of limitations)
  ✓ Citations: 7 years (MTO record)
  ✓ Device logs: 1 year
  ✓ Then: Anonymization or deletion
  ✓ Audit logs: Encrypted, 2-year retention
```

### Ontario Highway Traffic Act (HTA)

#### Speed Enforcement
```
Requirement: Radar must be certified + operated by trained officer
CivicMesh:
  ✓ Radar: Factory-calibrated, certified annually
  ✓ Officers: Trained + tested on citation law
  ✓ Evidence: Video + radar + geofence + speed verification
  ✓ Due process: Manual review before citation (human judgment)
  ✓ Appeal: Video evidence provided to accused
```

#### Chain of Custody
```
Requirement: Evidence must be tamper-proof + attributable
CivicMesh:
  ✓ Incident timestamp + device ID + officer
  ✓ Video encryption + signature verification
  ✓ Immutable audit trail (who viewed, when, why)
  ✓ Court-admissible (following Canadian evidence law)
  ✓ Officer signature (digital signature, timestamped)
```

### Municipal Bylaws

#### Privacy Impact Assessment (PIA)
```
Required: Before deploying in new city
CivicMesh:
  ✓ Public consultation (community transparency)
  ✓ Privacy assessment (data minimization analysis)
  ✓ Risk mitigation (encryption, anonymization, access control)
  ✓ Oversight board (citizen review of decisions)
  ✓ Annual audit (independent privacy review)
```

---

## Implementation Examples

### Example 1: Officer Reviewing Speeding Incident

```
Officer John (#4521) logs in
  ↓
System checks: Is this incident assigned to John? YES
  ↓
System checks: Is this a speeding incident? YES (authorized)
  ↓
Load incident:
  ✓ Video (encrypted, decryption at server, streamed to officer)
  ✓ Telematics (speed, location, time)
  ✓ Geofence (speed limit for zone)
  ✓ Radar confidence (97%)
  ✗ Driver name (NOT shown, only plate via MTO)
  ✗ Civilian telematics (NOT shown)
  
Officer approves citation
  ↓
Citation logged:
  ✓ Officer ID, time, digital signature
  ✓ Incident ID, violation type
  ✓ Audit trail entry (immutable)
  
Audit shows:
  "Officer #4521 viewed speeding incident (device: Bus #456, 
   timestamp: June 1 14:32, location: King & York). 
   Approved citation at 14:35."
```

### Example 2: Civilian Submitting Evidence + Insurance Integration

```
Civilian Jane records speeding incident
  ↓
Video stored locally (encrypted, rolling 24-hour buffer)
  ↓
Jane reviews video on her phone
  ↓
Jane submits evidence to CivicMesh
  ├─ Consent: Grants video + incident location
  ├─ Data: Video encrypted with city key, sent via HTTPS
  ├─ Server: Verifies signature, checks for duplicates
  └─ Reward: Email confirmation "$50 approved, pending payout"
  
Jane's risk profile updated
  ├─ Now shows: "1 violation reported"
  ├─ Safety score: 94/100
  └─ Sent to insurance (TD Insurance, consented)
  
Insurance query: "What's Jane's risk profile?"
  ├─ CivicMesh checks: Is Jane sharing data with TD? YES
  ├─ CivicMesh checks: Is request from TD? YES (OAuth verified)
  ├─ Returns: {safety_score: 94, violations_12mo: 1, 
              aggressive_driving: "low", collisions: 0}
  ├─ TD decides: "9% discount for Jane ($540/year)"
  └─ Jane sees: "Your discount increased to 9%"
  
Jane can stop sharing anytime
  ├─ "Manage Sharing" → Stop sharing with TD
  ├─ Future data: Deleted from TD
  ├─ Past data: Retained for claims (cannot undo)
  └─ Effect: Discount revoked next renewal
```

### Example 3: City Planner Analyzing Heatmaps (Privacy-Preserving)

```
City planner accesses violation heatmaps
  ↓
Sees: Heat map (red zones = high violation density)
  ├─ King St corridor: 247 violations this month
  ├─ Bloor Ave: 89 violations
  ├─ Residential zones (avg): 12 violations
  
Planner sees: Time-of-day pattern
  ├─ Rush hours (7-9am, 4-7pm): 60% of violations
  ├─ Night (11pm-6am): 5% of violations
  
Planner cannot see:
  ✗ Individual driver identity
  ✗ Specific vehicle locations
  ✗ Personal telematics data
  ✗ Any personally identifiable information
  
Planner exports report:
  ├─ "Speeding on King St up 12% (June vs. May)"
  ├─ "Recommendation: Increase enforcement, or reduce speed limit"
  ├─ Data: Anonymized (no PII possible to reverse-engineer)
  ├─ Public: Report shared with city council + media
  └─ Privacy: Zero privacy risk (aggregate data only)
```

---

## Privacy Controls Dashboard (All Roles)

### Officer Privacy Dashboard
```
┌─ My Data Access ──────────────────────────┐
│ Your access is logged + audited            │
│                                            │
│ Your Role: Enforcement Officer             │
│ Authorized Access:                         │
│  ✓ Assigned incidents (full data)          │
│  ✓ Any incident in jurisdiction (metadata) │
│  ✓ Vehicle data (during enforcement)       │
│                                            │
│ Restricted Access:                         │
│  ✗ Civilian telematics                     │
│  ✗ Personal data (beyond MTO plate lookup) │
│  ✗ Raw video download                      │
│  ✗ Other officers' personal notes          │
│                                            │
│ [View My Access Logs] [Privacy Training]   │
│                                            │
│ Last access: 47 incidents (June 1-7)       │
│ Compliance: 100% (no unauthorized access)  │
└────────────────────────────────────────────┘
```

### Fleet Manager Privacy Dashboard
```
┌─ Fleet Data Control ──────────────────────┐
│ What you can access:                       │
│  ✓ Own fleet vehicles (real-time)          │
│  ✓ Driver performance (aggregate)          │
│  ✓ Vehicle health metrics                  │
│  ✗ Employee personal data (beyond metrics) │
│  ✗ Specific location tracking              │
│  ✗ Other fleets' data                      │
│                                            │
│ Driver Privacy Policy:                     │
│  ✓ Metrics shown: Speeds, acceleration    │
│  ✗ Metrics hidden: Home address, routines  │
│  ✓ Coaching: "You hard-braked 5x today"   │
│  ✗ Coaching: "You went to X address"      │
│                                            │
│ Data Retention:                            │
│  • Real-time metrics: 24 hours             │
│  • Daily aggregates: 90 days               │
│  • Monthly summaries: 1 year               │
│                                            │
│ [Audit Driver Coaching] [Privacy Policy]   │
└────────────────────────────────────────────┘
```

---

## Summary

**CivicMesh Privacy Framework:**

1. **Minimize collection** — Collect only what's needed for enforcement + city intelligence
2. **Encrypt everything** — AES-256 in transit + at rest
3. **Anonymize aggregates** — Public data shows counts, not individuals
4. **Control access** — Role-based, logged, auditable
5. **Respect consent** — Explicit opt-in, granular controls, revocable
6. **Enable transparency** — Civilians see who accessed their data, when, why
7. **Comply with law** — PIPEDA, HTA, municipal bylaws
8. **Audit everything** — Immutable logs, quarterly independent review

**Result:** A privacy-respecting system that gives officers + planners the data they need, respects civilian privacy, and complies with Canadian law.

