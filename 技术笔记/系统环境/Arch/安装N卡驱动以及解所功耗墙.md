RTX 5060 属于 **Blackwell 架构**，在 Arch Linux 上需要安装 **`nvidia-open`** 驱动包（这是 NVIDIA 上游推荐用于 Blackwell 及更新架构的驱动）。

以下是完整的安装步骤：

---

## 1. 确认显卡被识别

```bash
lspci -k -d ::03xx
```

应该能看到类似 `NVIDIA Corporation GB206 [GeForce RTX 5060]` 的输出。

---

## 2. 安装 NVIDIA Open 驱动

根据你使用的内核选择对应的包：

```bash
# 如果你用的是标准 linux 内核（大多数情况）
sudo pacman -S nvidia-open nvidia-utils nvidia-settings

# 如果你用的是 linux-lts 内核
sudo pacman -S nvidia-open-lts nvidia-utils nvidia-settings

# 如果你用的是自定义内核（如 linux-zen、linux-hardened）
sudo pacman -S nvidia-open-dkms nvidia-utils nvidia-settings
```

同时安装 Wayland 支持：

```bash
sudo pacman -S egl-wayland
```

> ⚠️ **不要**从 NVIDIA 官网下载 `.run` 文件安装，pacman 安装才能与系统同步更新。

---

## 3. 配置 mkinitcpio（提前加载 NVIDIA 模块）

编辑 `/etc/mkinitcpio.conf`：

```bash
sudo nano /etc/mkinitcpio.conf
```

找到 `MODULES=()` 这一行，改为：

```bash
MODULES=(nvidia nvidia_modeset nvidia_uvm nvidia_drm)
```

找到 `HOOKS=()` 这一行，**删除其中的 `kms`**（如果存在），防止 initramfs 包含 nouveau 模块。

保存后重建 initramfs：

```bash
sudo mkinitcpio -P
```

---

## 4. 添加 Pacman Hook（驱动更新后自动重建 initramfs）

创建目录并写入 hook 文件：

```bash
sudo mkdir -p /etc/pacman.d/hooks
sudo tee /etc/pacman.d/hooks/nvidia.hook << 'EOF'
[Trigger]
Operation=Install
Operation=Upgrade
Operation=Remove
Type=Package
Target=nvidia-open
Target=nvidia-open-lts
Target=nvidia-open-dkms

[Action]
Description=Updating NVIDIA module in initcpio
Depends=mkinitcpio
When=PostTransaction
NeedsTargets
Exec=/bin/sh -c 'while read -r trg; do case $trg in linux*) exit 0; esac; done; /usr/bin/mkinitcpio -P'
EOF
```

---

## 5. 启用 DRM 内核模式设置（KDE Wayland 必需）

从 `nvidia-utils 560.35.03-5` 开始，DRM 默认已启用。验证一下：

```bash
cat /sys/module/nvidia_drm/parameters/modeset
```

输出应为 `Y`。如果是 `N`，手动启用：

```bash
sudo mkdir -p /etc/modprobe.d
echo 'options nvidia-drm modeset=1' | sudo tee /etc/modprobe.d/nvidia.conf
sudo mkinitcpio -P
```

---

## 6. 重启验证

```bash
sudo reboot
```

重启后验证驱动是否正常工作：

```bash
nvidia-smi
```

应该能看到 RTX 5060 的信息和驱动版本。

---

## 7. 32 位应用支持（可选，玩游戏需要）

如果你需要运行 Steam 或 32 位游戏，启用 multilib 仓库并安装 32 位库：

```bash
# 编辑 pacman.conf 启用 multilib
sudo nano /etc/pacman.conf
# 取消注释这两行：
# [multilib]
# Include = /etc/pacman.d/mirrorlist

sudo pacman -Sy
sudo pacman -S lib32-nvidia-utils
```

---

## 8. 常见问题

| 问题 | 解决 |
|------|------|
| 开机黑屏 | 确认 DRM `modeset=1` 已启用，且 `kms` 已从 HOOKS 中移除 |
| Wayland 下画面撕裂/闪烁 | 确保 `egl-wayland` 已安装，且 compositor 支持 Explicit Sync |
| 休眠后无法恢复 | 参考 Arch Wiki NVIDIA/Tips and tricks#Preserve video memory after suspend |

---

装完驱动后，你就可以继续安装 KDE Plasma 桌面了。需要我把 KDE + NVIDIA 的完整配置步骤也一起给你吗？


## 机械革命极光 X + Arch Linux RTX 5060 功耗墙解锁总结

### 🔴 问题现象
- `nvidia-smi` 显示显卡功耗锁在 **50W**，无法突破
- 游戏（CS2）帧数低、卡顿
- `Max Power Limit` 显示 **115W**，说明硬件支持更高功耗，但被软件限制

### 🔍 根本原因
**`nvidia-powerd` 服务未启用。**  
这是 NVIDIA 在笔记本上用于与 ACPI/EC 通信的守护进程，负责向固件请求解除功耗墙。服务未运行时，显卡会被 VBIOS/EC 锁死在默认的 50W 低功耗模式。

### ✅ 解决步骤

```bash
# 1. 启用并启动 nvidia-powerd 服务
sudo systemctl enable --now nvidia-powerd

# 2. 重启系统（必须）
sudo reboot

# 3. 验证功耗墙已解锁
nvidia-smi -q | grep "Current Power Limit"
# 应显示 115.00 W
```

### 🛠️ 额外优化

```bash
# 切换 PowerMizer 为最高性能模式（避免游戏时自动降频）
nvidia-settings -a '[gpu:0]/GPUPowerMizerMode=1'
nvidia-settings --save-config-only
```

### 📋 关键诊断命令回顾

| 命令 | 用途 |
|------|------|
| `nvidia-smi -q \| grep "Power Limit"` | 查看当前/最大功耗限制 |
| `nvidia-smi -q \| grep "SW Power Capping"` | 确认是否软件限功耗 |
| `systemctl status nvidia-powerd` | 检查服务状态 |
| `sudo nvidia-smi -pl 115` | 尝试手动改功耗墙（笔记本通常不支持） |

### 💡 核心结论
笔记本 NVIDIA 显卡在 Linux 下的功耗墙**不能通过 `nvidia-smi -pl` 手动修改**，必须由 `nvidia-powerd` 服务在启动时与 EC 协商解锁。启用该服务并重启即可解决。
