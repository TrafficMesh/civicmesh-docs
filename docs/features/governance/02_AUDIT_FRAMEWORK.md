# Feature: Community Audit Framework

**Enable CCSC members to audit CivicMesh operations. Three audit types: personal, extraordinary, and annual.**

---

## Requirements

### 1. Personal Audit Request

**Who:** Any CCSC member or citizen

**What:** View all audit log entries that affect you personally

**How:** REST API endpoint requiring user authentication

**Output:** Time-ordered list of entries where user was subject of action

### 2. Extraordinary Audit Petition

**Who:** 20% of CCSC voting members

**What:** Trigger independent audit outside regular annual cycle

**Process:** 
1. Petition filed on-chain (transparent counting)
2. CCSC board selects auditor (D-Central excluded)
3. Auditor conducts investigation
4. Results published (aggregate only, some details confidential)

**Threshold:** 20% = automatic trigger (no discretion)

### 3. Annual Independent Audit

**Who:** Third-party auditor selected by CCSC board

**When:** December each year (mandatory)

**Scope:** 
- Data retention compliance (are we deleting old data as promised?)
- Consent enforcement (are we respecting civilian opt-in preferences?)
- AI model performance (FPR claims vs. real field performance)
- Revenue distribution (blockchain matches transparency report)

**Access:** Auditor gets read-only database access + decryption key (escrow)

---

## Architecture

### Personal Audit Endpoint

**API:** `GET /api/personal/v1/my-audit-log?start_date=2026-01-01&end_date=2026-06-05`

**Authentication:** JWT (citizen portal login)

**Response:**

```json
{
  "user": "citizen_id_hash_xyz",
  "requested_at": "2026-06-05T10:30:00Z",
  "entries": [
    {
      "timestamp": "2026-06-04T14:32:15.847Z",
      "action": "OFFICER_VIEWED_INCIDENT",
      "incident_id": "CM-2026-00421",
      "officer": "Officer #[ANON-7521]",
      "reason": "Enforcement review: Speeding violation",
      "result": "Citation issued",
      "data_accessed": ["Video (redacted)", "OBD telemetry", "ALPR confidence"]
    },
    {
      "timestamp": "2026-06-04T18:00:00Z",
      "action": "LAW_ENFORCEMENT_DATA_REQUEST",
      "request_id": "LEO-2026-00847",
      "authority": "WARRANT",
      "requesting_agency": "Toronto Police Service",
      "data_requested": ["Incident video", "GPS coordinates", "Incident metadata"],
      "approved": true,
      "community_notified_at": "2026-06-05T08:15:00Z"
    },
    {
      "timestamp": "2026-06-05T09:00:00Z",
      "action": "CITATION_APPEAL_FILED",
      "incident_id": "CM-2026-00421",
      "appeal_reason": "AI confidence below 95%",
      "appeal_status": "Under review",
      "result_expected": "2026-06-07"
    }
  ],
  "summary": {
    "total_entries": 3,
    "incident_views": 1,
    "leo_requests": 1,
    "appeals_filed": 1
  }
}
```

**Implementation:** `backend/api/personal_api.py`

```python
@router.get("/v1/my-audit-log")
async def get_personal_audit_log(
    user: User = Depends(get_current_user),
    start_date: str = Query(...),  # ISO format
    end_date: str = Query(...),
):
    """Return audit entries where user is subject of action."""
    
    # Convert user (citizen_id) to audit log hash (one-way)
    user_hash = blake3(user.citizen_id).hexdigest()
    
    # Query audit log for entries mentioning user
    entries = db.query("""
        SELECT * FROM audit_logs
        WHERE (what LIKE ? OR who_affected LIKE ?)
          AND when >= ? AND when <= ?
        ORDER BY when DESC
    """, (user_hash, user_hash, start_date, end_date))
    
    # Redact sensitive fields
    for entry in entries:
        entry['officer_name'] = None  # Remove officer identity
        entry['internal_notes'] = None  # Internal analysis only
    
    return {
        "user": user_hash,
        "requested_at": utc_now(),
        "entries": entries,
        "summary": {
            "total_entries": len(entries),
            "incident_views": count_by_action(entries, "OFFICER_VIEWED_INCIDENT"),
            "leo_requests": count_by_action(entries, "LAW_ENFORCEMENT_DATA_REQUEST"),
            ...
        }
    }
```

