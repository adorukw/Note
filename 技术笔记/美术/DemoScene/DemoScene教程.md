# DemoScene · 系统教程

> 一本从历史源流到现代着色器的实时图形艺术实战教材。

---

## 课程总览

**预计讲数**：32 讲（8 章 × 4 讲）

**学习目标**：

1. 理解 DemoScene 的历史源流、文化精神与艺术价值，建立对这一数字艺术形式的全面认知。
2. 掌握实时图形编程基础：帧缓冲操作、像素绘制、调色板、VGA 模式与老硬件技巧。
3. 熟练运用过程化生成技术：噪声、L-System、元球、分形，用代码生成无限内容。
4. 深入理解 DemoScene 经典效果：等离子、火焰、隧道、星空、变形网格的数学原理与实现。
5. 掌握现代 GPU 着色器编程：GLSL 片段着色器、光线步进、SDF、后处理。
6. 学会 4K/64K Intro 的代码压缩技巧：crinkler、shader 工具链、过程化资产。
7. 具备使用合成器与跟踪器制作 Demo 音乐的能力。
8. 能够独立设计并实现一个完整的 Demo 作品，整合图形、音乐与设计。

**前置知识**：C/C++ 基础、基本线性代数（向量、矩阵）、三角函数、基本图形学概念。

**学习路径**：历史与文化 → 图形基础 → 过程化生成 → 经典效果 → 着色器与 GPU → 音频合成 → 代码压缩 → 实战项目

---

## 详细章节目录

### 第 1 章 · 历史与文化

- 第 01 讲：DemoScene 的起源与演变
- 第 02 讲：作品分类与竞赛类别
- 第 03 讲：传奇作品与创作者
- 第 04 讲：文化精神与艺术价值

### 第 2 章 · 图形基础

- 第 05 讲：帧缓冲与像素操作
- 第 06 讲：调色板与颜色循环
- 第 07 讲：VGA Mode 13h 与老硬件技巧
- 第 08 讲：固定时间步长与垂直同步

### 第 3 章 · 过程化生成

- 第 09 讲：随机数与噪声
- 第 10 讲：Value Noise 与 Perlin Noise
- 第 11 讲：L-System 与分形树
- 第 12 讲：元球与隐式曲面

### 第 4 章 · 经典效果

- 第 13 讲：等离子效果
- 第 14 讲：火焰效果
- 第 15 讲：隧道效果
- 第 16 讲：星空与视差滚动

### 第 5 章 · 着色器与 GPU

- 第 05 讲：GLSL 与片段着色器基础
- 第 18 讲：光线步进 Ray Marching
- 第 19 讲：SDF 与距离场渲染
- 第 20 讲：后处理与色彩分级

### 第 6 章 · 音频合成

- 第 21 讲：声音基础与波形合成
- 第 22 讲：合成器与包络
- 第 23 讲：跟踪器与模块音乐
- 第 24 讲：DSP 效果与混音

### 第 7 章 · 代码压缩

- 第 25 讲：4K Intro 工具链
- 第 26 讲：Shader 工具链与压缩
- 第 27 讲：过程化资产生成
- 第 28 讲：尺寸优化技巧

### 第 8 章 · 实战项目

- 第 29 讲：Demo 设计与节奏
- 第 30 讲：场景切换与时间轴
- 第 31 讲：完整 Demo 框架
- 第 32 讲：发布与竞赛

---

# 第 1 章 · 历史与文化

DemoScene 是一种独特的数字艺术形式——程序员用代码创造实时生成的视听作品，在有限的尺寸（4KB、64KB）或时间内展现极致的技术与美学。本章追溯 DemoScene 从 1980 年代盗版场景到现代艺术形式的演变，梳理作品分类与竞赛类别，致敬传奇作品与创作者，并探讨其文化精神与艺术价值。理解历史，才能理解为什么 DemoScene 程序员会为节省一个字节而欣喜若狂。

## 第 01 讲 · DemoScene 的起源与演变

### 概念

**DemoScene** 是一种以编程为核心的数字艺术亚文化，参与者（Demo 制作人）编写程序实时生成视听作品。这些程序称为 **Demo**，通常在 4KB、64KB 等严格尺寸限制下，展现惊人的图形效果与音乐。

DemoScene 起源于 1980 年代的软件盗版场景——破解者在自己破解的游戏前加入"签名画面"（Cracktro），炫耀破解能力。这些签名画面逐渐演变为独立的视听作品，脱离盗版语境，成为纯粹的艺术表达。

### 原理

**起源：Cracktro 时代（1980-1985）**

早期家用电脑（Commodore 64、ZX Spectrum、Amiga）的游戏以软盘传播。破解者移除游戏的版权保护后，会在游戏启动前加入自己的签名画面——闪烁的文字、滚动的字幕、简单的图形效果。这些 Cracktro 是 DemoScene 的雏形。

**演变：独立艺术（1985-1990）**

随着 Cracktro 越来越精致，制作者开始脱离盗版语境，制作纯粹的视听作品。1985 年，Commodore 64 上的第一批独立 Demo 出现。Amiga 的出现（1987）凭借其强大的图形与音频能力，推动 DemoScene 进入黄金时代。

**成熟：PC 时代与竞赛（1990-2000）**

PC 的普及让 DemoScene 转向 DOS 平台。VGA Mode 13h（320×200，256 色）成为标准。DemoParty（如 Assembly、The Gathering、Revision）兴起，形成正式的竞赛体系。

**现代：GPU 与着色器（2000-至今）**

2000 年代后，GPU 与着色器编程改变了 DemoScene。4K Intro（4KB 程序）成为主流，全程用 GLSL 片段着色器实现。2010 年代，Ray Marching 与 SDF（Signed Distance Field）让 4KB 程序能渲染电影级 3D 场景。

**文化地位**：

2015 年，芬兰将 DemoScene 列入国家非物质文化遗产名录。2021 年，德国、荷兰相继跟进。DemoScene 从地下亚文化成为受官方认可的文化遗产。

### 例子

```
时间线：DemoScene 关键节点

1980 ──── Cracktro 时代（C64、Spectrum）
  │
1985 ──── 第一批独立 Demo（C64）
  │
1987 ──── Amiga 黄金时代开始
  │
1990 ──── PC DOS 时代（VGA Mode 13h）
  │       Future Crew《Second Reality》(1993)
  │
1995 ──── 加速卡时代（3dfx Voodoo）
  │
2000 ──── GPU 着色器时代
  │       .the .product (2000, 64K)
  │
2004 ──── 4K Intro 兴起
  │       Chaos Theory (Farbrausch, 2004)
  │
2010 ──── Ray Marching 与 SDF 成熟
  │       Eos (TBC, 2010)
  │
2015 ──── 芬兰列入非物质文化遗产
  │
2020 ──── 现代着色器 Demo
  │       H - Immersion (Conspiracy, 2017)
```

**经典 Cracktro 示例**（伪代码）：

```c
// 早期 Cracktro 的典型结构
void main() {
    setup_vga_mode_13h();
    
    // 闪烁的破解组名称
    while (!key_pressed()) {
        for (int i = 0; i < 30; i++) {
            draw_text("CRACKED BY FAIRLIGHT", 100, 100, rainbow_palette(i));
            vsync();
        }
        
        // 滚动字幕
        scroll_text("GREETINGS TO ALL OUR FRIENDS...");
        
        // 简单的铜管效果（Copper Bar）
        copper_bars();
    }
}
```

### 总结

- DemoScene 起源于 1980 年代盗版场景的 Cracktro，逐渐演变为独立艺术形式。
- 经历 C64 → Amiga → PC DOS → GPU 着色器四个主要时代。
- 2015 年起被多国列为非物质文化遗产，从亚文化升华为官方认可的文化遗产。
- **核心精神**：用代码创造实时视听艺术，在限制中追求极致。
- **学习意义**：理解历史能帮助你理解 DemoScene 的美学偏好与技术传承。

---

## 第 02 讲 · 作品分类与竞赛类别

### 概念

DemoScene 作品按**尺寸限制**与**平台**分类，在 DemoParty 的竞赛中角逐。主要类别包括：Demo（无限制）、64K Intro（64KB）、4K Intro（4KB）、1K Intro（1KB）、Oldskool Demo（老平台）、Wild（任意形式）。

每个类别有不同的技术挑战：大 Demo 追求视觉震撼，4K Intro 追求极致压缩，Oldskool 追求老硬件的极致利用。

### 原理

**按尺寸分类**：

| 类别 | 大小限制 | 特点 |
|------|---------|------|
| Demo | 无限制（通常 < 256MB） | 完整视听作品，多场景、多音轨 |
| 64K Intro | 64KB | 过程化生成所有资产，需高级压缩 |
| 4K Intro | 4096 字节 | 几乎全用着色器，极致压缩 |
| 1K Intro | 1024 字节 | 单一效果，每个字节都珍贵 |
| 256B Intro | 256 字节 | 极简主义，DOS/JavaScript 为主 |

**按平台分类**：

- **PC Modern**：现代 PC，GPU 着色器为主。
- **PC Oldskool**：DOS、Win95 时代，CPU 软件渲染。
- **Commodore 64**：8 位传奇平台，至今仍有活跃创作。
- **Amiga**：16 位经典，OCS/ECS/AGA 芯片组。
- **ZX Spectrum**：英国 8 位机，色彩限制严格。
- **Game Boy / NES / SNES**：游戏机平台。
- **Mobile / JavaScript**：新兴平台。

**竞赛类别**：

1. **Demo 竞赛**：旗舰类别，无尺寸限制，追求完整体验。
2. **64K Intro**：64KB 限制，过程化生成标杆。
3. **4K Intro**：4KB 限制，着色器艺术巅峰。
4. **Oldskool Demo**：老平台 Demo，致敬传统。
5. **Executable Graphics**：单张程序生成图像。
6. **Executable Music**：程序生成音乐。
7. **Wild**：任意形式（视频、实拍、动画）。
8. **Music**：跟踪器音乐竞赛（Streaming / Tracked）。

**主要 DemoParty**：

| Party | 地点 | 时间 | 特点 |
|-------|------|------|------|
| Revision | 德国 | 复活节 | 全球最大，现代 DemoScene 中心 |
| Assembly | 芬兰 | 夏季 | 历史最悠久，1992 至今 |
| The Gathering | 挪威 | 复活节 | 北欧大型 Party |
| Evoke | 德国 | 夏季 | 氛围友好 |
| Field-FX | 英国 | 夏季 | 英国主要 Party |
| Outline | 荷兰 | 夏季 | 户外 Party |
| Solskogen | 挪威 | 夏季 | 小型精品 Party |

### 例子

```
各类别典型作品

Demo 类别：
  Second Reality (Future Crew, 1993)     ── PC DOS 里程碑
  The Popular Demo (Farbrausch, 2003)    ── 现代 Demo 经典
  H - Immersion (Conspiracy, 2017)       ── 电影级水下场景

64K Intro：
  .the .product (Farbrausch, 2000)       ── 首个里程碑 64K
  Chaos Theory (Farbrausch, 2004)        ── 过程化城市
  Lifeforce (Andromeda, 2007)            ── 视觉震撼

4K Intro：
  Eos (TBC, 2010)                        ── SDF 里程碑
  Framerate (Loonies, 2012)              ── 几何抽象
  Muon Baryon (Alcatraz, 2018)           ── 现代着色器巅峰

1K Intro：
  Insert No Coin (Linus, 2013)           ── JavaScript 1K
  Rose (Loonies, 2014)                   ── 几何之美

Oldskool：
  Various C64 Demos                      ── 8 位极限
  Amiga AGA Demos                        ── 16 位经典
```

**4K Intro 的尺寸构成**（典型）：

```
4096 字节总预算
├── 1024 字节：PE 头与启动代码（Crinkler 压缩后）
├── 512 字节：OpenGL/GLFW 初始化
├── 2048 字节：片段着色器源码（核心）
├── 256 字节：音乐合成代码
└── 256 字节：参数与常量
```

### 总结

- DemoScene 作品按尺寸（Demo/64K/4K/1K）与平台（PC/C64/Amiga 等）分类。
- 主要类别：Demo（无限制）、64K、4K、1K、Oldskool、Wild。
- **主要 Party**：Revision（德国）、Assembly（芬兰）是最重要的竞赛。
- **4K Intro** 是现代 DemoScene 的核心，几乎全用着色器实现。
- **选择建议**：初学者从 4K 着色器入门，熟练后挑战 64K 或完整 Demo。

---

## 第 03 讲 · 传奇作品与创作者

### 概念

DemoScene 40 年历史中诞生了无数传奇作品与创作者群体（Group）。这些作品推动了技术边界，定义了时代美学。本讲致敬里程碑作品与传奇 Group，理解他们的创新点能帮助你站在巨人的肩膀上。

### 原理

**里程碑作品的技术创新**：

1. **Second Reality (Future Crew, 1993)**
   - 平台：486 PC，VGA Mode 13h
   - 创新：软件渲染的 3D 场景、火焰、水波、变形效果
   - 意义：证明 PC 能匹敌 Amiga 的 Demo 能力，定义了 PC Demo 的标准

2. **.the .product (Farbrausch, 2000)**
   - 平台：Windows，OpenGL
   - 创新：64KB 内完整 3D 场景，过程化纹理与网格
   - 意义：开创 64K Intro 时代，证明过程化生成的威力

3. **Chaos Theory (Farbrausch, 2004)**
   - 平台：Windows，OpenGL
   - 创新：64KB 内生成完整城市，建筑、街道、车辆全程序生成
   - 意义：过程化生成的巅峰，至今仍是标杆

4. **Eos (TBC, 2010)**
   - 平台：Windows，OpenGL
   - 创新：4KB 内用 SDF + Ray Marching 渲染复杂 3D 场景
   - 意义：定义了现代 4K Intro 的技术路线

5. **H - Immersion (Conspiracy, 2017)**
   - 平台：Windows，OpenGL
   - 创新：电影级水下场景，体积光、焦散、粒子
   - 意义：Demo 类别的视觉巅峰，媲美电影特效

**传奇 Group**：

| Group | 国家 | 活跃年代 | 代表作 | 特点 |
|-------|------|---------|--------|------|
| Future Crew | 芬兰 | 1986-1996 | Second Reality | PC Demo 奠基者 |
| Farbrausch | 德国 | 1999-至今 | .the .product, Chaos Theory | 过程化生成先驱 |
| Conspiracy | 匈牙利 | 2003-至今 | H - Immersion | 电影级视觉 |
| Andromeda | 德国 | 1998-至今 | Lifeforce | 抽象美学 |
| TBC | 德国 | 2004-至今 | Eos | 4K SDF 先驱 |
| Alcatraz | 德国 | 2015-至今 | Muon Baryon | 现代着色器巅峰 |
| Loonies | 德国 | 2000-至今 | Framerate | 几何抽象 |
| Fairlight | 国际 | 1987-至今 | 各类 Oldskool | 老平台传承 |

**创作者精神**：

DemoScene 创作者通常是匿名或用 Handle（昵称），不追求商业利益。他们的动力来自：

1. **技术挑战**：突破硬件与尺寸限制。
2. **艺术表达**：用代码创造美。
3. **社区认可**：在 Party 获得掌声与投票。
4. **传承精神**：站在前人基础上，为后人铺路。

### 例子

```
Second Reality (1993) 场景结构

场景 1：星空与滚动文字（开场）
  └── 软件渲染星空 + 水平滚动字幕

场景 2：旋转的 3D 立方体
  └── 软件渲染，纯整数运算

场景 3：火焰效果
  └── 调色板循环 + 像素传播算法

场景 4：水波倒影
  └── 正弦波扭曲 + 镜像

场景 5：变形效果（Morph）
  └── 点集插值变形

场景 6：结束画面
  └── Future Crew 标志 + 致谢
```

**Farbrausch 的 werkkzeug 工具**：

```c
// Farbrausch 开发的过程化生成工具 werkkzeug
// 让艺术家能可视化编辑过程化场景
// 最终编译为 64KB 可执行文件

Scene {
    Generator: PerlinNoise(scale=0.1)
    Filter:    Smooth(iterations=3)
    Mesh:      MarchingCubes(threshold=0.5)
    Texture:   Procedural(marble_pattern)
    Light:     Directional(dir=(0.5, -1, 0.3))
    Camera:    Orbit(radius=10, speed=0.5)
}
```

### 总结

- 传奇作品定义了时代：Second Reality（PC 奠基）、.the .product（64K 开创）、Eos（4K SDF）、H - Immersion（视觉巅峰）。
- 传奇 Group：Future Crew、Farbrausch、Conspiracy、TBC、Alcatraz 等。
- **学习路径**：观看这些作品（YouTube 或 pouet.net），分析其技术与设计。
- **资源**：pouet.net 是 DemoScene 作品数据库，scene.org 存档历史作品。
- **精神传承**：DemoScene 强调开源分享，许多 Group 公开工具与代码。

---

## 第 04 讲 · 文化精神与艺术价值

### 概念

DemoScene 不仅是技术展示，更是一种独特的数字艺术形式与文化精神。其核心精神包括：**极限挑战**（在限制中追求极致）、**过程化思维**（用代码生成而非手工制作）、**实时性**（一切计算在运行时完成）、**社区分享**（开源与传承）。

DemoScene 的艺术价值在于：它是纯粹的"代码即艺术"——创作者用数学与算法表达美学，无需商业妥协。

### 原理

**DemoScene 的核心精神**：

1. **极限挑战（Pushing Limits）**

   DemoScene 的本质是"在限制中追求极致"。4KB 能做什么？64KB 能做什么？老硬件能做什么？这种限制驱动创新——Farbrausch 用 64KB 生成完整城市，TBC 用 4KB 渲染 3D 场景，都是限制催生的奇迹。

2. **过程化思维（Procedural Thinking）**

   传统游戏预先制作资产（贴图、模型、音乐），DemoScene 则用代码实时生成一切。这不仅是压缩需要，更是一种美学——过程化生成的图像有独特的"数学之美"，手工无法复制。

3. **实时性（Real-time）**

   Demo 必须实时运行，不能预渲染。这意味着所有效果都在 CPU/GPU 实时计算。实时性让 Demo 有"生命力"——每次运行都略有不同，响应硬件与时间。

4. **社区分享（Sharing & Heritage）**

   DemoScene 社区高度开放，Group 之间互相学习，工具与代码经常开源。这种分享精神让技术快速迭代，新人能站在前人基础上。

**艺术价值**：

1. **代码即艺术**

   DemoScene 证明了代码不仅是工具，更是艺术媒介。一段 GLSL 着色器可以生成令人惊叹的视觉，一首跟踪器音乐可以是数学与美学的完美结合。

2. **数学之美**

   DemoScene 大量使用数学：正弦波、噪声、分形、SDF。这些数学工具在 Demo 中化为视觉诗篇——等离子效果是正弦波的交响，分形树是递归的具象。

3. **限制即自由**

   4KB 的限制看似束缚，实则解放创造力。当不能依赖预制作资产，创作者必须深入理解图形本质，用数学直接表达。这种"限制即自由"的哲学是 DemoScene 的核心智慧。

4. **反商业美学**

   DemoScene 不追求商业利益，因此能探索商业游戏不会尝试的美学：抽象、极简、故障、复古。这种自由让 DemoScene 成为数字艺术的实验场。

**DemoScene 对主流的影响**：

- **过程化生成**：No Man's Sky、Minecraft 借鉴 DemoScene 的过程化技术。
- **着色器艺术**：Shadertoy 社区直接继承 DemoScene 的着色器传统。
- **游戏开发**：许多游戏开发者来自 DemoScene，带来极致优化文化。
- **视觉特效**：音乐节、演唱会的视觉特效受 DemoScene 启发。

### 例子

```
DemoScene 精神的具体体现

1. 极限挑战：
   "4KB 能做什么？"
   └── Eos (TBC, 2010): 4KB 内渲染完整 3D 场景
       - SDF 建模
       - Ray Marching 渲染
       - 软阴影
       - 体积雾
       - 程序化纹理

2. 过程化思维：
   "不存储任何资产，全部代码生成"
   └── Chaos Theory (Farbrausch, 2004): 64KB 完整城市
       - 建筑几何：L-System 生成
       - 街道布局：Voronoi 图
       - 纹理：Perlin Noise + 过程化
       - 音乐：合成器实时生成

3. 实时性：
   "每次运行都是现场表演"
   └── 所有 Demo 都实时渲染
       - 无预渲染帧
       - 响应硬件性能
       - 可交互（部分 Demo）

4. 社区分享：
   "站在前人基础上"
   └── 开源工具与代码
       - Farbrausch 的 werkkzeug
       - Inigo Quilez 的 Shadertoy 教程
       -各种压缩器（Crinkler、UPX）
```

**DemoScene 的"问候"文化**：

```c
// Demo 结尾的滚动字幕，向其他 Group 致敬
// 这是 DemoScene 的传统，体现社区精神
const char* greetings = 
    "GREETINGS TO:\n"
    "FARBR AUSCH - CONSPIRACY - ANDROMEDA - TBC\n"
    "ALCATRAZ - LOONIES - FAIRLIGHT - TRBL\n"
    "AND ALL THE SCENERS WE FORGOT...\n"
    "SEE YOU AT REVISION 2025!";
```

### 总结

- DemoScene 核心精神：极限挑战、过程化思维、实时性、社区分享。
- **艺术价值**：代码即艺术、数学之美、限制即自由、反商业美学。
- DemoScene 影响了主流游戏开发、着色器艺术、视觉特效。
- **学习态度**：不要只学技术，要理解文化——为什么 DemoScene 程序员会为节省一个字节而欣喜若狂。
- **参与方式**：观看作品、参加 Party、加入 Group、发布作品。

---

# 第 2 章 · 图形基础

DemoScene 的所有视觉效果最终都归结为"如何高效地操作像素"。本章回到图形学的最底层：帧缓冲与像素操作、调色板与颜色循环、VGA Mode 13h 与老硬件技巧、固定时间步长与垂直同步。这些是 DemoScene 的根基——理解它们，你才能理解为什么老 Demo 程序员对"每帧 16.67 毫秒"有近乎宗教般的执着。

## 第 05 讲 · 帧缓冲与像素操作

### 概念

**帧缓冲（Frame Buffer）** 是一块存储屏幕像素数据的内存区域。在 DOS 时代，VGA Mode 13h 的帧缓冲位于物理地址 `0xA0000`，320×200 像素，每像素 1 字节（索引颜色）。直接写入这块内存，屏幕立即显示对应像素。

**像素操作（Pixel Operation）** 是所有图形效果的基础——无论是等离子、火焰还是隧道，最终都是修改帧缓冲中的像素值。DemoScene 程序员对像素操作的优化达到极致：查表、位运算、汇编内联，只为每帧多处理几千个像素。

### 原理

**帧缓冲的内存布局**：

```
VGA Mode 13h 帧缓冲：
  物理地址：0xA0000
  尺寸：320 × 200 = 64000 字节
  布局：线性，pixel(x,y) 在 0xA0000 + y*320 + x

写入像素：
  *(0xA0000 + y*320 + x) = color_index;
```

**现代等价物**：

现代系统不再允许直接写物理内存，但概念相同。SDL2 用 `SDL_Surface` 或 `SDL_Texture` 作为帧缓冲：

```c
// SDL2 软件帧缓冲
SDL_Surface* surface = SDL_CreateRGBSurface(0, 320, 200, 32, ...);
Uint32* pixels = (Uint32*)surface->pixels;
pixels[y * 320 + x] = color;  // 写入像素
```

**像素操作的性能**：

每帧 64000 像素（320×200），60 FPS 下每秒 3.84M 像素操作。每个操作若多花 10 纳秒，总开销就是 38ms——超过帧预算。因此 DemoScene 程序员极度优化像素循环：

1. **查表预计算**：正弦、颜色映射等预计算为查找表。
2. **位运算代替乘除**：`x >> 8` 代替 `x / 256`。
3. **定点运算**：用整数模拟浮点，避免 FPU 开销。
4. **汇编内联**：关键循环用手工汇编优化。

**双缓冲（Double Buffering）**：

