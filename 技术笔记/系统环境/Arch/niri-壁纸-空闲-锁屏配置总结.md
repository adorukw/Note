# niri 壁纸 / 空闲管理 / 锁屏 配置总结

日期：2026-07-31

## 结论先说

你的 compositor 是 **niri 26.04**，经过实测确认：

- niri **没有**图片壁纸功能，只有纯色背景（`background-image` 会报错）
- niri **没有**内置 idle 空闲检测（无空闲配置块、无 idle action）
- niri **没有**内置锁屏（`niri msg action` 里没有 lock 动作）

因此壁纸、空闲、锁屏仍然由外部工具承担，但都由 **niri 统一启动和托管**，配置集中在两处。

## 最终分工

| 职责 | 工具 | 配置文件 | 状态 |
|------|------|----------|------|
| 桌面壁纸 | swaybg | `niri/config.kdl`（启动参数） | 已启用 |
| 空闲锁屏/关屏 | swayidle | `~/.config/swayidle/config` | 已启用 |
| 屏幕锁定 | swaylock | `~/.config/swaylock/config` | 已启用 |
| 统一托管 | niri `spawn-at-startup` | `niri/config.kdl` | 已生效 |

原先计划卸载的 `swaybg` 和 `swayidle` **保留**（经确认，因为没有替代品）。

## 修改的文件

### 1. `~/.config/niri/config.kdl`

在 `spawn-at-startup` 区段新增两行：

```kdl
// Wallpaper: swaybg renders the desktop background image.
spawn-at-startup "swaybg" "--mode" "fill" "--image" "/home/adorukw/Pictures/Wallpaper/bg.png"
// Idle management: swayidle locks the screen and powers off monitors on inactivity.
spawn-at-startup "swayidle" "-w"
```

锁定快捷键加上 `allow-when-locked=true`（锁屏界面卡死时可用它重新拉起锁屏程序，参考 niri FAQ）：

```kdl
Super+Alt+L hotkey-overlay-title="Lock the Screen: swaylock" allow-when-locked=true { spawn "swaylock"; }
```

### 2. `~/.config/swayidle/config`（新建）

```conf
timeout 300 'swaylock -f'
timeout 600 'niri msg action power-off-monitors'
before-sleep 'swaylock -f'
after-resume 'niri msg action power-on-monitors'
```

含义：
- 空闲 5 分钟 → 锁屏（swaylock）
- 空闲 10 分钟 → DPMS 关闭显示器
- 系统睡眠前 → 先锁屏
- 系统唤醒后 → 重新点亮显示器

### 3. `~/.config/swaylock/config`（新建）

```conf
image=/home/adorukw/Pictures/Wallpaper/lock.png
daemonize
```

- `image`：锁屏背景图（注意是 `lock.png`，3840x2160；`idle.png` 与它是同一张图，md5 一致）
- `daemonize`：后台运行，供 swayidle 调用

## 生效方式

- `swaybg` 和 `swayidle` 已手动启动，立即生效
- 下次重新登录 niri 时，`spawn-at-startup` 会自动拉起它们
- 桌面壁纸是 `bg.png`（1920x1200），fill 模式

## 验证结果

- `niri validate` → config is valid
- `swayidle -d`（debug）→ 正确注册 300s/600s 两个 timeout
- `niri msg action power-off-monitors` / `power-on-monitors` → DPMS 开关正常
- swaybg / swayidle 进程均已确认在运行

## 手动命令速查

```bash
# 手动锁屏
swaylock -f

# 立即关屏（不解锁）
niri msg action power-off-monitors

# 重新亮屏
niri msg action power-on-monitors

# 重载 niri 配置（不重启会话）
niri msg action load-config-file
```

## 想调整的地方

- 空闲时间：编辑 `~/.config/swayidle/config` 里的数字（单位秒）
- 锁屏图 / 壁纸：分别改 `~/.config/swaylock/config` 和 niri 里 `swaybg` 的 `--image` 路径
- 壁纸缩放：`--mode fill` 可换成 `stretch` / `fit` / `center` / `tile`