---

### Extraordinary Audit Petition

**Process:**

1. **Petition filed** (CCSC member)
   ```
   POST /api/governance/v1/audit-petition
   {
     "reason": "High false positive rate detected in community",
     "supporting_evidence": ["link1", "link2"],
     "proposed_scope": "AI model performance and retraining practices"
   }
   ```

2. **On-chain counter** (transparent counting)
   ```solidity
   contract AuditPetitionLedger {
     mapping(uint256 => PetitionRecord) petitions;
     
     struct PetitionRecord {
       uint256 petitionId;
       bytes32 petitioner;          // CCSC member ID (anonymized)
       string reason;
       uint256 timestamp;
       uint256 supportingVotes;     // How many signed this petition?
     }
     
     // Public query:
     function getPetitionStatus(uint256 petitionId) public view 
       returns (uint256 votes, uint256 threshold, bool triggered);
   }
   ```

3. **Threshold check** (automatic)
   - Total CCSC membership = 500 members (example)
   - 20% threshold = 100 signatures needed
   - Contract automatically triggers audit at 100+ signatures
   - No manual approval step

4. **Auditor selection** (CCSC board)
   - Pool of pre-approved auditors (published list)
   - D-Central team excluded from selection
   - Board votes on auditor
   - Auditor receives scope document + access keys

5. **Investigation** (Auditor, 30-60 days)
   - Spot-check data (sample 1000 incidents, verify claims)
   - Verify AI model performance (test models against live data)
   - Check consent enforcement
   - Interview stakeholders (optional)

6. **Results published** (Aggregate only)
   ```
   Audit Report: Community Petition #342
   Date: 2026-07-15
   Auditor: [Firm Name], [Credentials]
   Scope: AI false positive rate
   
   Findings:
   ├─ ALPR v2.3.1: Claimed FPR 1.8%, measured 1.9% (within tolerance)
   ├─ Speed estimator v1.9: Claimed 2.4%, measured 2.6% (flag)
   └─ Recommendation: Retrain speed estimator within 30 days
   
   Status: APPROVED (passed audit)
   ```

---

### Annual Independent Audit

**Requirements:** Auditor must be pre-approved (professional credentials, no conflicts)

**Pre-approval list maintained at:** `docs/governance/approved_auditors.md`

**Scope (from GOV-002):**

1. **Data Retention Compliance**
   - Are we deleting incident evidence after 3 years?
   - Are we deleting personal data per PIPEDA requests?
   - Check: Sample 20 deleted records, verify deletion is complete

2. **Consent Enforcement**
   - Are civilian opt-ins being respected?
   - Spot-check: 50 civilian records, verify consent tier matches data access
   - Check: No civilian data accessed without consent

3. **AI Model Performance**
   - Test deployed models on recent field data
   - Verify FPR claims match measured performance
   - Check: Model retraining was triggered at correct thresholds
   - Bias audit: Check for demographic/environmental bias

4. **Revenue Distribution**
   - Verify blockchain ledger matches transparency report
   - Spot-check: 10 months of revenue, verify math
   - Check: CCSC patronage distributed correctly

**Auditor access:**

```sql
-- Escrow key held by CCSC board
-- Provided to auditor for investigation only

CREATE ROLE auditor_xyz;
GRANT SELECT ON incidents TO auditor_xyz;        -- Read-only
GRANT SELECT ON audit_logs TO auditor_xyz;
GRANT SELECT ON enforcement_requests TO auditor_xyz;
GRANT SELECT ON revenue_ledger TO auditor_xyz;

-- Auditor CANNOT:
-- REVOKE CREATE ON SCHEMA FROM auditor_xyz;
-- REVOKE INSERT, UPDATE, DELETE ON ALL TABLES FROM auditor_xyz;
-- No encryption key access (only CCSC board holds key)
```