直接写帧缓冲会导致"撕裂"——屏幕刷新时像素正在被修改。双缓冲用两块内存：一块显示，一块绘制，绘制完成后整体复制（Flip）：

```
1. 在后备缓冲绘制
2. 等待垂直同步（VSync）
3. 后备缓冲 → 前台缓冲（Flip）
4. 重复
```

### 例子

```c
#include <SDL2/SDL.h>
#include <cstdint>

// 软件帧缓冲示例
class FrameBuffer {
public:
    int width, height;
    Uint32* pixels;
    SDL_Surface* surface;

    FrameBuffer(int w, int h) : width(w), height(h) {
        surface = SDL_CreateRGBSurface(0, w, h, 32,
            0xFF0000, 0xFF00, 0xFF, 0xFF000000);
        pixels = (Uint32*)surface->pixels;
    }

    ~FrameBuffer() {
        SDL_FreeSurface(surface);
    }

    // 写入单个像素
    void put_pixel(int x, int y, Uint32 color) {
        if (x >= 0 && x < width && y >= 0 && y < height) {
            pixels[y * width + x] = color;
        }
    }

    // 清屏
    void clear(Uint32 color) {
        for (int i = 0; i < width * height; ++i) {
            pixels[i] = color;
        }
    }

    // 水平线（优化版，memset 比 put_pixel 快）
    void hline(int x1, int x2, int y, Uint32 color) {
        if (y < 0 || y >= height) return;
        if (x1 > x2) { int t = x1; x1 = x2; x2 = t; }
        x1 = std::max(0, x1);
        x2 = std::min(width - 1, x2);
        Uint32* p = pixels + y * width + x1;
        for (int x = x1; x <= x2; ++x) *p++ = color;
    }

    // 垂直线
    void vline(int x, int y1, int y2, Uint32 color) {
        if (x < 0 || x >= width) return;
        if (y1 > y2) { int t = y1; y1 = y2; y2 = t; }
        y1 = std::max(0, y1);
        y2 = std::min(height - 1, y2);
        Uint32* p = pixels + y1 * width + x;
        for (int y = y1; y <= y2; ++y) {
            *p = color;
            p += width;
        }
    }

    // 矩形填充
    void fill_rect(int x, int y, int w, int h, Uint32 color) {
        for (int dy = 0; dy < h; ++dy) {
            hline(x, x + w - 1, y + dy, color);
        }
    }
};

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Frame Buffer",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 640, 400, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, 0);
    SDL_Texture* texture = SDL_CreateTexture(renderer,
        SDL_PIXELFORMAT_RGBA8888, SDL_TEXTUREACCESS_STREAMING, 320, 200);

    FrameBuffer fb(320, 200);

    bool running = true;
    int t = 0;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) if (e.type == SDL_QUIT) running = false;

        // 绘制：简单的动画
        fb.clear(0xFF000000);  // 黑色

        // 移动的方块
        int x = 160 + (int)(100 * sin(t * 0.05));
        int y = 100 + (int)(50 * cos(t * 0.07));
        fb.fill_rect(x - 20, y - 20, 40, 40, 0xFFFF0000);

        // 水平线扫描
        fb.hline(0, 319, (t * 2) % 200, 0xFF00FF00);

        // 像素雨
        for (int i = 0; i < 100; ++i) {
            int px = (i * 37 + t * 3) % 320;
            int py = (i * 71 + t * 5) % 200;
            fb.put_pixel(px, py, 0xFFFFFFFF);
        }

        t++;

        // 上传到纹理并渲染
        SDL_UpdateTexture(texture, nullptr, fb.pixels, 320 * 4);
        SDL_RenderClear(renderer);
        SDL_RenderCopy(renderer, texture, nullptr, nullptr);
        SDL_RenderPresent(renderer);
    }

    SDL_DestroyTexture(texture);
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- 帧缓冲是存储屏幕像素的内存，DOS 时代在 0xA0000，现代用 SDL_Surface。
- 像素操作是所有效果的基础，每秒数百万次操作需极致优化。
- **优化技巧**：查表、位运算、定点运算、汇编内联。
- **双缓冲**避免撕裂：后备缓冲绘制，VSync 后整体 Flip。
- **常见坑**：忘记锁屏导致撕裂；逐像素 put_pixel 比 memset 慢得多。

---

## 第 06 讲 · 调色板与颜色循环

### 概念

**调色板（Palette）** 是 8 位索引颜色系统的核心——帧缓冲存储颜色索引（0-255），调色板把索引映射为 RGB。VGA Mode 13h 有 256 个调色板寄存器，每个 18 位（6 位 RGB）。

**颜色循环（Color Cycling）** 是 DemoScene 的经典技巧——修改调色板而非像素，让画面"动起来"。一幅静态像素图，通过循环调色板，能产生流动的瀑布、燃烧的火焰等效果，几乎零计算开销。

### 原理

**调色板的工作原理**：

```
帧缓冲：[5, 5, 10, 10, 200, 200, ...]  (索引)
              ↓ 查调色板
调色板：[0]=黑, [1]=蓝, ..., [5]=红, [10]=橙, [200]=白
              ↓
屏幕：红, 红, 橙, 橙, 白, 白, ...
```

**VGA 调色板编程**：

```c
// 设置调色板寄存器（DOS）
void set_palette(int index, int r, int g, int b) {
    outp(0x3C8, index);   // 调色板索引端口
    outp(0x3C9, r >> 2);  // R（6 位，0-63）
    outp(0x3C9, g >> 2);  // G
    outp(0x3C9, b >> 2);  // B
}
```

**颜色循环**：

```c
// 循环调色板：把 [0..255] 整体左移一位
void cycle_palette() {
    // 保存颜色 0
    int r = palette[0].r, g = palette[0].g, b = palette[0].b;
    // 所有颜色前移
    for (int i = 0; i < 255; ++i) {
        palette[i] = palette[i + 1];
    }
    palette[255] = {r, g, b};
    // 应用到硬件
    for (int i = 0; i < 256; ++i) {
        set_palette(i, palette[i].r, palette[i].g, palette[i].b);
    }
}
```

**颜色循环的视觉效果**：

如果一幅图用调色板索引 0-255 表示"温度"（0=黑，128=红，255=白），循环调色板后，温度"流动"——原本的红色区域变成橙色，橙色区域变成黄色，仿佛火焰在燃烧。而像素数据完全不变！

**经典应用**：

1. **瀑布**：静态绘制水流的"高度图"，调色板按高度着色，循环调色板产生流动感。
2. **火焰**：静态绘制火焰的"温度图"，循环调色板产生燃烧感。
3. **等离子**：静态计算等离子函数，循环调色板产生色彩流动。
4. **等高线**：地形等高线图，循环调色板产生"扫描"效果。

**现代实现**：

现代系统用 32 位真彩色，无硬件调色板。但可用着色器模拟：

```glsl
// GLSL 模拟调色板循环
uniform sampler2D indexTexture;  // 索引图
uniform sampler2D palette;       // 调色板纹理
uniform float cycleOffset;

void main() {
    float index = texture(indexTexture, uv).r;  // 0-1
    float cycled = mod(index + cycleOffset, 1.0);
    gl_FragColor = texture(palette, vec2(cycled, 0));
}
```

### 例子

```c
#include <SDL2/SDL.h>
#include <cmath>

// 调色板系统
struct Palette {
    SDL_Color colors[256];

    // 生成火焰调色板
    void init_fire() {
        for (int i = 0; i < 256; ++i) {
            if (i < 64) {  // 黑→红
                colors[i] = {(Uint8)(i * 4), 0, 0, 255};
            } else if (i < 128) {  // 红→橙
                colors[i] = {255, (Uint8)((i - 64) * 4), 0, 255};
            } else if (i < 192) {  // 橙→黄
                colors[i] = {255, 255, (Uint8)((i - 128) * 4), 255};
            } else {  // 黄→白
                colors[i] = {255, 255, 255, 255};
            }
        }
    }

    // 生成等离子调色板（HSV 循环）
    void init_plasma() {
        for (int i = 0; i < 256; ++i) {
            float h = i / 256.0f * 360.0f;
            float s = 0.7f, v = 0.9f;
            // HSV → RGB（简化）
            float c = v * s;
            float x = c * (1 - fabs(fmod(h / 60, 2) - 1));
            float m = v - c;
            float r, g, b;
            if (h < 60) { r = c; g = x; b = 0; }
            else if (h < 120) { r = x; g = c; b = 0; }
            else if (h < 180) { r = 0; g = c; b = x; }
            else if (h < 240) { r = 0; g = x; b = c; }
            else if (h < 300) { r = x; g = 0; b = c; }
            else { r = c; g = 0; b = x; }
            colors[i] = {
                (Uint8)((r + m) * 255),
                (Uint8)((g + m) * 255),
                (Uint8)((b + m) * 255),
                255
            };
        }
    }

    // 循环调色板
    void cycle(int shift = 1) {
        SDL_Color temp[256];
        for (int i = 0; i < 256; ++i) {
            temp[i] = colors[(i + shift) % 256];
        }
        for (int i = 0; i < 256; ++i) {
            colors[i] = temp[i];
        }
    }
};

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Palette & Color Cycling",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 640, 400, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, 0);
    SDL_Texture* texture = SDL_CreateTexture(renderer,
        SDL_PIXELFORMAT_RGBA8888, SDL_TEXTUREACCESS_STREAMING, 320, 200);

    Palette palette;
    palette.init_plasma();

    // 预计算索引图（等离子）
    Uint8 indices[320 * 200];
    for (int y = 0; y < 200; ++y) {
        for (int x = 0; x < 320; ++x) {
            float fx = x / 32.0f, fy = y / 32.0f;
            float v = sin(fx) + sin(fy) + sin((fx + fy) * 0.7f) + sin(sqrtf(fx*fx + fy*fy) * 2);
            indices[y * 320 + x] = (Uint8)((v + 4) / 8 * 255);
        }
    }

    Uint32 framebuffer[320 * 200];

    bool running = true;
    int t = 0;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) if (e.type == SDL_QUIT) running = false;

        // 颜色循环：每帧调色板移动一位
        palette.cycle(1);

        // 用当前调色板渲染索引图
        for (int i = 0; i < 320 * 200; ++i) {
            SDL_Color c = palette.colors[indices[i]];
            framebuffer[i] = (c.a << 24) | (c.r << 16) | (c.g << 8) | c.b;
        }

        SDL_UpdateTexture(texture, nullptr, framebuffer, 320 * 4);
        SDL_RenderClear(renderer);
        SDL_RenderCopy(renderer, texture, nullptr, nullptr);
        SDL_RenderPresent(renderer);

        t++;
        SDL_Delay(16);
    }

    SDL_DestroyTexture(texture);
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- 调色板是 8 位索引颜色系统，帧缓冲存索引，调色板映射为 RGB。
- **颜色循环**：修改调色板而非像素，让静态图"动起来"，几乎零开销。
- 经典应用：瀑布、火焰、等离子、等高线扫描。
- 现代用着色器模拟调色板循环。
- **性能优势**：像素数据不变，只改 256 个调色板寄存器，极快。
- **常见坑**：调色板循环方向错误导致反向流动；现代系统无硬件调色板需软件模拟。

---

## 第 07 讲 · VGA Mode 13h 与老硬件技巧

### 概念

**VGA Mode 13h** 是 DemoScene 历史上最经典的图形模式——320×200 分辨率，256 色调色板，线性帧缓冲。1990 年代几乎所有 PC Demo 都基于此模式。Mode 13h 的简单性（线性内存、无位平面）让它成为软件渲染的理想平台。

**老硬件技巧**是 DemoScene 程序员在有限硬件上榨取性能的智慧结晶：铜管效果（Copper Bar）、光栅中断（Raster Interrupt）、Mode X（位平面技巧）、汇编优化。理解这些技巧，能让你体会"极限优化"的精神。

### 原理

**Mode 13h 的设置**：

```c
// DOS 下设置 Mode 13h
void set_mode_13h() {
    union REGS r;
    r.x.ax = 0x0013;  // AH=0, AL=0x13
    int86(0x10, &r, &r);  // 调用 BIOS 视频中断
}

// 返回文本模式
void set_text_mode() {
    union REGS r;
    r.x.ax = 0x0003;
    int86(0x10, &r, &r);
}
```

**Mode 13h 的特性**：

- 分辨率：320×200
- 颜色：256 色调色板
- 帧缓冲地址：0xA0000
- 布局：线性，每像素 1 字节
- 显存：256KB（Mode 13h 只用 64KB）

**Mode X（Mode-Y）**：

Mode 13h 虽简单，但无法硬件滚动、无法分页。Mode X 是通过修改 VGA 寄存器解锁的"隐藏模式"，使用 4 个位平面，能硬件滚动、分页，性能更高。许多高级 Demo 用 Mode X。

**铜管效果（Copper Bar）**：

源自 Amiga 的 Copper（协处理器），在 PC 上用光栅中断模拟。每条扫描线开始时触发中断，修改调色板，产生彩色横条：

```c
// 伪代码：光栅中断改变调色板
void raster_interrupt(int line) {
    if (line == 50) set_palette(0, 255, 0, 0);    // 红条
    if (line == 100) set_palette(0, 0, 255, 0);   // 绿条
    if (line == 150) set_palette(0, 0, 0, 255);   // 蓝条
}
```

**垂直同步（VSync）等待**：

```c
// 等待垂直同步，避免撕裂
void wait_vsync() {
    while ((inp(0x3DA) & 8) == 0);  // 等待 VSync 开始
    while ((inp(0x3DA) & 8) != 0);  // 等待 VSync 结束
}
```

**汇编优化的像素绘制**：

```asm
; 8086 汇编：快速绘制水平线
; ES:DI = 目标地址, DX = 颜色, CX = 长度
draw_hline:
    rep stosb       ; AL → ES:DI, DI++, CX--，重复 CX 次
    ret
```

`rep stosb` 是 8086 的字符串指令，一次 CPU 周期写一个字节，比 C 循环快数倍。

**老硬件技巧的现代意义**：

虽然现代硬件不再需要这些技巧，但其精神——"理解硬件、榨取性能"——依然重要。现代等价物：

- VSync → SDL 的 `SDL_RENDERER_PRESENTVSYNC`
- 光栅中断 → 着色器中的 `gl_FragCoord.y`
- 汇编优化 → SIMD（SSE/AVX）、GPU 着色器

### 例子

```c
#include <SDL2/SDL.h>
#include <cmath>

// 模拟 Mode 13h 的环境
class Mode13h {
public:
    static const int WIDTH = 320;
    static const int HEIGHT = 200;
    Uint8 framebuffer[WIDTH * HEIGHT];
    SDL_Color palette[256];

    void set_palette_color(int index, int r, int g, int b) {
        palette[index] = {(Uint8)r, (Uint8)g, (Uint8)b, 255};
    }

    void put_pixel(int x, int y, Uint8 color) {
        if (x >= 0 && x < WIDTH && y >= 0 && y < HEIGHT) {
            framebuffer[y * WIDTH + x] = color;
        }
    }

    // 模拟铜管效果（用调色板产生彩色横条）
    void copper_bars(int t) {
        for (int y = 0; y < HEIGHT; ++y) {
            // 每行调色板颜色随时间变化
            int r = (int)(127 + 127 * sin(y * 0.1 + t * 0.05));
            int g = (int)(127 + 127 * sin(y * 0.15 + t * 0.07));
            int b = (int)(127 + 127 * sin(y * 0.2 + t * 0.03));
            set_palette_color(1, r, g, b);
            // 绘制一行
            for (int x = 0; x < WIDTH; ++x) {
                put_pixel(x, y, 1);
            }
        }
    }

    // 转换为 32 位 RGBA 用于 SDL 渲染
    void to_rgba(Uint32* dest) {
        for (int i = 0; i < WIDTH * HEIGHT; ++i) {
            SDL_Color c = palette[framebuffer[i]];
            dest[i] = (c.a << 24) | (c.r << 16) | (c.g << 8) | c.b;
        }
    }
};

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Mode 13h Emulation",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 640, 400, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, 0);
    SDL_Texture* texture = SDL_CreateTexture(renderer,
        SDL_PIXELFORMAT_RGBA8888, SDL_TEXTUREACCESS_STREAMING, 320, 200);

    Mode13h mode;
    // 初始化调色板
    for (int i = 0; i < 256; ++i) {
        mode.set_palette_color(i, i, i, i);  // 灰度
    }

    Uint32 rgba_buffer[320 * 200];

    bool running = true;
    int t = 0;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) if (e.type == SDL_QUIT) running = false;

        // 铜管效果
        mode.copper_bars(t);

        mode.to_rgba(rgba_buffer);
        SDL_UpdateTexture(texture, nullptr, rgba_buffer, 320 * 4);
        SDL_RenderClear(renderer);
        SDL_RenderCopy(renderer, texture, nullptr, nullptr);
        SDL_RenderPresent(renderer);

        t++;
        SDL_Delay(16);
    }

    SDL_DestroyTexture(texture);
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- VGA Mode 13h 是 DemoScene 经典模式：320×200，256 色，线性帧缓冲。
- Mode X 是解锁的隐藏模式，支持硬件滚动与分页。
- **铜管效果**用光栅中断改变调色板，产生彩色横条。
- **VSync 等待**避免撕裂，是 DemoScene 的基本功。
- **汇编优化**：`rep stosb` 等字符串指令比 C 循环快数倍。
- **现代意义**：理解硬件、榨取性能的精神依然重要，只是工具变为 SIMD 与 GPU。

---

## 第 08 讲 · 固定时间步长与垂直同步

### 概念

**固定时间步长（Fixed Time Step）** 是 DemoScene 与游戏开发的基石——物理与动画以固定频率更新（如 60Hz），与渲染帧率解耦。这保证 Demo 在不同硬件上行为一致：60Hz 显示器与 144Hz 显示器看到相同的动画速度。

**垂直同步（VSync）** 同步帧缓冲翻转与显示器刷新，避免撕裂。DemoScene 程序员对 VSync 有近乎宗教般的执着——"错过一个 VSync 就是失败"。

### 原理

**为什么需要固定时间步长**：

如果动画用"每帧移动 1 像素"，60FPS 下每秒移动 60 像素，30FPS 下只有 30 像素——硬件越快动画越快，这是错误的。固定时间步长解耦更新频率与渲染频率：

```
update(dt):  // dt 是固定值，如 1/60 秒
    position += velocity * dt

render():
    draw(position)
```

**累加器模式**：

```c
const float FIXED_DT = 1.0f / 60.0f;
float accumulator = 0.0f;

while (running) {
    float frame_time = get_frame_time();
    accumulator += frame_time;

    while (accumulator >= FIXED_DT) {
        update(FIXED_DT);
        accumulator -= FIXED_DT;
    }

    render();
}
```

**VSync 的工作原理**：

CRT 显示器每秒刷新 60 次（60Hz），每次刷新从上到下扫描。如果在扫描过程中翻转帧缓冲，上半屏是旧帧、下半屏是新帧——撕裂。

VSync 让帧缓冲翻转等待下一次垂直同步信号，保证整屏同时更新：

```
无 VSync：
  帧 1 绘制中 → 翻转 → 帧 2 绘制中 → 翻转 → ...
  （可能撕裂）

有 VSync：
  帧 1 绘制 → 等待 VSync → 翻转 → 帧 2 绘制 → 等待 VSync → ...
  （无撕裂，但帧率被限制为刷新率的整数倍）
```

**DemoScene 的 VSync 执念**：

老 Demo 程序员用 `while ((inp(0x3DA) & 8) == 0);` 等待 VSync，确保每帧恰好 1/60 秒。错过 VSync（绘制太慢）意味着帧率减半（30FPS），是 Demo 程序员的耻辱。

**现代 VSync**：

- **VSync On**：`SDL_RENDERER_PRESENTVSYNC` 标志，帧率限制为刷新率。
- **Adaptive VSync**：帧率低于刷新率时关闭 VSync，避免卡顿。
- **G-Sync / FreeSync**：显示器自适应刷新率，完美解决撕裂与卡顿。

**帧率独立性**：

Demo 应在不同帧率下表现一致。用 dt 缩放所有运动：

```c
// 错误：依赖帧率
position += 1;  // 60FPS 下每秒 60，144FPS 下每秒 144

// 正确：用 dt 缩放
position += 60 * dt;  // 任何帧率下每秒 60
```

### 例子

```c
#include <SDL2/SDL.h>
#include <cmath>

class DemoTimer {
public:
    float fixed_dt = 1.0f / 60.0f;
    float accumulator = 0.0f;
    Uint64 last_time;
    float time_scale = 1.0f;  // 慢动作用
    int fps = 0;
    int frame_count = 0;
    float fps_timer = 0;

    DemoTimer() {
        last_time = SDL_GetPerformanceCounter();
    }

    float get_frame_time() {
        Uint64 now = SDL_GetPerformanceCounter();
        float frame_time = (float)(now - last_time) / SDL_GetPerformanceFrequency();
        last_time = now;
        frame_time *= time_scale;
        if (frame_time > 0.25f) frame_time = 0.25f;  // 防止大跳变
        return frame_time;
    }

    // 返回是否应该执行固定更新
    bool should_update() {
        return accumulator >= fixed_dt;
    }

    void consume() {
        accumulator -= fixed_dt;
    }

    void add(float time) {
        accumulator += time;
        frame_count++;
        fps_timer += time;
        if (fps_timer >= 1.0f) {
            fps = frame_count;
            frame_count = 0;
            fps_timer = 0;
        }
    }
};

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Fixed Time Step",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 640, 400, 0);
    // 启用 VSync
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1,
        SDL_RENDERER_ACCELERATED | SDL_RENDERER_PRESENTVSYNC);
    SDL_Texture* texture = SDL_CreateTexture(renderer,
        SDL_PIXELFORMAT_RGBA8888, SDL_TEXTUREACCESS_STREAMING, 320, 200);

    DemoTimer timer;
    Uint32 framebuffer[320 * 200];

    float position = 0;

    bool running = true;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) if (e.type == SDL_QUIT) running = false;

        float frame_time = timer.get_frame_time();
        timer.add(frame_time);

        // 固定时间步长更新
        while (timer.should_update()) {
            position += 60 * timer.fixed_dt;  // 每秒 60 像素
            if (position > 320) position = 0;
            timer.consume();
        }

        // 渲染
        SDL_SetRenderDrawColor(renderer, 0, 0, 0, 255);
        SDL_RenderClear(renderer);

        // 清空帧缓冲
        for (int i = 0; i < 320 * 200; ++i) framebuffer[i] = 0xFF000000;

        // 绘制移动的方块（位置由固定更新决定）
        int px = (int)position;
        for (int y = 90; y < 110; ++y) {
            for (int x = px - 10; x < px + 10; ++x) {
                if (x >= 0 && x < 320) {
                    framebuffer[y * 320 + x] = 0xFFFF0000;
                }
            }
        }

        SDL_UpdateTexture(texture, nullptr, framebuffer, 320 * 4);
        SDL_RenderCopy(renderer, texture, nullptr, nullptr);
        SDL_RenderPresent(renderer);
    }

    SDL_DestroyTexture(texture);
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- 固定时间步长解耦更新频率与渲染帧率，保证 Demo 在不同硬件上行为一致。
- **累加器模式**：累积帧时间，达到固定步长时执行更新。
- **VSync** 同步帧缓冲翻转与显示器刷新，避免撕裂。
- DemoScene 程序员对 VSync 极度执着——错过 VSync 是耻辱。
- **帧率独立性**：所有运动用 `dt` 缩放，不依赖帧率。
- **常见坑**：忘记限制 frame_time 最大值导致大跳变；VSync 关闭导致撕裂。

---

# 第 3 章 · 过程化生成

过程化生成（Procedural Generation）是 DemoScene 的灵魂——用代码生成无限内容，而非存储预制作资产。一个 4KB Intro 能展现完整城市、山脉、星空，全靠过程化。本章从随机数与噪声开始，逐步讲解 Value Noise、Perlin Noise、L-System、元球等技术。掌握这些，你就能用几行代码生成整个世界。

## 第 09 讲 · 随机数与噪声

### 概念

