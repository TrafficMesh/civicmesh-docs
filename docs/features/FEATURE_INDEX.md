# CivicMesh Feature Testing Index

**Master index of all features requiring test documentation and implementation.**

---

## Testing Status Overview

| # | Feature Module | Status | Priority | MVP | Phase 1 | Phase 2+ | Notes |
|---|---|---|---|---|---|---|---|
| 1 | Violation Detection | 🔄 IN PROGRESS | CRITICAL | ✓ | ✓ | ✓ | 10+ violation types |
| 2 | Enforcement Workflow | 📋 PLANNED | CRITICAL | ✓ | ✓ | ✓ | Officer review & citation |
| 3 | Digital Twin | 📋 PLANNED | HIGH | ✗ | ✓ | ✓ | Continuous road model |
| 4 | Data Pipeline | 🔄 IN PROGRESS | CRITICAL | ✓ | ✓ | ✓ | Edge → Cloud flow |
| 5 | Officer Portal | 📋 PLANNED | HIGH | ✓ | ✓ | ✓ | Web interface |
| 6 | Hardware Tiers | 📋 PLANNED | HIGH | ✓ | ✓ | ✓ | 4 deployment types |
| 7 | Business Model | 📋 PLANNED | MEDIUM | ✗ | ✓ | ✓ | Revenue calculations |
| 8 | Insurance Integration | 📋 PLANNED | MEDIUM | ✗ | ✗ | ✓ | 3rd-party data APIs |
| 9 | Blockchain/DID | 📋 PLANNED | LOW | ✗ | ✗ | ✓ | Optional Phase 3 |
| 10 | Federation/Multi-City | 📋 PLANNED | MEDIUM | ✗ | ✗ | ✓ | Cross-city deployment |
| 11 | Training & Cert | 📋 PLANNED | MEDIUM | ✗ | ✓ | ✓ | Technician programs |
| 12 | Governance & Transparency | 🔄 IN PROGRESS | HIGH | ✗ | ✓ | ✓ | 5-layer transparency + governance |
| 13 | APIs & Integration | 📋 PLANNED | HIGH | ✓ | ✓ | ✓ | REST/GraphQL endpoints |
| 14 | Operations & Management | 📋 PLANNED | HIGH | ✗ | ✓ | ✓ | 24/7 NOC tools |

---

## Feature Test Documents

### 1. Violation Detection (10 violation types)

**Status:** 🔄 IN PROGRESS

**Overview:** [violation-detection/00_OVERVIEW.md](violation-detection/00_OVERVIEW.md)

**Subfeatures:**
- [x] [01_SPEEDING.md](violation-detection/01_SPEEDING.md) — ✓ COMPLETE WITH TEST CODE
- [ ] [02_LANE_VIOLATIONS.md](violation-detection/02_LANE_VIOLATIONS.md) — Phase 1
- [ ] [03_ALPR.md](violation-detection/03_ALPR.md) — MVP critical
- [ ] [04_BUS_LANE.md](violation-detection/04_BUS_LANE.md) — Phase 1
- [ ] [05_STOP_SIGN.md](violation-detection/05_STOP_SIGN.md) — Phase 1
- [ ] [06_ACCESSIBLE_PARKING.md](violation-detection/06_ACCESSIBLE_PARKING.md) — Phase 2
- [ ] [07_SCHOOL_ZONE.md](violation-detection/07_SCHOOL_ZONE.md) — Phase 2
- [ ] [08_EXPIRED_PLATES.md](violation-detection/08_EXPIRED_PLATES.md) — Phase 2
- [ ] [09_CONSTRUCTION_ZONE.md](violation-detection/09_CONSTRUCTION_ZONE.md) — Phase 3
- [ ] [10_SNOW_ROUTE.md](violation-detection/10_SNOW_ROUTE.md) — Phase 2

**Test Suite Scope:**
- Unit tests: Speed logic, confidence scoring, geofence lookup
- Integration tests: Full violation flow (video → detection → incident)
- Performance tests: <200ms inference latency, 30 fps throughput
- Accuracy tests: <5% false positive rate
- Field tests: Real buses, real violations

**Estimated Hours:** ~4 hours per violation type (40 total)