---

## Implementation

### Audit Log Schema

**Location:** `backend/database/schema_compliance.sql`

```sql
CREATE TABLE audit_logs (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,
  prev_hash VARCHAR(64) NOT NULL UNIQUE,
  entry_hash VARCHAR(64) NOT NULL UNIQUE,
  
  -- Who performed the action
  who VARCHAR(255) NOT NULL,  -- User ID (hashed for privacy)
  who_role VARCHAR(50) NOT NULL,  -- OFFICER, ADMIN, CIVILIAN, SYSTEM
  
  -- What happened
  what TEXT NOT NULL,  -- Action description
  action_type VARCHAR(50) NOT NULL,  -- OFFICER_VIEWED_INCIDENT, LEO_REQUEST, etc.
  
  -- When
  when TIMESTAMP NOT NULL,
  
  -- Where (system component)
  where_component VARCHAR(255) NOT NULL,  -- API endpoint, scheduled job, etc.
  
  -- Why
  why TEXT NOT NULL,  -- Context/reason
  
  -- Result
  result VARCHAR(50) NOT NULL,  -- success, failure, denied
  result_details TEXT,  -- Error message if failed
  
  -- Data affected
  data_affected TEXT,  -- List of tables/fields accessed
  
  -- Enforcement
  CONSTRAINT no_update CHECK (id IS NOT NULL),
  CONSTRAINT no_delete CHECK (1=1),  -- Prevents DELETE operations
  
  INDEX idx_who (who),
  INDEX idx_when (when),
  INDEX idx_action (action_type)
);
```

### Hash Chain Implementation

```python
# backend/services/audit_log.py

class AuditLogService:
    
    def append_entry(who: str, what: str, why: str, result: str) -> str:
        """Append entry to audit log, return hash."""
        
        # Get previous hash
        prev_entry = db.query("SELECT entry_hash FROM audit_logs ORDER BY id DESC LIMIT 1")
        prev_hash = prev_entry['entry_hash'] if prev_entry else "00000000"
        
        # Create entry
        entry = {
            'who': hash_user(who),  # One-way hash
            'what': what,
            'when': utc_now(),
            'why': why,
            'result': result,
        }
        
        # Compute hash (blake3)
        entry_blob = json.dumps(entry, sort_keys=True)
        entry_hash = blake3(prev_hash + entry_blob).hexdigest()
        
        # Insert into DB
        db.insert("audit_logs", {
            'prev_hash': prev_hash,
            'entry_hash': entry_hash,
            **entry
        })
        
        return entry_hash
    
    def verify_chain() -> bool:
        """Verify hash chain integrity (no tampering)."""
        
        entries = db.query("SELECT * FROM audit_logs ORDER BY id ASC")
        
        prev_hash = "00000000"
        for entry in entries:
            # Recompute hash
            entry_blob = json.dumps({
                'who': entry['who'],
                'what': entry['what'],
                'when': entry['when'],
                'why': entry['why'],
                'result': entry['result'],
            }, sort_keys=True)
            computed_hash = blake3(prev_hash + entry_blob).hexdigest()
            
            # Compare
            if computed_hash != entry['entry_hash']:
                # TAMPERING DETECTED
                log_alert(f"Hash chain broken at entry {entry['id']}")
                return False
            
            prev_hash = entry['entry_hash']
        
        return True
```

---

## Test Suite

**Location:** `tests/integration/test_audit_framework.py`

```python
def test_personal_audit_log_returns_user_entries():
    """User can view entries where they are subject"""
    user = create_test_citizen()
    
    # Simulate actions
    log_action(who="Officer #123", what="Viewed incident", who_affected=user.id)
    log_action(who="Admin", what="Accessed data", who_affected=user.id)
    
    # Query
    entries = get_personal_audit_log(user)
    
    assert len(entries) == 2
    assert entries[0]['what'] == "Viewed incident"
    assert entries[1]['what'] == "Accessed data"

def test_personal_audit_log_excludes_other_users():
    """User cannot see entries for other users"""
    user_a = create_test_citizen()
    user_b = create_test_citizen()
    
    log_action(who="Officer", what="Action for A", who_affected=user_a.id)
    log_action(who="Officer", what="Action for B", who_affected=user_b.id)
    
    entries = get_personal_audit_log(user_a)
    assert len(entries) == 1
    assert "Action for A" in entries[0]['what']

def test_audit_log_hash_chain_integrity():
    """Hash chain cannot be broken without detection"""
    log_action(who="Officer", what="Action 1")
    log_action(who="Officer", what="Action 2")
    log_action(who="Officer", what="Action 3")
    
    # Verify chain
    assert verify_chain() is True
    
    # Tamper with entry #2
    db.update("audit_logs", {'id': 2}, {'what': 'MODIFIED'})
    
    # Verify chain fails
    assert verify_chain() is False

def test_extraordinary_audit_petition_threshold():
    """20% of CCSC triggers audit automatically"""
    ccsc_members = [create_test_ccsc_member() for _ in range(100)]
    threshold = len(ccsc_members) // 5  # 20
    
    # File petition
    petition_id = file_audit_petition("Test reason")
    
    # Collect signatures (19 members)
    for member in ccsc_members[:19]:
        sign_petition(petition_id, member)
    
    # Audit NOT triggered yet
    assert get_petition_status(petition_id)['triggered'] is False
    
    # 20th signature triggers audit
    sign_petition(petition_id, ccsc_members[19])
    assert get_petition_status(petition_id)['triggered'] is True

def test_auditor_has_readonly_access():
    """Auditor can read all tables but not modify"""
    auditor = create_test_auditor()
    
    # Should be able to read
    incidents = db.query("SELECT * FROM incidents", user=auditor)
    assert len(incidents) > 0
    
    # Should NOT be able to modify
    with pytest.raises(PermissionError):
        db.update("incidents", {'id': 1}, {'status': 'dismissed'}, user=auditor)
    
    # Should NOT be able to delete
    with pytest.raises(PermissionError):
        db.delete("audit_logs", {'id': 1}, user=auditor)
```

---

## Public Audit Status Dashboard

**URL:** `civicmesh.app/transparency/audits`

```
┌─ Community Audit Status ────────────────────────┐
│                                                  │
│ Annual Independent Audit (2025)                 │
│ Status: ✅ COMPLETED (Dec 31, 2025)             │
│ Auditor: [Firm Name], [Credentials]             │
│ Result: PASSED                                  │
│ [View full report]                              │
│                                                  │
│ Extraordinary Audit Petitions                  │
│                                                  │
│ Petition #342: "AI model false positives"       │
│ ├─ Filed: June 1, 2026                         │
│ ├─ Signatures: 125 / 100 threshold              │
│ ├─ Status: TRIGGERED                            │
│ ├─ Auditor selected: [Firm], ETA: July 15      │
│ └─ [View petition details]                      │
│                                                  │
│ Personal Audit Requests (This Month)            │
│ ├─ Requests: 247                               │
│ ├─ Avg. response time: 2 hours                 │
│ ├─ Satisfaction: 98%                           │
│ └─ [Request your audit]                         │
│                                                  │
└──────────────────────────────────────────────────┘
```

---

## Success Criteria

- ✅ Personal audit endpoint returns correct user entries
- ✅ Hash chain implementation prevents tampering
- ✅ Extraordinary petition auto-triggers at 20%
- ✅ Auditor has read-only access to all tables
- ✅ Annual audit completed without fail (mandatory)
- ✅ Audit reports published (aggregate, no PII)
- ✅ Community can petition for extraordinary audits
- ✅ All audit requests logged and tracked

---

Next: See [[03_OPEN_GOVERNANCE.md]] for governance process implementation.

