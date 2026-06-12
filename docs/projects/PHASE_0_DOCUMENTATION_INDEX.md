# Phase 0 MVP Documentation Index

**Complete documentation for CivicMesh OC Transpo enforcement system**

---

## Quick Navigation

### For Different Audiences

**I want to understand the system...**
→ Start with [PHASE_0_MVP_ARCHITECTURE.md](./PHASE_0_MVP_ARCHITECTURE.md)
- Complete system overview
- Architecture diagrams
- Core components explanation
- Data flow walkthrough

**I want to deploy it...**
→ Read [DEPLOYMENT_GUIDE.md](./DEPLOYMENT_GUIDE.md)
- Quick start (Docker Compose)
- Local development setup
- Production deployment options
- Monitoring & troubleshooting

**I want to call the API...**
→ Use [API_SPECIFICATION.md](./API_SPECIFICATION.md)
- All endpoints documented
- Request/response examples
- Error codes and handling
- CURL examples

**I want to contribute code...**
→ Follow [DEVELOPER_GUIDE.md](./DEVELOPER_GUIDE.md)
- Project structure overview
- Code style standards
- Testing guidelines
- Feature addition examples
- Git workflow

---

## Documents Overview

### 1. PHASE_0_MVP_ARCHITECTURE.md (~500 lines)

**What:** Complete system design and architecture  
**Audience:** Architects, technical leads, operators  
**Contains:**
- System overview + success criteria
- Architecture diagram
- Core components (8 sections)
- Data flow (complete incident lifecycle)
- Technology stack
- Deployment models (single VM, Kubernetes)
- Security & compliance
- Future phases integration points

**Key Sections:**
- Architecture Diagram (ASCII visual)
- Evidence Ingestion Pipeline (8-step detailed flow)
- Deduplication Engine (PostGIS logic)
- Confidence-Based Queue Routing (thresholds per violation type)
- Database Schema (4 core tables: incidents, queues, citations, audit_log)

---

### 2. API_SPECIFICATION.md (~350 lines)

**What:** Complete HTTP API reference  
**Audience:** Backend developers, integration engineers, node firmware developers  
**Contains:**
- Authentication (HMAC-SHA256 for nodes, JWT Phase 1+)
- 5 main endpoints fully documented
- Request/response formats with examples
- Error codes and handling
- Rate limiting (Phase 0 vs Phase 1)
- Detailed CURL examples

**Endpoints Documented:**
- `POST /api/v1/incidents/submit` — Evidence ingestion (8-step pipeline)
- `GET /api/v1/portal/queue` — Officer queue summary
- `GET /api/v1/portal/incidents/{id}` — Incident details
- `POST /api/v1/portal/incidents/{id}/approve` — Issue citation
- `POST /api/v1/portal/incidents/{id}/reject` — Reject incident
- `POST /api/v1/portal/incidents/{id}/escalate` — Escalate to supervisor

**Examples Section:**
- Complete CURL examples for each endpoint
- Sample payloads (JSON)
- Expected responses
- Error scenarios

---

### 3. DEPLOYMENT_GUIDE.md (~400 lines)

**What:** Setup and operations guide  
**Audience:** DevOps, system administrators, developers  
**Contains:**
- Quick start (Docker Compose)
- Local development setup
- Production deployment (single VM + Kubernetes)
- Monitoring & troubleshooting
- Backup & recovery
- Scaling considerations
- Security checklist
- Maintenance procedures

**Quick Start Covers:**
- Prerequisites (Docker, ports)
- 7-step setup process
- Service verification
- Test data creation
- Health checks

**Troubleshooting Section:**
- Health checks (all services)
- Log viewing (Docker)
- Performance issues
- Common errors + fixes
- Database debugging

**Backup Strategy:**
- PostgreSQL backup/restore
- MinIO backup/restore
- Data retention policies (3 years incidents, indefinite audit logs)

---

### 4. DEVELOPER_GUIDE.md (~400 lines)

**What:** Contribution and development guide  
**Audience:** Backend developers, QA engineers, contributors  
**Contains:**
- Getting started (venv, dependencies)
- Project structure (module overview)
- Code style & standards (formatting, naming, docstrings)
- Testing (unit + integration tests)
- Adding features (step-by-step examples)
- Git workflow (branching, commits, PRs)
- Debugging techniques
- Performance tips

**Project Structure Section:**
- File-by-file module overview
- What each file does
- Where to find what

**Adding Features Example:**
- Step-by-step guide to add new violation type (speeding)
- Updates to confidence scorer, classifier, tests, docs
- End-to-end testing

**Testing Section:**
- Unit test examples
- Integration test placeholders
- Coverage targets (>80%)
- Test philosophy (pure functions, fast feedback)

**Git Workflow:**
- Branch naming conventions
- Commit message format
- PR process + template

---

## Document Cross-References

