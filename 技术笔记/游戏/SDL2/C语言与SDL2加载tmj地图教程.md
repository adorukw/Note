# C语言与SDL2加载TMJ地图 系统教程

> 本教程以教科书形式，从基础到实战，系统讲解如何使用 C 语言和 SDL2 库解析 Tiled Map Editor 生成的 TMJ（JSON）地图文件，并实现完整的地图加载与渲染系统。

---

## 课程总览

- **预计讲数**：22讲（6章）
- **学习目标**：从零基础掌握使用 C 语言和 SDL2 库解析 TMJ 地图文件，实现完整的地图加载、渲染和交互系统
- **适合人群**：有 C 语言基础、想学习 2D 游戏开发的程序员
- **前置知识**：C 语言基本语法、指针与结构体、基本图形学概念
- **学习建议**：每讲包含「概念→原理→例子→总结」四部分，建议按顺序学习，每讲配合代码实操

---

## 详细章节目录

### 第1章：基础准备
- 第1讲：TMJ地图格式与Tiled编辑器
- 第2讲：开发环境搭建
- 第3讲：SDL2窗口与渲染器基础
- 第4讲：SDL2图像加载与纹理渲染

### 第2章：JSON解析基础
- 第5讲：cJSON库介绍与集成
- 第6讲：TMJ文件结构解析
- 第7讲：提取地图元数据

### 第3章：图层解析
- 第8讲：Tileset图块集解析
- 第9讲：Tile图层解析
- 第10讲：对象图层解析
- 第11讲：图像图层解析

### 第4章：地图渲染核心
- 第12讲：图块切割与纹理管理
- 第13讲：渲染Tile图层
- 第14讲：坐标系统与摄像机
- 第15讲：地图渲染优化

### 第5章：进阶功能
- 第16讲：动画图块处理
- 第17讲：对象交互与碰撞检测
- 第18讲：多图层叠加渲染
- 第19讲：地图属性与自定义数据

### 第6章：实战项目
- 第20讲：完整地图加载器封装
- 第21讲：角色在地图上移动
- 第22讲：综合实战：2D游戏地图系统

---

# 第1章：基础准备

## 第1讲：TMJ地图格式与Tiled编辑器

### 概念

TMJ（Tiled Map JSON）是 Tiled Map Editor 导出的一种地图文件格式，采用 JSON 文本格式存储。Tiled 是一款免费开源的 2D 地图编辑器，广泛用于游戏开发。TMJ 文件描述了地图的尺寸、图块集（Tileset）、图层（Layer）、对象（Object）等所有信息，是连接美术资源和游戏程序的桥梁。

### 原理

TMJ 文件本质是一个 JSON 对象，包含以下核心字段：

```json
{
  "width": 20,           // 地图宽度（图块数）
  "height": 15,          // 地图高度（图块数）
  "tilewidth": 32,       // 单个图块宽度（像素）
  "tileheight": 32,      // 单个图块高度（像素）
  "orientation": "orthogonal",  // 地图类型：正交/等距/六边形
  "renderorder": "right-down",  // 渲染顺序
  "tilesets": [...],     // 图块集数组
  "layers": [...]        // 图层数组
}
```

Tiled 编辑器支持三种主要图层类型：
1. **Tile Layer（图块图层）**：由图块ID组成的网格，用于绘制地形
2. **Object Layer（对象图层）**：存储任意形状的对象，用于触发器、碰撞体等
3. **Image Layer（图像图层）**：整张图片作为背景

TMJ 与 TMX 的区别：TMX 是 XML 格式，TMJ 是 JSON 格式。JSON 更易于程序解析，且 C 语言有成熟的 JSON 库（如 cJSON），因此本教程使用 TMJ 格式。

### 例子

**一个最简单的 TMJ 文件示例：**

```json
{
  "compressionlevel": -1,
  "width": 5,
  "height": 5,
  "tilewidth": 32,
  "tileheight": 32,
  "orientation": "orthogonal",
  "renderorder": "right-down",
  "tilesets": [
    {
      "firstgid": 1,
      "source": "tiles.tsx"
    }
  ],
  "layers": [
    {
      "type": "tilelayer",
      "name": "Ground",
      "width": 5,
      "height": 5,
      "data": [1, 2, 3, 2, 1, 2, 3, 1, 1, 3, 3, 1, 2, 3, 1, 2, 3, 1, 2, 3, 1, 2, 3, 1, 2]
    }
  ]
}
```

**地图结构示意图（5×5 正交地图）：**

```
列号:  0  1  2  3  4
行号0: [1][2][3][2][1]
行号1: [2][3][1][1][3]
行号2: [3][1][2][3][1]
行号3: [2][3][1][2][3]
行号4: [1][2][3][1][2]

每个数字代表一个图块ID（gid）
0 表示空白（无图块）
```

**在 Tiled 中创建地图的步骤：**

1. 打开 Tiled → 文件 → 新建地图
2. 设置地图尺寸（如 20×15）和图块大小（如 32×32）
3. 选择"正交"方向
4. 创建图块集：地图 → 新图块集 → 选择图片
5. 在图块图层上绘制地图
6. 文件 → 导出为 → 选择 JSON 格式（.tmj）

### 总结

- TMJ 是 Tiled Map Editor 导出的 JSON 格式地图文件
- TMJ 包含地图尺寸、图块大小、图块集、图层等核心信息
- 三种图层类型：Tile Layer（地形）、Object Layer（对象）、Image Layer（背景图）
- TMJ 相比 TMX（XML）更易于 C 语言解析
- 图块ID（gid）是标识每个图块的全局唯一编号，0 表示空白
- 学习 TMJ 格式是开发 2D 游戏地图系统的基础
- 建议先用 Tiled 编辑器手动创建几个简单地图，熟悉格式后再编程解析

---

## 第2讲：开发环境搭建

### 概念

本讲搭建 C 语言 + SDL2 + cJSON 的开发环境。SDL2（Simple DirectMedia Layer 2）是一个跨平台的多媒体库，提供窗口、图形渲染、输入处理等功能。cJSON 是一个轻量级的 C 语言 JSON 解析库，用于解析 TMJ 文件。三者结合即可实现完整的地图加载系统。

### 原理

**技术栈组成：**

```
┌─────────────────────────────────────┐
│         你的游戏程序（C代码）          │
├─────────────┬───────────────────────┤
│   SDL2      │      cJSON            │
│ (渲染图形)   │   (解析TMJ文件)        │
├─────────────┼───────────────────────┤
│  OpenGL/    │    标准C库             │
│  DirectX    │   (文件IO/内存)        │
├─────────────┴───────────────────────┤
│         操作系统（Windows/Linux）     │
└─────────────────────────────────────┘
```

**SDL2 核心模块：**
- `SDL.h`：核心功能（窗口、事件）
- `SDL_image.h`：图像加载（PNG、JPG等）
- `SDL_render.h`：2D 渲染器

**cJSON 特点：**
- 单文件库（cJSON.c + cJSON.h）
- 纯 C 实现，无依赖
- MIT 许可证，可商用

**编译流程：**
```bash
# Linux/MacOS 编译命令
gcc main.c cJSON.c -o game \
    $(sdl2-config --cflags --libs) \
    -lSDL2_image -lm

# Windows (MinGW)
gcc main.c cJSON.c -o game.exe \
    -I SDL2/include -L SDL2/lib \
    -lSDL2 -lSDL2_image -lm
```

### 例子

**Linux 环境安装（Ubuntu/Debian）：**

```bash
# 安装 SDL2 核心库和扩展库
sudo apt-get update
sudo apt-get install libsdl2-dev libsdl2-image-dev

# 安装编译工具
sudo apt-get install build-essential

# 验证安装
sdl2-config --version
# 输出类似: 2.0.20

# 获取编译参数
sdl2-config --cflags --libs
# 输出类似: -I/usr/include/SDL2 -D_REENTRANT -lSDL2
```

**获取 cJSON 库：**

```bash
# 方式1: git clone
git clone https://github.com/DaveGamble/cJSON.git

# 方式2: 直接下载两个文件
# cJSON.h 和 cJSON.c 放到项目目录
```

**项目目录结构：**

```
my_game/
├── main.c              # 主程序
├── cJSON.h             # JSON解析库头文件
├── cJSON.c             # JSON解析库源文件
├── assets/             # 资源目录
│   ├── map.tmj         # 地图文件
│   ├── tiles.png       # 图块图片
│   └── player.png      # 角色图片
├── Makefile            # 构建脚本
└── README.md
```

**Makefile 示例：**

```makefile
# Makefile
CC = gcc
CFLAGS = -Wall -Wextra -g $(shell sdl2-config --cflags)
LDFLAGS = $(shell sdl2-config --libs) -lSDL2_image -lm

SRCS = main.c cJSON.c
OBJS = $(SRCS:.c=.o)
TARGET = game

all: $(TARGET)

$(TARGET): $(OBJS)
        $(CC) $(OBJS) -o $(TARGET) $(LDFLAGS)

%.o: %.c
        $(CC) $(CFLAGS) -c $< -o $@

clean:
        rm -f $(OBJS) $(TARGET)

run: $(TARGET)
        ./$(TARGET)

.PHONY: all clean run
```

**第一个 SDL2 程序测试：**

```c
// main.c - 环境测试
#include <SDL2/SDL.h>
#include <SDL2/SDL_image.h>
#include <stdio.h>

int main(int argc, char* argv[]) {
    (void)argc; (void)argv;
    
    // 初始化SDL
    if (SDL_Init(SDL_INIT_VIDEO) != 0) {
        printf("SDL初始化失败: %s\n", SDL_GetError());
        return 1;
    }
    
    // 初始化SDL_image
    int imgFlags = IMG_INIT_PNG;
    if (!(IMG_Init(imgFlags) & imgFlags)) {
        printf("SDL_image初始化失败: %s\n", IMG_GetError());
        SDL_Quit();
        return 1;
    }
    
    printf("SDL2版本: %s\n", SDL_GetRevision());
    printf("SDL_image版本: %d.%d.%d\n", 
           SDL_IMAGE_MAJOR_VERSION,
           SDL_IMAGE_MINOR_VERSION,
           SDL_IMAGE_PATCHLEVEL);
    printf("环境搭建成功！\n");
    
    IMG_Quit();
    SDL_Quit();
    return 0;
}
```

```bash
# 编译运行
make
./game
# 输出:
# SDL2版本: SDL 2.0.20
# SDL_image版本: 2.0.5
# 环境搭建成功！
```

### 总结

- 开发环境三件套：SDL2（图形）、SDL2_image（图像加载）、cJSON（JSON解析）
- Linux 用 `apt-get install libsdl2-dev libsdl2-image-dev` 安装
- cJSON 是单文件库，直接将 cJSON.c 和 cJSON.h 加入项目即可
- 使用 Makefile 管理编译，`sdl2-config` 工具自动获取编译参数
- 项目结构建议：源码、资源、构建脚本分目录管理
- 编译时必须链接 `-lSDL2 -lSDL2_image -lm`
- Windows 环境需下载 SDL2 开发包，配置 include 和 lib 路径
- 环境搭建是第一步，确保能编译运行"Hello SDL"程序后再继续

---

## 第3讲：SDL2窗口与渲染器基础

### 概念

SDL2 的核心是窗口（Window）和渲染器（Renderer）。窗口是显示在屏幕上的矩形区域，渲染器是将图形绘制到窗口的引擎。理解窗口和渲染器的创建、配置和使用，是 SDL2 编程的基础。本讲创建一个能显示窗口并处理基本事件的程序。

### 原理

**SDL2 渲染流程：**

```
创建窗口 → 创建渲染器 → 清空渲染器 → 绘制内容 → 呈现渲染器
   ↑                                              |
   └──────────── 事件循环（重复）  ←──────────────┘
```

**关键概念：**

1. **Window（窗口）**：操作系统层面的窗口，有标题、位置、大小
2. **Renderer（渲染器）**：绑定到窗口的绘图上下文，负责实际渲染
3. **Texture（纹理）**：存储在显存中的图像数据，可高效绘制
4. **Surface（表面）**：存储在内存中的图像数据，CPU 可直接访问

**渲染器加速：**
- `SDL_RENDERER_ACCELERATED`：使用 GPU 硬件加速
- `SDL_RENDERER_SOFTWARE`：纯软件渲染（慢）
- `SDL_RENDERER_PRESENTVSYNC`：垂直同步，防止画面撕裂

**事件驱动模型：**
SDL2 采用事件驱动模型，所有用户输入（键盘、鼠标）都通过事件队列传递。程序在主循环中不断轮询事件并处理。

**颜色与清屏：**
渲染器维护一个"当前颜色"，`SDL_SetRenderDrawColor` 设置颜色，`SDL_RenderClear` 用当前颜色清屏。

### 例子

**创建窗口和渲染器：**

```c
// window_demo.c
#include <SDL2/SDL.h>
#include <stdio.h>

#define WINDOW_WIDTH  800
#define WINDOW_HEIGHT 600

int main(int argc, char* argv[]) {
    (void)argc; (void)argv;
    
    // 1. 初始化SDL视频子系统
    if (SDL_Init(SDL_INIT_VIDEO) < 0) {
        printf("SDL初始化失败: %s\n", SDL_GetError());
        return 1;
    }
    
    // 2. 创建窗口
    SDL_Window* window = SDL_CreateWindow(
        "SDL2 窗口示例",           // 窗口标题
        SDL_WINDOWPOS_CENTERED,    // X位置（居中）
        SDL_WINDOWPOS_CENTERED,    // Y位置（居中）
        WINDOW_WIDTH,              // 宽度
        WINDOW_HEIGHT,             // 高度
        SDL_WINDOW_SHOWN           // 显示窗口
    );
    
    if (window == NULL) {
        printf("窗口创建失败: %s\n", SDL_GetError());
        SDL_Quit();
        return 1;
    }
    
    // 3. 创建渲染器（硬件加速 + 垂直同步）
    SDL_Renderer* renderer = SDL_CreateRenderer(
        window,                    // 目标窗口
        -1,                        // 自动选择渲染驱动
        SDL_RENDERER_ACCELERATED | SDL_RENDERER_PRESENTVSYNC
    );
    
    if (renderer == NULL) {
        printf("渲染器创建失败: %s\n", SDL_GetError());
        SDL_DestroyWindow(window);
        SDL_Quit();
        return 1;
    }
    
    // 4. 设置渲染器逻辑尺寸（可选，方便坐标计算）
    SDL_RenderSetLogicalSize(renderer, WINDOW_WIDTH, WINDOW_HEIGHT);
    
    // 5. 主循环
    int running = 1;
    SDL_Event event;
    
    while (running) {
        // 事件处理
        while (SDL_PollEvent(&event)) {
            if (event.type == SDL_QUIT) {
                running = 0;
            }
            if (event.type == SDL_KEYDOWN) {
                if (event.key.keysym.sym == SDLK_ESCAPE) {
                    running = 0;
                }
            }
        }
        
        // 清屏（深蓝色背景）
        SDL_SetRenderDrawColor(renderer, 30, 30, 60, 255);
        SDL_RenderClear(renderer);
        
        // 绘制内容
        // 画一个红色矩形
        SDL_SetRenderDrawColor(renderer, 255, 0, 0, 255);
        SDL_Rect rect = {100, 100, 200, 150};
        SDL_RenderFillRect(renderer, &rect);
        
        // 画一个绿色矩形边框
        SDL_SetRenderDrawColor(renderer, 0, 255, 0, 255);
        SDL_Rect outline = {350, 100, 200, 150};
        SDL_RenderDrawRect(renderer, &outline);
        
        // 画一条线
        SDL_SetRenderDrawColor(renderer, 255, 255, 0, 255);
        SDL_RenderDrawLine(renderer, 100, 350, 700, 350);
        
        // 呈现
        SDL_RenderPresent(renderer);
    }
    
    // 6. 清理资源
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    
    return 0;
}
```

**编译运行：**

```bash
gcc window_demo.c -o window_demo $(sdl2-config --cflags --libs)
./window_demo
```

**程序运行效果示意图：**

```
┌──────────────────────────────────────┐
│  SDL2 窗口示例                       │
├──────────────────────────────────────┤
│                                      │
│   ┌─────────┐   ┌─────────┐         │
│   │ 红色    │   │ 绿色    │         │
│   │ 实心    │   │ 边框    │         │
│   └─────────┘   └─────────┘         │
│                                      │
│   ─────────────────────────          │ ← 黄色线
│                                      │
│         (深蓝色背景)                  │
└──────────────────────────────────────┘
```

**SDL_Rect 结构详解：**

```c
typedef struct {
    int x;      // 左上角X坐标
    int y;      // 左上角Y坐标
    int w;      // 宽度
    int h;      // 高度
} SDL_Rect;

// 坐标系：左上角为原点(0,0)，X向右增大，Y向下增大
/*
(0,0) ────────→ X
  │
  │
  │
  ↓
  Y
*/
```

### 总结

- SDL2 渲染三要素：Window（窗口）、Renderer（渲染器）、Texture（纹理）
- 创建顺序：`SDL_Init` → `SDL_CreateWindow` → `SDL_CreateRenderer`
- 主循环结构：事件处理 → 清屏 → 绘制 → 呈现（`SDL_RenderPresent`）
- 渲染器建议使用 `SDL_RENDERER_ACCELERATED` 硬件加速
- `SDL_SetRenderDrawColor` 设置颜色，影响后续 Clear/FillRect/DrawRect
- SDL 坐标系：左上角为原点，X 向右，Y 向下
- 程序结束前必须释放资源：`SDL_DestroyRenderer` → `SDL_DestroyWindow` → `SDL_Quit`
- 事件循环中 `SDL_QUIT` 处理窗口关闭，`SDL_KEYDOWN` 处理按键
- ESC 键退出是游戏的标准操作，建议始终实现

---

## 第4讲：SDL2图像加载与纹理渲染

### 概念

纹理（Texture）是 SDL2 中存储在显存中的图像数据，是渲染的核心对象。本讲学习如何使用 SDL2_image 库加载 PNG/JPG 图片文件，创建纹理，并将纹理绘制到屏幕上。这是渲染地图图块的基础——TMJ 地图本质上就是将图块图片切割后按数据绘制。

### 原理

**图像加载流程：**

```
图片文件(PNG) → SDL_Surface(内存) → SDL_Texture(显存) → 渲染到屏幕
                  ↑ CPU访问            ↑ GPU访问，高效
```

**Surface vs Texture：**
- **Surface**：存储在系统内存，CPU 可直接读写像素，但渲染慢
- **Texture**：存储在显存，GPU 加速渲染，但 CPU 不能直接读写
- 实际开发中，加载后立即转为 Texture，Surface 用完即释放

**SDL2_image 支持的格式：**
- PNG（推荐，支持透明）
- JPG（照片，不支持透明）
- BMP（无压缩，SDL原生支持）
- GIF、TIF、WEBP 等

**纹理渲染函数：**

| 函数 | 用途 |
|------|------|
| `SDL_QueryTexture` | 查询纹理宽高 |
| `SDL_RenderCopy` | 绘制整个纹理 |
| `SDL_RenderCopyEx` | 绘制纹理（支持旋转、翻转） |

**源矩形与目标矩形：**

```c
// SDL_RenderCopy 的参数
int SDL_RenderCopy(
    SDL_Renderer* renderer,   // 渲染器
    SDL_Texture* texture,     // 纹理
    const SDL_Rect* srcrect,  // 源矩形（从纹理的哪部分取，NULL=全部）
    const SDL_Rect* dstrect   // 目标矩形（画到屏幕的哪个位置和大小）
);
```

**透明色处理：**
- PNG 自带 alpha 通道，直接支持透明
- 对于无 alpha 的图片，可用 `SDL_SetColorKey` 设置透明色

### 例子

**加载并绘制图片：**

```c
// image_demo.c
#include <SDL2/SDL.h>
#include <SDL2/SDL_image.h>
#include <stdio.h>

#define WINDOW_WIDTH  800
#define WINDOW_HEIGHT 600

// 加载图片为纹理的辅助函数
SDL_Texture* loadTexture(const char* path, SDL_Renderer* renderer) {
    SDL_Texture* texture = NULL;
    
    // 方式1: 通过Surface加载（兼容性好）
    SDL_Surface* loadedSurface = IMG_Load(path);
    if (loadedSurface == NULL) {
        printf("无法加载图片 %s: %s\n", path, IMG_GetError());
        return NULL;
    }
    
    // 设置透明色（可选，针对无alpha通道的图片）
    // SDL_SetColorKey(loadedSurface, SDL_TRUE, 
    //     SDL_MapRGB(loadedSurface->format, 0xFF, 0x00, 0xFF));  // 紫色透明
    
    // 转换为纹理
    texture = SDL_CreateTextureFromSurface(renderer, loadedSurface);
    if (texture == NULL) {
        printf("无法创建纹理: %s\n", SDL_GetError());
    }
    
    // 释放Surface
    SDL_FreeSurface(loadedSurface);
    
    return texture;
}

// 方式2: 直接加载为纹理（SDL2_image 2.0+，更高效）
SDL_Texture* loadTextureDirect(const char* path, SDL_Renderer* renderer) {
    SDL_Texture* texture = IMG_LoadTexture(renderer, path);
    if (texture == NULL) {
        printf("无法加载纹理 %s: %s\n", path, IMG_GetError());
    }
    return texture;
}

int main(int argc, char* argv[]) {
    (void)argc; (void)argv;
    
    SDL_Init(SDL_INIT_VIDEO);
    IMG_Init(IMG_INIT_PNG);
    
    SDL_Window* window = SDL_CreateWindow("图片加载示例",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED,
        WINDOW_WIDTH, WINDOW_HEIGHT, SDL_WINDOW_SHOWN);
    
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1,
        SDL_RENDERER_ACCELERATED);
    
    // 加载图片
    SDL_Texture* texture = loadTexture("assets/player.png", renderer);
    if (texture == NULL) {
        printf("请确保 assets/player.png 存在\n");
        // 如果没有图片，我们创建一个纯色纹理作为替代
        texture = SDL_CreateTexture(renderer, SDL_PIXELFORMAT_RGBA8888,
            SDL_TEXTUREACCESS_TARGET, 64, 64);
        SDL_SetRenderTarget(renderer, texture);
        SDL_SetRenderDrawColor(renderer, 255, 100, 100, 255);
        SDL_RenderClear(renderer);
        SDL_SetRenderTarget(renderer, NULL);
    }
    
    // 查询纹理尺寸
    int texWidth, texHeight;
    SDL_QueryTexture(texture, NULL, NULL, &texWidth, &texHeight);
    printf("纹理尺寸: %d x %d\n", texWidth, texHeight);
    
    // 主循环
    int running = 1;
    SDL_Event event;
    int posX = 100, posY = 100;
    
    while (running) {
        // 事件处理
        while (SDL_PollEvent(&event)) {
            if (event.type == SDL_QUIT) running = 0;
            if (event.type == SDL_KEYDOWN) {
                switch (event.key.keysym.sym) {
                    case SDLK_ESCAPE: running = 0; break;
                    case SDLK_LEFT:  posX -= 5; break;
                    case SDLK_RIGHT: posX += 5; break;
                    case SDLK_UP:    posY -= 5; break;
                    case SDLK_DOWN:  posY += 5; break;
                }
            }
        }
        
        // 清屏
        SDL_SetRenderDrawColor(renderer, 50, 50, 80, 255);
        SDL_RenderClear(renderer);
        
        // 绘制整个纹理到指定位置
        SDL_Rect dstRect = {posX, posY, texWidth, texHeight};
        SDL_RenderCopy(renderer, texture, NULL, &dstRect);
        
        // 绘制纹理的一部分（放大2倍）
        SDL_Rect srcRect = {0, 0, texWidth/2, texHeight/2};  // 左上角1/4
        SDL_Rect dstRect2 = {400, 100, texWidth, texHeight};  // 放大到原大小
        SDL_RenderCopy(renderer, texture, &srcRect, &dstRect2);
        
        // 使用RenderCopyEx旋转绘制
        SDL_Rect dstRect3 = {500, 300, texWidth, texHeight};
        SDL_RenderCopyEx(renderer, texture, NULL, &dstRect3,
            45.0,           // 旋转45度
            NULL,           // 旋转中心（NULL=中心）
            SDL_FLIP_NONE); // 翻转（NONE/HORIZONTAL/VERTICAL）
        
        SDL_RenderPresent(renderer);
    }
    
    // 清理
    SDL_DestroyTexture(texture);
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    IMG_Quit();
    SDL_Quit();
    
    return 0;
}
```

**源矩形与目标矩形示意图：**

```
纹理图片（256x256）              屏幕
┌───────────────────┐           
│ ┌───────┐         │   srcRect  ┌──────────────┐
│ │srcRect│         │  ───────→  │   dstRect    │
│ │ 64x64 │         │   放大2倍  │   128x128    │
│ └───────┘         │            │              │
│                   │            └──────────────┘
└───────────────────┘
   srcrect指定        dstrect指定
   从纹理取哪部分      画到屏幕哪里、多大
```

**资源管理最佳实践：**

```c
// 纹理缓存结构（后续地图加载器会用到）
typedef struct {
    char name[64];        // 资源名
    SDL_Texture* texture; // 纹理
    int width;            // 宽度
    int height;           // 高度
} TextureCache;

// 全局纹理缓存数组
#define MAX_TEXTURES 32
TextureCache g_textureCache[MAX_TEXTURES];
int g_textureCount = 0;

// 加载纹理到缓存
int cacheTexture(const char* name, const char* path, SDL_Renderer* renderer) {
    if (g_textureCount >= MAX_TEXTURES) return -1;
    
    SDL_Texture* tex = IMG_LoadTexture(renderer, path);
    if (!tex) return -1;
    
    strncpy(g_textureCache[g_textureCount].name, name, 63);
    g_textureCache[g_textureCount].texture = tex;
    SDL_QueryTexture(tex, NULL, NULL, 
        &g_textureCache[g_textureCount].width,
        &g_textureCache[g_textureCount].height);
    
    return g_textureCount++;
}

// 从缓存获取纹理
SDL_Texture* getTexture(const char* name) {
    for (int i = 0; i < g_textureCount; i++) {
        if (strcmp(g_textureCache[i].name, name) == 0) {
            return g_textureCache[i].texture;
        }
    }
    return NULL;
}

// 释放所有缓存纹理
void freeAllTextures() {
    for (int i = 0; i < g_textureCount; i++) {
        SDL_DestroyTexture(g_textureCache[i].texture);
    }
    g_textureCount = 0;
}
```

