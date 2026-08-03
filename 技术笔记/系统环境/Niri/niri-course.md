# niri 系统教程：从入门到实战

> 一本面向 Wayland 用户的可滚动平铺式合成器教程
> 全书共 6 章 20 讲，按"基础认知 → 基础使用 → 配置系统 → 视觉个性化 → 进阶功能 → 实战运维"渐进展开。

---

## 课程总览

- **预计讲数**：20 讲（6 章）
- **学习目标**：从零掌握 niri 的安装、使用、配置、个性化、进阶 IPC 与实战运维，能够独立搭建一套可滚动平铺式 Wayland 桌面环境
- **适用读者**：有 Linux 基础、希望尝试新型窗口管理范式的用户；熟悉 i3/Sway/Hyprland 但想了解"可滚动平铺"理念的用户
- **渐进结构**：基础认知 → 基础使用 → 配置系统 → 视觉个性化 → 进阶功能 → 实战运维

## 详细章节目录

### 第 1 章 认识 niri
- 第 1 讲：niri 是什么——Wayland 生态与 niri 的定位
- 第 2 讲：可滚动平铺——niri 的核心设计理念
- 第 3 讲：安装与环境准备

### 第 2 章 基础使用
- 第 4 讲：首次启动与基本操作
- 第 5 讲：窗口管理基础——打开、关闭、聚焦
- 第 6 讲：工作区与列的概念
- 第 7 讲：窗口调整——移动、调整宽度、全屏

### 第 3 章 配置系统
- 第 8 讲：配置文件结构总览（KDL 语言）
- 第 9 讲：键位绑定配置
- 第 10 讲：窗口规则（window rules）
- 第 11 讲：外设配置——输入与输出

### 第 4 章 视觉与个性化
- 第 12 讲：主题、边框与间隙
- 第 13 讲：动画与布局配置
- 第 14 讲：状态栏与通知集成

### 第 5 章 进阶功能
- 第 15 讲：niri 的 IPC 与状态查询
- 第 16 讲：与外部工具集成（waybar、fuzzel 等）
- 第 17 讲：多显示器与输出管理
- 第 18 讲：Xwayland 与兼容性

### 第 6 章 实战与运维
- 第 19 讲：完整配置实战——从零搭建桌面
- 第 20 讲：调试、日志与常见问题排查

---

# 第 1 章 认识 niri

## 第 1 讲：niri 是什么——Wayland 生态与 niri 的定位

### 概念

niri（北欧语意为"近"）是一个**可滚动平铺式（scrollable-tiling）Wayland 合成器**，使用 Rust 语言编写，基于 Smithay 库实现。它既不是浮动式窗口管理器（如 GNOME/Mutter 的传统模式），也不是传统的网格平铺式管理器（如 i3/Sway），而是采用一种独特的"横向无限滚动列"布局范式。

在 Wayland 生态中，niri 与 Sway、Hyprland、River、Cage 等并列，但它的设计哲学更接近 PaperWM（一个 GNOME 扩展），而非 i3 系。理解 niri 的第一步，是把它放在整个 Wayland 合成器谱系中看待。

### 原理

Wayland 协议本身只定义了客户端与合成器之间的通信规范，并不规定窗口如何排列。合成器（compositor）同时承担"显示服务器"和"窗口管理器"两个角色。niri 作为合成器，需要自己实现：

1. **渲染管线**：通过 Smithay 调用 OpenGL/GLES 将窗口纹理合成到屏幕。
2. **输入处理**：接收键盘、鼠标、触摸事件并分发到正确的窗口。
3. **布局算法**：这是 niri 的核心——它把每个工作区视为一条**水平无限长的纸带**，窗口以"列"为单位排列其上，视口只显示纸带的一部分，左右滚动即可浏览所有窗口。

之所以选择 Rust + Smithay，是因为 Rust 的内存安全特性可以避免大量 C 语言合成器中常见的悬垂指针、缓冲区溢出问题，而 Smithay 提供了 Wayland 协议的成熟实现，让 niri 能专注于布局与交互创新。

### 例子

查看你系统上是否已有 niri：

```bash
niri --version
# 输出示例：niri 25.05.1
```

查看 niri 在 Wayland 合成器谱系中的位置：

```bash
# 列出常见 Wayland 合成器
echo "Sway (i3 兼容, 网格平铺)"
echo "Hyprland (动态平铺, 动画丰富)"
echo "River (动态平铺, 基于 wlroots)"
echo "niri (可滚动平铺, 基于 Smithay)"
echo "GNOME/Mutter (浮动式, 完整桌面)"
```

### 总结

- niri 是 Rust 编写的可滚动平铺式 Wayland 合成器，基于 Smithay。
- 它的定位介于传统平铺器与浮动桌面之间，核心是"水平滚动列"布局。
- 理解 niri 要先理解 Wayland 合成器同时是显示服务器和窗口管理器。
- 关键注意：niri 不是 i3 的 Wayland 移植，不要用 i3 思维套用 niri。

---

## 第 2 讲：可滚动平铺——niri 的核心设计理念

### 概念

**可滚动平铺（scrollable tiling）** 是 niri 区别于所有其他合成器的核心范式：每个工作区是一条**水平方向无限延伸的纸带**，窗口以"列（column）"形式从左到右排列；视口（viewport）只显示纸带的一段，通过左右滚动切换可见的列。每个列内部，窗口从上到下垂直堆叠。

### 原理

传统平铺器（i3/Sway/Hyprland）的布局是**有限二维网格**：屏幕被切分成若干矩形区域，窗口填满网格，新增窗口会挤压已有窗口。这种模式在窗口数量多时会出现每个窗口过小、难以阅读的问题。

niri 的设计洞察是：**大多数窗口在内容创作场景下需要接近"全宽"才能舒适阅读**（编辑器、浏览器、终端）。因此 niri 让每个窗口默认占据视口全宽（或预设宽度），多个窗口横向排列，通过滚动而非挤压来容纳更多窗口。这带来三个好处：

1. **窗口尺寸稳定**：新增窗口不会缩小已有窗口，每个窗口始终保持可读宽度。
2. **空间感清晰**：用户始终知道"左边是旧窗口，右边是新窗口"，方向感强。
3. **聚焦即居中**：聚焦某列时，该列自动滚动到视口中央，符合视觉焦点习惯。

列内部的垂直堆叠则用于同一"任务组"的多个相关窗口（例如一个编辑器 + 一个终端 + 一个预览窗口）。

### 例子

用 ASCII 示意一个工作区的布局（`|` 是列分隔，`#` 是窗口）：

```
工作区纸带（水平无限滚动）：
... | 列A | 列B | 列C | 列D | ...
       | 终端 | 编辑器 | 浏览器 | 文档 |
       | 日志 |        |        |       |
       ^---- 当前视口显示 列B 和 列C ----^
```

聚焦列 D 时，纸带自动右滚，列 D 居中：

```
... | 列B | 列C | 列D | 列E | ...
              | 文档 |
              ^-- 视口 --^
```

### 总结

- 可滚动平铺 = 水平无限纸带 + 列内垂直堆叠。
- 核心优势：窗口宽度稳定、方向感清晰、聚焦即居中。
- 列（column）是 niri 的基本布局单元，理解"列"就理解了 niri。
- 常见误区：不要试图在 niri 中实现 i3 式的"四宫格"，那是不同的范式。

---

## 第 3 讲：安装与环境准备

### 概念

niri 是一个独立的 Wayland 合成器，不依赖任何桌面环境（GNOME/KDE）。安装 niri 本身很简单，但要让它成为一个**可用的桌面**，还需要配套工具：终端模拟器、应用启动器、状态栏、锁屏器、壁纸工具等。本讲聚焦 niri 本体的安装与最小可用环境。

### 原理

niri 作为 Wayland 合成器，需要以下系统级依赖才能运行：

1. **DRM/KMS 支持**：内核显卡驱动正常加载，`/dev/dri/card0` 可访问。
2. **libseat / seatd 或 systemd-logind**：用于获取设备会话权限（seat）。
3. **XDG Desktop Portal**：用于 Flatpak 应用、截图、文件选择等现代桌面功能。
4. **字体与图标主题**：否则界面会出现方块字。

niri 官方推荐通过发行版包管理器安装，因为这样能自动处理依赖。从源码编译需要 Rust 工具链和一系列系统库（libseat、libinput、libudev、pipewire、dbus 等）。

### 例子

**Arch Linux（推荐方式）：**

```bash
sudo pacman -S niri
# 配套工具（按需）
sudo pacman -S alacritty fuzzel waybar swaylock swaybg \
    xdg-desktop-portal-gnome xdg-desktop-portal-gtk
```

**从源码编译（适用于无官方包的发行版）：**

```bash
# 安装 Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source "$HOME/.cargo/env"

# 克隆并编译
git clone https://github.com/YaLTeR/niri.git
cd niri
cargo build --release
# 产物在 target/release/niri
sudo cp target/release/niri /usr/local/bin/
```

**生成默认配置文件：**

```bash
mkdir -p ~/.config/niri
# niri 首次启动会自动生成默认配置，也可手动复制
cp /usr/share/doc/niri/default-config.kdl ~/.config/niri/config.kdl
```

**首次启动（在 TTY 中）：**

```bash
# 确保没有其他 X/Wayland 会话占用
niri-session   # 或直接 niri
```

### 总结

- niri 安装本身简单，难点在于配套桌面工具的组装。
- 推荐用发行版包管理器安装以自动处理依赖。
- 从源码编译需要完整 Rust 工具链和系统库。
- 关键注意：首次启动前确保 `/dev/dri` 可访问、seat 权限正常，否则会黑屏。

