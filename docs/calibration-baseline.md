# Calibration baseline

These are **live values read from the printer this session** (after an EEPROM reset),
i.e. the current runtime/EEPROM state — **not** firmware compile-time defaults. Preserve
them across any reflash: re-apply with `M301`/`M304`/`M92`, then `M500`.

| Parameter | Value | Re-apply with |
|---|---|---|
| Hotend PID | **Kp 17.07 · Ki 1.51 · Kd 48.25** | `M301 P17.07 I1.51 D48.25` |
| Bed PID | **Kp 44.56 · Ki 7.25 · Kd 182.52** | `M304 P44.56 I7.25 D182.52` |
| E-steps | **440.87 steps/mm** | `M92 E440.87` |
| (then save) | | `M500` |

- An **EEPROM reset was performed this session before** the above values were measured,
  so they reflect a clean baseline.
- Compare these against the **firmware compile-time defaults** in
  [`configuration-diff.md`](configuration-diff.md) — if a reflash changes the defaults,
  these EEPROM values must be re-applied or re-tuned.

## Host connection (Windows)
- Port **COM3**, baud **250000**, **CH340** USB driver.
- The **TFT shares one serial bus with USB** on the MKS Gen L v1.0. The TFT's built-in
  **"Disconnect"** mode (Menu → Settings → Connection) must be active for clean
  Pronterface/host access. This is a **known hardware limitation** of this board, not a
  bug. Confirmed working live this session.

## To capture on the next session (not yet recorded)
- Full `M503` dump (steps/accel/jerk/feedrate, Z-probe offset once BLTouch is in).
- XY steps/mm, acceleration & jerk/junction-deviation values.
- Filament-sensor and any input-shaping values if enabled.
