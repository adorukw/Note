# Waybar 配置与 Niri 修改总结

> 环境：niri 26.04 + waybar 0.15.0（Arch Linux）
> 日期：2026-07-31

---

## 1. 目录结构

```
~/.config/waybar/
├── config.jsonc          # Waybar 主配置
├── style.css             # Waybar 样式
└── scripts/
    ├── waybar-hide.py    # [新增] 自动隐藏守护脚本
    ├── fcitx.sh          # 输入法状态 (fcitx5)
    ├── battery.sh        # 双电池电量合并显示
    └── power.sh          # 电源菜单 (fuzzel)

~/.config/niri/config.kdl # niri 配置（改动 2 处）
```

---

## 2. Waybar 主配置 `config.jsonc`

### 2.1 顶栏全局选项

```jsonc
{
    "layer": "top",          // 在顶层图层显示（覆盖窗口之上）
    "position": "top",       // 屏幕顶部
    "height": 32,            // 高度 32px
    "spacing": 6,            // 模块间距
    "margin-top": 4,         // 上边距（浮空效果）
    "margin-left": 4,
    "margin-right": 4,
    "on-sigusr1": "show",    // [新增] SIGUSR1 = 显示
    "on-sigusr2": "hide",    // [新增] SIGUSR2 = 隐藏
    ...
}
```

**自动隐藏相关的两行是关键**：
- waybar 0.15 支持自定义信号动作（`show`/`hide`/`toggle`/`reload`/`noop`）。
- 收到 `SIGUSR2` 隐藏时，waybar 会切换到内置 `invisible` 模式：**释放独占区（窗口占满全屏）、全透明、鼠标事件穿透**。
- 收到 `SIGUSR1` 显示时，恢复到默认模式（top 图层 + 独占区）。

### 2.2 模块布局

| 区域 | 模块 | 作用 |
|------|------|------|
| `modules-left` | `niri/workspaces` | 工作区指示器，滚轮切换工作区 |
| `modules-center` | `niri/window` | 当前窗口标题 |
| `modules-right` | tray / fcitx / network / pulseaudio / backlight / cpu / memory / temperature / battery / clock / power | 托盘、输入法、网络、音量、亮度、系统状态、时钟、电源菜单 |

### 2.3 各模块要点

- **niri/workspaces**：`format: {index}`，点击/滚轮切换工作区。
- **niri/window**：显示焦点窗口标题，最多 60 字符，悬停显示 `app_id\n标题`。
- **custom/fcitx**：每 1 秒运行 `fcitx.sh`，点击切换输入法，返回 JSON 带 `active/inactive` 状态。
- **network**：每 5 秒刷新，区分 WiFi/有线/未连接，点击打开 `nm-connection-editor`。
- **pulseaudio**：左键静音、右键 `pavucontrol`、滚轮调音量（WirePlumber）。
- **backlight**：Intel 背光，左键 40%、右键 80%、滚轮 ±5%。
- **cpu / memory**：每 5 秒刷新，70%/90% 触发 `warning`/`critical` 状态色。
- **temperature**：读取 `hwmon6`，80°C 触发临界色。
- **custom/battery**：每 30 秒运行 `battery.sh`（合并 BAT0+BAT1），返回 charging/full/discharging/low/critical 状态。
- **clock**：默认 `HH:MM`，点击切换 `MM-DD HH:MM`，悬停显示完整日期。
- **custom/power**：点击运行 `power.sh`（fuzzel 电源菜单）。

### 2.4 样式 `style.css` 要点

- 全局：无边框、Nerd Font + Noto Sans CJK SC、13px、`min-height: 0`。
- `window#waybar`：半透明 Catppuccin 底色 + 圆角 12px。
- 模块统一样式：内边距胶囊底、hover 高亮。
- 状态色：cpu/memory 告警黄、临界红；电池充电绿、低电橙、临界红；fcitx 激活蓝。
- **删除项**：`window#waybar.hidden { opacity: 0.2; }` —— 0.15 版隐藏时直接 `set_opacity(0)`，该规则已无效。

---

## 3. 自动隐藏实现

### 3.1 为什么是"事件驱动"而不是"鼠标贴顶"

原计划的"鼠标移到顶部显示"需要读取光标坐标，但：
- `wlrctl` 没有读取位置的子命令（只有 `click`/`move`/`scroll`）；
- niri 26.04 没有查询光标的 IPC（`niri msg pointer` 已合并到上游但未发布）。

因此改用**纯事件驱动**，零依赖、零轮询。

### 3.2 隐藏/显示规则

| 条件 | 动作 |
|------|------|
| 当前（焦点）工作区有窗口 | 隐藏 |
| 当前工作区无窗口 | 显示 |
| 任意窗口/工作区 urgent（紧急） | 显示 |
| 总览 (overview) 打开 | 显示 |

