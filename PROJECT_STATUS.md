# Garmin Navigation — Project Status

**Master Plan:** 1.3.0  
**Product baseline:** A0 v2.0.0  
**A1–A7 specification revision:** 2.0.0  
**A8 UX specification:** 2.0.0  
**A9 design review:** 1.0.0 (review recorded; freeze not approved)  
**Updated:** 2026-11-11  
**Phase:** A — Product & System Design  
**Product direction:** B — Google Maps + komoot experience (not API/data coupling)  
**Design freeze:** No  
**Production implementation:** Not started  
**Physical-device verification:** Not started

## Current result

A0–A7 were re-baselined for direction B and committed in the repository. A8 v2.0.0 defines the phone/watch UX contract and 30 UX acceptance criteria. A9 v1.0.0 now records the traceability review, architecture decisions, technical risks, freeze gates and post-freeze implementation backlog.

**A9 desk review is recorded, but DESIGN FROZEN is not approved.** No evidence exists yet for actual wireframe usability, the fēnix 6S communications/API discrepancy, selected-watch delivery, Hanoi motorbike routing quality, route payload limits or physical watch operation. The next work remains A9 closure, not production coding.

## Work package state

| Package | State | Deliverable |
|---|---|---|
| A0 Product baseline | Complete at spec level | docs/00_PRODUCT_BASELINE_A0.md |
| A1 Functional requirements | Complete at spec level | docs/01_FUNCTIONAL_REQUIREMENTS_A1.md |
| A2 Map, discovery & routing | Architecture candidate; provider/quality gates open | docs/02_MAP_DISCOVERY_ROUTING_A2.md |
| A3 Domain & data model | Complete at spec level; measured payload bounds open | docs/03_DOMAIN_DATA_MODEL_A3.md |
| A4 Garmin sync protocol | Protocol specified; exact uplink and transfer bench open | docs/04_GARMIN_SYNC_PROTOCOL_A4.md |
| A5 Garmin capability matrix | Official-doc research recorded; compile/device verification open | docs/05_GARMIN_CAPABILITY_MATRIX_A5.md |
| A6 Navigation engine | Algorithm spec complete; thresholds and field calibration open | docs/06_NAVIGATION_ENGINE_A6.md |
| A7 Offline/recovery/privacy/battery | Policy spec complete; physical measurements open | docs/07_OFFLINE_RECOVERY_PRIVACY_BATTERY_A7.md |
| A8 UX specification | Complete at design-spec level; wireframe/usability evidence open | docs/08_UX_SPECIFICATION_A8.md |
| A9 Design review & freeze | Review v1.0.0 recorded; mandatory closure gates open | docs/09_DESIGN_REVIEW_A9.md |

## Product and technical baseline

- Phone is the primary surface: Map, Explore, Plan, Trips and Garmin.
- Garmin fēnix 6S is a compact route-guidance companion; it is not a full map/search/planning device.
- Base fēnix 6S is the conservative device target. Use a custom Graphics.Dc renderer; Pro/Sapphire native map support is optional and separately gated.
- Phone platforms: Android and iOS. Prove the native Garmin Mobile SDK bridge on Android first; iOS needs a separate verified bridge.
- Flutter architecture default: Riverpod, Drift/SQLite and provider-neutral adapters.
- Map direction: MapLibre-compatible renderer and licensed OSM-derived tiles. Tile vendor, terms, attribution and offline rights are not yet selected.
- Routing proof-of-concept: self-hosted Valhalla. Test motor_scooter first for everyday Vietnamese motorbike use; car uses an explicit auto profile. Walking/bicycle route availability remains conditional on mode-specific validation.
- Search proof-of-concept: Photon; compare Pelias against Vietnamese address/POI fixtures.
- Never silently replace a motorbike route with a car route.
- No Google Maps Platform or komoot data/API is approved for the OSM/MapLibre/Garmin data path.
- Multi-stop alternatives are shown only when the provider returns them; Valhalla does not currently support alternatives for routes with more than two locations.
- Watch route guidance is cached only when a valid committed route exists, the Watch App is active and GPS is eligible. No watch offline basemap or offline recalculation.
- Raw GPS trace is not retained by default.

## A9 freeze gates

Mandatory before DESIGN FROZEN:

1. Review low-fidelity wireframes for eight phone screens and four base-6S watch layouts.
2. Complete a moderated stationary usability walkthrough and record findings.
3. Record the exact fēnix 6S variant, firmware, Connect IQ SDK and API target.
4. Prove the Garmin Mobile SDK native bridge, one-watch targeting and transfer behavior; resolve or explicitly limit watch uplink/ACK.
5. Build and verify the Hanoi motorbike route fixture corpus.
6. Measure route-payload, geometry and Application.Storage limits on base 6S.
7. Complete deterministic navigation replay and document provisional calibrated thresholds.
8. Record the Android/iOS native bridge verification plan and pass the required Android proof.

Map tile licensing and privacy/provider review remain release gates; offline map downloads stay disabled until a selected provider explicitly permits them.

## Validation truth

- No automated acceptance suite has been run.
- No API provider is deployed.
- No Flutter or Monkey C production source exists in this repository.
- No Connect IQ project has been compiled for the physical fēnix 6S.
- No phone-to-watch or watch-to-phone delivery/ACK test has been completed.
- No multi-watch targeting, BLE throughput, route-transfer, storage, GPS, haptic, rendering or battery bench test has been completed.
- No Hanoi motorbike route fixture has been executed or manually verified.
- No wireframe usability, daylight legibility or exact-watch visual test has been conducted.
- Design freeze is not approved.

## Next work

Continue A9 closure in this order:

1. Exact-watch/Connect IQ SDK compile and communication spike.
2. Native Garmin Mobile SDK bridge and selected-device targeting spike.
3. Hanoi motorbike routing/search fixture and provider evaluation.
4. Watch route payload/storage budget benchmark.
5. Phone/watch wireframes and stationary usability review.
6. Navigation replay/calibration and formal A9 exit review.

Only after the mandatory gates are evidenced may Phase B — Flutter foundation — begin. See docs/09_DESIGN_REVIEW_A9.md for the full audit and backlog.

## Continuation rule

When the user says “tiếp tục phát triển”, read this file, MASTER_PLAN.md and the next open A9 gate. Continue from A9 closure; do not restart A0–A8 and do not begin production Flutter or Monkey C implementation before A9 explicitly approves DESIGN FROZEN. Update the relevant documents, changelog and continuation prompt; commit and verify the change on GitHub.