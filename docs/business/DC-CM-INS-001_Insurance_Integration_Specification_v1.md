# DC-CM-INS-001 Insurance Integration Specification v1

**DC-CM-INS-001**
**D-CENTRAL GROUP**
**CivicMesh Inc.**
Insurance Integration Specification


| Document ID | DC-CM-INS-001 |
|---|---|
| Version | 1.0 |
| Status | Draft — SR&ED: novel data product |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Technical |
| SR&ED Eligibility | Strong — novel actuarial data pipeline from community safety infrastructure with privacy-preserving consent management |
| Related Documents | LEGAL-CM-014 | OS-PATROL-TM-INS-001 | DC-CM-PRV-001 | MKT-INS-001 |




| Executive Summary
This specification defines the technical architecture for CivicMesh's insurance data products. Three tiers: aggregate anonymised corridor and community data for actuarial modelling (no consent required beyond community agreement), consent-gated individual driving record API for underwriting (explicit policyholder opt-in), and incident evidence package retrieval for claims and subrogation (individual consent or legal authority). The challenge is building a data pipeline that satisfies both the insurer's need for high-quality behavioural data and PIPEDA's consent requirements — simultaneously. |
|---|


# 1. Insurance Data Product Architecture


| Product | API Endpoint | Data Returned | Consent Layer | Pricing |
|---|---|---|---|---|
| Tier 1 — Aggregate Feed | GET /v1/insurance/aggregate/{region}/{date_range} | Corridor risk scores, violation density heatmaps by type/time/season, road condition index by segment, infrastructure asset condition, collision hotspot analysis — all aggregated to census-tract minimum | Community data federation agreement (no individual consent) | Annual subscription per region |
| Tier 2 — Individual Record | GET /v1/insurance/record/{policyholderDID} | 12-month driving behaviour summary: lane discipline score, following distance percentile, time-of-day risk profile, validated incident history, clean record periods — for consented policyholders only | Individual CCSC member opt-in through Member App — insurer-specific consent per query | Per-query fee at underwriting renewal |
| Tier 3 — Incident Evidence | GET /v1/insurance/evidence/{packageId} | Video clip, GPS coordinates, timestamp, violation classification, confidence score, node credential chain, chain of custody documentation — for specific identified claims | Individual consent (claims process) OR legal authority (subrogation) | Per-package flat fee |
| Tier 4 — Premium Reduction Certification | POST /v1/insurance/certify/{communityId} | Community coverage density report, node uptime statistics, evidence quality metrics — confirms community meets premium reduction threshold | Community manager certification request | Annual certification fee |


# 2. Individual Record API — Consent Flow
- Insurer submits Tier 2 query with: policyholderDID, insurer DID, query purpose, policy renewal date;
- Platform verifies: insurer DID has active Insurance Data Sharing Agreement (LEGAL-CM-014);
- Platform checks member consent store: does this member have an active, non-revoked consent for this specific insurer's Tier 2 access?
- If consent absent or revoked: return 403 Forbidden with reason CONSENT_NOT_FOUND or CONSENT_REVOKED;
- If consent valid: generate 12-month behaviour summary from member's evidence package history → anonymise to remove specific location patterns → return summary;
- Log query to member's consent history (visible in Member App → Privacy → Data Accesses);
- Log query to insurer's audit log (available for annual audit under LEGAL-CM-014 Section 5).
# 3. UBI (Usage-Based Insurance) Backbone


| Technical Challenge (SR&ED) | Existing UBI products (Intact MyDriving, Desjardins Ajusto) collect self-reported telematics data from the insured driver's own device. CivicMesh collects third-party validated data from the road infrastructure. The challenge is: how to construct a behavioural risk score from enforcement-adjacent data (violations detected near a vehicle) without creating adverse selection problems or proxy discrimination. |
|---|---|
| Data Sources for UBI | (1) Violations attributed to the insured vehicle (from opted-in enforcement data); (2) Following distance events (from TrafficMesh node footage when insured vehicle is visible); (3) Speed percentile (from OBD-II data for opted-in node-owning drivers); (4) Time-of-day pattern (from node location data for opted-in drivers). |
| Privacy Constraint | UBI data is available ONLY for drivers who have explicitly opted in through the Member App. Drivers who have not opted in receive no UBI pricing — their standard premium applies. No inference about opted-out drivers from network data. |
| Anti-Discrimination Design | Risk scoring uses only behaviour attributes, not geographic proxies. Corridor risk scores are not applied to individual drivers based on where they live — only where they drive and how they behave there. |


# 4. Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

