# ECS 实体组件系统 - 从入门到精通

> 一本系统讲解 ECS（Entity-Component-System）架构的教科书
> 从基础概念到高级实战，25 讲带你彻底掌握数据导向设计

---

## 课程总览

### 学习目标

完成本课程后，你将能够：

1. **理解 ECS 的核心思想**：掌握实体、组件、系统三大要素的本质，理解数据导向设计（DOD）与传统面向对象编程（OOP）的根本差异。
2. **掌握 ECS 的实现原理**：深入理解 Archetype 架构、Chunk 内存管理、EntityQuery 查询机制等底层实现。
3. **具备性能优化能力**：理解缓存局部性、SIMD、Burst 编译、多线程 Job System 等性能优化手段。
4. **能够实战应用**：在 Unity DOTS 或自研框架中应用 ECS 架构，构建高性能游戏与应用。

### 课程结构

本课程共 **7 章 25 讲**，遵循"基础 → 核心 → 原理 → 优化 → 进阶 → 实战"的渐进结构：

| 章节 | 主题 | 讲数 | 核心目标 |
|------|------|------|----------|
| 第一章 | ECS 基础认知 | 4 讲 | 建立 ECS 整体认知 |
| 第二章 | 核心概念解析 | 4 讲 | 深入理解三大要素 |
| 第三章 | 内存与存储原理 | 4 讲 | 掌握底层实现机制 |
| 第四章 | 数据流与生命周期 | 3 讲 | 理解运行时行为 |
| 第五章 | 性能优化 | 4 讲 | 掌握高性能编程 |
| 第六章 | 进阶主题 | 3 讲 | 拓展架构边界 |
| 第七章 | 实战应用 | 3 讲 | 真实项目落地 |

### 适合读者

- 游戏开发者（尤其是 Unity、Unreal 用户）
- 对高性能架构感兴趣的后端工程师
- 希望突破 OOP 思维局限的程序员
- 计算机科学专业学生与架构爱好者

### 前置知识

- 熟悉至少一门主流编程语言（C#、C++、Java 等）
- 了解面向对象编程的基本概念
- 对内存布局、数据结构有基础认识

---

## 详细章节目录

### 第一章：ECS 基础认知

- 第 1 讲：什么是 ECS —— 起源、定义与发展
- 第 2 讲：ECS vs 传统 OOP —— 范式的根本对比
- 第 3 讲：ECS 核心三要素总览
- 第 4 讲：ECS 的适用场景与优缺点

### 第二章：核心概念解析

- 第 5 讲：实体（Entity）—— ID 即一切
- 第 6 讲：组件（Component）—— 纯数据载体
- 第 7 讲：系统（System）—— 纯逻辑处理器
- 第 8 讲：World 与 EntityManager —— 全局协调者

### 第三章：内存与存储原理

- 第 9 讲：数据导向设计（DOD）基础
- 第 10 讲：Archetype（原型）架构
- 第 11 讲：Chunk（块）内存管理
- 第 12 讲：EntityQuery（实体查询）

### 第四章：数据流与生命周期

- 第 13 讲：实体与组件的生命周期
- 第 14 讲：系统的执行顺序与依赖
- 第 15 讲：生命周期回调与事件机制

### 第五章：性能优化

- 第 16 讲：缓存局部性原理
- 第 17 讲：SIMD 与 Burst 编译
- 第 18 讲：多线程与 Job System
- 第 19 讲：内存碎片与 GC 规避

### 第六章：进阶主题

- 第 20 讲：共享组件与单例
- 第 21 讲：系统分组与标签
- 第 22 讲：序列化与存档

### 第七章：实战应用

- 第 23 讲：Unity DOTS 实战入门
- 第 24 讲：ECS 游戏案例剖析
- 第 25 讲：常见陷阱与最佳实践

---

# 第一章：ECS 基础认知

> 本章目标：建立对 ECS 的整体认知，理解它"是什么"、"为什么出现"、"解决什么问题"。

---

## 第 1 讲：什么是 ECS —— 起源、定义与发展

### 概念

**ECS（Entity-Component-System，实体-组件-系统）** 是一种主要用于游戏开发和高性能应用的软件架构模式。它将程序的数据与逻辑彻底分离：用"实体"标识对象，用"组件"存储数据，用"系统"处理逻辑。

ECS 的核心思想可以概括为一句话：**"组合优于继承，数据与逻辑分离"**。

ECS 最早源于游戏开发社区对传统面向对象继承体系的不满。2007 年左右，Adam Martin 等人在博客中系统阐述了 ECS 思想，随后被《守望先锋》《Unity DOTS》《Bevy》等知名项目广泛采用，成为现代游戏引擎的主流架构之一。

### 原理

ECS 的出现是为了解决传统 OOP 架构在游戏开发中遇到的几个核心痛点：

**痛点一：继承地狱（Diamond Problem）**

在 OOP 中，我们倾向于用继承树来组织对象。例如：

```
GameObject
├── Character
│   ├── Player
│   └── Enemy
├── Item
│   ├── Weapon
│   └── Potion
└── Projectile
```

当需求增长，会出现"会飞的敌人"、"会游泳的玩家"、"可拾取的武器"等组合需求，继承树会变得极度复杂，甚至出现菱形继承（一个类同时继承两个有共同父类的类）。

**痛点二：数据与逻辑耦合**

OOP 将数据和方法放在同一个类中。当我们只需要遍历所有"有位置"的对象做碰撞检测时，却不得不加载整个对象的所有方法和数据，导致缓存命中率极低。

**痛点三：难以为继的扩展**

新增一个功能（如"可燃烧"），在 OOP 中要么修改基类（影响所有子类），要么新建子类（组合爆炸）。ECS 中只需新增一个 `Flammable` 组件和一个 `BurningSystem`，零侵入。

ECS 通过**组合（Composition）** 替代继承，通过**数据导向设计（DOD）** 替代对象导向设计，从根本上解决了这些问题。

### 例子

一个最简化的 ECS 伪代码示例：

```csharp
// === 组件：纯数据，无逻辑 ===
struct Position { public float x, y, z; }
struct Velocity { public float x, y, z; }

// === 实体：仅是一个 ID ===
using Entity = int;  // 实体就是一个整数 ID

// === 系统：纯逻辑，处理拥有特定组件的实体 ===
class MovementSystem {
    void Update(World world) {
        foreach (var entity in world.Query<Position, Velocity>()) {
            ref var pos = ref world.Get<Position>(entity);
            var vel = world.Get<Velocity>(entity);
            pos.x += vel.x * deltaTime;
            pos.y += vel.y * deltaTime;
            pos.z += vel.z * deltaTime;
        }
    }
}
```

注意三个关键点：
1. `Entity` 只是一个 ID，不包含任何数据或方法
2. `Position` 和 `Velocity` 是纯数据结构，没有任何方法
3. `MovementSystem` 只关心"同时拥有 Position 和 Velocity 的实体"，不关心实体具体是什么

这种解耦带来了极大的灵活性：一个子弹、一个玩家、一个怪物，只要它们有 `Position` 和 `Velocity`，`MovementSystem` 就会统一处理它们。

### 总结

**核心要点：**
- ECS 是一种数据与逻辑分离的架构模式，由实体（ID）、组件（数据）、系统（逻辑）三部分组成
- ECS 起源于游戏开发，解决 OOP 的继承地狱、数据耦合、扩展困难三大痛点
- ECS 的核心思想是"组合优于继承，数据与逻辑分离"

**常见注意事项：**
- 不要把 ECS 理解为"另一种 OOP"，它是完全不同的范式
- ECS 不是银弹，它牺牲了封装性换取了性能和灵活性
- 学习 ECS 最大的障碍是思维转换：从"对象有什么行为"转向"数据如何流动"

---

## 第 2 讲：ECS vs 传统 OOP —— 范式的根本对比

### 概念

**OOP（面向对象编程）** 将世界建模为"对象"——每个对象封装了数据和行为，对象之间通过方法调用交互。

**ECS（实体组件系统）** 将世界建模为"数据流"——数据以组件形式存在，系统像流水线一样批量处理数据。

两者的根本差异在于：**OOP 以"对象"为中心组织代码，ECS 以"数据"为中心组织代码。**

### 原理

我们通过一个具体场景对比两种范式：**"让所有会移动的对象每帧更新位置"**。

**OOP 的做法：**

```csharp
abstract class GameObject {
    public abstract void Update(float dt);
}

class Player : GameObject {
    Vector3 position;
    Vector3 velocity;
    public override void Update(float dt) {
        position += velocity * dt;
        // ... 还有玩家专属逻辑
    }
}

class Enemy : GameObject {
    Vector3 position;
    Vector3 velocity;
    public override void Update(float dt) {
        position += velocity * dt;
        // ... 还有敌人专属逻辑
    }
}

// 调用
foreach (var obj in allGameObjects) {
    obj.Update(dt);  // 多态调用，虚函数表查找
}
```

问题：
1. `Player` 和 `Enemy` 都重复了 `position += velocity * dt` 的代码
2. 每个对象在堆上独立分配，内存不连续
3. 虚函数调用破坏 CPU 分支预测
4. 遍历时缓存命中率低（对象散落在堆各处）

**ECS 的做法：**

```csharp
struct Position { public float x, y, z; }
struct Velocity { public float x, y, z; }

class MovementSystem {
    void Update(float dt) {
        var positions = world.GetComponentDataArray<Position>();
        var velocities = world.GetComponentDataArray<Velocity>();
        for (int i = 0; i < positions.Length; i++) {
            positions[i].x += velocities[i].x * dt;
            positions[i].y += velocities[i].y * dt;
            positions[i].z += velocities[i].z * dt;
        }
    }
}
```

优势：
1. 移动逻辑只写一次，所有有 Position+Velocity 的实体共享
2. 组件数据在内存中连续存储（SoA 布局）
3. 无虚函数调用，直接数组遍历
4. 缓存命中率极高，CPU 预取生效

**内存布局对比：**

```
OOP (AoS - Array of Structs):
[Player1{pos,vel,hp,...}][Enemy1{pos,vel,ai,...}][Player2{pos,vel,hp,...}]
 ↑ 不连续字段，遍历 position 时会加载大量无关数据

ECS (SoA - Struct of Arrays):
[Position1][Position2][Position3]...  ← 连续
[Velocity1][Velocity2][Velocity3]...  ← 连续
 ↑ 只遍历 Position 时，CPU 缓存行完美利用
```

### 例子

用一个"游戏角色"的例子完整对比：

**OOP 实现：**

```csharp
class GameCharacter {
    string name;
    Vector3 position;
    float health;
    float mana;
    Inventory inventory;
    List<Buff> buffs;
    
    virtual void Update(float dt) { /* ... */ }
    virtual void TakeDamage(float amount) { /* ... */ }
    virtual void Die() { /* ... */ }
}

class Warrior : GameCharacter { /* ... */ }
class Mage : GameCharacter { /* ... */ }
class Archer : GameCharacter { /* ... */ }
```

如果要新增一个"会飞、能施法、有背包"的怪物，需要多重继承或大量接口，代码复杂度爆炸。

**ECS 实现：**

```csharp
// 组件
struct Name { string value; }
struct Position { float x, y, z; }
struct Health { float current, max; }
struct Mana { float current, max; }
struct Inventory { Item[] items; }
struct Flyable { float altitude; }
struct SpellCaster { Spell[] knownSpells; }

// 实体只是 ID
Entity warrior = world.CreateEntity();
world.AddComponent(warrior, new Name{value="战士"});
world.AddComponent(warrior, new Position{0,0,0});
world.AddComponent(warrior, new Health{100,100});

Entity flyingMage = world.CreateEntity();
world.AddComponent(flyingMage, new Name{value="飞行法师"});
world.AddComponent(flyingMage, new Position{0,10,0});
world.AddComponent(flyingMage, new Mana{50,50});
world.AddComponent(flyingMage, new Flyable{10});
world.AddComponent(flyingMage, new SpellCaster{...});
```

新增"飞行法师"无需任何继承，只是组件的组合。系统按需查询：飞行系统只查有 `Flyable` 的实体，施法系统只查有 `SpellCaster` 的实体，互不干扰。

### 总结

**核心要点：**

| 维度 | OOP | ECS |
|------|-----|-----|
| 组织中心 | 对象 | 数据 |
| 代码复用 | 继承 | 组合 |
| 内存布局 | AoS（散乱） | SoA（连续） |
| 多态机制 | 虚函数表 | 数据查询 |
| 缓存友好性 | 差 | 极好 |
| 扩展性 | 受继承树限制 | 自由组合 |
| 封装性 | 强 | 弱（数据公开） |

**常见注意事项：**
- ECS 牺牲了封装性，组件数据通常是公开的，这是为了性能做出的权衡
- 不要在 ECS 中强行套用 OOP 的设计模式（如单例、工厂），它们往往不适用
- ECS 的学习曲线主要在思维转换，而非语法

---

## 第 3 讲：ECS 核心三要素总览

### 概念

ECS 由三个核心要素构成，它们各司其职、紧密协作：

1. **实体（Entity）**：一个唯一的标识符（通常是一个整数 ID），本身不包含任何数据或逻辑。它代表"存在"这个概念。
2. **组件（Component）**：纯数据结构，只包含字段，不包含方法。每个组件代表对象的一个"方面"或"属性"。
3. **系统（System）**：纯逻辑处理器，负责查询拥有特定组件集合的实体，并对它们的数据进行操作。

三者关系：**实体是组件的容器，组件是数据的载体，系统是数据的处理器。**

### 原理

ECS 三要素的设计遵循三个基本原则：

**原则一：实体极简**

实体只是一个 ID，没有任何数据。这意味着：
- 创建实体极快（只需分配一个 ID）
- 销毁实体极快（只需回收 ID）
- 实体本身不占用额外内存（4 字节或 8 字节）

**原则二：组件极纯**

组件只包含数据，没有任何方法。这是为了：
- 数据可以连续存储在内存中
- 系统可以批量访问同类数据
- 编译器能更好地优化（如 SIMD 向量化）

**原则三：系统极专**

每个系统只做一件事，只处理它关心的组件组合。这是为了：
- 单一职责原则
- 易于并行化（不同系统可并行）
- 易于理解和维护

**数据流模型：**

```
[组件数据池]  →  [系统A: 处理组件1,2]  →  [更新后的组件数据]
                  [系统B: 处理组件2,3]  →  [更新后的组件数据]
                  [系统C: 处理组件1,3]  →  [更新后的组件数据]
```

系统像流水线上的工位，数据像流水线上的产品，每个工位只负责一道工序。

### 例子

用一个"简单的弹幕游戏"展示三要素如何协作：

```csharp
// ========== 组件定义 ==========
struct Position { public float x, y; }
struct Velocity { public float x, y; }
struct Sprite { public int textureId; public float size; }
struct Lifetime { public float remaining; }
struct Damage { public int amount; }
struct Health { public int current, max; }
struct PlayerTag { }  // 标签组件，无数据，用于标记"这是玩家"

// ========== 实体创建 ==========
// 创建玩家
Entity player = world.CreateEntity();
world.AddComponent(player, new Position { x = 400, y = 300 });
world.AddComponent(player, new Sprite { textureId = 1, size = 32 });
world.AddComponent(player, new Health { current = 100, max = 100 });
world.AddComponent(player, new PlayerTag { });

// 创建子弹
Entity bullet = world.CreateEntity();
world.AddComponent(bullet, new Position { x = 400, y = 300 });
world.AddComponent(bullet, new Velocity { x = 0, y = 10 });
world.AddComponent(bullet, new Sprite { textureId = 2, size = 8 });
world.AddComponent(bullet, new Lifetime { remaining = 3.0f });
world.AddComponent(bullet, new Damage { amount = 10 });

// ========== 系统定义 ==========
class MovementSystem : SystemBase {
    protected override void OnUpdate(float dt) {
        // 只处理有 Position 和 Velocity 的实体
        Entities.ForEach((ref Position pos, in Velocity vel) => {
            pos.x += vel.x * dt;
            pos.y += vel.y * dt;
        }).Schedule();
    }
}

class LifetimeSystem : SystemBase {
    protected override void OnUpdate(float dt) {
        // 只处理有 Lifetime 的实体
        Entities.ForEach((Entity e, ref Lifetime life) => {
            life.remaining -= dt;
            if (life.remaining <= 0) {
                EntityManager.DestroyEntity(e);
            }
        }).Schedule();
    }
}

class RenderSystem : SystemBase {
    protected override void OnUpdate(float dt) {
        // 只处理有 Position 和 Sprite 的实体
        Entities.ForEach((in Position pos, in Sprite sprite) => {
            DrawSprite(sprite.textureId, pos.x, pos.y, sprite.size);
        }).Run();
    }
}
```

观察这个例子：
- 玩家和子弹都是"实体"，但它们的组件组合不同
- `MovementSystem` 不会处理玩家（玩家没有 Velocity），只处理子弹
- `RenderSystem` 同时处理玩家和子弹（它们都有 Position 和 Sprite）
- 新增一种"激光"实体，只要有 Position+Sprite，就能自动被渲染，无需修改 RenderSystem

### 总结

**核心要点：**
- 实体 = ID（标识存在）
- 组件 = 数据（描述属性）
- 系统 = 逻辑（处理数据）
- 三者通过"组件组合查询"松耦合协作

**记忆口诀：**
> 实体是"谁"，组件是"什么"，系统是"做什么"。

**常见注意事项：**
- 不要在组件里写方法（即使是属性 getter/setter 也尽量避免）
- 不要在系统里存储状态（系统应该是无状态的，状态都属于组件）
- 不要让实体"知道"自己有哪些组件（实体只是 ID，它什么都不知道）

---

## 第 4 讲：ECS 的适用场景与优缺点

### 概念

ECS 不是银弹，它有明确的适用场景和不适用场景。理解这些边界，才能在正确的场合使用正确的工具。

**适合 ECS 的场景：**
- 大量同质对象的游戏（弹幕、RTS、模拟类）
- 对性能要求极高的实时应用
- 需要灵活组合行为的系统
- 数据密集型计算

**不适合 ECS 的场景：**
- UI 框架（UI 是高度层次化的，OOP 更合适）
- 业务逻辑复杂的 CRUD 系统（ECS 的优势发挥不出来）
- 小型项目（ECS 的架构成本可能超过收益）
- 高度封装的库（ECS 牺牲封装性）

### 原理

ECS 的优势和劣势都源于其"数据导向"的本质设计：

**优势的根源：数据连续存储**

CPU 缓存的工作原理决定了：连续内存访问比随机访问快 10-100 倍。现代 CPU 缓存行通常是 64 字节，一次加载 64 字节几乎和加载 1 字节一样快。ECS 的 SoA 布局让同类数据连续存储，遍历时缓存命中率接近 100%。

**优势的根源：组合替代继承**

新增功能只需新增组件+系统，不修改现有代码，符合开闭原则。例如给敌人加"燃烧"效果，只需：

```csharp
struct Burning { public float duration; }
class BurningSystem : SystemBase { /* ... */ }
```

无需修改 Enemy 类，无需修改任何现有系统。

**劣势的根源：封装性丧失**

组件数据是公开的，任何系统都能读写，导致：
- 难以追踪"谁修改了这个数据"
- 调试时数据状态不直观
- 代码可读性下降（逻辑分散在多个系统中）

**劣势的根源：架构复杂度**

ECS 需要框架支持（World、EntityManager、SystemScheduler 等），对小型项目是过度设计。学习曲线也较陡峭。

### 例子

**适合 ECS 的典型案例：RTS 游戏**

一个 RTS 游戏可能有 1000+ 单位，每个单位有位置、血量、攻击力、AI 状态等。用 OOP 会导致：
- 1000 个对象散落在堆上，缓存不友好
- 每帧遍历 1000 个对象的 Update 方法，虚函数开销大
- 新增"飞行单位"需要修改继承树

用 ECS：
- 1000 个单位的 Position 数据连续存储，遍历极快
- 系统批量处理，无虚函数
- 新增"飞行单位"只需加 Flyable 组件

```csharp
// 1000 个单位的位置更新，连续内存遍历
class MovementSystem : SystemBase {
    protected override void OnUpdate(float dt) {
        // 底层是连续数组，CPU 缓存完美利用
        Entities.ForEach((ref Position pos, in Velocity vel, in PathTarget target) => {
            // 寻路 + 移动逻辑
        }).ScheduleParallel();  // 自动多线程
    }
}
```

**不适合 ECS 的典型案例：表单 UI**

一个表单有输入框、按钮、下拉框，它们有严格的父子关系和事件冒泡机制。用 ECS 强行实现会很别扭：

```csharp
// 别扭的 ECS UI 实现
struct Button { string text; }
struct OnClick { Action callback; }
struct Parent { Entity parentEntity; }
struct Child { Entity childEntity; }

class ButtonClickSystem : SystemBase {
    // 处理点击事件冒泡？需要查询 Parent 链...
    // 处理焦点切换？需要全局状态...
    // 远不如 OOP 的 Control 基类 + 事件冒泡直观
}
```

这种场景下，传统的 OOP UI 框架（如 WPF、Qt）更合适。

### 总结

**核心要点：**

| 优势 | 劣势 |
|------|------|
| 高性能（缓存友好） | 封装性差 |
| 高扩展性（组合） | 学习曲线陡 |
| 易并行化 | 调试困难 |
| 数据密集型场景极佳 | 架构复杂度高 |
| 代码复用性好 | 不适合层次化结构 |

**决策建议：**
- 对象数量 > 1000 且需要每帧更新 → 强烈推荐 ECS
- 需要灵活组合行为（如技能系统） → 推荐 ECS
- UI、表单、业务系统 → 不推荐 ECS
- 小型 Demo 或原型 → 视情况，可用简化版 ECS

**常见注意事项：**
- 不要为了用 ECS 而用 ECS，架构选择应服务于需求
- ECS 和 OOP 可以共存：游戏逻辑用 ECS，UI 用 OOP
- 评估 ECS 时，重点看"数据量"和"更新频率"两个指标

---

# 第二章：核心概念解析

> 本章目标：深入理解实体、组件、系统、World 四大核心概念的本质与实现细节。

---

## 第 5 讲：实体（Entity）—— ID 即一切

### 概念

在 ECS 中，**实体（Entity）就是一个唯一的标识符**，通常是一个整数 ID。它本身不包含任何数据，也不包含任何方法。实体的唯一职责是"标识一个游戏对象的存在"。

一个实体可以理解为：**"一组组件的集合的代号"**。当你给一个 ID 添加了 Position、Health、Sprite 组件，这个 ID 就成了一个"有位置、有血量、有图像的游戏对象"。

### 原理

**实体的本质：索引而非对象**

传统 OOP 中，"对象"是一个内存块，包含数据和指向方法的指针。ECS 中，"实体"只是一个索引，真正的数据存储在组件池中。

```
OOP 对象:
Player对象 → [数据1, 数据2, ..., 方法指针1, 方法指针2, ...]

ECS 实体:
Entity(42) → 索引 → [Position组件池][42] = {x:10, y:20}
                    → [Health组件池][42] = {current:100, max:100}
```

