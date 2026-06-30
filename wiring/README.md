# Wiring — Waggster Mod (BLTouch on the Z-MAX / LED port)

**Primary document:** [`Waggster Mod - BLTouch v1.5.pdf`](Waggster%20Mod%20-%20BLTouch%20v1.5.pdf)
(collected from the digant73 repo; present in every `bltouch_waggster_mod_(z_max_used)/cabling/` folder).

## What the Waggster mod does (verified against digant73's labeled source)
Instead of running new wires through the gantry, the Waggster mod **reuses the existing
extruder LED ribbon cable path** to carry the BLTouch signal, and maps the BLTouch
**Z-probe signal to the Z-MAX endstop pin** — **pin 19** on RAMPS / MKS Gen L. This is
why digant73 labels the variant **`(z_max_used)`**. Trade-off: **the extruder LED light
is sacrificed.**

The firmware side of this mapping (which `#define`s must change to read the probe on
Z-MAX pin 19 rather than the default Z-MIN) is documented with exact line-level evidence
in [`../docs/configuration-diff.md`](../docs/configuration-diff.md) and
[`../docs/verification-log.md`](../docs/verification-log.md). **Wiring and firmware must
agree** — a Waggster-wired probe needs a Z-MAX-pin firmware build, or it will not be read.

## Sensor (already ordered)
- **Genuine ANTCLABS BLTouch v3.1** from 3dprinteruniverse.com (official US reseller).
- ⚠️ **Avoid** the "Artillery 3D ABL" sensor sold under the printer's own brand —
  ANTCLABS explicitly names it a **counterfeit clone**, not a genuine BLTouch.
- **2 m** extension cable purchased (buffer over the 1 m, given gantry travel and an
  uncertain connector landing point).

## Verify before trusting (hardware steps, not yet done)
After wiring + flashing a Z-MAX build:
1. `M119` — manually trigger/deploy the probe and confirm the **z_max** (or z_probe)
   endstop state changes. If nothing changes, the pin mapping is wrong.
2. `M115` — confirm `Cap:Z_PROBE:1` and `Cap:AUTOLEVEL:1`.
3. `G29` — must run (not "Unknown command").
