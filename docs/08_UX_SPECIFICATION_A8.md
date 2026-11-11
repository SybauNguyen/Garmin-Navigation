# A8 — UX Specification & Acceptance Criteria

**Document ID:** GN-A8-001 · **Version:** 2.0.0 · **Date:** 2026-11-11  
**Master Plan baseline:** 1.2.0 · **Product direction:** B — Google Maps + komoot experience  
**Status:** UX specification complete at design level; no visual prototype, usability study or device test is claimed.

## 1. Purpose and design principles

A8 turns the A0–A7 requirements into an implementable phone/watch experience. It defines information architecture, main journeys, screen states, interaction behavior, the compact fēnix 6S presentation and UX acceptance criteria.

Principles:
1. Map-first for everyday navigation; the user can reach search and route planning quickly.
2. Discovery and deliberate trip planning are distinct from immediate turn-by-turn navigation.
3. Phone is the planning and primary navigation surface; Garmin is a glanceable companion.
4. Mode, route warnings, source limitations and sync status remain visible at the moment they matter.
5. No success state without evidence: “route calculated”, “sent”, “received”, “validated” and “committed” are different states.
6. The UI must not hide uncertainty in GPS, road access, traffic, ETA or provider coverage.
7. Riding/driving interaction is minimized; setup happens before departure and voice/mounted use is preferred.

## 2. Information architecture

### 2.1 Phone primary navigation

Use five primary destinations in a bottom navigation bar:

| Tab | Primary purpose | Main content |
|---|---|---|
| Map | Fast everyday use | Current location, search, mode selector, nearby map, destination selection |
| Explore | Discover places and route ideas | Categories, curated collections, nearby places, saved/discovered place details |
| Plan | Build an intentional journey | Origin, destination, ordered stops, mode, route options and route preview |
| Trips | Revisit and manage journeys | Saved plans/routes, recent trips, trip summaries and route files where supported |
| Garmin | Manage watch connection | Device selection, compatibility, app reachability, sync state, watch guidance preferences |

Settings and profile are reached from a consistent account/settings entry in the top app bar; they are not a sixth bottom tab. Active navigation opens a dedicated full-screen guidance mode and hides the primary tab bar.

The initial launch destination is Map. A Garmin device is optional; every P0 phone journey remains usable without one.

### 2.2 Navigation hierarchy

- Map → Search → Search results → Place detail → Route preview → Start navigation.
- Map → long-press/pin → Pin detail → Route preview.
- Explore → Category/collection → Place or route detail → Save / Add stop / Plan.
- Plan → Itinerary editor → Route preview → Start / Save.
- Trips → Saved plan / Trip detail → Resume planning or navigate again.
- Garmin → Select device → Compatibility/setup → Transfer route → Confirmed or explicit failure state.

Back navigation preserves user-entered stops, selected mode and route preview where safe. An active trip must not be discarded by accidental navigation away from a screen.

## 3. Core phone screens

### 3.1 Map home

**Purpose:** quickest path to a destination.

Layout:
- Map occupies the majority of the screen.
- Search entry is visible near the top and supports address, POI and place-name queries.
- Current location marker appears only with a valid fix; otherwise show a permission/GPS state.
- Compact travel-mode selector: Xe máy, Ô tô, Đi bộ, Xe đạp. The last selection is remembered.
- Map actions: recenter, map layers (only configured layers), select destination by pin.
- Bottom sheet shows nearby place cards or destination/route actions depending on context.

Behavior:
- Tapping search opens a focused search surface without losing map context.
- Long press creates a destination pin and opens a place/pin sheet.
- Changing mode invalidates the prior route preview and triggers recalculation; the old route must not remain visually presented as if it matched the new mode.
- Map layer choice must not imply live traffic unless a current licensed source exists.
- If map tiles fail, preserve search, saved destinations and a clear retry state.

### 3.2 Search

Elements:
- Search field with clear/cancel.
- Optional recent queries and category shortcuts.
- Results list with title, concise address/context, distance only when a valid current location exists, and source/provenance as needed.
- Loading, no-result, network-error and ambiguous-result states.

