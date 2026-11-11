# A9 — Design Review, Traceability & Freeze Gate

**Document ID:** GN-A9-001  
**Version:** 1.0.0  
**Review date:** 2026-11-11  
**Reviewed baseline:** A0–A7 v2.0.0; A8 v2.0.0; Master Plan v1.2.0  
**Review type:** Desk review of specification set and current official technical documentation  
**Decision:** **DESIGN FREEZE NOT APPROVED**  
**Repository status:** Review and implementation backlog recorded; no production code authorized

## 1. Executive decision

The A0–A8 documents form a coherent product and system-design baseline for a phone-first navigation product with a Garmin fēnix 6S companion. The core product boundaries, route/domain separation, transfer safety model, navigation-state model, offline policy and phone/watch UX are consistent enough to carry forward.

The review does **not** approve DESIGN FROZEN yet. This is not because the product direction needs to be restarted. It is because several evidence gates that A8 explicitly placed before freeze have not been executed:

1. Phone and watch low-fidelity wireframes have not been reviewed as a complete screen set, and no task-based usability session has been run.
2. The Connect IQ API-level discrepancy around watch-to-phone transmission has not been resolved by a compile and physical-device probe.
3. The Garmin Mobile SDK path has not been proven from a Flutter host app, including exact selected-device targeting when more than one watch is paired.
4. The proposed OSM/Valhalla stack has not passed the Hanoi motorbike access and route-quality fixture suite.
5. Watch route serialization, storage limits and long-route behavior have not been measured on the actual fēnix 6S.

The review therefore records an **A9 closure backlog**, not a false freeze. Production Flutter and Monkey C implementation remain blocked. Small, disposable feasibility probes and design-validation artifacts may be prepared as A9 evidence, but they must not be treated as production features.

## 2. Review scope and evidence standard

This review checks the chain:

A0 product scope → A1 functional requirements → A2 provider/data policy → A3 domain model → A4 transfer protocol → A5 Garmin capabilities → A6 navigation engine → A7 recovery/privacy/power → A8 screens/states.

Evidence is classified as:

- **Specified:** the behavior or constraint is written down.
- **Desk-verified:** checked against current official documentation or internal traceability.
- **Prototype-verified:** reproduced in a disposable proof of concept.
- **Device-verified:** measured on the exact watch/phone/firmware combination.
- **User-validated:** exercised with representative users in a controlled usability session.

A written acceptance criterion is not a passed test. A vendor's supported-device list is not a substitute for compilation or physical delivery testing.

## 3. A0–A8 traceability audit

| Chain | Review result | Finding | Closure evidence |
|---|---|---|---|
| A0 → A1 | Pass at specification level | Daily navigation, multi-stop planning, discovery and Garmin handoff map to explicit requirements and priorities. | Keep requirement IDs stable through implementation. |
| A1 → A2 | Partial | Search, route modes, restrictions, alternatives, attribution and traffic policy are represented. Actual Vietnamese search and motorbike quality remain unmeasured. | Hanoi route corpus and Vietnamese search relevance comparison. |
| A2 → A3 | Pass with open provider contracts | Canonical place/route records preserve provider provenance and keep vendor fields at adapter boundaries. Cache expiry and map rights depend on the chosen provider contract. | Provider contract and cache-policy record before production data storage. |
| A3 → A4 | Partial | The normalized route is projected into a compact versioned watch payload. Maximum points, route length and geometry simplification are not measured. | Serialization fixtures, size budget and base-6S storage benchmark. |
| A4 → A5 | Blocked | The protocol requires application-level confirmation, but watch uplink is not proven for the API 3.4 baseline. | Exact SDK compile/runtime and phone-listener test on the actual watch. |
| A5 → A6 | Partial | Watch App, custom renderer, GPS qualification and lifecycle boundaries align. Exact location cadence, app resume and haptic behavior are not device-tested. | Simulator and physical-watch lifecycle test. |
| A6 → A7 | Pass at algorithm/policy level | GPS uncertainty, route replacement, stale samples, off-route hysteresis, arrival and recovery preserve the same safety principles. Thresholds remain tunable candidates. | Deterministic replay plus field calibration dataset. |
| A7 → A8 | Pass at specification level | Offline, permission, GPS, link-loss, transfer, storage and deletion states have corresponding UX treatment. | Execute the relevant A8 acceptance tests. |
| A8 → A9 | Not yet satisfied | A8 has 30 UX acceptance criteria, but wireframes, task-based usability, daylight legibility and exact-watch visual checks have not been completed. | Screen wireframes, usability findings and round-screen evidence. |

