# Box2D 物理引擎 · 系统教程

> 一本面向游戏开发者的工业级 2D 物理引擎实战教材，从零基础到完整游戏集成。

---

## 课程总览

**预计讲数**：28 讲（7 章）

**学习目标**：

1. 理解 Box2D 的核心架构（World、Body、Fixture、Shape），能够独立搭建物理场景。
2. 掌握三种刚体类型（静态、运动学、动态）的适用场景与行为差异。
3. 熟练使用力、冲量、阻尼控制物体运动，实现浮力、重力缩放等高级效果。
4. 深入理解碰撞系统：接触流形、碰撞回调、传感器、接触过滤。
5. 掌握全部主流关节（距离、旋转、棱柱、滑轮、齿轮、焊接、鼠标），能构建机械装置。
6. 学会使用 CCD、射线投射、AABB 查询等高级特性解决实际问题。
7. 具备将 Box2D 集成到 SDL2 游戏中的能力，并能进行性能优化与调试。

**前置知识**：C++ 基础语法、SDL2 基本使用、初等向量数学、基本物理常识（力、质量、加速度）。

**学习路径**：基础入门 → 形状与材质 → 力与运动 → 碰撞系统 → 关节约束 → 高级特性 → 实战集成

---

## 详细章节目录

### 第 1 章 · 基础入门

- 第 01 讲：Box2D 概述与环境搭建
- 第 02 讲：物理世界 b2World 与重力
- 第 03 讲：刚体 b2Body 与三种类型
- 第 04 讲：夹具 b2Fixture 与形状基础

### 第 2 章 · 形状与材质

- 第 05 讲：圆形与多边形形状
- 第 06 讲：密度、摩擦与弹性
- 第 07 讲：边缘形状 b2EdgeShape
- 第 08 讲：链形状 b2ChainShape

### 第 3 章 · 力与运动

- 第 09 讲：施加力与冲量
- 第 10 讲：线性速度与角速度
- 第 11 讲：阻尼与休眠
- 第 12 讲：重力缩放与浮力

### 第 4 章 · 碰撞系统

- 第 13 讲：接触 b2Contact 与接触流形
- 第 14 讲：碰撞回调 b2ContactListener
- 第 15 讲：传感器 Sensor
- 第 16 讲：接触过滤 b2ContactFilter

### 第 5 章 · 关节约束

- 第 17 讲：距离关节 b2DistanceJoint
- 第 18 讲：旋转关节 b2RevoluteJoint
- 第 19 讲：棱柱关节 b2PrismaticJoint
- 第 20 讲：滑轮关节与齿轮关节
- 第 21 讲：焊接关节与鼠标关节

### 第 6 章 · 高级特性

- 第 22 讲：连续碰撞检测 CCD
- 第 23 讲：射线投射 RayCast
- 第 24 讲：世界查询 AABB Query
- 第 25 讲：求解器参数调优

### 第 7 章 · 实战集成

- 第 26 讲：Box2D 与 SDL2 集成
- 第 27 讲：性能优化与调试绘制
- 第 28 讲：完整游戏案例：物理沙盒

---

# 第 1 章 · 基础入门

Box2D 是一个用 C++ 编写的开源 2D 刚体物理引擎，由 Erin Catto 于 2006 年开发，被《愤怒的小鸟》《Cut the Rope》《Hollow Knight》等知名游戏采用。本章将带你从零开始：搭建开发环境、创建物理世界、理解刚体与夹具的关系。这些概念是 Box2D 的基石——后续所有高级特性（关节、传感器、CCD）都建立在这四个核心对象之上。

## 第 01 讲 · Box2D 概述与环境搭建

### 概念

**Box2D** 是一个工业级 2D 刚体物理引擎，提供完整的碰撞检测、碰撞响应、关节约束、连续碰撞检测（CCD）等功能。它不是一个游戏引擎，而是一个**物理库**——你负责渲染与游戏逻辑，Box2D 负责物理模拟。

Box2D 的核心设计哲学是**数据导向**（Data-Oriented Design）：内部使用 SOA（Structure of Arrays）布局，缓存友好，适合大规模物体模拟。它不依赖任何渲染库，可以与 SDL2、SFML、OpenGL、Unity（通过 C# 绑定）等任意前端集成。

### 原理

Box2D 的版本演进：

- **Box2D 2.x**：经典版本，被广泛使用，API 稳定。C++ 编写，头文件在 `Box2D/` 目录。
- **Box2D 3.x**（2024 年发布）：完全重写，使用 C 语言，性能提升 2-5 倍，API 大幅变化。

本教程基于 **Box2D 2.4.x**（最稳定的版本，文档与社区资源最丰富）。如果你使用 3.x，API 名称略有不同但概念一致。

**Box2D 的单位系统**：Box2D 使用**米-千克-秒（MKS）**单位制：

- 长度：米（meter）
- 质量：千克（kilogram）
- 时间：秒（second）

游戏通常用像素（pixel）作为长度单位，需要转换。**经验法则**：1 米 = 50~100 像素。Box2D 优化范围为 0.1~10 米的物体，过小会导致浮点精度问题，过大会有数值不稳定。

**为什么需要单位转换？** Box2D 内部使用 32 位浮点数（float），其有效精度约为 7 位十进制数。如果一个物体是 1000 像素（= 1000 米），位置精度只有 0.001 米，碰撞会抖动。把像素除以 50 转换为米后，物体是 20 米，精度足够。

### 例子

**安装 Box2D（Linux/macOS）**：

```bash
# Ubuntu/Debian
sudo apt-get install libbox2d-dev

# macOS (Homebrew)
brew install box2d

# 从源码编译（获取最新版本）
git clone https://github.com/erincatto/box2d.git
cd box2d
mkdir build && cd build
cmake -DBUILD_SHARED_LIBS=ON ..
make -j4
sudo make install
```

**CMake 配置示例**：

```cmake
cmake_minimum_required(VERSION 3.10)
project(Box2D_Tutorial)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 查找 Box2D 和 SDL2
find_package(Box2D REQUIRED)
find_package(SDL2 REQUIRED)

add_executable(tutorial main.cpp)

target_link_libraries(tutorial
    ${BOX2D_LIBRARIES}
    SDL2::SDL2
    SDL2::SDL2main
)

target_include_directories(tutorial PRIVATE
    ${BOX2D_INCLUDE_DIRS}
)
```

**最小可运行示例**：

```cpp
#include <box2d/box2d.h>
#include <cstdio>

int main() {
    // 1. 创建物理世界，重力向下 9.8 m/s²
    b2World world(b2Vec2(0.0f, 9.8f));

    // 2. 创建地面（静态刚体）
    b2BodyDef ground_def;
    ground_def.position.Set(0.0f, 10.0f);  // 位置 (0, 10) 米
    b2Body* ground = world.CreateBody(&ground_def);

    // 3. 给地面添加碰撞形状
    b2PolygonShape ground_shape;
    ground_shape.SetAsBox(50.0f, 1.0f);  // 半宽 50m，半高 1m（实际 100m × 2m）
    ground->CreateFixture(&ground_shape, 0.0f);  // 密度 0 = 静态

    // 4. 创建一个动态盒子
    b2BodyDef box_def;
    box_def.type = b2_dynamicBody;
    box_def.position.Set(0.0f, 4.0f);  // 在地面上方 6 米
    b2Body* box = world.CreateBody(&box_def);

    b2PolygonShape box_shape;
    box_shape.SetAsBox(1.0f, 1.0f);  // 1m × 1m 的盒子
    b2FixtureDef box_fixture;
    box_fixture.shape = &box_shape;
    box_fixture.density = 1.0f;       // 密度 1 kg/m²
    box_fixture.friction = 0.3f;      // 摩擦系数
    box->CreateFixture(&box_fixture);

    // 5. 模拟 90 步，每步 1/60 秒
    float time_step = 1.0f / 60.0f;
    int velocity_iterations = 8;
    int position_iterations = 3;

    for (int i = 0; i < 90; ++i) {
        world.Step(time_step, velocity_iterations, position_iterations);
        b2Vec2 pos = box->GetPosition();
        float angle = box->GetAngle();
        printf("Step %d: pos=(%.2f, %.2f), angle=%.2f\n",
               i, pos.x, pos.y, angle);
    }

    return 0;
}
```

**编译运行**：

```bash
g++ -std=c++17 main.cpp -lbox2d -o tutorial
./tutorial
```

**预期输出**：盒子从 y=4 自由下落，约 0.9 秒后撞到地面（y=9），随后因摩擦逐渐静止。

### 总结

- Box2D 是工业级 2D 物理引擎，本教程基于稳定的 2.4.x 版本。
- **单位系统**：MKS（米-千克-秒），游戏像素需转换为米（1 米 = 50~100 像素）。
- **物体大小**：优化范围 0.1~10 米，过小或过大会数值不稳定。
- **最小程序四步**：创建 World → 创建 Body → 添加 Fixture → 循环 Step。
- **常见坑**：忘记设置密度（density=0 的 fixture 不会让动态刚体有质量）；忘记调用 `world.Step()` 物理不会更新。
- **下一步**：第 02 讲深入讲解 b2World 的配置与重力。

---

## 第 02 讲 · 物理世界 b2World 与重力

### 概念

**b2World** 是 Box2D 的顶层容器，管理所有刚体、夹具、关节和接触。一个游戏通常只需要一个 World（除非有独立的子场景，如分屏）。World 负责：

- 存储所有物理对象
- 推进物理模拟（`Step` 方法）
- 管理全局参数（重力、求解器迭代次数、允许休眠）
- 提供查询接口（射线投射、AABB 查询）

**重力（Gravity）** 是 World 的核心参数，是一个 `b2Vec2` 向量，表示每秒每秒的加速度（m/s²）。地球重力约 `(0, 9.8)`，月球约 `(0, 1.6)`，太空约 `(0, 0)`。

### 原理

**World 的内部结构**：

```
b2World
├── b2Body 列表（双向链表）
│   └── 每个 Body 持有 b2Fixture 列表
├── b2Joint 列表
├── b2ContactManager（管理碰撞）
├── b2Solver（求解器，处理约束）
└── b2BroadPhase（宽相位，AABB 剪枝）
```

**重力的作用机制**：每个动态刚体在 `Step` 时受到 `gravity · mass · dt` 的冲量，等价于施加力 `F = m·g`。这是 Box2D 的默认行为，可通过 `body->SetGravityScale(0)` 关闭单个刚体的重力（用于气球、飞船）。

**Step 的三个参数**：

1. `time_step`：模拟的时间步长（秒）。**必须固定**，否则物理不稳定。推荐 1/60 秒。
2. `velocity_iterations`：速度约束求解器迭代次数。影响关节与接触的刚度。默认 8，越多次数越精确但越慢。
3. `position_iterations`：位置约束求解器迭代次数。影响穿透修正的精度。默认 3。

**为什么需要迭代？** 物理约束（如"两物体不穿透"）是一个非线性方程组，无法一次求解。Box2D 使用顺序冲量法（Sequential Impulse），多次迭代逐步收敛。8 次迭代后通常收敛到可接受精度。

**子步进（Sub-stepping）**：当 `time_step` 较大（如 1/30 秒）时，可在一步内做多次小步进，提升精度。Box2D 2.x 不直接支持，需手动调用多次 `Step`。

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>

int main() {
    // ========== 1. 创建 World ==========
    b2Vec2 gravity(0.0f, 9.8f);  // 地球重力
    b2World world(gravity);

    // ========== 2. 配置 World 全局参数 ==========
    // 允许休眠：静止的物体进入休眠状态，跳过模拟以节省 CPU
    world.SetAllowSleeping(true);   // 默认 true
    // 允许连续碰撞检测：防止快速移动物体穿透（第 22 讲详讲）
    world.SetContinuousPhysics(true);  // 默认 true
    // 子步进：当物体速度过快时自动细分时间步
    world.SetSubStepping(false);  // 默认 false

    // ========== 3. 创建测试物体 ==========
    // 地面
    b2BodyDef ground_def;
    ground_def.position.Set(0.0f, 10.0f);
    b2Body* ground = world.CreateBody(&ground_def);
    b2PolygonShape ground_shape;
    ground_shape.SetAsBox(50.0f, 1.0f);
    ground->CreateFixture(&ground_shape, 0.0f);

    // 三个不同重力缩放的球
    b2BodyDef ball_def;
    ball_def.type = b2_dynamicBody;
    b2CircleShape ball_shape;
    ball_shape.m_radius = 0.5f;
    b2FixtureDef ball_fixture;
    ball_fixture.shape = &ball_shape;
    ball_fixture.density = 1.0f;
    ball_fixture.restitution = 0.7f;

    // 球 A：正常重力
    ball_def.position.Set(-3.0f, 0.0f);
    b2Body* ball_a = world.CreateBody(&ball_def);
    ball_a->CreateFixture(&ball_fixture);
    ball_a->SetGravityScale(1.0f);  // 默认值

    // 球 B：半重力（模拟月球感觉）
    ball_def.position.Set(0.0f, 0.0f);
    b2Body* ball_b = world.CreateBody(&ball_def);
    ball_b->CreateFixture(&ball_fixture);
    ball_b->SetGravityScale(0.5f);

    // 球 C：无重力（气球效果）
    ball_def.position.Set(3.0f, 0.0f);
    b2Body* ball_c = world.CreateBody(&ball_def);
    ball_c->CreateFixture(&ball_fixture);
    ball_c->SetGravityScale(0.0f);  // 不受重力
    // 给气球一个向上的初速度
    ball_c->SetLinearVelocity(b2Vec2(0.0f, -2.0f));

    // ========== 4. 模拟 ==========
    float time_step = 1.0f / 60.0f;
    for (int i = 0; i < 180; ++i) {  // 3 秒
        world.Step(time_step, 8, 3);

        if (i % 30 == 0) {  // 每 0.5 秒打印一次
            printf("t=%.1fs: A=(%.2f,%.2f) B=(%.2f,%.2f) C=(%.2f,%.2f)\n",
                   i * time_step,
                   ball_a->GetPosition().x, ball_a->GetPosition().y,
                   ball_b->GetPosition().x, ball_b->GetPosition().y,
                   ball_c->GetPosition().x, ball_c->GetPosition().y);
        }
    }

    // ========== 5. 销毁 World ==========
    // World 析构时自动销毁所有 Body、Fixture、Joint
    // 无需手动释放
    return 0;
}
```

**预期输出**：球 A 下落最快，球 B 次之（半重力），球 C 向上漂浮（无重力 + 初速度）。

### 总结

- b2World 是物理场景的顶层容器，管理所有对象与全局参数。
- 重力是 `b2Vec2`，地球 `(0, 9.8)`，可逐刚体缩放（`SetGravityScale`）。
- `Step(dt, vel_iter, pos_iter)` 推进模拟，时间步必须固定，推荐 1/60 秒。
- 迭代次数越多越精确但越慢：默认 8/3 适合大多数场景，堆叠场景需 10/5。
- **常见坑**：`time_step` 不固定会导致物理不稳定（如使用真实帧时间）；忘记 `SetAllowSleeping(true)` 会让静止物体持续消耗 CPU。
- **性能提示**：休眠的物体不参与模拟，可大幅降低 CPU 占用；调试时可用 `world.SetAllowSleeping(false)` 观察所有物体。

---

## 第 03 讲 · 刚体 b2Body 与三种类型

### 概念

**b2Body** 是物理世界中的"物体"，持有位置、速度、角度、角速度等状态。Box2D 的刚体有三种类型，行为差异巨大：

- **b2_staticBody**：静态刚体。不移动、不受力、不参与碰撞响应（但会被其他物体碰撞）。用于墙壁、地面、固定平台。
- **b2_kinematicBody**：运动学刚体。可移动（通过设置速度），但不受力、不受碰撞影响。用于电梯、传送带、旋转门。
- **b2_dynamicBody**：动态刚体。受重力、力、冲量、碰撞影响。是物理模拟的主体，用于玩家、敌人、可投掷物。

### 原理

**三种刚体的属性对比**：

| 属性 | static | kinematic | dynamic |
|------|--------|-----------|---------|
| 质量 | 0 | 0 | > 0（由密度计算） |
| 受重力 | 否 | 否 | 是 |
| 受力/冲量 | 否 | 否 | 是 |
| 受碰撞影响 | 否 | 否 | 是 |
| 可设置速度 | 否（无效） | 是 | 是 |
| 可设置位置 | 是（瞬移） | 是（瞬移） | 是（不推荐，会破坏物理） |
| 与其他类型碰撞 | static-dynamic, static-kinematic | kinematic-dynamic | dynamic-dynamic, dynamic-static, dynamic-kinematic |
| 与同类碰撞 | 否 | 否 | 是 |

**关键规则**：

1. **static-static 不碰撞**：两个静态刚体即使重叠也不会触发碰撞响应（它们都不动）。
2. **kinematic-kinematic 不碰撞**：同理。
3. **kinematic 撞 dynamic**：dynamic 会被推开，kinematic 不受影响（像一堵移动的墙）。
4. **static 撞 dynamic**：dynamic 被弹开，static 不动。

**质量计算**：动态刚体的质量由其所有 fixture 的密度 × 面积自动累加。也可通过 `body->SetMassData()` 手动指定，但通常不推荐——让 Box2D 自动计算更合理。

**质心（Center of Mass）**：Box2D 自动计算刚体的质心，所有物理运算（力、冲量）都基于质心。`body->GetPosition()` 返回的是质心位置，而非创建时指定的位置。对于均匀形状，二者一致；对于不均匀形状（多个不同密度的 fixture），质心会偏移。

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>

int main() {
    b2World world(b2Vec2(0.0f, 9.8f));

    // ========== 1. 静态刚体：地面 ==========
    b2BodyDef static_def;
    static_def.type = b2_staticBody;  // 默认就是 static
    static_def.position.Set(0.0f, 10.0f);
    b2Body* ground = world.CreateBody(&static_def);
    b2PolygonShape ground_shape;
    ground_shape.SetAsBox(50.0f, 1.0f);
    ground->CreateFixture(&ground_shape, 0.0f);

    // ========== 2. 运动学刚体：电梯 ==========
    b2BodyDef kinematic_def;
    kinematic_def.type = b2_kinematicBody;
    kinematic_def.position.Set(0.0f, 5.0f);
    b2Body* elevator = world.CreateBody(&kinematic_def);
    b2PolygonShape elevator_shape;
    elevator_shape.SetAsBox(2.0f, 0.5f);
    b2FixtureDef elevator_fixture;
    elevator_fixture.shape = &elevator_shape;
    elevator_fixture.friction = 0.9f;  // 高摩擦，让玩家站得住
    elevator->CreateFixture(&elevator_fixture);

    // 设置电梯上下移动（速度，不是位置）
    elevator->SetLinearVelocity(b2Vec2(0.0f, 2.0f));  // 向下 2 m/s

    // ========== 3. 动态刚体：玩家 ==========
    b2BodyDef dynamic_def;
    dynamic_def.type = b2_dynamicBody;
    dynamic_def.position.Set(0.0f, 3.0f);
    b2Body* player = world.CreateBody(&dynamic_def);
    b2PolygonShape player_shape;
    player_shape.SetAsBox(0.5f, 1.0f);  // 1m × 2m 的玩家
    b2FixtureDef player_fixture;
    player_fixture.shape = &player_shape;
    player_fixture.density = 1.0f;
    player_fixture.friction = 0.3f;
    player->CreateFixture(&player_fixture);

    // 打印玩家质量（自动计算）
    printf("Player mass: %.2f kg\n", player->GetMass());  // 1×2×1 = 2 kg

    // ========== 4. 演示 SetTransform 的危险 ==========
    // 直接设置动态刚体位置会破坏物理（速度突变）
    // 仅在传送、重生等特殊场景使用
    // player->SetTransform(b2Vec2(10.0f, 3.0f), 0.0f);  // 瞬移到 (10, 3)

    // ========== 5. 模拟 ==========
    float time_step = 1.0f / 60.0f;
    for (int i = 0; i < 300; ++i) {  // 5 秒
        world.Step(time_step, 8, 3);

        // 电梯到达边界时反向
        if (elevator->GetPosition().y > 9.0f) {
            elevator->SetLinearVelocity(b2Vec2(0.0f, -2.0f));  // 向上
        } else if (elevator->GetPosition().y < 1.0f) {
            elevator->SetLinearVelocity(b2Vec2(0.0f, 2.0f));   // 向下
        }

        if (i % 60 == 0) {
            printf("t=%.1fs: elevator y=%.2f, player y=%.2f\n",
                   i * time_step,
                   elevator->GetPosition().y,
                   player->GetPosition().y);
        }
    }

    return 0;
}
```

**预期行为**：玩家下落，落在移动的电梯上，被电梯带着上下移动（因摩擦）。

### 总结

- 三种刚体：static（不动）、kinematic（手动控制速度）、dynamic（受物理影响）。
- **static 与 kinematic 不与同类碰撞**，只与 dynamic 碰撞。
- 动态刚体质量由 fixture 密度自动计算，无需手动设置。
- `GetPosition()` 返回质心位置，可能与创建位置不同（多 fixture 时）。
- **常见坑**：用 `SetTransform` 移动动态刚体会破坏物理（速度突变），仅用于传送/重生；运动学刚体用 `SetLinearVelocity` 移动，不要用 `SetTransform`。
- **选择建议**：墙壁地面用 static；电梯传送带用 kinematic；玩家敌人用 dynamic。

---

## 第 04 讲 · 夹具 b2Fixture 与形状基础

### 概念

**b2Fixture** 是刚体的"碰撞部件"，将形状（Shape）与材质属性（密度、摩擦、弹性）绑定到刚体上。一个刚体可以有多个 fixture，组合成复杂形状——例如一个人可以由头部（圆）、躯干（矩形）、四肢（胶囊）组成，每个部分有不同材质。

**Shape（形状）** 定义物体的几何外形，Box2D 提供四种：

- **b2CircleShape**：圆形，用圆心和半径定义。
- **b2PolygonShape**：凸多边形，最多 8 个顶点；也提供 `SetAsBox` 快速创建矩形。
- **b2EdgeShape**：线段，用于单向墙壁、地面边界。
- **b2ChainShape**：链形状，连接多个线段，用于地形轮廓。

### 原理

**Body 与 Fixture 的关系**：

```
b2Body（刚体：位置、速度、质量）
├── b2Fixture 1（形状 + 材质）
├── b2Fixture 2（形状 + 材质）
└── b2Fixture 3（形状 + 材质）
```

- Body 决定"如何运动"，Fixture 决定"如何碰撞"。
- 一个 Body 的总质量 = 所有 fixture 的密度 × 面积之和。
- 一个 Body 的总碰撞形状 = 所有 fixture 形状的并集。

**Fixture 的核心属性**：

- `shape`：指向形状对象的指针（不拥有，外部管理）。
- `density`：密度（kg/m²）。0 表示静态（不参与质量计算）。
- `friction`：摩擦系数 [0, 1]。两物体接触时的摩擦 = √(f1 × f2)。
- `restitution`：弹性系数 [0, 1]。两物体接触时的弹性 = max(r1, r2)（Box2D 2.x）或 √(r1 × r2)（某些版本）。
- `isSensor`：是否为传感器（只检测碰撞不产生响应，第 15 讲详讲）。
- `filter`：碰撞过滤（第 16 讲详讲）。

**FixtureDef 模式**：Box2D 使用"定义对象"模式创建资源——先填充 `b2FixtureDef`，再调用 `body->CreateFixture(&def)`。这避免了大量构造函数参数，且支持批量创建。

**形状的生命周期**：形状对象由用户管理，Box2D 不拥有它。通常把形状定义为局部变量，`CreateFixture` 内部会复制所需数据，函数返回后形状对象可销毁。

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>

