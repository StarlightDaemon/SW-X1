# Verification log

The governing rule of this project: **trust the hardware and the file contents, never a
filename, README, or changelog.** This log records what was actually checked and how.

---

## A. Hardware ground truth (live, this printer, this session)
Interrogated directly over USB (COM3 @ 250000, CH340; TFT in Disconnect mode):

- Running firmware: **Marlin 2.1.2.1, dgtaheno X1 non-BLT build** (`Marlin 2.1.2.1-X1.hex`), compiled Oct 14 2023.
- `M115` capability report: **`Cap:AUTOLEVEL:0`, `Cap:Z_PROBE:0`, `Cap:LEVELING_DATA:0`.**
- `G29` → **"Unknown command."**
- **Conclusion:** the running firmware has *zero* bed-leveling compiled in — not MBL, not
  ABL — despite the upstream README listing "Manual Bed leveling" as a family feature.
- TFT: MKS TFT28 V4.0.27.x, dgtaheno companion build. Serial shares the USB bus
  (MKS Gen L v1.0 hardware limitation); TFT "Disconnect" mode confirmed working for host access.

## B. Source-inspection verification (this session)
Method: all four upstream repos cloned in full; the running build's config plus 7 other
candidate configs read directly; the load-bearing `#define`s grepped first-hand (active
vs commented), then a 14-agent verification workflow independently re-read the same files
and three central claims were checked by independent skeptics. Line numbers below are
first-hand from the actual `Configuration.h` files.

### B1. The running build matches the hardware (no leveling) — CONFIRMED
`src/Marlin 2.1.2.1-X1/Configuration.h`: every leveling selector commented
(`//#define AUTO_BED_LEVELING_3POINT/LINEAR/BILINEAR/UBL` @1876–1879,
`//#define MESH_BED_LEVELING` @1880), `//#define BLTOUCH` @1336,
`//#define USE_PROBE_FOR_Z_HOMING` @1282. **Exactly consistent with `M115`.** This closed
the loop: the firmware really is leveling-less, not mislabeled-but-capable.

### B2. ⚠ The planned BLTouch build is mis-wired for Waggster — CONFIRMED (independent verdict: *confirmed*)
`src/Marlin 2.1.2.1-X1-BLT/Configuration.h` (the build the user planned to flash):
- `#define Z_MIN_PROBE_USES_Z_MIN_ENDSTOP_PIN` **active** @1279
- `#define Z_MIN_PROBE_PIN 19` **active** @1299
- `#define USE_PROBE_FOR_Z_HOMING` active @1282, `#define BLTOUCH` active @1336

These two are **mutually contradictory.** In Marlin, when
`Z_MIN_PROBE_USES_Z_MIN_ENDSTOP_PIN` is enabled the probe is forced onto the **Z-MIN**
pin and the explicit `Z_MIN_PROBE_PIN 19` (Z-MAX) override is **ignored**. The Waggster
mod wires the BLTouch signal to the **Z-MAX / pin-19** connector — so with this build
**as-shipped, Marlin would read the probe on the wrong (Z-MIN) pin and never see it.**

**Canonical contrast** — digant73's explicitly-labeled `bltouch_waggster_mod_(z_max_used)`
does it correctly: `//#define Z_MIN_PROBE_USES_Z_MIN_ENDSTOP_PIN` **commented** @1003
(its own comment: *"disabled due to usage of probe on Z max endstop"*) +
`#define USE_PROBE_FOR_Z_HOMING` @1006 + `#define Z_MIN_PROBE_PIN 19` @1023.

**Fix:** comment out line 1279 in the X1-BLT source and recompile — it then equals the
canonical Waggster recipe. **Hardware proof still required:** `M119` with the probe
deployed/retracted must show the endstop state toggle (see §C).

