# Sidewinder X1 — Master build & recommissioning guide

The single ordered walkthrough for taking this printer from its current state (running,
but **no bed leveling**) to a calibrated BLTouch auto-leveling machine via the Waggster
mod. Each phase links to the detail doc with exact commands/values. Do the phases in order;
don't pass a ⛔ gate that fails.

**Golden rule of this project:** trust the hardware (`M115`/`M119`/`G29`) and actual file
contents — never a filename, changelog, or web summary. See
[verification-log.md](verification-log.md).

---

## Status snapshot (2026-06-30)
| | |
|---|---|
| ✅ Firmware | Corrected, ready-to-flash build compiled & verified — `firmware/build--X1-BLT-2.1.2.1-waggster-fixed/` |
| ✅ Printed-part files | All mount/duct options vendored — `printed-parts/` ([options](../printed-parts/BLTOUCH-MOUNT-OPTIONS.md)) |
| ✅ Wiring doc, TFT fw, calibration baseline, upstream backups | in repo |
| ⏳ Hardware work | everything below Phase 1 — waiting on the BLTouch to arrive |
| ⏳ Bed warp | still **unmeasured** — mapped in Phase 5, not before |

---

## Phase 0 — Parts & printing (do now, before the sensor lands)
- [ ] **Sensor:** genuine ANTCLABS BLTouch **v3.1** (ordered). Avoid the "Artillery 3D ABL" clone.
- [ ] **Cable:** 2 m extension (ordered).
- [ ] **Print the mount** at your print service. Pick one:
  - **Option A (one part):** `printed-parts/waggster-bltouch-mount-duct/Fan_Mount_With_Duct_And_BL-Touch.stl`
  - **Option B (best cooling for a warped bed):** a Waggster mount from
    `printed-parts/waggster-bltouch-mount/` **+** `printed-parts/fan-duct-remix/Artillery_Sidewinder_X1_Futuristic_fan_duct_for_BLTouch_mount.stl`
  - **Spec sheet for the shop** (PETG, layers, supports, no-scale, licensing): [BLTOUCH-MOUNT-OPTIONS.md](../printed-parts/BLTOUCH-MOUNT-OPTIONS.md).
- [ ] **Back up current state:** connect host, run `M115` (note firmware) and `M503` (save the
      settings dump to a file). See host/TFT notes in [calibration-baseline.md](calibration-baseline.md).

## Phase 1 — Mechanical install (printer powered OFF)
- [ ] Fit the BLTouch to the printed mount; mount on the carriage; test-fit against the stock duct.
- [ ] Wire per the **authoritative** diagram — [wiring/README.md](../wiring/README.md) →
      `Waggster Mod - BLTouch v1.5.pdf`. Key mapping: **probe SIGNAL → Z-MAX endstop (pin 19)**,
      reusing the LED ribbon path (**sacrifices the extruder LED**). Follow the PDF for exact
      wire colours / the servo lead — don't guess pinouts.

## Phase 2 — Flash firmware
- [ ] Choose the build ([full table](../firmware/NOTES.md)):
  - ★ **`firmware/build--X1-BLT-2.1.2.1-waggster-fixed/Marlin-2.1.2.1-X1-BLT-waggster-fixed.hex`**
    (Marlin 2.1.2.1, exact X1 geometry, probe fix applied). **or**
  - `firmware/reference--digant73-2.0.9.1-waggster-zmax/firmware.hex` (UBL 7×7 — more mesh points).
  - ⛔ **Never** flash the unpatched `firmware/target--dgtaheno-X1-BLT/…` — it routes the probe to Z-MIN.
- [ ] TFT → **Disconnect** mode so the host owns the serial bus.
- [ ] Flash via Prusa Slicer → COM3. Then reset EEPROM: `M502` → `M500` → `M501`.

Exact command-by-command procedure: **[install-runbook.md](install-runbook.md)** §3.

## Phase 3 — ⛔ Verify before trusting (the whole point)
- [ ] `M115` → **`Cap:AUTOLEVEL:1`, `Cap:Z_PROBE:1`** (was 0/0).
- [ ] BLTouch boot self-test: pin deploys→retracts.
- [ ] **`M119` probe toggle** — `M401` deploy, push the pin by hand, `M119` reads TRIGGERED,
      `M402` retract. **If it never toggles, STOP — wrong pin.** This is the definitive proof.
- [ ] `G29` is recognized (was "Unknown command").

Full checklist + why: [install-runbook.md](install-runbook.md) §4, [verification-log.md](verification-log.md) §C.

## Phase 4 — Calibrate
- [ ] Re-apply saved calibration (values → [calibration-baseline.md](calibration-baseline.md)):
      `M301 P17.07 I1.51 D48.25`, `M304 P44.56 I7.25 D182.52`, `M92 E440.87`, `M500`.
- [ ] **Probe offset for your mount** — measure nozzle↔probe offset; set `M851` (Z) and
      `NOZZLE_TO_PROBE_OFFSET` (X/Y). The baked-in values are mount-specific, not authoritative.
- [ ] **Z-offset:** `G28` → paper/babystep → `M851 Z-…` → `M500`.
- [ ] `G28` → `G29` (build first mesh) → `M420 S1`. Add `M420 S1` after `G28` in your slicer start G-code.

## Phase 5 — Map the warp, first prints, tune
- [ ] Print a large single-layer bed-level test.
- [ ] **`M420 V`** — dump the probed mesh. **This is the first real measurement of the bed
      warp** (previously unmeasured; the "30–50 mm band" claim was rejected —
      [hardware-baseline.md](../docs/hardware-baseline.md)). Record it.
- [ ] If first-layer/overhang cooling is weak: keep the mount, swap to the alguiens
      **Futuristic** duct (`printed-parts/fan-duct-remix/`). If warp is severe, consider the
      digant73 UBL 7×7 firmware for more mesh points.

## ⚠ Safety
Keep real hotend temps **≤ ~240 °C** — the PTFE-lined heatbreak degrades above that even
though firmware caps allow 275 °C. Do **not** use the `_BLT-295C` build.

## Deferred maintenance (do opportunistically)
Z lead-screw lubrication (stick-slip), plastic→aluminium extruder idler, all-metal
heatbreak (to lift the 240 °C ceiling). Details: [hardware-baseline.md](hardware-baseline.md).

## Rollback
Re-flash `firmware/running--dgtaheno-X1-nonBLT/Marlin 2.1.2.1-X1.hex` for the known-good
pre-BLTouch state.

## Why these choices
Decision log with rationale: [decisions.md](decisions.md). Open questions (all answered by
source inspection) + remaining hardware items: [open-questions.md](open-questions.md).
Every firmware customization explained: [configuration-diff.md](configuration-diff.md).
