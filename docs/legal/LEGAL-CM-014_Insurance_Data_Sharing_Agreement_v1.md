# LEGAL-CM-014 Insurance Data Sharing Agreement v1

**LEGAL-CM-014**
**D-CENTRAL GROUP**
**CivicMesh Inc.**
Insurance Data Sharing Agreement — Template


| Document ID | LEGAL-CM-014 |
|---|---|
| Version | 1.0 — Template |
| Status | Draft — customize per insurer at execution |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Legal |
| Parties | CivicMesh Inc. (Data Provider) and [Insurance Company Name] (Data Subscriber) |
| Agreement Type | B2B data subscription agreement — not a surveillance or enforcement agreement |
| Related Documents | LEGAL-CM-013 | DC-CM-INS-001 | OS-PATROL-TM-INS-001 | REG-CM-001 | MKT-INS-001 |




| Executive Summary
This agreement governs the terms under which an insurance company subscribes to CivicMesh data products for actuarial, underwriting, claims, and fraud investigation purposes. Three data product tiers are available: aggregate anonymised corridor and community data (no PII — actuarial teams), consent-gated individual driving record API (opt-in policyholders only — underwriting), and incident evidence package retrieval (specific claims — claims and subrogation teams). All data sharing requires consent at every level, is prohibited from being used for re-identification or adverse purposes, and is auditable on request. |
|---|


# 1. Data Product Tiers


| Product Tier | Description | Data Included | PII? | Pricing Model |
|---|---|---|---|---|
| Tier 1 — Aggregate Feed | Anonymised aggregate data for actuarial modelling and risk assessment | Corridor risk scores, violation density heatmaps by type/time/season, road condition index by segment, infrastructure asset condition, collision hotspot analysis | No PII — all data aggregated and anonymised to census-tract level minimum | Annual subscription — per city/region |
| Tier 2 — Individual Record API | Consent-gated verified driving record for opted-in policyholders at renewal | 12-month driving behaviour summary: lane discipline, following distance, time-of-day profile, validated incident history (consented policyholders only) | Yes — PII for consented individuals only. No consent = no access. | Per-query fee at underwriting renewal |
| Tier 3 — Incident Evidence Package | Specific incident footage and metadata for claims investigation and subrogation | Video clip, GPS coordinates, timestamp, violation classification, confidence score, full node credential chain (chain-of-custody proof) | Minimum necessary PII for identified claim only | Per-package flat fee |


# 2. Consent Requirements by Tier


| Tier | Consent Required | Consent Mechanism | Consent Verification |
|---|---|---|---|
| Tier 1 — Aggregate | Community-level consent only (municipal federation agreement or HOA participation agreement) | Already obtained via LEGAL-CM-011 or LEGAL-CM-012 | CivicMesh Inc. confirms community consent before any Tier 1 data is included in the feed |
| Tier 2 — Individual | Explicit individual policyholder opt-in — separate consent per insurer | Policyholder opts in through CivicMesh Member App (DC-CM-APP-002) — selects specific insurer(s) to share record with | CivicMesh Inc. verifies active consent credential in member DID wallet before processing any Tier 2 query. Query rejected if consent is absent, expired, or revoked. |
| Tier 3 — Incident | Individual claimant consent OR valid legal authority (court order, subpoena) | Consent obtained through claims process or legal authority presented to CivicMesh Inc. | CivicMesh Inc. logs every Tier 3 retrieval in public audit trail (case reference only) |


# 3. Prohibited Uses
The Data Subscriber expressly agrees that CivicMesh data products will not be used for:
- Re-identification of any anonymised individual from Tier 1 aggregate data;
- Denial of insurance coverage based solely on residence in a geographic area covered by CivicMesh nodes (proxy discrimination);
- Sharing with or selling to any third party, including reinsurers, without CivicMesh Inc.'s prior written consent;
- Any immigration enforcement, law enforcement, or government surveillance purpose (insurance data products are strictly for insurance underwriting, claims, and fraud purposes);
- Political monitoring or profiling of any individual or group;
- Any purpose not listed in Schedule A of this agreement (purposes listed at execution).


| Prohibited use clause is a material term. Breach of any prohibited use provision entitles CivicMesh Inc. to terminate this agreement immediately and requires the Data Subscriber to destroy all CivicMesh data in its possession within 24 hours. |
|---|


# 4. Pricing Summary


| Product | Pricing Model | Estimated Range | Renewal |
|---|---|---|---|
| Tier 1 — Aggregate Feed | Annual subscription per geographic region | $[TBD] per city/region per year — scales with data coverage area | Annual renewal with 60-day notice |
| Tier 2 — Individual Record Query | Per-query fee at policyholder renewal | $[TBD] per query — payable on query submission regardless of result | No subscription — pay-per-use |
| Tier 3 — Incident Evidence Package | Per-package flat fee | $[TBD] per package retrieved | No subscription — pay-per-use |
| Premium Reduction Certification | Annual certification fee for communities | $[TBD] per community per year — insurer pays to certify coverage density | Annual |


# 5. Audit and Compliance


| CivicMesh Audit Right | CivicMesh Inc. has the right to audit Data Subscriber's use of CivicMesh data products annually with 15-business-day notice. Subscriber must provide access to relevant data use records. |
|---|---|
| Consent Audit | For Tier 2 queries, CivicMesh Inc. maintains a complete log of every consent verification. Subscriber may request a consent audit report for any query within 12 months of the query date. |
| Data Breach | Data Subscriber must notify CivicMesh Inc. within 24 hours of any suspected breach involving CivicMesh data. Subscriber is liable for any breach resulting from inadequate security controls on its side. |
| Regulatory Changes | If PIPEDA or any provincial privacy law changes in a way that affects the lawfulness of any data product, CivicMesh Inc. may suspend that product immediately. Subscriber receives pro-rata refund of any prepaid subscription fees for the suspended period. |


# 6. Incentive Programme Integration


| Premium Discount Commitments | Insurer agrees to offer the following minimum premium discounts to opted-in CivicMesh members: [Tier table to be negotiated per insurer — reference MKT-INS-001 discount table]. These discounts are the primary civilian opt-in incentive and are material to the CivicMesh community value proposition. |
|---|---|
| Fast-Track Claims | Insurer agrees to offer an expedited claims process for opted-in policyholders involved in incidents where CivicMesh footage is available. Target: claims resolution within [X] business days where footage is determinative. |
| Marketing Collaboration | Insurer may market the premium discount programme to its customers using CivicMesh-approved marketing materials. Co-branded materials require CivicMesh Inc. approval before distribution. |


# 7. Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