**随机数（Random Number）** 是过程化生成的基础——地形、纹理、粒子都需要随机性。但纯随机（白噪声）过于杂乱，不适合自然现象。DemoScene 需要**可控的随机**——既随机又有结构。

**噪声（Noise）** 是可控随机的数学工具。理想的噪声函数具有：连续性（相邻输入产生相近输出）、随机性（宏观看似无规律）、可重复性（相同输入产生相同输出）。

### 原理

**伪随机数生成器（PRNG）**：

纯随机数用 PRNG 生成，常用线性同余法：

```
X(n+1) = (a * X(n) + c) mod m
```

经典实现（C 标准库 rand）：

```c
// 简单 LCG
unsigned int seed = 12345;
unsigned int rand_lcg() {
    seed = seed * 1103515245 + 12345;
    return (seed / 65536) % 32768;
}
```

**DemoScene 常用 PRNG**：

1. **xorshift**：极快，适合 GPU。
2. **PCG**：质量高，统计性好。
3. **Mersenne Twister**：周期极长，但较慢。

```c
// xorshift32（DemoScene 最爱，4 字节状态）
uint32_t xorshift32(uint32_t* state) {
    uint32_t x = *state;
    x ^= x << 13;
    x ^= x >> 17;
    x ^= x << 5;
    *state = x;
    return x;
}
```

**白噪声**：

纯随机数映射到像素，产生"电视雪花"：

```c
for each pixel (x, y):
    value = rand() % 256
    put_pixel(x, y, value)
```

白噪声过于杂乱，不适合自然纹理。

**值噪声（Value Noise）**：

值噪声在网格点随机取值，中间用插值平滑：

```
1. 在整数网格点 (i, j) 赋随机值 v(i, j)
2. 对任意点 (x, y)，找其所在网格单元
3. 用双线性插值计算该点的值
```

值噪声连续平滑，但方块感明显。

**梯度噪声（Gradient Noise）**：

Perlin Noise 是梯度噪声的代表——在网格点赋随机梯度（方向），用梯度计算插值，产生更自然的纹理。

**噪声的频率与振幅**：

- 频率：噪声变化的快慢，高频=细节多，低频=大尺度。
- 振幅：噪声的幅度，高振幅=变化大。

**分形布朗运动（fBm）**：

叠加多个不同频率的噪声（倍频），产生分形细节：

```
fBm(x) = noise(x) + 0.5 * noise(2x) + 0.25 * noise(4x) + ...
```

每次频率翻倍（倍频），振幅减半。fBm 是地形、云彩等自然纹理的基础。

### 例子

```c
#include <SDL2/SDL.h>
#include <cmath>
#include <cstdint>

// xorshift32 PRNG
uint32_t rng_state = 12345;
uint32_t xorshift32() {
    uint32_t x = rng_state;
    x ^= x << 13; x ^= x >> 17; x ^= x << 5;
    rng_state = x;
    return x;
}
float frand() { return xorshift32() / (float)UINT32_MAX; }

// 哈希函数：把整数坐标映射为随机值（0-1）
float hash2(int x, int y) {
    uint32_t h = (uint32_t)x * 374761393 + (uint32_t)y * 668265263;
    h = (h ^ (h >> 13)) * 1274126177;
    return (h ^ (h >> 16)) / (float)UINT32_MAX;
}

// 平滑插值（smoothstep）
float smoothstep(float t) { return t * t * (3 - 2 * t); }

// 值噪声
float value_noise(float x, float y) {
    int xi = (int)floor(x), yi = (int)floor(y);
    float xf = x - xi, yf = y - yi;

    float v00 = hash2(xi, yi);
    float v10 = hash2(xi + 1, yi);
    float v01 = hash2(xi, yi + 1);
    float v11 = hash2(xi + 1, yi + 1);

    float u = smoothstep(xf);
    float v = smoothstep(yf);

    return (1-u)*(1-v)*v00 + u*(1-v)*v10 + (1-u)*v*v01 + u*v*v11;
}

// 分形布朗运动（fBm）
float fbm(float x, float y, int octaves) {
    float total = 0, amplitude = 1, frequency = 1, max = 0;
    for (int i = 0; i < octaves; ++i) {
        total += value_noise(x * frequency, y * frequency) * amplitude;
        max += amplitude;
        amplitude *= 0.5;
        frequency *= 2;
    }
    return total / max;
}

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Noise",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 512, 512, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, 0);
    SDL_Texture* texture = SDL_CreateTexture(renderer,
        SDL_PIXELFORMAT_RGBA8888, SDL_TEXTUREACCESS_STREAMING, 256, 256);

    Uint32 framebuffer[256 * 256];

    bool running = true;
    int mode = 0;  // 0=白噪声, 1=值噪声, 2=fBm
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) {
            if (e.type == SDL_QUIT) running = false;
            if (e.type == SDL_KEYDOWN) {
                if (e.key.keysym.sym >= SDLK_1 && e.key.keysym.sym <= SDLK_3)
                    mode = e.key.keysym.sym - SDLK_1;
            }
        }

        // 生成噪声图
        for (int y = 0; y < 256; ++y) {
            for (int x = 0; x < 256; ++x) {
                float v;
                switch (mode) {
                    case 0: v = frand(); break;  // 白噪声
                    case 1: v = value_noise(x / 32.0f, y / 32.0f); break;
                    case 2: v = fbm(x / 32.0f, y / 32.0f, 5); break;
                }
                Uint8 c = (Uint8)(v * 255);
                framebuffer[y * 256 + x] = 0xFF000000 | (c << 16) | (c << 8) | c;
            }
        }

        SDL_UpdateTexture(texture, nullptr, framebuffer, 256 * 4);
        SDL_RenderClear(renderer);
        SDL_RenderCopy(renderer, texture, nullptr, nullptr);
        SDL_RenderPresent(renderer);
    }

    SDL_DestroyTexture(texture);
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- 随机数是过程化基础，但纯随机（白噪声）过于杂乱。
- **xorshift** 是 DemoScene 最爱的 PRNG，极快且适合 GPU。
- **值噪声**：网格点随机值 + 双线性插值，连续但方块感。
- **fBm**：叠加倍频噪声，产生分形细节，是地形、云彩基础。
- **哈希函数**：把整数坐标映射为伪随机值，无需存储，适合 GPU。
- **常见坑**：用 `rand()` 在不同平台结果不同；插值不用 smoothstep 导致方块感。

---

## 第 10 讲 · Value Noise 与 Perlin Noise

### 概念

**Value Noise** 在网格点赋随机值，插值平滑。简单但方块感明显。

**Perlin Noise** 由 Ken Perlin 于 1985 年发明，在网格点赋随机梯度（方向向量），用梯度点积计算插值。Perlin Noise 产生更自然的有机纹理，是电影特效、游戏地形的标准。

### 原理

**Value Noise 回顾**：

```
1. 网格点 (i, j) 赋随机值 v(i, j) ∈ [0, 1]
2. 点 (x, y) 在网格 (i, j) 到 (i+1, j+1) 内
3. 双线性插值：
   u = smoothstep(x - i), v = smoothstep(y - j)
   result = lerp(lerp(v00, v10, u), lerp(v01, v11, u), v)
```

**Perlin Noise 的核心思想**：

不在网格点赋值，而赋**梯度**（单位向量）。点 (x, y) 的值由周围 4 个梯度与该点到网格点的方向向量的点积决定：

```
1. 网格点 (i, j) 赋随机梯度 g(i, j)
2. 点 (x, y) 在网格 (i, j) 到 (i+1, j+1) 内
3. 计算 4 个点积：
   d00 = g(i, j) · (x-i, y-j)
   d10 = g(i+1, j) · (x-i-1, y-j)
   d01 = g(i, j+1) · (x-i, y-j-1)
   d11 = g(i+1, j+1) · (x-i-1, y-j-1)
4. 双线性插值（用 smoothstep）
```

**梯度表**：

Perlin 用预计算的梯度表（12 或 16 个方向），通过哈希索引：

```c
// 经典 Perlin 梯度表（2D 简化版）
vec2 gradients[8] = {
    {1, 0}, {-1, 0}, {0, 1}, {0, -1},
    {0.707, 0.707}, {-0.707, 0.707}, {0.707, -0.707}, {-0.707, -0.707}
};

vec2 get_gradient(int x, int y) {
    int h = hash(x, y) % 8;
    return gradients[h];
}
```

**Perlin vs Value Noise**：

| 特性 | Value Noise | Perlin Noise |
|------|-------------|--------------|
| 网格点存储 | 随机值 | 随机梯度 |
| 计算 | 双线性插值 | 点积 + 插值 |
| 视觉 | 方块感 | 有机自然 |
| 复杂度 | 低 | 中 |
| 适用 | 简单纹理 | 地形、云、大理石 |

**Perlin Noise 的应用**：

1. **地形高度图**：fBm(Perlin) 生成山脉。
2. **云彩**：fBm(Perlin) + 阈值。
3. **大理石纹理**：sin(x + Perlin * scale)。
4. **木材纹理**：sin(distance + Perlin * scale)。
5. **火焰**：Perlin + 时间偏移。

### 例子

```c
#include <SDL2/SDL.h>
#include <cmath>
#include <cstdint>

// 2D 向量
struct Vec2 { float x, y; };

// 梯度表
Vec2 gradients[8] = {
    {1, 0}, {-1, 0}, {0, 1}, {0, -1},
    {0.707f, 0.707f}, {-0.707f, 0.707f}, {0.707f, -0.707f}, {-0.707f, -0.707f}
};

// 哈希
uint32_t hash(int x, int y) {
    uint32_t h = (uint32_t)x * 374761393 + (uint32_t)y * 668265263;
    h = (h ^ (h >> 13)) * 1274126177;
    return h ^ (h >> 16);
}

Vec2 get_gradient(int x, int y) {
    return gradients[hash(x, y) % 8];
}

float dot(Vec2 a, Vec2 b) { return a.x * b.x + a.y * b.y; }

float smoothstep(float t) { return t * t * (3 - 2 * t); }
float lerp(float a, float b, float t) { return a + (b - a) * t; }

// Perlin Noise 2D
float perlin_noise(float x, float y) {
    int xi = (int)floor(x), yi = (int)floor(y);
    float xf = x - xi, yf = y - yi;

    Vec2 g00 = get_gradient(xi, yi);
    Vec2 g10 = get_gradient(xi + 1, yi);
    Vec2 g01 = get_gradient(xi, yi + 1);
    Vec2 g11 = get_gradient(xi + 1, yi + 1);

    Vec2 d00 = {xf, yf};
    Vec2 d10 = {xf - 1, yf};
    Vec2 d01 = {xf, yf - 1};
    Vec2 d11 = {xf - 1, yf - 1};

    float n00 = dot(g00, d00);
    float n10 = dot(g10, d10);
    float n01 = dot(g01, d01);
    float n11 = dot(g11, d11);

    float u = smoothstep(xf);
    float v = smoothstep(yf);

    return lerp(lerp(n00, n10, u), lerp(n01, n11, u), v) * 0.5f + 0.5f;
}

// fBm
float fbm(float x, float y, int octaves) {
    float total = 0, amp = 1, freq = 1, max = 0;
    for (int i = 0; i < octaves; ++i) {
        total += perlin_noise(x * freq, y * freq) * amp;
        max += amp;
        amp *= 0.5f;
        freq *= 2;
    }
    return total / max;
}

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Perlin Noise",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 512, 512, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, 0);
    SDL_Texture* texture = SDL_CreateTexture(renderer,
        SDL_PIXELFORMAT_RGBA8888, SDL_TEXTUREACCESS_STREAMING, 256, 256);

    Uint32 framebuffer[256 * 256];
    int mode = 0;  // 0=Perlin, 1=fBm, 2=大理石, 3=地形

    bool running = true;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) {
            if (e.type == SDL_QUIT) running = false;
            if (e.type == SDL_KEYDOWN) {
                if (e.key.keysym.sym >= SDLK_1 && e.key.keysym.sym <= SDLK_4)
                    mode = e.key.keysym.sym - SDLK_1;
            }
        }

        for (int y = 0; y < 256; ++y) {
            for (int x = 0; x < 256; ++x) {
                float fx = x / 32.0f, fy = y / 32.0f;
                float v;
                Uint8 r, g, b;
                switch (mode) {
                    case 0:  // Perlin
                        v = perlin_noise(fx, fy);
                        r = g = b = (Uint8)(v * 255);
                        break;
                    case 1:  // fBm
                        v = fbm(fx, fy, 5);
                        r = g = b = (Uint8)(v * 255);
                        break;
                    case 2:  // 大理石
                        v = sin((fx + fbm(fx, fy, 4) * 4) * 2);
                        v = v * 0.5f + 0.5f;
                        r = (Uint8)(v * 240);
                        g = (Uint8)(v * 240);
                        b = (Uint8)(v * 255);
                        break;
                    case 3:  // 地形
                        v = fbm(fx, fy, 6);
                        if (v < 0.4f) { r = 0; g = 50; b = 100; }       // 水
                        else if (v < 0.5f) { r = 200; g = 200; b = 100; } // 沙滩
                        else if (v < 0.7f) { r = 0; g = 150; b = 50; }   // 草地
                        else if (v < 0.85f) { r = 100; g = 70; b = 40; } // 山
                        else { r = 255; g = 255; b = 255; }              // 雪
                        break;
                }
                framebuffer[y * 256 + x] = 0xFF000000 | (r << 16) | (g << 8) | b;
            }
        }

        SDL_UpdateTexture(texture, nullptr, framebuffer, 256 * 4);
        SDL_RenderClear(renderer);
        SDL_RenderCopy(renderer, texture, nullptr, nullptr);
        SDL_RenderPresent(renderer);
    }

    SDL_DestroyTexture(texture);
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- Perlin Noise 在网格点赋梯度，用点积计算，产生有机自然纹理。
- Value Noise 方块感明显，Perlin Noise 更适合自然现象。
- **fBm** 叠加倍频 Perlin，产生分形细节。
- **应用**：地形、云、大理石、木材、火焰。
- **优化**：梯度表预计算，哈希索引，避免存储。
- **常见坑**：忘记把 Perlin 结果从 [-1,1] 映射到 [0,1]；octaves 过多导致性能下降。

---

## 第 11 讲 · L-System 与分形树

### 概念

**L-System（Lindenmayer System）** 是 Aristid Lindenmayer 于 1968 年提出的形式语法，用于模拟植物生长。它用字符串重写规则生成递归结构，能产生逼真的树木、蕨类、雪花。

**分形（Fractal）** 是自相似的几何结构——局部与整体相似。L-System 是生成分形的经典工具，分形树、科赫雪花、谢尔宾斯基三角形都可用 L-System 生成。

### 原理

**L-System 的组成**：

1. **字母表（Alphabet）**：可用符号，如 F, +, -, [, ]。
2. **公理（Axiom）**：初始字符串，如 "F"。
3. **规则（Rules）**：重写规则，如 F → F[+F]F[-F]F。

**字符串重写**：

从公理开始，每代应用所有规则：

```
公理: F
规则: F → F[+F]F[-F]F

第 0 代: F
第 1 代: F[+F]F[-F]F
第 2 代: F[+F]F[-F]F[+F[+F]F[-F]F]F[+F]F[-F]F[-F[+F]F[-F]F]F[+F]F[-F]F
...
```

**海龟图形（Turtle Graphics）**：

把字符串解释为海龟命令：

- **F**：前进画线。
- **f**：前进不画线。
- **+**：左转。
- **-**：右转。
- **[**：保存当前状态（位置+方向）入栈。
- **]**：恢复栈顶状态。

```
状态 = (位置, 方向, 长度, 角度)
F: 位置 += 方向 * 长度; 画线
+: 方向旋转 +角度
-: 方向旋转 -角度
[: push(状态)
]: 状态 = pop()
```

**经典 L-System 例子**：

1. **分形树**：
   - 公理：F
   - 规则：F → F[+F]F[-F]F
   - 角度：25°

2. **科赫雪花**：
   - 公理：F++F++F
   - 规则：F → F-F++F-F
   - 角度：60°

3. **谢尔宾斯基三角形**：
   - 公理：A
   - 规则：A → B-A-B, B → A+B+A
   - 角度：60°

4. **蕨类植物**：
   - 公理：X
   - 规则：X → F+[[X]-X]-F[-FX]+X, F → FF
   - 角度：25°

**参数化 L-System**：

高级 L-System 支持参数（如长度、角度随代数变化），产生更自然的生长效果。

### 例子

```c
#include <SDL2/SDL.h>
#include <string>
#include <vector>
#include <stack>
#include <cmath>

struct TurtleState {
    float x, y, angle, length;
};

// L-System 生成
std::string lsystem(const std::string& axiom,
                    const std::vector<std::pair<char, std::string>>& rules,
                    int iterations) {
    std::string current = axiom;
    for (int i = 0; i < iterations; ++i) {
        std::string next;
        for (char c : current) {
            bool replaced = false;
            for (const auto& rule : rules) {
                if (c == rule.first) {
                    next += rule.second;
                    replaced = true;
                    break;
                }
            }
            if (!replaced) next += c;
        }
        current = next;
    }
    return current;
}

// 海龟绘制
void draw_lsystem(SDL_Renderer* r, const std::string& s,
                  float start_x, float start_y, float start_angle,
                  float length, float angle_delta) {
    TurtleState state = {start_x, start_y, start_angle, length};
    std::stack<TurtleState> stack;

    SDL_SetRenderDrawColor(r, 100, 200, 100, 255);

    for (char c : s) {
        switch (c) {
            case 'F':
            case 'A':
            case 'B': {
                float nx = state.x + cosf(state.angle) * state.length;
                float ny = state.y + sinf(state.angle) * state.length;
                SDL_RenderDrawLine(r, state.x, state.y, nx, ny);
                state.x = nx; state.y = ny;
                break;
            }
            case '+': state.angle += angle_delta; break;
            case '-': state.angle -= angle_delta; break;
            case '[': stack.push(state); break;
            case ']': state = stack.top(); stack.pop(); break;
        }
    }
}

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("L-System",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 800, 600, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, 0);

    // 分形树
    std::string tree = lsystem(
        "F",
        {{'F', "F[+F]F[-F]F"}},
        4
    );

    // 蕨类植物
    std::string fern = lsystem(
        "X",
        {{'X', "F+[[X]-X]-F[-FX]+X"}, {'F', "FF"}},
        5
    );

    bool running = true;
    int mode = 0;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) {
            if (e.type == SDL_QUIT) running = false;
            if (e.type == SDL_KEYDOWN) {
                if (e.key.keysym.sym >= SDLK_1 && e.key.keysym.sym <= SDLK_2)
                    mode = e.key.keysym.sym - SDLK_1;
            }
        }

        SDL_SetRenderDrawColor(renderer, 20, 20, 30, 255);
        SDL_RenderClear(renderer);

        if (mode == 0) {
            draw_lsystem(renderer, tree, 400, 550, -M_PI/2, 30, 25 * M_PI / 180);
        } else {
            draw_lsystem(renderer, fern, 200, 550, -M_PI/2, 8, 25 * M_PI / 180);
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

- L-System 用字符串重写规则生成递归结构，模拟植物生长。
- **海龟图形**把字符串解释为绘图命令：F 前进、+/- 转向、[/] 入栈出栈。
- 经典应用：分形树、科赫雪花、谢尔宾斯基三角形、蕨类植物。
- **迭代次数**控制细节，通常 4-6 代足够。
- **常见坑**：迭代过多导致字符串爆炸（指数增长）；角度选择不当导致结构不自然。

---

## 第 12 讲 · 元球与隐式曲面

### 概念

**元球（Metaball）** 是一种隐式曲面——多个"球体"的势场叠加，当势场超过阈值时形成曲面。元球之间会"融合"，产生有机的液态效果，适合模拟水银、史莱姆、等离子体。

**隐式曲面（Implicit Surface）** 由方程 f(x,y,z) = 0 定义，而非显式顶点。元球是隐式曲面的经典应用，势场函数决定形状。

### 原理

**元球的势场函数**：

每个元球是一个势场源，空间任一点的势场是所有元球势场之和：

```
f(p) = Σ metaball_i(p)
```

常用势场函数：

1. **倒数距离**：`f(p) = r² / |p - center|²`，无限远势场为 0。
2. **高斯**：`f(p) = exp(-|p - center|² / r²)`，平滑衰减。
3. **多项式**：`f(p) = (1 - |p-center|²/r²)²` 当 |p-center| < r，否则 0。

**曲面提取**：

势场超过阈值 `T` 的点构成曲面：

```
f(p) > T → 内部
f(p) = T → 曲面
f(p) < T → 外部
```

**2D 元球**：

2D 中元球是"圆"，势场超过阈值的区域填充。渲染 2D 元球最简单——逐像素计算势场，超过阈值涂色：

```c
for each pixel (x, y):
    float field = 0;
    for each metaball m:
        field += m.radius² / ((x-m.x)² + (y-m.y)² + epsilon)
    if field > threshold:
        put_pixel(x, y, color)
```

**Marching Squares**：

提取 2D 等值线的算法。把空间划分为网格，每个网格点计算势场，根据角点是否超过阈值决定等值线如何穿过网格：

```
对每个网格单元：
    计算 4 个角点的势场
    4 个角点各有"内/外"状态，共 16 种组合
    根据组合绘制对应的线段
```

**Marching Cubes**：

3D 版本，每个立方体 8 个角点，共 256 种组合（考虑对称性后约 15 种）。用于提取 3D 等值面。

**元球的动画**：

元球中心移动，曲面动态变化，产生"融合-分离"效果：

```c
for each frame:
    update metaball positions (移动、旋转)
    recompute field
    render surface
```

### 例子

```c
#include <SDL2/SDL.h>
#include <cmath>
#include <vector>

struct Metaball {
    float x, y, radius;
};

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Metaballs",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 640, 480, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, 0);
    SDL_Texture* texture = SDL_CreateTexture(renderer,
        SDL_PIXELFORMAT_RGBA8888, SDL_TEXTUREACCESS_STREAMING, 320, 240);

    std::vector<Metaball> balls = {
        {160, 120, 50},
        {100, 100, 40},
        {220, 140, 45},
        {160, 180, 35}
    };

    Uint32 framebuffer[320 * 240];

    bool running = true;
    int t = 0;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) if (e.type == SDL_QUIT) running = false;

        // 移动元球
        balls[0].x = 160 + 80 * sin(t * 0.02);
        balls[0].y = 120 + 60 * cos(t * 0.03);
        balls[1].x = 100 + 70 * cos(t * 0.025);
        balls[1].y = 100 + 50 * sin(t * 0.028);
        balls[2].x = 220 + 60 * sin(t * 0.03 + 1);
        balls[2].y = 140 + 70 * cos(t * 0.022 + 2);
        balls[3].x = 160 + 90 * cos(t * 0.018 + 3);
        balls[3].y = 180 + 40 * sin(t * 0.026 + 4);

        // 渲染元球
        for (int y = 0; y < 240; ++y) {
            for (int x = 0; x < 320; ++x) {
                float field = 0;
                for (const auto& b : balls) {
                    float dx = x - b.x, dy = y - b.y;
                    field += b.radius * b.radius / (dx*dx + dy*dy + 1);
                }
                if (field > 1.0f) {
                    // 根据势场强度着色
                    float intensity = std::min(1.0f, (field - 1.0f) * 0.5f);
                    Uint8 r = (Uint8)(255 * intensity);
                    Uint8 g = (Uint8)(100 + 155 * intensity);
                    Uint8 b_color = (Uint8)(200 + 55 * intensity);
                    framebuffer[y * 320 + x] = 0xFF000000 | (r << 16) | (g << 8) | b_color;
                } else {
                    framebuffer[y * 320 + x] = 0xFF000000;
                }
            }
        }

        SDL_UpdateTexture(texture, nullptr, framebuffer, 320 * 4);
        SDL_RenderClear(renderer);
        SDL_RenderCopy(renderer, texture, nullptr, nullptr);
        SDL_RenderPresent(renderer);

        t++;
    }

    SDL_DestroyTexture(texture);
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- 元球是隐式曲面，多个势场源叠加，超过阈值形成曲面。
- 势场函数：倒数距离、高斯、多项式。
- **2D 渲染**：逐像素计算势场，超过阈值涂色。
- **Marching Squares/Cubes**：提取等值线/面的标准算法。
- **应用**：水银、史莱姆、等离子体、液态金属。
- **常见坑**：势场计算 O(像素×元球数)，元球过多性能下降；阈值选择不当导致形状异常。