### Traceability conclusion

No package-level contradiction invalidates direction B. The unresolved items are concentrated in provider selection/quality, watch capability proof, numerical calibration, payload limits and UX evidence. These are now explicit A9 closure gates below.

## 4. Decisions confirmed by this review

These decisions are stable enough to use as the design baseline. They do not imply that their implementation has passed testing.

| ID | Decision | Status and boundary |
|---|---|---|
| A9-D01 | Phone is the primary product surface; Garmin is a companion. | Confirmed. Search, planning, route calculation, voice guidance and trip management remain phone responsibilities. |
| A9-D02 | Base fēnix 6S is the conservative watch baseline. | Confirmed as the compatibility target. Exact variant and firmware must still be recorded before device verification. |
| A9-D03 | The watch is a compact route follower, not a second phone map app. | Confirmed. No watch search, POI discovery, full offline basemap or offline route recalculation. |
| A9-D04 | Base 6S uses a custom Graphics.Dc route renderer. | Confirmed. Do not call native MapView/MapTrackView on the base model. Pro/Sapphire native map behavior is an optional later capability, not an MVP dependency. |
| A9-D05 | Phone-to-watch communication uses the official Connect IQ Mobile SDK app-message route. | Confirmed as the integration direction. A Flutter wrapper is not assumed to exist or to expose all required targeting behavior; implement a native Android/iOS bridge behind a Dart interface if the official SDK permits it. |
| A9-D06 | No route is marked committed from a phone SDK send callback alone. | Confirmed. “Sent”, “received”, “validated” and “committed” remain distinct states. |
| A9-D07 | Active/pending route replacement is transactional. | Confirmed. The prior active route remains available until a validated replacement is committed. |
| A9-D08 | Ordinary Vietnamese motorbike routing is tested first with Valhalla motor_scooter. | Confirmed as the first experiment, not as a proven legal or quality guarantee. Touring motorcycle remains separately gated. |
| A9-D09 | Car routing uses an explicit automobile profile; no silent profile substitution is allowed. | Confirmed. Any unsupported or failed mode must be surfaced to the user. |
| A9-D10 | Walking and bicycle remain selectable product modes, but their route availability is conditional on mode-specific access and quality tests. | Confirmed. Do not promise equal route coverage before fixtures pass. |
| A9-D11 | Valhalla is the routing proof-of-concept candidate; Photon is the first geocoder pilot, with Pelias as the comparison candidate. | Confirmed for evaluation only. Deployment, operating cost and final provider choice remain open. |
| A9-D12 | MapLibre-compatible rendering and licensed OSM-derived map data remain the map direction. | Confirmed at architecture level. No specific tile vendor or offline entitlement is approved. |
| A9-D13 | Phone persistence uses SQLite through Drift; state management uses Riverpod. | Selected as the default Flutter architecture for the implementation backlog. Revisit only if a concrete compatibility or maintenance issue is demonstrated. |
| A9-D14 | Android and iOS are the intended phone platforms. | Confirmed as the product target. Build and test the Garmin native bridge on Android first; iOS is not considered supported until its own SDK bridge test passes. |
| A9-D15 | Raw GPS breadcrumbs are not retained by default. | Confirmed. Trip summaries may be retained under the A7 deletion and retention rules. |

## 5. Garmin capability review

### 5.1 Exact fēnix 6S boundary

The current Garmin compatible-device matrix lists fēnix 6S variants at API 3.4 and 240 × 240 round Memory-in-Pixel. The base model must therefore be treated as an API 3.4 target unless the exact device proves otherwise.

The MapTrackView supported-device list includes 6S Pro/Sapphire variants but not the base 6S. The safe baseline remains a custom Graphics.Dc renderer for the base watch.

