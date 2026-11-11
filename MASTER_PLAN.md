# Garmin Navigation — Master Product & Engineering Plan

**Document ID:** GN-MASTER-001  
**Version:** 1.0.0  
**Updated:** 2026-11-11  
**Status:** A0–A7 re-baselined; A8 UX specification next; not design-frozen  
**Primary target:** Flutter phone app + Garmin Connect IQ app for fēnix 6S family

## 1. Product direction

The project adopts **direction B: Google Maps + komoot** as a product-experience reference:

- Google Maps-like utility: fast search, current-location map, destination selection, practical route comparison, daily turn-by-turn navigation.
- komoot-like journey layer: multi-stop planning, saved routes/places, route notes and curated discovery.
- Differentiator: travel modes include motorized two-wheelers and cars, while navigation can be handed to a Garmin fēnix 6S.

The product is not affiliated with or a clone of Google or komoot. It must not scrape their websites or rely on non-public APIs. "Google Maps + komoot" is not a decision to combine their map or route datasets.

## 2. Target users and region

Initial validation region is Vietnam, starting with Hanoi. The first high-priority use case is everyday motorbike navigation. Secondary use cases are car journeys, walking and bicycle trips. Discovery and planned journeys expand the app beyond a simple Garmin route uploader.

The exact customer segment, commercial model, cloud account strategy and release geography remain open and are not needed to freeze the first local MVP.

## 3. Product surfaces

1. **Phone app — primary:** map, search, place details, route preview, turn-by-turn, audio, discovery, multi-stop plans, trip history, settings and Garmin management.
2. **Garmin fēnix 6S — companion:** glanceable next-turn instruction, distance-to-turn, simple route display, GPS/route state, haptic cue and cached-route continuation within documented device limits.

Phone and watch capabilities are intentionally asymmetric. Full place search, map exploration, multi-stop editing and rerouting belong on the phone.

## 4. MVP boundary

### P0
- Onboarding and clear location/permission status.
- Interactive phone map with licensed basemap and current location.
- Search for addresses, named places and POIs; select a pin manually as fallback.
- Route calculation for motorbike/scooter, car, walking and bicycle, only where the configured provider profile supports them.
- Route preview with route geometry, distance, duration, instructions and provider warnings.
- Basic multi-stop route planning and stop reordering; if the selected engine does not support an operation, explain it rather than silently dropping stops.
- Phone turn-by-turn navigation with visual cue, distance-to-turn, optional spoken prompts, off-route detection and network-dependent rerouting.
- Garmin app install/reachability check, selected-device workflow, safe route transfer, compact watch guidance and visible sync status.
- Cached route following on watch only after transfer integrity/activation is confirmed.
- Trip summary and locally saved recent destinations.

### P1
- Alternative route comparison when provider supports it.
- Curated route collections and discovery categories (food, viewpoints, scenic stops, places to visit).
- Saved journeys, route notes, GPX export/import where licensing and privacy allow.
- More refined activity profiles (trekking/MTB) after a separate access and safety review.
- Traffic information from a licensed provider, clearly separated from base road/route data.
- Phone offline map areas from a provider that explicitly permits offline downloads.

### P2 / explicitly deferred
- Public transit, social feed, comments/ratings, public live-location sharing, user accounts/cloud sync, subscription tiers, fleet tracking, EV charging optimization and full offline maps on the watch.
- Generic global "all vehicles" support.
- Claims of traffic-aware or legally certified navigation.

## 5. Modes and routing policy

| User-visible mode | First candidate profile | Release rule |
|---|---|---|
| Xe máy / scooter (primary) | Valhalla `motor_scooter` | Must pass Vietnam access/field fixtures; profile label is not legal classification |
| Larger motorcycle / touring | Valhalla `motorcycle` (beta) | Do not expose until profile and route behavior are validated |
| Ô tô | Valhalla `auto` | Validate one-way, turn restrictions, road class and restrictions |
| Đi bộ | Valhalla `pedestrian` | Validate pedestrian access; do not reuse bicycle route |
| Xe đạp | Valhalla `bicycle` | Validate cycleway/road access |
| MTB / trekking | Separate later assessment | Not included in initial mode picker |
| EV | Later assessment | Requires reliable vehicle range/charging data |

A travel mode must change the routing engine's costing/access behavior, not just its icon. Never claim a road is legal for a mode from a provider profile name alone. Unknown or conflicting access data must produce a conservative route/warning or block the route.

## 6. Architecture baseline

### Phone
- Flutter/Dart.
- Riverpod or an equivalent explicit state-management layer (final choice in A9).
- MapLibre-compatible renderer with an OSM-derived tile source under a production license.
- Provider-neutral routing/search interfaces.
- Provisional routing engine: self-hosted Valhalla on OSM extracts.
- Provisional search/geocoder: Photon pilot; compare Pelias for Vietnam.
- Local persistence: SQLite through Drift or an equivalent migration-capable repository.
- Garmin Connect IQ Mobile SDK adapter behind an app-owned interface. The current community wrapper is not accepted until selected-device targeting is solved.