---

### 2. Enforcement Workflow

**Status:** 📋 PLANNED

**Overview:** enforcement/00_OVERVIEW.md (TO CREATE)

**Subfeatures:**
- [ ] 01_REVIEW_QUEUE.md — Queue management & routing
- [ ] 02_OFFICER_APPROVAL.md — Approval workflow & signature
- [ ] 03_CITATION_GENERATION.md — NOC generation
- [ ] 04_MTO_INTEGRATION.md — Provincial system integration

**Test Scope:**
- Unit: Queue logic, confidence routing, metadata validation
- Integration: Incident → queue → approval → citation
- E2E: Real officer review on portal
- Acceptance: Legal defensibility checklist

**Estimated Hours:** ~20 hours

---

### 3. Digital Twin

**Status:** 📋 PLANNED

**Overview:** digital-twin/00_OVERVIEW.md (TO CREATE)

**Subfeatures:**
- [ ] 01_ROAD_CONDITION.md — Pothole/crack detection
- [ ] 02_SIGN_INVENTORY.md — Sign detection & legibility
- [ ] 03_SIGNALS.md — Traffic signal status
- [ ] 04_LANE_MARKINGS.md — Lane marking visibility
- [ ] 05_CONSTRUCTION_ZONES.md — Active construction
- [ ] 06_ACCESSIBILITY.md — Curb cuts, accessible parking
- [ ] 07_PEDESTRIAN_CYCLISTS.md — Anonymized counting

**Test Scope:**
- Unit: Asset detection models, confidence updates
- Integration: Detection → twin fusion → work order generation
- Performance: <100ms fusion per incident
- Acceptance: Road condition heatmap accuracy

**Estimated Hours:** ~30 hours

---

### 4. Data Pipeline

**Status:** 🔄 IN PROGRESS

**Overview:** [data-pipeline/00_OVERVIEW.md](data-pipeline/00_OVERVIEW.md)

**Subfeatures:**
- [ ] [01_EDGE_CAPTURE.md](data-pipeline/01_EDGE_CAPTURE.md) — Frame buffering
- [ ] [02_ENCRYPTION.md](data-pipeline/02_ENCRYPTION.md) — AES-256 & HMAC
- [ ] [03_UPLOAD.md](data-pipeline/03_UPLOAD.md) — LTE & retry logic
- [ ] [04_INGESTION.md](data-pipeline/04_INGESTION.md) — Cloud API
- [ ] [05_DEDUPLICATION.md](data-pipeline/05_DEDUPLICATION.md) — Incident merging

**Test Scope:**
- Unit: Compression ratio, encryption/decryption, retry backoff
- Integration: Full incident flow (edge → cloud)
- Network: Dropout recovery, offline buffering
- Performance: <5 sec ingestion latency, 99%+ upload success
- Security: Signature verification, no data loss

**Estimated Hours:** ~25 hours

---

### 5. Officer Portal

**Status:** 📋 PLANNED

**Overview:** officer-portal/00_OVERVIEW.md (TO CREATE)

**Subfeatures:**
- [ ] 01_QUEUE_MANAGEMENT.md — Incident queue view
- [ ] 02_VIDEO_PLAYBACK.md — Evidence clip player
- [ ] 03_APPROVAL_WORKFLOW.md — Approve/reject/escalate
- [ ] 04_AUDIT_LOGGING.md — Chain of custody

**Test Scope:**
- Unit: Permission checks, queue filtering
- Integration: Login → queue → approval → audit log
- UI/UX: Video playback, response times
- Acceptance: Officer usability (< 2 min review time)

**Estimated Hours:** ~20 hours

---

### 6. Hardware Tiers

**Status:** 📋 PLANNED

**Overview:** hardware/00_OVERVIEW.md (TO CREATE)

**Subfeatures:**
- [ ] 01_TIER1_PERMANENT.md — OC Transpo buses
- [ ] 02_TIER2_SEASONAL.md — Snow plows, sweepers
- [ ] 03_TIER3_CONTRACTOR.md — Contractor modules (plug-and-play)
- [ ] 04_TIER4_CIVILIAN.md — Consumer kits

**Test Scope:**
- Unit: Power management, enrollment logic
- Integration: Hardware → network → cloud
- Field: Real bus deployment, uptime
- Acceptance: 99%+ reliability per tier

**Estimated Hours:** ~15 hours

---

### 7. Business Model & Revenue

**Status:** 📋 PLANNED

**Overview:** business/00_OVERVIEW.md (TO CREATE)

**Subfeatures:**
- [ ] 01_REVENUE_CALCULATION.md — Citation fine splits
- [ ] 02_REVENUE_SHARING.md — Phase 3 participant payouts
- [ ] 03_MSSP_BILLING.md — Per-node subscriptions

**Test Scope:**
- Unit: Revenue calculation formulas
- Integration: Citation → payout calculation → ledger
- Acceptance: Math accuracy, no revenue leakage

**Estimated Hours:** ~10 hours

---

### 8. Insurance Integration

**Status:** 📋 PLANNED

**Overview:** insurance/00_OVERVIEW.md (TO CREATE)

**Subfeatures:**
- [ ] 01_DRIVING_RECORDS.md — Individual records API
- [ ] 02_RISK_SCORING.md — Corridor risk heatmaps
- [ ] 03_CLAIMS_FAST_TRACK.md — Claim evidence retrieval

**Test Scope:**
- Unit: Consent verification, data filtering
- Integration: Insurance query → consent check → record return
- Acceptance: PIPEDA compliance, no privacy leaks

**Estimated Hours:** ~15 hours

---

### 9. Blockchain & DID

**Status:** 📋 PLANNED

**Overview:** blockchain/00_OVERVIEW.md (TO CREATE)

**Subfeatures:**
- [ ] 01_DID.md — Decentralized identities
- [ ] 02_VERIFIABLE_CREDENTIALS.md — ZK proofs
- [ ] 03_ON_CHAIN_ANCHORING.md — Blockchain hashing

**Test Scope:**
- Unit: DID generation, VC signing
- Integration: Claim → VC → blockchain anchor
- Acceptance: Tamper-proof audit trail

**Estimated Hours:** ~20 hours (Phase 3)

---

### 10. Federation & Multi-City

**Status:** 📋 PLANNED

**Overview:** federation/00_OVERVIEW.md (TO CREATE)

**Subfeatures:**
- [ ] 01_MULTI_CITY.md — Kubernetes multi-tenancy
- [ ] 02_DATA_SHARING.md — Cross-city incident data

**Test Scope:**
- Unit: Tenant isolation, data filtering
- Integration: City A query → City B data (anonymized)
- Acceptance: No cross-tenant data leakage

**Estimated Hours:** ~15 hours

---

### 11. Training & Certification

**Status:** 📋 PLANNED

**Overview:** training/00_OVERVIEW.md (TO CREATE)

**Subfeatures:**
- [ ] 01_TECHNICIAN_CERT.md — Hardware certification
- [ ] 02_MSSP_CERT.md — Operations certification
- [ ] 03_COMMUNITY_MANAGER_CERT.md — Community programs

**Test Scope:**
- Unit: Exam scoring, certification logic
- Integration: Training → exam → certification → credential
- Acceptance: Exam passing rate >70% on sample questions

**Estimated Hours:** ~10 hours

---

### 12. Governance & Transparency

**Status:** 🔄 IN PROGRESS

**Overview:** [governance/00_OVERVIEW.md](governance/00_OVERVIEW.md) — Structural transparency layer (see [docs/TRANSPARENCY_LAYER.md](../docs/TRANSPARENCY_LAYER.md))

**Subfeatures:**
- [x] [01_TRANSPARENCY_REPORTS.md](governance/01_TRANSPARENCY_REPORTS.md) — ✓ COMPLETE (auto-generated monthly & annual reports)
- [x] [02_AUDIT_FRAMEWORK.md](governance/02_AUDIT_FRAMEWORK.md) — ✓ COMPLETE (personal, extraordinary, annual audits)
- [x] [03_OPEN_GOVERNANCE.md](governance/03_OPEN_GOVERNANCE.md) — ✓ COMPLETE (RFC process, TSC voting, public meetings)