```
PHASE_0_MVP_ARCHITECTURE.md
├─ "See API_SPECIFICATION.md for endpoint details"
├─ "See DEPLOYMENT_GUIDE.md for setup instructions"
├─ "See DEVELOPER_GUIDE.md for code structure"
└─ "See DATABASE_SCHEMA.md for schema details"

API_SPECIFICATION.md
├─ "See DEPLOYMENT_GUIDE.md for MINIO/PostgreSQL setup"
├─ "See PHASE_0_MVP_ARCHITECTURE.md for component overview"
└─ "See DEVELOPER_GUIDE.md for authentication implementation"

DEPLOYMENT_GUIDE.md
├─ "See PHASE_0_MVP_ARCHITECTURE.md for system design"
├─ "See API_SPECIFICATION.md for health check endpoints"
└─ "See DEVELOPER_GUIDE.md for local development"

DEVELOPER_GUIDE.md
├─ "See PHASE_0_MVP_ARCHITECTURE.md for system context"
├─ "See API_SPECIFICATION.md for endpoint signatures"
└─ "See DEPLOYMENT_GUIDE.md for dev environment setup"
```

---

## Key Concepts by Document

### Authentication

| Document | Section | Details |
|----------|---------|---------|
| API_SPECIFICATION.md | [Authentication](#authentication) | HMAC-SHA256 signature, JWT Phase 1 |
| DEPLOYMENT_GUIDE.md | Security Checklist | Change default credentials |
| DEVELOPER_GUIDE.md | Debugging | View auth logs |

### Database Schema

| Document | Section | Details |
|----------|---------|---------|
| PHASE_0_MVP_ARCHITECTURE.md | [Database Schema](#6-database-schema) | 4 core tables overview |
| DATABASE_SCHEMA.md | Full specification | SQL DDL for all tables |
| DEPLOYMENT_GUIDE.md | Backup & Recovery | PostgreSQL backup procedures |
| DEVELOPER_GUIDE.md | Database Debugging | psql CLI queries |

### API Endpoints

| Document | Section | Details |
|----------|---------|---------|
| API_SPECIFICATION.md | [Ingestion](#post-apiv1incidentssubmit), [Portal](#officer-portal-endpoints) | Complete endpoint specs |
| PHASE_0_MVP_ARCHITECTURE.md | [Data Flow](#data-flow) | High-level workflow |
| DEVELOPER_GUIDE.md | Adding Features | How to add new endpoints |

### Testing

| Document | Section | Details |
|----------|---------|---------|
| DEVELOPER_GUIDE.md | [Testing](#testing) | Unit/integration test guidelines |
| Project Code | tests/ directory | Actual test implementations |
| DEPLOYMENT_GUIDE.md | Health Checks | Operational testing |

---

## Reading Paths by Role

### Architecture Review

1. [PHASE_0_MVP_ARCHITECTURE.md](./PHASE_0_MVP_ARCHITECTURE.md) — System design
2. [API_SPECIFICATION.md](./API_SPECIFICATION.md) — Interface definitions
3. [DEPLOYMENT_GUIDE.md](./DEPLOYMENT_GUIDE.md) — Deployment architecture

### Backend Developer

1. [DEVELOPER_GUIDE.md](./DEVELOPER_GUIDE.md) — Setup & project structure
2. [PHASE_0_MVP_ARCHITECTURE.md](./PHASE_0_MVP_ARCHITECTURE.md) — System context
3. [API_SPECIFICATION.md](./API_SPECIFICATION.md) — Endpoint specs
4. Code: `backend/api/*.py`, `backend/models/*.py`

### DevOps/SRE

1. [DEPLOYMENT_GUIDE.md](./DEPLOYMENT_GUIDE.md) — Setup & operations
2. [PHASE_0_MVP_ARCHITECTURE.md](./PHASE_0_MVP_ARCHITECTURE.md) — Component understanding
3. Config files: `config/docker/docker-compose.yml`, `config/k8s/*.yaml`

### Node Firmware Developer

1. [API_SPECIFICATION.md](./API_SPECIFICATION.md) — `/incidents/submit` endpoint
2. [PHASE_0_MVP_ARCHITECTURE.md](./PHASE_0_MVP_ARCHITECTURE.md) — Edge Node section
3. Example: CURL examples in API_SPECIFICATION.md

### QA/Tester

1. [DEPLOYMENT_GUIDE.md](./DEPLOYMENT_GUIDE.md) — Setup test environment
2. [API_SPECIFICATION.md](./API_SPECIFICATION.md) — Endpoint examples
3. [DEVELOPER_GUIDE.md](./DEVELOPER_GUIDE.md) — Testing guidelines

---

## Phase 0 Success Criteria (Documented)

From [PHASE_0_MVP_ARCHITECTURE.md](./PHASE_0_MVP_ARCHITECTURE.md#success-criteria-phase-0):

| Metric | Target | Status |
|--------|--------|--------|
| Buses Deployed | 5–10 → 50 | ✅ Ready for pilot |
| Violations/Day | 100–500 | ✅ System can handle |
| Officer Review Time | <2 min/incident | ✅ Fast-track queue optimized |
| False Positive Rate | <5% | ✅ Confidence thresholds set |
| System Uptime | 99%+ | ✅ Docker health checks |
| Monthly Revenue | $310K+ (50 buses) | ✅ Revenue splits documented |

---

## Phase 1 Roadmap (Referenced)

Mentioned in documents as integration points:

**Features:**
- React officer portal UI
- JWT authentication
- Speeding enforcement (OBD verification)
- Interior cameras (ride-sharing safety)
- Radar integration (collision detection)
- MTO/provincial integration
- Blockchain audit log (Polygon L2)

**References in docs:**
- [PHASE_0_MVP_ARCHITECTURE.md - Future Phases](#future-phases-integration-points)
- [DEPLOYMENT_GUIDE.md - Kubernetes MSSP](#option-2-kubernetes-mssp-phase-1)
- [API_SPECIFICATION.md - Future Enhancements](#future-enhancements-phase-1)

---

## Implementation Checklist

**Documentation Complete:**
- ✅ Architecture documented
- ✅ APIs documented
- ✅ Deployment documented
- ✅ Development documented
- ✅ Code implemented (15 files)
- ✅ Tests implemented (3 test suites)

**Ready for Phase 0 Pilot:**
- ✅ Evidence ingestion pipeline
- ✅ Officer review portal endpoints
- ✅ Database schema
- ✅ Confidence-based routing
- ✅ Audit logging
- ✅ Docker Compose setup
- ✅ HMAC authentication

**Coming in Phase 1:**
- 🔄 React UI
- 🔄 JWT tokens
- 🔄 Speeding detection
- 🔄 Ride-sharing integration

---

## Related Documents in Codebase

**Architecture & Vision:**
- [CLAUDE.md](../CLAUDE.md) — Overall CivicMesh vision & phases
- [PHASE_0_MVP_ARCHITECTURE.md](./PHASE_0_MVP_ARCHITECTURE.md) — This system's design

**Strategy & Market:**
- [GO_TO_MARKET_STRATEGY.md](./GO_TO_MARKET_STRATEGY.md) — B2B-first adoption
- [MULTI_SERVICE_NETWORK_STRATEGY.md](./MULTI_SERVICE_NETWORK_STRATEGY.md) — Future multi-service platform

**Transparency & Governance:**
- [TRANSPARENCY_LAYER.md](./TRANSPARENCY_LAYER.md) — Immutable audit logs, public dashboards
- [FIELD_OPERATIONS.md](./FIELD_OPERATIONS.md) — Technician guild, hardware tamper-proofing

---

## Quick Links

| Need | Link |
|------|------|
| **Deploy locally** | [DEPLOYMENT_GUIDE.md - Quick Start](./DEPLOYMENT_GUIDE.md#quick-start-docker-compose) |
| **Call an API** | [API_SPECIFICATION.md - Examples](./API_SPECIFICATION.md#examples) |
| **Understand architecture** | [PHASE_0_MVP_ARCHITECTURE.md - Overview](./PHASE_0_MVP_ARCHITECTURE.md#system-overview) |
| **Add a feature** | [DEVELOPER_GUIDE.md - Adding Features](./DEVELOPER_GUIDE.md#adding-features) |
| **Debug a problem** | [DEPLOYMENT_GUIDE.md - Troubleshooting](./DEPLOYMENT_GUIDE.md#monitoring--troubleshooting) |
| **Write a test** | [DEVELOPER_GUIDE.md - Testing](./DEVELOPER_GUIDE.md#testing) |

---

## Version History

| Date | Version | Changes |
|------|---------|---------|
| 2026-06-05 | 1.0 | Initial Phase 0 documentation |

---

**Last Updated:** 2026-06-05  
**Maintainer:** CivicMesh Platform Team  
**License:** AGPL-3.0 (CivicMesh core)

---

## How to Use This Index

1. **New to the project?** Start with [PHASE_0_MVP_ARCHITECTURE.md](./PHASE_0_MVP_ARCHITECTURE.md)
2. **Ready to code?** Jump to [DEVELOPER_GUIDE.md](./DEVELOPER_GUIDE.md)
3. **Need to deploy?** Go to [DEPLOYMENT_GUIDE.md](./DEPLOYMENT_GUIDE.md)
4. **Building a client?** Use [API_SPECIFICATION.md](./API_SPECIFICATION.md)
5. **Managing ops?** Use [DEPLOYMENT_GUIDE.md](./DEPLOYMENT_GUIDE.md) monitoring section

Each document is self-contained but cross-references others for context.
