# OpenGL · 系统教程

> 一本从三角形到延迟渲染的现代 OpenGL 实战教材。

---

## 课程总览

**预计讲数**：32 讲（8 章 × 4 讲）

**学习目标**：

1. 理解 OpenGL 的架构与状态机模型，搭建跨平台开发环境，掌握窗口创建与上下文初始化。
2. 熟练使用 VAO/VBO/EBO 管理顶点数据，编写 GLSL 顶点与片段着色器，实现图元绘制。
3. 掌握 3D 数学基础：向量、矩阵、齐次坐标、MVP 变换链、坐标系转换。
4. 理解纹理映射原理，掌握 UV 坐标、过滤模式、Mipmap、UV 寻址、纹理压缩。
5. 实现多种光照模型：Phong、Blinn-Phong、多光源、材质系统、光照贴图。
6. 掌握高级渲染技术：帧缓冲、后处理、阴影映射、法线贴图、HDR、Gamma 校正。
7. 学会性能优化：实例化、批处理、查询对象、显存管理、着色器优化、性能分析。
8. 具备开发现代 OpenGL 应用的能力：引擎架构、资源管理、调试工具、跨平台部署。

**前置知识**：C/C++ 基础、基本线性代数（向量、矩阵）、基本几何（坐标、三角函数）。

**学习路径**：入门基础 → 图元与着色器 → 变换与坐标 → 纹理与采样 → 光照与材质 → 高级渲染 → 性能与优化 → 现代 OpenGL 实战

---

## 详细章节目录

### 第 1 章 · 入门基础

- 第 01 讲：OpenGL 概述与历史
- 第 02 讲：开发环境搭建
- 第 03 讲：窗口与 OpenGL 上下文
- 第 04 讲：第一个三角形

### 第 2 章 · 图元与着色器

- 第 05 讲：VAO、VBO 与 EBO
- 第 06 讲：GLSL 顶点着色器
- 第 07 讲：GLSL 片段着色器
- 第 08 讲：图元类型与绘制

### 第 3 章 · 变换与坐标

- 第 09 讲：向量与矩阵基础
- 第 10 讲：平移、旋转、缩放
- 第 11 讲：MVP 变换链
- 第 12 讲：坐标系与投影

### 第 4 章 · 纹理与采样

- 第 13 讲：纹理映射基础
- 第 14 讲：UV 坐标与过滤
- 第 15 讲：Mipmap 与多级过滤
- 第 16 讲：纹理寻址与图集

### 第 5 章 · 光照与材质

- 第 17 讲：光照模型基础
- 第 18 讲：Phong 与 Blinn-Phong
- 第 19 讲：多光源与材质
- 第 20 讲：光照贴图与法线贴图

### 第 6 章 · 高级渲染

- 第 21 讲：帧缓冲与离屏渲染
- 第 22 讲：后处理效果
- 第 23 讲：阴影映射
- 第 24 讲：HDR 与 Gamma 校正

### 第 7 章 · 性能与优化

- 第 25 讲：实例化渲染
- 第 26 讲：批处理与状态排序
- 第 27 讲：查询对象与遮挡剔除
- 第 28 讲：着色器优化

### 第 8 章 · 现代 OpenGL 实战

- 第 29 讲：引擎架构设计
- 第 30 讲：资源管理器
- 第 31 讲：调试与错误处理
- 第 32 讲：跨平台部署

---

# 第 1 章 · 入门基础

OpenGL 是工业标准的跨平台 2D/3D 图形 API，自 1992 年由 SGI 发布以来，已成为游戏、CAD、科学可视化、虚拟现实等领域的基础。本章从 OpenGL 的历史与架构开始，搭建开发环境，创建窗口与 OpenGL 上下文，绘制第一个三角形。这些是所有 OpenGL 程序的起点——理解状态机模型与可编程管线，才能掌握现代 OpenGL。

## 第 01 讲 · OpenGL 概述与历史

### 概念

**OpenGL（Open Graphics Library）** 是跨平台的 2D/3D 图形 API，定义了图形渲染的标准接口。它本身不是软件，而是一份规范，由 Khronos Group 维护，各 GPU 厂商实现驱动。

**现代 OpenGL**（3.0+，2008 年）采用**可编程管线**（Programmable Pipeline），开发者用 GLSL 着色器控制渲染的每个阶段。这与早期 OpenGL 的**固定管线**（Fixed Pipeline）截然不同——固定管线用预定义函数（如 `glBegin/glEnd`）渲染，简单但灵活性低。

### 原理

**OpenGL 的架构**：

OpenGL 是一个**状态机（State Machine）**——你设置状态（如当前颜色、纹理、着色器），后续绘制命令使用这些状态，直到状态改变。理解状态机模型是掌握 OpenGL 的关键：

```
设置状态 → 绘制 → 设置新状态 → 绘制 → ...
```

例如：

```c
glUseProgram(shader1);     // 使用着色器 1
glBindTexture(texture1);   // 绑定纹理 1
glDrawArrays(...);         // 用着色器 1 + 纹理 1 绘制

glBindTexture(texture2);   // 改变纹理
glDrawArrays(...);         // 用着色器 1 + 纹理 2 绘制
```

**渲染管线（Rendering Pipeline）**：

现代 OpenGL 的渲染管线分多个阶段，部分阶段可编程（着色器），部分阶段固定（配置）：

```
顶点数据 → [顶点着色器] → 图元装配 → [几何着色器] → 光栅化 → [片段着色器] → 测试与混合 → 帧缓冲
```

- **顶点着色器（Vertex Shader）**：处理每个顶点，计算其位置。
- **图元装配（Primitive Assembly）**：把顶点组装为图元（三角形、线等）。
- **几何着色器（Geometry Shader）**：可选，生成/修改图元。
- **光栅化（Rasterization）**：把图元转换为像素（片段）。
- **片段着色器（Fragment Shader）**：处理每个片段，计算颜色。
- **测试与混合（Tests & Blending）**：深度测试、模板测试、混合。

**OpenGL 版本演进**：

| 版本 | 年份 | 重要特性 |
|------|------|---------|
| 1.0 | 1992 | 固定管线，glBegin/glEnd |
| 1.1 | 1997 | VBO、纹理坐标生成 |
| 2.0 | 2004 | GLSL 着色器，可编程管线开端 |
| 3.0 | 2008 | 核心模式与兼容模式分离，VAO |
| 3.3 | 2010 | GLSL 3.30，现代 OpenGL 入门 |
| 4.0 | 2010 | 细分着色器（Tessellation） |
| 4.3 | 2012 | 计算着色器（Compute Shader） |
| 4.6 | 2017 | SPIR-V 支持，最新稳定版 |

**核心模式 vs 兼容模式**：

- **核心模式（Core Profile）**：移除所有废弃 API（glBegin/glEnd、固定管线），强制使用现代 API。本教程全程使用核心模式。
- **兼容模式（Compatibility Profile）**：保留旧 API，向后兼容。不推荐新项目使用。

**OpenGL 与其他 API**：

| API | 平台 | 特点 |
|-----|------|------|
| OpenGL | 跨平台 | 开放标准，学习友好 |
| Vulkan | 跨平台 | 显式控制，高性能，复杂 |
| Direct3D | Windows | 微软生态，Xbox |
| Metal | Apple | iOS/macOS 专用 |
| WebGL | 浏览器 | OpenGL ES 子集 |

### 例子

```c
// OpenGL 程序的基本结构（伪代码）
#include <GL/glew.h>
#include <GLFW/glfw3.h>

int main() {
    // 1. 初始化窗口库
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);

    // 2. 创建窗口
    GLFWwindow* window = glfwCreateWindow(800, 600, "OpenGL", nullptr, nullptr);
    glfwMakeContextCurrent(window);

    // 3. 加载 OpenGL 函数
    glewInit();

    // 4. 设置视口
    glViewport(0, 0, 800, 600);

    // 5. 主循环
    while (!glfwWindowShouldClose(window)) {
        // 清屏
        glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT);

        // 绘制（后续章节详讲）
        // glDrawArrays(...);

        // 交换缓冲、处理事件
        glfwSwapBuffers(window);
        glfwPollEvents();
    }

    // 6. 清理
    glfwTerminate();
    return 0;
}
```

### 总结

- OpenGL 是跨平台图形 API，由 Khronos Group 维护，是规范而非软件。
- **状态机模型**：设置状态 → 绘制 → 改变状态 → 绘制。
- **可编程管线**：顶点着色器 → 图元装配 → 光栅化 → 片段着色器 → 测试混合。
- **核心模式**移除旧 API，强制现代编程方式，本教程全程使用。
- **版本选择**：3.3 是现代 OpenGL 入门，4.6 是最新稳定版。
- **常见坑**：误用兼容模式导致学到废弃 API；不理解状态机导致状态泄漏。

---

## 第 02 讲 · 开发环境搭建

### 概念

OpenGL 开发需要三类工具：

1. **窗口与上下文管理库**：GLFW、SDL2、GLUT（已过时）。
2. **函数加载库**：GLEW、glad、gl3w——加载 OpenGL 扩展函数。
3. **数学库**：GLM（OpenGL Mathematics），提供向量、矩阵运算。

本讲搭建一个完整的 OpenGL 开发环境，使用 GLFW + GLEW + GLM 组合，这是现代 OpenGL 学习的标准配置。

### 原理

**为什么需要窗口库？**

OpenGL 本身只管渲染，不管窗口创建、输入处理、上下文管理。这些由窗口库负责：

- **GLFW**：轻量，专为 OpenGL 设计，推荐。
- **SDL2**：功能更全（音频、网络），适合游戏。
- **GLUT**：古老，已停止维护，不推荐。

**为什么需要函数加载库？**

OpenGL 函数指针是运行时获取的——不同操作系统、不同驱动支持的函数不同。函数加载库自动加载所有可用函数：

```c
// 手动加载（痛苦）
PFNGLGENVERTEXARRAYSPROC glGenVertexArrays =
    (PFNGLGENVERTEXARRAYSPROC)wglGetProcAddress("glGenVertexArrays");

// 用 GLEW（自动）
glewInit();
glGenVertexArrays(1, &vao);  // 直接调用
```

**为什么需要数学库？**

OpenGL 着色器需要矩阵运算（MVP 变换），但 OpenGL 不提供数学函数。GLM 是专为 OpenGL 设计的数学库，语法与 GLSL 一致：

```cpp
// GLM
glm::mat4 model = glm::translate(glm::mat4(1.0f), glm::vec3(1, 2, 3));

// 对应的 GLSL
mat4 model = mat4(1.0);
model = translate(model, vec3(1, 2, 3));
```

**库的安装**：

- **Windows**：用 vcpkg（`vcpkg install glfw3 glew glm`）或手动下载。
- **Linux**：`sudo apt install libglfw3-dev libglew-dev libglm-dev`。
- **macOS**：`brew install glfw glew glm`。

### 例子

**CMake 项目配置**：

```cmake
cmake_minimum_required(VERSION 3.10)
project(OpenGLTutorial)

set(CMAKE_CXX_STANDARD 17)

# 寻找包
find_package(glfw3 REQUIRED)
find_package(GLEW REQUIRED)
find_package(glm REQUIRED)

# 可执行文件
add_executable(opengl_demo main.cpp)

# 链接库
target_link_libraries(opengl_demo glfw GLEW::GLEW)
if(APPLE)
    target_link_libraries(opengl_demo "-framework OpenGL")
else()
    target_link_libraries(opengl_demo GL)
endif()
```

**完整的最小程序**：

```cpp
#include <GL/glew.h>
#include <GLFW/glfw3.h>
#include <iostream>

// 窗口大小回调
void framebuffer_size_callback(GLFWwindow* window, int width, int height) {
    glViewport(0, 0, width, height);
}

// 输入处理
void process_input(GLFWwindow* window) {
    if (glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS) {
        glfwSetWindowShouldClose(window, true);
    }
}

int main() {
    // 初始化 GLFW
    if (!glfwInit()) {
        std::cerr << "Failed to initialize GLFW" << std::endl;
        return -1;
    }

    // 配置 OpenGL 版本（3.3 核心模式）
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
#ifdef __APPLE__
    glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);  // macOS 必需
#endif

    // 创建窗口
    GLFWwindow* window = glfwCreateWindow(800, 600, "OpenGL Tutorial", nullptr, nullptr);
    if (!window) {
        std::cerr << "Failed to create GLFW window" << std::endl;
        glfwTerminate();
        return -1;
    }
    glfwMakeContextCurrent(window);

    // 设置回调
    glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);

    // 初始化 GLEW
    glewExperimental = GL_TRUE;
    if (glewInit() != GLEW_OK) {
        std::cerr << "Failed to initialize GLEW" << std::endl;
        return -1;
    }

    // 设置视口
    glViewport(0, 0, 800, 600);

    // 主循环
    while (!glfwWindowShouldClose(window)) {
        process_input(window);

        // 清屏
        glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT);

        // TODO: 绘制

        glfwSwapBuffers(window);
        glfwPollEvents();
    }

    glfwTerminate();
    return 0;
}
```

### 总结

- OpenGL 开发需要三类工具：窗口库（GLFW）、函数加载库（GLEW）、数学库（GLM）。
- **GLFW** 轻量专为 OpenGL，**SDL2** 功能更全，**GLUT** 已过时。
- **函数加载库**自动加载 OpenGL 扩展函数，避免手动 wglGetProcAddress。
- **GLM** 语法与 GLSL 一致，是 OpenGL 数学标准库。
- **macOS** 需要 `GLFW_OPENGL_FORWARD_COMPAT` 标志。
- **常见坑**：忘记 `glewExperimental = GL_TRUE` 导致扩展加载失败；macOS 不设 forward compat 导致上下文创建失败。

---

## 第 03 讲 · 窗口与 OpenGL 上下文

### 概念

**窗口（Window）** 是 OpenGL 渲染的目标区域，由窗口库（GLFW）创建。

**OpenGL 上下文（Context）** 是 OpenGL 的状态机实例——包含所有 OpenGL 状态（当前着色器、纹理、缓冲区等）。每个窗口关联一个上下文，渲染命令作用于"当前上下文"。

理解上下文是掌握 OpenGL 的关键——所有 OpenGL 命令都作用于当前上下文，多窗口渲染需切换上下文。

### 原理

**上下文的作用**：

```c
// 创建窗口时隐式创建上下文
GLFWwindow* window = glfwCreateWindow(...);
glfwMakeContextCurrent(window);  // 设为当前上下文

// 后续 OpenGL 命令作用于该上下文
glClearColor(...);
glClear(...);
```

**双缓冲（Double Buffering）**：

OpenGL 默认使用双缓冲——前缓冲显示在屏幕上，后缓冲用于绘制。绘制完成后交换：

```c
glfwSwapBuffers(window);  // 交换前后缓冲
```

单缓冲会导致撕裂——屏幕刷新时正在绘制，看到半成品。双缓冲避免此问题。

**垂直同步（VSync）**：

VSync 同步缓冲交换与显示器刷新，避免撕裂与不必要的渲染：

```c
glfwSwapInterval(1);  // 启用 VSync（1 = 与刷新率同步）
glfwSwapInterval(0);  // 禁用 VSync（无限帧率）
```

**窗口属性**：

```c
// 创建窗口时的提示
glfwWindowHint(GLFW_RESIZABLE, GLFW_FALSE);      // 禁止调整大小
glfwWindowHint(GLFW_DECORATED, GLFW_FALSE);      // 无边框
glfwWindowHint(GLFW_VISIBLE, GLFW_FALSE);        // 隐藏窗口
glfwWindowHint(GLFW_SAMPLES, 4);                 // 4x MSAA 抗锯齿
glfwWindowHint(GLFW_MAXIMIZED, GLFW_TRUE);       // 最大化
```

**全屏模式**：

```c
// 获取主显示器
GLFWmonitor* monitor = glfwGetPrimaryMonitor();
const GLFWvidmode* mode = glfwGetVideoMode(monitor);

// 全屏窗口
GLFWwindow* window = glfwCreateWindow(
    mode->width, mode->height, "OpenGL Fullscreen", monitor, nullptr);
```

**多窗口**：

```c
GLFWwindow* window1 = glfwCreateWindow(400, 300, "Window 1", nullptr, nullptr);
GLFWwindow* window2 = glfwCreateWindow(400, 300, "Window 2", nullptr, nullptr);

// 渲染 window1
glfwMakeContextCurrent(window1);
glClearColor(1, 0, 0, 1);
glClear(GL_COLOR_BUFFER_BIT);
glfwSwapBuffers(window1);

// 渲染 window2
glfwMakeContextCurrent(window2);
glClearColor(0, 1, 0, 1);
glClear(GL_COLOR_BUFFER_BIT);
glfwSwapBuffers(window2);
```

**输入处理**：

GLFW 提供两种输入方式：

1. **轮询（Polling）**：每帧查询状态。
2. **回调（Callback）**：事件触发时调用。

```c
// 轮询
if (glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS) {
    // ESC 按下
}

// 回调
void key_callback(GLFWwindow* window, int key, int scancode, int action, int mods) {
    if (key == GLFW_KEY_ESCAPE && action == GLFW_PRESS) {
        glfwSetWindowShouldClose(window, true);
    }
}
glfwSetKeyCallback(window, key_callback);
```

### 例子

```cpp
#include <GL/glew.h>
#include <GLFW/glfw3.h>
#include <iostream>

// 全局状态
bool vsync = true;
int window_x = 100, window_y = 100;

// 键盘回调
void key_callback(GLFWwindow* window, int key, int scancode, int action, int mods) {
    if (action != GLFW_PRESS) return;

    switch (key) {
        case GLFW_KEY_ESCAPE:
            glfwSetWindowShouldClose(window, true);
            break;
        case GLFW_KEY_V:  // 切换 VSync
            vsync = !vsync;
            glfwSwapInterval(vsync ? 1 : 0);
            std::cout << "VSync: " << (vsync ? "ON" : "OFF") << std::endl;
            break;
        case GLFW_KEY_F:  // 切换全屏
            // 简化：实际需保存窗口状态
            break;
    }
}

// 鼠标移动回调
void cursor_pos_callback(GLFWwindow* window, double xpos, double ypos) {
    std::cout << "Mouse: " << xpos << ", " << ypos << std::endl;
}

// 窗口大小回调
void framebuffer_size_callback(GLFWwindow* window, int width, int height) {
    glViewport(0, 0, width, height);
    std::cout << "Window resized: " << width << "x" << height << std::endl;
}

int main() {
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
    glfwWindowHint(GLFW_SAMPLES, 4);  // 4x MSAA

    GLFWwindow* window = glfwCreateWindow(800, 600, "Window & Context", nullptr, nullptr);
    glfwMakeContextCurrent(window);
    glfwSwapInterval(1);  // VSync

    // 设置回调
    glfwSetKeyCallback(window, key_callback);
    glfwSetCursorPosCallback(window, cursor_pos_callback);
    glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);

    glewInit();
    glViewport(0, 0, 800, 600);

    while (!glfwWindowShouldClose(window)) {
        glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT);

        glfwSwapBuffers(window);
        glfwPollEvents();
    }

    glfwTerminate();
    return 0;
}
```

### 总结

- 窗口是渲染目标，上下文是 OpenGL 状态机实例。
- **双缓冲**避免撕裂：后缓冲绘制，交换到前缓冲显示。
- **VSync** 同步交换与刷新率，`glfwSwapInterval(1)` 启用。
- **多窗口**需切换当前上下文。
- **输入**：轮询（`glfwGetKey`）或回调（`glfwSetKeyCallback`）。
- **常见坑**：忘记 `glfwMakeContextCurrent` 导致 OpenGL 命令无效；单缓冲导致撕裂。

---

## 第 04 讲 · 第一个三角形

### 概念

绘制三角形是 OpenGL 的"Hello World"——它涉及顶点数据、着色器、缓冲区、绘制命令的完整流程。本讲从零开始绘制一个彩色三角形，建立现代 OpenGL 的完整认知。

### 原理

**绘制三角形的完整流程**：

```
1. 定义顶点数据（位置 + 颜色）
2. 创建 VAO、VBO
3. 上传顶点数据到 VBO
4. 设置顶点属性指针
5. 编译顶点着色器
6. 编译片段着色器
7. 链接着色器程序
8. 主循环：清屏 → 使用程序 → 绑定 VAO → 绘制
```

**顶点数据**：

每个顶点包含位置和颜色：

```c
float vertices[] = {
    // 位置              // 颜色
    -0.5f, -0.5f, 0.0f,  1.0f, 0.0f, 0.0f,  // 左下，红
     0.5f, -0.5f, 0.0f,  0.0f, 1.0f, 0.0f,  // 右下，绿
     0.0f,  0.5f, 0.0f,  0.0f, 0.0f, 1.0f   // 顶部，蓝
};
```

**VAO（Vertex Array Object）**：

VAO 存储顶点属性指针的配置。绑定 VAO 后，所有 `glVertexAttribPointer` 调用都记录在 VAO 中。绘制时只需绑定 VAO，无需重新配置。

**VBO（Vertex Buffer Object）**：

VBO 是 GPU 显存中的缓冲区，存储顶点数据。上传一次，多次绘制。

**顶点属性指针**：

告诉 OpenGL 如何解析顶点数据：

```c
// 位置属性
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);

// 颜色属性
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)(3 * sizeof(float)));
glEnableVertexAttribArray(1);
```

参数：索引、分量数、类型、是否归一化、步长、偏移。

**顶点着色器**：

```glsl
#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aColor;

out vec3 vertexColor;

void main() {
    gl_Position = vec4(aPos, 1.0);
    vertexColor = aColor;
}
```

**片段着色器**：

```glsl
#version 330 core
in vec3 vertexColor;
out vec4 FragColor;

void main() {
    FragColor = vec4(vertexColor, 1.0);
}
```

**着色器编译流程**：

```c
// 1. 创建着色器对象
GLuint vertex_shader = glCreateShader(GL_VERTEX_SHADER);
// 2. 附加源码
glShaderSource(vertex_shader, 1, &vertex_src, nullptr);
// 3. 编译
glCompileShader(vertex_shader);
// 4. 检查错误
GLint success;
glGetShaderiv(vertex_shader, GL_COMPILE_STATUS, &success);
if (!success) {
    char info[512];
    glGetShaderInfoLog(vertex_shader, 512, nullptr, info);
    // 打印错误
}
// 5. 创建程序、附加着色器、链接
GLuint program = glCreateProgram();
glAttachShader(program, vertex_shader);
glAttachShader(program, fragment_shader);
glLinkProgram(program);
// 6. 删除着色器对象（已链接到程序）
glDeleteShader(vertex_shader);
glDeleteShader(fragment_shader);
```

### 例子