### 5.2 Watch-to-phone transmission gate

The current Toybox.Communications reference marks transmit() as available since API 4.0.4. Its supported-device list also includes fēnix 6S and 6S Pro, while the compatible-device matrix lists those watches at API 3.4.

This is a documentation/API-level discrepancy, not proof of compatibility. A method's device list alone does not establish that a project targeting API 3.4 can compile and call it. Until a minimal project proves the exact behavior:

- do not make watch-to-phone events a dependency of normal phone navigation;
- do not claim watch-originated off-route reporting or phone-side reconciliation;
- do not show “Đã đồng bộ” from the SDK send result;
- keep the current route usable when a replacement cannot be confirmed;
- treat bidirectional commit confirmation as a release blocker for the full Garmin handoff requirement.

The Connect IQ SDK download page currently lists Android and iOS Mobile SDKs. That establishes that native SDK integration paths exist; it does not establish that a Flutter plugin, selected-device targeting or the desired message delivery behavior is already available.

### 5.3 Required Garmin spike evidence

Record all of the following against the exact watch and firmware:

1. Connect IQ SDK version and compiler target/API level.
2. Whether a Watch App targeting API 3.4 can compile the required communications calls.
3. Phone-to-watch app-message delivery from a native Android test host.
4. Watch-to-phone delivery and phone-listener receipt, if the API permits it.
5. One-watch and two-watch pairing/selection behavior.
6. Maximum reliable message size, round-trip latency, retry behavior and disconnect recovery.
7. Application.Storage maximum reliable block, aggregate quota and storage-full behavior.
8. GPS behavior after app inactive/resume; haptic delivery; display legibility and route rendering.
9. Battery drain for a controlled route duration compared with a baseline activity.

A failed uplink test must result in a documented reduced Garmin feature set or a narrower compatible-device list. It must not be hidden behind a simulated ACK.

## 6. Map, search and routing review

### 6.1 Provider boundaries

The provider-neutral interfaces remain mandatory:

- Map renderer consumes licensed tile/style sources and normalized overlays.
- Geocoder returns canonical app-owned places plus source/provenance metadata.
- Router accepts explicit mode, ordered locations and supported avoidances, then returns normalized route geometry, maneuvers, warnings, source and calculation time.
- Garmin payloads are projected from the normalized selected route, not assembled from a second provider or a raw external API response.

No Google Routes/Places content is to be inserted into the OSM/MapLibre/Garmin pipeline under the current A2 policy analysis. komoot remains an experience reference only.

### 6.2 Motorbike and other modes

The motor_scooter profile is suitable as a first experiment for ordinary scooter/moped travel because its costing model accounts for motor-scooter access and road-class preferences. That is not a Vietnam legal-access certification. The route corpus must include expressway prohibitions, one-way streets, alleys, frontage roads, grade-separated crossings, restricted roads, gates, ferries/tolls and multi-stop sequences.

The car profile must be tested independently. Walking and bicycle routes must be tested independently. Never fall back from a failed motorbike request to car routing.

### 6.3 Multi-stop alternatives

Valhalla documentation states that route alternatives are not supported for multipoint routes with more than two locations. The UI must therefore show only alternatives actually returned by the selected provider. For longer itineraries, it may display the single ordered route and per-leg facts where the provider returns them; it must not fabricate whole-trip alternatives.

### 6.4 Map and offline rights

The public OpenStreetMap standard tile service is not an offline map-pack source and does not permit bulk prefetch. The first MVP must not expose “download city/region for offline use” unless a separate self-hosted or contracted tile source explicitly grants the required rights.

The tile vendor, style, attribution implementation, cache expiry and commercial terms must be recorded before production map distribution. The OSM data licence and the tile-provider contract are separate concerns.

## 7. Domain, transfer and navigation review

### Domain and persistence

