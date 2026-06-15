# Hackintosh Installation Guide

A comprehensive guide for installing macOS on non-Apple hardware.

![Platform](https://img.shields.io/badge/Platform-macOS-0071BC)
![Bootloader](https://img.shields.io/badge/Bootloader-OpenCore_|_Clover-9cf)
![License](https://img.shields.io/badge/License-MIT-green)

---

## Table of Contents

- [Preparations](#preparations)
- [Step 1: Create USB](#step-1-create-macos-installation-usb)
- [Step 2: Install Bootloader](#step-2-install-bootloader)
- [Step 3: Install macOS](#step-3-install-macos)
- [Step 4: Post-Installation](#step-4-post-installation-configuration)
- [Step 5: Debugging](#step-5-debugging--optimization)

---

## Preparations

**Hardware:**
- Intel CPU (4th gen or newer)
- UEFI-compatible motherboard
- 8 GB+ RAM
- AMD or compatible NVIDIA GPU
- 50 GB+ free storage

**Software:**

| Tool | Link |
|:-----|:-----|
| macOS Installer | Mac App Store |
| OpenCore | [acidanthera/OpenCorePkg](https://github.com/acidanthera/OpenCorePkg) |
| Clover | [CloverHackyColor/CloverBootloader](https://github.com/CloverHackyColor/CloverBootloader) |
| gibMacOS | [corpnewt/gibMacOS](https://github.com/corpnewt/gibMacOS) |
| USB Drive | 16 GB+ |

---

## Step 1: Create macOS Installation USB

```bash
sudo /Applications/Install\ macOS\ Sonoma.app/Contents/Resources/createinstallmedia \
  --volume /Volumes/MyVolume
```

## Step 2: Install Bootloader

- Install OpenCore or Clover to USB
- Configure `config.plist` for your hardware
- Add required kexts

## Step 3: Install macOS

1. BIOS: Enable AHCI, disable Secure Boot, set USB as primary boot
2. Boot USB → OpenCore menu → macOS installer
3. Follow on-screen instructions

## Step 4: Post-Installation Configuration

- Copy bootloader to system drive EFI partition
- Install essential kexts: `Lilu.kext`, `WhateverGreen.kext`, etc.

## Step 5: Debugging & Optimization

- **Hackintool**: Hardware diagnostics
- **IORegistryExplorer**: Device tree inspection

---

## Disclaimer

This guide is for educational purposes only. Installing macOS on non-Apple hardware may violate Apple's EULA.

---

*Licensed under the [MIT License](LICENSE).*
