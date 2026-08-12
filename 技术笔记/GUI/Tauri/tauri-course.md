# Tauri 系统化教程：从入门到实战

> 本教程以"教科书式"的讲解方式，按"概念 → 原理 → 例子 → 总结"四段式结构，循序渐进地讲解 Tauri——用 Rust 构建后端、Web 技术构建前端的高性能桌面应用框架。每讲聚焦一个核心知识点，配合可运行代码示例，帮助读者建立完整的 Tauri 知识体系。

---

## 课程总览

- **预计讲数**：29 讲（7 章）
- **学习目标**：
  1. 理解 Tauri 的设计哲学与核心架构
  2. 掌握前后端通信（IPC）的命令与事件机制
  3. 熟练使用窗口管理、系统托盘、菜单等系统能力
  4. 掌握插件系统，使用内置插件并开发自定义插件
  5. 具备自动更新、全局快捷键、通知等进阶能力
  6. 具备独立开发、测试、发布 Tauri 应用的完整能力
- **适用读者**：有 Rust 基础和前端基础（HTML/CSS/JS 或框架），希望学习跨平台桌面应用开发的开发者
- **学习建议**：按章节顺序学习，每讲先读"概念"和"原理"建立认知，再动手运行"例子"中的代码，最后用"总结"回顾要点。Tauri 2.x 是当前主版本，本教程以 2.x 为主

---

## 详细章节目录

### 第 1 章：Tauri 入门基础
- 第 1 讲：Tauri 简介与设计哲学
- 第 2 讲：环境搭建与工具链
- 第 3 讲：创建第一个 Tauri 应用
- 第 4 讲：项目结构详解

### 第 2 章：核心架构与配置
- 第 5 讲：Tauri 架构总览
- 第 6 讲：tauri.conf.json 配置详解
- 第 7 讲：应用生命周期与事件
- 第 8 讲：前端集成与构建

### 第 3 章：前后端通信 IPC
- 第 9 讲：命令系统基础
- 第 10 讲：命令参数与返回值
- 第 11 讲：事件系统
- 第 12 讲：状态管理
- 第 13 讲：错误处理与异步命令

### 第 4 章：窗口与系统集成
- 第 14 讲：窗口管理
- 第 15 讲：系统托盘
- 第 16 讲：应用菜单
- 第 17 讲：系统对话框与剪贴板

### 第 5 章：插件系统与生态
- 第 18 讲：内置插件概览
- 第 19 讲：文件系统插件
- 第 20 讲：数据库与存储插件
- 第 21 讲：开发自定义插件

### 第 6 章：进阶能力
- 第 22 讲：自动更新
- 第 23 讲：全局快捷键
- 第 24 讲：通知系统与深度链接
- 第 25 讲：安全策略与权限

### 第 7 章：实战与工程化
- 第 26 讲：实战：笔记应用
- 第 27 讲：性能优化
- 第 28 讲：测试 Tauri 应用
- 第 29 讲：发布与部署

---

# 第 1 章：Tauri 入门基础

本章带你走进 Tauri 的世界。我们将从 Tauri 的设计哲学讲起，搭建开发环境，创建第一个应用，并理解项目结构。Tauri 与 Electron 有本质区别——它用系统 WebView 而非打包 Chromium，用 Rust 而非 Node.js 作为后端，因此体积更小、内存更低、性能更高。

## 第 1 讲：Tauri 简介与设计哲学

### 概念

**Tauri** 是一个用 Rust 构建后端、用 Web 技术（HTML/CSS/JS）构建前端的高性能跨平台桌面应用框架。由 Daniel Thompson-Yvetot 等人于 2019 年发起，1.0 于 2022 年发布，2.0 于 2024 年发布（新增移动端支持）。Tauri 的核心特征是：① **Rust 后端**——用 Rust 处理系统调用、文件 IO、网络等；② **系统 WebView**——前端用操作系统自带的 WebView 渲染，不打包浏览器；③ **安全优先**——通过权限系统精细控制前端能力；④ **体积小**——典型应用仅 3-10MB，远小于 Electron 的 100MB+。

### 原理

Tauri 与 Electron 的根本区别在于"浏览器引擎"的处理方式。Electron 将完整的 Chromium 浏览器打包进每个应用，导致体积庞大（100MB+）、内存占用高。Tauri 则使用操作系统自带的 WebView——macOS 用 WKWebView、Windows 用 WebView2、Linux 用 WebKitGTK，应用本身不包含浏览器，因此体积可控制在 3-10MB。

Tauri 的架构分为三层：① **前端层**——用任意 Web 框架（React/Vue/Svelte/原生 JS）构建 UI，运行在系统 WebView 中；② **IPC 层**——前后端通过自定义协议通信，前端调用 `invoke('command_name')` 触发后端 Rust 函数，后端通过 `emit('event')` 通知前端；③ **后端层**——Rust 代码处理系统调用、业务逻辑，通过 Tauri API 暴露给前端。

这种"前后端分离 + Rust 后端"的设计带来了多重优势：前端开发者可复用 Web 技能，Rust 保证后端安全高效，IPC 机制让前后端解耦。同时，Tauri 的安全模型通过 `capabilities` 权限系统，精细控制前端能调用哪些后端能力，避免恶意网页滥用系统资源。

### 例子

Tauri 与 Electron 对比：

```
┌─────────────────────────────────────────────────────────────┐
│                      Electron 架构                          │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Chromium (完整浏览器，~150MB)                        │  │
│  │  ┌────────────────────────────────────────────────┐  │  │
│  │  │  Node.js 运行时 (~30MB)                        │  │  │
│  │  │  ┌──────────────────────────────────────────┐  │  │  │
│  │  │  │  应用前端 (HTML/CSS/JS)                  │  │  │  │
│  │  │  │  应用后端 (Node.js)                      │  │  │  │
│  │  │  └──────────────────────────────────────────┘  │  │  │
│  │  └────────────────────────────────────────────────┘  │  │
│  └──────────────────────────────────────────────────────┘  │
│  总体积: ~180MB+                                            │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                       Tauri 架构                            │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  系统 WebView (复用 OS 自带，0MB 额外)                │  │
│  │  ┌────────────────────────────────────────────────┐  │  │
│  │  │  应用前端 (HTML/CSS/JS)                        │  │  │
│  │  └────────────────────────────────────────────────┘  │  │
│  └──────────────────────────────────────────────────────┘  │
│                          ↕ IPC (invoke/emit)                │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Rust 后端 (~3-10MB)                                 │  │
│  │  - 业务逻辑                                          │  │
│  │  - 系统调用                                          │  │
│  │  - 文件 IO                                           │  │
│  └──────────────────────────────────────────────────────┘  │
│  总体积: ~3-10MB                                            │
└─────────────────────────────────────────────────────────────┘
```

一个最简的 Tauri 应用示例：

**前端（HTML）**：
```html
<!DOCTYPE html>
<html>
<head>
    <title>我的 Tauri 应用</title>
</head>
<body>
    <h1>你好，Tauri！</h1>
    <button id="greet">打招呼</button>
    <p id="result"></p>

    <script type="module">
        import { invoke } from '@tauri-apps/api/core';

        document.getElementById('greet').addEventListener('click', async () => {
            const result = await invoke('greet', { name: '世界' });
            document.getElementById('result').textContent = result;
        });
    </script>
</body>
</html>
```

**后端（Rust）**：
```rust
// src-tauri/src/main.rs
#[tauri::command]
fn greet(name: &str) -> String {
    format!("你好，{}！这是来自 Rust 的问候。", name)
}

fn main() {
    tauri::Builder::default()
        .invoke_handler(tauri::generate_handler![greet])
        .run(tauri::generate_context!())
        .expect("error while running tauri application");
}
```

### 总结

- Tauri 用 Rust 构建后端，Web 技术构建前端，系统 WebView 渲染
- 与 Electron 相比：体积小（3-10MB vs 180MB+）、内存低、启动快
- 三层架构：前端（WebView）+ IPC（invoke/emit）+ 后端（Rust）
- 安全模型通过 `capabilities` 权限系统精细控制前端能力
- **常见坑**：系统 WebView 行为有差异（尤其 Linux WebKitGTK 版本碎片化）；Tauri 2.x 与 1.x API 有较大变化，注意版本；前端需通过 `@tauri-apps/api` 调用后端，不能直接 `require`

---

## 第 2 讲：环境搭建与工具链

### 概念

开发 Tauri 应用需要准备三套环境：① **Rust 工具链**——编译后端 Rust 代码；② **系统 WebView 依赖**——各平台不同的原生依赖；③ **前端工具链**——Node.js + 包管理器（npm/pnpm/yarn）+ 前端框架。Tauri CLI 是核心命令行工具，用于创建、开发、构建项目。

### 原理

Tauri 的构建流程涉及多个工具链协作：前端用 `npm run build` 生成静态资源（HTML/CSS/JS），Rust 后端用 `cargo build` 编译为原生二进制，Tauri CLI 将前端资源嵌入 Rust 二进制（release 模式）或代理到 dev server（开发模式）。

各平台 WebView 依赖：① **macOS**——自带 WKWebView（无需安装），但需 Xcode Command Line Tools；② **Windows**——需 WebView2 Runtime（Win10/11 通常预装，旧系统需手动安装）；③ **Linux**——需安装 `webkit2gtk-4.1`、`libgtk-3-dev` 等系统库。这些依赖是 Tauri 复用系统 WebView 的基础。

Rust 工具链通过 `rustup` 管理，建议安装 stable 最新版。Tauri 2.x 要求 Rust 1.77.2+。前端工具链推荐 Node.js 18+ 和 pnpm（比 npm 更快、磁盘占用更小）。Tauri CLI 可通过 `cargo install tauri-cli` 或 `npm install -g @tauri-apps/cli` 安装。

### 例子

**macOS 环境搭建**：

```bash
# 1. 安装 Xcode Command Line Tools（含 clang、git 等）
xcode-select --install

# 2. 安装 Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source "$HOME/.cargo/env"

# 验证 Rust
rustc --version    # 应输出 rustc 1.77.2+
cargo --version

# 3. 安装 Node.js（推荐用 nvm 管理版本）
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
nvm install 20
nvm use 20

# 4. 安装 pnpm（可选，推荐）
npm install -g pnpm

# 5. 安装 Tauri CLI
cargo install tauri-cli --version "^2.0"
# 或用 npm 安装
# npm install -g @tauri-apps/cli

# 验证 Tauri CLI
cargo tauri --version
```

**Windows 环境搭建**（PowerShell）：

```powershell
# 1. 安装 Visual Studio Build Tools（含 MSVC 编译器）
# 下载: https://visualstudio.microsoft.com/visual-cpp-build-tools/
# 勾选 "Desktop development with C++"

# 2. 安装 WebView2 Runtime（Win10/11 通常预装）
# 下载: https://developer.microsoft.com/microsoft-edge/webview2/

# 3. 安装 Rust
# 下载 rustup-init.exe: https://rustup.rs/
# 或用 winget:
winget install Rustlang.Rustup

# 4. 安装 Node.js
winget install OpenJS.NodeJS.LTS

# 5. 安装 Tauri CLI
cargo install tauri-cli --version "^2.0"
```

**Linux 环境搭建**（Ubuntu/Debian）：

```bash
# 1. 安装系统依赖（WebView2 对应的 WebKitGTK）
sudo apt update
sudo apt install libwebkit2gtk-4.1-dev \
    build-essential \
    curl \
    wget \
    file \
    libxdo-dev \
    libssl-dev \
    libayatana-appindicator3-dev \
    librsvg2-dev

# 2. 安装 Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source "$HOME/.cargo/env"

# 3. 安装 Node.js
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs

# 4. 安装 Tauri CLI
cargo install tauri-cli --version "^2.0"
```

**Fedora**：

```bash
sudo dnf install webkit2gtk4.1-devel \
    openssl-devel \
    curl \
    wget \
    file \
    libappindicator-gtk3-devel \
    librsvg2-devel \
    gcc
```

**Arch Linux**：

```bash
sudo pacman -S --needed webkit2gtk-4.1 \
    base-devel \
    curl \
    wget \
    file \
    openssl \
    appmenu-gtk-module \
    libappindicator-gtk3 \
    librsvg
```

验证环境完整性：

```bash
# Tauri 提供环境检查命令
cargo tauri info
```

输出示例：

```
[✔] Environment
    - OS: Mac OS 14.0.0 X64
    ✔ Xcode Command Line Tools: installed
    ✔ rustc: 1.80.0
    ✔ cargo: 1.80.0
    ✔ rustup: 1.27.1
    - Node.js: 20.10.0
    - pnpm: 8.15.0
    - npm: 10.2.3

[-] Packages
    - tauri 🦀: 2.0.0
    - tauri-build 🦀: 2.0.0
    - wry 🦀: 0.41.0
    - tao 🦀: 0.29.0
    - @tauri-apps/api: 2.0.0
    - @tauri-apps/cli: 2.0.0

[-] App
    - build-type: bundle
    - CSP: unset
    - frontendDist: ../dist
    - devUrl: http://localhost:1420/
    - framework: Vanilla
    - bundler: Vite
```

### 总结

- 三套环境：Rust 工具链（rustup）、系统 WebView 依赖（各平台不同）、前端工具链（Node.js + pnpm）
- macOS 需 Xcode CLT；Windows 需 MSVC + WebView2；Linux 需 webkit2gtk-4.1 等
- Tauri CLI 通过 `cargo install tauri-cli` 或 `npm install -g @tauri-apps/cli` 安装
- `cargo tauri info` 检查环境完整性
- **常见坑**：Linux 的 `webkit2gtk-4.0`（旧）与 `4.1`（新）不兼容，Tauri 2.x 需 4.1；Windows 必须装 MSVC（不能用 MinGW）；Rust 版本太旧会编译失败，用 `rustup update` 升级

---

## 第 3 讲：创建第一个 Tauri 应用

### 概念

创建 Tauri 应用有两种方式：① **`create-tauri-app` 脚手架**——交互式创建，支持选择前端框架（React/Vue/Svelte/Vanilla 等）；② **手动集成**——在已有前端项目中添加 Tauri。本讲用脚手架创建第一个应用，理解开发与构建流程。

### 原理

`create-tauri-app` 是 Tauri 官方脚手架，它根据用户选择的前端框架，生成完整的项目模板：前端代码（如 React + Vite）、Rust 后端（`src-tauri/`）、配置文件（`tauri.conf.json`）。模板预配置了前端与 Tauri 的集成——前端通过 `@tauri-apps/api` 调用后端，Vite 配置了 Tauri 专用的开发端口。

开发模式下，Tauri 启动两个进程：① 前端 dev server（如 Vite 的 `localhost:1420`）；② Rust 后端进程。WebView 加载 dev server 的页面，前后端通过 IPC 通信。修改前端代码会触发热重载，修改 Rust 代码需重启后端（`cargo tauri dev` 会自动重启）。

构建模式下，前端先 `npm run build` 生成静态资源到 `dist/`，Rust 后端将这些资源嵌入二进制（通过 `tauri::generate_context!` 宏），最终生成单个可执行文件。

### 例子

**用脚手架创建应用**：

```bash
# 方式 1：用 npm/pnpm/yarn
npm create tauri-app@latest
# 或
pnpm create tauri-app
# 或
yarn create tauri-app

# 方式 2：用 cargo
cargo create-tauri-app
```

交互式选择：

```
✔ Project name · my-tauri-app
✔ Identifier · com.example.my-tauri-app
✔ Choose which language to use for your frontend · TypeScript / JavaScript
✔ Choose your package manager · pnpm
✔ Choose your UI template · Vanilla
✔ Choose your UI flavor · TypeScript
```

生成的项目结构：

```
my-tauri-app/
├── src/                    # 前端源码
│   ├── index.html
│   ├── main.ts
│   └── styles.css
├── src-tauri/              # Rust 后端
│   ├── src/
│   │   ├── main.rs         # Rust 入口
│   │   └── lib.rs          # 库代码（2.x 推荐）
│   ├── icons/              # 应用图标
│   ├── capabilities/       # 权限配置
│   │   └── default.json
│   ├── Cargo.toml          # Rust 依赖
│   ├── build.rs            # 构建脚本
│   └── tauri.conf.json     # Tauri 配置
├── public/                 # 前端静态资源
├── package.json            # 前端依赖
├── tsconfig.json
└── vite.config.ts
```

**开发运行**：

```bash
cd my-tauri-app
pnpm install        # 安装前端依赖
pnpm tauri dev      # 启动开发模式
# 或
cargo tauri dev
```

开发模式下，Tauri 会：
1. 启动 Vite dev server（`localhost:1420`）
2. 编译 Rust 后端（首次较慢，约 30-60 秒）
3. 打开应用窗口，加载 dev server 页面
4. 监听文件变化，前端热重载，Rust 改动自动重启

**前端代码**（`src/main.ts`）：

```typescript
import { invoke } from '@tauri-apps/api/core';

let greetInputEl: HTMLInputElement | null;
let greetMsgEl: HTMLElement | null;

window.addEventListener("DOMContentLoaded", () => {
  greetInputEl = document.querySelector("#greet-input");
  greetMsgEl = document.querySelector("#greet-msg");
});

document.querySelector("#greet-button")?.addEventListener("click", async () => {
  const name = greetInputEl?.value;
  const result = await invoke("greet", { name });
  if (greetMsgEl) {
    greetMsgEl.textContent = result;
  }
});
```

**前端 HTML**（`src/index.html`）：

```html
<!DOCTYPE html>
<html lang="zh">
  <head>
    <meta charset="UTF-8" />
    <link rel="stylesheet" href="styles.css" />
    <title>Tauri 应用</title>
  </head>
  <body>
    <main class="container">
      <h1>欢迎来到 Tauri</h1>
      <form class="row">
        <input id="greet-input" placeholder="输入名字..." />
        <button type="button" id="greet-button">打招呼</button>
      </form>
      <p id="greet-msg"></p>
    </main>
    <script type="module" src="/main.ts"></script>
  </body>
</html>
```

**Rust 后端**（`src-tauri/src/lib.rs`）：

```rust
#[tauri::command]
fn greet(name: &str) -> String {
    format!("你好, {}! 你已被 Tauri 问候过了!", name)
}

#[cfg_attr(mobile, tauri::mobile_entry_point)]
pub fn run() {
    tauri::Builder::default()
        .plugin(tauri_plugin_opener::init())
        .invoke_handler(tauri::generate_handler![greet])
        .run(tauri::generate_context!())
        .expect("error while running tauri application");
}
```

`src-tauri/src/main.rs`：

```rust
// Prevents additional console window on Windows in release
#![cfg_attr(not(debug_assertions), windows_subsystem = "windows")]

fn main() {
    my_tauri_app_lib::run()
}
```

**构建发布版本**：

```bash
pnpm tauri build
# 或
cargo tauri build
```

构建产物在 `src-tauri/target/release/`：
- macOS: `.app` bundle + `.dmg`
- Windows: `.exe` + `.msi`
- Linux: `.deb` + `.rpm` + AppImage

### 总结

- `npm create tauri-app@latest` 交互式创建项目，支持多种前端框架
- 开发模式：`pnpm tauri dev`，前端热重载 + Rust 自动重启
- 构建模式：`pnpm tauri build`，生成各平台安装包
- Tauri 2.x 推荐 `lib.rs` + `main.rs` 分离，支持移动端共享代码
- **常见坑**：首次 `cargo tauri dev` 编译较慢（下载+编译依赖）；前端端口需与 `tauri.conf.json` 的 `devUrl` 一致；`invoke` 的参数名用驼峰（JS）转蛇形（Rust）

---

## 第 4 讲：项目结构详解

### 概念

Tauri 项目是"前端 + Rust 后端"的双层结构。前端部分（`src/`、`public/`、`package.json`）与普通 Web 项目一致；Rust 后端部分（`src-tauri/`）是独立的 Cargo 项目，包含入口代码、配置、图标、权限等。理解项目结构是高效开发的基础。

### 原理

Tauri 项目的核心是 `src-tauri/` 目录——它是一个完整的 Rust Cargo 项目，通过 `tauri` crate 集成 Tauri 框架。`tauri.conf.json` 是 Tauri 的核心配置文件，定义应用名称、窗口、前端路径、打包选项等。`capabilities/` 目录存放权限配置（Tauri 2.x 新增），控制前端能调用哪些后端 API。

前端与后端的"桥梁"是 `@tauri-apps/api` npm 包和 `tauri` Rust crate。前端通过 `invoke('command')` 调用后端 `#[tauri::command]` 标记的函数，后端通过 `app.emit('event')` 向前端发送事件。这种 IPC 机制是 Tauri 的核心。

`tauri::generate_context!` 宏在编译时读取 `tauri.conf.json`，将配置嵌入 Rust 二进制。`tauri::generate_handler![cmd1, cmd2]` 宏注册命令列表，让前端能调用这些命令。这两个宏是 Tauri 的"胶水"，将配置和代码连接起来。

### 例子

完整项目结构解析：

```
my-tauri-app/
├── src/                          # ===== 前端源码 =====
│   ├── index.html                # HTML 入口
│   ├── main.ts                   # JS/TS 入口
│   ├── styles.css                # 样式
│   └── components/               # 前端组件（如用框架）
├── public/                       # 前端静态资源（不经过构建处理）
│   └── favicon.ico
├── package.json                  # 前端依赖与脚本
├── pnpm-lock.yaml                # pnpm 锁文件
├── tsconfig.json                 # TypeScript 配置
├── vite.config.ts                # Vite 构建配置
│
├── src-tauri/                    # ===== Rust 后端 =====
│   ├── src/
│   │   ├── main.rs               # 二进制入口（调用 lib::run）
│   │   └── lib.rs                # 库代码（主要逻辑）
│   ├── icons/                    # 应用图标（各尺寸）
│   │   ├── 32x32.png
│   │   ├── 128x128.png
│   │   ├── icon.icns             # macOS
│   │   ├── icon.ico              # Windows
│   │   └── Square*Logo.png       # Linux/移动端
│   ├── capabilities/             # 权限配置（Tauri 2.x）
│   │   └── default.json          # 默认权限
│   ├── gen/                      # 自动生成的代码（勿手动改）
│   │   └── schemas/              # 配置 JSON Schema
│   ├── Cargo.toml                # Rust 依赖配置
│   ├── build.rs                  # 构建脚本
│   ├── tauri.conf.json           # ★ Tauri 核心配置
│   └── .gitignore
│
├── .gitignore
└── README.md
```

**`package.json` 详解**：

```json
{
  "name": "my-tauri-app",
  "version": "0.1.0",
  "description": "我的 Tauri 应用",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "tauri": "tauri"
  },
  "dependencies": {
    "@tauri-apps/api": "^2.0.0",          // Tauri 前端 API
    "@tauri-apps/plugin-opener": "^2.0.0" // opener 插件
  },
  "devDependencies": {
    "@tauri-apps/cli": "^2.0.0",          // Tauri CLI
    "typescript": "^5.0.0",
    "vite": "^5.0.0"
  }
}
```

**`src-tauri/Cargo.toml` 详解**：

```toml
[package]
name = "my-tauri-app"
version = "0.1.0"
description = "我的 Tauri 应用"
authors = ["you"]
edition = "2021"

# Tauri 应用的 Rust 库
[lib]
name = "my_tauri_app_lib"
crate-type = ["staticlib", "cdylib", "rlib"]

[build-dependencies]
tauri-build = { version = "2", features = [] }

[dependencies]
tauri = { version = "2", features = [] }
tauri-plugin-opener = "2"     # opener 插件
serde = { version = "1", features = ["derive"] }
serde_json = "1"

# 平台特定依赖
[target.'cfg(not(any(target_os = "android", target_os = "ios")))'.dependencies]
tauri-plugin-opener = "2"

[features]
# 默认特性
default = ["custom-protocol"]
custom-protocol = ["tauri/custom-protocol"]
```

**`src-tauri/build.rs`**：

```rust
fn main() {
    tauri_build::build()
}
```

这个脚本在 `cargo build` 前运行，由 `tauri-build` crate 处理，负责：① 验证 `tauri.conf.json`；② 生成配置相关的 Rust 代码；③ 处理图标等资源。

**`src-tauri/capabilities/default.json`**（Tauri 2.x 权限配置）：

```json
{
  "$schema": "../gen/schemas/desktop-schema.json",
  "identifier": "default",
  "description": "默认权限集合",
  "windows": ["main"],
  "permissions": [
    "core:default",
    "opener:default"
  ]
}
```

这个文件定义了名为 `default` 的权限集合，应用于 `main` 窗口，包含 `core:default`（核心默认权限）和 `opener:default`（opener 插件权限）。

**`tauri.conf.json` 简览**（下讲详解）：

```json
{
  "$schema": "https://schema.tauri.app/config/2",
  "productName": "my-tauri-app",
  "version": "0.1.0",
  "identifier": "com.example.my-tauri-app",
  "build": {
    "frontendDist": "../dist",
    "devUrl": "http://localhost:1420",
    "beforeDevCommand": "pnpm dev",
    "beforeBuildCommand": "pnpm build"
  },
  "app": {
    "windows": [
      {
        "title": "my-tauri-app",
        "width": 800,
        "height": 600
      }
    ],
    "security": {
      "csp": null
    }
  },
  "bundle": {
    "active": true,
    "targets": "all",
    "icon": [
      "icons/32x32.png",
      "icons/128x128.png",
      "icons/[email protected]",
      "icons/icon.icns",
      "icons/icon.ico"
    ]
  }
}
```

项目结构关系图：

```
┌─────────────────────────────────────────────────────────────┐
│                    Tauri 项目结构                            │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────────┐    ┌─────────────────────────┐    │
│  │   前端 (src/)        │    │  后端 (src-tauri/)       │    │
│  │                     │    │                         │    │
│  │  index.html         │    │  src/main.rs (入口)     │    │
│  │  main.ts            │    │  src/lib.rs  (逻辑)     │    │
│  │  styles.css         │    │  Cargo.toml (依赖)      │    │
│  │  components/        │    │  tauri.conf.json (配置) │    │
│  │                     │    │  capabilities/ (权限)   │    │
│  │  package.json       │    │  icons/ (图标)          │    │
│  │  vite.config.ts     │    │  build.rs (构建脚本)    │    │
│  └──────────┬──────────┘    └───────────┬─────────────┘    │
│             │                           │                  │
│             │   @tauri-apps/api         │  tauri crate     │
│             │   (invoke/emit)           │  (#[command])    │
│             │                           │                  │
│             └───────────┬───────────────┘                  │
│                         │                                  │
│                         ▼                                  │
│              ┌─────────────────────┐                       │
│              │   IPC 桥梁           │                       │
│              │   invoke / emit     │                       │
│              └─────────────────────┘                       │
│                                                             │
│  构建流程:                                                  │
│  1. pnpm build → 生成 dist/                                │
│  2. cargo build → 嵌入 dist/ 到 Rust 二进制                │
│  3. tauri build → 打包为 .app/.exe/.deb                    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 总结

- 前端部分（`src/`、`package.json`）与普通 Web 项目一致
- 后端部分（`src-tauri/`）是独立 Cargo 项目，含 Rust 代码、配置、图标、权限
- `tauri.conf.json` 是核心配置，`capabilities/` 是权限配置（2.x 新增）
- `@tauri-apps/api`（前端）和 `tauri` crate（后端）是 IPC 桥梁
- **常见坑**：`src-tauri/gen/` 是自动生成的，勿手动修改；`Cargo.toml` 的 `[lib]` 部分支持移动端，勿删除；权限配置错误会导致前端调用后端失败

---

# 第 2 章：核心架构与配置

本章深入 Tauri 的核心架构与配置系统。我们将理解 Tauri 的分层设计、掌握 `tauri.conf.json` 的各项配置、学习应用生命周期事件，并了解前端如何与 Tauri 集成构建。这些知识是构建复杂 Tauri 应用的基础。

## 第 5 讲：Tauri 架构总览

### 概念

Tauri 的架构分为四层：① **前端层**——Web 技术（HTML/CSS/JS）构建 UI，运行在系统 WebView 中；② **IPC 层**——前后端通信桥梁，通过 `invoke`（前端调后端）和 `emit`（后端推前端）实现；③ **Rust 后端层**——处理业务逻辑、系统调用、文件 IO 等；④ **系统层**——操作系统 API，通过 Tauri 的 Wry（WebView 封装）和 Tao（窗口管理）库访问。理解这四层的关系是掌握 Tauri 的关键。

### 原理

Tauri 的底层依赖两个关键 Rust 库：① **Wry**——跨平台 WebView 封装，统一了 macOS WKWebView、Windows WebView2、Linux WebKitGTK 的接口；② **Tao**——跨平台窗口管理，处理窗口创建、事件循环、菜单等。这两个库让 Tauri 的上层 API 跨平台一致。

IPC 机制基于 WebView 的自定义协议（custom protocol）。前端调用 `invoke('greet', { name: 'Alice' })` 时，Tauri 将其序列化为 JSON，通过自定义协议发送到 Rust 后端；Rust 执行 `greet` 命令后，结果序列化为 JSON 返回前端。这种设计让前后端完全解耦，通信开销极小（JSON 序列化 + IPC 调用，通常 <1ms）。

事件系统基于发布-订阅模式。后端 `app.emit('event-name', payload)` 发布事件，前端 `listen('event-name', callback)` 订阅。事件可全局广播或定向到特定窗口。这种机制适合后端主动通知前端的场景（如文件下载进度、后台任务完成）。

### 例子

Tauri 架构详细分层：

```
┌─────────────────────────────────────────────────────────────┐
│                    Tauri 应用架构                           │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  前端层 (WebView 中运行)                             │   │
│  │                                                     │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌────────────┐  │   │
│  │  │ HTML/CSS    │  │ JavaScript  │  │ 前端框架   │  │   │
│  │  │ (UI 结构)   │  │ (逻辑)      │  │ (React/Vue)│  │   │
│  │  └─────────────┘  └─────────────┘  └────────────┘  │   │
│  │                                                     │   │
│  │  @tauri-apps/api                                    │   │
│  │  - invoke('cmd', args)  调用后端命令                │   │
│  │  - listen('event', cb)  监听后端事件                │   │
│  │  - emit('event', data)  向后端发送事件              │   │
│  └──────────────────────┬──────────────────────────────┘   │
│                         │                                   │
│                    IPC 桥梁                                  │
│                    (JSON over                               │
│                     custom protocol)                        │
│                         │                                   │
│  ┌──────────────────────┴──────────────────────────────┐   │
│  │  Rust 后端层                                        │   │
│  │                                                     │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌────────────┐  │   │
│  │  │ #[command]  │  │ 事件系统    │  │ 状态管理   │  │   │
│  │  │ 函数        │  │ emit/listen │  │ State<T>   │  │   │
│  │  └─────────────┘  └─────────────┘  └────────────┘  │   │
│  │                                                     │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌────────────┐  │   │
│  │  │ 插件系统    │  │ 窗口管理    │  │ 系统 API   │  │   │
│  │  │ Plugin<T>   │  │ Window      │  │ 文件/网络  │  │   │
│  │  └─────────────┘  └─────────────┘  └────────────┘  │   │
│  └──────────────────────┬──────────────────────────────┘   │
│                         │                                   │
│                    Tauri 核心                               │
│                    (tauri crate)                            │
│                         │                                   │
│  ┌──────────────────────┴──────────────────────────────┐   │
│  │  系统层 (跨平台封装)                                 │   │
│  │                                                     │   │
│  │  ┌─────────────┐         ┌─────────────────────┐    │   │
│  │  │ Wry         │         │ Tao                 │    │   │
│  │  │ (WebView)   │         │ (窗口管理)          │    │   │
│  │  │             │         │                     │    │   │
│  │  │ macOS:      │         │ macOS: NSWindow     │    │   │
│  │  │  WKWebView  │         │ Windows: HWND       │    │   │
│  │  │             │         │ Linux: GTK Window   │    │   │
│  │  │ Windows:    │         │                     │    │   │
│  │  │  WebView2   │         │                     │    │   │
│  │  │             │         │                     │    │   │
│  │  │ Linux:      │         │                     │    │   │
│  │  │  WebKitGTK  │         │                     │    │   │
│  │  └─────────────┘         └─────────────────────┘    │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

IPC 通信流程：

```rust
// ===== 后端：定义命令 =====
use tauri::{Emitter, State};
use std::sync::Mutex;

#[tauri::command]
async fn process_data(
    input: String,
    state: State<'_, Mutex<AppState>>,
    app: tauri::AppHandle,
) -> Result<String, String> {
    // 1. 接收前端参数
    println!("收到: {}", input);

    // 2. 处理业务逻辑
    let result = format!("处理后的: {}", input);

    // 3. 可选：向后端发送事件（如进度通知）
    app.emit("processing-complete", &result).ok();

    // 4. 返回结果给前端
    Ok(result)
}

fn main() {
    tauri::Builder::default()
        .manage(Mutex::new(AppState::default()))
        .invoke_handler(tauri::generate_handler![process_data])
        .run(tauri::generate_context!())
        .unwrap();
}
```

```typescript
// ===== 前端：调用命令 + 监听事件 =====
import { invoke } from '@tauri-apps/api/core';
import { listen } from '@tauri-apps/api/event';

// 监听后端事件
const unlisten = await listen<string>('processing-complete', (event) => {
    console.log('处理完成:', event.payload);
});

// 调用后端命令
try {
    const result = await invoke<string>('process_data', {
        input: 'Hello Tauri'
    });
    console.log('结果:', result);
} catch (e) {
    console.error('错误:', e);
}

// 组件卸载时取消监听
// unlisten();
```

数据流：

```
前端 (JS)                           后端 (Rust)
   │                                    │
   │  invoke('process_data', {input})   │
   │ ─────────────────────────────────► │
   │                                    │  执行 process_data 函数
   │                                    │  - 读取参数
   │                                    │  - 访问 State
   │                                    │  - 业务逻辑
   │                                    │
   │            emit('processing-complete', result)
   │ ◄───────────────────────────────── │  (可选：事件通知)
   │                                    │
   │  listen callback 触发              │
   │  console.log(payload)              │
   │                                    │
   │       return Ok(result)            │
   │ ◄──────────────────────────────────│
   │                                    │
   │  invoke 的 Promise resolve         │
   │  console.log(result)               │
   │                                    │
```

