# CivicMesh — Project Summary & Registry

## Executive Overview

**CivicMesh** is a distributed traffic enforcement and city intelligence platform combining:
- Crowdsourced mobile sensor network (fleet + civilian opt-in)
- Real-time violation detection and evidence collection
- Digital twin infrastructure mapping
- Municipal revenue sharing and insurance data integration
- Three-tier business model: open source core + MSSP + managed IT

---

## Core Platform Architecture

### 1. Hardware Stack (Per-Vehicle Node)

| Component | Spec | Purpose |
|-----------|------|---------|
| **OBD-II Dongle** | Standard automotive interface | Vehicle telemetry (speed, GPS, heading, timestamp) |
| **Forward-Facing Dashcam** | 1080p/60fps, wide FOV, low-light capable | Road surface and vehicle capture |
| **Edge Compute Module** | Raspberry Pi CM4 / Radxa equivalent | On-device AI inference (ALPR, speed, lanes, following distance) |
| **Cellular Modem** | LTE/5G SIM card | Encrypted evidence upload |
| **GPS Module** | ±5m accuracy | Incident geolocation |
| **Power Management** | OBD + battery backup | 8-hour minimum backup on battery-only vehicles |

### 2. Node Tiers

| Tier | Vehicles | Deployment | Coverage |
|------|----------|------------|----------|
| **T1: Permanent City Fleet** | OC Transpo buses, bylaw, police, Public Works | Year-round hardwired | Arterials + dense city routes |
| **T2: Seasonal City Fleet** | Snow plows, salt spreaders, street sweepers, line painters | November–March (winter) or summer-only | Full residential coverage on rotation |
| **T3: Contractor Module** | Contracted road crews, construction, event crews | Temporary deployment per contract | Zone-specific, event-based |
| **T4: Civilian Opt-In** | Consumer vehicles (Phase 3+) | Voluntary participation | Gaps in Tier 1–3 coverage |

---

## Violation Detection Models (AI/CV)

| Violation Type | Detection Method | Confidence Threshold | Tier Deployment |
|---|---|---|---|
| **Bus Lane Obstruction** | CV + location | High (95%+) | T1 (OC Transpo) |
| **Stop Sign Running** | CV (traffic signal state) | High (95%+) | T1, T2, T3 |
| **Lane Violations** | Lane marking segmentation + OBD heading | Medium (85%+) | T1, T2, T3, T4 |
| **Speeding** | GPS/OBD delta + ALPR reference | High (95%+) | All tiers |
| **Following Distance** | Optical flow + radar distance | Medium (80%+) | T1, T4 |
| **Accessible Parking Abuse** | ALPR + provincial permit DB | High (98%+) | T1, T2, T3 |
| **School Zone Violations** | Time + location + speed threshold | High (95%+) | T1, T2, T3 |
| **Expired/Suspended Plates** | ALPR + MTO registration DB | High (99%+) | All → Police queue |
| **Construction Zone Violations** | Geofence activation + speed | Medium (85%+) | T3 (contractors) |
| **Snow Route Parking** | Geofence + time-of-day rules | High (97%+) | T2 (plows) |

---

## Digital Twin Layer

**What It Is:** Continuously updated, georeferenced model of Ottawa's road environment (not a static map).

### Twin Data Sources (Per Node Pass)
- Road surface condition (cracks, heaving, potholes)
- Sign inventory and condition (legibility, damage)
- Signal and pedestrian signal status
- Lane marking visibility (especially post-winter/storm)
- Construction zone boundaries and equipment
- Parked vehicle density by block
- Pedestrian and cyclist activity levels
- Vegetation encroachment on sightlines
- Curb cut and accessibility infrastructure

### Twin Update Frequency Guarantees
- **Arterial streets:** Every OC Transpo pass (multiple times daily)
- **Residential streets:** Minimum once per plow cycle (winter) or sweep cycle (summer)
- **Construction zones:** Real-time during active deployment
- **Signal changes:** Within 24 hours of change detection

### Department-Specific Products
- **Public Works:** Live asset registry with condition ratings and maintenance predictions
- **Emergency Management:** Real-time road clearing status and signal outages post-disaster
- **Urban Planning:** 3-year continuous performance data (cyclist counts, dwell times, queue lengths)
- **OC Transpo:** Transit performance analytics and corridor optimization
- **Insurance API:** Road condition context for underwriting and claims

