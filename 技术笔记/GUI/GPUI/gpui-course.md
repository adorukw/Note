# GPUI 系统化教程：从入门到实战

> 本教程以"教科书式"的讲解方式，按"概念 → 原理 → 例子 → 总结"四段式结构，循序渐进地讲解 GPUI——Zed 编辑器背后的 Rust GUI 框架。每讲聚焦一个核心知识点，配合可运行代码示例，帮助读者建立完整的 GPUI 知识体系。

---

## 课程总览

- **预计讲数**：25 讲（6 章）
- **学习目标**：
  1. 理解 GPUI 的设计哲学与核心架构
  2. 掌握 View、Element、Context 三大基石
  3. 熟练使用 Flex 布局与样式系统构建界面
  4. 掌握 Model 状态管理与观察者模式
  5. 处理鼠标、键盘、焦点、拖拽等交互事件
  6. 具备独立开发 GPUI 应用的能力
- **适用读者**：有 Rust 基础（建议先学完 Rust 系统教程），希望学习现代 GPU 加速 GUI 开发的开发者
- **学习建议**：按章节顺序学习，每讲先读"概念"和"原理"建立认知，再动手运行"例子"中的代码，最后用"总结"回顾要点。GPUI 仍在快速演进，API 可能变化，请以官方仓库为准

---

## 详细章节目录

### 第 1 章：GPUI 入门基础
- 第 1 讲：GPUI 简介与设计哲学
- 第 2 讲：环境搭建与第一个 GPUI 应用
- 第 3 讲：应用生命周期与窗口创建
- 第 4 讲：GPUI 核心概念总览

### 第 2 章：视图与元素系统
- 第 5 讲：View 与 Render trait
- 第 6 讲：Element 基础与 div 元素
- 第 7 讲：布局系统：Flex 布局
- 第 8 讲：样式系统：颜色、字体、间距
- 第 9 讲：交互元素与事件处理

### 第 3 章：状态管理
- 第 10 讲：Model 与状态管理
- 第 11 讲：Context 与观察者模式
- 第 12 讲：Entity 订阅与通知
- 第 13 讲：全局状态与 AppContext

### 第 4 章：事件与交互
- 第 14 讲：鼠标事件处理
- 第 15 讲：键盘事件与快捷键
- 第 16 讲：焦点管理
- 第 17 讲：拖拽与手势

### 第 5 章：高级 UI
- 第 18 讲：动画系统
- 第 19 讲：主题与样式系统
- 第 20 讲：SVG 与图标
- 第 21 讲：自定义 Element

### 第 6 章：实战与工程化
- 第 22 讲：构建 TodoMVC 应用
- 第 23 讲：性能优化
- 第 24 讲：测试 GPUI 应用
- 第 25 讲：发布与部署

---

# 第 1 章：GPUI 入门基础

本章带你走进 GPUI 的世界。我们将从 GPUI 的设计哲学讲起，搭建开发环境，编写第一个 GPUI 应用，并学习 GPUI 的核心概念。GPUI 是一个相对独特的 GUI 框架，理解它的设计理念比死记 API 更重要。

## 第 1 讲：GPUI 简介与设计哲学

### 概念

**GPUI** 是 Zed Industries 开发的 Rust GUI 框架，专为构建高性能桌面应用设计。它首次应用于 Zed 编辑器——一个号称"世界上最快的文本编辑器"的 IDE。GPUI 的核心特征是：① **Rust 原生**——完全用 Rust 编写，无 C++/JS 中间层；② **GPU 加速**——所有渲染通过 GPU 完成，达到原生性能；③ **保留模式 + 即时风格**——结合了保留模式（retained mode）的性能优势和即时模式（immediate mode）的开发便利性；④ **跨平台**——支持 macOS、Linux、Windows。

### 原理

传统 GUI 框架分两大流派：**保留模式**（如 Qt、GTK、WinForms）维护一棵持久的控件树，状态变化时更新对应节点；**即时模式**（如 Dear ImGui）每帧重新构建整个 UI，简单但性能开销大。GPUI 采用独特的混合模式——**保留的视图树 + 即时的元素树**。

具体来说，GPUI 维护一棵持久的 View 树（视图树），每个 View 持有自己的状态；但每次渲染时，View 的 `render` 方法返回一棵临时的 Element 树（元素树），GPUI 将其与上一帧的 Element 树做 diff，只更新变化的部分。这种设计既避免了即时模式每帧重建状态的开销，又保留了"声明式 UI"的开发便利性。渲染层使用平台原生 GPU API（macOS 用 Metal，Linux 用 Vulkan/OpenGL，Windows 用 DirectX/Metal），通过自研的着色器实现文本、图形、图像的高效绘制。

GPUI 的另一个核心理念是**类型安全的状态管理**。状态被封装在 `Model<T>` 中，通过 `Context` 访问和修改，编译器保证状态访问的安全性。事件处理通过闭包捕获 `View` 引用，避免了传统 GUI 框架中常见的"回调地狱"和"空指针崩溃"。

### 例子

GPUI 与其他 GUI 框架的对比：

```
┌─────────────────┬──────────────────┬──────────────────┬──────────────────┐
│     特性        │   保留模式(Qt)   │  即时模式(ImGui) │   GPUI(混合)     │
├─────────────────┼──────────────────┼──────────────────┼──────────────────┤
│ 状态管理        │ 控件树持久保存   │ 每帧重建         │ View 持久+Element│
│                 │                  │                  │ 即时             │
│ 渲染性能        │ 高(增量更新)     │ 中(全量重绘)     │ 高(Element diff) │
│ 开发便利性      │ 中(需手动同步)   │ 高(声明式)       │ 高(声明式)       │
│ 内存占用        │ 中               │ 低               │ 中               │
│ 类型安全        │ 弱(C++ 信号槽)   │ 弱(C 风格)       │ 强(Rust 类型系统)│
│ GPU 加速        │ 可选             │ 是               │ 是               │
└─────────────────┴──────────────────┴──────────────────┴──────────────────┘
```

GPUI 的架构层次：

```
┌─────────────────────────────────────────────┐
│           应用层 (Your App)                 │
│     View / Model / 事件处理 / 业务逻辑      │
├─────────────────────────────────────────────┤
│           GPUI 框架层                       │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  │
│  │ View 系统│  │ Element  │  │ 状态管理 │  │
│  │          │  │   系统   │  │ (Model)  │  │
│  └──────────┘  └──────────┘  └──────────┘  │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  │
│  │ 事件系统 │  │ 布局系统 │  │ 样式系统 │  │
│  └──────────┘  └──────────┘  └──────────┘  │
├─────────────────────────────────────────────┤
│           平台抽象层 (Platform)             │
│   macOS(Metal) / Linux(Vulkan) / Win(DX)   │
├─────────────────────────────────────────────┤
│           GPU 硬件层                        │
└─────────────────────────────────────────────┘
```

一个最简 GPUI 应用的"骨架"预览（下一讲会详细讲解）：

```rust
use gpui::*;

fn main() {
    Application::new().run(|cx: &mut App| {
        cx.open_window(WindowOptions::default(), |_window, cx| {
            cx.new(|_cx| {
                // 这里返回一个 RootView
                // 下一讲会完整实现
            })
        });
    });
}
```

### 总结

- GPUI 是 Zed 编辑器的 GUI 框架，Rust 原生、GPU 加速、跨平台
- 采用"保留视图树 + 即时元素树"的混合模式，兼顾性能与开发便利
- 渲染层使用平台原生 GPU API（Metal/Vulkan/DirectX）
- 类型安全的状态管理通过 `Model<T>` 和 `Context` 实现
- **常见坑**：GPUI 仍在快速演进，API 可能跨版本变化；不要用传统保留模式的思维理解 GPUI，它的 `render` 方法是每帧调用的；GPUI 不是 immediate mode GUI，状态不会每帧重建

---

## 第 2 讲：环境搭建与第一个 GPUI 应用

### 概念

本讲搭建 GPUI 开发环境并编写第一个应用。GPUI 目前未发布到 crates.io，需从 Zed 仓库或 GitHub 引用。开发环境需要：Rust 工具链（推荐最新 stable）、平台依赖（macOS 需 Xcode Command Line Tools，Linux 需图形驱动和 Vulkan 开发包，Windows 需 Visual Studio Build Tools）。第一个应用将创建一个窗口并显示"Hello, GPUI!"文本。

### 原理

GPUI 的依赖关系较复杂，因为它需要链接平台原生 GPU API。在 `Cargo.toml` 中通过 git 依赖引入 GPUI：

```toml
[dependencies]
gpui = { git = "https://github.com/zed-industries/zed" }
```

GPUI 应用的入口是 `Application::new().run(|cx| {...})`，`run` 接收一个闭包，闭包参数 `cx: &mut App` 是应用上下文。通过 `cx.open_window()` 创建窗口，窗口需要一个 `WindowOptions`（配置标题、大小等）和一个返回根视图的闭包。根视图是一个实现了 `Render` trait 的类型，其 `render` 方法返回要显示的元素树。

GPUI 的编译期较慢（因为依赖较多且使用了大量泛型），首次编译可能需要几分钟。建议开启 `lld` 链接器和 `sccache` 加速构建。运行时会创建一个原生窗口，所有渲染通过 GPU 完成，文本使用系统字体或自带的字体栈。

### 例子

**步骤 1：创建项目**

```bash
cargo new hello-gpui
cd hello-gpui
```

**步骤 2：配置 `Cargo.toml`**

```toml
[package]
name = "hello-gpui"
version = "0.1.0"
edition = "2021"

[dependencies]
gpui = { git = "https://github.com/zed-industries/zed" }
```

**步骤 3：编写 `src/main.rs`**

```rust
use gpui::*;

// 定义根视图——任何实现了 Render 的类型都可以作为视图
struct HelloWorld {
    count: usize,
}

// Render trait 是 GPUI 视图的核心
impl Render for HelloWorld {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .flex()
            .flex_col()
            .size_full()
            .items_center()
            .justify_center()
            .bg(rgb(0x1e1e2e))
            .text_color(rgb(0xcdd6f4))
            .child(
                div()
                    .text_xl()
                    .child(format!("Hello, GPUI! (点击次数: {})", self.count)),
            )
            .child(
                div()
                    .mt_4()
                    .px_4()
                    .py_2()
                    .bg(rgb(0x89b4fa))
                    .text_color(rgb(0x1e1e2e))
                    .rounded_md()
                    .cursor_pointer()
                    .child("点击我")
                    .on_click(cx.listener(|this, _event, _window, cx| {
                        this.count += 1;
                        cx.notify();
                    })),
            )
    }
}

fn main() {
    Application::new().run(|cx: &mut App| {
        cx.open_window(
            WindowOptions {
                window_bounds: Some(WindowBounds::Windowed(Bounds {
                    origin: Default::default(),
                    size: size(px(800.), px(600.)),
                })),
                titlebar: Some(TitlebarOptions {
                    title: Some("Hello GPUI".into()),
                    appears_transparent: false,
                    traffic_light_position: None,
                }),
                ..Default::default()
            },
            |_window, cx| {
                cx.new(|_cx| HelloWorld { count: 0 })
            },
        )
        .unwrap();

        cx.activate(true);
    });
}
```

**步骤 4：运行**

```bash
cargo run
```

运行后会弹出一个 800×600 的窗口，深色背景，居中显示"Hello, GPUI! (点击次数: 0)"和一个蓝色按钮。点击按钮，计数器递增。

**代码解析**：

```
Application::new().run(...)        ← 应用入口
    │
    ▼
cx.open_window(options, closure)   ← 创建窗口
    │
    ▼
cx.new(|_cx| HelloWorld { ... })   ← 创建根视图（Model）
    │
    ▼
impl Render for HelloWorld         ← 视图渲染逻辑
    │
    ▼
render() -> div().child(...).child(...)   ← 返回元素树
    │
    ▼
.on_click(cx.listener(...))        ← 事件绑定
    │
    ▼
this.count += 1; cx.notify();      ← 修改状态 + 通知重绘
```

### 总结

- GPUI 通过 git 依赖引入，需 Rust 工具链和平台 GPU 依赖
- 应用入口：`Application::new().run(|cx| { cx.open_window(...); })`
- 根视图需实现 `Render` trait，`render` 方法返回元素树
- `cx.new(|cx| ViewType { ... })` 创建视图实例
- `cx.listener(|this, event, window, cx| {...})` 绑定事件，闭包内修改状态后调用 `cx.notify()` 触发重绘
- **常见坑**：首次编译很慢（依赖多）；Linux 需安装 Vulkan 开发包；`cx.notify()` 必须调用否则界面不更新；`Render` 的 `render` 方法签名随版本可能变化

---

## 第 3 讲：应用生命周期与窗口创建

### 概念

GPUI 应用有明确的生命周期：**创建**（`Application::new`）→ **启动**（`run`）→ **运行**（事件循环）→ **退出**。窗口是应用的顶层容器，一个应用可创建多个窗口。每个窗口有独立的 `Window` 上下文，包含渲染表面、输入事件队列等。理解生命周期和窗口管理是构建复杂应用的基础。

### 原理

`Application::new()` 初始化平台抽象层（创建 GPU 设备、字体缓存、事件循环等）。`run()` 进入事件循环，阻塞直到所有窗口关闭或调用 `cx.quit()`。事件循环处理三类事件：① 系统事件（窗口缩放、焦点变化）；② 输入事件（鼠标、键盘）；③ 自定义任务（通过 `cx.spawn` 提交的异步任务）。

窗口创建通过 `cx.open_window(options, build_closure)` 完成。`WindowOptions` 配置窗口属性：`window_bounds`（位置和大小）、`titlebar`（标题栏样式）、`fullscreen`、`decorations` 等。`build_closure` 接收 `&mut Window` 和 `&mut App`，返回根视图。窗口创建后返回一个 `WindowHandle`，可用于跨线程操作窗口（如关闭、聚焦）。

每个窗口维护自己的**渲染表面**（rendering surface）——一个 GPU 上下文和帧缓冲。GPUI 在每帧调用根视图的 `render`，生成元素树，diff 后只重绘变化区域。窗口的 `Window` 上下文提供 `observe_window_bounds`、`window_bounds` 等方法，让视图能响应窗口大小变化。

### 例子

```rust
use gpui::*;
use std::time::Duration;

struct LifecycleApp {
    window_title: String,
    frame_count: usize,
}

impl Render for LifecycleApp {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .size_full()
            .flex()
            .items_center()
            .justify_center()
            .bg(rgb(0x2e3440))
            .text_color(rgb(0xeceff4))
            .child(
                div()
                    .text_xl()
                    .child(format!("{} - 帧 {}", self.window_title, self.frame_count)),
            )
    }
}

fn main() {
    Application::new().run(|cx: &mut App| {
        // ===== 应用启动时执行 =====
        println!("应用启动");

        // 注册全局快捷键：Cmd+Q 退出
        cx.on_action::<gpui::actions::Quit>(|_action, cx| {
            println!("收到退出动作");
            cx.quit();
        });

        // ===== 创建第一个窗口 =====
        let window1 = cx
            .open_window(
                WindowOptions {
                    window_bounds: Some(WindowBounds::Windowed(Bounds {
                        origin: point(px(100.), px(100.)),
                        size: size(px(600.), px(400.)),
                    })),
                    titlebar: Some(TitlebarOptions {
                        title: Some("窗口 1".into()),
                        appears_transparent: false,
                        traffic_light_position: None,
                    }),
                    ..Default::default()
                },
                |_window, cx| {
                    cx.new(|_cx| LifecycleApp {
                        window_title: "窗口 1".to_string(),
                        frame_count: 0,
                    })
                },
            )
            .unwrap();

        // ===== 创建第二个窗口 =====
        let _window2 = cx
            .open_window(
                WindowOptions {
                    window_bounds: Some(WindowBounds::Windowed(Bounds {
                        origin: point(px(750.), px(100.)),
                        size: size(px(600.), px(400.)),
                    })),
                    titlebar: Some(TitlebarOptions {
                        title: Some("窗口 2".into()),
                        appears_transparent: false,
                        traffic_light_position: None,
                    }),
                    ..Default::default()
                },
                |_window, cx| {
                    cx.new(|_cx| LifecycleApp {
                        window_title: "窗口 2".to_string(),
                        frame_count: 0,
                    })
                },
            )
            .unwrap();

        // ===== 后台任务：每秒更新帧计数 =====
        cx.spawn(async move |cx| {
            loop {
                cx.background_executor().timer(Duration::from_secs(1)).await;
                // 通过 window handle 更新视图
                let _ = window1.update(cx, |view, _window, cx| {
                    view.frame_count += 1;
                    cx.notify();
                });
            }
        })
        .detach();

        cx.activate(true);
    });

    // run() 返回后执行
    println!("应用退出");
}
```

窗口生命周期示意图：

```
Application::new()
       │
       ▼
   run(cx) ──────────► 事件循环开始
       │                     │
       │                     ├─ 系统事件 → 分发到窗口
       │                     ├─ 输入事件 → 分发到焦点视图
       │                     ├─ 自定义任务 → spawn 执行
       │                     │
       │                     │  每帧：
       │                     │  1. 调用根视图 render()
       │                     │  2. 生成 Element 树
       │                     │  3. 与上帧 diff
       │                     │  4. GPU 重绘变化区域
       │                     │
       │                     ├─ 所有窗口关闭？
       │                     │     ├─ 是 → 退出循环
       │                     │     └─ 否 → 继续
       │                     │
       │                     └─ cx.quit() → 退出循环
       │
       ▼
   run() 返回，应用结束
```

### 总结

- 应用生命周期：`new()` → `run()` → 事件循环 → 退出
- 一个应用可创建多个窗口，每个窗口有独立的 `Window` 上下文和渲染表面
- `WindowOptions` 配置窗口属性：大小、位置、标题栏、全屏等
- `cx.spawn(async move |cx| {...})` 提交异步任务，`.detach()` 让其独立运行
- `cx.on_action::<Action>(handler)` 注册全局动作（如退出）
- **常见坑**：`spawn` 的闭包需 `move` 捕获；跨线程更新视图需通过 `WindowHandle::update`；忘记 `cx.activate(true)` 窗口可能不激活；`cx.quit()` 是优雅退出，`std::process::exit` 会跳过清理

---

## 第 4 讲：GPUI 核心概念总览

### 概念

本讲从宏观梳理 GPUI 的核心概念，为后续章节建立"地图"。GPUI 有五大核心概念：① **View（视图）**——实现 `Render` 的类型，是 UI 的逻辑单元；② **Element（元素）**——`render` 返回的不可变描述，是 UI 的视觉单元；③ **Model（模型）**——封装可变状态，通过 `Context` 访问；④ **Context（上下文）**——贯穿所有操作的"万能句柄"，提供状态访问、事件订阅、任务调度等能力；⑤ **Event（事件）**——用户输入和系统通知，通过 `on_click`、`on_key_down` 等绑定到元素。

### 原理

这五大概念的关系是 GPUI 架构的核心。**View** 持有业务状态和 `render` 逻辑，是开发者主要编写的类型。每次需要重绘时，GPUI 调用 View 的 `render` 方法，返回一棵 **Element** 树（如 `div().child(text()).child(button())`）。Element 树是**不可变的描述**——它只描述"应该长什么样"，不持有状态。GPUI 将新 Element 树与上一帧做 diff，只更新变化的视觉节点。

**Model** 是状态容器，用 `cx.new(|cx| StateType { ... })` 创建，返回 `Model<T>` 句柄。Model 的内部状态通过 `model.read(cx)` / `model.update(cx, |state, cx| {...})` 访问。多个 View 可共享同一个 Model，实现状态共享。**Context** 是 GPUI 的"万能句柄"——每个 View 的方法都接收 `&mut Context<Self>`，通过它可访问应用状态、订阅 Model 变化、调度任务、触发重绘。

**Event** 流：用户输入（鼠标点击、键盘按键）→ 平台事件队列 → GPUI 事件分发 → 命中测试（hit test）找到目标 Element → 触发绑定的闭包。闭包内通常修改 Model 状态，然后调用 `cx.notify()` 标记 View 需要重绘，GPUI 在下一帧调用 `render` 生成新 Element 树。

### 例子

```rust
use gpui::*;

// ===== Model：状态容器 =====
struct CounterState {
    value: i32,
}

// ===== View：UI 逻辑单元 =====
struct CounterApp {
    counter: Model<CounterState>,
}

impl CounterApp {
    fn new(cx: &mut Context<Self>) -> Self {
        // 创建 Model
        let counter = cx.new_model(|_cx| CounterState { value: 0 });
        // 订阅 Model 变化，当 counter 更新时收到通知
        cx.observe(&counter, |_this, _counter, cx| {
            cx.notify();  // 触发重绘
        })
        .detach();
        Self { counter }
    }
}

// ===== Render：返回 Element 树 =====
impl Render for CounterApp {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        // 读取 Model 状态
        let value = self.counter.read(cx).value;

        div()
            .size_full()
            .flex()
            .flex_col()
            .items_center()
            .justify_center()
            .gap_4()
            .bg(rgb(0x282828))
            .text_color(rgb(0xebdbb2))
            // Element 树：文本 + 按钮
            .child(div().text_3xl().child(format!("计数: {}", value)))
            .child(
                div()
                    .flex()
                    .gap_2()
                    .child(
                        button("-",
                            cx.listener(|this, _event, _window, cx| {
                                this.counter.update(cx, |state, cx| {
                                    state.value -= 1;
                                    cx.notify();
                                });
                            }),
                        )
                    )
                    .child(
                        button("+",
                            cx.listener(|this, _event, _window, cx| {
                                this.counter.update(cx, |state, cx| {
                                    state.value += 1;
                                    cx.notify();
                                });
                            }),
                        )
                    ),
            )
    }
}

// 辅助函数：创建按钮 Element
fn button(
    label: &str,
    handler: impl Fn(&ClickEvent, &mut Window, &mut App) + 'static,
) -> impl IntoElement {
    div()
        .px_4()
        .py_2()
        .bg(rgb(0x504945))
        .text_color(rgb(0xebdbb2))
        .rounded_md()
        .cursor_pointer()
        .child(label.to_string())
        .on_click(move |event, window, cx| handler(event, window, cx))
}

fn main() {
    Application::new().run(|cx: &mut App| {
        cx.open_window(WindowOptions::default(), |_window, cx| {
            cx.new(|cx| CounterApp::new(cx))
        })
        .unwrap();
        cx.activate(true);
    });
}
```

五大概念关系图：

```
┌──────────────────────────────────────────────────────────┐
│                     GPUI 应用                            │
│                                                          │
│  ┌─────────────┐         ┌─────────────────────┐        │
│  │   View      │ render()│     Element 树      │        │
│  │ (CounterApp)│────────►│  div()              │        │
│  │             │         │   ├─ text("计数: 0")│        │
│  │ 持有状态    │         │   └─ div()          │        │
│  │ 引用 Model  │         │       ├─ button("-")│        │
│  └──────┬──────┘         │       └─ button("+")│        │
│         │                └─────────┬───────────┘        │
│         │ read/update              │ diff               │
│         ▼                          ▼                    │
│  ┌─────────────┐            ┌─────────────┐             │
│  │   Model     │            │  GPU 渲染   │             │
│  │ (CounterState)│          │  重绘变化区域│             │
│  │   value: 0  │            └─────────────┘             │
│  └──────┬──────┘                                        │
│         │ observe/notify                                 │
│         ▼                                                │
│  ┌─────────────┐                                        │
│  │  Context    │◄──── Event (click, key, ...)           │
│  │  万能句柄   │                                       │
│  │  - 访问状态 │                                       │
│  │  - 订阅变化 │                                       │
│  │  - 调度任务 │                                       │
│  │  - 触发重绘 │                                       │
│  └─────────────┘                                        │
└──────────────────────────────────────────────────────────┘
```

### 总结

- **View**：实现 `Render` 的类型，持有业务状态和渲染逻辑，是开发者主要编写的单元
- **Element**：`render` 返回的不可变描述树，只描述"应该长什么样"，不持有状态
- **Model**：状态容器 `Model<T>`，通过 `cx.new_model` 创建，`read`/`update` 访问
- **Context**：万能句柄，提供状态访问、事件订阅、任务调度、重绘触发
- **Event**：用户输入经命中测试分发到目标 Element，触发绑定的闭包
- **常见坑**：Element 是不可变的，不要试图在 Element 上存状态；`cx.notify()` 是触发重绘的唯一方式；Model 变化不会自动触发 View 重绘，需 `observe` 订阅

---

# 第 2 章：视图与元素系统

本章是 GPUI UI 构建的核心。我们将深入 View 与 Render trait，学习 div 等基础元素，掌握 Flex 布局系统，理解样式如何作用于元素，并初步接触交互事件。学完本章，你就能用 GPUI 构建出结构清晰、样式美观的静态界面。

## 第 5 讲：View 与 Render trait

### 概念

**View** 在 GPUI 中并非一个具体的 trait，而是指"实现了 `Render` 的类型"。`Render` trait 是 GPUI 视图的核心契约——任何类型只要实现了 `render` 方法，就可以作为视图被 GPUI 渲染。View 持有业务状态，定义渲染逻辑，是开发者编写应用的主要载体。每个 View 实例通过 `cx.new(|cx| ViewType { ... })` 创建，返回一个 `View<T>` 句柄。