Rules:
- Debounce and cancel stale requests; older responses cannot replace newer results.
- Do not fabricate opening hours, price, review count, exact entrance or ranking.
- A result may be selected even if optional metadata is absent.
- Manual map pin remains available when text search fails.

### 3.3 Place detail

Show:
- Place name and canonical coordinate-backed identity.
- Address and category only when available.
- Optional sourced fields (hours, photos, pricing, ratings) only when a provider/license and freshness policy has been approved.
- Actions: Set as destination, Add to plan, Save, Share coordinate/address if available.
- Attribution/provenance in a predictable details area.

A blank optional field is omitted; do not render invented placeholders such as “open now” without current verified hours.

### 3.4 Explore

Explore is not a social feed in the first release. It is an organized entry point to app-owned categories and collections.

P0 shell:
- Category chips or tiles.
- Nearby list/map results using the current viewport or user location.
- Save and Add to plan actions.
- Filters supported by the selected source.

P1:
- Curated route collections, scenic stops, themed journeys, route notes and community-contributed content after moderation/privacy design.
- Ratings, comments, public profiles and live location sharing remain outside MVP.

A collection item records source, attribution, update time and whether it is app-curated, user-created or provider-derived.

### 3.5 Plan editor

Purpose: deliberate trip creation rather than a one-tap commute.

Elements:
- Origin (current location or manually selected point).
- Ordered waypoint list: origin, intermediate stops, destination.
- Add stop via search, Explore or map pin.
- Reorder controls with clear accessible labels; remove stop with undo where feasible.
- Travel-mode selector remains visible.
- Route preferences (avoid toll/highway/ferry) are shown only if the chosen profile/provider truly supports them.
- Save plan and Calculate actions.

Rules:
- Provider waypoint limits are surfaced before or at calculation.
- The UI must not silently drop, reorder or merge stops.
- If multi-stop alternatives are not supported, show one valid route and explain the limitation.
- Route recalculation after a stop or mode change has a loading state and blocks Start until the new result is validated.

### 3.6 Route preview

Display:
- Selected mode/profile.
- Route line and ordered waypoints.
- Distance and estimated duration, both tied to selected route.
- Maneuver summary/step list.
- Provider warnings and access uncertainty.
- Alternatives only if actually returned and valid.
- Actions: Start on phone, Send to Garmin, Save route/plan, Edit stops.

The primary action is Start. Send to Garmin is secondary and enabled only when the route is valid and a selected reachable device is available. If watch transfer is unverified, the action must communicate the limitation instead of promising sync.

### 3.7 Active phone navigation

The guidance layout should minimize attention switching:
- Full map with clear current route and current location.
- One prominent current instruction and distance to maneuver.
- Next instruction preview only when useful; avoid dense stacked cards.
- Remaining distance and ETA with source/freshness indication when not current.
- Mode indicator and a prominent but compact GPS/network state.
- Voice mute/settings in a predictable place.
- Reroute prompt or status only after sustained off-route confidence.
- End/stop control separated from high-frequency interaction and guarded against accidental touch.

When GPS quality becomes poor or stale:
- Freeze distance-to-turn and route progress.
- Keep last position visually distinct from live position.
- Show “Đang tìm GPS” / “Tín hiệu GPS yếu” and last-fix age where useful.
- Do not keep counting down using stale samples.

When offline:
- Continue the current route if cached valid data permits.
- Hide or label live traffic and current network-dependent ETA/reroute features unavailable.
- Never silently present an old route calculation as freshly rerouted.

### 3.8 Trips

Sections:
- Saved plans/routes.
- Recent/completed trips.
- Trip detail with mode, date, distance/duration where available, stop list and route summary.
- Delete action for user-owned data.
- Resume planning / Navigate again.

No public profile or feed is required. Trip history does not retain a raw GPS breadcrumb by default.

### 3.9 Garmin management

The Garmin tab must distinguish:
1. Garmin Connect unavailable/not installed.
2. Connect IQ integration unavailable.
3. No compatible watch paired.
4. Multiple watches found but none selected.
5. Selected watch disconnected.
6. Watch connected but app not installed/not reachable.
7. Watch app ready.
8. Transfer in progress.
9. Sent by SDK but application confirmation unavailable.
10. Watch validated route and is ready to commit.
11. Route committed/confirmed.
12. Transfer rejected/failed/expired.

