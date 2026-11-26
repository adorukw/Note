# 水星 UX3 免驱版 (AIC8800DC) Arch Linux 驱动安装记录与使用指南

> 文档日期：2026-09-20
> 系统环境：Arch Linux，内核 `7.2.6-arch2-1`，GCC 16.2.1，DKMS 3.4.3-2
> 适用设备：水星（MERCURY）UX3 免驱版 USB 无线网卡，芯片 AIC8800DC

---

## 一、问题背景

在 Arch Linux 上使用水星 UX3 免驱版 USB 网卡，官方只提供了国产操作系统（麒麟/UOS）的 `.deb` 驱动包：

```
UX3(免驱版) V1.0 Linux系统驱动程序20250118/
├── aic8800fdrvpackage.deb            # 驱动包（源码 + 固件 + udev 规则）
├── 国产操作系统水星网卡驱动安装指导.pdf
└── 国产操作系统网卡驱动安装常见问题.pdf
```

**问题：Arch Linux 不能使用这个 deb 包**，原因有二：

1. Arch 使用 pacman，没有 dpkg/apt，无法直接安装 deb；
2. deb 内附带的驱动源码（2024-12 版本）在 Arch 当前内核 7.2.6 上**无法编译**。

本文记录了完整的诊断过程、最终可用的安装方案、以及日常使用与维护说明。

---

## 二、诊断过程

### 2.1 设备识别

插入网卡后 `lsusb` 显示：

```
Bus 003 Device 007: ID a69c:5721 aicsemi Aic MSC
```

这是 AIC 芯片的 **免驱模式（MSC，U 盘模式）**：设备先以 U 盘（只读 CD/Flash）形式出现，里面放着 Windows 驱动。需要向它发送 `eject`（弹出）命令，设备才会重新枚举为 **WiFi 模式**：

```
Bus 003 Device 008: ID 2357:0147 TP-Link AIC8800DC
```

> 注意：实际切换到 WiFi 模式后 VID:PID 是 `2357:0147`（TP-Link 标识），而不是某些资料所说的 `a69c:88dc`。选驱动时两个 ID 都要支持。

### 2.2 deb 包内容分析

使用 `ar` 解包（无需 dpkg）：

```bash
ar x aic8800fdrvpackage.deb
# 得到 debian-binary、control.tar.gz、data.tar.gz
tar xzf data.tar.gz
```

包内结构：

```
AIC8800/
├── aic.rules              # udev 规则（自动 eject 切换模式）
├── fw/aic8800DC/          # 固件（15 个文件）
├── drivers/aic8800/       # 驱动源码（aic_load_fw + aic8800_fdrv）
└── aicrf_test/            # 射频测试工具（不需要）
```

其中 `aic.rules` 内容为：

```
KERNEL=="sd*", ATTRS{idVendor}=="a69c", ATTRS{idProduct}=="5721", SYMLINK+="aicudisk", RUN+="/usr/bin/eject /dev/%k"
```

### 2.3 为什么 deb 里的驱动源码不能用

debian 包的 `postinst` 脚本流程是：复制固件 → `make` 编译 → `make install` → `insmod`。但在内核 7.2.6 + GCC 16 下实测编译失败，错误示例：

| 错误 | 原因 |
|---|---|
| `implicit declaration of function 'from_timer'` | 内核 6.15+ 已移除 `from_timer`，改用 `timer_container_of` |
| `implicit declaration of function 'del_timer_sync'` | 已改用 `timer_delete_sync` |
| `too many arguments to function 'cfg80211_ch_switch_notify'` | cfg80211 API 签名变化 |
| `too few arguments to function 'cfg80211_cac_event'` | cfg80211 API 新增参数 |
| `implicit declaration of 'wakeup_source_create/add/remove'` | 已移除，改用 `wakeup_source_register` |
| `implicit declaration of function 'in_irq'` | 已移除 |
| `'VFS_internal_I_am_really_a_filesystem...'` 未声明 | 旧版本 BUG 宏技巧已被移除 |
| `cfg80211_new_sta/del_sta` 参数类型不兼容 | cfg80211 回调签名变化 |
| `rwnx_tdls.c: 'struct <匿名>' 没有名为 'u' 的成员` | 结构体定义变化 |

