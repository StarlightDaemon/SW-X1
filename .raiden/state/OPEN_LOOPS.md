# Open Loops

Work must be executed one loop at a time.

## LOOP-001: BLTouch mechanical install + firmware flash + verification

- Status: open
- Gate: hardware
- Scope: complete `docs/GUIDE.md` Phases 1-4 — mount the BLTouch, wire per
  the Waggster PDF, flash `firmware/build--X1-BLT-2.1.2.1-waggster-fixed/`,
  and run the Phase 3 verification gate (`M115` capability flags, boot
  self-test, `M119` probe toggle, `G29` recognized) before trusting the
  install.
- Readiness: blocked — waiting on the BLTouch v3.1 sensor and extension cable
  to arrive (both ordered per `docs/GUIDE.md` Phase 0).
- Closure condition: `M119` probe toggle confirmed live (the project's own
  definitive proof standard — "if it never toggles, STOP, wrong pin"), `G29`
  runs without error.

## LOOP-002: bed warp mapping + calibration

- Status: open
- Gate: hardware
- Scope: Phase 5 of `docs/GUIDE.md` — map the bed warp and calibrate. Not to
  be started before Phase 3's verification gate passes.
- Readiness: blocked on LOOP-001.
- Closure condition: bed warp mapped, calibration complete, printer verified
  ready for normal use.

## Provenance

- RAIDEN Instance installed 2026-07-08. No prior ledger to
  migrate from — this is the project's first RAIDEN state record.