---

# 第 2 章 基础使用

## 第 4 讲：首次启动与基本操作

### 概念

niri 启动后会进入一个**纯净的桌面**：没有桌面图标、没有任务栏、没有默认壁纸（除非配置）。所有交互通过键盘完成，鼠标主要用于窗口内操作。首次启动时，niri 会显示一个**热键提示浮层（hotkey overlay）**，列出最常用的快捷键，按任意键关闭。

### 原理

niri 遵循"键盘优先"设计哲学，这与 i3/Hyprland 一致。其交互模型基于"修饰键 + 字母"的组合，默认修饰键是 `Super`（即 Windows/Command 键）。niri 选择 Super 而非 Alt，是为了避免与应用内快捷键冲突（Alt 常用于菜单激活）。

热键提示浮层是 niri 的一个贴心设计：它通过 `hotkey-overlay` 配置项控制，默认在首次启动或配置变更后显示，帮助用户快速上手。浮层内容会根据你的实际键位绑定动态生成，因此修改配置后看到的提示总是最新的。

### 例子

**默认配置下的核心键位（Super = Win/Cmd 键）：**

| 操作 | 快捷键 |
|------|--------|
| 打开终端 | `Super + T` |
| 打开应用启动器 | `Super + D` |
| 关闭当前窗口 | `Super + Q` |
| 聚焦左/右列 | `Super + ← / →` 或 `Super + H / L` |
| 聚焦上/下窗口 | `Super + ↑ / ↓` 或 `Super + K / J` |
| 切换工作区 | `Super + 1` ~ `Super + 9` |
| 退出 niri | `Super + Shift + E` |

**验证 niri 是否正常运行：**

```bash
# 在 niri 内打开终端后执行
echo $WAYLAND_DISPLAY   # 应输出 wayland-1 或类似
echo $XDG_SESSION_TYPE  # 应输出 wayland
niri msg version         # 应输出 niri 版本号
```

### 总结

- niri 首次启动是纯净桌面，所有操作靠键盘。
- 默认修饰键是 Super，避免与应用快捷键冲突。
- 热键提示浮层会动态反映你的配置，是学习的好帮手。
- 关键注意：若启动后黑屏，先检查 `journalctl` 或 niri 日志，多为显卡驱动或 seat 权限问题。

---

## 第 5 讲：窗口管理基础——打开、关闭、聚焦

### 概念

窗口管理的三个最基本动作是：**打开（spawn）**、**关闭（close）**、**聚焦（focus）**。在 niri 中，新打开的窗口会作为**新的一列**插入到当前聚焦列的右侧，并自动获得焦点。关闭窗口后，焦点会移动到相邻的列。

### 原理

niri 的窗口插入策略遵循"可滚动平铺"理念：每个新窗口默认占据视口全宽（或预设宽度），不会挤压已有窗口。这与 i3 的"默认分裂当前容器"截然不同——i3 会把当前窗口一分为二，而 niri 是在右侧追加一列。

聚焦机制基于"列优先"原则：左右移动是在列之间切换，上下移动是在同一列内的窗口之间切换。当聚焦变化时，niri 会平滑滚动纸带，使被聚焦的列居中显示。这种"聚焦即居中"的行为是 niri 的标志性体验。

关闭窗口时，niri 会智能选择下一个焦点：通常是被关闭列的左邻列，若左邻不存在则取右邻。这保证了关闭后用户视线无需大幅移动。

### 例子

**打开窗口的几种方式：**

```bash
# 方式1：通过快捷键（默认 Super+T 打开终端）
# 在 niri 配置中定义为：
# binds { Mod+T { spawn "alacritty"; } }

# 方式2：通过应用启动器（默认 Super+D）
# 会调用 fuzzel/wofi/rofi 等

# 方式3：从已打开的终端直接启动程序
alacritty &    # 后台启动一个新终端窗口
firefox &      # 启动 Firefox
```

**聚焦移动示意：**

```
当前状态：聚焦列 B
[ 列A ][ 列B* ][ 列C ][ 列D ]
         ↑焦点

按 Super+L（聚焦右列）：
[ 列A ][ 列B ][ 列C* ][ 列D ]
                ↑焦点，纸带右滚

按 Super+J（聚焦下方窗口，假设列C有多个窗口）：
[ 列C 上窗口 ]
[ 列C 下窗口* ]   ← 焦点下移
```

**关闭窗口：**

```bash
# 默认 Super+Q 关闭当前聚焦窗口
# 也可通过 IPC 命令关闭：
niri msg action close-window
```

### 总结

- 新窗口默认作为新列插入当前列右侧，不挤压已有窗口。
- 聚焦分两级：左右切列，上下切列内窗口。
- "聚焦即居中"是 niri 标志性体验，靠平滑滚动实现。
- 关键注意：某些应用（如 Electron 应用）可能不响应 close 请求，需用 `kill` 强制结束。

---

## 第 6 讲：工作区与列的概念

### 概念

niri 有两个核心组织单元：**工作区（workspace）** 和 **列（column）**。工作区是顶级的容器，每个工作区是一条独立的水平纸带；列是工作区内的窗口分组单元，一列可包含一个或多个垂直堆叠的窗口。理解这两个层级是掌握 niri 布局的关键。

### 原理

**工作区**在 niri 中是"虚拟桌面"的等价物，但有一个重要特性：**工作区绑定到具体的输出（显示器）**。这意味着在多显示器环境下，每个显示器有自己独立的工作区列表，切换显示器不会改变另一个显示器的工作区状态。这与 Sway 的"工作区跨显示器移动"模型不同。

**列**是 niri 布局的基本单元。一列的宽度由其内部最宽的窗口决定（或由窗口规则/预设宽度指定）。列内的多个窗口垂直堆叠，高度按比例分配。你可以把一列理解为"一个任务组"——例如一列放编辑器+终端+日志，它们共同完成一个开发任务。

niri 还有一个独特概念：**consume / expel（吸收/排出）**。`consume-window` 把当前窗口"吸收"进左邻列，使其成为左邻列的一部分；`expel-window` 则把列内非首窗口"排出"成独立的新列。这是 niri 独有的列重组操作，非常强大。

### 例子

**工作区操作：**

```bash
# 切换到工作区 3
niri msg action focus-workspace 3

# 把当前列移动到工作区 5
niri msg action move-column-to-workspace 5

# 默认快捷键：
# Super+1..9  → 切换到工作区 1..9
# Super+Shift+1..9 → 当前列移动到工作区 1..9
```

**列重组（consume / expel）：**

```
初始状态（两个独立列）：
[ 列A: 编辑器 ][ 列B: 终端 ]

执行 consume-window（把列B吸收进列A）：
[ 列A: 编辑器 ]
[       终端 ]   ← 终端现在属于列A

执行 expel-window（把终端排出为新列）：
[ 列A: 编辑器 ][ 列B: 终端 ]   ← 恢复原状
```

对应配置：

```kdl
binds {
    Mod+BracketLeft  { consume-window; }   // 吸收右列到当前列
    Mod+BracketRight { expel-window; }     // 排出当前窗口为新列
}
```

### 总结

- 工作区是顶级容器，绑定到具体显示器，多显示器各自独立。
- 列是布局基本单元，列内窗口垂直堆叠。
- consume/expel 是 niri 独有的列重组操作，灵活强大。
- 关键注意：工作区编号是每个输出独立的，"工作区 3"在不同显示器上是不同的。

---

## 第 7 讲：窗口调整——移动、调整宽度、全屏

### 概念

窗口调整包括：**移动窗口/列的位置**、**调整列宽**、**最大化列**、**全屏窗口**。niri 提供了精细的调整能力，但调整逻辑围绕"列"而非"窗口"展开（除了列内窗口的上下顺序）。

### 原理

**列宽调整**是 niri 的特色功能。每列有三种预设宽度状态，通过 `switch-preset-column-width` 循环切换：

1. **固定宽度（fixed）**：用户手动设定的具体像素宽度。
2. **比例宽度（proportion）**：占视口宽度的比例（如 1/3、1/2、2/3）。
3. **自适应宽度（auto）**：根据窗口内容自动决定。

这种"预设循环"设计避免了用户反复按方向键微调的繁琐，符合 niri"少按键、多效果"的理念。

**最大化（maximize-column）** 让当前列占据整个视口宽度，其他列被推到视口外（但仍存在，可滚动回到）。这与"全屏（fullscreen）"不同：全屏是窗口级，会隐藏所有 niri 装饰并覆盖整个输出；最大化是列级，只影响布局宽度。

**移动操作**分两类：列级移动（左右移动整列在纸带上的位置）和窗口级移动（上下调整列内窗口顺序，或 consume/expel 改变列归属）。

### 例子

**调整列宽：**

```kdl
binds {
    // 循环切换预设宽度
    Mod+F { switch-preset-column-width; }

    // 直接设定比例宽度（占视口 1/3）
    Mod+1 { set-column-width "1/3"; }
    // 占视口 1/2
    Mod+2 { set-column-width "1/2"; }
    // 占视口 2/3
    Mod+3 { set-column-width "2/3"; }

    // 固定像素宽度
    Mod+Shift+F { set-column-width "1200"; }
}
```

**最大化与全屏：**

```kdl
binds {
    Mod+M { maximize-column; }      // 列最大化（占满视口宽）
    Mod+Shift+F { fullscreen-window; } // 窗口全屏（覆盖整个输出）
}
```

**移动列与窗口：**

