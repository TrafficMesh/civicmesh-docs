# DC-CM-SUPPLY-001 Open Supply Chain Architecture v1

**DC-CM-SUPPLY-001**
**D-CENTRAL GROUP**
**CivicMesh Inc. / DCMC**
CivicMesh Open Supply Chain Architecture


| Document ID | DC-CM-SUPPLY-001 |
|---|---|
| Version | 1.0 |
| Status | Draft |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Technical |
| SR&ED Eligibility | Moderate — novel credentialing architecture for hardware supply chain integrity |
| Related Documents | DC-CM-HW-001 | DC-CM-DID-001 | DC-CM-NODE-PROV-001 | DC-CM-CERT-001 | DC-CM-MSSP-CERT-001 |




| Executive Summary
This document maps the complete supply chain from open hardware design to deployed enforcement node, showing how each step is credentialed, audited, and verifiable. The supply chain is the foundation of the evidentiary argument: a CivicMesh evidence package is trustworthy because every entity that touched the hardware or software — from the manufacturer to the installer to the MSSP operator — holds a verifiable credential on a public blockchain. This document is the master reference for the supply chain architecture. |
|---|


# 1. Supply Chain Map


| Stage | Entity | Credential Required | Credential Issued | Verification Point |
|---|---|---|---|---|
| Hardware Design | D-Central Group (CERN-OHL-S v2 publisher) | Root Trust Anchor (self-issued) | N/A | GitHub — public design files with CERN-OHL-S v2 licence |
| Hardware Manufacture | DCMC or certified manufacturer | Certified Manufacturer Credential (from D-Central Group) | Node Provenance Credential → Node DID | CIPO trademark on node (D-Central brand) + Polygon trust registry |
| Firmware Development | D-Central engineering team | Root trust anchor | Firmware hash published on GitHub with release signature | Any party can reproduce build and verify hash |
| Node Distribution | DCMC logistics or certified distributor | N/A — handled by DCMC cooperative | N/A | Serial number registry (internal) |
| Node Installation | Certified Technician (L2 minimum) | Technician Credential (from CTC or D-Central direct) | Installation Credential → Node DID | Officer portal credential verification + Polygon trust registry |
| Node Activation | Certified MSSP Operator | MSSP Operator Credential (from D-Central) | Activation Credential → Node DID | Polygon node activation event |
| Ongoing Operation | MSSP NOC operations | MSSP Operator Credential (annual renewal) | Evidence packages signed by node | Evidence verification API |
| Evidence Submission | Node (automated) | Node DID active + full credential chain valid | Evidence package with JWS signature | Officer portal auto-verification on display |
| Citation Issuance | Municipal Officer | Officer Authorization Credential (from municipal partner) | Officer DID signature on confirmed citation | Citation record + audit log |


# 2. Certified Manufacturer Programme


| Eligibility | Any manufacturer who: (a) agrees to manufacture strictly from the published CERN-OHL-S v2 open hardware design without prohibited modifications; (b) publishes any permitted design modifications under CERN-OHL-S v2; (c) passes D-Central quality inspection of first production batch; (d) holds the required product liability insurance. |
|---|---|
| Certification Process | Submit production samples → D-Central quality inspection → firmware hash verification → ATECC608B key provisioning audit → Certified Manufacturer Credential issued → manufacturer DID registered on Polygon |
| DCMC Position | DCMC is the founding certified manufacturer. Its competitive advantage is not exclusivity (any qualified manufacturer can be certified) but quality, supply chain relationships, and first-mover production volume. DCMC's manufacturing operation is structured as a cooperative — member manufacturers share revenue and participate in quality standards governance. |
| Annual Audit | Certified Manufacturer Credential renewable annually on successful audit. Audit covers: production process compliance with open hardware design, firmware hash integrity, ATECC608B provisioning process, and component sourcing (no prohibited substitutions). |


# 3. Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

