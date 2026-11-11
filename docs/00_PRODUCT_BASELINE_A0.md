# A0 — Product Baseline

**Document ID:** GN-A0-002 · **Version:** 2.0.0 · **Date:** 2026-11-11  
**Status:** Product direction accepted; technical choices remain subject to A2/A5/A9 gates.

## 1. Product statement

Build a phone-first navigation and journey-planning application with the practical speed of Google Maps-style everyday navigation and the deliberate trip-planning/discovery experience associated with komoot. Differentiate through **motorized travel modes**, Vietnam-first motorbike routing and an integrated Garmin fēnix 6S companion.

The user should be able to search for a destination quickly, choose a travel mode, understand the route before starting, follow clear turn-by-turn directions, save or organize journeys and optionally move the active route to a Garmin watch.

## 2. Meaning of direction B

The accepted choice B is a product-design decision:

- **Google Maps-inspired:** fast map opening, current location, omnibox search, nearby place search, destination selection, route preview and daily turn-by-turn guidance.
- **komoot-inspired:** planned journeys, multiple stops, saved routes, route collections and place discovery beyond shortest-path navigation.
- **Our own product identity:** Vietnamese motorbike support and practical glanceable Garmin route guidance.

This decision does **not** authorize Google/komoot scraping, reverse engineering, unlicensed data reuse or an assumption that their APIs are available. Provider policy is defined in A2.

## 3. Primary users and jobs

| User group | Job to be done | Priority |
|---|---|---|
| Daily motorbike rider in Vietnam | Find a destination and reach it without being sent onto roads prohibited to motorbikes | Primary |
| Car driver | Navigate with clear route/turn instructions and restrictions | Secondary |
| Trip planner | Prepare a multi-stop journey, save it and follow it later | Secondary |
| Walker/cyclist | Find a mode-appropriate route rather than a car route | Secondary |
| Garmin fēnix 6S owner | See next-turn guidance without constantly looking at the phone | Differentiator |

## 4. First-market boundary

- Initial market and verification: Vietnam.
- Initial test geography: Hanoi and surrounding routes.
- Language: Vietnamese-first UI/instructions; architecture should permit localization.
- Units: metric by default.
- Safety/legal behavior: local road signs, access restrictions and traffic law always override an app instruction.

## 5. Modes

First MVP candidates: **xe máy/scooter, ô tô, đi bộ, xe đạp**. Motorbike is the first field-validation priority.

Do not treat every powered two-wheeler as the same. The initial common-motorbike candidate is Valhalla `motor_scooter`; a distinct touring/adventure motorcycle profile is a later, separately validated mode. Hiking/MTB, public transit, EV-specific routing and truck routing are not initial MVP commitments.

## 6. MVP scope

### P0 — first useful release
1. Phone map and live location state.
2. Text search for address, POI and named places; manual map-pin selection fallback.
3. Mode selection and provider-backed route calculation.
4. Route preview with geometry, distance, estimated duration, turn list and explicit limitations.
5. Basic multi-stop plan with add/remove/reorder where engine limits allow.
6. Phone turn-by-turn with current/next maneuver, voice preference, GPS-quality handling, off-route state and online reroute.
7. Garmin Connect IQ companion for supported fēnix 6S variants: send a validated compact route and render next-turn guidance.
8. Cached watch route following after a route has been confirmed and activated.
9. Recent destinations and trip summary saved locally.

### P1 — post-MVP expansion
- Curated discovery collections and categorized nearby places.
- Saved trips and shareable route links/files, subject to source license.
- Alternative route comparison where supported.
- Licensed live-traffic data, incident awareness and richer route preference filters.
- Phone-side offline map downloads from a provider that explicitly permits prefetch/offline use.
- Hiking/MTB activity profiles after a separate routing and safety evaluation.

### P2 — not promised
Social network/feed, public reviews and ratings, cloud accounts, subscription tiers, live location sharing, fleet features, public transit, global offline map download, Garmin full offline basemap rendering and automated route recommendations based on private behavioral profiling.

## 7. Product principles

1. **Mode is functional:** selected mode changes route costing/access rules, not merely the icon.
2. **Phone-first, watch-assisted:** the phone owns search, discovery, route calculation and network-dependent rerouting.
3. **Watch is glanceable:** do not attempt a full phone-like search/map UI on a 240×240 watch.
4. **Route truth over convenience:** missing or uncertain road-access data must not be disguised as certainty.
5. **One coherent data lineage:** route geometry, basemap and place content must have compatible licensing/provenance.
6. **Offline claims are narrow:** a cached route is not the same as an offline map or offline route calculator.
7. **User control:** permissions, saved history, data deletion and alert preferences are visible.
8. **Safe interaction:** discourage phone/watch interaction while operating a vehicle; provide mount/voice-first use where practical.
9. **No fake quality:** do not claim live traffic, road legality, ETA confidence or watch sync success without evidence.

## 8. Product success criteria (targets to validate, not current performance)

- A user can search or pin a destination and see a route preview without connecting Garmin.
- The user can choose motorbike separately from car, walking and bicycle.
- A motorbike test route does not route through a verified prohibited segment in the controlled Vietnam fixture set.
- A Garmin transfer is labelled confirmed only after application-level validation/commit.
- When the phone link or Internet is lost, the watch reports the exact limitation and does not silently lose the currently active route.
- A user can find a saved trip again after app restart.
- All essential watch content is legible on the exact 6S device.

## 9. Explicit exclusions

No Google/komoot brand use that implies partnership; no web scraping; no third-party API whose terms prohibit the intended data flow; no promise of universal legal correctness; no automatic background monitoring; no production implementation before A9.

## 10. Decision log

| Decision | State | Reason |
|---|---|---|
| Product direction B | Accepted | User selected Google Maps + komoot |
| Google/komoot are UX references, not data dependencies | Accepted baseline | Provider access/licensing constraints |
| Vietnam-first motorbike navigation | Accepted baseline | Main differentiator and first validation target |
| Phone primary, fēnix 6S secondary | Accepted baseline | Watch screen/compute constraints |
| OSM/Valhalla candidate for coherent Garmin/offline data path | Provisional | Must pass data, route, license and field gates |
| P0 modes: scooter/motorbike, car, walk, bicycle | Proposed baseline | Keeps MVP focused |