### 原理

`Render` trait 的核心方法签名（简化）：

```rust
pub trait Render: 'static {
    fn render(&mut self, window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement;
}
```

`render` 接收 `&mut self`（可变引用，允许修改状态）、`&mut Window`（窗口上下文，访问窗口属性）、`&mut Context<Self>`（视图上下文）。返回值是 `impl IntoElement`——任何能转换为 `Element` 的类型。`IntoElement` 是一个 trait，`div()`、`text()`、`img()` 等都实现了它。

View 的创建通过 `cx.new(|cx| ViewType { ... })`，这实际上是 `cx.new_model` 的语义糖——View 本质上是一个特殊的 Model。`cx.new` 返回 `View<T>`，它是一个引用计数的句柄，可在多个地方持有。当 View 的状态变化时，调用 `cx.notify()` 标记需要重绘，GPUI 在下一帧调用 `render`。

View 可以嵌套——一个 View 的 `render` 可以返回另一个 View 作为子元素。这通过 `.child(view_handle.clone())` 实现，GPUI 会自动调用子 View 的 `render`。嵌套 View 让复杂界面可分解为小组件，符合"组合优于继承"的原则。

### 例子

```rust
use gpui::*;

// ===== 子视图：标题栏 =====
struct TitleBar {
    title: String,
}

impl TitleBar {
    fn new(title: impl Into<String>) -> Self {
        Self { title: title.into() }
    }
}

impl Render for TitleBar {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .w_full()
            .h(px(40.))
            .flex()
            .items_center()
            .px_4()
            .bg(rgb(0x3c3836))
            .text_color(rgb(0xebdbb2))
            .text_sm()
            .child(self.title.clone())
    }
}

// ===== 子视图：内容区 =====
struct Content {
    items: Vec<String>,
}

impl Content {
    fn new() -> Self {
        Self {
            items: vec!["第一项".into(), "第二项".into(), "第三项".into()],
        }
    }
}

impl Render for Content {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .flex_1()
            .flex()
            .flex_col()
            .p_4()
            .gap_2()
            .bg(rgb(0x282828))
            .children(self.items.iter().map(|item| {
                div()
                    .px_3()
                    .py_2()
                    .bg(rgb(0x504945))
                    .rounded_md()
                    .text_color(rgb(0xebdbb2))
                    .child(item.clone())
            }))
    }
}

// ===== 根视图：组合子视图 =====
struct App {
    title_bar: View<TitleBar>,
    content: View<Content>,
}

impl App {
    fn new(cx: &mut Context<Self>) -> Self {
        Self {
            title_bar: cx.new(|_| TitleBar::new("我的应用")),
            content: cx.new(|_| Content::new()),
        }
    }
}

impl Render for App {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .flex()
            .flex_col()
            .size_full()
            .child(self.title_bar.clone())   // 嵌套子视图
            .child(self.content.clone())     // 嵌套子视图
    }
}

fn main() {
    Application::new().run(|cx: &mut App| {
        cx.open_window(WindowOptions::default(), |_window, cx| {
            cx.new(|cx| App::new(cx))
        })
        .unwrap();
        cx.activate(true);
    });
}
```

View 嵌套结构：

```
App (根视图)
├── TitleBar (子视图)
│   └── div().child("我的应用")
└── Content (子视图)
    └── div().flex_col()
        ├── div().child("第一项")
        ├── div().child("第二项")
        └── div().child("第三项")
```

### 总结

- View 是"实现了 `Render` 的类型"，持有状态和渲染逻辑
- `Render::render(&mut self, window, cx) -> impl IntoElement` 是核心方法
- `cx.new(|cx| ViewType { ... })` 创建 View，返回 `View<T>` 句柄
- View 可嵌套：`.child(view_handle.clone())` 将子 View 作为元素
- **常见坑**：`render` 接收 `&mut self`，不要在 `render` 中调用 `cx.notify()`（会无限递归）；嵌套 View 需 `clone` 句柄；`View<T>` 必须实现 `Render`，否则编译错误

---

## 第 6 讲：Element 基础与 div 元素

### 概念

**Element** 是 GPUI 中描述 UI 视觉的最小单元。`render` 方法返回的就是一棵 Element 树。GPUI 提供多种内置 Element：`div`（块级容器，最常用）、`img`（图片）、`svg`（矢量图）、`text`（文本，通常通过字符串自动转换）。`div` 是 GPUI 的"万能容器"——通过链式调用添加样式、布局、子元素、事件，构建出复杂界面。

### 原理

`div()` 返回一个 `Div` 类型，它实现了 `IntoElement`。`Div` 是一个**构建器（builder）**——每个方法（如 `.flex()`、`.bg()`、`.child()`）都消费 self 并返回修改后的 `Div`，形成链式调用。这种"构建器模式"让 UI 描述既类型安全又简洁。

`Div` 内部维护一个 `Style` 结构体（存储布局、颜色、尺寸等）和一个子元素列表。当 `Div` 被加入元素树后，GPUI 在布局阶段计算其最终位置和大小，在绘制阶段调用 GPU 命令渲染。`Div` 支持任意数量的子元素，通过 `.child()` 逐个添加或 `.children()` 批量添加（接收迭代器）。

`div` 的链式调用顺序通常为：布局属性（`.flex()`、`.flex_col()`）→ 尺寸（`.size_full()`、`.w()`、`.h()`）→ 间距（`.p_4()`、`.gap_2()`）→ 视觉样式（`.bg()`、`.text_color()`、`.rounded_md()`）→ 子元素（`.child()`）→ 事件（`.on_click()`）。这只是惯例，顺序不影响最终结果，但保持一致的顺序可提高代码可读性。

### 例子

```rust
use gpui::*;

fn main() {
    Application::new().run(|cx: &mut App| {
        cx.open_window(WindowOptions::default(), |_window, cx| {
            cx.new(|_cx| ElementDemo)
        })
        .unwrap();
        cx.activate(true);
    });
}

struct ElementDemo;

impl Render for ElementDemo {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        // ===== 基础 div =====
        div()
            .size_full()
            .flex()
            .flex_col()
            .gap_4()
            .p_6()
            .bg(rgb(0x1e1e2e))

            // ===== 文本子元素（字符串自动转 text 元素）=====
            .child("GPUI Element 演示".to_string())

            // ===== 嵌套 div =====
            .child(
                div()
                    .w(px(200.))
                    .h(px(100.))
                    .bg(rgb(0x89b4fa))
                    .rounded_lg()
                    .flex()
                    .items_center()
                    .justify_center()
                    .text_color(rgb(0x1e1e2e))
                    .child("蓝色方块")
            )

            // ===== 多个子元素 =====
            .child(
                div()
                    .flex()
                    .gap_2()
                    // 用 .children() 批量添加
                    .children(["红", "绿", "黄"].iter().map(|&name| {
                        let color = match name {
                            "红" => rgb(0xf38ba8),
                            "绿" => rgb(0xa6e3a1),
                            _ => rgb(0xf9e2af),
                        };
                        div()
                            .size(px(60.))
                            .bg(color)
                            .rounded_md()
                            .flex()
                            .items_center()
                            .justify_center()
                            .child(name.to_string())
                    }))
            )

            // ===== 条件渲染 =====
            .child(
                div()
                    .flex()
                    .gap_2()
                    .child(if true {
                        div().px_3().py_1().bg(rgb(0xa6e3a1)).child("启用").into_any_element()
                    } else {
                        div().px_3().py_1().bg(rgb(0x6c7086)).child("禁用").into_any_element()
                    })
            )

            // ===== 图片元素（需实际图片路径）=====
            // .child(
            //     img("path/to/image.png")
            //         .size(px(100.))
            //         .rounded_full()
            // )

            // ===== SVG 元素 =====
            .child(
                svg()
                    .path("assets/icon.svg")  // 需实际 svg 文件
                    .size(px(32.))
                    .text_color(rgb(0xcdd6f4))
            )
    }
}
```

`div` 构建器常用方法分类：

```
┌─────────────────────────────────────────────────────────────┐
│                    div() 构建器方法分类                      │
├──────────────┬──────────────────────────────────────────────┤
│ 布局         │ .flex() .flex_col() .flex_row() .flex_wrap() │
│              │ .items_center() .justify_center()            │
│              │ .justify_between() .justify_around()         │
├──────────────┼──────────────────────────────────────────────┤
│ 尺寸         │ .size_full() .size(px) .w_full() .w(px)      │
│              │ .h_full() .h(px) .min_w() .max_w()           │
│              │ .min_h() .max_h() .flex_1() .flex_none()     │
├──────────────┼──────────────────────────────────────────────┤
│ 间距         │ .p_1()..p_8() .px_1().. .py_1()..            │
│              │ .m_1()..m_8() .mx_1().. .my_1()..            │
│              │ .gap_1()..gap_8()                             │
├──────────────┼──────────────────────────────────────────────┤
│ 视觉         │ .bg(rgb) .text_color(rgb) .border()          │
│              │ .border_color(rgb) .rounded_sm/md/lg/xl/full │
│              │ .shadow_sm/md/lg .opacity(f32)               │
├──────────────┼──────────────────────────────────────────────┤
│ 文本         │ .text_xs/sm/md/lg/xl/2xl/3xl/4xl             │
│              │ .font_weight() .italic() .underline()        │
│              │ .text_center() .text_left() .text_right()    │
├──────────────┼──────────────────────────────────────────────┤
│ 子元素       │ .child(element) .children(iter)              │
├──────────────┼──────────────────────────────────────────────┤
│ 交互         │ .cursor_pointer() .on_click(handler)         │
│              │ .on_hover(handler) .on_drag(handler)         │
└──────────────┴──────────────────────────────────────────────┘
```

### 总结

- `div()` 是 GPUI 的万能容器，通过链式调用构建 UI
- `Div` 是构建器，每个方法消费 self 返回修改后的 `Div`
- `.child()` 添加单个子元素，`.children()` 批量添加（接收迭代器）
- 条件渲染用 `if/else` 配合 `.into_any_element()` 统一类型
- **常见坑**：`.child()` 接收 `impl IntoElement`，字符串需 `.to_string()`；条件分支返回不同 Element 类型需 `.into_any_element()` 统一；`div` 默认无样式，需手动设置背景/尺寸

---

## 第 7 讲：布局系统：Flex 布局

### 概念

GPUI 采用 **Flex 布局**（弹性盒子布局），与 CSS Flexbox 几乎一致。Flex 布局通过主轴（main axis）和交叉轴（cross axis）组织子元素，支持对齐、分布、伸缩。核心概念：① **flex direction**——主轴方向（行/列）；② **justify content**——主轴对齐；③ **align items**——交叉轴对齐；④ **flex grow/shrink**——伸缩比例。掌握 Flex 布局是构建复杂界面的关键。

### 原理

Flex 布局的核心是"主轴"和"交叉轴"。当 `flex direction` 为 `row`（默认）时，主轴是水平方向，交叉轴是垂直方向；为 `column` 时反之。`justify_content` 控制子元素在主轴上的分布（起始、居中、末尾、两端、均匀），`align_items` 控制子元素在交叉轴上的对齐。

`flex_grow`（GPUI 中为 `.flex_1()`）让元素占据剩余空间——多个 `flex_1` 元素平分剩余空间。`flex_none`（`.flex_none()`）禁止伸缩，元素保持固有大小。`flex_wrap`（`.flex_wrap()`）允许子元素换行，当容器空间不足时自动换到下一行。

GPUI 的 Flex 布局由 Taffy 引擎（一个 Rust 实现的 Flexbox/Grid 布局库）计算。每帧布局阶段，GPUI 将 Element 树转换为 Taffy 的节点树，调用 Taffy 计算每个节点的位置和大小，结果用于后续绘制。这种设计让 GPUI 的布局与 Web 前端开发体验一致，降低了学习成本。

### 例子

```rust
use gpui::*;

struct FlexLayout;

impl Render for FlexLayout {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .size_full()
            .flex()
            .flex_col()
            .gap_4()
            .p_4()
            .bg(rgb(0x1e1e2e))

            // ===== 1. 主轴方向：row vs column =====
            .child(
                div()
                    .flex()
                    .gap_2()
                    .child(color_box(rgb(0xf38ba8), "1"))
                    .child(color_box(rgb(0xa6e3a1), "2"))
                    .child(color_box(rgb(0xf9e2af), "3"))
            )
            // 默认 row：水平排列

            // ===== 2. justify_content：主轴对齐 =====
            .child(
                div()
                    .flex()
                    .justify_between()  // 两端对齐
                    .bg(rgb(0x313244))
                    .p_2()
                    .rounded_md()
                    .child(color_box(rgb(0xf38ba8), "起始"))
                    .child(color_box(rgb(0xa6e3a1), "末尾"))
            )

            // ===== 3. align_items：交叉轴对齐 =====
            .child(
                div()
                    .flex()
                    .h(px(80.))
                    .items_center()  // 垂直居中
                    .bg(rgb(0x313244))
                    .p_2()
                    .rounded_md()
                    .child(color_box(rgb(0x89b4fa), "垂直居中"))
            )

            // ===== 4. flex_1：伸缩占据剩余空间 =====
            .child(
                div()
                    .flex()
                    .gap_2()
                    .h(px(60.))
                    .child(color_box(rgb(0xf38ba8), "固定"))
                    .child(div().flex_1().bg(rgb(0xa6e3a1)).rounded_md().flex().items_center().justify_center().text_color(rgb(0x1e1e2e)).child("flex_1 占据剩余"))
                    .child(color_box(rgb(0xf9e2af), "固定"))
            )

            // ===== 5. flex_wrap：换行 =====
            .child(
                div()
                    .flex()
                    .flex_wrap()
                    .gap_2()
                    .bg(rgb(0x313244))
                    .p_2()
                    .rounded_md()
                    .children((0..8).map(|i| {
                        div()
                            .w(px(80.))
                            .h(px(30.))
                            .bg(rgb(0x89b4fa))
                            .rounded_md()
                            .flex()
                            .items_center()
                            .justify_center()
                            .child(format!("项 {}", i))
                    }))
            )

            // ===== 6. 嵌套 Flex 实现复杂布局 =====
            .child(
                // 经典布局：左侧栏 + 主内容 + 右侧栏
                div()
                    .flex()
                    .flex_1()
                    .gap_2()
                    .child(
                        div()
                            .w(px(120.))
                            .bg(rgb(0x181825))
                            .rounded_md()
                            .p_2()
                            .child("侧边栏")
                    )
                    .child(
                        div()
                            .flex_1()
                            .bg(rgb(0x313244))
                            .rounded_md()
                            .p_2()
                            .child("主内容区")
                    )
                    .child(
                        div()
                            .w(px(120.))
                            .bg(rgb(0x181825))
                            .rounded_md()
                            .p_2()
                            .child("右侧栏")
                    )
            )
    }
}

fn color_box(color: Hsla, label: &str) -> impl IntoElement {
    div()
        .size(px(50.))
        .bg(color)
        .rounded_md()
        .flex()
        .items_center()
        .justify_center()
        .text_color(rgb(0x1e1e2e))
        .text_sm()
        .child(label.to_string())
}

fn main() {
    Application::new().run(|cx: &mut App| {
        cx.open_window(WindowOptions::default(), |_window, cx| {
            cx.new(|_cx| FlexLayout)
        })
        .unwrap();
        cx.activate(true);
    });
}
```

Flex 布局示意图：

```
justify_content (主轴分布)         align_items (交叉轴对齐)
┌──────────────────────────┐      ┌──────────────────────────┐
│ start:                   │      │ start:                   │
│  [■][■][■]               │      │  [■]                     │
│                          │      │  [■]                     │
│ center:                  │      │  [■]                     │
│    [■][■][■]             │      │                          │
│                          │      │ center:                  │
│ end:                     │      │  [■]                     │
│               [■][■][■]  │      │  [■]                     │
│                          │      │  [■]                     │
│ space_between:           │      │                          │
│  [■]        [■]        [■]│      │ end:                     │
│                          │      │              [■]         │
│ space_around:            │      │              [■]         │
│   [■]    [■]    [■]      │      │              [■]         │
│                          │      │                          │
└──────────────────────────┘      └──────────────────────────┘
```

### 总结

- Flex 布局通过主轴/交叉轴组织子元素，与 CSS Flexbox 一致
- `flex()` 默认 row（水平），`flex_col()` 改为 column（垂直）
- `justify_*` 控制主轴分布，`items_*` 控制交叉轴对齐
- `flex_1()` 让元素占据剩余空间，多个 `flex_1` 平分；`flex_none()` 禁止伸缩
- `flex_wrap()` 允许换行
- **常见坑**：`flex_1` 需父容器有确定尺寸才生效；`items_center` 要求交叉轴有富余空间；嵌套 Flex 时内层需明确尺寸或 `flex_1`

---

## 第 8 讲：样式系统：颜色、字体、间距

### 概念

GPUI 的样式系统通过 `Style` 结构体和 `div` 的链式方法配置。核心样式包括：① **颜色**——背景色、文字色、边框色，用 `rgb()`/`rgba()`/`hsla()` 指定；② **字体**——大小、粗细、字体族；③ **间距**——内边距、外边距、gap，用预设档位（`p_1` 到 `p_8`）或精确像素；④ **边框与圆角**——`border()`、`rounded_md()` 等。GPUI 的样式方法命名遵循 Tailwind CSS 风格，降低学习成本。

### 原理

GPUI 的颜色用 `Hsla` 类型表示（色相、饱和度、亮度、透明度），但提供了便捷的 `rgb()`、`rgba()`、`hsla()` 函数转换。`rgb(0xRRGGBB)` 将十六进制颜色转为 `Hsla`。颜色支持透明度叠加——`rgba(0x1e1e2eff)` 最后两位是 alpha 通道。

间距系统采用 4px 基准：`p_1` = 4px，`p_2` = 8px，`p_3` = 12px，`p_4` = 16px，以此类推。这种"档位制"让间距视觉一致，避免随意数值导致的混乱。精确控制时可用 `p(px(15.))` 传入任意像素值。

字体系统通过 `text_xs/sm/md/lg/xl/2xl/3xl/4xl` 设置大小（对应 12/14/16/18/20/24/30/36px），`font_weight()` 设置粗细（`FontWeight::THIN` 到 `FontWeight::BLACK`）。GPUI 默认使用系统字体栈，可通过 `cx.text_style()` 全局设置。文本对齐用 `text_left/center/right`，但注意 Flex 布局下文本对齐需配合 `items_*` 和 `justify_*`。

### 例子

```rust
use gpui::*;

struct StyleDemo;

impl Render for StyleDemo {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .size_full()
            .flex()
            .flex_col()
            .gap_4()
            .p_6()
            // ===== 颜色系统 =====
            .bg(rgb(0x1e1e2e))           // 十六进制
            .text_color(rgb(0xcdd6f4))

            // ===== 字体大小档位 =====
            .child(div().text_xs().child("text_xs (12px)"))
            .child(div().text_sm().child("text_sm (14px)"))
            .child(div().text_md().child("text_md (16px)"))
            .child(div().text_lg().child("text_lg (18px)"))
            .child(div().text_xl().child("text_xl (20px)"))
            .child(div().text_2xl().child("text_2xl (24px)"))
            .child(div().text_3xl().child("text_3xl (30px)"))

            // ===== 字体粗细 =====
            .child(
                div()
                    .flex()
                    .gap_4()
                    .child(div().font_weight(FontWeight::THIN).child("THIN"))
                    .child(div().font_weight(FontWeight::NORMAL).child("NORMAL"))
                    .child(div().font_weight(FontWeight::BOLD).child("BOLD"))
                    .child(div().font_weight(FontWeight::BLACK).child("BLACK"))
            )

            // ===== 间距档位演示 =====
            .child(
                div()
                    .flex()
                    .gap_2()
                    .child(spacer_box(rgb(0xf38ba8), "p_1", px(4.)))
                    .child(spacer_box(rgb(0xa6e3a1), "p_2", px(8.)))
                    .child(spacer_box(rgb(0xf9e2af), "p_4", px(16.)))
                    .child(spacer_box(rgb(0x89b4fa), "p_8", px(32.)))
            )

            // ===== 边框与圆角 =====
            .child(
                div()
                    .flex()
                    .gap_2()
                    .child(div().size(px(60.)).bg(rgb(0xf38ba8)).border_0().child(""))
                    .child(div().size(px(60.)).bg(rgb(0xa6e3a1)).border_2().border_color(rgb(0xcdd6f4)).child(""))
                    .child(div().size(px(60.)).bg(rgb(0xf9e2af)).rounded_sm().child(""))
                    .child(div().size(px(60.)).bg(rgb(0x89b4fa)).rounded_md().child(""))
                    .child(div().size(px(60.)).bg(rgb(0xf5c2e7)).rounded_lg().child(""))
                    .child(div().size(px(60.)).bg(rgb(0x94e2d5)).rounded_xl().child(""))
                    .child(div().size(px(60.)).bg(rgb(0xfab387)).rounded_full().child(""))
            )

            // ===== 透明度 =====
            .child(
                div()
                    .flex()
                    .gap_2()
                    .bg(rgb(0xcdd6f4))
                    .p_2()
                    .rounded_md()
                    .child(div().size(px(50.)).bg(rgb(0xf38ba8)).opacity(1.0).child(""))
                    .child(div().size(px(50.)).bg(rgb(0xf38ba8)).opacity(0.7).child(""))
                    .child(div().size(px(50.)).bg(rgb(0xf38ba8)).opacity(0.4).child(""))
                    .child(div().size(px(50.)).bg(rgb(0xf38ba8)).opacity(0.2).child(""))
            )

            // ===== 阴影 =====
            .child(
                div()
                    .flex()
                    .gap_4()
                    .p_4()
                    .child(div().size(px(60.)).bg(rgb(0xcdd6f4)).shadow_sm().rounded_md().child(""))
                    .child(div().size(px(60.)).bg(rgb(0xcdd6f4)).shadow_md().rounded_md().child(""))
                    .child(div().size(px(60.)).bg(rgb(0xcdd6f4)).shadow_lg().rounded_md().child(""))
            )

            // ===== 文本样式 =====
            .child(
                div()
                    .flex()
                    .flex_col()
                    .gap_1()
                    .child(div().italic().child("斜体文本"))
                    .child(div().underline().child("下划线文本"))
                    .child(div().line_through().child("删除线文本"))
                    .child(div().text_center().w(px(200.)).bg(rgb(0x313244)).child("居中对齐"))
            )
    }
}

fn spacer_box(color: Hsla, label: &str, pad: Pixels) -> impl IntoElement {
    div()
        .bg(rgb(0x313244))
        .rounded_md()
        .child(
            div()
                .p(pad)  // 精确像素值
                .bg(color)
                .rounded_md()
                .text_color(rgb(0x1e1e2e))
                .text_xs()
                .child(label.to_string())
        )
}

fn main() {
    Application::new().run(|cx: &mut App| {
        cx.open_window(WindowOptions::default(), |_window, cx| {
            cx.new(|_cx| StyleDemo)
        })
        .unwrap();
        cx.activate(true);
    });
}
```

### 总结

- 颜色用 `rgb(0xRRGGBB)` 或 `rgba(0xRRGGBBAA)` 指定，内部转为 `Hsla`
- 间距档位基于 4px：`p_1`=4px, `p_2`=8px, `p_4`=16px, `p_8`=32px；精确值用 `p(px(15.))`
- 字体大小档位：`text_xs`(12px) 到 `text_4xl`(36px)；粗细用 `font_weight(FontWeight::BOLD)`
- 圆角：`rounded_sm/md/lg/xl/full`；边框：`border_0/1/2/4/8` + `border_color()`
- 透明度 `.opacity(f32)`，阴影 `.shadow_sm/md/lg`
- **常见坑**：`rgb()` 接收 `u32` 不是字符串；间距档位是预设的，不能写 `p_3_5`；文本对齐在 Flex 下需配合布局属性；颜色透明度用 `rgba` 或 `.opacity()` 二选一

---

## 第 9 讲：交互元素与事件处理

### 概念

交互是 GUI 应用的灵魂。GPUI 通过事件处理器（event handler）让元素响应鼠标、键盘等输入。核心方法：`.on_click()`（点击）、`.on_hover()`（悬停）、`.on_mouse_down()`（按下）、`.on_mouse_move()`（移动）等。事件处理器接收闭包，闭包内可修改 View 状态并触发重绘。`cx.listener()` 是绑定事件到当前 View 的语法糖，让闭包能访问 `&mut self`。

### 原理

GPUI 的事件分发基于**命中测试（hit test）**。当用户点击鼠标时，GPUI 从元素树的根开始，递归查找包含点击坐标的最深层元素，然后从该元素向上冒泡触发事件。每个元素可通过 `.on_click()` 等方法注册处理器，处理器闭包接收事件对象（如 `&ClickEvent`）、`&mut Window` 和 `&mut App`。

`cx.listener()` 的作用是将普通闭包转换为"绑定到当前 View"的闭包。普通事件闭包签名是 `Fn(&Event, &mut Window, &mut App)`，无法访问 View 状态；`cx.listener()` 将其包装为 `Fn(&mut View, &Event, &mut Window, &mut Context<View>)`，让闭包能修改 `self`。这是 GPUI 类型安全的体现——编译器保证只有持有 View 句柄的代码才能修改其状态。

