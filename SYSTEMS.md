# OpTA Systems Engineering

Current product architecture, requirements, interfaces, open trades, and forward verification gates.
All requirements trace to [MISSION.md](MISSION.md).

## Product tree

```text
OpTA array
├── Node × N
│   ├── Optics
│   ├── Sensor
│   └── Mount
└── Platform
    ├── Compute
    ├── GNSS timing
    ├── Power
    ├── Enclosure
    └── Connectivity
```

One node is the minimum deployable array. Software maps onto the product as follows:

| Repository | Role |
|---|---|
| `opta-model` | Hardware sizing, array optimization, radiometry, and error budgets |
| `opta-hardware` | Node and platform hardware design |
| `opta-pipeline` | Runtime frame processing and tracklet export |
| `vesida-agent` | Array scheduling, capture, reduction, upload, and health |
| `vesida-platform` | Tracklet ingestion, catalog, API, and open-data publication |

## Requirement identifiers

System requirements use `OpTA.<domain>`. Subsystem requirements use
`OpTA.<subsystem>.<domain>`. `DT-*` values are design controls used by models and regression tests;
they are not field-verification results.

## System requirements

| ID | Requirement | Verification | Traces to | Status |
|---|---|---|---|---|
| OpTA.COST | The array BOM shall be no greater than $3,000 USD, excluding one-time tooling. | Priced-BOM inspection | PO-3, C-1 | Open |
| OpTA.AUTO | The array shall operate from dusk to dawn with zero human intervention per session. | Demonstration | PO-2, C-2 | Open |
| OpTA.ACC | Each node's fit-free cross-track residual shall have a 68% chi-squared upper endpoint no greater than `sqrt(10.0² + sigma_ref²)` arcsec against an independent precision ephemeris. Each qualifying tracklet shall contain at least 25 valid positions; at least two tracklets from two objects shall pass. Report the 95% endpoint and along-track residual after one per-session clock-offset diagnostic. | Field test | PO-1 | Open |
| OpTA.DET | Blind search shall produce at least one accepted I-02 tracklet for at least 80% of 25 or more pre-registered eligible crossings. Report the two-sided Wilson 95% interval. | Field test | PO-1, C-5 | Open |
| OpTA.FAR | The one-sided 95% Poisson upper confidence bound shall be no greater than one false accepted tracklet per node-hour under the same production search used for OpTA.DET. | Field test | PO-1 | Open |
| OpTA.SCALE | A one-node array shall be fully functional and shall scale to multiple nodes without changing the node or platform-bus architecture. | Architecture review and demonstration | SO-4, PO-3 | Open |

The 10 arcsec angular component of `OpTA.ACC` corresponds to 38.8 m transverse at an 800 km
reference slant range.

## Field-test operating condition

`OpTA.DET` and `OpTA.FAR` use the as-built node and a hashed production configuration. The blind
search processes every valid, contiguous, non-overlapping 5 s window over the full operational
image and a two-axis angular-rate range of ±2.5 degrees per second. No ephemeris prior is available
to the production search.

An independent ephemeris selects the scoring window before pipeline output is inspected. An
eligible crossing must provide a full 5 s usable-field dwell, independent apparent Johnson V no
greater than 13.0, elevation of at least 20 degrees, measured sky brightness of at least
20.5 V mag/arcsec², motion inside the search range, and valid calibration, timing, WCS, and duty
metadata. The sample shall span at least five objects and two sessions.

Every accepted tracklet is resolved using independent truth or adjudication. An unresolved
acceptance counts as false. FAR exposure is the sum of valid searched window durations. Cued search
is reported separately and cannot close the blind-search requirements.

## Design controls

| ID | Control | Use |
|---|---|---|
| DT-ACC | Modelled astrometric and timing RSS no greater than 10.0 arcsec, using 0.3 px centroiding and 2.0 arcsec residual distortion. | Error budget and optimizer regression gate |
| DT-DET | Known-track stacked SNR at least 5 for apparent V magnitude 13.0 in one 5 s coherent window at sky 21.0 mag/arcsec², 45-degree elevation, 800 km range, and 0.5 degrees/s. | Radiometric sizing and signal-chain regression |
| DT-COV | Full-sensor solid angle at least 0.095 sr per node above 20-degree elevation. | Coverage sensitivity; not a procurement veto without a mission-level coverage requirement |

