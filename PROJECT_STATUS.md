# Garmin Navigation — Project Status

**Master Plan:** 1.1.0  
**Product baseline:** A0 v2.0.0  
**A1–A7 specification revision:** 2.0.0  
**A8 UX specification:** 1.0.0  
**Updated:** 2026-11-11  
**Phase:** A — Product & System Design  
**Product direction:** B — Google Maps + komoot experience (not API/data coupling)  
**Design freeze:** No  
**Production implementation:** Not started  
**Physical-device verification:** Not started

## Current result

A0–A7 were re-baselined for the current direction and committed in `f83c192`. A8 UX specification is now complete at design/specification level and committed separately. All documents describe intended behavior and verification criteria; they do not prove implementation or test execution.

## Next work package

**A9 — Design Review & Freeze.** Audit traceability from A0 product scope → A1 requirements → A2 provider/data policy → A3 schema → A4 transfer → A5 Garmin capability → A6 navigation logic → A7 recovery/privacy/power → A8 screens/states. Resolve blockers or explicitly accept them as limitations/deferred work/release blockers. Produce an implementation backlog and only then mark DESIGN FROZEN.

Do not start production Flutter or Monkey C implementation until A9 explicitly approves the design and lists all residual risks.

## Work package state

| Package | State | Deliverable |
|---|---|---|
| A0 Product baseline | Complete at spec level | `docs/00_PRODUCT_BASELINE_A0.md` |
| A1 Functional requirements | Complete at spec level | `docs/01_FUNCTIONAL_REQUIREMENTS_A1.md` |
| A2 Map, discovery & routing | Complete as provisional architecture | `docs/02_MAP_DISCOVERY_ROUTING_A2.md` |
| A3 Domain & data model | Complete at spec level | `docs/03_DOMAIN_DATA_MODEL_A3.md` |
| A4 Garmin sync protocol | Complete at spec level; uplink gate open | `docs/04_GARMIN_SYNC_PROTOCOL_A4.md` |
| A5 Garmin capability matrix | Complete at research/spec level; hardware gates open | `docs/05_GARMIN_CAPABILITY_MATRIX_A5.md` |
| A6 Navigation engine | Complete at algorithm-spec level; calibration open | `docs/06_NAVIGATION_ENGINE_A6.md` |
| A7 Offline/recovery/privacy/battery | Complete at policy-spec level; measurements open | `docs/07_OFFLINE_RECOVERY_PRIVACY_BATTERY_A7.md` |
| A8 UX specification | Complete at design-spec level; wireframe/usability validation pending | `docs/08_UX_SPECIFICATION_A8.md` |
| A9 Design review & freeze | Next | `docs/09_DESIGN_FREEZE_A9.md` |

## Product and technical decisions

- Product experience: fast daily destination search and turn-by-turn navigation, plus multi-stop trip planning, saved journeys and place discovery.
- Initial market/validation: Vietnam, with Hanoi as the initial field-test area.
- P0 travel modes: motorbike/scooter, car, walking and bicycle. Motorbike is first validation priority.
- Phone is primary map/search/planning/navigation; Garmin fēnix 6S is a glanceable companion and cached-route follower.
- Map/rendering candidate: MapLibre-compatible renderer with licensed OSM-derived tiles.
- Routing candidate: self-hosted Valhalla; test `motor_scooter` first for common Vietnamese motorbike travel. `motorcycle` is beta and needs separate validation.
- Search candidate: Photon pilot; compare Pelias for Vietnamese addresses/POIs.
- Google Maps Platform is not the default provider for the Garmin/offline route pipeline. Current terms for non-EEA billing accounts restrict Routes/Places/Geocoding content from use with non-Google maps; temporary route-coordinate caching is limited. A Google-only map mode would require isolated design/legal review.
- komoot has no publicly accessible API; use as UX reference only.
- Base fēnix 6S uses a custom renderer; native MapView is only for listed Pro/Sapphire variants after exact-device verification.
- `Communications.transmit()` documentation/API-level mismatch remains unresolved for API 3.4 6S devices; uplink is a verification gate.

## Open blockers and risks

1. Exact watch model, firmware and Connect IQ compatibility.
2. `transmit()` compile/runtime/physical delivery on API 3.4 fēnix 6S.
3. Exact Android/iOS targets and Garmin Mobile SDK bridge.
4. Hanoi motorbike profile quality and road access restrictions.
5. Photon vs Pelias Vietnamese search relevance.
6. Licensed map tile provider and offline rights.
7. Multi-stop provider capability and alternatives behavior.
8. Navigation thresholds require deterministic replay and field calibration.
9. Offline storage, route length, battery and checkpoint policies need measurements.
10. A8 wireframes and usability sessions remain to be produced before A9.
11. No physical Garmin prototype test has been executed.

## Validation truth

- No automated acceptance test has been run.
- No API provider is deployed.
- No Flutter or Connect IQ production source exists in this repository.
- No BLE throughput, route-transfer, storage, GPS, haptic, rendering or battery bench test has been completed.
- No Hanoi route field fixture has been verified.
- No usability test/wireframe test has been conducted.
- Design freeze is not approved.

## Continuation rule

When the user says “tiếp tục phát triển”, read this file and the Master Plan, then continue exactly from A9. Do not repeat decisions already recorded. Do not begin production implementation before A9 design freeze. Update the relevant package, Project Status, Master Plan, Changelog and Continuation Prompt; commit and verify the change on GitHub.
