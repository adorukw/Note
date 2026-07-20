# Tiled 地图编辑器实战教程

> 一本从零开始、循序渐进的教科书，带你掌握2D游戏开发中最强大的开源地图编辑器——Tiled。

---

## 课程总览

### 学习目标

完成本课程后，你将能够：

1. **熟练使用Tiled界面**：掌握所有工具栏、面板、快捷键，高效编辑地图
2. **管理瓦片集与图层**：导入瓦片图集、配置动画瓦片、组织多图层结构
3. **绘制专业地图**：使用基础工具、地形系统、王冠刷快速绘制自然过渡的地图
4. **运用对象层**：放置碰撞体、触发器、出生点、NPC等游戏逻辑对象
5. **自定义属性系统**：为瓦片、对象、图层添加自定义属性，实现数据驱动设计
6. **导出与集成**：理解TMX/TSX/JSON格式，将地图集成到SDL2、Unity、Godot等引擎
7. **自动化与实战**：使用命令行批处理，完成一个完整的游戏地图项目

### 课程定位

- **前置知识**：基本的2D游戏概念（瓦片、精灵、坐标系），无需编程基础（但集成章节需要）
- **难度曲线**：基础（1-6讲）→ 核心（7-15讲）→ 进阶（16-18讲）→ 实战（19-20讲）
- **预计学时**：每讲30-60分钟，总计约15-20小时
- **配套软件**：Tiled 1.9+（免费开源，支持Windows/macOS/Linux）

### 详细章节目录

#### 第一章 入门基础（第1-3讲）
- 第1讲：Tiled概述与环境搭建
- 第2讲：界面布局与基本操作
- 第3讲：创建第一个地图

#### 第二章 瓦片集管理（第4-6讲）
- 第4讲：瓦片集基础与导入
- 第5讲：瓦片属性与动画
- 第6讲：外部瓦片集与共享

#### 第三章 图层系统（第7-9讲）
- 第7讲：瓦片图层与绘制
- 第8讲：对象层与几何对象
- 第9讲：图层组织与可见性

#### 第四章 绘制工具与技巧（第10-12讲）
- 第10讲：基础绘制工具
- 第11讲：地形系统（Terrains）
- 第12讲：王冠刷（Wang Tiles）

#### 第五章 对象与属性（第13-15讲）
- 第13讲：对象类型与编辑
- 第14讲：自定义属性系统
- 第15讲：对象模板与复用

#### 第六章 导出与集成（第16-18讲）
- 第16讲：TMX/TSX文件格式解析
- 第17讲：导出JSON与CSV
- 第18讲：游戏引擎集成实战

#### 第七章 高级与实战（第19-20讲）
- 第19讲：命令行与自动化
- 第20讲：完整游戏地图实战

---

## 第一章 入门基础

> 万丈高楼平地起。本章带你认识Tiled的本质，搭建工作环境，并完成第一张能保存的地图。这是后续所有内容的地基。

### 第1讲 Tiled概述与环境搭建

#### 概念

**Tiled** 是一款免费、开源的2D地图编辑器，由Thorbjørn Lindeijer于2008年发起，现已成为2D游戏开发的事实标准工具。它支持正交（Orthogonal）、等距（Isometric）、六边形（Hexagonal）等多种地图类型，被《星露谷物语》《死亡细胞》《Hollow Knight》等知名游戏使用。Tiled本身不渲染游戏，它的产物是**地图数据文件**（.tmx），由游戏引擎读取并渲染。

#### 原理

**为什么需要专门的地图编辑器？**

如果用代码硬编码地图（如`int map[100][100] = {...}`），修改极其痛苦——调整一棵树的位置要重新编译。地图编辑器把"地图数据"和"游戏代码"解耦：

```
美术/策划：在Tiled中可视化编辑地图 → 保存为.tmx文件
游戏程序：读取.tmx文件 → 渲染 + 碰撞检测
```

这种**数据驱动**设计让非程序员也能参与地图制作，大幅提升开发效率。

**Tiled的核心数据模型**：

```
Map（地图）
├── Tileset（瓦片集）—— 引用图集图片
├── Layer（图层）
│   ├── Tile Layer（瓦片图层）—— 存瓦片ID网格
│   ├── Object Layer（对象层）—— 存几何对象
│   └── Image Layer（图片层）—— 存背景图
└── Properties（自定义属性）—— 任意键值对
```

**支持的地图类型**：

| 类型 | 说明 | 典型游戏 |
|------|------|----------|
| Orthogonal | 正交（方格） | 宝可梦、塞尔达 |
| Isometric | 等距（45度斜视） | 模拟城市、暗黑破坏神 |
| Isometric (Staggered) | 交错等距 | 文明、辐射 |
| Hexagonal | 六边形 | 文明5、英雄无敌 |

#### 例子

**安装Tiled**：

1. **官方下载**：访问 https://www.mapeditor.org/ ，下载对应平台安装包
2. **Linux**：`sudo apt install tiled`（Ubuntu/Debian）或 `sudo pacman -S tiled`（Arch）
3. **macOS**：`brew install --cask tiled`
4. **源码编译**：`git clone https://github.com/mapeditor/tiled.git`，用Qt编译

**验证安装**：

```bash
tiled --version
# 输出类似：Tiled 1.9.2
```

**第一个Tiled项目目录结构建议**：

```
my_game/
├── maps/
│   ├── overworld.tmx        # 地图文件
│   ├── dungeon.tmx
│   └── village.tmx
├── tilesets/
│   ├── overworld_tiles.png  # 瓦片图集
│   ├── overworld_tiles.tsx  # 瓦片集定义
│   └── dungeon_tiles.tsx
├── objects/
│   └── objects.tsx          # 对象瓦片集
└── game/
    └── main.py              # 游戏代码
```

**关键约定**：地图文件（.tmx）和瓦片集文件（.tsx）使用**相对路径**引用图片，这样整个项目文件夹移动后仍能正常工作。

#### 总结

- **Tiled本质**：2D地图数据编辑器，产物是.tmx文件，不负责游戏渲染
- **数据驱动**：地图数据与游戏代码分离，非程序员也能编辑地图
- **核心模型**：Map → Tileset + Layers + Properties
- **四种地图类型**：正交、等距、交错等距、六边形
- **路径约定**：始终用相对路径引用资源，保证项目可移植
- **常见坑**：把图片放在项目外用绝对路径引用，导致换机器打不开；用旧版Tiled打开新版文件出现兼容问题

---

### 第2讲 界面布局与基本操作

#### 概念

Tiled的界面由**菜单栏、工具栏、图层面板、瓦片集面板、属性面板、状态栏**六大区域组成。理解每个区域的功能是高效编辑的前提。本讲带你熟悉界面布局，掌握核心快捷键。

#### 原理

**Tiled界面分区**：

```
┌─────────────────────────────────────────────┐
│  菜单栏（File / Edit / View / Map / ...）    │
├─────────────────────────────────────────────┤
│  工具栏（绘制工具、缩放、图层操作）           │
├──────────┬──────────────────────┬───────────┤
│          │                      │           │
│  瓦片集   │      地图编辑区       │  图层面板  │
│  面板     │   （主视图）          │  属性面板  │
│          │                      │           │
│          │                      │           │
├──────────┴──────────────────────┴───────────┤
│  状态栏（坐标、缩放、当前工具提示）           │
└─────────────────────────────────────────────┘
```

**各区域职责**：

| 区域 | 功能 |
|------|------|
| 菜单栏 | 文件操作、编辑、视图、地图配置、图层、瓦片集、工具、帮助 |
| 工具栏 | 绘制工具（印章、矩形、填充等）、缩放、撤销重做 |
| 瓦片集面板 | 显示当前瓦片集，选择要绘制的瓦片 |
| 地图编辑区 | 主视图，在此绘制地图 |
| 图层面板 | 管理图层顺序、可见性、锁定 |
| 属性面板 | 编辑选中对象（瓦片、对象、图层）的属性 |
| 状态栏 | 显示鼠标坐标、缩放比例、工具提示 |

**坐标系**：

Tiled使用**屏幕坐标系**——原点在左上角，x向右增大，y向下增大。这与SDL2、Unity 2D一致，但与数学坐标系（y向上）相反。

**瓦片坐标 vs 像素坐标**：
- 瓦片坐标：(5, 3) 表示第5列第3行的瓦片
- 像素坐标：(160, 96) = 瓦片坐标 × 瓦片尺寸（32×32时）

#### 例子

**核心快捷键表**：

| 快捷键 | 功能 |
|--------|------|
| `B` | 印章工具（Stamp Brush） |
| `F` | 填充工具（Bucket Fill） |
| `R` | 矩形工具（Rectangle） |
| `L` | 直线工具（Line） |
| `E` | 橡皮擦（Eraser） |
| `O` | 选择对象（Select Objects） |
| `M` | 编辑对象（Edit Objects） |
| `Ctrl+Z` | 撤销 |
| `Ctrl+Y` | 重做 |
| `Ctrl+S` | 保存 |
| `Ctrl+C / V` | 复制/粘贴 |
| `+ / -` | 放大/缩小 |
| `0` | 100%缩放 |
| `Space+拖动` | 平移视图 |
| `Tab` | 隐藏/显示面板（全屏编辑） |

**首次启动配置建议**：

1. **Edit → Preferences → Interface**：
   - 主题：选择深色或浅色
   - 语言：可切换中文

2. **Edit → Preferences → Tilesets**：
   - 勾选 "Use ambient color for tiles"（让瓦片有环境光）

3. **View → Show Grid**：始终显示网格，便于对齐

4. **View → Snap to Grid**：开启网格吸附，绘制对象时自动对齐

**自定义工具栏**：右键工具栏 → Configure Toolbars，可添加/移除按钮。

#### 总结

- **六大区域**：菜单栏、工具栏、瓦片集面板、编辑区、图层面板、属性面板
- **坐标系**：屏幕坐标，原点左上角，y向下
- **快捷键**：B/F/R/L/E是五大绘制工具，必须熟记
- **Space+拖动**：平移视图，比滚动条快
- **Tab键**：全屏编辑，专注绘制
- **常见坑**：忘记开网格吸附导致对象位置乱；用滚动条平移效率低，应改用Space

---

### 第3讲 创建第一个地图

#### 概念

本讲从零创建一张完整的正交地图：新建地图、配置参数、添加瓦片集、绘制瓦片、保存文件。这是Tiled最基础的工作流，掌握后即可开始制作简单地图。

#### 原理

**新建地图的参数**：

1. **Orientation（方向）**：正交/等距/六边形
2. **Tile layer format（瓦片层格式）**：
   - CSV：可读文本，文件大，加载慢
   - Base64 (uncompressed)：二进制，文件小
   - Base64 (zlib/gzip/zstd compressed)：压缩二进制，文件最小，推荐
3. **Tile render order（渲染顺序）**：右下/右上/左下/左上，影响瓦片叠加
4. **Map size（地图尺寸）**：以瓦片为单位
5. **Tile size（瓦片尺寸）**：单个瓦片的像素尺寸，必须与瓦片图集匹配

**瓦片尺寸选择**：
- 16×16：复古风（FC/GBA风格），细节少但精致
- 32×32：主流2D游戏，平衡细节与工作量
- 48×48：RPG Maker风格，细节丰富
- 64×64：高清2D，适合现代美术

**地图尺寸建议**：
- 单屏地图：20×15（640×480 @ 32px）
- 房间地图：25×19（800×608 @ 32px）
- 大地图：100×100以上，需配合摄像机滚动

**工作流**：

```
新建地图 → 添加瓦片集 → 创建图层 → 绘制瓦片 → 保存.tmx
```

#### 例子

**步骤1：新建地图**

`File → New → New Map`，配置：
- Orientation: Orthogonal
- Tile size: 32×32
- Map size: 30×20（960×640像素）
- Tile layer format: Base64 (zlib compressed)

**步骤2：添加瓦片集**

`Map → New Tileset`，配置：
- Name: overworld
- Image: 选择 `tilesets/overworld_tiles.png`
- Tile size: 32×32（与地图一致）
- Spacing/Margin: 0（如果图集无间距）
- 勾选 "Embed in map"：**不勾选**（推荐保存为外部.tsx，便于复用）

**步骤3：创建图层**

在图层面板右键 → Add Layer → Tile Layer，命名：
- `Ground`（地面层：草地、地板）
- `Decor`（装饰层：花、小石头）
- `Overhead`（高层层：树冠、屋顶）

**步骤4：绘制瓦片**

1. 选中Ground图层
2. 在瓦片集面板点击草地瓦片
3. 用填充工具（F）点击地图，填满草地
4. 切换到印章工具（B），绘制路径
5. 选中Overhead图层，绘制树木

**步骤5：保存**

`File → Save As` → `maps/overworld.tmx`

