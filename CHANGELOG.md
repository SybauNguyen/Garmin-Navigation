# Changelog

All entries describe specification/repository changes, not application releases.

## 1.0.0 — 2026-11-11 — Product re-baseline

- Reframed the product around direction B: Google Maps-like daily map/search/navigation plus komoot-like trip planning and discovery.
- Made the distinction explicit between product inspiration and permission to use third-party APIs/data.
- Replaced the old provisional assumption that Google Routes/Places could simply be combined with OSM/Garmin content. Current non-EEA terms require a provider-isolation decision.
- Re-centered the MVP on Vietnamese motorbike navigation, while retaining car, walking and bicycle profiles.
- Kept OSM-derived tiles + MapLibre-compatible rendering and Valhalla as the primary technical candidates, subject to field, license and provider validation.
- Re-stated Garmin fēnix 6S constraints: 240×240/API 3.4, native MapView only for listed Pro/Sapphire variants; watch-to-phone transmit remains a compile/runtime/physical-test gate.
- Re-baselined A0–A7 to revision 2.0.0. Legacy A1–A6 counts are historical only; new test criteria replace, not silently inherit, them.
- No implementation code or acceptance tests were executed.

## 0.7.0 — 2026-11-09 — Legacy baseline

- Prior baseline captured A0–A6 specification work and A7 as the next work package.
- This baseline is superseded by revision 1.0.0 after the product direction change.
