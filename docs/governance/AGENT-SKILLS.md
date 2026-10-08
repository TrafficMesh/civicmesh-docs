# TrafficMesh Agent Skills Adoption

**Status:** Proposed for TrafficMesh owner review. Structural package only; it does not claim an organization setting has been configured or that an agent has executed a skill.

**Upstream:** `mattpocock/skills` at `f3fc5632f401156837ee3872f14fe33ccf1024ea` (MIT). Full source tree and license are under `.github/skills/`.

## Model

`civicmesh-docs` is the proposed common skill-pack and policy home. Each peer repo pins the docs commit in `.trafficmesh/agent-skills.yaml`, lists the skills enabled for its work, and carries local scope/authority notes. Updating the common pack requires a reviewed PR and then deliberate consumer manifest updates. Source skills stay byte-for-byte upstream; TrafficMesh policy is in `config/agent-skills.yaml`, `AGENTS.md`, and consumer manifests.

A skill is a task-specific method, not permission. It cannot authorize creating or closing issues, merging, deployment, field trials, credential changes, or production operations. A generated recommendation remains a recommendation until the relevant owner accepts it. Keep observed evidence distinct from documentation claims and assumptions.

## Applicability inventory

| Skill | What it does | TrafficMesh disposition |
|---|---|---|
| `ask-matt` | Routes a request to a fitting skill/workflow. | Adapted, gated router: must respect this registry and never route around an authorization gate. |
| `code-review` | Reviews a bounded diff against repository standards and its originating specification. | Enabled for code repos; findings only, no approval or merge authority. |
| `codebase-design` | Designs deep modules with narrow, testable interfaces and explicit seams. | Enabled for backend, firmware, frontend. |
| `diagnosing-bugs` | Uses evidence and hypotheses to find the first divergence behind a bug/regression. | Gated until the target behavior is executable and reproducible; enable for implementation repos after that. |
| `domain-modeling` | Sharpens domain language, glossary, and durable design decisions. | Enabled across all repos; prefer docs hub terms and existing product contracts. |
| `grill-me` | Interviews the user to stress-test an idea/plan. | Enabled; interview only, no automatic document or tracker writes. |
| `grill-with-docs` | Interviews and records domain decisions in docs. | Adapted; proposed decisions must remain marked proposed until owner acceptance. |
| `grilling` | Runs a structured interview about a plan or decision. | Enabled for docs and architecture work; questions do not imply acceptance. |
| `handoff` | Captures current state, evidence, and next steps for another agent/person. | Enabled across repos; handoff transfers context, not authority. |
| `implement` | Implements a bounded specification/ticket. | Gated by explicit user task and repository contribution controls; the skill itself supplies no authority. |
| `implement-spec` | Coordinates implementation of a specification and its tickets. | Gated by explicit implementation request, accepted spec, and verified ticket/contract mapping. |
| `improve-codebase-architecture` | Scans for boundary improvements and reports candidate refactors. | Enabled for backend, frontend, firmware; recommendations only, no unrequested refactor. |
| `pr` | Writes a reviewable PR description. | Enabled across repos; report evidence and omitted checks accurately. |
| `prototype` | Builds a disposable spike to answer a design/UX question. | Enabled for code/UI research; synthetic data only, clearly labeled non-production. |
| `research` | Investigates a question from high-trust primary sources and records findings. | Enabled; cite provenance, version/date, uncertainty, and license when relevant. |
| `retro` | Reviews an engineering session and proposes feedback-loop improvements. | Enabled for code repos; suggestions require separate approval before policy changes. |
| `setup-matt-pocock-skills` | Configures issue vocabulary and domain-document layout for other skills. | Adapted and gated; first map actual TrafficMesh tracker labels and docs, do not write settings without specific authorization. |
| `tdd` | Guides red/green/refactor and test-quality practice. | Enabled for backend/frontend; firmware when runnable test setup supports the task. Never claim hardware validation from unit tests. |
| `teach` | Builds a staged learning path and practice activities. | Enabled across project work; examples must not be mistaken for approved system behavior. |
| `to-questionnaire` | Turns unresolved decisions into questions for their human owner. | Enabled; drafting is allowed, sending requires explicit authorization. |
| `to-spec` | Synthesizes known context into a testable specification. | Adapted; separate facts, proposals, assumptions, evidence, and acceptance owner; publishing requires explicit request. |
| `to-tickets` | Breaks an accepted plan/spec into dependency-aware small tickets. | Adapted; draft locally first; creating GitHub issues requires explicit request. |
| `triage` | Classifies issues/PRs through a tracker state machine and prepares briefs. | Gated until TrafficMesh label/state mapping is reviewed; no issue/PR state writes without explicit authorization. |
| `wait-what` | Re-explains a confusing answer in plainer project language. | Enabled across repos; use the docs hub glossary and preserve technical accuracy. |
| `wayfinder` | Maps a large uncertain effort into decisions and dependencies. | Adapted; map product questions before execution and mark owner/unresolved decisions. Tracker publication gated. |
| `wizard` | Generates an interactive shell guide for human-only setup. | Excluded from default pack pending Windows, credentials, and city/institution security review. |
| `writing-for-agents` | Makes agent instructions concise, routed, and verifiable. | Enabled for docs and repo guidance. |

