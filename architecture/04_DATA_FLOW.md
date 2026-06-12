# Data Flow & Processing Pipeline

## End-to-End Data Journey

```
┌─────────────────────────────────────────────────────────────────┐
│ VEHICLE NODE (Edge Compute)                                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Camera Capture (30 fps)     OBD Poll (10 Hz)     GPS (5 Hz)   │
│  │                           │                      │            │
│  └─→ Frame Buffer ───────────┼──────────────────────┘            │
│      (rolling, last 10s)     │                                   │
│                              │                                   │
│  Edge AI Inference  ◄────────┴──────────────────────            │
│  ├─ ALPR (YOLO v5-nano)                                         │
│  ├─ Lane detection (UNet)                                       │
│  ├─ Sign detection (YOLO)                                       │
│  ├─ Speed check (OBD > limit?)                                  │
│  └─ Output: violation flags + confidence scores                 │
│      │                                                           │
│      └─→ Incident Builder                                       │
│          ├─ Clip extraction (2–5 sec context)                   │
│          ├─ Metadata assembly (GPS, OBD, timestamp, node ID)    │
│          ├─ Compression (H.264, ~500 KB clip)                  │
│          └─ Encryption (AES-256, city-controlled key)          │
│              │                                                   │
│              └─→ Upload Queue (buffer if no connectivity)       │
│
└─────────────────────────────────────────────────────────────────┘
                            │
                            │ LTE/Cellular (encrypted)
                            │
┌─────────────────────────────────────────────────────────────────┐
│ CLOUD INGESTION LAYER (FastAPI)                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  POST /incidents/submit                                         │
│  ├─ Validate signature (node authentication)                    │
│  ├─ Decrypt payload                                             │
│  ├─ Store evidence clip (S3 / MinIO)                           │
│  ├─ Parse metadata (GPS, OBD, violation type, confidence)      │
│  │                                                              │
│  └─→ Deduplication Pipeline                                    │
│      ├─ Query: Is this plate/violation/location already seen?  │
│      ├─ Geofence check: Within 50m?                            │
│      ├─ Time check: Within last 5 minutes?                     │
│      ├─ Confidence check: Is this new obs better than existing? │
│      └─ Decision: Keep new, keep old, or merge + deduplicate   │
│          │                                                      │
│          └─→ Incident Record (PostgreSQL)                      │
│              ├─ violation_id, plate, location, timestamp        │
│              ├─ evidence_clip_s3_url                            │
│              ├─ confidence_score, violation_type               │
│              ├─ node_id (which vehicle submitted)              │
│              └─ status: "queued" (waiting for review)          │
│                  │                                              │
│                  └─→ Review Queue Router                       │
│                      │                                          │
│       ┌─────────────┼─────────────┐                            │
│       │             │             │                            │
│   Fast-Track   Manual Review   Discard                         │
│   (≥95%)       (80–95%)       (<80%)                           │
│   Auto-Issue    Needs Human    Not Pursued                     │
│                                                                │
└─────────────────────────────────────────────────────────────────┘
                            │
                            ├──────────────────┬──────────────────┐
                            │                  │                  │
        ┌───────────────────▼─┐    ┌───────────▼──────┐   ┌──────▼────────┐
        │ FAST-TRACK QUEUE   │    │ MANUAL REVIEW    │   │ DIGITAL TWIN  │
        │ (High Confidence)  │    │ QUEUE (Officer)  │   │ FUSION ENGINE │
        ├────────────────────┤    ├──────────────────┤   ├───────────────┤
        │                    │    │                  │   │               │
        │ Auto-generate NOC  │    │ Officer views    │   │ Extract info: │
        │ (Notice of         │    │ clip + metadata  │   │ - Asset type  │
        │ Contravention)     │    │                  │   │ - Condition   │
        │                    │    │ Decision:        │   │ - Location    │
        │ → Sign-off         │    │ ✓ Approve        │   │ - Timestamp   │
        │ → Issue citation   │    │ ✗ Reject         │   │               │
        │                    │    │ ? Escalate       │   │ → Update Twin │
        └────────┬───────────┘    └────────┬─────────┘   └───────┬───────┘
                 │                         │                     │
                 │                         │                     │
        ┌────────▼─────────┐      ┌───────▼────────┐      ┌──────▼────────┐
        │ CITATION ISSUED  │      │ OFFICER SIGNS  │      │ SEGMENT/ASSET │
        │                  │      │ (Key + Sig)    │      │ CONDITION     │
        │ → NOC generated  │      │ → Citation     │      │ DATABASE      │
        │ → MTO/DMV        │      │   Record       │      │               │
        │ → Registered     │      │   (confirmed)  │      │ Confidence    │
        │   vehicle owner  │      │                │      │ updates,      │
        │ → Payment portal │      │ → Sent to MOT  │      │ alerts for    │
        │ → Revenue        │      │ → Registered   │      │ maintenance   │
        │   accounting     │      │   owner        │      │ work orders   │
        └──────────────────┘      │ → Payment      │      └────────────────┘
                                  │   portal       │
                                  └────────────────┘
```

