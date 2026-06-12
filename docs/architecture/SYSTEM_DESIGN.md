# System Design Overview

## Layers
- Edge: RPi nodes with TFLite inference
- Backend: FastAPI + PostgreSQL + PostGIS + MinIO
- Portal: Officer React UI + analytics

## Data Flow
Node → Backend (HMAC) → Dedup (PostGIS) → Queue → Officer → Citation

## Tech Stack
FastAPI, PostgreSQL, PostGIS, MinIO, Redis, RabbitMQ, React, TFLite, K8s