Device card shows model, firmware if available, connection/reachability state and capabilities. Base fēnix 6S must be identified as custom-renderer capability; native MapView must not be promised for the base model. Pro/Sapphire native map support remains conditional on exact supported-device verification.

The phone must target one explicitly selected watch. If multiple watches are connected and selection cannot be enforced, disable transfer and explain why.

### 3.10 Settings

P0 settings:
- Default travel mode and units.
- Voice prompt on/off and volume/voice choice where supported.
- Haptic/turn-alert preference (watch).
- Map style only for configured styles.
- Saved data/trip deletion and privacy controls.
- Permission and provider diagnostics.

P1:
- Custom turn-alert lead distance within calibrated bounds.
- Downloaded phone map regions (only with licensed provider rights).
- Advanced activity profiles.

## 4. Garmin fēnix 6S UX

### 4.1 Design envelope

- Round 240×240 display.
- Keep all essential text away from clipped corners.
- Use large type and short Vietnamese phrases; do not attempt to show the full address or long instruction.
- Prefer one glanceable fact per visual group.
- Use contrast, shape and text in addition to color for status.

### 4.2 Watch screen set

**Screen W1 — Navigation HUD (default)**
- Large maneuver arrow/symbol.
- Short instruction (e.g. “Rẽ trái”, “Đi thẳng”, “Quay đầu”).
- Distance to maneuver in meters/kilometers.
- Smaller next-street label when known and space allows.
- Remaining trip distance as secondary information.
- GPS/route state icon with readable short label when not normal.

**Screen W2 — Route overview**
- Simplified route line around current position.
- Current direction marker and a small upcoming segment.
- No full POI browsing or search.
- Base 6S uses custom Graphics.Dc renderer. A Pro/Sapphire native MapView path may be used only after exact model/API support is confirmed; keep custom fallback.

**Screen W3 — Trip status**
- Remaining distance.
- Estimated remaining time only if trustworthy/current.
- Route mode.
- Bluetooth/phone link indicator.
- GPS fix state.

**Screen W4 — Pause/arrival/error**
- Clear state, one primary action, safe resume/exit route.
- Arrival is a stable terminal state; GPS drift must not make the UI oscillate back to navigation.

### 4.3 Watch interaction

- Use physical buttons and a small number of predictable screen transitions; avoid dense touch-only interactions.
- Default screen returns to HUD during active navigation.
- User can switch HUD/overview/status deliberately.
- Essential guidance remains available when phone/Bluetooth is disconnected, provided a committed route and eligible GPS fix exist.
- When watch GPS is unavailable, show the last-fix state and freeze countdown.
- Do not promise continuous GPS while the app is suspended/inactive.
- Haptic cue is supplementary; visual instruction remains complete if vibration is unavailable.
- Repeated alerts must be suppressed according to A6 one-shot stage rules.

## 5. Component and visual behavior

### Phone
- Minimum practical touch targets: aim for 44–48 logical pixels for primary controls; spacing must prevent accidental adjacent taps.
- Text hierarchy: primary action/instruction, place/route title, supporting metadata, provenance/status.
- Map overlays should not obscure the next maneuver or essential controls.
- Bottom sheets must have clear expanded/collapsed states and remain dismissible.
- Loading controls prevent duplicate route requests without blocking safe navigation away.

### Watch
- Round-screen layout with central safe area.
- Avoid text blocks wrapping into corners.
- Use concise labels and fixed-size symbols.
- Do not rely on color alone to show GPS, sync or route state.
- Test fonts at the actual device scale.

### State language
Use consistent Vietnamese messages:
- “Đang tìm GPS” — acquiring a fix.
- “GPS yếu” — fix exists but is not eligible for confident progress.
- “Mất kết nối điện thoại” — Garmin/phone app-message link unavailable; cached route may continue.
- “Không có Internet” — online search/reroute/traffic unavailable.
- “Đã gửi, chưa xác nhận” — SDK send attempt without app-level confirmation.
- “Đã đồng bộ” — only after verified application-level commit.
- “Đang lệch tuyến” — confirmed sustained deviation, not a single noisy fix.
- “Không thể tính tuyến cho phương tiện này” — requested mode unsupported; never substitute another.

