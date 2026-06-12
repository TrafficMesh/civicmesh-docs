# LEGAL-CM-016 Open Source Licence Strategy v1

**LEGAL-CM-016**
**D-CENTRAL GROUP**
**CivicMesh Inc.**
Open Source Licence Strategy — AGPL v3 Dual-Licence Architecture


| Document ID | LEGAL-CM-016 |
|---|---|
| Version | 1.0 |
| Status | Draft — for legal counsel review |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Internal — portions to be published |
| Licence Type | Dual-licence: AGPL v3 Community Edition + Proprietary Commercial Edition |
| Hardware Licence | CERN Open Hardware Licence Strong Reciprocal (CERN-OHL-S v2) |
| Related Documents | LEGAL-CM-001 | DC-CM-GOV-001 | LEGAL-003 | DC-CM-SUPPLY-001 |




| Executive Summary
CivicMesh uses a dual-licence model. The Community Edition is published under GNU AGPL v3 — the copyleft provision means any organization that deploys the platform as a service must open source their modifications. The Commercial Edition adds proprietary layers (MSSP orchestration, insurance API, data marketplace) available only to commercial licence holders. This model: drives adoption (anyone can evaluate and deploy Community Edition free), generates revenue (production enforcement deployments require Commercial Edition), protects against proprietary forks (AGPL prevents competitors from taking the code without contributing back), and enables academic partnerships (universities use Community Edition under AGPL). |
|---|


# 1. Dual-Licence Component Map


| Component | Community Edition (AGPL v3) | Commercial Edition |
|---|---|---|
| Node firmware and edge AI models | ✅ Open source — GitHub | ✅ Same codebase — commercial licence required for enforcement use |
| Ingestion API and evidence pipeline | ✅ Open source | ✅ Included |
| Officer review portal (React + FastAPI) | ✅ Open source | ✅ Included |
| Digital twin mapping engine | ✅ Open source | ✅ Included |
| Federation protocol implementation | ✅ Open source | ✅ Included |
| DID/VC credential schemas | ✅ Open source (W3C standard) | ✅ Included |
| MSSP orchestration layer | ❌ Not in Community Edition | ✅ Commercial only — multi-tenant management, partner billing, SLA tooling |
| Insurance API integration | ❌ Not in Community Edition | ✅ Commercial only — consent-gated individual records, aggregate feed |
| Data marketplace API | ❌ Not in Community Edition | ✅ Commercial only — insurer and municipal subscriber management |


# 2. AGPL v3 Key Obligations for Deployers


| Network Use Provision (the AGPL difference) | Unlike GPL, AGPL requires that any organization that deploys the software over a network (SaaS or API) must provide source code to users. This is the anti-competitive fork protection — a competitor who builds a commercial enforcement platform on CivicMesh code must open source all their modifications. |
|---|---|
| Attribution Requirement | All deployments must retain copyright notices and attribution to CivicMesh Inc. in source code and user-facing documentation. |
| Share-Alike | Any derivative work distributed or deployed over a network must be licenced under AGPL v3. Cannot be relicenced to a proprietary licence. |
| Patent Grant | Contributors grant a patent licence for patents necessarily infringed by their contributions — protects community from patent aggression. |


# 3. Commercial Licence Summary


| Who Needs One | Any MSSP deploying CivicMesh for municipal enforcement, HOA fine enforcement, or any revenue-generating deployment. Academic/research use under AGPL — no commercial licence required. |
|---|---|
| What It Grants | Rights to: deploy Commercial Edition features; keep modifications proprietary (no AGPL share-alike on Commercial Edition additions); use CivicMesh marks in partner marketing per brand guidelines |
| What It Does Not Grant | Cannot: sublicence to third parties; deploy outside Authorized Territory; use for prohibited purposes (immigration enforcement, political monitoring); remove attribution from Community Edition components |
| Licence Fee | Incorporated into MSSP Partner Agreement (LEGAL-CM-010) — no separate payment. Commercial licence is granted as part of MSSP certification. |


# 4. CERN-OHL-S v2 — Open Hardware Licence


| Applies To | All CivicMesh node hardware designs: PCB schematics, enclosure STEP files, component BOM, hardware-specific firmware |
|---|---|
| Key Obligation | Any modified version of the hardware design that is distributed or manufactured at scale must be published under CERN-OHL-S v2. Prevents a large manufacturer from forking the design with proprietary improvements. |
| Commercial Manufacture | Permitted under CERN-OHL-S v2 — the licence does not restrict commercial use, only the obligation to share modifications. |
| DCMC Position | DCMC manufactures the reference design. Other certified manufacturers may produce compatible nodes using the published design. |


# 5. Contributor Licence Agreement (CLA)


| Purpose | Any external contributor must sign a CLA: (a) confirms right to contribute the code; (b) grants CivicMesh Inc. right to include contribution in both Community and Commercial editions; (c) contributor retains copyright. |
|---|---|
| Dual-Licence CLA | Contributors agree their contributions may be included in both AGPL v3 Community and Commercial editions. Without this, contributions could not be included in the Commercial Edition. |
| CLA Platform | CLA Assistant or equivalent deployed on GitHub. Automated — contributors sign electronically when submitting their first pull request. |


# 6. Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

