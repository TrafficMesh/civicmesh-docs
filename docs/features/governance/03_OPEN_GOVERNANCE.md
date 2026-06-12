# Feature: Open Governance Model

**Transparent decision-making: RFC process, TSC voting, public meetings, contributor roles.**

---

## Requirements

### 1. RFC (Request for Comments) Process

**RFC = Formal proposal for any feature, config, or breaking change**

**Submission:**
1. Create file in `rfcs/in-progress/YYYYMMDD-title.md`
2. Open PR to `main` branch
3. CI enforces 14-day minimum comment period (blocks merge until deadline passes)
4. Community comments on PR (GitHub issues/discussion)
5. Author revises based on feedback
6. After 14 days: TSC votes (supermajority = 4/5 required)

**RFC Template:**

```markdown
# RFC-YYYYMMDD: [Title]

## Summary
One paragraph explaining what this RFC proposes.

## Motivation
Why is this change needed? What problem does it solve?

## Detailed Design
How will this work? What are the implementation details?

## Drawbacks
What are the downsides of this approach?

## Alternatives
What other approaches were considered? Why rejected?

## Impact
- Who is affected? (e.g., "users", "operators", "auditors")
- What existing code/promises does this change? (e.g., "backward incompatible")
- Timeline for implementation?

## Open Questions
(Address during review period)
```

**Examples:**
- `rfcs/accepted/20260101-blockchain-ledger.md` — Accepted
- `rfcs/rejected/20260215-cloud-storage.md` — Rejected
- `rfcs/in-progress/20260601-model-versioning.md` — Under review (14-day window)

### 2. TSC (Technical Steering Committee) Voting

**TSC Composition:** 5 senior contributors (chosen by community)

**Voting Rules:**
- **Standard change:** Majority (3/5) to approve
- **Breaking change:** Supermajority (4/5) to approve
- **Breaking + security/privacy impact:** Requires D-Central approval (can override TSC)
- **All votes public** (GitHub reactions or dedicated voting tool)

**Voting deadline:** RFC comment period closes, vote happens within 48 hours

**Outcome:**
- Approved: RFC merged, move to `rfcs/accepted/`
- Rejected: RFC moved to `rfcs/rejected/`, issue pinned with explanation

**Example voting record:**

```
RFC: Add blockchain ledger for revenue transparency
Date: 2026-06-01
Votes:
  ✅ Alice (contributor)
  ✅ Bob (contributor)
  ✅ Charlie (TSC member)
  ❌ Diana (TSC member) — "Concerned about Polygon centralization"
  ✅ Eve (TSC member)

Result: 4/5 supermajority → APPROVED
Implementation deadline: Aug 1, 2026
```

### 3. TSC Meetings (Public)

**When:** Monthly on 2nd Wednesday at 7pm UTC (example)

**Where:** Zoom (link posted in `docs/governance/meeting-schedule.md`)

**Agenda:**
1. Review open RFCs
2. Discuss outstanding issues
3. Approve/reject major changes
4. Announce contributor role changes
5. Q&A from community

**Outcome:** Minutes published within 48 hours

**Example meeting notes:**

```markdown
# TSC Meeting: June 2026

Attendees: Alice, Bob, Charlie, Diana, Eve + 30 community members

## Agenda

### 1. RFC-20260601 (Blockchain Ledger)
- Status: Approved 4/5 (Diana opposed, detailed reasons noted below)
- Implementation timeline: 60 days
- Assigned to: Team Alpha

### 2. Issue #847 (False positives in school zones)
- Discussed: FPR 4.1% exceeds 3% target
- Decision: Trigger model retraining RFC
- Responsible party: ML team
- Timeline: 30-day field trial

### 3. Contributor role changes
- Approved: Bob → Committer (merged 12 PRs, all high quality)
- Proposed: Alice → TSC (nominated by 3 contributors, 5/5 vote approval needed next month)

### 4. Community Q&A
- Q: Why was RFC-20260215 rejected? A: [linked to decision doc]
- Q: When will community livestream be available? A: Phase 2, tentatively Oct 2026

## Next Meeting
July 11, 2026, 7pm UTC
Zoom: [link]
```

---

## Architecture

### RFC Repository Structure

