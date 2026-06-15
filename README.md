# Hackintosh Installation Guide

Comprehensive guide to installing macOS on non-Apple hardware using OpenCore bootloader.

<div align="center">

![macOS](https://img.shields.io/badge/Platform-macOS_Sequoia_|_Sonoma_|_Ventura-0071BC?logo=apple)
![OpenCore](https://img.shields.io/badge/Bootloader-OpenCore_1.x-9cf)
![License](https://img.shields.io/badge/License-MIT-green)
![Status](https://img.shields.io/badge/Status-Maintained-success)

</div>

---

## Table of Contents

- [1. Introduction](#1-introduction)
- [2. Hardware Compatibility](#2-hardware-compatibility)
- [3. Tools & Resources](#3-tools--resources)
- [4. Build the USB Installer](#4-build-the-usb-installer)
- [5. Build the EFI](#5-build-the-efi)
- [6. BIOS Settings](#6-bios-settings)
- [7. Installation](#7-installation)
- [8. Post-Installation](#8-post-installation)
- [9. Troubleshooting](#9-troubleshooting)
- [10. References](#10-references)

---

## 1. Introduction

**OpenCore** is the modern, secure, and actively maintained bootloader for running macOS on non-Apple hardware. It has replaced the legacy Clover bootloader as the community standard.

### OpenCore vs Clover

| Aspect | OpenCore | Clover |
|:-------|:---------|:-------|
| **Security** | FileVault 2, SecureBoot | Limited |
| **Maintenance** | Active (weekly releases) | Deprecated |
| **Performance** | Optimized | Slower |
| **macOS Updates** | Seamless | Often breaks |
| **Configuration** | Well-documented | Sparse docs |

**Use OpenCore. Clover is no longer recommended.**

### Prerequisites

- A PC with compatible hardware (see Section 2)
- A macOS installation from the App Store (borrow a Mac or use a VM)
- Technical familiarity with EFI partitions and BIOS settings

---

## 2. Hardware Compatibility

### CPU

| Generation | Support | Notes |
|:-----------|:-------:|:------|
| Intel 6th–10th Gen (Skylake→Comet Lake) | ✅ Full | Most compatible |
| Intel 12th–14th Gen (Alder Lake→Raptor Lake) | ✅ Partial | E-cores need mapping |
| Intel 15th Gen (Arrow Lake) | ⚠️ Experimental | Limited support |
| AMD Ryzen (1000–7000 series) | ✅ Partial | No iGPU, limited DRM |
| AMD Threadripper (TRX40) | ✅ Partial | NootRX for Navi GPUs |
| AMD Ryzen 8000G (Phoenix) | ⚠️ Experimental | Limited |
| Intel Xeon (v3/v4, W series) | ✅ Partial | Platform dependent |
| Intel Core 2 Duo / Pentium | ❌ | Too old |

### GPU

| GPU | Support | Notes |
|:----|:-------:|:------|
| Intel UHD 630 / Iris Plus | ✅ Native | Highly compatible |
| Intel UHD 7xx (12th–14th Gen) | ✅ Partial | Needs device-id spoof |
| AMD RX 460–6900 XT (Navi/ Polaris) | ✅ Full | Best choice for dGPU |
| AMD RX 6950 XT / 6600 XT | ⚠️ NootRX | Requires spoof |
| AMD RX 7000 series | ⚠️ Experimental | Work in progress |
| NVIDIA GTX 600–900 | ✅ Up to High Sierra | Web Drivers required |
| NVIDIA GTX 1000 series | ❌ Dropped after High Sierra | Unsupported |
| NVIDIA RTX series | ❌ | Unsupported |
| AMD Radeon HD series | ⚠️ Partial | Legacy support |

### Wi-Fi / Bluetooth

| Adapter | Support | Notes |
|:--------|:-------:|:------|
| Intel AX200/AX201/AX210 | ✅ AirportItlwm | Wi-Fi + BT |
| Intel AC 8260/8265/9560 | ✅ AirportItlwm | Wi-Fi + BT |
| Broadcom BCM94360NG | ✅ Native | Best compatibility |
| Broadcom BCM94352Z | ✅ Partial | Needs patches |
| Realtek | ❌ | Unsupported |
| Killer 1650/1650x | ✅ Intel chip | Uses Intel kexts |
| Fenvi T919 | ✅ Native | Plug-and-play |

### Motherboard Chipsets

| Chipset | Support | Notes |
|:--------|:-------:|:------|
| Intel Z390/Z490/Z590 | ✅ Full | USB mapping required |
| Intel B460/B560/B660 | ✅ Full | Most compatible |
| Intel Z690/Z790 | ✅ Partial | Kernel patches needed |
| AMD B450/B550/X570 | ✅ Full | NVRAM fix needed |
| AMD TRX40 | ✅ Partial | NootRX + power patches |
| Intel H610/B760 | ✅ Full | Basic features |

---

## 3. Tools & Resources

### Essential Tools

| Tool | Purpose | Link |
|:-----|:--------|:-----|
| **OpenCorePkg** | Bootloader | [acidanthera/OpenCorePkg](https://github.com/acidanthera/OpenCorePkg) |
| **OCAT** | Config editor | [ic005k/OCAuxiliaryTools](https://github.com/ic005k/OCAuxiliaryTools) |
| **ProperTree** | plist editor | [corpnewt/ProperTree](https://github.com/corpnewt/ProperTree) |
| **gibMacOS** | Download macOS | [corpnewt/gibMacOS](https://github.com/corpnewt/gibMacOS) |
| **MaciASL** | ACPI editor | [acidanthera/MaciASL](https://github.com/acidanthera/MaciASL) |
| **SSDTTime** | Auto-generate SSDTs | [corpnewt/SSDTTime](https://github.com/corpnewt/SSDTTime) |
| **Hackintool** | Diagnostics | [benbaker76/Hackintool](https://github.com/benbaker76/Hackintool) |
| **IORegistryExplorer** | Device tree | [Download](https://github.com/utopia-team/IORegistryExplorer) |

### Essential Kexts

| Kext | Purpose | Link |
|:-----|:--------|:-----|
| **Lilu.kext** | Kernel patching (dependency) | [acidanthera/Lilu](https://github.com/acidanthera/Lilu) |
| **VirtualSMC.kext** | SMC emulation | [acidanthera/VirtualSMC](https://github.com/acidanthera/VirtualSMC) |
| **WhateverGreen.kext** | GPU fixes | [acidanthera/WhateverGreen](https://github.com/acidanthera/WhateverGreen) |
| **AppleALC.kext** | Onboard audio | [acidanthera/AppleALC](https://github.com/acidanthera/AppleALC) |
| **IntelMausi.kext** | Intel Ethernet | [acidanthera/IntelMausi](https://github.com/acidanthera/IntelMausi) |
| **LucyRTL8125Ethernet.kext** | Realtek 2.5GbE | [Mieze/LucyRTL8125Ethernet](https://github.com/Mieze/LucyRTL8125Ethernet) |
| **NVMeFix.kext** | NVMe power mgmt | [acidanthera/NVMeFix](https://github.com/acidanthera/NVMeFix) |
| **USBToolBox.kext** | USB mapping | [USBToolBox](https://github.com/USBToolBox/tool) |
| **RestrictEvents.kext** | Process filtering | [acidanthera/RestrictEvents](https://github.com/acidanthera/RestrictEvents) |

### Documentation

- **[OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)** — The definitive guide. Read this.
- **[OpenCore Configuration.pdf](https://github.com/acidanthera/OpenCorePkg/blob/master/Docs/Configuration.pdf)** — Full config.plist reference.
- **[Getting Started with ACPI](https://dortania.github.io/Getting-Started-With-ACPI/)** — SSDT creation guide.

---

## 4. Build the USB Installer

### Option A: Using gibMacOS (Windows/macOS/Linux)

```bash
# Clone gibMacOS
git clone https://github.com/corpnewt/gibMacOS.git
cd gibMacOS

# List available macOS versions
./gibMacOS.command

# Download (replace with desired version)
./gibMacOS.command -v 15.0  # macOS Sequoia
# ./gibMacOS.command -v 14.5  # macOS Sonoma
```

### Option B: Using a Mac (direct App Store download)

Download macOS from the App Store, then:

```bash
# Find the installer app
ls /Applications/Install\ macOS\ *.app

# Create bootable USB (16 GB+ USB drive)
sudo /Applications/Install\ macOS\ Sequoia.app/Contents/Resources/createinstallmedia \
  --volume /Volumes/MyUSB

# Replace MyUSB with your USB drive name
# The drive will be renamed and formatted automatically
```

### Verify the USB

```bash
# Check the USB structure
ls /Volumes/Install\ macOS\ Sequoia/
# You should see: .IABootFiles  .disk_label  .disk_label_2x  Library  System  installimageapp  usb

# Or for Sonoma:
ls /Volumes/Install\ macOS\ Sonoma/
```

---

## 5. Build the EFI

### Directory Structure

```
EFI/
├── BOOT/
│   └── BOOTx64.efi            # Bootstrap (from OpenCorePkg)
└── OC/
    ├── ACPI/
    │   ├── SSDT-EC.aml         # Embedded Controller fix
    │   ├── SSDT-PLUG.aml       # CPU power management
    │   ├── SSDT-USBX.aml       # USB power properties
    │   └── SSDT-PNLF.aml       # Backlight (laptops only)
    ├── Drivers/
    │   ├── OpenRuntime.efi     # Required runtime
    │   ├── OpenCanopy.efi      # GUI picker (optional)
    │   └── HfsPlus.efi         # HFS+ support
    ├── Kexts/
    │   ├── Lilu.kext           # Always required
    │   ├── VirtualSMC.kext     # Always required
    │   ├── WhateverGreen.kext  # GPU fixes
    │   ├── AppleALC.kext       # Audio (if compatible)
    │   ├── IntelMausi.kext     # Intel Ethernet
    │   └── ...                 # Hardware-specific kexts
    ├── Resources/              # GUI picker icons (optional)
    ├── Tools/                  # Debug tools (optional)
    ├── OpenCore.efi            # Main bootloader
    └── config.plist            # Configuration (the core of the build)
```

### Key config.plist Sections

#### ACPI
```xml
<key>Add</key>
<array>
    <dict>
        <key>Comment</key>
        <string>EC Fix</string>
        <key>Enabled</key>
        <true/>
        <key>Path</key>
        <string>SSDT-EC.aml</string>
    </dict>
    <dict>
        <key>Comment</key>
        <string>CPU Plugin</string>
        <key>Enabled</key>
        <true/>
        <key>Path</key>
        <string>SSDT-PLUG.aml</string>
    </dict>
</array>
```

#### boot-args (NVRAM)
```xml
<key>boot-args</key>
<string>-v keepsyms=1 debug=0x100</string>
<!--
  -v              : Verbose mode (text output instead of Apple logo)
  keepsyms=1      : Show symbols in panics
  debug=0x100     : Enable debug logging
  alcid=1         : Force AppleALC layout (varies by motherboard)
  agdpmod=pikera  : Disable GPU board-id check (RX 5000/6000 series)
-->
```

#### PlatformInfo (SMBIOS)

Choose a SMBIOS matching your CPU generation:

| CPU Generation | SMBIOS |
|:---------------|:-------|
| Coffee Lake (8th–9th Gen) | iMac19,1 |
| Comet Lake (10th Gen) | iMac20,1 |
| Alder Lake (12th Gen) | MacPro7,1 |
| AMD Ryzen | MacPro7,1 |
| AMD Threadripper | MacPro7,1 |

Generate serials using **GenSMBIOS** or **OCAT**:

```bash
git clone https://github.com/corpnewt/GenSMBIOS.git
cd GenSMBIOS
./GenSMBIOS.command
# Select option 1, then enter (e.g.) "iMacPro1,1"
```

> ⚠️ Never use generated serials that are already registered to real Macs. Check at [checkcoverage.apple.com](https://checkcoverage.apple.com).

### Getting SSDTs

**Method 1 — SSDTTime (recommended for beginners):**
```bash
git clone https://github.com/corpnewt/SSDTTime.git
cd SSDTTime
./SSDTTime.command
```

**Method 2 — Pre-built for common platforms:**
- [Dortania's ACPI Samples](https://github.com/dortania/OpenCore-Install-Guide/tree/master/extra-files)

### USB Mapping

Always create a custom USB map — this prevents sleep/wake issues and disables unused ports.

```bash
# Option 1: USBToolBox (Windows)
#   Download https://github.com/USBToolBox/tool
#   Run on Windows, generate UTBMap.kext

# Option 2: Hackintool (macOS) — post-install
#   Run Hackintool → USB tab → Map ports
```

---

## 6. BIOS Settings

### Required Settings

| Setting | Value |
|:--------|:------|
| Secure Boot | **Disabled** |
| Fast Boot | **Disabled** |
| Boot Mode | **UEFI** (not Legacy/CSM) |
| SATA Mode | **AHCI** (not RAID/Intel RST) |
| VT-d | **Disabled** (or add `dart=0` to boot-args) |
| Above 4G Decoding | **Enabled** (also enable Resizable BAR) |
| XHCI Hand-off | **Enabled** |
| Legacy USB | **Enabled** |
| OS Type | **Other OS** (not Windows 8/10) |
| CFG Lock | **Disabled** (use `AppleXcpmCfgLock` if unavailable) |

### Per-Platform BIOS References

| Platform | Notes |
|:---------|:------|
| **ASUS** | Search "Secure Boot" → OS Type: Other OS |
| **MSI** | Settings → Boot → UEFI/Legacy: UEFI only |
| **Gigabyte** | BIOS → CSM: Disabled, Secure Boot: Disabled |
| **ASRock** | Advanced → USB Configuration → XHCI Hand-off: Enabled |
| **DELL** | May need DVMT modification via GRUB Shell |
| **HP** | Look for "Legacy Support" options |
| **Lenovo** | May require CFG Lock unlock tool |

---

## 7. Installation

### First Boot

1. Insert USB installer
2. Boot from USB (usually F12/F2/Del to enter boot menu)
3. Select your USB → OpenCore menu appears
4. Choose `Install macOS [version]`

### What to Expect

- **Stage 1 — Installer loads:** Text scrolling (verbose mode). May take 1–5 minutes.
- **Stage 2 — macOS Utilities:** Disk Utility → Erase target drive as `APFS` / `GUID Partition Map`
- **Stage 3 — Install:** Select target drive → Install → System will reboot 2–3 times
- **Stage 4 — Region Setup:** Configure language, Wi-Fi, Apple ID, etc.

> ⚠️ Always select your USB drive at each reboot until you've copied the EFI to the system drive.

### Common Installation Errors

| Error | Likely Cause | Fix |
|:------|:-------------|:----|
| `[EB|#LOG:EXITBS:START]` | Booter quirks | Enable `DevirtualiseMmio`, `SyncRuntimePermissions` |
| `Cannot boot from device` | Wrong SMBIOS | Use proper SMBIOS for your CPU |
| Panic: `AppleIntelCFLGraphics` | GPU not configured | Check device-id / WhateverGreen settings |
| Panic: `No HPETs available` | HPET/IRQ conflicts | Enable SSDT-HPET or check ACPI patches |
| `Still waiting for root device` | USB not mapped | Create proper USB map (USBToolBox) |

---

## 8. Post-Installation

### Step 1: Copy EFI to System Drive

```bash
# Mount system EFI partition
diskutil list
sudo diskutil mount diskXs1  # Replace with your system EFI partition

# Copy EFI folder
sudo cp -R /Volumes/InstallUSB/EFI /Volumes/EFI/

# Or use OpenCore Configurator to mount
```

> ⚠️ Keep a backup USB with the working EFI at all times.

### Step 2: Essential Post-Install Tasks

- [ ] **USB Mapping** — Run USBToolBox or Hackintool to create a permanent USB map
- [ ] **Audio Verification** — Test all audio outputs, try different `alcid=` values if needed
- [ ] **Sleep/Wake** — Test sleep: `pmset sleepnow`. Check console for wake failures
- [ ] **iMessage/FaceTime** — Verify Apple services work (serial-dependent)
- [ ] **DRM** — Test Apple TV+, Netflix in Safari. May need `shikigva=` boot-args
- [ ] **GPU Benchmarks** — Run Geekbench or Metal benchmark to verify GPU acceleration
- [ ] **Bluetooth** — Pair devices, check continuity features

### Recommended Kexts for Post-Install

| Kext | When to Add |
|:-----|:------------|
| `RestrictEvents.kext` | Always (blocks unwanted processes) |
| `FeatureUnlock.kext` | Sidecar / AirPlay support |
| `BrightnessKeys.kext` | Laptops with brightness key issues |
| `CPUFriend.kext` | CPU power management optimization |
| `CpuTopologyRebuild.kext` | 12th–14th Gen Intel (E-core) |

### Making Updates Safe

```bash
# Keep backups of EFI before updates
cp -R /Volumes/EFI/EFI /Volumes/EFI/EFI-backup-$(date +%Y%m%d)

# For macOS updates:
# 1. Update kexts and OpenCore to latest versions
# 2. Install the macOS update normally
# 3. If boot fails, use backup USB to recover
```

---

## 9. Troubleshooting

### Boot Log Collection

```bash
# Enable detailed logging in config.plist NVRAM:
# boot-args: -v debug=0x100 keepsyms=1

# Collect kernel log (after booting):
log show --last boot --style syslog | grep -i "error\|panic\|fail" > hackintosh_debug.log
```

### Kext Conflicts

| Symptom | Possible Culprit |
|:--------|:-----------------|
| Kernel panic at boot | Incompatible kext version |
| Random freezing | Wrong USB map or SMBIOS |
| No audio | Wrong `alcid=` or missing AppleALC |
| No Wi-Fi | Wrong AirportItlwm version for macOS |
| Bluetooth not working | Missing IntelBluetoothFirmware or BlueToolFixup |
| GPU artifacts | Missing shikigva boot-args or device-id |

### Where to Get Help

| Resource | Link |
|:---------|:-----|
| **r/Hackintosh** | [reddit.com/r/hackintosh](https://reddit.com/r/hackintosh) |
| **Dortania Guide** | [dortania.github.io](https://dortania.github.io/OpenCore-Install-Guide/) |
| **TonyMacx86** | [tonymacx86.com](https://tonymacx86.com) |
| **InsanelyMac** | [insanelymac.com](https://insanelymac.com) |
| **OpenCore Discussion** | [GitHub Discussions](https://github.com/acidanthera/OpenCorePkg/discussions) |

### Build a Debug EFI

Create a separate debug EFI for troubleshooting:

```
- Keep one working EFI (production)
- Build a debug EFI with:
  • OpenCore DEBUG version (from OpenCorePkg DEBUG builds)
  • boot-args: -v debug=0x100 keepsyms=1
  • Target: 10 (log everything)
  • DisplayLevel: 2147483714 (all)
```

---

## 10. References

### Official Documentation

- [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg) — Bootloader & documentation
- [Configuration.pdf](https://github.com/acidanthera/OpenCorePkg/blob/master/Docs/Configuration.pdf) — Full parameter reference
- [Dortania OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/) — Step-by-step platform guides
- [Dortania ACPI Guide](https://dortania.github.io/Getting-Started-With-ACPI/) — SSDT creation

### Key Kext Repositories

- [acidanthera](https://github.com/acidanthera) — Lilu, VirtualSMC, WhateverGreen, AppleALC, NVMeFix
- [OpenIntelWireless](https://github.com/OpenIntelWireless) — AirportItlwm, IntelBluetoothFirmware
- [USBToolBox](https://github.com/USBToolBox) — USB mapping tool
- [NootInc](https://github.com/NootInc) — NootRX for AMD Navi GPUs

### Community Tools

- [OCAT](https://github.com/ic005k/OCAuxiliaryTools) — OpenCore config editor
- [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS) — SMBIOS generator
- [SSDTTime](https://github.com/corpnewt/SSDTTime) — Auto SSDT generation
- [Hackintool](https://github.com/benbaker76/Hackintool) — System diagnostics

---

## License

This guide is for educational purposes. Installing macOS on non-Apple hardware may violate Apple's End User License Agreement.

MIT License — See [LICENSE](LICENSE).

---

<p align="center">
  <i>Built with ❤️ for the Hackintosh community.</i>
</p>
