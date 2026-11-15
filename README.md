# Android car head-unit reverse engineering

Reverse engineering of aftermarket car head units — bootloaders, firmware/update formats, root,
the MCU/CAN link to the car, vendor telemetry, and flashing. Scalable multi-model layout: new
units slot in under `models/`, with shared SoC notes under `platforms/`.

> Redacted public mirror. No raw device dumps or vendor binaries here.

## Layout
- **`models/<model>/`** — one unit each.
- **`platforms/<soc>/`** — shared SoC/OS notes reused across models on that chip.
- **`docs/`** — cross-model topics (e.g. VW cluster CAN).

## Models
| Model | SoC / OS | Status |
|---|---|---|
| [ff5000-ac8227l](models/ff5000-ac8227l/) | MediaTek MT8127 / **AC8227L**, Android 8.1 | **deep** — rooted (su backdoor), CAN decoded, telemetry mapped, flash routes tested |
| [f133-melis-vw](models/f133-melis-vw/) | **AllWinner F133** / **MelisOS** (not Android) | **stub** — photo only, no device access |
| [qcm6125-candidate](models/qcm6125-candidate/) | Qualcomm **QCM6125**, Android 13 | reference / stub |

## Platforms
| Platform | Notes |
|---|---|
| [mt8127-ac8227l](platforms/mt8127-ac8227l/) | MediaTek MT8127 (= AutoChips AC8227L); lk `88xj` USB updater, BROM, TS/Junsun firmware family |
| [allwinner-f133](platforms/allwinner-f133/) | AllWinner F133 + MelisOS — the ultra-cheap "looks-like-Android-but-isn't" class |
| [qcm6125](platforms/qcm6125/) | Qualcomm QCM6125 (Snapdragon), Android 13, Microntek/HCT stack |

*Everything here is for a unit the author owns, for interoperability and security research. Flashing can brick a device — at your own risk.*
