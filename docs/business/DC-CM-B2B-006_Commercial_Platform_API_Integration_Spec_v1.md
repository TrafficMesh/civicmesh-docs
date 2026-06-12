# DC-CM-B2B-006 Commercial Platform API Integration Spec v1

**DC-CM-B2B-006**
**D-CENTRAL GROUP**
**CivicMesh Inc.**
Commercial Platform API Integration Specification


| Document ID | DC-CM-B2B-006 |
|---|---|
| Version | 1.0 |
| Status | Draft — Technical |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Technical |
| SR&ED Eligibility | Moderate — novel B2B API integration layer for commercial fleet context enrichment in enforcement evidence pipeline |
| Related Documents | DC-CM-B2B-001 | DC-CM-B2B-003 | DC-CM-B2B-004 | DC-CM-ARCH-001 | DC-CM-VC-001 |




| Executive Summary
This document specifies the technical architecture of the Commercial Platform Integration Layer — the API middleware that connects CivicMesh to commercial partner backends (Uber, Lyft, Enterprise, Amazon Logistics, etc.). The integration layer handles three functions: (1) context enrichment — attaching commercial context to evidence packages in real time; (2) credential lifecycle management — issuing, renewing, and revoking Commercial Driver VCs in response to platform events; (3) revenue routing — distributing citation revenue to the correct commercial partner and driver accounts. All integrations are strictly scoped — the API agreement defines what data flows in each direction, and anything outside that scope is technically impossible at the integration layer, not just prohibited by policy. |
|---|


# 1. Integration Layer Architecture


| Component | Function | Technology | Privacy Control |
|---|---|---|---|
| Platform Webhook Receiver | Receives real-time events from commercial platforms: trip start/end (Uber/Lyft), booking start/end (Communauto/Zipcar), vehicle checkout/return (Enterprise), route start/end (delivery networks) | FastAPI webhook endpoint with HMAC signature verification per platform | Events processed in memory only — trip/booking details not persisted. Only the derived boolean state (on_trip: true/false) is attached to evidence packages. |
| Context Enrichment Service | At evidence package ingestion, queries the context store for the generating node's current commercial context. Attaches permitted context fields to the package. | Redis cache for real-time context (max 60-second TTL), FastAPI service | Cache contains: node_did → {commercial_tier, platform, on_trip, vehicle_type, route_zone}. No PII in cache. |
| Credential Lifecycle Manager | Issues, renews, and revokes Commercial Driver VCs in response to platform status events. Manages trust registry updates on Polygon. | Veramo SDK for DID/VC operations, Polygon RPC for on-chain updates | Credential issuance only with explicit driver opt-in confirmation. Revocation is immediate on platform deactivation event. |
| Revenue Attribution Engine | Calculates citation revenue distribution for each commercial evidence package. Routes payments to correct commercial partner and driver accounts. | PostgreSQL revenue ledger, automated payment batch processor | Revenue routing uses patronage wallet DIDs only — no real-world identity in routing records. |
| Scope Enforcement Gateway | API gateway layer that enforces the permitted data scope for each commercial partner. Prevents any data flow outside the agreed scope. | GraphQL API gateway with per-partner scope configuration, automated audit logging of all cross-partner data requests | Any out-of-scope data request triggers an alert to the SOC Console (DC-CM-APP-004) and is logged permanently. |


# 2. Uber / Lyft Integration — Detailed Specification
## 2.1 Inbound — Trip Events from Uber


| Event | Payload Received | How Used | Data Retained? |
|---|---|---|---|
| trip.started | driver_did_hash, trip_id_hash, timestamp | Sets on_trip: true in Redis cache for all nodes associated with driver_did_hash. Cache TTL: 4 hours. | No — event processed and discarded. Only cache state retained, TTL 4 hours. |
| trip.ended | driver_did_hash, trip_id_hash, timestamp | Sets on_trip: false in Redis cache. Clears trip context. | No — event processed and discarded. |
| driver.deactivated | driver_did_hash | Triggers CDVC revocation for the associated driver DID. On-chain StatusList update within 60 seconds. | Revocation record on Polygon — permanent, but contains only credential ID, not identity. |
| driver.activated (new opt-in) | driver_did_hash, driver_platform_status | Triggers CDVC issuance process if driver has completed opt-in in Member App. | Credential issued and stored in driver's DID wallet — not on CivicMesh servers. |