```kdl
binds {
    Mod+BracketLeft  { move-column-left; }   // 整列左移
    Mod+BracketRight { move-column-right; } // 整列右移
    Mod+Shift+K      { move-window-up; }     // 列内窗口上移
    Mod+Shift+J      { move-window-down; }   // 列内窗口下移
}
```

**居中当前列：**

```kdl
binds {
    Mod+C { center-column; }  // 让当前列滚动到视口正中
}
```

### 总结

- 列宽通过"预设循环"或"直接设定比例/像素"调整，避免繁琐微调。
- 最大化是列级（占满视口宽），全屏是窗口级（覆盖整个输出），二者不同。
- 移动分列级（左右）和窗口级（上下、consume/expel）。
- 关键注意：`set-column-width "1/3"` 是比例字符串，必须用引号；纯数字 `"1200"` 是像素。

---

# 第 3 章 配置系统

## 第 8 讲：配置文件结构总览（KDL 语言）

### 概念

niri 的配置文件位于 `~/.config/niri/config.kdl`，使用 **KDL（Kuhn's Declarative Language）** 语言编写。KDL 是一种类似 JSON 但更人性化的声明式配置语言，采用"节点 + 属性 + 子节点"的树形结构。niri 的配置就是一棵 KDL 树，根节点隐式存在，下面是若干顶级配置块。

### 原理

KDL 的设计目标是"既适合人类阅读，又适合机器解析"。它的语法特点：

1. **节点名 + 花括号**：`input { ... }` 定义一个名为 input 的节点。
2. **属性键值对**：`repeat-rate 50` 设置属性，无需等号（也支持 `key=value`）。
3. **字符串用引号**：`spawn "alacritty"`。
4. **注释用 `//`**：与 C/Rust 一致。
5. **嵌套**：节点可任意嵌套，形成树。

niri 的顶级配置块包括：`input`（输入设备）、`outputs`（输出设备）、`binds`（键位）、`layout`（布局）、`spawn-at-startup`（启动项）、`window-rule`（窗口规则）、`animations`（动画）、`environment`（环境变量）、`cursor`（光标）、`hotkey-overlay`（热键浮层）、`screenshot-path`（截图路径）等。

配置文件修改后**即时生效**，无需重启 niri——这是 niri 的一个亮点，得益于其配置热重载机制。

### 例子

**一个最小可用的配置：**

```kdl
// ~/.config/niri/config.kdl

// 环境变量
environment {
    QT_QPA_PLATFORM "wayland"
    XDG_SESSION_TYPE "wayland"
    DISPLAY ""  // 禁用 X11 默认显示
}

// 启动项
spawn-at-startup "waybar"
spawn-at-startup "swaybg" "-i" "~/Pictures/wallpaper.jpg"
spawn-at-startup "xwayland-satellite"  // 提供 Xwayland 兼容

// 输入设备
input {
    keyboard {
        xkb {
            layout "us"
            variant ""
        }
        repeat-delay 250
        repeat-rate 50
    }
    touchpad {
        tap true
        natural-scroll true
    }
}

// 键位绑定
binds {
    Mod+T { spawn "alacritty"; }
    Mod+Q { close-window; }
    Mod+Shift+E { quit; }
}

// 布局
layout {
    gap 8
    focus-ring {
        width 2
        active-color "#7aa2f7"
        inactive-color "#3b4261"
    }
}
```

**验证配置语法：**

```bash
niri validate   # 检查 config.kdl 语法和逻辑错误
```

### 总结

- niri 配置用 KDL 语言，位于 `~/.config/niri/config.kdl`。
- KDL 是"节点+属性"的树形结构，语法类似 Rust 注释风格。
- 配置修改即时热重载，无需重启。
- 关键注意：用 `niri validate` 检查配置，避免语法错误导致 niri 行为异常。

---

## 第 9 讲：键位绑定配置

### 概念

`binds` 块是 niri 配置中使用频率最高的部分，它定义了所有快捷键到动作（action）的映射。每条绑定的格式是 `修饰键+键名 { 动作; }`。niri 的动作系统非常丰富，涵盖窗口管理、工作区切换、布局调整、媒体控制等。

### 原理

niri 的键位绑定解析遵循以下规则：

1. **修饰键**：`Mod`（默认 Super，可在 input 中改）、`Shift`、`Ctrl`、`Alt`。
2. **键名**：使用 xkb 键名规范，如字母 `T`、方向键 `Left`/`Right`/`Up`/`Down`、功能键 `F1`~`F12`、特殊键 `Space`/`Tab`/`Return`。
3. **多动作**：一个绑定可触发多个动作，按顺序执行。
4. **重复触发**：长按重复键时，`repeat` 控制是否重复触发动作（默认对方向键等开启）。

niri 的动作（action）是命名良好的函数，如 `spawn`（启动程序）、`close-window`、`focus-column-left`、`focus-workspace`、`move-column-to-workspace`、`switch-preset-column-width`、`consume-window`、`fullscreen-window` 等。完整列表可通过 `niri msg action --help` 或官方文档查阅。

一个重要特性：`focus-workspace` 接受数字参数，`move-column-to-workspace` 也接受数字参数，因此可以为工作区 1~9 各定义一组切换/移动绑定。

### 例子

**完整的常用键位绑定：**

```kdl
binds {
    // ===== 应用启动 =====
    Mod+T { spawn "alacritty"; }
    Mod+D { spawn "fuzzel"; }
    Mod+E { spawn "thunar"; }
    Mod+B { spawn "firefox"; }
    Print { spawn "grim" "-g" "$(slurp)" "/tmp/screenshot.png"; }

    // ===== 窗口管理 =====
    Mod+Q { close-window; }
    Mod+Shift+Slash { toggle-window-floating; }  // 切换浮动模式
    Mod+F { switch-preset-column-width; }
    Mod+M { maximize-column; }
    Mod+Shift+F { fullscreen-window; }
    Mod+C { center-column; }

    // ===== 聚焦移动 =====
    Mod+H      { focus-column-left; }
    Mod+L      { focus-column-right; }
    Mod+K      { focus-window-up; }
    Mod+J      { focus-window-down; }
    Mod+Left   { focus-column-left; }
    Mod+Right  { focus-column-right; }
    Mod+Up     { focus-window-up; }
    Mod+Down   { focus-window-down; }

    // ===== 列/窗口移动 =====
    Mod+Shift+H { move-column-left; }
    Mod+Shift+L { move-column-right; }
    Mod+Shift+K { move-window-up; }
    Mod+Shift+J { move-window-down; }

    // ===== 列重组 =====
    Mod+BracketLeft  { consume-window; }
    Mod+BracketRight { expel-window; }

    // ===== 工作区切换 =====
    Mod+1 { focus-workspace 1; }
    Mod+2 { focus-workspace 2; }
    Mod+3 { focus-workspace 3; }
    Mod+4 { focus-workspace 4; }
    Mod+5 { focus-workspace 5; }
    Mod+6 { focus-workspace 6; }
    Mod+7 { focus-workspace 7; }
    Mod+8 { focus-workspace 8; }
    Mod+9 { focus-workspace 9; }

    // ===== 当前列移动到工作区 =====
    Mod+Shift+1 { move-column-to-workspace 1; }
    Mod+Shift+2 { move-column-to-workspace 2; }
    Mod+Shift+3 { move-column-to-workspace 3; }
    // ... 以此类推

    // ===== 音量控制（调用 pactl）=====
    XF86AudioRaiseVolume { spawn "pactl" "set-sink-volume" "@DEFAULT_SINK@" "+5%"; }
    XF86AudioLowerVolume { spawn "pactl" "set-sink-volume" "@DEFAULT_SINK@" "-5%"; }
    XF86AudioMute        { spawn "pactl" "set-sink-mute"   "@DEFAULT_SINK@" "toggle"; }

    // ===== 退出 =====
    Mod+Shift+E { quit; }
}
```

### 总结

- `binds` 块定义所有快捷键，格式为 `修饰键+键名 { 动作; }`。
- 动作系统丰富，覆盖窗口、工作区、布局、媒体等。
- 工作区切换/移动需为每个数字单独定义绑定。
- 关键注意：`Mod` 默认是 Super，若与系统冲突可在 input 中改为 Alt，但要注意应用快捷键冲突。

---

## 第 10 讲：窗口规则（window rules）

### 概念

**窗口规则（window-rule）** 允许你针对特定应用（通过 app-id 或标题匹配）定制行为，例如：默认浮动、默认全屏、指定列宽、禁用边框、固定到某工作区等。这是 niri 实现"按应用定制"的核心机制，类似于 Sway 的 `assign` + `for_window`，但更强大。

### 原理

niri 的窗口规则通过**匹配器（matchers）**筛选窗口。匹配器基于窗口的两个属性：

1. **app-id**：应用的 Wayland app_id（类似 X11 的 WM_CLASS），如 `firefox`、`Alacritty`、`org.gnome.Nautilus`。
2. **title**：窗口标题（正则匹配）。

一个 `window-rule` 可包含多个 `match` 块（OR 关系，任一匹配即生效），并指定一组动作属性。规则按配置文件中的顺序求值，**第一个匹配的规则生效**（后续规则不再应用），除非使用 `block-out-from` 等特殊属性。

窗口规则支持的关键属性包括：`default-column-width`（默认列宽）、`default-window-height`、`open-floating`（浮动打开）、`open-fullscreen`、`open-maximized`、`focus-ring`/`border`（覆盖布局默认值）、`draw-border-with-background`、`block-out-from`（截图屏蔽，用于密码管理器等敏感窗口）等。

### 例子

**常见窗口规则配置：**