- Keep JourneyPlan, Route, Trip and NavigationSession separate.
- Keep route revisions immutable.
- Keep provider references opaque and source metadata at adapter boundaries.
- Keep coordinates in the A3 WGS84 convention and units explicit.
- Keep phone database migrations, watch payload schema and transfer protocol versioning independent.
- Watch route blocks remain bounded. The 16 KB block is only an initial experiment; 32 KB is the documented per-value ceiling, not a recommended target for a whole route.
- Define a maximum route-point/maneuver/stop budget from measured fēnix 6S results rather than an assumed universal route distance.

### Transfer

The GN-NAV-2 stop-and-wait protocol, chunk checksums, whole-route digest, idempotent staging and atomic active-pointer replacement are retained. The current 128-byte chunk and 16 KB storage-block sizes are test values, not platform guarantees.

A4 must be revised only if compile/bench evidence requires a change. Any revised payload or ACK semantics must preserve the rule that incomplete or unverified data cannot replace the active route.

### Navigation engine

The route-matching architecture, confidence gates, progress model, one-shot maneuver alerts, off-route suspect/confirm hysteresis and terminal arrival behavior are accepted. The following remain tunable parameters, not production constants:

- stale-fix age and plausible displacement;
- speed gate for heading use;
- candidate segment search radius and cross-track corridor;
- confidence boundaries;
- maneuver alert lead time;
- off-route quorum/dwell and rejoin corridor;
- arrival radius/dwell and terminal transition.

Select them using deterministic replay fixtures first and controlled Hanoi field tests second. Record the dataset date, watch/phone firmware and policy version.

## 8. UX review and wireframe closure

A8 v2.0.0 is accepted as the interaction contract, but not as proof of usability. Build and review these low-fidelity wireframes before freeze:

**Phone**
1. Map home: app bar/search entry, map canvas, current-location state, quick mode selector, bottom navigation.
2. Search: query field, loading/empty/error states, recent searches and result list.
3. Place detail: place identity, sourced facts, missing-data treatment, Save, Add stop and Route actions.
4. Plan editor: origin, ordered stop list, add/reorder/remove, selected mode and calculate action.
5. Route preview: map/route geometry, mode, distance/ETA, warnings, supported alternatives and Start.
6. Active navigation: current maneuver, distance-to-turn, map context, remaining trip/leg summary, voice and pause/exit controls.
7. Trips: saved plans, recent/completed trips, trip detail and delete confirmation.
8. Garmin: compatible device card, exact selected target, app/reachability states, transfer progress and sent/received/validated/committed states.

**Base fēnix 6S**
1. HUD: large maneuver arrow, short instruction, distance-to-turn, secondary next-road/remaining-distance values and GPS state.
2. Route overview: simplified nearby route geometry, current-position marker and heading.
3. Trip status: remaining distance, trustworthy ETA if available, mode, phone-link and GPS state.
4. Pause/arrival/error: stable state, clear primary action, safe resume/exit and no false live countdown.

The round-screen test must use a 240 × 240 circular safe area, not a rectangular screenshot crop. Test Vietnamese diacritics, long street names, text scale, daylight contrast and physical-button navigation.

### Minimum qualitative usability session

Run a small, moderated and stationary task walkthrough with representative Vietnamese users before freeze. Include daily motorbike users and at least one intended Garmin user where available. Tasks:

- find a real Hanoi destination and identify the correct result;
- choose motorbike and interpret an access warning;
- add and reorder two intermediate stops;
- compare the route preview and recognize when alternatives are unavailable;
- start and pause navigation;
- interpret GPS loss versus internet loss versus phone/watch disconnection;
- understand “sent but not confirmed” versus “committed”;
- read the watch HUD and identify the next maneuver without operating a vehicle.

Record task completion, critical misunderstandings, observed failure, time-on-task and participant profile at a privacy-safe level. Do not run these tasks while anyone is riding or driving.

## 9. A9 closure gates

