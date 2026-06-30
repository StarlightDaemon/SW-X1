# Printed parts

| Folder | File present | Source | Print notes |
|---|---|---|---|
| `waggster-bltouch-mount/` | `SWX1-BLT-Bracket.stl` (collected, from digant73 repo) | see `waggster-bltouch-mount/SOURCE.md` | **PETG recommended** — mount sits near the hotend; PLA can deform. |
| `fan-duct-remix/` | *link only* (host blocks auto-download) | see `fan-duct-remix/SOURCE.md` | Contingency duct if the mount fouls the stock part-cooling duct. |
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