---

# 第 4 章 · 经典效果

本章讲解 DemoScene 的四大经典效果：等离子、火焰、隧道、星空。这些效果是 1990 年代 Demo 的标志，用极简的数学产生惊艳的视觉。每个效果都体现 DemoScene 的核心智慧——用算法代替资产，用数学创造美。

## 第 13 讲 · 等离子效果

### 概念

**等离子（Plasma）** 是 DemoScene 最经典的效果之一——色彩流动的有机图案，像等离子体或液态金属。它由多个正弦波叠加而成，配合调色板循环产生流动感。

等离子的魅力在于极简——几行代码就能产生 mesmerizing 的视觉效果，是 DemoScene"数学之美"的完美体现。

### 原理

**等离子的数学**：

等离子是多个正弦波的叠加：

```
plasma(x, y) = sin(x * a) + sin(y * b) + sin((x+y) * c) + sin(sqrt(x²+y²) * d)
```

每个正弦波贡献不同方向的"波纹"，叠加产生复杂的干涉图案。

**简化版**：

```c
float plasma(float x, float y, float t) {
    float v = sin(x * 0.1 + t);
    v += sin(y * 0.1 + t * 1.3);
    v += sin((x + y) * 0.07 + t * 0.7);
    v += sin(sqrt(x*x + y*y) * 0.1 + t);
    return v / 4.0f;  // 归一化到 [-0.5, 0.5]
}
```

**调色板映射**：

把等离子值映射到调色板索引，配合颜色循环产生流动：

```c
int index = (int)((plasma(x, y, t) + 0.5f) * 255);
color = palette[index];
```

**优化技巧**：

1. **预计算正弦表**：sin 计算昂贵，预计算 256 个值的查找表。
2. **定点运算**：用整数代替浮点，避免 FPU 开销。
3. **增量计算**：相邻像素的 sin 值可用递推公式计算。

**正弦表**：

```c
float sin_table[256];
for (int i = 0; i < 256; ++i) {
    sin_table[i] = sin(i * 2 * M_PI / 256);
}
float fast_sin(float x) {
    return sin_table[(int)(x * 40.74f) & 255];  // x / (2π) * 256
}
```

**变体**：

1. **扭曲等离子**：用另一个等离子函数扭曲坐标。
2. **分形等离子**：叠加不同频率的等离子（类似 fBm）。
3. **3D 等离子**：扩展到 3D，用体素或光线步进渲染。

### 例子

```c
#include <SDL2/SDL.h>
#include <cmath>

// 预计算正弦表
float sin_table[256];
void init_sin_table() {
    for (int i = 0; i < 256; ++i) {
        sin_table[i] = sin(i * 2 * M_PI / 256);
    }
}
float fast_sin(float x) {
    int idx = (int)(x * 40.743665f) & 255;  // x / (2π) * 256
    return sin_table[idx];
}

// 等离子函数
float plasma(float x, float y, float t) {
    float v = fast_sin(x * 0.05f + t);
    v += fast_sin(y * 0.05f + t * 1.3f);
    v += fast_sin((x + y) * 0.04f + t * 0.7f);
    float d = sqrt(x*x + y*y);
    v += fast_sin(d * 0.04f + t);
    return v * 0.25f;  // 归一化
}

// HSV → RGB
void hsv_to_rgb(float h, float s, float v, Uint8& r, Uint8& g, Uint8& b) {
    float c = v * s;
    float x = c * (1 - fabs(fmod(h / 60, 2) - 1));
    float m = v - c;
    float rf, gf, bf;
    if (h < 60) { rf = c; gf = x; bf = 0; }
    else if (h < 120) { rf = x; gf = c; bf = 0; }
    else if (h < 180) { rf = 0; gf = c; bf = x; }
    else if (h < 240) { rf = 0; gf = x; bf = c; }
    else if (h < 300) { rf = x; gf = 0; bf = c; }
    else { rf = c; gf = 0; bf = x; }
    r = (Uint8)((rf + m) * 255);
    g = (Uint8)((gf + m) * 255);
    b = (Uint8)((bf + m) * 255);
}

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Plasma",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 640, 480, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, 0);
    SDL_Texture* texture = SDL_CreateTexture(renderer,
        SDL_PIXELFORMAT_RGBA8888, SDL_TEXTUREACCESS_STREAMING, 320, 240);

    init_sin_table();
    Uint32 framebuffer[320 * 240];

    bool running = true;
    float t = 0;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) if (e.type == SDL_QUIT) running = false;

        for (int y = 0; y < 240; ++y) {
            for (int x = 0; x < 320; ++x) {
                float p = plasma(x - 160, y - 120, t);
                // 映射到 HSV 色相
                float hue = (p + 0.5f) * 360 + t * 30;
                hue = fmod(hue, 360);
                Uint8 r, g, b;
                hsv_to_rgb(hue, 0.8f, 0.9f, r, g, b);
                framebuffer[y * 320 + x] = 0xFF000000 | (r << 16) | (g << 8) | b;
            }
        }

        SDL_UpdateTexture(texture, nullptr, framebuffer, 320 * 4);
        SDL_RenderClear(renderer);
        SDL_RenderCopy(renderer, texture, nullptr, nullptr);
        SDL_RenderPresent(renderer);

        t += 0.05f;
    }

    SDL_DestroyTexture(texture);
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- 等离子是多个正弦波叠加，产生有机流动图案。
- 公式：`sin(x) + sin(y) + sin(x+y) + sin(sqrt(x²+y²))`。
- **优化**：预计算正弦表、定点运算、增量计算。
- 配合调色板循环或 HSV 着色产生流动感。
- **变体**：扭曲等离子、分形等离子、3D 等离子。
- **常见坑**：sin 计算昂贵，必须用查找表；颜色映射不当导致灰暗。

---

## 第 14 讲 · 火焰效果

### 概念

**火焰效果（Fire Effect）** 模拟燃烧的火焰——从底部升起，颜色从白到红到黑渐变。它是 DemoScene 最具辨识度的效果之一，用简单的细胞自动机产生逼真的火焰。

火焰效果的核心是"热扩散"——底部的热源向上传播，逐渐冷却，形成火焰的形状与颜色。

### 原理

**火焰算法**：

1. **底部点火**：帧缓冲底部一行设为最高温度（白色）。
2. **热扩散**：每个像素的温度 = 下方像素温度的平均值减去冷却量。
3. **调色板映射**：温度映射到火焰调色板（黑→红→橙→黄→白）。

**热扩散公式**：

```c
for y from HEIGHT-2 down to 0:
    for x from 0 to WIDTH-1:
        new_temp[x][y] = (temp[x][y+1] + temp[(x-1)%W][y+1] +
                          temp[(x+1)%W][y+1] + temp[x][y+1]) / 4 - cooling
        new_temp[x][y] = max(0, new_temp[x][y])
```

从下往上扫描，每个像素取下方 4 个像素的平均值（含左右），减去冷却量。冷却让火焰顶部逐渐熄灭。

**底部点火**：

```c
// 底部一行设为最高温度
for x in 0..WIDTH:
    temp[x][HEIGHT-1] = 255  // 或随机值增加变化
```

**调色板**：

火焰调色板从黑到白：

```
0-63:    黑 → 红
64-127:  红 → 橙
128-191: 橙 → 黄
192-255: 黄 → 白
```

**优化**：

1. **单缓冲**：直接在帧缓冲上操作，用从下往上的扫描顺序避免覆盖。
2. **整数运算**：温度用 0-255 整数，避免浮点。
3. **随机扰动**：底部点火加随机值，火焰更自然。

**变体**：

1. **横向火焰**：旋转 90 度，火焰从侧面喷出。
2. **多火源**：多个底部点火点。
3. **风向**：扩散时偏向一侧，模拟风吹。
4. **烟雾**：冷却量大的区域生成灰色烟雾。

### 例子

```c
#include <SDL2/SDL.h>
#include <cstdlib>

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Fire Effect",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 640, 480, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, 0);
    SDL_Texture* texture = SDL_CreateTexture(renderer,
        SDL_PIXELFORMAT_RGBA8888, SDL_TEXTUREACCESS_STREAMING, 320, 240);

    const int W = 320, H = 240;
    Uint8 temp[W * H];  // 温度图

    // 火焰调色板
    Uint32 palette[256];
    for (int i = 0; i < 256; ++i) {
        Uint8 r, g, b;
        if (i < 64) { r = i * 4; g = 0; b = 0; }
        else if (i < 128) { r = 255; g = (i - 64) * 4; b = 0; }
        else if (i < 192) { r = 255; g = 255; b = (i - 128) * 4; }
        else { r = 255; g = 255; b = 255; }
        palette[i] = 0xFF000000 | (r << 16) | (g << 8) | b;
    }

    // 初始化温度为 0
    for (int i = 0; i < W * H; ++i) temp[i] = 0;

    Uint32 framebuffer[W * H];

    bool running = true;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) if (e.type == SDL_QUIT) running = false;

        // 1. 底部点火（随机温度）
        for (int x = 0; x < W; ++x) {
            temp[(H - 1) * W + x] = rand() % 256 > 100 ? 255 : 0;
        }

        // 2. 热扩散（从下往上）
        for (int y = 0; y < H - 1; ++y) {
            for (int x = 0; x < W; ++x) {
                int below = temp[(y + 1) * W + x];
                int below_left = temp[(y + 1) * W + ((x - 1 + W) % W)];
                int below_right = temp[(y + 1) * W + ((x + 1) % W)];
                int below_below = temp[(y + 2 < H ? (y + 2) : (H - 1)) * W + x];
                int avg = (below + below_left + below_right + below_below) / 4;
                // 冷却
                avg = avg > 1 ? avg - 1 : 0;
                temp[y * W + x] = avg;
            }
        }

        // 3. 映射到调色板
        for (int i = 0; i < W * H; ++i) {
            framebuffer[i] = palette[temp[i]];
        }

        SDL_UpdateTexture(texture, nullptr, framebuffer, W * 4);
        SDL_RenderClear(renderer);
        SDL_RenderCopy(renderer, texture, nullptr, nullptr);
        SDL_RenderPresent(renderer);
    }

    SDL_DestroyTexture(texture);
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- 火焰效果用细胞自动机模拟热扩散，底部点火向上传播。
- 算法：底部点火 → 热扩散（下方 4 像素平均 - 冷却）→ 调色板映射。
- 调色板：黑→红→橙→黄→白。
- **优化**：单缓冲、整数运算、随机扰动。
- **变体**：横向火焰、多火源、风向、烟雾。
- **常见坑**：扫描顺序错误导致信息丢失；冷却量过大导致火焰熄灭。

---

## 第 15 讲 · 隧道效果

### 概念

**隧道效果（Tunnel Effect）** 让玩家仿佛在无限隧道中飞行——同心圆从中心向外扩散，纹理沿隧道壁流动。它是 DemoScene 最具沉浸感的效果，用极坐标变换实现。

隧道效果的核心是"极坐标映射"——屏幕上每个像素的位置映射到纹理坐标，距离中心越远对应纹理越远，角度对应纹理的环向位置。

### 原理

**隧道的数学**：

对屏幕上每个像素 (x, y)：

1. 计算相对中心的极坐标：
   - 距离 `r = sqrt((x-cx)² + (y-cy)²)`
   - 角度 `θ = atan2(y-cy, x-cx)`

2. 映射到纹理坐标：
   - 纹理 u = θ / (2π)（角度归一化到 [0,1]）
   - 纹理 v = 1 / r（距离的倒数，越远 v 越小）

3. 加入时间偏移产生流动：
   - u += t * 0.1（环向旋转）
   - v -= t * 0.5（向前飞行）

```c
for each pixel (x, y):
    dx = x - cx, dy = y - cy
    r = sqrt(dx*dx + dy*dy)
    theta = atan2(dy, dx)
    u = theta / (2 * PI) + t * 0.1
    v = 1.0 / r + t * 0.5  // 或 v = r * scale + t
    color = texture(u, v)
```

**纹理选择**：

隧道纹理通常是环形或砖墙图案，沿 v 方向（深度）重复。可用过程化纹理（如棋盘格、砖块）或预制作纹理。

**优化**：

1. **预计算查找表**：u、v 与时间无关的部分预计算为查找表。
2. **避免 atan2**：atan2 较慢，可用近似或查找表。
3. **避免 1/r**：用查找表或近似。

**查找表优化**：

```c
// 预计算 u、v 查找表（不含时间偏移）
float u_table[W * H], v_table[W * H];
for each pixel (x, y):
    dx = x - cx, dy = y - cy
    u_table[x + y*W] = atan2(dy, dx) / (2 * PI)
    v_table[x + y*W] = 1.0 / sqrt(dx*dx + dy*dy)

// 渲染时只需加时间偏移
for each pixel:
    u = u_table[i] + t * 0.1
    v = v_table[i] + t * 0.5
    color = texture(u, v)
```

**变体**：

1. **扭曲隧道**：用噪声扭曲极坐标。
2. **多隧道**：多个隧道叠加或切换。
3. **隧道变形**：中心位置移动，隧道"弯曲"。
4. **反向隧道**：v = r 而非 1/r，产生从远到近的效果。

### 例子

```c
#include <SDL2/SDL.h>
#include <cmath>
#include <cstdint>

const int W = 320, H = 240;

// 预计算查找表
float u_table[W * H], v_table[W * H];

void init_tunnel() {
    float cx = W / 2.0f, cy = H / 2.0f;
    for (int y = 0; y < H; ++y) {
        for (int x = 0; x < W; ++x) {
            float dx = x - cx, dy = y - cy;
            float r = sqrt(dx*dx + dy*dy);
            float theta = atan2(dy, dx);
            u_table[y * W + x] = theta / (2 * M_PI);
            v_table[y * W + x] = (r > 1) ? 1.0f / r : 0;
        }
    }
}

// 过程化纹理（棋盘格 + 噪声）
Uint32 tunnel_texture(float u, float v) {
    // 重复 UV
    u = fmod(u, 1.0f); if (u < 0) u += 1;
    v = fmod(v, 1.0f); if (v < 0) v += 1;

    // 棋盘格
    int cu = (int)(u * 8) % 2;
    int cv = (int)(v * 8) % 2;
    int checker = (cu + cv) % 2;

    // 添加亮度变化
    float shade = 0.7f + 0.3f * sin(v * 20);

    Uint8 c = (Uint8)(checker ? 200 * shade : 50 * shade);
    return 0xFF000000 | (c << 16) | (c << 8) | c;
}

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Tunnel",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 640, 480, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, 0);
    SDL_Texture* texture = SDL_CreateTexture(renderer,
        SDL_PIXELFORMAT_RGBA8888, SDL_TEXTUREACCESS_STREAMING, W, H);

    init_tunnel();
    Uint32 framebuffer[W * H];

    bool running = true;
    float t = 0;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) if (e.type == SDL_QUIT) running = false;

        for (int i = 0; i < W * H; ++i) {
            float u = u_table[i] + t * 0.1f;
            float v = v_table[i] + t * 0.5f;
            framebuffer[i] = tunnel_texture(u, v);
        }

        SDL_UpdateTexture(texture, nullptr, framebuffer, W * 4);
        SDL_RenderClear(renderer);
        SDL_RenderCopy(renderer, texture, nullptr, nullptr);
        SDL_RenderPresent(renderer);

        t += 0.02f;
    }

    SDL_DestroyTexture(texture);
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- 隧道效果用极坐标映射：u = 角度，v = 1/距离，加时间偏移产生流动。
- **查找表优化**：预计算 u、v 表，渲染时只加时间偏移。
- 纹理用环形或砖墙图案，沿 v 方向重复。
- **变体**：扭曲隧道、多隧道、隧道变形、反向隧道。
- **常见坑**：中心点 r=0 导致除零；atan2 较慢需查找表。

---

## 第 16 讲 · 星空与视差滚动

### 概念

**星空效果（Starfield）** 模拟太空飞行——星星从中心向外飞过，产生 3D 透视感。它是 DemoScene 最古老也最经典的效果，Future Crew 的《Second Reality》开场就是星空。

**视差滚动（Parallax Scrolling）** 用多层背景不同速度滚动，产生深度感。虽然第 5 章已介绍视差，这里从 DemoScene 角度补充星空与视差的结合。

### 原理

**星空的数学**：

每颗星有 3D 坐标 (x, y, z)，投影到 2D 屏幕：

```
screen_x = x / z * scale + cx
screen_y = y / z * scale + cy
```

z 越小（越近），星星越远离中心。每帧 z 减小（飞向观察者），星星向外扩散。

**星空算法**：

```c
struct Star { float x, y, z; };

for each star:
    z -= speed  // 飞向观察者
    if z <= 0:
        // 重置到远处
        x = rand() - 0.5
        y = rand() - 0.5
        z = 1.0
    screen_x = x / z * scale + cx
    screen_y = y / z * scale + cy
    brightness = 1 / z  // 近的星更亮
    draw_pixel(screen_x, screen_y, brightness)
```

**星星的大小**：

近的星星更大：

```c
size = (1 - z) * max_size + 1
```

**视差星空**：

多层星空，不同速度滚动：

- 远层：小星星，慢速，暗。
- 中层：中星星，中速，中亮。
- 近层：大星星，快速，亮。

**超光速效果**：

星星拉成线段，模拟超光速飞行：

```c
// 保存上一帧位置
prev_x = x / (z + speed) * scale + cx
prev_y = y / (z + speed) * scale + cy
// 画线段
draw_line(prev_x, prev_y, screen_x, screen_y)
```

**彩色星空**：

星星有不同颜色（蓝、白、红、黄），模拟恒星光谱：

```c
star_color = random_choice([blue, white, yellow, red])
```

### 例子

```c
#include <SDL2/SDL.h>
#include <cmath>
#include <vector>
#include <cstdlib>

struct Star {
    float x, y, z;
    Uint8 r, g, b;
};

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Starfield",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 640, 480, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, 0);
    SDL_Texture* texture = SDL_CreateTexture(renderer,
        SDL_PIXELFORMAT_RGBA8888, SDL_TEXTUREACCESS_STREAMING, 320, 240);

    const int W = 320, H = 240;
    const int NUM_STARS = 400;
    std::vector<Star> stars(NUM_STARS);

    // 初始化星星
    for (auto& s : stars) {
        s.x = (rand() % 2000 - 1000) / 1000.0f;
        s.y = (rand() % 2000 - 1000) / 1000.0f;
        s.z = (rand() % 1000 + 1) / 1000.0f;
        // 随机颜色（恒星光谱）
        int color_type = rand() % 4;
        if (color_type == 0) { s.r = 180; s.g = 200; s.b = 255; }  // 蓝
        else if (color_type == 1) { s.r = 255; s.g = 255; s.b = 255; }  // 白
        else if (color_type == 2) { s.r = 255; s.g = 240; s.b = 200; }  // 黄
        else { s.r = 255; s.g = 150; s.b = 100; }  // 红
    }

    Uint32 framebuffer[W * H];
    bool warp = false;  // 超光速模式

    bool running = true;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) {
            if (e.type == SDL_QUIT) running = false;
            if (e.type == SDL_KEYDOWN && e.key.keysym.sym == SDLK_SPACE)
                warp = !warp;
        }

        // 清屏（带拖影效果）
        for (int i = 0; i < W * H; ++i) {
            Uint32 c = framebuffer[i];
            Uint8 r = (c >> 16) & 0xFF, g = (c >> 8) & 0xFF, b = c & 0xFF;
            r = r > 10 ? r - 10 : 0;
            g = g > 10 ? g - 10 : 0;
            b = b > 10 ? b - 10 : 0;
            framebuffer[i] = 0xFF000000 | (r << 16) | (g << 8) | b;
        }

        float speed = warp ? 0.05f : 0.01f;

        for (auto& s : stars) {
            float prev_z = s.z;
            s.z -= speed;
            if (s.z <= 0.01f) {
                s.x = (rand() % 2000 - 1000) / 1000.0f;
                s.y = (rand() % 2000 - 1000) / 1000.0f;
                s.z = 1.0f;
                continue;
            }

            float scale = 200.0f;
            int sx = (int)(s.x / s.z * scale + W / 2);
            int sy = (int)(s.y / s.z * scale + H / 2);

            if (sx < 0 || sx >= W || sy < 0 || sy >= H) continue;

            float brightness = (1 - s.z) * 2.0f;
            if (brightness > 1) brightness = 1;

            Uint8 r = (Uint8)(s.r * brightness);
            Uint8 g = (Uint8)(s.g * brightness);
            Uint8 b = (Uint8)(s.b * brightness);

            // 超光速时画线段
            if (warp && prev_z > s.z) {
                int prev_sx = (int)(s.x / prev_z * scale + W / 2);
                int prev_sy = (int)(s.y / prev_z * scale + H / 2);
                // 简单画线（Bresenham 简化）
                int steps = std::max(abs(sx - prev_sx), abs(sy - prev_sy));
                for (int i = 0; i <= steps; ++i) {
                    int px = prev_sx + (sx - prev_sx) * i / steps;
                    int py = prev_sy + (sy - prev_sy) * i / steps;
                    if (px >= 0 && px < W && py >= 0 && py < H) {
                        framebuffer[py * W + px] = 0xFF000000 | (r << 16) | (g << 8) | b;
                    }
                }
            } else {
                framebuffer[sy * W + sx] = 0xFF000000 | (r << 16) | (g << 8) | b;
                // 大星星画十字
                if (brightness > 0.7f) {
                    if (sx > 0) framebuffer[sy * W + sx - 1] = 0xFF000000 | (r << 16) | (g << 8) | b;
                    if (sx < W-1) framebuffer[sy * W + sx + 1] = 0xFF000000 | (r << 16) | (g << 8) | b;
                    if (sy > 0) framebuffer[(sy-1) * W + sx] = 0xFF000000 | (r << 16) | (g << 8) | b;
                    if (sy < H-1) framebuffer[(sy+1) * W + sx] = 0xFF000000 | (r << 16) | (g << 8) | b;
                }
            }
        }

        SDL_UpdateTexture(texture, nullptr, framebuffer, W * 4);
        SDL_RenderClear(renderer);
        SDL_RenderCopy(renderer, texture, nullptr, nullptr);
        SDL_RenderPresent(renderer);
    }

    SDL_DestroyTexture(texture);
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- 星空用 3D 投影：`screen = pos / z * scale + center`，z 减小产生飞行感。
- 近的星星更亮更大，可画十字或线段增强。
- **超光速**：画上一帧到当前帧的线段，产生拉线效果。
- **彩色星空**：星星有不同颜色，模拟恒星光谱。
- **拖影**：不清屏而是衰减，产生运动模糊。
- **常见坑**：z=0 导致除零；星星数量过多性能下降。

---

# 第 5 章 · 着色器与 GPU

现代 DemoScene 的核心是 GPU 着色器——用 GLSL 在显卡上并行计算每像素颜色，实现 CPU 无法企及的复杂效果。本章从 GLSL 基础开始，讲解光线步进（Ray Marching）、SDF 距离场渲染、后处理与色彩分级。掌握这些，你就能理解现代 4K Intro 如何用 4096 字节渲染完整 3D 场景。

## 第 17 讲 · GLSL 与片段着色器基础

### 概念

**GLSL（OpenGL Shading Language）** 是 OpenGL 的着色器语言，语法类似 C。在 DemoScene 中，最常用的是**片段着色器（Fragment Shader）**——为每个像素并行执行的程序，决定其颜色。

片段着色器的威力在于并行——GPU 同时执行数千个片段着色器实例，让逐像素计算变得极快。一个 1920×1080 屏幕，每帧 2M 像素，GPU 能在毫秒内完成复杂计算。

### 原理

**着色器管线**：

```
顶点着色器 → 光栅化 → 片段着色器 → 帧缓冲
```

