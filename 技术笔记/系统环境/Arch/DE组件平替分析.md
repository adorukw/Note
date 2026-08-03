# DE 组件平替分析：12 个"外借"软件包的完全独立替代方案

> 生成时间：2026-08-01
> 环境：Arch Linux + niri + PipeWire
> 目的：评估这些来自 GNOME/XFCE/KDE 的组件，是否有**完全独立的第三方软件**可以替换

---

## 结论速览

| 原软件包 | 来源 | 推荐独立平替 | 难度 | 是否值得换 |
|----------|------|-------------|------|-----------|
| gnome-keyring | GNOME | **KeePassXC**（提供 secrets API） | 中 | ⚠️ 可选 |
| gnome-power-manager | GNOME | 不需要（niri + brightnessctl 即可） | 低 | ✅ 直接删 |
| gvfs | GNOME | **无完整平替**（可用 sshfs/cifs-utils 替代功能） | 高 | ❌ 建议保留 |
| file-roller | GNOME | **xarchiver** / **peazip** | 低 | ✅ 可换 |
| networkmanager | GNOME | **iwd** 或 **systemd-networkd** | 中 | ⚠️ 谨慎 |
| network-manager-applet | GNOME | 不换 NM 的话无解；换 NM 则用 nm-tray / iwd | 中 | ⚠️ 可选 |
| thunar | XFCE | **lf / ranger / yazi / nnn**（终端） | 低 | ✅ 可换 |
| tumbler | XFCE | **ffmpegthumbnailer + chafa**（预览） | 低 | ✅ 可换 |
| thunar-archive-plugin | XFCE | **atool**（终端解压） | 低 | ✅ 可换 |
| thunar-volman | XFCE | **udisks2 规则 + udev 脚本** | 中 | ✅ 可换 |
| sddm | KDE | **ly** 或 **greetd** | 低 | ✅ 可换 |
| pavucontrol | PulseAudio | **pwvucontrol** 或 **pulsemixer** | 低 | ✅ 可换 |

---

## 详细分析

### 1. gnome-keyring（密钥/密码存储）

**问题**：niri 等非 GNOME 环境通常需要它提供 `org.freedesktop.secrets`，供浏览器/应用存取密码。

**独立平替**：
- **KeePassXC** ✅（官方仓库/独立项目）— 自带 `org.freedesktop.secrets` 服务，可完全接管 gnome-keyring 的角色，且是跨平台、独立的密码管理器。
- `KSecretService` — 是 KDE 组件，不符合"独立"要求。

**注意**：替换后浏览器（Edge/Chrome）需要通过 KeePassXC 的 browser integration 或 secrets 接口存取密码，配置比 gnome-keyring 略繁琐。

---

### 2. gnome-power-manager（电源管理）

**问题**：它是 GNOME 很老的工具，在 niri + PipeWire 环境下基本没有 GUI 需求。

**独立平替**：
- **不需要任何替代**。niri 自带 `niri power-action`（关机/重启/锁屏），亮度用已装的 **brightnessctl**，电池状态用 upower + 脚本即可。
- 若想要图形界面：`upower` 的 CLI 已足够，没有真正"独立"的成熟 GUI 电源工具值得装。

**结论**：**直接卸载即可**，属于 12 个里最该删的一个。

---

### 3. gvfs（虚拟文件系统）

**问题**：提供 Thunar 的回收站、挂载、SFTP/SMB 网络浏览、MTP 等抽象层。**没有真正独立的完整平替**——它是这个生态的事实标准。

**替代思路**（按功能拆分）：
- SSH 浏览 → `sshfs`（独立，FUSE 挂载）
- SMB 挂载 → `cifs-utils`
- 回收站 → `trash-cli`
- 移动硬盘自动挂载 → `udisks2`（已装）+ udev 规则
- 代价：每个功能都要单独配置，且 Thunar 的"回收站"/"网络"侧边栏会失效。

**结论**：**不建议替换**。它虽来自 GNOME，但已近乎"通用基础设施"，gvfs 的替代成本远高于收益。

---

### 4. file-roller（压缩管理器）

**问题**：GTK 压缩 GUI，被 thunar-archive-plugin 调用。

**独立平替**：
- **xarchiver** ✅（官方仓库，独立项目）— 轻量 GTK 解压工具。
- **peazip** ✅（独立项目）— 功能更全，跨平台，含压缩校验。

**注意**：换掉后需要改 thunar-archive-plugin 的配置指向 xarchiver；若同时弃用 Thunar 则无关紧要。

---

### 5. networkmanager（网络管理）

**问题**：起源 GNOME，但现在是跨 DE 标准网络栈。

**独立平替**：
- **iwd**（Intel 开发，独立项目，官方仓库）— 自带 DHCP、WPA，可直接替代 wifi 管理；但**无图形界面**。
- **systemd-networkd** — systemd 自带，独立、零额外依赖，但配置偏"编辑配置文件"风格。
- **connman**（独立项目，官方仓库）— 有 `connman-gtk` 但属 GNOME/其他生态。

**注意**：若浏览器/应用依赖 `libnm` 或 nmcli，替换后 API 不兼容。niri 用户若只用 nmcli 命令行，换成 iwd 体验尚可，但 GUI 功能丢失。

