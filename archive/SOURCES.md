# Upstream Sources — provenance & restore

All four upstream repositories were cloned **2026-06-30** as full `git clone --mirror`
clones and archived as git **bundles** under [`upstream-bundles/`](upstream-bundles/).
Each bundle contains **every branch, tag, and commit** of its upstream at clone time,
so this workspace stays complete and buildable even if an upstream repo is later
deleted, rewritten, or taken private. This is the upstream-independence guarantee for
the whole project.

| Local name | Upstream URL | Default branch | HEAD @ clone | Role in this workspace |
|---|---|---|---|---|
| dgtaheno-X1-marlin | https://github.com/dgtaheno/artillery-Genius-Genius_Pro-X1-Marlinfw | `Marlin-2.1.2.1` | `3d8709e7b438b949ae31ea842f0256367ba67599` | **Primary** — the currently-running firmware lineage (config headers + compiled .hex per variant) |
| digant73-releases | https://github.com/digant73/Artillery-Sidewinder-X1_releases_main_board | `master` | `b2574f48741b501ab93f956fdc40f4f83129a43a` | **Canonical pin-mapping reference** — full Marlin-2.0.x source for explicitly-labeled `waggster_mod_(z_max_used)` vs `standard_mod_(z_min_used)` vs `mbl`, plus the Waggster cabling PDF and a BLTouch bracket STL |
| peterrakolcza-upgrades | https://github.com/peterrakolcza/Artillery_Sidewinder_X1_Upgrades | `main` | `6848ac97ada88e754911ea27356bfe82c5befc3b` | 2.1.2-era waggster / z-min / mbl firmware **binaries** + community TFT firmware |
| artillery3d-stock | https://github.com/artillery3d/sidewinder-x1-firmware | `master` | `69c18e5bc7d8a8959120cfced1a256729702ddc7` | Manufacturer **stock reference** (pins, bed-size constants) |

### Correction to the handoff
The handoff described `digant73` as "an older, separate repo **by the same author**" as
`dgtaheno`. These are **different GitHub accounts** (`dgtaheno` vs `digant73`). The
digant73 firmware carries inline `// DIGA-Tech:` author comments. They are a closely
related but **separate** lineage — treated as independent here, not assumed identical.

## Restoring a full upstream from a bundle
```sh
git clone archive/upstream-bundles/dgtaheno-X1-marlin.bundle restored-dgtaheno
cd restored-dgtaheno && git branch -a       # all original branches/tags are present
```
Integrity can be re-verified at any time:
```sh
git bundle verify archive/upstream-bundles/dgtaheno-X1-marlin.bundle
```
Verified OK at archive time — the dgtaheno bundle restores all 10 refs, including
branches `Marlin-2.0.9.3`, `Marlin-2.1.2`, `Marlin-2.1.2.1`, `Marlin-2.1.2.5`.

Bundle sizes: dgtaheno ≈ 3.4 MB · digant73 ≈ 38 MB · peterrakolcza ≈ 50 MB · artillery3d ≈ 1 MB.

## Notes on upstream contents
- **dgtaheno** ships only the per-variant `Configuration.h` / `Configuration_adv.h`
  headers (under `src/`) plus precompiled `.hex` — **not** a full Marlin source tree.
  To rebuild, drop these headers into an upstream Marlin 2.1.2.1 checkout.
- **peterrakolcza**'s 2.1.2 firmware archives are **binary-only** (`firmware.hex`); no
  source `Configuration.h` is included. Its lineage note (`changes.txt`) states the FW is
  based on official Marlin 2.0.6.1 and modifies `pins_RAMPS.h`, `Configuration.h`,
  `Configuration_adv.h`, `Version.h`.
- **digant73** is the only source-bearing BLTouch reference (full Marlin-2.0.x trees).
