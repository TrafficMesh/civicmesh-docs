# DC-CM-B2B-001 Commercial Vehicle Network Programme v1

**DC-CM-B2B-001**
**D-CENTRAL GROUP**
**CivicMesh Inc.**
CivicMesh Commercial Vehicle Network Programme


| Document ID | DC-CM-B2B-001 |
|---|---|
| Version | 1.0 |
| Status | Draft — strategic architecture document |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Strategic |
| Programme Tier | Tier 4 — Commercial Vehicle Networks (sits between city fleet Tier 1–3 and civilian Tier 5) |
| Related Documents | DC-CM-B2B-002 through DC-CM-B2B-006 | DC-CM-ARCH-001 | DC-CM-INS-001 | LEGAL-CM-013 | DC-CM-GTM-001 |




| Executive Summary
The CivicMesh Commercial Vehicle Network Programme (CVN Programme) is a B2B tier that extends the enforcement node network through commercial vehicle fleets — rideshare platforms (Uber, Lyft), car rental companies (Enterprise, Hertz, National), peer-to-peer car share (Turo), one-way car share (Communauto, Zipcar), taxi and licensed operators, and commercial delivery networks (UPS, FedEx, Amazon Logistics, Purolator). Where the civilian opt-in model (Tier 5) reaches individual residents one at a time, the CVN Programme reaches thousands of vehicles through a single B2B agreement. One Enterprise contract = 2,000 fleet vehicles. One Uber API agreement = 3,000–5,000 active Ottawa drivers. The CVN Programme is the fastest path to network density — the critical variable that determines enforcement accuracy, insurance data quality, and digital twin coverage. |
|---|


# 1. Strategic Rationale
## 1.1 Why Commercial Fleets Change the Network Economics
The CivicMesh network's value compounds with node density. The faster density is achieved, the faster the flywheel accelerates: better coverage → better enforcement accuracy → better insurance data → more insurer subscriptions → more civilian opt-in incentive funding → more nodes. The civilian opt-in model builds density gradually — one resident, one dashcam at a time. Commercial fleet agreements build density in step changes — one contract, thousands of nodes.


| Deployment Path | Nodes Added | Effort | Timeline |
|---|---|---|---|
| Single HOA deployment | 20–80 nodes | 1 community manager, 1 NOC client | 2–4 weeks |
| OC Transpo pilot (30 buses) | 30 nodes | 1 city contract, 30 installations | 3–6 months |
| Communauto fleet agreement | 300–500 nodes | 1 B2B contract, fleet provisioning | 2–4 months |
| Enterprise Rent-A-Car Ottawa | 800–1,200 nodes | 1 B2B contract, fleet provisioning | 3–6 months |
| Uber Ottawa driver programme | 3,000–5,000 nodes (opt-in rate dependent) | 1 API agreement + driver opt-in campaign | 6–12 months |
| Amazon Logistics Ottawa delivery fleet | 200–400 nodes | 1 B2B contract, fleet provisioning | 2–3 months |


## 1.2 Unique Contributions of Each Commercial Fleet Type


| Fleet Type | Network Contribution | Coverage Advantage | Data Quality Advantage |
|---|---|---|---|
| Rideshare (Uber/Lyft) | High-density urban corridor coverage, 18+ hours/day operation | Downtown and suburban coverage during commute hours and late nights when city fleet is sparse | Trip context enrichment — evidence packages carry 'on-trip' or 'off-trip' status, known route context |
| Car rental (Enterprise/Hertz) | Visitor and tourist vehicles — diverse driving patterns not captured by residents | Airport corridors, hotel areas, business districts covered systematically | Known vehicle identity, professional maintenance, reliable hardware uptime |
| One-way car share (Communauto/Zipcar) | City-wide distribution by design — vehicles must be accessible across the city | Residential neighbourhoods and transit hubs — fills gaps between city fleet routes | Known start/end locations, member driving data for UBI (consented) |
| Peer-to-peer car share (Turo) | Long-tail coverage — Turo vehicles are parked across all city neighbourhoods | Residential street coverage during evenings and weekends | Individual owner opt-in — highest-quality consent, clearest data ownership |
| Taxi and licensed operators | High mileage, high hours, professional drivers, defined service areas | Airport, hospital, hotel corridors, late-night coverage | Professional driver credentials, clear liability chain |
| Commercial delivery (UPS/FedEx/Amazon) | Repeating daily routes covering every postal code | Last-mile residential coverage — every street every day | Highest digital twin contribution — same streets observed daily, time-anchored |


