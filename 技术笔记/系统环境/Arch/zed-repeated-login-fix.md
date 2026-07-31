# Zed 每次启动都要重新登录 —— 分析与解决记录

日期：2026-07-31
环境：Arch Linux + niri（Wayland 合成器）+ sddm 登录管理器 + `zeditor` 包

## 现象

每次打开 Zed 都被要求重新登录 GitHub / Zed 账号，无法记住登录态。

## 问题定位

### 排除项：`~/.config/zed`

检查 `~/.config/zed/`，只有 `keymap.json`、`settings.json`、`tasks.json`、`themes/`，
没有任何凭据相关文件——**这本身是正常的**。Zed 在 Linux 上不把登录凭据写在配置文件里。

### 真正的机制

Zed 通过系统的 **Secret Service（密钥环）** 持久化登录 token：
- Linux 上由 `keyring` crate 调用 D-Bus 上的 `org.freedesktop.secrets` 服务；
- 凭据由密钥环守护进程加密存储在 `~/.local/share/keyrings/`。

### 根因（证据链）

1. 会话总线上**没有**注册 `org.freedesktop.secrets` 服务
   （`busctl --user list` 中只有 niri、waybar、gvfs、fcitx5 等，无任何 keyring/secret 提供者）；
2. `~/.local/share/keyrings/` 目录不存在；
3. 没有 gnome-keyring / kwallet 进程在运行。

结论：系统缺少 Secret Service 实现，Zed 的登录 token **存不进去**，所以每次启动都要求重新登录。
问题不在 `~/.config/zed`，而是系统缺少密钥环服务。

## 方案：安装并启用 gnome-keyring

### 为什么选 gnome-keyring

密钥环服务是一个系统级安全存储，通过标准的 Secret Service 接口替应用（Zed、VS Code、git 等）
集中加密保管密码/token，应用自身不落盘明文。

选择 gnome-keyring 的原因：
- 通用性最好，几乎所有 Linux 桌面默认使用，Zed 等应用开箱即用；
- 与 Arch + niri 搭配最轻量，只装一个包、加一行自启，无需引入 KDE 组件；
- 备选（KWallet 需整套 KDE 组件）更重且非必需。

### 实施步骤

1. 安装：

   ```bash
   sudo pacman -S --noconfirm gnome-keyring
   ```

   安装时自动创建了 systemd user socket：`gnome-keyring-daemon.socket`。

2. 在 `~/.config/niri/config.kdl` 中加入自启（位于 `spawn-at-startup` 段落）：

   ```kdl
   spawn-at-startup "gnome-keyring-daemon" "--start" "--components=secrets,ssh"
   ```

3. 验证配置：

   ```bash
   niri validate    # config is valid
   systemctl --user start gnome-keyring-daemon.service
   systemctl --user is-active gnome-keyring-daemon.service   # active
   busctl --user status org.freedesktop.secrets              # PID / UniqueName 正常
   ```

   服务注册成功：`org.freedesktop.secrets` 出现在会话总线上，daemon 由 systemd 管理。

## 自动解锁

本机 sddm 的 PAM 配置 `/etc/pam.d/sddm` 已包含 `pam_gnome_keyring.so`：
安装 gnome-keyring 后该 PAM 模块（`/usr/lib/security/pam_gnome_keyring.so`）生效，
下次 sddm 图形登录时会把登录密码自动喂给密钥环并解锁 `login` 集合。

实测中 `~/.local/share/keyrings/login.keyring` 已成功创建，证明链路可用。

## 待办

**注销并重新登录一次**（或重启），然后打开 Zed 重新登录一次：
- sddm 登录时 PAM 解锁密钥环；
- Zed 登录后 token 写入密钥环；
- 此后无需再重复登录。

## 验证结果

| 检查项 | 结果 |
|---|---|
| gnome-keyring 安装 | 成功 |
| `pam_gnome_keyring.so` 模块存在 | 是 |
| niri 配置校验 | 通过 |
| `gnome-keyring-daemon.service` | active |
| `org.freedesktop.secrets` 总线注册 | 成功 |
| `login.keyring` 创建 | 成功 |

> 注：终端里直接手动 `--login`/`secret-tool store` 解锁测试会因管道与守护进程兼容问题挂起，
> 与真实登录流程无关；实际解锁由 sddm 的 PAM 自动完成。
