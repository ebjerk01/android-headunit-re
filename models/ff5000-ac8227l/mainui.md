# MainUI — the vendor UI app (FF-5000 / AC8227L)

Notes on `com.ts.MainUI`, the app that *is* the infotainment system on this unit's Junsun/TS
firmware. Written from static analysis of the APK pulled from the author's own unit (apktool for
the manifest and resources, jadx for the Dalvik code). Nothing here was run on the car, and no
vendor code, resources or binaries are reproduced: this page describes structure and behaviour
in our own words, with identifiers named only where you need them to interoperate.

Related: [can-protocol.md](can-protocol.md) (MCU serial framing, CAN decoder),
[can-java-model.md](can-java-model.md), [car-data.md](car-data.md), [ROOT.md](ROOT.md),
[ACCESS-CODES.md](ACCESS-CODES.md).

## 1. What kind of app it is

| | |
|---|---|
| Package | `com.ts.MainUI`, version 1.1 |
| Android | API 27 (8.1) — min, target and compile |
| Privilege | runs as the **system UID**, platform-signed |
| Build | ships **debuggable** |
| Native code | 32-bit ARM only |
| Size | ~620 activities, 17 services, 2 receivers, no providers |

The key fact: MainUI is not a client of a vendor HAL. It is a privileged system app that talks
to the hardware itself, in-process, over JNI. There is no separate vendor service to emulate.
Replacing it means either re-implementing the MCU serial link plus a system app, or keeping the
vendor native library (section 3).

It holds system-grade permissions (secure settings, mounting, system windows, contacts and call
log, camera, Bluetooth). It declares no `INTERNET` permission, though as the system UID it can
still reach the network.

## 2. Components

**Boot.** A boot receiver (also triggered by the vendor quick-boot power on/off broadcasts)
starts a main service, which in turn starts the rest of the stack. A second receiver handles
Bluetooth hands-free call events.

**Services.** A central singleton service that owns the hardware objects; the boot orchestrator;
the exported car-info service (see [car-data.md](car-data.md)); the raw CAN-box log window;
reverse camera; navigation/status bar; Bluetooth; a vendor settings bridge; a voice assistant
with car, Bluetooth, music, radio and function sub-services; and the TXZ telematics SDK
([juncan-txz.md](juncan-txz.md)).

**Exposure.** Most of these services are exported without a permission, so any installed app
can bind them. Through the car-info service an unprivileged app can read the VIN and send
commands to the CAN box — consistent with the earlier finding in [car-data.md](car-data.md).

**Activities.** About 90 % are per-vehicle car-settings and car-info screens (one family per
make: VW/Golf, Audi, BMW, Mercedes, GM, Honda, Ford, PSA, Chrysler, Toyota, …). The rest are the
main surfaces (navigation, media sources, AV-in, radio, TV, DVR, front/rear/360 cameras, phone,
steering-wheel learning, touch calibration), the settings pages (sound, display, Bluetooth, GPS,
wheel keys, DSP EQ/balance/time alignment, …) and the factory/engineering screens.

## 3. Hardware API — the seam a custom OS can keep

All hardware goes through a small set of Java classes in `com.yyw.ts70xhw`, each backed by the
native library `libts50xhw.so`. The names are descriptive, and together they form a complete
hardware abstraction:

| Class | Covers |
|---|---|
| `Mcu` | CAN messages to/from the CAN box; panel, rotary, steering-wheel and encoder keys; power state, backlight, illumination, reverse and brake inputs; G-sensor; beep, clock and display OSD; MCU identity; **MCU firmware update** |
| `Radio` | tuner bands, seek/scan/presets, frequency, full RDS (PS, PTY, AF, TA) |
| `Iop` | volume, balance/fader/tone, a parametric DSP (EQ bands, filters, per-seat delay, reverb, loudness, bass), DSP version and **DSP firmware update**, FM transmitter |
| `FtSet` / `StSet` | factory calibration and option flags (semantics live in native code) |
| `KeyDef` | numeric ids for every key action (home, apps, audio, CarPlay, car info, navi, mode, mute, EQ, volume, media transport, gesture keys, encoder keys) |

Other native libraries: `libcan50.so` (per-vehicle CAN decoder, see
[can-protocol.md](can-protocol.md)), a pinyin name matcher, a launcher icon-config loader, and
MediaTek picture-quality (MiraVision) from the system image.

The serial ports are opened inside the native library, not from Java: MCU/CAN on two of the
`ttyMT` ports (held exclusively by MainUI), the in-dash DSP on another.

**Steering-wheel keys** do not arrive as Linux input events. They come over the MCU link as a
CAN sub-command and are read through `Mcu`. The learning screen pairs each wheel button with a
`KeyDef` action, so that id table plus the learn/delete calls are all a replacement UI needs.

**Radio text and RDS** come from the tuner through `Radio`, not from CAN.

