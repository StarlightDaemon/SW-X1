# Configuration comparison & customization diff

All values below are **first-hand from the actual `Configuration.h` files** (line numbers
are real). "Active" = uncommented `#define`; "commented" = `//#define`.

## Full comparison matrix

| Build (folder) | Bed X×Y×Z | Leveling (active) | Grid | BLTouch | Probe-pin routing | Hotend max | E-steps | Probe offset |
|---|---|---|---|---|---|---|---|---|
| `running--dgtaheno-X1-nonBLT` | 300×310×400 | **NONE** | — | off | Z-MIN (inert) | 275 | 445 | {10,10,0} vestigial |
| `target--dgtaheno-X1-BLT` | 300×310×400 | BILINEAR | 5×5 | on | ⚠ **CONTRADICTORY → Z-MIN** | 275 | 445 | {25.4,-26.4,-1} |
| `reference--dgtaheno-Genius-BLT` (`_BLT`) | 220×220×250 | BILINEAR | 5×5 | on | ✓ Z-MAX (pin 19) | 275 | 445 | {25.4,-26.4,-1} |
| `reference--dgtaheno-Genius-BLT` (`_BLT-295C`) | 220×220×250 | BILINEAR | 5×5 | on | ⚠ CONTRADICTORY → Z-MIN | **295** | 445 | {-44,-9,0} |
| `reference--digant73…waggster-zmax` | 305×305×400 | UBL | 7×7 | on | ✓ **Z-MAX (pin 19) — CANONICAL** | 275 | 445 | {33,-33,0} |
| `reference--digant73…(z_min, in bundle)` | 305×305×400 | UBL | 7×7 | on | ✓ Z-MIN | 275 | 445 | — |
| `reference--digant73-2.0.9.1-mbl` | 305×305×400 | MBL | 5×5 | off | Z-MIN (manual mesh) | 275 | 445 | — |
| `reference--artillery3d-stock` | 300×310×400 | NONE | — | off | none | 275 | 445 | — |

Marlin version: dgtaheno builds = **2.1.2.1**; digant73 + artillery3d = **2.0.x**.
Motherboard on every build: `BOARD_MKS_GEN_L`.

## The probe-pin defines, side by side (the crux)
The Waggster mod requires reading the probe on the **Z-MAX endstop = pin 19**. Three
`#define`s decide this. ✓ = correct for Waggster, ✗ = wrong/contradictory.

| Build | `Z_MIN_PROBE_USES_Z_MIN_ENDSTOP_PIN` | `USE_PROBE_FOR_Z_HOMING` | `Z_MIN_PROBE_PIN 19` | Net |
|---|---|---|---|---|
| **digant73 waggster (canonical)** | commented @1003 | active @1006 | active @1023 | **✓ Z-MAX** |
| dgtaheno `_BLT` (Genius) | commented @1279 | active @1282 | active @1299 | ✓ Z-MAX |
| **dgtaheno `-X1-BLT` (target)** | **active @1279** | active @1282 | active @1299 | **✗ → Z-MIN** |
| dgtaheno `_BLT-295C` | active @1279 | active @1282 | active @1299 | ✗ → Z-MIN |
| digant73 standard (z_min) | active @1003 | commented @1006 | commented @1023 | ✓ Z-MIN (by design) |

**Rule:** for Waggster, `Z_MIN_PROBE_USES_Z_MIN_ENDSTOP_PIN` must be **commented out** so
the `Z_MIN_PROBE_PIN 19` override takes effect. The X1-BLT build violates this.

## Customizations vs stock, explained (the dgtaheno X1 lineage)
Relative to `artillery3d-stock`, the dgtaheno X1 builds change:
- **Bed size** kept at X1 300×310×400 (Y is **310**, not 300 — matches stock). Z 400.
- **E-steps** default `{80, 80, 400, 445}` — E = 445 (extruder). *Live tuned value on the
  printer is 440.87; re-apply `M92 E440.87` after any flash.*
- **Hotend PID default** Kp 14.58 / bed Kp 244.21. *Live tuned values differ
  (Kp 17.07 hotend, 44.56 bed); re-apply after flash — see calibration-baseline.md.*
- **Hotend max temp** 275 °C. ⚠ **Hardware caps at ~240 °C** (PTFE-lined heatbreak) — do
  not exploit the firmware headroom until an all-metal heatbreak is fitted.
- **Leveling:** the running `-X1` build leaves all leveling OFF (the central surprise).
  The `-X1-BLT` build turns on BILINEAR 5×5 + BLTouch but mis-routes the probe pin (above).
- **NOZZLE_TO_PROBE_OFFSET** is build/mount-specific and must be re-measured for the
  actual printed BLTouch mount.

## Build the corrected X1 Waggster firmware (recommended recipe)
Two viable paths — see [decisions.md](decisions.md) for why:
1. **Flash digant73 `waggster_z_max` `firmware.hex` directly** (already correct: X1 bed
   305×305×400, UBL 7×7, Z-MAX pin 19, offset {33,-33,0}). Older Marlin 2.0.9.1 — fine for
   an 8-bit board. Then verify on hardware (§C of verification-log).
2. **Patch dgtaheno `-X1-BLT` source** if you want Marlin 2.1.2.1 + exact X1 geometry:
   - In `target--dgtaheno-X1-BLT/Configuration.h`, **comment out line 1279**
     (`Z_MIN_PROBE_USES_Z_MIN_ENDSTOP_PIN`). Leave 1282 and 1299 as-is.
   - (Optional) review `INPUT_SHAPING_*` in `Configuration_adv.h` — input shaping is
     atypical/often ineffective on 8-bit AVR; consider disabling. *(Not re-verified this
     session — confirm in source first.)*
   - Recompile (PlatformIO + Auto Build Marlin) against an upstream Marlin 2.1.2.1 tree
     with these headers dropped in (dgtaheno ships only the headers, not a full tree).
   - Re-measure and set `NOZZLE_TO_PROBE_OFFSET` for your mount.
