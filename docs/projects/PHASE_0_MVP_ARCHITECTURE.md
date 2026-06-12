# CivicMesh Phase 0 MVP Architecture

**OC Transpo Traffic Enforcement Pilot: Evidence Ingestion → Deduplication → Officer Review → Citation Issuance**

Document Version: 1.0  
Last Updated: 2026-06-05  
Scope: Phase 0 (OC Transpo Pilot, Q2–Q3 2026)

---

## Quick Links to Hardware BOM

**Phase 0 nodes are fully specced:**
- [NODE_BOM.md](../hardware/bom/NODE_BOM.md) — Complete node BOM ($485–600/unit for 50 buses)
- [DASHCAM_SPECS.md](../hardware/bom/DASHCAM_SPECS.md) — Camera selection & lens calibration
- [POWER_MANAGEMENT.md](../hardware/bom/POWER_MANAGEMENT.md) — OBD-II power, 5V regulation, battery backup

**Procurement timeline:** 4–6 weeks at volume, contract assembly available in Ontario.

---

## Table of Contents

1. [System Overview](#system-overview)
2. [Architecture Diagram](#architecture-diagram)
3. [Core Components](#core-components)
4. [Data Flow](#data-flow)
5. [Technology Stack](#technology-stack)
6. [Deployment Model](#deployment-model)
7. [Security & Compliance](#security--compliance)
8. [Future Phases Integration Points](#future-phases-integration-points)

---

## System Overview

CivicMesh Phase 0 is a **distributed traffic enforcement platform** designed to:

- **Detect violations** via cameras + sensors on OC Transpo buses
- **Ingest evidence** (video + metadata) to a centralized backend
- **Deduplicate** incidents (same car, same location, same time → count once)
- **Route intelligently** based on detection confidence
- **Enable officer review** via web portal (humans-in-the-loop)
- **Issue citations** (Notice of Contravention) with full chain-of-custody audit trail

### Success Criteria (Phase 0)

| Metric | Target | Rationale |
|--------|--------|-----------|
| **Buses Deployed** | 5–10 initial, scaling to 50 | Pilot validation on live OC Transpo routes |
| **Violations Detected/Day** | 100–500 | ~3 violations per bus per day |
| **Officer Review Time** | <2 min per incident | Efficiency target for scalability |
| **False Positive Rate** | <5% | Quality threshold for legal defensibility |
| **System Uptime** | 99%+ | Municipal service reliability expectation |
| **Monthly Revenue** | $310K+ (50 buses) | Coverage of operational costs |

---

## Architecture Diagram

```
┌──────────────────────────────────────────────────────────────────────┐
│                        CIVICMESH PHASE 0 ARCHITECTURE                │
└──────────────────────────────────────────────────────────────────────┘

┌─────────────────┐
│   EDGE LAYER    │
│  (OC Transpo    │
│    Buses)       │
├─────────────────┤
│ • Front Camera  │
│ • OBD (Speed)   │ ──┐
│ • GPS/IMU       │   │
│ • Edge Compute  │   │
│   (RPi + AI)    │   │
└─────────────────┘   │
                      │ HTTP POST
                      │ {metadata + H.264 clip}
                      │ HMAC-SHA256 signed
                      ▼
┌─────────────────────────────────────┐
│    FASTAPI BACKEND (Port 8000)      │
├─────────────────────────────────────┤
│                                     │
│  POST /incidents/submit             │
│  ├─ Verify node signature (HMAC)    │
│  ├─ Upload clip to MinIO            │
│  ├─ PostGIS deduplication           │
│  ├─ Classify violation type         │
│  ├─ Score confidence                │
│  ├─ Route to queue                  │
│  └─ Append audit log (immutable)    │
│                                     │
│  GET /portal/queue                  │
│  GET /portal/incidents/{id}         │
│  POST /portal/incidents/{id}/approve│
│  POST /portal/incidents/{id}/reject │
│  POST /portal/incidents/{id}/escalate
│                                     │
└─────────────────────────────────────┘
        │                   │
        ▼                   ▼
┌──────────────────┐  ┌──────────────────┐
│  PostgreSQL      │  │   MinIO (S3)     │
│  + PostGIS       │  │                  │
├──────────────────┤  ├──────────────────┤
│ • incidents      │  │ • Evidence clips │
│ • review_queues  │  │   (H.264 video)  │
│ • citations      │  │ • bucket: civicmesh
│ • audit_log      │  │ • path: incidents/{uuid}
│ • devices        │  │                  │
│ • device_secrets │  │                  │
│ • officer_sessions
│ • officer_actions
└──────────────────┘  └──────────────────┘

        │
        ├─────────────────────────┐
        │                         │
        ▼                         ▼
┌──────────────────┐     ┌──────────────────┐
│     REDIS        │     │    RabbitMQ      │
│  (Cache Layer)   │     │ (Message Queue)  │
├──────────────────┤     ├──────────────────┤
│ • Dedup cache    │     │ • Phase 1+       │
│ • Rate limiting  │     │   (async jobs)   │
│ • Sessions       │     │                  │
└──────────────────┘     └──────────────────┘

┌──────────────────────────────────────┐
│   OFFICER PORTAL (React, Phase 1)    │
├──────────────────────────────────────┤
│ • Queue summary                      │
│ • Incident detail + video playback   │
│ • Approval/rejection workflow        │
│ • Escalation to supervisor           │
└──────────────────────────────────────┘
```

---

## Core Components

### 1. Edge Node (OC Transpo Bus)

**Hardware:** Raspberry Pi CM4 + Dashcam + OBD Dongle + GPS + Cellular  
**Firmware:** (Phase 1 implementation)

**Responsibilities:**
- Capture video at 30 fps from front + rear cameras
- Poll OBD at 10 Hz for vehicle speed (most reliable source)
- Record GPS location at 5 Hz
- Run edge AI inference locally (ALPR, lane detection, signal recognition)
- Detect violations with confidence scoring
- Sign evidence package with node HMAC secret
- Upload to backend via LTE (or depot Wi-Fi fallback)

**Security:**
- HMAC-SHA256 signature prevents tampering
- Local buffering survives connectivity drops (ring buffer, ~50 MB)
- Evidence files encrypted during transit (TLS 1.3)

---

### 2. Backend Ingestion Pipeline

**Framework:** FastAPI + asyncpg + SQLAlchemy  
**Concurrency Model:** Async/await (handle 50 buses uploading simultaneously)

**8-Step Processing:**

| Step | Action | Purpose |
|------|--------|---------|
| 1 | Verify HMAC signature | Authenticate node (prove ownership of secret) |
| 2 | Validate confidence | Reject invalid data (0.0–1.0) |
| 3 | Upload evidence to MinIO | Store clip for officer review |
| 4 | PostGIS deduplication | Same plate + location + time → 1 incident |
| 5 | Classify violation type | Normalize raw types (bus_lane, red_light, etc.) |
| 6 | Score confidence | Route by reliability (95%+ fast-track, 80–95% manual, <80% discard) |
| 7 | Insert into incidents table | Create evidence record |
| 8 | Append audit log | Immutable chain-of-custody |

**Output:** `{status: "accepted", incident_id: UUID, queue_type: "fast_track"|"manual_review"}`

---

### 3. Deduplication Engine

**Technology:** PostGIS spatial indexing (PostgreSQL GiST)

**Query Logic:**
```sql
SELECT * FROM incidents
WHERE plate = %s 
  AND violation_type = %s
  AND ST_DWithin(location, POINT(lat, lon), 50)    -- 50m radius
  AND timestamp > NOW() - INTERVAL '5 minutes'
  AND status IN ('queued', 'confirmed')
LIMIT 1
```

**Decision Rules:**

| Scenario | Decision | Action |
|----------|----------|--------|
| No match | UNIQUE | Insert new incident |
| Match + new_confidence > existing | DUPLICATE_HIGHER | Update existing with new confidence |
| Match + new_confidence ≤ existing | DUPLICATE_LOWER | Discard new, keep existing |

**Purpose:** 
- Prevents billing same violator twice for one incident
- Improves confidence by combining evidence (picks highest)
- Saves officer review time (don't see duplicates in queue)

---

### 4. Confidence-Based Queue Routing

**Phase 0 Thresholds (Bus Lane & Red Light):**

| Queue Type | Confidence | Officer Workflow | Volume |
|------------|------------|------------------|--------|
| **fast_track** | ≥95% | 30-second spot-check | 60% |
| **manual_review** | 80–95% | Watch 1–2 min clip | 35% |
| **discard** | <80% | Not stored in DB | 5% |

**Rationale:**
- **Fast-track (95%+):** High confidence violations need minimal review time
- **Manual (80–95%):** Medium confidence requires human judgment
- **Discard (<80%):** Low quality detections waste officer time; don't store

**Phase 1+ Extensions:**
- Speeding: 99%+ (OBD is ground truth, highest standard)
- Lane violations: 90%+ (computer vision less reliable than speed)

---

### 5. Officer Portal API

**Endpoints:**

1. **GET /api/v1/portal/queue** (Summary)
   - Returns queue counts by type
   - Officer sees how many waiting in each queue

2. **GET /api/v1/portal/incidents/{id}** (Detail)
   - Returns incident metadata + presigned S3 URL for video
   - Officer can stream evidence clip in browser

3. **POST /api/v1/portal/incidents/{id}/approve** (Approval)
   - Lock incident (5-min lock prevents double-approval)
   - Create citation record (NOC document)
   - Append audit log: event='approved', actor=badge_number
   - Return citation ID + NOC hash

4. **POST /api/v1/portal/incidents/{id}/reject** (Rejection)
   - Update status to 'rejected'
   - Record reason in audit log
   - Unlock queue entry (make available for another officer)

5. **POST /api/v1/portal/incidents/{id}/escalate** (Escalation)
   - Move to 'escalation' queue
   - Supervisor can approve/reject

**Authentication (Phase 0):** Badge number in query param (no JWT yet)  
**Authentication (Phase 1):** JWT tokens + officer_sessions table

---

### 6. Database Schema

**4 Core Tables:**

#### **incidents**
Stores all evidence packages. Central fact table.
```
incident_id (UUID)
node_id (VARCHAR) ──────────┐
violation_type (VARCHAR)    │
plate (VARCHAR)             ├─→ Indexed for dedup queries
location (GEOMETRY)         │
timestamp (TIMESTAMPTZ)     │
evidence_s3_url (TEXT) ─────┘
confidence (FLOAT 0–1)
status (VARCHAR) ──────────→ 'queued', 'confirmed', 'rejected', 'escalated', 'duplicate'
created_at (TIMESTAMPTZ)
```

#### **review_queues**
Routes incidents to officers based on confidence.
```
queue_id (UUID)
queue_type (VARCHAR) ──────→ 'fast_track', 'manual_review', 'escalation'
incident_id (UUID FK) ─────→ Ref to incidents.incident_id
assigned_officer_id (VARCHAR) ──→ NULL until officer locks
locked_until (TIMESTAMPTZ) ──→ 5-min lock for review exclusivity
created_at (TIMESTAMPTZ)
```

#### **citations**
Officer-approved violations (Notice of Contravention). 1-to-1 with approved incidents.
```
citation_id (UUID)
incident_id (UUID FK)
officer_id (VARCHAR)
badge_number (VARCHAR)
noc_document_hash (TEXT) ──→ Blake3 hash of NOC (tamper-proof)
signed_at (TIMESTAMPTZ)
mto_ticket_number (VARCHAR) ──→ Populated when sent to provincial system
status (VARCHAR) ──────────→ 'issued', 'paid', 'disputed', 'appealed'
created_at (TIMESTAMPTZ)
```

#### **audit_log** (Immutable, append-only)
Chain-of-custody for legal proceedings.
```
log_id (UUID)
incident_id (UUID FK)
event_type (VARCHAR) ──────→ 'uploaded', 'deduped', 'queued', 'approved', 'rejected', 'escalated'
actor (VARCHAR) ───────────→ node_id or officer_id
timestamp (TIMESTAMPTZ)
metadata (JSONB) ──────────→ Structured context (confidence, reason, etc.)
hmac_hash (TEXT) ──────────→ Cryptographic signature
```

**Immutability Enforcement:** PostgreSQL trigger prevents DELETE on audit_log
```sql
CREATE TRIGGER audit_log_immutable
BEFORE DELETE ON audit_log
FOR EACH ROW
EXECUTE FUNCTION prevent_audit_log_delete();
```

---

### 7. Evidence Storage (MinIO/S3)

**Bucket:** `civicmesh`  
**Key Structure:** `incidents/{uuid[:8]}/{uuid}.mp4`  
**Content:** H.264 video clips (~500 KB per 10-second clip)

**Presigned URLs:** Officers get time-limited URLs to stream evidence

**Retention Policy:** 3 years (Ontario statute of limitations), then delete or archive

---

## Data Flow

### Complete Incident Lifecycle

```
1. DETECTION (Edge Node, Bus 501)
   ├─ Camera detects object in bus lane (YOLO confidence 0.97)
   ├─ Sign metadata + video with HMAC-SHA256
   └─ POST /incidents/submit (with evidence clip)

2. INGESTION (Backend Pipeline, T=0–0.5s)
   ├─ Verify node signature (HMAC-SHA256)
   ├─ Validate confidence (0.0–1.0)
   ├─ Upload clip to MinIO
   ├─ PostGIS dedup query (same plate + location + time)
   ├─ Classify violation type (ViolationType.BUS_LANE)
   ├─ Score confidence (0.97 ≥ 0.95 → FAST_TRACK)
   ├─ INSERT into incidents table
   ├─ INSERT into review_queues (queue_type='fast_track')
   ├─ APPEND to audit_log (event='uploaded')
   └─ RETURN 200 {accepted, incident_id, queue_type}

3. OFFICER REVIEW (Portal, T=2–5 min)
   ├─ Officer Sally checks queue (15 incidents in fast_track)
   ├─ GET /portal/incidents/{id} → gets details + presigned S3 URL
   ├─ Watches 10-second video clip (clearly a violation)
   └─ Decides: "Approve"

4. APPROVAL & CITATION (Backend, T=5 min)
   ├─ POST /portal/incidents/{id}/approve
   ├─ Lock incident (assigned_officer_id=OCA123, locked_until=+5min)
   ├─ UPDATE incidents SET status='confirmed'
   ├─ Generate NOC document (plate, violation, timestamp, officer badge)
   ├─ Blake3 hash of NOC (tamper-proof proof)
   ├─ INSERT into citations table
   ├─ APPEND to audit_log (event='approved', actor=OCA123)
   └─ RETURN 200 {status: approved, citation_id, noc_hash}

5. ACCOUNTABILITY & APPEALS (Phase 1+)
   ├─ Violator receives NOC (digital or printed)
   ├─ Can view evidence via public portal (with redactions)
   ├─ Can appeal to supervisor (POST /escalate)
   ├─ Supervisor reviews appeal, approves or denies
   ├─ All decisions logged immutably in audit_log
   └─ Escalation to MTO/provincial system (Phase 1)
```

---

## Technology Stack

| Layer | Technology | Version | Purpose |
|-------|-----------|---------|---------|
| **Language** | Python | 3.11 | Backend logic, data models |
| **Web Framework** | FastAPI | 0.111.0 | HTTP API endpoints |
| **ASGI Server** | Uvicorn | 0.30.1 | Production-ready ASGI server |
| **Database (SQL)** | PostgreSQL | 16 | Transactional consistency, ACID |
| **Geospatial (PostGIS)** | PostGIS | 3.4 | Deduplication via ST_DWithin |
| **Async ORM** | SQLAlchemy | 2.0 | Async query builder |
| **Async DB Driver** | asyncpg | 0.29.0 | PostgreSQL async driver |
| **Object Storage** | MinIO | latest | S3-compatible evidence storage |
| **Cache** | Redis | 7 | Dedup cache, session store |
| **Message Queue** | RabbitMQ | 3.13 | Phase 1+ async processing |
| **Cryptography** | blake3 | 0.4.1 | Immutable hash function |
| **Testing** | pytest | 8.2.2 | Unit test framework |
| **Containerization** | Docker | latest | Reproducible deployments |
| **Orchestration** | Kubernetes | (Phase 1) | Multi-city MSSP scaling |

---

## Deployment Model

### Phase 0: Local Development + Single Deployment

**Environment:** Docker Compose (single machine)

```bash
docker compose up -d
# Brings online:
# - PostgreSQL (port 5432)
# - MinIO (port 9000 API, 9001 web console)
# - Redis (port 6379)
# - RabbitMQ (port 5672 AMQP, 15672 web console)
# - FastAPI (port 8000)
```

**Production Deployment:** Single Linux VM with docker-compose
- 4 vCPU, 8 GB RAM minimum
- Persistent volumes for PostgreSQL + MinIO
- Health checks + auto-restart

---

### Phase 1: Kubernetes MSSP

**Each city gets its own namespace** (data isolation):
```
civicmesh-prod/
├─ toronto/
│  ├─ PostgreSQL (StatefulSet)
│  ├─ MinIO (StatefulSet)
│  ├─ FastAPI (Deployment, 3 replicas)
│  └─ Redis (StatefulSet)
├─ ottawa/
├─ vancouver/
└─ (etc.)
```

**Multi-tenancy guarantees:**
- Separate databases per city (RBAC at schema level)
- NetworkPolicies isolate namespaces
- Ingress routes by hostname (toronto.civicmesh.io, ottawa.civicmesh.io, etc.)

---

## Security & Compliance

### Authentication & Authorization

**Edge Node → Backend:**
- HMAC-SHA256 shared secret (Phase 0)
- Device must exist in `devices` table (enrollment)
- Signature verification on every request

**Officer Portal → Backend:**
- Badge number in query param (Phase 0)
- JWT tokens (Phase 1)
- Session management (officer_sessions table)

**Data Ownership:**
- City owns all enforcement data (CivicMesh is processor, not owner)
- Driver owns their data (can request deletion after 3 years)
- Officer audit log is immutable (cannot be altered)

---

### Data Retention

| Data Type | Retention | Reason |
|-----------|-----------|--------|
| **Evidence Clips** | 3 years | Ontario statute of limitations |
| **Incident Records** | 3 years | Legal holds |
| **Citations** | 7 years | Tax/legal records |
| **Officer Audit Log** | Indefinite | Chain-of-custody proof |
| **Device Heartbeats** | 1 year | Diagnostics, then aggregate |

---

### Immutability Enforcement

**Audit Log:** PostgreSQL trigger prevents DELETE
```sql
CREATE OR REPLACE FUNCTION prevent_audit_log_delete()
RETURNS TRIGGER AS $$
BEGIN
    RAISE EXCEPTION 'Audit log records cannot be deleted (immutable by design)';
END;
$$ LANGUAGE plpgsql;
```

**Citations:** Once issued, cannot be modified (only new records added)

**Evidence:** MinIO does not permit object deletion (retention policy enforced)

---

### Cryptographic Proofs

| Item | Hash Function | Purpose |
|------|---------------|---------|
| **HMAC (node signature)** | SHA-256 | Authenticate edge node |
| **Audit Log Hash** | Blake3 | Tamper detection |
| **NOC Document Hash** | Blake3 | Citation immutability proof |
| **Incident Upload** | (Future) TPM signature | Hardware-rooted identity |

---

## Future Phases Integration Points

### Phase 1: Enhanced Data + Ride-Sharing

**New Capabilities:**
- ✅ TPM 2.0 hardware identity (replaces HMAC)
- ✅ Speeding enforcement via OBD verification
- ✅ Interior cameras (ride-sharing safety)
- ✅ Radar integration (collision detection)
- ✅ React officer portal UI
- ✅ MTO/provincial integration (send citations)
- ✅ Blockchain audit log (Polygon L2)

**Database Extensions:**
- `device_heartbeats` table: TPM attestation logs
- `citations.mto_ticket_number` field: populated
- New tables: `ride_share_trips`, `insurance_partnerships`

---

### Phase 2: Multi-City MSSP + Insurance

**Deployment:**
- Kubernetes multi-tenancy (toronto, ottawa, vancouver namespaces)
- Insurance API (real-time telematics to Intact)
- Digital twin data products (road conditions)

**New Tables:**
- `segment_observations` (road assets)
- `insurance_partnerships` (data licensing agreements)

---

### Phase 3: Civilian Opt-In + DAO Governance

**New Features:**
- Civilian nodes (consumer vehicles)
- DAO governance (community voting on enforcement rules)
- Blockchain smart contracts (revenue distribution)

---

## Document References

- **[API_SPECIFICATION.md](./API_SPECIFICATION.md)** — Detailed endpoint specs
- **[DATABASE_SCHEMA.md](./DATABASE_SCHEMA.md)** — Full schema documentation
- **[DEPLOYMENT_GUIDE.md](./DEPLOYMENT_GUIDE.md)** — Setup & operations
- **[DEVELOPER_GUIDE.md](./DEVELOPER_GUIDE.md)** — Local development setup

---

**Last Updated:** 2026-06-05  
**Maintainer:** CivicMesh Platform Team  
**License:** AGPL-3.0 (CivicMesh core)