事件处理器内修改状态后，必须调用 `cx.notify()` 标记 View 需要重绘。GPUI 不会自动检测状态变化（因为 Rust 没有响应式系统），显式 `notify` 是触发重绘的唯一方式。这种"显式通知"虽然略显繁琐，但让重绘时机完全可控，避免了不必要的渲染开销。

### 例子

```rust
use gpui::*;

struct InteractiveDemo {
    click_count: usize,
    hover_count: usize,
    is_hovering: bool,
    last_mouse_pos: Point<Pixels>,
    background: Hsla,
}

impl InteractiveDemo {
    fn new() -> Self {
        Self {
            click_count: 0,
            hover_count: 0,
            is_hovering: false,
            last_mouse_pos: Default::default(),
            background: rgb(0x1e1e2e),
        }
    }
}

impl Render for InteractiveDemo {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .size_full()
            .flex()
            .flex_col()
            .gap_4()
            .p_6()
            .bg(self.background)
            .text_color(rgb(0xcdd6f4))
            // ===== 鼠标移动事件（在整个容器上）=====
            .on_mouse_move(cx.listener(|this, event, _window, cx| {
                this.last_mouse_pos = event.position;
                cx.notify();
            }))
            .child(
                div()
                    .text_lg()
                    .child(format!(
                        "鼠标位置: ({:.0}, {:.0})",
                        self.last_mouse_pos.x.0, self.last_mouse_pos.y.0
                    ))
            )
            .child(
                div()
                    .text_sm()
                    .child(format!("点击次数: {} | 悬停次数: {}", self.click_count, self.hover_count))
            )
            // ===== 点击按钮 =====
            .child(
                div()
                    .px_6()
                    .py_3()
                    .bg(if self.is_hovering { rgb(0x89b4fa) } else { rgb(0x313244) })
                    .text_color(if self.is_hovering { rgb(0x1e1e2e) } else { rgb(0xcdd6f4) })
                    .rounded_md()
                    .cursor_pointer()
                    .child("点击我")
                    .on_click(cx.listener(|this, _event, _window, cx| {
                        this.click_count += 1;
                        // 随机改变背景色
                        this.background = rgb(0x1e1e2e + (this.click_count as u32 * 0x101010) & 0xFFFFFF);
                        cx.notify();
                    }))
                    // ===== 悬停事件 =====
                    .on_hover(cx.listener(|this, state, _window, cx| {
                        this.is_hovering = state;
                        if state {
                            this.hover_count += 1;
                        }
                        cx.notify();
                    }))
            )
            // ===== 鼠标按下/释放 =====
            .child(
                div()
                    .px_6()
                    .py_3()
                    .bg(rgb(0xa6e3a1))
                    .text_color(rgb(0x1e1e2e))
                    .rounded_md()
                    .cursor_pointer()
                    .child("按住我")
                    .on_mouse_down(MouseButton::Left, cx.listener(|_this, _event, _window, cx| {
                        println!("鼠标按下");
                        cx.notify();
                    }))
                    .on_mouse_up(MouseButton::Left, cx.listener(|_this, _event, _window, cx| {
                        println!("鼠标释放");
                        cx.notify();
                    }))
            )
            // ===== 双击事件 =====
            .child(
                div()
                    .px_6()
                    .py_3()
                    .bg(rgb(0xf9e2af))
                    .text_color(rgb(0x1e1e2e))
                    .rounded_md()
                    .cursor_pointer()
                    .child("双击我")
                    .on_click(cx.listener(|this, event: &ClickEvent, _window, cx| {
                        // ClickEvent 包含点击次数信息
                        if event.click_count >= 2 {
                            this.click_count += 10;
                            cx.notify();
                        }
                    }))
            )
    }
}

fn main() {
    Application::new().run(|cx: &mut App| {
        cx.open_window(WindowOptions::default(), |_window, cx| {
            cx.new(|_cx| InteractiveDemo::new())
        })
        .unwrap();
        cx.activate(true);
    });
}
```

事件流示意图：

```
用户点击鼠标 (x=100, y=50)
         │
         ▼
┌─────────────────────────────────────┐
│  GPUI 事件循环                       │
│  1. 接收平台鼠标事件                  │
│  2. 命中测试：从根遍历元素树          │
│     找到坐标 (100,50) 的最深层元素    │
│  3. 从该元素向上冒泡                  │
└─────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────┐
│  元素树                              │
│  div (根)                            │
│   └─ div (按钮) ← 命中！             │
│       └─ on_click(handler)          │
└─────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────┐
│  handler 闭包执行                    │
│  cx.listener(|this, event, w, cx| { │
│      this.count += 1;  // 修改状态  │
│      cx.notify();      // 通知重绘   │
│  })                                  │
└─────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────┐
│  下一帧                              │
│  1. GPUI 调用 render()               │
│  2. 生成新 Element 树                │
│  3. diff 后重绘变化区域              │
└─────────────────────────────────────┘
```

### 总结

- 事件通过 `.on_click()`、`.on_hover()`、`.on_mouse_down()` 等方法绑定到元素
- `cx.listener()` 将闭包绑定到当前 View，让闭包能访问 `&mut self`
- 事件分发基于命中测试：从根遍历找最深层命中元素，向上冒泡
- 修改状态后必须调用 `cx.notify()` 触发重绘
- **常见坑**：忘记 `cx.notify()` 导致界面不更新；`on_click` 的闭包参数是 `&ClickEvent`，含 `click_count` 可判断双击；事件冒泡顺序是从内到外；`on_hover` 的状态参数是 `bool`

---

# 第 3 章：状态管理

GUI 应用的核心是"状态驱动视图"——状态变化触发界面更新。本章深入 GPUI 的状态管理机制：Model 作为状态容器、Context 作为访问句柄、观察者模式实现状态订阅、全局状态用于跨视图共享。掌握这些，你就能构建出状态清晰、数据流动可控的复杂应用。

## 第 10 讲：Model 与状态管理

### 概念

**Model** 是 GPUI 的状态容器，用 `Model<T>` 表示——一个引用计数的智能指针，包装任意类型 `T` 的状态。Model 让状态独立于 View 存在，可被多个 View 共享访问。通过 `cx.new_model(|cx| State { ... })` 创建，`model.read(cx)` 读取，`model.update(cx, |state, cx| { ... })` 修改。Model 是 GPUI 实现"状态与视图分离"的关键工具。

### 原理

`Model<T>` 内部是一个 `Arc<RefCell<T>>`（简化理解），让多个持有者共享同一份状态，且可在运行期修改。与 `Rc<RefCell<T>>` 不同的是，GPUI 的 Model 集成了观察者模式——当 `update` 闭包内调用 `cx.notify()` 时，所有订阅该 Model 的 View 都会被标记为需要重绘。

Model 的设计哲学是"单一数据源"（single source of truth）。例如，一个购物车的状态应放在一个 Model 中，多个 View（购物车图标、购物车页面、结算栏）都订阅这个 Model，而非各自维护一份状态。当 Model 变化时，所有相关 View 自动更新，避免状态不一致。

`Model<T>` 与 `View<T>` 的关系：View 本质上是"实现了 `Render` 的 Model"。`cx.new(|cx| ViewType { ... })` 内部调用 `cx.new_model`，只是额外要求 `ViewType: Render`。因此 Model 的所有 API（`read`/`update`/`observe`）对 View 同样适用。选择 Model 还是 View 的判断标准：需要渲染 UI 就用 View，纯数据状态用 Model。

### 例子

```rust
use gpui::*;
use std::time::Duration;

// ===== 共享状态：计数器 =====
struct CounterState {
    value: i32,
}

impl CounterState {
    fn new() -> Self {
        Self { value: 0 }
    }

    fn increment(&mut self, cx: &mut Context<Self>) {
        self.value += 1;
        cx.notify();  // 通知订阅者
    }

    fn decrement(&mut self, cx: &mut Context<Self>) {
        self.value -= 1;
        cx.notify();
    }

    fn reset(&mut self, cx: &mut Context<Self>) {
        self.value = 0;
        cx.notify();
    }
}

// ===== 视图 1：显示计数 =====
struct CounterDisplay {
    state: Model<CounterState>,
}

impl Render for CounterDisplay {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        let value = self.state.read(cx).value;
        div()
            .px_6()
            .py_4()
            .bg(rgb(0x313244))
            .rounded_md()
            .text_color(rgb(0xcdd6f4))
            .text_2xl()
            .flex()
            .items_center()
            .justify_center()
            .child(format!("计数: {}", value))
            // 订阅 state 变化，自动重绘
            .observe(&self.state, |_this, _state, cx| {
                cx.notify();
            })
    }
}

// ===== 视图 2：控制按钮 =====
struct CounterControls {
    state: Model<CounterState>,
}

impl Render for CounterControls {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        let state = self.state.clone();
        div()
            .flex()
            .gap_2()
            // 减少按钮
            .child(
                div()
                    .px_4()
                    .py_2()
                    .bg(rgb(0xf38ba8))
                    .text_color(rgb(0x1e1e2e))
                    .rounded_md()
                    .cursor_pointer()
                    .child("-")
                    .on_click(cx.listener({
                        let state = state.clone();
                        move |_this, _event, _window, cx| {
                            state.update(cx, |s, cx| s.decrement(cx));
                        }
                    }))
            )
            // 重置按钮
            .child(
                div()
                    .px_4()
                    .py_2()
                    .bg(rgb(0xf9e2af))
                    .text_color(rgb(0x1e1e2e))
                    .rounded_md()
                    .cursor_pointer()
                    .child("重置")
                    .on_click(cx.listener({
                        let state = state.clone();
                        move |_this, _event, _window, cx| {
                            state.update(cx, |s, cx| s.reset(cx));
                        }
                    }))
            )
            // 增加按钮
            .child(
                div()
                    .px_4()
                    .py_2()
                    .bg(rgb(0xa6e3a1))
                    .text_color(rgb(0x1e1e2e))
                    .rounded_md()
                    .cursor_pointer()
                    .child("+")
                    .on_click(cx.listener(move |_this, _event, _window, cx| {
                        state.update(cx, |s, cx| s.increment(cx));
                    }))
            )
    }
}

// ===== 根视图：组合显示与控制 =====
struct App {
    state: Model<CounterState>,
    display: View<CounterDisplay>,
    controls: View<CounterControls>,
}

impl App {
    fn new(cx: &mut Context<Self>) -> Self {
        let state = cx.new_model(|_| CounterState::new());
        let state_for_display = state.clone();
        let state_for_controls = state.clone();
        Self {
            state,
            display: cx.new(|_| CounterDisplay { state: state_for_display }),
            controls: cx.new(|_| CounterControls { state: state_for_controls }),
        }
    }
}

impl Render for App {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .size_full()
            .flex()
            .flex_col()
            .items_center()
            .justify_center()
            .gap_4()
            .bg(rgb(0x1e1e2e))
            .child(self.display.clone())
            .child(self.controls.clone())
    }
}

fn main() {
    Application::new().run(|cx: &mut App| {
        cx.open_window(WindowOptions::default(), |_window, cx| {
            cx.new(|cx| App::new(cx))
        })
        .unwrap();
        cx.activate(true);
    });
}
```

Model 状态流：

```
        ┌─────────────────────────────────┐
        │     Model<CounterState>          │
        │     value: 5                     │
        │     (单一数据源)                  │
        └──────────┬──────────────────────┘
                   │ observe (订阅)
       ┌───────────┴───────────┐
       ▼                       ▼
┌──────────────┐       ┌──────────────┐
│ CounterDisplay│       │CounterControls│
│ (View)        │       │ (View)        │
│              │       │              │
│ 读取 value   │       │ 修改 value   │
│ 显示 "5"     │       │ +/- 按钮     │
└──────────────┘       └──────────────┘
       ▲                       │
       │                       │ update + notify
       └───────────────────────┘
        状态变化 → 通知所有订阅者 → 重绘
```

### 总结

- `Model<T>` 是引用计数的状态容器，让状态独立于 View 存在
- `cx.new_model(|cx| State { ... })` 创建，`read(cx)` 读取，`update(cx, |s, cx| ...)` 修改
- `update` 闭包内调用 `cx.notify()` 通知所有订阅者重绘
- View 本质是"实现了 Render 的 Model"，共享相同 API
- **常见坑**：`read` 返回 `&T` 不可变借用，不能修改；`update` 闭包内必须 `cx.notify()` 否则订阅者不重绘；多个 View 共享同一 Model 需 `clone` 句柄（引用计数 +1）

---

## 第 11 讲：Context 与观察者模式

### 概念

**Context**（`Context<T>`）是 GPUI 的"万能句柄"，每个 View/Model 都有自己的 Context。Context 提供：① 访问自身状态（`&mut self`）；② 订阅其他 Model/View 的变化（`observe`）；③ 调度异步任务（`spawn`）；④ 创建新 Model/View（`new_model`/`new`）；⑤ 触发重绘（`notify`）。**观察者模式**通过 `cx.observe(&model, |this, model, cx| { ... })` 实现——当 `model` 调用 `notify` 时，闭包被触发。

### 原理

`Context<T>` 的泛型参数 `T` 表示"当前 View/Model 的类型"，让 Context 提供类型安全的状态访问。例如 `Context<CounterDisplay>` 的闭包能访问 `&mut CounterDisplay`。Context 内部持有应用全局状态（`App`）的引用、当前 View 的句柄、订阅列表等。

观察者模式的实现：`cx.observe(&model, callback)` 将 `callback` 注册到 `model` 的订阅者列表。当 `model.update(cx, |s, cx| { ...; cx.notify(); })` 调用 `notify` 时，GPUI 遍历订阅者列表，依次调用每个 callback。callback 接收 `&mut this`（订阅者自身）、`&model`（被观察者）、`&mut cx`（订阅者的 context）。

除了 `observe`，GPUI 还有 `subscribe`——订阅**事件**而非状态变化。区别：`observe` 在 `notify()` 时触发，传递 Model 的引用；`subscribe` 在 `emit(event)` 时触发，传递事件值。`observe` 适合"状态变了我就重绘"，`subscribe` 适合"发生了某事我就响应"。

### 例子

```rust
use gpui::*;
use std::time::{Duration, Instant};

// ===== 被观察的状态：时钟 =====
struct Clock {
    current_time: String,
}

impl Clock {
    fn new() -> Self {
        Self { current_time: "00:00:00".to_string() }
    }

    fn tick(&mut self, cx: &mut Context<Self>) {
        // 模拟时间更新
        let now = Instant::now();
        let secs = now.elapsed().as_secs();
        self.current_time = format!("{:02}:{:02}:{:02}", (secs / 3600) % 24, (secs / 60) % 60, secs % 60);
        cx.notify();
    }
}

// ===== 观察者 1：数字时钟 =====
struct DigitalClock {
    clock: Model<Clock>,
}

impl Render for DigitalClock {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        let time = self.clock.read(cx).current_time.clone();
        div()
            .px_6()
            .py_3()
            .bg(rgb(0x313244))
            .rounded_md()
            .text_color(rgb(0xa6e3a1))
            .text_2xl()
            .child(format!("数字时钟: {}", time))
            // 观察时钟变化
            .observe(&self.clock, |this, clock, cx| {
                // 当 clock 调用 notify 时，这里被触发
                // this 是 &mut DigitalClock
                // clock 是 &Model<Clock>
                let _ = (this, clock);
                cx.notify();  // 触发自身重绘
            })
    }
}

// ===== 观察者 2：进度条时钟 =====
struct ProgressBarClock {
    clock: Model<Clock>,
    progress: f32,
}

impl ProgressBarClock {
    fn new(clock: Model<Clock>) -> Self {
        Self { clock, progress: 0.0 }
    }
}

impl Render for ProgressBarClock {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        let time = self.clock.read(cx).current_time.clone();
        div()
            .px_6()
            .py_3()
            .bg(rgb(0x313244))
            .rounded_md()
            .flex()
            .flex_col()
            .gap_2()
            .child(div().text_color(rgb(0xf9e2af)).child(format!("进度: {}", time)))
            .child(
                div()
                    .w(px(200.))
                    .h(px(10.))
                    .bg(rgb(0x1e1e2e))
                    .rounded_full()
                    .child(
                        div()
                            .w(px(200.0 * self.progress))
                            .h_full()
                            .bg(rgb(0xf9e2af))
                            .rounded_full()
                    )
            )
            .observe(&self.clock, |this, clock, cx| {
                // 从 clock 读取最新状态，更新自身进度
                let secs = clock.read(cx).current_time.len() as f32;
                this.progress = (secs / 8.0).min(1.0);
                cx.notify();
            })
    }
}

// ===== 根视图 =====
struct App {
    clock: Model<Clock>,
    digital: View<DigitalClock>,
    progress: View<ProgressBarClock>,
}

impl App {
    fn new(cx: &mut Context<Self>) -> Self {
        let clock = cx.new_model(|_| Clock::new());
        let clock_clone1 = clock.clone();
        let clock_clone2 = clock.clone();

        let app = Self {
            clock: clock.clone(),
            digital: cx.new(|_| DigitalClock { clock: clock_clone1 }),
            progress: cx.new(|_| ProgressBarClock::new(clock_clone2)),
        };

        // 启动定时器，每秒更新时钟
        let clock_for_timer = clock.clone();
        cx.spawn(async move |cx| {
            loop {
                cx.background_executor().timer(Duration::from_secs(1)).await;
                let _ = cx.update(|cx| {
                    clock_for_timer.update(cx, |clock, cx| clock.tick(cx));
                });
            }
        }).detach();

        app
    }
}

impl Render for App {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .size_full()
            .flex()
            .flex_col()
            .items_center()
            .justify_center()
            .gap_4()
            .bg(rgb(0x1e1e2e))
            .child(self.digital.clone())
            .child(self.progress.clone())
    }
}

fn main() {
    Application::new().run(|cx: &mut App| {
        cx.open_window(WindowOptions::default(), |_window, cx| {
            cx.new(|cx| App::new(cx))
        })
        .unwrap();
        cx.activate(true);
    });
}
```

观察者模式数据流：

```
        ┌─────────────────────────────────────┐
        │  Model<Clock>                       │
        │  current_time: "12:34:56"           │
        │                                     │
        │  tick() → cx.notify() ──────┐       │
        └─────────────────────────────┼───────┘
                                      │
                    ┌─────────────────┼─────────────────┐
                    │                 │                 │
                    ▼                 ▼                 ▼
          ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
          │ DigitalClock │  │ProgressBarClock│ │ 其他观察者   │
          │ (View)       │  │ (View)         │ │              │
          │              │  │                │ │              │
          │ observe      │  │ observe        │ │ observe      │
          │ callback     │  │ callback       │ │ callback     │
          │ ↓            │  │ ↓              │ │ ↓            │
          │ cx.notify()  │  │ 更新 progress  │ │ cx.notify()  │
          │ → 重绘       │  │ cx.notify()    │ │ → 重绘       │
          └──────────────┘  │ → 重绘         │ └──────────────┘
                            └──────────────┘
```

### 总结

- `Context<T>` 是 View/Model 的万能句柄，提供状态访问、订阅、调度等能力
- `cx.observe(&model, |this, model, cx| { ... })` 订阅 Model 变化，`notify` 时触发
- `observe` 适合"状态变化触发重绘"，`subscribe` 适合"事件发生触发响应"
- Context 的泛型参数 `T` 保证类型安全的状态访问
- **常见坑**：`observe` 的闭包内必须调用 `cx.notify()` 才会重绘；`observe` 注册的闭包在每次 `notify` 都触发，注意性能；`cx.spawn` 启动的异步任务需通过 `cx.update` 修改状态

---

## 第 12 讲：Entity 订阅与通知

### 概念

GPUI 的 **Entity** 是 Model 和 View 的统称——两者都是"实体"，都可通过 `Entity` trait 统一处理。**订阅（subscribe）** 用于监听 Entity 发出的**事件**（event），与 `observe` 监听状态变化不同。Entity 通过 `cx.emit(event)` 发出事件，订阅者通过 `cx.subscribe(&entity, |this, entity, event, cx| { ... })` 接收。这种"事件驱动"模式适合处理"发生了某事"的逻辑，如"用户登录成功"、"文件加载完成"。

### 原理

GPUI 的事件系统基于"事件类型"而非"状态变化"。`cx.emit(EventType)` 将事件发送给所有订阅者，订阅者通过 `cx.subscribe` 注册的闭包接收事件。事件类型必须是 `'static + Send` 的类型，通常用枚举定义。一个 Entity 可发射多种事件，订阅者用 `match` 区分。

`subscribe` 与 `observe` 的区别：`observe` 在每次 `notify()` 时触发，不传递具体信息，适合"重绘即可"的场景；`subscribe` 在 `emit(event)` 时触发，传递具体事件值，适合"根据事件类型做不同响应"的场景。例如，一个文件加载器 Model 可发射 `Loaded(String)`、`Error(String)` 两种事件，订阅者根据事件类型更新 UI。

Entity 的设计让 GPUI 的状态管理更灵活——你可以将"业务逻辑"（如文件加载、网络请求）封装在 Model 中，通过事件通知 View 更新，实现关注点分离。这种模式与 Elm 架构、Flux/Redux 等前端状态管理方案异曲同工。

### 例子

```rust
use gpui::*;
use std::path::PathBuf;

// ===== 事件类型 =====
#[derive(Clone, Debug)]
enum FileEvent {
    Loading,
    Loaded(String),
    Error(String),
}

// ===== 文件加载器 Model =====
struct FileLoader {
    path: PathBuf,
    content: Option<String>,
    status: String,
}

impl FileLoader {
    fn new(path: PathBuf) -> Self {
        Self {
            path,
            content: None,
            status: "空闲".to_string(),
        }
    }

    fn load(&mut self, cx: &mut Context<Self>) {
        self.status = format!("加载中: {:?}", self.path);
        cx.emit(FileEvent::Loading);
        cx.notify();

        // 模拟异步加载
        let path = self.path.clone();
        cx.spawn(async move |cx| {
            // 模拟耗时操作
            cx.background_executor().timer(std::time::Duration::from_secs(1)).await;

            // 模拟读取结果
            let result = if path.exists() {
                std::fs::read_to_string(&path).map_err(|e| e.to_string())
            } else {
                Err(format!("文件不存在: {:?}", path))
            };

            let _ = cx.update(|cx| {
                match result {
                    Ok(content) => {
                        cx.emit(FileEvent::Loaded(content.clone()));
                        cx.update(|cx| {
                            // 更新自身状态
                        });
                    }
                    Err(e) => {
                        cx.emit(FileEvent::Error(e));
                    }
                }
            });
        }).detach();
    }
}

impl Render for FileLoader {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .px_4()
            .py_2()
            .bg(rgb(0x313244))
            .text_color(rgb(0xcdd6f4))
            .child(self.status.clone())
    }
}

// ===== 订阅者视图 =====
struct FileViewer {
    loader: Model<FileLoader>,
    content: String,
    error: Option<String>,
    is_loading: bool,
}

impl FileViewer {
    fn new(loader: Model<FileLoader>) -> Self {
        Self {
            loader,
            content: String::new(),
            error: None,
            is_loading: false,
        }
    }
}

impl Render for FileViewer {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        let loader = self.loader.clone();
        div()
            .size_full()
            .flex()
            .flex_col()
            .gap_2()
            .p_4()
            .bg(rgb(0x1e1e2e))
            .text_color(rgb(0xcdd6f4))
            // 订阅 loader 的事件
            .subscribe(&self.loader, |this, _loader, event: &FileEvent, cx| {
                match event {
                    FileEvent::Loading => {
                        this.is_loading = true;
                        this.error = None;
                        cx.notify();
                    }
                    FileEvent::Loaded(content) => {
                        this.is_loading = false;
                        this.content = content.clone();
                        this.error = None;
                        cx.notify();
                    }
                    FileEvent::Error(e) => {
                        this.is_loading = false;
                        this.error = Some(e.clone());
                        cx.notify();
                    }
                }
            })
            .child(
                div()
                    .flex()
                    .gap_2()
                    .child(
                        div()
                            .px_4()
                            .py_2()
                            .bg(rgb(0x89b4fa))
                            .text_color(rgb(0x1e1e2e))
                            .rounded_md()
                            .cursor_pointer()
                            .child("加载文件")
                            .on_click(cx.listener(move |_this, _event, _window, cx| {
                                loader.update(cx, |loader, cx| loader.load(cx));
                            }))
                    )
            )
            .child(
                if self.is_loading {
                    div().text_color(rgb(0xf9e2af)).child("加载中...").into_any_element()
                } else if let Some(e) = &self.error {
                    div().text_color(rgb(0xf38ba8)).child(format!("错误: {}", e)).into_any_element()
                } else {
                    div()
                        .p_3()
                        .bg(rgb(0x313244))
                        .rounded_md()
                        .text_color(rgb(0xa6e3a1))
                        .child(if self.content.is_empty() {
                            "(无内容)".to_string()
                        } else {
                            self.content.clone()
                        })
                        .into_any_element()
                }
            )
    }
}

// ===== 根视图 =====
struct App {
    viewer: View<FileViewer>,
}

impl App {
    fn new(cx: &mut Context<Self>) -> Self {
        let loader = cx.new_model(|_| FileLoader::new(PathBuf::from("test.txt")));
        Self {
            viewer: cx.new(|_| FileViewer::new(loader)),
        }
    }
}

impl Render for App {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        div().size_full().child(self.viewer.clone())
    }
}

fn main() {
    Application::new().run(|cx: &mut App| {
        cx.open_window(WindowOptions::default(), |_window, cx| {
            cx.new(|cx| App::new(cx))
        })
        .unwrap();
        cx.activate(true);
    });
}
```