```cpp
#include <GL/glew.h>
#include <GLFW/glfw3.h>
#include <iostream>

const char* vertex_shader_src = R"(
#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aColor;
out vec3 vertexColor;
void main() {
    gl_Position = vec4(aPos, 1.0);
    vertexColor = aColor;
}
)";

const char* fragment_shader_src = R"(
#version 330 core
in vec3 vertexColor;
out vec4 FragColor;
void main() {
    FragColor = vec4(vertexColor, 1.0);
}
)";

// 检查着色器编译错误
void check_shader_errors(GLuint shader, const char* type) {
    GLint success;
    glGetShaderiv(shader, GL_COMPILE_STATUS, &success);
    if (!success) {
        char info[512];
        glGetShaderInfoLog(shader, 512, nullptr, info);
        std::cerr << type << " shader error: " << info << std::endl;
    }
}

int main() {
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);

    GLFWwindow* window = glfwCreateWindow(800, 600, "Triangle", nullptr, nullptr);
    glfwMakeContextCurrent(window);
    glewInit();

    // 顶点数据
    float vertices[] = {
        -0.5f, -0.5f, 0.0f,  1.0f, 0.0f, 0.0f,
         0.5f, -0.5f, 0.0f,  0.0f, 1.0f, 0.0f,
         0.0f,  0.5f, 0.0f,  0.0f, 0.0f, 1.0f
    };

    // 创建 VAO、VBO
    GLuint VAO, VBO;
    glGenVertexArrays(1, &VAO);
    glGenBuffers(1, &VBO);

    // 绑定 VAO
    glBindVertexArray(VAO);

    // 绑定 VBO，上传数据
    glBindBuffer(GL_ARRAY_BUFFER, VBO);
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

    // 设置顶点属性
    // 位置
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)0);
    glEnableVertexAttribArray(0);
    // 颜色
    glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)(3 * sizeof(float)));
    glEnableVertexAttribArray(1);

    // 解绑
    glBindBuffer(GL_ARRAY_BUFFER, 0);
    glBindVertexArray(0);

    // 编译着色器
    GLuint vertex_shader = glCreateShader(GL_VERTEX_SHADER);
    glShaderSource(vertex_shader, 1, &vertex_shader_src, nullptr);
    glCompileShader(vertex_shader);
    check_shader_errors(vertex_shader, "Vertex");

    GLuint fragment_shader = glCreateShader(GL_FRAGMENT_SHADER);
    glShaderSource(fragment_shader, 1, &fragment_shader_src, nullptr);
    glCompileShader(fragment_shader);
    check_shader_errors(fragment_shader, "Fragment");

    // 链接程序
    GLuint shader_program = glCreateProgram();
    glAttachShader(shader_program, vertex_shader);
    glAttachShader(shader_program, fragment_shader);
    glLinkProgram(shader_program);

    glDeleteShader(vertex_shader);
    glDeleteShader(fragment_shader);

    // 主循环
    while (!glfwWindowShouldClose(window)) {
        if (glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS) {
            glfwSetWindowShouldClose(window, true);
        }

        glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT);

        // 绘制三角形
        glUseProgram(shader_program);
        glBindVertexArray(VAO);
        glDrawArrays(GL_TRIANGLES, 0, 3);

        glfwSwapBuffers(window);
        glfwPollEvents();
    }

    glDeleteVertexArrays(1, &VAO);
    glDeleteBuffers(1, &VBO);
    glDeleteProgram(shader_program);

    glfwTerminate();
    return 0;
}
```

### 总结

- 绘制三角形涉及：顶点数据 → VAO/VBO → 顶点属性 → 着色器 → 绘制。
- **VAO** 存储顶点属性配置，**VBO** 存储顶点数据。
- **顶点属性指针**告诉 OpenGL 如何解析顶点数据（位置、颜色等）。
- 着色器流程：创建 → 附加源码 → 编译 → 链接程序 → 使用。
- **常见坑**：忘记 `glEnableVertexAttribArray`；着色器编译错误未检查；VBO 解绑顺序错误。

---

# 第 2 章 · 图元与着色器

第 1 章绘制了第一个三角形，但 OpenGL 的真正威力在于灵活的顶点数据管理与 GLSL 着色器编程。本章深入讲解 VAO/VBO/EBO 的使用、GLSL 顶点与片段着色器的语法、各种图元类型的绘制。掌握这些，你就能渲染任意几何体，并用着色器实现自定义视觉效果。

## 第 05 讲 · VAO、VBO 与 EBO

### 概念

**VAO（Vertex Array Object，顶点数组对象）** 存储顶点属性配置——哪些属性启用、每个属性的格式、对应的 VBO 绑定。

**VBO（Vertex Buffer Object，顶点缓冲对象）** 是 GPU 显存中的缓冲区，存储顶点数据（位置、颜色、法线、UV 等）。

**EBO（Element Buffer Object，元素缓冲对象）** 存储顶点索引，让重复顶点只存储一次，通过索引引用。绘制矩形时，4 个顶点 + 6 个索引代替 6 个顶点，节省显存与带宽。

### 原理

**为什么需要 VAO？**

没有 VAO，每次绘制都需重新设置顶点属性指针：

```c
// 没有 VAO（痛苦）
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, ...);
glEnableVertexAttribArray(0);
glDrawArrays(...);
// 每帧重复
```

VAO 记录所有属性配置，绘制时只需绑定：

```c
// 有 VAO（高效）
glBindVertexArray(VAO);
glDrawArrays(...);
// 完成
```

**VAO 存储的状态**：

- `glEnableVertexAttribArray` / `glDisableVertexAttribArray` 调用
- `glVertexAttribPointer` 的配置
- `glVertexAttribIPointer` / `glVertexAttribLPointer` 的配置
- `glVertexAttribBinding`（如果使用）
- 当前绑定的 `GL_ELEMENT_ARRAY_BUFFER`

**VBO 的使用模式**：

```c
// 1. 生成
GLuint vbo;
glGenBuffers(1, &vbo);

// 2. 绑定到 GL_ARRAY_BUFFER 目标
glBindBuffer(GL_ARRAY_BUFFER, vbo);

// 3. 上传数据
glBufferData(GL_ARRAY_BUFFER, size, data, usage);

// 4. 设置属性指针（记录在当前 VAO）
glVertexAttribPointer(...);

// 5. 解绑（可选）
glBindBuffer(GL_ARRAY_BUFFER, 0);
```

**usage 参数**：

- `GL_STATIC_DRAW`：数据不变，GPU 优化为只读。
- `GL_DYNAMIC_DRAW`：数据会改变，GPU 优化为读写。
- `GL_STREAM_DRAW`：数据每帧改变，GPU 优化为单次使用。

**EBO 的使用**：

```c
// 矩形顶点（4 个，不重复）
float vertices[] = {
     0.5f,  0.5f, 0.0f,  // 右上
     0.5f, -0.5f, 0.0f,  // 右下
    -0.5f, -0.5f, 0.0f,  // 左下
    -0.5f,  0.5f, 0.0f   // 左上
};

// 索引（两个三角形）
unsigned int indices[] = {
    0, 1, 3,  // 第一个三角形
    1, 2, 3   // 第二个三角形
};

GLuint ebo;
glGenBuffers(1, &ebo);
glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, ebo);
glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);

// 绘制
glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);
```

**EBO 的优势**：

- **节省显存**：矩形 4 顶点 + 6 索引（40 字节）vs 6 顶点（72 字节）。
- **节省带宽**：GPU 从显存读取的数据更少。
- **顶点复用**：复杂模型（如球体）大量顶点被多个三角形共享。

**多个 VBO 的组织**：

一个 VAO 可关联多个 VBO——位置、颜色、法线分别存储在不同 VBO：

```c
glBindVertexArray(VAO);

// 位置 VBO
glBindBuffer(GL_ARRAY_BUFFER, pos_vbo);
glBufferData(GL_ARRAY_BUFFER, pos_size, pos_data, GL_STATIC_DRAW);
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 0, (void*)0);
glEnableVertexAttribArray(0);

// 颜色 VBO
glBindBuffer(GL_ARRAY_BUFFER, color_vbo);
glBufferData(GL_ARRAY_BUFFER, color_size, color_data, GL_STATIC_DRAW);
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 0, (void*)0);
glEnableVertexAttribArray(1);
```

或用单个交织（interleaved）VBO，所有属性连续存储（第 04 讲示例）。

### 例子

```cpp
#include <GL/glew.h>
#include <GLFW/glfw3.h>

const char* vs = R"(#version 330 core
layout(location=0) in vec3 aPos;
layout(location=1) in vec3 aColor;
out vec3 vColor;
void main() {
    gl_Position = vec4(aPos, 1.0);
    vColor = aColor;
})";

const char* fs = R"(#version 330 core
in vec3 vColor;
out vec4 FragColor;
void main() { FragColor = vec4(vColor, 1.0); })";

GLuint compile(GLenum t, const char* s) {
    GLuint sh = glCreateShader(t);
    glShaderSource(sh, 1, &s, nullptr);
    glCompileShader(sh);
    return sh;
}

int main() {
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
    GLFWwindow* w = glfwCreateWindow(800, 600, "VAO VBO EBO", nullptr, nullptr);
    glfwMakeContextCurrent(w);
    glewInit();

    // 矩形顶点
    float vertices[] = {
         0.5f,  0.5f, 0.0f,  1.0f, 0.0f, 0.0f,  // 右上 红
         0.5f, -0.5f, 0.0f,  0.0f, 1.0f, 0.0f,  // 右下 绿
        -0.5f, -0.5f, 0.0f,  0.0f, 0.0f, 1.0f,  // 左下 蓝
        -0.5f,  0.5f, 0.0f,  1.0f, 1.0f, 0.0f   // 左上 黄
    };
    unsigned int indices[] = {0, 1, 3, 1, 2, 3};

    GLuint VAO, VBO, EBO;
    glGenVertexArrays(1, &VAO);
    glGenBuffers(1, &VBO);
    glGenBuffers(1, &EBO);

    glBindVertexArray(VAO);

    glBindBuffer(GL_ARRAY_BUFFER, VBO);
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

    glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
    glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);

    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)0);
    glEnableVertexAttribArray(0);
    glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)(3 * sizeof(float)));
    glEnableVertexAttribArray(1);

    GLuint prog = glCreateProgram();
    glAttachShader(prog, compile(GL_VERTEX_SHADER, vs));
    glAttachShader(prog, compile(GL_FRAGMENT_SHADER, fs));
    glLinkProgram(prog);

    // 线框模式（演示）
    // glPolygonMode(GL_FRONT_AND_BACK, GL_LINE);

    while (!glfwWindowShouldClose(w)) {
        if (glfwGetKey(w, GLFW_KEY_ESCAPE) == GLFW_PRESS)
            glfwSetWindowShouldClose(w, true);

        glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT);

        glUseProgram(prog);
        glBindVertexArray(VAO);
        glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);

        glfwSwapBuffers(w);
        glfwPollEvents();
    }

    glDeleteVertexArrays(1, &VAO);
    glDeleteBuffers(1, &VBO);
    glDeleteBuffers(1, &EBO);
    glfwTerminate();
    return 0;
}
```

### 总结

- VAO 存储顶点属性配置，VBO 存储顶点数据，EBO 存储索引。
- VAO 让绘制时只需绑定，无需重新配置属性。
- **EBO 节省显存与带宽**：矩形 4 顶点 + 6 索引 vs 6 顶点。
- **usage**：STATIC_DRAW（不变）、DYNAMIC_DRAW（偶尔变）、STREAM_DRAW（每帧变）。
- **多 VBO**：属性分开存储，或用交织 VBO 连续存储。
- **常见坑**：EBO 必须在 VAO 绑定时绑定，否则不被记录；解绑 VAO 前解绑 EBO 会导致 EBO 丢失。

---

## 第 06 讲 · GLSL 顶点着色器

### 概念

**顶点着色器（Vertex Shader）** 是渲染管线的第一个可编程阶段，对每个顶点执行一次。它的主要职责是计算顶点的**裁剪空间位置**（`gl_Position`），并可传递数据到片段着色器。

顶点着色器是 3D 图形的核心——所有变换（模型、视图、投影）都在这里完成。

### 原理

**GLSL（OpenGL Shading Language）** 语法类似 C，专为 GPU 设计。版本声明：

```glsl
#version 330 core
```

**输入与输出**：

```glsl
#version 330 core
layout (location = 0) in vec3 aPos;      // 顶点属性输入
layout (location = 1) in vec2 aTexCoord;

out vec2 TexCoord;                        // 输出到片段着色器

void main() {
    gl_Position = vec4(aPos, 1.0);        // 必须设置：裁剪空间位置
    TexCoord = aTexCoord;                 // 传递 UV
}
```

- `in`：从顶点数组接收的属性。
- `out`：传递到下一阶段（片段着色器）。
- `gl_Position`：内置输出，裁剪空间位置（vec4）。

**Uniform 变量**：

Uniform 是从 CPU 设置的全局变量，所有顶点共享：

```glsl
uniform mat4 model;
uniform mat4 view;
uniform mat4 projection;

void main() {
    gl_Position = projection * view * model * vec4(aPos, 1.0);
}
```

CPU 端设置：

```c
GLint loc = glGetUniformLocation(program, "model");
glUniformMatrix4fv(loc, 1, GL_FALSE, &model[0][0]);
```

**GLSL 数据类型**：

| 类型 | 说明 |
|------|------|
| `int, float, bool` | 标量 |
| `vec2, vec3, vec4` | 浮点向量 |
| `ivec2, ivec3, ivec4` | 整数向量 |
| `mat2, mat3, mat4` | 矩阵 |
| `sampler2D` | 2D 纹理采样器 |

**Swizzling**：

```glsl
vec4 v = vec4(1, 2, 3, 4);
vec2 a = v.xy;    // (1, 2)
vec3 b = v.xyz;   // (1, 2, 3)
vec2 c = v.xx;    // (1, 1) - 可重复
v.x = 5;          // 修改分量
```

**内置变量**：

| 变量 | 类型 | 说明 |
|------|------|------|
| `gl_Position` | vec4 | 输出裁剪空间位置 |
| `gl_PointSize` | float | 点的大小（像素） |
| `gl_VertexID` | int | 顶点索引 |
| `gl_InstanceID` | int | 实例索引（实例化） |

**内置函数**：

```glsl
// 数学
float sin(float), cos(float), tan(float);
float pow(float, float), sqrt(float), exp(float), log(float);
float abs(float), floor(float), ceil(float), fract(float);
float min(float, float), max(float, float), clamp(float, min, max);
float mix(float, float, float);  // 线性插值
float smoothstep(float, float, float);

// 几何
float length(vec3);
float distance(vec3, vec3);
float dot(vec3, vec3);
vec3 cross(vec3, vec3);
vec3 normalize(vec3);
vec3 reflect(vec3, vec3);  // 反射
vec3 refract(vec3, vec3, float);  // 折射
```

**顶点着色器的典型职责**：

1. **计算位置**：`gl_Position = MVP * vec4(pos, 1.0)`
2. **传递属性**：UV、颜色、法线传递到片段着色器
3. **计算法线**：`normal = mat3(transpose(inverse(model))) * aNormal`
4. **世界空间位置**：`worldPos = model * vec4(pos, 1.0)`

### 例子

```glsl
#version 330 core
// 完整顶点着色器：变换 + 传递属性

layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aNormal;
layout (location = 2) in vec2 aTexCoord;

out vec3 FragPos;       // 世界空间位置
out vec3 Normal;        // 世界空间法线
out vec2 TexCoord;

uniform mat4 model;
uniform mat4 view;
uniform mat4 projection;
uniform mat3 normalMatrix;  // transpose(inverse(model))

void main() {
    // 世界空间位置
    FragPos = vec3(model * vec4(aPos, 1.0));

    // 世界空间法线
    Normal = normalMatrix * aNormal;

    // 传递 UV
    TexCoord = aTexCoord;

    // 裁剪空间位置
    gl_Position = projection * view * vec4(FragPos, 1.0);
}
```

**CPU 端设置 uniform**：

```cpp
#include <glm/glm.hpp>
#include <glm/gtc/matrix_transform.hpp>
#include <glm/gtc/type_ptr.hpp>

// 计算矩阵
glm::mat4 model = glm::translate(glm::mat4(1.0f), glm::vec3(1, 2, 3));
glm::mat4 view = glm::lookAt(glm::vec3(0, 0, 5), glm::vec3(0, 0, 0), glm::vec3(0, 1, 0));
glm::mat4 projection = glm::perspective(glm::radians(45.0f), 800.0f / 600.0f, 0.1f, 100.0f);
glm::mat3 normalMatrix = glm::mat3(glm::transpose(glm::inverse(model)));

// 设置 uniform
glUseProgram(program);
glUniformMatrix4fv(glGetUniformLocation(program, "model"), 1, GL_FALSE, glm::value_ptr(model));
glUniformMatrix4fv(glGetUniformLocation(program, "view"), 1, GL_FALSE, glm::value_ptr(view));
glUniformMatrix4fv(glGetUniformLocation(program, "projection"), 1, GL_FALSE, glm::value_ptr(projection));
glUniformMatrix3fv(glGetUniformLocation(program, "normalMatrix"), 1, GL_FALSE, glm::value_ptr(normalMatrix));
```

### 总结

- 顶点着色器对每个顶点执行一次，计算 `gl_Position`（裁剪空间位置）。
- **in/out**：接收顶点属性，传递数据到片段着色器。
- **uniform**：CPU 设置的全局变量，所有顶点共享。
- **Swizzling**：`v.xy`、`v.xyz` 灵活访问向量分量。
- **典型职责**：计算位置、传递属性、计算法线、世界空间位置。
- **常见坑**：忘记设置 `gl_Position`；法线矩阵用 model 而非 transpose(inverse(model))。

---

## 第 07 讲 · GLSL 片段着色器

### 概念

**片段着色器（Fragment Shader）** 对每个片段（像素候选）执行一次，计算最终颜色。它是 OpenGL 中最"创造性"的阶段——光照、纹理、后处理、特效都在这里实现。

片段着色器决定了像素的颜色，是视觉效果的灵魂。

### 原理

**片段着色器结构**：

```glsl
#version 330 core
in vec3 FragPos;       // 从顶点着色器接收
in vec3 Normal;
in vec2 TexCoord;

out vec4 FragColor;    // 输出颜色

uniform sampler2D texture1;
uniform vec3 lightColor;

void main() {
    vec3 color = texture(texture1, TexCoord).rgb;
    FragColor = vec4(color * lightColor, 1.0);
}
```

**片段着色器的输入**：

1. **从顶点着色器**：`in` 变量，光栅化后插值。
2. **Uniform**：全局变量。
3. **内置**：`gl_FragCoord`（像素坐标）、`gl_FrontFacing`（是否正面）。

**片段着色器的输出**：

- `out vec4 FragColor`：最终颜色。
- 可输出到多个目标（MRT，Multiple Render Targets）。

**插值**：

顶点着色器输出的 `out` 变量在三角形内**插值**——每个片段得到基于位置的加权平均：

```glsl
// 顶点着色器
out vec3 vColor;
vColor = aColor;  // 三个顶点不同颜色

// 片段着色器
in vec3 vColor;
// vColor 是三个顶点颜色的插值
```

**插值限定符**：

```glsl
flat out int vID;        // 不插值，用第一个顶点的值
smooth out vec3 vColor;  // 默认，透视正确插值
noperspective out vec2 uv;  // 线性插值（非透视正确）
```

**纹理采样**：

```glsl
uniform sampler2D tex;
vec4 color = texture(tex, TexCoord);  // 采样
```

**discard**：

```glsl
vec4 color = texture(tex, TexCoord);
if (color.a < 0.1) discard;  // 丢弃透明片段
```

`discard` 类似 `return`，但不写入任何缓冲。

**内置变量**：

| 变量 | 类型 | 说明 |
|------|------|------|
| `gl_FragCoord` | vec4 | 窗口坐标 (x, y, z, 1/w) |
| `gl_FrontFacing` | bool | 是否正面 |
| `gl_FragDepth` | float | 深度值（可修改） |

**gl_FragCoord 的用途**：

```glsl
// 屏幕空间效果
vec2 uv = gl_FragCoord.xy / resolution;
// 后处理、屏幕特效
```

**多渲染目标（MRT）**：

```glsl
layout (location = 0) out vec4 FragColor;
layout (location = 1) out vec4 BrightColor;

void main() {
    FragColor = color;
    BrightColor = length(color) > 1.0 ? color : vec4(0);
}
```

需用 `glDrawBuffers` 启用多个目标。

### 例子

```glsl
#version 330 core
// 完整片段着色器：纹理 + 光照

in vec3 FragPos;
in vec3 Normal;
in vec2 TexCoord;

out vec4 FragColor;

uniform sampler2D diffuseMap;
uniform sampler2D specularMap;

uniform vec3 lightPos;
uniform vec3 viewPos;
uniform vec3 lightColor;

uniform float shininess;

void main() {
    // 纹理采样
    vec3 diffuseColor = texture(diffuseMap, TexCoord).rgb;
    vec3 specularColor = texture(specularMap, TexCoord).rgb;

    // 环境光
    vec3 ambient = 0.1 * diffuseColor;

    // 漫反射
    vec3 N = normalize(Normal);
    vec3 L = normalize(lightPos - FragPos);
    float diff = max(dot(N, L), 0.0);
    vec3 diffuse = diff * diffuseColor * lightColor;

    // 镜面反射
    vec3 V = normalize(viewPos - FragPos);
    vec3 R = reflect(-L, N);
    float spec = pow(max(dot(V, R), 0.0), shininess);
    vec3 specular = spec * specularColor * lightColor;

    vec3 result = ambient + diffuse + specular;
    FragColor = vec4(result, 1.0);
}
```

### 总结

- 片段着色器对每个片段执行一次，计算最终颜色。
- **输入**：顶点着色器的 `out`（插值）、uniform、内置变量。
- **输出**：`out vec4 FragColor`，可多目标（MRT）。
- **插值**：顶点属性在三角形内透视正确插值。
- **discard** 丢弃片段，不写入缓冲。
- **gl_FragCoord** 用于屏幕空间效果。
- **常见坑**：忘记 `normalize` 法线；纹理未绑定导致采样错误。

---

## 第 08 讲 · 图元类型与绘制

### 概念

**图元（Primitive）** 是 OpenGL 渲染的基本形状：点、线、三角形。所有复杂 3D 模型最终都由三角形组成。OpenGL 提供多种图元类型，适应不同绘制需求。

**绘制命令** 把顶点数据转换为图元并渲染：`glDrawArrays`（直接绘制）、`glDrawElements`（索引绘制）。

### 原理

**图元类型**：

| 类型 | 说明 | 顶点数 |
|------|------|--------|
| `GL_POINTS` | 点 | 每顶点一个点 |
| `GL_LINES` | 独立线段 | 每 2 顶点一条线 |
| `GL_LINE_STRIP` | 连续线段 | n 顶点 n-1 条线 |
| `GL_LINE_LOOP` | 闭合线段 | n 顶点 n 条线 |
| `GL_TRIANGLES` | 独立三角形 | 每 3 顶点一个三角形 |
| `GL_TRIANGLE_STRIP` | 三角带 | n 顶点 n-2 个三角形 |
| `GL_TRIANGLE_FAN` | 扇形 | n 顶点 n-2 个三角形 |

**GL_TRIANGLES**：

最常用，每 3 顶点一个独立三角形：

```
顶点：v0, v1, v2, v3, v4, v5
三角形：(v0,v1,v2), (v3,v4,v5)
```

**GL_TRIANGLE_STRIP**：

三角带，相邻三角形共享边，节省顶点：

```
顶点：v0, v1, v2, v3, v4
三角形：(v0,v1,v2), (v1,v2,v3), (v2,v3,v4)
```

n 个顶点产生 n-2 个三角形，比 GL_TRIANGLES 节省近 2/3 顶点。

**GL_TRIANGLE_FAN**：

扇形，所有三角形共享第一个顶点：

```
顶点：v0, v1, v2, v3, v4
三角形：(v0,v1,v2), (v0,v2,v3), (v0,v3,v4)
```

适合绘制圆形、多边形。

**绘制命令**：

```c
// 直接绘制（用 VBO 中的顶点）
glDrawArrays(GL_TRIANGLES, 0, vertex_count);

// 索引绘制（用 EBO 中的索引）
glDrawElements(GL_TRIANGLES, index_count, GL_UNSIGNED_INT, 0);
```

**glDrawArrays 参数**：

- mode：图元类型
- first：起始顶点索引
- count：顶点数

**glDrawElements 参数**：

- mode：图元类型
- count：索引数
- type：索引类型（GL_UNSIGNED_BYTE/SHORT/INT）
- indices：索引偏移（通常 0）

**索引类型选择**：

| 类型 | 范围 | 显存 |
|------|------|------|
| GL_UNSIGNED_BYTE | 0-255 | 1 字节/索引 |
| GL_UNSIGNED_SHORT | 0-65535 | 2 字节/索引 |
| GL_UNSIGNED_INT | 0-4294967295 | 4 字节/索引 |

顶点数 < 256 用 BYTE 最省，< 65536 用 SHORT，否则用 INT。

**实例化绘制**：

```c
// 绘制 instance_count 个实例
glDrawArraysInstanced(GL_TRIANGLES, 0, vertex_count, instance_count);
glDrawElementsInstanced(GL_TRIANGLES, index_count, GL_UNSIGNED_INT, 0, instance_count);
```

