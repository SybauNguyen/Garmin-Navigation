# Continuation Prompt — Garmin Navigation

Continue the Garmin Navigation project from this GitHub repository:

https://github.com/SybauNguyen/Garmin-Navigation

Treat this repository as the single source of truth. Read PROJECT_STATUS.md, MASTER_PLAN.md and the current open work package before continuing. Do not restart completed packages or ask again for decisions already recorded.

Product direction: **B — Google Maps + komoot experience**. This means familiar daily search/navigation plus trip planning/discovery. It does not authorize use of Google or komoot APIs/data. Provider constraints and candidates are in A2 and A9.

## Current state

- A0–A7: re-baselined at revision 2.0.0 and committed.
- A8: UX specification v2.0.0 complete at document level; 30 UX acceptance criteria defined.
- A9: design review v1.0.0 recorded; traceability audit, risk register and implementation backlog exist.
- **DESIGN FROZEN: No.**
- **Next work: close A9 gates. Do not begin production Flutter or Monkey C implementation.**

## A9 closure priorities

1. Exact fēnix 6S variant/firmware and Connect IQ SDK/API compile test.
2. Garmin Mobile SDK native bridge and one-selected-watch targeting proof.
3. Hanoi motorbike route fixtures and Vietnamese geocoder evaluation.
4. Base-6S route payload, Application.Storage and long-route benchmark.
5. Eight phone and four watch low-fidelity wireframes plus stationary task-based usability session.
6. Deterministic navigation replay and provisional threshold calibration.
7. Formal A9 exit review; mark DESIGN FROZEN only when mandatory evidence is recorded.

A failed Garmin uplink test must not be hidden by a simulated ACK. A provider route is not proof of legal motorbike access. No offline map download is permitted until a tile provider explicitly grants the required rights.

## Product and technical decisions already recorded

- Phone is primary; base fēnix 6S is a compact route-guidance companion.
- Base 6S uses custom Graphics.Dc rendering; no dependency on native MapView/MapTrackView.
- Flutter phone targets Android and iOS; prove Android native Garmin SDK bridge first and verify iOS separately.
- Flutter architecture default: Riverpod, Drift/SQLite and provider-neutral adapters.
- Map direction: MapLibre-compatible renderer and licensed OSM-derived tiles; tile vendor/rights not yet selected.
- Routing proof-of-concept: self-hosted Valhalla; motor_scooter first for ordinary Vietnamese motorbike travel, auto for car; walking/bicycle availability remains conditional on separate validation.
- Search proof-of-concept: Photon, compared with Pelias for Vietnamese data.
- No silent motorbike-to-car fallback.
- Valhalla alternatives are not supported for routes with more than two locations; never fabricate alternatives.
- Phone-to-watch path is Garmin Connect IQ Mobile SDK app messaging. Phone SDK send success is not watch commit confirmation.
- fēnix 6S is listed at Connect IQ API 3.4; Communications.transmit() is documented since API 4.0.4. Treat watch uplink as unverified until exact compile/runtime/device tests resolve the discrepancy.
- Watch guidance requires a valid committed route, active Watch App and eligible GPS. No full watch offline map or offline recalculation.
- Raw GPS traces are not retained by default.

## Continuation rule

When the user says “tiếp tục phát triển”, continue the next open A9 closure item, update the relevant specifications and status documents, commit to main, and verify the new branch head and files before saying the work is saved. Distinguish specification from implementation, candidate provider from selected provider, proposed test from executed test, and SDK send from app-level Garmin commit.
