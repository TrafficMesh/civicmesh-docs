# OPS-TM-001 TrafficMesh Node Operations Manual v1

**OPS-TM-001**
**D-CENTRAL GROUP**
**D-Central Group / CivicMesh Inc.**
TrafficMesh Node Operations Manual


| Document ID | OPS-TM-001 |
|---|---|
| Version | 1.0 |
| Status | Draft |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Operations |
| Applies To | D-Central Shield NOC operators, certified MSSP NOC operators |
| Related Documents | OS-PATROL-TM-001 | OS-PATROL-TM-FLEET-001 | DC-CM-NOC-001 | DC-CM-APP-003 |


# 1. Daily NOC Checklist
Execute in order every morning at 07:00 EST before municipal enforcement hours begin:


| Time | Check | Pass Criterion | Action on Fail |
|---|---|---|---|
| 07:00 | Fleet health overview — NOC Console → Fleet Health dashboard | All Tier 1 nodes active (green). Degraded nodes <5%. No tamper flags. | P1 alert for any tamper flag. P2 ticket for degraded >5%. Contact vehicle depot for offline Tier 1 nodes. |
| 07:05 | Evidence pipeline health — packages/hour, processing latency | Pipeline processing >50 packages/hour if fleet operational. Latency p95 <30 seconds. | P2 alert if latency >5 min. Check Kafka queue depth and MinIO storage utilization. |
| 07:10 | Chain of custody integrity — any COC failures overnight | Zero COC failures. Any failure is a P1. | Immediately suspend affected node DID. Initiate tamper investigation. Alert MSSP legal. |
| 07:15 | Confidence threshold distribution — review overnight queue | Fast-track queue processing normally. No queue backlog >100 packages. | If backlog: check officer portal login status. Contact municipal officer supervisor if portal unused overnight. |
| 07:20 | Credential expiry monitor — credentials expiring <30 days | No expired credentials in active fleet. <30-day warnings logged. | Generate renewal notification to credential holder. Escalate to MSSP supervisor if <7 days. |
| 07:25 | Storage utilization — MinIO buckets by community | No community bucket >80% capacity. | Trigger evidence retention purge for packages beyond TTL. Alert if purge insufficient. |
| 07:30 | Seasonal fleet status (Nov–Mar) — plow node activation | All deployed plow nodes active. Coverage map updated. | Contact fleet garage. Escalate if >3 plow nodes offline during active snow event. |


# 2. Firmware Update Protocol


| Update cadence | Platform pushes firmware updates monthly. Critical security patches pushed immediately with 2-hour deployment window. |
|---|---|
| Pre-deployment checklist | (1) Review firmware release notes — confirm no breaking changes to evidence schema. (2) Test on 3 non-production nodes for 24 hours. (3) Schedule deployment during low-enforcement window (02:00–05:00 EST). (4) Notify MSSP clients 48 hours before production deployment. |
| Deployment procedure | NOC Console → Fleet Management → Firmware Update → Select version → Stage to [community/tier] → Deploy. Platform pushes update to nodes in batches of 50. Each node: downloads, verifies hash against ATECC608B-stored expected hash, installs, reboots, sends confirmation ping. |
| Rollback trigger | If >5% of nodes fail to confirm within 2 hours of deployment, auto-rollback to previous version. NOC alerted. Post-mortem required before re-deployment. |
| Documentation | Each firmware deployment logged in NOC Console with: version deployed, node count, success/failure count, deployment duration, operator DID. Required for MSSP certification audit trail. |


# 3. Evidence Pipeline Integrity Monitoring


| Metric | Target | Warning Threshold | Critical Threshold | Response |
|---|---|---|---|---|
| Packages per hour (fleet active) | 50–500 depending on fleet size and time of day | <80% of 30-day average for same time window | <50% of 30-day average | Warning: check node connectivity. Critical: P1 incident, check ingestion API health |
| Processing latency p95 | <30 seconds | 30–120 seconds | <120 seconds | Warning: check Kafka consumer lag. Critical: scale ingestion workers. |
| Chain of custody failures | 0 per day | Any single failure | >3 failures in 24 hours | Single failure: suspend node, investigate. Multiple: P1 incident, notify legal team. |
| Confidence score distribution | >60% of packages ≥0.70 (qualifying for review) | <50% qualifying | <40% qualifying | Warning: check camera alignment and firmware version. Critical: dispatch technician. |
| Storage utilization | <70% per community | 70–85% | >85% | Warning: schedule retention purge. Critical: immediate purge, alert community manager. |


# 4. Contractor Kit Checkout/Return SOP
- City procurement department issues kit checkout request to D-Central Shield NOC — contractor name, contract number, vehicle type, expected duration.
- NOC retrieves kit from inventory, verifies: firmware version current, tamper seal intact, camera lens clean, OBD-II adapter functional, cellular SIM active.
- NOC activates kit: NodeActivationCredential issued for the kit's node DID, registered on Polygon, community ID set to city contractor network.
- Kit issued to contractor with LEGAL-CM-015 agreement and Quick-Start card. Contractor DID linked to kit record.
- Kit operates passively on contractor vehicle. NOC monitors kit health same as permanent fleet.
- On contract completion or expiry: contractor returns kit. NOC receives kit, inspects tamper seal, verifies firmware integrity.
- NOC deactivates kit: NodeActivationCredential revoked on Polygon within 24 hours. Local storage wiped. Data Clearance Certificate issued to contractor.
- Kit logged as available in inventory. Recertification scheduled if kit has been deployed >90 days.
# 5. Seasonal Fleet Deployment — Plow/Sweeper SOP


| Activity | Timing | Action | Responsible |
|---|---|---|---|
| Pre-season kit inspection | 2 weeks before first deployment (Oct 15 for plows, Mar 15 for sweepers) | Inspect all Tier 2 kits: IP67 seal integrity test, -40°C battery capacity check, firmware update, camera lens replacement if degraded. | Lead Technician (L2 minimum) |
| Fleet garage coordination | 1 week before deployment | Contact Public Works fleet garage: confirm installation schedule, assign technician teams to vehicle bays, confirm vehicle availability. | MSSP Account Manager |
| Installation week | Per seasonal deployment schedule | Technicians install kits per OS-PATROL-TM-FLEET-001 Tier 2 procedure. Each installation generates InstallationCredential. NOC activates each node as installed. | Certified Technicians (L2) |
| Season end decommission | 2 weeks after last expected use (Mar 31 for plows, Oct 31 for sweepers) | Retrieve kits from fleet garage, inspect, clean, firmware update, store. NodeActivationCredentials suspended (not revoked — re-activated next season). | Lead Technician + NOC |


# Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