**生成的.tmx文件结构（简化）**：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<map version="1.9" tiledversion="1.9.2"
     orientation="orthogonal" renderorder="right-down"
     width="30" height="20" tilewidth="32" tileheight="32"
     infinite="0" nextlayerid="4" nextobjectid="1">
  <tileset firstgid="1" source="overworld.tsx"/>
  <layer id="1" name="Ground" width="30" height="20">
    <data encoding="base64" compression="zlib">
      eJztwTEBAAAAwqD1T20JT6AAAHgaCAAAAg==
    </data>
  </layer>
  <layer id="2" name="Decor" width="30" height="20">
    <data encoding="base64" compression="zlib">...</data>
  </layer>
  <layer id="3" name="Overhead" width="30" height="20">
    <data encoding="base64" compression="zlib">...</data>
  </layer>
</map>
```

**关键参数解读**：
- `firstgid="1"`：该瓦片集在全局瓦片ID中的起始编号
- `encoding="base64"`：瓦片数据用Base64编码
- `compression="zlib"`：再用zlib压缩，文件最小

#### 总结

- **新建地图五参数**：方向、格式、渲染顺序、地图尺寸、瓦片尺寸
- **瓦片尺寸**：16/32/48/64是主流，必须与图集匹配
- **图层规划**：地面→装饰→高层，从底到顶
- **外部瓦片集**：不勾选"Embed in map"，保存为.tsx便于复用
- **压缩格式**：Base64+zlib文件最小，推荐发布版用
- **常见坑**：瓦片尺寸与图集不匹配导致显示错乱；地图尺寸设太小后期无法扩展（可勾选infinite无限地图）

---

## 第二章 瓦片集管理

> 瓦片集（Tileset）是地图的"颜料"。本章学习瓦片集的导入、配置、动画、共享，让你能高效管理游戏的所有瓦片资源。

### 第4讲 瓦片集基础与导入

#### 概念

**瓦片集（Tileset）** 是Tiled中对瓦片图集（Tile Sheet，一张包含多个小瓦片的大图）的抽象。它不仅记录图片路径，还记录每个瓦片的尺寸、间距、边距、属性等元数据。一个瓦片集可以服务于多张地图，实现资源复用。

#### 原理

**瓦片图集的物理结构**：

```
overworld_tiles.png (256×256像素，32×32瓦片，8×8网格)

  margin
  ↓
  ┌──┬─────────────────────────────────┐
  │  │  T0  T1  T2  T3  T4  T5  T6  T7│
  │  ├─────────────────────────────────┤
  │  │  T8  T9  T10 T11 T12 T13 T14 T15│
spacing→│  ...                            │
  │  │                                 │
  └──┴─────────────────────────────────┘
```

- **Tile size**：单个瓦片的像素尺寸（如32×32）
- **Margin**：图集边缘留白（通常0）
- **Spacing**：瓦片之间的间距（通常0，但有些图集有1像素分隔线防渗色）

**全局瓦片ID（Global Tile ID, GID）**：

Tiled为每张地图维护一个全局瓦片ID空间。第一个瓦片集的firstgid=1，第二个瓦片集的firstgid=第一个瓦片集瓦片数+1。这样地图数据中只需存一个GID，就能定位到具体瓦片集和瓦片。

```
瓦片集A: firstgid=1, 64个瓦片 → GID 1~64
瓦片集B: firstgid=65, 32个瓦片 → GID 65~96
瓦片集C: firstgid=97, 16个瓦片 → GID 97~112

地图数据中 GID=70 → 瓦片集B的第6个瓦片（70-65+1=6）
```

**本地瓦片ID（Local Tile ID, LID）**：

瓦片集内部从0开始的编号。GID = firstgid + LID。

**瓦片集类型**：

1. **基于图集的瓦片集（Tileset based on tileset image）**：传统方式，一张大图切分
2. **基于集合的瓦片集（Collection of images）**：每个瓦片是独立图片，适合大小不一的对象

#### 例子

**导入图集瓦片集**：

`Map → New Tileset`：

```
Name: overworld
Type: Based on Tileset Image
Image: tilesets/overworld_tiles.png
Tile size: 32 × 32
Margin: 0
Spacing: 0
Transparent color: (可选，通常用PNG透明通道)
[ ] Embed in map    ← 不勾选，保存为外部.tsx
```

**导入集合瓦片集**（适合RPG Maker风格的独立角色图）：

`Map → New Tileset`：

```
Name: characters
Type: Collection of Images
[ ] Embed in map
```

然后点击 "Add Tiles" 按钮，逐个添加PNG图片。每个瓦片可以有不同尺寸。

**生成的.tsx文件**：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<tileset version="1.9" tiledversion="1.9.2"
         name="overworld" tilewidth="32" tileheight="32"
         tilecount="64" columns="8">
  <image source="../tilesets/overworld_tiles.png"
         width="256" height="256"/>
</tileset>
```

**集合瓦片集的.tsx**：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<tileset version="1.9" tiledversion="1.9.2"
         name="characters" tilewidth="48" tileheight="48"
         tilecount="3" columns="0">
  <tile id="0">
    <image source="../objects/hero.png" width="48" height="48"/>
  </tile>
  <tile id="1">
    <image source="../objects/npc1.png" width="48" height="48"/>
  </tile>
  <tile id="2">
    <image source="../objects/npc2.png" width="48" height="48"/>
  </tile>
</tileset>
```

**修改瓦片集属性**：双击瓦片集面板中的瓦片集名，或右键 → Tileset Properties，可修改尺寸、间距等。

#### 总结

- **瓦片集**：图集+元数据，可被多张地图复用
- **GID vs LID**：GID是全局唯一，LID是瓦片集内本地编号，GID = firstgid + LID
- **两种类型**：基于图集（统一尺寸）、基于集合（独立图片，尺寸可不同）
- **外部.tsx**：不勾选Embed，保存为独立文件，多地图共享
- **Margin/Spacing**：图集有防渗色分隔线时需设置，通常为0
- **常见坑**：图集尺寸不是瓦片尺寸整数倍导致最后一个瓦片被裁切；修改图集后忘记在Tiled中刷新（View → Reload）

---

### 第5讲 瓦片属性与动画

#### 概念

每个瓦片除了图形，还可以有**自定义属性**（如是否可通行、伤害值）和**动画**（多帧切换，如水面波纹、火焰）。本讲学习如何为瓦片添加属性和动画，实现数据驱动的游戏逻辑。

#### 原理

**瓦片属性的作用**：

游戏读取地图时，不仅需要知道"这里画了什么瓦片"，还需要知道"这个瓦片是否可通行"、"是否造成伤害"。这些信息存在瓦片属性中，避免在游戏代码里硬编码瓦片ID判断。

**常见瓦片属性**：

| 属性名 | 类型 | 说明 |
|--------|------|------|
| solid | bool | 是否实心（不可通行） |
| damage | int | 踩上去的伤害值 |
| type | string | 瓦片类型（water/lava/grass） |
| trigger | string | 触发器ID |
| sound | string | 踩上去的音效 |

**动画原理**：

Tiled的瓦片动画是**多帧循环**，每帧指定一个瓦片ID和持续时间。游戏引擎读取动画帧数据，按时间切换显示的瓦片。

```
瓦片ID 10（水面）的动画：
  帧0: 瓦片10, 持续200ms
  帧1: 瓦片11, 持续200ms
  帧2: 瓦片12, 持续200ms
  帧3: 瓦片11, 持续200ms  ← 回弹，形成无缝循环
```

**动画的存储**：动画数据存在.tsx文件中，属于瓦片集，所有使用该瓦片的地图共享同一动画。

#### 例子

**为瓦片添加属性**：

1. 在瓦片集面板右键某个瓦片 → Tile Properties
2. 在属性面板点击 "+" 添加自定义属性：

```
Name: solid
Type: bool
Value: true

Name: type
Type: string
Value: wall
```

**生成的.tsx片段**：

```xml
<tile id="5">
  <properties>
    <property name="solid" type="bool" value="true"/>
    <property name="type" type="string" value="wall"/>
  </properties>
</tile>
```

**创建瓦片动画**：

1. 在瓦片集面板右键瓦片 → Tile Properties
2. 切换到 "Animation" 标签
3. 添加帧：
   - 点击 "+" 添加帧
   - 选择瓦片ID（在瓦片集面板点击）
   - 设置持续时间（毫秒）

**动画的.tsx表示**：

```xml
<tile id="10">
  <properties>
    <property name="type" type="string" value="water"/>
    <property name="solid" type="bool" value="true"/>
  </properties>
  <animation>
    <frame tileid="10" duration="200"/>
    <frame tileid="11" duration="200"/>
    <frame tileid="12" duration="200"/>
    <frame tileid="11" duration="200"/>
  </animation>
</tile>
```

**游戏端读取动画（伪代码）**：

```python
class AnimatedTile:
    def __init__(self, frames):
        self.frames = frames  # [(tile_id, duration_ms), ...]
        self.current_frame = 0
        self.timer = 0

    def update(self, dt_ms):
        self.timer += dt_ms
        if self.timer >= self.frames[self.current_frame][1]:
            self.timer = 0
            self.current_frame = (self.current_frame + 1) % len(self.frames)

    def get_current_tile_id(self):
        return self.frames[self.current_frame][0]
```

**批量编辑瓦片属性**：

选中多个瓦片（Ctrl+点击或框选）→ 右键 → Tile Properties，可批量设置相同属性。

#### 总结

- **瓦片属性**：数据驱动游戏逻辑，避免代码硬编码瓦片ID
- **常用属性**：solid、damage、type、trigger、sound
- **动画**：多帧循环，每帧指定瓦片ID和持续时间
- **动画存储**：在.tsx中，所有地图共享
- **批量编辑**：选中多瓦片可批量设置属性
- **常见坑**：动画帧顺序错误导致跳变；属性类型选错（bool写成string）导致游戏解析失败；动画帧太多导致内存占用高

---

### 第6讲 外部瓦片集与共享

#### 概念

**外部瓦片集（External Tileset）** 是将瓦片集保存为独立的.tsx文件，被多个.tmx地图引用。这是大型项目的标准做法——修改一处，所有地图同步更新。本讲学习外部瓦片集的创建、引用、路径管理。

#### 原理

**内嵌 vs 外部瓦片集**：

| 特性 | 内嵌（Embed） | 外部（External） |
|------|---------------|------------------|
| 存储位置 | 嵌入.tmx文件 | 独立.tsx文件 |
| 文件大小 | .tmx变大 | .tmx小 |
| 复用性 | 仅当前地图 | 多地图共享 |
| 修改同步 | 需逐地图改 | 改一次全同步 |
| 推荐场景 | 一次性地图 | 正式项目 |

**引用机制**：

.tmx文件通过`<tileset>`标签的`source`属性引用.tsx：

```xml
<!-- 内嵌瓦片集 -->
<tileset firstgid="1" name="overworld" tilewidth="32" tileheight="32">
  <image source="overworld.png" width="256" height="256"/>
</tileset>

<!-- 外部瓦片集 -->
<tileset firstgid="1" source="tilesets/overworld.tsx"/>
```

**路径解析规则**：

Tiled使用**相对路径**，相对于.tmx文件所在目录：

```
项目结构：
my_game/
├── maps/
│   └── overworld.tmx    ← 引用 ../tilesets/overworld.tsx
└── tilesets/
    └── overworld.tsx    ← 引用 overworld.png（同目录）
```

**firstgid的作用**：

每个.tmx引用瓦片集时指定firstgid，确保GID全局唯一。同一.tsx被不同.tmx引用时，firstgid可能不同（取决于引用顺序）。

#### 例子

**创建外部瓦片集**：

方法1：新建时不勾选 "Embed in map"，自动保存为.tsx。

方法2：已有内嵌瓦片集转外部：
1. 在瓦片集面板右键瓦片集 → Export As Tileset
2. 保存为 `tilesets/overworld.tsx`
3. 原地图自动改为引用外部文件

**在多张地图中引用同一瓦片集**：

```xml
<!-- maps/village.tmx -->
<map ...>
  <tileset firstgid="1" source="../tilesets/overworld.tsx"/>
  ...
</map>

<!-- maps/dungeon.tmx -->
<map ...>
  <tileset firstgid="1" source="../tilesets/overworld.tsx"/>
  <tileset firstgid="65" source="../tilesets/dungeon.tsx"/>
  ...
</map>
```

**路径管理最佳实践**：

1. **统一资源根目录**：所有.tmx和.tsx相对于项目根目录
2. **避免绝对路径**：绝对路径换机器就失效
3. **避免".."过多**：`../../../tilesets/x.tsx`易出错，建议扁平结构
4. **使用Tiled的"Resolve Paths"**：`Edit → Preferences → Files → "Use relative paths"`确保保存时用相对路径

**瓦片集的依赖链**：

```
overworld.tmx
  └── 引用 overworld.tsx
        └── 引用 overworld.png