### 总结

- 纹理（Texture）存储在显存，GPU 加速渲染，是 SDL2 绘图的核心
- 加载流程：`IMG_Load`（Surface）→ `SDL_CreateTextureFromSurface`（Texture），或直接 `IMG_LoadTexture`
- `SDL_RenderCopy(renderer, texture, srcrect, dstrect)` 是最常用的绘制函数
- srcrect 指定从纹理取哪部分（NULL=全部），dstrect 指定画到屏幕哪里多大
- `SDL_RenderCopyEx` 支持旋转、翻转，用于角色朝向等效果
- `SDL_QueryTexture` 查询纹理尺寸，加载后应保存避免频繁查询
- PNG 格式自带透明通道，是游戏资源的首选格式
- 纹理使用后必须 `SDL_DestroyTexture` 释放，否则内存泄漏
- 建议实现纹理缓存系统，避免重复加载同一图片
- 源矩形（srcrect）是后续切割图块集的关键概念

---

# 第2章：JSON解析基础

## 第5讲：cJSON库介绍与集成

### 概念

cJSON 是一个轻量级的 C 语言 JSON 解析库，由 Dave Gamble 开发。它采用 MIT 许可证，整个库只有 `cJSON.c` 和 `cJSON.h` 两个文件，零依赖，非常适合嵌入到项目中。cJSON 将 JSON 文本解析为内存中的树形结构（cJSON 节点树），程序通过遍历这棵树来读取 JSON 数据。本讲学习 cJSON 的基本用法。

### 原理

**cJSON 数据结构：**

```c
typedef struct cJSON {
    struct cJSON *next;        // 下一个兄弟节点
    struct cJSON *prev;        // 上一个兄弟节点
    struct cJSON *child;       // 第一个子节点
    int type;                  // 节点类型（Object/Array/String/Number等）
    char *valuestring;         // 字符串值
    int valueint;              // 整数值
    double valuedouble;        // 浮点数值
    char *string;              // 键名（如果是对象的成员）
} cJSON;
```

**JSON 类型与 cJSON 类型对应：**

| JSON 类型 | cJSON 常量 | 说明 |
|-----------|------------|------|
| 对象 `{}` | `cJSON_Object` | 键值对集合 |
| 数组 `[]` | `cJSON_Array` | 有序列表 |
| 字符串 | `cJSON_String` | `valuestring` 字段 |
| 数字 | `cJSON_Number` | `valuedouble`/`valueint` 字段 |
| 布尔 | `cJSON_True`/`cJSON_False` | |
| null | `cJSON_NULL` | |

**cJSON 解析流程：**

```
JSON文本 → cJSON_Parse() → cJSON节点树 → 程序读取 → cJSON_Delete()释放
```

**内存管理原则：**
- `cJSON_Parse` 返回的根节点必须用 `cJSON_Delete` 释放
- `cJSON_Print` 返回的字符串必须用 `free` 释放
- cJSON 内部自动管理子节点内存，只需释放根节点

### 例子

**基本解析示例：**

```c
// cJSON_demo.c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "cJSON.h"

int main() {
    // 示例JSON文本
    const char* json_text = 
        "{"
        "  \"name\": \"TMJ地图\","
        "  \"width\": 20,"
        "  \"height\": 15,"
        "  \"tilewidth\": 32,"
        "  \"tileheight\": 32,"
        "  \"orientation\": \"orthogonal\","
        "  \"infinite\": false,"
        "  \"layers\": ["
        "    {\"name\": \"Ground\", \"type\": \"tilelayer\", \"visible\": true},"
        "    {\"name\": \"Objects\", \"type\": \"objectgroup\", \"visible\": true}"
        "  ]"
        "}";
    
    // 1. 解析JSON文本
    cJSON* root = cJSON_Parse(json_text);
    if (root == NULL) {
        const char* error_ptr = cJSON_GetErrorPtr();
        if (error_ptr != NULL) {
            printf("解析错误: %s\n", error_ptr);
        }
        return 1;
    }
    
    // 2. 读取字符串值
    cJSON* name = cJSON_GetObjectItem(root, "name");
    if (cJSON_IsString(name)) {
        printf("地图名称: %s\n", name->valuestring);
    }
    
    // 3. 读取整数值
    cJSON* width = cJSON_GetObjectItem(root, "width");
    cJSON* height = cJSON_GetObjectItem(root, "height");
    if (cJSON_IsNumber(width) && cJSON_IsNumber(height)) {
        printf("地图尺寸: %d x %d\n", width->valueint, height->valueint);
    }
    
    // 4. 读取布尔值
    cJSON* infinite = cJSON_GetObjectItem(root, "infinite");
    if (cJSON_IsBool(infinite)) {
        printf("是否无限地图: %s\n", cJSON_IsTrue(infinite) ? "是" : "否");
    }
    
    // 5. 读取数组
    cJSON* layers = cJSON_GetObjectItem(root, "layers");
    if (cJSON_IsArray(layers)) {
        int layerCount = cJSON_GetArraySize(layers);
        printf("图层数量: %d\n", layerCount);
        
        // 遍历数组
        cJSON* layer;
        cJSON_ArrayForEach(layer, layers) {
            cJSON* layerName = cJSON_GetObjectItem(layer, "name");
            cJSON* layerType = cJSON_GetObjectItem(layer, "type");
            printf("  - 图层: %s (类型: %s)\n", 
                layerName->valuestring, 
                layerType->valuestring);
        }
    }
    
    // 6. 释放根节点（自动释放所有子节点）
    cJSON_Delete(root);
    
    return 0;
}
```

**编译运行：**

```bash
gcc cJSON_demo.c cJSON.c -o cJSON_demo -lm
./cJSON_demo
/*
输出:
地图名称: TMJ地图
地图尺寸: 20 x 15
是否无限地图: 否
图层数量: 2
  - 图层: Ground (类型: tilelayer)
  - 图层: Objects (类型: objectgroup)
*/
```

**从文件读取 JSON：**

```c
// file_json.c - 从文件读取并解析JSON
#include <stdio.h>
#include <stdlib.h>
#include "cJSON.h"

// 读取整个文件到字符串
char* readFileToString(const char* filename) {
    FILE* file = fopen(filename, "rb");
    if (file == NULL) {
        printf("无法打开文件: %s\n", filename);
        return NULL;
    }
    
    // 获取文件大小
    fseek(file, 0, SEEK_END);
    long length = ftell(file);
    fseek(file, 0, SEEK_SET);
    
    // 分配内存
    char* buffer = (char*)malloc(length + 1);
    if (buffer == NULL) {
        fclose(file);
        return NULL;
    }
    
    // 读取内容
    size_t read = fread(buffer, 1, length, file);
    buffer[read] = '\0';
    
    fclose(file);
    return buffer;
}

int main() {
    // 读取TMJ文件
    char* json_text = readFileToString("assets/map.tmj");
    if (json_text == NULL) {
        return 1;
    }
    
    // 解析JSON
    cJSON* root = cJSON_Parse(json_text);
    if (root == NULL) {
        printf("JSON解析失败\n");
        free(json_text);
        return 1;
    }
    
    // 使用数据...
    cJSON* width = cJSON_GetObjectItem(root, "width");
    cJSON* height = cJSON_GetObjectItem(root, "height");
    printf("地图: %d x %d\n", width->valueint, height->valueint);
    
    // 清理
    cJSON_Delete(root);
    free(json_text);
    
    return 0;
}
```

**cJSON 常用 API 速查：**

```c
// 解析与序列化
cJSON* cJSON_Parse(const char* json);           // 文本→节点树
char* cJSON_Print(cJSON* item);                  // 节点树→格式化文本
char* cJSON_PrintUnformatted(cJSON* item);       // 节点树→紧凑文本

// 获取子项
cJSON* cJSON_GetObjectItem(cJSON* obj, const char* key);  // 按键名获取
cJSON* cJSON_GetArrayItem(cJSON* arr, int index);          // 按索引获取
int cJSON_GetArraySize(cJSON* arr);                        // 数组长度

// 类型判断
cJSON_bool cJSON_IsObject(cJSON* item);
cJSON_bool cJSON_IsArray(cJSON* item);
cJSON_bool cJSON_IsString(cJSON* item);
cJSON_bool cJSON_IsNumber(cJSON* item);
cJSON_bool cJSON_IsBool(cJSON* item);
cJSON_bool cJSON_IsNull(cJSON* item);

// 数组遍历宏
cJSON_ArrayForEach(element, array) { ... }

// 内存管理
void cJSON_Delete(cJSON* item);  // 释放节点树
```

### 总结

- cJSON 是单文件 C 语言 JSON 库，零依赖，适合嵌入项目
- `cJSON_Parse` 将 JSON 文本解析为节点树，`cJSON_Delete` 释放
- `cJSON_GetObjectItem` 按键名获取对象成员，`cJSON_GetArrayItem` 按索引获取数组元素
- 读取值前用 `cJSON_IsString`/`cJSON_IsNumber` 等判断类型，避免空指针
- `cJSON_ArrayForEach` 宏简化数组遍历
- 从文件读取 JSON：`fopen` → `fseek/ftell` 获取大小 → `fread` 读取 → `cJSON_Parse`
- 内存管理：只需 `cJSON_Delete(root)` 释放根节点，子节点自动释放
- `cJSON_Print` 返回的字符串需手动 `free`
- 解析失败时用 `cJSON_GetErrorPtr` 获取错误位置
- cJSON 是解析 TMJ 文件的核心工具，后续所有地图数据提取都基于它

---

## 第6讲：TMJ文件结构解析

### 概念

TMJ 文件有固定的结构层次。本讲详细分析 TMJ 文件的完整结构，包括地图根属性、tilesets 数组、layers 数组等。理解 TMJ 的整体结构是正确解析地图的前提。我们将用一个真实的 TMJ 文件作为示例，逐层剖析其组成。

### 原理

**TMJ 文件整体结构：**

```
TMJ根对象
├── 地图属性
│   ├── width, height              地图尺寸（图块数）
│   ├── tilewidth, tileheight      图块像素尺寸
│   ├── orientation                方向（orthogonal/isometric/hexagonal）
│   ├── renderorder                渲染顺序（right-down/right-up/left-down/left-up）
│   ├── infinite                   是否无限地图
│   ├── compressionlevel           压缩级别
│   ├── backgroundcolor            背景色
│   └── nextlayerid, nextobjectid  下一个ID
│
├── tilesets[]                     图块集数组
│   └── 每个tileset
│       ├── firstgid               第一个图块的全局ID
│       ├── source                 外部TSX文件路径（可选）
│       ├── name                   图块集名称
│       ├── tilewidth, tileheight  图块尺寸
│       ├── tilecount              图块总数
│       ├── columns                列数
│       ├── image                  图片路径
│       ├── imagewidth, imageheight 图片尺寸
│       └── tiles[]                特殊图块属性（动画等）
│
├── layers[]                       图层数组
│   ├── tilelayer（图块图层）
│   │   ├── name, type, visible, opacity
│   │   ├── width, height
│   │   ├── x, y                   偏移
│   │   └── data[]                 图块ID数组
│   │
│   ├── objectgroup（对象图层）
│   │   ├── name, type, visible
│   │   └── objects[]
│   │       ├── name, type
│   │       ├── x, y, width, height
│   │       ├── rotation
│   │       ├── gid                （如果是图块对象）
│   │       └── properties[]       自定义属性
│   │
│   └── imagelayer（图像图层）
│       ├── name, type, visible
│       ├── image                  图片路径
│       └── x, y                   偏移
│
└── properties[]                   地图自定义属性
```

**全局图块ID（gid）机制：**
- 每个图块在所有 tileset 中有一个全局唯一 ID
- 第一个 tileset 的 `firstgid` 通常为 1
- 后续 tileset 的 `firstgid` = 前一个 tileset 的 `firstgid + tilecount`
- gid = 0 表示空白（无图块）
- gid 有标志位：`gid & 0x80000000`（水平翻转）、`gid & 0x40000000`（垂直翻转）、`gid & 0x20000000`（对角翻转）
- 实际图块ID = `gid & 0x1FFFFFFF`（清除标志位）

**渲染顺序（renderorder）：**

```
right-down（默认）:     right-up:            left-down:            left-up:
→ → → →                → → → →              ← ← ← ←              ← ← ← ←
→ → → →                ↑ ↑ ↑ ↑              ← ← ← ←              ↑ ↑ ↑ ↑
→ → → →                ↑ ↑ ↑ ↑              ← ← ← ←              ↑ ↑ ↑ ↑
↓ ↓ ↓ ↓                                     ↓ ↓ ↓ ↓
```

### 例子

**完整 TMJ 文件示例：**

```json
{
  "compressionlevel": -1,
  "width": 10,
  "height": 8,
  "tilewidth": 32,
  "tileheight": 32,
  "orientation": "orthogonal",
  "renderorder": "right-down",
  "backgroundcolor": "#000000",
  "infinite": false,
  "tilesets": [
    {
      "firstgid": 1,
      "source": "tileset.tsx"
    }
  ],
  "layers": [
    {
      "type": "tilelayer",
      "name": "Background",
      "x": 0, "y": 0,
      "width": 10,
      "height": 8,
      "visible": true,
      "opacity": 1,
      "data": [
        1, 1, 1, 1, 1, 1, 1, 1, 1, 1,
        1, 2, 2, 2, 2, 2, 2, 2, 2, 1,
        1, 2, 3, 3, 3, 3, 3, 3, 2, 1,
        1, 2, 3, 4, 4, 4, 4, 3, 2, 1,
        1, 2, 3, 4, 5, 5, 4, 3, 2, 1,
        1, 2, 3, 4, 4, 4, 4, 3, 2, 1,
        1, 2, 2, 2, 2, 2, 2, 2, 2, 1,
        1, 1, 1, 1, 1, 1, 1, 1, 1, 1
      ]
    },
    {
      "type": "objectgroup",
      "name": "Collisions",
      "visible": true,
      "objects": [
        {
          "name": "Wall",
          "type": "collision",
          "x": 32, "y": 32,
          "width": 256, "height": 32
        },
        {
          "name": "Door",
          "type": "trigger",
          "x": 160, "y": 192,
          "width": 32, "height": 32,
          "properties": [
            {"name": "target", "type": "string", "value": "level2"}
          ]
        }
      ]
    }
  ],
  "properties": [
    {"name": "bgm", "type": "string", "value": "forest.mp3"},
    {"name": "level", "type": "int", "value": 1}
  ]
}
```

**解析 TMJ 根属性的代码：**

```c
// parse_tmj_structure.c
#include <stdio.h>
#include <stdlib.h>
#include "cJSON.h"

char* readFileToString(const char* filename) {
    FILE* file = fopen(filename, "rb");
    if (!file) return NULL;
    fseek(file, 0, SEEK_END);
    long length = ftell(file);
    fseek(file, 0, SEEK_SET);
    char* buffer = malloc(length + 1);
    fread(buffer, 1, length, file);
    buffer[length] = '\0';
    fclose(file);
    return buffer;
}

int main() {
    char* json_text = readFileToString("assets/map.tmj");
    if (!json_text) {
        printf("无法读取地图文件\n");
        return 1;
    }
    
    cJSON* root = cJSON_Parse(json_text);
    if (!root) {
        printf("JSON解析失败\n");
        free(json_text);
        return 1;
    }
    
    // 打印地图基本信息
    printf("=== 地图基本信息 ===\n");
    printf("尺寸: %d x %d 图块\n",
        cJSON_GetObjectItem(root, "width")->valueint,
        cJSON_GetObjectItem(root, "height")->valueint);
    printf("图块大小: %d x %d 像素\n",
        cJSON_GetObjectItem(root, "tilewidth")->valueint,
        cJSON_GetObjectItem(root, "tileheight")->valueint);
    printf("方向: %s\n",
        cJSON_GetObjectItem(root, "orientation")->valuestring);
    printf("渲染顺序: %s\n",
        cJSON_GetObjectItem(root, "renderorder")->valuestring);
    
    cJSON* bgColor = cJSON_GetObjectItem(root, "backgroundcolor");
    if (bgColor) {
        printf("背景色: %s\n", bgColor->valuestring);
    }
    
    // 打印tileset信息
    printf("\n=== 图块集 ===\n");
    cJSON* tilesets = cJSON_GetObjectItem(root, "tilesets");
    cJSON* tileset;
    cJSON_ArrayForEach(tileset, tilesets) {
        printf("firstgid: %d", 
            cJSON_GetObjectItem(tileset, "firstgid")->valueint);
        
        cJSON* source = cJSON_GetObjectItem(tileset, "source");
        if (source) {
            printf(" (外部: %s)\n", source->valuestring);
        } else {
            cJSON* name = cJSON_GetObjectItem(tileset, "name");
            cJSON* image = cJSON_GetObjectItem(tileset, "image");
            printf(" (内嵌: %s, 图片: %s)\n",
                name ? name->valuestring : "?",
                image ? image->valuestring : "?");
        }
    }
    
    // 打印图层信息
    printf("\n=== 图层 ===\n");
    cJSON* layers = cJSON_GetObjectItem(root, "layers");
    cJSON* layer;
    cJSON_ArrayForEach(layer, layers) {
        const char* type = cJSON_GetObjectItem(layer, "type")->valuestring;
        const char* name = cJSON_GetObjectItem(layer, "name")->valuestring;
        cJSON* visible = cJSON_GetObjectItem(layer, "visible");
        
        printf("[%s] %s (可见: %s)\n", 
            type, name, 
            (visible && cJSON_IsTrue(visible)) ? "是" : "否");
        
        if (strcmp(type, "tilelayer") == 0) {
            cJSON* data = cJSON_GetObjectItem(layer, "data");
            if (cJSON_IsArray(data)) {
                printf("  图块数据: %d 个\n", cJSON_GetArraySize(data));
            }
        } else if (strcmp(type, "objectgroup") == 0) {
            cJSON* objects = cJSON_GetObjectItem(layer, "objects");
            if (cJSON_IsArray(objects)) {
                printf("  对象数: %d\n", cJSON_GetArraySize(objects));
            }
        }
    }
    
    cJSON_Delete(root);
    free(json_text);
    return 0;
}
```

### 总结

- TMJ 根对象包含：地图属性、tilesets 数组、layers 数组、properties 数组
- tileset 的 `firstgid` 是全局图块ID的起始值，用于区分不同图块集
- 图层有三种类型：tilelayer（图块）、objectgroup（对象）、imagelayer（图像）
- gid（全局图块ID）= 0 表示空白，非零值需清除标志位后才是实际ID
- gid 标志位：0x80000000（水平翻转）、0x40000000（垂直翻转）、0x20000000（对角翻转）
- 渲染顺序影响图块的绘制顺序，默认 right-down（从左到右，从上到下）
- tileset 可以是内嵌的（直接在 TMJ 中定义）或外部的（引用 .tsx 文件）
- properties 数组存储自定义属性，用于扩展地图功能
- 理解 TMJ 结构是编写地图解析器的基础，建议对照真实文件学习

---

## 第7讲：提取地图元数据

### 概念

地图元数据是描述地图基本信息的数据，包括地图尺寸、图块大小、方向、渲染顺序等。这些数据是加载和渲染地图的基础参数。本讲学习如何用 cJSON 从 TMJ 文件中提取这些元数据，并封装成 C 语言结构体，为后续的图层解析和渲染做准备。

### 原理

**元数据结构设计原则：**
1. **完整性**：包含渲染所需的所有基本信息
2. **类型安全**：用枚举代替字符串，避免拼写错误
3. **封装性**：将相关数据组织在结构体中，便于传递
4. **默认值**：对可选字段提供合理默认值

**需要提取的元数据：**

| 字段 | 类型 | 说明 | 用途 |
|------|------|------|------|
| width | int | 地图宽（图块数） | 计算坐标 |
| height | int | 地图高（图块数） | 计算坐标 |
| tilewidth | int | 图块宽（像素） | 切割图块 |
| tileheight | int | 图块高（像素） | 切割图块 |
| orientation | enum | 方向 | 渲染算法选择 |
| renderorder | enum | 渲染顺序 | 绘制顺序 |
| infinite | bool | 是否无限地图 | 数据存储方式 |
| layerCount | int | 图层数量 | 遍历图层 |
| tilesetCount | int | 图块集数量 | 加载图块集 |

**枚举设计的好处：**
- 比字符串比较更高效（整数比较 vs 字符串比较）
- 避免拼写错误
- 便于 switch 语句处理

### 例子

**定义地图元数据结构体：**

```c
// map_metadata.h
#ifndef MAP_METADATA_H
#define MAP_METADATA_H

#include "cJSON.h"

// 地图方向
typedef enum {
    ORTHOGONAL,     // 正交（默认）
    ISOMETRIC,      // 等距
    HEXAGONAL,      // 六边形
    STAGGERED       // 交错
} MapOrientation;

// 渲染顺序
typedef enum {
    RENDER_RIGHT_DOWN,  // 从左到右，从上到下（默认）
    RENDER_RIGHT_UP,    // 从左到右，从下到上
    RENDER_LEFT_DOWN,   // 从右到左，从上到下
    RENDER_LEFT_UP      // 从右到左，从下到上
} RenderOrder;

// 地图元数据
typedef struct {
    int width;              // 地图宽度（图块数）
    int height;             // 地图高度（图块数）
    int tileWidth;          // 图块宽度（像素）
    int tileHeight;         // 图块高度（像素）
    MapOrientation orientation;
    RenderOrder renderOrder;
    int infinite;           // 是否无限地图
    char backgroundColor[16]; // 背景色（如 "#000000"）
    int layerCount;         // 图层数量
    int tilesetCount;       // 图块集数量
} MapMetadata;

// 函数声明
int parseMapMetadata(const char* tmjPath, MapMetadata* metadata);
MapOrientation parseOrientation(const char* str);
RenderOrder parseRenderOrder(const char* str);
void printMapMetadata(const MapMetadata* metadata);

#endif
```

**实现元数据解析：**

```c
// map_metadata.c
#include "map_metadata.h"
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// 读取文件到字符串
static char* readFileToString(const char* filename) {
    FILE* file = fopen(filename, "rb");
    if (!file) return NULL;
    fseek(file, 0, SEEK_END);
    long length = ftell(file);
    fseek(file, 0, SEEK_SET);
    char* buffer = malloc(length + 1);
    if (buffer) {
        size_t read = fread(buffer, 1, length, file);
        buffer[read] = '\0';
    }
    fclose(file);
    return buffer;
}

// 解析方向字符串为枚举
MapOrientation parseOrientation(const char* str) {
    if (!str) return ORTHOGONAL;
    if (strcmp(str, "isometric") == 0) return ISOMETRIC;
    if (strcmp(str, "hexagonal") == 0) return HEXAGONAL;
    if (strcmp(str, "staggered") == 0) return STAGGERED;
    return ORTHOGONAL;  // 默认正交
}

// 解析渲染顺序字符串为枚举
RenderOrder parseRenderOrder(const char* str) {
    if (!str) return RENDER_RIGHT_DOWN;
    if (strcmp(str, "right-up") == 0) return RENDER_RIGHT_UP;
    if (strcmp(str, "left-down") == 0) return RENDER_LEFT_DOWN;
    if (strcmp(str, "left-up") == 0) return RENDER_LEFT_UP;
    return RENDER_RIGHT_DOWN;  // 默认
}

// 解析背景色（如 "#3366AA" → 保存原字符串）
static void parseBackgroundColor(cJSON* root, MapMetadata* metadata) {
    cJSON* bg = cJSON_GetObjectItem(root, "backgroundcolor");
    if (bg && cJSON_IsString(bg)) {
        strncpy(metadata->backgroundColor, bg->valuestring, 15);
        metadata->backgroundColor[15] = '\0';
    } else {
        strcpy(metadata->backgroundColor, "#000000");  // 默认黑色
    }
}

// 将十六进制颜色字符串转为SDL_Color
// 输入: "#RRGGBB" 或 "#RRGGBBAA"
void hexToColor(const char* hex, Uint8* r, Uint8* g, Uint8* b, Uint8* a) {
    if (!hex || hex[0] != '#') {
        *r = *g = *b = 0; *a = 255;
        return;
    }
    hex++;  // 跳过#
    *r = (Uint8)strtol(hex, NULL, 16);
    *g = (Uint8)strtol(hex + 2, NULL, 16);
    *b = (Uint8)strtol(hex + 4, NULL, 16);
    *a = (strlen(hex) >= 8) ? (Uint8)strtol(hex + 6, NULL, 16) : 255;
}

// 解析地图元数据
int parseMapMetadata(const char* tmjPath, MapMetadata* metadata) {
    char* jsonText = readFileToString(tmjPath);
    if (!jsonText) {
        printf("错误: 无法读取文件 %s\n", tmjPath);
        return -1;
    }
    
    cJSON* root = cJSON_Parse(jsonText);
    if (!root) {
        printf("错误: JSON解析失败\n");
        free(jsonText);
        return -1;
    }
    
    // 设置默认值
    memset(metadata, 0, sizeof(MapMetadata));
    metadata->orientation = ORTHOGONAL;
    metadata->renderOrder = RENDER_RIGHT_DOWN;
    metadata->infinite = 0;
    strcpy(metadata->backgroundColor, "#000000");
    
    // 提取基本属性
    cJSON* item;
    
    item = cJSON_GetObjectItem(root, "width");
    if (cJSON_IsNumber(item)) metadata->width = item->valueint;
    
    item = cJSON_GetObjectItem(root, "height");
    if (cJSON_IsNumber(item)) metadata->height = item->valueint;
    
    item = cJSON_GetObjectItem(root, "tilewidth");
    if (cJSON_IsNumber(item)) metadata->tileWidth = item->valueint;
    
    item = cJSON_GetObjectItem(root, "tileheight");
    if (cJSON_IsNumber(item)) metadata->tileHeight = item->valueint;
    
    item = cJSON_GetObjectItem(root, "orientation");
    if (cJSON_IsString(item)) {
        metadata->orientation = parseOrientation(item->valuestring);
    }
    
    item = cJSON_GetObjectItem(root, "renderorder");
    if (cJSON_IsString(item)) {
        metadata->renderOrder = parseRenderOrder(item->valuestring);
    }
    
    item = cJSON_GetObjectItem(root, "infinite");
    if (cJSON_IsBool(item)) {
        metadata->infinite = cJSON_IsTrue(item) ? 1 : 0;
    }
    
    parseBackgroundColor(root, metadata);
    
    // 统计图层数量
    cJSON* layers = cJSON_GetObjectItem(root, "layers");
    if (cJSON_IsArray(layers)) {
        metadata->layerCount = cJSON_GetArraySize(layers);
    }
    
    // 统计图块集数量
    cJSON* tilesets = cJSON_GetObjectItem(root, "tilesets");
    if (cJSON_IsArray(tilesets)) {
        metadata->tilesetCount = cJSON_GetArraySize(tilesets);
    }
    
    cJSON_Delete(root);
    free(jsonText);
    
    return 0;
}

// 打印元数据（调试用）
void printMapMetadata(const MapMetadata* metadata) {
    printf("=== 地图元数据 ===\n");
    printf("尺寸: %d x %d 图块\n", metadata->width, metadata->height);
    printf("图块大小: %d x %d 像素\n", metadata->tileWidth, metadata->tileHeight);
    printf("像素总尺寸: %d x %d\n", 
        metadata->width * metadata->tileWidth,
        metadata->height * metadata->tileHeight);
    
    const char* orientStr[] = {"正交", "等距", "六边形", "交错"};
    printf("方向: %s\n", orientStr[metadata->orientation]);
    
    const char* renderStr[] = {"右下", "右上", "左下", "左上"};
    printf("渲染顺序: %s\n", renderStr[metadata->renderOrder]);
    
    printf("无限地图: %s\n", metadata->infinite ? "是" : "否");
    printf("背景色: %s\n", metadata->backgroundColor);
    printf("图层数: %d\n", metadata->layerCount);
    printf("图块集数: %d\n", metadata->tilesetCount);
}
```

