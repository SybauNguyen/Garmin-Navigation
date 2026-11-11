# A7 — Offline, Recovery, Privacy & Battery Policy

**Document ID:** GN-A7-002 · **Version:** 2.0.0 · **Date:** 2026-11-11  
**Status:** Policy specification; implementation and physical measurements pending.

## 1. Offline scope

Offline support in the MVP means **continuing a previously calculated and committed route**, not:
- searching arbitrary places without cached data;
- calculating a new route without a routing graph;
- receiving live traffic or incident updates;
- downloading a full basemap to the fēnix 6S;
- promising background navigation while the watch app is inactive.

The phone may retain the current route and user-saved items locally. A full offline map area is P1 and requires a tile provider whose license explicitly permits offline downloads.

## 2. Data classes and source of truth

| Data | Phone | Watch | Source of truth |
|---|---|---|---|
| Saved places/journeys | Local DB | Not required | Phone |
| Provider search cache | Only within provider terms/expiry | Not transferred | Provider-specific policy |
| Basemap tiles | Licensed cache | No full tile set in MVP | Tile provider |
| Active normalized route | Yes | Compact committed projection | Phone route record; watch owns local active projection while disconnected |
| Pending transfer | Transfer staging | Pending slot | Transfer state machine |
| Active navigation progress | Session DB | Watch checkpoint | Watch progress for watch-local guidance; reconcile only through versioned events |
| Raw GPS trace | Transient by default | Transient by default | Not retained unless user opts in |
| Trip summary | Local DB | Optional minimal snapshot | Phone after reconciliation |

## 3. Recovery scenarios

| Failure | Required behavior |
|---|---|
| Phone app process killed | Restore last valid trip/session; if stale, show resume confirmation and require fresh fix |
| Watch app exits/restarts | Restore only a verified committed route and safe checkpoint; otherwise return to ready state |
| Bluetooth disconnected | Watch continues committed cached route; phone shows disconnected; no assumed status sync |
| Internet lost | Continue current route; disable online search/live traffic/reroute and label unavailable |
| GPS weak/stale | Freeze turn countdown, off-route confirmation and arrival; display last-fix age/state |
| Permission revoked | Stop using location immediately; show recovery action; retain non-location planning/history |
| Watch storage full | Reject pending route/checkpoint write; never corrupt or erase active route |
| Transfer interrupted | Resume only with matching transfer identity/staging state; otherwise clean abort and retry from start |
| New app/schema version | Migrate safely or invalidate session explicitly; do not reinterpret old payload silently |
| Low watch battery | Offer reduced update/render frequency where supported; never suppress essential turn state without notice |

## 4. Persistence and atomicity

- Phone uses migration-capable local persistence for saved places, journeys, trips and active session metadata.
- Route replacement on watch is active/pending transactional storage as specified in A4.
- Persist state at important transitions: route committed, navigation started/paused/resumed/stopped, maneuver confirmed, reroute committed and trip arrived.
- A periodic checkpoint may be added after measuring write cost and recovery benefit; interval is an A8/A9/implementation decision.
- On crash recovery, validate route hash, schema version, session identity and last checkpoint before restoring navigation.
- Pending chunks expire after a bounded period and are cleaned without touching active route data.

## 5. Privacy and user control

- Raw high-frequency GPS traces are not stored by default.
- Trip summary storage is local by default; any cloud sync is out of MVP scope and requires separate consent/privacy review.
- Logs use event codes and redacted metadata. Do not record full destination names or exact traces unless required for user-facing history and clearly controlled.
- Provide controls to view/delete saved places, plans and trip history.
- Provide a trace-retention option only after explaining purpose, retention duration and export/delete controls.
- No background location collection solely for analytics.
- Permission is requested in context and can be revoked without trapping the user.
- Never place API keys, secrets or personal user data in the public repository.

## 6. Security

- Network providers use TLS and appropriately restricted API credentials.
- Secrets belong in runtime configuration/secret storage, not source files.
- Validate provider payloads, imported files and Garmin messages before persistence.
- Use opaque identifiers and bind transfer messages to selected device/session/route revision.
- Avoid exposing precise home/work locations in diagnostic exports.
- Define a documented support bundle that excludes raw trace by default.

## 7. Battery and performance policy

- Avoid continuous unnecessary phone-to-watch GPS streaming; the watch follows its own route when its GPS app lifecycle permits.
- Use bounded route geometry and a simplified watch renderer.
- Avoid rendering or writing storage on every raw sample if the UI does not need it.
- Use speed/quality/lifecycle gates to select update frequency, but do not freeze safety-relevant maneuver state based only on battery saving.
- Measure phone and watch battery drain on real trips of multiple durations, including weak-GPS and reconnect scenarios.
- Record CPU/RAM, storage write frequency, render time, route length and BLE throughput during A5/F/G validation.
- Do not set an unsupported universal battery-hours promise.

## 8. Retention/cleanup policy

| Data | Default retention | Deletion |
|---|---|---|
| Search text history | Short local history; user configurable | Clear history |
| Saved place/journey | Until user deletes | Delete item |
| Trip summary | Local until user deletes or configured retention | Delete trip/history |
| Raw GPS trace | Not retained by default | Immediate discard after transient processing |
| Provider cache | Exact provider contract/expiry | Scheduled cleanup |
| Pending transfer | Until completed/aborted/expired | Clean staging only |
| Active route | Until stopped/replaced/cleared by user | Explicit action after confirmation |
| Diagnostic logs | Minimal bounded ring buffer | Automatic expiry/clear controls |

## 9. A7 acceptance criteria

- A7-AT-001: internet loss does not erase or invalidate the committed route.
- A7-AT-002: BLE loss preserves watch-side guidance and shows phone-side disconnection.
- A7-AT-003: app restart restores a valid session or explicitly asks to resume.
- A7-AT-004: route hash/schema mismatch prevents unsafe recovery.
- A7-AT-005: pending route data never overwrites the active route before commit.
- A7-AT-006: storage-full condition preserves active route and reports failure.
- A7-AT-007: weak/stale GPS freezes progress/alerts/arrival.
- A7-AT-008: permission revocation stops location use and leaves non-location features usable.
- A7-AT-009: a pending transfer expires and is cleaned without removing the active route.
- A7-AT-010: raw GPS trace is not retained by default.
- A7-AT-011: user deletion removes all linked user-owned records in the scope defined by policy.
- A7-AT-012: provider caches expire according to provider-specific retention metadata.
- A7-AT-013: no full offline tile prefetch occurs from public OSM standard tile servers.
- A7-AT-014: battery/performance telemetry is opt-in or aggregated/redacted and does not include precise trace by default.
- A7-AT-015: crash/power-loss tests cover route transfer and session checkpoint windows.

## 10. Open measurements

- Watch total Object Store quota and reliable route length.
- Checkpoint interval vs storage writes and recovery time.
- GPS and display update frequency vs battery drain.
- Long-route memory/CPU behavior on base 6S and Pro.
- Phone background location behavior on each supported Android/iOS target.
- Tile cache size and retention according to selected vendor.