DemoScene 通常用一个全屏四边形，顶点着色器只负责传递坐标，所有计算在片段着色器中完成。这种"屏幕空间"渲染是 ShaderToy 与 4K Intro 的标准模式。

**片段着色器结构**：

```glsl
#version 330 core

in vec2 uv;              // 纹理坐标 [0,1]
out vec4 fragColor;      // 输出颜色

uniform float iTime;     // 时间
uniform vec2 iResolution; // 分辨率

void main() {
    vec2 uv = gl_FragCoord.xy / iResolution.xy;  // 归一化坐标
    vec3 color = vec3(uv.x, uv.y, 0.5 + 0.5 * sin(iTime));
    fragColor = vec4(color, 1.0);
}
```

**GLSL 内置函数**：

```glsl
// 数学
sin, cos, tan, sqrt, pow, exp, log, abs, min, max, clamp, mix, smoothstep
// 几何
length, distance, dot, cross, normalize, reflect, refract
// 纹理
texture(sampler, uv)
// 向量
vec2, vec3, vec4, swizzling (xyzw, rgba, stpq)
```

**坐标系**：

- `gl_FragCoord.xy`：像素坐标，左下角为原点。
- 归一化到 [0,1]：`uv = gl_FragCoord.xy / iResolution.xy`。
- 居中并保持宽高比：`uv = (gl_FragCoord.xy - 0.5*iResolution.xy) / iResolution.y`。

**ShaderToy 模式**：

ShaderToy 是 DemoScene 社区的着色器分享平台，提供标准 uniform：

```glsl
uniform vec3 iResolution;   // 视口分辨率
uniform float iTime;        // 时间（秒）
uniform float iTimeDelta;   // 帧时间
uniform int iFrame;         // 帧号
uniform vec4 iMouse;        // 鼠标位置
uniform sampler2D iChannel0; // 纹理通道
```

**全屏四边形渲染**：

```c
// 顶点数据：两个三角形覆盖全屏
float quad[] = {
    -1, -1,  1, -1,  -1, 1,
    -1,  1,  1, -1,   1, 1
};
// 顶点着色器只传递位置
// 片段着色器做所有计算
```

### 例子

```glsl
#version 330 core
// 简单片段着色器：彩色波纹

out vec4 fragColor;
uniform float iTime;
uniform vec2 iResolution;

void main() {
    vec2 uv = (gl_FragCoord.xy - 0.5 * iResolution.xy) / iResolution.y;
    
    // 极坐标
    float r = length(uv);
    float a = atan(uv.y, uv.x);
    
    // 波纹
    float wave = sin(r * 20.0 - iTime * 2.0) * 0.5 + 0.5;
    
    // 颜色
    vec3 color = vec3(
        sin(a * 3.0 + iTime) * 0.5 + 0.5,
        wave,
        sin(r * 10.0 + iTime) * 0.5 + 0.5
    );
    
    fragColor = vec4(color, 1.0);
}
```

**C++ 主程序（SDL2 + OpenGL）**：

```c
#include <SDL2/SDL.h>
#include <GL/glew.h>
#include <cstdio>

const char* vertex_src = R"(
#version 330 core
const vec2 verts[4] = vec2[4](
    vec2(-1, -1), vec2(1, -1), vec2(-1, 1), vec2(1, 1)
);
void main() {
    gl_Position = vec4(verts[gl_VertexID], 0, 1);
}
)";

const char* fragment_src = R"(
#version 330 core
out vec4 fragColor;
uniform float iTime;
uniform vec2 iResolution;

void main() {
    vec2 uv = (gl_FragCoord.xy - 0.5 * iResolution.xy) / iResolution.y;
    float r = length(uv);
    float a = atan(uv.y, uv.x);
    float wave = sin(r * 20.0 - iTime * 2.0) * 0.5 + 0.5;
    vec3 color = vec3(
        sin(a * 3.0 + iTime) * 0.5 + 0.5,
        wave,
        sin(r * 10.0 + iTime) * 0.5 + 0.5
    );
    fragColor = vec4(color, 1.0);
}
)";

GLuint compile_shader(GLenum type, const char* src) {
    GLuint shader = glCreateShader(type);
    glShaderSource(shader, 1, &src, nullptr);
    glCompileShader(shader);
    return shader;
}

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    SDL_GL_SetAttribute(SDL_GL_CONTEXT_PROFILE_MASK, SDL_GL_CONTEXT_PROFILE_CORE);
    SDL_GL_SetAttribute(SDL_GL_CONTEXT_MAJOR_VERSION, 3);
    SDL_GL_SetAttribute(SDL_GL_CONTEXT_MINOR_VERSION, 3);
    SDL_Window* window = SDL_CreateWindow("GLSL Demo",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 800, 600, SDL_WINDOW_OPENGL);
    SDL_GLContext ctx = SDL_GL_CreateContext(window);
    glewInit();

    GLuint vs = compile_shader(GL_VERTEX_SHADER, vertex_src);
    GLuint fs = compile_shader(GL_FRAGMENT_SHADER, fragment_src);
    GLuint prog = glCreateProgram();
    glAttachShader(prog, vs);
    glAttachShader(prog, fs);
    glLinkProgram(prog);
    glUseProgram(prog);

    GLint time_loc = glGetUniformLocation(prog, "iTime");
    GLint res_loc = glGetUniformLocation(prog, "iResolution");

    bool running = true;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) if (e.type == SDL_QUIT) running = false;

        float t = (float)SDL_GetTicks() / 1000.0f;
        glUniform1f(time_loc, t);
        glUniform2f(res_loc, 800, 600);

        glDrawArrays(GL_TRIANGLE_STRIP, 0, 4);
        SDL_GL_SwapWindow(window);
    }

    SDL_GL_DeleteContext(ctx);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- GLSL 是 OpenGL 着色器语言，片段着色器为每像素并行执行。
- DemoScene 用全屏四边形 + 片段着色器，所有计算在屏幕空间。
- **坐标系**：`uv = (gl_FragCoord - 0.5*res) / res.y` 居中并保持宽高比。
- **ShaderToy** 提供标准 uniform（iTime、iResolution 等）。
- **性能**：GPU 并行执行数千实例，复杂逐像素计算毫秒级完成。
- **常见坑**：忘记归一化坐标导致拉伸；GLSL 版本不匹配导致编译失败。

---

## 第 18 讲 · 光线步进 Ray Marching

### 概念

**光线步进（Ray Marching）** 是 DemoScene 渲染 3D 场景的标准技术——从相机发射光线，沿光线逐步前进，检测与场景的交点。与传统光栅化不同，Ray Marching 直接在片段着色器中渲染 3D，无需顶点数据。

Ray Marching 配合 SDF（Signed Distance Field）能高效渲染复杂 3D 场景，是现代 4K Intro 的核心技术——Inigo Quilez 等大师用它在 4KB 内渲染完整 3D 世界。

### 原理

**光线投射（Ray Casting）**：

对每个像素，从相机发射一条光线：

```
ray_origin = camera_pos
ray_direction = normalize(pixel_pos - camera_pos)
```

**光线步进**：

沿光线方向逐步前进，每步检测是否击中物体：

```glsl
vec3 ray_pos = ray_origin;
for (int i = 0; i < MAX_STEPS; ++i) {
    float d = scene_sdf(ray_pos);  // 到最近物体的距离
    if (d < 0.001) {
        // 击中！
        break;
    }
    ray_pos += ray_direction * d;  // 前进 d 距离
    if (length(ray_pos - ray_origin) > MAX_DIST) break;  // 太远
}
```

**SDF 的优势**：

SDF 告诉"到最近物体的距离"，因此可以安全地前进这个距离而不会穿过物体——这比固定步长快得多，称为"球体追踪"（Sphere Tracing）。

**场景 SDF**：

```glsl
// 球体的 SDF
float sd_sphere(vec3 p, float r) {
    return length(p) - r;
}

// 立方体的 SDF
float sd_box(vec3 p, vec3 b) {
    vec3 q = abs(p) - b;
    return length(max(q, 0.0)) + min(max(q.x, max(q.y, q.z)), 0.0);
}

// 场景：组合多个 SDF
float map(vec3 p) {
    float sphere = sd_sphere(p - vec3(0, 0, 5), 1.0);
    float box = sd_box(p - vec3(2, 0, 5), vec3(0.8));
    return min(sphere, box);  // 取最近
}
```

**SDF 的组合操作**：

```glsl
// 并集
float op_union(float d1, float d2) { return min(d1, d2); }

// 交集
float op_intersect(float d1, float d2) { return max(d1, d2); }

// 差集
float op_subtract(float d1, float d2) { return max(d1, -d2); }

// 平滑并集
float op_smooth_union(float d1, float d2, float k) {
    float h = clamp(0.5 + 0.5*(d2-d1)/k, 0.0, 1.0);
    return mix(d2, d1, h) - k*h*(1.0-h);
}
```

**法向量计算**：

击中后，用梯度计算法向量（用于光照）：

```glsl
vec3 calc_normal(vec3 p) {
    vec2 e = vec2(0.001, 0);
    return normalize(vec3(
        map(p + e.xyy) - map(p - e.xyy),
        map(p + e.yxy) - map(p - e.yxy),
        map(p + e.yyx) - map(p - e.yyx)
    ));
}
```

**光照**：

```glsl
// 漫反射
float diffuse = max(0.0, dot(normal, light_dir));

// 阴影：从交点向光源 Ray March，被遮挡则在阴影中
float shadow = soft_shadow(p, light_pos);

// 环境光
float ambient = 0.1;

color = albedo * (ambient + diffuse * shadow) * light_color;
```

### 例子

```glsl
#version 330 core
out vec4 fragColor;
uniform float iTime;
uniform vec2 iResolution;

// 球体 SDF
float sd_sphere(vec3 p, float r) { return length(p) - r; }

// 立方体 SDF
float sd_box(vec3 p, vec3 b) {
    vec3 q = abs(p) - b;
    return length(max(q, 0.0)) + min(max(q.x, max(q.y, q.z)), 0.0);
}

// 平面 SDF
float sd_plane(vec3 p, float y) { return p.y - y; }

// 场景
float map(vec3 p) {
    float ground = sd_plane(p, -1.0);
    float sphere = sd_sphere(p - vec3(0, 0, 5), 1.0);
    float box = sd_box(p - vec3(sin(iTime), 1, 5 + cos(iTime)), vec3(0.5));
    return min(min(ground, sphere), box);
}

// 法向量
vec3 calc_normal(vec3 p) {
    vec2 e = vec2(0.001, 0);
    return normalize(vec3(
        map(p + e.xyy) - map(p - e.xyy),
        map(p + e.yxy) - map(p - e.yxy),
        map(p + e.yyx) - map(p - e.yyx)
    ));
}

// Ray Marching
float ray_march(vec3 ro, vec3 rd) {
    float t = 0.0;
    for (int i = 0; i < 100; ++i) {
        vec3 p = ro + rd * t;
        float d = map(p);
        if (d < 0.001) return t;
        t += d;
        if (t > 100.0) break;
    }
    return -1.0;
}

void main() {
    vec2 uv = (gl_FragCoord.xy - 0.5 * iResolution.xy) / iResolution.y;
    
    // 相机
    vec3 ro = vec3(0, 1, 0);
    vec3 rd = normalize(vec3(uv, 1.0));
    
    // Ray March
    float t = ray_march(ro, rd);
    
    vec3 color;
    if (t < 0.0) {
        // 天空
        color = vec3(0.5, 0.7, 1.0);
    } else {
        vec3 p = ro + rd * t;
        vec3 n = calc_normal(p);
        vec3 light = normalize(vec3(1, 1, -1));
        float diff = max(0.0, dot(n, light));
        color = vec3(0.8) * (0.1 + diff * 0.9);
    }
    
    fragColor = vec4(color, 1.0);
}
```

### 总结

- Ray Marching 从相机发射光线，逐步前进检测交点，渲染 3D 场景。
- **SDF** 告诉到最近物体的距离，允许安全大步前进（球体追踪）。
- SDF 组合：并集（min）、交集（max）、差集（max -）、平滑并集。
- **法向量**用梯度计算，用于光照。
- **优势**：无需顶点数据，纯数学描述场景，适合 4K Intro。
- **常见坑**：步数过少导致穿透；SDF 精度不足导致伪影。

---

## 第 19 讲 · SDF 与距离场渲染

### 概念

**SDF（Signed Distance Field，有符号距离场）** 是 DemoScene 的核心数学工具——空间中任一点到最近物体表面的有符号距离（内部为负，外部为正）。SDF 不仅能用于 Ray Marching，还能直接渲染 2D 形状、字体、阴影。

Inigo Quilez 的网站（iquilezles.org）是 SDF 的宝库，收录了几乎所有形状的 SDF 公式。

### 原理

**SDF 的性质**：

1. **有符号**：内部为负，表面为 0，外部为正。
2. **距离**：值的绝对值是到表面的最短距离。
3. **梯度即法向量**：SDF 的梯度方向就是表面法向量。

**2D SDF**：

```glsl
// 圆
float sd_circle(vec2 p, float r) { return length(p) - r; }

// 矩形
float sd_box(vec2 p, vec2 b) {
    vec2 d = abs(p) - b;
    return length(max(d, 0.0)) + min(max(d.x, d.y), 0.0);
}

// 线段
float sd_segment(vec2 p, vec2 a, vec2 b) {
    vec2 pa = p - a, ba = b - a;
    float h = clamp(dot(pa, ba) / dot(ba, ba), 0.0, 1.0);
    return length(pa - ba * h);
}

// 等边三角形
float sd_triangle(vec2 p, float r) {
    const float k = sqrt(3.0);
    p.x = abs(p.x) - r;
    p.y = p.y + r / k;
    if (p.x + k * p.y > 0.0) p = vec2(p.x - k * p.y, -k * p.x - p.y) / 2.0;
    p.x -= clamp(p.x, -2.0 * r, 0.0);
    return -length(p) * sign(p.y);
}
```

**3D SDF**：

```glsl
// 球
float sd_sphere(vec3 p, float r) { return length(p) - r; }

// 立方体
float sd_box(vec3 p, vec3 b) {
    vec3 q = abs(p) - b;
    return length(max(q, 0.0)) + min(max(q.x, max(q.y, q.z)), 0.0);
}

// 圆柱
float sd_cylinder(vec3 p, float r, float h) {
    vec2 d = vec2(length(p.xz) - r, abs(p.y) - h);
    return min(max(d.x, d.y), 0.0) + length(max(d, 0.0));
}

// 圆环
float sd_torus(vec3 p, float r, float R) {
    vec2 q = vec2(length(p.xz) - R, p.y);
    return length(q) - r;
}
```

**SDF 变换**：

```glsl
// 平移
float translated_sdf(vec3 p, vec3 offset) {
    return original_sdf(p - offset);
}

// 旋转
float rotated_sdf(vec3 p, mat3 rot) {
    return original_sdf(rot * p);
}

// 缩放
float scaled_sdf(vec3 p, float s) {
    return original_sdf(p / s) * s;  // 注意乘回 s
}

// 重复
float repeated_sdf(vec3 p, vec3 c) {
    vec3 q = mod(p, c) - 0.5 * c;
    return original_sdf(q);
}

// 扭曲
float twisted_sdf(vec3 p) {
    float t = 0.5 * p.y;
    float c = cos(t), s = sin(t);
    mat2 m = mat2(c, -s, s, c);
    p.xz = m * p.xz;
    return original_sdf(p);
}
```

**2D SDF 渲染**：

```glsl
void main() {
    vec2 uv = (gl_FragCoord.xy - 0.5 * iResolution.xy) / iResolution.y;
    
    float d = sd_box(uv, vec2(0.3));
    
    vec3 color;
    if (d < 0.0) {
        color = vec3(1.0);  // 内部白色
    } else {
        color = vec3(0.0);  // 外部黑色
    }
    
    // 抗锯齿边缘
    color = vec3(smoothstep(0.002, 0.0, d));
    
    fragColor = vec4(color, 1.0);
}
```

**SDF 字体**：

SDF 字体用低分辨率纹理存储字符的 SDF，渲染时采样并阈值化，能任意缩放保持锐利边缘，是游戏 UI 字体的标准技术。

### 例子

```glsl
#version 330 core
out vec4 fragColor;
uniform float iTime;
uniform vec2 iResolution;

// 2D SDF
float sd_circle(vec2 p, float r) { return length(p) - r; }
float sd_box(vec2 p, vec2 b) {
    vec2 d = abs(p) - b;
    return length(max(d, 0.0)) + min(max(d.x, d.y), 0.0);
}

// 平滑并集
float op_smooth_union(float d1, float d2, float k) {
    float h = clamp(0.5 + 0.5*(d2-d1)/k, 0.0, 1.0);
    return mix(d2, d1, h) - k*h*(1.0-h);
}

void main() {
    vec2 uv = (gl_FragCoord.xy - 0.5 * iResolution.xy) / iResolution.y * 3.0;
    
    // 旋转的圆
    float t = iTime;
    vec2 c1 = vec2(cos(t), sin(t)) * 0.5;
    vec2 c2 = vec2(cos(t + 2.094), sin(t + 2.094)) * 0.5;
    vec2 c3 = vec2(cos(t + 4.188), sin(t + 4.188)) * 0.5;
    
    float d1 = sd_circle(uv - c1, 0.4);
    float d2 = sd_circle(uv - c2, 0.4);
    float d3 = sd_circle(uv - c3, 0.4);
    
    // 平滑融合
    float d = op_smooth_union(d1, d2, 0.3);
    d = op_smooth_union(d, d3, 0.3);
    
    // 渲染
    vec3 color = vec3(0.0);
    if (d < 0.0) {
        // 内部：根据距离渐变
        color = mix(vec3(1, 0.3, 0.5), vec3(0.3, 0.7, 1), -d);
    } else {
        // 外部：发光
        color = vec3(0.5, 0.7, 1.0) * exp(-d * 5.0) * 0.5;
    }
    
    // 抗锯齿
    color *= smoothstep(0.002, -0.002, d) + smoothstep(0.002, 0.0, abs(d)) * 0.5;
    
    fragColor = vec4(color, 1.0);
}
```

### 总结

- SDF 是有符号距离场，内部负、表面 0、外部正。
- 2D/3D 都有丰富的 SDF 公式（iquilezles.org 是宝库）。
- **变换**：平移、旋转、缩放、重复、扭曲。
- **组合**：并集、交集、差集、平滑并集。
- **2D 渲染**：阈值化 + 抗锯齿 + 发光。
- **应用**：Ray Marching、字体、UI、阴影、碰撞检测。
- **常见坑**：缩放后忘记乘回 s；平滑并集的 k 值不当导致形状模糊。

---

## 第 20 讲 · 后处理与色彩分级

### 概念

**后处理（Post-Processing）** 在渲染完成后对整个画面应用特效：辉光、景深、色彩分级、暗角、胶片颗粒。后处理让 Demo 画面有"电影感"，是现代 Demo 提升视觉品质的关键步骤。

**色彩分级（Color Grading）** 是后处理的核心——调整画面的色调、对比度、饱和度，营造特定氛围。电影工业用 LUT（查找表）做色彩分级，DemoScene 用着色器实时计算。

### 原理

**后处理流程**：

```
1. 渲染场景到纹理（Render Target）
2. 对纹理应用后处理着色器
3. 输出到屏幕
```

**辉光（Bloom）**：

提取画面亮部，模糊后叠加回原图，产生发光效果：

```glsl
// 1. 提取亮部
vec3 bright = max(color - 1.0, 0.0);

// 2. 模糊（多次高斯模糊）
vec3 blurred = gaussian_blur(bright);

// 3. 叠加
vec3 result = color + blurred;
```

**色彩分级**：

```glsl
// 亮度/对比度
color = (color - 0.5) * contrast + 0.5 + brightness;

// 饱和度
float gray = dot(color, vec3(0.299, 0.587, 0.114));
color = mix(vec3(gray), color, saturation);

// 色调偏移
color.r += tint_r;
color.g += tint_g;
color.b += tint_b;

// 色调映射（HDR → LDR）
color = color / (color + 1.0);  // Reinhard
// 或 ACES filmic
color = (color * (2.51 * color + 0.03)) / (color * (2.43 * color + 0.59) + 0.14);
```

**LUT 色彩分级**：

用预计算的 3D 查找表映射颜色：

```glsl
uniform sampler3D lut;

vec3 graded = texture(lut, color).rgb;
```

**暗角（Vignette）**：

```glsl
vec2 uv = gl_FragCoord.xy / iResolution.xy;
float vignette = 1.0 - length(uv - 0.5) * 1.5;
color *= vignette;
```

**胶片颗粒**：

```glsl
float noise = fract(sin(dot(uv, vec2(12.9898, 78.233)) + iTime) * 43758.5453);
color += (noise - 0.5) * 0.05;
```

**色差（Chromatic Aberration）**：

模拟镜头边缘的颜色分离：

```glsl
float r = texture(scene, uv + offset).r;
float g = texture(scene, uv).g;
float b = texture(scene, uv - offset).b;
color = vec3(r, g, b);
```

### 例子

```glsl
#version 330 core
out vec4 fragColor;
uniform float iTime;
uniform vec2 iResolution;

// 简单场景（彩色圆）
vec3 render_scene(vec2 uv) {
    float d = length(uv) - 0.3;
    vec3 color = vec3(0.0);
    if (d < 0.0) {
        color = vec3(1.0, 0.5, 0.2);
    } else {
        // 发光
        color = vec3(1.0, 0.5, 0.2) * exp(-d * 10.0) * 0.5;
    }
    return color;
}

void main() {
    vec2 uv = (gl_FragCoord.xy - 0.5 * iResolution.xy) / iResolution.y;
    
    // 色差
    float ca = 0.005;
    vec3 color;
    color.r = render_scene(uv + vec2(ca, 0)).r;
    color.g = render_scene(uv).g;
    color.b = render_scene(uv - vec2(ca, 0)).b;
    
    // 色彩分级
    // 对比度
    color = (color - 0.5) * 1.2 + 0.5;
    // 饱和度
    float gray = dot(color, vec3(0.299, 0.587, 0.114));
    color = mix(vec3(gray), color, 1.3);
    // 色调偏移（冷色调）
    color.b += 0.05;
    color.r -= 0.02;
    
    // 色调映射
    color = color / (color + 1.0);
    
    // 暗角
    vec2 vuv = gl_FragCoord.xy / iResolution.xy;
    float vignette = 1.0 - length(vuv - 0.5) * 1.2;
    color *= clamp(vignette, 0.0, 1.0);
    
    // 胶片颗粒
    float noise = fract(sin(dot(gl_FragCoord.xy, vec2(12.9898, 78.233)) + iTime) * 43758.5453);
    color += (noise - 0.5) * 0.03;
    
    fragColor = vec4(color, 1.0);
}
```

### 总结

- 后处理在渲染后对画面应用特效，提升视觉品质。
- **辉光**：提取亮部 → 模糊 → 叠加。
- **色彩分级**：亮度/对比度/饱和度/色调偏移/色调映射。
- **LUT** 用预计算查找表映射颜色。
- **暗角、颗粒、色差** 模拟镜头效果。
- **应用场景**：电影感 Demo、游戏后处理、UI 美化。
- **常见坑**：后处理过多导致性能下降；色调映射不当导致颜色失真。

---

# 第 6 章 · 音频合成

DemoScene 的音乐不是预录制的 MP3，而是用代码实时合成的——合成器、跟踪器、DSP 效果全部在程序内生成。一个 64K Intro 能包含完整电子乐，全靠音频合成技术。本章从波形合成开始，讲解合成器与包络、跟踪器与模块音乐、DSP 效果与混音。

## 第 21 讲 · 声音基础与波形合成

### 概念

**声音** 是空气压力的周期性变化，可表示为时间函数 `y(t)`。数字音频用采样率（如 44100Hz）离散化这个函数，每秒存储 44100 个样本。

**波形合成** 是用数学函数生成声音——正弦波、方波、锯齿波、三角波是基础波形，不同波形有不同的谐波结构，产生不同音色。

### 原理

**基础波形**：

1. **正弦波（Sine）**：最纯净，只有基频，无谐波。音色柔和、空洞。
   ```
   y = sin(2π * f * t)  // f 是频率
   ```

2. **方波（Square）**：只有奇次谐波（1f, 3f, 5f...）。音色明亮、蜂鸣。
   ```
   y = sign(sin(2π * f * t))  // 或 sin > 0 ? 1 : -1
   ```

3. **锯齿波（Sawtooth）**：所有谐波（1f, 2f, 3f...），振幅递减。音色丰富、刺耳。
   ```
   y = 2 * (t * f - floor(t * f + 0.5))  // 或 2 * frac(t*f) - 1
   ```

4. **三角波（Triangle）**：只有奇次谐波，振幅递减更快。音色柔和、介于正弦与方波之间。
   ```
   y = 2 * abs(2 * (t * f - floor(t * f + 0.5))) - 1
   ```

**频率与音高**：

音乐音高用音名（A4, C5）表示，对应频率：

```
A4 = 440 Hz（标准音高）
频率 = 440 * 2^((n - 69) / 12)  // n 是 MIDI 音符号
```

MIDI 音符号 69 = A4 = 440Hz，每升高一个半音频率乘 2^(1/12)。

**音色与谐波**：

不同波形的谐波结构决定音色：

- 正弦波：只有基频，纯净。
- 方波：奇次谐波，蜂鸣。
- 锯齿波：所有谐波，丰富。
- 三角波：奇次谐波（弱），柔和。

**实时合成**：

```c
// 生成 1 秒 440Hz 正弦波
float sample_rate = 44100;
float frequency = 440;
float duration = 1.0;
int num_samples = sample_rate * duration;