Exact copy is subject to Vietnamese language review during prototyping.

## 6. Cross-screen state catalogue

Every screen must define at least the relevant states below:

| State | User feedback | Allowed actions / restrictions |
|---|---|---|
| Initial/loading | Short progress indicator and context | Cancel where safe; prevent duplicate requests |
| Empty/no result | Explain what is empty and next action | Retry, change query, pin manually |
| Network offline | Clearly distinguish from empty result | Use local saved data/current cached route |
| Map tile error | Map unavailable message | Retry; retain search/saved place entry |
| Location permission denied | Explain what feature is affected | Open settings; keep manual route planning |
| GPS acquiring/weak/stale | Label last fix and freeze progress | Continue only with explicit uncertainty; no false countdown |
| No route | Explain mode or connectivity issue | Change endpoints/mode only with explicit user action |
| Unsupported mode | State that the exact mode is unavailable | Choose another mode manually; never auto-fallback |
| Route warning | Show the affected segment/reason before start | Review route or cancel |
| Provider data stale | Show stale/unavailable state | Refresh if network available |
| Bluetooth off/disconnected | Distinguish from Garmin app not installed | Phone navigation remains available |
| Connect IQ unavailable | Setup explanation | Continue phone-only |
| Multiple watches | Ask user to select exact target | Do not broadcast transfer |
| Watch incompatible | Show supported limitation | Use phone only |
| Transfer progress | Progress plus current transfer phase | Cancel/abort safely |
| Transfer sent/unconfirmed | Explicitly not committed | Preserve prior watch route; retry or use phone |
| Transfer rejected/failed | Reason and retry guidance | Retain prior route |
| Storage full | Explain watch storage failure | Do not delete active route automatically |
| Off-route suspect | Quietly indicate checking position | Do not reroute yet |
| Off-route confirmed | Clear reroute status | Request reroute only if uplink/network capability exists |
| Arrival | Stable completed state | View/save trip summary |
| Critical battery | Visible warning/reduced optional work | Preserve essential route and state |

## 7. Interaction safety

- Route setup, mode selection and multi-stop editing should be performed before departure.
- During movement, phone UI should emphasize voice prompts and a mounted, glanceable map. Avoid interaction flows requiring typing.
- Do not encourage handling the watch while riding or driving; haptics and short visual cues are supplementary.
- A route instruction never supersedes road signs, traffic controls or local law.
- Avoid placing a destructive action such as End Trip next to a high-frequency control.
- GPS uncertainty or access uncertainty should be stated plainly, not hidden to create a polished impression.

## 8. Accessibility and localization

- Vietnamese is the initial UI/instruction language.
- Text must support diacritics correctly on phone and watch fonts.
- Use text/icon/shape in addition to color to represent route, GPS, sync and warning states.
- Support system font scaling on phone without clipping primary actions.
- Provide sufficient contrast for outdoor daylight use and dark/night use.
- Essential actions have accessible names and predictable focus order.
- Motion/animation is optional and must not be the only state indicator.
- Units are metric by default; formatting follows locale.
- Long street names are shortened safely; do not cut off the maneuver verb.

## 9. P0/P1 screen scope

| Screen/flow | Priority |
|---|---|
| Map home, search, search results, pin selection | P0 |
| Place detail with core fields | P0 |
| Mode selection, route preview and warning | P0 |
| Basic multi-stop Plan editor | P0 |
| Active phone navigation / GPS / offline states | P0 |
| Garmin device selection, compatibility and transfer states | P0 |
| Watch HUD, route overview, pause/arrival/error states | P0 |
| Trip summary, recent destinations and delete controls | P0 |
| Explore category shell and nearby places | P1 |
| Curated discovery collections and route notes | P1 |
| Advanced map styles, traffic overlay and custom alerts | P1 |
| Public feed, comments, reviews, social/live sharing | P2/out of scope |

## 10. A8 UX acceptance criteria