事件流：

```
FileLoader (Model)                  FileViewer (View)
    │                                     │
    │ load() 被调用                        │
    │   ↓                                 │
    │ cx.emit(FileEvent::Loading) ───────►│ subscribe 闭包触发
    │                                     │   match FileEvent::Loading
    │                                     │   → is_loading = true
    │                                     │   → cx.notify() → 重绘
    │                                     │
    │ (异步加载完成)                       │
    │   ↓                                 │
    │ cx.emit(FileEvent::Loaded(content))─►│ subscribe 闭包触发
    │                                     │   match FileEvent::Loaded(content)
    │                                     │   → content = content
    │                                     │   → cx.notify() → 重绘
```

### 总结

- Entity 是 Model 和 View 的统称，都支持 `emit`/`subscribe` 事件机制
- `cx.emit(Event)` 发出事件，`cx.subscribe(&entity, |this, entity, event, cx| { ... })` 接收
- `subscribe` 传递具体事件值，适合"根据事件类型做不同响应"
- `observe` 在 `notify` 时触发，`subscribe` 在 `emit` 时触发，二者用途不同
- **常见坑**：事件类型必须 `Clone + Debug + 'static + Send`；`subscribe` 闭包的 `event` 参数是引用 `&Event`；一个 Entity 可发射多种事件，用枚举统一管理

---

## 第 13 讲：全局状态与 AppContext

### 概念

当应用状态需要跨多个 View 共享时，逐层传递 Model 句柄会变得繁琐。GPUI 提供**全局状态**（global state）机制——通过 `cx.set_global(state)` 注册全局状态，`cx.global::<StateType>()` 访问。全局状态适合"应用级"配置，如主题、用户偏好、当前登录用户等。`AppContext`（`App`）是所有 Context 的根，提供应用级 API：窗口管理、全局状态、定时器、文件系统等。

### 原理

GPUI 的全局状态存储在 `App` 中，用一个 `HashMap<TypeId, Box<dyn Any>>` 存储。`cx.set_global::<T>(value)` 将类型 `T` 的实例存入，`cx.global::<T>()` 按 `TypeId` 取出。这种"类型即键"的设计让全局状态类型安全——编译器保证你取出的类型与存入的一致。

全局状态的修改需通过 `cx.update_global::<T>(|state, cx| { ... })`，闭包内可修改状态并触发通知。所有"观察"该全局状态的 View 会自动重绘。全局状态适合"单一实例"的应用级状态，不适合"多个实例"的业务数据（如多个文档、多个标签页）——后者应用 Model。

`App` 是所有 Context 的"根"，提供应用级 API：`cx.open_window()` 创建窗口、`cx.windows()` 列出窗口、`cx.spawn()` 启动异步任务、`cx.background_executor()` 获取后台执行器。`Context<T>` 内部持有 `App` 的引用，因此所有 App 的 API 都可通过 Context 访问。

### 例子

```rust
use gpui::*;

// ===== 全局状态 1：主题 =====
#[derive(Clone, Debug)]
struct Theme {
    bg: Hsla,
    surface: Hsla,
    text: Hsla,
    primary: Hsla,
    is_dark: bool,
}

impl Theme {
    fn dark() -> Self {
        Self {
            bg: rgb(0x1e1e2e),
            surface: rgb(0x313244),
            text: rgb(0xcdd6f4),
            primary: rgb(0x89b4fa),
            is_dark: true,
        }
    }

    fn light() -> Self {
        Self {
            bg: rgb(0xeff1f5),
            surface: rgb(0xe6e9ef),
            text: rgb(0x4c4f69),
            primary: rgb(0x1e66f5),
            is_dark: false,
        }
    }

    fn toggle(&mut self) {
        *self = if self.is_dark { Self::light() } else { Self::dark() };
    }
}

// ===== 全局状态 2：用户信息 =====
#[derive(Clone, Debug)]
struct User {
    name: String,
    logged_in: bool,
}

// ===== 使用全局状态的视图 =====
struct ThemedApp {
    click_count: usize,
}

impl ThemedApp {
    fn new() -> Self {
        Self { click_count: 0 }
    }
}

impl Render for ThemedApp {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        // 读取全局主题
        let theme = cx.global::<Theme>().clone();

        div()
            .size_full()
            .flex()
            .flex_col()
            .items_center()
            .justify_center()
            .gap_4()
            .bg(theme.bg)
            .text_color(theme.text)
            // 观察全局主题变化
            .observe_global::<Theme>(cx, |this, cx| {
                let _ = this;
                cx.notify();
            })
            .child(
                div()
                    .text_3xl()
                    .child(format!("点击次数: {}", self.click_count))
            )
            // 主题切换按钮
            .child(
                div()
                    .px_6()
                    .py_3()
                    .bg(theme.primary)
                    .text_color(theme.bg)
                    .rounded_md()
                    .cursor_pointer()
                    .child(if theme.is_dark { "切换到亮色" } else { "切换到暗色" })
                    .on_click(cx.listener(|this, _event, _window, cx| {
                        this.click_count += 1;
                        // 修改全局主题
                        cx.update_global::<Theme>(|theme, cx| {
                            theme.toggle();
                            cx.notify();
                        });
                        cx.notify();
                    }))
            )
            // 显示当前主题信息
            .child(
                div()
                    .px_4()
                    .py_2()
                    .bg(theme.surface)
                    .rounded_md()
                    .text_sm()
                    .child(format!("当前主题: {}", if theme.is_dark { "暗色" } else { "亮色" }))
            )
    }
}

// ===== 根视图 =====
struct App;

impl Render for App {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        let theme = cx.global::<Theme>().clone();
        div()
            .size_full()
            .bg(theme.bg)
            .child(
                div()
                    .flex()
                    .flex_col()
                    .size_full()
                    .child(
                        // 嵌套 ThemedApp
                        div().flex_1().flex().items_center().justify_center()
                    )
            )
    }
}

fn main() {
    Application::new()
        .with_global(Theme::dark())  // 注册全局状态
        .run(|cx: &mut App| {
            cx.open_window(WindowOptions::default(), |_window, cx| {
                cx.new(|_cx| ThemedApp::new())
            })
            .unwrap();
            cx.activate(true);
        });
}
```

全局状态架构：

```
┌─────────────────────────────────────────────────────────┐
│                    App (AppContext)                      │
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │  全局状态存储 (HashMap<TypeId, Box<dyn Any>>)    │   │
│  │                                                 │   │
│  │  TypeId::of::<Theme>()  → Theme { ... }         │   │
│  │  TypeId::of::<User>()   → User { ... }          │   │
│  │  TypeId::of::<Settings>() → Settings { ... }    │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐       │
│  │ Window 1   │  │ Window 2   │  │ Window 3   │       │
│  │            │  │            │  │            │       │
│  │ ┌────────┐ │  │ ┌────────┐ │  │ ┌────────┐ │       │
│  │ │ View A │ │  │ │ View B │ │  │ │ View C │ │       │
│  │ │        │ │  │ │        │ │  │ │        │ │       │
│  │ │ global │◄├──┤ │ global │◄├──┤ │ global │◄┤       │
│  │ │::<Theme>│ │  │ ::<Theme>│ │  │ ::<Theme>│ │       │
│  │ └────────┘ │  │ └────────┘ │  │ └────────┘ │       │
│  └────────────┘  └────────────┘  └────────────┘       │
│                                                         │
│  所有 View 共享同一份全局状态                            │
└─────────────────────────────────────────────────────────┘
```

### 总结

- 全局状态通过 `cx.set_global::<T>(value)` 注册，`cx.global::<T>()` 访问
- `cx.update_global::<T>(|state, cx| { ... })` 修改全局状态并触发通知
- `cx.observe_global::<T>(cx, |this, cx| { ... })` 订阅全局状态变化
- 全局状态适合"应用级单一实例"（主题、用户、设置），不适合"多实例业务数据"
- **常见坑**：全局状态类型必须实现 `Default` 或通过 `with_global` 初始化；`global()` 在未注册时会 panic，可用 `try_global()` 安全访问；过度使用全局状态会导致耦合，优先用 Model 传递

---

# 第 4 章：事件与交互

交互是 GUI 应用的核心价值。本章深入 GPUI 的各类事件处理：鼠标事件（点击、移动、滚轮）、键盘事件与快捷键、焦点管理（哪个控件接收输入）、拖拽与手势。掌握这些，你就能构建出响应灵敏、交互丰富的桌面应用。

## 第 14 讲：鼠标事件处理

### 概念

鼠标是最常用的输入设备之一。GPUI 提供完整的鼠标事件 API：`.on_click()`（点击）、`.on_mouse_down()`（按下）、`.on_mouse_up()`（释放）、`.on_mouse_move()`（移动）、`.on_scroll()`（滚轮）。每个事件处理器接收对应的事件对象（如 `&MouseDownEvent`），包含按键、坐标、修饰键等信息。通过这些事件，可实现点击、悬停、拖拽、缩放等交互。

### 原理

GPUI 的鼠标事件基于**命中测试 + 冒泡**机制。当鼠标事件发生时，GPUI 从元素树根开始，递归查找包含事件坐标的最深层元素，然后从该元素向上冒泡触发事件处理器。每个元素可通过 `.on_mouse_down()` 等方法注册处理器，处理器闭包接收 `&mut self`（通过 `cx.listener`）、事件对象、`&mut Window`、`&mut Context`。

鼠标事件对象包含丰富信息：`MouseDownEvent` 有 `button`（左/右/中键）、`position`（坐标）、`modifiers`（Ctrl/Shift/Alt/Cmd）、`click_count`（单击/双击/三击）。`MouseMoveEvent` 有 `position` 和 `delta`（移动增量）。`ScrollWheelEvent` 有 `delta`（滚动方向和量）。

事件冒泡可被 `cx.stop_propagation()` 阻止——在处理器内调用后，事件不再向上传播。这在嵌套元素都监听同一事件时有用，避免误触发。另外，`.on_click()` 与 `.on_mouse_down()` 的区别：`on_click` 只在完整点击（按下+释放且在同一元素）时触发，`on_mouse_down` 在按下瞬间触发。

### 例子

```rust
use gpui::*;

struct MouseDemo {
    last_click_pos: Point<Pixels>,
    last_mouse_pos: Point<Pixels>,
    is_pressed: bool,
    scroll_delta: f32,
    hover_state: [bool; 3],  // 三个区域的悬停状态
}

impl MouseDemo {
    fn new() -> Self {
        Self {
            last_click_pos: Default::default(),
            last_mouse_pos: Default::default(),
            is_pressed: false,
            scroll_delta: 0.0,
            hover_state: [false; 3],
        }
    }
}

impl Render for MouseDemo {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .size_full()
            .flex()
            .flex_col()
            .gap_4()
            .p_6()
            .bg(rgb(0x1e1e2e))
            .text_color(rgb(0xcdd6f4))
            // 全局鼠标移动监听
            .on_mouse_move(cx.listener(|this, event, _window, cx| {
                this.last_mouse_pos = event.position;
                cx.notify();
            }))
            // 全局滚轮监听
            .on_scroll_wheel(cx.listener(|this, event, _window, cx| {
                this.scroll_delta += event.delta.pixel_delta(Pixels(16.0)).y.0;
                cx.notify();
            }))
            .child(
                div()
                    .text_lg()
                    .child(format!(
                        "鼠标位置: ({:.0}, {:.0}) | 按下: {} | 滚动: {:.0}",
                        self.last_mouse_pos.x.0, self.last_mouse_pos.y.0,
                        self.is_pressed, self.scroll_delta
                    ))
            )
            // ===== 三个交互区域 =====
            .child(
                div()
                    .flex()
                    .gap_2()
                    .children((0..3).map(|i| {
                        let is_hover = self.hover_state[i];
                        let colors = [rgb(0xf38ba8), rgb(0xa6e3a1), rgb(0x89b4fa)];
                        div()
                            .size(px(100.))
                            .bg(if is_hover { colors[i].opacity(0.7) } else { colors[i] })
                            .rounded_md()
                            .flex()
                            .items_center()
                            .justify_center()
                            .text_color(rgb(0x1e1e2e))
                            .cursor_pointer()
                            .child(format!("区域 {}", i + 1))
                            .on_hover(cx.listener(move |this, state, _window, cx| {
                                this.hover_state[i] = state;
                                cx.notify();
                            }))
                            .on_mouse_down(
                                MouseButton::Left,
                                cx.listener(move |this, event, _window, cx| {
                                    this.last_click_pos = event.position;
                                    this.is_pressed = true;
                                    cx.notify();
                                })
                            )
                            .on_mouse_up(
                                MouseButton::Left,
                                cx.listener(|this, _event, _window, cx| {
                                    this.is_pressed = false;
                                    cx.notify();
                                })
                            )
                            .on_click(cx.listener(move |this, event, _window, cx| {
                                println!("区域 {} 被点击，次数: {}", i + 1, event.click_count);
                                if event.click_count >= 2 {
                                    println!("双击！");
                                }
                                let _ = this;
                            }))
                    }))
            )
            // ===== 右键菜单演示 =====
            .child(
                div()
                    .px_6()
                    .py_3()
                    .bg(rgb(0x313244))
                    .rounded_md()
                    .cursor_pointer()
                    .child("右键点击我")
                    .on_mouse_down(
                        MouseButton::Right,
                        cx.listener(|_this, event, _window, cx| {
                            println!("右键菜单应在 ({:.0}, {:.0}) 弹出", event.position.x.0, event.position.y.0);
                            cx.notify();
                        })
                    )
            )
            // ===== 滚轮区域 =====
            .child(
                div()
                    .h(px(100.))
                    .w(px(300.))
                    .bg(rgb(0x313244))
                    .rounded_md()
                    .p_3()
                    .overflow_y_scroll()
                    .child(
                        div().child(format!("滚动这里来改变滚动值\n当前: {:.0}", self.scroll_delta))
                    )
            )
    }
}

fn main() {
    Application::new().run(|cx: &mut App| {
        cx.open_window(WindowOptions::default(), |_window, cx| {
            cx.new(|_cx| MouseDemo::new())
        })
        .unwrap();
        cx.activate(true);
    });
}
```

鼠标事件类型一览：

```
┌─────────────────────────────────────────────────────────────┐
│                    GPUI 鼠标事件类型                         │
├──────────────────┬──────────────────────────────────────────┤
│ on_click         │ 完整点击（按下+释放同一元素）             │
│                  │ ClickEvent { click_count, button, ... }  │
├──────────────────┼──────────────────────────────────────────┤
│ on_mouse_down    │ 按下瞬间触发                             │
│                  │ MouseDownEvent { button, position, ... } │
├──────────────────┼──────────────────────────────────────────┤
│ on_mouse_up      │ 释放瞬间触发                             │
│                  │ MouseUpEvent { button, position, ... }   │
├──────────────────┼──────────────────────────────────────────┤
│ on_mouse_move    │ 鼠标移动时触发                           │
│                  │ MouseMoveEvent { position, ... }         │
├──────────────────┼──────────────────────────────────────────┤
│ on_hover         │ 鼠标进入/离开元素                        │
│                  │ 参数: bool (true=进入, false=离开)       │
├──────────────────┼──────────────────────────────────────────┤
│ on_scroll_wheel  │ 滚轮滚动                                 │
│                  │ ScrollWheelEvent { delta, position }     │
├──────────────────┼──────────────────────────────────────────┤
│ on_drag          │ 拖拽开始                                 │
│ on_drop          │ 拖拽释放                                 │
└──────────────────┴──────────────────────────────────────────┘
```

### 总结

- 鼠标事件通过 `.on_click()`、`.on_mouse_down()`、`.on_mouse_move()` 等绑定
- 事件对象包含 `button`、`position`、`modifiers`、`click_count` 等信息
- 事件基于命中测试 + 冒泡，`cx.stop_propagation()` 可阻止冒泡
- `on_click` 需完整点击（按下+释放同元素），`on_mouse_down` 按下即触发
- **常见坑**：`on_hover` 参数是 `bool` 不是事件对象；`on_scroll_wheel` 的 `delta` 需转换像素值；右键用 `MouseButton::Right`；移动事件频率高，闭包内避免重操作

---

## 第 15 讲：键盘事件与快捷键

### 概念

键盘事件是文本编辑、快捷键操作的基础。GPUI 提供 `.on_key_down()`、`.on_key_up()` 处理原始按键，`.on_action()` 处理高级"动作"（action）。**动作（Action）**是 GPUI 的快捷键抽象——定义一个类型代表某操作（如 `Save`、`Quit`），注册按键绑定（如 `Cmd+S` → `Save`），然后在元素上用 `.on_action::<Save>(handler)` 响应。这种设计让快捷键可配置、可复用。

### 原理

GPUI 的键盘处理分两层：① **低级按键事件**——`KeyDownEvent`/`KeyUpEvent`，包含 `keystroke`（键位 + 修饰键），适合处理原始输入；② **高级动作系统**——将按键组合映射为"动作"类型，元素注册动作处理器，实现快捷键解耦。

动作系统的核心是 `actions!` 宏，定义动作类型：

```rust
actions!(editor, [Save, Quit, Find, ToggleComment]);
```

这会生成 `Save`、`Quit` 等空结构体，每个实现 `Action` trait。然后在窗口或元素上注册按键绑定：

```rust
cx.bind_keys([
    KeyBinding::new("cmd-s", Save, None),
    KeyBinding::new("cmd-q", Quit, None),
    KeyBinding::new("cmd-f", Find, None),
]);
```

最后在元素上用 `.on_action::<Save>(cx.listener(|this, _action, _window, cx| { ... }))` 响应。当用户按 Cmd+S 时，GPUI 查找当前焦点元素链上第一个注册了 `Save` 处理器的元素，触发其闭包。这种"按键 → 动作 → 处理器"的分层让快捷键可全局配置，且支持上下文（如编辑器内 Cmd+S 保存文件，设置页 Cmd-S 保存设置）。

### 例子

```rust
use gpui::*;

// ===== 定义动作 =====
actions!(text_editor, [Save, Quit, Find, SelectAll, Delete]);

struct TextEditor {
    text: String,
    cursor_pos: usize,
    is_saved: bool,
    show_find: bool,
}

impl TextEditor {
    fn new() -> Self {
        Self {
            text: "Hello, GPUI!".to_string(),
            cursor_pos: 5,
            is_saved: false,
            show_find: false,
        }
    }

    fn save(&mut self, cx: &mut Context<Self>) {
        self.is_saved = true;
        println!("保存: {}", self.text);
        cx.notify();
    }

    fn delete(&mut self, cx: &mut Context<Self>) {
        if self.cursor_pos < self.text.len() {
            self.text.remove(self.cursor_pos);
            self.is_saved = false;
            cx.notify();
        }
    }

    fn select_all(&mut self, cx: &mut Context<Self>) {
        self.cursor_pos = self.text.len();
        println!("全选");
        cx.notify();
    }
}

impl Render for TextEditor {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .size_full()
            .flex()
            .flex_col()
            .gap_2()
            .p_4()
            .bg(rgb(0x1e1e2e))
            .text_color(rgb(0xcdd6f4))
            // ===== 注册动作处理器 =====
            .on_action::<Save>(cx.listener(|this, _action, _window, cx| {
                this.save(cx);
            }))
            .on_action::<Delete>(cx.listener(|this, _action, _window, cx| {
                this.delete(cx);
            }))
            .on_action::<SelectAll>(cx.listener(|this, _action, _window, cx| {
                this.select_all(cx);
            }))
            .on_action::<Find>(cx.listener(|this, _action, _window, cx| {
                this.show_find = !this.show_find;
                cx.notify();
            }))
            // ===== 低级键盘事件 =====
            .on_key_down(cx.listener(|this, event: &KeyDownEvent, _window, cx| {
                // 处理普通字符输入
                let key = &event.keystroke.key;
                if key.len() == 1 && !event.keystroke.modifiers.control {
                    self.text.insert(self.cursor_pos, key.chars().next().unwrap());
                    self.cursor_pos += 1;
                    self.is_saved = false;
                    cx.notify();
                }
            }))
            .child(
                div()
                    .flex()
                    .justify_between()
                    .child(div().text_lg().child("文本编辑器"))
                    .child(
                        div()
                            .text_sm()
                            .text_color(if self.is_saved { rgb(0xa6e3a1) } else { rgb(0xf9e2af) })
                            .child(if self.is_saved { "已保存" } else { "未保存" })
                    )
            )
            .child(
                div()
                    .p_3()
                    .bg(rgb(0x313244))
                    .rounded_md()
                    .child(self.text.clone())
            )
            .child(
                div()
                    .text_sm()
                    .text_color(rgb(0x6c7086))
                    .child(format!("光标位置: {}", self.cursor_pos))
            )
            // ===== 快捷键提示 =====
            .child(
                div()
                    .flex()
                    .gap_4()
                    .text_xs()
                    .text_color(rgb(0x6c7086))
                    .child(div().child("⌘S 保存"))
                    .child(div().child("⌫ 删除"))
                    .child(div().child("⌘A 全选"))
                    .child(div().child("⌘F 查找"))
            )
            // ===== 查找框（条件显示）=====
            .child(if self.show_find {
                div()
                    .p_2()
                    .bg(rgb(0x313244))
                    .rounded_md()
                    .child("查找: [____________]")
                    .into_any_element()
            } else {
                div().into_any_element()
            })
    }
}

fn main() {
    Application::new().run(|cx: &mut App| {
        // ===== 注册全局快捷键绑定 =====
        cx.bind_keys([
            KeyBinding::new("cmd-s", Save, None),
            KeyBinding::new("cmd-q", Quit, None),
            KeyBinding::new("cmd-f", Find, None),
            KeyBinding::new("cmd-a", SelectAll, None),
            KeyBinding::new("backspace", Delete, None),
        ]);

        cx.open_window(WindowOptions::default(), |_window, cx| {
            cx.new(|_cx| TextEditor::new())
        })
        .unwrap();
        cx.activate(true);
    });
}
```

动作系统数据流：

```
用户按下 Cmd+S
         │
         ▼
┌─────────────────────────────────────┐
│  GPUI 按键分发                      │
│  1. 解析 keystroke: cmd+s           │
│  2. 查找按键绑定: cmd-s → Save      │
│  3. 从焦点元素向上查找 on_action    │
│     ::<Save> 处理器                 │
└─────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────┐
│  元素树 (焦点链)                    │
│  div (根)                           │
│   └─ TextEditor (焦点)              │
│       └─ on_action::<Save>(handler) │
│           ↑ 找到！触发 handler       │
└─────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────┐
│  handler 执行                       │
│  cx.listener(|this, _action, cx| {  │
│      this.save(cx);                 │
│  })                                 │
└─────────────────────────────────────┘
```

### 总结

- 键盘事件分两层：低级 `on_key_down`/`on_key_up`，高级 `on_action::<T>`
- `actions!` 宏定义动作类型，`cx.bind_keys()` 注册按键绑定
- 动作系统让快捷键可配置、可复用，支持上下文（不同 View 响应同一动作）
- `KeyDownEvent` 包含 `keystroke`（key + modifiers），可处理原始输入
- **常见坑**：动作类型必须实现 `Action` trait（用 `actions!` 宏自动生成）；按键绑定需在 `Application::run` 早期注册；`on_action` 的处理器参数是 `&Action`（如 `&Save`）；焦点元素链上需有注册处理器才会响应

---

## 第 16 讲：焦点管理

### 概念

**焦点（Focus）**决定哪个控件接收键盘输入。同一时刻只有一个元素拥有焦点。GPUI 的焦点管理通过 `FocusHandle` 实现——元素调用 `cx.focus_handle()` 获取句柄，`.track_focus(&handle)` 让元素可被聚焦，`.on_focus()`/`.on_blur()` 监听焦点变化，`handle.focus(cx)` 主动获取焦点。焦点在元素间用 Tab 键切换，遵循 `tab_index` 顺序。

### 原理

GPUI 维护一个"焦点栈"（focus stack），栈顶是当前焦点元素。当用户点击可聚焦元素或按 Tab 时，焦点切换。`FocusHandle` 是焦点的句柄，每个可聚焦元素持有一个。`.track_focus(&handle)` 将元素注册为"可聚焦"，GPUI 在命中测试时考虑焦点切换。

焦点变化通过事件通知：`.on_focus(cx.listener(|this, _event, window, cx| { ... }))` 在获得焦点时触发，`.on_blur(...)` 在失去焦点时触发。主动获取焦点用 `handle.focus(cx)`，清除用 `handle.blur(cx)`。`window.focused(cx)` 查询当前焦点句柄。

