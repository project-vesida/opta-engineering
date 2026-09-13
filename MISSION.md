# OpTA Mission Definition

Defines the mission for the Optical Transit Array (OpTA). Requirements and design decisions trace
to this document.

## Mission statement

Design, build, and verify an autonomous optical transit array that produces satellite tracklets
with characterized accuracy and timing at a hardware cost accessible to independent operators.

## Primary objectives

| ID | Objective | Success criterion |
|---|---|---|
| PO-1 | Produce trustworthy astrometric tracklets of sunlit LEO objects. | Meet the accuracy, blind-completeness, and false-acceptance requirements in [SYSTEMS.md](SYSTEMS.md). Orbit determination is downstream and out of scope. |
| PO-2 | Operate autonomously each night. | Complete a dusk-to-dawn acquisition and processing session with zero manual steps. |
| PO-3 | Keep the array accessible to independent operators. | Priced array BOM no greater than $3,000 USD, excluding one-time tooling. |

## Secondary objectives

| ID | Objective | Notes |
|---|---|---|
| SO-1 | Maximize useful sky coverage within the cost constraint. | Re-evaluate node count and field allocation as the hardware catalog changes. |
| SO-2 | Characterize performance beyond the acceptance gate. | Report completeness and false-acceptance rate versus magnitude, angular rate, sky brightness, and blind/cued mode. |
| SO-3 | Enable community replication. | Publish hardware, software, interfaces, and operating documentation under open licenses. |
| SO-4 | Support multi-node arrays. | Scale by replicating nodes without changing the platform architecture. |

## Stakeholders

| Stakeholder | Interest |
|---|---|
| Project Vesida contributors | Build and maintain the open instrument and shared catalog. |
| Independent array operators | Affordable, replicable hardware and unattended operation. |
| Researchers and civil society | Open observations and reproducible methods. |
| Orbit determination consumers | Tracklets with characterized accuracy and timing. |

## Constraints

| ID | Constraint | Rationale |
|---|---|---|
| C-1 | Total array BOM no greater than $3,000 USD. | Community accessibility. |
| C-2 | One-person build and operation. | Independent operators must be able to deploy and maintain an array. |
| C-3 | Evidence-gated delivery. | Phases advance when their verification criteria pass. |
| C-4 | Passive observation only. | Optical receive-only operation. |
| C-5 | LEO is the primary target regime. | Defines angular rates, sensitivity, and timing requirements. |
| C-6 | GNSS-disciplined timing. | Frame timestamps derive from GNSS PPS. |

## Concept of operations

An array is fixed at a site with open sky access. It contains one or more optical nodes, each
covering a designated portion of the sky. Nodes share compute, GNSS time, power, enclosure, and
connectivity.

1. The array powers up and establishes GNSS time.
2. At the configured solar-elevation threshold, all nodes begin acquisition.
3. Every frame receives a GNSS-disciplined timestamp.
4. The pipeline calibrates frames, solves astrometry, detects moving objects, and builds tracklets.
5. The agent stores and uploads tracklets when connectivity permits.
6. Acquisition stops at the configured dawn threshold.

No operator action is required between deployment and physical maintenance.

### Site assumptions

- Mains power is available; solar power is outside the initial scope.
- Delayed network upload is acceptable.
- The GNSS antenna has a clear sky view.
- Sky brightness is measured for every scored observation.

## Delivery phases

| Phase | Objective | Exit question |
|---|---|---|
| Prototype | Build one node and run the pipeline on its frames. | Does the hardware produce correctly timestamped data the pipeline can reduce? |
| Field test | Operate one node autonomously against independently known targets. | Does the node meet accuracy, completeness, false-acceptance, timing, and autonomy requirements? |
| Array | Integrate the selected number of nodes on one platform. | Does the array meet cost, throughput, coverage, and replication requirements? |
| Network | Connect independently operated arrays to the public catalog. | Can operators reliably contribute interoperable observations? |

## Terminology

| Term | Definition |
|---|---|
| Node | One observation unit: lens, sensor, and mounting hardware. |
| Array | One deployable system at one site: one or more nodes sharing compute and infrastructure. |
| Agent | Software that operates one array and exchanges data with the platform. |
| Network | Arrays at multiple sites contributing tracklets to a shared catalog. |

A single-node array is valid; requirements apply regardless of node count unless stated otherwise.
