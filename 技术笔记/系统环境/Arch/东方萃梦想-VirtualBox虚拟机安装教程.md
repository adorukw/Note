# 东方萃梦想 VirtualBox 虚拟机安装教程（Arch Linux）

> 目标：用 VirtualBox + Windows 7 虚拟机运行 2004 年 DX8.1 游戏《东方萃梦想》
> 配套文档：`东方萃梦想-Win7虚拟机安装全程指南.md`（KVM 方案与游戏设置详见那边）

---

## 一、为什么改用 VirtualBox

KVM 方案的 virtio-gpu 3D 路径在 Win7 上走不通：

- GL 版 virtio 显卡（virtio-vga-gl）要求显示后端开 `gl=on`，且宿主（NVIDIA + Wayland）的 EGL 渲染有已知问题
- virtio-win 的 GPU 驱动（viogpudo）主要面向 Win10/11，Win7 无可用 3D 驱动
- 萃梦想必须要像素着色器（无 3D 设备时 DirectGraphicsInit 会失败）

**VirtualBox 增强功能（Guest Additions）自带 Win7 的 D3D 驱动，对 DX8/DX9 老游戏兼容性最成熟**，这是 Win7 虚拟机跑 3D 游戏的最稳路径。

---

## 二、安装 VirtualBox（适配新内核）

> 注意：Arch 已不再发布预编译内核模块（`virtualbox-host-modules-arch` 不存在是正常的），官方只提供 DKMS 版，DKMS 会针对你当前内核现场编译，所以**新内核（如 7.1.6-arch1-1）也能直接用**。

```bash
sudo pacman -Syu                                        # 先更新，保证内核和头文件同版本
sudo pacman -S virtualbox virtualbox-host-dkms linux-headers
sudo modprobe vboxdrx                                   # 加载模块
modinfo vboxdrv | grep vermagic                         # 验证：应显示 7.1.6-arch1-1（与 uname -r 一致）
sudo usermod -aG vboxusers $USER                        # 加入用户组，重新登录一次生效
```

可选：安装扩展包（USB 2.0/3.0、RDP 支持）：

```bash
yay -S virtualbox-ext-oracle
```

> 兜底方案：若 DKMS 编译报错（VBox 上游未适配新内核），安装 LTS 内核并用它进系统：
> ```bash
> sudo pacman -S linux-lts linux-lts-headers
> ```
> 重启后在 GRUB 引导菜单选 LTS 内核，VBox 在 LTS 上非常稳。

---

## 三、安装报错处理（镜像下载超时）

**现象**：`pacman -S` 下载到一半报 `无法获取某些文件` / `Operation too slow`（如阿里云 .sig 文件超时），整个事务中止，出现"没有软件包被更新"。此时 `modprobe vboxdrv` 报 not found 是正常的——包根本没装上，与内核无关。

**解决**：

1. 直接重试安装（已下载的包在缓存里会复用）：
   ```bash
   sudo pacman -S virtualbox virtualbox-host-dkms linux-headers
   ```
2. 反复失败则换镜像源：编辑 `/etc/pacman.conf`，把 Server 行换成：
   ```
   Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
   ```
3. （可选）网络慢但稳定时，在 `/etc/pacman.conf` 的 `[options]` 区加 `DisableDownloadTimeout`，避免慢速下载被超时中断。

---

## 四、新建 Win7 虚拟机

打开 VirtualBox → **新建**：

1. 名称随意（如 Win7），类型 **Microsoft Windows**，版本 **Windows 7 (64-bit)**
2. 内存：**4096 MB**
3. 硬盘：现在创建虚拟硬盘 → **VDI** → 动态分配 → **40 GB**
4. 创建后进 **设置**：
   - **系统 → 处理器**：4 核
   - **显示 → 显存拉到 128 MB**，**勾选"启用 3D 加速"**（关键）
   - **存储**：点光驱图标 → 选择磁盘文件 → 选你的 Win7 ISO
   - **网络 → 连接方式：NAT**（guest 可上网）
   - **声音**：默认控制器即可，Win7 自带驱动

---

## 五、安装 Win7

点**启动** → 从光驱引导 → 正常安装（选纯净安装）。装完能进桌面即可。

---

## 六、安装增强功能（Guest Additions）——3D 全靠这一步

1. 虚拟机菜单：**设备 → 安装增强功能**
2. Win7 里打开光驱，运行 **VBoxWindowsAdditions-amd64.exe**（右键 → 以管理员身份运行），装完重启
3. 验证：Win7 **设备管理器 → 显示适配器**应显示 **"VirtualBox Graphics Adapter (WDDM)"**（而不是"标准 VGA"）

增强功能一并带来：拖放、共享文件夹、剪贴板双向、USB 支持。

---

## 七、传文件进 Win7

- **拖放**（最方便）：设备 → **拖放 → 双向** → 直接把文件/文件夹从宿主导航窗口拖进 Win7 窗口
- **共享文件夹**（大文件更稳）：设备 → **共享文件夹 → 共享文件夹设置 → 添加** → 选宿主目录 → 勾自动挂载 → Win7 里打开 `\\VBOXSVR\共享名`
- **USB**：设备 → USB → 选你的 U 盘（需已装扩展包）

---

## 八、运行游戏

1. 游戏文件夹拷到 **C:\** 下（如 `C:\th075`），**别放共享盘里跑**（存档 `score.dat` 写在游戏目录）
2. `th075.exe` 右键 → 属性 → **兼容性**：
   - 以兼容模式运行 → **Windows XP (Service Pack 3)**
   - 勾"以管理员身份运行"
   - 仍闪退再加勾"**640 × 480 分辨率下运行**"
3. 用 `config.exe` 设 **640x480 窗口模式**，配置按键

---

## 九、常见问题

- **启动报 kernel module 错误**：`sudo modprobe vboxdrv`；内核升级后要重启才用上新模块（DKMS 会自动为新内核重编）
- **3D 加速下黑屏/花屏**：设置 → 显示 → 关掉再重新打开"启用 3D 加速" → 重启 VM；或重装一遍增强功能；宿主是 NVIDIA + Wayland 时渲染异常可尝试在 **X11 会话**里运行 VirtualBox（登录界面选 X11 会话）
- **增强功能安装失败**：右键安装包 → 以管理员身份运行
- **手柄**：设备 → USB → 选你的手柄（需扩展包）
