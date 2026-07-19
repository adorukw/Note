# C语言+SDL2类塞尔达游戏实战教程

> 一本从零开始、循序渐进的实战教科书，带你用纯C语言和SDL2打造一款类塞尔达动作冒险游戏。

---

## 课程总览

### 学习目标

完成本课程后，你将能够：

1. **掌握SDL2核心API**：窗口、渲染器、纹理、事件、音频等全套2D游戏开发接口
2. **理解游戏架构**：游戏循环、状态机、实体系统、资源管理等通用游戏设计模式
3. **实现类塞尔达核心玩法**：玩家移动、Tile地图、碰撞检测、敌人AI、战斗系统、物品背包
4. **构建完整游戏项目**：从空窗口到可发布的可玩Demo，掌握工程化开发流程
5. **具备C语言工程能力**：内存管理、模块化设计、头文件组织、跨平台编译

### 课程定位

- **前置知识**：C语言基础（指针、结构体、函数指针、动态内存）、基本数据结构
- **难度曲线**：基础（1-7讲）→ 核心（8-21讲）→ 进阶（22-28讲）→ 实战（29-30讲）
- **预计学时**：每讲1-2小时，总计约40-60小时
- **配套项目**：每讲代码可独立编译运行，最终整合为完整游戏

### 详细章节目录

#### 第一章 入门基础（第1-4讲）
- 第1讲：C语言游戏开发概述与环境搭建
- 第2讲：SDL2简介与第一个窗口
- 第3讲：SDL2渲染基础
- 第4讲：事件处理与输入系统

#### 第二章 游戏架构设计（第5-7讲）
- 第5讲：游戏循环的深入设计
- 第6讲：游戏状态机
- 第7讲：代码组织与资源管理

#### 第三章 图形与动画（第8-11讲）
- 第8讲：纹理加载与精灵图
- 第9讲：角色动画系统
- 第10讲：摄像机系统
- 第11讲：图层渲染

#### 第四章 玩家系统（第12-14讲）
- 第12讲：玩家角色结构设计
- 第13讲：输入处理与角色移动
- 第14讲：攻击与动作系统

#### 第五章 地图系统（第15-18讲）
- 第15讲：Tile地图基础
- 第16讲：地图数据加载
- 第17讲：多层地图渲染
- 第18讲：场景切换与传送

#### 第六章 碰撞系统（第19-21讲）
- 第19讲：AABB碰撞检测
- 第20讲：碰撞响应与分离
- 第21讲：碰撞优化策略

#### 第七章 敌人与战斗（第22-25讲）
- 第22讲：敌人基础结构
- 第23讲：简单AI与状态机
- 第24讲：战斗系统与伤害计算
- 第25讲：敌人生成与波次系统

#### 第八章 物品与UI（第26-28讲）
- 第26讲：物品系统设计
- 第27讲：背包与HUD界面
- 第28讲：菜单与对话框系统

#### 第九章 音效与存档（第29-30讲）
- 第29讲：SDL_mixer音效系统
- 第30讲：存档系统与游戏整合

---

## 第一章 入门基础

> 万丈高楼平地起。本章带你搭建开发环境，理解SDL2的本质，并完成第一个能响应键盘的窗口程序。这是后续所有内容的地基。

### 第1讲 C语言游戏开发概述与环境搭建

#### 概念

**SDL（Simple DirectMedia Layer）** 是一套跨平台的多媒体开发库，提供对音频、键盘、鼠标、手柄、显卡（通过OpenGL/Direct3D）的底层访问。它用C语言编写，是开发2D游戏、模拟器、媒体播放器的工业级选择。类塞尔达游戏作为典型的2D动作冒险游戏，其核心需求——窗口管理、图像渲染、输入响应、音频播放——SDL2全部原生支持，因此是本课程的最佳工具。

**类塞尔达游戏**的核心特征包括：俯视角2D视角、Tile瓦片地图、玩家角色实时移动、攻击与战斗、敌人AI、物品收集、场景切换。这些特征决定了我们需要一个能高效处理2D图形和实时输入的框架。

#### 原理

为什么选择C语言+SDL2而不是Python+Pygame或C++ + SFML？

1. **性能可控**：C语言没有GC停顿，内存布局完全由开发者掌控，对于需要稳定60FPS的游戏至关重要
2. **学习底层**：SDL2暴露了大量底层细节（渲染器、纹理格式、像素格式），有助于理解游戏引擎工作原理
3. **依赖极简**：SDL2是动态链接库，部署简单，跨平台一致
4. **生态成熟**：Steam上大量独立游戏（如Stardew Valley的早期版本、FTL）使用SDL2

SDL2的架构采用"渲染器+纹理"模型：`SDL_Window`代表窗口，`SDL_Renderer`负责将`SDL_Texture`绘制到窗口。这种抽象让代码无需关心底层是OpenGL、Direct3D还是软件渲染。

#### 例子

**Windows环境搭建（MinGW + MSYS2）**：

```bash
# 1. 安装MSYS2后，在MSYS2终端执行
pacman -S mingw-w64-x86_64-gcc
pacman -S mingw-w64-x86_64-SDL2
pacman -S mingw-w64-x86_64-SDL2_image
pacman -S mingw-w64-x86_64-SDL2_mixer
pacman -S mingw-w64-x86_64-SDL2_ttf

# 2. 验证安装
sdl2-config --version
```

**Linux环境搭建（Ubuntu/Debian）**：

```bash
sudo apt-get install libsdl2-dev libsdl2-image-dev libsdl2-mixer-dev libsdl2-ttf-dev
```

**macOS环境搭建（Homebrew）**：

```bash
brew install sdl2 sdl2_image sdl2_mixer sdl2_ttf
```

**第一个工程结构**：

```
zelda-clone/
├── src/
│   ├── main.c
│   └── game.c
├── include/
│   └── game.h
├── assets/
│   ├── images/
│   └── sounds/
├── Makefile
└── README.md
```

**Makefile模板**：

```makefile
CC = gcc
CFLAGS = -Wall -Wextra -g -Iinclude $(shell sdl2-config --cflags)
LDFLAGS = $(shell sdl2-config --libs) -lSDL2_image -lSDL2_mixer -lSDL2_ttf

SRCS = $(wildcard src/*.c)
OBJS = $(SRCS:.c=.o)
TARGET = zelda

$(TARGET): $(OBJS)
        $(CC) $(OBJS) -o $(TARGET) $(LDFLAGS)

%.o: %.c
        $(CC) $(CFLAGS) -c $< -o $@

clean:
        rm -f $(OBJS) $(TARGET)

run: $(TARGET)
        ./$(TARGET)
```

#### 总结

- **SDL2是C语言2D游戏开发的事实标准**，跨平台、轻量、性能优秀
- **环境搭建核心**：安装SDL2主库及image/mixer/ttf三个扩展库
- **工程组织**：分离src/include/assets目录，使用Makefile管理编译
- **常见坑**：Windows下需将SDL2.dll复制到可执行文件目录；macOS下注意Framework路径
- **下一步**：第2讲将编写第一个SDL2窗口程序

---

### 第2讲 SDL2简介与第一个窗口

#### 概念

本讲编写第一个SDL2程序：创建一个800×600的窗口，保持显示3秒后退出。这是所有SDL2程序的骨架，掌握它就掌握了SDL2的初始化、窗口创建、事件循环、清理退出四大基础流程。

#### 原理

SDL2程序的生命周期遵循固定模式：

1. **初始化**：调用`SDL_Init(SDL_INIT_VIDEO)`加载SDL子系统
2. **创建窗口**：`SDL_CreateWindow`分配窗口资源
3. **创建渲染器**：`SDL_CreateRenderer`绑定到窗口，负责绘制
4. **事件循环**：通过`SDL_PollEvent`持续处理输入和系统事件
5. **清理**：销毁渲染器、窗口，调用`SDL_Quit`释放资源

SDL2采用引用计数管理资源，所有`SDL_Create*`返回的对象必须用对应的`SDL_Destroy*`释放，否则内存泄漏。这是C语言游戏开发的核心纪律。

#### 例子

```c
#include <SDL2/SDL.h>
#include <stdio.h>

#define WINDOW_WIDTH  800
#define WINDOW_HEIGHT 600

int main(int argc, char* argv[]) {
    (void)argc; (void)argv;

    // 1. 初始化SDL视频子系统
    if (SDL_Init(SDL_INIT_VIDEO) != 0) {
        fprintf(stderr, "SDL_Init failed: %s\n", SDL_GetError());
        return 1;
    }

    // 2. 创建窗口
    SDL_Window* window = SDL_CreateWindow(
        "Zelda Clone - Lesson 2",       // 窗口标题
        SDL_WINDOWPOS_CENTERED,         // x位置（居中）
        SDL_WINDOWPOS_CENTERED,         // y位置（居中）
        WINDOW_WIDTH, WINDOW_HEIGHT,    // 宽高
        SDL_WINDOW_SHOWN                // 显示标志
    );
    if (!window) {
        fprintf(stderr, "SDL_CreateWindow failed: %s\n", SDL_GetError());
        SDL_Quit();
        return 1;
    }

    // 3. 创建渲染器（vsync开启，硬件加速）
    SDL_Renderer* renderer = SDL_CreateRenderer(
        window, -1,
        SDL_RENDERER_ACCELERATED | SDL_RENDERER_PRESENTVSYNC
    );
    if (!renderer) {
        fprintf(stderr, "SDL_CreateRenderer failed: %s\n", SDL_GetError());
        SDL_DestroyWindow(window);
        SDL_Quit();
        return 1;
    }

    // 4. 主循环：保持窗口显示3秒
    Uint32 start_time = SDL_GetTicks();
    int running = 1;
    while (running) {
        SDL_Event event;
        while (SDL_PollEvent(&event)) {
            if (event.type == SDL_QUIT) {
                running = 0;
            }
        }

        // 清屏（深蓝色背景）
        SDL_SetRenderDrawColor(renderer, 30, 40, 80, 255);
        SDL_RenderClear(renderer);

        // 绘制一个红色矩形作为测试
        SDL_Rect rect = { 350, 270, 100, 60 };
        SDL_SetRenderDrawColor(renderer, 220, 60, 60, 255);
        SDL_RenderFillRect(renderer, &rect);

        // 呈现
        SDL_RenderPresent(renderer);

        // 3秒后自动退出
        if (SDL_GetTicks() - start_time > 3000) {
            running = 0;
        }
    }

    // 5. 清理资源（顺序与创建相反）
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

编译运行：

```bash
gcc main.c -o main $(sdl2-config --cflags --libs) && ./main
```

#### 总结

- **SDL2程序四步走**：初始化→创建窗口→事件循环→清理
- **渲染器是核心**：所有绘制操作都通过`SDL_Renderer`完成
- **双缓冲机制**：`SDL_RenderPresent`将后台缓冲区交换到前台，避免画面撕裂
- **资源释放纪律**：每个`Create`必须有对应的`Destroy`，顺序与创建相反
- **错误处理**：所有SDL函数失败时返回NULL或负值，用`SDL_GetError()`获取原因
- **常见坑**：忘记`SDL_Quit`会导致SDL内部状态泄漏；忘记`SDL_RenderPresent`会看到黑屏

---

### 第3讲 SDL2渲染基础

#### 概念

本讲深入SDL2的渲染系统。我们将学习如何加载图片（BMP/PNG）、创建纹理、绘制纹理到屏幕、设置颜色混合模式。纹理（Texture）是SDL2中图像数据的载体，存储在显存中，由GPU加速绘制，是2D游戏渲染的基本单位。

#### 原理

SDL2渲染管线涉及三个核心概念：

1. **Surface（表面）**：CPU内存中的像素数据，可读写但绘制慢。`SDL_LoadBMP`返回Surface
2. **Texture（纹理）**：GPU显存中的像素数据，不可直接读写但绘制极快。由Surface转换而来
3. **Renderer（渲染器）**：将Texture绘制到后台缓冲区的引擎

绘制流程：`加载图片(Surface) → 转换为纹理(Texture) → 渲染器绘制(RenderCopy) → 呈现(Present)`

为什么需要Surface到Texture的转换？因为CPU内存和GPU显存是分离的，直接在CPU内存中绘制无法利用GPU加速。一次性将Surface上传到GPU成为Texture，后续绘制都由GPU完成，性能提升数十倍。

**坐标系统**：SDL2使用屏幕坐标系，原点(0,0)在左上角，x向右增大，y向下增大。这与数学坐标系y轴方向相反，是2D游戏开发的惯例。

#### 例子

```c
#include <SDL2/SDL.h>
#include <SDL2/SDL_image.h>
#include <stdio.h>

#define WINDOW_WIDTH  800
#define WINDOW_HEIGHT 600

// 加载纹理的辅助函数
SDL_Texture* load_texture(SDL_Renderer* renderer, const char* path) {
    SDL_Surface* surface = IMG_Load(path);
    if (!surface) {
        fprintf(stderr, "IMG_Load failed: %s (%s)\n", path, IMG_GetError());
        return NULL;
    }
    // 设置透明色（可选：让品红色透明）
    // SDL_SetColorKey(surface, SDL_TRUE,
    //     SDL_MapRGB(surface->format, 255, 0, 255));

    SDL_Texture* texture = SDL_CreateTextureFromSurface(renderer, surface);
    SDL_FreeSurface(surface);  // Surface转Texture后即可释放
    if (!texture) {
        fprintf(stderr, "CreateTextureFromSurface failed: %s\n", SDL_GetError());
    }
    return texture;
}

int main(int argc, char* argv[]) {
    (void)argc; (void)argv;

    SDL_Init(SDL_INIT_VIDEO | SDL_INIT_EVENTS);
    SDL_Window* window = SDL_CreateWindow("Lesson 3 - Rendering",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED,
        WINDOW_WIDTH, WINDOW_HEIGHT, SDL_WINDOW_SHOWN);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1,
        SDL_RENDERER_ACCELERATED | SDL_RENDERER_PRESENTVSYNC);

    // 初始化SDL_image（支持PNG/JPG）
    int img_flags = IMG_INIT_PNG;
    if (!(IMG_Init(img_flags) & img_flags)) {
        fprintf(stderr, "IMG_Init failed: %s\n", IMG_GetError());
        return 1;
    }

    // 加载纹理（如果没有图片，用代码生成一个）
    SDL_Texture* player_tex = load_texture(renderer, "assets/player.png");
    SDL_Rect player_pos = { 100, 100, 64, 64 };  // x, y, w, h

    int running = 1;
    while (running) {
        SDL_Event event;
        while (SDL_PollEvent(&event)) {
            if (event.type == SDL_QUIT) running = 0;
        }

        // 清屏
        SDL_SetRenderDrawColor(renderer, 20, 25, 40, 255);
        SDL_RenderClear(renderer);

        // 绘制网格背景（演示基本绘制）
        SDL_SetRenderDrawColor(renderer, 40, 50, 70, 255);
        for (int x = 0; x < WINDOW_WIDTH; x += 32) {
            SDL_RenderDrawLine(renderer, x, 0, x, WINDOW_HEIGHT);
        }
        for (int y = 0; y < WINDOW_HEIGHT; y += 32) {
            SDL_RenderDrawLine(renderer, 0, y, WINDOW_WIDTH, y);
        }

        // 绘制玩家纹理
        if (player_tex) {
            SDL_RenderCopy(renderer, player_tex, NULL, &player_pos);
        } else {
            // 没有图片时用矩形代替
            SDL_SetRenderDrawColor(renderer, 100, 200, 100, 255);
            SDL_RenderFillRect(renderer, &player_pos);
        }

        SDL_RenderPresent(renderer);
    }

    // 清理
    if (player_tex) SDL_DestroyTexture(player_tex);
    IMG_Quit();
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

#### 总结

- **Surface vs Texture**：Surface在CPU内存，Texture在GPU显存；游戏运行时只用Texture
- **加载流程**：`IMG_Load → SDL_CreateTextureFromSurface → SDL_FreeSurface`
- **绘制核心**：`SDL_RenderCopy(renderer, texture, &src_rect, &dst_rect)`
  - `src_rect`为NULL表示绘制整个纹理
  - `dst_rect`为NULL表示绘制到整个屏幕
- **基本图元**：`SDL_RenderDrawLine`、`SDL_RenderFillRect`、`SDL_RenderDrawPoint`
- **坐标系统**：原点左上角，y向下，与数学坐标系相反
- **常见坑**：忘记`IMG_Quit`、忘记`SDL_FreeSurface`导致内存泄漏；PNG透明通道需用`IMG_Load`而非`SDL_LoadBMP`

---

### 第4讲 事件处理与输入系统

#### 概念

事件（Event）是SDL2与用户交互的核心机制。本讲学习键盘、鼠标事件的捕获与处理，构建一个可响应方向键移动矩形的程序。这是玩家控制角色移动的基础，也是后续所有交互的根基。

#### 原理

SDL2采用**事件队列**模型：所有输入（键盘、鼠标、窗口关闭等）都被放入一个全局队列，开发者通过`SDL_PollEvent`从队列取出事件并处理。这种设计解耦了输入产生与处理，避免输入丢失。

事件类型主要分三类：

1. **窗口事件**：`SDL_QUIT`（关闭按钮）、`SDL_WINDOWEVENT`（大小改变、失焦）
2. **键盘事件**：`SDL_KEYDOWN`、`SDL_KEYUP`，包含按键码`SDL_Keycode`
3. **鼠标事件**：`SDL_MOUSEMOTION`、`SDL_MOUSEBUTTONDOWN`、`SDL_MOUSEBUTTONUP`

**两种输入处理模式**：

- **事件驱动**：在事件循环中响应`KEYDOWN`/`KEYUP`，适合菜单、对话
- **状态查询**：用`SDL_GetKeyboardState`获取当前按键状态，适合游戏内持续移动

游戏角色移动通常用**状态查询**模式，因为玩家可能持续按住方向键，事件模式会产生大量重复事件。

#### 例子

```c
#include <SDL2/SDL.h>
#include <stdio.h>

#define WINDOW_WIDTH  800
#define WINDOW_HEIGHT 600
#define PLAYER_SPEED  300  // 像素/秒

typedef struct {
    float x, y;
    int w, h;
} Player;

int main(int argc, char* argv[]) {
    (void)argc; (void)argv;

    SDL_Init(SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Lesson 4 - Input",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED,
        WINDOW_WIDTH, WINDOW_HEIGHT, SDL_WINDOW_SHOWN);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1,
        SDL_RENDERER_ACCELERATED | SDL_RENDERER_PRESENTVSYNC);

    Player player = { 400, 300, 40, 40 };

    int running = 1;
    Uint32 last_time = SDL_GetTicks();

    while (running) {
        // 计算帧间隔（秒）
        Uint32 current_time = SDL_GetTicks();
        float delta_time = (current_time - last_time) / 1000.0f;
        last_time = current_time;

        // 1. 处理事件（事件驱动模式）
        SDL_Event event;
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

        // 2. 查询键盘状态（状态查询模式）
        const Uint8* keystate = SDL_GetKeyboardState(NULL);
        float dx = 0, dy = 0;
        if (keystate[SDL_SCANCODE_UP]    || keystate[SDL_SCANCODE_W]) dy -= 1;
        if (keystate[SDL_SCANCODE_DOWN]  || keystate[SDL_SCANCODE_S]) dy += 1;
        if (keystate[SDL_SCANCODE_LEFT]  || keystate[SDL_SCANCODE_A]) dx -= 1;
        if (keystate[SDL_SCANCODE_RIGHT] || keystate[SDL_SCANCODE_D]) dx += 1;

        // 对角线移动归一化（避免对角线速度更快）
        if (dx != 0 && dy != 0) {
            dx *= 0.7071f;  // 1/√2
            dy *= 0.7071f;
        }

        // 3. 更新位置（基于时间，而非帧数）
        player.x += dx * PLAYER_SPEED * delta_time;
        player.y += dy * PLAYER_SPEED * delta_time;

        // 边界限制
        if (player.x < 0) player.x = 0;
        if (player.y < 0) player.y = 0;
        if (player.x + player.w > WINDOW_WIDTH)  player.x = WINDOW_WIDTH - player.w;
        if (player.y + player.h > WINDOW_HEIGHT) player.y = WINDOW_HEIGHT - player.h;

        // 4. 渲染
        SDL_SetRenderDrawColor(renderer, 20, 25, 40, 255);
        SDL_RenderClear(renderer);

        SDL_Rect player_rect = { (int)player.x, (int)player.y, player.w, player.h };
        SDL_SetRenderDrawColor(renderer, 100, 200, 100, 255);
        SDL_RenderFillRect(renderer, &player_rect);

        SDL_RenderPresent(renderer);
    }

    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

#### 总结

- **事件队列模型**：`SDL_PollEvent`取出事件，避免输入丢失
- **两种输入模式**：事件驱动适合菜单，状态查询适合游戏内移动
- **帧率无关移动**：`position += velocity * delta_time`，确保不同帧率下移动速度一致
- **对角线归一化**：dx和dy同时非零时乘以1/√2，避免对角线移动比直线快41%
- **边界限制**：防止角色移出屏幕，这是碰撞检测的雏形
- **常见坑**：用`SDL_GetTicks()`而非帧数计算移动；忘记`delta_time`会导致不同机器速度不同

---

## 第二章 游戏架构设计

> 一个能跑的程序和一个能维护的游戏，差距就在架构。本章学习游戏循环的精确设计、状态机模式、模块化代码组织，让你的项目从"能跑"变成"能扩展"。

### 第5讲 游戏循环的深入设计

#### 概念

**游戏循环（Game Loop）** 是游戏程序的心脏，是一个持续运行的循环，每一帧执行三件事：处理输入、更新游戏状态、渲染画面。一个设计良好的游戏循环必须解决三个问题：帧率无关性、固定时间步长、流畅渲染。本讲深入讲解这三大问题的解决方案。

#### 原理

**问题1：帧率无关性**

如果直接`player.x += 5`每帧，那么60FPS下角色每秒移动300像素，30FPS下只移动150像素——同样的代码在不同机器上速度不同。解决方案是用`delta_time`：

```
player.x += velocity * delta_time
```

**问题2：固定时间步长**

物理模拟（碰撞、运动）需要稳定的步长才能保证一致性。如果delta_time波动大（如0.005到0.05），物理计算会出现穿透、抖动。解决方案是**固定时间步长 + 累加器**：

```
accumulator += frame_time
while (accumulator >= FIXED_DT) {
    update(FIXED_DT);  // 物理更新用固定步长
    accumulator -= FIXED_DT;
}
render();  // 渲染用实际帧时间
```

**问题3：流畅渲染**

如果一帧内多次update，渲染应该插值显示，避免视觉卡顿。但简单实现可省略插值。

**经典游戏循环模式**：

```c
while (running) {
    process_input();
    update(delta_time);
    render();
}
```

进阶版（固定步长）：

```c
const float FIXED_DT = 1.0f / 60.0f;
float accumulator = 0.0f;
Uint32 last_time = SDL_GetTicks();

while (running) {
    Uint32 current_time = SDL_GetTicks();
    float frame_time = (current_time - last_time) / 1000.0f;
    last_time = current_time;

    // 防止暂停后大跳变
    if (frame_time > 0.25f) frame_time = 0.25f;

    process_input();

    accumulator += frame_time;
    while (accumulator >= FIXED_DT) {
        update(FIXED_DT);
        accumulator -= FIXED_DT;
    }

    render(accumulator / FIXED_DT);  // alpha用于插值
}
```

#### 例子

完整的游戏循环封装：

```c
// game.h
#ifndef GAME_H
#define GAME_H

#include <SDL2/SDL.h>

#define WINDOW_WIDTH  800
#define WINDOW_HEIGHT 600
#define FIXED_DT      (1.0f / 60.0f)
#define MAX_FRAME_TIME 0.25f

typedef struct {
    SDL_Window* window;
    SDL_Renderer* renderer;
    int running;
    float accumulator;
    Uint32 last_tick;
    // 游戏数据...
    float player_x, player_y;
} Game;

void game_init(Game* game);
void game_run(Game* game);
void game_shutdown(Game* game);

void game_process_input(Game* game);
void game_update(Game* game, float dt);
void game_render(Game* game, float alpha);

#endif
```

```c
// game.c
#include "game.h"
#include <stdio.h>

void game_init(Game* game) {
    SDL_Init(SDL_INIT_VIDEO);
    game->window = SDL_CreateWindow("Game Loop Demo",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED,
        WINDOW_WIDTH, WINDOW_HEIGHT, SDL_WINDOW_SHOWN);
    game->renderer = SDL_CreateRenderer(game->window, -1,
        SDL_RENDERER_ACCELERATED | SDL_RENDERER_PRESENTVSYNC);
    game->running = 1;
    game->accumulator = 0.0f;
    game->last_tick = SDL_GetTicks();
    game->player_x = 400;
    game->player_y = 300;
}

void game_run(Game* game) {
    while (game->running) {
        Uint32 current_tick = SDL_GetTicks();
        float frame_time = (current_tick - game->last_tick) / 1000.0f;
        game->last_tick = current_tick;

        if (frame_time > MAX_FRAME_TIME) frame_time = MAX_FRAME_TIME;

        game_process_input(game);

        game->accumulator += frame_time;
        while (game->accumulator >= FIXED_DT) {
            game_update(game, FIXED_DT);
            game->accumulator -= FIXED_DT;
        }

        float alpha = game->accumulator / FIXED_DT;
        game_render(game, alpha);
    }
}

void game_process_input(Game* game) {
    SDL_Event event;
    while (SDL_PollEvent(&event)) {
        if (event.type == SDL_QUIT) game->running = 0;
        if (event.type == SDL_KEYDOWN && event.key.keysym.sym == SDLK_ESCAPE)
            game->running = 0;
    }
}

