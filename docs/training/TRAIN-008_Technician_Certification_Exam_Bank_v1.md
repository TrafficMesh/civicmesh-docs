# TRAIN-008 Technician Certification Exam Bank v1

**TRAIN-008**
**D-CENTRAL GROUP**
**CivicMesh Training Cooperative / D-Central Group**
CivicMesh Technician Certification Exam Bank


| Document ID | TRAIN-008 |
|---|---|
| Version | 1.0 |
| Status | Draft |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Training |
| Classification | CONFIDENTIAL — Internal use only. Do not distribute to candidates or providers. |
| Purpose | Question bank for L1/L2/L3 written assessments and practical assessment rubrics |
| Related Documents | TRAIN-002 | DC-CM-CERT-001 | DC-CM-APP-007 |




| SECURITY NOTICE: This document contains assessment questions and rubrics. Distribution outside of D-Central Group and accredited CTC provider instructors is prohibited. Any question found to have been distributed to candidates before assessment will require that question set to be replaced and affected certifications to be re-assessed. |
|---|


# 1. Question Bank Structure


| Level | Question Pool | Questions Per Exam | Pass Threshold | Randomization |
|---|---|---|---|---|
| L1 Written | 100 questions across all L1 modules | 30 questions — randomized draw | 70% (21/30 correct) | Questions randomized per candidate. Module representation maintained (10Q module A/B/C/D proportional) |
| L2 Written | 150 questions across L2 modules | 50 questions — randomized draw | 75% (38/50 correct) | Module representation maintained |
| L3 Written | 200 questions across L3 modules | 60 questions — randomized draw | 80% (48/60 correct) | Module representation maintained |
| MSSP Operator Written | 120 questions across Track 2 modules | 50 questions — randomized draw | 75% (38/50 correct) | Module representation maintained |
| Municipal Partner Written | 100 questions across Track 5 modules | 40 questions — randomized draw | 75% (30/40 correct) | Module representation maintained |


# 2. Sample Questions by Module — L1


| # | Question | Correct Answer | Distractor Notes |
|---|---|---|---|
| L1-HW-001 | What is the minimum IP rating required for a CivicMesh Tier 2 seasonal node operating on a snow plow in Ottawa winter conditions? | IP67 | Common distractors: IP65 (Tier 3 minimum — not Tier 2), IP54 (insufficient), IP68 (higher than required) |
| L1-PRI-002 | A contractor working for the city asks you to cover the node camera while he completes work near a sensitive area. What do you do? | Refuse and explain that covering the camera is a tamper event that will be logged and reported to the city. Contact your supervisor. | Distractors: 'comply as requested', 'cover temporarily and uncover after', 'call police' |
| L1-HW-003 | What happens if the ATECC608B secure element in a node is replaced with a different unit? | The node's private key changes. All previously signed evidence packages remain valid. The new key must be provisioned by DCMC or a certified L3 technician. The Installation Credential must be reissued. | Distractors: 'nothing — same functionality', 'all prior evidence is invalidated', 'the node stops working entirely' |
| L1-EV-001 | A node captures footage of a potential violation. Before the footage can be used to issue a citation, what must happen? | A qualified officer must review the footage and confirm the violation by signing with their DID. | Distractors: 'the AI confirms it at high confidence', 'the NOC operator approves it', 'it is sent directly to the municipality' |


# 3. Practical Assessment Rubrics — L1


| Assessment Step | Pass Criterion | Fail Criterion | Points |
|---|---|---|---|
| Hardware verification | Scans node QR code. Correctly reads NodeProvenanceCredential. Identifies firmware hash. Reports credential status accurately. | Skips verification. Cannot interpret credential. Misreports status. | 20 points |
| Mount installation | Magnetic mount applied correctly. Press test performed and confirmed. Mount is level within ±5°. | Mount is crooked >5°. Press test not performed. Mount unstable when pushed. | 20 points |
| OBD-II connection | Adapter connected to correct port. Vehicle powered on. Speed reading visible in Technician App. | Wrong port attempted. Speed reading absent or zero with vehicle moving. | 20 points |
| Camera alignment | Camera angle within specification (road fills lower 60-70% of frame at 20m). Horizon level within ±2°. | Angle outside specification. Horizon significantly tilted. | 20 points |
| Test evidence package | Test package generated successfully. Package appears in NOC Console within 60 seconds. GPS coordinates are accurate to within 10m of known test location. | Package not generated. Package does not appear in NOC within 2 minutes. GPS accuracy >50m. | 20 points |
| Total time | Completed within 10 minutes for L1. | Exceeded 10 minutes. | Pass/Fail gate — must pass time limit in addition to rubric score |


# 4. Question Maintenance


| Annual review | All question banks reviewed annually by CTC Standards Committee. Questions that have been exposed (identified by candidates as having appeared) are flagged and replaced. |
|---|---|
| Post-incident review | After any significant platform change (new firmware version, new violation type, regulatory change), affected question bank modules are updated within 30 days. |
| Anomaly detection | Assessment platform tracks: time per question, answer pattern correlation across candidates, pass rate by question. Questions with anomalous pass rates (>90% or <30%) are reviewed for clarity or exposure. |
| Security classification | This document is Classification: CONFIDENTIAL. Access restricted to: D-Central Group engineering and training staff, CTC Standards Committee, accredited provider lead instructors (Module-specific questions only — not full bank). |


# Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

