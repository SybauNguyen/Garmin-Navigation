# A2 — Map, Discovery & Routing Provider Specification

**Document ID:** GN-A2-002 · **Version:** 2.0.0 · **Date:** 2026-11-11  
**Status:** Recommended architecture; provider deployment and Vietnam route quality are not yet verified.

## 1. Scope

A2 translates product direction B into one coherent and licensable mapping/routing architecture. It defines the distinction between phone map rendering, basemap tiles, place search, route calculation, route discovery and Garmin navigation payloads.

## 2. Key decision: experience inspiration is not data integration

The UI may borrow familiar patterns from Google Maps (fast search, nearby places, route preview) and komoot (collections, multi-stop planning and saved routes). The project must not assume that these brands expose usable public APIs or grant rights to redistribute their content.

### 2.1 Google Maps Platform constraint

The current Google Maps Platform Service Specific Terms apply to customers whose billing account address is outside the EEA. Vietnam is outside the EEA. The current terms state that:
- Routes API content may be used without a corresponding Google Map, but must not be used in conjunction with a non-Google map.
- Places API content must not be used in conjunction with a non-Google map.
- Routes API latitude/longitude values may be temporarily cached for up to 30 consecutive calendar days, after which they must be deleted.
- Similar non-Google-map restrictions apply to Geocoding and other listed content APIs.

A Garmin route display based on Google route content alongside an OSM/Garmin map and longer-lived offline route cache is therefore **not approved by this baseline**. This is a product/engineering interpretation of the published terms, not legal advice. Any future Google-only implementation needs a separate architecture and written legal review of the intended phone-to-watch data flow.

Google does document two-wheel routing in Vietnam, but the Routes API guidance says two-wheel routes are beta, can be non-optimal, and must carry a user warning. It triggers higher-priced billing for two-wheel requests. This is not enough to overcome the cross-platform content/retention issue for the primary Garmin route pipeline.

### 2.2 komoot constraint

komoot's official support article (updated 2026-03-03) says it does not provide a publicly accessible API. Therefore:
- use komoot only as a product/UX reference;
- do not scrape komoot, automate private endpoints, copy route catalogues or imply partnership;
- build our own place/route discovery layer on licensed/open or independently contributed data.

## 3. Recommended technical stack

| Concern | Candidate | Role | State |
|---|---|---|---|
| Phone map renderer | MapLibre-compatible renderer | Interactive basemap, route line, markers and layers | Recommended candidate |
| Basemap | Licensed OSM-derived vector tiles | Shared phone geography and consistent visual identity | Vendor/hosting not selected |
| Routing | Self-hosted Valhalla on OSM extracts | Route calculation and maneuvers | Provisional primary |
| Geocoder / place search | Photon pilot | Text search, reverse lookup and result identity | Pilot candidate |
| Search comparison | Pelias | Alternative if Photon coverage/quality is insufficient | Evaluation candidate |
| Discovery catalogue | App-owned data layer | Curated collections, tags, saved journeys and user notes | Required product layer |
| Watch route renderer | Garmin MapView where supported; otherwise custom Graphics.Dc renderer | Small route overview and current-position/turn HUD | Device-gated |

## 4. Travel-mode mapping

| User-facing mode | Valhalla costing candidate | Notes / release requirement |
|---|---|---|
| Xe máy phổ thông / scooter | `motor_scooter` | First candidate. Avoids roads without motor_scooter/moped/mofa access and generally avoids higher-class roads unless configured for regional overrides. Field validation mandatory. |
| Touring/adventure motorcycle | `motorcycle` | Explicitly beta in Valhalla; supports road-touring vs tracks/trails options. Not a default substitute for ordinary Vietnam motorbike routing. |
| Ô tô | `auto` | Must honor vehicle access, one-way and turn restrictions. |
| Đi bộ | `pedestrian` | Human-powered pedestrian network; never reuse bicycle/motor routes. |
| Xe đạp | `bicycle` | Bicycle access, cycleway and surface preferences. |
| MTB/trekking | Later profile review | Need terrain, trail legality and surface-specific QA. |

A route mode must affect graph eligibility/costing. A label change without a corresponding routing profile is a defect.

## 5. Vietnam motorbike routing requirements

The route engine and OSM extract must be tested against:
- motorbike-accessible vs motorbike-prohibited segments;
- motor_vehicle/motorcycle/moped access tags and conditional restrictions where present;
- one-way direction and exceptions;
- motorway/expressway restrictions and links;
- bridges, tunnels, elevated roads, underpasses and grade-separated crossings;
- alleys and narrow local roads;
- turn restrictions, U-turn restrictions, private/restricted roads and gates;
- ferry/toll segments;
- route continuation across administrative boundaries;
- multi-stop plans whose order must not change.

A routing result is not proof of legal access. In areas with missing/conflicting tags, apply conservative handling, display a caution or reject the route until verified. Road signs and local law take precedence.

## 6. Routing request/response contract

Provider adapters must implement an app-owned interface rather than leak Valhalla response structures into UI/domain code.

### Request fields
- origin coordinate and optional heading;
- destination coordinate;
- ordered intermediate stops;
- canonical travel mode and profile configuration;
- avoidances only where provider supports them;
- requested language/units;
- request timestamp and routing-policy version.