### Inventory exclusions

The 12 skills below are not in the default TrafficMesh pack: `chief-of-staff`, `claude-handoff`, `loop-me`, `setup-ts-deep-modules`, `writing-beats`, `writing-fragments`, `writing-shape`, `git-guardrails-claude-code`, `migrate-to-shoehorn`, `scaffold-exercises`, `setup-pre-commit`, and `wizard`.

Reasons: experimental or long-running delegation, Claude-specific behavior, TypeScript-only constraints not selected project-wide, editorial work outside engineering scope, Claude hooks, an unselected test helper, unrelated course scaffolding, non-universal pre-commit tooling, and credentials/security review needs. `wizard` is not installed until its gate is addressed.

## Repo fit and enabled sets

| Repo | Focus | Initial enabled set |
|---|---|---|
| `civicmesh-backend` | FastAPI service, API and data behavior | `code-review`, `codebase-design`, `domain-modeling`, `pr`, `research`, `tdd`, `improve-codebase-architecture`, `prototype`, `retro`, `teach`, `grill-me`, `grill-with-docs`, `grilling`, `handoff`, `to-questionnaire`, `to-spec`, `to-tickets`, `wayfinder`, `wait-what`, `writing-for-agents` |
| `civicmesh-frontend` | React/TypeScript UI | Same as backend, excluding tracker-writing permissions; TypeScript boundary tooling remains excluded pending a separate decision. |
| `civicmesh-firmware` | Raspberry Pi/edge inference and device software | `code-review`, `codebase-design`, `domain-modeling`, `pr`, `research`, `teach`, `grill-me`, `grill-with-docs`, `grilling`, `handoff`, `to-questionnaire`, `to-spec`, `to-tickets`, `wayfinder`, `wait-what`, `writing-for-agents`; `tdd` and bug diagnosis only when the target is reproducible in an executable environment. |
| `civicmesh-hardware` | BOM, schematics/specification, assembly/calibration docs | `domain-modeling`, `pr`, `research`, `teach`, `grill-me`, `grill-with-docs`, `grilling`, `handoff`, `to-questionnaire`, `to-spec`, `to-tickets`, `wayfinder`, `wait-what`, `writing-for-agents`; no software-test assumptions for physical assemblies. |
| `civicmesh-docs` | Common project architecture, API, policy and roadmap docs | `code-review`, `domain-modeling`, `grill-me`, `grill-with-docs`, `grilling`, `handoff`, `pr`, `research`, `teach`, `to-questionnaire`, `to-spec`, `to-tickets`, `wayfinder`, `wait-what`, `writing-for-agents`, `prototype`, `retro`; implementation and triage remain gated. |

The exact skill IDs and gates are machine-readable in `config/agent-skills.yaml`. Consumer manifests currently use the explicit placeholder `pending-merge`; replace it with the merge commit before enabling the pack in other repositories. Per-repo manifests are opt-ins, not organization-wide GitHub Copilot configuration.

## Governance checks

1. Confirm the owning repo, lifecycle, domain docs, contracts, tests, and current GitHub labels.
2. Confirm upstream source, revision, license, files, tool assumptions, side effects, and writes.
3. Separate observed facts, documentation claims, inferences, assumptions, and recommendations.
4. Add TrafficMesh constraints outside upstream source.
5. Validate source hashes and manifest references; this proves structure/provenance, not behavior.
6. Review and merge the adoption PR before relying on the pack.

## Organization-level Copilot instructions

Repository skill packs and organization custom instructions have different scopes. These files travel with repositories; org-wide instructions are configured in TrafficMesh organization settings. A GitHub administrator should add a short rule set pointing agents to the consumer manifest and authority boundaries. Do not claim the setting is active until saved and verified in GitHub.