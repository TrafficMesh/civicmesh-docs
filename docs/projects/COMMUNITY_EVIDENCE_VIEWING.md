# Community Evidence Viewing & Privacy-Preserving Verification

**Enable communities to verify enforcement fairness without exposing personally identifiable information.**

---

## Problem Statement

The community has a democratic right to see that enforcement is being done fairly. But raw evidence packages contain sensitive PII:
- License plates (identification)
- Faces (privacy + bias detection vector)
- Officer identities (safety concerns)
- Exact GPS coordinates (home/work location inference)

CivicMesh solves this by publishing redacted evidence: All PII removed, violation facts preserved, independently verifiable.

---

## Redaction Pipeline

### Raw Evidence Package (Private)

Every citation starts with a complete evidence package stored in encrypted central database:

```
Raw Incident CM-2026-00421:
├─ Video (5-10 sec): Faces visible, plate readable, GPS tagged
├─ Audio: Officer radio traffic (not stored long-term)
├─ Telematics: Full OBD (RPM, fuel, throttle, engine codes)
├─ ALPR result: "ABC1234" (exact plate string)
├─ Officer: "Badge #4521, Officer John Smith"
├─ Location: GPS 43.6629, -79.3960 (exact coordinates)
├─ Timestamp: 2026-06-04 14:32:15.847 UTC (exact)
└─ Case status: [active legal case, sealed records, etc.]
```

**Access:** Only authorized (officer, auditor, prosecutor with warrant)

### Redaction Engine (Automatic)

```python
def redact_for_community(raw_evidence):
    """Remove all PII while preserving violation facts."""
    
    # Video redaction (on-device, before transmission)
    redacted_video = apply_face_blur(raw_evidence.video)  # Gaussian blur, <99% recognizable
    redacted_video = pixelate_plate(redacted_video)       # 8x8 pixel blocks
    
    # Telematics redaction
    telematics = {
        'speed_kmh': raw_evidence.obd.speed,              # Keep (violation relevant)
        'speed_source': 'OBD verified',                    # Keep (transparency)
        # NOT: RPM, fuel level, throttle, engine codes (irrelevant)
    }
    
    # ALPR redaction
    alpr = {
        'status': 'PLATE_DETECTED',                        # Keep (shows detection worked)
        'confidence': raw_evidence.alpr.confidence,         # Keep (accuracy metric)
        'plate_string': None,                              # REMOVE (no PII needed)
        'province': None,                                  # REMOVE (combined with other data = identification)
    }
    
    # Officer redaction
    officer = {
        'badge_number': hash_badge(raw_evidence.officer.badge),  # Anonymized, consistent per month
        'name': None,                                      # REMOVE
        'photo': None,                                     # REMOVE
    }
    
    # Location redaction
    location = {
        'street_name': reverse_geocode(raw_evidence.gps),  # "King St, Toronto"
        'district': 'Downtown',                             # Neighborhood only
        'coordinates': None,                                # REMOVE (privacy)
        'exact_address': None,                              # REMOVE (privacy)
    }
    
    # Timestamp redaction
    timestamp = {
        'date': raw_evidence.timestamp.date(),             # "2026-06-04"
        'hour': raw_evidence.timestamp.hour(),             # "14:00" (hour resolution)
        'minute': None,                                    # REMOVE (too specific)
        'second': None,                                    # REMOVE (too specific)
    }
    
    # Violation facts (KEEP ALL)
    violation = {
        'type': raw_evidence.violation_type,               # "Speeding"
        'speed_detected': raw_evidence.speed_detected,     # 67 km/h
        'speed_limit': raw_evidence.speed_limit,           # 50 km/h
        'confidence': raw_evidence.ml_confidence,          # 97%
        'zone': raw_evidence.zone_type,                    # "School zone"
    }
    
    # Outcome (KEEP ALL)
    outcome = {
        'issued': raw_evidence.citation_issued,            # true/false
        'dismissed_reason': raw_evidence.dismissal_reason,  # "Confidence <95%"
        'appeal_status': raw_evidence.appeal_status,        # "appealed", "upheld", etc.
    }
    
    return {
        'video': redacted_video,
        'telematics': telematics,
        'alpr': alpr,
        'officer': officer,
        'location': location,
        'timestamp': timestamp,
        'violation': violation,
        'outcome': outcome,
        'redaction_version': 'v1.2',
        'redaction_applied_at': utc_now(),
    }
```

### Community Evidence Package (Published)

```
Redacted Incident CM-2026-00421:
├─ Video: Faces blurred, plate pixelated, location not visible
├─ Violation: Speeding, 67 km/h in 50 km/h zone (school)
├─ Confidence: 97% (AI model version ALPR v2.3.1)
├─ Location: King St, Toronto (street name only, not coordinates)
├─ Officer: Officer #[ANON-7521] (consistent per month, cannot be de-anonymized)
├─ Timestamp: June 4, 2026, 14:00 (hour resolution, no minutes)
├─ Outcome: Citation issued
├─ Telematics: Speed 67 km/h (OBD verified), no other details
└─ Redaction: Applied by FaceBlur v1.2 + PixelateAlgorithm v2.1
```