for (int i = 0; i < num_samples; ++i) {
    float t = i / sample_rate;
    float sample = sin(2 * M_PI * frequency * t);
    output[i] = sample;  // [-1, 1]
}
```

**SDL2 音频回调**：

```c
// SDL2 音频回调
void audio_callback(void* userdata, Uint8* stream, int len) {
    float* out = (float*)stream;
    int num_samples = len / sizeof(float);
    for (int i = 0; i < num_samples; ++i) {
        out[i] = synthesize_sample();  // 实时合成
    }
}

SDL_AudioSpec spec;
spec.freq = 44100;
spec.format = AUDIO_F32;
spec.channels = 1;
spec.samples = 1024;
spec.callback = audio_callback;
SDL_OpenAudio(&spec, nullptr);
SDL_PauseAudio(0);  // 开始播放
```

### 例子

```c
#include <SDL2/SDL.h>
#include <cmath>

// 全局状态
struct SynthState {
    double phase = 0;
    double frequency = 440;
    double sample_rate = 44100;
    int waveform = 0;  // 0=sine, 1=square, 2=saw, 3=triangle
} state;

// 波形生成
float generate_wave(double phase, int type) {
    switch (type) {
        case 0: return sin(phase);  // 正弦
        case 1: return sin(phase) > 0 ? 1.0f : -1.0f;  // 方波
        case 2: {  // 锯齿
            double p = phase / (2 * M_PI);
            return (float)(2 * (p - floor(p + 0.5)));
        }
        case 3: {  // 三角
            double p = phase / (2 * M_PI);
            return (float)(2 * fabs(2 * (p - floor(p + 0.5))) - 1);
        }
    }
    return 0;
}

// 音频回调
void audio_callback(void* userdata, Uint8* stream, int len) {
    float* out = (float*)stream;
    int num_samples = len / sizeof(float);
    for (int i = 0; i < num_samples; ++i) {
        float sample = generate_wave(state.phase, state.waveform) * 0.2f;  // 音量
        out[i] = sample;
        state.phase += 2 * M_PI * state.frequency / state.sample_rate;
        if (state.phase > 2 * M_PI) state.phase -= 2 * M_PI;
    }
}

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_AUDIO | SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Waveform Synth",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 400, 100, 0);

    SDL_AudioSpec spec;
    spec.freq = 44100;
    spec.format = AUDIO_F32;
    spec.channels = 1;
    spec.samples = 1024;
    spec.callback = audio_callback;
    spec.userdata = nullptr;
    SDL_OpenAudio(&spec, nullptr);
    SDL_PauseAudio(0);

    bool running = true;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) {
            if (e.type == SDL_QUIT) running = false;
            if (e.type == SDL_KEYDOWN) {
                switch (e.key.keysym.sym) {
                    case SDLK_1: state.waveform = 0; break;  // 正弦
                    case SDLK_2: state.waveform = 1; break;  // 方波
                    case SDLK_3: state.waveform = 2; break;  // 锯齿
                    case SDLK_4: state.waveform = 3; break;  // 三角
                    case SDLK_z: state.frequency = 261.63; break;  // C4
                    case SDLK_x: state.frequency = 293.66; break;  // D4
                    case SDLK_c: state.frequency = 329.63; break;  // E4
                    case SDLK_v: state.frequency = 349.23; break;  // F4
                    case SDLK_b: state.frequency = 392.00; break;  // G4
                    case SDLK_n: state.frequency = 440.00; break;  // A4
                    case SDLK_m: state.frequency = 493.88; break;  // B4
                }
            }
        }
    }

    SDL_CloseAudio();
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- 声音是空气压力变化，数字音频用采样率离散化。
- 四种基础波形：正弦（纯净）、方波（奇次谐波）、锯齿（所有谐波）、三角（柔和）。
- 频率与音高：`f = 440 * 2^((n-69)/12)`，MIDI 音符号 69 = A4。
- **实时合成**：在音频回调中逐样本生成。
- **SDL2 音频**：`SDL_OpenAudio` + 回调函数。
- **常见坑**：音量过大导致削波；相位累积误差导致音高漂移。

---

## 第 22 讲 · 合成器与包络

### 概念

**合成器（Synthesizer）** 把基础波形、包络、滤波器、效果器组合，产生丰富音色。DemoScene 的音乐全部由合成器实时生成，不存储任何采样。

**包络（Envelope）** 描述声音参数随时间变化——最常用的是 ADSR（Attack-Decay-Sustain-Release），控制音量从按下到释放的演变。

### 原理

**ADSR 包络**：

```
    /\____
   / |    \____
  /  |         \____
 /   |              \
A    D    S          R
```

- **Attack（起音）**：按下瞬间音量从 0 上升到峰值，通常 0.01~0.1 秒。
- **Decay（衰减）**：从峰值下降到 sustain 电平，通常 0.05~0.5 秒。
- **Sustain（持续）**：按住期间保持的电平（0~1），不是时间。
- **Release（释放）**：松开后从 sustain 下降到 0，通常 0.1~2 秒。

**ADSR 实现**：

```c
struct Envelope {
    float attack, decay, sustain, release;
    float time;
    bool holding;
    float release_start_level;

    float get_value() {
        if (holding) {
            if (time < attack) return time / attack;
            if (time < attack + decay) {
                return 1.0f - (1.0f - sustain) * (time - attack) / decay;
            }
            return sustain;
        } else {
            // 释放阶段
            if (time < release) {
                return release_start_level * (1.0f - time / release);
            }
            return 0;
        }
    }

    void note_on() { holding = true; time = 0; }
    void note_off() {
        holding = false;
        release_start_level = get_value();
        time = 0;
    }
    void update(float dt) { time += dt; }
};
```

**振荡器（Oscillator）**：

```c
struct Oscillator {
    double phase = 0;
    double frequency = 440;
    int waveform = 0;  // 0=sine, 1=square, 2=saw, 3=triangle

    float sample(double sample_rate) {
        float value = generate_wave(phase, waveform);
        phase += 2 * M_PI * frequency / sample_rate;
        if (phase > 2 * M_PI) phase -= 2 * M_PI;
        return value;
    }
};
```

**合成器结构**：

```c
struct Synth {
    Oscillator osc;
    Envelope env;
    float volume = 0.3;

    float sample(double sample_rate) {
        return osc.sample(sample_rate) * env.get_value() * volume;
    }

    void note_on(float freq) {
        osc.frequency = freq;
        env.note_on();
    }
    void note_off() { env.note_off(); }
};
```

**多振荡器叠加**：

丰富音色用多个振荡器叠加（detune 产生合唱感）：

```c
struct Voice {
    Oscillator osc1, osc2, osc3;
    Envelope env;
    float detune = 1.005;  // osc2 比 osc1 高一点

    float sample(double sr) {
        float s = osc1.sample(sr);
        s += osc2.sample(sr) * 0.5;  // osc2 detune
        s += osc3.sample(sr) * 0.3;  // osc3 低八度
        return s * env.get_value() * 0.3;
    }
};
```

**滤波器**：

低通滤波器（LPF）让低频通过，高频衰减，是合成器最常用的滤波器：

```c
// 一阶低通滤波器
struct LowPass {
    float cutoff = 1000;
    float prev = 0;

    float process(float input, double sample_rate) {
        float alpha = 2 * M_PI * cutoff / sample_rate;
        alpha = alpha / (1 + alpha);
        prev = prev + alpha * (input - prev);
        return prev;
    }
};
```

### 例子

```c
#include <SDL2/SDL.h>
#include <cmath>
#include <vector>

struct Envelope {
    float attack = 0.01, decay = 0.1, sustain = 0.7, release = 0.3;
    float time = 0;
    bool holding = false;
    float release_level = 0;

    float value() {
        if (holding) {
            if (time < attack) return time / attack;
            if (time < attack + decay)
                return 1.0f - (1.0f - sustain) * (time - attack) / decay;
            return sustain;
        }
        if (time < release) return release_level * (1.0f - time / release);
        return 0;
    }
    void note_on() { holding = true; time = 0; }
    void note_off() { holding = false; release_level = value(); time = 0; }
    void update(float dt) { time += dt; }
};

struct Synth {
    double phase = 0;
    double frequency = 440;
    Envelope env;
    float volume = 0.3;

    float sample(double sr) {
        float wave = 2 * (phase / (2*M_PI) - floor(phase / (2*M_PI) + 0.5));  // 锯齿
        phase += 2 * M_PI * frequency / sr;
        if (phase > 2*M_PI) phase -= 2*M_PI;
        return wave * env.value() * volume;
    }
    void note_on(float freq) { frequency = freq; env.note_on(); }
    void note_off() { env.note_off(); }
};

Synth synth;
double sample_rate = 44100;
double phase_accum = 0;

void audio_callback(void* ud, Uint8* stream, int len) {
    float* out = (float*)stream;
    int n = len / sizeof(float);
    for (int i = 0; i < n; ++i) {
        out[i] = synth.sample(sample_rate);
        synth.env.update(1.0 / sample_rate);
    }
}

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_AUDIO | SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Synth",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 400, 100, 0);

    SDL_AudioSpec spec;
    spec.freq = 44100;
    spec.format = AUDIO_F32;
    spec.channels = 1;
    spec.samples = 1024;
    spec.callback = audio_callback;
    SDL_OpenAudio(&spec, nullptr);
    SDL_PauseAudio(0);

    bool running = true;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) {
            if (e.type == SDL_QUIT) running = false;
            if (e.type == SDL_KEYDOWN) {
                float freqs[] = {261.63, 293.66, 329.63, 349.23, 392, 440, 493.88};
                int keys[] = {SDLK_z, SDLK_x, SDLK_c, SDLK_v, SDLK_b, SDLK_n, SDLK_m};
                for (int i = 0; i < 7; ++i) {
                    if (e.key.keysym.sym == keys[i]) synth.note_on(freqs[i]);
                }
            }
            if (e.type == SDL_KEYUP) {
                int keys[] = {SDLK_z, SDLK_x, SDLK_c, SDLK_v, SDLK_b, SDLK_n, SDLK_m};
                for (int i = 0; i < 7; ++i) {
                    if (e.key.keysym.sym == keys[i]) synth.note_off();
                }
            }
        }
    }

    SDL_CloseAudio();
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- 合成器 = 振荡器 + 包络 + 滤波器 + 效果器。
- **ADSR 包络**：Attack（起音）、Decay（衰减）、Sustain（持续电平）、Release（释放）。
- 多振荡器叠加（detune）产生合唱感。
- **低通滤波器**让音色柔和，是合成器核心。
- **应用场景**：Demo 音乐、游戏音效、电子音乐。
- **常见坑**：包络时间过短导致爆音；多音叠加音量过大削波。

---

## 第 23 讲 · 跟踪器与模块音乐

### 概念

**跟踪器（Tracker）** 是 DemoScene 标准的音乐制作工具——用文本格式描述音符、乐器、效果，文件极小（几 KB），适合 4K/64K Intro。

**模块音乐（Module Music）** 是跟踪器生成的音乐格式（MOD、XM、IT、S3M），包含乐器（采样或合成）与乐谱（音符序列）。DemoScene 用自定义跟踪器格式，乐器用合成器生成，乐谱用紧凑编码。

### 原理

**跟踪器界面**：

```
+--------+--------+--------+--------+
| Track1 | Track2 | Track3 | Track4 |  ← 4 个音轨
+--------+--------+--------+--------+
| C-4 01 | ....   | E-4 01 | ....   |  ← 行 0
| ... 02 | D-4 01 | ... 02 | ....   |  ← 行 1
| E-4 01 | ....   | G-4 01 | ....   |  ← 行 2
+--------+--------+--------+--------+
```

每行是一个 16 分音符，每列是一个音轨。格式：`音符 乐器 效果`。

**音符编码**：

```
C-4 = C4 音符（音名 + 八度）
... = 无音符（延续上一音）
=== = 音符关闭
```

**乐器**：

跟踪器乐器可以是采样（MOD 格式）或合成参数（DemoScene 自定义）。DemoScene 倾向合成，节省空间。

**模式（Pattern）**：

音乐由多个模式组成，每个模式是若干行的音符序列。模式可重复使用，节省空间：

```
Song = [Pattern 0, Pattern 1, Pattern 0, Pattern 2, ...]
```

**节拍与速度**：

- **速度（Speed）**：每行多少 tick（通常 6）。
- **节拍（Tempo）**：每分钟多少拍（BPM）。
- **采样率**：每秒样本数。

行持续时间 = (speed * 2.5) / BPM 秒。

**紧凑编码**：

DemoScene 用极紧凑的乐谱编码：

```c
// 每个音符 1 字节：高 4 位音高，低 4 位乐器/效果
uint8_t note = (pitch << 4) | instrument;
```

或用位打包：

```c
// 每行打包为 32 位
struct Row {
    uint8_t note[4];  // 4 个音轨的音符
    uint8_t instrument[4];  // 乐器
    uint8_t effect[4];  // 效果
};
```

**实时播放**：

```c
struct Tracker {
    Pattern* patterns;
    int* song_order;  // 模式顺序
    int current_pattern, current_row;
    float row_timer;
    float row_duration;
    Synth voices[4];  // 4 个音轨

    void update(float dt) {
        row_timer += dt;
        if (row_timer >= row_duration) {
            row_timer = 0;
            play_row();
            current_row++;
            if (current_row >= 64) {  // 每模式 64 行
                current_row = 0;
                current_pattern++;
            }
        }
    }

    void play_row() {
        Pattern& p = patterns[song_order[current_pattern]];
        for (int t = 0; t < 4; ++t) {
            if (p.rows[current_row].note[t] > 0) {
                voices[t].note_on(note_to_freq(p.rows[current_row].note[t]));
            }
        }
    }
};
```

### 例子

```c
#include <SDL2/SDL.h>
#include <cmath>
#include <vector>

// 简化跟踪器
struct Note { int pitch; int instrument; };  // pitch: 0=无, 1-12=音高
struct Pattern { std::vector<Note> tracks[4]; };  // 4 音轨

struct Tracker {
    std::vector<Pattern> patterns;
    std::vector<int> order;  // 模式顺序
    int pattern_idx = 0, row = 0;
    float timer = 0;
    float row_duration = 0.15;  // 每行 0.15 秒
    double phases[4] = {0,0,0,0};
    double freqs[4] = {0,0,0,0};
    bool active[4] = {0,0,0,0};

    float note_to_freq(int pitch) {
        // pitch 1-12 对应 C4-B4
        return 261.63 * pow(2, (pitch - 1) / 12.0);
    }

    void update(float dt, double sr) {
        timer += dt;
        if (timer >= row_duration) {
            timer = 0;
            if (pattern_idx < order.size()) {
                Pattern& p = patterns[order[pattern_idx]];
                if (row < p.tracks[0].size()) {
                    for (int t = 0; t < 4; ++t) {
                        if (row < p.tracks[t].size() && p.tracks[t][row].pitch > 0) {
                            freqs[t] = note_to_freq(p.tracks[t][row].pitch);
                            active[t] = true;
                        }
                    }
                }
                row++;
                if (row >= 64) { row = 0; pattern_idx++; }
            }
        }
    }

    float sample(double sr) {
        float out = 0;
        for (int t = 0; t < 4; ++t) {
            if (active[t]) {
                float wave = sin(phases[t]);  // 正弦波
                out += wave * 0.15;
                phases[t] += 2 * M_PI * freqs[t] / sr;
                if (phases[t] > 2*M_PI) phases[t] -= 2*M_PI;
            }
        }
        return out;
    }
};

Tracker tracker;
double sample_rate = 44100;

void audio_callback(void* ud, Uint8* stream, int len) {
    float* out = (float*)stream;
    int n = len / sizeof(float);
    for (int i = 0; i < n; ++i) {
        out[i] = tracker.sample(sample_rate);
        tracker.update(1.0 / sample_rate, sample_rate);
    }
}

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_AUDIO | SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Tracker",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 400, 100, 0);

    // 创建简单模式：C 大调音阶
    Pattern p;
    int scale[] = {1, 3, 5, 6, 8, 10, 12, 10, 8, 6, 5, 3, 1, 0, 0, 0};
    for (int t = 0; t < 4; ++t) p.tracks[t].resize(64);
    for (int i = 0; i < 16; ++i) {
        if (scale[i] > 0) p.tracks[0][i] = {scale[i], 1};
    }
    // 低音
    int bass[] = {1, 0, 0, 0, 6, 0, 0, 0, 8, 0, 0, 0, 6, 0, 0, 0};
    for (int i = 0; i < 16; ++i) {
        if (bass[i] > 0) p.tracks[1][i] = {bass[i] - 12, 1};  // 低八度
    }
    tracker.patterns.push_back(p);
    tracker.order = {0, 0, 0, 0};  // 重复 4 次

    SDL_AudioSpec spec;
    spec.freq = 44100;
    spec.format = AUDIO_F32;
    spec.channels = 1;
    spec.samples = 1024;
    spec.callback = audio_callback;
    SDL_OpenAudio(&spec, nullptr);
    SDL_PauseAudio(0);

    bool running = true;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) if (e.type == SDL_QUIT) running = false;
    }

    SDL_CloseAudio();
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- 跟踪器用文本格式描述音乐，文件极小，适合 DemoScene。
- 模块音乐 = 乐器 + 乐谱（模式序列）。
- **模式**是若干行音符序列，可重复使用。
- **节拍与速度**：行持续时间 = (speed * 2.5) / BPM。
- **紧凑编码**：每音符 1 字节，位打包。
- **常见坑**：模式顺序错误导致音乐错乱；音轨过多导致音量过大。

---

## 第 24 讲 · DSP 效果与混音

### 概念

**DSP（Digital Signal Processing）效果** 改变声音特性：混响、延迟、合唱、失真、压缩。DemoScene 用 DSP 让合成器声音更丰富、专业。

**混音（Mixing）** 把多个音轨合成为最终输出，调整音量、声像、效果。好的混音让每个声音都清晰可辨。

### 原理

**延迟（Delay）**：

把声音延迟一段时间后叠加，产生回声：

```c
struct Delay {
    std::vector<float> buffer;
    int write_pos = 0;
    float delay_time = 0.3;  // 秒
    float feedback = 0.5;    // 反馈量
    double sample_rate;

    float process(float input) {
        int delay_samples = delay_time * sample_rate;
        int read_pos = (write_pos - delay_samples + buffer.size()) % buffer.size();
        float delayed = buffer[read_pos];
        buffer[write_pos] = input + delayed * feedback;
        write_pos = (write_pos + 1) % buffer.size();
        return input + delayed * 0.7;  // 干湿混合
    }
};
```

**混响（Reverb）**：

模拟空间反射，产生环境感。简单实现用多个延迟叠加，复杂用卷积或 Schroeder 混响：

```c
// 简化混响：多个延迟叠加
struct Reverb {
    Delay delays[4];
    float process(float input) {
        float out = input;
        for (auto& d : delays) out += d.process(input) * 0.25;
        return out;
    }
};
```

**合唱（Chorus）**：

用轻微 detune 的延迟产生合唱感：

```c
struct Chorus {
    std::vector<float> buffer;
    int write_pos = 0;
    double phase = 0;
    double rate = 0.5;  // 调制速率
    double depth = 0.003;  // 调制深度（秒）
    double sample_rate;

    float process(float input) {
        // LFO 调制延迟时间
        double lfo = sin(phase) * depth * sample_rate;
        phase += 2 * M_PI * rate / sample_rate;
        int read_pos = (write_pos - (int)(0.02 * sample_rate) - (int)lfo + buffer.size()) % buffer.size();
        float delayed = buffer[read_pos];
        buffer[write_pos] = input;
        write_pos = (write_pos + 1) % buffer.size();
        return input + delayed * 0.5;
    }
};
```

**失真（Distortion）**：

用非线性函数产生失真：

```c
float distort(float input, float drive) {
    return tanh(input * drive) / tanh(drive);
}
```

**压缩器（Compressor）**：

自动调整音量，让动态范围更小：

```c
struct Compressor {
    float threshold = 0.5;
    float ratio = 4;
    float attack = 0.01, release = 0.1;
    float envelope = 0;

    float process(float input, double dt) {
        float level = fabs(input);
        if (level > envelope) envelope += (level - envelope) * dt / attack;
        else envelope += (level - envelope) * dt / release;

        float gain = 1;
        if (envelope > threshold) {
            gain = 1 - (envelope - threshold) * (1 - 1/ratio) / envelope;
        }
        return input * gain;
    }
};
```

**混音**：

```c
float mix(float* tracks, int num_tracks, float* volumes) {
    float sum = 0;
    for (int i = 0; i < num_tracks; ++i) {
        sum += tracks[i] * volumes[i];
    }
    return sum / num_tracks;  // 防止削波
}
```

**声像（Panning）**：

```c
void pan(float input, float pan, float& left, float& right) {
    left = input * cos(pan * M_PI / 2);
    right = input * sin(pan * M_PI / 2);
}
```

### 例子

