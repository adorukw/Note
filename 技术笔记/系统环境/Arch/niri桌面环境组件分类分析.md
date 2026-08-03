# niri 桌面环境组件分类分析

> 生成时间：2026-08-01
> 环境：Arch Linux + niri（滚动平铺 Wayland 合成器）+ PipeWire
> 目的：对 niri 图形界面所用全部软件/组件进行分类，区分「从其他 DE/WM 借用的组件」与「完全独立的第三方软件」

---

## 一、架构总览

```
┌─────────────────────────────────────────────────────┐
│                   应用层（独立软件）                    │
│   Edge / Zed / Steam / mpv / Clash Verge / Mission  │
├─────────────────────────────────────────────────────┤
│          Wayland 生态工具（独立，但面向 wlroots）        │
│   waybar / fuzzel / grim+slurp / xwayland-satellite │
├─────────────────────────────────────────────────────┤
│   借用组件层（来自 Sway / GNOME / XFCE / KDE）          │
│   swaybg / swayidle / swaylock / mako               │
│   gnome-keyring / gnome-power-manager / nm-applet    │
│   gvfs / xdg-desktop-portal-gtk / thunar / tumbler   │
│   xfconf / sddm                                     │
├─────────────────────────────────────────────────────┤
│             核心合成器（独立）                          │
│                    niri                              │
├─────────────────────────────────────────────────────┤
│        系统基础设施（中立于任何 DE）                     │
│   PipeWire / WirePlumber / NetworkManager / systemd  │
│   udisks2 / polkit / xdg-desktop-portal / fcitx5     │
├─────────────────────────────────────────────────────┤
│         X11 兼容层（面向所有 Wayland 合成器）            │
│   XWayland (xorg-xwayland) + X11 协议库               │
└─────────────────────────────────────────────────────┘
```

**启动链**：`systemd graphical.target` → **sddm**（KDE）→ 加载 `niri.desktop` 会话 → niri 执行 `spawn-at-startup` 启动各组件。

niri 配置中的启动项（`~/.config/niri/config.kdl`）：

```
spawn-at-startup waybar
spawn-at-startup waybar-hide.py        （自写脚本，自动隐藏状态栏）
spawn-at-startup gnome-keyring-daemon  （GNOME 密钥环）
spawn-at-startup swaybg                （壁纸）
spawn-at-startup swayidle              （空闲/锁屏管理）
```

---

## 二、完全独立的第三方软件（不属于任何 DE/WM）

这些项目上游与 GNOME/XFCE/KDE/Sway 均无隶属关系，是独立开源项目，任何合成器下都能使用。

### 核心合成器
| 组件 | 用途 | 说明 |
|------|------|------|
| **niri** | 窗口合成器 | 独立项目（YaLTeR 开发），不与任何 DE 绑定，本身即是"桌面环境"的基础 |

### 状态栏 / 启动器
| 组件 | 用途 | 说明 |
|------|------|------|
| **waybar** | 顶栏状态栏 | 独立项目，面向 wlroots 合成器但非 sway 官方组件；提供 niri 专属模块（workspaces/window） |
| **fuzzel** | 应用启动器 | 独立项目（jp7677），与 wayland 通用（wlr-layer-shell），被电源菜单脚本调用 |

### 输入法
| 组件 | 用途 | 说明 |
|------|------|------|
| **fcitx5 + 中文插件** | 输入法框架 | 独立项目（中科院/社区），完全 DE 无关；通过 `environment.d` 设置 GTK/QT/SDL 环境变量注入 |

### 文件 / 归档 / 图片
| 组件 | 用途 | 说明 |
|------|------|------|
| **mpv** | 视频播放器 | 独立项目 |
| **file-roller** | 压缩管理 GUI | 源自 GNOME（见"借用"表），若归类为独立则仅指其独立打包 |
| **glycin** | 图片解码库 | 独立 |

### 系统工具
| 组件 | 用途 | 说明 |
|------|------|------|
| **mission-center** | 系统监视器 | 独立项目（类似 Windows 任务管理器风格） |
| **fastfetch** | 系统信息展示 | 独立项目 |
| **brightnessctl** | 屏幕亮度控制 | 独立项目（waybar 背光模块调用） |
| **pamixer / playerctl** | 音量 / 媒体键 | 独立项目（PulseAudio/PipeWire 客户端，非 DE 组件） |
| **nvtop** | GPU 监视 | 独立 |