| Gate | Required evidence | Priority | Blocks |
|---|---|---|---|
| A9-G01 — Wireframes | All 8 phone and 4 watch low-fidelity layouts reviewed against A8 screen/state contract. | Must close before freeze | Design freeze |
| A9-G02 — Usability | Moderated task walkthrough, findings and resulting A8 updates recorded. | Must close before freeze | Design freeze |
| A9-G03 — Exact device | Exact fēnix 6S variant, firmware, Connect IQ SDK and API target recorded. | Must close before freeze | Design freeze |
| A9-G04 — Garmin bridge | Native Mobile SDK phone-to-watch, selected-device targeting, message-size and delivery behavior demonstrated; uplink/ACK outcome recorded honestly. | Must close before freeze | Design freeze and Garmin P0 release |
| A9-G05 — Routing corpus | Hanoi motorbike fixtures pass or each failure is explicitly blocked/warned; car fixtures and mode separation verified. | Must close before freeze | Design freeze and motorbike P0 release |
| A9-G06 — Map provider rights | Tile provider, attribution, caching, offline rights and commercial terms selected and documented. If offline rights are absent, offline map packs remain disabled. | Close before map release; provider-neutral interfaces may be implemented after freeze | Map release |
| A9-G07 — Watch payload budget | Route simplification, point/maneuver/stop caps, storage behavior and long-route limits measured on base 6S. | Must close before freeze | Design freeze and Garmin P0 release |
| A9-G08 — Navigation calibration | Deterministic trace suite passes; provisional thresholds are recorded; controlled field calibration plan and known limits documented. | Must close before freeze | Design freeze and navigation P0 release |
| A9-G09 — Platform bridge | Android native SDK host proof; iOS SDK bridge plan and separate verification path recorded. | Must close before freeze | Design freeze |
| A9-G10 — Privacy/license review | Data lineage, cache expiry, deletion, diagnostics and third-party terms checked for selected providers. | Close before external beta | External beta/release |

A9 can approve DESIGN FROZEN only when G01–G05 and G07–G09 are evidenced, or a specific gate is formally converted into an accepted limitation that does not invalidate a P0 claim. G06 and G10 may remain release gates only if the product disables the affected map/offline/third-party feature until closed.

## 10. Risk register

| Risk | Severity | Current treatment | Owner stage |
|---|---|---|---|
| fēnix 6S API 3.4 vs transmit() API 4.0.4 | Critical | Compile/runtime probe; do not assume uplink or ACK. | A9 |
| Garmin Mobile SDK multi-watch targeting | Critical | Native SDK proof; disable send if target is ambiguous. | A9 |
| Incorrect Hanoi motorbike access/routing | Critical | Build route fixtures and manual verification; reject/warn on uncertain access. | A9 |
| Watch route exceeds storage/runtime limits | High | Bounded encoding, geometry simplification and physical size benchmark. | A9 |
| Tile provider rights/cost/availability | High | Provider interface; select licensed vendor before distribution. | A9 / Phase C |
| GPS lifecycle and battery variability | High | Stale-fix gating, foreground app contract and physical trip measurements. | A9 / Phase F |
| UX clipping or misunderstood sync states | High | Round-screen wireframes and task-based usability. | A9 |
| Search relevance for Vietnamese addresses | Medium–High | Photon/Pelias comparison with Vietnamese test corpus. | A9 / Phase C |
| Live traffic and offline phone maps | Deferred | Keep out of P0 until a licensed, fresh source and explicit offline rights exist. | P1 |
| Raw GPS privacy | High | No raw trace retention by default; verify deletion and redacted diagnostics. | Phase G |

## 11. Implementation backlog after freeze

This backlog orders work; it does not authorize starting production code before the A9 exit decision.

### Phase B — Flutter foundation
- B-001: Flutter project, Android/iOS build targets, environment configuration and app bootstrap.
- B-002: Riverpod state architecture, navigation shell and design tokens.
- B-003: Drift/SQLite schema, migrations, repository layer and test fixtures.
- B-004: logging policy, redaction, linting, formatting, CI build and unit-test workflow.
- B-005: provider interfaces for map, geocoding, routing and Garmin device integration.

**Exit:** clean builds on the agreed development platforms; domain, persistence and adapter tests pass.

### Phase C — Phone map, search and routing
- C-001: MapLibre map shell, licensed tile integration, attribution and location permission states.
- C-002: Photon pilot and Vietnamese address/POI search evaluation; retain a Pelias adapter option.
- C-003: Valhalla adapter, normalized route model, explicit mode mapping and route warnings.
- C-004: ordered multi-stop planning, route preview and provider-returned alternatives only.
- C-005: phone turn-by-turn, voice prompts, GPS quality and online reroute.