### 总结

- Tauri 四层架构：前端（WebView）+ IPC + Rust 后端 + 系统层（Wry/Tao）
- Wry 封装跨平台 WebView，Tao 封装跨平台窗口管理
- IPC 基于 JSON over custom protocol，`invoke` 调用命令，`emit`/`listen` 收发事件
- 前端通过 `@tauri-apps/api`，后端通过 `tauri` crate 交互
- **常见坑**：IPC 参数需 JSON 可序列化（Rust 端需 `Serialize`/`Deserialize`）；大对象通过 IPC 传递有序列化开销，考虑用文件或共享内存；事件名需前后端一致

---

## 第 6 讲：tauri.conf.json 配置详解

### 概念

`tauri.conf.json` 是 Tauri 应用的核心配置文件，定义应用名称、版本、窗口、前端路径、打包选项、安全策略等。Tauri 2.x 的配置结构分为 `build`（构建）、`app`（应用）、`bundle`（打包）三大块。理解这个配置文件是定制 Tauri 应用的关键。

### 原理

`tauri.conf.json` 在编译时被 `tauri::generate_context!` 宏读取，配置被嵌入 Rust 二进制。配置结构对应 Rust 的 `tauri::Config` 结构体，每个字段都有类型约束。Tauri 提供 JSON Schema（`$schema` 字段）让编辑器（VS Code）提供自动补全和校验。

配置支持平台覆盖——`tauri.macos.conf.json`、`tauri.windows.conf.json`、`tauri.linux.conf.json` 可覆盖基础配置的平台特定字段。构建时 Tauri 会合并基础配置和平台配置。这让跨平台差异（如 macOS 用 `.dmg`、Windows 用 `.msi`）可在配置中表达。

配置的三大块：① `build`——定义前端构建命令、dev server URL、产物路径；② `app`——定义窗口、安全策略（CSP）、tray、beforeDarwin 等运行时配置；③ `bundle`——定义打包选项（图标、安装包格式、签名等）。

### 例子

完整的 `tauri.conf.json`（带详细注释）：

```json
{
  "$schema": "https://schema.tauri.app/config/2",
  "productName": "我的应用",
  "version": "1.0.0",
  "identifier": "com.example.myapp",

  "build": {
    "frontendDist": "../dist",
    "devUrl": "http://localhost:1420",
    "beforeDevCommand": "pnpm dev",
    "beforeBuildCommand": "pnpm build",
    "runner": ""
  },

  "app": {
    "windows": [
      {
        "label": "main",
        "title": "我的应用",
        "width": 1200,
        "height": 800,
        "minWidth": 800,
        "minHeight": 600,
        "resizable": true,
        "fullscreen": false,
        "center": true,
        "decorations": true,
        "transparent": false,
        "alwaysOnTop": false,
        "skipTaskbar": false,
        "focus": true,
        "theme": "Dark",
        "titleBarStyle": "Visible",
        "hiddenTitle": false,
        "maximized": false,
        "visible": true
      }
    ],
    "security": {
      "csp": "default-src 'self'; img-src 'self' data: https:; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline'",
      "devCsp": null,
      "freezePrototype": false,
      "dangerousDisableAssetCspModification": false,
      "assetProtocol": {
        "enable": true,
        "scope": ["**"]
      }
    },
    "trayIcon": {
      "iconPath": "icons/tray-icon.png",
      "iconAsTemplate": true,
      "menuOnLeftClick": false,
      "title": "我的应用"
    },
    "macOSPrivateApi": false,
    "withGlobalTauri": false
  },

  "bundle": {
    "active": true,
    "targets": "all",
    "icon": [
      "icons/32x32.png",
      "icons/128x128.png",
      "icons/[email protected]",
      "icons/icon.icns",
      "icons/icon.ico"
    ],
    "resources": ["resources/*"],
    "copyright": "© 2024 我的公司",
    "category": "Productivity",
    "shortDescription": "一个 Tauri 应用",
    "longDescription": "这是一个用 Tauri 构建的跨平台桌面应用",
    "appimage": {
      "bundleMediaFramework": true
    },
    "deb": {
      "depends": ["libwebkit2gtk-4.1-0", "libgtk-3-0"]
    },
    "macOS": {
      "frameworks": [],
      "minimumSystemVersion": "10.15",
      "exceptionDomain": "",
      "signingIdentity": null,
      "providerShortName": null,
      "entitlements": null
    },
    "windows": {
      "nsis": {
        "installerIcon": "icons/icon.ico",
        "installMode": "perMachine",
        "languages": ["en-US", "zh-CN"]
      },
      "wix": {
        "language": ["en-US", "zh-CN"]
      },
      "certificateThumbprint": null,
      "digestAlgorithm": "sha256",
      "timestampUrl": "http://timestamp.sectigo.com"
    },
    "createUpdaterArtifacts": true
  },

  "plugins": {
    "updater": {
      "active": true,
      "endpoints": [
        "https://myapp.com/updates/{{target}}/{{arch}}/{{current_version}}"
      ],
      "pubkey": "公钥内容..."
    }
  }
}
```

平台覆盖配置 `tauri.macos.conf.json`：

```json
{
  "app": {
    "windows": [
      {
        "title": "我的应用 - macOS",
        "titleBarStyle": "Overlay",
        "hiddenTitle": true
      }
    ]
  },
  "bundle": {
    "macOS": {
      "minimumSystemVersion": "11.0"
    }
  }
}
```

常用配置场景：

```json
{
  // 场景 1：无边框窗口（自定义标题栏）
  "app": {
    "windows": [{
      "decorations": false,
      "transparent": true
    }]
  },

  // 场景 2：启动隐藏窗口（后台应用）
  "app": {
    "windows": [{
      "visible": false
    }]
  },

  // 场景 3：多窗口
  "app": {
    "windows": [
      { "label": "main", "title": "主窗口", "width": 1200, "height": 800 },
      { "label": "settings", "title": "设置", "width": 600, "height": 400, "visible": false }
    ]
  },

  // 场景 4：仅打包特定格式
  "bundle": {
    "targets": ["deb", "appimage"]
  },

  // 场景 5：严格 CSP（生产环境）
  "app": {
    "security": {
      "csp": "default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'"
    }
  }
}
```

### 总结

- `tauri.conf.json` 分三大块：`build`（构建）、`app`（应用）、`bundle`（打包）
- `build.frontendDist` 指向前端产物，`devUrl` 指向 dev server
- `app.windows` 定义窗口列表，`app.security.csp` 定义内容安全策略
- `bundle` 控制打包格式、图标、签名等
- 平台覆盖用 `tauri.{platform}.conf.json`
- **常见坑**：`identifier` 必须是反向域名格式（如 `com.example.app`）；CSP 太严会阻断前端资源；`targets: "all"` 会生成所有格式，构建慢；图标需包含所有指定尺寸

---

## 第 7 讲：应用生命周期与事件

### 概念

Tauri 应用有完整的生命周期：启动 → 窗口创建 → 运行 → 退出。每个阶段都触发对应事件，开发者可注册回调在这些时机执行逻辑。核心生命周期事件包括：`setup`（应用启动）、`window-created`（窗口创建）、`close-requested`（窗口关闭请求）、`destroyed`（销毁）。掌握生命周期让应用能在正确时机初始化资源、保存状态、清理资源。

### 原理

Tauri 的生命周期基于事件循环（event loop）。应用启动后进入事件循环，持续处理系统事件（鼠标、键盘、窗口、定时器等）直到退出。`tauri::Builder` 提供多个钩子让开发者在生命周期各阶段插入逻辑：`.setup()` 在应用初始化后调用，`.on_window_event()` 处理窗口事件，`.run()` 启动事件循环。

`setup` 回调是最重要的生命周期钩子——它在 Tauri 初始化完成后、第一个窗口创建前调用，适合做全局初始化（加载配置、连接数据库、注册状态）。`setup` 接收 `&mut App` 参数，可访问所有 Tauri API。返回 `Result<(), Box<dyn Error>>` 让初始化失败时优雅退出。

窗口事件通过 `.on_window_event()` 注册，接收 `&WindowEvent`。常见事件：`CloseRequested`（用户点关闭按钮，可拦截）、`Focused`（窗口获焦）、`Resized`（窗口大小变化）、`Moved`（窗口移动）。`CloseRequested` 的 `api.prevent_close()` 可阻止关闭，用于"确认退出"对话框。

### 例子

```rust
use tauri::{Manager, WindowEvent, Emitter};
use std::sync::Mutex;

struct AppState {
    config: Mutex<Config>,
    start_time: std::time::Instant,
}

#[derive(Default, serde::Serialize)]
struct Config {
    theme: String,
    language: String,
}

#[tauri::command]
fn get_uptime(state: tauri::State<AppState>) -> u64 {
    state.start_time.elapsed().as_secs()
}

fn main() {
    tauri::Builder::default()
        .setup(|app| {
            // ===== 应用启动时初始化 =====
            println!("应用启动");

            // 加载配置
            let config = Config {
                theme: "dark".to_string(),
                language: "zh-CN".to_string(),
            };
            println!("配置加载完成: {:?}", config);

            // 注册全局状态
            app.manage(AppState {
                config: Mutex::new(config),
                start_time: std::time::Instant::now(),
            });

            // 获取主窗口
            let main_window = app.get_webview_window("main").unwrap();
            main_window.set_title("我的应用 - 已启动").ok();

            // 发送启动完成事件给前端
            app.emit("app-ready", { "version": "1.0.0" }).ok();

            Ok(())
        })
        .on_window_event(|window, event| {
            // ===== 窗口事件处理 =====
            match event {
                WindowEvent::CloseRequested { api, .. } => {
                    println!("窗口 {} 请求关闭", window.label());

                    // 拦截关闭，弹出确认对话框
                    // api.prevent_close();
                    // window.emit("confirm-exit", ()).ok();
                }
                WindowEvent::Focused(focused) => {
                    if *focused {
                        println!("窗口 {} 获得焦点", window.label());
                    } else {
                        println!("窗口 {} 失去焦点", window.label());
                    }
                }
                WindowEvent::Resized(physical_size) => {
                    println!("窗口 {} 大小变化: {}x{}",
                        window.label(),
                        physical_size.width,
                        physical_size.height
                    );
                }
                WindowEvent::Destroyed => {
                    println!("窗口 {} 已销毁", window.label());
                }
                _ => {}
            }
        })
        .invoke_handler(tauri::generate_handler![get_uptime])
        .run(tauri::generate_context!())
        .expect("error while running tauri application");
}
```

前端监听生命周期事件：

```typescript
import { listen } from '@tauri-apps/api/event';
import { getCurrentWindow } from '@tauri-apps/api/window';

// 监听应用启动完成
await listen('app-ready', (event) => {
    console.log('应用已就绪:', event.payload);
    document.getElementById('loading')?.remove();
});

// 监听退出确认
await listen('confirm-exit', async () => {
    const confirmed = confirm('确定要退出吗？未保存的数据将丢失。');
    if (confirmed) {
        await getCurrentWindow().destroy();
    }
});

// 监听窗口焦点变化
await getCurrentWindow().onFocusChanged(({ payload: focused }) => {
    console.log(focused ? '窗口获焦' : '窗口失焦');
    if (!focused) {
        // 自动保存草稿
        saveDraft();
    }
});

// 监听窗口大小变化
await getCurrentWindow().onResized(({ payload: size }) => {
    console.log(`窗口大小: ${size.width}x${size.height}`);
});
```

生命周期时序图：

```
应用启动
   │
   ▼
┌─────────────────────────────────┐
│  tauri::Builder::default()      │
│  - 注册插件                     │
│  - 注册状态 (.manage)           │
│  - 注册命令处理器               │
└────────────┬────────────────────┘
             │
             ▼
┌─────────────────────────────────┐
│  .setup(|app| { ... })          │  ← 初始化钩子
│  - 加载配置                     │     适合：读文件、连数据库
│  - 注册运行时状态               │
│  - 获取窗口句柄                 │
│  - 发送 "app-ready" 事件        │
└────────────┬────────────────────┘
             │
             ▼
┌─────────────────────────────────┐
│  创建窗口 (按 tauri.conf.json)  │
│  - 加载前端页面                 │
│  - 触发 window-created 事件     │
└────────────┬────────────────────┘
             │
             ▼
┌─────────────────────────────────┐
│  事件循环 (.run())              │  ← 应用运行中
│                                 │
│  持续处理:                      │
│  - 用户输入 (鼠标/键盘)         │
│  - 窗口事件 (焦点/大小/移动)    │
│  - IPC 调用 (invoke)            │
│  - 定时器/异步任务              │
│                                 │
│  .on_window_event() 回调:       │
│  - CloseRequested (可拦截)      │
│  - Focused                      │
│  - Resized                      │
│  - Moved                        │
│  - Destroyed                    │
└────────────┬────────────────────┘
             │
             ▼
┌─────────────────────────────────┐
│  应用退出                       │
│  - 所有窗口关闭                 │
│  - Drop 资源 (数据库连接等)     │
│  - 进程结束                     │
└─────────────────────────────────┘
```

### 总结

- `setup` 回调在应用初始化后、窗口创建前调用，适合全局初始化
- `.on_window_event()` 处理窗口事件，`CloseRequested` 可用 `api.prevent_close()` 拦截
- 前端通过 `listen()` 监听后端事件，通过 `getCurrentWindow()` 监听窗口事件
- 生命周期时序：Builder → setup → 窗口创建 → 事件循环 → 退出
- **常见坑**：`setup` 中访问窗口需用 `app.get_webview_window("label")`；`prevent_close` 后需手动 `window.destroy()`；状态需在 `setup` 中 `app.manage()` 注册后才能在命令中用 `State` 访问

---

## 第 8 讲：前端集成与构建

### 概念

Tauri 支持任意前端技术栈——React、Vue、Svelte、SolidJS、Angular 或原生 JS。前端通过 `@tauri-apps/api` 包调用后端能力。构建时，前端打包为静态资源（`dist/`），Rust 后端将其嵌入二进制。理解前端与 Tauri 的集成方式、Vite 配置、环境变量传递，是构建现代 Tauri 应用的关键。

### 原理

Tauri 前端集成的核心是 `@tauri-apps/api` npm 包，它提供类型安全的 IPC 调用：`invoke` 调用命令、`listen`/`emit` 收发事件、`getCurrentWindow` 操作窗口等。这个包是纯 JavaScript，不依赖 Rust，可在任何前端环境使用。

开发模式下，Tauri 启动前端 dev server（如 Vite 的 `localhost:1420`），WebView 加载该 URL。修改前端代码触发 HMR（热模块替换），无需重启 Tauri。修改 Rust 代码则需重新编译，`cargo tauri dev` 会自动重启。

构建模式下，`beforeBuildCommand`（如 `pnpm build`）生成前端静态资源到 `frontendDist`（如 `../dist`），Rust 的 `tauri::generate_context!` 宏将这些资源嵌入二进制。最终应用是单个可执行文件，包含前端 + 后端，无需外部文件。

Vite 是 Tauri 推荐的前端构建工具，因其开发服务器启动快、HMR 即时。Tauri 提供官方 Vite 插件 `@tauri-apps/plugin-vite`（可选），简化配置。关键 Vite 配置：`clearScreen: false`（避免 Tauri CLI 输出被清除）、`server.port: 1420`（与 `tauri.conf.json` 的 `devUrl` 一致）、`server.strictPort: true`（端口被占用时报错而非切换）。

### 例子

**React + Vite + Tauri 集成**：

`vite.config.ts`：

```typescript
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

export default defineConfig({
  plugins: [react()],
  clearScreen: false,
  server: {
    port: 1420,
    strictPort: true,
    watch: {
      // 忽略 Rust 文件变化（由 cargo tauri dev 处理）
      ignored: ["**/src-tauri/**"],
    },
  },
  // Tauri 特定环境变量
  envPrefix: ['VITE_', 'TAURI_ENV_*'],
  build: {
    // Tauri 在 webview 中运行，支持现代浏览器
    target: ['es2021', 'chrome100', 'safari13'],
    minify: !process.env.TAURI_ENV_DEBUG ? 'esbuild' : false,
    sourcemap: !!process.env.TAURI_ENV_DEBUG,
  },
});
```

React 组件中调用 Tauri：

```typescript
// src/App.tsx
import { useState, useEffect } from 'react';
import { invoke } from '@tauri-apps/api/core';
import { listen, UnlistenFn } from '@tauri-apps/api/event';

interface GreetingResult {
  message: string;
  timestamp: number;
}

function App() {
  const [name, setName] = useState('');
  const [result, setResult] = useState('');
  const [logs, setLogs] = useState<string[]>([]);

  useEffect(() => {
    // 监听后端事件
    let unlisten: UnlistenFn;
    (async () => {
      unlisten = await listen<string>('backend-log', (event) => {
        setLogs(prev => [...prev, event.payload]);
      });
    })();
    return () => {
      if (unlisten) unlisten();
    };
  }, []);

  const handleGreet = async () => {
    try {
      const res = await invoke<GreetingResult>('greet', { name });
      setResult(res.message);
    } catch (e) {
      setResult(`错误: ${e}`);
    }
  };

  return (
    <div className="app">
      <h1>Tauri + React</h1>
      <input
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="输入名字"
      />
      <button onClick={handleGreet}>打招呼</button>
      <p>{result}</p>

      <h2>后端日志</h2>
      <ul>
        {logs.map((log, i) => <li key={i}>{log}</li>)}
      </ul>
    </div>
  );
}

export default App;
```

**Vue 3 + Tauri 集成**：

```typescript
// src/App.vue
<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue';
import { invoke } from '@tauri-apps/api/core';
import { listen, type UnlistenFn } from '@tauri-apps/api/event';

const name = ref('');
const result = ref('');
const logs = ref<string[]>([]);
let unlisten: UnlistenFn;

async function greet() {
  try {
    result.value = await invoke<string>('greet', { name: name.value });
  } catch (e) {
    result.value = `错误: ${e}`;
  }
}

onMounted(async () => {
  unlisten = await listen<string>('backend-log', (event) => {
    logs.value.push(event.payload);
  });
});

onUnmounted(() => {
  if (unlisten) unlisten();
});
</script>

<template>
  <div class="app">
    <h1>Tauri + Vue</h1>
    <input v-model="name" placeholder="输入名字" />
    <button @click="greet">打招呼</button>
    <p>{{ result }}</p>
  </div>
</template>
```

**环境变量传递**：

Tauri 将构建环境变量注入前端：

```typescript
// 前端读取 Tauri 环境变量
const isDebug = import.meta.env.TAURI_ENV_DEBUG;  // true in dev
const platform = import.meta.env.TAURI_ENV_PLATFORM;  // 'macos' | 'windows' | 'linux'
const arch = import.meta.env.TAURI_ENV_ARCH;  // 'x86_64' | 'aarch64'

console.log(`运行在 ${platform} ${arch}, debug=${isDebug}`);
```

```rust
// 后端通过命令传递数据给前端
#[tauri::command]
fn get_app_info() -> AppInfo {
    AppInfo {
        version: env!("CARGO_PKG_VERSION").to_string(),
        platform: std::env::consts::OS.to_string(),
        debug: cfg!(debug_assertions),
    }
}
```

**前端构建产物结构**：

```
dist/                        # 前端构建产物
├── index.html               # HTML 入口
├── assets/
│   ├── index-abc123.js      # JS bundle（带 hash）
│   ├── index-def456.css     # CSS bundle
│   └── logo-789.png         # 图片
└── favicon.ico
```

构建流程：

```
pnpm tauri build
       │
       ▼
┌─────────────────────────────────────┐
│  1. beforeBuildCommand: pnpm build  │
│     → 生成 dist/                    │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│  2. cargo build --release           │
│     - tauri::generate_context!()    │
│       读取 tauri.conf.json          │
│       嵌入 dist/ 到二进制           │
│     - 编译 Rust 代码                │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│  3. tauri-build 打包                │
│     macOS: .app + .dmg              │
│     Windows: .exe + .msi            │
│     Linux: .deb + .rpm + AppImage   │
└─────────────────────────────────────┘
```

### 总结

- Tauri 支持任意前端框架，通过 `@tauri-apps/api` 调用后端
- Vite 配置需注意 `port: 1420`、`strictPort: true`、`clearScreen: false`
- 开发模式用 dev server + HMR，构建模式嵌入静态资源到二进制
- 环境变量通过 `TAURI_ENV_*` 前缀传递给前端
- **常见坑**：Vite 端口必须与 `tauri.conf.json` 的 `devUrl` 一致；`server.strictPort` 避免端口漂移；前端代码不能直接 `require('fs')` 等 Node 模块，需通过 Tauri API

---

# 第 3 章：前后端通信 IPC

前后端通信是 Tauri 应用的核心。本章深入 IPC 的两大机制：命令（invoke）和事件（emit/listen）。我们将学习命令的定义、参数传递、返回值处理，事件系统的发布订阅，状态管理，以及错误处理与异步命令。掌握这些，你就能构建出前后端高效协作的复杂应用。

## 第 9 讲：命令系统基础

### 概念

**命令（Command）**是 Tauri 前后端通信的主要方式——前端通过 `invoke('command_name', args)` 调用后端函数，后端用 `#[tauri::command]` 宏标记函数使其可被前端调用。命令是类型安全的——参数和返回值都需实现 `Serialize`/`Deserialize`，Tauri 自动处理 JSON 序列化。命令可以是同步或异步的，支持访问应用状态、窗口、AppHandle 等。

### 原理

`#[tauri::command]` 宏将普通 Rust 函数转换为 Tauri 命令处理器。它生成一个包装函数，负责：① 从前端传来的 JSON 参数中反序列化出函数参数；② 调用原函数；③ 将返回值序列化为 JSON 返回前端。这个过程对开发者透明——你只需写普通函数，宏处理所有 IPC 细节。

命令注册通过 `tauri::generate_handler![cmd1, cmd2, ...]` 宏，它生成一个命令处理器集合，传给 `Builder::invoke_handler`。只有注册的命令才能被前端调用，这是安全机制——防止前端调用任意 Rust 函数。

参数传递基于参数名匹配。前端 `invoke('greet', { name: 'Alice' })` 中的 `name` 键必须与后端函数的 `name` 参数名一致。Tauri 用 serde 反序列化，支持复杂类型（结构体、枚举、集合）。特殊参数如 `State`、`AppHandle`、`Window` 由 Tauri 自动注入，不需前端传递。

### 例子

```rust
use tauri::{State, AppHandle, Manager, Emitter};
use serde::{Serialize, Deserialize};
use std::sync::Mutex;

// ===== 基础命令 =====
#[tauri::command]
fn greet(name: &str) -> String {
    format!("你好, {}!", name)
}

// ===== 带多个参数的命令 =====
#[tauri::command]
fn add(a: i32, b: i32) -> i32 {
    a + b
}

// ===== 返回结构体 =====
#[derive(Serialize, Deserialize)]
struct User {
    id: u32,
    name: String,
    email: String,
    active: bool,
}

#[tauri::command]
fn get_user(user_id: u32) -> User {
    User {
        id: user_id,
        name: "张三".to_string(),
        email: "[email protected]".to_string(),
        active: true,
    }
}

// ===== 返回 Result（错误处理）=====
#[tauri::command]
fn divide(a: f64, b: f64) -> Result<f64, String> {
    if b == 0.0 {
        Err("除数不能为零".to_string())
    } else {
        Ok(a / b)
    }
}

// ===== 访问应用状态 =====
struct Counter(Mutex<i32>);

#[tauri::command]
fn increment(state: State<'_, Counter>) -> i32 {
    let mut count = state.0.lock().unwrap();
    *count += 1;
    *count
}

#[tauri::command]
fn get_count(state: State<'_, Counter>) -> i32 {
    *state.0.lock().unwrap()
}

// ===== 访问 AppHandle 和 Window =====
#[tauri::command]
fn show_window(app: AppHandle, label: &str) -> Result<(), String> {
    let window = app.get_webview_window(label)
        .ok_or_else(|| format!("窗口 {} 不存在", label))?;
    window.show().map_err(|e| e.to_string())?;
    window.set_focus().map_err(|e| e.to_string())?;
    Ok(())
}

// ===== 接收复杂参数 =====
#[derive(Deserialize)]
struct CreateTaskRequest {
    title: String,
    description: Option<String>,
    priority: u8,
    tags: Vec<String>,
}

#[tauri::command]
fn create_task(request: CreateTaskRequest) -> String {
    format!(
        "创建任务: {} (优先级: {}, 标签: {:?})",
        request.title, request.priority, request.tags
    )
}

fn main() {
    tauri::Builder::default()
        .manage(Counter(Mutex::new(0)))
        .invoke_handler(tauri::generate_handler![
            greet,
            add,
            get_user,
            divide,
            increment,
            get_count,
            show_window,
            create_task,
        ])
        .run(tauri::generate_context!())
        .unwrap();
}
```

前端调用：

```typescript
import { invoke } from '@tauri-apps/api/core';

// ===== 基础调用 =====
const greeting = await invoke<string>('greet', { name: 'Alice' });
console.log(greeting);  // "你好, Alice!"

// ===== 多参数 =====
const sum = await invoke<number>('add', { a: 3, b: 5 });
console.log(sum);  // 8

// ===== 返回结构体 =====
const user = await invoke<User>('get_user', { userId: 1 });
console.log(user.name);  // "张三"

// ===== 错误处理 =====
try {
    const result = await invoke<number>('divide', { a: 10, b: 0 });
} catch (e) {
    console.error(e);  // "除数不能为零"
}

// ===== 状态管理 =====
const count1 = await invoke<number>('increment');
const count2 = await invoke<number>('increment');
console.log(count1, count2);  // 1, 2

// ===== 复杂参数 =====
const result = await invoke<string>('create_task', {
    request: {
        title: '学习 Tauri',
        description: '完成 IPC 章节',
        priority: 1,
        tags: ['rust', 'tauri']
    }
});
```

命令调用流程：

```
前端 (JS)                                后端 (Rust)
   │                                        │
   │  invoke('greet', { name: 'Alice' })    │
   │ ─────────────────────────────────────► │
   │                                        │
   │           JSON: { "name": "Alice" }    │
   │                                        │
   │                          ┌─────────────┴─────────────┐
   │                          │  命令处理器 (宏生成)       │
   │                          │  1. 反序列化参数           │
   │                          │     name: &str = "Alice"  │
   │                          │  2. 调用原函数             │
   │                          │     greet("Alice")        │
   │                          │  3. 序列化返回值           │
   │                          │     "你好, Alice!"        │
   │                          └─────────────┬─────────────┘
   │                                        │
   │           JSON: "你好, Alice!"         │
   │ ◄───────────────────────────────────── │
   │                                        │
   │  Promise resolve("你好, Alice!")       │
   │                                        │
```

### 总结

- `#[tauri::command]` 宏将函数标记为可被前端调用的命令
- `tauri::generate_handler![cmd1, cmd2]` 注册命令，只有注册的才能被调用
- 参数按名匹配，前端 `{ name: 'Alice' }` 对应后端 `name: &str`
- 特殊参数 `State`/`AppHandle`/`Window` 由 Tauri 自动注入
- **常见坑**：参数名必须完全一致（包括大小写）；`&str` 参数前端传字符串即可；返回 `Result` 时 `Err` 会被前端 `catch` 捕获；命令必须注册到 `invoke_handler` 才能调用

---

## 第 10 讲：命令参数与返回值

### 概念

命令的参数和返回值是前后端数据交换的载体。Tauri 基于 serde 进行 JSON 序列化，支持丰富的 Rust 类型：基本类型（`i32`、`String`、`bool`）、集合（`Vec`、`HashMap`）、自定义结构体、枚举、`Option`、`Result`。理解类型映射规则、可选参数、默认值、引用参数，能让你设计出优雅的命令接口。

### 原理

Tauri 的参数和返回值序列化由 serde 处理。Rust 类型与 JSON 类型的映射：`String` ↔ JSON 字符串，`i32`/`f64` ↔ JSON 数字，`bool` ↔ JSON 布尔，`Vec<T>` ↔ JSON 数组，`struct` ↔ JSON 对象，`Option<T>` ↔ JSON 值或 `null`，`HashMap<K,V>` ↔ JSON 对象。

参数传递时，Tauri 将前端传来的整个参数对象（如 `{ name: 'Alice', age: 30 }`）反序列化为函数参数。每个参数名需与 JSON 键匹配。`Option<T>` 类型的参数可省略——前端不传时为 `None`。引用参数（`&str`、`&[u8]`）避免克隆，但生命周期限于命令执行期间。

返回值序列化时，`Result<T, E>` 的 `Ok(T)` 返回成功值，`Err(E)` 触发前端 Promise reject。`E` 需实现 `Serialize`，常见做法是用 `String`（简单错误消息）或自定义错误类型（实现 `Serialize`）。Tauri 2.x 推荐用 `tauri::ipc::InvokeError` 或自定义错误类型实现 `Into<InvokeError>`。

### 例子

```rust
use serde::{Serialize, Deserialize};
use std::collections::HashMap;

// ===== 基本类型参数 =====
#[tauri::command]
fn basic_types(
    int_val: i32,
    float_val: f64,
    bool_val: bool,
    string_val: String,
    string_ref: &str,  // 引用，避免克隆
) -> String {
    format!("{} {} {} {} {}", int_val, float_val, bool_val, string_val, string_ref)
}

// ===== 可选参数 =====
#[tauri::command]
fn with_optional(
    required: String,
    optional: Option<String>,  // 前端可不传
) -> String {
    format!("必填: {}, 可选: {:?}", required, optional)
}

// ===== 集合参数 =====
#[tauri::command]
fn process_list(items: Vec<String>) -> usize {
    items.len()
}

#[tauri::command]
fn process_map(data: HashMap<String, i32>) -> String {
    data.iter()
        .map(|(k, v)| format!("{}={}", k, v))
        .collect::<Vec<_>>()
        .join(", ")
}

// ===== 复杂结构体 =====
#[derive(Deserialize)]
struct FilterOptions {
    status: Option<String>,
    min_price: Option<f64>,
    max_price: Option<f64>,
    tags: Vec<String>,
}

#[derive(Serialize)]
struct Product {
    id: u32,
    name: String,
    price: f64,
    in_stock: bool,
}

#[tauri::command]
fn search_products(filter: FilterOptions) -> Vec<Product> {
    // 模拟搜索
    vec![
        Product {
            id: 1,
            name: "商品 A".to_string(),
            price: 99.9,
            in_stock: true,
        },
        Product {
            id: 2,
            name: "商品 B".to_string(),
            price: 199.9,
            in_stock: false,
        },
    ]
}

// ===== 枚举参数 =====
#[derive(Deserialize)]
#[serde(tag = "type", content = "value")]
enum ConfigValue {
    String(String),
    Number(f64),
    Boolean(bool),
    List(Vec<String>),
}

#[tauri::command]
fn set_config(key: String, value: ConfigValue) -> String {
    match value {
        ConfigValue::String(s) => format!("{} = \"{}\"", key, s),
        ConfigValue::Number(n) => format!("{} = {}", key, n),
        ConfigValue::Boolean(b) => format!("{} = {}", key, b),
        ConfigValue::List(l) => format!("{} = {:?}", key, l),
    }
}

// ===== 自定义错误类型 =====
#[derive(Debug, Serialize)]
enum AppError {
    NotFound(String),
    PermissionDenied(String),
    InvalidInput(String),
    Internal(String),
}

impl std::fmt::Display for AppError {
    fn fmt(&self, f: &mut std::fmt::Formatter) -> std::fmt::Result {
        match self {
            AppError::NotFound(msg) => write!(f, "未找到: {}", msg),
            AppError::PermissionDenied(msg) => write!(f, "权限不足: {}", msg),
            AppError::InvalidInput(msg) => write!(f, "无效输入: {}", msg),
            AppError::Internal(msg) => write!(f, "内部错误: {}", msg),
        }
    }
}

impl std::error::Error for AppError {}

// 实现 Into<InvokeError> 让 Tauri 能处理
impl Into<tauri::ipc::InvokeError> for AppError {
    fn into(self) -> tauri::ipc::InvokeError {
        tauri::ipc::InvokeError::from(self.to_string())
    }
}

#[tauri::command]
fn get_user_by_id(id: u32) -> Result<User, AppError> {
    if id == 0 {
        return Err(AppError::InvalidInput("ID 不能为 0".to_string()));
    }
    if id > 100 {
        return Err(AppError::NotFound(format!("用户 {} 不存在", id)));
    }
    Ok(User {
        id,
        name: format!("用户{}", id),
        email: format!("user{}@example.com", id),
        active: true,
    })
}

// ===== 返回二进制数据 =====
#[tauri::command]
fn generate_image(width: u32, height: u32) -> Vec<u8> {
    // 模拟生成 PNG 数据
    vec![0u8; (width * height * 4) as usize]
}

// ===== 接收二进制数据 =====
#[tauri::command]
fn process_image(data: Vec<u8>) -> usize {
    data.len()
}

#[derive(Serialize)]
struct User {
    id: u32,
    name: String,
    email: String,
    active: bool,
}

fn main() {
    tauri::Builder::default()
        .invoke_handler(tauri::generate_handler![
            basic_types,
            with_optional,
            process_list,
            process_map,
            search_products,
            set_config,
            get_user_by_id,
            generate_image,
            process_image,
        ])
        .run(tauri::generate_context!())
        .unwrap();
}
```

前端调用：

