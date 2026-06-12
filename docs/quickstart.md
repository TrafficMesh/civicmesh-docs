# Quick Start Guide

Get CivicMesh running locally in 5 minutes.

## Prerequisites

- Docker & Docker Compose
- Git
- 4GB RAM, 10GB disk space
- Linux, macOS, or Windows (WSL2)

## For Developers

### 1. Clone the Backend

```bash
git clone https://github.com/TrafficMesh/civicmesh-backend.git
cd civicmesh-backend
```

### 2. Start Services

```bash
docker compose -f config/docker/docker-compose.yml up
```

**Services available:**
- Backend API: http://localhost:8000
- PostgreSQL: localhost:5432
- MinIO Console: http://localhost:9001 (admin/minioadmin)
- RabbitMQ Console: http://localhost:15672 (guest/guest)
- Redis: localhost:6379

### 3. Verify Health

```bash
curl http://localhost:8000/health
# Response: {"status": "healthy"}
```

### 4. Run Tests

```bash
docker compose exec backend pytest tests/unit/ -v
```

## For Hardware Engineers

### 1. Get the BOM

[Hardware Bill of Materials](../hardware/NODE_BOM.md)

**Cost:** $511/unit for 50-unit deployment

### 2. Order Components

- Raspberry Pi CM4 2GB ($55)
- Viofo A119 dashcam ($250)
- Quectel EC25 LTE modem ($35)
- u-blox NEO-M9N GPS ($30)
- TPM 2.0 + OBD-II + power management ($96)

### 3. Follow Assembly Guide

[30-Minute Assembly Procedure](../hardware/NODE_ASSEMBLY.md)

### 4. Run QA Tests

[QA Checklist](../hardware/QA_TESTING.md)

## For DevOps/SRE

### 1. Local Development (Docker)

```bash
git clone https://github.com/TrafficMesh/civicmesh-backend.git
cd civicmesh-backend
docker compose -f config/docker/docker-compose.yml up -d
```

### 2. Single Machine (Ubuntu)

[Single VM Deployment](../deployment/SINGLE_VM_DEPLOYMENT.md)

### 3. Kubernetes (Production)

[Kubernetes MSSP Deployment](../deployment/KUBERNETES_MSSP.md)

## For Product/Business

### 1. Understand the Vision

[Project Registry](../architecture/00_PROJECT_REGISTRY.md) — Complete specification

### 2. Review Business Model

[Business Model](../architecture/05_BUSINESS_MODEL.md) — Three-tier strategy

### 3. Check Phase 0 Status

[Phase 0 MVP](../roadmap/PHASE_0_STATUS.md) — OC Transpo pilot timeline

## For Legal/Compliance

### 1. Regulatory Compliance

[Ontario HTA](../compliance/ONTARIO_HTA.md) — Highway Traffic Act requirements

### 2. Privacy & Data

[Privacy & PIPEDA](../compliance/PRIVACY_PIPEDA.md) — Data protection compliance

### 3. Evidence Handling

[Chain of Custody](../compliance/CHAIN_OF_CUSTODY.md) — Audit trail procedures

## Next Steps

- [API Reference](../api/backend-api.md) — All endpoints
- [Data Flow Architecture](../architecture/04_DATA_FLOW.md) — Complete pipeline
- [Hardware Stack](../architecture/01_HARDWARE_STACK.md) — Edge to cloud

---

**Time to first running service:** ~5 minutes  
**Time to first test passing:** ~2 minutes  
**Time to understand full architecture:** ~1-2 hours