Tab 导航的顺序由 `tab_index` 决定（类似 HTML 的 tabindex）。默认情况下，注册了 `track_focus` 的元素按 DOM 顺序参与 Tab 循环。可通过 `.tab_index(N)` 自定义顺序，`tab_index(0)` 表示不参与 Tab（但仍可点击聚焦）。

### 例子

```rust
use gpui::*;

struct FocusDemo {
    input1: FocusHandle,
    input2: FocusHandle,
    input3: FocusHandle,
    focused_input: Option<usize>,
    log: Vec<String>,
}

impl FocusDemo {
    fn new(cx: &mut Context<Self>) -> Self {
        Self {
            input1: cx.focus_handle(),
            input2: cx.focus_handle(),
            input3: cx.focus_handle(),
            focused_input: None,
            log: vec![],
        }
    }

    fn log(&mut self, msg: impl Into<String>, cx: &mut Context<Self>) {
        self.log.push(msg.into());
        if self.log.len() > 5 {
            self.log.remove(0);
        }
        cx.notify();
    }
}

impl Render for FocusDemo {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        let input1 = self.input1.clone();
        let input2 = self.input2.clone();
        let input3 = self.input3.clone();

        div()
            .size_full()
            .flex()
            .flex_col()
            .gap_4()
            .p_6()
            .bg(rgb(0x1e1e2e))
            .text_color(rgb(0xcdd6f4))
            .child(div().text_lg().child("焦点管理演示"))
            // ===== 三个输入框 =====
            .child(
                div()
                    .flex()
                    .flex_col()
                    .gap_2()
                    .child(
                        div()
                            .h(px(36.))
                            .px_3()
                            .flex()
                            .items_center()
                            .bg(rgb(0x313244))
                            .border_2()
                            .border_color(if self.focused_input == Some(1) { rgb(0x89b4fa) } else { rgb(0x45475a) })
                            .rounded_md()
                            .track_focus(&self.input1)
                            .on_focus(cx.listener(|this, _event, _window, cx| {
                                this.focused_input = Some(1);
                                this.log("输入框 1 获得焦点", cx);
                            }))
                            .on_blur(cx.listener(|this, _event, _window, cx| {
                                if this.focused_input == Some(1) {
                                    this.focused_input = None;
                                    this.log("输入框 1 失去焦点", cx);
                                }
                            }))
                            .child("输入框 1 (点击聚焦)")
                    )
                    .child(
                        div()
                            .h(px(36.))
                            .px_3()
                            .flex()
                            .items_center()
                            .bg(rgb(0x313244))
                            .border_2()
                            .border_color(if self.focused_input == Some(2) { rgb(0x89b4fa) } else { rgb(0x45475a) })
                            .rounded_md()
                            .track_focus(&self.input2)
                            .on_focus(cx.listener(|this, _event, _window, cx| {
                                this.focused_input = Some(2);
                                this.log("输入框 2 获得焦点", cx);
                            }))
                            .on_blur(cx.listener(|this, _event, _window, cx| {
                                if this.focused_input == Some(2) {
                                    this.focused_input = None;
                                    this.log("输入框 2 失去焦点", cx);
                                }
                            }))
                            .child("输入框 2 (点击聚焦)")
                    )
                    .child(
                        div()
                            .h(px(36.))
                            .px_3()
                            .flex()
                            .items_center()
                            .bg(rgb(0x313244))
                            .border_2()
                            .border_color(if self.focused_input == Some(3) { rgb(0x89b4fa) } else { rgb(0x45475a) })
                            .rounded_md()
                            .track_focus(&self.input3)
                            .on_focus(cx.listener(|this, _event, _window, cx| {
                                this.focused_input = Some(3);
                                this.log("输入框 3 获得焦点", cx);
                            }))
                            .on_blur(cx.listener(|this, _event, _window, cx| {
                                if this.focused_input == Some(3) {
                                    this.focused_input = None;
                                    this.log("输入框 3 失去焦点", cx);
                                }
                            }))
                            .child("输入框 3 (点击聚焦)")
                    )
            )
            // ===== 焦点控制按钮 =====
            .child(
                div()
                    .flex()
                    .gap_2()
                    .child(
                        div()
                            .px_4()
                            .py_2()
                            .bg(rgb(0x89b4fa))
                            .text_color(rgb(0x1e1e2e))
                            .rounded_md()
                            .cursor_pointer()
                            .child("聚焦输入框 1")
                            .on_click(cx.listener(move |_this, _event, _window, cx| {
                                input1.focus(cx);
                            }))
                    )
                    .child(
                        div()
                            .px_4()
                            .py_2()
                            .bg(rgb(0xa6e3a1))
                            .text_color(rgb(0x1e1e2e))
                            .rounded_md()
                            .cursor_pointer()
                            .child("聚焦输入框 2")
                            .on_click(cx.listener(move |_this, _event, _window, cx| {
                                input2.focus(cx);
                            }))
                    )
                    .child(
                        div()
                            .px_4()
                            .py_2()
                            .bg(rgb(0xf9e2af))
                            .text_color(rgb(0x1e1e2e))
                            .rounded_md()
                            .cursor_pointer()
                            .child("聚焦输入框 3")
                            .on_click(cx.listener(move |_this, _event, _window, cx| {
                                input3.focus(cx);
                            }))
                    )
            )
            // ===== 提示 =====
            .child(
                div()
                    .text_sm()
                    .text_color(rgb(0x6c7086))
                    .child("提示: 按 Tab 在输入框间切换，点击按钮主动聚焦")
            )
            // ===== 焦点日志 =====
            .child(
                div()
                    .p_3()
                    .bg(rgb(0x313244))
                    .rounded_md()
                    .flex()
                    .flex_col()
                    .gap_1()
                    .child(div().text_sm().text_color(rgb(0x6c7086)).child("焦点事件日志:"))
                    .children(self.log.iter().rev().map(|entry| {
                        div().text_xs().child(entry.clone())
                    }))
            )
    }
}

fn main() {
    Application::new().run(|cx: &mut App| {
        cx.open_window(WindowOptions::default(), |_window, cx| {
            cx.new(|cx| FocusDemo::new(cx))
        })
        .unwrap();
        cx.activate(true);
    });
}
```

### 总结

- `FocusHandle` 是焦点的句柄，`cx.focus_handle()` 创建
- `.track_focus(&handle)` 让元素可聚焦，`.on_focus()`/`.on_blur()` 监听变化
- `handle.focus(cx)` 主动获取焦点，`handle.blur(cx)` 清除
- Tab 键按 `tab_index` 顺序在可聚焦元素间切换
- **常见坑**：`FocusHandle` 需在 View 初始化时创建（`cx.focus_handle()`）；`on_focus`/`on_blur` 的参数是 `&FocusEvent`；焦点切换会先 blur 旧元素再 focus 新元素；`track_focus` 必须在元素上调用才生效

---

## 第 17 讲：拖拽与手势

### 概念

拖拽（drag and drop）是 GUI 常见交互——用户按住元素移动，释放时触发"放置"动作。GPUI 通过 `.on_drag()` 标记元素可拖拽，`.on_drop()` 标记元素可接收放置。拖拽过程中可显示"拖拽预览"（drag preview）。**手势**是更复杂的交互模式，如滑动、捏合缩放，GPUI 通过组合鼠标事件实现。

### 原理

GPUI 的拖拽基于"拖拽值"（drag value）——一个 `'static + Send + Sync` 的类型，代表被拖拽的数据。`.on_drag(cx.listener(|this, event, window, cx| { ...; DragValue::new(some_data) }))` 在拖拽开始时返回拖拽值。其他元素用 `.on_drop::<DragValueType>(handler)` 接收特定类型的拖拽值。

拖拽过程：① 用户在可拖拽元素上按下鼠标并移动，触发 `on_drag`，返回 `DragValue`；② GPUI 显示拖拽预览（可通过 `cx.set_drag_overlay(element)` 自定义）；③ 鼠标移动时，沿途的元素若注册了 `on_drop` 会高亮提示；④ 用户释放鼠标，目标元素的 `on_drop` 处理器被调用，接收 `DragValue`。

手势的实现更底层——通过组合 `on_mouse_down`、`on_mouse_move`、`on_mouse_up` 计算位移、速度，判断是点击、拖拽还是滑动。例如，"滑动"可定义为"按下后移动超过 10px 且持续移动"。GPUI 没有内置手势识别，需开发者基于鼠标事件自行实现。

### 例子

```rust
use gpui::*;
use std::fmt;

// ===== 拖拽值类型 =====
#[derive(Clone, Debug)]
struct CardData {
    id: usize,
    label: String,
}

impl fmt::Display for CardData {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "Card({})", self.label)
    }
}

// ===== 可拖拽的卡片 =====
struct DraggableCard {
    data: CardData,
    is_dragging: bool,
}

impl DraggableCard {
    fn new(id: usize, label: &str) -> Self {
        Self {
            data: CardData { id, label: label.to_string() },
            is_dragging: false,
        }
    }
}

impl Render for DraggableCard {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        let data = self.data.clone();
        div()
            .px_4()
            .py_3()
            .bg(if self.is_dragging { rgb(0x6c7086) } else { rgb(0x89b4fa) })
            .text_color(rgb(0x1e1e2e))
            .rounded_md()
            .cursor_pointer()
            .child(self.data.label.clone())
            .on_drag(cx.listener(move |_this, _event, _window, cx| {
                // 返回拖拽值
                cx.stop_propagation();
                cx.new_view(|_| DragOverlay { data: data.clone() });
            }))
    }
}

// ===== 拖拽预览 =====
struct DragOverlay {
    data: CardData,
}

impl Render for DragOverlay {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .px_4()
            .py_3()
            .bg(rgb(0xf9e2af))
            .text_color(rgb(0x1e1e2e))
            .rounded_md()
            .opacity(0.8)
            .child(format!("拖拽中: {}", self.data.label))
    }
}

// ===== 放置区域 =====
struct DropZone {
    label: String,
    dropped_items: Vec<CardData>,
    is_hovered: bool,
}

impl DropZone {
    fn new(label: &str) -> Self {
        Self {
            label: label.to_string(),
            dropped_items: vec![],
            is_hovered: false,
        }
    }
}

impl Render for DropZone {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .flex_1()
            .min_h(px(200.))
            .p_4()
            .bg(if self.is_hovered { rgb(0x45475a) } else { rgb(0x313244) })
            .border_2()
            .border_color(if self.is_hovered { rgb(0xa6e3a1) } else { rgb(0x45475a) })
            .border_dashed()
            .rounded_md()
            .flex()
            .flex_col()
            .gap_2()
            .child(div().text_lg().child(self.label.clone()))
            .children(self.dropped_items.iter().map(|item| {
                div()
                    .px_3()
                    .py_2()
                    .bg(rgb(0xa6e3a1))
                    .text_color(rgb(0x1e1e2e))
                    .rounded_md()
                    .child(item.label.clone())
            }))
            // 接收 CardData 类型的拖拽
            .on_drop::<CardData>(cx.listener(|this, event, _window, cx| {
                this.dropped_items.push(event.value.clone());
                cx.notify();
            }))
            .on_drag_move(cx.listener(|this, _event, _window, cx| {
                if !this.is_hovered {
                    this.is_hovered = true;
                    cx.notify();
                }
            }))
            .on_drop(cx.listener(|this, _event, _window, cx| {
                this.is_hovered = false;
                cx.notify();
            }))
    }
}

// ===== 根视图 =====
struct App {
    cards: Vec<View<DraggableCard>>,
    zone_a: View<DropZone>,
    zone_b: View<DropZone>,
}

impl App {
    fn new(cx: &mut Context<Self>) -> Self {
        Self {
            cards: vec![
                cx.new(|_| DraggableCard::new(1, "卡片 A")),
                cx.new(|_| DraggableCard::new(2, "卡片 B")),
                cx.new(|_| DraggableCard::new(3, "卡片 C")),
            ],
            zone_a: cx.new(|_| DropZone::new("放置区 1")),
            zone_b: cx.new(|_| DropZone::new("放置区 2")),
        }
    }
}

impl Render for App {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .size_full()
            .flex()
            .flex_col()
            .gap_4()
            .p_6()
            .bg(rgb(0x1e1e2e))
            .text_color(rgb(0xcdd6f4))
            .child(div().text_lg().child("拖拽演示: 拖动卡片到放置区"))
            .child(
                div()
                    .flex()
                    .gap_2()
                    .children(self.cards.iter().map(|card| card.clone()))
            )
            .child(
                div()
                    .flex()
                    .gap_4()
                    .child(self.zone_a.clone())
                    .child(self.zone_b.clone())
            )
    }
}

fn main() {
    Application::new().run(|cx: &mut App| {
        cx.open_window(WindowOptions::default(), |_window, cx| {
            cx.new(|cx| App::new(cx))
        })
        .unwrap();
        cx.activate(true);
    });
}
```

拖拽流程：

```
1. 用户在卡片上按下鼠标
   ┌──────────┐
   │ 卡片 A    │ ← on_drag 触发
   └──────────┘   返回 DragValue(CardData { ... })

2. 鼠标移动，GPUI 显示拖拽预览
   ┌──────────┐
   │ 拖拽中: A │ ← DragOverlay 跟随鼠标
   └──────────┘

3. 鼠标进入放置区，区域高亮
   ┌─────────────────┐
   │ 放置区 1         │ ← on_drag_move 触发
   │ (高亮边框)       │   is_hovered = true
   └─────────────────┘

4. 用户释放鼠标
   ┌─────────────────┐
   │ 放置区 1         │ ← on_drop::<CardData> 触发
   │ 卡片 A           │   dropped_items.push(data)
   └─────────────────┘
```

### 总结

- `.on_drag(handler)` 标记元素可拖拽，handler 返回 `DragValue`
- `.on_drop::<T>(handler)` 接收特定类型的拖拽值
- 拖拽预览通过 `cx.new_view` 创建 `DragOverlay` 视图
- `.on_drag_move()` 监听拖拽经过，用于高亮提示
- **常见坑**：拖拽值类型必须 `Clone + Send + Sync + 'static`；`on_drop` 需指定泛型类型匹配拖拽值；拖拽预览是独立 View，不与原元素共享状态；手势需基于 `on_mouse_*` 自行实现

---

# 第 5 章：高级 UI

本章关注 GPUI 的高级 UI 能力：动画让界面"活"起来，主题系统支持深色/浅色切换，SVG 与图标增强视觉表达，自定义 Element 让你突破内置元素的限制。这些能力让你的应用从"能用"提升到"好用"。

## 第 18 讲：动画系统

### 概念

**动画（Animation）**让 UI 元素的属性（位置、大小、颜色、透明度等）随时间平滑变化，提升用户体验。GPUI 的动画通过 `Animation` 类型和 `.with_animation(name, animation, |style| { ... })` 方法实现。动画基于"关键帧"——定义起始和结束状态，GPUI 在指定时长内插值过渡。支持缓动函数（easing）控制变化节奏。

### 原理

GPUI 的动画系统基于"每帧重绘 + 插值"。当调用 `.with_animation("my_anim", Animation::new(Duration::from_millis(300)), |style| { style.set_scale(Some(scale)); })` 时，GPUI 在动画期间每帧重绘元素，根据当前时间计算插值后的属性值。`Animation::new(duration)` 创建指定时长的动画，`.with_easing(easing)` 设置缓动函数（如 `EaseInOut`）。

动画的状态（进行中/已完成）由 GPUI 内部追踪。动画完成后，元素保持最终状态。若需循环动画，用 `.repeat(Repeat::Infinitely)`。动画的"名称"（如 `"my_anim"`）用于标识，同一元素可有多个不同名称的动画并行。

动画的触发通常与状态变化绑定——在 `on_click` 等事件中修改状态，`render` 中根据状态决定动画参数。例如，点击按钮时 `is_expanded = true`，`render` 中 `if is_expanded { .with_animation("expand", ...) }`。注意动画需在每帧重新调用 `.with_animation`，GPUI 会自动追踪是否已完成。

### 例子

```rust
use gpui::*;
use std::time::Duration;

struct AnimationDemo {
    is_expanded: bool,
    is_visible: bool,
    rotation: f32,
    scale: f32,
    animation_count: usize,
}

impl AnimationDemo {
    fn new() -> Self {
        Self {
            is_expanded: false,
            is_visible: true,
            rotation: 0.0,
            scale: 1.0,
            animation_count: 0,
        }
    }
}

impl Render for AnimationDemo {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .size_full()
            .flex()
            .flex_col()
            .gap_4()
            .p_6()
            .bg(rgb(0x1e1e2e))
            .text_color(rgb(0xcdd6f4))
            .child(div().text_lg().child("动画演示"))
            // ===== 展开/收起动画 =====
            .child(
                div()
                    .flex()
                    .gap_2()
                    .child(
                        div()
                            .px_4()
                            .py_2()
                            .bg(rgb(0x89b4fa))
                            .text_color(rgb(0x1e1e2e))
                            .rounded_md()
                            .cursor_pointer()
                            .child(if self.is_expanded { "收起" } else { "展开" })
                            .on_click(cx.listener(|this, _event, _window, cx| {
                                this.is_expanded = !this.is_expanded;
                                this.animation_count += 1;
                                cx.notify();
                            }))
                    )
            )
            .child(
                div()
                    .h(px(80.))
                    .w_full()
                    .bg(rgb(0x313244))
                    .rounded_md()
                    .overflow_hidden()
                    .child(
                        div()
                            .h_full()
                            .w(if self.is_expanded { px(400.) } else { px(100.) })
                            .bg(rgb(0xa6e3a1))
                            .rounded_md()
                            .flex()
                            .items_center()
                            .justify_center()
                            .text_color(rgb(0x1e1e2e))
                            .child(if self.is_expanded { "已展开" } else { "已收起" })
                            // 宽度变化动画
                            .with_animation(
                                "width_anim",
                                Animation::new(Duration::from_millis(300)),
                                |style, _| {
                                    // 动画期间自动插值
                                    let _ = style;
                                }
                            )
                    )
            )
            // ===== 缩放动画 =====
            .child(
                div()
                    .flex()
                    .gap_4()
                    .items_center()
                    .child(
                        div()
                            .size(px(80.))
                            .bg(rgb(0xf38ba8))
                            .rounded_md()
                            .flex()
                            .items_center()
                            .justify_center()
                            .text_color(rgb(0x1e1e2e))
                            .child("缩放")
                            .with_animation(
                                "scale_anim",
                                Animation::new(Duration::from_millis(500))
                                    .with_easing(EaseInOut),
                                |style, _| {
                                    style.set_scale(Some(self.scale));
                                }
                            )
                    )
                    .child(
                        div()
                            .flex()
                            .gap_2()
                            .child(
                                div()
                                    .px_3()
                                    .py_1()
                                    .bg(rgb(0x313244))
                                    .rounded_md()
                                    .cursor_pointer()
                                    .child("放大")
                                    .on_click(cx.listener(|this, _event, _window, cx| {
                                        this.scale = 1.5;
                                        cx.notify();
                                    }))
                            )
                            .child(
                                div()
                                    .px_3()
                                    .py_1()
                                    .bg(rgb(0x313244))
                                    .rounded_md()
                                    .cursor_pointer()
                                    .child("缩小")
                                    .on_click(cx.listener(|this, _event, _window, cx| {
                                        this.scale = 0.5;
                                        cx.notify();
                                    }))
                            )
                            .child(
                                div()
                                    .px_3()
                                    .py_1()
                                    .bg(rgb(0x313244))
                                    .rounded_md()
                                    .cursor_pointer()
                                    .child("重置")
                                    .on_click(cx.listener(|this, _event, _window, cx| {
                                        this.scale = 1.0;
                                        cx.notify();
                                    }))
                            )
                    )
            )
            // ===== 旋转动画（循环）=====
            .child(
                div()
                    .size(px(60.))
                    .bg(rgb(0xf9e2af))
                    .rounded_md()
                    .with_animation(
                        "rotation_anim",
                        Animation::new(Duration::from_secs(2))
                            .repeat(Repeat::Infinitely),
                        |style, _| {
                            style.set_angle(self.rotation);
                        }
                    )
                    .on_click(cx.listener(|this, _event, _window, cx| {
                        this.rotation += 90.0;
                        cx.notify();
                    }))
            )
            // ===== 淡入淡出 =====
            .child(
                div()
                    .flex()
                    .gap_2()
                    .items_center()
                    .child(
                        div()
                            .px_4()
                            .py_2()
                            .bg(rgb(0x313244))
                            .rounded_md()
                            .cursor_pointer()
                            .child(if self.is_visible { "隐藏" } else { "显示" })
                            .on_click(cx.listener(|this, _event, _window, cx| {
                                this.is_visible = !this.is_visible;
                                cx.notify();
                            }))
                    )
                    .child(
                        div()
                            .size(px(60.))
                            .bg(rgb(0x89b4fa))
                            .rounded_md()
                            .opacity(if self.is_visible { 1.0 } else { 0.0 })
                            .with_animation(
                                "fade_anim",
                                Animation::new(Duration::from_millis(400)),
                                |style, _| {
                                    let _ = style;
                                }
                            )
                    )
            )
            .child(
                div()
                    .text_sm()
                    .text_color(rgb(0x6c7086))
                    .child(format!("动画触发次数: {}", self.animation_count))
            )
    }
}

fn main() {
    Application::new().run(|cx: &mut App| {
        cx.open_window(WindowOptions::default(), |_window, cx| {
            cx.new(|_cx| AnimationDemo::new())
        })
        .unwrap();
        cx.activate(true);
    });
}
```

动画时间线：

```
时间 ──────────────────────────────────────────────────────►

动画开始 (t=0)
  │
  │  状态: scale = 1.0
  │  ┌────────┐
  │  │        │
  │  │  1.0x  │
  │  │        │
  │  └────────┘
  │
  ▼
t = 150ms (动画进行中)
  │
  │  状态: scale = 1.25 (插值)
  │  ┌──────────┐
  │  │          │
  │  │  1.25x   │
  │  │          │
  │  └──────────┘
  │
  ▼
t = 300ms (动画结束)
  │
  │  状态: scale = 1.5 (最终值)
  │  ┌────────────┐
  │  │            │
  │  │   1.5x     │
  │  │            │
  │  └────────────┘
  │
  ▼
  动画完成，保持最终状态

缓动函数影响插值节奏:
  Linear:      匀速变化
  EaseIn:      开始慢，后面快
  EaseOut:     开始快，后面慢
  EaseInOut:   两端慢，中间快 (最常用)
```

### 总结

- `Animation::new(duration)` 创建动画，`.with_easing()` 设置缓动函数
- `.with_animation(name, animation, |style, _| { ... })` 在元素上应用动画
- 动画每帧重绘并插值，`.repeat(Repeat::Infinitely)` 实现循环
- 动画通过状态变化触发——事件中改状态，`render` 中根据状态设动画参数
- **常见坑**：动画需每帧重新调用 `.with_animation`；动画名称用于标识，同名动画不重复触发；动画期间元素持续重绘，注意性能；`set_scale`/`set_angle` 等方法在闭包内修改 `Style`

---

## 第 19 讲：主题与样式系统

### 概念

**主题（Theme）**是应用视觉风格的统一抽象——颜色、字体、间距等集中定义，全局应用。GPUI 没有内置主题系统（不像 CSS 的变量），但可通过全局状态实现：将主题定义为结构体，注册为全局状态，所有 View 从中读取颜色。切换主题时更新全局状态，所有 View 自动重绘。这种模式让深色/浅色切换、用户自定义配色成为可能。

### 原理

主题系统的核心是"单一数据源 + 观察者模式"。主题结构体（如 `Theme { bg, surface, text, primary, ... }`）注册为全局状态，每个 View 在 `render` 时通过 `cx.global::<Theme>()` 读取当前主题颜色。当用户切换主题时，`cx.update_global::<Theme>(|theme, cx| { *theme = new_theme; cx.notify(); })` 更新全局状态并通知所有观察者。

`observe_global::<Theme>` 让 View 在主题变化时自动重绘。这种设计的好处是：① 主题切换即时生效，无需重新加载；② 主题定义集中，修改一处全局生效；③ 支持运行时动态主题（如用户自定义颜色）。

主题通常包含多组颜色：背景色（bg、surface、overlay）、文字色（text、text_muted、text_placeholder）、强调色（primary、secondary、accent）、状态色（success、warning、error、info）、边框色（border、border_subtle）。还可包含字体大小、圆角等设计 token。将主题与具体颜色分离，让 UI 代码更易维护——`bg(theme.surface)` 比 `bg(rgb(0x313244))` 更具语义。

### 例子