```c
#include <SDL2/SDL.h>
#include <cmath>
#include <vector>

struct Delay {
    std::vector<float> buffer;
    int write_pos = 0;
    float feedback = 0.4;

    Delay(double sr, float time) {
        buffer.resize(time * sr, 0);
    }
    float process(float input) {
        float delayed = buffer[write_pos];
        buffer[write_pos] = input + delayed * feedback;
        write_pos = (write_pos + 1) % buffer.size();
        return input + delayed * 0.6;
    }
};

Delay* delay;
double phase = 0;
double sample_rate = 44100;

void audio_callback(void* ud, Uint8* stream, int len) {
    float* out = (float*)stream;
    int n = len / sizeof(float);
    for (int i = 0; i < n; ++i) {
        // 简单旋律
        double t = SDL_GetTicks() / 1000.0;
        double freq = 220 * pow(2, ((int)(t * 2) % 8) / 12.0);
        float wave = sin(phase) * 0.2;
        phase += 2 * M_PI * freq / sample_rate;
        if (phase > 2*M_PI) phase -= 2*M_PI;
        out[i] = delay->process(wave);
    }
}

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_AUDIO | SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("DSP Effects",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 400, 100, 0);

    delay = new Delay(sample_rate, 0.3);

    SDL_AudioSpec spec;
    spec.freq = 44100;
    spec.format = AUDIO_F32;
    spec.channels = 1;
    spec.samples = 1024;
    spec.callback = audio_callback;
    SDL_OpenAudio(&spec, nullptr);
    SDL_PauseAudio(0);

    bool running = true;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) if (e.type == SDL_QUIT) running = false;
    }

    delete delay;
    SDL_CloseAudio();
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- DSP 效果改变声音特性：延迟、混响、合唱、失真、压缩。
- **延迟**：缓冲 + 反馈，产生回声。
- **混响**：多延迟叠加，模拟空间。
- **合唱**：LFO 调制延迟，产生合唱感。
- **失真**：tanh 等非线性函数。
- **压缩器**：自动调整音量，控制动态范围。
- **混音**：多音轨加权求和，防止削波。
- **常见坑**：反馈过大导致啸叫；延迟缓冲区过小导致杂音。

---

# 第 7 章 · 代码压缩

4K Intro 是 DemoScene 最具挑战的类别——用 4096 字节创造完整视听作品。这需要极致的代码压缩与过程化生成。本章讲解 4K Intro 工具链、Shader 工具链与压缩、过程化资产生成、尺寸优化技巧。掌握这些，你就能理解为什么 DemoScene 程序员会为节省一个字节而欣喜若狂。

## 第 25 讲 · 4K Intro 工具链

### 概念

**4K Intro** 是 DemoScene 最严格的尺寸限制类别——可执行文件必须 ≤ 4096 字节。在这 4KB 内要包含：图形渲染、音乐合成、输入处理、窗口管理。这需要特殊的工具链与极致的优化。

**4K 工具链**的核心组件：

1. **编译器**：MSVC、GCC、Clang，需配置生成最小代码。
2. **链接器**：Crinkler（Windows 4K 标配）、UPX（通用压缩）。
3. **运行时**：最小化 CRT，或完全去除。
4. **图形 API**：OpenGL（跨平台）、DirectX（Windows）。
5. **音频 API**：DirectSound、WASAPI、SDL2。

### 原理

**4K Intro 的尺寸构成**：

```
4096 字节总预算：
├── PE 头 + 引入表：~500 字节
├── 代码：~2000 字节
├── 着色器源码：~1000 字节
├── 音乐数据：~500 字节
└── 资源（纹理等）：~0 字节（全部过程化）
```

**Crinkler 链接器**：

Crinkler 是 4K Intro 专用链接器，用极紧凑的 PE 头与导入表压缩：

```
cl /c /O1 /GS- demo.c        # 编译，最小化代码
crinkler demo.obj /ENTRY:main /SUBSYSTEM:WINDOWS /OUT:demo.exe
```

Crinkler 的特性：

- **压缩 PE 头**：标准 PE 头 ~500 字节，Crinkler 压到 ~200 字节。
- **合并引入表**：把所有 API 调用合并为紧凑表。
- **代码压缩**：用变换编码压缩代码段。

**去除 CRT**：

C 运行时（CRT）包含 printf、malloc 等，占用数 KB。4K Intro 必须去除：

```c
// 不用 CRT，自己实现入口
void main() {
    // 直接调用 Win32 API
    ExitProcess(0);
}

// 链接选项
/ENTRY:main /NODEFAULTLIB
```

**最小化入口**：

```c
#include <windows.h>

int WINAPI WinMain(HINSTANCE, HINSTANCE, LPSTR, int) {
    // 创建窗口、初始化 OpenGL、主循环
    return 0;
}
```

**API 调用优化**：

每个 API 调用需引入表项，占空间。4K Intro 用 GetProcAddress 动态获取：

```c
// 动态获取所有 API，只需引入 GetProcAddress 与 LoadLibrary
HMODULE opengl32 = LoadLibrary("opengl32");
void* glClear = GetProcAddress(opengl32, "glClear");
```

**着色器压缩**：

着色器源码占大量空间，用压缩工具（如 ShaderMinifier）：

```
原版：
uniform float iTime;
void main() {
    gl_FragColor = vec4(sin(iTime), 0, 0, 1);
}

压缩后：
uniform float i;void main(){gl_FragColor=vec4(sin(i),0,0,1);}
```

**音乐数据压缩**：

音乐用紧凑编码（第 23 讲），或用合成器参数而非乐谱。

### 例子

```c
// 最小化 4K Intro 框架（伪代码）
#include <windows.h>
#include <GL/gl.h>

// 动态加载 OpenGL 函数
PFNGLCREATESHADERPROC glCreateShader;
PFNGLSHADERSOURCEPROC glShaderSource;
// ...

void load_gl() {
    HMODULE h = LoadLibrary("opengl32");
    glCreateShader = (PFNGLCREATESHADERPROC)GetProcAddress(h, "glCreateShader");
    glShaderSource = (PFNGLSHADERSOURCEPROC)GetProcAddress(h, "glShaderSource");
    // ...
}

// 着色器源码（压缩后）
const char* frag_src =
"uniform float i;out vec4 o;"
"void main(){vec2 u=gl_FragCoord.xy/vec2(800,600);"
"o=vec4(u,sin(i),1);}";

int WINAPI WinMain(HINSTANCE hInst, HINSTANCE, LPSTR, int) {
    // 创建窗口
    WNDCLASS wc = {0};
    wc.lpfnWndProc = DefWindowProc;
    wc.lpszClassName = "d";
    RegisterClass(&wc);
    HWND hwnd = CreateWindow("d", 0, WS_POPUP, 0, 0, 800, 600, 0, 0, hInst, 0);
    
    // 像素格式 + OpenGL 上下文
    HDC dc = GetDC(hwnd);
    PIXELFORMATDESCRIPTOR pfd = {sizeof(pfd), 1, PFD_DRAW_TO_WINDOW | PFD_SUPPORT_OPENGL | PFD_DOUBLEBUFFER, PFD_TYPE_RGBA, 32};
    SetPixelFormat(dc, ChoosePixelFormat(dc, &pfd), &pfd);
    wglMakeCurrent(dc, wglCreateContext(dc));
    
    load_gl();
    ShowWindow(hwnd, SW_SHOW);
    
    // 编译着色器
    GLuint s = glCreateShader(GL_FRAGMENT_SHADER);
    glShaderSource(s, 1, &frag_src, 0);
    glCompileShader(s);
    GLuint p = glCreateProgram();
    glAttachShader(p, s);
    glLinkProgram(p);
    glUseProgram(p);
    
    // 主循环
    MSG msg;
    while (!GetMessage(&msg, 0, 0, 0)) {
        glUniform1f(glGetUniformLocation(p, "i"), GetTickCount() / 1000.0f);
        glRecti(-1, -1, 1, 1);
        SwapBuffers(dc);
    }
    return 0;
}
```

**编译命令**：

```bash
cl /c /O1 /GS- /GL demo.c
crinkler demo.obj /ENTRY:WinMain /SUBSYSTEM:WINDOWS /OUT:demo.exe
```

### 总结

- 4K Intro 用 4096 字节创造完整视听作品，需极致优化。
- **Crinkler** 是 4K 标配链接器，压缩 PE 头与导入表。
- **去除 CRT**：自己实现入口，不链接运行时库。
- **动态加载 API**：用 GetProcAddress 避免导入表膨胀。
- **着色器压缩**：用 ShaderMinifier 等工具压缩源码。
- **常见坑**：忘记 /NODEFAULTLIB 导致 CRT 链入；优化选项不当导致代码膨胀。

---

## 第 26 讲 · Shader 工具链与压缩

### 概念

现代 4K Intro 几乎都用 Shader——片段着色器在 GPU 上并行计算，CPU 代码只需初始化与 uniform 更新。这把大部分"代码"转移到着色器，而着色器源码可压缩。

**Shader 工具链**包括：着色器编写、压缩、嵌入、编译。ShaderMinifier 是 DemoScene 标准的着色器压缩工具。

### 原理

**ShaderMinifier**：

ShaderMinifier 自动压缩 GLSL 源码：

1. **去除注释与空格**：`/* */`、`//`、多余空格。
2. **重命名变量**：`myColor` → `a`，`position` → `p`。
3. **合并声明**：`float a; float b;` → `float a,b;`。
4. **简化表达式**：`vec3(1,1,1)` → `vec3(1)`。
5. **去除冗余**：`0.0` → `0.`，`1.0` → `1.`。

**压缩示例**：

```
原版（200 字节）：
uniform float iTime;
uniform vec2 iResolution;

void main() {
    vec2 uv = gl_FragCoord.xy / iResolution.xy;
    vec3 color = vec3(uv.x, uv.y, sin(iTime));
    gl_FragColor = vec4(color, 1.0);
}

压缩后（80 字节）：
uniform float i;uniform vec2 r;
void main(){vec2 u=gl_FragCoord.xy/r;gl_FragColor=vec4(u,sin(i),1);}
```

**嵌入着色器**：

压缩后的着色器源码嵌入可执行文件：

```c
const char* shader =
#include "shader.glsl.h"
;
```

或用 `R"()"` 原始字符串：

```c
const char* shader = R"(
uniform float i;
void main(){...}
)";
```

**着色器编译**：

```c
GLuint compile_shader(const char* src) {
    GLuint s = glCreateShader(GL_FRAGMENT_SHADER);
    glShaderSource(s, 1, &src, nullptr);
    glCompileShader(s);
    return s;
}
```

**着色器调试**：

压缩后的着色器难以调试，开发时用未压缩版，发布时压缩：

```bash
# 开发：用原始着色器
demo.exe --debug-shader original.glsl

# 发布：压缩后嵌入
shader_minifier original.glsl -o shader.glsl.h
demo.exe
```

**ShaderToy 与 4K**：

ShaderToy 是着色器开发平台，可直接把作品移植到 4K Intro：

1. 在 ShaderToy 开发着色器。
2. 用 ShaderMinifier 压缩。
3. 嵌入 4K Intro 框架。

### 例子

```c
// 4K Intro 的着色器加载（伪代码）
#include <windows.h>
#include <GL/gl.h>

// 压缩后的着色器（实际用 ShaderMinifier 生成）
const char* frag_src = R"(#version 330
uniform float i;uniform vec2 r;out vec4 o;
void main(){vec2 u=(gl_FragCoord.xy-.5*r)/r.y;
float t=length(u)-.3;
o=vec4(.5+.5*sin(i+u.x*10),.5+.5*sin(i+u.y*10),.5+.5*sin(i+t*20),1);})";

PFNGLCREATESHADERPROC glCreateShader;
PFNGLSHADERSOURCEPROC glShaderSource;
PFNGLCOMPILESHADERPROC glCompileShader;
PFNGLCREATEPROGRAMPROC glCreateProgram;
PFNGLATTACHSHADERPROC glAttachShader;
PFNGLLINKPROGRAMPROC glLinkProgram;
PFNGLUSEPROGRAMPROC glUseProgram;
PFNGLGETUNIFORMLOCATIONPROC glGetUniformLocation;
PFNGLUNIFORM1FPROC glUniform1f;
PFNGLUNIFORM2FPROC glUniform2f;

void load_gl_functions() {
    HMODULE h = GetModuleHandle("opengl32");
    // 用 wglGetProcAddress 获取扩展函数
    glCreateShader = (PFNGLCREATESHADERPROC)wglGetProcAddress("glCreateShader");
    glShaderSource = (PFNGLSHADERSOURCEPROC)wglGetProcAddress("glShaderSource");
    // ...
}

int WINAPI WinMain(HINSTANCE hI, HINSTANCE, LPSTR, int) {
    // 窗口 + OpenGL 初始化（省略，见第 25 讲）
    // ...
    
    load_gl_functions();
    
    GLuint s = glCreateShader(GL_FRAGMENT_SHADER);
    glShaderSource(s, 1, &frag_src, 0);
    glCompileShader(s);
    GLuint p = glCreateProgram();
    glAttachShader(p, s);
    glLinkProgram(p);
    glUseProgram(p);
    
    GLint i_loc = glGetUniformLocation(p, "i");
    GLint r_loc = glGetUniformLocation(p, "r");
    
    MSG msg;
    while (1) {
        if (PeekMessage(&msg, 0, 0, 0, PM_REMOVE)) {
            if (msg.message == WM_QUIT) break;
            DispatchMessage(&msg);
        }
        glUniform1f(i_loc, GetTickCount() / 1000.0f);
        glUniform2f(r_loc, 800, 600);
        glRecti(-1, -1, 1, 1);
        SwapBuffers(dc);
    }
    return 0;
}
```

### 总结

- 现代 4K Intro 用 Shader 把计算转移到 GPU，CPU 代码极简。
- **ShaderMinifier** 自动压缩 GLSL：去空格、重命名、合并声明。
- 压缩后着色器嵌入可执行文件，运行时编译。
- **开发流程**：ShaderToy 开发 → ShaderMinifier 压缩 → 嵌入 4K 框架。
- **常见坑**：压缩后变量名冲突；不同 GLSL 版本兼容性。

---

## 第 27 讲 · 过程化资产生成

### 概念

4K Intro 不能存储任何预制作资产——纹理、模型、音乐全部用代码生成。**过程化资产生成**是 4K 的核心：用数学函数与算法生成所有视觉与音频内容。

本讲聚焦于"资产"——纹理、几何、动画的过程化生成，与第 3 章的过程化生成互补，更侧重 4K 场景下的实用技巧。

### 原理

**过程化纹理**：

```glsl
// 棋盘格纹理
vec3 checker(vec2 uv) {
    vec2 c = floor(uv * 8.0);
    return mod(c.x + c.y, 2.0) > 0.5 ? vec3(1) : vec3(0);
}

// 砖墙纹理
vec3 brick(vec2 uv) {
    vec2 c = floor(uv * vec2(4, 8));
    c.x += mod(c.y, 2.0);  // 错位
    vec2 f = fract(uv * vec2(4, 8));
    vec3 color = vec3(0.6, 0.3, 0.2);
    if (f.x < 0.05 || f.y < 0.05) color = vec3(0.2);  // 灰浆
    return color;
}

// 木材纹理
vec3 wood(vec2 uv) {
    float n = noise(uv * 4.0);
    float ring = sin(uv.x * 20 + n * 5);
    return mix(vec3(0.4, 0.2, 0.1), vec3(0.6, 0.4, 0.2), ring * 0.5 + 0.5);
}
```

**过程化几何**：

用 SDF（第 19 讲）生成所有几何：

```glsl
// 建筑物 SDF
float sd_building(vec3 p) {
    // 主体
    float box = sd_box(p, vec3(1, 2, 1));
    // 窗户（用重复）
    vec3 q = p;
    q.xz = mod(q.xz, 0.3) - 0.15;
    float windows = sd_box(q - vec3(0, 0, 0.95), vec3(0.1, 0.1, 0.05));
    // 减去窗户
    return max(box, -windows);
}

// 城市
float map(vec3 p) {
    // 重复建筑物
    vec3 q = mod(p, 4.0) - 2.0;
    return sd_building(q);
}
```

**过程化动画**：

```glsl
// 旋转
float t = iTime;
mat3 rot = mat3(cos(t),0,sin(t), 0,1,0, -sin(t),0,cos(t));
p = rot * p;

// 摆动
p.x += sin(iTime + p.y * 2) * 0.1;

// 脉动
scale = 1 + sin(iTime * 2) * 0.1;
```

**过程化天空**：

```glsl
vec3 sky(vec3 dir) {
    // 渐变
    float t = dir.y * 0.5 + 0.5;
    vec3 horizon = vec3(0.8, 0.7, 0.5);
    vec3 zenith = vec3(0.2, 0.4, 0.8);
    vec3 color = mix(horizon, zenith, t);
    
    // 太阳
    vec3 sun_dir = normalize(vec3(1, 0.5, 0));
    float sun = max(0, dot(dir, sun_dir));
    color += vec3(1, 0.8, 0.4) * pow(sun, 64);
    
    // 云
    float clouds = noise(dir.xz * 5 + iTime * 0.1);
    color = mix(color, vec3(1), clouds * 0.3 * t);
    
    return color;
}
```

**过程化音乐**：

```c
// 用合成器实时生成音乐（第 22-23 讲）
// 4K Intro 的音乐通常是：
// 1. 几个合成器音色（参数化）
// 2. 紧凑乐谱（每音符 1-2 字节）
// 3. 简单 DSP（延迟、混响）

// 紧凑乐谱示例
const uint8_t melody[] = {
    0x10, 0x20, 0x30, 0x20,  // 每字节：高4位音高，低4位时长
    0x40, 0x30, 0x20, 0x10,
    // ...
};
```

### 例子

```glsl
#version 330 core
// 过程化场景：建筑物 + 天空

uniform float iTime;
uniform vec2 iResolution;
out vec4 fragColor;

// 噪声
float hash(vec2 p) {
    return fract(sin(dot(p, vec2(127.1, 311.7))) * 43758.5453);
}
float noise(vec2 p) {
    vec2 i = floor(p), f = fract(p);
    f = f * f * (3 - 2 * f);
    return mix(mix(hash(i), hash(i + vec2(1,0)), f.x),
               mix(hash(i + vec2(0,1)), hash(i + vec2(1,1)), f.x), f.y);
}

// SDF
float sd_box(vec3 p, vec3 b) {
    vec3 q = abs(p) - b;
    return length(max(q, 0)) + min(max(q.x, max(q.y, q.z)), 0);
}

// 建筑物
float sd_building(vec3 p) {
    p.xz = mod(p.xz, 6.0) - 3.0;
    float h = 2 + noise(floor(p.xz / 6.0) * 0.5) * 4;
    return sd_box(p - vec3(0, h, 0), vec3(1, h, 1));
}

// 天空
vec3 sky(vec3 d) {
    float t = d.y * 0.5 + 0.5;
    vec3 c = mix(vec3(0.5, 0.6, 0.8), vec3(0.1, 0.2, 0.4), t);
    float sun = max(0, dot(d, normalize(vec3(1, 0.5, 0.3))));
    c += vec3(1, 0.7, 0.3) * pow(sun, 32);
    return c;
}

void main() {
    vec2 uv = (gl_FragCoord.xy - 0.5 * iResolution.xy) / iResolution.y;
    vec3 ro = vec3(sin(iTime * 0.1) * 10, 2, -10);
    vec3 rd = normalize(vec3(uv, 1));
    
    // 简单 Ray March
    float t = 0;
    for (int i = 0; i < 60; ++i) {
        vec3 p = ro + rd * t;
        float d = sd_building(p);
        if (d < 0.01) {
            // 法向量
            vec2 e = vec2(0.001, 0);
            vec3 n = normalize(vec3(
                sd_building(p + e.xyy) - sd_building(p - e.xyy),
                sd_building(p + e.yxy) - sd_building(p - e.yxy),
                sd_building(p + e.yyx) - sd_building(p - e.yyx)
            ));
            vec3 light = normalize(vec3(1, 1, 0.5));
            float diff = max(0, dot(n, light));
            vec3 color = vec3(0.7) * (0.2 + diff * 0.8);
            fragColor = vec4(color, 1);
            return;
        }
        t += d;
        if (t > 50) break;
    }
    
    fragColor = vec4(sky(rd), 1);
}
```

### 总结

- 4K Intro 全部资产用代码生成，无预制作。
- **过程化纹理**：棋盘格、砖墙、木材用数学函数生成。
- **过程化几何**：用 SDF 生成建筑物、城市。
- **过程化动画**：旋转、摆动、脉动用三角函数。
- **过程化天空**：渐变 + 太阳 + 云。
- **过程化音乐**：合成器 + 紧凑乐谱。
- **常见坑**：噪声函数选择不当导致图案重复；SDF 过于复杂导致渲染慢。

---

## 第 28 讲 · 尺寸优化技巧

### 概念

4K Intro 的每一字节都珍贵。本讲总结尺寸优化的实用技巧——从代码风格到编译选项，从数据编码到算法选择。这些技巧是 DemoScene 程序员多年智慧的结晶。

### 原理

**代码层面**：

1. **短变量名**：`a` 代替 `color`，`p` 代替 `position`。
2. **合并声明**：`float a,b,c;` 代替三行。
3. **复用变量**：一个变量多次使用。
4. **避免库函数**：自己实现 sin/cos 可能比链接 libm 小。
5. **用位运算**：`x*2` → `x<<1`，`x%8` → `x&7`。

**数据层面**：

1. **紧凑编码**：每音符 1 字节，每像素 16 位。
2. **查表共享**：多个效果共享同一查找表。
3. **过程化代替存储**：用 sin 函数代替正弦表。
4. **数据压缩**：RLE、LZ77 压缩静态数据。

**编译层面**：

1. **`/O1`**：最小化代码尺寸（MSVC）。
2. **`/GS-`**：去除安全检查。
3. **`/GL`**：全程序优化。
4. **`/NODEFAULTLIB`**：不链接默认库。
5. **`/ENTRY:main`**：指定入口，跳过 CRT 初始化。

**链接层面**：

1. **Crinkler**：4K 标配，压缩 PE 头。
2. **UPX**：通用可执行文件压缩。
3. **合并段**：把代码、数据合并到一个段。

**算法层面**：

1. **选择紧凑算法**：SDF 代替三角形网格。
2. **避免递归**：递归产生大量代码，用迭代。
3. **内联小函数**：减少调用开销（但可能增大代码）。
4. **共享代码**：多个效果共享同一渲染函数。

**着色器层面**：

1. **ShaderMinifier**：自动压缩。
2. **用内置函数**：`mix`、`smoothstep` 比手写短。
3. **复用变量**：`vec4` 存 4 个 float。
4. **swizzling**：`p.xyz`、`p.xy` 灵活访问。

**数学技巧**：

1. **用向量存标量**：`vec4(x, y, z, w)` 比 4 个 float 省空间。
2. **用矩阵存数据**：4×4 矩阵存 16 个值。
3. **复用常量**：`vec3(1)` 代替 `vec3(1,1,1)`。
4. **隐式类型转换**：`1` 自动转为 `1.0`。

### 例子

```c
// 优化前（200 字节）
float calculate_distance(vec3 position, vec3 center, float radius) {
    float dx = position.x - center.x;
    float dy = position.y - center.y;
    float dz = position.z - center.z;
    return sqrt(dx*dx + dy*dy + dz*dz) - radius;
}

// 优化后（50 字节）
float d(vec3 p,vec3 c,float r){return length(p-c)-r;}
```

```glsl
// 优化前
vec3 color = vec3(1.0, 1.0, 1.0);
float alpha = 1.0;
gl_FragColor = vec4(color, alpha);

// 优化后
gl_FragColor = vec4(1);
```

```c
// 数据压缩：RLE 编码
// 原始：AAAAABBBCCCDDE
// RLE：  A5B3C3D2E1

void rle_encode(const uint8_t* input, int len, uint8_t* output) {
    int out_pos = 0;
    int i = 0;
    while (i < len) {
        uint8_t count = 1;
        while (i + count < len && input[i] == input[i + count] && count < 255) {
            count++;
        }
        output[out_pos++] = count;
        output[out_pos++] = input[i];
        i += count;
    }
}
```

**编译命令对比**：

```bash
# 未优化（10KB）
cl demo.c /link /OUT:demo.exe

# 4K 优化（3.5KB）
cl /O1 /GS- /GL /c demo.c
crinkler demo.obj /ENTRY:main /NODEFAULTLIB /SUBSYSTEM:WINDOWS /OUT:demo.exe
```

### 总结

- 4K Intro 每字节珍贵，需全方位优化。
- **代码**：短变量名、合并声明、复用变量、位运算。
- **数据**：紧凑编码、查表共享、过程化代替存储。
- **编译**：`/O1`、`/GS-`、`/GL`、`/NODEFAULTLIB`。
- **链接**：Crinkler 压缩 PE 头。
- **着色器**：ShaderMinifier、内置函数、swizzling。
- **数学**：向量存标量、复用常量。
- **常见坑**：过度优化导致代码不可读；优化破坏正确性。

---

# 第 8 章 · 实战项目

前 7 章学习了 DemoScene 的各个组件，本章把它们组装成完整的 Demo 作品。从 Demo 设计与节奏开始，到场景切换与时间轴，再到完整 Demo 框架，最后讲解发布与竞赛。掌握这些，你就能独立创作一个完整的 Demo，甚至参加 Revision、Assembly 等 DemoScene 竞赛。

## 第 29 讲 · Demo 设计与节奏

### 概念

**Demo 设计** 是艺术与技术的结合——决定 Demo 讲什么故事、传达什么情感、如何组织内容。好的 Demo 不是技术的堆砌，而是有节奏、有情感、有叙事的视听作品。

