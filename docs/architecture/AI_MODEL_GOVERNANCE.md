# AI Model Governance Framework

**Transparent, community-auditable governance for all machine learning models used in enforcement.**

---

## Problem Statement

AI models (ALPR, lane detection, speed estimation, object detection) are central to CivicMesh enforcement decisions. A single biased model or incorrect calibration can cause thousands of incorrect citations. Yet most surveillance systems treat models as black boxes — even regulators cannot inspect them.

CivicMesh applies the same transparency principle to AI: every model is publicly documented, falsifiable, and auditable.

---

## Model Inventory

### Deployed Models (As of June 2026)

| Model | Purpose | Input | Output | Deployed | Version |
|-------|---------|-------|--------|----------|---------|
| **ALPR** | License plate recognition | Image frame | Plate string, confidence | Bus #456, Van #123, ... | 2.3.1 |
| **Speed Estimator** | Doppler velocity + optical flow | Radar + frame | Speed (km/h), confidence | All nodes | 1.9 |
| **Lane Detection** | Lane boundary segmentation | Image frame | Pixel mask + confidence | All nodes | 3.1 |
| **Object Detection** | Vehicle, pedestrian, sign, bike | Image frame | Bounding boxes + classes | All nodes | 4.2 |

---

## Model Cards (Public Specification)

Every model has a public model card. These are living documents, updated whenever the model changes.

### Model Card: ALPR v2.3.1

**Purpose:** Detect and read license plates from dashcam frames

**Training Data:**
- 500K images from North American vehicles
- Plates: Ontario, Quebec, federal, US (limited)
- Lighting: Day, night, overcast, rain, snow
- Angles: 0°–45° (not profile views)
- Sources: Public datasets (CCPD, OpenALPR, internal field data)
- Bias audit: <1% error rate variance across demographic plate distributions (non-applicable: plates are not demographic)

**Known Limitations:**
- Does NOT work: Heavily obscured plates (mud, tape, damage)
- Accuracy degrades: extreme angles (>45°), heavy rain, motion blur
- False positive rate: 1.8% (reads non-plates as plates)
- False negative rate: 2.3% (misses valid plates)
- Confidence threshold for enforcement: 95%+ (automatic right to contest if lower)

**Calibration:**
- Tested on 50K field images (OC Transpo buses, real conditions)
- Accuracy: 98.2% (plate correctly identified)
- Speed: <50ms per frame (GPU-accelerated)
- Retraining: Every 90 days or on FPR increase >2.5%

**Last Updated:** April 2026
**Retrained:** Yes (March 2026, +0.3% accuracy)
**Retraining Reason:** Dataset included winter conditions (snow, salt)