**Access:** Public (anyone, no auth required)

---

## Community Evidence Viewing Interface

### Web Interface: civicmesh.app/community/evidence

```
┌─ CivicMesh Community Evidence Explorer ─────────────────────┐
│                                                               │
│ Filter & Search                                              │
│ ┌─────────────────────────────────────────────────────────┐  │
│ │ Violation Type: [Speeding ▼]                            │  │
│ │ Zone: [All ▼]                                           │  │
│ │ Month: [June 2026 ▼]                                    │  │
│ │ Search: [_____________________]                          │  │
│ │ [Apply Filters]                                          │  │
│ └─────────────────────────────────────────────────────────┘  │
│                                                               │
│ Results: Showing 1-20 of 11,203 incidents this month       │
│                                                               │
│ ┌─ Incident #CM-2026-00421 ───────────────────────────────┐  │
│ │ Violation: Speeding (67 km/h in 50 km/h school zone)    │  │
│ │ Location: King St, Toronto | Date: June 4, 2026, 14:00  │  │
│ │ Confidence: 97% (ALPR v2.3.1) | Outcome: Issued         │  │
│ │ Officer: #[ANON-7521]                                    │  │
│ │                                                           │  │
│ │ [Watch Redacted Video ▶] [View Full Details] [API Link]  │  │
│ └─────────────────────────────────────────────────────────┘  │
│                                                               │
│ ┌─ Incident #CM-2026-00420 ───────────────────────────────┐  │
│ │ Violation: Lane violation (unsafe change)                │  │
│ │ Location: Bloor St, Toronto | Date: June 4, 2026, 13:00  │  │
│ │ Confidence: 86% | Outcome: Dismissed (low confidence)     │  │
│ │ Officer: #[ANON-7419]                                    │  │
│ │                                                           │  │
│ │ [Watch Redacted Video ▶] [View Full Details] [API Link]  │  │
│ └─────────────────────────────────────────────────────────┘  │
│                                                               │
│ [Previous Page] [1 2 3 ... 561] [Next Page]                 │
│                                                               │
│ [Export as CSV] [Access via API] [Share Results]             │
│                                                               │
└───────────────────────────────────────────────────────────────┘
```

### Video Playback (Redacted)

```
Video player (civicmesh.app/evidence/CM-2026-00421):
┌──────────────────────────────────────────────────┐
│ [Redacted Dashcam Video]                         │
│                                                   │
│ - Faces: Blurred (Gaussian blur, <1% recognizable)
│ - Plate: Pixelated (8x8 blocks, unreadable)     │
│ - Location tags: Removed (no GPS overlay)       │
│ - Video quality: 720p (downsampled for privacy) │
│                                                   │
│ Timeline: [6s clip from 14:32-14:38]            │
│ Speed overlay: "67 km/h (OBD)" displayed on screen │
│ [Play] ━━━━━━●━━━━━━━ [6s]                      │
│ [Download redacted clip] [Share link]            │
└──────────────────────────────────────────────────┘
```

### API Access (For Researchers)

```
GET /api/community/v1/evidence?
  violation_type=speeding&
  zone=school&
  month=202606&
  limit=100&
  offset=0

Response:
{
  "total": 11203,
  "incidents": [
    {
      "id": "CM-2026-00421",
      "violation_type": "speeding",
      "speed_detected": 67,
      "speed_limit": 50,
      "zone": "school",
      "confidence": 0.97,
      "location": "King St, Toronto",
      "timestamp": "2026-06-04T14:00:00Z",
      "outcome": "issued",
      "video_url": "https://civicmesh.app/evidence/CM-2026-00421/redacted.mp4",
      "redaction_version": "v1.2"
    },
    ...
  ]
}
```

---

## What Communities Can Verify

### 1. Enforcement is Actually Happening
```
Claim: "CivicMesh issued 11,203 citations this month"
Verification: Download all incidents, count them
Math: 11,203 = reported number ✓
```

### 2. False Positive Rate Matches Claims
```
Report claims: FPR 3.1%
Verification: Review redacted evidence for dismissed incidents
Math: 324 dismissed / 10,527 issued = 3.08% ✓
```

### 3. Dismissal Reasons Are Legitimate
```
Community observation: "Why were these dismissed?"
Verification: Filter for dismissed incidents, note reasons
Reason breakdown:
  - Confidence <95%: 200 cases
  - Model bug: 10 cases
  - Calibration error: 8 cases
  - Appeal successful: 106 cases
  = Legitimate pattern ✓
```

