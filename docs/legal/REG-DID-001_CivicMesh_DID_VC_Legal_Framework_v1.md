# REG-DID-001 CivicMesh DID VC Legal Framework v1

**REG-DID-001**
**D-CENTRAL GROUP**
**D-Central Group / TrafficMesh Technologies Inc. / CivicMesh Inc.**
CivicMesh DID/VC Legal Framework


| Document ID | REG-DID-001 |
|---|---|
| Version | 1.0 |
| Status | Draft — for legal counsel review |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Legal / Regulatory |
| Scope | Legal standing of W3C Verifiable Credentials in Canadian courts, Evidence Act implications, blockchain record admissibility, Electronic Commerce Act alignment |
| SR&ED Note | The legal research itself — determining whether a novel technical architecture satisfies existing legal standards — is potentially SR&ED eligible as it involves technical uncertainty with legal dimensions. |
| Related Documents | DC-CM-DID-001 | DC-CM-VC-001 | DC-CM-CHAIN-001 | OS-PATROL-TM-EV-001 |


# 1. Canada Evidence Act — Electronic Records


| Relevant provisions | Canada Evidence Act s.31.1–31.8 governs the admissibility of electronic documents. Key provision: s.31.1 — a party seeking to admit an electronic document has the burden of proving authenticity by evidence capable of supporting a finding that the electronic document is what it purports to be. |
|---|---|
| How blockchain records satisfy s.31.1 | A Polygon blockchain record has the following authenticity-supporting characteristics: (1) immutable — once written, cannot be altered; (2) timestamped — block timestamp provides independent time verification; (3) cryptographically anchored — the hash of any record can be independently verified; (4) publicly auditable — any party can verify the record independently without relying on CivicMesh's testimony. |
| Business records exception | Canada Evidence Act s.30 provides that a record made in the usual and ordinary course of business is admissible without the need to call the maker. CivicMesh's evidence pipeline — where node capture, upload, and processing are automated in the ordinary course of enforcement operations — should satisfy the business records exception. |
| Expert evidence on AI | Where a citation relies on AI model output (violation type classification), the methodology may require expert evidence at trial if contested. CivicMesh maintains model documentation (training data, architecture, accuracy benchmarks) suitable for expert witness support. See OS-PATROL-TM-EV-001 Section 4. |


# 2. Electronic Commerce Act Alignment


| Federal ECIPA | Electronic Commerce Protection Act (Canada) — governs electronic documents and signatures. A JWS (JSON Web Signature) on a CivicMesh evidence package qualifies as an electronic signature under ECIPA — it identifies the signatory (node DID), indicates the signatory's approval, and is attached to the document. |
|---|---|
| Ontario Electronic Commerce Act | Ontario ECA 2000 — electronic records are as legally binding as paper records when they satisfy the authentication requirements. CivicMesh evidence packages satisfy these requirements through the cryptographic signature chain. |
| DID/VC as electronic identification | W3C DIDs and Verifiable Credentials are not yet explicitly addressed in Canadian legislation. The broader principle of electronic signatures and records accommodates them — but legislative recognition would strengthen their standing. The Mitacs legal faculty partnership should address this gap. |


# 3. Blockchain Evidence in Canadian Courts — Current Status


| Case/Context | Jurisdiction | Relevance to CivicMesh |
|---|---|---|
| Blockchain records as evidence — emerging jurisprudence | Various — no definitive Supreme Court ruling yet | Canadian courts have accepted blockchain records as evidence in commercial disputes. No enforcement-specific cases yet. CivicMesh may be among the first. |
| R v Oakes standard — burden of proof | Supreme Court of Canada | For enforcement citations, the Crown must prove the violation beyond a reasonable doubt. CivicMesh's credential chain supports authentication — it does not lower the evidentiary standard. |
| Expert witness precedent — AI evidence | Ontario courts | Courts have accepted expert evidence on AI decision systems in various contexts. The key is that the AI methodology can be explained and the expert can speak to accuracy rates. CivicMesh model documentation is designed for this purpose. |


# 4. Mitacs Legal Research Opportunity


| Research question | Are W3C Verifiable Credentials anchored on a public blockchain (Polygon PoS) legally admissible as foundation evidence in Ontario provincial offence proceedings under the Canada Evidence Act, and what credential schema design satisfies the business records exception? |
|---|---|
| Target institution | University of Ottawa Faculty of Law — strong in technology law and evidence law. |
| Publication potential | Canadian Bar Review or University of Ottawa Law Journal — novel enough for tier-1 Canadian legal journal publication. |
| SR&ED eligibility | Yes — the technical design work driven by the legal uncertainty (what schema design satisfies the legal standard?) is SR&ED eligible as it involves technical uncertainty with a legal dimension. Document every design iteration motivated by the legal research findings. |


# Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

