# Model: QCM6125 candidate (reference)  — stub

A newer Qualcomm **QCM6125** (Snapdragon) VW head unit, **Android 13**, the Microntek/HCT
(`android.microntek`) vendor stack — the modern alternative to the MT8127/AC8227L units. See
[platform note](../../platforms/qcm6125/).

Reference RE used the closest public QCM6125 Android 13 build (a Dasaita G13 OTA): the HCT
MCU/CAN stack (`HCTManagerService` serial protocol, `HCTCanBus` AIDL surface) and an
exported, unguarded CAN service mirroring the AC8227L finding. Deep write-up is kept in the
private repo; this is a placeholder for when a real device/firmware is on hand.
