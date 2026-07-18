# SDL2 碰撞与物理 · 系统教程

> 一本面向游戏开发者的实战型教材，从零开始构建 2D 物理与碰撞系统。

---

## 课程总览

**预计讲数**：24 讲（6 章 × 4 讲）

**学习目标**：

1. 理解 2D 游戏中运动学的基本原理，能够独立实现位置、速度、加速度的数值积分。
2. 掌握主流碰撞检测算法（AABB、圆形、OBB、SAT），并能根据场景选择最优方案。
3. 理解碰撞响应机制，包括位置修正、速度反射、弹性系数与能量守恒。
4. 学会使用空间分区（网格、四叉树）优化大规模碰撞检测。
5. 具备集成第三方物理引擎（Box2D）的能力，并能将其应用于实际游戏项目。

**前置知识**：C/C++ 基础语法、SDL2 基本窗口与事件处理、初等向量数学。

**学习路径**：基础准备 → 运动学 → 碰撞检测 → 碰撞响应 → 高级碰撞 → 物理进阶与实战

---

## 详细章节目录

### 第 1 章 · 基础准备

- 第 01 讲：SDL2 环境搭建与项目骨架
- 第 02 讲：游戏循环与固定时间步长
- 第 03 讲：2D 向量数学基础
- 第 04 讲：基本图形与纹理渲染

### 第 2 章 · 运动学

- 第 05 讲：位置、速度与加速度
- 第 06 讲：重力与跳跃实现
- 第 07 讲：摩擦力与阻力
- 第 08 讲：抛体运动与轨迹预测

### 第 3 章 · 碰撞检测基础

- 第 09 讲：AABB 包围盒与碰撞检测
- 第 10 讲：圆形碰撞检测
- 第 11 讲：点与形状包含检测
- 第 12 讲：多重碰撞遍历策略

### 第 4 章 · 碰撞响应

- 第 13 讲：穿透深度与法向量
- 第 14 讲：位置修正（防穿透）
- 第 15 讲：速度反射与反弹
- 第 16 讲：弹性系数与能量损失

### 第 5 章 · 高级碰撞

- 第 17 讲：圆与矩形碰撞
- 第 18 讲：旋转矩形（OBB）碰撞
- 第 19 讲：分离轴定理 SAT
- 第 20 讲：射线投射与相交检测

### 第 6 章 · 物理进阶与实战

- 第 21 讲：刚体与冲量
- 第 22 讲：角动量与旋转物理
- 第 23 讲：空间分区优化（网格 / 四叉树）
- 第 24 讲：集成 Box2D 物理引擎

---

# 第 1 章 · 基础准备

物理与碰撞系统的搭建离不开一个稳定、可预测的运行环境。本章将带你从零搭建 SDL2 项目骨架，理解游戏循环的本质，掌握 2D 向量数学，并完成基本图形的渲染。这些内容看似简单，却是后续所有物理计算的基石——一个不稳定的帧率或错误的向量运算，会让最精妙的物理算法也变得毫无意义。

## 第 01 讲 · SDL2 环境搭建与项目骨架

### 概念

SDL2（Simple DirectMedia Layer 2）是一个跨平台的多媒体开发库，提供对窗口、图形、音频、输入设备的底层访问。在物理与碰撞教程中，SDL2 充当"显示与输入层"，负责把我们的物理计算结果绘制到屏幕上，并把玩家的键盘鼠标输入传递给游戏逻辑。

一个最小可用的 SDL2 项目骨架包含四个核心组件：初始化、窗口创建、事件循环、资源清理。这四个组件构成所有 SDL2 程序的通用模板。

### 原理

SDL2 的设计哲学是"薄封装"——它不试图替你管理游戏对象、场景图或物理状态，只提供最底层的硬件访问接口。这意味着：

- **初始化是分模块的**：`SDL_Init(SDL_INIT_VIDEO)` 只初始化视频子系统，若需要音频则需 `SDL_INIT_AUDIO`，多个子系统可用按位或 `|` 组合。
- **窗口与渲染器分离**：`SDL_Window` 表示操作系统窗口（可见的矩形区域），`SDL_Renderer` 表示绘制上下文（实际执行绘图命令的对象）。这种分离让同一窗口可以切换不同的渲染后端（OpenGL、DirectX、软件渲染）。
- **资源必须显式释放**：SDL2 是 C 语言库，没有 RAII 机制。所有 `SDL_Create*` 调用必须有对应的 `SDL_Destroy*`，否则会内存泄漏。

理解这些设计原则，能避免后续开发中 90% 的"为什么窗口一闪就关了"或"为什么内存越跑越高"的问题。

### 例子

下面是一个最小可运行的 SDL2 骨架，后续所有讲次的代码都基于这个模板扩展：

```cpp
// main.cpp
#include <SDL2/SDL.h>
#include <iostream>

const int WINDOW_W = 800;
const int WINDOW_H = 600;

int main(int argc, char* argv[]) {
    // 1. 初始化 SDL 视频子系统
    if (SDL_Init(SDL_INIT_VIDEO) != 0) {
        std::cerr << "SDL_Init 失败: " << SDL_GetError() << std::endl;
        return 1;
    }

    // 2. 创建窗口与渲染器
    SDL_Window* window = SDL_CreateWindow(
        "SDL2 Physics Demo",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED,
        WINDOW_W, WINDOW_H,
        SDL_WINDOW_SHOWN
    );
    SDL_Renderer* renderer = SDL_CreateRenderer(
        window, -1, SDL_RENDERER_ACCELERATED | SDL_RENDERER_PRESENTVSYNC
    );

    // 3. 主循环
    bool running = true;
    while (running) {
        SDL_Event event;
        while (SDL_PollEvent(&event)) {
            if (event.type == SDL_QUIT) running = false;
        }

        // 清屏（深灰色背景）
        SDL_SetRenderDrawColor(renderer, 30, 30, 30, 255);
        SDL_RenderClear(renderer);

        // 绘制一个白色矩形作为占位
        SDL_Rect rect = { 360, 280, 80, 40 };
        SDL_SetRenderDrawColor(renderer, 255, 255, 255, 255);
        SDL_RenderFillRect(renderer, &rect);

        // 呈现
        SDL_RenderPresent(renderer);
    }

    // 4. 清理资源
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

**编译命令**（Linux / macOS）：

```bash
g++ main.cpp -o demo $(sdl2-config --cflags --libs)
```

**编译命令**（Windows + MSYS2）：

```bash
g++ main.cpp -o demo.exe -lSDL2main -lSDL2
```

### 总结

- SDL2 项目骨架由"初始化 → 创建窗口/渲染器 → 主循环 → 清理"四步构成，这是所有后续讲次的通用模板。
- 窗口（`SDL_Window`）与渲染器（`SDL_Renderer`）是分离的：窗口管可见区域，渲染器管绘制命令。
- 所有 `SDL_Create*` 必须配对 `SDL_Destroy*`，否则内存泄漏。
- 编译时务必链接正确的库（`sdl2-config` 工具能自动生成正确的编译参数）。
- **常见坑**：忘记 `SDL_Quit()` 会导致下次启动时子系统状态异常；忘记 `SDL_RenderPresent` 会导致屏幕一片黑。

---

## 第 02 讲 · 游戏循环与固定时间步长

### 概念

游戏循环（Game Loop）是游戏程序的心脏，它反复执行"处理输入 → 更新状态 → 渲染画面"三个步骤，直到玩家退出。**固定时间步长（Fixed Timestep）** 是一种更新策略：无论实际帧率多高，物理状态都以恒定的时间间隔（如 1/60 秒）更新，从而保证物理模拟的确定性与稳定性。

### 原理

物理积分对时间步长极其敏感。考虑一个简单的自由落体：

```
y(t+Δt) = y(t) + v(t)·Δt
v(t+Δt) = v(t) + g·Δt
```

如果 Δt 在每一帧都变化（比如 0.016s、0.020s、0.011s 交替），那么物体下落的轨迹会因浮点误差累积而偏离理论值，甚至出现"穿墙"或"抖动"。

**固定时间步长**的核心思想是：把物理更新与渲染解耦。渲染按显示器刷新率（60Hz、144Hz 都可以），但物理更新永远以固定 Δt 推进。具体做法是引入一个"时间累加器"：

```
每帧：
  accumulator += 实际经过的时间
  while (accumulator >= FIXED_DT):
      physics_update(FIXED_DT)
      accumulator -= FIXED_DT
  render()
```

这样无论帧率如何波动，物理状态都按 1/60 秒一拍地稳定推进。

### 例子

```cpp
#include <SDL2/SDL.h>
#include <cstdint>

const int WINDOW_W = 800;
const int WINDOW_H = 600;
const double FIXED_DT = 1.0 / 60.0;  // 物理步长：1/60 秒

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Fixed Timestep",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED,
        WINDOW_W, WINDOW_H, SDL_WINDOW_SHOWN);
    SDL_Renderer* renderer = SDL_CreateRenderer(
        window, -1, SDL_RENDERER_ACCELERATED);

    // 物理状态：一个下落的小球
    double y = 0.0;
    double v = 0.0;
    const double g = 980.0;  // 像素/秒²，模拟重力

    Uint64 now = SDL_GetPerformanceCounter();
    Uint64 last = now;
    double accumulator = 0.0;

    bool running = true;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) {
            if (e.type == SDL_QUIT) running = false;
        }

        // 计算实际经过的秒数
        now = SDL_GetPerformanceCounter();
        double frame_time = (double)(now - last) / SDL_GetPerformanceFrequency();
        last = now;

        // 防止暂停后的大跳变（如切换窗口）
        if (frame_time > 0.25) frame_time = 0.25;

        accumulator += frame_time;
        while (accumulator >= FIXED_DT) {
            // 物理更新：固定步长
            v += g * FIXED_DT;
            y += v * FIXED_DT;
            if (y > WINDOW_H - 20) { y = WINDOW_H - 20; v = -v * 0.7; }  // 弹地
            accumulator -= FIXED_DT;
        }

        // 渲染
        SDL_SetRenderDrawColor(renderer, 20, 20, 30, 255);
        SDL_RenderClear(renderer);
        SDL_SetRenderDrawColor(renderer, 255, 200, 80, 255);
        SDL_Rect ball = { WINDOW_W/2 - 10, (int)y, 20, 20 };
        SDL_RenderFillRect(renderer, &ball);
        SDL_RenderPresent(renderer);
    }

    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- 固定时间步长是物理稳定性的基石：无论帧率如何波动，物理状态都按恒定 Δt 推进。
- 使用累加器（accumulator）模式实现"渲染快、物理稳"的解耦。
- `SDL_GetPerformanceCounter()` 提供高精度计时（纳秒级），远比 `SDL_GetTicks()`（毫秒级）精确。
- **常见坑**：必须对 `frame_time` 设上限（如 0.25s），否则窗口切换后的"时间黑洞"会让物理瞬间爆炸。
- **进阶**：可在累加器剩余的小数部分做插值渲染（`alpha = accumulator / FIXED_DT`），让画面更平滑，但物理状态保持离散。

---

## 第 03 讲 · 2D 向量数学基础

### 概念

2D 向量（Vector2）是物理与碰撞计算的"原子"。一个向量有两个分量 `(x, y)`，既可以表示**位置**（点坐标），也可以表示**方向与大小**（速度、力、法向量）。掌握向量的基本运算，是理解后续所有物理公式的前提。

### 原理

向量的核心运算有六种，每一种都有明确的几何意义：

| 运算 | 公式 | 几何意义 |
|------|------|----------|
| 加法 | `(a.x+b.x, a.y+b.y)` | 两个位移的合成 |
| 减法 | `(a.x-b.x, a.y-b.y)` | 从 b 指向 a 的位移 |
| 数乘 | `(s·a.x, s·a.y)` | 沿原方向缩放 |
| 点积 | `a.x·b.x + a.y·b.y` | 投影长度 × 被投影向量长度 |
| 叉积（2D 标量） | `a.x·b.y - a.y·b.x` | 两向量张成的有向面积 |
| 长度 | `√(x² + y²)` | 向量的模 |
| 归一化 | `(x/len, y/len)` | 转为单位向量（长度为 1） |

**点积**是碰撞检测中最重要的运算：`a · b = |a| |b| cos θ`。当点积为正，两向量夹角小于 90°；为负则大于 90°；为零则垂直。SAT 算法、速度反射、光照计算都依赖点积。

**叉积**在 2D 中退化为标量，正值表示 b 在 a 的逆时针方向，负值表示顺时针。它常用于判断点在多边形的哪一侧、计算力矩等。

### 例子

下面是一个完整的 `Vec2` 实现，后续所有讲次都会复用：

```cpp
// vec2.h
#pragma once
#include <cmath>

struct Vec2 {
    float x, y;

    Vec2(float x = 0.0f, float y = 0.0f) : x(x), y(y) {}

    Vec2 operator+(const Vec2& o) const { return { x + o.x, y + o.y }; }
    Vec2 operator-(const Vec2& o) const { return { x - o.x, y - o.y }; }
    Vec2 operator*(float s) const { return { x * s, y * s }; }
    Vec2 operator/(float s) const { return { x / s, y / s }; }
    Vec2& operator+=(const Vec2& o) { x += o.x; y += o.y; return *this; }
    Vec2& operator-=(const Vec2& o) { x -= o.x; y -= o.y; return *this; }

    float dot(const Vec2& o) const { return x * o.x + y * o.y; }
    float cross(const Vec2& o) const { return x * o.y - y * o.x; }
    float length_sq() const { return x * x + y * y; }       // 长度平方（避免开方）
    float length() const { return std::sqrt(length_sq()); }

    Vec2 normalized() const {
        float len = length();
        return len > 1e-6f ? Vec2(x / len, y / len) : Vec2(0, 0);
    }

    // 垂直向量（逆时针旋转 90°）
    Vec2 perp() const { return { -y, x }; }
};
```

**使用示例**：计算小球反弹方向。

```cpp
Vec2 velocity(3.0f, 4.0f);          // 速度向量
Vec2 normal(0.0f, -1.0f);           // 地面法向量（向上）
Vec2 normalized_n = normal.normalized();

// 反射公式：v' = v - 2(v·n)n
float dot = velocity.dot(normalized_n);
Vec2 reflected = velocity - normalized_n * (2.0f * dot);
// 结果：velocity 变为 (3, -4)，垂直分量翻转
```

### 总结

- `Vec2` 是物理计算的通用语言，封装好之后所有公式都能写得简洁优雅。
- **点积**判断方向关系（投影、夹角），**叉积**判断左右关系（旋转方向、点在边的哪侧）。
- 比较长度时优先用 `length_sq()`，避免不必要的 `sqrt` 调用——在每帧上万次碰撞检测中，这是显著的性能优化。
- 归一化前必须检查长度是否为零，否则会得到 NaN。
- `perp()` 返回逆时针 90° 的垂直向量，在 SAT 算法中用于求分离轴。

---

## 第 04 讲 · 基本图形与纹理渲染

### 概念

物理计算的结果需要可视化才有意义。SDL2 提供两类基础绘制能力：**基本图形**（矩形、线段、点）和**纹理**（加载自 PNG/BMP 的位图）。在物理调试阶段，基本图形足以表达所有碰撞体；在最终游戏中，纹理让物体看起来"真实"。

### 原理

SDL2 的渲染器是**立即模式**的：每帧调用一次 `SDL_RenderClear` 清屏，然后依次调用 `SDL_RenderFillRect`、`SDL_RenderCopy` 等绘制命令，最后 `SDL_RenderPresent` 一次性把后台缓冲区呈现到屏幕。这种模式简单直观，但要注意：

- **绘制顺序决定层级**：后绘制的覆盖先绘制的。背景应最先绘制，前景物体其次，UI 最后。
- **颜色状态是持久的**：`SDL_SetRenderDrawColor` 设置的颜色会一直生效，直到下次设置。每次绘制前显式设置颜色是好习惯。
- **纹理需要硬件加速**：`SDL_TEXTUREACCESS_STATIC` 适合不变的图，`SDL_TEXTUREACCESS_STREAMING` 适合每帧更新的图（如视频），`SDL_TEXTUREACCESS_TARGET` 适合渲染到纹理。
- **透明度**：纹理的 alpha 通道需要 `SDL_SetTextureBlendMode(tex, SDL_BLENDMODE_BLEND)` 才能正确混合。

### 例子

下面演示如何绘制矩形（用于调试碰撞体）、线段（用于法向量）、圆（用于圆形碰撞体）以及加载纹理：

