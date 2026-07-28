# Arch Linux 装机历程分析

> **用户**: `adorukw` | **主机**: ADORUKW-T480  
> **内核**: Linux 7.1.5-arch1-2 (x86_64)  
> **磁盘**: 238.5G SSD — `/dev/sda1` /boot (976M), `/dev/sda2` / (229.6G ext4), `/dev/sda3` swap (7.9G)  
> **引导**: UEFI + GRUB | **桌面**: Wayland (niri)  
> **安装时间**: 约 2026-07-28 ~ 07-29

---

## 一、系统基础

| 软件 | 说明 |
|------|------|
| `base` / `base-devel` | 系统基础 + 编译工具链 |
| `linux` / `linux-firmware` / `intel-ucode` | 内核、固件、Intel 微码 |
| `grub` / `efibootmgr` / `dosfstools` / `mtools` | GRUB 引导 + EFI 工具 |
| `sudo` | 权限管理 |
| `vim` | 默认编辑器 |

**分区方案**: `/boot` (vfat), `/` (ext4), `swap`

---

## 二、桌面环境 (niri)

Wayland 纯滚动平铺窗口管理器，配合全手动组装的环境：

| 软件 | 作用 |
|------|------|
| `niri` | 滚动式平铺 Wayland 合成器 |
| `sddm` | 显示管理器（已启用 `systemctl enable sddm`） |
| `waybar` | 顶部状态栏 |
| `mako` | 通知守护进程（user service） |
| `swaybg` | 壁纸设置 |
| `swayidle` | 空闲管理（锁屏/休眠） |
| `swaylock` | 屏幕锁定 |
| `fuzzel` | 应用启动器 |
| `xwayland-satellite` | XWayland 兼容层 |
| `alacritty` | 终端模拟器 |
| `xdg-desktop-portal` / `xdg-desktop-portal-gtk` / `xdg-desktop-portal-wlr` | 桌面门户（屏幕录制/截图等） |

**自启服务（user）**: `niri.service`, `mako.service`

---

## 三、音频

| 软件 | 说明 |
|------|------|
| `pipewire` / `pipewire-audio` | 音频服务 |
| `pipewire-alsa` / `pipewire-pulse` | ALSA + PulseAudio 兼容层 |
| `wireplumber` | 会话管理 |
| `pamixer` | 命令行音量控制 |
| `pavucontrol` | 音量控制面板 |

**自启服务（user）**: `pipewire.service`, `pipewire-pulse.service`, `wireplumber.service`

---

## 四、中文输入法

| 软件 | 说明 |
|------|------|
| `fcitx5` | 输入法框架 |
| `fcitx5-chinese-addons` | 中文输入引擎 |
| `fcitx5-configtool` | GUI 配置工具 |
| `fcitx5-gtk` | GTK 支持 |

**自启服务（user）**: `app-org.fcitx.Fcitx5@autostart.service`

---

## 五、网络

| 软件 | 说明 |
|------|------|
| `NetworkManager` | 网络管理（系统服务） |
| `network-manager-applet` | 系统托盘图标（user 自启） |
| `wpa_supplicant` | Wi-Fi 认证 |

**系统服务**: `NetworkManager.service`, `wpa_supplicant.service`

**代理**: `http://127.0.0.1:7897` / `socks5://127.0.0.1:7897`（clash-verge-rev 代理客户端）

---

## 六、字体

| 软件 | 说明 |
|------|------|
| `noto-fonts` | Noto 基础字体 |
| `noto-fonts-cjk` | 中文/日文/韩文字体 |
| `noto-fonts-emoji` | Emoji 字体 |
| `ttf-nerd-fonts-symbols` | Nerd Font 图标 |
| `ttf-jetbrains-mono-nerd` | JetBrains Mono 编程字体（含图标） |

---

## 七、AUR 包

通过 `yay`（本机自编译安装的 AUR 助手）安装：

| 包名 | 说明 |
|------|------|
| `clash-verge-rev-bin` | Clash 代理客户端 GUI（v2.5.2） |
| `microsoft-edge-stable-bin` | Microsoft Edge 浏览器 |

---

## 八、文件管理

| 软件 | 说明 |
|------|------|
| `thunar` | 文件管理器 |
| `thunar-archive-plugin` | 归档插件 |
| `thunar-volman` | 卷管理插件 |
| `gvfs` | 虚拟文件系统（挂载、trash、网络） |
| `udisks2` | 磁盘管理（系统服务 + user 自启） |
| `tumbler` | 缩略图生成（user 自启） |

