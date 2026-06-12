# DC-CM-NOC-001 NOC Dashboard Specification v1

**DC-CM-NOC-001**
**D-CENTRAL GROUP**
**CivicMesh Inc.**
CivicMesh NOC / Community Dashboard Specification


| Document ID | DC-CM-NOC-001 |
|---|---|
| Version | 1.0 |
| Status | Draft |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Technical |
| SR&ED Eligibility | Moderate — novel real-time multi-community node health aggregation and transparency reporting system |
| Related Documents | DC-CM-ARCH-001 | DC-CM-APP-003 | DC-CM-APP-009 | DC-CM-GOV-003 |




| Executive Summary
The CivicMesh NOC dashboard serves two distinct users: the MSSP NOC operator (technical operations monitoring across all managed communities) and the community manager (HOA/condo/campus operational view for their specific community). Both share the same underlying data but see different views filtered by their credential scope. This document specifies the functional requirements and data models for both views. |
|---|


# 1. MSSP NOC View — Functional Requirements


| Feature | Description | Data Source | Alert? |
|---|---|---|---|
| Fleet Health Overview | Real-time node health across all managed communities — active, degraded, offline, tamper-flagged counts with drill-down | Node heartbeat API, tamper event stream | Yes — offline >15 min, tamper event |
| Node Map | Geographic map of all managed nodes with colour-coded health status. Click node for detail (last ping, firmware version, uptime, last evidence package). | PostGIS + node registry | No — visual only |
| Evidence Pipeline Health | Packages per hour, processing latency, upload failures, storage utilisation by community | Ingestion API metrics | Yes — pipeline lag >5 min |
| Chain of Custody Monitor | Any evidence package where signature verification fails is flagged immediately with node ID and package ID | Evidence verification service | Yes — any COC failure |
| Federation Request Queue | Pending law enforcement data requests awaiting MSSP legal review. Shows: request type, legal authority, data scope, time in queue. | Federation request store | Yes — any request >4 hours in queue |
| SLA Compliance Dashboard | Uptime %, incident response times, P1/P2/P3 open tickets vs SLA targets, by community | Incident management integration | Yes — SLA breach imminent |
| Credential Expiry Monitor | All credentials in managed communities approaching expiry — technician, MSSP, node activation. 90/60/30 day warnings. | VC registry | Yes — 30 days to expiry |
| Transparency Report Generator | One-click generate annual transparency report for any community. Pre-filled from audit log data. | Audit log database | No |


# 2. Community Manager View — Functional Requirements


| Feature | Description | Access Control |
|---|---|---|
| Community Node Map | Nodes within this community only — location, status, last active time. Visible to all community members (public-facing version shows node locations without operational detail). | Community manager + public (location only) |
| Incident Queue | Evidence packages requiring community manager review (private property enforcement). Shows clip, GPS, confidence score. One-tap confirm/dismiss with reason code. | Community manager only |
| Revenue Dashboard | Fine revenue this month, this year, all-time. Breakdown by violation type. CCSC patronage distributions to date. Insurance premium reduction certification status. | Community manager + board |
| Member Management | Node owner registry — name, DID, consent status, active nodes, patronage earned. Member onboarding workflow. Consent renewal reminders. | Community manager only |
| Law Enforcement Request Log | Every law enforcement data request — date, type, legal authority, status (approved/denied/pending), notification sent. Full detail visible to community manager. | Community manager only — not visible to general members |
| Annual Transparency Report | Read-only view of the published annual report. Download PDF. Share link for community distribution. | Community manager + all members |
| Privacy Complaints | Receive and respond to member privacy complaints. Log resolution. Export for annual report. | Community manager only |


# 3. Data Architecture


| Multi-tenant isolation | Each community has an isolated PostgreSQL schema. NOC view aggregates across schemas with explicit permission checks — no cross-tenant data leakage possible at query level. |
|---|---|
| Real-time updates | Node health events streamed via Apache Kafka → Server-Sent Events to NOC dashboard. No polling — push-based updates for all health metrics. |
| Audit log immutability | All NOC actions (federation approvals, credential revocations, report generation) are appended to the audit log and cannot be modified. NOC operators cannot edit audit records. |
| Access logging | Every NOC and community manager action is logged with operator DID, timestamp, action type, and data scope accessed. This log is itself auditable by the MSSP certification auditor. |


# 4. Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

