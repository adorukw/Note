# ThinkPad T14 找不到 WiFi 适配器 — 问题分析与修复方案

> 机型：Lenovo ThinkPad T14（`Adorukw-T14`）
> 系统：Debian GNU/Linux forky/sid，内核 `7.1.13+deb14-amd64`
> 无线网卡：Intel Wi-Fi 6E AX210（PCI `03:00.0`，ID `[8086:2725]`）
> 修复日期：2026-09-22

---

## 一、故障现象

- NetworkManager 中**完全看不到 WiFi 设备**，只有有线网卡和 USB 网络共享设备。
- `nmcli device status` 输出中没有 `wifi` 类型的设备。
- `ip link` 中不存在 `wlan0` / `wlp3s0` 接口。
- 蓝牙同样不可用。

## 二、根本原因

**缺少 Intel WiFi 固件包 `firmware-iwlwifi`。**

内核自带的 `iwlwifi` 驱动能识别网卡，但启动时需要从 `/lib/firmware` 加载固件文件，
而系统里从未安装过该固件包，导致驱动初始化失败、**网卡接口根本没有被创建**，
因此 NetworkManager 连设备的影子都看不到。

### 排查证据链

| 检查项 | 结果 |
|---|---|
| PCI 识别网卡 | ✅ `03:00.0 Intel Wi-Fi 6E AX210 [8086:2725]` |
| 驱动绑定（lspci） | ❌ 无 `Kernel driver in use`，仅有 `Kernel modules: iwlwifi` |
| 内核模块 | ✅ `iwlwifi` 已加载（驱动本身没问题） |
| 固件加载 | ❌ 失败 |
| `/lib/firmware` | ❌ 完全没有 iwlwifi 固件文件 |
| `rfkill` | ✅ 无软/硬阻塞（且当时 `rfkill` 命令本身也没装） |
| AppArmor | ✅ 无任何拒绝日志（排除） |

内核日志（`dmesg`）给出了直接答案：

```
iwlwifi 0000:03:00.0: Detected Intel(R) Wi-Fi 6E AX210 160MHz
iwlwifi 0000:03:00.0: firmware: failed to load iwlwifi-ty-a0-gf-a0-89.ucode (-2)
iwlwifi 0000:03:00.0: no suitable firmware found!
iwlwifi 0000:03:00.0: iwlwifi-ty-a0-gf-a0-89 is required
```

`-2` 即 `ENOENT`（文件不存在）。随后蓝牙也报了同样的错：

```
bluetooth hci0: firmware: failed to load intel/ibt-0041-0041.sfi (-2)
```

### 为什么包会缺失

`dpkg -l` 显示系统只安装了这些固件包：

```
firmware-amd-graphics   （AMD 显卡）
firmware-realtek        （Realtek 网卡/读卡器）
firmware-sof-signed     （音频 DSP）
amd64-microcode
```

**`firmware-iwlwifi` 从未被安装**（安装系统时未被自动选中）。

## 三、修复步骤

### 1. 确认软件源已启用 `non-free-firmware` 组件

```bash
grep -rh "Components" /etc/apt/sources.list /etc/apt/sources.list.d/
```

本机源已包含 `main non-free-firmware`，无需修改。

### 2. 安装固件包

```bash
sudo apt update
sudo apt install firmware-iwlwifi rfkill
```

- 安装版本：`firmware-iwlwifi 20260810-1`（25 MB）
- 该包同时提供：
  - WiFi 固件 `iwlwifi-ty-a0-gf-a0-89.ucode`（正是内核要求的版本）
  - 蓝牙固件 `intel/ibt-0041-0041.sfi`
- `rfkill` 用于后续排查无线开关状态（顺手补装）。

### 3. 重新加载驱动（免重启立即生效）

```bash
sudo modprobe -r iwlwifi
sudo modprobe iwlwifi
# 蓝牙固件同样需要重载才能生效
sudo modprobe -r btusb
sudo modprobe btusb
```

加载成功后日志显示：

```
iwlwifi 0000:03:00.0: loaded firmware version 89.f7dcb3ee.0 ty-a0-gf-a0-89.ucode op_mode iwlmvm
iwlwifi 0000:03:00.0 wlp3s0: renamed from wlan0
```

### 4. 处理附带的 supplicant 问题

网卡是在开机约 4 分钟后"热插入"出现的，此时 `wpa_supplicant` 实例状态已过期，报错：

```
device (wlp3s0): Couldn't initialize supplicant interface:
GDBus.Error:fi.w1.wpa_supplicant1.UnknownError: wpa_supplicant couldn't grab this interface.
```

解决：

```bash
sudo systemctl restart wpa_supplicant
sudo systemctl restart NetworkManager
```

> 已排查 AppArmor，无任何 `DENIED` 日志（该问题是 Ubuntu 的 wpa_supplicant
> AppArmor 配置 bug，Debian 本机不存在该 profile，与本故障无关）。

## 四、修复后验证

```bash
nmcli device status
rfkill list
dmesg | grep -i iwlwifi | tail
```

结果：

```
DEVICE    TYPE      STATE      CONNECTION
wlp3s0    wifi      已连接      NHK          # 接口已创建并成功连接
```

- 接口：`wlp3s0`，固件 `89.f7dcb3ee.0`
- IP 地址：`10.230.42.23/24`，网关 `10.230.42.145`
- `ping 223.5.5.5`：0% 丢包，可正常上网
- 蓝牙控制器 `Adorukw-T14` 正常启动（固件 `2026.5 build 82122`）
- `rfkill`：软/硬阻塞均已解除

## 五、持久化说明

固件包已永久安装，**重启后会自动加载，无需任何额外配置**。
建议重启一次做最终验证。

若重启后 WiFi 意外消失，执行下面一条即可恢复：

```bash
sudo systemctl restart wpa_supplicant
```

## 六、常用命令备查

```bash
# 查看无线设备
nmcli device status
ip link show
rfkill list

# 查看无线开关
nmcli radio all

# 连接指定 WiFi
nmcli device wifi connect "WiFi名称" password "密码"

# 查看驱动/固件加载情况
sudo dmesg | grep -i iwlwifi

# 确认固件包状态
dpkg -l firmware-iwlwifi
ls -l /lib/firmware/iwlwifi-ty-a0-gf-a0-89.ucode
```

## 七、注意事项

- 当前自动连接的是**开放网络 NHK**（无加密），在公共 WiFi 上请勿处理敏感信息。
- 本机已启用 `non-free-firmware` 软件源，后续其他硬件缺固件时可直接用
  `apt install firmware-<厂商>` 补装。
- 本次修复共安装 2 个包：`firmware-iwlwifi`、`rfkill`，未修改任何配置文件。
