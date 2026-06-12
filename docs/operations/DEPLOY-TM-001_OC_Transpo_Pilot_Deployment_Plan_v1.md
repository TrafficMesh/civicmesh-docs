# DEPLOY-TM-001 OC Transpo Pilot Deployment Plan v1

**DEPLOY-TM-001**
**D-CENTRAL GROUP**
**TrafficMesh Technologies Inc. / CommunityShield Inc.**
TrafficMesh OC Transpo Pilot Deployment Plan


| Document ID | DEPLOY-TM-001 |
|---|---|
| Version | 1.0 |
| Status | Draft |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Deployment |
| Pilot Client | OC Transpo — Ottawa's public transit authority |
| Pilot Scope | 30 buses, 3 high-priority corridors, 6-month pilot period |
| Primary Contact | [OC Transpo VP Operations / Innovation Lead — to be confirmed] |
| Related Documents | OS-PATROL-TM-001 | OS-PATROL-TM-EV-001 | MKT-TM-GOV-007 | REG-TM-001 | IRAP-TM-001 |


# 1. Pilot Rationale — Why OC Transpo First


| Argument | Detail |
|---|---|
| Regulatory path — no new legislation | Vehicle-mounted enforcement on transit authority vehicles operates under existing HTA authority. Legal opinion (REG-TM-001) confirms OC Transpo can implement this without provincial amendment. The Ford government's ASE ban explicitly covers fixed cameras — mobile, vehicle-mounted systems are not covered. |
| NYC MTA precedent | New York's ACE programme (bus-mounted cameras) issued 76.4% of all bus lane and bus stop violations in December 2024. $225M in fines over 24 months. Bus speed increased 5%, collisions reduced 20%. Ottawa can point to a proven, operational, large-scale reference deployment. |
| Revenue urgency | Ottawa's ASE ban eliminated $38.7M in annual enforcement revenue. OC Transpo operates bus lane and bus stop enforcement under its own authority — TrafficMesh fills this gap immediately without requiring new city council approval. |
| Political safety | City-owned buses are the least controversial possible starting point. No civilian surveillance concerns. No resident privacy debates. Just putting cameras on buses that already drive the routes. |
| Beachhead for city fleet | A successful OC Transpo pilot is the evidence package for the City of Ottawa fleet conversation. 'It worked on buses — here's the data — now let's deploy on bylaw vehicles and plows.' |


# 2. Pilot Scope — Phase 1


| Parameter | Specification |
|---|---|
| Pilot duration | 6 months from first node activation on OC Transpo bus |
| Bus fleet enrolled | 30 buses — Tier 1 permanent installation (hardwired 12V, custom bracket per bus model) |
| Target corridors | (1) Transitway — Ottawa's dedicated bus rapid transit corridor (highest bus lane violation density); (2) Bank Street (downtown — high bus stop obstruction); (3) Albert/Slater Street (downtown core — bus lane + stop violations) |
| Violation types active | Bus stop obstruction (Phase 1 only — highest confidence, clearest legal authority). Bus lane obstruction (Phase 1 if HTA legal opinion confirms authority). Speed violations (advisory only — not enforced in Phase 1 until speed measurement accuracy target achieved). |
| Officer review | City of Ottawa Bylaw and Regulatory Services provides 2 trained officers for pilot review queue. Target: all Fast-Track packages reviewed within 4 hours, all Standard packages within 24 hours. |
| Citation authority | Provincial Offence Notice issued under HTA s.170 (stop obstruction) and municipal bus lane bylaw. Human officer review + DID signature mandatory on every citation. |
| Revenue model | Ottawa receives 65% of net citation revenue. TrafficMesh / D-Central Shield receives 20% (MSSP fee). CCSC general fund receives 5%. OC Transpo reinvestment fund receives 10%. |


# 3. Installation Timeline


| Week | Activity | Responsible | Milestone |
|---|---|---|---|
| 1–2 | Fleet garage access coordination. Vehicle type survey (articulated vs. standard bus — 3 bracket variants). Schedule installation windows (buses typically available 02:00–06:00). | MSSP Account Manager + OC Transpo Fleet | Installation schedule confirmed |
| 3–5 | Node installation — 30 buses, 6 buses per day. 2 certified L2 technicians per day. InstallationCredentials issued for each bus. | Certified Technicians (L2) supervised by L3 | 30 InstallationCredentials on Polygon |
| 6 | System integration testing — officer portal access for 2 OC Transpo-designated officers. Test evidence packages reviewed and approved. NOC monitoring confirmed active. | D-Central Shield NOC + OC Transpo officers | Officer portal access confirmed, test citations issued |
| 7 | Soft launch — nodes active, evidence pipeline running, review queue monitored. No citations issued yet — observation period. | NOC + Officers | First 100 real evidence packages reviewed |
| 8 | Go-live — citations begin. Weekly revenue report to OC Transpo. Public communication: 'OC Transpo Bus Safety Camera Programme.' | All parties | First citations issued and revenue flows confirmed |
| 9–24 | Active pilot operation. Monthly KPI reports. Mid-pilot review at Month 3 to assess expansion to additional routes. | NOC + Account Manager | Monthly KPI reports delivered |
| 25–26 | Pilot close-out report. Evidence package for City of Ottawa fleet expansion pitch. | TrafficMesh Account Manager | Close-out report delivered to OC Transpo and City of Ottawa |


# 4. KPI Framework


| KPI | Target | Measurement Method | Reporting Frequency |
|---|---|---|---|
| Enforcement revenue generated | $150,000+ over 6-month pilot | Citation system revenue log | Monthly |
| Evidence package volume | 500+ packages per month at steady state | Ingestion API metrics | Monthly |
| Fast-track queue clearance time | <4 hours average | Officer portal audit log | Monthly |
| Dismissal rate | <25% (indicating AI accuracy is meeting standard) | Officer portal dismissal codes | Monthly |
| False positive rate by violation type | <15% for bus stop obstruction | Dismissal code analysis | Monthly |
| System uptime | ≥99.5% during enforcement hours (06:00–22:00) | NOC Console uptime log | Monthly |
| Contested citation rate | <5% of issued citations | OC Transpo / city legal records | Monthly |
| Bus lane speed improvement (proxy) | Measurable improvement on pilot corridors vs pre-pilot | OC Transpo operational data | At pilot close |
| Collision reduction on pilot corridors | Measurable trend (full measurement requires longer observation) | City of Ottawa collision data | At pilot close + 6-month post-pilot |


# 5. Public Communication Plan


| Announcement timing | Announce 30 days before go-live. Allow public comment period before first citations issued. |
|---|---|
| Key messages | (1) Human officer reviews every citation before it is issued — no fully automated ticketing; (2) Cameras are on existing OC Transpo buses — no new infrastructure; (3) Revenue supports transit operations; (4) Bus lanes are safer and faster when they are enforced consistently. |
| Media inquiries | Directed to OC Transpo Communications + D-Central Shield communications contact. TrafficMesh's privacy architecture documentation published proactively at civicmesh.ca/transparency. |
| Rider/public FAQ | Published on OC Transpo website and CivicMesh transparency portal: What does the camera capture? (violations in bus lanes and at bus stops) Does it record continuously? (No — event-triggered) Who reviews the footage? (Trained officers before any ticket is issued) How do I contest a ticket? (Same process as any provincial offence notice) |


# Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