```typescript
import { invoke } from '@tauri-apps/api/core';

// ===== 基本类型 =====
await invoke('basic_types', {
    intVal: 42,
    floatVal: 3.14,
    boolVal: true,
    stringVal: 'hello',
    stringRef: 'world'
});

// ===== 可选参数（省略 optional）=====
await invoke('with_optional', { required: '必填值' });
await invoke('with_optional', { required: '必填值', optional: '可选值' });

// ===== 集合 =====
const len = await invoke<number>('process_list', {
    items: ['a', 'b', 'c']
});

const mapStr = await invoke<string>('process_map', {
    data: { a: 1, b: 2, c: 3 }
});

// ===== 复杂结构体 =====
const products = await invoke<Product[]>('search_products', {
    filter: {
        status: 'active',
        minPrice: 50,
        maxPrice: 200,
        tags: ['electronics', 'sale']
    }
});

// ===== 枚举（tagged union）=====
await invoke('set_config', {
    key: 'theme',
    value: { type: 'String', value: 'dark' }
});

await invoke('set_config', {
    key: 'fontSize',
    value: { type: 'Number', value: 14 }
});

// ===== 错误处理 =====
try {
    const user = await invoke<User>('get_user_by_id', { id: 5 });
} catch (e) {
    console.error(e);  // "无效输入: ID 不能为 0"
}

// ===== 二进制数据 =====
const imageBytes = await invoke<number[]>('generate_image', {
    width: 100,
    height: 100
});
console.log(`图片大小: ${imageBytes.length} 字节`);

// 传递 ArrayBuffer/Uint8Array
await invoke('process_image', {
    data: Array.from(new Uint8Array([1, 2, 3, 4]))
});
```

Rust 与 JSON 类型映射：

```
┌─────────────────────────────────────────────────────────────┐
│              Rust 类型与 JSON 类型映射                       │
├──────────────────┬──────────────────────────────────────────┤
│ Rust             │ JSON                                     │
├──────────────────┼──────────────────────────────────────────┤
│ String           │ string                                   │
│ &str             │ string (零拷贝引用)                      │
│ i32, i64, u32    │ number                                   │
│ f32, f64         │ number                                   │
│ bool             │ boolean                                  │
│ Vec<T>           │ array                                    │
│ &[u8]            │ array of numbers                         │
│ HashMap<K,V>     │ object                                   │
│ struct           │ object                                   │
│ Option<T>        │ T 或 null (可省略)                       │
│ Result<T,E>      │ T (成功) 或 reject E (失败)              │
│ enum (unit)      │ string                                   │
│ enum (tagged)    │ { "type": "...", "value": ... }          │
│ Vec<u8>          │ array of numbers                         │
└──────────────────┴──────────────────────────────────────────┘
```

### 总结

- 参数按名匹配，`Option<T>` 可省略，引用参数（`&str`）避免克隆
- 自定义错误类型实现 `Into<InvokeError>` 让前端能捕获结构化错误
- 枚举用 `#[serde(tag = "type", content = "value")]` 实现 tagged union
- 二进制数据用 `Vec<u8>` 传递，但大文件建议用文件路径或 `tauri-plugin-fs`
- **常见坑**：参数名 Rust 用 snake_case，JS 需用 camelCase（Tauri 自动转换）；`Option` 参数前端传 `null` 或省略；大 `Vec` 序列化开销大，考虑分页或流式传输

---

## 第 11 讲：事件系统

### 概念

**事件系统**是 Tauri 的另一种通信机制，用于后端主动通知前端，或前端向后端发送消息。与命令的"请求-响应"模式不同，事件是"发布-订阅"模式：发送方 `emit` 事件，所有订阅方 `listen` 接收。事件适合实时通知场景——下载进度、后台任务完成、状态变化等。事件可全局广播或定向到特定窗口。

### 原理

Tauri 事件系统基于发布-订阅模式。后端通过 `app.emit(event, payload)` 或 `window.emit(event, payload)` 发布事件，前端通过 `listen(event, callback)` 或 `once(event, callback)` 订阅。事件 payload 需实现 `Serialize`（后端发送）或 `Deserialize`（前端接收）。

事件的定向发送：`app.emit_to(target, event, payload)` 将事件发送到特定目标（窗口 label 或 webview label）。`app.emit` 广播到所有窗口。前端也可向后端发送事件——`emit(event, payload)`，后端用 `app.listen(event, callback)` 监听。

`listen` 返回一个 `unlisten` 函数，调用后取消订阅，防止内存泄漏。`once` 只监听一次，触发后自动取消。事件可携带任意可序列化数据，但注意大 payload 的序列化开销。

### 例子

```rust
use tauri::{AppHandle, Emitter, Listener, Manager};
use std::time::{Duration, Instant};
use std::sync::Mutex;
use serde::{Serialize, Deserialize};

// ===== 后端定义事件 payload =====
#[derive(Serialize, Clone)]
struct DownloadProgress {
    file_name: String,
    downloaded: u64,
    total: u64,
    speed: f64,  // MB/s
}

#[derive(Serialize, Clone)]
struct DownloadComplete {
    file_name: String,
    file_path: String,
    duration: Duration,
}

// ===== 模拟下载命令 =====
#[tauri::command]
async fn download_file(
    url: String,
    app: AppHandle,
) -> Result<String, String> {
    let file_name = url.split('/').last().unwrap_or("file").to_string();
    let total = 10_000_000u64;  // 10MB
    let start = Instant::now();

    // 模拟分块下载
    let mut downloaded = 0u64;
    while downloaded < total {
        // 模拟下载 1MB
        tokio::time::sleep(Duration::from_millis(100)).await;
        downloaded = (downloaded + 1_000_000).min(total);

        // 发送进度事件
        let progress = DownloadProgress {
            file_name: file_name.clone(),
            downloaded,
            total,
            speed: 10.0,  // 模拟速度
        };
        app.emit("download-progress", &progress).ok();
    }

    let duration = start.elapsed();
    let file_path = format!("/downloads/{}", file_name);

    // 发送完成事件
    app.emit("download-complete", DownloadComplete {
        file_name: file_name.clone(),
        file_path: file_path.clone(),
        duration,
    }).ok();

    Ok(file_path)
}

// ===== 后端监听前端事件 =====
#[derive(Deserialize)]
struct ClientMessage {
    event_type: String,
    data: serde_json::Value,
}

fn setup_event_listeners(app: &AppHandle) {
    app.listen("client-message", |event| {
        let payload: ClientMessage = serde_json::from_str(event.payload()).unwrap();
        println!("收到前端消息: {:?}", payload);
    });

    app.listen("cancel-download", |_event| {
        println!("取消下载请求");
        // 实际中这里会设置取消标志
    });
}

// ===== 定向发送事件到特定窗口 =====
#[tauri::command]
fn notify_window(app: AppHandle, window_label: String, message: String) {
    app.emit_to(&window_label, "notification", message).ok();
}

// ===== 后台定时任务发送事件 =====
#[derive(Serialize)]
struct SystemStats {
    cpu_usage: f32,
    memory_usage: f32,
    timestamp: u64,
}

fn start_stats_reporter(app: AppHandle) {
    tokio::spawn(async move {
        loop {
            tokio::time::sleep(Duration::from_secs(5)).await;

            let stats = SystemStats {
                cpu_usage: 45.2,
                memory_usage: 62.8,
                timestamp: std::time::SystemTime::now()
                    .duration_since(std::time::UNIX_EPOCH)
                    .unwrap()
                    .as_secs(),
            };
            app.emit("system-stats", &stats).ok();
        }
    });
}

fn main() {
    tauri::Builder::default()
        .setup(|app| {
            setup_event_listeners(app.handle());
            start_stats_reporter(app.handle().clone());
            Ok(())
        })
        .invoke_handler(tauri::generate_handler![
            download_file,
            notify_window,
        ])
        .run(tauri::generate_context!())
        .unwrap();
}
```

前端监听事件：

```typescript
import { invoke } from '@tauri-apps/api/core';
import { listen, once, emit } from '@tauri-apps/api/event';

interface DownloadProgress {
    fileName: string;
    downloaded: number;
    total: number;
    speed: number;
}

interface DownloadComplete {
    fileName: string;
    filePath: string;
    duration: number;
}

// ===== 监听下载进度 =====
const unlistenProgress = await listen<DownloadProgress>('download-progress', (event) => {
    const { fileName, downloaded, total, speed } = event.payload;
    const percent = (downloaded / total * 100).toFixed(1);
    console.log(`${fileName}: ${percent}% (${speed} MB/s)`);

    // 更新 UI
    document.getElementById('progress-bar')!.style.width = `${percent}%`;
});

// ===== 监听下载完成（只监听一次）=====
const unlistenComplete = await once<DownloadComplete>('download-complete', (event) => {
    console.log(`下载完成: ${event.payload.filePath}`);
    console.log(`耗时: ${event.payload.duration / 1000}秒`);
});

// ===== 触发下载 =====
async function startDownload() {
    try {
        const filePath = await invoke<string>('download_file', {
            url: 'https://example.com/file.zip'
        });
        console.log('文件保存到:', filePath);
    } catch (e) {
        console.error('下载失败:', e);
    }
}

// ===== 取消下载 =====
async function cancelDownload() {
    await emit('cancel-download', { reason: 'user_cancelled' });
}

// ===== 向后端发送消息 =====
async function sendMessage() {
    await emit('client-message', {
        eventType: 'user_action',
        data: { action: 'button_click', target: 'save' }
    });
}

// ===== 监听系统统计 =====
const unlistenStats = await listen<SystemStats>('system-stats', (event) => {
    const { cpuUsage, memoryUsage, timestamp } = event.payload;
    console.log(`[${timestamp}] CPU: ${cpuUsage}%, 内存: ${memoryUsage}%`);
});

// ===== 组件卸载时取消所有监听 =====
// window.addEventListener('beforeunload', () => {
//     unlistenProgress();
//     unlistenComplete();
//     unlistenStats();
// });
```

事件通信模式：

```
┌─────────────────────────────────────────────────────────────┐
│                   事件系统通信模式                          │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  模式 1: 后端 → 前端 (最常用)                               │
│  ┌──────────┐  emit('event', payload)  ┌──────────┐        │
│  │  Rust    │ ─────────────────────────► │  JS      │        │
│  │  后端    │                            │  listen  │        │
│  └──────────┘                            └──────────┘        │
│  场景: 下载进度、任务完成、状态变化                         │
│                                                             │
│  模式 2: 前端 → 后端                                        │
│  ┌──────────┐  emit('event', payload)  ┌──────────┐        │
│  │  JS      │ ─────────────────────────► │  Rust    │        │
│  │  emit    │                            │  listen  │        │
│  └──────────┘                            └──────────┘        │
│  场景: 用户操作通知、取消请求                               │
│                                                             │
│  模式 3: 后端 → 特定窗口                                    │
│  ┌──────────┐  emit_to('window2', ...)  ┌──────────┐        │
│  │  Rust    │ ─────────────────────────► │ Window 2 │        │
│  │  后端    │                            │  listen  │        │
│  └──────────┘                            └──────────┘        │
│  场景: 多窗口通信、定向通知                                 │
│                                                             │
│  模式 4: 前端 → 前端 (通过后端中转)                         │
│  ┌──────────┐  emit  ┌──────────┐  emit  ┌──────────┐      │
│  │ Window 1 │ ──────► │  Rust    │ ──────► │ Window 2 │      │
│  └──────────┘        └──────────┘        └──────────┘      │
│  场景: 多窗口同步                                           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 总结

- 事件系统是"发布-订阅"模式，`emit` 发布，`listen` 订阅，`once` 只监听一次
- 后端 `app.emit` 广播，`app.emit_to` 定向到特定窗口
- 前端 `listen` 返回 `unlisten` 函数，组件卸载时必须调用防止泄漏
- 事件适合实时通知（进度、状态变化），命令适合请求-响应
- **常见坑**：忘记 `unlisten` 导致内存泄漏；事件名前后端需一致；大 payload 序列化开销大；事件不保证顺序，需用序列号或时间戳

---

## 第 12 讲：状态管理

### 概念

**状态管理**是 Tauri 应用共享数据的核心机制。通过 `app.manage(state)` 注册全局状态，命令中用 `State<'_, T>` 访问。状态在应用生命周期内持久存在，所有命令共享。Tauri 的状态是类型安全的——每个类型只能注册一个实例，通过类型自动匹配。状态通常配合 `Mutex`/`RwLock` 实现内部可变性，因为命令可能并发执行。

### 原理

Tauri 的状态管理基于 `TypeMap`——一个以类型为键的 HashMap。`app.manage(value)` 将 `value` 存入，类型 `T` 作为键。`State<'_, T>` 是一个智能指针，从 TypeMap 中取出 `&T`。每个类型只能有一个实例，重复 `manage` 同类型会覆盖。

状态的并发安全：Tauri 命令可能并发执行（多个前端调用同时到达），因此状态需实现 `Send + Sync`。直接修改状态需用 `Mutex<T>` 或 `RwLock<T>` 包装，提供内部可变性。`Mutex::lock()` 返回 `Result<MutexGuard>`，获取锁后可修改内部数据。

状态的生命周期与应用一致——应用启动时注册，退出时销毁。`State<'_, T>` 的生命周期 `'a` 绑定到命令调用，确保引用在命令执行期间有效。状态可在 `setup` 中初始化（如读取配置文件），也可在 `Builder::manage` 中注册。

### 例子

```rust
use tauri::{State, Manager};
use std::sync::{Mutex, RwLock, Arc};
use std::collections::HashMap;
use serde::{Serialize, Deserialize};

// ===== 简单状态：计数器 =====
struct CounterState {
    count: Mutex<i32>,
}

#[tauri::command]
fn increment(state: State<'_, CounterState>) -> i32 {
    let mut count = state.count.lock().unwrap();
    *count += 1;
    *count
}

#[tauri::command]
fn decrement(state: State<'_, CounterState>) -> i32 {
    let mut count = state.count.lock().unwrap();
    *count -= 1;
    *count
}

#[tauri::command]
fn get_count(state: State<'_, CounterState>) -> i32 {
    *state.count.lock().unwrap()
}

// ===== 复杂状态：用户会话 =====
#[derive(Clone, Serialize, Deserialize)]
struct User {
    id: u32,
    name: String,
    email: String,
    role: String,
}

struct SessionState {
    current_user: RwLock<Option<User>>,
    token: RwLock<Option<String>>,
}

impl SessionState {
    fn new() -> Self {
        Self {
            current_user: RwLock::new(None),
            token: RwLock::new(None),
        }
    }
}

#[tauri::command]
fn login(
    username: String,
    password: String,
    state: State<'_, SessionState>,
) -> Result<User, String> {
    // 模拟验证
    if username == "admin" && password == "123456" {
        let user = User {
            id: 1,
            name: "管理员".to_string(),
            email: "[email protected]".to_string(),
            role: "admin".to_string(),
        };

        *state.current_user.write().unwrap() = Some(user.clone());
        *state.token.write().unwrap() = Some("token-abc123".to_string());

        Ok(user)
    } else {
        Err("用户名或密码错误".to_string())
    }
}

#[tauri::command]
fn logout(state: State<'_, SessionState>) {
    *state.current_user.write().unwrap() = None;
    *state.token.write().unwrap() = None;
}

#[tauri::command]
fn get_current_user(state: State<'_, SessionState>) -> Option<User> {
    state.current_user.read().unwrap().clone()
}

// ===== 集合状态：缓存 =====
struct CacheState {
    data: RwLock<HashMap<String, serde_json::Value>>,
}

impl CacheState {
    fn new() -> Self {
        Self {
            data: RwLock::new(HashMap::new()),
        }
    }
}

#[tauri::command]
fn cache_set(
    key: String,
    value: serde_json::Value,
    state: State<'_, CacheState>,
) {
    state.data.write().unwrap().insert(key, value);
}

#[tauri::command]
fn cache_get(
    key: String,
    state: State<'_, CacheState>,
) -> Option<serde_json::Value> {
    state.data.read().unwrap().get(&key).cloned()
}

#[tauri::command]
fn cache_delete(key: String, state: State<'_, CacheState>) -> bool {
    state.data.write().unwrap().remove(&key).is_some()
}

#[tauri::command]
fn cache_clear(state: State<'_, CacheState>) {
    state.data.write().unwrap().clear();
}

// ===== 配置状态：持久化 =====
struct ConfigState {
    settings: RwLock<Config>,
    config_path: std::path::PathBuf,
}

#[derive(Clone, Serialize, Deserialize)]
struct Config {
    theme: String,
    language: String,
    auto_save: bool,
    window_size: (u32, u32),
}

impl Default for Config {
    fn default() -> Self {
        Self {
            theme: "dark".to_string(),
            language: "zh-CN".to_string(),
            auto_save: true,
            window_size: (1200, 800),
        }
    }
}

impl ConfigState {
    fn new(app_data_dir: &std::path::Path) -> Self {
        let config_path = app_data_dir.join("config.json");
        let settings = if config_path.exists() {
            std::fs::read_to_string(&config_path)
                .ok()
                .and_then(|s| serde_json::from_str(&s).ok())
                .unwrap_or_default()
        } else {
            Config::default()
        };

        Self {
            settings: RwLock::new(settings),
            config_path,
        }
    }

    fn save(&self) -> Result<(), String> {
        let settings = self.settings.read().unwrap();
        let json = serde_json::to_string_pretty(&*settings)
            .map_err(|e| e.to_string())?;
        std::fs::write(&self.config_path, json)
            .map_err(|e| e.to_string())?;
        Ok(())
    }
}

#[tauri::command]
fn get_config(state: State<'_, ConfigState>) -> Config {
    state.settings.read().unwrap().clone()
}

#[tauri::command]
fn update_config(
    theme: Option<String>,
    language: Option<String>,
    auto_save: Option<bool>,
    state: State<'_, ConfigState>,
) -> Result<Config, String> {
    {
        let mut settings = state.settings.write().unwrap();
        if let Some(t) = theme { settings.theme = t; }
        if let Some(l) = language { settings.language = l; }
        if let Some(a) = auto_save { settings.auto_save = a; }
    }
    state.save()?;
    Ok(state.settings.read().unwrap().clone())
}

// ===== 多状态共存 =====
fn main() {
    tauri::Builder::default()
        .setup(|app| {
            // 获取应用数据目录
            let app_data_dir = app.path().app_data_dir().unwrap();

            // 注册多个状态
            app.manage(CounterState { count: Mutex::new(0) });
            app.manage(SessionState::new());
            app.manage(CacheState::new());
            app.manage(ConfigState::new(&app_data_dir));

            Ok(())
        })
        .invoke_handler(tauri::generate_handler![
            increment, decrement, get_count,
            login, logout, get_current_user,
            cache_set, cache_get, cache_delete, cache_clear,
            get_config, update_config,
        ])
        .run(tauri::generate_context!())
        .unwrap();
}
```

前端使用状态：

```typescript
import { invoke } from '@tauri-apps/api/core';

// ===== 计数器 =====
await invoke('increment');  // 1
await invoke('increment');  // 2
await invoke('decrement');  // 1
const count = await invoke<number>('get_count');

// ===== 用户会话 =====
const user = await invoke<User>('login', {
    username: 'admin',
    password: '123456'
});
console.log(`登录成功: ${user.name}`);

const current = await invoke<User | null>('get_current_user');
await invoke('logout');

// ===== 缓存 =====
await invoke('cache_set', {
    key: 'user_preference',
    value: { theme: 'dark', fontSize: 14 }
});

const cached = await invoke('cache_get', { key: 'user_preference' });
await invoke('cache_delete', { key: 'user_preference' });

// ===== 配置 =====
const config = await invoke<Config>('get_config');
await invoke('update_config', {
    theme: 'light',
    language: 'en-US'
});
```

状态管理架构：

```
┌─────────────────────────────────────────────────────────────┐
│                   Tauri 状态管理架构                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  TypeMap (类型 → 值 的映射)                          │   │
│  │                                                     │   │
│  │  CounterState  → CounterState { count: Mutex(0) }   │   │
│  │  SessionState  → SessionState { user: RwLock(None) }│   │
│  │  CacheState    → CacheState { data: RwLock(HashMap) }│  │
│  │  ConfigState   → ConfigState { settings: RwLock }   │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  命令通过 State<'_, T> 访问:                                │
│  ┌──────────────────────────────────────────────────┐      │
│  │  fn increment(state: State<'_, CounterState>) {  │      │
│  │      let mut count = state.count.lock().unwrap();│      │
│  │      *count += 1;                                │      │
│  │  }                                               │      │
│  └──────────────────────────────────────────────────┘      │
│                                                             │
│  并发安全:                                                  │
│  - Mutex<T>: 互斥锁，一次一个写者                           │
│  - RwLock<T>: 读写锁，多个读者或一个写者                    │
│  - State<'_, T>: 借用，生命周期绑定到命令调用               │
│                                                             │
│  生命周期:                                                  │
│  app.manage(T) ──► 应用启动到退出                           │
│  State<'_, T>  ──► 单次命令调用                            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 总结

- `app.manage(value)` 注册全局状态，`State<'_, T>` 在命令中访问
- 每个类型只能注册一个实例，通过类型自动匹配
- 状态需 `Send + Sync`，修改用 `Mutex`/`RwLock` 包装
- 状态适合全局共享数据（会话、缓存、配置），不适合临时数据
- **常见坑**：`Mutex::lock()` 可能 panic（中毒锁）；`RwLock` 适合读多写少；状态在 `setup` 中初始化才能在命令中访问；不要在状态中存储 `Window`/`AppHandle`，用 `app.get_webview_window()` 获取

---

## 第 13 讲：错误处理与异步命令

### 概念

生产级应用需要完善的错误处理和异步支持。Tauri 命令可返回 `Result<T, E>` 让前端捕获错误，`E` 需实现 `Serialize`。异步命令用 `async fn` 定义，适合 IO 密集操作（文件、网络、数据库）。Tauri 内置 Tokio 运行时，`async` 命令自动在其上执行。结合 `tokio::spawn` 可启动后台任务。

### 原理

Tauri 的错误处理基于 `Result`。命令返回 `Result<T, E>` 时，`Ok(T)` 序列化为 JSON 返回前端（Promise resolve），`Err(E)` 序列化后触发 Promise reject。`E` 需实现 `Serialize`，Tauri 2.x 推荐 `E: Into<InvokeError>`。简单场景用 `String` 错误，复杂场景用自定义错误枚举，配合 `thiserror` crate 自动派生。

异步命令用 `async fn` 定义，Tauri 自动在 Tokio 运行时上执行。`async` 命令不阻塞主线程，适合耗时操作（HTTP 请求、文件读写、数据库查询）。命令内可用 `.await` 调用其他异步函数，用 `tokio::spawn` 启动独立后台任务（不等待完成）。

Tauri 的 Tokio 运行时是多线程的，命令可能并发执行。因此访问共享状态需加锁（`Mutex`/`RwLock`）。`tokio::sync::Mutex` 是异步友好的锁（`await` 时不阻塞线程），但性能略低于 `std::sync::Mutex`（短时持锁用 std，长时持锁用 tokio）。

### 例子

```rust
use tauri::{State, AppHandle, Emitter};
use serde::{Serialize, Deserialize};
use std::sync::Arc;
use tokio::sync::Mutex;
use thiserror::Error;

// ===== 自定义错误类型 =====
#[derive(Debug, Error, Serialize)]
enum AppError {
    #[error("文件未找到: {0}")]
    NotFound(String),

    #[error("权限不足: {0}")]
    PermissionDenied(String),

    #[error("网络错误: {0}")]
    NetworkError(String),

    #[error("数据库错误: {0}")]
    DatabaseError(String),

    #[error("无效输入: {0}")]
    InvalidInput(String),

    #[error("内部错误: {0}")]
    Internal(String),
}

impl From<std::io::Error> for AppError {
    fn from(e: std::io::Error) -> Self {
        match e.kind() {
            std::io::ErrorKind::NotFound => AppError::NotFound(e.to_string()),
            std::io::ErrorKind::PermissionDenied => AppError::PermissionDenied(e.to_string()),
            _ => AppError::Internal(e.to_string()),
        }
    }
}

impl From<serde_json::Error> for AppError {
    fn from(e: serde_json::Error) -> Self {
        AppError::InvalidInput(format!("JSON 解析失败: {}", e))
    }
}

// ===== 异步命令：读取文件 =====
#[tauri::command]
async fn read_file(path: String) -> Result<String, AppError> {
    // 模拟异步 IO
    tokio::time::sleep(std::time::Duration::from_millis(100)).await;

    let content = tokio::fs::read_to_string(&path).await?;
    Ok(content)
}

// ===== 异步命令：HTTP 请求 =====
#[derive(Serialize)]
struct HttpResponse {
    status: u16,
    body: String,
    headers: std::collections::HashMap<String, String>,
}

#[tauri::command]
async fn fetch_url(url: String) -> Result<HttpResponse, AppError> {
    // 实际中用 reqwest crate
    // let client = reqwest::Client::new();
    // let resp = client.get(&url).send().await
    //     .map_err(|e| AppError::NetworkError(e.to_string()))?;

    // 模拟响应
    tokio::time::sleep(std::time::Duration::from_millis(500)).await;

    Ok(HttpResponse {
        status: 200,
        body: format!("响应内容 from {}", url),
        headers: std::collections::HashMap::from([
            ("content-type".to_string(), "application/json".to_string()),
        ]),
    })
}

// ===== 异步命令 + 状态 + 事件 =====
struct DownloadManager {
    active_downloads: Arc<Mutex<std::collections::HashMap<String, bool>>>,
}

#[derive(Serialize, Clone)]
struct DownloadStatus {
    id: String,
    status: String,  // "started", "progress", "completed", "cancelled"
    progress: f32,
}

#[tauri::command]
async fn start_download(
    url: String,
    app: AppHandle,
    state: State<'_, DownloadManager>,
) -> Result<String, AppError> {
    let download_id = format!("dl-{}", uuid::Uuid::new_v4());

    // 注册下载任务
    state.active_downloads.lock().await.insert(download_id.clone(), true);

    // 发送开始事件
    app.emit("download-status", DownloadStatus {
        id: download_id.clone(),
        status: "started".to_string(),
        progress: 0.0,
    }).ok();

    // 克隆 ID 用于后台任务
    let id = download_id.clone();
    let app_clone = app.clone();
    let active = state.active_downloads.clone();

    // 启动后台下载任务
    tokio::spawn(async move {
        for i in 1..=100 {
            // 检查是否取消
            let is_active = active.lock().await.get(&id).copied().unwrap_or(false);
            if !is_active {
                app_clone.emit("download-status", DownloadStatus {
                    id: id.clone(),
                    status: "cancelled".to_string(),
                    progress: i as f32,
                }).ok();
                return;
            }

            tokio::time::sleep(std::time::Duration::from_millis(50)).await;

            app_clone.emit("download-status", DownloadStatus {
                id: id.clone(),
                status: "progress".to_string(),
                progress: i as f32,
            }).ok();
        }

        app_clone.emit("download-status", DownloadStatus {
            id: id.clone(),
            status: "completed".to_string(),
            progress: 100.0,
        }).ok();

        active.lock().await.remove(&id);
    });

    Ok(download_id)
}

#[tauri::command]
async fn cancel_download(
    id: String,
    state: State<'_, DownloadManager>,
) -> Result<(), AppError> {
    let mut active = state.active_downloads.lock().await;
    active.insert(id, false);
    Ok(())
}

// ===== 异步命令：批量操作 =====
#[tauri::command]
async fn process_files(paths: Vec<String>) -> Result<Vec<String>, AppError> {
    let mut results = Vec::new();

    // 并发处理（用 tokio::join! 或 futures::join_all）
    let futures: Vec<_> = paths.iter().map(|path| {
        async {
            // 模拟处理每个文件
            tokio::time::sleep(std::time::Duration::from_millis(100)).await;
            Ok::<_, AppError>(format!("已处理: {}", path))
        }
    }).collect();

    for fut in futures {
        results.push(fut.await?);
    }

    Ok(results)
}

// ===== 同步命令 vs 异步命令 =====
#[tauri::command]
fn sync_command() -> String {
    // 同步命令会阻塞 IPC 线程，适合快速操作
    "同步结果".to_string()
}

#[tauri::command]
async fn async_command() -> String {
    // 异步命令不阻塞，适合耗时操作
    tokio::time::sleep(std::time::Duration::from_millis(100)).await;
    "异步结果".to_string()
}

fn main() {
    tauri::Builder::default()
        .manage(DownloadManager {
            active_downloads: Arc::new(Mutex::new(std::collections::HashMap::new())),
        })
        .invoke_handler(tauri::generate_handler![
            read_file,
            fetch_url,
            start_download,
            cancel_download,
            process_files,
            sync_command,
            async_command,
        ])
        .run(tauri::generate_context!())
        .unwrap();
}
```

前端处理异步与错误：

```typescript
import { invoke } from '@tauri-apps/api/core';
import { listen } from '@tauri-apps/api/event';

// ===== 异步命令调用 =====
try {
    const content = await invoke<string>('read_file', { path: '/tmp/test.txt' });
    console.log(content);
} catch (e) {
    // e 是 AppError 序列化后的对象
    console.error('读取失败:', e);
}

// ===== HTTP 请求 =====
try {
    const resp = await invoke<HttpResponse>('fetch_url', {
        url: 'https://api.example.com/data'
    });
    console.log(`状态: ${resp.status}`, resp.body);
} catch (e) {
    console.error('请求失败:', e);
}

// ===== 下载管理 =====
const unlisten = await listen<DownloadStatus>('download-status', (event) => {
    const { id, status, progress } = event.payload;
    console.log(`[${id}] ${status}: ${progress}%`);

    if (status === 'completed') {
        console.log('下载完成！');
    }
});

// 启动下载
const downloadId = await invoke<string>('start_download', {
    url: 'https://example.com/file.zip'
});

// 取消下载
// await invoke('cancel_download', { id: downloadId });

// ===== 批量操作 =====
const results = await invoke<string[]>('process_files', {
    paths: ['/tmp/a.txt', '/tmp/b.txt', '/tmp/c.txt']
});
console.log(results);
```

错误处理流程：

```
┌─────────────────────────────────────────────────────────────┐
│                   错误处理流程                              │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  后端命令                                                   │
│  ┌──────────────────────────────────────────────────┐       │
│  │  #[tauri::command]                               │       │
│  │  async fn read_file(path: String)                │       │
│  │      -> Result<String, AppError> {               │       │
│  │      let content = tokio::fs::read_to_string     │       │
│  │          (&path).await?;  // ? 自动转换错误      │       │
│  │      Ok(content)                                 │       │
│  │  }                                               │       │
│  └──────────────────────────────────────────────────┘       │
│                                                             │
│  成功: Ok(String)                                           │
│  ┌──────────────────────────────────────────────────┐       │
│  │  Tauri 序列化 String → JSON                      │       │
│  │  前端 Promise.resolve("文件内容")                │       │
│  └──────────────────────────────────────────────────┘       │
│                                                             │
│  失败: Err(AppError)                                        │
│  ┌──────────────────────────────────────────────────┐       │
│  │  Tauri 序列化 AppError → JSON                    │       │
│  │  前端 Promise.reject({ "NotFound": "..." })      │       │
│  │  catch (e) { console.error(e) }                  │       │
│  └──────────────────────────────────────────────────┘       │
│                                                             │
│  错误转换链:                                                │
│  std::io::Error ──► AppError::From ──► Result<_, AppError>  │
│  serde_json::Error ──► AppError::From ──► ? 自动转换        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 总结

- 命令返回 `Result<T, E>`，`E` 需 `Serialize`，推荐用 `thiserror` 派生自定义错误
- `async fn` 命令在 Tokio 运行时执行，不阻塞主线程
- `tokio::spawn` 启动后台任务，配合事件通知前端进度
- `?` 运算符配合 `From` impl 自动转换错误类型
- **常见坑**：同步命令会阻塞 IPC 线程，耗时操作用 `async`；`tokio::sync::Mutex` 与 `std::sync::Mutex` 选型（短锁用 std，长锁用 tokio）；后台任务需处理 panic，否则静默失败；`AppHandle` 可 `clone` 传入后台任务

---

# 第 4 章：窗口与系统集成

桌面应用需要与操作系统深度集成——多窗口、系统托盘、菜单、对话框、剪贴板等。本章深入 Tauri 的窗口管理和系统能力，让你构建出原生体验的桌面应用。

## 第 14 讲：窗口管理

### 概念

**窗口管理**是桌面应用的基础。Tauri 支持多窗口——每个窗口有独立的 `label`、WebView 和生命周期。窗口可在 `tauri.conf.json` 中预定义，也可在运行时动态创建。`WebviewWindow` API 提供窗口控制：显示/隐藏、最大化/最小化、设置标题、调整大小、置顶等。多窗口适合设置页、独立查看器、浮工具窗口等场景。

### 原理

Tauri 的窗口由 Tao 库管理，每个窗口对应一个原生 OS 窗口（macOS NSWindow、Windows HWND、Linux GTK Window）。WebView（Wry）嵌入窗口中渲染前端。`WebviewWindow` 是窗口和 WebView 的组合体，提供统一 API。

窗口通过 `label` 标识——字符串，在应用内唯一。`tauri.conf.json` 的 `app.windows` 数组定义启动时创建的窗口。运行时用 `WebviewWindowBuilder` 创建新窗口，`app.get_webview_window(label)` 获取已有窗口句柄。

窗口间通信通过事件系统——`window.emit` 发送到该窗口，`app.emit_to(label, event, payload)` 定向发送。窗口也可通过 `app.emit` 广播到所有窗口。这种机制让多窗口应用能协调状态。

### 例子

```rust
use tauri::{Manager, WebviewWindowBuilder, WebviewUrl, Emitter};
use serde::{Serialize, Deserialize};

// ===== 创建新窗口 =====
#[tauri::command]
async fn open_settings_window(app: tauri::AppHandle) -> Result<(), String> {
    // 检查窗口是否已存在
    if app.get_webview_window("settings").is_some() {
        // 已存在则聚焦
        let window = app.get_webview_window("settings").unwrap();
        window.show().map_err(|e| e.to_string())?;
        window.set_focus().map_err(|e| e.to_string())?;
        return Ok(());
    }

    // 创建新窗口
    WebviewWindowBuilder::new(
        &app,
        "settings",                          // 窗口 label
        WebviewUrl::App("index.html#/settings".into())  // 加载的页面
    )
    .title("设置")
    .inner_size(600.0, 400.0)
    .min_inner_size(400.0, 300.0)
    .resizable(true)
    .center()
    .build()
    .map_err(|e| e.to_string())?;

    Ok(())
}