```rust
use gpui::*;

// ===== 主题定义 =====
#[derive(Clone, Debug)]
struct Theme {
    name: String,
    bg: Hsla,
    surface: Hsla,
    surface_hover: Hsla,
    text: Hsla,
    text_muted: Hsla,
    primary: Hsla,
    primary_hover: Hsla,
    success: Hsla,
    warning: Hsla,
    error: Hsla,
    border: Hsla,
    is_dark: bool,
}

impl Theme {
    fn dark() -> Self {
        Self {
            name: "Catppuccin Mocha".into(),
            bg: rgb(0x1e1e2e),
            surface: rgb(0x313244),
            surface_hover: rgb(0x45475a),
            text: rgb(0xcdd6f4),
            text_muted: rgb(0x6c7086),
            primary: rgb(0x89b4fa),
            primary_hover: rgb(0xb4befe),
            success: rgb(0xa6e3a1),
            warning: rgb(0xf9e2af),
            error: rgb(0xf38ba8),
            border: rgb(0x45475a),
            is_dark: true,
        }
    }

    fn light() -> Self {
        Self {
            name: "Catppuccin Latte".into(),
            bg: rgb(0xeff1f5),
            surface: rgb(0xe6e9ef),
            surface_hover: rgb(0xccd0da),
            text: rgb(0x4c4f69),
            text_muted: rgb(0x9ca0b0),
            primary: rgb(0x1e66f5),
            primary_hover: rgb(0x2a6ef5),
            success: rgb(0x40a02b),
            warning: rgb(0xdf8e1d),
            error: rgb(0xd20f39),
            border: rgb(0xccd0da),
            is_dark: false,
        }
    }

    fn gruvbox() -> Self {
        Self {
            name: "Gruvbox Dark".into(),
            bg: rgb(0x282828),
            surface: rgb(0x3c3836),
            surface_hover: rgb(0x504945),
            text: rgb(0xebdbb2),
            text_muted: rgb(0x928374),
            primary: rgb(0xfe8019),
            primary_hover: rgb(0xfb4934),
            success: rgb(0xb8bb26),
            warning: rgb(0xfabd2f),
            error: rgb(0xfb4934),
            border: rgb(0x504945),
            is_dark: true,
        }
    }
}

// ===== 主题管理器 =====
struct ThemeManager {
    themes: Vec<Theme>,
    current_index: usize,
}

impl ThemeManager {
    fn new() -> Self {
        Self {
            themes: vec![Theme::dark(), Theme::light(), Theme::gruvbox()],
            current_index: 0,
        }
    }

    fn current(&self) -> &Theme {
        &self.themes[self.current_index]
    }

    fn next(&mut self) {
        self.current_index = (self.current_index + 1) % self.themes.len();
    }
}

// ===== 按钮：使用主题 =====
struct ThemedButton {
    label: String,
    variant: ButtonVariant,
}

#[derive(Clone, Copy)]
enum ButtonVariant {
    Primary,
    Secondary,
    Danger,
}

impl ThemedButton {
    fn new(label: impl Into<String>, variant: ButtonVariant) -> Self {
        Self { label: label.into(), variant }
    }
}

impl Render for ThemedButton {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        let theme = cx.global::<Theme>().clone();
        let (bg, bg_hover, text_color) = match self.variant {
            ButtonVariant::Primary => (theme.primary, theme.primary_hover, theme.bg),
            ButtonVariant::Secondary => (theme.surface, theme.surface_hover, theme.text),
            ButtonVariant::Danger => (theme.error, theme.error, theme.bg),
        };

        div()
            .px_4()
            .py_2()
            .bg(bg)
            .text_color(text_color)
            .rounded_md()
            .cursor_pointer()
            .child(self.label.clone())
            .observe_global::<Theme>(cx, |this, cx| {
                let _ = this;
                cx.notify();
            })
    }
}

// ===== 主应用 =====
struct App {
    theme_manager: ThemeManager,
}

impl App {
    fn new() -> Self {
        Self { theme_manager: ThemeManager::new() }
    }
}

impl Render for App {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        let theme = self.theme_manager.current().clone();
        let theme_name = theme.name.clone();
        let is_dark = theme.is_dark;

        div()
            .size_full()
            .flex()
            .flex_col()
            .gap_4()
            .p_6()
            .bg(theme.bg)
            .text_color(theme.text)
            .child(
                div()
                    .flex()
                    .justify_between()
                    .child(div().text_2xl().child("主题系统演示"))
                    .child(
                        div()
                            .px_4()
                            .py_2()
                            .bg(theme.surface)
                            .text_color(theme.text)
                            .rounded_md()
                            .cursor_pointer()
                            .child(format!("切换主题 (当前: {})", theme_name))
                            .on_click(cx.listener(|this, _event, _window, cx| {
                                this.theme_manager.next();
                                let new_theme = this.theme_manager.current().clone();
                                cx.update_global::<Theme>(|theme, cx| {
                                    *theme = new_theme;
                                    cx.notify();
                                });
                            }))
                    )
            )
            // ===== 主题色板展示 =====
            .child(
                div()
                    .flex()
                    .gap_2()
                    .children([
                        ("bg", theme.bg),
                        ("surface", theme.surface),
                        ("primary", theme.primary),
                        ("success", theme.success),
                        ("warning", theme.warning),
                        ("error", theme.error),
                    ].iter().map(|(name, color)| {
                        div()
                            .flex()
                            .flex_col()
                            .gap_1()
                            .child(div().size(px(60.)).bg(*color).rounded_md())
                            .child(div().text_xs().text_color(theme.text_muted).child(name.to_string()))
                    }))
            )
            // ===== 主题化按钮 =====
            .child(
                div()
                    .flex()
                    .gap_2()
                    .child(
                        div()
                            .px_4()
                            .py_2()
                            .bg(theme.primary)
                            .text_color(theme.bg)
                            .rounded_md()
                            .cursor_pointer()
                            .child("Primary 按钮")
                    )
                    .child(
                        div()
                            .px_4()
                            .py_2()
                            .bg(theme.surface)
                            .text_color(theme.text)
                            .rounded_md()
                            .cursor_pointer()
                            .child("Secondary 按钮")
                    )
                    .child(
                        div()
                            .px_4()
                            .py_2()
                            .bg(theme.error)
                            .text_color(theme.bg)
                            .rounded_md()
                            .cursor_pointer()
                            .child("Danger 按钮")
                    )
            )
            // ===== 主题化卡片 =====
            .child(
                div()
                    .p_4()
                    .bg(theme.surface)
                    .border_1()
                    .border_color(theme.border)
                    .rounded_md()
                    .flex()
                    .flex_col()
                    .gap_2()
                    .child(div().text_lg().child("主题化卡片"))
                    .child(div().text_color(theme.text_muted).child(format!("当前模式: {}", if is_dark { "深色" } else { "浅色" })))
                    .child(
                        div()
                            .text_sm()
                            .child("这个卡片使用了主题变量，切换主题时自动更新颜色。")
                    )
            )
            // ===== 输入框样式 =====
            .child(
                div()
                    .h(px(40.))
                    .px_3()
                    .flex()
                    .items_center()
                    .bg(theme.surface)
                    .border_1()
                    .border_color(theme.border)
                    .rounded_md()
                    .text_color(theme.text_muted)
                    .child("搜索...")
            )
    }
}

fn main() {
    Application::new()
        .with_global(Theme::dark())
        .run(|cx: &mut App| {
            cx.open_window(WindowOptions::default(), |_window, cx| {
                cx.new(|_cx| App::new())
            })
            .unwrap();
            cx.activate(true);
        });
}
```

### 总结

- 主题通过全局状态实现：`Theme` 结构体注册为 global，View 用 `cx.global::<Theme>()` 读取
- `cx.update_global::<Theme>` 切换主题，`observe_global::<Theme>` 自动重绘
- 主题包含多组语义颜色（bg/surface/text/primary/success/warning/error/border）
- 用语义名（`theme.primary`）替代硬编码颜色（`rgb(0x89b4fa)`），提升可维护性
- **常见坑**：主题类型必须 `Clone`；`global()` 返回引用，需 `.clone()` 才能在闭包外使用；主题切换时所有 `observe_global` 的 View 都重绘，注意性能；主题字段用 `Hsla` 类型存储颜色

---

## 第 20 讲：SVG 与图标

### 概念

**SVG（可缩放矢量图）**是 GUI 中图标的理想格式——矢量描述，任意缩放不失真，文件小。GPUI 通过 `svg()` 元素加载 SVG 文件，支持设置大小、颜色（通过 `text_color` 影响 `fill`）。图标系统通常将常用图标打包，通过名称引用，如 `IconName::Search`、`IconName::Settings`。Zed 编辑器内置了完整的图标集。

### 原理

GPUI 的 `svg()` 元素接收 SVG 文件路径，内部解析 SVG XML，将其转为 GPU 可绘制的路径。SVG 的 `fill`/`stroke` 颜色可通过 `text_color()` 覆盖——这让单色图标能适应主题。`svg().path("assets/icon.svg").size(px(24.)).text_color(rgb(0xff0000))` 加载图标并设为红色。

图标系统的实现通常是一个枚举，每个变体对应一个 SVG 路径：

```rust
enum IconName {
    Search,
    Settings,
    Close,
    // ...
}

impl IconName {
    fn path(&self) -> &'static str {
        match self {
            IconName::Search => "icons/search.svg",
            IconName::Settings => "icons/settings.svg",
            // ...
        }
    }
}
```

然后用 `svg().path(icon.path()).size(px(16.))` 渲染。这种设计让图标使用统一、可搜索。Zed 使用的是 Phosphor Icons 图标集，提供上千个常用图标。

SVG 的优势在于：① 矢量缩放——16px、24px、32px 都清晰；② 主题适配——通过 `text_color` 改变颜色；③ 文件小——比 PNG 节省空间；④ 可编辑——SVG 是 XML 文本，可程序化生成或修改。

### 例子

```rust
use gpui::*;

// ===== 图标枚举 =====
#[derive(Clone, Copy)]
enum Icon {
    Search,
    Settings,
    Close,
    Plus,
    Check,
    Warning,
    Error,
    Info,
}

impl Icon {
    fn svg_path(&self) -> &'static str {
        match self {
            // 实际项目中这些路径指向真实 SVG 文件
            // 这里用内联 SVG 演示概念
            Icon::Search => "icons/search.svg",
            Icon::Settings => "icons/settings.svg",
            Icon::Close => "icons/close.svg",
            Icon::Plus => "icons/plus.svg",
            Icon::Check => "icons/check.svg",
            Icon::Warning => "icons/warning.svg",
            Icon::Error => "icons/error.svg",
            Icon::Info => "icons/info.svg",
        }
    }

    // 内联 SVG（用于演示，实际应加载文件）
    fn inline_svg(&self) -> &'static str {
        match self {
            Icon::Search => r#"<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M10 2a8 8 0 105.29 14.04l5.34 5.34 1.42-1.42-5.34-5.34A8 8 0 0010 2zm0 2a6 6 0 110 12 6 6 0 010-12z"/></svg>"#,
            Icon::Settings => r#"<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M12 8a4 4 0 100 8 4 4 0 000-8zm0 2a2 2 0 110 4 2 2 0 010-4z"/></svg>"#,
            Icon::Close => r#"<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M18.3 5.71L12 12l6.3 6.29-1.42 1.42L12 14.83l-6.29 6.3-1.42-1.42L10.59 12 4.29 5.71 5.71 4.29 12 10.59l6.29-6.3z"/></svg>"#,
            Icon::Plus => r#"<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M11 11V5h2v6h6v2h-6v6h-2v-6H5v-2z"/></svg>"#,
            Icon::Check => r#"<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M9 16.17L4.83 12l-1.42 1.41L9 19 21 7l-1.41-1.41z"/></svg>"#,
            _ => r#"<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><circle cx="12" cy="12" r="10"/></svg>"#,
        }
    }
}

// ===== 图标组件 =====
struct IconView {
    icon: Icon,
    size: Pixels,
    color: Hsla,
}

impl IconView {
    fn new(icon: Icon) -> Self {
        Self {
            icon,
            size: px(24.),
            color: rgb(0xcdd6f4),
        }
    }

    fn with_size(mut self, size: Pixels) -> Self {
        self.size = size;
        self
    }

    fn with_color(mut self, color: Hsla) -> Self {
        self.color = color;
        self
    }
}

impl Render for IconView {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        svg()
            .path(self.icon.svg_path())
            .size(self.size)
            .text_color(self.color)
            // 实际项目中 .path() 加载文件，这里用概念演示
    }
}

// ===== 主应用 =====
struct App {
    icons: Vec<(Icon, &'static str)>,
}

impl App {
    fn new() -> Self {
        Self {
            icons: vec![
                (Icon::Search, "搜索"),
                (Icon::Settings, "设置"),
                (Icon::Close, "关闭"),
                (Icon::Plus, "添加"),
                (Icon::Check, "确认"),
                (Icon::Warning, "警告"),
                (Icon::Error, "错误"),
                (Icon::Info, "信息"),
            ],
        }
    }
}

impl Render for App {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .size_full()
            .flex()
            .flex_col()
            .gap_4()
            .p_6()
            .bg(rgb(0x1e1e2e))
            .text_color(rgb(0xcdd6f4))
            .child(div().text_lg().child("SVG 与图标演示"))
            // ===== 图标网格 =====
            .child(
                div()
                    .flex()
                    .flex_wrap()
                    .gap_4()
                    .children(self.icons.iter().map(|(icon, label)| {
                        let color = match icon {
                            Icon::Warning => rgb(0xf9e2af),
                            Icon::Error => rgb(0xf38ba8),
                            Icon::Info => rgb(0x89b4fa),
                            Icon::Check => rgb(0xa6e3a1),
                            _ => rgb(0xcdd6f4),
                        };
                        div()
                            .flex()
                            .flex_col()
                            .items_center()
                            .gap_2()
                            .p_3()
                            .bg(rgb(0x313244))
                            .rounded_md()
                            .child(
                                // 实际用 svg().path(icon.svg_path())
                                // 这里用占位 div 演示布局
                                div()
                                    .size(px(32.))
                                    .bg(color)
                                    .rounded_md()
                                    .flex()
                                    .items_center()
                                    .justify_center()
                                    .text_color(rgb(0x1e1e2e))
                                    .child(label.chars().next().unwrap().to_string())
                            )
                            .child(div().text_sm().child(label.to_string()))
                    }))
            )
            // ===== 不同尺寸的图标 =====
            .child(
                div()
                    .flex()
                    .gap_4()
                    .items_center()
                    .child(div().text_sm().text_color(rgb(0x6c7086)).child("尺寸:"))
                    .children([16., 24., 32., 48., 64.].iter().map(|size| {
                        div()
                            .size(px(*size))
                            .bg(rgb(0x89b4fa))
                            .rounded_md()
                    }))
            )
            // ===== 带图标的按钮 =====
            .child(
                div()
                    .flex()
                    .gap_2()
                    .child(
                        div()
                            .flex()
                            .items_center()
                            .gap_2()
                            .px_4()
                            .py_2()
                            .bg(rgb(0x89b4fa))
                            .text_color(rgb(0x1e1e2e))
                            .rounded_md()
                            .cursor_pointer()
                            .child(div().size(px(16.)).bg(rgb(0x1e1e2e)).rounded_full())  // 占位图标
                            .child("搜索")
                    )
                    .child(
                        div()
                            .flex()
                            .items_center()
                            .gap_2()
                            .px_4()
                            .py_2()
                            .bg(rgb(0xa6e3a1))
                            .text_color(rgb(0x1e1e2e))
                            .rounded_md()
                            .cursor_pointer()
                            .child(div().size(px(16.)).bg(rgb(0x1e1e2e)).rounded_md())  // 占位图标
                            .child("添加")
                    )
                    .child(
                        div()
                            .flex()
                            .items_center()
                            .gap_2()
                            .px_4()
                            .py_2()
                            .bg(rgb(0xf38ba8))
                            .text_color(rgb(0x1e1e2e))
                            .rounded_md()
                            .cursor_pointer()
                            .child(div().size(px(16.)).bg(rgb(0x1e1e2e)).rounded_md())  // 占位图标
                            .child("删除")
                    )
            )
            // ===== 状态指示器 =====
            .child(
                div()
                    .flex()
                    .gap_4()
                    .children([
                        ("运行中", rgb(0xa6e3a1)),
                        ("警告", rgb(0xf9e2af)),
                        ("错误", rgb(0xf38ba8)),
                        ("已停止", rgb(0x6c7086)),
                    ].iter().map(|(label, color)| {
                        div()
                            .flex()
                            .items_center()
                            .gap_2()
                            .child(
                                div()
                                    .size(px(8.))
                                    .bg(*color)
                                    .rounded_full()
                            )
                            .child(div().text_sm().child(label.to_string()))
                    }))
            )
    }
}

fn main() {
    Application::new().run(|cx: &mut App| {
        cx.open_window(WindowOptions::default(), |_window, cx| {
            cx.new(|_cx| App::new())
        })
        .unwrap();
        cx.activate(true);
    });
}
```

### 总结

- `svg().path("file.svg").size(px(24.)).text_color(color)` 加载并渲染 SVG 图标
- `text_color()` 覆盖 SVG 的 `fill` 颜色，让单色图标适应主题
- 图标系统用枚举封装，每个变体对应一个 SVG 路径，统一管理
- SVG 矢量缩放不失真，文件小，适合图标
- **常见坑**：SVG 文件需在 `assets/` 目录并配置资源加载；复杂 SVG（多色、渐变）的 `text_color` 可能不生效；SVG 路径错误会 panic；图标尺寸用 `Pixels` 类型

---

## 第 21 讲：自定义 Element

### 概念

当内置的 `div`、`img`、`svg` 无法满足需求时，可创建**自定义 Element**。自定义 Element 实现 `Element` trait，直接控制布局和绘制，能实现任意视觉效果——如自定义图表、画布、特殊布局。这是 GPUI 扩展能力的终极手段，但也是最底层的 API，需要理解布局和绘制原理。

### 原理

`Element` trait 的核心方法（简化）：

```rust
pub trait Element: 'static {
    type RequestLayoutState: 'static;
    type PrepaintState: 'static;

    fn request_layout(&mut self, id: &mut ElementId, window: &mut Window, cx: &mut App) -> (LayoutId, Self::RequestLayoutState);
    fn prepaint(&mut self, id: &mut ElementId, bounds: Bounds<Pixels>, request_layout: &mut Self::RequestLayoutState, window: &mut Window, cx: &mut App) -> Self::PrepaintState;
    fn paint(&mut self, id: &mut ElementId, bounds: Bounds<Pixels>, request_layout: &mut Self::RequestLayoutState, prepaint: &mut Self::PrepaintState, window: &mut Window, cx: &mut App);
}
```

三个阶段：① `request_layout`——向布局引擎注册，返回 `LayoutId`，Taffy 计算最终位置和大小；② `prepaint`——接收计算好的 `bounds`（位置和尺寸），做绘制前准备（如计算文本位置）；③ `paint`——实际绘制，调用 `window.paint_*` 系列 API（`paint_quad`、`paint_text`、`paint_path` 等）发出 GPU 命令。

`Element` 通常配合 `IntoElement` 实现，让自定义元素能像 `div()` 一样在 `render` 中使用。`ElementId` 用于标识元素实例，GPUI 通过它在 diff 时追踪元素。自定义 Element 的状态（`RequestLayoutState`/`PrepaintState`）在阶段间传递，避免重复计算。

### 例子

```rust
use gpui::*;

// ===== 自定义 Element：彩色进度条 =====
struct ProgressBar {
    progress: f32,  // 0.0 - 1.0
    color: Hsla,
    bg_color: Hsla,
}

impl ProgressBar {
    fn new(progress: f32) -> Self {
        Self {
            progress: progress.clamp(0.0, 1.0),
            color: rgb(0x89b4fa),
            bg_color: rgb(0x313244),
        }
    }

    fn with_color(mut self, color: Hsla) -> Self {
        self.color = color;
        self
    }
}

impl Element for ProgressBar {
    type RequestLayoutState = ();
    type PrepaintState = ();

    fn request_layout(
        &mut self,
        _id: &mut Option<ElementId>,
        window: &mut Window,
        _cx: &mut App,
    ) -> (LayoutId, ()) {
        // 创建布局样式
        let mut style = Style::default();
        style.size.width = relative(1.).into();  // 宽度 100%
        style.size.height = px(20.).into();      // 高度 20px

        let layout_id = window.request_layout(style, []);
        (layout_id, ())
    }

    fn prepaint(
        &mut self,
        _id: &mut Option<ElementId>,
        bounds: Bounds<Pixels>,
        _request_layout: &mut (),
        _window: &mut Window,
        _cx: &mut App,
    ) -> () {
        // prepaint 阶段可做准备工作，这里不需要
        let _ = bounds;
    }

    fn paint(
        &mut self,
        _id: &mut Option<ElementId>,
        bounds: Bounds<Pixels>,
        _request_layout: &mut (),
        _prepaint: &mut (),
        window: &mut Window,
        _cx: &mut App,
    ) {
        // 绘制背景
        window.paint_quad(bounds, self.bg_color.into()).log_err();

        // 绘制进度部分
        let progress_width = bounds.size.width * self.progress;
        let progress_bounds = Bounds {
            origin: bounds.origin,
            size: size(progress_width, bounds.size.height),
        };
        window.paint_quad(progress_bounds, self.color.into()).log_err();
    }
}

impl IntoElement for ProgressBar {
    type Element = ProgressBar;
    type Builder = ProgressBar;

    fn into_element(self) -> Self::Element {
        self
    }

    fn into_builder(self) -> Self::Builder {
        self
    }
}

// ===== 自定义 Element：圆形指示器 =====
struct CircleIndicator {
    value: f32,  // 0.0 - 1.0
    size: Pixels,
    color: Hsla,
}

impl CircleIndicator {
    fn new(value: f32, size: Pixels) -> Self {
        Self {
            value: value.clamp(0.0, 1.0),
            size,
            color: rgb(0xa6e3a1),
        }
    }
}

impl Element for CircleIndicator {
    type RequestLayoutState = ();
    type PrepaintState = ();

    fn request_layout(
        &mut self,
        _id: &mut Option<ElementId>,
        window: &mut Window,
        _cx: &mut App,
    ) -> (LayoutId, ()) {
        let mut style = Style::default();
        style.size.width = self.size.into();
        style.size.height = self.size.into();
        let layout_id = window.request_layout(style, []);
        (layout_id, ())
    }

    fn prepaint(
        &mut self,
        _id: &mut Option<ElementId>,
        _bounds: Bounds<Pixels>,
        _request_layout: &mut (),
        _window: &mut Window,
        _cx: &mut App,
    ) -> () {
    }

    fn paint(
        &mut self,
        _id: &mut Option<ElementId>,
        bounds: Bounds<Pixels>,
        _request_layout: &mut (),
        _prepaint: &mut (),
        window: &mut Window,
        _cx: &mut App,
    ) {
        // 绘制背景圆（灰色）
        let bg_path = gpui::Path::ellipse(bounds);
        window.paint_path(bg_path, rgb(0x313244).into()).log_err();

        // 绘制前景圆（按 value 缩放）
        let scaled_size = self.size * self.value;
        let center = bounds.center();
        let fg_bounds = Bounds::centered(center, size(scaled_size, scaled_size));
        let fg_path = gpui::Path::ellipse(fg_bounds);
        window.paint_path(fg_path, self.color.into()).log_err();
    }
}

impl IntoElement for CircleIndicator {
    type Element = CircleIndicator;
    type Builder = CircleIndicator;

    fn into_element(self) -> Self::Element {
        self
    }

    fn into_builder(self) -> Self::Builder {
        self
    }
}

// ===== 主应用 =====
struct App {
    progress: f32,
}

impl App {
    fn new() -> Self {
        Self { progress: 0.3 }
    }
}

impl Render for App {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .size_full()
            .flex()
            .flex_col()
            .gap_4()
            .p_6()
            .bg(rgb(0x1e1e2e))
            .text_color(rgb(0xcdd6f4))
            .child(div().text_lg().child("自定义 Element 演示"))
            // ===== 自定义进度条 =====
            .child(
                div()
                    .flex()
                    .flex_col()
                    .gap_2()
                    .child(div().text_sm().child("进度条:"))
                    .child(ProgressBar::new(self.progress).with_color(rgb(0x89b4fa)))
            )
            // ===== 自定义圆形指示器 =====
            .child(
                div()
                    .flex()
                    .gap_4()
                    .items_center()
                    .child(div().text_sm().child("圆形指示器:"))
                    .child(CircleIndicator::new(self.progress, px(60.)))
                    .child(CircleIndicator::new(0.5, px(60.)))
                    .child(CircleIndicator::new(0.8, px(60.)))
            )
            // ===== 控制按钮 =====
            .child(
                div()
                    .flex()
                    .gap_2()
                    .child(
                        div()
                            .px_4()
                            .py_2()
                            .bg(rgb(0xa6e3a1))
                            .text_color(rgb(0x1e1e2e))
                            .rounded_md()
                            .cursor_pointer()
                            .child("+10%")
                            .on_click(cx.listener(|this, _event, _window, cx| {
                                this.progress = (this.progress + 0.1).min(1.0);
                                cx.notify();
                            }))
                    )
                    .child(
                        div()
                            .px_4()
                            .py_2()
                            .bg(rgb(0xf38ba8))
                            .text_color(rgb(0x1e1e2e))
                            .rounded_md()
                            .cursor_pointer()
                            .child("-10%")
                            .on_click(cx.listener(|this, _event, _window, cx| {
                                this.progress = (this.progress - 0.1).max(0.0);
                                cx.notify();
                            }))
                    )
                    .child(
                        div()
                            .px_4()
                            .py_2()
                            .bg(rgb(0x313244))
                            .text_color(rgb(0xcdd6f4))
                            .rounded_md()
                            .cursor_pointer()
                            .child("重置")
                            .on_click(cx.listener(|this, _event, _window, cx| {
                                this.progress = 0.0;
                                cx.notify();
                            }))
                    )
            )
            .child(
                div()
                    .text_sm()
                    .text_color(rgb(0x6c7086))
                    .child(format!("当前进度: {:.0}%", self.progress * 100.0))
            )
    }
}

fn main() {
    Application::new().run(|cx: &mut App| {
        cx.open_window(WindowOptions::default(), |_window, cx| {
            cx.new(|_cx| App::new())
        })
        .unwrap();
        cx.activate(true);
    });
}
```

