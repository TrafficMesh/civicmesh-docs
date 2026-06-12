# REG-TM-002 TrafficMesh Provincial Variation Matrix v1

**REG-TM-002**
**D-CENTRAL GROUP**
**D-Central Group / TrafficMesh Technologies Inc. / CivicMesh Inc.**
TrafficMesh Provincial Variation Matrix


| Document ID | REG-TM-002 |
|---|---|
| Version | 1.0 |
| Status | Draft — for legal counsel review |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Legal / Regulatory |
| Purpose | Evidence admissibility, citation authority, ALPR authorization, and AI evidence rules across all Canadian provinces and territories |
| Use Case | Required before TrafficMesh expansion beyond Ontario — each province requires separate legal analysis |
| Related Documents | REG-TM-001 | OS-PATROL-TM-EV-001 | DC-CM-GTM-001 |


# 1. Provincial Matrix


| Province / Territory | Automated Enforcement Framework | ALPR Legal Status | HTA Equivalent | Key Consideration for TrafficMesh |
|---|---|---|---|---|
| Ontario | ASE ban (Nov 2025) — fixed cameras. Mobile/human review not banned. Red light cameras active. | No specific ALPR statute — Charter s.8 analysis applies (R v Jarvis 2019 SCC 10). Plate reads on public roads: low expectation of privacy. | HTA R.S.O. 1990 c. H.8 | Legal opinion required before deployment. ASE ban scope most favourable for TrafficMesh of all provinces. |
| British Columbia | Photo radar banned 2001. Red light cameras active. No current mobile enforcement programme. | No specific ALPR statute. Police ALPR use governed by OIPC guidelines. | Motor Vehicle Act RSBC 1996 | Provincial privacy legislation (PIPA) adds to PIPEDA requirements. Municipal authority varies. |
| Alberta | No provincial automated enforcement programme. Municipal photo radar active in Calgary, Edmonton. | No specific ALPR statute. Freedom of Information and Protection of Privacy Act applies. | Traffic Safety Act RSA 2000 | Potential first-mover advantage — no competing fixed camera infrastructure. Municipal bylaw authority strongest. |
| Quebec | Photo radar and red light cameras active under provincial programme. Bilingual requirements. | No specific ALPR statute. Commission d'accès à l'information oversight. | Code de la sécurité routière RLRQ c C-24.2 | French language requirements for all interfaces, notices, and documentation. Privacy framework more restrictive than PIPEDA. |
| Manitoba | Limited automated enforcement. Photo radar in school zones. | No specific ALPR statute. | Highway Traffic Act CCSM c H60 | Small market. Consider post-Ontario, post-Quebec expansion. |
| Saskatchewan | SGI (provincial insurer) operates automated enforcement. | SGI operates ALPR for insurance purposes — unique landscape. | Traffic Safety Act SS 2004 | SGI relationship could be a partnership opportunity rather than a regulatory hurdle. |
| Nova Scotia | Photo radar used in construction zones. | No specific ALPR statute. | Motor Vehicle Act RSNS 1989 c 293 | Atlantic Canada markets — smaller but potentially receptive municipalities. |
| Federal (RCMP / NPS) | Federal enforcement on federal properties (national parks, federal buildings, parliament area). | RCMP operates ALPR under their own authority. | Federal Motor Vehicle Safety Act + provincial HTA by reference | ISC Phase 2 federal opportunity — RCMP and NPS as deployment partners. |


# 2. Consistent Requirements Across All Jurisdictions


| Requirement | Standard | Notes |
|---|---|---|
| Human review before citation | Officer must review and approve every citation | Non-negotiable architectural requirement — satisfies all provincial evidence standards |
| PIPEDA compliance | Federal baseline — provincial legislation may add requirements | Quebec (Law 25), BC (PIPA), and Alberta (PIPA) add provincial requirements on top of PIPEDA |
| Evidence package integrity | Cryptographic chain of custody — admissible as business record in all provinces | Canada Evidence Act s.31.1 authentication provisions apply nationally |
| Violation type authorization | Each violation type must be confirmed as within municipal or provincial enforcement authority in each jurisdiction | Always obtain local legal opinion before activating a new violation type in a new province |


# Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

