# Open Source Verification Guide

**Verify every claim in the Privacy Promise by reading the source code.**

---

## Purpose

CivicMesh makes explicit privacy promises to communities (see `MKT-CCSC-004_CivicMesh_Privacy_Promise_v1.md`). This guide shows exactly where in the codebase to verify each promise is real — not marketing, but cryptographically enforced or architecturally guaranteed.

**Audience:** Security researchers, independent auditors, journalists, technical community members.

---

## Promise 1: On-Device Face Blurring (Before Transmission)

**Promise:** "Faces are blurred on the vehicle before any video leaves the node."

### How to Verify

1. **Get the firmware source:**
   ```bash
   git clone https://github.com/civicmesh/firmware.git
   cd firmware
   git log --oneline | head -10  # Verify recent commits
   ```

2. **Inspect the video encoding pipeline:**
   ```bash
   # Edge video capture module
   cat edge/capture/camera_manager.py
   cat edge/capture/frame_encoder.py
   ```
   
   Look for:
   - Face detection model load (TFLite model name + version)
   - Blurring operation (should find `cv2.GaussianBlur` or similar)
   - **Critical:** Blurring happens BEFORE `h264_encode()` call
   - **Verify order:** `detect_faces() → blur_faces() → encode_video() → queue_upload()`

3. **Confirm no bypass paths:**
   ```bash
   # Search for code that might skip blurring
   grep -r "def upload_video" edge/inference/
   grep -r "raw_frame" edge/
   grep -r "without_blur" edge/
   ```
   
   Should find **zero** "skip blurring" code paths.

4. **Check the CI/CD:**
   ```bash
   cat .github/workflows/build_node_firmware.yml
   ```
   
   Look for test step: `test_frame_blurring` — this should run automatically on every commit.

### Verification Outcome

✅ **Verified if:**
- Blurring code is in `edge/capture/frame_encoder.py`
- `cv2.GaussianBlur()` call happens before `h264_encode()`
- Test `test_frame_blurring` exists and is non-trivial
- No code paths bypass blurring

---

## Promise 2: License Plate Hashing (Plates Not Stored in Raw Form)

**Promise:** "License plates are hashed at detection time. No raw plate strings are stored on the vehicle."

### How to Verify

1. **Inspect the ALPR inference code:**
   ```bash
   cat edge/inference/detector.py
   cat edge/inference/incident_builder.py
   ```

2. **Look for the hashing function:**
   ```bash
   grep -A5 "def hash_plate" edge/inference/
   grep -A5 "alpr_result\[" edge/inference/
   ```

   Should find something like:
   ```python
   alpr_result = run_alpr_model(frame)
   hashed_plate = blake3(alpr_result['plate']).hexdigest()
   # Then: store hashed_plate, not alpr_result['plate']
   ```

3. **Verify the incident record doesn't contain raw plate:**
   ```bash
   grep -r "plate_string" edge/  # Should be ZERO results
   grep -r "raw_plate" edge/     # Should be ZERO results
   ```

4. **Check the backend API ingestion:**
   ```bash
   cat backend/api/ingest.py
   cat backend/models/incident_validator.py
   ```
   
   Look for: API rejects evidence packages with unhashed plates (validates schema).

### Verification Outcome

✅ **Verified if:**
- Plate hashing happens in `edge/inference/detector.py`
- Raw plate never written to disk (search finds zero)
- Incident record schema has `plate_hash` but not `plate_string`
- Backend API validation rejects unhashed plates

---

## Promise 3: City-Controlled Encryption Keys (Platform Has No Access)

**Promise:** "City controls the encryption key for evidence storage. CivicMesh platform staff cannot decrypt without city permission."

### How to Verify

1. **Examine key provisioning:**
   ```bash
   cat docs/deployment/SECURITY.md
   cat backend/services/kms_gateway.py
   ```

2. **Trace the key flow:**
   - Look for: City provisions key via AWS KMS / HashiCorp Vault / or similar
   - City = account owner with sole access to key material
   - CivicMesh has read-only permissions on encrypted data
   - Decryption request requires city signature / approval

3. **Check the encryption code:**
   ```bash
   cat backend/services/evidence_storage.py
   grep -A10 "def encrypt_evidence" backend/
   grep -A10 "def decrypt_evidence" backend/
   ```

   Should see:
   - Evidence encrypted with city-provided key (not platform-generated)
   - Decryption requires city KMS call (not local operation)
   - No key stored in application config / secrets

4. **Verify audit log of key access:**
   ```bash
   grep -r "kms_decrypt_request" backend/
   cat backend/models/audit_logs.py
   ```
   
   Every decryption attempt should be logged (who, when, evidence_id, result).

### Verification Outcome

