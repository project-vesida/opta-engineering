# opta-engineering

Part of [Project Vesida](https://github.com/project-vesida). CERN-OHL-S v2.

Design authority for the Optical Transit Array (OpTA). This repository contains the mission,
requirements, product architecture, interfaces, verification criteria, and forward roadmap.

| Document | Purpose |
|---|---|
| [MISSION.md](MISSION.md) | Mission, objectives, constraints, operating concept, and terminology |
| [SYSTEMS.md](SYSTEMS.md) | Product tree, requirements, interfaces, open trades, and phase gates |

## Related repositories

- [opta-model](https://github.com/project-vesida/opta-model) implements the engineering models,
  hardware trade space, and error budget.
- [opta-pipeline](https://github.com/project-vesida/opta-pipeline) implements frame ingestion,
  calibration, detection, astrometry, tracklets, and TDM export.
- [opta-hardware](https://github.com/project-vesida/opta-hardware) owns the node and platform
  hardware catalog and build design.
- [vesida-agent](https://github.com/project-vesida/vesida-agent) will operate one array.
- [vesida-platform](https://github.com/project-vesida/vesida-platform) will ingest and publish
  observations.

Requirements remain open until evidence from the specified verification method closes them.
Model results and synthetic tests are design evidence, not substitutes for hardware verification.