```kdl
// 1. 文件管理器、计算器等小工具默认浮动
window-rule {
    match app-id="org.gnome.Nautilus"
    match app-id="org.gnome.Calculator"
    match app-id="pavucontrol"
    open-floating true
    default-column-width "600"
    default-window-height "400"
}

// 2. Firefox 默认占视口 1/2 宽
window-rule {
    match app-id="firefox"
    default-column-width "1/2"
}

// 3. 终端默认占视口 1/3 宽
window-rule {
    match app-id="Alacritty"
    default-column-width "1/3"
}

// 4. 图片查看器默认浮动且无装饰
window-rule {
    match app-id="org.gnome.Loupe"
    open-floating true
    border {
        off
    }
    focus-ring {
        off
    }
}

// 5. 密码管理器屏蔽截图（安全）
window-rule {
    match app-id="org.keepassxc.KeePassXC"
    block-out-from "screencast"  // 截图/录屏时显示黑块
}

// 6. 游戏/视频默认全屏
window-rule {
    match title=".*- Steam"
    match app-id="mpv"
    open-fullscreen true
}
```

**查看当前所有窗口的 app-id：**

```bash
niri msg windows --json | jq '.[].app-id'
```

### 总结

- 窗口规则通过 app-id 和 title 匹配，按顺序求值，首个匹配生效。
- 可控制浮动、全屏、列宽、边框、截图屏蔽等。
- `block-out-from` 是安全特性，用于密码管理器等敏感窗口。
- 关键注意：app-id 大小写敏感，用 `niri msg windows` 查看实际值，避免猜错。

---

## 第 11 讲：外设配置——输入与输出

### 概念

`input` 块配置键盘、触摸板、鼠标、手写板等输入设备；`outputs` 块配置显示器（输出设备）。这两个块决定了 niri 如何与硬件交互，是搭建多显示器、笔记本触控板环境的基础。

### 原理

**输入设备**方面，niri 通过 libinput 处理所有输入。键盘配置基于 xkbcommon，支持布局（layout）、变体（variant）、选项（options，如 CapsLock 改 Ctrl）。触摸板支持点击（tap）、自然滚动（natural-scroll）、禁用打字时触摸板（dwt）等。鼠标可配置加速、自然滚动。手写板（tablet）支持映射到特定输出。

**输出设备**方面，niri 的 `outputs` 块采用"声明式"模型：你声明期望的输出状态（分辨率、刷新率、位置、缩放），niri 负责应用。这与 Sway 的 `output` 命令类似，但 niri 支持按显示器型号（make/model/serial）匹配，而非仅按连接名（如 HDMI-A-1），这样插拔时配置更稳定。

输出配置支持的关键属性：`mode`（分辨率@刷新率）、`scale`（缩放因子，用于 HiDPI）、`position`（相对于其他输出的坐标）、`transform`（旋转：normal/90/180/270/flipped）、`variable-refresh-rate`（VRR 可变刷新率）。

### 例子

**输入设备配置：**

```kdl
input {
    keyboard {
        xkb {
            layout "us,cn"        // 双布局：英文+中文
            variant ","
            options "caps:ctrl_modifier"  // CapsLock 改为 Ctrl
        }
        repeat-delay 250    // 按键重复延迟（毫秒）
        repeat-rate 50      // 每秒重复次数
        track-layout "window"  // 每个窗口独立布局
    }

    touchpad {
        tap true                  // 单指点击=左键
        dwt true                  // 打字时禁用触摸板
        dwtp true                 // 鼠标点击时禁用触摸板
        natural-scroll true       // 自然滚动（macOS 风格）
        click-method "button-areas"  // 点击区域方式
        accel-profile "flat"      // 无鼠标加速（游戏/精确场景）
        scroll-method "two-finger"
    }

    mouse {
        natural-scroll false
        accel-speed 0.0
    }

    // 按设备名精确配置（多设备场景）
    // 用 libinput list-devices 查看设备名
    touchpad "PIXA3854:00 093A:0274 Touchpad" {
        tap true
        natural-scroll true
    }

    tablet {
        map-to-output "eDP-1"  // 手写板映射到内置屏幕
    }
}

// 环境变量（输入法等）
environment {
    QT_IM_MODULE "fcitx"
    XMODIFIERS "@im=fcitx"
    INPUT_METHOD "fcitx"
}
```

**输出设备配置：**

```kdl
outputs {
    // 内置屏幕（笔记本）
    output "eDP-1" {
        mode "1920x1080@60Hz"
        scale 1.0
        position x=0 y=0
        variable-refresh-rate true
    }

    // 外接显示器（按型号匹配，更稳定）
    output "Dell Inc. DELL U2720Q XXXXXXX" {
        mode "3840x2160@60Hz"
        scale 1.5          // HiDPI 缩放
        position x=1920 y=0  // 放在内置屏幕右侧
        transform "normal"
    }

    // 投影仪/电视（竖屏旋转）
    output "HDMI-A-1" {
        mode "1920x1080@60Hz"
        transform "90"      // 顺时针旋转 90 度
    }
}
```

**查看可用输出设备名：**

```bash
niri msg outputs --json | jq '.[].name'
# 或
swaymsg -t get_outputs  # 若装了 swaymsg
```

### 总结

- 输入配置基于 libinput + xkbcommon，支持键盘布局、触摸板手势、手写板映射。
- 输出配置是声明式，按型号匹配比按连接名更稳定。
- HiDPI 用 `scale` 缩放，VRR 用 `variable-refresh-rate` 开启。
- 关键注意：多显示器位置用 `position x= y=` 相对定位，坐标系原点在左上角。

---

# 第 4 章 视觉与个性化

## 第 12 讲：主题、边框与间隙

### 概念

niri 的视觉个性化通过 `layout` 块配置，核心元素包括：**gap（窗口间隙）**、**focus-ring（聚焦环）**、**border（边框）**、**struts（屏幕边距）**、**shadow（阴影）**、`tab-indicator`（标签指示器）。这些元素共同决定 niri 桌面的"质感"。

### 原理

niri 的视觉设计遵循"克制而精致"的原则。与 Hyprland 的炫酷特效不同，niri 默认只提供必要的视觉反馈：

1. **gap**：窗口之间的间距，让布局有呼吸感。可分别设置 `gap`（窗口间）和 `gaps`（屏幕边缘）。
2. **focus-ring**：当前聚焦窗口周围的彩色环，是 niri 最显眼的焦点指示。支持 active/inactive 两种颜色。
3. **border**：所有窗口的边框，比 focus-ring 更细，用于非聚焦窗口的轮廓。
4. **struts**：屏幕边缘的保留区域（类似 GNOME 的 strut），用于避开状态栏、dock。
5. **shadow**：窗口阴影，增加层次感，可配置半径、颜色、偏移。

focus-ring 和 border 是互斥的——通常二选一。focus-ring 更醒目（适合键盘流），border 更低调（适合鼠标流）。

### 例子

**完整的视觉配置：**

```kdl
layout {
    // 窗口间隙
    gap 8

    // 屏幕边缘保留（避开顶部 waybar）
    struts {
        left 0
        right 0
        top 30     // 顶部留 30px 给状态栏
        bottom 0
    }

    // 聚焦环（推荐方式）
    focus-ring {
        width 3
        active-color "#7aa2f7"      // 聚焦时蓝色（Tokyo Night 风格）
        inactive-color "#3b4261"    // 非聚焦时深灰
        active-gradient from="#7aa2f7" to="#bb9af7" angle=45  // 渐变
    }

    // 边框（与 focus-ring 二选一，这里注释掉）
    // border {
    //     width 1
    //     active-color "#7aa2f7"
    //     inactive-color "#3b4261"
    // }

    // 窗口阴影
    shadow {
        on
        softness 30
        spread 5
        offset x=0 y=5
        color "#00000080"     // 半透明黑
        inactive-color "#00000040"
    }

    // 标签指示器（浮动窗口的标签）
    tab-indicator {
        on
        place-within-column true
        active-color "#7aa2f7"
        inactive-color "#3b4261"
        width 4
        length 100
        position "left"
    }

    // 窗口标题栏（默认关闭，niri 倾向无装饰）
    // prefer-no-csd true  // 在顶级配置，禁用客户端装饰
}

// 顶级：禁用客户端装饰（CSD），统一由 niri 绘制边框
prefer-no-csd true

// 光标主题
cursor {
    xcursor-theme "Bibata-Modern-Classic"
    xcursor-size 24
}
```

**应用主题（通过环境变量传递给 GTK/Qt 应用）：**

```kdl
environment {
    GTK_THEME "Tokyo-Night:dark"
    QT_QPA_PLATFORMTHEME "qt5ct"
    ICON_THEME "Papirus-Dark"
}
```

### 总结

- 视觉个性化集中在 `layout` 块：gap、focus-ring、border、struts、shadow。
- focus-ring 和 border 二选一，前者醒目后者低调。
- `prefer-no-csd` 统一禁用客户端装饰，由 niri 绘制边框，视觉更一致。
- 关键注意：颜色用十六进制 `#RRGGBB` 或 `#RRGGBBAA`（带透明度），渐变用 `from= to= angle=`。

---

## 第 13 讲：动画与布局配置

### 概念

niri 内置了一套流畅的动画系统，通过 `animations` 块配置。动画涵盖窗口打开/关闭、聚焦切换、工作区切换、窗口移动等场景。同时，`layout` 块还控制一些行为级配置，如 `center-focused-column`（聚焦列是否居中）、`preset-column-widths`（预设宽度列表）。

### 原理

