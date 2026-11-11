# Garmin Navigation

Phone-first navigation and trip-planning product inspired by the everyday practicality of Google Maps-style navigation and the route-planning/discovery experience of komoot, extended for motorized vehicles and Garmin fēnix 6S.

> **Project direction:** B — Google Maps + komoot (experience references, not API/data dependencies)  
> **Master Plan:** 1.3.0 · **A0–A7:** revision 2.0.0 · **A8:** 2.0.0 · **A9:** review 1.0.0 · **Updated:** 2026-11-11  
> **Phase:** A — Product & System Design · **Design freeze:** No · **Implementation:** Not started

## Start here

1. [Master Plan](MASTER_PLAN.md) — product vision, architecture, decisions and roadmap.
2. [Project Status](PROJECT_STATUS.md) — current work package, blockers and validation truth.
3. [Continuation Prompt](CONTINUE_PROMPT.md) — resume in a new conversation.
4. [Changelog](CHANGELOG.md) — version history.
5. [A0 Product Baseline](docs/00_PRODUCT_BASELINE_A0.md)
6. [A1 Functional Requirements](docs/01_FUNCTIONAL_REQUIREMENTS_A1.md)
7. [A2 Map, Discovery & Routing](docs/02_MAP_DISCOVERY_ROUTING_A2.md)
8. [A3 Domain & Data Model](docs/03_DOMAIN_DATA_MODEL_A3.md)
9. [A4 Garmin Sync Protocol](docs/04_GARMIN_SYNC_PROTOCOL_A4.md)
10. [A5 Garmin Capability Matrix](docs/05_GARMIN_CAPABILITY_MATRIX_A5.md)
11. [A6 Navigation Engine](docs/06_NAVIGATION_ENGINE_A6.md)
12. [A7 Offline, Recovery, Privacy & Battery](docs/07_OFFLINE_RECOVERY_PRIVACY_BATTERY_A7.md)
13. [A8 UX Specification](docs/08_UX_SPECIFICATION_A8.md)
14. [A9 Design Review & Freeze Gate](docs/09_DESIGN_REVIEW_A9.md)

## Important interpretation

“Google Maps + komoot” describes the **product experience**: fast daily search/navigation plus deliberate multi-stop trip planning and place discovery. It does not mean reusing Google or komoot map/route datasets. The current technical proposal uses one OSM-derived map/routing ecosystem for phone and Garmin compatibility. See A2 for provider and licensing constraints.

## Repository layout

```
.
├── README.md
├── MASTER_PLAN.md
├── PROJECT_STATUS.md
├── CHANGELOG.md
├── CONTINUE_PROMPT.md
└── docs/
    ├── 00_PRODUCT_BASELINE_A0.md
    ├── 01_FUNCTIONAL_REQUIREMENTS_A1.md
    ├── 02_MAP_DISCOVERY_ROUTING_A2.md
    ├── 03_DOMAIN_DATA_MODEL_A3.md
    ├── 04_GARMIN_SYNC_PROTOCOL_A4.md
    ├── 05_GARMIN_CAPABILITY_MATRIX_A5.md
    ├── 06_NAVIGATION_ENGINE_A6.md
    ├── 07_OFFLINE_RECOVERY_PRIVACY_BATTERY_A7.md
    ├── 08_UX_SPECIFICATION_A8.md
    └── 09_DESIGN_REVIEW_A9.md
```

## Status truth

The repository contains specifications only. No Flutter application, Monkey C watch application, deployed routing server, route dataset, CI acceptance test, simulator run, usability study or physical-watch validation is claimed. Acceptance tests are requirements for future verification, not tests already passed. A9 desk review is recorded, but DESIGN FROZEN remains No until mandatory wireframe/usability, Garmin, routing and payload gates are evidenced. See docs/09_DESIGN_REVIEW_A9.md.

## Reference sources

- [Garmin Connect IQ compatible devices](https://developer.garmin.com/connect-iq/compatible-devices)
- [Garmin MapView](https://developer.garmin.com/connect-iq/api-docs/Toybox/WatchUi/MapView.html)
- [Garmin Communications](https://developer.garmin.com/connect-iq/api-docs/Toybox/Communications.html)
- [Google Maps Platform Service Specific Terms](https://cloud.google.com/maps-platform/terms/maps-service-terms)
- [Google two-wheeled Routes coverage](https://developers.google.com/maps/documentation/routes/coverage-two-wheeled)
- [komoot API statement](https://support.komoot.com/hc/en-us/articles/10331570510618-komoot-API)
- [Valhalla Route API](https://valhalla.github.io/valhalla/api/route/api-reference/)
- [OSM Tile Usage Policy](https://operations.osmfoundation.org/policies/tiles/)
- [OpenStreetMap Copyright & License](https://www.openstreetmap.org/copyright)