// ===== 创建弹窗窗口 =====
#[tauri::command]
async fn open_modal(
    app: tauri::AppHandle,
    title: String,
    content: String,
) -> Result<(), String> {
    let label = format!("modal-{}", uuid::Uuid::new_v4());

    WebviewWindowBuilder::new(
        &app,
        &label,
        WebviewUrl::App(format!("index.html#/modal?content={}", urlencoding::encode(&content)))
    )
    .title(&title)
    .inner_size(400.0, 200.0)
    .resizable(false)
    .center()
    .always_on_top(true)
    .build()
    .map_err(|e| e.to_string())?;

    Ok(())
}

// ===== 窗口控制命令 =====
#[tauri::command]
fn minimize_window(window: tauri::WebviewWindow) -> Result<(), String> {
    window.minimize().map_err(|e| e.to_string())
}

#[tauri::command]
fn toggle_maximize(window: tauri::WebviewWindow) -> Result<(), String> {
    if window.is_maximized().map_err(|e| e.to_string())? {
        window.unmaximize().map_err(|e| e.to_string())?;
    } else {
        window.maximize().map_err(|e| e.to_string())?;
    }
    Ok(())
}

#[tauri::command]
fn close_window(window: tauri::WebviewWindow) -> Result<(), String> {
    window.close().map_err(|e| e.to_string())
}

#[tauri::command]
fn set_window_title(window: tauri::WebviewWindow, title: String) -> Result<(), String> {
    window.set_title(&title).map_err(|e| e.to_string())
}

// ===== 窗口间通信 =====
#[derive(Serialize, Deserialize, Clone)]
struct WindowMessage {
    from: String,
    content: String,
    timestamp: u64,
}

#[tauri::command]
fn send_to_window(
    app: tauri::AppHandle,
    target: String,
    message: String,
) -> Result<(), String> {
    app.emit_to(&target, "window-message", WindowMessage {
        from: "main".to_string(),
        content: message,
        timestamp: std::time::SystemTime::now()
            .duration_since(std::time::UNIX_EPOCH)
            .unwrap()
            .as_secs(),
    }).map_err(|e| e.to_string())?;
    Ok(())
}

// ===== 获取所有窗口 =====
#[tauri::command]
fn list_windows(app: tauri::AppHandle) -> Vec<String> {
    app.webview_windows()
        .keys()
        .cloned()
        .collect()
}

// ===== 窗口状态监听 =====
#[tauri::command]
fn setup_window_listener(window: tauri::WebviewWindow) {
    // 监听窗口焦点
    window.on_window_event(|event| {
        match event {
            tauri::WindowEvent::Focused(focused) => {
                println!("窗口焦点: {}", focused);
            }
            tauri::WindowEvent::Resized(size) => {
                println!("窗口大小: {}x{}", size.width, size.height);
            }
            tauri::WindowEvent::Moved(pos) => {
                println!("窗口位置: {}, {}", pos.x, pos.y);
            }
            _ => {}
        }
    });
}

fn main() {
    tauri::Builder::default()
        .invoke_handler(tauri::generate_handler![
            open_settings_window,
            open_modal,
            minimize_window,
            toggle_maximize,
            close_window,
            set_window_title,
            send_to_window,
            list_windows,
            setup_window_listener,
        ])
        .run(tauri::generate_context!())
        .unwrap();
}
```

前端窗口操作：

```typescript
import { invoke } from '@tauri-apps/api/core';
import { getCurrentWindow, getAllWindows } from '@tauri-apps/api/window';
import { listen } from '@tauri-apps/api/event';

// ===== 打开新窗口 =====
await invoke('open_settings_window');

// ===== 窗口控制 =====
await invoke('minimize_window');
await invoke('toggle_maximize');
await invoke('close_window');
await invoke('set_window_title', { title: '新标题' });

// ===== 使用 @tauri-apps/api/window =====
import { getCurrentWindow } from '@tauri-apps/api/window';

const currentWindow = getCurrentWindow();

// 最小化
await currentWindow.minimize();

// 最大化/还原
await currentWindow.toggleMaximize();

// 关闭
await currentWindow.close();

// 设置标题
await currentWindow.setTitle('新标题');

// 置顶
await currentWindow.setAlwaysOnTop(true);

// 设置大小
await currentWindow.setSize({ width: 800, height: 600 });

// 设置位置
await currentWindow.setPosition({ x: 100, y: 100 });

// ===== 监听窗口事件 =====
await currentWindow.onFocusChanged(({ payload: focused }) => {
    console.log(focused ? '获焦' : '失焦');
});

await currentWindow.onResized(({ payload: size }) => {
    console.log(`大小: ${size.width}x${size.height}`);
});

await currentWindow.onMoved(({ payload: pos }) => {
    console.log(`位置: ${pos.x}, ${pos.y}`);
});

// ===== 多窗口通信 =====
await listen<WindowMessage>('window-message', (event) => {
    console.log(`来自 ${event.payload.from}: ${event.payload.content}`);
});

// 向其他窗口发送消息
await invoke('send_to_window', {
    target: 'settings',
    message: '你好，设置窗口！'
});

// ===== 列出所有窗口 =====
const windows = await getAllWindows();
console.log('打开的窗口:', windows.map(w => w.label));

// ===== 创建窗口（前端直接创建）=====
import { WebviewWindow } from '@tauri-apps/api/webviewWindow';

const newWindow = new WebviewWindow('settings', {
    url: 'index.html#/settings',
    title: '设置',
    width: 600,
    height: 400,
    center: true,
});

newWindow.once('tauri://created', () => {
    console.log('窗口创建成功');
});

newWindow.once('tauri://error', (e) => {
    console.error('窗口创建失败:', e);
});
```

多窗口架构：

```
┌─────────────────────────────────────────────────────────────┐
│                   Tauri 多窗口架构                          │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  AppHandle (应用句柄)                                │   │
│  │                                                     │   │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────┐  │   │
│  │  │  Window 1    │  │  Window 2    │  │ Window 3 │  │   │
│  │  │  label:main  │  │ label:settings│ │ label:modal│  │   │
│  │  │              │  │              │  │          │  │   │
│  │  │  ┌────────┐  │  │  ┌────────┐  │  │ ┌────────┐│  │   │
│  │  │  │WebView │  │  │  │WebView │  │  │ │WebView ││  │   │
│  │  │  │(前端)  │  │  │  │(前端)  │  │  │ │(前端)  ││  │   │
│  │  │  └────────┘  │  │  └────────┘  │  │ └────────┘│  │   │
│  │  └──────────────┘  └──────────────┘  └──────────┘  │   │
│  │         │                │                │         │   │
│  │         └────────────────┼────────────────┘         │   │
│  │                          │                          │   │
│  │                   事件系统通信                       │   │
│  │             emit / emit_to / listen                 │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  通信方式:                                                  │
│  - app.emit(event, payload)          广播到所有窗口       │
│  - app.emit_to(label, event, payload) 定向到特定窗口      │
│  - window.emit(event, payload)       发送到该窗口         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 总结

- `WebviewWindowBuilder` 在运行时创建窗口，`label` 是唯一标识
- `app.get_webview_window(label)` 获取窗口句柄进行控制
- 窗口间通过事件系统通信：`emit_to` 定向，`emit` 广播
- 前端用 `@tauri-apps/api/window` 的 `getCurrentWindow`/`getAllWindows`/`WebviewWindow`
- **常见坑**：窗口 `label` 必须唯一，重复创建会报错；`WebviewUrl::App` 路径是前端路由，需前端支持；窗口关闭后句柄失效，需重新检查存在性

---

## 第 15 讲：系统托盘

### 概念

**系统托盘（System Tray）**是桌面应用常驻任务栏的图标，提供快速访问和后台运行能力。Tauri 2.x 通过 `TrayIconBuilder` 创建托盘图标，支持菜单、点击事件、图标更新。托盘适合"关闭窗口后保持运行"的应用——如即时通讯、下载工具、系统工具。

### 原理

Tauri 的系统托盘由 Tao 库封装各平台原生托盘 API。`TrayIconBuilder` 创建托盘实例，设置图标、提示文字、菜单。托盘菜单用 `MenuBuilder` 构建，包含菜单项（`MenuItem`）、分隔符（`PredefinedMenuItem::separator`）、子菜单等。

托盘事件通过 `on_tray_icon_event` 回调处理——左键点击、右键点击、双击等。macOS 上左键点击通常打开主窗口，右键点击显示菜单；Windows/Linux 上点击通常直接显示菜单。`on_menu_event` 处理菜单项点击。

托盘的生命周期与应用一致——创建后持续存在直到应用退出。关闭主窗口不退出应用（需拦截 `CloseRequested` 并 `hide` 而非 `close`），让应用在托盘后台运行。用户从托盘菜单选择"退出"时才真正退出。

### 例子

```rust
use tauri::{
    Manager, TrayIconBuilder, Menu, MenuItem, PredefinedMenuItem,
    tray::{TrayIconEvent, MouseButton, MouseButtonState},
    Emitter,
};

// ===== 托盘菜单事件处理 =====
fn handle_menu_event(app: &tauri::AppHandle, event: tauri::menu::MenuEvent) {
    match event.id().as_ref() {
        "show" => {
            if let Some(window) = app.get_webview_window("main") {
                let _ = window.show();
                let _ = window.set_focus();
            }
        }
        "hide" => {
            if let Some(window) = app.get_webview_window("main") {
                let _ = window.hide();
            }
        }
        "quit" => {
            app.exit(0);
        }
        "settings" => {
            // 打开设置窗口
            let _ = app.emit("open-settings", ());
        }
        _ => {}
    }
}

// ===== 托盘图标事件处理 =====
fn handle_tray_event(app: &tauri::AppHandle, event: TrayIconEvent) {
    match event {
        TrayIconEvent::Click {
            button: MouseButton::Left,
            button_state: MouseButtonState::Up,
            ..
        } => {
            // 左键点击：显示/隐藏主窗口
            if let Some(window) = app.get_webview_window("main") {
                if window.is_visible().unwrap_or(false) {
                    let _ = window.hide();
                } else {
                    let _ = window.show();
                    let _ = window.set_focus();
                }
            }
        }
        TrayIconEvent::DoubleClick {
            button: MouseButton::Left,
            ..
        } => {
            // 双击：显示主窗口
            if let Some(window) = app.get_webview_window("main") {
                let _ = window.show();
                let _ = window.set_focus();
            }
        }
        _ => {}
    }
}

fn main() {
    tauri::Builder::default()
        .setup(|app| {
            // ===== 构建托盘菜单 =====
            let menu = Menu::with_items(app, &[
                &MenuItem::with_id(app, "show", "显示主窗口", true, None::<&str>)?,
                &MenuItem::with_id(app, "hide", "隐藏主窗口", true, None::<&str>)?,
                &PredefinedMenuItem::separator(app)?,
                &MenuItem::with_id(app, "settings", "设置...", true, None::<&str>)?,
                &PredefinedMenuItem::separator(app)?,
                &MenuItem::with_id(app, "quit", "退出", true, None::<&str>)?,
            ])?;

            // ===== 创建托盘图标 =====
            TrayIconBuilder::new()
                .icon(app.default_window_icon().unwrap().clone())
                .menu(&menu)
                .tooltip("我的应用")
                .on_menu_event(handle_menu_event)
                .on_tray_icon_event(handle_tray_event)
                .build(app)?;

            Ok(())
        })
        .on_window_event(|window, event| {
            // 拦截主窗口关闭，改为隐藏（保持托盘运行）
            if let tauri::WindowEvent::CloseRequested { api, .. } = event {
                if window.label() == "main" {
                    api.prevent_close();
                    let _ = window.hide();
                }
            }
        })
        .run(tauri::generate_context!())
        .unwrap();
}
```

前端托盘交互：

```typescript
import { listen } from '@tauri-apps/api/event';
import { getCurrentWindow } from '@tauri-apps/api/window';

// 监听托盘菜单触发的设置打开
await listen('open-settings', () => {
    // 路由跳转到设置页
    window.location.hash = '#/settings';
});

// 关闭窗口时改为隐藏（实际由后端 prevent_close 处理）
// 用户点击关闭按钮 → 后端拦截 → 隐藏窗口 → 托盘仍运行

// 退出应用（从托盘菜单或设置页）
import { invoke } from '@tauri-apps/api/core';

// 通过命令退出
async function quitApp() {
    await invoke('quit_app');
}

// 或者直接退出
import { exit } from '@tauri-apps/plugin-process';
await exit(0);
```

动态更新托盘：

```rust
use tauri::{AppHandle, Manager};
use tauri::tray::TrayIconBuilder;

// ===== 动态更新托盘图标和提示 =====
#[tauri::command]
fn update_tray_status(
    app: AppHandle,
    status: String,  // "online", "busy", "away", "offline"
) -> Result<(), String> {
    let tray = app.tray_by_id("main-tray")
        .ok_or("托盘未找到")?;

    let (tooltip, icon_path) = match status.as_str() {
        "online" => ("在线", "icons/tray-online.png"),
        "busy" => ("忙碌", "icons/tray-busy.png"),
        "away" => ("离开", "icons/tray-away.png"),
        _ => ("离线", "icons/tray-offline.png"),
    };

    tray.set_tooltip(Some(tooltip))
        .map_err(|e| e.to_string())?;

    // 更新图标（需加载图片）
    // let icon = tauri::image::Image::from_path(icon_path)
    //     .map_err(|e| e.to_string())?;
    // tray.set_icon(Some(icon))
    //     .map_err(|e| e.to_string())?;

    Ok(())
}

// ===== 动态更新托盘菜单 =====
#[tauri::command]
fn update_tray_menu(app: AppHandle, items: Vec<String>) -> Result<(), String> {
    let tray = app.tray_by_id("main-tray")
        .ok_or("托盘未找到")?;

    let menu_items: Vec<_> = items.iter().enumerate().map(|(i, label)| {
        MenuItem::with_id(&app, format!("item-{}", i), label, true, None::<&str>)
            .unwrap()
    }).collect();

    let menu_refs: Vec<&dyn tauri::menu::IsMenuItem<tauri::Wry>> = menu_items
        .iter()
        .map(|item| item as &dyn tauri::menu::IsMenuItem<tauri::Wry>)
        .collect();

    let menu = Menu::with_items(&app, &menu_refs)
        .map_err(|e| e.to_string())?;

    tray.set_menu(Some(menu))
        .map_err(|e| e.to_string())?;

    Ok(())
}
```

托盘应用架构：

```
┌─────────────────────────────────────────────────────────────┐
│                   托盘应用架构                              │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  系统托盘 (TrayIcon)                                │   │
│  │                                                     │   │
│  │  ┌──────┐                                          │   │
│  │  │ 图标 │  ← TrayIconBuilder::icon()                │   │
│  │  └──────┘                                          │   │
│  │  "我的应用"  ← set_tooltip()                        │   │
│  │                                                     │   │
│  │  菜单 (右键点击显示):                                │   │
│  │  ┌────────────────────┐                            │   │
│  │  │ 显示主窗口          │ ← on_menu_event("show")   │   │
│  │  │ 隐藏主窗口          │ ← on_menu_event("hide")   │   │
│  │  ├────────────────────┤                            │   │
│  │  │ 设置...            │ ← on_menu_event("settings")│   │
│  │  ├────────────────────┤                            │   │
│  │  │ 退出               │ ← on_menu_event("quit")    │   │
│  │  └────────────────────┘                            │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  事件处理:                                                  │
│  - 左键点击: 切换主窗口显示/隐藏                            │
│  - 双击: 显示主窗口                                         │
│  - 右键点击: 显示菜单 (系统默认行为)                        │
│                                                             │
│  窗口关闭行为:                                              │
│  用户点关闭按钮 → CloseRequested → prevent_close → hide    │
│  (窗口隐藏，应用继续在托盘运行)                              │
│                                                             │
│  退出方式:                                                  │
│  - 托盘菜单 "退出" → app.exit(0)                           │
│  - 命令调用 quit_app → app.exit(0)                         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 总结

- `TrayIconBuilder` 创建托盘，设置图标、提示、菜单
- `on_menu_event` 处理菜单点击，`on_tray_icon_event` 处理图标点击
- 关闭窗口改为隐藏（`prevent_close` + `hide`），让应用后台运行
- 托盘适合常驻应用（IM、下载工具、系统工具）
- **常见坑**：托盘图标需透明背景的 PNG；macOS 上 `icon_as_template: true` 让图标适应深色/浅色模式；托盘菜单项 ID 需唯一；`tray_by_id` 的 ID 是 `TrayIconBuilder::id` 设置的

---

## 第 16 讲：应用菜单

### 概念

**应用菜单**是桌面应用顶部的菜单栏（macOS）或窗口菜单（Windows/Linux）。Tauri 通过 `Menu` 和 `MenuItem` 构建菜单，支持子菜单、分隔符、快捷键、预定义菜单项（剪切/复制/粘贴）。菜单项点击触发事件，可绑定到命令或前端事件。macOS 上菜单是应用级的（顶部菜单栏），Windows/Linux 上是窗口级的。

### 原理

Tauri 的菜单系统由 Tao 封装各平台原生菜单。`Menu::with_items(app, &[items])` 构建菜单，`MenuItem::with_id(app, id, text, enabled, accelerator)` 创建菜单项。`accelerator` 是快捷键（如 `"CmdOrCtrl+S"`），跨平台兼容（`CmdOrCtrl` 在 macOS 是 Cmd，Windows/Linux 是 Ctrl）。

菜单分两层：① **应用菜单**（macOS 顶部菜单栏）——通过 `app.set_menu(menu)` 设置；② **窗口菜单**（Windows/Linux 窗口内）——通过 `window.set_menu(menu)` 设置。Tauri 2.x 统一了这两者，`set_menu` 在 macOS 设置应用菜单，在其他平台设置窗口菜单。

预定义菜单项（`PredefinedMenuItem`）提供系统标准操作——`separator`（分隔符）、`copy`/`paste`/`cut`（剪贴板）、`minimize`/`close_window`（窗口操作）、`about`/`settings`（系统对话框）。这些菜单项自动绑定系统行为，无需手动处理事件。

### 例子

```rust
use tauri::{
    Manager, Menu, MenuItem, PredefinedMenuItem, Submenu,
    menu::{MenuEvent, MenuId},
    Emitter,
};

fn handle_menu_event(app: &tauri::AppHandle, event: MenuEvent) {
    match event.id().as_ref() {
        "new-file" => {
            let _ = app.emit("menu-new-file", ());
        }
        "open-file" => {
            let _ = app.emit("menu-open-file", ());
        }
        "save-file" => {
            let _ = app.emit("menu-save-file", ());
        }
        "save-as" => {
            let _ = app.emit("menu-save-as", ());
        }
        "export-pdf" => {
            let _ = app.emit("menu-export-pdf", ());
        }
        "toggle-theme" => {
            let _ = app.emit("menu-toggle-theme", ());
        }
        "zoom-in" => {
            let _ = app.emit("menu-zoom-in", ());
        }
        "zoom-out" => {
            let _ = app.emit("menu-zoom-out", ());
        }
        "reset-zoom" => {
            let _ = app.emit("menu-reset-zoom", ());
        }
        "about" => {
            // 显示关于对话框
            let _ = app.emit("show-about", ());
        }
        _ => {}
    }
}

fn build_menu(app: &tauri::AppHandle) -> tauri::Result<Menu<tauri::Wry>> {
    // ===== 文件菜单 =====
    let file_menu = Submenu::with_items(app, "文件", true, &[
        &MenuItem::with_id(app, "new-file", "新建", true, Some("CmdOrCtrl+N"))?,
        &MenuItem::with_id(app, "open-file", "打开...", true, Some("CmdOrCtrl+O"))?,
        &PredefinedMenuItem::separator(app)?,
        &MenuItem::with_id(app, "save-file", "保存", true, Some("CmdOrCtrl+S"))?,
        &MenuItem::with_id(app, "save-as", "另存为...", true, Some("CmdOrCtrl+Shift+S"))?,
        &PredefinedMenuItem::separator(app)?,
        &MenuItem::with_id(app, "export-pdf", "导出 PDF", true, Some("CmdOrCtrl+E"))?,
        &PredefinedMenuItem::separator(app)?,
        &PredefinedMenuItem::close_window(app, Some("关闭窗口"))?,
    ])?;

    // ===== 编辑菜单 =====
    let edit_menu = Submenu::with_items(app, "编辑", true, &[
        &PredefinedMenuItem::undo(app, Some("撤销"))?,
        &PredefinedMenuItem::redo(app, Some("重做"))?,
        &PredefinedMenuItem::separator(app)?,
        &PredefinedMenuItem::cut(app, Some("剪切"))?,
        &PredefinedMenuItem::copy(app, Some("复制"))?,
        &PredefinedMenuItem::paste(app, Some("粘贴"))?,
        &PredefinedMenuItem::select_all(app, Some("全选"))?,
    ])?;

    // ===== 视图菜单 =====
    let view_menu = Submenu::with_items(app, "视图", true, &[
        &MenuItem::with_id(app, "toggle-theme", "切换主题", true, Some("CmdOrCtrl+Shift+T"))?,
        &PredefinedMenuItem::separator(app)?,
        &MenuItem::with_id(app, "zoom-in", "放大", true, Some("CmdOrCtrl+Plus"))?,
        &MenuItem::with_id(app, "zoom-out", "缩小", true, Some("CmdOrCtrl+-"))?,
        &MenuItem::with_id(app, "reset-zoom", "实际大小", true, Some("CmdOrCtrl+0"))?,
    ])?;

    // ===== 窗口菜单 =====
    let window_menu = Submenu::with_items(app, "窗口", true, &[
        &PredefinedMenuItem::minimize(app, Some("最小化"))?,
        &PredefinedMenuItem::maximize(app, Some("最大化"))?,
        &PredefinedMenuItem::fullscreen(app, Some("全屏"))?,
    ])?;

    // ===== 帮助菜单 =====
    let help_menu = Submenu::with_items(app, "帮助", true, &[
        &MenuItem::with_id(app, "docs", "文档", true, None::<&str>)?,
        &MenuItem::with_id(app, "shortcuts", "快捷键", true, None::<&str>)?,
        &PredefinedMenuItem::separator(app)?,
        &MenuItem::with_id(app, "about", "关于", true, None::<&str>)?,
    ])?;

    // ===== 组合主菜单 =====
    Menu::with_items(app, &[
        &file_menu,
        &edit_menu,
        &view_menu,
        &window_menu,
        &help_menu,
    ])
}

fn main() {
    tauri::Builder::default()
        .setup(|app| {
            let menu = build_menu(app.handle())?;
            app.set_menu(menu)?;
            app.on_menu_event(handle_menu_event);
            Ok(())
        })
        .run(tauri::generate_context!())
        .unwrap();
}
```

前端响应菜单事件：

```typescript
import { listen } from '@tauri-apps/api/event';

// ===== 文件菜单事件 =====
await listen('menu-new-file', () => {
    // 新建文件
    createNewDocument();
});

await listen('menu-open-file', async () => {
    // 打开文件对话框
    const filePath = await openFilePicker();
    if (filePath) {
        await loadFile(filePath);
    }
});

await listen('menu-save-file', async () => {
    if (currentDocument.path) {
        await saveFile(currentDocument.path, currentDocument.content);
    } else {
        // 没有路径则另存为
        await saveAs();
    }
});

await listen('menu-save-as', async () => {
    await saveAs();
});

// ===== 视图菜单事件 =====
await listen('menu-toggle-theme', () => {
    toggleTheme();
});

await listen('menu-zoom-in', () => {
    document.body.style.zoom = String(parseFloat(document.body.style.zoom || '1') + 0.1);
});

await listen('menu-zoom-out', () => {
    document.body.style.zoom = String(parseFloat(document.body.style.zoom || '1') - 0.1);
});

await listen('menu-reset-zoom', () => {
    document.body.style.zoom = '1';
});

// ===== 关于对话框 =====
await listen('show-about', () => {
    showAboutDialog();
});

// ===== 动态更新菜单项状态 =====
import { getCurrentWindow } from '@tauri-apps/api/window';

async function updateMenuItemEnabled(itemId: string, enabled: boolean) {
    // Tauri 2.x 支持动态菜单更新
    // 实际 API 可能需要通过命令实现
    await invoke('update_menu_item', { itemId, enabled });
}
```

动态菜单管理：

```rust
use tauri::{AppHandle, Manager};

// ===== 动态启用/禁用菜单项 =====
#[tauri::command]
fn update_menu_item(
    app: AppHandle,
    item_id: String,
    enabled: bool,
) -> Result<(), String> {
    if let Some(menu) = app.menu() {
        // 查找菜单项并更新状态
        // Tauri 2.x 的菜单 API 仍在演进，具体方法以文档为准
        println!("更新菜单项 {}: enabled={}", item_id, enabled);
    }
    Ok(())
}

// ===== 根据状态切换菜单 =====
#[tauri::command]
fn set_edit_mode(app: AppHandle, is_editing: bool) -> Result<(), String> {
    let menu = if is_editing {
        // 编辑模式菜单
        build_edit_menu(&app).map_err(|e| e.to_string())?
    } else {
        // 查看模式菜单
        build_view_menu(&app).map_err(|e| e.to_string())?
    };

    app.set_menu(menu).map_err(|e| e.to_string())?;
    Ok(())
}

fn build_edit_menu(app: &AppHandle) -> tauri::Result<Menu<tauri::Wry>> {
    Menu::with_items(app, &[
        &Submenu::with_items(app, "文件", true, &[
            &MenuItem::with_id(app, "save", "保存", true, Some("CmdOrCtrl+S"))?,
            &MenuItem::with_id(app, "cancel", "取消编辑", true, Some("Escape"))?,
        ])?,
    ])
}

fn build_view_menu(app: &AppHandle) -> tauri::Result<Menu<tauri::Wry>> {
    Menu::with_items(app, &[
        &Submenu::with_items(app, "文件", true, &[
            &MenuItem::with_id(app, "edit", "编辑", true, Some("CmdOrCtrl+E"))?,
            &MenuItem::with_id(app, "share", "分享", true, None::<&str>)?,
        ])?,
    ])
}
```

菜单结构：

```
┌─────────────────────────────────────────────────────────────┐
│  文件    编辑    视图    窗口    帮助        ← 顶部菜单栏   │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  点击"文件"展开:                                            │
│  ┌──────────────────────┐                                  │
│  │ 新建          ⌘N     │ ← MenuItem + accelerator         │
│  │ 打开...       ⌘O     │                                  │
│  ├──────────────────────┤ ← PredefinedMenuItem::separator  │
│  │ 保存          ⌘S     │                                  │
│  │ 另存为...  ⌘⇧S     │                                  │
│  ├──────────────────────┤                                  │
│  │ 导出 PDF      ⌘E     │                                  │
│  ├──────────────────────┤                                  │
│  │ 关闭窗口      ⌘W     │ ← PredefinedMenuItem::close      │
│  └──────────────────────┘                                  │
│                                                             │
│  快捷键格式:                                                │
│  - "CmdOrCtrl+S": macOS=⌘S, Win/Linux=Ctrl+S              │
│  - "Shift+CmdOrCtrl+S": ⌘⇧S / Ctrl+Shift+S               │
│  - "Alt+F4": Alt+F4                                        │
│  - "F5": F5                                                │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 总结

- `Menu::with_items` 构建菜单，`Submenu` 创建子菜单，`MenuItem` 创建菜单项
- `PredefinedMenuItem` 提供系统标准操作（剪切/复制/粘贴/最小化等）
- `accelerator` 设置快捷键，`CmdOrCtrl` 跨平台兼容
- `app.on_menu_event` 处理菜单点击，通过 `emit` 转发到前端
- **常见坑**：macOS 菜单是应用级，Windows/Linux 是窗口级；菜单项 ID 需唯一；`PredefinedMenuItem` 自动绑定系统行为，无需手动处理；动态菜单更新 API 在 2.x 仍在演进

---

## 第 17 讲：系统对话框与剪贴板

### 概念

**系统对话框**包括文件选择（打开/保存）、消息框（确认/警告）。**剪贴板**让应用读写系统剪贴板。这些是桌面应用的常用能力。Tauri 通过 `tauri-plugin-dialog` 和 `tauri-plugin-clipboard-manager` 插件提供，支持原生对话框和剪贴板操作。

### 原理

Tauri 2.x 将系统能力拆分为独立插件——`tauri-plugin-dialog`（对话框）、`tauri-plugin-clipboard-manager`（剪贴板）、`tauri-plugin-fs`（文件系统）等。这种模块化设计让应用只引入需要的能力，减小体积。插件通过 `tauri::Builder::plugin()` 注册，前端通过 `@tauri-apps/plugin-*` 包调用。

文件对话框由 OS 原生提供——macOS NSOpenPanel/NSSavePanel、Windows IFileOpenDialog/IFileSaveDialog、Linux GTK FileChooserDialog。`dialog.open` 支持文件/目录选择、多选、过滤器（按扩展名过滤）。`dialog.save` 让用户选择保存路径。`dialog.message` 显示消息框（确认/警告/错误）。

剪贴板插件封装各平台剪贴板 API。`writeText` 写入文本，`readText` 读取文本。`writeImage`/`readImage` 处理图片。剪贴板操作是同步的，但 Tauri 包装为异步以保持 API 一致性。

### 例子

**安装插件**：

```bash
# 后端
cargo add tauri-plugin-dialog tauri-plugin-clipboard-manager

# 前端
pnpm add @tauri-apps/plugin-dialog @tauri-apps/plugin-clipboard-manager
```

**后端注册插件**：

```rust
use tauri::Manager;

fn main() {
    tauri::Builder::default()
        .plugin(tauri_plugin_dialog::init())
        .plugin(tauri_plugin_clipboard_manager::init())
        .setup(|app| {
            Ok(())
        })
        .run(tauri::generate_context!())
        .unwrap();
}
```

**前端使用对话框**：

```typescript
import { open, save, message, ask, confirm } from '@tauri-apps/plugin-dialog';

// ===== 文件打开对话框 =====
const filePath = await open({
    title: '选择文件',
    multiple: false,
    filters: [
        { name: '文本文件', extensions: ['txt', 'md'] },
        { name: '图片', extensions: ['png', 'jpg', 'jpeg', 'gif'] },
        { name: '所有文件', extensions: ['*'] },
    ],
});

if (filePath) {
    console.log('选中:', filePath);
    await loadFile(filePath);
}

// ===== 多选文件 =====
const filePaths = await open({
    multiple: true,
    filters: [{ name: '图片', extensions: ['png', 'jpg'] }],
});

if (filePaths && filePaths.length > 0) {
    console.log(`选中 ${filePaths.length} 个文件`);
}

// ===== 选择目录 =====
const dirPath = await open({
    directory: true,
    title: '选择目录',
});

// ===== 保存对话框 =====
const savePath = await save({
    title: '保存文件',
    defaultPath: 'untitled.txt',
    filters: [
        { name: '文本文件', extensions: ['txt'] },
        { name: 'Markdown', extensions: ['md'] },
    ],
});

if (savePath) {
    await saveFile(savePath, content);
}

// ===== 消息框 =====
await message('操作已完成', {
    title: '提示',
    kind: 'info',
});

await message('发生错误！', {
    title: '错误',
    kind: 'error',
});

// ===== 确认对话框 =====
const confirmed = await confirm('确定要删除吗？此操作不可撤销。', {
    title: '确认删除',
    kind: 'warning',
});

if (confirmed) {
    await deleteItem();
}

// ===== 是/否对话框 =====
const yes = await ask('是否保存更改？', {
    title: '未保存的更改',
    kind: 'warning',
});

if (yes) {
    await saveChanges();
}
```

**前端使用剪贴板**：

```typescript
import { writeText, readText } from '@tauri-apps/plugin-clipboard-manager';

// ===== 写入剪贴板 =====
await writeText('要复制的文本内容');
console.log('已复制到剪贴板');

// ===== 读取剪贴板 =====
const text = await readText();
console.log('剪贴板内容:', text);

// ===== 复制按钮 =====
async function copyToClipboard(text: string) {
    try {
        await writeText(text);
        showToast('已复制');
    } catch (e) {
        showToast('复制失败: ' + e);
    }
}

// ===== 粘贴按钮 =====
async function pasteFromClipboard() {
    try {
        const text = await readText();
        if (text) {
            insertTextAtCursor(text);
        }
    } catch (e) {
        console.error('粘贴失败:', e);
    }
}
```

**后端命令中使用对话框和剪贴板**：

```rust
use tauri_plugin_dialog::DialogExt;
use tauri_plugin_clipboard_manager::ClipboardExt;

// ===== 后端打开文件对话框 =====
#[tauri::command]
async fn open_file_dialog(app: tauri::AppHandle) -> Result<Option<String>, String> {
    let file_path = app.dialog()
        .file()
        .add_filter("文本文件", &["txt", "md"])
        .add_filter("所有文件", &["*"])
        .blocking_pick_file();

    match file_path {
        Some(path) => {
            let path_str = path.to_string();
            // 读取文件内容
            let content = std::fs::read_to_string(&path_str)
                .map_err(|e| e.to_string())?;
            println!("读取文件: {} ({} 字节)", path_str, content.len());
            Ok(Some(path_str))
        }
        None => Ok(None),
    }
}

// ===== 后端保存文件对话框 =====
#[tauri::command]
async fn save_file_dialog(
    app: tauri::AppHandle,
    content: String,
) -> Result<Option<String>, String> {
    let file_path = app.dialog()
        .file()
        .add_filter("文本文件", &["txt"])
        .set_file_name("untitled.txt")
        .blocking_save_file();

    if let Some(path) = file_path {
        let path_str = path.to_string();
        std::fs::write(&path_str, &content)
            .map_err(|e| e.to_string())?;
        Ok(Some(path_str))
    } else {
        Ok(None)
    }
}

// ===== 后端消息框 =====
#[tauri::command]
async fn show_error_dialog(
    app: tauri::AppHandle,
    title: String,
    message: String,
) -> Result<(), String> {
    app.dialog()
        .message(&message)
        .title(&title)
        .kind(tauri_plugin_dialog::MessageDialogKind::Error)
        .blocking_show();
    Ok(())
}

// ===== 后端剪贴板操作 =====
#[tauri::command]
fn copy_to_clipboard(
    app: tauri::AppHandle,
    text: String,
) -> Result<(), String> {
    app.clipboard()
        .write_text(&text)
        .map_err(|e| e.to_string())?;
    Ok(())
}

#[tauri::command]
fn read_clipboard(app: tauri::AppHandle) -> Result<String, String> {
    app.clipboard()
        .read_text()
        .map_err(|e| e.to_string())
}

fn main() {
    tauri::Builder::default()
        .plugin(tauri_plugin_dialog::init())
        .plugin(tauri_plugin_clipboard_manager::init())
        .invoke_handler(tauri::generate_handler![
            open_file_dialog,
            save_file_dialog,
            show_error_dialog,
            copy_to_clipboard,
            read_clipboard,
        ])
        .run(tauri::generate_context!())
        .unwrap();
}
```

