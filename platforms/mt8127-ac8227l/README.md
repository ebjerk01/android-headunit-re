# Platform: MediaTek MT8127 / AutoChips AC8227L

4× Cortex-A7 (armeabi-v7a), typically **Android 8.1** (API 27) on these TS/Junsun radios,
kernel 3.18.x, single-slot (no A/B). mtkclient hw code `0x8127`. AutoChips AC8227L = rebadged
MT8127.

**Security posture (as shipped on these units):** SELinux permissive, userdebug/test-keys,
`ro.debuggable=1`, ADB on 5555 with no auth, OEM-unlock allowed, no `vbmeta`.

**Flashing:** the `lk` bootloader flashes an unsigned `88xj` package (`8227L-8.bin`) from a FAT
USB stick (no signature check) — entered via a touch-panel/key trigger at boot; BROM/mtkclient
over a USB-A-to-A cable is the guaranteed route. Boot/recovery/lk/tee are platform
write-protected from a running Android (dd does not persist).

Full detail (lk decompile, the `88xj` format + tooling, the MCU/CAN serial protocol, root) is
in the only MT8127 model so far: **[models/ff5000-ac8227l](../../models/ff5000-ac8227l/)**.