自定义 Element 的三阶段：

```
┌─────────────────────────────────────────────────────────────┐
│                  自定义 Element 生命周期                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. request_layout                                          │
│     │                                                       │
│     │  输入: Element 自身                                    │
│     │  输出: LayoutId (注册到 Taffy 布局引擎)                │
│     │  作用: 声明元素想要的尺寸/布局属性                      │
│     │                                                       │
│     ▼                                                       │
│  (Taffy 计算所有元素的最终位置和大小)                        │
│                                                             │
│  2. prepaint                                                │
│     │                                                       │
│     │  输入: bounds (计算好的位置和尺寸)                      │
│     │  输出: PrepaintState (绘制前准备的数据)                │
│     │  作用: 计算文本位置、缓存路径等                         │
│     │                                                       │
│     ▼                                                       │
│  3. paint                                                   │
│     │                                                       │
│     │  输入: bounds + PrepaintState                          │
│     │  作用: 调用 window.paint_quad/paint_text/paint_path    │
│     │        发出 GPU 绘制命令                               │
│     │                                                       │
│     ▼                                                       │
│  (GPU 渲染到屏幕)                                           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 总结

- 自定义 Element 实现 `Element` trait，经历 `request_layout` → `prepaint` → `paint` 三阶段
- `request_layout` 声明尺寸，`prepaint` 做准备，`paint` 发出 GPU 命令
- `window.paint_quad(bounds, color)` 绘制矩形，`window.paint_path(path, color)` 绘制路径
- 实现 `IntoElement` 让自定义元素能在 `render` 中像 `div()` 一样使用
- **常见坑**：`Element` trait 的关联类型 `RequestLayoutState`/`PrepaintState` 用于阶段间传递数据；`paint_*` 方法返回 `Result`，需 `.log_err()` 处理；自定义 Element 不能有子元素（除非实现 `ParentElement`）；布局样式用 `Style` 结构体配置

---

# 第 6 章：实战与工程化

本章是 GPUI 学习的收官之作。我们将通过一个完整的 TodoMVC 应用综合运用前 21 讲所学知识，然后学习性能优化技巧、测试方法、发布部署流程。完成本章后，你将具备独立开发、优化、测试、发布 GPUI 应用的完整能力。

## 第 22 讲：构建 TodoMVC 应用

### 概念

**TodoMVC** 是前端领域的经典示例项目——实现一个待办事项应用，涵盖增删改查、过滤、本地存储等功能。本讲用 GPUI 实现一个完整的 TodoMVC，综合运用 View 嵌套、Model 状态管理、事件处理、主题、过滤逻辑等知识。这是对前 21 讲内容的全面检验。

### 原理

TodoMVC 的架构设计：① **Todo Model**——存储所有待办事项和过滤状态，作为单一数据源；② **TodoItem View**——单个待办项的 UI，支持编辑、删除、切换完成状态；③ **TodoList View**——列表容器，根据过滤状态显示对应项；④ **TodoInput View**——输入框，添加新待办；⑤ **TodoFilter View**——过滤按钮（全部/未完成/已完成）；⑥ **App View**——根视图，组合所有子视图。

数据流：用户操作（输入、点击）→ 事件处理器 → 修改 Todo Model → `cx.notify()` → 所有订阅 Todo 的 View 重绘。这种"单一数据源 + 观察者"模式让状态变化可追踪、UI 更新自动化。待办项的增删改查都通过 Todo Model 的方法进行，View 只负责渲染和触发事件，不直接修改数据。

### 例子

```rust
use gpui::*;
use std::fmt;

// ===== 数据模型 =====
#[derive(Clone, Debug)]
struct Todo {
    id: usize,
    text: String,
    completed: bool,
}

#[derive(Clone, Copy, PartialEq, Debug)]
enum Filter {
    All,
    Active,
    Completed,
}

impl fmt::Display for Filter {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        match self {
            Filter::All => write!(f, "全部"),
            Filter::Active => write!(f, "未完成"),
            Filter::Completed => write!(f, "已完成"),
        }
    }
}

// ===== Todo Model（单一数据源）=====
struct TodoStore {
    todos: Vec<Todo>,
    next_id: usize,
    filter: Filter,
}

impl TodoStore {
    fn new() -> Self {
        Self {
            todos: vec![
                Todo { id: 1, text: "学习 GPUI".into(), completed: false },
                Todo { id: 2, text: "构建 TodoMVC".into(), completed: false },
                Todo { id: 3, text: "发布应用".into(), completed: true },
            ],
            next_id: 4,
            filter: Filter::All,
        }
    }

    fn add(&mut self, text: String, cx: &mut Context<Self>) {
        if text.trim().is_empty() { return; }
        self.todos.push(Todo {
            id: self.next_id,
            text: text.trim().to_string(),
            completed: false,
        });
        self.next_id += 1;
        cx.notify();
    }

    fn toggle(&mut self, id: usize, cx: &mut Context<Self>) {
        if let Some(todo) = self.todos.iter_mut().find(|t| t.id == id) {
            todo.completed = !todo.completed;
            cx.notify();
        }
    }

    fn remove(&mut self, id: usize, cx: &mut Context<Self>) {
        self.todos.retain(|t| t.id != id);
        cx.notify();
    }

    fn edit(&mut self, id: usize, text: String, cx: &mut Context<Self>) {
        if let Some(todo) = self.todos.iter_mut().find(|t| t.id == id) {
            todo.text = text;
            cx.notify();
        }
    }

    fn set_filter(&mut self, filter: Filter, cx: &mut Context<Self>) {
        self.filter = filter;
        cx.notify();
    }

    fn clear_completed(&mut self, cx: &mut Context<Self>) {
        self.todos.retain(|t| !t.completed);
        cx.notify();
    }

    fn filtered(&self) -> Vec<&Todo> {
        self.todos.iter().filter(|t| match self.filter {
            Filter::All => true,
            Filter::Active => !t.completed,
            Filter::Completed => t.completed,
        }).collect()
    }

    fn active_count(&self) -> usize {
        self.todos.iter().filter(|t| !t.completed).count()
    }

    fn completed_count(&self) -> usize {
        self.todos.iter().filter(|t| t.completed).count()
    }
}

// ===== 单个待办项 View =====
struct TodoItem {
    todo: Todo,
    store: Model<TodoStore>,
    is_editing: bool,
    edit_text: String,
}

impl TodoItem {
    fn new(todo: Todo, store: Model<TodoStore>) -> Self {
        Self {
            todo,
            store,
            is_editing: false,
            edit_text: String::new(),
        }
    }
}

impl Render for TodoItem {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        let todo = &self.todo;
        let store = self.store.clone();

        div()
            .flex()
            .items_center()
            .gap_2()
            .px_3()
            .py_2()
            .bg(rgb(0x313244))
            .rounded_md()
            .child(
                // 复选框
                div()
                    .size(px(20.))
                    .bg(if todo.completed { rgb(0xa6e3a1) } else { rgb(0x45475a) })
                    .rounded_full()
                    .cursor_pointer()
                    .flex()
                    .items_center()
                    .justify_center()
                    .text_color(rgb(0x1e1e2e))
                    .text_xs()
                    .child(if todo.completed { "✓" } else { "" })
                    .on_click(cx.listener({
                        let id = todo.id;
                        let store = store.clone();
                        move |_this, _event, _window, cx| {
                            store.update(cx, |s, cx| s.toggle(id, cx));
                        }
                    }))
            )
            .child(
                if self.is_editing {
                    // 编辑模式
                    div()
                        .flex_1()
                        .px_2()
                        .py_1()
                        .bg(rgb(0x1e1e2e))
                        .border_1()
                        .border_color(rgb(0x89b4fa))
                        .rounded_md()
                        .text_color(rgb(0xcdd6f4))
                        .child(self.edit_text.clone())
                        .on_click(cx.listener(|_this, _event, _window, cx| {
                            cx.notify();
                        }))
                } else {
                    // 显示模式
                    div()
                        .flex_1()
                        .text_color(if todo.completed { rgb(0x6c7086) } else { rgb(0xcdd6f4) })
                        .child(todo.text.clone())
                        .on_double_click(cx.listener(|this, _event, _window, cx| {
                            this.is_editing = true;
                            this.edit_text = this.todo.text.clone();
                            cx.notify();
                        }))
                }
            )
            .child(
                // 删除按钮
                div()
                    .px_2()
                    .py_1()
                    .bg(rgb(0xf38ba8))
                    .text_color(rgb(0x1e1e2e))
                    .rounded_md()
                    .cursor_pointer()
                    .text_xs()
                    .child("删除")
                    .on_click(cx.listener({
                        let id = todo.id;
                        let store = store.clone();
                        move |_this, _event, _window, cx| {
                            store.update(cx, |s, cx| s.remove(id, cx));
                        }
                    }))
            )
    }
}

// ===== 输入框 View =====
struct TodoInput {
    store: Model<TodoStore>,
    text: String,
}

impl TodoInput {
    fn new(store: Model<TodoStore>) -> Self {
        Self { store, text: String::new() }
    }
}

impl Render for TodoInput {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        let store = self.store.clone();
        div()
            .flex()
            .gap_2()
            .child(
                div()
                    .flex_1()
                    .h(px(40.))
                    .px_3()
                    .flex()
                    .items_center()
                    .bg(rgb(0x313244))
                    .border_1()
                    .border_color(rgb(0x45475a))
                    .rounded_md()
                    .text_color(rgb(0xcdd6f4))
                    .child(if self.text.is_empty() {
                        "添加新待办...".to_string()
                    } else {
                        self.text.clone()
                    })
                    .on_click(cx.listener(|this, _event, _window, cx| {
                        // 实际应用中这里应聚焦输入框
                        let _ = this;
                        cx.notify();
                    }))
            )
            .child(
                div()
                    .px_4()
                    .py_2()
                    .bg(rgb(0x89b4fa))
                    .text_color(rgb(0x1e1e2e))
                    .rounded_md()
                    .cursor_pointer()
                    .child("添加")
                    .on_click(cx.listener({
                        let store = store.clone();
                        move |this, _event, _window, cx| {
                            if !this.text.is_empty() {
                                let text = this.text.clone();
                                this.text.clear();
                                store.update(cx, |s, cx| s.add(text, cx));
                            }
                        }
                    }))
            )
    }
}

// ===== 过滤器 View =====
struct TodoFilter {
    store: Model<TodoStore>,
}

impl TodoFilter {
    fn new(store: Model<TodoStore>) -> Self {
        Self { store }
    }
}

impl Render for TodoFilter {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        let current_filter = self.store.read(cx).filter;
        let store = self.store.clone();

        div()
            .flex()
            .gap_2()
            .children([Filter::All, Filter::Active, Filter::Completed].iter().map(|&f| {
                let is_active = current_filter == f;
                let store = store.clone();
                div()
                    .px_3()
                    .py_1()
                    .bg(if is_active { rgb(0x89b4fa) } else { rgb(0x313244) })
                    .text_color(if is_active { rgb(0x1e1e2e) } else { rgb(0xcdd6f4) })
                    .rounded_md()
                    .cursor_pointer()
                    .text_sm()
                    .child(f.to_string())
                    .on_click(cx.listener(move |_this, _event, _window, cx| {
                        store.update(cx, |s, cx| s.set_filter(f, cx));
                    }))
            }))
    }
}

// ===== 根应用 View =====
struct App {
    store: Model<TodoStore>,
    input: View<TodoInput>,
    filter: View<TodoFilter>,
}

impl App {
    fn new(cx: &mut Context<Self>) -> Self {
        let store = cx.new_model(|_| TodoStore::new());
        let store_for_input = store.clone();
        let store_for_filter = store.clone();

        Self {
            store,
            input: cx.new(|_| TodoInput::new(store_for_input)),
            filter: cx.new(|_| TodoFilter::new(store_for_filter)),
        }
    }
}

impl Render for App {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        let store = self.store.read(cx);
        let todos = store.filtered();
        let active_count = store.active_count();
        let completed_count = store.completed_count();
        let total_count = store.todos.len();

        let store_for_items = self.store.clone();

        div()
            .size_full()
            .flex()
            .flex_col()
            .gap_4()
            .p_6()
            .bg(rgb(0x1e1e2e))
            .text_color(rgb(0xcdd6f4))
            .observe(&self.store, |this, _store, cx| {
                let _ = this;
                cx.notify();
            })
            .child(
                div()
                    .flex()
                    .justify_between()
                    .items_center()
                    .child(div().text_2xl().child("TodoMVC"))
                    .child(
                        div()
                            .text_sm()
                            .text_color(rgb(0x6c7086))
                            .child(format!("共 {} 项", total_count))
                    )
            )
            // 输入框
            .child(self.input.clone())
            // 过滤器
            .child(
                div()
                    .flex()
                    .justify_between()
                    .items_center()
                    .child(self.filter.clone())
                    .child(
                        div()
                            .text_sm()
                            .text_color(rgb(0x6c7086))
                            .child(format!("{} 未完成 / {} 已完成", active_count, completed_count))
                    )
            )
            // 待办列表
            .child(
                div()
                    .flex_1()
                    .flex()
                    .flex_col()
                    .gap_2()
                    .overflow_y_scroll()
                    .children(todos.iter().map(|todo| {
                        let store = store_for_items.clone();
                        div().child(
                            // 每个 TodoItem 是独立 View
                            // 实际中应预创建 View 并复用
                            div()
                                .flex()
                                .items_center()
                                .gap_2()
                                .px_3()
                                .py_2()
                                .bg(rgb(0x313244))
                                .rounded_md()
                                .child(
                                    div()
                                        .size(px(20.))
                                        .bg(if todo.completed { rgb(0xa6e3a1) } else { rgb(0x45475a) })
                                        .rounded_full()
                                        .cursor_pointer()
                                        .flex()
                                        .items_center()
                                        .justify_center()
                                        .text_color(rgb(0x1e1e2e))
                                        .text_xs()
                                        .child(if todo.completed { "✓" } else { "" })
                                        .on_click(cx.listener({
                                            let id = todo.id;
                                            let store = store.clone();
                                            move |_this, _event, _window, cx| {
                                                store.update(cx, |s, cx| s.toggle(id, cx));
                                            }
                                        }))
                                )
                                .child(
                                    div()
                                        .flex_1()
                                        .text_color(if todo.completed { rgb(0x6c7086) } else { rgb(0xcdd6f4) })
                                        .child(todo.text.clone())
                                )
                                .child(
                                    div()
                                        .px_2()
                                        .py_1()
                                        .bg(rgb(0xf38ba8))
                                        .text_color(rgb(0x1e1e2e))
                                        .rounded_md()
                                        .cursor_pointer()
                                        .text_xs()
                                        .child("删除")
                                        .on_click(cx.listener({
                                            let id = todo.id;
                                            let store = store.clone();
                                            move |_this, _event, _window, cx| {
                                                store.update(cx, |s, cx| s.remove(id, cx));
                                            }
                                        }))
                                )
                        )
                    }))
            )
            // 底部操作
            .child(
                div()
                    .flex()
                    .justify_between()
                    .child(
                        div()
                            .text_sm()
                            .text_color(rgb(0x6c7086))
                            .child("双击编辑待办")
                    )
                    .child(
                        if completed_count > 0 {
                            div()
                                .px_3()
                                .py_1()
                                .bg(rgb(0x313244))
                                .text_color(rgb(0xcdd6f4))
                                .rounded_md()
                                .cursor_pointer()
                                .text_sm()
                                .child("清除已完成")
                                .on_click(cx.listener(|this, _event, _window, cx| {
                                    this.store.update(cx, |s, cx| s.clear_completed(cx));
                                }))
                                .into_any_element()
                        } else {
                            div().into_any_element()
                        }
                    )
            )
    }
}

fn main() {
    Application::new().run(|cx: &mut App| {
        cx.open_window(WindowOptions::default(), |_window, cx| {
            cx.new(|cx| App::new(cx))
        })
        .unwrap();
        cx.activate(true);
    });
}
```

TodoMVC 架构图：

```
┌─────────────────────────────────────────────────────────┐
│                    App (根 View)                         │
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │  Model<TodoStore> (单一数据源)                   │   │
│  │  todos: Vec<Todo>                                │   │
│  │  filter: Filter                                  │   │
│  │  next_id: usize                                  │   │
│  └─────────────────────────────────────────────────┘   │
│          ▲           ▲           ▲           ▲          │
│          │ observe   │ observe   │ observe   │ observe  │
│          │           │           │           │          │
│  ┌───────┴───┐ ┌─────┴─────┐ ┌───┴────┐ ┌────┴─────┐  │
│  │TodoInput  │ │TodoFilter │ │TodoList│ │TodoItem  │  │
│  │(View)     │ │(View)     │ │(View)  │ │(View)    │  │
│  │           │ │           │ │        │ │          │  │
│  │ 添加待办  │ │ 切换过滤  │ │ 渲染   │ │ 编辑/删除│  │
│  │ store.add │ │store.     │ │ 列表   │ │ store.   │  │
│  │           │ │set_filter │ │        │ │toggle/   │  │
│  │           │ │           │ │        │ │remove    │  │
│  └───────────┘ └───────────┘ └────────┘ └──────────┘  │
│                                                         │
│  所有 View 通过 store.update 修改状态，cx.notify 通知   │
└─────────────────────────────────────────────────────────┘
```

### 总结

- TodoMVC 采用"单一数据源（TodoStore）+ 多 View 订阅"架构
- TodoStore 封装所有状态操作（add/toggle/remove/edit/filter），View 只触发事件
- `cx.observe(&store, ...)` 让 View 在状态变化时自动重绘
- 每个 TodoItem 是独立 View，支持显示/编辑模式切换
- **常见坑**：列表渲染时每个 item 应是独立 View（用 `cx.new` 预创建），避免每帧重建；`store.update` 闭包内必须 `cx.notify()`；过滤逻辑放在 Model 中，View 只渲染过滤后的结果

---

## 第 23 讲：性能优化

### 概念

GPUI 本身性能优异（GPU 加速 + Rust 零成本抽象），但不当的代码仍可能导致卡顿。常见性能问题：① 不必要的重绘——`cx.notify()` 过于频繁；② 大列表渲染——数千项一次性渲染；③ 闭包捕获过重——每次 `render` 克隆大对象；④ 布局抖动——频繁改变布局属性。本讲介绍 GPUI 性能优化的核心技巧。

### 原理

GPUI 的渲染流程：`cx.notify()` → 标记 View 脏 → 下一帧调用 `render` → 生成 Element 树 → diff → 重绘变化区域。优化目标是减少 `render` 调用次数和每次 `render` 的工作量。

**减少重绘**：① 精确订阅——只 `observe` 真正关心的 Model，避免全局重绘；② 拆分 View——将大 View 拆成小 View，每个只在自己关心的状态变化时重绘；③ 避免在 `render` 中调用 `cx.notify()`（会导致无限循环）。

**大列表优化**：GPUI 没有内置虚拟列表，但可通过"窗口化渲染"实现——只渲染可见区域的 item，滚动时动态更新。这需要追踪滚动位置，计算可见范围，只渲染该范围的元素。对于千级以上列表，这是必须的优化。

**闭包优化**：`cx.listener` 的闭包会捕获环境变量，若捕获大对象（如 `Vec`、`String`），每次 `render` 都会克隆。优化：① 捕获 `Model` 句柄（轻量）而非数据本身；② 用 `Arc` 共享大对象；③ 将不变的数据移到 View 结构体中，闭包只捕获索引或 ID。

**布局优化**：避免在动画中改变 `flex` 属性（会触发重新布局），改用 `transform`（只影响绘制）。`overflow_hidden` 配合固定尺寸可避免布局抖动。

### 例子

```rust
use gpui::*;
use std::collections::HashMap;

// ===== 反例：低效的列表渲染 =====
struct BadList {
    items: Vec<String>,  // 直接持有大数据
}

impl Render for BadList {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .flex()
            .flex_col()
            .children(self.items.iter().enumerate().map(|(i, item)| {
                // 问题：每次 render 都克隆所有 item
                let item = item.clone();
                div()
                    .px_3()
                    .py_2()
                    .bg(rgb(0x313244))
                    .child(item)
                    .on_click(cx.listener(move |_this, _event, _window, cx| {
                        // 问题：闭包捕获了 item 的克隆
                        println!("点击: {}", item);
                        cx.notify();
                    }))
            }))
    }
}

// ===== 正例：高效的列表渲染 =====
struct GoodList {
    store: Model<ListStore>,  // 数据放在 Model 中
    item_views: HashMap<usize, View<ListItem>>,  // 预创建 View 复用
}

struct ListStore {
    items: Vec<(usize, String)>,  // (id, text)
}

impl GoodList {
    fn new(cx: &mut Context<Self>) -> Self {
        let store = cx.new_model(|_| ListStore {
            items: (0..1000).map(|i| (i, format!("项目 {}", i))).collect(),
        });

        Self {
            store,
            item_views: HashMap::new(),
        }
    }
}

impl Render for GoodList {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        let store = self.store.read(cx);

        div()
            .flex()
            .flex_col()
            .observe(&self.store, |this, _store, cx| {
                let _ = this;
                cx.notify();
            })
            .children(store.items.iter().map(|(id, _text)| {
                // 复用已创建的 View，避免每帧重建
                if !self.item_views.contains_key(id) {
                    // 实际中应在数据变化时创建/销毁 View
                }
                div()
                    .px_3()
                    .py_2()
                    .bg(rgb(0x313244))
                    .child(format!("项目 {}", id))
            }))
    }
}

struct ListItem {
    id: usize,
    store: Model<ListStore>,
}

// ===== 虚拟列表示例（概念）=====
struct VirtualList {
    store: Model<ListStore>,
    scroll_offset: f32,
    visible_range: (usize, usize),
    item_height: f32,
    viewport_height: f32,
}

impl VirtualList {
    fn new(store: Model<ListStore>) -> Self {
        Self {
            store,
            scroll_offset: 0.0,
            visible_range: (0, 20),
            item_height: 40.0,
            viewport_height: 800.0,
        }
    }

    fn update_visible_range(&mut self, scroll: f32) {
        let start = (scroll / self.item_height) as usize;
        let visible_count = (self.viewport_height / self.item_height) as usize + 2;  // 多渲染 2 个缓冲
        self.visible_range = (start, start + visible_count);
    }
}

impl Render for VirtualList {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        let store = self.store.read(cx);
        let (start, end) = self.visible_range;
        let total_height = store.items.len() as f32 * self.item_height;

        div()
            .h(px(self.viewport_height))
            .overflow_y_scroll()
            .on_scroll_wheel(cx.listener(|this, event, _window, cx| {
                this.scroll_offset += event.delta.pixel_delta(Pixels(16.0)).y.0;
                this.update_visible_range(this.scroll_offset);
                cx.notify();
            }))
            .child(
                div()
                    .h(px(total_height))  // 占位总高度
                    .children(store.items[start..end.min(store.items.len())].iter().map(|(id, text)| {
                        div()
                            .h(px(self.item_height))
                            .px_3()
                            .flex()
                            .items_center()
                            .bg(rgb(0x313244))
                            .child(format!("[{}] {}", id, text))
                    }))
            )
    }
}

// ===== 性能对比演示 =====
struct App {
    show_bad: bool,
    item_count: usize,
}

impl App {
    fn new() -> Self {
        Self { show_bad: false, item_count: 100 }
    }
}

impl Render for App {
    fn render(&mut self, _window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .size_full()
            .flex()
            .flex_col()
            .gap_4()
            .p_6()
            .bg(rgb(0x1e1e2e))
            .text_color(rgb(0xcdd6f4))
            .child(div().text_lg().child("性能优化演示"))
            .child(
                div()
                    .flex()
                    .gap_2()
                    .child(
                        div()
                            .px_4()
                            .py_2()
                            .bg(if self.show_bad { rgb(0xf38ba8) } else { rgb(0x313244) })
                            .text_color(rgb(0xcdd6f4))
                            .rounded_md()
                            .cursor_pointer()
                            .child("低效模式")
                            .on_click(cx.listener(|this, _event, _window, cx| {
                                this.show_bad = true;
                                cx.notify();
                            }))
                    )
                    .child(
                        div()
                            .px_4()
                            .py_2()
                            .bg(if !self.show_bad { rgb(0xa6e3a1) } else { rgb(0x313244) })
                            .text_color(rgb(0xcdd6f4))
                            .rounded_md()
                            .cursor_pointer()
                            .child("高效模式")
                            .on_click(cx.listener(|this, _event, _window, cx| {
                                this.show_bad = false;
                                cx.notify();
                            }))
                    )
            )
            .child(
                div()
                    .flex()
                    .gap_2()
                    .items_center()
                    .child(div().text_sm().child("项目数量:"))
                    .children([100, 500, 1000, 5000].iter().map(|&n| {
                        div()
                            .px_3()
                            .py_1()
                            .bg(if self.item_count == n { rgb(0x89b4fa) } else { rgb(0x313244) })
                            .text_color(if self.item_count == n { rgb(0x1e1e2e) } else { rgb(0xcdd6f4) })
                            .rounded_md()
                            .cursor_pointer()
                            .text_sm()
                            .child(n.to_string())
                            .on_click(cx.listener(move |this, _event, _window, cx| {
                                this.item_count = n;
                                cx.notify();
                            }))
                    }))
            )
            .child(
                div()
                    .p_4()
                    .bg(rgb(0x313244))
                    .rounded_md()
                    .flex()
                    .flex_col()
                    .gap_2()
                    .child(div().text_sm().text_color(rgb(0xf9e2af)).child("性能提示:"))
                    .child(div().text_xs().child("• 高效模式: 数据放 Model，View 复用，闭包只捕获轻量句柄"))
                    .child(div().text_xs().child("• 低效模式: 每帧克隆数据，闭包捕获大对象"))
                    .child(div().text_xs().child("• 大列表(1000+): 使用虚拟列表，只渲染可见区域"))
            )
    }
}