These are test specifications. None has been executed.

- A8-AT-001: first launch opens Map and phone-only planning works without Garmin.
- A8-AT-002: all five primary tabs open the correct destinations and preserve safe navigation state.
- A8-AT-003: active navigation hides primary tab navigation and exposes a clear safe exit/pause action.
- A8-AT-004: search supports loading, no-result, error, cancel and stale-response suppression.
- A8-AT-005: manual map pin can create a destination when search returns no results.
- A8-AT-006: mode selection remains visible in plan and route preview; mode change invalidates old route result.
- A8-AT-007: plan editor retains exact waypoint order; no provider-limit overflow silently drops stops.
- A8-AT-008: route preview shows only provider-returned alternatives and truthful warnings.
- A8-AT-009: unsupported route mode cannot start and is not silently substituted.
- A8-AT-010: stale/poor GPS freezes countdown and shows non-live state.
- A8-AT-011: offline state distinguishes unavailable live data from current cached route guidance.
- A8-AT-012: live traffic is never shown without a fresh licensed source.
- A8-AT-013: Garmin UI separates Bluetooth, Garmin Connect, Connect IQ app, compatibility and transfer states.
- A8-AT-014: transfer status says “confirmed” only after verified watch-side commit.
- A8-AT-015: when multiple watches are connected, only the selected target can receive route data.
- A8-AT-016: base fēnix 6S watch layout works without native MapView.
- A8-AT-017: every essential 6S watch HUD field fits inside a tested round-screen safe area.
- A8-AT-018: watch route guidance continues after phone-link loss only when a valid committed route exists and GPS is eligible.
- A8-AT-019: arrival state is terminal and does not oscillate after a later GPS drift.
- A8-AT-020: saved trip/place deletion has a clear confirmation and removes linked user-owned records.
- A8-AT-021: primary status is not conveyed by color alone.
- A8-AT-022: Vietnamese diacritics render correctly on actual phone/watch fonts.
- A8-AT-023: critical touch actions meet minimum target/spacing goals and are not clustered dangerously.
- A8-AT-024: navigation copy reminds the user to obey road signs and local laws and discourages interaction while moving.

## 11. Prototype and usability validation plan

Before A9 freeze:
1. Create phone wireframes for Map home, Search, Place detail, Plan editor, Route preview, Active navigation, Explore, Trips and Garmin.
2. Create watch layouts for base 6S (HUD/overview/status/pause/arrival) and separately gated Pro map screen.
3. Conduct task-based walkthroughs with representative Vietnamese users: find an address, choose motorbike, add two stops, spot a warning, start navigation, recover from GPS loss and transfer to Garmin.
4. Test round-screen clipping using the exact 240×240 watch emulator/device target.
5. Test in daylight, at night, with gloves/physical buttons where applicable, and in stationary/controlled conditions first. Do not conduct usability tasks while operating a vehicle.
6. Record task completion, critical errors, time-on-task, misread instruction cases and comprehension of sync/offline states.
7. Revise the wireframes and update A8 acceptance evidence before declaring A9 ready.

## 12. Handoff to A9

A9 must review:
- every P0 requirement in A1 and related A8 screen/state coverage;
- A2 provider/licensing and Vietnam motorbike validation blockers;
- A4 transfer protocol and exact 6S uplink discrepancy;
- A5 exact-device capabilities, storage and renderer;
- A6 parameter ranges and field calibration plan;
- A7 offline, privacy and battery policies;
- phone/watch wireframes and usability findings;
- implementation backlog and risk acceptance.

A8 completion does **not** equal design freeze. A9 must explicitly classify each open item as resolved, accepted limitation, deferred or release blocker. Production code remains prohibited until DESIGN FROZEN is formally recorded.


## 13. Alignment with the A0–A7 product baseline 2.0.0

This revision supersedes the earlier A8 draft where its assumptions conflict with the re-baselined A0–A7 package set.

### Product priority and transport modes

