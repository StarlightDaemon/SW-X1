# Firmware map & binary fingerprints

> Analytical conclusions (which build to flash, the X1-BLT probe-pin problem, grid sizes)
> live in [`../docs/verification-log.md`](../docs/verification-log.md),
> [`../docs/configuration-diff.md`](../docs/configuration-diff.md) and
> [`../docs/open-questions.md`](../docs/open-questions.md). This file is the inventory +
> fingerprints only.

## Folders
| Folder | What it is | Hardware fit |
|---|---|---|
| `running--dgtaheno-X1-nonBLT/` | **Exactly what is on the printer now.** `Marlin 2.1.2.1-X1.hex` + its config headers. No leveling, no probe (confirmed by `M115` on hardware). | X1 (300×300×400) ✓ |
| `target--dgtaheno-X1-BLT/` | The BLTouch build the user planned to flash. `Marlin 2.1.2.1-X1-BLT.hex` + config headers. **See verification-log: its probe-pin config is NOT cleanly Waggster-compatible as shipped.** | X1 ✓ |
| `reference--dgtaheno-Genius-BLT/` | `_BLT` + `_BLT-295C` (Genius line). The `_BLT` config has the *correct* Z-max/Waggster probe-pin setup — but **Genius** bed geometry, not X1. | Genius (220×220) ✗ for X1 |
| `reference--digant73-2.0.9.1-waggster-zmax/` | **Canonical correct Waggster config** (full source: Configuration.h/_adv.h, Version.h, pins_RAMPS.h) + firmware.hex + readme/changes + cabling PDF. Older Marlin (2.0.9.1). | X1 ✓ |
| `reference--digant73-2.0.9.1-mbl/` | Manual mesh-bed-leveling reference config + hex. | X1 ✓ |
| `reference--peterrakolcza-2.1.2/` | 2.1.2-era **binaries only** (waggster z-max, standard z-min, mbl, standard) + lineage note. | X1 ✓ |
| `reference--artillery3d-stock/` | Manufacturer stock Configuration + pins_RAMPS. | X1 ✓ |

## Binary fingerprints (MD5)
| File | MD5 | Bytes |
|---|---|---|
| running `Marlin 2.1.2.1-X1.hex` | `03a6b3ab03f902d3f291fe2632c2f625` | 282 356 |
| target `Marlin 2.1.2.1-X1-BLT.hex` | `01682ba04e424cc7f248516d22171a32` | 326 072 |
| Genius `Marlin 2.1.2.1_BLT.hex` | `e15b19272bf80974d0ac603637631600` | 364 019 |
| Genius `Marlin 2.1.2.1_BLT-295C.hex` | `48e8630f788ac133769806b1f54a3b34` | 326 068 |
| digant73 waggster `firmware.hex` | `9ccdb52de85ebf6e9297d280aca5eb6c` | — |
| digant73 mbl `firmware.hex` | `232ce9bd4907167b71dd637137477f5c` | — |
| peterrakolcza 2.1.2 waggster_z_max | `f7a74392f91e9a3f5c60a4a1d16dd173` | — |
| peterrakolcza 2.1.2 standard_z_min | `342778293ce3ebde0cedbed781d19552` | — |
| peterrakolcza 2.1.2 mbl | `bd0324371ac9980e0f3f2b50b05c3d32` | — |
| peterrakolcza 2.1.2 standard | `fcce3111a245d0f035954702435c401a` | — |

## The "corrections pending validation" question (binary evidence)
In the dgtaheno repo root, the Genius-line build `Marlin 2.1.2.1_BLT.hex` and
`Marlin 2.1.2.1_BLT_corrections_pending on validation.hex` are **byte-identical**
(both MD5 `e15b19272bf80974d0ac603637631600`). So the "corrections pending" file is a
**relabeled copy** of `_BLT.hex`, not a distinct build. A separate dated artifact,
`..._corrections_pending on validation_180424.hex` (310 183 B), **is** a different,
earlier binary. None of these are in the **X1** family — `-X1-BLT.hex` (326 072 B) is a
separate, smaller binary. (Those two `corrections_pending` hexes are preserved only inside
the dgtaheno bundle in `../archive/upstream-bundles/`; they have no published source.)
