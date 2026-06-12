# OS-PATROL-TM-FLEET-001 TrafficMesh Fleet Node Specification v1

**OS-PATROL-TM-FLEET-001**
**D-CENTRAL GROUP**
**TrafficMesh Technologies Inc.**
TrafficMesh Fleet Node Specification


| Document ID | OS-PATROL-TM-FLEET-001 |
|---|---|
| Version | 1.0 |
| Status | Draft — SR&ED: hardware R&D |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Technical |
| SR&ED Eligibility | Strong — IP67 -40°C hardware design, contractor kit universal vehicle compatibility, seasonal deployment protocol |
| Related Documents | OS-PATROL-TM-001 | DC-CM-HW-001 | DC-CM-NODE-PROV-001 | DEPLOY-TM-002 |




| Executive Summary
This document specifies the hardware configuration for all three TrafficMesh node tiers, the seasonal deployment calendar for Ottawa, and the contractor kit checkout programme. The hardware challenge is significant: enforcement nodes must operate reliably across the full range of Ottawa's municipal fleet — from heated OC Transpo buses (easy) to unheated snowplows operating at -30°C (very hard) to contractor vehicles with no guaranteed power source (hardest). |
|---|


# 1. Three-Tier Fleet Architecture


| Tier | Vehicle Types | Install Type | Key Hardware Challenge | Seasonal? |
|---|---|---|---|---|
| Tier 1 — Permanent City Fleet | OC Transpo buses, bylaw vehicles, Ottawa Police cruisers, Public Works inspection trucks | Hardwired 12V DC, custom vehicle-type bracket, permanently mounted | Vibration tolerance on bus (>50,000 vibration cycles/year), police vehicle RF interference mitigation | Year-round |
| Tier 2 — Seasonal City Fleet | Snow plows, salt spreaders, street sweepers, line painters, parks vehicles | Semi-permanent bracket (bolted, removable seasonally), hardwired 12V | IP67 at -30°C on plow (operating in salt spray + road spray + below-spec temperatures), heated enclosure requirement for battery | Plow/salt: Nov–Mar. Sweeper/line: Apr–Oct. |
| Tier 3 — Contractor Kit | Road construction crews, landscaping contractors, special event crews, any city-contracted vehicle | Magnetic/suction self-adhesive roof mount, OBD-II or 12V cigarette lighter power, no tools required | Universal vehicle compatibility (<5 min install on any vehicle type), vibration retention on non-metal roofs, battery performance without hardwired power | Per-contract — weeks to months |


# 2. Tier 2 Seasonal Deployment Calendar — Ottawa


| Month | Active Fleet | Active Violation Types | Notes |
|---|---|---|---|
| November | Plow fleet deployment begins (~Nov 14) | Snow route parking, expired plates, accessible parking | Kit installation at fleet garage before first snow event |
| December–February | Full plow fleet + salt spreaders | Snow route parking (highest volume), lane violations, construction zones | Coldest months — -30°C hardware performance critical |
| March | Plow fleet winds down — sweeper fleet begins | Pothole detection, line marking condition, post-winter road assessment | Transition period — some overlap |
| April–May | Full sweeper fleet active | Lane violations, accessible parking, illegal dumping detection | Spring road condition survey — digital twin update high-value period |
| June–September | Sweepers + line painters + parks vehicles | School zone (June/Sept), construction zones, lane violations | Summer construction season — contractor kits active |
| October | Sweeper wind-down, plow preparation | Reduced — transition period | Seasonal kit maintenance and recertification before November deployment |


# 3. Contractor Kit Checkout Programme


| Checkout Process | City procurement department issues Contractor Node Kit as part of standard contractor equipment package (alongside safety vests and traffic cones). Contractor signs LEGAL-CM-015 as condition of kit receipt. |
|---|---|
| Kit Inventory | City maintains a pool of Tier 3 kits. Recommended: 1 kit per 3 active contractors (utilization-based pooling). Ottawa with 50 active city contractors → 17–20 kit pool sufficient. |
| Kit Tracking | Each kit has a unique serial number. Checkout/return logged in city fleet management system. Kit DID registration on Polygon activated on checkout, deactivated on return. |
| Kit Maintenance | Monthly inspection of returned kits: tamper seal integrity, camera lens condition, firmware version, cellular SIM status. Annual full recertification (new Installation Credential issued). |
| Construction Zone Activation | When a Tier 3 kit checks in to the platform, the system automatically activates a construction zone enforcement geofence centred on the kit's GPS position. Drivers approaching the geofence receive a MeshNav alert (active construction zone enforcement). Speed violations within the geofence are flagged at HTA construction zone threshold. |


# 4. Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