对话框与剪贴板使用场景：

```
┌─────────────────────────────────────────────────────────────┐
│              对话框与剪贴板使用场景                          │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  文件对话框:                                                │
│  ┌──────────────────────────────────────────────────┐       │
│  │  场景 1: 打开文件                                │       │
│  │  用户点击"打开" → open() → 读取文件 → 显示内容   │       │
│  │                                                  │       │
│  │  场景 2: 保存文件                                │       │
│  │  用户点击"保存" → save() → 写入文件 → 提示成功   │       │
│  │                                                  │       │
│  │  场景 3: 选择目录                                │       │
│  │  用户选择输出目录 → open({directory: true})      │       │
│  └──────────────────────────────────────────────────┘       │
│                                                             │
│  消息对话框:                                                │
│  ┌──────────────────────────────────────────────────┐       │
│  │  场景 1: 信息提示                                │       │
│  │  message("操作完成", {kind: "info"})             │       │
│  │                                                  │       │
│  │  场景 2: 错误提示                                │       │
│  │  message("发生错误", {kind: "error"})            │       │
│  │                                                  │       │
│  │  场景 3: 确认操作                                │       │
│  │  if await confirm("确定删除?") { deleteItem() }  │       │
│  └──────────────────────────────────────────────────┘       │
│                                                             │
│  剪贴板:                                                    │
│  ┌──────────────────────────────────────────────────┐       │
│  │  场景 1: 复制文本                                │       │
│  │  await writeText(selectedText)                   │       │
│  │                                                  │       │
│  │  场景 2: 粘贴文本                                │       │
│  │  const text = await readText()                   │       │
│  │                                                  │       │
│  │  场景 3: 复制文件路径                            │       │
│  │  await writeText(filePath)                       │       │
│  └──────────────────────────────────────────────────┘       │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 总结

- `tauri-plugin-dialog` 提供文件对话框（open/save）和消息框（message/ask/confirm）
- `tauri-plugin-clipboard-manager` 提供剪贴板读写（writeText/readText）
- 文件对话框支持过滤器（按扩展名）、多选、目录选择
- 消息框有 info/warning/error 三种 kind
- **常见坑**：插件需在 `Cargo.toml` 和 `tauri.conf.json` 都添加；`blocking_pick_file` 会阻塞线程，异步用 `pick_file`；剪贴板读取空时返回空字符串而非 null；对话框标题和按钮文字由 OS 本地化

---

# 第 5 章：插件系统与生态

Tauri 2.x 的核心设计理念是"模块化"——将系统能力拆分为独立插件，按需引入。本章深入 Tauri 的插件生态，学习内置插件的使用、文件系统操作、数据库与存储，以及如何开发自定义插件。

## 第 18 讲：内置插件概览

### 概念

Tauri 2.x 将系统能力拆分为多个官方插件，每个插件封装一类能力：文件系统（fs）、对话框（dialog）、剪贴板（clipboard-manager）、通知（notification）、全局快捷键（global-shortcut）、自动更新（updater）、HTTP 请求（http）、Shell（shell）、进程（process）、日志（log）、位置（geolocation）等。这种设计让应用只引入需要的能力，保持体积小巧。

### 原理

Tauri 插件是独立的 Rust crate，通过 `tauri::Builder::plugin()` 注册。每个插件包含：① **后端代码**（Rust）——实现核心逻辑，通过命令或事件暴露 API；② **前端代码**（JS/TS）——`@tauri-apps/plugin-*` 包，提供类型安全的调用接口；③ **权限配置**——`capabilities/` 中声明插件权限，控制前端可访问的 API。

插件的安装分三步：① `cargo add tauri-plugin-xxx`（后端依赖）；② `pnpm add @tauri-apps/plugin-xxx`（前端依赖）；③ 在 `Cargo.toml` 注册插件 + 在 `capabilities/` 添加权限。权限系统是 Tauri 2.x 的安全核心——前端默认无法访问任何系统能力，必须在 `capabilities/` 显式授权。

### 例子

常用插件一览：

```
┌─────────────────────────────────────────────────────────────┐
│                Tauri 2.x 官方插件一览                       │
├──────────────────────┬──────────────────────────────────────┤
│ 插件名               │ 功能                                 │
├──────────────────────┼──────────────────────────────────────┤
│ fs                   │ 文件读写、目录操作                   │
│ dialog               │ 文件对话框、消息框                   │
│ clipboard-manager    │ 剪贴板读写                           │
│ notification         │ 系统通知                             │
│ global-shortcut      │ 全局快捷键                           │
│ updater              │ 自动更新                             │
│ http                 │ HTTP 请求（绕过 CORS）               │
│ shell                │ 执行外部命令、打开 URL               │
│ process              │ 进程控制、退出应用                   │
│ log                  │ 日志记录                             │
│ os                   │ 操作系统信息                         │
│ geolocation          │ 地理位置                             │
│ deep-link            │ 深度链接（自定义协议）               │
│ single-instance      │ 单实例运行                           │
│ store                │ 持久化键值存储                       │
│ sql                  │ SQLite/MySQL/PostgreSQL              │
│ websocket            │ WebSocket 客户端                     │
│ nfc                  │ NFC（移动端）                        │
│ barcode-scanner      │ 条码扫描（移动端）                   │
│ biometric            │ 生物识别（移动端）                   │
└──────────────────────┴──────────────────────────────────────┘
```

插件安装与使用流程：

```bash
# ===== 安装插件（以 notification 为例）=====

# 1. 后端依赖
cd src-tauri
cargo add tauri-plugin-notification

# 2. 前端依赖
cd ..
pnpm add @tauri-apps/plugin-notification
```

后端注册插件：

```rust
// src-tauri/src/main.rs
use tauri::Manager;

fn main() {
    tauri::Builder::default()
        .plugin(tauri_plugin_notification::init())  // 注册通知插件
        .plugin(tauri_plugin_clipboard_manager::init())  // 剪贴板
        .plugin(tauri_plugin_os::init())  // 系统信息
        .plugin(tauri_plugin_process::init())  // 进程控制
        .plugin(tauri_plugin_log::Builder::new().build())  // 日志
        .run(tauri::generate_context!())
        .unwrap();
}
```

权限配置 `src-tauri/capabilities/default.json`：

```json
{
  "$schema": "../gen/schemas/desktop-schema.json",
  "identifier": "default",
  "description": "默认权限",
  "windows": ["main"],
  "permissions": [
    "core:default",
    "notification:default",
    "notification:allow-notify",
    "notification:allow-is-permission-granted",
    "notification:allow-request-permission",
    "clipboard-manager:allow-read-text",
    "clipboard-manager:allow-write-text",
    "os:default",
    "process:default",
    "process:allow-exit",
    "process:allow-restart",
    "log:default",
    "log:allow-info",
    "log:allow-error",
    "log:allow-warn"
  ]
}
```

前端使用插件：

```typescript
// ===== 通知插件 =====
import {
    isPermissionGranted,
    requestPermission,
    sendNotification
} from '@tauri-apps/plugin-notification';

// 检查权限
let permissionGranted = await isPermissionGranted();
if (!permissionGranted) {
    const permission = await requestPermission();
    permissionGranted = permission === 'granted';
}

// 发送通知
if (permissionGranted) {
    await sendNotification({
        title: 'Tauri 通知',
        body: '这是一条来自 Tauri 的通知！',
        icon: 'icon.png'
    });
}

// ===== OS 插件 =====
import { platform, arch, version, hostname, locale } from '@tauri-apps/plugin-os';

console.log('平台:', platform());  // 'macos' | 'windows' | 'linux'
console.log('架构:', arch());      // 'x86_64' | 'aarch64'
console.log('系统版本:', version());
console.log('主机名:', hostname());
console.log('语言:', locale());

// ===== 进程插件 =====
import { exit, relaunch } from '@tauri-apps/plugin-process';

// 退出应用
await exit(0);

// 重启应用
await relaunch();

// ===== 日志插件 =====
import { info, error, warn, debug, trace } from '@tauri-apps/plugin-log';

await info('这是一条信息日志');
await error('这是一条错误日志');
await warn('这是一条警告日志');
await debug('这是一条调试日志');
await trace('这是一条跟踪日志');

// ===== Shell 插件 =====
import { open, Command } from '@tauri-apps/plugin-shell';

// 用默认浏览器打开 URL
await open('https://tauri.app');

// 用默认应用打开文件
await open('/path/to/file.pdf');

// 执行外部命令
const command = Command.create('echo', ['hello']);
const output = await command.execute();
console.log(output.stdout);  // 'hello'

// ===== HTTP 插件 =====
import { fetch } from '@tauri-apps/plugin-http';

// 绕过 CORS 的 HTTP 请求
const response = await fetch('https://api.example.com/data', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ key: 'value' })
});
const data = await response.json();
```

Shell 插件的命令配置 `src-tauri/permissions/shell.json`：

```json
{
  "$schema": "../gen/schemas/desktop-schema.json",
  "identifier": "shell-permissions",
  "description": "Shell 命令权限",
  "windows": ["main"],
  "permissions": [
    "shell:allow-open",
    {
      "identifier": "shell:allow-execute",
      "allow": [
        {
          "name": "echo",
          "command": "echo",
          "args": true
        },
        {
          "name": "run-script",
          "command": "npm",
          "args": ["run", "build"]
        }
      ]
    }
  ]
}
```

插件架构：

```
┌─────────────────────────────────────────────────────────────┐
│                   Tauri 插件架构                            │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  前端 (@tauri-apps/plugin-xxx)                      │   │
│  │                                                     │   │
│  │  import { sendNotification } from                   │   │
│  │    '@tauri-apps/plugin-notification';               │   │
│  │                                                     │   │
│  │  await sendNotification({ title: 'Hello' });        │   │
│  │         │                                           │   │
│  │         │ invoke('plugin:notification|notify', ...) │   │
│  │         ▼                                           │   │
│  └─────────────────────────────────────────────────────┘   │
│                      │ IPC                                 │
│                      ▼                                     │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  后端 (tauri-plugin-xxx)                            │   │
│  │                                                     │   │
│  │  tauri_plugin_notification::init()                  │   │
│  │    .register_command("notify", |args| {             │   │
│  │        // 调用系统通知 API                           │   │
│  │        notify_os(args.title, args.body);            │   │
│  │    })                                               │   │
│  │         │                                           │   │
│  │         │ 调用系统能力                              │   │
│  │         ▼                                           │   │
│  └─────────────────────────────────────────────────────┘   │
│                      │                                     │
│                      ▼                                     │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  权限系统 (capabilities/)                           │   │
│  │                                                     │   │
│  │  {                                                  │   │
│  │    "permissions": [                                 │   │
│  │      "notification:allow-notify",                   │   │
│  │      "notification:allow-request-permission"        │   │
│  │    ]                                                │   │
│  │  }                                                  │   │
│  │                                                     │   │
│  │  前端只能调用已授权的 API                           │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 总结

- Tauri 2.x 将系统能力拆分为独立插件，按需引入
- 插件安装三步：`cargo add` + `pnpm add` + 权限配置
- 权限系统是安全核心——前端默认无权限，需在 `capabilities/` 显式授权
- 常用插件：fs、dialog、clipboard、notification、http、shell、os、process、log
- **常见坑**：忘记配置权限导致前端调用失败；插件版本需与 Tauri 核心版本兼容；Shell 插件的命令执行需在权限中声明允许的命令；移动端插件（nfc、biometric）在桌面端不可用

---

## 第 19 讲：文件系统插件

### 概念

**文件系统插件**（`tauri-plugin-fs`）让前端安全地读写本地文件。与 Node.js 的 `fs` 模块不同，Tauri 的 fs 插件基于**作用域（scope）**——只有配置允许的路径才能访问，防止恶意前端代码读取敏感文件。支持文本/二进制文件读写、目录操作、文件监听等。

### 原理

Tauri fs 插件的安全模型基于"路径作用域"。在 `capabilities/` 中配置允许访问的路径模式（如 `$APPDATA/*`、`$DOCUMENT/*`），前端只能在这些路径内操作。路径变量（`$APPDATA`、`$DOCUMENT`、`$HOME` 等）由 Tauri 自动解析为平台对应路径。

fs 插件提供两套 API：① **前端 API**（`@tauri-apps/plugin-fs`）——`readTextFile`、`writeTextFile`、`readDir`、`exists`、`remove`、`mkdir` 等，适合前端直接操作文件；② **后端 API**（`tauri::path::PathResolver` + `std::fs`）——在 Rust 命令中用标准库操作文件，通过命令暴露给前端。前端 API 更便捷但受作用域限制，后端 API 更灵活但需自己处理安全。

文件监听通过 `tauri-plugin-fs` 的 `watch` API 实现——监听指定路径的文件变化（创建、修改、删除），通过事件通知前端。适合"文件被外部修改时自动刷新"的场景。

### 例子

**安装 fs 插件**：

```bash
# 后端
cargo add tauri-plugin-fs

# 前端
pnpm add @tauri-apps/plugin-fs
```

**权限配置** `src-tauri/capabilities/default.json`：

```json
{
  "$schema": "../gen/schemas/desktop-schema.json",
  "identifier": "default",
  "description": "默认权限",
  "windows": ["main"],
  "permissions": [
    "core:default",
    "fs:default",
    {
      "identifier": "fs:allow-read-text-file",
      "allow": [
        { "path": "$APPDATA/documents/*" },
        { "path": "$DOCUMENT/*" }
      ]
    },
    {
      "identifier": "fs:allow-write-text-file",
      "allow": [
        { "path": "$APPDATA/documents/*" },
        { "path": "$DOCUMENT/*" }
      ]
    },
    "fs:allow-read-dir",
    "fs:allow-exists",
    "fs:allow-mkdir",
    "fs:allow-remove",
    "fs:allow-rename",
    "fs:allow-copy-file"
  ]
}
```

**后端注册**：

```rust
use tauri::Manager;

fn main() {
    tauri::Builder::default()
        .plugin(tauri_plugin_fs::init())
        .run(tauri::generate_context!())
        .unwrap();
}
```

**前端文件操作**：

```typescript
import {
    readTextFile,
    writeTextFile,
    readDir,
    exists,
    mkdir,
    remove,
    rename,
    copyFile,
    BaseDirectory
} from '@tauri-apps/plugin-fs';

// ===== 读取文本文件 =====
const content = await readTextFile('notes.txt', { baseDir: BaseDirectory.AppData });
console.log(content);

// ===== 写入文本文件 =====
await writeTextFile(
    'notes.txt',
    '这是笔记内容',
    { baseDir: BaseDirectory.AppData }
);

// ===== 追加内容 =====
const existing = await readTextFile('log.txt', { baseDir: BaseDirectory.AppData });
await writeTextFile(
    'log.txt',
    existing + '\n新的一行',
    { baseDir: BaseDirectory.AppData }
);

// ===== 读取目录 =====
const entries = await readDir('documents', { baseDir: BaseDirectory.AppData });
for (const entry of entries) {
    console.log(entry.name, entry.isDirectory ? '目录' : '文件');
}

// ===== 检查文件是否存在 =====
const fileExists = await exists('notes.txt', { baseDir: BaseDirectory.AppData });
if (fileExists) {
    console.log('文件存在');
}

// ===== 创建目录 =====
await mkdir('backups', { baseDir: BaseDirectory.AppData, recursive: true });

// ===== 删除文件/目录 =====
await remove('old-file.txt', { baseDir: BaseDirectory.AppData });
await remove('old-dir', { baseDir: BaseDirectory.AppData, recursive: true });

// ===== 重命名 =====
await rename(
    'old-name.txt',
    'new-name.txt',
    { baseDir: BaseDirectory.AppData }
);

// ===== 复制文件 =====
await copyFile(
    'original.txt',
    'copy.txt',
    { baseDir: BaseDirectory.AppData }
);

// ===== 读写 JSON 配置 =====
async function loadConfig<T>(filename: string): Promise<T> {
    try {
        const content = await readTextFile(filename, { baseDir: BaseDirectory.AppData });
        return JSON.parse(content);
    } catch {
        return {} as T;
    }
}

async function saveConfig<T>(filename: string, config: T): Promise<void> {
    await writeTextFile(
        filename,
        JSON.stringify(config, null, 2),
        { baseDir: BaseDirectory.AppData }
    );
}

// 使用
interface AppSettings {
    theme: string;
    language: string;
    fontSize: number;
}

const settings = await loadConfig<AppSettings>('settings.json');
settings.theme = 'dark';
await saveConfig('settings.json', settings);
```

**后端文件操作命令**：

```rust
use tauri::{AppHandle, Manager};
use std::path::PathBuf;
use std::fs;
use serde::{Serialize, Deserialize};

// ===== 获取应用数据目录 =====
#[tauri::command]
fn get_app_data_dir(app: AppHandle) -> Result<String, String> {
    let path = app.path().app_data_dir()
        .map_err(|e| e.to_string())?;
    Ok(path.to_string_lossy().to_string())
}

// ===== 读取文件（后端）=====
#[tauri::command]
fn read_file_content(path: String) -> Result<String, String> {
    fs::read_to_string(&path)
        .map_err(|e| format!("读取文件失败: {}", e))
}

// ===== 写入文件（后端）=====
#[tauri::command]
fn write_file_content(path: String, content: String) -> Result<(), String> {
    // 确保父目录存在
    if let Some(parent) = std::path::Path::new(&path).parent() {
        fs::create_dir_all(parent)
            .map_err(|e| format!("创建目录失败: {}", e))?;
    }
    fs::write(&path, content)
        .map_err(|e| format!("写入文件失败: {}", e))
}

// ===== 列出目录内容 =====
#[derive(Serialize)]
struct FileEntry {
    name: String,
    path: String,
    is_dir: bool,
    size: u64,
    modified: u64,
}

#[tauri::command]
fn list_directory(path: String) -> Result<Vec<FileEntry>, String> {
    let mut entries = Vec::new();

    for entry in fs::read_dir(&path).map_err(|e| e.to_string())? {
        let entry = entry.map_err(|e| e.to_string())?;
        let metadata = entry.metadata().map_err(|e| e.to_string())?;

        entries.push(FileEntry {
            name: entry.file_name().to_string_lossy().to_string(),
            path: entry.path().to_string_lossy().to_string(),
            is_dir: metadata.is_dir(),
            size: metadata.len(),
            modified: metadata
                .modified()
                .ok()
                .and_then(|t| t.duration_since(std::time::UNIX_EPOCH).ok())
                .map(|d| d.as_secs())
                .unwrap_or(0),
        });
    }

    Ok(entries)
}

// ===== 文件监听 =====
use tauri_plugin_fs::FsExt;

#[tauri::command]
async fn watch_directory(
    app: AppHandle,
    path: String,
) -> Result<(), String> {
    // 使用 notify crate 监听文件变化
    let (tx, rx) = std::sync::mpsc::channel();

    let mut watcher = notify::recommended_watcher(move |res: Result<notify::Event, _>| {
        if let Ok(event) = res {
            let _ = tx.send(event);
        }
    }).map_err(|e| e.to_string())?;

    watcher.watch(
        std::path::Path::new(&path),
        notify::RecursiveMode::Recursive
    ).map_err(|e| e.to_string())?;

    // 在后台处理事件
    let app_clone = app.clone();
    tokio::spawn(async move {
        let mut watcher = watcher;
        loop {
            if let Ok(event) = rx.recv() {
                // 发送事件到前端
                let _ = app_clone.emit("fs-change", FileChangeEvent {
                    kind: format!("{:?}", event.kind),
                    paths: event.paths.iter()
                        .map(|p| p.to_string_lossy().to_string())
                        .collect(),
                });
            }
        }
        // 保持 watcher 存活
        drop(watcher);
    });

    Ok(())
}

#[derive(Serialize)]
struct FileChangeEvent {
    kind: String,
    paths: Vec<String>,
}

fn main() {
    tauri::Builder::default()
        .plugin(tauri_plugin_fs::init())
        .invoke_handler(tauri::generate_handler![
            get_app_data_dir,
            read_file_content,
            write_file_content,
            list_directory,
            watch_directory,
        ])
        .run(tauri::generate_context!())
        .unwrap();
}
```

**前端监听文件变化**：

```typescript
import { listen } from '@tauri-apps/api/event';
import { invoke } from '@tauri-apps/api/core';

interface FileChangeEvent {
    kind: string;
    paths: string[];
}

// 监听文件变化
const unlisten = await listen<FileChangeEvent>('fs-change', (event) => {
    console.log(`文件变化: ${event.kind}`);
    for (const path of event.payload.paths) {
        console.log(`  ${path}`);
    }
    // 自动刷新文件列表
    refreshFileList();
});

// 开始监听目录
await invoke('watch_directory', { path: '/path/to/watch' });

// 组件卸载时取消监听
// unlisten();
```

路径变量说明：

```
┌─────────────────────────────────────────────────────────────┐
│                Tauri 路径变量                               │
├──────────────────┬──────────────────────────────────────────┤
│ 变量             │ 对应路径                                 │
├──────────────────┼──────────────────────────────────────────┤
│ $APPDATA         │ 应用数据目录                             │
│                  │ macOS: ~/Library/Application Support    │
│                  │ Windows: %APPDATA%                      │
│                  │ Linux: ~/.local/share                   │
├──────────────────┼──────────────────────────────────────────┤
│ $APPCONFIG       │ 应用配置目录                             │
│                  │ macOS: ~/Library/Preferences            │
│                  │ Windows: %APPDATA%                      │
│                  │ Linux: ~/.config                        │
├──────────────────┼──────────────────────────────────────────┤
│ $DOCUMENT        │ 用户文档目录                             │
│                  │ macOS: ~/Documents                      │
│                  │ Windows: %USERPROFILE%\Documents        │
│                  │ Linux: ~/Documents                      │
├──────────────────┼──────────────────────────────────────────┤
│ $DOWNLOAD        │ 下载目录                                 │
├──────────────────┼──────────────────────────────────────────┤
│ $HOME            │ 用户主目录                               │
├──────────────────┼──────────────────────────────────────────┤
│ $DESKTOP         │ 桌面目录                                 │
├──────────────────┼──────────────────────────────────────────┤
│ $TEMP            │ 临时目录                                 │
├──────────────────┼──────────────────────────────────────────┤
│ $RESOURCE        │ 应用资源目录（打包后）                   │
└──────────────────┴──────────────────────────────────────────┘
```

### 总结

- `tauri-plugin-fs` 提供前端安全的文件操作，基于路径作用域限制访问范围
- 前端 API（`readTextFile`/`writeTextFile` 等）受权限作用域限制
- 后端 API（`std::fs`）更灵活，通过命令暴露给前端，需自己处理安全
- 路径变量（`$APPDATA`、`$DOCUMENT`）跨平台一致
- **常见坑**：权限配置的路径模式需正确（`*` 匹配单层，`**` 匹配多层）；大文件读写用后端命令避免 IPC 序列化开销；文件监听需保持 watcher 存活；`BaseDirectory` 枚举指定基础目录

---

## 第 20 讲：数据库与存储插件

### 概念

Tauri 提供两种数据持久化方案：① **SQL 插件**（`tauri-plugin-sql`）——支持 SQLite、MySQL、PostgreSQL，适合结构化数据；② **Store 插件**（`tauri-plugin-store`）——轻量级键值存储，适合配置、偏好等简单数据。选择依据：数据复杂度和查询需求——关系型数据用 SQL，简单键值用 Store。

### 原理

**SQL 插件**封装了 SQLx（Rust 异步数据库库），提供连接池管理。前端通过 `@tauri-apps/plugin-sql` 的 `Database.load('sqlite:app.db')` 加载数据库，返回 `Database` 实例，调用 `execute`/`select` 执行 SQL。SQL 语句在前端构造，通过 IPC 传到后端执行。插件支持迁移（migration）——在 `Cargo.toml` 中定义迁移 SQL，应用启动时自动执行。

**Store 插件**基于 JSON 文件实现键值存储。每个 Store 对应一个 JSON 文件（如 `settings.json`），数据自动持久化到 `$APPDATA`。前端通过 `Store.load('settings.json')` 加载，`store.set('key', value)`/`store.get('key')` 读写。Store 适合简单配置——主题、语言、窗口位置等。数据自动序列化为 JSON，支持任意可序列化类型。

两种插件的权限控制：SQL 插件需配置 `allow-execute`/`allow-select` 权限并指定允许的数据库文件；Store 插件需配置 `allow-set`/`allow-get`/`allow-delete` 权限并指定允许的 Store 文件。

### 例子

**SQL 插件使用**：

```bash
# 安装
cargo add tauri-plugin-sql
pnpm add @tauri-apps/plugin-sql
```

后端注册（含迁移）：

```rust
use tauri_plugin_sql::{Migration, MigrationKind};

fn main() {
    let migrations = vec![
        Migration {
            version: 1,
            description: "create users table",
            sql: "CREATE TABLE IF NOT EXISTS users (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                name TEXT NOT NULL,
                email TEXT UNIQUE NOT NULL,
                created_at DATETIME DEFAULT CURRENT_TIMESTAMP
            )",
            kind: MigrationKind::Up,
        },
        Migration {
            version: 2,
            description: "add notes table",
            sql: "CREATE TABLE IF NOT EXISTS notes (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                user_id INTEGER NOT NULL,
                title TEXT NOT NULL,
                content TEXT,
                created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
                FOREIGN KEY (user_id) REFERENCES users(id)
            )",
            kind: MigrationKind::Up,
        },
    ];

    tauri::Builder::default()
        .plugin(
            tauri_plugin_sql::Builder::default()
                .add_migrations("sqlite:app.db", migrations)
                .build()
        )
        .run(tauri::generate_context!())
        .unwrap();
}
```

权限配置：

```json
{
  "permissions": [
    "sql:default",
    "sql:allow-execute",
    "sql:allow-select",
    {
      "identifier": "sql:allow-load",
      "allow": [
        { "db": "sqlite:app.db" }
      ]
    }
  ]
}
```

前端使用：

```typescript
import Database from '@tauri-apps/plugin-sql';

// ===== 加载数据库 =====
const db = await Database.load('sqlite:app.db');

// ===== 插入数据 =====
const result = await db.execute(
    'INSERT INTO users (name, email) VALUES ($1, $2)',
    ['张三', '[email protected]']
);
console.log(`插入 ID: ${result.lastInsertId}`);

// ===== 查询数据 =====
const users = await db.select<User[]>(
    'SELECT * FROM users WHERE name = $1',
    ['张三']
);
console.log(users);

// ===== 更新数据 =====
await db.execute(
    'UPDATE users SET email = $1 WHERE id = $2',
    ['[email protected]', 1]
);

// ===== 删除数据 =====
await db.execute('DELETE FROM users WHERE id = $1', [1]);

// ===== 事务 =====
await db.execute('BEGIN');
try {
    await db.execute('INSERT INTO users (name, email) VALUES ($1, $2)', ['李四', '[email protected]']);
    await db.execute('INSERT INTO notes (user_id, title) VALUES ($1, $2)', [2, '第一条笔记']);
    await db.execute('COMMIT');
} catch (e) {
    await db.execute('ROLLBACK');
    console.error('事务失败:', e);
}

// ===== 关联查询 =====
const userNotes = await db.select(`
    SELECT u.name, n.title, n.content
    FROM users u
    JOIN notes n ON u.id = n.user_id
    WHERE u.id = $1
`, [2]);

// ===== 批量插入 =====
const usersToInsert = [
    ['王五', '[email protected]'],
    ['赵六', '[email protected]'],
    ['钱七', '[email protected]']
];

for (const [name, email] of usersToInsert) {
    await db.execute(
        'INSERT INTO users (name, email) VALUES ($1, $2)',
        [name, email]
    );
}

interface User {
    id: number;
    name: string;
    email: string;
    created_at: string;
}
```

**Store 插件使用**：

```bash
# 安装
cargo add tauri-plugin-store
pnpm add @tauri-apps/plugin-store
```

后端注册：

```rust
fn main() {
    tauri::Builder::default()
        .plugin(tauri_plugin_store::Builder::default().build())
        .run(tauri::generate_context!())
        .unwrap();
}
```

权限配置：

```json
{
  "permissions": [
    "store:default",
    "store:allow-set",
    "store:allow-get",
    "store:allow-delete",
    "store:allow-keys",
    "store:allow-values",
    "store:allow-entries",
    "store:allow-clear",
    "store:allow-reset",
    "store:allow-reload",
    "store:allow-save"
  ]
}
```

前端使用：

```typescript
import { Store } from '@tauri-apps/plugin-store';

// ===== 加载 Store =====
const store = await Store.load('settings.json');

// ===== 写入数据 =====
await store.set('theme', 'dark');
await store.set('fontSize', 14);
await store.set('windowPosition', { x: 100, y: 100 });
await store.set('recentFiles', ['/path/to/file1.txt', '/path/to/file2.txt']);

// 手动保存（默认自动保存）
await store.save();

// ===== 读取数据 =====
const theme = await store.get<string>('theme');
const fontSize = await store.get<number>('fontSize');
const position = await store.get<{ x: number; y: number }>('windowPosition');
const recentFiles = await store.get<string[]>('recentFiles');

console.log(theme, fontSize, position, recentFiles);

// ===== 读取带默认值 =====
const language = await store.get<string>('language') ?? 'zh-CN';

// ===== 删除数据 =====
await store.delete('tempData');

// ===== 获取所有键 =====
const keys = await store.keys<string>();
console.log('所有键:', keys);

// ===== 获取所有值 =====
const values = await store.values();
console.log('所有值:', values);

// ===== 获取所有键值对 =====
const entries = await store.entries<[string, unknown]>();
console.log('所有键值对:', entries);

// ===== 清空 Store =====
await store.clear();

// ===== 重置到磁盘上的状态 =====
await store.reset();

// ===== 重新从磁盘加载 =====
await store.reload();

// ===== 监听变化 =====
import { listen } from '@tauri-apps/api/event';

await listen<{ key: string; value: unknown }>('store://change', (event) => {
    console.log(`Store 变化: ${event.payload.key} =`, event.payload.value);
});

// ===== 封装配置管理 =====
class ConfigManager {
    private store: Store;

    constructor() {
        this.store = await Store.load('config.json');
    }

    async get<T>(key: string, defaultValue: T): Promise<T> {
        return (await this.store.get<T>(key)) ?? defaultValue;
    }

    async set<T>(key: string, value: T): Promise<void> {
        await this.store.set(key, value);
        await this.store.save();
    }

    async update<T>(key: string, updater: (current: T) => T, defaultValue: T): Promise<void> {
        const current = await this.get(key, defaultValue);
        await this.set(key, updater(current));
    }
}

// 使用
const config = new ConfigManager();
await config.set('theme', 'dark');
const theme = await config.get('theme', 'light');
```

数据持久化方案对比：

```
┌─────────────────────────────────────────────────────────────┐
│              数据持久化方案对比                             │
├──────────────────┬──────────────────────────────────────────┤
│ 方案             │ 适用场景                                 │
├──────────────────┼──────────────────────────────────────────┤
│ Store 插件       │ ✅ 简单配置（主题、语言、窗口位置）      │
│ (键值存储)       │ ✅ 少量数据                              │
│                  │ ✅ 无需查询                              │
│                  │ ❌ 不适合关系型数据                      │
│                  │ ❌ 不适合大量数据                        │
├──────────────────┼──────────────────────────────────────────┤
│ SQL 插件         │ ✅ 关系型数据（用户、订单、笔记）        │
│ (SQLite)         │ ✅ 需要查询、过滤、排序                  │
│                  │ ✅ 大量数据                              │
│                  │ ✅ 事务支持                              │
│                  │ ❌ 配置较复杂                            │
├──────────────────┼──────────────────────────────────────────┤
│ 自定义文件       │ ✅ 特殊格式（Markdown、JSON、CSV）       │
│ (fs 插件)        │ ✅ 用户可见的文档                        │
│                  │ ✅ 跨应用共享                            │
│                  │ ❌ 需自己实现解析                        │
└──────────────────┴──────────────────────────────────────────┘
```

### 总结

- SQL 插件支持 SQLite/MySQL/PostgreSQL，适合关系型数据，支持迁移
- Store 插件是轻量级键值存储，适合配置和偏好
- SQL 通过 `Database.load` + `execute`/`select` 操作，支持参数化查询
- Store 通过 `Store.load` + `set`/`get`/`delete` 操作，自动持久化
- **常见坑**：SQL 语句用 `$1`/`$2` 参数化（防注入）；Store 的 `set` 默认不立即保存，需 `save()`；SQL 迁移版本号必须递增；Store 文件路径基于 `$APPDATA`

---

## 第 21 讲：开发自定义插件

### 概念

当官方插件无法满足需求时，可开发**自定义插件**。Tauri 插件本质是一个 Rust crate，实现 `Plugin` trait，提供命令、事件、权限定义。自定义插件可封装业务逻辑、第三方 Rust 库、平台特定能力，并在多个项目间复用。理解插件开发是掌握 Tauri 生态的关键。

### 原理

Tauri 插件是一个实现 `tauri::plugin::Plugin` trait 的 Rust crate。`Plugin` trait 的核心方法：① `name()`——返回插件名（用于命令命名空间）；② `initialize()`——初始化插件，注册命令、状态、事件处理器；③ `extend_api()`——扩展前端 API（可选）。

插件的结构：① `Cargo.toml`——声明 `tauri` 依赖和插件元信息；② `src/lib.rs`——插件入口，定义 `Plugin` trait 实现；③ `src/commands.rs`——命令定义；④ `permissions/`——权限定义（JSON 文件）；⑤ `guest-js/`——前端 JS/TS 绑定（可选）。