**使用示例：**

```c
// main.c
#include "map_metadata.h"
#include <stdio.h>

int main() {
    MapMetadata metadata;
    
    if (parseMapMetadata("assets/map.tmj", &metadata) != 0) {
        printf("解析失败\n");
        return 1;
    }
    
    printMapMetadata(&metadata);
    
    // 计算地图总像素尺寸
    int pixelWidth = metadata.width * metadata.tileWidth;
    int pixelHeight = metadata.height * metadata.tileHeight;
    printf("\n地图总像素: %d x %d\n", pixelWidth, pixelHeight);
    
    // 根据方向选择渲染算法
    switch (metadata.orientation) {
        case ORTHOGONAL:
            printf("使用正交渲染算法\n");
            break;
        case ISOMETRIC:
            printf("使用等距渲染算法（需要菱形坐标转换）\n");
            break;
        default:
            printf("暂不支持的地图方向\n");
    }
    
    return 0;
}
```

**输出示例：**

```
=== 地图元数据 ===
尺寸: 20 x 15 图块
图块大小: 32 x 32 像素
像素总尺寸: 640 x 480
方向: 正交
渲染顺序: 右下
无限地图: 否
背景色: #3366AA
图层数: 3
图块集数: 2

地图总像素: 640 x 480
使用正交渲染算法
```

### 总结

- 元数据结构体封装了地图的基本信息，是地图加载的第一步
- 使用枚举代替字符串存储方向和渲染顺序，提高效率和安全性
- 解析时先设置默认值，再从 JSON 覆盖，确保字段始终有有效值
- `cJSON_IsNumber`/`cJSON_IsString` 等类型检查防止空指针崩溃
- 背景色格式为 "#RRGGBB" 或 "#RRGGBBAA"，需转换为数值使用
- 元数据是后续图层解析和渲染的基础参数，应提前提取并缓存
- `memset` 初始化结构体为 0 是良好的安全实践
- 打印函数（printMapMetadata）便于调试，建议每个解析模块都提供
- 元数据解析成功后，就可以开始解析 tilesets 和 layers 了

---

# 第3章：图层解析

## 第8讲：Tileset图块集解析

### 概念

Tileset（图块集）是 TMJ 地图中图块图片的集合。一个地图可以引用多个 tileset，每个 tileset 包含一张图片（通常是 PNG）和图块的切割信息。Tileset 定义了图块的全局ID范围、图片路径、图块尺寸、间距等。本讲学习如何解析 tileset 数据，为后续渲染图块做准备。

### 原理

**Tileset 的关键字段：**

| 字段 | 类型 | 说明 |
|------|------|------|
| firstgid | int | 该图块集第一个图块的全局ID |
| name | string | 图块集名称 |
| image | string | 图片文件路径 |
| imagewidth | int | 图片宽度（像素） |
| imageheight | int | 图片高度（像素） |
| tilewidth | int | 单个图块宽度 |
| tileheight | int | 单个图块高度 |
| tilecount | int | 图块总数 |
| columns | int | 每行图块数 |
| margin | int | 图片边缘留白（像素） |
| spacing | int | 图块间距（像素） |

**全局ID与本地ID的转换：**

```
全局ID（gid）= firstgid + 本地ID（localId）

本地ID = 全局ID - firstgid

图块在图片中的位置：
列号 col = localId % columns
行号 row = localId / columns

像素坐标：
srcX = margin + col * (tilewidth + spacing)
srcY = margin + row * (tileheight + spacing)
```

**Tileset 的两种形式：**
1. **内嵌式**：tileset 信息直接写在 TMJ 文件中
2. **外部引用式**：TMJ 中只有 `firstgid` 和 `source`（指向 .tsx 文件），需要额外解析 TSX

**margin 和 spacing 示意图：**

```
图片:
┌─ margin ─────────────────────┐
│ ┌──┐ spacing ┌──┐            │
│ │T1│         │T2│   ...      │
│ └──┘         └──┘            │
│      spacing                 │
│ ┌──┐         ┌──┐            │
│ │T3│         │T4│   ...      │
│ └──┘         └──┘            │
└──────────────────────────────┘
```

### 例子

**定义 Tileset 结构体：**

```c
// tileset.h
#ifndef TILESET_H
#define TILESET_H

#include "cJSON.h"
#include <SDL2/SDL.h>

#define MAX_TILESETS 16
#define MAX_PATH_LEN 256

typedef struct {
    int firstgid;           // 第一个图块的全局ID
    char name[64];          // 图块集名称
    char image[MAX_PATH_LEN]; // 图片路径
    int imageWidth;         // 图片宽度
    int imageHeight;        // 图片高度
    int tileWidth;          // 图块宽度
    int tileHeight;         // 图块高度
    int tileCount;          // 图块总数
    int columns;            // 每行列数
    int margin;             // 边缘留白
    int spacing;            // 图块间距
    SDL_Texture* texture;   // 加载的SDL纹理
} Tileset;

// 从cJSON节点解析单个tileset
int parseTileset(cJSON* tilesetNode, Tileset* tileset, 
                 SDL_Renderer* renderer, const char* basePath);

// 根据全局ID查找图块所属的tileset
Tileset* findTilesetByGid(Tileset* tilesets, int count, int gid);

// 根据全局ID计算图块在图片中的源矩形
void getTileSourceRect(Tileset* tileset, int gid, SDL_Rect* srcRect);

// 释放tileset资源
void freeTileset(Tileset* tileset);

#endif
```

**实现 Tileset 解析：**

```c
// tileset.c
#include "tileset.h"
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// 辅助函数：拼接路径（basePath + relativePath）
static void joinPath(char* dest, const char* basePath, const char* relPath) {
    if (relPath[0] == '/' || strstr(relPath, ":")) {
        // 绝对路径，直接使用
        strncpy(dest, relPath, MAX_PATH_LEN - 1);
    } else {
        // 相对路径，拼接basePath
        snprintf(dest, MAX_PATH_LEN, "%s/%s", basePath, relPath);
    }
}

// 从cJSON节点解析单个tileset
int parseTileset(cJSON* tilesetNode, Tileset* tileset, 
                 SDL_Renderer* renderer, const char* basePath) {
    memset(tileset, 0, sizeof(Tileset));
    tileset->texture = NULL;
    
    // 解析firstgid（必需）
    cJSON* firstgid = cJSON_GetObjectItem(tilesetNode, "firstgid");
    if (!cJSON_IsNumber(firstgid)) {
        printf("错误: tileset缺少firstgid\n");
        return -1;
    }
    tileset->firstgid = firstgid->valueint;
    
    // 解析name
    cJSON* name = cJSON_GetObjectItem(tilesetNode, "name");
    if (cJSON_IsString(name)) {
        strncpy(tileset->name, name->valuestring, 63);
    }
    
    // 解析图片路径
    cJSON* image = cJSON_GetObjectItem(tilesetNode, "image");
    if (cJSON_IsString(image)) {
        joinPath(tileset->image, basePath, image->valuestring);
    }
    
    // 解析图片尺寸
    cJSON* imgW = cJSON_GetObjectItem(tilesetNode, "imagewidth");
    cJSON* imgH = cJSON_GetObjectItem(tilesetNode, "imageheight");
    if (cJSON_IsNumber(imgW)) tileset->imageWidth = imgW->valueint;
    if (cJSON_IsNumber(imgH)) tileset->imageHeight = imgH->valueint;
    
    // 解析图块尺寸
    cJSON* tw = cJSON_GetObjectItem(tilesetNode, "tilewidth");
    cJSON* th = cJSON_GetObjectItem(tilesetNode, "tileheight");
    if (cJSON_IsNumber(tw)) tileset->tileWidth = tw->valueint;
    if (cJSON_IsNumber(th)) tileset->tileHeight = th->valueint;
    
    // 解析图块数量和列数
    cJSON* tc = cJSON_GetObjectItem(tilesetNode, "tilecount");
    cJSON* cols = cJSON_GetObjectItem(tilesetNode, "columns");
    if (cJSON_IsNumber(tc)) tileset->tileCount = tc->valueint;
    if (cJSON_IsNumber(cols)) tileset->columns = cols->valueint;
    
    // 解析margin和spacing（可选，默认0）
    cJSON* margin = cJSON_GetObjectItem(tilesetNode, "margin");
    cJSON* spacing = cJSON_GetObjectItem(tilesetNode, "spacing");
    if (cJSON_IsNumber(margin)) tileset->margin = margin->valueint;
    if (cJSON_IsNumber(spacing)) tileset->spacing = spacing->valueint;
    
    // 如果没有columns，根据图片宽度和图块宽度计算
    if (tileset->columns == 0 && tileset->tileWidth > 0) {
        tileset->columns = (tileset->imageWidth - tileset->margin * 2 + tileset->spacing) 
                         / (tileset->tileWidth + tileset->spacing);
    }
    
    // 如果没有tileCount，根据图片尺寸计算
    if (tileset->tileCount == 0 && tileset->columns > 0) {
        int rows = (tileset->imageHeight - tileset->margin * 2 + tileset->spacing)
                 / (tileset->tileHeight + tileset->spacing);
        tileset->tileCount = tileset->columns * rows;
    }
    
    // 加载纹理
    if (renderer && tileset->image[0]) {
        tileset->texture = IMG_LoadTexture(renderer, tileset->image);
        if (!tileset->texture) {
            printf("警告: 无法加载图块集图片 %s: %s\n", 
                tileset->image, IMG_GetError());
        }
    }
    
    return 0;
}

// 根据全局ID查找图块所属的tileset
Tileset* findTilesetByGid(Tileset* tilesets, int count, int gid) {
    // 清除标志位
    gid &= 0x1FFFFFFF;
    if (gid == 0) return NULL;
    
    // 从后往前找，找到第一个firstgid <= gid的
    for (int i = count - 1; i >= 0; i--) {
        if (gid >= tilesets[i].firstgid) {
            return &tilesets[i];
        }
    }
    return NULL;
}

// 根据全局ID计算图块在图片中的源矩形
void getTileSourceRect(Tileset* tileset, int gid, SDL_Rect* srcRect) {
    // 清除标志位，计算本地ID
    int localId = (gid & 0x1FFFFFFF) - tileset->firstgid;
    
    // 计算行列号
    int col = localId % tileset->columns;
    int row = localId / tileset->columns;
    
    // 计算像素坐标（考虑margin和spacing）
    srcRect->x = tileset->margin + col * (tileset->tileWidth + tileset->spacing);
    srcRect->y = tileset->margin + row * (tileset->tileHeight + tileset->spacing);
    srcRect->w = tileset->tileWidth;
    srcRect->h = tileset->tileHeight;
}

// 释放tileset资源
void freeTileset(Tileset* tileset) {
    if (tileset->texture) {
        SDL_DestroyTexture(tileset->texture);
        tileset->texture = NULL;
    }
}

// 打印tileset信息（调试用）
void printTileset(const Tileset* tileset) {
    printf("=== Tileset: %s ===\n", tileset->name);
    printf("  firstgid: %d\n", tileset->firstgid);
    printf("  image: %s\n", tileset->image);
    printf("  图片尺寸: %d x %d\n", tileset->imageWidth, tileset->imageHeight);
    printf("  图块尺寸: %d x %d\n", tileset->tileWidth, tileset->tileHeight);
    printf("  图块数: %d (列数: %d)\n", tileset->tileCount, tileset->columns);
    printf("  margin: %d, spacing: %d\n", tileset->margin, tileset->spacing);
    printf("  纹理: %s\n", tileset->texture ? "已加载" : "未加载");
}
```

**使用示例：**

```c
// main.c
#include "tileset.h"
#include <stdio.h>

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    IMG_Init(IMG_INIT_PNG);
    
    SDL_Window* window = SDL_CreateWindow("Tileset Test",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 640, 480, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, 
        SDL_RENDERER_ACCELERATED);
    
    // 读取TMJ文件
    char* jsonText = readFileToString("assets/map.tmj");
    cJSON* root = cJSON_Parse(jsonText);
    
    // 解析所有tileset
    cJSON* tilesetsNode = cJSON_GetObjectItem(root, "tilesets");
    int tilesetCount = cJSON_GetArraySize(tilesetsNode);
    Tileset tilesets[MAX_TILESETS];
    
    printf("解析 %d 个图块集...\n", tilesetCount);
    for (int i = 0; i < tilesetCount; i++) {
        cJSON* tsNode = cJSON_GetArrayItem(tilesetsNode, i);
        parseTileset(tsNode, &tilesets[i], renderer, "assets");
        printTileset(&tilesets[i]);
    }
    
    // 测试：根据gid查找tileset并计算源矩形
    printf("\n=== 测试gid查找 ===\n");
    int testGids[] = {1, 5, 10, 15, 20};
    for (int i = 0; i < 5; i++) {
        int gid = testGids[i];
        Tileset* ts = findTilesetByGid(tilesets, tilesetCount, gid);
        if (ts) {
            SDL_Rect srcRect;
            getTileSourceRect(ts, gid, &srcRect);
            printf("gid=%d → tileset '%s', 源矩形(%d,%d,%d,%d)\n",
                gid, ts->name, srcRect.x, srcRect.y, srcRect.w, srcRect.h);
        } else {
            printf("gid=%d → 未找到tileset\n", gid);
        }
    }
    
    // 清理
    for (int i = 0; i < tilesetCount; i++) {
        freeTileset(&tilesets[i]);
    }
    cJSON_Delete(root);
    free(jsonText);
    
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    IMG_Quit();
    SDL_Quit();
    
    return 0;
}
```

### 总结

- Tileset 定义了图块图片的切割方式，是渲染图块的基础
- `firstgid` 是全局ID起始值，多个 tileset 的 gid 范围连续不重叠
- 本地ID = gid - firstgid，用于计算图块在图片中的位置
- 源矩形计算：`srcX = margin + col * (tileWidth + spacing)`
- `findTilesetByGid` 从后往前查找，找到第一个 firstgid <= gid 的 tileset
- 解析时注意处理可选字段（margin、spacing 默认为 0）
- 如果 columns 或 tileCount 缺失，可根据图片尺寸自动计算
- 图片路径可能是相对路径，需要与 TMJ 文件路径拼接
- 加载纹理后应保存 SDL_Texture 指针，避免重复加载
- 外部 TSX 引用需要额外解析 TSX 文件（XML 格式），本讲暂不涉及

---

## 第9讲：Tile图层解析

### 概念

Tile Layer（图块图层）是 TMJ 中最核心的图层类型，由一个二维数组组成，每个元素是一个全局图块ID（gid）。Tile Layer 描述了地图上每个位置放置哪个图块，是地形、背景等视觉内容的主体。本讲学习如何解析 Tile Layer 的数据，处理 gid 标志位，并将数据存储到内存中供渲染使用。

### 原理

**Tile Layer 的 JSON 结构：**

```json
{
  "type": "tilelayer",
  "name": "Ground",
  "x": 0,
  "y": 0,
  "width": 10,
  "height": 8,
  "visible": true,
  "opacity": 1,
  "locked": false,
  "data": [1, 2, 3, 0, 0, 4, 5, ...]  // 一维数组，行优先
}
```

**data 数组的存储方式：**
- 一维数组，长度 = width × height
- 行优先存储：`data[y * width + x]` 获取 (x, y) 位置的 gid
- gid = 0 表示空白（无图块）
- gid 非 0 时，低 29 位是实际图块ID，高 3 位是标志位

**gid 标志位详解：**

```
位31 (0x80000000): 水平翻转 (Flipped Horizontally)
位30 (0x40000000): 垂直翻转 (Flipped Vertically)
位29 (0x20000000): 对角翻转 (Flipped Anti-diagonally)
位0-28 (0x1FFFFFFF): 实际图块ID
```

对角翻转意味着将图块沿主对角线翻转（相当于旋转90度+水平翻转），用于实现更多方向的旋转。

**data 的编码方式：**
- 普通格式：JSON 数组，直接是 gid 值
- 压缩格式：`"encoding": "base64"` + 可选 `"compression": "zlib/gzip"`
  - 需要先 base64 解码，再 zlib/gzip 解压
  - 解压后是二进制数据，每 4 字节一个 gid（小端序）

**图层属性：**
- `visible`：是否可见（false 时跳过渲染）
- `opacity`：透明度（0.0 ~ 1.0）
- `x`, `y`：图层偏移（像素）
- `locked`：是否锁定（编辑器用，运行时忽略）

### 例子

**定义 Tile Layer 结构体：**

```c
// tile_layer.h
#ifndef TILE_LAYER_H
#define TILE_LAYER_H

#include "cJSON.h"

// gid标志位掩码
#define FLIPPED_HORIZONTALLY_FLAG 0x80000000
#define FLIPPED_VERTICALLY_FLAG   0x40000000
#define FLIPPED_DIAGONALLY_FLAG   0x20000000
#define GID_MASK                  0x1FFFFFFF

typedef struct {
    char name[64];       // 图层名称
    int width;           // 图层宽度（图块数）
    int height;          // 图层高度（图块数）
    int offsetX;         // X偏移（像素）
    int offsetY;         // Y偏移（像素）
    int visible;         // 是否可见
    float opacity;       // 透明度（0.0~1.0）
    int* data;           // 图块数据数组（存储原始gid，含标志位）
} TileLayer;

// 解析tilelayer
int parseTileLayer(cJSON* layerNode, TileLayer* layer);

// 获取指定位置的gid（含标志位）
int getTileGid(const TileLayer* layer, int x, int y);

// 获取指定位置的纯gid（不含标志位）
int getTileId(const TileLayer* layer, int x, int y);

// 获取翻转标志
void getTileFlags(int gid, int* hFlip, int* vFlip, int* dFlip);

// 释放tilelayer资源
void freeTileLayer(TileLayer* layer);

// 打印图层信息（调试用）
void printTileLayer(const TileLayer* layer);

#endif
```

**实现 Tile Layer 解析：**

```c
// tile_layer.c
#include "tile_layer.h"
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// 解析tilelayer
int parseTileLayer(cJSON* layerNode, TileLayer* layer) {
    memset(layer, 0, sizeof(TileLayer));
    layer->data = NULL;
    
    // 解析名称
    cJSON* name = cJSON_GetObjectItem(layerNode, "name");
    if (cJSON_IsString(name)) {
        strncpy(layer->name, name->valuestring, 63);
    }
    
    // 解析尺寸
    cJSON* width = cJSON_GetObjectItem(layerNode, "width");
    cJSON* height = cJSON_GetObjectItem(layerNode, "height");
    if (cJSON_IsNumber(width)) layer->width = width->valueint;
    if (cJSON_IsNumber(height)) layer->height = height->valueint;
    
    // 解析偏移
    cJSON* x = cJSON_GetObjectItem(layerNode, "x");
    cJSON* y = cJSON_GetObjectItem(layerNode, "y");
    if (cJSON_IsNumber(x)) layer->offsetX = x->valueint;
    if (cJSON_IsNumber(y)) layer->offsetY = y->valueint;
    
    // 解析可见性和透明度
    cJSON* visible = cJSON_GetObjectItem(layerNode, "visible");
    layer->visible = cJSON_IsTrue(visible) || visible == NULL;  // 默认可见
    
    cJSON* opacity = cJSON_GetObjectItem(layerNode, "opacity");
    layer->opacity = cJSON_IsNumber(opacity) ? (float)opacity->valuedouble : 1.0f;
    
    // 解析data
    cJSON* data = cJSON_GetObjectItem(layerNode, "data");
    if (!data) {
        printf("错误: 图层 '%s' 没有data字段\n", layer->name);
        return -1;
    }
    
    // 检查编码方式
    cJSON* encoding = cJSON_GetObjectItem(layerNode, "encoding");
    
    if (cJSON_IsArray(data)) {
        // 普通JSON数组格式
        int dataSize = cJSON_GetArraySize(data);
        if (dataSize != layer->width * layer->height) {
            printf("警告: data大小(%d)与图层尺寸(%d)不匹配\n",
                dataSize, layer->width * layer->height);
        }
        
        layer->data = (int*)malloc(dataSize * sizeof(int));
        if (!layer->data) {
            printf("错误: 内存分配失败\n");
            return -1;
        }
        
        for (int i = 0; i < dataSize; i++) {
            cJSON* item = cJSON_GetArrayItem(data, i);
            layer->data[i] = cJSON_IsNumber(item) ? item->valueint : 0;
        }
    } else if (cJSON_IsString(data) && cJSON_IsString(encoding)) {
        // 压缩格式（base64 + zlib/gzip）
        printf("警告: 压缩格式暂不支持，请使用普通JSON数组格式\n");
        // 实际项目中需要实现base64解码和zlib解压
        return -1;
    } else {
        printf("错误: 未知的data格式\n");
        return -1;
    }
    
    return 0;
}

// 获取指定位置的gid（含标志位）
int getTileGid(const TileLayer* layer, int x, int y) {
    if (!layer || !layer->data) return 0;
    if (x < 0 || x >= layer->width || y < 0 || y >= layer->height) {
        return 0;  // 越界返回0（空白）
    }
    return layer->data[y * layer->width + x];
}

// 获取指定位置的纯gid（不含标志位）
int getTileId(const TileLayer* layer, int x, int y) {
    return getTileGid(layer, x, y) & GID_MASK;
}

// 获取翻转标志
void getTileFlags(int gid, int* hFlip, int* vFlip, int* dFlip) {
    *hFlip = (gid & FLIPPED_HORIZONTALLY_FLAG) ? 1 : 0;
    *vFlip = (gid & FLIPPED_VERTICALLY_FLAG) ? 1 : 0;
    *dFlip = (gid & FLIPPED_DIAGONALLY_FLAG) ? 1 : 0;
}

// 释放tilelayer资源
void freeTileLayer(TileLayer* layer) {
    if (layer->data) {
        free(layer->data);
        layer->data = NULL;
    }
}

// 打印图层信息（调试用）
void printTileLayer(const TileLayer* layer) {
    printf("=== TileLayer: %s ===\n", layer->name);
    printf("  尺寸: %d x %d\n", layer->width, layer->height);
    printf("  偏移: (%d, %d)\n", layer->offsetX, layer->offsetY);
    printf("  可见: %s\n", layer->visible ? "是" : "否");
    printf("  透明度: %.2f\n", layer->opacity);
    
    // 打印前几行数据（调试用）
    if (layer->data) {
        printf("  数据预览（前5x5）:\n");
        int previewW = layer->width < 5 ? layer->width : 5;
        int previewH = layer->height < 5 ? layer->height : 5;
        for (int y = 0; y < previewH; y++) {
            printf("    ");
            for (int x = 0; x < previewW; x++) {
                printf("%3d ", getTileId(layer, x, y));
            }
            printf("\n");
        }
    }
}
```

**使用示例：**

```c
// main.c
#include "tile_layer.h"
#include <stdio.h>

int main() {
    char* jsonText = readFileToString("assets/map.tmj");
    cJSON* root = cJSON_Parse(jsonText);
    
    cJSON* layers = cJSON_GetObjectItem(root, "layers");
    cJSON* layerNode;
    cJSON_ArrayForEach(layerNode, layers) {
        const char* type = cJSON_GetObjectItem(layerNode, "type")->valuestring;
        
        if (strcmp(type, "tilelayer") == 0) {
            TileLayer layer;
            if (parseTileLayer(layerNode, &layer) == 0) {
                printTileLayer(&layer);
                
                // 测试：查找特定位置的图块
                printf("\n  查找图块:\n");
                for (int y = 0; y < layer.height; y++) {
                    for (int x = 0; x < layer.width; x++) {
                        int gid = getTileGid(&layer, x, y);
                        if (gid > 0) {
                            int hFlip, vFlip, dFlip;
                            getTileFlags(gid, &hFlip, &vFlip, &dFlip);
                            if (hFlip || vFlip || dFlip) {
                                printf("  (%d,%d) gid=%d [H:%d V:%d D:%d]\n",
                                    x, y, gid & GID_MASK, hFlip, vFlip, dFlip);
                            }
                        }
                    }
                }
                
                freeTileLayer(&layer);
            }
        }
    }
    
    cJSON_Delete(root);
    free(jsonText);
    return 0;
}
```

### 总结

- Tile Layer 的 data 是一维数组，行优先存储，`data[y * width + x]` 访问 (x,y)
- gid = 0 表示空白，非 0 值包含图块ID和翻转标志位
- 标志位：0x80000000（水平翻转）、0x40000000（垂直翻转）、0x20000000（对角翻转）
- 实际图块ID = gid & 0x1FFFFFFF（清除高3位标志）
- 图层属性 visible 控制是否渲染，opacity 控制透明度
- data 可能是 JSON 数组或压缩格式（base64+zlib），本讲只处理数组格式
- 越界访问应返回 0（空白），避免数组越界崩溃
- 解析后 data 数组需要动态分配内存，使用后必须 free
- 翻转标志在渲染时通过 SDL_RenderCopyEx 的 flip 参数实现
- 打印函数帮助调试，建议开发时多用

