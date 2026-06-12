# CivicMesh Developer Guide

**Contributing to Phase 0 MVP and beyond**

Version: 1.0  
Last Updated: 2026-06-05

---

## Table of Contents

1. [Getting Started](#getting-started)
2. [Project Structure](#project-structure)
3. [Code Style & Standards](#code-style--standards)
4. [Testing](#testing)
5. [Adding Features](#adding-features)
6. [Git Workflow](#git-workflow)
7. [Debugging](#debugging)

---

## Getting Started

### Setup Local Environment

```bash
# Clone repo
git clone https://github.com/civicmesh/civicmesh.git
cd civicmesh

# Create Python virtual environment
python3.11 -m venv venv
source venv/bin/activate  # or: venv\Scripts\activate on Windows

# Install dependencies
pip install -r requirements.txt

# Install dev dependencies (optional)
pip install black flake8 mypy pytest-cov
```

### Start Dev Services

```bash
# Terminal 1: Start all services (postgres, minio, redis, rabbitmq)
docker compose -f config/docker/docker-compose.yml up -d

# Terminal 2: Run FastAPI with auto-reload
uvicorn backend.api.main:app --reload --host 0.0.0.0 --port 8000

# Terminal 3: Run tests or other commands
pytest tests/unit/ -v
```

### Verify Setup

```bash
# Health check
curl http://localhost:8000/health

# Run unit tests (should pass)
pytest tests/unit/ -v

# Check code style
black --check backend/
flake8 backend/
```

---

## Project Structure

```
CivicMesh/
├── backend/
│   ├── api/
│   │   ├── __init__.py
│   │   ├── main.py              # FastAPI app, lifespan, route registration
│   │   ├── ingest.py            # POST /incidents/submit pipeline (8 steps)
│   │   ├── review_queue.py      # Officer portal endpoints (GET queue, POST approve/reject)
│   │   └── deps.py              # Dependency injection (DB, S3, Redis)
│   │
│   ├── models/
│   │   ├── __init__.py
│   │   ├── confidence_scorer.py  # Queue routing by confidence threshold
│   │   ├── violation_classifier.py # Normalize violation types
│   │   ├── deduplication_logic.py  # PostGIS duplicate detection
│   │   └── citation_generator.py   # NOC document creation
│   │
│   └── database/
│       ├── __init__.py
│       ├── schema_incidents.sql    # incidents, review_queues, citations, audit_log
│       ├── schema_devices.sql      # devices, device_secrets, device_heartbeats
│       ├── schema_twin.sql         # segment_observations (Phase 1+)
│       └── schema_compliance.sql   # officer_sessions, officer_actions
│
├── tests/
│   ├── unit/
│   │   ├── test_confidence_scorer.py
│   │   ├── test_violation_classifier.py
│   │   └── test_dedup_logic.py
│   │
│   └── integration/
│       ├── test_ingest_pipeline.py  (placeholder)
│       └── test_officer_workflow.py  (placeholder)
│
├── config/
│   └── docker/
│       ├── docker-compose.yml
│       └── Dockerfile.api
│
├── docs/
│   ├── PHASE_0_MVP_ARCHITECTURE.md
│   ├── API_SPECIFICATION.md
│   ├── DEPLOYMENT_GUIDE.md
│   └── DEVELOPER_GUIDE.md (this file)
│
├── requirements.txt
├── CLAUDE.md                 # Project architecture & vision
└── README.md                 # Quick start guide
```

### Module Overview

**backend/api/main.py**
- Entry point: FastAPI application initialization
- Lifespan: Load database schemas on startup, cleanup on shutdown
- Route registration: Include ingest_router, review_router
- Health endpoints: /health, /

**backend/api/ingest.py**
- Core evidence pipeline: 8-step processing (sign verify → dedup → queue → audit)
- POST /api/v1/incidents/submit: ~150 lines
- Heavy lifting: S3 upload, PostGIS query, audit logging
- Async/await throughout (non-blocking I/O)

**backend/api/review_queue.py**
- Officer portal endpoints
- GET /portal/queue: Queue summary
- GET /portal/incidents/{id}: Incident details
- POST /approve|/reject|/escalate: Approval workflow
- Emphasis: Immutable audit logging on every decision

**backend/api/deps.py**
- Dependency injection: FastAPI uses `Depends()` to inject services
- Database pool: asyncpg connection pooling
- S3 client: MinIO boto3 client
- Redis: Cache for dedup lookups (Phase 1+)

**backend/models/confidence_scorer.py**
- Decision logic: confidence → queue_type
- Configurable thresholds: Fast-track 95%, manual 80–95%, discard <80%
- No I/O, pure function (testable, fast)

**backend/models/violation_classifier.py**
- Normalizes violation types: "bus_lane", "BUS_LANE", "bus_lane_obstruction" → ViolationType.BUS_LANE
- Generates descriptions: "Speeding: 65 km/h in 50 km/h zone"
- No I/O, pure function

**backend/models/deduplication_logic.py**
- PostGIS query: Same plate + location + time → detect duplicate
- Decision: UNIQUE, DUPLICATE_HIGHER (update), DUPLICATE_LOWER (discard)
- Async wrapper around SQL query

**backend/models/citation_generator.py**
- NOC document creation: Plate + violation + timestamp + officer badge
- Blake3 hash: Immutability proof
- Phase 0: Simple signature (badge number)
- Phase 1: TPM hardware signature

---

## Code Style & Standards

### Formatting

```bash
# Auto-format code
black backend/ tests/

# Check style
flake8 backend/ tests/

# Type checking
mypy backend/ --ignore-missing-imports
```

### Conventions

**Naming:**
- Functions: `snake_case` (e.g., `get_incident_detail`)
- Classes: `PascalCase` (e.g., `ViolationType`)
- Constants: `UPPER_SNAKE_CASE` (e.g., `CONFIDENCE_THRESHOLDS`)

**Imports:**
- Standard library first
- Third-party next
- Local imports last
- Sorted alphabetically within each group

```python
import hashlib
import json
from datetime import datetime
from uuid import UUID

from fastapi import APIRouter
from sqlalchemy import text
from sqlalchemy.ext.asyncio import AsyncSession

from backend.api.deps import get_db
from backend.models.confidence_scorer import score_confidence
```

**Docstrings:**
- Only on public functions (not internal helpers)
- One-line for simple functions
- Multi-line for complex logic (explain WHY, not WHAT)

```python
def score_confidence(violation_type: str, confidence: float) -> QueueType:
    """Determine queue routing based on violation type and confidence."""
    # ...

async def submit_incident(...):
    """
    Submit evidence package and route to review queue.
    
    8-step pipeline:
    1. Verify HMAC signature
    2. Upload clip to MinIO
    3. PostGIS deduplication
    4. Classify violation
    5. Score confidence
    6. Route to queue
    7. Insert into database
    8. Append audit log
    """
```

---

## Testing

### Unit Tests (No Database)

```bash
# Run all unit tests
pytest tests/unit/ -v

# Run specific test file
pytest tests/unit/test_confidence_scorer.py -v

# Run specific test function
pytest tests/unit/test_confidence_scorer.py::test_bus_lane_fast_track -v

# With coverage report
pytest tests/unit/ --cov=backend --cov-report=html
# Opens htmlcov/index.html
```

**Test Philosophy:**
- Pure functions only (no I/O, no database)
- Confidence scorer, violation classifier, dedup logic
- Fast feedback loop (sub-second)
- No mocking needed (functions are deterministic)

**Coverage Target:** >80%

### Integration Tests (With Database)

```bash
# Requires running services
docker compose -f config/docker/docker-compose.yml up -d

# Run integration tests
pytest tests/integration/ -v

# Includes:
# - Full ingest pipeline (signature verify → MinIO → dedup → queue)
# - Officer workflow (approve → citation → audit log)
# - Data API (insurance telematics queries)
```

### Writing Tests

**Example: Confidence Scorer**

```python
def test_bus_lane_fast_track():
    """High confidence bus lane violation → fast_track queue."""
    result = score_confidence("bus_lane", 0.97)
    assert result == QueueType.FAST_TRACK

def test_bus_lane_manual_review():
    """Medium confidence bus lane → manual_review queue."""
    result = score_confidence("bus_lane", 0.82)
    assert result == QueueType.MANUAL_REVIEW

def test_bus_lane_discard():
    """Low confidence bus lane → discard (not stored)."""
    result = score_confidence("bus_lane", 0.79)
    assert result == QueueType.DISCARD

def test_boundary_conditions():
    """Test exact threshold boundaries."""
    # Exactly at fast_track threshold
    assert score_confidence("bus_lane", 0.95) == QueueType.FAST_TRACK
    # Just below
    assert score_confidence("bus_lane", 0.949) == QueueType.MANUAL_REVIEW
```

---

## Adding Features

### Add New Violation Type (Speeding)

**Step 1: Update Confidence Scorer**

```python
# backend/models/confidence_scorer.py

CONFIDENCE_THRESHOLDS = {
    # ... existing ...
    ViolationType.SPEEDING: {
        "fast_track_min": 0.99,      # OBD-based is most reliable
        "manual_review_min": 0.85,   # GPS-based is less reliable
        "discard_below": 0.85,
    }
}
```

**Step 2: Update Violation Classifier**

```python
# backend/models/violation_classifier.py

def classify_violation(violation_type: str, metadata: dict):
    # ... existing ...
    elif violation_type.lower() == "speeding":
        speed_kmh = metadata.get("speed_kmh", 0)
        speed_limit = metadata.get("speed_limit", 0)
        excess = speed_kmh - speed_limit
        return ViolationClassification(
            violation_type=ViolationType.SPEEDING,
            confidence=confidence,
            description=f"Speeding: {speed_kmh} km/h in {speed_limit} km/h zone (+{excess} km/h)"
        )
```

**Step 3: Add Tests**

```python
# tests/unit/test_confidence_scorer.py

def test_speeding_strict_threshold():
    """Speeding requires 99% confidence for fast_track."""
    assert score_confidence("speeding", 0.99) == QueueType.FAST_TRACK
    assert score_confidence("speeding", 0.90) == QueueType.MANUAL_REVIEW
    assert score_confidence("speeding", 0.84) == QueueType.DISCARD
```

**Step 4: Update Documentation**

```markdown
# API_SPECIFICATION.md

## Violation Types

| Type | Confidence Threshold | Phase |
|------|----------------------|-------|
| bus_lane | 95%+ | 0 |
| red_light | 95%+ | 0 |
| speeding | 99%+ | 1 |
```

**Step 5: Test End-to-End**

```bash
# Run tests
pytest tests/unit/test_confidence_scorer.py::test_speeding_strict_threshold -v

# Manual test: POST evidence with violation_type=speeding
curl -X POST http://localhost:8000/api/v1/incidents/submit \
  -F violation_type=speeding \
  -F confidence=0.99 \
  ...
```

---

### Add New Officer Portal Feature (Batch Approval)

**Step 1: Create Endpoint**

```python
# backend/api/review_queue.py

@router.post("/portal/incidents/batch/approve")
async def approve_batch(
    incident_ids: list[str] = Query(...),
    badge_number: str = Depends(validate_officer),
    db: AsyncSession = Depends(get_db),
):
    """Approve multiple incidents at once."""
    for incident_id in incident_ids:
        # Reuse logic from single-approve endpoint
        # ... approval workflow ...
```

**Step 2: Add Tests**

```python
# tests/integration/test_officer_workflow.py

async def test_batch_approval():
    """Officer approves multiple incidents."""
    incident_ids = ["id1", "id2", "id3"]
    response = await client.post(
        "/api/v1/portal/incidents/batch/approve",
        params={"badge_number": "OCA123", "incident_ids": incident_ids}
    )
    assert response.status_code == 200
    assert response.json()["approved_count"] == 3
```

**Step 3: Update API Documentation**

```markdown
# API_SPECIFICATION.md

### POST /api/v1/portal/incidents/batch/approve

**Purpose:** Approve multiple incidents in one request

**Query Parameters:**
- incident_ids: List of UUID strings
- badge_number: Officer badge

**Response:** {status: "approved", approved_count: 3, ...}
```

---

## Git Workflow

### Branch Naming

```
feature/speeding-enforcement       # New feature
bugfix/dedup-false-positives       # Bug fix
docs/api-specification-update      # Documentation
refactor/confidence-scorer-cleanup # Refactoring
```

### Commit Message Format

```
<type>: <subject>

<body>

Fixes #<issue_number>
```

**Example:**

```
feature: Add speeding violation detection

- Implement speed verification via OBD
- Add 99% confidence threshold for fast-track
- Include unit tests for speeding classification

Fixes #42
```

### Pull Request Process

1. Create feature branch: `git checkout -b feature/xyz`
2. Make changes, test locally
3. Commit with clear message
4. Push: `git push origin feature/xyz`
5. Open PR on GitHub
6. Add description (what, why, how to test)
7. Request review from @civicmesh/platform
8. Address feedback, push updates
9. Merge when approved

**PR Template:**

```markdown
## Summary
What does this PR do?

## Type of Change
- [ ] Feature
- [ ] Bug fix
- [ ] Documentation
- [ ] Refactoring

## How to Test
1. Start services: `docker compose up -d`
2. Run tests: `pytest tests/`
3. Manual test: `curl ...`

## Checklist
- [ ] Tests pass
- [ ] Code style passes (black, flake8)
- [ ] Documentation updated
- [ ] No breaking changes
```

---

## Debugging

### Enable Logging

```python
# backend/api/main.py

import logging

logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger(__name__)

# In endpoint:
logger.debug(f"Processing incident: {incident_id}")
logger.info(f"Routed to {queue_type}")
logger.error(f"Failed to upload: {e}")
```

### View Logs

```bash
# FastAPI development server (auto-reload)
uvicorn backend.api.main:app --reload --log-level debug

# Docker logs
docker compose logs -f backend

# Specific container
docker logs civicmesh-backend --tail 100 -f
```

### Database Debugging

```bash
# Connect to PostgreSQL
docker exec -it civicmesh-postgres psql -U civicmesh -d civicmesh

# Useful commands:
SELECT * FROM incidents WHERE incident_id = 'xxx';
SELECT * FROM audit_log WHERE incident_id = 'xxx' ORDER BY timestamp;
SELECT * FROM review_queues WHERE queue_type = 'escalation';

-- Trace query performance
EXPLAIN ANALYZE SELECT * FROM incidents WHERE plate = 'ABC1234';
```

### IDE Debugging (VSCode)

```json
// .vscode/launch.json

{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "FastAPI",
      "type": "python",
      "request": "launch",
      "module": "uvicorn",
      "args": ["backend.api.main:app", "--reload"],
      "jinja": true,
      "justMyCode": true
    }
  ]
}
```

Set breakpoints, F5 to start debugging.

---

## Performance Tips

### Query Optimization

```python
# SLOW: Query all incidents then filter in Python
incidents = await db.execute(text("SELECT * FROM incidents"))
result = [i for i in incidents if i.plate == 'ABC1234']

# FAST: Let database filter
result = await db.execute(
    text("SELECT * FROM incidents WHERE plate = :plate"),
    {"plate": "ABC1234"}
)
```

### Connection Pooling

```python
# Configured in deps.py
engine = create_async_engine(
    DATABASE_URL,
    pool_size=20,        # Reuse connections
    max_overflow=10,     # Allow extra connections under load
    echo=False,          # Disable query logging in production
)
```

### Caching

```python
# Use Redis for dedup cache (Phase 1+)
redis = await get_redis()
cache_key = f"dedup:{plate}:{violation_type}"
cached = await redis.get(cache_key)
if cached:
    return cached  # Fast path
```

---

## Further Reading

- [PHASE_0_MVP_ARCHITECTURE.md](./PHASE_0_MVP_ARCHITECTURE.md)
- [API_SPECIFICATION.md](./API_SPECIFICATION.md)
- [PostgreSQL Async with SQLAlchemy](https://docs.sqlalchemy.org/en/20/orm/extensions/asyncio.html)
- [FastAPI Best Practices](https://fastapi.tiangolo.com/deployment/concepts/)

---

**Last Updated:** 2026-06-05  
**Maintainer:** CivicMesh Platform Team