### B3. The "corrections pending validation" question — RESOLVED
- Binary fact (my direct MD5): `Marlin 2.1.2.1_BLT.hex` ≡ `Marlin 2.1.2.1_BLT_corrections_pending on validation.hex` (both `e15b1927…`) — **byte-identical**; the dated `_180424.hex` differs. So "corrections pending" = a relabeled copy of `_BLT.hex`.
- Source fact: the **only** dgtaheno BLTouch config with the **correct** Z-max routing
  (line 1279 *commented*) is the Genius **`_BLT`** build. Both `-X1-BLT` **and**
  `_BLT-295C` leave line 1279 **active** (contradictory).
- **Therefore:** the "correction" embodied in `_BLT.hex` is precisely the Z-max probe-pin
  fix, and it was **never propagated** into the `-X1-BLT` build (nor `_BLT-295C`). The
  corrections are **not** folded into the X1-BLT firmware.

### B4. Grid points (for the active leveling system) — CONFIRMED
- `-X1-BLT` (AUTO_BED_LEVELING_BILINEAR): **5×5** (`GRID_MAX_POINTS_X 5` @1958, in the LINEAR/BILINEAR block)
- digant73 waggster (AUTO_BED_LEVELING_UBL): **7×7** (`GRID_MAX_POINTS_X 7` @1609, in the UBL block)
- digant73 mbl (MESH_BED_LEVELING): **5×5** (@1629, in the MBL block)
The running `-X1` value is moot (no leveling active). This finally answers the grid
question that could never be confirmed on hardware (the running hex had no leveling at all).

### B5. Bed / temp / motion constants (first-hand grep)
| Build | Bed X×Y×Z | Hotend max | E-steps | Hotend Kp / Bed Kp (fw default) |
|---|---|---|---|---|
| dgt-X1 (running) | 300×310×400 | 275 | 445 | 14.58 / 244.21 |
| dgt-X1-BLT (target) | 300×310×400 | 275 | 445 | 14.58 / 244.21 |
| dgt _BLT (Genius) | 220×220×250 | 275 | 445 | 9.31 / 92.46 |
| dgt _BLT-295C (Genius) | 220×220×250 | **295** | 445 | 9.31 / 92.46 |
| digant73 waggster z_max | 305×305×400 | 275 | 445 | 22.20 / 10.00 |
| artillery3d stock | 300×310×400 | 275 | 445 | — |

⚠ Every firmware hotend cap (275, and 295 in the 295C build) **exceeds this printer's
~240 °C PTFE-lined heatbreak limit.** Firmware permitting 275/295 °C does not make it safe
on this hardware — keep real hotend temps ≤ ~240 °C until the heatbreak is replaced with
an all-metal one.

## C. Still requires the physical printer (do NOT assert until checked)
1. `M115` after flashing a leveling build → expect `Cap:AUTOLEVEL:1`, `Cap:Z_PROBE:1`.
2. **`M119` with the BLTouch deployed vs retracted → the endstop state must toggle.** This
   is the definitive proof the probe is read on the correct pin (the exact thing the
   X1-BLT contradiction would break).
3. `G29` must be recognized and complete a mesh.
4. BLTouch boot self-test (servo/control wiring).
5. Physical `NOZZLE_TO_PROBE_OFFSET` measured for the actual printed mount (builds disagree:
   X1-BLT `{25.4,-26.4,-1}`, digant73 `{33,-33,0}`, 295C `{-44,-9,0}` — must match *your* mount).
6. `G28` with Z-safe-homing: probe deploys before Z descends, homes at bed center without crashing.
7. Re-apply EEPROM calibration (PID, E-steps) — see [calibration-baseline.md](calibration-baseline.md).

## D. Methodology note
14 verification agents (≈665k tokens) profiled all 8 configs, diffed the Waggster vs
standard pin mapping, and three independent skeptics re-read the raw files to refute the
central claims; all three returned *confirmed*. Where the workflow's reconcile pass
over-corrected (it briefly claimed `_BLT-295C` had no 295 °C source), the first-hand grep
above overrides it: `_BLT-295C/Configuration.h:624` does read `HEATER_0_MAXTEMP 295`.
