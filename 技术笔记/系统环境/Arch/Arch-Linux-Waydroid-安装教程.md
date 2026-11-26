# Arch Linux 安装 Waydroid 完整教程

## 前提条件检查

### CPU
```bash
cat /proc/cpuinfo
```
x86_64 架构即可，需要支持 SSE2 等基础指令集。

### GPU
Waydroid 对不同 GPU 的支持情况：
- **Intel 集显**：最佳支持，开箱即用
- **AMD GPU**：已支持，部分型号需要重建映像
- **NVIDIA GPU**：不直接支持，需要安装 `waydroid-nvidia-bin` 专用分支

### Wayland 会话
Waydroid 只能在 Wayland 会话中运行：
```bash
echo $XDG_SESSION_TYPE  # 应该输出 wayland
```

### 内核模块（loop）
Waydroid 需要 `loop` 内核模块来挂载系统映像：
```bash
# 检查模块是否存在
lsmod | grep loop

# 如果没有，手动加载
sudo modprobe loop
```

**问题1：loop 模块找不到**

如果 `modprobe loop` 报错 `Module not found`，说明内核安装不完整：
```bash
sudo pacman -S linux
sudo mkinitcpio -P
sudo reboot
```

**让 loop 模块开机自动加载：**
```bash
echo "loop" | sudo tee /etc/modules-load.d/loop.conf
```

---

## 安装步骤

### 1. 安装 Waydroid

```bash
sudo pacman -S waydroid
```

> `waydroid` 在 Arch 官方仓库 `extra` 中，直接用 `pacman` 安装即可，不需要 `yay`。
> 只有 AUR 中的辅助包（如 `waydroid-image`、`waydroid-helper` 等）才需要 `yay`。

### 2. 初始化 Waydroid

**方式A：让 Waydroid 自动下载映像（推荐）**
```bash
sudo waydroid init -s GAPPS
```
- `-s GAPPS` 表示安装带 Google 服务的版本（Google Play、Gmail 等）
- 不加 `-s GAPPS` 就是纯净版 Android

**方式B：通过 archlinuxcn 仓库加速下载（中国用户）**

编辑 `/etc/pacman.conf`，在末尾添加：
```
[archlinuxcn]
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```
```bash
sudo pacman -S archlinuxcn-keyring
sudo pacman -Syu
sudo pacman -S waydroid-image-gapps
```

### 3. 启动服务

```bash
sudo systemctl enable --now waydroid-container
```

### 4. 启动 Waydroid

```bash
waydroid session start
waydroid show-full-ui
```

---

## 常见问题及解决方案

### 问题2：SourceForge 下载映像失败

**现象：** `waydroid init` 下载中断，hash 校验失败。

**原因：** SourceForge 的重定向机制对终端下载工具支持不好，浏览器可以正常下载。

**解决方案：**
- 用浏览器下载映像，解压后放到 `/etc/waydroid-extra/images/`
- 或者设置代理：`sudo HTTPS_PROXY="http://127.0.0.1:7897/" waydroid init -s GAPPS`

### 问题3：手动下载的映像版本不匹配

**现象：** `mount` 失败，报 `failed to set up loop device`。

**原因：** system.img 和 vendor.img 来自不同版本，不兼容。

**解决方案：** 不要手动下载，让 Waydroid 自己处理映像：
```bash
sudo rm -rf /etc/waydroid-extra/images/*
sudo waydroid init -s GAPPS
```

### 问题4：mount -o ro 失败

**现象：** `failed to set up loop device for system.img`

**原因：** `loop` 内核模块未加载。

**解决方案：**
```bash
sudo modprobe loop
```

### 问题5：graphics.composer 和 surfaceflinger 崩溃

**现象：** composer@2.1-service 和 surfaceflinger 反复 ABRT 崩溃。

**原因：** NVIDIA GPU 被用于渲染，但 Waydroid 原版不支持 NVIDIA。

**解决方案（二选一）：**

**方案A：强制使用 Intel 集显**
```bash
# 创建环境变量配置
mkdir -p ~/.config/environment.d
cat > ~/.config/environment.d/gpu.conf << 'EOF'
MESA_LOADER_DRIVER_OVERRIDE=iris
EOF
```
注销重新登录后生效。

> 注意：环境变量可能无法传入容器内部，如果此方法无效请用方案B。

**方案B：安装 NVIDIA 专用分支（推荐）**
```bash
yay -S waydroid-nvidia-bin
sudo waydroid-nvidia-setup
sudo systemctl enable --now waydroid-container.service
systemctl --user enable --now wd-venus.service
waydroid session start
waydroid show-full-ui
```

