# LEGAL-CM-015 Contractor Node Kit Agreement v1

**LEGAL-CM-015**
**D-CENTRAL GROUP**
**TrafficMesh Technologies Inc.**
Contractor Node Kit Agreement — Template


| Document ID | LEGAL-CM-015 |
|---|---|
| Version | 1.0 — Template |
| Status | Draft — incorporated into city contractor procurement |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Legal |
| Parties | TrafficMesh Technologies Inc. (Kit Provider) and [Contractor Name] (Contractor) |
| Agreement Type | Equipment custody and data governance agreement — incorporated by reference into city contractor agreement |
| Related Documents | LEGAL-CM-011 | DEPLOY-TM-002 | DC-CM-HW-001 | DC-CM-NODE-PROV-001 | OPS-TECH-001 |




| Executive Summary
The Contractor Node Kit Agreement governs city contractors who receive modular CivicMesh node kits as a condition of their city contract. The kit is checked out from the city like safety equipment — it activates when plugged in, auto-enrolls the contractor's vehicle, runs passively while the contractor works, and de-enrolls when the kit is returned. The contractor's vehicle becomes a temporary network node while on city contract. This agreement covers custody, tamper obligations, data ownership (city and community, not contractor), and kit return procedures. |
|---|


# 1. Kit Description and Issue


| Kit Contents | CivicMesh Contractor Node Kit CM-TM-TIER3-001: weatherproof edge compute module, forward-facing camera (1080p minimum), GPS module, OBD-II or 12V adapter, cellular SIM (city-provisioned), magnetic/suction mount hardware, tamper-evident seal, quick-start card |
|---|---|
| Kit Issue | Kit issued by [City Department — Public Works / Fleet Management] at contract commencement. Contractor signs this agreement as a condition of kit receipt. |
| Kit Identification | Each kit has a unique serial number and an active Node DID registered on the CivicMesh blockchain. Kit serial number is linked to contractor's company registration and contract number in the city's procurement system. |
| No Purchase | Contractor does not purchase the kit. It is city property in the custody of the contractor for the duration of the city contract. |


# 2. Contractor Obligations


| Obligation | Details | Consequence of Breach |
|---|---|---|
| Installation | Install kit on the primary vehicle used for city contract work within 2 business days of kit receipt. Follow installation guide exactly. Do not modify the installation. | Kit deactivated. Contractor notified. City notified if kit not installed within 5 business days. |
| Tamper Prohibition | Do not open the kit housing, cover the camera, obstruct the GPS antenna, disconnect the cellular module, or interfere with the kit's operation in any way. | Immediate tamper event logged to blockchain. Kit DID suspended. City notified immediately. Potential contract breach. |
| Connectivity | Ensure kit remains powered and connected during all city contract work. Do not disable the kit's cellular connection. | Kit health alert sent to City NOC. Contractor contacted for explanation. |
| Kit Security | Store the kit securely when not in use. Do not loan the kit to any other person or use it on any vehicle other than the registered vehicle without prior written approval. | Unauthorized use logged. City notified. Contractor liable for any unauthorized data collected. |
| Kit Return | Return the kit to [City Department] within 2 business days of contract completion or termination. Kit must be in the same condition as issued (normal wear and tear excepted). | Late return: city may deduct replacement cost from contractor holdback. Damaged/missing kit: contractor liable for replacement cost at $[TBD]. |
| Cooperation with Inspection | Allow TrafficMesh Technologies Inc. or city representatives to inspect the kit installation on 24-hour notice. | Failure to cooperate is a contract breach reportable to the city. |


# 3. Data Ownership and Contractor Privacy


| Data Ownership | All data collected by the kit while on city contract — evidence packages, GPS tracks, video clips, telemetry — is owned by the City of [Municipality] and subject to the Municipal Data Federation Agreement (LEGAL-CM-011). Contractor has no ownership rights in any data collected by the kit. |
|---|---|
| Contractor Vehicle Data | OBD-II data (vehicle speed, position, heading) is collected for the purpose of anchoring evidence packages geospatially. This data is used solely for enforcement evidence purposes and is not used to monitor contractor work performance, driving behaviour, or route compliance (beyond what is necessary for evidence package generation). |
| Contractor Identity Protection | Contractor identity (company name, driver identity) is not embedded in evidence packages. Packages identify the node ID and contract number. Driver identity is not disclosed in enforcement actions. |
| No Personal Monitoring | The kit is not a contractor monitoring device. It is a road safety enforcement device. CivicMesh Inc. and the city agree not to use kit data to monitor contractor compliance with work schedules, route requirements, or employment obligations. |
| Privacy of Third Parties | Faces and personal vehicle plates of non-violating vehicles are blurred by the kit before any data is transmitted. The kit does not collect personal information beyond what is necessary for violation detection. |


# 4. Contractor Benefits


| Construction Zone Protection | When a contractor's kit is active, the CivicMesh platform automatically activates a construction zone enforcement envelope around the contractor's vehicle location. Speed violations within this envelope are flagged and enforced. This protects the contractor's workers from speeding vehicles at no additional cost to the contractor. |
|---|---|
| Safety Record | Participating contractors receive an annual CivicMesh Contractor Safety Participation certificate — a documented record that their operations contributed to road safety enforcement. This can be used in future contract bids as a safety credential. |
| Optional Civilian Extension | Contractors whose employees wish to connect personal dashcams to the civilian opt-in programme (and earn patronage revenue) are free to do so through the standard CCSC membership process. This is entirely voluntary and separate from this agreement. |


# 5. Kit Return and Data Wipe
- Contractor returns kit to [City Department] within 2 business days of contract completion or termination.
- City department confirms kit physical condition and serial number.
- TrafficMesh Technologies Inc. deactivates the kit's Node DID on the blockchain within 24 hours of return confirmation.
- Kit's local storage (any evidence packages buffered locally and not yet uploaded) is wiped within 24 hours of deactivation.
- Kit is inspected, recertified if required, and prepared for reissue to next contractor.
- Contractor receives a Data Clearance Certificate confirming all kit data has been wiped and the Node DID is deactivated.
# 6. City Integration
This agreement is intended to be incorporated by reference into the city's standard contractor agreement. The following clause should be added to all city contracts where node kit deployment applies:


| Recommended Contract Clause for City Procurement
• "As a condition of this contract, the Contractor agrees to receive, install, operate, and return a CivicMesh Contractor Node Kit in accordance with the CivicMesh Contractor Node Kit Agreement (Document LEGAL-CM-015), which is incorporated by reference into this contract. The Contractor acknowledges that the kit is city property, that all data collected by the kit is city property, and that any tampering with or unauthorized use of the kit constitutes a material breach of this contract." |
|---|


# 7. Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