**实体的内部结构（典型实现）：**

```csharp
struct Entity {
    public int Index;      // 实体在数组中的索引
    public int Version;    // 版本号，用于检测实体是否已被销毁并回收
}
```

**为什么需要 Version（版本号）？**

当实体被销毁时，它的 Index 会被回收复用。如果不加版本号，可能出现"悬空引用"问题：

```
1. 创建实体 A，Index=5，Version=1
2. 某系统持有 A 的引用（Index=5, Version=1）
3. 销毁实体 A，Index=5 被回收
4. 创建实体 B，复用 Index=5，Version=2
5. 持有 A 引用的系统用 Index=5 查询，会错误地访问到 B！
```

加入 Version 后：
```
5. 持有 A 引用的系统用 (Index=5, Version=1) 查询
   当前 Index=5 的 Version=2，不匹配，识别为"已失效引用"
```

**实体的生命周期：**

```
[创建] → 分配 Index（新或回收）→ Version++ → [活跃]
                                                ↓
[销毁] ← 标记删除 ← [活跃] ← ← ← ← ← ← ← ← ←
   ↓
[回收] Index 回收池，Version++（防止旧引用误用）
```

### 例子

**最小化的实体实现：**

```csharp
public struct Entity : IEquatable<Entity> {
    public readonly int Index;
    public readonly int Version;
    
    public Entity(int index, int version) {
        Index = index;
        Version = version;
    }
    
    public bool Equals(Entity other) {
        return Index == other.Index && Version == other.Version;
    }
    
    public override int GetHashCode() => Index * 397 ^ Version;
}

public class EntityManager {
    private int[] versions;          // 每个 Index 的当前版本
    private Queue<int> recycledIndices;  // 回收的 Index
    private int nextIndex = 0;
    
    public Entity CreateEntity() {
        int index;
        if (recycledIndices.Count > 0) {
            index = recycledIndices.Dequeue();
        } else {
            index = nextIndex++;
            // 扩容 versions 数组
        }
        return new Entity(index, versions[index]);
    }
    
    public void DestroyEntity(Entity e) {
        versions[e.Index]++;  // 版本号 +1，使旧引用失效
        recycledIndices.Enqueue(e.Index);  // 回收 Index
    }
    
    public bool Exists(Entity e) {
        return e.Index < versions.Length && versions[e.Index] == e.Version;
    }
}
```

**使用示例：**

```csharp
var manager = new EntityManager();

Entity player = manager.CreateEntity();
Console.WriteLine($"Player: Index={player.Index}, Version={player.Version}");
// 输出: Player: Index=0, Version=0

Entity enemy = manager.CreateEntity();
Console.WriteLine($"Enemy: Index={enemy.Index}, Version={enemy.Version}");
// 输出: Enemy: Index=1, Version=0

manager.DestroyEntity(player);
Console.WriteLine($"Player exists? {manager.Exists(player)}");
// 输出: Player exists? False

Entity newItem = manager.CreateEntity();  // 复用 player 的 Index
Console.WriteLine($"NewItem: Index={newItem.Index}, Version={newItem.Version}");
// 输出: NewItem: Index=0, Version=1  ← Version 变了！

Console.WriteLine($"Old player ref still valid? {manager.Exists(player)}");
// 输出: Old player ref still valid? False  ← 旧引用失效
```

### 总结

**核心要点：**
- 实体 = (Index, Version) 二元组，本质是一个 ID
- Index 用于定位，Version 用于检测失效引用
- 实体本身不存储数据，数据在组件池中
- 实体的创建/销毁是 O(1) 操作

**常见注意事项：**
- 不要长期持有实体引用而不检查有效性（可能已被销毁）
- 不要用实体的 Index 作为唯一标识（Index 会被复用），必须用 (Index, Version)
- 实体销毁后，其引用的所有组件会被自动移除（由框架处理）

---

## 第 6 讲：组件（Component）—— 纯数据载体

### 概念

**组件（Component）是纯数据结构**，只包含字段，不包含任何方法（包括构造函数逻辑、属性 getter/setter）。组件代表实体的一个"方面"或"属性"。

例如：`Position` 组件代表"有位置"，`Health` 组件代表"有血量"，`Renderable` 组件代表"可渲染"。

组件的设计哲学是：**数据与逻辑彻底分离**。数据归组件，逻辑归系统。

### 原理

**组件的纯数据原则**

为什么组件不能有方法？三个原因：

1. **内存布局**：方法是代码，不占用对象内存。但如果组件有虚函数，会有虚函数表指针（vptr），增加 8 字节开销，破坏内存对齐。
2. **批量处理**：系统需要批量访问组件数据，如果组件有方法，编译器难以向量化优化。
3. **职责清晰**：组件只管"存什么"，系统管"怎么算"，职责分离便于维护。

**组件的分类：**

| 类型 | 说明 | 示例 |
|------|------|------|
| 数据组件 | 包含实际数据 | Position, Health, Velocity |
| 标签组件 | 无数据，仅用于标记 | PlayerTag, EnemyTag |
| 共享组件 | 多个实体共享同一实例 | Mesh, Material |
| 缓冲组件 | 动态长度数据 | Inventory, BuffList |

**组件的内存布局（SoA）：**

```
传统 OOP (AoS):
实体0: [Position][Health][Velocity][...]
实体1: [Position][Health][Velocity][...]
实体2: [Position][Health][Velocity][...]

ECS (SoA):
Position 池: [实体0的Pos][实体1的Pos][实体2的Pos]  ← 连续
Health 池:   [实体0的HP][实体1的HP][实体2的HP]    ← 连续
Velocity 池: [实体0的Vel][实体1的Vel][实体2的Vel] ← 连续
```

当系统只需要 Position 时，只遍历 Position 池，CPU 缓存完美利用。

**组件的存储方式（两种主流方案）：**

1. **稀疏集（Sparse Set）**：每个组件类型用一个稀疏集存储，添加/删除 O(1)，但遍历时可能有空洞。
2. **原型（Archetype）**：按组件组合分组存储，同组实体连续内存，遍历极快，但添加/删除组件需移动数据。

Unity DOTS 使用 Archetype 方案，Bevy 使用 Sparse Set 方案，各有优劣。

### 例子

**组件定义示例：**

```csharp
using Unity.Entities;
using Unity.Mathematics;

// === 数据组件 ===
public struct Position : IComponentData {
    public float3 Value;  // 使用 float3 而非 Vector3，便于 SIMD
}

public struct Velocity : IComponentData {
    public float3 Value;
}

public struct Health : IComponentData {
    public float Current;
    public float Max;
}

// === 标签组件（无数据）===
public struct PlayerTag : IComponentData { }
public struct EnemyTag : IComponentData { }
public struct DestroyedTag : IComponentData { }

// === 共享组件（多个实体共享同一实例）===
public struct MeshRenderer : ISharedComponentData {
    public Mesh Mesh;
    public Material Material;
}

// === 缓冲组件（动态长度）===
public struct InventoryBuffer : IBufferElementData {
    public int ItemId;
    public int Count;
}
```

**组件的使用：**

```csharp
// 创建实体并添加组件
Entity player = entityManager.CreateEntity();

entityManager.AddComponentData(player, new Position { Value = new float3(0, 0, 0) });
entityManager.AddComponentData(player, new Velocity { Value = new float3(1, 0, 0) });
entityManager.AddComponentData(player, new Health { Current = 100, Max = 100 });
entityManager.AddComponentData(player, new PlayerTag { });

// 读取组件
Position pos = entityManager.GetComponentData<Position>(player);

// 修改组件
entityManager.SetComponentData(player, new Position { Value = new float3(10, 0, 0) });

// 移除组件
entityManager.RemoveComponent<Velocity>(player);

// 动态缓冲区使用
DynamicBuffer<InventoryBuffer> inventory = entityManager.GetBuffer<InventoryBuffer>(player);
inventory.Add(new InventoryBuffer { ItemId = 1, Count = 5 });
inventory.Add(new InventoryBuffer { ItemId = 2, Count = 3 });
```

**组件设计的最佳实践：**

```csharp
// ✅ 好的组件设计：粒度合适，数据内聚
struct Health { float current; float max; }
struct MovementStats { float speed; float acceleration; }

// ❌ 坏的组件设计：粒度过粗，包含无关数据
struct CharacterData {
    float health, maxHealth;
    float speed, acceleration;
    float attack, defense;
    string name;
    // ... 30 个字段
}

// ❌ 坏的组件设计：包含方法
struct Health {
    float current, max;
    void TakeDamage(float amount) { current -= amount; }  // 不应该有方法！
}
```

### 总结

**核心要点：**
- 组件 = 纯数据结构，无方法
- 组件分四类：数据组件、标签组件、共享组件、缓冲组件
- 组件按 SoA 布局存储，同类数据连续
- 组件粒度应适中，过粗失去灵活性，过细增加查询开销

**常见注意事项：**
- 组件字段尽量使用值类型（struct），避免引用类型（class）
- 组件大小尽量是 4/8/16/32 字节的倍数，利于内存对齐
- 不要在组件中存储 Entity 引用以外的对象引用（会导致 GC 压力）
- 标签组件虽然无数据，但能极大简化查询（如"查找所有玩家"）

---

## 第 7 讲：系统（System）—— 纯逻辑处理器

### 概念

**系统（System）是纯逻辑处理器**，负责查询拥有特定组件集合的实体，并对它们的数据进行操作。系统不存储状态（状态都属于组件），只负责"如何处理数据"。

每个系统通常对应一个具体的功能领域，如 `MovementSystem`（移动）、`RenderSystem`（渲染）、`CollisionSystem`（碰撞）、`AISystem`（AI）。

系统的设计哲学是：**单一职责，批量处理**。

### 原理

**系统的核心机制：查询 + 遍历**

每个系统在更新时执行三步：
1. **查询**：通过 EntityQuery 找到所有拥有特定组件集合的实体
2. **遍历**：批量遍历这些实体的组件数据
3. **处理**：对数据进行读写操作

```csharp
class MovementSystem {
    void OnUpdate(float dt) {
        // 1. 查询：找到所有有 Position 和 Velocity 的实体
        var query = world.Query<Position, Velocity>();
        
        // 2. 遍历 + 3. 处理
        foreach (var (pos, vel) in query) {
            pos.Value += vel.Value * dt;
        }
    }
}
```

**系统的分类：**

| 类型 | 触发方式 | 典型用途 |
|------|----------|----------|
| 初始化系统 | 启动时执行一次 | 初始化数据 |
| 更新系统 | 每帧执行 | 移动、AI、碰撞 |
| 固定更新系统 | 固定时间步长 | 物理 |
| 晚更新系统 | 每帧末尾 | 相机跟随 |
| 响应式系统 | 事件触发 | 处理伤害事件 |

**系统的执行模型：**

```
每帧执行流程:
[ComponentSystemGroup]
  ├─ [System A] - 查询+遍历+处理
  ├─ [System B] - 查询+遍历+处理
  ├─ [System C] - 查询+遍历+处理
  └─ [System D] - 查询+遍历+处理

每个系统内部:
[查询组件] → [获取数据数组] → [for循环处理] → [写回数据]
```

**系统的并行化：**

不同系统之间可能有数据依赖，但同一系统内的不同实体通常可以并行处理。ECS 框架通常提供并行调度：

```csharp
// 串行版本
foreach (var entity in query) {
    ProcessEntity(entity);
}

// 并行版本（自动多线程）
query.ScheduleParallel((Entity entity, ref Position pos) => {
    ProcessEntity(entity, ref pos);
});
```

### 例子

**Unity DOTS 中的系统示例：**

```csharp
using Unity.Entities;
using Unity.Mathematics;
using Unity.Transforms;
using Unity.Burst;

// === 移动系统 ===
[BurstCompile]  // 启用 Burst 编译，生成 SIMD 优化代码
public partial struct MovementSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        float dt = SystemAPI.Time.DeltaTime;
        
        // 遍历所有有 LocalTransform 和 Velocity 的实体
        foreach (var (transform, velocity) in 
                 SystemAPI.Query<RefRW<LocalTransform>, RefRO<Velocity>>()) {
            transform.ValueRW.Position += velocity.ValueRO.Value * dt;
        }
    }
}

// === 生命周期系统（销毁过期实体）===
public partial struct LifetimeSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        float dt = SystemAPI.Time.DeltaTime;
        var ecb = new EntityCommandBuffer(Allocator.Temp);
        
        foreach (var (lifetime, entity) in 
                 SystemAPI.Query<RefRW<Lifetime>>().WithEntityAccess()) {
            lifetime.ValueRW.Remaining -= dt;
            if (lifetime.ValueRO.Remaining <= 0) {
                ecb.DestroyEntity(entity);
            }
        }
        
        ecb.Playback(state.EntityManager);
    }
}

// === 碰撞检测系统（并行）===
[BurstCompile]
public partial struct CollisionSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        // 使用 Job 并行处理
        var job = new CollisionJob {
            // ... 参数
        };
        job.ScheduleParallel();
    }
}

[BurstCompile]
public partial struct CollisionJob : IJobEntity {
    void Execute(Entity entity, ref Health health, in Position pos, in Damage damage) {
        // 每个实体并行执行
        health.Current -= damage.Amount;
    }
}
```

**Bevy（Rust ECS）中的系统示例：**

```rust
use bevy_ecs::prelude::*;

// 组件
#[derive(Component)]
struct Position { x: f32, y: f32 }

#[derive(Component)]
struct Velocity { x: f32, y: f32 }

// 系统
fn movement_system(mut query: Query<(&mut Position, &Velocity)>, time: Res<Time>) {
    for (mut pos, vel) in query.iter_mut() {
        pos.x += vel.x * time.delta_seconds();
        pos.y += vel.y * time.delta_seconds();
    }
}

// 注册系统
fn main() {
    App::new()
        .add_systems(Update, movement_system)
        .run();
}
```

**系统设计的最佳实践：**

```csharp
// ✅ 好的系统设计：单一职责
class MovementSystem { /* 只处理移动 */ }
class CollisionSystem { /* 只处理碰撞 */ }
class DamageSystem { /* 只处理伤害 */ }

// ❌ 坏的系统设计：职责混乱
class GameLogicSystem {
    void Update() {
        MoveEntities();
        CheckCollisions();
        ApplyDamage();
        UpdateUI();
        PlaySounds();
    }
}
```

### 总结

**核心要点：**
- 系统 = 纯逻辑，无状态
- 系统核心机制：查询 + 遍历 + 处理
- 系统分类：初始化、更新、固定更新、晚更新、响应式
- 系统应遵循单一职责原则

**常见注意事项：**
- 不要在系统中存储状态（如 `private float someValue;`），状态应放在组件中
- 系统之间的执行顺序需要显式指定（不能依赖隐式顺序）
- 避免在系统中频繁创建/销毁实体，使用 EntityCommandBuffer 批量处理
- 系统应尽量设计为可并行（无跨实体数据依赖）

---

## 第 8 讲：World 与 EntityManager —— 全局协调者

### 概念

**World（世界）** 是 ECS 的顶层容器，管理所有实体、组件和系统。一个 World 相当于一个独立的"游戏场景"或"模拟空间"。

**EntityManager（实体管理器）** 是 World 的子模块，专门负责实体的创建、销毁和组件的增删改查。

World 和 EntityManager 共同构成了 ECS 的"运行时环境"，所有系统都在 World 中注册并按调度顺序执行。

### 原理

**World 的职责：**

1. **实体管理**：通过 EntityManager 管理所有实体的生命周期
2. **组件存储**：管理所有组件类型的数据池
3. **系统调度**：注册、排序、执行所有系统
4. **全局资源**：存储全局共享的数据（如时间、配置）

**World 的结构：**

```
World
├── EntityManager
│   ├── 实体索引表（Entity → 组件位置）
│   ├── 组件数据池（每种组件一个池）
│   ├── Archetype 表（按组件组合分组）
│   └── 实体回收池
├── SystemContainer
│   ├── 初始化系统组
│   ├── 更新系统组
│   │   ├── MovementSystem
│   │   ├── CollisionSystem
│   │   └── RenderSystem
│   └── 固定更新系统组
└── GlobalResources
    ├── Time
    ├── Input
    └── Config
```

**多 World 设计：**

ECS 允许同时存在多个 World，用于不同目的：
- **主 World**：游戏主逻辑
- **子 World**：独立模拟（如物理世界、AI 世界）
- **渲染 World**：仅用于渲染的数据
- **预制体 World**：存储预制体模板

```csharp
World gameWorld = new World("GameWorld");
World physicsWorld = new World("PhysicsWorld");

// 两个 World 独立运行，互不干扰
gameWorld.Update(dt);
physicsWorld.Update(dt);
```

**EntityManager 的核心 API：**

```csharp
// 实体生命周期
Entity CreateEntity();
void DestroyEntity(Entity e);
bool Exists(Entity e);

// 组件管理
void AddComponent<T>(Entity e);
void RemoveComponent<T>(Entity e);
T GetComponentData<T>(Entity e);
void SetComponentData<T>(Entity e, T data);
bool HasComponent<T>(Entity e);

// 批量操作
Entity[] CreateEntity(EntityArchetype archetype, int count);
void DestroyEntity(EntityQuery query);

// 查询
EntityQuery CreateEntityQuery(params ComponentType[] components);
```

### 例子

**Unity DOTS 中的 World 使用：**

```csharp
using Unity.Entities;

public class GameBootstrap {
    public static World GameWorld;
    
    [RuntimeInitializeOnLoadMethod(RuntimeInitializeLoadType.AfterSceneLoad)]
    public static void Initialize() {
        // 创建主 World
        GameWorld = new World("GameWorld");
        
        // 注册系统
        GameWorld.GetOrCreateSystem<MovementSystem>();
        GameWorld.GetOrCreateSystem<CollisionSystem>();
        GameWorld.GetOrCreateSystem<RenderSystem>();
        
        // 创建系统组并排序
        var simulationGroup = GameWorld.GetOrCreateSystem<SimulationSystemGroup>();
        simulationGroup.AddSystemToUpdateList(GameWorld.GetOrCreateSystem<MovementSystem>());
        simulationGroup.AddSystemToUpdateList(GameWorld.GetOrCreateSystem<CollisionSystem>());
        
        // 设置 World 为活跃 World
        World.DefaultGameObjectInjectionWorld = GameWorld;
    }
}

public class GameLoop : MonoBehaviour {
    void Update() {
        // 每帧推进 World
        GameBootstrap.GameWorld.Update();
    }
}
```

**EntityManager 操作示例：**

```csharp
var em = GameWorld.EntityManager;

// === 创建实体 ===
// 方式1：空实体，逐个添加组件
Entity player = em.CreateEntity();
em.AddComponentData(player, new Position { Value = float3.zero });
em.AddComponentData(player, new Health { Current = 100, Max = 100 });

// 方式2：使用 Archetype 批量创建（更高效）
EntityArchetype enemyArchetype = em.CreateArchetype(
    typeof(Position),
    typeof(Health),
    typeof(Velocity),
    typeof(EnemyTag)
);

Entity enemy1 = em.CreateEntity(enemyArchetype);
Entity enemy2 = em.CreateEntity(enemyArchetype);

// 批量创建 1000 个敌人
NativeArray<Entity> enemies = em.CreateEntity(enemyArchetype, 1000, Allocator.Temp);
for (int i = 0; i < enemies.Length; i++) {
    em.SetComponentData(enemies[i], new Position { Value = new float3(i, 0, 0) });
}
enemies.Dispose();

// === 查询实体 ===
EntityQuery enemyQuery = em.CreateEntityQuery(
    typeof(EnemyTag),
    typeof(Position),
    typeof(Health)
);
int enemyCount = enemyQuery.CalculateEntityCount();
Console.WriteLine($"敌人数量: {enemyCount}");

// === 批量销毁 ===
EntityQuery deadEnemies = em.CreateEntityQuery(
    typeof(EnemyTag),
    typeof(DestroyedTag)
);
em.DestroyEntity(deadEnemies);

// === 检查实体状态 ===
if (em.Exists(player) && em.HasComponent<Health>(player)) {
    Health hp = em.GetComponentData<Health>(player);
    Console.WriteLine($"玩家血量: {hp.Current}/{hp.Max}");
}
```

**Bevy 中的 World 使用：**

```rust
use bevy_ecs::prelude::*;

fn main() {
    // 创建 World
    let mut world = World::new();
    
    // 创建实体
    let player = world.spawn((
        Position { x: 0.0, y: 0.0 },
        Health { current: 100, max: 100 },
        PlayerTag,
    )).id();
    
    // 查询实体
    let mut query = world.query::<(&Position, &Health)>();
    for (pos, hp) in query.iter(&world) {
        println!("位置: ({}, {}), 血量: {}/{}", pos.x, pos.y, hp.current, hp.max);
    }
    
    // 创建 App（封装了 World + Schedule）
    App::new()
        .add_systems(Update, (movement_system, collision_system).chain())
        .run();
}
```

### 总结

**核心要点：**
- World = ECS 顶层容器，管理实体、组件、系统
- EntityManager = World 的子模块，负责实体和组件的 CRUD
- 一个应用可以有多个 World，各自独立运行
- 系统在 World 中注册，由 World 按调度顺序执行

**常见注意事项：**
- 不要在系统遍历过程中创建/销毁实体（会导致数据结构变动），使用 EntityCommandBuffer 延迟执行
- World 的初始化顺序很重要：先注册系统，再创建实体
- 多 World 之间数据同步需要显式处理（如复制实体数据）
- World 销毁时会自动清理所有实体和组件，无需手动释放

---

# 第三章：内存与存储原理

> 本章目标：深入 ECS 的底层实现，理解数据导向设计、Archetype 架构、Chunk 内存管理和 EntityQuery 查询机制。

---

## 第 9 讲：数据导向设计（DOD）基础

### 概念

**数据导向设计（Data-Oriented Design, DOD）** 是一种以数据为中心的编程范式，它关注"数据如何被处理"而非"对象如何行为"。DOD 是 ECS 的理论基础，ECS 是 DOD 在游戏开发中的具体实现。

DOD 的核心原则是：**先设计数据布局，再设计处理逻辑**。这与 OOP"先设计对象，再设计方法"的思路截然相反。

### 原理

**DOD 的四大原则：**

1. **数据布局优先**：根据数据的访问模式设计内存布局，而非根据业务概念组织对象。
2. **分离数据与逻辑**：数据是纯数据，逻辑是纯逻辑，二者不耦合。
3. **批量处理**：尽量批量处理同类数据，而非逐个处理对象。
4. **关注硬件友好性**：考虑 CPU 缓存、内存对齐、SIMD 等硬件特性。

**为什么 DOD 能大幅提升性能？**

关键在于理解 CPU 缓存的工作原理：

```
CPU 访问内存的延迟（近似值）:
- L1 缓存命中:     ~1 ns     (4 个时钟周期)
- L2 缓存命中:     ~4 ns     (12 个时钟周期)
- L3 缓存命中:     ~12 ns    (40 个时钟周期)
- 主内存访问:      ~100 ns   (300 个时钟周期)
```