```cpp
#include <SDL2/SDL.h>
#include <SDL2/SDL_image.h>
#include <cmath>

// 绘制填充矩形（AABB 调试用）
void draw_rect(SDL_Renderer* r, int x, int y, int w, int h,
               Uint8 red, Uint8 green, Uint8 blue) {
    SDL_SetRenderDrawColor(r, red, green, blue, 255);
    SDL_Rect rect = { x, y, w, h };
    SDL_RenderFillRect(r, &rect);
}

// 绘制矩形边框（碰撞体轮廓）
void draw_rect_outline(SDL_Renderer* r, int x, int y, int w, int h,
                       Uint8 red, Uint8 green, Uint8 blue) {
    SDL_SetRenderDrawColor(r, red, green, blue, 255);
    SDL_Rect rect = { x, y, w, h };
    SDL_RenderDrawRect(r, &rect);
}

// 绘制线段（法向量、速度向量调试用）
void draw_line(SDL_Renderer* r, float x1, float y1, float x2, float y2,
               Uint8 red, Uint8 green, Uint8 blue) {
    SDL_SetRenderDrawColor(r, red, green, blue, 255);
    SDL_RenderDrawLine(r, (int)x1, (int)y1, (int)x2, (int)y2);
}

// 绘制圆（圆形碰撞体）—— SDL2 没有原生圆函数，用分段线段近似
void draw_circle(SDL_Renderer* r, int cx, int cy, int radius,
                 Uint8 red, Uint8 green, Uint8 blue) {
    SDL_SetRenderDrawColor(r, red, green, blue, 255);
    const int segments = 32;
    for (int i = 0; i < segments; ++i) {
        float a1 = (float)i / segments * 2.0f * M_PI;
        float a2 = (float)(i + 1) / segments * 2.0f * M_PI;
        SDL_RenderDrawLine(r,
            cx + (int)(radius * std::cos(a1)), cy + (int)(radius * std::sin(a1)),
            cx + (int)(radius * std::cos(a2)), cy + (int)(radius * std::sin(a2)));
    }
}

// 加载纹理（PNG）
SDL_Texture* load_texture(SDL_Renderer* r, const char* path) {
    SDL_Surface* surface = IMG_Load(path);
    if (!surface) return nullptr;
    SDL_Texture* tex = SDL_CreateTextureFromSurface(r, surface);
    SDL_SetTextureBlendMode(tex, SDL_BLENDMODE_BLEND);  // 启用透明
    SDL_FreeSurface(surface);
    return tex;
}

// 在主循环中使用
// draw_rect(renderer, 100, 100, 50, 50, 255, 0, 0);          // 红色方块
// draw_circle(renderer, 300, 300, 25, 0, 255, 0);            // 绿色圆
// draw_line(renderer, 300, 300, 300, 250, 255, 255, 0);      // 黄色法向量
// SDL_RenderCopy(renderer, ball_tex, nullptr, &dst_rect);    // 绘制纹理
```

### 总结

- SDL2 渲染采用立即模式：清屏 → 绘制 → 呈现，绘制顺序即层级顺序。
- 调试阶段用基本图形（矩形、圆、线段）足以表达所有碰撞体，且性能开销极小。
- SDL2 没有原生圆函数，需用分段线段近似，32 段已足够平滑。
- 加载 PNG 需要额外的 SDL2_image 库，记得 `SDL_SetTextureBlendMode` 启用透明混合。
- **常见坑**：忘记 `SDL_FreeSurface` 会导致表面内存泄漏；忘记设置混合模式会导致 PNG 透明区域显示为黑色。

---

# 第 2 章 · 运动学

运动学（Kinematics）研究物体的运动规律，但不涉及运动的原因（力）。本章将带你从最基本的位置-速度-加速度三件套出发，逐步实现重力、跳跃、摩擦力与抛体运动。这些是任何 2D 平台游戏、弹球游戏、射击游戏的物理基础。掌握运动学，你就掌握了让物体"动起来"的全部秘密。

## 第 05 讲 · 位置、速度与加速度

### 概念

在 2D 物理中，物体的运动状态由三个量描述：

- **位置（Position）** `p`：物体在空间中的坐标，单位通常是像素。
- **速度（Velocity）** `v`：位置的变化率，单位是像素/秒。
- **加速度（Acceleration）** `a`：速度的变化率，单位是像素/秒²。

三者构成一条因果链：加速度改变速度，速度改变位置。物理引擎的核心工作，就是在每一帧根据加速度更新速度，再根据速度更新位置。

### 原理

最简单的更新方法是**欧拉积分（Euler Integration）**：

```
v(t+Δt) = v(t) + a · Δt
p(t+Δt) = p(t) + v(t+Δt) · Δt
```

这叫"半隐式欧拉"（Semi-implicit Euler），先用旧速度算新速度，再用新速度算新位置。它比"显式欧拉"（用旧速度算新位置）更稳定，是游戏开发中最常用的积分方法。

为什么不用更精确的方法（如龙格-库塔）？因为游戏物理不需要科学级的精度，半隐式欧拉在 60Hz 步长下已经足够稳定，且计算量极小。对于需要高精度的场景（如轨道力学），可考虑**Verlet 积分**，它对位置约束（如弹簧、绳索）更友好。

### 例子

```cpp
#include <SDL2/SDL.h>

struct Body {
    Vec2 pos;
    Vec2 vel;
    Vec2 acc;
};

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Kinematics",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 800, 600, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, 0);

    Body body;
    body.pos = Vec2(400.0f, 300.0f);
    body.vel = Vec2(0.0f, 0.0f);
    body.acc = Vec2(50.0f, 0.0f);  // 向右的恒定加速度

    const double FIXED_DT = 1.0 / 60.0;
    double accumulator = 0.0;
    Uint64 last = SDL_GetPerformanceCounter();

    bool running = true;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) if (e.type == SDL_QUIT) running = false;

        Uint64 now = SDL_GetPerformanceCounter();
        double frame_time = (double)(now - last) / SDL_GetPerformanceFrequency();
        last = now;
        if (frame_time > 0.25) frame_time = 0.25;
        accumulator += frame_time;

        while (accumulator >= FIXED_DT) {
            // 半隐式欧拉积分
            body.vel += body.acc * (float)FIXED_DT;
            body.pos += body.vel * (float)FIXED_DT;

            // 边界反弹
            if (body.pos.x > 780) { body.pos.x = 780; body.vel.x = -body.vel.x * 0.9f; }
            if (body.pos.x < 20)  { body.pos.x = 20;  body.vel.x = -body.vel.x * 0.9f; }
            if (body.pos.y > 580) { body.pos.y = 580; body.vel.y = -body.vel.y * 0.9f; }
            if (body.pos.y < 20)  { body.pos.y = 20;  body.vel.y = -body.vel.y * 0.9f; }

            accumulator -= FIXED_DT;
        }

        SDL_SetRenderDrawColor(renderer, 20, 20, 30, 255);
        SDL_RenderClear(renderer);
        SDL_SetRenderDrawColor(renderer, 255, 200, 80, 255);
        SDL_Rect r = { (int)body.pos.x - 10, (int)body.pos.y - 10, 20, 20 };
        SDL_RenderFillRect(renderer, &r);
        SDL_RenderPresent(renderer);
    }

    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- 位置、速度、加速度构成运动学三件套，加速度是"因"，速度和位置是"果"。
- 半隐式欧拉积分（先更新速度，再更新位置）是游戏物理的黄金标准，兼顾精度与性能。
- 加速度可以是恒定的（如重力），也可以是变化的（如玩家输入、弹簧力）。
- **常见坑**：用 `SDL_GetTicks()`（毫秒）做物理积分会精度不足，必须用 `SDL_GetPerformanceCounter()`（纳秒）。
- **进阶**：对于布料、绳索等约束系统，考虑 Verlet 积分；对于轨道力学，考虑 RK4。

---

## 第 06 讲 · 重力与跳跃实现

### 概念

**重力（Gravity）** 是一种持续的向下加速度，让物体产生"下落"的感觉。在 2D 游戏中，重力通常是一个常量 `g`（如 980 像素/秒²，模拟现实地球重力的 9.8 m/s² 缩放）。

**跳跃（Jump）** 是一个瞬时的速度脉冲：玩家按下跳跃键时，给角色一个向上的初速度 `v_jump`，之后角色受重力影响做抛体运动，直到落地。跳跃的高度由 `h = v_jump² / (2g)` 决定。

### 原理

跳跃的实现有几个关键点：

1. **跳跃只能在地面触发**：空中按跳跃键无效（除非实现二段跳）。需要一个 `on_ground` 标志，在碰撞响应中设置。
2. **跳跃初速度决定高度**：`v_jump = √(2 · g · h)`。想要跳 100 像素高，g=980 时，`v_jump = √(2·980·100) ≈ 443` 像素/秒。
3. **可变跳跃高度**：玩家按住跳跃键跳得高，松开跳得低。实现方式是检测跳跃键松开时，若 `v.y < 0`（上升中），则把 `v.y` 乘以 0.5（截断上升速度）。
4. **土狼时间（Coyote Time）**：玩家刚离开平台的一小段时间内（如 0.1 秒）仍可跳跃，提升手感。
5. **跳跃缓冲（Jump Buffer）**：玩家落地前一小段时间内按跳跃键，落地后立即触发跳跃，避免"差一帧"的挫败感。

这些"小技巧"是商业游戏与业余游戏的分水岭。

### 例子

```cpp
struct Player {
    Vec2 pos;
    Vec2 vel;
    bool on_ground;
    float coyote_timer;       // 土狼时间剩余
    float jump_buffer_timer;  // 跳跃缓冲剩余
};

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Jump Demo",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 800, 600, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, 0);

    Player p;
    p.pos = Vec2(400, 500);
    p.vel = Vec2(0, 0);
    p.on_ground = false;
    p.coyote_timer = 0;
    p.jump_buffer_timer = 0;

    const float GRAVITY = 1500.0f;        // 像素/秒²
    const float MOVE_SPEED = 300.0f;      // 水平移动速度
    const float JUMP_VELOCITY = -600.0f;  // 跳跃初速度（向上为负）
    const float COYOTE_TIME = 0.1f;       // 土狼时间窗口
    const float JUMP_BUFFER = 0.1f;       // 跳跃缓冲窗口
    const float GROUND_Y = 540;           // 地面 y 坐标

    const double FIXED_DT = 1.0 / 60.0;
    double accumulator = 0.0;
    Uint64 last = SDL_GetPerformanceCounter();
    const Uint8* keys = SDL_GetKeyboardState(nullptr);

    bool running = true;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) {
            if (e.type == SDL_QUIT) running = false;
            if (e.type == SDL_KEYDOWN && e.key.keysym.sym == SDLK_SPACE) {
                p.jump_buffer_timer = JUMP_BUFFER;  // 记录跳跃意图
            }
            if (e.type == SDL_KEYUP && e.key.keysym.sym == SDLK_SPACE) {
                // 可变跳跃高度：松开时若上升中，截断速度
                if (p.vel.y < 0) p.vel.y *= 0.5f;
            }
        }

        Uint64 now = SDL_GetPerformanceCounter();
        double frame_time = (double)(now - last) / SDL_GetPerformanceFrequency();
        last = now;
        if (frame_time > 0.25) frame_time = 0.25;
        accumulator += frame_time;

        while (accumulator >= FIXED_DT) {
            float dt = (float)FIXED_DT;

            // 水平输入
            float move = 0;
            if (keys[SDL_SCANCODE_LEFT])  move -= 1;
            if (keys[SDL_SCANCODE_RIGHT]) move += 1;
            p.vel.x = move * MOVE_SPEED;

            // 重力
            p.vel.y += GRAVITY * dt;

            // 土狼时间倒计时
            if (p.on_ground) p.coyote_timer = COYOTE_TIME;
            else p.coyote_timer -= dt;

            // 跳跃缓冲倒计时
            p.jump_buffer_timer -= dt;

            // 触发跳跃：缓冲有效 且 土狼时间有效
            if (p.jump_buffer_timer > 0 && p.coyote_timer > 0) {
                p.vel.y = JUMP_VELOCITY;
                p.on_ground = false;
                p.coyote_timer = 0;
                p.jump_buffer_timer = 0;
            }

            // 更新位置
            p.pos += p.vel * dt;

            // 地面碰撞
            if (p.pos.y > GROUND_Y) {
                p.pos.y = GROUND_Y;
                p.vel.y = 0;
                p.on_ground = true;
            } else {
                p.on_ground = false;
            }

            // 边界
            if (p.pos.x < 20) p.pos.x = 20;
            if (p.pos.x > 780) p.pos.x = 780;

            accumulator -= FIXED_DT;
        }

        SDL_SetRenderDrawColor(renderer, 20, 20, 30, 255);
        SDL_RenderClear(renderer);

        // 地面
        SDL_SetRenderDrawColor(renderer, 80, 80, 80, 255);
        SDL_Rect ground = { 0, (int)GROUND_Y + 20, 800, 600 - (int)GROUND_Y };
        SDL_RenderFillRect(renderer, &ground);

        // 玩家
        SDL_SetRenderDrawColor(renderer, 100, 200, 255, 255);
        SDL_Rect player_rect = { (int)p.pos.x - 15, (int)p.pos.y - 30, 30, 30 };
        SDL_RenderFillRect(renderer, &player_rect);

        SDL_RenderPresent(renderer);
    }

    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- 跳跃本质是给角色一个向上初速度，之后受重力做抛体运动。
- 跳跃高度公式：`h = v² / (2g)`，据此反推所需初速度。
- **可变跳跃高度**：松开跳跃键时截断上升速度，让玩家控制跳跃高度。
- **土狼时间**与**跳跃缓冲**是提升手感的两大法宝，几乎所有商业平台游戏都使用。
- **常见坑**：跳跃键用 `SDL_KEYDOWN` 事件而非轮询状态，避免连续触发；记得在 `SDL_KEYDOWN` 中检查 `e.key.repeat == 0`，过滤按键重复。

---

## 第 07 讲 · 摩擦力与阻力

### 概念

**摩擦力（Friction）** 是阻碍物体相对运动的力，分为静摩擦（阻止启动）和动摩擦（阻碍运动）。在 2D 游戏中，摩擦力让角色松开方向键后逐渐停下，而不是永远滑动。

**空气阻力（Drag）** 是一种与速度相关的阻力，速度越快阻力越大。它让抛体运动的水平速度逐渐衰减，让快速移动的物体最终减速到零。

### 原理

游戏中的摩擦力通常用**指数衰减**实现，而非物理上的线性摩擦：

```
v(t+Δt) = v(t) · e^(-μ·Δt)
```

其中 μ 是摩擦系数。这种实现有两个优点：

1. **永远不会让速度反向**：线性摩擦 `v -= μ·Δt` 可能让速度过零后反向，导致物体抖动。
2. **帧率无关**：`e^(-μ·Δt)` 在任何 Δt 下都给出正确的衰减比例。

对于小 Δt，可以用泰勒展开近似：`e^(-μ·Δt) ≈ 1 - μ·Δt`，但要注意 μ·Δt 不能大于 1，否则会出现负速度。

空气阻力通常用**二次阻力**：`F_drag = -k · |v| · v`，即阻力大小与速度平方成正比，方向与速度相反。这模拟了真实流体力学。

### 例子

```cpp
// 摩擦力：指数衰减
void apply_friction(Vec2& vel, float friction_coeff, float dt) {
    // friction_coeff 越大，摩擦越强（典型值 3~10）
    float decay = std::exp(-friction_coeff * dt);
    vel.x *= decay;
    vel.y *= decay;
}

// 空气阻力：二次衰减
void apply_drag(Vec2& vel, float drag_coeff, float dt) {
    // drag_coeff 典型值 0.001~0.01
    float speed = vel.length();
    if (speed > 1e-3f) {
        Vec2 drag_force = vel.normalized() * (-drag_coeff * speed * speed);
        vel += drag_force * dt;
    }
}

// 在玩家更新中使用
void update_player(Player& p, float dt) {
    const float GRAVITY = 1500.0f;
    const float MOVE_SPEED = 300.0f;
    const float GROUND_FRICTION = 8.0f;   // 地面摩擦
    const float AIR_FRICTION = 0.5f;      // 空气摩擦（很小）
    const float DRAG_COEFF = 0.0005f;

    // 输入
    float move = 0;
    const Uint8* keys = SDL_GetKeyboardState(nullptr);
    if (keys[SDL_SCANCODE_LEFT])  move -= 1;
    if (keys[SDL_SCANCODE_RIGHT]) move += 1;

    if (move != 0) {
        p.vel.x = move * MOVE_SPEED;
    } else {
        // 无输入时施加摩擦
        if (p.on_ground) apply_friction(p.vel, GROUND_FRICTION, dt);
        else             apply_friction(p.vel, AIR_FRICTION, dt);
    }

    // 重力
    p.vel.y += GRAVITY * dt;

    // 空气阻力（始终施加）
    apply_drag(p.vel, DRAG_COEFF, dt);

    // 更新位置
    p.pos += p.vel * dt;
}
```

