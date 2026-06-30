# TFT firmware (MKS TFT28 V4.0)

Two TFT firmware options are collected here. They are **different firmware families** —
read before flashing.

## 1. `dgtaheno-SD-TFT-update.zip` — the matching companion (use this one to stay in sync)
This is the TFT firmware that **pairs with the dgtaheno Marlin build currently on the
printer** (MKS TFT28 V4.0.27.x era). It is the in-sync option: flash via SD card (copy
contents to SD root, power on, wait for the flash, reboot). MD5 `2efd1b209a9621e6e5b2325a192f87fb`.

## 2. `mks-tft28-1.27.x-patch9.5/` — community BTT-based firmware (different UI; optional)
From the peterrakolcza bundle: a **BIGTREETECH-TouchScreenFirmware**-based build
(`MKSTFT28.bin` + `config.ini` + `TFT28/` assets + language packs). MD5 of the bin
`49d9726547ffe74c7bdfa38ca11b03d1`.

- This is the **1.27.x** BTT TFT firmware line — a **different family** from the stock
  MKS TFT firmware, with full **BLTouch / mesh-editor / Marlin-mode** menus (note the
  `bltouch*.bmp`, `mesh_editor.bmp`, `marlin_mode.bmp` icons in `TFT28/bmp/`).
- Potentially useful **after** the BLTouch is installed (on-screen probe deploy/test,
  mesh tuning), **but flashing it replaces the entire TFT UI** the printer ships with.
  Treat as an optional, deliberate upgrade — not a drop-in patch. Test with the printer
  in a known state and keep option 1 on hand to revert.

> The printer currently runs the dgtaheno companion TFT firmware (per the handoff:
> MKS TFT28 V4.0.27.x, Jan-2022 era). Don't reflash the TFT casually — the TFT shares the
> serial bus with USB, so a half-flashed TFT can complicate host access. See
> [`../docs/calibration-baseline.md`](../docs/calibration-baseline.md) for the
> Disconnect-mode requirement.