## Subsystem requirements

| ID | Requirement | Verification | Status |
|---|---|---|---|
| OpTA.NOD.ACC | Each node shall satisfy `OpTA.ACC` independently; no inter-node error-budget credit is permitted. | Field test | Open |
| OpTA.NOD.DET | Each node shall satisfy `OpTA.DET` independently. | Field test | Open |
| OpTA.NOD.FAR | Each node shall satisfy `OpTA.FAR` independently. | Field test | Open |
| OpTA.PLT.TMG | The platform shall timestamp each frame within 1 ms of UTC using GNSS PPS. | Bench and field test | Open |
| OpTA.PLT.CMP | The platform shall ingest and process the configured aggregate node data rate without loss. | Prototype profiling | Open |
| OpTA.PLT.ENV | The enclosure shall maintain safe operating conditions across the declared deployment envelope. | Environmental test | Open |

## Reference configuration

The current model profile is a design baseline, not procurement authority or verified hardware.

| Property | Baseline |
|---|---|
| Sensor | SVBONY SV705C / Sony IMX585 |
| Optics | 7Artisans 25 mm f/0.95 |
| Full-sensor field of view | 25.2 × 14.4 degrees |
| Full-resolution mode | 3856 × 2180 at 21 fps |
| Runtime ROI mode | 1920 × 1080 at 25 fps |
| Coherent processing window | 5 s |
| Reference array | Four nodes plus shared platform, subject to trade closure |

Source values live in `opta-model/src/opta_model/configs/hardware_catalog/` and are exercised by
`opta-model/tests/test_requirements.py`.

## Interfaces

| ID | From | To | Data | Format | Owner |
|---|---|---|---|---|---|
| I-01 | Node sensor | Array compute | Raw frame, timestamp, and node ID | FITS header contract | `opta-hardware` |
| I-02 | Array compute | Agent/platform | Astrometric tracklet | JSON and CCSDS TDM | `opta-pipeline` |
| I-03 | External catalog | Array compute | Reference stars | Local Gaia DR3 extract | `opta-pipeline` |
| I-04 | External provider | Verification tools | Reference orbits | OMM, OEM, SP3, or CPF | Verification consumer |
| I-05 | Platform timing | Array compute | PPS signal | Electrical pulse to GPIO | `opta-hardware` |

The executable I-01 and I-02 schemas are documented in
[`opta-pipeline/docs/interfaces.md`](https://github.com/project-vesida/opta-pipeline/blob/main/docs/interfaces.md).

## Open trades

| ID | Trade | Work required |
|---|---|---|
| T-01 | Lens selection | Re-run the optimizer against current stocked parts and the complete requirement envelope. |
| T-02 | Sensor selection | Validate read noise, quantum efficiency, full well, cadence, geometry, interface, and availability. |
| T-04 | Compute platform | Profile production workloads for the intended node count, power, and storage budget. |
| T-06 | Timestamp implementation | Measure end-to-end frame timestamp error against PPS. |
| T-09 | Array configuration | Re-optimize node count and pointing after T-01 and T-02 close. |
| T-10 | Enclosure strategy | Compare shared and per-node housings for thermal behavior, cost, and serviceability. |
| T-11 | Distortion calibration | Measure residual distortion across the selected lens and operating temperatures. |

## Forward gates

| Phase | Gate |
|---|---|
| Prototype | Assemble one node; capture valid I-01 frames; verify plate solving, detection, tracklets, timing, storage, and throughput. |
| Field test | Pass `OpTA.AUTO`, `OpTA.ACC`, `OpTA.DET`, `OpTA.FAR`, and `OpTA.PLT.TMG` using the declared protocol. |
| Array | Close hardware and platform trades; pass cost and aggregate-throughput checks; publish the reproducible build. |
| Network | Accept authenticated I-02/TDM uploads from independently operated arrays and publish them through the open-data interface. |
