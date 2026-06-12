# CivicMesh Phase 0 Deployment Guide

**Setup, deployment, and operations for OC Transpo pilot**

Version: 1.0  
Last Updated: 2026-06-05

---

## Table of Contents

1. [Quick Start (Docker Compose)](#quick-start-docker-compose)
2. [Local Development Setup](#local-development-setup)
3. [Production Deployment](#production-deployment)
4. [Monitoring & Troubleshooting](#monitoring--troubleshooting)
5. [Backup & Recovery](#backup--recovery)

---

## Quick Start (Docker Compose)

### Prerequisites

- Docker Desktop (or Docker Engine + Docker Compose)
- 8 GB RAM available
- Port availability: 5432, 6379, 5672, 9000, 9001, 8000
- Git

### 1. Clone Repository

```bash
git clone https://github.com/civicmesh/civicmesh.git
cd civicmesh
```

### 2. Start All Services

```bash
docker compose -f config/docker/docker-compose.yml up -d
```

**Expected output:**
```
Creating civicmesh-postgres ... done
Creating civicmesh-minio ... done
Creating civicmesh-redis ... done
Creating civicmesh-rabbitmq ... done
Creating civicmesh-backend ... done
```

### 3. Wait for Services to Be Ready

```bash
# Check health
docker compose ps

# Should show all containers "running" with healthy status:
# STATUS: Up X seconds (healthy)
```

Monitor logs:
```bash
docker compose logs -f backend
# Look for: "Application startup complete" message
```

### 4. Initialize Database

Database schemas auto-load on FastAPI startup. Check:

```bash
# Connect to PostgreSQL
docker exec -it civicmesh-postgres psql -U civicmesh -d civicmesh -c "\dt"

# Should list tables:
# incidents, review_queues, citations, audit_log, devices, device_secrets, etc.
```

### 5. Create Test Node (Device)

```bash
docker exec -it civicmesh-postgres psql -U civicmesh -d civicmesh << 'EOF'

-- Insert test device
INSERT INTO devices (node_id, vehicle_vin, vehicle_type, status, firmware_version, enrolled_at)
VALUES ('OC_TRANSPO_BUS_001', 'TEST_VIN_12345', 'bus', 'active', '0.1.0', NOW());

-- Insert device secret (HMAC key)
INSERT INTO device_secrets (device_id, secret_key, created_at)
VALUES ('OC_TRANSPO_BUS_001', '\x6e6f64655f7365637265745f3132333435', NOW());
-- Note: secret_key = hex('node_secret_12345')

-- Verify
SELECT * FROM devices WHERE node_id = 'OC_TRANSPO_BUS_001';
EOF
```

### 6. Test API Endpoint

```bash
# Health check
curl http://localhost:8000/health | jq .

# Expected: {"status": "ok", "service": "civicmesh-api", "version": "0.1.0"}
```

### 7. Access Services

| Service | URL | Credentials |
|---------|-----|-------------|
| **FastAPI** | http://localhost:8000 | (no auth) |
| **MinIO Console** | http://localhost:9001 | minioadmin / minioadmin |
| **RabbitMQ Console** | http://localhost:15672 | guest / guest |
| **PostgreSQL** | localhost:5432 | civicmesh / devpass123 |
| **Redis** | localhost:6379 | (no auth) |

---

## Local Development Setup

### Install Python Dependencies

```bash
pip install -r requirements.txt
```

### Run Tests

```bash
# Unit tests only (no database)
pytest tests/unit/ -v

# With database (requires running services)
pytest tests/integration/ -v
```

### Run Backend Locally (Without Docker)

```bash
# Start services in docker (but not backend)
docker compose -f config/docker/docker-compose.yml up -d \
  postgres minio redis rabbitmq

# Wait for PostgreSQL health check
sleep 10

# Run FastAPI directly
uvicorn backend.api.main:app --host 0.0.0.0 --port 8000 --reload
```

**Advantages:**
- Code changes auto-reload (no container restart)
- Easier debugging (use IDE breakpoints)
- Faster iteration

### Connect to Database Directly

```bash
# PostgreSQL CLI
docker exec -it civicmesh-postgres psql -U civicmesh -d civicmesh

# Useful queries:
SELECT COUNT(*) FROM incidents;
SELECT COUNT(*) FROM review_queues;
SELECT * FROM audit_log ORDER BY timestamp DESC LIMIT 10;
```

### Browse MinIO

1. Open http://localhost:9001
2. Login: minioadmin / minioadmin
3. Click "Buckets" → "civicmesh"
4. Browse uploaded evidence clips

---

## Production Deployment

### Recommended Architecture

```
┌─────────────────────────────────┐
│  Load Balancer (Nginx)          │
│  (SSL termination)              │
└──────────────┬──────────────────┘
               │
       ┌───────┴────────┐
       │                │
       ▼                ▼
┌────────────┐  ┌────────────┐
│ FastAPI 1  │  │ FastAPI 2  │
│ (Replica)  │  │ (Replica)  │
└────────────┘  └────────────┘
       │                │
       └───────┬────────┘
               │
       ┌───────┴────────┐
       │                │
       ▼                ▼
┌──────────────┐  ┌──────────────┐
│ PostgreSQL   │  │ MinIO        │
│ (Primary)    │  │ (S3 Storage) │
└──────────────┘  └──────────────┘

┌──────────────────────────────────┐
│ Redis (Session/Cache)            │
└──────────────────────────────────┘
```

### Option 1: Single VM with Docker Compose (Phase 0)

**Hardware:** 
- 4 vCPU, 8 GB RAM, 100 GB SSD
- Public static IP
- Ubuntu 22.04 LTS

**Installation:**

```bash
# SSH into VM
ssh ubuntu@your-server-ip

# Install Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Clone repo
git clone https://github.com/civicmesh/civicmesh.git
cd civicmesh

# Create .env file
cat > .env << 'EOF'
DATABASE_URL=postgresql+asyncpg://civicmesh:prodpass123@postgres:5432/civicmesh
MINIO_ACCESS_KEY=prodminio123
MINIO_SECRET_KEY=prodminio456
CIVICMESH_ENV=production
EOF

# Start services
docker compose -f config/docker/docker-compose.yml up -d

# Verify
curl http://localhost:8000/health
```

**SSL/HTTPS Setup:**

```bash
# Install certbot
sudo apt-get install certbot python3-certbot-nginx -y

# Get certificate (requires domain)
sudo certbot certonly --standalone -d api.civicmesh.example.com

# Point Nginx reverse proxy to FastAPI (port 8000)
# Configure SSL cert in Nginx config
```

### Option 2: Kubernetes MSSP (Phase 1+)

For multi-city deployments, use Kubernetes:

```bash
# Create per-city namespace
kubectl create namespace civicmesh-toronto

# Deploy stack
kubectl -n civicmesh-toronto apply -f config/k8s/

# Verify
kubectl -n civicmesh-toronto get pods
kubectl -n civicmesh-toronto logs -l app=civicmesh-backend -f
```

---

## Monitoring & Troubleshooting

### Health Checks

```bash
# API health
curl http://localhost:8000/health

# Database
docker exec -it civicmesh-postgres pg_isready

# MinIO
docker exec -it civicmesh-minio mc admin info minio

# Redis
docker exec -it civicmesh-redis redis-cli ping
```

### View Logs

```bash
# FastAPI logs
docker compose logs -f backend

# PostgreSQL logs
docker compose logs -f postgres

# All services
docker compose logs -f
```

### Monitor Incident Processing

```bash
# PostgreSQL CLI
docker exec -it civicmesh-postgres psql -U civicmesh -d civicmesh << 'EOF'

-- Count incidents by status
SELECT status, COUNT(*) as count FROM incidents GROUP BY status;

-- Recent incidents (last 10)
SELECT incident_id, violation_type, status, confidence, created_at 
FROM incidents 
ORDER BY created_at DESC LIMIT 10;

-- Queue summary
SELECT queue_type, COUNT(*) as count FROM review_queues GROUP BY queue_type;

-- Audit log (chain of custody)
SELECT incident_id, event_type, actor, timestamp FROM audit_log ORDER BY timestamp DESC LIMIT 20;
EOF
```

### Performance Troubleshooting

**Slow Evidence Upload:**
```bash
# Check MinIO connectivity
docker compose logs minio | grep error

# Check network bandwidth
iperf3 -s  # on server
iperf3 -c server-ip -t 10  # on client
```

**High Database CPU:**
```sql
-- Slow query log
SELECT query, calls, mean_time FROM pg_stat_statements ORDER BY mean_time DESC LIMIT 10;

-- Missing index?
SELECT schemaname, tablename FROM pg_tables WHERE schemaname = 'public';
EXPLAIN ANALYZE SELECT * FROM incidents WHERE plate = 'ABC1234';
```

**Queue Backup:**
```sql
SELECT queue_type, COUNT(*) FROM review_queues GROUP BY queue_type;
-- If manual_review > 100, may need more officers
```

### Common Issues

#### Issue: FastAPI Fails to Connect to PostgreSQL

```
Error: asyncpg.exceptions.PostgresError: could not connect to the server
```

**Fix:**
```bash
# Ensure PostgreSQL is running
docker compose ps postgres  # should show "running"

# Check connection string
echo $DATABASE_URL  # should be postgresql+asyncpg://...

# Verify network
docker compose exec backend ping postgres
```

#### Issue: MinIO Bucket Not Found

```
Error: botocore.exceptions.ClientError: An error occurred (NoSuchBucket)
```

**Fix:**
```bash
# Create bucket
docker exec -it civicmesh-minio bash
mc mb minio/civicmesh
```

#### Issue: Incident Not Inserted After Upload

```
Node POST returns 200 (accepted) but incident doesn't appear in database
```

**Fix:**
```bash
# Check audit log
SELECT * FROM audit_log WHERE incident_id = 'xxx' ORDER BY timestamp;

# Check for 'duplicate' status
SELECT COUNT(*) FROM incidents WHERE status = 'duplicate';

# Check confidence threshold
SELECT * FROM incidents WHERE confidence < 0.80 ORDER BY created_at DESC;
```

---

## Backup & Recovery

### PostgreSQL Backup

```bash
# Full backup
docker exec civicmesh-postgres pg_dump -U civicmesh civicmesh > civicmesh_backup.sql

# Backup with compression
docker exec civicmesh-postgres pg_dump -U civicmesh -Fc civicmesh > civicmesh_backup.dump

# Scheduled nightly backup (cron job)
0 2 * * * docker exec civicmesh-postgres pg_dump -U civicmesh -Fc civicmesh > /backups/civicmesh_$(date +\%Y\%m\%d).dump
```

### PostgreSQL Restore

```bash
# From SQL backup
docker exec -i civicmesh-postgres psql -U civicmesh civicmesh < civicmesh_backup.sql

# From dump backup
docker exec -i civicmesh-postgres pg_restore -U civicmesh -d civicmesh civicmesh_backup.dump
```

### MinIO Backup

```bash
# Sync to external storage (e.g., AWS S3)
docker exec civicmesh-minio mc mirror minio/civicmesh s3://backup-bucket/civicmesh

# Restore from backup
docker exec civicmesh-minio mc mirror s3://backup-bucket/civicmesh minio/civicmesh
```

### Data Retention Policies

**Incidents:** Keep 3 years (Ontario statute of limitations)
```sql
-- Cleanup query (run periodically)
DELETE FROM incidents WHERE created_at < NOW() - INTERVAL '3 years';
```

**Audit Log:** Keep indefinitely (chain of custody)
```sql
-- Never delete audit_log (immutable by design)
-- Only archive to cold storage after 3 years
```

---

## Scaling Considerations

### Vertical Scaling (Single Server)

**Current limits (4 vCPU, 8 GB RAM):**
- FastAPI: 50–100 concurrent connections
- PostgreSQL: 200+ connections (managed by pool_size)
- MinIO: Unlimited (limited by disk)
- Redis: Limited by RAM

**Upgrade to 8 vCPU, 32 GB RAM for 300+ buses**

### Horizontal Scaling (Kubernetes, Phase 1+)

```bash
# Scale FastAPI replicas
kubectl -n civicmesh-toronto scale deployment civicmesh-backend --replicas=3

# PostgreSQL: Setup read replicas (for reporting queries)
# Redis: Setup cluster mode (for distributed cache)
```

---

## Security Checklist

- [ ] PostgreSQL password changed from default (devpass123)
- [ ] MinIO credentials changed from default (minioadmin)
- [ ] SSL certificates installed (HTTPS enabled)
- [ ] Firewall allows only necessary ports (8000, 5432, 9000)
- [ ] Database backups stored off-site (encrypted)
- [ ] Audit logs backed up (immutable copy)
- [ ] Node secrets stored securely (not in logs)
- [ ] Officer authentication via JWT (Phase 1+)
- [ ] Regular security patches applied (Docker images updated)

---

## Maintenance Windows

**Recommended:** Monthly, off-peak hours (2 AM weeknights)

```bash
# 1. Alert users (maintenance window)
# 2. Backup database
docker exec civicmesh-postgres pg_dump -U civicmesh -Fc civicmesh > /backups/pre-maintenance.dump

# 3. Update Docker images
docker pull postgres:16
docker pull minio/minio:latest
docker compose -f config/docker/docker-compose.yml pull

# 4. Restart services (minimal downtime)
docker compose -f config/docker/docker-compose.yml down
docker compose -f config/docker/docker-compose.yml up -d

# 5. Verify all services
docker compose ps  # all should be "running"

# 6. Run health checks
curl http://localhost:8000/health

# 7. Alert users (maintenance complete)
```

---

## Documentation References

- [PHASE_0_MVP_ARCHITECTURE.md](./PHASE_0_MVP_ARCHITECTURE.md) — System design
- [API_SPECIFICATION.md](./API_SPECIFICATION.md) — Endpoint specs
- [DATABASE_SCHEMA.md](./DATABASE_SCHEMA.md) — Data model details

---

**Last Updated:** 2026-06-05  
**Maintainer:** CivicMesh Platform Team
