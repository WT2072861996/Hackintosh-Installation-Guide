# 🍏 OpenCore Hackintosh 完整安装指南

**全平台适配 · Intel/AMD CPU · 从入门到精通 · 硬件兼容性矩阵 · BIOS 优化 · 故障排除**

<div align="center">

![macOS](https://img.shields.io/badge/Platform-macOS_Sequoia_|_Sonoma_|_Ventura-0071BC?logo=apple&style=flat-square)
![OpenCore](https://img.shields.io/badge/Bootloader-OpenCore_1.x-9cf?style=flat-square)
![CPU](https://img.shields.io/badge/CPU-Intel_6th~15th_|_AMD_Ryzen-blue?style=flat-square)
![GPU](https://img.shields.io/badge/GPU-Intel_UHD_|_AMD_Navi-green?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)
![Status](https://img.shields.io/badge/Status-Maintained-success?style=flat-square)

**[📖 快速导航](#快速导航) · [💻 硬件兼容](#硬件兼容性) · [🛠️ 工具集](#工具--资源) · [⚙️ BIOS 配置](#bios-设置) · [❓ 故障排除](#故障排除)**

</div>

---

## 📌 关键信息速览

| 指标 | 说明 |
|:---:|:-----|
| **引导方式** | OpenCore 1.0x（现代、安全、活跃维护） |
| **支持系统** | macOS Sequoia 15.x · Sonoma 14.x · Ventura 13.x |
| **CPU 支持** | Intel 6th-15th Gen · AMD Ryzen 1000-7000 · Threadripper |
| **GPU 支持** | Intel UHD/Iris · AMD Radeon Navi/RDNA · NVIDIA 老旧系列 |
| **难度级别** | ⭐ 初级 ⭐⭐ 中级 ⭐⭐⭐ 高级 |

---

## 🎯 快速导航

### 🚀 新手入门
1. **[第1步：理解 OpenCore](#1-introduction)** - 为什么选择 OpenCore？
2. **[第2步：检查硬件](#2-hardware-compatibility)** - 你的硬件支持吗？
3. **[第3步：准备工具](#3-tools--resources)** - 下载必需软件
4. **[第4步：构建 EFI](#5-build-the-efi)** - 配置引导程序
5. **[第5步：安装系统](#7-installation)** - 启动安装程序

### 🔧 进阶话题
- [BIOS 优化](#6-bios-settings) - 主板设置指南
- [SSDT 定制](#getting-ssdts) - ACPI 热补丁
- [USB 映射](#usb-mapping) - 端口优化
- [故障排除](#9-troubleshooting) - 常见问题解决

---

## 1. 简介

**OpenCore** 是运行 macOS 在非 Apple 硬件上的现代化、安全且活跃维护的引导程序。它已成为 Hackintosh 社区的标准。

### 🆚 OpenCore vs Clover

| 特性 | OpenCore | Clover |
|:---:|:--------:|:------:|
| **安全性** | ✅ FileVault 2, SecureBoot | ⚠️ 受限 |
| **维护状态** | ✅ 每周更新 | ❌ 停止维护 |
| **性能** | ✅ 优化 | ⚠️ 较慢 |
| **系统更新** | ✅ 无缝升级 | ❌ 容易崩溃 |
| **文档完整性** | ✅ 详尽 | ⚠️ 不足 |

**结论**：使用 OpenCore。Clover 已不推荐。

### 📋 前置要求

- 🖥️ 兼容的 PC 硬件（见 [硬件兼容](#2-hardware-compatibility) 章节）
- 🍎 macOS 系统镜像（从 App Store 或虚拟机获取）
- 💾 USB 驱动器（≥16 GB）
- 📚 对 EFI 分区和 BIOS 的基本了解
- ⏱️ 预留 2-4 小时

---

## 2. 硬件兼容性

### 💻 CPU 支持

| 代数 | 型号范围 | 支持度 | 备注 |
|:---:|:---------|:-----:|:-----|
| **Intel 6代** | Skylake (6100/6500) | ✅ 完全 | 最兼容 |
| **Intel 7代** | Kaby Lake (7500) | ✅ 完全 | 安全选择 |
| **Intel 8-9代** | Coffee Lake (8400/9400) | ✅ 完全 | 广泛支持 |
| **Intel 10代** | Comet Lake (10400) | ✅ 完全 | 推荐平台 |
| **Intel 12-14代** | Alder/Raptor Lake | ⚠️ 部分 | 需 E-core 映射 |
| **Intel 15代** | Arrow Lake | ⚠️ 测试中 | 实验性 |
| **AMD Ryzen** | 1000-7000 系列 | ⚠️ 部分 | 无核显、DRM 受限 |
| **AMD Threadripper** | TRX40 平台 | ⚠️ 部分 | NootRX + 补丁 |

### 🎮 GPU 支持

| GPU 系列 | 支持度 | 驱动方案 | 备注 |
|:---------|:-----:|:-------:|:-----|
| **Intel UHD 630** | ✅ 原生 | 直接 | 最高兼容 |
| **Intel Iris Plus** | ✅ 原生 | 直接 | 笔记本常见 |
| **Intel UHD 7xx** | ✅ 部分 | Device-ID 仿冒 | 需配置 |
| **AMD RX 5000/6000** | ✅ 完全 | RDNA 驱动 | 推荐独显 |
| **AMD RX 6950 XT** | ⚠️ 仿冒 | NootRX | 需 SSDT |
| **NVIDIA GTX 900** | ✅ High Sierra | Web 驱动 | 系统限制 |
| **NVIDIA RTX/30系** | ❌ 不支持 | — | 官方放弃 |

### 🌐 网络 & 蓝牙

| 设备 | 支持度 | 驱动 | 备注 |
|:-----|:-----:|:----:|:-----|
| **Intel AX200/AX210** | ✅ | AirportItlwm | Wi-Fi + BT |
| **Intel AC 9560** | ✅ | AirportItlwm | 常见方案 |
| **Broadcom BCM94360** | ✅ | 原生 | 最佳体验 |
| **Realtek RTL8111** | ✅ | RealtekRTL8111 | 有线网卡 |
| **Killer 1650x** | ✅ | Intel 芯片 | 实际为 Intel |
| **Qualcomm QCA** | ❌ | — | 完全不支持 |

### 🖲️ 主板芯片组

| 芯片组 | 支持度 | USB 映射 | 备注 |
|:-----:|:-----:|:--------:|:-----|
| **Intel Z390/Z490** | ✅ 完全 | 需要 | USB 较多 |
| **Intel B460/B560** | ✅ 完全 | 推荐 | 最稳定 |
| **Intel Z690/Z790** | ✅ 部分 | 复杂 | 需核心补丁 |
| **AMD B450/B550** | ✅ 完全 | 需要 | NVRAM 修复 |
| **AMD X570** | ✅ 完全 | 需要 | 供电充足 |
| **AMD TRX40** | ⚠️ 部分 | 复杂 | 只支持 MacPro7,1 |

---

## 3. 工具 & 资源

### 🔧 必需工具

| 工具 | 功能 | 平台 | 下载 |
|:-----|:-----|:----:|:--:|
| **OpenCorePkg** | 引导程序核心 | Windows/Mac/Linux | [GitHub](https://github.com/acidanthera/OpenCorePkg) |
| **OCAT** | 配置编辑器（推荐） | Windows/Mac | [GitHub](https://github.com/ic005k/OCAuxiliaryTools) |
| **ProperTree** | Plist 编辑器 | Windows/Mac/Linux | [GitHub](https://github.com/corpnewt/ProperTree) |
| **gibMacOS** | macOS 下载工具 | Windows/Mac/Linux | [GitHub](https://github.com/corpnewt/gibMacOS) |
| **SSDTTime** | SSDT 自动生成 | Mac | [GitHub](https://github.com/corpnewt/SSDTTime) |
| **MaciASL** | ACPI 编辑器 | Mac | [GitHub](https://github.com/acidanthera/MaciASL) |
| **Hackintool** | 系统诊断工具 | Mac | [GitHub](https://github.com/benbaker76/Hackintool) |
| **USBToolBox** | USB 映射工具 | Windows | [GitHub](https://github.com/USBToolBox/tool) |

### 📦 核心 Kexts（驱动）

| Kext | 功能 | 何时必需 |
|:-----|:-----|:-------:|
| **Lilu.kext** | 内核补丁框架 | ✅ 总是 |
| **VirtualSMC.kext** | SMC 模拟 | ✅ 总是 |
| **WhateverGreen.kext** | GPU 修复 | ✅ 总是 |
| **AppleALC.kext** | 声卡驱动 | ✅ 音频设备 |
| **IntelMausi.kext** | Intel 网卡 | ✅ 有线网卡 |
| **NVMeFix.kext** | NVMe 电源管理 | ⚠️ NVMe 驱动 |
| **RestrictEvents.kext** | 事件限制 | ⚠️ 推荐 |
| **CPUFriend.kext** | CPU 电源管理 | ⚠️ 优化 |

### 📖 官方文档

- **[OpenCore 安装指南（Dortania）](https://dortania.github.io/OpenCore-Install-Guide/)** ← 必读
- **[OpenCore Configuration.pdf](https://github.com/acidanthera/OpenCorePkg/blob/master/Docs/Configuration.pdf)** - 配置参考
- **[ACPI 入门指南](https://dortania.github.io/Getting-Started-With-ACPI/)** - SSDT 学习

---

## 4. 构建 USB 安装程序

### 📥 方式 A：gibMacOS（跨平台）

```bash
# 克隆 gibMacOS
git clone https://github.com/corpnewt/gibMacOS.git
cd gibMacOS

# 列出可用版本
./gibMacOS.command

# 下载 macOS（按需选择）
./gibMacOS.command -v 15.0   # macOS Sequoia
./gibMacOS.command -v 14.5   # macOS Sonoma
./gibMacOS.command -v 13.6   # macOS Ventura
```

### 🖥️ 方式 B：真实 Mac（直接安装）

```bash
# 从 App Store 下载（需 Mac）
# 然后创建安装盘

sudo /Applications/Install\ macOS\ Sequoia.app/Contents/Resources/createinstallmedia \
  --volume /Volumes/MyUSB

# 替换 MyUSB 为你的 USB 盘名称
# 过程约 5-10 分钟
```

### ✅ 验证 USB

```bash
# 检查 USB 结构
ls /Volumes/Install\ macOS\ Sequoia/

# 应显示：
# .IABootFiles  .disk_label  Library  System  installimageapp  usb
```

---

## 5. 构建 EFI

### 📁 文件结构参考

```
EFI/
├── BOOT/
│   └── BOOTx64.efi              # OpenCore 引导加载程序
└── OC/
    ├── ACPI/                    # ACPI 热补丁（.aml 文件）
    │   ├── SSDT-EC.aml          # EC 修复（必需）
    │   ├── SSDT-PLUG.aml        # CPU 电源管理
    │   ├── SSDT-USBX.aml        # USB 电源属性
    │   └── SSDT-PNLF.aml        # 笔记本背光
    ├── Drivers/
    │   ├── OpenRuntime.efi      # 运行时环境（必需）
    │   ├── HfsPlus.efi          # HFS+ 支持
    │   └── OpenCanopy.efi       # GUI 引导界面
    ├── Kexts/                   # 驱动程序（.kext 包）
    │   ├── Lilu.kext
    │   ├── VirtualSMC.kext
    │   ├── WhateverGreen.kext
    │   ├── AppleALC.kext
    │   └── ...（硬件特定驱动）
    ├── Resources/               # 引导界面资源
    ├── Tools/                   # 调试工具
    ├── OpenCore.efi             # 主引导程序
    └── config.plist             # 配置文件（核心）
```

### ⚙️ config.plist 关键部分

#### ACPI 部分
```xml
<key>Add</key>
<array>
    <dict>
        <key>Comment</key>
        <string>EC Fix for non-Mac hardware</string>
        <key>Enabled</key>
        <true/>
        <key>Path</key>
        <string>SSDT-EC.aml</string>
    </dict>
    <dict>
        <key>Comment</key>
        <string>CPU Power Management</string>
        <key>Enabled</key>
        <true/>
        <key>Path</key>
        <string>SSDT-PLUG.aml</string>
    </dict>
</array>
```

#### 启动参数（boot-args）
```xml
<key>boot-args</key>
<string>-v debug=0x100 keepsyms=1 alcid=1</string>

<!-- 参数说明：
  -v              : 详细模式（显示启动日志）
  debug=0x100     : 调试级别
  keepsyms=1      : 崩溃信息符号
  alcid=1         : AppleALC 音频 Layout ID
  agdpmod=pikera  : GPU 验证跳过（AMD Navi）
  igfx=auto       : Intel 核显启用
-->
```

#### SMBIOS 选择

根据 CPU 选择合适的机型：

| CPU 代数 | 推荐 SMBIOS | 说明 |
|:---------|:-----------|:-----|
| 6-7代 Intel | MacBookPro14,1 | 笔记本 |
| 8-9代 Intel | iMac19,1 | 台式机 |
| 10代 Intel | iMac20,1 | 台式机 |
| 12+ 代 Intel | MacPro7,1 | 多核工作站 |
| AMD Ryzen | MacPro7,1 | 工作站级 |

**⚠️ 重要**：使用 GenSMBIOS 生成唯一序列号，勿用他人的三码

---

## 6. BIOS 设置

### 🔴 必需禁用

| 设置 | 原因 |
|:-----|:-----|
| **Secure Boot** | macOS 不兼容 |
| **Fast Boot** | 引导问题 |
| **CSM/Legacy Boot** | 必须 UEFI |
| **CFG Lock** | CPU 限制 |
| **VT-d** | 可选（或用 dart=0） |

### 🟢 必需启用

| 设置 | 原因 |
|:-----|:-----|
| **UEFI Boot** | 现代启动方式 |
| **AHCI Mode** | SSD 最优性能 |
| **Above 4G Decoding** | 64 位寻址 |
| **XHCI Hand-off** | USB 3.0 支持 |
| **Resizable BAR** | 可选但推荐 |

### 🖲️ 品牌特定设置

#### ASUS
- Security → Secure Boot: **Disabled**
- Boot → Boot Mode: **UEFI Only**
- Chipset → SATA Mode: **AHCI**

#### MSI
- Settings → Boot → Boot Mode: **UEFI**
- Settings → Advanced → Integrated Peripherals: **AHCI**

#### Gigabyte
- BIOS → CSM Support: **Disabled**
- BIOS → Secure Boot: **Disabled**
- Chipset → SATA Configuration: **AHCI**

#### ASRock
- Advanced → PCIE Configuration → Resizable BAR: **Enabled**
- Advanced → USB Configuration → XHCI Hand-off: **Enabled**

---

## 7. 安装流程

### 🚀 第一次启动

1. **插入 USB 安装盘**
2. **重启电脑 → 进入启动菜单**（通常 F12/F2/Del）
3. **选择 USB 驱动器**
4. **OpenCore 菜单出现** → 选择 `Install macOS [版本]`

### ⏱️ 安装阶段

| 阶段 | 现象 | 耗时 |
|:--:|:-----|:----:|
| 1️⃣ | 文字滚动（verbose 模式） | 1-5 分钟 |
| 2️⃣ | 磁盘工具 → 擦除为 APFS | 5 分钟 |
| 3️⃣ | 文件复制 → 系统重启（2-3 次） | 10-20 分钟 |
| 4️⃣ | 区域设置 → Apple ID | 5 分钟 |

### ⚠️ 重要提示

每次重启时**都选择 USB 盘启动**，直到系统完全安装到硬盘

---

## 8. 安装后

### 📋 必做清单

- [ ] **复制 EFI 到硬盘** - 系统 EFI 分区
- [ ] **USB 端口映射** - USBToolBox 或 Hackintool
- [ ] **生成三码** - SMBIOS 序列号（GenSMBIOS）
- [ ] **测试睡眠/唤醒** - `pmset sleepnow`
- [ ] **验证音频** - 测试多个 Layout ID
- [ ] **检查 DRM** - Netflix/AppleTV+ 视频
- [ ] **配置 iMessage** - 需正确的序列号

### 🔨 推荐后装 Kexts

| Kext | 用途 | 何时装 |
|:-----|:-----|:-----:|
| RestrictEvents | 限制进程 | ✅ 推荐 |
| CPUFriend | 电源优化 | ⚠️ 可选 |
| BrightnessKeys | 亮度键 | ⚠️ 笔记本 |
| FeatureUnlock | 接力/Sidecar | ⚠️ 可选 |

### 🆘 EFI 备份

```bash
# 创建备份
cp -R /Volumes/EFI/EFI /Volumes/EFI/EFI-backup-$(date +%Y%m%d)

# 保留一个工作 USB 作应急
```

---

## 9. 故障排除

### 🔍 收集诊断信息

```bash
# 查看启动日志
log show --last boot --style syslog | grep -i "error\|panic\|fail"

# 导出为文件
log show --last boot > ~/hackintosh_debug.log
```

### 📋 常见错误表

| 错误信息 | 原因 | 解决方案 |
|:---------|:-----|:-------:|
| `[EB\|#LOG:EXITBS]` | Booter 参数 | 启用 DevirtualiseMmio |
| `Cannot boot from device` | SMBIOS 错误 | 检查 CPU 对应型号 |
| `Panic: AppleIntelCFLGraphics` | GPU 未配置 | 检查 device-id / ig-platform-id |
| `No HPETs available` | ACPI 冲突 | 启用 SSDT-HPET + 重命名 |
| `Still waiting for root device` | USB 未映射 | 创建 USB 端口映射 |
| 黑屏 / 无输出 | 显卡问题 | 尝试 `alcid=` 或 `agdpmod=` |

### 💬 获取帮助

- **[r/Hackintosh](https://reddit.com/r/hackintosh)** - 社区讨论
- **[Dortania 指南](https://dortania.github.io/)** - 官方文档
- **[InsanelyMac 论坛](https://insanelymac.com)** - 技术讨论
- **[OpenCore Issues](https://github.com/acidanthera/OpenCorePkg/issues)** - Bug 报告

### 🐛 构建调试 EFI

用于深度排查的专用配置：

```
创建备用引导项：
1. OpenCore DEBUG 版本（从 OpenCorePkg DEBUG builds）
2. boot-args: -v debug=0x100 keepsyms=1
3. Target: 10（全量日志）
4. DisplayLevel: 2147483714（所有消息）
```

---

## 10. 参考资源

### 📚 必读文档

- [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/) ⭐ 权威指南
- [OpenCore Configuration Reference](https://github.com/acidanthera/OpenCorePkg/blob/master/Docs/Configuration.pdf)
- [Getting Started with ACPI](https://dortania.github.io/Getting-Started-With-ACPI/)

### 🔧 核心项目

| 项目 | 功能 |
|:-----|:-----|
| [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg) | 引导程序 & 文档 |
| [acidanthera](https://github.com/acidanthera) | Lilu, VirtualSMC, WhateverGreen, AppleALC |
| [OpenIntelWireless](https://github.com/OpenIntelWireless) | Wi-Fi & 蓝牙驱动 |
| [USBToolBox](https://github.com/USBToolBox) | USB 映射工具 |
| [NootInc](https://github.com/NootInc) | AMD Navi GPU 支持 |

### 🛠️ 必备工具

| 工具 | 用途 | 下载 |
|:-----|:-----|:----:|
| OCAT | 配置编辑 | [GitHub](https://github.com/ic005k/OCAuxiliaryTools) |
| GenSMBIOS | 生成序列号 | [GitHub](https://github.com/corpnewt/GenSMBIOS) |
| SSDTTime | 自动生成 SSDT | [GitHub](https://github.com/corpnewt/SSDTTime) |
| Hackintool | 系统诊断 | [GitHub](https://github.com/benbaker76/Hackintool) |

---

## 📄 许可证

本指南仅供学习和研究使用。

> ⚠️ **免责声明**：在非 Apple 硬件上安装 macOS 可能违反 Apple EULA。本项目不对任何损失负责。

MIT License - 详见 [LICENSE](LICENSE)

---

<p align="center">
  <b>为 Hackintosh 社区贡献 ❤️</b><br/>
  <sub>最后更新：2026年 · OpenCore 1.0x · macOS Sequoia 兼容</sub>
</p>
