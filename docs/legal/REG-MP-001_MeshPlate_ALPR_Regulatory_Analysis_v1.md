# REG-MP-001 MeshPlate ALPR Regulatory Analysis v1

**REG-MP-001**
**D-CENTRAL GROUP**
**D-Central Group / TrafficMesh Technologies Inc. / CivicMesh Inc.**
MeshPlate ALPR Regulatory Analysis


| Document ID | REG-MP-001 |
|---|---|
| Version | 1.0 |
| Status | Draft — for legal counsel review |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Legal / Regulatory |
| Scope | Canadian provincial ALPR legislation survey, Charter s.8 analysis, hash-before-store design implications |
| Critical Finding | Ontario has no specific ALPR statute — the Charter s.8 analysis governs. Hash-before-store architecture addresses the key Charter concern. |
| Related Documents | DC-MP-ARCH-001 | DC-MP-FED-001 | REG-TM-001 | REG-CM-001 |


# 1. Canadian ALPR Legal Landscape


| Province | ALPR-Specific Legislation | Charter s.8 Analysis | Police ALPR Guidance | MeshPlate Implication |
|---|---|---|---|---|
| Ontario | None — legislative gap | R v Jarvis 2019 SCC 10: reasonable expectation of privacy in movements over time. Bulk plate collection and retention may engage s.8. Hash-before-store addresses the retention concern. | OPP and OPS ALPR guidelines exist but are not public. IPC/Ontario has issued guidance on police ALPR. | Strongest jurisdiction for MeshPlate. Hash-before-store + formal query requirement satisfies the Jarvis s.8 concern. |
| British Columbia | PIPA applies to commercial ALPR. OIPC has investigated commercial ALPR use. | BC courts have applied Jarvis to commercial ALPR — bulk retention of plate reads engages privacy interests. | BC Privacy Commissioner issued order against parking enforcement ALPR in 2012. | More restrictive than Ontario. Legal opinion required before deployment. |
| Quebec | Quebec Law 25 — strongest provincial privacy legislation in Canada. Biometric data definition may extend to plate reads. | Quebec courts have been more privacy-protective than Ontario. | CAI (Commission d'accès à l'information) oversight of ALPR. | Most restrictive. Detailed Quebec legal opinion mandatory before deployment. |
| Alberta | PIPA applies. OIPC has not issued specific ALPR guidance. | Similar to BC approach. | RCMP and municipal police ALPR governed by internal policy. | Similar to BC — legal opinion required. |
| Federal (RCMP) | Privacy Act governs federal agency collection. No ALPR-specific statute. | Charter s.8 applies to state action — RCMP ALPR governed by existing s.8 jurisprudence. | RCMP ALPR use governed by internal policy and Commissioner direction. | Federal ISC opportunity — MeshPlate as non-state actor providing data to RCMP under formal request process has different legal analysis. |


# 2. The Jarvis Analysis and Hash-Before-Store


| R v Jarvis [2019] SCC 10 | The Supreme Court held that continuous observation of an individual over time engages a reasonable expectation of privacy under Charter s.8, even in public spaces. Applied to ALPR: a system that records every location a vehicle visits over time creates a 'biographical profile' — a chronicle of a person's movements — that engages s.8. |
|---|---|
| The key legal concern | It is not the reading of a single plate at a single location that is legally sensitive. It is the aggregation of plate reads over time into a location history that creates the privacy interest. |
| How hash-before-store addresses this | By storing SHA-256 hashes rather than plaintext plates, and by requiring formal legal authority to link a specific plate to a hash and retrieve location history, MeshPlate prevents the casual aggregation that Jarvis is concerned about. An operator cannot browse MeshPlate data and build a location history for any particular vehicle. They can only query whether a specific plate (submitted with legal authority) appears in the records. |
| Remaining legal question | Whether the hash-before-store architecture sufficiently addresses the Jarvis concern requires a formal legal opinion. The argument is strong, but it has not been tested in court. This is an active area of inquiry that the Mitacs legal faculty partnership should address. |


# 3. No-Data-Broker Covenant — Legal Foundation


| Why it matters legally | Data brokers acquiring ALPR data and selling it to insurers, employers, and private investigators has been a source of regulatory action in the US (California CPRA enforcement) and is attracting similar attention in Canada. |
|---|---|
| CivicMesh's protective design | The no-data-broker covenant (DC-MP-FED-001) is backed by AGPL licence terms that prohibit commercial data broker use. Any deployment of MeshPlate that routes data to a commercial data broker is in breach of the AGPL licence and the MSSP Partner Agreement simultaneously. |
| Regulatory positioning | Proactively briefing the IPC/Ontario and the OPC on MeshPlate's architecture — specifically the hash-before-store design and no-data-broker covenant — before deployment creates a cooperative regulatory relationship rather than a reactive enforcement relationship. |


# Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