---

## 第10讲：对象图层解析

### 概念

Object Layer（对象图层）用于存储任意形状的对象，包括矩形、椭圆、多边形、折线和图块对象。与 Tile Layer 不同，对象图层不绘制图块网格，而是存储具有位置、大小、类型和自定义属性的对象。对象图层常用于定义碰撞体、触发器、出生点、NPC 位置等游戏逻辑数据。

### 原理

**Object Layer 的 JSON 结构：**

```json
{
  "type": "objectgroup",
  "name": "Objects",
  "visible": true,
  "opacity": 1,
  "color": "#FF0000",
  "objects": [
    {
      "id": 1,
      "name": "PlayerSpawn",
      "type": "spawn",
      "x": 100, "y": 100,
      "width": 32, "height": 32,
      "rotation": 0,
      "visible": true,
      "gid": 0
    },
    {
      "id": 2,
      "name": "Chest",
      "type": "item",
      "x": 200, "y": 150,
      "width": 32, "height": 32,
      "gid": 5,
      "properties": [
        {"name": "itemId", "type": "int", "value": 1001},
        {"name": "count", "type": "int", "value": 3}
      ]
    }
  ]
}
```

**对象类型判断：**
- 有 `gid` 字段 → 图块对象（用图块图片显示）
- 有 `point` 字段 → 点对象（width=0, height=0）
- 有 `ellipse` 字段 → 椭圆对象
- 有 `polygon` 字段 → 多边形（points 数组）
- 有 `polyline` 字段 → 折线（points 数组）
- 以上都没有 → 矩形对象（默认）

**对象坐标系：**
- 对象的 x, y 是像素坐标（不是图块坐标）
- 对于图块对象，x, y 是左下角（Tiled 编辑器中），渲染时需转换为左上角
- width, height 是对象的像素尺寸

**自定义属性（properties）：**
- 每个属性有 name、type、value
- type 可以是 string、int、float、bool、file、color 等
- 用于存储游戏逻辑数据（如触发器目标、物品ID等）

### 例子

**定义对象结构体：**

```c
// object_layer.h
#ifndef OBJECT_LAYER_H
#define OBJECT_LAYER_H

#include "cJSON.h"

// 对象形状类型
typedef enum {
    OBJ_RECTANGLE,  // 矩形（默认）
    OBJ_ELLIPSE,    // 椭圆
    OBJ_POINT,      // 点
    OBJ_POLYGON,    // 多边形
    OBJ_POLYLINE,   // 折线
    OBJ_TILE        // 图块对象
} ObjectShape;

// 自定义属性
typedef struct {
    char name[64];
    char type[16];      // string/int/float/bool/file/color
    char value[256];    // 统一用字符串存储，使用时转换
} ObjectProperty;

// 地图对象
typedef struct {
    int id;
    char name[64];
    char type[64];      // 对象类型（如"spawn", "collision", "trigger"）
    float x, y;         // 像素坐标
    float width, height;
    float rotation;     // 旋转角度（度）
    int visible;
    int gid;            // 图块ID（如果是图块对象）
    ObjectShape shape;
    
    // 多边形/折线的点
    float* points;      // [x0,y0,x1,y1,...]
    int pointCount;
    
    // 自定义属性
    ObjectProperty* properties;
    int propertyCount;
} MapObject;

// 对象图层
typedef struct {
    char name[64];
    int visible;
    float opacity;
    MapObject* objects;
    int objectCount;
} ObjectLayer;

// 解析对象图层
int parseObjectLayer(cJSON* layerNode, ObjectLayer* layer);

// 根据名称查找对象
MapObject* findObjectByName(ObjectLayer* layer, const char* name);

// 根据类型查找对象（返回第一个匹配的）
MapObject* findObjectByType(ObjectLayer* layer, const char* type);

// 获取对象属性值
const char* getObjectProperty(MapObject* obj, const char* propName);

// 释放对象图层资源
void freeObjectLayer(ObjectLayer* layer);

#endif
```

**实现对象图层解析：**

```c
// object_layer.c
#include "object_layer.h"
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// 解析自定义属性数组
static int parseProperties(cJSON* propsNode, ObjectProperty** props) {
    if (!cJSON_IsArray(propsNode)) {
        *props = NULL;
        return 0;
    }
    
    int count = cJSON_GetArraySize(propsNode);
    *props = (ObjectProperty*)calloc(count, sizeof(ObjectProperty));
    if (!*props) return 0;
    
    for (int i = 0; i < count; i++) {
        cJSON* prop = cJSON_GetArrayItem(propsNode, i);
        cJSON* name = cJSON_GetObjectItem(prop, "name");
        cJSON* type = cJSON_GetObjectItem(prop, "type");
        cJSON* value = cJSON_GetObjectItem(prop, "value");
        
        if (cJSON_IsString(name)) {
            strncpy((*props)[i].name, name->valuestring, 63);
        }
        if (cJSON_IsString(type)) {
            strncpy((*props)[i].type, type->valuestring, 15);
        }
        
        // 根据类型转换值
        if (cJSON_IsString(value)) {
            strncpy((*props)[i].value, value->valuestring, 255);
        } else if (cJSON_IsNumber(value)) {
            snprintf((*props)[i].value, 255, "%g", value->valuedouble);
        } else if (cJSON_IsBool(value)) {
            strcpy((*props)[i].value, cJSON_IsTrue(value) ? "true" : "false");
        }
    }
    
    return count;
}

// 解析多边形/折线的点
static int parsePoints(cJSON* pointsNode, float** points) {
    if (!cJSON_IsArray(pointsNode)) {
        *points = NULL;
        return 0;
    }
    
    int count = cJSON_GetArraySize(pointsNode);
    *points = (float*)malloc(count * 2 * sizeof(float));
    if (!*points) return 0;
    
    for (int i = 0; i < count; i++) {
        cJSON* pt = cJSON_GetArrayItem(pointsNode, i);
        (*points)[i * 2] = (float)cJSON_GetObjectItem(pt, "x")->valuedouble;
        (*points)[i * 2 + 1] = (float)cJSON_GetObjectItem(pt, "y")->valuedouble;
    }
    
    return count;
}

// 解析单个对象
static int parseObject(cJSON* objNode, MapObject* obj) {
    memset(obj, 0, sizeof(MapObject));
    
    cJSON* item;
    
    item = cJSON_GetObjectItem(objNode, "id");
    if (cJSON_IsNumber(item)) obj->id = item->valueint;
    
    item = cJSON_GetObjectItem(objNode, "name");
    if (cJSON_IsString(item)) strncpy(obj->name, item->valuestring, 63);
    
    item = cJSON_GetObjectItem(objNode, "type");
    if (cJSON_IsString(item)) strncpy(obj->type, item->valuestring, 63);
    
    item = cJSON_GetObjectItem(objNode, "x");
    if (cJSON_IsNumber(item)) obj->x = (float)item->valuedouble;
    
    item = cJSON_GetObjectItem(objNode, "y");
    if (cJSON_IsNumber(item)) obj->y = (float)item->valuedouble;
    
    item = cJSON_GetObjectItem(objNode, "width");
    if (cJSON_IsNumber(item)) obj->width = (float)item->valuedouble;
    
    item = cJSON_GetObjectItem(objNode, "height");
    if (cJSON_IsNumber(item)) obj->height = (float)item->valuedouble;
    
    item = cJSON_GetObjectItem(objNode, "rotation");
    if (cJSON_IsNumber(item)) obj->rotation = (float)item->valuedouble;
    
    item = cJSON_GetObjectItem(objNode, "visible");
    obj->visible = cJSON_IsTrue(item) || item == NULL;
    
    item = cJSON_GetObjectItem(objNode, "gid");
    if (cJSON_IsNumber(item)) {
        obj->gid = item->valueint;
        obj->shape = OBJ_TILE;
    }
    
    // 判断形状
    if (cJSON_HasObjectItem(objNode, "point")) {
        obj->shape = OBJ_POINT;
    } else if (cJSON_HasObjectItem(objNode, "ellipse")) {
        obj->shape = OBJ_ELLIPSE;
    } else if (cJSON_HasObjectItem(objNode, "polygon")) {
        obj->shape = OBJ_POLYGON;
        obj->pointCount = parsePoints(
            cJSON_GetObjectItem(objNode, "polygon"), &obj->points);
    } else if (cJSON_HasObjectItem(objNode, "polyline")) {
        obj->shape = OBJ_POLYLINE;
        obj->pointCount = parsePoints(
            cJSON_GetObjectItem(objNode, "polyline"), &obj->points);
    }
    
    // 解析自定义属性
    obj->propertyCount = parseProperties(
        cJSON_GetObjectItem(objNode, "properties"), &obj->properties);
    
    return 0;
}

// 解析对象图层
int parseObjectLayer(cJSON* layerNode, ObjectLayer* layer) {
    memset(layer, 0, sizeof(ObjectLayer));
    
    cJSON* name = cJSON_GetObjectItem(layerNode, "name");
    if (cJSON_IsString(name)) strncpy(layer->name, name->valuestring, 63);
    
    cJSON* visible = cJSON_GetObjectItem(layerNode, "visible");
    layer->visible = cJSON_IsTrue(visible) || visible == NULL;
    
    cJSON* opacity = cJSON_GetObjectItem(layerNode, "opacity");
    layer->opacity = cJSON_IsNumber(opacity) ? (float)opacity->valuedouble : 1.0f;
    
    cJSON* objects = cJSON_GetObjectItem(layerNode, "objects");
    if (!cJSON_IsArray(objects)) {
        layer->objects = NULL;
        layer->objectCount = 0;
        return 0;
    }
    
    layer->objectCount = cJSON_GetArraySize(objects);
    layer->objects = (MapObject*)calloc(layer->objectCount, sizeof(MapObject));
    
    for (int i = 0; i < layer->objectCount; i++) {
        parseObject(cJSON_GetArrayItem(objects, i), &layer->objects[i]);
    }
    
    return 0;
}

// 根据名称查找对象
MapObject* findObjectByName(ObjectLayer* layer, const char* name) {
    for (int i = 0; i < layer->objectCount; i++) {
        if (strcmp(layer->objects[i].name, name) == 0) {
            return &layer->objects[i];
        }
    }
    return NULL;
}

// 根据类型查找对象
MapObject* findObjectByType(ObjectLayer* layer, const char* type) {
    for (int i = 0; i < layer->objectCount; i++) {
        if (strcmp(layer->objects[i].type, type) == 0) {
            return &layer->objects[i];
        }
    }
    return NULL;
}

// 获取对象属性值
const char* getObjectProperty(MapObject* obj, const char* propName) {
    for (int i = 0; i < obj->propertyCount; i++) {
        if (strcmp(obj->properties[i].name, propName) == 0) {
            return obj->properties[i].value;
        }
    }
    return NULL;
}

// 释放对象图层资源
void freeObjectLayer(ObjectLayer* layer) {
    for (int i = 0; i < layer->objectCount; i++) {
        if (layer->objects[i].points) free(layer->objects[i].points);
        if (layer->objects[i].properties) free(layer->objects[i].properties);
    }
    free(layer->objects);
    layer->objects = NULL;
    layer->objectCount = 0;
}
```

**使用示例：**

```c
// 查找玩家出生点
MapObject* spawn = findObjectByType(&objLayer, "spawn");
if (spawn) {
    printf("玩家出生点: (%.0f, %.0f)\n", spawn->x, spawn->y);
    player.x = spawn->x;
    player.y = spawn->y;
}

// 查找宝箱并获取属性
MapObject* chest = findObjectByName(&objLayer, "Chest");
if (chest) {
    const char* itemId = getObjectProperty(chest, "itemId");
    const char* count = getObjectProperty(chest, "count");
    printf("宝箱: 物品ID=%s, 数量=%s\n", itemId, count);
}

// 遍历所有碰撞体
for (int i = 0; i < objLayer.objectCount; i++) {
    MapObject* obj = &objLayer.objects[i];
    if (strcmp(obj->type, "collision") == 0) {
        printf("碰撞体: (%.0f,%.0f,%.0f,%.0f)\n",
            obj->x, obj->y, obj->width, obj->height);
        // 添加到碰撞检测系统
    }
}
```

### 总结

- Object Layer 存储任意形状的对象，用于游戏逻辑（碰撞、触发器、出生点等）
- 对象形状通过字段判断：gid（图块）、point（点）、ellipse（椭圆）、polygon/polyline（多边形/折线）
- 对象坐标是像素坐标，不是图块坐标
- 图块对象的 x,y 是左下角，渲染时 y 需减去 height 转为左上角
- 自定义属性（properties）存储游戏逻辑数据，值统一用字符串存储，使用时按需转换
- `findObjectByName` 和 `findObjectByType` 是常用的查找方法
- 多边形/折线的点存储为 [x0,y0,x1,y1,...] 的一维数组
- 对象图层的对象数量不固定，需动态分配内存
- 释放时要先释放每个对象的 points 和 properties，再释放 objects 数组
- 对象图层通常不直接渲染（除图块对象），而是用于游戏逻辑判断

---

## 第11讲：图像图层解析

### 概念

Image Layer（图像图层）是 TMJ 中最简单的图层类型，它只包含一张完整的图片，按指定位置绘制。与 Tile Layer 不同，Image Layer 不切割图块，而是整图渲染。Image Layer 常用于背景图、前景遮罩、远景视差等场景。

### 原理

**Image Layer 的 JSON 结构：**

```json
{
  "type": "imagelayer",
  "name": "Background",
  "visible": true,
  "opacity": 1,
  "x": 0,
  "y": 0,
  "image": "assets/background.png",
  "imagewidth": 1920,
  "imageheight": 1080
}
```

**Image Layer 的特点：**
- 只有一张图片，不切割
- 有 x, y 偏移（像素坐标）
- 有透明度和可见性控制
- 图片路径通常是相对 TMJ 文件的路径

**与 Tile Layer 的区别：**

| 特性 | Tile Layer | Image Layer |
|------|-----------|-------------|
| 内容 | 图块网格 | 单张图片 |
| 数据 | gid 数组 | 图片路径 |
| 尺寸 | width × height 图块 | imagewidth × imageheight 像素 |
| 用途 | 地形、建筑 | 背景、远景 |
| 渲染 | 逐图块绘制 | 整图绘制 |

**常见用途：**
- 背景图：天空、远山
- 前景遮罩：半透明雾效、光照
- 视差滚动：多层背景以不同速度移动
- 装饰图：大型装饰物

### 例子

**定义 Image Layer 结构体：**

```c
// image_layer.h
#ifndef IMAGE_LAYER_H
#define IMAGE_LAYER_H

#include "cJSON.h"
#include <SDL2/SDL.h>

#define MAX_PATH_LEN 256

typedef struct {
    char name[64];          // 图层名称
    char imagePath[MAX_PATH_LEN]; // 图片路径
    int imageWidth;         // 图片宽度
    int imageHeight;        // 图片高度
    int offsetX;            // X偏移
    int offsetY;            // Y偏移
    int visible;            // 是否可见
    float opacity;          // 透明度
    SDL_Texture* texture;   // SDL纹理
} ImageLayer;

// 解析图像图层
int parseImageLayer(cJSON* layerNode, ImageLayer* layer, 
                    SDL_Renderer* renderer, const char* basePath);

// 渲染图像图层
void renderImageLayer(SDL_Renderer* renderer, const ImageLayer* layer);

// 释放图像图层资源
void freeImageLayer(ImageLayer* layer);

#endif
```

**实现 Image Layer 解析：**

```c
// image_layer.c
#include "image_layer.h"
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// 辅助函数：拼接路径
static void joinPath(char* dest, const char* basePath, const char* relPath) {
    if (relPath[0] == '/' || strstr(relPath, ":")) {
        strncpy(dest, relPath, MAX_PATH_LEN - 1);
    } else {
        snprintf(dest, MAX_PATH_LEN, "%s/%s", basePath, relPath);
    }
}

int parseImageLayer(cJSON* layerNode, ImageLayer* layer, 
                    SDL_Renderer* renderer, const char* basePath) {
    memset(layer, 0, sizeof(ImageLayer));
    layer->texture = NULL;
    
    // 解析名称
    cJSON* name = cJSON_GetObjectItem(layerNode, "name");
    if (cJSON_IsString(name)) {
        strncpy(layer->name, name->valuestring, 63);
    }
    
    // 解析图片路径
    cJSON* image = cJSON_GetObjectItem(layerNode, "image");
    if (cJSON_IsString(image)) {
        joinPath(layer->imagePath, basePath, image->valuestring);
    }
    
    // 解析图片尺寸
    cJSON* imgW = cJSON_GetObjectItem(layerNode, "imagewidth");
    cJSON* imgH = cJSON_GetObjectItem(layerNode, "imageheight");
    if (cJSON_IsNumber(imgW)) layer->imageWidth = imgW->valueint;
    if (cJSON_IsNumber(imgH)) layer->imageHeight = imgH->valueint;
    
    // 解析偏移
    cJSON* x = cJSON_GetObjectItem(layerNode, "x");
    cJSON* y = cJSON_GetObjectItem(layerNode, "y");
    if (cJSON_IsNumber(x)) layer->offsetX = x->valueint;
    if (cJSON_IsNumber(y)) layer->offsetY = y->valueint;
    
    // 解析可见性和透明度
    cJSON* visible = cJSON_GetObjectItem(layerNode, "visible");
    layer->visible = cJSON_IsTrue(visible) || visible == NULL;
    
    cJSON* opacity = cJSON_GetObjectItem(layerNode, "opacity");
    layer->opacity = cJSON_IsNumber(opacity) ? (float)opacity->valuedouble : 1.0f;
    
    // 加载纹理
    if (renderer && layer->imagePath[0]) {
        layer->texture = IMG_LoadTexture(renderer, layer->imagePath);
        if (!layer->texture) {
            printf("警告: 无法加载图片 %s: %s\n", 
                layer->imagePath, IMG_GetError());
        } else {
            // 如果没有指定尺寸，从纹理查询
            if (layer->imageWidth == 0 || layer->imageHeight == 0) {
                SDL_QueryTexture(layer->texture, NULL, NULL,
                    &layer->imageWidth, &layer->imageHeight);
            }
        }
    }
    
    return 0;
}

// 渲染图像图层
void renderImageLayer(SDL_Renderer* renderer, const ImageLayer* layer) {
    if (!layer->visible || !layer->texture) return;
    
    // 设置透明度
    SDL_SetTextureAlphaMod(layer->texture, (Uint8)(layer->opacity * 255));
    
    // 目标矩形（考虑偏移）
    SDL_Rect dstRect = {
        .x = layer->offsetX,
        .y = layer->offsetY,
        .w = layer->imageWidth,
        .h = layer->imageHeight
    };
    
    SDL_RenderCopy(renderer, layer->texture, NULL, &dstRect);
}

// 释放图像图层资源
void freeImageLayer(ImageLayer* layer) {
    if (layer->texture) {
        SDL_DestroyTexture(layer->texture);
        layer->texture = NULL;
    }
}
```

**使用示例：**

```c
// 在主渲染循环中使用
void renderMap(SDL_Renderer* renderer, Map* map) {
    // 1. 先渲染背景图像图层
    for (int i = 0; i < map->layerCount; i++) {
        if (map->layers[i].type == LAYER_IMAGE) {
            renderImageLayer(renderer, &map->layers[i].imageLayer);
        }
    }
    
    // 2. 再渲染Tile图层
    for (int i = 0; i < map->layerCount; i++) {
        if (map->layers[i].type == LAYER_TILE) {
            renderTileLayer(renderer, map, &map->layers[i].tileLayer);
        }
    }
    
    // 3. 最后渲染前景图像图层（如雾效）
    // ...
}
```

### 总结

- Image Layer 是最简单的图层类型，只有一张图片和偏移位置
- 适用于背景、远景、装饰图等不需要切割的场景
- 渲染时整图绘制，使用 `SDL_RenderCopy` 即可
- 透明度通过 `SDL_SetTextureAlphaMod` 设置
- 图片路径需要与 TMJ 文件路径拼接成完整路径
- 加载纹理后应保存尺寸，避免频繁查询
- 渲染顺序：背景 Image Layer → Tile Layer → 前景 Image Layer
- Image Layer 不参与碰撞检测，纯视觉用途
- 多个 Image Layer 可以实现视差滚动效果（不同层不同移动速度）

---

# 第4章：地图渲染核心

## 第12讲：图块切割与纹理管理

### 概念

图块切割是从 tileset 图片中截取单个图块的过程。每个图块在图片中有固定的位置和大小，通过计算可以获取其源矩形（SDL_Rect）。纹理管理则是统一管理所有 tileset 纹理的加载、缓存和释放，避免重复加载和内存泄漏。本讲将图块切割算法和纹理管理整合为一个完整的系统。

### 原理

**图块切割算法：**

给定一个全局图块ID（gid），切割步骤如下：

1. **清除标志位**：`pureGid = gid & 0x1FFFFFFF`
2. **查找 tileset**：找到 `firstgid <= pureGid` 的 tileset
3. **计算本地ID**：`localId = pureGid - tileset.firstgid`
4. **计算行列号**：`col = localId % columns`，`row = localId / columns`
5. **计算像素坐标**：
   - `srcX = margin + col * (tileWidth + spacing)`
   - `srcY = margin + row * (tileHeight + spacing)`

**纹理管理器设计：**

纹理管理器负责：
- 加载纹理并缓存（避免重复加载同一图片）
- 按名称或路径查找纹理
- 统一释放所有纹理

使用哈希表或简单的数组都可以实现，小型项目用数组足够。

**翻转标志处理：**

gid 的高3位是翻转标志，渲染时需要转换为 SDL 的翻转参数：

| 标志组合 | SDL翻转方式 |
|----------|-------------|
| 无标志 | SDL_FLIP_NONE |
| 水平翻转 | SDL_FLIP_HORIZONTAL |
| 垂直翻转 | SDL_FLIP_VERTICAL |
| 水平+垂直 | SDL_FLIP_HORIZONTAL \| SDL_FLIP_VERTICAL |
| 对角翻转 | 需要旋转90度+翻转（SDL2不直接支持，需特殊处理） |

### 例子

**纹理管理器实现：**

```c
// texture_manager.h
#ifndef TEXTURE_MANAGER_H
#define TEXTURE_MANAGER_H

#include <SDL2/SDL.h>

#define MAX_TEXTURES 32
#define MAX_PATH_LEN 256

typedef struct {
    char path[MAX_PATH_LEN];    // 图片路径（作为键）
    SDL_Texture* texture;       // SDL纹理
    int width;                  // 纹理宽度
    int height;                 // 纹理高度
    int refCount;               // 引用计数
} TextureEntry;

typedef struct {
    TextureEntry entries[MAX_TEXTURES];
    int count;
    SDL_Renderer* renderer;
} TextureManager;

// 初始化纹理管理器
void initTextureManager(TextureManager* tm, SDL_Renderer* renderer);

// 加载纹理（如果已缓存则直接返回）
SDL_Texture* loadTexture(TextureManager* tm, const char* path);

// 获取纹理尺寸
void getTextureSize(TextureManager* tm, const char* path, int* w, int* h);

// 释放所有纹理
void freeAllTextures(TextureManager* tm);

#endif
```

```c
// texture_manager.c
#include "texture_manager.h"
#include <SDL2/SDL_image.h>
#include <stdio.h>
#include <string.h>

void initTextureManager(TextureManager* tm, SDL_Renderer* renderer) {
    tm->count = 0;
    tm->renderer = renderer;
    memset(tm->entries, 0, sizeof(tm->entries));
}

SDL_Texture* loadTexture(TextureManager* tm, const char* path) {
    // 查找是否已缓存
    for (int i = 0; i < tm->count; i++) {
        if (strcmp(tm->entries[i].path, path) == 0) {
            tm->entries[i].refCount++;
            return tm->entries[i].texture;
        }
    }
    
    // 未缓存，加载新纹理
    if (tm->count >= MAX_TEXTURES) {
        printf("错误: 纹理缓存已满\n");
        return NULL;
    }
    
    SDL_Texture* texture = IMG_LoadTexture(tm->renderer, path);
    if (!texture) {
        printf("错误: 无法加载纹理 %s: %s\n", path, IMG_GetError());
        return NULL;
    }
    
    // 保存到缓存
    TextureEntry* entry = &tm->entries[tm->count];
    strncpy(entry->path, path, MAX_PATH_LEN - 1);
    entry->texture = texture;
    entry->refCount = 1;
    SDL_QueryTexture(texture, NULL, NULL, &entry->width, &entry->height);
    
    tm->count++;
    return texture;
}

void getTextureSize(TextureManager* tm, const char* path, int* w, int* h) {
    for (int i = 0; i < tm->count; i++) {
        if (strcmp(tm->entries[i].path, path) == 0) {
            if (w) *w = tm->entries[i].width;
            if (h) *h = tm->entries[i].height;
            return;
        }
    }
    if (w) *w = 0;
    if (h) *h = 0;
}

void freeAllTextures(TextureManager* tm) {
    for (int i = 0; i < tm->count; i++) {
        if (tm->entries[i].texture) {
            SDL_DestroyTexture(tm->entries[i].texture);
        }
    }
    tm->count = 0;
}
```

**图块切割与渲染函数：**

```c
// tile_renderer.h
#ifndef TILE_RENDERER_H
#define TILE_RENDERER_H

#include "tileset.h"
#include "texture_manager.h"

// 渲染单个图块
void renderTile(SDL_Renderer* renderer, TextureManager* tm,
                Tileset* tilesets, int tilesetCount,
                int gid, int dstX, int dstY);

// 将gid标志位转换为SDL翻转标志
SDL_RendererFlip getFlipFromGid(int gid);

#endif
```

