# DC-CM-B2B-002 Fleet Operator Agreement Template v1

**DC-CM-B2B-002**
**D-CENTRAL GROUP**
**CivicMesh Inc.**
Fleet Operator Agreement Template — Model A


| Document ID | DC-CM-B2B-002 |
|---|---|
| Version | 1.0 — Template |
| Status | Draft — customize per fleet operator at execution |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Legal |
| Applies To | Enterprise Rent-A-Car, Hertz, National, Communauto, Zipcar, Turo, CAA — any company-owned or managed vehicle fleet |
| Agreement Type | B2B commercial agreement — company level, not driver level |
| Related Documents | DC-CM-B2B-001 | DC-CM-B2B-005 | LEGAL-CM-010 | LEGAL-CM-014 | DC-CM-PRV-001 |




| Executive Summary
The Fleet Operator Agreement governs the relationship between CivicMesh Inc. and a commercial fleet operator that deploys CivicMesh nodes across their vehicle fleet. The fleet operator is the single contracting party — they own the vehicles, manage the hardware, and receive the revenue share. Individual drivers or renters are not party to this agreement and are not identified in any evidence package. The fleet operator's primary value proposition is insurance premium reduction, citation revenue, and the fleet documentation tool. |
|---|


# 1. Fleet Operator Obligations


| Obligation | Detail | Consequence of Breach |
|---|---|---|
| Hardware Installation | Install CivicMesh-certified node hardware on all enrolled vehicles using certified technicians within [90] days of agreement execution. Maintain nodes in operational condition. | Non-operational nodes generate no revenue. Persistent non-installation triggers platform fee abatement and potential termination. |
| Node Maintenance | Report hardware failures to MSSP NOC within 24 hours. Cooperate with technician access for maintenance and replacement. Do not modify node hardware. | Tamper event logged on-chain. Fleet operator liable for evidence integrity failures caused by unauthorized hardware modification. |
| Privacy Compliance | Do not attempt to link node evidence packages to individual renters, customers, or passengers. Do not request renter/customer identity from CivicMesh. Maintain PIPEDA compliance for any fleet operational data. | Material breach. Immediate agreement termination. Regulatory reporting if breach involves personal data. |
| No Adverse Use | Do not use CivicMesh data for employee discipline, customer profiling, or any purpose beyond fleet safety and revenue sharing. | Material breach. Immediate termination. Credentials revoked on-chain within 24 hours. |
| Coverage Reporting | Submit monthly fleet roster update (VIN → node DID mapping) reflecting vehicle additions and retirements. | Revenue attribution errors. Fleet operator liable for citation revenue miscalculation. |


# 2. Revenue and Fee Structure


| Revenue / Fee Item | Amount | Timing | Notes |
|---|---|---|---|
| Node Hardware — supplied by DCMC | Cost price (wholesale DCMC rate) per node | One-time at installation | Fleet operators purchase at cost — no markup. Volume discount available for fleets >500 vehicles. |
| Installation Fee | $[TBD] per vehicle (certified technician rate) | One-time at installation | D-Central Shield or certified MSSP installs. Fleet operator may use their own certified technicians (L2 minimum) to reduce this cost. |
| Monthly NOC Management Fee | $[TBD] per active node per month | Monthly recurring | Covers: node health monitoring, firmware updates, model updates, evidence pipeline, NOC support. |
| Citation Revenue Share — Fleet Operator Receives | 25% of net citation revenue attributed to fleet nodes | Monthly payout | Net = gross citation amount minus municipal administration fee. Paid monthly to fleet operator's designated account. |
| Insurance Premium Reduction | Negotiated directly between fleet operator and their insurer (CivicMesh facilitates) | Annual at policy renewal | CivicMesh issues fleet coverage density certification to insurer. Reduction amount at insurer's discretion — typically 5–15% of fleet premium. |
| Digital Twin Data Revenue Share | 5–10% of any data marketplace revenue attributable to fleet node observations | Quarterly | Applies primarily to delivery-type fleets with high digital twin contribution. Rental and car share fleets receive lower share. |


# 3. Data Governance


| Data Owned by Municipality | All enforcement evidence packages — citations and their supporting footage — are ultimately owned by the municipality for enforcement purposes. Fleet operator has no ownership rights in citation evidence. |
|---|---|
| Data Owned by Fleet Operator | Aggregate fleet safety metrics generated from their fleet's node observations (not individual citation records). Available via the Analytics Platform Tier 1 subscription. |
| Renter/Customer Identity | Never collected, never stored, never linked to any CivicMesh record. The agreement prohibits CivicMesh from maintaining any renter identity database. Vehicle DID only. |
| On Termination | All node DIDs deactivated on-chain within 24 hours of termination effective date. Fleet operator returns or destroys all node hardware within 30 days. CivicMesh retains no fleet-specific data after termination. |


# 4. Insurance Facilitation


| Coverage Density Report | CivicMesh generates a monthly fleet coverage density report showing: percentage of fleet enrolled, average node uptime, evidence pipeline reliability, and any tamper events. This report is delivered to the fleet operator and, with their authorization, shared with their commercial fleet insurer. |
|---|---|
| Insurer Introduction | CivicMesh facilitates an introduction between the fleet operator and our insurance data subscriber network (Intact Commercial, Aviva Commercial, Zurich). Fleet operator negotiates their own premium reduction directly. CivicMesh earns the Tier 1 data subscription from the insurer separately. |
| No Premium Guarantee | CivicMesh does not guarantee any specific insurance premium reduction. The reduction is at the insurer's sole discretion based on the coverage density report and their own actuarial assessment. |


# 5. Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

