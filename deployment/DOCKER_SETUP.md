# Docker Setup for Local Development

## Start Services
```bash
cd civicmesh-backend
docker compose -f config/docker/docker-compose.yml up
```

## Service Endpoints
- **PostgreSQL**: localhost:5432
- **MinIO Console**: localhost:9001
- **Backend API**: localhost:8000
- **RabbitMQ Console**: localhost:15672

## Health Check
```bash
curl http://localhost:8000/health
```

## Stop Services
```bash
docker compose down
```