插件的命令命名规则：`plugin-name:command-name`。例如插件名为 `my-plugin`，命令 `greet` 的完整调用名是 `my-plugin:greet`。前端通过 `invoke('plugin-name|greet', args)` 调用（注意分隔符是 `|`）。

### 例子

**创建插件项目**：

```bash
# 使用 tauri-plugin-cli 脚手架
cargo install create-tauri-plugin
cargo create-tauri-plugin my-plugin
```

**插件结构**：

```
tauri-plugin-my-plugin/
├── Cargo.toml
├── package.json
├── src/
│   ├── lib.rs          # 插件入口
│   ├── commands.rs     # 命令定义
│   ├── error.rs        # 错误类型
│   └── mobile.rs       # 移动端支持（可选）
├── permissions/
│   ├── default.toml    # 默认权限
│   ├── allow-greet.toml
│   └── allow-calculate.toml
└── guest-js/
    ├── index.ts        # 前端绑定
    └── package.json
```

**`Cargo.toml`**：

```toml
[package]
name = "tauri-plugin-my-plugin"
version = "0.1.0"
edition = "2021"

[dependencies]
tauri = { version = "2", features = [] }
serde = "1.0"
thiserror = "1.0"
log = "0.4"

[build-dependencies]
tauri-plugin = { version = "2", features = ["build"] }
```

**`src/lib.rs`**：

```rust
use tauri::{plugin::{Plugin, Builder, TauriPlugin}, Manager, Runtime};

mod commands;
mod error;

pub use error::{Error, Result};

pub struct MyPlugin {
    config: PluginConfig,
}

#[derive(Default, serde::Deserialize)]
struct PluginConfig {
    api_key: Option<String>,
    timeout: Option<u64>,
}

impl MyPlugin {
    pub fn new() -> Self {
        Self { config: PluginConfig::default() }
    }

    pub fn with_config(config: PluginConfig) -> Self {
        Self { config }
    }
}

impl<R: Runtime> Plugin<R> for MyPlugin {
    fn name(&self) -> &'static str {
        "my-plugin"
    }

    fn initialize(&mut self, app: &tauri::AppHandle<R>, config: serde_json::Value) -> tauri::plugin::Result<()> {
        // 解析配置
        if let Some(cfg) = config.as_object() {
            self.config.api_key = cfg.get("apiKey").and_then(|v| v.as_str()).map(|s| s.to_string());
            self.config.timeout = cfg.get("timeout").and_then(|v| v.as_u64());
        }

        // 注册状态
        app.manage(self.config.clone());

        Ok(())
    }

    fn extend_api(&mut self, message: tauri::ipc::Invoke<R>) -> bool {
        // 自定义 IPC 处理（可选，通常用命令系统即可）
        false
    }
}

// 便捷函数：创建插件实例
pub fn init<R: Runtime>() -> TauriPlugin<R> {
    Builder::new().build()
}

// 带配置的初始化
pub fn init_with_config<R: Runtime>(config: PluginConfig) -> TauriPlugin<R> {
    Builder::new().setup(move |app| {
        app.manage(config.clone());
        Ok(())
    }).build()
}
```

**`src/commands.rs`**：

```rust
use tauri::{State, AppHandle};
use serde::{Serialize, Deserialize};
use crate::error::Result;

#[derive(Serialize, Deserialize)]
pub struct GreetingResult {
    message: String,
    timestamp: u64,
}

#[derive(Default, Clone)]
pub struct PluginConfig {
    pub api_key: Option<String>,
    pub timeout: Option<u64>,
}

#[tauri::command]
pub async fn greet(name: String) -> Result<GreetingResult> {
    Ok(GreetingResult {
        message: format!("你好, {}!", name),
        timestamp: std::time::SystemTime::now()
            .duration_since(std::time::UNIX_EPOCH)
            .unwrap()
            .as_secs(),
    })
}

#[tauri::command]
pub async fn calculate(
    a: f64,
    b: f64,
    operation: String,
) -> Result<f64> {
    let result = match operation.as_str() {
        "add" => a + b,
        "subtract" => a - b,
        "multiply" => a * b,
        "divide" => {
            if b == 0.0 {
                return Err(crate::error::Error::InvalidInput("除数不能为零".to_string()));
            }
            a / b
        }
        _ => return Err(crate::error::Error::InvalidInput(format!("未知操作: {}", operation))),
    };
    Ok(result)
}

#[tauri::command]
pub async fn call_external_api(
    endpoint: String,
    config: State<'_, PluginConfig>,
) -> Result<String> {
    let api_key = config.api_key.as_ref()
        .ok_or_else(|| crate::error::Error::ConfigError("API key 未配置".to_string()))?;

    let timeout = config.timeout.unwrap_or(30);
    println!("调用 API: {} (key: {}, timeout: {}s)", endpoint, api_key, timeout);

    // 模拟 API 调用
    tokio::time::sleep(std::time::Duration::from_secs(1)).await;
    Ok(format!("响应 from {}", endpoint))
}
```

**`src/error.rs`**：

```rust
use thiserror::Error;
use serde::Serialize;

#[derive(Debug, Error, Serialize)]
pub enum Error {
    #[error("无效输入: {0}")]
    InvalidInput(String),

    #[error("配置错误: {0}")]
    ConfigError(String),

    #[error("网络错误: {0}")]
    NetworkError(String),

    #[error("内部错误: {0}")]
    Internal(String),
}

impl From<std::io::Error> for Error {
    fn from(e: std::io::Error) -> Self {
        Error::Internal(e.to_string())
    }
}

pub type Result<T> = std::result::Result<T, Error>;
```

**`permissions/default.toml`**：

```toml
[default]
description = "My Plugin 默认权限"
permissions = ["allow-greet"]
```

**`permissions/allow-greet.toml`**：

```toml
[allow-greet]
description = "允许调用 greet 命令"
permissions = []
```

**`permissions/allow-calculate.toml`**：

```toml
[allow-calculate]
description = "允许调用 calculate 命令"
permissions = []
```

**`guest-js/index.ts`**（前端绑定）：

```typescript
import { invoke } from '@tauri-apps/api/core';

export interface GreetingResult {
    message: string;
    timestamp: number;
}

export async function greet(name: string): Promise<GreetingResult> {
    return await invoke<GreetingResult>('my-plugin:greet', { name });
}

export async function calculate(a: number, b: number, operation: string): Promise<number> {
    return await invoke<number>('my-plugin:calculate', { a, b, operation });
}

export async function callExternalApi(endpoint: string): Promise<string> {
    return await invoke<string>('my-plugin:call_external_api', { endpoint });
}
```

**使用自定义插件**：

```rust
// 在应用中注册插件
use tauri_plugin_my_plugin;

fn main() {
    tauri::Builder::default()
        .plugin(tauri_plugin_my_plugin::init())
        // 或带配置
        // .plugin(tauri_plugin_my_plugin::init_with_config(
        //     tauri_plugin_my_plugin::PluginConfig {
        //         api_key: Some("my-api-key".to_string()),
        //         timeout: Some(60),
        //     }
        // ))
        .run(tauri::generate_context!())
        .unwrap();
}
```

```json
// 应用的 capabilities/default.json
{
  "permissions": [
    "core:default",
    "my-plugin:default",
    "my-plugin:allow-greet",
    "my-plugin:allow-calculate"
  ]
}
```

```typescript
// 前端使用
import { greet, calculate, callExternalApi } from 'tauri-plugin-my-plugin-api';

const result = await greet('Alice');
console.log(result.message);

const sum = await calculate(3, 5, 'add');
console.log(sum);  // 8

const response = await callExternalApi('https://api.example.com/data');
console.log(response);
```

插件开发流程：

```
┌─────────────────────────────────────────────────────────────┐
│                   自定义插件开发流程                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. 创建插件项目                                            │
│     cargo create-tauri-plugin my-plugin                    │
│                    │                                        │
│                    ▼                                        │
│  2. 定义命令 (src/commands.rs)                              │
│     #[tauri::command]                                      │
│     pub async fn greet(name: String) -> Result<String>     │
│                    │                                        │
│                    ▼                                        │
│  3. 实现 Plugin trait (src/lib.rs)                          │
│     impl<R: Runtime> Plugin<R> for MyPlugin {              │
│         fn name(&self) -> &'static str { "my-plugin" }    │
│         fn initialize(&mut self, app, config) { ... }      │
│     }                                                       │
│                    │                                        │
│                    ▼                                        │
│  4. 定义权限 (permissions/)                                 │
│     [allow-greet]                                          │
│     description = "允许 greet 命令"                         │
│                    │                                        │
│                    ▼                                        │
│  5. 编写前端绑定 (guest-js/)                                │
│     export async function greet(name) {                    │
│         return invoke('my-plugin:greet', { name });        │
│     }                                                       │
│                    │                                        │
│                    ▼                                        │
│  6. 在应用中使用                                            │
│     - cargo add tauri-plugin-my-plugin                     │
│     - pnpm add tauri-plugin-my-plugin-api                  │
│     - .plugin(tauri_plugin_my_plugin::init())              │
│     - 配置权限                                              │
│                    │                                        │
│                    ▼                                        │
│  7. 发布 (可选)                                             │
│     - cargo publish                                         │
│     - npm publish                                           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 总结

- 自定义插件是实现 `Plugin` trait 的 Rust crate，提供命令、状态、权限
- 插件命令命名 `plugin-name:command-name`，前端用 `invoke('plugin-name|command')` 调用
- 权限在 `permissions/` 目录用 TOML 定义，控制前端可访问的命令
- `guest-js/` 提供前端类型安全的绑定
- **常见坑**：插件名需全小写连字符分隔；权限文件名需与权限标识符一致；前端调用的分隔符是 `|` 不是 `:`；插件配置通过 `tauri.conf.json` 的 `plugins` 部分传入

---

# 第 6 章：进阶能力

本章关注 Tauri 的进阶能力：自动更新让应用保持最新，全局快捷键提供系统级快捷操作，通知系统与深度链接增强用户体验，安全策略与权限系统保护应用安全。这些能力让你的应用从"能用"提升到"好用"。

## 第 22 讲：自动更新

### 概念

**自动更新**是桌面应用的重要能力——应用自动检查新版本、下载更新包、安装重启。Tauri 通过 `tauri-plugin-updater` 提供内置更新机制，支持签名验证（防止篡改）、增量更新、跨平台。配合 GitHub Releases 或自建更新服务器，可实现无缝的版本迭代。

### 原理

Tauri 更新机制基于"清单 + 签名"模型。应用启动时（或用户手动触发）向更新服务器发送请求，包含当前版本、平台、架构信息。服务器返回最新版本清单（JSON），包含版本号、下载 URL、签名。客户端比较版本号，若有新版本则下载更新包（`.tar.gz` 压缩的应用文件），验证签名（用预置公钥），然后替换应用文件并重启。

签名验证是安全核心——更新包必须用私钥签名，客户端用预置公钥验证。这防止中间人攻击注入恶意更新。密钥对通过 `tauri signer generate` 生成，私钥保密，公钥配置在 `tauri.conf.json` 中。

更新服务器可以是任意 HTTP 服务，只需返回特定格式的 JSON。Tauri 官方推荐用 GitHub Releases + `latest.json` 清单文件，或自建服务（如 `tauri-update-server`）。清单格式：`{ "version": "1.0.1", "notes": "修复 bug", "pub_date": "2024-01-01T00:00:00Z", "platforms": { "darwin-x86_64": { "signature": "...", "url": "..." } } }`。

### 例子

**安装更新插件**：

```bash
# 后端
cargo add tauri-plugin-updater

# 前端
pnpm add @tauri-apps/plugin-updater
pnpm add @tauri-apps/plugin-process  # 用于重启
```

**生成签名密钥**：

```bash
# 生成密钥对
tauri signer generate -w ~/.tauri/myapp.key

# 输出:
# 私钥: ~/.tauri/myapp.key (保密!)
# 公钥: dW50cnVzdGVkIGNvbW1l...
# 密码: (你设置的密码)
```

**配置 `tauri.conf.json`**：

```json
{
  "bundle": {
    "createUpdaterArtifacts": true
  },
  "plugins": {
    "updater": {
      "active": true,
      "endpoints": [
        "https://myapp.com/updates/{{target}}/{{arch}}/{{current_version}}"
      ],
      "pubkey": "dW50cnVzdGVkIGNvbW1lbnQ6IG1pbmlzaWduIHB1YmxpYyBrZXk6IEY1RUI4OEE2Qjg5RjlCMjcKUldRbmtPWGpDUEpFQXJ4R2V4Y2VqR2Zaa2VwSzM2S0hQUmFQcUtsRgo=",
      "windows": {
        "installMode": "passive"
      }
    }
  }
}
```

**后端注册插件**：

```rust
use tauri_plugin_updater::UpdaterExt;

fn main() {
    tauri::Builder::default()
        .plugin(tauri_plugin_updater::Builder::new().build())
        .plugin(tauri_plugin_process::init())
        .setup(|app| {
            // 可选：启动时自动检查更新
            let app_handle = app.handle().clone();
            tauri::async_runtime::spawn(async move {
                // 延迟 5 秒检查，避免影响启动
                tokio::time::sleep(std::time::Duration::from_secs(5)).await;
                check_for_updates(app_handle).await;
            });
            Ok(())
        })
        .run(tauri::generate_context!())
        .unwrap();
}

async fn check_for_updates(app: tauri::AppHandle) {
    if let Some(updater) = app.updater() {
        match updater.check().await {
            Ok(Some(update)) => {
                println!("发现新版本: {}", update.version);
                // 通知前端有新版本
                let _ = app.emit("update-available", UpdateInfo {
                    version: update.version.clone(),
                    notes: update.body.clone(),
                    date: update.date.clone(),
                });
            }
            Ok(None) => {
                println!("已是最新版本");
            }
            Err(e) => {
                println!("检查更新失败: {}", e);
            }
        }
    }
}

#[derive(serde::Serialize, Clone)]
struct UpdateInfo {
    version: String,
    notes: Option<String>,
    date: Option<String>,
}
```

**前端更新逻辑**：

```typescript
import { check } from '@tauri-apps/plugin-updater';
import { relaunch } from '@tauri-apps/plugin-process';
import { listen } from '@tauri-apps/api/event';

interface UpdateInfo {
    version: string;
    notes?: string;
    date?: string;
}

// ===== 手动检查更新 =====
async function checkForUpdates() {
    const update = await check();
    if (update) {
        const confirmed = confirm(
            `发现新版本 ${update.version}!\n\n` +
            `更新内容:\n${update.body}\n\n` +
            `是否立即更新？`
        );
        if (confirmed) {
            await downloadAndInstall(update);
        }
    } else {
        alert('已是最新版本');
    }
}

// ===== 下载并安装更新 =====
async function downloadAndInstall(update: any) {
    // 显示下载进度
    showUpdateProgress();

    let downloaded = 0;
    let total = 0;

    await update.downloadAndInstall((event: any) => {
        switch (event.event) {
            case 'Started':
                total = event.data.contentLength ?? 0;
                console.log('开始下载，总大小:', total);
                break;
            case 'Progress':
                downloaded += event.data.chunkLength;
                const percent = total > 0 ? (downloaded / total * 100).toFixed(1) : '0';
                console.log(`下载进度: ${percent}%`);
                updateProgress(percent);
                break;
            case 'Finished':
                console.log('下载完成');
                break;
        }
    });

    // 安装完成，重启应用
    await relaunch();
}

// ===== 监听后端的更新通知 =====
await listen<UpdateInfo>('update-available', (event) => {
    const { version, notes } = event.payload;
    showUpdateBanner(`新版本 ${version} 可用`, notes);
});

// ===== 更新 UI 组件 =====
function showUpdateProgress() {
    const modal = document.createElement('div');
    modal.id = 'update-modal';
    modal.innerHTML = `
        <div class="update-backdrop">
            <div class="update-dialog">
                <h3>正在更新...</h3>
                <div class="progress-bar">
                    <div id="progress-fill" style="width: 0%"></div>
                </div>
                <p id="progress-text">0%</p>
            </div>
        </div>
    `;
    document.body.appendChild(modal);
}

function updateProgress(percent: string) {
    const fill = document.getElementById('progress-fill');
    const text = document.getElementById('progress-text');
    if (fill) fill.style.width = `${percent}%`;
    if (text) text.textContent = `${percent}%`;
}
```

**更新服务器清单格式** `latest.json`：

```json
{
  "version": "1.0.1",
  "notes": "修复了若干 bug，提升性能",
  "pub_date": "2024-01-15T10:00:00Z",
  "platforms": {
    "darwin-x86_64": {
      "signature": "dW50cnVzdGVkIGNvbW1lbnQ6IHNpZ25hdHVyZSBmcm9tIH...",
      "url": "https://github.com/myapp/myapp/releases/download/v1.0.1/myapp.app.tar.gz"
    },
    "darwin-aarch64": {
      "signature": "dW50cnVzdGVkIGNvbW1lbnQ6IHNpZ25hdHVyZSBmcm9tIH...",
      "url": "https://github.com/myapp/myapp/releases/download/v1.0.1/myapp-aarch64.app.tar.gz"
    },
    "windows-x86_64": {
      "signature": "dW50cnVzdGVkIGNvbW1lbnQ6IHNpZ25hdHVyZSBmcm9tIH...",
      "url": "https://github.com/myapp/myapp/releases/download/v1.0.1/myapp-setup.nsis.zip"
    },
    "linux-x86_64": {
      "signature": "dW50cnVzdGVkIGNvbW1lbnQ6IHNpZ25hdHVyZSBmcm9tIH...",
      "url": "https://github.com/myapp/myapp/releases/download/v1.0.1/myapp-amd64.AppImage.tar.gz"
    }
  }
}
```

**GitHub Actions 自动发布更新**：

```yaml
name: Release

on:
  push:
    tags:
      - 'v*'

jobs:
  release:
    strategy:
      matrix:
        os: [ubuntu-latest, macos-latest, windows-latest]
    runs-on: ${{ matrix.os }}
    steps:
      - uses: actions/checkout@v3

      - uses: dtolnay/rust-toolchain@stable

      - uses: pnpm/action-setup@v2
        with:
          version: 8

      - uses: actions/setup-node@v3
        with:
          node-version: 20
          cache: pnpm

      - run: pnpm install

      - name: Build and release
        uses: tauri-apps/tauri-action@v0
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          TAURI_SIGNING_PRIVATE_KEY: ${{ secrets.TAURI_SIGNING_PRIVATE_KEY }}
          TAURI_SIGNING_PRIVATE_KEY_PASSWORD: ${{ secrets.TAURI_SIGNING_PRIVATE_KEY_PASSWORD }}
        with:
          tagName: ${{ github.ref_name }}
          releaseName: 'App v__VERSION__'
          releaseBody: 'See the assets to download this version and install.'
          releaseDraft: false
          prerelease: false
          updaterJsonPreferNsis: true
```

更新流程：

```
┌─────────────────────────────────────────────────────────────┐
│                   自动更新流程                              │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. 应用启动                                                 │
│     │                                                       │
│     ▼                                                       │
│  2. 检查更新 (check)                                        │
│     │                                                       │
│     │  GET https://myapp.com/updates/                       │
│     │      darwin-x86_64/1.0.0                              │
│     │                                                       │
│     ▼                                                       │
│  3. 服务器返回最新版本清单                                   │
│     {                                                       │
│       "version": "1.0.1",                                   │
│       "url": "...",                                         │
│       "signature": "..."                                    │
│     }                                                       │
│     │                                                       │
│     ▼                                                       │
│  4. 比较版本号                                               │
│     1.0.0 < 1.0.1 → 有新版本                                │
│     │                                                       │
│     ▼                                                       │
│  5. 下载更新包                                               │
│     │                                                       │
│     │  下载 .tar.gz (含新版本应用文件)                      │
│     │  显示进度条                                            │
│     │                                                       │
│     ▼                                                       │
│  6. 验证签名                                                 │
│     │                                                       │
│     │  用预置公钥验证下载包的签名                            │
│     │  签名不匹配 → 拒绝更新                                │
│     │                                                       │
│     ▼                                                       │
│  7. 安装更新                                                 │
│     │                                                       │
│     │  解压并替换应用文件                                    │
│     │  (macOS: 替换 .app 内容)                              │
│     │  (Windows: NSIS 安装器)                               │
│     │  (Linux: 替换 AppImage)                               │
│     │                                                       │
│     ▼                                                       │
│  8. 重启应用                                                 │
│     │                                                       │
│     │  relaunch() → 关闭当前进程 → 启动新版本               │
│     │                                                       │
│     ▼                                                       │
│  9. 新版本运行                                               │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 总结

- `tauri-plugin-updater` 提供自动更新，基于"清单 + 签名"模型
- `tauri signer generate` 生成密钥对，私钥签名更新包，公钥验证
- 更新服务器返回 JSON 清单，含版本号、下载 URL、签名
- `update.downloadAndInstall()` 下载并安装，支持进度回调
- **常见坑**：私钥必须保密，泄露后需重新生成；更新包必须用私钥签名，否则验证失败；macOS 需公证（notarize）才能更新；Windows NSIS 安装器需配置 `installMode`

---

## 第 23 讲：全局快捷键

### 概念

**全局快捷键**让应用在未获焦时也能响应快捷键——如截图工具按 `Cmd+Shift+5` 截屏、音乐播放器按 `MediaPlay` 播放。Tauri 通过 `tauri-plugin-global-shortcut` 提供全局快捷键注册，快捷键即使应用在后台或其他应用获焦时也能触发。适合"后台常驻 + 快速唤起"的应用场景。

### 原理

全局快捷键由操作系统注册——macOS 用 `CGEventTap`、Windows 用 `RegisterHotKey`、Linux 用 X11 的 `XGrabKey`。应用向系统注册快捷键后，无论哪个应用获焦，按下该快捷键都会通知注册应用。Tauri 的 `global-shortcut` 插件封装了这些平台 API，提供统一接口。

快捷键格式用修饰键 + 主键表示：`CmdOrCtrl+Shift+P`、`Alt+F1`、`MediaPlay`。修饰键包括 `Command`（macOS）、`Control`、`Option`/`Alt`、`Shift`、`Super`/`Meta`/`Win`。`CmdOrCtrl` 是跨平台便捷写法——macOS 自动映射为 `Command`，其他平台映射为 `Control`。

注册方式分两种：① **静态注册**——在 `tauri.conf.json` 的 `plugins.global-shortcut.shortcuts` 中定义，应用启动时自动注册；② **动态注册**——运行时用 `register(shortcut, handler)` 注册，`unregister(shortcut)` 注销。动态注册更灵活，适合用户自定义快捷键的场景。

### 例子

**安装插件**：

```bash
# 后端
cargo add tauri-plugin-global-shortcut

# 前端
pnpm add @tauri-apps/plugin-global-shortcut
```

**后端注册与使用**：

```rust
use tauri_plugin_global_shortcut::{GlobalShortcutExt, Code, Modifiers, Shortcut, ShortcutState};
use tauri::{Manager, Emitter};

fn main() {
    tauri::Builder::default()
        .plugin(
            tauri_plugin_global_shortcut::Builder::new()
                .with_shortcuts(["CmdOrCtrl+Shift+P", "CmdOrCtrl+Shift+O"])
                .unwrap()
                .with_handler(|app, shortcut, event| {
                    if event.state == ShortcutState::Pressed {
                        match shortcut {
                            s if s == Shortcut::new(Some(Modifiers::CONTROL | Modifiers::SHIFT), Code::KeyP) => {
                                // 显示主窗口
                                if let Some(window) = app.get_webview_window("main") {
                                    let _ = window.show();
                                    let _ = window.set_focus();
                                }
                                let _ = app.emit("shortcut-pressed", "show");
                            }
                            s if s == Shortcut::new(Some(Modifiers::CONTROL | Modifiers::SHIFT), Code::KeyO) => {
                                // 打开文件
                                let _ = app.emit("shortcut-pressed", "open");
                            }
                            _ => {}
                        }
                    }
                })
                .build()
        )
        .setup(|app| {
            // 动态注册额外快捷键
            let app_handle = app.handle().clone();
            app.global_shortcut().on_shortcut("CmdOrCtrl+Shift+S", move || {
                let _ = app_handle.emit("shortcut-pressed", "save");
            })?;

            Ok(())
        })
        .run(tauri::generate_context!())
        .unwrap();
}
```

**前端使用**：

```typescript
import { register, unregister, isRegistered } from '@tauri-apps/plugin-global-shortcut';
import { listen } from '@tauri-apps/api/event';

// ===== 监听快捷键事件 =====
await listen<string>('shortcut-pressed', (event) => {
    const action = event.payload;
    console.log(`快捷键触发: ${action}`);

    switch (action) {
        case 'show':
            showMainWindow();
            break;
        case 'open':
            openFilePicker();
            break;
        case 'save':
            saveCurrentFile();
            break;
    }
});

// ===== 动态注册快捷键 =====
async function registerCustomShortcut(shortcut: string, handler: () => void) {
    // 检查是否已注册
    const isAlreadyRegistered = await isRegistered(shortcut);
    if (isAlreadyRegistered) {
        await unregister(shortcut);
    }

    // 注册新快捷键
    await register(shortcut, (event) => {
        if (event.state === 'Pressed') {
            handler();
        }
    });

    console.log(`已注册快捷键: ${shortcut}`);
}

// 注册截图快捷键
await registerCustomShortcut('CmdOrCtrl+Shift+1', () => {
    captureScreen();
});

// 注册媒体快捷键
await registerCustomShortcut('MediaPlay', () => {
    togglePlay();
});

// ===== 注销快捷键 =====
async function unregisterShortcut(shortcut: string) {
    await unregister(shortcut);
    console.log(`已注销快捷键: ${shortcut}`);
}

// ===== 用户自定义快捷键 =====
interface ShortcutConfig {
    action: string;
    shortcut: string;
}

const shortcuts: ShortcutConfig[] = [
    { action: 'show', shortcut: 'CmdOrCtrl+Shift+P' },
    { action: 'open', shortcut: 'CmdOrCtrl+Shift+O' },
    { action: 'save', shortcut: 'CmdOrCtrl+Shift+S' },
    { action: 'screenshot', shortcut: 'CmdOrCtrl+Shift+1' },
];

async function applyShortcuts(configs: ShortcutConfig[]) {
    // 先注销所有
    for (const config of configs) {
        if (await isRegistered(config.shortcut)) {
            await unregister(config.shortcut);
        }
    }

    // 重新注册
    for (const config of configs) {
        await register(config.shortcut, (event) => {
            if (event.state === 'Pressed') {
                handleAction(config.action);
            }
        });
    }
}

function handleAction(action: string) {
    switch (action) {
        case 'show': showMainWindow(); break;
        case 'open': openFilePicker(); break;
        case 'save': saveCurrentFile(); break;
        case 'screenshot': captureScreen(); break;
    }
}
```

**快捷键设置界面**：

```typescript
// React 组件：快捷键设置
import { useState, useEffect } from 'react';
import { register, unregister, isRegistered } from '@tauri-apps/plugin-global-shortcut';

function ShortcutSettings() {
    const [shortcuts, setShortcuts] = useState<ShortcutConfig[]>([]);
    const [recording, setRecording] = useState<string | null>(null);

    // 加载已保存的快捷键配置
    useEffect(() => {
        loadShortcuts().then(setShortcuts);
    }, []);

    // 开始录制快捷键
    function startRecording(action: string) {
        setRecording(action);
        document.addEventListener('keydown', captureKey);
    }

    function captureKey(e: KeyboardEvent) {
        e.preventDefault();

        const modifiers: string[] = [];
        if (e.ctrlKey || e.metaKey) modifiers.push('CmdOrCtrl');
        if (e.shiftKey) modifiers.push('Shift');
        if (e.altKey) modifiers.push('Alt');

        const key = e.key.toUpperCase();
        if (!['CONTROL', 'SHIFT', 'ALT', 'META'].includes(key)) {
            const shortcut = [...modifiers, key].join('+');
            updateShortcut(recording!, shortcut);
            setRecording(null);
            document.removeEventListener('keydown', captureKey);
        }
    }

    async function updateShortcut(action: string, shortcut: string) {
        // 注销旧快捷键
        const oldConfig = shortcuts.find(s => s.action === action);
        if (oldConfig && await isRegistered(oldConfig.shortcut)) {
            await unregister(oldConfig.shortcut);
        }

        // 注册新快捷键
        await register(shortcut, () => handleAction(action));

        // 更新配置
        const newShortcuts = shortcuts.map(s =>
            s.action === action ? { ...s, shortcut } : s
        );
        setShortcuts(newShortcuts);
        await saveShortcuts(newShortcuts);
    }

    return (
        <div>
            <h2>快捷键设置</h2>
            {shortcuts.map(config => (
                <div key={config.action}>
                    <span>{getActionLabel(config.action)}</span>
                    <button onClick={() => startRecording(config.action)}>
                        {recording === config.action ? '按下快捷键...' : config.shortcut}
                    </button>
                </div>
            ))}
        </div>
    );
}
```

快捷键格式：

```
┌─────────────────────────────────────────────────────────────┐
│                   快捷键格式说明                            │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  格式: Modifier+Modifier+...+Key                            │
│                                                             │
│  修饰键:                                                    │
│  ┌──────────────┬──────────────────────────────────────┐   │
│  │ CmdOrCtrl    │ macOS: Command, 其他: Control        │   │
│  │ Command      │ macOS 的 Command 键 (⌘)              │   │
│  │ Control      │ Control 键 (Ctrl)                    │   │
│  │ Alt / Option │ Alt 键 (macOS: Option)               │   │
│  │ Shift        │ Shift 键                             │   │
│  │ Super / Meta │ Windows 键 / macOS Command           │   │
│  └──────────────┴──────────────────────────────────────┘   │
│                                                             │
│  主键:                                                      │
│  - 字母: KeyA, KeyB, ..., KeyZ                             │
│  - 数字: Digit0, Digit1, ..., Digit9                       │
│  - 功能键: F1, F2, ..., F24                                │
│  - 方向键: ArrowUp, ArrowDown, ArrowLeft, ArrowRight       │
│  - 特殊键: Space, Enter, Escape, Tab, Backspace            │
│  - 媒体键: MediaPlay, MediaPause, MediaNext, MediaPrev     │
│                                                             │
│  示例:                                                      │
│  - "CmdOrCtrl+Shift+P"                                     │
│  - "Alt+F1"                                                │
│  - "CmdOrCtrl+K"                                           │
│  - "MediaPlay"                                             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 总结

- `tauri-plugin-global-shortcut` 提供全局快捷键，应用未获焦也能触发
- 快捷键格式 `Modifier+Key`，`CmdOrCtrl` 跨平台兼容
- 静态注册在 `tauri.conf.json`，动态注册用 `register`/`unregister`
- 适合"后台常驻 + 快速唤起"场景（截图、播放器、启动器）
- **常见坑**：快捷键冲突——已被其他应用注册的快捷键会注册失败；macOS 需在 `Info.plist` 声明快捷键；`ShortcutState::Pressed`/`Released` 区分按下和释放；注销时需用相同的快捷键字符串

---

## 第 24 讲：通知系统与深度链接

### 概念

**通知系统**让应用向用户发送系统通知——消息提醒、任务完成、错误提示等。**深度链接**（Deep Link）让应用通过自定义 URL 协议（如 `myapp://action`）被其他应用唤起——浏览器点击链接打开应用、文件双击用应用打开。这两个能力增强了应用与系统的集成度。

### 原理

**通知系统**通过 `tauri-plugin-notification` 实现，封装各平台通知 API——macOS `UNUserNotificationCenter`、Windows `ToastNotificationManager`、Linux `libnotify`。通知支持标题、正文、图标、声音，点击通知可唤起应用。首次发送通知需请求权限（macOS、Windows），用户授权后才能显示。

**深度链接**通过注册自定义 URL 协议实现。应用在 `tauri.conf.json` 的 `app.deepLink` 中声明协议（如 `myapp`），系统会将 `myapp://...` 链接转发给应用。应用启动时（或已运行时）收到 URL，解析后执行对应操作。macOS 通过 `CFBundleURLTypes` 注册，Windows 通过注册表，Linux 通过 `.desktop` 文件。

深度链接的典型流程：① 用户在浏览器点击 `myapp://open?file=abc.txt`；② 系统识别协议，唤起应用；③ 应用收到 URL，解析出 `action=open` 和 `file=abc.txt`；④ 执行对应逻辑（打开文件）。这种机制让 Web 与桌面应用无缝衔接。

### 例子

**安装插件**：

```bash
# 通知
cargo add tauri-plugin-notification
pnpm add @tauri-apps/plugin-notification

# 深度链接
cargo add tauri-plugin-deep-link
pnpm add @tauri-apps/plugin-deep-link
```

**配置深度链接** `tauri.conf.json`：

```json
{
  "app": {
    "deepLink": {
      "desktop": {
        "schemes": ["myapp"]
      },
      "mobile": {
        "schemes": ["myapp"]
      }
    }
  }
}
```

**后端注册**：

```rust
use tauri_plugin_notification::NotificationExt;
use tauri_plugin_deep_link::DeepLinkExt;
use tauri::{Manager, Emitter};

fn main() {
    tauri::Builder::default()
        .plugin(tauri_plugin_notification::init())
        .plugin(tauri_plugin_deep_link::init())
        .setup(|app| {
            // ===== 处理深度链接 =====
            // 应用已运行时收到链接
            app.deep_link().on_open_url(|event| {
                for url in event.urls() {
                    println!("收到深度链接: {}", url);
                    // 解析 URL 并执行操作
                    let app_handle = event.app().clone();
                    handle_deep_link(app_handle, url.to_string());
                }
            });

            // 应用启动时检查是否有链接
            if let Some(urls) = app.deep_link().get_current() {
                for url in urls {
                    println!("启动链接: {}", url);
                    handle_deep_link(app.handle().clone(), url.to_string());
                }
            }

            Ok(())
        })
        .run(tauri::generate_context!())
        .unwrap();
}

fn handle_deep_link(app: tauri::AppHandle, url: String) {
    // 解析 URL: myapp://action?param=value
    let parsed = url::Url::parse(&url).unwrap();
    let action = parsed.host_str().unwrap_or("");
    let query: std::collections::HashMap<_, _> = parsed.query_pairs()
        .map(|(k, v)| (k.to_string(), v.to_string()))
        .collect();

    match action {
        "open" => {
            let file = query.get("file").unwrap_or(&String::new()).clone();
            let _ = app.emit("deep-link-open", file);
        }
        "search" => {
            let query_str = query.get("q").unwrap_or(&String::new()).clone();
            let _ = app.emit("deep-link-search", query_str);
        }
        "settings" => {
            let _ = app.emit("deep-link-settings", ());
        }
        _ => {
            println!("未知操作: {}", action);
        }
    }
}

// ===== 发送通知的命令 =====
#[tauri::command]
async fn send_notification(
    app: tauri::AppHandle,
    title: String,
    body: String,
) -> Result<(), String> {
    // 检查权限
    let permitted = app.notification()
        .permission_state()
        .map_err(|e| e.to_string())?;

    if permitted != tauri_plugin_notification::PermissionState::Granted {
        app.notification()
            .request_permission()
            .map_err(|e| e.to_string())?;
    }

    // 发送通知
    app.notification()
        .builder()
        .title(&title)
        .body(&body)
        .show()
        .map_err(|e| e.to_string())?;

    Ok(())
}
```

