# REG-CM-003 CivicMesh Open Source Licence Compliance v1

**REG-CM-003**
**D-CENTRAL GROUP**
**D-Central Group / TrafficMesh Technologies Inc. / CivicMesh Inc.**
CivicMesh Open Source Licence Compliance


| Document ID | REG-CM-003 |
|---|---|
| Version | 1.0 |
| Status | Draft — for legal counsel review |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Legal / Regulatory |
| Scope | AGPL v3 obligations for deployers, dual-licence commercial exceptions, CLA requirements, attribution |
| Related Documents | LEGAL-CM-016 | DC-CM-GOV-001 | LEGAL-003 |


# 1. AGPL v3 Compliance for CivicMesh Deployers


| Obligation | What It Means for CivicMesh Deployments | How CivicMesh Enforces |
|---|---|---|
| Network use provision | Any organization that deploys CivicMesh as a service (SaaS or API) must make the source code — including any modifications — available to users. This is the core anti-competitive fork protection. | MSSP Partner Agreement requires AGPL compliance. Annual MSSP audit includes open source compliance review. Any unreported modification is a material breach. |
| Attribution retention | All deployments must retain copyright notices and attribution to CivicMesh Inc. in source code and documentation. | Automated attribution check in firmware build system. Documentation templates include mandatory attribution. |
| Share-alike | Modifications distributed or deployed over a network must be licensed under AGPL v3. Cannot be relicensed to a proprietary licence. | Verified at MSSP annual audit. CLA for external contributors grants D-Central rights to include contributions in Commercial Edition. |
| CLA requirement | External contributors must sign a CLA before their code is merged. CLA grants CivicMesh Inc. rights to include contribution in both Community and Commercial editions. | CLA Assistant enforced on GitHub — contribution blocked without signed CLA. |


# 2. Commercial Edition Boundary


| What Commercial Edition adds | MSSP orchestration layer, insurance API integration, data marketplace API, NOC dashboard premium features. These are proprietary additions to the AGPL core. |
|---|---|
| Commercial licence grant | MSSP operators receive a Commercial Edition licence as part of the MSSP Partner Agreement. No separate commercial licence fee — included in MSSP fee structure. |
| What Commercial Edition does not allow | Re-closing the AGPL components. Removing attribution from Community Edition code. Sublicensing to any third party. Deploying outside Authorized Territory. |
| The dual-licence paradox | An MSSP could theoretically self-host the AGPL Community Edition without a Commercial licence — they just wouldn't have the MSSP orchestration layer, insurance API, or data marketplace features. This is acceptable — the Community Edition is genuinely open. |


# 3. CERN-OHL-S v2 Hardware Compliance


| Obligation | Application | Consequence of Non-Compliance |
|---|---|---|
| Share-alike for hardware modifications | Any manufacturer that modifies the open hardware design must publish modifications under CERN-OHL-S v2 | Modified hardware loses CivicMesh certification — Node Provenance Credential will not be issued. Cannot be deployed on the network. |
| Attribution on manufactured hardware | CivicMesh design attribution must appear on PCB silkscreen and in product documentation | Certification requirement — verified at manufacturing audit |
| Source file availability | All design source files (KiCad, STEP, BOM) must be provided to customers on request | Certification requirement |


# Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