每个实例可通过 `gl_InstanceID` 区分（第 25 讲详讲）。

**多绘制命令**：

```c
// 一次调用绘制多个不同范围
glMultiDrawArrays(GL_TRIANGLES, firsts, counts, draw_count);
glMultiDrawElements(GL_TRIANGLES, counts, GL_UNSIGNED_INT, indices, draw_count);
```

减少 CPU 绘制调用开销。

### 例子

```cpp
#include <GL/glew.h>
#include <GLFW/glfw3.h>
#include <cmath>

const char* vs = R"(#version 330 core
layout(location=0) in vec2 aPos;
void main() { gl_Position = vec4(aPos, 0, 1); })";

const char* fs = R"(#version 330 core
out vec4 FragColor;
uniform vec3 color;
void main() { FragColor = vec4(color, 1); })";

GLuint compile(GLenum t, const char* s) {
    GLuint sh = glCreateShader(t);
    glShaderSource(sh, 1, &s, nullptr);
    glCompileShader(sh);
    return sh;
}

int main() {
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
    GLFWwindow* w = glfwCreateWindow(800, 600, "Primitives", nullptr, nullptr);
    glfwMakeContextCurrent(w);
    glewInit();

    // 圆形顶点（用 TRIANGLE_FAN）
    const int segments = 64;
    float circle[segments * 2 + 2];
    circle[0] = 0; circle[1] = 0;  // 中心
    for (int i = 0; i <= segments; ++i) {
        float a = i * 2 * M_PI / segments;
        circle[(i + 1) * 2] = cosf(a) * 0.5f;
        circle[(i + 1) * 2 + 1] = sinf(a) * 0.5f;
    }

    GLuint VAO, VBO;
    glGenVertexArrays(1, &VAO);
    glGenBuffers(1, &VBO);
    glBindVertexArray(VAO);
    glBindBuffer(GL_ARRAY_BUFFER, VBO);
    glBufferData(GL_ARRAY_BUFFER, sizeof(circle), circle, GL_STATIC_DRAW);
    glVertexAttribPointer(0, 2, GL_FLOAT, GL_FALSE, 2 * sizeof(float), 0);
    glEnableVertexAttribArray(0);

    GLuint prog = glCreateProgram();
    glAttachShader(prog, compile(GL_VERTEX_SHADER, vs));
    glAttachShader(prog, compile(GL_FRAGMENT_SHADER, fs));
    glLinkProgram(prog);

    while (!glfwWindowShouldClose(w)) {
        if (glfwGetKey(w, GLFW_KEY_ESCAPE) == GLFW_PRESS)
            glfwSetWindowShouldClose(w, true);

        glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT);

        glUseProgram(prog);
        glBindVertexArray(VAO);

        // 绘制圆形（TRIANGLE_FAN）
        glUniform3f(glGetUniformLocation(prog, "color"), 1.0f, 0.5f, 0.2f);
        glDrawArrays(GL_TRIANGLE_FAN, 0, segments + 2);

        // 绘制圆形轮廓（LINE_LOOP）
        glUniform3f(glGetUniformLocation(prog, "color"), 1.0f, 1.0f, 1.0f);
        glDrawArrays(GL_LINE_LOOP, 1, segments);

        glfwSwapBuffers(w);
        glfwPollEvents();
    }

    glDeleteVertexArrays(1, &VAO);
    glDeleteBuffers(1, &VBO);
    glfwTerminate();
    return 0;
}
```

### 总结

- 图元类型：POINTS、LINES、LINE_STRIP/LOOP、TRIANGLES、TRIANGLE_STRIP/FAN。
- **GL_TRIANGLES** 最常用，每 3 顶点一个三角形。
- **GL_TRIANGLE_STRIP** 节省顶点，n 顶点 n-2 三角形。
- **GL_TRIANGLE_FAN** 适合圆形，所有三角形共享首顶点。
- **glDrawArrays** 直接绘制，**glDrawElements** 索引绘制。
- **索引类型**：BYTE（<256）、SHORT（<65536）、INT（更大）。
- **常见坑**：索引类型与数据不匹配；TRIANGLE_STRIP 顶点顺序错误导致背面剔除。

---

# 第 3 章 · 变换与坐标

3D 图形的核心是变换——把模型从本地空间变换到屏幕空间。本章讲解向量与矩阵基础、平移/旋转/缩放三种基本变换、MVP 变换链、坐标系与投影。掌握这些，你就能让 3D 模型在屏幕上正确显示，并理解"为什么需要 4×4 矩阵表示 3D 变换"。

## 第 09 讲 · 向量与矩阵基础

### 概念

**向量（Vector）** 是有方向和大小的量，在图形学中表示位置、方向、速度等。3D 向量 `(x, y, z)` 是最常用的形式。

**矩阵（Matrix）** 是变换的数学表示——旋转、缩放、平移都可用矩阵乘法实现。4×4 矩阵是 3D 图形的标准，能统一所有仿射变换。

### 原理

**向量运算**：

```glsl
vec3 a = vec3(1, 2, 3);
vec3 b = vec3(4, 5, 6);

vec3 sum = a + b;        // (5, 7, 9)
vec3 diff = a - b;       // (-3, -3, -3)
float dot = dot(a, b);   // 1*4 + 2*5 + 3*6 = 32（点积）
vec3 cross = cross(a, b); // 叉积，垂直于 a 和 b
float len = length(a);   // sqrt(1+4+9) = sqrt(14)
vec3 n = normalize(a);   // 单位向量 a/|a|
```

**点积的意义**：

```
a · b = |a| |b| cos(θ)
```

- 点积 > 0：两向量夹角 < 90°（同向）。
- 点积 = 0：垂直。
- 点积 < 0：夹角 > 90°（反向）。

点积用于光照计算（漫反射）、判断方向关系。

**叉积的意义**：

```
a × b 垂直于 a 和 b 所在平面
|a × b| = |a| |b| sin(θ)
```

叉积用于计算法向量（给定三角形三个顶点，叉积得到法线）。

**矩阵**：

4×4 矩阵是 3D 图形的标准：

```
[m00 m01 m02 m03]   [x]
[m10 m11 m12 m13] × [y]
[m20 m21 m22 m23]   [z]
[m30 m31 m32 m33]   [1]
```

**矩阵乘法**：

矩阵乘法不满足交换律：`A × B ≠ B × A`。变换顺序至关重要。

**齐次坐标**：

3D 点用 4D 向量 `(x, y, z, w)` 表示：

- `w = 1`：点（位置）。
- `w = 0`：方向向量（不受平移影响）。

齐次坐标让平移也能用矩阵乘法表示，统一所有仿射变换。

**矩阵的存储顺序**：

OpenGL 默认**列主序（Column-Major）**：

```
mat4 m;
// m[0] 是第一列：m[0][0], m[0][1], m[0][2], m[0][3]
// m[1] 是第二列...
```

GLM 默认也是列主序，与 OpenGL 一致。

**单位矩阵**：

```
[1 0 0 0]
[0 1 0 0]
[0 0 1 0]
[0 0 0 1]
```

不改变任何向量：`I × v = v`。

**矩阵的逆**：

逆矩阵 `M⁻¹` 满足 `M × M⁻¹ = I`。用于：

- 撤销变换：`v = M⁻¹ × (M × v)`。
- 法线变换：`normalMatrix = transpose(inverse(modelMatrix))`。

### 例子

```cpp
#include <glm/glm.hpp>
#include <glm/gtc/matrix_transform.hpp>
#include <cstdio>

int main() {
    // 向量运算
    glm::vec3 a(1, 2, 3);
    glm::vec3 b(4, 5, 6);

    printf("a + b = (%.1f, %.1f, %.1f)\n", (a+b).x, (a+b).y, (a+b).z);
    printf("dot(a, b) = %.1f\n", glm::dot(a, b));
    printf("cross(a, b) = (%.1f, %.1f, %.1f)\n",
           glm::cross(a, b).x, glm::cross(a, b).y, glm::cross(a, b).z);
    printf("length(a) = %.3f\n", glm::length(a));
    printf("normalize(a) = (%.3f, %.3f, %.3f)\n",
           glm::normalize(a).x, glm::normalize(a).y, glm::normalize(a).z);

    // 矩阵
    glm::mat4 m = glm::mat4(1.0f);  // 单位矩阵
    printf("Identity:\n");
    for (int i = 0; i < 4; ++i) {
        for (int j = 0; j < 4; ++j)
            printf("%.1f ", m[i][j]);
        printf("\n");
    }

    // 矩阵乘法
    glm::mat4 t = glm::translate(glm::mat4(1.0f), glm::vec3(1, 2, 3));
    glm::mat4 r = glm::rotate(glm::mat4(1.0f), glm::radians(45.0f), glm::vec3(0, 1, 0));
    glm::mat4 tr = t * r;  // 先旋转后平移

    // 变换点
    glm::vec4 point(1, 0, 0, 1);
    glm::vec4 transformed = tr * point;
    printf("Transformed: (%.2f, %.2f, %.2f)\n",
           transformed.x, transformed.y, transformed.z);

    // 逆矩阵
    glm::mat4 inv = glm::inverse(tr);
    glm::vec4 restored = inv * transformed;
    printf("Restored: (%.2f, %.2f, %.2f)\n", restored.x, restored.y, restored.z);

    return 0;
}
```

### 总结

- 向量表示位置/方向，点积判断角度，叉积计算法线。
- 4×4 矩阵统一所有 3D 仿射变换，齐次坐标让平移可用乘法。
- **列主序**：OpenGL 与 GLM 默认存储顺序。
- 矩阵乘法不满足交换律，顺序至关重要。
- **逆矩阵**用于撤销变换与法线变换。
- **常见坑**：混淆行主序与列主序；忘记齐次坐标 w 分量。

---

## 第 10 讲 · 平移、旋转、缩放

### 概念

平移、旋转、缩放是三种基本变换，所有复杂变换都由它们组合而成。本讲深入每种变换的矩阵形式、性质与组合规则。

### 原理

**平移矩阵 T(tx, ty, tz)**：

```
[1  0  0  tx]
[0  1  0  ty]
[0  0  1  tz]
[0  0  0  1 ]
```

平移是加法，不改变方向与大小。逆矩阵是 T(-tx, -ty, -tz)。

**旋转矩阵**：

绕 X 轴旋转 θ：

```
[1    0       0     0]
[0  cos θ  -sin θ  0]
[0  sin θ   cos θ  0]
[0    0       0     1]
```

绕 Y 轴旋转 θ：

```
[ cos θ  0  sin θ  0]
[   0    1    0    0]
[-sin θ  0  cos θ  0]
[   0    0    0    1]
```

绕 Z 轴旋转 θ：

```
[cos θ  -sin θ  0  0]
[sin θ   cos θ  0  0]
[  0       0    1  0]
[  0       0    0  1]
```

旋转保持长度与角度。逆矩阵是 R(-θ) = R(θ)的转置（正交矩阵性质）。

**缩放矩阵 S(sx, sy, sz)**：

```
[sx  0   0   0]
[0   sy  0   0]
[0   0   sz  0]
[0   0   0   1]
```

缩放改变大小。sx=sy=sz 是均匀缩放，sx≠sy≠sz 是非均匀缩放。逆矩阵是 S(1/sx, 1/sy, 1/sz)。

**绕任意点旋转**：

绕点 P 旋转 θ = T(P) × R(θ) × T(-P)：

1. T(-P)：把 P 移到原点。
2. R(θ)：绕原点旋转。
3. T(P)：移回。

**变换的组合规则**：

- 平移 + 平移 = 平移（位移相加）。
- 旋转 + 旋转 = 旋转（角度相加，同轴时）。
- 缩放 + 缩放 = 缩放（比例相乘）。
- 平移 + 旋转 ≠ 旋转 + 平移（不满足交换律）。

**变换的分解**：

任意 3D 仿射变换矩阵可分解为：

```
M = T × R × S
```

即"先缩放，再旋转，再平移"。

**GLM 实现**：

```cpp
// 平移
glm::mat4 T = glm::translate(glm::mat4(1.0f), glm::vec3(1, 2, 3));

// 旋转（弧度，轴）
glm::mat4 R = glm::rotate(glm::mat4(1.0f), glm::radians(45.0f), glm::vec3(0, 1, 0));

// 缩放
glm::mat4 S = glm::scale(glm::mat4(1.0f), glm::vec3(2, 2, 2));

// 组合：先缩放，再旋转，再平移
glm::mat4 M = T * R * S;
```

### 例子

```cpp
#include <GL/glew.h>
#include <GLFW/glfw3.h>
#include <glm/glm.hpp>
#include <glm/gtc/matrix_transform.hpp>
#include <glm/gtc/type_ptr.hpp>

const char* vs = R"(#version 330 core
layout(location=0) in vec3 aPos;
uniform mat4 transform;
void main() { gl_Position = transform * vec4(aPos, 1.0); })";

const char* fs = R"(#version 330 core
out vec4 FragColor;
void main() { FragColor = vec4(1.0, 0.5, 0.2, 1.0); })";

GLuint compile(GLenum t, const char* s) {
    GLuint sh = glCreateShader(t);
    glShaderSource(sh, 1, &s, nullptr);
    glCompileShader(sh);
    return sh;
}

int main() {
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
    GLFWwindow* w = glfwCreateWindow(800, 600, "Transforms", nullptr, nullptr);
    glfwMakeContextCurrent(w);
    glewInit();

    float vertices[] = {
         0.5f, -0.5f, 0.0f,
        -0.5f, -0.5f, 0.0f,
         0.0f,  0.5f, 0.0f
    };

    GLuint VAO, VBO;
    glGenVertexArrays(1, &VAO);
    glGenBuffers(1, &VBO);
    glBindVertexArray(VAO);
    glBindBuffer(GL_ARRAY_BUFFER, VBO);
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), 0);
    glEnableVertexAttribArray(0);

    GLuint prog = glCreateProgram();
    glAttachShader(prog, compile(GL_VERTEX_SHADER, vs));
    glAttachShader(prog, compile(GL_FRAGMENT_SHADER, fs));
    glLinkProgram(prog);

    GLint transformLoc = glGetUniformLocation(prog, "transform");

    float time = 0;
    while (!glfwWindowShouldClose(w)) {
        if (glfwGetKey(w, GLFW_KEY_ESCAPE) == GLFW_PRESS)
            glfwSetWindowShouldClose(w, true);

        time += 0.01f;

        glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT);

        glUseProgram(prog);

        // 第一个三角形：旋转
        glm::mat4 trans1 = glm::rotate(glm::mat4(1.0f), time, glm::vec3(0, 0, 1));
        glUniformMatrix4fv(transformLoc, 1, GL_FALSE, glm::value_ptr(trans1));
        glDrawArrays(GL_TRIANGLES, 0, 3);

        // 第二个三角形：缩放 + 平移
        glm::mat4 trans2 = glm::translate(glm::mat4(1.0f), glm::vec3(0.5f, 0.5f, 0));
        trans2 = glm::scale(trans2, glm::vec3(0.5f + 0.3f * sin(time), 0.5f + 0.3f * sin(time), 1));
        glUniformMatrix4fv(transformLoc, 1, GL_FALSE, glm::value_ptr(trans2));
        glDrawArrays(GL_TRIANGLES, 0, 3);

        glfwSwapBuffers(w);
        glfwPollEvents();
    }

    glDeleteVertexArrays(1, &VAO);
    glDeleteBuffers(1, &VBO);
    glfwTerminate();
    return 0;
}
```

### 总结

- 平移 T、旋转 R、缩放 S 是三种基本变换，各有对应矩阵。
- 绕任意点旋转 = T(P) × R(θ) × T(-P)。
- 变换组合不满足交换律，顺序至关重要。
- **物体变换标准模式**：`M = T(position) × R(rotation) × S(scale)`。
- **常见坑**：绕错误点旋转（未先平移到原点）；缩放导致位置偏移。

---

## 第 11 讲 · MVP 变换链

### 概念

**MVP 变换链** 是 3D 图形的核心——把顶点从模型空间变换到裁剪空间：

```
裁剪空间 = Projection × View × Model × 模型空间
```

- **Model（模型矩阵）**：模型本地 → 世界空间。
- **View（视图矩阵）**：世界 → 视图（相机）空间。
- **Projection（投影矩阵）**：视图 → 裁剪空间。

MVP 是 3D 渲染的基础，理解它就理解了"3D 物体如何显示在 2D 屏幕上"。

### 原理

**坐标系层次**：

```
本地坐标 → [Model] → 世界坐标 → [View] → 视图坐标 → [Projection] → 裁剪坐标 → [透视除法] → 标准化设备坐标 → [视口变换] → 屏幕坐标
```

**Model 矩阵**：

把模型从本地空间变换到世界空间：

```cpp
glm::mat4 model = glm::mat4(1.0f);
model = glm::translate(model, glm::vec3(1, 2, 3));      // 平移到世界位置
model = glm::rotate(model, glm::radians(45.0f), glm::vec3(0, 1, 0));  // 旋转
model = glm::scale(model, glm::vec3(2, 2, 2));          // 缩放
```

**View 矩阵**：

把世界空间变换到相机空间。View 矩阵是相机变换的逆矩阵：

```cpp
// 相机在 (0, 0, 5)，看向原点，上方向为 Y
glm::mat4 view = glm::lookAt(
    glm::vec3(0, 0, 5),    // 相机位置
    glm::vec3(0, 0, 0),    // 看向的点
    glm::vec3(0, 1, 0)     // 上方向
);
```

`lookAt` 内部构造相机的旋转与平移，然后取逆。

**Projection 矩阵**：

把视图空间变换到裁剪空间。两种投影：

**透视投影**（Perspective）：

```cpp
glm::mat4 projection = glm::perspective(
    glm::radians(45.0f),   // FOV（视野角度）
    800.0f / 600.0f,       // 宽高比
    0.1f,                  // 近平面
    100.0f                 // 远平面
);
```

透视投影模拟人眼——远处的物体看起来更小。

**正交投影**（Orthographic）：

```cpp
glm::mat4 projection = glm::ortho(
    -400.0f, 400.0f,       // 左右
    -300.0f, 300.0f,       // 下上
    0.1f, 100.0f           // 近远
);
```

正交投影无透视收缩——远处物体与近处同样大小。适合 2D 游戏、CAD。

**MVP 组合**：

```cpp
glm::mat4 mvp = projection * view * model;
```

注意顺序：从右到左应用（先 Model，再 View，再 Projection）。

**在着色器中应用**：

```glsl
uniform mat4 mvp;

void main() {
    gl_Position = mvp * vec4(aPos, 1.0);
}
```

或分开传递：

```glsl
uniform mat4 model;
uniform mat4 view;
uniform mat4 projection;

void main() {
    gl_Position = projection * view * model * vec4(aPos, 1.0);
}
```

分开传递更灵活（如需世界空间位置计算光照）。

**透视除法**：

裁剪坐标 `(x, y, z, w)` 经过透视除法变为标准化设备坐标 `(x/w, y/w, z/w)`，范围 [-1, 1]。OpenGL 自动执行此步骤。

**视口变换**：

NDC `[-1, 1]` 映射到屏幕像素 `[0, width] × [0, height]`，由 `glViewport` 设置：

```c
glViewport(0, 0, 800, 600);
```

### 例子

```cpp
#include <GL/glew.h>
#include <GLFW/glfw3.h>
#include <glm/glm.hpp>
#include <glm/gtc/matrix_transform.hpp>
#include <glm/gtc/type_ptr.hpp>

const char* vs = R"(#version 330 core
layout(location=0) in vec3 aPos;
uniform mat4 model, view, projection;
void main() { gl_Position = projection * view * model * vec4(aPos, 1.0); })";

const char* fs = R"(#version 330 core
out vec4 FragColor;
void main() { FragColor = vec4(1.0, 0.5, 0.2, 1.0); })";

GLuint compile(GLenum t, const char* s) {
    GLuint sh = glCreateShader(t);
    glShaderSource(sh, 1, &s, nullptr);
    glCompileShader(sh);
    return sh;
}

// 立方体顶点
float cube[] = {
    -0.5f,-0.5f,-0.5f,  0.5f,-0.5f,-0.5f,  0.5f, 0.5f,-0.5f,  0.5f, 0.5f,-0.5f, -0.5f, 0.5f,-0.5f, -0.5f,-0.5f,-0.5f,
    -0.5f,-0.5f, 0.5f,  0.5f,-0.5f, 0.5f,  0.5f, 0.5f, 0.5f,  0.5f, 0.5f, 0.5f, -0.5f, 0.5f, 0.5f, -0.5f,-0.5f, 0.5f,
    -0.5f, 0.5f, 0.5f, -0.5f, 0.5f,-0.5f, -0.5f,-0.5f,-0.5f, -0.5f,-0.5f,-0.5f, -0.5f,-0.5f, 0.5f, -0.5f, 0.5f, 0.5f,
     0.5f, 0.5f, 0.5f,  0.5f, 0.5f,-0.5f,  0.5f,-0.5f,-0.5f,  0.5f,-0.5f,-0.5f,  0.5f,-0.5f, 0.5f,  0.5f, 0.5f, 0.5f,
    -0.5f,-0.5f,-0.5f,  0.5f,-0.5f,-0.5f,  0.5f,-0.5f, 0.5f,  0.5f,-0.5f, 0.5f, -0.5f,-0.5f, 0.5f, -0.5f,-0.5f,-0.5f,
    -0.5f, 0.5f,-0.5f,  0.5f, 0.5f,-0.5f,  0.5f, 0.5f, 0.5f,  0.5f, 0.5f, 0.5f, -0.5f, 0.5f, 0.5f, -0.5f, 0.5f,-0.5f
};

int main() {
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
    GLFWwindow* w = glfwCreateWindow(800, 600, "MVP", nullptr, nullptr);
    glfwMakeContextCurrent(w);
    glewInit();

    glEnable(GL_DEPTH_TEST);  // 启用深度测试

    GLuint VAO, VBO;
    glGenVertexArrays(1, &VAO);
    glGenBuffers(1, &VBO);
    glBindVertexArray(VAO);
    glBindBuffer(GL_ARRAY_BUFFER, VBO);
    glBufferData(GL_ARRAY_BUFFER, sizeof(cube), cube, GL_STATIC_DRAW);
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), 0);
    glEnableVertexAttribArray(0);

    GLuint prog = glCreateProgram();
    glAttachShader(prog, compile(GL_VERTEX_SHADER, vs));
    glAttachShader(prog, compile(GL_FRAGMENT_SHADER, fs));
    glLinkProgram(prog);

    GLint modelLoc = glGetUniformLocation(prog, "model");
    GLint viewLoc = glGetUniformLocation(prog, "view");
    GLint projLoc = glGetUniformLocation(prog, "projection");

    float time = 0;
    while (!glfwWindowShouldClose(w)) {
        if (glfwGetKey(w, GLFW_KEY_ESCAPE) == GLFW_PRESS)
            glfwSetWindowShouldClose(w, true);

        time += 0.01f;

        glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

        glUseProgram(prog);

        // Model：旋转
        glm::mat4 model = glm::rotate(glm::mat4(1.0f), time, glm::vec3(0.5f, 1, 0));
        glUniformMatrix4fv(modelLoc, 1, GL_FALSE, glm::value_ptr(model));

        // View：相机
        glm::mat4 view = glm::lookAt(glm::vec3(0, 0, 3), glm::vec3(0, 0, 0), glm::vec3(0, 1, 0));
        glUniformMatrix4fv(viewLoc, 1, GL_FALSE, glm::value_ptr(view));

        // Projection：透视
        glm::mat4 projection = glm::perspective(glm::radians(45.0f), 800.0f / 600.0f, 0.1f, 100.0f);
        glUniformMatrix4fv(projLoc, 1, GL_FALSE, glm::value_ptr(projection));

        glDrawArrays(GL_TRIANGLES, 0, 36);

        glfwSwapBuffers(w);
        glfwPollEvents();
    }

    glDeleteVertexArrays(1, &VAO);
    glDeleteBuffers(1, &VBO);
    glfwTerminate();
    return 0;
}
```

### 总结