void game_update(Game* game, float dt) {
    const Uint8* keys = SDL_GetKeyboardState(NULL);
    float speed = 200.0f;
    if (keys[SDL_SCANCODE_LEFT])  game->player_x -= speed * dt;
    if (keys[SDL_SCANCODE_RIGHT]) game->player_x += speed * dt;
    if (keys[SDL_SCANCODE_UP])    game->player_y -= speed * dt;
    if (keys[SDL_SCANCODE_DOWN])  game->player_y += speed * dt;
}

void game_render(Game* game, float alpha) {
    (void)alpha;  // 简单实现不做插值
    SDL_SetRenderDrawColor(game->renderer, 20, 25, 40, 255);
    SDL_RenderClear(game->renderer);

    SDL_Rect rect = { (int)game->player_x, (int)game->player_y, 40, 40 };
    SDL_SetRenderDrawColor(game->renderer, 100, 200, 100, 255);
    SDL_RenderFillRect(game->renderer, &rect);

    SDL_RenderPresent(game->renderer);
}

void game_shutdown(Game* game) {
    SDL_DestroyRenderer(game->renderer);
    SDL_DestroyWindow(game->window);
    SDL_Quit();
}

int main(int argc, char* argv[]) {
    (void)argc; (void)argv;
    Game game;
    game_init(&game);
    game_run(&game);
    game_shutdown(&game);
    return 0;
}
```

#### 总结

- **游戏循环三件事**：输入→更新→渲染，每帧重复
- **帧率无关性**：所有移动用`velocity * delta_time`，禁止`position += 5`
- **固定时间步长**：物理更新用固定DT，避免抖动和穿透
- **累加器模式**：`accumulator += frame_time; while (acc >= DT) { update; acc -= DT; }`
- **帧时间上限**：限制最大frame_time防止暂停后大跳变（通常0.25秒）
- **插值渲染**：高级技巧，用alpha在两个物理状态间插值，让画面更流畅
- **常见坑**：vsync开启时delta_time约为16.67ms，关闭时可能波动大；调试时断点会导致frame_time暴涨

---

### 第6讲 游戏状态机

#### 概念

**游戏状态机（Game State Machine）** 是管理游戏不同场景（菜单、游戏中、暂停、结束）的设计模式。每个状态有独立的输入处理、更新逻辑、渲染函数。状态机让代码组织清晰，避免巨型switch-case，是中型以上游戏必备架构。

#### 原理

游戏通常有多个"模式"：主菜单、游戏中、暂停、背包界面、战斗、对话。如果把这些逻辑都塞进一个update函数，代码会变成几百行的if-else地狱。

**状态机模型**：

```
状态接口：
  - on_enter()    进入状态时调用一次
  - on_exit()     离开状态时调用一次
  - process_input()
  - update(dt)
  - render()

状态管理器：
  - current_state  当前状态指针
  - change_state(new_state)  切换状态
```

**状态切换流程**：
1. 调用`current_state->on_exit()`
2. 释放旧状态资源
3. 设置`current_state = new_state`
4. 调用`current_state->on_enter()`
5. 加载新状态资源

**状态栈模式**（进阶）：暂停、背包等"覆盖型"状态用栈管理，新状态压栈，退出时弹栈，底层状态恢复。这比单纯切换更灵活。

#### 例子

```c
// state.h
#ifndef STATE_H
#define STATE_H

#include "game.h"

// 状态接口（函数指针集合）
typedef struct GameState {
    void (*on_enter)(Game* game);
    void (*on_exit)(Game* game);
    void (*process_input)(Game* game);
    void (*update)(Game* game, float dt);
    void (*render)(Game* game);
} GameState;

// 具体状态
extern GameState menu_state;
extern GameState play_state;
extern GameState pause_state;
extern GameState gameover_state;

// 状态管理器
void state_change(Game* game, GameState* new_state);
void state_push(Game* game, GameState* new_state);
void state_pop(Game* game);

#endif
```

```c
// state.c
#include "state.h"
#include <assert.h>

#define STATE_STACK_SIZE 16

static GameState* state_stack[STATE_STACK_SIZE];
static int stack_top = 0;

void state_change(Game* game, GameState* new_state) {
    if (stack_top > 0) {
        state_stack[stack_top - 1]->on_exit(game);
        stack_top--;
    }
    state_stack[stack_top++] = new_state;
    new_state->on_enter(game);
}

void state_push(Game* game, GameState* new_state) {
    assert(stack_top < STATE_STACK_SIZE);
    state_stack[stack_top++] = new_state;
    new_state->on_enter(game);
}

void state_pop(Game* game) {
    if (stack_top > 0) {
        state_stack[stack_top - 1]->on_exit(game);
        stack_top--;
    }
}

// 当前状态的接口转发
void state_process_input(Game* game) {
    if (stack_top > 0) state_stack[stack_top - 1]->process_input(game);
}
void state_update(Game* game, float dt) {
    if (stack_top > 0) state_stack[stack_top - 1]->update(game, dt);
}
void state_render(Game* game) {
    if (stack_top > 0) state_stack[stack_top - 1]->render(game);
}
```

```c
// states.c - 具体状态实现
#include "state.h"
#include <SDL2/SDL_ttf.h>
#include <stdio.h>

// ===== 主菜单状态 =====
static void menu_on_enter(Game* game) { (void)game; printf("Enter Menu\n"); }
static void menu_on_exit(Game* game)  { (void)game; printf("Exit Menu\n"); }
static void menu_process_input(Game* game) {
    SDL_Event event;
    while (SDL_PollEvent(&event)) {
        if (event.type == SDL_QUIT) game->running = 0;
        if (event.type == SDL_KEYDOWN) {
            if (event.key.keysym.sym == SDLK_RETURN) {
                state_change(game, &play_state);
            }
            if (event.key.keysym.sym == SDLK_ESCAPE) game->running = 0;
        }
    }
}
static void menu_update(Game* game, float dt) { (void)game; (void)dt; }
static void menu_render(Game* game) {
    SDL_SetRenderDrawColor(game->renderer, 30, 30, 60, 255);
    SDL_RenderClear(game->renderer);
    // 实际项目用SDL_ttf渲染文字"Press ENTER to Start"
    SDL_Rect title = { 200, 250, 400, 60 };
    SDL_SetRenderDrawColor(game->renderer, 200, 200, 100, 255);
    SDL_RenderFillRect(game->renderer, &title);
    SDL_RenderPresent(game->renderer);
}

GameState menu_state = {
    menu_on_enter, menu_on_exit, menu_process_input, menu_update, menu_render
};

// ===== 游戏中状态 =====
static void play_on_enter(Game* game) { (void)game; printf("Enter Play\n"); }
static void play_on_exit(Game* game)  { (void)game; printf("Exit Play\n"); }
static void play_process_input(Game* game) {
    SDL_Event event;
    while (SDL_PollEvent(&event)) {
        if (event.type == SDL_QUIT) game->running = 0;
        if (event.type == SDL_KEYDOWN) {
            if (event.key.keysym.sym == SDLK_ESCAPE) {
                state_push(game, &pause_state);  // 暂停压栈
            }
            if (event.key.keysym.sym == SDLK_p) {
                state_change(game, &gameover_state);
            }
        }
    }
}
static void play_update(Game* game, float dt) {
    const Uint8* keys = SDL_GetKeyboardState(NULL);
    float speed = 200.0f;
    if (keys[SDL_SCANCODE_LEFT])  game->player_x -= speed * dt;
    if (keys[SDL_SCANCODE_RIGHT]) game->player_x += speed * dt;
    if (keys[SDL_SCANCODE_UP])    game->player_y -= speed * dt;
    if (keys[SDL_SCANCODE_DOWN])  game->player_y += speed * dt;
}
static void play_render(Game* game) {
    SDL_SetRenderDrawColor(game->renderer, 20, 40, 30, 255);
    SDL_RenderClear(game->renderer);
    SDL_Rect rect = { (int)game->player_x, (int)game->player_y, 40, 40 };
    SDL_SetRenderDrawColor(game->renderer, 100, 200, 100, 255);
    SDL_RenderFillRect(game->renderer, &rect);
    SDL_RenderPresent(game->renderer);
}

GameState play_state = {
    play_on_enter, play_on_exit, play_process_input, play_update, play_render
};

// ===== 暂停状态（覆盖型）=====
static void pause_on_enter(Game* game) { (void)game; printf("Enter Pause\n"); }
static void pause_on_exit(Game* game)  { (void)game; printf("Exit Pause\n"); }
static void pause_process_input(Game* game) {
    SDL_Event event;
    while (SDL_PollEvent(&event)) {
        if (event.type == SDL_QUIT) game->running = 0;
        if (event.type == SDL_KEYDOWN) {
            if (event.key.keysym.sym == SDLK_ESCAPE) {
                state_pop(game);  // 弹出暂停，恢复游戏
            }
        }
    }
}
static void pause_update(Game* game, float dt) { (void)game; (void)dt; }
static void pause_render(Game* game) {
    // 先渲染底层游戏画面
    play_render(game);
    // 再覆盖半透明黑色蒙版
    SDL_SetRenderDrawBlendMode(game->renderer, SDL_BLENDMODE_BLEND);
    SDL_SetRenderDrawColor(game->renderer, 0, 0, 0, 150);
    SDL_RenderFillRect(game->renderer, NULL);
    // 渲染"PAUSED"文字
    SDL_Rect pause_text = { 300, 270, 200, 60 };
    SDL_SetRenderDrawColor(game->renderer, 255, 255, 255, 255);
    SDL_RenderFillRect(game->renderer, &pause_text);
    SDL_RenderPresent(game->renderer);
}

GameState pause_state = {
    pause_on_enter, pause_on_exit, pause_process_input, pause_update, pause_render
};

// ===== 游戏结束状态 =====
static void gameover_on_enter(Game* game) { (void)game; }
static void gameover_on_exit(Game* game)  { (void)game; }
static void gameover_process_input(Game* game) {
    SDL_Event event;
    while (SDL_PollEvent(&event)) {
        if (event.type == SDL_QUIT) game->running = 0;
        if (event.type == SDL_KEYDOWN) {
            if (event.key.keysym.sym == SDLK_RETURN) {
                state_change(game, &menu_state);
            }
        }
    }
}
static void gameover_update(Game* game, float dt) { (void)game; (void)dt; }
static void gameover_render(Game* game) {
    SDL_SetRenderDrawColor(game->renderer, 60, 20, 20, 255);
    SDL_RenderClear(game->renderer);
    SDL_Rect text = { 250, 270, 300, 60 };
    SDL_SetRenderDrawColor(game->renderer, 255, 100, 100, 255);
    SDL_RenderFillRect(game->renderer, &text);
    SDL_RenderPresent(game->renderer);
}

GameState gameover_state = {
    gameover_on_enter, gameover_on_exit, gameover_process_input,
    gameover_update, gameover_render
};
```

#### 总结

- **状态机解决"巨型if-else"问题**：每个场景独立模块，互不干扰
- **状态接口五函数**：on_enter、on_exit、process_input、update、render
- **状态栈模式**：暂停、背包等覆盖型状态用push/pop，底层状态保留
- **状态切换纪律**：必须先on_exit旧状态，再on_enter新状态，资源不串台
- **常见坑**：状态切换时忘记释放旧状态资源导致内存泄漏；暂停状态忘记渲染底层画面会黑屏

---

### 第7讲 代码组织与资源管理

#### 概念

随着代码量增长，单文件会变成噩梦。本讲学习C语言游戏项目的模块化组织：头文件设计、资源管理器、内存池、错误处理。一个良好组织的项目能让后续添加功能（敌人、物品、地图）事半功倍。

#### 原理

**模块化原则**：

1. **单一职责**：每个`.c`文件只负责一个模块（player、enemy、map、input）
2. **头文件接口**：`.h`只暴露外部需要的类型和函数，内部细节用`static`隐藏
3. **前向声明**：减少头文件循环依赖
4. **包含守卫**：`#ifndef X_H / #define X_H / #endif`防止重复包含

**资源管理器模式**：

游戏需要加载大量纹理、音效、字体。直接在每个模块加载会导致重复加载和难以释放。解决方案是统一的资源管理器：

```c
// 加载时检查缓存，已加载则返回指针，否则加载并缓存
SDL_Texture* resources_get_texture(const char* path);
// 游戏结束时统一释放所有资源
void resources_cleanup(void);
```

**内存池模式**：

频繁malloc/free会导致内存碎片和性能下降。对于子弹、粒子等大量短生命周期对象，预分配一个池，用空闲链表管理：

```c
typedef struct {
    Bullet pool[MAX_BULLETS];
    int free_list[MAX_BULLETS];
    int free_count;
} BulletPool;
```

**错误处理**：

C语言没有异常，游戏开发常用"初始化返回bool"模式：

```c
int game_init(Game* game);  // 返回0成功，非0失败
```

#### 例子

**项目结构**：

```
src/
├── main.c              // 入口
├── game.c/h            // 游戏主结构
├── state.c/h           // 状态机
├── states/
│   ├── menu.c
│   ├── play.c
│   └── pause.c
├── entities/
│   ├── player.c/h
│   ├── enemy.c/h
│   └── bullet.c/h
├── systems/
│   ├── resources.c/h   // 资源管理器
│   ├── input.c/h       // 输入系统
│   └── collision.c/h   // 碰撞系统
└── utils/
    ├── math.c/h        // 向量、矩阵
    └── log.c/h         // 日志
```

**资源管理器实现**：

```c
// resources.h
#ifndef RESOURCES_H
#define RESOURCES_H

#include <SDL2/SDL.h>

#define MAX_TEXTURES 64

typedef struct {
    char path[256];
    SDL_Texture* texture;
} TextureEntry;

void resources_init(SDL_Renderer* renderer);
SDL_Texture* resources_get_texture(const char* path);
void resources_cleanup(void);

#endif
```

```c
// resources.c
#include "resources.h"
#include <SDL2/SDL_image.h>
#include <string.h>
#include <stdio.h>

static SDL_Renderer* g_renderer = NULL;
static TextureEntry g_textures[MAX_TEXTURES];
static int g_texture_count = 0;

void resources_init(SDL_Renderer* renderer) {
    g_renderer = renderer;
    g_texture_count = 0;
    memset(g_textures, 0, sizeof(g_textures));
}

SDL_Texture* resources_get_texture(const char* path) {
    // 1. 查缓存
    for (int i = 0; i < g_texture_count; i++) {
        if (strcmp(g_textures[i].path, path) == 0) {
            return g_textures[i].texture;
        }
    }
    // 2. 未命中，加载
    if (g_texture_count >= MAX_TEXTURES) {
        fprintf(stderr, "Texture cache full!\n");
        return NULL;
    }
    SDL_Surface* surface = IMG_Load(path);
    if (!surface) {
        fprintf(stderr, "Failed to load %s: %s\n", path, IMG_GetError());
        return NULL;
    }
    SDL_Texture* tex = SDL_CreateTextureFromSurface(g_renderer, surface);
    SDL_FreeSurface(surface);
    if (!tex) return NULL;

    strncpy(g_textures[g_texture_count].path, path, 255);
    g_textures[g_texture_count].texture = tex;
    g_texture_count++;
    return tex;
}

void resources_cleanup(void) {
    for (int i = 0; i < g_texture_count; i++) {
        if (g_textures[i].texture) {
            SDL_DestroyTexture(g_textures[i].texture);
            g_textures[i].texture = NULL;
        }
    }
    g_texture_count = 0;
}
```

**子弹对象池**：

```c
// bullet_pool.h
#ifndef BULLET_POOL_H
#define BULLET_POOL_H

#include <SDL2/SDL.h>

#define MAX_BULLETS 256

typedef struct {
    float x, y;
    float vx, vy;
    int active;  // 0=空闲, 1=使用中
} Bullet;

typedef struct {
    Bullet bullets[MAX_BULLETS];
} BulletPool;

void bullet_pool_init(BulletPool* pool);
Bullet* bullet_pool_spawn(BulletPool* pool);  // 从池中取一个
void bullet_pool_update(BulletPool* pool, float dt);
void bullet_pool_render(BulletPool* pool, SDL_Renderer* r);

#endif
```

```c
// bullet_pool.c
#include "bullet_pool.h"
#include <string.h>

void bullet_pool_init(BulletPool* pool) {
    memset(pool->bullets, 0, sizeof(pool->bullets));
}

Bullet* bullet_pool_spawn(BulletPool* pool) {
    for (int i = 0; i < MAX_BULLETS; i++) {
        if (!pool->bullets[i].active) {
            pool->bullets[i].active = 1;
            return &pool->bullets[i];
        }
    }
    return NULL;  // 池满
}

void bullet_pool_update(BulletPool* pool, float dt) {
    for (int i = 0; i < MAX_BULLETS; i++) {
        if (pool->bullets[i].active) {
            pool->bullets[i].x += pool->bullets[i].vx * dt;
            pool->bullets[i].y += pool->bullets[i].vy * dt;
            // 简单出界回收
            if (pool->bullets[i].x < 0 || pool->bullets[i].x > 800 ||
                pool->bullets[i].y < 0 || pool->bullets[i].y > 600) {
                pool->bullets[i].active = 0;
            }
        }
    }
}

void bullet_pool_render(BulletPool* pool, SDL_Renderer* r) {
    SDL_SetRenderDrawColor(r, 255, 255, 100, 255);
    for (int i = 0; i < MAX_BULLETS; i++) {
        if (pool->bullets[i].active) {
            SDL_Rect rect = { (int)pool->bullets[i].x, (int)pool->bullets[i].y, 8, 8 };
            SDL_RenderFillRect(r, &rect);
        }
    }
}
```

#### 总结

- **模块化四原则**：单一职责、头文件接口、前向声明、包含守卫
- **资源管理器**：统一缓存纹理/音效，避免重复加载，统一释放
- **对象池模式**：预分配大量同类对象（子弹、粒子），用active标志管理，避免频繁malloc
- **错误处理**：C语言用返回值表示成功失败，初始化函数返回int或bool
- **目录组织**：按功能分目录（entities、systems、states、utils），文件名小写下划线
- **常见坑**：头文件循环依赖（A.h包含B.h，B.h包含A.h）——用前向声明解决；忘记`static`导致符号冲突

---

## 第三章 图形与动画

> 静态画面只是开始，动画才是游戏的灵魂。本章学习精灵图、帧动画、摄像机、图层渲染，让你的游戏画面活起来。

### 第8讲 纹理加载与精灵图

#### 概念

**精灵图（Sprite Sheet）** 是将多个小图拼接成一张大图的技术。比如角色4方向×4帧的行走动画共16帧，单独存16个文件加载慢、切换慢；拼成一张4×4的精灵图，只需加载一次，通过指定源矩形（srcRect）选择显示哪一帧。这是2D游戏的工业标准。

#### 原理

**为什么用精灵图？**

1. **加载效率**：1次文件IO代替16次，启动更快
2. **显存效率**：GPU纹理切换有开销，单纹理绘制多帧最优
3. **批处理**：现代GPU单次draw call可绘制大量精灵，纹理切换是瓶颈
4. **打包工具**：TexturePacker等工具自动打包，支持旋转、裁剪

**SDL2绘制原理**：

```c
SDL_RenderCopy(renderer, texture, &src_rect, &dst_rect);
```

- `src_rect`：源矩形，指定从纹理的哪个区域取图（精灵图中的一帧）
- `dst_rect`：目标矩形，指定绘制到屏幕的位置和大小（可缩放）

**精灵图布局**：

假设角色精灵图是4×4网格，每帧32×32像素，总尺寸128×128：

```
行0: 向下走 4帧
行1: 向左走 4帧
行2: 向右走 4帧
行3: 向上走 4帧
```

第row行第col帧的src_rect为：
```c
SDL_Rect src = { col * 32, row * 32, 32, 32 };
```

#### 例子

```c
// sprite.h
#ifndef SPRITE_H
#define SPRITE_H

#include <SDL2/SDL.h>

typedef struct {
    SDL_Texture* texture;
    int frame_width;
    int frame_height;
    int cols;  // 每行帧数
    int rows;  // 总行数
} SpriteSheet;

int sprite_sheet_load(SpriteSheet* ss, SDL_Renderer* r,
                      const char* path, int fw, int fh);
void sprite_sheet_free(SpriteSheet* ss);

// 绘制指定行列的帧到目标位置
void sprite_sheet_draw(SpriteSheet* ss, SDL_Renderer* r,
                       int col, int row,
                       int dst_x, int dst_y,
                       int dst_w, int dst_h);

// 翻转绘制（用于左右镜像）
void sprite_sheet_draw_ex(SpriteSheet* ss, SDL_Renderer* r,
                          int col, int row,
                          int dst_x, int dst_y,
                          int dst_w, int dst_h,
                          SDL_RendererFlip flip);

#endif
```

```c
// sprite.c
#include "sprite.h"
#include <SDL2/SDL_image.h>
#include <stdio.h>

int sprite_sheet_load(SpriteSheet* ss, SDL_Renderer* r,
                      const char* path, int fw, int fh) {
    SDL_Surface* surface = IMG_Load(path);
    if (!surface) {
        fprintf(stderr, "Failed to load %s: %s\n", path, IMG_GetError());
        return -1;
    }
    ss->texture = SDL_CreateTextureFromSurface(r, surface);
    SDL_FreeSurface(surface);
    if (!ss->texture) return -1;

    ss->frame_width = fw;
    ss->frame_height = fh;
    ss->cols = surface->w / fw;  // 注意：surface已释放，这里假设已知
    // 实际应先存surface->w再释放
    ss->rows = 4;  // 假设4行
    return 0;
}

void sprite_sheet_free(SpriteSheet* ss) {
    if (ss->texture) {
        SDL_DestroyTexture(ss->texture);
        ss->texture = NULL;
    }
}

void sprite_sheet_draw(SpriteSheet* ss, SDL_Renderer* r,
                       int col, int row,
                       int dst_x, int dst_y,
                       int dst_w, int dst_h) {
    SDL_Rect src = {
        col * ss->frame_width,
        row * ss->frame_height,
        ss->frame_width,
        ss->frame_height
    };
    SDL_Rect dst = { dst_x, dst_y, dst_w, dst_h };
    SDL_RenderCopy(r, ss->texture, &src, &dst);
}

void sprite_sheet_draw_ex(SpriteSheet* ss, SDL_Renderer* r,
                          int col, int row,
                          int dst_x, int dst_y,
                          int dst_w, int dst_h,
                          SDL_RendererFlip flip) {
    SDL_Rect src = {
        col * ss->frame_width,
        row * ss->frame_height,
        ss->frame_width,
        ss->frame_height
    };
    SDL_Rect dst = { dst_x, dst_y, dst_w, dst_h };
    SDL_RenderCopyEx(r, ss->texture, &src, &dst, 0, NULL, flip);
}
```

**使用示例**：

```c
SpriteSheet player_ss;
sprite_sheet_load(&player_ss, renderer, "assets/player_sheet.png", 32, 32);

// 绘制向下走的第0帧
sprite_sheet_draw(&player_ss, renderer, 0, 0, 100, 100, 64, 64);

// 绘制向右走的第2帧（放大2倍）
sprite_sheet_draw(&player_ss, renderer, 2, 2, 200, 100, 64, 64);

// 水平翻转（用向左帧显示向右）
sprite_sheet_draw_ex(&player_ss, renderer, 0, 1, 300, 100, 64, 64,
                     SDL_FLIP_HORIZONTAL);
```

#### 总结

- **精灵图核心思想**：一张大图存多帧，通过src_rect选择显示区域
- **RenderCopy三参数**：texture、src_rect（源）、dst_rect（目标，可缩放）
- **RenderCopyEx**：支持旋转、翻转，用于镜像动画
- **行列计算**：`src.x = col * frame_w; src.y = row * frame_h;`
- **打包工具**：TexturePacker、ShoeBox可自动生成精灵图
- **常见坑**：src_rect超出纹理边界会绘制异常；忘记释放纹理导致显存泄漏

---

### 第9讲 角色动画系统

#### 概念

**帧动画（Frame Animation）** 是通过快速切换精灵图的不同帧产生运动错觉的技术。本讲实现一个完整的角色动画系统：4方向行走动画、空闲动画、动画状态切换。这是类塞尔达游戏玩家和敌人动画的基础。

#### 原理

**动画三要素**：

1. **帧序列**：一组精灵帧的有序集合
2. **帧率（FPS）**：每秒切换多少帧，通常8-12 FPS用于行走
3. **循环模式**：循环（行走）或单次（攻击）

**动画状态机**：

角色通常有多个动画状态：idle（空闲）、walk（行走）、attack（攻击）、hurt（受伤）、die（死亡）。状态间有转换规则：

```
idle --按键移动--> walk
walk --松开键--> idle
any --按攻击键--> attack
attack --结束--> idle
any --受伤--> hurt
hurt --结束--> idle
any --血量为0--> die
```

**帧切换原理**：

```c
// 每帧累加时间
anim->timer += delta_time;
// 达到帧间隔则切换到下一帧
if (anim->timer >= FRAME_DURATION) {
    anim->timer = 0;
    anim->current_frame++;
    if (anim->current_frame >= anim->frame_count) {
        if (anim->loop) anim->current_frame = 0;
        else anim->current_frame = anim->frame_count - 1;  // 停在最后一帧
    }
}
```

#### 例子