int main() {
    b2World world(b2Vec2(0.0f, 9.8f));

    // 地面
    b2BodyDef ground_def;
    ground_def.position.Set(0.0f, 10.0f);
    b2Body* ground = world.CreateBody(&ground_def);
    b2PolygonShape ground_shape;
    ground_shape.SetAsBox(50.0f, 1.0f);
    ground->CreateFixture(&ground_shape, 0.0f);

    // ========== 1. 单 fixture 的简单刚体 ==========
    b2BodyDef box_def;
    box_def.type = b2_dynamicBody;
    box_def.position.Set(-5.0f, 0.0f);
    b2Body* box = world.CreateBody(&box_def);

    b2PolygonShape box_shape;
    box_shape.SetAsBox(1.0f, 1.0f);
    b2FixtureDef box_fixture;
    box_fixture.shape = &box_shape;
    box_fixture.density = 1.0f;
    box_fixture.friction = 0.3f;
    box_fixture.restitution = 0.5f;
    box->CreateFixture(&box_fixture);

    printf("Box mass: %.2f kg\n", box->GetMass());  // 4 kg (1×1×1×4)

    // ========== 2. 多 fixture 的复合刚体：锤子 ==========
    // 锤子 = 木柄（细长矩形）+ 锤头（重矩形）
    b2BodyDef hammer_def;
    hammer_def.type = b2_dynamicBody;
    hammer_def.position.Set(0.0f, 0.0f);
    b2Body* hammer = world.CreateBody(&hammer_def);

    // 木柄：低密度
    b2PolygonShape handle_shape;
    handle_shape.SetAsBox(0.1f, 1.0f, b2Vec2(0.0f, -1.0f), 0.0f);  // 局部偏移
    b2FixtureDef handle_fixture;
    handle_fixture.shape = &handle_shape;
    handle_fixture.density = 0.5f;   // 木头密度低
    handle_fixture.friction = 0.6f;
    hammer->CreateFixture(&handle_fixture);

    // 锤头：高密度
    b2PolygonShape head_shape;
    head_shape.SetAsBox(0.5f, 0.3f, b2Vec2(0.0f, 0.0f), 0.0f);
    b2FixtureDef head_fixture;
    head_fixture.shape = &head_shape;
    head_fixture.density = 5.0f;     // 金属密度高
    head_fixture.friction = 0.3f;
    head_fixture.restitution = 0.2f;
    hammer->CreateFixture(&head_fixture);

    printf("Hammer mass: %.2f kg\n", hammer->GetMass());
    printf("Hammer center of mass: (%.2f, %.2f)\n",
           hammer->GetLocalCenter().x, hammer->GetLocalCenter().y);
    // 质心会偏向锤头（高密度端）

    // ========== 3. 圆形 fixture ==========
    b2BodyDef ball_def;
    ball_def.type = b2_dynamicBody;
    ball_def.position.Set(5.0f, 0.0f);
    b2Body* ball = world.CreateBody(&ball_def);

    b2CircleShape ball_shape;
    ball_shape.m_p.Set(0.0f, 0.0f);  // 圆心相对刚体原点
    ball_shape.m_radius = 0.5f;
    b2FixtureDef ball_fixture;
    ball_fixture.shape = &ball_shape;
    ball_fixture.density = 2.0f;
    ball_fixture.restitution = 0.9f;  // 高弹性
    ball->CreateFixture(&ball_fixture);

    printf("Ball mass: %.2f kg\n", ball->GetMass());
    // 圆面积 = π·r² = π·0.25 ≈ 0.785，质量 = 0.785 × 2 ≈ 1.57 kg

    // ========== 4. 运行时销毁 fixture ==========
    // 可以动态销毁某个 fixture（如敌人被击中后失去装甲）
    // hammer->DestroyFixture(hammer->GetFixtureList());

    // 模拟
    float time_step = 1.0f / 60.0f;
    for (int i = 0; i < 60; ++i) {
        world.Step(time_step, 8, 3);
    }

    return 0;
}
```

### 总结

- Fixture = 形状 + 材质（密度、摩擦、弹性），绑定到 Body 上。
- 一个 Body 可有多个 Fixture，组合成复杂形状（如锤子、人物）。
- 总质量 = 所有 fixture 的密度 × 面积之和，质心自动计算。
- **形状生命周期**：形状对象由用户管理，`CreateFixture` 内部复制数据，函数返回后形状可销毁。
- **常见坑**：忘记设置 density（默认 0，动态刚体会无质量导致 NaN）；多个 fixture 的局部偏移用 `SetAsBox(hx, hy, center, angle)` 的第三、四参数。
- **设计建议**：相同形状的多个刚体可共享同一个 shape 对象（节省内存）；不同材质的同一形状需分别创建 fixture。

---

# 第 2 章 · 形状与材质

形状决定了物体的碰撞边界，材质决定了物体的物理行为。本章深入讲解 Box2D 的四种形状（圆形、多边形、边缘、链）的创建与适用场景，以及密度、摩擦、弹性三大材质参数的调参技巧。掌握这些，你就能为各种物体选择最合适的形状与材质组合，让物理模拟既准确又高效。

## 第 05 讲 · 圆形与多边形形状

### 概念

**圆形（b2CircleShape）** 是最简单的形状，用圆心和半径定义。圆形的碰撞检测最快（仅需距离比较），且旋转不变（无论怎么转都是同一个圆），适合子弹、球类、粒子等各向同性物体。

**多边形（b2PolygonShape）** 是凸多边形，最多 8 个顶点。Box2D 只支持**凸多边形**（任意两顶点连线都在内部），不支持凹多边形。多边形适合矩形平台、箱子、角色身体等有明确边界的物体。

### 原理

**圆形的内部表示**：

```cpp
struct b2CircleShape {
    b2Vec2 m_p;      // 圆心相对刚体原点的偏移
    float m_radius;  // 半径
};
```

圆形的碰撞检测：两圆心距离 < 半径和。这是 O(1) 操作，比任何多边形都快。

**多边形的限制**：

1. **必须是凸多边形**：凹多边形需分解为多个凸多边形（凸分解），或用多个 fixture 组合。
2. **最多 8 个顶点**：超过 8 个顶点需分解。
3. **顶点必须逆时针排列**（CCW）：Box2D 内部依赖顶点顺序计算法向量。
4. **不能自相交**：边与边不能交叉。

**多边形的创建方式**：

```cpp
// 方式 1：矩形（最常用）
b2PolygonShape box;
box.SetAsBox(half_width, half_height);  // 注意是"半宽半高"

// 方式 2：带位置和旋转的矩形
box.SetAsBox(half_width, half_height, b2Vec2(offset_x, offset_y), rotation);

// 方式 3：任意凸多边形
b2Vec2 vertices[3] = { b2Vec2(0,0), b2Vec2(1,0), b2Vec2(0,1) };
b2PolygonShape triangle;
triangle.Set(vertices, 3);  // 顶点数组 + 顶点数
```

**SetAsBox 的"半宽半高"**：这是 Box2D 的设计——传入的是半宽半高，而非全宽全高。`SetAsBox(2, 1)` 创建的是 4m × 2m 的矩形。这容易混淆，务必注意。

**多边形的质心**：`Set()` 方法会自动计算质心并调整顶点，使形状的质心位于原点。这意味着创建后顶点坐标可能与传入的不同（被平移了）。可用 `GetVertex(i)` 获取调整后的顶点。

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>

int main() {
    b2World world(b2Vec2(0.0f, 9.8f));

    // 地面
    b2BodyDef ground_def;
    ground_def.position.Set(0.0f, 10.0f);
    b2Body* ground = world.CreateBody(&ground_def);
    b2PolygonShape ground_shape;
    ground_shape.SetAsBox(50.0f, 1.0f);
    ground->CreateFixture(&ground_shape, 0.0f);

    // ========== 1. 圆形 ==========
    b2BodyDef circle_def;
    circle_def.type = b2_dynamicBody;
    circle_def.position.Set(-5.0f, 0.0f);
    b2Body* circle_body = world.CreateBody(&circle_def);

    b2CircleShape circle;
    circle.m_p.Set(0.0f, 0.0f);  // 圆心在刚体原点
    circle.m_radius = 0.5f;       // 半径 0.5m
    b2FixtureDef circle_fix;
    circle_fix.shape = &circle;
    circle_fix.density = 1.0f;
    circle_fix.restitution = 0.8f;
    circle_body->CreateFixture(&circle_fix);

    // ========== 2. 矩形（SetAsBox） ==========
    b2BodyDef box_def;
    box_def.type = b2_dynamicBody;
    box_def.position.Set(-2.0f, 0.0f);
    b2Body* box_body = world.CreateBody(&box_def);

    b2PolygonShape box;
    box.SetAsBox(1.0f, 0.5f);  // 半宽 1m，半高 0.5m → 实际 2m × 1m
    b2FixtureDef box_fix;
    box_fix.shape = &box;
    box_fix.density = 1.0f;
    box_body->CreateFixture(&box_fix);

    printf("Box vertex count: %d\n", box.GetVertexCount());  // 4
    printf("Box vertex 0: (%.2f, %.2f)\n",
           box.GetVertex(0).x, box.GetVertex(0).y);  // (-1, -0.5)

    // ========== 3. 任意三角形 ==========
    b2BodyDef tri_def;
    tri_def.type = b2_dynamicBody;
    tri_def.position.Set(2.0f, 0.0f);
    b2Body* tri_body = world.CreateBody(&tri_def);

    b2Vec2 tri_vertices[3] = {
        b2Vec2(0.0f, 0.0f),
        b2Vec2(1.0f, 0.0f),
        b2Vec2(0.5f, 1.0f)
    };
    b2PolygonShape triangle;
    triangle.Set(tri_vertices, 3);
    // 注意：Set 会自动调整顶点使质心在原点
    printf("Triangle centroid: (%.2f, %.2f)\n",
           triangle.m_centroid.x, triangle.m_centroid.y);

    b2FixtureDef tri_fix;
    tri_fix.shape = &triangle;
    tri_fix.density = 1.0f;
    tri_body->CreateFixture(&tri_fix);

    // ========== 4. 任意凸多边形（六边形） ==========
    b2BodyDef hex_def;
    hex_def.type = b2_dynamicBody;
    hex_def.position.Set(5.0f, 0.0f);
    b2Body* hex_body = world.CreateBody(&hex_def);

    b2Vec2 hex_vertices[6];
    for (int i = 0; i < 6; ++i) {
        float angle = i * b2_pi / 3.0f;  // 60 度间隔
        hex_vertices[i] = b2Vec2(0.5f * cosf(angle), 0.5f * sinf(angle));
    }
    b2PolygonShape hexagon;
    hexagon.Set(hex_vertices, 6);
    b2FixtureDef hex_fix;
    hex_fix.shape = &hexagon;
    hex_fix.density = 1.0f;
    hex_body->CreateFixture(&hex_fix);

    // ========== 5. 带偏移的矩形（多 fixture 组合） ==========
    // 创建一个 L 形物体：两个矩形组合
    b2BodyDef l_def;
    l_def.type = b2_dynamicBody;
    l_def.position.Set(8.0f, 0.0f);
    b2Body* l_body = world.CreateBody(&l_def);

    // 竖直部分
    b2PolygonShape l_vert;
    l_vert.SetAsBox(0.5f, 1.5f, b2Vec2(-0.5f, 0.0f), 0.0f);
    l_body->CreateFixture(&l_vert, 1.0f);

    // 水平部分
    b2PolygonShape l_horiz;
    l_horiz.SetAsBox(1.0f, 0.5f, b2Vec2(0.0f, -1.0f), 0.0f);
    l_body->CreateFixture(&l_horiz, 1.0f);

    printf("L-shape mass: %.2f kg\n", l_body->GetMass());

    // 模拟
    float time_step = 1.0f / 60.0f;
    for (int i = 0; i < 120; ++i) {
        world.Step(time_step, 8, 3);
    }

    return 0;
}
```

### 总结

- 圆形最快、旋转不变，适合球类、子弹；多边形适合有边界的物体。
- 多边形限制：凸、≤8 顶点、逆时针、不自交。
- `SetAsBox(hx, hy)` 传入的是**半宽半高**，`SetAsBox(2, 1)` 实际是 4m × 2m。
- `Set(vertices, count)` 会自动调整顶点使质心在原点，用 `GetVertex(i)` 获取调整后的顶点。
- **凹多边形处理**：分解为多个凸多边形（多 fixture），或用 b2ChainShape（第 08 讲）。
- **常见坑**：顶点顺时针排列会导致法向量反向，碰撞异常；超过 8 顶点会断言失败。

---

## 第 06 讲 · 密度、摩擦与弹性

### 概念

材质三要素决定了物体的物理行为：

- **密度（Density）**：单位面积的质量（kg/m²）。密度 × 面积 = 质量。密度越大，物体越重，越难被推动。
- **摩擦（Friction）**：表面粗糙程度 [0, 1]。摩擦越大，物体越难滑动。两物体接触时的有效摩擦 = √(f1 × f2)。
- **弹性（Restitution）**：碰撞时的能量保留程度 [0, 1]。弹性越大，反弹越高。两物体接触时的有效弹性 = max(r1, r2)。

### 原理

**密度的物理意义**：

```
质量 = 密度 × 面积
```

对于 2D 物体，"面积"是形状的二维面积。一个 1m × 1m 的矩形，密度 1 kg/m²，质量 = 1 kg。密度 5 kg/m²，质量 = 5 kg。

**典型密度值**（kg/m²）：

| 材质 | 密度 |
|------|------|
| 木头 | 0.3 ~ 0.7 |
| 水 | 1.0 |
| 石头 | 2.5 ~ 3.0 |
| 金属 | 5.0 ~ 10.0 |
| 金 | 19.3 |

**摩擦的混合规则**：Box2D 默认使用几何平均 `√(f1 × f2)`。这意味着一个摩擦 0 的物体与任何物体接触都无摩擦（像冰面）。可通过自定义 `b2ContactFilter`（第 16 讲）改变混合规则。

**典型摩擦值**：

| 表面 | 摩擦 |
|------|------|
| 冰 | 0.02 |
| 木头 | 0.3 ~ 0.5 |
| 橡胶 | 0.8 ~ 0.9 |
| 粗糙地面 | 0.7 ~ 1.0 |

**弹性的混合规则**：Box2D 2.x 默认使用 `max(r1, r2)`（取较大者）。这意味着一个弹性 1 的球与任何表面碰撞都会完全反弹。也可通过 `b2ContactFilter` 自定义。

**典型弹性值**：

| 材质 | 弹性 |
|------|------|
| 湿泥巴 | 0.1 |
| 木头 | 0.3 |
| 橡胶球 | 0.8 ~ 0.9 |
| 完美弹性 | 1.0 |

**弹性陷阱**：弹性 = 1（完全弹性）会导致物体永远弹跳，能量不损失。在大多数游戏中，弹性设为 0.3~0.7 更真实。弹性 > 1 会让物体每次碰撞获得能量，违反物理定律，应避免。

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>

int main() {
    b2World world(b2Vec2(0.0f, 9.8f));

    // ========== 1. 不同材质的地面 ==========
    struct GroundMaterial { const char* name; float friction; float restitution; };
    GroundMaterial mats[] = {
        {"Ice",     0.02f, 0.1f},
        {"Wood",    0.4f,  0.3f},
        {"Rubber",  0.9f,  0.8f},
        {"Stone",   0.7f,  0.2f}
    };

    for (int i = 0; i < 4; ++i) {
        b2BodyDef gd;
        gd.position.Set(-7.5f + i * 5.0f, 10.0f);
        b2Body* g = world.CreateBody(&gd);
        b2PolygonShape gs;
        gs.SetAsBox(2.0f, 0.5f);
        b2FixtureDef gf;
        gf.shape = &gs;
        gf.friction = mats[i].friction;
        gf.restitution = mats[i].restitution;
        g->CreateFixture(&gf);
    }

    // ========== 2. 相同球落在不同地面上 ==========
    b2CircleShape ball_shape;
    ball_shape.m_radius = 0.3f;

    for (int i = 0; i < 4; ++i) {
        b2BodyDef bd;
        bd.type = b2_dynamicBody;
        bd.position.Set(-7.5f + i * 5.0f, 5.0f);
        b2Body* ball = world.CreateBody(&bd);

        b2FixtureDef bf;
        bf.shape = &ball_shape;
        bf.density = 1.0f;
        bf.friction = 0.5f;       // 球的摩擦
        bf.restitution = 0.6f;    // 球的弹性
        ball->CreateFixture(&bf);

        // 给球一个水平初速度，测试摩擦
        ball->SetLinearVelocity(b2Vec2(2.0f, 0.0f));
    }

    // ========== 3. 不同密度的箱子堆叠 ==========
    // 密度大的箱子更重，压在下面变形更小
    float densities[] = {0.5f, 1.0f, 3.0f, 7.0f};
    for (int i = 0; i < 4; ++i) {
        b2BodyDef bd;
        bd.type = b2_dynamicBody;
        bd.position.Set(15.0f, 5.0f + i * 1.2f);
        b2Body* box = world.CreateBody(&bd);

        b2PolygonShape bs;
        bs.SetAsBox(0.5f, 0.5f);
        b2FixtureDef bf;
        bf.shape = &bs;
        bf.density = densities[i];
        bf.friction = 0.5f;
        box->CreateFixture(&bf);

        printf("Box %d: density=%.1f, mass=%.2f kg\n",
               i, densities[i], box->GetMass());
    }

    // ========== 4. 模拟并观察行为差异 ==========
    float time_step = 1.0f / 60.0f;
    for (int i = 0; i < 180; ++i) {
        world.Step(time_step, 8, 3);

        if (i == 120) {  // 2 秒后打印位置
            printf("\nAfter 2 seconds:\n");
            for (b2Body* b = world.GetBodyList(); b; b = b->GetNext()) {
                if (b->GetType() == b2_dynamicBody) {
                    b2Vec2 v = b->GetLinearVelocity();
                    printf("  pos=(%.2f, %.2f) vel=(%.2f, %.2f)\n",
                           b->GetPosition().x, b->GetPosition().y, v.x, v.y);
                }
            }
        }
    }

    return 0;
}
```

**预期行为**：

- 冰面上的球滑得最远（低摩擦），橡胶面上很快停下。
- 橡胶面上的球弹得最高（高弹性），石头面上几乎不弹。
- 密度大的箱子更重，堆叠时压得更稳。

### 总结

- 密度决定质量（质量 = 密度 × 面积），木头 0.5、金属 7、金 19。
- 摩擦混合用几何平均 `√(f1·f2)`，冰 0.02、橡胶 0.9。
- 弹性混合用最大值 `max(r1, r2)`，橡胶 0.9、泥巴 0.1。
- **弹性 = 1 会导致永远弹跳**，弹性 > 1 违反物理，应避免。
- **调参建议**：玩家角色 friction=0.6~0.9（站得稳）、restitution=0（不弹跳）；弹球 restitution=0.8~0.95；子弹 restitution=0.1（撞击后基本停止）。
- **常见坑**：所有物体都用默认 friction=0.2、restitution=0，导致物理感"塑料化"；应根据材质差异化设置。

---

## 第 07 讲 · 边缘形状 b2EdgeShape

### 概念

**b2EdgeShape** 是一条线段，由两个端点定义。它**没有内部**——物体只能从一侧碰撞，不会"陷入"线段内部。边缘形状适合：

- 地形边界（单向平台）
- 墙壁
- 单向门（只允许从一侧通过）

边缘形状是**单向碰撞**的：它有"正面"和"背面"，物体只能从正面碰撞。这使得它特别适合做"单向平台"——玩家可以从下方跳上去，落到上面时被挡住。

### 原理

**边缘形状的内部表示**：

```cpp
struct b2EdgeShape {
    b2Vec2 m_vertex1;  // 起点
    b2Vec2 m_vertex2;  // 终点
    b2Vec2 m_vertex0;  // 前一个相邻顶点（用于平滑连接，可选）
    b2Vec2 m_vertex3;  // 后一个相邻顶点（用于平滑连接，可选）
    bool m_hasVertex0, m_hasVertex3;
};
```

`m_vertex0` 和 `m_vertex3` 是"幽灵顶点"，用于相邻边缘的平滑连接。如果不设置，相邻边缘的连接处会有"尖角"，物体经过时会卡住。设置后，Box2D 会用幽灵顶点计算虚拟法向量，让物体平滑过渡。

**单向碰撞的原理**：边缘形状的法向量指向"正面"（从 v1 到 v2 的逆时针方向）。物体只有从正面接近时才会碰撞，从背面接近会直接穿过。

**边缘形状 vs 多边形**：

| 特性 | b2EdgeShape | b2PolygonShape |
|------|-------------|----------------|
| 形状 | 线段（无内部） | 凸多边形（有内部） |
| 碰撞 | 单向 | 双向 |
| 适合 | 地形边界、墙壁 | 实体物体 |
| 连接 | 需幽灵顶点平滑 | 独立 |

**性能**：边缘形状的碰撞检测比多边形略快（少一个面），但差异不大。选择边缘形状的主要原因是**单向碰撞**特性，而非性能。

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>

int main() {
    b2World world(b2Vec2(0.0f, 9.8f));

    // ========== 1. 简单边缘形状：地面 ==========
    b2BodyDef ground_def;
    ground_def.position.Set(0.0f, 0.0f);
    b2Body* ground = world.CreateBody(&ground_def);

    b2EdgeShape ground_edge;
    ground_edge.SetTwoSided(b2Vec2(-20.0f, 10.0f), b2Vec2(20.0f, 10.0f));
    ground->CreateFixture(&ground_edge, 0.0f);

    // ========== 2. 单向平台（玩家可从下方穿过） ==========
    b2BodyDef platform_def;
    platform_def.position.Set(0.0f, 0.0f);
    b2Body* platform = world.CreateBody(&platform_def);

    b2EdgeShape platform_edge;
    // SetOneSided: 只有从上方碰撞有效
    // 参数：前一个幽灵顶点、起点、终点、后一个幽灵顶点
    platform_edge.SetOneSided(
        b2Vec2(-5.0f, 5.0f),   // 幽灵顶点 0（左侧延伸）
        b2Vec2(-3.0f, 5.0f),   // 起点
        b2Vec2(3.0f, 5.0f),    // 终点
        b2Vec2(5.0f, 5.0f)     // 幽灵顶点 3（右侧延伸）
    );
    platform->CreateFixture(&platform_edge, 0.0f);

    // ========== 3. 多段连接的边缘（地形轮廓） ==========
    b2BodyDef terrain_def;
    terrain_def.position.Set(0.0f, 0.0f);
    b2Body* terrain = world.CreateBody(&terrain_def);

    // 地形高度点
    float terrain_points[][2] = {
        {-15, 8}, {-10, 9}, {-5, 7}, {0, 8}, {5, 10}, {10, 8}, {15, 9}
    };
    int num_points = 7;

    for (int i = 0; i < num_points - 1; ++i) {
        b2EdgeShape segment;
        b2Vec2 v1(terrain_points[i][0], terrain_points[i][1]);
        b2Vec2 v2(terrain_points[i+1][0], terrain_points[i+1][1]);

        if (i == 0) {
            // 第一段：无前幽灵顶点
            segment.SetTwoSided(v1, v2);
        } else if (i == num_points - 2) {
            // 最后一段：无后幽灵顶点
            segment.SetTwoSided(v1, v2);
        } else {
            // 中间段：设置幽灵顶点实现平滑连接
            b2Vec2 v0(terrain_points[i-1][0], terrain_points[i-1][1]);
            b2Vec2 v3(terrain_points[i+2][0], terrain_points[i+2][1]);
            segment.SetOneSided(v0, v1, v2, v3);
        }
        terrain->CreateFixture(&segment, 0.0f);
    }

    // ========== 4. 测试球落在地形上 ==========
    b2CircleShape ball_shape;
    ball_shape.m_radius = 0.3f;
    for (int i = 0; i < 5; ++i) {
        b2BodyDef bd;
        bd.type = b2_dynamicBody;
        bd.position.Set(-12.0f + i * 6.0f, 0.0f);
        b2Body* ball = world.CreateBody(&bd);
        b2FixtureDef bf;
        bf.shape = &ball_shape;
        bf.density = 1.0f;
        bf.friction = 0.5f;
        bf.restitution = 0.3f;
        ball->CreateFixture(&bf);
    }

    // 模拟
    float time_step = 1.0f / 60.0f;
    for (int i = 0; i < 180; ++i) {
        world.Step(time_step, 8, 3);
    }

    return 0;
}
```

**API 说明**（Box2D 2.4+）：

- `SetTwoSided(v1, v2)`：双向碰撞，物体可从两侧碰撞。
- `SetOneSided(v0, v1, v2, v3)`：单向碰撞，v0/v3 是幽灵顶点用于平滑。

### 总结

- 边缘形状是线段，无内部，适合地形边界、单向平台。
- **单向碰撞**：物体只能从正面碰撞，背面穿过——适合"可跳穿平台"。
- **幽灵顶点** v0/v3 用于相邻边缘平滑连接，避免物体在连接处卡住。
- `SetTwoSided` 双向碰撞，`SetOneSided` 单向碰撞（Box2D 2.4+ API）。
- **应用场景**：平台游戏的地面与平台、地形轮廓、墙壁边界。
- **常见坑**：相邻边缘不设幽灵顶点会导致物体在连接处卡住或弹飞；单向平台的方向由顶点顺序决定（逆时针为正面）。

---

## 第 08 讲 · 链形状 b2ChainShape

### 概念

**b2ChainShape** 是连接多个线段的链，由一系列顶点定义。它是 b2EdgeShape 的"批量版"——一条链等价于多条相连的边缘，但内部自动处理幽灵顶点，无需手动设置。

