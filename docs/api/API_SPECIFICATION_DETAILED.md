# CivicMesh Phase 0 API Specification

**RESTful HTTP API for evidence ingestion, officer review, and citation issuance**

Version: 1.0  
Last Updated: 2026-06-05  
Base URL: `http://localhost:8000` (dev), `https://api.civicmesh.example.com` (prod)

---

## Table of Contents

1. [Authentication](#authentication)
2. [Ingestion Endpoints](#ingestion-endpoints)
3. [Officer Portal Endpoints](#officer-portal-endpoints)
4. [Error Handling](#error-handling)
5. [Rate Limiting](#rate-limiting)
6. [Examples](#examples)

---

## Authentication

### Node Authentication (Edge → Backend)

**Method:** HMAC-SHA256 signature in `signature` form parameter

**Workflow:**
1. Node retrieves its `secret_key` from local configuration (burned at factory)
2. Constructs metadata string: `node_id:violation_type:confidence:plate:latitude:longitude:timestamp`
3. Computes HMAC: `HMAC-SHA256(metadata, secret_key)`
4. Includes HMAC digest in signature form parameter
5. Backend looks up node in `device_secrets` table, verifies signature

**Example:**
```python
import hmac
import hashlib

metadata = "OC_TRANSPO_BUS_001:bus_lane:0.97:ABC1234:43.6629:-79.3957:2026-06-01T14:23:45Z"
secret_key = b"node_secret_12345"
signature = hmac.new(secret_key, metadata.encode(), hashlib.sha256).hexdigest()
# signature = "a1b2c3d4e5f6..."
```

**Phase 1 Evolution:** Replace with TPM-based attestation (hardware identity proof)

---

### Officer Authentication (Portal → Backend)

**Method:** Badge number in query parameter (Phase 0)

**Header (Phase 1+):**
```
Authorization: Bearer <jwt_token>
```

JWT claims will include:
- `sub` (subject): officer_id
- `badge_number`: e.g., "OCA123"
- `exp` (expiration): timestamp
- `iat` (issued at): timestamp

---

## Ingestion Endpoints

### POST /api/v1/incidents/submit

**Purpose:** Submit evidence package from edge node

**Request Method:** POST  
**Content-Type:** multipart/form-data

**Form Parameters:**

| Parameter | Type | Required | Description | Example |
|-----------|------|----------|-------------|---------|
| `node_id` | string | ✓ | Node identifier | `OC_TRANSPO_BUS_001` |
| `violation_type` | string | ✓ | Type of violation | `bus_lane`, `red_light` |
| `confidence` | float | ✓ | Detection confidence (0.0–1.0) | `0.97` |
| `plate` | string | ✗ | License plate (if detected) | `ABC1234` |
| `latitude` | float | ✓ | Incident latitude | `43.6629` |
| `longitude` | float | ✓ | Incident longitude | `-79.3957` |
| `timestamp` | string | ✓ | ISO 8601 timestamp (UTC) | `2026-06-01T14:23:45Z` |
| `signature` | string | ✓ | HMAC-SHA256 signature | `a1b2c3d4...` |
| `evidence_clip` | file | ✓ | H.264 video clip (MP4) | (binary) |

**Response: 200 OK (Accepted)**

```json
{
  "status": "accepted",
  "incident_id": "a1b2c3d4-1234-5678-90ab-cdefghijklmn",
  "violation_type": "bus_lane",
  "queue_type": "fast_track",
  "confidence": 0.97,
  "message": "Incident routed to fast_track"
}
```

**Response: 200 OK (Duplicate - Lower Confidence)**

```json
{
  "status": "duplicate",
  "dedup_group_id": "existing-incident-id",
  "message": "Lower confidence than existing incident"
}
```

**Response: 200 OK (Duplicate - Higher Confidence)**

```json
{
  "status": "duplicate_replaced",
  "dedup_group_id": "existing-incident-id",
  "message": "Higher confidence; existing incident updated"
}
```

**Response: 200 OK (Discarded - Low Confidence)**

```json
{
  "status": "discarded",
  "incident_id": "a1b2c3d4-...",
  "message": "Confidence too low (0.75) for queue routing"
}
```

**Response: 400 Bad Request**

```json
{
  "detail": "Confidence must be between 0.0 and 1.0"
}
```

**Response: 401 Unauthorized**

```json
{
  "detail": "Invalid signature" | "Node not authorized (unknown node)"
}
```

**Response: 500 Internal Server Error**

```json
{
  "detail": "Failed to upload evidence" | "Failed to store incident"
}
```

---

## Officer Portal Endpoints

### GET /api/v1/portal/queue

**Purpose:** Get queue summary for logged-in officer

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `badge_number` | string | ✓ | Officer badge (e.g., OCA123) |

**Response: 200 OK**

```json
{
  "badge_number": "OCA123",
  "queue_summary": {
    "fast_track": 15,
    "manual_review": 8,
    "escalation": 1
  },
  "locked_incidents": 2,
  "timestamp": "2026-06-01T14:30:00Z"
}
```

---

### GET /api/v1/portal/incidents/{incident_id}

**Purpose:** Get full incident details for review

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `badge_number` | string | ✓ | Officer badge |

**URL Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `incident_id` | UUID | Incident ID from incidents table |

**Response: 200 OK**

```json
{
  "incident_id": "a1b2c3d4-...",
  "node_id": "OC_TRANSPO_BUS_501",
  "violation_type": "bus_lane",
  "plate": "ABC1234",
  "confidence": 0.97,
  "timestamp": "2026-06-01T14:23:45Z",
  "location": {
    "latitude": 43.6629,
    "longitude": -79.3957
  },
  "evidence_url": "https://civicmesh-minio.example.com/civicmesh/incidents/a1b2c3d4/a1b2c3d4-....mp4?X-Amz-Algorithm=AWS4-HMAC-SHA256&...",
  "status": "queued",
  "queue_info": {
    "queue_type": "fast_track",
    "assigned_to": null,
    "locked_until": null
  }
}
```

---

### POST /api/v1/portal/incidents/{incident_id}/approve

**Purpose:** Officer approves incident and issues citation

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `badge_number` | string | ✓ | Officer badge |

**URL Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `incident_id` | UUID | Incident ID |

**Request Body:** (Empty, parameters in URL)

**Response: 200 OK**

```json
{
  "status": "approved",
  "incident_id": "a1b2c3d4-...",
  "citation_id": "cit-5678-...",
  "noc_hash": "blake3_hash_of_noc_document",
  "message": "Citation issued by OCA123"
}
```

**Side Effects:**
- Updates `incidents` table: `status` = 'confirmed'
- Creates new row in `citations` table
- Appends to `audit_log` table: event_type = 'approved'
- Locks incident for 5 minutes (prevents double-approval)

**Response: 404 Not Found**

```json
{
  "detail": "Incident not found"
}
```

---

### POST /api/v1/portal/incidents/{incident_id}/reject

**Purpose:** Officer rejects incident (false positive or insufficient evidence)

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `badge_number` | string | ✓ | Officer badge |
| `reason` | string | ✓ | Reason for rejection | `"Insufficient evidence"`, `"Not a violation"` |

**URL Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `incident_id` | UUID | Incident ID |

**Response: 200 OK**

```json
{
  "status": "rejected",
  "incident_id": "a1b2c3d4-...",
  "reason": "Insufficient evidence",
  "officer": "OCA123"
}
```

**Side Effects:**
- Updates `incidents` table: `status` = 'rejected'
- Appends to `audit_log` table: event_type = 'rejected', metadata includes reason
- Unlocks queue entry (released for another officer)

---

### POST /api/v1/portal/incidents/{incident_id}/escalate

**Purpose:** Officer escalates to supervisor queue

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `badge_number` | string | ✓ | Officer badge |
| `reason` | string | ✓ | Reason for escalation | `"Unclear if violation"` |

**URL Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `incident_id` | UUID | Incident ID |

**Response: 200 OK**

```json
{
  "status": "escalated",
  "incident_id": "a1b2c3d4-...",
  "reason": "Unclear if violation",
  "escalated_by": "OCA123"
}
```

**Side Effects:**
- Updates `review_queues` table: `queue_type` = 'escalation'
- Appends to `audit_log` table: event_type = 'escalated'

---

## Health Check Endpoints

### GET /health

**Purpose:** Liveness probe for Kubernetes/load balancers

**Response: 200 OK**

```json
{
  "status": "ok",
  "service": "civicmesh-api",
  "version": "0.1.0"
}
```

---

### GET /

**Purpose:** Root endpoint with service info

**Response: 200 OK**

```json
{
  "service": "CivicMesh API",
  "phase": "0 (OC Transpo Pilot)",
  "endpoints": {
    "health": "/health",
    "ingest": "/api/v1/incidents/submit",
    "portal": "/api/v1/portal/queue"
  }
}
```

---

## Error Handling

### HTTP Status Codes

| Code | Meaning | Typical Cause |
|------|---------|---------------|
| **200** | OK | Request succeeded |
| **400** | Bad Request | Invalid data (confidence not 0–1, missing parameters) |
| **401** | Unauthorized | Invalid HMAC signature or unknown node |
| **404** | Not Found | Incident or officer not found |
| **409** | Conflict | Incident already locked by another officer |
| **500** | Internal Server Error | Database, storage, or processing failure |

### Error Response Format

```json
{
  "detail": "Human-readable error message"
}
```

---

## Rate Limiting

**Phase 0:** No explicit rate limiting (backend pool handles 50 buses)

**Phase 1:** Per-node rate limiting
- Max 100 uploads per 5-minute window
- Max 10 MB upload size
- Auto-throttle with 429 (Too Many Requests)

---

## Examples

### Example 1: Node Uploads Evidence (Bus 501)

```bash
#!/bin/bash

NODE_ID="OC_TRANSPO_BUS_501"
VIOLATION_TYPE="bus_lane"
CONFIDENCE="0.97"
PLATE="ABC1234"
LATITUDE="43.6629"
LONGITUDE="-79.3957"
TIMESTAMP="2026-06-01T14:23:45Z"
SECRET_KEY="node_secret_12345"

# Build metadata string
METADATA="${NODE_ID}:${VIOLATION_TYPE}:${CONFIDENCE}:${PLATE}:${LATITUDE}:${LONGITUDE}:${TIMESTAMP}"

# Compute HMAC-SHA256
SIGNATURE=$(echo -n "$METADATA" | openssl dgst -sha256 -hmac "$SECRET_KEY" | awk '{print $2}')

# Upload
curl -X POST http://localhost:8000/api/v1/incidents/submit \
  -F "node_id=$NODE_ID" \
  -F "violation_type=$VIOLATION_TYPE" \
  -F "confidence=$CONFIDENCE" \
  -F "plate=$PLATE" \
  -F "latitude=$LATITUDE" \
  -F "longitude=$LONGITUDE" \
  -F "timestamp=$TIMESTAMP" \
  -F "signature=$SIGNATURE" \
  -F "evidence_clip=@evidence.mp4" \
  | jq .
```

**Expected Output:**
```json
{
  "status": "accepted",
  "incident_id": "a1b2c3d4-...",
  "queue_type": "fast_track",
  "confidence": 0.97
}
```

---

### Example 2: Officer Reviews Queue

```bash
BADGE_NUMBER="OCA123"

curl "http://localhost:8000/api/v1/portal/queue?badge_number=${BADGE_NUMBER}" | jq .
```

**Expected Output:**
```json
{
  "badge_number": "OCA123",
  "queue_summary": {
    "fast_track": 15,
    "manual_review": 8,
    "escalation": 1
  },
  "locked_incidents": 2,
  "timestamp": "2026-06-01T14:30:00Z"
}
```

---

### Example 3: Officer Gets Incident Details

```bash
INCIDENT_ID="a1b2c3d4-1234-5678-90ab-cdefghijklmn"
BADGE_NUMBER="OCA123"

curl "http://localhost:8000/api/v1/portal/incidents/${INCIDENT_ID}?badge_number=${BADGE_NUMBER}" | jq .
```

**Expected Output:**
```json
{
  "incident_id": "a1b2c3d4-...",
  "violation_type": "bus_lane",
  "plate": "ABC1234",
  "confidence": 0.97,
  "evidence_url": "https://..."
}
```

---

### Example 4: Officer Approves Incident

```bash
INCIDENT_ID="a1b2c3d4-1234-5678-90ab-cdefghijklmn"
BADGE_NUMBER="OCA123"

curl -X POST "http://localhost:8000/api/v1/portal/incidents/${INCIDENT_ID}/approve?badge_number=${BADGE_NUMBER}" | jq .
```

**Expected Output:**
```json
{
  "status": "approved",
  "incident_id": "a1b2c3d4-...",
  "citation_id": "cit-5678-...",
  "noc_hash": "blake3_hash..."
}
```

---

## Implementation Notes

### Async/Await

All endpoints are async (non-blocking) to handle concurrent uploads:

```python
@router.post("/incidents/submit")
async def submit_incident(
    node_id: str = Form(...),
    # ... other parameters
    db: AsyncSession = Depends(get_db),
):
    # Async operations:
    # - await asyncpg.execute() for database queries
    # - await s3_client.put_object() for MinIO uploads
    # - No blocking I/O
```

### Database Transactions

Ingestion endpoint wraps multi-step processing in a single transaction:
1. Verify signature
2. Upload to MinIO
3. Check for duplicates
4. Insert incident
5. Create queue entry
6. Append audit log

If any step fails, all are rolled back (atomicity).

---

## Future Enhancements (Phase 1+)

- JWT authentication instead of badge_number in query param
- Pagination for queue endpoints (limit, offset)
- Filter endpoints (by violation_type, date range, status)
- WebSocket support for real-time queue updates
- Batch upload endpoint (multiple incidents in one request)
- Mobile app support (OAuth2 integration)

---

**Last Updated:** 2026-06-05  
**Maintainer:** CivicMesh Platform Team