### Watch
- Garmin Connect IQ Watch App written in Monkey C.
- API 3.4 minimum candidate for fēnix 6S family.
- Route repository, transfer manager, GPS qualification, bounded route matcher, maneuver/alert state and renderer abstraction.
- Base 6S: custom renderer using supported graphics primitives; do not call native MapView.
- 6S Pro/Sapphire: native MapView/MapTrackView path may be used only after exact model check and tests; custom fallback remains.

### Provider separation
Provider-specific records, IDs and license/provenance metadata must not leak into canonical domain types. Any Google Maps Platform integration is a separately isolated provider mode requiring terms review. Do not combine Google Routes/Places-derived content with OSM/Garmin maps or persist restricted content outside allowed terms.

## 7. Phase A gates

| Gate | Package | Deliverable |
|---|---|---|
| A0 | Product baseline | Product vision, users, modes, MVP and non-goals |
| A1 | Functional requirements | Observable requirements, journeys, acceptance criteria |
| A2 | Map/discovery/routing | Provider policy, data/licensing, profiles, Vietnam validation |
| A3 | Domain/data model | Canonical entities, units, state and persistence contracts |
| A4 | Garmin sync protocol | Versioned payload, transfer states, integrity and fallback |
| A5 | Garmin capability | Exact device capabilities and hardware probes |
| A6 | Navigation engine | GPS qualification, matching, maneuvers, alerts, reroute and arrival |
| A7 | Offline/recovery/privacy/battery | Failure policies, retention, power and recovery |
| A8 | UX specification | Information architecture, screens, state catalog, interaction and accessibility |
| A9 | Design review/freeze | Resolve blockers or explicitly accept documented limits; freeze implementation backlog |

**No production implementation before A9.** A package can be specification-complete while its real-device validation remains open.

## 8. Implementation phases after design freeze

- **B — Flutter foundation:** project shell, architecture, dependency/toolchain pinning, CI and test framework.
- **C — Phone map/search/routing:** renderer, provider adapters, route normalization and local trip persistence.
- **D — Garmin app:** watch app lifecycle, renderer, GPS and route matching.
- **E — Phone/watch integration:** SDK bridge, device selection, transfer protocol and reconciliation.
- **F — Offline/performance:** route persistence, restart recovery, long-route, battery and memory profiling.
- **G — QA/release:** acceptance tests, field tests, privacy/license review, documentation and packaging.

Do not skip gates because a demo appears to work.

## 9. Risks and release blockers

- A valid route for one mode may be invalid for another; mode-specific route fixtures are mandatory.
- OSM access tagging and coverage may be incomplete or wrong on local roads; field verification is essential.
- Google Maps Platform's current non-EEA terms restrict Routes/Places/Geocoding content from use with non-Google maps and impose temporary coordinate caching limits; a Google API-based Garmin pipeline is not approved by this baseline.
- komoot states it has no publicly accessible API.
- fēnix 6S base has no MapView listing; Pro/Sapphire does.
- `Communications.transmit()` has a current documentation/API-level discrepancy for 6S; app-level ACK cannot be assumed.
- 32 KB per-value watch storage cap and device-varying total quota constrain payload blocks.
- Watch GPS events, foreground lifecycle and battery need exact-hardware testing.
- Routing, GPS matcher, off-route/arrival thresholds need field calibration.
- A normal public OSM tile endpoint is not an offline tile distribution service.

## 10. Current status

A0–A7 have been rewritten for direction B at specification level on 2026-11-11. A8 is the next package. No code, provider deployment, simulator tests or physical-device tests are claimed. See [PROJECT_STATUS.md](PROJECT_STATUS.md).

## 11. Primary references

- Garmin compatible devices: https://developer.garmin.com/connect-iq/compatible-devices
- Garmin MapView: https://developer.garmin.com/connect-iq/api-docs/Toybox/WatchUi/MapView.html
- Garmin Communications: https://developer.garmin.com/connect-iq/api-docs/Toybox/Communications.html
- Garmin Application Storage: https://developer.garmin.com/connect-iq/api-docs/Toybox/Application/Storage.html
- Garmin Position: https://developer.garmin.com/connect-iq/api-docs/Toybox/Position.html
- Google Maps Platform terms: https://cloud.google.com/maps-platform/terms/maps-service-terms
- Google two-wheeler API: https://developers.google.com/maps/documentation/routes/route_two_wheel
- komoot API statement: https://support.komoot.com/hc/en-us/articles/10331570510618-komoot-API
- Valhalla Route API: https://valhalla.github.io/valhalla/api/route/api-reference/
- OSM tile policy: https://operations.osmfoundation.org/policies/tiles/
- OSM copyright/license: https://www.openstreetmap.org/copyright
