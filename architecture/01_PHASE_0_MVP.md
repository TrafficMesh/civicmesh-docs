# Phase 0 MVP Specification

OC Transpo pilot deployment: 50 buses, 2 violation types, human-approved enforcement.

## Scope

### Backend
- Evidence ingestion pipeline (8 steps: HMAC → MinIO → PostGIS → Queue)
- Officer portal endpoints (5 endpoints: queue, detail, approve, reject, escalate)
- Unit tests (>80% coverage)
- Docker Compose local dev

### Hardware
- 50 node kits ($511/unit)
- Dashcam calibration
- Power management
- Assembly procedures

### Success Criteria
- 5 buses deployed 24/7
- <100ms inference latency
- <2 min officer review time
- <5% false positive rate

See civicmesh-backend and civicmesh-hardware for implementation details.