**节奏（Pacing）** 是 Demo 的生命线——快慢交替、张弛有度，让观众的注意力始终保持。没有节奏的 Demo 即使技术再强也让人疲倦。

### 原理

**Demo 的结构**：

经典 Demo 通常有起承转合的结构：

```
1. 开场（0-15s）：建立氛围，吸引注意
2. 发展（15-60s）：展示主要效果，节奏渐快
3. 高潮（60-90s）：最强烈的效果，音乐达到峰值
4. 结尾（90-120s）：渐弱，字幕滚动
```

**节奏设计**：

```
强度
 │    ___           ___
 │   /   \___      /   \___
 │  /        \____/        \____
 │ /
 └──────────────────────────────→ 时间
   开场  发展  高潮  缓和  高潮  结尾
```

- **慢→快→慢**：开场慢建立氛围，中段快展示技术，结尾慢收束。
- **对比**：静与动、暗与亮、简与繁的对比产生张力。
- **重复与变化**：主题重复出现但每次变化，保持新鲜感。

**情感曲线**：

Demo 应传达情感旅程：

```
情感
 │         ★
 │       ╱   ╲
 │     ╱       ╲
 │   ╱           ╲___
 │ ╱
 └──────────────────→ 时间
   好奇  惊喜  震撼  平静
```

**音乐与画面的同步**：

Demo 的核心是音画同步——画面变化与音乐节拍对齐：

1. **节拍命中**：重要视觉变化在音乐重音时发生。
2. **情绪匹配**：画面氛围与音乐情绪一致。
3. **节奏跟随**：画面切换频率与音乐节奏匹配。

**Demo 设计文档**：

制作 Demo 前应写设计文档：

```
Demo 名称：Ephemera
时长：3 分 30 秒
主题：时间的流逝与记忆的消散

时间轴：
0:00-0:20  开场：黑暗中渐现的粒子，低沉的环境音
0:20-0:50  发展：粒子聚合成结构，节奏渐起
0:50-1:30  高潮 1：结构爆发为城市，强烈的电子乐
1:30-2:00  缓和：城市消散为粒子，音乐转柔
2:00-2:40  高潮 2：粒子重组为隧道，超光速飞行
2:40-3:30  结尾：隧道尽头的光，渐隐，字幕

色彩方案：
开场：黑+蓝
发展：蓝+紫
高潮 1：橙+红（热烈）
缓和：紫+蓝
高潮 2：白+蓝（冷峻）
结尾：白（纯净）
```

**技术选型**：

根据设计选择技术：

- 粒子效果 → GPU 着色器或 CPU 粒子系统
- 城市 → SDF + Ray Marching
- 隧道 → 极坐标映射
- 超光速 → 星空 + 拉线

### 例子

```c
// Demo 设计文档示例
struct DemoDesign {
    const char* name = "Ephemera";
    float duration = 210.0f;  // 3.5 分钟
    
    struct Scene {
        float start_time;
        float end_time;
        const char* name;
        const char* description;
    };
    
    Scene scenes[6] = {
        {0,    20,  "Intro",      "黑暗中渐现的粒子"},
        {20,   50,  "Build",      "粒子聚合成结构"},
        {50,   90,  "City",       "结构爆发为城市"},
        {90,   120, "Dissolve",   "城市消散为粒子"},
        {120,  160, "Tunnel",     "粒子重组为隧道"},
        {160,  210, "Finale",     "隧道尽头，渐隐"}
    };
    
    void print_timeline() {
        printf("Demo: %s (%.0fs)\n", name, duration);
        for (const auto& s : scenes) {
            printf("  %.0f-%.0fs: %s - %s\n",
                   s.start_time, s.end_time, s.name, s.description);
        }
    }
};
```

### 总结

- Demo 设计是艺术与技术结合，需有结构、节奏、情感。
- **结构**：开场 → 发展 → 高潮 → 结尾，起承转合。
- **节奏**：快慢交替、张弛有度、对比鲜明。
- **音画同步**：画面变化与音乐节拍对齐。
- **设计文档**：制作前写时间轴、色彩方案、技术选型。
- **常见坑**：技术堆砌无叙事；节奏单一无变化；音画不同步。

---

## 第 30 讲 · 场景切换与时间轴

### 概念

**场景切换（Scene Transition）** 在 Demo 的不同效果间平滑过渡。Demo 由多个场景组成，每个场景展示不同效果，场景间需流畅切换。

**时间轴（Timeline）** 管理 Demo 的整体流程——哪个时间点显示哪个场景，场景间如何过渡。时间轴是 Demo 的"导演"，控制整个作品的节奏。

### 原理

**场景（Scene）**：

```c
struct Scene {
    float start_time;
    float duration;
    virtual void update(float t) = 0;  // t 是场景内时间 [0, duration]
    virtual void render() = 0;
};
```

**时间轴**：

```c
class Timeline {
    std::vector<Scene*> scenes;
    float current_time = 0;
    
    void update(float dt) {
        current_time += dt;
        for (auto* s : scenes) {
            if (current_time >= s->start_time && 
                current_time < s->start_time + s->duration) {
                s->update(current_time - s->start_time);
            }
        }
    }
    
    void render() {
        for (auto* s : scenes) {
            if (current_time >= s->start_time && 
                current_time < s->start_time + s->duration) {
                s->render();
            }
        }
    }
};
```

**场景过渡**：

1. **硬切（Cut）**：瞬间切换，适合节奏强烈的时刻。
2. **淡入淡出（Fade）**：渐变过渡，适合柔和切换。
3. **融合（Blend）**：两场景交叉混合，平滑过渡。
4. **遮罩（Mask）**：用形状或图案过渡，有创意感。

**淡入淡出实现**：

```c
struct FadeTransition {
    float duration;
    
    float get_alpha(float t) {
        // 淡入（0-0.5）→ 淡出（0.5-1）
        if (t < 0.5) return t * 2;  // 0→1
        return (1 - t) * 2;          // 1→0
    }
};

// 渲染时
float fade = transition.get_alpha(t);
render_scene_a();
render_scene_b();
// 用 fade 混合两场景
```

**音画同步**：

```c
// 在音乐节拍点触发场景切换
struct Beat {
    float time;
    int scene_index;
};

Beat beats[] = {
    {20.0, 1},   // 20 秒切到场景 1
    {50.0, 2},   // 50 秒切到场景 2
    {90.0, 3},   // 90 秒切到场景 3
};

void update(float t) {
    for (const auto& b : beats) {
        if (t >= b.time && !triggered[b.scene_index]) {
            switch_scene(b.scene_index);
            triggered[b.scene_index] = true;
        }
    }
}
```

**节奏可视化**：

```c
// 根据音乐强度调整画面
float music_intensity = get_music_amplitude();
if (music_intensity > threshold) {
    // 强节拍：闪光、震动
    flash = 1.0;
    shake = music_intensity * 10;
}
flash *= 0.9;  // 衰减
```

### 例子

```c
#include <SDL2/SDL.h>
#include <cmath>
#include <vector>

// 场景基类
class Scene {
public:
    float start_time, duration;
    Scene(float s, float d) : start_time(s), duration(d) {}
    virtual void update(float t) {}
    virtual void render(SDL_Renderer* r) {}
    bool is_active(float current_time) {
        return current_time >= start_time && 
               current_time < start_time + duration;
    }
};

// 等离子场景
class PlasmaScene : public Scene {
public:
    PlasmaScene(float s, float d) : Scene(s, d) {}
    void render(SDL_Renderer* r) override {
        // 简化：用纯色填充
        SDL_SetRenderDrawColor(r, 100, 50, 150, 255);
        SDL_Rect full = {0, 0, 800, 600};
        SDL_RenderFillRect(r, &full);
    }
};

// 星空场景
class StarfieldScene : public Scene {
public:
    StarfieldScene(float s, float d) : Scene(s, d) {}
    void render(SDL_Renderer* r) override {
        SDL_SetRenderDrawColor(r, 0, 0, 0, 255);
        SDL_RenderClear(r);
        SDL_SetRenderDrawColor(r, 255, 255, 255, 255);
        for (int i = 0; i < 100; ++i) {
            int x = rand() % 800, y = rand() % 600;
            SDL_RenderDrawPoint(r, x, y);
        }
    }
};

// 时间轴
class Timeline {
public:
    std::vector<Scene*> scenes;
    float current_time = 0;
    
    void add_scene(Scene* s) { scenes.push_back(s); }
    
    void update(float dt) {
        current_time += dt;
        for (auto* s : scenes) {
            if (s->is_active(current_time)) {
                s->update(current_time - s->start_time);
            }
        }
    }
    
    void render(SDL_Renderer* r) {
        for (auto* s : scenes) {
            if (s->is_active(current_time)) {
                s->render(r);
            }
        }
    }
};

int main(int argc, char* argv[]) {
    SDL_Init(SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Demo Timeline",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 800, 600, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, 0);

    Timeline timeline;
    timeline.add_scene(new PlasmaScene(0, 5));     // 0-5s
    timeline.add_scene(new StarfieldScene(5, 5));  // 5-10s
    timeline.add_scene(new PlasmaScene(10, 5));    // 10-15s

    Uint32 last_time = SDL_GetTicks();
    bool running = true;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) if (e.type == SDL_QUIT) running = false;

        Uint32 now = SDL_GetTicks();
        float dt = (now - last_time) / 1000.0f;
        last_time = now;

        timeline.update(dt);
        timeline.render(renderer);

        SDL_RenderPresent(renderer);
    }

    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

### 总结

- 场景切换在 Demo 不同效果间过渡，时间轴管理整体流程。
- **场景**是 Demo 的基本单元，有开始时间、持续时间、更新与渲染方法。
- **过渡方式**：硬切、淡入淡出、融合、遮罩。
- **音画同步**：在音乐节拍点触发场景切换。
- **节奏可视化**：根据音乐强度调整画面效果。
- **常见坑**：场景切换突兀；时间轴逻辑错误；音画不同步。

---

## 第 31 讲 · 完整 Demo 框架

### 概念

**Demo 框架** 整合所有组件——图形、音频、时间轴、输入——成完整系统。好的框架模块化、可扩展，让创作者专注于内容而非基础设施。

本讲展示一个完整的 Demo 框架，整合前 30 讲的所有技术。

### 原理

**框架架构**：

```
Demo
├── Core
│   ├── Window（窗口管理）
│   ├── Renderer（渲染器）
│   ├── Audio（音频系统）
│   └── Input（输入处理）
├── Timeline
│   ├── Scene 1
│   ├── Scene 2
│   └── ...
├── Effects
│   ├── Plasma
│   ├── Fire
│   ├── Tunnel
│   └── Starfield
└── Music
    ├── Synth
    ├── Tracker
    └── DSP
```

**主循环**：

```c
while (running) {
    float dt = get_frame_time();
    
    input.update();
    timeline.update(dt);
    music.update(dt);
    
    renderer.clear();
    timeline.render(renderer);
    renderer.present();
}
```

**资源管理**：

```c
class ResourceManager {
    std::map<string, Shader*> shaders;
    std::map<string, Texture*> textures;
    
    Shader* load_shader(const string& name) {
        if (shaders.count(name)) return shaders[name];
        shaders[name] = new Shader(name);
        return shaders[name];
    }
};
```

**配置系统**：

```c
struct Config {
    int width = 800, height = 600;
    bool fullscreen = false;
    bool vsync = true;
    float master_volume = 0.8;
};
```

### 例子

```c
#include <SDL2/SDL.h>
#include <cmath>
#include <vector>

// ============ 核心系统 ============

class Window {
public:
    SDL_Window* window;
    Window(int w, int h) {
        SDL_Init(SDL_INIT_VIDEO | SDL_INIT_AUDIO);
        window = SDL_CreateWindow("Demo",
            SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, w, h, 0);
    }
    ~Window() {
        SDL_DestroyWindow(window);
        SDL_Quit();
    }
};

class Renderer {
public:
    SDL_Renderer* renderer;
    SDL_Texture* framebuffer;
    int width, height;
    
    Renderer(SDL_Window* w, int sw, int sh) : width(sw), height(sh) {
        renderer = SDL_CreateRenderer(w, -1, SDL_RENDERER_ACCELERATED | SDL_RENDERER_PRESENTVSYNC);
        framebuffer = SDL_CreateTexture(renderer,
            SDL_PIXELFORMAT_RGBA8888, SDL_TEXTUREACCESS_STREAMING, sw, sh);
    }
    
    Uint32* lock() {
        void* pixels;
        int pitch;
        SDL_LockTexture(framebuffer, nullptr, &pixels, &pitch);
        return (Uint32*)pixels;
    }
    
    void unlock() {
        SDL_UnlockTexture(framebuffer);
    }
    
    void present() {
        SDL_RenderClear(renderer);
        SDL_RenderCopy(renderer, framebuffer, nullptr, nullptr);
        SDL_RenderPresent(renderer);
    }
};

// ============ 效果 ============

class Effect {
public:
    virtual void update(float t) {}
    virtual void render(Uint32* fb, int w, int h) {}
};

class PlasmaEffect : public Effect {
    float time;
public:
    void update(float t) override { time = t; }
    void render(Uint32* fb, int w, int h) override {
        for (int y = 0; y < h; ++y) {
            for (int x = 0; x < w; ++x) {
                float v = sin(x * 0.05 + time) + sin(y * 0.05 + time * 1.3) +
                          sin((x + y) * 0.04 + time * 0.7);
                v = v / 3 * 0.5 + 0.5;
                Uint8 c = (Uint8)(v * 255);
                fb[y * w + x] = 0xFF000000 | (c << 16) | (c << 8) | c;
            }
        }
    }
};

class StarfieldEffect : public Effect {
    struct Star { float x, y, z; };
    std::vector<Star> stars;
    float time;
public:
    StarfieldEffect() {
        for (int i = 0; i < 200; ++i) {
            stars.push_back({
                (float)(rand() % 2000 - 1000) / 1000,
                (float)(rand() % 2000 - 1000) / 1000,
                (float)(rand() % 1000 + 1) / 1000
            });
        }
    }
    void update(float t) override { time = t; }
    void render(Uint32* fb, int w, int h) override {
        for (int i = 0; i < w * h; ++i) fb[i] = 0xFF000000;
        for (auto& s : stars) {
            s.z -= 0.005;
            if (s.z <= 0.01) {
                s.x = (float)(rand() % 2000 - 1000) / 1000;
                s.y = (float)(rand() % 2000 - 1000) / 1000;
                s.z = 1;
            }
            int sx = (int)(s.x / s.z * 200 + w / 2);
            int sy = (int)(s.y / s.z * 200 + h / 2);
            if (sx >= 0 && sx < w && sy >= 0 && sy < h) {
                Uint8 c = (Uint8)((1 - s.z) * 255);
                fb[sy * w + sx] = 0xFF000000 | (c << 16) | (c << 8) | c;
            }
        }
    }
};

// ============ 时间轴 ============

class Timeline {
public:
    struct Entry {
        Effect* effect;
        float start, duration;
    };
    std::vector<Entry> entries;
    float time = 0;
    
    void add(Effect* e, float s, float d) {
        entries.push_back({e, s, d});
    }
    
    void update(float dt) {
        time += dt;
        for (auto& e : entries) {
            if (time >= e.start && time < e.start + e.duration) {
                e.effect->update(time - e.start);
            }
        }
    }
    
    void render(Uint32* fb, int w, int h) {
        for (auto& e : entries) {
            if (time >= e.start && time < e.start + e.duration) {
                e.effect->render(fb, w, h);
                return;  // 只渲染当前场景
            }
        }
    }
};

// ============ 主程序 ============

int main(int argc, char* argv[]) {
    Window window(800, 600);
    Renderer renderer(window.window, 320, 240);
    
    Timeline timeline;
    PlasmaEffect plasma;
    StarfieldEffect stars;
    timeline.add(&plasma, 0, 5);
    timeline.add(&stars, 5, 5);
    timeline.add(&plasma, 10, 5);
    
    Uint32 last = SDL_GetTicks();
    bool running = true;
    while (running) {
        SDL_Event e;
        while (SDL_PollEvent(&e)) if (e.type == SDL_QUIT) running = false;
        
        Uint32 now = SDL_GetTicks();
        float dt = (now - last) / 1000.0f;
        last = now;
        
        timeline.update(dt);
        
        Uint32* fb = renderer.lock();
        timeline.render(fb, 320, 240);
        renderer.unlock();
        renderer.present();
    }
    
    return 0;
}
```

### 总结

- Demo 框架整合图形、音频、时间轴、输入成完整系统。
- **架构**：Core（窗口/渲染/音频/输入）+ Timeline + Effects + Music。
- **主循环**：input → update → render → present。
- **资源管理**：集中管理着色器、纹理，避免重复加载。
- **模块化**：每个效果独立，便于添加与替换。
- **常见坑**：资源泄漏；时间轴逻辑错误；效果间状态冲突。

---

## 第 32 讲 · 发布与竞赛

### 概念

**发布（Release）** 是 Demo 的最终环节——把作品打包、测试、分发给观众。DemoScene 有严格的发布规范，违反可能导致竞赛资格取消。

**竞赛（Competition）** 是 DemoScene 的核心活动——在 Revision、Assembly、Evoke 等 Party 上，各 Group 提交作品，观众与评委投票评选。竞赛是 DemoScene 社区的"节日"，也是创作者展示成果的舞台。

### 原理

**发布前的检查清单**：

1. **尺寸限制**：4K ≤ 4096 字节，64K ≤ 65536 字节。
2. **依赖检查**：不依赖外部 DLL 或资源（除系统库）。
3. **多硬件测试**：不同 GPU、CPU、分辨率测试。
4. **崩溃测试**：长时间运行不崩溃。
5. **音画同步**：不同帧率下音画同步正确。
6. **退出功能**：ESC 键能正常退出。

**打包**：

```
demo.exe        # 可执行文件
readme.txt      # 说明（Group、创作者、工具）
file_id.diz     # 标准信息文件
```

**file_id.diz** 是 DemoScene 标准的元数据文件：

```
@Ephemera
#a demo by MyGroup
#at Revision 2025
@coded by Coder
@music by Musician
@art by Artist
@4k intro
@released on 2025-04-20
```

**竞赛类别**：

| 类别 | 尺寸限制 | 典型时长 |
|------|---------|---------|
| 4K Intro | ≤ 4096 字节 | 3-5 分钟 |
| 64K Intro | ≤ 65536 字节 | 4-8 分钟 |
| Demo | 无限制 | 5-10 分钟 |
| Wild | 任意 | 任意 |
| Music | 无 | 2-5 分钟 |
| Graphics | 无 | 静态 |

**主要 Party**：

- **Revision**（德国）：现代最大的 DemoScene Party。
- **Assembly**（芬兰）：历史最悠久的 Party 之一。
- **Evoke**（德国）：夏季重要 Party。
- **Outline**（荷兰）：友好氛围的 Party。
- **Tokyo Demo Fest**（日本）：亚洲主要 Party。

**竞赛流程**：

1. **报名**：在 Party 网站注册作品。
2. **提交**：上传可执行文件与元数据。
3. **预审**：组织者检查尺寸、合规性。
4. **放映**：在大屏幕播放所有作品。
5. **投票**：观众与评委投票。
6. **颁奖**：公布结果，颁发奖项。

**获奖策略**：

1. **技术新颖**：用新算法或新效果吸引眼球。
2. **艺术风格**：独特的视觉风格让人记住。
3. **音画同步**：精准的同步是基本功。
4. **情感共鸣**：传达情感的作品更受欢迎。
5. **完成度**：粗糙的作品难获奖，需打磨细节。

**社区分享**：

- **Pouet.net**：DemoScene 作品数据库，发布后上传。
- **Demozoo.org**：另一个作品数据库。
- **Shadertoy.com**：着色器分享平台。
- **YouTube**：录制视频分享。

### 例子

```c
// 发布前的自动检查
void pre_release_check() {
    // 1. 检查可执行文件尺寸
    FILE* f = fopen("demo.exe", "rb");
    fseek(f, 0, SEEK_END);
    long size = ftell(f);
    fclose(f);
    
    if (size > 4096) {
        printf("ERROR: 4K limit exceeded! Size: %ld bytes\n", size);
        return;
    }
    printf("OK: Size %ld bytes (limit 4096)\n", size);
    
    // 2. 检查依赖
    printf("Checking dependencies...\n");
    // 用工具检查 PE 导入表
    
    // 3. 性能测试
    printf("Performance test...\n");
    // 运行 60 秒，检查帧率
    
    // 4. 退出测试
    printf("Exit test (ESC to quit)...\n");
    // 模拟 ESC 键
}

// 生成 file_id.diz
void generate_file_id(const char* name, const char* group, 
                      const char* party, const char* category) {
    FILE* f = fopen("file_id.diz", "w");
    fprintf(f, "@%s\n", name);
    fprintf(f, "#a demo by %s\n", group);
    fprintf(f, "#at %s\n", party);
    fprintf(f, "@%s\n", category);
    fprintf(f, "@released on %s\n", __DATE__);
    fclose(f);
}

int main(int argc, char* argv[]) {
    if (argc > 1 && strcmp(argv[1], "--check") == 0) {
        pre_release_check();
        return 0;
    }
    if (argc > 1 && strcmp(argv[1], "--metadata") == 0) {
        generate_file_id("Ephemera", "MyGroup", "Revision 2025", "4k intro");
        return 0;
    }
    
    // 正常运行 Demo
    // ...
}
```

### 总结

- 发布前需严格检查：尺寸、依赖、多硬件、崩溃、音画同步、退出。
- **file_id.diz** 是 DemoScene 标准元数据文件。
- **竞赛类别**：4K、64K、Demo、Wild、Music、Graphics。
- **主要 Party**：Revision、Assembly、Evoke、Outline、Tokyo Demo Fest。
- **获奖策略**：技术新颖、艺术风格、音画同步、情感共鸣、完成度。
- **社区分享**：Pouet、Demozoo、Shadertoy、YouTube。
- **常见坑**：尺寸超限；依赖缺失；多硬件不兼容；无退出功能。

---

## 课程结语

恭喜你完成了《DemoScene》的全部 32 讲！让我们回顾这段旅程：

**第 1 章 历史与文化**：追溯 DemoScene 从 1980 年代盗版场景到现代数字艺术的演变，理解其文化精神与艺术价值。

**第 2 章 图形基础**：掌握帧缓冲与像素操作、调色板与颜色循环、VGA Mode 13h 与老硬件技巧、固定时间步长与垂直同步。

**第 3 章 过程化生成**：学习随机数与噪声、Value Noise 与 Perlin Noise、L-System 与分形树、元球与隐式曲面。

**第 4 章 经典效果**：实现等离子、火焰、隧道、星空四大经典效果，用极简数学创造惊艳视觉。

**第 5 章 着色器与 GPU**：掌握 GLSL 片段着色器、Ray Marching、SDF 距离场渲染、后处理与色彩分级。

**第 6 章 音频合成**：学习波形合成、合成器与 ADSR 包络、跟踪器与模块音乐、DSP 效果与混音。

**第 7 章 代码压缩**：掌握 4K Intro 工具链、Shader 压缩、过程化资产生成、尺寸优化技巧。

**第 8 章 实战项目**：学习 Demo 设计与节奏、场景切换与时间轴、完整 Demo 框架、发布与竞赛。

**下一步学习建议**：

1. **ShaderToy**：在 ShaderToy 发布着色器，与全球创作者交流。
2. **参加 Party**：报名 Revision、Assembly 等 Party，现场感受 DemoScene 文化。
3. **研究源码**：下载经典 Demo 的源码（如 Farbrausch 的工具），学习大师技巧。
4. **Inigo Quilez**：跟随 IQ 的教程与文章，深入 SDF 与 Ray Marching。
5. **实践项目**：从 4K Intro 开始，逐步挑战 64K、完整 Demo。

DemoScene 是代码与艺术的完美结合——它让程序员成为艺术家，让数学产生美。希望这份教程能激发你的创造力，在 DemoScene 的世界里找到属于自己的表达。See you at Revision!
