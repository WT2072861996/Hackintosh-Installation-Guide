# 🍏 Hackintosh 安装指南

<p align="center">
  <img src="https://img.shields.io/badge/指南-Hackintosh_安装-orange" alt="Guide" />
  <img src="https://img.shields.io/badge/平台-macOS-0071C5?logo=apple&logoColor=white" alt="macOS" />
  <img src="https://img.shields.io/badge/引导-OpenCore_|_Clover-9cf" alt="Bootloader" />
  <img src="https://img.shields.io/badge/语言-English-blue" alt="Language" />
</p>

<div align="center">
  <i>A comprehensive guide for installing macOS on non-Apple hardware — from USB creation to post-installation optimization.</i>
</div>

---

## 📋 Table of Contents

- [Introduction](#introduction)
- [Preparations](#preparations)
- [Step 1: Create macOS Installation USB](#step-1-create-macos-installation-usb)
- [Step 2: Install Bootloader](#step-2-install-bootloader)
- [Step 3: Install macOS](#step-3-install-macos)
- [Step 4: Post-Installation Configuration](#step-4-post-installation-configuration)
- [Step 5: Debugging & Optimization](#step-5-debugging--optimization)
- [Disclaimer](#disclaimer)

---

## Introduction

This guide walks you through the process of installing macOS on a PC (Hackintosh). Note: this may violate Apple's macOS licensing agreement — ensure you have a legitimate copy of macOS.

---

## Preparations

### Hardware Requirements

| Component | Recommendation |
|:---------:|:--------------|
| **CPU** | Intel CPU (4th gen or newer) |
| **Motherboard** | UEFI supported motherboard |
| **RAM** | At least 8 GB |
| **GPU** | AMD or compatible NVIDIA |
| **Storage** | At least 50 GB free |

### Software Requirements

| Tool | Link |
|:---:|:----:|
| macOS Installer | Apple Mac App Store |
| USB Drive (16GB+) | — |
| OpenCore | [acidanthera/OpenCorePkg](https://github.com/acidanthera/OpenCorePkg) |
| Clover | [CloverHackyColor/CloverBootloader](https://github.com/CloverHackyColor/CloverBootloader) |
| gibMacOS | [corpnewt/gibMacOS](https://github.com/corpnewt/gibMacOS) |

---

## Step 1: Create macOS Installation USB

1. **Download macOS** using [gibMacOS](https://github.com/corpnewt/gibMacOS)

2. **Create bootable USB**:

   ```bash
   sudo /Applications/Install\ macOS\ Sonoma.app/Contents/Resources/createinstallmedia \
     --volume /Volumes/MyVolume
   ```

---

## Step 2: Install Bootloader

1. Install OpenCore or Clover on the USB drive
2. Configure `config.plist` with appropriate patches for your hardware
3. Place necessary kexts in the `OC/Kexts` or `Clover/kexts` folder

---

## Step 3: Install macOS

1. **BIOS Settings**: Enable AHCI, disable Secure Boot, set USB as first boot
2. Boot from USB → select macOS installer
3. Follow on-screen instructions to install

---

## Step 4: Post-Installation Configuration

1. Reboot into USB, select the installed system
2. Copy bootloader to system drive's EFI partition
3. Install essential kexts: `Lilu.kext`, `WhateverGreen.kext`, etc.

---

## Step 5: Debugging & Optimization

- Use **Hackintool** for hardware diagnostics
- Use **IORegistryExplorer** for device tree inspection
- Verify all hardware is functioning correctly

---

## License & Disclaimer

> ⚠️ This guide is for educational purposes only. Please respect Apple's software licensing agreements.

Licensed under the [MIT License](LICENSE).

---

<div align="center">
  <img src="https://img.shields.io/badge/Built_with_❤️_for_the_Hackintosh_Community-FF6B6B" alt="Built with love">
</div>