---

## 九、开发工具

| 软件 | 说明 |
|------|------|
| `git` | 版本控制 |
| `base-devel` | 编译工具链（gcc, make, patch 等） |
| `yay` | AUR 助手（源码编译安装） |
| `zed` | 代码编辑器（v1.12.1，pacman 官方源） |
| `opencode` | AI 编程助手（curl 安装脚本） |
| `go` | Go 语言工具链 |
| `nodejs` / `npm` | JavaScript 运行时 |

---

## 十、日常应用

| 软件 | 说明 |
|------|------|
| `firefox` | 浏览器 |
| `microsoft-edge-stable` | 浏览器 |
| `brightnessctl` | 屏幕/键盘背光控制 |
| `pavucontrol` | PulseAudio 音量控制面板 |
| `thunar` | 文件管理器 |

---

## 十一、工作区 / 项目

```
~/AAAPAN/
  └── Project/
      ├── Temp/          （yay 编译时的临时工作区）
      └── Note/          （git clone 自 github.com/adorukw/Note）
```

---

## 十二、Shell 与配置

- **Shell**: `bash`，配置 `~/.bashrc`（添加了 opencode 到 PATH、颜色别名、自定义 PS1）
- **系统配置**: `~/.config/niri/config.kdl`、`fcitx5/`、`gtk-3.0/`、`waybar/`（隐含）
- **环境变量**: `~/.config/environment.d/`（可能用于 fcitx5 等环境变量）

---

## 十三、自启服务汇总

### 系统服务 (system)
```
NetworkManager.service    网络管理
sddm.service              显示管理器
polkit.service            权限控制
wpa_supplicant.service    Wi-Fi
udisks2.service           磁盘管理
dbus-broker.service       D-Bus 实现
```

### 用户服务 (user)
```
niri.service              桌面合成器
mako.service              通知守护进程
pipewire.service          音频
pipewire-pulse.service    PulseAudio 兼容层
wireplumber.service       音频会话管理
gvfs-daemon.service       文件系统
gvfs-udisks2-volume-monitor  卷监控
tumblerd.service          缩略图生成
fcitx5                    输入法
network-manager-applet    网络托盘
xdg-desktop-portal.service    桌面门户
xdg-desktop-portal-gtk.service GTK 门户
```

---

## 十四、安装 / 配置时间线

| 顺序 | 操作 | 说明 |
|------|------|------|
| 1 | `base` + 引导 | pacstrap 安装基础系统、grub-install、locale 设为 `zh_CN.UTF-8` |
| 2 | `niri` | 安装 niri 合成器 |
| 3 | 桌面套件 | alacritty + fuzzel + mako + waybar + swaybg/swayidle/swaylock |
| 4 | `xwayland-satellite` | XWayland 兼容 |
| 5 | `pipewire` + `wireplumber` | 音频栈（user service enable） |
| 6 | `sddm` | 安装并启用显示管理器 |
| 7 | 字体 | Noto系列 + Nerd Font + JetBrains Mono |
| 8 | `fc-cache -fv` | 刷新字体缓存 |
| 9 | `thunar` + `gvfs` + `udisks2` | 文件管理器 |
| 10 | `firefox` / `brightnessctl` / `pamixer` / `pavucontrol` | 日用工具 |
| 11 | `git` + `base-devel` + `yay` | 编译安装 AUR 助手 |
| 12 | `clash-verge-rev-bin` | 代理客户端 |
| 13 | `microsoft-edge-stable-bin` | Edge 浏览器 |
| 14 | `zed` | 代码编辑器 |
| 15 | `opencode` | AI 编程助手 |
| 16 | `fcitx5` | 中文输入法 |
| 17 | 代理环境变量 | 写入 `export http_proxy=...` |

---

## 十五、总结

这是一个 **极简手动搭建的 Arch Linux 桌面**，未使用任何桌面环境发行版（如 KDE/GNOME），而是使用 **niri**（Wayland 滚动平铺合成器）配合独立组件拼装而成。特点：

- 全 Wayland 原生，无 X11 依赖（xwayland-satellite 作为兼容层）
- 使用 `fcitx5` 处理中文输入
- 通过 `clash-verge-rev` 管理网络代理
- 使用 `yay` 管理 AUR 包（Edge、Clash Verge）
- 以 `zed` 为主要编辑器，`opencode` 作为 AI 辅助
- 系统语言中文（`zh_CN.UTF-8`），总计 ~40 个显式安装的包
