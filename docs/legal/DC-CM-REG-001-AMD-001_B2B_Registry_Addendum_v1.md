# DC-CM-REG-001-AMD-001 B2B Registry Addendum v1

**DC-CM-REG-001 — AMENDMENT 1**
**D-CENTRAL GROUP  |  CivicMesh Platform**
**B2B Commercial Vehicle Network Programme**
Document Registry Addendum — 6 New Documents


| Amendment ID | DC-CM-REG-001-AMD-001 |
|---|---|
| Amends | DC-CM-REG-001 CivicMesh Document Registry v1.0 |
| Also Amends | DC-MASTER-DOC-001 Master Documentation Registry v1.0 |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Documents Added | 6 new B2B documents — DC-CM-B2B-001 through DC-CM-B2B-006 |
| Registry Section | New Section 4G added to Technical Platform Documentation tier |
| Rationale | Commercial Vehicle Network Programme identified as missing B2B tier. Rideshare (Uber/Lyft), car rental (Enterprise, Hertz), car share (Communauto, Zipcar), delivery (Amazon, UPS, FedEx, Purolator), and taxi operators form Tier 4 between city fleet (Tier 1–3) and civilian opt-in (Tier 5). One Uber API agreement = 3,000–5,000 Ottawa nodes. Critical for network density acceleration. |
| Updated Document Count | DC-CM-REG-001: 76 → 82 documents. DC-MASTER-DOC-001: 177 → 183 documents. |


# New Documents — Section 4G: Commercial Vehicle Network Programme
These 6 documents add a new subsection to Section 4 (Technical Platform Documentation) of DC-CM-REG-001 and DC-MASTER-DOC-001. They should be inserted after Section 4F (Supply Chain and Governance) in both registries.


| Doc ID | Title & Description | Type | Primary Audience | Priority | Status | Notes |
|---|---|---|---|---|---|---|
| SECTION 4G — Commercial Vehicle Network Programme (NEW — B2B Tier 4) | SECTION 4G — Commercial Vehicle Network Programme (NEW — B2B Tier 4) | SECTION 4G — Commercial Vehicle Network Programme (NEW — B2B Tier 4) | SECTION 4G — Commercial Vehicle Network Programme (NEW — B2B Tier 4) | SECTION 4G — Commercial Vehicle Network Programme (NEW — B2B Tier 4) | SECTION 4G — Commercial Vehicle Network Programme (NEW — B2B Tier 4) | SECTION 4G — Commercial Vehicle Network Programme (NEW — B2B Tier 4) |
| DC-CM-B2B-001 | CivicMesh Commercial Vehicle Network Programme — Master document. B2B Tier 4 sitting between city fleet (Tier 1–3) and civilian (Tier 5). Four agreement models: Fleet Owner (Enterprise, Communauto), Platform Driver (Uber/Lyft), Delivery Network (Amazon, UPS, FedEx), Taxi/Licensed. Revenue model, privacy architecture, cooperative alignment, network effect projection. | Strategy | All Audiences | P2 | 🆕 | New — created this session |
| DC-CM-B2B-002 | Fleet Operator Agreement Template — Model A. Enterprise, Hertz, Communauto, Zipcar, Turo. Company-level agreement covering node installation, 25% revenue share, insurance premium reduction facilitation, renter privacy (no renter identity ever collected), no adverse use covenant. | Legal | Fleet Operators | P2 | 🆕 | New |
| DC-CM-B2B-003 | Platform Driver Programme Specification — Model B. Uber Canada, Lyft Canada. Two-level structure: Platform API Agreement (trip status, driver DID mapping, revenue routing, insurance data feed) + individual Driver Opt-In Programme (voluntary, benefit-first framing). Anti-surveillance protections, driver rights cannot be waived by platform. Uber pitch strategy. | Technical / Legal | Uber / Lyft / Drivers | P3 | 🆕 | New — Year 2–3 go-to-market |
| DC-CM-B2B-004 | Delivery Network Agreement Template — Model C. Amazon Logistics, UPS, FedEx, Purolator. Route-zone data only (not address-level), digital twin premium data tier, last-mile liability documentation tool, higher city share (55%) reflecting digital twin value, no worker surveillance covenant binding on DSPs. | Legal | Delivery Operators | P3 | 🆕 | New |
| DC-CM-B2B-005 | Commercial Driver Verifiable Credential Schema — JSON-LD schema for platform-issued driver credentials. Platform DID (Uber Canada) issues to driver personal DID. Fields: platform, driverStatus, vehicleDIDs, revenueShareRecipient, insuranceDataShareConsent, tripContextConsent, citationRevenueOptIn, platformDriverId (hashed). Credential lifecycle: revocation on platform deactivation, multi-platform driver support. | Technical | Engineering / Platforms | P2 | 🆕 | SR&ED: novel VC schema |
| DC-CM-B2B-006 | Commercial Platform API Integration Specification — Integration layer architecture. Uber/Lyft: trip webhook receiver, context enrichment service, credential lifecycle manager, revenue attribution engine, scope enforcement gateway. Enterprise/fleet: vehicle roster sync, checkout events. Delivery: route assignment, zone completion events, liability documentation requests. SR&ED: trip context enrichment latency, multi-platform attribution, Polygon revocation latency. | Technical | Engineering | P2 | 🆕 | SR&ED: novel integration |


# Updated Registry Totals


| Registry | Before | After | Delta |
|---|---|---|---|
| DC-CM-REG-001 — CivicMesh Document Registry | 76 | 82 | +6 documents (Section 4G B2B) |
| DC-MASTER-DOC-001 — Master Documentation Registry | 177 | 183 | +6 documents (B2B Programme section) |
| Total across all registries | 253 | 259 | +6 documents |


# Documents Produced This Session
The following 6 documents have been produced and are available for download:


| Document ID | Filename |
|---|---|
| DC-CM-B2B-001 | DC-CM-B2B-001_Commercial_Vehicle_Network_Programme_v1.docx |
| DC-CM-B2B-002 | DC-CM-B2B-002_Fleet_Operator_Agreement_Template_v1.docx |
| DC-CM-B2B-003 | DC-CM-B2B-003_Platform_Driver_Programme_Specification_v1.docx |
| DC-CM-B2B-004 | DC-CM-B2B-004_Delivery_Network_Agreement_Template_v1.docx |
| DC-CM-B2B-005 | DC-CM-B2B-005_Commercial_Driver_VC_Schema_v1.docx |
| DC-CM-B2B-006 | DC-CM-B2B-006_Commercial_Platform_API_Integration_Spec_v1.docx |


DC-CM-REG-001-AMD-001  |  May 2026  |  Toussaint Redji Jean Baptiste  |  D-Central Ecosystem  |  Confidential