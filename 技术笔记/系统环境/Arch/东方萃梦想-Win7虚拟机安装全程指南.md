# 东方萃梦想（th075）Win7 虚拟机安装全程指南

> 适用环境：Arch Linux + KVM/QEMU + virt-manager，客户机为 Windows 7 x64
> 目标：在虚拟机上运行 2004 年的 DX8.1 格斗游戏《东方萃梦想》

---

## 一、背景：为什么用虚拟机

- 在 wine 11.14、Proton 7.0、全新 WinXP 前缀下，游戏均在 `SystemDataInit` 阶段崩溃（wined3d 日志显示 GL/纹理创建正常，崩溃点是游戏代码解引用 NULL，地址 0x5F7354）
- 网上查证：ProtonDB 上的 "platinum" 是 appid 1100160，实为《东方星莲船》（th12）——**东方萃梦想大概率没有 Steam 版**，不存在可靠的 Proton 报告
- 结论：放弃 wine/Proton，改用 **Win7 虚拟机**，这也是老游戏的稳妥路线

---

## 二、环境检查（先确认条件满足）

```bash
ls -l /dev/kvm                      # 存在 = KVM 可用（硬件虚拟化已开启）
grep -cE "vmx|svm" /proc/cpuinfo    # >0 = VT-x/AMD-V 已开启
free -h                             # 建议可用内存 ≥ 4G
df -h /home                         # 建议剩余磁盘 ≥ 30G
```

笔记本（核显 Intel UHD 770 + 独显 RTX 5060）不影响，虚拟机用虚拟显卡即可。

---

## 三、安装虚拟化软件

```bash
sudo pacman -S qemu-full libvirt virt-manager dnsmasq iptables-nft

# 把自己加入 libvirt 组（之后重新登录一次生效）
sudo usermod -aG libvirt $USER

# 启动 libvirtd 并设开机自启
sudo systemctl enable --now libvirtd

# 启用默认 NAT 网络（default，供虚拟机上网）
sudo virsh net-start default
sudo virsh net-autostart default
# 若提示网络未定义，先执行：
# sudo virsh net-define /etc/libvirt/qemu/networks/default.xml
```

> 提示：`virsh` 默认连的是用户会话 `qemu:///session`；virt-manager 建的 VM 在系统会话 `qemu:///system`，查看虚拟机用：
> ```bash
> virsh -c qemu:///system list --all
> ```

---

## 四、virt-manager 创建 Win7 虚拟机

1. 打开 virt-manager → **文件 → 新建虚拟机** → 本地安装介质 → 选择你的 Win7 ISO
2. 操作系统类型：**Windows 7**
3. 内存 **4096 MB**，CPU **4** 核
4. 磁盘 **30 GB**（qcow2）
5. 勾选 **"安装前自定义配置"**，然后按下面设置：

| 项目 | 设置 | 说明 |
|---|---|---|
| 固件 | BIOS（SeaBIOS） | 不要用 UEFI/OVMF，Win7 x64 装 UEFI 麻烦 |
| CPU | host-passthrough | 性能最好 |
| 磁盘总线 | SATA | virtio 盘在 Win7 安装时无驱动，避免麻烦 |
| 显卡 | QXL（先） | 免驱，先装系统；3D 测试见第九节 |
| 声卡 | ich9-intel-hda | Win7 自带驱动，DirectSound 可用 |
| 网络 | e1000e | 免驱；配好后走 default NAT（宿主网关 192.168.122.1） |

6. 启动安装 Win7（选纯净安装）。装完桌面出现后，VM 能通过 NAT 上网。

---

## 五、安装 virtio-win 驱动（为 3D 做准备）

- 下载驱动盘（约 600MB，在**宿主**上下载）：
  `https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/stable-virtio/virtio-win.iso`
- virt-manager 里给 VM **添加硬件 → 存储 → CD/DVD 设备**，挂载该 ISO
- Win7 里运行安装 `virtio-win-guest-tools.exe`（含 spice 工具、vioserial、GPU 驱动等），重启

---

## 六、把游戏文件传进虚拟机（按推荐顺序）

### 方法 1：USB 重定向（usbredir，零安装，最快）

1. 游戏文件夹拷进 U 盘，插到宿主
2. virt-manager Win7 窗口工具栏点 **USB 重定向**按钮（或 虚拟机 → USB 设备选择器），选中 U 盘
3. Win7 里出现该 U 盘，直接拷文件

**Win7 提示"无法识别的 USB 设备"时的排查顺序：**