## 4. Factory and engineering codes

Codes are typed into Settings → Car/Equipment. One dispatcher in MainUI handles all of them. The
values come from string resources, so other firmware builds can rebrand them; this is the set in
this build.

| Code | Effect |
|---|---|
| 8888 | Factory settings menu |
| 7788 | MediaTek EngineerMode |
| 8877 | AutoChips (ATC) settings app |
| 5678 | Boot-logo menu (only if the logo option is enabled) |
| 0129 | Raw head-unit ↔ CAN-box traffic log window |
| 0128 | CAN-box update screen |
| 3699 / 5699 | CAN-box update through the separate CAN-update app (two vendor variants) |
| 12345678 | Same CAN-update app, with an MCU key first |
| 1515 | Factory self-test |
| 5555 | Mass-production mode screen |
| 2134 | App-info window |
| 1414 | Reboot |
| 1313 | Radio reset |
| 1101 | Toggle screenshot mode |
| 1616 / 1717 | Overlay window / play audio test buffer |
| 1234 / 4321 | Copy / delete the demo file |
| 6666 / 7777 | Install APKs from the vendor OEM folder |
| 002446 | Install the Play Store as a privileged app (see below) |
| 9090 / 9191 | Clear the activation id (9191 asks first) |
| 1111 | Start the phone-state service |
| 7758258 | Key test |
| 860 / 120 | Disc update check / HMI update check |
| 123 / 160 | Show DSP version / branch version |
| 140 / 141 | GPS off / on |
| single digits, 22 | MCU update, wheel learning, IDs/IMEI, power reset, VCOM, random BT name, navi whitelist reset, activation window, product id |

**002446 matters for security.** MainUI uses the vendor `su` it is whitelisted for
([ROOT.md](ROOT.md)) to make `/system` writable, copy an app into the privileged-app folder, and
reboot. That is a built-in way to modify the system partition without flashing.

The 8888 menu holds radio region, CAN type, MCU update, boot logo, key learning, language,
screenshot mode and "Erase all content". **MCU update flashes the MCU, not Android.**

## 5. Configuration surface

Behaviour is set by system properties and string resources, not by a config service:

- `ro.forfan.*` / `forfan.*` (Forfan platform): screen and touch size, device model, serial,
  IMEI, version, feature flags (EQ, install lockdown, media scanner, RAM size), runtime flags
  shared between MainUI, the launcher and the reverse camera.
- `persist.atc.*` / `ro.atc.*` (AutoChips): Bluetooth behaviour, Android Go build flag.
- `custom_*` string resources: screen size, auto-activation, hide EQ, forbid installs, front
  camera, mirroring, the vendor code.
- A VIN property from another board vendor (Hazens) is also read: the same build carries decode
  paths for several OEM boards.

## 6. Activation gate

A launcher activity checks for an activation licence. It can activate over the network or
through a USB channel, and binds the licence to the unit's Bluetooth address and MCU id. The
registration servers are Forfan's (same vendor as [carclient.md](carclient.md)). The gate only
exists in the app: it does not gate boot or the MCU link, so a custom OS that does not ship
MainUI never meets it. We did not analyse the licence format.

## 7. What this means for a custom OS

1. **Keep the native seam.** A new launcher can load `libts50xhw.so` and use the `Mcu`, `Radio`
   and `Iop` classes directly (power, backlight, reverse, wheel keys, radio, EQ) without
   re-implementing the serial framing. This is the fastest route to a working custom UI.
2. **Or go fully independent.** The MCU frame and the CAN sub-commands are documented in
   [can-protocol.md](can-protocol.md), and there is no HAL to emulate, so a non-Android OS can
   drive the MCU port itself.
3. **Car data without root:** bind the exported car-info service (VIN, speed, doors, climate).
   Richer VW data is only inside MainUI's process (root, a modified MainUI, or the 0129 log).
4. **No-flash system changes** are possible through the whitelisted `su` (code 002446 shows the
   pattern), e.g. to stage a custom launcher without touching `boot.img`.
5. **Config is data:** the properties, `custom_*` resources and the CAN profile file are the
   whole customisation layer; a custom build can read or override them.
6. **Leave MCU and DSP firmware alone:** they update independently of Android, and a custom OS
   does not need to touch them.
7. **Wheel learning is reproducible** from the `KeyDef` table and the learn/delete calls.

## 8. Confidence

**Read directly:** the Java layer is not obfuscated, so the component map, permissions, system
UID, the code dispatch and its values, the native method surface, property names and the boot
and activation flows are high confidence.

**Not resolved:** what each native method does inside `libts50xhw.so` / `libcan50.so`; the exact
UART index and baud and the MCU command bytes for beep, clock and display; the meaning of the
factory calibration setters; the licence format. Live behaviour quoted here comes from earlier
sessions on the unit, as cited in the linked pages.
