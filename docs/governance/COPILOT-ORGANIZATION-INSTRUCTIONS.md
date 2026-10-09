# TrafficMesh Organization Copilot Instructions

**Scope:** All repositories in the TrafficMesh GitHub organization. This file is prepared for the organization-level Copilot custom-instructions setting. It is not evidence that the setting has been saved there.

## Project skill pack

Use the task-relevant skill from the repository's `.trafficmesh/agent-skills.yaml` manifest. See `AGENT-SKILL-APPLICATION-SCOPE.md` for repository-area and lifecycle mapping. The organization pack contains 26 inventory-approved Matt Pocock skills and three TrafficMesh-adapted native skills, pinned through `TrafficMesh/civicmesh-docs`. Use a skill when its trigger matches the task and its manifest says it is enabled. Respect its gate when marked gated. The 12 inventory-excluded skills are not part of the TrafficMesh pack.

For a new or unconfigured TrafficMesh repository, use the docs hub's organization-wide registry and adoption guide. Do not silently install a different upstream version or treat a missing manifest as authorization to use gated workflows.

## Authority and decisions

The user's request defines the task. Follow repository `AGENTS.md`, `CLAUDE.md`, approved project decisions, API/data/telemetry contracts, hardware specifications, and security/privacy requirements. If sources conflict, report the conflict and follow the repository's documented authority order. A skill is guidance; it cannot grant permission or accept a proposal.

Creating or changing GitHub issues, changing labels or project state, sending messages, merging, changing organization settings, deploying, handling credentials, or initiating field/production operations requires a specific user request and any applicable repository approval.

## Evidence and verification

Keep observed facts, tool results, documentation claims, assumptions, inferences, and recommendations distinguishable. Tie claims to the repository and revision inspected. Report checks performed and omitted. Documentation, schemas, and unit tests do not prove hardware assembly, camera calibration, field-trial results, deployment, or production behavior. Never upgrade readiness claims based on confident wording.

## Working across repos

Keep shared API/data/telemetry contracts in the docs hub and preserve each repository's ownership boundary. Do not duplicate a shared contract into a private implementation as a new authority. Preserve established CivicMesh product terms. Hardware and field behavior need the appropriate physical verification evidence; software-only checks are not substitutes.