### 总结

- 摩擦力让物体逐渐停下，避免"永远滑动"的不真实感。
- **指数衰减** `v *= e^(-μ·Δt)` 是游戏中最稳定的摩擦实现，帧率无关且不会反向。
- 空气阻力用二次模型 `F = -k|v|v`，速度越快阻力越大。
- 地面摩擦系数远大于空气摩擦（8 vs 0.5），让地面移动响应快、空中漂移感强。
- **常见坑**：不要用 `v.x -= friction * dt`，这会让速度过零后反向，导致物体抖动；务必用乘法衰减。

---

## 第 08 讲 · 抛体运动与轨迹预测

### 概念

**抛体运动（Projectile Motion）** 是水平匀速 + 垂直匀加速的合成运动。投掷手雷、发射炮弹、跳跃攻击——所有这些都可以用抛体运动建模。

**轨迹预测（Trajectory Prediction）** 是在物体发射前，提前计算并绘制它的飞行路径。这在策略游戏（如《愤怒的小鸟》瞄准线）和射击游戏（如狙击枪弹道指示）中非常常见。

### 原理

抛体运动方程：

```
x(t) = x₀ + vₓ · t
y(t) = y₀ + v_y · t + 0.5 · g · t²
```

给定初速度 `(vₓ, v_y)` 和重力 `g`，任意时刻 `t` 的位置都可解析求出。这意味着我们**不需要模拟**就能预测轨迹——只需在循环中代入不同的 `t` 值，得到一系列点，连成线即可。

轨迹预测的步长选择很重要：太密（如 0.01s）会浪费性能，太疏（如 0.5s）会漏掉碰撞。通常 0.05~0.1s 是合理的折中。

对于发射角度计算（已知目标位置，求发射速度），可用抛体运动公式反解：

```
vₓ = (x_target - x₀) / t
v_y = (y_target - y₀ - 0.5·g·t²) / t
```

给定飞行时间 `t`，即可求出所需初速度。

### 例子

```cpp
#include <SDL2/SDL.h>
#include <vector>

struct Projectile {
    Vec2 pos;
    Vec2 vel;
    bool active;
};

// 预测轨迹：返回一系列采样点
std::vector<Vec2> predict_trajectory(Vec2 start, Vec2 initial_vel,
                                      float gravity, float dt, int steps) {
    std::vector<Vec2> points;
    Vec2 pos = start;
    Vec2 vel = initial_vel;
    for (int i = 0; i < steps; ++i) {
        vel.y += gravity * dt;
        pos += vel * dt;
        points.push_back(pos);
        if (pos.y > 600) break;  // 落地停止
    }
    return points;
}

// 根据目标位置和飞行时间，计算所需初速度
Vec2 compute_launch_velocity(Vec2 start, Vec2 target, float gravity, float time) {
    float vx = (target.x - start.x) / time;
    float vy = (target.y - start.y - 0.5f * gravity * time * time) / time;
    return Vec2(vx, vy);
}

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Trajectory",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 800, 600, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, 0);

    const float GRAVITY = 980.0f;
    std::vector<Projectile> projectiles;
    Vec2 launch_pos(100, 500);
    Vec2 mouse_pos(400, 200);

    const double FIXED_DT = 1.0 / 60.0;
    double accumulator = 0.0;
    Uint64 last = SDL_GetPerformanceCounter();

    bool running = true;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) {
            if (e.type == SDL_QUIT) running = false;
            if (e.type == SDL_MOUSEMOTION) {
                mouse_pos.x = (float)e.motion.x;
                mouse_pos.y = (float)e.motion.y;
            }
            if (e.type == SDL_MOUSEBUTTONDOWN && e.button.button == SDL_BUTTON_LEFT) {
                // 鼠标位置即目标，飞行时间 1.0 秒
                Vec2 v = compute_launch_velocity(launch_pos, mouse_pos, GRAVITY, 1.0f);
                projectiles.push_back({ launch_pos, v, true });
            }
        }

        Uint64 now = SDL_GetPerformanceCounter();
        double frame_time = (double)(now - last) / SDL_GetPerformanceFrequency();
        last = now;
        if (frame_time > 0.25) frame_time = 0.25;
        accumulator += frame_time;

        while (accumulator >= FIXED_DT) {
            float dt = (float)FIXED_DT;
            for (auto& p : projectiles) {
                if (!p.active) continue;
                p.vel.y += GRAVITY * dt;
                p.pos += p.vel * dt;
                if (p.pos.y > 580) p.active = false;
            }
            accumulator -= FIXED_DT;
        }

        // 渲染
        SDL_SetRenderDrawColor(renderer, 20, 20, 30, 255);
        SDL_RenderClear(renderer);

        // 地面
        SDL_SetRenderDrawColor(renderer, 80, 80, 80, 255);
        SDL_Rect ground = { 0, 580, 800, 20 };
        SDL_RenderFillRect(renderer, &ground);

        // 预测轨迹（虚线）
        Vec2 predicted_v = compute_launch_velocity(launch_pos, mouse_pos, GRAVITY, 1.0f);
        auto traj = predict_trajectory(launch_pos, predicted_v, GRAVITY, 0.05f, 60);
        SDL_SetRenderDrawColor(renderer, 100, 100, 100, 255);
        for (size_t i = 0; i < traj.size(); i += 2) {  // 每隔一个点画，形成虚线
            SDL_Rect dot = { (int)traj[i].x - 2, (int)traj[i].y - 2, 4, 4 };
            SDL_RenderFillRect(renderer, &dot);
        }

        // 发射点
        SDL_SetRenderDrawColor(renderer, 0, 255, 0, 255);
        SDL_Rect launcher = { (int)launch_pos.x - 8, (int)launch_pos.y - 8, 16, 16 };
        SDL_RenderFillRect(renderer, &launcher);

        // 鼠标目标
        SDL_SetRenderDrawColor(renderer, 255, 0, 0, 255);
        SDL_Rect target = { (int)mouse_pos.x - 5, (int)mouse_pos.y - 5, 10, 10 };
        SDL_RenderFillRect(renderer, &target);

        // 已发射的抛体
        SDL_SetRenderDrawColor(renderer, 255, 200, 80, 255);
        for (auto& p : projectiles) {
            if (!p.active) continue;
            SDL_Rect r = { (int)p.pos.x - 4, (int)p.pos.y - 4, 8, 8 };
            SDL_RenderFillRect(renderer, &r);
        }

        SDL_RenderPresent(renderer);
    }

    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- 抛体运动 = 水平匀速 + 垂直匀加速，可用解析公式直接求任意时刻位置。
- 轨迹预测不需要真正模拟，只需代入不同 `t` 值得到一系列点。
- 给定目标位置和飞行时间，可反解出所需初速度，用于"自动瞄准"。
- 轨迹采样步长 0.05~0.1s 是性能与精度的平衡点。
- **应用场景**：愤怒的小鸟瞄准线、塔防游戏射程预览、台球游戏击球线预测。

---

# 第 3 章 · 碰撞检测基础

碰撞检测（Collision Detection）回答一个简单的问题：两个物体是否重叠？这是物理引擎中最频繁的计算——一个有 100 个物体的场景，每帧需要 100×99/2 = 4950 次检测。本章从最常用的 AABB（轴对齐包围盒）开始，逐步介绍圆形碰撞、点包含检测，以及多重碰撞的遍历策略。掌握这些，你就能处理 80% 的 2D 游戏碰撞需求。

## 第 09 讲 · AABB 包围盒与碰撞检测

### 概念

**AABB（Axis-Aligned Bounding Box，轴对齐包围盒）** 是一个边与坐标轴平行的矩形。它用两个点表示：最小角 `(x_min, y_min)` 和最大角 `(x_max, y_max)`，或等价地用左上角和宽高表示。

AABB 是游戏开发中最常用的碰撞体，原因有三：检测算法极简（4 次比较即可）、计算量极小（无三角函数、无开方）、对存储友好（4 个 float 即可）。任何不旋转的矩形物体（平台、墙壁、子弹、UI 元素）都适合用 AABB。

### 原理

两个 AABB 不重叠的条件是：在 X 轴或 Y 轴上存在分离。具体来说：

```
A.x_max < B.x_min  或  B.x_max < A.x_min  → X 轴分离
A.y_max < B.y_min  或  B.y_max < A.y_min  → Y 轴分离
```

只要任一轴分离，两盒就不重叠。**取反**即得重叠条件：

```
A.x_min < B.x_max  且  B.x_min < A.x_max
且
A.y_min < B.y_max  且  B.y_min < A.y_max
```

这就是 AABB 碰撞检测的核心——4 次比较 + 3 次逻辑与，比任何其他碰撞算法都快。

AABB 的局限是**不能旋转**：一旦物体旋转，其 AABB 会变大（包围旋转后的形状），导致误报。对于旋转物体，需用 OBB（第 18 讲）或多边形碰撞（第 19 讲）。

### 例子

```cpp
// AABB 表示：左上角 + 宽高
struct AABB {
    float x, y;  // 左上角
    float w, h;  // 宽高

    float left()   const { return x; }
    float right()  const { return x + w; }
    float top()    const { return y; }
    float bottom() const { return y + h; }
};

// AABB 碰撞检测
bool aabb_vs_aabb(const AABB& a, const AABB& b) {
    // 任一轴分离即不碰撞
    if (a.right()  <= b.left())   return false;
    if (a.left()   >= b.right())  return false;
    if (a.bottom() <= b.top())    return false;
    if (a.top()    >= b.bottom()) return false;
    return true;
}

// 实战示例：玩家与平台碰撞
struct Player {
    AABB box;
    Vec2 vel;
    bool on_ground;
};

struct Platform {
    AABB box;
};

void update_player(Player& p, const std::vector<Platform>& platforms, float dt) {
    const float GRAVITY = 1500.0f;
    p.vel.y += GRAVITY * dt;

    // 分轴移动：先 X 后 Y，便于判断碰撞方向
    p.box.x += p.vel.x * dt;
    for (const auto& plat : platforms) {
        if (aabb_vs_aabb(p.box, plat.box)) {
            if (p.vel.x > 0) p.box.x = plat.box.left() - p.box.w;
            else if (p.vel.x < 0) p.box.x = plat.box.right();
            p.vel.x = 0;
        }
    }

    p.box.y += p.vel.y * dt;
    p.on_ground = false;
    for (const auto& plat : platforms) {
        if (aabb_vs_aabb(p.box, plat.box)) {
            if (p.vel.y > 0) {
                p.box.y = plat.box.top() - p.box.h;
                p.on_ground = true;
            } else if (p.vel.y < 0) {
                p.box.y = plat.box.bottom();
            }
            p.vel.y = 0;
        }
    }
}
```

### 总结

- AABB 是边与坐标轴平行的矩形，4 个 float 即可表示，4 次比较即可检测。
- 检测原理：两盒在 X 轴和 Y 轴上都重叠时才碰撞，任一轴分离即不碰撞。
- **分轴移动**是处理 AABB 碰撞响应的黄金法则：先移动 X 解决水平碰撞，再移动 Y 解决垂直碰撞，便于判断碰撞方向。
- AABB 不能处理旋转物体，旋转后包围盒会变大导致误报。
- **性能优势**：AABB 检测比圆形快约 2 倍，比 OBB 快约 10 倍，是大规模场景的首选。

---

## 第 10 讲 · 圆形碰撞检测

### 概念

**圆形碰撞体** 用圆心 `(cx, cy)` 和半径 `r` 表示。它适合球类、子弹、粒子等"各方向等长"的物体。圆形碰撞的最大优势是**旋转不变**：无论物体如何旋转，圆的形状不变，检测算法完全相同。

### 原理

两个圆 `(C1, r1)` 和 `(C2, r2)` 碰撞的条件是：圆心距离小于半径之和。

```
|C1 - C2| < r1 + r2
```

为避免开方，两边平方：

```
|C1 - C2|² < (r1 + r2)²
```

展开后：

```
(C1.x - C2.x)² + (C1.y - C2.y)² < (r1 + r2)²
```

这就是圆形碰撞检测公式——3 次乘法 + 2 次加法 + 1 次比较，比 AABB 略慢但仍然极快。

圆形碰撞的另一个优势是**碰撞响应简单**：碰撞法向量就是两圆心连线方向，无需额外计算。这使得圆形特别适合做弹球游戏（第 15 讲会详细讲反射）。

### 例子

```cpp
struct Circle {
    Vec2 center;
    float radius;
};

// 圆形碰撞检测（使用平方距离避免开方）
bool circle_vs_circle(const Circle& a, const Circle& b) {
    Vec2 diff = a.center - b.center;
    float dist_sq = diff.length_sq();          // 平方距离
    float radius_sum = a.radius + b.radius;
    return dist_sq < radius_sum * radius_sum;  // 比较平方
}

// 带穿透深度与法向量的版本（用于碰撞响应）
struct CollisionInfo {
    bool collided;
    Vec2 normal;       // 从 a 指向 b 的法向量
    float penetration; // 穿透深度
};

CollisionInfo circle_vs_circle_info(const Circle& a, const Circle& b) {
    CollisionInfo info = { false, Vec2(0, 0), 0 };
    Vec2 diff = b.center - a.center;
    float dist_sq = diff.length_sq();
    float radius_sum = a.radius + b.radius;

    if (dist_sq < radius_sum * radius_sum) {
        float dist = std::sqrt(dist_sq);
        info.collided = true;
        if (dist > 1e-6f) {
            info.normal = diff / dist;          // 单位法向量
        } else {
            info.normal = Vec2(0, -1);          // 完全重合时默认向上
        }
        info.penetration = radius_sum - dist;
    }
    return info;
}

// 实战：弹球游戏中的小球碰撞
struct Ball {
    Vec2 pos;
    Vec2 vel;
    float radius;
};

void resolve_ball_collision(Ball& a, Ball& b) {
    Circle ca = { a.pos, a.radius };
    Circle cb = { b.pos, b.radius };
    auto info = circle_vs_circle_info(ca, cb);
    if (!info.collided) return;

    // 位置修正：把两球分开
    float total_mass = 1.0f + 1.0f;  // 假设质量相等
    a.pos -= info.normal * (info.penetration / 2.0f);
    b.pos += info.normal * (info.penetration / 2.0f);

    // 速度交换（等质量弹性碰撞简化版）
    Vec2 rel_vel = b.vel - a.vel;
    float vel_along_normal = rel_vel.dot(info.normal);
    if (vel_along_normal > 0) return;  // 已经在分离，无需处理

    float restitution = 1.0f;  // 完全弹性
    float j = -(1 + restitution) * vel_along_normal / total_mass;
    Vec2 impulse = info.normal * j;
    a.vel -= impulse;
    b.vel += impulse;
}
```

### 总结

- 圆形碰撞检测：圆心距离平方 < 半径和的平方，避免开方。
- 圆形**旋转不变**，适合球类、子弹等各向同性物体。
- 碰撞法向量就是两圆心连线方向，响应计算极简。
- **性能**：略慢于 AABB（3 次乘法 vs 4 次比较），但仍属 O(1) 级别。
- **适用场景**：弹球、台球、子弹、粒子、角色头部碰撞胶囊（圆+矩形组合）。

---

## 第 11 讲 · 点与形状包含检测

### 概念

**点包含检测（Point Containment）** 判断一个点是否在某个形状内部。这看似简单，却是许多高级功能的基础：鼠标点击检测（点是否在按钮内）、子弹命中判定（点是否在敌人碰撞体内）、AI 视野判断（点是否在视野扇形内）。

### 原理

不同形状的点包含算法不同：

**点 vs AABB**：最简单，4 次比较。

```
point.x >= box.left  且  point.x <= box.right
且
point.y >= box.top   且  point.y <= box.bottom
```

**点 vs 圆**：点到圆心距离小于半径。

```
(point - center).length_sq() < radius²
```

**点 vs 凸多边形**：两种主流算法。

1. **射线法（Ray Casting）**：从点向任意方向发射射线，统计与多边形边的交点数。奇数在内，偶数在外。适用于任意多边形（凸或凹）。
2. **叉积法（Cross Product）**：对凸多边形，检查点是否在所有边的同一侧（如都在逆时针方向的左侧）。只需遍历所有边做叉积，比射线法更稳定。

**点 vs 旋转矩形（OBB）**：把点变换到矩形的本地坐标系（即把矩形"摆正"），然后做 AABB 检测。这是 OBB 碰撞的核心思想，第 18 讲会详细讲。

### 例子

```cpp
// 点 vs AABB
bool point_in_aabb(Vec2 p, const AABB& box) {
    return p.x >= box.left() && p.x <= box.right()
        && p.y >= box.top()  && p.y <= box.bottom();
}

