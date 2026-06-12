# MKT-INS-002 Insurance Data Product Catalogue v1

**MKT-INS-002**
**D-CENTRAL GROUP**
**D-Central Group / CivicMesh Inc.**
CivicMesh Insurance Data Product Catalogue


| Document ID | MKT-INS-002 |
|---|---|
| Version | 1.0 |
| Status | Draft |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Marketing |
| Format | Product catalogue for insurance actuarial and commercial lines teams |
| Purpose | Detailed description of each data product with pricing, access model, and sample metrics |
| Related Documents | DC-CM-INS-001 | LEGAL-CM-014 | FIN-TM-001 | MKT-INS-001 |


# 1. Product Catalogue


| Product | Description | Data Included | Access Model | Pricing |
|---|---|---|---|---|
| Tier 1 — Ottawa Corridor Risk Feed | Aggregate anonymised road intelligence for actuarial and risk teams. No PII. Updated weekly. | Corridor risk scores (0–100) by postal code and road segment. Violation density heatmaps by type, time of day, day of week, season. Road condition index by segment. Infrastructure quality scores. Collision hotspot analysis. | Annual subscription. API access or CSV download. No individual consent required — aggregate only. | $50,000–$150,000/year depending on data depth and update frequency. Volume pricing for multi-city agreements. |
| Tier 2 — Individual Driving Record API | Consent-gated verified driving record for opted-in policyholders at renewal. Per-query billing. | 12-month behaviour summary: lane discipline score (percentile vs Ottawa drivers), following distance score, time-of-day risk profile, hard braking frequency, clean period confirmation, violation count by type. | Per-query REST API call. Policyholder opt-in required per insurer (managed through CivicMesh Member App). Query rejected if no active consent. | $15–$25 per query. Query charged on submission regardless of result. |
| Tier 3 — Incident Evidence Package | Specific incident footage for identified claims — subrogation, fraud investigation, liability disputes. | Video clip (original, unmodified, chain-of-custody sealed). GPS coordinates. Timestamp. Violation/incident classification. Confidence score. Node credential chain (full provenance proof). | Per-incident request via secure portal. Individual consent (from claims process) or legal authority required. Per-package flat fee. | $50–$75 per package retrieved. |
| Tier 4 — Premium Reduction Certification | Annual community coverage density certification for HOA/fleet clients seeking premium reductions. | Community node count, coverage density map, average node uptime, evidence pipeline reliability score, evidence quality metrics. | Annual report submitted directly to insurer's underwriting team with community manager authorization. | $500–$1,000 per community per year. |


# 2. Premium Discount Tier Schedule


| Participation Level | Discount Range | Notes |
|---|---|---|
| Hardware installed, community enforcement opt-in active, no individual data share | 3–5% | Community safety contribution discount — applies at the household/vehicle level if community is certified |
| Full opt-in: verified driving record shared with this insurer | 8–12% | Verified safe driver discount — applies to policyholders who opt in via Member App |
| Clean enforcement record 12 months (no violations attributed to vehicle) | Additional 5–8% | Clean record bonus — policyholders with zero attributed violations receive additional reduction |
| Opted-in driver in high-coverage community (>80% street coverage) | Additional 2–3% | High-coverage community bonus — network density reward |
| Commercial fleet — CivicMesh certified, >80% fleet enrolled | 8–15% fleet premium | Commercial lines product — applicable to rental, car share, delivery fleet clients |


# Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