### 应用层
| 组件 | 用途 | 说明 |
|------|------|------|
| microsoft-edge | 浏览器 | 商业软件 |
| zed | 编辑器 | 独立 |
| steam | 游戏平台 | 独立 |
| clash-verge-rev | 代理客户端 | 独立 |
| yay | AUR 助手 | 独立 |
| vim / nodejs / npm / go | 开发工具 | 独立 |

---

## 三、从其他窗口合成器 / 桌面环境借用的组件

### 1. Sway 项目（sway 合成器生态）— 本机借用最多的来源

| 组件 | 用途 | 来源说明 |
|------|------|----------|
| **swaybg** | 壁纸渲染 | 在 **sway 官方仓库**内开发（packages 目录下），随 sway 打包发布 |
| **swayidle** | 空闲管理（超时锁屏/关屏） | sway 官方仓库组件，读 `~/.config/swayidle/config` |
| **swaylock** | 屏幕锁定 | sway 官方仓库组件，读 `~/.config/swaylock/config` |
| **mako** | 通知守护进程 | **sway 官方仓库**组件，Wayland 通知 |
| **grim** | 截图 | sway 团队（emersion）开发，虽独立打包但属 sway 生态工具链 |
| **slurp** | 截图区域选择 | 同上，与 grim 配套 |
| **xdg-desktop-portal-wlr** | 屏幕共享/截图 portal 后端 | **wlroots 项目**官方组件（sway 依赖 wlroots），供浏览器等调用 |

> 说明：这些组件与 sway 深度绑定，但因为 wlroots 协议通用，被 niri 直接复用。它们都不是 niri 的自带功能。

### 2. GNOME 项目

| 组件 | 用途 | 来源说明 |
|------|------|----------|
| **gnome-keyring** | 密钥环（secrets/ssh 组件） | GNOME 官方组件；niri 显式启动它，为 Edge/Zed 等提供密码存储 |
| **gnome-power-manager** | 电源管理 | GNOME 官方组件 |
| **network-manager-applet** | 网络托盘图标 | GNOME 官方组件（nm-applet） |
| **gvfs** | 虚拟文件系统（回收站/挂载/网络浏览） | GNOME 官方组件，被 Thunar 依赖 |
| **xdg-desktop-portal-gtk** | 文件选择等 portal 后端 | GNOME 官方实现 |
| **adwaita-icon-theme / cursors / fonts** | 图标/光标/字体主题 | GNOME 官方视觉资源，供 GTK 应用使用 |
| **gsettings-desktop-schemas / gsettings-system-schemas** | GTK 设置 schema | GNOME 官方基础库 |
| **libadwaita** | GTK4 适配库 | GNOME 官方 |
| **glib-networking / libsecret** | TLS/密钥依赖 | GNOME 官方基础库 |
| **file-roller** | 压缩 GUI | GNOME 官方应用（thunar-archive-plugin 的调用目标） |

> 现状：开机运行着 `gnome-keyring-daemon.service`、`gvfs-daemon.service`、`xdg-desktop-portal-gtk.service` 三个 GNOME 后台服务。

### 3. XFCE 项目

| 组件 | 用途 | 来源说明 |
|------|------|----------|
| **thunar** | 文件管理器 | XFCE 官方文件管理器 |
| **thunar-archive-plugin** | 右键压缩菜单 | XFCE 官方插件 |
| **thunar-volman** | 可移动设备自动挂载 | XFCE 官方插件 |
| **tumbler** | 缩略图后台服务 | XFCE 官方组件（运行中 `tumblerd.service`） |
| **xfconf / xfconfd** | XFCE 配置系统 | XFCE 官方组件（运行中 `xfconfd.service`） |
| **exo** | XFCE 基础库 | XFCE 官方依赖 |
| **libxfce4ui / libxfce4util** | XFCE 基础库 | XFCE 官方依赖 |

### 4. KDE 项目

| 组件 | 用途 | 来源说明 |
|------|------|----------|
| **sddm** | 登录/显示管理器 | **KDE 官方组件**，通过 `systemd graphical.target` 启动，加载 `niri.desktop` 会话 |
| **sddm-sugar-dark** | 登录界面主题 | 社区主题（Sugar Candy 系列），绑定 sddm |

### 5. PulseAudio 项目

| 组件 | 用途 | 来源说明 |
|------|------|----------|
| **pavucontrol** | 音量控制 GUI | PulseAudio 官方配套工具（虽非 DE 组件，但属旧音频栈，风格偏 GNOME） |