```

移动项目时，整条链的相对关系必须保持。推荐把整个项目文件夹作为一个整体移动。

**检查路径问题**：

如果打开.tmx时瓦片集显示红色叉号，说明路径错误。右键瓦片集 → Tileset Properties → 修改Image路径，或用 `Map → Map Properties → 修复`。

#### 总结

- **外部瓦片集**：独立.tsx文件，多地图共享，正式项目必用
- **相对路径**：相对于.tmx文件，避免绝对路径
- **firstgid**：每张地图独立分配，确保GID全局唯一
- **扁平结构**：避免过多".."，减少路径出错
- **依赖链**：.tmx → .tsx → .png，整体移动
- **常见坑**：移动文件后路径失效；不同操作系统路径分隔符（/ vs \），Tiled自动处理但手动编辑XML要注意

---

## 第三章 图层系统

> 图层是地图的骨架。本章学习瓦片图层、对象层、图片层的区别与用法，掌握图层组织技巧，构建有层次的地图。

### 第7讲 瓦片图层与绘制

#### 概念

**瓦片图层（Tile Layer）** 是Tiled中最基础的图层类型，存储一个二维瓦片ID网格。一张地图通常有多个瓦片图层，按从底到顶的顺序渲染。本讲学习瓦片图层的创建、绘制、编辑技巧。

#### 原理

**瓦片图层数据结构**：

瓦片图层本质是一个`width × height`的二维数组，每个元素是一个GID（全局瓦片ID）。GID=0表示空（无瓦片）。

```
图层 "Ground" (5×4):
0  0  0  0  0
0  1  1  1  0     ← 1是草地GID
0  1  2  1  0     ← 2是花GID
0  0  0  0  0
```

**图层渲染顺序**：

图层面板中**上面的图层渲染在顶层**。所以规划时把地面放最下，高层放最上：

```
图层面板（从上到下）：
  Overhead   ← 最上层（树冠、屋顶）
  Decor      ← 装饰层（花、石头）
  Ground     ← 最下层（草地、地板）
```

**渲染顺序（renderorder）**：

地图级别的属性，决定瓦片绘制的扫描顺序：
- `right-down`（默认）：从左到右，从上到下
- `right-up`：从左到右，从下到上
- `left-down`：从右到左，从上到下
- `left-up`：从右到左，从下到上

对于正交地图，渲染顺序影响不大（瓦片不重叠）。但对等距地图，正确的渲染顺序才能保证遮挡关系正确。

**图层透明度与混合**：

每个图层可设置透明度（0-1）和混合模式（Normal/Additive/Multiply），用于制作半透明效果（如水面、雾气）。

#### 例子

**创建瓦片图层**：

图层面板右键 → Add → Tile Layer，命名规范：
- `Ground`：地面（草地、地板、水）
- `Path`：路径（小路、桥）
- `Decor`：装饰（花、小石头、蘑菇）
- `Building`：建筑（墙、屋顶）
- `Overhead`：高层（树冠、拱门顶部）

**绘制操作**：

1. **选中图层**：在图层面板点击图层名
2. **选择瓦片**：在瓦片集面板点击瓦片
3. **绘制**：用印章工具（B）点击或拖动
4. **擦除**：用橡皮擦（E）或右键拖动

**图层操作**：

| 操作 | 方法 |
|------|------|
| 重命名 | 双击图层名 |
| 调整顺序 | 拖动图层上下移动 |
| 显示/隐藏 | 点击眼睛图标 |
| 锁定 | 点击锁图标 |
| 不透明度 | 双击图层 → Opacity |
| 复制 | 右键 → Duplicate |
| 删除 | 右键 → Remove Layer |
| 合并 | 选中多个 → 右键 → Merge Down |

**图层属性的.tmx表示**：

```xml
<layer id="1" name="Ground" width="30" height="20"
       opacity="1" visible="1" locked="0">
  <data encoding="base64" compression="zlib">
    eJztwTEBAAAAwqD1T20JT6AAAHgaCAAAAg==
  </data>
</layer>

<layer id="2" name="Water" width="30" height="20" opacity="0.7">
  <properties>
    <property name="effect" type="string" value="swim"/>
  </properties>
  <data encoding="base64" compression="zlib">...</data>
</layer>
```

**无限图层（Infinite Map）**：

新建地图时勾选 "Infinite"，图层变为无限大小，只在有瓦片的区域存储数据（chunk机制）。适合大型开放世界，避免预设尺寸限制。

```
普通图层：固定 width×height 数组
无限图层：按16×16的chunk动态分配，只存有内容的chunk
```

#### 总结

- **瓦片图层**：二维GID数组，0表示空
- **图层顺序**：面板上方=渲染顶层，地面在下高层在上
- **渲染顺序**：地图级属性，等距地图尤为重要
- **图层透明度**：0-1，用于水面、雾气效果
- **无限图层**：动态chunk，适合开放世界
- **常见坑**：图层顺序颠倒导致玩家被树根遮挡；忘记锁定已完成的图层导致误改；无限地图导出时chunk边界处理

---

### 第8讲 对象层与几何对象

#### 概念

**对象层（Object Layer / Object Group）** 存储几何对象（矩形、圆、多边形、折线、点），用于表示游戏逻辑元素：碰撞体、触发器、出生点、NPC位置、巡逻路径。对象层不直接渲染瓦片，而是提供游戏逻辑数据。

#### 原理

**对象层 vs 瓦片图层**：

| 特性 | 瓦片图层 | 对象层 |
|------|----------|--------|
| 数据 | 瓦片ID网格 | 几何对象列表 |
| 坐标 | 瓦片坐标（整数） | 像素坐标（浮点） |
| 用途 | 视觉渲染 | 游戏逻辑 |
| 对象类型 | 瓦片 | 矩形/圆/多边形/点/瓦片对象 |
| 数量 | 固定网格 | 任意数量 |

**对象类型**：

1. **矩形（Rectangle）**：碰撞体、触发区域
2. **椭圆（Ellipse）**：圆形碰撞、光照范围
3. **多边形（Polygon）**：复杂形状碰撞、地形边界
4. **折线（Polyline）**：巡逻路径、河流走向
5. **点（Point）**：出生点、传送目标
6. **瓦片对象（Tile Object）**：放置瓦片作为对象（如NPC、宝箱）

**对象的像素坐标**：

对象使用像素坐标（浮点），而非瓦片坐标。一个对象在(96, 64)表示位于第3列第2行瓦片的左上角（32px瓦片时）。

**对象属性**：

每个对象可有：名称、类型、位置、尺寸、旋转、自定义属性。其中"类型"字段常用于游戏端识别对象种类。

#### 例子

**创建对象层**：

图层面板右键 → Add → Object Layer，命名：
- `Collision`：碰撞体
- `Triggers`：触发器
- `Spawns`：出生点
- `NPCs`：NPC位置
- `Patrols`：巡逻路径

**绘制对象**：

工具栏选择对象工具：
- `R`（矩形）：拖动绘制
- `O`（选择对象）：选择、移动、缩放
- `M`（编辑对象）：编辑顶点（多边形）

**典型用例**：

**1. 碰撞体（矩形）**：
```
Name: wall_01
Type: solid
Position: (96, 64)
Size: 32×32
Properties:
  solid: true
```

**2. 触发器（矩形）**：
```
Name: door_to_village
Type: portal
Position: (480, 320)
Size: 32×32
Properties:
  target_map: village.tmx
  target_x: 100
  target_y: 200
```

**3. 出生点（点）**：
```
Name: player_start
Type: spawn
Position: (160, 160)
Properties:
  direction: down
```

**4. 巡逻路径（折线）**：
```
Name: guard_patrol
Type: patrol
Points: (100,100) (200,100) (200,200) (100,200)
Properties:
  loop: true
  speed: 50
```

**5. NPC（瓦片对象）**：
在对象层选中后，从瓦片集面板点击NPC瓦片，然后在地图上点击放置：
```
Name: merchant_01
Type: npc
Position: (320, 224)
GID: 65 (指向NPC瓦片)
Properties:
  dialog: "Welcome to my shop!"
  shop_id: 1
```

**对象层的.tmx表示**：

```xml
<objectgroup id="4" name="Collision" color="#ff0000">
  <object id="1" name="wall_01" type="solid" x="96" y="64" width="32" height="32">
    <properties>
      <property name="solid" type="bool" value="true"/>
    </properties>
  </object>
</objectgroup>

<objectgroup id="5" name="Triggers" color="#00ff00">
  <object id="2" name="door_to_village" type="portal" x="480" y="320" width="32" height="32">
    <properties>
      <property name="target_map" type="string" value="village.tmx"/>
      <property name="target_x" type="int" value="100"/>
      <property name="target_y" type="int" value="200"/>
    </properties>
  </object>
</objectgroup>

<objectgroup id="6" name="Patrols" color="#0000ff">
  <object id="3" name="guard_patrol" type="patrol" x="100" y="100">
    <polyline points="0,0 100,0 100,100 0,100"/>
    <properties>
      <property name="loop" type="bool" value="true"/>
      <property name="speed" type="int" value="50"/>
    </properties>
  </object>
</objectgroup>
```

**对象层颜色**：每个对象层可设置颜色（color属性），便于区分不同类型的对象。

#### 总结

- **对象层**：存储几何对象，用于游戏逻辑（碰撞、触发、出生点）
- **六种对象**：矩形、椭圆、多边形、折线、点、瓦片对象
- **像素坐标**：对象用浮点像素坐标，精确放置
- **Type字段**：游戏端识别对象种类的关键
- **对象层颜色**：不同层用不同颜色，便于区分
- **常见坑**：碰撞体画在错误的图层导致游戏端漏读；对象坐标用瓦片坐标思维导致位置偏移；多边形顶点顺序错误导致碰撞计算异常

---

### 第9讲 图层组织与可见性

#### 概念

随着地图复杂度增加，图层数量可能达到十几个甚至几十个。良好的图层组织是可维护地图的关键。本讲学习图层分组、可见性控制、图层过滤、图层效果。

#### 原理

**图层分组（Layer Group）**：

Tiled支持嵌套图层组，类似文件夹，便于组织：

```
图层面板：
📁 Village
   📁 Buildings
      🟦 Houses
      🟦 Shops
   📁 Nature
      🟦 Trees
      🟦 Bushes
   🟦 Ground
📁 Collision
   🟦 Walls
   🟦 Water
```

**图层可见性控制**：

- **眼睛图标**：单个图层显示/隐藏
- **图层组眼睛**：整组显示/隐藏
- **隔离模式**：右键图层 → Isolate，只显示该图层
- **过滤**：图层面板顶部搜索框，按名称过滤

**图层不透明度与混合模式**：

- **Normal**：正常覆盖
- **Additive**：相加（发光效果）
- **Multiply**：相乘（阴影、暗化）
- **Screen**：屏幕（提亮）
- **Overlay**：叠加

**图层偏移（Offset）**：

图层可设置x/y偏移，整体平移。用于视差滚动——背景层偏移量大，前景层偏移量小。

**图层锁定**：

- **锁定内容**：防止误编辑
- **锁定可见性**：防止误隐藏

#### 例子

**创建图层组**：

图层面板右键 → Add → Group Layer，命名后拖动其他图层进入。

**视差滚动配置**：

```
背景层（远山）：
  Offset X: 0, Offset Y: 0
  Parallax X: 0.3, Parallax Y: 0.3   ← 滚动30%

中景层（树木）：
  Parallax X: 0.6, Parallax Y: 0.6

前景层（地面）：
  Parallax X: 1.0, Parallax Y: 1.0   ← 正常滚动
```

**图层组的.tmx表示**：

```xml
<group id="1" name="Village" offsetx="0" offsety="0">
  <layer id="2" name="Ground" width="30" height="20">
    <data encoding="base64" compression="zlib">...</data>
  </layer>
  <group id="3" name="Buildings" offsetx="0" offsety="0">
    <layer id="4" name="Houses" width="30" height="20">
      <data encoding="base64" compression="zlib">...</data>
    </layer>
  </group>
</group>
```

**图层混合模式**：

```xml
<layer id="5" name="Fog" width="30" height="20"
       opacity="0.5" blendmode="multiply">
  <data encoding="base64" compression="zlib">...</data>
</layer>
```

**实战图层组织建议**：

```
📁 UI
   🟦 Minimap
📁 Effects
   🟦 Particles
   🟦 Weather
📁 Overhead
   🟦 Tree_Tops
   🟦 Roof_Tops
📁 Entities
   🔵 NPCs
   🔵 Enemies
   🔵 Items
📁 Collision
   🔵 Walls
   🔵 Water
   🔵 Triggers
📁 Decor
   🟦 Flowers
   🟦 Stones