# 2. Programme Structure — Four Agreement Models
Commercial Vehicle Network participants fall into four structural categories based on vehicle ownership and driver relationship. Each has a distinct agreement model, revenue structure, and technical integration approach.
## 2.1 Model A — Fleet Owner Agreement (Enterprise, Communauto, Zipcar, Hertz)


| Vehicle Ownership | Fleet company owns all vehicles. Single contracting party. Simplest legal structure. |
|---|---|
| Agreement Type | DC-CM-B2B-002 — Fleet Operator Agreement. Company-level, not driver-level. |
| Node Installation | Permanent Tier 1-equivalent installation by certified CivicMesh technicians. Fleet company pays hardware cost at cost price (DCMC wholesale). Installation provisioned by D-Central Shield or certified MSSP. Technician credential verified before any installation. |
| Revenue Distribution | City/municipality: 50% | Platform operator fee (CivicMesh): 20% | Fleet company: 25% | CCSC general fund: 5%. Fleet company retains their 25% as direct corporate revenue — not passed to drivers unless fleet company chooses to incentivize drivers separately. |
| Insurance Integration | Fleet company's commercial vehicle insurance policy. CivicMesh issues fleet-level coverage density certification to insurer. Fleet insurer (Intact, Aviva commercial lines) reduces fleet premium based on certified coverage. CivicMesh receives Tier 1 aggregate data subscription from insurer in return — separate revenue stream. |
| Driver Data | Rental/car share drivers are not individually identified in evidence packages. Vehicle DID only. No individual driver tracking unless driver explicitly opts in to individual driving record programme (separate, voluntary). |
| Key Benefit Pitch | 'One contract converts your entire Ottawa fleet into revenue-generating enforcement infrastructure. Your vehicles are already driving these roads — now they earn while they drive. Fleet insurance premium reduction certified from Day 1.' |


## 2.2 Model B — Platform Driver Programme (Uber, Lyft)


