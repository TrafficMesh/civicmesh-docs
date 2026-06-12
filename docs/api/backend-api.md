# Backend API Reference

## Evidence Ingestion
POST /incidents/submit - Upload evidence

## Officer Portal
GET /portal/queue - Queue summary
GET /portal/incidents/{id} - Details
POST /portal/incidents/{id}/approve - Issue citation
POST /portal/incidents/{id}/reject - Reject
POST /portal/incidents/{id}/escalate - Escalate