// 点 vs 圆
bool point_in_circle(Vec2 p, const Circle& c) {
    Vec2 diff = p - c.center;
    return diff.length_sq() < c.radius * c.radius;
}

// 点 vs 凸多边形（叉积法，假设顶点逆时针排列）
bool point_in_convex_polygon(Vec2 p, const std::vector<Vec2>& polygon) {
    int n = (int)polygon.size();
    if (n < 3) return false;
    for (int i = 0; i < n; ++i) {
        Vec2 a = polygon[i];
        Vec2 b = polygon[(i + 1) % n];
        Vec2 edge = b - a;
        Vec2 to_point = p - a;
        // 叉积为负表示点在边的右侧（逆时针多边形的内侧）
        if (edge.cross(to_point) < 0) return false;
    }
    return true;
}

// 点 vs 旋转矩形（OBB）
struct OBB {
    Vec2 center;
    Vec2 half_size;  // 半宽半高
    float rotation;  // 弧度
};

bool point_in_obb(Vec2 p, const OBB& box) {
    // 把点变换到矩形的本地坐标系
    Vec2 to_local = p - box.center;
    float cos_r = std::cos(-box.rotation);
    float sin_r = std::sin(-box.rotation);
    // 旋转 -box.rotation 角度（逆变换）
    Vec2 local(
        to_local.x * cos_r - to_local.y * sin_r,
        to_local.x * sin_r + to_local.y * cos_r
    );
    // 在本地坐标系中做 AABB 检测
    return std::abs(local.x) <= box.half_size.x
        && std::abs(local.y) <= box.half_size.y;
}

// 实战：鼠标点击检测
bool is_button_clicked(Vec2 mouse_pos, const AABB& button_box) {
    return point_in_aabb(mouse_pos, button_box);
}
```

### 总结

- 点包含检测是鼠标交互、命中判定的基础。
- **点 vs AABB**：4 次比较，最快。
- **点 vs 圆**：距离平方比较，避免开方。
- **点 vs 凸多边形**：叉积法（凸）或射线法（任意），前者更稳定。
- **点 vs OBB**：把点变换到本地坐标系再做 AABB 检测，这是处理旋转物体的通用思路。
- **应用场景**：UI 点击、子弹命中、AI 视野、区域触发器。

---

## 第 12 讲 · 多重碰撞遍历策略

### 概念

当场景中有 N 个物体时，两两碰撞检测需要 N×(N-1)/2 次。N=100 时是 4950 次，N=1000 时是 499500 次——平方复杂度让暴力检测在大场景中不可行。

**多重碰撞遍历策略**研究如何高效地组织检测顺序，包括：暴力遍历（适合小场景）、分轴排序扫描（Sweep and Prune，适合中等场景）、空间分区（适合大场景，第 23 讲详讲）。本讲聚焦前两种。

### 原理

**暴力遍历（Brute Force）**：

```
for i in 0..N:
    for j in i+1..N:
        check_collision(objects[i], objects[j])
```

简单直接，但 O(N²) 复杂度。N<100 时性能可接受，N>500 时帧率会明显下降。

**Sweep and Prune（扫描剪枝）**：

核心思想：如果两个物体的 X 轴投影不重叠，那它们一定不碰撞。先把所有物体按 X 轴最小值排序，然后扫描时只检查 X 轴投影重叠的物体对。

```
1. 对所有物体按 x_min 排序
2. for i in 0..N:
       for j in i+1..N:
           if objects[j].x_min > objects[i].x_max: break  // X 轴已分离，后续更远
           if y 轴也重叠: check_collision(i, j)
```

由于排序后物体在 X 轴上有序，内层循环通常很快 break，平均复杂度接近 O(N·k)，k 是每个物体的平均邻居数。对于分布均匀的场景，k 是常数级。

**增量排序优化**：物体每帧移动很小，排序顺序变化不大，可用**插入排序**（对几乎有序的数组是 O(N)）代替快速排序（O(N log N)）。

### 例子

```cpp
#include <algorithm>

struct PhysicsObject {
    AABB box;
    Vec2 vel;
    int id;  // 用于排序后追踪
};

// 暴力遍历
void brute_force_collisions(std::vector<PhysicsObject>& objects,
                             std::vector<std::pair<int,int>>& collisions) {
    collisions.clear();
    int n = (int)objects.size();
    for (int i = 0; i < n; ++i) {
        for (int j = i + 1; j < n; ++j) {
            if (aabb_vs_aabb(objects[i].box, objects[j].box)) {
                collisions.push_back({objects[i].id, objects[j].id});
            }
        }
    }
}

// Sweep and Prune
void sweep_and_prune(std::vector<PhysicsObject>& objects,
                      std::vector<std::pair<int,int>>& collisions) {
    collisions.clear();

    // 增量排序：插入排序（对几乎有序的数组高效）
    int n = (int)objects.size();
    for (int i = 1; i < n; ++i) {
        PhysicsObject key = objects[i];
        int j = i - 1;
        while (j >= 0 && objects[j].box.left() > key.box.left()) {
            objects[j + 1] = objects[j];
            --j;
        }
        objects[j + 1] = key;
    }

    // 扫描
    for (int i = 0; i < n; ++i) {
        for (int j = i + 1; j < n; ++j) {
            // X 轴已分离，后续物体更远，直接 break
            if (objects[j].box.left() > objects[i].box.right()) break;
            // X 轴重叠，检查 Y 轴
            if (aabb_vs_aabb(objects[i].box, objects[j].box)) {
                collisions.push_back({objects[i].id, objects[j].id});
            }
        }
    }
}

// 性能对比测试
void benchmark() {
    std::vector<PhysicsObject> objects;
    for (int i = 0; i < 500; ++i) {
        PhysicsObject obj;
        obj.box.x = (float)(rand() % 760);
        obj.box.y = (float)(rand() % 560);
        obj.box.w = 20;
        obj.box.h = 20;
        obj.id = i;
        objects.push_back(obj);
    }

    std::vector<std::pair<int,int>> collisions;
    // 暴力：约 124750 次检测
    brute_force_collisions(objects, collisions);
    // SAP：通常只检测几百到几千次
    sweep_and_prune(objects, collisions);
}
```

### 总结

- 暴力遍历 O(N²)，N<100 时可用；N>500 时必须优化。
- **Sweep and Prune** 利用 X 轴排序剪枝，平均复杂度接近 O(N·k)，k 是平均邻居数。
- 用**插入排序**代替快速排序，利用帧间连贯性（物体每帧移动很小），排序几乎 O(N)。
- SAP 适合物体分布相对均匀的场景；若物体在 X 轴上跨度很大但 Y 轴紧凑，可改用 Y 轴排序。
- **进阶**：对于超大场景（N>1000），需用空间分区（网格、四叉树），第 23 讲详讲。

---

# 第 4 章 · 碰撞响应

检测到碰撞只是第一步，更关键的是**如何响应**。如果只检测不响应，物体会互相穿透、卡在墙里、抖动不止。本章将系统讲解碰撞响应的核心机制：穿透深度与法向量的计算、位置修正（防穿透）、速度反射（反弹）、弹性系数与能量损失。掌握这些，你的物理系统才真正"有质感"。

## 第 13 讲 · 穿透深度与法向量

### 概念

**穿透深度（Penetration Depth）** 是两物体重叠区域的最小距离——把一个物体沿某个方向移动这个距离，就能让它刚好脱离另一个物体。

**碰撞法向量（Collision Normal）** 是指明"分离方向"的单位向量。它垂直于碰撞接触面，从物体 A 指向物体 B。这两个量是所有碰撞响应的基础：位置修正需要穿透深度，速度反射需要法向量。

### 原理

不同形状对的穿透深度与法向量计算方法不同：

**圆 vs 圆**：法向量是两圆心连线方向，穿透深度是 `r1 + r2 - 圆心距离`。这是最简单的情况。

**AABB vs AABB**：需要分别计算 X 轴和 Y 轴的重叠量，取较小者作为穿透方向。

```
overlap_x = min(a.right, b.right) - max(a.left, b.left)
overlap_y = min(a.bottom, b.bottom) - max(a.top, b.top)

if overlap_x < overlap_y:
    normal = (±1, 0)   // X 轴方向
    penetration = overlap_x
else:
    normal = (0, ±1)   // Y 轴方向
    penetration = overlap_y
```

法向量的正负号取决于两物体的相对位置：若 A 在 B 左边，X 轴法向量为 `(1, 0)`（从 A 指向 B）。

**OBB vs OBB / 多边形**：使用 SAT（分离轴定理，第 19 讲）求最小重叠轴，那个轴就是法向量。

### 例子

```cpp
struct CollisionManifold {
    bool collided;
    Vec2 normal;        // 从 A 指向 B 的单位法向量
    float penetration;  // 穿透深度
};

// AABB vs AABB 的完整碰撞信息
CollisionManifold aabb_vs_aabb_manifold(const AABB& a, const AABB& b) {
    CollisionManifold m = { false, Vec2(0, 0), 0 };

    // 计算两盒中心
    Vec2 center_a(a.x + a.w / 2, a.y + a.h / 2);
    Vec2 center_b(b.x + b.w / 2, b.y + b.h / 2);

    // 计算两中心间距
    Vec2 diff = center_b - center_a;

    // X 轴重叠
    float overlap_x = (a.w + b.w) / 2 - std::abs(diff.x);
    if (overlap_x <= 0) return m;  // X 轴分离

    // Y 轴重叠
    float overlap_y = (a.h + b.h) / 2 - std::abs(diff.y);
    if (overlap_y <= 0) return m;  // Y 轴分离

    // 取较小重叠轴作为分离方向
    if (overlap_x < overlap_y) {
        m.collided = true;
        m.penetration = overlap_x;
        m.normal = Vec2(diff.x < 0 ? -1.0f : 1.0f, 0);
    } else {
        m.collided = true;
        m.penetration = overlap_y;
        m.normal = Vec2(0, diff.y < 0 ? -1.0f : 1.0f);
    }
    return m;
}

// 圆 vs 圆 的完整碰撞信息
CollisionManifold circle_vs_circle_manifold(const Circle& a, const Circle& b) {
    CollisionManifold m = { false, Vec2(0, 0), 0 };
    Vec2 diff = b.center - a.center;
    float dist_sq = diff.length_sq();
    float radius_sum = a.radius + b.radius;

    if (dist_sq >= radius_sum * radius_sum) return m;

    float dist = std::sqrt(dist_sq);
    m.collided = true;
    m.penetration = radius_sum - dist;
    m.normal = dist > 1e-6f ? diff / dist : Vec2(0, -1);
    return m;
}

// 调试可视化：绘制法向量
void draw_manifold(SDL_Renderer* r, const AABB& a, const AABB& b,
                   const CollisionManifold& m) {
    if (!m.collided) return;
    Vec2 contact(a.x + a.w / 2, a.y + a.h / 2);  // 简化：用 A 的中心作为接触点
    Vec2 end = contact + m.normal * (m.penetration + 20);
    SDL_SetRenderDrawColor(r, 255, 255, 0, 255);  // 黄色法向量
    SDL_RenderDrawLine(r, (int)contact.x, (int)contact.y, (int)end.x, (int)end.y);
}
```

### 总结

- 穿透深度是分离两物体所需的最小位移，法向量是分离方向。
- **圆 vs 圆**：法向量 = 圆心连线方向，穿透深度 = 半径和 - 圆心距。
- **AABB vs AABB**：取 X/Y 轴重叠较小者作为分离方向。
- 法向量始终从 A 指向 B，方向选择影响后续响应的正负号。
- **调试技巧**：在屏幕上绘制法向量（黄色线段）和穿透深度（红色矩形），能直观看到碰撞响应是否正确。

---

## 第 14 讲 · 位置修正（防穿透）

### 概念

**位置修正（Positional Correction）** 是在检测到穿透后，把物体沿法向量推开，使其刚好脱离接触。这是消除"卡墙"、"抖动"现象的关键步骤。

如果不做位置修正，只做速度反射，物体会因为浮点误差持续穿透墙壁，每帧都被弹回又穿透，产生肉眼可见的抖动。

### 原理

位置修正的核心是**按质量分配位移**。两物体碰撞后，重的物体应该少移动，轻的物体多移动，这才符合物理直觉（撞墙时墙不动，你被弹开）。

设两物体质量为 `m1` 和 `m2`，总质量 `M = m1 + m2`，则：

```
位移1 = -normal × penetration × (m2 / M)
位移2 =  normal × penetration × (m1 / M)
```

注意位移1 是反方向（A 远离 B），位移2 是正方向（B 远离 A）。当 m1 远大于 m2（如玩家撞墙），位移1 ≈ 0，位移2 ≈ 全部位移——墙不动，玩家被推开。

**质量倒数**优化：物理引擎通常存储质量的倒数 `inv_mass = 1/mass`，避免除法。修正公式变为：

```
total_inv = inv_mass1 + inv_mass2
位移1 = -normal × penetration × (inv_mass1 / total_inv)
位移2 =  normal × penetration × (inv_mass2 / total_inv)
```

**静态物体**（墙壁、地面）质量设为无穷大，`inv_mass = 0`，所有位移都由动态物体承担。

### 例子

```cpp
struct RigidBody {
    Vec2 pos;
    Vec2 vel;
    float mass;
    float inv_mass;  // 质量倒数，静态物体为 0

    void set_mass(float m) {
        mass = m;
        inv_mass = m > 0 ? 1.0f / m : 0.0f;  // m=0 表示静态
    }
};

// 位置修正：按质量分配位移
void positional_correction(RigidBody& a, RigidBody& b,
                            const CollisionManifold& m) {
    if (!m.collided) return;

    const float percent = 0.8f;     // 修正比例（<1 避免过冲）
    const float slop = 0.01f;       // 容差（避免微小穿透导致抖动）

    float total_inv = a.inv_mass + b.inv_mass;
    if (total_inv <= 0) return;     // 两物体都静态，无需修正

    Vec2 correction = m.normal * (std::max(m.penetration - slop, 0.0f)
                                   / total_inv * percent);
    a.pos -= correction * a.inv_mass;
    b.pos += correction * b.inv_mass;
}

// 完整的碰撞响应流程
void resolve_collision(RigidBody& a, RigidBody& b,
                       const CollisionManifold& m) {
    if (!m.collided) return;

    // 1. 位置修正（防穿透）
    positional_correction(a, b, m);

    // 2. 速度响应（下一讲详讲）
    Vec2 rel_vel = b.vel - a.vel;
    float vel_along_normal = rel_vel.dot(m.normal);
    if (vel_along_normal > 0) return;  // 已在分离

    float restitution = 0.3f;  // 弹性系数
    float j = -(1 + restitution) * vel_along_normal / (a.inv_mass + b.inv_mass);
    Vec2 impulse = m.normal * j;
    a.vel -= impulse * a.inv_mass;
    b.vel += impulse * b.inv_mass;
}

// 实战：玩家与平台
void update_player_with_platforms(RigidBody& player,
                                   const std::vector<RigidBody>& platforms,
                                   float dt) {
    player.vel.y += 1500.0f * dt;  // 重力
    player.pos += player.vel * dt;

    for (const auto& plat : platforms) {
        AABB a = { player.pos.x, player.pos.y, 30, 30 };
        AABB b = { plat.pos.x, plat.pos.y, 100, 20 };
        auto m = aabb_vs_aabb_manifold(a, b);
        if (m.collided) {
            RigidBody plat_copy = plat;  // 平台是静态的，inv_mass=0
            resolve_collision(player, plat_copy, m);
        }
    }
}
```

### 总结

- 位置修正是消除穿透与抖动的关键，按质量分配位移让重的物体少动。
- 用**质量倒数** `inv_mass` 代替质量，避免除法；静态物体 `inv_mass = 0`。
- 引入**容差 slop**（如 0.01）避免微小穿透导致反复修正抖动。
- 修正比例 `percent`（如 0.8）避免一次修正过冲，剩余穿透在下一帧继续修正。
- **常见坑**：只做位置修正不做速度响应，物体会"滑进墙里再被推出"；两者必须配合。

---

## 第 15 讲 · 速度反射与反弹

### 概念

**速度反射（Velocity Reflection）** 是碰撞后改变物体速度方向的过程。最简单的形式是"镜面反射"：入射角等于反射角，就像光线在镜面上的反射。这是弹球游戏、台球游戏的核心物理。

### 原理

反射公式基于法向量：

```
v' = v - 2(v · n)n
```

其中 `v` 是入射速度，`n` 是单位法向量，`v'` 是反射速度。几何意义：把速度沿法向量方向的分量取反，切向分量保持不变。