fn main() {
    Application::new().run(|cx: &mut App| {
        cx.open_window(WindowOptions::default(), |_window, cx| {
            cx.new(|_cx| App::new())
        })
        .unwrap();
        cx.activate(true);
    });
}
```

性能优化检查清单：

```
┌─────────────────────────────────────────────────────────────┐
│                  GPUI 性能优化检查清单                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  □ 减少重绘                                                 │
│    □ 精确 observe，避免全局重绘                              │
│    □ 拆分大 View 为小 View                                  │
│    □ 不在 render 中调用 cx.notify()                         │
│    □ 条件渲染用 if 而非创建空元素                            │
│                                                             │
│  □ 数据管理                                                 │
│    □ 数据放 Model，View 只持有句柄                           │
│    □ 大对象用 Arc 共享，避免克隆                             │
│    □ 列表数据用 ID 索引，闭包捕获 ID 而非数据                │
│                                                             │
│  □ 列表优化                                                 │
│    □ 1000+ 项用虚拟列表                                     │
│    □ 预创建 View 复用，避免每帧重建                          │
│    □ 滚动时只更新可见范围                                   │
│                                                             │
│  □ 布局优化                                                 │
│    □ 动画用 transform 而非 flex 属性                        │
│    □ 固定尺寸避免布局抖动                                   │
│    □ overflow_hidden 裁剪不可见内容                         │
│                                                             │
│  □ 闭包优化                                                 │
│    □ 捕获 Model 句柄而非数据                                │
│    □ 捕获索引/ID 而非完整对象                               │
│    □ 闭包内避免重计算                                       │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 总结

- 减少 `cx.notify()` 调用，精确订阅只关心的 Model
- 大列表用虚拟列表（只渲染可见区域），预创建 View 复用
- 数据放 Model，闭包捕获句柄而非数据，避免每帧克隆
- 动画用 `transform`（不影响布局），避免改变 `flex` 属性
- **常见坑**：`render` 中调用 `cx.notify()` 导致无限循环；闭包捕获 `Vec`/`String` 每帧克隆；大列表不虚拟化导致卡顿；`observe` 过于宽泛导致全量重绘

---

## 第 24 讲：测试 GPUI 应用

### 概念

测试 GUI 应用是公认的难题——UI 状态复杂、交互异步、视觉难验证。GPUI 提供测试支持：通过 `TestAppContext` 模拟应用环境，无需真实窗口即可测试 View 逻辑。测试分两类：① **单元测试**——测试 Model 的业务逻辑（纯函数，易测试）；② **集成测试**——测试 View 的渲染和交互（需模拟事件）。本讲介绍 GPUI 测试的方法论。

### 原理

GPUI 的测试核心是 `TestAppContext`——一个模拟的 `App`，提供与真实 `App` 相同的 API，但不创建真实窗口。通过 `cx.update(|cx| { ... })` 在测试环境中操作 View，`cx.read` 读取状态，`cx.simulate_event` 模拟用户输入。

Model 的单元测试最简单——因为 Model 是普通 Rust 结构体，其方法（add/toggle/remove 等）是纯逻辑，可直接 `cargo test` 测试，无需 GPUI 环境。这是推荐的做法——将业务逻辑放在 Model 中，与 UI 解耦，便于测试。

View 的集成测试较复杂——需创建 `TestAppContext`，初始化 View，模拟事件，断言渲染结果。GPUI 的 `render` 返回 Element 树，可通过 `element.text()` 等方法检查内容。但 Element 树的断言较脆弱（依赖具体结构），建议优先测试 Model 逻辑，View 测试只验证关键交互。

### 例子

```rust
use gpui::*;

// ===== 被测代码：TodoStore =====
#[derive(Clone, Debug, PartialEq)]
struct Todo {
    id: usize,
    text: String,
    completed: bool,
}

struct TodoStore {
    todos: Vec<Todo>,
    next_id: usize,
}

impl TodoStore {
    fn new() -> Self {
        Self { todos: vec![], next_id: 1 }
    }

    fn add(&mut self, text: String, cx: &mut Context<Self>) {
        if text.trim().is_empty() { return; }
        self.todos.push(Todo {
            id: self.next_id,
            text: text.trim().to_string(),
            completed: false,
        });
        self.next_id += 1;
        cx.notify();
    }

    fn toggle(&mut self, id: usize, cx: &mut Context<Self>) {
        if let Some(todo) = self.todos.iter_mut().find(|t| t.id == id) {
            todo.completed = !todo.completed;
            cx.notify();
        }
    }

    fn remove(&mut self, id: usize, cx: &mut Context<Self>) {
        self.todos.retain(|t| t.id != id);
        cx.notify();
    }

    fn active_count(&self) -> usize {
        self.todos.iter().filter(|t| !t.completed).count()
    }
}

// ===== 单元测试：Model 逻辑 =====
#[cfg(test)]
mod tests {
    use super::*;

    // 测试需要 Context，用 TestAppContext
    fn setup() -> (Model<TodoStore>, TestAppContext) {
        let cx = TestAppContext::default();
        let store = cx.new_model(|_| TodoStore::new());
        (store, cx)
    }

    #[test]
    fn test_add_todo() {
        let (store, mut cx) = setup();
        store.update(&mut cx, |s, cx| s.add("学习 GPUI".into(), cx));

        let todos = store.read(&cx).todos.clone();
        assert_eq!(todos.len(), 1);
        assert_eq!(todos[0].text, "学习 GPUI");
        assert!(!todos[0].completed);
        assert_eq!(todos[0].id, 1);
    }

    #[test]
    fn test_add_empty_todo_ignored() {
        let (store, mut cx) = setup();
        store.update(&mut cx, |s, cx| s.add("   ".into(), cx));

        assert_eq!(store.read(&cx).todos.len(), 0);
    }

    #[test]
    fn test_toggle_todo() {
        let (store, mut cx) = setup();
        store.update(&mut cx, |s, cx| {
            s.add("待办 1".into(), cx);
            s.add("待办 2".into(), cx);
        });

        store.update(&mut cx, |s, cx| s.toggle(1, cx));

        let todos = store.read(&cx).todos.clone();
        assert!(todos[0].completed);
        assert!(!todos[1].completed);
    }

    #[test]
    fn test_remove_todo() {
        let (store, mut cx) = setup();
        store.update(&mut cx, |s, cx| {
            s.add("待办 1".into(), cx);
            s.add("待办 2".into(), cx);
        });

        store.update(&mut cx, |s, cx| s.remove(1, cx));

        let todos = store.read(&cx).todos.clone();
        assert_eq!(todos.len(), 1);
        assert_eq!(todos[0].id, 2);
    }

    #[test]
    fn test_active_count() {
        let (store, mut cx) = setup();
        store.update(&mut cx, |s, cx| {
            s.add("待办 1".into(), cx);
            s.add("待办 2".into(), cx);
            s.add("待办 3".into(), cx);
            s.toggle(1, cx);  // 完成 1
        });

        assert_eq!(store.read(&cx).active_count(), 2);
    }

    #[test]
    fn test_toggle_nonexistent_id() {
        let (store, mut cx) = setup();
        store.update(&mut cx, |s, cx| {
            s.add("待办 1".into(), cx);
            s.toggle(999, cx);  // 不存在的 ID
        });

        // 不应 panic，列表不变
        assert_eq!(store.read(&cx).todos.len(), 1);
        assert!(!store.read(&cx).todos[0].completed);
    }

    #[test]
    fn test_add_multiple_increments_id() {
        let (store, mut cx) = setup();
        for i in 0..5 {
            store.update(&mut cx, |s, cx| s.add(format!("待办 {}", i), cx));
        }

        let todos = store.read(&cx).todos.clone();
        assert_eq!(todos.len(), 5);
        assert_eq!(todos[0].id, 1);
        assert_eq!(todos[4].id, 5);
    }
}

// ===== 集成测试：View 渲染 =====
#[cfg(test)]
mod integration_tests {
    use super::*;

    struct CounterView {
        count: i32,
    }

    impl Render for CounterView {
        fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
            div()
                .child(format!("计数: {}", self.count))
                .child(
                    div()
                        .child("增加")
                        .on_click(cx.listener(|this, _event, _window, cx| {
                            this.count += 1;
                            cx.notify();
                        }))
                )
        }
    }

    #[test]
    fn test_counter_initial_render() {
        let cx = TestAppContext::default();
        let view = cx.new(|_| CounterView { count: 0 });

        // 读取 View 状态
        let count = view.read(&cx).count;
        assert_eq!(count, 0);
    }

    #[test]
    fn test_counter_increment() {
        let cx = TestAppContext::default();
        let view = cx.new(|_| CounterView { count: 0 });

        // 模拟点击
        view.update(&mut cx, |v, cx| {
            v.count += 1;
            cx.notify();
        });

        assert_eq!(view.read(&cx).count, 1);
    }
}

fn main() {
    // 主函数留空，测试通过 cargo test 运行
    Application::new().run(|cx: &mut App| {
        cx.open_window(WindowOptions::default(), |_window, cx| {
            cx.new(|_cx| {
                struct Placeholder;
                impl Render for Placeholder {
                    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
                        div().child("运行 cargo test 查看测试结果")
                    }
                }
                Placeholder
            })
        })
        .unwrap();
        cx.activate(true);
    });
}
```

测试策略：

```
┌─────────────────────────────────────────────────────────────┐
│                   GPUI 测试策略金字塔                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│                    ╱╲                                       │
│                   ╱  ╲     端到端测试 (少)                   │
│                  ╱ E2E ╲   完整用户流程                      │
│                 ╱──────╲                                    │
│                ╱        ╲                                   │
│               ╱ 集成测试  ╲  中等数量                        │
│              ╱ View 渲染   ╲ 测试 View 交互                  │
│             ╱──────────────╲                                │
│            ╱                ╲                               │
│           ╱   单元测试 (多)   ╲ 大量                         │
│          ╱   Model 业务逻辑    ╲ 纯函数测试                  │
│         ╱──────────────────────╲                            │
│                                                             │
│  原则:                                                       │
│  1. 业务逻辑放 Model，单元测试覆盖                           │
│  2. View 测试只验证关键交互，不断言具体 DOM                  │
│  3. E2E 测试覆盖核心用户流程                                │
│  4. 用 TestAppContext 模拟环境，无需真实窗口                 │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 总结

- Model 单元测试最简单——业务逻辑与 UI 解耦，用 `TestAppContext` 创建 `new_model`
- View 集成测试用 `TestAppContext`，`view.update` 模拟事件，`view.read` 断言状态
- 测试金字塔：大量 Model 单元测试 + 中等 View 集成测试 + 少量 E2E
- `TestAppContext` 提供与真实 `App` 相同的 API，无需真实窗口
- **常见坑**：`TestAppContext` 的方法签名可能与真实 `App` 略有不同；View 的 Element 树断言脆弱，优先测 Model；异步任务测试需 `cx.run_until_parked()` 等待；`cargo test` 默认运行所有测试

---

## 第 25 讲：发布与部署

### 概念

完成开发和测试后，最后一步是**发布与部署**——将 GPUI 应用打包为可分发的安装包，让用户安装使用。这涉及：① 编译优化（release 模式）；② 资源打包（图标、字体、SVG）；③ 平台特定打包（macOS .app、Windows .exe、Linux AppImage）；④ 代码签名（macOS 公证、Windows 签名）；⑤ 自动更新。本讲介绍 GPUI 应用的发布流程。

### 原理

**编译优化**：`cargo build --release` 启用 O3 优化，生成体积小、速度快的二进制。可在 `Cargo.toml` 的 `[profile.release]` 中进一步配置：`lto = true`（链接时优化）、`codegen-units = 1`（单线程编译，优化更好）、`strip = true`（移除调试符号）。这些设置让 release 二进制比 debug 小 5-10 倍，启动更快。

**资源打包**：GPUI 应用通常需要图标、字体、SVG 等资源。两种方式：① **运行时加载**——资源放在 `assets/` 目录，运行时按路径加载，需确保路径正确（用 `env!("CARGO_MANIFEST_DIR")` 或配置资源路径）；② **编译时嵌入**——用 `include_str!`/`include_bytes!` 将资源嵌入二进制，单文件分发更简单。Zed 采用嵌入方式，所有资源编译进二进制。

**平台打包**：macOS 需打包为 `.app` bundle（含 `Info.plist`、图标、可执行文件）；Windows 打包为 `.exe` + 资源文件；Linux 通常用 AppImage 或 Flatpak。`cargo-bundle` 是常用的 Rust 打包工具，支持自动生成各平台安装包。

**代码签名**：macOS 应用需"公证"（notarize）才能分发，否则用户会看到"无法打开"警告；Windows 应用建议代码签名，避免 SmartScreen 警告。这需要 Apple Developer 账号或 Windows 代码签名证书。

### 例子

`Cargo.toml` 发布配置：

```toml
[package]
name = "my-gpui-app"
version = "1.0.0"
edition = "2021"

[dependencies]
gpui = { git = "https://github.com/zed-industries/zed" }
# 其他依赖...

[profile.release]
opt-level = 3
lto = true           # 链接时优化
codegen-units = 1    # 单线程编译，优化更好
strip = true         # 移除调试符号
panic = "abort"      # panic 时直接终止，减小体积

# macOS 特定配置
[target.'cfg(target_os = "macos")'.dependencies]
# macOS 特定依赖

# Windows 特定配置
[target.'cfg(target_os = "windows")'.dependencies]
# Windows 特定依赖
```

资源嵌入与加载：

```rust
use gpui::*;

// ===== 方式 1：编译时嵌入资源 =====
const APP_ICON: &[u8] = include_bytes!("../assets/icon.png");
const APP_SVG: &str = include_str!("../assets/icon.svg");
const FONT_DATA: &[u8] = include_bytes!("../assets/font.ttf");

// ===== 方式 2：运行时加载资源 =====
fn get_asset_path() -> std::path::PathBuf {
    // 开发环境：从项目目录加载
    if cfg!(debug_assertions) {
        std::path::PathBuf::from(env!("CARGO_MANIFEST_DIR")).join("assets")
    } else {
        // 发布环境：从可执行文件同级目录加载
        std::env::current_exe()
            .ok()
            .and_then(|p| p.parent().map(|p| p.join("assets")))
            .unwrap_or_else(|| std::path::PathBuf::from("assets"))
    }
}

// ===== 应用配置 =====
struct App {
    title: String,
}

impl App {
    fn new() -> Self {
        Self {
            title: "我的 GPUI 应用".to_string(),
        }
    }
}

impl Render for App {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .size_full()
            .flex()
            .items_center()
            .justify_center()
            .bg(rgb(0x1e1e2e))
            .text_color(rgb(0xcdd6f4))
            .child(
                div()
                    .flex()
                    .flex_col()
                    .items_center()
                    .gap_4()
                    .child(div().text_2xl().child(self.title.clone()))
                    .child(div().text_sm().text_color(rgb(0x6c7086)).child("v1.0.0"))
            )
    }
}

fn main() {
    // ===== 应用初始化 =====
    Application::new()
        .with_assets(get_asset_path())  // 配置资源路径
        .run(|cx: &mut App| {
            // 设置应用选项
            cx.set_menus(vec![
                Menu {
                    name: "文件".into(),
                    items: vec![
                        MenuItem::action("新建", "new"),
                        MenuItem::action("打开", "open"),
                        MenuItem::action("保存", "save"),
                        MenuItem::separator(),
                        MenuItem::action("退出", "quit"),
                    ],
                },
                Menu {
                    name: "编辑".into(),
                    items: vec![
                        MenuItem::action("撤销", "undo"),
                        MenuItem::action("重做", "redo"),
                    ],
                },
            ]);

            // 打开主窗口
            cx.open_window(
                WindowOptions {
                    window_bounds: Some(WindowBounds::Windowed(Bounds {
                        origin: Default::default(),
                        size: size(px(800.), px(600.)),
                    })),
                    titlebar: Some(TitlebarOptions {
                        title: Some("我的 GPUI 应用".into()),
                        appears_transparent: false,
                        traffic_light_position: None,
                    }),
                    ..Default::default()
                },
                |_window, cx| {
                    cx.new(|_cx| App::new())
                },
            )
            .unwrap();

            cx.activate(true);
        });
}
```

构建与打包脚本 `scripts/build.sh`：

```bash
#!/bin/bash
set -e

APP_NAME="my-gpui-app"
VERSION="1.0.0"

echo "=== 清理旧构建 ==="
rm -rf target/release/bundle

echo "=== 编译 Release 版本 ==="
cargo build --release

echo "=== 复制资源 ==="
mkdir -p target/release/bundle/assets
cp -r assets/* target/release/bundle/assets/

# ===== 平台特定打包 =====
OS=$(uname -s)

if [ "$OS" = "Darwin" ]; then
    echo "=== 打包 macOS .app ==="
    APP_DIR="target/release/bundle/${APP_NAME}.app"
    mkdir -p "${APP_DIR}/Contents/MacOS"
    mkdir -p "${APP_DIR}/Contents/Resources"

    # 复制可执行文件
    cp "target/release/${APP_NAME}" "${APP_DIR}/Contents/MacOS/"

    # 复制图标
    cp "assets/icon.icns" "${APP_DIR}/Contents/Resources/"

    # 创建 Info.plist
    cat > "${APP_DIR}/Contents/Info.plist" << EOF
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>CFBundleName</key>
    <string>${APP_NAME}</string>
    <key>CFBundleIdentifier</key>
    <string>com.example.${APP_NAME}</string>
    <key>CFBundleVersion</key>
    <string>${VERSION}</string>
    <key>CFBundleExecutable</key>
    <string>${APP_NAME}</string>
    <key>CFBundleIconFile</key>
    <string>icon</string>
    <key>NSHighResolutionCapable</key>
    <true/>
</dict>
</plist>
EOF

    echo "macOS 应用已打包: ${APP_DIR}"

elif [ "$OS" = "Linux" ]; then
    echo "=== 打包 Linux AppImage ==="
    # 使用 cargo-bundle 或 AppImage 工具
    cargo install cargo-bundle
    cargo bundle --release

    echo "Linux 应用已打包"

elif [ "$OS" = "MINGW64_NT"* ] || [ "$OS" = "Windows_NT" ]; then
    echo "=== 打包 Windows ==="
    # 复制 exe 和资源
    mkdir -p "target/release/bundle"
    cp "target/release/${APP_NAME}.exe" "target/release/bundle/"
    cp -r assets "target/release/bundle/"

    echo "Windows 应用已打包"
fi

echo "=== 构建完成 ==="
echo "输出目录: target/release/bundle/"
```

GitHub Actions CI/CD `.github/workflows/release.yml`：

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

      - name: Build release
        run: cargo build --release

      - name: Package (macOS)
        if: matrix.os == 'macos-latest'
        run: |
          mkdir -p my-app.app/Contents/MacOS
          mkdir -p my-app.app/Contents/Resources
          cp target/release/my-gpui-app my-app.app/Contents/MacOS/
          # 创建 Info.plist 等...
          zip -r my-app-macos.zip my-app.app

      - name: Package (Linux)
        if: matrix.os == 'ubuntu-latest'
        run: |
          tar -czf my-app-linux.tar.gz -C target/release my-gpui-app

      - name: Package (Windows)
        if: matrix.os == 'windows-latest'
        run: |
          Compress-Archive -Path target\release\my-gpui-app.exe -DestinationPath my-app-windows.zip

      - name: Upload release
        uses: softprops/action-gh-release@v1
        with:
          files: |
            my-app-macos.zip
            my-app-linux.tar.gz
            my-app-windows.zip
```

发布检查清单：

```
┌─────────────────────────────────────────────────────────────┐
│                   GPUI 应用发布检查清单                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  □ 编译优化                                                 │
│    □ cargo build --release                                  │
│    □ Cargo.toml 配置 lto/codegen-units/strip                │
│    □ 测试 release 版本功能正常                              │
│                                                             │
│  □ 资源处理                                                 │
│    □ 图标、字体、SVG 等资源打包                             │
│    □ 资源路径在开发/发布环境都正确                          │
│    □ 考虑用 include_bytes! 嵌入关键资源                     │
│                                                             │
│  □ 平台打包                                                 │
│    □ macOS: .app bundle + Info.plist                        │
│    □ Windows: .exe + 资源目录                               │
│    □ Linux: AppImage 或 tar.gz                              │
│                                                             │
│  □ 代码签名（可选但推荐）                                   │
│    □ macOS: 公证 (notarize)                                 │
│    □ Windows: 代码签名证书                                  │
│                                                             │
│  □ 分发                                                     │
│    □ GitHub Release 上传安装包                              │
│    □ 自动更新机制 (可选)                                    │
│    □ 用户文档/README                                        │
│                                                             │
│  □ CI/CD                                                    │
│    □ GitHub Actions 自动构建                                │
│    □ 多平台矩阵构建                                         │
│    □ tag 触发自动发布                                       │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 总结

- `cargo build --release` + `Cargo.toml` 的 `[profile.release]` 优化生成生产二进制
- 资源用 `include_bytes!`/`include_str!` 嵌入或运行时加载，确保路径正确
- 平台打包：macOS `.app` bundle、Windows `.exe`、Linux AppImage
- 代码签名（macOS 公证、Windows 签名）避免用户安全警告
- GitHub Actions 实现多平台自动构建与发布
- **常见坑**：release 模式下 `debug_assertions` 为 false，注意条件编译；资源路径在打包后可能变化，用 `env!("CARGO_MANIFEST_DIR")` 或配置；macOS 公证需 Apple Developer 账号；Windows 签名证书需付费

---

## 课程结语

恭喜你完成了 GPUI 系统化教程的全部 25 讲！让我们回顾这段学习旅程：

**第 1-2 章** 建立了 GPUI 的基础认知：从设计哲学、环境搭建、应用生命周期，到 View/Render、Element、Flex 布局、样式系统、交互事件。这些是构建 GPUI 界面的日常工具。

**第 3-4 章** 深入了状态管理与事件交互：Model 状态容器、Context 观察者模式、Entity 事件订阅、全局状态，以及鼠标、键盘、焦点、拖拽等完整交互能力。这些让应用"动"起来。

**第 5 章** 提升了 UI 品质：动画让界面流畅，主题系统支持深色/浅色切换，SVG 图标增强视觉，自定义 Element 突破内置限制。

**第 6 章** 将所学应用于工程实践：TodoMVC 综合实战、性能优化、测试方法、发布部署，让你具备完整的 GPUI 工程能力。

GPUI 的学习曲线确实陡峭，尤其是"保留模式 + 即时风格"的混合范式、Model/View/Context 的状态管理、Element 的三阶段生命周期，初学时可能感到困惑。但请相信，当你理解了"状态驱动视图 + 观察者模式"的核心思想后，会发现 GPUI 的设计既严谨又优雅——它用 Rust 的类型系统保证了 UI 安全，用 GPU 加速实现了原生性能，用组合模式构建了复杂界面。

继续前进的方向：
- **阅读 Zed 源码**：Zed 编辑器是 GPUI 的最佳实践，阅读其源码能学到大量高级用法
- **贡献 GPUI 生态**：从提交 issue、PR 开始，参与 GPUI 框架本身的发展
- **构建自己的应用**：用 GPUI 实现一个你需要的工具——笔记应用、文件管理器、数据库客户端等
- **探索跨平台**：GPUI 支持 macOS、Linux、Windows，尝试在多平台发布你的应用
- **关注 GPUI 演进**：GPUI 仍在快速迭代，关注官方仓库的更新

GPUI 的官方资源：
- [Zed 仓库](https://github.com/zed-industries/zed)：GPUI 源码与 Zed 编辑器实现
- [GPUI 文档](https://docs.rs/gpui)：API 参考（部分）
- [Zed 博客](https://zed.dev/blog)：GPUI 设计理念与更新
- [GPUI 示例](https://github.com/zed-industries/zed/tree/main/crates/gpui/examples)：官方示例集

愿你在 GPUI 的世界里，构建出既快又美的桌面应用，享受 Rust + GPU 加速 GUI 开发的乐趣。Happy GPUI-ing!

