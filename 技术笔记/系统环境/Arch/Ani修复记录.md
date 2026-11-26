# Animeko (Ani) 无法加载视频问题 —— 排查与修复记录

日期:2026-08-20
环境:Arch Linux,glib 2.88.3,Ani 6.0.0 (AppImage)

## 一、问题现象

- 打开任意番剧,所有在线视频源均报"加载失败,无法找到视频文件"
- 日志 `~/.local/share/ani/logs/app.log` 中反复出现:

```
JCEFInitializationException: Failed to initialize JCEF, state: TERMINATED
WebViewVideoExtractor: AniCefApp isn't initialized yet.
CEF client is disposed.
MediaResolutionException: Failed to resolve video source: NO_MATCHING_RESOURCE
```

- 播放器自动在 9 个视频源间无限切换,全部失败

## 二、定位过程

### 1. 排除无关因素

- 部分源(yinghua2.com、rebozj.pro 等)本身网络不通 → IO_EXCEPTION,与本次问题无关
- 系统库齐全:libnss3、libatk、libgtk-3、libdrm 等均在
- `/dev/shm` 空间充足,unprivileged userns 已开启

### 2. 复现真实错误

解包 AppImage 后直接运行,stderr 中出现关键报错:

```
jcef_helper: symbol lookup error: /usr/lib/libgobject-2.0.so.0:
undefined symbol: g_string_copy
```

用 App 的 `LD_LIBRARY_PATH` 环境独立复现:

```bash
LD_LIBRARY_PATH=<解包目录>/usr/lib:<解包目录>/usr/lib/app \
  ./jcef_helper --type=browser --no-sandbox
# => symbol lookup error: undefined symbol: g_string_copy
```

## 三、根因

AppImage 内部捆绑了**旧版** `libglib-2.0.so.0`(位于 `usr/lib/app/`),
该版本不导出 `g_string_copy` 符号(glib 2.78 之后才引入)。

App 启动时把 `usr/lib/app` 放入 `LD_LIBRARY_PATH`,导致:

```
系统 glib 2.88 的 libgobject(需要 g_string_copy)
  + 捆绑旧 libglib(不提供该符号)
  = 符号解析失败 → jcef_helper(CEF 浏览器子进程)启动即崩
  → JCEF 状态 TERMINATED → 网页视频源全部无法解析
```

Ani 播放网页视频的流程:CEF 浏览器打开视频页 → 解析出真实视频地址(mp4/m3u8)→ 交给 mpv 播放。
CEF 起不来,这一步永远无法完成,于是所有网页源报 NO_MATCHING_RESOURCE。
这是 AppImage 在 Arch(glib 较新)上的兼容性 bug,与网络和视频源无关。

## 四、修复方案(已实测验证)

### 方案 A:LD_PRELOAD 预加载系统新版 glib(推荐,零修改)

```bash
LD_PRELOAD=/usr/lib/libglib-2.0.so.0 ./ani-6.0.0-linux-x86_64.appimage
```

原理:预加载的库符号在全局符号表最前面,CEF 子进程直接解析到系统 glib 的
`g_string_copy`,绕过捆绑旧库的坑。

已创建启动脚本 `~/ani.sh`:

```bash
#!/bin/bash
export LD_PRELOAD=/usr/lib/libglib-2.0.so.0
exec /home/adorukw/Downloads/ani-6.0.0-linux-x86_64.appimage "$@"
```

以后启动: `~/ani.sh`

### 方案 B:删掉捆绑的旧 libglib 后运行(修改版)

```bash
./ani-6.0.0-linux-x86_64.appimage --appimage-extract
rm squashfs-root/usr/lib/app/libglib-2.0.so.0
./squashfs-root/AppRun
```

注意:此方案会破坏单文件 AppImage 形式;若需保持单文件需用 appimagetool 重新打包。

## 五、验证结果

修复后启动日志关键行:

```
AniCefApp: JCEF is initialized.
Ani: [JCEF init] AniCefApp is initialized.
WebViewVideoExtractor: CEF loading nested page: https://...
```

- JCEF 初始化成功 ✓
- CEF 浏览器正常打开视频页并执行页面 JS ✓
- Anitorrent / FFmpegKit / mediampv / mpv 均正常加载 ✓
- 运行稳定无崩溃 ✓

## 六、注意事项

1. **单实例限制**:Ani 已运行时再启动第二个实例会被单例锁拒绝直接退出,
   看起来像"没反应",先 `pkill -f ani` 再启动
2. **升级新版本时**:新 AppImage 可能已修复此问题,先不加 LD_PRELOAD 试跑;
   若仍报 JCEF TERMINATED 则加上
3. **其他发行版**:Ubuntu/Debian 等 glib 较旧(或与捆绑版接近)的系统一般不受影响,
   此问题主要出现在 glib ≥ 2.78 的较新系统(如 Arch)