| Vehicle Ownership | Individual drivers own their vehicles. Platform (Uber/Lyft) operates the booking and dispatch layer. |
|---|---|
| Two-Level Structure | Level 1: Platform API Agreement (DC-CM-B2B-003) signed with Uber/Lyft corporate. Provides: trip status API, driver DID mapping framework, insurance data feed integration, and revenue routing. Level 2: Driver Opt-In Programme — individual drivers opt in through CivicMesh Member App exactly like civilian members, but with a Commercial Driver credential unlocking platform-specific benefits. |
| Node Installation | Driver-installed using Tier 3 contractor kit equivalent — magnetic mount, OBD-II power, 5-minute install. Certified through CivicMesh Training Institute L1 programme (online-only version for rideshare drivers — simplified track, 2-hour completion, $149 fee or waived by Uber as driver benefit). |
| Revenue Distribution | City/municipality: 50% | Platform operator fee (CivicMesh): 20% | Driver: 22% | Uber/Lyft platform cut: 3% (negotiated). The platform cut is modest by design — Uber's primary benefit is the insurance programme and the driver retention tool, not citation revenue. |
| Driver Incentive Stack | (1) Citation revenue share — per validated citation attributed to their node; (2) Insurance premium reduction — verified safe driving record reduces their rideshare-specific insurance cost; (3) Trip documentation — node footage protects drivers against false passenger claims; (4) D-Credit for vehicle maintenance and fuel discount partners. |
| Insurance Integration — Critical | Ontario rideshare insurance is expensive and complex (personal + commercial hybrid). Intact (Uber's insurer in Ontario) is a priority target for the Tier 2 individual driving record API. An Uber driver with a CivicMesh-verified safe driving record + Tier 2 consent earns a premium reduction that can offset 2–4 months of their insurance cost annually. This is the primary opt-in driver. |
| Trip Context Enrichment | When a CivicMesh node is active in an Uber vehicle, evidence packages carry trip context: on_trip (boolean), platform (Uber), vehicle_type. This context does not identify the passenger — ever. It helps adjudicate evidence weight for certain violation types (e.g. a hard brake event during a passenger trip is contextually different from an empty-vehicle event). |
| Scope Limitation | The Uber API integration is scoped to: trip status, driver DID mapping, insurance data feed, revenue routing. It explicitly excludes: real-time driver location outside of trip context, passenger data of any kind, driver performance monitoring for Uber's workforce management. The agreement prohibits Uber from using CivicMesh data for driver deactivation decisions. This is enforced contractually and technically. |


## 2.3 Model C — Delivery Network Agreement (UPS, FedEx, Amazon Logistics, Purolator)


| Vehicle Ownership | Company-owned or contracted. Similar to Model A but with route-based coverage optimization. |
|---|---|
| Agreement Type | DC-CM-B2B-004 — Delivery Network Agreement. Includes route-based coverage reporting. |
| Node Installation | Permanent installation. Delivery vehicles operate in defined geographic areas on repeating routes — makes coverage mapping predictable and optimizable. Route manager portal shows which postal codes have CivicMesh coverage through delivery fleet nodes. |
| Revenue Distribution | City/municipality: 55% (higher share — delivery networks generate highest digital twin value) | Platform operator fee: 20% | Delivery company: 20% | CCSC general: 5%. |
| Digital Twin Value | Delivery vehicles make the highest digital twin contribution of any network tier. A FedEx driver covering South Ottawa covers the same residential streets every day. Their node generates a time-stamped, weather-correlated road condition update for every postal code in their route — daily. This is more valuable than any other network tier for insurance and municipal planning purposes. Reflected in insurer subscription pricing. |
| Driver Programme | Delivery drivers can opt in to the individual driving record programme for their personal insurance benefit — separate from the corporate fleet agreement. Company facilitates opt-in as an employee benefit. |
| Last-Mile Liability Tool | Evidence packages from delivery vehicles provide route documentation — every stop, every double-park, every loading zone use. This creates a liability management tool for the delivery company: if a claim is made about a delivery vehicle's behaviour, the node footage is the defence. Independent commercial value beyond citation revenue. |


## 2.4 Model D — Taxi and Licensed Operator Agreement


| Operator Types | Ottawa taxi co-ops (Crown, Blue Line), licensed limousine companies, accessible transportation providers, non-emergency medical transport (NEMT) |
|---|---|
| Agreement Type | Adapted from DC-CM-B2B-002 Fleet Operator Agreement — same structure with driver-level opt-in module where drivers own their vehicles. |
| Revenue Distribution | City/municipality: 50% | Platform operator fee: 20% | Operator/driver: 25% | CCSC: 5%. |
| Unique Consideration | Taxi co-ops are cooperative organizations — structurally aligned with D-Central's cooperative model. A taxi co-op joining the CivicMesh network as a member of the CCSC is a natural fit. Their drivers can also be CCSC cooperative members. The cooperative-to-cooperative relationship is a differentiator in the pitch. |
| Accessible Transportation | HandyTrans and NEMT operators cover routes that neither city fleet nor rideshare serves — medical facilities, seniors' residences, disability services. Their coverage fills a geographic and temporal gap. Municipal argument: improved accessibility infrastructure monitoring as a byproduct. |


# 3. Technical Architecture — Commercial Platform Integration Layer
A new API integration layer sits between the CivicMesh platform and commercial partner backends. This layer handles context enrichment, credential management, and revenue routing for all Tier 4 participants.
## 3.1 Platform API Integration Architecture


| Integration | Inbound from Partner | Outbound to Partner | Protocol | Privacy Constraint |
|---|---|---|---|---|
| Uber / Lyft | Trip status (on_trip boolean), driver_platform_id (hashed → maps to driver DID) | Driver citation revenue share (batched weekly), driver safe driving score (consented only), aggregate corridor safety metrics | REST API, OAuth 2.0, webhook for real-time trip events | No passenger data ever. Driver location only during active trip and only for evidence context — not retained. |
| Enterprise / Hertz / National | Fleet vehicle roster (VIN → node DID mapping), vehicle check-out/return events | Fleet coverage density report (weekly), citation revenue share (monthly), fleet insurance certification | REST API, OAuth 2.0, batch file for vehicle roster | No renter identity ever. Vehicle DID only. |
| Communauto / Zipcar | Vehicle availability status, booking start/end events (timestamps only — no member identity) | Coverage map contribution report, citation revenue share, insurance certification | REST API, OAuth 2.0 | No member identity. Vehicle DID + booking period only. |
| Amazon / UPS / FedEx / Purolator | Route assignment (vehicle DID → postal code coverage area), delivery completion events | Digital twin coverage report by postal code, citation revenue share, route safety score | REST API, SFTP for bulk route data | No driver identity in platform. Vehicle DID + route zone only. |
| Turo | Listing vehicle DID registration, host opt-in status, rental period events | Per-rental revenue share, host insurance certification | REST API, OAuth 2.0 | No guest/renter identity. Host opt-in required per vehicle. |


## 3.2 Commercial Driver Verifiable Credential


| Field | Type | Description | Privacy |
|---|---|---|---|
| id | DID string | Driver's own DID (did:ethr:polygon:...) | Driver-controlled |
| platform | String | Platform name: Uber, Lyft, etc. | Non-PII |
| driverStatus | Enum | active / inactive / suspended | Issued by platform DID |
| vehicleDIDs | DID array | Node DIDs installed in driver's enrolled vehicles | Driver controls which vehicles enrolled |
| revenueShareRecipient | DID string | Driver's CCSC patronage wallet DID | Driver-controlled |
| insuranceDataShareConsent | Boolean | Has driver consented to insurance Tier 2 data share? | Explicit opt-in required — false by default |
| insurerDID | DID string or null | DID of the insurer receiving Tier 2 data (null if not consented) | One insurer per credential — new credential issued for each insurer |
| platformCitationShare | Boolean | Has driver opted in to citation revenue sharing? | Explicit opt-in |
| tripContextConsent | Boolean | Has driver consented to trip status enrichment of evidence packages? | Explicit opt-in — false by default |
| issuanceDate | ISO 8601 | Date credential was issued | Non-PII |
| expirationDate | ISO 8601 | Credential expiry — renews with platform active driver status | Non-PII |


## 3.3 Evidence Package Context Enrichment
When a Tier 4 commercial vehicle node generates an evidence package, the following additional fields are added beyond the standard schema (DC-CM-ARCH-001 Section 3):


| Additional Field | Source | Value Example | Privacy |
|---|---|---|---|
| commercial_tier | Node configuration | 4B (rideshare driver) | Non-PII |
| platform | Node configuration | Uber | Non-PII |
| on_trip | Uber API — trip status at capture timestamp | true / false | Non-PII — no trip details, no passenger reference |
| vehicle_type | Node configuration | Rideshare_UberX / Rental_Economy / Delivery_Van | Non-PII |
| route_zone | Delivery API (Model C only) | postal_code_K1A (delivery route zone) | Non-PII — zone only, not specific address |
| driver_credential_hash | Driver's Commercial Driver VC hash | sha256:7f3a... | Non-PII — hash only, no identity |




| Privacy Enforcement: The trip context and driver credential hash fields are only included when the driver has explicitly consented (tripContextConsent: true in their Commercial Driver Credential). A driver who has not consented generates evidence packages identical to civilian Tier 5 packages — no commercial context attached. |
|---|


# 4. Revenue Model and Insurance Integration
## 4.1 Citation Revenue Distribution by Agreement Model


| Agreement Model | Municipality | CivicMesh Fee | Commercial Partner | CCSC | Rationale |
|---|---|---|---|---|---|
| Model A — Fleet Owner (rental/car share) | 50% | 20% | 25% | 5% | Fleet owner provides managed hardware, reliable uptime — higher partner share than civilian |
| Model B — Rideshare Driver (Uber/Lyft) | 50% | 20% | Driver 22% + Platform 3% | 5% | Driver is the primary beneficiary. Platform cut is minimal — their value is distribution and API. |
| Model C — Delivery Network | 55% | 20% | 20% | 5% | Delivery networks generate highest digital twin value. Higher city share reflects infrastructure contribution. |
| Model D — Taxi/Licensed Operator | 50% | 20% | 25% | 5% | Same as fleet owner — operator manages the vehicles |
| Civilian Tier 5 (for comparison) | 50% | 20% | — | 30% | Higher CCSC share because individual citizens have no corporate backing |


## 4.2 Insurance Revenue Amplification
The CVN Programme dramatically expands the insurance data product. Each commercial fleet type unlocks a distinct insurance market segment:


| Commercial Segment | Insurance Product Unlocked | Target Insurer | Revenue Model |
|---|---|---|---|
| Enterprise/Hertz/National fleet | Commercial auto fleet insurance — coverage density certification + aggregate fleet risk data | Intact Commercial, Aviva Commercial, intact fleet division | Tier 1 annual fleet subscription + premium reduction certification fee |
| Communauto/Zipcar one-way car share | Car share operator insurance + member driving records (consented) | Specialized car share insurers (SGI Canada, La Capitale) + major carriers | Tier 1 subscription + Tier 2 per-member renewal query (consented Communauto members) |
| Uber/Lyft drivers | Rideshare-specific personal + commercial hybrid coverage | Intact (Uber's Ontario insurer), Aviva, Desjardins | Tier 2 per-driver renewal query (consented drivers) — high volume, recurring |
| Amazon/UPS/FedEx delivery | Commercial fleet + last-mile delivery liability | Zurich, Intact Commercial, Aviva Commercial | Tier 1 delivery fleet subscription + route risk scoring product |
| Turo peer-to-peer | Peer-to-peer car share host insurance | Intact, Sonnet, specialized P2P insurers | Tier 2 per-host record (consented) + vehicle risk rating product |


## 4.3 Non-Citation Revenue Streams


| Revenue Stream | Source | Estimated Value | Timing |
|---|---|---|---|
| Commercial node installation fees | D-Central Shield or MSSP installs nodes on fleet vehicles at MSSP rates | $500–$2,500 per vehicle depending on node tier | One-time per vehicle, recurring on fleet renewal |
| L1 technician certification — rideshare track | Online-only L1 certification for Uber/Lyft driver self-installation ($149 per driver or waived by Uber as benefit) | $149 × opt-in driver count or lump sum arrangement with Uber | Per driver at onboarding |
| Fleet NOC management fee | D-Central Shield or MSSP manages node fleet health for fleet operators | $10–$30/node/month depending on fleet size and tier | Monthly recurring |
| Digital twin data premium tier | Delivery networks generate highest-value twin data — premium data subscription from insurers and municipalities | Additional 15–25% premium on standard Tier 1 subscription for delivery-node-sourced data | Annual subscription uplift |
| Uber/Lyft driver false claim defence package | Riders make false claims against drivers. Node footage is the defence. Per-incident evidence package retrieval fee. | $25–$75 per incident package retrieved | Per incident — supplementary revenue |


# 5. Go-to-Market Strategy
## 5.1 Sequencing
The CVN Programme should not be launched simultaneously with the OC Transpo pilot. The right sequencing:


| Phase | Timing | CVN Activity | Rationale |
|---|---|---|---|
| Phase 0 — Foundation | Months 1–12 (OC Transpo pilot) | No CVN launch. Develop agreement templates. Begin introductory conversations with one target fleet (Communauto or Enterprise preferred — simpler than rideshare for first CVN agreement). | Build the evidence base and legal framework before approaching commercial partners. OC Transpo pilot provides the proof of concept commercial partners need to see. |
| Phase 1 — First Fleet Agreement | Months 12–18 | Sign first Model A agreement (Communauto or Enterprise). 300–500 nodes added to Ottawa network. Document results. | Communauto is cooperative-adjacent — aligns with D-Central values. Small enough fleet to manage. Urban coverage fills gaps between bus routes and plow routes. |
| Phase 2 — Delivery Network | Months 18–24 | Sign first Model C agreement (Amazon Logistics or Purolator preferred — Canadian, privacy-sensitive). 200–400 nodes, highest twin contribution. | Proof of digital twin value proposition for Public Works and insurance. Strengthens ISC Phase 2 application. |
| Phase 3 — Rideshare Platform | Year 2–3 | Approach Uber Canada after delivery network proves API integration model. Start with driver-facing benefits programme — not enforcement-first. | Uber is the largest CVN opportunity but also the most complex legally and politically. Approach after track record is established. Frame as driver safety and income programme, not surveillance. |
| Phase 4 — Full CVN | Year 3+ | All model types active. National CVN partnerships as TrafficMesh expands to Hamilton, Mississauga, Toronto. | At national scale, one Enterprise agreement covers all cities simultaneously. |


## 5.2 The Uber Pitch — Getting the Framing Right


| Wrong framing (do not use) | 'Help us build a surveillance network and earn revenue from tickets.' This will be rejected at the legal team level before reaching a decision-maker. |
|---|---|
| Right framing | 'Help your drivers earn more, reduce their insurance costs, and protect themselves against false claims — using hardware they install themselves in under 5 minutes.' |
| Lead benefit | Ontario rideshare insurance cost. An Uber driver paying $3,000–$5,000/year in rideshare insurance who earns a 15% reduction through CivicMesh saves $450–$750/year. That is a meaningful income improvement for a gig worker. This is the lead benefit in every driver-facing communication. |
| Secondary benefit | Citation revenue share. On average, an active Ottawa rideshare driver covering bus corridors and downtown might attribute 3–8 validated citations per week to their node at peak. At $[X] per citation, this is meaningful supplementary income. |
| Uber's incentive | Driver retention and acquisition. If CivicMesh opt-in drivers earn meaningfully more and pay less for insurance, Uber benefits from lower driver churn. Uber's cost of driver acquisition in competitive markets is significant — anything that improves retention has direct financial value to the platform. |
| Privacy protection for Uber | CivicMesh is explicitly scoped against workforce surveillance. The API agreement prohibits use of CivicMesh data for driver performance monitoring or deactivation decisions. Uber's legal team needs to see this in writing — it removes their primary objection. |


# 6. Privacy Architecture for Commercial Participants


| Privacy Concern | Risk | Technical Mitigation | Contractual Mitigation |
|---|---|---|---|
| Driver identity in evidence packages | A driver's daily route is reconstructed from their node's evidence packages, creating a location surveillance record | Driver DID only in packages — no name, no employee ID, no plate. Route reconstruction requires access to driver DID mapping which is held by the platform, not CivicMesh. | Platform API agreement prohibits CivicMesh from requesting DID-to-identity mapping except where driver has explicitly consented for insurance purposes |
| Rideshare passenger identification | A passenger in an Uber vehicle is recorded by the node camera | On-device face blurring before any footage leaves the device. Passengers are never identifiable in evidence packages. Trip context field is boolean only — no route, no pickup/dropoff location. | Uber API agreement explicitly prohibits any passenger data flow. Breach is grounds for immediate agreement termination. |
| Rental car renter identification | Enterprise renter's movements tracked through vehicle node | Vehicle DID only — no renter identity. Evidence packages from rental vehicles carry no rental period or renter reference. | Fleet agreement prohibits renter identity linkage. Privacy impact assessment required for each fleet operator. |
| Delivery driver route surveillance | Amazon uses CivicMesh data to monitor driver performance | Route zone only — postal code area, not specific addresses. No stop-level data. | Delivery network agreement explicitly prohibits use of CivicMesh data for worker performance monitoring or disciplinary purposes. |
| Insurance adverse use | Insurance company uses CivicMesh data to deny coverage or raise premiums for protected groups | Prohibited use clause in LEGAL-CM-014. Equity audit tool flags correlations with protected characteristics. Annual audit of insurer data use. | Insurance agreement prohibits: denial of coverage based on geography alone, re-identification from aggregate data, any use outside underwriting/claims/fraud purposes. |


# 7. Cooperative Alignment and Worker Protection
The CVN Programme has an inherent tension: commercial fleets are operated by corporations whose interests are not always aligned with their workers (drivers, delivery workers). The cooperative architecture provides structural protections:


| Protection | Mechanism | Who Benefits |
|---|---|---|
| Driver-controlled opt-in | No commercial driver can be enrolled in CivicMesh by their employer without the driver's explicit consent through the Member App. Employer API agreements create the framework; individual drivers activate it. | Uber/Lyft drivers, taxi drivers, Turo hosts |
| Revenue direct to driver | Citation revenue share and insurance rebates flow directly to the driver's CCSC patronage wallet — not routed through the employer. Driver receives payment regardless of whether the employer participates. | All Model B and D drivers |
| No adverse use covenant | Commercial partners contractually prohibited from using CivicMesh data for driver discipline, deactivation, performance monitoring, or any workforce management purpose. Breach triggers agreement termination and credential revocation. | All commercial drivers |
| Cooperative membership option | Any commercial driver who opts in becomes a CCSC member with full governance rights. Collective voice of 5,000 Uber drivers who are all CCSC members is significant — they can raise platform governance issues through cooperative channels. | All opted-in commercial drivers |
| Taxi cooperative alignment | Taxi co-ops (Crown, Blue Line) are cooperative organizations. CivicMesh approaches them as cooperative-to-cooperative — they join the CCSC as organizational members, their drivers are individual members, and revenue flows through the cooperative distribution model. | Taxi co-op members and drivers |


# 8. CVN Programme Document Family


| Document ID | Title | Type | Status |
|---|---|---|---|
| DC-CM-B2B-001 | CivicMesh Commercial Vehicle Network Programme (this document) | Strategic Architecture | Active |
| DC-CM-B2B-002 | Fleet Operator Agreement Template — Model A (rental, car share) | Legal Template | To produce |
| DC-CM-B2B-003 | Platform Driver Programme Specification — Model B (Uber, Lyft) | Technical + Legal | To produce |
| DC-CM-B2B-004 | Delivery Network Agreement Template — Model C (UPS, FedEx, Amazon) | Legal Template | To produce |
| DC-CM-B2B-005 | Commercial Driver Verifiable Credential Schema | Technical | To produce |
| DC-CM-B2B-006 | Commercial Platform API Integration Specification | Technical | To produce |


# 9. Network Effect Projection with CVN Programme
The following illustrates the node count trajectory with and without the CVN Programme, demonstrating why this B2B tier is strategically critical to the network's value compound:


| Year | Without CVN (City Fleet + Civilian Only) | With CVN Programme | Delta — CVN Contribution |
|---|---|---|---|
| Year 1 End | ~200 nodes (OC Transpo pilot + 10 HOAs + early civilian opt-in) | ~700 nodes (+ Communauto fleet + first delivery agreement + early Uber opt-ins) | 3.5× network density — enforcement accuracy materially improves |
| Year 2 End | ~800 nodes (city fleet expansion + 30 HOAs + growing civilian) | ~4,000 nodes (+ Enterprise fleet + Amazon Logistics + Uber programme at scale) | 5× density — insurance data quality reaches actuarial-grade |
| Year 3 End | ~2,000 nodes (Ottawa network maturing) | ~12,000 nodes (full CVN + national MSSP expansion) | 6× density — city-wide coverage achieved, digital twin complete |


At 12,000 Ottawa nodes, the CivicMesh network covers every road in Ottawa at minimum once per day. The digital twin update cycle for every street drops below 24 hours. Insurance actuarial data reaches the sample size required for statistically significant postal code-level pricing. The enforcement accuracy reaches >95% confidence on all violation types. This is the network that investors are funding when they write a cheque — the CVN Programme is how it gets there in 3 years instead of 8.
# 10. Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

