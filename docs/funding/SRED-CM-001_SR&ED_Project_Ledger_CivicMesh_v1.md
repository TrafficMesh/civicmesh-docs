# SRED-CM-001 SR&ED Project Ledger CivicMesh v1

**SRED-CM-001**
**D-CENTRAL GROUP**
**CivicMesh Inc. / D-Central Group Inc.**
SR&ED Project Ledger — CivicMesh Platform


| Document ID | SRED-CM-001 |
|---|---|
| Version | 1.0 — Living document, updated daily |
| Status | ACTIVE — set up Day 1, non-negotiable |
| Date | May 2026 — ongoing |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — CRA Documentation |
| Programme | Scientific Research and Experimental Development (SR&ED) Tax Incentive Programme |
| Filing Entity | CivicMesh Inc. (or D-Central Group Inc. if not yet incorporated) — CCPC — refundable credits |
| Refund Rate | Up to 35% federal refundable investment tax credit on first $3M of qualified SR&ED expenditures for CCPCs. Ontario provincial top-up adds up to 30% = up to 65% combined refund. |
| Related Documents | SRED-TM-001 | IRAP-CM-001 | IRAP-TM-001 | FIN-CM-001 |




| Executive Summary
SR&ED is Canada's largest government R&D programme — $4.5 billion annually in federal budget. For CCPCs, it is refundable, meaning you receive cash back even with no taxable income. The critical requirement is CONTEMPORANEOUS EVIDENCE — documentation created on the day the R&D occurs, not reconstructed months later. This document establishes the documentation system and daily habits that are worth tens of thousands of dollars in refunds. CRA audits SR&ED claims looking specifically for contemporaneous evidence: Git commit logs, code PRs, Jira/Linear tickets, and technical decision log entries created on the actual dates R&D occurred. |
|---|




| DAY 1 NON-NEGOTIABLE: Before writing a single line of code or running a single model training experiment, set up the GitHub organization, the Linear workspace with SR&ED eligibility tags, and start the Technical Decision Log. Every day you build without this documentation system in place is a day of SR&ED evidence you cannot recover. |
|---|


# 1. SR&ED Eligibility Criteria
For work to qualify as SR&ED, it must satisfy all three of the following criteria:


| Criterion | Definition | CivicMesh Application |
|---|---|---|
| Scientific or Technological Advancement | The work must advance knowledge in a field of science or technology — it must attempt to resolve a technological uncertainty | Edge AI violation detection in Canadian conditions, privacy-preserving federation protocol, on-device ALPR optimization, DID/VC chain for evidence admissibility, OBD-II telemetry fusion — all advance knowledge in their respective fields |
| Scientific or Technological Uncertainty | There must be a technological uncertainty that cannot be resolved through standard practice, routine engineering, or commonly available knowledge | See IRAP-CM-001 Section 3 — five documented areas of genuine technical uncertainty where the solution is not known in advance |
| Scientific or Technological Content | The work must follow a systematic investigation and experimentation process — hypothesis, experimentation, documentation of results (including failures) | GitHub commit history, Linear task log, Technical Decision Log, and model training experiment logs provide the systematic investigation record CRA requires |


# 2. What Qualifies — CivicMesh Specific


| Activity | SR&ED Eligible? | Notes |
|---|---|---|
| Edge AI model development and training for violation detection | ✅ YES — Primary | Each training run, hyperparameter search, and model evaluation is SR&ED work. Document every experiment. |
| ALPR optimization for Canadian weather and plate conditions | ✅ YES — Primary | Systematic investigation with documented failed approaches is the core SR&ED narrative. |
| Privacy-preserving federation protocol design and implementation | ✅ YES — Primary | Novel architecture with no existing solution — strong SR&ED claim. |
| DID/VC credential schema design and blockchain implementation | ✅ YES — Strong | Novel application of W3C standards to enforcement evidence — documentable uncertainty. |
| Digital twin mapping engine development | ✅ YES — Strong | Novel continuous-update algorithm from mobile nodes — no existing comparable system. |
| OBD-II telemetry fusion with camera evidence | ✅ YES — Strong | Speed estimation from moving reference frame — novel sensor fusion problem. |
| On-device face/plate blurring pipeline | ✅ YES — Moderate | Novel privacy-preserving processing constraint — document the optimization challenge. |
| MeshEar acoustic classification model development | ✅ YES — Primary | Custom model for Canadian acoustic environment — strong R&D claim. |
| Officer review portal UI development | ❌ NO — Routine | Standard React/FastAPI development. Not SR&ED. Segregate time carefully. |
| Business development, sales, marketing | ❌ NO | Not SR&ED eligible under any circumstances. |
| Routine software integration (API calls to third-party services) | ❌ NO | Not SR&ED eligible. Document separately from eligible work. |
| Legal research on HTA compliance (technical-legal intersection) | ⚠️ PARTIAL | The technical design work driven by legal uncertainty may qualify. Discuss with SR&ED consultant. |


# 3. The Documentation System — Daily Habits
## 3.1 GitHub Commit Messages — The Primary Evidence Record
Every commit message must describe the technical problem being solved, not just what was changed. CRA looks at commit history as primary evidence of systematic investigation.


