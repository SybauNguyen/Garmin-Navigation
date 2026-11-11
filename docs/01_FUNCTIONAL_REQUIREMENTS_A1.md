# A1 — Functional Requirements & User Journeys

**Document ID:** GN-A1-002 · **Version:** 2.0.0 · **Date:** 2026-11-11  
**Status:** Re-baselined requirements; test cases are specifications only.

## 1. Conventions

- **P0:** release-blocking first-release behavior.
- **P1:** important follow-up.
- **P2:** deferred.
- **Phone / Watch / Shared:** principal owner.
- An acceptance ID is a test specification. It is not marked passed until run on the stated environment.

## 2. Functional requirements

| ID | Priority | Owner | Requirement | Acceptance evidence |
|---|---|---|---|---|
| APP-001 | P0 | Phone | Onboarding explains phone/watch roles and permits phone-only use. | AT-001: first launch, skip and return paths work. |
| APP-002 | P0 | Phone | Location permission, service state and current-fix quality are distinct UI states. | AT-002: denied, restricted, disabled and valid-fix states are distinguishable. |
| APP-003 | P0 | Phone | User can set default mode, units, voice prompts and map preferences. | AT-003: settings persist after restart. |
| MAP-001 | P0 | Phone | Display an interactive licensed map and current location only when a valid fix exists. | AT-004: map pan/zoom/recenter and no-fix states work. |
| MAP-002 | P0 | Phone | User can set origin/destination through search or a map pin. | AT-005: pin coordinates and route endpoints match. |
| SRH-001 | P0 | Phone | Search by address, place name and POI text. | AT-006: result selection returns a usable canonical place. |
| SRH-002 | P1 | Phone | Provide suggestions, category browse and nearby search. | AT-007: stale search responses do not overwrite newer queries. |
| SRH-003 | P0 | Phone | Place detail clearly separates sourced fields from missing information. | AT-008: no invented address, rating or hours. |
| SRH-004 | P1 | Phone | Save places and recent destinations locally. | AT-009: saved items survive restart and can be removed. |
| DISC-001 | P1 | Phone | Explore curated collections and categories relevant to a planned journey. | AT-010: each item has provider/provenance metadata and opens the correct place. |
| DISC-002 | P1 | Phone | User can save a discovered place or add it to a trip plan. | AT-011: selected place is added once and retains identity. |
| PLAN-001 | P0 | Phone | User can choose a mode: motorbike/scooter, car, walking or bicycle. | AT-012: mode is passed to a distinct provider profile or explicit unsupported response. |
| PLAN-002 | P0 | Phone | Calculate and preview a route with geometry, distance, duration and turn steps. | AT-013: summary corresponds to the selected normalized route. |
| PLAN-003 | P0 | Phone | User can add, remove and reorder intermediate stops within provider limits. | AT-014: route reflects exact ordered stops; unsupported cases are explained. |
| PLAN-004 | P1 | Phone | Show alternatives only when the engine returns valid alternatives. | AT-015: no fabricated alternative is displayed. |
| PLAN-005 | P0 | Phone | Show route warnings, access limitations and source limitations before navigation. | AT-016: warnings survive route selection and transfer. |
| PLAN-006 | P0 | Phone | Do not silently replace a requested mode with a different mode. | AT-017: unsupported mode blocks Start and explains why. |
| PLAN-007 | P1 | Phone | Provide supported avoidances (tolls/highways/ferries) only where mode/provider can honor them. | AT-018: unsupported options are disabled or labelled not applied. |
| NAV-001 | P0 | Phone | Start, pause, resume and stop a navigation session. | AT-019: state transitions and persistence are deterministic. |
| NAV-002 | P0 | Phone | Display one current maneuver, next maneuver and along-route distance to turn. | AT-020: countdown freezes on stale/invalid GPS. |
| NAV-003 | P0 | Phone | Provide visual guidance and optional spoken prompts. | AT-021: muted/audio-focus failure does not break visual guidance. |
| NAV-004 | P0 | Shared | Match position to the selected route with confidence and fix-quality gates. | AT-022: noisy/ambiguous fixes cannot skip turns. |
| NAV-005 | P0 | Shared | Detect sustained off-route movement and show a clear state. | AT-023: one noisy fix cannot trigger confirmed off-route. |
| NAV-006 | P0 | Phone | Request a new route only when network/provider is available and preserve the current route until replacement is accepted. | AT-024: failed reroute does not corrupt active navigation. |
| NAV-007 | P0 | Shared | Confirm arrival once using route and position evidence. | AT-025: GPS drift cannot repeatedly toggle arrival. |
| NAV-008 | P1 | Phone | Show traffic status only when a licensed, current source is configured. | AT-026: stale/absent traffic is not displayed as live. |
| TRIP-001 | P0 | Phone | Save trip summary locally with route/mode/timestamps/distance where available. | AT-027: trip remains after restart. |
| TRIP-002 | P1 | Phone | User can review and delete saved trips. | AT-028: deletion removes linked user-owned data per policy. |
| GAR-001 | P0 | Phone | Identify Garmin Connect/Connect IQ availability separately from watch reachability. | AT-029: app missing, SDK unavailable, disconnected and ready states differ. |
| GAR-002 | P0 | Shared | Select and target one exact compatible watch; never fan out a route to every connected watch. | AT-030: multi-watch test confirms only selected device receives payload. |
| GAR-003 | P0 | Shared | Transfer the selected route with versioning, chunk integrity and explicit application-level confirmation. | AT-031: no “synced” state before watch validation/commit. |
| GAR-004 | P0 | Watch | Present next turn, turn distance, route state and remaining trip data in a legible 6S HUD. | AT-032: test on base 6S; no unsupported native MapView call. |
| GAR-005 | P0 | Watch | Continue the committed route when phone/BLE/Internet is temporarily unavailable, subject to GPS/app lifecycle. | AT-033: interruption test retains valid route and shows status. |
| GAR-006 | P0 | Shared | Preserve active route until a replacement route is verified and committed. | AT-034: corrupt/incomplete replacement cannot replace current route. |
| GAR-007 | P1 | Shared | Sync compact trip events/progress to phone when watch uplink is proven. | AT-035: stale events cannot roll back newer state. |
| OFF-001 | P0 | Watch | Persist the active compact route and navigation snapshot. | AT-036: app restart restores or safely invalidates the session. |
| OFF-002 | P1 | Phone | Support offline map regions only through a provider that explicitly licenses downloads. | AT-037: offline package source and expiry/attribution are recorded. |
| SAFE-001 | P0 | Shared | Make GPS, network, Bluetooth, route freshness and uncertainty visible. | AT-038: all interruption states are represented truthfully. |
| SAFE-002 | P0 | Shared | Do not claim legal road access or safety certification from an unverified routing profile. | AT-039: warnings/unknown access handling are tested. |
| SAFE-003 | P0 | Phone | Encourage voice/mounted use and discourage interaction while driving/riding. | AT-040: navigation UI safety text and settings are accessible. |
| PRIV-001 | P0 | Phone | Detailed GPS traces are off by default and user-owned data can be deleted. | AT-041: default retention and delete flow are verified. |