**前端使用**：

```typescript
import {
    isPermissionGranted,
    requestPermission,
    sendNotification
} from '@tauri-apps/plugin-notification';
import { onOpenUrl, getCurrent } from '@tauri-apps/plugin-deep-link';
import { listen } from '@tauri-apps/api/event';
import { invoke } from '@tauri-apps/api/core';

// ===== 通知功能 =====

// 请求通知权限
async function ensureNotificationPermission() {
    let granted = await isPermissionGranted();
    if (!granted) {
        const permission = await requestPermission();
        granted = permission === 'granted';
    }
    return granted;
}

// 发送简单通知
async function notify(title: string, body: string) {
    const granted = await ensureNotificationPermission();
    if (granted) {
        await sendNotification({ title, body });
    }
}

// 发送带图标的通知
async function notifyWithIcon() {
    await sendNotification({
        title: '新消息',
        body: '你收到一条新消息',
        icon: 'icon.png'
    });
}

// 带操作按钮的通知（macOS）
async function notifyWithActions() {
    await sendNotification({
        title: '下载完成',
        body: '文件已下载完成，是否打开？',
        // macOS 支持操作按钮
        actionTypeId: 'OPEN_FILE'
    });
}

// ===== 深度链接 =====

// 监听深度链接（应用已运行时）
await onOpenUrl((urls) => {
    for (const url of urls) {
        console.log('收到链接:', url);
        handleDeepLink(url);
    }
});

// 检查启动时的链接
const startupUrls = await getCurrent();
if (startupUrls) {
    for (const url of startupUrls) {
        console.log('启动链接:', url);
        handleDeepLink(url);
    }
}

// 处理深度链接
function handleDeepLink(url: string) {
    // myapp://action?param=value
    const parsed = new URL(url);
    const action = parsed.hostname;
    const params = new URLSearchParams(parsed.search);

    switch (action) {
        case 'open':
            const file = params.get('file');
            if (file) {
                openFile(file);
            }
            break;
        case 'search':
            const query = params.get('q');
            if (query) {
                performSearch(query);
            }
            break;
        case 'settings':
            navigateToSettings();
            break;
    }
}

// 监听后端转发的深度链接事件
await listen<string>('deep-link-open', (event) => {
    openFile(event.payload);
});

await listen<string>('deep-link-search', (event) => {
    performSearch(event.payload);
});

// ===== 使用场景 =====

// 场景 1: 下载完成通知
async function onDownloadComplete(fileName: string) {
    await notify('下载完成', `${fileName} 已下载完成`);
}

// 场景 2: 后台任务错误
async function onTaskError(taskName: string, error: string) {
    await notify('任务失败', `${taskName}: ${error}`);
}

// 场景 3: 从浏览器唤起应用
// 用户在浏览器点击 <a href="myapp://open?file=document.pdf">
// → 系统唤起应用 → 应用收到链接 → 打开文件
```

**Web 页面中的深度链接**：

```html
<!-- 网页中放置深度链接 -->
<a href="myapp://open?file=document.pdf">在应用中打开 document.pdf</a>

<a href="myapp://search?q=hello">在应用中搜索 "hello"</a>

<a href="myapp://settings">打开应用设置</a>
```

通知与深度链接架构：

```
┌─────────────────────────────────────────────────────────────┐
│              通知与深度链接架构                             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  通知系统:                                                  │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  应用                                               │   │
│  │  ┌──────────────────────────────────────────────┐   │   │
│  │  │  sendNotification({ title, body })           │   │   │
│  │  └────────────────┬─────────────────────────────┘   │   │
│  │                   │                                 │   │
│  │                   ▼                                 │   │
│  │  ┌──────────────────────────────────────────────┐   │   │
│  │  │  系统通知中心                                │   │   │
│  │  │  macOS: UNUserNotificationCenter             │   │   │
│  │  │  Windows: ToastNotificationManager           │   │   │
│  │  │  Linux: libnotify                            │   │   │
│  │  └────────────────┬─────────────────────────────┘   │   │
│  │                   │                                 │   │
│  │                   ▼                                 │   │
│  │  ┌──────────────────────────────────────────────┐   │   │
│  │  │  用户看到通知                                │   │   │
│  │  │  ┌────────────────────────────────────┐      │   │   │
│  │  │  │ 🔔 新消息                          │      │   │   │
│  │  │  │ 你收到一条新消息                   │      │   │   │
│  │  │  └────────────────────────────────────┘      │   │   │
│  │  └──────────────────────────────────────────────┘   │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  深度链接:                                                  │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  浏览器                                              │   │
│  │  ┌──────────────────────────────────────────────┐   │   │
│  │  │  <a href="myapp://open?file=abc.txt">       │   │   │
│  │  │  在应用中打开                                │   │   │
│  │  └────────────────┬─────────────────────────────┘   │   │
│  │                   │ 点击                            │   │
│  │                   ▼                                 │   │
│  │  ┌──────────────────────────────────────────────┐   │   │
│  │  │  操作系统                                    │   │   │
│  │  │  识别 myapp:// 协议                          │   │   │
│  │  │  查找注册的应用                              │   │   │
│  │  └────────────────┬─────────────────────────────┘   │   │
│  │                   │                                 │   │
│  │                   ▼                                 │   │
│  │  ┌──────────────────────────────────────────────┐   │   │
│  │  │  Tauri 应用                                  │   │   │
│  │  │  收到 URL: myapp://open?file=abc.txt         │   │   │
│  │  │  解析: action=open, file=abc.txt             │   │   │
│  │  │  执行: 打开 abc.txt                          │   │   │
│  │  └──────────────────────────────────────────────┘   │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 总结

- `tauri-plugin-notification` 提供系统通知，首次需请求权限
- `tauri-plugin-deep-link` 注册自定义 URL 协议，让应用通过链接唤起
- 通知支持标题、正文、图标，点击可唤起应用
- 深度链接格式 `scheme://action?param=value`，前端用 `onOpenUrl` 监听
- **常见坑**：通知权限被拒绝时无法显示；深度链接协议需在 `tauri.conf.json` 声明；macOS 需在 `Info.plist` 配置 URL schemes；深度链接在应用已运行和未运行时的处理方式不同

---

## 第 25 讲：安全策略与权限

### 概念

安全是桌面应用的重要考量。Tauri 2.x 的安全模型基于**权限系统**——前端默认无任何系统能力访问权，必须在 `capabilities/` 中显式授权。**CSP（内容安全策略）**防止 XSS 攻击，**作用域**限制文件/网络访问范围。理解这些安全机制，才能构建出既功能丰富又安全可靠的桌面应用。

### 原理

Tauri 2.x 的权限系统是"默认拒绝"模型——前端无法访问任何系统能力（文件、剪贴板、通知等），除非在 `capabilities/` 中显式授权。每个能力（capability）关联到一组窗口，定义这些窗口可使用的权限。权限以 `plugin:permission-name` 格式标识，如 `fs:allow-read-text-file`、`notification:allow-notify`。

**CSP（Content Security Policy）**是 Web 标准的安全机制，限制前端可加载的资源（脚本、样式、图片、连接等）。Tauri 在 `tauri.conf.json` 的 `app.security.csp` 中配置。严格 CSP 防止 XSS 攻击——即使攻击者注入恶意脚本，也无法加载外部资源或执行内联脚本。开发环境通常放宽 CSP（`devCsp`），生产环境需严格配置。

**作用域（Scope）**进一步限制能力的作用范围。例如 `fs:allow-read-text-file` 权限可配置 `allow` 路径模式，限制只能读取特定目录的文件。`http:allow-fetch` 可配置允许的 URL 模式。这种"权限 + 作用域"的双重控制，让安全粒度更精细。

### 例子

**权限配置 `src-tauri/capabilities/default.json`**：

```json
{
  "$schema": "../gen/schemas/desktop-schema.json",
  "identifier": "default",
  "description": "主窗口的默认权限",
  "windows": ["main"],
  "permissions": [
    "core:default",

    "core:window:default",
    "core:window:allow-close",
    "core:window:allow-minimize",
    "core:window:allow-maximize",
    "core:window:allow-set-title",

    "core:event:default",

    "core:webview:default",

    "core:app:default"
  ]
}
```

**细粒度权限配置**：

```json
{
  "$schema": "../gen/schemas/desktop-schema.json",
  "identifier": "main-capabilities",
  "description": "主窗口权限",
  "windows": ["main"],
  "permissions": [
    "core:default",

    "fs:default",
    {
      "identifier": "fs:allow-read-text-file",
      "allow": [
        { "path": "$APPDATA/documents/*" },
        { "path": "$DOCUMENT/*.txt" },
        { "path": "$DOCUMENT/*.md" }
      ]
    },
    {
      "identifier": "fs:allow-write-text-file",
      "allow": [
        { "path": "$APPDATA/documents/*" }
      ]
    },
    "fs:allow-read-dir",
    "fs:allow-exists",
    "fs:allow-mkdir",

    "dialog:default",
    "dialog:allow-open",
    "dialog:allow-save",
    "dialog:allow-message",
    "dialog:allow-ask",
    "dialog:allow-confirm",

    "clipboard-manager:allow-read-text",
    "clipboard-manager:allow-write-text",

    "notification:default",
    "notification:allow-notify",
    "notification:allow-is-permission-granted",
    "notification:allow-request-permission",

    "shell:allow-open",
    {
      "identifier": "shell:allow-execute",
      "allow": [
        {
          "name": "run-script",
          "command": "npm",
          "args": ["run", "build"]
        }
      ]
    },

    "http:default",
    {
      "identifier": "http:allow-fetch",
      "allow": [
        { "url": "https://api.example.com/*" },
        { "url": "https://cdn.example.com/*" }
      ]
    },

    "os:default",
    "process:default",
    "process:allow-exit",
    "process:allow-restart",

    "log:default",
    "log:allow-info",
    "log:allow-error",
    "log:allow-warn"
  ]
}
```

**多窗口不同权限**：

```json
// capabilities/main.json
{
  "identifier": "main-capabilities",
  "windows": ["main"],
  "permissions": [
    "core:default",
    "fs:default",
    "dialog:default",
    "notification:default"
  ]
}

// capabilities/settings.json
{
  "identifier": "settings-capabilities",
  "windows": ["settings"],
  "permissions": [
    "core:default",
    "core:window:allow-close",
    "fs:allow-read-text-file",
    "fs:allow-write-text-file"
  ]
}

// capabilities/login.json
{
  "identifier": "login-capabilities",
  "windows": ["login"],
  "permissions": [
    "core:default",
    "core:window:allow-close",
    "http:allow-fetch"
  ]
}
```

**CSP 配置** `tauri.conf.json`：

```json
{
  "app": {
    "security": {
      "csp": "default-src 'self'; img-src 'self' data: https:; script-src 'self'; style-src 'self' 'unsafe-inline'; connect-src 'self' https://api.example.com; font-src 'self' data:",
      "devCsp": "default-src 'self' 'unsafe-inline' 'unsafe-eval' data: https:",
      "freezePrototype": false,
      "dangerousDisableAssetCspModification": false,
      "assetProtocol": {
        "enable": true,
        "scope": ["$APPDATA/assets/*", "$DOCUMENT/*"]
      }
    }
  }
}
```

CSP 指令说明：

```
┌─────────────────────────────────────────────────────────────┐
│                   CSP 指令说明                              │
├──────────────────┬──────────────────────────────────────────┤
│ 指令             │ 说明                                    │
├──────────────────┼──────────────────────────────────────────┤
│ default-src      │ 默认策略，其他指令未指定时使用          │
│ script-src       │ JavaScript 来源                         │
│ style-src        │ CSS 来源                                │
│ img-src          │ 图片来源                                │
│ font-src         │ 字体来源                                │
│ connect-src      │ XMLHttpRequest/fetch/WebSocket 连接     │
│ media-src        │ 音视频来源                              │
│ frame-src        │ iframe 来源                             │
│ object-src       │ <object>/<embed> 来源                  │
│ worker-src       │ Worker 来源                             │
├──────────────────┼──────────────────────────────────────────┤
│ 来源值           │ 说明                                    │
├──────────────────┼──────────────────────────────────────────┤
│ 'self'           │ 同源（应用自身）                        │
│ 'none'           │ 禁止所有                                │
│ 'unsafe-inline'  │ 允许内联（不安全）                      │
│ 'unsafe-eval'    │ 允许 eval（不安全）                     │
│ https:           │ 所有 HTTPS                              │
│ data:            │ data: URI                              │
│ 具体URL          │ 指定域名                                │
└──────────────────┴──────────────────────────────────────────┘

推荐 CSP (生产环境):
default-src 'self';
script-src 'self';
style-src 'self' 'unsafe-inline';
img-src 'self' data: https:;
connect-src 'self' https://api.example.com;
font-src 'self' data:;
object-src 'none';
base-uri 'self';
frame-ancestors 'none';
```

**安全最佳实践**：

```rust
// ===== 后端安全实践 =====

// 1. 输入验证
#[tauri::command]
async fn read_file(path: String) -> Result<String, String> {
    // 验证路径（防止路径遍历攻击）
    let path = std::path::Path::new(&path);
    let canonical = path.canonicalize()
        .map_err(|e| e.to_string())?;

    // 确保路径在允许的目录内
    let allowed_dir = std::env::current_dir().unwrap();
    if !canonical.starts_with(&allowed_dir) {
        return Err("无权访问该路径".to_string());
    }

    std::fs::read_to_string(&canonical)
        .map_err(|e| e.to_string())
}

// 2. SQL 参数化（防注入）
#[tauri::command]
async fn search_users(
    pool: State<'_, sqlx::SqlitePool>,
    query: String,
) -> Result<Vec<User>, String> {
    // 使用参数化查询
    let users = sqlx::query_as::<_, User>(
        "SELECT * FROM users WHERE name LIKE $1"
    )
    .bind(format!("%{}%", query))  // 参数绑定
    .fetch_all(pool.inner())
    .await
    .map_err(|e| e.to_string())?;

    Ok(users)
}

// 3. 命令执行白名单
#[tauri::command]
async fn run_command(cmd: String, args: Vec<String>) -> Result<String, String> {
    // 白名单检查
    let allowed_commands = ["git", "npm", "cargo"];
    if !allowed_commands.contains(&cmd.as_str()) {
        return Err(format!("命令 {} 不被允许", cmd));
    }

    let output = std::process::Command::new(&cmd)
        .args(&args)
        .output()
        .map_err(|e| e.to_string())?;

    Ok(String::from_utf8_lossy(&output.stdout).to_string())
}

// 4. 敏感数据不记录日志
#[tauri::command]
async fn login(username: String, password: String) -> Result<User, String> {
    // 不要记录密码！
    println!("用户 {} 尝试登录", username);  // 只记录用户名

    // 验证...
    Ok(User { /* ... */ })
}
```

```typescript
// ===== 前端安全实践 =====

// 1. 转义用户输入（防 XSS）
function escapeHtml(text: string): string {
    const div = document.createElement('div');
    div.textContent = text;
    return div.innerHTML;
}

// 显示用户输入时转义
function displayUserInput(input: string) {
    // ❌ 危险：XSS 漏洞
    // document.getElementById('output').innerHTML = input;

    // ✅ 安全：转义后插入
    document.getElementById('output')!.innerHTML = escapeHtml(input);

    // ✅ 或用 textContent
    document.getElementById('output')!.textContent = input;
}

// 2. 验证 IPC 返回数据
interface User {
    id: number;
    name: string;
    email: string;
}

function validateUser(data: unknown): User {
    if (typeof data !== 'object' || data === null) {
        throw new Error('无效的用户数据');
    }
    const user = data as Record<string, unknown>;
    if (typeof user.id !== 'number' ||
        typeof user.name !== 'string' ||
        typeof user.email !== 'string') {
        throw new Error('用户数据格式错误');
    }
    return data as User;
}

const userData = await invoke<unknown>('get_user', { id: 1 });
const user = validateUser(userData);  // 验证后再使用

// 3. 安全存储 token
import { Store } from '@tauri-apps/plugin-store';

const store = await Store.load('auth.json');
// 存储 token（Tauri Store 数据在 $APPDATA，相对安全）
await store.set('token', token);
await store.save();

// 4. 不在前端存储敏感信息
// ❌ 不要在前端代码中硬编码 API key
// const API_KEY = 'sk-1234567890';

// ✅ 通过后端代理 API 调用
const result = await invoke('call_api', { endpoint: '/data' });
```

安全检查清单：

```
┌─────────────────────────────────────────────────────────────┐
│                Tauri 应用安全检查清单                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  □ 权限最小化                                               │
│    □ 只授予必要的权限                                       │
│    □ 不同窗口不同权限                                       │
│    □ 文件系统用作用域限制路径                               │
│    □ HTTP 请求用作用域限制 URL                              │
│                                                             │
│  □ CSP 配置                                                 │
│    □ 生产环境严格 CSP                                       │
│    □ 禁止 unsafe-inline/unsafe-eval（如可能）              │
│    □ 限制 connect-src 到已知 API                           │
│    □ 开发环境用 devCsp 放宽                                 │
│                                                             │
│  □ 输入验证                                                 │
│    □ 后端命令验证所有输入                                   │
│    □ 路径遍历防护（canonicalize + starts_with）            │
│    □ SQL 参数化查询                                         │
│    □ 命令执行白名单                                         │
│                                                             │
│  □ 敏感数据                                                 │
│    □ 不记录密码/token 到日志                                │
│    □ API key 放后端，不暴露给前端                           │
│    □ 敏感数据用 Tauri Store（$APPDATA）                    │
│    □ 不在前端代码硬编码密钥                                 │
│                                                             │
│  □ 前端安全                                                 │
│    □ 转义用户输入（防 XSS）                                 │
│    □ 验证 IPC 返回数据                                      │
│    □ 不用 innerHTML 插入用户输入                            │
│    □ 使用 Content-Security-Policy                           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 总结

- Tauri 2.x 权限系统是"默认拒绝"模型，需在 `capabilities/` 显式授权
- 权限格式 `plugin:permission-name`，可配置作用域限制范围
- CSP 防止 XSS，生产环境需严格配置
- 作用域限制文件/网络访问范围，提供精细安全控制
- **常见坑**：权限配置错误导致前端调用失败；CSP 太严会阻断前端资源；`unsafe-inline`/`unsafe-eval` 降低安全性；路径遍历攻击需用 `canonicalize` 防护

---

# 第 7 章：实战与工程化

本章是 Tauri 学习的收官之作。我们将通过一个完整的笔记应用综合运用前 25 讲所学知识，然后学习性能优化技巧、测试方法、发布部署流程。完成本章后，你将具备独立开发、优化、测试、发布 Tauri 应用的完整能力。

## 第 26 讲：实战：笔记应用

### 概念

本讲通过一个完整的笔记应用，综合运用前 25 讲的知识。应用功能包括：笔记的增删改查、Markdown 编辑与预览、自动保存、主题切换、全局快捷键唤起、系统托盘后台运行。这是对 Tauri 核心能力的全面检验——IPC、状态管理、文件系统、窗口管理、插件集成、安全配置。

### 原理

笔记应用的架构设计：① **数据层**——用 `tauri-plugin-store` 存储笔记元数据（标题、创建时间），用 `tauri-plugin-fs` 存储笔记内容（Markdown 文件）；② **业务层**——Rust 后端处理笔记 CRUD、文件读写、搜索；③ **UI 层**——React 前端，左侧笔记列表，右侧编辑器/预览；④ **集成层**——系统托盘后台运行、全局快捷键唤起、自动保存。

数据流：用户编辑笔记 → 前端防抖（500ms 无操作）→ 调用 `save_note` 命令 → 后端写入文件 → 更新 Store 元数据 → 发送 `note-saved` 事件 → 前端更新状态。这种"防抖 + 异步保存"让编辑流畅，避免每次按键都写文件。

### 例子

**项目结构**：

```
notes-app/
├── src/                    # 前端
│   ├── App.tsx
│   ├── components/
│   │   ├── NoteList.tsx
│   │   ├── NoteEditor.tsx
│   │   └── NotePreview.tsx
│   ├── hooks/
│   │   └── useNotes.ts
│   └── styles/
├── src-tauri/
│   ├── src/
│   │   ├── main.rs
│   │   ├── commands.rs
│   │   ├── models.rs
│   │   └── tray.rs
│   ├── Cargo.toml
│   ├── tauri.conf.json
│   └── capabilities/
│       └── default.json
└── package.json
```

**后端实现** `src-tauri/src/main.rs`：

```rust
use tauri::Manager;
use std::sync::Mutex;

mod commands;
mod models;
mod tray;

use models::AppState;

fn main() {
    tauri::Builder::default()
        .plugin(tauri_plugin_fs::init())
        .plugin(tauri_plugin_store::Builder::default().build())
        .plugin(tauri_plugin_dialog::init())
        .plugin(tauri_plugin_notification::init())
        .plugin(tauri_plugin_global_shortcut::Builder::new().build())
        .plugin(tauri_plugin_clipboard_manager::init())
        .setup(|app| {
            // 初始化应用状态
            let app_data_dir = app.path().app_data_dir()?;
            std::fs::create_dir_all(&app_data_dir)?;

            let notes_dir = app_data_dir.join("notes");
            std::fs::create_dir_all(&notes_dir)?;

            app.manage(Mutex::new(AppState {
                notes_dir: notes_dir.clone(),
                current_note_id: None,
            }));

            // 设置系统托盘
            tray::setup_tray(app)?;

            // 注册全局快捷键
            app.global_shortcut().on_shortcut("CmdOrCtrl+Shift+N", || {
                // 显示主窗口
            })?;

            Ok(())
        })
        .invoke_handler(tauri::generate_handler![
            commands::get_notes,
            commands::create_note,
            commands::save_note,
            commands::delete_note,
            commands::search_notes,
            commands::get_note_content,
        ])
        .run(tauri::generate_context!())
        .unwrap();
}
```

**数据模型** `src-tauri/src/models.rs`：

```rust
use serde::{Serialize, Deserialize};
use std::path::PathBuf;

#[derive(Serialize, Deserialize, Clone)]
pub struct Note {
    pub id: String,
    pub title: String,
    pub created_at: u64,
    pub updated_at: u64,
    pub tags: Vec<String>,
}

pub struct AppState {
    pub notes_dir: PathBuf,
    pub current_note_id: Option<String>,
}
```

**命令实现** `src-tauri/src/commands.rs`：

```rust
use crate::models::{Note, AppState};
use tauri::{State, AppHandle, Emitter};
use tauri_plugin_store::StoreExt;
use std::sync::Mutex;
use uuid::Uuid;

#[tauri::command]
pub async fn get_notes(
    app: AppHandle,
) -> Result<Vec<Note>, String> {
    let store = app.store("notes.json")
        .map_err(|e| e.to_string())?;

    let notes: Vec<Note> = store.entries()
        .map_err(|e| e.to_string())?
        .filter_map(|(key, value)| {
            if key.as_str().starts_with("note:") {
                serde_json::from_value(value).ok()
            } else {
                None
            }
        })
        .collect();

    // 按更新时间排序
    let mut notes = notes;
    notes.sort_by(|a, b| b.updated_at.cmp(&a.updated_at));

    Ok(notes)
}

#[tauri::command]
pub async fn create_note(
    app: AppHandle,
    state: State<'_, Mutex<AppState>>,
) -> Result<Note, String> {
    let id = Uuid::new_v4().to_string();
    let now = std::time::SystemTime::now()
        .duration_since(std::time::UNIX_EPOCH)
        .unwrap()
        .as_secs();

    let note = Note {
        id: id.clone(),
        title: "新笔记".to_string(),
        created_at: now,
        updated_at: now,
        tags: vec![],
    };

    // 保存到 Store
    let store = app.store("notes.json").map_err(|e| e.to_string())?;
    store.set(format!("note:{}", id), serde_json::to_value(&note).unwrap());
    store.save().map_err(|e| e.to_string())?;

    // 创建笔记文件
    let state = state.lock().unwrap();
    let note_path = state.notes_dir.join(format!("{}.md", id));
    std::fs::write(&note_path, "# 新笔记\n\n开始编辑...").map_err(|e| e.to_string())?;

    Ok(note)
}

#[tauri::command]
pub async fn save_note(
    app: AppHandle,
    state: State<'_, Mutex<AppState>>,
    id: String,
    content: String,
    title: Option<String>,
) -> Result<Note, String> {
    // 提取标题（Markdown 第一行）
    let extracted_title = title.unwrap_or_else(|| {
        content.lines()
            .next()
            .and_then(|line| line.strip_prefix("# ").map(|s| s.to_string()))
            .unwrap_or("无标题".to_string())
    });

    // 保存内容到文件
    let state = state.lock().unwrap();
    let note_path = state.notes_dir.join(format!("{}.md", id));
    std::fs::write(&note_path, &content).map_err(|e| e.to_string())?;
    drop(state);

    // 更新元数据
    let store = app.store("notes.json").map_err(|e| e.to_string())?;
    let key = format!("note:{}", id);

    let mut note: Note = store.get(&key)
        .map_err(|e| e.to_string())?
        .ok_or("笔记不存在")?;
    note.title = extracted_title;
    note.updated_at = std::time::SystemTime::now()
        .duration_since(std::time::UNIX_EPOCH)
        .unwrap()
        .as_secs();

    store.set(key, serde_json::to_value(&note).unwrap());
    store.save().map_err(|e| e.to_string())?;

    // 通知前端
    app.emit("note-saved", &note).ok();

    Ok(note)
}

#[tauri::command]
pub async fn delete_note(
    app: AppHandle,
    state: State<'_, Mutex<AppState>>,
    id: String,
) -> Result<(), String> {
    // 删除文件
    let state = state.lock().unwrap();
    let note_path = state.notes_dir.join(format!("{}.md", id));
    if note_path.exists() {
        std::fs::remove_file(&note_path).map_err(|e| e.to_string())?;
    }
    drop(state);

    // 删除元数据
    let store = app.store("notes.json").map_err(|e| e.to_string())?;
    store.delete(format!("note:{}", id));
    store.save().map_err(|e| e.to_string())?;

    Ok(())
}

#[tauri::command]
pub async fn get_note_content(
    state: State<'_, Mutex<AppState>>,
    id: String,
) -> Result<String, String> {
    let state = state.lock().unwrap();
    let note_path = state.notes_dir.join(format!("{}.md", id));
    std::fs::read_to_string(&note_path).map_err(|e| e.to_string())
}

#[tauri::command]
pub async fn search_notes(
    app: AppHandle,
    query: String,
) -> Result<Vec<Note>, String> {
    let store = app.store("notes.json").map_err(|e| e.to_string())?;
    let state: tauri::State<'_, Mutex<AppState>> = app.state();

    let mut results = Vec::new();

    let entries = store.entries().map_err(|e| e.to_string())?;
    for (key, value) in entries {
        if !key.as_str().starts_with("note:") {
            continue;
        }

        let note: Note = serde_json::from_value(value).unwrap();
        let state = state.lock().unwrap();
        let note_path = state.notes_dir.join(format!("{}.md", note.id));
        drop(state);

        // 搜索标题和内容
        let title_match = note.title.to_lowercase().contains(&query.to_lowercase());
        let content_match = std::fs::read_to_string(&note_path)
            .map(|c| c.to_lowercase().contains(&query.to_lowercase()))
            .unwrap_or(false);

        if title_match || content_match {
            results.push(note);
        }
    }

    Ok(results)
}
```

**前端实现** `src/App.tsx`：

```typescript
import { useState, useEffect, useCallback } from 'react';
import { invoke } from '@tauri-apps/api/core';
import { listen } from '@tauri-apps/api/event';
import NoteList from './components/NoteList';
import NoteEditor from './components/NoteEditor';
import './styles/App.css';

interface Note {
    id: string;
    title: string;
    created_at: number;
    updated_at: number;
    tags: string[];
}

function App() {
    const [notes, setNotes] = useState<Note[]>([]);
    const [currentNote, setCurrentNote] = useState<Note | null>(null);
    const [content, setContent] = useState('');
    const [searchQuery, setSearchQuery] = useState('');

    // 加载笔记列表
    const loadNotes = useCallback(async () => {
        const notes = await invoke<Note[]>('get_notes');
        setNotes(notes);
    }, []);

    useEffect(() => {
        loadNotes();
    }, [loadNotes]);

    // 监听笔记保存事件
    useEffect(() => {
        const unlisten = listen<Note>('note-saved', (event) => {
            setNotes(prev => {
                const index = prev.findIndex(n => n.id === event.payload.id);
                if (index >= 0) {
                    const updated = [...prev];
                    updated[index] = event.payload;
                    return updated.sort((a, b) => b.updated_at - a.updated_at);
                }
                return prev;
            });
        });
        return () => { unlisten.then(fn => fn()); };
    }, []);

    // 选择笔记
    const selectNote = async (note: Note) => {
        setCurrentNote(note);
        const content = await invoke<string>('get_note_content', { id: note.id });
        setContent(content);
    };

    // 创建新笔记
    const createNote = async () => {
        const note = await invoke<Note>('create_note');
        setNotes(prev => [note, ...prev]);
        await selectNote(note);
    };

    // 删除笔记
    const deleteNote = async (id: string) => {
        await invoke('delete_note', { id });
        setNotes(prev => prev.filter(n => n.id !== id));
        if (currentNote?.id === id) {
            setCurrentNote(null);
            setContent('');
        }
    };

    // 保存笔记（防抖）
    const saveTimeoutRef = useRef<number>();
    const saveNote = useCallback((newContent: string) => {
        if (!currentNote) return;

        setContent(newContent);

        // 防抖：500ms 后保存
        if (saveTimeoutRef.current) {
            clearTimeout(saveTimeoutRef.current);
        }
        saveTimeoutRef.current = setTimeout(async () => {
            await invoke('save_note', {
                id: currentNote.id,
                content: newContent,
            });
        }, 500);
    }, [currentNote]);

    // 搜索
    const search = async (query: string) => {
        setSearchQuery(query);
        if (query.trim()) {
            const results = await invoke<Note[]>('search_notes', { query });
            setNotes(results);
        } else {
            loadNotes();
        }
    };

    return (
        <div className="app">
            <div className="sidebar">
                <div className="sidebar-header">
                    <h2>笔记</h2>
                    <button onClick={createNote}>+</button>
                </div>
                <input
                    type="text"
                    placeholder="搜索..."
                    value={searchQuery}
                    onChange={(e) => search(e.target.value)}
                    className="search-input"
                />
                <NoteList
                    notes={notes}
                    currentId={currentNote?.id}
                    onSelect={selectNote}
                    onDelete={deleteNote}
                />
            </div>
            <div className="main">
                {currentNote ? (
                    <NoteEditor
                        note={currentNote}
                        content={content}
                        onChange={saveNote}
                    />
                ) : (
                    <div className="empty-state">
                        <p>选择或创建一个笔记</p>
                    </div>
                )}
            </div>
        </div>
    );
}

export default App;
```

**笔记编辑器组件** `src/components/NoteEditor.tsx`：

```typescript
import { useState } from 'react';
import ReactMarkdown from 'react-markdown';

interface NoteEditorProps {
    note: Note;
    content: string;
    onChange: (content: string) => void;
}

function NoteEditor({ note, content, onChange }: NoteEditorProps) {
    const [mode, setMode] = useState<'edit' | 'preview'>('edit');

    return (
        <div className="note-editor">
            <div className="editor-header">
                <h1>{note.title}</h1>
                <div className="mode-switch">
                    <button
                        className={mode === 'edit' ? 'active' : ''}
                        onClick={() => setMode('edit')}
                    >
                        编辑
                    </button>
                    <button
                        className={mode === 'preview' ? 'active' : ''}
                        onClick={() => setMode('preview')}
                    >
                        预览
                    </button>
                </div>
            </div>
            {mode === 'edit' ? (
                <textarea
                    value={content}
                    onChange={(e) => onChange(e.target.value)}
                    className="editor-textarea"
                    placeholder="开始编辑..."
                />
            ) : (
                <div className="editor-preview">
                    <ReactMarkdown>{content}</ReactMarkdown>
                </div>
            )}
        </div>
    );
}