**Exit:** phone P0 journeys pass route/search/navigation fixtures in supported regions and modes.

### Phase D — Garmin watch application
- D-001: Watch App manifest, API target, Positioning and lifecycle handling.
- D-002: base-6S Graphics.Dc renderer and 240 × 240 safe-area HUD.
- D-003: route matcher, progress, maneuver state, off-route and arrival logic.
- D-004: haptic policy, pause/resume and committed-route persistence.

**Exit:** simulator plus physical base-6S tests pass for the supported watch-local route-following scope.

### Phase E — Garmin bridge and transfer
- E-001: Android native Connect IQ Mobile SDK adapter exposed through Flutter platform channels.
- E-002: iOS native adapter and independent device/connection tests.
- E-003: selected-device discovery and unambiguous one-watch targeting.
- E-004: GN-NAV-2 chunking, staging, validation, commit, ACK and retry.
- E-005: reconcile confirmed watch events without accepting stale route revisions.

**Exit:** application-level confirmed transfer works on the explicitly supported model/platform matrix. If uplink is impossible on base 6S, revise the feature contract and device support before release.

### Phase F — Recovery, offline and performance
- F-001: active/pending recovery and crash/power-loss simulation.
- F-002: phone network loss, Bluetooth loss, watch app pause/resume and GPS reacquisition.
- F-003: route length, memory, CPU, storage writes and render budget.
- F-004: phone/watch battery measurements and performance tuning.

**Exit:** measured resource budgets and recovery behavior meet the project targets or the published supported-route/device limits are narrowed.

### Phase G — QA and release readiness
- G-001: execute A1/A2/A3/A4/A5/A6/A7/A8 acceptance suites.
- G-002: Hanoi controlled route tests and regression corpus.
- G-003: licensing, map attribution, provider terms and privacy/deletion review.
- G-004: accessibility, Vietnamese localization and outdoor readability review.
- G-005: beta packaging, crash diagnostics, support documentation and release checklist.

**Exit:** every P0 acceptance test is passed on a named build/provider/device matrix; known limitations are user-visible.

## 12. Design-freeze checklist

Do not change the repository's Design freeze field to Yes until all mandatory gates above contain:

- test date and environment;
- exact device model/firmware or phone OS/SDK where applicable;
- test procedure and reproducible input;
- expected and actual result;
- pass/fail/blocked status;
- evidence link or stored artifact path;
- decision for every failure: fix, reduce scope, accept with rationale or block release.

No evidence may be inferred from a written requirement, a vendor capability list or a successful phone-side send callback.

## 13. Official references checked on 2026-11-11

- Garmin Connect IQ compatible devices: https://developer.garmin.com/connect-iq/compatible-devices
- Garmin Communications API: https://developer.garmin.com/connect-iq/api-docs/Toybox/Communications.html
- Garmin Application Storage API: https://developer.garmin.com/connect-iq/api-docs/Toybox/Application/Storage.html
- Garmin Connect IQ SDK / Mobile SDK download: https://developer.garmin.com/connect-iq/sdk/
- Garmin MapTrackView supported devices: https://developer.garmin.com/connect-iq/api-docs/Toybox/WatchUi/MapTrackView.html
- Valhalla route API: https://valhalla.github.io/valhalla/api/route/api-reference/
- Valhalla route costing models: https://valhalla.github.io/valhalla/api/route/overview/
- OpenStreetMap tile usage policy: https://operations.osmfoundation.org/policies/tiles/
- OpenStreetMap copyright and licence: https://www.openstreetmap.org/copyright
- Google Maps Platform service terms: https://cloud.google.com/maps-platform/terms/maps-service-terms
- Google Routes two-wheeler documentation: https://developers.google.com/maps/documentation/routes/route_two_wheel
- komoot API statement: https://support.komoot.com/hc/en-us/articles/10331570510618-komoot-API

---

**A9 review result:** Traceability audit and backlog are recorded. DESIGN FROZEN remains **No** until the mandatory A9 closure gates are evidenced.