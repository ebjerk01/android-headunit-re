# Platform: AllWinner F133 + MelisOS

A **low-cost** SoC from AllWinner's D1/F series. On these cheap car radios it runs **MelisOS**,
AllWinner's in-house RTOS — **not Android**, despite an Android-looking UI. Fast boot, media +
radio + BT, small flash. (F133 core details — e.g. the RISC-V C906 — to confirm once firmware
is in hand.)

Implications vs the MediaTek/Android units:
- No ADB, no Android `su` — RE is firmware/UART-first (a MelisOS image, not a classic Android stack).
- Update is a vendor **OTA package** flashed from USB/SD (filename pattern `F133-…-OTA-…`).
- The MCU + CAN box (here a VW `H1H2VWFA3A` box) are separate, same as the other platforms —
  the car-side CAN work (`../../docs/vw-media-text.md`) still applies.

First unit on this platform: **[models/f133-melis-vw](../../models/f133-melis-vw/)** (stub).