链形状适合**长而曲折的地形**：山脉轮廓、河流边界、赛道边缘。相比手动创建多条 EdgeShape，ChainShape 更简洁，且自动平滑连接。

### 原理

**链形状的两种模式**：

1. **开放链（Open Chain）**：首尾不相连，是一条折线。用 `CreateChain(vertices, count)` 创建。
2. **闭合链（Closed Loop）**：首尾相连，形成闭合环。用 `CreateLoop(vertices, count)` 创建。

**内部实现**：ChainShape 内部存储所有顶点，并为每条子线段计算幽灵顶点。碰撞检测时，它等价于多条 EdgeShape，但共享数据，内存更紧凑。

**碰撞方向**：

- 开放链：法向量指向顶点顺序的逆时针方向。
- 闭合链：法向量指向环的外部（如果顶点逆时针排列）或内部（顺时针排列）。

**与 EdgeShape 的对比**：

| 特性 | b2ChainShape | b2EdgeShape |
|------|--------------|-------------|
| 形状 | 多段相连线段 | 单条线段 |
| 幽灵顶点 | 自动处理 | 需手动设置 |
| 适合 | 长地形 | 单段边界 |
| API | 简洁 | 繁琐 |
| 灵活性 | 低（整体管理） | 高（每段独立） |

**性能**：ChainShape 与多个 EdgeShape 性能相当，但 ChainShape 内存更紧凑，创建更方便。

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>
#include <cmath>

int main() {
    b2World world(b2Vec2(0.0f, 9.8f));

    // ========== 1. 开放链：山脉地形 ==========
    b2BodyDef terrain_def;
    terrain_def.position.Set(0.0f, 0.0f);
    b2Body* terrain = world.CreateBody(&terrain_def);

    // 生成正弦波地形
    std::vector<b2Vec2> terrain_points;
    for (int i = 0; i <= 40; ++i) {
        float x = -20.0f + i * 1.0f;
        float y = 8.0f + 2.0f * sinf(i * 0.3f);  // 正弦波
        terrain_points.push_back(b2Vec2(x, y));
    }

    b2ChainShape terrain_chain;
    terrain_chain.CreateChain(terrain_points.data(), terrain_points.size());
    terrain->CreateFixture(&terrain_chain, 0.0f);

    // ========== 2. 闭合链：圆形围墙 ==========
    b2BodyDef wall_def;
    wall_def.position.Set(0.0f, 0.0f);
    b2Body* wall = world.CreateBody(&wall_def);

    std::vector<b2Vec2> wall_points;
    for (int i = 0; i < 32; ++i) {
        float angle = i * 2.0f * b2_pi / 32.0f;
        wall_points.push_back(b2Vec2(
            15.0f * cosf(angle),
            15.0f * sinf(angle) + 10.0f  // 中心在 (0, 10)
        ));
    }

    b2ChainShape wall_loop;
    wall_loop.CreateLoop(wall_points.data(), wall_points.size());
    wall->CreateFixture(&wall_loop, 0.0f);

    // ========== 3. 在地形上撒球 ==========
    b2CircleShape ball_shape;
    ball_shape.m_radius = 0.3f;
    for (int i = 0; i < 10; ++i) {
        b2BodyDef bd;
        bd.type = b2_dynamicBody;
        bd.position.Set(-15.0f + i * 3.0f, 15.0f);
        b2Body* ball = world.CreateBody(&bd);
        b2FixtureDef bf;
        bf.shape = &ball_shape;
        bf.density = 1.0f;
        bf.friction = 0.5f;
        bf.restitution = 0.3f;
        ball->CreateFixture(&bf);
    }

    // ========== 4. 模拟 ==========
    float time_step = 1.0f / 60.0f;
    for (int i = 0; i < 300; ++i) {
        world.Step(time_step, 8, 3);

        if (i % 60 == 0) {
            printf("t=%.1fs:\n", i * time_step);
            for (b2Body* b = world.GetBodyList(); b; b = b->GetNext()) {
                if (b->GetType() == b2_dynamicBody) {
                    printf("  ball at (%.2f, %.2f)\n",
                           b->GetPosition().x, b->GetPosition().y);
                }
            }
        }
    }

    return 0;
}
```

**预期行为**：球落在正弦波地形上，沿地形滚动；圆形围墙防止球滚出场景。

### 总结

- 链形状是连接多线段的链，自动处理幽灵顶点，适合长地形。
- `CreateChain` 开放链（折线），`CreateLoop` 闭合链（环）。
- 法向量指向顶点顺序的逆时针方向，决定碰撞方向。
- **与 EdgeShape 选择**：长地形用 ChainShape（简洁），单段边界用 EdgeShape（灵活）。
- **应用场景**：山脉、河流、赛道、围墙、任意曲线地形。
- **常见坑**：顶点顺序错误会导致法向量反向，物体从错误方向碰撞；闭合链的顶点数不能少于 3。

---

# 第 3 章 · 力与运动

物理引擎的核心价值在于模拟物体在力作用下的运动。本章将系统讲解 Box2D 中施加力与冲量的各种方式、速度的直接控制、阻尼与休眠机制，以及重力缩放与浮力等高级效果。掌握这些，你就能精确控制物体的运动行为——从角色跳跃到火箭推进，从漂浮气球到沉入水底。

## 第 09 讲 · 施加力与冲量

### 概念

**力（Force）** 是持续作用的推力，通过加速度改变速度：`a = F/m`。力在 `Step` 期间持续作用，适合模拟重力、推力、风力等持续效果。

**冲量（Impulse）** 是瞬时作用的力脉冲，直接改变速度：`Δv = J/m`。冲量在单帧内立即生效，适合模拟跳跃、爆炸、碰撞响应等瞬时事件。

Box2D 提供四种施加方式：力（持续）、冲量（瞬时）、力到质心、冲量到质心。选择哪种取决于应用场景。

### 原理

**力的施加方式**：

```cpp
// 在世界坐标点施加力（会产生力矩，导致旋转）
body->ApplyForce(force, point, wake);

// 在质心施加力（不产生力矩，纯平移）
body->ApplyForceToCenter(force, wake);

// 在世界坐标点施加冲量（会产生角冲量，导致旋转）
body->ApplyLinearImpulse(impulse, point, wake);

// 在质心施加冲量（不产生旋转）
body->ApplyLinearImpulseToCenter(impulse, wake);

// 施加角冲量（直接改变角速度）
body->ApplyAngularImpulse(impulse, wake);
```

**力与冲量的区别**：

| 特性 | 力 (Force) | 冲量 (Impulse) |
|------|-----------|----------------|
| 作用时间 | 持续（每帧施加） | 瞬时（单帧） |
| 单位 | 牛顿 N (kg·m/s²) | 冲量 (kg·m/s) |
| 效果 | 通过加速度改变速度 | 直接改变速度 |
| 适合 | 重力、推力、风力 | 跳跃、爆炸、击退 |
| 累积 | 在 Step 中累积，Step 后清零 | 立即生效 |

**力的累积机制**：Box2D 在内部累积每帧施加的力，在 `Step` 时统一应用，然后清零。这意味着力必须**每帧重新施加**，否则不会持续作用。

**wake 参数**：如果刚体处于休眠状态（第 11 讲），施加力/冲量时需设 `wake=true` 唤醒它，否则力不会生效。

**力矩的产生**：力作用在非质心点时会产生力矩 `τ = r × F`，导致旋转。例如推门的把手（远离铰链）比推门轴更容易开门。

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>

int main() {
    b2World world(b2Vec2(0.0f, 0.0f));  // 无重力，便于观察力效果

    // ========== 1. 持续推力（火箭） ==========
    b2BodyDef rocket_def;
    rocket_def.type = b2_dynamicBody;
    rocket_def.position.Set(0.0f, 0.0f);
    b2Body* rocket = world.CreateBody(&rocket_def);
    b2PolygonShape rocket_shape;
    rocket_shape.SetAsBox(0.5f, 1.0f);
    b2FixtureDef rocket_fix;
    rocket_fix.shape = &rocket_shape;
    rocket_fix.density = 1.0f;
    rocket->CreateFixture(&rocket_fix);

    printf("Rocket mass: %.2f kg\n", rocket->GetMass());

    // ========== 2. 跳跃冲量 ==========
    b2BodyDef player_def;
    player_def.type = b2_dynamicBody;
    player_def.position.Set(5.0f, 0.0f);
    b2Body* player = world.CreateBody(&player_def);
    b2PolygonShape player_shape;
    player_shape.SetAsBox(0.4f, 0.8f);
    b2FixtureDef player_fix;
    player_fix.shape = &player_shape;
    player_fix.density = 1.0f;
    player->CreateFixture(&player_fix);

    // 跳跃：施加向上冲量
    // 想跳 2m 高，v = sqrt(2 * g * h) = sqrt(2 * 9.8 * 2) ≈ 6.26 m/s
    // 冲量 J = m * Δv = mass * 6.26
    float jump_height = 2.0f;
    float gravity = 9.8f;
    float jump_velocity = sqrtf(2.0f * gravity * jump_height);
    float jump_impulse = player->GetMass() * jump_velocity;
    player->ApplyLinearImpulseToCenter(b2Vec2(0.0f, -jump_impulse), true);
    printf("Jump impulse: %.2f (velocity: %.2f m/s)\n",
           jump_impulse, jump_velocity);

    // ========== 3. 力矩（旋转） ==========
    b2BodyDef bar_def;
    bar_def.type = b2_dynamicBody;
    bar_def.position.Set(10.0f, 0.0f);
    b2Body* bar = world.CreateBody(&bar_def);
    b2PolygonShape bar_shape;
    bar_shape.SetAsBox(2.0f, 0.2f);  // 长条
    b2FixtureDef bar_fix;
    bar_fix.shape = &bar_shape;
    bar_fix.density = 1.0f;
    bar->CreateFixture(&bar_fix);

    // 在一端施加力，产生旋转
    b2Vec2 force(0.0f, 10.0f);  // 向上的力
    b2Vec2 point = bar->GetWorldPoint(b2Vec2(2.0f, 0.0f));  // 右端
    bar->ApplyForce(force, point, true);

    // ========== 4. 模拟 ==========
    float time_step = 1.0f / 60.0f;
    for (int i = 0; i < 120; ++i) {
        // 火箭持续推力（每帧施加）
        if (i < 60) {  // 前 1 秒推进
            rocket->ApplyForceToCenter(b2Vec2(0.0f, -20.0f), true);
        }

        world.Step(time_step, 8, 3);

        if (i % 30 == 0) {
            printf("t=%.1fs: rocket=(%.2f,%.2f) v=%.2f | player=(%.2f,%.2f) | bar_angle=%.2f\n",
                   i * time_step,
                   rocket->GetPosition().x, rocket->GetPosition().y,
                   rocket->GetLinearVelocity().Length(),
                   player->GetPosition().x, player->GetPosition().y,
                   bar->GetAngle());
        }
    }

    return 0;
}
```

### 总结

- 力持续作用（每帧施加），冲量瞬时作用（单帧生效）。
- `ApplyForce` 产生力矩（旋转），`ApplyForceToCenter` 纯平移。
- 力在 `Step` 后清零，必须每帧重新施加才能持续。
- `wake=true` 唤醒休眠刚体，否则力不生效。
- **跳跃公式**：`v = √(2gh)`，`J = m·v`。
- **应用场景**：火箭推力（力）、角色跳跃（冲量）、爆炸击退（冲量）、风力（力）。

---

## 第 10 讲 · 线性速度与角速度

### 概念

除了通过力间接控制运动，Box2D 也允许**直接设置速度**。这种方式跳过物理积分，立即改变物体状态，适合需要精确控制的场景：传送带、平台移动、角色瞬移、子弹发射。

**线性速度（Linear Velocity）** 是物体的平移速度（m/s）。**角速度（Angular Velocity）** 是物体的旋转速度（rad/s）。

### 原理

**速度的获取与设置**：

```cpp
// 线性速度
b2Vec2 GetLinearVelocity() const;
void SetLinearVelocity(const b2Vec2& v);

// 角速度
float GetAngularVelocity() const;
void SetAngularVelocity(float omega);
```

**直接设置速度 vs 施加力**：

| 方式 | 优点 | 缺点 | 适合 |
|------|------|------|------|
| 施加力 | 物理真实，受质量影响 | 控制不精确 | 推力、重力 |
| 直接设置速度 | 精确控制 | 破坏物理真实 | 传送带、平台 |
| 施加冲量 | 物理真实，瞬时 | 需计算冲量大小 | 跳跃、击退 |

**传送带原理**：传送带表面有速度，物体接触时受摩擦力带动。实现方式是设置刚体的 `SetLinearVelocity`，但保持 `type = b2_kinematicBody`，这样它不受力影响但能带动其他物体。

**SetTransform 的陷阱**：`SetTransform(pos, angle)` 直接瞬移物体，会破坏物理连续性，可能导致穿透或卡住。仅在必要时使用（如重置位置），且瞬移后需调用 `SetAwake(true)`。

**速度阻尼**：直接设置速度后，物体仍受线性阻尼影响逐渐减速。若想保持恒定速度，需每帧重新设置。

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>

int main() {
    b2World world(b2Vec2(0.0f, 9.8f));

    // ========== 1. 传送带（运动学刚体） ==========
    b2BodyDef conveyor_def;
    conveyor_def.type = b2_kinematicBody;
    conveyor_def.position.Set(0.0f, 5.0f);
    b2Body* conveyor = world.CreateBody(&conveyor_def);
    b2PolygonShape conveyor_shape;
    conveyor_shape.SetAsBox(5.0f, 0.5f);
    b2FixtureDef conveyor_fix;
    conveyor_fix.shape = &conveyor_shape;
    conveyor_fix.friction = 0.9f;  // 高摩擦，带动物体
    conveyor->CreateFixture(&conveyor_fix);

    // 设置传送带速度（向右）
    conveyor->SetLinearVelocity(b2Vec2(3.0f, 0.0f));

    // ========== 2. 移动平台（往返） ==========
    b2BodyDef platform_def;
    platform_def.type = b2_kinematicBody;
    platform_def.position.Set(-8.0f, 8.0f);
    b2Body* platform = world.CreateBody(&platform_def);
    b2PolygonShape platform_shape;
    platform_shape.SetAsBox(2.0f, 0.3f);
    b2FixtureDef platform_fix;
    platform_fix.shape = &platform_shape;
    platform_fix.friction = 0.7f;
    platform->CreateFixture(&platform_fix);

    // ========== 3. 发射子弹 ==========
    b2BodyDef bullet_def;
    bullet_def.type = b2_dynamicBody;
    bullet_def.position.Set(-10.0f, 3.0f);
    bullet_def.bullet = true;  // 启用 CCD（第 22 讲）
    b2Body* bullet = world.CreateBody(&bullet_def);
    b2CircleShape bullet_shape;
    bullet_shape.m_radius = 0.2f;
    b2FixtureDef bullet_fix;
    bullet_fix.shape = &bullet_shape;
    bullet_fix.density = 5.0f;  // 高密度，质量大
    bullet_fix.restitution = 0.3f;
    bullet->CreateFixture(&bullet_fix);

    // 直接设置速度发射
    bullet->SetLinearVelocity(b2Vec2(20.0f, 0.0f));  // 20 m/s 向右

    // ========== 4. 落在传送带上的箱子 ==========
    b2BodyDef box_def;
    box_def.type = b2_dynamicBody;
    box_def.position.Set(-3.0f, 3.0f);
    b2Body* box = world.CreateBody(&box_def);
    b2PolygonShape box_shape;
    box_shape.SetAsBox(0.5f, 0.5f);
    b2FixtureDef box_fix;
    box_fix.shape = &box_shape;
    box_fix.density = 1.0f;
    box_fix.friction = 0.7f;
    box->CreateFixture(&box_fix);

    // ========== 5. 模拟 ==========
    float time_step = 1.0f / 60.0f;
    float platform_time = 0.0f;

    for (int i = 0; i < 300; ++i) {
        // 移动平台往返运动
        platform_time += time_step;
        float platform_x = -8.0f + 4.0f * sinf(platform_time * 1.5f);
        float platform_vx = 4.0f * 1.5f * cosf(platform_time * 1.5f);
        platform->SetTransform(b2Vec2(platform_x, 8.0f), 0.0f);
        platform->SetLinearVelocity(b2Vec2(platform_vx, 0.0f));

        world.Step(time_step, 8, 3);

        if (i % 60 == 0) {
            printf("t=%.1fs: box=(%.2f,%.2f) v=(%.2f,%.2f) | bullet=(%.2f,%.2f)\n",
                   i * time_step,
                   box->GetPosition().x, box->GetPosition().y,
                   box->GetLinearVelocity().x, box->GetLinearVelocity().y,
                   bullet->GetPosition().x, bullet->GetPosition().y);
        }
    }

    return 0;
}
```

**预期行为**：箱子落在传送带上后被带动向右移动；子弹高速飞行；移动平台往返运动。

### 总结

- 直接设置速度跳过物理积分，适合精确控制（传送带、平台、子弹）。
- 传送带用 `b2_kinematicBody` + `SetLinearVelocity`，高摩擦带动物体。
- `SetTransform` 瞬移物体，破坏物理连续性，仅在必要时使用。
- **kinematic 刚体**不受力但能带动 dynamic 刚体，是移动平台的首选。
- **常见坑**：直接设置速度后物体仍受阻尼影响；瞬移后忘记 `SetAwake(true)` 导致物体休眠。

---

## 第 11 讲 · 阻尼与休眠

### 概念

**阻尼（Damping）** 模拟空气阻力与摩擦，让物体逐渐减速。Box2D 提供线性阻尼（Linear Damping）和角阻尼（Angular Damping），分别影响平移和旋转。

**休眠（Sleep）** 是性能优化机制：当物体长时间静止且速度极小时，Box2D 将其标记为"休眠"，跳过其物理计算。休眠物体不参与碰撞检测与求解，大幅提升性能。

### 原理

**阻尼的数学模型**：

Box2D 使用指数阻尼，每帧速度乘以衰减系数：

```
v(t+Δt) = v(t) × exp(-damping × Δt)
```

阻尼值越大，减速越快。阻尼 0 表示无阻力（永远运动），阻尼 1 表示较强阻力。

**典型阻尼值**：

| 场景 | 线性阻尼 | 角阻尼 |
|------|---------|--------|
| 真空 | 0 | 0 |
| 空气 | 0.1 ~ 0.5 | 0.1 ~ 0.5 |
| 水 | 1.0 ~ 3.0 | 1.0 ~ 3.0 |
| 糖浆 | 5.0+ | 5.0+ |

**休眠的触发条件**：

1. 速度低于阈值（`b2_linearSleepTolerance`，默认 0.01 m/s）
2. 角速度低于阈值（`b2_angularSleepTolerance`，默认 0.011 rad/s）
3. 持续时间超过 `b2_timeToSleep`（默认 0.5 秒）

**休眠的唤醒**：

- 施加力或冲量（`wake=true`）
- 被其他运动物体碰撞
- 手动调用 `SetAwake(true)`

**休眠的注意事项**：

- 休眠物体不参与碰撞检测，但被运动物体碰撞时会自动唤醒。
- 修改休眠物体的位置/速度不会自动唤醒，需手动 `SetAwake(true)`。
- 休眠物体不响应 `ApplyForce`（除非 `wake=true`）。

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>

int main() {
    b2World world(b2Vec2(0.0f, 9.8f));

    // ========== 1. 不同阻尼的球 ==========
    struct DampingTest { const char* name; float damping; };
    DampingTest tests[] = {
        {"No damping",   0.0f},
        {"Air",          0.3f},
        {"Water",        2.0f},
        {"Syrup",        8.0f}
    };

    b2BodyDef ground_def;
    ground_def.position.Set(0.0f, 10.0f);
    b2Body* ground = world.CreateBody(&ground_def);
    b2PolygonShape ground_shape;
    ground_shape.SetAsBox(50.0f, 1.0f);
    ground->CreateFixture(&ground_shape, 0.0f);

    b2Body* balls[4];
    for (int i = 0; i < 4; ++i) {
        b2BodyDef bd;
        bd.type = b2_dynamicBody;
        bd.position.Set(-15.0f + i * 10.0f, 0.0f);
        bd.linearDamping = tests[i].damping;
        balls[i] = world.CreateBody(&bd);

        b2CircleShape shape;
        shape.m_radius = 0.5f;
        b2FixtureDef fix;
        fix.shape = &shape;
        fix.density = 1.0f;
        fix.restitution = 0.5f;
        balls[i]->CreateFixture(&fix);

        // 给球一个水平初速度
        balls[i]->SetLinearVelocity(b2Vec2(10.0f, 0.0f));
    }

    // ========== 2. 休眠测试 ==========
    b2BodyDef sleep_def;
    sleep_def.type = b2_dynamicBody;
    sleep_def.position.Set(20.0f, 5.0f);
    sleep_def.allowSleep = true;  // 允许休眠（默认）
    b2Body* sleep_box = world.CreateBody(&sleep_def);
    b2PolygonShape sleep_shape;
    sleep_shape.SetAsBox(0.5f, 0.5f);
    b2FixtureDef sleep_fix;
    sleep_fix.shape = &sleep_shape;
    sleep_fix.density = 1.0f;
    sleep_box->CreateFixture(&sleep_fix);

    // ========== 3. 禁止休眠的对比 ==========
    b2BodyDef awake_def;
    awake_def.type = b2_dynamicBody;
    awake_def.position.Set(22.0f, 5.0f);
    awake_def.allowSleep = false;  // 禁止休眠
    b2Body* awake_box = world.CreateBody(&awake_def);
    b2PolygonShape awake_shape;
    awake_shape.SetAsBox(0.5f, 0.5f);
    b2FixtureDef awake_fix;
    awake_fix.shape = &awake_shape;
    awake_fix.density = 1.0f;
    awake_box->CreateFixture(&awake_fix);

    // ========== 4. 模拟 ==========
    float time_step = 1.0f / 60.0f;
    for (int i = 0; i < 300; ++i) {
        world.Step(time_step, 8, 3);

        if (i % 60 == 0) {
            printf("t=%.1fs:\n", i * time_step);
            for (int j = 0; j < 4; ++j) {
                printf("  %s: pos=(%.2f,%.2f) v=%.2f\n",
                       tests[j].name,
                       balls[j]->GetPosition().x, balls[j]->GetPosition().y,
                       balls[j]->GetLinearVelocity().Length());
            }
            printf("  sleep_box: awake=%d\n", sleep_box->IsAwake());
            printf("  awake_box: awake=%d\n", awake_box->IsAwake());
        }
    }

    return 0;
}
```

**预期行为**：

- 无阻尼球落地后持续滚动；高阻尼球很快停下。
- sleep_box 落地静止后 0.5 秒进入休眠（awake=0）。
- awake_box 永不休眠（awake=1）。

### 总结

- 阻尼模拟空气/液体阻力，指数衰减 `v *= exp(-d·Δt)`。
- 线性阻尼影响平移，角阻尼影响旋转；空气 0.3、水 2.0。
- 休眠是性能优化，静止物体跳过计算。
- 休眠条件：速度 < 阈值，持续 0.5 秒。
- **唤醒方式**：施加力（wake=true）、被碰撞、手动 `SetAwake(true)`。
- **常见坑**：休眠物体不响应力（除非 wake=true）；禁止休眠（allowSleep=false）会持续消耗 CPU。

---

## 第 12 讲 · 重力缩放与浮力

### 概念

**重力缩放（Gravity Scale）** 允许单独调整每个刚体受重力的程度。设为 0 表示不受重力（漂浮），设为 -1 表示反向重力（上升），设为 0.5 表示半重力（月球感）。

**浮力（Buoyancy）** 是流体对浸入物体的向上力。Box2D 没有内置浮力，需通过施加力模拟。浮力大小等于排开流体的重量，方向向上。

### 原理

**重力缩放的实现**：

Box2D 内部对每个刚体存储 `m_gravityScale`，施加重力时乘以该系数：

```
effective_gravity = world_gravity × gravity_scale
```

- `gravityScale = 1`：正常重力（默认）
- `gravityScale = 0`：无重力（气球、漂浮物）
- `gravityScale = -1`：反向重力（热气球上升）
- `gravityScale = 0.16`：月球重力（地球的 1/6）

**浮力的物理公式**：

```
F_buoyancy = ρ_fluid × V_submerged × g
```

其中 ρ_fluid 是流体密度，V_submerged 是浸入体积，g 是重力加速度。

**2D 浮力简化**：

在 2D 中，"体积"变为"面积"。假设物体是矩形，水面是水平线：

```
submerged_depth = clamp(water_level - body_bottom, 0, body_height)
submerged_area = body_width × submerged_depth
F_buoyancy = fluid_density × submerged_area × g (向上)
```

