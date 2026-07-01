# X1-BLT 2.1.2.1 — Waggster-fixed build

A corrected, **ready-to-flash** firmware built this session (2026-06-30). It is dgtaheno's
`Marlin 2.1.2.1-X1-BLT` with the single probe-pin bug fixed, so the BLTouch is read on the
**Z-MAX / pin 19** connector the Waggster mod wires to.

## Artifact
- **`Marlin-2.1.2.1-X1-BLT-waggster-fixed.hex`** — 326,346 bytes, MD5 `ddde4fc3f13dee23b9e6bad17e3bb845`.
- For comparison, dgtaheno's official (buggy) `-X1-BLT.hex` is 326,072 bytes — a 274-byte
  difference, confirming this is a faithful rebuild of the same config **plus** the fix.

## The only change vs dgtaheno's source
`Configuration.h` line 1279, `Z_MIN_PROBE_USES_Z_MIN_ENDSTOP_PIN`, **commented out**.
Verified in the compiled tree:
```
1279 : //#define Z_MIN_PROBE_USES_Z_MIN_ENDSTOP_PIN   (was active → forced probe to Z-MIN)
1282 :   #define USE_PROBE_FOR_Z_HOMING               (active)
1299 :   #define Z_MIN_PROBE_PIN 19                   (active → Z-MAX endstop pin)
1336 :   #define BLTOUCH                              (active)
1878 :   #define AUTO_BED_LEVELING_BILINEAR           (active, 5×5)
```
This matches digant73's canonical `waggster_(z_max_used)` recipe. See
[../../docs/configuration-diff.md](../../docs/configuration-diff.md).

## Build result
- Env `mega2560` (ATmega2560). PlatformIO 6.1.19. **SUCCESS.**
- **Flash 45.7%** (116,020 / 253,952 B) — fits comfortably.
- **RAM 84.9%** (6,954 / 8,192 B) — high but valid. ⚠ Typical for Marlin + ABL on 8-bit;
  a reason **not** to enable extra features on this board. If you see erratic behavior,
  SRAM pressure is the first suspect.

## Reproduce
```sh
python3 -m venv pio-venv && ./pio-venv/bin/pip install platformio
git clone --depth 1 --branch 2.1.2.1 https://github.com/MarlinFirmware/Marlin.git marlin-2.1.2.1
#   (upstream commit 09d0b4d15228b3efb6fd765f1e1574f521df2e27)
cp Configuration.h Configuration_adv.h marlin-2.1.2.1/Marlin/    # the fixed headers in this folder
./pio-venv/bin/pio run -d marlin-2.1.2.1 -e mega2560
#   -> marlin-2.1.2.1/.pio/build/mega2560/firmware.hex
```
The `Configuration.h` / `Configuration_adv.h` in this folder are the exact (fixed) inputs.

## Before relying on it — hardware checks
Flashing is not proof. Run the [install runbook](../../docs/install-runbook.md) §4:
`M115` (Cap:Z_PROBE:1), **`M119` (probe toggles — the definitive pin-routing proof)**, `G29`.
Also set `NOZZLE_TO_PROBE_OFFSET`/`M851` for *your* physical mount — the baked-in
`{25.4,-26.4,-1}` is dgtaheno's value, not authoritative for your bracket.