- MVP 变换链：本地 → 世界 → 视图 → 裁剪，由 Model/View/Projection 矩阵完成。
- **Model**：物体本地 → 世界（位置、旋转、缩放）。
- **View**：世界 → 相机，用 `lookAt` 构造。
- **Projection**：透视（远小近大）或正交（无透视）。
- **顺序**：`projection × view × model`，从右到左应用。
- **常见坑**：忘记启用深度测试导致遮挡错误；FOV 过大导致畸变。

---

## 第 12 讲 · 坐标系与投影

### 概念

OpenGL 渲染涉及多个坐标系：本地、世界、视图、裁剪、NDC、屏幕。理解这些坐标系的转换是 3D 图形的基础。

**投影** 把 3D 视图空间映射到 2D 裁剪空间，分透视与正交两种。

### 原理

**坐标系层次**：

```
1. 本地坐标（Local/Object Space）：模型自身坐标，原点在模型中心。
2. 世界坐标（World Space）：场景全局坐标，所有物体统一参考系。
3. 视图坐标（View/Eye Space）：相机视角坐标，相机在原点。
4. 裁剪坐标（Clip Space）：投影后的坐标，[-w, w] 范围。
5. 标准化设备坐标（NDC）：透视除法后，[-1, 1] 范围。
6. 屏幕坐标（Screen Space）：像素坐标，[0, width] × [0, height]。
```

**各坐标系的转换**：

```
本地 → [Model] → 世界 → [View] → 视图 → [Projection] → 裁剪 → [透视除法] → NDC → [视口变换] → 屏幕
```

**透视投影矩阵**：

```
[f/aspect  0    0              0            ]
[0         f    0              0            ]    f = 1/tan(FOV/2)
[0         0   (f+n)/(n-f)    2fn/(n-f)    ]    n=近, f=远
[0         0   -1              0            ]
```

透视投影的 w 分量是 -z（视图空间深度），透视除法后远处的物体被压缩。

**正交投影矩阵**：

```
[2/(r-l)    0         0            -(r+l)/(r-l)  ]
[0          2/(t-b)   0            -(t+b)/(t-b)  ]
[0          0         -2/(f-n)     -(f+n)/(f-n)  ]
[0          0         0            1             ]
```

正交投影的 w 分量始终为 1，无透视除法效果。

**深度范围**：

OpenGL 默认深度范围 [-1, 1]（NDC 的 z）。`glDepthRange` 可修改：

```c
glDepthRange(0, 1);  // 改为 [0, 1]
```

**视口变换**：

NDC `[-1, 1]` 映射到屏幕 `[0, width] × [0, height]`：

```c
glViewport(x, y, width, height);
glDepthRange(near, far);  // 深度范围
```

**深度缓冲**：

每个像素存储深度值（z），绘制时比较：

```c
glEnable(GL_DEPTH_TEST);
glDepthFunc(GL_LESS);  // 默认：新像素 z < 已有 z 才绘制
```

**深度冲突（Z-fighting）**：

两个面距离过近时，深度缓冲精度不足，导致闪烁。解决方法：

1. 增大近平面（避免近处精度浪费）。
2. 使用 24 位或 32 位深度缓冲。
3. 用 `glPolygonOffset` 偏移共面多边形。

**右手坐标系**：

OpenGL 默认右手坐标系：

- +X 向右
- +Y 向上
- +Z 朝向观察者（屏幕外）

**左手 vs 右手**：

DirectX 用左手坐标系（+Z 朝向远处），OpenGL 用右手。转换时需注意 Z 翻转。

### 例子

```cpp
#include <GL/glew.h>
#include <GLFW/glfw3.h>
#include <glm/glm.hpp>
#include <glm/gtc/matrix_transform.hpp>
#include <glm/gtc/type_ptr.hpp>

// 演示透视与正交投影对比
int main() {
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
    GLFWwindow* w = glfwCreateWindow(800, 600, "Projections", nullptr, nullptr);
    glfwMakeContextCurrent(w);
    glewInit();

    glEnable(GL_DEPTH_TEST);

    // ...（VBO/着色器初始化省略，参考第 11 讲）

    bool perspective = true;
    while (!glfwWindowShouldClose(w)) {
        if (glfwGetKey(w, GLFW_KEY_ESCAPE) == GLFW_PRESS)
            glfwSetWindowShouldClose(w, true);
        if (glfwGetKey(w, GLFW_KEY_SPACE) == GLFW_PRESS)
            perspective = !perspective;

        glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

        // ...（绑定着色器与 VAO）

        glm::mat4 view = glm::lookAt(glm::vec3(0, 0, 5), glm::vec3(0, 0, 0), glm::vec3(0, 1, 0));

        glm::mat4 projection;
        if (perspective) {
            projection = glm::perspective(glm::radians(45.0f), 800.0f / 600.0f, 0.1f, 100.0f);
        } else {
            projection = glm::ortho(-2.0f, 2.0f, -1.5f, 1.5f, 0.1f, 100.0f);
        }

        // 绘制多个不同深度的立方体
        for (int i = 0; i < 5; ++i) {
            glm::mat4 model = glm::translate(glm::mat4(1.0f), glm::vec3(-3 + i * 1.5f, 0, -i * 0.5f));
            // ...（设置 MVP，绘制）
        }

        glfwSwapBuffers(w);
        glfwPollEvents();
    }

    glfwTerminate();
    return 0;
}
```

### 总结

- 坐标系层次：本地 → 世界 → 视图 → 裁剪 → NDC → 屏幕。
- **透视投影**：远小近大，w = -z，透视除法。
- **正交投影**：无透视收缩，w = 1，适合 2D/CAD。
- **深度缓冲**：存储每像素深度，避免遮挡错误。
- **深度冲突**：面距过近导致闪烁，增大近平面或用 PolygonOffset。
- **右手坐标系**：OpenGL 默认，+Z 朝向观察者。
- **常见坑**：近平面过小导致深度精度浪费；忘记启用深度测试。

---

# 第 4 章 · 纹理与采样

纹理让 3D 模型从单色变为逼真——木纹、砖墙、皮肤、金属，全靠纹理贴图。本章讲解纹理映射基础、UV 坐标与过滤、Mipmap 多级过滤、纹理寻址与图集。掌握这些，你就能为模型贴上逼真的纹理，并理解"为什么远处的地面会闪烁，以及如何用 Mipmap 解决"。

## 第 13 讲 · 纹理映射基础

### 概念

**纹理（Texture）** 是 2D 图像数据，存储在 GPU 显存中，通过**纹理映射**贴到 3D 模型表面。纹理让简单的几何体显示任意复杂的表面细节。

**纹理映射（Texture Mapping）** 把纹理的 2D 坐标（UV）对应到模型的 3D 顶点，光栅化时插值 UV，每个片段从纹理采样颜色。

### 原理

**纹理对象**：

```c
GLuint texture;
glGenTextures(1, &texture);
glBindTexture(GL_TEXTURE_2D, texture);
```

**纹理上传**：

```c
glTexImage2D(
    GL_TEXTURE_2D,      // 目标
    0,                  // Mipmap 级别（0 = 原始）
    GL_RGB,             // 内部格式
    width, height,
    0,                  // 边框（必须 0）
    GL_RGB,             // 像素数据格式
    GL_UNSIGNED_BYTE,   // 像素数据类型
    data                // 像素数据
);
```

**内部格式**：

| 格式 | 说明 |
|------|------|
| GL_RGB | 24 位 RGB |
| GL_RGBA | 32 位 RGBA |
| GL_SRGB | sRGB 伽马空间 |
| GL_COMPRESSED_RGBA | 压缩纹理 |

**像素数据格式与类型**：

- 格式：GL_RGB、GL_RGBA、GL_RED 等。
- 类型：GL_UNSIGNED_BYTE（8 位）、GL_FLOAT（32 位浮点）。

**UV 坐标**：

每个顶点有 UV 坐标 `(u, v)`，范围通常 [0, 1]：

```
顶点        UV
(0, 0, 0)  (0, 0)  // 左下
(1, 0, 0)  (1, 0)  // 右下
(1, 1, 0)  (1, 1)  // 右上
(0, 1, 0)  (0, 1)  // 左上
```

**UV 的方向**：

- OpenGL 默认 V 向上，(0,0) 在左下角。
- DirectX V 向下，(0,0) 在左上角。
- 加载图片时需注意 Y 翻转。

**Y 翻转**：

```c
// OpenGL 默认 (0,0) 在左下，但图片通常 (0,0) 在左上
// 用 stb_image 加载时翻转 Y
stbi_set_flip_vertically_on_load(true);
```

或在着色器中翻转：

```glsl
vec2 uv = vec2(TexCoord.x, 1.0 - TexCoord.y);
```

**纹理单元（Texture Unit）**：

GPU 有多个纹理单元（如 16~32 个），可同时绑定多个纹理：

```c
glActiveTexture(GL_TEXTURE0);
glBindTexture(GL_TEXTURE_2D, texture1);
glActiveTexture(GL_TEXTURE1);
glBindTexture(GL_TEXTURE_2D, texture2);
```

着色器中：

```glsl
uniform sampler2D tex1;  // 对应 TEXTURE0
uniform sampler2D tex2;  // 对应 TEXTURE1
```

设置：

```c
glUniform1i(glGetUniformLocation(prog, "tex1"), 0);  // 纹理单元 0
glUniform1i(glGetUniformLocation(prog, "tex2"), 1);  // 纹理单元 1
```

**纹理参数**：

```c
// 过滤方式
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);

// 寻址方式
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);
```

### 例子

```cpp
#include <GL/glew.h>
#include <GLFW/glfw3.h>
#define STB_IMAGE_IMPLEMENTATION
#include "stb_image.h"

const char* vs = R"(#version 330 core
layout(location=0) in vec3 aPos;
layout(location=1) in vec2 aTexCoord;
out vec2 TexCoord;
void main() {
    gl_Position = vec4(aPos, 1.0);
    TexCoord = aTexCoord;
})";

const char* fs = R"(#version 330 core
in vec2 TexCoord;
out vec4 FragColor;
uniform sampler2D ourTexture;
void main() {
    FragColor = texture(ourTexture, TexCoord);
})";

GLuint compile(GLenum t, const char* s) {
    GLuint sh = glCreateShader(t);
    glShaderSource(sh, 1, &s, nullptr);
    glCompileShader(sh);
    return sh;
}

int main() {
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
    GLFWwindow* w = glfwCreateWindow(800, 600, "Texture", nullptr, nullptr);
    glfwMakeContextCurrent(w);
    glewInit();

    // 矩形顶点（位置 + UV）
    float vertices[] = {
        // 位置              // UV
         0.5f,  0.5f, 0.0f,  1.0f, 1.0f,
         0.5f, -0.5f, 0.0f,  1.0f, 0.0f,
        -0.5f, -0.5f, 0.0f,  0.0f, 0.0f,
        -0.5f,  0.5f, 0.0f,  0.0f, 1.0f
    };
    unsigned int indices[] = {0, 1, 3, 1, 2, 3};

    GLuint VAO, VBO, EBO;
    glGenVertexArrays(1, &VAO);
    glGenBuffers(1, &VBO);
    glGenBuffers(1, &EBO);
    glBindVertexArray(VAO);
    glBindBuffer(GL_ARRAY_BUFFER, VBO);
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
    glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
    glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);

    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 5 * sizeof(float), 0);
    glEnableVertexAttribArray(0);
    glVertexAttribPointer(1, 2, GL_FLOAT, GL_FALSE, 5 * sizeof(float), (void*)(3 * sizeof(float)));
    glEnableVertexAttribArray(1);

    // 加载纹理
    int width, height, nrChannels;
    stbi_set_flip_vertically_on_load(true);
    unsigned char* data = stbi_load("texture.jpg", &width, &height, &nrChannels, 0);

    GLuint texture;
    glGenTextures(1, &texture);
    glBindTexture(GL_TEXTURE_2D, texture);
    glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, width, height, 0, GL_RGB, GL_UNSIGNED_BYTE, data);
    glGenerateMipmap(GL_TEXTURE_2D);
    stbi_image_free(data);

    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);

    GLuint prog = glCreateProgram();
    glAttachShader(prog, compile(GL_VERTEX_SHADER, vs));
    glAttachShader(prog, compile(GL_FRAGMENT_SHADER, fs));
    glLinkProgram(prog);

    while (!glfwWindowShouldClose(w)) {
        if (glfwGetKey(w, GLFW_KEY_ESCAPE) == GLFW_PRESS)
            glfwSetWindowShouldClose(w, true);

        glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT);

        glUseProgram(prog);
        glBindVertexArray(VAO);
        glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);

        glfwSwapBuffers(w);
        glfwPollEvents();
    }

    glDeleteVertexArrays(1, &VAO);
    glDeleteBuffers(1, &VBO);
    glDeleteBuffers(1, &EBO);
    glDeleteTextures(1, &texture);
    glfwTerminate();
    return 0;
}
```

### 总结

- 纹理是 2D 图像数据，通过 UV 坐标映射到模型表面。
- **纹理上传**：`glTexImage2D`，指定内部格式、像素格式、类型。
- **UV 方向**：OpenGL V 向上，加载图片需 Y 翻转。
- **纹理单元**：GPU 有多个单元，可同时绑定多个纹理。
- **纹理参数**：过滤方式、寻址方式。
- **常见坑**：忘记 Y 翻转导致纹理上下颠倒；纹理单元与 sampler 不匹配。

---

## 第 14 讲 · UV 坐标与过滤

### 概念

**UV 坐标** 是纹理映射的 2D 坐标，U 是水平方向，V 是垂直方向，范围通常 [0, 1]。

**纹理过滤（Texture Filtering）** 决定 UV 落在非整数像素位置时如何取色：最近邻（锐利但锯齿）或双线性（平滑但模糊）。

### 原理

**UV 坐标空间**：

纹理是离散像素网格（如 256×256），但 UV 是连续的 [0,1] 范围。采样时需把 UV 转换为像素坐标：

```
texel_x = uv.x * texture_width
texel_y = uv.y * texture_height
```

**最近邻过滤（GL_NEAREST）**：

取最近像素的颜色：

```
texel_x = floor(uv.x * width)
texel_y = floor(uv.y * height)
color = texture[texel_y][texel_x]
```

放大时产生方块感（马赛克），适合像素艺术。

**双线性过滤（GL_LINEAR）**：

取周围 4 像素加权平均：

```
fx = uv.x * width - 0.5
fy = uv.y * height - 0.5
x0 = floor(fx), x1 = x0 + 1
y0 = floor(fy), y1 = y0 + 1
sx = fx - x0, sy = fy - y0

c00 = texture[y0][x0]
c10 = texture[y0][x1]
c01 = texture[y1][x0]
c11 = texture[y1][x1]

color = bilinear_interpolation(c00, c10, c01, c11, sx, sy)
```

平滑过渡，适合照片纹理。

**过滤模式设置**：

```c
// 放大过滤（纹理小，目标大）
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);

// 缩小过滤（纹理大，目标小）
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
```

**放大与缩小的区别**：

- **放大（MAG_FILTER）**：纹理小，目标大，需插值。
- **缩小（MIN_FILTER）**：纹理大，目标小，需平均（Mipmap）。

**过滤模式选择**：

| 场景 | MAG | MIN | 原因 |
|------|-----|-----|------|
| 像素艺术 | NEAREST | NEAREST | 保持锐利 |
| 照片纹理 | LINEAR | LINEAR_MIPMAP_LINEAR | 平滑 |
| UI | LINEAR | LINEAR | 平滑 |

**各向异性过滤（Anisotropic Filtering）**：

斜视角表面（如地面）标准过滤过度模糊。各向异性过滤沿屏幕像素形状采样多个纹理像素，保持细节：

```c
GLfloat maxAniso;
glGetFloatv(GL_MAX_TEXTURE_MAX_ANISOTROPY_EXT, &maxAniso);
glTexParameterf(GL_TEXTURE_2D, GL_TEXTURE_MAX_ANISOTROPY_EXT, maxAniso);
```

### 例子

```cpp
#include <GL/glew.h>
#include <GLFW/glfw3.h>
#define STB_IMAGE_IMPLEMENTATION
#include "stb_image.h"

// 演示不同过滤模式
int main() {
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
    GLFWwindow* w = glfwCreateWindow(800, 600, "Filtering", nullptr, nullptr);
    glfwMakeContextCurrent(w);
    glewInit();

    // 创建小纹理（8x8 棋盘格）
    unsigned char checker[8 * 8 * 3];
    for (int y = 0; y < 8; ++y) {
        for (int x = 0; x < 8; ++x) {
            bool white = (x + y) % 2 == 0;
            int idx = (y * 8 + x) * 3;
            checker[idx] = white ? 255 : 0;
            checker[idx + 1] = white ? 255 : 0;
            checker[idx + 2] = white ? 255 : 0;
        }
    }

    GLuint textures[2];
    glGenTextures(2, textures);

    // 纹理 0：最近邻
    glBindTexture(GL_TEXTURE_2D, textures[0]);
    glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, 8, 8, 0, GL_RGB, GL_UNSIGNED_BYTE, checker);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);

    // 纹理 1：双线性
    glBindTexture(GL_TEXTURE_2D, textures[1]);
    glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, 8, 8, 0, GL_RGB, GL_UNSIGNED_BYTE, checker);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);

    // ...（着色器与 VAO 初始化省略）

    bool nearest = true;
    while (!glfwWindowShouldClose(w)) {
        if (glfwGetKey(w, GLFW_KEY_SPACE) == GLFW_PRESS)
            nearest = !nearest;

        glClear(GL_COLOR_BUFFER_BIT);

        glBindTexture(GL_TEXTURE_2D, nearest ? textures[0] : textures[1]);
        // 绘制放大的四边形
        // ...

        glfwSwapBuffers(w);
        glfwPollEvents();
    }

    glDeleteTextures(2, textures);
    glfwTerminate();
    return 0;
}
```

### 总结

- UV 坐标范围 [0,1]，U 水平 V 垂直。
- **最近邻**：取最近像素，锐利但方块感，适合像素艺术。
- **双线性**：4 像素加权平均，平滑但模糊，适合照片。
- **放大与缩小**：MAG_FILTER 处理放大，MIN_FILTER 处理缩小。
- **各向异性过滤**：斜视角保持细节，质量最高。
- **常见坑**：像素艺术用 LINEAR 导致模糊；照片用 NEAREST 产生马赛克。

---

## 第 15 讲 · Mipmap 与多级过滤

### 概念

**Mipmap** 是纹理的多级缩放版本——原始纹理（Level 0）逐级减半，生成 Level 1（1/2）、Level 2（1/4）...直到 1×1。采样时根据像素覆盖的纹理区域大小选择合适级别，避免缩小时的闪烁与锯齿。

### 原理

**为什么需要 Mipmap？**

缩小纹理时（如远处地面），一个屏幕像素可能覆盖多个纹理像素。若用最近邻或双线性，只采样一个纹理像素，会丢失细节并产生闪烁（不同帧采样不同像素）。

Mipmap 预计算缩小版本，采样时选择合适级别，相当于"预平均"，消除闪烁。

**Mipmap 的生成**：

每级纹理是上一级的 2×2 平均：

```
Level 0: 256×256（原始）
Level 1: 128×128（每 2×2 平均）
Level 2: 64×64
...
Level 8: 1×1
```

总内存增加 1/3（1 + 1/4 + 1/16 + ... ≈ 4/3）。

**自动生成 Mipmap**：

```c
glGenerateMipmap(GL_TEXTURE_2D);  // 自动生成所有级别
```

或手动指定每级：

```c
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, 256, 256, 0, GL_RGB, GL_UNSIGNED_BYTE, data_level0);
glTexImage2D(GL_TEXTURE_2D, 1, GL_RGB, 128, 128, 0, GL_RGB, GL_UNSIGNED_BYTE, data_level1);
// ...
```

**Mipmap 过滤模式**：

```c
// 缩小时用 Mipmap
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
```

MIN_FILTER 的 Mipmap 模式：

| 模式 | 说明 |
|------|------|
| GL_NEAREST_MIPMAP_NEAREST | 选最近级别，最近邻采样 |
| GL_LINEAR_MIPMAP_NEAREST | 选最近级别，双线性采样 |
| GL_NEAREST_MIPMAP_LINEAR | 两级别混合，最近邻采样 |
| GL_LINEAR_MIPMAP_LINEAR | 两级别混合，双线性采样（三线性，推荐） |

**三线性过滤（Trilinear）**：

`GL_LINEAR_MIPMAP_LINEAR` 在两个相邻 Mipmap 级别各做双线性，再在级别间线性插值，是最平滑的过滤方式。

**Mipmap 选择**：

根据屏幕像素覆盖的纹理区域大小选择级别：

```
ρ = 屏幕像素在纹理空间的覆盖面积
level = log2(sqrt(ρ))
```

若屏幕像素覆盖 4×4 纹理像素，level = log2(2) = 1，用 Level 1。

**Mipmap 的限制**：

- 仅适用于 2 的幂尺寸的纹理（256、512、1024 等）。
- 增加 1/3 显存。
- 不适用于需要精确像素采样的场景（如 UI）。

### 例子

```cpp
#include <GL/glew.h>
#include <GLFW/glfw3.h>
#define STB_IMAGE_IMPLEMENTATION
#include "stb_image.h"

int main() {
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
    GLFWwindow* w = glfwCreateWindow(800, 600, "Mipmap", nullptr, nullptr);
    glfwMakeContextCurrent(w);
    glewInit();

    // 加载高频纹理（棋盘格）
    int width, height, nrChannels;
    stbi_set_flip_vertically_on_load(true);
    unsigned char* data = stbi_load("checkerboard.png", &width, &height, &nrChannels, 0);

    GLuint texture;
    glGenTextures(1, &texture);
    glBindTexture(GL_TEXTURE_2D, texture);
    glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, width, height, 0, GL_RGB, GL_UNSIGNED_BYTE, data);
    glGenerateMipmap(GL_TEXTURE_2D);  // 生成 Mipmap
    stbi_image_free(data);

    // 三线性过滤
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);

    // ...（着色器与 VAO 初始化省略）

    while (!glfwWindowShouldClose(w)) {
        glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

        // 绘制远处的地面（缩小严重，Mipmap 避免闪烁）
        // ...

        glfwSwapBuffers(w);
        glfwPollEvents();
    }

    glDeleteTextures(1, &texture);
    glfwTerminate();
    return 0;
}
```

### 总结

- Mipmap 是纹理的多级缩放版本，每级 2×2 平均，总内存增加 1/3。
- 缩小时选择合适级别，避免闪烁与锯齿。
- **三线性过滤**（GL_LINEAR_MIPMAP_LINEAR）最平滑，推荐。
- 自动生成：`glGenerateMipmap`。
- **限制**：需 2 的幂尺寸；增加显存；不适用于精确像素采样。
- **常见坑**：非 2 幂纹理无法生成 Mipmap；忘记设置 MIN_FILTER 为 Mipmap 模式。

---

## 第 16 讲 · 纹理寻址与图集

### 概念

**纹理寻址模式（Address Mode）** 决定 UV 坐标超出 [0,1] 范围时的采样行为。四种模式：重复、钳制、镜像、边框。

**纹理图集（Texture Atlas）** 把多个小纹理拼成一张大纹理，每个子纹理用 UV 子区域索引。图集减少纹理切换开销，是批处理的基础。

### 原理

**寻址模式**：

1. **重复（GL_REPEAT）**：UV 取小数部分，纹理无限平铺。
2. **钳制（GL_CLAMP_TO_EDGE）**：UV 限制在 [0,1]，超出用边缘像素。
3. **镜像（GL_MIRRORED_REPEAT）**：UV 超出时反向，纹理镜像平铺。
4. **边框（GL_CLAMP_TO_BORDER）**：超出用指定边框色。

```c
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);
```

**边框色**：

```c
float borderColor[] = {1.0f, 0.0f, 0.0f, 1.0f};  // 红色边框
glTexParameterfv(GL_TEXTURE_2D, GL_TEXTURE_BORDER_COLOR, borderColor);
```

**纹理图集**：

