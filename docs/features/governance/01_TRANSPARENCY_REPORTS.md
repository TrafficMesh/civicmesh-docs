# Feature: Transparency Reports Generation

**Auto-generate monthly and annual transparency reports from operational data. Zero manual editing, all fields mandatory.**

---

## Requirements

### 1. Monthly Summary Report

**Frequency:** First day of each month (automated)

**Output:** `civicmesh.app/public/reports/monthly/2026-06.json`

**Fields:**
```json
{
  "period": "2026-06",
  "generated_at": "2026-07-01T00:00:00Z",
  
  "network": {
    "active_nodes": 247,
    "jurisdictions": ["Toronto", "Ottawa", "Hamilton"],
    
    "violations": {
      "speeding": 6234,
      "lane_change": 2847,
      "accessible_parking": 1420,
      "school_zone": 1089,
      "total": 12847
    }
  },
  
  "enforcement": {
    "citations_issued": 11203,
    "citations_dismissed": 824,
    "false_positive_rate": 0.031,
    "appeals_filed": 47,
    "appeals_upheld": 25
  },
  
  "revenue": {
    "gross_total": 1240000.00,
    "municipal_share": 892800.00,
    "platform_fee": 186000.00,
    "ccsc_patronage": 99200.00,
    "operator_rewards": 62000.00
  },
  
  "law_enforcement": {
    "requests_received": 14,
    "requests_approved": 12,
    "requests_denied": 2,
    "avg_notification_hours": 38
  }
}
```

### 2. Annual Comprehensive Report

**Frequency:** December 31st annually (automated)

**Output:** `civicmesh.ca/reports/annual/2026.pdf` (rendered from same JSON template as monthly)

**8-section template (from GOV-003):**

1. **Network Overview**
   - Node count by jurisdiction
   - Vehicle types (bus, city vehicle, contractor kit)
   - All violations by type (table + sparklines)
   - Total revenue collected

2. **Enforcement Metrics**
   - Citations issued vs. dismissed
   - Breakdowns per violation type
   - False positive rate (target: <5%, actual: report true value)
   - Appeal outcomes (upheld vs. overturned)

3. **Law Enforcement Access**
   - Requests by authority type: WARRANT, REASONABLE_GROUNDS, EXIGENT, ROUTINE
   - Approval/denial rate
   - Notification timeline (avg. hours to notify community)
   - Any redaction requests (if applicable)

4. **AI Model Performance**
   - Models deployed (name, version, date deployed)
   - False positive rate per model
   - Retraining events (dates, reasons, outcomes)
   - Demographic/environmental bias audit results
   - Appeal rate (% of citations contested due to AI error)

5. **Privacy Compliance**
   - Data access complaints: received / resolved
   - Subject access requests: received / response time (avg.)
   - Data deletion requests: received / compliance rate
   - Breach incidents (if any, else: "0 breaches")

6. **Revenue Distribution**
   - Gross revenue by source (enforcement fines only)
   - Percentage breakdown (72% municipal, 15% platform, 8% CCSC, 5% operator)
   - CCSC patronage fund: recipients and amounts
   - Civilian rewards pool: total distributed

7. **Governance Activity**
   - RFC submissions this year
   - RFC outcomes (approved/rejected)
   - TSC voting record (supermajority decisions)
   - Contributor role changes
   - Community audit petitions filed + outcomes

8. **Corrective Actions**
   - Issues identified in prior audit
   - Remediation status (completed/in-progress/pending)
   - Outstanding items with timeline

---

## Architecture

### Data Flow

```
Operational DB (PostgreSQL)
  ├─ incidents table (violations, citations, dismissals)
  ├─ audit_logs table (all access)
  ├─ enforcement_requests table (law enforcement queries)
  ├─ revenue_ledger table (fines, distribution)
  └─ model_performance table (FPR, retraining events)
                    ↓
     Report Generator (Python scheduled job)
                    ↓
           Aggregation Engine
  ├─ Count violations by type
  ├─ Calculate FPR (dismissed / total)
  ├─ Sum revenue per bucket
  ├─ Check audit_logs for enforcement requests
  └─ Query law_enforcement_requests table
                    ↓
         Validation Engine
  ├─ Ensure all fields populated (no NULLs)
  ├─ Verify math (percentages sum to 100%)
  ├─ Spot-check referential integrity
  └─ Throw error if validation fails
                    ↓
         Report Output (JSON)
                    ↓
   ┌────────────────┬──────────────┐
   ↓                ↓              ↓
Public API      PDF Renderer   GitHub Commit
(civicmesh.app) (annual.pdf)   (archival)
```

