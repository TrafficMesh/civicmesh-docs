# DC-CM-B2B-003 Platform Driver Programme Specification v1

**DC-CM-B2B-003**
**D-CENTRAL GROUP**
**CivicMesh Inc.**
Platform Driver Programme Specification — Model B


| Document ID | DC-CM-B2B-003 |
|---|---|
| Version | 1.0 |
| Status | Draft — Technical + Legal |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Technical / Legal |
| Applies To | Uber Canada, Lyft Canada — rideshare platform API agreements and driver opt-in programmes |
| Related Documents | DC-CM-B2B-001 | DC-CM-B2B-005 | DC-CM-B2B-006 | LEGAL-CM-013 | DC-CM-APP-002 |




| Executive Summary
The Platform Driver Programme has two interlocking components: a Platform API Agreement with Uber/Lyft corporate (company-level, technical integration) and an individual Driver Opt-In Programme (driver-level, voluntary, managed through the CivicMesh Member App). The platform API agreement enables context enrichment and revenue routing. The driver opt-in programme is where the value actually flows — to drivers, not to Uber. This distinction is critical in every communication with drivers and regulators: CivicMesh is a driver benefit programme that Uber enables, not a driver monitoring programme that Uber operates. |
|---|


# 1. Platform API Agreement — Scope


| API Capability | Permitted Use | Prohibited Use | Technical Implementation |
|---|---|---|---|
| Trip status endpoint | Enrich evidence packages with on_trip boolean at time of capture. Allows context-aware evidence routing. | Real-time driver tracking. Passenger identification. Route reconstruction. | Webhook push from Uber to CivicMesh on trip start/end. CivicMesh stores trip_active flag only — no route data. |
| Driver DID mapping | Link driver's Uber account to their self-sovereign CivicMesh DID for credential and revenue purposes — with driver consent. | Any use without explicit driver consent. Bulk export of driver identities. Identity linkage to non-consented purposes. | Driver initiates linking in Member App. Uber API confirms driver active status. Mapping stored encrypted, accessible only by driver and for revenue routing. |
| Revenue routing API | Route driver citation revenue shares to driver's designated payment method via Uber's payout infrastructure (where driver prefers this channel). | Revenue routing for non-consented drivers. Fee deduction beyond the agreed 3% platform cut. | Optional — drivers may also receive revenue directly to CCSC patronage wallet without using Uber's payout channel. |
| Insurance data feed | Aggregate safe driving score for consented drivers shared with Uber's insurance partner (Intact Ontario) for premium reduction calculation. | Individual driver scoring without consent. Using driving scores for driver deactivation decisions. Sharing with non-insurance third parties. | Consented drivers only. Batch weekly aggregate. Insurer receives statistical summary — not individual trip data. |


# 2. Driver Opt-In Programme
## 2.1 Driver Benefits (Lead with These)


| Benefit | Description | Estimated Annual Value to Driver |
|---|---|---|
| Insurance premium reduction | CivicMesh-verified safe driving record reduces Ontario rideshare-specific insurance cost. Verified by Intact or other carrier. | $450–$750 for driver paying $3,000–$5,000/year in rideshare insurance at 15% reduction |
| Citation revenue share | Per-validated-citation revenue attributed to the driver's node. Active downtown/corridor drivers see meaningful volume. | $800–$2,400/year for active Ottawa rideshare driver (estimated 3–8 citations/week at $[X] per citation) |
| False claim protection | Node footage provides evidence against false passenger damage, assault, or behaviour claims. Incident package retrieved within 4 hours of driver report. | Value varies — one defended false claim can save the driver's deactivation |
| D-Credit ecosystem | Revenue earned in D-Credit spends at: vehicle maintenance partners, fuel discount programmes, training certification fee waivers. | Spending power within cooperative ecosystem |
| CCSC cooperative membership | Governance rights, AGM participation, collective voice on platform standards. | Non-monetary — governance value |


## 2.2 Driver Opt-In Process
- Driver downloads CivicMesh Member App (or accesses via Uber Driver app integration if Uber enables in-app module)
- Driver creates DID wallet — self-sovereign, not controlled by Uber or CivicMesh
- Driver completes CivicMesh Rideshare Track certification (2-hour online, $149 or waived by Uber as driver benefit)
- Driver purchases or receives Tier 3 node kit (magnetic mount, OBD-II power, 5-minute install)
- Driver installs node in vehicle using Technician App guided workflow — generates InstallationCredential signed by driver DID
- Driver links Uber account to CivicMesh DID in Member App — Uber API confirms active driver status
- Driver configures consent: citation revenue opt-in, insurance data share opt-in (select insurer), trip context enrichment opt-in
- Commercial Driver Credential issued by Uber's DID to driver's DID — driver is now enrolled
## 2.3 Anti-Surveillance Protections — Non-Negotiable


| Driver Rights — Cannot Be Waived by Uber or CivicMesh
• Driver opt-in is voluntary. No Uber driver can be required to install a CivicMesh node as a condition of driving for Uber.
• Citation revenue flows directly to the driver — Uber cannot intercept or redirect citation revenue without the driver's explicit instruction.
• CivicMesh data cannot be used by Uber for driver performance monitoring, deactivation decisions, or any workforce management purpose. This is written into the Platform API Agreement.
• Driver can opt out at any time by deactivating the node in the Member App. No penalty. No effect on Uber driver status.
• If Uber terminates the Platform API Agreement, driver's CivicMesh DID, CCSC membership, and accumulated patronage are unaffected — driver's cooperative membership is independent of Uber. |
|---|


# 3. Uber Pitch Strategy


| Entry Point | Uber Canada's driver experience or driver earnings team — not their legal or policy team first. Frame as a driver earnings and retention initiative. |
|---|---|
| Business Case for Uber | Driver retention: CivicMesh opt-in drivers earn more and pay less for insurance — lower churn. Driver acquisition: competitive advantage in recruiting ('Uber drivers in Ottawa earn up to $[X] more per year through the CivicMesh programme'). Safety marketing: 'Uber vehicles equipped with CivicMesh road safety technology'. |
| Legal Team Objections | Primary: surveillance liability. Answer: the Platform API Agreement prohibits any use of CivicMesh data for driver monitoring. Secondary: competitive data sharing. Answer: aggregate only — no individual driver routing data shared with competitors or municipalities beyond citation evidence. |
| Regulatory Consideration | Ontario's Working for Workers Act (and subsequent gig worker legislation) creates obligations for platform companies around driver transparency. CivicMesh is a driver benefit programme, not a monitoring tool — this framing aligns with regulatory direction, not against it. |


# 4. Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