📁 Ground
   🟦 Path
   🟦 Water_Anim
   🟦 Grass
```

**图层过滤技巧**：

图层面板顶部搜索框输入关键字（如"collision"），只显示匹配的图层。大型地图必备。

#### 总结

- **图层分组**：嵌套组织，类似文件夹，提升可维护性
- **可见性控制**：眼睛图标、隔离模式、搜索过滤
- **混合模式**：Normal/Additive/Multiply等，用于光影效果
- **图层偏移**：视差滚动，背景慢前景快
- **图层锁定**：防止误编辑已完成的部分
- **常见坑**：图层太多不分组导致混乱；忘记锁定导致误改；视差系数设置不当导致背景穿帮

---

## 第四章 绘制工具与技巧

> 工欲善其事，必先利其器。本章学习Tiled的全部绘制工具，从基础印章到高级地形系统、王冠刷，让你能高效绘制自然过渡的地图。

### 第10讲 基础绘制工具

#### 概念

Tiled提供一套完整的绘制工具：印章（Stamp）、桶填充（Bucket Fill）、橡皮擦（Eraser）、形状填充（Shape Fill）、阴影笔（Terrain Brush）。本讲详解每个工具的用法和适用场景。

#### 原理

**工具栏总览**：

| 快捷键 | 工具 | 用途 |
|--------|------|------|
| B | 印章工具（Stamp Brush） | 绘制选中的瓦片 |
| E | 橡皮擦（Eraser） | 擦除瓦片 |
| F | 桶填充（Bucket Fill） | 填充连通区域 |
| L | 线条（Line） | 绘制直线 |
| R | 矩形（Rectangle） | 绘制矩形区域 |
| C | 圆（Circle） | 绘制圆形区域 |
| O | 选择对象（Select Objects） | 选择/移动对象 |
| M | 编辑对象（Edit Objects） | 编辑对象顶点 |
| T | 地形刷（Terrain Brush） | 自动过渡绘制 |
| W | 王冠刷（Wang Brush） | 王冠瓦片绘制 |

**印章工具的"笔刷"概念**：

印章工具不仅画单个瓦片，可以画一个"笔刷区域"。在瓦片集面板框选多个瓦片，就形成一个笔刷，可以一次性绘制一个图案（如一栋房子）。

**桶填充的连通性**：

桶填充基于**洪水填充算法（Flood Fill）**，从点击位置开始，把所有连通的相同瓦片替换为新瓦片。适合大面积替换（如把所有草地换成沙地）。

**形状填充的边界**：

矩形/圆/线工具先预览，松开鼠标后填充。适合绘制规则形状（如矩形房间、圆形湖泊）。

#### 例子

**印章工具用法**：

1. 单击瓦片集面板的瓦片 → 笔刷为单瓦片
2. 框选瓦片集面板的多个瓦片 → 笔刷为多瓦片图案
3. 在地图上点击或拖动 → 绘制
4. 右键拖动 → 拾取地图上的瓦片作为笔刷

**笔刷模式**：

印章工具顶部有笔刷模式选项：
- **Pen（笔）**：直接绘制
- **Spray（喷雾）**：随机散布，适合绘制花草
- **Fill（填充）**：类似桶填充，但用笔刷图案

**桶填充用法**：

1. 选择目标瓦片
2. 按F切换到桶填充
3. 点击要替换的区域

**形状填充用法**：

1. 选择瓦片
2. 按R（矩形）/C（圆）/L（线）
3. 拖动绘制形状，松开确认

**随机瓦片绘制**：

在瓦片集面板Ctrl+点击多个瓦片，选中区域成为"随机池"。绘制时从池中随机选瓦片，适合绘制有变化的草地、石地：

```
选中3种草地瓦片 → 绘制时随机出现，避免单调
```

**笔刷尺寸调整**：

`[` 缩小笔刷，`]` 放大笔刷。或用数字键1-9设置尺寸。

**翻转与旋转**：

绘制时按快捷键翻转/旋转笔刷：
- `X`：水平翻转
- `Y`：垂直翻转
- `Z`：旋转90度

**复制地图区域**：

1. 用选择工具（S）框选区域
2. Ctrl+C复制
3. Ctrl+V粘贴（变成笔刷）
4. 点击放置

#### 总结

- **印章工具**：核心绘制工具，支持单瓦片和多瓦片笔刷
- **桶填充**：洪水填充连通区域，适合大面积替换
- **形状填充**：矩形/圆/线，适合规则形状
- **随机瓦片**：Ctrl多选形成随机池，绘制有变化的纹理
- **翻转旋转**：X/Y/Z快捷键，复用瓦片减少资源
- **常见坑**：笔刷尺寸过大误伤周围瓦片；桶填充未连通区域只填一部分；翻转的瓦片在游戏端需特殊处理

---

### 第11讲 地形系统（Terrains）

#### 概念

**地形系统（Terrains）** 是Tiled的自动过渡绘制功能。定义瓦片集的"地形"（如草地、水、沙），Tiled自动选择合适的过渡瓦片，绘制自然衔接的地图。无需手动挑选15种过渡瓦片，大幅提升绘制效率。

#### 原理

**地形（Terrain）的概念**：

每个瓦片的四个角可以标记为不同地形。例如一个"草地-水"过渡瓦片，左上角是草地，右下角是水。Tiled维护一个地形集合（如草地、水、沙、山），每个瓦片标注四角地形。

```
瓦片四角地形标记：
  草 ┃ 草
  ──╂──
  水 ┃ 水

这个瓦片是"上草下水"的过渡
```

**地形集合（Terrain Set）**：

瓦片集中定义一组地形（如4种：草地、水、沙、山）。每个瓦片标注四角属于哪种地形。

**自动过渡原理**：

用地形刷绘制时，Tiled根据周围瓦片的地形，自动选择四角匹配的瓦片。例如在草地中画一片水，Tiled自动在边界放置"半草半水"的过渡瓦片。

**15种过渡瓦片**：

对于两种地形A和B的组合，理论上需要15种过渡瓦片（2^4 - 1，四角各有A/B两种选择，减去全A和全B）。完整的地形图集应包含这15种过渡。

#### 例子

**配置地形集合**：

1. 打开瓦片集编辑器：`Tileset → Edit Terrain`
2. 添加地形：草地、水、沙、山
3. 为每个瓦片标注四角地形

**标注瓦片地形**：

在瓦片集面板，点击瓦片 → 在地形编辑器中点击四角，分配地形：

```
瓦片0（纯草地）：四角都是"草地"
瓦片1（左上水）：左上=水，其余=草地
瓦片2（上水）：上两角=水，下两角=草地
...
瓦片15（纯水）：四角都是"水"
```

**使用地形刷**：

1. 按T切换到地形刷
2. 在瓦片集面板选择地形（如"水"）
3. 在地图上绘制，Tiled自动选择过渡瓦片

**地形集合的.tsx表示**：

```xml
<tileset name="overworld" tilewidth="32" tileheight="32">
  <image source="overworld.png" width="256" height="256"/>

  <terraintypes>
    <terrain name="Grass" tile="0"/>
    <terrain name="Water" tile="15"/>
    <terrain name="Sand"  tile="8"/>
  </terraintypes>

  <tile id="0" terrain="0,0,0,0"/>     <!-- 纯草地 -->
  <tile id="1" terrain="1,0,0,0"/>     <!-- 左上水 -->
  <tile id="2" terrain="1,1,0,0"/>     <!-- 上水 -->
  <tile id="3" terrain="0,1,0,0"/>     <!-- 右上水 -->
  <tile id="4" terrain="0,0,1,0"/>     <!-- 左下水 -->
  <tile id="5" terrain="1,0,1,0"/>     <!-- 左水 -->
  <tile id="6" terrain="1,1,1,0"/>     <!-- 上左下水 -->
  <tile id="7" terrain="0,1,1,0"/>     <!-- 右水 -->
  <tile id="8" terrain="0,0,1,1"/>     <!-- 下水 -->
  <tile id="9" terrain="1,0,1,1"/>     <!-- 左下水 -->
  <tile id="10" terrain="1,1,1,1"/>    <!-- 纯水 -->
  ...
</tileset>
```

**terrain属性格式**：`左上,右上,左下,右下`，数字是地形ID。

**地形刷的尺寸**：

地形刷顶部可设置尺寸（1×1, 3×3, 5×5），大尺寸一次绘制更大区域。

#### 总结

- **地形系统**：自动过渡绘制，无需手动挑选过渡瓦片
- **四角标记**：每个瓦片标注四角地形
- **地形集合**：瓦片集定义一组地形（草地、水、沙等）
- **15种过渡**：两种地形组合需15种过渡瓦片
- **terrain属性**：`左上,右上,左下,右下`格式
- **常见坑**：瓦片集缺少某些过渡瓦片导致自动选择失败；地形标注错误导致过渡方向反了；地形系统不适合所有图集风格

---

### 第12讲 王冠刷（Wang Tiles）

#### 概念

**王冠刷（Wang Tiles）** 是地形系统的升级版，支持更复杂的过渡规则。地形系统只有4个角，王冠刷支持每条边和每个角共8个连接点，能处理更细腻的过渡（如河流、道路、铁轨）。这是Tiled 1.5+的新功能。

#### 原理

**Wang Tiles的由来**：

由数学家Hao Wang提出，用于无周期性铺砖。游戏开发中用于自动过渡。每个瓦片的边和角有"颜色"标记，相邻瓦片的边颜色必须匹配。

**8个连接点**：

```
   N1  N2  N3
  W8  ┌───┐  E4
      │   │
  W7  └───┘  E5
   S6  S5  S4
```

每个瓦片有8个连接点（4边中点+4角），每个点可标记为某种"颜色"（如0=无，1=草地，2=水）。

**Wang vs Terrain**：

| 特性 | Terrain | Wang |
|------|---------|------|
| 连接点 | 4角 | 4边+4角=8 |
| 过渡细腻度 | 一般 | 高 |
| 适合 | 简单地形 | 道路、河流、铁轨 |
| 配置复杂度 | 低 | 高 |

**Wang颜色集（Wang Color Set）**：

定义一组颜色（如0=无，1=草地，2=水，3=沙）。每个瓦片的8个点标注颜色。

**自动匹配规则**：

绘制时，Tiled根据周围瓦片的边/角颜色，选择匹配的瓦片。相邻瓦片的对应边颜色必须相同。

#### 例子

**配置Wang颜色集**：

1. 瓦片集面板 → `Tileset → Edit Wang Sets`
2. 添加Wang颜色集，命名（如"Terrain"）
3. 添加颜色：0=无, 1=草地, 2=水, 3=沙

**标注瓦片Wang**：

在Wang编辑器中，点击瓦片的8个点，分配颜色：

```
瓦片0（纯草地）：8点都是1
瓦片1（左水右草）：左边=2，右边=1
瓦片2（上水下水）：上边=2，下边=1
...
```

**使用王冠刷**：

1. 按W切换到王冠刷
2. 选择颜色（如"水"）
3. 在地图上绘制，Tiled自动选择匹配瓦片

**Wang的.tsx表示**：

```xml
<tileset name="overworld" tilewidth="32" tileheight="32">
  <wangsets>
    <wangset name="Terrain" type="corner" tile="-1">
      <wangcolor name="Empty" color="#000000" tile="-1"/>
      <wangcolor name="Grass" color="#00ff00" tile="0"/>
      <wangcolor name="Water" color="#0000ff" tile="15"/>
      <wangcolor name="Sand"  color="#ffff00" tile="8"/>

      <tile id="0" wang="1,1,1,1,1,1,1,1"/>     <!-- 纯草地 -->
      <tile id="1" wang="2,2,1,1,1,1,1,1"/>     <!-- 左上水 -->
      <tile id="2" wang="2,1,2,1,1,1,1,1"/>     <!-- 上水 -->
      <tile id="15" wang="2,2,2,2,2,2,2,2"/>    <!-- 纯水 -->
    </wangset>
  </wangsets>