niri 的动画系统基于"插值"：当布局发生变化（如聚焦切换、窗口打开），niri 会在动画时长内对窗口位置/大小做线性或缓动插值，产生平滑过渡。

动画配置分两层：

1. **全局开关与缓动**：`animations { off; }` 关闭所有动画；`easing { duration-ms 250; curve "ease-out-cubic"; }` 设置默认缓动。
2. **具体动画**：`window-open`、`window-close`、`window-movement`、`window-resize`、`workspace-switch`、`horizontal-view-shift` 等，可单独开关和设置缓动。

`center-focused-column` 是 niri 的一个重要行为开关：开启时，聚焦列始终滚动到视口正中（默认行为）；关闭时，聚焦列只在"快出视口"时才滚动，更接近传统平铺器的体验。

`preset-column-widths` 允许你自定义 `switch-preset-column-width` 循环的宽度列表，例如 `[1/3, 1/2, 2/3, 1]`，比默认更灵活。

### 例子

**动画配置：**

```kdl
animations {
    // 全局开关（注释则开启）
    // off

    // 默认缓动
    easing {
        duration-ms 250
        curve "ease-out-cubic"
    }

    // 窗口打开动画
    window-open {
        on
        easing {
            duration-ms 200
            curve "ease-out-expo"
        }
    }

    // 窗口关闭动画
    window-close {
        on
        easing {
            duration-ms 200
            curve "ease-in-cubic"
        }
    }

    // 窗口移动动画（聚焦切换时的滚动）
    window-movement {
        on
        easing {
            duration-ms 300
            curve "ease-out-quint"
        }
    }

    // 工作区切换动画
    workspace-switch {
        on
        easing {
            duration-ms 250
            curve "ease-out-cubic"
        }
    }

    // 水平视图滚动（聚焦列变化时纸带的滚动）
    horizontal-view-shift {
        on
        easing {
            duration-ms 300
            curve "ease-out-quad"
        }
    }
}
```

**布局行为配置：**

```kdl
layout {
    gap 8

    // 聚焦列居中行为
    center-focused-column "on-overflow"
    // 可选值：
    //   "never"      - 从不自动居中
    //   "on-overflow" - 仅当列快出视口时才居中（默认）
    //   "always"     - 总是居中

    // 自定义预设宽度循环列表
    preset-column-widths {
        proportion 1/3
        proportion 1/2
        proportion 2/3
        proportion 1.0    // 全宽
    }

    // 默认列宽（新窗口的初始宽度）
    default-column-width "1/2"
    // 也可设为固定像素："800"

    // 默认窗口高度（列内多窗口时）
    default-window-height "800"
}
```

### 总结

- 动画系统基于插值，分全局缓动和具体动画两层配置。
- `center-focused-column` 控制聚焦列居中策略，影响滚动体验。
- `preset-column-widths` 自定义宽度循环列表，比默认更灵活。
- 关键注意：动画时长不宜过长（>400ms 会显得拖沓），250ms 左右最舒适。

---

## 第 14 讲：状态栏与通知集成

### 概念

niri 本身不提供状态栏、通知、锁屏等功能，而是通过**外部工具**实现。最常用的组合是：**waybar**（状态栏）+ **mako/dunst**（通知）+ **swaylock**（锁屏）+ **swaybg**（壁纸）+ **fuzzel**（应用启动器）。niri 通过 IPC 和环境变量与这些工具协作。

### 原理

niri 与外部工具的集成依赖三个机制：

1. **Wayland 协议层**：niri 实现了 `wlr-layer-shell`（层表面，用于状态栏）、`wlr-foreign-toplevel`（窗口列表，用于任务栏）、`ext-idle-notify`（空闲通知，用于锁屏）等协议，使支持这些协议的工具能直接工作。
2. **IPC 层**：niri 提供 `niri msg` 命令和 `event-stream`（JSON 事件流），让 waybar 等工具能实时获取 niri 状态（当前工作区、窗口列表、聚焦变化）。
3. **环境变量层**：niri 设置 `WAYLAND_DISPLAY`、`XDG_CURRENT_DESKTOP` 等变量，让工具知道运行在 Wayland/niri 下。

waybar 是最流行的状态栏，它通过自定义模块调用 `niri msg` 获取状态。niri 官方提供了 waybar 配置示例，支持工作区切换、窗口标题显示、布局指示等。

### 例子

**waybar 配置（`~/.config/waybar/config`）：**

```json
{
    "layer": "top",
    "position": "top",
    "height": 30,
    "modules-left": ["niri/workspaces", "niri/window"],
    "modules-center": ["clock"],
    "modules-right": ["pulseaudio", "network", "battery", "tray"],

    "niri/workspaces": {
        "format": "{index}"
    },
    "niri/window": {
        "format": "{}",
        "max-length": 50
    },
    "clock": {
        "format": " {:%Y-%m-%d %H:%M}",
        "tooltip-format": "<tt>{calendar}</tt>"
    },
    "pulseaudio": {
        "format": "{icon} {volume}%",
        "format-muted": " Muted",
        "format-icons": ["", "", ""]
    },
    "battery": {
        "format": "{icon} {capacity}%",
        "format-icons": ["", "", "", "", ""]
    }
}
```

**waybar 样式（`~/.config/waybar/style.css`）：**

```css
* {
    font-family: "Noto Sans CJK SC", "Symbols Nerd Font";
    font-size: 14px;
}

window#waybar {
    background-color: #1a1b26;
    color: #c0caf5;
}

#workspaces button {
    padding: 0 10px;
    color: #565f89;
}
#workspaces button.focused {
    color: #7aa2f7;
    border-bottom: 2px solid #7aa2f7;
}

#clock { padding: 0 15px; }
#pulseaudio { padding: 0 10px; color: #bb9af7; }
#battery { padding: 0 10px; color: #9ece6a; }
```

**通知守护进程（mako）配置（`~/.config/mako/config`）：**

```ini
font=Noto Sans CJK SC 12
background-color=#1a1b26
text-color=#c0caf5
border-color=#7aa2f7
border-size=2
border-radius=8
padding=10
margin=10
width=400
default-timeout=5000
```

**niri 中启动这些工具：**

```kdl
spawn-at-startup "waybar"
spawn-at-startup "mako"
spawn-at-startup "swaybg" "-i" "~/Pictures/wallpaper.jpg" "-m" "fill"
spawn-at-startup "swayidle" "-w" \
    "timeout" "300" "niri msg action lock-screen" \
    "timeout" "600" "niri msg action power-off-monitors" \
    "before-sleep" "niri msg action lock-screen"
```

### 总结

- niri 不内置状态栏/通知/锁屏，通过 waybar/mako/swaylock 等外部工具实现。
- 集成依赖 wlr 协议、IPC、环境变量三层机制。
- waybar 通过 `niri/workspaces`、`niri/window` 模块获取 niri 状态。
- 关键注意：`swayidle` 配合 `niri msg action lock-screen` 实现自动锁屏，注意超时时间设置合理。

---

# 第 5 章 进阶功能

## 第 15 讲：niri 的 IPC 与状态查询

### 概念

niri 提供了一套强大的 **IPC（进程间通信）** 系统，通过 `niri msg` 命令暴露。IPC 允许外部脚本和工具查询 niri 状态（窗口列表、工作区、输出、聚焦窗口）并触发动作（执行任意 niri 动作）。这是 niri 与 waybar、脚本自动化、自定义控制面板集成的核心接口。

### 原理

niri 的 IPC 基于 Unix 域套接字（socket），路径通常为 `$XDG_RUNTIME_DIR/niri IPC.sock`。`niri msg` 命令是这个套接字的客户端封装，提供两类操作：

1. **查询命令**：`windows`、`outputs`、`workspaces`、`focused-window`、`focused-output`、`version`。返回 JSON 格式数据，便于脚本解析。
2. **动作命令**：`action <动作名> [参数]`，执行任意 niri 动作，等价于按快捷键。
3. **事件流**：`event-stream`，持续输出 JSON 事件（窗口变化、聚焦变化、工作区切换等），用于实时监控。

IPC 的设计让 niri 极易被脚本化。例如，你可以写一个脚本"把当前 Firefox 窗口移动到工作区 3"，或"统计当前打开的窗口数量"。

### 例子

**查询命令：**

```bash
# 查看 niri 版本
niri msg version

# 列出所有窗口（JSON）
niri msg windows --json
# 人类可读格式
niri msg windows

# 列出所有输出（显示器）
niri msg outputs

# 列出所有工作区
niri msg workspaces

# 查看当前聚焦窗口
niri msg focused-window

# 查看当前聚焦输出
niri msg focused-output
```

**用 jq 解析窗口列表：**

```bash
# 统计当前窗口数量
niri msg windows --json | jq 'length'

# 列出所有窗口的 app-id
niri msg windows --json | jq '.[].app-id'

# 找出 Firefox 窗口所在的工作区
niri msg windows --json | \
    jq '.[] | select(.app-id=="firefox") | .workspace-id'

# 查看当前聚焦窗口的标题
niri msg focused-window --json | jq '.title'
```

**动作命令：**

```bash
# 执行任意动作（等价于快捷键）
niri msg action focus-column-left
niri msg action focus-workspace 3
niri msg action close-window
niri msg action fullscreen-window
niri msg action spawn "alacritty"

# 移动当前列到工作区 5
niri msg action move-column-to-workspace 5
```

**事件流（实时监控）：**

```bash
# 持续输出事件（Ctrl+C 退出）
niri msg event-stream
# 输出示例：
# {"WindowOpenedOrChangedFocus":{"window":{...}}}
# {"WorkspaceActivated":{"workspace_id":3,"focused":true}}
# {"WindowClosed":{"id":42}}

# 用 jq 过滤特定事件
niri msg event-stream | jq --unbuffered 'select(.WorkspaceActivated)'
```

