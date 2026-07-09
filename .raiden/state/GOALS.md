# Goals

## Primary Goal

Recommission one Artillery Sidewinder X1 (Gen 1 Rev 1) after ~5 years in
storage, adding BLTouch auto-leveling via the Waggster mod, with everything
under this repo's control (verified firmware source + binaries, configuration
diff, TFT firmware, printed-part files, wiring docs, upstream backups) —
independent of any upstream GitHub repo's future activity.

## Milestones

| # | Milestone | Status |
|---|---|---|
| 1 | RAIDEN Orientation | Done |
| 2 | Firmware build corrected & verified (probe-pin fix) | Done — `firmware/build--X1-BLT-2.1.2.1-waggster-fixed/` |
| 3 | Printed-part files vendored (mount/duct options) | Done |
| 4 | Wiring doc, TFT firmware, calibration baseline, upstream backups | Done |
| 5 | Parts ordered (BLTouch v3.1, extension cable) | In progress (per docs/GUIDE.md Phase 0) |
| 6 | Mechanical install + wiring | Pending — hardware work, waiting on BLTouch |
| 7 | Flash firmware, verify probe (`M119` toggle), `G29` | Pending |
| 8 | Bed warp mapping + calibration | Pending — explicitly deferred until Phase 5 |

## Governing Rule

Trust the hardware (`M115`/`M119`/`G29`) and actual file contents — never a
filename, README, or changelog. See `docs/verification-log.md`.

## Provenance

RAIDEN Instance installed 2026-07-08. Goals captured from
`README.md` and `docs/GUIDE.md` at install time.