### 4. School/Residential Zone Enforcement is Active
```
Claim: "We enforce speed limits in school zones"
Verification: Filter for school zone violations
Result: 847 speeding citations in school zones this month ✓
```

### 5. No Discriminatory Enforcement Pattern
```
Claim: "We don't target specific vehicle types"
Verification: Group incidents by vehicle color (from video)
Result: Violation distribution matches overall traffic composition ✓
```

### 6. Officer Performance is Consistent
```
Community question: "Do some officers approve more citations than others?"
Verification: Group dismissed incidents by officer #[ANON]
Result: All officers have similar dismissal rate (~3%) ✓
```

---

## Future: Community Livestream Access

### Phase 2 (Future Roadmap)

**Certain enforcement activities can be livestreamed during active incidents:**

Example: Amber Alert response
- Police approves streaming during active search
- Same redaction pipeline applied in real-time
- Community can watch enforcement response as it happens
- Builds trust through transparency

**Redaction in Livestream:**
- Face blurring: Applied on-device before transmission
- Plate pixelation: Applied to all video output
- Location: "Downtown Toronto" shown, not GPS coordinates
- Officer identity: Badge # only (anonymized)
- Radio traffic: Not transmitted (sensitive operational details)

**Example Livestream:**
```
civicmesh.app/live/amber-alert-2026-06-04

"AMBER ALERT: Missing child, blue Honda CR-V, ABC1234
Live response from 3 enforcement vehicles

[Watch live stream] [Redacted dashcam views from all 3 vehicles]
[Location: Downtown Toronto area]
[Status: Vehicle located - child safe]

Thank you to community for assistance
Total viewers: 23,400
Community tip led to recovery"
```

---

## Future: Zero-Knowledge Proofs

### Phase 3 (Long-Term Vision)

**Prove "This citation is valid" without revealing any PII:**

```
ZK-SNARK Proof:
Claim: "Vehicle traveling at 67 km/h in 50 km/h zone 
        was detected with 97% confidence"

Proof provides:
✓ Speed detected: 67 km/h (verifiable)
✓ Speed limit: 50 km/h (verifiable from geofence)
✓ ML confidence: 97% (verifiable)
✓ Violation occurred (verifiable)

Proof does NOT reveal:
✗ License plate
✗ Vehicle identity
✗ Driver identity
✗ Exact location
✗ Officer identity
✗ Any other PII

Community member can verify: "This citation is valid"
Without exposing: Any personally identifiable information
```

**Technology:** Circom + SnarkJS or Noir

**Benefit:** Maximum transparency + maximum privacy simultaneously

---

## Privacy Safeguards in Practice

### What Stays Private (Never Published)

✗ License plates (prevents vehicle identification)
✗ Faces (prevents driver identification)
✗ GPS coordinates (prevents home/work/routine inference)
✗ Officer full name (prevents targeting, harassment)
✗ Civilian PII (protects informants)
✗ Active investigation details (prevents interference)

### What is Published (For Verification)

✅ Violation type + facts (speeding: 67 in 50 zone)
✅ Confidence score (97%, verifiable with model card)
✅ Outcome (issued / dismissed)
✅ Zone type (school, residential, etc.)
✅ Street name + district (location without precision)
✅ Date + hour (timestamp without minutes)
✅ Anonymized officer ID (consistent within month)

### Redaction Reliability

**Threat:** Can faces be de-blurred?
- Use aggressive blurring (Gaussian, >50 pixel radius)
- Test for recognizability by humans (<1% can identify)
- Monitor academic research on de-blurring (adapt if needed)

**Threat:** Can pixelated plates be reconstructed?
- Use 8×8 pixel blocks (hard to recover without model training on the exact variant)
- Randomize offset per video (prevent pattern matching)
- Monitor attack research

**Threat:** Can "street name + hour" re-identify someone?
- Acceptable: Person might infer "This was probably near my house"
- Not acceptable: Exact address (removed)
- Not acceptable: Exact time (hour resolution only)

---

## Integration with Governance

- **Transparency layer:** Community evidence viewing is one of 5 layers
- **Annual report:** Evidence statistics (incident counts, dismissal rates) published
- **Community audit:** Auditors can access full evidence (with PII) for verification
- **Governance (RFC):** Community can propose changes to redaction rules via RFC

---

## Summary

**Communities see enforcement in action without compromising privacy:**
- Redacted evidence viewable by anyone (no login)
- PII automatically removed (faces, plates, coordinates)
- Violation facts preserved (speed, confidence, outcome)
- Independently verifiable (false positive rate, dismissal reasons)
- Future: Livestreams (real-time transparency during active incidents)
- Future: ZK-proofs (prove validity without revealing any PII)

---

Next: See [[TRANSPARENCY_LAYER.md]] for how all transparency mechanisms fit together.