主内存访问比 L1 缓存慢 100 倍！CPU 每次从内存读取数据时，会读取整个缓存行（通常 64 字节）。如果后续访问的数据在同一缓存行，就是"缓存命中"，几乎零延迟；如果不在，就是"缓存未命中"，需要等待 100ns。

**OOP 的缓存问题：**

```csharp
class Enemy {
    Vector3 position;    // 12 字节
    Vector3 velocity;    // 12 字节
    float health;        // 4 字节
    float attack;        // 4 字节
    string name;         // 8 字节（引用）
    AIState aiState;     // 4 字节
    // ... 更多字段
}
// 总大小可能 100+ 字节

Enemy[] enemies = new Enemy[1000];
foreach (var e in enemies) {
    e.position += e.velocity * dt;  // 只用 position 和 velocity
}
```

每个 Enemy 对象 100+ 字节，但我们只需要 24 字节（position + velocity）。CPU 读取一个 64 字节缓存行，只有 24 字节有用，其余 40 字节浪费。缓存利用率仅 37%。

**DOD 的解决方案：**

```csharp
struct Position { float x, y, z; }  // 12 字节
struct Velocity { float x, y, z; }  // 12 字节

// 数据分开存储
Position[] positions = new Position[1000];  // 12000 字节连续
Velocity[] velocities = new Velocity[1000]; // 12000 字节连续

for (int i = 0; i < 1000; i++) {
    positions[i] += velocities[i] * dt;
}
```

现在 positions 数组连续存储 1000 个 Position，每个 12 字节。CPU 读取一个 64 字节缓存行，包含 5 个 Position，全部有用。缓存利用率接近 100%。

**性能对比（实测近似值）：**

```
处理 100,000 个对象的位置更新:
- OOP (AoS, 每对象 100 字节): ~5 ms
- DOD (SoA, 分离数组):        ~0.5 ms  (10 倍提升)
```

### 例子

**一个完整的 DOD 重构示例：粒子系统**

**OOP 版本：**

```csharp
class Particle {
    public Vector3 position;
    public Vector3 velocity;
    public Color color;
    public float life;
    public float size;
    public bool active;
    public Texture texture;  // 引用类型
    // ... 总大小 ~80 字节
}

class ParticleSystemOOP {
    private List<Particle> particles = new List<Particle>(10000);
    
    public void Update(float dt) {
        foreach (var p in particles) {
            if (!p.active) continue;
            p.position += p.velocity * dt;
            p.life -= dt;
            if (p.life <= 0) p.active = false;
        }
    }
}
```

问题：每个 Particle 80 字节，但 Update 只用 position(12) + velocity(12) + life(4) + active(1) = 29 字节，缓存利用率 36%。

**DOD 版本：**

```csharp
struct ParticleData {
    public Vector3[] positions;   // 连续
    public Vector3[] velocities;  // 连续
    public Color[] colors;        // 连续
    public float[] lives;         // 连续
    public float[] sizes;         // 连续
    public bool[] actives;        // 连续
    public int count;
}

class ParticleSystemDOD {
    private ParticleData data;
    
    public void Update(float dt) {
        // 只加载 positions, velocities, lives, actives
        var positions = data.positions;
        var velocities = data.velocities;
        var lives = data.lives;
        var actives = data.actives;
        
        for (int i = 0; i < data.count; i++) {
            if (!actives[i]) continue;
            positions[i] += velocities[i] * dt;
            lives[i] -= dt;
            if (lives[i] <= 0) actives[i] = false;
        }
    }
}
```

现在缓存利用率接近 100%，性能提升 5-10 倍。

**进一步优化：剔除 inactive 粒子**

```csharp
// 坏做法：遍历时跳过 inactive
for (int i = 0; i < count; i++) {
    if (!actives[i]) continue;  // 分支预测失败，缓存浪费
    // ...
}

// 好做法：紧凑存储，没有 inactive 粒子
// 用 swap-remove 技巧保持数组紧凑
void Deactivate(int index) {
    int lastIndex = --count;
    positions[index] = positions[lastIndex];  // 把最后一个移到空位
    velocities[index] = velocities[lastIndex];
    // ... 其他字段
}
```

### 总结

**核心要点：**
- DOD 以数据为中心，关注数据布局和批量处理
- DOD 的核心优势是 CPU 缓存友好，性能可提升 5-10 倍
- DOD 四原则：数据布局优先、数据逻辑分离、批量处理、硬件友好
- SoA（Struct of Arrays）是 DOD 的典型数据布局

**常见注意事项：**
- DOD 不是要完全替代 OOP，而是针对性能关键代码使用
- DOD 重构时，先分析数据访问模式，再决定如何拆分
- 不要过度优化：非热点代码用 OOP 更易维护
- DOD 要求思维转变：从"对象有什么行为"转向"数据如何流动"

---

## 第 10 讲：Archetype（原型）架构

### 概念

**Archetype（原型）** 是 ECS 中一种高效的内存管理方案，由 Unity DOTS 推广。它将拥有相同组件组合的实体分组到一起，存储在连续的内存块中。

一个 Archetype 代表一种"组件组合模式"。例如：
- Archetype A：{Position, Velocity, Health} —— 有位置、速度、血量的实体
- Archetype B：{Position, Sprite} —— 有位置、图像的静态实体
- Archetype C：{Position, Velocity, Health, AI} —— 有位置、速度、血量、AI 的智能实体

每个 Archetype 内部的实体数据连续存储，遍历时缓存命中率极高。

### 原理

**Archetype 的核心思想：按组件组合分组**

传统 ECS（如早期 entt）用稀疏集存储组件，每个组件类型一个稀疏集。查询时需要交集运算，且数据不连续。

Archetype 方案则按"组件组合"分组：

```
实体1: {Position, Velocity}          → Archetype A
实体2: {Position, Velocity, Health}  → Archetype B
实体3: {Position, Velocity}          → Archetype A
实体4: {Position, Sprite}            → Archetype C
实体5: {Position, Velocity, Health}  → Archetype B

分组结果:
Archetype A {Position, Velocity}:     [实体1, 实体3]
Archetype B {Position, Velocity, Health}: [实体2, 实体5]
Archetype C {Position, Sprite}:       [实体4]
```

**Archetype 的内存布局：**

```
Archetype A {Position, Velocity}:
┌─────────────────────────────────────┐
│ Chunk 0 (16KB)                      │
│  Position 数组: [E1.pos][E3.pos]... │  ← 连续
│  Velocity 数组: [E1.vel][E3.vel]... │  ← 连续
├─────────────────────────────────────┤
│ Chunk 1 (16KB)                      │
│  Position 数组: [...]               │
│  Velocity 数组: [...]               │
└─────────────────────────────────────┘

Archetype B {Position, Velocity, Health}:
┌─────────────────────────────────────┐
│ Chunk 0 (16KB)                      │
│  Position 数组: [E2.pos][E5.pos]... │
│  Velocity 数组: [E2.vel][E5.vel]... │
│  Health 数组:   [E2.hp][E5.hp]...   │
└─────────────────────────────────────┘
```

**查询效率：**

当系统查询"所有有 Position 和 Velocity 的实体"时：
1. 遍历所有 Archetype，检查是否包含查询的所有组件
2. Archetype A 包含 {Position, Velocity} ✓ → 加入结果
3. Archetype B 包含 {Position, Velocity, Health} ⊇ {Position, Velocity} ✓ → 加入结果
4. Archetype C 只有 {Position, Sprite}，缺 Velocity ✗ → 跳过
5. 对匹配的 Archetype，遍历其所有 Chunk，批量处理

这种查询是 O(Archetype 数量 + 匹配实体数量)，远快于稀疏集的交集运算。

**添加/删除组件的代价：**

Archetype 方案的代价是组件增删较慢。给实体添加组件会改变其组件组合，导致它需要从一个 Archetype 移动到另一个：

```
实体 E1 在 Archetype A {Position, Velocity}
添加 Health 组件:
1. 找到或创建 Archetype B {Position, Velocity, Health}
2. 在 Archetype B 中分配新位置
3. 复制 E1 的 Position, Velocity 数据到新位置
4. 设置 Health 数据
5. 从 Archetype A 中移除 E1（swap-remove）
```

这个操作是 O(组件数量)，比稀疏集的 O(1) 慢，但通常不是性能瓶颈（组件增删不频繁）。

### 例子

**Archetype 的伪代码实现：**

```csharp
// Archetype 标识：组件类型的集合
class Archetype {
    public BitSet ComponentMask;  // 用位图表示包含哪些组件
    public Type[] ComponentTypes;
    public List<Chunk> Chunks;    // 该 Archetype 的所有内存块
    public int EntityCount;
    
    public bool ContainsAll(BitSet requiredMask) {
        return (ComponentMask & requiredMask) == requiredMask;
    }
}

// Chunk：固定大小的内存块
class Chunk {
    public const int Size = 16384;  // 16KB
    public Archetype Archetype;
    public int Count;               // 当前存储的实体数
    public int Capacity;            // 最大容量
    public Array[] ComponentArrays; // 每种组件一个数组
    
    public Span<T> GetComponentArray<T>() {
        return (T[])ComponentArrays[GetComponentIndex<T>()];
    }
}

// ArchetypeManager：管理所有 Archetype
class ArchetypeManager {
    private Dictionary<BitSet, Archetype> archetypes = new();
    
    public Archetype GetOrCreate(Type[] componentTypes) {
        var mask = ComputeMask(componentTypes);
        if (!archetypes.TryGetValue(mask, out var archetype)) {
            archetype = new Archetype(mask, componentTypes);
            archetypes[mask] = archetype;
        }
        return archetype;
    }
    
    public List<Archetype> Query(BitSet requiredMask) {
        var result = new List<Archetype>();
        foreach (var archetype in archetypes.Values) {
            if (archetype.ContainsAll(requiredMask)) {
                result.Add(archetype);
            }
        }
        return result;
    }
}
```

**Unity DOTS 中的 Archetype 使用：**

```csharp
// 显式创建 Archetype（高效）
EntityArchetype enemyArchetype = entityManager.CreateArchetype(
    typeof(Position),
    typeof(Velocity),
    typeof(Health),
    typeof(EnemyTag)
);

// 用 Archetype 创建实体（一次分配所有组件内存）
Entity enemy = entityManager.CreateEntity(enemyArchetype);

// 批量创建
NativeArray<Entity> enemies = entityManager.CreateEntity(enemyArchetype, 1000, Allocator.Temp);

// 添加组件会改变 Archetype（有性能代价）
entityManager.AddComponent<Health>(enemy);  // enemy 从原 Archetype 移到新 Archetype

// 移除组件同样会改变 Archetype
entityManager.RemoveComponent<Velocity>(enemy);
```

**Archetype 查询的优化：**

```csharp
// 系统定义查询时，框架会缓存匹配的 Archetype 列表
public partial struct MovementSystem : ISystem {
    private EntityQuery query;
    
    public void OnCreate(ref SystemState state) {
        // 创建查询，框架会缓存匹配的 Archetype
        query = state.GetEntityQuery(
            ComponentType.ReadWrite<Position>(),
            ComponentType.ReadOnly<Velocity>()
        );
    }
    
    public void OnUpdate(ref SystemState state) {
        // 查询直接使用缓存的 Archetype 列表，极快
        int count = query.CalculateEntityCount();
        // ...
    }
}
```

### 总结

**核心要点：**
- Archetype = 组件组合模式，相同组合的实体分组存储
- Archetype 内部数据连续存储，缓存友好
- 查询通过匹配 Archetype 的组件掩码，效率高
- 添加/删除组件需要移动实体到新 Archetype，有性能代价

**Archetype vs 稀疏集对比：**

| 维度 | Archetype | 稀疏集 |
|------|-----------|--------|
| 遍历性能 | 极快（连续内存） | 较快（可能有空洞） |
| 查询性能 | 快（匹配 Archetype） | 中（交集运算） |
| 添加组件 | 慢（移动实体） | 快（O(1)） |
| 删除组件 | 慢（移动实体） | 快（O(1)） |
| 内存利用率 | 高（紧凑） | 中（有稀疏索引） |
| 适用场景 | 组件组合稳定 | 组件频繁增删 |

**常见注意事项：**
- 避免频繁添加/删除组件，这会导致实体在 Archetype 间反复移动
- 设计时尽量让组件组合稳定（如用标签组件表示状态，而非增删组件）
- 批量创建实体时使用 Archetype，比逐个添加组件高效得多
- Archetype 数量不宜过多（几百个以内），否则查询匹配开销增大

---

## 第 11 讲：Chunk（块）内存管理

### 概念

**Chunk（块）** 是 Archetype 架构中的基本内存单元。每个 Archetype 由多个 Chunk 组成，每个 Chunk 是固定大小的连续内存块（通常 16KB），存储若干个同 Archetype 实体的组件数据。

Chunk 是 ECS 高性能的核心：它保证了同类数据的内存局部性，让 CPU 缓存发挥最大效用。

### 原理

**Chunk 的设计目标：**

1. **固定大小**：通常 16KB，匹配 CPU L1 缓存大小，确保一个 Chunk 完全放入 L1
2. **连续内存**：Chunk 内部数据连续存储，无空洞
3. **紧凑布局**：同 Archetype 的实体数据紧密排列，无浪费
4. **批量处理**：系统按 Chunk 粒度遍历，每个 Chunk 内部顺序处理

**Chunk 的内部结构：**

```
Chunk (16KB) - Archetype {Position, Velocity, Health}:
┌──────────────────────────────────────────────┐
│ Header (元数据)                               │
│  - Archetype 指针                             │
│  - 实体数量                                   │
│  - 容量                                       │
├──────────────────────────────────────────────┤
│ Entity 数组:    [E1][E2][E3][E4]...          │  ← 实体 ID
│ Position 数组:  [P1][P2][P3][P4]...          │  ← 连续
│ Velocity 数组:  [V1][V2][V3][V4]...          │  ← 连续
│ Health 数组:    [H1][H2][H3][H4]...          │  ← 连续
└──────────────────────────────────────────────┘

每个数组按 SoA 布局，同类数据连续
```

**Chunk 容量计算：**

```
Chunk 大小 = 16384 字节
组件大小 = sizeof(Position) + sizeof(Velocity) + sizeof(Health) + sizeof(Entity)
        = 12 + 12 + 8 + 8 = 40 字节
Chunk 容量 = 16384 / 40 ≈ 409 个实体
```

**Chunk 的分配与回收：**

```
创建实体:
1. 找到对应 Archetype
2. 找一个有空位的 Chunk（或分配新 Chunk）
3. 在 Chunk 中分配一个槽位
4. 写入组件数据

销毁实体:
1. 找到实体所在的 Chunk
2. 用 swap-remove：把最后一个实体移到被删除位置
3. 减少 Chunk 的实体计数
4. 如果 Chunk 空了，回收到空闲池
```

**swap-remove 的优势：**

```
删除前:
Position: [P1][P2][P3][P4][P5]  count=5

删除 P2:
方式1（移动）：[P1][P3][P4][P5][  ]  count=4  ← 需要移动 3 个，O(n)
方式2（swap-remove）：[P1][P5][P3][P4][  ]  count=4  ← 只移动 1 个，O(1)

swap-remove 保持了数据紧凑，且是 O(1) 操作
```

**系统遍历 Chunk 的流程：**

```csharp
void UpdateSystem(EntityQuery query) {
    // 1. 获取所有匹配的 Chunk
    var chunks = query.GetChunks();
    
    // 2. 逐个 Chunk 处理
    foreach (var chunk in chunks) {
        int count = chunk.Count;
        var positions = chunk.GetComponentArray<Position>();
        var velocities = chunk.GetComponentArray<Velocity>();
        
        // 3. Chunk 内部顺序遍历（缓存完美利用）
        for (int i = 0; i < count; i++) {
            positions[i] += velocities[i] * dt;
        }
    }
}
```

### 例子

**简化的 Chunk 实现：**

```csharp
public class Chunk {
    public const int ChunkSize = 16384;  // 16KB
    
    public Archetype Archetype { get; }
    public int Count { get; private set; }
    public int Capacity { get; }
    
    private Array[] componentArrays;
    private Entity[] entities;
    
    public Chunk(Archetype archetype) {
        Archetype = archetype;
        
        // 计算容量
        int bytesPerEntity = archetype.BytesPerEntity;
        Capacity = ChunkSize / bytesPerEntity;
        
        // 分配数组
        entities = new Entity[Capacity];
        componentArrays = new Array[archetype.ComponentTypes.Length];
        for (int i = 0; i < archetype.ComponentTypes.Length; i++) {
            var type = archetype.ComponentTypes[i];
            componentArrays[i] = Array.CreateInstance(type, Capacity);
        }
    }
    
    public int Allocate(Entity entity) {
        int index = Count++;
        entities[index] = entity;
        return index;
    }
    
    public void SetComponent<T>(int index, T data) {
        var array = (T[])componentArrays[GetComponentIndex<T>()];
        array[index] = data;
    }
    
    public ref T GetComponent<T>(int index) {
        var array = (T[])componentArrays[GetComponentIndex<T>()];
        return ref array[index];
    }
    
    // swap-remove：删除指定索引的实体
    public void Remove(int index) {
        int lastIndex = --Count;
        if (index != lastIndex) {
            // 把最后一个移到被删除位置
            entities[index] = entities[lastIndex];
            for (int i = 0; i < componentArrays.Length; i++) {
                Array.Copy(componentArrays[i], lastIndex, 
                          componentArrays[i], index, 1);
            }
            // 更新被移动实体的 Chunk 内索引
            UpdateEntityIndex(entities[index], index);
        }
    }
    
    private int GetComponentIndex<T>() {
        // 查找 T 在 Archetype 中的索引
        return Archetype.GetComponentIndex(typeof(T));
    }
}
```

**Unity DOTS 中查看 Chunk 信息：**

```csharp
public partial struct ChunkDebugSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        var query = state.GetEntityQuery(
            ComponentType.ReadWrite<Position>(),
            ComponentType.ReadOnly<Velocity>()
        );
        
        // 获取所有匹配的 Chunk
        var chunks = query.ToArchetypeChunkArray(Allocator.Temp);
        
        foreach (var chunk in chunks) {
            UnityEngine.Debug.Log($"Chunk: 实体数={chunk.Count}, " +
                $"容量={chunk.Capacity}, " +
                $"Archetype={chunk.Archetype}");
        }
        
        chunks.Dispose();
    }
}

// 使用 IJobChunk 按 Chunk 粒度处理
[BurstCompile]
public partial struct MovementJob : IJobChunk {
    public float DeltaTime;
    public ComponentTypeHandle<Position> PositionType;
    [ReadOnly] public ComponentTypeHandle<Velocity> VelocityType;
    
    public void Execute(in ArchetypeChunk chunk, int firstEntityIndex) {
        var positions = chunk.GetNativeArray(ref PositionType);
        var velocities = chunk.GetNativeArray(ref VelocityType);
        
        // Chunk 内部顺序遍历
        for (int i = 0; i < chunk.Count; i++) {
            positions[i] = new Position { 
                Value = positions[i].Value + velocities[i].Value * DeltaTime 
            };
        }
    }
}
```

### 总结

**核心要点：**
- Chunk = 固定大小（16KB）的连续内存块，是 Archetype 的基本存储单元
- Chunk 内部按 SoA 布局，同类组件数据连续存储
- Chunk 大小匹配 L1 缓存，确保遍历时缓存命中率极高
- 实体删除使用 swap-remove，保持数据紧凑，O(1) 复杂度

**常见注意事项：**
- Chunk 大小是固定的，组件越多，每个 Chunk 能容纳的实体越少
- 频繁创建/销毁实体会导致 Chunk 碎片化（部分 Chunk 半满）
- 共享组件会导致 Archetype 进一步细分（每个共享值一个子组）
- 遍历时优先按 Chunk 粒度处理，而非逐实体处理

---

## 第 12 讲：EntityQuery（实体查询）

### 概念

**EntityQuery（实体查询）** 是 ECS 中系统获取数据的接口。它描述"我需要哪些组件的实体"，框架根据查询条件返回匹配的实体数据。

EntityQuery 是系统与数据之间的桥梁：系统声明"我要什么"，EntityQuery 负责"找到它"。

### 原理

**EntityQuery 的查询条件：**

一个查询由三类条件组成：

1. **必需组件（All）**：实体必须拥有所有这些组件
2. **任一组件（Any）**：实体至少拥有其中一个（可选）
3. **排除组件（None）**：实体不能拥有这些组件

```
查询示例:
All:   {Position, Velocity}      ← 必须有位置和速度
Any:   {PlayerTag, EnemyTag}     ← 必须是玩家或敌人
None:  {DeadTag}                 ← 不能是已死亡的

匹配的实体:
✓ {Position, Velocity, PlayerTag}           ← 匹配
✓ {Position, Velocity, EnemyTag}            ← 匹配
✗ {Position, Velocity, DeadTag, PlayerTag}  ← 有 DeadTag，排除
✗ {Position, PlayerTag}                     ← 缺 Velocity，排除
```

**查询的执行过程：**

```
1. 解析查询条件，生成 BitSet 掩码
2. 遍历所有 Archetype:
   - 检查 Archetype 是否包含所有 All 组件
   - 检查 Archetype 是否不包含任何 None 组件
   - 检查 Archetype 是否包含至少一个 Any 组件（如果有 Any 条件）
3. 收集所有匹配的 Archetype 的 Chunk
4. 返回 Chunk 列表或扁平化的组件数组
```

**查询缓存：**

每次查询都遍历所有 Archetype 代价较高，因此框架会缓存查询结果：

```
首次查询:
  遍历所有 Archetype → 匹配 → 缓存结果

后续查询:
  直接使用缓存 → 检查是否有新 Archetype → 增量更新

Archetype 变化时（添加/删除组件类型）:
  通知所有相关查询 → 更新缓存
```

**查询的访问模式：**

```csharp
// 只读访问（多个系统可并行）
ComponentType.ReadOnly<Position>()

// 读写访问（独占，其他系统不能同时写）
ComponentType.ReadWrite<Position>()

// 正确使用只读标记可以让框架自动并行化系统
```

### 例子

**Unity DOTS 中的 EntityQuery：**