---

## Detailed Stage-by-Stage Breakdown

### 1. Edge Capture & Inference (On Node)

**Input:** Continuous video stream from dashcam.

**Process:**
```
Loop (every 33ms at 30 fps):
  1. Read frame from camera
  2. Add frame to rolling 10-second buffer (for context clips later)
  3. Run edge AI models:
     - ALPR: detect plates (confidence + text)
     - Lane detection: analyze lane boundaries (confidence + heatmap)
     - Sign/signal detection: find traffic control elements
     - Speed check: OBD speed > posted limit?
  4. For each violation detected:
     - Assign confidence score (0–100%)
     - Flag for incident creation if confidence > 70%
  5. Poll OBD (10 Hz) and GPS (5 Hz) in parallel, sync timestamps
```

**Output:** Violation flags with metadata (timestamp, confidence, bounding boxes).

**Edge Processing Example (Speeding Detection):**
```
OBD speed: 65 km/h
Posted limit (from geofence): 50 km/h
Threshold: >10 km/h over limit
65 > 60? YES → Violation triggered
Confidence: 99% (OBD is ground truth)
Clip extraction: 2 sec before + 3 sec after speed threshold crossed
Incident package size: ~500 KB (compressed H.264 + 300 bytes metadata)
```

---

### 2. Incident Packaging & Local Buffering

**Input:** Violation flag from edge AI.

**Process:**
```
1. Extract clip context from rolling buffer
   - Pre-clip: 2–5 seconds before violation started
   - Post-clip: 2–5 seconds after violation ended
   - Total: 5–10 seconds of H.264 video

2. Compress video
   - Codec: H.264 (hardware acceleration on Pi)
   - Bitrate: 500–800 Kbps (tuned for 1440p/30fps)
   - Output: ~500 KB per 10-second clip

3. Assemble metadata JSON
   {
     "incident_id": "uuid",
     "node_id": "bus_001",
     "violation_type": "speeding",
     "confidence": 0.99,
     "timestamp": "2026-06-01T14:23:45Z",
     "location": {
       "lat": 45.4215,
       "lon": -75.6972,
       "geohash": "f24"
     },
     "obd": {
       "speed_kmh": 65,
       "speed_limit": 50
     },
     "alpr": {
       "plate": "ABC1234",
       "confidence": 0.98
     }
   }
   ~ 300 bytes

4. Sign and encrypt
   - Signature: HMAC-SHA256 (node's private key)
   - Encryption: AES-256-GCM (city's public key)
   - Encrypted package: ~600 bytes header + 500 KB clip

5. Queue for upload
   - Ring buffer (FIFO): 100 incidents max (50 MB)
   - If buffer full: Oldest incidents are locally deleted (kept only if critical)
   - Persistent store on SD card: Survive reboot, reconnection
```

**Output:** Encrypted incident package, queued for transmission.

---

### 3. Cellular Upload & Retry Logic

**Input:** Encrypted incident package.

**Process:**
```
1. Check connectivity
   - If LTE available: Upload immediately
   - If no LTE: Queue locally, retry every 30 seconds

2. Upload
   - POST /incidents/submit (to cloud ingestion API)
   - Include node_id, signature, encrypted payload
   - Timeout: 10 seconds; retry up to 3 times

3. Backoff strategy
   - 1st retry: +10 seconds delay
   - 2nd retry: +30 seconds delay
   - 3rd retry: +60 seconds delay
   - After 3 failures: Log locally, try again next day

4. Vehicle dock (optional)
   - Evening: Vehicle returns to depot, connects Wi-Fi
   - Wi-Fi upload: Offload 24 hours of queued incidents
   - Cost savings: ~10x cheaper than cellular data

5. Confirmation
   - Server responds with HTTP 200 + incident_id
   - Node logs upload success, can delete clip from local buffer
```

