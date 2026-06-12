# IRAP-CM-001 NRC IRAP Application CivicMesh Platform v1

**IRAP-CM-001**
**D-CENTRAL GROUP**
**CivicMesh Inc. / D-Central Group Inc.**
NRC IRAP Application — CivicMesh Platform


| Document ID | IRAP-CM-001 |
|---|---|
| Version | 1.0 |
| Status | Draft — complete and submit before any R&D spending begins |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Funding Application |
| Programme | NRC Industrial Research Assistance Programme (IRAP) |
| IRAP Phone | 1-877-994-4727 — request meeting with Industrial Technology Advisor (ITA) before submitting |
| Funding Stream | Dual-use / Public Safety Technology — $241M announced January 9, 2026 |
| Related Documents | SRED-CM-001 | IRAP-TM-001 | LOAN-CM-001 | LEGAL-001 | FIN-CM-001 |




| Executive Summary
NRC IRAP provides non-repayable financial assistance — up to 80% of eligible internal technical labour costs and up to 50% of eligible contractor costs for approved projects. Funding is disbursed monthly based on milestone reporting, making it the primary cash flow mechanism during development. IRAP does NOT fund work already completed — the ITA relationship must be established and the project approved before any eligible expenditure occurs. This document is the application narrative and project specification. The ITA will guide the formal submission process. |
|---|




| CRITICAL: Call 1-877-994-4727 and request a meeting with an Industrial Technology Advisor BEFORE spending any money on R&D. IRAP cannot retroactively fund work already done. The ITA relationship is the most important government relationship in Year 1 — treat it like a co-founder relationship. |
|---|


# 1. Company Profile


| Legal Name | CivicMesh Inc. (or D-Central Group Inc. if CivicMesh Inc. not yet incorporated — update at submission) |
|---|---|
| Business Number | [To be obtained at incorporation — CRA BN] |
| Incorporation Date | [Date of incorporation — CBCA federal] |
| Company Type | Canadian Controlled Private Corporation (CCPC) |
| Primary Location | Ottawa, Ontario, Canada |
| Industry Sector | Digital Technologies / Artificial Intelligence / Public Safety |
| Number of Employees | 1 (founder) at application — projected 3–5 by end of approved project |
| Annual Revenue | $0 at application — pre-revenue startup |
| NAICS Code | 541519 — Other Computer Related Services / 541715 — Research and Development in Physical, Engineering and Life Sciences |


# 2. Project Overview


| Project Title | CivicMesh: Privacy-Preserving Federated Community Safety Enforcement Platform |
|---|---|
| Project Duration | 18 months (Months 1–18 from ITA approval date) |
| Total Eligible Project Costs | $[TBD — complete with ITA] — primarily internal technical labour |
| IRAP Funding Requested | Up to 80% of eligible internal technical labour costs + up to 50% of eligible contractor costs |
| Dual-Use / Public Safety Alignment | CivicMesh directly addresses public safety enforcement, law enforcement support (Amber Alert integration, stolen vehicle detection, hit-and-run evidence), and municipal infrastructure — qualifying for the January 2026 dual-use/public safety funding stream expansion |


# 3. Technical Uncertainty Narrative
IRAP requires that the project involve genuine technological uncertainty — problems where the solution is not known in advance and where the company's technical team must conduct systematic investigation to resolve them. The following five areas of technical uncertainty exist in the CivicMesh platform development:


| Technical Uncertainty Area | Why It Is Uncertain | Approach to Resolution |
|---|---|---|
| Edge AI Violation Detection in Canadian Conditions | It is not known in advance whether YOLOv8 and similar models can achieve acceptable precision/recall for violation detection (bus stop obstruction, lane violations, speed estimation) on Canadian roads under variable weather — snow, ice, salt spray, -30°C operation, reduced daylight hours, and vehicle-mounted vibration. No published benchmark exists for this specific deployment context. | Systematic experimental investigation: collect real dashcam footage on Ottawa routes across seasons, annotate datasets, train and evaluate multiple model architectures, document every failed approach and parameter set, iterate toward production accuracy thresholds. Each training run and evaluation is SR&ED-documentable R&D. |
| Privacy-Preserving Federated Data Architecture | It is not known whether a multi-community, multi-jurisdiction data federation system can be implemented that: (a) provides enforcement-quality evidence to municipalities, (b) maintains PIPEDA compliance for all participants, (c) processes federation requests in real time with auditable chain of custody, and (d) allows communities to independently verify every data access event — simultaneously. No existing system achieves all four simultaneously. | Architecture research: evaluate existing federated learning frameworks, zero-knowledge proof approaches, and consent management systems. Design novel federation protocol (DC-CM-FED-001). Build and test against real multi-community scenarios. Document every design choice, rejected alternative, and technical failure. |
| On-Device ALPR Optimisation for Canadian Plates and Weather | It is not known whether existing open-source ALPR systems (OpenALPR, Plate Recognizer) can achieve production-quality plate recognition on a vehicle-mounted system operating in Canadian winter conditions — partial plate occlusion by snow, frost on camera lens, motion blur at speeds above 40km/h in precipitation, and optical character recognition of provincial plates with varied fonts across provinces. | Systematic testing: collect plate recognition footage across weather conditions, measure accuracy degradation by condition, investigate optical preprocessing approaches (defogging, deblurring, contrast enhancement), evaluate and compare model fine-tuning approaches on Canadian plate datasets. Document all experiments with contemporaneous evidence. |
| W3C DID/VC Credential Chain for Enforcement Evidence Admissibility | It is not known whether a W3C Decentralized Identifier and Verifiable Credential implementation on Polygon can produce a cryptographic provenance chain that satisfies Canadian court standards for evidence admissibility — specifically whether a blockchain-anchored credential chain constitutes sufficient foundation evidence for the court to admit the evidence package as a business record under the Canada Evidence Act. | Legal research (Mitacs law faculty partnership) + technical implementation: design credential schema, implement on Polygon testnet, engage legal counsel to assess admissibility, iterate credential design based on legal requirements, test against mock court scenarios. Document all technical and legal uncertainty encountered. |
| OBD-II Telemetry Fusion with Camera Evidence for Speed Estimation | It is not known whether OBD-II vehicle speed data can be reliably fused with camera-based optical flow speed estimation to produce a legally defensible speed measurement for a moving enforcement vehicle — accounting for GPS lag, OBD-II sampling rate limitations, camera frame rate constraints, and the mathematical challenge of estimating the speed of another vehicle relative to a moving reference frame. | Systematic investigation: instrument test vehicle with OBD-II + GPS + camera, collect comparative speed data against known speed reference, investigate sensor fusion algorithms, evaluate accuracy and variance, document all failed approaches and design iterations. |


# 4. Project Milestones


| Milestone | Description | Deliverable | Month |
|---|---|---|---|
| M1 — Platform Architecture | Complete CivicMesh platform architecture documentation, DID/VC schema design, and federation protocol specification | DC-CM-ARCH-001, DC-CM-FED-001, DC-CM-VC-001 completed and reviewed | Month 3 |
| M2 — Software MVP | Officer review portal (React/FastAPI), ingestion API, simulated evidence pipeline, basic ALPR integration, and digital twin prototype operational | Working software demo — evidence package from simulation appears in officer portal | Month 6 |
| M3 — First Physical Node | Single CivicMesh node on test vehicle capturing real Ottawa footage, running edge inference, uploading validated evidence packages to officer portal | Real evidence package from Ottawa road — officer portal review and sign-off demonstrated | Month 9 |
| M4 — ALPR Optimisation | On-device ALPR achieving target accuracy threshold across tested Canadian weather conditions. Dataset collected, model fine-tuned, accuracy benchmarked | ALPR performance report with weather condition breakdown. Model published to GitHub. | Month 12 |
| M5 — Credential Chain | Full DID/VC credential chain implemented on Polygon. Node provenance credential, technician installation credential, and evidence package signing all operational | On-chain credential chain for 3 test nodes. Legal admissibility analysis completed. | Month 15 |
| M6 — Pilot-Ready System | 3–5 nodes operational, evidence pipeline reliable, officer review portal court-ready, digital twin live on Ottawa map data, OC Transpo pilot proposal formally submitted | OC Transpo pilot proposal submitted with working system demo. ISC Phase 1 application submitted. | Month 18 |