```csharp
// === 方式1：在系统类中用特性声明查询 ===
public partial struct MovementSystem : ISystem {
    [BurstCompile]
    public void OnUpdate(ref SystemState state) {
        float dt = SystemAPI.Time.DeltaTime;
        
        // 直接用 SystemAPI.Query 声明查询
        foreach (var (transform, velocity) in 
                 SystemAPI.Query<RefRW<LocalTransform>, RefRO<Velocity>>()) {
            transform.ValueRW.Position += velocity.ValueRO.Value * dt;
        }
    }
}

// === 方式2：手动创建 EntityQuery ===
public partial struct AdvancedQuerySystem : ISystem {
    private EntityQuery aliveEnemiesQuery;
    
    public void OnCreate(ref SystemState state) {
        // 创建查询：有 EnemyTag 和 Health，没有 DeadTag
        aliveEnemiesQuery = state.GetEntityQuery(
            new EntityQueryDesc {
                All = new ComponentType[] {
                    ComponentType.ReadOnly<EnemyTag>(),
                    ComponentType.ReadWrite<Health>()
                },
                None = new ComponentType[] {
                    ComponentType.ReadOnly<DeadTag>()
                }
            }
        );
    }
    
    public void OnUpdate(ref SystemState state) {
        // 获取实体数量
        int enemyCount = aliveEnemiesQuery.CalculateEntityCount();
        
        // 获取组件数组（扁平化）
        var healths = aliveEnemiesQuery.ToComponentDataArray<Health>(Allocator.Temp);
        
        // 获取实体数组
        var entities = aliveEnemiesQuery.ToEntityArray(Allocator.Temp);
        
        // 处理...
        for (int i = 0; i < healths.Length; i++) {
            // ...
        }
        
        healths.Dispose();
        entities.Dispose();
    }
}

// === 方式3：使用 IJobEntity（推荐，自动并行）===
[BurstCompile]
public partial struct DamageJob : IJobEntity {
    public float DamageAmount;
    
    // 查询条件通过参数类型自动推断
    void Execute(Entity entity, ref Health health, in Damage damage) {
        health.Current -= damage.Amount;
    }
}

public partial struct DamageSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        new DamageJob { DamageAmount = 10f }.ScheduleParallel();
    }
}
```

**Bevy 中的查询：**

```rust
use bevy_ecs::prelude::*;

fn combat_system(
    mut enemies: Query<(&mut Health, &Position), (With<EnemyTag>, Without<DeadTag>)>,
    players: Query<&Position, With<PlayerTag>>,
) {
    // 查询所有有 EnemyTag、没有 DeadTag 的实体
    for (mut enemy_health, enemy_pos) in enemies.iter_mut() {
        // 查询所有玩家
        for player_pos in players.iter() {
            if enemy_pos.distance(player_pos) < 5.0 {
                enemy_health.current -= 10;
            }
        }
    }
}
```

**查询性能优化技巧：**

```csharp
// ✅ 好的做法：用只读标记
SystemAPI.Query<RefRW<Position>, RefRO<Velocity>>()  // Velocity 只读

// ❌ 坏的做法：全部用读写
SystemAPI.Query<RefRW<Position>, RefRW<Velocity>>()  // Velocity 不必要地独占

// ✅ 好的做法：用标签组件过滤
query = GetEntityQuery(typeof(EnemyTag), typeof(Health));  // 只查敌人

// ❌ 坏的做法：查询所有实体再手动过滤
foreach (var entity in allEntitiesQuery) {
    if (HasComponent<EnemyTag>(entity)) { ... }  // 浪费
}

// ✅ 好的做法：缓存查询结果
private EntityQuery cachedQuery;
void OnCreate() { cachedQuery = GetEntityQuery(...); }
void OnUpdate() { /* 使用 cachedQuery */ }

// ❌ 坏的做法：每帧重新创建查询
void OnUpdate() {
    var query = GetEntityQuery(...);  // 每帧创建，有开销
}
```

### 总结

**核心要点：**
- EntityQuery = 系统获取数据的接口，描述"我要什么组件的实体"
- 查询条件三类：All（必需）、Any（任一）、None（排除）
- 查询结果会被缓存，Archetype 变化时增量更新
- 只读标记（ReadOnly）能让框架自动并行化系统

**常见注意事项：**
- 尽量用只读标记，让框架能并行执行多个系统
- 缓存 EntityQuery，不要每帧重新创建
- 用标签组件过滤，而非查询所有实体再手动过滤
- 避免在查询中包含不必要的组件，减少数据加载量

---

# 第四章：数据流与生命周期

> 本章目标：理解 ECS 运行时的数据流动方式，掌握实体、组件、系统的生命周期管理。

---

## 第 13 讲：实体与组件的生命周期

### 概念

**实体与组件的生命周期** 指从创建到销毁的完整过程。在 ECS 中，实体和组件的生命周期紧密耦合：实体创建时分配组件存储，销毁时释放组件存储；组件可以动态添加和移除，每次操作都会影响实体的 Archetype 归属。

理解生命周期管理是写出正确、高效 ECS 代码的基础。错误的操作（如遍历时销毁实体）会导致数据结构损坏和崩溃。

### 原理

**实体的完整生命周期：**

```
[创建]
  ├─ 分配 Entity ID（新或回收）
  ├─ 确定 Archetype（根据初始组件）
  ├─ 在 Archetype 的 Chunk 中分配槽位
  ├─ 初始化组件数据（默认值或指定值）
  └─ 实体进入"活跃"状态
     │
     ├─ [组件变更]（可能多次）
     │   ├─ 添加组件 → 移到新 Archetype
     │   ├─ 移除组件 → 移到新 Archetype
     │   └─ 修改组件数据 → 原地更新
     │
     └─ [销毁]
         ├─ 从当前 Archetype 的 Chunk 中移除（swap-remove）
         ├─ 释放所有组件数据
         ├─ Entity ID 回收（Version++）
         └─ 实体进入"已销毁"状态
```

**组件的生命周期：**

```
[添加组件]
  ├─ 实体从原 Archetype 移到新 Archetype（包含新组件）
  ├─ 复制原有组件数据到新位置
  ├─ 初始化新组件数据
  └─ 旧 Archetype 中的槽位被回收

[修改组件]
  └─ 直接在 Chunk 中更新数据（原地操作，极快）

[移除组件]
  ├─ 实体从原 Archetype 移到新 Archetype（不包含该组件）
  ├─ 复制保留的组件数据到新位置
  └─ 旧 Archetype 中的槽位被回收
```

**结构变更的代价：**

添加/移除组件是"结构变更"（structural change），代价较高：
1. 需要查找或创建新 Archetype
2. 需要复制组件数据
3. 需要更新所有相关的 EntityQuery 缓存
4. 可能导致 Chunk 重新分配

因此，**应尽量避免在每帧中频繁增删组件**。

**EntityCommandBuffer（延迟执行）：**

在系统遍历过程中直接增删实体会导致数据结构变动，可能使当前遍历失效。解决方案是使用 EntityCommandBuffer（ECB）将变更操作记录下来，在遍历结束后统一执行：

```
遍历阶段:
  系统 A 遍历实体 → 发现需要销毁的实体 → 记录到 ECB
  系统 B 遍历实体 → 发现需要添加组件的实体 → 记录到 ECB

执行阶段（遍历结束后）:
  ECB.Playback() → 按顺序执行所有记录的操作
```

### 例子

**Unity DOTS 中的生命周期管理：**

```csharp
// === 实体创建 ===
public partial struct SpawnSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        var ecb = new EntityCommandBuffer(Allocator.Temp);
        
        // 创建单个实体
        Entity enemy = ecb.CreateEntity();
        ecb.AddComponent(enemy, new Position { Value = float3.zero });
        ecb.AddComponent(enemy, new Health { Current = 100, Max = 100 });
        ecb.AddComponent(enemy, new EnemyTag());
        
        // 使用 Archetype 批量创建（更高效）
        var archetype = state.EntityManager.CreateArchetype(
            typeof(Position), typeof(Health), typeof(EnemyTag)
        );
        
        for (int i = 0; i < 100; i++) {
            Entity e = ecb.CreateEntity(archetype);
            ecb.SetComponent(e, new Position { Value = new float3(i, 0, 0) });
        }
        
        ecb.Playback(state.EntityManager);
        ecb.Dispose();
    }
}

// === 实体销毁 ===
public partial struct DeathSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        var ecb = new EntityCommandBuffer(Allocator.Temp);
        
        // 销毁所有血量 <= 0 的实体
        foreach (var (health, entity) in 
                 SystemAPI.Query<RefRO<Health>>().WithEntityAccess()) {
            if (health.ValueRO.Current <= 0) {
                ecb.DestroyEntity(entity);  // 延迟销毁
            }
        }
        
        ecb.Playback(state.EntityManager);
        ecb.Dispose();
    }
}

// === 组件动态添加/移除 ===
public partial struct StatusSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        var ecb = new EntityCommandBuffer(Allocator.Temp);
        
        // 给血量低于 30% 的实体添加"濒死"标签
        foreach (var (health, entity) in 
                 SystemAPI.Query<RefRO<Health>>().WithEntityAccess()) {
            if (health.ValueRO.Current < health.ValueRO.Max * 0.3f) {
                if (!state.EntityManager.HasComponent<DyingTag>(entity)) {
                    ecb.AddComponent<DyingTag>(entity);
                }
            }
        }
        
        // 移除恢复健康的实体的"濒死"标签
        foreach (var (health, entity) in 
                 SystemAPI.Query<RefRO<Health>>().WithEntityAccess()) {
            if (health.ValueRO.Current > health.ValueRO.Max * 0.5f) {
                if (state.EntityManager.HasComponent<DyingTag>(entity)) {
                    ecb.RemoveComponent<DyingTag>(entity);
                }
            }
        }
        
        ecb.Playback(state.EntityManager);
        ecb.Dispose();
    }
}
```

**使用 BeginSimulationEntityCommandBufferSystem：**

```csharp
// 推荐方式：使用系统组的 ECB，自动在合适时机执行
public partial struct SpawnSystem : ISystem {
    private BeginSimulationEntityCommandBufferSystem ecbSystem;
    
    public void OnCreate(ref SystemState state) {
        ecbSystem = state.World.GetExistingSystemManaged<BeginSimulationEntityCommandBufferSystem>();
    }
    
    public void OnUpdate(ref SystemState state) {
        // 获取 ECB，会在下一帧模拟开始时自动 Playback
        var ecb = ecbSystem.CreateCommandBuffer();
        
        // 记录操作...
        ecb.CreateEntity();
        // ...
        
        // 无需手动 Playback，系统会自动处理
    }
}
```

**生命周期的最佳实践：**

```csharp
// ✅ 好的做法：用标签组件表示状态，而非频繁增删组件
struct PoisonedTag : IComponentData { }  // 标签
struct PoisonTimer : IComponentData { float remaining; }  // 数据

// 添加中毒状态（一次性操作）
ecb.AddComponent<PoisonedTag>(entity);
ecb.AddComponent(entity, new PoisonTimer { remaining = 5f });

// 更新中毒计时器（原地修改，无结构变更）
foreach (var timer in SystemAPI.Query<RefRW<PoisonTimer>>()) {
    timer.ValueRW.remaining -= dt;
}

// ❌ 坏的做法：用增删组件表示状态变化
// 每帧都增删组件，导致频繁结构变更
void BadApproach() {
    if (isPoisoned) AddComponent<Poisoned>(entity);
    else RemoveComponent<Poisoned>(entity);
    // 每帧都做结构变更，性能差
}
```

### 总结

**核心要点：**
- 实体生命周期：创建 → 活跃（组件变更）→ 销毁
- 组件增删是"结构变更"，代价较高，应避免频繁操作
- 遍历过程中不能直接增删实体，必须用 EntityCommandBuffer 延迟执行
- 用标签组件表示状态，比频繁增删组件更高效

**常见注意事项：**
- 不要在 `foreach` 遍历中直接调用 `DestroyEntity` 或 `AddComponent`，会导致崩溃
- ECB 使用后必须 Dispose，否则内存泄漏
- 批量创建实体时用 Archetype，比逐个添加组件快 10 倍以上
- 实体销毁后，所有持有该 Entity 引用的代码都应检查 `Exists()`

---

## 第 14 讲：系统的执行顺序与依赖

### 概念

**系统的执行顺序** 决定了多个系统如何在一帧内协同工作。ECS 框架通过系统组（SystemGroup）和排序属性（UpdateBefore/UpdateAfter）来管理系统间的执行顺序。

正确的系统顺序至关重要：例如，移动系统必须在碰撞系统之前执行，渲染系统必须在所有逻辑系统之后执行。错误的顺序会导致"一帧延迟"或逻辑错误。

### 原理

**系统组的层级结构：**

ECS 框架通常用树形结构组织系统：

```
RootSystemGroup
├── InitializationSystemGroup      (初始化阶段)
│   ├── BeginInitializationECBSystem
│   ├── SpawnSystem
│   └── EndInitializationECBSystem
├── SimulationSystemGroup          (模拟阶段)
│   ├── BeginSimulationECBSystem
│   ├── MovementSystem
│   ├── CollisionSystem            (必须在 MovementSystem 之后)
│   ├── DamageSystem               (必须在 CollisionSystem 之后)
│   ├── DeathSystem                (必须在 DamageSystem 之后)
│   └── EndSimulationECBSystem
└── PresentationSystemGroup        (表现阶段)
    ├── BeginPresentationECBSystem
    ├── RenderSystem
    └── EndPresentationECBSystem
```

**排序机制：**

系统排序有两种方式：

1. **显式排序**：用 `[UpdateBefore]` 和 `[UpdateAfter]` 特性
2. **隐式排序**：通过数据依赖自动推断

```csharp
// 显式排序
[UpdateBefore(typeof(CollisionSystem))]
public partial struct MovementSystem : ISystem { }

[UpdateAfter(typeof(MovementSystem))]
public partial struct CollisionSystem : ISystem { }
```

**数据依赖与自动并行：**

当两个系统访问相同组件时，框架会根据访问模式推断依赖：
- 两个系统都只读同一组件 → 可以并行
- 一个系统写，另一个系统读/写同一组件 → 必须串行

```
系统 A: 读 Position, 写 Velocity
系统 B: 读 Position, 写 Velocity
→ A 和 B 都写 Velocity，必须串行

系统 A: 读 Position (只读)
系统 B: 读 Position (只读)
→ 都只读，可以并行

系统 A: 读 Position, 写 Velocity
系统 B: 读 Velocity, 写 Health
→ A 写 Velocity，B 读 Velocity，必须串行（A 先 B 后）
```

**系统组的更新流程：**

```
SimulationSystemGroup.Update():
  1. 排序所有子系统（根据 UpdateBefore/After 和数据依赖）
  2. 按顺序执行每个系统:
     for each system in sortedSystems:
         system.Update()
  3. 等待所有并行 Job 完成
```

### 例子

**Unity DOTS 中的系统排序：**

```csharp
// === 使用特性声明执行顺序 ===
[UpdateInGroup(typeof(SimulationSystemGroup))]
[UpdateBefore(typeof(CollisionSystem))]  // 在碰撞系统之前执行
public partial struct MovementSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        // 移动逻辑
    }
}

[UpdateInGroup(typeof(SimulationSystemGroup))]
[UpdateAfter(typeof(MovementSystem))]  // 在移动系统之后执行
public partial struct CollisionSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        // 碰撞检测
    }
}

[UpdateInGroup(typeof(SimulationSystemGroup))]
[UpdateAfter(typeof(CollisionSystem))]
public partial struct DamageSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        // 伤害计算
    }
}

// === 自定义系统组 ===
[UpdateInGroup(typeof(SimulationSystemGroup))]
public partial struct CombatSystemGroup : ComponentSystemGroup {
    // 战斗相关系统的分组
}

[UpdateInGroup(typeof(CombatSystemGroup))]
public partial struct AttackSystem : ISystem { }

[UpdateInGroup(typeof(CombatSystemGroup))]
[UpdateAfter(typeof(AttackSystem))]
public partial struct DefenseSystem : ISystem { }
```

**Bevy 中的系统排序：**

```rust
use bevy_ecs::prelude::*;

fn main() {
    App::new()
        // .chain() 保证顺序执行
        .add_systems(Update, (
            movement_system,
            collision_system,
            damage_system,
        ).chain())
        // 等价于:
        // movement_system.before(collision_system)
        // collision_system.before(damage_system)
        .run();
}

// 也可以用 before/after 特性
fn movement_system() { }
fn collision_system() { }
fn damage_system() { }

// 显式声明依赖
app.add_systems(Update, collision_system.after(movement_system));
app.add_systems(Update, damage_system.after(collision_system));
```

**系统顺序错误的典型问题：**

```csharp
// ❌ 错误：渲染系统在移动系统之前
[UpdateBefore(typeof(MovementSystem))]  // 渲染在前？
public partial struct RenderSystem : ISystem { }

// 结果：玩家看到的画面比实际位置慢一帧
// 玩家按方向键 → 移动系统更新位置 → 但渲染已经用旧位置画了
// 下一帧才画新位置 → 视觉延迟

// ✅ 正确：渲染系统在所有逻辑之后
[UpdateInGroup(typeof(PresentationSystemGroup))]  // 表现阶段
public partial struct RenderSystem : ISystem { }
```

**多线程系统的依赖管理：**

```csharp
// 系统 A：写入 Velocity
[BurstCompile]
public partial struct VelocityUpdateSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        // 框架自动记录：此系统写 Velocity
        new VelocityUpdateJob().ScheduleParallel();
    }
}

// 系统 B：读取 Velocity，写入 Position
[BurstCompile]
public partial struct MovementSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        // 框架自动检测：B 读 Velocity（A 写），B 写 Position
        // 自动让 B 等待 A 完成
        new MovementJob().ScheduleParallel();
    }
}

// 框架自动处理依赖，无需手动同步
```

### 总结

**核心要点：**
- 系统通过系统组（SystemGroup）组织成树形结构
- 系统顺序用 `[UpdateBefore]` / `[UpdateAfter]` 显式声明
- 框架根据数据读写依赖自动推断并行性
- 只读访问可以让多个系统并行执行

**常见注意事项：**
- 不要依赖系统的注册顺序，必须显式声明依赖关系
- 渲染系统必须在所有逻辑系统之后执行
- 遍历中创建/销毁实体的系统应放在系统组的开头或结尾（用 ECB）
- 跨系统的数据共享用单例组件（Singleton）而非静态变量

---

## 第 15 讲：生命周期回调与事件机制

### 概念

**生命周期回调** 是 ECS 框架在特定时机自动调用的函数，如实体创建时、组件添加时、实体销毁前等。它们让开发者能在关键节点执行初始化或清理逻辑。

**事件机制** 是 ECS 中系统间通信的方式。由于系统是解耦的，不能直接调用彼此的方法，事件提供了一种"发布-订阅"模式的通信手段。

### 原理

**常见的生命周期回调：**

| 回调 | 触发时机 | 典型用途 |
|------|----------|----------|
| OnCreate | 系统/实体创建时 | 初始化资源 |
| OnDestroy | 系统/实体销毁时 | 释放资源 |
| OnComponentAdded | 组件添加时 | 初始化组件数据 |
| OnComponentRemoved | 组件移除时 | 清理关联资源 |
| OnUpdate | 每帧更新 | 主逻辑 |

**事件机制的设计：**

ECS 中的事件通常用"事件组件"实现：用一个组件表示事件，添加该组件表示触发事件，系统处理后移除该组件。

```
事件触发:
  系统 A → 给实体添加 DamageEvent 组件 → 表示"该实体受到伤害"

事件处理:
  系统 B → 查询所有有 DamageEvent 的实体 → 处理伤害 → 移除 DamageEvent

事件清理:
  系统 C → 清理过期的事件组件
```

**事件的分类：**

1. **单帧事件**：处理完立即移除（如 DamageEvent）
2. **持续事件**：持续多帧（如 BuffEvent，有持续时间）
3. **全局事件**：不绑定实体（如 GameStartEvent）

### 例子

**Unity DOTS 中的生命周期回调：**

```csharp
// === 系统生命周期回调 ===
public partial struct MySystem : ISystem {
    public void OnCreate(ref SystemState state) {
        // 系统创建时调用（只一次）
        // 用于初始化查询、资源等
        state.RequireForUpdate<PlayerTag>();  // 没有玩家时不更新
    }
    
    public void OnDestroy(ref SystemState state) {
        // 系统销毁时调用（只一次）
        // 用于释放资源
    }
    
    public void OnUpdate(ref SystemState state) {
        // 每帧调用
    }
}

// === 实体组件的生命周期回调（通过 IComponentData 接口）===
// Unity DOTS 中，组件是纯数据，没有回调
// 需要用系统模拟生命周期回调

// 模拟 OnComponentAdded：检测新添加的组件
public partial struct HealthInitSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        var ecb = new EntityCommandBuffer(Allocator.Temp);
        
        // 查询有 Health 但没有 HealthInitialized 标签的实体
        foreach (var (health, entity) in 
                 SystemAPI.Query<RefRW<Health>>()
                          .WithNone<HealthInitialized>()
                          .WithEntityAccess()) {
            // 初始化逻辑
            health.ValueRW.Current = health.ValueRO.Max;
            ecb.AddComponent<HealthInitialized>(entity);
        }
        
        ecb.Playback(state.EntityManager);
        ecb.Dispose();
    }
}

// === 事件机制实现 ===

// 事件组件
public struct DamageEvent : IComponentData {
    public float Amount;
    public Entity Source;
}

// 事件触发系统
public partial struct AttackSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        var ecb = new EntityCommandBuffer(Allocator.Temp);
        
        // 攻击者攻击目标
        foreach (var (attacker, attackerEntity) in 
                 SystemAPI.Query<RefRO<AttackTarget>>().WithEntityAccess()) {
            Entity target = attacker.ValueRO.Target;
            if (state.EntityManager.Exists(target)) {
                // 触发伤害事件：给目标添加 DamageEvent
                ecb.AddComponent(target, new DamageEvent {
                    Amount = 10,
                    Source = attackerEntity
                });
            }
        }
        
        ecb.Playback(state.EntityManager);
        ecb.Dispose();
    }
}

// 事件处理系统
public partial struct DamageProcessSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        var ecb = new EntityCommandBuffer(Allocator.Temp);
        
        // 处理所有伤害事件
        foreach (var (damageEvent, health, entity) in 
                 SystemAPI.Query<RefRO<DamageEvent>, RefRW<Health>>()
                          .WithEntityAccess()) {
            health.ValueRW.Current -= damageEvent.ValueRO.Amount;
            
            // 处理完毕，移除事件
            ecb.RemoveComponent<DamageEvent>(entity);
        }
        
        ecb.Playback(state.EntityManager);
        ecb.Dispose();
    }
}
```

**Bevy 中的事件系统：**

```rust
use bevy_ecs::prelude::*;

// 定义事件类型
#[derive(Event)]
struct DamageEvent {
    target: Entity,
    amount: f32,
    source: Entity,
}

fn main() {
    App::new()
        .add_event::<DamageEvent>()  // 注册事件
        .add_systems(Update, (
            attack_system,
            damage_process_system.after(attack_system),
        ))
        .run();
}

// 事件发送系统
fn attack_system(
    mut events: EventWriter<DamageEvent>,
    attackers: Query<&AttackTarget>,
) {
    for target in attackers.iter() {
        events.send(DamageEvent {
            target: target.target,
            amount: 10.0,
            source: target.attacker,
        });
    }
}

// 事件接收系统
fn damage_process_system(
    mut events: EventReader<DamageEvent>,
    mut healths: Query<&mut Health>,
) {
    for event in events.read() {
        if let Ok(mut health) = healths.get_mut(event.target) {
            health.current -= event.amount;
        }
    }
}
```

