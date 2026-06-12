# REG-CM-002 CivicMesh Ontario Privacy Act Analysis v1

**REG-CM-002**
**D-CENTRAL GROUP**
**D-Central Group / TrafficMesh Technologies Inc. / CivicMesh Inc.**
CivicMesh Ontario Privacy Act Analysis


| Document ID | REG-CM-002 |
|---|---|
| Version | 1.0 |
| Status | Draft — for legal counsel review |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Legal / Regulatory |
| Scope | MFIPPA obligations for city-federated data, HOA/condo data governance, biometric data prohibition |
| Related Documents | REG-CM-001 | DC-CM-FED-001 | LEGAL-CM-011 | LEGAL-CM-012 |


# 1. MFIPPA — Municipal Institutions


| What is MFIPPA | The Municipal Freedom of Information and Protection of Privacy Act (MFIPPA) governs the collection, use, and disclosure of personal information by Ontario municipal institutions (cities, transit authorities, school boards, etc.). |
|---|---|
| When it applies to CivicMesh | When City of Ottawa, OC Transpo, or other Ontario municipal institutions receive TrafficMesh evidence data under a Municipal Data Federation Agreement (LEGAL-CM-011), they become subject to MFIPPA for that data. CivicMesh Inc. is not directly subject to MFIPPA — the municipality is. |
| Key municipal obligations | (1) Privacy Impact Assessment required before implementing a new information system (IPC/Ontario guidance); (2) Personal information collected only for lawful authority; (3) Individual access rights to their own information; (4) Retention and disposal schedules; (5) Annual reporting to council on surveillance systems. |
| IPC/Ontario guidance on surveillance | The Information and Privacy Commissioner of Ontario has published guidance on municipal surveillance systems. TrafficMesh should be reviewed against this guidance by the municipal partner's privacy team before deployment. D-Central provides a MFIPPA-ready data governance package as part of the municipal deployment. |


# 2. Biometric Data — Critical Prohibition


| Definition | Biometric data in Ontario includes: facial recognition outputs, fingerprints, iris patterns, and any unique physical characteristic used to identify a person. Face blurring outputs are NOT biometric data — they are processed video where biometric data has been removed. |
|---|---|
| CivicMesh position | CivicMesh performs face detection only for the purpose of blurring. No facial recognition is performed. No biometric data is generated, stored, or transmitted. The platform is explicitly designed to destroy, not create, biometric data. |
| What is prohibited | Running facial recognition to identify individuals from footage. Storing facial feature vectors or encodings. Matching faces across cameras. None of these occur in CivicMesh. |
| Contractual prohibition | The MSSP Partner Agreement (LEGAL-CM-010), HOA Participation Agreement (LEGAL-CM-012), and Municipal Data Federation Agreement (LEGAL-CM-011) all explicitly prohibit adding facial recognition capabilities to the platform. This prohibition survives agreement termination. |
| Why this matters | Flock Safety and Amazon Ring have faced regulatory scrutiny and civil litigation over facial recognition capabilities (even if disabled by default). CivicMesh's architectural design makes facial recognition technically impossible — not just prohibited. |


# 3. Condo Corporation Data Governance


| Legal status | Condominium corporations in Ontario are governed by the Condominium Act, 1998. They are not municipal institutions — MFIPPA does not apply. PIPEDA applies as a commercial activity. |
|---|---|
| Authority to deploy cameras | Condo corporations have authority to install security cameras in common areas under the Condominium Act. The declaration and rules may specify procedures for security decisions — community manager must confirm board authority before installation. |
| Common element surveillance | Cameras in common elements (lobby, parking, amenities) are clearly within condo authority. Cameras that could observe individual unit entrances or private outdoor spaces require more careful positioning and privacy analysis. |
| Tenant/owner notification | The Condominium Act requires notice to owners and tenants of significant changes to common elements. Camera installation is typically a significant change — notice and (depending on declaration) owner vote may be required. |


# Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