### Normalized route fields
- app route ID and immutable revision;
- provider name/version and provider route reference;
- mode/profile ID;
- route geometry and shape encoding;
- legs and ordered stops;
- maneuvers with canonical maneuver type, shape/route anchor and road-name provenance;
- distance (cm), duration (s), optional traffic-derived duration with source/time;
- route warnings, toll/ferry/access notes and limitations;
- calculation timestamp and attribution/license metadata.

Provider alternative routes are optional. Valhalla's documentation describes limitations for alternatives on multipoint routes; do not fabricate alternatives for a multi-stop plan. If fewer alternatives are returned than requested, show the available count only.

## 7. Search and discovery

- Photon is the first evaluation pilot, not a frozen provider.
- Compare Photon and Pelias using Vietnamese address spelling, diacritics, abbreviations, house numbers, alley names, POIs, reverse geocoding and ambiguous matches.
- Place results carry provider ID, source, coordinate, retrieved time, attribution and field-level provenance.
- User-created saved places/notes are separated from provider data.
- Discovery collections are app-owned and can group public, licensed or user-created places. Do not copy komoot's catalogue or media.
- Ratings, hours, pricing and photos are not required for P0 and must never be invented.

## 8. Map tile, license and offline rules

- OpenStreetMap data is ODbL open data; provide visible attribution to OpenStreetMap and contributors and comply with share-alike obligations when distributing adapted databases.
- OSM's standard public tile servers are best-effort and have no SLA. Their usage policy prohibits bulk downloading/prefetch and offline-region features on `tile.openstreetmap.org`.
- Production must use a licensed tile provider that explicitly permits the expected app volume and any offline/prefetch function, or self-host tiles.
- Never use public standard OSM tiles for "download city/area for offline".
- Put the tile endpoint and style configuration behind remote/app configuration so providers can be changed without rewriting map UI.
- The phone's offline basemap is a later feature unless a compliant licensed provider is selected. Watch receives route geometry/instructions only, not a full tile package.

## 9. Traffic policy

The primary OSM/Valhalla baseline does not imply live traffic. Traffic overlays/ETAs may be added only from a separate licensed provider with source timestamp, freshness, coverage and attribution. Stale/no traffic data must be labelled unavailable, not "live". Traffic must not silently change legal access rules.

## 10. Vietnam validation suite

Create a route fixture suite before freezing provider selection:
1. Hanoi local streets with legal motorbike access.
2. Expressway/motorway where motorbikes are prohibited.
3. One-way street and wrong-direction approach.
4. Alley/small local road with uncertain width/access.
5. Bridge and elevated road with parallel frontage road.
6. Tunnel/underpass and grade-separated crossing.
7. Restricted/private road and gate.
8. Ferry/toll road where supported.
9. Motorbike route crossing a car-only link.
10. Multi-stop trip with a required ordered sequence.
11. City-to-suburb boundary and route continuity.
12. Walking/bicycle comparison where legal access differs from motorized access.

For every fixture record origin, destination, selected mode, expected road-access behavior, map-data date, provider version, returned path, reason for pass/fail and manual verifier. Include screenshots or traces only with appropriate privacy protection.

## 11. Acceptance criteria

- A2-AT-001: each UI mode maps to a separate explicit provider costing/profile.
- A2-AT-002: motorbike route excludes a fixture segment marked motorbike-prohibited.
- A2-AT-003: one-way restrictions are respected for all relevant modes.
- A2-AT-004: no route silently falls back from motorbike to car.
- A2-AT-005: uncertain access produces warning/rejection rather than false certainty.
- A2-AT-006: provider response is normalized without provider-specific fields leaking into domain objects.
- A2-AT-007: missing alternative routes do not generate placeholder/fake options.
- A2-AT-008: multi-stop order is retained and provider limitations are explicit.
- A2-AT-009: Vietnamese search comparison records relevance, spelling and coordinate errors.
- A2-AT-010: POI fields include source/provenance and missing values remain missing.
- A2-AT-011: map attribution remains visible at supported display sizes.
- A2-AT-012: offline tile feature is disabled unless provider terms explicitly permit prefetch/download.
- A2-AT-013: traffic is not presented as live without a fresh licensed data source.
- A2-AT-014: all watch-transfer route data derives from the normalized selected route and records the source provider.
- A2-AT-015: Google content is not piped into the non-Google/Garmin route pipeline.
- A2-AT-016: no komoot scraping/private API dependency exists.

## 12. Decisions still open

- Self-hosted Valhalla hardware/hosting budget and update cadence.
- Photon vs Pelias based on Hanoi/Vietnam test results.
- Licensed tile vendor, style and offline rights.
- Whether traffic becomes P1 and which provider can supply it with suitable terms.
- Exact OSM extract scope and data update process.
- Whether alternatives are available for selected multi-stop use cases.

## 13. Official references

- Google terms: https://cloud.google.com/maps-platform/terms/maps-service-terms
- Google Routes two-wheeler guide: https://developers.google.com/maps/documentation/routes/route_two_wheel
- Google two-wheeler coverage (Vietnam listed): https://developers.google.com/maps/documentation/routes/coverage-two-wheeled
- komoot API policy: https://support.komoot.com/hc/en-us/articles/10331570510618-komoot-API
- Valhalla Route API: https://valhalla.github.io/valhalla/api/route/api-reference/
- Photon: https://github.com/komoot/photon
- Pelias: https://pelias.io/
- MapLibre: https://maplibre.org/
- OSM Tile Usage Policy: https://operations.osmfoundation.org/policies/tiles/
- OSM Copyright and License: https://www.openstreetmap.org/copyright
