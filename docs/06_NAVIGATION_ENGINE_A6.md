# A6 — Navigation Engine & Guidance Algorithms

**Document ID:** GN-A6-002 · **Version:** 2.0.0 · **Date:** 2026-11-11  
**Status:** Algorithm specification; thresholds require route replay and exact-device calibration.

## 1. Responsibility split

- Phone: route calculation, provider interaction, route alternatives, network reroute orchestration, map rendering, trip persistence and optional voice prompts.
- Watch: local GPS qualification, bounded matching against the committed route, progress/maneuver state, haptic cue, compact renderer and offline continuation.
- Shared contract: route revision, maneuver anchors, travel mode, confidence/state definitions and event versioning.

The watch is a route follower, not a full routing engine. It does not compute a new road-network route independently.

## 2. GPS qualification

Before matching, validate:
- coordinate range and non-null position;
- fix timestamp freshness and monotonicity;
- provider quality classification;
- plausible displacement relative to elapsed time/speed;
- optional heading and speed availability.

Garmin `Position.Info.accuracy` is categorical (e.g. GOOD, USABLE, POOR, LAST_KNOWN, NOT_AVAILABLE), not a numeric metre radius. Do not fabricate a horizontal accuracy number from the enum.

Only fresh eligible fixes may advance authoritative progress or trigger maneuver/off-route/arrival transitions. Poor, stale or last-known fixes can display a subdued marker but freeze countdown and state transitions. Long gaps trigger bounded reacquisition, not straight-line progress injection.

## 3. Route matching

1. Use a bounded candidate segment window near the previous accepted route segment; include adjacent shape blocks at block boundaries.
2. Project the fix onto each candidate segment using a local metric tangent-plane projection.
3. Compute cross-track distance, projected along-route offset and segment bearing.
4. Score candidates using normalized cross-track distance, route continuity, plausible along-route progress and heading agreement when the device is moving fast enough for heading to be meaningful.
5. Classify confidence as HIGH/MEDIUM/LOW. The score is a ranking aid, not a calibrated probability.
6. Require temporal consistency on parallel roads, overlapping loops, junctions and grade-separated crossings.

A LOW-confidence match cannot advance the authoritative maneuver state. If no candidate is credible, show GPS/route uncertainty instead of snapping to a visually convenient road.

## 4. Progress model

Maintain separate values:
- raw candidate offset;
- filtered current offset;
- furthest confirmed offset;
- current matched segment/index;
- match confidence and last eligible fix.

Small GPS jitter must not rewind the active maneuver. Sustained reverse movement may lower current offset only after evidence of real backtracking. A route-prescribed U-turn is recognized by ordered route anchors, not by a simplistic "negative progress" rule.

Distances to maneuvers are measured along the route shape (route offsets), not straight-line distance to the maneuver coordinate.

## 5. Maneuver lifecycle

Each maneuver has:
- route offset/shape anchor;
- canonical maneuver code;
- optional street-name and instruction fields with provenance;
- preview, prepare and final-alert stages;
- passed/confirmed state.

Advance only when eligible post-anchor evidence supports the outgoing segment. Do not skip multiple maneuvers on one noisy fix. When close maneuvers occur, sequence them without overlapping alert storms. A new route revision resets matcher/filter/alert state only after successful commit.

## 6. Alert policy

- Alert distances must use a configurable speed/time lead-time model with per-mode caps/floors.
- Each alert stage fires at most once per route revision and maneuver.
- Suppress delayed alerts when GPS has been stale or poor; do not replay an obsolete turn alert after GPS recovers.
- Haptic failure cannot block visual guidance.
- User preferences may disable sound/vibration or tune lead distance within safe bounds.
- Initial numerical thresholds are not frozen in A6; A8/A9 must define candidate values and the field-calibration plan.

## 7. Off-route and rejoin

- A cross-track breach first enters **suspect** state.
- Confirm off-route only after eligible-fix quorum and dwell time; a single poor fix never confirms it.
- Apply hysteresis: rejoin requires repeated good matches inside a clear corridor.
- Parallel roads, loops and grade-separated crossings must be used as false-positive fixtures.
- On confirmed off-route, show the state on watch and request a new phone route only if the phone has network/provider access and watch-to-phone signaling is available.
- If uplink is unavailable, watch continues its cached route and cannot promise phone-side automatic reroute from a watch-originated event.

