# Edge 无法通过 Clash Verge 代理访问 Google/GitHub —— 排查与解决记录

日期：2026-07-31
环境：Arch Linux / niri（Wayland 滚动平铺合成器）

## 问题现象

- 已运行 Clash Verge 代理，但 Edge 无法访问 Google、GitHub 等网站。
- 打开 `edge://linux-proxy-config/` 提示：

  > 在受支持的桌面环境下运行 Microsoft Edge 时，将使用系统代理设置。但是，你的系统可能不受支持，或者启动系统配置时出现问题。但仍可以通过命令行进行配置。

## 排查过程

1. **确认 Clash 进程在运行**
   - `clash-verge` 与 `verge-mihomo` 进程均存在。

2. **找到实际代理端口（关键）**
   - 默认端口 7890 不通；通过 `ss -tlnp | grep mihomo` 发现实际监听端口是 **7897**。

3. **验证代理本身可用**
   - `curl -x http://127.0.0.1:7897 https://www.google.com` → 302（正常）
   - `curl -x http://127.0.0.1:7897 https://github.com` → 200（正常）
   - 结论：**代理链路完全正常，问题出在浏览器端。**

4. **确认系统代理已正确设置**
   - `gsettings get org.gnome.system.proxy mode` → `manual`
   - http / https 均指向 `127.0.0.1:7897`（由 Clash Verge 写入）。

5. **确认 Edge 自身无覆盖配置**
   - 无企业策略（`/etc/opt/edge/policies/managed` 为空）。
   - Preferences 中无 `proxies` 覆盖项，启动命令行无 `--proxy-server` 参数。

6. **定位根因**
   - 检查桌面环境：`XDG_CURRENT_DESKTOP=niri`，Wayland 会话。
   - Chromium 内核的 Linux 代理服务只识别「受支持的桌面环境」（如 GNOME/KDE 等），**niri 不在其识别列表**，导致 Edge 放弃读取 GSettings 系统代理。
   - 时间线索：Edge 于 19:56 启动，Clash 于 19:58 启动，Edge 启动时系统尚无代理。

## 解决方案（最终采用：修改 desktop 启动器）

在 `~/.local/share/applications/microsoft-edge.desktop` 中为所有 Exec 行追加 `--proxy-server=http://127.0.0.1:7897`：

```bash
mkdir -p ~/.local/share/applications
sed -e 's|Exec=/usr/bin/microsoft-edge-stable |Exec=/usr/bin/microsoft-edge-stable --proxy-server=http://127.0.0.1:7897 |' \
    /usr/share/applications/microsoft-edge.desktop > ~/.local/share/applications/microsoft-edge.desktop
# 补上无参数入口
sed -i 's|Exec=/usr/bin/microsoft-edge-stable$|Exec=/usr/bin/microsoft-edge-stable --proxy-server=http://127.0.0.1:7897|' \
    ~/.local/share/applications/microsoft-edge.desktop
# 刷新菜单缓存
update-desktop-database ~/.local/share/applications
```

修改后的三个入口：

```ini
Exec=/usr/bin/microsoft-edge-stable --proxy-server=http://127.0.0.1:7897 %U
Exec=/usr/bin/microsoft-edge-stable --proxy-server=http://127.0.0.1:7897
Exec=/usr/bin/microsoft-edge-stable --proxy-server=http://127.0.0.1:7897 --inprivate
```

### 使用方式

彻底退出 Edge（`killall msedge`）后，从应用菜单重新启动即可生效。

## 备选方案（未采用，供参考）

1. **命令行临时验证**：`microsoft-edge-stable --proxy-server=http://127.0.0.1:7897`
2. **Clash Verge TUN 模式**：开启后由虚拟网卡接管流量，浏览器完全无需代理设置，任何应用都直接生效，最省心。
3. **环境变量**：启动前 `export http_proxy=http://127.0.0.1:7897 https_proxy=http://127.0.0.1:7897`。

## 注意事项

- Clash Verge 实际端口是 **7897**（非常见默认 7890）。
- 若 Clash 端口变更，需同步修改 desktop 文件中的 `7897`。
- niri 等非主流桌面环境下，Chromium 内核浏览器均可能不读取系统代理，需命令行方式指定。