图集是一张大纹理（如 1024×1024），内含多个小纹理。每个子纹理用 `(x, y, w, h)` 在图集中的位置索引：

```
图集 1024×1024：
    子图 A: (0, 0, 64, 64)
    子图 B: (64, 0, 64, 64)
    子图 C: (0, 64, 128, 64)
```

渲染时用子图的 UV 范围：

```glsl
// 子图 A 的 UV 范围
vec2 uv_min = vec2(0.0, 0.0);
vec2 uv_max = vec2(64.0 / 1024.0, 64.0 / 1024.0);

vec2 final_uv = mix(uv_min, uv_max, TexCoord);
vec4 color = texture(atlas, final_uv);
```

**图集的优势**：

1. **减少纹理切换**：GPU 切换纹理需重新绑定，开销大。图集让多个子图共享一个纹理。
2. **减少 Draw Call**：批处理把同图集的物体合并为一次绘制。
3. **更好的缓存局部性**：相邻子图在显存中相邻。

**图集的注意事项**：

- **Mipmap 渗色**：子图边缘的 Mipmap 可能采样到相邻子图，需留 padding（边距）。
- **寻址模式**：图集用 CLAMP_TO_EDGE，避免子图边缘采样到相邻图。

### 例子

```cpp
#include <GL/glew.h>
#include <GLFW/glfw3.h>

const char* vs = R"(#version 330 core
layout(location=0) in vec3 aPos;
layout(location=1) in vec2 aTexCoord;
out vec2 TexCoord;
uniform vec2 uvOffset;  // 子图在图集中的偏移
uniform vec2 uvScale;   // 子图大小
void main() {
    gl_Position = vec4(aPos, 1.0);
    TexCoord = uvOffset + aTexCoord * uvScale;
})";

const char* fs = R"(#version 330 core
in vec2 TexCoord;
out vec4 FragColor;
uniform sampler2D atlas;
void main() {
    FragColor = texture(atlas, TexCoord);
})";

GLuint compile(GLenum t, const char* s) {
    GLuint sh = glCreateShader(t);
    glShaderSource(sh, 1, &s, nullptr);
    glCompileShader(sh);
    return sh;
}

int main() {
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
    GLFWwindow* w = glfwCreateWindow(800, 600, "Atlas", nullptr, nullptr);
    glfwMakeContextCurrent(w);
    glewInit();

    // 创建图集（4x4 网格，每格不同颜色）
    const int ATLAS_SIZE = 256;
    const int TILE_SIZE = 64;
    unsigned char atlas[ATLAS_SIZE * ATLAS_SIZE * 3];
    for (int ty = 0; ty < 4; ++ty) {
        for (int tx = 0; tx < 4; ++tx) {
            for (int y = 0; y < TILE_SIZE; ++y) {
                for (int x = 0; x < TILE_SIZE; ++x) {
                    int idx = ((ty * TILE_SIZE + y) * ATLAS_SIZE + (tx * TILE_SIZE + x)) * 3;
                    atlas[idx] = tx * 60;       // R
                    atlas[idx + 1] = ty * 60;   // G
                    atlas[idx + 2] = 128;       // B
                }
            }
        }
    }

    GLuint texture;
    glGenTextures(1, &texture);
    glBindTexture(GL_TEXTURE_2D, texture);
    glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, ATLAS_SIZE, ATLAS_SIZE, 0, GL_RGB, GL_UNSIGNED_BYTE, atlas);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);

    // 矩形顶点
    float vertices[] = {
         0.5f,  0.5f, 0.0f,  1.0f, 1.0f,
         0.5f, -0.5f, 0.0f,  1.0f, 0.0f,
        -0.5f, -0.5f, 0.0f,  0.0f, 0.0f,
        -0.5f,  0.5f, 0.0f,  0.0f, 1.0f
    };
    unsigned int indices[] = {0, 1, 3, 1, 2, 3};

    GLuint VAO, VBO, EBO;
    glGenVertexArrays(1, &VAO);
    glGenBuffers(1, &VBO);
    glGenBuffers(1, &EBO);
    glBindVertexArray(VAO);
    glBindBuffer(GL_ARRAY_BUFFER, VBO);
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
    glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
    glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 5 * sizeof(float), 0);
    glEnableVertexAttribArray(0);
    glVertexAttribPointer(1, 2, GL_FLOAT, GL_FALSE, 5 * sizeof(float), (void*)(3 * sizeof(float)));
    glEnableVertexAttribArray(1);

    GLuint prog = glCreateProgram();
    glAttachShader(prog, compile(GL_VERTEX_SHADER, vs));
    glAttachShader(prog, compile(GL_FRAGMENT_SHADER, fs));
    glLinkProgram(prog);

    GLint uvOffsetLoc = glGetUniformLocation(prog, "uvOffset");
    GLint uvScaleLoc = glGetUniformLocation(prog, "uvScale");

    int current_tile = 0;
    while (!glfwWindowShouldClose(w)) {
        if (glfwGetKey(w, GLFW_KEY_ESCAPE) == GLFW_PRESS)
            glfwSetWindowShouldClose(w, true);
        if (glfwGetKey(w, GLFW_KEY_RIGHT) == GLFW_PRESS)
            current_tile = (current_tile + 1) % 16;

        glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT);

        glUseProgram(prog);

        // 选择图集中的子图
        int tx = current_tile % 4;
        int ty = current_tile / 4;
        glUniform2f(uvOffsetLoc, tx * 0.25f, ty * 0.25f);
        glUniform2f(uvScaleLoc, 0.25f, 0.25f);

        glBindVertexArray(VAO);
        glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);

        glfwSwapBuffers(w);
        glfwPollEvents();
    }

    glDeleteVertexArrays(1, &VAO);
    glDeleteBuffers(1, &VBO);
    glDeleteBuffers(1, &EBO);
    glDeleteTextures(1, &texture);
    glfwTerminate();
    return 0;
}
```

### 总结

- 纹理寻址模式：重复、钳制、镜像、边框。
- **图集**把多个子图拼成大纹理，减少纹理切换与 Draw Call。
- 子图用 UV 偏移与缩放索引：`final_uv = offset + texCoord * scale`。
- **图集用 CLAMP_TO_EDGE**，避免边缘渗色。
- **Mipmap 渗色**：子图边缘 Mipmap 可能采样到相邻图，需留 padding。
- **常见坑**：图集用 REPEAT 导致子图边缘渗色；忘记 UV 缩放导致显示整个图集。

---

# 第 5 章 · 光照与材质

光照是 3D 图形的灵魂——没有光照，3D 模型只是单色多边形；有了光照，球体看起来是球体，金属看起来是金属。本章从光照模型基础开始，讲解 Phong 与 Blinn-Phong 模型、多光源与材质系统、光照贴图与法线贴图。掌握这些，你的 3D 场景就能有真实的光影效果。

## 第 17 讲 · 光照模型基础

### 概念

**光照模型（Lighting Model）** 描述物体表面如何响应光照，计算最终颜色。基础光照模型由三个分量组成：

- **环境光（Ambient）**：模拟间接光照（如天空光），常量。
- **漫反射（Diffuse）**：粗糙表面的散射光，与法线和光源方向夹角有关。
- **镜面反射（Specular）**：光滑表面的高光，与反射方向和视线方向有关。

### 原理

**环境光**：

```
ambient = ambient_strength * light_color
```

模拟间接光照，避免阴影区域全黑。通常 ambient_strength = 0.1~0.2。

**漫反射（Lambert 模型）**：

```
diffuse = max(dot(N, L), 0) * light_color
```

- N：表面法线（单位向量）。
- L：从表面指向光源的方向（单位向量）。
- dot(N, L)：法线与光源方向的点积，衡量光照角度。

当 N 与 L 同向（光线垂直照射），dot = 1，最亮；当 N 与 L 垂直，dot = 0，无光；当 N 与 L 反向（背光），dot < 0，截断为 0。

**镜面反射（Phong 模型）**：

```
R = reflect(-L, N)  // 反射方向
specular = pow(max(dot(V, R), 0), shininess) * light_color
```

- V：从表面指向相机的方向（视线方向）。
- R：光线的反射方向。
- shininess：高光指数，越大高光越集中。

当 V 与 R 同向（视线与反射光一致），高光最强。

**完整 Phong 光照**：

```
final_color = (ambient + diffuse + specular) * object_color
```

**法线的变换**：

法线不能用 model 矩阵直接变换，需用**法线矩阵**：

```
normalMatrix = transpose(inverse(mat3(model)))
```

非均匀缩放会扭曲法线，法线矩阵修正这一点。

**光源类型**：

1. **方向光（Directional Light）**：所有光线平行（如太阳），只有方向无位置。
2. **点光源（Point Light）**：从一点向四周辐射，有距离衰减。
3. **聚光灯（Spot Light）**：点光源 + 方向限制（圆锥）。

**点光源的衰减**：

```
distance = length(lightPos - fragPos)
attenuation = 1.0 / (1.0 + 0.09 * distance + 0.032 * distance²)
diffuse *= attenuation
```

**聚光灯的圆锥**：

```
lightDir = normalize(lightPos - fragPos)
theta = dot(lightDir, normalize(-spotDir))
if (theta > cos(cutoff)):
    // 在圆锥内
    intensity = (theta - cos(cutoff)) / (cos(outerCutoff) - cos(cutoff))
    intensity = clamp(intensity, 0, 1)
    diffuse *= intensity
```

### 例子

```glsl
#version 330 core
// 基础光照着色器

in vec3 FragPos;
in vec3 Normal;
in vec2 TexCoord;

out vec4 FragColor;

uniform sampler2D diffuseMap;
uniform vec3 lightPos;
uniform vec3 viewPos;
uniform vec3 lightColor;

void main() {
    vec3 objectColor = texture(diffuseMap, TexCoord).rgb;

    // 环境光
    float ambientStrength = 0.1;
    vec3 ambient = ambientStrength * lightColor;

    // 漫反射
    vec3 N = normalize(Normal);
    vec3 L = normalize(lightPos - FragPos);
    float diff = max(dot(N, L), 0.0);
    vec3 diffuse = diff * lightColor;

    // 镜面反射
    float specularStrength = 0.5;
    vec3 V = normalize(viewPos - FragPos);
    vec3 R = reflect(-L, N);
    float spec = pow(max(dot(V, R), 0.0), 32);
    vec3 specular = specularStrength * spec * lightColor;

    vec3 result = (ambient + diffuse + specular) * objectColor;
    FragColor = vec4(result, 1.0);
}
```

### 总结

- 光照模型 = 环境光 + 漫反射 + 镜面反射。
- **环境光**：模拟间接光，避免阴影全黑。
- **漫反射**：`max(dot(N, L), 0)`，粗糙表面散射。
- **镜面反射**：`pow(dot(V, R), shininess)`，光滑表面高光。
- **法线矩阵**：`transpose(inverse(model))`，修正非均匀缩放。
- **光源类型**：方向光、点光源、聚光灯。
- **常见坑**：法线未归一化；法线用 model 而非法线矩阵变换。

---

## 第 18 讲 · Phong 与 Blinn-Phong

### 概念

**Phong 模型** 是经典光照模型，用反射向量 R 计算镜面反射。

**Blinn-Phong 模型** 是 Phong 的改进，用半程向量 H 代替反射向量 R，计算更高效且视觉更自然。Blinn-Phong 是 OpenGL 固定管线使用的模型，也是现代着色器的标准选择。

### 原理

**Phong 模型回顾**：

```
R = reflect(-L, N)  // 反射向量
specular = pow(max(dot(V, R), 0), shininess)
```

反射向量 R 的计算需一次 reflect 函数（含点积与乘法）。

**Blinn-Phong 模型**：

用**半程向量 H**（光线方向 L 与视线方向 V 的中间方向）代替反射向量：

```
H = normalize(L + V)  // 半程向量
specular = pow(max(dot(N, H), 0), shininess)
```

H 的计算只需一次加法与归一化，比 reflect 更快。

**Phong vs Blinn-Phong**：

| 特性 | Phong | Blinn-Phong |
|------|-------|-------------|
| 镜面计算 | dot(V, R) | dot(N, H) |
| 计算量 | reflect（重） | normalize(L+V)（轻） |
| 高光位置 | 略偏 | 更准确 |
| shininess 调整 | 直接 | 通常需 ×4 |

**shininess 的调整**：

Blinn-Phong 的 dot(N, H) 比 Phong 的 dot(V, R) 变化更快，因此相同 shininess 下 Blinn-Phong 的高光更小。通常 Blinn-Phong 的 shininess 设为 Phong 的 4 倍以获得相似效果。

**高光指数 shininess**：

| 材质 | shininess |
|------|-----------|
| 哑光 | 2-8 |
| 木材 | 8-32 |
| 塑料 | 32-64 |
| 金属 | 64-256 |
| 镜面 | 256+ |

**半程向量的几何意义**：

H 是 L 与 V 的角平分线方向。当 N 与 H 重合时，镜面反射最强——此时 L 与 V 关于 N 对称，即观察者正好在反射方向上。

### 例子

```glsl
#version 330 core
// Blinn-Phong 光照

in vec3 FragPos;
in vec3 Normal;
in vec2 TexCoord;

out vec4 FragColor;

uniform sampler2D diffuseMap;
uniform vec3 lightPos;
uniform vec3 viewPos;
uniform vec3 lightColor;

void main() {
    vec3 objectColor = texture(diffuseMap, TexCoord).rgb;

    // 环境光
    vec3 ambient = 0.1 * lightColor;

    // 漫反射
    vec3 N = normalize(Normal);
    vec3 L = normalize(lightPos - FragPos);
    float diff = max(dot(N, L), 0.0);
    vec3 diffuse = diff * lightColor;

    // Blinn-Phong 镜面反射
    vec3 V = normalize(viewPos - FragPos);
    vec3 H = normalize(L + V);  // 半程向量
    float spec = pow(max(dot(N, H), 0.0), 128);  // shininess=128
    vec3 specular = spec * lightColor;

    vec3 result = (ambient + diffuse + specular) * objectColor;
    FragColor = vec4(result, 1.0);
}
```

### 总结

- Blinn-Phong 用半程向量 H 代替反射向量 R，更高效。
- `H = normalize(L + V)`，`specular = pow(dot(N, H), shininess)`。
- Blinn-Phong 的 shininess 通常设为 Phong 的 4 倍。
- **shininess**：哑光 2-8，木材 8-32，塑料 32-64，金属 64-256。
- **优势**：计算量小，高光位置更准确。
- **常见坑**：shininess 过小导致高光过大；L 与 V 都需归一化。

---

## 第 19 讲 · 多光源与材质

### 概念

真实场景通常有多个光源——太阳光、点光源、聚光灯组合。**多光源**技术让场景光照更丰富。

**材质（Material）** 描述物体表面的光学属性：环境色、漫反射色、镜面色、发光色、shininess。不同材质让同一光照下物体呈现不同外观。

### 原理

**多光源的合成**：

每个光源独立计算光照，结果相加：

```glsl
vec3 result = vec3(0);
result += calc_directional_light(dirLight, N, V);
result += calc_point_light(pointLight, FragPos, N, V);
result += calc_spot_light(spotLight, FragPos, N, V);
```

**方向光函数**：

```glsl
vec3 calc_directional_light(DirLight light, vec3 N, vec3 V) {
    vec3 L = normalize(-light.direction);
    float diff = max(dot(N, L), 0.0);
    vec3 H = normalize(L + V);
    float spec = pow(max(dot(N, H), 0.0), material.shininess);

    vec3 ambient = light.ambient * material.diffuse;
    vec3 diffuse = light.diffuse * diff * material.diffuse;
    vec3 specular = light.specular * spec * material.specular;
    return ambient + diffuse + specular;
}
```

**点光源函数**：

```glsl
vec3 calc_point_light(PointLight light, vec3 fragPos, vec3 N, vec3 V) {
    vec3 L = normalize(light.position - fragPos);
    float diff = max(dot(N, L), 0.0);
    vec3 H = normalize(L + V);
    float spec = pow(max(dot(N, H), 0.0), material.shininess);

    // 衰减
    float distance = length(light.position - fragPos);
    float attenuation = 1.0 / (1.0 + 0.09 * distance + 0.032 * distance * distance);

    vec3 ambient = light.ambient * material.diffuse * attenuation;
    vec3 diffuse = light.diffuse * diff * material.diffuse * attenuation;
    vec3 specular = light.specular * spec * material.specular * attenuation;
    return ambient + diffuse + specular;
}
```

**聚光灯函数**：

```glsl
vec3 calc_spot_light(SpotLight light, vec3 fragPos, vec3 N, vec3 V) {
    vec3 L = normalize(light.position - fragPos);
    float diff = max(dot(N, L), 0.0);
    vec3 H = normalize(L + V);
    float spec = pow(max(dot(N, H), 0.0), material.shininess);

    // 圆锥
    float theta = dot(L, normalize(-light.direction));
    float epsilon = light.cutoff - light.outerCutoff;
    float intensity = clamp((theta - light.outerCutoff) / epsilon, 0.0, 1.0);

    // 衰减
    float distance = length(light.position - fragPos);
    float attenuation = 1.0 / (1.0 + 0.09 * distance + 0.032 * distance * distance);

    vec3 ambient = light.ambient * material.diffuse * attenuation;
    vec3 diffuse = light.diffuse * diff * material.diffuse * intensity * attenuation;
    vec3 specular = light.specular * spec * material.specular * intensity * attenuation;
    return ambient + diffuse + specular;
}
```

**材质结构体**：

```glsl
struct Material {
    sampler2D diffuse;      // 漫反射贴图
    sampler2D specular;     // 镜面贴图
    float shininess;
};

uniform Material material;
```

**光源结构体**：

```glsl
struct DirLight {
    vec3 direction;
    vec3 ambient, diffuse, specular;
};

struct PointLight {
    vec3 position;
    vec3 ambient, diffuse, specular;
    // 衰减系数
};

struct SpotLight {
    vec3 position, direction;
    float cutoff, outerCutoff;
    vec3 ambient, diffuse, specular;
};
```

### 例子

```glsl
#version 330 core
// 多光源 + 材质

in vec3 FragPos;
in vec3 Normal;
in vec2 TexCoord;
out vec4 FragColor;

struct Material {
    sampler2D diffuse;
    sampler2D specular;
    float shininess;
};

struct DirLight {
    vec3 direction;
    vec3 ambient, diffuse, specular;
};

struct PointLight {
    vec3 position;
    vec3 ambient, diffuse, specular;
    float constant, linear, quadratic;
};

uniform Material material;
uniform DirLight dirLight;
uniform PointLight pointLights[4];  // 4 个点光源
uniform vec3 viewPos;

vec3 calc_dir_light(DirLight light, vec3 N, vec3 V) {
    vec3 L = normalize(-light.direction);
    float diff = max(dot(N, L), 0.0);
    vec3 H = normalize(L + V);
    float spec = pow(max(dot(N, H), 0.0), material.shininess);
    vec3 ambient = light.ambient * vec3(texture(material.diffuse, TexCoord));
    vec3 diffuse = light.diffuse * diff * vec3(texture(material.diffuse, TexCoord));
    vec3 specular = light.specular * spec * vec3(texture(material.specular, TexCoord));
    return ambient + diffuse + specular;
}

vec3 calc_point_light(PointLight light, vec3 fragPos, vec3 N, vec3 V) {
    vec3 L = normalize(light.position - fragPos);
    float diff = max(dot(N, L), 0.0);
    vec3 H = normalize(L + V);
    float spec = pow(max(dot(N, H), 0.0), material.shininess);
    float distance = length(light.position - fragPos);
    float attenuation = 1.0 / (light.constant + light.linear * distance + light.quadratic * distance * distance);
    vec3 ambient = light.ambient * vec3(texture(material.diffuse, TexCoord)) * attenuation;
    vec3 diffuse = light.diffuse * diff * vec3(texture(material.diffuse, TexCoord)) * attenuation;
    vec3 specular = light.specular * spec * vec3(texture(material.specular, TexCoord)) * attenuation;
    return ambient + diffuse + specular;
}

void main() {
    vec3 N = normalize(Normal);
    vec3 V = normalize(viewPos - FragPos);

    vec3 result = calc_dir_light(dirLight, N, V);
    for (int i = 0; i < 4; ++i)
        result += calc_point_light(pointLights[i], FragPos, N, V);

    FragColor = vec4(result, 1.0);
}
```

### 总结

- 多光源 = 各光源独立计算后相加。
- **光源类型**：方向光、点光源（衰减）、聚光灯（圆锥 + 衰减）。
- **材质**：漫反射贴图、镜面贴图、shininess。
- **结构体**：用 struct 组织光源与材质属性。
- **点光源衰减**：`1 / (c + l*d + q*d²)`。
- **常见坑**：多光源性能开销大，需限制数量；衰减系数设置不当导致光照范围异常。

---

## 第 20 讲 · 光照贴图与法线贴图

### 概念

**光照贴图（Lighting Map）** 用纹理控制材质属性——漫反射贴图决定基础颜色，镜面贴图决定哪些部分反光（如金属边框反光，布料不反光）。

**法线贴图（Normal Map）** 用纹理存储表面法线，让低多边形模型呈现高多边形细节。法线贴图是现代游戏提升视觉品质的核心技术——几万三角形的模型能呈现几百万三角形的细节。

### 原理

**镜面贴图**：

```glsl
vec3 specularColor = texture(specularMap, TexCoord).rgb;
vec3 specular = spec * specularColor * lightColor;
```

镜面贴图是灰度图，白色表示强反光（金属），黑色表示不反光（布料）。

**发光贴图（Emission Map）**：

```glsl
vec3 emission = texture(emissionMap, TexCoord).rgb;
result += emission;  // 不受光照影响
```

发光贴图让物体部分区域自发光（如灯泡、屏幕），不参与光照计算。

**法线贴图**：

法线贴图是 RGB 纹理，每个像素的 RGB 编码一个法向量：

```
R = (normal.x + 1) / 2   // x 分量，[-1,1] → [0,1]
G = (normal.y + 1) / 2   // y 分量
B = (normal.z + 1) / 2   // z 分量（通常接近 1，蓝色调）
```

平坦表面的法线为 (0, 0, 1)，编码为 (128, 128, 255)（蓝紫色）。

**切线空间（Tangent Space）**：

法线贴图的法线在**切线空间**——以顶点法线为 Z 轴、切线为 X 轴、副切线为 Y 轴的局部坐标系。需把法线变换到世界空间：

```glsl
vec3 tangentNormal = texture(normalMap, TexCoord).rgb * 2.0 - 1.0;
vec3 worldNormal = normalize(TBN * tangentNormal);
```

TBN 矩阵由顶点的 T（切线）、B（副切线）、N（法线）组成：

```glsl
mat3 TBN = mat3(T, B, N);
```

**TBN 的计算**：

顶点着色器中：

```glsl
vec3 T = normalize(vec3(model * vec4(aTangent, 0.0)));
vec3 N = normalize(vec3(model * vec4(aNormal, 0.0)));
// Gram-Schmidt 正交化
T = normalize(T - dot(T, N) * N);
vec3 B = cross(N, T);
mat3 TBN = mat3(T, B, N);
```

**法线贴图的优势**：

1. **低多边形高细节**：1000 三角形模型呈现 100 万三角形细节。
2. **性能高**：仅增加一次纹理采样与矩阵乘法。
3. **内存省**：法线贴图比真实高模小得多。

### 例子

```glsl
#version 330 core
// 法线贴图着色器

in vec3 FragPos;
in vec2 TexCoord;
in mat3 TBN;  // 切线空间矩阵

out vec4 FragColor;

uniform sampler2D diffuseMap;
uniform sampler2D normalMap;
uniform sampler2D specularMap;

uniform vec3 lightPos;
uniform vec3 viewPos;

void main() {
    // 从法线贴图采样并解码
    vec3 tangentNormal = texture(normalMap, TexCoord).rgb * 2.0 - 1.0;
    vec3 N = normalize(TBN * tangentNormal);  // 变换到世界空间

    vec3 objectColor = texture(diffuseMap, TexCoord).rgb;
    vec3 specularColor = texture(specularMap, TexCoord).rgb;

    // 光照（在切线空间或世界空间计算）
    vec3 L = normalize(lightPos - FragPos);
    vec3 V = normalize(viewPos - FragPos);
    vec3 H = normalize(L + V);

    vec3 ambient = 0.1 * objectColor;
    float diff = max(dot(N, L), 0.0);
    vec3 diffuse = diff * objectColor;
    float spec = pow(max(dot(N, H), 0.0), 32);
    vec3 specular = spec * specularColor;

    FragColor = vec4(ambient + diffuse + specular, 1.0);
}
```