**Public Links:**
- [Model weights (HuggingFace)](https://huggingface.co/civicmesh/alpr-v2.3.1)
- [Training data split](https://data.civicmesh.ca/alpr-training-v2.3.1/)
- [Calibration report](https://reports.civicmesh.ca/alpr-calibration-q2-2026.pdf)
- [Appeal statistics](https://civicmesh.app/transparency/models/alpr/appeals)

**Community Feedback Channel:**
- GitHub Issues: [civicmesh/models/issues](https://github.com/civicmesh/models/issues)
- Tag: `model:alpr:v2.3.1`
- Response SLA: 5 business days

---

### Model Card Template (All Models Follow This)

```
# [Model Name] v[Version]

## Purpose
[One sentence: what does this model detect?]

## Training Data
- Dataset size: [N] images/samples
- Data sources: [where did data come from?]
- Temporal coverage: [date range]
- Geographic coverage: [regions, countries]
- Demographic bias audit: [results]

## Performance Metrics
- Accuracy: [%]
- False positive rate: [%]
- False negative rate: [%]
- Latency: [ms per inference]
- Hardware: [GPU/CPU/TPU]
- Inference framework: [TFLite, ONNX, etc.]

## Known Limitations
- [Limitation 1]: [specific failure mode]
- [Limitation 2]: [specific failure mode]
- Does NOT work: [clear failure cases]
- Accuracy degrades: [conditions where FPR increases]

## Calibration & Validation
- Field test size: [N] samples
- Field test accuracy: [%]
- Retraining schedule: [every N days or on FPR > X%]
- Last retrained: [date]
- Retraining reason: [what triggered update?]

## Versioning & History
- Version: [current version]
- Previous versions: [links to older cards]
- Known regressions: [if any]
- Breaking changes: [if any]

## Community Access
- Model weights: [link to public repo]
- Training data: [link or access process]
- Calibration reports: [annual reports]
- Appeal feedback: [link to community issue tracker]
```

---

## Retraining Governance

### When to Retrain

**Automatic triggers (no approval needed):**
- False positive rate increases >2.5% (from baseline)
- Field accuracy drops >1% (from calibration benchmark)
- New environmental conditions emerge (e.g., winter deployed in summer model)
- Quarterly refresh (every 90 days minimum, even if no issues)

**Requires TSC approval (RFC process):**
- Major version bump (e.g., v2.3 → v3.0)
- New training data source (external dataset)
- Significant architecture change (different model family)
- Removing a violation type from detection

### Retraining Process

```
1. Detection (automated)
   └─ Monitoring system detects FPR > threshold
   └─ Alert sent to ML team

2. Analysis (manual, <3 days)
   └─ Review recent failures
   └─ Identify root cause (dataset bias, distribution shift, etc.)
   └─ Propose retraining approach

3. Field Trial (30 days minimum)
   └─ New model deployed to 5–10 test nodes
   └─ Monitor FPR, latency, resource usage
   └─ Collect feedback from officers
   └─ Compare against current production model

4. Validation (automatic)
   └─ Field test accuracy must exceed production by >0.5%
   └─ Latency must not increase >10ms
   └─ FPR must not exceed previous version by >0.1%
   └─ If validation fails: abort, go back to step 2

5. Rollout (staged)
   └─ 10% fleet (25 nodes) for 1 week
   └─ Monitor for issues
   └─ If OK: 50% fleet for 1 week
   └─ If OK: 100% fleet

6. Rollback (if needed)
   └─ Previous model version preserved for 90 days
   └─ If critical bug discovered: immediate revert
   └─ Issue post-mortem

7. Documentation (public)
   └─ Update model card
   └─ Publish retraining report
   └─ Post on GitHub (link from model card)
   └─ Community notified via email
```

### Version Preservation

- **Current model:** In production, actively used
- **Previous 2 versions:** Preserved on edge devices (fallback if rollback needed)
- **Archive (>3 versions old):** Stored in cold storage, publicly accessible for research

---

## Community Appeal Process

### Appeal Grounds

A citation recipient can appeal if:
- **AI confidence <95%:** Automatic right (no grounds needed)
- **Model bug identified:** If can prove model error (e.g., "detected non-existent plate")
- **Calibration issue:** If can show model performance degraded at time of citation
- **Data bias:** If can show systematic bias against vehicle type/color/region

### Appeal Workflow

```
1. Citation issued with AI confidence score

2. Recipient receives notification
   └─ Email: "Your citation summary"
   └─ Link to video evidence (redacted)
   └─ Link to model card (at time of citation)
   └─ Appeal instructions

3. Appeal submission (officer portal)
   └─ Grounds: [choose from above]
   └─ Evidence: [optional video, documents]
   └─ Comment: [free text explanation]

4. Supervisor review (48 hours)
   └─ Check model card at citation time
   └─ If confidence <95%: uphold appeal (automatic)
   └─ If confidence ≥95%: human judgment
   └─ Decision: uphold, dismiss, or refer to community ombudsperson

5. Outcome notification
   └─ Email: Decision + reasoning
   └─ If upheld: Citation removed, record updated
   └─ If dismissed: Explanation of why

6. Escalation (optional)
   └─ If dissatisfied: escalate to community ombudsperson
   └─ Independent review by third party
   └─ Final decision (binding)

7. Community feedback
   └─ All appeals logged (anonymized)
   └─ Aggregate outcomes published monthly
   └─ "X appeals this month: Y upheld, Z dismissed"
```

### Appeal Statistics (Monthly Public Report)

```
ALPR Model Appeals (June 2026):
├─ Total appeals: 47
├─ Grounds:
│  ├─ Confidence <95%: 23 (auto-upheld)
│  ├─ Model bug claimed: 12 (0 confirmed bugs)
│  ├─ Calibration issue: 8 (2 found legitimate)
│  └─ Bias claim: 4 (0 confirmed)
├─ Outcome:
│  ├─ Upheld: 25 (53%)
│  ├─ Dismissed: 22 (47%)
│  └─ Escalated to ombudsperson: 3
└─ Avg. time to resolution: 2.3 days
```

---

## Bias Auditing

### Annual Bias Audit (Third-Party)

**Scope:** Each model tested for demographic/environmental bias

**Methodology:**
1. Stratify field data by dimensions (e.g., plate origin, vehicle color, time of day)
2. Measure false positive rate per stratum
3. Statistical test: Chi-square test for independence
4. If bias detected (p<0.05): recommend retraining or mitigation

**Example:** ALPR bias audit (April 2026)
```
Plate origin breakdown:
├─ Ontario plates: FPR 1.8%
├─ Quebec plates: FPR 1.9%
├─ US plates: FPR 2.1%
├─ Federal plates: FPR 2.0%
└─ Chi-square test: p=0.73 (no significant bias)

Time of day breakdown:
├─ Day (6am–6pm): FPR 1.6%
├─ Night (6pm–6am): FPR 2.4%
└─ Chi-square test: p=0.08 (marginal, monitor)
   → Recommendation: retrain with more night images
```

**Report publication:**
- Published at [civicmesh.ca/audits/models/](civicmesh.ca/audits/models/)
- Linked from model card
- If bias found: action plan published with timeline

---

## AI Error Analysis Pipeline

### When an Appeal is Upheld (Legitimate Model Error)

```
1. Error documented
   └─ Citation number
   └─ What model got wrong
   └─ Correct answer
   └─ Input data (image, radar)

2. Root cause analysis
   └─ Lighting condition?
   └─ Unusual object?
   └─ Data distribution not seen in training?
   └─ Model bug (inference code)?

3. Feedback to training pipeline
   └─ If training data gap: add to retraining dataset
   └─ If inference bug: patch code, redeploy
   └─ If architectural issue: flag for major retraining

4. Aggregate feedback report
   └─ Published monthly
   └─ "X errors this month, root causes:"
   └─ Trends tracked (e.g., winter darkness = 3 errors)

5. Retraining trigger
   └─ If error frequency exceeds threshold: trigger retraining RFC
```

---

## Model Transparency Guarantees

### What is Public

✅ Model weights (HuggingFace or equivalent)
✅ Training data (metadata + samples)
✅ Model card (performance, limitations, calibration)
✅ Retraining history (versions, reasons, dates)
✅ Appeal statistics (aggregate + trends)
✅ Bias audit results (annual, published)
✅ Error analysis (root causes aggregated)

### What is Private (For Good Reason)

✗ Personal data in training (redacted before public release)
✗ Active adversarial research (until patched)
✗ Pre-release model versions (until validated)

---

## Integration with Governance

- **RFC process (GOV-001):** Major model changes must go through RFC
- **Annual report (GOV-003):** Model performance metrics published
- **Community audit (GOV-002):** Auditors can inspect model training data
- **Transparency dashboard:** Model FPR + appeal rate shown real-time

---

## Summary

**AI models are not black boxes. Every model is:**
- Publicly documented (model cards)
- Community-auditable (weights + training data public)
- Appealable (community right to contest)
- Monitored (FPR tracked continuously)
- Improvable (error feedback → retraining)

---

Next: See [[COMMUNITY_EVIDENCE_VIEWING.md]] for how model decisions are explained to communities. See [[BLOCKCHAIN_FINANCIAL_TRANSPARENCY.md]] for how revenue from AI-driven enforcement is tracked transparently.