```c
// tile_renderer.c
#include "tile_renderer.h"
#include <stdio.h>

SDL_RendererFlip getFlipFromGid(int gid) {
    int hFlip = (gid & 0x80000000) ? 1 : 0;
    int vFlip = (gid & 0x40000000) ? 1 : 0;
    int dFlip = (gid & 0x20000000) ? 1 : 0;
    
    SDL_RendererFlip flip = SDL_FLIP_NONE;
    if (hFlip) flip |= SDL_FLIP_HORIZONTAL;
    if (vFlip) flip |= SDL_FLIP_VERTICAL;
    
    // 注意：对角翻转（dFlip）需要旋转90度，SDL2不直接支持
    // 实际项目中需要用SDL_RenderCopyEx的angle参数配合翻转实现
    // 简化处理：忽略dFlip或用旋转近似
    
    return flip;
}

void renderTile(SDL_Renderer* renderer, TextureManager* tm,
                Tileset* tilesets, int tilesetCount,
                int gid, int dstX, int dstY) {
    if (gid == 0) return;  // 空白图块
    
    // 查找所属tileset
    Tileset* ts = findTilesetByGid(tilesets, tilesetCount, gid);
    if (!ts || !ts->texture) {
        printf("警告: 未找到gid=%d的tileset\n", gid);
        return;
    }
    
    // 计算源矩形
    SDL_Rect srcRect;
    getTileSourceRect(ts, gid, &srcRect);
    
    // 目标矩形
    SDL_Rect dstRect = {
        .x = dstX,
        .y = dstY,
        .w = ts->tileWidth,
        .h = ts->tileHeight
    };
    
    // 获取翻转标志
    SDL_RendererFlip flip = getFlipFromGid(gid);
    
    // 渲染
    if (flip == SDL_FLIP_NONE) {
        SDL_RenderCopy(renderer, ts->texture, &srcRect, &dstRect);
    } else {
        SDL_RenderCopyEx(renderer, ts->texture, &srcRect, &dstRect,
                         0, NULL, flip);
    }
}
```

**使用示例：**

```c
// main.c - 测试图块切割与渲染
#include "tile_renderer.h"
#include "texture_manager.h"

int main() {
    SDL_Init(SDL_INIT_VIDEO);
    IMG_Init(IMG_INIT_PNG);
    
    SDL_Window* window = SDL_CreateWindow("Tile Cutting Test",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 640, 480, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1,
        SDL_RENDERER_ACCELERATED);
    
    // 初始化纹理管理器
    TextureManager tm;
    initTextureManager(&tm, renderer);
    
    // 解析tilesets（假设已从TMJ加载）
    Tileset tilesets[2];
    // ... 解析tileset代码 ...
    
    // 渲染单个图块测试
    SDL_SetRenderDrawColor(renderer, 0, 0, 0, 255);
    SDL_RenderClear(renderer);
    
    // 在(100,100)位置渲染gid=1的图块
    renderTile(renderer, &tm, tilesets, 2, 1, 100, 100);
    
    // 在(132,100)位置渲染gid=2的图块（水平翻转）
    renderTile(renderer, &tm, tilesets, 2, 2 | 0x80000000, 132, 100);
    
    SDL_RenderPresent(renderer);
    
    SDL_Delay(3000);
    
    // 清理
    freeAllTextures(&tm);
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    IMG_Quit();
    SDL_Quit();
    
    return 0;
}
```

### 总结

- 图块切割算法：清除标志位 → 查找tileset → 计算本地ID → 计算行列号 → 计算像素坐标
- 源矩形公式：`srcX = margin + col * (tileWidth + spacing)`
- 纹理管理器避免重复加载，通过路径作为键缓存纹理
- 引用计数（refCount）跟踪纹理使用情况，避免提前释放
- gid 标志位转换为 SDL 翻转参数：水平翻转、垂直翻转
- 对角翻转（dFlip）需要旋转90度+翻转，SDL2 需用 `SDL_RenderCopyEx` 的 angle 参数
- `SDL_RenderCopyEx` 支持旋转和翻转，是渲染图块的核心函数
- 纹理管理器应在程序结束时统一释放所有纹理
- 好的纹理管理器设计能显著提升加载效率和减少内存占用

---

## 第13讲：渲染Tile图层

### 概念

渲染 Tile 图层是将 TileLayer 的 data 数组中的 gid 转换为屏幕上的图块图像的过程。本讲实现完整的 Tile 图层渲染函数，包括遍历图块数据、计算屏幕坐标、处理图层偏移和透明度等。这是地图渲染的核心功能。

### 原理

**渲染流程：**

```
遍历图层每个位置 (x, y)
  → 获取 gid = data[y * width + x]
  → 如果 gid == 0，跳过（空白）
  → 查找 tileset
  → 计算源矩形（从图片切割）
  → 计算目标矩形（屏幕位置）
  → SDL_RenderCopy 绘制
```

**坐标计算：**

```
屏幕X = 图层偏移X + x * tileWidth
屏幕Y = 图层偏移Y + y * tileHeight
```

**渲染顺序（renderorder）：**

| 渲染顺序 | 遍历方向 |
|----------|----------|
| right-down（默认） | x: 0→width, y: 0→height |
| right-up | x: 0→width, y: height-1→0 |
| left-down | x: width-1→0, y: 0→height |
| left-up | x: width-1→0, y: height-1→0 |

对于正交地图，渲染顺序对最终视觉效果影响不大（因为图块不重叠），但对等距地图很重要。

**透明度处理：**
- 使用 `SDL_SetTextureAlphaMod` 设置纹理透明度
- 透明度值范围 0-255，对应 opacity 0.0-1.0

**性能考虑：**
- 只渲染可见区域的图块（视口剔除）
- 跳过 gid=0 的空白图块
- 批量渲染同一 tileset 的图块（减少纹理切换）

### 例子

**基础 Tile 图层渲染：**

```c
// tile_layer_renderer.h
#ifndef TILE_LAYER_RENDERER_H
#define TILE_LAYER_RENDERER_H

#include "tile_layer.h"
#include "tileset.h"
#include "map_metadata.h"

// 渲染Tile图层（基础版）
void renderTileLayer(SDL_Renderer* renderer, 
                     const TileLayer* layer,
                     const MapMetadata* mapMeta,
                     Tileset* tilesets, int tilesetCount);

// 渲染Tile图层（带视口剔除）
void renderTileLayerVisible(SDL_Renderer* renderer,
                            const TileLayer* layer,
                            const MapMetadata* mapMeta,
                            Tileset* tilesets, int tilesetCount,
                            int cameraX, int cameraY,
                            int viewportW, int viewportH);

#endif
```

```c
// tile_layer_renderer.c
#include "tile_layer_renderer.h"
#include "tile_renderer.h"
#include <stdio.h>

// 基础渲染（渲染所有图块）
void renderTileLayer(SDL_Renderer* renderer, 
                     const TileLayer* layer,
                     const MapMetadata* mapMeta,
                     Tileset* tilesets, int tilesetCount) {
    // 不可见的图层跳过
    if (!layer->visible) return;
    
    // 设置图层透明度
    if (layer->opacity < 1.0f) {
        // 对每个tileset纹理设置透明度
        for (int i = 0; i < tilesetCount; i++) {
            if (tilesets[i].texture) {
                SDL_SetTextureAlphaMod(tilesets[i].texture, 
                    (Uint8)(layer->opacity * 255));
            }
        }
    }
    
    // 遍历图层每个位置
    for (int y = 0; y < layer->height; y++) {
        for (int x = 0; x < layer->width; x++) {
            int gid = getTileGid(layer, x, y);
            if (gid == 0) continue;  // 跳过空白
            
            // 计算屏幕坐标
            int screenX = layer->offsetX + x * mapMeta->tileWidth;
            int screenY = layer->offsetY + y * mapMeta->tileHeight;
            
            // 渲染图块
            renderTile(renderer, NULL, tilesets, tilesetCount,
                       gid, screenX, screenY);
        }
    }
    
    // 恢复透明度
    if (layer->opacity < 1.0f) {
        for (int i = 0; i < tilesetCount; i++) {
            if (tilesets[i].texture) {
                SDL_SetTextureAlphaMod(tilesets[i].texture, 255);
            }
        }
    }
}

// 带视口剔除的渲染（只渲染可见区域）
void renderTileLayerVisible(SDL_Renderer* renderer,
                            const TileLayer* layer,
                            const MapMetadata* mapMeta,
                            Tileset* tilesets, int tilesetCount,
                            int cameraX, int cameraY,
                            int viewportW, int viewportH) {
    if (!layer->visible) return;
    
    // 设置透明度
    if (layer->opacity < 1.0f) {
        for (int i = 0; i < tilesetCount; i++) {
            if (tilesets[i].texture) {
                SDL_SetTextureAlphaMod(tilesets[i].texture,
                    (Uint8)(layer->opacity * 255));
            }
        }
    }
    
    // 计算可见的图块范围
    int startCol = (cameraX - layer->offsetX) / mapMeta->tileWidth;
    int endCol = (cameraX + viewportW - layer->offsetX) / mapMeta->tileWidth + 1;
    int startRow = (cameraY - layer->offsetY) / mapMeta->tileHeight;
    int endRow = (cameraY + viewportH - layer->offsetY) / mapMeta->tileHeight + 1;
    
    // 裁剪到图层边界
    if (startCol < 0) startCol = 0;
    if (startRow < 0) startRow = 0;
    if (endCol > layer->width) endCol = layer->width;
    if (endRow > layer->height) endRow = layer->height;
    
    // 只渲染可见区域
    for (int y = startRow; y < endRow; y++) {
        for (int x = startCol; x < endCol; x++) {
            int gid = getTileGid(layer, x, y);
            if (gid == 0) continue;
            
            // 计算屏幕坐标（考虑摄像机偏移）
            int screenX = layer->offsetX + x * mapMeta->tileWidth - cameraX;
            int screenY = layer->offsetY + y * mapMeta->tileHeight - cameraY;
            
            renderTile(renderer, NULL, tilesets, tilesetCount,
                       gid, screenX, screenY);
        }
    }
    
    // 恢复透明度
    if (layer->opacity < 1.0f) {
        for (int i = 0; i < tilesetCount; i++) {
            if (tilesets[i].texture) {
                SDL_SetTextureAlphaMod(tilesets[i].texture, 255);
            }
        }
    }
}
```

**支持渲染顺序的版本：**

```c
// 支持不同渲染顺序的渲染函数
void renderTileLayerWithOrder(SDL_Renderer* renderer,
                              const TileLayer* layer,
                              const MapMetadata* mapMeta,
                              Tileset* tilesets, int tilesetCount) {
    if (!layer->visible) return;
    
    // 根据渲染顺序确定遍历方向
    int xStart, xEnd, xStep;
    int yStart, yEnd, yStep;
    
    switch (mapMeta->renderOrder) {
        case RENDER_RIGHT_DOWN:
            xStart = 0; xEnd = layer->width; xStep = 1;
            yStart = 0; yEnd = layer->height; yStep = 1;
            break;
        case RENDER_RIGHT_UP:
            xStart = 0; xEnd = layer->width; xStep = 1;
            yStart = layer->height - 1; yEnd = -1; yStep = -1;
            break;
        case RENDER_LEFT_DOWN:
            xStart = layer->width - 1; xEnd = -1; xStep = -1;
            yStart = 0; yEnd = layer->height; yStep = 1;
            break;
        case RENDER_LEFT_UP:
            xStart = layer->width - 1; xEnd = -1; xStep = -1;
            yStart = layer->height - 1; yEnd = -1; yStep = -1;
            break;
        default:
            xStart = 0; xEnd = layer->width; xStep = 1;
            yStart = 0; yEnd = layer->height; yStep = 1;
    }
    
    for (int y = yStart; y != yEnd; y += yStep) {
        for (int x = xStart; x != xEnd; x += xStep) {
            int gid = getTileGid(layer, x, y);
            if (gid == 0) continue;
            
            int screenX = layer->offsetX + x * mapMeta->tileWidth;
            int screenY = layer->offsetY + y * mapMeta->tileHeight;
            
            renderTile(renderer, NULL, tilesets, tilesetCount,
                       gid, screenX, screenY);
        }
    }
}
```

**完整使用示例：**

```c
// main.c - 完整的地图渲染
int main() {
    SDL_Init(SDL_INIT_VIDEO);
    IMG_Init(IMG_INIT_PNG);
    
    SDL_Window* window = SDL_CreateWindow("Map Render",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 800, 600, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1,
        SDL_RENDERER_ACCELERATED);
    
    // 1. 解析地图元数据
    MapMetadata mapMeta;
    parseMapMetadata("assets/map.tmj", &mapMeta);
    
    // 2. 解析tilesets
    char* jsonText = readFileToString("assets/map.tmj");
    cJSON* root = cJSON_Parse(jsonText);
    cJSON* tilesetsNode = cJSON_GetObjectItem(root, "tilesets");
    int tilesetCount = cJSON_GetArraySize(tilesetsNode);
    Tileset tilesets[MAX_TILESETS];
    for (int i = 0; i < tilesetCount; i++) {
        parseTileset(cJSON_GetArrayItem(tilesetsNode, i), 
                    &tilesets[i], renderer, "assets");
    }
    
    // 3. 解析图层
    cJSON* layersNode = cJSON_GetObjectItem(root, "layers");
    int layerCount = cJSON_GetArraySize(layersNode);
    TileLayer* tileLayers = malloc(layerCount * sizeof(TileLayer));
    int tileLayerCount = 0;
    
    for (int i = 0; i < layerCount; i++) {
        cJSON* layerNode = cJSON_GetArrayItem(layersNode, i);
        const char* type = cJSON_GetObjectItem(layerNode, "type")->valuestring;
        if (strcmp(type, "tilelayer") == 0) {
            parseTileLayer(layerNode, &tileLayers[tileLayerCount++]);
        }
    }
    
    // 4. 渲染循环
    SDL_Event e;
    int quit = 0;
    while (!quit) {
        while (SDL_PollEvent(&e)) {
            if (e.type == SDL_QUIT) quit = 1;
        }
        
        SDL_SetRenderDrawColor(renderer, 0, 0, 0, 255);
        SDL_RenderClear(renderer);
        
        // 渲染所有Tile图层
        for (int i = 0; i < tileLayerCount; i++) {
            renderTileLayer(renderer, &tileLayers[i], &mapMeta,
                           tilesets, tilesetCount);
        }
        
        SDL_RenderPresent(renderer);
        SDL_Delay(16);  // ~60 FPS
    }
    
    // 5. 清理
    for (int i = 0; i < tileLayerCount; i++) {
        freeTileLayer(&tileLayers[i]);
    }
    free(tileLayers);
    for (int i = 0; i < tilesetCount; i++) {
        freeTileset(&tilesets[i]);
    }
    cJSON_Delete(root);
    free(jsonText);
    
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    IMG_Quit();
    SDL_Quit();
    
    return 0;
}
```

### 总结

- Tile 图层渲染核心：遍历 data 数组，将 gid 转换为屏幕图块
- 屏幕坐标 = 图层偏移 + 图块坐标 × 图块尺寸
- gid=0 的空白图块应跳过，避免不必要的渲染调用
- 视口剔除只渲染屏幕可见区域，大幅提升大地图性能
- 透明度通过 `SDL_SetTextureAlphaMod` 设置，渲染后需恢复
- 渲染顺序（renderorder）影响遍历方向，对等距地图很重要
- 性能优化：减少纹理切换、跳过空白、视口剔除
- `SDL_RenderCopyEx` 支持翻转，用于处理 gid 标志位
- 渲染循环应控制帧率（约60FPS），避免CPU占用过高
- 所有资源（纹理、data数组、cJSON）都要在程序结束时释放

---

## 第14讲：坐标系统与摄像机

### 概念

坐标系统是游戏开发的基础。在 TMJ 地图渲染中，涉及多种坐标系：图块坐标（tile coordinate）、世界坐标（world coordinate）、屏幕坐标（screen coordinate）。摄像机（Camera）是控制玩家视野的机制，决定了地图的哪一部分显示在屏幕上。本讲学习坐标转换和摄像机系统的实现。

### 原理

**三种坐标系：**

```
图块坐标: (tileX, tileY)      → 网格位置，如(5, 3)
世界坐标: (worldX, worldY)    → 像素位置，如(160, 96)
屏幕坐标: (screenX, screenY)  → 屏幕上的位置，如(100, 50)
```

**坐标转换公式：**

```
世界坐标 = 图块坐标 × 图块尺寸
worldX = tileX * tileWidth
worldY = tileY * tileHeight

图块坐标 = 世界坐标 / 图块尺寸
tileX = worldX / tileWidth
tileY = worldY / tileHeight

屏幕坐标 = 世界坐标 - 摄像机位置
screenX = worldX - cameraX
screenY = worldY - cameraY

世界坐标 = 屏幕坐标 + 摄像机位置
worldX = screenX + cameraX
worldY = screenY + cameraY
```

**摄像机系统：**

摄像机本质上是一个矩形，定义了可见区域：
- `camera.x, camera.y`：摄像机左上角的世界坐标
- `camera.w, camera.h`：可见区域大小（通常等于窗口大小）

**摄像机跟随逻辑：**
1. 计算目标位置（如玩家位置）
2. 将摄像机中心对准目标
3. 限制摄像机不超出地图边界

```
cameraX = playerX - viewportW / 2
cameraY = playerY - viewportH / 2

// 限制边界
if (cameraX < 0) cameraX = 0
if (cameraY < 0) cameraY = 0
if (cameraX > mapPixelWidth - viewportW) 
    cameraX = mapPixelWidth - viewportW
if (cameraY > mapPixelHeight - viewportH) 
    cameraY = mapPixelHeight - viewportH
```

### 例子

**摄像机结构体与操作：**

```c
// camera.h
#ifndef CAMERA_H
#define CAMERA_H

#include "map_metadata.h"

typedef struct {
    int x, y;       // 摄像机左上角的世界坐标
    int width;      // 视口宽度
    int height;     // 视口高度
} Camera;

// 初始化摄像机
void initCamera(Camera* cam, int viewportW, int viewportH);

// 摄像机跟随目标
void cameraFollow(Camera* cam, int targetX, int targetY,
                  const MapMetadata* mapMeta);

// 限制摄像机在地图边界内
void clampCamera(Camera* cam, const MapMetadata* mapMeta);

// 世界坐标转屏幕坐标
void worldToScreen(const Camera* cam, int worldX, int worldY,
                   int* screenX, int* screenY);

// 屏幕坐标转世界坐标
void screenToWorld(const Camera* cam, int screenX, int screenY,
                   int* worldX, int* worldY);

// 图块坐标转世界坐标
void tileToWorld(int tileX, int tileY, const MapMetadata* mapMeta,
                 int* worldX, int* worldY);

// 世界坐标转图块坐标
void worldToTile(int worldX, int worldY, const MapMetadata* mapMeta,
                 int* tileX, int* tileY);

#endif
```

```c
// camera.c
#include "camera.h"

void initCamera(Camera* cam, int viewportW, int viewportH) {
    cam->x = 0;
    cam->y = 0;
    cam->width = viewportW;
    cam->height = viewportH;
}

void cameraFollow(Camera* cam, int targetX, int targetY,
                  const MapMetadata* mapMeta) {
    // 将摄像机中心对准目标
    cam->x = targetX - cam->width / 2;
    cam->y = targetY - cam->height / 2;
    
    // 限制边界
    clampCamera(cam, mapMeta);
}

void clampCamera(Camera* cam, const MapMetadata* mapMeta) {
    int mapPixelW = mapMeta->width * mapMeta->tileWidth;
    int mapPixelH = mapMeta->height * mapMeta->tileHeight;
    
    // 如果地图比视口小，居中显示
    if (mapPixelW <= cam->width) {
        cam->x = (mapPixelW - cam->width) / 2;
    } else {
        if (cam->x < 0) cam->x = 0;
        if (cam->x > mapPixelW - cam->width) 
            cam->x = mapPixelW - cam->width;
    }
    
    if (mapPixelH <= cam->height) {
        cam->y = (mapPixelH - cam->height) / 2;
    } else {
        if (cam->y < 0) cam->y = 0;
        if (cam->y > mapPixelH - cam->height) 
            cam->y = mapPixelH - cam->height;
    }
}

void worldToScreen(const Camera* cam, int worldX, int worldY,
                   int* screenX, int* screenY) {
    *screenX = worldX - cam->x;
    *screenY = worldY - cam->y;
}

void screenToWorld(const Camera* cam, int screenX, int screenY,
                   int* worldX, int* worldY) {
    *worldX = screenX + cam->x;
    *worldY = screenY + cam->y;
}

void tileToWorld(int tileX, int tileY, const MapMetadata* mapMeta,
                 int* worldX, int* worldY) {
    *worldX = tileX * mapMeta->tileWidth;
    *worldY = tileY * mapMeta->tileHeight;
}

void worldToTile(int worldX, int worldY, const MapMetadata* mapMeta,
                 int* tileX, int* tileY) {
    *tileX = worldX / mapMeta->tileWidth;
    *tileY = worldY / mapMeta->tileHeight;
}
```

**使用示例 - 摄像机跟随鼠标：**

```c
// main.c - 摄像机测试
#include "camera.h"

int main() {
    SDL_Init(SDL_INIT_VIDEO);
    IMG_Init(IMG_INIT_PNG);
    
    SDL_Window* window = SDL_CreateWindow("Camera Test",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 800, 600, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1,
        SDL_RENDERER_ACCELERATED);
    
    // 加载地图
    MapMetadata mapMeta;
    parseMapMetadata("assets/map.tmj", &mapMeta);
    // ... 加载tilesets和layers ...
    
    // 初始化摄像机
    Camera camera;
    initCamera(&camera, 800, 600);
    
    SDL_Event e;
    int quit = 0;
    int mouseX = 0, mouseY = 0;
    
    while (!quit) {
        while (SDL_PollEvent(&e)) {
            if (e.type == SDL_QUIT) quit = 1;
            if (e.type == SDL_MOUSEMOTION) {
                mouseX = e.motion.x;
                mouseY = e.motion.y;
            }
        }
        
        // 鼠标位置转世界坐标
        int worldX, worldY;
        screenToWorld(&camera, mouseX, mouseY, &worldX, &worldY);
        
        // 摄像机跟随鼠标的世界坐标
        cameraFollow(&camera, worldX, worldY, &mapMeta);
        
        // 渲染
        SDL_SetRenderDrawColor(renderer, 0, 0, 0, 255);
        SDL_RenderClear(renderer);
        
        // 使用带视口剔除的渲染
        for (int i = 0; i < tileLayerCount; i++) {
            renderTileLayerVisible(renderer, &tileLayers[i], &mapMeta,
                                  tilesets, tilesetCount,
                                  camera.x, camera.y,
                                  camera.width, camera.height);
        }
        
        SDL_RenderPresent(renderer);
        SDL_Delay(16);
    }
    
    // 清理...
    return 0;
}
```

**键盘控制摄像机移动：**

```c
// 在事件循环中处理键盘输入
const Uint8* keystates = SDL_GetKeyboardState(NULL);
int camSpeed = 5;

if (keystates[SDL_SCANCODE_LEFT] || keystates[SDL_SCANCODE_A]) {
    camera.x -= camSpeed;
}
if (keystates[SDL_SCANCODE_RIGHT] || keystates[SDL_SCANCODE_D]) {
    camera.x += camSpeed;
}
if (keystates[SDL_SCANCODE_UP] || keystates[SDL_SCANCODE_W]) {
    camera.y -= camSpeed;
}
if (keystates[SDL_SCANCODE_DOWN] || keystates[SDL_SCANCODE_S]) {
    camera.y += camSpeed;
}

clampCamera(&camera, &mapMeta);
```

### 总结

- 三种坐标系：图块坐标（网格）、世界坐标（像素）、屏幕坐标（显示）
- 转换公式：世界 = 图块 × 尺寸，屏幕 = 世界 - 摄像机
- 摄像机是一个矩形，定义可见区域，位置即左上角世界坐标
- 摄像机跟随：中心对准目标，然后限制在地图边界内
- `clampCamera` 确保摄像机不超出地图，小地图时居中显示
- 视口剔除渲染只绘制摄像机可见区域，大幅提升性能
- 鼠标点击转世界坐标：`screenToWorld`，用于交互
- 键盘控制摄像机：`SDL_GetKeyboardState` 获取按键状态
- 摄像机移动速度应与帧率无关（考虑使用 delta time）
- 摄像机系统是 2D 游戏的基础，后续角色移动会依赖它

---

## 第15讲：地图渲染优化

### 概念

随着地图变大、图层增多，渲染性能可能下降。本讲介绍多种地图渲染优化技术，包括视口剔除、纹理图集、批量渲染、脏矩形更新等。合理的优化可以让大地图（如 1000×1000 图块）保持流畅的 60 FPS。

### 原理

**性能瓶颈分析：**

1. **图块数量过多**：大地图有数百万图块，逐个渲染慢
2. **纹理切换频繁**：不同 tileset 的图块交替渲染，导致 GPU 缓存失效
3. **重复计算**：每帧重新计算源矩形、坐标转换
4. **全量渲染**：渲染不可见图块浪费资源

**优化策略：**

| 优化技术 | 原理 | 效果 |
|----------|------|------|
| 视口剔除 | 只渲染可见区域 | 大地图提升10-100倍 |
| 纹理图集 | 合并小图为大图 | 减少纹理切换 |
| 批量渲染 | 按tileset分组渲染 | 减少状态切换 |
| 缓存计算 | 预计算源矩形 | 减少CPU计算 |
| 离屏渲染 | 渲染到纹理再显示 | 适合静态地图 |
| 脏矩形 | 只更新变化区域 | 适合静态场景 |

**视口剔除原理：**

```
地图: 1000 x 1000 图块 (32000 x 32000 像素)
屏幕: 800 x 600 像素
可见图块: (800/32) x (600/32) = 25 x 19 = 475 个
总图块: 1000000 个
优化比: 1000000 / 475 ≈ 2100倍
```

**批量渲染原理：**

按 tileset 分组，先渲染所有 tileset A 的图块，再渲染 tileset B 的，减少 GPU 纹理绑定切换。

### 例子

**优化版渲染 - 按tileset分组：**

```c
// optimized_renderer.h
#ifndef OPTIMIZED_RENDERER_H
#define OPTIMIZED_RENDERER_H

#include "tile_layer.h"
#include "tileset.h"
#include "map_metadata.h"
#include "camera.h"

// 优化版Tile图层渲染（视口剔除+按tileset分组）
void renderTileLayerOptimized(SDL_Renderer* renderer,
                              const TileLayer* layer,
                              const MapMetadata* mapMeta,
                              Tileset* tilesets, int tilesetCount,
                              const Camera* camera);

#endif
```

