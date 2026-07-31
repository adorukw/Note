# SDDM 登录界面配置总结

日期：2026-07-31
系统：Arch Linux（KDE / SDDM）

## 背景

SDDM 默认只有三个主题（elarun / maldives / maya），比较朴素。本流程安装第三方主题 `sddm-sugar-dark` 并启用。

## 流程总结

### 1. 安装主题

包在 AUR 上，用 `yay` 安装：

```bash
yay -S sddm-sugar-dark
```

> 注意：本机 `git config --global` 配置了代理 `127.0.0.1:7897`，但当时代理未运行，导致 yay 拉取 AUR 源码失败。可临时绕过代理：
>
> ```bash
> GIT_CONFIG_COUNT=2 \
> GIT_CONFIG_KEY_0=http.proxy GIT_CONFIG_VALUE_0= \
> GIT_CONFIG_KEY_1=https.proxy GIT_CONFIG_VALUE_1= \
> yay -S sddm-sugar-dark
> ```

### 2. 依赖安装（自动）

主题需要以下依赖，yay 会自动处理：

- `qt5-quickcontrols2`
- `qt5-svg`
- `qt5-graphicaleffects`

### 3. 手动配置主题

主题包的安装脚本**不会**自动写入配置，只是打印提示信息，需要手动创建配置文件。

创建 `/etc/sddm.conf.d/theme.conf`：

```ini
[Theme]
Current=sugar-dark

[General]
Numlock=on
```

`/etc/sddm.conf.d/` 目录下的配置会自动被 SDDM 读取（优先级高于 `/etc/sddm.conf`）。

### 4. 验证

- 主题目录：`/usr/share/sddm/themes/sugar-dark/`，包含 `Main.qml`、`Background.jpg`、`Preview.png` 等
- 预览测试：`sddm --test-mode`
- 生效方式：注销或重启后即可看到新登录界面

## 相关文件

| 路径 | 说明 |
|------|------|
| `/etc/sddm.conf.d/theme.conf` | SDDM 主题配置（新建） |
| `/usr/share/sddm/themes/sugar-dark/` | 主题文件（安装产生） |
| `~/.cache/yay/sddm-sugar-dark/` | 构建源码缓存 |

## 其他可选主题

用 yay 搜索：

```bash
yay -Ss sddm-theme
```

常见的有：`sddm-sugar-dark`、`sddm-sugarcandy`、`sddm-astronaut-theme`、`sddm-theme-aerial` 等。
