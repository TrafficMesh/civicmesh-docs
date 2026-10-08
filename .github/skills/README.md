# TrafficMesh agent skills pack

This repository vendors the TrafficMesh skill policy and the pinned upstream source pack. Upstream: [mattpocock/skills](https://github.com/mattpocock/skills), revision `f3fc5632f401156837ee3872f14fe33ccf1024ea`, MIT; see `MIT-LICENSE-Matt-Pocock`.

The 26 inventory-approved upstream skills are under `.github/skills/<skill>/`, byte-matched to the pinned source using `config/mattpocock-skills.sha256`. Three TrafficMesh-native adapted skills are included too. The 12 inventory-excluded upstream skills are absent. The authoritative TrafficMesh disposition is `config/agent-skills.yaml`; this file records what is enabled, gated, prohibited, or excluded. Repo consumers opt in through `.trafficmesh/agent-skills.yaml` pinned to this repo's immutable commit.

## Principles

- Preserve upstream source bytes. Put TrafficMesh rules in the registry, wrapper guidance, and consumer manifests.
- Keep documented, proposed, implemented, deployed, and production-proven states distinct.
- A skill never grants authority to publish issues, merge, deploy, or change production state.
- A structural validation pass proves manifest shape and paths only; it does not prove skill behavior or product runtime.
- Review upstream changes through a PR; never float to the default branch.

See `docs/governance/AGENT-SKILLS.md` for applicability, gates, and per-repository enablement.