### 总结

- 光照贴图：漫反射贴图（颜色）、镜面贴图（反光区域）、发光贴图（自发光）。
- **法线贴图**用 RGB 编码法线，让低模呈现高模细节。
- 法线贴图在**切线空间**，需用 TBN 矩阵变换到世界空间。
- **TBN**：T（切线）、B（副切线）、N（法线），用 Gram-Schmidt 正交化。
- **优势**：低多边形高细节、性能高、内存省。
- **常见坑**：法线贴图未解码（`*2-1`）；TBN 矩阵计算错误导致光照异常。

---

# 第 6 章 · 高级渲染

本章讲解让画面从"能看"到"好看"的高级技术：帧缓冲与离屏渲染、后处理效果、阴影映射、HDR 与 Gamma 校正。这些是现代游戏引擎的标配——没有阴影的场景缺乏立体感，没有 HDR 的画面色彩平淡，没有后处理的渲染缺少电影感。

## 第 21 讲 · 帧缓冲与离屏渲染

### 概念

**帧缓冲（Frame Buffer Object, FBO）** 是 OpenGL 的离屏渲染目标——不直接渲染到屏幕，而是渲染到纹理或渲染缓冲。FBO 让你能"先渲染到纹理，再处理"，是后处理、阴影映射、延迟渲染的基础。

默认帧缓冲直接显示到屏幕，自定义 FBO 渲染到纹理，可进一步处理。

### 原理

**FBO 的组成**：

一个完整的 FBO 需要：

1. **颜色附件（Color Attachment）**：纹理或渲染缓冲，存储颜色。
2. **深度附件（Depth Attachment）**：渲染缓冲，存储深度。
3. **模板附件（Stencil Attachment）**：可选，存储模板。

**创建 FBO**：

```c
GLuint fbo;
glGenFramebuffers(1, &fbo);
glBindFramebuffer(GL_FRAMEBUFFER, fbo);
```

**创建颜色附件（纹理）**：

```c
GLuint texColorBuffer;
glGenTextures(1, &texColorBuffer);
glBindTexture(GL_TEXTURE_2D, texColorBuffer);
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, width, height, 0, GL_RGB, GL_UNSIGNED_BYTE, NULL);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_2D, texColorBuffer, 0);
```

**创建深度附件（渲染缓冲）**：

```c
GLuint rboDepth;
glGenRenderbuffers(1, &rboDepth);
glBindRenderbuffer(GL_RENDERBUFFER, rboDepth);
glRenderbufferStorage(GL_RENDERBUFFER, GL_DEPTH24_STENCIL8, width, height);
glFramebufferRenderbuffer(GL_FRAMEBUFFER, GL_DEPTH_STENCIL_ATTACHMENT, GL_RENDERBUFFER, rboDepth);
```

**检查 FBO 完整性**：

```c
if (glCheckFramebufferStatus(GL_FRAMEBUFFER) != GL_FRAMEBUFFER_COMPLETE)
    std::cout << "Framebuffer not complete!" << std::endl;
```

**渲染到 FBO**：

```c
glBindFramebuffer(GL_FRAMEBUFFER, fbo);
glClearColor(0.1f, 0.1f, 0.1f, 1.0f);
glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
// 渲染场景...
```

**渲染到屏幕**：

```c
glBindFramebuffer(GL_FRAMEBUFFER, 0);  // 0 = 默认帧缓冲（屏幕）
// 渲染 FBO 的纹理...
```

**使用 FBO 纹理**：

把 FBO 的颜色附件当作普通纹理使用：

```c
glBindTexture(GL_TEXTURE_2D, texColorBuffer);
// 在着色器中采样
```

### 例子

```cpp
#include <GL/glew.h>
#include <GLFW/glfw3.h>

// 创建 FBO
GLuint create_fbo(int width, int height, GLuint& color_tex, GLuint& depth_rbo) {
    GLuint fbo;
    glGenFramebuffers(1, &fbo);
    glBindFramebuffer(GL_FRAMEBUFFER, fbo);

    // 颜色附件
    glGenTextures(1, &color_tex);
    glBindTexture(GL_TEXTURE_2D, color_tex);
    glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, width, height, 0, GL_RGB, GL_UNSIGNED_BYTE, NULL);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
    glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_2D, color_tex, 0);

    // 深度附件
    glGenRenderbuffers(1, &depth_rbo);
    glBindRenderbuffer(GL_RENDERBUFFER, depth_rbo);
    glRenderbufferStorage(GL_RENDERBUFFER, GL_DEPTH24_STENCIL8, width, height);
    glFramebufferRenderbuffer(GL_FRAMEBUFFER, GL_DEPTH_STENCIL_ATTACHMENT, GL_RENDERBUFFER, depth_rbo);

    if (glCheckFramebufferStatus(GL_FRAMEBUFFER) != GL_FRAMEBUFFER_COMPLETE)
        printf("FBO incomplete!\n");

    glBindFramebuffer(GL_FRAMEBUFFER, 0);
    return fbo;
}

int main() {
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
    GLFWwindow* w = glfwCreateWindow(800, 600, "FBO", nullptr, nullptr);
    glfwMakeContextCurrent(w);
    glewInit();

    GLuint color_tex, depth_rbo;
    GLuint fbo = create_fbo(800, 600, color_tex, depth_rbo);

    // ...（着色器与场景初始化省略）

    while (!glfwWindowShouldClose(w)) {
        // 1. 渲染场景到 FBO
        glBindFramebuffer(GL_FRAMEBUFFER, fbo);
        glEnable(GL_DEPTH_TEST);
        glClearColor(0.1f, 0.1f, 0.1f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
        // render_scene();

        // 2. 渲染 FBO 纹理到屏幕（用后处理着色器）
        glBindFramebuffer(GL_FRAMEBUFFER, 0);
        glDisable(GL_DEPTH_TEST);
        glClearColor(1.0f, 1.0f, 1.0f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT);
        glBindTexture(GL_TEXTURE_2D, color_tex);
        // render_quad();

        glfwSwapBuffers(w);
        glfwPollEvents();
    }

    glDeleteFramebuffers(1, &fbo);
    glDeleteTextures(1, &color_tex);
    glDeleteRenderbuffers(1, &depth_rbo);
    glfwTerminate();
    return 0;
}
```

### 总结

- FBO 是离屏渲染目标，渲染到纹理而非屏幕。
- **组成**：颜色附件（纹理）+ 深度附件（渲染缓冲）。
- **流程**：创建 FBO → 附加纹理/渲染缓冲 → 检查完整性 → 渲染到 FBO → 用 FBO 纹理。
- **应用**：后处理、阴影映射、延迟渲染、屏幕空间特效。
- **常见坑**：忘记检查完整性；FBO 尺寸与视口不匹配；忘记绑定回 0。

---

## 第 22 讲 · 后处理效果

### 概念

**后处理（Post-Processing）** 在渲染完成后对整个画面应用特效：模糊、辉光、色调映射、暗角、色差。后处理让画面有"电影感"，是现代游戏视觉品质的关键。

### 原理

**后处理流程**：

```
1. 渲染场景到 FBO 纹理
2. 用后处理着色器渲染全屏四边形，采样 FBO 纹理
3. 输出到屏幕
```

**全屏四边形**：

后处理用覆盖全屏的两个三角形：

```c
float quad[] = {
    -1, -1,  0, 0,
     1, -1,  1, 0,
    -1,  1,  0, 1,
     1,  1,  1, 1
};
```

**反相（Inversion）**：

```glsl
FragColor = vec4(1.0 - texture(scene, uv).rgb, 1.0);
```

**灰度（Grayscale）**：

```glsl
vec3 color = texture(scene, uv).rgb;
float gray = dot(color, vec3(0.299, 0.587, 0.114));
FragColor = vec4(gray, gray, gray, 1.0);
```

**模糊（Blur）**：

高斯模糊用卷积核采样周围像素：

```glsl
const float offset[5] = float[](0.0, 1.0, 2.0, 3.0, 4.0);
const float weight[5] = float[](0.227027, 0.1945946, 0.1216216, 0.054054, 0.016216);

void main() {
    vec2 tex_offset = 1.0 / textureSize(scene, 0);
    vec3 result = texture(scene, uv).rgb * weight[0];
    for (int i = 1; i < 5; ++i) {
        result += texture(scene, uv + vec2(tex_offset.x * offset[i], 0)).rgb * weight[i];
        result += texture(scene, uv - vec2(tex_offset.x * offset[i], 0)).rgb * weight[i];
    }
    FragColor = vec4(result, 1.0);
}
```

**分离模糊**：先水平模糊，再垂直模糊，复杂度从 O(N²) 降到 O(N)。

**辉光（Bloom）**：

1. 提取亮部（亮度 > 阈值）。
2. 多次高斯模糊。
3. 叠加回原图。

```glsl
// 提取亮部
vec3 color = texture(scene, uv).rgb;
float brightness = dot(color, vec3(0.2126, 0.7152, 0.0722));
if (brightness > 1.0)
    FragColor = vec4(color, 1.0);
else
    FragColor = vec4(0, 0, 0, 1.0);
```

**暗角（Vignette）**：

```glsl
vec2 uv = gl_FragCoord.xy / resolution;
float vignette = 1.0 - length(uv - 0.5) * 1.5;
FragColor = texture(scene, uv) * vignette;
```

**色差（Chromatic Aberration）**：

```glsl
float offset = 0.005;
vec3 color;
color.r = texture(scene, uv + vec2(offset, 0)).r;
color.g = texture(scene, uv).g;
color.b = texture(scene, uv - vec2(offset, 0)).b;
FragColor = vec4(color, 1.0);
```

### 例子

```glsl
#version 330 core
// 后处理：模糊 + 暗角 + 色差

in vec2 uv;
out vec4 FragColor;

uniform sampler2D scene;
uniform vec2 resolution;

const float offset[5] = float[](0.0, 1.0, 2.0, 3.0, 4.0);
const float weight[5] = float[](0.227027, 0.1945946, 0.1216216, 0.054054, 0.016216);

void main() {
    vec2 tex_offset = 1.0 / resolution;

    // 水平模糊
    vec3 result = texture(scene, uv).rgb * weight[0];
    for (int i = 1; i < 5; ++i) {
        result += texture(scene, uv + vec2(tex_offset.x * offset[i], 0)).rgb * weight[i];
        result += texture(scene, uv - vec2(tex_offset.x * offset[i], 0)).rgb * weight[i];
    }

    // 色差
    float ca = 0.003;
    result.r = texture(scene, uv + vec2(ca, 0)).r;
    result.b = texture(scene, uv - vec2(ca, 0)).b;

    // 暗角
    float vignette = 1.0 - length(uv - 0.5) * 1.2;
    result *= clamp(vignette, 0.0, 1.0);

    FragColor = vec4(result, 1.0);
}
```

### 总结

- 后处理在渲染后对画面应用特效，提升视觉品质。
- **流程**：渲染到 FBO → 后处理着色器采样 FBO 纹理 → 输出到屏幕。
- **效果**：反相、灰度、模糊、辉光、暗角、色差。
- **分离模糊**：先水平后垂直，复杂度 O(N²) → O(N)。
- **辉光**：提取亮部 → 模糊 → 叠加。
- **常见坑**：后处理过多导致性能下降；UV 方向错误导致翻转。

---

## 第 23 讲 · 阴影映射

### 概念

**阴影映射（Shadow Mapping）** 是实时阴影的标准技术——从光源视角渲染深度，再在主渲染中比较深度判断阴影。

阴影让 3D 场景有立体感与空间感——没有阴影，物体仿佛漂浮；有了阴影，物体"扎根"在地面。

### 原理

**阴影映射的两步**：

1. **深度贴图（Depth Map）**：从光源视角渲染场景，只存深度。
2. **阴影计算**：主渲染时，把片段变换到光源空间，比较深度判断是否在阴影中。

**第一步：渲染深度贴图**：

```c
// 用光源的视图与投影矩阵
glm::mat4 lightView = glm::lookAt(lightPos, target, up);
glm::mat4 lightProjection = glm::ortho(-10, 10, -10, 10, 1, 20);
glm::mat4 lightSpaceMatrix = lightProjection * lightView;

// 渲染到 FBO（只深度）
glBindFramebuffer(GL_FRAMEBUFFER, depthFBO);
glClear(GL_DEPTH_BUFFER_BIT);
glUseProgram(depthShader);
glUniformMatrix4fv(loc, 1, GL_FALSE, &lightSpaceMatrix[0][0]);
// render_scene();
```

深度着色器：

```glsl
// depth.vs
uniform mat4 lightSpaceMatrix;
uniform mat4 model;
void main() {
    gl_Position = lightSpaceMatrix * model * vec4(aPos, 1.0);
}

// depth.fs
void main() {
    // 不需写颜色，深度自动写入
}
```

**第二步：阴影计算**：

```glsl
// 主着色器
uniform sampler2D shadowMap;
uniform mat4 lightSpaceMatrix;

in vec4 FragPosLightSpace;

void main() {
    // 透视除法
    vec3 projCoords = FragPosLightSpace.xyz / FragPosLightSpace.w;
    projCoords = projCoords * 0.5 + 0.5;  // [-1,1] → [0,1]

    // 采样深度贴图
    float closestDepth = texture(shadowMap, projCoords.xy).r;
    float currentDepth = projCoords.z;

    // 阴影判断
    float shadow = currentDepth > closestDepth + 0.005 ? 0.5 : 1.0;

    vec3 result = (ambient + diffuse * shadow + specular * shadow) * objectColor;
    FragColor = vec4(result, 1.0);
}
```

**阴影痤疮（Shadow Acne）**：

由于深度贴图离散采样，阴影边缘出现条纹。用**偏移（Bias）**解决：

```glsl
float shadow = currentDepth > closestDepth + bias ? 0.5 : 1.0;
```

**Peter Panning**：

偏移过大导致物体"漂浮"（阴影与物体分离）。需选择合适偏移。

**过度采样**：

超出深度贴图范围（[0,1] 之外）的片段不应有阴影：

```glsl
if (projCoords.z > 1.0)
    shadow = 1.0;  // 无阴影
```

**PCF（Percentage-Close Filtering）**：

采样周围多个点，平均阴影值，软化阴影边缘：

```glsl
float shadow = 0.0;
vec2 texelSize = 1.0 / textureSize(shadowMap, 0);
for (int x = -1; x <= 1; ++x) {
    for (int y = -1; y <= 1; ++y) {
        float pcfDepth = texture(shadowMap, projCoords.xy + vec2(x, y) * texelSize).r;
        shadow += currentDepth > pcfDepth + bias ? 0.5 : 1.0;
    }
}
shadow /= 9.0;
```

### 例子

```glsl
#version 330 core
// 阴影着色器

in vec3 FragPos;
in vec3 Normal;
in vec2 TexCoord;
in vec4 FragPosLightSpace;

out vec4 FragColor;

uniform sampler2D diffuseMap;
uniform sampler2D shadowMap;

uniform vec3 lightPos;
uniform vec3 viewPos;

float calc_shadow(vec4 fragPosLightSpace) {
    vec3 projCoords = fragPosLightSpace.xyz / fragPosLightSpace.w;
    projCoords = projCoords * 0.5 + 0.5;
    if (projCoords.z > 1.0) return 0.0;

    float currentDepth = projCoords.z;
    float bias = 0.005;

    // PCF
    float shadow = 0.0;
    vec2 texelSize = 1.0 / textureSize(shadowMap, 0);
    for (int x = -1; x <= 1; ++x) {
        for (int y = -1; y <= 1; ++y) {
            float pcfDepth = texture(shadowMap, projCoords.xy + vec2(x, y) * texelSize).r;
            shadow += currentDepth > pcfDepth + bias ? 1.0 : 0.0;
        }
    }
    shadow /= 9.0;
    return shadow;
}

void main() {
    vec3 objectColor = texture(diffuseMap, TexCoord).rgb;
    vec3 N = normalize(Normal);
    vec3 L = normalize(lightPos - FragPos);
    float diff = max(dot(N, L), 0.0);

    float shadow = calc_shadow(FragPosLightSpace);

    vec3 ambient = 0.1 * objectColor;
    vec3 diffuse = (1.0 - shadow) * diff * objectColor;

    FragColor = vec4(ambient + diffuse, 1.0);
}
```

### 总结

- 阴影映射两步：渲染深度贴图 → 主渲染比较深度。
- **深度贴图**：从光源视角渲染，只存深度。
- **阴影判断**：`currentDepth > closestDepth + bias` 则在阴影中。
- **偏移（Bias）**解决阴影痤疮，但过大会导致 Peter Panning。
- **PCF** 采样周围点平均，软化阴影边缘。
- **常见坑**：忘记透视除法；超出深度贴图范围未处理。

---

## 第 24 讲 · HDR 与 Gamma 校正

### 概念

**HDR（High Dynamic Range，高动态范围）** 用浮点数存储颜色，允许亮度超过 1.0，模拟真实光照。LDR（Low Dynamic Range）用 8 位整数 [0,1]，无法表达强光。

**色调映射（Tone Mapping）** 把 HDR 颜色映射到 LDR [0,1]，用于显示。

**Gamma 校正** 补偿显示器的非线性响应——显示器显示的颜色比输入暗（gamma 2.2），需预先提亮（gamma 1/2.2）。

### 原理

**为什么需要 HDR？**

LDR 颜色范围 [0,1]，多个光源叠加后很快饱和（白色），丢失细节。HDR 用浮点数，亮度可达 10、100 甚至更高，保留细节。

**HDR 流程**：

```
1. 用浮点 FBO 渲染场景（GL_RGB16F 或 GL_RGB32F）
2. 色调映射到 [0,1]
3. Gamma 校正
4. 输出到屏幕
```

**浮点 FBO**：

```c
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB16F, width, height, 0, GL_RGB, GL_FLOAT, NULL);
```

`GL_RGB16F` 是半精度浮点格式，足够 HDR 且省显存。

**色调映射**：

**Reinhard 色调映射**：

```glsl
vec3 hdrColor = texture(hdrBuffer, uv).rgb;
vec3 ldrColor = hdrColor / (hdrColor + 1.0);
```

简单，但亮部可能过白。

**ACES 色调映射**（电影工业标准）：

```glsl
vec3 aces(vec3 x) {
    const float a = 2.51, b = 0.03, c = 2.43, d = 0.59, e = 0.14;
    return clamp((x * (a * x + b)) / (x * (c * x + d) + e), 0.0, 1.0);
}
vec3 ldrColor = aces(hdrColor);
```

ACES 有更好的色彩保留与对比度。

**Gamma 校正**：

显示器响应函数近似 `displayed = input^2.2`，因此需预先 `corrected = input^(1/2.2)`：

```glsl
vec3 finalColor = pow(ldrColor, vec3(1.0 / 2.2));
```

或用 GLSL 内置：

```glsl
finalColor = pow(finalColor, vec3(0.4545));  // 1/2.2
```

**sRGB 纹理**：

纹理通常在 sRGB 空间（已 gamma 编码），加载时需转换到线性：

```c
glTexImage2D(GL_TEXTURE_2D, 0, GL_SRGB, width, height, 0, GL_RGB, GL_UNSIGNED_BYTE, data);
```

`GL_SRGB` 让 OpenGL 自动把纹理从 sRGB 转换到线性。

**完整 HDR + Gamma 流程**：

```glsl
void main() {
    vec3 hdrColor = texture(hdrBuffer, uv).rgb;

    // 色调映射
    vec3 ldrColor = hdrColor / (hdrColor + 1.0);

    // Gamma 校正
    ldrColor = pow(ldrColor, vec3(1.0 / 2.2));

    FragColor = vec4(ldrColor, 1.0);
}
```

### 例子

```glsl
#version 330 core
// HDR + Gamma 校正

in vec2 uv;
out vec4 FragColor;

uniform sampler2D hdrBuffer;
uniform float exposure;  // 曝光控制

void main() {
    vec3 hdrColor = texture(hdrBuffer, uv).rgb;

    // 曝光调整
    hdrColor *= exposure;

    // ACES 色调映射
    vec3 aces(vec3 x) {
        const float a = 2.51, b = 0.03, c = 2.43, d = 0.59, e = 0.14;
        return clamp((x * (a * x + b)) / (x * (c * x + d) + e), 0.0, 1.0);
    }
    vec3 ldrColor = aces(hdrColor);

    // Gamma 校正
    ldrColor = pow(ldrColor, vec3(1.0 / 2.2));

    FragColor = vec4(ldrColor, 1.0);
}
```

### 总结

- HDR 用浮点数存储颜色，亮度可超 1.0，保留细节。
- **流程**：浮点 FBO 渲染 → 色调映射 → Gamma 校正 → 屏幕。
- **色调映射**：Reinhard（简单）或 ACES（电影级）。
- **Gamma 校正**：`pow(color, 1/2.2)`，补偿显示器非线性。
- **sRGB 纹理**：用 GL_SRGB 自动转换到线性。
- **常见坑**：忘记 Gamma 校正导致画面偏暗；纹理未用 sRGB 导致颜色失真。

---

# 第 7 章 · 性能与优化

当场景从几百个物体增长到几万个，朴素渲染方法会崩溃——Draw Call 过多、状态切换频繁、显存带宽不足。本章讲解 OpenGL 性能优化的核心技术：实例化渲染、批处理与状态排序、查询对象与遮挡剔除、着色器优化。掌握这些，你的应用就能在复杂场景下保持流畅帧率。

## 第 25 讲 · 实例化渲染

### 概念

**实例化渲染（Instanced Rendering）** 用一次 Draw Call 绘制多个相同几何体的实例。每个实例可有不同位置、旋转、缩放等属性，通过**实例属性（Instanced Attribute）**或**Uniform 数组**传递。

实例化是渲染大量相同物体的核心技术——森林的树木、草地的草叶、星空的星星，都靠实例化实现。

### 原理

**为什么需要实例化？**

渲染 10000 个相同立方体，朴素方法需 10000 次 Draw Call：

```c
for (int i = 0; i < 10000; ++i) {
    glUniformMatrix4fv(modelLoc, ...);  // 设置每个立方体的 model
    glDrawArrays(GL_TRIANGLES, 0, 36);  // 绘制
}
```

每次 Draw Call 有 CPU 开销（约 0.01~0.1ms），10000 次就是 100ms~1s——远超帧预算。

实例化用一次 Draw Call 绘制全部：

```c
glDrawArraysInstanced(GL_TRIANGLES, 0, 36, 10000);  // 一次绘制 10000 个
```

**实例属性**：

每个实例可有不同属性（位置、旋转等），用**实例化顶点属性**传递：

```c
// 每个实例一个 model 矩阵
glm::mat4* modelMatrices = new glm::mat4[10000];
// 填充数据...

GLuint instanceVBO;
glGenBuffers(1, &instanceVBO);
glBindBuffer(GL_ARRAY_BUFFER, instanceVBO);
glBufferData(GL_ARRAY_BUFFER, 10000 * sizeof(glm::mat4), modelMatrices, GL_STATIC_DRAW);

// 矩阵占 4 个属性位置（vec4 × 4）
GLint matLoc = glGetAttribLocation(program, "instanceMatrix");
for (int i = 0; i < 4; ++i) {
    glEnableVertexAttribArray(matLoc + i);
    glVertexAttribPointer(matLoc + i, 4, GL_FLOAT, GL_FALSE, sizeof(glm::mat4),
                          (void*)(i * sizeof(glm::vec4)));
    glVertexAttribDivisor(matLoc + i, 1);  // 每实例更新一次
}
```

`glVertexAttribDivisor(loc, 1)` 是关键——它告诉 OpenGL 该属性每实例更新一次，而非每顶点。

