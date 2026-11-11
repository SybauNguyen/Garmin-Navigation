# Garmin Navigation

Phone-first navigation and trip-planning product inspired by the everyday practicality of Google Maps and the route-planning/discovery experience of komoot, extended for motorized vehicles and Garmin fēnix 6S.

> **Project baseline:** Product direction B — Google Maps + komoot (experience references, not API/data dependencies)  
> **Master plan:** 1.0.0 · **A0–A7 specification revision:** 2.0.0 · **Updated:** 2026-11-11  
> **Phase:** A — Product & System Design · **Design freeze:** No · **Implementation:** Not started

## Start here

1. [Master Plan](MASTER_PLAN.md) — product vision, architecture, phase gates and current decisions.
2. [Project Status](PROJECT_STATUS.md) — completed work, blockers and next package.
3. [Continuation Prompt](CONTINUE_PROMPT.md) — copy into a new conversation to continue without restarting.
4. [A0 Product Baseline](docs/00_PRODUCT_BASELINE_A0.md)
5. [A1 Functional Requirements](docs/01_FUNCTIONAL_REQUIREMENTS_A1.md)
6. [A2 Map, Discovery & Routing](docs/02_MAP_DISCOVERY_ROUTING_A2.md)
7. [A3 Domain & Data Model](docs/03_DOMAIN_DATA_MODEL_A3.md)
8. [A4 Garmin Sync Protocol](docs/04_GARMIN_SYNC_PROTOCOL_A4.md)
9. [A5 Garmin Capability Matrix](docs/05_GARMIN_CAPABILITY_MATRIX_A5.md)
10. [A6 Navigation Engine](docs/06_NAVIGATION_ENGINE_A6.md)
11. [A7 Offline, Recovery, Privacy & Battery](docs/07_OFFLINE_RECOVERY_PRIVACY_BATTERY_A7.md)

## Important interpretation

“Google Maps + komoot” describes the **product experience**: fast search and daily navigation plus deliberate multi-stop trip planning and place discovery. It does not mean reusing Google or komoot map/route datasets. The primary technical proposal keeps one OSM-derived map/routing ecosystem for phone and Garmin compatibility. Current Google Maps Platform terms restrict combining several Google Maps content types with non-Google maps; komoot states it has no publicly accessible API. See A2 before adding any provider.

## Proposed repository structure

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
    └── 08_UX_SPECIFICATION_A8.md  # added as a separate milestone after A0–A7 verification
```

## Status truth

This repository currently contains specifications only. No Flutter application, Monkey C watch application, deployed routing server, route dataset, CI acceptance test, simulator run or physical-watch validation is claimed. Requirements and acceptance tests in the documents are proposed verification criteria, not evidence of tests already passed.

## Reference sources

- [Garmin Connect IQ compatible devices](https://developer.garmin.com/connect-iq/compatible-devices)
- [Garmin MapView](https://developer.garmin.com/connect-iq/api-docs/Toybox/WatchUi/MapView.html)
- [Garmin Communications](https://developer.garmin.com/connect-iq/api-docs/Toybox/Communications.html)
- [Garmin Application.Storage](https://developer.garmin.com/connect-iq/api-docs/Toybox/Application/Storage.html)
- [Google Maps Platform Service Specific Terms](https://cloud.google.com/maps-platform/terms/maps-service-terms)
- [Google two-wheeled Routes coverage](https://developers.google.com/maps/documentation/routes/coverage-two-wheeled)
- [Google two-wheeled Routes guidance](https://developers.google.com/maps/documentation/routes/route_two_wheel)
- [komoot API statement](https://support.komoot.com/hc/en-us/articles/10331570510618-komoot-API)
- [Valhalla Route API](https://valhalla.github.io/valhalla/api/route/api-reference/)
- [OSM Tile Usage Policy](https://operations.osmfoundation.org/policies/tiles/)
- [OpenStreetMap Copyright & License](https://www.openstreetmap.org/copyright)
