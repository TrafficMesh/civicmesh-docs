# DEPLOY-MP-001 MeshPlate Municipal Deployment Package v1

**DEPLOY-MP-001**
**D-CENTRAL GROUP**
**TrafficMesh Technologies Inc. / CommunityShield Inc.**
MeshPlate Municipal Deployment Package


| Document ID | DEPLOY-MP-001 |
|---|---|
| Version | 1.0 |
| Status | Draft |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Deployment |
| Target | City of Ottawa, Ottawa Police Service, municipal parking authority |
| Prerequisite | MeshPlate regulatory analysis (REG-MP-001) and ALPR legal opinion completed |
| Related Documents | DC-MP-ARCH-001 | DC-MP-FED-001 | REG-MP-001 | LEGAL-CM-011 | DC-CM-APP-008 |


# 1. Municipal MeshPlate Value Proposition


| Department | Problem Solved | MeshPlate Solution | Estimated Annual Value |
|---|---|---|---|
| Ottawa Police — Traffic Unit | Limited ALPR coverage — only police cruisers have ALPR. Stolen vehicles and suspended plates routinely pass without detection on streets without cruiser presence. | City fleet nodes run MeshPlate continuously. Any stolen vehicle or suspended plate passing any city vehicle is detected in real time. Police receive an alert with clip, GPS, and timestamp. | Improved stolen vehicle recovery rate. Suspended plate revenue from HTA citations. Conservative: $200K–$500K in incremental enforcement outcomes. |
| Ottawa Parking Authority | Limited parking enforcement coverage downtown. Permit enforcement requires officers to physically check every vehicle. | MeshPlate nodes on bylaw and parking enforcement vehicles check every plate they pass. Permit violators flagged automatically for officer confirmation. | $500K–$1M in incremental parking violation revenue at city enforcement volumes. |
| Ottawa Police — Amber Alert | Amber Alert activation relies on public sightings. Detection in Ottawa is slow and unreliable. | MeshPlate on all city fleet vehicles creates a 200+ vehicle detection network. Target plate pushed to all nodes in real time. Any detection immediately reported to dispatch. | Public safety outcome — not measured in revenue but in child recovery. |
| OC Transpo — Security | Limited ability to detect vehicles associated with transit incidents. | MeshPlate on OC Transpo buses provides automatic plate logging at transit hubs and along routes. | Incident investigation support. Complements TrafficMesh enforcement data. |


# 2. Privacy-First Deployment Architecture


| Hash-before-store confirmed | All plate reads stored as SHA-256 hashes with community-specific salt. No plaintext plate database. Police cannot query 'all locations of plate ABC-123' without a specific investigation request through the formal federation process. |
|---|---|
| No national database connection | MeshPlate Ottawa data stays in Ottawa. No connection to Flock Safety, Motorola Vigilant, or any national private ALPR database. Data is owned by the City of Ottawa. |
| City council transparency reporting | Annual report to City Council: number of plates scanned, number of stolen vehicle detections, number of suspended plate alerts, number of Amber Alert activations, number of formal police investigation queries, number of queries approved/denied. Published publicly. |
| Privacy oversight | Ottawa's Information and Privacy Commissioner receives a copy of the annual MeshPlate transparency report. Any privacy complaints from residents about MeshPlate data are reportable to the IPC. |


# 3. Police Integration Protocol
- Ottawa Police Service provides a formal list of designated officers authorized to submit MeshPlate queries through the Officer Portal.
- D-Central Group issues OfficerAuthorizationCredentials to each designated officer's DID. Credential specifies: jurisdiction (Ottawa), authorized query types (stolen vehicle, suspended plate, active investigation).
- On Amber Alert activation: OPS sends alert with target plate to CivicMesh NOC via secure API. NOC pushes plate hash to all active Ottawa fleet nodes. Nodes flag any match and alert OPS dispatch within seconds.
- On routine investigation query: officer submits through Officer Portal with case number and reasonable grounds documentation. Standard federation protocol applies — logged, community notified, data purged post-case.
- Monthly coordination meeting between CivicMesh MSSP account manager and OPS traffic unit — review detection rates, false positive rate, system performance, any policy concerns.
# 4. City Council Presentation Framework


| Lead message | Ottawa owns the network, Ottawa controls the data, and Ottawa earns the revenue. Unlike Flock Safety (American company, private database, no community benefit), MeshPlate Ottawa is city infrastructure operated by a local cooperative technology company. |
|---|---|
| Privacy reassurance | Council's primary concern will be civil liberties. Lead with the hash-before-store architecture, the no-national-database covenant, the formal query requirement for police access, and the public transparency reporting. These structural protections — not policy promises — are the answer. |
| Revenue argument | Present the parking authority and HTA enforcement revenue projections alongside the capital cost. The network pays for itself. Annual transparency report to council shows exactly what was detected, queried, and enforced. |
| Comparison to Flock | If councillors ask about Flock Safety: 'Flock stores plaintext plates in a private US database, shares data with federal agencies without transparency, and returns zero revenue to the community. MeshPlate Ottawa does none of those things. The source code is publicly auditable at GitHub.' |


# Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