**着色器中的实例属性**：

```glsl
#version 330 core
layout(location = 0) in vec3 aPos;
layout(location = 2) in mat4 instanceMatrix;  // 占 location 2,3,4,5

uniform mat4 view, projection;

void main() {
    gl_Position = projection * view * instanceMatrix * vec4(aPos, 1.0);
}
```

**gl_InstanceID**：

着色器中可用 `gl_InstanceID` 访问当前实例索引：

```glsl
void main() {
    vec3 offset = offsets[gl_InstanceID];  // 从数组取偏移
    gl_Position = projection * view * vec4(aPos + offset, 1.0);
}
```

**实例化的适用场景**：

- 物体几何相同（同一 VAO）。
- 物体数量大（>100）。
- 物体属性可批量传递。

**实例化的限制**：

- 所有实例必须用同一着色器。
- 实例属性需预先计算并上传。
- 不同几何体不能混合实例化。

### 例子

```cpp
#include <GL/glew.h>
#include <GLFW/glfw3.h>
#include <glm/glm.hpp>
#include <glm/gtc/matrix_transform.hpp>
#include <glm/gtc/type_ptr.hpp>
#include <vector>

const char* vs = R"(#version 330 core
layout(location=0) in vec3 aPos;
layout(location=2) in mat4 instanceMatrix;
uniform mat4 view, projection;
void main() {
    gl_Position = projection * view * instanceMatrix * vec4(aPos, 1.0);
})";

const char* fs = R"(#version 330 core
out vec4 FragColor;
void main() { FragColor = vec4(1.0, 0.5, 0.2, 1.0); })";

GLuint compile(GLenum t, const char* s) {
    GLuint sh = glCreateShader(t);
    glShaderSource(sh, 1, &s, nullptr);
    glCompileShader(sh);
    return sh;
}

int main() {
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
    GLFWwindow* w = glfwCreateWindow(800, 600, "Instancing", nullptr, nullptr);
    glfwMakeContextCurrent(w);
    glewInit();

    // 立方体顶点（省略，参考第 11 讲）
    float cube[] = { /* ... */ };

    GLuint VAO, VBO;
    glGenVertexArrays(1, &VAO);
    glGenBuffers(1, &VBO);
    glBindVertexArray(VAO);
    glBindBuffer(GL_ARRAY_BUFFER, VBO);
    glBufferData(GL_ARRAY_BUFFER, sizeof(cube), cube, GL_STATIC_DRAW);
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), 0);
    glEnableVertexAttribArray(0);

    // 生成 1000 个实例的 model 矩阵
    const int NUM_INSTANCES = 1000;
    std::vector<glm::mat4> modelMatrices(NUM_INSTANCES);
    for (int i = 0; i < NUM_INSTANCES; ++i) {
        float angle = (float)i / NUM_INSTANCES * 360.0f;
        float radius = 10.0f;
        glm::mat4 model = glm::translate(glm::mat4(1.0f),
            glm::vec3(cos(glm::radians(angle)) * radius, 0, sin(glm::radians(angle)) * radius));
        model = glm::rotate(model, glm::radians(angle), glm::vec3(0, 1, 0));
        modelMatrices[i] = model;
    }

    // 实例 VBO
    GLuint instanceVBO;
    glGenBuffers(1, &instanceVBO);
    glBindBuffer(GL_ARRAY_BUFFER, instanceVBO);
    glBufferData(GL_ARRAY_BUFFER, NUM_INSTANCES * sizeof(glm::mat4), modelMatrices.data(), GL_STATIC_DRAW);

    GLint matLoc = 2;  // 对应 location=2
    for (int i = 0; i < 4; ++i) {
        glEnableVertexAttribArray(matLoc + i);
        glVertexAttribPointer(matLoc + i, 4, GL_FLOAT, GL_FALSE, sizeof(glm::mat4),
                              (void*)(i * sizeof(glm::vec4)));
        glVertexAttribDivisor(matLoc + i, 1);  // 每实例更新
    }

    GLuint prog = glCreateProgram();
    glAttachShader(prog, compile(GL_VERTEX_SHADER, vs));
    glAttachShader(prog, compile(GL_FRAGMENT_SHADER, fs));
    glLinkProgram(prog);

    while (!glfwWindowShouldClose(w)) {
        glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

        glUseProgram(prog);
        glm::mat4 view = glm::lookAt(glm::vec3(0, 5, 20), glm::vec3(0, 0, 0), glm::vec3(0, 1, 0));
        glm::mat4 projection = glm::perspective(glm::radians(45.0f), 800.0f / 600.0f, 0.1f, 100.0f);
        glUniformMatrix4fv(glGetUniformLocation(prog, "view"), 1, GL_FALSE, glm::value_ptr(view));
        glUniformMatrix4fv(glGetUniformLocation(prog, "projection"), 1, GL_FALSE, glm::value_ptr(projection));

        glBindVertexArray(VAO);
        glDrawArraysInstanced(GL_TRIANGLES, 0, 36, NUM_INSTANCES);  // 一次绘制 1000 个

        glfwSwapBuffers(w);
        glfwPollEvents();
    }

    glDeleteVertexArrays(1, &VAO);
    glDeleteBuffers(1, &VBO);
    glDeleteBuffers(1, &instanceVBO);
    glfwTerminate();
    return 0;
}
```

### 总结

- 实例化用一次 Draw Call 绘制多个相同几何体实例。
- **glDrawArraysInstanced** / **glDrawElementsInstanced** 是实例化绘制命令。
- **实例属性**用 `glVertexAttribDivisor(loc, 1)` 设置每实例更新。
- **gl_InstanceID** 在着色器中访问实例索引。
- **性能**：10000 个物体从 10000 次 Draw Call 降为 1 次。
- **常见坑**：忘记 `glVertexAttribDivisor`；矩阵占 4 个属性位置。

---

## 第 26 讲 · 批处理与状态排序

### 概念

**批处理（Batching）** 把多个物体的顶点数据合并为一个大缓冲，用一次 Draw Call 绘制。批处理减少 Draw Call 数量，是渲染优化的核心。

**状态排序（State Sorting）** 按渲染状态（着色器、纹理、混合模式）对绘制命令排序，最小化状态切换。GPU 切换状态开销大，排序能显著提升性能。

### 原理

**Draw Call 的开销**：

每次 Draw Call 包含：

1. CPU 设置渲染状态（着色器、纹理、混合）。
2. CPU 提交绘制命令到 GPU。
3. GPU 等待并执行。

CPU 开销是主要的，约 0.01~0.1ms/次。1000 次 Draw Call = 10~100ms，远超帧预算。

**批处理的方法**：

1. **静态批处理**：把不动的物体合并为一个大 VBO，一次绘制。
2. **动态批处理**：每帧把可见物体合并（开销大，慎用）。
3. **实例化**：相同几何体用实例化（第 25 讲）。

**静态批处理示例**：

```c
// 100 个静态立方体合并为一个大 VBO
std::vector<float> allVertices;
for (int i = 0; i < 100; ++i) {
    glm::mat4 model = compute_model(i);
    for (int v = 0; v < 36; ++v) {  // 每个立方体 36 顶点
        glm::vec4 transformed = model * glm::vec4(cube[v*3], cube[v*3+1], cube[v*3+2], 1);
        allVertices.push_back(transformed.x);
        allVertices.push_back(transformed.y);
        allVertices.push_back(transformed.z);
    }
}
glBufferData(GL_ARRAY_BUFFER, allVertices.size() * sizeof(float), allVertices.data(), GL_STATIC_DRAW);
glDrawArrays(GL_TRIANGLES, 0, 100 * 36);  // 一次绘制全部
```

**状态排序**：

GPU 切换状态开销：

| 状态切换 | 开销 |
|---------|------|
| 着色器 | 高 |
| 纹理 | 中 |
| 混合模式 | 中 |
| 深度测试 | 低 |
| 多边形模式 | 低 |

按开销从高到低排序绘制命令：

```
1. 按着色器分组
2. 同着色器内按纹理分组
3. 同纹理内按混合模式分组
4. 同混合模式内按深度排序
```

**排序的实现**：

```cpp
struct RenderCommand {
    GLuint shader;
    GLuint texture;
    GLuint blendMode;
    float depth;
    // 绘制数据...
};

std::vector<RenderCommand> commands;
// 收集所有绘制命令...

std::sort(commands.begin(), commands.end(), [](const auto& a, const auto& b) {
    if (a.shader != b.shader) return a.shader < b.shader;
    if (a.texture != b.texture) return a.texture < b.texture;
    if (a.blendMode != b.blendMode) return a.blendMode < b.blendMode;
    return a.depth < b.depth;
});

// 按排序顺序绘制
GLuint currentShader = 0, currentTexture = 0;
for (const auto& cmd : commands) {
    if (cmd.shader != currentShader) {
        glUseProgram(cmd.shader);
        currentShader = cmd.shader;
    }
    if (cmd.texture != currentTexture) {
        glBindTexture(GL_TEXTURE_2D, cmd.texture);
        currentTexture = cmd.texture;
    }
    // 绘制...
}
```

**纹理图集减少切换**：

把多个小纹理合并为图集（第 16 讲），多个物体共享一个纹理，无需切换。

### 例子

```cpp
#include <GL/glew.h>
#include <GLFW/glfw3.h>
#include <vector>
#include <algorithm>

// 渲染命令
struct RenderCommand {
    GLuint shader;
    GLuint texture;
    GLuint vao;
    int indexCount;
    float depth;
};

class Renderer {
public:
    std::vector<RenderCommand> commands;

    void submit(const RenderCommand& cmd) {
        commands.push_back(cmd);
    }

    void flush() {
        // 状态排序
        std::sort(commands.begin(), commands.end(), [](const RenderCommand& a, const RenderCommand& b) {
            if (a.shader != b.shader) return a.shader < b.shader;
            if (a.texture != b.texture) return a.texture < b.texture;
            return a.depth < b.depth;
        });

        // 按排序顺序绘制，最小化状态切换
        GLuint currentShader = 0, currentTexture = 0;
        for (const auto& cmd : commands) {
            if (cmd.shader != currentShader) {
                glUseProgram(cmd.shader);
                currentShader = cmd.shader;
            }
            if (cmd.texture != currentTexture) {
                glActiveTexture(GL_TEXTURE0);
                glBindTexture(GL_TEXTURE_2D, cmd.texture);
                currentTexture = cmd.texture;
            }
            glBindVertexArray(cmd.vao);
            glDrawElements(GL_TRIANGLES, cmd.indexCount, GL_UNSIGNED_INT, 0);
        }
        commands.clear();
    }
};

int main() {
    // 初始化 OpenGL...
    Renderer renderer;

    // 收集渲染命令
    for (int i = 0; i < 1000; ++i) {
        RenderCommand cmd;
        cmd.shader = shaders[i % 3];      // 3 个着色器
        cmd.texture = textures[i % 5];    // 5 个纹理
        cmd.vao = vaos[i];
        cmd.indexCount = 36;
        cmd.depth = positions[i].z;
        renderer.submit(cmd);
    }

    // 一次刷新，最小化状态切换
    renderer.flush();

    return 0;
}
```

### 总结

- 批处理合并顶点数据，减少 Draw Call。
- **静态批处理**：静态物体合并为一个大 VBO。
- **状态排序**：按着色器 → 纹理 → 混合 → 深度排序，最小化状态切换。
- **状态切换开销**：着色器 > 纹理 > 混合 > 深度。
- **纹理图集**减少纹理切换。
- **常见坑**：动态批处理开销大；排序不稳定导致闪烁。

---

## 第 27 讲 · 查询对象与遮挡剔除

### 概念

**查询对象（Query Object）** 让 GPU 反馈信息给 CPU，如遮挡查询（物体是否可见）、计时查询（GPU 耗时）。

**遮挡剔除（Occlusion Culling）** 用查询对象判断物体是否被遮挡，跳过被遮挡物体的绘制。当场景有大量物体但许多被遮挡时（如城市中的建筑），遮挡剔除能大幅提升性能。

### 原理

**遮挡查询**：

```c
GLuint query;
glGenQueries(1, &query);

glBeginQuery(GL_SAMPLES_PASSED, query);
// 绘制物体的简化包围盒（不写颜色/深度）
glEndQuery(GL_SAMPLES_PASSED);

// 获取结果（通过的样本数）
GLuint samplesPassed;
glGetQueryObjectuiv(query, GL_RESULT, &samplesPassed);

if (samplesPassed > 0) {
    // 物体可见，绘制完整几何
    draw_full_geometry();
}
```

**条件渲染**：

避免 GPU→CPU 回读（导致流水线停顿），用**条件渲染**：

```c
glBeginConditionalRender(query, GL_QUERY_WAIT);
draw_full_geometry();  // 仅当 query 通过时执行
glEndConditionalRender();
```

GPU 自行判断是否绘制，无需回读结果到 CPU。

**遮挡查询的流程**：

1. 用简化包围盒（AABB 或低多边形）测试可见性。
2. 包围盒绘制时禁用颜色与深度写入（只测试）。
3. 查询通过的样本数。
4. 若样本数 > 0，物体可见，绘制完整几何。

**查询的模式**：

| 模式 | 说明 |
|------|------|
| GL_QUERY_WAIT | CPU 等待结果（停顿） |
| GL_QUERY_NO_WAIT | 不等待，无结果时直接绘制 |
| GL_QUERY_BY_REGION_WAIT | 仅等待可见区域 |
| GL_QUERY_BY_REGION_NO_WAIT | 仅区域，不等待 |

**计时查询**：

```c
GLuint timerQuery;
glGenQueries(1, &timerQuery);

glBeginQuery(GL_TIME_ELAPSED, timerQuery);
// 绘制...
glEndQuery(GL_TIME_ELAPSED);

GLuint64 elapsed;
glGetQueryObjectui64v(timerQuery, GL_RESULT, &elapsed);
// elapsed 是纳秒
```

**遮挡剔除的适用场景**：

- 场景有大量物体。
- 许多物体被遮挡（如城市、室内）。
- 物体几何复杂（完整绘制开销大）。

**遮挡剔除的限制**：

- 查询有延迟（GPU 异步），结果可能滞后一帧。
- 简化包围盒需预先计算。
- 不适合动态场景（遮挡关系每帧变化）。

### 例子

```cpp
#include <GL/glew.h>
#include <GLFW/glfw3.h>
#include <vector>

struct OcclusionObject {
    GLuint fullVAO;       // 完整几何
    GLuint bboxVAO;       // 包围盒
    GLuint query;
    bool visible;
};

int main() {
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
    GLFWwindow* w = glfwCreateWindow(800, 600, "Occlusion Culling", nullptr, nullptr);
    glfwMakeContextCurrent(w);
    glewInit();

    std::vector<OcclusionObject> objects(100);
    for (auto& obj : objects) {
        // 创建完整几何与包围盒 VAO（省略）
        glGenQueries(1, &obj.query);
        obj.visible = true;
    }

    while (!glfwWindowShouldClose(w)) {
        glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

        // 第一遍：渲染包围盒，查询可见性
        glColorMask(GL_FALSE, GL_FALSE, GL_FALSE, GL_FALSE);  // 禁用颜色写入
        glDepthMask(GL_FALSE);  // 禁用深度写入

        for (auto& obj : objects) {
            glBeginQuery(GL_SAMPLES_PASSED, obj.query);
            glBindVertexArray(obj.bboxVAO);
            glDrawElements(GL_TRIANGLES, 36, GL_UNSIGNED_INT, 0);
            glEndQuery(GL_SAMPLES_PASSED);
        }

        glColorMask(GL_TRUE, GL_TRUE, GL_TRUE, GL_TRUE);
        glDepthMask(GL_TRUE);

        // 第二遍：渲染可见物体的完整几何
        for (auto& obj : objects) {
            glBeginConditionalRender(obj.query, GL_QUERY_NO_WAIT);
            glBindVertexArray(obj.fullVAO);
            glDrawElements(GL_TRIANGLES, 3600, GL_UNSIGNED_INT, 0);  // 完整几何
            glEndConditionalRender();
        }

        glfwSwapBuffers(w);
        glfwPollEvents();
    }

    for (auto& obj : objects) {
        glDeleteQueries(1, &obj.query);
    }
    glfwTerminate();
    return 0;
}
```

### 总结

- 查询对象让 GPU 反馈信息：遮挡查询、计时查询。
- **遮挡剔除**：用包围盒测试可见性，跳过被遮挡物体。
- **条件渲染**：`glBeginConditionalRender` 避免 GPU→CPU 回读。
- **查询模式**：WAIT（等待）、NO_WAIT（不等待）。
- **计时查询**：测量 GPU 耗时，用于性能分析。
- **常见坑**：查询结果滞后一帧；包围盒过大致使剔除无效。

---

## 第 28 讲 · 着色器优化

### 概念

着色器是 GPU 执行的程序，其性能直接影响渲染速度。**着色器优化**通过减少计算量、优化指令、合理使用纹理，提升渲染性能。

优化着色器是高级 OpenGL 开发的必备技能——一个未优化的着色器可能让帧率减半。

### 原理

**优化原则**：

1. **减少计算**：用查表代替复杂计算，用近似代替精确。
2. **减少分支**：GPU 分支（if/else）效率低，尽量用数学替代。
3. **减少纹理采样**：纹理采样开销大，合并采样。
4. **利用内置函数**：GLSL 内置函数通常有硬件加速。
5. **减少 uniform 设置**：批量设置，避免频繁更新。

**分支优化**：

GPU 的 SIMT 架构下，同一组的所有线程必须执行相同指令。if/else 会导致两组线程都执行，然后丢弃不需要的结果：

```glsl
// 慢：分支
if (x > 0) {
    result = a;
} else {
    result = b;
}

// 快：数学
result = mix(b, a, step(0, x));  // x>0 时返回 a，否则 b
```

**内置函数优化**：

```glsl
// 慢：手写
float clamp_val = max(min(val, 1.0), 0.0);

// 快：内置
float clamp_val = clamp(val, 0.0, 1.0);
```

GLSL 内置函数（clamp、mix、smoothstep、step）通常有硬件加速。

**纹理采样优化**：

```glsl
// 慢：多次采样
vec3 color = texture(tex, uv).rgb;
float alpha = texture(tex, uv).a;

// 快：一次采样
vec4 color = texture(tex, uv);
```

**Mipmap 优化**：

远处物体用低级别 Mipmap，减少纹理采样开销。确保启用 Mipmap：

```c
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
```

**计算优化**：

```glsl
// 慢：pow
float spec = pow(max(dot(N, H), 0.0), 32);

// 快：多次乘法（小指数时）
float d = max(dot(N, H), 0.0);
float spec = d * d * d * d * d;  // 等价于 pow(d, 5)
```

**预计算**：

把不变的计算移到顶点着色器或 CPU：

```glsl
// 顶点着色器：预计算
out vec3 worldPos;
void main() {
    worldPos = (model * vec4(aPos, 1)).xyz;  // 顶点着色器算一次
    // ...
}

// 片段着色器：用插值结果
in vec3 worldPos;
void main() {
    // 直接用 worldPos，不重新计算
}
```

**减少 discard**：

`discard` 会破坏早期深度测试，降低性能。尽量用 alpha blend 代替：

```glsl
// 慢：discard
if (color.a < 0.1) discard;

// 快：alpha blend
FragColor = color;  // 用混合，需启用 GL_BLEND
```

**Uniform Buffer Object（UBO）**：

大量 uniform 用 UBO 批量更新，比逐个设置快：

```c
GLuint ubo;
glGenBuffers(1, &ubo);
glBindBuffer(GL_UNIFORM_BUFFER, ubo);
glBufferData(GL_UNIFORM_BUFFER, sizeof(Matrices), NULL, GL_DYNAMIC_DRAW);
glBindBufferBase(GL_UNIFORM_BUFFER, 0, ubo);

// 更新
glBufferSubData(GL_UNIFORM_BUFFER, 0, sizeof(Matrices), &matrices);
```

着色器中：

```glsl
layout(std140, binding = 0) uniform Matrices {
    mat4 view;
    mat4 projection;
};
```

### 例子

```glsl
#version 330 core
// 优化后的光照着色器

in vec3 FragPos;
in vec3 Normal;
in vec2 TexCoord;

out vec4 FragColor;

uniform sampler2D diffuseMap;
uniform vec3 lightPos;
uniform vec3 viewPos;

void main() {
    // 一次纹理采样
    vec4 diffuseColor = texture(diffuseMap, TexCoord);

    // 用内置函数
    vec3 N = normalize(Normal);
    vec3 L = normalize(lightPos - FragPos);
    vec3 V = normalize(viewPos - FragPos);
    vec3 H = normalize(L + V);

    // 用 step 代替 if
    float facing = step(0.0, dot(N, L));
    float diff = max(dot(N, L), 0.0) * facing;

    // 用乘法代替 pow（shininess=8）
    float d = max(dot(N, H), 0.0);
    float d2 = d * d;
    float d4 = d2 * d2;
    float spec = d4 * d4 * facing;  // d^8

    vec3 result = (0.1 + diff + spec) * diffuseColor.rgb;
    FragColor = vec4(result, diffuseColor.a);
}
```

### 总结

- 着色器优化：减少计算、分支、纹理采样。
- **分支优化**：用 mix、step、clamp 等数学函数代替 if/else。
- **内置函数**有硬件加速，优先使用。
- **纹理采样**：合并为一次，启用 Mipmap。
- **预计算**：不变计算移到顶点着色器或 CPU。
- **UBO** 批量更新 uniform。
- **常见坑**：discard 破坏早期深度测试；过度优化降低可读性。

---

# 第 8 章 · 现代 OpenGL 实战

前 7 章学习了 OpenGL 的各个组件，本章把它们组装成完整的应用。从引擎架构设计开始，到资源管理器、调试与错误处理、跨平台部署。掌握这些，你就能构建一个像 Unity 或 Unreal 那样模块化、可扩展的 OpenGL 应用框架。

## 第 29 讲 · 引擎架构设计

### 概念

**引擎架构（Engine Architecture）** 是 OpenGL 应用的骨架——如何组织窗口、渲染、资源、场景、输入等模块，让代码清晰、可维护、可扩展。

好的架构让开发事半功倍——添加新功能不需修改旧代码，修复 bug 不会引入新问题。

### 原理

**分层架构**：

```
应用层（Application）
├── 场景层（Scene）
│   ├── 游戏对象（GameObject）
│   └── 组件（Component）
├── 渲染层（Renderer）
│   ├── 着色器管理（ShaderManager）
│   ├── 纹理管理（TextureManager）
│   └── 网格管理（MeshManager）
├── 资源层（ResourceManager）
├── 核心层（Core）
│   ├── 窗口（Window）
│   ├── 输入（Input）
│   └── 时间（Timer）
└── 平台层（Platform）
    ├── OpenGL 上下文
    └── 窗口系统
```

**核心模块**：

1. **Window**：封装 GLFW/SDL，管理窗口与 OpenGL 上下文。
2. **Renderer**：管理渲染状态、着色器、绘制命令。
3. **ResourceManager**：加载、缓存、释放资源（纹理、网格、着色器）。
4. **Scene**：管理游戏对象、场景图、更新与渲染顺序。
5. **Input**：处理键盘、鼠标、手柄输入。
6. **Timer**：固定时间步长、帧率统计。

**主循环**：

```cpp
while (running) {
    float dt = timer.get_frame_time();
    
    input.update();
    scene.update(dt);
    
    renderer.begin_frame();
    scene.render(renderer);
    renderer.end_frame();
    
    window.swap_buffers();
}
```

**场景图（Scene Graph）**：

用树形结构组织游戏对象，父子关系自动传递变换：

```cpp
class GameObject {
    std::vector<GameObject*> children;
    Transform transform;  // 本地变换
    mat4 world_transform;  // 世界变换（计算得到）

    void update(float dt) {
        // 更新自身
        for (auto* child : children) {
            child->world_transform = world_transform * child->transform;
            child->update(dt);
        }
    }
};
```

**组件系统（ECS）**：

现代引擎用 ECS（Entity-Component-System）代替继承：