export default NoteEditor;
```

笔记应用架构：

```
┌─────────────────────────────────────────────────────────────┐
│                   笔记应用架构                              │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  前端 (React)                                       │   │
│  │                                                     │   │
│  │  ┌────────────┐  ┌──────────────────────────────┐   │   │
│  │  │ NoteList   │  │ NoteEditor                   │   │   │
│  │  │ (侧边栏)   │  │ ┌─────────┐ ┌─────────────┐ │   │   │
│  │  │            │  │ │ 编辑模式 │ │ 预览模式    │ │   │   │
│  │  │ - 笔记1    │  │ │ (textarea)│ │ (Markdown)  │ │   │   │
│  │  │ - 笔记2    │  │ └─────────┘ └─────────────┘ │   │   │
│  │  │ - 笔记3    │  │                              │   │   │
│  │  └────────────┘  └──────────────────────────────┘   │   │
│  └─────────────────────────────────────────────────────┘   │
│                      │ invoke                              │
│                      ▼                                     │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  后端 (Rust)                                        │   │
│  │                                                     │   │
│  │  ┌────────────┐  ┌─────────────┐  ┌────────────┐  │   │
│  │  │ commands   │  │ models      │  │ tray       │  │   │
│  │  │            │  │             │  │            │  │   │
│  │  │ get_notes  │  │ Note        │  │ 系统托盘   │  │   │
│  │  │ create_note│  │ AppState    │  │ 最小化     │  │   │
│  │  │ save_note  │  │             │  │            │  │   │
│  │  │ delete_note│  │             │  │            │  │   │
│  │  │ search     │  │             │  │            │  │   │
│  │  └────────────┘  └─────────────┘  └────────────┘  │   │
│  └─────────────────────────────────────────────────────┘   │
│                      │                                     │
│                      ▼                                     │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  存储                                               │   │
│  │                                                     │   │
│  │  ┌────────────────────┐  ┌──────────────────────┐   │   │
│  │  │ Store 插件         │  │ fs 插件              │   │   │
│  │  │ notes.json         │  │ $APPDATA/notes/      │   │   │
│  │  │ (元数据)           │  │ ├── uuid1.md         │   │   │
│  │  │ {                  │  │ ├── uuid2.md         │   │   │
│  │  │   "note:uuid1": {  │  │ └── uuid3.md         │   │   │
│  │  │     "title": "...",│  │ (笔记内容)           │   │   │
│  │  │     "created_at":  │  │                      │   │   │
│  │  │     ...            │  │                      │   │   │
│  │  │   }                │  │                      │   │   │
│  │  │ }                  │  │                      │   │   │
│  │  └────────────────────┘  └──────────────────────┘   │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 总结

- 笔记应用综合运用 IPC、状态管理、文件系统、窗口管理、插件等知识
- 数据分层：Store 存元数据，fs 存内容文件
- 防抖保存（500ms）避免频繁写文件，提升编辑流畅度
- 系统托盘 + 全局快捷键实现后台常驻 + 快速唤起
- **常见坑**：`AppState` 需用 `Mutex` 包装支持并发；`Store` 的 `set` 需配合 `save` 持久化；Markdown 预览需前端库（如 `react-markdown`）；防抖需在组件卸载时清理定时器

---

## 第 27 讲：性能优化

### 概念

Tauri 应用虽天生高效（Rust 后端 + 系统 WebView），但不当代码仍可能导致卡顿。常见性能问题：① IPC 调用过于频繁；② 大数据通过 IPC 传递；③ 前端重渲染；④ 后端阻塞。本讲介绍 Tauri 应用的性能优化技巧，涵盖前后端。

### 原理

**IPC 优化**：每次 `invoke` 都有 JSON 序列化 + 跨进程通信开销（约 0.1-1ms）。频繁调用（如每次按键）会累积。优化策略：① 批量操作——一次传递多个数据，减少调用次数；② 防抖——高频操作延迟执行；③ 缓存——前端缓存后端数据，避免重复请求；④ 大数据用文件——避免通过 IPC 传递大 `Vec` 或 `String`，改为写文件后传路径。

**前端优化**：React/Vue 的重渲染是性能瓶颈。优化策略：① `useMemo`/`useCallback` 缓存计算和回调；② `React.memo` 避免子组件重渲染；③ 虚拟列表（`react-window`/`react-virtualized`）处理大列表；④ 懒加载（`React.lazy`）分割代码。

**后端优化**：Rust 后端需避免阻塞 Tokio 运行时。优化策略：① 耗时操作用 `async`；② CPU 密集任务用 `tokio::task::spawn_blocking`；③ 文件 IO 用 `tokio::fs`；④ 数据库用连接池。

**构建优化**：`Cargo.toml` 的 `[profile.release]` 配置影响二进制大小和性能。`lto = true`（链接时优化）、`codegen-units = 1`（单线程编译，优化更好）、`strip = true`（移除调试符号）让 release 二进制更小更快。

### 例子

**IPC 优化**：

```rust
// ❌ 反例：频繁 IPC 调用
// 前端每次按键都调用
// await invoke('save_char', { char: 'a', position: 0 });

// ✅ 正例：批量 + 防抖
// 前端防抖 500ms，一次传递完整内容
// await invoke('save_note', { id, content });

// ===== 批量操作 =====
#[tauri::command]
async fn create_multiple_notes(
    app: AppHandle,
    notes: Vec<CreateNoteRequest>,  // 批量数据
) -> Result<Vec<Note>, String> {
    let mut created = Vec::new();
    for req in notes {
        let note = create_note_internal(&app, req).await?;
        created.push(note);
    }
    Ok(created)  // 一次返回所有结果
}

// ===== 大数据用文件 =====
#[tauri::command]
async fn export_notes(
    state: State<'_, Mutex<AppState>>,
    note_ids: Vec<String>,
) -> Result<String, String> {
    // 导出为文件，返回路径（而非通过 IPC 传递大内容）
    let export_path = state.lock().unwrap().notes_dir.join("export.json");
    let notes: Vec<_> = note_ids.iter()
        .filter_map(|id| {
            let path = state.lock().unwrap().notes_dir.join(format!("{}.md", id));
            std::fs::read_to_string(&path).ok()
        })
        .collect();

    let json = serde_json::to_string_pretty(&notes).map_err(|e| e.to_string())?;
    std::fs::write(&export_path, json).map_err(|e| e.to_string())?;

    Ok(export_path.to_string_lossy().to_string())  // 返回路径
}

// ===== CPU 密集任务用 spawn_blocking =====
#[tauri::command]
async fn search_notes_parallel(
    state: State<'_, Mutex<AppState>>,
    query: String,
) -> Result<Vec<Note>, String> {
    let notes_dir = state.lock().unwrap().notes_dir.clone();

    // CPU 密集的搜索放到阻塞线程池
    let results = tokio::task::spawn_blocking(move || {
        search_in_directory(&notes_dir, &query)
    }).await.map_err(|e| e.to_string())?;

    Ok(results)
}

fn search_in_directory(dir: &Path, query: &str) -> Vec<Note> {
    // 同步搜索逻辑
    vec![]
}
```

**前端优化**：

```typescript
import { useState, useMemo, useCallback, memo } from 'react';

// ===== useMemo 缓存计算 =====
function NoteList({ notes, searchQuery }: { notes: Note[]; searchQuery: string }) {
    const filteredNotes = useMemo(() => {
        if (!searchQuery) return notes;
        return notes.filter(note =>
            note.title.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }, [notes, searchQuery]);

    // ...
}

// ===== useCallback 缓存回调 =====
function App() {
    const [notes, setNotes] = useState<Note[]>([]);

    const handleSelect = useCallback((note: Note) => {
        // ...
    }, []);  // 空依赖，回调不变

    return <NoteList notes={notes} onSelect={handleSelect} />;
}

// ===== React.memo 避免重渲染 =====
const NoteItem = memo(({ note, onSelect }: { note: Note; onSelect: (note: Note) => void }) => {
    console.log(`渲染: ${note.title}`);
    return <div onClick={() => onSelect(note)}>{note.title}</div>;
});

// ===== 虚拟列表（大列表）=====
import { FixedSizeList as List } from 'react-window';

function VirtualNoteList({ notes }: { notes: Note[] }) {
    const Row = ({ index, style }: { index: number; style: React.CSSProperties }) => (
        <div style={style}>
            <NoteItem note={notes[index]} onSelect={handleSelect} />
        </div>
    );

    return (
        <List
            height={600}
            itemCount={notes.length}
            itemSize={50}
            width="100%"
        >
            {Row}
        </List>
    );
}

// ===== 防抖 Hook =====
function useDebounce<T>(value: T, delay: number): T {
    const [debounced, setDebounced] = useState(value);

    useEffect(() => {
        const timer = setTimeout(() => setDebounced(value), delay);
        return () => clearTimeout(timer);
    }, [value, delay]);

    return debounced;
}

// 使用
function SearchInput() {
    const [query, setQuery] = useState('');
    const debouncedQuery = useDebounce(query, 300);

    useEffect(() => {
        if (debouncedQuery) {
            search(debouncedQuery);
        }
    }, [debouncedQuery]);

    return <input value={query} onChange={e => setQuery(e.target.value)} />;
}
```

**构建优化** `Cargo.toml`：

```toml
[profile.release]
opt-level = 3
lto = true           # 链接时优化
codegen-units = 1    # 单线程编译，优化更好
strip = true         # 移除调试符号
panic = "abort"      # panic 时终止，减小体积

[profile.dev]
opt-level = 0
debug = true
```

性能优化检查清单：

```
┌─────────────────────────────────────────────────────────────┐
│                Tauri 性能优化检查清单                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  □ IPC 优化                                                 │
│    □ 批量操作，减少调用次数                                 │
│    □ 防抖高频操作（按键、滚动）                             │
│    □ 前端缓存后端数据                                       │
│    □ 大数据用文件传递路径                                   │
│                                                             │
│  □ 前端优化                                                 │
│    □ useMemo/useCallback 缓存                               │
│    □ React.memo 避免重渲染                                  │
│    □ 虚拟列表处理大列表                                     │
│    □ 懒加载分割代码                                         │
│    □ CSS 硬件加速 (transform, opacity)                      │
│                                                             │
│  □ 后端优化                                                 │
│    □ 耗时操作用 async                                       │
│    □ CPU 密集用 spawn_blocking                              │
│    □ 文件 IO 用 tokio::fs                                   │
│    □ 数据库用连接池                                         │
│    □ 避免在命令中持有锁过久                                 │
│                                                             │
│  □ 构建优化                                                 │
│    □ lto = true                                             │
│    □ codegen-units = 1                                      │
│    □ strip = true                                           │
│    □ panic = "abort"                                        │
│                                                             │
│  □ 资源优化                                                 │
│    □ 图片压缩                                               │
│    □ 字体子集化                                             │
│    □ Tree shaking 移除无用代码                              │
│    □ Code splitting 分割 bundle                             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 总结

- IPC 优化：批量操作、防抖、缓存、大数据用文件
- 前端优化：`useMemo`/`useCallback`/`React.memo`、虚拟列表、懒加载
- 后端优化：`async`、`spawn_blocking`、`tokio::fs`、连接池
- 构建优化：`lto`、`codegen-units = 1`、`strip`、`panic = "abort"`
- **常见坑**：频繁 IPC 调用累积开销；React 重渲染导致卡顿；阻塞 Tokio 运行时；release 二进制过大（用 strip）

---

## 第 28 讲：测试 Tauri 应用

### 概念

测试是保证应用质量的关键。Tauri 应用的测试分三层：① **Rust 单元测试**——测试后端命令逻辑；② **前端单元测试**——测试 React/Vue 组件；③ **端到端测试（E2E）**——测试完整应用流程。Tauri 2.x 推荐用 `tauri-driver` + WebDriverIO 进行 E2E 测试，模拟真实用户操作。

### 原理

**Rust 单元测试**用标准 `#[cfg(test)]` 模块，测试命令的业务逻辑。命令函数本身是普通 Rust 函数，可直接调用测试。需 mock `State`/`AppHandle` 等依赖，或重构逻辑为纯函数便于测试。

**前端单元测试**用 Vitest（Vite 推荐）或 Jest，测试组件渲染和交互。需 mock `@tauri-apps/api` 的 `invoke`/`listen` 等，避免真实 IPC 调用。`vi.mock('@tauri-apps/api/core')` 模拟 `invoke` 返回测试数据。

**E2E 测试**用 `tauri-driver`（基于 WebDriver 协议）控制 Tauri 应用。`tauri-driver` 启动一个 WebDriver 服务器，测试框架（WebDriverIO）通过协议控制应用——点击、输入、断言 UI。这种测试最接近真实用户体验，但设置复杂、运行慢。

### 例子

**Rust 单元测试**：

```rust
// src-tauri/src/commands.rs

#[tauri::command]
pub async fn create_note(
    app: AppHandle,
    state: State<'_, Mutex<AppState>>,
) -> Result<Note, String> {
    create_note_internal(&app, &state.lock().unwrap()).await
}

// 重构为可测试的纯函数
pub async fn create_note_internal(
    app: &AppHandle,
    state: &AppState,
) -> Result<Note, String> {
    let id = Uuid::new_v4().to_string();
    let now = current_timestamp();

    let note = Note {
        id: id.clone(),
        title: "新笔记".to_string(),
        created_at: now,
        updated_at: now,
        tags: vec![],
    };

    let note_path = state.notes_dir.join(format!("{}.md", id));
    std::fs::write(&note_path, "# 新笔记\n\n开始编辑...").map_err(|e| e.to_string())?;

    Ok(note)
}

pub fn extract_title(content: &str) -> String {
    content.lines()
        .next()
        .and_then(|line| line.strip_prefix("# ").map(|s| s.to_string()))
        .unwrap_or("无标题".to_string())
}

pub fn current_timestamp() -> u64 {
    std::time::SystemTime::now()
        .duration_since(std::time::UNIX_EPOCH)
        .unwrap()
        .as_secs()
}

// ===== 测试模块 =====
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_extract_title() {
        assert_eq!(extract_title("# 标题\n内容"), "标题");
        assert_eq!(extract_title("无标题行"), "无标题");
        assert_eq!(extract_title(""), "无标题");
    }

    #[test]
    fn test_extract_title_with_multiple_lines() {
        let content = "# 第一行\n第二行\n第三行";
        assert_eq!(extract_title(content), "第一行");
    }

    #[test]
    fn test_current_timestamp() {
        let t1 = current_timestamp();
        std::thread::sleep(std::time::Duration::from_secs(1));
        let t2 = current_timestamp();
        assert!(t2 > t1);
    }

    use tempfile::TempDir;

    #[tokio::test]
    async fn test_create_note() {
        let temp_dir = TempDir::new().unwrap();
        let state = AppState {
            notes_dir: temp_dir.path().to_path_buf(),
            current_note_id: None,
        };

        // 注意：这里需要 mock AppHandle，实际中可能需要更复杂的设置
        // 或者进一步重构 create_note_internal 不依赖 AppHandle
        let note = Note {
            id: "test-id".to_string(),
            title: "新笔记".to_string(),
            created_at: current_timestamp(),
            updated_at: current_timestamp(),
            tags: vec![],
        };

        let note_path = state.notes_dir.join("test-id.md");
        std::fs::write(&note_path, "# 新笔记").unwrap();

        assert!(note_path.exists());
        let content = std::fs::read_to_string(&note_path).unwrap();
        assert!(content.contains("新笔记"));
    }
}
```

**前端单元测试**（Vitest）：

```typescript
// src/hooks/useNotes.test.ts
import { describe, it, expect, vi, beforeEach } from 'vitest';
import { renderHook, act } from '@testing-library/react';
import { invoke } from '@tauri-apps/api/core';

// Mock Tauri API
vi.mock('@tauri-apps/api/core', () => ({
    invoke: vi.fn(),
}));

vi.mock('@tauri-apps/api/event', () => ({
    listen: vi.fn(() => Promise.resolve(() => {})),
}));

import { useNotes } from './useNotes';

describe('useNotes', () => {
    beforeEach(() => {
        vi.clearAllMocks();
    });

    it('should load notes on mount', async () => {
        const mockNotes = [
            { id: '1', title: '笔记 1', created_at: 0, updated_at: 0, tags: [] },
            { id: '2', title: '笔记 2', created_at: 0, updated_at: 0, tags: [] },
        ];
        (invoke as any).mockResolvedValue(mockNotes);

        const { result } = renderHook(() => useNotes());

        await act(async () => {
            await new Promise(resolve => setTimeout(resolve, 0));
        });

        expect(result.current.notes).toEqual(mockNotes);
        expect(invoke).toHaveBeenCalledWith('get_notes');
    });

    it('should create a new note', async () => {
        const newNote = { id: '3', title: '新笔记', created_at: 0, updated_at: 0, tags: [] };
        (invoke as any).mockResolvedValue(newNote);

        const { result } = renderHook(() => useNotes());

        await act(async () => {
            await result.current.createNote();
        });

        expect(result.current.notes).toContainEqual(newNote);
        expect(invoke).toHaveBeenCalledWith('create_note');
    });

    it('should handle errors', async () => {
        (invoke as any).mockRejectedValue('加载失败');

        const { result } = renderHook(() => useNotes());

        await act(async () => {
            await new Promise(resolve => setTimeout(resolve, 0));
        });

        expect(result.current.error).toBe('加载失败');
    });
});
```

**E2E 测试**（WebDriverIO + tauri-driver）：

```typescript
// e2e/notes.spec.ts
import { expect } from '@wdio/globals';
import { browser } from '@wdio/globals';

describe('Notes App', () => {
    beforeEach(async () => {
        await browser.url('http://localhost:1420');
    });

    it('should create a new note', async () => {
        // 点击创建按钮
        const createButton = await $('.sidebar-header button');
        await createButton.click();

        // 等待编辑器出现
        const editor = await $('.editor-textarea');
        await editor.waitForDisplayed({ timeout: 5000 });

        // 输入内容
        await editor.setValue('# 测试笔记\n\n这是测试内容');

        // 等待自动保存
        await browser.pause(1000);

        // 验证笔记出现在列表
        const noteTitle = await $('.note-item .title');
        const titleText = await noteTitle.getText();
        expect(titleText).toContain('测试笔记');
    });

    it('should search notes', async () => {
        // 输入搜索词
        const searchInput = await $('.search-input');
        await searchInput.setValue('测试');

        // 等待搜索结果
        await browser.pause(500);

        // 验证结果
        const noteItems = await $$('.note-item');
        expect(noteItems.length).toBeGreaterThan(0);
    });

    it('should delete a note', async () => {
        // 获取初始笔记数
        const initialNotes = await $$('.note-item');

        // 点击删除按钮
        const deleteButton = await $('.note-item .delete-button');
        await deleteButton.click();

        // 确认删除（如果有对话框）
        // await browser.acceptAlert();

        // 等待删除完成
        await browser.pause(500);

        // 验证笔记数减少
        const finalNotes = await $$('.note-item');
        expect(finalNotes.length).toBe(initialNotes.length - 1);
    });

    it('should toggle edit/preview mode', async () => {
        // 选择笔记
        const noteItem = await $('.note-item');
        await noteItem.click();

        // 切换到预览模式
        const previewButton = await $('.mode-switch button:nth-child(2)');
        await previewButton.click();

        // 验证预览显示
        const preview = await $('.editor-preview');
        await preview.waitForDisplayed();

        // 切换回编辑模式
        const editButton = await $('.mode-switch button:nth-child(1)');
        await editButton.click();

        // 验证编辑器显示
        const editor = await $('.editor-textarea');
        await editor.waitForDisplayed();
    });
});
```

**E2E 测试配置** `wdio.conf.ts`：

```typescript
import type { Options } from '@wdio/types';

export const config: Options.Testrunner = {
    runner: 'local',
    specs: ['./e2e/**/*.spec.ts'],
    capabilities: [{
        browserName: 'wry',  // Tauri 的 WebView
        'tauri:options': {
            application: '../src-tauri/target/release/notes-app',
        },
    }],
    services: [
        ['tauri', {
            application: '../src-tauri/target/release/notes-app',
        }],
    ],
    framework: 'mocha',
    mochaOpts: {
        ui: 'bdd',
        timeout: 60000,
    },
    reporters: ['spec'],
};
```

测试策略：

```
┌─────────────────────────────────────────────────────────────┐
│                Tauri 测试策略金字塔                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│                    ╱╲                                       │
│                   ╱  ╲     E2E 测试 (少)                    │
│                  ╱ E2E ╲   完整用户流程                      │
│                 ╱──────╲    WebDriverIO + tauri-driver      │
│                ╱        ╲                                   │
│               ╱ 前端测试  ╲  中等数量                        │
│              ╱ (Vitest)    ╲ 组件渲染和交互                  │
│             ╱──────────────╲                                │
│            ╱                ╲                               │
│           ╱   Rust 单元测试   ╲ 大量                         │
│          ╱   (cargo test)     ╲ 业务逻辑                    │
│         ╱──────────────────────╲                            │
│                                                             │
│  原则:                                                       │
│  1. 业务逻辑放后端，Rust 单元测试覆盖                       │
│  2. 前端测试 mock Tauri API，测组件逻辑                     │
│  3. E2E 测试覆盖核心用户流程（创建、编辑、删除）            │
│  4. CI 中运行所有测试                                       │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 总结

- Rust 单元测试用 `#[cfg(test)]`，测试命令的业务逻辑（重构为纯函数）
- 前端单元测试用 Vitest，mock `@tauri-apps/api` 避免真实 IPC
- E2E 测试用 `tauri-driver` + WebDriverIO，模拟真实用户操作
- 测试金字塔：大量 Rust 单元测试 + 中等前端测试 + 少量 E2E
- **常见坑**：命令中的 `State`/`AppHandle` 难 mock，需重构为纯函数；E2E 测试需 release 二进制；前端测试需 mock 所有 Tauri API；E2E 测试慢，只覆盖核心流程

---

## 第 29 讲：发布与部署

### 概念

完成开发和测试后，最后一步是**发布与部署**——将 Tauri 应用打包为各平台安装包，分发给用户。这涉及：① 构建优化（release 模式）；② 平台打包（macOS .dmg、Windows .msi、Linux .deb/.AppImage）；③ 代码签名（macOS 公证、Windows 签名）；④ 自动更新配置；⑤ CI/CD 自动化。本讲介绍 Tauri 应用的完整发布流程。

### 原理

**构建流程**：`pnpm tauri build` 执行三步——① `beforeBuildCommand`（如 `pnpm build`）生成前端静态资源；② `cargo build --release` 编译 Rust 后端，嵌入前端资源；③ `tauri-build` 打包为平台安装包。`tauri.conf.json` 的 `bundle.targets` 控制生成哪些格式（`"all"` 或指定 `["deb", "appimage", "msi", "nsis", "app", "dmg"]`）。

**平台打包格式**：macOS 生成 `.app`（应用 bundle）和 `.dmg`（磁盘镜像安装包）；Windows 生成 `.exe`（NSIS 安装器）和 `.msi`（Windows Installer）；Linux 生成 `.deb`（Debian 包）、`.rpm`（Red Hat 包）、`.AppImage`（便携式镜像）。每种格式有特定配置（如 NSIS 的安装模式、deb 的依赖）。

**代码签名**：macOS 应用需"公证"（notarize）才能分发，否则用户看到"无法打开"警告；Windows 应用建议代码签名，避免 SmartScreen 警告。macOS 需 Apple Developer 账号，用 `codesign` 签名 + `xcrun notarytool` 公证；Windows 需代码签名证书，用 `signtool` 签名。

**CI/CD**：用 GitHub Actions 自动构建多平台安装包。`tauri-apps/tauri-action` 是官方 Action，封装了构建、签名、发布流程。配合 GitHub Releases，推送 tag 自动触发构建并上传安装包。

### 例子

**`tauri.conf.json` 发布配置**：

```json
{
  "productName": "笔记应用",
  "version": "1.0.0",
  "identifier": "com.example.notes-app",

  "build": {
    "frontendDist": "../dist",
    "beforeBuildCommand": "pnpm build"
  },

  "bundle": {
    "active": true,
    "targets": "all",
    "icon": [
      "icons/32x32.png",
      "icons/128x128.png",
      "icons/[email protected]",
      "icons/icon.icns",
      "icons/icon.ico"
    ],
    "resources": ["resources/*"],
    "copyright": "© 2024 笔记应用",
    "category": "Productivity",
    "shortDescription": "一个 Markdown 笔记应用",
    "longDescription": "一个用 Tauri 构建的跨平台 Markdown 笔记应用",

    "appimage": {
      "bundleMediaFramework": true
    },
    "deb": {
      "depends": ["libwebkit2gtk-4.1-0", "libgtk-3-0"]
    },
    "macOS": {
      "minimumSystemVersion": "10.15",
      "signingIdentity": "Developer ID Application: Your Name (XXXXXXXXXX)",
      "entitlements": "entitlements.plist"
    },
    "windows": {
      "nsis": {
        "installerIcon": "icons/icon.ico",
        "installMode": "perMachine",
        "languages": ["en-US", "zh-CN"]
      },
      "wix": {
        "language": ["en-US", "zh-CN"]
      },
      "certificateThumbprint": null,
      "digestAlgorithm": "sha256",
      "timestampUrl": "http://timestamp.sectigo.com"
    },
    "createUpdaterArtifacts": true
  },

  "plugins": {
    "updater": {
      "active": true,
      "endpoints": [
        "https://github.com/example/notes-app/releases/latest/download/latest.json"
      ],
      "pubkey": "公钥内容..."
    }
  }
}
```

**macOS 公证配置** `entitlements.plist`：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>com.apple.security.cs.allow-jit</key>
    <true/>
    <key>com.apple.security.cs.allow-unsigned-executable-memory</key>
    <true/>
    <key>com.apple.security.cs.disable-library-validation</key>
    <true/>
    <key>com.apple.security.network.client</key>
    <true/>
    <key>com.apple.security.files.user-selected.read-write</key>
    <true/>
</dict>
</plist>
```

**构建命令**：

```bash
# ===== 开发构建 =====
pnpm tauri build

# ===== 指定平台 =====
pnpm tauri build --target x86_64-apple-darwin      # macOS Intel
pnpm tauri build --target aarch64-apple-darwin     # macOS Apple Silicon
pnpm tauri build --target x86_64-pc-windows-msvc   # Windows
pnpm tauri build --target x86_64-unknown-linux-gnu # Linux

# ===== 指定打包格式 =====
pnpm tauri build --bundles dmg      # 只生成 macOS dmg
pnpm tauri build --bundles nsis     # 只生成 Windows NSIS
pnpm tauri build --bundles deb      # 只生成 Linux deb
pnpm tauri build --bundles appimage # 只生成 Linux AppImage

# ===== 调试构建 =====
pnpm tauri build --debug

# ===== 生成更新签名 =====
# 需设置环境变量:
# TAURI_SIGNING_PRIVATE_KEY=私钥内容
# TAURI_SIGNING_PRIVATE_KEY_PASSWORD=密码
```

**GitHub Actions CI/CD** `.github/workflows/release.yml`：

```yaml
name: Release

on:
  push:
    tags:
      - 'v*'
  workflow_dispatch:

jobs:
  create-release:
    runs-on: ubuntu-latest
    outputs:
      release_id: ${{ steps.create-release.outputs.id }}
    steps:
      - uses: actions/checkout@v3
      - name: Create release
        id: create-release
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: ${{ github.ref_name }}
          release_name: 'Notes App v__VERSION__'
          release_body: 'See the assets to download this version and install.'
          release_draft: true
          prerelease: false

  build-tauri:
    needs: create-release
    strategy:
      fail-fast: false
      matrix:
        include:
          - platform: macos-latest
            args: '--target aarch64-apple-darwin'
          - platform: macos-latest
            args: '--target x86_64-apple-darwin'
          - platform: ubuntu-22.04
            args: ''
          - platform: windows-latest
            args: ''

    runs-on: ${{ matrix.platform }}
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: 20

      - name: Setup pnpm
        uses: pnpm/action-setup@v2
        with:
          version: 8

      - name: Install dependencies (Linux)
        if: matrix.platform == 'ubuntu-22.04'
        run: |
          sudo apt-get update
          sudo apt-get install -y libwebkit2gtk-4.1-dev libappindicator3-dev librsvg2-dev patchelf

      - name: Install Rust
        uses: dtolnay/rust-toolchain@stable
        with:
          targets: ${{ matrix.platform == 'macos-latest' && 'aarch64-apple-darwin,x86_64-apple-darwin' || '' }}

      - name: Rust cache
        uses: swatinem/rust-cache@v2
        with:
          workspaces: './src-tauri -> target'

      - name: Install frontend dependencies
        run: pnpm install

      - uses: tauri-apps/tauri-action@v0
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          # macOS 签名
          APPLE_CERTIFICATE: ${{ secrets.APPLE_CERTIFICATE }}
          APPLE_CERTIFICATE_PASSWORD: ${{ secrets.APPLE_CERTIFICATE_PASSWORD }}
          APPLE_SIGNING_IDENTITY: ${{ secrets.APPLE_SIGNING_IDENTITY }}
          APPLE_ID: ${{ secrets.APPLE_ID }}
          APPLE_PASSWORD: ${{ secrets.APPLE_PASSWORD }}
          APPLE_TEAM_ID: ${{ secrets.APPLE_TEAM_ID }}
          # Windows 签名
          TAURI_SIGNING_PRIVATE_KEY: ${{ secrets.TAURI_SIGNING_PRIVATE_KEY }}
          TAURI_SIGNING_PRIVATE_KEY_PASSWORD: ${{ secrets.TAURI_SIGNING_PRIVATE_KEY_PASSWORD }}
        with:
          releaseId: ${{ needs.create-release.outputs.release_id }}
          args: ${{ matrix.args }}

  publish-release:
    needs: [create-release, build-tauri]
    runs-on: ubuntu-latest
    steps:
      - name: Publish release
        uses: actions/github-script@v6
        env:
          release_id: ${{ needs.create-release.outputs.release_id }}
        with:
          script: |
            github.rest.repos.updateRelease({
              owner: context.repo.owner,
              repo: context.repo.repo,
              release_id: process.env.release_id,
              draft: false,
              prerelease: false
            })
```

**发布检查清单**：

```
┌─────────────────────────────────────────────────────────────┐
│                Tauri 应用发布检查清单                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  □ 构建优化                                                 │
│    □ Cargo.toml [profile.release] 配置                      │
│      - lto = true                                           │
│      - codegen-units = 1                                    │
│      - strip = true                                         │
│      - panic = "abort"                                      │
│    □ 前端构建优化（Tree shaking, minify）                   │
│    □ 测试 release 版本功能正常                              │
│                                                             │
│  □ 应用配置                                                 │
│    □ productName, version, identifier 正确                  │
│    □ 图标完整（各尺寸 PNG + icns + ico）                    │
│    □ copyright, description 填写                            │
│    □ bundle.targets 配置正确                                │
│                                                             │
│  □ 平台特定                                                 │
│    □ macOS: minimumSystemVersion, entitlements              │
│    □ Windows: NSIS/WiX 配置, 安装模式                       │
│    □ Linux: deb 依赖, AppImage 配置                         │
│                                                             │
│  □ 代码签名                                                 │
│    □ macOS: Apple Developer 证书 + 公证                    │
│    □ Windows: 代码签名证书                                  │
│    □ 环境变量配置 (APPLE_*, TAURI_SIGNING_*)                │
│                                                             │
│  □ 自动更新                                                 │
│    □ 生成签名密钥对                                         │
│    □ 配置 updater endpoints                                 │
│    □ pubkey 配置在 tauri.conf.json                          │
│    □ 测试更新流程                                           │
│                                                             │
│  □ CI/CD                                                    │
│    □ GitHub Actions 多平台矩阵构建                          │
│    □ Secrets 配置（签名密钥、token）                        │
│    □ tag 触发自动构建发布                                   │
│    □ GitHub Releases 上传安装包                             │
│                                                             │
│  □ 分发                                                     │
│    □ GitHub Releases 提供下载                               │
│    □ 官网下载页面                                           │
│    □ 应用商店（可选：Mac App Store, Microsoft Store）       │
│    □ 用户文档/README                                       │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 总结

- `pnpm tauri build` 三步构建：前端打包 → Rust 编译 → 平台打包
- 平台格式：macOS `.dmg`、Windows `.msi`/`.exe`、Linux `.deb`/`.AppImage`
- 代码签名：macOS 公证 + Windows 签名，避免用户安全警告
- GitHub Actions + `tauri-action` 实现多平台自动构建发布
- **常见坑**：`identifier` 必须是反向域名；图标需包含所有尺寸；macOS 需 Apple Developer 账号公证；Windows 签名证书需付费；Linux 需安装 `libwebkit2gtk-4.1` 等依赖

---

## 课程结语

恭喜你完成了 Tauri 系统化教程的全部 29 讲！让我们回顾这段学习旅程：

**第 1-2 章** 建立了 Tauri 的基础认知：从设计哲学、环境搭建、第一个应用，到核心架构、配置系统、生命周期、前端集成。你理解了 Tauri 与 Electron 的本质区别——系统 WebView + Rust 后端带来的小体积、低内存、高性能。

**第 3-4 章** 深入了前后端通信与系统集成：命令系统、事件系统、状态管理、错误处理，以及窗口管理、系统托盘、菜单、对话框、剪贴板。这些让你构建出功能丰富、交互完整的桌面应用。

**第 5-6 章** 掌握了插件生态与进阶能力：文件系统、数据库、自定义插件，以及自动更新、全局快捷键、通知、深度链接、安全策略。这些让应用具备生产级能力与安全保障。

**第 7 章** 将所学应用于工程实践：笔记应用实战、性能优化、测试、发布部署。你已具备完整的 Tauri 工程能力。

Tauri 的学习曲线相对平缓（有 Rust 和前端基础的话），但"前后端协作"的思维模式需要适应——何时用命令、何时用事件、状态放哪端、如何保证安全。当你习惯了这种"Rust 后端 + Web 前端 + IPC 桥梁"的架构后，会发现 Tauri 是构建跨平台桌面应用的优雅方案。

继续前进的方向：
- **阅读 Tauri 源码**：理解 Wry/Tao 的底层实现，学习跨平台封装技巧
- **贡献 Tauri 生态**：开发并发布自定义插件，丰富社区
- **构建自己的应用**：用 Tauri 实现你需要的工具——笔记、文件管理器、数据库客户端、设计工具等
- **探索移动端**：Tauri 2.x 支持 iOS/Android，尝试跨平台移动应用开发
- **关注 Tauri 演进**：Tauri 仍在快速迭代，关注官方博客和更新

Tauri 的官方资源：
- [Tauri 官网](https://tauri.app)：文档与教程
- [Tauri GitHub](https://github.com/tauri-apps/tauri)：源码与示例
- [Tauri 插件](https://github.com/tauri-apps/plugins-workspace)：官方插件集
- [Awesome Tauri](https://github.com/tauri-apps/awesome-tauri)：社区资源汇总

愿你在 Tauri 的世界里，构建出既轻量又强大的桌面应用，享受 Rust + Web 融合开发的乐趣。Happy Tauri-ing!