## 3. User journeys

1. **Daily navigation:** open map → search/pin destination → select motorbike/car/walk/bicycle → review route/warnings → start phone navigation.
2. **Garmin handoff:** select route → select one watch → verify app reachability → transfer → watch validates and commits → phone displays confirmed status.
3. **Motorbike road restriction:** select motorbike → route engine applies the motorbike costing profile → any unknown/prohibited segment is flagged or route is rejected.
4. **Multi-stop trip:** add several places → reorder stops → request route → review route/stop limitations → save trip.
5. **Discovery:** explore category/collection → open place → save or add to plan → calculate a route.
6. **Off-route:** watch/phone obtains qualified fixes → suspected deviation → sustained evidence → phone computes replacement → safe route replacement handshake.
7. **Network lost:** continue current committed route; no live traffic or network reroute; display disconnected state.
8. **BLE lost:** watch continues cached route; phone shows that watch sync is unavailable; reconcile only with confirmed events.
9. **Arrival:** require destination/route evidence → arrive once → save summary.
10. **No GPS permission:** allow search/manual route planning; no fake current-location marker or progress.

## 4. Cross-feature invariants

- A route has an explicit mode/profile and provider provenance.
- Route versions are immutable; a reroute creates a new revision/route identity.
- Route matching, turn countdown, off-route confirmation and arrival freeze when eligible GPS evidence is unavailable.
- An application-level Garmin ACK is distinct from SDK send success.
- A new route cannot replace the active watch route until the complete payload is validated, durably staged and committed.
- Provider warnings and restrictions are not dropped when rendering or transferring.
- No stale traffic or ETA is shown as live.
- The same route mode is preserved through planning, transfer, navigation, trip history and recovery.
- Garmin is optional; core phone navigation remains usable without a watch.

## 5. Priority and release gates

P0 items are blockers for the first integrated release. A device/provider inability may lead to a documented fallback or a reduced supported-device list, but it must not be hidden by changing the meaning of “confirmed” or “offline”.

The detailed A8 screen/state catalogue and test execution environment are still required before A9.
