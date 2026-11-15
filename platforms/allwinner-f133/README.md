# Platform: AllWinner F133 / D1s + MelisOS

A **low-cost** SoC: AllWinner **F133/D1s**, a **T-Head XuanTie C906 (RISC-V)** core, running
**MelisOS** (AllWinner's in-house RTOS) — **not Android**, despite the Android-looking UI. Fast
boot, media + radio + BT, small flash. The ultra-cheap "looks-like-Android-but-isn't" class.

Implications vs the MediaTek/Android units: no ADB / no Android `su`; RE is firmware/FEL-first;
MCU + CAN box are separate (same as the other platforms — the car-side CAN work in
`../../docs/vw-media-text.md` still applies).

## Storage & firmware layout (from community RE of F133/Melis boards)
- **Flash:** usually **SPI NOR** (e.g. 16 MB, chips like XM25QH128C) on the cheap boards; F133 can
  also boot SPI-NAND/SD/eMMC. SPI NOR erase is **4 KiB aligned**.
- **Layout:** `boot0.bin` (eGON.BT0 SPL) → GPT → **TOC1** boot package (`melis-config.bin`,
  `melis-lzma.bin` = LZMA kernel, `epos.img`) → **ROOTFS = MinFS** → **UDISK = FAT16**.
- **Boot chain:** BootROM → boot0 → GPT/TOC1 → LZMA-decompress kernel @`0x40000000` → OpenSBI →
  Melis kernel → mount ROOTFS + UDISK.
- **Update image:** AllWinner **IMAGEWTY** `.img` (vendor names it e.g. `LTTF133.img`).

## How to GET the firmware (since vendor downloads are board-specific/absent)
1. **Dump it off the device via FEL** (recommended, non-destructive): write `sd_fel_switcher.bin`
   to an SD card (`dd ... seek=8`), boot from it → the BootROM enters **FEL** (low-level USB
   bootloader) when no valid boot media is found → USB-A-to-A → `xfel_spinor_dump.sh` →
   `nor_dump.bin`. Recover with `xfel_spinor_recovery.sh nor_dump.bin`. **Keep the original dump.**
2. **SPI clip + programmer** (NeoProgrammer) directly on the NOR chip — the other dump route.
3. **Vendor update package** on FAT32 SD/USB at `\Update\LTTF133.img` (filename from `appUpdateFile`
   in the UDISK `Config.ini`); also `\Update\LTTMcu.bin` (MCU fw), `BtHeadset.bin`, `Config.ini`,
   `stalogo.jpg`. Trigger: insert the media, or factory code **`000000`**. `update.mod` checks +
   flashes via ioctls (check=7, flash=2, progress=4) then resets.

## Toolchain (open-source, reusable)
- **Gamadril/d1s-melis-tools** — dump/unpack/repack: `dump_tool extract|pack`, `img_tool from-dump`
  (→ `LTTF133.img`) / `pack` (IMAGEWTY), `compile_fex`, xfel SPI-NOR scripts. The core kit.
- **ChrisHughes/allwinner-image-tool** — modify AllWinner firmware images.
- **Vabian124/chipdump** — SK-8168 F133-B SPI dump/unpack/flash (NeoProgrammer, XM25QH128C).
- **skyfortress/ekiy-melis-re** — EKIY SK-8168: emulator + Melis UI-format tools + docs.
- **DiegoSagredo911/F133_BootLogo_Customizer**, **fuho/moto-display-re** — boot-logo/theme + a
  multi-session F133-B/Melis 4.0 RE writeup (PAIRIP defeat, cloud RE).

First unit on this platform: **[../../models/f133-melis-vw](../../models/f133-melis-vw/)**.
