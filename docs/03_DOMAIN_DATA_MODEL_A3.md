# A3 — Domain & Data Model

**Document ID:** GN-A3-002 · **Version:** 2.0.0 · **Date:** 2026-11-11  
**Status:** Canonical model proposal; no schema/migration code has been implemented.

## 1. Design goals

- Keep phone, routing provider, discovery layer and Garmin payload independent.
- Preserve source/provenance/licensing for places, routes and map-derived values.
- Support distinct travel modes and vehicle profiles.
- Make routes immutable and navigation sessions stateful.
- Send only the minimum navigation information required to the watch.
- Keep all cross-platform units and identifiers deterministic.

## 2. Canonical entities

| Entity | Purpose | Required fields / notes |
|---|---|---|
| UserPreferences | Local preferences | defaultMode, units, voice, alert preferences, map style |
| TravelMode | User-visible transport | enum: scooter, motorcycle, car, walk, bicycle; future modes versioned |
| VehicleProfile | Detailed routing constraints | mode, profileId, topSpeed/vehicle attributes where meaningful, avoidances |
| Place | Canonical user-facing place | placeId, name, optional address, coordinate, type, provenance |
| ProviderReference | External provider link | provider, opaque providerId, retrievedAt, license/policy reference |
| SearchQuery | Search history state | query text, mode/category, timestamp; retention bounded |
| DiscoveryCollection | Curated group of places/routes | collectionId, title, source, items, visibility |
| SavedPlace | User-owned favourite | userId/localOwnerId, place reference, label, note, createdAt |
| JourneyPlan | Multi-stop intent | ordered waypoint IDs, selected mode/profile, preferences |
| Waypoint | Plan/route stop | waypointId, coordinate, optional placeId, stopType, sequence |
| Route | Immutable calculated result | routeId, revision, mode/profile, provider, summary, geometry, legs, warnings |
| RouteLeg | Segment between stops | legId, start/end waypoint, geometry range, distanceCm, durationSec |
| RouteShape | Encoded geometry | encoding, coordinates/encoded bytes, precision, bounds, source |
| Maneuver | Turn instruction | canonical type, routeOffsetCm, shapeIndex, road labels and provenance |
| RouteWarning | Limitation/risk metadata | code, severity, text key, source and affected range |
| Trip | User's completed/in-progress journey | tripId, routeId/revision, start/end, status and summary |
| NavigationSession | Live route-following state | sessionId, tripId, active route revision, progress, lifecycle |
| GPSFix | Location sample | latE6, lonE6, capturedAtUtcMs, source, quality, optional speed/heading |
| RouteMatch | Matcher output | segment index, offsetCm, crossTrackCm, confidence, eligible flag |
| ManeuverState | Current instruction state | maneuverId, alert stages, passed/confirmed state |
| GarminDevice | Selected paired watch | opaque deviceId, model, firmware, selected flag |
| DeviceCapabilities | Runtime/verified capability | apiLevel, map renderer, storage, uplink, supported feature flags |
| TransferSession | Phone-watch transaction | transferId, routeId/revision, state, chunk count, hash, target device |
| TransferChunk | Bounded message piece | sequence, payload, checksum, durable status |
| WatchRouteSnapshot | Cached route on watch | compact route, active/pending slot, route hash, schema version |
| WatchNavigationSnapshot | Restart checkpoint | session state, progress, maneuver state, last good fix time |
| DiagnosticEvent | Minimal troubleshooting | event type, timestamp, code, redacted metadata; no raw trace by default |
| OfflineCacheEntry | Cached licensed/user data | content type, source, license, expiry, bytes and owner |

## 3. Identifier and provider separation

- Canonical IDs are UUID v4 strings unless a protocol/embedded-device limitation requires a bounded opaque identifier.
- Provider IDs remain opaque strings and are never used as primary database keys.
- No UI/business rule assumes a provider ID can be decoded or used against another provider.
- User-saved place identity remains stable if the geocoder/provider changes; provider references may be replaced.
- Provider-specific records belong in adapter/storage boundaries and map to canonical models through explicit normalization.
- Record provenance: source/provider, retrieval timestamp, attribution/license identifier and any expiry/retention condition.

## 4. Units and coordinate conventions

