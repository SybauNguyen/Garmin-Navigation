# Continuation Prompt — Garmin Navigation

Continue the Garmin Navigation project from this GitHub repository:

https://github.com/SybauNguyen/Garmin-Navigation

Read `PROJECT_STATUS.md`, `MASTER_PLAN.md` and the next package specification first. Treat this repository as the single source of truth. Do not restart completed packages or ask again for decisions already recorded.

Product direction: **B — Google Maps + komoot experience**. It means familiar daily search/navigation plus trip planning/discovery. It does not authorize use of Google or komoot APIs/data. Provider constraints and candidates are in A2.

Current state:
- A0–A7: re-baselined at revision 2.0.0.
- A8: UX specification v1.0.0 complete at specification level; wireframes/usability validation not yet done.
- **Next: A9 — Design Review & Freeze.**
- Do not write production Flutter or Monkey C code until A9 explicitly states DESIGN FROZEN.

For every “tiếp tục phát triển” request, advance one package only. Update the relevant spec, `PROJECT_STATUS.md`, `MASTER_PLAN.md`, `CHANGELOG.md` and this prompt; commit the change and verify the branch/files before saying it is saved.

Always distinguish requirements from implementation; candidate providers from frozen decisions; proposed tests from executed tests; official documents from physical-device observations; and sent data from app-level confirmed Garmin sync.

Do not claim route legality, Garmin uplink, map rendering, offline recovery, GPS accuracy, battery duration or UX usability has passed without evidence on the exact provider/device/platform.