</tileset>
```

**wang属性格式**：8个数字，顺序为`上左,上中,上右,右中,下右,下中,下左,左中`。

**Wang类型**：

- **corner**：基于角的颜色匹配
- **edge**：基于边的颜色匹配
- **mixed**：角和边都匹配

**实战用例：绘制河流**：

1. 配置Wang颜色集：水=蓝，草地=绿
2. 标注所有河流瓦片的8点颜色
3. 用王冠刷绘制河流路径，Tiled自动选择弯曲过渡瓦片

**实战用例：绘制道路**：

道路有方向性（南北、东西、十字、T字），用Wang的边匹配可以自动选择正确的道路瓦片。

#### 总结

- **王冠刷**：8连接点（4边+4角），比地形系统更细腻
- **Wang颜色集**：定义颜色，每个瓦片8点标注
- **适用场景**：河流、道路、铁轨等有方向性的过渡
- **Wang类型**：corner/edge/mixed，按需选择
- **配置复杂**：需要标注更多瓦片，但过渡效果更好
- **常见坑**：颜色标注遗漏导致匹配失败；Wang和Terrain混用导致混乱；颜色集太多增加维护成本

---

## 第五章 对象与属性

> 对象和属性让地图从"好看的图"变成"可交互的游戏数据"。本章学习对象类型、自定义属性、对象模板，实现数据驱动的游戏设计。

### 第13讲 对象类型与编辑

#### 概念

**对象类型（Object Types）** 是Tiled中预定义的对象类别，统一管理同类对象的属性模板。例如定义"NPC"类型，所有NPC对象自动继承NPC的属性集（dialog、shop_id等），避免每个对象重复设置。这是大型项目的必备功能。

#### 原理

**为什么需要对象类型？**

如果每个对象都手动添加属性，会有两个问题：
1. **重复劳动**：100个NPC都要加dialog、shop_id属性
2. **不一致**：某个NPC漏加属性导致游戏崩溃

对象类型解决这两个问题：定义一次，所有同类对象自动继承。

**对象类型 vs 对象的Type字段**：

- **Type字段**：对象的字符串属性，如"npc"、"portal"
- **对象类型**：在`View → Object Types Editor`中预定义的类型，与Type字段对应

**属性继承**：

对象类型的属性是"默认值"，对象可覆盖。例如NPC类型默认`shop_id=0`，某个对象可设为`shop_id=5`。

**颜色标识**：

每个对象类型可设置颜色，地图上该类型的对象用对应颜色显示，便于区分。

#### 例子

**定义对象类型**：

`View → Object Types Editor` → "+" 添加类型：

```
Type Name: NPC
Color: #00ff00
Properties:
  dialog: string = ""
  shop_id: int = 0
  hostile: bool = false

Type Name: Portal
Color: #0000ff
Properties:
  target_map: string = ""
  target_x: int = 0
  target_y: int = 0

Type Name: Enemy
Color: #ff0000
Properties:
  enemy_type: string = "slime"
  hp: int = 3
  attack: int = 1
  detect_range: int = 150
```

**导出对象类型文件**：

对象类型可导出为.xml文件，团队共享：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<objecttypes>
  <objecttype name="NPC" color="#00ff00">
    <property name="dialog" type="string" default=""/>
    <property name="shop_id" type="int" default="0"/>
    <property name="hostile" type="bool" default="false"/>
  </objecttype>
  <objecttype name="Portal" color="#0000ff">
    <property name="target_map" type="string" default=""/>
    <property name="target_x" type="int" default="0"/>
    <property name="target_y" type="int" default="0"/>
  </objecttype>
  <objecttype name="Enemy" color="#ff0000">
    <property name="enemy_type" type="string" default="slime"/>
    <property name="hp" type="int" default="3"/>
    <property name="attack" type="int" default="1"/>
    <property name="detect_range" type="int" default="150"/>
  </objecttype>
</objecttypes>
```

**使用对象类型**：

1. 创建对象，设置Type为"NPC"
2. 对象自动继承NPC类型的属性（dialog、shop_id、hostile）
3. 可覆盖默认值

**对象类型的.tmx体现**：

对象本身不存储继承的属性，只存储覆盖值。游戏端读取时，先加载对象类型定义，再合并对象的覆盖值：

```xml
<!-- 对象只存储覆盖的属性 -->
<object id="1" name="merchant" type="NPC" x="320" y="224" width="32" height="32">
  <properties>
    <property name="dialog" type="string" value="Welcome!"/>
    <property name="shop_id" type="int" value="5"/>
    <!-- hostile未覆盖，使用类型默认值false -->
  </properties>
</object>
```

**游戏端读取逻辑（伪代码）**：

```python
def load_object(obj_xml, object_types):
    obj_type = object_types.get(obj_xml.type)
    props = obj_type.properties.copy()  # 继承默认值
    props.update(obj_xml.properties)    # 覆盖
    return GameObject(type=obj_xml.type, properties=props)
```

#### 总结

- **对象类型**：预定义的属性模板，同类对象自动继承
- **解决重复**：定义一次，多处复用
- **属性继承**：类型提供默认值，对象可覆盖
- **颜色标识**：不同类型用不同颜色，便于区分
- **导出共享**：.xml文件团队共享，保证一致
- **常见坑**：忘记导出对象类型文件导致团队成员属性不一致；修改类型属性后旧对象不自动更新（需手动刷新）

---

### 第14讲 自定义属性系统

#### 概念

**自定义属性（Custom Properties）** 是Tiled最强大的功能之一。可以为地图、瓦片集、瓦片、图层、对象添加任意属性，实现完全数据驱动的设计。本讲学习属性的类型、批量编辑、属性模板。

#### 原理

**属性的作用域**：

Tiled中属性可附加在五个层级：

| 层级 | 用途 | 示例 |
|------|------|------|
| 地图 | 全局配置 | bgm、weather、time_of_day |
| 瓦片集 | 瓦片集元数据 | author、version |
| 瓦片 | 瓦片行为 | solid、damage、type |
| 图层 | 图层行为 | parallax、collision_layer |
| 对象 | 对象行为 | dialog、target_map、hp |

**属性类型**：

Tiled支持丰富的属性类型：

| 类型 | 说明 | 示例 |
|------|------|------|
| string | 字符串 | "village.tmx" |
| int | 整数 | 100 |
| float | 浮点 | 0.5 |
| bool | 布尔 | true |
| color | 颜色 | #ff0000 |
| file | 文件路径 | assets/sounds/bgm.ogg |
| object | 对象引用 | 引用另一个对象的ID |
| enum | 枚举 | ["easy", "normal", "hard"] |

**属性模板（Property Templates）**：

类似对象类型，但更通用。可定义一组属性模板，附加到任何元素：

```
模板 "Interactable":
  interact_range: int = 32
  interact_text: string = "Press E"
```

#### 例子

**添加属性**：

1. 选中元素（瓦片/对象/图层/地图）
2. 在属性面板点击 "+"
3. 输入名称、选择类型、设置默认值

**地图级属性示例**：

```
Map Properties:
  bgm: file = assets/music/overworld.ogg
  weather: enum = ["clear", "rain", "snow"] = "clear"
  time_of_day: enum = ["day", "dusk", "night"] = "day"
  spawn_point: object = (引用player_start对象)
```

**瓦片属性示例**：

```
瓦片ID 5 (墙壁):
  solid: bool = true
  type: string = "wall"
  sound: file = assets/sounds/hit_wall.wav

瓦片ID 10 (水):
  solid: bool = true
  type: string = "water"
  damage: int = 0
  effect: string = "swim"

瓦片ID 15 (岩浆):
  solid: bool = true
  type: string = "lava"
  damage: int = 2
  effect: string = "burn"
```

**图层属性示例**：

```
图层 "Collision":
  is_collision: bool = true
  collision_type: enum = ["full", "top_only", "one_way"] = "full"

图层 "Background":
  parallax_x: float = 0.3
  parallax_y: float = 0.3
  scroll_speed: int = 50
```

**对象属性示例**：

```
对象 "chest_01":
  Type: chest
  Properties:
    item_id: int = 5
    item_count: int = 100
    locked: bool = true
    unlock_key: string = "silver_key"
    opened: bool = false
```

**enum属性的.tmx表示**：

```xml
<properties>
  <property name="weather" type="string">
    <propertytype>
      <enum>
        <value>clear</value>
        <value>rain</value>
        <value>snow</value>
      </enum>
    </propertytype>
  </property>
</properties>
```

**属性模板**：

`View → Property Templates` → 创建模板：

```xml
<propertytemplates>
  <propertytemplate name="Interactable">
    <property name="interact_range" type="int" default="32"/>
    <property name="interact_text" type="string" default="Press E"/>
  </propertytemplate>
</propertytemplates>
```

附加模板到对象：对象属性面板 → "Templates" → 选择模板。

**批量编辑属性**：

选中多个对象/瓦片 → 右键 → Properties → 可批量设置相同属性。

#### 总结

- **五层属性**：地图、瓦片集、瓦片、图层、对象
- **丰富类型**：string/int/float/bool/color/file/object/enum
- **enum类型**：限定取值范围，避免拼写错误
- **属性模板**：复用属性集，附加到任何元素
- **批量编辑**：多选后统一设置，提升效率
- **常见坑**：属性名拼写不一致导致游戏端解析失败；enum值修改后旧数据不自动迁移；file类型路径错误导致加载失败

---

### 第15讲 对象模板与复用

#### 概念

**对象模板（Object Templates）** 是预定义的对象配置，包含瓦片、尺寸、属性。类似"蓝图"——定义一次，多次实例化。修改模板，所有实例自动更新。适合大量重复的对象（如宝箱、敌人、传送门）。

#### 原理

**对象模板 vs 对象类型**：

| 特性 | 对象类型 | 对象模板 |
|------|----------|----------|
| 内容 | 仅属性 | 瓦片+尺寸+属性 |
| 复用 | 属性集 | 完整对象 |
| 修改同步 | 属性同步 | 整个对象同步 |
| 存储 | .xml文件 | .tx文件 |

**模板实例化**：

从模板创建的对象是"实例"，与模板保持链接。修改模板，所有实例更新；实例可覆盖部分属性。

**模板文件（.tx）**：

每个模板是一个独立的.tx文件，可被多个地图引用。

#### 例子

**创建对象模板**：

1. 在地图上创建一个对象，配置好瓦片、尺寸、属性
2. 右键对象 → "Save As Template"
3. 保存为 `templates/chest.tx`

**chest.tx内容**：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<template>
  <object type="chest" width="32" height="32" gid="65">
    <properties>
      <property name="item_id" type="int" value="5"/>
      <property name="item_count" type="int" value="100"/>
      <property name="locked" type="bool" value="true"/>
      <property name="opened" type="bool" value="false"/>
    </properties>
  </object>
</template>
```

**使用模板**：

1. `View → Templates` 打开模板面板
2. 从模板面板拖动模板到地图
3. 创建实例，可调整位置和覆盖属性

**模板实例的.tmx表示**：

```xml
<object id="10" name="chest_01" template="templates/chest.tx"
        type="chest" x="480" y="320" width="32" height="32" gid="65">
  <properties>
    <!-- 只存储覆盖的属性 -->
    <property name="item_id" type="int" value="10"/>  <!-- 覆盖为10 -->
  </properties>
</object>
```

**修改模板同步**：

修改 `chest.tx` 中的属性默认值，所有引用该模板的对象自动更新（除非实例覆盖了该属性）。

**模板面板操作**：

- **拖动放置**：从面板拖到地图
- **编辑模板**：双击面板中的模板，打开编辑器
- **重新加载**：修改外部.tx文件后，右键模板 → Reload

**实战用例：敌人模板**：

```
templates/slime.tx:
  Type: enemy
  GID: 70 (史莱姆瓦片)
  Size: 32×32
  Properties:
    enemy_type: "slime"
    hp: 3
    attack: 1
    speed: 60
    detect_range: 150

templates/bat.tx:
  Type: enemy
  GID: 71 (蝙蝠瓦片)
  Properties:
    enemy_type: "bat"
    hp: 2
    attack: 1
    speed: 120
    flying: true
```

放置敌人时，从模板面板拖动对应模板，无需每次配置属性。

#### 总结

- **对象模板**：完整对象蓝图，含瓦片+尺寸+属性
- **模板文件**：独立.tx文件，多地图共享
- **实例化**：拖动模板到地图创建实例
- **修改同步**：改模板，所有实例更新（除覆盖值）
- **vs 对象类型**：模板更完整，类型仅属性
- **常见坑**：模板路径错误导致实例显示为空；修改模板后忘记重新加载；实例覆盖属性后模板修改不生效

---

## 第六章 导出与集成

> 地图做好了，怎么用？本章学习TMX/TSX/JSON文件格式，以及如何将Tiled地图集成到SDL2、Unity、Godot等游戏引擎。

### 第16讲 TMX/TSX文件格式解析

#### 概念

**TMX（Tile Map XML）** 是Tiled的原生地图格式，基于XML。**TSX（Tile Set XML）** 是瓦片集格式。理解TMX/TSX结构，是游戏端解析地图、调试问题、编写转换工具的基础。

#### 原理

**TMX文件整体结构**：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<map version="1.9" tiledversion="1.9.2"
     orientation="orthogonal" renderorder="right-down"
     width="30" height="20" tilewidth="32" tileheight="32"
     infinite="0" nextlayerid="5" nextobjectid="10"
     backgroundcolor="#000000">

  <!-- 自定义属性 -->
  <properties>
    <property name="bgm" type="file" value="assets/music/overworld.ogg"/>
  </properties>

  <!-- 瓦片集引用 -->
  <tileset firstgid="1" source="tilesets/overworld.tsx"/>
  <tileset firstgid="65" source="tilesets/characters.tsx"/>

  <!-- 瓦片图层 -->
  <layer id="1" name="Ground" width="30" height="20">
    <data encoding="base64" compression="zlib">
      eJztwTEBAAAAwqD1T20JT6AAAHgaCAAAAg==
    </data>
  </layer>

  <!-- 对象层 -->
  <objectgroup id="3" name="Collision" color="#ff0000">
    <object id="1" name="wall_01" type="solid" x="96" y="64" width="32" height="32"/>
  </objectgroup>

  <!-- 图层组 -->
  <group id="4" name="Village">
    <layer id="5" name="Houses" width="30" height="20">
      <data encoding="base64" compression="zlib">...</data>
    </layer>
  </group>
</map>
```

