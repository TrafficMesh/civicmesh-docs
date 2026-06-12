# FIN-CM-002 CivicMesh Unit Economics Model v1

**FIN-CM-002**
**D-CENTRAL GROUP**
**D-Central Group Inc. / CivicMesh Inc.**
CivicMesh Unit Economics Model


| Document ID | FIN-CM-002 |
|---|---|
| Version | 1.0 |
| Status | Draft |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Financial |
| Purpose | Per-node economics, per-community model, municipal contract model, path to profitability |
| Related Documents | FIN-CM-001 | FIN-TM-001 | FIN-CM-003 | DC-CM-MSSP-001 | DC-CM-B2B-001 |


# 1. Per-Node Economics


| Executive Summary
The unit economics model answers the fundamental question: what does a single CivicMesh node cost to deploy and operate, and what revenue does it generate? The answer determines: (1) the minimum viable network density for profitability, (2) the MSSP operator margin, (3) the civilian opt-in incentive that drives adoption, and (4) the insurance data value per node-observation-day. |
|---|




| Economic Item | Amount (CAD) | Notes |
|---|---|---|
| HARDWARE — One-Time Per Node |  |  |
| Node hardware cost (DCMC wholesale) | $800–$2,500 | Tier 1 (Jetson Orin NX + full sensor suite): $2,500. Tier 3 (contractor kit): $800. Tier 2: ~$1,800 |
| Installation labour (certified technician) | $150–$500 | Tier 1 (permanent): $500 (4-hour install + credential ceremony). Tier 3 (5-min install): $150. Tier 2: $350 |
| Total hardware + install | $950–$3,000 | Amortized over 3-year node life: $26–$83/month |
| OPERATING COSTS — Monthly Per Node |  |  |
| MSSP NOC management fee | $15–$30 | Includes: health monitoring, firmware updates, evidence pipeline, NOC support. Volume discounts at 100+ nodes. |
| Cellular data (LTE SIM) | $8–$15 | Dependent on evidence upload volume. Bus route node: ~$12/month at average violation rate. |
| Cloud infrastructure (S3, compute, DB) | $3–$8 | Per-node share of platform infrastructure costs. Decreases with scale. |
| Total monthly operating cost | $26–$53 | Before amortized hardware. Full cost incl. amortization: $52–$136/month |
| REVENUE — Monthly Per Node |  |  |
| Citation revenue (city fleet node) | $80–$400 | Highly variable: bus stop obstruction on Transitway corridor: high. Residential plow route: lower. Average target: $150/month at steady state. |
| Citation revenue — MSSP share (20%) | $16–$80 | MSSP operator retains 20% of gross citation revenue attributed to managed nodes. |
| Digital twin data subscription contribution | $2–$8 | Per-node share of insurer/municipal data subscription revenue. Delivery network nodes: higher share. |
| Training certification (indirect) | $0 direct | Technicians certified to install the node → training revenue flows to CTC. Not per-node but driven by node count. |
| Total MSSP revenue per node per month | $18–$88 | At steady-state citation volume |
| NODE ECONOMICS SUMMARY |  |  |
| MSSP break-even (months from activation) | 6–18 months | Varies by node tier and enforcement corridor. Tier 1 bus route: 8–12 months. Tier 3 contractor kit: 3–6 months (lower hardware cost). |
| MSSP NPV per node (3-year life, 15% discount) | $400–$2,200 | Wide range reflects corridor variability. Portfolio approach required — high-value corridors cross-subsidize low-value coverage. |


# 2. Per-Community Economics (HOA/Condo)


| Economic Item | Small HOA (20 nodes) | Medium Condo (60 nodes) | Large Campus (150 nodes) |
|---|---|---|---|
| Hardware + install (one-time) | $20,000–$40,000 | $60,000–$120,000 | $150,000–$300,000 |
| Monthly platform fee (NOC management) | $600–$800 | $1,800–$2,400 | $4,500–$6,000 |
| Monthly citation revenue — community share (60%) | $1,000–$3,000 | $3,000–$9,000 | $7,500–$22,500 |
| Annual insurance premium reduction (est.) | $2,000–$5,000 | $6,000–$15,000 | $15,000–$40,000 |
| Annual net benefit (revenue + savings - fees) | $6,400–$30,600 | $19,200–$91,800 | $48,000–$229,500 |
| Payback period on hardware | 4–12 months | 4–12 months | 4–12 months |


# 3. Municipal Contract Economics (OC Transpo Pilot → City Fleet)


| Phase | Node Count | Annual Gross Citation Revenue | Municipal Share (65%) | CivicMesh MSSP Revenue (20%) | CCSC/Community (15%) |
|---|---|---|---|---|---|
| OC Transpo Pilot (30 buses) | 30 | $216,000–$720,000 | $140,400–$468,000 | $43,200–$144,000 | $32,400–$108,000 |
| City Fleet Phase 2A (200 vehicles) | 200 | $1,440,000–$4,800,000 | $936,000–$3,120,000 | $288,000–$960,000 | $216,000–$720,000 |
| Full Ottawa Deployment (500 vehicles + civilian) | 800+ | $5,760,000–$19,200,000 | $3,744,000–$12,480,000 | $1,152,000–$3,840,000 | $864,000–$2,880,000 |


# 4. Path to Profitability


| D-Central Shield break-even | D-Central Shield (founding MSSP) breaks even when: Monthly MSSP revenue (20% citation share + NOC fees across managed nodes) exceeds: D-Central Shield operating costs (NOC staff, infrastructure, legal, admin). Estimated break-even: 150–200 nodes under management. OC Transpo pilot alone (30 nodes) does not achieve break-even — city fleet Phase 2A does. |
|---|---|
| CivicMesh Inc. platform break-even | CivicMesh Inc. (platform IP layer) earns: platform fees from D-Central Shield and external MSSPs, insurance API subscriptions, data marketplace subscriptions. Platform break-even estimated at: 3 active MSSP operators + 1 insurance subscriber + 500 active nodes under management. |
| Cooperative patronage sustainability | CCSC distributes 80% of citation revenue share to members. CCSC retains 20% for operations. CCSC becomes operationally self-sustaining when: monthly citation revenue share exceeds $15,000 (operations threshold). At 500 managed nodes generating average $150/month: $75,000 gross × 15% CCSC share = $11,250/month — approaching threshold. |


# Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