**Supporting Documentation (Phase 1):**
- [x] [docs/TRANSPARENCY_LAYER.md](../docs/TRANSPARENCY_LAYER.md) — 5-layer architecture (audit logs, open source, dashboards, reports, governance)
- [x] [docs/AI_MODEL_GOVERNANCE.md](../docs/AI_MODEL_GOVERNANCE.md) — Model cards, retraining governance, community appeals
- [x] [docs/BLOCKCHAIN_FINANCIAL_TRANSPARENCY.md](../docs/BLOCKCHAIN_FINANCIAL_TRANSPARENCY.md) — On-chain ledger, node earnings, CCSC patronage
- [x] [docs/COMMUNITY_EVIDENCE_VIEWING.md](../docs/COMMUNITY_EVIDENCE_VIEWING.md) — PII redaction, public evidence viewer, future livestreams
- [x] [docs/OPEN_SOURCE_VERIFICATION_GUIDE.md](../docs/OPEN_SOURCE_VERIFICATION_GUIDE.md) — Verify 8 core privacy promises in source code

**Test Scope:**
- Unit: Report generation, data aggregation, validation, hash chain integrity
- Integration: Incidents → monthly report → public API
- Acceptance: Accuracy of statistics, no PII in public reports, blockchain verification
- Audit framework: Personal audit endpoint, petition threshold, auditor access control
- Governance: RFC merge blocking, TSC voting, meeting notes publication

**Estimated Hours:** ~10 hours (implementation), ~30 hours (testing)

**Phase 2 Features (Future):**
- [ ] Community livestream API (real-time redaction)
- [ ] Zero-knowledge proofs (prove validity without PII)
- [ ] Automated audit bots (blockchain vs. report reconciliation)

---

### 13. APIs & Integrations

**Status:** 📋 PLANNED

**Overview:** api/00_OVERVIEW.md (TO CREATE)

**Subfeatures:**
- [ ] 01_INGESTION_API.md — Device uploads
- [ ] 02_OFFICER_API.md — Portal endpoints
- [ ] 03_INSURANCE_API.md — Partner data feeds
- [ ] 04_PUBLIC_API.md — Public transparency data

**Test Scope:**
- Unit: Request validation, response format
- Integration: Full API workflows
- Performance: <500ms response time
- Security: Auth, rate limiting, no data leakage
- Acceptance: API contract compliance

**Estimated Hours:** ~20 hours

---

### 14. Operations & Management

**Status:** 📋 PLANNED

**Overview:** operations/00_OVERVIEW.md (TO CREATE)

**Subfeatures:**
- [ ] 01_FLEET_HEALTH.md — Device monitoring
- [ ] 02_INCIDENT_MANAGEMENT.md — SLA tracking
- [ ] 03_COMPLIANCE.md — Audit logging
- [ ] 04_MONITORING.md — Prometheus/Grafana

**Test Scope:**
- Unit: Health check logic, alert thresholds
- Integration: Device down → alert → escalation
- Performance: <1 sec alert notification
- Acceptance: 99%+ monitoring accuracy

**Estimated Hours:** ~15 hours

---

### 15. Field Operations & Technician Guild

**Status:** 🔄 IN PROGRESS

**Overview:** [docs/FIELD_OPERATIONS.md](../docs/FIELD_OPERATIONS.md) — Comprehensive field operations layer (technician credentials, hardware tamper-proofing, installation procedures, maintenance)

**Key Components:**
- [x] Part 1: Decentralized Technician Guild (W3C DIDs, on-chain credentials, reputation)
- [x] Part 2: Expert Witness Network (jurisdictional, for contested citations)
- [x] Part 3: Tamper-Proof Hardware Architecture (5 layers: physical, TPM, secure boot, intrusion, heartbeat)
- [x] Part 4: Installation Procedure (pre-install, physical, enrollment, calibration, attestation)
- [x] Part 5: Maintenance Schedule (preventive + remote monitoring)
- [x] Part 6: Future — Ride-Sharing Integration (software + Phase 2 hardware add-on)
- [x] Part 7: Future — Additional Security & Sensor Capabilities (air quality, noise, flood, etc.)
- [x] Part 8: Hardware BOM Updates (TPM, intrusion switch, tamper seals, expansion port)