✅ **Verified if:**
- Key provisioning documented in SECURITY.md
- Encryption happens with city-supplied key (not platform-generated)
- Decryption requires city KMS interaction (not local key)
- Every decryption is audited

---

## Promise 4: Audit Log is Tamper-Proof (Database Constraints Prevent Deletion)

**Promise:** "Audit logs cannot be deleted, even by database administrators. The hash chain detects tampering."

### How to Verify

1. **Examine the audit log schema:**
   ```bash
   cat backend/database/schema_compliance.sql
   ```

   Look for:
   ```sql
   CREATE TABLE audit_logs (
     ...
     CONSTRAINT no_delete CHECK (1=1)
   );
   ```

2. **Verify the hash chain implementation:**
   ```bash
   cat backend/services/audit_log.py
   grep -A20 "def compute_hash" backend/
   grep -A10 "def validate_chain" backend/
   ```

   Should show:
   - Each entry: `blake3(prev_hash || entry_data) = entry_hash`
   - Chain validation computes hashes and detects breaks
   - Hash mismatch triggers alert

3. **Test deletion attempt (read-only verification):**
   ```bash
   # Don't actually test deletion! But check test coverage
   cat tests/integration/test_audit_immutability.py
   grep "DELETE FROM audit_logs" tests/
   ```
   
   Test should verify: `DELETE` throws constraint violation error.

4. **Check database user permissions:**
   ```bash
   cat config/database/users.sql
   ```
   
   Should show: REVOKE ALL from audit_logs table (read-only user for ops).

### Verification Outcome

✅ **Verified if:**
- `audit_logs` table has `CHECK (1=1)` constraint preventing deletes
- blake3 hash chain implemented in `audit_log.py`
- Chain validation tested in integration tests
- Database read-only user cannot delete

---

## Promise 5: No Cross-Tenant Data Leakage (Tenant Isolation in Code)

**Promise:** "Multi-tenant system prevents one city from accessing another city's data. Isolation is enforced in application code, not just database."

### How to Verify

1. **Examine tenant authentication:**
   ```bash
   cat backend/api/auth.py
   grep -A20 "def extract_tenant" backend/
   grep -A20 "tenant_id = " backend/
   ```

   Look for: `tenant_id` extracted from JWT at auth layer (not from user input).

2. **Check query filtering:**
   ```bash
   grep -r "WHERE tenant_id" backend/
   grep -r "WHERE city_id" backend/
   ```
   
   Every query should have `WHERE tenant_id = $1` (parameterized, not string concatenation).

3. **Verify no admin bypass:**
   ```bash
   grep -r "superuser" backend/
   grep -r "bypass_tenant" backend/
   grep -r "admin.*SELECT" backend/
   ```
   
   Should find: No tenant bypass for any user (all routes respect tenant).

4. **Check API endpoints:**
   ```bash
   cat backend/api/officer_portal.py
   grep -B5 "async def get_incidents" backend/
   ```
   
   Should show: Every endpoint receives `tenant_id` from auth context, uses it in query.

5. **Integration test:**
   ```bash
   cat tests/integration/test_tenant_isolation.py
   ```
   
   Should test: User A (city A) cannot read city B data.

### Verification Outcome

✅ **Verified if:**
- `tenant_id` extracted at auth layer (code path before query)
- All queries filter by tenant (no exception)
- Integration test verifies isolation
- No admin bypass paths

---

## Promise 6: Immutable Annual Report (Fields Cannot Be Null)

**Promise:** "Annual transparency report cannot omit data. All fields are mandatory. Omission is impossible."

### How to Verify

1. **Examine report schema:**
   ```bash
   cat backend/database/schema_reports.sql
   grep -A50 "CREATE TABLE annual_reports" backend/
   ```
   
   Every field should have `NOT NULL` constraint.

2. **Check the report generator:**
   ```bash
   cat backend/services/report_generator.py
   grep -A20 "def generate_annual_report" backend/
   ```
   
   Should show: All 8 sections from `DC-CM-GOV-003` template populated.

3. **Verify validation:**
   ```bash
   cat backend/models/annual_report_validator.py
   grep -B5 "validate_report" backend/
   ```
   
   Should throw error if any field is null.

4. **Test the validation:**
   ```bash
   cat tests/unit/test_annual_report_validation.py
   ```
   
   Test should verify: Attempting to save report with null field throws error.

### Verification Outcome

✅ **Verified if:**
- `annual_reports` table has all fields marked `NOT NULL`
- Report generator populates all 8 sections
- Validation fails on null field
- Integration test prevents publish if validation fails

---

## Promise 7: Open Governance (RFC Process is on GitHub)

**Promise:** "All major feature decisions go through a 14-day public comment RFC process. This is not discretionary."

### How to Verify