**阻力的附加**：浸入水中的物体还受水流阻力，可用阻尼模拟。进入水中的瞬间，给物体设置高线性阻尼；离开水时恢复。

**浮力的完整模拟**（含阻尼与角阻尼）：

1. 检测物体是否浸入水中（位置 < 水面）
2. 计算浸入深度与面积
3. 施加向上浮力 `F = ρ × A × g`
4. 施加水流阻力（与速度反向，大小与速度平方成正比）
5. 可选：施加角阻尼让旋转减速

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>
#include <algorithm>

// 水体区域
struct FluidArea {
    b2Vec2 center;
    b2Vec2 half_size;
    float density;  // 流体密度
};

// 计算刚体在水中的浸入面积（简化：仅支持矩形）
float compute_submerged_area(b2Body* body, const FluidArea& fluid) {
    float water_top = fluid.center.y - fluid.half_size.y;

    b2Vec2 pos = body->GetPosition();
    b2Fixture* f = body->GetFixtureList();
    if (f->GetShape()->GetType() != b2Shape::e_polygon) return 0.0f;

    b2PolygonShape* poly = (b2PolygonShape*)f->GetShape();
    // 获取世界坐标的 AABB（简化）
    b2Vec2 half_size = poly->m_vertices[0];  // 假设是 SetAsBox 创建
    float body_bottom = pos.y - 0.5f;  // 简化
    float body_top = pos.y + 0.5f;
    float body_width = 1.0f;  // 简化

    if (body_top <= water_top) return 0.0f;  // 完全在水面上
    if (body_bottom >= water_top) return 0.0f;  // 完全在水面上方（错误，应该是 >=）

    float submerged_depth = std::min(body_top, water_top) - body_bottom;
    submerged_depth = std::max(submerged_depth, 0.0f);
    return body_width * submerged_depth;
}

// 施加浮力
void apply_buoyancy(b2Body* body, const FluidArea& fluid, float gravity) {
    float submerged_area = compute_submerged_area(body, fluid);
    if (submerged_area <= 0.0f) return;

    // 浮力 = 密度 × 面积 × g（向上）
    float buoyancy_force = fluid.density * submerged_area * gravity;
    body->ApplyForceToCenter(b2Vec2(0.0f, -buoyancy_force), true);

    // 水流阻力（与速度反向）
    b2Vec2 vel = body->GetLinearVelocity();
    float drag_x = -fluid.density * submerged_area * vel.x * 0.5f;
    float drag_y = -fluid.density * submerged_area * vel.y * 0.5f;
    body->ApplyForceToCenter(b2Vec2(drag_x, drag_y), true);
}

int main() {
    b2World world(b2Vec2(0.0f, 9.8f));

    // ========== 1. 气球（反重力） ==========
    b2BodyDef balloon_def;
    balloon_def.type = b2_dynamicBody;
    balloon_def.position.Set(-5.0f, 10.0f);
    balloon_def.gravityScale = -0.5f;  // 反重力，缓慢上升
    b2Body* balloon = world.CreateBody(&balloon_def);
    b2CircleShape balloon_shape;
    balloon_shape.m_radius = 0.5f;
    b2FixtureDef balloon_fix;
    balloon_fix.shape = &balloon_shape;
    balloon_fix.density = 0.1f;  // 低密度
    balloon_fix.restitution = 0.8f;
    balloon->CreateFixture(&balloon_fix);

    // ========== 2. 月球重力球 ==========
    b2BodyDef moon_def;
    moon_def.type = b2_dynamicBody;
    moon_def.position.Set(0.0f, 10.0f);
    moon_def.gravityScale = 0.16f;  // 月球重力
    b2Body* moon_ball = world.CreateBody(&moon_def);
    b2CircleShape moon_shape;
    moon_shape.m_radius = 0.3f;
    b2FixtureDef moon_fix;
    moon_fix.shape = &moon_shape;
    moon_fix.density = 1.0f;
    moon_ball->CreateFixture(&moon_fix);

    // ========== 3. 水池 ==========
    FluidArea water = { b2Vec2(5.0f, 8.0f), b2Vec2(3.0f, 2.0f), 2.0f };

    // 水池底部
    b2BodyDef pool_floor_def;
    pool_floor_def.position.Set(5.0f, 10.0f);
    b2Body* pool_floor = world.CreateBody(&pool_floor_def);
    b2PolygonShape pool_shape;
    pool_shape.SetAsBox(3.0f, 0.5f);
    pool_floor->CreateFixture(&pool_shape, 0.0f);

    // 落入水中的箱子
    b2BodyDef box_def;
    box_def.type = b2_dynamicBody;
    box_def.position.Set(5.0f, 3.0f);
    b2Body* box = world.CreateBody(&box_def);
    b2PolygonShape box_shape;
    box_shape.SetAsBox(0.5f, 0.5f);
    b2FixtureDef box_fix;
    box_fix.shape = &box_shape;
    box_fix.density = 0.8f;  // 密度小于水，会浮起来
    box->CreateFixture(&box_fix);

    // ========== 4. 模拟 ==========
    float time_step = 1.0f / 60.0f;
    for (int i = 0; i < 300; ++i) {
        // 每帧施加浮力
        apply_buoyancy(box, water, 9.8f);

        world.Step(time_step, 8, 3);

        if (i % 60 == 0) {
            printf("t=%.1fs: balloon=(%.2f,%.2f) moon=(%.2f,%.2f) box=(%.2f,%.2f)\n",
                   i * time_step,
                   balloon->GetPosition().x, balloon->GetPosition().y,
                   moon_ball->GetPosition().x, moon_ball->GetPosition().y,
                   box->GetPosition().x, box->GetPosition().y);
        }
    }

    return 0;
}
```

**预期行为**：气球缓慢上升，月球球缓慢下落，箱子落入水中后浮起。

### 总结

- 重力缩放单独调整每个刚体的重力，0=无重力，-1=反重力，0.16=月球。
- 浮力 = 流体密度 × 浸入面积 × g，方向向上。
- 浸入水中时附加水流阻力（与速度反向）。
- **密度与浮沉**：物体密度 < 流体密度则浮起，> 则沉底。
- **应用场景**：气球（反重力）、月球关卡（低重力）、水池（浮力）、太空（无重力）。
- **常见坑**：浮力需每帧施加（像力一样）；浸入面积计算需考虑物体旋转（简化版忽略旋转）。

---

# 第 4 章 · 碰撞系统

碰撞是物理引擎的核心功能。本章深入 Box2D 的碰撞系统：接触对象（b2Contact）的内部结构、接触流形（Manifold）的几何意义、碰撞回调（b2ContactListener）的使用、传感器（Sensor）的触发机制，以及接触过滤（b2ContactFilter）的自定义规则。掌握这些，你就能实现"子弹击中敌人扣血"、"角色进入触发区域"、"特定物体之间不碰撞"等游戏逻辑。

## 第 13 讲 · 接触 b2Contact 与接触流形

### 概念

**b2Contact** 是 Box2D 内部表示两个 fixture 接触的对象。每当两个 fixture 的 AABB 重叠且形状真正相交时，Box2D 创建一个 b2Contact 对象记录这次接触。

**接触流形（Contact Manifold）** 描述接触的几何信息：接触点位置、接触法向量、穿透深度。一次碰撞可能有 1~2 个接触点（如矩形落在平面上有 2 个接触点）。

### 原理

**b2Contact 的生命周期**：

1. **创建**：两 fixture 的 AABB 重叠时，Box2D 创建 b2Contact（此时可能尚未真正碰撞）。
2. **更新**：每个 Step 中，Box2D 更新接触流形（计算接触点、法向量、穿透深度）。
3. **销毁**：AABB 不再重叠时，b2Contact 被销毁。

**接触状态**：

- `b2Contact::IsTouching()`：是否真正接触（形状相交），区别于 AABB 重叠。
- `b2Contact::IsEnabled()`：是否启用（可手动禁用）。
- `b2Contact::GetManifold()`：获取接触流形。

**接触流形 b2Manifold 的结构**：

```cpp
struct b2Manifold {
    b2ManifoldPoint points[b2_maxManifoldPoints];  // 最多 2 个接触点
    int pointCount;          // 实际接触点数（1 或 2）
    b2Vec2 localNormal;      // 接触法向量（本地坐标）
    b2Vec2 localPoint;       // 接触参考点（本地坐标）
    b2Manifold::Type type;   // 流形类型（圆-圆、圆-多边形等）
};

struct b2ManifoldPoint {
    b2Vec2 localPoint;       // 接触点（本地坐标）
    float normalImpulse;     // 法向冲量（用于碰撞响应）
    float tangentImpulse;    // 切向冲量（用于摩擦）
    b2ContactID id;          // 接触点 ID（跨帧追踪）
};
```

**世界坐标接触点**：b2Manifold 中的点是本地坐标，需用 `b2Contact::GetWorldManifold()` 转换为世界坐标：

```cpp
b2WorldManifold world_manifold;
contact->GetWorldManifold(&world_manifold);
// world_manifold.normal: 世界坐标法向量
// world_manifold.points[i]: 世界坐标接触点
// world_manifold.separations[i]: 分离距离（负数=穿透）
```

**接触点 ID**：Box2D 用 `b2ContactID` 跨帧追踪接触点，确保冲量累积的连续性。即使接触点位置略有变化，ID 相同则视为同一接触点。

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>

int main() {
    b2World world(b2Vec2(0.0f, 9.8f));

    // 地面
    b2BodyDef ground_def;
    ground_def.position.Set(0.0f, 10.0f);
    b2Body* ground = world.CreateBody(&ground_def);
    b2PolygonShape ground_shape;
    ground_shape.SetAsBox(50.0f, 1.0f);
    ground->CreateFixture(&ground_shape, 0.0f);

    // 落下的箱子
    b2BodyDef box_def;
    box_def.type = b2_dynamicBody;
    box_def.position.Set(0.0f, 5.0f);
    b2Body* box = world.CreateBody(&box_def);
    b2PolygonShape box_shape;
    box_shape.SetAsBox(1.0f, 1.0f);
    b2FixtureDef box_fix;
    box_fix.shape = &box_shape;
    box_fix.density = 1.0f;
    box->CreateFixture(&box_fix);

    // 模拟并检查接触
    float time_step = 1.0f / 60.0f;
    for (int i = 0; i < 120; ++i) {
        world.Step(time_step, 8, 3);

        // 遍历所有接触
        for (b2Contact* contact = world.GetContactList(); contact;
             contact = contact->GetNext()) {
            if (!contact->IsTouching()) continue;

            b2Fixture* a = contact->GetFixtureA();
            b2Fixture* b = contact->GetFixtureB();
            b2Body* body_a = a->GetBody();
            b2Body* body_b = b->GetBody();

            // 获取世界坐标流形
            b2WorldManifold wm;
            contact->GetWorldManifold(&wm);

            printf("t=%.2f: contact between bodyA(%.2f,%.2f) and bodyB(%.2f,%.2f)\n",
                   i * time_step,
                   body_a->GetPosition().x, body_a->GetPosition().y,
                   body_b->GetPosition().x, body_b->GetPosition().y);
            printf("  normal=(%.2f,%.2f), points=%d\n",
                   wm.normal.x, wm.normal.y, contact->GetManifold()->pointCount);

            for (int j = 0; j < contact->GetManifold()->pointCount; ++j) {
                printf("  point %d: (%.2f,%.2f) separation=%.2f\n",
                       j, wm.points[j].x, wm.points[j].y, wm.separations[j]);
            }
        }
    }

    return 0;
}
```

### 总结

- b2Contact 表示两 fixture 的接触，AABB 重叠时创建，分离时销毁。
- `IsTouching()` 表示真正接触（形状相交），区别于 AABB 重叠。
- 接触流形包含 1~2 个接触点、法向量、穿透深度。
- `GetWorldManifold()` 获取世界坐标的接触信息，便于调试与游戏逻辑。
- **接触点 ID** 跨帧追踪，确保冲量累积连续。
- **应用场景**：调试碰撞、自定义碰撞响应、获取接触点位置（如贴花、火花特效）。

---

## 第 14 讲 · 碰撞回调 b2ContactListener

### 概念

**b2ContactListener** 是 Box2D 提供的事件回调接口，在碰撞发生时通知游戏逻辑。它有四个回调函数，分别对应碰撞的不同阶段：

- `BeginContact`：两 fixture 开始接触（从无到有）
- `EndContact`：两 fixture 结束接触（从有到无）
- `PreSolve`：每次求解前（可修改接触参数）
- `PostSolve`：每次求解后（可获取冲量信息）

通过继承 b2ContactListener 并实现这些函数，你可以响应碰撞事件而不必每帧轮询接触列表。

### 原理

**回调的调用时机**：

```
Step() 内部流程：
1. 碰撞检测（broad phase + narrow phase）
2. 对新接触调用 BeginContact
3. 对消失接触调用 EndContact
4. 对每个接触调用 PreSolve（可修改参数）
5. 速度求解
6. 位置求解
7. 对每个接触调用 PostSolve（获取冲量）
```

**四个回调的用途**：

| 回调 | 时机 | 典型用途 |
|------|------|---------|
| BeginContact | 开始接触 | 扣血、播放音效、触发事件 |
| EndContact | 结束接触 | 离开区域、停止音效 |
| PreSolve | 求解前 | 禁用接触、修改摩擦/弹性 |
| PostSolve | 求解后 | 获取冲量大小、判断伤害程度 |

**PreSolve 的特殊用途**：可以动态禁用接触（`contact->SetEnabled(false)`），让物体穿过。典型应用是"单向平台"——玩家从下方跳上时禁用接触，从上方落下时启用。

**PostSolve 的冲量信息**：`b2ContactImpulse` 包含法向冲量和切向冲量数组，可用于判断碰撞强度（如轻碰 vs 重击）。

**重要约束**：回调在 `Step` 内部调用，此时物理世界处于锁定状态，**不能修改物理世界**（不能创建/销毁刚体、不能施加冲量）。需要修改时，记录事件，在 Step 后处理。

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>
#include <vector>

// 自定义碰撞监听器
class GameContactListener : public b2ContactListener {
public:
    // 开始接触
    void BeginContact(b2Contact* contact) override {
        b2Fixture* fa = contact->GetFixtureA();
        b2Fixture* fb = contact->GetFixtureB();
        void* ua = fa->GetUserData();
        void* ub = fb->GetUserData();

        // 通过 userData 识别物体类型
        int type_a = (intptr_t)ua;
        int type_b = (intptr_t)ub;

        // 子弹击中敌人
        if ((type_a == 1 && type_b == 2) || (type_a == 2 && type_b == 1)) {
            events.push_back({"hit", fa->GetBody(), fb->GetBody()});
        }
        // 玩家进入触发区域
        if ((type_a == 3 && type_b == 4) || (type_a == 4 && type_b == 3)) {
            events.push_back({"enter_zone", fa->GetBody(), fb->GetBody()});
        }
    }

    // 结束接触
    void EndContact(b2Contact* contact) override {
        b2Fixture* fa = contact->GetFixtureA();
        b2Fixture* fb = contact->GetFixtureB();
        int type_a = (intptr_t)fa->GetUserData();
        int type_b = (intptr_t)fb->GetUserData();

        if ((type_a == 3 && type_b == 4) || (type_a == 4 && type_b == 3)) {
            events.push_back({"leave_zone", fa->GetBody(), fb->GetBody()});
        }
    }

    // 求解前（可修改接触）
    void PreSolve(b2Contact* contact, const b2Manifold* oldManifold) override {
        b2Fixture* fa = contact->GetFixtureA();
        b2Fixture* fb = contact->GetFixtureB();
        int type_a = (intptr_t)fa->GetUserData();
        int type_b = (intptr_t)fb->GetUserData();

        // 单向平台：玩家从下方接触平台时禁用
        if (type_a == 5 || type_b == 5) {  // 5 = 平台
            b2WorldManifold wm;
            contact->GetWorldManifold(&wm);
            b2Body* player = (type_a == 6) ? fa->GetBody() : fb->GetBody();
            // 玩家在平台下方且向上运动
            if (player->GetPosition().y < wm.points[0].y &&
                player->GetLinearVelocity().y > 0) {
                contact->SetEnabled(false);
            }
        }
    }

    // 求解后（获取冲量）
    void PostSolve(b2Contact* contact, const b2ContactImpulse* impulse) override {
        b2Fixture* fa = contact->GetFixtureA();
        b2Fixture* fb = contact->GetFixtureB();
        int type_a = (intptr_t)fa->GetUserData();
        int type_b = (intptr_t)fb->GetUserData();

        // 计算碰撞强度
        float max_impulse = 0.0f;
        for (int i = 0; i < impulse->count; ++i) {
            max_impulse = b2Max(max_impulse, impulse->normalImpulses[i]);
        }

        // 强烈碰撞造成伤害
        if (max_impulse > 5.0f && (type_a == 2 || type_b == 2)) {
            events.push_back({"damage", fa->GetBody(), fb->GetBody()});
            printf("Strong hit! impulse=%.2f\n", max_impulse);
        }
    }

    struct CollisionEvent {
        const char* type;
        b2Body* a;
        b2Body* b;
    };
    std::vector<CollisionEvent> events;
};

int main() {
    b2World world(b2Vec2(0.0f, 9.8f));

    GameContactListener listener;
    world.SetContactListener(&listener);

    // 创建物体（用 userData 标记类型）
    // 1=子弹, 2=敌人, 3=玩家, 4=触发区域, 5=单向平台, 6=玩家(平台用)

    // 敌人
    b2BodyDef enemy_def;
    enemy_def.type = b2_dynamicBody;
    enemy_def.position.Set(0.0f, 8.0f);
    b2Body* enemy = world.CreateBody(&enemy_def);
    b2PolygonShape enemy_shape;
    enemy_shape.SetAsBox(0.5f, 0.5f);
    b2FixtureDef enemy_fix;
    enemy_fix.shape = &enemy_shape;
    enemy_fix.density = 1.0f;
    enemy_fix.userData = (void*)2;
    enemy->CreateFixture(&enemy_fix);

    // 子弹
    b2BodyDef bullet_def;
    bullet_def.type = b2_dynamicBody;
    bullet_def.position.Set(-5.0f, 8.0f);
    bullet_def.bullet = true;
    b2Body* bullet = world.CreateBody(&bullet_def);
    b2CircleShape bullet_shape;
    bullet_shape.m_radius = 0.1f;
    b2FixtureDef bullet_fix;
    bullet_fix.shape = &bullet_shape;
    bullet_fix.density = 5.0f;
    bullet_fix.userData = (void*)1;
    bullet->CreateFixture(&bullet_fix);
    bullet->SetLinearVelocity(b2Vec2(20.0f, 0.0f));

    // 模拟
    float time_step = 1.0f / 60.0f;
    for (int i = 0; i < 60; ++i) {
        world.Step(time_step, 8, 3);

        // 处理回调事件（在 Step 外）
        for (auto& e : listener.events) {
            printf("Event: %s\n", e.type);
        }
        listener.events.clear();
    }

    return 0;
}
```

### 总结

- b2ContactListener 提供四个回调：BeginContact、EndContact、PreSolve、PostSolve。
- BeginContact/EndContact 用于游戏逻辑（扣血、触发事件）。
- PreSolve 可禁用接触（单向平台）或修改参数。
- PostSolve 获取冲量，判断碰撞强度。
- **关键约束**：回调内不能修改物理世界，需记录事件在 Step 后处理。
- **userData 机制**：用 `SetUserData` 标记物体类型，回调中识别。
- **常见坑**：在回调中销毁刚体会崩溃；忘记设置 listener 导致收不到事件。

---

## 第 15 讲 · 传感器 Sensor

### 概念

**传感器（Sensor）** 是一种特殊 fixture，**只检测碰撞但不产生物理响应**。物体可以穿过传感器，但会触发 BeginContact/EndContact 回调。

传感器适合**触发区域**：检查点、地雷、视野范围、拾取区域、传送门。它不阻挡物体运动，只在物体进入/离开时通知游戏逻辑。

### 原理

**传感器的创建**：

```cpp
b2FixtureDef fix;
fix.isSensor = true;  // 设为传感器
```

**传感器的特性**：

1. **无物理响应**：物体穿过传感器不会停止或反弹。
2. **触发回调**：与其他 fixture 接触时触发 BeginContact/EndContact。
3. **不触发 PreSolve/PostSolve**：因为没有物理求解。
4. **可以查询接触**：`IsTouching()` 返回是否在传感器内。

**传感器 vs 普通fixture**：

| 特性 | 普通 fixture | 传感器 |
|------|-------------|--------|
| 物理响应 | 有（碰撞、反弹） | 无 |
| BeginContact | 触发 | 触发 |
| PreSolve/PostSolve | 触发 | 不触发 |
| 用途 | 实体碰撞 | 触发区域 |

**两个传感器之间**：默认不产生接触（避免无意义的事件）。如需检测两传感器接触，需自定义 b2ContactFilter（第 16 讲）。

**性能**：传感器仍参与碰撞检测（broad phase + narrow phase），但不参与求解，性能略高于普通 fixture。

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>
#include <vector>

class SensorListener : public b2ContactListener {
public:
    void BeginContact(b2Contact* contact) override {
        b2Fixture* fa = contact->GetFixtureA();
        b2Fixture* fb = contact->GetFixtureB();

        // 检查哪个是传感器
        b2Fixture* sensor = fa->IsSensor() ? fa : (fb->IsSensor() ? fb : nullptr);
        b2Fixture* other = sensor == fa ? fb : fa;

        if (sensor) {
            const char* sensor_name = (const char*)sensor->GetUserData();
            printf("[%s] 物体进入\n", sensor_name);
            entered_zones.push_back(sensor);
        }
    }

    void EndContact(b2Contact* contact) override {
        b2Fixture* fa = contact->GetFixtureA();
        b2Fixture* fb = contact->GetFixtureB();
        b2Fixture* sensor = fa->IsSensor() ? fa : (fb->IsSensor() ? fb : nullptr);

        if (sensor) {
            const char* sensor_name = (const char*)sensor->GetUserData();
            printf("[%s] 物体离开\n", sensor_name);
        }
    }

    std::vector<b2Fixture*> entered_zones;
};

int main() {
    b2World world(b2Vec2(0.0f, 9.8f));

    SensorListener listener;
    world.SetContactListener(&listener);

    // ========== 1. 检查点传感器 ==========
    b2BodyDef checkpoint_def;
    checkpoint_def.position.Set(5.0f, 9.0f);
    b2Body* checkpoint = world.CreateBody(&checkpoint_def);
    b2PolygonShape cp_shape;
    cp_shape.SetAsBox(1.0f, 1.0f);
    b2FixtureDef cp_fix;
    cp_fix.shape = &cp_shape;
    cp_fix.isSensor = true;
    cp_fix.userData = (void*)"检查点";
    checkpoint->CreateFixture(&cp_fix);

    // ========== 2. 地雷传感器 ==========
    b2BodyDef mine_def;
    mine_def.position.Set(10.0f, 9.5f);
    b2Body* mine = world.CreateBody(&mine_def);
    b2CircleShape mine_shape;
    mine_shape.m_radius = 1.5f;
    b2FixtureDef mine_fix;
    mine_fix.shape = &mine_shape;
    mine_fix.isSensor = true;
    mine_fix.userData = (void*)"地雷";
    mine->CreateFixture(&mine_fix);

    // ========== 3. 地面 ==========
    b2BodyDef ground_def;
    ground_def.position.Set(0.0f, 10.0f);
    b2Body* ground = world.CreateBody(&ground_def);
    b2PolygonShape ground_shape;
    ground_shape.SetAsBox(50.0f, 1.0f);
    ground->CreateFixture(&ground_shape, 0.0f);

    // ========== 4. 玩家 ==========
    b2BodyDef player_def;
    player_def.type = b2_dynamicBody;
    player_def.position.Set(-5.0f, 5.0f);
    b2Body* player = world.CreateBody(&player_def);
    b2CircleShape player_shape;
    player_shape.m_radius = 0.3f;
    b2FixtureDef player_fix;
    player_fix.shape = &player_shape;
    player_fix.density = 1.0f;
    player->CreateFixture(&player_fix);
    player->SetLinearVelocity(b2Vec2(5.0f, 0.0f));  // 向右移动

    // ========== 5. 模拟 ==========
    float time_step = 1.0f / 60.0f;
    for (int i = 0; i < 240; ++i) {
        world.Step(time_step, 8, 3);

        if (i % 30 == 0) {
            printf("t=%.1f: player at (%.2f, %.2f)\n",
                   i * time_step,
                   player->GetPosition().x, player->GetPosition().y);
        }
    }

    return 0;
}
```