**全局事件（不绑定实体）：**

```csharp
// Unity DOTS 中用单例组件实现全局事件
public struct GameStartEvent : IComponentData { }

public partial struct GameStartSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        // 检查游戏开始事件
        if (SystemAPI.HasSingleton<GameStartEvent>()) {
            // 处理游戏开始
            SpawnInitialEnemies(state);
            // 移除事件
            state.EntityManager.DestroyEntity(
                SystemAPI.GetSingletonEntity<GameStartEvent>()
            );
        }
    }
    
    void SpawnInitialEnemies(SystemState state) {
        // ...
    }
}
```

### 总结

**核心要点：**
- 生命周期回调在关键节点自动触发，用于初始化和清理
- ECS 组件是纯数据，生命周期回调通过系统模拟
- 事件机制用"事件组件"实现：添加组件=触发事件，移除组件=处理完毕
- 事件分三类：单帧事件、持续事件、全局事件

**常见注意事项：**
- 事件处理完后必须移除事件组件，否则会重复处理
- 事件触发和处理要在不同系统，避免遍历中结构变更
- 全局事件用单例组件实现，而非静态变量（静态变量无法被框架管理）
- 事件机制会增加组件增删开销，高频事件考虑用其他方案（如环形缓冲区）

---

# 第五章：性能优化

> 本章目标：掌握 ECS 高性能编程的核心技术，包括缓存局部性、SIMD、Burst 编译、多线程和内存管理。

---

## 第 16 讲：缓存局部性原理

### 概念

**缓存局部性（Cache Locality）** 是计算机体系结构中的一个核心概念，指程序访问内存时倾向于访问"附近"的内存地址。ECS 的高性能很大程度上源于对缓存局部性的极致利用。

缓存局部性分两种：
- **空间局部性**：访问了一个地址，很可能接着访问附近的地址
- **时间局部性**：访问了一个地址，很可能不久后再次访问它

### 原理

**CPU 缓存层级结构：**

```
CPU 核心
    ↓
L1 缓存 (32KB, ~1ns)     ← 每核心独享
    ↓
L2 缓存 (256KB, ~4ns)    ← 每核心独享
    ↓
L3 缓存 (8MB, ~12ns)     ← 多核心共享
    ↓
主内存 (GB级, ~100ns)    ← 所有核心共享
```

**缓存行（Cache Line）：**

CPU 不是按字节读取内存，而是按"缓存行"读取，通常 64 字节。即使你只读 1 个字节，CPU 也会把附近 64 字节都加载到缓存。

```
读取 float x (4字节):
CPU 实际加载: [x][?][?][?][?][?]...[?]  (64字节，包含 x 和附近 60 字节)

如果接下来访问这 60 字节内的数据 → 缓存命中，几乎零延迟
如果访问 60 字节外的数据 → 缓存未命中，等待 100ns
```

**ECS 如何利用缓存局部性：**

```
传统 OOP (AoS - Array of Structs):
Enemy[] enemies:
[Enemy0: pos(12B) hp(4B) ai(20B) ...][Enemy1: pos hp ai ...][Enemy2: ...]
     ↑ 64字节缓存行只包含约1个Enemy的部分数据

遍历位置更新:
for (Enemy e : enemies) { e.pos += e.vel * dt; }
每次迭代: 加载整个 Enemy (可能 100B)，但只用 pos(12B) + vel(12B) = 24B
缓存利用率: 24/100 = 24%

ECS (SoA - Struct of Arrays):
Position[] positions: [pos0][pos1][pos2][pos3][pos4][pos5]...  ← 连续
Velocity[] velocities: [vel0][vel1][vel2][vel3][vel4][vel5]...  ← 连续

遍历位置更新:
for (int i=0; i<n; i++) { positions[i] += velocities[i] * dt; }
每次迭代: positions 和 velocities 都是连续数组
64字节缓存行包含 5 个 float3 (每个12B)
缓存利用率: 接近 100%
```

**性能差距实测：**

```
处理 1,000,000 个实体的位置更新:
- OOP (AoS, 每对象 100B): ~50 ms
- ECS (SoA, 连续数组):    ~5 ms   (10 倍提升)
- ECS + SIMD:             ~1.5 ms (33 倍提升)
- ECS + SIMD + 多线程:    ~0.3 ms (166 倍提升)
```

**影响缓存局部性的因素：**

1. **数据布局**：SoA 比 AoS 缓存友好
2. **数据大小**：组件越小，缓存行能容纳越多
3. **访问模式**：顺序访问比随机访问缓存友好
4. **数据对齐**：未对齐的数据可能跨越缓存行

### 例子

**验证缓存局部性的实验：**

```csharp
using System;
using System.Diagnostics;

// 实验数据结构
struct Position { public float X, Y, Z; }  // 12 字节
struct Velocity { public float X, Y, Z; }  // 12 字节

// AoS 布局（传统 OOP）
class EnemyAoS {
    public Position Pos;
    public Velocity Vel;
    public float Health;
    public float Attack;
    public float Defense;
    public int AIState;
    public long Padding1, Padding2, Padding3;  // 模拟更多字段
    // 总大小约 64 字节
}

class Program {
    static void Main() {
        int count = 1_000_000;
        
        // === AoS 测试 ===
        var enemies = new EnemyAoS[count];
        for (int i = 0; i < count; i++) enemies[i] = new EnemyAoS();
        
        var sw = Stopwatch.StartNew();
        for (int i = 0; i < count; i++) {
            enemies[i].Pos.X += enemies[i].Vel.X * 0.016f;
            enemies[i].Pos.Y += enemies[i].Vel.Y * 0.016f;
            enemies[i].Pos.Z += enemies[i].Vel.Z * 0.016f;
        }
        sw.Stop();
        Console.WriteLine($"AoS: {sw.ElapsedMilliseconds} ms");
        
        // === SoA 测试 ===
        var positions = new Position[count];
        var velocities = new Velocity[count];
        
        sw.Restart();
        for (int i = 0; i < count; i++) {
            positions[i].X += velocities[i].X * 0.016f;
            positions[i].Y += velocities[i].Y * 0.016f;
            positions[i].Z += velocities[i].Z * 0.016f;
        }
        sw.Stop();
        Console.WriteLine($"SoA: {sw.ElapsedMilliseconds} ms");
        
        // 典型输出:
        // AoS: ~50 ms
        // SoA: ~8 ms  (6 倍提升)
    }
}
```

**ECS 中优化缓存局部性的实践：**

```csharp
// === 1. 组件设计要小 ===

// ❌ 坏的设计：组件过大
struct CharacterData : IComponentData {
    public float3 position;
    public float3 velocity;
    public float health, maxHealth;
    public float attack, defense, speed;
    public int level, exp;
    public float3 target;
    // ... 50+ 字节
}

// ✅ 好的设计：拆分为小组件
struct Position : IComponentData { public float3 Value; }      // 12B
struct Velocity : IComponentData { public float3 Value; }      // 12B
struct Health : IComponentData { public float Cur, Max; }      // 8B
struct CombatStats : IComponentData { public float Atk, Def; } // 8B

// === 2. 避免在组件中存储引用类型 ===

// ❌ 坏的设计：引用类型导致数据分散
struct BadInventory : IComponentData {
    public Item[] Items;  // 引用类型，数据在堆上，不连续
}

// ✅ 好的设计：用 DynamicBuffer 存储连续数据
struct InventoryBuffer : IBufferElementData {
    public int ItemId;
    public int Count;
}
// 实体上的 DynamicBuffer<InventoryBuffer> 是连续内存

// === 3. 按访问模式组织数据 ===

// 每帧访问的数据（热数据）：保持小组件
struct Position : IComponentData { public float3 Value; }
struct Velocity : IComponentData { public float3 Value; }

// 偶尔访问的数据（冷数据）：可以大一些
struct CharacterInfo : IComponentData {
    public FixedString32 Name;
    public int Level;
    public int Exp;
    // ...
}

// 这样，每帧遍历 Position/Velocity 时，不会加载冷数据
```

**Unity DOTS 中查看缓存效率：**

```csharp
// 使用 Profiler 分析缓存命中
public partial struct MovementSystem : ISystem {
    [BurstCompile]
    public void OnUpdate(ref SystemState state) {
        float dt = SystemAPI.Time.DeltaTime;
        
        // Profiler 会显示:
        // - 遍历的实体数
        // - 缓存命中率
        // - 内存加载量
        
        foreach (var (pos, vel) in 
                 SystemAPI.Query<RefRW<Position>, RefRO<Velocity>>()) {
            pos.ValueRW.Value += vel.ValueRO.Value * dt;
        }
    }
}
```

### 总结

**核心要点：**
- 缓存局部性是 ECS 高性能的根本原因
- 空间局部性：连续内存访问，缓存命中率高
- SoA 布局比 AoS 布局缓存利用率高 5-10 倍
- 组件越小，缓存行能容纳的数据越多

**常见注意事项：**
- 组件设计要小，避免"大组件"导致缓存浪费
- 不要在组件中存储引用类型（class、数组），会破坏连续性
- 热数据和冷数据分离，避免遍历时加载无用数据
- 顺序访问数组，避免随机访问（如链表、哈希表）

---

## 第 17 讲：SIMD 与 Burst 编译

### 概念

**SIMD（Single Instruction Multiple Data）** 是一种 CPU 并行计算技术，一条指令同时处理多个数据。现代 CPU 的 SIMD 指令集包括 SSE（128位，同时处理 4 个 float）、AVX（256位，同时处理 8 个 float）、AVX-512（512位，同时处理 16 个 float）。

**Burst 编译器** 是 Unity 为 ECS 开发的高性能编译器，基于 LLVM，能自动将 C# 代码编译为优化的机器码，自动利用 SIMD 指令。

### 原理

**SIMD 的工作原理：**

```
标量处理（传统）:
4 个 float 相加:
  add eax, [pos0]      ; 1 个时钟周期
  add eax, [pos1]      ; 1 个时钟周期
  add eax, [pos2]      ; 1 个时钟周期
  add eax, [pos3]      ; 1 个时钟周期
  总计: 4 个时钟周期

SIMD 处理（SSE, 128位）:
4 个 float 同时相加:
  addps xmm0, [pos0123]  ; 1 个时钟周期处理 4 个 float
  总计: 1 个时钟周期 (4 倍加速)
```

**Burst 编译器的优化：**

1. **自动向量化**：识别循环，自动用 SIMD 指令
2. **去除运行时检查**：去除数组越界检查、空引用检查等
3. **内联优化**： aggressively 内联函数调用
4. **寄存器分配**：优化寄存器使用，减少内存访问
5. **指令重排**：优化指令顺序，提高流水线效率

**Burst 的限制：**

为了高性能，Burst 限制了一些 C# 特性：
- 不能使用引用类型（class、string、数组）
- 不能使用虚函数、反射
- 不能使用 try-catch
- 不能使用大多数 .NET API

```csharp
// ❌ Burst 不支持的代码
[BurstCompile]
void BadFunction() {
    string name = "Player";  // 引用类型，不支持
    object obj = new object();  // 装箱，不支持
    try { } catch { }  // 异常处理，不支持
    var list = new List<int>();  // 引用类型，不支持
}

// ✅ Burst 支持的代码
[BurstCompile]
void GoodFunction() {
    float3 pos = new float3(0, 0, 0);  // 值类型，支持
    NativeArray<int> arr = new NativeArray<int>(100, Allocator.Temp);  // 原生数组，支持
    for (int i = 0; i < arr.Length; i++) {  // 循环，会被向量化
        arr[i] = i * 2;
    }
}
```

### 例子

**Burst 编译示例：**

```csharp
using Unity.Burst;
using Unity.Collections;
using Unity.Jobs;
using Unity.Mathematics;
using UnityEngine;

// === 未优化的版本（无 Burst）===
public class SlowMovement : MonoBehaviour {
    void Update() {
        var enemies = FindObjectsOfType<EnemyMono>();
        float dt = Time.deltaTime;
        foreach (var e in enemies) {
            e.Position += e.Velocity * dt;
        }
        // 性能：10000 个敌人约 15ms
    }
}

// === Burst 优化版本 ===
[BurstCompile]  // 关键：启用 Burst 编译
public partial struct MovementJob : IJobEntity {
    public float DeltaTime;
    
    void Execute(ref Position pos, in Velocity vel) {
        pos.Value += vel.Value * DeltaTime;
    }
}

public partial struct MovementSystem : ISystem {
    [BurstCompile]
    public void OnUpdate(ref SystemState state) {
        float dt = SystemAPI.Time.DeltaTime;
        
        new MovementJob { DeltaTime = dt }.ScheduleParallel();
        // 性能：10000 个敌人约 0.2ms (75 倍提升)
    }
}
```

**Burst 编译选项：**

```csharp
[BurstCompile(
    CompileSynchronously = true,      // 同步编译（启动时编译，非运行时）
    FloatMode = FloatMode.Fast,       // 快速浮点模式（允许重排）
    FloatPrecision = FloatMode.Low,   // 低精度（更快）
    Debug = false                      // 关闭调试信息
)]
public partial struct FastJob : IJobEntity {
    void Execute(ref Position pos, in Velocity vel) {
        pos.Value += vel.Value * 0.016f;
    }
}
```

**手动 SIMD 优化（使用 Unity.Mathematics）：**

```csharp
using Unity.Mathematics;

// === 标量版本 ===
struct Position { public float X, Y, Z; }
struct Velocity { public float X, Y, Z; }

void UpdateScalar(Position[] positions, Velocity[] velocities, float dt) {
    for (int i = 0; i < positions.Length; i++) {
        positions[i].X += velocities[i].X * dt;
        positions[i].Y += velocities[i].Y * dt;
        positions[i].Z += velocities[i].Z * dt;
    }
}

// === SIMD 版本（使用 float3）===
void UpdateSIMD(NativeArray<float3> positions, NativeArray<float3> velocities, float dt) {
    for (int i = 0; i < positions.Length; i++) {
        positions[i] += velocities[i] * dt;  // float3 运算会被向量化
    }
}

// === 进一步优化：批量处理 ===
[BurstCompile]
void UpdateBatched(NativeArray<float3> positions, NativeArray<float3> velocities, float dt) {
    // 每次处理 4 个 float3（匹配 SSE 128位）
    int batchCount = positions.Length / 4;
    for (int i = 0; i < batchCount; i++) {
        int idx = i * 4;
        positions[idx]     += velocities[idx]     * dt;
        positions[idx + 1] += velocities[idx + 1] * dt;
        positions[idx + 2] += velocities[idx + 2] * dt;
        positions[idx + 3] += velocities[idx + 3] * dt;
    }
    // Burst 会自动将这 4 次运算合并为 1 条 SIMD 指令
}
```

**Burst Inspector：**

Unity 提供 Burst Inspector 窗口，可以查看 Burst 编译后的机器码：

```
菜单: Jobs > Burst > Show Inspector

可以看到:
1. 原始 C# 代码
2. LLVM IR（中间表示）
3. 优化后的机器码（含 SIMD 指令）

关键指标:
- 是否使用了 SIMD 指令（如 addps, mulpd）
- 循环是否被向量化
- 寄存器分配是否合理
```

### 总结

**核心要点：**
- SIMD 一条指令处理多个数据，可提升 4-16 倍性能
- Burst 编译器自动向量化 C# 代码，生成优化机器码
- Burst 限制引用类型、异常、反射等特性
- 使用 `Unity.Mathematics` 的类型（float3、quaternion 等）利于 SIMD

**常见注意事项：**
- 所有 Job 和热点系统都应加 `[BurstCompile]`
- 避免在 Burst 代码中使用引用类型和 string
- 用 `FixedString` 替代 string，用 `NativeArray`/`NativeList` 替代 List
- 用 Burst Inspector 检查是否成功向量化

---

## 第 18 讲：多线程与 Job System

### 概念

**Job System** 是 ECS 实现多线程并行处理的机制。它将工作拆分为多个"Job"（作业），由框架自动调度到多个 CPU 核心上并行执行。

ECS 的 Job System 与传统多线程相比，优势在于：
- **自动依赖管理**：框架根据数据访问自动推断 Job 间的依赖
- **无锁设计**：通过只读/读写标记避免数据竞争
- **工作窃取**：空闲核心会从忙碌核心"窃取"任务，负载均衡

### 原理

**Job 的执行模型：**

```
主线程:
  系统 OnUpdate()
    ├─ 创建 Job A
    ├─ Schedule(Job A)  → 分配到工作线程
    ├─ 创建 Job B
    ├─ Schedule(Job B)  → 分配到工作线程
    └─ Complete(Job A, Job B)  → 等待所有 Job 完成

工作线程池:
  线程 1: [Job A 的 Chunk 0-99]
  线程 2: [Job A 的 Chunk 100-199]
  线程 3: [Job B 的 Chunk 0-99]
  线程 4: [Job B 的 Chunk 100-199]
  ...
```

**Job 的类型：**

| Job 类型 | 说明 | 用途 |
|----------|------|------|
| IJobEntity | 按实体遍历 | 最常用，自动并行 |
| IJobChunk | 按 Chunk 遍历 | 需要访问 Chunk 元数据时 |
| IJobParallelFor | 并行 for 循环 | 自定义并行任务 |
| IJob | 单线程任务 | 非并行任务 |

**依赖管理：**

```
Job A: 写 Velocity
  ↓ (A 写 Velocity)
Job B: 读 Velocity, 写 Position
  ↓ (B 写 Position)
Job C: 读 Position

框架自动推断:
  A → B → C (串行依赖)

如果:
Job A: 读 Position (只读)
Job B: 读 Position (只读)
  → A 和 B 可以并行
```

**数据竞争的避免：**

Job System 通过"读写标记"避免数据竞争：
- 多个 Job 同时只读同一数据 → 安全，可并行
- 一个 Job 写，其他 Job 读/写同一数据 → 不安全，必须串行

```csharp
// Job A 只读 Position
[ReadOnly] public NativeArray<float3> Positions;

// Job B 读写 Position
public NativeArray<float3> Positions;  // 无 ReadOnly，表示读写

// 框架会自动让 B 等待 A 完成
```

### 例子

**IJobEntity 示例（最常用）：**

```csharp
using Unity.Burst;
using Unity.Entities;
using Unity.Mathematics;

// 定义 Job
[BurstCompile]
public partial struct MovementJob : IJobEntity {
    public float DeltaTime;
    
    // Execute 方法的参数决定查询条件
    // ref = 读写, in = 只读
    void Execute(ref Position pos, in Velocity vel) {
        pos.Value += vel.Value * DeltaTime;
    }
}

// 在系统中调度 Job
public partial struct MovementSystem : ISystem {
    [BurstCompile]
    public void OnUpdate(ref SystemState state) {
        float dt = SystemAPI.Time.DeltaTime;
        
        // ScheduleParallel: 自动并行执行
        new MovementJob { DeltaTime = dt }.ScheduleParallel();
        
        // Schedule: 单线程执行（有跨实体依赖时用）
        // new MovementJob { DeltaTime = dt }.Schedule();
        
        // Run: 主线程同步执行（调试用）
        // new MovementJob { DeltaTime = dt }.Run();
    }
}
```

**IJobChunk 示例（更底层的控制）：**

```csharp
[BurstCompile]
public struct CollisionJob : IJobChunk {
    public float DeltaTime;
    public ComponentTypeHandle<Position> PositionHandle;
    [ReadOnly] public ComponentTypeHandle<Collider> ColliderHandle;
    
    public void Execute(in ArchetypeChunk chunk, int firstEntityIndex) {
        var positions = chunk.GetNativeArray(ref PositionHandle);
        var colliders = chunk.GetNativeArray(ref ColliderHandle);
        
        for (int i = 0; i < chunk.Count; i++) {
            // 处理每个实体
            var pos = positions[i];
            var col = colliders[i];
            // ...
        }
    }
}

public partial struct CollisionSystem : ISystem {
    private EntityQuery query;
    private ComponentTypeHandle<Position> positionHandle;
    private ComponentTypeHandle<Collider> colliderHandle;
    
    public void OnCreate(ref SystemState state) {
        query = state.GetEntityQuery(
            ComponentType.ReadWrite<Position>(),
            ComponentType.ReadOnly<Collider>()
        );
    }
    
    public void OnUpdate(ref SystemState state) {
        positionHandle = state.GetComponentTypeHandle<Position>();
        colliderHandle = state.GetComponentTypeHandle<Collider>(true);
        
        var job = new CollisionJob {
            DeltaTime = SystemAPI.Time.DeltaTime,
            PositionHandle = positionHandle,
            ColliderHandle = colliderHandle
        };
        
        // 传入 query，框架自动按 Chunk 分配到多线程
        state.Dependency = job.ScheduleParallel(query, state.Dependency);
    }
}
```

**IJobParallelFor 示例（自定义并行）：**

```csharp
[BurstCompile]
public struct PathfindingJob : IJobParallelFor {
    [ReadOnly] public NativeArray<float3> StartPositions;
    [ReadOnly] public NativeArray<float3> EndPositions;
    public NativeArray<float3> Results;
    
    public void Execute(int index) {
        // 为每个起点-终点对计算路径
        float3 start = StartPositions[index];
        float3 end = EndPositions[index];
        
        // 简单的直线寻路（实际会更复杂）
        Results[index] = math.lerp(start, end, 0.5f);
    }
}

// 调度
public void SchedulePathfinding() {
    int count = 1000;
    var job = new PathfindingJob {
        StartPositions = new NativeArray<float3>(count, Allocator.TempJob),
        EndPositions = new NativeArray<float3>(count, Allocator.TempJob),
        Results = new NativeArray<float3>(count, Allocator.TempJob),
    };
    
    // batchSize = 64: 每 64 个任务为一个批次
    JobHandle handle = job.Schedule(count, 64);
    handle.Complete();
    
    // 使用结果...
    
    // 释放 NativeArray
    job.StartPositions.Dispose();
    job.EndPositions.Dispose();
    job.Results.Dispose();
}
```

**多 Job 依赖链：**

```csharp
public partial struct CombatSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        float dt = SystemAPI.Time.DeltaTime;
        
        // Job 1: 更新速度
        var velocityJob = new VelocityUpdateJob { DeltaTime = dt };
        JobHandle velocityHandle = velocityJob.ScheduleParallel(state.Dependency);
        
        // Job 2: 更新位置（依赖 Job 1，因为读 Velocity）
        var movementJob = new MovementJob { DeltaTime = dt };
        JobHandle movementHandle = movementJob.ScheduleParallel(velocityHandle);
        
        // Job 3: 碰撞检测（依赖 Job 2，因为读 Position）
        var collisionJob = new CollisionJob { DeltaTime = dt };
        JobHandle collisionHandle = collisionJob.ScheduleParallel(movementHandle);
        
        // 设置最终依赖
        state.Dependency = collisionHandle;
        
        // 框架会在系统组结束时自动 Complete 所有 Job
    }
}
```

