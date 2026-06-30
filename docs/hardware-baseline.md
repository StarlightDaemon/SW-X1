# Hardware baseline — Artillery Sidewinder X1 (Gen 1, Rev 1)

Canonical hardware facts for this specific unit. **Do not re-derive or re-ask these.**

| Subsystem | Detail |
|---|---|
| Printer | Artillery Sidewinder X1, **Gen 1 Rev 1** (original US-launch hardware). Build volume **300 × 300 × 400 mm**. |
| Mainboard | **MKS Gen L v1.0**, ATmega2560 (8-bit AVR). |
| Stepper drivers | **TMC2100, standalone** (no UART). StealthChop on the extruder conflicts with LIN_ADVANCE — relevant when enabling features. |
| Extruder / hotend | Direct-drive **Titan Aero clone**, **Volcano-style** hotend, **PTFE-lined heatbreak → hard temperature cap ≈ 240 °C**. Extruder idler is plastic; an aluminium replacement is a cheap future swap (**not yet done**). |
| Bed | 300 × 300 mm **glass**, **AC-mains heater via SSR**. Reported **warped** — see warning below. |
| TFT | **MKS TFT28 V4.0**. |
| Z axis | Lead screw shows **stick-slip** (jump-then-smooth on first move after dormancy). Flagged for **lubrication** on the next maintenance pass — not yet addressed. |

## ⚠️ Bed-warp shape is UNMEASURED on this unit
The bed is *reported* warped, but the **specific shape/location has not been measured on
this physical bed.** An earlier AI-generated research report asserted a specific
"**30–50 mm raised band inset from the perimeter**" geometry — this was **rejected
mid-project as unverified**: it was inferred from generic community reports about the X1
model, not measured here. **Do not treat it as fact.** The real warp shape is to be
determined empirically once a working mesh-leveling system is in place.

## Known deferred maintenance (not yet done)
- Z lead-screw lubrication (stick-slip).
- Plastic → aluminium extruder idler swap.
- Empirical bed-warp mapping (needs working mesh leveling first).

## Out of scope (explicitly deferred)
- Klipper / Raspberry Pi / 32-bit board migration (long-term option, not part of this work).
- CNC / manual-machining tooling.
- 3D-modeling workflow.
