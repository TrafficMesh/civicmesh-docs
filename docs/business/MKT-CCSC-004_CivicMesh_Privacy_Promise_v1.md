# MKT-CCSC-004 CivicMesh Privacy Promise v1

**MKT-CCSC-004**
**D-CENTRAL GROUP**
**D-Central Group / CivicMesh Inc.**
CivicMesh Privacy Promise


| Document ID | MKT-CCSC-004 |
|---|---|
| Version | 1.0 |
| Status | Draft |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Marketing |
| Format | Public document — plain language, published openly at civicmesh.ca/privacy |
| Purpose | The public-facing privacy commitment — must be impeccable and honest |
| Related Documents | DC-CM-PRV-001 | REG-CM-001 | LEGAL-CM-013 | DC-CM-GOV-003 |




| Our Privacy Promise — Plain Language
• WE COLLECT: Short video clips when our AI model detects a potential road safety violation. GPS coordinates of the capture location. A timestamp. The AI's confidence score. A hash (scrambled version) of the license plate of vehicles involved.
• WE DO NOT COLLECT: Continuous video footage. Your face or anyone else's face in an identifiable form — faces are blurred on the camera before any footage leaves the device. Your driving route for advertising purposes. Any data you haven't specifically consented to share.
• YOUR DATA BELONGS TO YOU: You can download everything we hold about you, anytime. You can correct any inaccurate data. You can delete everything and leave the network — no penalty, no minimum commitment.
• LAW ENFORCEMENT: Police and government agencies can only access your data through a formal written request with documented legal authority. Every such request is logged publicly (case number visible, case details protected). You are notified within 48 hours of any access to your data.
• WHO HAS ACCESS: Your community manager, for safety events in your community. Officer review portal users (municipal bylaw officers), for enforcement evidence. Our technical team, for platform operations — they see metadata, not your footage content.
• WE WILL NEVER: Sell your data. Share it with advertisers. Connect it to a national private database. Share it with immigration authorities without a court order. Use it to build a profile of your daily movements. |
|---|


# 1. Technical Privacy Controls


| Privacy Feature | Technical Implementation | Independently Verifiable? |
|---|---|---|
| On-device face blurring | Face detection model runs on the camera hardware. GaussianBlur applied to all detected faces before any frame is transmitted or stored off-device. | Yes — open source firmware code published. Anyone can verify the blurring runs before transmission. |
| Hash-before-store ALPR | License plates are converted to SHA-256 hashes before storage. The original plate is held in device memory only during the capture event — never written to storage or transmitted in plaintext. | Yes — open source code shows the hashing pipeline. |
| No continuous recording | Non-violation frames are discarded on the device. The camera processes every frame but only stores a clip when an AI model detects a violation candidate. | Yes — open source firmware confirms the discard logic. |
| Data minimization | Only the minimum data necessary for the specific enforcement purpose is included in each evidence package. | Yes — evidence package schema is publicly documented (DC-CM-ARCH-001). |
| PIPEDA compliance | Privacy Impact Assessment completed and available on request. Individual access, correction, and deletion rights technically implemented in Member App. | PIA available on request. Member App access functions can be demonstrated. |


# 2. Your Rights — How to Exercise Them


| See your data | Member App → Settings → Privacy → Download My Data. Fulfilled within 30 days. |
|---|---|
| Correct your data | Email privacy@civicmesh.ca. Corrections fulfilled within 30 days. |
| Delete your data | Member App → Settings → Privacy → Delete My Account and Data. Deleted within 30 days (except data required by law or active law enforcement hold). |
| See who accessed your data | Member App → Privacy → Data Access History. Shows every time your data was accessed, by whom (role, not name), for what purpose. |
| File a complaint | If you believe your privacy rights have been violated: email privacy@civicmesh.ca first. If unsatisfied: file with the Office of the Privacy Commissioner of Canada (priv.gc.ca) or your provincial privacy commissioner. |


# Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