```
rfcs/
├─ README.md                    # Instructions for RFC process
├─ TEMPLATE.md                  # RFC template
├─ in-progress/
│  ├─ 20260601-blockchain.md
│  └─ 20260610-model-versioning.md
├─ accepted/
│  ├─ 20260501-transparency-layer.md
│  └─ 20260515-audit-framework.md
├─ rejected/
│  ├─ 20260215-cloud-storage.md
│  └─ 20260301-centralized-auth.md
└─ implemented/
   ├─ 20260101-edge-ai.md
   └─ 20260201-audit-logs.md
```

### CI Enforcement of 14-Day Comment Window

**File:** `.github/workflows/rfc_validation.yml`

```yaml
name: RFC Validation

on:
  pull_request:
    paths:
      - 'rfcs/in-progress/**'

jobs:
  enforce_comment_period:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Check RFC minimum comment period
        run: |
          RFC_FILE=$(git diff --name-only HEAD origin/main | grep rfcs/in-progress/)
          
          # Extract creation date from filename (YYYYMMDD-title.md)
          RFC_DATE=$(echo $RFC_FILE | sed 's/.*\/\([0-9]*\).*/\1/')
          RFC_DATETIME="${RFC_DATE}T00:00:00Z"
          
          # Compute deadline (14 days later)
          DEADLINE=$(date -d "$RFC_DATETIME + 14 days" -u +%s)
          NOW=$(date -u +%s)
          
          if [ $NOW -lt $DEADLINE ]; then
            DAYS_LEFT=$(( ($DEADLINE - $NOW) / 86400 ))
            echo "RFC comment period: $DAYS_LEFT days remaining"
            echo "Merge blocked until $(date -d @$DEADLINE)" 
            exit 1  # Block merge
          fi
          
          echo "Comment period complete. RFC is mergeable."
          exit 0
```

### TSC Voting Tool

**Option 1: GitHub Reactions (Built-in)**
```
RFC PR #42
Reactions: 👍👎👎👍👍

Parsing script: count reactions, determine majority
```

**Option 2: Dedicated Voting Tool (Ballot.io or similar)**
```
URL: https://voting.civicmesh.ca/rfc/20260601
Voters: Alice, Bob, Charlie, Diana, Eve
Results:
  ✅ 4 votes
  ❌ 1 vote
  => APPROVED
```

### Contributor Role Progression

**Progression path:**

```
Contributor → Committer → TSC member
```

**Contributor**
- Requirements: 1+ PR merged, passes code review
- Permissions: Create RFCs, comment on issues
- Example: "alice" on GitHub (no special permissions)

**Committer**
- Requirements: 5+ PRs merged, sustained quality, peer nomination
- Permissions: Merge PRs (with review), approve RFCs
- Example: Bob approved to merge without further review
- Grant: `committer` role in GitHub (add to CODEOWNERS)

**TSC Member**
- Requirements: 10+ PRs, demonstrable leadership/vision, 3/5 vote approval
- Permissions: Vote on RFCs, approve breaking changes, set quarterly roadmap
- Example: Alice becomes TSC member (5/5 approval)
- Grant: Add to `@civicmesh/tsc` GitHub team

**Nomination Process:**

1. Community proposes: "I nominate X for [role]"
2. Propose in GitHub issue (pinned, public)
3. Discussion period: 2 weeks
4. Vote by current TSC/Committers:
   - Contributor → Committer: 3/5 Committers
   - Committer → TSC: 4/5 TSC
5. Result announced at next monthly meeting

---

## Implementation

### RFC Submission Workflow

**Step 1: User creates PR**

```bash
git checkout -b rfc-blockchain-ledger
cp rfcs/TEMPLATE.md rfcs/in-progress/20260601-blockchain-ledger.md
# Edit RFC
git add rfcs/
git commit -m "RFC: Blockchain-based financial transparency"
git push origin rfc-blockchain-ledger
# Open PR on GitHub
```

**Step 2: GitHub Actions runs validation**

```
✅ RFC filename format valid (YYYYMMDD-*.md)
✅ RFC template structure valid (has all required sections)
⏳ Comment period: 14 days (merge blocked until June 15)
🔔 Notified: @civicmesh/community (email + discussion)
```

**Step 3: Community comments (14 days)**

```
- June 1: RFC posted
- June 1-5: 20 comments (questions, concerns)
- June 6-10: Author revises RFC based on feedback
- June 11-14: Final discussion
- June 15: Comment period closes
```

**Step 4: TSC votes (48 hours)**

