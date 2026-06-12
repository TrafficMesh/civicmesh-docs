# IRAP-TM-001 NRC IRAP Application TrafficMesh v1

**IRAP-TM-001**
**D-CENTRAL GROUP**
**TrafficMesh Technologies Inc.**
NRC IRAP Application — TrafficMesh Platform


| Document ID | IRAP-TM-001 |
|---|---|
| Version | 1.0 |
| Status | Draft — submit before any TrafficMesh R&D spending |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Funding Application |
| Programme | NRC Industrial Research Assistance Programme (IRAP) |
| Relationship to IRAP-CM-001 | Companion application — CivicMesh platform-level work covered by IRAP-CM-001; TrafficMesh-specific enforcement, fleet, and digital twin R&D covered here |
| Public Safety Alignment | Vehicle-mounted enforcement, law enforcement ALPR support, Amber Alert integration, hit-and-run evidence — direct public safety mandate |
| Related Documents | IRAP-CM-001 | SRED-TM-001 | LOAN-CM-001 | LEGAL-TM-001 | DEPLOY-TM-001 |




| Executive Summary
This application covers TrafficMesh-specific R&D: the hardware node stack, the enforcement AI models, the OBD-II telemetry fusion system, the digital twin engine, and the evidence pipeline optimised for vehicle-mounted enforcement. While IRAP-CM-001 covers platform-level architecture and privacy systems, this application focuses on the technical work performed by TrafficMesh Technologies Inc. as the operating entity building and deploying the enforcement product. |
|---|


# 1. TrafficMesh-Specific Technical Uncertainties


| Uncertainty | Technical Challenge | Why Not Routine |
|---|---|---|
| Tier 2 Seasonal Node Operation | Operating edge compute hardware on snowplow and street sweeper fleets at -30°C with salt spray, vibration, and intermittent power during blade operation. Current commercial dashcam systems are not rated for this environment or for the specific power draw profiles of municipal heavy equipment. | No off-the-shelf solution exists. Requires novel enclosure design (IP67+), power management firmware, and cold-start protocols for Jetson Orin at extreme temperatures. |
| Digital Twin Continuous Update from Mobile Nodes | Fusing georeferenced image frames from 200+ moving vehicles into a continuously updated, queryable spatial model of Ottawa's road infrastructure — sign condition, lane marking visibility, pavement condition, construction zone boundaries — with sub-24-hour update latency for any given road segment. | Existing digital twin systems (Nexar CityStream) use civilian dashcams with random coverage. A city-fleet-driven twin with systematic coverage requires novel routing, deduplication, and confidence-scoring algorithms that have not been published or commercialized. |
| Evidence Package Legal Admissibility Optimization | Designing the evidence package schema and chain-of-custody protocol to satisfy the specific requirements of the Ontario Highway Traffic Act for automated enforcement evidence — including the technical specification of what constitutes a 'complete' evidence record sufficient to support a provincial offence notice without an in-person officer. | The HTA has no existing technical standard for vehicle-mounted camera evidence. The technical design must anticipate the legal requirements through research with legal counsel, creating genuine technical and legal uncertainty. |
| Contractor Node Kit 5-Minute Deploy | Designing a self-contained, weatherproof, tamper-evident enforcement node kit that a non-technical city contractor can install in under 5 minutes on any vehicle type — from a dump truck to a line painter to a parks vehicle — without tools and without technical training. | The constraint set (universal vehicle compatibility, <5 minute install, tamper-evident, IP67, -40°C, 8-hour battery backup, cellular connectivity, edge compute) has no existing commercial solution. Requires novel modular hardware architecture. |


# 2. TrafficMesh Milestone Plan


| Phase | Milestone | Month | IRAP Evidence |
|---|---|---|---|
| Phase A | Officer review portal and ingestion API operational with simulated evidence. Basic ALPR integration documented with accuracy benchmarks on publicly available footage. | Month 3 | GitHub commit history, Linear tasks, ALPR accuracy report |
| Phase A | Digital twin prototype on Ottawa OpenStreetMap data. Simulated node observations populating twin in real time. | Month 5 | Working demo recording, architecture documentation |
| Phase B | First physical node on test vehicle. Evidence packages from real Ottawa routes captured, uploaded, appearing in officer portal. | Month 8 | Video evidence, GPS tracks, node credential chain, test report |
| Phase B | Contractor kit prototype deployed on 2 vehicle types (personal vehicle + borrowed utility vehicle). 5-minute install documented with timing and photo evidence. | Month 10 | Installation photos, timing records, design iteration log |
| Phase C | 3-node fleet operational with 99%+ evidence pipeline uptime over 30-day test period. OC Transpo pilot proposal submitted to OC Transpo board. | Month 14 | Uptime logs, evidence package samples, OC Transpo submission receipt |
| Phase C | Digital twin live on real Ottawa route data from test node fleet. Public Works defect detection demonstrated with 3 real pothole detections validated against 311 records. | Month 17 | Twin data export, pothole validation report, 311 correlation |


# 3. Public Safety Dual-Use Justification
TrafficMesh directly supports the following public safety mandates that align with the January 2026 IRAP dual-use expansion:
- Municipal traffic enforcement: bus lane compliance, school zone protection, construction zone worker safety — all directly reducing road fatalities and injuries;
- Law enforcement support: ALPR network integration with Ottawa Police ALPR database for real-time stolen vehicle detection across the city fleet node network;
- Amber Alert support: when an Amber Alert is active, every node vehicle receives the target plate in real time — the city fleet becomes a 200-vehicle search network;
- Hit-and-run investigation support: any node vehicle in the vicinity of a collision provides footage to investigators — a capability Ottawa Police currently cannot access;
- Emergency situational awareness: during major events and emergencies, city fleet nodes provide real-time corridor status to the Emergency Operations Centre.
# 4. SR&ED and IRAP Interaction


| Key Rule | SR&ED eligible expenditures must be reduced by the amount of government assistance (IRAP) received on the same expenditure. Since IRAP covers up to 80% of eligible salaries, the SR&ED claim on those same salaries is calculated on the remaining 20%. |
|---|---|
| Why Both Are Still Optimal | IRAP funds 80% of salary now (monthly cash flow). SR&ED refunds a portion of the remaining 20% at year end. Together they cover approximately 88% of eligible R&D salary costs. Using both is always the right strategy — IRAP for cash flow, SR&ED for the year-end top-up. |
| Accounting Requirement | The company accountant must track IRAP receipts by project and by expense category and reduce the SR&ED claim accordingly. This is standard procedure for any SR&ED consultant — flag at engagement. |
| IRAP on SR&ED Consultant Fees | SR&ED consultant fees are not IRAP-eligible but are themselves SR&ED-eligible expenditures (as contractor costs). Account for this correctly in the SR&ED claim. |


# 5. Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