**预期输出**：玩家移动经过检查点和地雷时，打印"进入"和"离开"事件。

### 总结

- 传感器只检测碰撞但不产生物理响应，物体可穿过。
- 创建方式：`fixtureDef.isSensor = true`。
- 触发 BeginContact/EndContact，但不触发 PreSolve/PostSolve。
- **应用场景**：检查点、地雷、视野、拾取区域、传送门、触发剧情。
- **常见坑**：两传感器默认不产生接触；传感器仍参与碰撞检测，过多会拖慢性能。
- **设计建议**：传感器形状通常比实体大（如视野范围），用圆形或多边形。

---

## 第 16 讲 · 接触过滤 b2ContactFilter

### 概念

**b2ContactFilter** 控制哪些 fixture 之间会产生碰撞。默认规则是：两个 fixture 都不是传感器，且其 `filter.categoryBits` 与 `filter.maskBits` 按位与不为零，则碰撞。

通过自定义 b2ContactFilter，你可以实现：特定物体之间不碰撞（如玩家子弹不击中玩家）、自定义碰撞规则（如团队系统）、修改摩擦/弹性的混合规则。

### 原理

**碰撞过滤的三个字段**：

```cpp
struct b2Filter {
    uint16 categoryBits;  // 类别位（物体属于哪些类别）
    uint16 maskBits;      // 掩码位（物体与哪些类别碰撞）
    int16 groupIndex;     // 组索引（特殊规则）
};
```

**类别与掩码的工作方式**：

```
碰撞条件：(categoryBits_A & maskBits_B) != 0 且 (categoryBits_B & maskBits_A) != 0
```

即 A 的类别在 B 的掩码中，且 B 的类别在 A 的掩码中，才碰撞。

**示例**：

```cpp
// 定义类别
const uint16 CATEGORY_PLAYER    = 0x0001;
const uint16 CATEGORY_ENEMY     = 0x0002;
const uint16 CATEGORY_PLAYER_BULLET = 0x0004;
const uint16 CATEGORY_ENEMY_BULLET  = 0x0008;
const uint16 CATEGORY_WALL      = 0x0010;

// 玩家：与敌人子弹、墙壁碰撞
b2Filter player_filter;
player_filter.categoryBits = CATEGORY_PLAYER;
player_filter.maskBits = CATEGORY_ENEMY_BULLET | CATEGORY_WALL;

// 玩家子弹：与敌人、墙壁碰撞（不与玩家碰撞）
b2Filter player_bullet_filter;
player_bullet_filter.categoryBits = CATEGORY_PLAYER_BULLET;
player_bullet_filter.maskBits = CATEGORY_ENEMY | CATEGORY_WALL;
```

**组索引的特殊规则**：

- `groupIndex > 0` 且相同：始终碰撞（同组友好）。
- `groupIndex < 0` 且相同：始终不碰撞（同组免疫）。
- `groupIndex == 0` 或不同：使用 categoryBits/maskBits 规则。

组索引优先级高于类别/掩码，适合"同队不伤害"等规则。

**自定义 b2ContactFilter**：

```cpp
class MyContactFilter : public b2ContactFilter {
    bool ShouldCollide(b2Fixture* a, b2Fixture* b) override {
        // 自定义规则，返回 true 表示碰撞
        // 可以访问 fixture 的 userData 做复杂判断
        return true;
    }
};
```

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>

// 类别定义
const uint16 CAT_PLAYER     = 0x0001;
const uint16 CAT_ENEMY      = 0x0002;
const uint16 CAT_P_BULLET   = 0x0004;
const uint16 CAT_E_BULLET   = 0x0008;
const uint16 CAT_WALL       = 0x0010;

// 自定义过滤器：修改摩擦混合规则
class CustomFilter : public b2ContactFilter {
public:
    bool ShouldCollide(b2Fixture* a, b2Fixture* b) override {
        // 默认规则
        if (!b2ContactFilter::ShouldCollide(a, b)) return false;

        // 自定义：通过 userData 排除特定组合
        int ua = (intptr_t)a->GetUserData();
        int ub = (intptr_t)b->GetUserData();
        // 例如：类型 10 和 11 永不碰撞
        if ((ua == 10 && ub == 11) || (ua == 11 && ub == 10)) return false;

        return true;
    }
};

int main() {
    b2World world(b2Vec2(0.0f, 9.8f));

    CustomFilter filter;
    world.SetContactFilter(&filter);

    // ========== 1. 墙壁 ==========
    b2BodyDef wall_def;
    wall_def.position.Set(0.0f, 10.0f);
    b2Body* wall = world.CreateBody(&wall_def);
    b2PolygonShape wall_shape;
    wall_shape.SetAsBox(50.0f, 1.0f);
    b2FixtureDef wall_fix;
    wall_fix.shape = &wall_shape;
    wall_fix.filter.categoryBits = CAT_WALL;
    wall_fix.filter.maskBits = 0xFFFF;  // 与所有类别碰撞
    wall->CreateFixture(&wall_fix);

    // ========== 2. 玩家 ==========
    b2BodyDef player_def;
    player_def.type = b2_dynamicBody;
    player_def.position.Set(-3.0f, 5.0f);
    b2Body* player = world.CreateBody(&player_def);
    b2CircleShape player_shape;
    player_shape.m_radius = 0.5f;
    b2FixtureDef player_fix;
    player_fix.shape = &player_shape;
    player_fix.density = 1.0f;
    player_fix.filter.categoryBits = CAT_PLAYER;
    player_fix.filter.maskBits = CAT_E_BULLET | CAT_WALL;  // 被敌人子弹打，撞墙
    player->CreateFixture(&player_fix);

    // ========== 3. 玩家子弹 ==========
    b2BodyDef pbullet_def;
    pbullet_def.type = b2_dynamicBody;
    pbullet_def.position.Set(-2.0f, 5.0f);
    pbullet_def.bullet = true;
    b2Body* pbullet = world.CreateBody(&pbullet_def);
    b2CircleShape pbullet_shape;
    pbullet_shape.m_radius = 0.1f;
    b2FixtureDef pbullet_fix;
    pbullet_fix.shape = &pbullet_shape;
    pbullet_fix.density = 5.0f;
    pbullet_fix.filter.categoryBits = CAT_P_BULLET;
    pbullet_fix.filter.maskBits = CAT_ENEMY | CAT_WALL;  // 打敌人，撞墙
    pbullet->CreateFixture(&pbullet_fix);
    pbullet->SetLinearVelocity(b2Vec2(10.0f, 0.0f));

    // ========== 4. 敌人 ==========
    b2BodyDef enemy_def;
    enemy_def.type = b2_dynamicBody;
    enemy_def.position.Set(3.0f, 5.0f);
    b2Body* enemy = world.CreateBody(&enemy_def);
    b2PolygonShape enemy_shape;
    enemy_shape.SetAsBox(0.5f, 0.5f);
    b2FixtureDef enemy_fix;
    enemy_fix.shape = &enemy_shape;
    enemy_fix.density = 1.0f;
    enemy_fix.filter.categoryBits = CAT_ENEMY;
    enemy_fix.filter.maskBits = CAT_P_BULLET | CAT_WALL;  // 被玩家子弹打，撞墙
    enemy->CreateFixture(&enemy_fix);

    // ========== 5. 组索引示例：同队不碰撞 ==========
    b2Filter team_a_filter;
    team_a_filter.groupIndex = -1;  // 同组负值，互不碰撞

    b2BodyDef a1_def;
    a1_def.type = b2_dynamicBody;
    a1_def.position.Set(10.0f, 5.0f);
    b2Body* a1 = world.CreateBody(&a1_def);
    b2CircleShape a1_shape;
    a1_shape.m_radius = 0.3f;
    b2FixtureDef a1_fix;
    a1_fix.shape = &a1_shape;
    a1_fix.density = 1.0f;
    a1_fix.filter = team_a_filter;
    a1->CreateFixture(&a1_fix);

    b2BodyDef a2_def;
    a2_def.type = b2_dynamicBody;
    a2_def.position.Set(10.3f, 5.0f);  // 与 a1 重叠
    b2Body* a2 = world.CreateBody(&a2_def);
    b2FixtureDef a2_fix;
    a2_fix.shape = &a1_shape;
    a2_fix.density = 1.0f;
    a2_fix.filter = team_a_filter;  // 同组，不碰撞
    a2->CreateFixture(&a2_fix);

    // ========== 6. 模拟 ==========
    float time_step = 1.0f / 60.0f;
    for (int i = 0; i < 120; ++i) {
        world.Step(time_step, 8, 3);

        if (i % 30 == 0) {
            printf("t=%.1f: pbullet=(%.2f,%.2f) enemy=(%.2f,%.2f)\n",
                   i * time_step,
                   pbullet->GetPosition().x, pbullet->GetPosition().y,
                   enemy->GetPosition().x, enemy->GetPosition().y);
        }
    }

    return 0;
}
```

**预期行为**：玩家子弹飞向敌人并击中（碰撞）；玩家与玩家子弹不碰撞；同组 a1/a2 互不碰撞。

### 总结

- 碰撞过滤用 categoryBits（类别）和 maskBits（掩码）按位与判断。
- 组索引 groupIndex：正值同组始终碰撞，负值同组始终不碰撞，优先级最高。
- 自定义 b2ContactFilter 可实现复杂规则（基于 userData）。
- **应用场景**：子弹不击中友军、特定物体穿过特定墙壁、团队系统。
- **常见坑**：忘记设置 categoryBits（默认 0x0001）导致所有物体同类别；maskBits 默认 0xFFFF 与所有碰撞。
- **设计建议**：用枚举或常量定义类别位，避免魔法数字；注释每个类别的含义。

---

# 第 5 章 · 关节约束

关节（Joint）是连接两个刚体的约束，让它们以特定方式相对运动。Box2D 提供多种关节：距离关节保持固定距离、旋转关节绕轴旋转、棱柱关节沿轴滑动、滑轮关节模拟滑轮系统、齿轮关节联动旋转、焊接关节刚性连接、鼠标关节拖拽物体。本章将逐一讲解这些关节的原理与应用，让你能构建各种机械装置——从钟摆到起重机，从布娃娃到车辆。

## 第 17 讲 · 距离关节 b2DistanceJoint

### 概念

**b2DistanceJoint** 强制两个刚体上的两个点保持固定距离。它像一根不可拉伸的绳子，连接两个物体。无论物体如何运动，这两个点的距离始终等于设定值。

距离关节适合：绳索（近似）、弹性连接、链条的每一节、固定两物体的相对位置但允许旋转。

### 原理

**关节定义 b2DistanceJointDef**：

```cpp
struct b2DistanceJointDef {
    b2Body* bodyA;           // 刚体 A
    b2Body* bodyB;           // 刚体 B
    b2Vec2 localAnchorA;     // A 上的锚点（本地坐标）
    b2Vec2 localAnchorB;     // B 上的锚点（本地坐标）
    float length;            // 目标距离（米）
    float minLength;         // 最小距离（可压缩时使用，Box2D 2.4+）
    float maxLength;         // 最大距离（可拉伸时使用）
    float stiffness;         // 刚度（弹簧，Box2D 2.4+）
    float damping;           // 阻尼（弹簧）
};
```

**锚点的意义**：关节连接的不是刚体质心，而是刚体上的特定点（锚点）。锚点用本地坐标表示（相对刚体原点）。例如 `localAnchorA = (1, 0)` 表示 A 的右边缘。

**刚性与柔性**：

- 传统距离关节（刚性）：`stiffness = 0`，距离严格不变。
- 弹性距离关节（弹簧）：`stiffness > 0`，距离可弹性变化，配合 `damping` 减震。

**创建关节**：

```cpp
b2DistanceJointDef joint_def;
joint_def.bodyA = bodyA;
joint_def.bodyB = bodyB;
joint_def.localAnchorA.Set(1.0f, 0.0f);
joint_def.localAnchorB.Set(-1.0f, 0.0f);
joint_def.length = 2.0f;  // 2 米
b2DistanceJoint* joint = (b2DistanceJoint*)world.CreateJoint(&joint_def);
```

**关节的生命周期**：关节由 world 拥有，创建后返回指针。销毁用 `world.DestroyJoint(joint)`。销毁任一连接的刚体前，必须先销毁其关节。

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>

int main() {
    b2World world(b2Vec2(0.0f, 9.8f));

    // ========== 1. 天花板（静态锚点） ==========
    b2BodyDef ceiling_def;
    ceiling_def.position.Set(0.0f, 0.0f);
    b2Body* ceiling = world.CreateBody(&ceiling_def);

    // ========== 2. 钟摆（距离关节） ==========
    b2BodyDef pendulum_def;
    pendulum_def.type = b2_dynamicBody;
    pendulum_def.position.Set(2.0f, 2.0f);  // 距天花板 2√2 米
    b2Body* pendulum = world.CreateBody(&pendulum_def);
    b2PolygonShape pendulum_shape;
    pendulum_shape.SetAsBox(0.3f, 0.3f);
    b2FixtureDef pendulum_fix;
    pendulum_fix.shape = &pendulum_shape;
    pendulum_fix.density = 1.0f;
    pendulum->CreateFixture(&pendulum_fix);

    // 距离关节：天花板原点 ↔ 钟摆原点，距离 2 米
    b2DistanceJointDef dist_def;
    dist_def.bodyA = ceiling;
    dist_def.bodyB = pendulum;
    dist_def.localAnchorA.Set(0.0f, 0.0f);   // 天花板原点
    dist_def.localAnchorB.Set(0.0f, 0.0f);   // 钟摆原点
    dist_def.length = 2.0f;                   // 绳长 2 米
    world.CreateJoint(&dist_def);

    // 给钟摆一个初速度，开始摆动
    pendulum->SetLinearVelocity(b2Vec2(5.0f, 0.0f));

    // ========== 3. 弹性连接（弹簧） ==========
    b2BodyDef box1_def, box2_def;
    box1_def.type = b2_dynamicBody;
    box1_def.position.Set(-5.0f, 5.0f);
    b2Body* box1 = world.CreateBody(&box1_def);
    b2PolygonShape box_shape;
    box_shape.SetAsBox(0.5f, 0.5f);
    b2FixtureDef box_fix;
    box_fix.shape = &box_shape;
    box_fix.density = 1.0f;
    box1->CreateFixture(&box_fix);

    box2_def.type = b2_dynamicBody;
    box2_def.position.Set(-3.0f, 5.0f);
    b2Body* box2 = world.CreateBody(&box2_def);
    box2->CreateFixture(&box_fix);

    // 弹性距离关节
    b2DistanceJointDef spring_def;
    spring_def.Initialize(box1, box2, box1->GetPosition(), box2->GetPosition());
    spring_def.length = 2.0f;
    spring_def.minLength = 1.0f;
    spring_def.maxLength = 3.0f;
    spring_def.stiffness = 50.0f;   // 弹簧刚度
    spring_def.damping = 5.0f;      // 阻尼
    world.CreateJoint(&spring_def);

    // ========== 4. 模拟 ==========
    float time_step = 1.0f / 60.0f;
    for (int i = 0; i < 300; ++i) {
        world.Step(time_step, 8, 3);

        if (i % 60 == 0) {
            printf("t=%.1f: pendulum=(%.2f,%.2f) | box1=(%.2f,%.2f) box2=(%.2f,%.2f)\n",
                   i * time_step,
                   pendulum->GetPosition().x, pendulum->GetPosition().y,
                   box1->GetPosition().x, box1->GetPosition().y,
                   box2->GetPosition().x, box2->GetPosition().y);
        }
    }

    return 0;
}
```

**预期行为**：钟摆绕天花板摆动；两盒子用弹簧连接，拉伸后回弹。

### 总结

- 距离关节保持两锚点固定距离，像不可拉伸的绳子。
- 锚点用本地坐标指定，可以是刚体上的任意点。
- `Initialize(bodyA, bodyB, worldAnchorA, worldAnchorB)` 自动计算本地锚点与长度。
- 弹性距离关节（stiffness > 0）模拟弹簧，配合 damping 减震。
- **应用场景**：钟摆、绳索（近似）、弹簧、链条每一节。
- **常见坑**：销毁刚体前未销毁其关节会崩溃；长度为 0 会导致奇异。

---

## 第 18 讲 · 旋转关节 b2RevoluteJoint

### 概念

**b2RevoluteJoint** 强制两个刚体共享一个锚点，允许绕该点相对旋转。它像铰链或门轴，两物体在锚点处连接，可以自由旋转（或限制角度）。

旋转关节是机械装置的核心：车轮、门、风扇、杠杆、钟摆（精确版）、布娃娃的关节。

### 原理

**关节定义 b2RevoluteJointDef**：

```cpp
struct b2RevoluteJointDef {
    b2Body* bodyA;
    b2Body* bodyB;
    b2Vec2 localAnchorA;     // A 上的锚点
    b2Vec2 localAnchorB;     // B 上的锚点
    bool enableLimit;        // 是否启用角度限制
    float lowerAngle;        // 最小角度（弧度）
    float upperAngle;        // 最大角度（弧度）
    bool enableMotor;        // 是否启用马达
    float motorSpeed;        // 马达目标速度（rad/s）
    float maxMotorTorque;    // 马达最大力矩（N·m）
};
```

**角度限制**：`lowerAngle` 和 `upperAngle` 限制相对旋转的角度范围。例如门只能开 0~120 度。启用需 `enableLimit = true`。

**马达**：旋转关节可以内置马达，主动驱动旋转。`motorSpeed` 是目标角速度，`maxMotorTorque` 是最大力矩。马达会尝试达到目标速度，但受最大力矩限制——负载过重时无法达到。

**Initialize 方法**：

```cpp
joint_def.Initialize(bodyA, bodyB, worldAnchor);
// 自动设置 localAnchorA/B，锚点为世界坐标
```

**关节角度查询**：

```cpp
float GetJointAngle() const;       // 当前相对角度
float GetJointSpeed() const;       // 当前相对角速度
float GetMotorTorque(float inv_dt) const;  // 当前马达力矩
```

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>

int main() {
    b2World world(b2Vec2(0.0f, 9.8f));

    // ========== 1. 地面 ==========
    b2BodyDef ground_def;
    ground_def.position.Set(0.0f, 10.0f);
    b2Body* ground = world.CreateBody(&ground_def);
    b2PolygonShape ground_shape;
    ground_shape.SetAsBox(50.0f, 1.0f);
    ground->CreateFixture(&ground_shape, 0.0f);

    // ========== 2. 门（带角度限制） ==========
    b2BodyDef door_hinge_def;
    door_hinge_def.position.Set(0.0f, 9.0f);
    b2Body* door_hinge = world.CreateBody(&door_hinge_def);  // 静态铰链

    b2BodyDef door_def;
    door_def.type = b2_dynamicBody;
    door_def.position.Set(1.0f, 9.0f);  // 门体在铰链右侧
    b2Body* door = world.CreateBody(&door_def);
    b2PolygonShape door_shape;
    door_shape.SetAsBox(1.0f, 0.1f);  // 长条门
    b2FixtureDef door_fix;
    door_fix.shape = &door_shape;
    door_fix.density = 1.0f;
    door->CreateFixture(&door_fix);

    // 旋转关节：铰链在门左端
    b2RevoluteJointDef door_joint_def;
    door_joint_def.Initialize(door_hinge, door, b2Vec2(0.0f, 9.0f));
    door_joint_def.enableLimit = true;
    door_joint_def.lowerAngle = -b2_pi / 2;  // -90 度
    door_joint_def.upperAngle = b2_pi / 2;   // +90 度
    world.CreateJoint(&door_joint_def);

    // 给门一个初速度，让它摆动
    door->SetAngularVelocity(2.0f);

    // ========== 3. 车轮（带马达） ==========
    b2BodyDef car_def;
    car_def.type = b2_dynamicBody;
    car_def.position.Set(5.0f, 8.0f);
    b2Body* car = world.CreateBody(&car_def);
    b2PolygonShape car_shape;
    car_shape.SetAsBox(1.0f, 0.3f);
    b2FixtureDef car_fix;
    car_fix.shape = &car_shape;
    car_fix.density = 1.0f;
    car->CreateFixture(&car_fix);

    b2BodyDef wheel_def;
    wheel_def.type = b2_dynamicBody;
    wheel_def.position.Set(5.0f, 7.5f);
    b2Body* wheel = world.CreateBody(&wheel_def);
    b2CircleShape wheel_shape;
    wheel_shape.m_radius = 0.4f;
    b2FixtureDef wheel_fix;
    wheel_fix.shape = &wheel_shape;
    wheel_fix.density = 1.0f;
    wheel_fix.friction = 0.9f;
    wheel->CreateFixture(&wheel_fix);

    // 旋转关节：车轮绕中心旋转，带马达
    b2RevoluteJointDef wheel_joint_def;
    wheel_joint_def.Initialize(car, wheel, wheel->GetPosition());
    wheel_joint_def.enableMotor = true;
    wheel_joint_def.motorSpeed = 10.0f;       // 10 rad/s
    wheel_joint_def.maxMotorTorque = 50.0f;   // 最大力矩
    b2RevoluteJoint* wheel_joint = (b2RevoluteJoint*)world.CreateJoint(&wheel_joint_def);

    // ========== 4. 模拟 ==========
    float time_step = 1.0f / 60.0f;
    for (int i = 0; i < 300; ++i) {
        world.Step(time_step, 8, 3);

        if (i % 60 == 0) {
            printf("t=%.1f: door_angle=%.2f | car=(%.2f,%.2f) wheel_angle=%.2f\n",
                   i * time_step,
                   door->GetAngle(),
                   car->GetPosition().x, car->GetPosition().y,
                   wheel->GetAngle());
        }

        // 3 秒后切换马达方向
        if (i == 180) {
            wheel_joint->SetMotorSpeed(-10.0f);
        }
    }

    return 0;
}
```

**预期行为**：门在 -90~+90 度间摆动；车轮在马达驱动下旋转，带动车前进。

### 总结

- 旋转关节让两刚体绕共享锚点旋转，像铰链。
- 角度限制 `lowerAngle/upperAngle` 限制旋转范围，需 `enableLimit=true`。
- 马达 `motorSpeed/maxMotorTorque` 主动驱动旋转，受最大力矩限制。
- `Initialize(bodyA, bodyB, worldAnchor)` 自动设置本地锚点。
- **应用场景**：门、车轮、风扇、杠杆、布娃娃关节、钟摆。
- **常见坑**：马达力矩过小无法驱动重物；角度限制需启用才生效。

---

## 第 19 讲 · 棱柱关节 b2PrismaticJoint

### 概念

**b2PrismaticJoint** 强制两个刚体沿一条轴相对滑动，禁止旋转。它像活塞或抽屉滑轨，两物体只能沿轴方向平移，不能旋转或偏离轴。

棱柱关节适合：活塞、抽屉、电梯、弹簧减震器、滑动门。

### 原理

**关节定义 b2PrismaticJointDef**：

```cpp
struct b2PrismaticJointDef {
    b2Body* bodyA;
    b2Body* bodyB;
    b2Vec2 localAnchorA;
    b2Vec2 localAnchorB;
    b2Vec2 localAxisA;       // 滑动轴（A 的本地坐标方向）
    bool enableLimit;        // 是否启用位移限制
    float lowerTranslation;  // 最小位移（米）
    float upperTranslation;  // 最大位移（米）
    bool enableMotor;        // 是否启用马达
    float motorSpeed;        // 马达目标速度（m/s）
    float maxMotorForce;     // 马达最大力（N）
};
```

**滑动轴 localAxisA**：定义滑动的方向，是 A 的本地坐标系中的单位向量。例如 `(1, 0)` 表示沿 X 轴滑动，`(0, 1)` 表示沿 Y 轴滑动。

**位移限制**：`lowerTranslation` 和 `upperTranslation` 限制相对位移范围。例如抽屉只能拉出 0~0.5 米。

**马达**：与旋转关节类似，但驱动的是平移而非旋转。`motorSpeed` 是目标线速度，`maxMotorForce` 是最大力。

**Initialize 方法**：

```cpp
joint_def.Initialize(bodyA, bodyB, worldAnchor, worldAxis);
// worldAnchor: 锚点世界坐标
// worldAxis: 滑动轴世界方向（会归一化）
```

**查询关节状态**：

```cpp
float GetJointTranslation() const;  // 当前位移（米）
float GetJointSpeed() const;        // 当前速度（m/s）
```

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>

int main() {
    b2World world(b2Vec2(0.0f, 9.8f));

    // ========== 1. 地面 ==========
    b2BodyDef ground_def;
    ground_def.position.Set(0.0f, 10.0f);
    b2Body* ground = world.CreateBody(&ground_def);
    b2PolygonShape ground_shape;
    ground_shape.SetAsBox(50.0f, 1.0f);
    ground->CreateFixture(&ground_shape, 0.0f);

    // ========== 2. 电梯（垂直滑动） ==========
    b2BodyDef elevator_def;
    elevator_def.type = b2_dynamicBody;
    elevator_def.position.Set(0.0f, 8.0f);
    elevator_def.gravityScale = 0;  // 不受重力，由马达控制
    b2Body* elevator = world.CreateBody(&elevator_def);
    b2PolygonShape elevator_shape;
    elevator_shape.SetAsBox(1.0f, 0.2f);
    b2FixtureDef elevator_fix;
    elevator_fix.shape = &elevator_shape;
    elevator_fix.density = 1.0f;
    elevator->CreateFixture(&elevator_fix);

    // 棱柱关节：沿 Y 轴滑动
    b2PrismaticJointDef elev_joint_def;
    elev_joint_def.Initialize(ground, elevator, elevator->GetPosition(), b2Vec2(0, 1));
    elev_joint_def.enableLimit = true;
    elev_joint_def.lowerTranslation = 0.0f;    // 最低
    elev_joint_def.upperTranslation = 5.0f;    // 最高 5 米
    elev_joint_def.enableMotor = true;
    elev_joint_def.motorSpeed = 2.0f;          // 2 m/s 向上
    elev_joint_def.maxMotorForce = 100.0f;
    b2PrismaticJoint* elev_joint = (b2PrismaticJoint*)world.CreateJoint(&elev_joint_def);

    // ========== 3. 滑动门（水平滑动） ==========
    b2BodyDef door_def;
    door_def.type = b2_dynamicBody;
    door_def.position.Set(5.0f, 5.0f);
    door_def.gravityScale = 0;
    b2Body* door = world.CreateBody(&door_def);
    b2PolygonShape door_shape;
    door_shape.SetAsBox(0.8f, 1.0f);
    b2FixtureDef door_fix;
    door_fix.shape = &door_shape;
    door_fix.density = 1.0f;
    door->CreateFixture(&door_fix);

    b2PrismaticJointDef door_joint_def;
    door_joint_def.Initialize(ground, door, door->GetPosition(), b2Vec2(1, 0));
    door_joint_def.enableLimit = true;
    door_joint_def.lowerTranslation = -2.0f;
    door_joint_def.upperTranslation = 2.0f;
    door_joint_def.enableMotor = true;
    door_joint_def.motorSpeed = 0;
    door_joint_def.maxMotorForce = 50.0f;
    b2PrismaticJoint* door_joint = (b2PrismaticJoint*)world.CreateJoint(&door_joint_def);

    // ========== 4. 模拟 ==========
    float time_step = 1.0f / 60.0f;
    for (int i = 0; i < 600; ++i) {
        world.Step(time_step, 8, 3);

        // 电梯往返
        float elev_pos = elev_joint->GetJointTranslation();
        if (elev_pos >= 4.8f) elev_joint->SetMotorSpeed(-2.0f);
        if (elev_pos <= 0.2f) elev_joint->SetMotorSpeed(2.0f);

        // 门开关
        if (i == 120) door_joint->SetMotorSpeed(3.0f);   // 开门
        if (i == 300) door_joint->SetMotorSpeed(-3.0f);  // 关门

        if (i % 60 == 0) {
            printf("t=%.1f: elev=%.2f door=%.2f\n",
                   i * time_step,
                   elev_joint->GetJointTranslation(),
                   door_joint->GetJointTranslation());
        }
    }

    return 0;
}
```