```c
// optimized_renderer.c
#include "optimized_renderer.h"
#include "tile_renderer.h"
#include <stdlib.h>
#include <string.h>

// 临时存储待渲染的图块
typedef struct {
    int gid;
    int screenX;
    int screenY;
} RenderItem;

void renderTileLayerOptimized(SDL_Renderer* renderer,
                              const TileLayer* layer,
                              const MapMetadata* mapMeta,
                              Tileset* tilesets, int tilesetCount,
                              const Camera* camera) {
    if (!layer->visible) return;
    
    // 计算可见图块范围
    int startCol = camera->x / mapMeta->tileWidth;
    int endCol = (camera->x + camera->width) / mapMeta->tileWidth + 1;
    int startRow = camera->y / mapMeta->tileHeight;
    int endRow = (camera->y + camera->height) / mapMeta->tileHeight + 1;
    
    // 裁剪到图层边界
    if (startCol < 0) startCol = 0;
    if (startRow < 0) startRow = 0;
    if (endCol > layer->width) endCol = layer->width;
    if (endRow > layer->height) endRow = layer->height;
    
    // 收集所有待渲染的图块
    int maxItems = (endCol - startCol) * (endRow - startRow);
    RenderItem* items = malloc(maxItems * sizeof(RenderItem));
    int itemCount = 0;
    
    for (int y = startRow; y < endRow; y++) {
        for (int x = startCol; x < endCol; x++) {
            int gid = getTileGid(layer, x, y);
            if (gid == 0) continue;
            
            items[itemCount].gid = gid;
            items[itemCount].screenX = x * mapMeta->tileWidth - camera->x;
            items[itemCount].screenY = y * mapMeta->tileHeight - camera->y;
            itemCount++;
        }
    }
    
    // 按tileset分组渲染（减少纹理切换）
    for (int ts = 0; ts < tilesetCount; ts++) {
        if (!tilesets[ts].texture) continue;
        
        // 设置透明度
        SDL_SetTextureAlphaMod(tilesets[ts].texture,
            (Uint8)(layer->opacity * 255));
        
        // 渲染属于当前tileset的所有图块
        for (int i = 0; i < itemCount; i++) {
            int pureGid = items[i].gid & 0x1FFFFFFF;
            if (pureGid >= tilesets[ts].firstgid &&
                pureGid < tilesets[ts].firstgid + tilesets[ts].tileCount) {
                
                SDL_Rect srcRect;
                getTileSourceRect(&tilesets[ts], items[i].gid, &srcRect);
                
                SDL_Rect dstRect = {
                    .x = items[i].screenX,
                    .y = items[i].screenY,
                    .w = tilesets[ts].tileWidth,
                    .h = tilesets[ts].tileHeight
                };
                
                SDL_RendererFlip flip = getFlipFromGid(items[i].gid);
                if (flip == SDL_FLIP_NONE) {
                    SDL_RenderCopy(renderer, tilesets[ts].texture,
                                  &srcRect, &dstRect);
                } else {
                    SDL_RenderCopyEx(renderer, tilesets[ts].texture,
                                    &srcRect, &dstRect, 0, NULL, flip);
                }
            }
        }
    }
    
    free(items);
}
```

**离屏渲染（渲染到纹理）：**

```c
// offscreen_render.c
// 将静态图层渲染到一个大纹理，每帧只复制该纹理

typedef struct {
    SDL_Texture* cachedTexture;
    int isDirty;  // 是否需要重新渲染
} LayerCache;

// 预渲染图层到纹理
void prerenderLayer(SDL_Renderer* renderer, LayerCache* cache,
                    const TileLayer* layer, const MapMetadata* mapMeta,
                    Tileset* tilesets, int tilesetCount) {
    int mapPixelW = mapMeta->width * mapMeta->tileWidth;
    int mapPixelH = mapMeta->height * mapMeta->tileHeight;
    
    // 创建目标纹理
    cache->cachedTexture = SDL_CreateTexture(renderer,
        SDL_PIXELFORMAT_RGBA8888, SDL_TEXTUREACCESS_TARGET,
        mapPixelW, mapPixelH);
    
    // 设置渲染目标为纹理
    SDL_SetRenderTarget(renderer, cache->cachedTexture);
    SDL_SetRenderDrawColor(renderer, 0, 0, 0, 0);
    SDL.RenderClear(renderer);
    
    // 渲染图层到纹理
    renderTileLayer(renderer, layer, mapMeta, tilesets, tilesetCount);
    
    // 恢复默认渲染目标
    SDL_SetRenderTarget(renderer, NULL);
    cache->isDirty = 0;
}

// 从缓存纹理渲染到屏幕
void renderCachedLayer(SDL_Renderer* renderer, LayerCache* cache,
                       const Camera* camera) {
    if (!cache->cachedTexture) return;
    
    // 源矩形：摄像机在地图中的位置
    SDL_Rect srcRect = {
        .x = camera->x,
        .y = camera->y,
        .w = camera->width,
        .h = camera->height
    };
    
    // 目标矩形：整个屏幕
    SDL_Rect dstRect = {0, 0, camera->width, camera->height};
    
    SDL_RenderCopy(renderer, cache->cachedTexture, &srcRect, &dstRect);
}
```

**性能对比示例：**

```c
// 性能测试
#include <time.h>

void benchmarkRender(SDL_Renderer* renderer, /* ... */) {
    clock_t start, end;
    
    // 测试基础渲染
    start = clock();
    for (int i = 0; i < 100; i++) {
        renderTileLayer(renderer, &layer, &mapMeta, tilesets, tilesetCount);
    }
    end = clock();
    printf("基础渲染: %.2f ms/帧\n", (double)(end - start) / CLOCKS_PER_SEC * 10);
    
    // 测试优化渲染
    start = clock();
    for (int i = 0; i < 100; i++) {
        renderTileLayerOptimized(renderer, &layer, &mapMeta,
                                tilesets, tilesetCount, &camera);
    }
    end = clock();
    printf("优化渲染: %.2f ms/帧\n", (double)(end - start) / CLOCKS_PER_SEC * 10);
}
```

### 总结

- 视口剔除是最有效的优化，只渲染屏幕可见的图块
- 按 tileset 分组渲染减少 GPU 纹理切换，提升批量渲染效率
- 离屏渲染将静态图层预渲染到纹理，每帧只复制纹理，适合不变化的背景
- 脏矩形更新只重绘变化区域，适合静态场景
- 缓存计算结果（如源矩形）减少每帧 CPU 计算
- 大地图必须使用视口剔除，否则帧率会极低
- 性能优化应先测量（benchmark）再优化，避免盲目优化
- SDL_RenderCopy 的调用次数是主要瓶颈，应尽量减少
- 纹理图集（将多个小图合并为一张大图）能进一步减少纹理切换
- 优化要在保证视觉效果的前提下进行，不要过度优化

---

# 第5章：进阶功能

## 第16讲：动画图块处理

### 概念

动画图块（Animated Tile）是 Tiled 中可以自动播放动画的图块。例如流水、火焰、闪烁的灯等。TMJ 文件中，动画图块在 tileset 的 tiles 数组中定义动画帧序列。本讲学习如何解析动画图块数据，并在渲染时根据时间播放正确的帧。

### 原理

**动画图块的 JSON 结构：**

```json
{
  "tilesets": [{
    "firstgid": 1,
    "tiles": [
      {
        "id": 10,
        "animation": [
          {"tileid": 10, "duration": 200},
          {"tileid": 11, "duration": 200},
          {"tileid": 12, "duration": 200},
          {"tileid": 11, "duration": 200}
        ]
      }
    ]
  }]
}
```

- `id`：tileset 内的本地图块ID
- `animation`：动画帧数组
  - `tileid`：该帧显示的图块本地ID
  - `duration`：该帧持续时间（毫秒）

**动画播放原理：**

```
总周期 = 所有帧 duration 之和
当前时间取模 = elapsed % 总周期
遍历帧，累加 duration 直到超过当前时间取模值
当前帧 = 累加超过的那一帧
```

**渲染流程：**
1. 检查 gid 对应的图块是否有动画
2. 如果有动画，根据当前时间计算应该显示的帧
3. 用帧的 tileid 替换原始 gid 进行渲染

### 例子

**定义动画图块结构体：**

```c
// animation.h
#ifndef ANIMATION_H
#define ANIMATION_H

#include "tileset.h"

// 动画帧
typedef struct {
    int tileId;     // 该帧的本地图块ID
    int duration;   // 持续时间（毫秒）
} AnimationFrame;

// 动画图块
typedef struct {
    int localTileId;            // 本地图块ID（动画所属图块）
    AnimationFrame* frames;     // 动画帧数组
    int frameCount;             // 帧数
    int totalDuration;          // 总周期（毫秒）
} TileAnimation;

// 从tileset的tiles数组解析动画
int parseTileAnimations(cJSON* tilesNode, TileAnimation** animations, int* count);

// 根据gid和时间获取当前帧的gid
int getAnimatedTileGid(TileAnimation* animations, int count,
                       Tileset* tileset, int gid, Uint32 elapsed);

// 释放动画数据
void freeAnimations(TileAnimation* animations, int count);

#endif
```

```c
// animation.c
#include "animation.h"
#include <stdlib.h>
#include <string.h>

int parseTileAnimations(cJSON* tilesNode, TileAnimation** animations, int* count) {
    *animations = NULL;
    *count = 0;
    
    if (!cJSON_IsArray(tilesNode)) return 0;
    
    int tileCount = cJSON_GetArraySize(tilesNode);
    *animations = (TileAnimation*)calloc(tileCount, sizeof(TileAnimation));
    if (!*animations) return -1;
    
    int animCount = 0;
    for (int i = 0; i < tileCount; i++) {
        cJSON* tileNode = cJSON_GetArrayItem(tilesNode, i);
        cJSON* animNode = cJSON_GetObjectItem(tileNode, "animation");
        
        if (!cJSON_IsArray(animNode)) continue;
        
        TileAnimation* anim = &(*animations)[animCount];
        anim->frameCount = cJSON_GetArraySize(animNode);
        anim->frames = (AnimationFrame*)malloc(anim->frameCount * sizeof(AnimationFrame));
        anim->totalDuration = 0;
        
        cJSON* idNode = cJSON_GetObjectItem(tileNode, "id");
        anim->localTileId = cJSON_IsNumber(idNode) ? idNode->valueint : 0;
        
        for (int f = 0; f < anim->frameCount; f++) {
            cJSON* frameNode = cJSON_GetArrayItem(animNode, f);
            anim->frames[f].tileId = cJSON_GetObjectItem(frameNode, "tileid")->valueint;
            anim->frames[f].duration = cJSON_GetObjectItem(frameNode, "duration")->valueint;
            anim->totalDuration += anim->frames[f].duration;
        }
        
        animCount++;
    }
    
    *count = animCount;
    return 0;
}

int getAnimatedTileGid(TileAnimation* animations, int count,
                       Tileset* tileset, int gid, Uint32 elapsed) {
    int pureGid = gid & 0x1FFFFFFF;
    int localId = pureGid - tileset->firstgid;
    
    // 查找该图块的动画
    for (int i = 0; i < count; i++) {
        if (animations[i].localTileId == localId) {
            // 计算当前帧
            int currentTime = elapsed % animations[i].totalDuration;
            int accumTime = 0;
            
            for (int f = 0; f < animations[i].frameCount; f++) {
                accumTime += animations[i].frames[f].duration;
                if (currentTime < accumTime) {
                    // 返回当前帧的gid（保留标志位）
                    return (gid & 0xE0000000) | (tileset->firstgid + animations[i].frames[f].tileId);
                }
            }
            // 默认返回第一帧
            return (gid & 0xE0000000) | (tileset->firstgid + animations[i].frames[0].tileId);
        }
    }
    
    // 没有动画，返回原始gid
    return gid;
}

void freeAnimations(TileAnimation* animations, int count) {
    for (int i = 0; i < count; i++) {
        if (animations[i].frames) free(animations[i].frames);
    }
    free(animations);
}
```

**在渲染中使用动画：**

```c
// 修改渲染函数，支持动画
void renderTileLayerWithAnimation(SDL_Renderer* renderer,
                                  const TileLayer* layer,
                                  const MapMetadata* mapMeta,
                                  Tileset* tilesets, int tilesetCount,
                                  TileAnimation* animations, int animCount,
                                  Uint32 elapsedTime) {
    if (!layer->visible) return;
    
    for (int y = 0; y < layer->height; y++) {
        for (int x = 0; x < layer->width; x++) {
            int gid = getTileGid(layer, x, y);
            if (gid == 0) continue;
            
            // 查找tileset
            Tileset* ts = findTilesetByGid(tilesets, tilesetCount, gid);
            if (!ts) continue;
            
            // 检查是否有动画，获取当前帧的gid
            int animatedGid = getAnimatedTileGid(animations, animCount, ts, gid, elapsedTime);
            
            int screenX = x * mapMeta->tileWidth;
            int screenY = y * mapMeta->tileHeight;
            
            renderTile(renderer, NULL, tilesets, tilesetCount, animatedGid, screenX, screenY);
        }
    }
}
```

### 总结

- 动画图块在 tileset 的 tiles 数组中定义，包含 animation 帧序列
- 每帧有 tileid（显示的图块）和 duration（持续时间毫秒）
- 动画播放：`当前帧 = (elapsed % 总周期)` 对应的帧
- 渲染时用当前帧的 gid 替换原始 gid，保留翻转标志位
- 动画数据应在 tileset 解析时一并提取并缓存
- 使用 `SDL_GetTicks()` 获取程序运行时间作为动画时间
- 动画图块不影响碰撞检测，只影响视觉显示
- 多个图块可以共享同一动画定义（通过 localTileId 匹配）

---

## 第17讲：对象交互与碰撞检测

### 概念

对象图层中的对象常用于碰撞检测和触发器。碰撞检测判断角色是否与障碍物重叠，触发器判断角色是否进入特定区域。本讲学习如何利用 Object Layer 的数据实现碰撞检测和区域触发，让角色能在地图上正确移动而不穿墙。

### 原理

**碰撞检测类型：**

1. **AABB（Axis-Aligned Bounding Box）**：轴对齐矩形碰撞，最简单高效
2. **圆形碰撞**：用圆形近似物体，适合圆形物体
3. **像素级碰撞**：精确但昂贵，很少用于图块地图

**AABB 碰撞检测原理：**

两个矩形 A 和 B 不碰撞的条件（任一满足即不碰撞）：
- A 的右边 < B 的左边
- A 的左边 > B 的右边
- A 的下边 < B 的上边
- A 的上边 > B 的下边

碰撞条件（所有都满足）：
- A 的右边 >= B 的左边
- A 的左边 <= B 的右边
- A 的下边 >= B 的上边
- A 的上边 <= B 的下边

```c
int checkAABB(int ax, int ay, int aw, int ah,
              int bx, int by, int bw, int bh) {
    return (ax < bx + bw && ax + aw > bx &&
            ay < by + bh && ay + ah > by);
}
```

**碰撞检测策略：**
1. 从 Object Layer 提取所有碰撞体（type="collision"）
2. 角色移动前，预测新位置
3. 检查新位置是否与任何碰撞体重叠
4. 如果碰撞，阻止移动或调整位置

**触发器检测：**
- 触发器是特殊对象（type="trigger"）
- 角色进入触发器区域时执行动作
- 每帧检查角色是否在触发器范围内

### 例子

**碰撞系统实现：**

```c
// collision.h
#ifndef COLLISION_H
#define COLLISION_H

#include "object_layer.h"

// AABB碰撞检测
int checkAABB(float ax, float ay, float aw, float ah,
              float bx, float by, float bw, float bh);

// 从对象图层提取碰撞体
int extractColliders(ObjectLayer* layer, SDL_Rect** colliders);

// 检查矩形是否与任何碰撞体碰撞
int checkCollisionWithMap(SDL_Rect rect, SDL_Rect* colliders, int count);

// 检查并阻止移动（返回调整后的位置）
void resolveCollision(float* x, float* y, float w, float h,
                      float dx, float dy,
                      SDL_Rect* colliders, int count);

#endif
```

```c
// collision.c
#include "collision.h"
#include <stdlib.h>
#include <string.h>

int checkAABB(float ax, float ay, float aw, float ah,
              float bx, float by, float bw, float bh) {
    return (ax < bx + bw && ax + aw > bx &&
            ay < by + bh && ay + ah > by);
}

int extractColliders(ObjectLayer* layer, SDL_Rect** colliders) {
    // 先计算碰撞体数量
    int count = 0;
    for (int i = 0; i < layer->objectCount; i++) {
        if (strcmp(layer->objects[i].type, "collision") == 0) {
            count++;
        }
    }
    
    if (count == 0) {
        *colliders = NULL;
        return 0;
    }
    
    *colliders = (SDL_Rect*)malloc(count * sizeof(SDL_Rect));
    int idx = 0;
    for (int i = 0; i < layer->objectCount; i++) {
        MapObject* obj = &layer->objects[i];
        if (strcmp(obj->type, "collision") == 0) {
            (*colliders)[idx].x = (int)obj->x;
            (*colliders)[idx].y = (int)obj->y;
            (*colliders)[idx].w = (int)obj->width;
            (*colliders)[idx].h = (int)obj->height;
            idx++;
        }
    }
    
    return count;
}

int checkCollisionWithMap(SDL_Rect rect, SDL_Rect* colliders, int count) {
    for (int i = 0; i < count; i++) {
        if (checkAABB(rect.x, rect.y, rect.w, rect.h,
                      colliders[i].x, colliders[i].y,
                      colliders[i].w, colliders[i].h)) {
            return 1;  // 碰撞
        }
    }
    return 0;  // 无碰撞
}

void resolveCollision(float* x, float* y, float w, float h,
                      float dx, float dy,
                      SDL_Rect* colliders, int count) {
    // 分轴检测：先X后Y，避免角落卡住
    
    // 尝试X轴移动
    float newX = *x + dx;
    SDL_Rect rectX = {(int)newX, (int)*y, (int)w, (int)h};
    if (!checkCollisionWithMap(rectX, colliders, count)) {
        *x = newX;  // X轴移动成功
    }
    
    // 尝试Y轴移动
    float newY = *y + dy;
    SDL_Rect rectY = {(int)*x, (int)newY, (int)w, (int)h};
    if (!checkCollisionWithMap(rectY, colliders, count)) {
        *y = newY;  // Y轴移动成功
    }
}
```

**触发器系统：**

```c
// trigger.h
#ifndef TRIGGER_H
#define TRIGGER_H

#include "object_layer.h"

// 触发器回调函数类型
typedef void (*TriggerCallback)(MapObject* trigger, void* userData);

// 检查点是否在触发器内
int isPointInTrigger(float px, float py, MapObject* trigger);

// 检查并触发区域
void checkTriggers(ObjectLayer* layer, float px, float py,
                   TriggerCallback callback, void* userData);

#endif
```

```c
// trigger.c
#include "trigger.h"
#include <string.h>

int isPointInTrigger(float px, float py, MapObject* trigger) {
    return (px >= trigger->x && px <= trigger->x + trigger->width &&
            py >= trigger->y && py <= trigger->y + trigger->height);
}

void checkTriggers(ObjectLayer* layer, float px, float py,
                   TriggerCallback callback, void* userData) {
    for (int i = 0; i < layer->objectCount; i++) {
        MapObject* obj = &layer->objects[i];
        if (strcmp(obj->type, "trigger") == 0) {
            if (isPointInTrigger(px, py, obj)) {
                if (callback) callback(obj, userData);
            }
        }
    }
}
```

**使用示例 - 角色移动与碰撞：**

```c
// player.h
typedef struct {
    float x, y;         // 世界坐标
    float width, height;
    float speed;
    SDL_Texture* texture;
} Player;

void updatePlayer(Player* player, const Uint8* keystates,
                  SDL_Rect* colliders, int colliderCount,
                  float deltaTime) {
    float dx = 0, dy = 0;
    
    if (keystates[SDL_SCANCODE_LEFT] || keystates[SDL_SCANCODE_A]) dx -= player->speed * deltaTime;
    if (keystates[SDL_SCANCODE_RIGHT] || keystates[SDL_SCANCODE_D]) dx += player->speed * deltaTime;
    if (keystates[SDL_SCANCODE_UP] || keystates[SDL_SCANCODE_W]) dy -= player->speed * deltaTime;
    if (keystates[SDL_SCANCODE_DOWN] || keystates[SDL_SCANCODE_S]) dy += player->speed * deltaTime;
    
    // 碰撞检测并移动
    resolveCollision(&player->x, &player->y, 
                     player->width, player->height,
                     dx, dy, colliders, colliderCount);
}
```

```c
// 触发器回调示例
void onTriggerEnter(MapObject* trigger, void* userData) {
    const char* target = getObjectProperty(trigger, "target");
    if (target) {
        printf("进入触发器 '%s'，目标: %s\n", trigger->name, target);
        // 切换地图等操作
    }
}

// 在主循环中
checkTriggers(&objectLayer, player.x + player.width/2, 
              player.y + player.height/2, onTriggerEnter, NULL);
```

### 总结

- AABB 碰撞检测是最简单高效的矩形碰撞算法
- 从 Object Layer 提取 type="collision" 的对象作为碰撞体
- 分轴检测（先X后Y）避免角落卡住问题
- 触发器是 type="trigger" 的对象，用于区域事件
- 角色移动前预测新位置，碰撞则阻止该轴移动
- 碰撞体应在地图加载时提取并缓存，避免每帧重新解析
- 触发器检测用点是否在矩形内判断
- 触发器可以携带自定义属性（如目标地图），通过 getObjectProperty 获取
- 碰撞检测是游戏可玩性的基础，确保角色不穿墙
- 复杂形状（多边形）碰撞可用分离轴定理（SAT）实现

---

## 第18讲：多图层叠加渲染

### 概念

实际游戏地图通常有多个图层：背景层、地形层、装饰层、前景层等。正确的图层叠加顺序决定了视觉效果。本讲学习如何管理多种图层类型，按正确顺序渲染，并处理图层间的遮挡关系（如角色在树前面还是在树后面）。

### 原理

**图层渲染顺序（从下到上）：**

```
1. 背景图像图层（天空、远景）
2. 底层Tile图层（地面、地板）
3. 中层Tile图层（墙壁、建筑）
4. 角色和NPC（在地面之上，墙壁之下或之上）
5. 高层Tile图层（树冠、屋顶）
6. 前景图像图层（雾效、光照）
7. UI层（血条、小地图）
```

**图层类型枚举：**

```c
typedef enum {
    LAYER_IMAGE,     // 图像图层
    LAYER_TILE,      // 图块图层
    LAYER_OBJECT     // 对象图层
} LayerType;

typedef struct {
    LayerType type;
    char name[64];
    int visible;
    float opacity;
    union {
        ImageLayer image;
        TileLayer tile;
        ObjectLayer object;
    };
} MapLayer;
```

**角色与图层的遮挡：**

对于"角色在树后"的效果，需要将角色渲染插入到图层序列中：
1. 渲染地面图层
2. 渲染角色
3. 渲染树冠图层（覆盖角色）

这要求图层按 z-order 排序，角色有特定的 z 值。

### 例子

**统一图层管理：**

```c
// map.h
#ifndef MAP_H
#define MAP_H

#include "map_metadata.h"
#include "tileset.h"
#include "tile_layer.h"
#include "object_layer.h"
#include "image_layer.h"

#define MAX_LAYERS 32

typedef enum {
    LAYER_IMAGE,
    LAYER_TILE,
    LAYER_OBJECT
} LayerType;

typedef struct {
    LayerType type;
    char name[64];
    int visible;
    float opacity;
    // 使用指针避免union大小问题
    union {
        ImageLayer* imageLayer;
        TileLayer* tileLayer;
        ObjectLayer* objectLayer;
    };
} MapLayer;

typedef struct {
    MapMetadata metadata;
    Tileset tilesets[MAX_TILESETS];
    int tilesetCount;
    MapLayer layers[MAX_LAYERS];
    int layerCount;
    SDL_Rect* colliders;
    int colliderCount;
} GameMap;

// 加载完整地图
int loadMap(GameMap* map, const char* tmjPath, SDL_Renderer* renderer);

// 渲染地图（指定渲染到哪个图层索引为止）
void renderMap(SDL_Renderer* renderer, GameMap* map, const Camera* camera,
               int upToLayerIndex);

// 渲染所有图层
void renderMapAll(SDL_Renderer* renderer, GameMap* map, const Camera* camera);

// 释放地图资源
void freeMap(GameMap* map);

#endif
```

