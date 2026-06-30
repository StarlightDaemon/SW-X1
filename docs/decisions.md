# Decisions & rationale

Chronological record of decisions made for this printer, with the *why*. Newest first.

## 2026-06-30 — Firmware build for the Waggster BLTouch: don't flash `-X1-BLT` as-is
**Decision:** Do **not** flash dgtaheno `Marlin 2.1.2.1-X1-BLT.hex` for the Waggster mod
as shipped. Use one of:
1. **digant73 `waggster_z_max` `firmware.hex`** (correct out of the box), or
2. **dgtaheno `-X1-BLT` source with line 1279 commented out, recompiled** (if Marlin
   2.1.2.1 + exact X1 geometry is wanted).

**Why:** source inspection (verified by an independent agent panel) found `-X1-BLT`'s
probe pin is **internally contradictory** — `Z_MIN_PROBE_USES_Z_MIN_ENDSTOP_PIN` is left
active while `Z_MIN_PROBE_PIN 19` is also set, so Marlin reads the probe on **Z-MIN**, not
the **Z-MAX/pin-19** connector the Waggster mod wires to. As-shipped it would fail to see
the probe. digant73's `waggster_z_max` is the only config that is *both* correctly
Z-MAX-routed *and* X1-bed-sized. See [verification-log.md](verification-log.md) §B2 and
[configuration-diff.md](configuration-diff.md). **This is exactly the label-vs-reality
trap the project guards against — caught before flashing.**

**Tradeoffs:** option 1 is older Marlin (2.0.9.1, no input shaping — a plus on 8-bit) with
bed 305×305 and UBL 7×7; option 2 is current Marlin with exact 300×310 geometry and
BILINEAR 5×5 but requires a compile. Either way, `NOZZLE_TO_PROBE_OFFSET` must be
re-measured for the physical mount, and the probe must pass `M119`/`G29` on hardware.

**Do NOT use `_BLT-295C`** even though it raises the hotend cap to 295 °C: this printer's
PTFE-lined heatbreak caps at ~240 °C, so the extra headroom is a hazard, not a feature
(and that build also has the contradictory probe pin + Genius bed size).

## (Carried from handoff) — Add BLTouch via the Waggster mod
**Decision:** add a genuine BLTouch using the **Waggster mod** (reuses the extruder LED
ribbon path; maps probe → Z-MAX/pin 19), rather than self-compiling a manual MBL build.
**Why:** the running firmware has *no* leveling at all (verified, not assumed); a real
probe is more reliable than hand-MBL for a warped bed. Sacrifices the extruder LED.

## (Carried from handoff) — Sensor & cable sourcing
- **Genuine ANTCLABS BLTouch v3.1** from 3dprinteruniverse.com (official US reseller).
- **Avoid the "Artillery 3D ABL"** — ANTCLABS names it a counterfeit clone.
- **2 m** extension cable (buffer over 1 m given gantry travel / connector landing uncertainty).

## (Carried from handoff) — Mount material & duct contingency
- Print the Waggster mount in **PETG** (proximity to hotend; PLA can deform).
- Keep the **3DPrintBeginner improved fan duct** on standby in case the mount fouls the
  stock part-cooling duct. See [../printed-parts/](../printed-parts/).

## 2026-06-30 — Workspace strategy: vendored curation + full bundle archive
**Decision:** make this directory a standalone git repo. Vendor a *curated* set of the
relevant configs/binaries/docs for browsability, **and** archive complete `git bundle`
mirrors of all four upstreams under `archive/upstream-bundles/`.
**Why:** the mission requires survival independent of any upstream's future activity. The
bundles preserve every branch/tag/commit (verified restorable); the curated tree keeps the
day-to-day material readable. See [../archive/SOURCES.md](../archive/SOURCES.md).

## 2026-06-30 — Rejected: the "30–50 mm raised band" bed-warp geometry
**Decision:** treat the bed-warp shape as **unknown/unmeasured**.
**Why:** that specific geometry came from an AI report inferring generic community claims,
not a measurement of this bed. Map the real warp only once a working mesh exists.
