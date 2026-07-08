# Current State

## Summary

- Confirmed: workspace is at the "status snapshot 2026-06-30" point recorded
  in `docs/GUIDE.md` — firmware corrected/verified, printed-part files
  vendored, wiring doc/TFT firmware/calibration baseline/upstream backups all
  in repo. Hardware work (everything from Phase 1 on) is pending on the
  BLTouch sensor arriving.
- Confirmed: the originally-planned firmware build
  (`dgtaheno Marlin-2.1.2.1-X1-BLT.hex`) was found NOT Waggster-compatible as
  shipped — it leaves the probe wired to Z-MIN instead of the Z-MAX/pin-19
  port the Waggster mod uses. A corrected build
  (`firmware/build--X1-BLT-2.1.2.1-waggster-fixed/`) was compiled this repo's
  own session and is the one to flash.
- Confirmed: bed warp is still unmeasured — deliberately deferred to Phase 5,
  not before.
- Confirmed: RAIDEN Instance installed 2026-07-08 at Edict v1.0.0. Repo now
  has a GitHub remote (`StarlightDaemon/SW-X1`, public) as of this install —
  see `RAIDEN-ops/logs/DISTRIBUTION_LOG.md` and `LOOP-0021`. The 4 pre-install
  commits were re-authored from the operator's personal git identity to the
  `StarlightDaemon` fleet identity as part of this install (GitHub blocked
  the initial push over the private-email policy); no content changed.

## Constraints

- Confirmed: single-printer, single-operator maintenance record — not a
  general-purpose Sidewinder X1 project. Scope is this one physical unit.
- Confirmed: governing verification rule — trust the hardware, not
  filenames/READMEs/changelogs (this ecosystem has repeatedly mislabeled
  capability). See `docs/verification-log.md`.

## Provenance

- RAIDEN Instance installed 2026-07-08 (Edict v1.0.0), following the scan and
  install recorded under LOOP-0021 in RAIDEN-ops.
- State captured from `README.md` and `docs/GUIDE.md` at install time — not
  migrated from a prior ledger (this is the project's first RAIDEN state
  record).