### 3.3 守护脚本 `waybar-hide.py`

```
~/.config/waybar/scripts/waybar-hide.py
```

工作流程：

1. **启动时**：读取当前状态（`niri msg --json windows / workspaces / overview-state`），立即同步一次 bar 状态。
2. **监听事件**：通过 `niri msg --json event-stream` 持续接收事件，只对相关事件重算：
   - 窗口类：`WindowOpenedOrChanged` / `WindowClosed` / `WindowFocusChanged` / `WindowUrgencyChanged` / `WindowsChanged`
   - 工作区类：`WorkspacesChanged` / `WorkspaceUrgencyChanged` / `WorkspaceActivated` / `WorkspaceActiveWindowChanged`
   - 总览：`OverviewOpenedOrClosed`
3. **下发信号**：需要隐藏时 `SIGUSR2`（hide），需要显示时 `SIGUSR1`（show），发往 `pgrep -x waybar` 的进程。
4. **防失步**：每 10 秒强制重算一次；若检测到 waybar 进程 PID 变化（重启过），会重新下发当前状态。
5. **手动开关**：收到 `SIGHUP` 切换自动隐藏开关；关闭时强制显示 bar。
6. PID 写入 `/tmp/waybar-hide.pid`。

### 3.4 信号语义对照

| 信号 | 对象 | 动作 |
|------|------|------|
| `SIGUSR1` | waybar | 显示（配置了 `on-sigusr1: show`） |
| `SIGUSR2` | waybar | 隐藏（配置了 `on-sigusr2: hide`） |
| `SIGHUP` | waybar-hide.py | 切换自动隐藏开/关 |

---

## 4. Niri 修改 `config.kdl`

### 4.1 自启动守护脚本（位于 `spawn-at-startup "waybar"` 之后）

```kdl
spawn-at-startup "waybar"
spawn-at-startup "/home/adorukw/.config/waybar/scripts/waybar-hide.py"
```

注意：`spawn-at-startup` 只在 niri 启动时执行一次，`niri msg action load-config-file` 重载不会重复触发（已验证）。

### 4.2 快捷键（binds 段内）

```kdl
Mod+B hotkey-overlay-title="Toggle Waybar Auto-Hide" { spawn-sh "kill -HUP $(cat /tmp/waybar-hide.pid)"; }
```

按下 `Mod+B` → 给 daemon 发 `SIGHUP` → 切换自动隐藏。
- 自动隐藏 **开**：有窗口时 bar 隐藏；
- 自动隐藏 **关**：bar 常驻显示（临时全屏需要时可用）。

---

## 5. 使用与维护

### 5.1 常用操作

| 操作 | 命令 |
|------|------|
| 手动隐藏 bar | `kill -USR2 $(pgrep -x waybar)` |
| 手动显示 bar | `kill -USR1 $(pgrep -x waybar)` |
| 切换自动隐藏 | `Mod+B` 或 `kill -HUP $(cat /tmp/waybar-hide.pid)` |
| 查看 daemon 是否运行 | `pgrep -af waybar-hide.py` |
| 重启 daemon | `kill $(cat /tmp/waybar-hide.pid) && nohup ~/.config/waybar/scripts/waybar-hide.py >> /tmp/waybar-hide.log 2>&1 &` |

### 5.2 注意事项

1. **重载 waybar 配置不再用 `SIGUSR2`**：现在 `SIGUSR2` 是"隐藏"。以后改 `config.jsonc`/`style.css` 需要重启 waybar：
   ```sh
   pkill -x waybar   # 之后手动再启动，或下次登录由 niri 自动拉起
   ```
2. **bar 隐藏后窗口自动占满全屏**：这是 waybar 释放 exclusive zone 的效果，属预期行为。
3. **daemon 与 bar 的生命周期**：daemon 已手动启动并持续运行；下次登录由 niri 自动拉起。若 daemon 先于 waybar 启动，10 秒自检会补齐状态，无需担心。
4. **验证方法**：检查 bar 所在图层（隐藏时是 Bottom，显示时是 Top）：
   ```sh
   niri msg --json layers
   ```

---

## 6. 变更文件清单

| 文件 | 变更 |
|------|------|
| `~/.config/waybar/scripts/waybar-hide.py` | 新增（自动隐藏守护脚本） |
| `~/.config/waybar/config.jsonc` | 新增 `on-sigusr1`/`on-sigusr2` |
| `~/.config/waybar/style.css` | 删除无效的 `.hidden` 规则 |
| `~/.config/niri/config.kdl` | 新增 daemon 自启 + `Mod+B` 快捷键 |