```c
// animation.h
#ifndef ANIMATION_H
#define ANIMATION_H

#include "sprite.h"

typedef enum {
    DIR_DOWN = 0,
    DIR_LEFT = 1,
    DIR_RIGHT = 2,
    DIR_UP = 3
} Direction;

typedef enum {
    ANIM_IDLE = 0,
    ANIM_WALK = 1,
    ANIM_ATTACK = 2,
    ANIM_HURT = 3,
    ANIM_DIE = 4,
    ANIM_COUNT
} AnimState;

typedef struct {
    int row;          // 精灵图中对应的行
    int frame_count;  // 帧数
    float frame_duration;  // 每帧持续时间（秒）
    int loop;         // 是否循环
} Animation;

typedef struct {
    SpriteSheet* sheet;
    Animation animations[ANIM_COUNT];
    AnimState current_state;
    Direction current_dir;
    int current_frame;
    float timer;
    int finished;  // 非循环动画是否播放完毕
} Animator;

void animator_init(Animator* a, SpriteSheet* sheet);
void animator_set_state(Animator* a, AnimState state, Direction dir);
void animator_update(Animator* a, float dt);
void animator_draw(Animator* a, SDL_Renderer* r, int x, int y, int w, int h);

#endif
```

```c
// animation.c
#include "animation.h"
#include <string.h>

void animator_init(Animator* a, SpriteSheet* sheet) {
    memset(a, 0, sizeof(*a));
    a->sheet = sheet;

    // 配置各动画（假设精灵图布局：每行8帧）
    // 行0-3: idle 4方向各1帧
    // 行4-7: walk 4方向各4帧
    // 行8-11: attack 4方向各3帧
    a->animations[ANIM_IDLE].row = 0;
    a->animations[ANIM_IDLE].frame_count = 1;
    a->animations[ANIM_IDLE].frame_duration = 0.1f;
    a->animations[ANIM_IDLE].loop = 1;

    a->animations[ANIM_WALK].row = 4;
    a->animations[ANIM_WALK].frame_count = 4;
    a->animations[ANIM_WALK].frame_duration = 0.12f;
    a->animations[ANIM_WALK].loop = 1;

    a->animations[ANIM_ATTACK].row = 8;
    a->animations[ANIM_ATTACK].frame_count = 3;
    a->animations[ANIM_ATTACK].frame_duration = 0.08f;
    a->animations[ANIM_ATTACK].loop = 0;

    a->current_state = ANIM_IDLE;
    a->current_dir = DIR_DOWN;
    a->current_frame = 0;
    a->timer = 0;
    a->finished = 0;
}

void animator_set_state(Animator* a, AnimState state, Direction dir) {
    if (a->current_state != state || a->current_dir != dir) {
        a->current_state = state;
        a->current_dir = dir;
        a->current_frame = 0;
        a->timer = 0;
        a->finished = 0;
    }
}

void animator_update(Animator* a, float dt) {
    Animation* anim = &a->animations[a->current_state];
    if (a->finished) return;

    a->timer += dt;
    if (a->timer >= anim->frame_duration) {
        a->timer -= anim->frame_duration;
        a->current_frame++;
        if (a->current_frame >= anim->frame_count) {
            if (anim->loop) {
                a->current_frame = 0;
            } else {
                a->current_frame = anim->frame_count - 1;
                a->finished = 1;
            }
        }
    }
}

void animator_draw(Animator* a, SDL_Renderer* r, int x, int y, int w, int h) {
    Animation* anim = &a->animations[a->current_state];
    // 行 = 动画基础行 + 方向偏移
    int row = anim->row + a->current_dir;
    int col = a->current_frame;
    sprite_sheet_draw(a->sheet, r, col, row, x, y, w, h);
}
```

**使用示例**：

```c
// 在玩家更新中
void player_update(Player* p, float dt) {
    float dx = 0, dy = 0;
    const Uint8* keys = SDL_GetKeyboardState(NULL);
    if (keys[SDL_SCANCODE_LEFT])  { dx = -1; p->dir = DIR_LEFT; }
    if (keys[SDL_SCANCODE_RIGHT]) { dx = 1;  p->dir = DIR_RIGHT; }
    if (keys[SDL_SCANCODE_UP])    { dy = -1; p->dir = DIR_UP; }
    if (keys[SDL_SCANCODE_DOWN])  { dy = 1;  p->dir = DIR_DOWN; }

    if (p->attacking) {
        animator_set_state(&p->animator, ANIM_ATTACK, p->dir);
    } else if (dx != 0 || dy != 0) {
        animator_set_state(&p->animator, ANIM_WALK, p->dir);
        p->x += dx * p->speed * dt;
        p->y += dy * p->speed * dt;
    } else {
        animator_set_state(&p->animator, ANIM_IDLE, p->dir);
    }

    animator_update(&p->animator, dt);

    // 攻击动画结束
    if (p->attacking && p->animator.finished) {
        p->attacking = 0;
    }
}

void player_draw(Player* p, SDL_Renderer* r) {
    animator_draw(&p->animator, r, (int)p->x, (int)p->y, 64, 64);
}
```

#### 总结

- **动画三要素**：帧序列、帧率、循环模式
- **动画状态机**：idle/walk/attack/hurt/die，状态间有转换规则
- **帧切换**：累加delta_time，达到frame_duration则切换
- **方向处理**：精灵图每行对应一个方向，或用翻转减少资源
- **非循环动画**：attack/die需检测finished标志
- **常见坑**：动画状态切换时忘记重置current_frame和timer；攻击动画未结束就切换状态导致动作中断

---

### 第10讲 摄像机系统

#### 概念

**摄像机（Camera）** 是游戏世界的观察窗口。地图可能比屏幕大很多（如2000×2000像素），但屏幕只有800×600。摄像机决定显示地图的哪个区域，并跟随玩家移动。这是开放世界、大地图游戏的基础。

#### 原理

**世界坐标 vs 屏幕坐标**：

- **世界坐标**：物体在游戏世界中的绝对位置，如玩家在(1500, 800)
- **屏幕坐标**：物体在屏幕上的绘制位置，如玩家显示在(400, 300)

转换公式：
```
screen_x = world_x - camera_x
screen_y = world_y - camera_y
```

**摄像机跟随算法**：

1. **直接跟随**：摄像机中心 = 玩家中心，简单但抖动
2. **平滑跟随（Lerp）**：摄像机逐渐接近玩家位置，避免抖动
   ```c
   camera.x += (target_x - camera.x) * 0.1f;
   ```
3. **死区跟随**：玩家在屏幕中心一定区域内移动时摄像机不动，超出死区才跟随
4. **前瞻跟随**：根据玩家速度提前移动摄像机，让玩家看到前方

**边界限制**：

摄像机不能移出地图边界：
```c
if (camera.x < 0) camera.x = 0;
if (camera.x + SCREEN_W > MAP_W) camera.x = MAP_W - SCREEN_W;
```

#### 例子

```c
// camera.h
#ifndef CAMERA_H
#define CAMERA_H

#include <SDL2/SDL.h>

typedef struct {
    float x, y;           // 摄像机左上角世界坐标
    int view_width;       // 视口宽（屏幕宽）
    int view_height;      // 视口高（屏幕高）
    int world_width;      // 世界宽（地图宽）
    int world_height;     // 世界高
    float follow_speed;   // 跟随速度（0-1，越大越快）
    int deadzone_x;       // 死区半宽
    int deadzone_y;       // 死区半高
} Camera;

void camera_init(Camera* c, int vw, int vh, int ww, int wh);
void camera_follow(Camera* c, float target_x, float target_y, float dt);
void camera_clamp(Camera* c);
void camera_world_to_screen(Camera* c, float wx, float wy, int* sx, int* sy);
int camera_is_visible(Camera* c, float wx, float wy, int w, int h);

#endif
```

```c
// camera.c
#include "camera.h"

void camera_init(Camera* c, int vw, int vh, int ww, int wh) {
    c->x = 0;
    c->y = 0;
    c->view_width = vw;
    c->view_height = vh;
    c->world_width = ww;
    c->world_height = wh;
    c->follow_speed = 5.0f;  // 每秒接近5倍距离
    c->deadzone_x = vw / 4;  // 死区为屏幕1/4
    c->deadzone_y = vh / 4;
}

void camera_follow(Camera* c, float target_x, float target_y, float dt) {
    // 目标位置：让target在屏幕中心
    float desired_x = target_x - c->view_width / 2.0f;
    float desired_y = target_y - c->view_height / 2.0f;

    // 死区检测：如果target在死区内，不移动
    float center_x = c->x + c->view_width / 2.0f;
    float center_y = c->y + c->view_height / 2.0f;
    if (fabsf(target_x - center_x) < c->deadzone_x &&
        fabsf(target_y - center_y) < c->deadzone_y) {
        return;  // 在死区内，不动
    }

    // 平滑跟随（指数衰减）
    float t = 1.0f - expf(-c->follow_speed * dt);
    c->x += (desired_x - c->x) * t;
    c->y += (desired_y - c->y) * t;

    camera_clamp(c);
}

void camera_clamp(Camera* c) {
    if (c->x < 0) c->x = 0;
    if (c->y < 0) c->y = 0;
    if (c->x + c->view_width > c->world_width)
        c->x = c->world_width - c->view_width;
    if (c->y + c->view_height > c->world_height)
        c->y = c->world_height - c->view_height;

    // 如果地图比屏幕小，居中
    if (c->world_width < c->view_width)
        c->x = (c->world_width - c->view_width) / 2.0f;
    if (c->world_height < c->view_height)
        c->y = (c->world_height - c->view_height) / 2.0f;
}

void camera_world_to_screen(Camera* c, float wx, float wy, int* sx, int* sy) {
    *sx = (int)(wx - c->x);
    *sy = (int)(wy - c->y);
}

int camera_is_visible(Camera* c, float wx, float wy, int w, int h) {
    // 视口范围 [camera.x, camera.x + view_width]
    // 物体范围 [wx, wx + w]
    // 相交则可见
    return (wx + w > c->x && wx < c->x + c->view_width &&
            wy + h > c->y && wy < c->y + c->view_height);
}
```

**使用示例**：

```c
Camera camera;
camera_init(&camera, 800, 600, 2000, 1500);  // 800x600屏幕，2000x1500世界

// 在update中
camera_follow(&camera, player.x, player.y, dt);

// 在render中
int screen_x, screen_y;
camera_world_to_screen(&camera, player.x, player.y, &screen_x, &screen_y);
sprite_sheet_draw(&player_ss, renderer, frame, row, screen_x, screen_y, 64, 64);

// 绘制地图时只绘制可见的tile
for (int ty = 0; ty < map_rows; ty++) {
    for (int tx = 0; tx < map_cols; tx++) {
        float world_x = tx * TILE_SIZE;
        float world_y = ty * TILE_SIZE;
        if (!camera_is_visible(&camera, world_x, world_y, TILE_SIZE, TILE_SIZE))
            continue;
        camera_world_to_screen(&camera, world_x, world_y, &screen_x, &screen_y);
        // 绘制tile...
    }
}
```

#### 总结

- **世界坐标 vs 屏幕坐标**：`screen = world - camera`
- **摄像机跟随**：直接跟随抖动，Lerp平滑，死区减少抖动
- **平滑公式**：`x += (target - x) * (1 - exp(-speed * dt))`，帧率无关
- **边界限制**：摄像机不能移出地图，地图比屏幕小时居中
- **视锥剔除**：只绘制摄像机可见的物体，大幅提升性能
- **常见坑**：忘记clamp导致摄像机移出地图显示黑边；UI不应受摄像机影响，应直接用屏幕坐标

---

### 第11讲 图层渲染

#### 概念

**图层（Layer）** 是将游戏元素按深度分层渲染的技术。典型分层：背景层（天空、远景）、地图层（地面瓦片）、实体层（玩家、敌人、NPC）、特效层（粒子、伤害数字）、UI层（HUD、菜单）。图层让画面有层次感，也方便控制渲染顺序和滚动速度。

#### 原理

**渲染顺序决定Z序**：先绘制的在底层，后绘制的在顶层。所以图层按从底到顶顺序渲染：

```
1. 背景层（视差滚动，慢速）
2. 地面层（Tile地图）
3. 实体层（按y坐标排序，y大的后绘制——遮挡关系）
4. 高层Tile（树冠、屋顶，玩家可走到下面）
5. 特效层（粒子、伤害数字）
6. UI层（HUD，不受摄像机影响）
```

**视差滚动（Parallax）**：

背景层以低于摄像机的速度滚动，产生远近层次感：
```c
bg_x = camera.x * 0.5f;  // 背景只移动摄像机一半距离
```

**Y轴排序**：

实体层中，y坐标大的（屏幕下方）应该遮挡y坐标小的（屏幕上方）。所以渲染前按y排序：

```c
qsort(entities, count, sizeof(Entity), compare_by_y);
// compare: return a.y - b.y;
```

#### 例子

```c
// layer.h
#ifndef LAYER_H
#define LAYER_H

#include "camera.h"
#include "sprite.h"

typedef enum {
    LAYER_BACKGROUND = 0,
    LAYER_GROUND,
    LAYER_ENTITIES,
    LAYER_OVERHEAD,    // 高层Tile（树冠等）
    LAYER_EFFECTS,
    LAYER_UI,
    LAYER_COUNT
} RenderLayer;

typedef struct {
    int layer;
    float world_x, world_y;
    int width, height;
    SDL_Texture* texture;
    SDL_Rect src_rect;
    int parallax_x, parallax_y;  // 视差因子（百分比，100=正常，50=半速）
    int screen_space;  // 1=UI层，不受摄像机影响
} RenderCommand;

#define MAX_RENDER_COMMANDS 1024

typedef struct {
    RenderCommand commands[MAX_RENDER_COMMANDS];
    int count;
} RenderQueue;

void render_queue_init(RenderQueue* q);
void render_queue_add(RenderQueue* q, RenderCommand* cmd);
void render_queue_sort(RenderQueue* q);  // 按layer和y排序
void render_queue_flush(RenderQueue* q, SDL_Renderer* r, Camera* cam);

#endif
```

```c
// layer.c
#include "layer.h"
#include <stdlib.h>
#include <string.h>

void render_queue_init(RenderQueue* q) {
    q->count = 0;
}

void render_queue_add(RenderQueue* q, RenderCommand* cmd) {
    if (q->count >= MAX_RENDER_COMMANDS) return;
    q->commands[q->count++] = *cmd;
}

static int compare_commands(const void* a, const void* b) {
    const RenderCommand* ca = a;
    const RenderCommand* cb = b;
    // 先按layer排序
    if (ca->layer != cb->layer) return ca->layer - cb->layer;
    // 同layer按y排序（y大的后绘制）
    if (ca->world_y != cb->world_y) {
        return (ca->world_y > cb->world_y) ? 1 : -1;
    }
    return 0;
}

void render_queue_sort(RenderQueue* q) {
    qsort(q->commands, q->count, sizeof(RenderCommand), compare_commands);
}

void render_queue_flush(RenderQueue* q, SDL_Renderer* r, Camera* cam) {
    render_queue_sort(q);

    for (int i = 0; i < q->count; i++) {
        RenderCommand* cmd = &q->commands[i];
        int sx, sy;

        if (cmd->screen_space) {
            sx = (int)cmd->world_x;
            sy = (int)cmd->world_y;
        } else {
            // 视差滚动
            float cam_x = cam->x * (cmd->parallax_x / 100.0f);
            float cam_y = cam->y * (cmd->parallax_y / 100.0f);
            sx = (int)(cmd->world_x - cam_x);
            sy = (int)(cmd->world_y - cam_y);
        }

        SDL_Rect dst = { sx, sy, cmd->width, cmd->height };
        SDL_RenderCopy(r, cmd->texture, &cmd->src_rect, &dst);
    }
    q->count = 0;
}
```

**使用示例**：

```c
RenderQueue queue;
render_queue_init(&queue);

// 添加背景（视差50%）
RenderCommand bg = {
    .layer = LAYER_BACKGROUND,
    .world_x = 0, .world_y = 0,
    .width = 1600, .height = 1200,
    .texture = bg_texture,
    .src_rect = {0, 0, 1600, 1200},
    .parallax_x = 50, .parallax_y = 50,
    .screen_space = 0
};
render_queue_add(&queue, &bg);

// 添加玩家
RenderCommand player_cmd = {
    .layer = LAYER_ENTITIES,
    .world_x = player.x, .world_y = player.y,
    .width = 64, .height = 64,
    .texture = player_ss.texture,
    .src_rect = {0, 0, 32, 32},
    .parallax_x = 100, .parallax_y = 100,
    .screen_space = 0
};
render_queue_add(&queue, &player_cmd);

// 添加敌人（同layer，按y自动排序）
// ...

// 添加UI（屏幕空间）
RenderCommand hud = {
    .layer = LAYER_UI,
    .world_x = 10, .world_y = 10,
    .width = 200, .height = 40,
    .texture = hud_texture,
    .src_rect = {0, 0, 200, 40},
    .parallax_x = 0, .parallax_y = 0,
    .screen_space = 1
};
render_queue_add(&queue, &hud);

// 渲染
SDL_RenderClear(renderer);
render_queue_flush(&queue, renderer, &camera);
SDL_RenderPresent(renderer);
```

#### 总结

- **图层顺序**：背景→地面→实体→高层Tile→特效→UI
- **Y轴排序**：同层实体按y排序，y大的后绘制（遮挡关系）
- **视差滚动**：背景以低于100%的速度滚动，产生远近感
- **屏幕空间**：UI层不受摄像机影响，直接用屏幕坐标
- **渲染队列**：收集所有绘制命令，统一排序后批量绘制
- **常见坑**：UI忘记设screen_space导致随摄像机移动；树冠层和地面层顺序颠倒导致玩家走到树下被遮挡

---

## 第四章 玩家系统

> 玩家是游戏的灵魂。本章从玩家角色的数据结构开始，逐步实现输入处理、移动、攻击、动作系统，打造一个手感丝滑的可控角色。

### 第12讲 玩家角色结构设计

#### 概念

**玩家角色（Player Character）** 是用户在游戏世界中的化身。一个设计良好的玩家结构应包含：位置、速度、方向、动画状态、生命值、攻击力、装备、状态标志等。本讲设计一个完整的Player结构体，为后续功能扩展打好基础。

#### 原理

**实体（Entity）概念**：

游戏中所有可交互对象（玩家、敌人、NPC、子弹）都是实体。实体通常有共同属性：位置、大小、速度、生命值。可以设计一个Entity基类，玩家和敌人继承它。但C语言没有继承，常用**组合**方式：

```c
typedef struct {
    Entity base;      // 共同属性
    PlayerSpecific specific;  // 玩家特有属性
} Player;
```

**玩家状态标志**：

玩家有多种状态：是否在攻击、是否受伤、是否无敌、是否在对话。用位标志（bitfield）节省内存：

```c
#define PLAYER_FLAG_ATTACKING  (1 << 0)
#define PLAYER_FLAG_HURT        (1 << 1)
#define PLAYER_FLAG_INVINCIBLE  (1 << 2)
#define PLAYER_FLAG_TALKING     (1 << 3)

if (player.flags & PLAYER_FLAG_ATTACKING) { ... }
player.flags |= PLAYER_FLAG_HURT;   // 设置
player.flags &= ~PLAYER_FLAG_HURT;  // 清除
```

**碰撞盒与渲染盒分离**：

角色精灵图通常64×64，但实际碰撞盒应该更小（如32×24，居中底部），这样视觉上更合理——玩家可以"贴近"墙壁而不显得有缝隙。

#### 例子

```c
// player.h
#ifndef PLAYER_H
#define PLAYER_H

#include "animation.h"
#include "camera.h"

typedef enum {
    PLAYER_STATE_IDLE = 0,
    PLAYER_STATE_WALK,
    PLAYER_STATE_ATTACK,
    PLAYER_STATE_HURT,
    PLAYER_STATE_DIE
} PlayerState;

typedef struct {
    // 位置和速度（世界坐标）
    float x, y;
    float vx, vy;
    int width, height;       // 渲染尺寸

    // 碰撞盒（相对渲染盒的偏移）
    int hitbox_offset_x;
    int hitbox_offset_y;
    int hitbox_w;
    int hitbox_h;

    // 方向和状态
    Direction dir;
    PlayerState state;
    int flags;  // 位标志

    // 属性
    int max_hp;
    int hp;
    int attack_power;
    float speed;
    float invincible_timer;  // 无敌时间

    // 动画
    Animator animator;

    // 攻击相关
    float attack_timer;
    float attack_duration;
    int attack_hit;  // 本次攻击是否已造成伤害（避免重复伤害）
} Player;

void player_init(Player* p, SpriteSheet* sheet, float x, float y);
void player_update(Player* p, float dt);
void player_draw(Player* p, SDL_Renderer* r, Camera* cam);
void player_take_damage(Player* p, int damage);
void player_attack(Player* p);
SDL_Rect player_get_hitbox(Player* p);
SDL_Rect player_get_attack_box(Player* p);

// 标志位操作
#define player_has_flag(p, f)  ((p)->flags & (f))
#define player_set_flag(p, f)  ((p)->flags |= (f))
#define player_clear_flag(p, f) ((p)->flags &= ~(f))

#endif
```

```c
// player.c
#include "player.h"
#include <string.h>

void player_init(Player* p, SpriteSheet* sheet, float x, float y) {
    memset(p, 0, sizeof(*p));
    p->x = x;
    p->y = y;
    p->width = 48;
    p->height = 48;
    p->speed = 180.0f;

    // 碰撞盒：底部居中，比渲染盒小
    p->hitbox_offset_x = 8;
    p->hitbox_offset_y = 24;
    p->hitbox_w = 32;
    p->hitbox_h = 24;

    p->dir = DIR_DOWN;
    p->state = PLAYER_STATE_IDLE;

    p->max_hp = 6;
    p->hp = 6;
    p->attack_power = 1;
    p->attack_duration = 0.3f;

    animator_init(&p->animator, sheet);
}

SDL_Rect player_get_hitbox(Player* p) {
    SDL_Rect r = {
        (int)p->x + p->hitbox_offset_x,
        (int)p->y + p->hitbox_offset_y,
        p->hitbox_w,
        p->hitbox_h
    };
    return r;
}

// 攻击范围：在玩家前方一定区域
SDL_Rect player_get_attack_box(Player* p) {
    SDL_Rect hitbox = player_get_hitbox(p);
    SDL_Rect attack;
    int range = 24;
    switch (p->dir) {
        case DIR_DOWN:
            attack = (SDL_Rect){hitbox.x, hitbox.y + hitbox.h, hitbox.w, range};
            break;
        case DIR_UP:
            attack = (SDL_Rect){hitbox.x, hitbox.y - range, hitbox.w, range};
            break;
        case DIR_LEFT:
            attack = (SDL_Rect){hitbox.x - range, hitbox.y, range, hitbox.h};
            break;
        case DIR_RIGHT:
            attack = (SDL_Rect){hitbox.x + hitbox.w, hitbox.y, range, hitbox.h};
            break;
    }
    return attack;
}

void player_take_damage(Player* p, int damage) {
    if (player_has_flag(p, PLAYER_FLAG_INVINCIBLE)) return;
    if (p->state == PLAYER_STATE_DIE) return;

    p->hp -= damage;
    player_set_flag(p, PLAYER_FLAG_HURT);
    player_set_flag(p, PLAYER_FLAG_INVINCIBLE);
    p->invincible_timer = 1.0f;  // 1秒无敌

    if (p->hp <= 0) {
        p->hp = 0;
        p->state = PLAYER_STATE_DIE;
    } else {
        p->state = PLAYER_STATE_HURT;
    }
}

void player_attack(Player* p) {
    if (p->state == PLAYER_STATE_ATTACK) return;
    if (p->state == PLAYER_STATE_DIE) return;
    p->state = PLAYER_STATE_ATTACK;
    p->attack_timer = p->attack_duration;
    p->attack_hit = 0;
    player_set_flag(p, PLAYER_FLAG_ATTACKING);
}

void player_draw(Player* p, SDL_Renderer* r, Camera* cam) {
    int sx, sy;
    camera_world_to_screen(cam, p->x, p->y, &sx, &sy);

    // 无敌时闪烁
    if (player_has_flag(p, PLAYER_FLAG_INVINCIBLE)) {
        int blink = ((int)(p->invincible_timer * 10)) % 2;
        if (blink) return;  // 跳过本帧绘制
    }

    animator_draw(&p->animator, r, sx, sy, p->width, p->height);

    // 调试：绘制碰撞盒和攻击盒
    #ifdef DEBUG_DRAW
    SDL_Rect hb = player_get_hitbox(p);
    camera_world_to_screen(cam, hb.x, hb.y, &sx, &sy);
    SDL_Rect screen_hb = {sx, sy, hb.w, hb.h};
    SDL_SetRenderDrawColor(r, 0, 255, 0, 128);
    SDL_RenderDrawRect(r, &screen_hb);

    if (player_has_flag(p, PLAYER_FLAG_ATTACKING)) {
        SDL_Rect ab = player_get_attack_box(p);
        camera_world_to_screen(cam, ab.x, ab.y, &sx, &sy);
        SDL_Rect screen_ab = {sx, sy, ab.w, ab.h};
        SDL_SetRenderDrawColor(r, 255, 0, 0, 128);
        SDL_RenderDrawRect(r, &screen_ab);
    }
    #endif
}
```

#### 总结

- **实体设计**：位置、速度、大小、生命值、动画、状态标志
- **位标志**：用bitfield管理多个布尔状态，节省内存且操作高效
- **碰撞盒分离**：渲染盒大（视觉好看），碰撞盒小（手感好），通常底部居中
- **攻击盒**：根据方向在玩家前方生成，用于检测命中敌人
- **无敌时间**：受伤后短暂无敌，避免连续掉血，配合闪烁视觉反馈
- **常见坑**：忘记memset导致字段未初始化；碰撞盒和渲染盒混用导致手感差

---

### 第13讲 输入处理与角色移动

#### 概念

**输入处理（Input Handling）** 是连接玩家与游戏的桥梁。本讲实现完整的玩家移动系统：8方向移动、对角线归一化、加速减速、方向锁定（攻击时不能转向）、按键缓冲（提前按攻击键也能响应）。

#### 原理

**8方向移动**：

