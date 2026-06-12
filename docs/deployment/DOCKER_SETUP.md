# Docker Setup

Start services:
\`\`\`bash
cd civicmesh-backend
docker compose -f config/docker/docker-compose.yml up
\`\`\`

Endpoints:
- PostgreSQL: localhost:5432
- MinIO: localhost:9001
- Backend: localhost:8000
- RabbitMQ: localhost:15672
