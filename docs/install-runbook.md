# BLTouch install & verification runbook

Do this **in order** the day the BLTouch arrives. Boxes are checkpoints — don't proceed
past a failed one. Keep the current firmware handy to roll back
(`firmware/running--dgtaheno-X1-nonBLT/`).

---

## 0 · Pre-flight (before touching the printer)
- [ ] Confirm the sensor is a **genuine ANTCLABS BLTouch v3.1** (not the "Artillery 3D ABL" clone).
- [ ] Print the mount in **PETG** — `printed-parts/waggster-bltouch-mount/SWX1-BLT-Bracket.stl`.
      **Test-fit against the stock part-cooling duct.** If it collides, print the duct in
      `printed-parts/fan-duct-remix/` first.
- [ ] Gather: chosen firmware `.hex`, Prusa Slicer (flasher), USB cable, 2 mm hex, the 2 m extension.
- [ ] **Record current state** so nothing is lost: connect host (TFT → Disconnect mode
      first), run `M115` (note firmware) and `M503` (save the full settings dump to a file).

## 1 · Choose firmware
| Option | File | Marlin | Bed | Leveling | Notes |
|---|---|---|---|---|---|
| **A (recommended)** | `firmware/build--X1-BLT-2.1.2.1-waggster-fixed/…-waggster-fixed.hex` | 2.1.2.1 | 300×310×400 | Bilinear 5×5 | Current Marlin, exact X1 geometry, probe fix applied + built this session |
| **B (proven, best for heavy warp)** | `firmware/reference--digant73-2.0.9.1-waggster-zmax/firmware.hex` | 2.0.9.1 | 305×305×400 | **UBL 7×7** | Prebuilt & correct out of the box; more mesh points |
| ❌ Do NOT flash | `firmware/target--dgtaheno-X1-BLT/…-X1-BLT.hex` | 2.1.2.1 | 300×310×400 | Bilinear 5×5 | Probe forced to **Z-MIN** — won't see a Waggster-wired probe |

Both A and B route the probe to **Z-MAX / pin 19** (Waggster). For a badly warped bed, B's
UBL 7×7 resolves warp better; A can be bumped to 7×7 later (`GRID_MAX_POINTS`) if desired.

## 2 · Wire the BLTouch (printer **powered OFF**)
- [ ] Fasten the BLTouch to the printed bracket; mount on the carriage.
- [ ] Route the cable along the **existing extruder LED ribbon path** and connect per the
      **authoritative diagram**: [`wiring/Waggster Mod - BLTouch v1.5.pdf`](../wiring/Waggster%20Mod%20-%20BLTouch%20v1.5.pdf).
      The key mapping: the **probe SIGNAL goes to the Z-MAX endstop connector (pin 19)**;
      this reuses the LED port and **sacrifices the extruder LED**. Follow the PDF for the
      exact wire colors and the servo/control lead — do not guess pinouts.

## 3 · Flash
- [ ] TFT → **Disconnect** mode (Menu → Settings → Connection) so the host owns the serial bus.
- [ ] Prusa Slicer → *Configuration → Flash printer firmware* → select the chosen `.hex` →
      port **COM3** → **Flash**.
- [ ] After flashing, reset EEPROM to the new firmware's defaults: **`M502`** then **`M500`** then **`M501`**
      (or "print" `calibration/Reset Settings.gcode`).

## 4 · Verify the firmware BEFORE trusting it (the whole point of this project)
- [ ] **`M115`** → must now show **`Cap:AUTOLEVEL:1`** and **`Cap:Z_PROBE:1`** (was 0/0).
- [ ] **Boot self-test:** on power-up the BLTouch pin should deploy→retract (and blink). If
      not, fix control/servo wiring before anything else.
- [ ] **`M119` pin-routing proof (critical):** `M401` (deploy) → gently push the pin up by
      hand → `M119` should read the Z-probe / Z-MIN endstop **TRIGGERED**; release → `M402`
      (retract). **If `M119` never changes, STOP — the probe is on the wrong pin** (the exact
      failure mode the unpatched X1-BLT build would have). This is the definitive test.
- [ ] **`G29`** must be a recognized command (previously "Unknown command").

## 5 · Calibrate
- [ ] Re-apply the saved calibration (values in [calibration-baseline.md](calibration-baseline.md)):
      `M301 P17.07 I1.51 D48.25`, `M304 P44.56 I7.25 D182.52`, `M92 E440.87`, then `M500`.
- [ ] **Probe offset:** measure the physical nozzle↔probe-tip offset for *your* mount and set
      it (`M851` for Z; X/Y come from `NOZZLE_TO_PROBE_OFFSET`). The builds' baked-in offsets
      differ (A: {25.4,-26.4,-1}; B: {33,-33,0}) and are **not** authoritative for your mount.
- [ ] **Z-offset:** `G28`, then paper/babystep to set `M851 Z-…`, `M500`.
- [ ] `G28` → `G29` to build the first mesh → `M420 S1` to enable it. Add `M420 S1` after
      `G28` in your slicer's start G-code so leveling is applied every print.

## 6 · First print + finally map the warp
- [ ] Print a large single-layer bed-level test.
- [ ] `M420 V` to dump the probed mesh — **this is the first real measurement of the bed
      warp** (still unmeasured; the "30–50 mm band" claim was rejected). Record it; it drives
      any future glass shimming / manual-corner decisions.

## ⚠ Safety reminder
Keep real hotend temps **≤ ~240 °C** — the PTFE-lined heatbreak degrades above that even
though the firmware caps allow 275 °C. (Do not use the `_BLT-295C` build.)

## Rollback
Re-flash `firmware/running--dgtaheno-X1-nonBLT/Marlin 2.1.2.1-X1.hex` to return to the
known-good pre-BLTouch state.