**预期行为**：电梯在 0~5 米间往返；门在 2 秒时打开，5 秒时关闭。

### 总结

- 棱柱关节让两刚体沿轴滑动，禁止旋转，像活塞。
- `localAxisA` 定义滑动方向，`enableLimit` 限制位移范围。
- 马达 `motorSpeed/maxMotorForce` 主动驱动平移。
- **应用场景**：电梯、滑动门、活塞、抽屉、弹簧减震器。
- **常见坑**：滑动轴必须归一化（Initialize 自动处理）；忘记 `gravityScale=0` 会导致重力干扰马达。

---

## 第 20 讲 · 滑轮关节与齿轮关节

### 概念

**b2PulleyJoint** 模拟滑轮系统：两个刚体通过绳子绕过滑轮连接，一个上升另一个下降。位移比由两个滑轮半径决定。

**b2GearJoint** 联动两个旋转关节或棱柱关节，让它们的运动按比例关联。如齿轮传动：一个齿轮转动带动另一个齿轮反向转动。

### 原理

**滑轮关节 b2PulleyJointDef**：

```cpp
struct b2PulleyJointDef {
    b2Body* bodyA;
    b2Body* bodyB;
    b2Vec2 groundAnchorA;    // 滑轮 A 的固定点（世界坐标）
    b2Vec2 groundAnchorB;    // 滑轮 B 的固定点
    b2Vec2 localAnchorA;     // A 上的绳子锚点
    b2Vec2 localAnchorB;     // B 上的绳子锚点
    float ratio;             // 位移比（A 上升 ratio 米，B 下降 1 米）
};
```

**位移比 ratio**：模拟不同半径的滑轮。`ratio = 1` 是等比滑轮（A 上升 1 米，B 下降 1 米）；`ratio = 2` 是省力滑轮（A 上升 2 米，B 下降 1 米，B 端力是 A 端的 2 倍）。

**约束公式**：`lengthA + ratio × lengthB = constant`，其中 length 是绳子从滑轮到物体的长度。

**齿轮关节 b2GearJointDef**：

```cpp
struct b2GearJointDef {
    b2Body* bodyA;
    b2Body* bodyB;
    b2Joint* joint1;    // 第一个关节（旋转或棱柱）
    b2Joint* joint2;    // 第二个关节（旋转或棱柱）
    float ratio;        // 传动比
};
```

**传动比 ratio**：`joint2 的运动 = ratio × joint1 的运动`。`ratio = 1` 等比传动；`ratio = -1` 反向等比（如啮合齿轮）；`ratio = 2` 增速传动。

**关节类型限制**：joint1 和 joint2 必须是旋转关节或棱柱关节，且其 bodyA 必须是同一个静态刚体（齿轮的轴）。

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>

int main() {
    b2World world(b2Vec2(0.0f, 9.8f));

    // ========== 1. 滑轮系统 ==========
    // 两个物体通过滑轮连接，一个上升另一个下降
    b2BodyDef ground_def;
    ground_def.position.Set(0.0f, 0.0f);
    b2Body* ground = world.CreateBody(&ground_def);

    // 物体 A
    b2BodyDef a_def;
    a_def.type = b2_dynamicBody;
    a_def.position.Set(-3.0f, 5.0f);
    b2Body* bodyA = world.CreateBody(&a_def);
    b2PolygonShape box_shape;
    box_shape.SetAsBox(0.5f, 0.5f);
    b2FixtureDef box_fix;
    box_fix.shape = &box_shape;
    box_fix.density = 1.0f;
    bodyA->CreateFixture(&box_fix);

    // 物体 B（更重）
    b2BodyDef b_def;
    b_def.type = b2_dynamicBody;
    b_def.position.Set(3.0f, 5.0f);
    b2Body* bodyB = world.CreateBody(&b_def);
    b2FixtureDef box_fix2;
    box_fix2.shape = &box_shape;
    box_fix2.density = 2.0f;  // 更重
    bodyB->CreateFixture(&box_fix2);

    // 滑轮关节
    b2PulleyJointDef pulley_def;
    pulley_def.Initialize(bodyA, bodyB,
                          b2Vec2(-3.0f, 1.0f),  // 滑轮 A 位置
                          b2Vec2(3.0f, 1.0f),   // 滑轮 B 位置
                          bodyA->GetPosition(),
                          bodyB->GetPosition(),
                          1.0f);  // 等比
    world.CreateJoint(&pulley_def);

    // ========== 2. 齿轮系统 ==========
    // 两个齿轮联动
    b2BodyDef gear_ground_def;
    gear_ground_def.position.Set(0.0f, 15.0f);
    b2Body* gear_ground = world.CreateBody(&gear_ground_def);

    // 齿轮 1
    b2BodyDef g1_def;
    g1_def.type = b2_dynamicBody;
    g1_def.position.Set(-1.0f, 15.0f);
    b2Body* gear1 = world.CreateBody(&g1_def);
    b2CircleShape gear_shape;
    gear_shape.m_radius = 1.0f;
    b2FixtureDef gear_fix;
    gear_fix.shape = &gear_shape;
    gear_fix.density = 1.0f;
    gear1->CreateFixture(&gear_fix);

    // 齿轮 2
    b2BodyDef g2_def;
    g2_def.type = b2_dynamicBody;
    g2_def.position.Set(2.0f, 15.0f);
    b2Body* gear2 = world.CreateBody(&g2_def);
    b2CircleShape gear2_shape;
    gear2_shape.m_radius = 0.5f;  // 小齿轮
    b2FixtureDef gear2_fix;
    gear2_fix.shape = &gear2_shape;
    gear2_fix.density = 1.0f;
    gear2->CreateFixture(&gear2_fix);

    // 旋转关节（齿轮轴）
    b2RevoluteJointDef rj1_def, rj2_def;
    rj1_def.Initialize(gear_ground, gear1, gear1->GetPosition());
    b2RevoluteJoint* rj1 = (b2RevoluteJoint*)world.CreateJoint(&rj1_def);
    rj2_def.Initialize(gear_ground, gear2, gear2->GetPosition());
    b2RevoluteJoint* rj2 = (b2RevoluteJoint*)world.CreateJoint(&rj2_def);

    // 齿轮关节联动（ratio = -2，反向且小齿轮转速 2 倍）
    b2GearJointDef gear_joint_def;
    gear_joint_def.bodyA = gear_ground;
    gear_joint_def.bodyB = gear2;
    gear_joint_def.joint1 = rj1;
    gear_joint_def.joint2 = rj2;
    gear_joint_def.ratio = -2.0f;  // 齿轮1转1圈，齿轮2反向转2圈
    world.CreateJoint(&gear_joint_def);

    // 给齿轮 1 一个初速度
    gear1->SetAngularVelocity(1.0f);

    // ========== 3. 模拟 ==========
    float time_step = 1.0f / 60.0f;
    for (int i = 0; i < 180; ++i) {
        world.Step(time_step, 8, 3);

        if (i % 60 == 0) {
            printf("t=%.1f: A=(%.2f,%.2f) B=(%.2f,%.2f) | g1=%.2f g2=%.2f\n",
                   i * time_step,
                   bodyA->GetPosition().x, bodyA->GetPosition().y,
                   bodyB->GetPosition().x, bodyB->GetPosition().y,
                   gear1->GetAngle(), gear2->GetAngle());
        }
    }

    return 0;
}
```

**预期行为**：重物 B 下降，轻物 A 上升；齿轮 1 转 1 弧度，齿轮 2 反向转 2 弧度。

### 总结

- 滑轮关节模拟滑轮系统，`ratio` 控制位移比与力比。
- 齿轮关节联动两个旋转/棱柱关节，`ratio` 控制传动比与方向。
- 齿轮关节的 joint1/joint2 必须共享同一个静态 bodyA 作为轴。
- **应用场景**：滑轮（电梯、起重机）、齿轮传动（钟表、变速箱）、联动装置。
- **常见坑**：齿轮关节的 ratio 符号决定方向；滑轮的 groundAnchor 必须是固定点。

---

## 第 21 讲 · 焊接关节与鼠标关节

### 概念

**b2WeldJoint** 将两个刚体刚性焊接在一起，相对位置与角度固定。它像把两个物体焊死，运动完全同步。适合可破坏物体（拼接的箱子被击碎后分离）。

**b2MouseJoint** 让玩家通过鼠标拖拽物体。它用弹簧连接鼠标位置与物体上的点，物体被"拉"向鼠标，但不会瞬移，保持物理真实。

### 原理

**焊接关节 b2WeldJointDef**：

```cpp
struct b2WeldJointDef {
    b2Body* bodyA;
    b2Body* bodyB;
    b2Vec2 localAnchorA;
    b2Vec2 localAnchorB;
    float referenceAngle;   // 参考角度（B 相对 A 的初始角度）
    float stiffness;        // 刚度（柔性焊接，Box2D 2.4+）
    float damping;          // 阻尼
};
```

**柔性焊接**：`stiffness > 0` 时焊接有弹性，可轻微变形。适合模拟可弯曲的连接（如链条节）。

**鼠标关节 b2MouseJointDef**：

```cpp
struct b2MouseJointDef {
    b2Body* bodyA;          // 通常是世界外的"虚拟地面"
    b2Body* bodyB;          // 被拖拽的物体
    b2Vec2 target;          // 鼠标目标位置（世界坐标）
    float maxForce;         // 最大力（N）
    float stiffness;        // 弹簧刚度
    float damping;          // 阻尼
};
```

**鼠标关节的工作原理**：

1. 玩家点击物体，记录锚点（物体上的点）。
2. 每帧更新 `target` 为当前鼠标位置。
3. 关节用弹簧把锚点拉向 target，受 `maxForce` 限制。
4. 物体被"拉"向鼠标，但受质量与力限制，不会瞬移。

**bodyA 的选择**：鼠标关节的 bodyA 通常是 `b2_staticBody`（地面），但 Box2D 要求它存在。可用 `world.CreateBody(&empty_def)` 创建一个空静态体。

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>

int main() {
    b2World world(b2Vec2(0.0f, 9.8f));

    // ========== 1. 地面 ==========
    b2BodyDef ground_def;
    ground_def.position.Set(0.0f, 10.0f);
    b2Body* ground = world.CreateBody(&ground_def);
    b2PolygonShape ground_shape;
    ground_shape.SetAsBox(50.0f, 1.0f);
    ground->CreateFixture(&ground_shape, 0.0f);

    // ========== 2. 焊接关节：拼接箱子 ==========
    b2Body* parts[3];
    for (int i = 0; i < 3; ++i) {
        b2BodyDef part_def;
        part_def.type = b2_dynamicBody;
        part_def.position.Set(5.0f + i * 1.0f, 5.0f);
        parts[i] = world.CreateBody(&part_def);
        b2PolygonShape part_shape;
        part_shape.SetAsBox(0.5f, 0.5f);
        b2FixtureDef part_fix;
        part_fix.shape = &part_shape;
        part_fix.density = 1.0f;
        parts[i]->CreateFixture(&part_fix);
    }

    // 焊接 parts[0]-parts[1], parts[1]-parts[2]
    for (int i = 0; i < 2; ++i) {
        b2WeldJointDef weld_def;
        weld_def.Initialize(parts[i], parts[i+1], parts[i+1]->GetPosition());
        world.CreateJoint(&weld_def);
    }
    // 三个箱子焊成一体，整体运动

    // ========== 3. 鼠标关节：拖拽物体 ==========
    b2BodyDef drag_target_def;
    drag_target_def.type = b2_dynamicBody;
    drag_target_def.position.Set(-5.0f, 5.0f);
    b2Body* drag_box = world.CreateBody(&drag_target_def);
    b2PolygonShape drag_shape;
    drag_shape.SetAsBox(0.5f, 0.5f);
    b2FixtureDef drag_fix;
    drag_fix.shape = &drag_shape;
    drag_fix.density = 1.0f;
    drag_box->CreateFixture(&drag_fix);

    b2MouseJoint* mouse_joint = nullptr;
    b2Vec2 mouse_pos(-5.0f, 5.0f);

    // ========== 4. 模拟（模拟鼠标移动） ==========
    float time_step = 1.0f / 60.0f;
    for (int i = 0; i < 300; ++i) {
        // 模拟鼠标移动：圆形轨迹
        float t = i * time_step;
        mouse_pos.Set(-5.0f + 3.0f * cosf(t * 2.0f),
                      5.0f + 3.0f * sinf(t * 2.0f));

        // 第 30 帧创建鼠标关节（模拟点击）
        if (i == 30 && !mouse_joint) {
            b2MouseJointDef mj_def;
            mj_def.bodyA = ground;  // 虚拟地面
            mj_def.bodyB = drag_box;
            mj_def.target = drag_box->GetPosition();
            mj_def.maxForce = 1000.0f;
            mj_def.stiffness = 1000.0f;
            mj_def.damping = 50.0f;
            mouse_joint = (b2MouseJoint*)world.CreateJoint(&mj_def);
        }

        // 更新鼠标目标
        if (mouse_joint) {
            mouse_joint->SetTarget(mouse_pos);
        }

        world.Step(time_step, 8, 3);

        if (i % 60 == 0) {
            printf("t=%.1f: drag_box=(%.2f,%.2f) mouse=(%.2f,%.2f)\n",
                   i * time_step,
                   drag_box->GetPosition().x, drag_box->GetPosition().y,
                   mouse_pos.x, mouse_pos.y);
        }
    }

    return 0;
}
```

**预期行为**：三个焊接箱子整体下落；鼠标关节创建后，物体被拉向鼠标轨迹。

### 总结

- 焊接关节刚性连接两刚体，运动完全同步，适合可破坏物体。
- 柔性焊接（stiffness > 0）允许轻微变形，模拟可弯曲连接。
- 鼠标关节用弹簧拖拽物体，保持物理真实，适合玩家交互。
- 鼠标关节的 bodyA 通常是空静态体，bodyB 是被拖物体。
- **应用场景**：焊接（拼接物体、可破坏结构）、鼠标拖拽（编辑器、抓取物体）。
- **常见坑**：鼠标关节 maxForce 过小无法拖动重物；焊接后销毁刚体需先销毁关节。

---

# 第 6 章 · 高级特性

本章涵盖 Box2D 的高级功能：连续碰撞检测（CCD）解决高速物体穿透问题、射线投射实现视线与命中检测、世界查询批量获取区域内的物体、求解器参数调优优化模拟稳定性。这些特性让你能处理子弹穿透、AI 视野、爆炸范围伤害等复杂场景，并让物理模拟在极端情况下保持稳定。

## 第 22 讲 · 连续碰撞检测 CCD

### 概念

**连续碰撞检测（Continuous Collision Detection, CCD）** 解决高速物体穿透薄壁的问题。默认的离散碰撞检测每帧检查一次位置，若物体在一帧内移动距离大于障碍物厚度，会直接"跳过"障碍物，产生穿透。

CCD 通过在两帧之间进行射线投射，检测物体运动路径上是否与障碍物相交，从而避免穿透。Box2D 提供两种 CCD 模式：`b2_bullet`（针对单个高速物体）和 `world.SetContinuousPhysics(true)`（全局启用）。

### 原理

**穿透问题示例**：

```
帧 N：子弹在 x=0，墙在 x=10（厚 1 米）
帧 N+1：子弹在 x=20（一帧移动 20 米）
离散检测：帧 N 时子弹与墙不重叠，帧 N+1 时也不重叠（已穿过）
结果：子弹穿透墙壁
```

**CCD 的工作原理**：

1. **运动扫描**：对启用 CCD 的物体，Box2D 在 Step 开始时计算其从上一帧位置到当前位置的运动路径。
2. **射线投射**：沿路径进行射线投射，检测是否与障碍物相交。
3. **时间撞击点**：若相交，计算撞击的时间点 `t`（0~1 之间），把物体移动到撞击点位置。
4. **求解碰撞**：在撞击点求解碰撞响应。

**两种 CCD 模式**：

- **b2_bullet（局部 CCD）**：在 bodyDef 中设置 `bullet = true`，仅对该刚体启用 CCD。适合子弹、箭矢等少数高速物体。
- **SetContinuousPhysics（全局 CCD）**：`world.SetContinuousPhysics(true)`，对所有动态刚体启用 CCD。性能开销大，通常不推荐。

**性能考量**：CCD 比离散检测慢约 2~5 倍，应仅对真正需要的高速物体启用。大多数游戏只需对子弹启用 `bullet = true`，其他物体用离散检测。

**CCD 的限制**：

- 仅检测平移运动，不检测快速旋转导致的穿透。
- 仅对动态刚体生效，运动学刚体不参与 CCD。
- 不能完全消除穿透，极端情况下仍可能有微小穿透。

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>

int main() {
    b2World world(b2Vec2(0.0f, 0.0f));  // 无重力，便于观察

    // ========== 1. 薄墙 ==========
    b2BodyDef wall_def;
    wall_def.position.Set(10.0f, 0.0f);
    b2Body* wall = world.CreateBody(&wall_def);
    b2PolygonShape wall_shape;
    wall_shape.SetAsBox(0.1f, 5.0f);  // 厚 0.2 米的薄墙
    b2FixtureDef wall_fix;
    wall_fix.shape = &wall_shape;
    wall_fix.friction = 0.0f;
    wall_fix.restitution = 0.5f;
    wall->CreateFixture(&wall_fix);

    // ========== 2. 普通子弹（无 CCD，会穿透） ==========
    b2BodyDef bullet1_def;
    bullet1_def.type = b2_dynamicBody;
    bullet1_def.position.Set(0.0f, 1.0f);
    bullet1_def.bullet = false;  // 不启用 CCD
    b2Body* bullet1 = world.CreateBody(&bullet1_def);
    b2CircleShape bullet_shape;
    bullet_shape.m_radius = 0.2f;
    b2FixtureDef bullet_fix;
    bullet_fix.shape = &bullet_shape;
    bullet_fix.density = 1.0f;
    bullet_fix.restitution = 0.5f;
    bullet1->CreateFixture(&bullet_fix);
    bullet1->SetLinearVelocity(b2Vec2(50.0f, 0.0f));  // 50 m/s，一帧移动 0.83 米

    // ========== 3. CCD 子弹（启用 CCD，不穿透） ==========
    b2BodyDef bullet2_def;
    bullet2_def.type = b2_dynamicBody;
    bullet2_def.position.Set(0.0f, -1.0f);
    bullet2_def.bullet = true;  // 启用 CCD
    b2Body* bullet2 = world.CreateBody(&bullet2_def);
    bullet2->CreateFixture(&bullet_fix);
    bullet2->SetLinearVelocity(b2Vec2(50.0f, 0.0f));

    // ========== 4. 模拟 ==========
    float time_step = 1.0f / 60.0f;
    for (int i = 0; i < 60; ++i) {
        world.Step(time_step, 8, 3);

        if (i % 5 == 0) {
            printf("t=%.3f: bullet1=(%.2f,%.2f) | bullet2=(%.2f,%.2f)\n",
                   i * time_step,
                   bullet1->GetPosition().x, bullet1->GetPosition().y,
                   bullet2->GetPosition().x, bullet2->GetPosition().y);
        }
    }

    // 预期：bullet1 穿过墙（x > 10.2），bullet2 被墙挡住（x ≈ 9.8）
    printf("\nFinal: bullet1.x=%.2f (穿透), bullet2.x=%.2f (被挡)\n",
           bullet1->GetPosition().x, bullet2->GetPosition().x);

    return 0;
}
```

**预期结果**：bullet1 穿过薄墙（位置 > 10.2），bullet2 被 CCD 检测挡住（位置 ≈ 9.8）。

### 总结

- CCD 解决高速物体穿透薄壁的问题，通过运动路径射线投射检测。
- `bullet = true` 对单个刚体启用 CCD，性能开销小，推荐用于子弹。
- `SetContinuousPhysics(true)` 全局启用，性能开销大，通常不推荐。
- CCD 仅检测平移，不检测快速旋转；仅对动态刚体生效。
- **应用场景**：子弹、箭矢、高速抛射物、激光武器。
- **常见坑**：对所有物体启用 CCD 会严重拖慢性能；CCD 不能完全消除穿透，极端情况仍有微小穿透。

---

## 第 23 讲 · 射线投射 RayCast

### 概念

**射线投射（Ray Casting）** 从一点沿某方向发射射线，检测与场景中物体的交点，返回最近命中点、法向量、命中的 fixture。这是 AI 视线检测、子弹命中判定、激光武器、光照计算的基础。

Box2D 提供 `RayCast` 回调接口，支持多种查询模式：最近命中、所有命中、首个命中、自定义过滤。

### 原理

**b2RayCastCallback 接口**：

```cpp
class b2RayCastCallback {
public:
    virtual float ReportFixture(b2Fixture* fixture, const b2Vec2& point,
                                const b2Vec2& normal, float fraction) = 0;
};
```

**回调的返回值 fraction** 控制射线的行为：

- `-1`：忽略此 fixture，继续检测其他 fixture。
- `0`：终止射线投射，不返回任何命中。
- `1`：继续检测，记录此命中但不缩短射线（用于获取所有命中）。
- `0~1` 之间：缩短射线到此 fraction，只检测更近的命中。返回最小 fraction 即得最近命中。

**四种常见查询模式**：

1. **最近命中**：记录所有命中，返回 fraction 最小者。每次回调返回当前 fraction，射线逐渐缩短。
2. **所有命中**：每次回调返回 1，不缩短射线，收集所有命中。
3. **首个命中**：第一次回调即返回 fraction，终止检测。
4. **自定义过滤**：根据 fixture 的 userData 或属性决定是否忽略。

**RayCast 的参数**：

```cpp
world.RayCast(callback, point1, point2);
// point1: 射线起点
// point2: 射线终点（射线是线段，不是无限射线）
```

注意 Box2D 的 RayCast 是**线段**检测，从 point1 到 point2，不是无限延伸的射线。

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>
#include <vector>

// ========== 1. 最近命中回调 ==========
class ClosestRayCast : public b2RayCastCallback {
public:
    b2Fixture* fixture = nullptr;
    b2Vec2 point;
    b2Vec2 normal;
    float fraction = 1.0f;

    float ReportFixture(b2Fixture* f, const b2Vec2& p,
                        const b2Vec2& n, float frac) override {
        // 忽略传感器
        if (f->IsSensor()) return -1.0f;

        // 记录最近命中
        fixture = f;
        point = p;
        normal = n;
        fraction = frac;
        return frac;  // 缩短射线，只找更近的
    }
};

// ========== 2. 所有命中回调 ==========
class AllRayCast : public b2RayCastCallback {
public:
    struct Hit { b2Fixture* fixture; b2Vec2 point; b2Vec2 normal; float fraction; };
    std::vector<Hit> hits;

    float ReportFixture(b2Fixture* f, const b2Vec2& p,
                        const b2Vec2& n, float frac) override {
        hits.push_back({f, p, n, frac});
        return 1.0f;  // 不缩短，继续检测
    }
};

// ========== 3. 自定义过滤（忽略特定类型） ==========
class FilteredRayCast : public b2RayCastCallback {
public:
    int ignore_type;  // 要忽略的类型
    b2Fixture* fixture = nullptr;
    b2Vec2 point;
    b2Vec2 normal;

    float ReportFixture(b2Fixture* f, const b2Vec2& p,
                        const b2Vec2& n, float frac) override {
        int type = (intptr_t)f->GetUserData();
        if (type == ignore_type) return -1.0f;  // 忽略此类型

        fixture = f;
        point = p;
        normal = n;
        return frac;
    }
};

int main() {
    b2World world(b2Vec2(0.0f, 9.8f));

    // 创建几堵墙
    b2BodyDef wall_def;
    wall_def.position.Set(0.0f, 0.0f);
    b2Body* wall = world.CreateBody(&wall_def);

    b2PolygonShape shape1;
    shape1.SetAsBox(0.5f, 3.0f);
    b2FixtureDef fix1;
    fix1.shape = &shape1;
    fix1.userData = (void*)1;  // 类型 1：墙
    wall->CreateFixture(&fix1);

    b2BodyDef wall2_def;
    wall2_def.position.Set(5.0f, 0.0f);
    b2Body* wall2 = world.CreateBody(&wall2_def);
    b2PolygonShape shape2;
    shape2.SetAsBox(0.5f, 3.0f);
    b2FixtureDef fix2;
    fix2.shape = &shape2;
    fix2.userData = (void*)1;
    wall2->CreateFixture(&fix2);

    // ========== 测试 1：最近命中 ==========
    ClosestRayCast closest;
    world.RayCast(&closest, b2Vec2(-5.0f, 0.0f), b2Vec2(10.0f, 0.0f));
    if (closest.fixture) {
        printf("最近命中: point=(%.2f,%.2f) normal=(%.2f,%.2f) frac=%.2f\n",
               closest.point.x, closest.point.y,
               closest.normal.x, closest.normal.y, closest.fraction);
    }

    // ========== 测试 2：所有命中 ==========
    AllRayCast all;
    world.RayCast(&all, b2Vec2(-5.0f, 0.0f), b2Vec2(10.0f, 0.0f));
    printf("所有命中: %d 个\n", (int)all.hits.size());
    for (const auto& h : all.hits) {
        printf("  point=(%.2f,%.2f) frac=%.2f\n", h.point.x, h.point.y, h.fraction);
    }

    // ========== 测试 3：AI 视线检测 ==========
    // 敌人在 (3, 0)，玩家在 (-3, 0)，中间有墙
    b2Vec2 enemy(3.0f, 0.0f);
    b2Vec2 player(-3.0f, 0.0f);
    ClosestRayCast sight;
    world.RayCast(&sight, enemy, player);
    bool can_see = !sight.fixture || sight.fraction >= 0.95f;
    printf("敌人能看到玩家: %s (frac=%.2f)\n",
           can_see ? "是" : "否", sight.fraction);

    return 0;
}
```

