# DC-CM-FED-001 CivicMesh Federation Protocol v1

**DC-CM-FED-001**
**D-CENTRAL GROUP**
**CivicMesh Inc.**
CivicMesh Federation Protocol


| Document ID | DC-CM-FED-001 |
|---|---|
| Version | 1.0 |
| Status | Draft — SR&ED: novel protocol |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Technical |
| SR&ED Eligibility | Primary — novel privacy-preserving upward data federation architecture with no existing comparable implementation |
| Related Documents | DC-CM-ARCH-001 | DC-CM-PRV-001 | DC-CM-GOV-003 | LEGAL-CM-011 | REG-CM-001 |




| Executive Summary
The CivicMesh Federation Protocol defines how data flows upward from individual community nodes through community networks to municipal, regional, and provincial/federal layers. The fundamental design principle is consent-first federation: data moves upward only with explicit, auditable, community-level consent. Every federation event is logged immutably, every affected community is notified, and every federation decision is reversible (communities can withdraw from federation agreements). This protocol is a novel contribution — no existing system implements privacy-preserving, community-auditable, reversible upward data federation for enforcement infrastructure. |
|---|


# 1. Federation Hierarchy


| Layer | Entity | Data They Can Access | Consent Required | Audit Visibility |
|---|---|---|---|---|
| Layer 0 — Node | Individual node device | Own evidence packages only | N/A — generates data | Node owner via Member App |
| Layer 1 — Community | HOA / Condo / Campus NOC | All evidence packages from community nodes | Community operator agreement (LEGAL-CM-012) | Community manager via NOC dashboard |
| Layer 2 — Municipal | City bylaw, OC Transpo, Ottawa Police | Evidence packages for citations in jurisdiction — per federation agreement | Municipal Data Federation Agreement (LEGAL-CM-011) | City via officer portal + public audit log |
| Layer 3 — Regional | Regional police, county | Investigation-specific requests only — no automatic federation | Formal investigation request with legal authority | Public audit log (case ref only) |
| Layer 4 — Provincial/Federal | OPP, RCMP, federal departments | Investigation-specific — warrant or equivalent required | Court order or equivalent | Public audit log + community notification |


# 2. Standard Evidence Federation (Layer 1→2)
Standard federation of enforcement evidence packages from community to municipal layer occurs automatically for packages meeting the following criteria:
- Package confidence score ≥ 0.70 (below threshold packages are not federated);
- Violation type is within the scope of municipal enforcement authority (per Municipal Data Federation Agreement);
- Community has an active Municipal Data Federation Agreement with the receiving municipality;
- The node that generated the package is on a public road (not on private property — private property enforcement stays at Layer 1).
Standard federation is logged to the community audit log but does not trigger individual community member notification (volume would be excessive for routine enforcement). The community aggregate count of federated packages is shown in the monthly NOC dashboard summary and annual transparency report.
# 3. Investigation-Specific Federation (Layer 2→3→4)
Any federation above Layer 2, or any request for data outside the standard evidence package (historical footage, acoustic clips, cross-community plate queries) follows the investigation-specific protocol:


| Step | Action | System Behavior | Timing |
|---|---|---|---|
| 1 — Request submission | Requesting officer submits through Officer Portal: case number, legal authority type, specific data sought, time window, justification | Platform validates officer DID credential, confirms active Municipal Partner status, creates pending request record | Immediate |
| 2 — Legal authority classification | System classifies request by legal authority: WARRANT (court order), REASONABLE_GROUNDS (sworn officer attestation), EXIGENT (immediate threat to life), ROUTINE (standard enforcement authority) | Classification determines automatic approval vs. human review requirement | <30 seconds |
| 3 — Review routing | WARRANT and EXIGENT → automatic approval with notification. REASONABLE_GROUNDS → MSSP legal review flag. ROUTINE → automatic if within standard federation scope | Review queue notification sent to MSSP NOC if human review required | Immediate |
| 4 — Audit log entry | Immutable audit log entry created: timestamp, requesting officer DID (not name), legal authority type, data scope, community IDs affected (not the data itself) | Audit log entry is permanent and cannot be modified or deleted | Immediate |
| 5 — Community notification | Notification dispatched to all affected community managers: 'A [legal authority type] data request has been approved for [data type] from [time window]. Reference: [case number].'' | Email + NOC dashboard alert | Within 24 hours (may delay up to 30 days with judicial authorization for active investigations) |
| 6 — Data package generation | Approved data retrieved, packaged with full chain of custody documentation, delivered to requesting officer's secure evidence workspace | Evidence package with COC documentation | Within 4 hours of approval |
| 7 — Post-case purge | On case closure or retention period expiry, data automatically purged from requesting party's access | Purge confirmation logged to audit trail | Automatic on trigger |


# 4. Consent Gate Implementation


| Technical Challenge (SR&ED) | The consent gate must atomically execute two operations: (1) record the federation approval in the audit log, and (2) dispatch the community notification. If either operation fails, the other must be rolled back to prevent silent data access without notification — or notification of an access that didn't occur. |
|---|---|
| Current Approach (v3) | Database transaction wrapping both the audit log write and the notification queue publish. If the queue publish fails, the transaction rolls back and the request returns to pending. The notification is then retried asynchronously. |
| Rejected Approaches | v1: Two-phase commit across audit log and notification service — too complex and introduced distributed deadlock risk. v2: Audit log write first, notification on success — failed atomicity guarantee (audit log could succeed while notification fails silently, leaving community unaware of data access). |
| Remaining Uncertainty | Whether database transaction approach maintains performance at scale (1,000+ concurrent federation requests from a major event) — this is an active area of investigation. Apache Kafka event sourcing is being evaluated as an alternative. |


# 5. Public Transparency Implementation


| Public Audit Log | Published at civicmesh.ca/transparency. Updated in real time. Shows: date, data type requested, legal authority type, number of communities affected. Does NOT show: case details, officer identity, investigation content. |
|---|---|
| Community Dashboard | Each community's NOC dashboard shows the community's own federation history in full detail (all fields visible to community manager). Not visible to other communities. |
| Annual Transparency Report | Generated automatically from audit log data. Structure defined in DC-CM-GOV-003. Published 30 days before CCSC AGM. Includes false positive rate, dismissal rate, and AI model performance alongside federation statistics. |
| DeFlock Countermeasure | The transparency infrastructure is specifically designed to eliminate the political vulnerability that destroyed Flock Safety's community relationships. When any resident can see exactly when and why their community's data was accessed, the 'secret surveillance' narrative cannot gain traction. |


# 6. Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