```
- June 15: CI allows merge (deadline passed)
- June 15 (7pm UTC): TSC meeting, vote on RFC
- June 15: Reactions added by TSC members
  ✅ Alice
  ✅ Bob
  ❌ Charlie (commented: "Need more audit details")
  ✅ Diana
  ✅ Eve
- June 16: Vote count: 4/5 → APPROVED
- June 16: Move file to rfcs/accepted/, merge PR
```

**Step 5: Implementation**

```
- RFC marked as "accepted" in RFC folder
- Implementation issue created (assigned team)
- Feature branch created: feature/blockchain-ledger
- Implementation deadline: Aug 1, 2026
- Status tracked in FEATURE_INDEX.md
```

### Public Meeting Notes Template

**File:** `docs/governance/meetings/2026-06-meeting-notes.md`

```markdown
# TSC Meeting: June 11, 2026

**Date:** June 11, 2026, 7pm UTC
**Duration:** 90 minutes
**Attendees:** Alice (Chair), Bob, Charlie, Diana, Eve, + 27 community members
**Recording:** [YouTube link]
**Chat log:** [Archived in GitHub discussions]

## Agenda

### 1. Review Open RFCs (15 min)

**RFC-20260601: Blockchain-based financial transparency**
- Status: Comment period ends June 15
- Key feedback so far:
  - Support: Addresses "DeFlock Risk" (community validation)
  - Concerns: Polygon centralization (Charlie raised), long-term maintainability (Bob)
- Author response: Documented in RFC #42
- TSC preliminary vote: 4/5 likely to approve (pending final feedback)
- Timeline: 60 days to implementation if approved

**RFC-20260603: Community livestream API**
- Status: New, comment period just started (June 3)
- Summary: Real-time enforcement video access for high-profile incidents
- Key concern: Privacy (faces/plates must be blurred in real-time)
- Recommendation: Schedule deep-dive discussion (next month) before voting

### 2. Issue Triage (30 min)

**Issue #847: School zone false positives**
- Description: Speed estimator FPR 4.1% (exceeds 3% target)
- Severity: High (public safety claim at risk)
- Discussion:
  - Data suggests training data lacked winter conditions
  - Retraining RFC proposed for next month
  - Temporary mitigation: Lower confidence threshold to 98% (prevents issuing low-conf citations)
- Decision: Trigger retraining RFC (Bob will draft by June 20)

**Issue #923: Audit log performance**
- Description: Hash chain verification taking >30 seconds
- Impact: Blocks auditor access when dataset large
- Discussion: Optimize blake3 library version + caching strategy
- Owner: Charlie (assigned, will propose optimization PR by July 1)

### 3. Contributor Role Changes (10 min)

**Promotion: Bob → Committer**
- Merged PRs: 12 (all high quality)
- Code review feedback: Consistently thorough
- Recommendation: Grant committer role
- Vote: 5/5 unanimous approval ✅
- Effective date: Immediately
- Permissions granted: Merge PRs on approval from one reviewer

**Nomination: Alice → TSC Member**
- Merged PRs: 22 (core infrastructure)
- Community feedback: Strong leader, clear vision
- Nominator: Bob, 3 community seconders
- Vote scheduled: Next month (July meeting)
- Discussion: No concerns raised

### 4. Community Q&A (25 min)

**Q: When will citizen crowdsourcing launch?**
A: Phase 3, targeting July 2027. Currently gathering requirements. RFC expected Q4 2026.
[Link to roadmap](docs/PLATFORM_EXPANSION_ROADMAP.md)

**Q: Why was RFC-20260215 (centralized auth) rejected?**
A: Security risk: single point of failure. Proposal was to use proprietary Google auth instead of federated system. Community voted 1/5 (majority wanted to keep federation). Decision doc pinned [here](rfcs/rejected/20260215-centralized-auth.md#why-rejected).

**Q: How do we know model retraining is actually happening?**
A: Great question. All retraining events are logged in `backend/models/retraining_log.py` and published in annual transparency report. Additionally, every retraining requires an RFC (for major versions), so community can track it. Evidence: [AI_MODEL_GOVERNANCE.md](docs/AI_MODEL_GOVERNANCE.md#retraining-process).

**Q: Can we get more women on the TSC?**
A: Important point. Current TSC: 2/5 women. Nomination process is community-driven. Anyone can nominate, voting is by existing TSC/committers. We encourage nominations from underrepresented groups. Please suggest names!

### 5. Announcements (10 min)

- Security audit scheduled for July-August (external firm)
- New documentation: [OPEN_SOURCE_VERIFICATION_GUIDE.md](docs/OPEN_SOURCE_VERIFICATION_GUIDE.md) now published
- CivicMesh booth at Civic Tech Summit (July 15-17, booth #42)
- Hiring: Looking for 2 engineers (firmware + backend). Refer friends!

## Decisions Made

| Decision | Owner | Deadline |
|----------|-------|----------|
| Approve RFC-20260601 (blockchain ledger) | TSC vote June 15 | June 16 |
| Draft retraining RFC for speed estimator | Bob | June 20 |
| Optimize hash chain verification | Charlie | July 1 |
| Grant Bob committer role | Ops | Immediate |
| Schedule deep-dive: livestream API | Diana (moderator) | July 8 |

## Next Meeting

**Date:** July 9, 2026, 7pm UTC
**Location:** Zoom [link tbd]
**Agenda:** Livestream API deep-dive, model retraining RFC, election of new TSC member

## Recording & Materials

- Recording: [YouTube](https://youtube.com/watch?v=...)
- Chat log: [GitHub Discussions](https://github.com/civicmesh/civicmesh/discussions/...)
- Slides: [Google Drive](https://drive.google.com/...)

---

**Published:** June 12, 2026 by Alice
**Reviewed by:** Bob, Charlie
```

