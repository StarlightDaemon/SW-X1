# Printed parts

> **BLTouch mount + airflow:** see **[BLTOUCH-MOUNT-OPTIONS.md](BLTOUCH-MOUNT-OPTIONS.md)**
> for the researched + browser-verified recommendation (integrated blinkybill part
> thing:4606514, or Waggster mount + alguiens Futuristic duct thing:4368198) and the
> print-shop spec. That doc supersedes the older bare-mount + separate-duct split below.

| Folder | File present | Source | Print notes |
|---|---|---|---|
| `waggster-bltouch-mount-duct/` | blinkybill integrated **mount+duct** STLs (thing:4606514) | **Option A** | ★ single-piece mount+duct+BLTouch. PETG. |
| `waggster-bltouch-mount/` | Waggster originals + `SWX1-BLT-Bracket.stl` (digant73) | thing:3716043 + digant73 | Mount-only (pair with a duct for Option B). PETG. |
| `fan-duct-remix/` | alguiens **Futuristic** + **~180** ducts (thing:4368198 / 4360786) | **Option B** duct | Best cooling; pair with a Waggster mount. ⚠ CC BY-NC. |
| `gantry-square/` | `Gantry Square.STL` | peterrakolcza repo | Gantry-squaring jig. |
| `brackets/` | `Brackets.zip` | peterrakolcza repo | Misc upgrade brackets. |

## On the BLTouch mount
The **original** source is Thingiverse **thing:3716043** ("Waggster Mod BLTouch
Artillery sidewinder X1") — the file the whole ecosystem traces back to. Thingiverse
blocks automated download, so the binary STL is **not** vendored from there directly.
Instead, `SWX1-BLT-Bracket.stl` — a Waggster BLTouch bracket collected from the
**digant73** firmware repo (it ships in every `bltouch_waggster_mod_(z_max_used)` folder)
— is provided as a verified, locally-owned equivalent. Confirm fit against the original
thing:3716043 photos before printing. See `waggster-bltouch-mount/SOURCE.md`.

> Known fit issue: multiple users report the Waggster mount can interfere with the stock
> part-cooling fan duct. If a test-fit shows a collision, print the improved fan duct in
> `fan-duct-remix/` (it is designed specifically not to foul the Waggster mount).