# 5. Eligible Expenditures


| Expenditure Category | IRAP Eligible? | Estimated Annual Amount | Notes |
|---|---|---|---|
| Founder salary — technical work (edge AI, platform development, architecture) | Yes — up to 80% of eligible portion | $70,000–$80,000 | Salary must be market-rate and documented through payroll. Time tracking required. |
| Additional technical staff (when hired) | Yes — up to 80% | $65,000–$75,000 per person | Each staff member must be approved in project budget amendment |
| External contractors — technical (AI/ML, backend) | Yes — up to 50% of eligible costs | Variable | Contractor must provide invoices. Work must be part of approved project scope. |
| Hardware for R&D (Jetson Orin, cameras, GPS, OBD-II modules, test nodes) | Yes — as project materials | $5,000–$15,000 | Must be used directly in approved R&D project |
| Cloud computing costs for model training | Yes — as project materials | $2,000–$5,000/year | AWS/GCP GPU instances for model training runs |
| Legal and patent costs | No — not eligible | — | Fund separately |
| Business development and sales | No — not eligible | — | Fund separately |
| UI/UX design and front-end (non-novel) | Partial — only SR&ED-eligible portions | Variable | Document technical vs non-technical work separately |


# 6. Market Opportunity Statement
The CivicMesh platform addresses an urgent and documented market gap in Canadian municipal enforcement infrastructure created by Ontario's November 2025 automated speed enforcement camera ban. The City of Ottawa alone lost $27.8M in speed camera revenue and $10.9M in red light camera revenue annually ($38.7M total). The platform's beachhead market — OC Transpo bus lane enforcement — operates under existing HTA authority with no new legislation required, making it deployable within a 12–18 month commercial timeline following platform readiness.
The broader market includes: municipal enforcement infrastructure (Flock Safety comparable — $7.5B valuation), community safety technology (Ring comparable — Amazon acquisition at $1.2B), navigation data (Waze comparable — Google acquisition at $1.3B), and insurance telematics (global market $11.4B by 2030). CivicMesh's privacy-preserving, open-source, community-owned architecture addresses the documented political and regulatory vulnerabilities of all major incumbents.
# 7. Documentation System
The following contemporaneous documentation system is in place from Day 1 to support IRAP milestone reporting and SR&ED claims simultaneously:


| System | Purpose | Setup Status |
|---|---|---|
| GitHub (D-Central organization) | Every commit message describes the technical problem being solved, the approach taken, and any failures encountered. Branch names reference Linear/Jira ticket numbers. Commit history is the primary contemporaneous evidence record. | Set up Day 1 — before any R&D begins |
| Linear (project management) | Every task tagged with: SR&ED eligible (yes/no), IRAP milestone reference, technical uncertainty area. At year end the SR&ED consultant runs a report and the claim writes itself. | Set up Day 1 |
| Technical Decision Log | Running document recording: every architectural decision, alternatives considered, why the chosen approach was selected, what was tried and failed, and what uncertainty remains. This is the narrative CRA looks for. | Active from Day 1 |
| Time Tracking | Every team member logs hours daily by: project, task, SR&ED eligible vs non-eligible activity, IRAP milestone. Non-dilutive funding lives or dies on time tracking discipline. | Active from Day 1 — Harvest or Toggl recommended |
| IRAP Milestone Reports | Monthly report to ITA covering: work completed, milestones progress, eligible expenditures, hours logged, technical challenges encountered. Submitted within 10 days of month end. | Template prepared before project start |


# 8. Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