| Value | Canonical representation |
|---|---|
| Coordinates | WGS84 latitude/longitude in signed E6 integers for persisted/protocol models |
| Distances and route offsets | Integer centimetres |
| Durations | Integer seconds |
| Heading | Integer deci-degrees [0, 3600), nullable |
| Speed | Integer cm/s, nullable |
| Timestamps | UTC epoch milliseconds; display-localized |
| Route geometry | Polyline6 or explicitly versioned alternative; never infer precision |
| Elevation | Optional metres with datum/source metadata |

Reject coordinates outside valid WGS84 ranges. Null, unknown and zero are not interchangeable (e.g. unknown ETA must not be stored as zero).

## 5. Route immutability and revision rules

- A calculated route has a route ID and immutable revision.
- Recalculation creates a new route identity or a new immutable revision; it must not mutate a route already used by an active transfer.
- The route revision includes mode/profile, provider, waypoint order, geometry and maneuver sequence.
- Transfer, GPS match, maneuver, trip and watch state are scoped to the exact route ID/revision.
- A late chunk or event from an old revision is rejected.
- Trip history points to the exact route revision used at start.

## 6. Separation of intent, route and navigation

- `JourneyPlan` is editable intent: origin, ordered stops, mode and preferences.
- `Route` is the immutable provider-calculated result.
- `NavigationSession` is runtime execution state: active/pause/arrival, current offset, next maneuver.
- `Trip` is a retained user record and summary.
- `TransferSession` is a short-lived phone-watch transport transaction.
- `WatchRouteSnapshot` and `WatchNavigationSnapshot` are bounded watch persistence records.
- Discovery content is not required for route following and is not copied to the watch.

## 7. Persistence baseline

### Phone
- SQLite (Drift candidate) for preferences, saved places, journey plans, route summaries, active route/session and trip history.
- Persist provider cache only according to the provider's terms and expiration policy.
- Map tiles are owned by the selected licensed tile provider's cache policy.
- Raw GPS samples are transient by default; trip summary is retained only according to A7.

### Watch
- Garmin Application.Storage for compact active/pending route blocks and navigation snapshots.
- Per-value maximum is 32 KB; the total Object Store quota varies by device. Start with conservative blocks and profile actual limits.
- Do not store full basemap tiles or POI catalogs.
- Route commit and checkpoint writes must tolerate power loss and storage-full exceptions.

## 8. Garmin payload projection

A3 canonical domain data is richer than the watch payload. A dedicated projection strips:
- photos, ratings, hours, discovery collection metadata;
- provider search documents and raw API response;
- phone map tile references;
- unnecessary addresses and personal history.

Retain only:
- route identity/revision and selected mode/profile label;
- ordered route shape sufficient for watch display and matching;
- required maneuver anchors, canonical types and compact localized text;
- remaining distance/duration snapshot if allowed by provider data lineage;
- route warnings/limitations relevant to navigation;
- schema/protocol version and integrity data.

## 9. Schema evolution

- Version all persisted DB schemas, route payload schemas and protocol versions independently.
- Add optional fields backward-compatibly where practical.
- Unknown enum values must produce a controlled unsupported state, not crash or default to car.
- Migration must preserve or safely invalidate active navigation. Never silently reinterpret a stored route under a new mode/profile definition.

## 10. A3 acceptance criteria

- A3-AT-001: canonical IDs are independent of provider IDs.
- A3-AT-002: coordinates and units round-trip without precision drift beyond declared encoding.
- A3-AT-003: unknown ETA/speed/heading remain null, not zero.
- A3-AT-004: route record is immutable after transfer start.
- A3-AT-005: reroute creates a distinct revision and resets state only after commit.
- A3-AT-006: old-revision chunks/events cannot affect a new active route.
- A3-AT-007: a saved place remains usable when provider reference is refreshed.
- A3-AT-008: provenance/attribution survives place and route normalization.
- A3-AT-009: user-owned discovery notes are separate from third-party source fields.
- A3-AT-010: watch payload omits non-navigation discovery content.
- A3-AT-011: storage-full behavior does not corrupt active route.
- A3-AT-012: schema migration safely restores or invalidates a session after restart.

## 11. Open data decisions

- Final SQLite/Drift package and migration strategy.
- Provider cache/expiry registry after provider contract review.
- Route geometry encoding and simplification tolerance for fēnix 6S.
- Maximum watch route length/stop count, to be measured against payload/storage limits.
- Whether elevation and route surface fields are in P1.