1. 宿主文件管理器里右键 U 盘 → **弹出/卸载**（不拔），断开重定向再重新连接 —— 最常见原因：U 盘被宿主自动挂载，设备状态损坏
2. 重启 Win7 虚拟机再重定向（USB3.0 优盘热插枚举失败常见）
3. Win7 设备管理器 → 找到带感叹号的"未知设备" → 右键 **卸载设备** → 断开重定向再重连，强制重新枚举
4. 改用**直通**（更稳定）：先卸载宿主侧 U 盘 → **查看 → 详情 → 添加硬件 → USB 主机设备** → 选中 U 盘 → 完成
5. 换一个 U 盘或读卡器+SD 卡（部分主控在虚拟 USB2.0 口上就是枚举失败）
6. 如果出现了盘符但打不开提示"需要格式化"：文件系统不被 Win7 识别，备份后格式化为 FAT32 再试

### 方法 2：Samba 共享（最稳、可长期用）

```bash
sudo pacman -S samba
mkdir -p ~/GameShare
# 编辑 /etc/samba/smb.conf，追加：
#   [gameshare]
#   path = /home/你的用户名/GameShare
#   browseable = yes
#   read only = no
#   guest ok = yes
#   map to guest = Bad User
sudo systemctl enable --now smbd
```

Win7 里 **Win+R** 输入 `\\192.168.122.1`，打开共享拷文件（NAT 网络下 192.168.122.1 就是宿主）。

### 方法 3：SPICE 拖放（装好 spice 工具后最方便）

- guest 有网，Win7 浏览器直接打开 `https://www.spice-space.org/download/windows/spice-guest-tools/` 下载安装（打不开就用方法 1/2 把 exe 拷进去）
- 装完重启，即可把文件/文件夹**直接从宿主拖进 Win7 窗口**

### 方法 4：WebDAV 共享文件夹（不推荐，步骤最多）

- 宿主官方源无该包，需 AUR：`yay -S spice-webdavd`
- virt-manager 添加 **Spice webdav** 通道（`org.spice-space.webdav.0`）+ guest 装 spice-webdavd 客户端

> **重要：游戏最终要放在 Win7 的 C 盘（可写磁盘）里运行**，不要直接在共享盘上跑——萃梦想存档 `score.dat` 写在游戏自己的目录里。

---

## 七、运行游戏

1. 把游戏文件夹整个拷到 `C:\` 下（如 `C:\th075`）
2. `th075.exe` 右键 → 属性 → **兼容性**：
   - 以兼容模式运行：**Windows XP (Service Pack 3)**
   - 勾选"以管理员身份运行"
   - 仍闪退则再加勾"**640 × 480 分辨率下运行**"
3. 用 `config.exe` 设 **640x480 窗口模式**（最稳），按自己习惯配置按键
4. 存档：沿用原来的 `score.dat`（拷进游戏目录即可）

---

## 八、3D 加速（关键一步）

萃梦想是 **DX8.1 + 像素着色器** 游戏，Win7 默认基本显示适配器**没有任何 3D 能力**，必须给虚拟机提供虚拟 3D：

1. 装好 virtio-win 后：关机 → 显卡从 **QXL 换成 virtio-vga** → 开机 → 设备管理器给显示适配器装 virtio-win 的 GPU 驱动（viogpudo）
2. 跑游戏验证：能正常进 3D 就成功
3. 如果 virtio-gpu 在 Win7 下驱动装不上/游戏黑屏 → **改用 VirtualBox**（Guest Additions 对 Win7 的 D3D 兼容性最成熟）：

```bash
sudo pacman -S virtualbox virtualbox-host-modules-arch
sudo modprobe vboxdrv
# 建 Win7 VM：设置 → 显示 → 显存 128MB + 启用 3D 加速 → 装增强功能
```

---

## 九、注意事项

- **不要尝试独显直通**：RTX 5060（Blackwell）**没有 Win7 驱动**（NVIDIA 2021 年已停止 Win7/8.1 支持），直通成功 Win7 也认不出卡。核显同理（Intel 不给 12 代核显出 Win7 驱动）。直通只对 Win10/11 VM 有意义
- **Win7 已停止安全支持**：虚拟机内**不要联网、不要放重要数据**
- 你的镜像为第三方修改版（ITHaoge 2025），一般带驱动注入和更新，可用但注意来源安全
- 手柄：virt-manager **添加硬件 → USB 主机设备** 直通；或 USB 重定向（游戏手柄一般枚举正常）
- 性能：2004 年的游戏，2-4 核 + 4G 内存 + 虚拟 3D 轻松 60fps

---

## 十、常用命令速查

```bash
virsh -c qemu:///system list --all        # 查看系统会话的虚拟机
virsh -c qemu:///system net-list --all    # 查看网络（default NAT 是否活动）
sudo systemctl status libvirtd            # 检查 libvirtd
```