```c
// map.c
#include "map.h"
#include "tile_layer_renderer.h"
#include "image_layer.h"
#include <stdlib.h>
#include <string.h>

int loadMap(GameMap* map, const char* tmjPath, SDL_Renderer* renderer) {
    memset(map, 0, sizeof(GameMap));
    
    // 解析元数据
    if (parseMapMetadata(tmjPath, &map->metadata) != 0) return -1;
    
    // 读取JSON
    char* jsonText = readFileToString(tmjPath);
    cJSON* root = cJSON_Parse(jsonText);
    
    // 解析tilesets
    cJSON* tilesetsNode = cJSON_GetObjectItem(root, "tilesets");
    map->tilesetCount = cJSON_GetArraySize(tilesetsNode);
    for (int i = 0; i < map->tilesetCount; i++) {
        parseTileset(cJSON_GetArrayItem(tilesetsNode, i),
                    &map->tilesets[i], renderer, "assets");
    }
    
    // 解析图层
    cJSON* layersNode = cJSON_GetObjectItem(root, "layers");
    map->layerCount = cJSON_GetArraySize(layersNode);
    
    for (int i = 0; i < map->layerCount; i++) {
        cJSON* layerNode = cJSON_GetArrayItem(layersNode, i);
        const char* type = cJSON_GetObjectItem(layerNode, "type")->valuestring;
        MapLayer* ml = &map->layers[i];
        
        cJSON* name = cJSON_GetObjectItem(layerNode, "name");
        if (cJSON_IsString(name)) strncpy(ml->name, name->valuestring, 63);
        
        cJSON* visible = cJSON_GetObjectItem(layerNode, "visible");
        ml->visible = cJSON_IsTrue(visible) || visible == NULL;
        
        cJSON* opacity = cJSON_GetObjectItem(layerNode, "opacity");
        ml->opacity = cJSON_IsNumber(opacity) ? (float)opacity->valuedouble : 1.0f;
        
        if (strcmp(type, "tilelayer") == 0) {
            ml->type = LAYER_TILE;
            ml->tileLayer = malloc(sizeof(TileLayer));
            parseTileLayer(layerNode, ml->tileLayer);
        } else if (strcmp(type, "objectgroup") == 0) {
            ml->type = LAYER_OBJECT;
            ml->objectLayer = malloc(sizeof(ObjectLayer));
            parseObjectLayer(layerNode, ml->objectLayer);
            // 提取碰撞体
            if (strcmp(ml->name, "Collisions") == 0) {
                map->colliderCount = extractColliders(ml->objectLayer, &map->colliders);
            }
        } else if (strcmp(type, "imagelayer") == 0) {
            ml->type = LAYER_IMAGE;
            ml->imageLayer = malloc(sizeof(ImageLayer));
            parseImageLayer(layerNode, ml->imageLayer, renderer, "assets");
        }
    }
    
    cJSON_Delete(root);
    free(jsonText);
    return 0;
}

void renderMap(SDL_Renderer* renderer, GameMap* map, const Camera* camera,
               int upToLayerIndex) {
    int maxLayer = (upToLayerIndex < 0) ? map->layerCount : upToLayerIndex;
    
    for (int i = 0; i < maxLayer && i < map->layerCount; i++) {
        MapLayer* ml = &map->layers[i];
        if (!ml->visible) continue;
        
        switch (ml->type) {
            case LAYER_IMAGE:
                renderImageLayer(renderer, ml->imageLayer);
                break;
            case LAYER_TILE:
                renderTileLayerVisible(renderer, ml->tileLayer,
                                      &map->metadata, map->tilesets,
                                      map->tilesetCount,
                                      camera->x, camera->y,
                                      camera->width, camera->height);
                break;
            case LAYER_OBJECT:
                // 对象图层通常不渲染（除非有图块对象）
                break;
        }
    }
}

void renderMapAll(SDL_Renderer* renderer, GameMap* map, const Camera* camera) {
    renderMap(renderer, map, camera, -1);
}

void freeMap(GameMap* map) {
    for (int i = 0; i < map->layerCount; i++) {
        MapLayer* ml = &map->layers[i];
        switch (ml->type) {
            case LAYER_TILE:
                if (ml->tileLayer) { freeTileLayer(ml->tileLayer); free(ml->tileLayer); }
                break;
            case LAYER_OBJECT:
                if (ml->objectLayer) { freeObjectLayer(ml->objectLayer); free(ml->objectLayer); }
                break;
            case LAYER_IMAGE:
                if (ml->imageLayer) { freeImageLayer(ml->imageLayer); free(ml->imageLayer); }
                break;
        }
    }
    for (int i = 0; i < map->tilesetCount; i++) {
        freeTileset(&map->tilesets[i]);
    }
    if (map->colliders) free(map->colliders);
}
```

**角色插入图层渲染：**

```c
// 在主循环中实现角色遮挡
void renderGame(SDL_Renderer* renderer, GameMap* map, Camera* camera,
                Player* player) {
    SDL_SetRenderDrawColor(renderer, 0, 0, 0, 255);
    SDL_RenderClear(renderer);
    
    // 找到"Foreground"图层的索引
    int foregroundIdx = -1;
    for (int i = 0; i < map->layerCount; i++) {
        if (strcmp(map->layers[i].name, "Foreground") == 0) {
            foregroundIdx = i;
            break;
        }
    }
    
    // 渲染前景层之前的所有图层
    renderMap(renderer, map, camera, foregroundIdx);
    
    // 渲染角色
    int screenX, screenY;
    worldToScreen(camera, (int)player->x, (int)player->y, &screenX, &screenY);
    SDL_Rect dst = {screenX, screenY, (int)player->width, (int)player->height};
    SDL_RenderCopy(renderer, player->texture, NULL, &dst);
    
    // 渲染前景层及之后的图层（覆盖角色）
    for (int i = foregroundIdx; i >= 0 && i < map->layerCount; i++) {
        MapLayer* ml = &map->layers[i];
        if (!ml->visible) continue;
        if (ml->type == LAYER_TILE) {
            renderTileLayerVisible(renderer, ml->tileLayer, &map->metadata,
                                  map->tilesets, map->tilesetCount,
                                  camera->x, camera->y,
                                  camera->width, camera->height);
        }
    }
    
    SDL_RenderPresent(renderer);
}
```

### 总结

- 多图层叠加按从下到上顺序渲染：背景 → 地面 → 建筑 → 角色 → 前景
- 统一的 MapLayer 结构体管理不同类型图层，用 type 字段区分
- `renderMap` 支持渲染到指定图层索引，用于插入角色渲染
- 角色遮挡：渲染角色前先渲染下层图层，角色后再渲染上层图层
- 图层名称约定（如"Foreground"）用于定位插入点
- 对象图层通常不直接渲染，用于碰撞和触发器
- 碰撞体在加载地图时提取并缓存，避免每帧重新解析
- 释放地图时要释放所有图层指针和 tileset 纹理
- 良好的图层管理是复杂游戏场景的基础

---

## 第19讲：地图属性与自定义数据

### 概念

TMJ 文件支持自定义属性（Properties），可以附加在地图、图层、对象、图块等元素上。这些属性用于存储游戏逻辑数据，如背景音乐、天气、传送目标、NPC对话等。本讲学习如何解析和使用自定义属性，扩展地图的功能性。

### 原理

**属性的位置：**

| 附加对象 | 用途示例 |
|----------|----------|
| 地图 | 背景音乐、天气、关卡名 |
| 图层 | 渲染优先级、碰撞开关 |
| 对象 | NPC对话、物品ID、传送目标 |
| 图块 | 可通行性、伤害值、音效 |

**属性的 JSON 结构：**

```json
{
  "properties": [
    {"name": "bgm", "type": "string", "value": "forest.mp3"},
    {"name": "weather", "type": "string", "value": "rain"},
    {"name": "level", "type": "int", "value": 1},
    {"name": "isDungeon", "type": "bool", "value": false}
  ]
}
```

**属性类型：**
- `string`：字符串
- `int`：整数
- `float`：浮点数
- `bool`：布尔值
- `file`：文件路径
- `color`：颜色值（#RRGGBBAA）
- `object`：对象引用（ID）

**属性存储策略：**
统一用字符串存储所有属性值，使用时按需转换。这样简化数据结构，避免类型判断的复杂性。

### 例子

**通用属性系统：**

```c
// properties.h
#ifndef PROPERTIES_H
#define PROPERTIES_H

#include "cJSON.h"

typedef struct {
    char name[64];
    char type[16];
    char value[256];
} Property;

typedef struct {
    Property* items;
    int count;
} PropertyList;

// 解析属性列表
int parseProperties(cJSON* propsNode, PropertyList* list);

// 获取字符串属性
const char* getPropertyString(PropertyList* list, const char* name);

// 获取整数属性
int getPropertyInt(PropertyList* list, const char* name, int defaultValue);

// 获取浮点属性
float getPropertyFloat(PropertyList* list, const char* name, float defaultValue);

// 获取布尔属性
int getPropertyBool(PropertyList* list, const char* name, int defaultValue);

// 释放属性列表
void freeProperties(PropertyList* list);

#endif
```

```c
// properties.c
#include "properties.h"
#include <stdlib.h>
#include <string.h>

int parseProperties(cJSON* propsNode, PropertyList* list) {
    list->items = NULL;
    list->count = 0;
    
    if (!cJSON_IsArray(propsNode)) return 0;
    
    list->count = cJSON_GetArraySize(propsNode);
    list->items = (Property*)calloc(list->count, sizeof(Property));
    
    for (int i = 0; i < list->count; i++) {
        cJSON* prop = cJSON_GetArrayItem(propsNode, i);
        cJSON* name = cJSON_GetObjectItem(prop, "name");
        cJSON* type = cJSON_GetObjectItem(prop, "type");
        cJSON* value = cJSON_GetObjectItem(prop, "value");
        
        if (cJSON_IsString(name)) strncpy(list->items[i].name, name->valuestring, 63);
        if (cJSON_IsString(type)) strncpy(list->items[i].type, type->valuestring, 15);
        
        if (cJSON_IsString(value)) {
            strncpy(list->items[i].value, value->valuestring, 255);
        } else if (cJSON_IsNumber(value)) {
            snprintf(list->items[i].value, 255, "%g", value->valuedouble);
        } else if (cJSON_IsBool(value)) {
            strcpy(list->items[i].value, cJSON_IsTrue(value) ? "true" : "false");
        }
    }
    
    return 0;
}

const char* getPropertyString(PropertyList* list, const char* name) {
    for (int i = 0; i < list->count; i++) {
        if (strcmp(list->items[i].name, name) == 0) {
            return list->items[i].value;
        }
    }
    return NULL;
}

int getPropertyInt(PropertyList* list, const char* name, int defaultValue) {
    const char* val = getPropertyString(list, name);
    return val ? atoi(val) : defaultValue;
}

float getPropertyFloat(PropertyList* list, const char* name, float defaultValue) {
    const char* val = getPropertyString(list, name);
    return val ? (float)atof(val) : defaultValue;
}

int getPropertyBool(PropertyList* list, const char* name, int defaultValue) {
    const char* val = getPropertyString(list, name);
    if (!val) return defaultValue;
    return (strcmp(val, "true") == 0 || strcmp(val, "1") == 0);
}

void freeProperties(PropertyList* list) {
    if (list->items) free(list->items);
    list->items = NULL;
    list->count = 0;
}
```

**在地图结构中集成属性：**

```c
// 扩展 GameMap 结构
typedef struct {
    MapMetadata metadata;
    PropertyList mapProperties;  // 地图级属性
    Tileset tilesets[MAX_TILESETS];
    int tilesetCount;
    MapLayer layers[MAX_LAYERS];
    int layerCount;
    SDL_Rect* colliders;
    int colliderCount;
} GameMap;

// 加载时解析地图属性
int loadMap(GameMap* map, const char* tmjPath, SDL_Renderer* renderer) {
    // ... 其他加载代码 ...
    
    // 解析地图属性
    cJSON* propsNode = cJSON_GetObjectItem(root, "properties");
    parseProperties(propsNode, &map->mapProperties);
    
    // ... 
}
```

**使用属性示例：**

```c
// 使用地图属性
void applyMapProperties(GameMap* map) {
    // 播放背景音乐
    const char* bgm = getPropertyString(&map->mapProperties, "bgm");
    if (bgm) {
        printf("播放背景音乐: %s\n", bgm);
        // playMusic(bgm);
    }
    
    // 设置天气
    const char* weather = getPropertyString(&map->mapProperties, "weather");
    if (weather) {
        printf("天气: %s\n", weather);
        // setWeather(weather);
    }
    
    // 获取关卡等级
    int level = getPropertyInt(&map->mapProperties, "level", 1);
    printf("关卡等级: %d\n", level);
}

// 使用对象属性（传送门）
void handlePortal(MapObject* portal) {
    const char* targetMap = getPropertyString(
        /* portal的属性列表 */, "targetMap");
    const char* targetX = getPropertyString(
        /* portal的属性列表 */, "targetX");
    const char* targetY = getPropertyString(
        /* portal的属性列表 */, "targetY");
    
    if (targetMap) {
        printf("传送到: %s (%s, %s)\n", targetMap, targetX, targetY);
        // loadMap(targetMap);
        // setPlayerPosition(atoi(targetX), atoi(targetY));
    }
}
```

**图块属性（可通行性）：**

```c
// 解析tileset中图块的自定义属性
typedef struct {
    int tileId;
    PropertyList properties;
} TileProperties;

// 检查图块是否可通行
int isTilePassable(TileProperties* tileProps, int count, int gid, Tileset* tileset) {
    int localId = (gid & 0x1FFFFFFF) - tileset->firstgid;
    
    for (int i = 0; i < count; i++) {
        if (tileProps[i].tileId == localId) {
            return getPropertyBool(&tileProps[i].properties, "passable", 1);
        }
    }
    return 1;  // 默认可通行
}
```

### 总结

- 自定义属性可以附加在地图、图层、对象、图块上，存储游戏逻辑数据
- 属性类型有 string/int/float/bool/file/color 等
- 统一用字符串存储属性值，使用时按需转换，简化数据结构
- `getPropertyString/Int/Float/Bool` 提供类型安全的访问
- 地图属性用于全局设置（BGM、天气、关卡名）
- 对象属性用于交互逻辑（传送目标、NPC对话、物品ID）
- 图块属性用于游戏规则（可通行性、伤害值、音效）
- 属性系统让地图数据与游戏逻辑解耦，美术和程序可以独立工作
- 解析属性时注意处理缺失字段，提供默认值
- 属性列表使用后要释放内存

---

# 第6章：实战项目

## 第20讲：完整地图加载器封装

### 概念

本讲将前面学到的所有模块整合为一个完整的、可复用的地图加载器。封装后的加载器提供简洁的 API，只需几行代码就能加载 TMJ 地图并渲染。这是实际游戏项目中可以直接使用的代码。

### 原理

**封装设计原则：**

1. **单一入口**：一个函数完成所有加载工作
2. **隐藏细节**：内部管理 tileset、图层、碰撞体等
3. **简洁API**：外部调用简单明了
4. **错误处理**：加载失败有明确反馈
5. **资源管理**：自动释放所有资源

**API 设计：**

```c
// 加载地图
GameMap* map = loadGameMap("map.tmj", renderer);

// 渲染地图
renderGameMap(renderer, map, &camera);

// 获取碰撞体
SDL_Rect* colliders = getMapColliders(map, &count);

// 获取对象
MapObject* spawn = findMapObject(map, "PlayerSpawn");

// 释放地图
freeGameMap(map);
```

### 例子

**完整地图加载器头文件：**

```c
// game_map.h
#ifndef GAME_MAP_H
#define GAME_MAP_H

#include <SDL2/SDL.h>
#include "camera.h"
#include "object_layer.h"
#include "properties.h"

#define MAX_TILESETS 16
#define MAX_LAYERS 32

typedef enum {
    GLAYER_IMAGE,
    GLAYER_TILE,
    GLAYER_OBJECT
} GameLayerType;

typedef struct {
    GameLayerType type;
    char name[64];
    int visible;
    float opacity;
    void* data;  // 指向具体图层类型
} GameLayer;

typedef struct {
    // 元数据
    int width, height;
    int tileWidth, tileHeight;
    
    // Tilesets
    Tileset tilesets[MAX_TILESETS];
    int tilesetCount;
    
    // 图层
    GameLayer layers[MAX_LAYERS];
    int layerCount;
    
    // 碰撞体
    SDL_Rect* colliders;
    int colliderCount;
    
    // 地图属性
    PropertyList properties;
    
    // 动画
    TileAnimation* animations;
    int animationCount;
} GameMap;

// === 公共 API ===

// 加载地图
GameMap* loadGameMap(const char* tmjPath, SDL_Renderer* renderer);

// 释放地图
void freeGameMap(GameMap* map);

// 渲染地图（全部图层）
void renderGameMap(SDL_Renderer* renderer, GameMap* map, const Camera* camera);

// 渲染地图到指定图层（用于角色遮挡）
void renderGameMapToLayer(SDL_Renderer* renderer, GameMap* map, 
                          const Camera* camera, int layerIndex);

// 获取碰撞体
SDL_Rect* getMapColliders(GameMap* map, int* count);

// 按名称查找对象
MapObject* findMapObject(GameMap* map, const char* name);

// 按类型查找对象
MapObject* findMapObjectByType(GameMap* map, const char* type);

// 获取地图属性
const char* getMapProperty(GameMap* map, const char* name);

// 检查图块是否可通行
int isTilePassable(GameMap* map, int tileX, int tileY, const char* layerName);

#endif
```

**完整实现：**

```c
// game_map.c
#include "game_map.h"
#include "tileset.h"
#include "tile_layer.h"
#include "object_layer.h"
#include "image_layer.h"
#include "tile_layer_renderer.h"
#include "optimized_renderer.h"
#include "animation.h"
#include "collision.h"
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// 内部函数：读取文件
static char* readFile(const char* path) {
    FILE* f = fopen(path, "rb");
    if (!f) return NULL;
    fseek(f, 0, SEEK_END);
    long len = ftell(f);
    fseek(f, 0, SEEK_SET);
    char* buf = malloc(len + 1);
    if (buf) {
        size_t read = fread(buf, 1, len, f);
        buf[read] = '\0';
    }
    fclose(f);
    return buf;
}

// 内部函数：提取路径的目录部分
static void getDirectory(char* dir, const char* path) {
    strncpy(dir, path, 255);
    char* lastSlash = strrchr(dir, '/');
    if (lastSlash) {
        *lastSlash = '\0';
    } else {
        strcpy(dir, ".");
    }
}

GameMap* loadGameMap(const char* tmjPath, SDL_Renderer* renderer) {
    char* jsonText = readFile(tmjPath);
    if (!jsonText) {
        printf("错误: 无法读取地图文件 %s\n", tmjPath);
        return NULL;
    }
    
    cJSON* root = cJSON_Parse(jsonText);
    if (!root) {
        printf("错误: JSON解析失败\n");
        free(jsonText);
        return NULL;
    }
    
    GameMap* map = (GameMap*)calloc(1, sizeof(GameMap));
    if (!map) {
        cJSON_Delete(root);
        free(jsonText);
        return NULL;
    }
    
    // 提取基础目录（用于解析相对路径）
    char basePath[256];
    getDirectory(basePath, tmjPath);
    
    // 解析元数据
    cJSON* w = cJSON_GetObjectItem(root, "width");
    cJSON* h = cJSON_GetObjectItem(root, "height");
    cJSON* tw = cJSON_GetObjectItem(root, "tilewidth");
    cJSON* th = cJSON_GetObjectItem(root, "tileheight");
    map->width = cJSON_IsNumber(w) ? w->valueint : 0;
    map->height = cJSON_IsNumber(h) ? h->valueint : 0;
    map->tileWidth = cJSON_IsNumber(tw) ? tw->valueint : 32;
    map->tileHeight = cJSON_IsNumber(th) ? th->valueint : 32;
    
    // 解析tilesets
    cJSON* tilesetsNode = cJSON_GetObjectItem(root, "tilesets");
    if (cJSON_IsArray(tilesetsNode)) {
        map->tilesetCount = cJSON_GetArraySize(tilesetsNode);
        for (int i = 0; i < map->tilesetCount && i < MAX_TILESETS; i++) {
            parseTileset(cJSON_GetArrayItem(tilesetsNode, i),
                        &map->tilesets[i], renderer, basePath);
            
            // 解析动画
            cJSON* tilesNode = cJSON_GetObjectItem(
                cJSON_GetArrayItem(tilesetsNode, i), "tiles");
            if (tilesNode) {
                TileAnimation* anims;
                int animCount;
                parseTileAnimations(tilesNode, &anims, &animCount);
                // 合并到全局动画数组（简化处理）
                map->animations = anims;
                map->animationCount = animCount;
            }
        }
    }
    
    // 解析图层
    cJSON* layersNode = cJSON_GetObjectItem(root, "layers");
    if (cJSON_IsArray(layersNode)) {
        map->layerCount = cJSON_GetArraySize(layersNode);
        for (int i = 0; i < map->layerCount && i < MAX_LAYERS; i++) {
            cJSON* layerNode = cJSON_GetArrayItem(layersNode, i);
            const char* type = cJSON_GetObjectItem(layerNode, "type")->valuestring;
            GameLayer* gl = &map->layers[i];
            
            cJSON* name = cJSON_GetObjectItem(layerNode, "name");
            if (cJSON_IsString(name)) strncpy(gl->name, name->valuestring, 63);
            
            cJSON* visible = cJSON_GetObjectItem(layerNode, "visible");
            gl->visible = cJSON_IsTrue(visible) || visible == NULL;
            
            cJSON* opacity = cJSON_GetObjectItem(layerNode, "opacity");
            gl->opacity = cJSON_IsNumber(opacity) ? (float)opacity->valuedouble : 1.0f;
            
            if (strcmp(type, "tilelayer") == 0) {
                gl->type = GLAYER_TILE;
                gl->data = malloc(sizeof(TileLayer));
                parseTileLayer(layerNode, (TileLayer*)gl->data);
            } else if (strcmp(type, "objectgroup") == 0) {
                gl->type = GLAYER_OBJECT;
                gl->data = malloc(sizeof(ObjectLayer));
                parseObjectLayer(layerNode, (ObjectLayer*)gl->data);
                
                // 提取碰撞体
                if (strcmp(gl->name, "Collisions") == 0 ||
                    strcmp(gl->name, "Collision") == 0) {
                    map->colliderCount = extractColliders(
                        (ObjectLayer*)gl->data, &map->colliders);
                }
            } else if (strcmp(type, "imagelayer") == 0) {
                gl->type = GLAYER_IMAGE;
                gl->data = malloc(sizeof(ImageLayer));
                parseImageLayer(layerNode, (ImageLayer*)gl->data,
                               renderer, basePath);
            }
        }
    }
    
    // 解析地图属性
    cJSON* propsNode = cJSON_GetObjectItem(root, "properties");
    parseProperties(propsNode, &map->properties);
    
    cJSON_Delete(root);
    free(jsonText);
    
    printf("地图加载完成: %dx%d, %d个tileset, %d个图层\n",
        map->width, map->height, map->tilesetCount, map->layerCount);
    
    return map;
}

void freeGameMap(GameMap* map) {
    if (!map) return;
    
    // 释放图层
    for (int i = 0; i < map->layerCount; i++) {
        GameLayer* gl = &map->layers[i];
        if (!gl->data) continue;
        
        switch (gl->type) {
            case GLAYER_TILE:
                freeTileLayer((TileLayer*)gl->data);
                break;
            case GLAYER_OBJECT:
                freeObjectLayer((ObjectLayer*)gl->data);
                break;
            case GLAYER_IMAGE:
                freeImageLayer((ImageLayer*)gl->data);
                break;
        }
        free(gl->data);
    }
    
    // 释放tilesets
    for (int i = 0; i < map->tilesetCount; i++) {
        freeTileset(&map->tilesets[i]);
    }
    
    // 释放碰撞体
    if (map->colliders) free(map->colliders);
    
    // 释放动画
    if (map->animations) freeAnimations(map->animations, map->animationCount);
    
    // 释放属性
    freeProperties(&map->properties);
    
    free(map);
}

void renderGameMap(SDL_Renderer* renderer, GameMap* map, const Camera* camera) {
    renderGameMapToLayer(renderer, map, camera, -1);
}

void renderGameMapToLayer(SDL_Renderer* renderer, GameMap* map,
                          const Camera* camera, int layerIndex) {
    int maxLayer = (layerIndex < 0) ? map->layerCount : layerIndex;
    Uint32 elapsed = SDL_GetTicks();
    
    for (int i = 0; i < maxLayer && i < map->layerCount; i++) {
        GameLayer* gl = &map->layers[i];
        if (!gl->visible) continue;
        
        switch (gl->type) {
            case GLAYER_IMAGE:
                renderImageLayer(renderer, (ImageLayer*)gl->data);
                break;
            case GLAYER_TILE: {
                TileLayer* tl = (TileLayer*)gl->data;
                // 使用优化渲染
                Camera cam = *camera;
                renderTileLayerOptimized(renderer, tl,
                    &(MapMetadata){.tileWidth=map->tileWidth, .tileHeight=map->tileHeight},
                    map->tilesets, map->tilesetCount, &cam);
                break;
            }
            case GLAYER_OBJECT:
                // 对象图层不直接渲染
                break;
        }
    }
}

SDL_Rect* getMapColliders(GameMap* map, int* count) {
    if (count) *count = map->colliderCount;
    return map->colliders;
}

MapObject* findMapObject(GameMap* map, const char* name) {
    for (int i = 0; i < map->layerCount; i++) {
        GameLayer* gl = &map->layers[i];
        if (gl->type == GLAYER_OBJECT) {
            MapObject* obj = findObjectByName((ObjectLayer*)gl->data, name);
            if (obj) return obj;
        }
    }
    return NULL;
}

MapObject* findMapObjectByType(GameMap* map, const char* type) {
    for (int i = 0; i < map->layerCount; i++) {
        GameLayer* gl = &map->layers[i];
        if (gl->type == GLAYER_OBJECT) {
            MapObject* obj = findObjectByType((ObjectLayer*)gl->data, type);
            if (obj) return obj;
        }
    }
    return NULL;
}

const char* getMapProperty(GameMap* map, const char* name) {
    return getPropertyString(&map->properties, name);
}
```

**使用示例：**

```c
// main.c - 使用封装好的地图加载器
#include "game_map.h"
#include "camera.h"

int main() {
    SDL_Init(SDL_INIT_VIDEO);
    IMG_Init(IMG_INIT_PNG);
    
    SDL_Window* window = SDL_CreateWindow("Game Map Demo",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 800, 600, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1,
        SDL_RENDERER_ACCELERATED);
    
    // 一行代码加载地图
    GameMap* map = loadGameMap("assets/level1.tmj", renderer);
    if (!map) {
        printf("地图加载失败\n");
        return 1;
    }
    
    // 获取碰撞体
    int colliderCount;
    SDL_Rect* colliders = getMapColliders(map, &colliderCount);
    
    // 查找出生点
    MapObject* spawn = findMapObjectByType(map, "spawn");
    float playerX = spawn ? spawn->x : 0;
    float playerY = spawn ? spawn->y : 0;
    
    // 初始化摄像机
    Camera camera;
    initCamera(&camera, 800, 600);
    
    // 主循环
    SDL_Event e;
    int quit = 0;
    const Uint8* keys = SDL_GetKeyboardState(NULL);
    
    while (!quit) {
        while (SDL_PollEvent(&e)) {
            if (e.type == SDL_QUIT) quit = 1;
        }
        
        // 角色移动
        float dx = 0, dy = 0;
        if (keys[SDL_SCANCODE_A]) dx -= 3;
        if (keys[SDL_SCANCODE_D]) dx += 3;
        if (keys[SDL_SCANCODE_W]) dy -= 3;
        if (keys[SDL_SCANCODE_S]) dy += 3;
        
        resolveCollision(&playerX, &playerY, 32, 32, dx, dy,
                        colliders, colliderCount);
        
        // 摄像机跟随
        cameraFollow(&camera, (int)playerX, (int)playerY,
                    &(MapMetadata){.width=map->width, .height=map->height,
                                  .tileWidth=map->tileWidth, .tileHeight=map->tileHeight});
        
        // 渲染
        SDL_SetRenderDrawColor(renderer, 0, 0, 0, 255);
        SDL_RenderClear(renderer);
        
        renderGameMap(renderer, map, &camera);
        
        // 渲染角色（简单矩形）
        int screenX = (int)playerX - camera.x;
        int screenY = (int)playerY - camera.y;
        SDL_Rect playerRect = {screenX, screenY, 32, 32};
        SDL_SetRenderDrawColor(renderer, 255, 0, 0, 255);
        SDL_RenderFillRect(renderer, &playerRect);
        
        SDL_RenderPresent(renderer);
        SDL_Delay(16);
    }
    
    // 一行代码释放地图
    freeGameMap(map);
    
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    IMG_Quit();
    SDL_Quit();
    
    return 0;
}
```

