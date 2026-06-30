# Open questions — status

The handoff posed three "resolve by source inspection" questions plus a running
hardware-confirmation list. Status as of 2026-06-30.

## ✅ Q1 — Does `-X1-BLT` map the probe to Z-MAX (Waggster) or Z-MIN (standard)?
**ANSWERED (source-level, independently verified).** Neither cleanly. `-X1-BLT` is
**internally contradictory**: it leaves `Z_MIN_PROBE_USES_Z_MIN_ENDSTOP_PIN` **active**
(@1279) *and* sets `Z_MIN_PROBE_PIN 19` (@1299). Marlin honors the former and ignores the
latter → the probe is effectively read on **Z-MIN**, so the build is **NOT Waggster
(Z-MAX) compatible as-shipped.** The canonical correct Waggster config (digant73
`waggster_z_max`) comments that line out. **Fix:** comment line 1279, recompile.
Full evidence: [verification-log.md](verification-log.md) §B2, [configuration-diff.md](configuration-diff.md).
*Hardware confirmation still needed:* `M119` probe toggle.

## ✅ Q2 — Are the `_corrections_pending on validation` fixes folded into `X1-BLT`?
**ANSWERED.** **No.** The "corrections" = the Z-max probe-pin fix (commenting
`Z_MIN_PROBE_USES_Z_MIN_ENDSTOP_PIN`). Evidence chain:
- `Marlin 2.1.2.1_BLT.hex` is **byte-identical** (MD5 `e15b1927…`) to
  `…_BLT_corrections_pending on validation.hex` — the "corrections" build *is* `_BLT`.
- Source: only the Genius **`_BLT`** config has line 1279 commented (the fix). Both
  `-X1-BLT` and `_BLT-295C` still have it **active**.
- So the fix lives in the Genius `_BLT` build and was **never propagated** to the X1 build.
  (The separate dated `_180424.hex` is a distinct earlier binary; no source published.)

## ✅ Q3 — What `GRID_MAX_POINTS` is actually compiled in?
**ANSWERED (per active leveling system).**
- dgtaheno `-X1-BLT` (BILINEAR): **5×5**
- digant73 `waggster_z_max` (UBL): **7×7**
- digant73 `mbl` (MBL): **5×5**
- Running `-X1`: n/a (no leveling — this is *why* it was never confirmable on hardware before).

## ⏳ Still open — requires the physical printer (after BLTouch install + flash)
These cannot be settled from source. Full list in [verification-log.md](verification-log.md) §C.
- `M115` shows `Cap:AUTOLEVEL:1` / `Cap:Z_PROBE:1` after flashing a leveling build.
- **`M119` probe toggles** (definitive pin-routing proof).
- `G29` recognized + completes a mesh.
- BLTouch boot self-test passes.
- Physical `NOZZLE_TO_PROBE_OFFSET` measured for the actual printed mount.
- `_BLT-295C`'s real runtime behavior (it *does* set 295 °C in source — but irrelevant on
  this PTFE-limited hotend; do not use it for that reason).
- Whether `INPUT_SHAPING_*` (reportedly enabled in dgtaheno 2.1.x builds) actually helps
  on this 8-bit AVR — not re-verified in source this session.

## ⏳ Still open — empirical, deferred
- **Bed-warp shape** — unmeasured; map it only once a working probe/mesh exists.
  See [hardware-baseline.md](hardware-baseline.md) (the "30–50 mm raised band" claim is rejected).
- Z lead-screw lubrication; plastic→aluminium idler swap.
