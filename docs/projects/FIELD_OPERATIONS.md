# Field Operations: Technician Guild, Hardware Integrity & Future Capabilities

**Decentralized technician networks, tamper-proof hardware architecture, and scalable operational procedures for distributed enforcement nodes.**

---

## Part 1: Decentralized Technician Guild

### Credential Model

CivicMesh field operations run on verifiable, on-chain credentials using W3C Decentralized Identifiers (DIDs) and Verifiable Credentials (VCs), recorded immutably on Polygon blockchain.

Every technician has:
```
did:civicmesh:tech:abc123def456  ← Permanent on-chain technician identity
                                    (never changes, publicly verifiable)
```

### Credential Hierarchy

| Credential | Requirements | Scope | Duration |
|---|---|---|---|
| **INSTALL_L1** | 8h online training + shadow install + practical assessment + background check | Install T3/T4 kits (contractor, civilian) | 2 years |
| **INSTALL_L2** | Hold L1 for 6+ months + 5 supervised T1 installs + advanced calibration exam | Install T1/T2 nodes (permanent fleet, seasonal) + approve L1 work | 2 years |
| **MAINTAIN_L1** | Hold L1 + L2 + maintenance training + 90-day supervised maintenance log | Routine maintenance, calibration, firmware updates | 2 years |
| **AUDIT_L1** | Hold MAINTAIN_L1 + chain-of-custody test + CCSC board approval | Certify hardware post-install, inspect tamper seals, sign attestations | 2 years |
| **EXPERT_L1** | Hold AUDIT_L1 + jurisdiction expert witness standards + per-court approval | Expert witness testimony (Ontario HTA, Quebec Bill 31, etc.) | 2 years |
| **CERTIFIER** | CCSC board appointment only | Issue/revoke credentials to other technicians, conduct training | 2 years |

### Earning Credentials

**INSTALL_L1 Path (Entry Level)**
```
1. Online training (8 hours)
   └─ Topics: Hardware BOM, safety, calibration, chain-of-custody, legal basics
   └─ Quiz: 80% pass required
   
2. Shadow install (3–5 supervised installs with L2 technician)
   └─ Observe full install procedure
   └─ Questions answered, notes signed off by supervising tech
   
3. Practical assessment (1 unsupervised install under observation)
   └─ Examiner watches entire procedure
   └─ Checklist: camera FOV, cable routing, seals, calibration, burn-in
   └─ Pass/fail recorded on-chain
   
4. Background check
   └─ Criminal record search (jurisdiction-dependent)
   └─ No felony convictions, no violence or fraud history
   └─ Result recorded in credential issuance
   
5. Issuance
   └─ CERTIFIER signs credential on-chain: `did:civicmesh:tech:abc123 holds INSTALL_L1, issued 2026-06-05, expires 2028-06-05`
   └─ NFT issued, searchable in credential registry
```

**INSTALL_L2 Path**
```
1. Hold INSTALL_L1 for minimum 6 months
   └─ During this time: complete 5 T1/T2 installs, documented
   └─ Each install signed off by supervising L2 tech
   
2. Advanced calibration exam
   └─ Practical test: camera distortion matrix generation + OBD sensor fusion + GPS accuracy verification
   └─ Written test: speed/confidence scoring, model calibration, field trial procedures
   └─ 85% pass required
   
3. Issuance
   └─ CERTIFIER signs: `did:civicmesh:tech:abc123 promoted to INSTALL_L2`
   └─ Previous INSTALL_L1 credential remains active
   └─ New expiry: 2028-06-05
```

**MAINTAIN_L1 Path**
```
1. Hold INSTALL_L1 + INSTALL_L2
2. Complete maintenance training (4 hours)
   └─ Battery/supercap health, firmware OTA, intrusion recovery, recalibration
3. 90-day supervised maintenance log
   └─ Technician performs maintenance under observation
   └─ Supervisor signs off: "Maintenance completed correctly, to spec"
4. Issuance: `did:civicmesh:tech:abc123 holds MAINTAIN_L1`
```

**AUDIT_L1 Path**
```
1. Hold MAINTAIN_L1 (which implies L1 + L2)
2. Chain-of-custody exam (written + practical)
   └─ Understand hash chains, TPM attestation, firmware signing
   └─ Practical: inspect a node, identify tampering, document seals, verify integrity
3. CCSC board approval
   └─ Not automatic; board votes on auditor candidates
   └─ Board considers: reputation, technical depth, neutrality
4. Issuance: `did:civicmesh:tech:abc123 holds AUDIT_L1 (CCSC-approved)`
```

**EXPERT_L1 Path**
```
1. Hold AUDIT_L1
2. Jurisdiction-specific expert witness qualification
   └─ Ontario: Rules of Civil Procedure 53.02 (definition of expert); Ontario bar association approved list
   └─ Quebec: Code of Civil Procedure 282 + expert affidavit template
   └─ Federal: Rules of Court (Crown jurisdiction, per court)
3. Per-court approval
   └─ Must seek approval from specific court/tribunal before first expert testimony
   └─ Court reviews credentials, expertise scope, potential bias
   └─ Approval stored on-chain: `did:civicmesh:expert:abc123 approved by Superior Court of Ontario, 2026-07-15`
4. Issuance: `did:civicmesh:expert:abc123 holds EXPERT_L1 (Ontario Superior Court)`
   └─ Note: Different courts may grant different approval scopes
```

### On-Chain Credential Registry

All credentials stored immutably on Polygon smart contract:

```solidity
contract TechnicianRegistry {
  
  struct TechnicianRecord {
    bytes32 did;                    // did:civicmesh:tech:abc123
    string name;                    // Full legal name
    address walletAddress;          // Ethereum wallet for attestation signing
    
    // Credentials held
    CredentialType[] credentials;   // [INSTALL_L1, MAINTAIN_L1, ...]
    uint256[] credentialIssuedAt;   // Timestamp for each
    uint256[] credentialExpiresAt;  // Expiry for each
    bytes32[] credentialIssuedBy;   // did of CERTIFIER who granted each
    
    // Reputation tracking
    uint256 jobsCompleted;
    uint256 jobsPassed;             // Jobs that passed quality audit
    uint256 jobsFailed;             // Work flagged during inspection
    uint256 reputationScore;        // Weighted: (jobsPassed - jobsFailed*2) / jobsCompleted * 100
    
    bool active;
    bool revoked;
    uint256 joinDate;
  }
  
  mapping(bytes32 => TechnicianRecord) registry;
  
  event CredentialIssued(bytes32 did, CredentialType credential, uint256 expiresAt);
  event CredentialRevoked(bytes32 did, CredentialType credential);
  event JobCompleted(bytes32 techDID, bytes32 nodeID, bool passed);
}
```

### Credential Verification API

Public API endpoint for app-based verification during installations:

