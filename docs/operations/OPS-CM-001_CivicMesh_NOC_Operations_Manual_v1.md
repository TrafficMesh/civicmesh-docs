# OPS-CM-001 CivicMesh NOC Operations Manual v1

**OPS-CM-001**
**D-CENTRAL GROUP**
**D-Central Group / CivicMesh Inc.**
CivicMesh NOC Operations Manual


| Document ID | OPS-CM-001 |
|---|---|
| Version | 1.0 |
| Status | Draft |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Operations |
| Applies To | D-Central Shield NOC operators, certified MSSP NOC operators |
| Related Documents | DC-CM-NOC-001 | DC-CM-APP-003 | DC-CM-FED-001 | OPS-TM-001 | DC-CM-MSSP-001 |


# 1. NOC Staffing Requirements


| MSSP Tier | Coverage Hours | Minimum Concurrent Operators | On-Call Requirement |
|---|---|---|---|
| Bronze | Business hours (08:00–20:00 M–F, 09:00–17:00 Sat) | 1 operator | 1 on-call after hours — P1 response within 4 hours |
| Silver | Extended (06:00–22:00 7 days) | 2 operators | 1 on-call overnight — P1 response within 1 hour |
| Gold | 24/7 | 2 operators at all times | No on-call — full coverage |
| D-Central Shield (founding MSSP) | 24/7 | 2 operators at all times | Full coverage for OC Transpo pilot and municipal clients |


# 2. Incident Priority Levels


| Priority | Definition | Examples | Response SLA | Escalation |
|---|---|---|---|---|
| P1 — Critical | Service impairment affecting enforcement operations or data integrity | Chain of custody failure, tamper event, evidence pipeline down, officer portal unreachable | Acknowledge: 15 min. Update: 30 min. Resolution target: 4 hours | Immediate: MSSP supervisor + D-Central Shield on-call + client notification |
| P2 — High | Significant degradation affecting service quality | >5% nodes offline, pipeline latency >5 min, credential expiry <7 days, federation request overdue | Acknowledge: 30 min. Update: 2 hours. Resolution target: 8 hours | MSSP supervisor within 1 hour |
| P3 — Medium | Service degradation without immediate enforcement impact | Single node offline, storage >80%, non-critical credential expiry 30–60 days | Acknowledge: 2 hours. Resolution target: 48 hours | Next business day supervisor review |
| P4 — Low | Informational — no immediate action required | Firmware update available, minor configuration drift, monthly report due | Acknowledge: next business day | No escalation — scheduled maintenance |


# 3. Federation Request Handling SOP
Federation requests from law enforcement must be processed carefully. The following procedure is mandatory — deviation is a compliance violation.
- Request arrives in NOC Console → Federation Requests queue. Timer starts immediately.
- NOC operator verifies requesting officer's DID credential: active, correct jurisdiction, OfficerAuthorizationCredential type. If verification fails: deny request, log reason, notify requesting officer's supervisor.
- Classify request by legal authority type: WARRANT, REASONABLE_GROUNDS, EXIGENT, ROUTINE.
- EXIGENT (immediate threat to life): approve immediately, notify MSSP legal within 30 minutes, community notification within 24 hours.
- WARRANT: verify warrant document attached. If yes: approve, log, notify community. If no: request warrant document, set 48-hour response window.
- REASONABLE_GROUNDS: flag for MSSP legal review. Do NOT approve without legal review sign-off. Legal review target: 4 hours.
- ROUTINE (within standard federation scope): approve if within the requesting municipality's Data Federation Agreement scope. Log and notify community.
- All approvals generate: immutable audit log entry, community notification dispatch, evidence package assembly with chain of custody documentation.
- No request may remain in queue for more than 4 hours without a status update. P2 alert if approaching 4 hours without resolution.
# 4. Audit Log Integrity Monitoring


| What the audit log is | An append-only PostgreSQL table. Once written, records cannot be modified or deleted. Any attempt to modify triggers an immediate SOC alert. |
|---|---|
| Daily integrity check | NOC Console runs automated daily check: last 24 hours of log entries verified against expected hash chain. Any gap or hash mismatch is a P1 incident. |
| Monthly reconciliation | NOC operator manually reconciles audit log entry count against: evidence package count, federation request count, citation count, privacy complaint count. Discrepancies escalated to MSSP supervisor. |
| Audit log export | For MSSP certification audits and community transparency reports, export is generated via NOC Console → Audit → Export. Exports are digitally signed with MSSP operator DID and cannot be modified post-export. |


# 5. Community Transparency Report Generation
- NOC Console → Reports → Annual Transparency Report → Select community → Select year
- System auto-populates all fields from audit log: node count, incident volume, citation count, dismissal rate, federation events, privacy complaints, AI false positive rate.
- NOC operator reviews auto-generated draft for accuracy. Flag any anomalies for manual review before submission to community manager.
- Submit to community manager for review via NOC Console message. Community manager has 14 days to review and request corrections.
- Community manager approves with their DID signature. Report is published to civicmesh.ca/transparency/{community-id}/{year} and distributed to CCSC members.
- Report publication logged in audit trail with community manager DID signature timestamp.
# Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