### 总结

- RayCast 从起点到终点检测线段与物体的交点。
- 回调返回值控制行为：-1 忽略、0 终止、1 继续、0~1 缩短射线。
- 四种模式：最近命中（返回 fraction）、所有命中（返回 1）、首个命中（返回 fraction 终止）、自定义过滤。
- **应用场景**：AI 视线、子弹命中、激光武器、光照计算、距离测量。
- **常见坑**：RayCast 是线段不是无限射线；忽略传感器需在回调中检查 `IsSensor()`。

---

## 第 24 讲 · 世界查询 AABB Query

### 概念

**AABB 查询（AABB Query）** 获取与指定矩形区域重叠的所有 fixture。它是射线投射的"区域版"——不是检测一条线，而是检测一个矩形区域内的所有物体。

AABB 查询适合：爆炸范围伤害、区域技能效果、视野扇形检测（配合射线投射）、批量选择物体、空间分区优化。

### 原理

**b2QueryCallback 接口**：

```cpp
class b2QueryCallback {
public:
    virtual bool ReportFixture(b2Fixture* fixture) = 0;
};
```

**回调返回值**：

- `true`：继续查询其他 fixture。
- `false`：终止查询。

**查询方法**：

```cpp
world.QueryAABB(callback, aabb);
// aabb: b2AABB 结构，包含 lowerBound 和 upperBound
```

**b2AABB 的构造**：

```cpp
b2AABB aabb;
aabb.lowerBound.Set(x_min, y_min);
aabb.upperBound.Set(x_max, y_max);
```

**查询的是 AABB 而非形状**：QueryAABB 检测的是 fixture 的 AABB 是否与查询区域重叠，而非形状本身。若需精确检测形状是否在区域内，需在回调中进一步检查（如点包含、距离判断）。

**与 RayCast 配合**：典型应用是"视野扇形检测"——先用 AABB 查询获取视野范围内的物体，再用 RayCast 检测每个物体是否被障碍物遮挡。

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>
#include <vector>

// ========== 1. 收集区域内所有 fixture ==========
class AreaQuery : public b2QueryCallback {
public:
    std::vector<b2Fixture*> fixtures;

    bool ReportFixture(b2Fixture* fixture) override {
        if (!fixture->IsSensor()) {  // 忽略传感器
            fixtures.push_back(fixture);
        }
        return true;  // 继续查询
    }
};

// ========== 2. 爆炸伤害查询 ==========
class ExplosionQuery : public b2QueryCallback {
public:
    b2Vec2 center;
    float radius;
    std::vector<std::pair<b2Body*, float>> victims;  // body, 距离

    bool ReportFixture(b2Fixture* fixture) override {
        b2Body* body = fixture->GetBody();
        b2Vec2 pos = body->GetPosition();
        float dist = (pos - center).Length();

        if (dist <= radius) {
            victims.push_back({body, dist});
        }
        return true;
    }
};

// ========== 3. 批量选择（鼠标框选） ==========
class SelectionQuery : public b2QueryCallback {
public:
    std::vector<b2Body*> selected;

    bool ReportFixture(b2Fixture* fixture) override {
        // 只选择可选中物体（通过 userData 标记）
        int type = (intptr_t)fixture->GetUserData();
        if (type == 1) {  // 1 = 可选中单位
            selected.push_back(fixture->GetBody());
        }
        return true;
    }
};

int main() {
    b2World world(b2Vec2(0.0f, 9.8f));

    // 创建一些物体
    for (int i = 0; i < 10; ++i) {
        b2BodyDef def;
        def.type = b2_dynamicBody;
        def.position.Set(-5.0f + i * 1.0f, 0.0f);
        b2Body* body = world.CreateBody(&def);
        b2CircleShape shape;
        shape.m_radius = 0.3f;
        b2FixtureDef fix;
        fix.shape = &shape;
        fix.density = 1.0f;
        fix.userData = (void*)1;  // 可选中
        body->CreateFixture(&fix);
    }

    // ========== 测试 1：区域查询 ==========
    AreaQuery area;
    b2AABB aabb;
    aabb.lowerBound.Set(-3.0f, -1.0f);
    aabb.upperBound.Set(3.0f, 1.0f);
    world.QueryAABB(&area, aabb);
    printf("区域内物体: %d 个\n", (int)area.fixtures.size());

    // ========== 测试 2：爆炸伤害 ==========
    ExplosionQuery explosion;
    explosion.center.Set(0.0f, 0.0f);
    explosion.radius = 3.0f;
    b2AABB exp_aabb;
    exp_aabb.lowerBound.Set(-3.0f, -3.0f);
    exp_aabb.upperBound.Set(3.0f, 3.0f);
    world.QueryAABB(&explosion, exp_aabb);
    printf("爆炸范围内受害者: %d 个\n", (int)explosion.victims.size());
    for (auto& v : explosion.victims) {
        printf("  距离 %.2f 米\n", v.second);
    }

    // ========== 测试 3：鼠标框选 ==========
    SelectionQuery selection;
    b2AABB sel_aabb;
    sel_aabb.lowerBound.Set(-2.0f, -0.5f);
    sel_aabb.upperBound.Set(2.0f, 0.5f);
    world.QueryAABB(&selection, sel_aabb);
    printf("选中单位: %d 个\n", (int)selection.selected.size());

    return 0;
}
```

### 总结

- AABB 查询获取与矩形区域重叠的所有 fixture。
- 回调返回 true 继续，false 终止。
- 查询的是 fixture 的 AABB，非形状本身；精确检测需在回调中进一步判断。
- **应用场景**：爆炸伤害、区域技能、鼠标框选、视野扇形（配合 RayCast）。
- **性能**：基于动态树（b2DynamicTree），复杂度接近 O(log N + K)，K 是结果数。
- **常见坑**：忘记过滤传感器导致触发器被误选；AABB 比形状大，可能有误报。

---

## 第 25 讲 · 求解器参数调优

### 概念

Box2D 的物理求解器（Solver）通过迭代计算约束力，让物体满足碰撞与关节约束。求解器的参数控制迭代次数与精度，直接影响模拟的稳定性与性能。

核心参数包括：速度迭代次数（velocityIterations）、位置迭代次数（positionIterations）、时间步长（timeStep）、以及世界级的容差参数。

### 原理

**Step 的三个参数**：

```cpp
world.Step(timeStep, velocityIterations, positionIterations);
```

**velocityIterations（速度迭代）**：

控制速度约束的求解次数。每次迭代修正一次速度误差（如穿透导致的速度反弹、关节约束的速度）。迭代越多，速度越精确，但性能越低。

- 默认 8：大多数场景适用。
- 4：简单场景，性能优先。
- 16+：复杂堆叠、高精度需求。

**positionIterations（位置迭代）**：

控制位置约束的求解次数。每次迭代修正一次位置误差（如穿透深度、关节位置偏差）。迭代越多，穿透越小，但性能越低。

- 默认 3：大多数场景适用。
- 1：简单场景。
- 8+：高精度需求（如精密机械）。

**timeStep（时间步长）**：

每帧物理模拟的时间。Box2D 推荐 1/60 秒（60Hz）。固定时间步长对稳定性至关重要——变化的 timeStep 会导致物理行为不一致。

**世界级参数**：

```cpp
world.SetWarmStarting(true);       // 热启动：加速收敛，默认开
world.SetContinuousPhysics(true);  // 全局 CCD，默认开
world.SetSubStepping(false);       // 子步进：高精度，默认关
```

**堆叠稳定性**：多个物体堆叠时，底层物体承受大压力，容易出现抖动或穿透。解决方法：

1. 增加位置迭代次数（8~16）。
2. 启用子步进 `SetSubStepping(true)`。
3. 增加物体的线性阻尼（0.1~0.5）。
4. 使用更小的 timeStep（1/120 秒）。

### 例子

```cpp
#include <box2d/box2d.h>
#include <cstdio>
#include <chrono>

int main() {
    b2World world(b2Vec2(0.0f, 9.8f));

    // 地面
    b2BodyDef ground_def;
    ground_def.position.Set(0.0f, 10.0f);
    b2Body* ground = world.CreateBody(&ground_def);
    b2PolygonShape ground_shape;
    ground_shape.SetAsBox(50.0f, 1.0f);
    ground->CreateFixture(&ground_shape, 0.0f);

    // ========== 1. 堆叠箱子（压力测试） ==========
    for (int i = 0; i < 20; ++i) {
        b2BodyDef def;
        def.type = b2_dynamicBody;
        def.position.Set(0.0f, 9.0f - i * 1.05f);
        b2Body* body = world.CreateBody(&def);
        b2PolygonShape shape;
        shape.SetAsBox(0.5f, 0.5f);
        b2FixtureDef fix;
        fix.shape = &shape;
        fix.density = 1.0f;
        fix.friction = 0.6f;
        body->CreateFixture(&fix);
    }

    // ========== 2. 不同迭代次数的性能对比 ==========
    struct Config { const char* name; int vel_iter; int pos_iter; };
    Config configs[] = {
        {"低精度 (4, 1)",  4, 1},
        {"默认 (8, 3)",    8, 3},
        {"高精度 (16, 8)", 16, 8}
    };

    float time_step = 1.0f / 60.0f;

    for (const auto& cfg : configs) {
        auto start = std::chrono::high_resolution_clock::now();

        for (int i = 0; i < 600; ++i) {  // 10 秒模拟
            world.Step(time_step, cfg.vel_iter, cfg.pos_iter);
        }

        auto end = std::chrono::high_resolution_clock::now();
        float ms = std::chrono::duration<float, std::milli>(end - start).count();

        // 检查堆叠稳定性（顶层箱子位置）
        b2Body* top = world.GetBodyList();
        for (int i = 0; i < 19; ++i) top = top->GetNext();

        printf("%s: %.1f ms, 顶层位置=(%.3f, %.3f)\n",
               cfg.name, ms, top->GetPosition().x, top->GetPosition().y);
    }

    // ========== 3. 子步进测试 ==========
    world.SetSubStepping(true);
    printf("\n启用子步进后:\n");
    for (int i = 0; i < 60; ++i) {
        world.Step(time_step, 8, 3);
    }
    world.SetSubStepping(false);

    return 0;
}
```

**预期结果**：低精度配置最快但堆叠可能抖动；高精度配置最慢但堆叠稳定。

### 总结

- velocityIterations 控制速度精度（默认 8），positionIterations 控制位置精度（默认 3）。
- 固定 timeStep（1/60 秒）对稳定性至关重要。
- 堆叠不稳定时：增加位置迭代、启用子步进、增加阻尼、减小 timeStep。
- **热启动**（WarmStarting）加速收敛，默认开启，通常不要关闭。
- **性能调优建议**：从默认 (8, 3) 开始，遇到抖动先增加位置迭代到 8，仍不稳定再启用子步进。
- **常见坑**：timeStep 不固定导致物理不一致；迭代次数过高严重拖慢性能。

---

# 第 7 章 · 实战集成

理论学完了，现在要把 Box2D 真正用起来。本章将讲解如何将 Box2D 集成到 SDL2 游戏中（包括单位转换、渲染同步、输入处理）、如何进行性能优化与调试绘制（使用 b2Draw 可视化物理世界）、最后通过一个完整的物理沙盒案例整合所有知识。学完本章，你就具备了在实际游戏项目中应用 Box2D 的全部能力。

## 第 26 讲 · Box2D 与 SDL2 集成

### 概念

将 Box2D 集成到 SDL2 游戏需要解决三个核心问题：

1. **单位转换**：Box2D 用米，SDL2 用像素，需要双向转换。
2. **渲染同步**：每帧从 Box2D 读取刚体位置与角度，更新 SDL 纹理位置。
3. **输入处理**：把 SDL 的键盘鼠标输入转换为 Box2D 的力、冲量或速度。

一个典型的集成框架包含：物理世界管理器、刚体-精灵映射、固定时间步长循环、输入到物理的转换层。

### 原理

**单位转换**：

Box2D 优化范围是 0.1~10 米的物体。游戏中的像素需要转换为米，通常 1 米 = 50 像素（PPM = Pixels Per Meter）。

```cpp
const float PPM = 50.0f;
float to_pixels(float meters) { return meters * PPM; }
float to_meters(float pixels) { return pixels / PPM; }
```

**坐标系的注意**：

- Box2D 的 Y 轴**向上**为正（数学坐标系）。
- SDL2 的 Y 轴**向下**为正（屏幕坐标系）。
- 渲染时需要翻转 Y：`screen_y = window_height - world_y * PPM`。

或者，更简单的方法是让 Box2D 的重力也向下（正 Y），这样两者方向一致，只需做缩放不需翻转。本教程采用这种方式。

**刚体-精灵映射**：

每个游戏对象包含一个 Box2D 刚体和一个 SDL 纹理。每帧从刚体读取位置和角度，更新纹理的渲染位置：

```cpp
struct GameObject {
    b2Body* body;
    SDL_Texture* texture;
    int width, height;  // 纹理尺寸（像素）

    void render(SDL_Renderer* renderer) {
        b2Vec2 pos = body->GetPosition();
        float angle = body->GetAngle();
        SDL_Rect dst = {
            (int)(pos.x * PPM) - width / 2,
            (int)(pos.y * PPM) - height / 2,
            width, height
        };
        SDL_RenderCopyEx(renderer, texture, nullptr, &dst,
                         angle * 180.0f / M_PI, nullptr, SDL_FLIP_NONE);
    }
};
```

**固定时间步长循环**：

Box2D 要求固定 timeStep，但游戏渲染帧率可能波动。使用累加器（accumulator）模式：

```cpp
const float FIXED_DT = 1.0f / 60.0f;
float accumulator = 0.0f;

while (running) {
    float frame_time = get_frame_time();
    accumulator += frame_time;
    while (accumulator >= FIXED_DT) {
        process_input();
        world.Step(FIXED_DT, 8, 3);
        accumulator -= FIXED_DT;
    }
    render();
}
```

### 例子

```cpp
#include <SDL2/SDL.h>
#include <SDL2/SDL_image.h>
#include <box2d/box2d.h>
#include <cstdio>

const float PPM = 50.0f;
const int WINDOW_W = 800;
const int WINDOW_H = 600;

float to_pixels(float meters) { return meters * PPM; }
float to_meters(float pixels) { return pixels / PPM; }

// 游戏对象：刚体 + 纹理
struct GameObject {
    b2Body* body;
    SDL_Texture* texture;
    int width, height;

    void render(SDL_Renderer* renderer) {
        b2Vec2 pos = body->GetPosition();
        float angle = body->GetAngle();
        SDL_Rect dst = {
            (int)(to_pixels(pos.x)) - width / 2,
            (int)(to_pixels(pos.y)) - height / 2,
            width, height
        };
        SDL_RenderCopyEx(renderer, texture, nullptr, &dst,
                         angle * 180.0f / M_PI, nullptr, SDL_FLIP_NONE);
    }
};

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    IMG_Init(IMG_INIT_PNG);
    SDL_Window* window = SDL_CreateWindow("Box2D + SDL2",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, WINDOW_W, WINDOW_H, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1,
        SDL_RENDERER_ACCELERATED | SDL_RENDERER_PRESENTVSYNC);

    // ========== 1. 创建物理世界 ==========
    b2World world(b2Vec2(0.0f, 9.8f));

    // ========== 2. 创建地面 ==========
    b2BodyDef ground_def;
    ground_def.position.Set(to_meters(WINDOW_W / 2), to_meters(WINDOW_H - 30));
    b2Body* ground = world.CreateBody(&ground_def);
    b2PolygonShape ground_shape;
    ground_shape.SetAsBox(to_meters(WINDOW_W / 2), to_meters(30));
    b2FixtureDef ground_fix;
    ground_fix.shape = &ground_shape;
    ground_fix.friction = 0.7f;
    ground->CreateFixture(&ground_fix);

    // ========== 3. 创建动态箱子 ==========
    SDL_Texture* box_tex = IMG_LoadTexture(renderer, "box.png");
    // 若无图片，用纯色矩形代替
    std::vector<GameObject> boxes;

    auto create_box = [&](float x, float y) {
        GameObject obj;
        obj.width = 40;
        obj.height = 40;

        b2BodyDef def;
        def.type = b2_dynamicBody;
        def.position.Set(to_meters(x), to_meters(y));
        obj.body = world.CreateBody(&def);

        b2PolygonShape shape;
        shape.SetAsBox(to_meters(20), to_meters(20));
        b2FixtureDef fix;
        fix.shape = &shape;
        fix.density = 1.0f;
        fix.friction = 0.5f;
        fix.restitution = 0.3f;
        obj.body->CreateFixture(&fix);

        obj.texture = box_tex;
        boxes.push_back(obj);
    };

    // 初始放几个箱子
    for (int i = 0; i < 5; ++i) {
        create_box(200 + i * 60, 100);
    }

    // ========== 4. 主循环 ==========
    const float FIXED_DT = 1.0f / 60.0f;
    float accumulator = 0.0f;
    Uint64 last = SDL_GetPerformanceCounter();
    const Uint8* keys = SDL_GetKeyboardState(nullptr);

    bool running = true;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) {
            if (e.type == SDL_QUIT) running = false;
            if (e.type == SDL_MOUSEBUTTONDOWN && e.button.button == SDL_BUTTON_LEFT) {
                create_box(e.button.x, e.button.y);
            }
        }

        // 计算帧时间
        Uint64 now = SDL_GetPerformanceCounter();
        float frame_time = (float)(now - last) / SDL_GetPerformanceFrequency();
        last = now;
        if (frame_time > 0.25f) frame_time = 0.25f;
        accumulator += frame_time;

        // 物理步进（固定时间步长）
        while (accumulator >= FIXED_DT) {
            world.Step(FIXED_DT, 8, 3);
            accumulator -= FIXED_DT;
        }

        // 渲染
        SDL_SetRenderDrawColor(renderer, 30, 30, 40, 255);
        SDL_RenderClear(renderer);

        // 地面
        SDL_SetRenderDrawColor(renderer, 100, 100, 100, 255);
        SDL_Rect ground_rect = { 0, WINDOW_H - 60, WINDOW_W, 60 };
        SDL_RenderFillRect(renderer, &ground_rect);

        // 箱子
        for (auto& box : boxes) {
            if (box.texture) {
                box.render(renderer);
            } else {
                // 无纹理时用纯色矩形
                b2Vec2 pos = box.body->GetPosition();
                float angle = box.body->GetAngle();
                SDL_Rect dst = {
                    (int)to_pixels(pos.x) - 20,
                    (int)to_pixels(pos.y) - 20,
                    40, 40
                };
                SDL_RenderCopyEx(renderer, nullptr, nullptr, &dst,
                                 angle * 180.0f / M_PI, nullptr, SDL_FLIP_NONE);
            }
        }

        SDL_RenderPresent(renderer);
    }

    if (box_tex) SDL_DestroyTexture(box_tex);
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    IMG_Quit();
    SDL_Quit();
    return 0;
}
```

### 总结

- 单位转换：1 米 = 50 像素（PPM），用 `to_meters/to_pixels` 双向转换。
- 坐标系：Box2D Y 向上，SDL2 Y 向下；让重力向下可避免翻转。
- 刚体-精灵映射：每帧从 body 读取位置和角度，更新纹理渲染。
- 固定时间步长用累加器模式，保证物理稳定。
- **常见坑**：忘记单位转换导致物体过大或过小；timeStep 不固定导致物理不一致。
- **设计建议**：封装 GameObject 类统一管理刚体与纹理；用对象池管理动态创建的物体。

---

## 第 27 讲 · 性能优化与调试绘制

### 概念

**性能优化** 让游戏在大量物体下仍保持 60 FPS。关键策略包括：物体池化、休眠利用、形状简化、迭代次数调优、避免频繁创建销毁。

**调试绘制（Debug Draw）** 用 b2Draw 接口可视化物理世界的形状、关节、接触点、AABB，帮助开发者理解物理行为，定位碰撞异常。

### 原理

**性能优化策略**：

1. **物体池化**：预创建一批刚体，复用而非频繁创建销毁。销毁刚体涉及内存分配与树更新，开销大。

2. **利用休眠**：让静止物体自动休眠，跳过计算。确保 `allowSleep = true`（默认）。

3. **形状简化**：用圆形代替多边形（圆形检测最快）；用 EdgeShape 代替薄多边形；避免复杂凹多边形。

4. **迭代次数调优**：从默认 (8, 3) 开始，仅在抖动时增加。简单场景可用 (4, 1)。

5. **CCD 选择性启用**：仅对子弹等高速物体启用 `bullet = true`，不要全局启用。

6. **BroadPhase 优化**：Box2D 默认用 b2DynamicTree，通常无需调整。物体数量极大（>10000）时可考虑空间分区。

**b2Draw 调试绘制接口**：

```cpp
class b2Draw {
public:
    virtual void DrawPolygon(const b2Vec2* vertices, int vertexCount, const b2Color& color) = 0;
    virtual void DrawSolidPolygon(const b2Vec2* vertices, int vertexCount, const b2Color& color) = 0;
    virtual void DrawCircle(const b2Vec2& center, float radius, const b2Color& color) = 0;
    virtual void DrawSolidCircle(const b2Vec2& center, float radius, const b2Vec2& axis, const b2Color& color) = 0;
    virtual void DrawSegment(const b2Vec2& p1, const b2Vec2& p2, const b2Color& color) = 0;
    virtual void DrawTransform(const b2Transform& xf) = 0;
    virtual void DrawPoint(const b2Vec2& p, float size, const b2Color& color) = 0;
};
```

**调试绘制标志**：

```cpp
uint32 flags = 0;
flags |= b2Draw::e_shapeBit;       // 绘制形状
flags |= b2Draw::e_jointBit;       // 绘制关节
flags |= b2Draw::e_aabbBit;        // 绘制 AABB
flags |= b2Draw::e_pairBit;        // 绘制 broad-phase 对
flags |= b2Draw::e_centerOfMassBit; // 绘制质心
debug_draw.SetFlags(flags);
world.SetDebugDraw(&debug_draw);
```

每帧调用 `world.DebugDraw()` 即可绘制所有调试信息。

### 例子

```cpp
#include <SDL2/SDL.h>
#include <box2d/box2d.h>
#include <cstdio>