- Everyday map/search/route preview/phone navigation remains the primary use case.
- Scooter/ordinary motorbike and car are the P0 motorized modes; bicycle and walking are supported only where provider coverage and mode-specific access are validated. Touring/adventure motorcycle, hiking/MTB and EV are not implied MVP capabilities.
- A user-facing travel mode and a routing-engine costing profile are separate values. The preview must identify the selected user-facing mode and must never silently replace a failed motorbike route with an automobile route.
- Multi-stop planning preserves the exact stop order. A2 notes that route-wide alternatives may not be available for multipoint requests; the UI shows only alternatives returned by the selected provider and clearly labels any per-leg alternatives.

### Provider and map consistency

- Google Maps and komoot remain experience references only. Do not present their protected route or place data inside the OSM/MapLibre/Garmin pipeline.
- MapLibre and OSM-derived licensed map data remain technical candidates, not a frozen tile-provider choice.
- Offline map areas on the phone are not promised until a provider contract permits downloads. The public OSM standard tile service is not an offline map-pack source.
- Do not label traffic as live unless a current licensed traffic source is configured and its freshness is known.

### Garmin communication and device behavior

- Describe the phone/watch path as Garmin Connect IQ app messaging through the supported Mobile SDK; do not describe it as a custom raw BLE GATT protocol.
- Phone-side “send accepted” is not proof that the watch received, validated or committed a route. Keep separate UI states for sent/unconfirmed, received, validated and committed.
- Watch-to-phone events remain conditional on exact Connect IQ API/device/firmware verification. Phone guidance and watch-local route following must not depend on unverified uplink.
- Base fēnix 6S must work with a custom Graphics.Dc route renderer. Native MapView is an optional Pro/Sapphire enhancement only after exact compatibility confirmation.
- Watch location updates are lifecycle-dependent. When the Connect IQ app becomes inactive, the UI must not imply continuous GPS/navigation; after resume, obtain a fresh eligible fix before advancing the maneuver.
- Cached route guidance is available only while a valid committed route exists, the app is active and GPS is eligible. The watch is not a full offline map or offline routing engine.

### UX state and visual review additions

- The route preview distinguishes selected mode, provider profile limitation, route warning and stale provider/map data.
- The active screen distinguishes GPS degraded/lost, network offline, phone/watch link loss, watch app inactive and route deviation. These states must not share one generic “disconnected” label.
- For a multi-stop trip, identify the next stop and current leg; do not mix whole-trip totals with the current-leg remaining distance.
- The watch Turn HUD gives maneuver arrow and distance-to-turn highest visual priority; remaining distance, mode, road label and connection state are secondary.
- The base 6S screen safe area must be validated on a circular 240 × 240 display. A rectangular screenshot is not sufficient.

## 14. Additional A0–A7 traceability acceptance criteria

These extend the earlier A8-AT-001 through A8-AT-024. They are proposed tests, not execution evidence.

- A8-AT-025 — Mode/profile separation: route preview shows the selected product mode and does not present a different engine profile as equivalent without an explicit reviewed mapping.
- A8-AT-026 — Multi-stop capability: route preview never displays fabricated whole-trip alternatives when the provider does not return them.
- A8-AT-027 — Phone-only fallback: all P0 phone navigation flows remain available when Garmin Connect/Connect IQ integration is unavailable.
- A8-AT-028 — GPS lifecycle: when the watch app resumes from inactive state, the HUD shows GPS reacquisition and does not advance the maneuver from a stale fix.
- A8-AT-029 — Watch offline boundary: the interface says cached route guidance, not offline maps or offline rerouting, when phone/network service is absent.
- A8-AT-030 — Message transport truth: user-facing status does not imply raw BLE delivery or watch-side route commit from a phone SDK send callback alone.

## 15. A8 completion status

A8 v2.0.0 is complete as a written UX specification and traceability update. It defines five phone areas, primary journeys, screen/state catalogues, watch HUD/interaction, safety/accessibility requirements and 30 UX acceptance criteria.

Not completed and not claimed:
- high-fidelity/clickable phone wireframes;
- watch visual prototype validated on the exact 6S variant;
- task-based usability sessions;
- outdoor readability/glove/button tests;
- implementation, build or acceptance-test execution.

A9 must complete the design review, inspect wireframes/prototypes and record residual risks before any production code is started.
