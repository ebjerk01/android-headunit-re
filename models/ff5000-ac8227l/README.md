# Model: FF-5000 (AC8227L) — VW Passat CC

A **Radical R-C11VW1** 9" VW unit reflashed with **Junsun "TS" firmware** (`com.ts.MainUI`),
SoC **AutoChips AC8227L = MediaTek MT8127**, **Android 8.1** labelled "11". The deepest-RE'd
unit here. Platform: [mt8127-ac8227l](../../platforms/mt8127-ac8227l/).

| File | What |
|---|---|
| [ROOT.md](ROOT.md) | The `su` magic-string backdoor + the whitelist scheme (headline finding) |
| [car-data.md](car-data.md) | How car data reaches Android; exported `CarInfoService`; factory CAN log |
| [can-protocol.md](can-protocol.md) | MCU serial frame format + the two native libs |
| [can-java-model.md](can-java-model.md) | Java CAN model (`com.lgb.canmodule`), accessor surface, raw-CAN logger |
| [boot-and-package.md](boot-and-package.md) | `88xj` package format + lk USB updater (trigger + write-protect findings) |
| [flash-magisk.md](flash-magisk.md) | Magisk-on-boot — ⛔ NOT confirmed working (both no-cable routes failed) |
| [carclient.md](carclient.md) | `com.ts.car.client` hidden MQTT telemetry + remote-management agent |
| [juncan-txz.md](juncan-txz.md) | `com.txznet.trace` second cloud agent (voice/telematics) |
| [app-triage.md](app-triage.md) | Security triage of the remaining vendor apps |
| [re-targets.md](re-targets.md) | Backlog of components worth decompiling |
| [tools/](tools/) | `pkg.bin` RC4, `88xj` (un)packer, boot-pull / boot-only package builder (experimental) |

VW cluster CAN (shared across VW units): [../../docs/vw-media-text.md](../../docs/vw-media-text.md).