**推导**：把 `v` 分解为法向分量 `v_n = (v·n)n` 和切向分量 `v_t = v - v_n`。反射后法向分量取反，切向分量不变：

```
v' = v_t - v_n = (v - v_n) - v_n = v - 2v_n = v - 2(v·n)n
```

**带弹性系数的反射**：

```
v' = v - (1 + e)(v · n)n
```

其中 `e` 是弹性系数（restitution）：e=1 完全弹性（无能量损失），e=0 完全非弹性（法向速度归零），0<e<1 部分能量损失。

**两物体碰撞**：需要考虑相对速度与质量。冲量公式：

```
j = -(1 + e) · (v_rel · n) / (1/m1 + 1/m2)
v1' = v1 - (j/m1) · n
v2' = v2 + (j/m2) · n
```

### 例子

```cpp
// 简单反射：物体撞墙（墙质量无穷大）
Vec2 reflect_velocity(Vec2 v, Vec2 normal, float restitution = 1.0f) {
    float dot = v.dot(normal);
    return v - normal * ((1.0f + restitution) * dot);
}

// 实战：弹球游戏
struct Ball {
    Vec2 pos;
    Vec2 vel;
    float radius;
};

void update_ball(Ball& ball, float dt) {
    const float GRAVITY = 500.0f;
    ball.vel.y += GRAVITY * dt;
    ball.pos += ball.vel * dt;

    // 与四面墙碰撞
    const int W = 800, H = 600;
    const float RESTITUTION = 0.9f;  // 略有能量损失

    // 左墙
    if (ball.pos.x - ball.radius < 0) {
        ball.pos.x = ball.radius;
        ball.vel = reflect_velocity(ball.vel, Vec2(1, 0), RESTITUTION);
    }
    // 右墙
    if (ball.pos.x + ball.radius > W) {
        ball.pos.x = W - ball.radius;
        ball.vel = reflect_velocity(ball.vel, Vec2(-1, 0), RESTITUTION);
    }
    // 上墙
    if (ball.pos.y - ball.radius < 0) {
        ball.pos.y = ball.radius;
        ball.vel = reflect_velocity(ball.vel, Vec2(0, 1), RESTITUTION);
    }
    // 下墙（地面）
    if (ball.pos.y + ball.radius > H) {
        ball.pos.y = H - ball.radius;
        ball.vel = reflect_velocity(ball.vel, Vec2(0, -1), RESTITUTION);
    }
}

// 两球碰撞的冲量响应
void resolve_ball_collision_impulse(Ball& a, Ball& b, float restitution = 0.95f) {
    Vec2 diff = b.pos - a.pos;
    float dist = diff.length();
    float radius_sum = a.radius + b.radius;
    if (dist >= radius_sum) return;

    Vec2 normal = dist > 1e-6f ? diff / dist : Vec2(0, -1);
    float penetration = radius_sum - dist;

    // 位置修正（等质量）
    a.pos -= normal * (penetration / 2);
    b.pos += normal * (penetration / 2);

    // 速度响应
    Vec2 rel_vel = b.vel - a.vel;
    float vel_along_normal = rel_vel.dot(normal);
    if (vel_along_normal > 0) return;  // 已在分离

    // 假设两球质量相等（1.0）
    float j = -(1 + restitution) * vel_along_normal / 2.0f;
    Vec2 impulse = normal * j;
    a.vel -= impulse;
    b.vel += impulse;
}

// 实战：台球游戏
// 球与球杆的碰撞、球与球之间的碰撞都用上述函数
// 球与球桌边缘的碰撞用 reflect_velocity
```

### 总结

- 反射公式 `v' = v - 2(v·n)n` 是镜面反射的核心，入射角等于反射角。
- 带弹性系数的反射 `v' = v - (1+e)(v·n)n`，e 控制能量损失。
- 两物体碰撞用冲量公式，考虑相对速度与质量分配。
- **常见坑**：反射前必须检查 `v·n < 0`（物体正在接近），否则已分离的物体会被错误反弹。
- **应用场景**：弹球、台球、Breakout、Pong、所有"球反弹"类游戏。

---

## 第 16 讲 · 弹性系数与能量损失

### 概念

**弹性系数（Coefficient of Restitution, e）** 是衡量碰撞中能量保留程度的物理量，取值范围 [0, 1]：

- e = 1：完全弹性碰撞，无能量损失（理想情况）。
- e = 0：完全非弹性碰撞，物体粘在一起（如湿泥巴）。
- 0 < e < 1：部分能量损失，现实世界大多数碰撞属于此类。

不同材质的典型 e 值：橡皮球 0.85、钢球 0.6、木球 0.4、湿泥巴 0.1。

### 原理

弹性系数的物理定义是碰撞前后相对速度的比值：

```
e = -v_rel_after / v_rel_before
```

其中 `v_rel` 是两物体沿法向量的相对速度。e=1 表示相对速度大小不变（仅方向反转），e=0 表示相对速度归零（物体一起运动）。

**能量损失**：碰撞前动能 `KE_before = 0.5·m1·v1² + 0.5·m2·v2²`，碰撞后同理。损失的能量转化为热、声、形变。在游戏中，能量损失让物体最终停下来，避免永远弹跳。

**混合弹性系数**：当两物体材质不同时，常用几何平均：

```
e_mix = sqrt(e1 · e2)
```

或取较小者 `e_mix = min(e1, e2)`，模拟"软材质主导"。

**切向摩擦**：弹性系数只影响法向速度。切向速度的损失由摩擦系数控制，通常单独处理：

```
v_tangent_after = v_tangent_before × (1 - friction)
```

### 例子

```cpp
struct Material {
    float restitution;  // 弹性系数
    float friction;     // 摩擦系数
};

// 材质库
const Material RUBBER  = { 0.85f, 0.3f };
const Material STEEL   = { 0.60f, 0.1f };
const Material WOOD    = { 0.40f, 0.5f };
const Material GROUND  = { 0.30f, 0.7f };
const Material WALL    = { 0.20f, 0.6f };

// 混合两材质的弹性系数
float mix_restitution(float e1, float e2) {
    return std::sqrt(e1 * e2);  // 几何平均
}

// 混合两材质的摩擦系数
float mix_friction(float f1, float f2) {
    return std::sqrt(f1 * f2);
}

// 带材质的碰撞响应
void resolve_with_materials(RigidBody& a, RigidBody& b,
                             const CollisionManifold& m,
                             const Material& mat_a, const Material& mat_b) {
    if (!m.collided) return;

    // 位置修正
    positional_correction(a, b, m);

    // 计算混合材质参数
    float e = mix_restitution(mat_a.restitution, mat_b.restitution);
    float f = mix_friction(mat_a.friction, mat_b.friction);

    // 法向冲量
    Vec2 rel_vel = b.vel - a.vel;
    float vel_along_normal = rel_vel.dot(m.normal);
    if (vel_along_normal > 0) return;

    float j_normal = -(1 + e) * vel_along_normal / (a.inv_mass + b.inv_mass);
    Vec2 impulse_normal = m.normal * j_normal;
    a.vel -= impulse_normal * a.inv_mass;
    b.vel += impulse_normal * b.inv_mass;

    // 切向冲量（摩擦）
    Vec2 rel_vel_after = b.vel - a.vel;
    Vec2 tangent = rel_vel_after - m.normal * rel_vel_after.dot(m.normal);
    if (tangent.length_sq() > 1e-6f) {
        tangent = tangent.normalized();
        float vel_along_tangent = rel_vel_after.dot(tangent);
        float j_tangent = -vel_along_tangent / (a.inv_mass + b.inv_mass);
        // 库仑摩擦：切向冲量不超过摩擦系数 × 法向冲量
        j_tangent = std::clamp(j_tangent, -j_normal * f, j_normal * f);
        Vec2 impulse_tangent = tangent * j_tangent;
        a.vel -= impulse_tangent * a.inv_mass;
        b.vel += impulse_tangent * b.inv_mass;
    }
}

// 实战：不同材质球的弹跳对比
void demo_materials() {
    RigidBody rubber_ball, steel_ball;
    rubber_ball.set_mass(1.0f);
    steel_ball.set_mass(1.0f);
    rubber_ball.pos = Vec2(200, 100);
    steel_ball.pos = Vec2(400, 100);
    rubber_ball.vel = Vec2(0, 0);
    steel_ball.vel = Vec2(0, 0);

    RigidBody ground;
    ground.set_mass(0);  // 静态
    ground.pos = Vec2(0, 500);

    // 橡胶球弹得高，钢球弹得低
    // 多次弹跳后，橡胶球仍能弹起，钢球很快停止
}
```

### 总结

- 弹性系数 e ∈ [0, 1]，控制碰撞中能量保留程度。
- 物理定义：e = -v_rel_after / v_rel_before，相对速度的比值。
- 不同材质有不同 e 值，混合时取几何平均。
- 切向速度损失由摩擦系数控制，遵循库仑摩擦定律（切向冲量 ≤ 摩擦系数 × 法向冲量）。
- **游戏调参建议**：玩家角色 e=0（不弹跳，落地即停），弹球 e=0.9（高弹性），子弹 e=0.2（撞击后基本停止）。

---

# 第 5 章 · 高级碰撞

现实中的物体形状千变万化——旋转的飞船、不规则的多边形敌人、激光射线。本章将突破 AABB 与圆形的局限，学习圆与矩形碰撞、旋转矩形（OBB）碰撞、分离轴定理（SAT）以及射线投射。这些技术让你能处理任意形状的物体，是构建复杂物理引擎的必经之路。

## 第 17 讲 · 圆与矩形碰撞

### 概念

**圆与矩形碰撞** 是游戏开发中极常见的需求：圆形子弹击中矩形敌人、球形角色站在矩形平台上。检测算法需要找到矩形上离圆心最近的点，再判断该点是否在圆内。

### 原理

算法分两步：

1. **找最近点**：把圆心坐标钳制（clamp）到矩形范围内，得到矩形上离圆心最近的点。
2. **距离判断**：计算圆心到最近点的距离，若小于半径则碰撞。

```
closest.x = clamp(circle.x, rect.left, rect.right)
closest.y = clamp(circle.y, rect.top, rect.bottom)
dist_sq = (circle.x - closest.x)² + (circle.y - closest.y)²
collided = dist_sq < radius²
```

**为什么用钳制？** 矩形上离圆心最近的点有三种情况：

- 圆心在矩形正上方/下方/左方/右方时，最近点是圆心在矩形边上的投影。
- 圆心在矩形对角线方向时，最近点是矩形最近的角点。
- 圆心在矩形内部时，最近点就是圆心本身（此时距离为 0，必然碰撞）。

钳制操作 `clamp(value, min, max)` 把 value 限制在 [min, max] 范围内，正好覆盖上述三种情况。

**法向量计算**：碰撞后，法向量是从最近点指向圆心的方向。若圆心在矩形内部（最近点 = 圆心），需要特殊处理——通常选择圆心到最近矩形边的最短方向作为法向量。

### 例子

```cpp
struct CollisionManifoldCircleRect {
    bool collided;
    Vec2 normal;        // 从矩形指向圆
    float penetration;
};

CollisionManifoldCircleRect circle_vs_rect(const Circle& c, const AABB& r) {
    CollisionManifoldCircleRect m = { false, Vec2(0, 0), 0 };

    // 1. 找矩形上离圆心最近的点
    Vec2 closest;
    closest.x = std::clamp(c.center.x, r.left(), r.right());
    closest.y = std::clamp(c.center.y, r.top(), r.bottom());

    // 2. 计算距离
    Vec2 diff = c.center - closest;
    float dist_sq = diff.length_sq();

    if (dist_sq < c.radius * c.radius) {
        m.collided = true;
        float dist = std::sqrt(dist_sq);
        if (dist > 1e-6f) {
            m.normal = diff / dist;
            m.penetration = c.radius - dist;
        } else {
            // 圆心在矩形内部：找最近的边
            float dist_left   = c.center.x - r.left();
            float dist_right  = r.right() - c.center.x;
            float dist_top    = c.center.y - r.top();
            float dist_bottom = r.bottom() - c.center.y;
            float min_dist = std::min({dist_left, dist_right, dist_top, dist_bottom});

            if (min_dist == dist_left)        m.normal = Vec2(-1, 0);
            else if (min_dist == dist_right)  m.normal = Vec2(1, 0);
            else if (min_dist == dist_top)    m.normal = Vec2(0, -1);
            else                              m.normal = Vec2(0, 1);
            m.penetration = c.radius + min_dist;
        }
    }
    return m;
}

// 实战：圆形子弹与矩形敌人
struct Bullet {
    Vec2 pos;
    Vec2 vel;
    float radius;
    bool active;
};

struct Enemy {
    AABB box;
    int hp;
};

void update_bullets_vs_enemies(std::vector<Bullet>& bullets,
                                std::vector<Enemy>& enemies) {
    for (auto& bullet : bullets) {
        if (!bullet.active) continue;
        Circle bullet_circle = { bullet.pos, bullet.radius };

        for (auto& enemy : enemies) {
            auto m = circle_vs_rect(bullet_circle, enemy.box);
            if (m.collided) {
                enemy.hp -= 10;
                bullet.active = false;

                // 可选：子弹反弹效果
                // bullet.vel = reflect_velocity(bullet.vel, m.normal, 0.5f);
                break;
            }
        }
    }
}

// 实战：球形角色与矩形平台
void update_player_circle_vs_platforms(Ball& player,
                                        const std::vector<AABB>& platforms,
                                        float dt) {
    player.vel.y += 1500.0f * dt;
    player.pos += player.vel * dt;

    for (const auto& plat : platforms) {
        Circle c = { player.pos, player.radius };
        auto m = circle_vs_rect(c, plat);
        if (m.collided) {
            // 位置修正
            player.pos += m.normal * m.penetration;
            // 速度反射（仅法向分量）
            float vel_along_normal = player.vel.dot(m.normal);
            if (vel_along_normal < 0) {
                player.vel -= m.normal * (1.0f + 0.0f) * vel_along_normal;
            }
        }
    }
}
```

### 总结

- 圆与矩形碰撞：钳制圆心到矩形范围得最近点，判断距离是否小于半径。
- 钳制操作统一处理"圆心在矩形外/边上/内部"三种情况。
- 圆心在矩形内部时，需特殊处理法向量（选最近边方向）。
- **性能**：比 AABB 慢约 2 倍（多一次开方），但仍属 O(1)。
- **应用场景**：子弹与敌人、球形角色与平台、碰撞触发器。

---

## 第 18 讲 · 旋转矩形（OBB）碰撞

### 概念

**OBB（Oriented Bounding Box，有向包围盒）** 是可以旋转的矩形。它用中心点、半宽半高、旋转角度表示。OBB 适合飞船、旋转的剑、倾斜的平台等需要旋转的物体。

OBB 比 AABB 复杂得多：检测算法需要把一个 OBB 变换到另一个的本地坐标系，再做 AABB 检测。但它的精度远高于 AABB——旋转物体用 AABB 会产生大量误报。

### 原理

OBB vs OBB 的主流算法是 **SAT（分离轴定理）**，下一讲详讲。本讲先讲 OBB vs 点、OBB vs 圆、OBB vs AABB，这些用**坐标变换**即可解决。

**核心思想**：把查询物体（点/圆/AABB）变换到 OBB 的本地坐标系，在本地坐标系中 OBB 就是一个 AABB，问题简化为 AABB 检测。

**变换步骤**：

1. 平移：把查询物体的坐标减去 OBB 中心。
2. 旋转：把结果旋转 `-OBB.rotation` 角度（逆变换）。
3. 在本地坐标系中做 AABB 检测。

**OBB vs 圆**：圆变换到本地坐标系后仍是圆（圆旋转不变），但圆心位置变了。然后做"圆 vs AABB"检测（第 17 讲）。

