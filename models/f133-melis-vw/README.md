# Model: F133 / MelisOS VW head unit  (stub — no device access yet)

A VW double-DIN aftermarket head unit on **AllWinner F133 + MelisOS** (see
[platform note](../../platforms/allwinner-f133/)). **Not Android** despite the look.

> ⚠️ **Stub.** No device access yet — everything below is read off one **System Info**
> photo. No firmware pulled, nothing verified. See [firmware-sources.md](firmware-sources.md).

## System Info (from the unit's screen)
| Field | Value | Notes |
|---|---|---|
| OS | `Melis-20220511` | AllWinner **MelisOS** (RTOS), 2022-05-11 build |
| APP | `F133-XFH-AKW-HY-G-OTA-2026082609…` | F133 SoC; `OTA` = update-package naming |
| MCU | `AB5301_20251128-1445` | separate MCU firmware |
| CAN | `H1H2VWFA3A-260526` | VW CAN box (`VWFA` = VW) |
| BT MAC | `41000000BD5C` | (looks like a placeholder/low-quality BT addr) |

## First RE targets (when a firmware/dump is available)
- Get the **OTA package** (`F133-…-OTA-…`) from the vendor/update channel → unpack it
  (MelisOS images: eGON/boot0 + the Melis rootfs; AllWinner `imagewty`/`PhoenixSuit` format is
  the usual container for AllWinner flashers — confirm).
- Look for a **UART console** on the board (AllWinner boot0/u-boot/Melis logs) — the usual way
  in on these, since there's no ADB.
- MCU serial + CAN box protocol (same structure as the other units — the box is a VW translator).
- Whether the Melis UI exposes any media/track data that could feed the VW cluster
  ([../../docs/vw-media-text.md](../../docs/vw-media-text.md)).