读取上下左右四个方向键，组合出8个方向。对角线移动时需归一化，否则速度比直线快41%（√2倍）。

**加速减速（可选）**：

真实角色移动有惯性——按下键不是瞬间达到最大速度，而是逐渐加速；松开键逐渐减速。这能让手感更真实：

```c
// 加速
if (input != 0) {
    velocity += accel * dt;
    if (velocity > max_speed) velocity = max_speed;
}
// 减速
else {
    velocity -= friction * dt;
    if (velocity < 0) velocity = 0;
}
```

**按键缓冲（Input Buffer）**：

玩家在攻击动画快结束时按下攻击键，传统做法是忽略（动画未结束）。按键缓冲记录最近100ms内的输入，动画一结束立即响应，提升手感：

```c
if (attack_pressed) {
    attack_buffer_timer = 0.1f;  // 缓冲100ms
}
// 在动画结束时检查
if (attack_buffer_timer > 0) {
    start_attack();
    attack_buffer_timer = 0;
}
```

**方向锁定**：

攻击时不能转向（避免攻击方向乱跳），但可以保留最后方向。

#### 例子

```c
// player.c 续
void player_update(Player* p, float dt) {
    // 衰减计时器
    if (p->invincible_timer > 0) {
        p->invincible_timer -= dt;
        if (p->invincible_timer <= 0) {
            player_clear_flag(p, PLAYER_FLAG_INVINCIBLE);
            player_clear_flag(p, PLAYER_FLAG_HURT);
        }
    }

    // 死亡状态：只播放死亡动画
    if (p->state == PLAYER_STATE_DIE) {
        animator_set_state(&p->animator, ANIM_DIE, p->dir);
        animator_update(&p->animator, dt);
        return;
    }

    // 攻击状态：锁定移动，播放攻击动画
    if (p->state == PLAYER_STATE_ATTACK) {
        p->attack_timer -= dt;
        animator_set_state(&p->animator, ANIM_ATTACK, p->dir);
        animator_update(&p->animator, dt);

        if (p->attack_timer <= 0 || p->animator.finished) {
            p->state = PLAYER_STATE_IDLE;
            player_clear_flag(p, PLAYER_FLAG_ATTACKING);
        }
        return;  // 攻击中不处理移动
    }

    // 受伤状态：短暂硬直
    if (p->state == PLAYER_STATE_HURT) {
        // 受伤硬直0.3秒
        // 简化：直接转idle
        p->state = PLAYER_STATE_IDLE;
    }

    // 读取输入
    const Uint8* keys = SDL_GetKeyboardState(NULL);
    float dx = 0, dy = 0;
    if (keys[SDL_SCANCODE_LEFT] || keys[SDL_SCANCODE_A])  dx -= 1;
    if (keys[SDL_SCANCODE_RIGHT] || keys[SDL_SCANCODE_D]) dx += 1;
    if (keys[SDL_SCANCODE_UP] || keys[SDL_SCANCODE_W])    dy -= 1;
    if (keys[SDL_SCANCODE_DOWN] || keys[SDL_SCANCODE_S])  dy += 1;

    // 对角线归一化
    if (dx != 0 && dy != 0) {
        dx *= 0.7071f;
        dy *= 0.7071f;
    }

    // 更新方向（只在有输入时更新，保留最后方向）
    if (dx != 0 || dy != 0) {
        if (fabsf(dx) > fabsf(dy)) {
            p->dir = (dx > 0) ? DIR_RIGHT : DIR_LEFT;
        } else {
            p->dir = (dy > 0) ? DIR_DOWN : DIR_UP;
        }
    }

    // 移动
    p->vx = dx * p->speed;
    p->vy = dy * p->speed;
    p->x += p->vx * dt;
    p->y += p->vy * dt;

    // 状态切换
    if (dx != 0 || dy != 0) {
        p->state = PLAYER_STATE_WALK;
        animator_set_state(&p->animator, ANIM_WALK, p->dir);
    } else {
        p->state = PLAYER_STATE_IDLE;
        animator_set_state(&p->animator, ANIM_IDLE, p->dir);
    }

    animator_update(&p->animator, dt);
}
```

**带按键缓冲的攻击处理**：

```c
// 在事件处理中
void player_handle_event(Player* p, SDL_Event* event) {
    if (event->type == SDL_KEYDOWN) {
        if (event->key.keysym.scancode == SDL_SCANCODE_J ||
            event->key.keysym.scancode == SDL_SCANCODE_SPACE) {
            // 攻击键按下
            if (p->state == PLAYER_STATE_ATTACK) {
                p->attack_buffer = 0.1f;  // 缓冲
            } else {
                player_attack(p);
            }
        }
    }
}

// 在update中检查缓冲
void player_update(Player* p, float dt) {
    // ... 攻击状态结束的代码
    if (p->attack_timer <= 0) {
        p->state = PLAYER_STATE_IDLE;
        player_clear_flag(p, PLAYER_FLAG_ATTACKING);
        // 检查缓冲
        if (p->attack_buffer > 0) {
            player_attack(p);
            p->attack_buffer = 0;
        }
    }

    // 衰减缓冲
    if (p->attack_buffer > 0) {
        p->attack_buffer -= dt;
    }
    // ... 其余更新
}
```

#### 总结

- **8方向移动**：四方向键组合，对角线归一化避免速度差
- **方向更新策略**：只在有输入时更新方向，保留最后朝向
- **加速减速**：可选的真实手感，按下加速、松开减速
- **按键缓冲**：记录100ms内输入，动画结束立即响应，提升手感
- **方向锁定**：攻击时锁定方向，避免攻击方向乱跳
- **常见坑**：对角线不归一化导致斜向移动快41%；攻击中忘记return导致移动和攻击同时进行

---

### 第14讲 攻击与动作系统

#### 概念

**攻击系统（Combat System）** 是动作游戏的核心。本讲实现完整的近战攻击：攻击动画、攻击判定盒、命中检测、击退效果、伤害判定、连击系统。这是玩家与敌人交互的基础。

#### 原理

**攻击判定流程**：

1. 玩家按攻击键 → 进入ATTACK状态，播放攻击动画
2. 攻击动画的特定帧（如第2帧）激活攻击盒
3. 每帧检测攻击盒与敌人碰撞盒的相交
4. 相交则造成伤害，标记本次攻击已命中（避免重复伤害）
5. 攻击动画结束 → 回到IDLE状态

**攻击盒（Attack Box）**：

根据玩家方向在玩家前方生成一个矩形区域，用于检测命中。通常比玩家碰撞盒略大，向前延伸一定距离。

**击退效果**：

命中敌人时，敌人被向后推开一段距离，增加打击感：

```c
enemy->vx = direction_x * knockback_force;
enemy->vy = direction_y * knockback_force;
enemy->knockback_timer = 0.2f;
```

**连击系统**：

连续按攻击键触发不同攻击动作（如三连击），每次伤害递增：

```c
if (attack_pressed && last_attack_time < 0.5f) {
    combo_count++;
    if (combo_count > 3) combo_count = 1;
} else {
    combo_count = 1;
}
```

#### 例子

```c
// combat.h
#ifndef COMBAT_H
#define COMBAT_H

#include "player.h"

typedef struct Enemy Enemy;  // 前向声明

void combat_player_attack_enemies(Player* p, Enemy* enemies, int count);
void combat_enemy_attack_player(Enemy* e, Player* p);

#endif
```

```c
// combat.c
#include "combat.h"
#include "enemy.h"
#include <math.h>

// 检测两个矩形是否相交
static int rects_intersect(SDL_Rect a, SDL_Rect b) {
    return (a.x < b.x + b.w && a.x + a.w > b.x &&
            a.y < b.y + b.h && a.y + a.h > b.y);
}

void combat_player_attack_enemies(Player* p, Enemy* enemies, int count) {
    if (!player_has_flag(p, PLAYER_FLAG_ATTACKING)) return;
    if (p->attack_hit) return;  // 本次攻击已命中过

    // 攻击动画在特定帧才生效（如第1帧，0-indexed）
    if (p->animator.current_frame < 1) return;

    SDL_Rect attack_box = player_get_attack_box(p);

    for (int i = 0; i < count; i++) {
        Enemy* e = &enemies[i];
        if (!enemy_is_alive(e)) continue;
        if (enemy_has_flag(e, ENEMY_FLAG_INVINCIBLE)) continue;

        SDL_Rect enemy_box = enemy_get_hitbox(e);
        if (rects_intersect(attack_box, enemy_box)) {
            // 命中！
            enemy_take_damage(e, p->attack_power);

            // 击退
            float dx = e->x - p->x;
            float dy = e->y - p->y;
            float len = sqrtf(dx*dx + dy*dy);
            if (len > 0) {
                dx /= len;
                dy /= len;
            }
            enemy_apply_knockback(e, dx * 200.0f, dy * 200.0f, 0.2f);

            // 标记本次攻击已命中（单次攻击只伤害一个敌人，或全部敌人）
            // 选择1：只伤害第一个 p->attack_hit = 1; break;
            // 选择2：横扫全部敌人（不设attack_hit）
            p->attack_hit = 1;
            break;  // 单次攻击只命中一个
        }
    }
}

void combat_enemy_attack_player(Enemy* e, Player* p) {
    if (!enemy_is_attacking(e)) return;

    SDL_Rect enemy_attack_box = enemy_get_attack_box(e);
    SDL_Rect player_box = player_get_hitbox(p);

    if (rects_intersect(enemy_attack_box, player_box)) {
        player_take_damage(p, e->attack_power);

        // 击退玩家
        float dx = p->x - e->x;
        float dy = p->y - e->y;
        float len = sqrtf(dx*dx + dy*dy);
        if (len > 0) { dx /= len; dy /= len; }
        p->x += dx * 30.0f;  // 立即推开
        p->y += dy * 30.0f;
    }
}
```

**连击系统扩展**：

```c
// player.h 添加字段
typedef struct {
    // ... 原有字段
    int combo_count;
    float combo_timer;  // 连击窗口
    float last_attack_time;
} Player;

// player.c
void player_attack(Player* p) {
    if (p->state == PLAYER_STATE_DIE) return;

    // 连击逻辑
    float now = SDL_GetTicks() / 1000.0f;
    if (now - p->last_attack_time < 0.5f) {
        p->combo_count++;
        if (p->combo_count > 3) p->combo_count = 1;
    } else {
        p->combo_count = 1;
    }
    p->last_attack_time = now;
    p->combo_timer = 0.5f;

    // 根据连击数调整攻击力
    p->attack_power = 1 + (p->combo_count - 1);  // 1, 2, 3

    p->state = PLAYER_STATE_ATTACK;
    p->attack_timer = p->attack_duration;
    p->attack_hit = 0;
    player_set_flag(p, PLAYER_FLAG_ATTACKING);
}

void player_update(Player* p, float dt) {
    // ... 衰减连击计时器
    if (p->combo_timer > 0) {
        p->combo_timer -= dt;
        if (p->combo_timer <= 0) {
            p->combo_count = 0;
        }
    }
    // ... 其余更新
}
```

#### 总结

- **攻击判定流程**：按键→动画→特定帧激活攻击盒→检测相交→造成伤害
- **攻击盒**：根据方向在玩家前方生成，用于命中检测
- **单次命中**：用attack_hit标志避免一次攻击多次伤害同一敌人
- **击退效果**：命中后给敌人施加速度，增加打击感
- **连击系统**：记录连击窗口内的攻击次数，递增伤害
- **常见坑**：攻击盒每帧都检测导致一次攻击多次伤害；忘记击退导致打击感弱；连击窗口设置不当导致连击难触发

---

## 第五章 地图系统

> 地图是游戏世界的舞台。本章学习Tile地图原理、地图数据加载、多层渲染、场景切换，构建可探索的塞尔达式世界。

### 第15讲 Tile地图基础

#### 概念

**Tile地图（瓦片地图）** 是用小方块（瓦片）拼接成大地图的技术。一张32×32的瓦片图集可以拼出无限大的地图。这是2D游戏最经典的地图表示方式，塞尔达、宝可梦、星露谷等游戏都用Tile地图。

#### 原理

**核心数据结构**：

地图本质是一个二维数组，每个元素是瓦片ID（指向瓦片图集中的某个瓦片）：

```
地图数据（5×4）：
1 1 1 1 1
1 0 0 0 1
1 0 2 0 1
1 1 1 1 1

瓦片图集：
ID 0: 草地
ID 1: 墙壁
ID 2: 树
```

**瓦片图集（Tileset）**：

一张大图包含所有瓦片，按网格排列。如32×32瓦片，图集8×8网格，总尺寸256×256。瓦片ID计算：

```
tile_id = row * cols + col
col = tile_id % cols
row = tile_id / cols
```

**地图坐标 vs 世界坐标**：

```
world_x = tile_x * TILE_SIZE
world_y = tile_y * TILE_SIZE
tile_x = world_x / TILE_SIZE
tile_y = world_y / TILE_SIZE
```

**瓦片属性**：

每个瓦片ID除了图形，还有属性：是否可通行、是否可破坏、是否是水（不可走但可游泳）、是否是传送点。通常用单独的属性表存储。

#### 例子

```c
// tilemap.h
#ifndef TILEMAP_H
#define TILEMAP_H

#include "sprite.h"
#include "camera.h"

#define TILE_SIZE 32
#define MAX_MAP_TILES 256  // 瓦片种类上限

typedef enum {
    TILE_PROP_NONE     = 0,
    TILE_PROP_SOLID    = 1 << 0,  // 实心（不可通行）
    TILE_PROP_WATER    = 1 << 1,  // 水
    TILE_PROP_LADDER   = 1 << 2,  // 梯子
    TILE_PROP_DAMAGE   = 1 << 3,  // 伤害
    TILE_PROP_TRIGGER  = 1 << 4,  // 触发器
} TileProperty;

typedef struct {
    int width;          // 地图宽（瓦片数）
    int height;         // 地图高（瓦片数）
    int* tiles;         // 瓦片ID数组，width*height
    int tile_props[MAX_MAP_TILES];  // 瓦片属性表
    SpriteSheet tileset;
} Tilemap;

int tilemap_load(Tilemap* tm, SDL_Renderer* r,
                 const char* tileset_path,
                 int* map_data, int map_w, int map_h);
void tilemap_free(Tilemap* tm);
void tilemap_draw(Tilemap* tm, SDL_Renderer* r, Camera* cam);
int tilemap_get_tile(Tilemap* tm, int tile_x, int tile_y);
int tilemap_is_solid(Tilemap* tm, int tile_x, int tile_y);
int tilemap_is_solid_at(Tilemap* tm, float world_x, float world_y);

#endif
```

```c
// tilemap.c
#include "tilemap.h"
#include <stdlib.h>
#include <string.h>

int tilemap_load(Tilemap* tm, SDL_Renderer* r,
                 const char* tileset_path,
                 int* map_data, int map_w, int map_h) {
    if (sprite_sheet_load(&tm->tileset, r, tileset_path, TILE_SIZE, TILE_SIZE) != 0)
        return -1;

    tm->width = map_w;
    tm->height = map_h;
    tm->tiles = malloc(map_w * map_h * sizeof(int));
    if (!tm->tiles) return -1;
    memcpy(tm->tiles, map_data, map_w * map_h * sizeof(int));

    // 初始化属性表（示例）
    memset(tm->tile_props, 0, sizeof(tm->tile_props));
    tm->tile_props[1] = TILE_PROP_SOLID;  // ID 1 = 墙壁
    tm->tile_props[2] = TILE_PROP_SOLID;  // ID 2 = 树
    tm->tile_props[3] = TILE_PROP_WATER;  // ID 3 = 水

    return 0;
}

void tilemap_free(Tilemap* tm) {
    if (tm->tiles) {
        free(tm->tiles);
        tm->tiles = NULL;
    }
    sprite_sheet_free(&tm->tileset);
}

int tilemap_get_tile(Tilemap* tm, int tile_x, int tile_y) {
    if (tile_x < 0 || tile_x >= tm->width || tile_y < 0 || tile_y >= tm->height)
        return 1;  // 越界视为墙壁
    return tm->tiles[tile_y * tm->width + tile_x];
}

int tilemap_is_solid(Tilemap* tm, int tile_x, int tile_y) {
    int tile = tilemap_get_tile(tm, tile_x, tile_y);
    if (tile < 0 || tile >= MAX_MAP_TILES) return 0;
    return (tm->tile_props[tile] & TILE_PROP_SOLID) != 0;
}

int tilemap_is_solid_at(Tilemap* tm, float world_x, float world_y) {
    int tx = (int)(world_x / TILE_SIZE);
    int ty = (int)(world_y / TILE_SIZE);
    return tilemap_is_solid(tm, tx, ty);
}

void tilemap_draw(Tilemap* tm, SDL_Renderer* r, Camera* cam) {
    // 计算可见瓦片范围（视锥剔除）
    int start_tx = (int)(cam->x / TILE_SIZE);
    int start_ty = (int)(cam->y / TILE_SIZE);
    int end_tx = (int)((cam->x + cam->view_width) / TILE_SIZE) + 1;
    int end_ty = (int)((cam->y + cam->view_height) / TILE_SIZE) + 1;

    if (start_tx < 0) start_tx = 0;
    if (start_ty < 0) start_ty = 0;
    if (end_tx >= tm->width) end_tx = tm->width - 1;
    if (end_ty >= tm->height) end_ty = tm->height - 1;

    for (int ty = start_ty; ty <= end_ty; ty++) {
        for (int tx = start_tx; tx <= end_tx; tx++) {
            int tile_id = tm->tiles[ty * tm->width + tx];
            if (tile_id == 0) continue;  // 0通常表示空

            int col = tile_id % tm->tileset.cols;
            int row = tile_id / tm->tileset.cols;

            int sx, sy;
            camera_world_to_screen(cam, tx * TILE_SIZE, ty * TILE_SIZE, &sx, &sy);
            sprite_sheet_draw(&tm->tileset, r, col, row, sx, sy, TILE_SIZE, TILE_SIZE);
        }
    }
}
```

#### 总结

- **Tile地图本质**：二维数组存瓦片ID，瓦片图集存图形
- **瓦片ID计算**：`col = id % cols; row = id / cols;`
- **视锥剔除**：只绘制摄像机可见的瓦片，大幅提升性能
- **瓦片属性**：用位标志存储solid/water/ladder等属性，独立于图形
- **越界处理**：地图外默认视为墙壁，防止角色走出地图
- **常见坑**：忘记视锥剔除导致大地图卡顿；瓦片ID和图集位置计算错误导致显示乱码

---

### 第16讲 地图数据加载

#### 概念

硬编码地图数据不现实——一个100×100的地图有10000个瓦片。本讲学习从外部文件加载地图数据，支持自定义二进制格式和Tiled地图编辑器的TMX格式。

#### 原理

**自定义二进制格式**：

最简单的格式：文件头（宽、高）+ 瓦片数据数组。

```
[4字节: width][4字节: height][width*height*4字节: tile数据]
```

优点：紧凑、加载快；缺点：不可读，需自己写编辑器。

**文本格式（CSV）**：

```
1,1,1,1,1
1,0,0,0,1
1,0,2,0,1
1,1,1,1,1
```

优点：可读、可手动编辑、可被Tiled导出；缺点：文件较大。

**Tiled TMX格式**：

Tiled是开源地图编辑器，导出XML格式的TMX文件。支持多图层、对象层、瓦片属性。SDL2生态有TMX解析库（如SDL2_tmx）。

**资源路径管理**：

游戏需要从相对路径加载资源。建议统一资源根目录：

```
assets/
├── maps/
│   ├── overworld.bin
│   └── dungeon1.bin
├── tilesets/
│   ├── overworld_tiles.png
│   └── dungeon_tiles.png
├── sprites/
│   └── player.png
└── sounds/
```

#### 例子

**二进制格式加载**：

```c
// map_loader.h
#ifndef MAP_LOADER_H
#define MAP_LOADER_H

#include "tilemap.h"

int map_load_binary(Tilemap* tm, SDL_Renderer* r,
                    const char* map_path, const char* tileset_path);
int map_load_csv(Tilemap* tm, SDL_Renderer* r,
                 const char* map_path, const char* tileset_path);

#endif
```

```c
// map_loader.c
#include "map_loader.h"
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int map_load_binary(Tilemap* tm, SDL_Renderer* r,
                    const char* map_path, const char* tileset_path) {
    FILE* f = fopen(map_path, "rb");
    if (!f) {
        fprintf(stderr, "Cannot open map: %s\n", map_path);
        return -1;
    }

    int width, height;
    fread(&width, sizeof(int), 1, f);
    fread(&height, sizeof(int), 1, f);

    int* tiles = malloc(width * height * sizeof(int));
    if (!tiles) { fclose(f); return -1; }
    fread(tiles, sizeof(int), width * height, f);
    fclose(f);

    int result = tilemap_load(tm, r, tileset_path, tiles, width, height);
    free(tiles);
    return result;
}

int map_load_csv(Tilemap* tm, SDL_Renderer* r,
                 const char* map_path, const char* tileset_path) {
    FILE* f = fopen(map_path, "r");
    if (!f) return -1;

    // 第一遍：计算宽高
    int width = 0, height = 0;
    char line[4096];
    while (fgets(line, sizeof(line), f)) {
        if (height == 0) {
            // 计算第一行的逗号数+1
            int commas = 0;
            for (char* p = line; *p; p++) if (*p == ',') commas++;
            width = commas + 1;
        }
        height++;
    }
    rewind(f);

    int* tiles = malloc(width * height * sizeof(int));
    if (!tiles) { fclose(f); return -1; }

    int idx = 0;
    while (fgets(line, sizeof(line), f) && idx < width * height) {
        char* token = strtok(line, ",\n");
        while (token && idx < width * height) {
            tiles[idx++] = atoi(token);
            token = strtok(NULL, ",\n");
        }
    }
    fclose(f);

    int result = tilemap_load(tm, r, tileset_path, tiles, width, height);
    free(tiles);
    return result;
}
```

**地图生成工具（简单版）**：

```c
// map_gen.c - 生成一个测试地图并保存为二进制
#include <stdio.h>
#include <stdlib.h>

int main() {
    int width = 50, height = 40;
    int* tiles = calloc(width * height, sizeof(int));

    // 边界墙
    for (int x = 0; x < width; x++) {
        tiles[0 * width + x] = 1;
        tiles[(height-1) * width + x] = 1;
    }
    for (int y = 0; y < height; y++) {
        tiles[y * width + 0] = 1;
        tiles[y * width + (width-1)] = 1;
    }

    // 随机放树
    for (int i = 0; i < 30; i++) {
        int x = rand() % (width - 2) + 1;
        int y = rand() % (height - 2) + 1;
        tiles[y * width + x] = 2;
    }

    // 保存
    FILE* f = fopen("test_map.bin", "wb");
    fwrite(&width, sizeof(int), 1, f);
    fwrite(&height, sizeof(int), 1, f);
    fwrite(tiles, sizeof(int), width * height, f);
    fclose(f);
    free(tiles);
    return 0;
}
```

#### 总结

- **二进制格式**：紧凑高效，适合发布版本
- **CSV格式**：可读可编辑，Tiled可直接导出，开发阶段推荐
- **Tiled编辑器**：开源强大的地图编辑器，支持图层、对象、属性
- **资源路径**：统一assets目录，相对路径加载
- **地图生成**：可用算法程序化生成地图（随机、迷宫、房间）
- **常见坑**：字节序问题（跨平台需统一为大端或小端）；CSV末尾换行处理不当导致多读一行

---

### 第17讲 多层地图渲染

#### 概念

真实游戏地图不止一层：地面层（草地、地板）、装饰层（花、小石头）、高层层（树冠、屋顶，玩家可走到下面）、碰撞层（不可见的实心区域）。多层地图让世界更丰富，玩家可以走在树下、桥上。

#### 原理

**图层类型**：

1. **地面层（Ground）**：最底层，草地、地板、水
2. **装饰层（Decoration）**：花、小路、地上的物品
3. **实体层（Entities）**：玩家、敌人、NPC（在地面之上，高层之下）
4. **高层层（Overhead）**：树冠、屋顶、拱门顶部，遮挡实体
5. **碰撞层（Collision）**：不可见，只用于碰撞检测

**渲染顺序**：

```
1. 绘制地面层
2. 绘制装饰层
3. 绘制实体层（玩家、敌人，按y排序）
4. 绘制高层层
5. 绘制特效层
6. 绘制UI层
```

**为什么实体层在高层之下？**

当玩家走到树下时，树冠应该遮挡玩家上半身，营造"走在树下"的感觉。所以高层在实体之后绘制。

**多层地图数据结构**：

```c
typedef struct {
    int width, height;
    int* ground;   // 地面层
    int* decor;    // 装饰层
    int* overhead; // 高层层
    int* collision; // 碰撞层（0/1）
} MultiLayerMap;
```

#### 例子

```c
// multilayer_map.h
#ifndef MULTILAYER_MAP_H
#define MULTILAYER_MAP_H

#include "tilemap.h"
#include "camera.h"
#include "sprite.h"

typedef struct {
    int width, height;
    int* ground;
    int* decor;
    int* overhead;
    int* collision;
    SpriteSheet tileset;
    int tile_props[MAX_MAP_TILES];
} MultiLayerMap;

int mlmap_load(MultiLayerMap* m, SDL_Renderer* r,
               const char* tileset_path,
               const char* ground_path,
               const char* decor_path,
               const char* overhead_path,
               const char* collision_path,
               int map_w, int map_h);
void mlmap_free(MultiLayerMap* m);
void mlmap_draw_ground(MultiLayerMap* m, SDL_Renderer* r, Camera* cam);
void mlmap_draw_decor(MultiLayerMap* m, SDL_Renderer* r, Camera* cam);
void mlmap_draw_overhead(MultiLayerMap* m, SDL_Renderer* r, Camera* cam);
int mlmap_is_solid(MultiLayerMap* m, int tx, int ty);

#endif
```