### 问题6：sudo 启动 waydroid 报 DBUS 错误

**现象：** `Using X11 for dbus-daemon autolaunch was disabled at compile time`

**原因：** `sudo` 会清除环境变量，包括 `DBUS_SESSION_BUS_ADDRESS`。

**解决方案：** 不要用 `sudo` 启动 waydroid session。

### 问题7：Waydroid 内部无法上网

**现象：** Waydroid 会话启动正常，但容器内无法访问互联网。

**原因：** 缺少 NAT 转发规则，容器内网（192.168.240.0/24）无法通过宿主机访问外网。

**解决方案：**

**1. 安装 iptables-nft：**
```bash
sudo pacman -S iptables-nft
sudo systemctl enable --now iptables
```

**2. 添加 NAT 转发规则：**
```bash
sudo iptables -t nat -A POSTROUTING -s 192.168.240.0/24 ! -o waydroid0 -j MASQUERADE
```

**3. 验证规则是否生效：**
```bash
sudo iptables -t nat -L POSTROUTING -n | grep MASQUERADE
# 应输出：MASQUERADE  all  --  192.168.240.0/24     0.0.0.0/0
```

**4. 永久保存规则（开机自动生效）：**
```bash
sudo mkdir -p /etc/iptables
echo "*nat" | sudo tee /etc/iptables/iptables.rules
echo ":PREROUTING ACCEPT [0:0]" | sudo tee -a /etc/iptables/iptables.rules
echo ":INPUT ACCEPT [0:0]" | sudo tee -a /etc/iptables/iptables.rules
echo ":OUTPUT ACCEPT [0:0]" | sudo tee -a /etc/iptables/iptables.rules
echo ":POSTROUTING ACCEPT [0:0]" | sudo tee -a /etc/iptables/iptables.rules
echo "-A POSTROUTING -s 192.168.240.0/24 ! -o waydroid0 -j MASQUERADE" | sudo tee -a /etc/iptables/iptables.rules
echo "COMMIT" | sudo tee -a /etc/iptables/iptables.rules
sudo systemctl restart iptables
```

**5. 重启 Waydroid 使网络生效：**
```bash
waydroid session stop
waydroid session start
```

---

## 可选配置

### 开机自动打开 Waydroid

```bash
mkdir -p ~/.config/autostart
cat > ~/.config/autostart/waydroid.desktop << 'EOF'
[Desktop Entry]
Type=Application
Name=Waydroid
Exec=waydroid show-full-ui
EOF
```

### 安装 ARM 翻译层

在 x86 Android 中运行 ARM 应用需要翻译层。雷电模拟器等自带翻译层，Waydroid 需要手动安装。

**1. 安装 waydroid-script 工具：**
```bash
yay -S waydroid-script-git
```

**2. 安装翻译层（Intel CPU 推荐 libhoudini，AMD CPU 推荐 libndk）：**
```bash
sudo python /opt/waydroid-script/main.py install libhoudini
# 或
sudo python /opt/waydroid-script/main.py install libndk
```

> 注意：命令是 `python /opt/waydroid-script/main.py`，不是 `waydroid-script`。
> 参数直接跟包名，**没有** `-n`。

**3. 验证翻译层是否安装成功：**
```bash
sudo python /opt/waydroid-script/main.py --help
```

---

## 常见问题及解决方案（续）

### 问题8：ARM APK 安装失败

**现象：** `waydroid app install xxx.apk` 执行后无反应，应用列表中没有出现。

**原因：** APK 是 ARM 架构（如 `arm64`），而 Waydroid 是 x86_64 架构，无法直接运行。

**解决方案：** 安装 ARM 翻译层（见上方「安装 ARM 翻译层」），然后重新安装 APK：
```bash
waydroid app install ./your-app.apk
```

### 问题9：waydroid-script 命令找不到

**现象：** `waydroid-script` 或 `waydroid-script android_env install` 报 `找不到命令`。

**原因：** waydroid-script-git 安装路径在 `/opt/waydroid-script/`，没有在 `/usr/bin/` 创建链接。

**解决方案：** 使用完整路径运行：
```bash
sudo python /opt/waydroid-script/main.py install libhoudini
```

---

## 系统信息参考

本教程基于以下环境编写：
- **CPU：** Intel Core i7-13700HX (13th Gen)
- **GPU：** Intel UHD Graphics 770 + NVIDIA GeForce RTX 5060 Max-Q
- **内核：** Linux 7.1.9-arch1-2
- **桌面环境：** KDE Plasma (Wayland)
- **发行版：** Arch Linux
