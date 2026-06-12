# DC-CM-NODE-PROV-001 Node Provenance Tamper Detection v1

**DC-CM-NODE-PROV-001**
**D-CENTRAL GROUP**
**CivicMesh Inc.**
Node Provenance and Tamper Detection Specification


| Document ID | DC-CM-NODE-PROV-001 |
|---|---|
| Version | 1.0 |
| Status | Draft — court admissibility design |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Technical / Legal |
| SR&ED Eligibility | Strong — novel cryptographic provenance chain for enforcement evidence admissibility |
| Related Documents | DC-CM-DID-001 | DC-CM-VC-001 | DC-CM-HW-001 | DC-CM-CHAIN-001 | REG-DID-001 |




| Executive Summary
This document specifies the complete credential chain from hardware manufacture to active enforcement deployment, and the tamper detection system that maintains the integrity of that chain throughout the node's operational life. The goal is court-admissible evidence: any defence challenge to 'where did this footage come from and can we trust it' is answered by a cryptographic proof chain verifiable on a public blockchain without requiring testimony from any CivicMesh employee. |
|---|


# 1. The Complete Node Credential Chain


| Stage | Credential Issued | Issuer | Anchored? |
|---|---|---|---|
| 1 — Manufacturing | NodeProvenanceCredential — hardware model, firmware hash, component manifest, manufacturer DID | Certified Manufacturer (DID) | Hash anchored to IPFS, manufacturer DID on Polygon |
| 2 — Installation | InstallationCredential — technician DID, MSSP DID, installation date, vehicle type, calibration verified, test evidence package hash, installation photos (IPFS hashes) | Certified Technician (DID — signed on-device via Technician App) | Technician DID on Polygon; credential on IPFS |
| 3 — Activation | NodeActivationCredential — MSSP DID, activation date, community ID, deployment tier, active modules | MSSP Operator (DID — signed via NOC Console) | MSSP DID on Polygon; node activation event on Polygon |
| 4 — Operational | Node signs every evidence package with ATECC608B private key. Package includes credential chain hash (hash of all three credentials above). | Node device (hardware-bound private key) | Package signature verifiable via node DID public key on Polygon |


# 2. Tamper Detection Architecture


| Hardware Tamper Sensors | Enclosure intrusion switch (triggered by opening enclosure), accelerometer (detects mount change or removal), secure element key availability check (ATECC608B hardware key cannot be extracted — any attempt to substitute a different device is detectable by the key mismatch) |
|---|---|
| Firmware Integrity | On every boot, firmware hash is computed and compared against the ATECC608B-stored expected hash. If hash mismatch (unofficial firmware), node refuses to sign evidence packages and sends tamper alert to NOC. |
| Tamper Response Chain | (1) Hardware sensor trigger or firmware mismatch → (2) Immediate LTE alert to MSSP NOC → (3) Evidence package generation suspended → (4) Node DID suspension request sent to Polygon (on-chain — permanent record) → (5) All subsequent evidence packages automatically flagged as 'POST-TAMPER-UNVERIFIED' → (6) MSSP dispatches technician for physical inspection → (7) If cleared: new ATECC608B key provisioned, new firmware installed, new Installation Credential issued, node re-activated on Polygon |
| Evidence After Tamper | Any evidence package signed after the tamper event timestamp and before the re-activation event is automatically excluded from the enforcement queue — flagged in officer portal as POST_TAMPER_EXCLUDED. |
| Court Admissibility Impact | The tamper detection chain means that if a defence attorney argues the node was tampered with to fabricate evidence, the blockchain record shows either: (a) no tamper event was recorded (node was never compromised), or (b) a tamper event was recorded and all post-tamper evidence was excluded. Either way, the platform's response is documentable and defensible. |


# 3. Evidence Package Verification — Public API
Any party (defence counsel, court, independent auditor) can verify a CivicMesh evidence package using the public verification API at verify.civicmesh.ca — no account required:
- Submit: evidence package ID (from citation notice)
- System resolves: node DID → Polygon DID document → node public key
- Verifies: package JWS signature using node public key
- Resolves: credential chain hash from package → IPFS → full credential chain
- Verifies: each credential in chain → issuer DID on Polygon → authorized issuer status → no revocation
- Checks: no tamper events recorded for node between activation and package capture timestamp
- Returns: VERIFIED / TAMPER_DETECTED / CREDENTIAL_REVOKED / SIGNATURE_INVALID with full technical detail
Response time target: <2 seconds. No rate limiting for legal verification queries (with citation reference number).
# 4. Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