```c
// multilayer_map.c
#include "multilayer_map.h"
#include <stdlib.h>
#include <string.h>
#include <stdio.h>

static void draw_layer(MultiLayerMap* m, SDL_Renderer* r, Camera* cam, int* layer) {
    int start_tx = (int)(cam->x / TILE_SIZE);
    int start_ty = (int)(cam->y / TILE_SIZE);
    int end_tx = (int)((cam->x + cam->view_width) / TILE_SIZE) + 1;
    int end_ty = (int)((cam->y + cam->view_height) / TILE_SIZE) + 1;

    if (start_tx < 0) start_tx = 0;
    if (start_ty < 0) start_ty = 0;
    if (end_tx >= m->width) end_tx = m->width - 1;
    if (end_ty >= m->height) end_ty = m->height - 1;

    for (int ty = start_ty; ty <= end_ty; ty++) {
        for (int tx = start_tx; tx <= end_tx; tx++) {
            int tile_id = layer[ty * m->width + tx];
            if (tile_id == 0) continue;

            int col = tile_id % m->tileset.cols;
            int row = tile_id / m->tileset.cols;

            int sx, sy;
            camera_world_to_screen(cam, tx * TILE_SIZE, ty * TILE_SIZE, &sx, &sy);
            sprite_sheet_draw(&m->tileset, r, col, row, sx, sy, TILE_SIZE, TILE_SIZE);
        }
    }
}

void mlmap_draw_ground(MultiLayerMap* m, SDL_Renderer* r, Camera* cam) {
    draw_layer(m, r, cam, m->ground);
}
void mlmap_draw_decor(MultiLayerMap* m, SDL_Renderer* r, Camera* cam) {
    draw_layer(m, r, cam, m->decor);
}
void mlmap_draw_overhead(MultiLayerMap* m, SDL_Renderer* r, Camera* cam) {
    draw_layer(m, r, cam, m->overhead);
}

int mlmap_is_solid(MultiLayerMap* m, int tx, int ty) {
    if (tx < 0 || tx >= m->width || ty < 0 || ty >= m->height) return 1;
    return m->collision[ty * m->width + tx];
}
```

**渲染流程示例**：

```c
void game_render(Game* game) {
    SDL_SetRenderDrawColor(game->renderer, 0, 0, 0, 255);
    SDL_RenderClear(game->renderer);

    // 1. 地面层
    mlmap_draw_ground(&game->map, game->renderer, &game->camera);

    // 2. 装饰层
    mlmap_draw_decor(&game->map, game->renderer, &game->camera);

    // 3. 实体层（玩家、敌人，按y排序）
    // 收集所有实体，按y排序，依次绘制
    Entity* entities[32];
    int count = 0;
    entities[count++] = (Entity*)&game->player;
    for (int i = 0; i < game->enemy_count; i++) {
        if (enemy_is_alive(&game->enemies[i]))
            entities[count++] = (Entity*)&game->enemies[i];
    }
    // 按y排序
    qsort(entities, count, sizeof(Entity*), compare_entity_y);
    for (int i = 0; i < count; i++) {
        entity_draw(entities[i], game->renderer, &game->camera);
    }

    // 4. 高层层（树冠、屋顶，遮挡玩家）
    mlmap_draw_overhead(&game->map, game->renderer, &game->camera);

    // 5. 特效层（粒子、伤害数字）
    effects_draw(&game->effects, game->renderer, &game->camera);

    // 6. UI层（HUD）
    hud_draw(&game->hud, game->renderer);

    SDL_RenderPresent(game->renderer);
}
```

#### 总结

- **多层地图**：地面、装饰、实体、高层、碰撞，各司其职
- **渲染顺序**：地面→装饰→实体→高层→特效→UI
- **高层遮挡**：树冠在实体后绘制，营造"走在树下"效果
- **碰撞层独立**：不可见，只用于碰撞检测，与图形解耦
- **视锥剔除**：每层都做视锥剔除，避免绘制不可见瓦片
- **常见坑**：高层和地面顺序颠倒导致玩家永远在树上方；碰撞层和图形层混用导致修改图形影响碰撞

---

### 第18讲 场景切换与传送

#### 概念

**场景切换（Scene Transition）** 是玩家从一个地图进入另一个地图的机制，如从大地图进入村庄、从村庄进入房屋、进入地下城。本讲实现传送门系统、场景切换动画、玩家位置保留。

#### 原理

**传送门（Portal）**：

地图上特定瓦片或区域，玩家踩上去触发场景切换。传送门数据：

```c
typedef struct {
    int src_map_id;       // 源地图
    int src_x, src_y;     // 源位置（瓦片坐标）
    int dst_map_id;       // 目标地图
    int dst_x, dst_y;     // 目标位置（世界坐标）
    int trigger_type;     // 触发类型：踩到、按键
} Portal;
```

**场景切换流程**：

1. 检测玩家是否在传送门上
2. 触发切换：保存当前地图状态（可选）
3. 加载新地图（释放旧资源，加载新资源）
4. 设置玩家新位置
5. 播放切换动画（淡入淡出）

**淡入淡出动画**：

切换时用全屏黑色矩形，alpha从0到255（淡出）或255到0（淡入）：

```c
// 淡出阶段
fade_alpha += fade_speed * dt;
if (fade_alpha >= 255) {
    fade_alpha = 255;
    // 实际切换地图
    switch_map();
    state = FADE_IN;
}
// 淡入阶段
fade_alpha -= fade_speed * dt;
if (fade_alpha <= 0) {
    fade_alpha = 0;
    state = NORMAL;
}
```

**地图状态保存**：

切换回旧地图时，是否保留之前的状态（如已打开的宝箱、已击杀的敌人）？简单做法是不保存，每次重新加载；复杂做法是每个地图维护一个状态结构体。

#### 例子

```c
// scene.h
#ifndef SCENE_H
#define SCENE_H

#include "multilayer_map.h"
#include "player.h"

#define MAX_PORTALS 32

typedef struct {
    int src_map_id;
    SDL_Rect src_rect;   // 触发区域（世界坐标）
    int dst_map_id;
    float dst_x, dst_y;  // 目标位置（世界坐标）
} Portal;

typedef enum {
    SCENE_NORMAL,
    SCENE_FADING_OUT,
    SCENE_FADING_IN
} SceneState;

typedef struct {
    MultiLayerMap map;
    int map_id;
    Portal portals[MAX_PORTALS];
    int portal_count;
    SceneState state;
    float fade_alpha;     // 0-255
    float fade_speed;     // 每秒变化量
    int pending_map_id;
    float pending_x, pending_y;
} SceneManager;

void scene_init(SceneManager* sm);
void scene_load(SceneManager* sm, SDL_Renderer* r, int map_id, float player_x, float player_y);
void scene_update(SceneManager* sm, Player* p, float dt);
void scene_draw_overlay(SceneManager* sm, SDL_Renderer* r);  // 绘制淡入淡出
void scene_add_portal(SceneManager* sm, Portal portal);

#endif
```

```c
// scene.c
#include "scene.h"
#include <SDL2/SDL_image.h>
#include <stdio.h>

void scene_init(SceneManager* sm) {
    memset(sm, 0, sizeof(*sm));
    sm->state = SCENE_NORMAL;
    sm->fade_alpha = 0;
    sm->fade_speed = 600.0f;  // 0.4秒完成
}

void scene_load(SceneManager* sm, SDL_Renderer* r, int map_id,
                float player_x, float player_y) {
    // 释放旧地图
    mlmap_free(&sm->map);

    // 根据map_id加载新地图（实际项目用查表或配置文件）
    char tileset_path[256], ground_path[256];
    snprintf(tileset_path, sizeof(tileset_path), "assets/maps/map%d_tiles.png", map_id);
    snprintf(ground_path, sizeof(ground_path), "assets/maps/map%d_ground.csv", map_id);

    // 简化：直接加载
    // mlmap_load(&sm->map, r, tileset_path, ground_path, ...);
    sm->map_id = map_id;
    (void)player_x; (void)player_y;
}

void scene_add_portal(SceneManager* sm, Portal portal) {
    if (sm->portal_count < MAX_PORTALS) {
        sm->portals[sm->portal_count++] = portal;
    }
}

static void check_portals(SceneManager* sm, Player* p) {
    if (sm->state != SCENE_NORMAL) return;

    SDL_Rect player_box = player_get_hitbox(p);
    for (int i = 0; i < sm->portal_count; i++) {
        Portal* portal = &sm->portals[i];
        if (portal->src_map_id != sm->map_id) continue;

        // 检测玩家是否在传送门区域
        if (player_box.x < portal->src_rect.x + portal->src_rect.w &&
            player_box.x + player_box.w > portal->src_rect.x &&
            player_box.y < portal->src_rect.y + portal->src_rect.h &&
            player_box.y + player_box.h > portal->src_rect.y) {

            // 触发切换
            sm->pending_map_id = portal->dst_map_id;
            sm->pending_x = portal->dst_x;
            sm->pending_y = portal->dst_y;
            sm->state = SCENE_FADING_OUT;
            return;
        }
    }
}

void scene_update(SceneManager* sm, Player* p, float dt) {
    switch (sm->state) {
        case SCENE_NORMAL:
            check_portals(sm, p);
            break;

        case SCENE_FADING_OUT:
            sm->fade_alpha += sm->fade_speed * dt;
            if (sm->fade_alpha >= 255) {
                sm->fade_alpha = 255;
                // 实际切换地图
                scene_load(sm, NULL, sm->pending_map_id, sm->pending_x, sm->pending_y);
                p->x = sm->pending_x;
                p->y = sm->pending_y;
                sm->state = SCENE_FADING_IN;
            }
            break;

        case SCENE_FADING_IN:
            sm->fade_alpha -= sm->fade_speed * dt;
            if (sm->fade_alpha <= 0) {
                sm->fade_alpha = 0;
                sm->state = SCENE_NORMAL;
            }
            break;
    }
}

void scene_draw_overlay(SceneManager* sm, SDL_Renderer* r) {
    if (sm->fade_alpha > 0) {
        SDL_SetRenderDrawBlendMode(r, SDL_BLENDMODE_BLEND);
        SDL_SetRenderDrawColor(r, 0, 0, 0, (Uint8)sm->fade_alpha);
        SDL_RenderFillRect(r, NULL);  // 全屏
    }
}
```

#### 总结

- **传送门**：地图上特定区域，玩家进入触发场景切换
- **切换流程**：检测→淡出→加载新地图→设置玩家位置→淡入
- **淡入淡出**：全屏黑色矩形，alpha渐变，掩盖加载卡顿
- **地图状态**：简单做法每次重新加载，复杂做法维护状态结构体
- **传送门数据**：源地图、源区域、目标地图、目标位置
- **常见坑**：切换时未释放旧地图资源导致内存泄漏；淡入淡出期间未禁止玩家输入导致穿模

---

## 第六章 碰撞系统

> 没有碰撞，角色会穿墙、掉出地图。本章学习AABB碰撞检测、碰撞响应、优化策略，让你的游戏物理可信。

### 第19讲 AABB碰撞检测

#### 概念

**AABB（Axis-Aligned Bounding Box，轴对齐包围盒）** 是最简单的碰撞检测方法：用矩形包围物体，检测两个矩形是否相交。由于矩形不旋转（轴对齐），检测算法极其高效，是2D游戏最常用的碰撞方案。

#### 原理

**AABB相交判定**：

两个矩形A和B，当且仅当以下四个条件同时满足时相交：

```
A.x < B.x + B.w   (A左边在B右边左侧)
A.x + A.w > B.x   (A右边在B左边右侧)
A.y < B.y + B.h   (A上边在B下边上方)
A.y + A.h > B.y   (A下边在B上边下方)
```

等价于：A在B的左、右、上、下都不在外侧。

**为什么用AABB？**

1. **简单**：4次比较即可判定
2. **快速**：无三角函数、无平方根
3. **足够**：2D游戏大多数物体可视为矩形
4. **稳定**：不依赖旋转，无奇异情况

**AABB的局限**：

- 不支持旋转矩形（需用OBB或SAT）
- 不支持圆形、多边形（需专门算法）
- 精度有限（矩形包围圆形会有空隙）

对于类塞尔达游戏，AABB完全够用——角色、敌人、墙壁都是矩形。

**碰撞盒设计**：

物体的碰撞盒通常比渲染盒小且居中底部，让视觉更合理：

```
渲染盒（64×64）：
+--------+
|        |
|   头   |
|        |
|  +--+  |  碰撞盒（32×24，底部居中）
|  |  |  |
+--+--+--+
```

#### 例子

```c
// collision.h
#ifndef COLLISION_H
#define COLLISION_H

#include <SDL2/SDL.h>

// AABB相交检测
static inline int aabb_intersect(SDL_Rect a, SDL_Rect b) {
    return (a.x < b.x + b.w && a.x + a.w > b.x &&
            a.y < b.y + b.h && a.y + a.h > b.y);
}

// 点是否在矩形内
static inline int point_in_rect(int px, int py, SDL_Rect r) {
    return (px >= r.x && px < r.x + r.w && py >= r.y && py < r.y + r.h);
}

// 获取两个矩形的相交区域
static inline int aabb_get_overlap(SDL_Rect a, SDL_Rect b, SDL_Rect* overlap) {
    if (!aabb_intersect(a, b)) return 0;
    overlap->x = (a.x > b.x) ? a.x : b.x;
    overlap->y = (a.y > b.y) ? a.y : b.y;
    int right = (a.x + a.w < b.x + b.w) ? a.x + a.w : b.x + b.w;
    int bottom = (a.y + a.h < b.y + b.h) ? a.y + a.h : b.y + b.h;
    overlap->w = right - overlap->x;
    overlap->h = bottom - overlap->y;
    return 1;
}

#endif
```

**玩家与地图瓦片的碰撞检测**：

```c
// player_collision.c
#include "collision.h"
#include "tilemap.h"
#include "player.h"

// 检测玩家碰撞盒是否与地图中任何实心瓦片相交
int player_collides_with_map(Player* p, Tilemap* tm) {
    SDL_Rect hb = player_get_hitbox(p);

    // 计算碰撞盒覆盖的瓦片范围
    int start_tx = hb.x / TILE_SIZE;
    int end_tx = (hb.x + hb.w - 1) / TILE_SIZE;
    int start_ty = hb.y / TILE_SIZE;
    int end_ty = (hb.y + hb.h - 1) / TILE_SIZE;

    for (int ty = start_ty; ty <= end_ty; ty++) {
        for (int tx = start_tx; tx <= end_tx; tx++) {
            if (tilemap_is_solid(tm, tx, ty)) {
                SDL_Rect tile_rect = { tx * TILE_SIZE, ty * TILE_SIZE, TILE_SIZE, TILE_SIZE };
                if (aabb_intersect(hb, tile_rect)) {
                    return 1;
                }
            }
        }
    }
    return 0;
}
```

#### 总结

- **AABB判定**：4次比较，A在B的四个方向都不在外侧则相交
- **优势**：简单、快速、稳定，2D游戏首选
- **局限**：不支持旋转、圆形，精度有限
- **碰撞盒设计**：比渲染盒小，底部居中，手感更好
- **瓦片碰撞优化**：只检测碰撞盒覆盖的瓦片，而非全地图
- **常见坑**：碰撞盒和渲染盒混用导致视觉与判定不符；忘记处理边界瓦片导致角落穿模

---

### 第20讲 碰撞响应与分离

#### 概念

检测到碰撞只是第一步，更重要的是**响应**——把物体推回不碰撞的位置。本讲学习分离轴定理（SAT）的简化版、轴向分离、滑动碰撞，让角色能贴墙行走而不卡顿。

#### 原理

**问题：直接移动会穿墙**

如果先更新x和y，再检测碰撞，发现碰撞时不知道该退回x还是y。解决方案是**分轴移动**：

```
1. 先移动x，检测碰撞，若碰撞则退回x
2. 再移动y，检测碰撞，若碰撞则退回y
```

这样角色可以贴墙滑动——水平移动撞墙时x退回但y仍可移动，实现"贴墙走"。

**最小平移向量（MTV）**：

更精细的方法是计算把两个物体分离所需的最小平移向量。对于AABB，MTV就是重叠区域较小方向的偏移：

```c
if (overlap_w < overlap_h) {
    // x方向分离
    if (a.x < b.x) a.x -= overlap_w;
    else a.x += overlap_w;
} else {
    // y方向分离
    if (a.y < b.y) a.y -= overlap_h;
    else a.y += overlap_h;
}
```

**滑动碰撞**：

角色斜向移动撞墙时，不应该完全停下，而应该沿墙滑动。分轴移动天然实现这一点：

```
原位置(100, 100)，想移动到(110, 110)
1. 移动x到110，检测碰撞→撞墙，退回x=100
2. 移动y到110，检测碰撞→无碰撞，y=110
最终位置(100, 110)，沿墙滑动
```

#### 例子

```c
// player_movement.c
#include "collision.h"
#include "tilemap.h"
#include "player.h"

// 检测玩家在指定位置是否与地图碰撞
static int player_collides_at(Player* p, Tilemap* tm, float x, float y) {
    SDL_Rect hb = {
        (int)x + p->hitbox_offset_x,
        (int)y + p->hitbox_offset_y,
        p->hitbox_w,
        p->hitbox_h
    };

    int start_tx = hb.x / TILE_SIZE;
    int end_tx = (hb.x + hb.w - 1) / TILE_SIZE;
    int start_ty = hb.y / TILE_SIZE;
    int end_ty = (hb.y + hb.h - 1) / TILE_SIZE;

    for (int ty = start_ty; ty <= end_ty; ty++) {
        for (int tx = start_tx; tx <= end_tx; tx++) {
            if (tilemap_is_solid(tm, tx, ty)) {
                SDL_Rect tile_rect = { tx * TILE_SIZE, ty * TILE_SIZE, TILE_SIZE, TILE_SIZE };
                if (aabb_intersect(hb, tile_rect)) return 1;
            }
        }
    }
    return 0;
}

// 分轴移动：实现贴墙滑动
void player_move_with_collision(Player* p, Tilemap* tm, float dt) {
    float dx = p->vx * dt;
    float dy = p->vy * dt;

    // 1. 先移动x
    float new_x = p->x + dx;
    if (!player_collides_at(p, tm, new_x, p->y)) {
        p->x = new_x;
    } else {
        // x方向碰撞，尝试微调贴墙
        // 方法：逐步逼近，找到最接近墙的位置
        if (dx > 0) {
            // 向右移动撞墙，对齐到墙左边
            int tile_tx = ((int)(new_x + p->hitbox_offset_x + p->hitbox_w) / TILE_SIZE);
            p->x = tile_tx * TILE_SIZE - p->hitbox_offset_x - p->hitbox_w - 1;
        } else if (dx < 0) {
            // 向左移动撞墙，对齐到墙右边
            int tile_tx = ((int)(new_x + p->hitbox_offset_x) / TILE_SIZE);
            p->x = (tile_tx + 1) * TILE_SIZE - p->hitbox_offset_x;
        }
    }

    // 2. 再移动y
    float new_y = p->y + dy;
    if (!player_collides_at(p, tm, p->x, new_y)) {
        p->y = new_y;
    } else {
        if (dy > 0) {
            // 向下移动撞墙，对齐到墙上方
            int tile_ty = ((int)(new_y + p->hitbox_offset_y + p->hitbox_h) / TILE_SIZE);
            p->y = tile_ty * TILE_SIZE - p->hitbox_offset_y - p->hitbox_h - 1;
        } else if (dy < 0) {
            // 向上移动撞墙，对齐到墙下方
            int tile_ty = ((int)(new_y + p->hitbox_offset_y) / TILE_SIZE);
            p->y = (tile_ty + 1) * TILE_SIZE - p->hitbox_offset_y;
        }
    }
}
```

**实体间碰撞响应（推开效果）**：

```c
// entity_collision.c
#include "collision.h"

// 两个实体碰撞时，相互推开
void entity_separate(Entity* a, Entity* b) {
    SDL_Rect a_box = entity_get_hitbox(a);
    SDL_Rect b_box = entity_get_hitbox(b);

    SDL_Rect overlap;
    if (!aabb_get_overlap(a_box, b_box, &overlap)) return;

    // 选择重叠较小的轴分离
    if (overlap.w < overlap.h) {
        // x方向分离
        float push = overlap.w / 2.0f;
        if (a_box.x < b_box.x) {
            a->x -= push;
            b->x += push;
        } else {
            a->x += push;
            b->x -= push;
        }
    } else {
        // y方向分离
        float push = overlap.h / 2.0f;
        if (a_box.y < b_box.y) {
            a->y -= push;
            b->y += push;
        } else {
            a->y += push;
            b->y -= push;
        }
    }
}
```

#### 总结

- **分轴移动**：先x后y（或先y后x），各自检测碰撞，实现贴墙滑动
- **MTV分离**：计算最小平移向量，把物体推回不碰撞位置
- **贴墙对齐**：碰撞时对齐到瓦片边界，避免缝隙
- **实体间分离**：两实体碰撞时，沿重叠较小轴各退一半
- **滑动碰撞**：斜向移动撞墙时，沿墙滑动而非完全停下
- **常见坑**：不分轴导致斜向移动卡墙；碰撞后不对齐瓦片导致1像素缝隙；高速移动可能穿透（需连续碰撞检测）

---

### 第21讲 碰撞优化策略

#### 概念

当地图很大、实体很多时，O(n²)的碰撞检测会成为性能瓶颈。本讲学习空间分区优化：均匀网格、四叉树、碰撞层，让碰撞检测从O(n²)降到接近O(n)。

#### 原理

**问题：暴力检测的代价**

N个实体两两检测需要N*(N-1)/2次，100个实体就是4950次，1000个实体近50万次。每帧50万次AABB检测在现代CPU上虽可行，但加上其他逻辑会卡顿。

**优化思路：空间分区**

只检测"可能碰撞"的实体对。如果两个实体相距很远，根本不可能碰撞，跳过检测。

**均匀网格（Uniform Grid）**：

把世界划分成等大小的格子，每个实体注册到所在格子。检测时只检测同格子和相邻格子的实体：

```
格子大小 = 最大实体尺寸
实体A在格子(3,4)，只检测(2,3)(2,4)(2,5)(3,3)(3,4)(3,5)(4,3)(4,4)(4,5)的实体
```

适合实体大小相近的场景。

**四叉树（Quadtree）**：

递归地把空间分成四象限，实体少的区域不再细分。查询时只遍历相关象限。适合实体分布不均的场景。

**碰撞层（Collision Layer）**：

不同类型的实体分到不同层，只检测需要交互的层对：

```
玩家层 vs 敌人层：检测
玩家层 vs 子弹层：检测
玩家层 vs 玩家层：不检测（单人游戏）
敌人层 vs 敌人层：不检测（避免互相推开）
```

用位掩码表示层间关系：

```c
#define LAYER_PLAYER    (1 << 0)
#define LAYER_ENEMY     (1 << 1)
#define LAYER_BULLET    (1 << 2)
#define LAYER_WALL      (1 << 3)

// 玩家与敌人、子弹、墙碰撞
int player_collides_with = LAYER_ENEMY | LAYER_BULLET | LAYER_WALL;
```

#### 例子

**均匀网格实现**：

```c
// spatial_grid.h
#ifndef SPATIAL_GRID_H
#define SPATIAL_GRID_H

#include <SDL2/SDL.h>

#define MAX_ENTITIES_PER_CELL 32
#define GRID_CELL_SIZE 128

typedef struct Entity Entity;

typedef struct {
    Entity* entities[MAX_ENTITIES_PER_CELL];
    int count;
} GridCell;

typedef struct {
    GridCell* cells;
    int cols, rows;
    int world_width, world_height;
} SpatialGrid;

void grid_init(SpatialGrid* g, int world_w, int world_h);
void grid_clear(SpatialGrid* g);
void grid_insert(SpatialGrid* g, Entity* e, SDL_Rect box);
int grid_query(SpatialGrid* g, SDL_Rect box, Entity** results, int max_results);

#endif
```

```c
// spatial_grid.c
#include "spatial_grid.h"
#include <stdlib.h>
#include <string.h>

void grid_init(SpatialGrid* g, int world_w, int world_h) {
    g->world_width = world_w;
    g->world_height = world_h;
    g->cols = (world_w + GRID_CELL_SIZE - 1) / GRID_CELL_SIZE;
    g->rows = (world_h + GRID_CELL_SIZE - 1) / GRID_CELL_SIZE;
    g->cells = calloc(g->cols * g->rows, sizeof(GridCell));
}

void grid_clear(SpatialGrid* g) {
    for (int i = 0; i < g->cols * g->rows; i++) {
        g->cells[i].count = 0;
    }
}

void grid_insert(SpatialGrid* g, Entity* e, SDL_Rect box) {
    int start_tx = box.x / GRID_CELL_SIZE;
    int end_tx = (box.x + box.w) / GRID_CELL_SIZE;
    int start_ty = box.y / GRID_CELL_SIZE;
    int end_ty = (box.y + box.h) / GRID_CELL_SIZE;

    for (int ty = start_ty; ty <= end_ty; ty++) {
        for (int tx = start_tx; tx <= end_tx; tx++) {
            if (tx < 0 || tx >= g->cols || ty < 0 || ty >= g->rows) continue;
            GridCell* cell = &g->cells[ty * g->cols + tx];
            if (cell->count < MAX_ENTITIES_PER_CELL) {
                cell->entities[cell->count++] = e;
            }
        }
    }
}

int grid_query(SpatialGrid* g, SDL_Rect box, Entity** results, int max_results) {
    int result_count = 0;
    int start_tx = box.x / GRID_CELL_SIZE;
    int end_tx = (box.x + box.w) / GRID_CELL_SIZE;
    int start_ty = box.y / GRID_CELL_SIZE;
    int end_ty = (box.y + box.h) / GRID_CELL_SIZE;

    for (int ty = start_ty; ty <= end_ty; ty++) {
        for (int tx = start_tx; tx <= end_tx; tx++) {
            if (tx < 0 || tx >= g->cols || ty < 0 || ty >= g->rows) continue;
            GridCell* cell = &g->cells[ty * g->cols + tx];
            for (int i = 0; i < cell->count; i++) {
                // 去重
                int found = 0;
                for (int j = 0; j < result_count; j++) {
                    if (results[j] == cell->entities[i]) { found = 1; break; }
                }
                if (!found && result_count < max_results) {
                    results[result_count++] = cell->entities[i];
                }
            }
        }
    }
    return result_count;
}
```

