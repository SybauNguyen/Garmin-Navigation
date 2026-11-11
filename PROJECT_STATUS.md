# Garmin Navigation — Project Status

**Master Plan:** 1.0.0  
**Product baseline:** A0 v2.0.0  
**A1–A7 specification revision:** 2.0.0  
**Updated:** 2026-11-11  
**Phase:** A — Product & System Design  
**Product direction:** B — Google Maps + komoot experience (not API/data coupling)  
**Design freeze:** No  
**Production implementation:** Not started  
**Physical-device verification:** Not started

## Current result

A0–A7 have been re-baselined for the current product direction and stored in this repository. The A0–A7 files are design specifications, not implemented features. Acceptance tests are proposed test cases and have not been run.

## Next work package

**A8 — UX Specification & Acceptance Criteria.** Define the full phone/watch information architecture, screen and state catalogue, map/search/route planning/navigation/discovery/trip/Garmin flows, fēnix 6S HUD behavior, errors and empty states, accessibility, interaction safety and measurable UX release gates.

After A8, perform **A9 Design Review & Freeze**. Do not begin production Flutter or Monkey C implementation until A9 explicitly marks the design **DESIGN FROZEN** and lists every unresolved blocker.

## Re-baselined work packages

| Package | State | Deliverable |
|---|---|---|
| A0 Product baseline | Complete at spec level | `docs/00_PRODUCT_BASELINE_A0.md` |
| A1 Functional requirements | Complete at spec level | `docs/01_FUNCTIONAL_REQUIREMENTS_A1.md` |
| A2 Map, discovery & routing | Complete as provisional architecture | `docs/02_MAP_DISCOVERY_ROUTING_A2.md` |
| A3 Domain & data model | Complete at spec level | `docs/03_DOMAIN_DATA_MODEL_A3.md` |
| A4 Garmin sync protocol | Complete at spec level; uplink gate open | `docs/04_GARMIN_SYNC_PROTOCOL_A4.md` |
| A5 Garmin capability matrix | Complete at research/spec level; hardware gates open | `docs/05_GARMIN_CAPABILITY_MATRIX_A5.md` |
| A6 Navigation engine | Complete at algorithm-spec level; calibration open | `docs/06_NAVIGATION_ENGINE_A6.md` |
| A7 Offline/recovery/privacy/battery | Complete at policy-spec level; real-device measurements open | `docs/07_OFFLINE_RECOVERY_PRIVACY_BATTERY_A7.md` |
| A8 UX specification | Next | `docs/08_UX_SPECIFICATION_A8.md` |
| A9 Design review & freeze | Not started | `docs/09_DESIGN_FREEZE_A9.md` |

## Product and technical decisions

- Product experience: fast daily destination search and turn-by-turn navigation, plus saved places, multi-stop trip planning and curated discovery.
- Target market/validation region: Vietnam first, with Hanoi as the initial field-test area.
- Primary travel modes: motorbike/scooter, car, walking and bicycle. Motorbike is the first validation priority. Hiking/MTB and EV-specific routing are later decisions.
- Phone is the primary map/search/planning/navigation surface; Garmin is the glanceable companion and cached-route follower.
- Map/rendering candidate: MapLibre-compatible renderer with a licensed OSM-derived tile service.
- Routing candidate: self-hosted Valhalla; test `motor_scooter` first for common Vietnamese motorbike travel. `motorcycle` remains beta and must be compared rather than assumed superior.
- Search candidate: Photon pilot; compare Pelias for Vietnamese addresses/POIs.
- Google Maps Platform is not the default provider for the Garmin/offline route pipeline. Current terms for non-EEA billing accounts restrict Routes/Places/Geocoding content from use with non-Google maps; Routes coordinate values have a temporary 30-day cache limit. Any Google-only map mode must be separately isolated and reviewed.
- komoot has no publicly accessible API. Use it as a UX reference only; do not scrape or depend on private endpoints.
- fēnix 6S base model uses a custom renderer. The official MapView supported-device list includes 6S Pro/Sapphire variants, not base 6S.
- `Communications.transmit()` is documented as Since API 4.0.4 while compatible-device lists show fēnix 6S at API 3.4 and the method supported-device list includes 6S. Uplink, app ACK and watch-originated events remain conditional until verified on exact hardware/firmware.
- The watch stores a compact route and maneuver payload, not full phone map tiles or POI catalogs.
- No production code before A9.

## Open blockers

1. Exact fēnix 6S model (base vs Pro/Sapphire), firmware and Connect IQ app compatibility.
2. Whether watch-to-phone `Communications.transmit()` can compile and deliver reliably on the chosen API 3.4 watch.
3. Phone OS targets and exact Garmin Mobile SDK / Flutter bridge integration.
4. Valhalla profile quality and access restrictions for Hanoi motorbike routes, including prohibited roads and road-class edge cases.
5. Photon vs Pelias quality for Vietnamese place/address search.
6. Licensed tile provider and offline/prefetch permissions; public OSM standard tile servers are not an offline tile source.
7. Multi-stop route support and alternatives behavior per selected routing provider.
8. Route-matching thresholds and alert timing require GPS trace replay and physical calibration.
9. Offline storage quota, battery drain and long-route limits need physical measurements.
10. UX usability, legibility and riding/vehicle safety review remain to be completed in A8/A9.

## Validation truth

- No automated acceptance test has been executed.
- No API provider is deployed.
- No Flutter or Connect IQ app source is in this repository yet.
- No BLE throughput, route-transfer, storage, GPS, haptic, rendering or battery bench test has been completed.
- No field route in Hanoi has been verified.
- No design freeze has been approved.

## Continuation rule

When the user says “tiếp tục phát triển”, read this file and the Master Plan, then continue exactly from the next unfinished package. Do not repeat questions already answered. Do not begin production implementation before A9 design freeze. Update Project Status, Master Plan, Changelog and Continuation Prompt after every accepted milestone.
