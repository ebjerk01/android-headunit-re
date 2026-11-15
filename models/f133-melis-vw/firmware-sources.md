# Firmware sources — F133 / MelisOS VW unit

> **Searched 2026-11-15: the exact firmware for this board is NOT publicly downloadable.**
> F133/Melis firmware is board-specific — there's no vendor archive, and forum threads confirm a
> mismatched `.img` bricks the unit. The realistic routes are: pull it off the device, or get the
> matching package from the seller. (We have only a System Info photo; no device access yet.)

- **OTA name seen on screen:** `F133-XFH-AKW-HY-G-OTA-2026082609…` (MCU `AB5301`, CAN box `H1H2VWFA3A`).
  No web hit for that exact string (vendor-specific).

## How to obtain it (when there's device access — this unit)
1. **Dump via FEL** (best, non-destructive): SD-card `sd_fel_switcher` → FEL → USB-A-to-A →
   `xfel_spinor_dump.sh` → `nor_dump.bin`. Keep the original; recover with `xfel_spinor_recovery.sh`.
   See [platform note](../../platforms/allwinner-f133/) for the full toolchain (Gamadril/d1s-melis-tools).
2. **SPI clip + programmer** on the NOR chip (NeoProgrammer) if FEL is awkward.
3. **Seller/support package**, or grab the in-unit **Online upgrade** file (QR+wifi) / the SD-card
   `\Update\LTTF133.img` mechanism (factory code `000000`). Note: `\Update\LTTMcu.bin` is the **MCU**
   image — matches the `AB5301` MCU on this unit (MCU update is separate from the main Melis fw).

## When obtained
Record exact URL/source + SHA-256 here; stage the binary off-repo (never commit raw firmware to the
public mirror); `dump_tool extract` it under this model dir and start the write-up.