```cpp
// 实体（Entity）：仅 ID
using Entity = uint32_t;

// 组件（Component）：纯数据
struct Transform { vec3 pos, rot, scale; };
struct Mesh { GLuint vao; int indexCount; };
struct Material { GLuint shader; GLuint texture; };

// 系统（System）：处理组件
class RenderSystem {
    void update(std::vector<Entity>& entities, ComponentManager& cm) {
        for (auto e : entities) {
            auto& t = cm.get<Transform>(e);
            auto& m = cm.get<Mesh>(e);
            auto& mat = cm.get<Material>(e);
            // 渲染
        }
    }
};
```

### 例子

```cpp
#include <GL/glew.h>
#include <GLFW/glfw3.h>
#include <glm/glm.hpp>
#include <vector>
#include <memory>

// ============ 核心层 ============

class Window {
public:
    GLFWwindow* window;
    int width, height;

    Window(int w, int h, const char* title) : width(w), height(h) {
        glfwInit();
        glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
        glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
        glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
        window = glfwCreateWindow(w, h, title, nullptr, nullptr);
        glfwMakeContextCurrent(window);
        glewInit();
    }

    ~Window() {
        glfwDestroyWindow(window);
        glfwTerminate();
    }

    bool should_close() { return glfwWindowShouldClose(window); }
    void swap_buffers() { glfwSwapBuffers(window); }
    void poll_events() { glfwPollEvents(); }
};

class Timer {
    float last_time = 0;
public:
    float get_frame_time() {
        float current = glfwGetTime();
        float dt = current - last_time;
        last_time = current;
        return dt;
    }
};

class Input {
public:
    bool is_key_pressed(int key) {
        return glfwGetKey(glfwGetCurrentContext(), key) == GLFW_PRESS;
    }
    void get_mouse_pos(double& x, double& y) {
        glfwGetCursorPos(glfwGetCurrentContext(), &x, &y);
    }
};

// ============ 渲染层 ============

class Shader {
public:
    GLuint program;
    Shader(const char* vs, const char* fs) {
        // 编译链接（省略，参考前文）
    }
    void use() { glUseProgram(program); }
    void set_mat4(const char* name, const glm::mat4& m) {
        glUniformMatrix4fv(glGetUniformLocation(program, name), 1, GL_FALSE, &m[0][0]);
    }
};

class Mesh {
public:
    GLuint VAO, VBO, EBO;
    int indexCount;
    // 创建与绘制（省略）
};

class Renderer {
public:
    void begin_frame() {
        glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
        glEnable(GL_DEPTH_TEST);
    }
    void end_frame() {}
    void draw(Mesh& mesh, Shader& shader, const glm::mat4& mvp) {
        shader.use();
        shader.set_mat4("mvp", mvp);
        glBindVertexArray(mesh.VAO);
        glDrawElements(GL_TRIANGLES, mesh.indexCount, GL_UNSIGNED_INT, 0);
    }
};

// ============ 应用层 ============

class Application {
public:
    Window window;
    Timer timer;
    Input input;
    Renderer renderer;

    Application() : window(800, 600, "OpenGL Engine") {}

    void run() {
        while (!window.should_close()) {
            float dt = timer.get_frame_time();
            window.poll_events();
            update(dt);
            render();
            window.swap_buffers();
        }
    }

    virtual void update(float dt) {}
    virtual void render() {
        renderer.begin_frame();
        // 子类实现
        renderer.end_frame();
    }
};

int main() {
    Application app;
    app.run();
    return 0;
}
```

### 总结

- 引擎架构分核心、渲染、资源、场景、应用层。
- **核心模块**：Window、Renderer、ResourceManager、Scene、Input、Timer。
- **主循环**：input → update → render → swap。
- **场景图**用树形结构组织对象，自动传递变换。
- **ECS** 用组件代替继承，更灵活。
- **常见坑**：模块耦合过紧；忘记资源释放。

---

## 第 30 讲 · 资源管理器

### 概念

**资源管理器（Resource Manager）** 统一管理纹理、网格、着色器等资源的加载、缓存、释放。它避免重复加载（节省内存与时间），统一资源生命周期（防止泄漏）。

好的资源管理器让开发者只需说"加载 texture.png"，无需关心是否已加载、如何缓存、何时释放。

### 原理

**资源管理器的职责**：

1. **加载**：从文件创建 OpenGL 对象（纹理、着色器、VAO）。
2. **缓存**：已加载的资源不重复加载。
3. **引用计数**：跟踪资源使用，无人用时释放。
4. **热重载**：文件修改时重新加载（开发用）。

**资源缓存**：

```cpp
template<typename T>
class ResourceCache {
    std::map<std::string, std::shared_ptr<T>> resources;

public:
    std::shared_ptr<T> load(const std::string& path) {
        auto it = resources.find(path);
        if (it != resources.end()) {
            return it->second;  // 已缓存
        }
        auto resource = std::make_shared<T>(path);  // 加载
        resources[path] = resource;
        return resource;
    }
};
```

**shared_ptr 自动释放**：

资源用 `shared_ptr` 管理，引用计数为 0 时自动调用析构函数释放 OpenGL 对象：

```cpp
class Texture {
public:
    GLuint id;
    Texture(const std::string& path) {
        // 加载图片，创建纹理
        glGenTextures(1, &id);
        // ...
    }
    ~Texture() {
        glDeleteTextures(1, &id);  // 自动释放
    }
};
```

**资源类型**：

```cpp
class ResourceManager {
    ResourceCache<Texture> textures;
    ResourceCache<Shader> shaders;
    ResourceCache<Mesh> meshes;

public:
    std::shared_ptr<Texture> load_texture(const std::string& path) {
        return textures.load(path);
    }
    std::shared_ptr<Shader> load_shader(const std::string& vs, const std::string& fs) {
        return shaders.load(vs + "|" + fs);
    }
    // ...
};
```

**异步加载**：

大资源（如大纹理）异步加载，避免卡顿：

```cpp
void load_async(const std::string& path, std::function<void(std::shared_ptr<T>)> callback) {
    std::thread([path, callback, this]() {
        auto resource = load(path);
        callback(resource);
    }).detach();
}
```

注意：OpenGL 调用必须在主线程，异步加载只能加载文件数据，OpenGL 对象创建需在主线程。

**资源生命周期**：

```
加载 → 使用 → 引用计数为 0 → 释放
```

**热重载**：

```cpp
class HotReloadManager {
    std::map<std::string, time_t> file_times;

    void check_reload() {
        for (auto& [path, time] : file_times) {
            time_t current = get_file_modified_time(path);
            if (current != time) {
                reload(path);
                time = current;
            }
        }
    }
};
```

### 例子

```cpp
#include <GL/glew.h>
#include <map>
#include <memory>
#include <string>

// 纹理资源
class Texture {
public:
    GLuint id;
    int width, height;

    Texture(const std::string& path) {
        // 用 stb_image 加载（省略）
        glGenTextures(1, &id);
        glBindTexture(GL_TEXTURE_2D, id);
        // glTexImage2D(...);
        glGenerateMipmap(GL_TEXTURE_2D);
    }

    ~Texture() {
        glDeleteTextures(1, &id);
    }

    void bind(int unit = 0) {
        glActiveTexture(GL_TEXTURE0 + unit);
        glBindTexture(GL_TEXTURE_2D, id);
    }
};

// 着色器资源
class Shader {
public:
    GLuint program;

    Shader(const std::string& vs_path, const std::string& fs_path) {
        // 编译链接（省略）
    }

    ~Shader() {
        glDeleteProgram(program);
    }

    void use() { glUseProgram(program); }
};

// 资源缓存
template<typename T>
class ResourceCache {
    std::map<std::string, std::shared_ptr<T>> resources;

public:
    template<typename... Args>
    std::shared_ptr<T> load(const std::string& key, Args&&... args) {
        auto it = resources.find(key);
        if (it != resources.end()) {
            return it->second;
        }
        auto resource = std::make_shared<T>(std::forward<Args>(args)...);
        resources[key] = resource;
        return resource;
    }

    void clear() { resources.clear(); }
};

// 资源管理器
class ResourceManager {
    ResourceCache<Texture> textures;
    ResourceCache<Shader> shaders;

public:
    std::shared_ptr<Texture> load_texture(const std::string& path) {
        return textures.load(path, path);
    }

    std::shared_ptr<Shader> load_shader(const std::string& vs, const std::string& fs) {
        return shaders.load(vs + "|" + fs, vs, fs);
    }

    void clear() {
        textures.clear();
        shaders.clear();
    }
};

// 使用
int main() {
    ResourceManager rm;

    auto tex1 = rm.load_texture("texture.png");
    auto tex2 = rm.load_texture("texture.png");  // 返回缓存，不重复加载
    // tex1 == tex2

    auto shader = rm.load_shader("vs.glsl", "fs.glsl");

    // 使用资源
    tex1->bind();
    shader->use();

    // 退出时自动释放
    return 0;
}
```

### 总结

- 资源管理器统一管理资源加载、缓存、释放。
- **缓存**避免重复加载，节省内存与时间。
- **shared_ptr** 自动释放，引用计数为 0 时调用析构。
- **异步加载**避免卡顿，但 OpenGL 调用需在主线程。
- **热重载**便于开发，文件修改时重新加载。
- **常见坑**：资源循环引用导致泄漏；异步加载在错误线程调用 OpenGL。

---

## 第 31 讲 · 调试与错误处理

### 概念

OpenGL 是状态机，错误不易察觉——一个错误的参数可能导致后续渲染全部异常，但程序不崩溃。**调试与错误处理**是 OpenGL 开发的必备技能，帮你快速定位问题。

现代 OpenGL 提供调试输出（Debug Output）扩展，让 GPU 主动报告错误与警告。

### 原理

**错误检查**：

```c
GLenum err = glGetError();
if (err != GL_NO_ERROR) {
    switch (err) {
        case GL_INVALID_ENUM: printf("Invalid enum\n"); break;
        case GL_INVALID_VALUE: printf("Invalid value\n"); break;
        case GL_INVALID_OPERATION: printf("Invalid operation\n"); break;
        case GL_OUT_OF_MEMORY: printf("Out of memory\n"); break;
    }
}
```

**宏简化错误检查**：

```c
#define GL_CHECK(x) \
    x; \
    { \
        GLenum err = glGetError(); \
        if (err != GL_NO_ERROR) \
            printf("GL Error %d at %s:%d\n", err, __FILE__, __LINE__); \
    }

// 使用
GL_CHECK(glBindTexture(GL_TEXTURE_2D, texture));
```

**调试输出（Debug Output）**：

OpenGL 4.3+ 提供调试输出，GPU 主动报告错误、警告、性能提示：

```c
void APIENTRY debug_callback(GLenum source, GLenum type, GLuint id,
                              GLenum severity, GLsizei length,
                              const GLchar* message, const void* userParam) {
    printf("GL Debug: %s\n", message);
}

glEnable(GL_DEBUG_OUTPUT);
glEnable(GL_DEBUG_OUTPUT_SYNCHRONOUS);
glDebugMessageCallback(debug_callback, nullptr);
glDebugMessageControl(GL_DONT_CARE, GL_DONT_CARE, GL_DONT_CARE, 0, nullptr, GL_TRUE);
```

**严重性级别**：

| 级别 | 说明 |
|------|------|
| GL_DEBUG_SEVERITY_HIGH | 错误，必须修复 |
| GL_DEBUG_SEVERITY_MEDIUM | 警告，建议修复 |
| GL_DEBUG_SEVERITY_LOW | 信息，可选 |
| GL_DEBUG_SEVERITY_NOTIFICATION | 通知，默认 |

**着色器编译错误检查**：

```c
GLuint compile_shader(GLenum type, const char* src) {
    GLuint shader = glCreateShader(type);
    glShaderSource(shader, 1, &src, nullptr);
    glCompileShader(shader);

    GLint success;
    glGetShaderiv(shader, GL_COMPILE_STATUS, &success);
    if (!success) {
        char log[512];
        glGetShaderInfoLog(shader, 512, nullptr, log);
        printf("Shader compile error: %s\n", log);
        glDeleteShader(shader);
        return 0;
    }
    return shader;
}
```

**程序链接错误检查**：

```c
GLint success;
glGetProgramiv(program, GL_LINK_STATUS, &success);
if (!success) {
    char log[512];
    glGetProgramInfoLog(program, 512, nullptr, log);
    printf("Program link error: %s\n", log);
}
```

**调试工具**：

1. **RenderDoc**：开源图形调试器，捕获帧、检查状态、查看纹理。
2. **gDEBugger**：商业工具，类似 RenderDoc。
3. **NVIDIA Nsight**：NVIDIA 的调试工具。
4. **apitrace**：记录 OpenGL 调用序列。

**着色器调试技巧**：

GLSL 没有 printf，调试用颜色输出：

```glsl
void main() {
    vec3 color = vec3(0);
    color.r = FragPos.x;  // 用红色显示 x 坐标
    color.g = Normal.y;   // 用绿色显示法线 y
    FragColor = vec4(color, 1.0);
}
```

### 例子

```cpp
#include <GL/glew.h>
#include <GLFW/glfw3.h>
#include <cstdio>

// 调试回调
void APIENTRY gl_debug_callback(GLenum source, GLenum type, GLuint id,
                                  GLenum severity, GLsizei length,
                                  const GLchar* message, const void* userParam) {
    const char* severity_str;
    switch (severity) {
        case GL_DEBUG_SEVERITY_HIGH: severity_str = "HIGH"; break;
        case GL_DEBUG_SEVERITY_MEDIUM: severity_str = "MEDIUM"; break;
        case GL_DEBUG_SEVERITY_LOW: severity_str = "LOW"; break;
        default: severity_str = "NOTIFY"; break;
    }
    printf("[GL %s] %s\n", severity_str, message);
}

// 错误检查宏
#define GL_CHECK(x) \
    do { \
        x; \
        GLenum err = glGetError(); \
        if (err != GL_NO_ERROR) \
            printf("GL Error 0x%x at %s:%d: %s\n", err, __FILE__, __LINE__, #x); \
    } while (0)

// 着色器编译（带错误检查）
GLuint compile_shader(GLenum type, const char* src) {
    GLuint shader = glCreateShader(type);
    glShaderSource(shader, 1, &src, nullptr);
    glCompileShader(shader);

    GLint success;
    glGetShaderiv(shader, GL_COMPILE_STATUS, &success);
    if (!success) {
        char log[1024];
        glGetShaderInfoLog(shader, 1024, nullptr, log);
        printf("Shader compile error:\n%s\n", log);
        glDeleteShader(shader);
        return 0;
    }
    return shader;
}

int main() {
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 4);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
    glfwWindowHint(GLFW_OPENGL_DEBUG_CONTEXT, GL_TRUE);  // 启用调试上下文
    GLFWwindow* w = glfwCreateWindow(800, 600, "Debug", nullptr, nullptr);
    glfwMakeContextCurrent(w);
    glewInit();

    // 注册调试回调
    glEnable(GL_DEBUG_OUTPUT);
    glEnable(GL_DEBUG_OUTPUT_SYNCHRONOUS);
    glDebugMessageCallback(gl_debug_callback, nullptr);

    // 测试错误
    GL_CHECK(glBindTexture(GL_TEXTURE_2D, 999));  // 无效纹理（应警告）

    // 测试着色器错误
    const char* bad_shader = "#version 330 core\nvoid main() { invalid_code }";
    compile_shader(GL_VERTEX_SHADER, bad_shader);

    while (!glfwWindowShouldClose(w)) {
        glfwPollEvents();
    }

    glfwTerminate();
    return 0;
}
```

### 总结

- OpenGL 错误需主动检查，`glGetError` 获取错误码。
- **调试输出**（OpenGL 4.3+）让 GPU 主动报告错误与警告。
- **宏 GL_CHECK** 简化错误检查，自动定位错误位置。
- **着色器编译/链接**必须检查错误并输出日志。
- **调试工具**：RenderDoc、Nsight、apitrace。
- **着色器调试**用颜色输出变量值。
- **常见坑**：忽略错误导致后续渲染异常；调试输出未启用。

---

## 第 32 讲 · 跨平台部署

### 概念

**跨平台部署** 让 OpenGL 应用在 Windows、Linux、macOS、移动端运行。OpenGL 本身是跨平台标准，但窗口创建、上下文管理、扩展加载需平台特定代码。

本章讲解如何用 GLFW/GLAD 等库实现跨平台，处理不同平台的差异。

### 原理

**跨平台库的选择**：

| 库 | 用途 | 平台 |
|----|------|------|
| GLFW | 窗口、输入、上下文 | Win/Linux/Mac |
| SDL2 | 窗口、输入、音频 | 全平台 |
| GLAD | OpenGL 扩展加载 | 全平台 |
| GLEW | OpenGL 扩展加载 | Win/Linux/Mac |
| glm | 数学库 | 全平台 |

**GLFW 跨平台窗口**：

```c
glfwInit();
glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
#ifdef __APPLE__
    glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);  // macOS 必需
#endif
glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
GLFWwindow* window = glfwCreateWindow(800, 600, "App", nullptr, nullptr);
glfwMakeContextCurrent(window);
```

**macOS 特殊处理**：

macOS 有特殊要求：

1. **Forward Compat**：必须启用 `GLFW_OPENGL_FORWARD_COMPAT`。
2. **OpenGL 版本**：macOS 最高支持 OpenGL 4.1（Apple 已弃用 OpenGL）。
3. **Retina**：高 DPI 屏幕需处理像素分辨率。

```c
#ifdef __APPLE__
    glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);
    glfwWindowHint(GLFW_COCOA_RETINA_FRAMEBUFFER, GL_TRUE);
#endif
```

**Retina 处理**：

```c
int width, height;
glfwGetFramebufferSize(window, &width, &height);
glViewport(0, 0, width, height);  // 用帧缓冲尺寸，非窗口尺寸
```

**Linux 特殊处理**：

Linux 用 GLX 创建 OpenGL 上下文，GLFW 已封装。需注意：

1. **显示服务器**：X11 或 Wayland。
2. **驱动**：Mesa（开源）或 NVIDIA 专有。

**Windows 特殊处理**：

Windows 用 WGL 创建上下文，GLFW 已封装。需注意：

1. **DLL**：opengl32.dll 是系统库，无需额外。
2. **GPU 驱动**：用户需安装 GPU 驱动。

**移动端（OpenGL ES）**：

移动端用 OpenGL ES（嵌入式子集），API 略有不同：

- 无 `glBegin/glEnd`，强制使用 VAO/VBO。
- 着色器语言是 GLSL ES（精度限定符）。
- 用 EGL 创建上下文。

```glsl
// GLSL ES
precision mediump float;  // 必需声明精度
void main() { ... }
```

**Web（WebGL）**：

Web 用 WebGL（基于 OpenGL ES），通过 Emscripten 编译 C++ 到 JavaScript：

```c
// C++ 代码
int main() {
    // OpenGL ES 代码
}

// 编译
emcc demo.cpp -o demo.html -s USE_WEBGL2=1
```

**构建系统**：

跨平台构建用 CMake：

```cmake
cmake_minimum_required(VERSION 3.10)
project(OpenGLApp)

set(CMAKE_CXX_STANDARD 17)

# 查找包
find_package(OpenGL REQUIRED)
find_package(glfw3 REQUIRED)

# 包含目录
include_directories(${OPENGL_INCLUDE_DIRS})

# 可执行文件
add_executable(app main.cpp)

# 链接库
target_link_libraries(app ${OPENGL_LIBRARIES} glfw)
```

### 例子

```cpp
#include <GL/glew.h>
#include <GLFW/glfw3.h>
#include <cstdio>

// 跨平台窗口创建
GLFWwindow* create_window(int width, int height, const char* title) {
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);

#ifdef __APPLE__
    glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);
    glfwWindowHint(GLFW_COCOA_RETINA_FRAMEBUFFER, GL_TRUE);
#endif

    GLFWwindow* window = glfwCreateWindow(width, height, title, nullptr, nullptr);
    if (!window) {
        printf("Failed to create window\n");
        glfwTerminate();
        return nullptr;
    }
    glfwMakeContextCurrent(window);

    glewExperimental = GL_TRUE;
    if (glewInit() != GLEW_OK) {
        printf("Failed to init GLEW\n");
        return nullptr;
    }

    // 处理 Retina
    int fbWidth, fbHeight;
    glfwGetFramebufferSize(window, &fbWidth, &fbHeight);
    glViewport(0, 0, fbWidth, fbHeight);

    return window;
}

int main() {
    GLFWwindow* window = create_window(800, 600, "Cross Platform");
    if (!window) return -1;

    // 输出平台信息
    printf("Vendor: %s\n", glGetString(GL_VENDOR));
    printf("Renderer: %s\n", glGetString(GL_RENDERER));
    printf("Version: %s\n", glGetString(GL_VERSION));
    printf("GLSL: %s\n", glGetString(GL_SHADING_LANGUAGE_VERSION));

    while (!glfwWindowShouldClose(window)) {
        if (glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
            glfwSetWindowShouldClose(window, true);

        // 处理窗口 resize
        int width, height;
        glfwGetFramebufferSize(window, &width, &height);
        glViewport(0, 0, width, height);

        glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT);

        glfwSwapBuffers(window);
        glfwPollEvents();
    }

    glfwTerminate();
    return 0;
}
```

**CMakeLists.txt**：

```cmake
cmake_minimum_required(VERSION 3.10)
project(OpenGLApp)

set(CMAKE_CXX_STANDARD 17)

find_package(OpenGL REQUIRED)
find_package(glew REQUIRED)
find_package(glfw3 REQUIRED)

add_executable(app main.cpp)

target_link_libraries(app
    ${OPENGL_LIBRARIES}
    GLEW::GLEW
    glfw
)
```

### 总结

- OpenGL 本身跨平台，窗口/上下文需平台特定代码。
- **GLFW** 封装跨平台窗口创建。
- **macOS**：需 Forward Compat，最高 OpenGL 4.1，处理 Retina。
- **Linux**：X11/Wayland，Mesa/NVIDIA 驱动。
- **移动端**：OpenGL ES，用 EGL，GLSL ES。
- **Web**：WebGL，用 Emscripten 编译。
- **CMake** 跨平台构建。
- **常见坑**：macOS 忘记 Forward Compat；Retina 未处理帧缓冲尺寸。

---

## 课程结语

恭喜你完成了《OpenGL》的全部 32 讲！让我们回顾这段旅程：

**第 1 章 入门基础**：理解 OpenGL 架构与状态机，搭建开发环境，创建窗口与上下文，绘制第一个三角形。

**第 2 章 图元与着色器**：掌握 VAO/VBO/EBO 管理，编写 GLSL 顶点与片段着色器，绘制各种图元。

**第 3 章 变换与坐标**：学习向量矩阵基础、平移旋转缩放、MVP 变换链、坐标系与投影。

**第 4 章 纹理与采样**：掌握纹理映射、UV 与过滤、Mipmap、纹理寻址与图集。

**第 5 章 光照与材质**：实现 Phong/Blinn-Phong 光照、多光源、材质系统、光照贴图与法线贴图。

**第 6 章 高级渲染**：掌握帧缓冲、后处理、阴影映射、HDR 与 Gamma 校正。

**第 7 章 性能与优化**：学习实例化、批处理、遮挡剔除、着色器优化。

**第 8 章 现代 OpenGL 实战**：构建引擎架构、资源管理器、调试工具、跨平台部署。

**下一步学习建议**：

1. **Vulkan**：学习下一代图形 API，更底层更高效。
2. **PBR（物理渲染）**：学习基于物理的着色，理解 BRDF、能量守恒。
3. **延迟渲染**：学习 G-Buffer、光照 pass，支持大量光源。
4. **GPU Driven Rendering**：学习 Compute Shader、Indirect Draw，最大化 GPU 并行。
5. **实战项目**：用所学知识开发一个完整的 3D 游戏或引擎。

OpenGL 是图形学的基础——理解它，你就理解了 GPU 渲染的本质。无论未来学习 Vulkan、DirectX 还是 Metal，OpenGL 的概念都是通用的。祝你在图形学的道路上越走越远！