**关键属性解读**：

| 属性 | 说明 |
|------|------|
| orientation | 地图方向：orthogonal/isometric/hexagonal/staggered |
| renderorder | 渲染顺序：right-down/right-up/left-down/left-up |
| width/height | 地图尺寸（瓦片数） |
| tilewidth/tileheight | 瓦片尺寸（像素） |
| infinite | 是否无限地图 |
| nextlayerid | 下一个图层ID（自增） |
| nextobjectid | 下一个对象ID（自增） |

**瓦片数据的编码与压缩**：

| encoding | 说明 | 文件大小 |
|----------|------|----------|
| csv | 逗号分隔文本 | 最大 |
| base64 | Base64二进制 | 中等 |
| base64+zlib | Base64+zlib压缩 | 最小 |
| base64+gzip | Base64+gzip压缩 | 小 |

**GID的位标志**：

GID不仅存瓦片ID，还用高位存标志：

```
GID 32位：
  bit 31: 水平翻转
  bit 30: 垂直翻转
  bit 29: 对角翻转（旋转90度）
  bit 28: 保留
  bit 27-0: 实际瓦片ID（28位，足够2.7亿瓦片）
```

解析时需先剥离高3位标志：

```python
FLIPPED_HORIZONTALLY = 0x80000000
FLIPPED_VERTICALLY   = 0x40000000
FLIPPED_DIAGONALLY   = 0x20000000

def parse_gid(gid):
    h_flip = gid & FLIPPED_HORIZONTALLY
    v_flip = gid & FLIPPED_VERTICALLY
    d_flip = gid & FLIPPED_DIAGONALLY
    real_gid = gid & ~(FLIPPED_HORIZONTALLY | FLIPPED_VERTICALLY | FLIPPED_DIAGONALLY)
    return real_gid, h_flip, v_flip, d_flip
```

#### 例子

**CSV编码的TMX**（最易读）：

```xml
<layer id="1" name="Ground" width="5" height="4">
  <data encoding="csv">
1,1,1,1,1,
1,0,0,0,1,
1,0,2,0,1,
1,1,1,1,1
  </data>
</layer>
```

**Base64编码的解析（Python）**：

```python
import base64
import zlib
import struct

def parse_layer_data(data_element):
    encoding = data_element.get('encoding')
    compression = data_element.get('compression')

    raw = data_element.text.strip()

    if encoding == 'base64':
        binary = base64.b64decode(raw)
        if compression == 'zlib':
            binary = zlib.decompress(binary)
        elif compression == 'gzip':
            import gzip
            binary = gzip.decompress(binary)
        # 解析为32位无符号整数数组
        gids = struct.unpack(f'<{len(binary)//4}I', binary)
        return gids
    elif encoding == 'csv':
        return [int(x) for x in raw.split(',')]
    else:
        raise ValueError(f"Unknown encoding: {encoding}")

# 使用
# gids = parse_layer_data(layer.find('data'))
# for y in range(height):
#     for x in range(width):
#         gid = gids[y * width + x]
#         if gid == 0: continue  # 空
#         real_gid, h, v, d = parse_gid(gid)
#         tileset, local_id = find_tileset(real_gid)
#         draw_tile(tileset, local_id, x, y, h, v, d)
```

**TSX文件结构**：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<tileset version="1.9" tiledversion="1.9.2"
         name="overworld" tilewidth="32" tileheight="32"
         tilecount="64" columns="8">

  <image source="overworld.png" width="256" height="256"/>

  <terraintypes>
    <terrain name="Grass" tile="0"/>
    <terrain name="Water" tile="15"/>
  </terraintypes>

  <tile id="0" terrain="0,0,0,0">
    <properties>
      <property name="solid" type="bool" value="false"/>
    </properties>
  </tile>

  <tile id="10" terrain="1,1,1,1">
    <properties>
      <property name="solid" type="bool" value="true"/>
      <property name="type" type="string" value="water"/>
    </properties>
    <animation>
      <frame tileid="10" duration="200"/>
      <frame tileid="11" duration="200"/>
    </animation>
  </tile>
</tileset>
```

**GID到瓦片的映射**：

```python
class Tileset:
    def __init__(self, firstgid, tsx_data):
        self.firstgid = firstgid
        self.tilecount = tsx_data.tilecount
        self.lastgid = firstgid + tsx_data.tilecount - 1

    def contains(self, gid):
        return self.firstgid <= gid <= self.lastgid

    def local_id(self, gid):
        return gid - self.firstgid

    def image_rect(self, gid):
        local = self.local_id(gid)
        col = local % self.columns
        row = local // self.columns
        return (col * self.tilewidth, row * self.tileheight,
                self.tilewidth, self.tileheight)
```

#### 总结

- **TMX结构**：map → properties → tileset → layer/objectgroup/group
- **编码压缩**：CSV最易读，Base64+zlib最小
- **GID位标志**：高3位存翻转标志，低28位存瓦片ID
- **TSX结构**：tileset → image → terraintypes → tile（含属性和动画）
- **GID映射**：先剥离标志，再用firstgid定位瓦片集和本地ID
- **常见坑**：忘记剥离翻转标志导致瓦片ID错误；编码格式不匹配导致解析失败；无限地图的chunk数据需特殊处理

---

### 第17讲 导出JSON与CSV

#### 概念

除了原生TMX，Tiled支持导出为JSON、CSV、Lua、Python等多种格式。JSON格式尤其重要——几乎所有游戏引擎都能解析JSON，且比XML更轻量。本讲学习各格式的导出与特点。

#### 原理

**导出格式对比**：

| 格式 | 优点 | 缺点 | 适用场景 |
|------|------|------|----------|
| TMX (XML) | Tiled原生，功能完整 | 文件较大，解析稍慢 | Tiled编辑 |
| JSON | 轻量，易解析，通用 | 无XML的schema验证 | 游戏集成首选 |
| CSV | 极简，仅瓦片数据 | 无图层/对象信息 | 简单瓦片地图 |
| Lua | 可直接require | 仅Lua引擎用 | LÖVE2D等 |
| Python | 可直接import | 仅Python用 | Python游戏 |

**JSON导出的两种模式**：

1. **嵌入式（Embedded）**：瓦片集数据嵌入JSON，单文件
2. **分离式（External）**：瓦片集单独.tsx/.json文件，多文件

**JSON结构**：

```json
{
  "compressionlevel": -1,
  "width": 30,
  "height": 20,
  "tilewidth": 32,
  "tileheight": 32,
  "orientation": "orthogonal",
  "renderorder": "right-down",
  "tilesets": [...],
  "layers": [...],
  "properties": {...}
}
```

#### 例子

**导出JSON**：

`File → Export As...` → 选择 `JSON map files (*.json)`

**导出的JSON示例**：

```json
{
  "compressionlevel": -1,
  "height": 20,
  "infinite": false,
  "layers": [
    {
      "data": [1, 1, 1, 1, 1, 1, 0, 0, 0, 1],
      "height": 20,
      "id": 1,
      "name": "Ground",
      "opacity": 1,
      "type": "tilelayer",
      "visible": true,
      "width": 30,
      "x": 0,
      "y": 0
    },
    {
      "draworder": "topdown",
      "id": 3,
      "name": "Collision",
      "objects": [
        {
          "height": 32,
          "id": 1,
          "name": "wall_01",
          "properties": [
            {"name": "solid", "type": "bool", "value": true}
          ],
          "type": "solid",
          "visible": true,
          "width": 32,
          "x": 96,
          "y": 64
        }
      ],
      "opacity": 1,
      "type": "objectgroup",
      "visible": true,
      "x": 0,
      "y": 0
    }
  ],
  "orientation": "orthogonal",
  "renderorder": "right-down",
  "tileheight": 32,
  "tilesets": [
    {
      "columns": 8,
      "firstgid": 1,
      "image": "overworld.png",
      "imageheight": 256,
      "imagewidth": 256,
      "name": "overworld",
      "tilecount": 64,
      "tileheight": 32,
      "tilewidth": 32
    }
  ],
  "tilewidth": 32,
  "type": "map",
  "width": 30
}
```

**Python解析JSON地图**：

```python
import json

class TiledMap:
    def __init__(self, json_path):
        with open(json_path) as f:
            self.data = json.load(f)
        self.width = self.data['width']
        self.height = self.data['height']
        self.tilewidth = self.data['tilewidth']
        self.tileheight = self.data['tileheight']
        self.tilesets = self._parse_tilesets()
        self.layers = self._parse_layers()

    def _parse_tilesets(self):
        tilesets = []
        for ts in self.data['tilesets']:
            tilesets.append({
                'firstgid': ts['firstgid'],
                'name': ts['name'],
                'image': ts['image'],
                'tilecount': ts['tilecount'],
                'columns': ts['columns'],
                'tilewidth': ts['tilewidth'],
                'tileheight': ts['tileheight'],
            })
        return tilesets

    def _parse_layers(self):
        layers = []
        for layer in self.data['layers']:
            if layer['type'] == 'tilelayer':
                layers.append(self._parse_tile_layer(layer))
            elif layer['type'] == 'objectgroup':
                layers.append(self._parse_object_layer(layer))
        return layers

    def _parse_tile_layer(self, layer):
        return {
            'name': layer['name'],
            'type': 'tilelayer',
            'width': layer['width'],
            'height': layer['height'],
            'data': layer['data'],
            'visible': layer['visible'],
            'opacity': layer['opacity']
        }

    def _parse_object_layer(self, layer):
        return {
            'name': layer['name'],
            'type': 'objectgroup',
            'objects': layer['objects'],
            'color': layer.get('color', '#ffffff')
        }

    def get_tile_at(self, layer_name, x, y):
        for layer in self.layers:
            if layer['name'] == layer_name and layer['type'] == 'tilelayer':
                idx = y * layer['width'] + x
                return layer['data'][idx]
        return 0

    def get_objects_by_type(self, type_name):
        result = []
        for layer in self.layers:
            if layer['type'] == 'objectgroup':
                for obj in layer['objects']:
                    if obj.get('type') == type_name:
                        result.append(obj)
        return result
```

**导出CSV**：

`File → Export As...` → 选择 `CSV map files (*.csv)`

注意：CSV导出**只导出第一个瓦片图层**，丢失对象层和其他图层信息。仅适合简单场景。

**批量导出**：

`File → Export As...` 可对每张地图导出。批量导出需用命令行（见第19讲）。

#### 总结

- **JSON格式**：游戏集成首选，轻量易解析
- **嵌入式 vs 分离式**：嵌入式单文件，分离式多文件复用
- **CSV局限**：仅瓦片数据，丢失对象信息
- **Python解析**：json模块直接load，结构清晰
- **批量导出**：需命令行自动化
- **常见坑**：JSON版本与Tiled版本不匹配导致字段差异；导出时忘记选择分离式导致文件臃肿；CSV丢失图层信息

---

### 第18讲 游戏引擎集成实战

#### 概念

本讲实战将Tiled地图集成到三种主流引擎：SDL2（C/C++）、Unity（C#）、Godot（GDScript）。掌握这些集成方法，你就能在任何引擎中使用Tiled地图。

#### 原理

**集成的核心步骤**：

1. **加载TMX/JSON**：解析地图文件
2. **加载瓦片集图片**：根据tileset的image路径
3. **渲染瓦片图层**：遍历data数组，按GID绘制瓦片
4. **解析对象层**：创建碰撞体、触发器、实体
5. **应用自定义属性**：根据属性配置游戏行为

**第三方库**：

不用从零写解析器，有成熟的库：
- **SDL2**：SDL2_tmx（C）、TMXLoader（C++）
- **Unity**：Tiled2Unity、SuperTiled2Unity、UnityTiled
- **Godot**：Godot Tiled Importer（官方插件）
- **LibGDX**：gdx-tiled
- **Pygame**：pytmx

#### 例子

**1. SDL2 + C语言集成（简化版）**：

```c
// tiled_loader.h
#ifndef TILED_LOADER_H
#define TILED_LOADER_H