---

## Data Flow Architecture

```
Node Vehicle Captures Frame + Metadata
  ↓
Edge AI Detection (ALPR, speed, lanes, following distance)
  ↓
Compression + Encryption
  ↓
Cellular Upload (LTE/5G)
  ↓
Ingestion API (FastAPI/cloud)
  ├─→ Evidence Queue (low confidence → manual review)
  ├─→ High-Confidence Fast-Track Queue (95%+ confidence)
  ├─→ Digital Twin Fusion Engine
  └─→ Police Queue (stolen vehicles, expired plates)
      ↓
Officer Review Portal (human sign-off before citation)
      ↓
Citation Issuance → DMV/Payment Gateway → Revenue Distribution
```

---

## Enforcement Workflow

1. **Detection:** On-device AI flags violation with confidence score + evidence clip
2. **Upload:** Compressed clip + metadata + GPS sent to cloud
3. **Deduplication:** System checks for duplicate reports from multiple nodes (same plate/time/location)
4. **Review Queue:** 
   - **Fast-track:** High confidence (95%+) → minimal review, auto-issue citation
   - **Manual Review:** Medium confidence (80–95%) → officer review required
   - **Rejection:** Low confidence (<80%) → discarded
5. **Officer Sign-Off:** Human review of clip + evidence before citation issuance (legal defensibility)
6. **Citation Issuance:** Notice generated, sent to registered plate owner via DMV
7. **Payment Processing:** City treasury + stakeholder revenue distribution

---

## Revenue Model & Distribution

### Phase 1 — OC Transpo (Year 1)
- **City takes:** 100% of bus lane violation revenue
- **Purpose:** Proof of concept, data collection, political capital

### Phase 2 — City Fleet (Year 1–2)
- **City takes:** 85–90% of revenue
- **OC Transpo/Bylaw:** Share from violations captured by their vehicles
- **Purpose:** Scale infrastructure, multi-department integration

### Phase 3 — Civilian Opt-In (Year 2–3)
- **City:** 50–60%
- **Opt-in participant:** 20–30% (per validated incident)
- **Platform:** 10–20% (operator fee)
- **Purpose:** Community dividend program, network density growth

### Phase 4+ — Data Marketplace (Year 3+)
- **Aggregate feed:** Annual subscription (actuaries, urban planners)
- **Individual records:** Per-query fee (insurance underwriting)
- **Incident evidence:** Per-claim fee (subrogation, fraud investigation)
- **Platform self-sustaining** on data revenue; enforcement revenue becomes secondary

---

## Business Model: Three-Tier Architecture

### Layer 1 — Open Source Core
**What's Open:**
- Node firmware and edge AI models
- Ingestion API and evidence pipeline
- Officer review portal (frontend + backend)
- Digital twin mapping engine
- ALPR and violation detection models
- Citation generation workflow
- Data schema and API specifications

**What's Proprietary:**
- Hosted cloud infrastructure
- MSSP orchestration layer
- Managed IT tooling and runbooks
- Insurance and data marketplace integrations
- Brand and certification program

**Licensing:**
- Community Edition: AGPL (anyone can self-host for free)
- Commercial Edition: BSL/commercial license (MSSP, SLA, insurance API)
- AGPL ensures modifications deployed as a service must be open sourced (vendor lock-out protection)

**Value:**
- Government procurement advantage (open source preference policies)
- Community-driven model improvement (academic partnerships, researcher contributions)
- Network effects via shared violation dataset (better models = better adoption)

### Layer 2 — MSSP (Managed Security & Operations)
**Customers:** Channel partners / integrators who sell to municipalities (not the cities themselves)

**Services Provided:**
- Multi-tenant cloud hosting (isolated environments per partner)
- Node device management (firmware updates, remote diagnostics, fleet health)
- Edge AI model deployment and auto-updates
- 24/7 NOC and incident response
- SLA guarantees (99.9% enforcement pipeline uptime)
- Compliance and audit logging (HTA, privacy, chain of custody)
- API operations and rate-limit management

**Pricing Model:**
- **Per-node monthly fee:** $50–150/node depending on SLA tier (varies by geography)
- **Per-citation transaction fee:** $0.50–2.00 per issued citation (volume discount)
- **Data API usage fees:** Tiered per query/month for insurance and marketplace

**Examples of MSSP Customers:**
- Small security integrator wins Hamilton Street Railway contract → uses CivicMesh MSSP
- Regional transit authority wants platform but lacks ops team → MSSP runs it
- Police department wants ALPR network for their own jurisdiction → MSSP operates

**Revenue Stability:**
- Monthly per-node fees = predictable recurring base
- Citation transaction fees scale with usage (win-win alignment)
- Data API customers = margin expansion after Year 2

### Layer 3 — Managed IT (24/7 Operations)
**Customers:** MSSP customers who want fully hands-off operations

**Services Provided:**
- Vehicle fleet health monitoring and remote diagnostics
- Firmware rollout and edge AI model updates (zero-touch)
- Node replacement logistics (swap out failed hardware)
- 24/7 helpdesk for partner's support team
- Compliance reporting and audit preparation
- Data retention and privacy compliance automation

**Pricing Model:**
- **Per-vehicle monthly:** $150–300 (includes hardware replacement, SLA response)
- **Incident response:** $500–2,000 per escalation (hardware failure, data integrity, etc.)

**Lock-In Effect:**
- MSSP + Managed IT combined = partner becomes operationally dependent
- Switching requires finding new ops team + rebuilding relationships + downtime
- Creates 3–5 year customer stickiness minimum

---

## Go-to-Market Strategy

### Phase 1: OC Transpo Pilot (Months 1–12)
**Objective:** Prove the model, gather data, build political capital

**Scope:**
- Bus lane + stop obstruction detection
- Select high-traffic routes (Transitway, downtown corridors)
- City keeps 100% of revenue
- Manual officer review for all citations

**Success Metrics:**
- 50+ buses deployed within 6 months
- $100K+ monthly enforcement revenue
- <5% false positive rate in officer review
- Data proves 5% reduction in bus lane collisions

**Stakeholders:**
- OC Transpo operations + planning
- City of Ottawa Finance
- Ottawa Police (optional partnership)
- Bylaw enforcement

### Phase 2: City Fleet Expansion (Months 12–24)
**Objective:** Scale across all city vehicles, expand violation types

**Scope:**
- All Public Works vehicles (sweepers, line painters)
- Bylaw enforcement vehicles
- Snow plows (winter, November–March)
- Salt spreaders (winter)
- Parks maintenance vehicles
- Expanded violation detection: accessible parking, school zones, snow routes, expired plates

**New Features:**
- Pothole/defect reporting → Public Works
- Sign/signal inventory → 311 integration
- Road condition data → digital twin
- ALPR queue to Ottawa Police (stolen vehicles, suspended plates)

**Revenue Sharing (TBD):**
- City: 80–85%
- OC Transpo/departments: 10–15%
- Platform operator: 5%

**Success Metrics:**
- 200+ city vehicles deployed
- $500K+ monthly revenue
- 8+ city departments using data products
- Digital twin live and updated daily

### Phase 3: Civilian Opt-In Launch (Months 24–36)
**Objective:** Network density growth, revenue share community program

**Scope:**
- Consumer hardware kit (subsidized or $200–400 out-of-pocket)
- Monthly revenue share for validated incidents
- Insurance API partnerships announced
- Marketing: "Road Safety Dividend" program

**Messaging:**
- "The city makes money, so do you"
- Community safety infrastructure, not surveillance
- Full transparency: monthly dashboards showing violation types and revenue distribution

**Success Metrics:**
- 5,000+ civilian opt-in by end of year
- 30–40% network coverage increase
- 10%+ adoption among insured drivers (insurance partnership)
- $1M+ monthly revenue (city + participants combined)

### Phase 4: Data Platform (Year 3+)
**Objective:** Self-sustaining platform via data subscriptions

**Customers:**
- Insurance actuaries (aggregate risk feeds)
- Urban planners (infrastructure & pedestrian data)
- Real estate developers (traffic pattern data)
- Logistics companies (route optimization)

**Pricing:**
- Annual subscription: $50K–200K
- Per-query: $500–5K (incident evidence, individual records)

**Success Metrics:**
- 10+ data subscribers
- Data revenue exceeds enforcement revenue

---

## Regulatory & Legal Framework