**使用示例**：

```c
// 每帧碰撞检测流程
void game_update_collisions(Game* game, float dt) {
    // 1. 清空网格
    grid_clear(&game->grid);

    // 2. 插入所有实体
    grid_insert(&game->grid, (Entity*)&game->player, player_get_hitbox(&game->player));
    for (int i = 0; i < game->enemy_count; i++) {
        if (enemy_is_alive(&game->enemies[i])) {
            grid_insert(&game->grid, (Entity*)&game->enemies[i],
                        enemy_get_hitbox(&game->enemies[i]));
        }
    }

    // 3. 查询玩家附近的实体，只与这些实体检测碰撞
    Entity* nearby[64];
    int count = grid_query(&game->grid, player_get_hitbox(&game->player),
                           nearby, 64);
    for (int i = 0; i < count; i++) {
        // 检测玩家与nearby[i]的碰撞
        // ...
    }
}
```

#### 总结

- **暴力检测O(n²)**：实体少时可用，多了必卡
- **均匀网格**：等大小格子，只检测同格和相邻格，适合实体大小相近
- **四叉树**：递归分区，适合实体分布不均
- **碰撞层**：位掩码表示层间关系，跳过不需要的检测
- **网格大小**：通常设为最大实体尺寸，保证不会跨多格
- **常见坑**：网格太小导致实体跨多格，查询效率下降；忘记每帧clear导致旧数据残留

---

## 第七章 敌人与战斗

> 没有敌人的游戏不是冒险。本章学习敌人结构、AI状态机、战斗系统、波次生成，让你的世界充满挑战。

### 第22讲 敌人基础结构

#### 概念

**敌人（Enemy）** 是游戏中的非玩家角色（NPC），具有AI行为、生命值、攻击力。本讲设计一个通用的敌人结构，支持多种敌人类型（史莱姆、蝙蝠、骷髅），为后续AI和战斗打好基础。

#### 原理

**敌人数据结构设计**：

敌人和玩家结构相似，但多了AI相关字段：

```c
typedef struct {
    // 基础属性（与玩家相同）
    float x, y;
    float vx, vy;
    int width, height;
    int hitbox_offset_x, hitbox_offset_y, hitbox_w, hitbox_h;
    Direction dir;
    int hp, max_hp;
    int attack_power;
    float speed;

    // 敌人特有
    int enemy_type;       // 敌人类型ID
    int state;            // AI状态
    int flags;
    float state_timer;    // 状态计时器
    float invincible_timer;
    float knockback_timer;
    float knockback_vx, knockback_vy;

    // AI参数
    float detect_range;   // 侦测范围
    float attack_range;   // 攻击范围
    float attack_cooldown; // 攻击冷却
    float attack_timer;   // 当前冷却剩余

    // 动画
    Animator animator;
} Enemy;
```

**敌人类型**：

用枚举或类型ID区分不同敌人。每种敌人有不同属性、AI、动画：

```c
typedef enum {
    ENEMY_SLIME = 0,    // 史莱姆：缓慢、近战
    ENEMY_BAT,          // 蝙蝠：快速、飞行、近战
    ENEMY_SKELETON,     // 骷髅：中速、近战+远程
    ENEMY_ARCHER,       // 弓箭手：远程
    ENEMY_BOSS          // BOSS：多阶段
} EnemyType;
```

**敌人配置表**：

用数据驱动设计，每种敌人的属性存在配置表（数组或文件）中，避免硬编码：

```c
typedef struct {
    int max_hp;
    int attack_power;
    float speed;
    float detect_range;
    float attack_range;
    float attack_cooldown;
    // 动画信息...
} EnemyConfig;

static EnemyConfig enemy_configs[] = {
    [ENEMY_SLIME]    = { 3, 1, 60,  150, 32, 1.5f },
    [ENEMY_BAT]      = { 2, 1, 120, 200, 28, 1.0f },
    [ENEMY_SKELETON] = { 5, 2, 80,  180, 40, 1.2f },
    [ENEMY_ARCHER]   = { 3, 2, 50,  300, 0,  2.0f },
};
```

#### 例子

```c
// enemy.h
#ifndef ENEMY_H
#define ENEMY_H

#include "animation.h"
#include "camera.h"
#include "player.h"

typedef enum {
    ENEMY_SLIME = 0,
    ENEMY_BAT,
    ENEMY_SKELETON,
    ENEMY_ARCHER,
    ENEMY_TYPE_COUNT
} EnemyType;

typedef enum {
    ENEMY_STATE_IDLE = 0,
    ENEMY_STATE_PATROL,    // 巡逻
    ENEMY_STATE_CHASE,     // 追击
    ENEMY_STATE_ATTACK,    // 攻击
    ENEMY_STATE_HURT,      // 受伤
    ENEMY_STATE_DIE        // 死亡
} EnemyState;

#define ENEMY_FLAG_INVINCIBLE  (1 << 0)
#define ENEMY_FLAG_ATTACKING   (1 << 1)
#define ENEMY_FLAG_FLYING      (1 << 2)  // 飞行（不受地形碰撞）

typedef struct {
    float x, y;
    float vx, vy;
    int width, height;
    int hitbox_offset_x, hitbox_offset_y, hitbox_w, hitbox_h;

    EnemyType type;
    EnemyState state;
    int flags;

    int max_hp, hp;
    int attack_power;
    float speed;

    float state_timer;
    float invincible_timer;
    float knockback_timer;
    float knockback_vx, knockback_vy;

    float detect_range;
    float attack_range;
    float attack_cooldown;
    float attack_timer;
    int attack_hit;

    Direction dir;
    Animator animator;

    // 巡逻相关
    float patrol_origin_x, patrol_origin_y;
    float patrol_radius;
    float patrol_target_x, patrol_target_y;
} Enemy;

void enemy_init(Enemy* e, EnemyType type, SpriteSheet* sheets[], float x, float y);
void enemy_update(Enemy* e, Player* p, float dt);
void enemy_draw(Enemy* e, SDL_Renderer* r, Camera* cam);
void enemy_take_damage(Enemy* e, int damage);
void enemy_apply_knockback(Enemy* e, float vx, float vy, float duration);
SDL_Rect enemy_get_hitbox(Enemy* e);
SDL_Rect enemy_get_attack_box(Enemy* e);
int enemy_is_alive(Enemy* e);
int enemy_is_attacking(Enemy* e);

#define enemy_has_flag(e, f)  ((e)->flags & (f))
#define enemy_set_flag(e, f)  ((e)->flags |= (f))
#define enemy_clear_flag(e, f) ((e)->flags &= ~(f))

#endif
```

```c
// enemy.c
#include "enemy.h"
#include <string.h>
#include <math.h>

typedef struct {
    int max_hp;
    int attack_power;
    float speed;
    float detect_range;
    float attack_range;
    float attack_cooldown;
    int flying;
} EnemyConfig;

static EnemyConfig enemy_configs[ENEMY_TYPE_COUNT] = {
    [ENEMY_SLIME]    = { 3, 1, 60,  150, 32, 1.5f, 0 },
    [ENEMY_BAT]      = { 2, 1, 120, 200, 28, 1.0f, 1 },
    [ENEMY_SKELETON] = { 5, 2, 80,  180, 40, 1.2f, 0 },
    [ENEMY_ARCHER]   = { 3, 2, 50,  300, 0,  2.0f, 0 },
};

void enemy_init(Enemy* e, EnemyType type, SpriteSheet* sheets[], float x, float y) {
    memset(e, 0, sizeof(*e));
    e->type = type;
    e->x = x;
    e->y = y;
    e->width = 48;
    e->height = 48;
    e->hitbox_offset_x = 8;
    e->hitbox_offset_y = 24;
    e->hitbox_w = 32;
    e->hitbox_h = 24;
    e->dir = DIR_DOWN;
    e->state = ENEMY_STATE_IDLE;

    EnemyConfig* cfg = &enemy_configs[type];
    e->max_hp = cfg->max_hp;
    e->hp = cfg->max_hp;
    e->attack_power = cfg->attack_power;
    e->speed = cfg->speed;
    e->detect_range = cfg->detect_range;
    e->attack_range = cfg->attack_range;
    e->attack_cooldown = cfg->attack_cooldown;
    if (cfg->flying) enemy_set_flag(e, ENEMY_FLAG_FLYING);

    e->patrol_origin_x = x;
    e->patrol_origin_y = y;
    e->patrol_radius = 100;

    animator_init(&e->animator, sheets[type]);
}

int enemy_is_alive(Enemy* e) {
    return e->state != ENEMY_STATE_DIE && e->hp > 0;
}

int enemy_is_attacking(Enemy* e) {
    return e->state == ENEMY_STATE_ATTACK && !e->attack_hit;
}

SDL_Rect enemy_get_hitbox(Enemy* e) {
    SDL_Rect r = {
        (int)e->x + e->hitbox_offset_x,
        (int)e->y + e->hitbox_offset_y,
        e->hitbox_w, e->hitbox_h
    };
    return r;
}

SDL_Rect enemy_get_attack_box(Enemy* e) {
    SDL_Rect hb = enemy_get_hitbox(e);
    int range = 24;
    SDL_Rect attack;
    switch (e->dir) {
        case DIR_DOWN:  attack = (SDL_Rect){hb.x, hb.y + hb.h, hb.w, range}; break;
        case DIR_UP:    attack = (SDL_Rect){hb.x, hb.y - range, hb.w, range}; break;
        case DIR_LEFT:  attack = (SDL_Rect){hb.x - range, hb.y, range, hb.h}; break;
        case DIR_RIGHT: attack = (SDL_Rect){hb.x + hb.w, hb.y, range, hb.h}; break;
    }
    return attack;
}

void enemy_take_damage(Enemy* e, int damage) {
    if (!enemy_is_alive(e)) return;
    if (enemy_has_flag(e, ENEMY_FLAG_INVINCIBLE)) return;

    e->hp -= damage;
    enemy_set_flag(e, ENEMY_FLAG_INVINCIBLE);
    e->invincible_timer = 0.5f;

    if (e->hp <= 0) {
        e->hp = 0;
        e->state = ENEMY_STATE_DIE;
        e->state_timer = 0;
    } else {
        e->state = ENEMY_STATE_HURT;
        e->state_timer = 0.3f;
    }
}

void enemy_apply_knockback(Enemy* e, float vx, float vy, float duration) {
    e->knockback_vx = vx;
    e->knockback_vy = vy;
    e->knockback_timer = duration;
}

void enemy_draw(Enemy* e, SDL_Renderer* r, Camera* cam) {
    if (!enemy_is_alive(e) && e->state != ENEMY_STATE_DIE) return;

    int sx, sy;
    camera_world_to_screen(cam, e->x, e->y, &sx, &sy);

    // 无敌闪烁
    if (enemy_has_flag(e, ENEMY_FLAG_INVINCIBLE)) {
        int blink = ((int)(e->invincible_timer * 15)) % 2;
        if (blink) return;
    }

    animator_draw(&e->animator, r, sx, sy, e->width, e->height);
}
```

#### 总结

- **敌人结构**：基础属性 + AI状态 + 动画，与玩家相似但多了AI字段
- **敌人类型**：枚举区分，每种类型有不同属性和行为
- **数据驱动**：用配置表存储各类型属性，避免硬编码，方便调整数值
- **飞行标志**：飞行敌人不受地形碰撞，简化AI
- **巡逻原点**：记录敌人初始位置，巡逻时围绕此点活动
- **常见坑**：所有敌人共用一个动画器导致动画串台；忘记memset导致字段未初始化

---

### 第23讲 简单AI与状态机

#### 概念

**敌人AI（Artificial Intelligence）** 让敌人有自主行为。本讲实现一个基于状态机的简单AI：巡逻→发现玩家→追击→攻击→受伤→恢复。这是动作游戏敌人AI的经典模式。

#### 原理

**AI状态机**：

```
IDLE（空闲）
  ↓ 经过随机时间或玩家进入侦测范围
PATROL（巡逻）──随机移动──┐
  ↓ 玩家进入侦测范围        │
CHASE（追击）──────────────┘
  ↓ 玩家进入攻击范围
ATTACK（攻击）
  ↓ 攻击结束
CHASE（追击）
  ↓ 受伤
HURT（受伤）
  ↓ 受伤结束
CHASE（追击）
  ↓ 血量为0
DIE（死亡）
```

**状态转换条件**：

- IDLE → PATROL：随机时间到（如2-5秒）
- 任意 → CHASE：玩家进入侦测范围（detect_range）
- CHASE → ATTACK：玩家进入攻击范围（attack_range）
- ATTACK → CHASE：攻击动画结束
- 任意 → HURT：受到伤害
- HURT → CHASE：受伤硬直结束
- 任意 → DIE：血量为0

**侦测玩家**：

计算敌人到玩家的距离，小于detect_range则发现玩家：

```c
float dx = player->x - enemy->x;
float dy = player->y - enemy->y;
float dist = sqrtf(dx*dx + dy*dy);
if (dist < enemy->detect_range) {
    // 发现玩家
}
```

**追击移动**：

朝玩家方向移动，归一化方向向量：

```c
float dx = player->x - enemy->x;
float dy = player->y - enemy->y;
float len = sqrtf(dx*dx + dy*dy);
if (len > 0) {
    enemy->vx = (dx / len) * enemy->speed;
    enemy->vy = (dy / len) * enemy->speed;
}
```

#### 例子

```c
// enemy_ai.c
#include "enemy.h"
#include "player.h"
#include <math.h>
#include <stdlib.h>
#include <time.h>

static float distance(float x1, float y1, float x2, float y2) {
    float dx = x2 - x1;
    float dy = y2 - y1;
    return sqrtf(dx*dx + dy*dy);
}

static void pick_patrol_target(Enemy* e) {
    float angle = ((float)rand() / RAND_MAX) * 2.0f * 3.14159f;
    float r = ((float)rand() / RAND_MAX) * e->patrol_radius;
    e->patrol_target_x = e->patrol_origin_x + cosf(angle) * r;
    e->patrol_target_y = e->patrol_origin_y + sinf(angle) * r;
}

static void move_toward(Enemy* e, float tx, float ty, float dt) {
    float dx = tx - e->x;
    float dy = ty - e->y;
    float len = sqrtf(dx*dx + dy*dy);
    if (len < 1.0f) {
        e->vx = 0;
        e->vy = 0;
        return;
    }
    dx /= len;
    dy /= len;
    e->vx = dx * e->speed;
    e->vy = dy * e->speed;

    // 更新方向
    if (fabsf(dx) > fabsf(dy)) {
        e->dir = (dx > 0) ? DIR_RIGHT : DIR_LEFT;
    } else {
        e->dir = (dy > 0) ? DIR_DOWN : DIR_UP;
    }
}

void enemy_update(Enemy* e, Player* p, float dt) {
    // 衰减计时器
    if (e->invincible_timer > 0) {
        e->invincible_timer -= dt;
        if (e->invincible_timer <= 0) {
            enemy_clear_flag(e, ENEMY_FLAG_INVINCIBLE);
        }
    }
    if (e->attack_timer > 0) e->attack_timer -= dt;

    // 击退处理
    if (e->knockback_timer > 0) {
        e->knockback_timer -= dt;
        e->x += e->knockback_vx * dt;
        e->y += e->knockback_vy * dt;
        // 击退中不执行AI
        animator_update(&e->animator, dt);
        return;
    }

    // 死亡状态
    if (e->state == ENEMY_STATE_DIE) {
        e->state_timer += dt;
        animator_set_state(&e->animator, ANIM_DIE, e->dir);
        animator_update(&e->animator, dt);
        return;
    }

    // 受伤硬直
    if (e->state == ENEMY_STATE_HURT) {
        e->state_timer -= dt;
        if (e->state_timer <= 0) {
            e->state = ENEMY_STATE_CHASE;
        }
        animator_set_state(&e->animator, ANIM_HURT, e->dir);
        animator_update(&e->animator, dt);
        return;
    }

    // 计算与玩家距离
    float dist_to_player = distance(e->x, e->y, p->x, p->y);

    // 状态机
    switch (e->state) {
        case ENEMY_STATE_IDLE:
            animator_set_state(&e->animator, ANIM_IDLE, e->dir);
            e->state_timer -= dt;
            if (e->state_timer <= 0) {
                e->state = ENEMY_STATE_PATROL;
                pick_patrol_target(e);
            }
            if (dist_to_player < e->detect_range) {
                e->state = ENEMY_STATE_CHASE;
            }
            break;

        case ENEMY_STATE_PATROL:
            animator_set_state(&e->animator, ANIM_WALK, e->dir);
            move_toward(e, e->patrol_target_x, e->patrol_target_y, dt);
            e->x += e->vx * dt;
            e->y += e->vy * dt;

            // 到达目标或超时
            if (distance(e->x, e->y, e->patrol_target_x, e->patrol_target_y) < 5.0f) {
                e->state = ENEMY_STATE_IDLE;
                e->state_timer = 2.0f + ((float)rand() / RAND_MAX) * 3.0f;
            }
            if (dist_to_player < e->detect_range) {
                e->state = ENEMY_STATE_CHASE;
            }
            break;

        case ENEMY_STATE_CHASE:
            animator_set_state(&e->animator, ANIM_WALK, e->dir);
            if (dist_to_player < e->attack_range && e->attack_timer <= 0) {
                e->state = ENEMY_STATE_ATTACK;
                e->state_timer = 0.4f;  // 攻击动画时长
                e->attack_hit = 0;
                enemy_set_flag(e, ENEMY_FLAG_ATTACKING);
            } else if (dist_to_player > e->detect_range * 1.5f) {
                // 玩家逃脱，回到巡逻
                e->state = ENEMY_STATE_PATROL;
                pick_patrol_target(e);
            } else {
                move_toward(e, p->x, p->y, dt);
                e->x += e->vx * dt;
                e->y += e->vy * dt;
            }
            break;

        case ENEMY_STATE_ATTACK:
            animator_set_state(&e->animator, ANIM_ATTACK, e->dir);
            e->state_timer -= dt;
            if (e->state_timer <= 0) {
                e->state = ENEMY_STATE_CHASE;
                e->attack_timer = e->attack_cooldown;
                enemy_clear_flag(e, ENEMY_FLAG_ATTACKING);
            }
            break;

        default:
            break;
    }

    animator_update(&e->animator, dt);
}
```

#### 总结

- **AI状态机**：IDLE→PATROL→CHASE→ATTACK→HURT→DIE，状态间有明确转换条件
- **侦测玩家**：距离小于detect_range则发现
- **追击移动**：归一化方向向量乘以速度
- **巡逻**：在原点周围随机选点，到达后回到IDLE
- **攻击冷却**：attack_timer防止连续攻击
- **常见坑**：忘记归一化导致对角线追击速度更快；状态转换时未重置计时器导致行为异常

---

### 第24讲 战斗系统与伤害计算

#### 概念

**战斗系统（Combat System）** 管理玩家与敌人之间的伤害交互。本讲整合攻击判定、伤害计算、击退、死亡掉落，构建完整的战斗循环。

#### 原理

**伤害计算公式**：

简单版：`damage = attacker.attack_power`

进阶版（含防御、暴击）：
```c
damage = attacker.attack_power - defender.defense;
if (damage < 1) damage = 1;  // 最少1点伤害
if (rand() % 100 < crit_rate) {
    damage *= 2;  // 暴击
}
```

**伤害数字飘字**：

命中时在敌人头顶显示伤害数字，向上飘动并淡出，增强反馈：

```c
typedef struct {
    float x, y;
    float vy;
    int damage;
    float lifetime;
    int color;  // 0=白色, 1=红色(暴击)
} DamageNumber;
```

**死亡掉落**：

敌人死亡时随机掉落物品（金币、心、药水）：

```c
typedef enum {
    DROP_NONE = 0,
    DROP_COIN,
    DROP_HEART,
    DROP_POTION
} DropType;

DropType enemy_get_drop(EnemyType type) {
    int r = rand() % 100;
    if (r < 30) return DROP_COIN;      // 30%金币
    if (r < 40) return DROP_HEART;     // 10%心
    if (r < 45) return DROP_POTION;    // 5%药水
    return DROP_NONE;                  // 50%无掉落
}
```

**连击与硬直**：

受伤后短暂硬直（无法行动），硬直期间可被再次攻击但伤害减半（防连死）。

#### 例子

```c
// damage_number.h
#ifndef DAMAGE_NUMBER_H
#define DAMAGE_NUMBER_H

#include <SDL2/SDL.h>
#include <SDL2/SDL_ttf.h>

#define MAX_DAMAGE_NUMBERS 64

typedef struct {
    float x, y;
    float vy;
    int damage;
    float lifetime;
    float max_lifetime;
    int crit;
    int active;
} DamageNumber;

typedef struct {
    DamageNumber numbers[MAX_DAMAGE_NUMBERS];
    TTF_Font* font;
} DamageNumberSystem;

void dmg_num_init(DamageNumberSystem* s, TTF_Font* font);
void dmg_num_spawn(DamageNumberSystem* s, float x, float y, int damage, int crit);
void dmg_num_update(DamageNumberSystem* s, float dt);
void dmg_num_draw(DamageNumberSystem* s, SDL_Renderer* r, Camera* cam);

#endif
```

```c
// damage_number.c
#include "damage_number.h"
#include <string.h>
#include <stdio.h>

void dmg_num_init(DamageNumberSystem* s, TTF_Font* font) {
    memset(s->numbers, 0, sizeof(s->numbers));
    s->font = font;
}

void dmg_num_spawn(DamageNumberSystem* s, float x, float y, int damage, int crit) {
    for (int i = 0; i < MAX_DAMAGE_NUMBERS; i++) {
        if (!s->numbers[i].active) {
            s->numbers[i].x = x;
            s->numbers[i].y = y;
            s->numbers[i].vy = -60.0f;  // 向上飘
            s->numbers[i].damage = damage;
            s->numbers[i].lifetime = 0.8f;
            s->numbers[i].max_lifetime = 0.8f;
            s->numbers[i].crit = crit;
            s->numbers[i].active = 1;
            return;
        }
    }
}

void dmg_num_update(DamageNumberSystem* s, float dt) {
    for (int i = 0; i < MAX_DAMAGE_NUMBERS; i++) {
        if (s->numbers[i].active) {
            s->numbers[i].y += s->numbers[i].vy * dt;
            s->numbers[i].lifetime -= dt;
            if (s->numbers[i].lifetime <= 0) {
                s->numbers[i].active = 0;
            }
        }
    }
}

void dmg_num_draw(DamageNumberSystem* s, SDL_Renderer* r, Camera* cam) {
    for (int i = 0; i < MAX_DAMAGE_NUMBERS; i++) {
        DamageNumber* n = &s->numbers[i];
        if (!n->active) continue;

        int sx, sy;
        camera_world_to_screen(cam, n->x, n->y, &sx, &sy);

        // alpha随生命周期衰减
        Uint8 alpha = (Uint8)(255 * (n->lifetime / n->max_lifetime));

        char text[16];
        snprintf(text, sizeof(text), "%d", n->damage);

        SDL_Color color = n->crit ?
            (SDL_Color){255, 100, 100, alpha} :
            (SDL_Color){255, 255, 255, alpha};

        SDL_Surface* surface = TTF_RenderText_Blended(s->font, text, color);
        if (surface) {
            SDL_Texture* tex = SDL_CreateTextureFromSurface(r, surface);
            SDL_Rect dst = {sx, sy, surface->w, surface->h};
            SDL_RenderCopy(r, tex, NULL, &dst);
            SDL_DestroyTexture(tex);
            SDL_FreeSurface(surface);
        }
    }
}
```

**完整战斗流程**：

