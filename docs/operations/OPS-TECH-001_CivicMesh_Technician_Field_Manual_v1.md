# OPS-TECH-001 CivicMesh Technician Field Manual v1

**OPS-TECH-001**
**D-CENTRAL GROUP**
**D-Central Group / CivicMesh Inc.**
CivicMesh Technician Field Manual


| Document ID | OPS-TECH-001 |
|---|---|
| Version | 1.0 |
| Status | Draft |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Operations |
| Applies To | Certified CivicMesh Node Technicians (L1, L2, L3) |
| Related Documents | DC-CM-HW-001 | DC-CM-CERT-001 | DC-CM-APP-006 | OS-PATROL-TM-FLEET-001 |


# 1. Pre-Installation Checklist (All Tiers)


| Check | Procedure | Pass Criterion | Fail Action |
|---|---|---|---|
| Hardware verification | Scan node QR code in Technician App. Fetch NodeProvenanceCredential. | Credential status GREEN. Firmware hash matches published release. | Do not install. Report to NOC. Request replacement node. |
| Firmware verification | Technician App auto-checks firmware hash against ATECC608B stored hash. | Hash match confirmed. | Do not install. Suspect hardware tampering — return node. |
| Camera lens inspection | Physical inspection under adequate lighting. | Lens clean, no cracks, no fogging, IR cut filter intact. | Clean lens with provided microfibre cloth. Replace if cracked. |
| Cellular SIM status | Technician App → Node Diagnostics → Cellular. | SIM active, network registration confirmed. | Contact MSSP NOC to provision SIM. Do not install without confirmed cellular. |
| OBD-II adapter test | Connect adapter to test vehicle, check voltage reading in Technician App. | 12V ± 0.5V confirmed, no error codes. | Replace adapter. If vehicle OBD-II port is non-standard, use 12V cigarette lighter adapter (Tier 3 only). |


# 2. Installation by Tier
## Tier 1 — Permanent Fleet (OC Transpo Bus, Bylaw Vehicle, Police Cruiser)
- Mount camera bracket using vehicle-type specific mount kit. Torque specifications: [per vehicle-type installation guide — available in Technician App job brief].
- Route 12V DC power cable through vehicle trim to fuse box. Use provided add-a-fuse adapter. Fuse rating: 5A.
- Connect OBD-II adapter to vehicle OBD-II port (typically under dash, driver's side).
- Secure all cables with provided zip ties. Minimum 3 cable anchor points. No cable slack across moving components.
- Power on node. Wait for boot sequence completion (blue LED solid = ready). Confirm GPS lock in Technician App (≥8 satellites, accuracy <3m — may take up to 10 minutes in covered parking).
- Run calibration sequence in Technician App: camera alignment (crosshair overlay on live feed — adjust bracket until horizon is level and road fills lower 2/3 of frame), OBD-II speed test (drive 20m, confirm speed reading non-zero), test evidence package generation.
- Install tamper-evident seal over enclosure seam. Photograph seal in position.
- Complete InstallationCredential in Technician App. Sign with DID. Upload to NOC.
## Tier 3 — Contractor Kit (Magnetic Mount, OBD-II Power)
- Clean vehicle roof surface with isopropyl wipe at mount location. Allow to dry 30 seconds.
- Attach magnetic mount to roof. Press firmly for 10 seconds. Test retention by pulling laterally — should not move.
- Connect OBD-II adapter. Verify power LED on node activates.
- Angle camera downward approximately 15° from horizontal using ball joint. Verify field of view covers lane ahead in Technician App live preview.
- Route OBD-II cable to minimize driver distraction. Use provided cable clip on dashboard trim.
- Power on, GPS lock confirmation, test evidence package. Target install time: <5 minutes from kit bag open to test package confirmed.
- Affix tamper-evident seal over power connector junction. Photograph.
- Complete InstallationCredential. Hand Contractor Kit Agreement copy to driver/operator.
# 3. Calibration Standards


| Parameter | Acceptable Range | Calibration Procedure | Fail Action |
|---|---|---|---|
| Camera horizontal level | ±2° from true horizontal | Use bubble level built into Technician App camera preview overlay. | Adjust bracket, re-test. |
| Camera vertical angle | Road fills lower 60–70% of frame at 20m ahead | Drive to open road, review live feed in Technician App. | Adjust ball joint, re-test. |
| GPS accuracy at activation | <3m CEP (circular error probable) | Allow 10-minute GPS warm-up in open sky. Check accuracy reading in Technician App. | Do not complete installation until <3m achieved. If persists: suspect GPS hardware fault. |
| OBD-II speed reading | Speed reading within ±2 km/h of speedometer at 30 km/h | Drive at steady 30 km/h, compare OBD-II reading in Technician App to speedometer. | Check OBD-II adapter connection. Try alternate OBD-II protocol in Technician App settings. |
| Cellular upload test | Test evidence package uploaded to NOC within 60 seconds of generation | Technician App → Test Package → Upload → Confirm receipt in NOC Console. | Check signal strength. Move vehicle to better coverage area for test. If persistent: report SIM issue to NOC. |


# 4. Tamper Inspection Procedure
Required: on every kit return from contractor deployment, and whenever a tamper alert is triggered by the platform.
- Visual inspection: tamper-evident seal intact? If broken: photograph seal condition, log tamper event in Technician App, do NOT attempt to re-seal.
- Enclosure inspection: any physical damage, unauthorized openings, or modifications to cable routing?
- Firmware verification: connect to node via local WiFi hotspot in Technician App. Run firmware hash check. Any mismatch: flag as potential compromise.
- ATECC608B key check: Technician App → Advanced Diagnostics → Secure Element. Key presence confirmed? Key generation date matches provisioning record?
- Document all findings in Technician App tamper inspection form. Sign with L3 DID (tamper inspections require L3 credential minimum).
- If tamper confirmed: node is quarantined, NodeActivationCredential revoked by NOC, evidence packages from node post-tamper are flagged. Node sent to D-Central Forensics for detailed investigation before return to service.
# Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

