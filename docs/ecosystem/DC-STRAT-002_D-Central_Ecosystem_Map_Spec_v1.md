# DC-STRAT-002 D-Central Ecosystem Map Spec v1

**DC-STRAT-002**
**D-CENTRAL GROUP**
**Ecosystem Map**
Entity Relationships · Revenue Flows · Cooperative Linkages · Data Federation


| Document ID | DC-STRAT-002 |
|---|---|
| Version | 1.0 |
| Status | Draft |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential |
| Document Type | Visual specification — source document for designed one-pager |
| Related Documents | DC-STRAT-001 | DC-CM-BIZ-001 | DC-CM-REG-001 | DC-MASTER-DOC-001 |




| Executive Summary
This document specifies the content, structure, and data for the D-Central Ecosystem Map — the single visual that explains the entire Group to any audience in under two minutes. The Ecosystem Map is not a document; it is a designed visual asset (one-pager / A3 poster / pitch slide) produced from the specification contained here. This document defines every entity, every relationship arrow, every revenue flow, and every cooperative linkage that must appear in the final designed asset. |
|---|


# 1. Map Purpose and Audiences


| Audience | What the Map Must Communicate at a Glance |
|---|---|
| Investor | Scale of the ecosystem, revenue diversification, CivicMesh as flagship, cooperative structure as moat, government funding as de-risking mechanism |
| Municipality / Government | CivicMesh modules and their city-facing value, D-Central as technical operator, cooperative community benefit model |
| MSSP Partner | Their role in the ecosystem, what D-Central provides vs. what they operate, revenue model |
| Community Member / Resident | What the cooperative is, how they participate, what they earn, what data they control |
| Media / Public | Open source, community-owned, privacy-preserving — the anti-Flock, anti-Ring narrative in one image |


# 2. Entity Inventory for the Map
## 2.1 Group Level


| Entity | Type | Role on Map | Visual Layer |
|---|---|---|---|
| D-Central Group Inc. | CCPC — IP Holding | Centre anchor node | Layer 0 — Core |
| D-Central Work Cooperative (DWC) | Worker Cooperative | Labour and technical workforce | Layer 1 — People |
| D-Central Education Credit Union (DECU) | Credit Union (regulatory path) | Financial services, D-Credit issuance | Layer 1 — Finance |
| D-Central Manufacturing Coop (DCMC) | Manufacturing Cooperative | Open hardware node production | Layer 1 — Hardware |


## 2.2 CivicMesh Vertical


| Entity | Type | Role | Revenue Flow |
|---|---|---|---|
| CivicMesh Inc. | CCPC — Platform IP | Platform architecture, open source governance, MSSP franchise | Platform fees → Group |
| TrafficMesh Technologies Inc. | CCPC — Operating | Traffic enforcement, OC Transpo pilot, city fleet | Citation share + MSSP fees |
| CommunityShield Inc. | CCPC — Operating | Doorbell/camera network, HOA deployments, NOC | NOC fees + insurance API |
| CivicMesh Community Safety Coop (CCSC) | Worker/Consumer Coop | Member cooperative — residents and node owners | Patronage ← citation revenue |
| CivicMesh Training Cooperative (CTC) | Multi-stakeholder Coop | Certification programme, accredited providers | Certification fees |
| CivicMesh MSSP Cooperative | Worker/Producer Coop | Certified MSSP operators | Platform fee patronage |
| D-Central Shield | MSSP entity | First MSSP — operates platform for early clients | MSSP contracts |
| D-Central Guard | Security Response entity | First certified response provider | Per-dispatch revenue |
| D-Central Forensics | Evidence Services entity | Court-ready evidence packages, insurance subrogation | Evidence package fees |


## 2.3 Connected Ecosystem Participants