### Ontario Highway Traffic Act Compliance
- **Chain of custody:** Timestamped, GPS-anchored, encrypted evidence with audit logs
- **Human review:** Officer sign-off required before any citation issuance (differentiates from fixed photo radar)
- **Appeal rights:** Standard Notice of Contravention, defends in court like any human-issued ticket
- **Privacy:** PIPEDA compliance, especially for civilian data retention

### Municipal Procurement
- **RFQ process:** City defines enforcement need, invites bids
- **Platform neutrality:** Open source core means other vendors can't claim lock-in
- **Data ownership:** City owns all evidence and violation records, platform licensed back

### Insurance Partnerships
- **Consent-gated data:** Individual records only shared with explicit driver opt-in per insurer
- **ZK attestations:** Blockchain-anchored consent records for audit trail
- **Data minimization:** Only driving-relevant metrics shared, no location history

---

## Competitive Positioning

| Company | Mobile | Civilian Opt-In | Violation Detection | Enforcement | Revenue Share | Digital Twin |
|---------|--------|---|---|---|---|---|
| **Nexar** | ✅ | ✅ | Partial | ❌ | ❌ | ✅ |
| **Rekor/Waycare** | Partial | ❌ | ✅ | ✅ (gov only) | ❌ | ❌ |
| **Flock Safety** | ❌ | ❌ | ✅ (ALPR only) | ✅ | ❌ | ❌ |
| **CivicMesh** | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |

**Your Wedge:** Nobody has assembled civilian opt-in + enforcement + revenue share + insurance integration + digital twin. You own the entire stack.

---

## Feature Roadmap

### Phase 1 Launch (Q2 2024)
- [ ] Bus lane violation detection (OC Transpo)
- [ ] Basic ALPR (plate reading + speed)
- [ ] Officer review portal (web)
- [ ] Citation generation workflow
- [ ] Evidence storage (S3/MinIO)

### Phase 2 (Q4 2024)
- [ ] Expanded city fleet deployment
- [ ] Accessible parking violations
- [ ] School zone enforcement (time-gated)
- [ ] Snow route violations
- [ ] Expired/suspended plate flagging → police queue
- [ ] Digital twin foundation
- [ ] Pothole detection → Public Works API
- [ ] Sign/signal inventory → 311 integration

### Phase 3 (Q2 2025)
- [ ] Civilian opt-in hardware module
- [ ] Revenue share payment system
- [ ] Insurance API (individual records)
- [ ] Construction zone geofencing + violations
- [ ] Stolen vehicle / Amber Alert integration
- [ ] Hit-and-run evidence network
- [ ] Digital twin marketplace API

### Phase 4 (Q4 2025+)
- [ ] Predictive enforcement models
- [ ] Pedestrian/cyclist counting (urban planning)
- [ ] Transit performance analytics (OC Transpo)
- [ ] Aggregate data feeds (insurance actuaries)
- [ ] V2X integration (connected vehicles)
- [ ] Multi-city federation (scale beyond Ottawa)

---

## Tech Stack (Proposed)

| Layer | Technology | Rationale |
|-------|-----------|-----------|
| **Edge Compute** | Python + TensorFlow Lite | Lightweight AI on Raspberry Pi |
| **ALPR** | OpenALPR / Plate Recognizer API | Open source + high accuracy |
| **Dashcam Software** | GStreamer / ffmpeg | Hardware-agnostic video handling |
| **Ingestion API** | FastAPI + uvicorn | Async, scales to high throughput |
| **Database** | PostgreSQL + PostGIS | Geospatial queries (digital twin) |
| **Evidence Store** | MinIO (S3-compatible) | Self-hosted object storage, cost-effective |
| **Message Queue** | RabbitMQ or Kafka | Decouple ingestion from processing |
| **Officer Portal** | React + TypeScript | Modern SPA, responsive design |
| **Digital Twin** | PostGIS + GDAL + Cesium | Geospatial data + 3D visualization |
| **Data API** | GraphQL + REST | Flexible querying for insurers, planners |
| **Orchestration** | Kubernetes | Multi-tenant MSSP scaling |
| **Infrastructure as Code** | Terraform | Repeatable multi-city deployments |
| **Monitoring** | Prometheus + Grafana | Ops visibility for MSSP |
| **CI/CD** | GitHub Actions | Open source-friendly automation |

---

