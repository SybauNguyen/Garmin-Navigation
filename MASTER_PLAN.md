# Garmin Navigation — Master Product & Engineering Plan

**Document ID:** GN-MASTER-001  
**Version:** 1.1.0  
**Updated:** 2026-11-11  
**Status:** A0–A8 complete at specification level; A9 design review/freeze next  
**Primary target:** Flutter phone app + Garmin Connect IQ app for fēnix 6S family

## 1. Product direction

Adopt **direction B: Google Maps + komoot experience**:
- Google Maps-like utility: fast search, current-location map, destination selection, practical route comparison, daily turn-by-turn.
- komoot-like journey layer: multi-stop planning, saved routes, route notes and discovery.
- Differentiator: Vietnamese motorbike support and glanceable Garmin route guidance.

This is a product-design reference, not affiliation, cloning, scraping or permission to reuse data. It is not a decision to combine Google/komoot APIs or content.

## 2. Target users and region

Initial validation region is Vietnam, starting with Hanoi. Everyday motorbike navigation is the first priority. Secondary modes are car, walking and bicycle. Discovery and planned journeys extend the product beyond a route uploader.

Customer segment, commercial model, cloud account strategy and release geography remain open.

## 3. Product surfaces and information architecture

Phone primary navigation has five destinations: **Map, Explore, Plan, Trips, Garmin**. Settings/profile are reached from the app bar. Active navigation is a dedicated full-screen guidance mode.

- **Phone:** map, search, place details, route preview, turn-by-turn, discovery, multi-stop plans, trip history, settings and Garmin management.
- **Garmin:** compact next-turn HUD, turn distance, simple route overview, GPS/route state and haptic cue. It does not replicate phone search/plan/discovery.

See A8 for screen hierarchy, states, interaction, accessibility and acceptance criteria.

## 4. MVP boundary

### P0
- Map/search/pin selection and clear permission/location states.
- Route preview by explicit mode: scooter/motorbike, car, walk, bicycle.
- Basic multi-stop planning and stop order retention within provider capability.
- Phone turn-by-turn, voice preference, GPS quality state, off-route confirmation and online reroute.
- Garmin compatibility/device selection, validated route transfer and truthful sync status.
- Compact watch guidance and cached-route continuation after confirmed commit.
- Recent/saved destinations, trip summary and user deletion controls.
- Essential error/empty/offline states.

### P1
- Curated discovery collections, nearby categories, saved journeys and route notes.
- Alternative routes where supported.
- Licensed traffic and incident source.
- Phone offline map regions only where provider explicitly permits offline download.
- Hiking/MTB after routing/access review.

### P2 / deferred
Public social feed, comments/ratings, public live-location sharing, cloud accounts/sync, subscriptions, fleet tracking, public transit, EV charging optimization and full offline map on watch.

## 5. Modes and routing policy

| User-visible mode | Candidate | Release rule |
|---|---|---|
| Xe máy / scooter | Valhalla `motor_scooter` | Must pass Vietnam access/field fixtures; not a legal vehicle class |
| Touring motorcycle | Valhalla `motorcycle` (beta) | Do not expose until separately validated |
| Ô tô | Valhalla `auto` | Validate road class, access, one-way and turn restrictions |
| Đi bộ | Valhalla `pedestrian` | Validate pedestrian access |
| Xe đạp | Valhalla `bicycle` | Validate bicycle access |
| MTB/trekking | Later assessment | Not initial mode promise |
| EV | Later assessment | Needs reliable range/charging data |

A mode must alter graph costing/access, not just the icon. Unknown or conflicting access data must not be disguised as certainty.

## 6. Architecture baseline

### Phone
Flutter/Dart; Riverpod or equivalent explicit state management (freeze at A9); MapLibre-compatible renderer; licensed OSM-derived tiles; provider-neutral routing/search interfaces; provisional self-hosted Valhalla; Photon pilot vs Pelias; migration-capable SQLite/Drift; Garmin Connect IQ Mobile SDK adapter with selected-device targeting.

### Watch
Connect IQ Watch App in Monkey C; API 3.4 minimum candidate; active/pending route store; GPS qualification, bounded matcher, maneuver/alert state and renderer abstraction. Base 6S uses custom Graphics.Dc renderer. 6S Pro/Sapphire may use native MapView only after exact capability check and tests; retain custom fallback.

### Provider separation
Keep provider IDs, cache expiry, attribution and license metadata at adapter boundaries. Google data is not approved for the Garmin/OSM route pipeline under the current non-EEA restrictions. komoot API/data dependency is not available under current public API policy.

## 7. Phase A gates

A0 product baseline, A1 requirements, A2 map/discovery/routing, A3 domain model, A4 sync protocol, A5 Garmin capability, A6 navigation engine, A7 offline/privacy/battery, A8 UX specification and A9 formal design review/freeze.

A0–A8 are complete at specification level. A9 must audit requirement traceability, open risks, provider/legal conditions, hardware constraints, testability and implementation backlog.

**No production implementation before A9 explicitly marks DESIGN FROZEN.**

## 8. Implementation phases after design freeze

- B Flutter foundation, toolchain and test infrastructure.
- C Phone map/search/routing and route normalization.
- D Garmin watch app, renderer, GPS and route matching.
- E Garmin SDK bridge, selected-device transfer and reconciliation.
- F Offline/recovery, performance and battery optimization.
- G QA, field tests, licensing/privacy review and packaging.

## 9. Risks and release blockers

- OSM motorbike access quality and Vietnamese local road restrictions need field verification.
- Google Routes/Places/Geocoding content has non-Google map restrictions for non-EEA accounts and route-coordinate cache limits; no mixed-provider pipeline without legal review.
- komoot has no publicly accessible API.
- fēnix 6S base lacks a MapView listing; Pro/Sapphire has.
- `Communications.transmit()` API-level discrepancy means uplink cannot be assumed.
- Watch storage max 32 KB/value, aggregate quota varies.
- Phone/watch lifecycle, GPS and battery need exact-hardware tests.
- Routing, GPS matching, alert, off-route and arrival thresholds need field calibration.
- Standard OSM tile endpoint cannot be used for bulk offline downloads.

## 10. Current status

A0–A7 re-baselined at revision 2.0.0 and A8 UX specification v1.0.0 are committed to GitHub. No code, provider deployment, acceptance test, usability study or physical-device test is claimed. A9 is next. See [PROJECT_STATUS.md](PROJECT_STATUS.md).

## 11. Primary references

- Garmin compatible devices: https://developer.garmin.com/connect-iq/compatible-devices
- Garmin MapView: https://developer.garmin.com/connect-iq/api-docs/Toybox/WatchUi/MapView.html
- Garmin Communications: https://developer.garmin.com/connect-iq/api-docs/Toybox/Communications.html
- Garmin Application Storage: https://developer.garmin.com/connect-iq/api-docs/Toybox/Application/Storage.html
- Google Maps Platform terms: https://cloud.google.com/maps-platform/terms/maps-service-terms
- Google two-wheeler API: https://developers.google.com/maps/documentation/routes/route_two_wheel
- komoot API statement: https://support.komoot.com/hc/en-us/articles/10331570510618-komoot-API
- Valhalla Route API: https://valhalla.github.io/valhalla/api/route/api-reference/
- OSM tile policy: https://operations.osmfoundation.org/policies/tiles/
- OSM copyright/license: https://www.openstreetmap.org/copyright