### 总结

**核心要点：**
- Job System 将工作拆分为多个 Job，自动调度到多核
- 框架根据读写标记自动管理 Job 间的依赖
- IJobEntity 最常用，自动按实体并行
- 只读访问可以让多个 Job 并行执行

**Schedule vs Run：**

| 方法 | 执行方式 | 适用场景 |
|------|----------|----------|
| ScheduleParallel | 多线程并行 | 默认选择，性能最佳 |
| Schedule | 单线程异步 | 有跨实体依赖时 |
| Run | 主线程同步 | 调试或需要立即结果 |

**常见注意事项：**
- 优先用 `ScheduleParallel`，性能最佳
- NativeArray 等 Native 容器用完必须 Dispose
- 避免在 Job 中访问托管对象（class），会破坏 Burst 优化
- Job 间有数据依赖时，框架会自动串行，无需手动同步

---

## 第 19 讲：内存碎片与 GC 规避

### 概念

**内存碎片** 和 **垃圾回收（GC）** 是 .NET 环境中性能的两大杀手。ECS 通过使用非托管内存（NativeArray 等）和值类型，从根本上规避了这两个问题。

理解 ECS 的内存管理策略，是写出高性能代码的关键。

### 原理

**GC 的问题：**

.NET 的垃圾回收器（GC）会定期扫描堆内存，回收不再使用的对象。这个过程会导致：
1. **暂停所有线程**（Stop-The-World）：GC 时游戏卡顿
2. **内存碎片**：频繁分配/释放导致堆内存碎片化
3. **缓存不友好**：堆上对象分散，缓存命中率低

```
传统 C# 游戏循环:
帧 1: 创建 100 个 Enemy 对象 → 堆分配
帧 2: 销毁 50 个 Enemy → 等待 GC
帧 3: GC 触发 → 暂停 50ms → 卡顿
帧 4: 创建 80 个 Enemy → 堆碎片化
...
```

**ECS 的解决方案：**

1. **非托管内存**：用 NativeArray/NativeList 替代 Array/List，手动管理内存
2. **值类型组件**：组件是 struct，分配在栈上或连续内存中，无 GC
3. **对象池**：实体和 Chunk 复用，避免频繁分配
4. **Arena 分配**：临时内存在帧结束时统一释放

```
ECS 游戏循环:
帧 1: 创建 100 个 Enemy → 从 Chunk 池分配（无 GC）
帧 2: 销毁 50 个 Enemy → 回收到 Chunk 池（无 GC）
帧 3: 无 GC 触发 → 无卡顿
帧 4: 创建 80 个 Enemy → 复用 Chunk（无分配）
...
```

**Native 容器：**

ECS 提供了一系列 Native 容器，使用非托管内存：

| 容器 | 说明 | 对应托管类型 |
|------|------|-------------|
| NativeArray<T> | 固定长度数组 | T[] |
| NativeList<T> | 动态长度数组 | List<T> |
| NativeHashMap<K,V> | 哈希表 | Dictionary<K,V> |
| NativeMultiHashMap<K,V> | 多值哈希表 | Dictionary<K, List<V>> |
| NativeQueue<T> | 队列 | Queue<T> |
| NativeReference<T> | 单值引用 | ref T |

这些容器：
- 分配在非托管堆上，不受 GC 管理
- 内存连续，缓存友好
- 必须手动 Dispose，否则内存泄漏
- 可以在 Job 中使用（线程安全）

### 例子

**GC 问题的演示：**

```csharp
// === 传统 C# 代码（有 GC 问题）===
public class EnemySpawner : MonoBehaviour {
    void Update() {
        // 每帧创建对象 → GC 压力
        for (int i = 0; i < 100; i++) {
            var enemy = new Enemy();  // 堆分配
            enemy.Position = Random.insideUnitSphere * 10;
        }
        
        // 每帧创建闭包 → GC 压力
        enemies.FindAll(e => e.Health > 0);  // Lambda 闭包
        
        // 每帧创建数组 → GC 压力
        var nearby = Physics.OverlapSphere(pos, 5f);  // 返回新数组
    }
}

// === ECS 代码（无 GC）===
public partial struct EnemySpawnSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        var ecb = new EntityCommandBuffer(Allocator.TempJob);
        
        // 实体创建无 GC（从 Chunk 池分配）
        for (int i = 0; i < 100; i++) {
            Entity e = ecb.CreateEntity();
            ecb.AddComponent(e, new Position { 
                Value = Random.insideUnitSphere() * 10  // 无 GC
            });
        }
        
        ecb.Playback(state.EntityManager);
        ecb.Dispose();  // 手动释放
    }
}
```

**Native 容器的使用：**

```csharp
using Unity.Collections;
using Unity.Collections.LowLevel.Unsafe;

// === NativeArray ===
public partial struct MovementSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        // 临时分配（帧结束自动释放）
        var tempArray = new NativeArray<float3>(1000, Allocator.Temp);
        
        // 临时 Job 分配（Job 完成后手动释放）
        var jobArray = new NativeArray<float3>(1000, Allocator.TempJob);
        
        // 持久分配（手动管理生命周期）
        var persistentArray = new NativeArray<float3>(1000, Allocator.Persistent);
        
        // 使用...
        for (int i = 0; i < tempArray.Length; i++) {
            tempArray[i] = new float3(i, 0, 0);
        }
        
        // 释放
        tempArray.Dispose();  // Temp 可以不显式释放，但建议释放
        jobArray.Dispose();
        persistentArray.Dispose();  // Persistent 必须显式释放
    }
}

// === NativeList ===
public partial struct PathfindingSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        var path = new NativeList<float3>(Allocator.Temp);
        
        // 动态添加
        path.Add(new float3(0, 0, 0));
        path.Add(new float3(1, 0, 0));
        path.Add(new float3(2, 0, 0));
        
        // 访问
        for (int i = 0; i < path.Length; i++) {
            // path[i] ...
        }
        
        path.Dispose();
    }
}

// === NativeHashMap ===
public partial struct SpatialHashSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        var grid = new NativeHashMap<int, Entity>(1000, Allocator.Temp);
        
        // 添加
        grid.TryAdd(GetCellHash(pos), entity);
        
        // 查询
        if (grid.TryGetValue(GetCellHash(pos), out Entity e)) {
            // 找到
        }
        
        grid.Dispose();
    }
    
    int GetCellHash(float3 pos) {
        return (int)(pos.x + pos.y * 1000 + pos.z * 1000000);
    }
}
```

**Allocator 的选择：**

```csharp
// Allocator.Temp: 最快，帧结束自动释放，单线程
var temp = new NativeArray<int>(100, Allocator.Temp);

// Allocator.TempJob: 临时 Job 用，4帧内必须释放，可多线程
var tempJob = new NativeArray<int>(100, Allocator.TempJob);

// Allocator.Persistent: 持久存在，必须手动释放，可多线程
var persistent = new NativeArray<int>(100, Allocator.Persistent);

// 选择建议:
// - 单帧临时数据 → Temp
// - Job 中的临时数据 → TempJob
// - 跨帧持久数据 → Persistent
```

**对象池模式（避免频繁创建/销毁）：**

```csharp
// 实体对象池：复用实体而非销毁
public partial struct BulletPoolSystem : ISystem {
    private EntityQuery inactiveBullets;
    
    public void OnCreate(ref SystemState state) {
        // 查询所有"未激活"的子弹
        inactiveBullets = state.GetEntityQuery(
            typeof(BulletTag),
            typeof(InactiveTag)
        );
    }
    
    public void OnUpdate(ref SystemState state) {
        var ecb = new EntityCommandBuffer(Allocator.Temp);
        
        // 发射子弹时：优先从池中取
        if (inactiveBullets.CalculateEntityCount() > 0) {
            var entities = inactiveBullets.ToEntityArray(Allocator.Temp);
            Entity bullet = entities[0];
            
            // 激活子弹
            ecb.RemoveComponent<InactiveTag>(bullet);
            ecb.SetComponent(bullet, new Position { Value = spawnPos });
            ecb.SetComponent(bullet, new Velocity { Value = direction * speed });
            
            entities.Dispose();
        } else {
            // 池空了才创建新子弹
            Entity bullet = ecb.CreateEntity();
            ecb.AddComponent(bullet, new BulletTag());
            ecb.AddComponent(bullet, new Position { Value = spawnPos });
            ecb.AddComponent(bullet, new Velocity { Value = direction * speed });
        }
        
        ecb.Playback(state.EntityManager);
        ecb.Dispose();
    }
}

// 子弹"销毁"时：不真正销毁，而是标记为未激活
public partial struct BulletLifetimeSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        var ecb = new EntityCommandBuffer(Allocator.Temp);
        
        foreach (var (lifetime, entity) in 
                 SystemAPI.Query<RefRW<Lifetime>>().WithEntityAccess()) {
            lifetime.ValueRW.Remaining -= SystemAPI.Time.DeltaTime;
            if (lifetime.ValueRO.Remaining <= 0) {
                // 不销毁，标记为未激活
                ecb.AddComponent<InactiveTag>(entity);
            }
        }
        
        ecb.Playback(state.EntityManager);
        ecb.Dispose();
    }
}
```

### 总结

**核心要点：**
- ECS 通过非托管内存和值类型，从根本上规避 GC
- Native 容器（NativeArray、NativeList 等）使用非托管内存，无 GC 压力
- Native 容器必须手动 Dispose，否则内存泄漏
- 对象池模式可以避免频繁创建/销毁实体

**Allocator 选择：**

| Allocator | 生命周期 | 线程安全 | 适用场景 |
|-----------|----------|----------|----------|
| Temp | 单帧 | 单线程 | 临时数据 |
| TempJob | 4帧内 | 多线程 | Job 临时数据 |
| Persistent | 手动释放 | 多线程 | 持久数据 |

**常见注意事项：**
- 所有 Native 容器用完必须 Dispose，否则内存泄漏
- 避免在 ECS 代码中使用 class、string、List 等引用类型
- 用 FixedString 替代 string，用 NativeList 替代 List
- 频繁创建/销毁的实体用对象池，而非真正销毁

---

# 第六章：进阶主题

> 本章目标：拓展 ECS 架构的边界，掌握共享组件、系统分组、序列化等进阶技术。

---

## 第 20 讲：共享组件与单例

### 概念

**共享组件（Shared Component）** 是一种特殊的组件，多个实体可以共享同一个组件实例。当实体的共享组件值相同时，它们会被分组到同一个 Chunk 中，便于批量处理和过滤。

**单例（Singleton）** 是全局唯一的组件实例，用于存储全局配置、游戏状态等数据。单例组件只有一个实体持有，方便所有系统访问。

### 原理

**共享组件的工作机制：**

普通组件：每个实体有自己的组件数据副本
```
实体0: Position{0,0,0}
实体1: Position{1,0,0}
实体2: Position{2,0,0}
```

共享组件：相同值的实体共享一个实例
```
实体0, 实体1, 实体2 → 共享 Mesh{Cube}（同一个实例）
实体3, 实体4       → 共享 Mesh{Sphere}（另一个实例）
```

**共享组件对 Archetype 的影响：**

共享组件会进一步细分 Archetype。相同组件组合但不同共享值的实体会分到不同 Chunk：

```
Archetype {Position, Mesh(共享)}:
  Chunk 0: Mesh=Cube    → [实体0, 实体1, 实体2]
  Chunk 1: Mesh=Sphere  → [实体3, 实体4]
  Chunk 2: Mesh=Plane   → [实体5, 实体6, 实体7]
```

**共享组件的优势：**

1. **内存节省**：相同值只存一份
2. **批量处理**：按共享值分组，可以批量渲染（如所有 Cube 一起画）
3. **快速过滤**：查询时可以按共享值过滤

**共享组件的劣势：**

1. **结构变更代价**：修改共享值会导致实体移动到新 Chunk
2. **Archetype 碎片化**：共享值种类多时，Chunk 数量爆炸
3. **不能在 Job 中访问**：共享组件是引用类型，不能在 Burst Job 中使用

**单例的工作机制：**

单例组件是全局唯一的，通常用一个特殊实体持有：

```
World
├── 普通实体 (Player, Enemy, ...)
├── 单例实体 (持有 GameConfig 组件)
├── 单例实体 (持有 GameState 组件)
└── 单例实体 (持有 InputState 组件)
```

系统通过 API 快速访问单例：
```csharp
var config = SystemAPI.GetSingleton<GameConfig>();
```

### 例子

**共享组件示例：**

```csharp
using Unity.Entities;
using Unity.Rendering;

// === 定义共享组件 ===
public struct EnemyType : ISharedComponentData {
    public int TypeId;  // 0=小兵, 1=精英, 2=Boss
}

public struct MeshRenderer : ISharedComponentData {
    public Mesh Mesh;
    public Material Material;
}

// === 使用共享组件 ===
public partial struct SpawnSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        var ecb = new EntityCommandBuffer(Allocator.Temp);
        
        // 创建小兵（共享 EnemyType=0）
        for (int i = 0; i < 100; i++) {
            Entity e = ecb.CreateEntity();
            ecb.AddComponent(e, new Position { Value = new float3(i, 0, 0) });
            ecb.AddSharedComponent(e, new EnemyType { TypeId = 0 });
        }
        
        // 创建精英（共享 EnemyType=1）
        for (int i = 0; i < 10; i++) {
            Entity e = ecb.CreateEntity();
            ecb.AddComponent(e, new Position { Value = new float3(i, 1, 0) });
            ecb.AddSharedComponent(e, new EnemyType { TypeId = 1 });
        }
        
        ecb.Playback(state.EntityManager);
        ecb.Dispose();
    }
}

// === 按共享组件过滤查询 ===
public partial struct EliteAISystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        // 只处理精英怪
        var query = state.GetEntityQuery(
            typeof(Position),
            typeof(EnemyType)
        );
        query.SetSharedComponentFilter(new EnemyType { TypeId = 1 });
        
        int eliteCount = query.CalculateEntityCount();
        UnityEngine.Debug.Log($"精英怪数量: {eliteCount}");
        
        // 遍历精英怪
        foreach (var pos in query.ToComponentDataArray<Position>(Allocator.Temp)) {
            // 处理精英怪 AI
        }
    }
}

// === 批量渲染（共享组件的核心用途）===
public partial struct RenderSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        // 按共享组件分组渲染
        var query = state.GetEntityQuery(
            typeof(Position),
            typeof(MeshRenderer)
        );
        
        // 获取所有不同的 MeshRenderer 值
        var sharedRenderers = query.GetSharedComponentFilter<Position, MeshRenderer>();
        
        foreach (var renderer in sharedRenderers) {
            // 设置过滤条件
            query.SetSharedComponentFilter(renderer);
            
            // 获取所有使用该 MeshRenderer 的实体位置
            var positions = query.ToComponentDataArray<Position>(Allocator.Temp);
            
            // 一次性渲染所有相同 Mesh+Material 的实体（GPU Instancing）
            Graphics.DrawMeshInstanced(
                renderer.Mesh,
                0,
                renderer.Material,
                positions.Reinterpret<Matrix4x4>()
            );
            
            positions.Dispose();
        }
    }
}
```

**单例组件示例：**

```csharp
// === 定义单例组件 ===
public struct GameConfig : IComponentData {
    public float EnemySpawnRate;
    public int MaxEnemies;
    public float DifficultyMultiplier;
}

public struct GameState : IComponentData {
    public int Score;
    public float TimeRemaining;
    public bool IsGameOver;
}

public struct InputState : IComponentData {
    public float2 MoveInput;
    public bool FirePressed;
    public bool JumpPressed;
}

// === 创建单例 ===
public partial struct GameInitSystem : ISystem {
    public void OnCreate(ref SystemState state) {
        // 创建单例实体
        Entity configEntity = state.EntityManager.CreateEntity();
        state.EntityManager.AddComponentData(configEntity, new GameConfig {
            EnemySpawnRate = 1.0f,
            MaxEnemies = 100,
            DifficultyMultiplier = 1.0f
        });
        
        Entity stateEntity = state.EntityManager.CreateEntity();
        state.EntityManager.AddComponentData(stateEntity, new GameState {
            Score = 0,
            TimeRemaining = 300f,
            IsGameOver = false
        });
        
        Entity inputEntity = state.EntityManager.CreateEntity();
        state.EntityManager.AddComponentData(inputEntity, new InputState {
            MoveInput = float2.zero,
            FirePressed = false,
            JumpPressed = false
        });
    }
}

// === 访问单例 ===
public partial struct SpawnSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        // 读取单例
        var config = SystemAPI.GetSingleton<GameConfig>();
        var gameState = SystemAPI.GetSingleton<GameState>();
        
        if (gameState.IsGameOver) return;
        
        // 使用配置
        if (UnityEngine.Random.value < config.EnemySpawnRate * SystemAPI.Time.DeltaTime) {
            // 生成敌人
        }
    }
}

// === 修改单例 ===
public partial struct InputSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        // 修改单例
        SystemAPI.SetSingleton(new InputState {
            MoveInput = new float2(
                UnityEngine.Input.GetAxis("Horizontal"),
                UnityEngine.Input.GetAxis("Vertical")
            ),
            FirePressed = UnityEngine.Input.GetButtonDown("Fire1"),
            JumpPressed = UnityEngine.Input.GetButtonDown("Jump")
        });
    }
}

// === 直接引用单例（避免频繁查询）===
public partial struct ScoreSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        // 获取单例的可写引用
        ref var gameState = ref SystemAPI.GetSingletonRW<GameState>().ValueRW;
        
        // 直接修改
        gameState.TimeRemaining -= SystemAPI.Time.DeltaTime;
        if (gameState.TimeRemaining <= 0) {
            gameState.IsGameOver = true;
        }
    }
}
```

### 总结

**核心要点：**
- 共享组件：相同值的实体共享一个实例，按值分组 Chunk
- 共享组件用途：内存节省、批量渲染、快速过滤
- 单例组件：全局唯一的组件，用于配置和状态
- 单例访问：`SystemAPI.GetSingleton<T>()` / `SystemAPI.SetSingleton<T>()`

**共享组件 vs 普通组件：**

| 维度 | 普通组件 | 共享组件 |
|------|----------|----------|
| 存储 | 每实体一份 | 相同值共享 |
| 内存 | 较多 | 较少 |
| Job 访问 | 支持 | 不支持（引用类型）|
| 修改代价 | 低 | 高（移动 Chunk）|
| 过滤 | 按组件有无 | 按值 |

**常见注意事项：**
- 共享组件的值种类不宜过多（会导致 Chunk 碎片化）
- 不要在 Burst Job 中访问共享组件（会破坏编译）
- 单例组件要确保只有一个实体持有（创建时检查）
- 单例适合全局配置和状态，不适合频繁变化的数据

---

## 第 21 讲：系统分组与标签

### 概念

**系统分组（System Group）** 是将多个相关系统组织成一个逻辑单元的机制。系统组可以嵌套，形成树形结构，便于管理复杂项目的系统执行顺序。

**标签（Tag）** 是无数据的组件，用于标记实体的类别或状态。标签组件不占用实际内存（仅占一个位），是 ECS 中最高效的过滤手段。

### 原理

**系统组的层级结构：**

```
RootSystemGroup (根组)
├── InitializationSystemGroup (初始化组)
│   ├── BeginInitializationECBSystem
│   ├── SpawnSystem
│   └── EndInitializationECBSystem
├── SimulationSystemGroup (模拟组)
│   ├── BeginSimulationECBSystem
│   ├── CombatSystemGroup (战斗子组)
│   │   ├── AttackSystem
│   │   ├── DamageSystem
│   │   └── DeathSystem
│   ├── MovementSystemGroup (移动子组)
│   │   ├── PathfindingSystem
│   │   ├── MovementSystem
│   │   └── CollisionSystem
│   └── EndSimulationECBSystem
└── PresentationSystemGroup (表现组)
    ├── RenderSystem
    └── AudioSystem
```

**系统组的作用：**

1. **批量管理**：启用/禁用一组系统
2. **顺序控制**：组内系统按顺序执行
3. **逻辑分组**：按功能领域组织系统
4. **性能优化**：可以按组跳过更新

**标签组件的原理：**

标签组件是无数据的 IComponentData：

```csharp
public struct PlayerTag : IComponentData { }  // 无字段
public struct EnemyTag : IComponentData { }   // 无字段
public struct DeadTag : IComponentData { }    // 无字段
```

标签在 Archetype 中只占一个位（bitset 的一位），不占用 Chunk 内存。查询时用标签过滤极快。

**标签 vs 数据组件：**

```
数据组件 Health { current, max }:
  - 占用 8 字节/实体
  - 可以存储和修改数据
  - 查询时需要加载

标签组件 DeadTag:
  - 占用 0 字节（仅 Archetype 位图）
  - 只表示"有"或"无"
  - 查询时无需加载
```

### 例子

**自定义系统组：**

```csharp
using Unity.Entities;

// === 定义自定义系统组 ===
[UpdateInGroup(typeof(SimulationSystemGroup))]
public partial struct CombatSystemGroup : ComponentSystemGroup {
    // 系统组本身不需要实现逻辑，只需声明
}

// === 将系统加入组 ===
[UpdateInGroup(typeof(CombatSystemGroup))]
[UpdateBefore(typeof(DamageSystem))]
public partial struct AttackSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        // 攻击逻辑
    }
}

[UpdateInGroup(typeof(CombatSystemGroup))]
[UpdateAfter(typeof(AttackSystem))]
public partial struct DamageSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        // 伤害计算
    }
}

[UpdateInGroup(typeof(CombatSystemGroup))]
[UpdateAfter(typeof(DamageSystem))]
public partial struct DeathSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        // 死亡处理
    }
}

// === 嵌套系统组 ===
[UpdateInGroup(typeof(CombatSystemGroup))]
public partial struct AISystemGroup : ComponentSystemGroup { }

[UpdateInGroup(typeof(AISystemGroup))]
public partial struct AIDecisionSystem : ISystem { }

[UpdateInGroup(typeof(AISystemGroup))]
[UpdateAfter(typeof(AIDecisionSystem))]
public partial struct AIActionSystem : ISystem { }
```

**标签组件的使用：**