**Data efficiency:**
- 10 incidents/day × 500 KB = 5 MB/day
- ~500 KB/month (with compression)
- LTE budget: 500 MB/month per node (~$25 if commercial rate)
- Wi-Fi offload: ~1 MB/night × 250 nights = 250 MB/year (free at depot)

---

### 4. Cloud Ingestion & Validation

**Input:** POST /incidents/submit (encrypted JSON + clip).

**API Endpoint (FastAPI):**
```python
@app.post("/incidents/submit")
async def submit_incident(request: IncidentSubmission):
    # 1. Authenticate node (verify signature)
    if not verify_hmac(request.signature, request.payload, node_secret):
        return HTTPException(401, "Invalid signature")
    
    # 2. Decrypt payload
    payload = decrypt_aes256(request.payload, city_private_key)
    
    # 3. Validate schema
    incident = IncidentSchema(**payload)  # Pydantic validation
    
    # 4. Store evidence clip
    s3_url = store_evidence(incident.clip, incident.incident_id)
    
    # 5. Deduplication pipeline
    is_duplicate = check_duplicate(incident)
    if is_duplicate:
        return {"status": "duplicate", "dedup_group_id": "..."}
    
    # 6. Create incident record
    db.incidents.insert({
        "incident_id": incident.incident_id,
        "violation_type": incident.violation_type,
        "status": "queued",
        "confidence": incident.confidence,
        "evidence_s3_url": s3_url,
        "created_at": incident.timestamp
    })
    
    # 7. Route to review queue
    queue_type = route_to_queue(incident.confidence)
    db.review_queues.insert({
        "queue_type": queue_type,
        "incident_id": incident.incident_id
    })
    
    return {"status": "accepted", "incident_id": incident.incident_id}
```

**Process details:**
- Authentication: HMAC signature verified against node's stored secret
- Decryption: City's private key (rotate quarterly)
- Validation: Schema check + data type validation (Pydantic)
- S3 storage: Evidence clip saved with city's encryption key (separate from transport key)

---

### 5. Deduplication

**Problem:** Bus line 1 passes intersection 5 times per day. Speeding vehicle caught by all 5 buses. Don't ticket driver 5 times.

**Solution:**

```python
def check_duplicate(incident: Incident) -> bool:
    # Query: Is there a recent incident with same plate + violation type + location?
    recent = db.query(
        """
        SELECT * FROM incidents 
        WHERE plate = %s 
        AND violation_type = %s 
        AND ST_DWithin(location, %s, 50)  -- Within 50 meters
        AND timestamp > NOW() - INTERVAL '5 minutes'
        AND status IN ('queued', 'confirmed')
        """
        (incident.plate, incident.violation_type, incident.location)
    )
    
    if recent:
        # Existing incident found; compare confidence
        if incident.confidence > recent[0].confidence:
            # Replace: new evidence is better
            db.update(recent[0].id, confidence=incident.confidence)
        # else: keep existing; discard new
        return True
    
    # No duplicate; this is a new incident
    return False
```

**Example dedup logic:**
- Bus A catches speeding at 14:23:15 (confidence 99%)
- Bus B (same route) catches same car at 14:23:42 (confidence 95%)
- Decision: Keep Bus A's evidence (higher confidence), discard Bus B
- Plate "ABC1234" appears once in review queue

---

### 6. Review Queue Routing

**Input:** Incident record with confidence score.

**Routing logic:**
```
if confidence >= 95%:
    → Fast-Track Queue
    # Officer sees summary; minimal review
    # ~30 seconds per citation
    # Volume: ~60% of incidents

elif confidence >= 80%:
    → Manual Review Queue
    # Officer must watch clip, confirm violation
    # ~1–2 minutes per citation
    # Volume: ~35% of incidents

else:
    → Discard
    # Too uncertain; not pursued
    # Volume: ~5% of incidents
```

**Fast-Track Example (Speeding):**
- High confidence because OBD is ground truth
- Officer sees: "65 km/h in 50 km/h zone"
- Decision: Approve (takes 20 seconds)
- NOC auto-generated, signature added

