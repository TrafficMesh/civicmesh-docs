# SRED-TM-001 SR&ED Project Ledger TrafficMesh v1

**SRED-TM-001**
**D-CENTRAL GROUP**
**TrafficMesh Technologies Inc.**
SR&ED Project Ledger — TrafficMesh


| Document ID | SRED-TM-001 |
|---|---|
| Version | 1.0 — Living document, updated daily |
| Status | ACTIVE — Day 1, non-negotiable |
| Date | May 2026 — ongoing |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — CRA Documentation |
| Relationship to SRED-CM-001 | Companion ledger — platform-level SR&ED documented in SRED-CM-001; TrafficMesh-specific hardware, enforcement AI, and fleet deployment R&D documented here |
| Related Documents | SRED-CM-001 | IRAP-TM-001 | IRAP-CM-001 | OS-PATROL-TM-001 |




| Executive Summary
This ledger covers SR&ED activities specific to TrafficMesh Technologies Inc. — the hardware node stack, violation detection AI models, OBD-II telemetry integration, digital twin engine, and the enforcement evidence pipeline. These activities are conducted by TrafficMesh Technologies Inc. as the operating entity, separate from the platform-level architecture R&D conducted by CivicMesh Inc. Both entities file separate SR&ED claims. Both require the same daily documentation discipline. |
|---|


# 1. TrafficMesh SR&ED Activity Register


| SR&ED Activity | Technical Uncertainty | Documentation Required | Eligible Expenditure Type |
|---|---|---|---|
| YOLOv8 violation detection model training and optimization | Model performance on Canadian road footage under variable conditions (weather, lighting, motion) — target accuracy not known in advance | Training run logs (hyperparameters, dataset composition, evaluation metrics), GitHub commits for each training iteration, failure documentation when model falls below threshold | Salaries + contractor costs + cloud compute materials |
| ALPR on-device optimization — cold weather and motion | Production-quality plate recognition under -30°C, frost, salt spray, motion blur — no published benchmark for this constraint set | Accuracy reports by weather condition, camera settings experiments, preprocessing filter comparison (defogging, deblurring, contrast), failed approach documentation | Salaries + hardware materials |
| OBD-II telemetry fusion for speed estimation | Mathematical uncertainty: estimating speed of a target vehicle relative to a moving reference frame from OBD-II + GPS + optical flow | Sensor comparison logs, mathematical model documentation, accuracy vs reference tests, failed fusion algorithm documentation | Salaries + hardware materials + contractor costs |
| Digital twin continuous update algorithm | Novel algorithm for fusing 200+ moving node observations into a consistently accurate spatial model with defined update latency guarantees | Algorithm design documents, performance benchmarking, accuracy tests against known ground truth, update latency measurements | Salaries + cloud compute |
| IP67 -40°C node enclosure design and testing | Hardware design uncertainty: achieving IP67 rating at -40°C with 8-hour battery backup and <5-minute install constraint simultaneously | Design iteration log, test results (temperature chamber if available; outdoor winter testing otherwise), failed design documentation, STEP file version history | Salaries + materials (hardware prototypes) |
| Evidence chain-of-custody cryptographic pipeline | Novel: applying ATECC608B secure element key management to per-evidence-package signing in a resource-constrained edge compute environment with LTE dropout tolerance | Architecture decision log, implementation iterations, failed key management approaches, performance benchmarking (signing latency vs evidence package generation rate) | Salaries + hardware materials |
| Contractor node kit 5-minute universal install design | Design uncertainty: universal vehicle compatibility (dump truck to line painter to parks vehicle) with <5-minute install constraint — no existing commercial solution | Vehicle compatibility test matrix, install timing records by vehicle type, design iteration log with failed approaches, field test results | Salaries + materials (prototype kits) |


# 2. TrafficMesh Commit Message Standards
In addition to the general standards in SRED-CM-001, TrafficMesh-specific commit messages should reference the hardware and model context:


| Scenario | Example Commit Message |
|---|---|
| Model training run | Train TM-DETECT-v12: YOLOv8m fine-tuned on Ottawa-Winter-2026 dataset (4,200 annotated frames). mAP@0.5: 0.68 (target 0.85). Main failure mode: bus stop sign occlusion by snow. Next: augment with synthetic snow occlusion data. |
| Hardware design iteration | Enclosure v4 cold seal redesign: v3 IP67 seal failed at -28°C due to silicone Shore hardness change. Switched to -55°C rated silicone (Dowsil 3140). Re-test scheduled. Previous 3 material attempts documented in hardware log. |
| OBD-II integration | OBD-II speed fusion experiment 7: Kalman filter approach gives 2.1km/h RMS error vs 3.8km/h for simple averaging. Still above 1.5km/h target for enforcement use. Investigating GPS RTK integration as reference signal. |
| Digital twin pipeline | Digital twin update latency fix: spatial query time reduced from 340ms to 87ms by replacing ST_DWithin with ST_Intersects on pre-computed bounding box grid. Meets 100ms SLA target. Approach 3 of 3 — previous two approaches documented in technical decision log. |


# 3. Hardware R&D Materials Documentation
Hardware purchased for SR&ED is eligible as materials expenditure. The following documentation is required for each hardware purchase:
- Purchase date and supplier invoice (kept with SR&ED records)
- Linear ticket reference showing the R&D activity the hardware supports
- Disposition record: whether the hardware was consumed in testing, destroyed in testing, or remains as a prototype asset
- If destroyed in testing: describe the test and the outcome in the Technical Decision Log on the date of the test


| Hardware Item | SR&ED Eligible? | Notes |
|---|---|---|
| NVIDIA Jetson Orin NX development kit | Yes — if used directly in R&D prototype | Document R&D use in Linear ticket. Keep invoice. |
| IMX477 camera modules — multiple units for testing | Yes — as materials consumed in R&D | Document each unit's role in the experiment log |
| ATECC608B secure element development boards | Yes | Document cryptographic pipeline R&D use |
| IP67 enclosure materials — multiple prototype iterations | Yes — prototypes consumed in testing | Document each design iteration and test result |
| Raspberry Pi / Jetson Nano (early prototypes before Orin) | Yes — if used in approved R&D project period | Early prototyping hardware — document experiments |
| Production hardware for client deployment | No — capital equipment, not R&D materials | Depreciate as capital; do not claim as SR&ED materials |


# 4. Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

