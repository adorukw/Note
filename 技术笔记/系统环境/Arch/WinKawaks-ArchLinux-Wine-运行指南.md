# Arch Linux 上用 Wine 运行 WinKawaks 完整指南

> 适用环境:Arch Linux (x86_64),Wine 9/10 系列,WinKawaks 中文版(2005 年老街机模拟器)

---

## 1. 背景

- WinKawaks 是 2005 年的 Windows 老程序(VC6 + MFC 编写),Linux 下需要用 **Wine** 运行
- 它的 ROM 是 **FinalBurn 系格式**(按 `cps1` / `cps2` / `neogeo` 子文件夹存放),**不能直接给新版 MAME(如 0.289)用**,校验全部失败
- 三个常见坑:缺 `MFC42.DLL`、中文乱码、中文字体缺失,本文逐一解决

---

## 2. 安装 Wine

```bash
sudo pacman -S wine
```

- 前提:确认 `/etc/pacman.conf` 里 `[multilib]` 已启用(Arch 默认多已启用),否则没有 32 位支持
- 验证:`wine --version`

---

## 3. 修复 "MFC42.DLL not found"

运行 `wine WinKawaks.exe` 报错:

```
err:module:import_dll Library MFC42.DLL (which is needed by L"...WinKawaks.exe") not found
```

原因:WinKawaks 基于 MFC 4.2 编译,启动必须加载 `MFC42.DLL`,而 Wine 不内置该私有运行库。

### 3.1 winetricks 方案(可能因网络失败)

```bash
sudo pacman -S winetricks
winetricks mfc42
```

**在国内大概率失败**:微软官方链接已失效(SSL 证书错误),自动回退的 `web.archive.org` 被墙(连接超时)。

### 3.2 手动方案(推荐,实测有效)

1. 从可访问的渠道下载 `mfc42.dll`(32 位版本,约 974 KB,可用 `file` 验证:`PE32 executable ... Intel i386`)
2. 复制到 WinKawaks 目录,与 `WinKawaks.exe` 同级:

```bash
cp ~/Downloads/mfc42.dll /home/adorukw/AAAPAN/Game/WinKawaks/
```

Windows 程序会优先从自身目录加载 DLL,因此放在 exe 旁边即可生效。

> 提示:下载站附带的 `.url` 快捷方式文件是广告,不要点。

---

## 4. 修复中文乱码(两层问题)

### 4.1 第一层:编码乱码(乱码如 "涓枃")

现象:界面中文是乱码字符。

原因:WinKawaks 中文版用 **GBK(代码页 936)** 读取 `lang/Chinese(Simp).lng` 语言包;Wine 前缀默认 `ACP=1252`(英文),解码错乱。且系统若未生成 `zh_CN.UTF-8` locale(可用 `locale -a` 检查),`LANG=zh_CN.UTF-8` 会静默回退到 C locale。

**解决**:用 GBK locale 启动(系统里已生成 `zh_CN.gbk` 就能用):

```bash
LC_ALL=zh_CN.GBK wine WinKawaks.exe
```

### 4.2 第二层:字体缺失(方块 "口口")

现象:文字不乱码了,但全是方框(缺字形)。

原因:Wine 用没有中文字形的字体(如 Tahoma)渲染,需要把 Windows 字体名替换为系统中已装的中文字体(如 `Noto Sans CJK SC`)。

**解决**:写入 Wine 注册表字体替换(仅影响你的 wine 环境,可撤销):

```bash
# 删除旧配置(如有)
LC_ALL=zh_CN.GBK wine reg delete "HKCU\\Software\\Wine\\Fonts\\Replacements" /f

# 添加替换(注意:含中文键名"宋体"的命令必须带 LC_ALL=zh_CN.GBK,否则键名会写坏)
LC_ALL=zh_CN.GBK wine reg add "HKCU\\Software\\Wine\\Fonts\\Replacements" /v "MS Shell Dlg"   /t REG_SZ /d "Noto Sans CJK SC" /f
LC_ALL=zh_CN.GBK wine reg add "HKCU\\Software\\Wine\\Fonts\\Replacements" /v "MS Shell Dlg 2" /t REG_SZ /d "Noto Sans CJK SC" /f
LC_ALL=zh_CN.GBK wine reg add "HKCU\\Software\\Wine\\Fonts\\Replacements" /v "SimSun"          /t REG_SZ /d "Noto Sans CJK SC" /f
LC_ALL=zh_CN.GBK wine reg add "HKCU\\Software\\Wine\\Fonts\\Replacements" /v "Tahoma"          /t REG_SZ /d "Noto Sans CJK SC" /f
LC_ALL=zh_CN.GBK wine reg add "HKCU\\Software\\Wine\\Fonts\\Replacements" /v $'\xcb\xce\xcc\xe5' /t REG_SZ /d "Noto Sans CJK SC" /f   # "宋体" 的 GBK 字节
```

- 前提:系统已装中文字体:`sudo pacman -S noto-fonts-cjk`(或 `wqy-zenhei`);用 `fc-list :lang=zh family` 查看可用字体名
- 验证键名写对:查看 `~/.wine/user.reg`,宋体应显示为 `"\x5b8b\x4f53"` 而不是乱码
- 改完必须**完全退出 WinKawaks 再重启**才生效

### 4.3 治本(可选):生成 UTF-8 locale

```bash
sudo sed -i 's/^#zh_CN.UTF-8/zh_CN.UTF-8/' /etc/locale.gen
sudo locale-gen
```