## 8. Reroute transaction

- Phone requests a new route using the same explicit travel mode/profile and remaining ordered stops.
- New route gets a new immutable identity/revision.
- Phone presents route changes/warnings and transfers the new route.
- Watch stages, validates and commits it under A4.
- Old active route remains usable until the replacement is committed.
- After commit, reset route matcher/maneuver/alert state to the new route revision.

## 9. Arrival

Arrival requires more than proximity to the destination coordinate:
- eligible fresh fixes;
- destination radius/corridor;
- final-route/approach evidence;
- quorum/dwell;
- single terminal transition.

Passing near a destination on a parallel road or upper/lower deck must not cause arrival. Once ARRIVED, later GPS drift cannot revert the session to NAVIGATING.

## 10. Mode-aware behavior

Matcher/alert policy may use travel mode for speed plausibility, alert lead distance and route access semantics. The route geometry itself must already be generated for the correct mode by A2. The watch cannot repair a bad road-access profile. If mode/provider metadata is missing or mismatched, reject activation.

## 11. Deterministic fixture families

At minimum create test traces for:
1. clean straight road;
2. urban junction with close turns;
3. parallel frontage road;
4. grade-separated crossing;
5. repeated-coordinate loop;
6. planned U-turn;
7. real backtrack;
8. low-speed/standing heading instability;
9. single GPS spike/teleport;
10. long GPS gap and reacquisition;
11. persistent off-route deviation and rejoin;
12. destination pass-by/parallel approach;
13. multi-stop waypoint transition;
14. mode mismatch and wrong-route profile;
15. route revision replacement while navigating.

Fixtures must record route shape, GPS samples, quality/timestamps, mode, expected match/maneuver/off-route/arrival output and policy version.

## 12. A6 acceptance criteria

- A6-AT-001: invalid/null/out-of-range fixes never enter matching.
- A6-AT-002: stale/non-monotonic fixes cannot advance progress.
- A6-AT-003: categorical Garmin quality is not displayed as metre accuracy.
- A6-AT-004: projection handles zero-length/short segments without invalid math.
- A6-AT-005: candidate search remains bounded on long routes.
- A6-AT-006: route continuity prevents jumps to a distant branch.
- A6-AT-007: heading is ignored when invalid or below a configured low-speed gate.
- A6-AT-008: parallel-road and overlapping-loop traces do not cause one-fix branch jumps.
- A6-AT-009: small backward jitter does not rewind active maneuver.
- A6-AT-010: genuine sustained backtracking is detected.
- A6-AT-011: distance-to-turn uses route offset, not Euclidean distance.
- A6-AT-012: maneuver completion requires post-anchor evidence.
- A6-AT-013: closely spaced turns do not trigger overlapping haptics.
- A6-AT-014: alert stages are one-shot per revision/maneuver.
- A6-AT-015: poor/stale fixes suppress final alerts.
- A6-AT-016: one off-route breach enters suspect, not confirmed.
- A6-AT-017: off-route quorum/dwell and rejoin hysteresis are deterministic.
- A6-AT-018: GPS loss pauses off-route confirmation.
- A6-AT-019: no uplink does not produce false reroute status.
- A6-AT-020: old route remains active until a valid new revision commits.
- A6-AT-021: destination pass-by on adjacent road does not confirm arrival.
- A6-AT-022: arrival occurs once and is terminal.
- A6-AT-023: mode/profile mismatch rejects route activation.
- A6-AT-024: the same fixture and policy version produce deterministic outputs.

## 13. Calibration gates

The following are not yet production constants: stale-fix age, plausible displacement, speed threshold for heading, candidate search radius, cross-track corridor, confidence boundaries, alert lead time, off-route quorum/dwell, rejoin corridor and arrival radius/dwell. Select these through deterministic replay and controlled Hanoi field tests on the exact watch/phone hardware. Record calibration dataset date and avoid overfitting to a single route.
