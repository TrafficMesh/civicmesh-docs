# DC-CM-GOV-001 Open Source Governance Model v1

**DC-CM-GOV-001**
**D-CENTRAL GROUP**
**CivicMesh Inc. / TrafficMesh Technologies Inc.**
CivicMesh Open Source Governance Model


| Document ID | DC-CM-GOV-001 |
|---|---|
| Version | 1.0 |
| Status | Draft |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Technical |
| SR&ED Eligibility | Minimal — governance document |
| Related Documents | LEGAL-CM-016 | DC-CM-VC-001 | DC-STRAT-005 |


# 1. Technical Steering Committee (TSC)


| Composition | D-Central Group (permanent seat, 2 votes), MSSP Cooperative elected representative (1 vote), CTC Training Cooperative elected representative (1 vote), Community contributor elected representative (1 vote, elected by GitHub contributor community annually) |
|---|---|
| Scope of Authority | Approve breaking changes to platform APIs, federation protocol, and VC schemas. Approve new module additions to the platform. Approve security disclosure responses. Review and approve annual open source roadmap. |
| Decision Making | Simple majority for minor changes. Supermajority (4/5) for breaking changes. D-Central veto power on security and privacy architecture changes only. |
| Meeting Cadence | Monthly video call (public notes published). Quarterly in-person (Ottawa preferred, remote option always available). |


# 2. RFC Process


| Stage | Description | Duration |
|---|---|---|
| Draft RFC | Contributor opens GitHub issue with RFC label. Describes proposed change, rationale, alternatives considered, breaking change assessment. | No minimum |
| Community Comment | RFC open for community comments. D-Central or TSC member assigned as RFC shepherd. | Minimum 14 days (28 days for breaking changes) |
| TSC Review | TSC reviews RFC and comments at next monthly meeting. Requests changes, asks clarifying questions, or schedules vote. | At next monthly meeting |
| TSC Vote | Approve / Request Changes / Reject. Rejection requires written rationale. Approved RFC assigned to milestone. | At TSC meeting |
| Implementation | RFC implementor (proposer or community volunteer) submits PR. Code review by D-Central engineering. CLA verified. | Per implementation timeline |
| Release | Merged to main branch. Included in next release. Schema version incremented if applicable. | Per release cycle |


# 3. Security Disclosure Policy


| Responsible Disclosure | Security vulnerabilities should be reported to security@civicmesh.ca (PGP key published on civicmesh.ca/security). Do not disclose publicly until D-Central has had 90 days to remediate. |
|---|---|
| Response SLA | Acknowledgement within 24 hours. Severity classification within 48 hours. Fix timeline communicated within 7 days. Critical (CVSS ≥9.0): fix within 14 days. |
| Bounty Programme | D-Central maintains a responsible disclosure recognition programme. Bounties paid in D-Credit. Amounts based on CVSS severity and practical exploitability. |
| Public Disclosure | On remediation: CVE filed (if applicable), security advisory published on GitHub, affected communities notified via NOC dashboard. |


# Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

