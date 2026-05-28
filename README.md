# 💻 Hackintosh EFI — Dell Inspiron 3458
> OpenCore EFI for macOS Big Sur on Dell Inspiron 3458 (Broadwell)

![macOS](https://img.shields.io/badge/macOS-Big_Sur_11.x-white?style=flat-square&logo=apple)
![OpenCore](https://img.shields.io/badge/OpenCore-0.6.4-blue?style=flat-square)
![Status](https://img.shields.io/badge/Status-Working-brightgreen?style=flat-square)

---

## 🧰 Hardware Specifications

| Component | Details |
|-----------|---------|
| **Model** | Dell Inspiron 3458 |
| **CPU** | Intel Core i5-5005U (Broadwell, 2C/4T) |
| **GPU** | Intel HD Graphics 5500 — `AAPL,ig-platform-id: 06002616` |
| **RAM** | 8 GB DDR3L |
| **Storage** | SATA SSD 256 GB |
| **WiFi** | Intel OEM — AirportItlwm.kext |
| **Bluetooth** | Intel OEM — IntelBluetoothFirmware + IntelBTPatcher |
| **LAN** | Realtek RTL8100 — RealtekRTL8100.kext |
| **Audio** | AppleALC — layout-id: 17 |
| **SMBIOS** | `MacBookPro12,1` |

---

## ✅ What's Working

| Feature | Status |
|---------|--------|
| CPU Power Management (NullCPUPowerManagement) | ✅ Working |
| Intel HD 5500 Graphics Acceleration | ✅ Working |
| Audio (AppleALC layout-id 17) | ✅ Working |
| WiFi (Intel — AirportItlwm) | ✅ Working |
| Bluetooth (IntelBluetoothFirmware) | ✅ Working |
| LAN (RealtekRTL8100) | ✅ Working |
| Sleep / Wake | ✅ Working |
| USB Ports (UTBMap) | ✅ Working |
| Trackpad (VoodooI2CHID) | ✅ Working |
| Keyboard (VoodooPS2) | ✅ Working |
| Battery Status (ECEnabler) | ✅ Working |
| Brightness Keys | ✅ Working |
| iCloud / iMessage / FaceTime | ✅ Working (requires own SMBIOS) |

---

## ❌ What's Not Working

| Feature | Status |
|---------|--------|
| AirDrop | ❌ Not working (Intel WiFi limitation) |
| Handoff / Continuity | ❌ Not working (Intel WiFi limitation) |
| Sidecar | ❌ Not working |
| Apple Watch Unlock | ❌ Not working |

> **หมายเหตุ:** AirDrop และ Continuity ต้องการ Broadcom WiFi/BT card
> แนะนำเปลี่ยนเป็น BCM94360NG หรือ DW1820A หากต้องการใช้งาน

---

## 📦 ACPI

**SSDT (Active):**

| File | Status | Description |
|------|--------|-------------|
| `SSDT_For_DellInspiron3567.aml` | ✅ ON | SSDT หลัก: EC, PLUG, PNLF, GPI0, BATT, XOSI |

**Binary Patches:**

| Patch | Status |
|-------|--------|
| HPET `_STA` → `XSTA` Rename | ✅ Enabled |
| HPET `_CRS` → `XCRS` Rename | ✅ Enabled |
| `OSID` → `XSID` Rename | ✅ Enabled |
| `_OSI` → `XOSI` Rename | ✅ Enabled |

---

## 🔑 Kexts

| Kext | Status | หน้าที่ |
|------|--------|--------|
| Lilu | ✅ | Base patcher framework |
| VirtualSMC | ✅ | SMC emulation |
| WhateverGreen | ✅ | iGPU patch |
| AppleALC | ✅ | Audio (layout-id 17) |
| AirportItlwm | ✅ | Intel WiFi |
| IntelBluetoothFirmware | ✅ | Intel BT firmware |
| IntelBTPatcher | ✅ | Intel BT patch |
| IntelBluetoothInjector | ✅ | Intel BT injector |
| RealtekRTL8100 | ✅ | Realtek LAN |
| BrightnessKeys | ✅ | Fn brightness keys |
| VoodooI2C | ✅ | I2C Trackpad controller |
| VoodooI2CHID | ✅ | I2C HID protocol |
| VoodooPS2Controller | ✅ | Keyboard |
| USBToolBox + UTBMap | ✅ | USB port mapping |
| ECEnabler | ✅ | Battery reading fix |
| NullCPUPowerManagement | ✅ | CPU PM (Broadwell workaround) |
| SMCProcessor | ✅ | CPU temperature |
| SMCSuperIO | ✅ | Fan/sensor data |
| SMCBatteryManager | ❌ Off | แทนด้วย ECEnabler |
| SMCDellSensors | ❌ Off | ไม่จำเป็น |
| SMCLightSensor | ❌ Off | ไม่มี ambient light sensor |

---

## 🖥️ iGPU Configuration

| Property | Value | Notes |
|----------|-------|-------|
| `AAPL,ig-platform-id` | `06002616` | Broadwell HD 5500 |
| `device-id` | `26160000` | HD 5500 device ID |
| `framebuffer-patch-enable` | `01000000` | Enable framebuffer patch |
| `framebuffer-stolenmem` | `00003001` | 48 MB stolen memory |
| `framebuffer-fbmem` | `00009000` | 9 MB framebuffer |

**PCI Path:** `PciRoot(0x0)/Pci(0x2,0x0)`

---

## 🔊 Audio Configuration

| Property | Value |
|----------|-------|
| **layout-id** | `17` |
| **PCI Path** | `PciRoot(0x0)/Pci(0x1b,0x0)` |

---

## 🥾 Boot Arguments

```
debug=0x100 keepsyms=1 -vi2c-force-polling -wegnoegpu -igfxlowmem igfxonln=1 npci=0x3000
```

| Argument | หน้าที่ |
|----------|--------|
| `-vi2c-force-polling` | Force polling mode สำหรับ I2C trackpad |
| `-wegnoegpu` | Disable dGPU (ถ้ามี) |
| `-igfxlowmem` | ลด iGPU memory usage |
| `igfxonln=1` | Force iGPU online |
| `npci=0x3000` | แก้ปัญหา PCI initialization |
| `debug=0x100 keepsyms=1` | Debug logging (ลบออกได้หลัง stable) |

---

## ⚙️ Kernel Quirks

| Quirk | Status |
|-------|--------|
| AppleXcpmCfgLock | ✅ |
| CustomSMBIOSGuid | ✅ |
| DisableIoMapper | ✅ |
| DisableLinkeditJettison | ✅ |
| PanicNoKextDump | ✅ |
| PowerTimeoutKernelPanic | ✅ |
| SetApfsTrimTimeout | `-1` |

## ⚙️ Booter Quirks

| Quirk | Status |
|-------|--------|
| AvoidRuntimeDefrag | ✅ |
| EnableSafeModeSlide | ✅ |
| EnableWriteUnprotector | ✅ |
| ProvideCustomSlide | ✅ |
| ResizeAppleGpuBars | `-1` |
| SetupVirtualMap | ✅ |

---

## ⚙️ BIOS Settings

**Disable:**
- Secure Boot
- Fast Boot
- Intel SGX

**Enable:**
- UEFI Boot Mode
- SATA Mode: AHCI
- Intel Virtualization (VT-x)

---

## 🚀 Installation

1. สร้าง macOS Big Sur installer USB ด้วย `createinstallmedia`
2. วาง EFI folder นี้บน EFI partition ของ USB
3. **Generate SMBIOS ใหม่** ด้วย [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS) — SMBIOS: `MacBookPro12,1`
4. Boot จาก USB → เลือก macOS Installer
5. Format disk เป็น **APFS + GUID Partition Map**
6. หลัง install เสร็จ mount EFI ของ SSD และวาง EFI folder

> **Tip:** ใช้ [MountEFI](https://github.com/corpnewt/MountEFI) สำหรับ mount EFI partition

---

## ⚠️ Disclaimer

> กรุณา **Generate ค่าต่อไปนี้ใหม่เสมอ** ก่อนนำ EFI ไปใช้:
> - `SystemSerialNumber`
> - `MLB`
> - `SystemUUID`
> - `ROM`
>
> ใช้ [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS) หรือ [OCAuxiliaryTools](https://github.com/ic005k/OCAuxiliaryTools)
>
> การใช้ Serial Number ซ้ำกันอาจทำให้ iCloud / iMessage ถูก ban ได้

---

## 📚 References

- [Dortania — Broadwell Laptop Guide](https://dortania.github.io/OpenCore-Install-Guide/config-laptop.plist/broadwell.html)
- [VoodooI2C Documentation](https://voodooi2c.github.io/)
- [OpenIntelWireless](https://github.com/OpenIntelWireless)

---

*Made with ❤️ by [suanaph](https://github.com/J4olahmXDev) · Bangkok, TH*