**Manual Review Example (Lane Violation):**
- Medium confidence because lane detection is CV-based
- Officer sees: Clip + lane segmentation heatmap overlay
- Decision: Watch clip (30 sec), confirm vehicle crossed lane (20 sec), approve (10 sec) = 60 seconds
- NOC generated, officer reviews metadata, signs off

---

### 7. Officer Review Portal

**Officer Workflow:**

```
1. Officer logs in → /portal/queue
   ├─ See count of fast-track incidents (auto-approved)
   ├─ See count of manual review queue
   └─ See count of escalations (officer + manager review)

2. Officer selects incident from queue
   ├─ Video player: Play evidence clip (muted, option to unmute)
   ├─ Metadata panel: Speed, location, plate, timestamp
   ├─ Confidence score: Visual indicator
   ├─ Violation summary: "Speeding: 65 km/h in 50 km/h zone"
   └─ Buttons: [Approve] [Reject] [Escalate]

3. Officer approves
   ├─ Review timer: ~1–2 minutes
   ├─ Signature required: Officer name + badge number (logged)
   ├─ NOC generated: "Notice of Contravention" with officer signature
   └─ Incident status: "confirmed"

4. Officer rejects
   ├─ Reject reason required: "Vehicle not in violation", "Ambiguous evidence", etc.
   ├─ Incident status: "rejected"
   └─ No NOC issued; incident archived

5. Officer escalates
   ├─ Escalation reason: Marked for supervisor review
   ├─ Escalation queue: Separate path for manager decision
   └─ Example: "Unclear signal state; supervisor to verify"
```

**Real-world timing:**
- Officer on patrol: Reviews incidents during downtime
- Fast-track: 30 sec per incident × 100 incidents/day = 50 minutes
- Manual review: 2 min per incident × 50 incidents/day = 100 minutes
- Escalation: 5 min per incident × 5 incidents/day = 25 minutes
- **Total**: ~175 minutes per officer per day (~3 hours), leaving ~5 hours for patrol

---

### 8. Citation Issuance

**Input:** Approved incident record + officer signature.

**Process:**
```
1. Generate Notice of Contravention (NOC)
   ├─ Vehicle info: Plate, make, model, color, registered owner (from MTO lookup)
   ├─ Violation: Type, statute (HTA section), fine amount
   ├─ Evidence: "Electronic enforcement camera. Speeding: 65 km/h in 50 km/h zone"
   ├─ Photo: Plate crop from evidence clip (optional, include if high confidence)
   ├─ Officer info: Name, badge number, signature
   ├─ Chain of custody: Timestamps of detection, review, sign-off
   └─ Court info: Where to pay, where to dispute (local courthouse)

2. Digital signature
   ├─ Officer's digital signature (PKI cert): Proof of review
   ├─ Timestamp: Signed at exact moment of approval
   ├─ Hash: NOC document hash (tamper-evident)
   └─ Audit log: Who signed, when, from which terminal

3. Send to MTO/DMV
   ├─ API call: POST to provincial motor vehicle system
   ├─ Include: Plate, NOC details, evidence metadata
   ├─ MTO confirms receipt + ticket number (cross-reference)
   └─ Mailed to registered owner within 24 hours

4. Payment processing
   ├─ Online portal: Driver can pay online or dispute
   ├─ Payment gateway: Secure, PCI-compliant
   ├─ City treasury: Revenue deposited per phased revenue share
   └─ Tracking: Payment status monitored for collections

5. Audit & compliance
   ├─ Chain of custody logged: Detection → Review → Sign-off → Issue
   ├─ Timestamps immutable: Each event recorded with server clock
   ├─ Encryption: Evidence clips stored with city key
   └─ Retention: Records kept 3 years (statute of limitations)
```

**Revenue Accounting:**

```
Phase 1 (OC Transpo):
├─ Citation issued: $110 fine
├─ City keeps: 100% ($110)
└─ Monthly: 1,000 citations × $110 = $110,000

Phase 2 (City fleet):
├─ Citation issued: $110 fine
├─ City keeps: 80% ($88)
├─ Department split: 15% ($16.50) [OC Transpo, bylaw]
├─ Platform operator: 5% ($5.50)
└─ Monthly: 5,000 citations × $110 = $550,000 distributed

Phase 3 (Civilian opt-in):
├─ Citation issued: $110 fine
├─ City keeps: 50% ($55)
├─ Civilian participant: 30% ($33) — paid monthly
├─ Platform operator: 20% ($22)
└─ Monthly: 10,000 citations × $110 = $1.1M distributed
```