```
GET /api/credentials/v1/verify
Params: did (technician DID), credential (INSTALL_L1, etc.), optional jurisdiction

Response:
{
  "did": "did:civicmesh:tech:abc123def456",
  "name": "Jane Smith",
  "credentials": [
    {
      "type": "INSTALL_L1",
      "status": "ACTIVE",
      "issued": "2025-03-01T00:00:00Z",
      "expires": "2027-03-01T00:00:00Z",
      "issued_by": "did:civicmesh:certifier:xyz789"
    },
    {
      "type": "MAINTAIN_L1",
      "status": "ACTIVE",
      "issued": "2025-09-15T00:00:00Z",
      "expires": "2027-09-15T00:00:00Z",
      "issued_by": "did:civicmesh:certifier:xyz789"
    }
  ],
  "reputation": {
    "jobs_completed": 47,
    "reputation_score": 94,
    "recent_issues": 0
  },
  "blockchain_proof": "0xabc123...txhash"
}
```

### All Installations Logged On-Chain

Every install job creates an immutable on-chain record:

```
InstallationRecord {
  techDID: did:civicmesh:tech:abc123,
  nodeID: did:civicmesh:node:bus_456,
  timestamp: 1717420335,
  vehicleVIN: 1FTFW1ET0EKE12345,
  credentialUsed: INSTALL_L2,
  statusBefore: UNATTESTED,
  statusAfter: ATTESTED,
  burn_in_passed: true,
  signature: techDID.sign(above)
}
```

Quality audits sample these records (10% of all installs per month). Failed audits:
- Flag technician's work for re-inspection
- Reduce reputation score
- If systematic failures: credential revocation hearing with CCSC board

---

## Part 2: Expert Witness Network (Jurisdictional)

### Problem Context