**实用脚本：把 Firefox 移到工作区 3：**

```bash
#!/bin/bash
# move-firefox-to-ws3.sh
niri msg action focus-workspace 3
# 然后通过窗口规则或 IPC 移动
# 更精确的方式：用 niri msg action 配合窗口规则
```

### 总结

- IPC 通过 `niri msg` 命令暴露，基于 Unix 域套接字。
- 查询命令返回 JSON，便于 jq 解析；动作命令执行任意 niri 动作。
- `event-stream` 提供实时事件流，是自动化集成的关键。
- 关键注意：`--json` 标志输出机器可读格式，不加则输出人类可读格式。

---

## 第 16 讲：与外部工具集成（waybar、fuzzel 等）

### 概念

niri 的"积木式"哲学意味着它只做合成器，其他功能交给专业工具。本讲深入 niri 与常用工具的集成：**fuzzel**（应用启动器）、**wlogout/swaylock**（锁屏/退出菜单）、**grim+slurp**（截图）、**wl-clipboard**（剪贴板）、**cliphist**（剪贴板历史）、**walker/wofi**（替代启动器）。

### 原理

这些工具大多遵循 `wlroots` 生态的协议标准，niri 实现了这些协议，因此工具无需针对 niri 特殊适配即可工作。集成方式分三类：

1. **协议级集成**：工具直接通过 Wayland 协议与 niri 通信，如 fuzzel 通过 `layer-shell` 显示为浮层。
2. **IPC 级集成**：工具调用 `niri msg` 获取状态或触发动作，如 waybar 的工作区模块。
3. **启动级集成**：通过 `spawn-at-startup` 在 niri 启动时拉起工具，如 swaybg、swayidle。

截图工具 grim+slurp 是典型组合：slurp 让用户框选区域，grim 截取该区域。niri 实现了 `wlr-screencopy` 协议使 grim 能工作，同时 niri 自身也提供 `screenshot` 动作作为便捷替代。

### 例子

**应用启动器（fuzzel）：**

```bash
# 安装
sudo pacman -S fuzzel

# 配置 ~/.config/fuzzel/fuzzel.ini
cat > ~/.config/fuzzel/fuzzel.ini << 'EOF'
[main]
font=Noto Sans CJK SC:size=14
terminal=alacritty -e
width=40
lines=10
horizontal-pad=20
vertical-pad=20
inner-pad=5
prompt="❯ "
layer=overlay

[colors]
background=1a1b26ff
text=c0caf5ff
match=7aa2f7ff
selection=33467cff
selection-text=c0caf5ff
border=7aa2f7ff
EOF

# niri 中绑定
# binds { Mod+D { spawn "fuzzel"; } }
```

**截图（grim + slurp）：**

```bash
sudo pacman -S grim slurp wl-clipboard

# 全屏截图
grim ~/Pictures/screenshot-$(date +%s).png

# 区域截图（框选）
grim -g "$(slurp)" ~/Pictures/screenshot-$(date +%s).png

# 截图到剪贴板
grim -g "$(slurp)" - | wl-copy

# niri 内置截图动作（更便捷）
# binds { Print { screenshot; } }
# binds { Ctrl+Print { screenshot-screen; } }
# binds { Shift+Print { screenshot-window; } }
```

**剪贴板历史（wl-clipboard + cliphist）：**

```bash
sudo pacman -S wl-clipboard cliphist

# 启动时启用剪贴板监听
# 在 niri 配置中：
# spawn-at-startup "wl-paste" "--watch" "cliphist" "store"

# 通过 fuzzel 选择历史剪贴板
cat >> ~/.config/niri/config.kdl << 'EOF'
binds {
    Mod+V { spawn "sh" "-c" "cliphist list | fuzzel -d | cliphist decode | wl-copy"; }
}
EOF
```

**锁屏与退出菜单（swaylock + wlogout）：**

```bash
sudo pacman -S swaylock wlogout

# wlogout 配置（电源菜单）
mkdir -p ~/.config/wlogout
cat > ~/.config/wlogout/layout << 'EOF'
{
    "label" : "lock",
    "action" : "niri msg action lock-screen",
    "text" : "锁屏",
    "keybind" : "l"
}
{
    "label" : "logout",
    "action" : "niri msg action quit",
    "text" : "退出",
    "keybind" : "e"
}
{
    "label" : "shutdown",
    "action" : "systemctl poweroff",
    "text" : "关机",
    "keybind" : "s"
}
{
    "label" : "reboot",
    "action" : "systemctl reboot",
    "text" : "重启",
    "keybind" : "r"
}
EOF

# niri 绑定
# binds { Mod+Shift+P { spawn "wlogout"; } }
```

**swaylock 配置（`~/.config/swaylock/config`）：**

```ini
ignore-empty-password
indicator-caps-lock
line-uses-ring
color=1a1b26ff
inside-color=1a1b26ff
ring-color=7aa2f7ff
key-hl-color=bb9af7ff
text-color=c0caf5ff
```

### 总结

- niri 通过 wlr 协议与 fuzzel、grim、swaylock 等工具无缝集成。
- 截图可用 grim+slurp 或 niri 内置 `screenshot` 动作。
- 剪贴板历史用 wl-clipboard + cliphist + fuzzel 组合。
- 关键注意：wlogout 的 action 通过 `niri msg action` 调用 niri，而非直接退出进程。

---

## 第 17 讲：多显示器与输出管理

### 概念

niri 的多显示器模型与 Sway/Hyprland 有显著不同：**每个输出（显示器）有独立的工作区列表**，工作区不跨输出移动。这意味着"工作区 3"在笔记本屏幕和外接显示器上是两个不同的工作区。本讲深入多显示器的配置、工作区管理、输出热插拔。

### 原理

niri 选择"工作区绑定输出"模型的原因是：可滚动平铺范式下，工作区是"水平纸带"，而纸带的宽度取决于输出宽度。如果工作区跨输出移动，纸带宽度会变化，导致窗口布局错乱。因此 niri 让每个输出维护自己的工作区列表，切换输出时聚焦该输出当前的工作区。

输出配置采用声明式：你声明期望状态（分辨率、位置、缩放），niri 应用。对于热插拔（插拔显示器），niri 会自动检测并应用匹配的配置；若无匹配配置，使用显示器 EDID 推荐模式。

输出位置用 `position x= y=` 相对定位，坐标系原点在虚拟桌面的左上角，x 向右增、y 向下增。多个输出的 position 拼成虚拟桌面布局。

### 例子

**多显示器配置：**

```kdl
outputs {
    // 笔记本内置屏
    output "eDP-1" {
        mode "1920x1080@60Hz"
        scale 1.0
        position x=0 y=0
    }

    // 家里的 4K 显示器
    output "Dell Inc. DELL U2720Q ABC1234" {
        mode "3840x2160@60Hz"
        scale 1.5
        position x=1920 y=0   // 在内置屏右侧
        variable-refresh-rate true
    }

    // 公司的 2K 显示器
    output "LG Electronics LG HDR 2K 5678" {
        mode "2560x1440@144Hz"
        scale 1.25
        position x=1920 y=0   // 同一位置，热插拔时自动切换
    }
}
```

**工作区在多显示器间的行为：**

```bash
# 笔记本屏（eDP-1）有工作区 1,2,3
# 外接屏（DP-1）有工作区 1,2,3

# 在笔记本屏按 Super+2 → 切换笔记本屏的工作区 2
# 聚焦移到外接屏（鼠标移过去或用快捷键）
# 按 Super+2 → 切换外接屏的工作区 2（与笔记本屏的 2 无关）

# 把当前列移动到"另一个显示器的工作区"
# niri 提供 focus-output 系列 action
```

**输出切换快捷键：**

```kdl
binds {
    // 聚焦到左/右输出
    Mod+Shift+Left  { focus-output-left; }
    Mod+Shift+Right { focus-output-right; }
    Mod+Shift+Up    { focus-output-up; }
    Mod+Shift+Down  { focus-output-down; }

    // 把当前列移动到另一个输出
    Mod+Shift+Ctrl+Left  { move-column-to-output-left; }
    Mod+Shift+Ctrl+Right { move-column-to-output-right; }
}
```

**热插拔脚本（监听输出变化）：**

```bash
#!/bin/bash
# ~/.config/niri/scripts/hotplug.sh
# 通过 niri 事件流监听输出变化
niri msg event-stream | jq --unbuffered '.OutputsChanged?' | while read -r event; do
    # 重新应用壁纸（适应新分辨率）
    pkill swaybg
    swaybg -i ~/Pictures/wallpaper.jpg -m fill &
    # 重新排列 waybar
    pkill waybar
    waybar &
done
```

**查看当前输出状态：**

```bash
niri msg outputs --json | jq '.[] | {name, make, model, current: .current_mode}'
```

### 总结

- niri 多显示器模型：每个输出独立工作区列表，工作区不跨输出移动。
- 输出配置声明式，按型号匹配比按连接名更稳定。
- 输出位置用 `position x= y=` 相对定位，拼成虚拟桌面。
- 关键注意：热插拔时 niri 自动应用匹配配置，可用事件流脚本做额外处理（如重设壁纸）。

---

## 第 18 讲：Xwayland 与兼容性

### 概念

