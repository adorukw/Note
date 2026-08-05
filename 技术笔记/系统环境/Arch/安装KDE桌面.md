以下是 Arch Linux 上安装 **KDE Plasma 6** 桌面环境的完整最新步骤（基于 Arch Wiki，Plasma 6 为当前主流版本）。

---

## 1. 基础系统准备

确保你的 Arch 基础系统已联网，并更新了软件包数据库：

```bash
sudo pacman -Syu
```

---

## 2. 安装显卡驱动（关键前置步骤）

根据你的显卡选择对应的驱动，这是避免后续黑屏/卡顿的关键：

| 显卡 | 开源驱动 | 闭源驱动 |
|------|---------|---------|
| Intel | `xf86-video-intel`（较老）或内置 `modesetting` | — |
| AMD | `xf86-video-amdgpu` | — |
| NVIDIA | `nouveau`（不推荐） | `nvidia` / `nvidia-lts` |

**NVIDIA 用户特别注意**：必须启用 DRM 内核模式设置：

```bash
sudo mkdir -p /etc/modprobe.d
echo 'options nvidia-drm modeset=1' | sudo tee /etc/modprobe.d/nvidia.conf
```

---

## 3. 安装 KDE Plasma 桌面

有三种安装方式，推荐第一种：

### 方式一：完整安装（推荐）

```bash
sudo pacman -S plasma-meta
```

这会安装 Plasma 桌面 + 所有官方组件。

### 方式二：安装 plasma 软件包组

```bash
sudo pacman -S plasma
```

与 `plasma-meta` 类似，但组内包更新时可能需要手动处理。

### 方式三：最小安装（仅桌面核心）

```bash
sudo pacman -S plasma-desktop
```

只安装最基本的桌面，适合高级用户自行搭配组件。

---

## 4. 安装显示管理器（登录界面）

**SDDM** 是 KDE 官方推荐的显示管理器：

```bash
sudo pacman -S sddm
sudo systemctl enable sddm.service
```

> **注意**：Arch Wiki 提到 Plasma Login Manager 正在成为首选显示管理器，但目前 SDDM 仍是主流选择。

---

## 5. 安装 KDE 应用程序（可选但推荐）

安装完整的 KDE 应用套件：

```bash
sudo pacman -S kde-applications-meta
```

或只安装特定类别的应用（如仅安装文件管理器）：

```bash
sudo pacman -S dolphin konsole kate
```

---

## 6. 安装必要的附加组件

```bash
# 网络管理（必需，用于系统托盘网络图标）
sudo pacman -S networkmanager
sudo systemctl enable NetworkManager.service

# 音频支持（PulseAudio / PipeWire）
sudo pacman -S pipewire pipewire-pulse wireplumber

# 蓝牙支持
sudo pacman -S bluez bluez-utils
sudo systemctl enable bluetooth.service

# 电源管理
sudo pacman -S powerdevil

# 显示配置（多显示器支持）
sudo pacman -S kscreen

# GTK 应用主题统一（让 GTK 应用在 KDE 下不突兀）
sudo pacman -S breeze-gtk kde-gtk-config

# 缩略图支持（Dolphin 文件预览）
sudo pacman -S kdegraphics-thumbnailers ffmpegthumbs
```

---

## 7. 启动进入 KDE

完成上述安装后，重启系统：

```bash
reboot
```

重启后 SDDM 登录界面会出现，选择会话类型：

- **Plasma (Wayland)** ← **默认推荐**（Plasma 6.4+ 的默认选项）
- **Plasma (X11)** ← 如需 X11，需额外安装 `plasma-x11-session`

> ⚠️ X11 会话将在 Plasma 6.8 中移除，Wayland 是未来的主流。

---

## 8. 安装后推荐配置

### 8.1 中文字体与输入法

```bash
# 中文字体
sudo pacman -S noto-fonts-cjk noto-fonts-emoji

# Fcitx5 输入法（推荐）
sudo pacman -S fcitx5 fcitx5-configtool fcitx5-gtk fcitx5-qt fcitx5-chinese-addons
```

然后在 `~/.config/environment.d/im.conf` 添加：

```bash
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
```

### 8.2 浏览器集成

```bash
sudo pacman -S plasma-browser-integration
```

然后在浏览器中安装对应的 Plasma 扩展，可实现媒体控制、下载通知等功能。

### 8.3 文件搜索（Baloo）

KDE 的桌面搜索默认启用，如需管理：

```bash
# 查看索引状态
balooctl status

# 暂停/恢复索引
balooctl suspend
balooctl resume
```

---

## 9. 可选：从命令行直接启动（无显示管理器）

如果你不想用 SDDM，可以在 TTY 登录后手动启动：

**Wayland 会话：**
```bash
/usr/lib/plasma-dbus-run-session-if-needed /usr/bin/startplasma-wayland
```

**X11 会话（需安装 `plasma-x11-session`）：**
```bash
startx /usr/bin/startplasma-x11
```

---

## 10. 常见问题速查

| 问题 | 解决 |
|------|------|
| 登录后黑屏 | 检查显卡驱动是否正确安装 |
| 没有网络图标 | 确认 `NetworkManager` 已启用并安装 `plasma-nm` |
| 没有声音 | 确认 PipeWire 服务运行：`systemctl --user status pipewire` |
| GTK 应用外观不一致 | 在**系统设置 > 颜色与主题 > 应用程序风格 > 配置 GNOME/GTK 应用程序风格**中选择 Breeze |
| 图标缺失 | 去**系统设置 > 颜色与主题 > 图标**重新选择主题 |

---

如果你告诉我你的显卡型号（NVIDIA/AMD/Intel），我可以给你更精确的驱动安装命令。