```csharp
// === 定义标签 ===
public struct PlayerTag : IComponentData { }
public struct EnemyTag : IComponentData { }
public struct NPC : IComponentData { }
public struct DeadTag : IComponentData { }
public struct InvincibleTag : IComponentData { }

// === 用标签创建实体 ===
public partial struct SpawnSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        var ecb = new EntityCommandBuffer(Allocator.Temp);
        
        // 创建玩家
        Entity player = ecb.CreateEntity();
        ecb.AddComponent(player, new Position { Value = float3.zero });
        ecb.AddComponent(player, new Health { Current = 100, Max = 100 });
        ecb.AddComponent<PlayerTag>(player);  // 标签
        
        // 创建敌人
        for (int i = 0; i < 50; i++) {
            Entity enemy = ecb.CreateEntity();
            ecb.AddComponent(enemy, new Position { Value = new float3(i, 0, 0) });
            ecb.AddComponent(enemy, new Health { Current = 50, Max = 50 });
            ecb.AddComponent<EnemyTag>(enemy);  // 标签
        }
        
        ecb.Playback(state.EntityManager);
        ecb.Dispose();
    }
}

// === 用标签过滤查询 ===
public partial struct PlayerMovementSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        float dt = SystemAPI.Time.DeltaTime;
        
        // 只处理玩家（有 PlayerTag）
        foreach (var (pos, vel) in 
                 SystemAPI.Query<RefRW<Position>, RefRO<Velocity>>()
                          .WithAll<PlayerTag>()) {
            pos.ValueRW.Value += vel.ValueRO.Value * dt;
        }
    }
}

public partial struct EnemyAISystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        // 只处理活着的敌人（有 EnemyTag，没有 DeadTag）
        foreach (var (pos, entity) in 
                 SystemAPI.Query<RefRO<Position>>()
                          .WithAll<EnemyTag>()
                          .WithNone<DeadTag>()
                          .WithEntityAccess()) {
            // AI 逻辑
        }
    }
}

// === 用标签表示状态 ===
public partial struct DeathSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        var ecb = new EntityCommandBuffer(Allocator.Temp);
        
        // 血量归零的实体添加 DeadTag
        foreach (var (health, entity) in 
                 SystemAPI.Query<RefRO<Health>>()
                          .WithNone<DeadTag>()  // 还没死的
                          .WithEntityAccess()) {
            if (health.ValueRO.Current <= 0) {
                ecb.AddComponent<DeadTag>(entity);  // 标记为死亡
            }
        }
        
        ecb.Playback(state.EntityManager);
        ecb.Dispose();
    }
}

// === 排除已死亡实体的系统 ===
public partial struct CombatSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        // 只处理活着的实体
        foreach (var (health, pos) in 
                 SystemAPI.Query<RefRW<Health>, RefRO<Position>>()
                          .WithNone<DeadTag, InvincibleTag>()) {
            // 战斗逻辑（排除死亡和无敌的）
        }
    }
}
```

**系统组的动态控制：**

```csharp
public partial struct GameManager : ISystem {
    public void OnUpdate(ref SystemState state) {
        var gameState = SystemAPI.GetSingleton<GameState>();
        
        // 游戏结束时禁用战斗系统组
        var combatGroup = state.World.GetExistingSystemManaged<CombatSystemGroup>();
        if (gameState.IsGameOver) {
            combatGroup.Enabled = false;  // 禁用整个组
        } else {
            combatGroup.Enabled = true;
        }
    }
}
```

### 总结

**核心要点：**
- 系统组将相关系统组织成树形结构，便于管理
- 系统组可以嵌套，支持复杂的项目结构
- 标签组件无数据，仅占 Archetype 位图一位，查询极快
- 标签用于标记类别（Player/Enemy）和状态（Dead/Invincible）

**常见注意事项：**
- 系统组本身不实现逻辑，只负责组织子系统的执行顺序
- 标签组件不要滥用，每个标签都会增加 Archetype 数量
- 用 `WithAll<T>()` / `WithNone<T>()` 过滤标签，比用数据组件过滤快
- 系统组可以动态启用/禁用，用于暂停游戏等场景

---

## 第 22 讲：序列化与存档

### 概念

**序列化（Serialization）** 是将 ECS 数据转换为可存储或传输格式的过程。序列化用于：
- **存档/读档**：保存和恢复游戏进度
- **网络同步**：多人游戏中同步实体状态
- **预制体**：保存实体模板，运行时实例化
- **调试**：导出当前 World 状态用于分析

ECS 的序列化比传统 OOP 更简单：因为组件是纯数据，无需处理对象引用、虚函数等复杂情况。

### 原理

**ECS 序列化的层次：**

```
1. 组件序列化：将单个组件数据转为字节流
2. 实体序列化：将实体的所有组件打包
3. Archetype 序列化：记录实体的组件类型组合
4. World 序列化：序列化所有实体和全局状态
```

**序列化的挑战：**

1. **实体引用**：组件中可能存储 Entity 引用，反序列化后需要重映射
2. **共享组件**：共享组件需要去重，避免重复存储
3. **动态缓冲区**：DynamicBuffer 长度可变，需要特殊处理
4. **Blob 资产**：BlobAsset 是不可变数据，需要单独序列化

**实体引用的重映射：**

```
序列化前:
  实体 A (Index=0) 的 Target 组件引用实体 B (Index=1)
  实体 B (Index=1) 的 Owner 组件引用实体 A (Index=0)

序列化（存储相对关系）:
  实体 A: Target → 实体 B
  实体 B: Owner → 实体 A

反序列化（重新分配 Index）:
  实体 A (Index=5) 的 Target → 实体 B (Index=6)
  实体 B (Index=6) 的 Owner → 实体 A (Index=5)
  
  框架自动重映射引用
```

### 例子

**Unity DOTS 中的序列化：**

```csharp
using Unity.Entities;
using Unity.Entities.Serialization;

// === 使用 EntitySerializer 序列化 World ===
public class SaveLoadSystem {
    public void SaveWorld(World world, string path) {
        // 创建序列化器
        var serializer = new StreamBinaryWriter(path);
        
        // 序列化所有实体
        EntitySerializer.SerializeWorld(
            serializer,
            world.EntityManager,
            // 可以指定要序列化的实体（默认全部）
            world.EntityManager.GetAllEntities()
        );
        
        serializer.Dispose();
    }
    
    public void LoadWorld(World world, string path) {
        var deserializer = new StreamBinaryReader(path);
        
        // 反序列化到 World
        EntitySerializer.DeserializeWorld(
            deserializer,
            world.EntityManager
        );
        
        deserializer.Dispose();
    }
}

// === 手动序列化（更灵活）===
public partial struct ManualSaveSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        if (!UnityEngine.Input.GetKeyDown(KeyCode.F5)) return;
        
        SaveGame(state.EntityManager);
    }
    
    void SaveGame(EntityManager em) {
        using var writer = new System.IO.BinaryWriter(
            System.IO.File.Create("save.dat")
        );
        
        // 1. 保存玩家数据
        var playerQuery = em.CreateEntityQuery(typeof(PlayerTag), typeof(Health), typeof(Position));
        var playerEntities = playerQuery.ToEntityArray(Allocator.Temp);
        
        writer.Write(playerEntities.Length);  // 玩家数量
        foreach (var entity in playerEntities) {
            var health = em.GetComponentData<Health>(entity);
            var pos = em.GetComponentData<Position>(entity);
            
            writer.Write(health.Current);
            writer.Write(health.Max);
            writer.Write(pos.Value.x);
            writer.Write(pos.Value.y);
            writer.Write(pos.Value.z);
        }
        
        // 2. 保存敌人数据
        var enemyQuery = em.CreateEntityQuery(typeof(EnemyTag), typeof(Health), typeof(Position));
        var enemyEntities = enemyQuery.ToEntityArray(Allocator.Temp);
        
        writer.Write(enemyEntities.Length);
        foreach (var entity in enemyEntities) {
            var health = em.GetComponentData<Health>(entity);
            var pos = em.GetComponentData<Position>(entity);
            
            writer.Write(health.Current);
            writer.Write(health.Max);
            writer.Write(pos.Value.x);
            writer.Write(pos.Value.y);
            writer.Write(pos.Value.z);
        }
        
        playerEntities.Dispose();
        enemyEntities.Dispose();
    }
}

// === 手动反序列化 ===
public partial struct ManualLoadSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        if (!UnityEngine.Input.GetKeyDown(KeyCode.F9)) return;
        
        LoadGame(state.EntityManager, state);
    }
    
    void LoadGame(EntityManager em, SystemState state) {
        // 清空当前 World
        em.DestroyEntity(em.GetAllEntities());
        
        using var reader = new System.IO.BinaryReader(
            System.IO.File.OpenRead("save.dat")
        );
        
        var ecb = new EntityCommandBuffer(Allocator.Temp);
        
        // 1. 读取玩家
        int playerCount = reader.ReadInt32();
        for (int i = 0; i < playerCount; i++) {
            Entity e = ecb.CreateEntity();
            ecb.AddComponent<PlayerTag>(e);
            ecb.AddComponent(e, new Health {
                Current = reader.ReadSingle(),
                Max = reader.ReadSingle()
            });
            ecb.AddComponent(e, new Position {
                Value = new float3(
                    reader.ReadSingle(),
                    reader.ReadSingle(),
                    reader.ReadSingle()
                )
            });
        }
        
        // 2. 读取敌人
        int enemyCount = reader.ReadInt32();
        for (int i = 0; i < enemyCount; i++) {
            Entity e = ecb.CreateEntity();
            ecb.AddComponent<EnemyTag>(e);
            ecb.AddComponent(e, new Health {
                Current = reader.ReadSingle(),
                Max = reader.ReadSingle()
            });
            ecb.AddComponent(e, new Position {
                Value = new float3(
                    reader.ReadSingle(),
                    reader.ReadSingle(),
                    reader.ReadSingle()
                )
            });
        }
        
        ecb.Playback(em);
        ecb.Dispose();
    }
}
```

**预制体（Prefab）序列化：**

```csharp
// === 创建预制体 ===
public partial struct PrefabInitSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        // 创建预制体实体（标记为 Prefab）
        Entity enemyPrefab = state.EntityManager.CreateEntity();
        state.EntityManager.AddComponentData(enemyPrefab, new Prefab());  // Prefab 标签
        state.EntityManager.AddComponentData(enemyPrefab, new Position { Value = float3.zero });
        state.EntityManager.AddComponentData(enemyPrefab, new Health { Current = 50, Max = 50 });
        state.EntityManager.AddComponentData(enemyPrefab, new EnemyTag());
        
        // 保存预制体到单例，供其他系统使用
        Entity prefabHolder = state.EntityManager.CreateEntity();
        state.EntityManager.AddComponentData(prefabHolder, new EnemyPrefabRef { Value = enemyPrefab });
    }
}

public struct EnemyPrefabRef : IComponentData {
    public Entity Value;
}

// === 使用预制体实例化 ===
public partial struct SpawnSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        if (!SystemAPI.HasSingleton<EnemyPrefabRef>()) return;
        
        Entity prefab = SystemAPI.GetSingleton<EnemyPrefabRef>().Value;
        var ecb = new EntityCommandBuffer(Allocator.Temp);
        
        // 实例化预制体（自动复制所有组件）
        for (int i = 0; i < 10; i++) {
            Entity enemy = ecb.Instantiate(prefab);
            ecb.SetComponent(enemy, new Position { 
                Value = new float3(i, 0, 0) 
            });
        }
        
        ecb.Playback(state.EntityManager);
        ecb.Dispose();
    }
}
```

**Bevy 中的序列化：**

```rust
use bevy_ecs::prelude::*;
use serde::{Serialize, Deserialize};

// 组件需要实现 Serialize/Deserialize
#[derive(Component, Serialize, Deserialize)]
struct Position { x: f32, y: f32, z: f32 }

#[derive(Component, Serialize, Deserialize)]
struct Health { current: f32, max: f32 }

// 序列化 World
fn save_world(world: &World, path: &str) {
    let mut query = world.query::<(&Position, &Health)>();
    let data: Vec<_> = query.iter(world).collect();
    
    let json = serde_json::to_string(&data).unwrap();
    std::fs::write(path, json).unwrap();
}

// 反序列化 World
fn load_world(world: &mut World, path: &str) {
    let json = std::fs::read_to_string(path).unwrap();
    let data: Vec<(Position, Health)> = serde_json::from_str(&json).unwrap();
    
    for (pos, health) in data {
        world.spawn((pos, health));
    }
}
```

### 总结

**核心要点：**
- ECS 序列化比 OOP 简单，因为组件是纯数据
- 序列化层次：组件 → 实体 → Archetype → World
- 实体引用需要重映射，框架通常自动处理
- 预制体用 Prefab 标签标记，实例化时自动复制组件

**常见注意事项：**
- 序列化时排除 Prefab 实体（它们是模板，不是运行时数据）
- 共享组件序列化时要去重，避免重复存储
- DynamicBuffer 需要特殊处理（存储长度 + 数据）
- 网络同步只需序列化变化的数据（增量同步），而非全量

---

# 第七章：实战应用

> 本章目标：将所学知识应用到真实项目，掌握 Unity DOTS 实战、ECS 游戏案例剖析和最佳实践。

---

## 第 23 讲：Unity DOTS 实战入门

### 概念

**Unity DOTS（Data-Oriented Technology Stack）** 是 Unity 官方推出的数据导向技术栈，包含：
- **Entities**：ECS 框架核心
- **Burst**：高性能编译器
- **Jobs**：多线程任务系统
- **Collections**：Native 容器
- **Mathematics**：数学库

本讲通过一个完整的"弹幕游戏"示例，演示如何从零搭建 Unity DOTS 项目。

### 原理

**Unity DOTS 的项目结构：**

```
项目
├── Assets/
│   ├── Scripts/
│   │   ├── Components/     # 组件定义
│   │   ├── Systems/        # 系统实现
│   │   ├── Jobs/           # Job 定义
│   │   ├── Authoring/      # 转换脚本（MonoBehaviour → Entity）
│   │   └── Main/           # 入口和配置
│   └── Prefabs/            # 预制体
└── Packages/
    └── manifest.json       # 依赖包
```

**Authoring 工作流：**

Unity 是面向对象的游戏引擎，场景中的 GameObject 需要转换为 Entity 才能在 ECS 中使用。这个过程称为"烘焙（Baking）"：

```
设计阶段:
  GameObject + Authoring 脚本 → 定义组件映射

运行时（场景加载时）:
  SubScene → Baking → Entity
  GameObject 被转换为 Entity，组件被映射
```

**DOTS 的性能优势来源：**

1. **Burst 编译**：C# → 优化机器码（10-50 倍提升）
2. **多线程 Job**：自动并行化（N 倍提升，N=核心数）
3. **缓存友好**：SoA 布局（5-10 倍提升）
4. **无 GC**：非托管内存（避免卡顿）

综合下来，DOTS 比传统 MonoBehaviour 性能高 50-100 倍。

### 例子

**完整示例：弹幕游戏**

**1. 组件定义：**

```csharp
// Assets/Scripts/Components/Components.cs
using Unity.Entities;
using Unity.Mathematics;

// 玩家标签
public struct PlayerTag : IComponentData { }

// 敌人标签
public struct EnemyTag : IComponentData { }

// 子弹标签
public struct BulletTag : IComponentData { }

// 位置（Unity 内置 LocalTransform 可替代，这里演示自定义）
public struct Position : IComponentData {
    public float3 Value;
}

// 速度
public struct Velocity : IComponentData {
    public float3 Value;
}

// 生命值
public struct Health : IComponentData {
    public float Current;
    public float Max;
}

// 子弹生命周期
public struct BulletLifetime : IComponentData {
    public float Remaining;
}

// 伤害值
public struct Damage : IComponentData {
    public float Amount;
}

// 射击冷却
public struct FireCooldown : IComponentData {
    public float Remaining;
}

// 射击配置（单例）
public struct FireConfig : IComponentData {
    public float FireRate;      // 每秒射击次数
    public float BulletSpeed;
    public float BulletLifetime;
}

// 游戏状态（单例）
public struct GameState : IComponentData {
    public int Score;
    public bool IsGameOver;
}
```

**2. Authoring 脚本（GameObject → Entity 转换）：**

```csharp
// Assets/Scripts/Authoring/PlayerAuthoring.cs
using Unity.Entities;
using UnityEngine;

// MonoBehaviour 用于在编辑器中配置
public class PlayerAuthoring : MonoBehaviour {
    public float moveSpeed = 5f;
    public float maxHealth = 100f;
    public float fireRate = 5f;
    public float bulletSpeed = 20f;
    public float bulletLifetime = 2f;
}

// Baker：将 MonoBehaviour 数据转换为 Entity 组件
public class PlayerBaker : Baker<PlayerAuthoring> {
    public override void Bake(PlayerAuthoring authoring) {
        var entity = GetEntity(TransformUsageFlags.Dynamic);
        
        // 添加组件
        AddComponent(entity, new PlayerTag());
        AddComponent(entity, new Position { Value = authoring.transform.position });
        AddComponent(entity, new Velocity { Value = float3.zero });
        AddComponent(entity, new Health { 
            Current = authoring.maxHealth, 
            Max = authoring.maxHealth 
        });
        AddComponent(entity, new FireCooldown { Remaining = 0 });
        
        // 设置射击配置（单例）
        var configEntity = CreateAdditionalEntity(TransformUsageFlags.None);
        AddComponent(configEntity, new FireConfig {
            FireRate = authoring.fireRate,
            BulletSpeed = authoring.bulletSpeed,
            BulletLifetime = authoring.bulletLifetime
        });
    }
}
```

**3. 系统实现：**

```csharp
// Assets/Scripts/Systems/PlayerMovementSystem.cs
using Unity.Burst;
using Unity.Entities;
using Unity.Mathematics;
using UnityEngine;

[BurstCompile]
[UpdateInGroup(typeof(SimulationSystemGroup))]
public partial struct PlayerMovementSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        float dt = SystemAPI.Time.DeltaTime;
        
        // 读取输入
        float2 input = new float2(
            Input.GetAxis("Horizontal"),
            Input.GetAxis("Vertical")
        );
        
        // 移动玩家
        foreach (var (pos, vel) in 
                 SystemAPI.Query<RefRW<Position>, RefRW<Velocity>>()
                          .WithAll<PlayerTag>()) {
            vel.ValueRW.Value = new float3(input.x, 0, input.y) * 5f;
            pos.ValueRW.Value += vel.ValueRO.Value * dt;
        }
    }
}

// Assets/Scripts/Systems/PlayerFireSystem.cs
[BurstCompile]
[UpdateInGroup(typeof(SimulationSystemGroup))]
[UpdateAfter(typeof(PlayerMovementSystem))]
public partial struct PlayerFireSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        float dt = SystemAPI.Time.DeltaTime;
        var config = SystemAPI.GetSingleton<FireConfig>();
        var ecb = new EntityCommandBuffer(Allocator.Temp);
        
        foreach (var (cooldown, pos) in 
                 SystemAPI.Query<RefRW<FireCooldown>, RefRO<Position>>()
                          .WithAll<PlayerTag>()) {
            // 减少冷却时间
            cooldown.ValueRW.Remaining -= dt;
            
            // 冷却完毕且按下射击键
            if (cooldown.ValueRO.Remaining <= 0 && Input.GetButton("Fire1")) {
                // 创建子弹
                Entity bullet = ecb.CreateEntity();
                ecb.AddComponent(bullet, new BulletTag());
                ecb.AddComponent(bullet, new Position { 
                    Value = pos.ValueRO.Value 
                });
                ecb.AddComponent(bullet, new Velocity { 
                    Value = new float3(0, 0, 1) * config.BulletSpeed 
                });
                ecb.AddComponent(bullet, new BulletLifetime { 
                    Remaining = config.BulletLifetime 
                });
                ecb.AddComponent(bullet, new Damage { Amount = 10 });
                
                // 重置冷却
                cooldown.ValueRW.Remaining = 1f / config.FireRate;
            }
        }
        
        ecb.Playback(state.EntityManager);
        ecb.Dispose();
    }
}

// Assets/Scripts/Systems/BulletMovementSystem.cs
[BurstCompile]
[UpdateInGroup(typeof(SimulationSystemGroup))]
public partial struct BulletMovementSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        float dt = SystemAPI.Time.DeltaTime;
        
        foreach (var (pos, vel) in 
                 SystemAPI.Query<RefRW<Position>, RefRO<Velocity>>()
                          .WithAll<BulletTag>()) {
            pos.ValueRW.Value += vel.ValueRO.Value * dt;
        }
    }
}

// Assets/Scripts/Systems/BulletLifetimeSystem.cs
[BurstCompile]
[UpdateInGroup(typeof(SimulationSystemGroup))]
[UpdateAfter(typeof(BulletMovementSystem))]
public partial struct BulletLifetimeSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        float dt = SystemAPI.Time.DeltaTime;
        var ecb = new EntityCommandBuffer(Allocator.Temp);
        
        foreach (var (lifetime, entity) in 
                 SystemAPI.Query<RefRW<BulletLifetime>>()
                          .WithAll<BulletTag>()
                          .WithEntityAccess()) {
            lifetime.ValueRW.Remaining -= dt;
            if (lifetime.ValueRO.Remaining <= 0) {
                ecb.DestroyEntity(entity);
            }
        }
        
        ecb.Playback(state.EntityManager);
        ecb.Dispose();
    }
}

// Assets/Scripts/Systems/CollisionSystem.cs
[BurstCompile]
[UpdateInGroup(typeof(SimulationSystemGroup))]
[UpdateAfter(typeof(BulletMovementSystem))]
public partial struct CollisionSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        var ecb = new EntityCommandBuffer(Allocator.Temp);
        
        // 简化的碰撞检测：子弹 vs 敌人
        var bullets = SystemAPI.Query<RefRO<Position>, RefRO<Damage>>()
                               .WithAll<BulletTag>()
                               .ToComponentDataArray<Position>(Allocator.Temp);
        
        foreach (var (enemyPos, enemyHealth, enemyEntity) in 
                 SystemAPI.Query<RefRO<Position>, RefRW<Health>>()
                          .WithAll<EnemyTag>()
                          .WithEntityAccess()) {
            foreach (var bulletPos in bullets) {
                if (math.distance(enemyPos.ValueRO.Value, bulletPos.Value) < 1f) {
                    // 命中
                    enemyHealth.ValueRW.Current -= 10;
                    break;
                }
            }
            
            if (enemyHealth.ValueRO.Current <= 0) {
                ecb.DestroyEntity(enemyEntity);
            }
        }
        
        ecb.Playback(state.EntityManager);
        ecb.Dispose();
        bullets.Dispose();
    }
}
```

**4. 渲染系统（使用 Entities Graphics）：**

```csharp
// Assets/Scripts/Systems/RenderSystem.cs
using Unity.Entities;
using Unity.Transforms;
using Unity.Rendering;
using UnityEngine;

// 通过 Authoring 自动添加渲染组件
public class MeshAuthoring : MonoBehaviour {
    public Mesh mesh;
    public Material material;
}

public class MeshBaker : Baker<MeshAuthoring> {
    public override void Bake(MeshAuthoring authoring) {
        var entity = GetEntity(TransformUsageFlags.Dynamic);
        
        // Unity DOTS 的渲染组件
        AddComponent(entity, new MaterialMeshInfo {
            MeshID = RegisterMesh(authoring.mesh),
            MaterialID = RegisterMaterial(authoring.material)
        });
        AddComponent(entity, new LocalToWorld { Value = float4x4.identity });
        AddComponent(entity, new LocalTransform {
            Position = authoring.transform.position,
            Rotation = quaternion.identity,
            Scale = 1f
        });
    }
}
```

**5. 主入口：**