#include <SDL2/SDL.h>
#include <SDL2/SDL_image.h>

typedef struct {
    int firstgid;
    int tilecount;
    int columns;
    int tilewidth, tileheight;
    char image_path[256];
    SDL_Texture* texture;
    int image_width, image_height;
} TilesetInfo;

typedef struct {
    char name[32];
    int width, height;
    int* data;  // GID数组
    int visible;
    float opacity;
} TileLayerInfo;

typedef struct {
    int width, height;
    int tilewidth, tileheight;
    TilesetInfo tilesets[8];
    int tileset_count;
    TileLayerInfo layers[16];
    int layer_count;
} TiledMap;

TiledMap* tiled_load(const char* tmx_path, SDL_Renderer* r);
void tiled_free(TiledMap* map);
void tiled_draw_layer(TiledMap* map, SDL_Renderer* r, int layer_index,
                      int camera_x, int camera_y);
Uint32 tiled_get_gid(TiledMap* map, int layer_index, int tx, int ty);

#endif
```

```c
// tiled_loader.c (简化，实际需XML解析库如libxml2或miniXML)
#include "tiled_loader.h"
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

TiledMap* tiled_load(const char* tmx_path, SDL_Renderer* r) {
    // 实际需XML解析，这里简化
    TiledMap* map = calloc(1, sizeof(TiledMap));

    // 解析TMX（伪代码）
    // parse_xml(tmx_path, map);

    // 加载瓦片集图片
    for (int i = 0; i < map->tileset_count; i++) {
        TilesetInfo* ts = &map->tilesets[i];
        ts->texture = IMG_LoadTexture(r, ts->image_path);
    }

    return map;
}

void tiled_draw_layer(TiledMap* map, SDL_Renderer* r, int layer_index,
                      int camera_x, int camera_y) {
    TileLayerInfo* layer = &map->layers[layer_index];
    if (!layer->visible) return;

    // 视锥剔除
    int start_tx = camera_x / map->tilewidth;
    int start_ty = camera_y / map->tileheight;
    int end_tx = (camera_x + 800) / map->tilewidth + 1;
    int end_ty = (camera_y + 600) / map->tileheight + 1;

    for (int ty = start_ty; ty < end_ty && ty < layer->height; ty++) {
        for (int tx = start_tx; tx < end_tx && tx < layer->width; tx++) {
            if (tx < 0 || ty < 0) continue;
            Uint32 gid = layer->data[ty * layer->width + tx];
            if (gid == 0) continue;

            // 剥离翻转标志
            int h_flip = gid & 0x80000000;
            int v_flip = gid & 0x40000000;
            int real_gid = gid & 0x1FFFFFFF;

            // 找到瓦片集
            TilesetInfo* ts = NULL;
            for (int i = map->tileset_count - 1; i >= 0; i--) {
                if (real_gid >= map->tilesets[i].firstgid) {
                    ts = &map->tilesets[i];
                    break;
                }
            }
            if (!ts) continue;

            int local_id = real_gid - ts->firstgid;
            int col = local_id % ts->columns;
            int row = local_id / ts->columns;

            SDL_Rect src = {
                col * ts->tilewidth, row * ts->tileheight,
                ts->tilewidth, ts->tileheight
            };
            SDL_Rect dst = {
                tx * map->tilewidth - camera_x,
                ty * map->tileheight - camera_y,
                map->tilewidth, map->tileheight
            };

            SDL_RendererFlip flip = SDL_FLIP_NONE;
            if (h_flip) flip |= SDL_FLIP_HORIZONTAL;
            if (v_flip) flip |= SDL_FLIP_VERTICAL;

            SDL_RenderCopyEx(r, ts->texture, &src, &dst, 0, NULL, flip);
        }
    }
}
```

**2. Unity + C#集成（使用SuperTiled2Unity）**：

安装SuperTiled2Unity后，直接把.tmx文件拖入Unity的Assets文件夹，自动生成Prefab。

```csharp
// 读取Tiled对象层，生成Unity碰撞体
using UnityEngine;
using SuperTiled2Unity;

public class TiledCollisionLoader : MonoBehaviour
{
    void Start()
    {
        // 查找所有标记为solid的对象
        SuperObject[] objects = FindObjectsOfType<SuperObject>();
        foreach (var obj in objects)
        {
            if (obj.m_Type == "solid")
            {
                // 添加BoxCollider2D
                BoxCollider2D collider = obj.gameObject.AddComponent<BoxCollider2D>();
                collider.size = new Vector2(obj.m_Width / 32f, obj.m_Height / 32f);
                collider.offset = new Vector2(collider.size.x / 2, -collider.size.y / 2);
            }
        }
    }
}
```

**3. Godot + GDScript集成**：

Godot有官方Tiled导入器插件：`Asset Library → Search "Tiled"`。

```gdscript
# Godot读取Tiled地图（导入后）
extends Node2D

func _ready():
    # Tiled地图导入后成为TileMap节点
    var tilemap = $GroundTileMap

    # 读取对象层
    var object_layer = $CollisionObjects
    for obj in object_layer.get_children():
        if obj.get("type") == "solid":
            var collider = StaticBody2D.new()
            var shape = RectangleShape2D.new()
            shape.extents = Vector2(obj.size.x / 2, obj.size.y / 2)
            collider.add_child(CollisionShape2D.new())
            collider.get_child(0).shape = shape
            collider.position = obj.position + Vector2(obj.size.x / 2, obj.size.y / 2)
            add_child(collider)

    # 读取自定义属性
    var bgm = tilemap.get_meta("bgm")
    if bgm:
        $AudioStreamPlayer.stream = load(bgm)
        $AudioStreamPlayer.play()
```

**4. Python + Pygame集成（使用pytmx）**：

```python
import pygame
from pytmx import load_pygame

class TiledGame:
    def __init__(self):
        pygame.init()
        self.screen = pygame.display.set_mode((800, 600))
        self.tmx_data = load_pygame('maps/overworld.tmx')

    def render(self, camera_x, camera_y):
        for layer in self.tmx_data.visible_layers:
            if hasattr(layer, 'data'):  # 瓦片图层
                for x, y, gid in layer:
                    tile = self.tmx_data.get_tile_image_by_gid(gid)
                    if tile:
                        screen_x = x * self.tmx_data.tilewidth - camera_x
                        screen_y = y * self.tmx_data.tileheight - camera_y
                        self.screen.blit(tile, (screen_x, screen_y))

            elif hasattr(layer, 'objects'):  # 对象层
                for obj in layer:
                    if obj.type == 'solid':
                        rect = pygame.Rect(
                            obj.x - camera_x, obj.y - camera_y,
                            obj.width, obj.height
                        )
                        pygame.draw.rect(self.screen, (255, 0, 0), rect, 2)