| Not SR&ED Evidence (DO NOT WRITE) | SR&ED Evidence (WRITE THIS) |
|---|---|
| Fix null pointer | Resolve null pointer in GPS timestamp sync during LTE dropout — root cause: race condition in buffer flush. Third approach tried — previous two (mutex lock, queue timeout) caused unacceptable latency. |
| Update ALPR model | Retrain ALPR model v7 on augmented Ottawa winter dataset — added 2,400 frost-occluded plate images. F1 score 0.71 → 0.78 on held-out test set. Still below 0.85 production threshold. Next: investigate preprocessing defogging filter. |
| Add federation endpoint | Implement federation consent gate v3 — previous design (v2) failed audit log atomicity requirement when federation request and community notification fired simultaneously. New approach uses database transaction wrapping both operations. |
| Fix bug in twin update | Debug digital twin update latency — 340ms avg vs 100ms target. Profiled: bottleneck is PostGIS spatial index query on high-density node update. Investigating: (1) spatial index rebuild, (2) update batching, (3) Redis spatial cache. |


## 3.2 Linear Task Tags — SR&ED Eligibility Tagging
Every task in Linear must be tagged at creation with:
- SR&ED Eligible: Yes / No / Partial
- SR&ED Project: [CivicMesh-Platform / TrafficMesh / CommunityShield / MeshEar / MeshNav]
- Technical Uncertainty Area: [Edge AI / ALPR / Federation / DID-VC / OBD-Fusion / Acoustic / Digital-Twin / Other]
- IRAP Milestone: [M1 through M6 — per IRAP-CM-001]
At year end, the SR&ED consultant exports the Linear report filtered by SR&ED Eligible: Yes and the claim is built from the tagged task list. This saves 40+ hours of consultant time and significantly reduces audit risk.
## 3.3 Technical Decision Log — The SR&ED Narrative
A running Google Doc or Notion page maintained daily. For every significant technical decision:


| Field | What to Record |
|---|---|
| Date | The actual date — not a retroactive entry |
| Technical Problem | What specific technical problem or uncertainty was being addressed |
| Approaches Considered | At least 2–3 approaches considered, with brief rationale for why each was or was not pursued |
| Approach Chosen | What was selected and why |
| Result | What happened — including failures (failures are SR&ED gold — they prove the uncertainty was genuine) |
| Remaining Uncertainty | What is still unknown — what needs to be investigated next |
| Time Spent | Hours spent on this investigation (must match time tracking records) |


## 3.4 Time Tracking — The Financial Foundation
Every team member must log hours DAILY using Harvest, Toggl Track, or equivalent. Time entries must include:
- Date and duration
- Project (CivicMesh Platform / TrafficMesh / etc.)
- Task (with Linear ticket reference)
- SR&ED eligible: Yes / No
- Brief description of work performed


| Time tracking is where most SR&ED claims are reduced or denied on audit. A claim for $80,000 in eligible salaries must be supported by time records showing those hours were spent on eligible activities. Retroactive time reconstruction is not accepted by CRA. Log daily — no exceptions. |
|---|


# 4. SR&ED Financial Summary Template


| Expenditure Category | Estimated Annual Amount | IRAP Reduction (80% of eligible salary) | Net SR&ED Eligible | Federal ITC (35% of net) | Ontario Top-Up (~30% of net) | Total Refund |
|---|---|---|---|---|---|---|
| Founder salary (eligible portion) | $75,000 | $60,000 | $15,000 | $5,250 | $4,500 | $9,750 |
| Additional staff salary (eligible portion — 1 FTE) | $70,000 | $56,000 | $14,000 | $4,900 | $4,200 | $9,100 |
| Contractor costs (eligible portion) | $20,000 | $10,000 (50% IRAP) | $10,000 | $3,500 | $3,000 | $6,500 |
| Materials (hardware, cloud compute) | $15,000 | $0 (not IRAP-eligible) | $15,000 | $5,250 | $4,500 | $9,750 |
| TOTAL | $180,000 | $126,000 | $54,000 | $18,900 | $16,200 | $35,100 |


Note: Actual amounts depend on IRAP approval scope and eligible salary determination. Engage a qualified SR&ED consultant (suggest FSI Digital, Brainhunter, or similar Ottawa-based firm) in Month 1 to review this model and set up the claim structure before year end. Consultant fee is itself SR&ED-eligible as a contractor cost.
# 5. Annual SR&ED Claim Process


| Step | Action | Timing |
|---|---|---|
| 1 | Year-end close: export GitHub commit history, Linear SR&ED-tagged tasks, time tracking records, Technical Decision Log entries | December 31 / fiscal year end |
| 2 | SR&ED consultant reviews documentation, identifies eligible activities, calculates eligible expenditures | January — February |
| 3 | IRAP reimbursements received during the year are deducted from eligible expenditure calculations | February |
| 4 | T2 corporate income tax return prepared with Schedule 31 (Investment Tax Credit) and Form T661 (SR&ED expenditures) | March — April |
| 5 | CRA processes refund — typically 60–120 days after filing for e-filed returns | June — August |
| 6 | If CRA selects for audit: provide all contemporaneous documentation. Do not modify any records. | As required |


# 6. Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