1. **Check the repository structure:**
   ```bash
   ls -la rfcs/
   ls -la rfcs/implemented/
   ls -la rfcs/rejected/
   ```
   
   All RFCs should be present in version control.

2. **Examine the CI/CD rule:**
   ```bash
   cat .github/workflows/rfc_validation.yml
   ```
   
   Look for: CI check that enforces 14-day comment period before merge.

3. **Inspect the voting mechanism:**
   ```bash
   cat docs/ecosystem/DC-CM-GOV-001.md
   # Look for: "Supermajority (4/5)" and voting SLA
   ```
   
   Should specify: How votes are counted, minimum 14 days, records public.

4. **Verify breaking changes go through:**
   ```bash
   # Check recent RFCs for breaking changes
   git log --grep="RFC" --oneline | head -20
   # All should have merged PRs (not force-pushes)
   ```

### Verification Outcome

✅ **Verified if:**
- `/rfcs/` folder exists with template + submitted RFCs
- CI enforces 14-day comment period (blocks merge before deadline)
- Voting tracked in GitHub (reactions or dedicated issue)
- Breaking changes require RFC (checked in code review)

---

## Promise 8: Public Model Weights and Training Data

**Promise:** "All deployed AI models have publicly available weights. Training data is published with sampling info."

### How to Verify

1. **Check HuggingFace model registry:**
   ```bash
   # Visit: https://huggingface.co/civicmesh/
   # Should see models listed:
   # - civicmesh/alpr-v2.3.1
   # - civicmesh/speed-estimator-v1.9
   # - civicmesh/lane-detection-v3.1
   # - civicmesh/object-detection-v4.2
   ```

2. **Verify training data repository:**
   ```bash
   # Visit: https://data.civicmesh.ca/
   # Should have downloadable datasets with:
   # - Data splits (train/val/test percentages)
   # - Sampling methodology
   # - Bias audit results
   ```

3. **Check model card content:**
   ```bash
   cat docs/AI_MODEL_GOVERNANCE.md
   # Each model card should reference:
   # - HuggingFace link
   # - Training data link
   # - Calibration report link
   ```

4. **Verify no proprietary black box:**
   ```bash
   # All models should be open (TFLite quantized weights)
   # Not cloud-only or proprietary format
   ls -la edge/ai/*.tflite
   ```

### Verification Outcome

✅ **Verified if:**
- All deployed models on HuggingFace (public)
- Training data accessible (not locked behind login)
- Model cards reference data + weights + calibration
- No models are cloud-only or proprietary

---

## Summary Table

| Promise | File(s) to Verify | Key Evidence |
|---------|-------------------|--------------|
| Face blurring on-device | `edge/capture/frame_encoder.py` | Blurring before encode, tests exist |
| Plates not stored raw | `edge/inference/detector.py` | Blake3 hashing, schema validation |
| City-controlled encryption | `docs/deployment/SECURITY.md`, `backend/services/kms_gateway.py` | City provisions key, CivicMesh no access |
| Audit log immutable | `backend/database/schema_compliance.sql` | `CHECK` constraint, hash chain tests |
| No cross-tenant leakage | `backend/api/auth.py`, integration tests | Tenant injection at auth, isolation test |
| Report fields mandatory | `backend/database/schema_reports.sql` | All fields `NOT NULL`, validation tests |
| Open governance | `rfcs/`, `.github/workflows/rfc_validation.yml` | RFC template, CI enforces deadline |
| Public models & data | HuggingFace registry, data.civicmesh.ca | Weights public, training data published |

---

## How to Run a Full Verification

**For independent auditors / security researchers:**

```bash
# 1. Clone the official repository
git clone https://github.com/civicmesh/civicmesh.git
cd civicmesh

# 2. Verify code integrity (signed commits, branch protection)
git log --format="%h %G? %s" | head -20  # Should show "G" (good signature)

# 3. Run the verification checks (all 8 promises)
./verify.sh  # Should output: "8/8 promises verified ✓"

# 4. Build from source (proves no hidden code in binaries)
make firmware
make backend

# 5. Publish your findings
# Create issue: "Verification Report: [date] — All promises verified"
# Or: "Issue found in [promise#]: [description]"
```

---

## If You Find a Problem

1. **File a security issue:**
   - Email: security@civicmesh.ca (GPG key on website)
   - GitHub: Use security advisory feature (private disclosure)

2. **Include:**
   - Promise # and text
   - File(s) that contradict the promise
   - Suggested fix (if any)
   - Public disclosure timeline (e.g., 90 days)

3. **We commit to:**
   - Respond within 48 hours
   - Fix or publicly dispute within 30 days
   - Credit you in the disclosure (unless you prefer anonymity)

---

Next: See [[TRANSPARENCY_LAYER.md]] for the full architecture. See [[AI_MODEL_GOVERNANCE.md]] for how models are verified and audited.