之后裸敲 `wine WinKawaks.exe` 也默认用中文代码页(需系统 `LANG=zh_CN.UTF-8`)。

---

## 5. 配置 ROM

WinKawaks 只认**平铺**的 roms 目录(不支持子文件夹):

```bash
# 把 cps1 / cps2 / neogeo 三个文件夹里所有 zip 合并到 roms/
mkdir -p /home/adorukw/AAAPAN/Game/WinKawaks/roms
cp /home/adorukw/AAAPAN/Game/WinKawaks/roms/cps1/*.zip   /home/adorukw/AAAPAN/Game/WinKawaks/roms/
cp /home/adorukw/AAAPAN/Game/WinKawaks/roms/cps2/*.zip   /home/adorukw/AAAPAN/Game/WinKawaks/roms/
cp /home/adorukw/AAAPAN/Game/WinKawaks/roms/neogeo/*.zip /home/adorukw/AAAPAN/Game/WinKawaks/roms/
```

**注意**:`neogeo.zip`(NeoGeo 主板 BIOS)不能丢,NeoGeo 游戏没它启动不了。

---

## 6. 一键启动脚本(可选)

```bash
cat > /home/adorukw/AAAPAN/Game/WinKawaks/start.sh <<'EOF'
#!/bin/bash
cd "$(dirname "$0")"
LC_ALL=zh_CN.GBK exec wine WinKawaks.exe
EOF
chmod +x /home/adorukw/AAAPAN/Game/WinKawaks/start.sh
```

以后直接 `./start.sh` 即可。

---

## 7. FAQ

| 问题 | 原因 | 解决 |
| --- | --- | --- |
| `MFC42.DLL not found` | 缺 VC6 运行库 | 下载 mfc42.dll 放 exe 旁边(见 §3.2) |
| 中文乱码(乱码字符) | 代码页错误(ACP=1252) | `LC_ALL=zh_CN.GBK` 启动(见 §4.1) |
| 中文全是口口 | 缺中文字形/字体未替换 | 装 CJK 字体 + 注册表字体替换(见 §4.2) |
| 首次运行弹 wine-mono | Wine 尝试装 .NET,与游戏无关 | 直接 Cancel;或 `sudo pacman -S wine-mono` 一劳永逸 |
| libEGL / pci id 警告 | NVIDIA 驱动提示,非致命 | 无视 |
| winetricks 下载失败 | 微软链接失效 + archive.org 被墙 | 手动放 DLL(见 §3.2) |

---

## 8. 手柄识别不到(权限问题)

现象:WinKawaks 里找不到手柄(GamePad)。

### 8.1 分层排查

| 层 | 检查方法 | 本机实测结果 |
| --- | --- | --- |
| USB 层 | `lsusb` | ✅ 能看到设备(如 `3537:1093 GameSir-Tegenaria Lite`) |
| 内核层 | `ls /dev/input/`、`lsmod \| grep joydev` | ✅ `/dev/input/js0` 存在、`joydev` 已加载 |
| 权限层 | `ls -l /dev/input/js0`、`groups` | ❌ 设备属 `root:input`(rw-rw----),用户不在 `input` 组 |

**最常见的原因就是权限**:`/dev/input/js0` 只有 root 和 `input` 组成员能读写,Wine 打不开设备,WinKawaks 自然识别不到。

### 8.2 解决

```bash
# 把当前用户加入 input 组(需要 sudo)
sudo usermod -aG input $USER
```

**重要**:加组后必须**注销再登录(或重启)**,组权限才生效。之后不用任何配置,Wine 即可读取手柄。

### 8.3 验证

```bash
cd /home/adorukw/AAAPAN/Game/WinKawaks
LC_ALL=zh_CN.GBK wine joy.cpl   # Windows 游戏控制器面板,能看到手柄即成功
```

### 8.4 在 WinKawaks 里配置

菜单 **选项 → 控制器设置**:启用 GamePad,然后为每个按键绑定手柄按键即可。

---

## 9. 备选方案:FinalBurn Neo(更省事)

这些 ROM 本就是 FinalBurn 系格式,直接装 **FBNeo(Linux 原生)** 最省心:

- 安装:Flatpak 搜 "FinalBurn Neo",或 GitHub fbneo 发布页下 Linux 版
- 配置 `fbneo.ini` 的 rompath,加入三个文件夹:

```
rompath /home/adorukw/AAAPAN/Game/WinKawaks/roms/cps1;/home/adorukw/AAAPAN/Game/WinKawaks/roms/cps2;/home/adorukw/AAAPAN/Game/WinKawaks/roms/neogeo
```

- 无 MFC / 乱码 / 字体等任何问题,NeoGeo 游戏自动使用同目录 `neogeo.zip` 作为 BIOS

---

## 10. 关键命令速查

```bash
# 启动(带中文代码页)
cd /home/adorukw/AAAPAN/Game/WinKawaks
LC_ALL=zh_CN.GBK wine WinKawaks.exe

# 手柄相关
sudo usermod -aG input $USER   # 加 input 组(需重登生效)
LC_ALL=zh_CN.GBK wine joy.cpl  # 验证手柄是否被 Wine 识别

# 查看字体
fc-list :lang=zh family

# 查看 wine 代码页
grep '"ACP"\|"OEMCP"' ~/.wine/system.reg

# 验证 ROM 与 MAME 是否匹配(此处 ROM 是 FB 系,必然 bad,属正常)
mame -verifyroms sf2 -rompath /home/adorukw/AAAPAN/Game/WinKawaks/roms/cps1
```

---

*文档生成日期:2026-08-07,基于实际排障过程整理*
