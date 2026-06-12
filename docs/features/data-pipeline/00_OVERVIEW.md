# Data Pipeline Module - Overview

**End-to-end incident data flow from edge node to cloud ingestion.**

---

## Feature Summary

The data pipeline is the **glue** connecting edge detection to cloud storage and officer review. It handles:
- Edge capture & compression (on node)
- Encryption & signing (security)
- Buffering & retry logic (reliability)
- Cloud upload (connectivity)
- Ingestion & deduplication (backend)
- Digital twin fusion (parallel processing)

**Key Characteristics:**
- End-to-end encrypted
- Survives 24-hour offline
- Automatic retry with backoff
- Deduplication across multiple nodes
- <5 sec ingestion latency
- 99%+ upload success rate

---

## Subfeatures

| Subfeature | Description | Priority | Phase |
|-----------|-------------|----------|-------|
| **Frame Capture** | Grab dashcam frames at 30 fps | CRITICAL | MVP |
| **Metadata Collection** | Assemble timestamp, GPS, OBD, violation data | CRITICAL | MVP |
| **Clip Extraction** | Extract 5-sec context (pre + post) | CRITICAL | MVP |
| **Compression** | H.264 video codec, ~500 KB per clip | HIGH | MVP |
| **Encryption** | AES-256-GCM for confidentiality | HIGH | MVP |
| **Signing** | HMAC-SHA256 for authenticity | HIGH | MVP |
| **Local Buffer** | Ring buffer (100 incidents, 50 MB) | MEDIUM | MVP |
| **Cellular Upload** | LTE/5G with retry logic | CRITICAL | MVP |
| **Cloud Ingestion** | FastAPI validation + storage | CRITICAL | MVP |
| **Deduplication** | Merge duplicate incidents from multiple nodes | HIGH | MVP |
| **Digital Twin Fusion** | Extract asset data, update twin | MEDIUM | Phase 2 |

---

## Architecture

```
Edge Node (Capture)
├── Frame Buffer (rolling 10 sec)
├── Violation Detection (async)
└── Incident Packaging
    ├── Clip extraction
    ├── Metadata assembly
    ├── Compression (H.264)
    ├── Encryption (AES-256)
    └── Signing (HMAC)
        ↓
Local Buffer
├── Ring buffer (100 incidents)
├── Persistent storage (SD card)
├── Retry queue
└── Status tracking
    ↓
LTE/Cellular Uplink
├── Encrypt packet
├── POST to cloud API
├── Automatic retry (exponential backoff)
└── Fallback to Wi-Fi (depot)
    ↓
Cloud Ingestion API
├── Signature verification (node auth)
├── Packet decryption
├── Evidence storage (S3/MinIO)
├── Database persistence (PostgreSQL)
├── Deduplication
└── Review queue routing
    ├→ Fast-track queue (95%+ confidence)
    ├→ Manual review (80-95%)
    ├→ Discard (<80%)
    └→ Digital Twin fusion
```

---

## Data Flow Diagram

```
INCIDENT GENERATION
├─ Violation detected (speeding, lane, etc.)
├─ Type + confidence logged
└─ Incident ID assigned

CLIP EXTRACTION
├─ 2 sec pre-violation from buffer
├─ Violation event frame(s)
└─ 3 sec post-violation from buffer
   Total: 5-10 sec clip

METADATA ASSEMBLY
├─ Timestamp (frame-level)
├─ GPS (lat, lon, accuracy)
├─ OBD (speed, heading, RPM)
├─ Violation details (type, speed, limit, excess)
├─ ALPR results (if applicable)
├─ Node ID (which vehicle)
└─ Confidence score (violation confidence)

COMPRESSION & ENCRYPTION
├─ H.264 video compression
│  └─ ~500 KB per 10-sec clip
├─ Metadata JSON (~1 KB)
└─ Package size: ~501 KB total

SIGNING & ENCRYPTION
├─ HMAC-SHA256 signature (node secret key)
├─ AES-256-GCM encryption (city public key)
└─ Final encrypted package: ~510 KB

LOCAL BUFFERING
├─ Ring buffer: 100 incidents max (50 MB)
├─ If buffer full: oldest incident dropped
├─ If upload fails: retry queue (up to 24 hrs)
└─ Status: pending, uploading, success, failed

CELLULAR UPLOAD
├─ Check LTE connectivity
├─ If connected: upload immediately
├─ If no connection: queue locally
├─ Retry strategy:
│  ├─ 1st retry: +10 sec delay
│  ├─ 2nd retry: +30 sec delay
│  ├─ 3rd retry: +60 sec delay
│  └─ After 3 failures: try next hour
├─ Depot Wi-Fi offload:
│  ├─ Vehicle returns at night
│  ├─ Connect to depot Wi-Fi
│  └─ Offload queued incidents (cost savings)
└─ Success: HTTP 200 + incident_id returned

CLOUD INGESTION
├─ Receive POST /incidents/submit
├─ Verify signature (node authentication)
├─ Decrypt payload (city private key)
├─ Validate schema (Pydantic)
├─ Store evidence clip (S3/MinIO)
├─ Store metadata (PostgreSQL)
└─ Return: HTTP 200 + incident_id

DEDUPLICATION
├─ Query: "Is this (plate, violation, location) already reported?"
├─ Geofence check: Within 50m?
├─ Time check: Within last 5 min?
├─ Decision:
│  ├─ New incident: Create record
│  ├─ Duplicate (lower confidence): Ignore
│  └─ Duplicate (higher confidence): Replace
└─ Result: Single incident per violation

REVIEW QUEUE ROUTING
├─ Fast-track (≥95% confidence):
│  ├─ Auto-issue citation
│  └─ Officer minimal review
├─ Manual review (80-95%):
│  ├─ Officer watches clip
│  └─ Approves/rejects
└─ Discard (<80%):
   └─ Not pursued
```