---

### 6. network-manager-applet（网络托盘）

**问题**：GNOME 的托盘图标，依赖 NetworkManager。

**独立平替**：
- **不换 NM** → 无真正独立平替（所有托盘网络工具都基于 libnm）。
- **换 NM → iwd/systemd-networkd** → 托盘可改用 `nm-tray`（独立，AUR）——但 nm-tray 仍需要 NM 后端。
- 若走 systemd-networkd：**放弃托盘**，用 `nmcli`/`wifi-menu` 或按键绑定调用 `iwctl`。

**结论**：这是个"拴在 NM 上"的组件，独立性取决于 NM 的去留。

---

### 7. thunar（文件管理器）

**问题**：XFCE 核心组件。

**独立平替**：
- **lf** ✅（独立，Go 编写）— 终端文件管理器，vim 键位，支持预览。
- **ranger** ✅（独立）— Python 终端 FM，老牌。
- **yazi** ✅（独立，Rust）— 现代化，预览/图片支持好。
- **nnn** ✅（独立，C）— 极简快速。

**注意**：终端 FM 在 niri 全屏窗口下体验很好；但失去图形拖拽、侧边栏、缩略图网格。若需要 GUI，`pcmanfm-qt` 属 LXQt 生态，非完全独立。**Double Commander**（AUR 无包）也可考虑。

---

### 8. tumbler（缩略图服务）

**问题**：XFCE 的缩略图后台服务，供 Thunar 使用。

**独立平替**：
- **ffmpegthumbnailer** ✅（独立，官方仓库）— 视频缩略图。
- **chafa** ✅（独立）— 终端内图片渲染。
- 若换用终端 FM（lf/yazi），它们自带的预览（`ueberzugpp` ✅ / chafa）完全替代 tumbler。

**结论**：**随 Thunar 一起换掉**，在 niri 场景下没有独立 GUI 缩略图需求。

---

### 9. thunar-archive-plugin（压缩集成）

**独立平替**：
- **atool** ✅（独立，`aunpack`/`apack`）— 命令行通用解压前端。
- 终端 FM（yazi/ranger）自带解压绑定，无需插件。
- GUI 侧：配合 xarchiver 即可。

---

### 10. thunar-volman（可移动设备）

**问题**：Thunar 的自动挂载/设备处理。

**独立平替**：
- **udisks2**（已装）本身是独立/中立的系统服务，不属于任何 DE。
- 用 `udev` 规则 + `udisksctl`/`pmount` 脚本实现自动挂载。
- 或靠终端 FM 手动 `udisksctl mount`。

**结论**：**可删**，代价是失去 Thunar 的即插即用弹窗。

---

### 11. sddm（登录管理器）

**问题**：KDE 主导的 QML 登录管理器。

**独立平替**：
- **ly** ✅（官方仓库，独立项目）— 极简 TUI 登录器，默认接你的桌面。
- **greetd** ✅（官方仓库，独立项目）— 极简登录守护，配合 `tuigreet`/`regreet` 等。
- `lightdm` — 虽然跨 DE，但主要维护者属 Xubuntu，独立性稍差。

**注意**：sddm-sugar-dark 主题会随 sddm 一起失效，需要换 greetd 主题。终端里用 `ly` 最省事。

---

### 12. pavucontrol（音量控制）

**问题**：PulseAudio 项目的配套 GTK 工具，非 DE 组件但风格偏 GNOME。

**独立平替**：
- **pwvucontrol** ✅（AUR，独立项目）— 专为 PipeWire 写的 GTK 音量控制，功能对标 pavucontrol。
- **pulsemixer** ✅（官方仓库，独立）— ncurses 音量工具，依赖少。
- **ncpamixer** ✅（AUR，独立）— 同上，ncurses 风格。

**结论**：**强烈建议换 pwvucontrol**——原生 PipeWire，比 pavucontrol（PulseAudio 老栈）更契合你的音频栈。

---

## 替换后的理想组合（niri + 全独立软件栈）

```
登录器      ly / greetd
合成器      niri（已独立）
输入法      fcitx5（已独立）
文件管理    yazi 或 lf（终端）
解压        atool + xarchiver
缩略图/预览 chafa + ffmpegthumbnailer
网络        iwd + iwctl（或保留 NM）
音量        pwvucontrol / pulsemixer
密码        KeePassXC
电源        直接删 gnome-power-manager
```

替换后仍属于 DE 组件的仅剩：**gvfs（建议保留）** 和 **network-manager-applet（取决于 NM）**，其余全部可换为完全独立软件。

---

## 附加提醒

- 本机已装 `yay`/`base-devel`，AUR 包（pwvucontrol、nm-tray、ncpamixer）可直接 `yay -S`。
- 替换 sddm 前先装好 ly/greetd 并 `systemctl enable` 新服务，避免重启后无登录界面。
- 卸载 gnome-power-manager 前确认 niri 的 `power-action` 绑定已配置。
- 想保留 Thunar 但去掉 XFCE 依赖时，`gvfs` 不能删，否则回收站和挂载失效。