```

#### 总结

- **集成五步**：加载文件→加载图片→渲染瓦片→解析对象→应用属性
- **第三方库**：不要从零写，用成熟库（SDL2_tmx、SuperTiled2Unity、pytmx）
- **GID处理**：剥离高3位翻转标志再使用
- **视锥剔除**：只绘制摄像机可见的瓦片
- **对象层**：转换为引擎的碰撞体、触发器、实体
- **常见坑**：瓦片集firstgid顺序错误导致瓦片错乱；翻转标志未处理导致瓦片方向错；对象坐标系与引擎坐标系不一致（Tiled y向下，Unity y向上）

---

## 第七章 高级与实战

> 掌握自动化与完整项目流程，从Tiled用户进阶为Tiled专家。本章学习命令行自动化，并完成一个完整的游戏地图项目。

### 第19讲 命令行与自动化

#### 概念

Tiled不仅是一个GUI工具，还提供**命令行接口（CLI）**，支持脚本化操作：批量导出、格式转换、自动化构建。这是大型项目和CI/CD流水线必备能力。

#### 原理

**Tiled命令行基础**：

```
tiled --export-map [options] <input.tmx> <output>
tiled --export-tileset [options] <input.tsx> <output>
```

**常用命令**：

| 命令 | 用途 |
|------|------|
| --export-map | 导出地图为其他格式 |
| --export-tileset | 导出瓦片集为其他格式 |
| --export-json | 导出为JSON（等价于--export-map fmt=json） |
| --check-changes | 检查地图是否有未保存修改 |
| --version | 显示版本 |

**导出格式选项**：

```
--export-map --json-format=1.6 input.tmx output.json
```

格式选项：
- `--json-format`：JSON格式版本（1.0/1.1/1.2/1.3/1.4/1.5/1.6）
- `--csv`：CSV格式
- `--lua`：Lua格式
- `--python`：Python格式

**批量处理思路**：

用shell脚本遍历所有.tmx文件，逐个导出：

```bash
for tmx in maps/*.tmx; do
    tiled --export-map "$tmx" "${tmx%.tmx}.json"
done
```

#### 例子

**1. 单个地图导出JSON**：

```bash
# Windows
tiled.exe --export-map --json-format=1.6 maps\overworld.tmx build\overworld.json

# macOS/Linux
tiled --export-map --json-format=1.6 maps/overworld.tmx build/overworld.json
```

**2. 批量导出所有地图为JSON**：

```bash
#!/bin/bash
# export_all_maps.sh

mkdir -p build/maps

for tmx in maps/*.tmx; do
    name=$(basename "$tmx" .tmx)
    echo "Exporting $name..."
    tiled --export-map --json-format=1.6 "$tmx" "build/maps/$name.json"
done

echo "All maps exported."
```

**3. 批量导出瓦片集**：

```bash
#!/bin/bash
mkdir -p build/tilesets

for tsx in tilesets/*.tsx; do
    name=$(basename "$tsx" .tsx)
    tiled --export-tileset "$tsx" "build/tilesets/$name.json"
done
```

**4. Python自动化脚本**：

```python
#!/usr/bin/env python3
"""批量导出Tiled地图为JSON，并验证完整性"""
import subprocess
import os
import json
import sys
from pathlib import Path

TILED_CMD = "tiled"  # Windows可能需要完整路径
MAPS_DIR = "maps"
BUILD_DIR = "build/maps"

def export_map(tmx_path, output_path):
    """导出单个地图"""
    cmd = [
        TILED_CMD,
        "--export-map",
        "--json-format=1.6",
        str(tmx_path),
        str(output_path)
    ]
    result = subprocess.run(cmd, capture_output=True, text=True)
    if result.returncode != 0:
        print(f"Error exporting {tmx_path}: {result.stderr}")
        return False
    return True

def validate_json(json_path):
    """验证导出的JSON是否有效"""
    try:
        with open(json_path) as f:
            data = json.load(f)
        # 检查必要字段
        required = ['width', 'height', 'tilewidth', 'tileheight',
                    'tilesets', 'layers']
        for field in required:
            if field not in data:
                print(f"Missing field '{field}' in {json_path}")
                return False
        return True
    except Exception as e:
        print(f"Invalid JSON {json_path}: {e}")
        return False

def main():
    os.makedirs(BUILD_DIR, exist_ok=True)

    tmx_files = list(Path(MAPS_DIR).glob("*.tmx"))
    if not tmx_files:
        print("No .tmx files found!")
        sys.exit(1)

    success = 0
    failed = 0

    for tmx in tmx_files:
        output = Path(BUILD_DIR) / f"{tmx.stem}.json"
        print(f"Exporting {tmx.name}...")

        if export_map(tmx, output) and validate_json(output):
            print(f"  OK: {output}")
            success += 1
        else:
            print(f"  FAILED: {tmx}")
            failed += 1

    print(f"\nDone: {success} success, {failed} failed")
    sys.exit(0 if failed == 0 else 1)

if __name__ == "__main__":
    main()
```

**5. Makefile集成**：

```makefile
# Makefile
TILED = tiled
MAPS_DIR = maps
BUILD_DIR = build

TMX_FILES = $(wildcard $(MAPS_DIR)/*.tmx)
JSON_FILES = $(patsubst $(MAPS_DIR)/%.tmx,$(BUILD_DIR)/maps/%.json,$(TMX_FILES))

.PHONY: all clean maps tilesets

all: maps tilesets

maps: $(JSON_FILES)

$(BUILD_DIR)/maps/%.json: $(MAPS_DIR)/%.tmx | $(BUILD_DIR)/maps
        $(TILED) --export-map --json-format=1.6 $< $@

tilesets:
        @mkdir -p $(BUILD_DIR)/tilesets
        @for tsx in tilesets/*.tsx; do \
                name=$$(basename $$tsx .tsx); \
                $(TILED) --export-tileset $$tsx $(BUILD_DIR)/tilesets/$$name.json; \
        done

$(BUILD_DIR)/maps:
        mkdir -p $@

clean:
        rm -rf $(BUILD_DIR)
```

**6. CI/CD集成（GitHub Actions）**：

```yaml
# .github/workflows/build-maps.yml
name: Build Maps

on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Install Tiled
        run: |
          sudo apt-get update
          sudo apt-get install -y tiled

      - name: Export maps
        run: make maps

      - name: Upload artifacts
        uses: actions/upload-artifact@v3
        with:
          name: maps
          path: build/maps/
```

#### 总结

- **命令行接口**：--export-map、--export-tileset
- **批量导出**：shell循环或Python脚本
- **JSON格式版本**：用--json-format指定，保证兼容性
- **Makefile集成**：自动化构建，依赖管理
- **CI/CD**：GitHub Actions等平台自动导出，保证一致性
- **常见坑**：Tiled未加入PATH导致命令找不到；JSON格式版本与游戏端库不匹配；CI环境无GUI需用--no-gui标志

---

### 第20讲 完整游戏地图实战

#### 概念

本讲整合前19讲所有知识，完成一个完整的类塞尔达游戏地图项目。从项目结构规划、瓦片集设计、地图绘制、对象配置到导出集成，走完整个流程。

#### 原理

**项目规划**：

```
zelda_game/
├── assets/
│   ├── tilesets/
│   │   ├── overworld.png      # 主世界瓦片图集
│   │   ├── overworld.tsx
│   │   ├── dungeon.png        # 地下城瓦片图集
│   │   ├── dungeon.tsx
│   │   └── characters.png     # 角色瓦片
│   ├── maps/
│   │   ├── overworld.tmx      # 主世界地图
│   │   ├── village.tmx        # 村庄
│   │   ├── dungeon1.tmx       # 地下城1
│   │   └── dungeon2.tmx       # 地下城2
│   ├── templates/
│   │   ├── chest.tx           # 宝箱模板
│   │   ├── slime.tx           # 史莱姆模板
│   │   └── portal.tx          # 传送门模板
│   ├── objecttypes.xml        # 对象类型定义
│   └── sounds/
├── build/
│   └── maps/                  # 导出的JSON
├── scripts/
│   └── export_maps.py
└── Makefile
```

**地图设计原则**：

1. **主世界**：开放，多区域，连接村庄和地下城
2. **村庄**：安全区，有NPC、商店
3. **地下城**：战斗区，有敌人、宝箱、BOSS

**对象类型规划**：

- `solid`：碰撞体
- `portal`：传送门
- `spawn`：出生点
- `npc`：NPC
- `enemy`：敌人
- `chest`：宝箱
- `trigger`：触发器

#### 例子

**步骤1：创建瓦片集**

`overworld.tsx`：
- 32×32瓦片，8×8网格
- 标注地形：草地、水、沙、山
- 配置Wang颜色集：用于道路、河流
- 为关键瓦片添加属性：solid、type、damage

**步骤2：定义对象类型**

`objecttypes.xml`：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<objecttypes>
  <objecttype name="solid" color="#ff0000">
    <property name="collision" type="bool" default="true"/>
  </objecttype>

  <objecttype name="portal" color="#0000ff">
    <property name="target_map" type="string" default=""/>
    <property name="target_x" type="int" default="0"/>
    <property name="target_y" type="int" default="0"/>
    <property name="direction" type="string" default="down"/>
  </objecttype>

  <objecttype name="spawn" color="#00ff00">
    <property name="entity" type="string" default="player"/>
    <property name="direction" type="string" default="down"/>
  </objecttype>

  <objecttype name="npc" color="#ffff00">
    <property name="dialog" type="string" default=""/>
    <property name="shop_id" type="int" default="0"/>
    <property name="quest_id" type="int" default="0"/>
  </objecttype>

  <objecttype name="enemy" color="#ff00ff">
    <property name="enemy_type" type="string" default="slime"/>
    <property name="hp" type="int" default="3"/>
    <property name="attack" type="int" default="1"/>
    <property name="detect_range" type="int" default="150"/>
  </objecttype>

  <objecttype name="chest" color="#ff8800">
    <property name="item_id" type="int" default="0"/>
    <property name="item_count" type="int" default="1"/>
    <property name="locked" type="bool" default="false"/>
    <property name="unlock_key" type="string" default=""/>
  </objecttype>

  <objecttype name="trigger" color="#00ffff">
    <property name="event_id" type="string" default=""/>
    <property name="one_shot" type="bool" default="true"/>
  </objecttype>
</objecttypes>
```

**步骤3：创建对象模板**

`templates/chest.tx`：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<template>
  <object type="chest" width="32" height="32" gid="65">
    <properties>
      <property name="item_id" type="int" value="0"/>
      <property name="item_count" type="int" value="1"/>
      <property name="locked" type="bool" value="false"/>
    </properties>
  </object>
</template>
```

`templates/slime.tx`：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<template>
  <object type="enemy" width="32" height="32" gid="70">
    <properties>
      <property name="enemy_type" type="string" value="slime"/>
      <property name="hp" type="int" value="3"/>
      <property name="attack" type="int" value="1"/>
      <property name="detect_range" type="int" value="150"/>
    </properties>
  </object>
</template>
```

**步骤4：绘制主世界地图**

`overworld.tmx`：
- 尺寸：100×80瓦片
- 图层（从下到上）：
  1. `Ground`：草地、路径（用Wang刷绘制道路）
  2. `Water`：水面（带动画瓦片）
  3. `Decor`：花、石头、灌木
  4. `Buildings`：房屋、桥梁
  5. `Overhead`：树冠、屋顶
  6. `Collision`（对象层）：碰撞体
  7. `Portals`（对象层）：传送门到村庄、地下城
  8. `Spawns`（对象层）：敌人、NPC、宝箱

**步骤5：配置传送门**

在`Portals`对象层放置矩形对象：
```
Name: to_village
Type: portal
Position: (480, 320)
Size: 32×32
Properties:
  target_map: village.tmx
  target_x: 100
  target_y: 200
  direction: down
```

**步骤6：放置敌人和宝箱**

从模板面板拖动`slime.tx`到地图，覆盖属性：
```
Name: slime_01
Template: templates/slime.tx
Position: (640, 480)
Override Properties:
  hp: 5  ← 比默认值强
```

**步骤7：地图属性**

设置地图级属性：
```
bgm: file = assets/sounds/overworld.ogg
weather: enum = "clear"
time_of_day: enum = "day"
player_spawn: object = (引用spawn对象)
```

**步骤8：导出与集成**

```bash
# 批量导出
python scripts/export_maps.py

# 或用Makefile
make maps
```

**步骤9：游戏端加载（Python示例）**：

```python
import json
import pygame

class GameMap:
    def __init__(self, json_path):
        with open(json_path) as f:
            self.data = json.load(f)
        self.tilesets = self._load_tilesets()
        self.collision_rects = []
        self.portals = []
        self.enemies = []
        self.chests = []
        self._parse_objects()

    def _load_tilesets(self):
        tilesets = []
        for ts in self.data['tilesets']:
            image = pygame.image.load(ts['image']).convert_alpha()
            tilesets.append({
                'firstgid': ts['firstgid'],
                'image': image,
                'columns': ts['columns'],
                'tilewidth': ts['tilewidth'],
                'tileheight': ts['tileheight']
            })
        return tilesets

    def _parse_objects(self):
        for layer in self.data['layers']:
            if layer['type'] != 'objectgroup':
                continue
            for obj in layer['objects']:
                obj_type = obj.get('type', '')
                props = {p['name']: p['value'] for p in obj.get('properties', [])}

                if obj_type == 'solid':
                    rect = pygame.Rect(obj['x'], obj['y'],
                                      obj['width'], obj['height'])
                    self.collision_rects.append(rect)

                elif obj_type == 'portal':
                    self.portals.append({
                        'rect': pygame.Rect(obj['x'], obj['y'],
                                           obj['width'], obj['height']),
                        'target_map': props.get('target_map'),
                        'target_x': props.get('target_x', 0),
                        'target_y': props.get('target_y', 0)
                    })

                elif obj_type == 'enemy':
                    self.enemies.append({
                        'x': obj['x'], 'y': obj['y'],
                        'type': props.get('enemy_type', 'slime'),
                        'hp': props.get('hp', 3),
                        'attack': props.get('attack', 1)
                    })

                elif obj_type == 'chest':
                    self.chests.append({
                        'x': obj['x'], 'y': obj['y'],
                        'item_id': props.get('item_id', 0),
                        'item_count': props.get('item_count', 1),
                        'locked': props.get('locked', False)
                    })

    def render(self, screen, camera_x, camera_y):
        for layer in self.data['layers']:
            if layer['type'] != 'tilelayer' or not layer.get('visible', True):
                continue
            self._render_tile_layer(screen, layer, camera_x, camera_y)

    def _render_tile_layer(self, screen, layer, camera_x, camera_y):
        tw = self.data['tilewidth']
        th = self.data['tileheight']

        start_tx = max(0, camera_x // tw)
        start_ty = max(0, camera_y // th)
        end_tx = min(layer['width'], (camera_x + 800) // tw + 1)
        end_ty = min(layer['height'], (camera_y + 600) // th + 1)

        for ty in range(start_ty, end_ty):
            for tx in range(start_tx, end_tx):
                gid = layer['data'][ty * layer['width'] + tx]
                if gid == 0:
                    continue

                # 剥离翻转标志
                real_gid = gid & 0x1FFFFFFF
                h_flip = bool(gid & 0x80000000)
                v_flip = bool(gid & 0x40000000)

                # 找瓦片集
                for ts in reversed(self.tilesets):
                    if real_gid >= ts['firstgid']:
                        break

                local_id = real_gid - ts['firstgid']
                col = local_id % ts['columns']
                row = local_id // ts['columns']

                src_rect = pygame.Rect(
                    col * ts['tilewidth'], row * ts['tileheight'],
                    ts['tilewidth'], ts['tileheight']
                )
                dst_pos = (tx * tw - camera_x, ty * th - camera_y)

                tile_image = ts['image'].subsurface(src_rect)
                if h_flip or v_flip:
                    tile_image = pygame.transform.flip(tile_image, h_flip, v_flip)
                screen.blit(tile_image, dst_pos)

# 使用
game_map = GameMap('build/maps/overworld.json')

# 渲染
game_map.render(screen, camera.x, camera.y)

# 碰撞检测
for rect in game_map.collision_rects:
    if player_rect.colliderect(rect):
        # 处理碰撞
        pass

# 传送门检测
for portal in game_map.portals:
    if player_rect.colliderect(portal['rect']):
        # 切换地图
        game_map = GameMap(f"build/maps/{portal['target_map']}")
        player.x = portal['target_x']
        player.y = portal['target_y']
```

#### 总结

- **项目结构**：tilesets/maps/templates分类清晰，build目录存导出文件
- **对象类型**：统一XML定义，团队共享
- **对象模板**：复用完整对象配置，提升效率
- **地图设计**：多图层分工，对象层承载游戏逻辑
- **导出集成**：命令行批量导出，游戏端解析JSON
- **完整流程**：规划→瓦片集→对象类型→模板→绘制→配置→导出→集成

---

## 课程结语

恭喜你完成了《Tiled地图编辑器实战教程》全部20讲的学习！

### 你已经掌握的核心能力

**第一章 入门基础**：搭建环境，理解Tiled界面，创建第一张地图

**第二章 瓦片集管理**：导入图集、配置动画、外部瓦片集共享

**第三章 图层系统**：瓦片图层、对象层、图层组织与可见性

**第四章 绘制工具**：基础工具、地形系统、王冠刷自动过渡

**第五章 对象与属性**：对象类型、自定义属性、对象模板复用

**第六章 导出与集成**：TMX/TSX/JSON格式、多引擎集成实战

**第七章 高级与实战**：命令行自动化、完整游戏地图项目

### 下一步进阶方向

1. **等距地图**：学习Isometric地图，制作RPG风格游戏
2. **六边形地图**：策略游戏常用的六边形瓦片
3. **自定义插件**：用JavaScript编写Tiled插件，扩展功能
4. **程序化生成**：结合Tiled和算法，自动生成地图
5. **多人协作**：Git管理.tmx文件，团队协作编辑
6. **性能优化**：大地图分块加载、视锥剔除

### 推荐资源

- **Tiled官方文档**：doc.mapeditor.org，最权威的参考
- **Tiled论坛**：discourse.mapeditor.org，社区交流
- **GitHub**：github.com/mapeditor/tiled，源码与Issue
- **示例项目**：研究OpenTTD、Shattered Pixel Dungeon等开源游戏的Tiled用法
- **LPC（Liberated Pixel Cup）**：免费精灵资源，适合练习

Tiled是2D游戏开发的瑞士军刀，掌握它你就能高效制作任何2D游戏地图。从这20讲出发，持续实践，加入自己的创意，你就能创造出独一无二的游戏世界。祝你地图编辑之旅愉快！