### Report Generator Service

**Location:** `backend/services/report_generator.py`

```python
class TransparencyReportGenerator:
    
    def generate_monthly_report(period: str) -> dict:
        """Generate monthly summary (3-5 MB JSON, takes ~30s)"""
        # period = "2026-06"
        
        # Connect to ops DB (read-only)
        db = connect_readonly("operations_db")
        
        # 1. Network metrics
        active_nodes = db.query("SELECT COUNT(DISTINCT node_id) FROM telemetry WHERE period = ?", period)
        violations = db.query("SELECT violation_type, COUNT(*) FROM incidents WHERE period = ? GROUP BY violation_type", period)
        
        # 2. Enforcement metrics
        citations_issued = db.query("SELECT COUNT(*) FROM incidents WHERE period = ? AND status = 'issued'", period)
        citations_dismissed = db.query("SELECT COUNT(*) FROM incidents WHERE period = ? AND status = 'dismissed'", period)
        fpr = citations_dismissed / (citations_issued + citations_dismissed)
        
        # 3. Revenue (query blockchain ledger, not internal DB)
        ledger = query_blockchain("CivicMeshLedger", "cityCombinedEarnings", period)
        revenue = ledger['gross_revenue']
        distribution = ledger['distribution']
        
        # 4. Law enforcement requests
        leo_requests = db.query("SELECT * FROM enforcement_requests WHERE period = ?", period)
        
        # 5. Validate
        validate_report(monthly_report)  # Throws if any field is NULL
        
        # 6. Return
        return monthly_report
    
    def generate_annual_report(year: int) -> dict:
        """Generate annual comprehensive report (20-30 MB JSON, takes ~2 min)"""
        # Aggregate 12 monthly reports
        # Add year-over-year comparisons
        # Add auditor verification
        # Validate all 8 sections
        return annual_report
```

### Validation Engine

**Location:** `backend/models/report_validator.py`

```python
class ReportValidator:
    
    def validate_report(report: dict) -> bool:
        """Ensure all fields populated, no omissions"""
        
        mandatory_fields = {
            "period": str,
            "generated_at": str,
            "network.active_nodes": int,
            "network.violations": dict,  # must have all violation types
            "enforcement.citations_issued": int,
            "enforcement.citations_dismissed": int,
            "enforcement.false_positive_rate": float,
            "revenue.gross_total": float,
            "revenue.municipal_share": float,
            "revenue.platform_fee": float,
            "revenue.ccsc_patronage": float,
            "revenue.operator_rewards": float,
            "law_enforcement.requests_received": int,
            "law_enforcement.requests_approved": int,
            "law_enforcement.requests_denied": int,
            # ... all 8 sections
        }
        
        for field, expected_type in mandatory_fields.items():
            value = get_nested(report, field)
            
            # No NULLs, no missing fields
            if value is None:
                raise ReportValidationError(f"Missing mandatory field: {field}")
            
            # Correct type
            if not isinstance(value, expected_type):
                raise ReportValidationError(f"Field {field} has wrong type: {type(value)}, expected {expected_type}")
            
            # If numeric, no negative (except explicitly allowed)
            if isinstance(value, (int, float)) and value < 0:
                raise ReportValidationError(f"Field {field} cannot be negative: {value}")
        
        # Verify revenue math
        total = report['revenue']['municipal_share'] + \
                report['revenue']['platform_fee'] + \
                report['revenue']['ccsc_patronage'] + \
                report['revenue']['operator_rewards']
        
        if abs(total - report['revenue']['gross_total']) > 0.01:  # Allow rounding error
            raise ReportValidationError(f"Revenue doesn't sum: parts={total}, total={report['revenue']['gross_total']}")
        
        # Verify FPR math
        fpr = report['enforcement']['citations_dismissed'] / \
              (report['enforcement']['citations_issued'] + report['enforcement']['citations_dismissed'])
        
        if abs(fpr - report['enforcement']['false_positive_rate']) > 0.001:
            raise ReportValidationError(f"FPR doesn't match: calculated={fpr}, reported={report['enforcement']['false_positive_rate']}")
        
        return True
```

---

## API Endpoint

### Public Access (No Auth)

```
GET /api/public/v1/reports?period=2026-06&format=json

Response:
{
  "period": "2026-06",
  "generated_at": "2026-07-01T00:00:00Z",
  ...
}

GET /api/public/v1/reports/annual?year=2026&format=pdf
Response: [PDF binary]

GET /api/public/v1/reports/annual?year=2026&format=json
Response: {...all 8 sections...}
```

---

## Scheduled Jobs

### Daily Job (Midnight UTC)