| Participant Type | Relationship | Data Flow | Revenue Flow |
|---|---|---|---|
| Municipalities (Ottawa, etc.) | Data federation agreement | Citation data → city; road condition → city | City pays MSSP fee; city receives citation revenue share |
| OC Transpo | Pilot client — bus fleet nodes | Evidence packages → officer portal | OC Transpo pays node kit + MSSP fee; receives citation revenue |
| Insurance Companies (Intact, Desjardins, Aviva) | Data subscriber — API agreement | Anonymised aggregate + consent-gated individual records → insurer | Insurer pays API subscription; insurer reduces premiums for members |
| HOAs / Condo Corporations | Community network participant | CommunityShield footage → community NOC | HOA pays platform fee; HOA receives fine revenue; residents get patronage |
| University Research Partners (Carleton, uOttawa, Algonquin) | Mitacs Accelerate partnership | Research outputs → platform improvement | Mitacs funds 75% of research labour |
| Certified MSSP Partners (future) | Franchise operators | Node data → NOC → officer portal | MSSP pays platform fee; earns client revenue; receives cooperative patronage |


# 3. Relationship and Flow Specification
## 3.1 Revenue Flows (arrows on the map)


| Flow | Description |
|---|---|
| Node → CCSC → Member | Citation revenue share flows from the enforcement pipeline through CCSC to member node owners as patronage (D-Credit or CAD) |
| City → TrafficMesh → D-Central Shield | Municipalities pay MSSP fees to D-Central Shield (first MSSP operator); Shield pays platform fee to CivicMesh Inc. |
| Insurer → CivicMesh Inc. | Insurance companies pay API subscription fees to CivicMesh Inc. for data products; portion flows to CCSC as additional member revenue |
| HOA → CommunityShield → CCSC | HOAs pay NOC platform fees; fine revenue flows through CommunityShield to CCSC member distribution |
| Training fees → CTC → Training providers | Certification fees flow through CTC (60% to delivering provider, 30% to CTC common fund, 10% to curriculum development) |
| Government → CivicMesh entities | IRAP monthly reimbursements (80% of eligible salaries); SR&ED annual refunds (up to 65%); ISC Phase 1/2 grants; Mitacs contributions |


## 3.2 Data Flows (federated arrows on the map)


| Flow | Description |
|---|---|
| Node → Ingestion API | Encrypted evidence packages (clip + GPS + timestamp + violation type + confidence) uploaded via LTE from edge compute module |
| Ingestion API → Officer Portal | Validated evidence packages placed in human review queue; officer reviews and confirms or dismisses before any enforcement action |
| Officer Portal → Citation → City Revenue | Confirmed citations flow to municipal payment system; revenue distributed per revenue share agreement |
| Community Layer → Municipal Layer | Upward federation only on explicit consent, with legal authority verification, audit log entry, and community notification |
| CivicMesh → Insurance API | Anonymised aggregate corridor data and (consent-gated) individual driving records flow to insurance subscribers |
| Digital Twin → City Departments | Road condition data, asset condition registry, pothole locations, sign/signal status flow to Public Works, Planning, Emergency Management |


# 4. Visual Design Specification
The following specification is for the graphic designer who will produce the final Ecosystem Map asset. This document is the content source; the designer produces the visual.


| Design Parameter | Specification |
|---|---|
| Format | A3 landscape (420mm × 297mm) for print; 2480 × 3508px at 300dpi. Also export as 1920 × 1080px 16:9 slide version. |
| Colour Palette | Dark Blue #1F3864 (anchors, headings), Mid Blue #2E75B6 (secondary entities, arrows), Light Blue #D5E8F0 (backgrounds), White #FFFFFF (card fills), Black #000000 (body text) |
| Typography | Arial Bold for entity names; Arial Regular for descriptions; minimum 9pt for printed labels |
| Layout | Concentric ring layout: D-Central Group Inc. at centre. Layer 1: five verticals. Layer 2: cooperative entities. Layer 3: external participants (municipalities, insurers, HOAs, universities). Revenue flows as solid arrows. Data flows as dashed arrows. |
| Entity Cards | Rounded rectangle cards. Header bar in entity colour. Icon + entity name + one-line description. Revenue amount or flow label on connecting arrow where known. |
| Cooperative Indicator | All cooperative entities carry a small 'COOP' badge in green (#E2EFDA border). CCPC entities carry no badge. |
| Legend | Bottom-right corner: solid arrow = revenue flow, dashed arrow = data flow, green badge = cooperative entity, D-Credit symbol = cooperative currency flow |
| Produced By | [Graphic designer to be engaged — Canva Pro, Adobe Illustrator, or Figma acceptable] |


# 5. Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |

