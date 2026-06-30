# Sidewinder X1 — Firmware & Tooling Maintenance Workspace

Locally-owned maintenance record for **one** Artillery Sidewinder X1 (Gen 1 Rev 1) being
recommissioned after ~5 years in storage. Self-contained: verified firmware source +
binaries, a documented configuration diff, matching TFT firmware, printed-part files,
wiring docs, and full upstream backups — all under this repo's control, independent of any
upstream GitHub repo's future activity.

> **Governing rule:** trust the hardware (`M115`/`M119`/`G29`) and the actual file
> contents — never a filename, README, or changelog. This ecosystem has repeatedly
> mislabeled capability. See [docs/verification-log.md](docs/verification-log.md).

---

## ⚠️ Headline finding (read before flashing anything)
The BLTouch build the project planned to flash — dgtaheno **`Marlin 2.1.2.1-X1-BLT.hex`** —
is **NOT Waggster-compatible as shipped.** Its `Configuration.h` leaves
`Z_MIN_PROBE_USES_Z_MIN_ENDSTOP_PIN` active *and* sets `Z_MIN_PROBE_PIN 19`, which
contradict: Marlin reads the probe on **Z-MIN**, not the **Z-MAX/pin-19** port the
Waggster mod wires to. Flashed as-is, the probe would never be seen.

**Use instead** (see [docs/decisions.md](docs/decisions.md)):
1. `firmware/reference--digant73-2.0.9.1-waggster-zmax/firmware.hex` — correct out of the
   box (X1 bed, UBL 7×7, Z-MAX pin 19), **or**
2. dgtaheno `-X1-BLT` source with **line 1279 commented out**, recompiled (for Marlin
   2.1.2.1 + exact 300×310 geometry).

Then **verify on hardware** (`M119` probe toggle, `G29`) before trusting it.

## Current state of the printer
| | |
|---|---|
| Running firmware | Marlin 2.1.2.1, dgtaheno **X1 non-BLT** (`Marlin 2.1.2.1-X1.hex`), Oct 14 2023 |
| Leveling | **None compiled in** (verified via `M115`: `Cap:AUTOLEVEL:0`, `Z_PROBE:0`; `G29`→Unknown) |
| TFT | MKS TFT28 V4.0.27.x, dgtaheno companion build |
| Host | COM3 @ 250000, CH340; TFT "Disconnect" mode needed for host access |
| Calibration (live) | Hotend PID 17.07/1.51/48.25 · Bed PID 44.56/7.25/182.52 · E-steps 440.87 |

Hardware details: [docs/hardware-baseline.md](docs/hardware-baseline.md).
Calibration to preserve across reflash: [docs/calibration-baseline.md](docs/calibration-baseline.md).

## What's planned
Add a **genuine ANTCLABS BLTouch v3.1** via the **Waggster mod** (probe signal → Z-MAX /
pin 19, reusing the LED ribbon path; sacrifices the extruder LED). Sensor + 2 m cable
ordered. Mount in PETG; fan-duct remix on standby. Wiring: [wiring/README.md](wiring/README.md).

## Repository map
```
README.md                     ← you are here
docs/
  hardware-baseline.md         canonical hardware facts (do not re-derive)
  calibration-baseline.md      live PID / e-steps + host connection notes
  verification-log.md          M115 ground truth + source-inspection proofs ★
  configuration-diff.md        8-build comparison matrix + customizations ★
  open-questions.md            Q1/Q2/Q3 answered + remaining hardware items
  decisions.md                 decisions + rationale
firmware/
  running--dgtaheno-X1-nonBLT/      what's on the printer now (hex + headers)
  target--dgtaheno-X1-BLT/          planned build (⚠ needs the 1-line probe fix)
  reference--dgtaheno-Genius-BLT/   correct z-max routing but Genius bed (220²)
  reference--digant73-2.0.9.1-waggster-zmax/  ★ correct Waggster build (source+hex+pins)
  reference--digant73-2.0.9.1-mbl/  manual-mesh reference
  reference--peterrakolcza-2.1.2/   2.1.2-era binaries (no source)
  reference--artillery3d-stock/     manufacturer stock reference
  NOTES.md                          firmware map + MD5 fingerprints
tft-firmware/                  matching dgtaheno TFT update + a BTT-based option
printed-parts/                 Waggster mount (collected) + duct/gantry/brackets
wiring/                        Waggster Mod - BLTouch v1.5.pdf + pin-mapping notes
calibration/                   Reset Settings.gcode (EEPROM reset)
archive/
  upstream-bundles/            full git bundles of all 4 upstreams (complete backup)
  SOURCES.md                   repo URLs, cloned commit SHAs, restore instructions
```
★ = start here.

## Next actions (in order)
1. Print Waggster mount (PETG); test-fit against the stock fan duct.
2. Wire the BLTouch per [wiring/README.md](wiring/README.md) (probe → Z-MAX/pin 19).
3. Flash a **correct** Waggster build (see Headline finding). Run the EEPROM reset
   (`calibration/Reset Settings.gcode` or `M502/M500`).
4. **Verify on hardware:** `M115` (Cap:Z_PROBE:1), `M119` (probe toggles), `G29` (runs).
5. Re-apply live calibration (`M301`/`M304`/`M92` then `M500`).
6. Set `M851` Z-offset; measure `NOZZLE_TO_PROBE_OFFSET` for the actual mount.
7. **Then** map the real bed warp (still unmeasured — see hardware-baseline.md).

## Deferred / out of scope
Z lead-screw lubrication; plastic→aluminium idler swap; all-metal heatbreak (current
~240 °C cap); empirical bed-warp mapping. **Out of scope:** Klipper/RPi/32-bit migration,
CNC tooling, 3D-modeling workflow.

## Provenance
All four upstreams cloned **2026-06-30**; commit SHAs and bundle-restore instructions in
[archive/SOURCES.md](archive/SOURCES.md). Firmware fingerprints in
[firmware/NOTES.md](firmware/NOTES.md).