**Trigger:** Kubernetes CronJob

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: daily-report-check
spec:
  schedule: "0 0 * * *"  # Every day at midnight UTC
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: report-generator
            image: civicmesh/backend:latest
            command:
            - python
            - -m
            - backend.jobs.daily_consistency_check
            env:
            - name: REPORT_PERIOD
              value: "TODAY"
```

**What it does:**
- Checks if any critical field is NULL
- Verifies data consistency
- Logs warnings if issues found

### Monthly Job (1st of month)

```yaml
schedule: "0 0 1 * *"  # Every month on the 1st
command: [python, -m, backend.jobs.monthly_report_generation]
```

**What it does:**
- Generates monthly JSON report
- Publishes to public API
- Commits to GitHub archive repo
- Emails stakeholders

### Annual Job (Dec 31)

```yaml
schedule: "0 0 31 12 *"  # Dec 31 at midnight
command: [python, -m, backend.jobs.annual_report_generation]
```

**What it does:**
- Generates full annual JSON + PDF
- Sends to independent auditor for verification
- Publishes after auditor sign-off
- Archives in git (commits with GPG signature)

---

## Test Suite

**Location:** `tests/integration/test_report_generation.py`

```python
def test_monthly_report_all_fields_populated():
    """Every mandatory field must have a value"""
    report = generate_monthly_report("2026-06")
    assert report['period'] == "2026-06"
    assert report['network']['active_nodes'] > 0
    assert len(report['network']['violations']) > 0
    assert report['enforcement']['citations_issued'] >= 0
    # ... check all fields
    assert None not in flatten(report)  # No NULL values anywhere

def test_monthly_report_revenue_math():
    """Revenue breakdown must sum correctly"""
    report = generate_monthly_report("2026-06")
    shares = [
        report['revenue']['municipal_share'],
        report['revenue']['platform_fee'],
        report['revenue']['ccsc_patronage'],
        report['revenue']['operator_rewards']
    ]
    total = sum(shares)
    assert abs(total - report['revenue']['gross_total']) < 0.01

def test_monthly_report_fpr_calculation():
    """FPR must match data"""
    report = generate_monthly_report("2026-06")
    issued = report['enforcement']['citations_issued']
    dismissed = report['enforcement']['citations_dismissed']
    expected_fpr = dismissed / (issued + dismissed)
    assert abs(expected_fpr - report['enforcement']['false_positive_rate']) < 0.001

def test_annual_report_no_nulls():
    """Annual report has all 8 sections, no missing fields"""
    report = generate_annual_report(2026)
    assert report['network'] is not None
    assert report['enforcement'] is not None
    assert report['law_enforcement'] is not None
    assert report['ai_models'] is not None
    assert report['privacy_compliance'] is not None
    assert report['revenue_distribution'] is not None
    assert report['governance'] is not None
    assert report['corrective_actions'] is not None
    # All fields non-null
    assert validate_report(report) is True

def test_report_validation_rejects_null_fields():
    """Validation throws if any field is NULL"""
    report = generate_monthly_report("2026-06")
    report['enforcement']['citations_issued'] = None
    with pytest.raises(ReportValidationError):
        validate_report(report)

def test_pdf_rendering_matches_json():
    """PDF content matches JSON source"""
    json_report = generate_annual_report(2026)
    pdf_report = render_pdf(json_report)
    # Extract text from PDF, verify key numbers match JSON
    assert "12,847" in pdf_report  # violations count
    assert "11,203" in pdf_report  # citations issued
```

---

## Public Dashboard Integration

Reports feed data to the public transparency dashboard:

```
civicmesh.app/transparency
├─ Real-time stats (updated every 15 min from operational DB)
├─ "View monthly report (JSON)" link → `/api/public/v1/reports?period=2026-06`
├─ "View annual report (PDF)" link → `/api/public/v1/reports/annual?year=2026`
├─ "Download as CSV" → exports current dashboard data
└─ "Archive of all reports" → `/reports/`
```

---

## Success Criteria

- ✅ Monthly report auto-generated on 1st of month
- ✅ Annual report auto-generated on Dec 31st
- ✅ All mandatory fields always populated
- ✅ Validation prevents NULL fields (database + code)
- ✅ Revenue math verified in code
- ✅ Public API endpoint serves reports
- ✅ PDF rendering matches JSON source
- ✅ Reports committed to GitHub (archival, audit trail)
- ✅ Test coverage: all validation paths tested
- ✅ No manual editing possible (auto-generated only)

---

Next: See [[02_AUDIT_FRAMEWORK.md]] for community audit rights implementation.