```csharp
// Assets/Scripts/Main/GameBootstrap.cs
using Unity.Entities;
using UnityEngine;

public class GameBootstrap : MonoBehaviour {
    void Start() {
        // 创建游戏状态单例
        var em = World.DefaultGameObjectInjectionWorld.EntityManager;
        var stateEntity = em.CreateEntity();
        em.AddComponentData(stateEntity, new GameState {
            Score = 0,
            IsGameOver = false
        });
    }
}
```

### 总结

**核心要点：**
- Unity DOTS = Entities + Burst + Jobs + Collections + Mathematics
- Authoring 工作流：GameObject + Baker → Entity
- 系统按功能划分，用 SystemGroup 组织执行顺序
- 渲染用 Entities Graphics 包，自动处理

**项目搭建流程：**
1. 安装 DOTS 包（Entities、Burst、Jobs 等）
2. 定义组件（Components/）
3. 编写 Authoring 脚本（Authoring/）
4. 实现系统（Systems/）
5. 创建 SubScene，放置 GameObject
6. 运行，自动烘焙为 Entity

**常见注意事项：**
- 使用 SubScene 而非普通 Scene，才能触发烘焙
- Authoring 脚本只在编辑器使用，运行时不存在
- 系统默认在 SimulationSystemGroup 中执行
- 调试时用 Entity Hierarchy 窗口查看实体状态

---

## 第 24 讲：ECS 游戏案例剖析

### 概念

本讲通过剖析一个完整的"塔防游戏"案例，展示 ECS 在真实项目中的架构设计。塔防游戏包含大量敌人、子弹、防御塔，是 ECS 的典型应用场景。

### 原理

**塔防游戏的 ECS 架构设计：**

```
组件层:
├── 通用组件: Position, Health, Velocity
├── 标签组件: EnemyTag, TowerTag, BulletTag, DeadTag
├── 敌人组件: PathIndex, PathProgress, EnemyType
├── 防御塔组件: Target, Range, FireRate, FireCooldown, Damage
├── 子弹组件: Target, Speed, Damage, Lifetime
└── 全局组件: GameConfig, GameState, WaveInfo

系统层:
├── 生成系统: EnemySpawnSystem, BulletSpawnSystem
├── 移动系统: EnemyMovementSystem, BulletMovementSystem
├── 战斗系统: TargetingSystem, FireSystem, DamageSystem
├── 生命系统: DeathSystem, CleanupSystem
└── 表现系统: RenderSystem, AudioSystem, EffectSystem
```

**数据流：**

```
每帧执行:
1. EnemySpawnSystem: 按波次生成敌人
2. EnemyMovementSystem: 敌人沿路径移动
3. TargetingSystem: 防御塔寻找目标
4. FireSystem: 防御塔射击，生成子弹
5. BulletMovementSystem: 子弹飞向目标
6. DamageSystem: 子弹命中，造成伤害
7. DeathSystem: 处理死亡敌人
8. CleanupSystem: 清理过期子弹和死亡实体
9. RenderSystem: 渲染所有实体
```

### 例子

**塔防游戏核心代码：**

```csharp
// === 组件定义 ===

// 敌人路径数据
public struct PathData : IComponentData {
    public int CurrentIndex;      // 当前路径点索引
    public float Progress;        // 到当前点的进度
}

// 防御塔目标
public struct Target : IComponentData {
    public Entity Value;
}

// 防御塔配置
public struct TowerConfig : IComponentData {
    public float Range;           // 攻击范围
    public float FireRate;        // 射速
    public float Damage;          // 伤害
    public float BulletSpeed;     // 子弹速度
}

// 子弹目标
public struct BulletTarget : IComponentData {
    public Entity Value;
}

// 波次信息（单例）
public struct WaveInfo : IComponentData {
    public int CurrentWave;
    public float NextWaveTime;
    public int EnemiesRemaining;
}

// === 敌人生成系统 ===
[BurstCompile]
[UpdateInGroup(typeof(SimulationSystemGroup))]
public partial struct EnemySpawnSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        ref var wave = ref SystemAPI.GetSingletonRW<WaveInfo>().ValueRW;
        wave.NextWaveTime -= SystemAPI.Time.DeltaTime;
        
        if (wave.NextWaveTime <= 0 && wave.EnemiesRemaining > 0) {
            var ecb = new EntityCommandBuffer(Allocator.Temp);
            
            // 生成敌人
            Entity enemy = ecb.CreateEntity();
            ecb.AddComponent(enemy, new EnemyTag());
            ecb.AddComponent(enemy, new Position { Value = new float3(0, 0, 0) });
            ecb.AddComponent(enemy, new Health { Current = 100, Max = 100 });
            ecb.AddComponent(enemy, new Velocity { Value = new float3(0, 0, 1) });
            ecb.AddComponent(enemy, new PathData { CurrentIndex = 0, Progress = 0 });
            
            wave.EnemiesRemaining--;
            wave.NextWaveTime = 1f;  // 每秒生成一个
            
            ecb.Playback(state.EntityManager);
            ecb.Dispose();
        }
    }
}

// === 敌人移动系统（沿路径）===
[BurstCompile]
[UpdateInGroup(typeof(SimulationSystemGroup))]
[UpdateAfter(typeof(EnemySpawnSystem))]
public partial struct EnemyMovementSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        float dt = SystemAPI.Time.DeltaTime;
        float speed = 2f;
        
        foreach (var (pos, path) in 
                 SystemAPI.Query<RefRW<Position>, RefRW<PathData>>()
                          .WithAll<EnemyTag>()) {
            // 简化：直线移动
            pos.ValueRW.Value += new float3(0, 0, 1) * speed * dt;
            path.ValueRW.Progress += speed * dt;
        }
    }
}

// === 目标选择系统 ===
[BurstCompile]
[UpdateInGroup(typeof(SimulationSystemGroup))]
[UpdateAfter(typeof(EnemyMovementSystem))]
public partial struct TargetingSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        // 收集所有敌人位置
        var enemyPositions = new NativeList<(Entity, float3)>(Allocator.Temp);
        foreach (var (pos, entity) in 
                 SystemAPI.Query<RefRO<Position>>()
                          .WithAll<EnemyTag>()
                          .WithEntityAccess()) {
            enemyPositions.Add((entity, pos.ValueRO.Value));
        }
        
        // 为每个防御塔找最近敌人
        foreach (var (towerPos, towerConfig, target) in 
                 SystemAPI.Query<RefRO<Position>, RefRO<TowerConfig>, RefRW<Target>>()
                          .WithAll<TowerTag>()) {
            float3 towerPosValue = towerPos.ValueRO.Value;
            float range = towerConfig.ValueRO.Range;
            
            Entity closestEnemy = Entity.Null;
            float closestDist = float.MaxValue;
            
            foreach (var (enemyEntity, enemyPos) in enemyPositions) {
                float dist = math.distance(towerPosValue, enemyPos);
                if (dist < range && dist < closestDist) {
                    closestDist = dist;
                    closestEnemy = enemyEntity;
                }
            }
            
            target.ValueRW.Value = closestEnemy;
        }
        
        enemyPositions.Dispose();
    }
}

// === 射击系统 ===
[BurstCompile]
[UpdateInGroup(typeof(SimulationSystemGroup))]
[UpdateAfter(typeof(TargetingSystem))]
public partial struct FireSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        float dt = SystemAPI.Time.DeltaTime;
        var ecb = new EntityCommandBuffer(Allocator.Temp);
        
        foreach (var (pos, config, target, cooldown, entity) in 
                 SystemAPI.Query<RefRO<Position>, RefRO<TowerConfig>, 
                               RefRO<Target>, RefRW<FireCooldown>>()
                          .WithAll<TowerTag>()
                          .WithEntityAccess()) {
            cooldown.ValueRW.Remaining -= dt;
            
            // 有目标且冷却完毕
            if (target.ValueRO.Value != Entity.Null && 
                cooldown.ValueRO.Remaining <= 0) {
                
                // 生成子弹
                Entity bullet = ecb.CreateEntity();
                ecb.AddComponent(bullet, new BulletTag());
                ecb.AddComponent(bullet, new Position { 
                    Value = pos.ValueRO.Value 
                });
                ecb.AddComponent(bullet, new BulletTarget { 
                    Value = target.ValueRO.Value 
                });
                ecb.AddComponent(bullet, new Damage { 
                    Amount = config.ValueRO.Damage 
                });
                ecb.AddComponent(bullet, new BulletLifetime { 
                    Remaining = 3f 
                });
                
                cooldown.ValueRW.Remaining = 1f / config.ValueRO.FireRate;
            }
        }
        
        ecb.Playback(state.EntityManager);
        ecb.Dispose();
    }
}

// === 子弹移动系统（追踪目标）===
[BurstCompile]
[UpdateInGroup(typeof(SimulationSystemGroup))]
[UpdateAfter(typeof(FireSystem))]
public partial struct BulletMovementSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        float dt = SystemAPI.Time.DeltaTime;
        float speed = 20f;
        
        foreach (var (pos, target) in 
                 SystemAPI.Query<RefRW<Position>, RefRO<BulletTarget>>()
                          .WithAll<BulletTag>()) {
            Entity targetEntity = target.ValueRO.Value;
            
            if (SystemAPI.HasComponent<Position>(targetEntity)) {
                float3 targetPos = SystemAPI.GetComponent<Position>(targetEntity).Value;
                float3 direction = math.normalize(targetPos - pos.ValueRO.Value);
                pos.ValueRW.Value += direction * speed * dt;
            }
        }
    }
}

// === 伤害系统 ===
[BurstCompile]
[UpdateInGroup(typeof(SimulationSystemGroup))]
[UpdateAfter(typeof(BulletMovementSystem))]
public partial struct DamageSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        var ecb = new EntityCommandBuffer(Allocator.Temp);
        
        foreach (var (bulletPos, bulletTarget, damage, bulletEntity) in 
                 SystemAPI.Query<RefRO<Position>, RefRO<BulletTarget>, 
                               RefRO<Damage>>()
                          .WithAll<BulletTag>()
                          .WithEntityAccess()) {
            Entity target = bulletTarget.ValueRO.Value;
            
            if (SystemAPI.HasComponent<Position>(target) && 
                SystemAPI.HasComponent<Health>(target)) {
                float3 targetPos = SystemAPI.GetComponent<Position>(target).Value;
                
                // 命中检测
                if (math.distance(bulletPos.ValueRO.Value, targetPos) < 0.5f) {
                    // 造成伤害
                    var health = SystemAPI.GetComponentRW<Health>(target);
                    health.ValueRW.Current -= damage.ValueRO.Amount;
                    
                    // 销毁子弹
                    ecb.DestroyEntity(bulletEntity);
                }
            } else {
                // 目标已消失，销毁子弹
                ecb.DestroyEntity(bulletEntity);
            }
        }
        
        ecb.Playback(state.EntityManager);
        ecb.Dispose();
    }
}

// === 死亡清理系统 ===
[BurstCompile]
[UpdateInGroup(typeof(SimulationSystemGroup))]
[UpdateAfter(typeof(DamageSystem))]
public partial struct DeathSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        var ecb = new EntityCommandBuffer(Allocator.Temp);
        
        // 销毁血量归零的敌人
        foreach (var (health, entity) in 
                 SystemAPI.Query<RefRO<Health>>()
                          .WithAll<EnemyTag>()
                          .WithEntityAccess()) {
            if (health.ValueRO.Current <= 0) {
                ecb.DestroyEntity(entity);
                
                // 加分
                ref var gameState = ref SystemAPI.GetSingletonRW<GameState>().ValueRW;
                gameState.Score += 10;
            }
        }
        
        // 销毁过期子弹
        foreach (var (lifetime, entity) in 
                 SystemAPI.Query<RefRW<BulletLifetime>>()
                          .WithAll<BulletTag>()
                          .WithEntityAccess()) {
            lifetime.ValueRW.Remaining -= SystemAPI.Time.DeltaTime;
            if (lifetime.ValueRO.Remaining <= 0) {
                ecb.DestroyEntity(entity);
            }
        }
        
        ecb.Playback(state.EntityManager);
        ecb.Dispose();
    }
}
```

### 总结

**核心要点：**
- 塔防游戏是 ECS 的典型应用：大量实体、批量处理、清晰的数据流
- 系统按功能划分，用 UpdateBefore/After 控制顺序
- 目标选择用 NativeList 收集敌人，避免嵌套查询
- 子弹追踪用 Entity 引用，每帧查询目标位置

**架构设计原则：**
- 组件设计要小而精，按功能领域划分
- 系统职责单一，一个系统只做一件事
- 数据流要清晰，避免循环依赖
- 频繁创建/销毁的实体（子弹）考虑用对象池

**常见注意事项：**
- 目标选择系统可能成为性能瓶颈，考虑用空间分区优化
- 子弹追踪目标时，目标可能已销毁，必须检查 HasComponent
- 死亡清理放在所有逻辑之后，避免销毁正在使用的实体
- 用 Profiler 监控各系统耗时，优化热点

---

## 第 25 讲：常见陷阱与最佳实践

### 概念

本讲总结 ECS 开发中的常见陷阱和最佳实践，帮助你避免踩坑，写出高质量的 ECS 代码。

### 原理

**ECS 开发的三大类陷阱：**

1. **正确性陷阱**：导致崩溃或逻辑错误
2. **性能陷阱**：导致性能不如预期
3. **架构陷阱**：导致代码难以维护

### 例子

**陷阱 1：遍历时修改结构**

```csharp
// ❌ 错误：遍历时销毁实体
public partial struct BadSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        foreach (var (health, entity) in 
                 SystemAPI.Query<RefRO<Health>>().WithEntityAccess()) {
            if (health.ValueRO.Current <= 0) {
                state.EntityManager.DestroyEntity(entity);  // 崩溃！
            }
        }
    }
}

// ✅ 正确：用 EntityCommandBuffer 延迟执行
public partial struct GoodSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        var ecb = new EntityCommandBuffer(Allocator.Temp);
        
        foreach (var (health, entity) in 
                 SystemAPI.Query<RefRO<Health>>().WithEntityAccess()) {
            if (health.ValueRO.Current <= 0) {
                ecb.DestroyEntity(entity);  // 记录，不立即执行
            }
        }
        
        ecb.Playback(state.EntityManager);  // 遍历结束后统一执行
        ecb.Dispose();
    }
}
```

**陷阱 2：持有过期的 Entity 引用**

```csharp
// ❌ 错误：长期持有 Entity 引用不检查有效性
public class BadReference {
    private Entity target;  // 可能已被销毁
    
    public void Attack(EntityManager em) {
        var health = em.GetComponentData<Health>(target);  // 可能崩溃
    }
}

// ✅ 正确：使用前检查 Exists
public class GoodReference {
    private Entity target;
    
    public void Attack(EntityManager em) {
        if (!em.Exists(target)) return;  // 检查有效性
        var health = em.GetComponentData<Health>(target);
    }
}
```

**陷阱 3：在组件中存储引用类型**

```csharp
// ❌ 错误：组件中存储 class
public struct BadComponent : IComponentData {
    public List<int> Data;  // 引用类型，导致 GC
    public string Name;     // 引用类型
}

// ✅ 正确：用 Native 容器和 FixedString
public struct GoodComponent : IComponentData {
    // 用 DynamicBuffer 替代 List
    // 用 FixedString 替代 string
    public FixedString32 Name;
}

// 或者用 DynamicBuffer
public struct IntBuffer : IBufferElementData {
    public int Value;
}
// 实体上可以 GetBuffer<IntBuffer>() 获取动态数组
```

**陷阱 4：忘记 Dispose Native 容器**

```csharp
// ❌ 错误：忘记释放
public partial struct LeakSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        var array = new NativeArray<float>(1000, Allocator.Persistent);
        // 使用 array...
        // 忘记 Dispose → 内存泄漏！
    }
}

// ✅ 正确：使用 using 或显式 Dispose
public partial struct NoLeakSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        // 方式1：using 自动释放
        using var array = new NativeArray<float>(1000, Allocator.Persistent);
        // 使用 array...
        // using 块结束时自动 Dispose
        
        // 方式2：显式释放
        var array2 = new NativeArray<float>(1000, Allocator.Persistent);
        try {
            // 使用 array2...
        } finally {
            array2.Dispose();
        }
    }
}
```

**陷阱 5：Burst 编译失败**

```csharp
// ❌ 错误：Burst 不支持的代码
[BurstCompile]
public partial struct BadJob : IJobEntity {
    void Execute(ref Position pos) {
        // 以下都会导致 Burst 编译失败或回退到 IL2CPP：
        Debug.Log("Message");  // 不支持
        string name = "Enemy";  // 不支持 string
        var list = new List<int>();  // 不支持引用类型
        try { } catch { }  // 不支持异常
    }
}

// ✅ 正确：Burst 友好的代码
[BurstCompile]
public partial struct GoodJob : IJobEntity {
    public float DeltaTime;
    
    void Execute(ref Position pos, in Velocity vel) {
        pos.Value += vel.Value * DeltaTime;  // 纯数学运算
    }
}
```

**陷阱 6：系统顺序错误**

```csharp
// ❌ 错误：未指定顺序，导致一帧延迟
public partial struct MovementSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        // 移动实体
    }
}

public partial struct RenderSystem : ISystem {
    public void OnUpdate(ref SystemState state) {
        // 渲染：可能用旧位置
    }
}

// ✅ 正确：显式指定顺序
[UpdateInGroup(typeof(SimulationSystemGroup))]
public partial struct MovementSystem : ISystem { }

[UpdateInGroup(typeof(PresentationSystemGroup))]  // 表现阶段，在模拟之后
public partial struct RenderSystem : ISystem { }
```

**陷阱 7：过度使用共享组件**

```csharp
// ❌ 错误：共享组件值过多，导致 Chunk 碎片化
public struct EnemyId : ISharedComponentData {
    public int Id;  // 每个敌人 Id 不同 → 每个敌人独占一个 Chunk
}

// ✅ 正确：共享组件用于真正"共享"的数据
public struct EnemyType : ISharedComponentData {
    public int TypeId;  // 0=小兵, 1=精英, 2=Boss（只有几种值）
}
```

**陷阱 8：在 Job 中访问主线程数据**

```csharp
// ❌ 错误：Job 中访问托管对象
[BurstCompile]
public partial struct BadJob : IJobEntity {
    public GameObject Prefab;  // 托管对象，不能在 Job 中用
    
    void Execute(ref Position pos) {
        Instantiate(Prefab);  // 崩溃！
    }
}

// ✅ 正确：用 EntityCommandBuffer 延迟创建
[BurstCompile]
public partial struct GoodJob : IJobEntity {
    public EntityCommandBuffer.ParallelWriter Ecb;
    
    void Execute(ref Position pos, [EntityIndexInQuery] int sortKey) {
        Entity e = Ecb.CreateEntity(sortKey);
        // 配置实体...
    }
}
```

**最佳实践总结：**

```csharp
// 1. 组件设计：小而精
struct Position : IComponentData { float3 Value; }  // ✅ 12 字节
struct Health : IComponentData { float Cur, Max; }  // ✅ 8 字节

// 2. 系统设计：单一职责
public partial struct MovementSystem : ISystem { /* 只管移动 */ }
public partial struct CollisionSystem : ISystem { /* 只管碰撞 */ }

// 3. 查询优化：用只读标记
SystemAPI.Query<RefRW<Position>, RefRO<Velocity>>()  // Velocity 只读

// 4. 并行化：用 ScheduleParallel
new MyJob { ... }.ScheduleParallel();

// 5. 内存管理：及时 Dispose
using var array = new NativeArray<float>(100, Allocator.Temp);

// 6. 结构变更：用 ECB
var ecb = new EntityCommandBuffer(Allocator.Temp);
// ... 记录操作
ecb.Playback(state.EntityManager);
ecb.Dispose();

// 7. 全局状态：用单例
var config = SystemAPI.GetSingleton<GameConfig>();

// 8. 状态标记：用标签组件
ecb.AddComponent<DeadTag>(entity);  // 而非增删数据组件
```

### 总结

**核心要点：**

| 陷阱类别 | 常见问题 | 解决方案 |
|----------|----------|----------|
| 正确性 | 遍历时修改结构 | 用 EntityCommandBuffer |
| 正确性 | 持有过期引用 | 使用前检查 Exists |
| 正确性 | Job 访问主线程数据 | 用 ECB 延迟操作 |
| 性能 | 组件存引用类型 | 用 Native 容器和 FixedString |
| 性能 | 忘记 Dispose | 用 using 或 try-finally |
| 性能 | Burst 编译失败 | 避免 string、class、异常 |
| 性能 | 系统顺序错误 | 显式声明 UpdateBefore/After |
| 性能 | 共享组件碎片化 | 共享组件值种类要少 |
| 架构 | 组件过大 | 拆分为小组件 |
| 架构 | 系统职责混乱 | 单一职责原则 |

**ECS 开发黄金法则：**
1. **数据优先**：先设计组件，再设计系统
2. **小即是美**：组件越小，性能越好
3. **延迟执行**：结构变更用 ECB
4. **只读优先**：能用 RefRO 就不用 RefRW
5. **并行优先**：能用 ScheduleParallel 就不用 Schedule
6. **检查引用**：使用 Entity 前检查 Exists
7. **及时清理**：Native 容器用完 Dispose
8. **Burst 友好**：避免引用类型和异常

**学习路径建议：**
1. 入门：理解 Entity、Component、System 三要素
2. 进阶：掌握 Archetype、Chunk、EntityQuery
3. 高级：学会 Burst、Job System、Native 容器
4. 实战：用 Unity DOTS 完成一个完整项目
5. 优化：用 Profiler 分析性能，针对性优化

---

## 课程结语

恭喜你完成了 ECS 实体组件系统的 25 讲学习！

通过本课程，你应该已经：

1. **理解了 ECS 的核心思想**：数据导向设计，数据与逻辑分离
2. **掌握了三大要素**：实体（ID）、组件（数据）、系统（逻辑）
3. **深入了底层原理**：Archetype、Chunk、EntityQuery、缓存局部性
4. **学会了性能优化**：Burst 编译、Job System、Native 容器
5. **具备了实战能力**：能在 Unity DOTS 中构建高性能游戏

ECS 不仅是一种架构模式，更是一种思维方式。它要求我们从"对象有什么行为"转向"数据如何流动"。这种思维转变一旦完成，你会发现写出的代码更高效、更灵活、更易维护。

**下一步学习建议：**
- 深入 Unity DOTS 官方文档和示例项目
- 学习 Bevy（Rust ECS）拓展视野
- 研究 ECS 在其他领域的应用（如仿真、数据可视化）
- 参与开源 ECS 项目，积累实战经验

祝你在 ECS 的道路上越走越远！

---

> **参考资料：**
> - Unity DOTS 官方文档：https://docs.unity3d.com/Packages/com.unity.entities@latest
> - Bevy Book：https://bevyengine.org/learn/book/
> - ECS FAQ：https://github.com/SanderMertens/ecs-faq
> - Data-Oriented Design：https://www.dataorienteddesign.com/site.php

