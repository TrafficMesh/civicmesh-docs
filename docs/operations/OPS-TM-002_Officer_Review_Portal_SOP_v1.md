# OPS-TM-002 Officer Review Portal SOP v1

**OPS-TM-002**
**D-CENTRAL GROUP**
**D-Central Group / CivicMesh Inc.**
TrafficMesh Officer Review Portal SOP


| Document ID | OPS-TM-002 |
|---|---|
| Version | 1.0 |
| Status | Draft |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Operations |
| Applies To | Municipal bylaw officers, OC Transpo enforcement staff, authorized enforcement personnel |
| Related Documents | OS-PATROL-TM-EV-001 | DC-CM-APP-008 | OS-PATROL-TM-001 | REG-TM-001 |


# 1. Daily Review Workflow
Officers should review queued evidence packages at the start of each shift and at regular intervals throughout the shift. Target: no evidence package remains in Fast-Track Queue for more than 4 hours, Standard Review Queue for more than 24 hours.


| Queue Type | Target Review Time | Volume Expectation (OC Transpo Pilot) | Officer Action |
|---|---|---|---|
| Fast-Track Queue (confidence ≥0.90) | <4 hours from package generation | 5–20 packages per shift during active enforcement hours | Watch clip (minimum 3 seconds — timer locks Confirm button). Verify credential chain status (GREEN badge). Confirm violation type from dropdown. One-tap Confirm or Dismiss. |
| Standard Review Queue (confidence 0.70–0.89) | <24 hours | 2–8 packages per shift | Watch full clip. Manually confirm violation type. Verify or correct ALPR plate read. Add review notes (encouraged). Confirm or Dismiss with reason code. |
| Manual Review Queue (NOC-flagged) | <48 hours | Rare — <1 per week | Full review required. Supervisor countersignature mandatory. Notes required explaining why package was flagged. |


# 2. Credential Chain Verification
Every evidence package displays a credential chain status badge. Officers must not confirm any citation from a package with a RED badge. AMBER badges require officer to note the expiring credential in review notes.


| Badge | Meaning | Officer Action |
|---|---|---|
| 🟢 GREEN — Verified | Full credential chain verified on Polygon. Node is certified, installer was qualified, MSSP is authorized, no tamper events. | Proceed normally with review. |
| 🟡 AMBER — Expiring Soon | Chain is valid but one or more credentials expire within 30 days. | Note expiring credential in review notes. Proceed with citation — credential is still valid. NOC has been alerted to renew. |
| 🔴 RED — Verification Failed | Chain verification failed: signature mismatch, revoked credential, or tamper event detected. | Do NOT confirm citation. Dismiss package with reason code CREDENTIAL_FAILURE. Alert NOC immediately via portal message. |


# 3. Dismissal Reason Codes
All dismissals require a reason code. This data feeds directly into AI model retraining — accurate dismissal codes improve the model. Never use LOW_CONFIDENCE as a substitute for a specific reason.


| Code | Use When | Model Feedback Effect |
|---|---|---|
| LOW_CONFIDENCE | Footage is too unclear to make a determination — not a specific identifiable problem | Increases model uncertainty threshold for similar conditions |
| NOT_A_VIOLATION | AI classified as violation but reviewing the clip confirms it is not | Direct negative training signal for this violation type in these conditions |
| CLIP_UNCLEAR | Clip is technically defective — corrupted, too dark, blocked camera | Hardware issue flag sent to NOC — triggers camera inspection |
| DUPLICATE | Same vehicle and violation already cited in another package in the same queue | Deduplication improvement signal |
| EXEMPT_VEHICLE | Vehicle is an exempt class (emergency, transit, other authorized) | Adds to exempt vehicle classification training data |
| PLATE_UNREADABLE | Cannot confirm plate read to sufficient certainty for citation | ALPR model retrain signal for this condition |
| TECHNICAL_FAULT | Evidence package has a technical issue (timestamp mismatch, GPS anomaly) | NOC investigation trigger |
| CREDENTIAL_FAILURE | Credential chain verification failed — RED badge | Automatic P1 NOC alert |


# 4. Contested Citation Preparation
When a citation recipient contests through the provincial offence process, the officer may be required to provide supporting documentation. The following package is generated from the Officer Portal:
- Officer Portal → Citation History → [Citation Number] → Generate Legal Package
- Package contents auto-generated: evidence video clip (original, unmodified), GPS track overlay, timestamp and timezone documentation, ALPR confidence score and methodology note, violation type classification rationale, node credential chain (full — with Polygon transaction IDs), officer review record (DID signature timestamp, review duration, actions taken), model version and training data documentation reference.
- Package is cryptographically sealed — any modification after generation invalidates the package signature.
- Package delivered to officer and crown prosecutor via secure portal download. Defence counsel can verify the package authenticity independently at verify.civicmesh.ca/{citation-id}.
- Expert witness documentation request: if case proceeds to hearing and expert testimony on the AI model methodology is required, contact D-Central Forensics. Response time: 10 business days for written report, 30 days for in-person availability.
# 5. Officer DID Credential Management


| Credential expiry | Officer Authorization Credential expires annually. 90/60/30-day renewal reminders sent to officer and supervisor via portal notification. |
|---|---|
| Renewal process | Supervisor submits renewal request through municipal partner portal. D-Central Group verifies active employment status with municipal partner. New credential issued to officer's DID wallet. |
| Lost/compromised DID key | Officer reports immediately to supervisor and MSSP NOC. Old credential revoked on Polygon within 24 hours. New DID provisioned and new credential issued within 5 business days. |
| Officer departure | Supervisor submits credential revocation request immediately on officer departure from the role. MSSP NOC revokes credential on Polygon. All future evidence packages reviewed by this officer remain valid — revocation is not retroactive. |


# Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

