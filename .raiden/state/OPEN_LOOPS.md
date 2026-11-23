# Open Loops

<!-- Statuses: open · deferred · held: <who> · no-action · accepted · closed. Each active item: ID, status, gate owner, next step or restart condition, evidence. IDs never change or get reused. Entries below stay in their original order. -->

Work must be executed one loop at a time.

## Active

- **LOOP-001 · held: owner · gate: hardware** · BLTouch install, firmware flash and verification (`docs/GUIDE.md` Phases 1–4). Restarts when: the BLTouch v3.1 sensor and cable arrive. Closes when `M119` toggles live and `G29` runs.
- **LOOP-002 · held: owner · gate: hardware** · Bed warp mapping and calibration (Phase 5). Restarts when: LOOP-001 closes.

## LOOP-001: BLTouch mechanical install + firmware flash + verification

- Status: held: owner (hardware work; waiting on parts)
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

- Status: held: owner (hardware work; after LOOP-001)
- Gate: hardware
- Scope: Phase 5 of `docs/GUIDE.md` — map the bed warp and calibrate. Not to
  be started before Phase 3's verification gate passes.
- Readiness: blocked on LOOP-001.
- Closure condition: bed warp mapped, calibration complete, printer verified
  ready for normal use.

## Provenance

- RAIDEN Instance installed 2026-07-08. No prior ledger to
  migrate from — this is the project's first RAIDEN state record.