```c
// combat_system.c
#include "combat.h"
#include "enemy.h"
#include "player.h"
#include "damage_number.h"
#include <stdlib.h>

typedef enum {
    DROP_NONE = 0,
    DROP_COIN,
    DROP_HEART,
    DROP_POTION
} DropType;

static DropType enemy_get_drop(EnemyType type) {
    int r = rand() % 100;
    if (r < 30) return DROP_COIN;
    if (r < 40) return DROP_HEART;
    if (r < 45) return DROP_POTION;
    (void)type;
    return DROP_NONE;
}

// 玩家攻击敌人
void combat_player_attack_enemies(Player* p, Enemy* enemies, int count,
                                   DamageNumberSystem* dns) {
    if (!player_has_flag(p, PLAYER_FLAG_ATTACKING)) return;
    if (p->attack_hit) return;
    if (p->animator.current_frame < 1) return;  // 攻击动画特定帧

    SDL_Rect attack_box = player_get_attack_box(p);

    for (int i = 0; i < count; i++) {
        Enemy* e = &enemies[i];
        if (!enemy_is_alive(e)) continue;

        SDL_Rect enemy_box = enemy_get_hitbox(e);
        if (aabb_intersect(attack_box, enemy_box)) {
            // 计算伤害
            int damage = p->attack_power;
            int crit = (rand() % 100 < 20);  // 20%暴击
            if (crit) damage *= 2;

            enemy_take_damage(e, damage);

            // 击退
            float dx = e->x - p->x;
            float dy = e->y - p->y;
            float len = sqrtf(dx*dx + dy*dy);
            if (len > 0) { dx /= len; dy /= len; }
            enemy_apply_knockback(e, dx * 200, dy * 200, 0.2f);

            // 飘字
            dmg_num_spawn(dns, e->x + e->width/2, e->y, damage, crit);

            p->attack_hit = 1;

            // 死亡掉落
            if (!enemy_is_alive(e)) {
                DropType drop = enemy_get_drop(e->type);
                if (drop != DROP_NONE) {
                    // 在敌人位置生成掉落物
                    // spawn_item(drop, e->x, e->y);
                }
            }
            break;
        }
    }
}

// 敌人攻击玩家
void combat_enemy_attack_player(Enemy* e, Player* p, DamageNumberSystem* dns) {
    if (!enemy_is_attacking(e)) return;
    if (e->animator.current_frame < 1) return;

    SDL_Rect enemy_attack = enemy_get_attack_box(e);
    SDL_Rect player_box = player_get_hitbox(p);

    if (aabb_intersect(enemy_attack, player_box)) {
        int damage = e->attack_power;
        player_take_damage(p, damage);

        // 击退玩家
        float dx = p->x - e->x;
        float dy = p->y - e->y;
        float len = sqrtf(dx*dx + dy*dy);
        if (len > 0) { dx /= len; dy /= len; }
        p->x += dx * 30;
        p->y += dy * 30;

        dmg_num_spawn(dns, p->x + p->width/2, p->y, damage, 0);
        e->attack_hit = 1;
    }
}
```

#### 总结

- **伤害计算**：基础攻击力，可扩展防御、暴击、元素抗性
- **飘字系统**：命中时显示伤害数字，向上飘动淡出，增强反馈
- **死亡掉落**：敌人死亡随机掉落物品，用概率表控制
- **击退**：命中后施加速度，增加打击感
- **暴击**：随机概率造成双倍伤害，红色飘字区分
- **常见坑**：飘字未用对象池导致频繁malloc；掉落物未清理导致内存泄漏；暴击概率过高破坏平衡

---

### 第25讲 敌人生成与波次系统

#### 概念

**波次系统（Wave System）** 用于地下城、竞技场等场景：玩家击败一波敌人后，下一波刷新，难度递增。本讲实现敌人生成器、波次配置、难度曲线。

#### 原理

**敌人生成器（Spawner）**：

在地图上放置生成点，按规则生成敌人：

```c
typedef struct {
    float x, y;           // 生成位置
    EnemyType type;       // 敌人类型
    int max_alive;        // 同时存活上限
    float spawn_interval; // 生成间隔
    float spawn_timer;    // 当前计时
    int total_spawned;    // 已生成总数
    int max_total;        // 总生成上限（-1无限）
} EnemySpawner;
```

**波次配置**：

每波定义敌人种类、数量、生成点：

```c
typedef struct {
    int wave_id;
    EnemyType enemy_type;
    int count;
    float spawn_delay;    // 波次开始后多久开始生成
    float spawn_interval; // 生成间隔
} WaveConfig;
```

**难度曲线**：

随波次推进，敌人属性增强：

```c
int hp_multiplier = 1 + (wave_number - 1) * 0.2f;  // 每波+20%血量
int damage_multiplier = 1 + (wave_number - 1) * 0.1f;
```

**波次切换条件**：

- 所有敌人被击败
- 所有生成器达到max_total
- 当前波次无存活敌人

#### 例子

```c
// wave_system.h
#ifndef WAVE_SYSTEM_H
#define WAVE_SYSTEM_H

#include "enemy.h"

#define MAX_WAVES 20
#define MAX_SPAWNERS_PER_WAVE 8

typedef struct {
    EnemyType type;
    int count;
    float spawn_delay;
    float spawn_interval;
    float x, y;  // 生成位置
} WaveConfig;

typedef struct {
    WaveConfig waves[MAX_WAVES];
    int wave_count;
    int current_wave;
    float wave_timer;
    int spawned_this_wave;
    int wave_active;
} WaveSystem;

void wave_system_init(WaveSystem* ws);
void wave_system_add_wave(WaveSystem* ws, WaveConfig config);
void wave_system_update(WaveSystem* ws, Enemy* enemies, int max_enemies,
                        int* enemy_count, SpriteSheet* sheets[], float dt);
int wave_system_is_complete(WaveSystem* ws, Enemy* enemies, int enemy_count);
int wave_system_all_done(WaveSystem* ws);

#endif
```

```c
// wave_system.c
#include "wave_system.h"
#include <string.h>

void wave_system_init(WaveSystem* ws) {
    memset(ws, 0, sizeof(*ws));
    ws->current_wave = -1;
}

void wave_system_add_wave(WaveSystem* ws, WaveConfig config) {
    if (ws->wave_count < MAX_WAVES) {
        ws->waves[ws->wave_count++] = config;
    }
}

static void spawn_enemy(Enemy* enemies, int max_enemies, int* count,
                        EnemyType type, float x, float y, SpriteSheet* sheets[]) {
    if (*count >= max_enemies) return;
    enemy_init(&enemies[*count], type, sheets, x, y);
    (*count)++;
}

void wave_system_update(WaveSystem* ws, Enemy* enemies, int max_enemies,
                        int* enemy_count, SpriteSheet* sheets[], float dt) {
    if (ws->current_wave >= ws->wave_count) return;  // 所有波次结束

    // 开始新波次
    if (!ws->wave_active && ws->current_wave < ws->wave_count - 1) {
        ws->current_wave++;
        ws->wave_timer = 0;
        ws->spawned_this_wave = 0;
        ws->wave_active = 1;
    }

    if (!ws->wave_active) return;

    WaveConfig* wave = &ws->waves[ws->current_wave];
    ws->wave_timer += dt;

    // 延迟过后开始生成
    if (ws->wave_timer >= wave->spawn_delay) {
        // 按间隔生成
        static float spawn_timer = 0;
        spawn_timer += dt;

        if (spawn_timer >= wave->spawn_interval && ws->spawned_this_wave < wave->count) {
            spawn_enemy(enemies, max_enemies, enemy_count, wave->type,
                       wave->x, wave->y, sheets);
            ws->spawned_this_wave++;
            spawn_timer = 0;
        }

        // 本波次生成完毕
        if (ws->spawned_this_wave >= wave->count) {
            ws->wave_active = 0;
        }
    }
}

int wave_system_is_complete(WaveSystem* ws, Enemy* enemies, int enemy_count) {
    if (ws->current_wave < 0) return 0;
    if (ws->wave_active) return 0;

    // 检查是否所有敌人都死亡
    for (int i = 0; i < enemy_count; i++) {
        if (enemy_is_alive(&enemies[i])) return 0;
    }
    return 1;
}

int wave_system_all_done(WaveSystem* ws) {
    return ws->current_wave >= ws->wave_count - 1 && !ws->wave_active;
}
```

**使用示例**：

```c
// 初始化波次
WaveSystem wave_sys;
wave_system_init(&wave_sys);

WaveConfig wave1 = { .type = ENEMY_SLIME, .count = 5, .spawn_delay = 1.0f,
                    .spawn_interval = 0.5f, .x = 400, .y = 100 };
WaveConfig wave2 = { .type = ENEMY_BAT, .count = 8, .spawn_delay = 2.0f,
                    .spawn_interval = 0.3f, .x = 400, .y = 100 };
WaveConfig wave3 = { .type = ENEMY_SKELETON, .count = 4, .spawn_delay = 1.5f,
                    .spawn_interval = 0.8f, .x = 400, .y = 100 };

wave_system_add_wave(&wave_sys, wave1);
wave_system_add_wave(&wave_sys, wave2);
wave_system_add_wave(&wave_sys, wave3);

// 每帧更新
wave_system_update(&wave_sys, enemies, MAX_ENEMIES, &enemy_count, sheets, dt);

// 检查波次完成
if (wave_system_is_complete(&wave_sys, enemies, enemy_count)) {
    // 显示"Wave Cleared"提示
    // 自动开始下一波
}
```

#### 总结

- **生成器**：在固定位置按间隔生成敌人，控制同时存活数
- **波次配置**：每波定义敌人类型、数量、生成节奏
- **难度曲线**：随波次提升敌人血量、攻击力
- **波次切换**：当前波次敌人全灭后开始下一波
- **数据驱动**：波次配置存数组或文件，方便调整
- **常见坑**：生成器无上限导致敌人无限增多卡顿；波次切换条件错误导致卡关

---

## 第八章 物品与UI

> 物品和UI让游戏有深度和交互。本章学习物品系统、背包、HUD、菜单对话框，打造完整的游戏体验。

### 第26讲 物品系统设计

#### 概念

**物品系统（Item System）** 管理游戏中的道具、装备、消耗品。本讲设计一个数据驱动的物品系统：物品定义、堆叠、掉落物、拾取。

#### 原理

**物品分类**：

```c
typedef enum {
    ITEM_NONE = 0,
    ITEM_CONSUMABLE,  // 消耗品：药水、心
    ITEM_WEAPON,      // 武器：剑、弓
    ITEM_ARMOR,       // 防具
    ITEM_KEY,         // 关键道具：钥匙、剧情物品
    ITEM_MATERIAL     // 材料：金币、矿石
} ItemType;
```

**物品定义（数据驱动）**：

```c
typedef struct {
    int id;
    char name[32];
    char description[128];
    ItemType type;
    int max_stack;       // 最大堆叠数
    int value;           // 价值（金币）
    int effect_id;       // 效果ID（治疗、伤害等）
    int effect_amount;   // 效果数值
    SDL_Texture* icon;   // 图标
} ItemDef;
```

**物品实例**：

```c
typedef struct {
    int item_id;     // 指向ItemDef
    int count;       // 当前堆叠数
} ItemInstance;
```

**掉落物**：

地图上的可拾取物品：

```c
typedef struct {
    float x, y;
    int item_id;
    int count;
    float lifetime;    // 存在时间（-1永久）
    float bob_timer;   // 上下浮动动画
    int active;
} DroppedItem;
```

**拾取流程**：

1. 检测玩家与掉落物碰撞
2. 尝试加入背包
3. 成功则移除掉落物，播放拾取音效
4. 失败（背包满）则保留掉落物

#### 例子

```c
// item.h
#ifndef ITEM_H
#define ITEM_H

#include <SDL2/SDL.h>

#define MAX_ITEMS 128
#define MAX_DROPPED 64

typedef enum {
    ITEM_NONE = 0,
    ITEM_CONSUMABLE,
    ITEM_WEAPON,
    ITEM_ARMOR,
    ITEM_KEY,
    ITEM_MATERIAL
} ItemType;

typedef struct {
    int id;
    char name[32];
    char description[128];
    ItemType type;
    int max_stack;
    int value;
    int effect_id;      // 0=无, 1=治疗, 2=伤害, 3=加攻击
    int effect_amount;
    SDL_Texture* icon;
} ItemDef;

typedef struct {
    int item_id;
    int count;
} ItemInstance;

typedef struct {
    float x, y;
    int item_id;
    int count;
    float lifetime;
    float bob_timer;
    int active;
} DroppedItem;

typedef struct {
    ItemDef definitions[MAX_ITEMS];
    int def_count;
    DroppedItem dropped[MAX_DROPPED];
} ItemDatabase;

void item_db_init(ItemDatabase* db);
int item_db_register(ItemDatabase* db, ItemDef def);
ItemDef* item_db_get(ItemDatabase* db, int id);

void dropped_spawn(ItemDatabase* db, int item_id, int count, float x, float y);
void dropped_update(ItemDatabase* db, float dt);
void dropped_draw(ItemDatabase* db, SDL_Renderer* r, Camera* cam);
DroppedItem* dropped_check_pickup(ItemDatabase* db, SDL_Rect picker_box);

#endif
```

```c
// item.c
#include "item.h"
#include <string.h>
#include <math.h>

void item_db_init(ItemDatabase* db) {
    memset(db, 0, sizeof(*db));
}

int item_db_register(ItemDatabase* db, ItemDef def) {
    if (db->def_count >= MAX_ITEMS) return -1;
    def.id = db->def_count;
    db->definitions[db->def_count] = def;
    return db->def_count++;
}

ItemDef* item_db_get(ItemDatabase* db, int id) {
    if (id < 0 || id >= db->def_count) return NULL;
    return &db->definitions[id];
}

void dropped_spawn(ItemDatabase* db, int item_id, int count, float x, float y) {
    for (int i = 0; i < MAX_DROPPED; i++) {
        if (!db->dropped[i].active) {
            db->dropped[i].x = x;
            db->dropped[i].y = y;
            db->dropped[i].item_id = item_id;
            db->dropped[i].count = count;
            db->dropped[i].lifetime = 30.0f;  // 30秒后消失
            db->dropped[i].bob_timer = 0;
            db->dropped[i].active = 1;
            return;
        }
    }
}

void dropped_update(ItemDatabase* db, float dt) {
    for (int i = 0; i < MAX_DROPPED; i++) {
        if (!db->dropped[i].active) continue;
        db->dropped[i].lifetime -= dt;
        db->dropped[i].bob_timer += dt;
        if (db->dropped[i].lifetime <= 0) {
            db->dropped[i].active = 0;
        }
    }
}

void dropped_draw(ItemDatabase* db, SDL_Renderer* r, Camera* cam) {
    for (int i = 0; i < MAX_DROPPED; i++) {
        DroppedItem* d = &db->dropped[i];
        if (!d->active) continue;

        ItemDef* def = item_db_get(db, d->item_id);
        if (!def || !def->icon) continue;

        // 上下浮动
        float bob = sinf(d->bob_timer * 3.0f) * 4.0f;

        int sx, sy;
        camera_world_to_screen(cam, d->x, d->y + bob, &sx, &sy);

        // 闪烁警告（即将消失）
        if (d->lifetime < 5.0f) {
            int blink = ((int)(d->lifetime * 8)) % 2;
            if (blink) continue;
        }

        SDL_Rect dst = { sx, sy, 32, 32 };
        SDL_RenderCopy(r, def->icon, NULL, &dst);
    }
}

DroppedItem* dropped_check_pickup(ItemDatabase* db, SDL_Rect picker_box) {
    for (int i = 0; i < MAX_DROPPED; i++) {
        DroppedItem* d = &db->dropped[i];
        if (!d->active) continue;

        SDL_Rect drop_box = { (int)d->x, (int)d->y, 32, 32 };
        if (picker_box.x < drop_box.x + drop_box.w &&
            picker_box.x + picker_box.w > drop_box.x &&
            picker_box.y < drop_box.y + drop_box.h &&
            picker_box.y + picker_box.h > drop_box.y) {
            return d;
        }
    }
    return NULL;
}
```

#### 总结

- **物品分类**：消耗品、武器、防具、关键道具、材料
- **数据驱动**：ItemDef存定义，ItemInstance存实例（id+count）
- **堆叠**：同id物品可堆叠到max_stack
- **掉落物**：地图上的可拾取物品，有生命周期和浮动动画
- **拾取检测**：玩家碰撞盒与掉落物碰撞盒相交
- **常见坑**：掉落物未用对象池导致频繁malloc；忘记清理过期掉落物

---

### 第27讲 背包与HUD界面

#### 概念

**背包（Inventory）** 存储玩家收集的物品。**HUD（Heads-Up Display）** 是游戏中的常驻界面：血量、金币、小地图。本讲实现背包系统和HUD绘制。

#### 原理

**背包数据结构**：

```c
typedef struct {
    ItemInstance slots[INVENTORY_SIZE];  // 固定槽位
    int size;
    int coins;  // 金币单独存储
} Inventory;
```

**添加物品逻辑**：

1. 如果物品可堆叠，先找已有该物品的槽位，累加count
2. 找空槽位放入
3. 都失败则背包满

**HUD元素**：

- 血量：用心形图标，每个代表1点HP
- 金币：数字+图标
- 小地图：简化版地图，显示玩家位置
- 当前装备：显示武器、防具图标

**血量心形绘制**：

```c
// 假设每个心代表2HP，满心=2HP，半心=1HP，空心=0HP
for (int i = 0; i < max_hp / 2; i++) {
    int heart_state;  // 0=空, 1=半, 2=满
    if (hp >= (i+1)*2) heart_state = 2;
    else if (hp == i*2+1) heart_state = 1;
    else heart_state = 0;
    // 绘制对应图标
}
```

#### 例子

```c
// inventory.h
#ifndef INVENTORY_H
#define INVENTORY_H

#include "item.h"

#define INVENTORY_SIZE 20

typedef struct {
    ItemInstance slots[INVENTORY_SIZE];
    int coins;
} Inventory;

void inventory_init(Inventory* inv);
int inventory_add(Inventory* inv, ItemDatabase* db, int item_id, int count);
int inventory_remove(Inventory* inv, int slot, int count);
int inventory_count_item(Inventory* inv, int item_id);
int inventory_find_item(Inventory* inv, int item_id);

#endif
```

```c
// inventory.c
#include "inventory.h"
#include <string.h>

void inventory_init(Inventory* inv) {
    memset(inv, 0, sizeof(*inv));
}

int inventory_add(Inventory* inv, ItemDatabase* db, int item_id, int count) {
    ItemDef* def = item_db_get(db, item_id);
    if (!def) return 0;

    // 1. 尝试堆叠到已有槽位
    if (def->max_stack > 1) {
        for (int i = 0; i < INVENTORY_SIZE; i++) {
            if (inv->slots[i].item_id == item_id) {
                int space = def->max_stack - inv->slots[i].count;
                if (space > 0) {
                    int add = (count < space) ? count : space;
                    inv->slots[i].count += add;
                    count -= add;
                    if (count == 0) return 1;
                }
            }
        }
    }

    // 2. 找空槽位
    while (count > 0) {
        int empty = -1;
        for (int i = 0; i < INVENTORY_SIZE; i++) {
            if (inv->slots[i].item_id == 0) {  // 0表示空
                empty = i;
                break;
            }
        }
        if (empty < 0) return 0;  // 背包满

        int add = (count < def->max_stack) ? count : def->max_stack;
        inv->slots[empty].item_id = item_id;
        inv->slots[empty].count = add;
        count -= add;
    }
    return 1;
}

int inventory_remove(Inventory* inv, int slot, int count) {
    if (slot < 0 || slot >= INVENTORY_SIZE) return 0;
    if (inv->slots[slot].item_id == 0) return 0;
    if (inv->slots[slot].count < count) return 0;

    inv->slots[slot].count -= count;
    if (inv->slots[slot].count <= 0) {
        inv->slots[slot].item_id = 0;
        inv->slots[slot].count = 0;
    }
    return 1;
}

int inventory_count_item(Inventory* inv, int item_id) {
    int total = 0;
    for (int i = 0; i < INVENTORY_SIZE; i++) {
        if (inv->slots[i].item_id == item_id) {
            total += inv->slots[i].count;
        }
    }
    return total;
}

int inventory_find_item(Inventory* inv, int item_id) {
    for (int i = 0; i < INVENTORY_SIZE; i++) {
        if (inv->slots[i].item_id == item_id) return i;
    }
    return -1;
}
```

**HUD绘制**：

```c
// hud.h
#ifndef HUD_H
#define HUD_H

#include "player.h"
#include "inventory.h"
#include <SDL2/SDL_ttf.h>

typedef struct {
    SDL_Texture* heart_full;
    SDL_Texture* heart_half;
    SDL_Texture* heart_empty;
    SDL_Texture* coin_icon;
    TTF_Font* font;
} HUDAssets;

void hud_init(HUDAssets* h, SDL_Renderer* r);
void hud_draw(HUDAssets* h, SDL_Renderer* r, Player* p, Inventory* inv);

#endif
```

```c
// hud.c
#include "hud.h"
#include <SDL2/SDL_image.h>
#include <stdio.h>

void hud_init(HUDAssets* h, SDL_Renderer* r) {
    h->heart_full = IMG_LoadTexture(r, "assets/ui/heart_full.png");
    h->heart_half = IMG_LoadTexture(r, "assets/ui/heart_half.png");
    h->heart_empty = IMG_LoadTexture(r, "assets/ui/heart_empty.png");
    h->coin_icon = IMG_LoadTexture(r, "assets/ui/coin.png");
    h->font = TTF_OpenFont("assets/fonts/arial.ttf", 18);
}

void hud_draw(HUDAssets* h, SDL_Renderer* r, Player* p, Inventory* inv) {
    // 血量心形（左上角）
    int heart_size = 24;
    int heart_spacing = 28;
    int hearts_total = p->max_hp / 2;  // 每心2HP
    for (int i = 0; i < hearts_total; i++) {
        SDL_Texture* tex;
        int heart_hp = p->hp - i * 2;
        if (heart_hp >= 2) tex = h->heart_full;
        else if (heart_hp == 1) tex = h->heart_half;
        else tex = h->heart_empty;

        SDL_Rect dst = { 10 + i * heart_spacing, 10, heart_size, heart_size };
        SDL_RenderCopy(r, tex, NULL, &dst);
    }

    // 金币（右上角）
    SDL_Rect coin_dst = { 700, 10, 24, 24 };
    SDL_RenderCopy(r, h->coin_icon, NULL, &coin_dst);

    char coin_text[32];
    snprintf(coin_text, sizeof(coin_text), "x %d", inv->coins);
    SDL_Color white = {255, 255, 255, 255};
    SDL_Surface* surface = TTF_RenderText_Blended(h->font, coin_text, white);
    if (surface) {
        SDL_Texture* tex = SDL_CreateTextureFromSurface(r, surface);
        SDL_Rect text_dst = { 730, 12, surface->w, surface->h };
        SDL_RenderCopy(r, tex, NULL, &text_dst);
        SDL_DestroyTexture(tex);
        SDL_FreeSurface(surface);
    }
}
```

#### 总结

- **背包结构**：固定槽位数组，每个槽位存ItemInstance（id+count）
- **添加物品**：先尝试堆叠，再找空槽，失败则满
- **HUD元素**：血量心形、金币、装备、小地图
- **心形血量**：满心/半心/空心三态，每心代表2HP
- **文字渲染**：用SDL_ttf的TTF_RenderText_Blended生成surface再转纹理
- **常见坑**：背包满时未提示玩家；心形血量计算错误（奇数HP处理）；文字纹理未销毁导致内存泄漏

---

### 第28讲 菜单与对话框系统

#### 概念

**菜单系统（Menu System）** 用于暂停菜单、设置、背包界面。**对话框（Dialog Box）** 用于NPC对话、剧情演出。本讲实现可导航菜单和打字机效果对话框。

#### 原理

**菜单结构**：

```c
typedef struct {
    char label[32];
    int action_id;  // 选中时触发的动作
} MenuItem;

typedef struct {
    MenuItem items[16];
    int count;
    int selected;   // 当前选中项
    int active;
} Menu;
```

**菜单导航**：

- 上下键移动选中项
- 回车确认
- ESC关闭

**打字机效果**：

对话文字逐字显示，模拟打字感：

```c
// 每帧显示更多字符
if (chars_shown < strlen(text)) {
    type_timer += dt;
    if (type_timer >= 0.05f) {  // 每50ms显示一个字
        chars_shown++;
        type_timer = 0;
    }
}
```

**对话框状态**：

- IDLE：无对话
- TYPING：正在打字
- WAITING：打字完成，等待玩家按键继续
- DONE：对话结束

#### 例子

```c
// dialog.h
#ifndef DIALOG_H
#define DIALOG_H

#include <SDL2/SDL.h>
#include <SDL2/SDL_ttf.h>

#define DIALOG_MAX_CHARS 256

typedef enum {
    DIALOG_IDLE,
    DIALOG_TYPING,
    DIALOG_WAITING,
    DIALOG_DONE
} DialogState;

typedef struct {
    char text[DIALOG_MAX_CHARS];
    char speaker[32];
    int chars_shown;
    float type_timer;
    float type_speed;  // 每字间隔
    DialogState state;
    TTF_Font* font;
} DialogBox;

void dialog_init(DialogBox* d, TTF_Font* font);
void dialog_start(DialogBox* d, const char* speaker, const char* text);
void dialog_update(DialogBox* d, float dt);
void dialog_handle_input(DialogBox* d, SDL_Event* e);  // 按键跳过/继续
void dialog_draw(DialogBox* d, SDL_Renderer* r);

#endif
```

