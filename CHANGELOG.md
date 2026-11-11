# Changelog

All entries describe specification/repository changes, not application releases.

## 1.3.0 — 2026-11-11 — A9 design review and freeze gates

- Added A9 traceability review across A0–A8, architecture decisions, risk register and implementation backlog.
- Recorded the formal outcome: design freeze is not approved; production code remains blocked pending explicit evidence.
- Made the fēnix 6S API 3.4 versus Communications.transmit() API 4.0.4 discrepancy an explicit compile/device gate.
- Added gates for Garmin Mobile SDK selected-device targeting, Hanoi motorbike routes, base-6S route/storage budget, wireframe/usability and navigation calibration.
- Updated README, Master Plan, Project Status and Continuation Prompt. No production implementation or test pass is claimed.

## 1.2.0 — 2026-11-11 — A8 aligned with A0–A7 revision 2.0.0

- Upgraded A8 from 1.0.0 to 2.0.0 against the re-baselined A0–A7 product and platform contracts.
- Clarified P0 motorbike/car versus P1 bicycle/walking mode presentation and the distinction between product mode and routing profile.
- Added multi-stop alternative limitations, phone-only fallback, Connect IQ app-message terminology, conditional watch uplink and watch GPS inactive/resume behavior.
- Expanded A8 traceability from 24 to 30 UX acceptance criteria.
- Updated README, Master Plan, Project Status and Continuation Prompt. A9 remains next; no wireframe usability or physical-device UX test is claimed.

## 1.1.0 — 2026-11-11 — A8 UX specification

- Added A8 UX specification for five-tab phone navigation, map/search/place/Explore/Plan/route preview/navigation/Trips/Garmin/settings flows.
- Defined a distinct watch UX for 240×240 fēnix 6S: HUD, route overview, trip status and pause/arrival/error states.
- Added interruption/error/empty states, safety, accessibility, localization and 24 UX acceptance criteria.
- Updated project status and continuation point to A9 design review/freeze.
- No wireframe, usability session, UI implementation or acceptance test is claimed.

## 1.0.0 — 2026-11-11 — Product re-baseline

- Reframed product around direction B: Google Maps-like daily map/search/navigation plus komoot-like trip planning/discovery.
- Clarified that product inspiration does not authorize third-party API/data reuse.
- Re-based A0–A7 to revision 2.0.0 and committed the new specification set.
- Recorded provider/legal constraints and Garmin fēnix 6S hardware verification gates.
- No implementation code or acceptance tests executed.

## 0.7.0 — 2026-11-09 — Legacy baseline

- Previous baseline captured A0–A6 specification work; A7 was the next work package.
- Superseded by revision 1.0.0 after product direction change.