---

## Requirements

### Software
```
Python 3.8+
├── opencv-python        # Frame capture & compression
├── cryptography         # AES-256, HMAC
├── requests             # HTTP uploads
├── fastapi              # Cloud API
├── sqlalchemy           # Database ORM
├── boto3 (or minio)     # S3-compatible storage
├── pydantic             # Schema validation
└── pytest               # Testing
```

### Data / Test Assets

#### Test Videos
- `test_data/videos/short_incident.mp4` (10 sec) — Single violation
- `test_data/videos/multiple_incidents.mp4` (1 min) — 3 violations

#### Test Scenarios
- `test_data/scenarios/normal_flow.json` — Happy path
- `test_data/scenarios/network_dropout.json` — 30-min offline, then recovery
- `test_data/scenarios/duplicate_incidents.json` — Same violation from 2 buses

#### Expected Results
- `test_data/results/incident_metadata.json` — Expected packet structure
- `test_data/results/dedup_results.json` — Expected dedup behavior

### Hardware (Real Deployment)
- Raspberry Pi CM4
- LTE modem (SIM card)
- SD card (persistent buffer)

**For MVP Testing:** Mock LTE API + local storage

---

## Testing Strategy

### Unit Tests
- [ ] Clip extraction (2+3 sec context)
- [ ] Metadata assembly (JSON structure)
- [ ] Compression ratio (target <1 MB per minute)
- [ ] Encryption/decryption (verify no data loss)
- [ ] Signing/verification (tampering detection)
- [ ] Buffer management (ring buffer logic)
- [ ] Retry logic (exponential backoff)
- [ ] Deduplication (merge duplicate incidents)

### Integration Tests
- [ ] End-to-end: incident → compression → encryption → upload → ingestion
- [ ] Network disconnection recovery
- [ ] Multiple violations in quick succession
- [ ] Large incident queue (edge of buffer capacity)

### Performance Tests
- [ ] Compression latency <500 ms
- [ ] Encryption latency <100 ms
- [ ] Upload throughput (sustained LTE speed)
- [ ] Memory usage during buffering

### Acceptance Tests
- [ ] Real video + OBD data flows end-to-end
- [ ] Evidence retrievable from cloud storage
- [ ] Metadata accurate in database
- [ ] False duplicates don't merge
- [ ] True duplicates do merge

---

## Success Criteria

### MVP (Weeks 1–4)
- ✓ Incident captures correctly with metadata
- ✓ Compression ratio <1 MB per minute
- ✓ Encryption/decryption preserves data integrity
- ✓ Upload success rate >99%
- ✓ <5 sec ingestion latency
- ✓ Deduplication accuracy >95%

### Field Trial (Weeks 5–12)
- ✓ All above maintained with real hardware
- ✓ Survives 24-hour offline scenario
- ✓ Handles 50+ concurrent nodes
- ✓ No data loss in any scenario

---

## Next Documents

- `01_EDGE_CAPTURE.md` — Frame capture & buffering
- `02_ENCRYPTION.md` — AES-256 & HMAC signing
- `03_UPLOAD.md` — Cellular upload & retry logic
- `04_INGESTION.md` — Cloud API & database
- `05_DEDUPLICATION.md` — Merge duplicate incidents

---

**Start Here:** `01_EDGE_CAPTURE.md`