## Key Risks & Mitigations

| Risk | Impact | Mitigation |
|------|--------|-----------|
| **Privacy backlash** | Political opposition, provincial regulation | Transparent opt-in, data minimization, ZK attestations, public dashboards |
| **False positives** | Legal liability, driver appeals | Human officer review for ALL citations, high confidence thresholds, appeal-friendly design |
| **Vendor lock-in concerns** | City unwilling to adopt | Open source core, multi-vendor support, avoid proprietary data formats |
| **Insurance data abuse** | Regulatory action, driver trust loss | Consent-gated API, blockchain audit trail, third-party audits |
| **Hardware obsolescence** | Node fleet becomes outdated | Modular design, firmware-agnostic API, planned replacement schedule |
| **Model drift** | Detection accuracy degrades over time | Continuous retraining on city-owned data, community contribution framework |

---

## Success Metrics (Year 1)

| Metric | Target |
|--------|--------|
| OC Transpo buses deployed | 50+ |
| Monthly enforcement revenue | $100K+ |
| False positive rate | <5% |
| Average citation uphold rate | 90%+ |
| Collision reduction (bus lanes) | 5%+ |
| Officer review time per citation | <2 minutes |
| System uptime | 99%+ |
| Data retention compliance | 100% |

---

## Team Requirements (MVP)

| Role | FTE | Responsibilities |
|------|-----|------------------|
| **Platform Lead** | 1 | Architecture, roadmap, partnerships |
| **Edge AI Engineer** | 1 | ALPR, CV models, hardware integration |
| **Backend Engineer** | 2 | Ingestion API, database, event pipeline |
| **Frontend Engineer** | 1 | Officer portal, analytics dashboard |
| **DevOps / Cloud Architect** | 1 | Kubernetes, Terraform, monitoring |
| **Product Manager** | 0.5 | Stakeholder management, requirements |
| **Legal/Compliance** | 0.5 | HTA, privacy, procurement support |

---

## Next Steps

1. **Secure OC Transpo commitment** (LOI or pilot agreement)
2. **Finalize hardware bill of materials** (Raspberry Pi, dashcam module selection)
3. **Build MVP ingestion + portal** (FastAPI + React)
4. **Pilot on 5–10 buses** (December 2024)
5. **Gather metrics + refine models** (January–March 2025)
6. **Prepare Phase 2 city fleet proposal** (April 2025)

---

## Appendix: Detailed Specifications

### Hardware Bill of Materials (Per Node)

| Item | Model | Cost | Notes |
|------|-------|------|-------|
| Edge Compute | Raspberry Pi CM4 | $50 | 4GB RAM minimum, industrial temp range |
| Dashcam | Viofo A119 Mini | $200 | 1440p/30fps or equivalent, low-light capable |
| OBD-II Interface | STN1110 | $30 | ELM327 compatible, all vehicle models |
| GPS + Antenna | Neo-M8N | $25 | ±5m accuracy, 5Hz update |
| Cellular Modem | Quectel EC25 | $40 | LTE Cat-4, worldwide bands |
| Power Management | Custom 12V buck + UPS | $60 | Supercap + LiPo backup |
| Housing + Mounts | IP67 weatherproof | $80 | Automotive-grade, vibration-resistant |
| **Total BOM** | | **$485** | ~$500–600 fully assembled |

### ALPR Confidence Scoring

- **99%+:** Plate clearly visible, standard fonts, clean background → auto-issue
- **95–99%:** Slight obstruction or glare, manual review recommended → officer review
- **90–95%:** Partial plate, OCR ambiguity → human review mandatory
- **<90%:** Discarded, not investigated

---

## Glossary

- **ALPR:** Automatic License Plate Recognition
- **BSL:** Business Source License (proprietary licensing model)
- **CV:** Computer Vision
- **HTA:** Ontario's Highway Traffic Act
- **MSSP:** Managed Security Service Provider
- **OBD:** On-Board Diagnostics (vehicle interface)
- **RFQ:** Request for Quote (municipal procurement)
- **SLA:** Service Level Agreement
- **ZK:** Zero-Knowledge (cryptographic proofs)

---

**Document Version:** 1.0  
**Last Updated:** June 1, 2026  
**Next Review:** August 1, 2026  
**Maintained By:** CivicMesh Project Lead