## 2.2 Outbound — Data from CivicMesh to Uber


| Data Sent | Frequency | Format | Recipient at Uber | Privacy |
|---|---|---|---|---|
| Driver citation revenue batch | Weekly | CSV: driver_did_hash, citation_count, revenue_amount (CAD) | Uber Finance / Driver Payments team | No individual citation detail — aggregated by driver |
| Aggregate driver safety score (consented only) | Weekly | JSON: driver_did_hash[], safety_score_percentile, clean_period_days | Intact Insurance (Uber's insurer) via Uber API — NOT to Uber directly | Only consented drivers included. Intact receives summary score, not individual events. |
| Fleet coverage certification | Monthly | PDF report: active node count in Uber Ottawa fleet, average uptime, evidence pipeline reliability | Uber insurance team for fleet rider insurance negotiations | Aggregate metrics only — no individual driver data |


# 3. Enterprise / Fleet Operator Integration


| Integration Point | Direction | Data | Frequency |
|---|---|---|---|
| Fleet vehicle roster | Inbound from Enterprise | VIN → node DID mapping. Additions and retirements. | Monthly batch + real-time webhook on vehicle addition |
| Vehicle checkout event | Inbound from Enterprise | vehicle_did, checkout_timestamp, return_expected_timestamp (no renter identity) | Real-time webhook — sets vehicle as active |
| Vehicle return event | Inbound from Enterprise | vehicle_did, return_timestamp | Real-time webhook — sets vehicle as inactive. Evidence pipeline paused during inactive periods. |
| Fleet coverage report | Outbound to Enterprise | Active node count, uptime %, evidence volume, citation revenue attribution (aggregate) | Monthly automated report |
| Insurance certification | Outbound to Enterprise (for their insurer) | Coverage density %, node reliability score, evidence pipeline integrity metrics | Annual at policy renewal |


# 4. Delivery Network Integration


| Integration Point | Direction | Data | Notes |
|---|---|---|---|
| Route assignment | Inbound from delivery operator | vehicle_did → postal_code_zone_array (route zone assignment for the day) | Sets route_zone context for evidence package enrichment. No specific address data. |
| Delivery completion event | Inbound from delivery operator | vehicle_did, zone_id, completion_timestamp | Used to update digital twin coverage tracking — confirms this zone has been observed today. |
| Digital twin contribution report | Outbound to delivery operator | Observations by postal code zone: count, condition updates triggered, new defects detected | Weekly — allows delivery operator to see their network contribution |
| Liability documentation request | Inbound from delivery operator | vehicle_did, timestamp_range, location (GPS bounding box), incident_reference | Per-incident. Returns evidence package if matching footage exists. Charges per-package retrieval fee. |


# 5. Technical Uncertainty Register (SR&ED)
- Real-time trip context enrichment latency: the evidence package must be enriched with trip context (on_trip boolean) within the frame capture window. If the trip start/end webhook from Uber arrives after the evidence package is already in the ingestion pipeline, the context is inconsistent. Investigating: pre-enrichment caching strategy vs. post-ingestion enrichment with package amendment.
- Multi-platform driver node attribution: a driver who is registered on both Uber and Lyft and has nodes in two vehicles — how to correctly attribute citation revenue to the correct platform's revenue routing when both CDVCs are valid simultaneously. Current approach: evidence package carries node DID which is linked to specific vehicle → revenue routed to the vehicle's registered commercial driver credential's platform.
- Credential revocation propagation latency: when Uber sends a driver.deactivated event, the CDVC must be revoked on Polygon within 60 seconds. Polygon PoS block time is approximately 2 seconds — the technical challenge is ensuring the StatusList update transaction is confirmed within the target latency under network congestion.
# 6. Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