**Related Documentation:**
- [docs/architecture/01_HARDWARE_STACK.md](../docs/architecture/01_HARDWARE_STACK.md) — Updated with security components + expansion connector
- [docs/FIELD_OPERATIONS.md](../docs/FIELD_OPERATIONS.md) — Complete field operations guide (8 parts, ~1200 lines)

**Future Implementation Specs (Phase 2):**
- [ ] features/operations/01_TECHNICIAN_ENROLLMENT.md — Onboarding flow, credential verification
- [ ] features/operations/02_INSTALLATION_WORKFLOW.md — Step-by-step app-guided installation
- [ ] features/operations/03_MAINTENANCE_DISPATCH.md — NOC-triggered maintenance scheduling
- [ ] features/operations/04_RIDE_SHARING_INTEGRATION.md — Driver verification, trip recording, fare contracts

**Test Scope:**
- Unit: Credential verification API, TPM attestation, hash chain validation
- Integration: Full installation flow (pre-install → attestation → burn-in)
- Acceptance: Technician can install without training (app-guided), node passes burn-in

**Estimated Hours:** ~30 hours (documentation complete, implementation pending Phase 2)

---

## Timeline

### Week 1: Foundation
- [ ] Complete TEST_GUIDE.md ✓
- [ ] Create feature overview documents (all 14)
- [ ] Set up test data directory structure
- [ ] Implement mock simulators (camera, OBD, GPS)

**Estimated:** 20 hours

### Week 2-3: MVP Features
- [ ] Complete speeding detection test suite ✓
- [ ] Complete ALPR test suite
- [ ] Complete data pipeline test suite
- [ ] Complete enforcement workflow test suite

**Estimated:** 60 hours

### Week 4: Integration & Field
- [ ] Integration tests across 4 MVP features
- [ ] Performance testing & optimization
- [ ] Field trial on 5 OC Transpo buses
- [ ] Debug & refinement

**Estimated:** 40 hours

### Weeks 5-8: Phase 1 Features
- [ ] Lane violation detection suite
- [ ] Bus lane obstruction suite
- [ ] Stop sign running suite
- [ ] Officer portal suite
- [ ] Digital twin foundation

**Estimated:** 80 hours

### Weeks 9-12: Phase 2+ Features
- [ ] Remaining violation types
- [ ] Insurance integration
- [ ] Multi-city federation
- [ ] Training & governance

**Estimated:** 100 hours

**Total Estimated:** ~300 hours (~8 weeks full-time)

---

## How to Use This Index

1. **Start with MVP priorities:**
   - [x] Violation Detection (Speeding)
   - [x] Data Pipeline
   - [ ] Enforcement Workflow
   - [ ] Officer Portal

2. **For each feature, follow the pattern:**
   - Read 00_OVERVIEW.md
   - Study test requirements
   - Implement test suite (unit → integration → E2E)
   - Run tests, measure coverage
   - Move to next feature

3. **Track progress:**
   - Update status (📋 → 🔄 → ✓)
   - Record test coverage %
   - Note known issues
   - Update timeline

---

## Legend

| Status | Meaning |
|--------|---------|
| 📋 | Planned (not started) |
| 🔄 | In progress |
| ✓ | Complete with test suite |
| 🚀 | Tested in field |

| Priority | Phase |
|----------|-------|
| CRITICAL | MVP essential |
| HIGH | MVP important |
| MEDIUM | Phase 1-2 |
| LOW | Phase 3+ |

---

## Next Actions

**This Week:**
1. [ ] Read TEST_GUIDE.md
2. [ ] Review speeding detection test suite (01_SPEEDING.md)
3. [ ] Set up test data directory (videos, scenarios)
4. [ ] Implement mock simulators (camera, OBD, GPS)

**Next Week:**
1. [ ] Create ALPR test suite
2. [ ] Create data pipeline subfeature docs (01-05)
3. [ ] Begin writing unit test code
4. [ ] Start gathering test video data

---

**Questions?**
- See TEST_GUIDE.md for testing methodology
- See individual feature docs for detailed requirements
- See test code examples in 01_SPEEDING.md

**Let's build it!**