const float PPM = 50.0f;

// ========== 1. SDL2 实现 b2Draw ==========
class SDLDebugDraw : public b2Draw {
public:
    SDL_Renderer* renderer;

    SDLDebugDraw(SDL_Renderer* r) : renderer(r) {
        SetFlags(b2Draw::e_shapeBit | b2Draw::e_jointBit | b2Draw::e_centerOfMassBit);
    }

    void DrawPolygon(const b2Vec2* vertices, int vertexCount, const b2Color& color) override {
        SDL_SetRenderDrawColor(renderer, color.r * 255, color.g * 255, color.b * 255, 255);
        for (int i = 0; i < vertexCount; ++i) {
            const b2Vec2& p1 = vertices[i];
            const b2Vec2& p2 = vertices[(i + 1) % vertexCount];
            SDL_RenderDrawLine(renderer,
                (int)(p1.x * PPM), (int)(p1.y * PPM),
                (int)(p2.x * PPM), (int)(p2.y * PPM));
        }
    }

    void DrawSolidPolygon(const b2Vec2* vertices, int vertexCount, const b2Color& color) override {
        SDL_SetRenderDrawColor(renderer, color.r * 255, color.g * 255, color.b * 255, 100);
        // 填充（SDL2 不支持多边形填充，用线框近似）
        DrawPolygon(vertices, vertexCount, color);
    }

    void DrawCircle(const b2Vec2& center, float radius, const b2Color& color) override {
        SDL_SetRenderDrawColor(renderer, color.r * 255, color.g * 255, color.b * 255, 255);
        const int segments = 32;
        for (int i = 0; i < segments; ++i) {
            float a1 = (float)i / segments * 2.0f * M_PI;
            float a2 = (float)(i + 1) / segments * 2.0f * M_PI;
            SDL_RenderDrawLine(renderer,
                (int)((center.x + radius * cosf(a1)) * PPM),
                (int)((center.y + radius * sinf(a1)) * PPM),
                (int)((center.x + radius * cosf(a2)) * PPM),
                (int)((center.y + radius * sinf(a2)) * PPM));
        }
    }

    void DrawSolidCircle(const b2Vec2& center, float radius, const b2Vec2& axis, const b2Color& color) override {
        DrawCircle(center, radius, color);
        // 绘制轴
        SDL_RenderDrawLine(renderer,
            (int)(center.x * PPM), (int)(center.y * PPM),
            (int)((center.x + axis.x * radius) * PPM),
            (int)((center.y + axis.y * radius) * PPM));
    }

    void DrawSegment(const b2Vec2& p1, const b2Vec2& p2, const b2Color& color) override {
        SDL_SetRenderDrawColor(renderer, color.r * 255, color.g * 255, color.b * 255, 255);
        SDL_RenderDrawLine(renderer,
            (int)(p1.x * PPM), (int)(p1.y * PPM),
            (int)(p2.x * PPM), (int)(p2.y * PPM));
    }

    void DrawTransform(const b2Transform& xf) override {
        b2Vec2 p1 = xf.p;
        b2Vec2 p2_x = p1 + b2Mul(xf.q, b2Vec2(0.5f, 0.0f));
        b2Vec2 p2_y = p1 + b2Mul(xf.q, b2Vec2(0.0f, 0.5f));
        SDL_SetRenderDrawColor(renderer, 255, 0, 0, 255);
        SDL_RenderDrawLine(renderer,
            (int)(p1.x * PPM), (int)(p1.y * PPM),
            (int)(p2_x.x * PPM), (int)(p2_x.y * PPM));
        SDL_SetRenderDrawColor(renderer, 0, 255, 0, 255);
        SDL_RenderDrawLine(renderer,
            (int)(p1.x * PPM), (int)(p1.y * PPM),
            (int)(p2_y.x * PPM), (int)(p2_y.y * PPM));
    }

    void DrawPoint(const b2Vec2& p, float size, const b2Color& color) override {
        SDL_SetRenderDrawColor(renderer, color.r * 255, color.g * 255, color.b * 255, 255);
        SDL_Rect r = { (int)(p.x * PPM) - 2, (int)(p.y * PPM) - 2, 4, 4 };
        SDL_RenderFillRect(renderer, &r);
    }
};

// ========== 2. 物体池示例 ==========
class BodyPool {
public:
    b2World* world;
    std::vector<b2Body*> pool;
    b2PolygonShape shared_shape;

    BodyPool(b2World* w, int initial_size) : world(w) {
        shared_shape.SetAsBox(0.4f, 0.4f);
        for (int i = 0; i < initial_size; ++i) {
            b2BodyDef def;
            def.type = b2_dynamicBody;
            def.position.Set(-1000.0f, -1000.0f);  // 屏幕外
            def.active = false;  // 不参与模拟
            b2Body* body = world->CreateBody(&def);
            b2FixtureDef fix;
            fix.shape = &shared_shape;
            fix.density = 1.0f;
            body->CreateFixture(&fix);
            pool.push_back(body);
        }
    }

    b2Body* acquire(const b2Vec2& pos) {
        for (b2Body* body : pool) {
            if (!body->IsActive()) {
                body->SetActive(true);
                body->SetTransform(pos, 0.0f);
                body->SetLinearVelocity(b2Vec2(0, 0));
                body->SetAngularVelocity(0.0f);
                body->SetAwake(true);
                return body;
            }
        }
        return nullptr;  // 池已满
    }

    void release(b2Body* body) {
        body->SetActive(false);
        body->SetTransform(b2Vec2(-1000.0f, -1000.0f), 0.0f);
    }
};

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Debug Draw",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 800, 600, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, 0);

    b2World world(b2Vec2(0.0f, 9.8f));
    SDLDebugDraw debug_draw(renderer);
    world.SetDebugDraw(&debug_draw);

    // 地面
    b2BodyDef ground_def;
    ground_def.position.Set(8.0f, 11.0f);
    b2Body* ground = world.CreateBody(&ground_def);
    b2PolygonShape ground_shape;
    ground_shape.SetAsBox(8.0f, 1.0f);
    ground->CreateFixture(&ground_shape, 0.0f);

    // 一些动态物体
    BodyPool pool(&world, 20);
    for (int i = 0; i < 10; ++i) {
        pool.acquire(b2Vec2(5.0f + i * 0.5f, 1.0f + i * 0.5f));
    }

    // 主循环
    bool running = true;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) {
            if (e.type == SDL_QUIT) running = false;
        }

        world.Step(1.0f / 60.0f, 8, 3);

        SDL_SetRenderDrawColor(renderer, 20, 20, 30, 255);
        SDL_RenderClear(renderer);

        // 调试绘制（绘制所有物理形状）
        world.DebugDraw();

        SDL_RenderPresent(renderer);
    }

    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- 性能优化：物体池化、利用休眠、形状简化、迭代调优、选择性 CCD。
- b2Draw 接口实现调试绘制，可视化形状、关节、接触点、AABB。
- 用 `SetFlags` 控制绘制内容，`world.DebugDraw()` 每帧调用。
- **物体池**：预创建刚体，用 `SetActive` 复用，避免频繁创建销毁。
- **调试技巧**：开启 e_shapeBit 检查形状是否正确；开启 e_centerOfMassBit 检查质心；开启 e_aabbBit 检查 broad-phase。
- **常见坑**：调试绘制在发布版应关闭；物体池忘记 SetActive(false) 导致池耗尽。

---

## 第 28 讲 · 完整游戏案例：物理沙盒

### 概念

本讲通过一个完整的物理沙盒游戏整合前 27 讲的所有知识。玩家可以：用鼠标拖拽物体、点击生成箱子、按键切换工具（箱子、球、链条）、观察物理模拟。案例涵盖：世界创建、多种形状、关节（鼠标关节、距离关节）、碰撞回调、传感器、CCD、调试绘制。

### 原理

**游戏架构**：

```
Game
├── PhysicsWorld (b2World + 碰撞监听器)
├── ObjectFactory (创建箱子、球、链条)
├── InputHandler (鼠标拖拽、工具切换)
├── Renderer (SDL2 渲染 + 调试绘制)
└── GameLoop (固定时间步长)
```

**工具系统**：

- 工具 1：箱子（动态多边形）
- 工具 2：球（动态圆形）
- 工具 3：链条（多个箱子用距离关节连接）
- 工具 4：炸弹（传感器，触发后施加爆炸力）

**鼠标拖拽**：

- 鼠标按下：用 AABB 查询找到点击的物体，创建鼠标关节。
- 鼠标移动：更新鼠标关节的 target。
- 鼠标释放：销毁鼠标关节。

**爆炸效果**：

- 炸弹传感器触发后，用 AABB 查询获取范围内的物体。
- 对每个物体施加远离爆炸中心的冲量，冲量大小与距离成反比。

### 例子

```cpp
#include <SDL2/SDL.h>
#include <box2d/box2d.h>
#include <cstdio>
#include <vector>
#include <cmath>

const float PPM = 50.0f;
const int WINDOW_W = 800;
const int WINDOW_H = 600;

float to_px(float m) { return m * PPM; }
float to_m(float p) { return p / PPM; }

// ========== 调试绘制（简化，见第 27 讲） ==========
class DebugDraw : public b2Draw {
public:
    SDL_Renderer* r;
    DebugDraw(SDL_Renderer* renderer) : r(renderer) {
        SetFlags(e_shapeBit | e_jointBit);
    }
    void DrawPolygon(const b2Vec2* v, int n, const b2Color& c) override {
        SDL_SetRenderDrawColor(r, c.r*255, c.g*255, c.b*255, 255);
        for (int i = 0; i < n; ++i) {
            const b2Vec2& a = v[i], &b = v[(i+1)%n];
            SDL_RenderDrawLine(r, to_px(a.x), to_px(a.y), to_px(b.x), to_px(b.y));
        }
    }
    void DrawSolidPolygon(const b2Vec2* v, int n, const b2Color& c) override { DrawPolygon(v, n, c); }
    void DrawCircle(const b2Vec2& center, float radius, const b2Color& c) override {
        SDL_SetRenderDrawColor(r, c.r*255, c.g*255, c.b*255, 255);
        for (int i = 0; i < 32; ++i) {
            float a1 = i / 32.0f * 2 * M_PI, a2 = (i+1) / 32.0f * 2 * M_PI;
            SDL_RenderDrawLine(r,
                to_px(center.x + radius*cosf(a1)), to_px(center.y + radius*sinf(a1)),
                to_px(center.x + radius*cosf(a2)), to_px(center.y + radius*sinf(a2)));
        }
    }
    void DrawSolidCircle(const b2Vec2& c, float r, const b2Vec2& a, const b2Color& col) override { DrawCircle(c, r, col); }
    void DrawSegment(const b2Vec2& p1, const b2Vec2& p2, const b2Color& c) override {
        SDL_SetRenderDrawColor(r, c.r*255, c.g*255, c.b*255, 255);
        SDL_RenderDrawLine(r, to_px(p1.x), to_px(p1.y), to_px(p2.x), to_px(p2.y));
    }
    void DrawTransform(const b2Transform& xf) override {}
    void DrawPoint(const b2Vec2& p, float size, const b2Color& c) override {}
};

// ========== 爆炸查询 ==========
class ExplosionQuery : public b2QueryCallback {
public:
    b2Vec2 center;
    float radius;
    float power;
    std::vector<b2Body*> victims;

    bool ReportFixture(b2Fixture* f) override {
        b2Body* body = f->GetBody();
        b2Vec2 pos = body->GetPosition();
        b2Vec2 dir = pos - center;
        float dist = dir.Length();
        if (dist < radius && dist > 0.1f) {
            victims.push_back(body);
        }
        return true;
    }

    void apply_explosion() {
        for (b2Body* body : victims) {
            b2Vec2 pos = body->GetPosition();
            b2Vec2 dir = pos - center;
            float dist = dir.Length();
            float force = power * (1.0f - dist / radius);
            dir.Normalize();
            body->ApplyLinearImpulseToCenter(dir * force * body->GetMass(), true);
        }
    }
};

// ========== 游戏类 ==========
class PhysicsSandbox {
public:
    b2World world;
    SDL_Renderer* renderer;
    DebugDraw debug_draw;
    b2MouseJoint* mouse_joint;
    b2Body* ground;
    int current_tool;  // 0=箱子, 1=球, 2=链条, 3=炸弹

    PhysicsSandbox(SDL_Renderer* r)
        : world(b2Vec2(0.0f, 9.8f)), renderer(r), debug_draw(r),
          mouse_joint(nullptr), current_tool(0) {
        world.SetDebugDraw(&debug_draw);
        create_ground();
    }

    void create_ground() {
        b2BodyDef def;
        def.position.Set(to_m(WINDOW_W/2), to_m(WINDOW_H - 30));
        ground = world.CreateBody(&def);
        b2PolygonShape shape;
        shape.SetAsBox(to_m(WINDOW_W/2), to_m(30));
        b2FixtureDef fix;
        fix.shape = &shape;
        fix.friction = 0.7f;
        ground->CreateFixture(&fix);

        // 左右墙
        b2BodyDef wall_def;
        wall_def.position.Set(0, to_m(WINDOW_H/2));
        b2Body* left_wall = world.CreateBody(&wall_def);
        b2PolygonShape wall_shape;
        wall_shape.SetAsBox(to_m(10), to_m(WINDOW_H/2));
        left_wall->CreateFixture(&wall_shape, 0.0f);

        wall_def.position.Set(to_m(WINDOW_W), to_m(WINDOW_H/2));
        b2Body* right_wall = world.CreateBody(&wall_def);
        right_wall->CreateFixture(&wall_shape, 0.0f);
    }

    b2Body* create_box(float x, float y, float size = 0.5f) {
        b2BodyDef def;
        def.type = b2_dynamicBody;
        def.position.Set(to_m(x), to_m(y));
        b2Body* body = world.CreateBody(&def);
        b2PolygonShape shape;
        shape.SetAsBox(size, size);
        b2FixtureDef fix;
        fix.shape = &shape;
        fix.density = 1.0f;
        fix.friction = 0.5f;
        fix.restitution = 0.3f;
        body->CreateFixture(&fix);
        return body;
    }

    b2Body* create_ball(float x, float y, float radius = 0.3f) {
        b2BodyDef def;
        def.type = b2_dynamicBody;
        def.position.Set(to_m(x), to_m(y));
        b2Body* body = world.CreateBody(&def);
        b2CircleShape shape;
        shape.m_radius = radius;
        b2FixtureDef fix;
        fix.shape = &shape;
        fix.density = 1.0f;
        fix.friction = 0.3f;
        fix.restitution = 0.7f;
        body->CreateFixture(&fix);
        return body;
    }

    void create_chain(float x, float y, int links = 5) {
        b2Body* prev = ground;
        b2Vec2 prev_anchor = b2Vec2(to_m(x), to_m(0));
        for (int i = 0; i < links; ++i) {
            b2Body* link = create_box(x, y + i * 30, 0.2f);
            b2DistanceJointDef joint_def;
            joint_def.Initialize(prev, link, prev_anchor, link->GetPosition());
            joint_def.length = 0.3f;
            world.CreateJoint(&joint_def);
            prev = link;
            prev_anchor = link->GetPosition();
        }
    }

    void create_bomb(float x, float y) {
        b2Body* bomb = create_box(x, y, 0.3f);
        // 标记为炸弹（用 userData）
        bomb->GetUserData().pointer = 99;

        // 创建传感器范围
        b2CircleShape sensor_shape;
        sensor_shape.m_radius = 2.0f;
        b2FixtureDef sensor_fix;
        sensor_fix.shape = &sensor_shape;
        sensor_fix.isSensor = true;
        bomb->CreateFixture(&sensor_fix);
    }

    void explode(b2Body* bomb) {
        ExplosionQuery query;
        query.center = bomb->GetPosition();
        query.radius = 2.0f;
        query.power = 10.0f;
        b2AABB aabb;
        aabb.lowerBound = query.center - b2Vec2(query.radius, query.radius);
        aabb.upperBound = query.center + b2Vec2(query.radius, query.radius);
        world.QueryAABB(&query, aabb);
        query.apply_explosion();
        world.DestroyBody(bomb);
    }

    void handle_mouse_down(int x, int y) {
        b2Vec2 world_pos(to_m(x), to_m(y));

        if (current_tool == 3) {
            create_bomb(x, y);
            return;
        }

        // 查询点击的物体
        class PickQuery : public b2QueryCallback {
        public:
            b2Vec2 point;
            b2Body* picked;
            bool ReportFixture(b2Fixture* f) override {
                if (f->TestPoint(point)) {
                    picked = f->GetBody();
                    return false;
                }
                return true;
            }
        };
        PickQuery pq;
        pq.point = world_pos;
        pq.picked = nullptr;
        b2AABB aabb;
        aabb.lowerBound = world_pos - b2Vec2(0.1f, 0.1f);
        aabb.upperBound = world_pos + b2Vec2(0.1f, 0.1f);
        world.QueryAABB(&pq, aabb);

        if (pq.picked) {
            // 检查是否是炸弹
            if (pq.picked->GetUserData().pointer == 99) {
                explode(pq.picked);
                return;
            }
            // 创建鼠标关节
            b2MouseJointDef mj_def;
            mj_def.bodyA = ground;
            mj_def.bodyB = pq.picked;
            mj_def.target = world_pos;
            mj_def.maxForce = 1000.0f;
            mj_def.stiffness = 1000.0f;
            mj_def.damping = 50.0f;
            mouse_joint = (b2MouseJoint*)world.CreateJoint(&mj_def);
        } else {
            // 空白处点击：创建物体
            switch (current_tool) {
                case 0: create_box(x, y); break;
                case 1: create_ball(x, y); break;
                case 2: create_chain(x, y); break;
            }
        }
    }

    void handle_mouse_move(int x, int y) {
        if (mouse_joint) {
            mouse_joint->SetTarget(b2Vec2(to_m(x), to_m(y)));
        }
    }

    void handle_mouse_up() {
        if (mouse_joint) {
            world.DestroyJoint(mouse_joint);
            mouse_joint = nullptr;
        }
    }

    void step(float dt) {
        world.Step(dt, 8, 3);
    }

    void render() {
        SDL_SetRenderDrawColor(renderer, 20, 20, 30, 255);
        SDL_RenderClear(renderer);
        world.DebugDraw();
        SDL_RenderPresent(renderer);
    }
};

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Physics Sandbox",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, WINDOW_W, WINDOW_H, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, 0);

    PhysicsSandbox game(renderer);

    printf("操作说明:\n");
    printf("  1/2/3/4: 切换工具（箱子/球/链条/炸弹）\n");
    printf("  左键点击空白: 创建物体\n");
    printf("  左键拖拽物体: 移动物体\n");
    printf("  左键点击炸弹: 引爆\n");

    const float FIXED_DT = 1.0f / 60.0f;
    float accumulator = 0.0f;
    Uint64 last = SDL_GetPerformanceCounter();

    bool running = true;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) {
            if (e.type == SDL_QUIT) running = false;
            if (e.type == SDL_KEYDOWN) {
                switch (e.key.keysym.sym) {
                    case SDLK_1: game.current_tool = 0; printf("工具: 箱子\n"); break;
                    case SDLK_2: game.current_tool = 1; printf("工具: 球\n"); break;
                    case SDLK_3: game.current_tool = 2; printf("工具: 链条\n"); break;
                    case SDLK_4: game.current_tool = 3; printf("工具: 炸弹\n"); break;
                }
            }
            if (e.type == SDL_MOUSEBUTTONDOWN && e.button.button == SDL_BUTTON_LEFT) {
                game.handle_mouse_down(e.button.x, e.button.y);
            }
            if (e.type == SDL_MOUSEMOTION && game.mouse_joint) {
                game.handle_mouse_move(e.motion.x, e.motion.y);
            }
            if (e.type == SDL_MOUSEBUTTONUP && e.button.button == SDL_BUTTON_LEFT) {
                game.handle_mouse_up();
            }
        }

        Uint64 now = SDL_GetPerformanceCounter();
        float frame_time = (float)(now - last) / SDL_GetPerformanceFrequency();
        last = now;
        if (frame_time > 0.25f) frame_time = 0.25f;
        accumulator += frame_time;

        while (accumulator >= FIXED_DT) {
            game.step(FIXED_DT);
            accumulator -= FIXED_DT;
        }

        game.render();
    }

    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- 物理沙盒整合了世界创建、多种形状、关节、碰撞回调、传感器、CCD、调试绘制。
- **工具系统**：用按键切换工具，鼠标点击创建对应物体。
- **鼠标拖拽**：AABB 查询找到点击物体，创建鼠标关节拖拽。
- **爆炸效果**：传感器触发后，AABB 查询范围内物体，施加远离中心的冲量。
- **架构建议**：将游戏逻辑封装在类中，分离物理、渲染、输入；用对象池管理动态物体。
- **扩展方向**：添加保存/加载功能、更多工具（弹簧、绳索）、粒子特效、音效。

---

## 课程结语

恭喜你完成了《Box2D 物理引擎》的全部 28 讲！让我们回顾这段旅程：

**第 1 章 基础入门**：搭建 Box2D 环境，理解 World、Body、Fixture、Shape 四大核心对象，掌握三种刚体类型与夹具的材质属性。

**第 2 章 形状与材质**：深入圆形、多边形、边缘、链四种形状的创建，掌握密度、摩擦、弹性三大材质参数的调参技巧。

**第 3 章 力与运动**：学习施加力与冲量、直接控制速度、阻尼与休眠机制、重力缩放与浮力模拟。

**第 4 章 碰撞系统**：理解接触流形、使用碰撞回调、创建传感器触发区域、自定义接触过滤规则。

**第 5 章 关节约束**：掌握距离、旋转、棱柱、滑轮、齿轮、焊接、鼠标七种关节，构建各种机械装置。

**第 6 章 高级特性**：使用 CCD 防穿透、射线投射做视线检测、AABB 查询做范围伤害、调优求解器参数。

**第 7 章 实战集成**：将 Box2D 集成到 SDL2、实现调试绘制、完成物理沙盒游戏案例。

**下一步学习建议**：

1. **深入源码**：阅读 Box2D 源码理解约束求解器、动态树、连续碰撞的内部实现。
2. **进阶主题**：软体物理（Verlet 积分）、流体模拟（SPH）、布料模拟。
3. **3D 扩展**：学习 Bullet、PhysX 等 3D 物理引擎，探索 3D 碰撞与约束。
4. **性能极致**：学习数据导向设计（DOD）、SIMD 向量化、多线程物理。
5. **实战项目**：用 Box2D 实现一个完整的平台跳跃游戏或物理解谜游戏。

Box2D 是 2D 物理引擎的黄金标准，掌握它不仅让你能做出物理真实的游戏，更让你理解物理引擎的内部机制——这种理解在你学习任何其他物理引擎时都会受益匪浅。祝你编码愉快，物理模拟稳定 60 FPS！