**OBB vs AABB**：把 AABB 变换到 OBB 本地坐标系后，它变成了一个 OBB（旋转了 -rotation 角度）。此时需要 SAT 或再次变换。

### 例子

```cpp
struct OBB {
    Vec2 center;
    Vec2 half_size;  // 半宽半高
    float rotation;  // 弧度

    // 获取 OBB 的四个角点（世界坐标）
    std::vector<Vec2> get_corners() const {
        float cos_r = std::cos(rotation);
        float sin_r = std::sin(rotation);
        std::vector<Vec2> corners(4);
        Vec2 ext[4] = {
            { -half_size.x, -half_size.y },
            {  half_size.x, -half_size.y },
            {  half_size.x,  half_size.y },
            { -half_size.x,  half_size.y }
        };
        for (int i = 0; i < 4; ++i) {
            corners[i] = Vec2(
                center.x + ext[i].x * cos_r - ext[i].y * sin_r,
                center.y + ext[i].x * sin_r + ext[i].y * cos_r
            );
        }
        return corners;
    }
};

// 把世界坐标点变换到 OBB 本地坐标系
Vec2 world_to_local(const Vec2& p, const OBB& box) {
    Vec2 to_local = p - box.center;
    float cos_r = std::cos(-box.rotation);
    float sin_r = std::sin(-box.rotation);
    return Vec2(
        to_local.x * cos_r - to_local.y * sin_r,
        to_local.x * sin_r + to_local.y * cos_r
    );
}

// OBB vs 点
bool point_in_obb(Vec2 p, const OBB& box) {
    Vec2 local = world_to_local(p, box);
    return std::abs(local.x) <= box.half_size.x
        && std::abs(local.y) <= box.half_size.y;
}

// OBB vs 圆
struct OBBvsCircleManifold {
    bool collided;
    Vec2 normal;  // 世界坐标系，从 OBB 指向圆
    float penetration;
};

OBBvsCircleManifold obb_vs_circle(const OBB& box, const Circle& c) {
    OBBvsCircleManifold m = { false, Vec2(0, 0), 0 };

    // 把圆心变换到 OBB 本地坐标系
    Vec2 local_center = world_to_local(c.center, box);

    // 在本地坐标系中做"圆 vs AABB"检测
    Vec2 closest;
    closest.x = std::clamp(local_center.x, -box.half_size.x, box.half_size.x);
    closest.y = std::clamp(local_center.y, -box.half_size.y, box.half_size.y);

    Vec2 diff = local_center - closest;
    float dist_sq = diff.length_sq();

    if (dist_sq < c.radius * c.radius) {
        m.collided = true;
        float dist = std::sqrt(dist_sq);
        Vec2 local_normal;
        if (dist > 1e-6f) {
            local_normal = diff / dist;
            m.penetration = c.radius - dist;
        } else {
            // 圆心在 OBB 内部
            float dx = box.half_size.x - std::abs(local_center.x);
            float dy = box.half_size.y - std::abs(local_center.y);
            if (dx < dy) {
                local_normal = Vec2(local_center.x > 0 ? 1 : -1, 0);
                m.penetration = c.radius + dx;
            } else {
                local_normal = Vec2(0, local_center.y > 0 ? 1 : -1);
                m.penetration = c.radius + dy;
            }
        }
        // 把法向量变换回世界坐标系
        float cos_r = std::cos(box.rotation);
        float sin_r = std::sin(box.rotation);
        m.normal = Vec2(
            local_normal.x * cos_r - local_normal.y * sin_r,
            local_normal.x * sin_r + local_normal.y * cos_r
        );
    }
    return m;
}

// 实战：旋转飞船与小行星碰撞
struct Ship {
    OBB box;
    Vec2 vel;
    float angular_vel;
};

void update_ship(Ship& ship, float dt) {
    ship.box.center += ship.vel * dt;
    ship.box.rotation += ship.angular_vel * dt;
}

bool check_ship_collision(const Ship& ship, const Circle& asteroid) {
    auto m = obb_vs_circle(ship.box, asteroid);
    return m.collided;
}
```

### 总结

- OBB 是可旋转矩形，用中心+半宽半高+旋转角度表示。
- 核心技巧：把查询物体变换到 OBB 本地坐标系，简化为 AABB 检测。
- **OBB vs 圆**：圆变换后仍是圆，做"圆 vs AABB"检测，法向量需变换回世界坐标。
- **OBB vs OBB**：需要 SAT（下一讲），坐标变换不够。
- **性能**：比 AABB 慢约 10 倍（多 4 次三角函数），仅对真正旋转的物体使用。

---

## 第 19 讲 · 分离轴定理 SAT

### 概念

**分离轴定理（Separating Axis Theorem, SAT）** 是凸多边形碰撞检测的通用算法。定理内容：**两个凸多边形不重叠，当且仅当存在一条轴（称为分离轴），使得两多边形在该轴上的投影不重叠**。

SAT 是 2D 凸多边形碰撞的金标准，能处理任意凸形状：矩形、三角形、五边形、OBB vs OBB、多边形 vs 多边形。它是 Box2D、Chipmunk 等物理引擎的核心算法。

### 原理

SAT 算法步骤：

1. **候选轴**：对两个凸多边形，取所有边的法向量作为候选分离轴。两个三角形有 6 条边，所以 6 个候选轴；两个矩形有 8 条边，8 个候选轴。
2. **投影**：对每个候选轴，把两个多边形的所有顶点投影到该轴上，得到两个区间 [min1, max1] 和 [min2, max2]。
3. **检查重叠**：若任一轴上两区间不重叠，则找到分离轴，两多边形不碰撞。
4. **全部重叠**：若所有候选轴上投影都重叠，则两多边形碰撞。**最小重叠轴**就是碰撞法向量，**最小重叠量**就是穿透深度。

**为什么只检查边的法向量？** 直觉上，如果两多边形能分离，那么"卡住"它们的边一定是某条边——分离轴垂直于这条边。数学证明：对凸多边形，分离轴必定平行于某条边的法向量。

**优化**：对矩形 vs 矩形，由于对边平行，只有 4 个独立法向量（2 个来自 A，2 个来自 B），只需检查 4 个轴而非 8 个。

### 例子

```cpp
// 把多边形投影到轴上，返回 [min, max]
Vec2 project_polygon(const std::vector<Vec2>& polygon, const Vec2& axis) {
    float min = polygon[0].dot(axis);
    float max = min;
    for (size_t i = 1; i < polygon.size(); ++i) {
        float proj = polygon[i].dot(axis);
        if (proj < min) min = proj;
        if (proj > max) max = proj;
    }
    return Vec2(min, max);
}

// SAT 碰撞检测
struct SATManifold {
    bool collided;
    Vec2 normal;
    float penetration;
};

SATManifold sat_collision(const std::vector<Vec2>& a,
                           const std::vector<Vec2>& b) {
    SATManifold result = { false, Vec2(0, 0), FLT_MAX };

    // 检查多边形 A 的所有边法向量
    for (size_t i = 0; i < a.size(); ++i) {
        Vec2 edge = a[(i + 1) % a.size()] - a[i];
        Vec2 axis(-edge.y, edge.x);  // 法向量
        axis = axis.normalized();

        Vec2 proj_a = project_polygon(a, axis);
        Vec2 proj_b = project_polygon(b, axis);

        // 检查投影是否重叠
        float overlap = std::min(proj_a.y, proj_b.y) - std::max(proj_a.x, proj_b.x);
        if (overlap <= 0) {
            // 找到分离轴，不碰撞
            return { false, Vec2(0, 0), 0 };
        }
        if (overlap < result.penetration) {
            result.penetration = overlap;
            result.normal = axis;
        }
    }

    // 检查多边形 B 的所有边法向量
    for (size_t i = 0; i < b.size(); ++i) {
        Vec2 edge = b[(i + 1) % b.size()] - b[i];
        Vec2 axis(-edge.y, edge.x);
        axis = axis.normalized();

        Vec2 proj_a = project_polygon(a, axis);
        Vec2 proj_b = project_polygon(b, axis);

        float overlap = std::min(proj_a.y, proj_b.y) - std::max(proj_a.x, proj_b.x);
        if (overlap <= 0) {
            return { false, Vec2(0, 0), 0 };
        }
        if (overlap < result.penetration) {
            result.penetration = overlap;
            result.normal = axis;
        }
    }

    // 所有轴都重叠，碰撞
    result.collided = true;

    // 确保法向量从 A 指向 B
    Vec2 center_a = Vec2(0, 0), center_b = Vec2(0, 0);
    for (const auto& p : a) center_a += p;
    for (const auto& p : b) center_b += p;
    center_a = center_a / (float)a.size();
    center_b = center_b / (float)b.size();
    Vec2 a_to_b = center_b - center_a;
    if (a_to_b.dot(result.normal) < 0) {
        result.normal = -result.normal;
    }

    return result;
}

// 实战：OBB vs OBB（用 SAT）
SATManifold obb_vs_obb(const OBB& a, const OBB& b) {
    auto corners_a = a.get_corners();
    auto corners_b = b.get_corners();
    return sat_collision(corners_a, corners_b);
}

// 实战：任意凸多边形敌人碰撞
struct PolygonEnemy {
    std::vector<Vec2> vertices;  // 世界坐标顶点
    int hp;
};

bool enemies_collide(const PolygonEnemy& a, const PolygonEnemy& b) {
    auto m = sat_collision(a.vertices, b.vertices);
    return m.collided;
}
```

### 总结

- SAT 定理：两凸多边形不重叠 ⟺ 存在分离轴使投影不重叠。
- 候选轴 = 两多边形所有边的法向量。
- 最小重叠轴 = 碰撞法向量，最小重叠量 = 穿透深度。
- **适用范围**：仅限凸多边形；凹多边形需先分解为凸多边形（凸分解）。
- **性能**：O(N+M) per axis × (N+M) axes = O((N+M)²)，对小型多边形（<10 顶点）足够快。
- **优化**：对矩形 vs 矩形，由于对边平行，只需检查 4 个轴而非 8 个。

---

## 第 20 讲 · 射线投射与相交检测

### 概念

**射线（Ray）** 是从一个点出发、沿某个方向无限延伸的直线。**射线投射（Ray Casting）** 检测射线与场景中物体的交点，返回最近的命中点。

射线投射是 3D 渲染（光线追踪）的基础，在 2D 游戏中也有广泛应用：鼠标点击拾取、子弹射线检测、AI 视线判断、激光武器、视野遮蔽。

### 原理

射线参数化表示：`P(t) = O + D·t`，其中 O 是起点，D 是方向（单位向量），t ≥ 0 是参数。求交点就是求 t。

**射线 vs AABB（Slab 法）**：

把 AABB 看作三对平行平面（X 轴一对、Y 轴一对，2D 中只有两对）。射线进入 AABB 的条件是：在所有轴上，射线的"进入时间"小于"离开时间"。

```
t_x_min = (box.left - O.x) / D.x
t_x_max = (box.right - O.x) / D.x
若 D.x < 0，交换 t_x_min 和 t_x_max

t_y_min = (box.top - O.y) / D.y
t_y_max = (box.bottom - O.y) / D.y
若 D.y < 0，交换

t_enter = max(t_x_min, t_y_min)
t_exit  = min(t_x_max, t_y_max)

若 t_enter < t_exit 且 t_exit > 0：相交，t = max(t_enter, 0)
```

**射线 vs 圆**：

把射线方程代入圆方程 `(P - C)² = r²`，得到关于 t 的一元二次方程：

```
(D·D)·t² + 2·D·(O - C)·t + (O - C)² - r² = 0
```

判别式 `Δ = b² - 4ac`：Δ < 0 无交点，Δ = 0 相切，Δ > 0 两个交点（取较小正根）。

**射线 vs 线段**：

求射线与线段的交点，用参数方程联立：

```
射线: P(t) = O + D·t,  t ≥ 0
线段: Q(s) = A + (B - A)·s,  0 ≤ s ≤ 1

联立解 t 和 s，若都在有效范围内则相交。
```

### 例子

```cpp
struct Ray {
    Vec2 origin;
    Vec2 direction;  // 单位向量
};

struct RaycastHit {
    bool hit;
    float distance;  // 从射线起点到命中点的距离
    Vec2 point;      // 命中点
    Vec2 normal;     // 命中面的法向量
};

// 射线 vs AABB（Slab 法）
RaycastHit ray_vs_aabb(const Ray& ray, const AABB& box) {
    RaycastHit result = { false, 0, Vec2(0, 0), Vec2(0, 0) };

    float t_min = -FLT_MAX;
    float t_max = FLT_MAX;
    Vec2 normal;

    // X 轴
    if (std::abs(ray.direction.x) > 1e-6f) {
        float tx1 = (box.left() - ray.origin.x) / ray.direction.x;
        float tx2 = (box.right() - ray.origin.x) / ray.direction.x;
        Vec2 nx(1, 0);
        if (tx1 > tx2) { std::swap(tx1, tx2); nx = Vec2(-1, 0); }
        if (tx1 > t_min) { t_min = tx1; normal = nx; }
        if (tx2 < t_max) t_max = tx2;
        if (t_min > t_max) return result;
    } else {
        if (ray.origin.x < box.left() || ray.origin.x > box.right())
            return result;
    }

    // Y 轴
    if (std::abs(ray.direction.y) > 1e-6f) {
        float ty1 = (box.top() - ray.origin.y) / ray.direction.y;
        float ty2 = (box.bottom() - ray.origin.y) / ray.direction.y;
        Vec2 ny(0, 1);
        if (ty1 > ty2) { std::swap(ty1, ty2); ny = Vec2(0, -1); }
        if (ty1 > t_min) { t_min = ty1; normal = ny; }
        if (ty2 < t_max) t_max = ty2;
        if (t_min > t_max) return result;
    } else {
        if (ray.origin.y < box.top() || ray.origin.y > box.bottom())
            return result;
    }

    if (t_max < 0) return result;  // AABB 在射线背后

    result.hit = true;
    result.distance = t_min >= 0 ? t_min : t_max;
    result.point = ray.origin + ray.direction * result.distance;
    result.normal = normal;
    return result;
}

// 射线 vs 圆
RaycastHit ray_vs_circle(const Ray& ray, const Circle& c) {
    RaycastHit result = { false, 0, Vec2(0, 0), Vec2(0, 0) };

    Vec2 oc = ray.origin - c.center;
    float a = ray.direction.dot(ray.direction);  // = 1（单位向量）
    float b = 2 * oc.dot(ray.direction);
    float cc = oc.dot(oc) - c.radius * c.radius;

    float discriminant = b * b - 4 * a * cc;
    if (discriminant < 0) return result;

    float t = (-b - std::sqrt(discriminant)) / (2 * a);
    if (t < 0) {
        t = (-b + std::sqrt(discriminant)) / (2 * a);
        if (t < 0) return result;  // 圆在射线背后
    }

    result.hit = true;
    result.distance = t;
    result.point = ray.origin + ray.direction * t;
    result.normal = (result.point - c.center).normalized();
    return result;
}

// 实战：从玩家位置发射射线，找最近的命中物体
RaycastHit raycast_all(const Ray& ray,
                       const std::vector<AABB>& walls,
                       const std::vector<Circle>& obstacles) {
    RaycastHit closest = { false, FLT_MAX, Vec2(0, 0), Vec2(0, 0) };

    for (const auto& wall : walls) {
        auto hit = ray_vs_aabb(ray, wall);
        if (hit.hit && hit.distance < closest.distance) {
            closest = hit;
            closest.hit = true;
        }
    }
    for (const auto& obs : obstacles) {
        auto hit = ray_vs_circle(ray, obs);
        if (hit.hit && hit.distance < closest.distance) {
            closest = hit;
            closest.hit = true;
        }
    }
    return closest;
}

// 实战：AI 视线检测——敌人能否看到玩家？
bool can_see_player(const Vec2& enemy_pos, const Vec2& player_pos,
                    const std::vector<AABB>& walls) {
    Vec2 dir = player_pos - enemy_pos;
    float dist = dir.length();
    dir = dir / dist;
    Ray ray = { enemy_pos, dir };

    auto hit = raycast_all(ray, walls, {});
    // 若射线被墙挡住（命中距离 < 玩家距离），则看不到
    return !hit.hit || hit.distance >= dist;
}
```

### 总结