---

## 四、中立基础设施（不属于任何 DE，但被所有 DE 依赖）

这些组件同样**不是 niri 自带**，但也不属于任何特定桌面环境，是跨 DE 的标准服务，无法归类为"借用"：

| 组件 | 用途 |
|------|------|
| **PipeWire / WirePlumber / pipewire-pulse** | 现代音频/视频服务（系统级服务，Wayland 无关） |
| **NetworkManager + nm-connection-editor** | 网络管理（跨 DE 标准，GNOME 发起但已成通用） |
| **systemd（含用户级服务）** | 服务/会话管理；`pipewire.service`、`xdg-desktop-portal.service` 等均挂在其下 |
| **xdg-desktop-portal** | 跨 DE 应用集成协议（沙箱/屏幕共享/文件选择） |
| **polkit** | 授权框架（挂载/电源操作权限） |
| **udisks2** | 磁盘管理 |
| **dbus / dbus-broker** | 进程间通信 |
| **gtk-layer-shell** | Wayland 图层协议库（waybar/fuzzel 等依赖） |
| **XWayland（xorg-xwayland / xwayland-satellite）** | 为 X11 应用提供兼容层；任何 Wayland 合成器都需要 |
| **libinput / xf86-input-libinput** | 输入设备驱动 |
| **mesa / vulkan-intel / libdrm** | 图形驱动栈 |

---

## 五、关键判定：swaybg 与 waybar 的差别

这正是分类的典型例子：

| | **swaybg** | **waybar** |
|---|---|---|
| 上游项目 | **sway**（合成器） | 独立项目 |
| 维护方 | sway 开发者 | Alexays 等社区开发者 |
| 是否随 sway 发布 | 是（sway 仓库内开发） | 否 |
| 归类 | **从其他 WM 借用** | **完全独立** |
| 换用 niri 自带替代 | niri 无壁纸功能，需外部程序 | niri 无状态栏功能，需外部程序 |

两者都是 niri 的"外部组件"，但**归属不同**：swaybg 属于 sway 生态，waybar 属于独立生态。

---

## 六、汇总清单

### 从其他 DE/WM 借用（22 项）
- **Sway**（8）：swaybg、swayidle、swaylock、mako、grim、slurp、xdg-desktop-portal-wlr、gtk-layer-shell 生态依赖
- **GNOME**（11）：gnome-keyring、gnome-power-manager、network-manager-applet、gvfs、xdg-desktop-portal-gtk、file-roller、adwaita 三件套、gsettings-desktop-schemas、libadwaita、glib-networking
- **XFCE**（6）：thunar、thunar-archive-plugin、thunar-volman、tumbler、xfconf、exo
- **KDE**（2）：sddm、sddm-sugar-dark
- **PulseAudio**（1）：pavucontrol

### 完全独立（16 项）
niri、waybar、fuzzel、fcitx5、mpv、mission-center、fastfetch、brightnessctl、pamixer、playerctl、nvtop、zed、steam、clash-verge-rev、microsoft-edge、yay

### 中立基础设施（约 14 项）
PipeWire 栈、NetworkManager、systemd、xdg-desktop-portal、polkit、udisks2、dbus、XWayland、libinput、mesa/vulkan 等

---

## 七、结论

1. **niri 自身是完全独立的合成器**，但它几乎不内置任何桌面组件，需要外部程序补齐：壁纸（swaybg）、锁屏（swaylock）、通知（mako）、状态栏（waybar）、启动器（fuzzel）。

2. **借用最多的来源是 Sway 生态**（壁纸/锁屏/通知/截图全家桶），这与 niri 同为 Wayland/wlroots 环境有关，协议兼容故直接复用。

3. **GNOME/XFCE 组件多为"隐性依赖"**：gnome-keyring、gvfs、tumbler 是作为后台服务被动拉起的，用户未必感知到它们属于对应 DE。

4. **真正"完全独立"的软件栈核心是**：niri + waybar + fuzzel + fcitx5 + PipeWire 音频 + NetworkManager，这套组合不依赖任何 DE 也能构成完整桌面。

5. 若追求"全独立、零 DE 依赖"，可参考 `DE组件平替分析.md` 中的替换方案（如 gnome-keyring → KeePassXC、thunar → yazi、sddm → greetd/ly 等），本分类文档为此提供了完整的来源清单。