---

### 9. Digital Twin Fusion

**Parallel to enforcement pipeline, every incident contributes to the twin:**

```
For each incident (even rejected ones):
  1. Extract asset/condition info
     ├─ Sign detected? → Log sign type, location, legibility
     ├─ Lane marking visible? → Log marking condition
     ├─ Signal state? → Log red/green/yellow + timestamp
     └─ Road surface? → Log pothole/crack observations

  2. Geohash assignment
     ├─ Quantize lat/lon to 10m geohash cells
     ├─ Example: (45.4215, -75.6972) → geohash "f24wxyz"
     └─ Bucket all observations by geohash

  3. Confidence update
     ├─ Prior: Existing observation for this geohash
     ├─ New: Fresh evidence from this incident
     ├─ Posterior: Bayesian update (higher confidence = more evidence)
     └─ Formula: new_confidence = 0.7 × prior + 0.3 × new_obs

  4. Persistence
     ├─ Update segment_observations table
     ├─ Flag condition changes (degradation, improvement)
     ├─ Route alerts if severity > threshold
     └─ Example: "Pothole at King St. + Bay (new), size ~25 cm → Work order to Public Works"
```

---

## Data Quality & Monitoring

### Key Metrics (Real-time Dashboard)

| Metric | Target | Alert |
|--------|--------|-------|
| **Incidents uploaded per day** | 1,000 | <500 (device health) |
| **Deduplication rate** | 10–15% | >20% (model drift?) |
| **Fast-track ratio** | 60% | <50% (model confidence degrading) |
| **False positive rate** | <5% | >8% (officer rejections) |
| **Review time (manual)** | <2 min | >5 min (backlog building) |
| **Average confidence** | >85% | <75% (model needs retraining) |
| **Data upload success rate** | >99% | <95% (connectivity / modem issues) |

### Alerts & Escalations
- **Model drift:** If false positive rate increases >10% over week → trigger retraining
- **Review backlog:** If manual queue > 500 incidents → page supervisors
- **Device failure:** If node goes offline >1 day → send technician
- **Data loss:** If incident buffer full and dropped → flag node for service

---

## Failure Modes & Resilience

### What if LTE connectivity drops?
- Edge: Local buffer (100 incidents, 50 MB) persists on SD card
- Recovery: Automatic retry every 30 seconds
- Depot offload: Wi-Fi at night uploads queued incidents
- Max data loss: ~0 (persistent local storage)

### What if officer is offline (reviewing incident)?
- System: Incident stays in queue, available for next officer
- Locking: Incident lock expires after 5 minutes (in case officer forgets)
- Reassignment: Supervisor can reassign if stuck too long

### What if evidence clip is corrupted?
- Detection: Hash check on upload; server rejects if mismatch
- Recovery: Node retransmits; if persistent, incident marked "evidence lost"
- Fallback: Officer sees metadata (plate, speed, etc.) without video; lower confidence

### What if officer signs incorrect citation?
- Audit: Digital signature + timestamp logged; can be reviewed
- Dispute: Driver appeals; video evidence retrieved and reviewed
- Accountability: Officer's signature tracked; repeat patterns flagged to supervisor

---

## Compliance & Audit Trail

Every incident has an immutable audit log:

```
Incident ABC-123:
├─ 14:23:15 — Detected by node "bus_001" at location (45.4215, -75.6972)
├─ 14:23:45 — Uploaded to cloud
├─ 14:23:47 — Deduplication: No match; new incident
├─ 14:23:48 — Routed to fast-track queue (confidence 99%)
├─ 14:25:12 — Officer "Smith" reviewed (session ID: xyz)
├─ 14:25:35 — Officer approved
├─ 14:25:37 — Digital signature applied (cert hash: abc123def)
├─ 14:25:40 — NOC generated, sent to MTO
├─ 14:25:45 — MTO confirmed, ticket #TK-2026-001234
├─ 14:26:00 — Digital twin updated: Road condition at location confirmed
└─ (Searchable by: plate, officer, timestamp, location, violation type)
```

All logs are:
- **Timestamped:** Server clock (NTP synchronized)
- **Signed:** HMAC-SHA256 to prevent tampering
- **Encrypted:** At rest, accessible only to authorized officers/supervisors
- **Retained:** 3 years per statute of limitations

This is what makes enforcement defensible in court.
