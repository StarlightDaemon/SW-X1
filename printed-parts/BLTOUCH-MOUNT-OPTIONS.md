# BLTouch mount + airflow — options, recommendation & print-shop spec

Goal: a BLTouch mount for the Waggster (Z-max) setup that **also handles part-cooling
airflow** (the bare Waggster bracket fouls / displaces the stock duct), to be **printed by
a print service** in PETG.

Researched + verified 2026-06-30 (multi-source workflow, then **hands-on Thingiverse
verification via the logged-in browser**). The browser pass corrected a bad lead:
**Thingiverse `thing:3972011` does NOT exist** ("removed or never existed") — it was a
hallucinated ID in the web research. Real, confirmed options below.

## The two real approaches
**A) One integrated part (simplest for a shop):**
**blinkybill — "Artillery Sidewinder/Genius Waggster Mod BL Touch Mount With Fan Duct"**
- Thingiverse **thing:4606514** — https://www.thingiverse.com/thing:4606514/files
- Contains 3 STLs (verified in the file list): **`Fan_Mount_With_Duct_And_BL-Touch.stl`**
  (the integrated mount + duct + BLTouch holder — *print this one*),
  `BLTouch_Mount_Slider.stl`, `Fan_Mount_With_Duct_No_BL-Touch.stl`.
- Waggster family, fits genuine BLTouch. One print, no separate duct. Cooling is a
  single-sided/near-stock wrap (adequate, not maximal).

**B) Best cooling, two parts (mount + a corrected duct):**
- **Mount:** the Waggster bracket — either the one already vendored here
  (`waggster-bltouch-mount/SWX1-BLT-Bracket.stl`, from digant73) or the canonical
  original (Thingiverse **thing:3716043**). *(Note: this bracket geometry is
  wiring-agnostic — the Z-MAX/Waggster distinction lives in the firmware + cabling, not
  the printed part. Verified: byte-identical to the z-min bracket.)*
- **Duct (pick one):**
  - **alguiens "Futuristic fan duct for BLTouch mount"** — Thingiverse **thing:4368198**
    (~251 makes, 5-star). Long reach *to the nozzle tip* → best actual tip cooling, but
    single-sided.
  - **alguiens "~180 just fan duct"** — Thingiverse **thing:4360786** (~47). Keeps
    bilateral ~180° coverage.
  - Both alguiens ducts are **CC BY-NC** → flag for a *commercial* print shop.

**Also exists (not on Thingiverse):** 3DPrintBeginner's combined ~180° mount+duct on
Printables (model 21261) / MakerWorld (23049) — the web research's original #1. Good ~180°
cooling but **login-walled** (you have Thingiverse, not necessarily Printables) and carries
a documented "duct sits a few mm too far from the nozzle" complaint (the very reason the
alguiens ducts exist). Left off the primary path because it's harder to obtain and no
better than B for cooling.

## Recommendation
- **Want one part the shop just prints →** A: blinkybill `Fan_Mount_With_Duct_And_BL-Touch.stl` (thing:4606514).
- **Want the best cooling for a warped bed →** B: Waggster mount (already in-repo) + alguiens **Futuristic** duct (thing:4368198).

Both are Thingiverse-obtainable with your login. For a *warped* bed where first-layer/
overhang cooling matters, **B with the Futuristic duct is the stronger pick**; A is the
lower-effort single print. Decide duct-to-nozzle adequacy after install (see caveats).

## Print-shop spec (hand this over)
- **Material: PETG (mandatory).** The part sits next to the hotend; PLA will creep/deform.
- **Layer height:** 0.15 mm (0.2 mm acceptable for speed).
- **Infill:** ~40–50% (structural mount near heat; don't go below ~30%).
- **Supports:** **the integrated combined part (A / the 3DPrintBeginner part) REQUIRES supports.**
  The standalone alguiens ducts and the flat Waggster/digant73 bracket are essentially
  support-free. Tell the shop which file they're printing.
- **Orientation:** carriage/mounting face flat to the bed; duct outlet supported (as the
  model loads). Ducts print on their flat/back face.
- **Quantity:** 1 (print 1–2 spare ducts if you expect eventual heat-creep on a near-hotend duct).
- **Fit/scale:** designed for a **genuine ANTCLABS BLTouch v3.1** push-fit boss — **do NOT
  scale.** If a clone is ever used, print ONE test unit and confirm the boss fully seats.
- **STEP/CAD:** none available for any of these (mesh/Tinkercad origin) — STL only.

## Verify-on-install caveats (only settleable on the real printer)
- **Duct-to-nozzle distance / tip cooling** — the recurring family complaint. If cooling is
  weak after mounting, keep the mount and swap to the alguiens Futuristic duct (thing:4368198).
- **BLTouch boss seat** — do one test print before any batch (especially with a clone probe).
- **Probe offset** — the Waggster-family firmware value is `{28,-33,0}`; the exact Z-offset
  must be calibrated on the assembled machine (`M851` + babystep). A *different* mount
  (e.g. SpankyToot X+60/Y+18) needs a different offset.
- **Carriage variant** — confirm standard vs large-display X1 carriage before printing if a
  design ships two variants.
- **Stock 4020 fan** — these use the stock fan; only the (not-recommended) Dual-5015 designs
  need added blowers.

## Acquisition status
The integrated file (`Fan_Mount_With_Duct_And_BL-Touch.stl`, thing:4606514) was **triggered
to download to the operator's ~/Downloads** during verification. It could not be pulled
directly into this repo (macOS blocks tool access to ~/Downloads, and Thingiverse exposes
no anonymous file URL). To vendor it here, move the downloaded STL into
`printed-parts/waggster-bltouch-mount-duct/`; otherwise hand the file (or the thing:4606514
/ thing:4368198 links) straight to the print shop.
