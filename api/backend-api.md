# Backend API Reference

## Evidence Submission
**POST /incidents/submit**
```json
{
  "node_id": "OC-001",
  "violation_type": "bus_lane",
  "plate": "ABC1234",
  "location": [45.42, -75.69],
  "timestamp": "2026-06-12T10:30:00Z",
  "confidence": 0.97
}
```

## Officer Portal
- **GET /portal/queue** - Queue summary
- **GET /portal/incidents/{id}** - Incident details
- **POST /portal/incidents/{id}/approve** - Issue citation
- **POST /portal/incidents/{id}/reject** - Reject incident
- **POST /portal/incidents/{id}/escalate** - Escalate