- 射线 `P(t) = O + D·t`，求交点就是求参数 t。
- **射线 vs AABB**：Slab 法，分别求 X/Y 轴的进入/离开时间，取交集。
- **射线 vs 圆**：代入圆方程得一元二次方程，判别式判断交点。
- **射线 vs 线段**：参数方程联立，检查 t 和 s 都在有效范围。
- **应用场景**：鼠标拾取、子弹射线、AI 视线、激光武器、视野遮蔽、光照计算。
- **性能优化**：对大量物体做射线检测时，先用 AABB 包围盒做粗筛，再用精确形状做细检。

---

# 第 6 章 · 物理进阶与实战

前五章我们手写了从运动学到 SAT 的全部算法，这让你深刻理解了物理引擎的内部机制。但在实际项目中，当物体数量超过几百、需要旋转物理、需要约束（关节、绳索）时，自己造轮子就不再划算。本章将介绍刚体与冲量的完整理论、角动量与旋转物理、空间分区优化（网格与四叉树），最后教你如何集成业界标准的 Box2D 物理引擎。学完本章，你就具备了从"手写物理"到"使用工业级引擎"的完整能力。

## 第 21 讲 · 刚体与冲量

### 概念

**刚体（Rigid Body）** 是物理引擎中对物体的抽象：一个有质量、位置、速度、旋转的不可形变物体。与质点不同，刚体有体积，可以旋转，能承受力矩。

**冲量（Impulse）** 是力在极短时间内的作用效果，定义为 `J = F·Δt`。冲量直接改变动量：`Δp = J`，即 `m·Δv = J`，所以 `Δv = J/m`。在碰撞响应中，我们用冲量而非力，因为碰撞时间极短（毫秒级），用力描述不直观。

### 原理

**完整刚体状态**：

```
位置 pos
速度 vel
角度 angle
角速度 angular_vel
质量 mass / 质量倒数 inv_mass
转动惯量 inertia / 转动惯量倒数 inv_inertia（2D 中是标量）
```

**转动惯量（Moment of Inertia）** 是旋转的"质量"，衡量物体抵抗角加速度的能力。常见形状的转动惯量公式：

- 矩形（宽 w 高 h，质量 m）：`I = m·(w² + h²) / 12`
- 圆（半径 r，质量 m）：`I = m·r² / 2`
- 圆环（外半径 R，内半径 r，质量 m）：`I = m·(R² + r²) / 2`

**冲量响应完整公式**（考虑旋转）：

```
// 接触点相对质心的位置
r_a = contact_point - a.pos
r_b = contact_point - b.pos

// 接触点速度（含旋转贡献）
v_a = a.vel + cross(a.angular_vel, r_a)  // 2D: cross(ω, r) = (-ω·r.y, ω·r.x)
v_b = b.vel + cross(b.angular_vel, r_b)

// 相对速度
rv = v_b - v_a

// 沿法向量的相对速度
vel_along_normal = rv.dot(normal)
if vel_along_normal > 0: return  // 已分离

// 冲量大小（含旋转）
ra_cross_n = r_a.cross(normal)
rb_cross_n = r_b.cross(normal)
inv_mass_sum = a.inv_mass + b.inv_mass
              + ra_cross_n² · a.inv_inertia
              + rb_cross_n² · b.inv_inertia

j = -(1 + e) · vel_along_normal / inv_mass_sum

// 应用冲量
impulse = normal · j
a.vel -= impulse · a.inv_mass
b.vel += impulse · b.inv_mass
a.angular_vel -= ra_cross_n · j · a.inv_inertia
b.angular_vel += rb_cross_n · j · b.inv_inertia
```

这就是 Box2D 等引擎的核心碰撞响应公式。相比第 15 讲的简化版，它多了旋转项，让物体碰撞后会旋转（如台球被侧面击中后会旋转）。

### 例子

```cpp
struct RigidBody2D {
    Vec2 pos;
    Vec2 vel;
    float angle;
    float angular_vel;
    float mass;
    float inv_mass;
    float inertia;
    float inv_inertia;
    float restitution;
    float friction;

    void set_mass(float m) {
        mass = m;
        inv_mass = m > 0 ? 1.0f / m : 0.0f;
    }
    void set_inertia(float i) {
        inertia = i;
        inv_inertia = i > 0 ? 1.0f / i : 0.0f;
    }
    // 矩形刚体的转动惯量
    void set_box_inertia(float w, float h) {
        set_inertia(mass * (w * w + h * h) / 12.0f);
    }
    // 圆形刚体的转动惯量
    void set_circle_inertia(float r) {
        set_inertia(mass * r * r / 2.0f);
    }
};

// 2D 叉积：标量 × 向量
Vec2 cross(float s, const Vec2& v) {
    return Vec2(-s * v.y, s * v.x);
}

// 完整冲量响应（含旋转）
void resolve_collision_full(RigidBody2D& a, RigidBody2D& b,
                             const Vec2& contact_point,
                             const Vec2& normal, float penetration) {
    // 位置修正（同第 14 讲）
    float total_inv = a.inv_mass + b.inv_mass;
    if (total_inv <= 0) return;
    Vec2 correction = normal * (std::max(penetration - 0.01f, 0.0f)
                                 / total_inv * 0.8f);
    a.pos -= correction * a.inv_mass;
    b.pos += correction * b.inv_mass;

    // 接触点相对质心
    Vec2 r_a = contact_point - a.pos;
    Vec2 r_b = contact_point - b.pos;

    // 接触点速度
    Vec2 v_a = a.vel + cross(a.angular_vel, r_a);
    Vec2 v_b = b.vel + cross(b.angular_vel, r_b);
    Vec2 rv = v_b - v_a;

    float vel_along_normal = rv.dot(normal);
    if (vel_along_normal > 0) return;

    float e = std::min(a.restitution, b.restitution);

    float ra_cross_n = r_a.cross(normal);
    float rb_cross_n = r_b.cross(normal);
    float inv_mass_sum = a.inv_mass + b.inv_mass
                       + ra_cross_n * ra_cross_n * a.inv_inertia
                       + rb_cross_n * rb_cross_n * b.inv_inertia;

    float j = -(1 + e) * vel_along_normal / inv_mass_sum;
    Vec2 impulse = normal * j;

    a.vel -= impulse * a.inv_mass;
    b.vel += impulse * b.inv_mass;
    a.angular_vel -= r_a.cross(impulse) * a.inv_inertia;
    b.angular_vel += r_b.cross(impulse) * b.inv_inertia;

    // 摩擦冲量（切向）
    Vec2 v_a2 = a.vel + cross(a.angular_vel, r_a);
    Vec2 v_b2 = b.vel + cross(b.angular_vel, r_b);
    Vec2 rv2 = v_b2 - v_a2;
    Vec2 t = rv2 - normal * rv2.dot(normal);
    if (t.length_sq() > 1e-6f) {
        t = t.normalized();
        float ra_cross_t = r_a.cross(t);
        float rb_cross_t = r_b.cross(t);
        float inv_mass_sum_t = a.inv_mass + b.inv_mass
                             + ra_cross_t * ra_cross_t * a.inv_inertia
                             + rb_cross_t * rb_cross_t * b.inv_inertia;
        float jt = -rv2.dot(t) / inv_mass_sum_t;
        float friction = std::sqrt(a.friction * b.friction);
        jt = std::clamp(jt, -j * friction, j * friction);
        Vec2 friction_impulse = t * jt;
        a.vel -= friction_impulse * a.inv_mass;
        b.vel += friction_impulse * b.inv_mass;
        a.angular_vel -= r_a.cross(friction_impulse) * a.inv_inertia;
        b.angular_vel += r_b.cross(friction_impulse) * b.inv_inertia;
    }
}

// 实战：台球游戏，球被球杆侧面击中后会旋转
void billiards_demo() {
    RigidBody2D cue_ball, target_ball;
    cue_ball.set_mass(0.17f);  // 标准台球 170g
    cue_ball.set_circle_inertia(0.028f);  // 半径 2.8cm
    cue_ball.restitution = 0.95f;
    cue_ball.friction = 0.1f;
    cue_ball.pos = Vec2(200, 300);
    cue_ball.vel = Vec2(500, 0);  // 水平高速

    target_ball.set_mass(0.17f);
    target_ball.set_circle_inertia(0.028f);
    target_ball.restitution = 0.95f;
    target_ball.friction = 0.1f;
    target_ball.pos = Vec2(400, 305);  // 略偏，产生侧面碰撞

    // 碰撞接触点近似为两球心连线中点
    Vec2 contact = (cue_ball.pos + target_ball.pos) * 0.5f;
    Vec2 normal = (target_ball.pos - cue_ball.pos).normalized();
    resolve_collision_full(cue_ball, target_ball, contact, normal, 0.5f);
    // 碰撞后两球都会旋转
}
```

### 总结

- 刚体是物理引擎的核心抽象，包含位置、速度、角度、角速度、质量、转动惯量。
- 冲量 `J = F·Δt` 直接改变动量，碰撞响应用冲量而非力。
- 转动惯量是旋转的"质量"，矩形 `I = m(w²+h²)/12`，圆 `I = mr²/2`。
- 完整冲量响应包含旋转项，让物体碰撞后自然旋转，这是 Box2D 的核心算法。
- **性能**：完整响应比简化版慢约 5 倍，但对真实感提升巨大。

---

## 第 22 讲 · 角动量与旋转物理

### 概念

**角动量（Angular Momentum）** `L = I·ω` 是旋转的动量，描述物体保持旋转的趋势。**力矩（Torque）** `τ = r × F` 是旋转的"力"，改变角动量：`ΔL = τ·Δt`，即 `I·Δω = τ·Δt`，所以 `Δω = τ·Δt / I`。

理解角动量与力矩，才能实现真实的旋转物理：陀螺仪、旋转的飞镖、不倒翁、保龄球的旋转。

### 原理

**力矩的计算**：力 `F` 作用在距质心 `r` 处，产生的力矩 `τ = r × F`（2D 中是标量，`τ = r.x·F.y - r.y·F.x`）。

- 力作用在质心（r=0）：无力矩，不旋转。
- 力沿质心方向（r 与 F 平行）：无力矩，不旋转。
- 力垂直于质心方向（r 与 F 垂直）：力矩最大，旋转最强。

**角动量守恒**：无外力矩时，角动量守恒。花样滑冰运动员收拢手臂时转动惯量减小，角速度增大——这就是角动量守恒的经典例子。

**旋转阻尼**：与线性阻尼类似，旋转阻尼让角速度逐渐衰减：

```
ω(t+Δt) = ω(t) · e^(-damping·Δt)
```

**旋转积分**：

```
// 应用力矩
angular_vel += (torque / inertia) · dt
// 旋转阻尼
angular_vel *= exp(-angular_damping · dt)
// 更新角度
angle += angular_vel · dt
```

### 例子

```cpp
struct RotatingBody {
    Vec2 pos;
    Vec2 vel;
    float angle;
    float angular_vel;
    float mass;
    float inv_mass;
    float inertia;
    float inv_inertia;
    float angular_damping;

    // 施加力（作用在质心）
    void apply_force(const Vec2& force, float dt) {
        vel += force * (inv_mass * dt);
    }

    // 施加力矩（直接给）
    void apply_torque(float torque, float dt) {
        angular_vel += torque * (inv_inertia * dt);
    }

    // 施加力在指定点（产生力矩）
    void apply_force_at(const Vec2& force, const Vec2& world_point, float dt) {
        // 线性部分
        vel += force * (inv_mass * dt);
        // 旋转部分：力矩 = r × F
        Vec2 r = world_point - pos;
        float torque = r.cross(force);
        angular_vel += torque * (inv_inertia * dt);
    }

    // 施加冲量在指定点
    void apply_impulse_at(const Vec2& impulse, const Vec2& world_point) {
        vel += impulse * inv_mass;
        Vec2 r = world_point - pos;
        angular_vel += r.cross(impulse) * inv_inertia;
    }

    // 更新
    void update(float dt) {
        // 旋转阻尼
        angular_vel *= std::exp(-angular_damping * dt);
        // 积分
        pos += vel * dt;
        angle += angular_vel * dt;
    }
};

// 实战：飞镖投掷
struct Dart : public RotatingBody {
    // 飞镖头部位置（世界坐标）
    Vec2 head_position() const {
        Vec2 head_local(20, 0);  // 飞镖头在本地坐标 (20, 0)
        float c = std::cos(angle), s = std::sin(angle);
        return Vec2(pos.x + head_local.x * c - head_local.y * s,
                    pos.y + head_local.x * s + head_local.y * c);
    }
};

void throw_dart(Dart& dart, const Vec2& throw_velocity) {
    dart.vel = throw_velocity;
    dart.angular_vel = 0;

    // 空气阻力作用在飞镖头部，产生力矩让飞镖朝向运动方向
    // 每帧施加
}

void update_dart(Dart& dart, float dt) {
    // 重力
    dart.apply_force(Vec2(0, 500), dt);

    // 空气阻力作用在头部，让飞镖朝向速度方向
    Vec2 head = dart.head_position();
    Vec2 air_drag = -dart.vel.normalized() * dart.vel.length_sq() * 0.001f;
    dart.apply_force_at(air_drag, head, dt);

    dart.update(dt);
}

// 实战：不倒翁
struct RolyPoly : public RotatingBody {
    // 不倒翁底部圆心在质心下方
    // 重力作用在质心，当倾斜时产生恢复力矩
};

void update_roly_poly(RolyPoly& toy, float dt) {
    // 重力作用在质心（质心在底部圆心上方）
    // 当玩具倾斜时，质心偏离底部圆心正上方，重力产生恢复力矩
    Vec2 center_of_mass = toy.pos;  // 假设质心在 pos
    Vec2 bottom_center = toy.pos + Vec2(0, 30);  // 底部圆心在质心下方 30
    Vec2 gravity(0, 1000);  // 重力
    // 重力作用在质心，但相对底部圆心（支撑点）产生力矩
    Vec2 r = center_of_mass - bottom_center;
    // 旋转 r 到玩具本地坐标系
    float c = std::cos(toy.angle), s = std::sin(toy.angle);
    Vec2 r_local(r.x * c + r.y * s, -r.x * s + r.y * c);
    // 重力在本地坐标系
    Vec2 g_local(gravity.x * c + gravity.y * s, -gravity.x * s + gravity.y * c);
    float torque = r_local.cross(g_local);
    toy.apply_torque(torque, dt);
    toy.update(dt);
}
```

### 总结

- 角动量 `L = Iω`，力矩 `τ = r × F`，关系 `Δω = τ·Δt / I`。
- 力作用在质心不产生力矩；力垂直于质心方向力矩最大。
- 角动量守恒：无外力矩时 `Iω` 不变，转动惯量减小则角速度增大。
- 旋转阻尼用指数衰减 `ω *= e^(-d·Δt)`，与线性阻尼一致。
- **应用场景**：飞镖、保龄球、不倒翁、陀螺、绳索摆动、车辆漂移。

---

## 第 23 讲 · 空间分区优化（网格 / 四叉树）

### 概念

当场景中有数千甚至数万物体时，O(N²) 的暴力检测完全不可行。**空间分区（Spatial Partitioning）** 把空间划分为若干区域，每个物体只与自己所在区域及相邻区域的物体做碰撞检测，把复杂度从 O(N²) 降到接近 O(N)。

主流的空间分区方法有三种：**均匀网格（Uniform Grid）**、**四叉树（Quadtree）**、**空间哈希（Spatial Hashing）**。本讲重点讲前两种。

### 原理

**均匀网格**：

把空间划分为固定大小的方格（如 64×64 像素）。每个物体根据其位置分配到一个或多个格子。碰撞检测时，每个物体只与同格子及相邻 8 个格子的物体检测。

```
cell_size = 64  // 通常取最大物体直径
cell_x = floor(obj.x / cell_size)
cell_y = floor(obj.y / cell_size)

// 检测时遍历 (cell_x-1, cell_y-1) 到 (cell_x+1, cell_y+1) 共 9 个格子
```

**优点**：实现极简，查询 O(1)。
**缺点**：物体大小差异大时浪费空间（小物体在大格子里，大物体跨多个格子）。

**四叉树**：

递归地把空间划分为四象限。当某个象限的物体数超过阈值（如 8）时，再细分为四个子象限。查询时只遍历与查询范围相交的象限。

```
QuadtreeNode:
    AABB bounds
    std::vector<Object> objects
    QuadtreeNode* children[4]  // 左上、右上、左下、右下
    bool is_leaf

insert(obj):
    if not contains(bounds, obj): return false
    if is_leaf and objects.size() < MAX:
        objects.push_back(obj)
        return true
    if is_leaf:
        split()  // 分裂为四个子节点
    for child in children:
        if child.insert(obj): return true

query(range, result):
    if not intersects(bounds, range): return
    for obj in objects:
        if intersects(obj, range): result.push_back(obj)
    if not is_leaf:
        for child in children:
            child.query(range, result)
```