共约 20 处错误，属于典型的**旧厂商驱动跟不上新内核**问题，手工修补工作量大且容易遗漏，不推荐。

### 2.4 AUR 方案考察

AUR 中与 AIC8800 相关的包有 6 个，其中较相关的是：

| 包名 | 说明 | 结论 |
|---|---|---|
| `aic8800-linux7-dkms` | 明确支持 kernel 7.x（上游 [precomp666/aic8800-driver](https://github.com/precomp666/aic8800-driver)） | **源码可用**，但 AUR 包本身有坑 |
| `aic8800-dkms` | BrosTrend AX300 版，已被标记 OutOfDate | 不推荐 |
| `aic8800d80-dkms` 等 | 主要针对 8800D80，芯片不符 | 不适用 |

`aic8800-linux7-dkms` 的两个坑（实测）：

1. **prepare() 必然失败**：它自带的 `kernel-7.x.patch` 已经被上游合并进源码，`patch` 报 "Reversed (or previously applied) patch detected"，makepkg 直接中止；
2. **包里没有固件**：固件目录来自 git 子模块，而 makepkg 下载的 GitHub tarball 不含子模块，就算装上也连不上网。

**但它的上游源码本身是好的**：实测在你的机器上（内核 7.2.6 + GCC 16）可以完整、干净地编译通过，`vermagic` 完全匹配，并且同时支持 `a69c:88dc` 和 `2357:0147`。

---

## 三、最终方案

采用组合方案：

| 组件 | 来源 | 作用 |
|---|---|---|
| 驱动源码 | precomp666/aic8800-driver（AUR 上游） | 支持内核 7.x 的 AIC8800 驱动 |
| 固件 | 官方 deb 包中的 `fw/aic8800DC` | 设备启动所需的 15 个固件文件 |
| udev 规则 | 官方 deb 包中的 `aic.rules` | 插入后自动 eject 切换到 WiFi 模式 |
| 安装方式 | **DKMS** | 内核升级后自动重新编译，不会掉驱动 |

---

## 四、详细安装步骤（已实际执行并验证成功）

以下命令可直接复制执行。

### 步骤 1：提取固件和 udev 规则

```bash
cd /tmp
ar x "/home/adorukw/Downloads/UX3(免驱版) V1.0 Linux系统驱动程序20250118/aic8800fdrvpackage.deb"
tar xzf data.tar.gz AIC8800/fw/aic8800DC AIC8800/aic.rules
sudo cp -r AIC8800/fw/aic8800DC /lib/firmware/
sudo install -Dm644 AIC8800/aic.rules /etc/udev/rules.d/aic.rules
sudo udevadm control --reload && sudo udevadm trigger
```

### 步骤 2：下载适配 kernel 7.x 的驱动源码

```bash
curl -L -o /tmp/aic8800.tar.gz \
  https://codeload.github.com/precomp666/aic8800-driver/tar.gz/refs/heads/main

rm -rf /tmp/aic-prep && mkdir -p /tmp/aic-prep
tar xzf /tmp/aic8800.tar.gz -C /tmp/aic-prep --strip-components=1
cd /tmp/aic-prep
```

清理源码包里的作者预编译产物和 macOS 垃圾文件（不清理可能导致 DKMS 使用陈旧目标文件）：

```bash
find . \( -name '*.o' -o -name '.*.o.cmd' -o -name '*.ko' \
  -o -name '*.mod' -o -name '*.mod.c' -o -name '*.mod.o' \
  -o -name 'modules.order' -o -name 'Module.symvers' \
  -o -name '._*' -o -name '.DS_Store' \) -delete
```

### 步骤 3：放入 DKMS

```bash
sudo rm -rf /usr/src/aic8800-1.0.9 && sudo mkdir -p /usr/src/aic8800-1.0.9
sudo cp -r aic8800_fdrv aic_load_fw Makefile Kconfig /usr/src/aic8800-1.0.9/

sudo tee /usr/src/aic8800-1.0.9/dkms.conf >/dev/null <<'EOF'
PACKAGE_NAME="aic8800"
PACKAGE_VERSION="1.0.9"
BUILT_MODULE_NAME[0]="aic8800_fdrv"
BUILT_MODULE_LOCATION[0]="aic8800_fdrv/"
DEST_MODULE_LOCATION[0]="/extramodules/"
BUILT_MODULE_NAME[1]="aic_load_fw"
BUILT_MODULE_LOCATION[1]="aic_load_fw/"
DEST_MODULE_LOCATION[1]="/extramodules/"
MAKE[0]="make -C $kernel_source_dir M=$dkms_tree/$PACKAGE_NAME/$PACKAGE_VERSION/build"
CLEAN="make -C $kernel_source_dir M=$dkms_tree/$PACKAGE_NAME/$PACKAGE_VERSION/build clean"
AUTOINSTALL="YES"
EOF
```

> 注意：DKMS 3.4.3 要求 dkms.conf 必须显式包含 `PACKAGE_NAME` 和 `PACKAGE_VERSION`，否则报
> `Error! No 'PACKAGE_NAME' directive specified.`。

### 步骤 4：DKMS 编译安装

```bash
sudo dkms install aic8800/1.0.9
```

成功输出（关键行）：

```
Creating symlink /var/lib/dkms/aic8800/1.0.9/source -> /usr/src/aic8800-1.0.9
Building module(s).... done.
Signing module .../aic8800_fdrv.ko
Signing module .../aic_load_fw.ko
Installing /usr/lib/modules/7.2.6-arch2-1/updates/dkms/aic8800_fdrv.ko.zst
Installing /usr/lib/modules/7.2.6-arch2-1/updates/dkms/aic_load_fw.ko.zst
Running depmod... done.
```

### 步骤 5：加载模块

```bash
sudo modprobe aic_load_fw
sudo modprobe aic8800_fdrv
```

> 顺序不能反：`aic8800_fdrv` 依赖 `aic_load_fw` 导出的 `get_fw_path` 符号。

### 步骤 6：切换设备模式

如果设备还停留在 U 盘模式（`lsusb` 显示 `a69c:5721`）：

```bash
lsblk                        # 找到 AIC flash 对应的盘符，例如 sdb
sudo eject /dev/sdb          # 或重新插拔，让 udev 规则自动处理
```

切换成功后 `lsusb` 显示 `2357:0147 TP-Link AIC8800DC`，并自动加载驱动、创建 `wlan0`。

---

## 五、验证结果

```bash
$ dkms status
aic8800/1.0.9, 7.2.6-arch2-1, x86_64: installed

$ lsmod | grep aic
aic8800_fdrv          671744  0
aic_load_fw            94208  1 aic8800_fdrv

$ ip -br link
wlan0    DOWN    2a:c8:83:9d:2b:a4 <NO-CARRIER,BROADCAST,MULTICAST,UP>

$ rfkill list
0: phy0: Wireless LAN
        Soft blocked: no
        Hard blocked: no

$ nmcli dev status
wlan0          wifi      已断开        --
```

内核日志确认固件加载成功：

```
AICWFDBG(LOGINFO)  rwnx v6.4.3.0 ...
AICWFDBG(LOGINFO)  get macaddr:4c,52
AICWFDBG(LOGINFO)  HT supp 1, VHT supp 1, HE supp 1
AICWFDBG(LOGINFO)  New interface create wlan0
```

---

## 六、日常使用说明

### 6.1 连接 WiFi

**方式一：图形界面（推荐）**

点击桌面右上角/任务栏 NetworkManager 图标，选择 WiFi 网络输入密码即可。

**方式二：nmcli 命令行**

```bash
nmcli dev wifi list                                # 扫描周边 WiFi
nmcli dev wifi connect "SSID名称" password "密码"    # 连接
nmcli connection show                              # 查看已保存的连接
nmcli connection up "SSID名称"                      # 再次连接
nmcli connection down "SSID名称"                    # 断开
nmcli dev disconnect wlan0                         # 断开 wlan0
```

**方式三：nmtui 文本界面**

```bash
nmtui
```

### 6.2 常用状态检查

```bash
ip -br link                    # 查看网卡是否在
iw dev                         # 查看无线设备信息
iw dev wlan0 link              # 查看当前连接状态
rfkill list                    # 查看无线开关/软硬阻塞
lsusb | grep -iE 'aicsemi|AIC8800'   # 查看 USB 模式（5721=U盘模式, 0147=WiFi模式）
lsmod | grep aic               # 查看驱动模块
dkms status                    # 查看 DKMS 状态
journalctl -k | grep -i aic | tail -50   # 查看驱动日志
```

### 6.3 插拔行为说明

- 插入网卡：先出现为 `a69c:5721` U 盘 → udev 规则自动 `eject` → 变为 `2357:0147` WiFi 设备 → 内核按 modalias 自动加载 `aic8800_fdrv` → 出现 `wlan0`；
- 整个过程无需手动操作，NetworkManager 会自动接管；
- 开机时若网卡一直插着，同样会自动完成上述切换。

### 6.4 无线开关

```bash
nmcli radio wifi off     # 关闭 WiFi
nmcli radio wifi on      # 打开 WiFi
```

---

## 七、维护与卸载

### 7.1 内核升级

DKMS 的 `AUTOINSTALL="YES"` 配合 pacman 的 dkms 钩子，会在内核升级后**自动重新编译**驱动，无需人工干预。

升级内核后若想手动确认：

```bash
dkms status                          # 应显示 installed
# 如果显示 built 但未 installed，或报错：
sudo dkms install aic8800/1.0.9
```

> 前提：安装了对应版本的 `linux-headers`。Arch 上内核与头文件版本必须一致。

### 7.2 重新编译

```bash
sudo dkms uninstall aic8800/1.0.9
sudo dkms install aic8800/1.0.9
```

### 7.3 完全卸载

```bash
sudo modprobe -r aic8800_fdrv aic_load_fw
sudo dkms remove aic8800/1.0.9 --all
sudo rm -f /etc/udev/rules.d/aic.rules
sudo rm -rf /lib/firmware/aic8800DC
sudo udevadm control --reload && sudo udevadm trigger
```

---

## 八、故障排除

### 8.1 没有出现 wlan0

按顺序排查：

```bash
lsusb                            # 1. 设备是什么模式？
#   a69c:5721  -> 还在 U 盘模式，执行: sudo eject /dev/sdb（盘符以 lsblk 为准）
#   2357:0147  -> WiFi 模式正常

lsmod | grep aic                 # 2. 模块加载了吗？
sudo modprobe aic_load_fw && sudo modprobe aic8800_fdrv

journalctl -k | grep -i aic | tail -50   # 3. 看内核日志报什么错
```

### 8.2 设备卡在 U 盘模式、udev 不自动 eject

```bash
lsblk                            # 找到 AIC flash 的盘符
sudo eject /dev/sdX              # 手动弹出
# 或直接拔插一次
```

若规则丢失，重新安装：

```bash
sudo install -Dm644 .../aic.rules /etc/udev/rules.d/aic.rules
sudo udevadm control --reload && sudo udevadm trigger
```

### 8.3 模块加载失败（Secure Boot / 签名）

DKMS 编译时会自动用 `/var/lib/dkms/mok.key` 签名。若开启 Secure Boot 且未注册 MOK，加载会报
`Key was rejected by service` 或 `module verification failed`：

```bash
mokutil --sb-state               # 查看 Secure Boot 状态
sudo mokutil --import /var/lib/dkms/mok.pub   # 注册 MOK，重启时按提示操作
```

### 8.4 内核升级后驱动编译失败

```bash
sudo pacman -S linux-headers    # 确认头文件与内核版本一致
sudo dkms autoinstall            # 自动为所有内核重编
dkms status
```

如果上游新内核 API 再次变化导致编译失败，去上游仓库/AUR 查看是否有更新版本：
<https://github.com/precomp666/aic8800-driver>

### 8.5 提示 regulatory.db failed

```
Direct firmware load for regulatory.db failed with error -2
```

这是缺少 `wireless-regdb`（非致命，驱动自带 regdb 也可工作）。可选安装：

```bash
sudo pacman -S wireless-regdb
```

### 8.6 与其它 AIC 驱动冲突

如果之前装过其它 aic8800 驱动包，先卸载：

```bash
sudo dkms status                  # 查看所有 dkms 包
sudo dkms remove <冲突包名> --all
```

### 8.7 KDE 连接 WPA2/WPA3 混合网络报“密码错误”（实际是 SAE 关联失败）

**现象：**

- KDE 图形界面连接某个 WiFi（该 AP 同时广播 WPA2 和 WPA3，即 `PSK SAE` 混合模式）时，
  反复提示“密码错误”，但密码绝对正确；
- `wpa_supplicant` 日志显示 `CTRL-EVENT-ASSOC-REJECT ... status_code=13`（关联被拒绝，
  发生在 4 次握手之前，说明根本不是密码问题）；
- 驱动日志 `rwnx_rx_sm_connect_ind ... status_code:13`；
- 此时 `ip -br link` 里 `wlan0` 可能显示 `DOWN`（因为激活失败后没有活动连接，属正常现象）。

**原因：**

KDE/plasma-nm 扫描到 AP 支持 WPA3 后，自动创建的连接使用了 WPA3/SAE：

```
Config: added 'key_mgmt' value 'SAE FT-SAE'
Config: added 'ieee80211w' value '2'      # PMF 强制
```

而 AIC8800DC 厂商驱动虽然声明支持 SAE（`NL80211_FEATURE_SAE`），但固件实际处理 SAE
关联有缺陷，AP 直接回 `status_code=13`（不支持的认证算法）拒绝关联。
NetworkManager 等超时后弹出密码框，KDE 就把它显示为“密码错误”，具有误导性。

**解决：** 为该网络强制使用纯 WPA2-PSK 并关闭 PMF。命令行创建配置（GUI 可在
“连接编辑器”里把安全性改为“WPA/WPA2 个人版”并输入密码）：

```bash
nmcli con add type wifi con-name NHK ssid NHK connection.autoconnect yes
nmcli con mod NHK wifi-sec.key-mgmt wpa-psk \
  wifi-sec.psk "你的密码" \
  wifi-sec.proto rsn \
  wifi-sec.pairwise ccmp \
  wifi-sec.group ccmp \
  wifi-sec.pmf 1 \
  802-11-wireless.cloned-mac-address permanent
nmcli con up NHK
```

关键参数：`key-mgmt=wpa-psk`（不让 NM 选 SAE）、`pmf=1`（关闭 PMF）、
`cloned-mac-address=permanent`（固定网卡 MAC，排除随机 MAC 变量）。

**验证成功时日志应为：**

```
wlan0: Associated with <BSSID>
wlan0: WPA: Key negotiation completed ... [PTK=CCMP GTK=CCMP]
wlan0: CTRL-EVENT-CONNECTED
NetworkManager: 4way_handshake -> completed
```

**经对照测试确认：**

| 配置项 | 结果 |
|---|---|
| KDE 默认（SAE + ieee80211w=2） | 关联被拒 status_code=13 |
| 强制 wpa-psk + PMF 关闭 + 固定/随机 MAC | 均成功连接 |
| 随机 MAC（cloned-mac-address=stable） | 不是失败原因 |
| PMF 可选（pmf=2） | 也可以连接 |

> 注：NM 的扫描默认会随机 MAC（`wifi.scan-rand-mac-address=yes`），所以平时
> `ip link` 看到的 wlan0 MAC 与网卡永久 MAC 不同属正常现象，不代表故障。

---

## 九、系统文件清单（本次安装产生）

| 路径 | 说明 |
|---|---|
| `/lib/firmware/aic8800DC/` | 固件目录（15 个文件） |
| `/etc/udev/rules.d/aic.rules` | 自动 eject 切换模式的 udev 规则 |
| `/usr/src/aic8800-1.0.9/` | DKMS 源码目录（含 dkms.conf） |
| `/var/lib/dkms/aic8800/1.0.9/` | DKMS 构建状态目录 |
| `/usr/lib/modules/7.2.6-arch2-1/updates/dkms/aic8800_fdrv.ko.zst` | 已安装的 WLAN 驱动模块 |
| `/usr/lib/modules/7.2.6-arch2-1/updates/dkms/aic_load_fw.ko.zst` | 已安装的固件加载/蓝牙模块 |

---

## 十、参考链接

- AUR 包（驱动来源参考）：<https://aur.archlinux.org/packages/aic8800-linux7-dkms>
- 驱动上游源码：<https://github.com/precomp666/aic8800-driver>
- 官方 deb 包：`~/Downloads/UX3(免驱版) V1.0 Linux系统驱动程序20250118/aic8800fdrvpackage.deb`

---

*本文档由 opencode 根据实际安装过程整理，所有命令均已在本机执行验证。*
