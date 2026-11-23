# Factory / engineering access codes — FF-5000 (AC8227L / TS-Junsun)

Entered in the stock **Settings** (Car / Infotainment settings) or a hidden input dialog.
**Confirmed** = verified live on this unit or found as a literal in its firmware (`MainUI.apk`).
**Community** = reported for 8227L / TS / Junsun units generally — varies by firmware; harmless to try.

| Code | Opens | Source |
|---|---|---|
| **8888** | **Factory Settings** menu (Radio, CAN Type, **Mcu Update**, Volume, Menu Icon, Touch Study, Display, ATV, Erase All Content, Key, Default Language, **Boot Logo**, MP mode, Screen Shot Mode) | **Confirmed (live)** |
| **7788** | MediaTek EngineerMode | **Confirmed** (firmware dispatch) |
| **8877** | AutoChips (ATC) settings app (earlier noted as the audio/volume window) | **Confirmed** (firmware dispatch) |
| **0129** | Raw head-unit ↔ CAN-box traffic log (`com.ts.can.CanDataLogService`, floating window) | **Confirmed** (service + firmware dispatch) |
| **0128** | CAN-box update entry — not the log | **Confirmed** (firmware dispatch) |
| **5678** | Boot-logo window (only if the logo option is enabled) | **Confirmed** (firmware dispatch) |
| 8878 | Factory-reset / "Erase all content" path (Settings → About → gear → 8878) | Community |
| 000000 | Factory code on some newer Junsun builds | Community |
| 5802 / 123456 / 7890 | Factory / reset on some units | Community |
| 26959910 | "Engineering mode" (needs a manufacturer account on some) | Community (low confidence) |

**Note:** "Mcu Update" in the 8888 menu flashes the **MCU**, not Android — don't use it for OS/root
(and it can't install Magisk). The factory menu itself is a Settings-app code; the per-feature
codes above (7788, 8877, 0128, 0129, 5678) are checked inside `MainUI`. The complete code
dispatch is in [mainui.md](mainui.md). Flash findings: [boot-and-package.md](boot-and-package.md);
car data and the CAN log: [car-data.md](car-data.md).