**优点**：自适应，稀疏区域少分裂，密集区域多分裂。
**缺点**：实现复杂，物体移动时需频繁删除/插入。

**空间哈希**：

均匀网格的变种，用哈希表存储格子，避免预分配大数组。适合世界无限大的场景。

### 例子

```cpp
// ============ 均匀网格 ============
class UniformGrid {
public:
    UniformGrid(float cell_size, int cols, int rows)
        : cell_size_(cell_size), cols_(cols), rows_(rows) {
        cells_.resize(cols * rows);
    }

    void clear() {
        for (auto& cell : cells_) cell.clear();
    }

    void insert(int object_id, const AABB& box) {
        int x0 = (int)(box.left() / cell_size_);
        int y0 = (int)(box.top() / cell_size_);
        int x1 = (int)(box.right() / cell_size_);
        int y1 = (int)(box.bottom() / cell_size_);
        for (int y = y0; y <= y1; ++y) {
            for (int x = x0; x <= x1; ++x) {
                if (x >= 0 && x < cols_ && y >= 0 && y < rows_) {
                    cells_[y * cols_ + x].push_back(object_id);
                }
            }
        }
    }

    // 查询某物体可能碰撞的所有物体
    std::vector<int> query_potential(const AABB& box) const {
        std::vector<int> result;
        std::unordered_set<int> seen;  // 去重
        int x0 = (int)(box.left() / cell_size_) - 1;
        int y0 = (int)(box.top() / cell_size_) - 1;
        int x1 = (int)(box.right() / cell_size_) + 1;
        int y1 = (int)(box.bottom() / cell_size_) + 1;
        for (int y = y0; y <= y1; ++y) {
            for (int x = x0; x <= x1; ++x) {
                if (x >= 0 && x < cols_ && y >= 0 && y < rows_) {
                    for (int id : cells_[y * cols_ + x]) {
                        if (seen.insert(id).second) {
                            result.push_back(id);
                        }
                    }
                }
            }
        }
        return result;
    }

private:
    float cell_size_;
    int cols_, rows_;
    std::vector<std::vector<int>> cells_;
};

// ============ 四叉树 ============
class Quadtree {
public:
    Quadtree(const AABB& bounds, int max_objects = 8, int max_depth = 5)
        : bounds_(bounds), max_objects_(max_objects), max_depth_(max_depth),
          is_leaf_(true), depth_(0) {}

    void clear() {
        objects_.clear();
        if (!is_leaf_) {
            for (auto& child : children_) child->clear();
            children_.clear();
            is_leaf_ = true;
        }
    }

    bool insert(int id, const AABB& box) {
        if (!aabb_vs_aabb(box, bounds_)) return false;

        if (is_leaf_ && (int)objects_.size() < max_objects_) {
            objects_.push_back({id, box});
            return true;
        }

        if (is_leaf_) {
            if (depth_ >= max_depth_) {
                objects_.push_back({id, box});
                return true;
            }
            split();
        }

        for (auto& child : children_) {
            if (child->insert(id, box)) return true;
        }
        // 跨多个子节点，存在父节点
        objects_.push_back({id, box});
        return true;
    }

    void query(const AABB& range, std::vector<int>& result) const {
        if (!aabb_vs_aabb(range, bounds_)) return;
        for (const auto& obj : objects_) {
            if (aabb_vs_aabb(range, obj.box)) {
                result.push_back(obj.id);
            }
        }
        if (!is_leaf_) {
            for (const auto& child : children_) {
                child->query(range, result);
            }
        }
    }

private:
    struct Entry { int id; AABB box; };

    void split() {
        float cx = bounds_.x + bounds_.w / 2;
        float cy = bounds_.y + bounds_.h / 2;
        float hw = bounds_.w / 2;
        float hh = bounds_.h / 2;

        children_.resize(4);
        children_[0] = std::make_unique<Quadtree>(
            AABB{bounds_.x, bounds_.y, hw, hh}, max_objects_, max_depth_);
        children_[1] = std::make_unique<Quadtree>(
            AABB{cx, bounds_.y, hw, hh}, max_objects_, max_depth_);
        children_[2] = std::make_unique<Quadtree>(
            AABB{bounds_.x, cy, hw, hh}, max_objects_, max_depth_);
        children_[3] = std::make_unique<Quadtree>(
            AABB{cx, cy, hw, hh}, max_objects_, max_depth_);
        for (auto& child : children_) child->depth_ = depth_ + 1;

        // 把现有物体重新分配到子节点
        std::vector<Entry> remaining;
        for (auto& obj : objects_) {
            bool inserted = false;
            for (auto& child : children_) {
                if (child->insert(obj.id, obj.box)) {
                    inserted = true;
                    break;
                }
            }
            if (!inserted) remaining.push_back(obj);
        }
        objects_ = std::move(remaining);
        is_leaf_ = false;
    }

    AABB bounds_;
    int max_objects_;
    int max_depth_;
    int depth_;
    bool is_leaf_;
    std::vector<Entry> objects_;
    std::vector<std::unique_ptr<Quadtree>> children_;
};

// 实战：10000 个物体的碰撞检测
void benchmark_spatial_partition() {
    std::vector<AABB> objects;
    for (int i = 0; i < 10000; ++i) {
        objects.push_back({
            (float)(rand() % 780), (float)(rand() % 580), 10, 10
        });
    }

    // 暴力：约 50,000,000 次检测
    // 网格：约 50,000 次（每个物体平均 5 个邻居）
    // 四叉树：约 30,000 次（更优）

    UniformGrid grid(64, 13, 10);
    for (int i = 0; i < (int)objects.size(); ++i) {
        grid.insert(i, objects[i]);
    }
    for (int i = 0; i < (int)objects.size(); ++i) {
        auto candidates = grid.query_potential(objects[i]);
        for (int j : candidates) {
            if (j > i && aabb_vs_aabb(objects[i], objects[j])) {
                // 处理碰撞
            }
        }
    }
}
```

### 总结

- 空间分区把 O(N²) 降到接近 O(N)，是大规模场景的必备优化。
- **均匀网格**：实现极简，适合物体大小均匀的场景；查询 O(1)。
- **四叉树**：自适应，适合物体分布不均的场景；实现复杂。
- **空间哈希**：均匀网格 + 哈希表，适合无限大世界。
- **选择建议**：N<100 用暴力；100<N<1000 用网格；N>1000 用四叉树；无限世界用空间哈希。
- **常见坑**：物体跨多个格子时需去重（用 set 或检查 id 大小）；四叉树每帧 clear + 重建比动态删除/插入更快。

---

## 第 24 讲 · 集成 Box2D 物理引擎

### 概念

**Box2D** 是业界最知名的 2D 物理引擎，由 Erin Catto 开发，用 C++ 编写。它被用于《愤怒的小鸟》《Cut the Rope》《Hollow Knight》等知名游戏。Box2D 提供完整的刚体物理、碰撞检测、关节约束、连续碰撞检测（CCD），是手写物理的工业级替代方案。

集成 Box2D 让你专注于游戏逻辑，而非物理算法。但理解前 23 讲的内容，能让你更好地调参、调试、扩展 Box2D。

### 原理

Box2D 的核心概念：

- **World（世界）**：物理场景的容器，管理所有物体、重力、求解器。
- **Body（刚体）**：有质量、位置、速度、旋转的物体。三种类型：
  - `staticBody`：静态物体（墙壁、地面），不移动，inv_mass=0。
  - `kinematicBody`：运动学物体（移动平台），手动设置速度，不受力。
  - `dynamicBody`：动态物体（玩家、敌人），受力和碰撞影响。
- **Fixture（夹具）**：刚体的碰撞形状（圆、矩形、多边形）+ 材质属性（密度、摩擦、弹性）。
- **Shape（形状）**：`b2CircleShape`、`b2PolygonShape`、`b2EdgeShape`。
- **Joint（关节）**：连接两个刚体的约束，如距离关节、旋转关节、滑轮关节。

**Box2D 的单位**：Box2D 使用米-千克-秒（MKS）单位制，1 单位 = 1 米。游戏中的像素需要转换为米，通常 1 米 = 50~100 像素。Box2D 优化范围为 0.1~10 米的物体，过小或过大会导致数值不稳定。

**模拟流程**：

```
1. 创建 World（设置重力）
2. 创建 Bodies（定义类型、位置）
3. 创建 Fixtures（定义形状、密度、摩擦、弹性）
4. 游戏循环：
   a. 应用外力/冲量到 Bodies
   b. world.Step(dt, velocity_iterations, position_iterations)
   c. 从 Bodies 读取位置和角度，更新 SDL 渲染
5. 销毁 World（自动释放所有资源）
```

### 例子

```cpp
#include <SDL2/SDL.h>
#include <box2d/box2d.h>

// 像素与米的转换
const float PIXELS_PER_METER = 50.0f;
float to_pixels(float meters) { return meters * PIXELS_PER_METER; }
float to_meters(float pixels) { return pixels / PIXELS_PER_METER; }

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Box2D Demo",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 800, 600, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, 0);

    // 1. 创建物理世界（重力向下 9.8 m/s²）
    b2World world(b2Vec2(0.0f, 9.8f));

    // 2. 创建地面（静态物体）
    b2BodyDef ground_def;
    ground_def.position.Set(to_meters(400), to_meters(580));
    b2Body* ground = world.CreateBody(&ground_def);

    b2PolygonShape ground_shape;
    ground_shape.SetAsBox(to_meters(400), to_meters(20));
    b2FixtureDef ground_fixture;
    ground_fixture.shape = &ground_shape;
    ground_fixture.friction = 0.7f;
    ground_fixture.restitution = 0.3f;
    ground->CreateFixture(&ground_fixture);

    // 3. 创建一堆动态盒子
    std::vector<b2Body*> boxes;
    for (int i = 0; i < 10; ++i) {
        b2BodyDef box_def;
        box_def.type = b2_dynamicBody;
        box_def.position.Set(to_meters(200 + i * 30), to_meters(100));
        b2Body* box = world.CreateBody(&box_def);

        b2PolygonShape box_shape;
        box_shape.SetAsBox(to_meters(15), to_meters(15));
        b2FixtureDef box_fixture;
        box_fixture.shape = &box_shape;
        box_fixture.density = 1.0f;       // 密度 1 kg/m²
        box_fixture.friction = 0.5f;
        box_fixture.restitution = 0.4f;   // 略有弹性
        box->CreateFixture(&box_fixture);
        boxes.push_back(box);
    }

    // 4. 创建一个圆形球
    b2BodyDef ball_def;
    ball_def.type = b2_dynamicBody;
    ball_def.position.Set(to_meters(400), to_meters(50));
    b2Body* ball = world.CreateBody(&ball_def);

    b2CircleShape ball_shape;
    ball_shape.m_radius = to_meters(20);
    b2FixtureDef ball_fixture;
    ball_fixture.shape = &ball_shape;
    ball_fixture.density = 0.5f;
    ball_fixture.friction = 0.3f;
    ball_fixture.restitution = 0.8f;  // 高弹性
    ball->CreateFixture(&ball_fixture);

    // 5. 游戏循环
    const double FIXED_DT = 1.0 / 60.0;
    double accumulator = 0.0;
    Uint64 last = SDL_GetPerformanceCounter();

    bool running = true;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) {
            if (e.type == SDL_QUIT) running = false;
            if (e.type == SDL_MOUSEBUTTONDOWN) {
                // 点击施加冲量
                int mx, my;
                SDL_GetMouseState(&mx, &my);
                b2Vec2 impulse(to_meters(mx - to_pixels(ball->GetPosition().x)),
                               to_meters(my - to_pixels(ball->GetPosition().y)));
                impulse *= 5.0f;
                ball->ApplyLinearImpulseToCenter(impulse, true);
            }
        }

        Uint64 now = SDL_GetPerformanceCounter();
        double frame_time = (double)(now - last) / SDL_GetPerformanceFrequency();
        last = now;
        if (frame_time > 0.25) frame_time = 0.25;
        accumulator += frame_time;

        // 物理步进
        while (accumulator >= FIXED_DT) {
            // velocity_iterations: 速度约束求解次数（默认 8）
            // position_iterations: 位置约束求解次数（默认 3）
            world.Step((float)FIXED_DT, 8, 3);
            accumulator -= FIXED_DT;
        }

        // 渲染
        SDL_SetRenderDrawColor(renderer, 20, 20, 30, 255);
        SDL_RenderClear(renderer);

        // 地面
        SDL_SetRenderDrawColor(renderer, 80, 80, 80, 255);
        SDL_Rect ground_rect = { 0, 560, 800, 40 };
        SDL_RenderFillRect(renderer, &ground_rect);

        // 盒子
        SDL_SetRenderDrawColor(renderer, 100, 200, 255, 255);
        for (b2Body* box : boxes) {
            b2Vec2 pos = box->GetPosition();
            float angle = box->GetAngle();
            SDL_Rect r = {
                (int)(to_pixels(pos.x) - 15),
                (int)(to_pixels(pos.y) - 15),
                30, 30
            };
            // 简化：不画旋转，实际应用 SDL_RenderCopyEx
            SDL_RenderFillRect(renderer, &r);
        }

        // 球
        SDL_SetRenderDrawColor(renderer, 255, 200, 80, 255);
        b2Vec2 ball_pos = ball->GetPosition();
        SDL_Rect ball_rect = {
            (int)(to_pixels(ball_pos.x) - 20),
            (int)(to_pixels(ball_pos.y) - 20),
            40, 40
        };
        SDL_RenderFillRect(renderer, &ball_rect);

        SDL_RenderPresent(renderer);
    }

    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- Box2D 是业界标准 2D 物理引擎，提供完整刚体物理、碰撞、关节、CCD。
- 核心概念：World（世界）、Body（刚体）、Fixture（夹具）、Shape（形状）、Joint（关节）。
- 三种刚体类型：static（静态）、kinematic（运动学）、dynamic（动态）。
- **单位转换**：Box2D 用米，游戏用像素，通常 1 米 = 50~100 像素。
- **物体大小**：优化范围 0.1~10 米，过小或过大会数值不稳定。
- **求解器迭代**：velocity_iterations 默认 8，position_iterations 默认 3，数值不稳定时增大。
- **何时用 Box2D**：需要复杂物理（堆叠、关节、布料）、物体数量大、追求真实感时；简单平台游戏可手写。
- **学习路径**：先手写前 23 讲的内容理解原理，再用 Box2D 提升效率，遇到问题能调试与扩展。

---

## 课程结语

恭喜你完成了《SDL2 碰撞与物理》的全部 24 讲！让我们回顾这段旅程：

**第 1 章 基础准备**：搭建 SDL2 项目骨架，理解游戏循环与固定时间步长，掌握 2D 向量数学，完成基本图形渲染。

**第 2 章 运动学**：从位置-速度-加速度三件套出发，实现重力、跳跃、摩擦力、抛体运动，理解半隐式欧拉积分与可变跳跃高度等手感技巧。

**第 3 章 碰撞检测基础**：掌握 AABB、圆形、点包含三大基础检测算法，学习 Sweep and Prune 多重碰撞遍历策略。

**第 4 章 碰撞响应**：理解穿透深度与法向量，实现位置修正防穿透，掌握速度反射与弹性系数，让碰撞有真实的"质感"。

**第 5 章 高级碰撞**：突破 AABB 局限，学习圆与矩形、OBB、SAT 分离轴定理、射线投射，能处理任意形状的物体。

**第 6 章 物理进阶与实战**：完整刚体与冲量理论、角动量与旋转物理、空间分区优化（网格与四叉树），最后集成工业级 Box2D 引擎。

**下一步学习建议**：

1. **实践项目**：用所学知识实现一个完整的平台跳跃游戏或弹球游戏。
2. **进阶主题**：连续碰撞检测（CCD）、布料与软体物理、流体模拟、关节约束。
3. **3D 扩展**：学习 3D 数学（矩阵、四元数），探索 Bullet、PhysX 等 3D 物理引擎。
4. **性能优化**：学习数据导向设计（DOD）、SIMD 向量化、多线程物理。

物理与碰撞是游戏开发中最具挑战也最有成就感的领域之一。希望这份教程能成为你探索游戏物理世界的可靠指南。祝你编码愉快！
