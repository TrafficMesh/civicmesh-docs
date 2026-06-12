# OPS-CS-001 CommunityShield Community Manager SOP v1

**OPS-CS-001**
**D-CENTRAL GROUP**
**D-Central Group / CivicMesh Inc.**
CommunityShield Community Manager SOP


| Document ID | OPS-CS-001 |
|---|---|
| Version | 1.0 |
| Status | Draft |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Operations |
| Applies To | HOA boards, condo property managers, campus security managers, business park operators |
| Related Documents | DC-CS-ARCH-001 | DC-CM-APP-009 | DC-CS-FED-001 | LEGAL-CM-012 |


# 1. Daily Dashboard Routine
Recommended 10-minute daily review. Most community managers will see quiet dashboards most days — the system is designed to surface what needs attention rather than requiring constant monitoring.


| Check | Time | What to Look For | Action if Needed |
|---|---|---|---|
| Node health | 2 min | All nodes active (green). Any offline nodes. | If a node has been offline >24 hours: contact the node owner or check if installation issue. Report persistent offline nodes to MSSP NOC. |
| Incident queue | 5 min | New incidents requiring manager review (private property enforcement). | Review each incident clip, confirm or dismiss. Send notification to registered violating vehicle owner where applicable. |
| Member notifications | 2 min | Any new member requests, consent changes, opt-out requests. | Process member requests within 48 hours. Escalate unusual requests to MSSP NOC. |
| Revenue summary | 1 min | Monthly revenue total and CCSC distribution status. | No action needed unless distribution appears incorrect — contact MSSP NOC if amounts don't reconcile. |


# 2. Incident Queue Management


| Incident Type | Review Standard | Notification to Vehicle Owner | Documentation |
|---|---|---|---|
| Parking violation — private property (reserved spot, visitor parking exceeded) | View full clip. Confirm vehicle is parked in violation. Confirm violation type (reserved spot, no permit, etc.). Check community member directory — is this a registered visitor? | If registered violation: send notice to vehicle owner via portal (auto-drafts from plate read if owner is in member directory). If unregistered: note-only, no formal notice. | All confirmations logged with community manager DID + timestamp. |
| Trespassing / unauthorized vehicle | View full clip. Confirm vehicle is unauthorized. Cross-reference member directory. | Send notice if owner identified. If not identified: dispatch private security response via Guard Cooperative integration. | Escalate to MSSP NOC if security response required. |
| Vandalism / property damage detection | View clip. Confirm incident. Preserve footage — do NOT dismiss. | Contact affected property owner. Consider formal police report — use law enforcement request process. | Preserve clip with 'DO NOT AUTO-DELETE' flag in Manager Dashboard. Copy to secure backup. |
| Acoustic event (MeshEar) — if deployed | Review acoustic event classification and 10-second clip. Confirm or dismiss based on context. | Dispatch private security for confirmed medium-severity events. Call 911 for confirmed high-severity (gunshot, explosion). | All MeshEar confirmations logged. High-severity dispatches generate automatic audit log entry. |


# 3. Law Enforcement Request Handling
When police or other law enforcement contacts the community manager directly (rather than through the formal portal process), the community manager must redirect them to the formal process. Do not share footage informally.


| If Law Enforcement Contacts You Directly
• Politely confirm: 'Our community uses the CivicMesh platform. All law enforcement data requests must be submitted through the formal portal at civicmesh.ca/enforcement-request with a case number and legal authority documentation.'
• Do NOT share footage, clips, or any data informally — regardless of urgency. Urgent requests with legal authority can be processed through the portal in under 4 hours.
• Do NOT confirm or deny whether specific footage exists before a formal request is submitted.
• Log the informal contact in the Manager Dashboard → Law Enforcement Log → Informal Contact. Date, officer name/badge (if provided), agency, request description.
• Contact your MSSP NOC immediately if you feel pressured to share data outside the formal process. |
|---|


# 4. Member Consent and Privacy Management


| Situation | Manager Action | Documentation |
|---|---|---|
| New resident wants to connect their doorbell to community network | Direct to CivicMesh Member App — self-service enrollment. Manager approves in Manager Dashboard after resident completes opt-in agreement. | Enrollment logged with resident DID + manager DID approval. |
| Resident wants to opt out of all data sharing | Resident can opt out entirely in Member App. No manager action required. Inform resident that their node will be deactivated and they will no longer receive patronage distributions. | Opt-out logged automatically. Manager notified via dashboard. |
| Resident requests copy of their data | Direct to Member App → Privacy → Download My Data. Data export fulfilled automatically within 30 days. | Request logged. Confirm completion to resident within 30 days. |
| Resident requests data deletion | Direct to Member App → Privacy → Delete My Data. Manager has no ability to override a deletion request. | Deletion request logged. MSSP NOC receives deletion instruction. Completion confirmed within 30 days. |
| Privacy complaint from resident | Log in Manager Dashboard → Privacy Complaints. Acknowledge to resident within 5 business days. Investigate and respond within 30 days. | All complaints and resolutions logged. Included in annual transparency report. |


# 5. Annual Transparency Report Publication
- Manager Dashboard → Transparency Report → Generate [Year] Report
- Review auto-generated report. Check all numbers against your expectations: node count, incident volume, federation events. Flag any discrepancies to MSSP NOC before approving.
- Approve report with your DID signature (biometric authentication in Manager Dashboard).
- Report is automatically published to community URL and distributed to all CCSC members via email/notification.
- Print and post a summary notice on community bulletin board: 'Annual Safety Network Report available at [URL]. Key stats: [X] nodes active, [$Y] returned to community members.'
- Present at Annual General Meeting — the Manager Dashboard generates an AGM presentation slide deck automatically from the report data.
# Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