---

## Test Suite

**Location:** `tests/integration/test_governance.py`

```python
def test_rfc_comment_period_blocks_merge():
    """RFC cannot be merged before 14 days elapse"""
    pr = create_pr("RFC: New feature", file="rfcs/in-progress/20260601-test.md")
    
    # Attempt merge immediately
    result = try_merge(pr)
    assert result['blocked'] is True
    assert "14 days" in result['reason']

def test_rfc_merge_allowed_after_deadline():
    """RFC can be merged after 14-day comment period"""
    pr = create_pr("RFC: New feature", file="rfcs/in-progress/20260601-test.md")
    
    # Advance clock 14 days + 1 hour
    mock_time.advance(14*24*3600 + 3600)
    
    result = try_merge(pr)
    assert result['blocked'] is False

def test_tsc_voting_requires_supermajority_for_breaking_change():
    """Breaking changes need 4/5 TSC votes"""
    rfc = create_rfc(is_breaking=True)
    
    # Cast votes: 3 approve, 2 reject (not supermajority)
    vote(alice, rfc, approve=True)
    vote(bob, rfc, approve=True)
    vote(charlie, rfc, approve=True)
    vote(diana, rfc, approve=False)
    vote(eve, rfc, approve=False)
    
    result = tally_votes(rfc)
    assert result['approved'] is False
    assert result['reason'] == "Need 4/5, got 3/5"

def test_contributor_role_progression():
    """User can progress: contributor → committer → TSC"""
    alice = create_user()
    
    # Alice has 1 PR merged
    merge_pr(alice, count=1)
    assert alice.has_role('contributor') is True
    assert alice.has_role('committer') is False
    
    # Alice promoted to committer (3/5 committer vote)
    nominate_for_role(alice, 'committer')
    committers = get_committers()
    for committer in committers[:3]:  # 3 approve
        vote_for_role(committer, alice, 'committer', approve=True)
    grant_role(alice, 'committer')
    
    assert alice.has_role('committer') is True

def test_meeting_notes_are_published():
    """TSC meeting notes published within 48 hours"""
    meeting = create_meeting(date="2026-06-11")
    run_meeting(meeting)
    
    # Check notes exist
    assert file_exists("docs/governance/meetings/2026-06-meeting-notes.md") is True
    
    # Verify content
    notes = read_file("docs/governance/meetings/2026-06-meeting-notes.md")
    assert "TSC Meeting: June 11" in notes
    assert "Recording:" in notes
```

---

## Success Criteria

- ✅ RFC CI blocks merge before 14-day comment period ends
- ✅ TSC votes are public (recorded on GitHub)
- ✅ Breaking changes require supermajority (4/5)
- ✅ Monthly TSC meetings held on schedule (public + recorded)
- ✅ Meeting notes published within 48 hours
- ✅ Contributor role progression is transparent (nominations public)
- ✅ All decisions documented (no back-room deals)
- ✅ Community can participate (open comments, public votes)

---

Next: See [[01_TRANSPARENCY_REPORTS.md]] and [[02_AUDIT_FRAMEWORK.md]] for operational features that feed this governance model.