In Ontario HTA enforcement, a citation issued via automated speed detection (our ALPR + OBD) can be contested in traffic court. The defendant's lawyer may demand:
- Proof the hardware was calibrated and functioning correctly at time of incident
- Expert testimony on ML confidence scoring and error rates
- Chain-of-custody proof (evidence hasn't been tampered with)
- Model calibration report showing the detection is scientifically reliable

Without an expert to provide this testimony, the citation is vulnerable to dismissal.

**Solution:** EXPERT_L1 credential holders who can testify to:
- Hardware functioning within spec at time of incident
- ML models calibrated, bias-audited, field-tested
- Chain of custody unbroken (blake3 hash chain + TPM signatures)
- Evidence integrity (cryptographic verification)

### EXPERT_L1 Scope of Knowledge

Expert must be able to explain (in layperson terms, for court):

**Hardware:**
- OBD-II speed measurement accuracy (±2 km/h typical error)
- GPS accuracy at time of incident (CEP95 from Ublox specs)
- Camera calibration (lens distortion, FOV, accuracy limits)
- Sensor fusion (how OBD, radar, camera signals are combined)
- Environmental factors (weather, lighting, vehicle type effects)

**Software:**
- TFLite inference: what is it, how does it work, what can go wrong
- Model confidence scoring: what does 97% confidence mean, what does 40% mean
- ALPR pipeline: detection, recognition, OCR stages
- Face blurring: when/where it happens, why
- Speed estimator: optical flow + radar fusion approach

**Chain of Custody:**
- blake3 hash chain: how breaking it is detected
- TPM attestation: what TPM does, why non-extractable keys matter
- Firmware signing: how updates are verified
- Heartbeat protocol: continuous integrity proof, 60-second intervals
- Intrusion detection: reed switch on enclosure

**Model Performance:**
- Bias audit methodology: Chi-square testing for demographic/environment fairness
- False positive rate (FPR): what 1.8% FPR means, how it's measured
- Field calibration: 30-day trial before production, what that proves
- Model versioning: when models change, why, how tracked
- Test conditions vs. real conditions: accuracy claims on field data

### Attestation Workflow (Per Incident)

**Step 1: Incident Flagged for Litigation**
- Citation denied by violator, case goes to traffic court
- System automatically flags: "Citation CM-2026-00421 may require expert testimony"
- Case assigned to case management system

**Step 2: Expert Review Package Generated (Automated)**
```
ExpertReviewPackage {
  incidentID: CM-2026-00421,
  nodeID: did:civicmesh:node:bus_456,
  timestamp: 2026-06-04T14:32:15Z,
  
  hardware_state: {
    firmware_version: v4.2.1,
    firmware_hash: blake3:abc123...,
    camera_calibration_date: 2026-05-15,
    obd_sync_error: 0.5ms,
    gps_accuracy: 2.1m,
    intrusion_flag: false,
    heartbeat_signed: true
  },
  
  heartbeat_log: [
    { timestamp: 14:32:00, firmware_hash: abc123, gps_accuracy: 2.1, intrusion: false, signature_valid: true },
    { timestamp: 14:33:00, firmware_hash: abc123, gps_accuracy: 2.1, intrusion: false, signature_valid: true },
    ...
  ],
  
  model_state: {
    model_version: ALPR_v2.3.1,
    confidence: 97,
    bias_audit_date: 2026-04-30,
    bias_audit_result: no_significant_bias,
    fpr_baseline: 1.8,
    training_data_size: 500K
  },
  
  chain_of_custody: {
    evidence_hash: blake3:def456...,
    hash_chain_verified: true,
    hash_chain_breaks: 0,
    tpm_signature_valid: true,
    storage_encryption: AES256,
    access_log: [{ who: Officer#4521, when: 2026-06-04T15:00, action: reviewed }]
  },
  
  violation_facts: {
    speed_detected: 67,
    speed_limit: 50,
    zone: school,
    detection_clean: true,
    model_disagreement: 0,
    manual_review_passed: true
  }
}
```

**Step 3: Expert Reviews Package**
- Expert downloads package (JSON + evidence clips from IPFS)
- Reviews all fields: hardware state, model calibration, chain of custody
- Expert conclusion: "Hardware was functioning to spec. Model was calibrated. Chain of custody unbroken. Detection is reliable."

**Step 4: Expert Signs On-Chain Attestation**
```
ExpertAttestation {
  expertDID: did:civicmesh:expert:xyz789,
  incidentID: CM-2026-00421,
  nodeID: did:civicmesh:node:bus_456,
  
  attestation: "I, Jane Smith, expert in traffic enforcement hardware and AI models, attest that:
    1. The hardware (Ublox GPS, STN1110 OBD dongle, Viofo A119 dashcam) was calibrated on 2026-05-15 and had no detected faults at time of incident.
    2. The ALPR model v2.3.1 was trained on 500K images, field-tested for 30 days, bias-audited, and shows 97% confidence on this detection.
    3. The hash chain is unbroken (0 breaks detected), TPM signature is valid, and evidence has not been tampered with.
    4. My expert conclusion: This citation is reliable and admissible.",
  
  signature: expertDID.sign(above),
  timestamp: 1717420335,
  jurisdiction: Ontario,
  court_approval: Superior Court of Ontario (Approval ID: 2026-00421-A)
}
```
- Attestation minted as NFT, immutable, timestamped
- Linked to citation NFT (creates permanent record)

**Step 5: Submit to Court**
- Expert DID + credentials + attestation submitted as evidence
- Court reviews expert's qualifications (from Polygon smart contract)
- Expert testimony in court or affidavit (jurisdiction-dependent)
- Citation upheld (if evidence deemed credible) or dismissed (if expert credibility challenged)

### Jurisdictional Matrix

**Ontario HTA Speed Enforcement**
```
Legal basis: Highway Traffic Act s.128(2) ("shall observe and record the speed")
Expert required? YES, for contested citations (R. v. Sedor, 2011 SCC 47)
Admissibility: Expert affidavit or live testimony per Ontario Rules of Court
Expert standard: Must meet Daubert/Mohan test (scientific reliability, expert qualification)
Certificate needed? Yes, Approval from Superior Court of Ontario (per court registry)
```

**Quebec (Bill 31 Regulation Changes, Future)**
```
Legal basis: TBD (Bill 31 passed 2024, implementation ongoing)
Expert required? Expected yes, for photo radar evidence
Admissibility: Quebec Code of Civil Procedure 282 (expert declaration)
Expert standard: Quebec bar association standards (to be clarified)
Certificate needed? Provincial expert list, to be established
```

**BC (Current Status: Photo Radar Banned)**
```
Status: Speed photo radar banned in BC (Motor Vehicle Act amendments, 2024)
Use case: Document template for future if policy changes
Expert path: Reserved, not active
```

**US States (Future Expansion)**
```
Jurisdiction: Per-state variation (each state has own evidence rules)
Federal: Rules of Court, Daubert standard (FRE 702)
Expert standard: Varies (some states require court-appointed experts, others allow party experts)
Strategy: Develop jurisdiction profiles per state as expansion occurs
```

---

## Part 3: Tamper-Proof Hardware Architecture

Tamper-proofing runs on 5 independent layers. Breach of ANY one layer can be detected and remediated. Breach of ALL five would require: physical disassembly, TPM key extraction (physically impossible), secure boot break (requires factory equipment), intrusion event to go undetected (logged), and runtime heartbeat tampering (requires TPM key stolen + backdoored firmware).

### Layer 1: Physical Sealing

**Enclosure Design:**
- IP67 aluminum case (saltwater-resistant anodizing)
- Rubber gaskets (reusable, food-grade silicone)
- Cable entry points: Marine-grade polyurethane sealant + cable glands
- Main seams: Ultra-sonic sealed (not glued, bonds material at molecular level)

**Tamper-Evident Seals:**
- Holographic seals (anti-counterfeiting foil, changes color at different angles)
- Serialized: Each seal has unique printed code (e.g., `TES-2026-001847-A`)
- Serial linked on-chain at manufacture: `sealID → nodeID, issued 2026-06-05`
- Placement:
  - Enclosure lid (1 seal)
  - OBD connector access (1 seal)
  - Camera/GPS compartment (1 seal)
  - Battery access panel (1 seal)
  - Total: 4 seals per node
- If seal broken: visibly obvious (foil tears, color fades)
- Broken seal = immediate evidence quarantine (all evidence from that node flagged as "potentially tampered")

**Security Fasteners:**
- Torx-with-pin (T15 head + center pin) M3/M4 screws
- Requires specialized driver tool (not standard Phillips/flathead)
- Prevents casual disassembly
- Pin sheared if tampered, visible to technician

**PCB Potting:**
- Entire main circuit board encapsulated in epoxy (industrial-grade, thermal-conductive)
- Purpose: Prevent physical probing of circuits, side-channel attacks
- Transparent epoxy: Interior components still visible (assembly verification)
- If potting broken: immediately visible, documented by technician inspection

### Layer 2: Hardware Identity (TPM 2.0)

**TPM Chip Specification:**
- Infineon SLB9670 (ISO/IEC 11889 compliant)
- Permanently soldered to motherboard (not user-removable)
- Air-gap isolation (physically isolated from main CPU, communicates via I2C/SPI)
- Non-extractable cryptographic key stored in secure enclave (hardware guarantee: key cannot be read, only used to sign)
- Certification: Common Criteria EAL 4+ (highest practical security certification for TPM)

**Key Generation (At Factory):**
```
1. TPM.GenerateKey(ECDSA, 256-bit, "node_identity_key")
   → private_key (stored permanently in TPM enclave, never leaves chip)
   → public_key (extracted, used for verification)

2. public_key + nodeID registered on-chain
   NodeRegistry contract stores: { nodeID → public_key }
   
3. Every device has unique public key
   Example: node bus_456 → pub_key 0x123...456
```

**All Telemetry Signed by TPM:**
```
Every 60 seconds:
{
  nodeID, timestamp, speed (from OBD), gps_fix, uptime, firmware_hash
  → sign with TPM private key
  → result: signature (ECDSA signature)
  → send to backend along with data
}

Backend verification:
  1. Extract public key for this nodeID from on-chain registry
  2. Verify signature matches data using public key
  3. If signature invalid → "TPM key compromised" alert → quarantine node
```

**Implication:** If someone tries to modify any telemetry (e.g., falsify speed data), the signature becomes invalid. Signature verification happens on every upload.

### Layer 3: Secure Boot Chain

**Boot Stages (Sequential Verification):**

```
Stage 0: ROM Bootloader (immutable, baked into Raspberry Pi CM4 ROM)
  → Runs manufacturer's bootcode (loads next stage)
  → Cannot be modified (ROM is read-only)
  └─ Loads: Stage 1

Stage 1: CivicMesh Bootloader (signed by CivicMesh factory key)
  → Verify: Signature check (ed25519, CivicMesh release key)
  → Verify: Hash check against known-good release
  → If invalid: Loop forever (node bricked until re-flashed by technician)
  → If valid: Load next stage
  └─ Loads: Kernel

Stage 2: Linux Kernel (signed, hash stored in TPM)
  → Verify: Signature check
  → Verify: Hash matches TPM-stored value (anti-rollback check)
  → If invalid: Panic, drop to recovery shell, no inference
  → If valid: Load next stage
  └─ Loads: Application

Stage 3: Application (CivicMesh inference engine, signed)
  → Verify: Signature check
  → Verify: Hash matches running version
  → If invalid: Refuse to run, alert NOC
  → If valid: Launch with sensor plugins
  └─ Loads: Plugins

Stage 4: Sensor Plugins (each signed individually)
  → Verify: Each plugin file signed, checked before loading
  → If invalid plugin: Skip that plugin, log error, continue with others
  → If all invalid: Core operation continues, plugins disabled
```

**Anti-Rollback Counter (In TPM):**
- Every firmware version has a "generation number" (monotonically increasing)
- Current firmware: generation 42
- Attempted rollback to generation 40: TPM rejects (generation < counter)
- Prevents: Reverting to older firmware with known security holes

**Firmware Updates (Signed Release Process):**
```
1. CivicMesh releases firmware v4.2.2
   → Signed by ed25519 private key (release key, held by 3 company cryptographers)
   → Staged rollout: 10% fleet first, 48 hours observation, then 50%, then 100%
   
2. Node receives firmware update
   → Check: Signature valid? (using public release key)
   → Check: Newer generation? (generation counter)
   → Check: Staged rollout quota? (are we in the 10%?)
   → If all pass: Begin update, verify post-update boot, log success
   
3. If update fails
   → Rollback to prior version automatically
   → Log incident, send to NOC
   → Tech notified to inspect device
```

### Layer 4: Chassis Intrusion Detection

**Hardware Setup:**
- Reed switch (magnetic contact sensor): solder to GPIO on main board
- Magnet: embedded in enclosure lid gasket
- When lid closed → magnet aligns with reed switch → circuit closed (GPIO = HIGH)
- When lid opened → magnet moves away → circuit open (GPIO = LOW)

**Detection & Logging:**
```
Firmware loop:
  Every second:
    if GPIO.read() == LOW:
      → Intrusion detected!
      → Sign event with TPM: { timestamp, "intrusion", nodeID }
      → Log to flash (immutable event log)
      → Send alert to backend: "Node bus_456 enclosure opened 2026-06-05 14:32:10"
      → Set flag: "intrusion_flag = true" in heartbeat (sent every 60 sec)
```

**Consequences:**
- All evidence from that node AFTER intrusion event: flagged "Tamper Suspected"
- Evidence BEFORE intrusion: remain valid
- Technician inspection required: must verify node integrity + apply new seals

**Clearance Process:**
```
1. Technician dispatched to site
2. On-site inspection:
   - Check for physical tampering (seals, screws, potting)
   - Run diagnostic: firmware hash, TPM attestation
   - If OK: Apply new tamper seals, document serials
3. On-chain re-attestation:
   TechnicianAttestation {
     action: "intrusion_cleared",
     inspectionResult: PASSED,
     newSealSerials: [TES-2026-001850-B, TES-2026-001851-B, ...],
     signature: techDID.sign(above)
   }
4. Backend clears intrusion flag
5. Evidence accepted again
```

### Layer 5: Runtime Integrity (Heartbeat Protocol)

**What is a Heartbeat:**
- Periodic cryptographic proof that the device is "healthy" and unmodified
- Sent every 60 seconds (can be tuned per deployment)
- Signed by TPM key (cannot be forged without possessing private key)

**Heartbeat Content:**
```
Heartbeat {
  nodeID: "did:civicmesh:node:bus_456",
  timestamp: 1717420335,
  
  system: {
    uptime_seconds: 864000,
    cpu_temp: 45,
    memory_free: 256MB
  },
  
  firmware: {
    version: "v4.2.1",
    firmware_hash: "blake3:abc123def...",
    generation: 42,
    trusted_boot: true
  },
  
  sensors: {
    gps_accuracy: 2.1,
    gps_lock: true,
    camera_fps: 30,
    obd_connected: true,
    obd_data_valid: true
  },
  
  security: {
    intrusion_flag: false,
    last_heartbeat_valid: true,
    seal_status: INTACT,
    tpm_responding: true
  },
  
  signature: "ECDSA_sig(above, TPM_private_key)"
}
```

**Backend Verification (On Every Upload):**
```
1. Extract node's public key from on-chain registry
2. Verify ECDSA signature using public key
   → If invalid: "TPM key compromised" → quarantine node immediately
3. Verify firmware_hash against approved release list
   → If not on list: "Unauthorized firmware" → quarantine
4. Verify generation >= prior generation
   → If less: "Rollback attempted" → quarantine
5. Check intrusion_flag
   → If true: Evidence flagged "tamper suspected"
6. If all checks pass → Accept telemetry as valid
```

**Anomaly Detection (Automated):**
- Missing heartbeat for >5 minutes: Node offline, NOC alert
- Firmware hash suddenly changes: OTA update detected, expected behavior
- Generation number decreases: Rollback attempt, quarantine
- Signature invalid: TPM key compromised, dispatch technician
- GPS accuracy >10m: Calibration drift, flag for maintenance
- CPU temp >70°C: Thermal stress, investigate

---

## Part 4: Installation Procedure (On-Chain Attestation)

### Pre-Install Checklist (30 Minutes)

**On Technician's Mobile App:**

1. **Scan QR Code on Node Box**
   - QR encodes: nodeID + serial number
   - App queries: `GET /api/nodes/verify?serial=ABC123456`
   - Backend response: Node exists, not previously attested, not counterfeit
   - ✓ Continue or ✗ Stop (counterfeit node detected)

2. **Verify Tamper Seals Are Intact**
   - Take 4 photos (one of each seal location)
   - Upload photos to IPFS: returns hashes (e.g., `ipfs://QmXyz1`, `ipfs://QmXyz2`, etc.)
   - Hashes stored in attestation (immutable proof seals were intact)
   - App scan QR on seal: verify serialization code matches node manifest
   - ✓ All seals intact or ✗ Stop (contact logistics, device may be damaged)

3. **Verify Technician Credential**
   - App reads technician's DID + credential from QR code (or NFC/password)
   - App queries: `GET /api/credentials/v1/verify?did=did:civicmesh:tech:abc123&credential=INSTALL_L2`
   - Response: Credential ACTIVE, expires 2028-03-01
   - Check vehicle tier: Is INSTALL_L2 allowed for this vehicle type?
   - ✓ Credential valid or ✗ Stop (tech not authorized, cannot proceed)

4. **Confirm Vehicle Assignment**
   - Technician enters vehicle VIN (from registration)
   - App queries: `GET /api/vehicles/manifest?vin=1FTFW1ET0EKE12345`
   - Backend confirms: Vehicle assigned to T1 (permanent fleet), expected location, expected node
   - ✓ Match or ✗ Stop (mismatched node, investigate)

### Physical Installation (45 Minutes)

**Step 1: Camera Placement**
- FOV requirement: 160°+ horizontal (legal requirement in Ontario, ensures full roadway capture)
- Mounting: Upper center of windshield (minimize obstruction, minimize glare)
- Cable: Route via headliner (away from airbag deployment zones)
- Testing: Start engine, verify no camera interference with wiper blades or climate vents
- Photo: Take photo of camera placement for attestation

**Step 2: Power & OBD Connection**
- T1/T2 nodes: Hardwire to 12V (cigar lighter fuse, <5A)
- T3/T4 nodes: OBD-II port (location: under steering column, driver-side)
- Power verification: Node should boot and show LED indicator
- Cable routing: Secure along interior trim, no sharp bends, avoid pinch zones
- Cable sealant: Apply marine-grade polyurethane at enclosure entry, let cure 2 hours

**Step 3: Seal & Fasten**
- Apply tamper-evident seals:
  - Seal 1: Enclosure lid (serialize, scan QR, log serial in app)
  - Seal 2: OBD connector opening (if used)
  - Seal 3: Camera/GPS compartment (if accessible)
  - Seal 4: Battery access (if applicable)
- Security fasteners: Replace standard screws with Torx-pin M3/M4 (require specialized driver)
- Final check: No visible gaps, all seals applied, all fasteners tight

### Post-Install Enrollment (30 Minutes)

**Step 1: Node Bootstrap**
- Power on node (should connect to cellular or depot Wi-Fi)
- Node auto-contacts enrollment server: `POST /api/enroll/register`
- Server responds with enrollment challenge (cryptographic nonce)

**Step 2: TPM Remote Attestation**
- Node signs enrollment challenge with TPM private key
- Node sends: signedChallenge + public_key
- Server verifies: Signature valid? Public key matches on-chain registry?
- ✓ Device identity confirmed or ✗ Device rejected (counterfeit or wrong node)

**Step 3: VIN Verification (Against OBD)**
- Node reads VIN from OBD connector
- Compares: VIN from OBD == VIN technician entered?
- ✓ Match → proceed or ✗ Mismatch → alert technician, investigate

**Step 4: Camera Calibration**
- Node captures calibration target image (checkerboard pattern, printed or displayed)
- Inference engine computes: lens distortion matrix (corrects barrel distortion, FOV aberrations)
- Result: Calibration matrix stored + signed by node's TPM key
- Calibration hash computed: `blake3(calibration_matrix)` → stored on-chain

**Step 5: GPS Datum Verification**
- Node acquires GPS fix (may take 30 seconds to 5 minutes depending on signal)
- GPS fix recorded + signed by TPM
- Accuracy reported: e.g., "2.1 meters CEP95"
- Verification: Accuracy within spec (±5m standard, <10m acceptable)
- ✓ GPS working or ✗ GPS weak signal, may need relocation

### Post-Install On-Chain Attestation (Immutable Record)

**Technician Signs Attestation:**

```json
{
  "attestationType": "INSTALLATION",
  "techDID": "did:civicmesh:tech:abc123def456",
  "nodeID": "did:civicmesh:node:bus_456",
  "timestamp": "2026-06-05T14:32:15Z",
  
  "vehicle": {
    "vin": "1FTFW1ET0EKE12345",
    "type": "T1_PERMANENT_FLEET",
    "assignment": "OC Transpo Route 1 Bus 456"
  },
  
  "location": {
    "latitude": 43.6629,
    "longitude": -79.3960,
    "city": "Toronto",
    "address": "OC Transpo Depot, 1500 St. Laurent Blvd"
  },
  
  "physical": {
    "cameraFOV": "160 degrees",
    "cameraPlacement": "upper center windshield",
    "powerConnection": "12V hardwire via cigar lighter",
    "obd_status": "not used (hardwired)",
    "cableRouting": "along headliner, secured"
  },
  
  "seals": {
    "seal_1_lid": {
      "serialCode": "TES-2026-001847-A",
      "location": "enclosure lid",
      "status": "INTACT",
      "photo_hash": "ipfs://QmXyz1"
    },
    "seal_2_obd": {
      "serialCode": "TES-2026-001848-A",
      "location": "OBD access",
      "status": "INTACT",
      "photo_hash": "ipfs://QmXyz2"
    },
    "seal_3_gps": {
      "serialCode": "TES-2026-001849-A",
      "location": "GPS compartment",
      "status": "INTACT",
      "photo_hash": "ipfs://QmXyz3"
    },
    "seal_4_battery": {
      "serialCode": "TES-2026-001850-A",
      "location": "battery panel",
      "status": "INTACT",
      "photo_hash": "ipfs://QmXyz4"
    }
  },
  
  "calibration": {
    "cameraCalibrationHash": "blake3:abc123def456...",
    "calibrationDate": "2026-06-05T14:32:15Z",
    "lensDistortionCorrected": true,
    "fovVerified": true
  },
  
  "sensors": {
    "gpsLock": true,
    "gpsAccuracy": "2.1 meters",
    "obd_connected": true,
    "cameraPing": "30 fps",
    "cellular": "connected, signal -85dBm"
  },
  
  "credential": {
    "techCredential": "INSTALL_L2",
    "credentialStatus": "ACTIVE",
    "credentialExpires": "2028-06-05"
  },
  
  "tpmAttestation": {
    "tpmPublicKey": "0x123abc...",
    "tpmChallengeSigned": true,
    "bootChainValid": true,
    "firmwareHash": "blake3:xyz789...",
    "firmwareVersion": "v4.2.1"
  },
  
  "signature": "ECDSA_sig(above, did:civicmesh:tech:abc123def456)"
}
```

**Posted to Blockchain (Polygon):**
- `POST /api/attestations/installation` → stores on-chain via smart contract
- InstallationAttestation NFT minted (immutable, timestamped, linkable to node)
- Node status changes: `UNATTESTED` → `ATTESTED`
- Evidence is now accepted from this node

### 24-Hour Burn-In (Quality Assurance)

**Automated Monitoring:**
```
For 24 hours post-install:
  Every 60 seconds:
    - Verify heartbeat is being sent
    - Check: GPS fix acquired + stays locked
    - Check: Camera frames being captured (30 fps)
    - Check: OBD stream active (valid speed data)
    - Check: Cellular upload working
    - Check: Firmware hash stable (no unexpected updates)
    - Check: No intrusion events
    - Check: TPM signature valid
    
If any check fails:
  → Flag node as "BURN_IN_FAILED"
  → Alert technician: "Node did not pass 24-hour burn-in, requires re-inspection"
  → Do not accept evidence from node until re-attested
  
If all checks pass for 24 hours:
  → Node status: "PRODUCTION_READY"
  → Evidence accepted from this node
  → Node can issue citations
```

---

## Part 5: Maintenance Schedule

### Preventive Maintenance (Calendar-Driven)

**Every 90 Days: Visual Inspection & Seal Check**
- On-site visit by MAINTAIN_L1 technician
- Checklist:
  - All 4 tamper seals intact (no cracks, discoloration, movement)
  - Cable routing undisturbed (no pinched, exposed, or corroded areas)
  - Enclosure for dents, cracks, corrosion
  - Lens for dirt, condensation, scratches
  - Cable connectors for loose connections
- Documentation: Photo of each seal, technician signs on-chain MaintenanceRecord
- If issues found: Schedule corrective maintenance

**Every 6 Months: Full Calibration Re-Run**
- Repeat camera calibration (capture checkerboard target, compute new distortion matrix)
- Repeat OBD synchronization test (compare OBD speed vs. GPS speed, verify <2% error)
- Repeat GPS datum check (acquire new fix, verify accuracy within spec)
- Document: New calibration hashes, comparison to prior calibrations
- If calibration drifted >5%: Investigate root cause (environmental, hardware aging)

**Every 12 Months: Battery & Backup Systems Check**
- Supercap/UPS health: Test 12V line voltage under load
- Measure capacitance: If <80% of rated: schedule replacement
- Document: Capacitor health report, capacity measured
- If replacement needed: Order component, schedule technician visit

**Every 2 Years: Security Audit & TPM Attestation**
- Firmware security audit: Review latest firmware for known vulnerabilities
- TPM attestation refresh: Re-sign TPM identity on-chain (proofs TPM still operational)
- Hash chain validation: Spot-check hash chain integrity (verify no breaks)
- Update calibration seals: Replace any seals showing age/wear (proactive)

### Remote Maintenance (NOC-Automated, Continuous)

**Heartbeat Monitoring:**
- Every 60 seconds per node, heartbeat expected
- Missing heartbeat >5 min → NOC alert, technician dispatch
- Detects: Power loss, cellular disconnect, firmware crash, hardware failure

**Model Performance Monitoring:**
- FPR (False Positive Rate) tracked daily per node
- If FPR > 2.5% (threshold): Auto-flag for field calibration
- Sends alert: "Node bus_456 FPR 2.8%, exceeds tolerance, schedule calibration"
- Technician re-runs camera calibration, uploads new calibration matrix

**Firmware Over-The-Air (OTA) Updates:**
- Signed firmware releases pushed from backend
- Nodes auto-download (if scheduled maintenance window)
- Staged rollout: 10% fleet for 48 hours, monitor for issues, then 50%, then 100%
- Pre-update verification: Signature valid, generation higher, storage space available
- Post-update reboot verification: New firmware boots, hash matches expected value
- If update fails: Automatic rollback to prior version, log incident

**SIM Data Monitoring:**
- Track cellular data usage per node
- Baseline: ~150 MB/month (typical upload + heartbeats)
- Alert if usage >300 MB/month: Possible data exfiltration, investigate
- Alert if usage <50 MB/month: Possible cellular disconnect, investigate

### Corrective Maintenance (On-Site Response)

**When Technician Visits for Repair:**
```
MaintenanceRecord {
  type: "CORRECTIVE",
  techDID: did:civicmesh:tech:abc123,
  nodeID: did:civicmesh:node:bus_456,
  timestamp: 1717420335,
  
  reason: "Supercap failed, node not recording during power loss",
  
  actions: [
    { action: "REPLACED_SUPERCAP", part: "Murata DRV3V3224U2R7", status: "SUCCESS" },
    { action: "RECALIBRATED_CAMERA", hash: "blake3:new123..." },
    { action: "REPLACED_SEALS", newSerials: ["TES-2026-001851-B", ...] },
    { action: "BURN_IN_TEST", result: "PASSED" }
  ],
  
  parts_used: [
    { part: "Murata DRV3V3224U2R7", serial: "SN20260605001", cost: "$12.50" }
  ],
  
  time_on_site: 90,  // minutes
  signature: techDID.sign(above)
}
```

- Every part replaced: Serial number logged (for future recall/warranty)
- Every action: Timestamped, signed by technician DID
- On-chain record: Immutable proof of what was done + when
- Future audits: Can trace part history (detect if same part failed twice)

---

## Part 6: Future — Ride-Sharing Integration

### Concept

Same physical hardware platform used for ride-sharing fleet operations (Uber/Lyft alternative, or traditional taxi). New software capability layer; no new hardware required for Phase 1.

### What Existing Hardware Supports (No Modification)

| Hardware | Ride-Share Capability |
|---|---|
| Front + Rear Cameras | Driver verification (face recognition), passenger presence detection |
| GPS | Live trip tracking, route recording, fare zone calculation |
| OBD-II | Vehicle health status, speed verification, fuel efficiency |
| Cellular | Real-time trip data, panic broadcast, driver-dispatch communication |
| Edge AI (TFLite) | Fatigue detection (head-droop), seatbelt compliance, sudden braking alerts |

### Phase 1 Software Capabilities (2026-2027)

**Driver Verification at Trip Start**
```
Process:
1. Driver taps NFC card / enters PIN
2. System triggers face recognition (uses rear-facing mirror camera)
3. Compares live face to driver profile photo (stored encrypted on-device)
4. If match (>95% confidence): "Driver verified, trip authorized"
5. If no match: "Driver mismatch, contact dispatcher"
6. Trip recording begins automatically

Benefit: Prevents driver impersonation, ensures insurance coverage
```

**Trip Recording & Consent**
```
Same dashcam pipeline as traffic enforcement:
- Front + rear video (redacted)
- GPS track (street-level, not exact coordinates)
- OBD telemetry (speed, acceleration, braking)
- Audio (optional, with passenger consent)

Stored on-chain:
- Trip ID (immutable)
- Driver DID (anonymized)
- Passenger consent status (on-chain)
- Duration, distance, speed profile

Use cases:
- Safety verification (if passenger disputes ride quality)
- Insurance claim support (accident investigation)
- Driver performance metrics (aggregate statistics)
```

**Safety AI (Real-Time)**
```
Models deployed on-device:
- Head-droop fatigue detection (driver is falling asleep)
  → Alert sent to dispatcher: "Driver fatigue, recommend break"
  → Driver shown: "You've been driving 10 hours, rest recommended"
  
- Hard braking event detection (sudden deceleration >0.5G)
  → Passenger safety check: "Are you OK?"
  → Log event (safety incident)
  → Optional: Auto-send to dispatcher if >1G (emergency braking)
  
- Seatbelt detection (computer vision, occupancy detection)
  → Ensures driver + passengers wearing seatbelts
  → Alert if not: "Seatbelt required for safety"

Data stored: Aggregated safety metrics (not individual driver shaming)
```

**Fare Calculation (Smart Contract)**
```
On-device computation:
- Start: GPS location + time
- End: GPS location + time
- Distance: GPS track length (street-level)
- Duration: Trip time
- Surge pricing: Query DAO smart contract for current multiplier
- Final fare: base_rate + (distance_km * $1.50) + (duration_min * $0.40) * surge_multiplier

Example: 5 km, 15 min, surge 1.0x = $5 + $7.50 + $6 = $18.50

Smart contract execution:
1. Passenger pays to smart contract address
2. Contract executes distribution:
   - 85% to driver wallet ($15.73)
   - 10% to node maintenance pool ($1.85)
   - 5% to DAO treasury ($0.93)
3. Instant settlement (no central platform taking 25% cut)

Advantage: Transparent, decentralized, driver keeps 85%
```

### Phase 2 Hardware Add-On Module (2027-2028)

**Expansion Port Connector:**
- Physical: I2C + SPI + GPIO header, IP67-weatherproofed
- Interior cabin module (plug-in, no soldering):
  - Interior cabin camera (rear-facing, passenger-side view)
  - NFC reader (tap-to-pay, passenger boarding card)
  - Panic button (physical, passenger activates)
  - Intercom speaker (driver/passenger + dispatcher two-way audio)

**Passenger Safety Features**
```
Check-in:
- Passenger taps NFC card at boarding
- System records: passenger identity (pseudonymous)
- Interior camera captures passenger (stored encrypted, deleted after trip)

Panic Button:
- Passenger pressed: System immediately:
  - Activates interior + exterior cameras (burst mode, captures 30 sec before + 60 sec after)
  - GPS locks current location (accurate coordinates)
  - Sends emergency alert to dispatcher (live audio feed via intercom)
  - Driver receives notification: "Passenger activated panic button"
  
Outcome: If incident, evidence is captured + location known

Intercom:
- Two-way audio: passenger ↔ driver ↔ dispatcher
- Encrypted (same TLS as backend)
- Audio compressed & uploaded (OPUS codec, ~50 KB/min)
```

**Revenue Distribution (DAO Model)**
```
Per-trip fare: $18.50
Distribution:
├─ 85% Driver: $15.73 (direct to driver's wallet)
├─ 10% Node Maintenance Pool: $1.85
│  (rotated among node operators, covers repair/replacement)
├─ 4% DAO Treasury: $0.74
│  (governance fund, development bounties)
└─ 1% Insurance/Contingency: $0.18
   (covers passenger injury claims, fraud disputes)

Driver Incentives:
- 85% is significantly higher than Uber (75%) or Lyft (72%)
- Attracts quality drivers, improves service
- Smart contract ensures immediate payment (no waiting 2-3 days)

DAO Treasury:
- Funded by micro-percentages across all rides
- Community votes on allocation (safety improvements, insurance reserves, etc.)
```

**Driver Rating (On-Chain, Immutable)**
```
After each trip, passenger rates driver (1-5 stars):
- Rating + comment stored on-chain (immutable)
- Driver cannot dispute or delete ratings
- Aggregate rating visible to all passengers
- Example: Driver "JohnSmith.driver" has 4.2/5 over 500 trips

Advantages:
- Transparent (ratings not manipulated by platform)
- Long-term reputation (can't game short-term)
- Portable (driver reputation follows across platforms, future feature)

Disadvantages:
- No driver appeal mechanism (immutable)
- Bad days are permanent (rated during coffee shortage, gets 3-star)
- Mitigation: 50+ trip minimum before rating impacts driver (new drivers protected)
```

### Regulatory Note

Ride-share licensing varies by jurisdiction (Toronto has taxi medallion rules, some US cities allow Uber/Lyft, others don't). This is a **capability**, not a product. Regulatory compliance is jurisdictional and would be managed during deployment to specific cities.

---

## Part 7: Future — Additional Security & Sensor Capabilities

### Software-Only Additions (No New Hardware)

**BOLO/Warrant Scanning (Active Warrants)**
```
Existing hardware: Dashcam (ALPR already deployed)

Enhancement:
- Real-time connection to law enforcement warrant database
- ALPR results cross-referenced: "Plate ABC1234 is stolen vehicle, priority alert"
- Example: Missing person Amber Alert (blue Honda CR-V, specific plate)
  → If detected: Immediate GPS broadcast to police, automatic recording burst

Privacy/Consent:
- Data only shared with authorized law enforcement (court orders)
- Public Amber Alert platform separate from enforcement system
- See docs/AMBER_ALERTS_STOLEN_VEHICLES.md for full implementation
```

**Abandoned Vehicle Detection**
```
Logic:
- Vehicle parked same spot >4 hours, designated no-parking zone
- Automated alert to bylaw enforcement: "Abandoned vehicle at King & Simcoe"
- Technician can respond, issue notice

Use case: Street cleaning days, permit-required parking, disabled person parking violations
```

**Crowd Density & Crosswalk Safety**
```
Existing hardware: Camera + edge AI

Enhancement:
- Computer vision counts pedestrians at intersections (anonymized blobs, not faces)
- Detects: High crowd density, dangerous crossing behavior
- Auto-report to traffic management: "Dangerous crosswalk at Yonge & Queen, 50+ pedestrians waiting to cross"

Use case: City planning (which intersections need signal timing changes), Vision Zero (collision prevention)
```

**Smoke/Fire Detection**
```
Computer vision on existing dashcam:
- Detects smoke plumes (building fire, car fire)
- Auto-alert to fire department: location + severity estimate
- High confidence only (avoid false positives from fog, steam)

Use case: Early warning system, complement existing 911 calls
```

**Counter-UAV Detection**
```
Existing hardware: 77 GHz millimeter-wave radar

Enhancement:
- Train detection model to recognize low-altitude UAV signatures
- Detects drones <500 ft altitude operating near critical infrastructure
- Real-time alert to authorities: "Drone detected near Toronto Pearson Airport, coordinates..."

Use case: Airport security, critical infrastructure protection
```

### New Capabilities Requiring Hardware Add-Ons (Modular Expansion Port)

**Air Quality Monitoring**
```
Sensor Module: CO2 + PM2.5 (Sensirion SPS30 + MH-Z19B, ~$30)
Interface: I2C via expansion port

Data collected:
- CO2 parts-per-million (urban baseline: 400–450 ppm)
- PM2.5 particles (urban baseline: 15–30 μg/m³)
- Sampled every minute, aggregated hourly

Use case:
- Real-time city air quality map (civicmesh.app/air-quality)
- Early warning for wildfire smoke (PM2.5 spike)
- Long-term study on traffic impact on urban air (enforcement = less speeding = less aggressive acceleration = less emissions)
- Data sold to research institutions, insurance companies (optional, DAO-voted)
```

**Noise Monitoring**
```
Sensor Module: MEMS microphone array (4× Knowles SPH0645, ~$20)
Interface: I2S digital audio via expansion port

Data collected:
- Decibel level (90 dB = heavy traffic, 110 dB = construction)
- Frequency spectrum (construction noise is 100–1000 Hz, traffic is 1000–3000 Hz)
- Sampled continuously, peak level logged per minute

Use case:
- Enforce noise bylaws (ban construction >80 dB, 22:00–06:00)
- Identify persistent noise violators (construction company, industrial)
- City planning (which roads are noisiest, may need sound barriers)
```

**Flood/Water Detection**
```
Sensor Module: Ultrasonic distance sensor (HC-SR04 variant, ~$5) + IR thermal camera (~$50)
Interface: GPIO + I2C via expansion port

Data collected:
- Road surface water depth (ultrasonic: 0–4 meters range)
- Pavement temperature (IR: detect frozen water, icing risk)
- Location + timestamp + depth recorded

Use case:
- Municipal flood early warning (detect dangerous water levels before emergency calls)
- Road salt effectiveness monitoring (IR detects whether salt is de-icing)
- Climate data collection (which streets flood first in heavy rain)
```

**Pothole/Road Roughness Detection**
```
Existing hardware: Accelerometer (IMU already on-device)

Enhancement:
- Analyze vibration signature (rough pavement has characteristic 50–200 Hz vibration)
- Distinguish: Normal vibration vs. pothole impact vs. speed bump
- Auto-report: "Pothole detected at King & Simcoe (depth ~5 cm, hazard level medium)"

Use case:
- Public Works prioritization (which streets need resurfacing most urgently)
- Before/after measurement (did the repair actually help)
```

**Streetlight Outage Detection**
```
Sensor Module: Ambient light sensor (BH1750, ~$2)
Interface: I2C via expansion port

Data collected:
- Ambient light level (0–65535 lux)
- Expected level at night (street light on): 50–200 lux
- Actual level: 0–10 lux (streetlight burned out)

Use case:
- Automatic reporting to Public Works: "Streetlight outage at Bloor & Bedford, safety hazard"
- Smart cities: Real-time streetlight inventory
- Vision Zero: Well-lit streets reduce collision risk
```

### Modular Expansion Port Architecture

**Hardware Design:**
```
Expansion connector location: Side of aluminum enclosure (IP67-rated weatherproof)
Pin header: I2C (2 pins) + SPI (4 pins) + GPIO (4 pins) + 5V power (2 pins) = 12-pin connector
Connector type: Molex MicroFit 3.0 (used in automotive, industrial)
Weatherproofing: Silicone overmold, mated cap (keep connector sealed when not in use)

Cost: ~$8 per node (connector + PCB integration)
```

**Software Plugin Architecture:**
```
Firmware supports plugin loading:
- File location: `/opt/civicmesh/plugins/` directory on filesystem
- Plugin format: ELF binary (ARM 32-bit)
- Discovery: Firmware scans directory on boot, auto-detects new plugins
- Verification: Each plugin ed25519-signed (same key as kernel/app)
- Loading: TPM-verified bootloader loads signed plugins before app runs
- Failure mode: Bad plugin signature → skip that plugin, continue with others

Plugin API:
- Read I2C/SPI bus (provided by kernel driver)
- Read GPIO pins
- Write log entries (sent to backend in next heartbeat)
- Access JSON config file (plugin-specific settings)
```

**Example Plugin: Air Quality Module**
```c
// File: /opt/civicmesh/plugins/air_quality_monitor.c (compiled → .so)

#include <civicmesh_plugin.h>

struct AirQualityData {
  uint16_t co2_ppm;
  float pm25_ug_m3;
  uint32_t timestamp;
};

void plugin_init() {
  // Initialize I2C bus, select Sensirion SPS30 address
  // Configure sampling interval (1 minute)
}

void plugin_read() {
  // Poll sensors, format data
  // Call log_data(JSON);
  AirQualityData data = {
    .co2_ppm = 425,
    .pm25_ug_m3 = 22.5,
    .timestamp = time(NULL)
  };
  log_data("{...}");
}

void plugin_cleanup() {
  // Stop sensors, release I2C bus
}
```

**DAO Approval Process for Plugins:**
```
1. Developer submits plugin code to GitHub (open source)
2. Community reviews: correctness, security, power consumption
3. TSC votes: Is this a good plugin? (RFC process)
4. If approved: Binaries compiled, signed with release key
5. Nodes auto-download + load on next OTA update
6. Telemetry aggregated, data available to public API
7. Community members or researchers can query air quality data
```

**Commercial Plugins (Future):**
```
Companies can develop proprietary plugins (BSL license):
- Sensor hardware supplier builds air quality module
- Supplies pre-compiled plugin binary
- Plugin is signed by company's key (not CivicMesh key)
- Firmware allows plugins signed by authorized partners
- Revenue: Company charges per node per month (~$5/node/month for premium)
- Data: Company can opt to share anonymized data back to DAO (incentivized)

Example: Sensirion partnership → "Sensirion Air Quality Monitor" plugin

Governance risk: Must prevent malicious plugins (limit to TSC-approved partners)
Advantage: Scales capabilities without CivicMesh building everything
```

---

## Part 8: Hardware BOM Additions (Tamper-Proof + Expandable)

This section documents what to add to `docs/architecture/01_HARDWARE_STACK.md` BOM table.

### Security Enhancements

| Component | Specification | Cost | Purpose | Installation |
|---|---|---|---|---|
| **TPM 2.0 Chip** | Infineon SLB9670, I2C/SPI, EAL 4+ certified | ~$5 | Non-extractable device identity, secure boot, cryptographic signing | Factory: Solder to main PCB |
| **Chassis Intrusion Switch** | Reed switch + magnet, wired to GPIO, sealed connector | ~$2 | Detect enclosure tampering (when lid opened) | Factory: Solder magnet into lid gasket, mount switch to enclosure wall |
| **Tamper-Evident Seals** | Holographic foil, serialized codes, 4× per node | ~$1 per seal | Physical proof of tampering (broken seal = visible) | Field: Tech applies seals post-install, documents serials on-chain |
| **Security Fasteners** | Torx-with-pin M3/M4 screws (set of 6), requires specialized driver | ~$3 | Prevent casual disassembly (deters low-skill tampering) | Factory: Replace standard screws in enclosure assembly |
| **Expansion Connector** | Molex MicroFit 3.0, I2C + SPI + GPIO + power, IP67 overmold | ~$8 | Future sensor modules (air quality, noise, water detection) | Factory: Solder to main PCB, route traces to GPIO |
| **PCB Potting Material** | Industrial epoxy (thermal-conductive, transparent), ~50 mL per node | ~$3 | Prevent physical probing/solder bridge attacks | Factory: Encapsulate main circuit board |

**Revised Hardware BOM Total:**
- Prior total: $485 per unit ($485 + shipping/testing = ~$560 fully landed)
- New security/expansion components: +$22 per unit
- **New total: $507 per unit (~$580 fully landed with assembly + testing)**
- Cost increase: ~4.6% (minimal impact, significantly improves security posture)

### Installation & Maintenance Costs

| Activity | Frequency | Labor Cost | Parts Cost | Cumulative Over 2 Years |
|---|---|---|---|---|
| Initial install (INSTALL_L2 tech) | Once | $150 | $0 | $150 |
| 90-day seal inspection | 8× | $50 | $5 | $550 |
| 6-month calibration | 4× | $75 | $0 | $850 |
| 12-month battery check | 2× | $50 | $0 (preventive) or $25 (replacement) | $950–1000 |
| Annual security audit | 2× | $100 | $0 | $1050–1100 |
| **Total 2-year ownership** | | | | **~$1100** |

Compared to:
- Hardware cost: $507
- 2-year maintenance: $1100
- **Total cost of ownership: ~$1600 per vehicle over 2 years**

---

## Summary

CivicMesh's field operations layer ensures:

✓ **Verifiable technician credentials** — Decentralized DIDs, on-chain verification, reputation tracking

✓ **Tamper-proof hardware** — 5-layer defense (physical seals, TPM identity, secure boot, intrusion detection, runtime heartbeat)

✓ **Legal admissibility** — Expert witness network for contested citations, jurisdiction-specific templates

✓ **Scalable installation** — Step-by-step procedure with on-chain attestation, 24-hour burn-in validation

✓ **Continuous maintenance** — Preventive calendar + remote monitoring + technician response

✓ **Future-proofed** — Ride-sharing capability (same hardware), sensor expansion (modular port), community plugins (DAO-approved)

All backed by immutable blockchain records and cryptographic proof.

---

**Next:** See [docs/architecture/01_HARDWARE_STACK.md](docs/architecture/01_HARDWARE_STACK.md) for updated BOM. See [docs/TRANSPARENCY_LAYER.md](docs/TRANSPARENCY_LAYER.md) for how this operational layer integrates with community accountability infrastructure.