```c
// dialog.c
#include "dialog.h"
#include <string.h>
#include <stdio.h>

void dialog_init(DialogBox* d, TTF_Font* font) {
    memset(d, 0, sizeof(*d));
    d->font = font;
    d->type_speed = 0.04f;
    d->state = DIALOG_IDLE;
}

void dialog_start(DialogBox* d, const char* speaker, const char* text) {
    strncpy(d->speaker, speaker, sizeof(d->speaker) - 1);
    strncpy(d->text, text, sizeof(d->text) - 1);
    d->chars_shown = 0;
    d->type_timer = 0;
    d->state = DIALOG_TYPING;
}

void dialog_update(DialogBox* d, float dt) {
    if (d->state != DIALOG_TYPING) return;

    d->type_timer += dt;
    if (d->type_timer >= d->type_speed) {
        d->type_timer = 0;
        d->chars_shown++;
        if (d->chars_shown >= strlen(d->text)) {
            d->chars_shown = strlen(d->text);
            d->state = DIALOG_WAITING;
        }
    }
}

void dialog_handle_input(DialogBox* d, SDL_Event* e) {
    if (e->type != SDL_KEYDOWN) return;
    if (e->key.keysym.sym != SDLK_SPACE && e->key.keysym.sym != SDLK_RETURN) return;

    switch (d->state) {
        case DIALOG_TYPING:
            // 跳过打字，显示全部
            d->chars_shown = strlen(d->text);
            d->state = DIALOG_WAITING;
            break;
        case DIALOG_WAITING:
            // 结束对话
            d->state = DIALOG_DONE;
            break;
        default:
            break;
    }
}

void dialog_draw(DialogBox* d, SDL_Renderer* r) {
    if (d->state == DIALOG_IDLE || d->state == DIALOG_DONE) return;

    // 对话框背景（底部）
    SDL_Rect box = { 50, 450, 700, 130 };
    SDL_SetRenderDrawColor(r, 20, 20, 40, 220);
    SDL_SetRenderDrawBlendMode(r, SDL_BLENDMODE_BLEND);
    SDL_RenderFillRect(r, &box);
    SDL_SetRenderDrawColor(r, 200, 200, 220, 255);
    SDL_RenderDrawRect(r, &box);

    // 说话者名字
    SDL_Color name_color = {255, 220, 100, 255};
    SDL_Surface* name_surf = TTF_RenderText_Blended(d->font, d->speaker, name_color);
    if (name_surf) {
        SDL_Texture* name_tex = SDL_CreateTextureFromSurface(r, name_surf);
        SDL_Rect name_dst = { box.x + 15, box.y + 10, name_surf->w, name_surf->h };
        SDL_RenderCopy(r, name_tex, NULL, &name_dst);
        SDL_DestroyTexture(name_tex);
        SDL_FreeSurface(name_surf);
    }

    // 对话内容（截取已显示部分）
    char visible[DIALOG_MAX_CHARS];
    strncpy(visible, d->text, d->chars_shown);
    visible[d->chars_shown] = '\0';

    SDL_Color text_color = {255, 255, 255, 255};
    SDL_Surface* text_surf = TTF_RenderText_Blended_Wrapped(d->font, visible,
                                                             text_color, box.w - 30);
    if (text_surf) {
        SDL_Texture* text_tex = SDL_CreateTextureFromSurface(r, text_surf);
        SDL_Rect text_dst = { box.x + 15, box.y + 40, text_surf->w, text_surf->h };
        SDL_RenderCopy(r, text_tex, NULL, &text_dst);
        SDL_DestroyTexture(text_tex);
        SDL_FreeSurface(text_surf);
    }

    // 等待提示
    if (d->state == DIALOG_WAITING) {
        SDL_Color prompt_color = {200, 200, 200, 255};
        SDL_Surface* prompt_surf = TTF_RenderText_Blended(d->font, "[Space]",
                                                           prompt_color);
        if (prompt_surf) {
            SDL_Texture* prompt_tex = SDL_CreateTextureFromSurface(r, prompt_surf);
            SDL_Rect prompt_dst = { box.x + box.w - 60, box.y + box.h - 25,
                                   prompt_surf->w, prompt_surf->h };
            SDL_RenderCopy(r, prompt_tex, NULL, &prompt_dst);
            SDL_DestroyTexture(prompt_tex);
            SDL_FreeSurface(prompt_surf);
        }
    }
}
```

#### 总结

- **菜单结构**：选项数组+选中索引，上下键导航
- **打字机效果**：逐字显示，每字间隔约40ms
- **对话框状态**：IDLE→TYPING→WAITING→DONE
- **按键跳过**：打字中按键直接显示全部，等待中按键结束对话
- **文字换行**：用TTF_RenderText_Blended_Wrapped自动换行
- **常见坑**：文字纹理未销毁导致内存泄漏；对话框未暂停游戏导致对话中角色还在动；中文显示需加载中文字体

---

## 第九章 音效与存档

> 声音让游戏有灵魂，存档让进度可延续。本章学习SDL_mixer音效系统和存档系统，并整合所有模块完成最终游戏。

### 第29讲 SDL_mixer音效系统

#### 概念

**SDL_mixer** 是SDL2的音频扩展库，支持WAV、MP3、OGG等多种格式，提供音效（Sound Effect）和音乐（Music）两类播放接口。本讲学习音频初始化、音效加载播放、背景音乐管理、3D音效基础。

#### 原理

**音效 vs 音乐**：

- **音效（Chunk）**：短促音频（攻击、拾取、爆炸），常驻内存，可同时播放多个
- **音乐（Music）**：长音频（BGM），流式播放，同时只能播放一个

**SDL_mixer初始化**：

```c
Mix_OpenAudio(44100, MIX_DEFAULT_FORMAT, 2, 2048);
// 44100Hz采样率，16位格式，立体声，2048字节缓冲
```

**通道系统**：

SDL_mixer有多个通道（默认8个），每个通道可同时播放一个音效。播放时自动分配空闲通道：

```c
Mix_PlayChannel(-1, chunk, 0);  // -1=自动选通道，0=不循环
```

**音量控制**：

```c
Mix_Volume(chunk_channel, volume);  // 0-128
Mix_VolumeMusic(volume);            // 音乐音量
```

**音效池**：

频繁播放的音效（如脚步、攻击）预加载到内存，避免每次读盘：

```c
typedef struct {
    Mix_Chunk* attack;
    Mix_Chunk* hurt;
    Mix_Chunk* pickup;
    Mix_Chunk* death;
} SoundEffects;
```

**3D音效（简化）**：

根据声源与玩家距离调整音量和左右声道：

```c
float dist = sqrtf((sx-px)*(sx-px) + (sy-py)*(sy-py));
int volume = 128 * (1.0f - dist / max_dist);
if (volume < 0) volume = 0;
Mix_Volume(channel, volume);
```

#### 例子

```c
// audio.h
#ifndef AUDIO_H
#define AUDIO_H

#include <SDL2/SDL_mixer.h>

#define MAX_SOUNDS 32

typedef enum {
    SND_ATTACK = 0,
    SND_HURT,
    SND_PICKUP,
    SND_DEATH,
    SND_COIN,
    SND_MENU_MOVE,
    SND_MENU_SELECT,
    SND_COUNT
} SoundId;

typedef struct {
    Mix_Chunk* sounds[SND_COUNT];
    Mix_Music* current_music;
    int sound_volume;   // 0-128
    int music_volume;
    int initialized;
} AudioSystem;

int audio_init(AudioSystem* a);
void audio_shutdown(AudioSystem* a);
int audio_load_sound(AudioSystem* a, SoundId id, const char* path);
void audio_play_sound(AudioSystem* a, SoundId id);
void audio_play_sound_at(AudioSystem* a, SoundId id, float sx, float sy,
                         float px, float py, float max_dist);
int audio_play_music(AudioSystem* a, const char* path);
void audio_stop_music(AudioSystem* a);
void audio_set_sound_volume(AudioSystem* a, int volume);
void audio_set_music_volume(AudioSystem* a, int volume);

#endif
```

```c
// audio.c
#include "audio.h"
#include <stdio.h>
#include <math.h>

int audio_init(AudioSystem* a) {
    if (Mix_OpenAudio(44100, MIX_DEFAULT_FORMAT, 2, 2048) < 0) {
        fprintf(stderr, "SDL_mixer init failed: %s\n", Mix_GetError());
        return -1;
    }
    Mix_AllocateChannels(16);  // 16个通道
    a->current_music = NULL;
    a->sound_volume = 64;
    a->music_volume = 64;
    a->initialized = 1;
    for (int i = 0; i < SND_COUNT; i++) a->sounds[i] = NULL;
    return 0;
}

void audio_shutdown(AudioSystem* a) {
    if (!a->initialized) return;
    for (int i = 0; i < SND_COUNT; i++) {
        if (a->sounds[i]) {
            Mix_FreeChunk(a->sounds[i]);
            a->sounds[i] = NULL;
        }
    }
    if (a->current_music) {
        Mix_FreeMusic(a->current_music);
        a->current_music = NULL;
    }
    Mix_CloseAudio();
    a->initialized = 0;
}

int audio_load_sound(AudioSystem* a, SoundId id, const char* path) {
    if (id < 0 || id >= SND_COUNT) return -1;
    a->sounds[id] = Mix_LoadWAV(path);
    if (!a->sounds[id]) {
        fprintf(stderr, "Failed to load sound %s: %s\n", path, Mix_GetError());
        return -1;
    }
    return 0;
}

void audio_play_sound(AudioSystem* a, SoundId id) {
    if (!a->initialized || id < 0 || id >= SND_COUNT) return;
    if (!a->sounds[id]) return;
    int channel = Mix_PlayChannel(-1, a->sounds[id], 0);
    if (channel >= 0) {
        Mix_Volume(channel, a->sound_volume);
    }
}

void audio_play_sound_at(AudioSystem* a, SoundId id, float sx, float sy,
                         float px, float py, float max_dist) {
    if (!a->initialized || id < 0 || id >= SND_COUNT) return;
    if (!a->sounds[id]) return;

    float dx = sx - px;
    float dy = sy - py;
    float dist = sqrtf(dx*dx + dy*dy);
    if (dist > max_dist) return;  // 太远不播放

    int volume = (int)(a->sound_volume * (1.0f - dist / max_dist));
    if (volume < 0) volume = 0;

    int channel = Mix_PlayChannel(-1, a->sounds[id], 0);
    if (channel >= 0) {
        Mix_Volume(channel, volume);
    }
}

int audio_play_music(AudioSystem* a, const char* path) {
    if (!a->initialized) return -1;
    if (a->current_music) {
        Mix_HaltMusic();
        Mix_FreeMusic(a->current_music);
    }
    a->current_music = Mix_LoadMUS(path);
    if (!a->current_music) {
        fprintf(stderr, "Failed to load music %s: %s\n", path, Mix_GetError());
        return -1;
    }
    Mix_VolumeMusic(a->music_volume);
    Mix_PlayMusic(a->current_music, -1);  // -1=循环
    return 0;
}

void audio_stop_music(AudioSystem* a) {
    if (a->current_music) {
        Mix_HaltMusic();
    }
}

void audio_set_sound_volume(AudioSystem* a, int volume) {
    if (volume < 0) volume = 0;
    if (volume > 128) volume = 128;
    a->sound_volume = volume;
}

void audio_set_music_volume(AudioSystem* a, int volume) {
    if (volume < 0) volume = 0;
    if (volume > 128) volume = 128;
    a->music_volume = volume;
    Mix_VolumeMusic(volume);
}
```

**使用示例**：

```c
// 初始化
AudioSystem audio;
audio_init(&audio);
audio_load_sound(&audio, SND_ATTACK, "assets/sounds/attack.wav");
audio_load_sound(&audio, SND_HURT, "assets/sounds/hurt.wav");
audio_load_sound(&audio, SND_PICKUP, "assets/sounds/pickup.wav");

// 播放BGM
audio_play_music(&audio, "assets/music/overworld.ogg");

// 玩家攻击时
audio_play_sound(&audio, SND_ATTACK);

// 敌人受伤时（带3D音效）
audio_play_sound_at(&audio, SND_HURT, enemy.x, enemy.y,
                    player.x, player.y, 500.0f);

// 拾取物品
audio_play_sound(&audio, SND_PICKUP);

// 退出时
audio_shutdown(&audio);
```

#### 总结

- **音效（Chunk）**：短音频常驻内存，多通道同时播放
- **音乐（Music）**：长音频流式播放，同时只能一个
- **初始化**：`Mix_OpenAudio(44100, MIX_DEFAULT_FORMAT, 2, 2048)`
- **通道系统**：`Mix_PlayChannel(-1, chunk, 0)`自动分配空闲通道
- **3D音效**：根据距离调整音量，营造空间感
- **常见坑**：忘记`Mix_AllocateChannels`导致通道不够；音效未预加载导致首次播放卡顿；退出未释放音频资源

---

### 第30讲 存档系统与游戏整合

#### 概念

**存档系统（Save System）** 让玩家保存进度，下次继续游戏。本讲学习二进制存档格式、存档读写、游戏整合，将前29讲的所有模块组装成完整游戏。

#### 原理

**存档内容**：

```c
typedef struct {
    int version;          // 存档版本号，用于兼容性
    char player_name[32];
    int player_hp;
    int player_max_hp;
    int player_x, player_y;
    int player_attack_power;
    int current_map_id;
    int inventory_coins;
    ItemInstance inventory_slots[INVENTORY_SIZE];
    int enemies_defeated;
    int flags;            // 游戏进度标志位
} SaveData;
```

**二进制存档**：

直接把结构体写入文件，简单高效：

```c
FILE* f = fopen("save.dat", "wb");
fwrite(&save_data, sizeof(SaveData), 1, f);
fclose(f);
```

**存档版本控制**：

结构体修改后旧存档会不兼容。用version字段处理：

```c
if (save.version != SAVE_VERSION) {
    // 迁移或拒绝
}
```

**存档时机**：

- 手动存档：玩家在菜单选择"保存"
- 自动存档：切换场景、击败BOSS、定时存档
- 防止存档损坏：写入临时文件，成功后重命名

**游戏整合架构**：

```c
typedef struct {
    Game game;              // 游戏主循环
    Player player;
    Enemy enemies[MAX_ENEMIES];
    int enemy_count;
    MultiLayerMap map;
    Camera camera;
    Inventory inventory;
    ItemDatabase item_db;
    AudioSystem audio;
    HUDAssets hud;
    DialogBox dialog;
    WaveSystem wave_sys;
    DamageNumberSystem dmg_nums;
    SceneManager scene;
    SpatialGrid grid;
} GameWorld;
```

#### 例子

```c
// save_system.h
#ifndef SAVE_SYSTEM_H
#define SAVE_SYSTEM_H

#include "player.h"
#include "inventory.h"

#define SAVE_VERSION 1
#define MAX_SAVES 3

typedef struct {
    int version;
    int valid;
    char save_name[32];
    int play_time;        // 游戏时长（秒）

    // 玩家数据
    int player_hp;
    int player_max_hp;
    int player_x, player_y;
    int player_attack_power;
    int current_map_id;

    // 背包数据
    int coins;
    ItemInstance inventory_slots[INVENTORY_SIZE];

    // 进度
    int enemies_defeated;
    int game_flags;
} SaveData;

int save_write(SaveData* data, const char* path);
int save_read(SaveData* data, const char* path);
void save_from_game(SaveData* data, Player* p, Inventory* inv, int map_id);
void save_to_game(SaveData* data, Player* p, Inventory* inv, int* map_id);

#endif
```

```c
// save_system.c
#include "save_system.h"
#include <stdio.h>
#include <string.h>

int save_write(SaveData* data, const char* path) {
    data->version = SAVE_VERSION;
    data->valid = 1;

    // 写入临时文件，成功后重命名（防止写入中断导致损坏）
    char tmp_path[256];
    snprintf(tmp_path, sizeof(tmp_path), "%s.tmp", path);

    FILE* f = fopen(tmp_path, "wb");
    if (!f) return -1;

    if (fwrite(data, sizeof(SaveData), 1, f) != 1) {
        fclose(f);
        return -1;
    }
    fclose(f);

    // 重命名覆盖原文件
    if (rename(tmp_path, path) != 0) {
        return -1;
    }
    return 0;
}

int save_read(SaveData* data, const char* path) {
    FILE* f = fopen(path, "rb");
    if (!f) return -1;

    if (fread(data, sizeof(SaveData), 1, f) != 1) {
        fclose(f);
        return -1;
    }
    fclose(f);

    if (data->version != SAVE_VERSION) {
        return -2;  // 版本不兼容
    }
    if (!data->valid) {
        return -3;  // 存档无效
    }
    return 0;
}

void save_from_game(SaveData* data, Player* p, Inventory* inv, int map_id) {
    memset(data, 0, sizeof(*data));
    data->version = SAVE_VERSION;
    data->valid = 1;
    data->player_hp = p->hp;
    data->player_max_hp = p->max_hp;
    data->player_x = (int)p->x;
    data->player_y = (int)p->y;
    data->player_attack_power = p->attack_power;
    data->current_map_id = map_id;
    data->coins = inv->coins;
    memcpy(data->inventory_slots, inv->slots, sizeof(inv->slots));
}

void save_to_game(SaveData* data, Player* p, Inventory* inv, int* map_id) {
    p->hp = data->player_hp;
    p->max_hp = data->player_max_hp;
    p->x = (float)data->player_x;
    p->y = (float)data->player_y;
    p->attack_power = data->player_attack_power;
    inv->coins = data->coins;
    memcpy(inv->slots, data->inventory_slots, sizeof(inv->slots));
    *map_id = data->current_map_id;
}
```

**完整游戏整合**：

```c
// main.c - 完整游戏入口
#include "game.h"
#include "player.h"
#include "enemy.h"
#include "multilayer_map.h"
#include "camera.h"
#include "inventory.h"
#include "audio.h"
#include "hud.h"
#include "dialog.h"
#include "wave_system.h"
#include "damage_number.h"
#include "scene.h"
#include "save_system.h"
#include <SDL2/SDL.h>
#include <SDL2/SDL_image.h>
#include <SDL2/SDL_ttf.h>
#include <SDL2/SDL_mixer.h>

#define MAX_ENEMIES 64

typedef struct {
    SDL_Window* window;
    SDL_Renderer* renderer;
    int running;

    Player player;
    Enemy enemies[MAX_ENEMIES];
    int enemy_count;
    MultiLayerMap map;
    Camera camera;
    Inventory inventory;
    ItemDatabase item_db;
    AudioSystem audio;
    HUDAssets hud;
    DialogBox dialog;
    WaveSystem wave_sys;
    DamageNumberSystem dmg_nums;
    SceneManager scene;

    float delta_time;
    Uint32 last_tick;
} GameWorld;

int game_init(GameWorld* gw) {
    SDL_Init(SDL_INIT_VIDEO | SDL_INIT_AUDIO);
    IMG_Init(IMG_INIT_PNG);
    TTF_Init();

    gw->window = SDL_CreateWindow("Zelda-like Adventure",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED,
        800, 600, SDL_WINDOW_SHOWN);
    gw->renderer = SDL_CreateRenderer(gw->window, -1,
        SDL_RENDERER_ACCELERATED | SDL_RENDERER_PRESENTVSYNC);
    gw->running = 1;
    gw->enemy_count = 0;
    gw->last_tick = SDL_GetTicks();

    // 初始化各系统
    audio_init(&gw->audio);
    camera_init(&gw->camera, 800, 600, 2000, 1500);
    inventory_init(&gw->inventory);
    item_db_init(&gw->item_db);
    wave_system_init(&gw->wave_sys);
    scene_init(&gw->scene);

    // 加载资源（简化）
    // player_init(&gw->player, &player_sheet, 400, 300);
    // mlmap_load(&gw->map, gw->renderer, ...);
    // hud_init(&gw->hud, gw->renderer);

    // 加载音效
    audio_load_sound(&gw->audio, SND_ATTACK, "assets/sounds/attack.wav");
    audio_load_sound(&gw->audio, SND_HURT, "assets/sounds/hurt.wav");
    audio_play_music(&gw->audio, "assets/music/overworld.ogg");

    return 0;
}

void game_handle_event(GameWorld* gw, SDL_Event* e) {
    if (e->type == SDL_QUIT) gw->running = 0;
    if (e->type == SDL_KEYDOWN) {
        if (e->key.keysym.sym == SDLK_ESCAPE) gw->running = 0;
        if (e->key.keysym.sym == SDLK_F5) {
            // 快速保存
            SaveData save;
            save_from_game(&save, &gw->player, &gw->inventory, gw->scene.map_id);
            save_write(&save, "savegame.dat");
        }
        if (e->key.keysym.sym == SDLK_F9) {
            // 快速加载
            SaveData save;
            if (save_read(&save, "savegame.dat") == 0) {
                int map_id;
                save_to_game(&save, &gw->player, &gw->inventory, &map_id);
                // scene_load(&gw->scene, gw->renderer, map_id, gw->player.x, gw->player.y);
            }
        }
    }
    // player_handle_event(&gw->player, e);
    dialog_handle_input(&gw->dialog, e);
}

void game_update(GameWorld* gw, float dt) {
    // 更新玩家
    player_update(&gw->player, dt);

    // 更新敌人
    for (int i = 0; i < gw->enemy_count; i++) {
        if (enemy_is_alive(&gw->enemies[i])) {
            enemy_update(&gw->enemies[i], &gw->player, dt);
        }
    }

    // 战斗判定
    // combat_player_attack_enemies(&gw->player, gw->enemies, gw->enemy_count, &gw->dmg_nums);
    for (int i = 0; i < gw->enemy_count; i++) {
        if (enemy_is_alive(&gw->enemies[i])) {
            // combat_enemy_attack_player(&gw->enemies[i], &gw->player, &gw->dmg_nums);
        }
    }

    // 摄像机跟随
    camera_follow(&gw->camera, gw->player.x, gw->player.y, dt);

    // 波次系统
    // wave_system_update(&gw->wave_sys, gw->enemies, MAX_ENEMIES, &gw->enemy_count, sheets, dt);

    // 飘字
    dmg_num_update(&gw->dmg_nums, dt);

    // 对话框
    dialog_update(&gw->dialog, dt);

    // 场景切换
    scene_update(&gw->scene, &gw->player, dt);
}

void game_render(GameWorld* gw) {
    SDL_SetRenderDrawColor(gw->renderer, 0, 0, 0, 255);
    SDL_RenderClear(gw->renderer);

    // 1. 地面层
    // mlmap_draw_ground(&gw->map, gw->renderer, &gw->camera);
    // 2. 装饰层
    // mlmap_draw_decor(&gw->map, gw->renderer, &gw->camera);
    // 3. 实体层（按y排序）
    // player_draw(&gw->player, gw->renderer, &gw->camera);
    // for (int i = 0; i < gw->enemy_count; i++) {
    //     enemy_draw(&gw->enemies[i], gw->renderer, &gw->camera);
    // }
    // 4. 高层层
    // mlmap_draw_overhead(&gw->map, gw->renderer, &gw->camera);
    // 5. 飘字
    // dmg_num_draw(&gw->dmg_nums, gw->renderer, &gw->camera);
    // 6. HUD
    // hud_draw(&gw->hud, gw->renderer, &gw->player, &gw->inventory);
    // 7. 对话框
    // dialog_draw(&gw->dialog, gw->renderer);
    // 8. 场景切换遮罩
    scene_draw_overlay(&gw->scene, gw->renderer);

    SDL_RenderPresent(gw->renderer);
}

void game_run(GameWorld* gw) {
    while (gw->running) {
        Uint32 current = SDL_GetTicks();
        gw->delta_time = (current - gw->last_tick) / 1000.0f;
        gw->last_tick = current;
        if (gw->delta_time > 0.25f) gw->delta_time = 0.25f;

        SDL_Event e;
        while (SDL_PollEvent(&e)) {
            game_handle_event(gw, &e);
        }

        game_update(gw, gw->delta_time);
        game_render(gw);
    }
}

void game_shutdown(GameWorld* gw) {
    audio_shutdown(&gw->audio);
    // mlmap_free(&gw->map);
    // sprite_sheet_free(&player_sheet);
    SDL_DestroyRenderer(gw->renderer);
    SDL_DestroyWindow(gw->window);
    Mix_Quit();
    TTF_Quit();
    IMG_Quit();
    SDL_Quit();
}

int main(int argc, char* argv[]) {
    (void)argc; (void)argv;
    GameWorld gw;
    game_init(&gw);
    game_run(&gw);
    game_shutdown(&gw);
    return 0;
}
```

#### 总结

- **存档结构**：包含玩家状态、背包、进度标志，用version字段保证兼容性
- **二进制存档**：直接fwrite结构体，简单高效
- **原子写入**：先写临时文件再重命名，防止写入中断损坏存档
- **游戏整合**：GameWorld结构体聚合所有子系统，统一初始化、更新、渲染、释放
- **渲染顺序**：地面→装饰→实体→高层→飘字→HUD→对话框→遮罩
- **常见坑**：存档结构修改后旧存档不兼容；忘记释放某个子系统资源导致内存泄漏；渲染顺序错误导致UI被地图遮挡

---

## 课程结语

恭喜你完成了《C语言+SDL2类塞尔达游戏实战》全部30讲的学习！

### 你已经掌握的核心能力

**第一章 入门基础**：搭建开发环境，理解SDL2窗口、渲染、事件三大基础系统

**第二章 游戏架构**：掌握游戏循环、状态机、模块化代码组织，能写出可维护的游戏代码

**第三章 图形与动画**：精灵图、帧动画、摄像机、图层渲染，让画面活起来

**第四章 玩家系统**：完整的玩家角色，从结构设计到输入处理、攻击动作

**第五章 地图系统**：Tile地图、多层渲染、场景切换，构建可探索的世界

**第六章 碰撞系统**：AABB检测、分轴响应、空间分区优化，物理可信

**第七章 敌人与战斗**：敌人AI、战斗系统、波次生成，充满挑战

**第八章 物品与UI**：物品系统、背包、HUD、菜单对话框，完整的游戏体验

**第九章 音效与存档**：SDL_mixer音效、存档系统、游戏整合，可发布的完整游戏

### 下一步进阶方向

1. **物理引擎**：集成Box2D或Chipmunk，支持复杂物理
2. **脚本系统**：用Lua嵌入，数据驱动游戏逻辑
3. **网络多人**：SDL_net实现联机功能
4. **OpenGL渲染**：用SDL2的OpenGL上下文，实现硬件加速渲染
5. **编辑器开发**：为你的游戏制作关卡编辑器、角色编辑器
6. **跨平台发布**：Windows、Linux、macOS、甚至Steam Deck

### 推荐继续学习的资源

- **SDL2官方文档**：wiki.libsdl.org，最权威的API参考
- **《游戏编程模式》**：Robert Nystrom著，深入游戏架构设计
- **《游戏引擎架构》**：Jason Gregory著，工业级引擎设计
- **开源项目**：研究OpenTTD、SDL2示例代码、Lazy Foo教程

游戏开发是一场马拉松，不是短跑。从这30讲出发，持续迭代你的游戏，加入自己的创意，你就能创造出独一无二的作品。祝你游戏开发之旅愉快！