**Xwayland** 是在 Wayland 下运行 X11 应用的兼容层。许多传统应用（尤其是 Electron 应用、Wine、某些游戏）仍依赖 X11，niri 通过 Xwayland 让这些应用运行。niri 本身不直接集成 Xwayland，而是推荐使用 **xwayland-satellite** 来提供更完善的 Xwayland 支持。

### 原理

Wayland 协议与 X11 协议不兼容。Xwayland 是一个特殊的 X 服务器，它本身作为 Wayland 客户端运行，同时为 X11 应用提供 X 服务器功能。这样 X11 应用以为自己在 X11 下运行，而其窗口实际被 Xwayland 转译为 Wayland 窗口。

niri 的特殊性在于：它不像 GNOME/KDE 那样内置 Xwayland 管理，而是依赖独立的 `xwayland-satellite` 工具。xwayland-satellite 解决了一个关键问题——**让 Xwayland 窗口正确显示 app-id**，使 niri 的窗口规则能匹配 X11 应用。原生 Xwayland 会让所有 X11 窗口的 app-id 都是 "xwayland"，无法区分。

需要注意的是，Xwayland 应用无法使用 Wayland 的部分特性，如 HiDPI 分数缩放（只能整数缩放）、安全截图屏蔽等。因此推荐优先使用 Wayland 原生版本的应用。

### 例子

**安装 xwayland-satellite：**

```bash
# Arch Linux（AUR）
yay -S xwayland-satellite

# 或从源码编译
git clone https://github.com/Supreeeme/xwayland-satellite.git
cd xwayland-satellite
make
sudo cp xwayland-satellite /usr/local/bin/
```

**在 niri 中启动 xwayland-satellite：**

```kdl
// ~/.config/niri/config.kdl
spawn-at-startup "xwayland-satellite"

// 设置环境变量，让 X11 应用知道有 Xwayland
environment {
    DISPLAY ":0"   // xwayland-satellite 默认使用 :0
}
```

**验证 Xwayland 工作：**

```bash
# 检查 xwayland-satellite 进程
pgrep -a xwayland-satellite

# 运行一个 X11 应用测试
xeyes &

# 查看其 app-id（应该是应用名而非 "xwayland"）
niri msg windows --json | jq '.[].app-id'
```

**为 X11 应用配置窗口规则：**

```kdl
// Electron 应用（如 VS Code、Discord）通过 xwayland-satellite 后 app-id 正确
window-rule {
    match app-id="code"           // VS Code
    default-column-width "1/2"
}

window-rule {
    match app-id="discord"
    default-column-width "1/2"
}

// 强制某些应用使用 Wayland 原生（绕过 Xwayland）
environment {
    // Firefox
    MOZ_ENABLE_WAYLAND "1"
    // Electron 应用
    ELECTRON_OZONE_PLATFORM_HINT "wayland"
    // Qt 应用
    QT_QPA_PLATFORM "wayland"
}
```

**常见兼容性问题排查：**

```bash
# 1. 应用不显示 → 检查 DISPLAY 变量
echo $DISPLAY   # 应为 :0 或类似

# 2. 输入法不工作 → 设置 IM 变量
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx

# 3. HiDPI 模糊 → Xwayland 分数缩放问题
# 设置 XCURSOR_SIZE 和缩放
export XCURSOR_SIZE=24
# niri 配置中：
# output "eDP-1" { scale 1.5; }  // Wayland 原生应用清晰
# X11 应用可能模糊，这是 Xwayland 固有限制

# 4. 游戏全屏问题 → 用窗口规则强制窗口模式
window-rule {
    match app-id="steam_app_*"
    open-fullscreen true
}
```

### 总结

- Xwayland 让 X11 应用在 Wayland 下运行，niri 推荐用 xwayland-satellite。
- xwayland-satellite 解决了 X11 应用 app-id 显示问题，使窗口规则可用。
- 优先用 Wayland 原生应用，通过环境变量强制（如 `MOZ_ENABLE_WAYLAND`）。
- 关键注意：Xwayland 应用无法使用分数缩放和安全截图屏蔽，这是固有限制。

---

# 第 6 章 实战与运维

## 第 19 讲：完整配置实战——从零搭建桌面

### 概念

本讲将前 18 讲的知识整合，从零搭建一套**生产可用的 niri 桌面环境**。目标：一个具备状态栏、应用启动器、通知、锁屏、壁纸、截图、剪贴板历史、多显示器、输入法的完整桌面，配置清晰、易于维护。

### 原理

搭建完整桌面的核心是"分层组装"：

1. **基础层**：niri 本体 + 配置文件骨架。
2. **输入输出层**：键盘布局、触摸板、显示器。
3. **交互层**：键位绑定、窗口规则。
4. **视觉层**：布局、动画、主题。
5. **工具层**：状态栏、启动器、通知、锁屏、壁纸、截图。
6. **兼容层**：Xwayland、输入法、环境变量。

每一层独立配置，互不耦合，便于排错和迭代。配置文件应分块组织，用注释清晰分隔，方便后期维护。

### 例子

**完整的 `~/.config/niri/config.kdl`：**

```kdl
// ============================================================
// niri 完整配置 - Tokyo Night 主题
// ============================================================

// ---------- 环境变量 ----------
environment {
    // Wayland
    XDG_SESSION_TYPE "wayland"
    XDG_CURRENT_DESKTOP "niri"

    // Qt
    QT_QPA_PLATFORM "wayland"
    QT_QPA_PLATFORMTHEME "qt5ct"
    QT_WAYLAND_DISABLE_WINDOWDECORATION "1"

    // GTK
    GTK_THEME "Tokyo-Night:dark"
    ICON_THEME "Papirus-Dark"

    // 输入法
    GTK_IM_MODULE "fcitx"
    QT_IM_MODULE "fcitx"
    XMODIFIERS "@im=fcitx"
    INPUT_METHOD "fcitx"

    // Xwayland
    DISPLAY ":0"

    // 浏览器/Electron 强制 Wayland
    MOZ_ENABLE_WAYLAND "1"
    ELECTRON_OZONE_PLATFORM_HINT "wayland"
}

// ---------- 启动项 ----------
spawn-at-startup "xwayland-satellite"
spawn-at-startup "fcitx5" "-d"
spawn-at-startup "waybar"
spawn-at-startup "mako"
spawn-at-startup "swaybg" "-i" "/home/user/Pictures/wallpaper.jpg" "-m" "fill"
spawn-at-startup "wl-paste" "--watch" "cliphist" "store"
spawn-at-startup "swayidle" "-w" \
    "timeout" "300" "niri msg action lock-screen" \
    "timeout" "600" "niri msg action power-off-monitors" \
    "before-sleep" "niri msg action lock-screen"

// ---------- 输入设备 ----------
input {
    keyboard {
        xkb {
            layout "us"
            options "caps:ctrl_modifier"
        }
        repeat-delay 250
        repeat-rate 50
    }
    touchpad {
        tap true
        dwt true
        natural-scroll true
        accel-profile "flat"
    }
}

// ---------- 输出设备 ----------
outputs {
    output "eDP-1" {
        mode "1920x1080@60Hz"
        scale 1.0
        position x=0 y=0
    }
    output "Dell Inc. DELL U2720Q ABC1234" {
        mode "3840x2160@60Hz"
        scale 1.5
        position x=1920 y=0
        variable-refresh-rate true
    }
}

// ---------- 键位绑定 ----------
binds {
    // 应用启动
    Mod+T { spawn "alacritty"; }
    Mod+D { spawn "fuzzel"; }
    Mod+B { spawn "firefox"; }
    Mod+E { spawn "thunar"; }
    Mod+V { spawn "sh" "-c" "cliphist list | fuzzel -d | cliphist decode | wl-copy"; }

    // 截图
    Print { screenshot; }
    Shift+Print { screenshot-screen; }
    Ctrl+Print { screenshot-window; }

    // 窗口管理
    Mod+Q { close-window; }
    Mod+F { switch-preset-column-width; }
    Mod+M { maximize-column; }
    Mod+Shift+F { fullscreen-window; }
    Mod+C { center-column; }
    Mod+Shift+Slash { toggle-window-floating; }

    // 聚焦
    Mod+H { focus-column-left; }
    Mod+L { focus-column-right; }
    Mod+K { focus-window-up; }
    Mod+J { focus-window-down; }

    // 移动
    Mod+Shift+H { move-column-left; }
    Mod+Shift+L { move-column-right; }
    Mod+Shift+K { move-window-up; }
    Mod+Shift+J { move-window-down; }

    // 列重组
    Mod+BracketLeft { consume-window; }
    Mod+BracketRight { expel-window; }

    // 工作区
    Mod+1 { focus-workspace 1; }
    Mod+2 { focus-workspace 2; }
    Mod+3 { focus-workspace 3; }
    Mod+4 { focus-workspace 4; }
    Mod+5 { focus-workspace 5; }
    Mod+6 { focus-workspace 6; }
    Mod+7 { focus-workspace 7; }
    Mod+8 { focus-workspace 8; }
    Mod+9 { focus-workspace 9; }

    Mod+Shift+1 { move-column-to-workspace 1; }
    Mod+Shift+2 { move-column-to-workspace 2; }
    Mod+Shift+3 { move-column-to-workspace 3; }
    Mod+Shift+4 { move-column-to-workspace 4; }
    Mod+Shift+5 { move-column-to-workspace 5; }
    Mod+Shift+6 { move-column-to-workspace 6; }
    Mod+Shift+7 { move-column-to-workspace 7; }
    Mod+Shift+8 { move-column-to-workspace 8; }
    Mod+Shift+9 { move-column-to-workspace 9; }

    // 输出切换
    Mod+Shift+Left { focus-output-left; }
    Mod+Shift+Right { focus-output-right; }

    // 音量
    XF86AudioRaiseVolume { spawn "pactl" "set-sink-volume" "@DEFAULT_SINK@" "+5%"; }
    XF86AudioLowerVolume { spawn "pactl" "set-sink-volume" "@DEFAULT_SINK@" "-5%"; }
    XF86AudioMute { spawn "pactl" "set-sink-mute" "@DEFAULT_SINK@" "toggle"; }

    // 亮度
    XF86MonBrightnessUp { spawn "brightnessctl" "set" "+5%"; }
    XF86MonBrightnessDown { spawn "brightnessctl" "set" "5%-"; }

    // 电源菜单
    Mod+Shift+P { spawn "wlogout"; }

    // 退出
    Mod+Shift+E { quit; }
}

// ---------- 窗口规则 ----------
window-rule {
    match app-id="firefox"
    default-column-width "1/2"
}
window-rule {
    match app-id="Alacritty"
    default-column-width "1/3"
}
window-rule {
    match app-id="org.gnome.Nautilus"
    match app-id="org.gnome.Calculator"
    match app-id="pavucontrol"
    open-floating true
    default-column-width "600"
}
window-rule {
    match app-id="org.keepassxc.KeePassXC"
    block-out-from "screencast"
}

// ---------- 布局 ----------
layout {
    gap 8
    center-focused-column "on-overflow"
    default-column-width "1/2"
    preset-column-widths {
        proportion 1/3
        proportion 1/2
        proportion 2/3
        proportion 1.0
    }
    struts {
        top 30
    }
    focus-ring {
        width 3
        active-color "#7aa2f7"
        inactive-color "#3b4261"
    }
    shadow {
        on
        softness 30
        spread 5
        offset x=0 y=5
        color "#00000080"
    }
}

// ---------- 动画 ----------
animations {
    easing {
        duration-ms 250
        curve "ease-out-cubic"
    }
    window-open {
        on
        easing { duration-ms 200; curve "ease-out-expo"; }
    }
    window-close {
        on
        easing { duration-ms 200; curve "ease-in-cubic"; }
    }
    window-movement {
        on
        easing { duration-ms 300; curve "ease-out-quint"; }
    }
    workspace-switch {
        on
        easing { duration-ms 250; curve "ease-out-cubic"; }
    }
    horizontal-view-shift {
        on
        easing { duration-ms 300; curve "ease-out-quad"; }
    }
}

// ---------- 其他 ----------
prefer-no-csd true
cursor {
    xcursor-theme "Bibata-Modern-Classic"
    xcursor-size 24
}
hotkey-overlay {
    skip-at-startup false
}
screenshot-path "~/Pictures/Screenshots/Screenshot from %Y-%m-%d %H-%M-%S.png"
```