### 总结

- 封装后的加载器提供简洁 API：`loadGameMap` / `renderGameMap` / `freeGameMap`
- 内部自动处理 tileset、图层、碰撞体、动画、属性的解析
- `getDirectory` 提取 TMJ 文件所在目录，用于解析相对路径
- `findMapObject` 遍历所有对象图层查找对象
- `getMapProperty` 获取地图级自定义属性
- 封装隐藏了复杂的内部结构，外部只需关心 GameMap 指针
- 错误处理：加载失败返回 NULL，调用方检查返回值
- 资源管理：`freeGameMap` 释放所有内部资源，无内存泄漏
- 这个加载器可以直接用于实际游戏项目
- 后续可以扩展功能：等距地图、无限地图、TSX 外部引用等

---

## 第21讲：角色在地图上移动

### 概念

本讲实现一个完整的角色控制系统，包括角色加载、键盘控制、碰撞检测、动画播放和摄像机跟随。角色能在 TMJ 地图上自由移动，不穿墙，摄像机平滑跟随。这是 2D 游戏的核心功能。

### 原理

**角色系统组成：**

1. **角色数据**：位置、尺寸、速度、朝向、动画状态
2. **输入处理**：键盘/手柄输入转换为移动方向
3. **物理更新**：根据输入更新位置，处理碰撞
4. **动画系统**：根据状态和朝向播放对应动画
5. **渲染**：在正确位置绘制角色

**角色动画状态机：**

```
站立 → 移动 → 站立
  ↓      ↓
攻击   攻击
```

**朝向处理：**
- 4方向：上、下、左、右
- 8方向：加四个对角线
- 根据移动方向选择对应的精灵图

**帧率无关移动：**

```
位移 = 速度 × 时间增量（deltaTime）
deltaTime = (当前时间 - 上一帧时间) / 1000.0  // 秒
```

这样无论帧率高低，角色移动速度一致。

### 例子

**角色结构体：**

```c
// player.h
#ifndef PLAYER_H
#define PLAYER_H

#include <SDL2/SDL.h>

typedef enum {
    DIR_DOWN,
    DIR_UP,
    DIR_LEFT,
    DIR_RIGHT
} Direction;

typedef enum {
    STATE_IDLE,
    STATE_WALKING
} PlayerState;

typedef struct {
    float x, y;             // 世界坐标
    float width, height;    // 尺寸
    float speed;            // 移动速度（像素/秒）
    
    Direction direction;    // 朝向
    PlayerState state;      // 状态
    
    // 动画
    SDL_Texture* texture;   // 精灵图
    int frameWidth;         // 单帧宽度
    int frameHeight;        // 单帧高度
    int currentFrame;       // 当前帧
    float animTimer;        // 动画计时器
    float animSpeed;        // 动画速度（秒/帧）
    
    int framesPerDirection; // 每个方向的帧数
} Player;

// 初始化角色
void initPlayer(Player* p, SDL_Renderer* renderer, const char* spritePath);

// 更新角色（输入+物理+动画）
void updatePlayer(Player* p, const Uint8* keys, float deltaTime,
                  SDL_Rect* colliders, int colliderCount);

// 渲染角色
void renderPlayer(SDL_Renderer* renderer, Player* p, const Camera* camera);

// 释放角色资源
void freePlayer(Player* p);

#endif
```

```c
// player.c
#include "player.h"
#include "collision.h"
#include "camera.h"
#include <SDL2/SDL_image.h>
#include <stdio.h>
#include <math.h>

void initPlayer(Player* p, SDL_Renderer* renderer, const char* spritePath) {
    memset(p, 0, sizeof(Player));
    
    p->x = 100;
    p->y = 100;
    p->width = 32;
    p->height = 32;
    p->speed = 150.0f;  // 150像素/秒
    
    p->direction = DIR_DOWN;
    p->state = STATE_IDLE;
    
    // 加载精灵图
    p->texture = IMG_LoadTexture(renderer, spritePath);
    if (p->texture) {
        int texW, texH;
        SDL_QueryTexture(p->texture, NULL, NULL, &texW, &texH);
        p->frameWidth = texW / 4;  // 假设4列（4个方向）
        p->frameHeight = texH / 4; // 假设4行（每方向4帧）
        p->framesPerDirection = 4;
    } else {
        p->frameWidth = 32;
        p->frameHeight = 32;
        p->framesPerDirection = 1;
    }
    
    p->currentFrame = 0;
    p->animTimer = 0;
    p->animSpeed = 0.15f;  // 每帧0.15秒
}

void updatePlayer(Player* p, const Uint8* keys, float deltaTime,
                  SDL_Rect* colliders, int colliderCount) {
    float dx = 0, dy = 0;
    
    // 输入处理
    if (keys[SDL_SCANCODE_LEFT] || keys[SDL_SCANCODE_A]) {
        dx -= 1;
        p->direction = DIR_LEFT;
    }
    if (keys[SDL_SCANCODE_RIGHT] || keys[SDL_SCANCODE_D]) {
        dx += 1;
        p->direction = DIR_RIGHT;
    }
    if (keys[SDL_SCANCODE_UP] || keys[SDL_SCANCODE_W]) {
        dy -= 1;
        p->direction = DIR_UP;
    }
    if (keys[SDL_SCANCODE_DOWN] || keys[SDL_SCANCODE_S]) {
        dy += 1;
        p->direction = DIR_DOWN;
    }
    
    // 对角线移动归一化（避免对角线更快）
    if (dx != 0 && dy != 0) {
        dx *= 0.7071f;  // 1/sqrt(2)
        dy *= 0.7071f;
    }
    
    // 应用速度
    dx *= p->speed * deltaTime;
    dy *= p->speed * deltaTime;
    
    // 更新状态
    if (dx != 0 || dy != 0) {
        p->state = STATE_WALKING;
    } else {
        p->state = STATE_IDLE;
    }
    
    // 碰撞检测并移动
    resolveCollision(&p->x, &p->y, p->width, p->height, dx, dy,
                    colliders, colliderCount);
    
    // 动画更新
    if (p->state == STATE_WALKING) {
        p->animTimer += deltaTime;
        if (p->animTimer >= p->animSpeed) {
            p->animTimer = 0;
            p->currentFrame = (p->currentFrame + 1) % p->framesPerDirection;
        }
    } else {
        p->currentFrame = 0;  // 站立时显示第一帧
    }
}

void renderPlayer(SDL_Renderer* renderer, Player* p, const Camera* camera) {
    if (!p->texture) {
        // 没有纹理，绘制矩形
        int sx, sy;
        worldToScreen(camera, (int)p->x, (int)p->y, &sx, &sy);
        SDL_Rect dst = {sx, sy, (int)p->width, (int)p->height};
        SDL_SetRenderDrawColor(renderer, 255, 0, 0, 255);
        SDL_RenderFillRect(renderer, &dst);
        return;
    }
    
    // 计算源矩形（精灵图中的位置）
    SDL_Rect src = {
        .x = p->currentFrame * p->frameWidth,
        .y = p->direction * p->frameHeight,  // 行对应方向
        .w = p->frameWidth,
        .h = p->frameHeight
    };
    
    // 计算目标矩形（屏幕位置）
    int sx, sy;
    worldToScreen(camera, (int)p->x, (int)p->y, &sx, &sy);
    SDL_Rect dst = {sx, sy, (int)p->width, (int)p->height};
    
    SDL_RenderCopy(renderer, p->texture, &src, &dst);
}

void freePlayer(Player* p) {
    if (p->texture) {
        SDL_DestroyTexture(p->texture);
        p->texture = NULL;
    }
}
```

**完整游戏主循环：**

```c
// main.c - 完整的角色移动游戏
#include "game_map.h"
#include "player.h"
#include "camera.h"
#include <SDL2/SDL.h>

int main() {
    SDL_Init(SDL_INIT_VIDEO);
    IMG_Init(IMG_INIT_PNG);
    
    SDL_Window* window = SDL_CreateWindow("TMJ Map Game",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 800, 600, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1,
        SDL_RENDERER_ACCELERATED | SDL_RENDERER_PRESENTVSYNC);
    
    // 加载地图
    GameMap* map = loadGameMap("assets/level1.tmj", renderer);
    if (!map) return 1;
    
    // 获取碰撞体
    int colliderCount;
    SDL_Rect* colliders = getMapColliders(map, &colliderCount);
    
    // 初始化角色
    Player player;
    initPlayer(&player, renderer, "assets/player.png");
    
    // 从地图对象设置出生点
    MapObject* spawn = findMapObjectByType(map, "spawn");
    if (spawn) {
        player.x = spawn->x;
        player.y = spawn->y;
    }
    
    // 初始化摄像机
    Camera camera;
    initCamera(&camera, 800, 600);
    
    // 主循环
    SDL_Event e;
    int quit = 0;
    Uint32 lastTime = SDL_GetTicks();
    
    while (!quit) {
        // 计算deltaTime
        Uint32 currentTime = SDL_GetTicks();
        float deltaTime = (currentTime - lastTime) / 1000.0f;
        lastTime = currentTime;
        
        // 限制deltaTime（避免暂停后大跳）
        if (deltaTime > 0.1f) deltaTime = 0.1f;
        
        // 事件处理
        while (SDL_PollEvent(&e)) {
            if (e.type == SDL_QUIT) quit = 1;
        }
        
        const Uint8* keys = SDL_GetKeyboardState(NULL);
        
        // 更新角色
        updatePlayer(&player, keys, deltaTime, colliders, colliderCount);
        
        // 摄像机跟随角色
        cameraFollow(&camera, (int)(player.x + player.width/2),
                     (int)(player.y + player.height/2),
                     &(MapMetadata){.width=map->width, .height=map->height,
                                   .tileWidth=map->tileWidth, .tileHeight=map->tileHeight});
        
        // 渲染
        SDL_SetRenderDrawColor(renderer, 100, 149, 237, 255);  // 背景色
        SDL_RenderClear(renderer);
        
        // 渲染地图
        renderGameMap(renderer, map, &camera);
        
        // 渲染角色
        renderPlayer(renderer, &player, &camera);
        
        SDL_RenderPresent(renderer);
    }
    
    // 清理
    freePlayer(&player);
    freeGameMap(map);
    
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    IMG_Quit();
    SDL_Quit();
    
    return 0;
}
```

### 总结

- 角色系统包含：位置、速度、朝向、状态、动画
- 帧率无关移动：`位移 = 速度 × deltaTime`，确保不同帧率下速度一致
- 对角线移动需归一化（乘以 0.7071），避免比直线快
- 碰撞检测使用分轴法（先X后Y），避免角落卡住
- 动画根据状态切换：移动时播放行走动画，站立时显示第一帧
- 精灵图布局：行对应方向，列对应帧
- 摄像机跟随角色中心，并限制在地图边界内
- `SDL_RENDERER_PRESENTVSYNC` 启用垂直同步，自动限制帧率
- deltaTime 应限制最大值，避免窗口暂停后角色大跳
- 出生点从地图对象读取，实现数据驱动

---

## 第22讲：综合实战：2D游戏地图系统

### 概念

本讲是课程的总结，将所有知识整合为一个完整的 2D 游戏地图系统。系统支持多地图切换、NPC 对话、物品拾取、传送门、动画图块等完整功能。这是课程所学知识的综合应用，也是实际游戏开发的起点。

### 原理

**完整游戏系统架构：**

```
游戏主循环
├── 事件处理（输入、窗口事件）
├── 更新阶段
│   ├── 角色更新（输入、物理、动画）
│   ├── NPC更新（AI、对话）
│   ├── 触发器检测（传送、事件）
│   └── 摄像机更新
├── 渲染阶段
│   ├── 清屏
│   ├── 渲染地图（背景层）
│   ├── 渲染NPC和角色
│   ├── 渲染地图（前景层）
│   ├── 渲染UI
│   └── 呈现
└── 帧率控制
```

**地图切换流程：**

```
角色进入传送门触发器
  → 获取目标地图路径和坐标
  → 释放当前地图
  → 加载新地图
  → 设置角色新位置
  → 重置摄像机
```

**NPC 对话系统：**
- NPC 是 Object Layer 中的对象（type="npc"）
- 对话内容存储在对象属性中（dialogue 属性）
- 角色靠近 NPC 按键交互时显示对话

### 例子

**游戏状态管理：**

```c
// game.h
#ifndef GAME_H
#define GAME_H

#include "game_map.h"
#include "player.h"
#include "camera.h"

typedef enum {
    GAME_STATE_PLAYING,
    GAME_STATE_DIALOGUE,
    GAME_STATE_MENU
} GameState;

typedef struct {
    SDL_Window* window;
    SDL_Renderer* renderer;
    
    GameMap* currentMap;
    Player player;
    Camera camera;
    
    GameState state;
    int running;
    
    // 对话系统
    char currentDialogue[512];
    float dialogueTimer;
    
    // 当前地图路径（用于切换）
    char currentMapPath[256];
} Game;

// 初始化游戏
int initGame(Game* game, const char* title, int width, int height);

// 加载地图
int loadGameMap(Game* game, const char* mapPath);

// 主循环
void runGame(Game* game);

// 处理事件
void handleEvents(Game* game);

// 更新游戏
void updateGame(Game* game, float deltaTime);

// 渲染游戏
void renderGame(Game* game);

// 清理游戏
void cleanupGame(Game* game);

#endif
```

```c
// game.c
#include "game.h"
#include "collision.h"
#include "trigger.h"
#include <SDL2/SDL_image.h>
#include <stdio.h>
#include <string.h>

int initGame(Game* game, const char* title, int width, int height) {
    memset(game, 0, sizeof(Game));
    
    if (SDL_Init(SDL_INIT_VIDEO) < 0) return -1;
    IMG_Init(IMG_INIT_PNG);
    
    game->window = SDL_CreateWindow(title,
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED,
        width, height, 0);
    game->renderer = SDL_CreateRenderer(game->window, -1,
        SDL_RENDERER_ACCELERATED | SDL_RENDERER_PRESENTVSYNC);
    
    initCamera(&game->camera, width, height);
    initPlayer(&game->player, game->renderer, "assets/player.png");
    
    game->state = GAME_STATE_PLAYING;
    game->running = 1;
    
    return 0;
}

int loadGameMap(Game* game, const char* mapPath) {
    // 释放旧地图
    if (game->currentMap) {
        freeGameMap(game->currentMap);
        game->currentMap = NULL;
    }
    
    // 加载新地图
    game->currentMap = loadGameMap(mapPath, game->renderer);
    if (!game->currentMap) return -1;
    
    strncpy(game->currentMapPath, mapPath, 255);
    
    // 设置出生点
    MapObject* spawn = findMapObjectByType(game->currentMap, "spawn");
    if (spawn) {
        game->player.x = spawn->x;
        game->player.y = spawn->y;
    }
    
    return 0;
}

// 传送门回调
void onPortalEnter(MapObject* portal, void* userData) {
    Game* game = (Game*)userData;
    const char* targetMap = getObjectProperty(portal, "targetMap");
    const char* targetX = getObjectProperty(portal, "targetX");
    const char* targetY = getObjectProperty(portal, "targetY");
    
    if (targetMap) {
        printf("传送到: %s\n", targetMap);
        // 实际项目中这里加载新地图
        // loadGameMap(game, targetMap);
        // game->player.x = atoi(targetX);
        // game->player.y = atoi(targetY);
    }
}

// NPC交互
void interactWithNPC(Game* game) {
    // 查找附近的NPC
    for (int i = 0; i < game->currentMap->layerCount; i++) {
        GameLayer* gl = &game->currentMap->layers[i];
        if (gl->type != GLAYER_OBJECT) continue;
        
        ObjectLayer* ol = (ObjectLayer*)gl->data;
        for (int j = 0; j < ol->objectCount; j++) {
            MapObject* obj = &ol->objects[j];
            if (strcmp(obj->type, "npc") != 0) continue;
            
            // 检查距离
            float dx = obj->x - game->player.x;
            float dy = obj->y - game->player.y;
            float dist = sqrt(dx*dx + dy*dy);
            
            if (dist < 50) {  // 50像素内可交互
                const char* dialogue = getObjectProperty(obj, "dialogue");
                if (dialogue) {
                    strncpy(game->currentDialogue, dialogue, 511);
                    game->state = GAME_STATE_DIALOGUE;
                    game->dialogueTimer = 3.0f;  // 显示3秒
                    printf("NPC: %s\n", dialogue);
                }
                return;
            }
        }
    }
}

void handleEvents(Game* game) {
    SDL_Event e;
    while (SDL_PollEvent(&e)) {
        if (e.type == SDL_QUIT) {
            game->running = 0;
        }
        if (e.type == SDL_KEYDOWN) {
            switch (e.key.keysym.sym) {
                case SDLK_ESCAPE:
                    game->running = 0;
                    break;
                case SDLK_e:
                case SDLK_SPACE:
                    // 交互键
                    if (game->state == GAME_STATE_PLAYING) {
                        interactWithNPC(game);
                    }
                    break;
            }
        }
    }
}

void updateGame(Game* game, float deltaTime) {
    if (game->state != GAME_STATE_PLAYING) {
        // 对话状态更新计时器
        if (game->state == GAME_STATE_DIALOGUE) {
            game->dialogueTimer -= deltaTime;
            if (game->dialogueTimer <= 0) {
                game->state = GAME_STATE_PLAYING;
            }
        }
        return;
    }
    
    const Uint8* keys = SDL_GetKeyboardState(NULL);
    
    // 获取碰撞体
    int colliderCount;
    SDL_Rect* colliders = getMapColliders(game->currentMap, &colliderCount);
    
    // 更新角色
    updatePlayer(&game->player, keys, deltaTime, colliders, colliderCount);
    
    // 摄像机跟随
    cameraFollow(&game->camera,
                (int)(game->player.x + game->player.width/2),
                (int)(game->player.y + game->player.height/2),
                &(MapMetadata){.width=game->currentMap->width,
                              .height=game->currentMap->height,
                              .tileWidth=game->currentMap->tileWidth,
                              .tileHeight=game->currentMap->tileHeight});
    
    // 检查触发器（传送门）
    for (int i = 0; i < game->currentMap->layerCount; i++) {
        GameLayer* gl = &game->currentMap->layers[i];
        if (gl->type == GLAYER_OBJECT) {
            checkTriggers((ObjectLayer*)gl->data,
                         game->player.x + game->player.width/2,
                         game->player.y + game->player.height/2,
                         onPortalEnter, game);
        }
    }
}

void renderGame(Game* game) {
    SDL_SetRenderDrawColor(game->renderer, 100, 149, 237, 255);
    SDL_RenderClear(game->renderer);
    
    // 渲染地图
    renderGameMap(game->renderer, game->currentMap, &game->camera);
    
    // 渲染角色
    renderPlayer(game->renderer, &game->player, &game->camera);
    
    // 渲染对话框
    if (game->state == GAME_STATE_DIALOGUE && game->currentDialogue[0]) {
        // 对话框背景
        SDL_Rect box = {50, 450, 700, 120};
        SDL_SetRenderDrawColor(game->renderer, 0, 0, 0, 200);
        SDL_RenderFillRect(game->renderer, &box);
        SDL_SetRenderDrawColor(game->renderer, 255, 255, 255, 255);
        SDL_RenderDrawRect(game->renderer, &box);
        
        // 对话文字（简化，实际应用SDL_ttf渲染文字）
        printf("对话: %s\n", game->currentDialogue);
    }
    
    // 渲染UI（小地图、血条等）
    // ...
    
    SDL_RenderPresent(game->renderer);
}

void runGame(Game* game) {
    Uint32 lastTime = SDL_GetTicks();
    
    while (game->running) {
        Uint32 currentTime = SDL_GetTicks();
        float deltaTime = (currentTime - lastTime) / 1000.0f;
        lastTime = currentTime;
        if (deltaTime > 0.1f) deltaTime = 0.1f;
        
        handleEvents(game);
        updateGame(game, deltaTime);
        renderGame(game);
    }
}

void cleanupGame(Game* game) {
    freePlayer(&game->player);
    if (game->currentMap) freeGameMap(game->currentMap);
    
    SDL_DestroyRenderer(game->renderer);
    SDL_DestroyWindow(game->window);
    IMG_Quit();
    SDL_Quit();
}
```

**主函数：**

```c
// main.c
#include "game.h"

int main() {
    Game game;
    
    if (initGame(&game, "TMJ Map Game", 800, 600) != 0) {
        printf("游戏初始化失败\n");
        return 1;
    }
    
    if (loadGameMap(&game, "assets/level1.tmj") != 0) {
        printf("地图加载失败\n");
        cleanupGame(&game);
        return 1;
    }
    
    printf("游戏启动！\n");
    printf("WASD/方向键移动，E/空格交互，ESC退出\n");
    
    runGame(&game);
    
    cleanupGame(&game);
    return 0;
}
```

**编译命令（Makefile）：**

```makefile
# Makefile
CC = gcc
CFLAGS = -Wall -Wextra -g -O2
LDFLAGS = -lSDL2 -lSDL2_image -lm

SRCS = main.c game.c game_map.c player.c camera.c \
       tileset.c tile_layer.c object_layer.c image_layer.c \
       tile_layer_renderer.c optimized_renderer.c \
       tile_renderer.c texture_manager.c \
       collision.c trigger.c properties.c animation.c \
       map_metadata.c cJSON.c

OBJS = $(SRCS:.c=.o)

TARGET = game

$(TARGET): $(OBJS)
        $(CC) $(CFLAGS) -o $@ $^ $(LDFLAGS)

%.o: %.c
        $(CC) $(CFLAGS) -c $< -o $@

clean:
        rm -f $(OBJS) $(TARGET)

run: $(TARGET)
        ./$(TARGET)

.PHONY: clean run
```

### 总结

- 完整游戏系统包含：状态管理、地图加载、角色控制、碰撞检测、触发器、渲染
- 游戏状态机（PLAYING/DIALOGUE/MENU）控制不同场景的行为
- 地图切换：释放旧地图 → 加载新地图 → 设置出生点
- NPC 交互：检测距离 → 按键触发 → 显示对话
- 传送门：触发器检测 → 读取目标属性 → 切换地图
- 对话系统：显示对话框和文字（需 SDL_ttf 渲染文字）
- 主循环结构：事件处理 → 更新 → 渲染 → 帧率控制
- Makefile 管理多文件编译，便于维护
- 这个系统可以作为 2D RPG 游戏的基础框架
- 后续可扩展：战斗系统、物品系统、任务系统、存档系统等

---

## 课程总结

恭喜您完成了「C语言与SDL2加载TMJ地图」系统教程的学习！通过 22 讲的系统学习，您已经掌握了：

**基础知识**：TMJ 格式、Tiled 编辑器、SDL2 窗口与渲染、cJSON 解析
**数据解析**：地图元数据、Tileset、Tile Layer、Object Layer、Image Layer
**渲染技术**：图块切割、纹理管理、图层渲染、坐标转换、摄像机系统
**性能优化**：视口剔除、批量渲染、离屏渲染
**进阶功能**：动画图块、碰撞检测、触发器、多图层叠加、自定义属性
**实战项目**：完整地图加载器、角色移动、综合游戏系统

**项目文件结构建议：**

```
project/
├── src/
│   ├── main.c
│   ├── game.c / game.h
│   ├── game_map.c / game_map.h
│   ├── player.c / player.h
│   ├── camera.c / camera.h
│   ├── tileset.c / tileset.h
│   ├── tile_layer.c / tile_layer.h
│   ├── object_layer.c / object_layer.h
│   ├── image_layer.c / image_layer.h
│   ├── tile_layer_renderer.c
│   ├── optimized_renderer.c
│   ├── collision.c / collision.h
│   ├── trigger.c / trigger.h
│   ├── properties.c / properties.h
│   ├── animation.c / animation.h
│   └── cJSON.c / cJSON.h
├── assets/
│   ├── maps/
│   │   ├── level1.tmj
│   │   └── level2.tmj
│   ├── tilesets/
│   │   ├── terrain.png
│   │   └── characters.png
│   └── sprites/
│       └── player.png
├── Makefile
└── README.md
```

**持续学习建议：**

1. **动手实践**：用 Tiled 编辑器制作自己的地图，用课程代码加载渲染
2. **扩展功能**：添加等距地图支持、TSX 外部引用、base64 压缩格式
3. **学习 SDL_ttf**：实现文字渲染，完善对话系统
4. **学习 SDL_mixer**：添加背景音乐和音效
5. **研究游戏引擎**：对比专业引擎（如 Godot、Unity）的地图系统设计
6. **参与开源**：参考 LDtk、Tiled 的官方文档和示例代码
7. **性能调优**：学习 SDL2 的 GPU 加速、纹理流式加载
8. **跨平台**：将项目移植到 Windows、Linux、macOS、移动端

**推荐资源：**

- Tiled 官方文档：https://doc.mapeditor.org/
- SDL2 Wiki：https://wiki.libsdl.org/
- cJSON GitHub：https://github.com/DaveGamble/cJSON
- Lazy Foo' SDL Tutorials：https://lazyfoo.net/tutorials/SDL/

掌握 TMJ 地图加载是 2D 游戏开发的重要技能。通过本课程的学习，您已经具备了开发 2D RPG、平台跳跃、策略游戏等类型游戏的基础。祝您在游戏开发的道路上不断进步，创造出精彩的游戏作品！
