# Continuation Prompt — Garmin Navigation

Continue the Garmin Navigation project from this GitHub repository:

https://github.com/SybauNguyen/Garmin-Navigation

Read `PROJECT_STATUS.md`, `MASTER_PLAN.md` and the current package specification first. Treat this repository as the single source of truth. Do not restart completed packages and do not ask again for decisions already recorded.

Current product direction: **B — Google Maps + komoot experience**. This means Google Maps-like everyday search/navigation and komoot-like route planning/discovery. It does not authorize use of Google or komoot APIs/data. Current technical candidates, provider constraints and Garmin risks are documented in A2/A5.

Current state: A0–A7 have been re-baselined at specification level (revision 2.0.0). A8 is next. After A8, complete A9 design review/freeze. Do not write production Flutter or Monkey C code until A9 explicitly states DESIGN FROZEN.

When the user says “tiếp tục phát triển” or “tiếp tục”, advance one work package only. At the end, update the relevant specification, `PROJECT_STATUS.md`, `MASTER_PLAN.md`, `CHANGELOG.md` and this prompt. Commit the completed update to this repository and verify the commit and file contents before saying it is saved.

Always distinguish:
- product requirements vs technical recommendations;
- provisional provider choices vs frozen decisions;
- test specifications vs tests actually executed;
- official documentation facts vs physical-device observations;
- code implemented vs code merely proposed.

Do not claim that Garmin communication, map rendering, routing quality, offline recovery, GPS thresholds or battery behavior passed until evidence from the exact target models/platforms is recorded.