**验证配置：**

```bash
niri validate   # 语法检查
```

### 总结

- 完整桌面分六层组装：基础、输入输出、交互、视觉、工具、兼容。
- 配置文件用注释分块，便于维护。
- 启动项、环境变量、窗口规则、键位、布局、动画各司其职。
- 关键注意：先用 `niri validate` 检查，再启动；启动后用 `niri msg` 验证各功能正常。

---

## 第 20 讲：调试、日志与常见问题排查

### 概念

任何桌面环境都会遇到问题，niri 也不例外。本讲介绍 niri 的调试工具、日志查看方法、常见问题排查思路，帮助你独立解决运行中遇到的各种故障。掌握调试能力是从"会用"到"精通"的关键一步。

### 原理

niri 的调试支持基于以下机制：

1. **日志输出**：niri 默认将日志输出到 stderr，可通过 `RUST_LOG` 环境变量控制日志级别（error/warn/info/debug/trace）。
2. **配置验证**：`niri validate` 检查配置文件语法和逻辑，是排错的第一步。
3. **IPC 查询**：`niri msg` 系列命令可实时查询状态，用于验证功能是否正常。
4. **事件流**：`niri msg event-stream` 实时观察 niri 内部事件，定位交互问题。
5. **系统日志**：通过 `journalctl` 查看 systemd 会话日志，定位 seat、DRM 等系统级问题。

常见问题分几类：启动失败（黑屏/闪退）、输入异常（键盘/触摸板不工作）、显示异常（分辨率/缩放错误）、窗口异常（应用不显示/无法聚焦）、外设异常（多显示器/音频问题）。

### 例子

**1. 启动 niri 并查看详细日志：**

```bash
# 前台启动，详细日志
RUST_LOG=debug niri 2>&1 | tee ~/niri-debug.log

# 仅查看错误和警告
RUST_LOG=warn niri 2>&1 | tee ~/niri-warn.log

# 通过 systemd 启动并查看日志
systemctl --user start niri.service
journalctl --user -u niri.service -f
```

**2. 配置验证与排错：**

```bash
# 验证配置语法
niri validate
# 输出示例（有错误）：
# Error: expected `}` at line 42
#   --> ~/.config/niri/config.kdl:42:5

# 验证并显示详细错误位置
niri validate --verbose
```

**3. 查询运行状态排查问题：**

```bash
# 检查 niri 是否正常运行
niri msg version

# 检查输出（显示器）是否被识别
niri msg outputs
# 若外接显示器不显示，检查 outputs 配置和 DRM 权限

# 检查窗口是否被正确识别
niri msg windows
# 若应用不显示，检查 Xwayland-satellite 是否运行

# 检查聚焦窗口
niri msg focused-window
# 若快捷键无效，检查当前聚焦是否正确
```

**4. 实时事件流调试：**

```bash
# 监听所有事件
niri msg event-stream | jq .

# 仅监听窗口聚焦变化
niri msg event-stream | \
    jq --unbuffered 'select(.WindowOpenedOrChangedFocus)'

# 监听工作区切换
niri msg event-stream | \
    jq --unbuffered 'select(.WorkspaceActivated)'
```

**5. 常见问题排查清单：**

```bash
# ===== 问题1：niri 启动黑屏 =====
# 检查 DRM 权限
ls -la /dev/dri/
# 用户应在 video 和 render 组
groups $USER
sudo usermod -aG video,render $USER

# 检查 seat
loginctl show-session $XDG_SESSION_ID -p Type -p Active
# Type 应为 wayland，Active 应为 yes

# 检查显卡驱动
lspci -k | grep -A 3 VGA
# 确保内核驱动正确加载

# ===== 问题2：键盘输入法不工作 =====
# 检查 fcitx5 是否运行
pgrep -a fcitx5

# 检查环境变量
echo $GTK_IM_MODULE $QT_IM_MODULE $XMODIFIERS
# 应输出 fcitx fcitx @im=fcitx

# 检查 niri input 配置
niri msg action --help | grep -i input

# ===== 问题3：触摸板手势失效 =====
# 检查 libinput 识别
libinput list-devices | grep -A 5 Touchpad

# 检查 niri touchpad 配置是否生效
niri validate

# ===== 问题4：外接显示器无信号 =====
# 检查输出是否被识别
niri msg outputs

# 检查 xrandr（X11 兼容）
# Wayland 下用 wlr-randr
wlr-randr

# 检查内核 DRM
dmesg | grep -i drm

# ===== 问题5：X11 应用不显示 =====
# 检查 xwayland-satellite
pgrep -a xwayland-satellite

# 检查 DISPLAY 变量
echo $DISPLAY  # 应为 :0

# 手动启动测试
xwayland-satellite &
xterm  # 测试 X11 应用

# ===== 问题6：waybar 不显示工作区 =====
# 检查 waybar 是否运行
pgrep -a waybar

# 检查 waybar 配置是否使用 niri 模块
grep -i niri ~/.config/waybar/config

# 重启 waybar
killall waybar; waybar &

# ===== 问题7：截图黑屏 =====
# 检查 grim 是否支持 niri
grim -h | grep -i wayland

# 使用 niri 内置截图
niri msg action screenshot
```

**6. 调试脚本模板：**

```bash
#!/bin/bash
# ~/niri-diagnose.sh - niri 诊断脚本
echo "===== niri 诊断报告 ====="
echo "时间: $(date)"
echo ""
echo "--- 版本 ---"
niri msg version 2>&1
echo ""
echo "--- 配置验证 ---"
niri validate 2>&1
echo ""
echo "--- 输出设备 ---"
niri msg outputs 2>&1
echo ""
echo "--- 当前窗口 ---"
niri msg windows 2>&1 | head -20
echo ""
echo "--- 输入设备 ---"
libinput list-devices 2>&1 | grep -E "^(Device|Kernel)" | head -10
echo ""
echo "--- 环境变量 ---"
env | grep -E "^(WAYLAND|XDG|DISPLAY|GTK_IM|QT_IM|XMOD)"
echo ""
echo "--- 进程检查 ---"
for proc in niri waybar fuzzel mako fcitx5 xwayland-satellite swaybg; do
    if pgrep -x "$proc" > /dev/null; then
        echo "  [OK] $proc 运行中"
    else
        echo "  [缺失] $proc 未运行"
    fi
done
echo ""
echo "===== 诊断完成 ====="
```

### 总结

- 调试工具链：`RUST_LOG` 日志、`niri validate` 配置检查、`niri msg` 状态查询、`event-stream` 事件流、`journalctl` 系统日志。
- 常见问题分五类：启动、输入、显示、窗口、外设，各有排查路径。
- 编写诊断脚本可快速定位问题，是运维利器。
- 关键注意：遇到问题先 `niri validate` 检查配置，再查日志，最后查系统级（DRM/seat）问题，按"应用→配置→系统"顺序排查最高效。
