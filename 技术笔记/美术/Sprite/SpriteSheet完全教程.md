# SpriteSheet 完全教程：从入门到实战

> 本教程以教科书形式系统讲解 SpriteSheet（精灵图集）技术，涵盖基础概念、制作工具、数据格式、渲染技术、动画系统、性能优化与实战应用，共 28 讲。

---

## 课程总览

**预计讲数**：28讲（7章）

**学习目标**：

1. 理解 SpriteSheet 的核心原理与在 2D 渲染中的地位
2. 掌握主流打包工具的使用与自动化工作流
3. 熟练解析 JSON / XML / plist 等元数据格式
4. 能够使用 Canvas、WebGL、CSS 三种方式渲染精灵
5. 设计完整的帧动画系统与状态机
6. 掌握性能优化与移动端适配策略
7. 独立完成游戏角色与 Web UI 的精灵动画项目

**适合人群**：前端开发者、游戏开发者、动画工程师、对 2D 渲染感兴趣的学习者

**前置知识**：基础 JavaScript、HTML/CSS、了解 Canvas 或 WebGL 基础

---

## 详细章节目录

### 第一章：SpriteSheet 基础概念（第1-4讲）
- 第1讲：什么是 SpriteSheet——概念、起源与应用场景
- 第2讲：SpriteSheet vs 单帧图片——优势对比与原理剖析
- 第3讲：SpriteSheet 的核心组成——图集与元数据
- 第4讲：SpriteSheet 的常见分类——网格型与自由型

### 第二章：制作工具与工作流（第5-8讲）
- 第5讲：TexturePacker 入门——主流打包工具详解
- 第6讲：ShoeBox 与免费替代方案
- 第7讲：自动化打包脚本——CLI 工具与构建集成
- 第8讲：从设计到打包的完整工作流

### 第三章：数据格式与解析（第9-12讲）
- 第9讲：JSON 格式详解——Hash 与 Array 两种结构
- 第10讲：XML 与 plist 格式解析
- 第11讲：解析器实现——从元数据到精灵帧
- 第12讲：跨格式兼容与适配层设计

### 第四章：核心渲染技术（第13-17讲）
- 第13讲：Canvas 基础渲染——drawImage 九参数法
- 第14讲：WebGL 纹理切割与 UV 映射
- 第15讲：CSS Sprite 背景定位技术
- 第16讲：九宫格与切片缩放（9-slice scaling）
- 第17讲：多图集管理与动态切换

### 第五章：动画系统（第18-21讲）
- 第18讲：帧动画原理——时间轴与帧率控制
- 第19讲：动画状态机设计
- 第20讲：混合动画与平滑过渡
- 第21讲：事件触发与音画同步

### 第六章：性能优化（第22-25讲）
- 第22讲：纹理内存与 GPU 优化
- 第23讲：批量渲染与 DrawCall 合并
- 第24讲：图集尺寸与压缩策略
- 第25讲：移动端适配与降级方案

### 第七章：实战应用（第26-28讲）
- 第26讲：游戏角色动画系统实战
- 第27讲：Web UI 精灵动画实战
- 第28讲：完整项目案例——从零到一

---

## 第一章：SpriteSheet 基础概念

### 第1讲：什么是 SpriteSheet——概念、起源与应用场景

#### 概念

SpriteSheet（精灵图集，又称雪碧图）是一种将多个小尺寸图像（精灵，Sprite）合并到一张大图中的技术。这张大图本身被称为"图集"（Atlas），而记录每个小图在大图中位置、尺寸等信息的附属数据被称为"元数据"（Metadata）。两者结合，构成了完整的 SpriteSheet 资源。在现代 2D 渲染管线中，SpriteSheet 是最基础也最重要的资源组织形式之一。

#### 原理

SpriteSheet 的核心思想是"空间换时间"。在 2D 渲染中，GPU 每次切换纹理都需要付出状态切换开销（texture binding cost）。如果每个精灵都是独立纹理，渲染 100 个精灵可能需要 100 次纹理绑定操作，每次都伴随驱动层的状态验证与 GPU 流水线刷新。而将所有精灵合并到一张大图后，GPU 只需绑定一次纹理，就能通过 UV 坐标偏移渲染出所有精灵，大幅降低 DrawCall 数量。

这一技术最早可追溯到 1980 年代的街机游戏。当时硬件内存极其有限，开发者将角色动画的每一帧紧密排列在一张图上，通过修改显示寄存器的偏移地址来切换帧。现代 GPU 虽然性能强大，但纹理切换开销依然存在，因此 SpriteSheet 仍是 2D 游戏和 Web 动画的标准实践。

#### 例子

最简单的 SpriteSheet 示意——一个 4 帧行走动画合并到一张图中：

```
原始单帧：
[帧1] [帧2] [帧3] [帧4]  ← 每帧 64×64 像素

合并后的 SpriteSheet（256×64）：
┌──────┬──────┬──────┬──────┐
│ 帧1  │ 帧2  │ 帧3  │ 帧4  │
└──────┴──────┴──────┴──────┘

对应的元数据（JSON）：
{
  "frames": {
    "walk_1": {"x":0,   "y":0, "w":64, "h":64},
    "walk_2": {"x":64,  "y":0, "w":64, "h":64},
    "walk_3": {"x":128, "y":0, "w":64, "h":64},
    "walk_4": {"x":192, "y":0, "w":64, "h":64}
  }
}
```

实际应用场景包括：游戏角色动画（行走、攻击、待机）、UI 图标合集、Web 加载动画、特效粒子序列、位图字体等。在 Phaser、PixiJS、Cocos2d 等引擎中，SpriteSheet 都是核心资源类型。

#### 总结

- SpriteSheet = 图集（大图） + 元数据（位置信息）
- 核心价值：减少纹理切换，降低 DrawCall
- 应用场景：游戏、Web 动画、UI、特效、字体
- 历史悠久，从街机时代延续至今，仍是 2D 渲染基石

---

### 第2讲：SpriteSheet vs 单帧图片——优势对比与原理剖析

#### 概念

单帧图片（Individual Frame）指每个动画帧或每个精灵作为独立的图片文件存储；SpriteSheet 则将它们合并为一张大图。本讲对比两者在性能、内存、加载、管理四个维度的差异，帮助理解为什么 SpriteSheet 成为行业标准。

#### 原理

**1. 纹理切换开销**：GPU 渲染时，每次切换当前绑定的纹理对象，都需要执行 `gl.bindTexture` 调用，这会触发驱动层的状态验证和流水线刷新。在 WebGL 中，纹理切换是性能杀手之一。单帧图片模式下，N 个精灵需要 N 次绑定；SpriteSheet 模式下，同一图集内的精灵只需 1 次绑定。

**2. 内存碎片**：每张独立纹理在 GPU 中都需要独立的内存分配，且通常需要 2 的幂次方对齐（如 64×64 的图片实际占用 64×64，但 48×48 的图片可能被填充到 64×64）。这导致大量内存浪费。SpriteSheet 通过紧凑排列，将多个小图共享一个 2 的幂次方大纹理，显著减少碎片。

**3. 网络请求**：浏览器加载 100 张小图需要 100 次 HTTP 请求（即使使用 HTTP/2 多路复用，仍有解析与解码开销）。SpriteSheet 只需 1 次请求，加载效率大幅提升。

**4. 文件管理**：100 个独立文件在版本控制、资源命名、团队协作中都更繁琐。SpriteSheet 将相关资源集中管理，更新与维护更便捷。

#### 例子

性能对比测试（渲染 500 个精灵）：

```javascript
// 方式一：单帧图片（500 次纹理绑定）
const images = [];
for (let i = 0; i < 500; i++) {
  images.push(loadImage(`sprite_${i}.png`)); // 500 个独立纹理
}
// 渲染时每个精灵切换纹理 → 约 500 次 DrawCall
// 实测帧率：约 30 FPS（中端设备）

// 方式二：SpriteSheet（1 次纹理绑定）
const atlas = loadImage('atlas.png');
const frames = loadJSON('atlas.json'); // 500 帧的元数据
// 渲染时所有精灵共享同一纹理 → 可批量合并为 1 次 DrawCall
// 实测帧率：约 60 FPS（同设备）
```

| 维度 | 单帧图片 | SpriteSheet |
|------|---------|-------------|
| DrawCall 数量 | N（每精灵一次） | 1（同图集合并） |
| 内存占用 | 高（碎片+对齐浪费） | 低（紧凑排列） |
| 网络请求 | N 次 | 1 次 |
| 文件管理 | 繁琐 | 集中 |
| 加载解码 | 逐个解码 | 一次解码 |

#### 总结

- SpriteSheet 在性能、内存、网络、管理四个维度全面优于单帧图片
- 核心原理：减少纹理切换 + 紧凑内存排列 + 批量网络请求
- 适用前提：精灵之间属于同一渲染批次（如同一角色的不同动画帧）
- 例外场景：超大背景图、独立 UI 元素可能不适合合并

---

### 第3讲：SpriteSheet 的核心组成——图集与元数据

#### 概念

一个完整的 SpriteSheet 资源由两部分组成：**图集**（Atlas Image）和**元数据**（Metadata）。图集是合并后的大图本身，元数据是描述每个精灵在图集中位置、尺寸、旋转等信息的结构化数据文件。两者缺一不可——只有图集无法定位精灵，只有元数据无法渲染像素。

#### 原理

**图集**是一张普通的位图（PNG / JPEG / WebP），其尺寸通常为 2 的幂次方（如 256×256、512×512、1024×1024、2048×2048），以适配 GPU 纹理对齐要求。图集内部通过"打包算法"（如矩形装箱算法 MaxRects）将各精灵紧凑排列，最大化空间利用率。

**元数据**通常以 JSON / XML / plist 等文本格式存储，记录每个精灵的以下信息：

- `x, y`：精灵在图集中的左上角坐标
- `w, h`：精灵的宽高
- `frame`：实际像素区域（可能含旋转标记）
- `sourceSize`：原始尺寸（精灵可能被裁剪了透明边距）
- `spriteSourceSize`：裁剪后区域在原始尺寸中的偏移
- `rotated`：是否被旋转 90° 以节省空间

这些字段共同描述了"如何从大图中精确切出某个精灵"的全部信息。

#### 例子

TexturePacker 导出的标准 JSON 格式：

```json
{
  "frames": {
    "hero_idle.png": {
      "frame": {"x": 0, "y": 0, "w": 64, "h": 96},
      "rotated": false,
      "trimmed": true,
      "spriteSourceSize": {"x": 16, "y": 0, "w": 64, "h": 96},
      "sourceSize": {"w": 96, "h": 96}
    },
    "hero_walk1.png": {
      "frame": {"x": 64, "y": 0, "w": 70, "h": 95},
      "rotated": true,
      "trimmed": true,
      "spriteSourceSize": {"x": 10, "y": 1, "w": 70, "h": 95},
      "sourceSize": {"w": 96, "h": 96}
    }
  },
  "meta": {
    "image": "hero.png",
    "size": {"w": 256, "h": 256},
    "scale": 1
  }
}
```

字段解读：
- `hero_idle.png` 被裁剪了透明边距（trimmed=true），原始 96×96，实际内容 64×96，偏移 (16, 0)
- `hero_walk1.png` 被旋转了 90°（rotated=true），渲染时需反向旋转还原

#### 总结

- SpriteSheet = 图集（位图） + 元数据（结构化文本）
- 图集尺寸通常为 2 的幂次方，通过装箱算法紧凑排列
- 元数据记录坐标、尺寸、旋转、裁剪等完整信息
- 理解 `frame` / `sourceSize` / `spriteSourceSize` / `rotated` 四个核心字段是解析的基础

---

### 第4讲：SpriteSheet 的常见分类——网格型与自由型

#### 概念

根据精灵在图集中的排列方式，SpriteSheet 可分为两大类：**网格型**（Grid-based / Uniform）和**自由型**（Free / Packed）。网格型将所有精灵按统一尺寸的网格排列，无需元数据文件；自由型则根据每个精灵的实际尺寸紧凑排列，必须依赖元数据定位。两者各有适用场景。

#### 原理

**网格型 SpriteSheet** 假设所有精灵尺寸相同，按行列网格排列。定位第 N 个精灵只需简单数学计算：`x = (N % cols) * frameWidth`，`y = Math.floor(N / cols) * frameHeight`。优点是无需元数据文件，实现简单；缺点是空间利用率低（大精灵周围有空白），无法处理尺寸不一的精灵。

**自由型 SpriteSheet** 使用装箱算法（如 MaxRects、Shelf、Guillotine）将不同尺寸的精灵紧凑排列，最大化空间利用率。每个精灵的位置由元数据记录，支持裁剪透明边距和旋转优化。优点是空间利用率高（通常可达 90% 以上）、灵活；缺点是必须依赖元数据文件，解析稍复杂。

选择依据：如果所有精灵尺寸统一（如等帧动画），网格型更简单；如果精灵尺寸不一（如 UI 控件、混合资源），自由型更高效。

#### 例子

网格型 SpriteSheet 的定位计算：

```javascript
// 网格型：8列×4行，每帧 64×64，共 32 帧
const cols = 8;
const frameW = 64;
const frameH = 64;

function getFrameRect(frameIndex) {
  const col = frameIndex % cols;
  const row = Math.floor(frameIndex / cols);
  return {
    x: col * frameW,
    y: row * frameH,
    w: frameW,
    h: frameH
  };
}

// 获取第 10 帧的位置 → {x: 128, y: 64, w: 64, h: 64}
console.log(getFrameRect(10));
```

自由型 SpriteSheet 的定位（依赖元数据）：

```javascript
// 自由型：从 JSON 元数据读取
const metadata = {
  "button_normal": {"x": 12, "y": 8, "w": 80, "h": 32},
  "button_hover":  {"x": 100, "y": 8, "w": 80, "h": 32},
  "icon_star":     {"x": 12, "y": 50, "w": 24, "h": 24},
  "icon_heart":    {"x": 40, "y": 50, "w": 28, "h": 26}
};

function getFrame(name) {
  return metadata[name]; // 直接查表，位置无规律
}
```

对比示意：

```
网格型（整齐但有空隙）：        自由型（紧凑无空隙）：
┌──┬──┬──┬──┐                ┌────┬──┬───┐
│  │  │  │  │                │大图│小│中图│
├──┼──┼──┼──┤                ├──┬─┴──┤   │
│  │  │  │  │                │小│ 中 │   │
├──┼──┼──┼──┤                ├──┴───┴───┤
│  │  │  │  │                │  小图小图 │
└──┴──┴──┴──┘                └──────────┘
```

#### 总结

- 网格型：统一尺寸、数学定位、无需元数据、空间利用率低
- 自由型：任意尺寸、元数据定位、空间利用率高、需解析
- 等帧动画选网格型，混合资源选自由型
- 主流工具（TexturePacker 等）默认输出自由型，兼顾效率与灵活性

---

## 第二章：制作工具与工作流

### 第5讲：TexturePacker 入门——主流打包工具详解

#### 概念

TexturePacker 是 CodeAndWeb 公司开发的跨平台 SpriteSheet 打包工具，是游戏与前端行业使用最广泛的商业方案。它支持 PNG、JPEG、WebP 等输入格式，可输出 JSON、XML、plist、Unity 等多种元数据格式，并内置裁剪、旋转、多图集分割、压缩等高级功能。理解 TexturePacker 的使用是 SpriteSheet 工作流的起点。

#### 原理

TexturePacker 的核心是 **MaxRects 装箱算法**。该算法将每个精灵视为一个矩形，通过启发式策略（如 BestShortSideFit、BottomLeft）将矩形紧密排列到给定的大图区域中，最大化空间利用率。算法还支持以下优化：

- **Trim（裁剪）**：自动移除精灵四周的透明像素，减小实际占用面积
- **Rotate（旋转）**：将某些精灵旋转 90° 以更好地填充空隙
- **Extrude（边缘扩展）**：将边缘像素向外扩展 1-2 像素，防止采样时出现相邻精灵的"渗色"伪影
- **Multipack（多图集）**：当精灵总量超过单图集容量时，自动分割为多张图集

这些优化使得 TexturePacker 输出的图集空间利用率通常可达 85%-95%。

#### 例子

**GUI 操作流程**：
1. 将精灵图片文件夹拖入 TexturePacker 左侧面板
2. 右侧设置输出格式（如 Phaser JSON Hash）
3. 调整参数：最大尺寸 2048×2048、裁剪开启、旋转开启
4. 点击"Publish"生成 `atlas.png` + `atlas.json`

**关键参数配置**（TexturePacker 的 `.tps` 配置文件）：

```json
{
  "maxTextureSize": {"width": 2048, "height": 2048},
  "trimMode": "trim",
  "rotateSprites": true,
  "extrude": 1,
  "multipack": true,
  "dataFormat": "phaser",
  "textureFileName": "output/atlas.png",
  "dataFileName": "output/atlas.json"
}
```

**输出结果**：一张紧凑排列的 PNG 图集 + 一份 JSON 元数据文件，可直接被 Phaser / PixiJS 等引擎加载使用。

#### 总结

- TexturePacker 是行业标准打包工具，支持多格式输入输出
- 核心算法 MaxRects 实现高空间利用率的矩形装箱
- 关键优化：Trim（裁剪）、Rotate（旋转）、Extrude（防渗色）、Multipack（多图集）
- GUI 操作直观，适合设计与美术人员使用
- 商业授权，但免费版功能已足够学习使用（限制部分高级格式）

---

### 第6讲：ShoeBox 与免费替代方案

#### 概念

ShoeBox 是一款免费的 Adobe AIR 桌面应用，提供 SpriteSheet 打包、拆包、裁剪等实用功能，适合预算有限的独立开发者。除 ShoeBox 外，还有多种免费替代方案：在线工具（如 Leshy SpriteSheet Tool）、开源命令行工具（如 TexturePacker CLI 免费版、spritesheet-js）、以及直接使用图像处理库（如 Python PIL / Node sharp）自行编写打包脚本。本讲介绍这些工具的特点与适用场景。

#### 原理

免费工具的打包算法通常较简单（如 Shelf 货架算法或基础网格排列），空间利用率略低于 TexturePacker 的 MaxRects，但对中小型项目已足够。在线工具的优势是无需安装、即开即用；命令行工具的优势是可集成到自动化构建流程；自写脚本的优势是完全可控、可定制。

选择原则：学习阶段用在线工具快速体验；小型项目用 ShoeBox 或 spritesheet-js；中大型项目或需要精细控制时投资 TexturePacker 或自写 MaxRects 实现。

#### 例子

**方案一：spritesheet-js（Node.js 命令行工具）**

```bash
# 安装
npm install -g spritesheet-js

# 使用：将当前目录所有 PNG 打包为 Phaser 格式
spritesheet-js --format=phaser --padding=2 *.png
# 输出：spritesheet.png + spritesheet.json
```

**方案二：Python 自写简易打包脚本（网格型）**

```python
from PIL import Image
import os, json

# 读取所有精灵
sprites = sorted([f for f in os.listdir('.') if f.endswith('.png')])
images = [Image.open(f) for f in sprites]

# 网格排列：4列
cols = 4
rows = (len(images) + cols - 1) // cols
w, h = images[0].size  # 假设等尺寸
atlas = Image.new('RGBA', (cols * w, rows * h), (0, 0, 0, 0))

frames = {}
for i, (name, img) in enumerate(zip(sprites, images)):
    col, row = i % cols, i // cols
    atlas.paste(img, (col * w, row * h))
    frames[name] = {"x": col * w, "y": row * h, "w": w, "h": h}

atlas.save('atlas.png')
with open('atlas.json', 'w') as f:
    json.dump({"frames": frames}, f, indent=2)
print("打包完成：atlas.png + atlas.json")
```

**方案三：在线工具 Leshy SpriteSheet Tool**
- 访问 `https://www.leshylabs.com/apps/sstool/`
- 拖入图片，选择格式，下载结果
- 适合快速原型，无需安装任何软件

#### 总结

- ShoeBox：免费桌面工具，功能实用但更新缓慢
- 在线工具：零安装，适合学习与快速原型
- spritesheet-js：Node 命令行，可集成构建
- 自写脚本：完全可控，适合定制需求
- 免费方案算法较简单，空间利用率略低，但中小项目足够

---

### 第7讲：自动化打包脚本——CLI 工具与构建集成

#### 概念

在现代前端与游戏开发中，资源打包应集成到自动化构建流程中，而非手动操作 GUI。本讲讲解如何使用 TexturePacker CLI、spritesheet-js 等命令行工具，将 SpriteSheet 打包集成到 npm scripts、Webpack、Vite、Gulp 等构建系统中，实现"修改源图 → 自动重新打包"的开发体验。

#### 原理

自动化打包的核心是**文件监听**与**增量构建**。构建工具监听源图目录的变化，当检测到文件新增、修改、删除时，自动触发打包命令重新生成图集。这确保了图集始终与源图同步，避免"改了源图忘记重新打包"的人为错误。

集成方式通常有两种：
1. **npm scripts**：在 `package.json` 中定义打包命令，配合 `--watch` 参数监听
2. **构建插件**：编写 Webpack/Vite 插件，在构建管线中自动调用打包工具

前者简单直接，后者集成度更高、可与其他资源处理联动。

#### 例子

**方案一：npm scripts 集成 TexturePacker CLI**

```json
// package.json
{
  "scripts": {
    "pack:sprites": "TexturePacker --format phaser --data dist/atlas.json --sheet dist/atlas.png --trim-mode trim --rotate-sprites src/sprites/",
    "pack:watch": "TexturePacker --format phaser --data dist/atlas.json --sheet dist/atlas.png --watch src/sprites/",
    "build": "npm run pack:sprites && vite build",
    "dev": "npm run pack:watch & vite"
  }
}
```

**方案二：Vite 插件集成 spritesheet-js**

```javascript
// vite.config.js
import { defineConfig } from 'vite';
import spritesheet from 'spritesheet-js';
import { resolve } from 'path';
import fs from 'fs';

function spriteSheetPlugin() {
  return {
    name: 'spritesheet-generator',
    buildStart() {
      const inputDir = resolve(__dirname, 'src/sprites');
      const files = fs.readdirSync(inputDir).filter(f => f.endsWith('.png'));
      if (files.length === 0) return;

      return new Promise((resolvePromise) => {
        spritesheet(files.map(f => resolve(inputDir, f)), {
          format: 'pixi.js',
          path: resolve(__dirname, 'dist/assets'),
          name: 'atlas'
        }, (err) => {
          if (err) console.error('打包失败:', err);
          else console.log('SpriteSheet 打包完成');
          resolvePromise();
        });
      });
    }
  };
}

export default defineConfig({
  plugins: [spriteSheetPlugin()]
});
```

**方案三：Gulp 任务**

```javascript
// gulpfile.js
const gulp = require('gulp');
const exec = require('child_process').exec;

gulp.task('pack', (cb) => {
  exec('TexturePacker --format json --data dist/atlas.json --sheet dist/atlas.png src/sprites/', (err) => {
    if (err) console.error(err);
    cb();
  });
});

gulp.task('watch', () => {
  gulp.watch('src/sprites/**/*.png', gulp.series('pack'));
});
```

#### 总结

- 自动化打包消除人为遗漏，确保图集与源图同步
- npm scripts 方案最简单，适合中小项目
- 构建插件方案集成度高，适合大型项目
- 关键能力：文件监听、增量构建、错误处理
- 团队协作时，CI/CD 流水线中也应包含打包步骤

---

### 第8讲：从设计到打包的完整工作流

#### 概念

本讲梳理 SpriteSheet 从美术设计到最终使用的完整工作流，涵盖命名规范、源图组织、打包配置、版本管理、交付验收五个环节。一个规范的工作流能显著提升团队协作效率，减少资源冲突与返工。

#### 原理

良好的工作流遵循"单一数据源"原则：源图是唯一权威，图集是自动生成的派生物。源图应纳入版本控制（Git），图集则可由构建系统自动生成（也可同时提交以加速 CI）。命名规范确保打包顺序可预测，目录结构反映资源分类，打包配置以代码形式（`.tps` 或脚本）存档，避免依赖个人记忆。

#### 例子

**推荐的目录结构**：

```
project/
├── src/
│   └── sprites/              # 源图根目录
│       ├── characters/       # 角色精灵
│       │   ├── hero/         # 英雄角色
│       │   │   ├── hero_idle_01.png
│       │   │   ├── hero_idle_02.png
│       │   │   ├── hero_walk_01.png
│       │   │   └── hero_walk_02.png
│       │   └── enemy/
│       │       └── goblin_*.png
│       ├── ui/               # UI 精灵
│       │   ├── buttons/
│       │   └── icons/
│       └── effects/          # 特效精灵
├── build/
│   └── atlases/              # 自动生成的图集
│       ├── characters.png
│       ├── characters.json
│       ├── ui.png
│       └── ui.json
├── tools/
│   └── pack.config.json      # 打包配置
└── package.json
```

**命名规范**：

```
{类别}_{名称}_{动作}_{帧号}.png
示例：hero_walk_01.png, hero_walk_02.png
     ui_button_normal.png, ui_button_hover.png
     effect_explosion_01.png
```

**按类别分图集的打包配置**：

```json
// tools/pack.config.json
[
  {
    "input": "src/sprites/characters/",
    "output": "build/atlases/characters",
    "maxSize": 2048,
    "trim": true,
    "rotate": true,
    "multipack": true
  },
  {
    "input": "src/sprites/ui/",
    "output": "build/atlases/ui",
    "maxSize": 1024,
    "trim": true,
    "rotate": false
  },
  {
    "input": "src/sprites/effects/",
    "output": "build/atlases/effects",
    "maxSize": 1024,
    "trim": true,
    "rotate": true
  }
]
```

**完整工作流步骤**：
1. 美术按命名规范输出源图到 `src/sprites/` 对应子目录
2. 提交源图到 Git
3. 构建系统监听变化，自动触发打包
4. 生成图集到 `build/atlases/`，更新 JSON 元数据
5. 开发者引用最新图集进行渲染开发
6. CI/CD 流水线在部署前执行完整打包，确保产物一致

#### 总结

- 工作流五环节：命名规范 → 源图组织 → 打包配置 → 版本管理 → 交付验收
- 源图是唯一权威，图集是自动派生物
- 按资源类别分图集，避免单图集过大
- 命名规范确保可预测性与可维护性
- 配置以代码形式存档，团队共享同一套标准

---

## 第三章：数据格式与解析

### 第9讲：JSON 格式详解——Hash 与 Array 两种结构

#### 概念

JSON 是 SpriteSheet 元数据最常用的格式，因其轻量、易读、与 JavaScript 天然兼容而被广泛采用。JSON 格式主要有两种结构变体：**Hash 结构**（以精灵名称为键的对象）和 **Array 结构**（以精灵数组按顺序排列）。TexturePacker 等工具通常允许选择输出哪种结构，不同引擎对两者的偏好也不同。

#### 原理

**Hash 结构**将每个精灵以 `名称: 属性` 的键值对存储，查找时通过名称直接索引，时间复杂度 O(1)。优点是可读性强、查找高效、不依赖顺序；缺点是精灵数量极大时 JSON 文件略大（键名重复存储），且无法表达帧的顺序（需额外字段或约定命名排序）。

**Array 结构**将精灵按数组顺序排列，每个元素包含 `filename` 字段标识名称。优点是文件略小、天然有序（适合帧动画按序播放）；缺点是查找需遍历（O(n)），可读性稍弱。

两种结构的选择取决于使用场景：UI 图标等需按名称查找的资源适合 Hash；帧动画等需按序播放的资源适合 Array。实际项目中，Hash 结构更常见，因为现代引擎通常会在加载时将 Array 转为内部 Hash 索引。

#### 例子

**Hash 结构（TexturePacker Phaser JSON Hash 格式）**：

```json
{
  "frames": {
    "hero_idle.png": {
      "frame": {"x": 0, "y": 0, "w": 64, "h": 96},
      "rotated": false,
      "trimmed": false,
      "spriteSourceSize": {"x": 0, "y": 0, "w": 64, "h": 96},
      "sourceSize": {"w": 64, "h": 96}
    },
    "hero_walk1.png": {
      "frame": {"x": 64, "y": 0, "w": 70, "h": 95},
      "rotated": true,
      "trimmed": true,
      "spriteSourceSize": {"x": 10, "y": 1, "w": 70, "h": 95},
      "sourceSize": {"w": 96, "h": 96}
    }
  },
  "meta": {
    "image": "hero.png",
    "format": "RGBA8888",
    "size": {"w": 256, "h": 256},
    "scale": 1
  }
}
```

**Array 结构（TexturePacker JSON Array 格式）**：

```json
{
  "frames": [
    {
      "filename": "hero_idle.png",
      "frame": {"x": 0, "y": 0, "w": 64, "h": 96},
      "rotated": false,
      "trimmed": false,
      "spriteSourceSize": {"x": 0, "y": 0, "w": 64, "h": 96},
      "sourceSize": {"w": 64, "h": 96}
    },
    {
      "filename": "hero_walk1.png",
      "frame": {"x": 64, "y": 0, "w": 70, "h": 95},
      "rotated": true,
      "trimmed": true,
      "spriteSourceSize": {"x": 10, "y": 1, "w": 70, "h": 95},
      "sourceSize": {"w": 96, "h": 96}
    }
  ],
  "meta": {
    "image": "hero.png",
    "size": {"w": 256, "h": 256},
    "scale": 1
  }
}
```

**两种结构的查找方式对比**：

```javascript
// Hash 结构查找：直接索引
const frame = data.frames["hero_idle.png"];

// Array 结构查找：需遍历或预建索引
const frame = data.frames.find(f => f.filename === "hero_idle.png");
// 或预建索引
const index = {};
data.frames.forEach(f => index[f.filename] = f);
const frame = index["hero_idle.png"];
```

#### 总结

- Hash 结构：键值对存储，O(1) 查找，可读性强，适合按名称访问
- Array 结构：数组存储，天然有序，文件略小，适合帧动画序列
- 核心字段：`frame`（图集坐标）、`sourceSize`（原始尺寸）、`spriteSourceSize`（裁剪偏移）、`rotated`（旋转标记）
- `meta` 部分记录图集整体信息（文件名、尺寸、缩放比例）
- 引擎通常在加载时统一转为内部 Hash 索引以优化查找

---

### 第10讲：XML 与 plist 格式解析

#### 概念

除 JSON 外，XML 和 plist 也是常见的 SpriteSheet 元数据格式。XML 格式主要在 Cocos2d-x、Starling 等引擎中使用；plist（Property List）是 Apple 平台的原生格式，在 Cocos2d-iPhone、SpriteKit 中广泛使用。虽然 JSON 在 Web 领域占主导，但了解 XML 和 plist 有助于跨引擎协作与历史项目维护。

#### 原理

**XML 格式**使用标签嵌套表达层级关系，通过属性或子节点记录精灵信息。XML 的优势是可扩展性强（可添加自定义命名空间与属性），支持 XPath 查询；劣势是文件体积较大（标签冗余），解析速度略慢于 JSON。

**plist 格式**是 Apple 定义的 XML 子集，使用 `<dict>`、`<key>`、`<string>`、`<integer>` 等标签表达键值对。plist 的结构是"键值交替"的扁平字典，虽然冗长但与 Apple 生态深度集成。在 Web 端解析 plist 需使用 `plist.js` 等库，或手动解析 XML DOM。

两种格式承载的信息与 JSON 等价，只是序列化方式不同。跨格式转换时需注意字段名映射（如 XML 中 `frame` 可能写作 `frameX` / `frameY` / `frameWidth` / `frameHeight` 四个属性）。

#### 例子

**XML 格式（Cocos2d-x / Starling 格式）**：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<TextureAtlas imagePath="hero.png" width="256" height="256">
    <SubTexture name="hero_idle.png" x="0" y="0" width="64" height="96"/>
    <SubTexture name="hero_walk1.png" x="64" y="0" width="70" height="95" frameX="-10" frameY="-1" frameWidth="96" frameHeight="96" rotated="true"/>
    <SubTexture name="hero_walk2.png" x="0" y="96" width="68" height="94" frameX="-12" frameY="-2" frameWidth="96" frameHeight="96"/>
</TextureAtlas>
```

**plist 格式（Cocos2d-iPhone 格式）**：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>frames</key>
    <dict>
        <key>hero_idle.png</key>
        <dict>
            <key>frame</key>
            <string>{{0,0},{64,96}}</string>
            <key>offset</key>
            <string>{0,0}</string>
            <key>rotated</key>
            <false/>
            <key>sourceColorRect</key>
            <string>{{0,0},{64,96}}</string>
            <key>sourceSize</key>
            <string>{64,96}</string>
        </dict>
        <key>hero_walk1.png</key>
        <dict>
            <key>frame</key>
            <string>{{64,0},{70,95}}</string>
            <key>offset</key>
            <string>{-1,0}</string>
            <key>rotated</key>
            <true/>
            <key>sourceColorRect</key>
            <string>{{10,1},{70,95}}</string>
            <key>sourceSize</key>
            <string>{96,96}</string>
        </dict>
    </dict>
    <key>metadata</key>
    <dict>
        <key>format</key>
        <integer>2</integer>
        <key>size</key>
        <string>{256,256}</string>
        <key>textureFileName</key>
        <string>hero.png</string>
    </dict>
</dict>
</plist>
```

**JavaScript 解析 XML**：

```javascript
const parser = new DOMParser();
const doc = parser.parseFromString(xmlString, 'text/xml');
const subTextures = doc.querySelectorAll('SubTexture');
const frames = {};
subTextures.forEach(node => {
  frames[node.getAttribute('name')] = {
    x: parseInt(node.getAttribute('x')),
    y: parseInt(node.getAttribute('y')),
    w: parseInt(node.getAttribute('width')),
    h: parseInt(node.getAttribute('height')),
    rotated: node.getAttribute('rotated') === 'true'
  };
});
```

#### 总结

- XML 格式：标签嵌套，可扩展性强，Cocos2d-x / Starling 引擎常用
- plist 格式：Apple 原生，键值交替字典，Cocos2d-iPhone / SpriteKit 常用
- 信息内容与 JSON 等价，仅序列化方式不同
- Web 端解析 XML 用 DOMParser，解析 plist 需专用库或手动 DOM 处理
- 跨引擎协作时注意字段名映射差异

---

### 第11讲：解析器实现——从元数据到精灵帧

#### 概念

解析器（Parser）是将元数据文件转换为引擎内部可用精灵帧对象（SpriteFrame）的组件。一个健壮的解析器需处理坐标提取、旋转还原、裁剪偏移计算、尺寸归一化等逻辑，将原始的 JSON/XML/plist 数据转化为渲染层可直接使用的标准化帧对象。本讲实现一个完整的 JSON 解析器。

#### 原理

解析器的核心任务是将元数据中的四个关键字段转化为渲染所需的几何信息：

1. **frame（图集坐标）**：精灵在大图中的像素区域，需处理 `rotated` 标记——若旋转为 true，则 `frame.w` 和 `frame.h` 需互换（打包时宽高被转置以节省空间）
2. **sourceSize（原始尺寸）**：精灵未裁剪前的完整尺寸，用于确定渲染目标区域
3. **spriteSourceSize（裁剪偏移）**：裁剪后内容在原始尺寸中的偏移量，用于在渲染时正确对齐
4. **rotated（旋转标记）**：是否需要反向旋转 90° 还原

解析器输出标准化的 `SpriteFrame` 对象，包含：图集引用、源矩形（sx, sy, sw, sh）、目标偏移（dx, dy）、目标尺寸（dw, dh）、旋转标记。渲染层只需消费这些标准化字段，无需关心原始格式差异。

#### 例子

**完整 JSON 解析器实现**：

```javascript
class SpriteSheetParser {
  constructor(atlasImage, jsonData) {
    this.atlasImage = atlasImage;
    this.frames = {};
    this.parse(jsonData);
  }

  parse(data) {
    const meta = data.meta || {};
    this.scale = meta.scale || 1;
    this.imageName = meta.image;

    const framesData = data.frames;
    // 兼容 Hash 和 Array 两种结构
    const entries = Array.isArray(framesData)
      ? framesData.map(f => [f.filename, f])
      : Object.entries(framesData);

    for (const [name, frame] of entries) {
      this.frames[name] = this.parseFrame(frame);
    }
  }

  parseFrame(frame) {
    const fx = frame.frame.x;
    const fy = frame.frame.y;
    const fw = frame.frame.w;
    const fh = frame.frame.h;
    const rotated = frame.rotated || false;
    const trimmed = frame.trimmed || false;

    const sourceW = frame.sourceSize.w;
    const sourceH = frame.sourceSize.h;

    let spriteX = 0, spriteY = 0;
    let spriteW = sourceW, spriteH = sourceH;

    if (trimmed) {
      spriteX = frame.spriteSourceSize.x;
      spriteY = frame.spriteSourceSize.y;
      spriteW = frame.spriteSourceSize.w;
      spriteH = frame.spriteSourceSize.h;
    }

    // 旋转时，图集中的宽高是转置的
    const atlasW = rotated ? fh : fw;
    const atlasH = rotated ? fw : fh;

    return {
      name: frame.filename || name,
      // 图集中的源区域
      sx: fx, sy: fy, sw: atlasW, sh: atlasH,
      // 渲染目标中的偏移与尺寸
      dx: spriteX, dy: spriteY,
      dw: spriteW, dh: spriteH,
      // 原始尺寸（含透明边距）
      sourceW, sourceH,
      rotated
    };
  }

  getFrame(name) {
    return this.frames[name] || null;
  }
}
```

**使用示例**：

```javascript
// 加载图集与元数据
const atlasImage = await loadImage('hero.png');
const response = await fetch('hero.json');
const jsonData = await response.json();

// 解析
const parser = new SpriteSheetParser(atlasImage, jsonData);

// 获取某一帧
const frame = parser.getFrame('hero_walk1.png');
console.log(frame);
// { sx:64, sy:0, sw:95, sh:70, dx:10, dy:1, dw:70, dh:95, sourceW:96, sourceH:96, rotated:true }
```

#### 总结

- 解析器将元数据转化为标准化的 SpriteFrame 对象
- 核心处理：坐标提取、旋转还原（宽高互换）、裁剪偏移计算
- 兼容 Hash 与 Array 两种 JSON 结构
- 输出字段：源区域（sx,sy,sw,sh）、目标偏移（dx,dy,dw,dh）、旋转标记
- 渲染层只消费标准化字段，与原始格式解耦

---

### 第12讲：跨格式兼容与适配层设计

#### 概念

实际项目中，SpriteSheet 元数据可能来自不同工具（TexturePacker、ShoeBox、自写脚本）、不同格式（JSON Hash、JSON Array、XML、plist）、不同引擎约定（Phaser、PixiJS、Cocos2d）。直接在渲染层处理所有格式差异会导致代码臃肿。本讲设计一个适配层（Adapter Layer），将各种格式统一转换为内部标准格式，实现"一次解析，处处可用"。

#### 原理

适配层基于**适配器模式**（Adapter Pattern）：定义统一的内部接口（如 `parse(data) -> FrameMap`），为每种外部格式实现一个适配器类。渲染层只依赖内部接口，新增格式时只需添加新适配器，无需修改渲染代码。

适配层的关键设计：
1. **统一内部模型**：定义 `InternalFrame` 接口，包含所有格式共有的字段
2. **格式探测**：根据数据结构特征（如是否有 `frames` 数组、是否有 `<TextureAtlas>` 标签）自动选择适配器
3. **字段映射**：将各格式的字段名映射到内部模型（如 XML 的 `width` → 内部的 `w`）
4. **默认值填充**：缺失字段（如未裁剪时无 `spriteSourceSize`）用合理默认值填充

这种设计使系统具备良好的扩展性——支持新格式只需新增一个适配器类并注册到工厂中。

#### 例子

**适配层架构实现**：

```javascript
// 统一内部模型
class InternalFrame {
  constructor({name, sx, sy, sw, sh, dx, dy, dw, dh, sourceW, sourceH, rotated}) {
    Object.assign(this, {name, sx, sy, sw, sh, dx, dy, dw, dh, sourceW, sourceH, rotated});
  }
}

// 适配器接口
class FormatAdapter {
  parse(data) { throw new Error('Not implemented'); }
}

// JSON Hash 适配器
class JsonHashAdapter extends FormatAdapter {
  parse(data) {
    const frames = {};
    for (const [name, f] of Object.entries(data.frames)) {
      frames[name] = this.convertFrame(f, name);
    }
    return frames;
  }
  convertFrame(f, name) {
    const rotated = f.rotated || false;
    return new InternalFrame({
      name,
      sx: f.frame.x, sy: f.frame.y,
      sw: rotated ? f.frame.h : f.frame.w,
      sh: rotated ? f.frame.w : f.frame.h,
      dx: f.spriteSourceSize?.x || 0,
      dy: f.spriteSourceSize?.y || 0,
      dw: f.spriteSourceSize?.w || f.sourceSize.w,
      dh: f.spriteSourceSize?.h || f.sourceSize.h,
      sourceW: f.sourceSize.w, sourceH: f.sourceSize.h,
      rotated
    });
  }
}

// JSON Array 适配器
class JsonArrayAdapter extends FormatAdapter {
  parse(data) {
    const frames = {};
    for (const f of data.frames) {
      const adapter = new JsonHashAdapter();
      frames[f.filename] = adapter.convertFrame(f, f.filename);
    }
    return frames;
  }
}

// XML 适配器
class XmlAdapter extends FormatAdapter {
  parse(xmlText) {
    const doc = new DOMParser().parseFromString(xmlText, 'text/xml');
    const frames = {};
    doc.querySelectorAll('SubTexture').forEach(node => {
      const rotated = node.getAttribute('rotated') === 'true';
      const fw = parseInt(node.getAttribute('width'));
      const fh = parseInt(node.getAttribute('height'));
      frames[node.getAttribute('name')] = new InternalFrame({
        name: node.getAttribute('name'),
        sx: parseInt(node.getAttribute('x')),
        sy: parseInt(node.getAttribute('y')),
        sw: rotated ? fh : fw,
        sh: rotated ? fw : fh,
        dx: parseInt(node.getAttribute('frameX') || 0),
        dy: parseInt(node.getAttribute('frameY') || 0),
        dw: fw, dh: fh,
        sourceW: parseInt(node.getAttribute('frameWidth') || fw),
        sourceH: parseInt(node.getAttribute('frameHeight') || fh),
        rotated
      });
    });
    return frames;
  }
}

// 格式工厂：自动探测并选择适配器
class SpriteSheetAdapterFactory {
  static create(data) {
    if (typeof data === 'string') {
      if (data.trim().startsWith('<')) return new XmlAdapter();
      data = JSON.parse(data);
    }
    if (data.frames) {
      return Array.isArray(data.frames)
        ? new JsonArrayAdapter()
        : new JsonHashAdapter();
    }
    throw new Error('Unknown SpriteSheet format');
  }

  static parse(data) {
    const adapter = this.create(data);
    return adapter.parse(data);
  }
}
```

**使用示例**：

```javascript
// 无论什么格式，统一调用
const frames1 = SpriteSheetAdapterFactory.parse(jsonHashData);
const frames2 = SpriteSheetAdapterFactory.parse(jsonArrayData);
const frames3 = SpriteSheetAdapterFactory.parse(xmlString);

// 渲染层只依赖 InternalFrame，与格式无关
function renderFrame(ctx, atlas, frame, x, y) {
  ctx.save();
  if (frame.rotated) {
    ctx.translate(x + frame.dw, y);
    ctx.rotate(Math.PI / 2);
    ctx.drawImage(atlas, frame.sx, frame.sy, frame.sw, frame.sh,
                 frame.dx, frame.dy, frame.dw, frame.dh);
  } else {
    ctx.drawImage(atlas, frame.sx, frame.sy, frame.sw, frame.sh,
                 x + frame.dx, y + frame.dy, frame.dw, frame.dh);
  }
  ctx.restore();
}
```

#### 总结

- 适配层基于适配器模式，将多格式统一为内部模型
- 核心设计：统一接口、格式探测、字段映射、默认值填充
- 新增格式只需新增适配器类，无需修改渲染层
- 格式工厂自动探测数据类型并选择适配器
- 这是大型项目处理多格式资源的标准架构

---

## 第四章：核心渲染技术

### 第13讲：Canvas 基础渲染——drawImage 九参数法

#### 概念

Canvas 2D API 的 `drawImage` 方法是渲染 SpriteSheet 最基础的方式。它支持三种参数形式：三参数（整图绘制）、五参数（整图缩放绘制）、九参数（源区域裁剪 + 目标定位缩放）。其中九参数形式是 SpriteSheet 渲染的核心——通过指定源图集中的矩形区域，精确切出某个精灵并绘制到画布指定位置。

#### 原理

九参数 `drawImage` 的签名如下：

```javascript
ctx.drawImage(image, sx, sy, sw, sh, dx, dy, dw, dh)
```

参数含义：
- `image`：图集图像对象
- `sx, sy, sw, sh`：源矩形——精灵在图集中的位置与尺寸（裁剪区域）
- `dx, dy, dw, dh`：目标矩形——精灵在画布上的位置与尺寸（绘制区域）

渲染流程：GPU 从图集纹理的 `(sx, sy)` 位置取出 `sw × sh` 的像素块，缩放后绘制到画布的 `(dx, dy)` 位置，目标尺寸为 `dw × dh`。若 `sw ≠ dw` 或 `sh ≠ dh`，会触发缩放（默认双线性插值）。

对于被旋转的精灵（`rotated=true`），需配合 `ctx.translate` + `ctx.rotate` 还原：先平移到目标位置，旋转 90°，再以转置后的宽高绘制。这是因为打包时精灵被顺时针旋转 90° 存入图集，渲染时需逆时针旋转 90° 还原。

#### 例子

**基础渲染（无旋转、无裁剪）**：

```javascript
const canvas = document.getElementById('canvas');
const ctx = canvas.getContext('2d');
const atlas = new Image();
atlas.src = 'atlas.png';

atlas.onload = () => {
  // 从图集 (64, 0) 取出 64×96 的精灵，绘制到画布 (100, 50) 位置
  ctx.drawImage(atlas, 64, 0, 64, 96, 100, 50, 64, 96);
};
```

**处理裁剪偏移（trimmed 精灵）**：

```javascript
function drawFrame(ctx, atlas, frame, x, y) {
  // frame 包含裁剪信息：精灵内容偏移在原始尺寸内
  // dx/dy 是内容在原始框中的偏移，dw/dh 是内容尺寸
  ctx.drawImage(
    atlas,
    frame.sx, frame.sy, frame.sw, frame.sh,  // 源区域
    x + frame.dx, y + frame.dy,               // 目标位置（加上偏移）
    frame.dw, frame.dh                         // 目标尺寸
  );
}

// 使用：精灵原始框 96×96，内容偏移 (10, 1)，内容尺寸 70×95
drawFrame(ctx, atlas, {sx:64, sy:0, sw:95, sh:70, dx:10, dy:1, dw:70, dh:95}, 100, 50);
```

**处理旋转精灵**：

```javascript
function drawRotatedFrame(ctx, atlas, frame, x, y) {
  if (!frame.rotated) {
    drawFrame(ctx, atlas, frame, x, y);
    return;
  }
  ctx.save();
  // 旋转精灵：图集中宽高转置，需逆时针旋转 90° 还原
  ctx.translate(x + frame.dw, y);  // 平移到目标右上角
  ctx.rotate(Math.PI / 2);          // 顺时针旋转 90°（等价于图集逆时针还原）
  ctx.drawImage(
    atlas,
    frame.sx, frame.sy, frame.sw, frame.sh,
    frame.dx, frame.dy, frame.dw, frame.dh
  );
  ctx.restore();
}
```

**完整渲染器**：

```javascript
class CanvasSpriteRenderer {
  constructor(ctx, atlas) {
    this.ctx = ctx;
    this.atlas = atlas;
  }

  draw(frame, x, y, scale = 1) {
    const ctx = this.ctx;
    ctx.save();
    if (frame.rotated) {
      ctx.translate(x + frame.dw * scale, y);
      ctx.rotate(Math.PI / 2);
      ctx.drawImage(
        this.atlas,
        frame.sx, frame.sy, frame.sw, frame.sh,
        frame.dx * scale, frame.dy * scale,
        frame.dw * scale, frame.dh * scale
      );
    } else {
      ctx.drawImage(
        this.atlas,
        frame.sx, frame.sy, frame.sw, frame.sh,
        x + frame.dx * scale, y + frame.dy * scale,
        frame.dw * scale, frame.dh * scale
      );
    }
    ctx.restore();
  }
}
```

#### 总结

- `drawImage` 九参数形式是 SpriteSheet 渲染基础
- 源参数（sx,sy,sw,sh）指定图集中的裁剪区域
- 目标参数（dx,dy,dw,dh）指定画布上的绘制位置与尺寸
- 裁剪精灵需加上 `spriteSourceSize` 偏移
- 旋转精灵需 `translate` + `rotate` 还原，宽高在图集中是转置的
- Canvas 渲染简单直观，适合中小规模场景

---

### 第14讲：WebGL 纹理切割与 UV 映射

#### 概念

WebGL 渲染 SpriteSheet 比 Canvas 更高效，因为 WebGL 支持批量绘制（一次 DrawCall 渲染多个精灵）和 GPU 加速变换。WebGL 中通过**UV 坐标**（纹理坐标）实现纹理切割——将图集中精灵的像素坐标转换为 0-1 范围的 UV 坐标，通过顶点属性传递给着色器，GPU 在采样时自动取出对应区域。

#### 原理

WebGL 纹理坐标的原点在左下角，Y 轴向上；而图像坐标的原点在左上角，Y 轴向下。因此像素坐标转 UV 时需翻转 Y 轴：

```
u = pixelX / textureWidth
v = 1 - (pixelY + pixelHeight) / textureHeight
```

每个精灵由两个三角形（四边形）组成，四个顶点分别对应精灵在图集中的四个 UV 坐标。顶点着色器接收位置与 UV 属性，片段着色器根据 UV 采样纹理，输出对应像素颜色。

批量渲染的核心是**顶点缓冲区**：将所有精灵的顶点数据（位置 + UV）写入一个大的缓冲区，一次 `drawArrays` 或 `drawElements` 调用渲染全部。这避免了每个精灵单独 DrawCall 的开销，是 WebGL 性能优势的关键。

#### 例子

**UV 坐标计算**：

```javascript
function frameToUV(frame, textureWidth, textureHeight) {
  const u0 = frame.sx / textureWidth;
  const v0 = 1 - (frame.sy + frame.sh) / textureHeight;
  const u1 = (frame.sx + frame.sw) / textureWidth;
  const v1 = 1 - frame.sy / textureHeight;

  // 旋转精灵需交换 UV
  if (frame.rotated) {
    return { u0: u0, v0: v0, u1: u1, v1: v1, swapped: true };
  }
  return { u0, v0, u1, v1, swapped: false };
}
```

**顶点数据生成**：

```javascript
function buildQuad(frame, x, y, textureW, textureH) {
  const uv = frameToUV(frame, textureW, textureH);
  const w = frame.dw;
  const h = frame.dh;

  // 两个三角形，每个顶点 4 个 float：x, y, u, v
  let { u0, v0, u1, v1 } = uv;
  if (uv.swapped) {
    // 旋转精灵：UV 需转置
    [u1, v1] = [uv.u1, uv.v0];
    [u0, v0] = [uv.u0, uv.v1];
  }

  return [
    // 三角形1
    x,     y,     u0, v0,
    x + w, y,     u1, v0,
    x + w, y + h, u1, v1,
    // 三角形2
    x,     y,     u0, v0,
    x + w, y + h, u1, v1,
    x,     y + h, u0, v1
  ];
}
```

**批量渲染器（简化版）**：

```javascript
class WebGLSpriteRenderer {
  constructor(gl, atlasTexture) {
    this.gl = gl;
    this.texture = atlasTexture;
    this.vertices = [];
    this.maxSprites = 10000;
    this.initShader();
    this.initBuffers();
  }

  initShader() {
    const vsSource = `
      attribute vec2 aPosition;
      attribute vec2 aUV;
      uniform vec2 uResolution;
      varying vec2 vUV;
      void main() {
        vec2 zeroToOne = aPosition / uResolution;
        vec2 zeroToTwo = zeroToOne * 2.0;
        vec2 clipSpace = zeroToTwo - 1.0;
        gl_Position = vec4(clipSpace * vec2(1, -1), 0, 1);
        vUV = aUV;
      }
    `;
    const fsSource = `
      precision mediump float;
      uniform sampler2D uTexture;
      varying vec2 vUV;
      void main() {
        gl_FragColor = texture2D(uTexture, vUV);
      }
    `;
    // 编译链接着色器（略）
    this.program = createProgram(this.gl, vsSource, fsSource);
  }

  drawSprite(frame, x, y) {
    const quad = buildQuad(frame, x, y, this.texture.width, this.texture.height);
    this.vertices.push(...quad);
  }

  flush() {
    const gl = this.gl;
    const float32Array = new Float32Array(this.vertices);
    gl.bindBuffer(gl.ARRAY_BUFFER, this.vertexBuffer);
    gl.bufferData(gl.ARRAY_BUFFER, float32Array, gl.DYNAMIC_DRAW);

    gl.useProgram(this.program);
    gl.activeTexture(gl.TEXTURE0);
    gl.bindTexture(gl.TEXTURE_2D, this.texture);
    gl.uniform1i(gl.getUniformLocation(this.program, 'uTexture'), 0);

    const count = this.vertices.length / 4; // 每顶点4个float
    gl.drawArrays(gl.TRIANGLES, 0, count);
    this.vertices = [];
  }
}
```

#### 总结

- WebGL 通过 UV 坐标实现纹理切割，Y 轴需翻转
- 每个精灵 = 两个三角形 = 6 个顶点（含位置与 UV）
- 批量渲染：所有精灵顶点写入同一缓冲区，一次 DrawCall
- 旋转精灵需交换 UV 坐标
- WebGL 性能远超 Canvas，适合大规模精灵场景（数千个）

---

### 第15讲：CSS Sprite 背景定位技术

#### 概念

CSS Sprite 是 SpriteSheet 在 Web 前端的传统应用形式，通过 `background-image` + `background-position` 将图集中的某个精灵显示为元素背景。这种方式无需 JavaScript，纯 CSS 实现精灵切换，广泛用于网站图标、按钮状态、加载动画等 UI 场景。虽然现代前端更倾向使用 SVG 或图标字体，但 CSS Sprite 在性能敏感场景仍有价值。

#### 原理

CSS Sprite 的核心是 `background-position` 属性。该属性指定背景图相对于元素左上角的偏移，负值表示向左/上移动。若图集中某精灵位于 `(x, y)`，则将 `background-position` 设为 `-x -y`，元素窗口就只显示该精灵区域。

精灵尺寸通过元素的 `width` 和 `height` 限定，确保只露出目标区域。切换精灵时只需修改 `background-position`，浏览器无需重新加载图片，切换流畅。

对于悬停（hover）等状态切换，使用 CSS `:hover` 伪类自动切换 `background-position`，无需 JS 介入。对于帧动画，可通过 CSS `@keyframes` 动画依次切换 `background-position`，实现纯 CSS 帧动画。

#### 例子

**基础 CSS Sprite**：

```css
/* 图集：icons.png，每个图标 32×32 */
.icon {
  width: 32px;
  height: 32px;
  background-image: url('icons.png');
  background-repeat: no-repeat;
}

/* 精灵1位于 (0, 0) */
.icon-home { background-position: 0 0; }

/* 精灵2位于 (32, 0) */
.icon-search { background-position: -32px 0; }

/* 精灵3位于 (64, 0) */
.icon-settings { background-position: -64px 0; }

/* 悬停状态切换 */
.icon-home:hover { background-position: 0 -32px; } /* 第二行是悬停态 */
```

```html
<span class="icon icon-home"></span>
<span class="icon icon-search"></span>
<span class="icon icon-settings"></span>
```

**纯 CSS 帧动画（加载圈）**：

```css
.loader {
  width: 64px;
  height: 64px;
  background-image: url('loader.png');
  background-repeat: no-repeat;
  animation: spin 0.8s steps(8) infinite;
}

@keyframes spin {
  from { background-position: 0 0; }
  to   { background-position: -512px 0; } /* 8帧 × 64px = 512px */
}
```

**使用 `steps()` 函数**：`steps(8)` 确保动画以 8 个离散步骤切换（而非平滑插值），每帧停留相等时间，呈现逐帧动画效果。

**响应式 CSS Sprite（使用 `background-size`）**：

```css
.icon {
  width: 24px;
  height: 24px;
  background-image: url('icons.png');
  background-repeat: no-repeat;
  background-size: 96px 24px; /* 缩放图集到 3 倍图标宽度 */
}
.icon-home    { background-position: 0 0; }
.icon-search  { background-position: -24px 0; }
.icon-settings{ background-position: -48px 0; }
```

#### 总结

- CSS Sprite 通过 `background-position` 负偏移定位精灵
- 元素 `width/height` 限定显示窗口
- 纯 CSS 实现状态切换（`:hover`）与帧动画（`@keyframes` + `steps()`）
- 无需 JavaScript，加载后切换零延迟
- 适合 UI 图标、按钮、加载动画等小尺寸场景
- 现代替代方案：SVG sprite、图标字体，但 CSS Sprite 在兼容性与性能上仍有优势

---

### 第16讲：九宫格与切片缩放（9-slice scaling）

#### 概念

九宫格缩放（9-slice scaling，又称 9-patch）是一种特殊的 SpriteSheet 渲染技术，用于解决 UI 元素（如按钮、对话框、面板）在缩放时边缘变形的问题。它将一张图划分为 9 个区域：四角、四边、中心，缩放时只拉伸中心和边，四角保持原尺寸不变，从而保证圆角、边框等细节不变形。

#### 原理

九宫格将原图按两条水平线、两条垂直线切分为 9 块：

```
┌───┬───────┬───┐
│ 1 │   2   │ 3 │  ← 上边（角-边-角）
├───┼───────┼───┤
│ 4 │   5   │ 6 │  ← 中间（边-中心-边）
├───┼───────┼───┤
│ 7 │   8   │ 9 │  ← 下边（角-边-角）
└───┴───────┴───┘
```

缩放规则：
- **四角（1,3,7,9）**：保持原始尺寸，不缩放
- **四边（2,4,6,8）**：单方向缩放——上下边水平拉伸，左右边垂直拉伸
- **中心（5）**：双向缩放，填满剩余空间

这种划分使得按钮的圆角（四角）在任何尺寸下都保持清晰，边框纹理（四边）均匀拉伸，中心背景平滑填充。九宫格参数通常在元数据中记录为 `sliceLeft, sliceRight, sliceTop, sliceBottom` 四个值，表示切线距各边的像素距离。

#### 例子

**Canvas 实现九宫格绘制**：

```javascript
function draw9Slice(ctx, atlas, frame, x, y, w, h, slice) {
  // slice = {left, right, top, bottom}
  const sx = frame.sx, sy = frame.sy;
  const sw = frame.sw, sh = frame.sh;

  // 9 个源区域
  const srcCorners = {
    tl: [sx, sy, slice.left, slice.top],
    tr: [sx + sw - slice.right, sy, slice.right, slice.top],
    bl: [sx, sy + sh - slice.bottom, slice.left, slice.bottom],
    br: [sx + sw - slice.right, sy + sh - slice.bottom, slice.right, slice.bottom]
  };
  const srcEdges = {
    t: [sx + slice.left, sy, sw - slice.left - slice.right, slice.top],
    b: [sx + slice.left, sy + sh - slice.bottom, sw - slice.left - slice.right, slice.bottom],
    l: [sx, sy + slice.top, slice.left, sh - slice.top - slice.bottom],
    r: [sx + sw - slice.right, sy + slice.top, slice.right, sh - slice.top - slice.bottom]
  };
  const srcCenter = [
    sx + slice.left, sy + slice.top,
    sw - slice.left - slice.right, sh - slice.top - slice.bottom
  ];

  // 9 个目标区域
  const innerW = w - slice.left - slice.right;
  const innerH = h - slice.top - slice.bottom;

  // 绘制四角（不缩放）
  ctx.drawImage(atlas, ...srcCorners.tl, x, y, slice.left, slice.top);
  ctx.drawImage(atlas, ...srcCorners.tr, x + w - slice.right, y, slice.right, slice.top);
  ctx.drawImage(atlas, ...srcCorners.bl, x, y + h - slice.bottom, slice.left, slice.bottom);
  ctx.drawImage(atlas, ...srcCorners.br, x + w - slice.right, y + h - slice.bottom, slice.right, slice.bottom);

  // 绘制四边（单方向缩放）
  ctx.drawImage(atlas, ...srcEdges.t, x + slice.left, y, innerW, slice.top);
  ctx.drawImage(atlas, ...srcEdges.b, x + slice.left, y + h - slice.bottom, innerW, slice.bottom);
  ctx.drawImage(atlas, ...srcEdges.l, x, y + slice.top, slice.left, innerH);
  ctx.drawImage(atlas, ...srcEdges.r, x + w - slice.right, y + slice.top, slice.right, innerH);

  // 绘制中心（双向缩放）
  ctx.drawImage(atlas, ...srcCenter, x + slice.left, y + slice.top, innerW, innerH);
}
```

**CSS 实现（border-image）**：

```css
.panel {
  border-width: 20px;
  border-style: solid;
  border-image: url('panel.png') 20 20 20 20 fill stretch;
  /* 20 20 20 20 = 上右下左切线距离 */
  /* fill = 中心区域填充 */
  /* stretch = 边拉伸（也可用 repeat / round） */
}
```

**使用示例**：

```javascript
// 按钮原图 48×48，切线各 12px，缩放到 200×60
const buttonFrame = { sx: 0, sy: 0, sw: 48, sh: 48 };
const slice = { left: 12, right: 12, top: 12, bottom: 12 };
draw9Slice(ctx, atlas, buttonFrame, 100, 100, 200, 60, slice);
// 结果：圆角清晰，边框均匀，中心平滑填充
```

#### 总结

- 九宫格将图切为 9 块：四角不缩放、四边单方向缩放、中心双向缩放
- 解决 UI 元素缩放时圆角与边框变形问题
- 切线参数：`sliceLeft, sliceRight, sliceTop, sliceBottom`
- Canvas 需 9 次 `drawImage` 调用分别绘制 9 块
- CSS 可用 `border-image` 简洁实现
- 广泛用于按钮、对话框、面板等可变尺寸 UI

---

### 第17讲：多图集管理与动态切换

#### 概念

当游戏或应用资源量较大时，单个图集无法容纳所有精灵（受最大纹理尺寸限制，通常 2048×2048 或 4096×4096）。此时需将资源分散到多个图集中，并实现多图集管理——统一索引、按需加载、动态切换。本讲讲解多图集的索引设计与加载策略。

#### 原理

多图集管理的核心是**全局帧索引**：将所有图集的帧注册到一个统一的 Map 中，键为精灵名称，值为 `{atlasId, frame}`。渲染时通过精灵名称查索引，获取所属图集与帧信息，再绑定对应图集纹理进行绘制。

加载策略分两种：
1. **预加载**：启动时加载所有图集，适合资源量可控的小型项目
2. **按需加载（懒加载）**：根据场景需要动态加载对应图集，适合大型项目。加载完成后注册到全局索引，未加载时返回占位符或触发加载请求

动态切换指渲染时根据精灵所属图集自动切换当前绑定的纹理。为减少切换次数，渲染前应按图集 ID 对精灵排序，使同一图集的精灵连续渲染，最大化批量绘制效率。

#### 例子

**多图集管理器**：

```javascript
class SpriteSheetManager {
  constructor() {
    this.atlases = new Map();    // atlasId -> {image, texture}
    this.frameIndex = new Map(); // frameName -> {atlasId, frame}
  }

  // 注册图集
  registerAtlas(atlasId, image, frames) {
    this.atlases.set(atlasId, { image, frames: {} });
    // 将帧注册到全局索引
    for (const [name, frame] of Object.entries(frames)) {
      this.frameIndex.set(name, { atlasId, frame });
    }
  }

  // 获取帧信息
  getFrame(frameName) {
    return this.frameIndex.get(frameName);
  }

  // 获取图集
  getAtlas(atlasId) {
    return this.atlases.get(atlasId);
  }

  // 按图集分组精灵（优化渲染顺序）
  groupByAtlas(spriteList) {
    const groups = new Map();
    for (const sprite of spriteList) {
      const info = this.getFrame(sprite.frameName);
      if (!info) continue;
      if (!groups.has(info.atlasId)) {
        groups.set(info.atlasId, []);
      }
      groups.get(info.atlasId).push({ ...sprite, frame: info.frame });
    }
    return groups;
  }
}
```

**按需加载**：

```javascript
class LazySpriteSheetManager extends SpriteSheetManager {
  constructor(manifest) {
    super();
    this.manifest = manifest; // {atlasId: {image, json}}
    this.loadingPromises = new Map();
  }

  async ensureLoaded(atlasId) {
    if (this.atlases.has(atlasId)) return;
    if (this.loadingPromises.has(atlasId)) {
      return this.loadingPromises.get(atlasId);
    }

    const promise = (async () => {
      const config = this.manifest[atlasId];
      const [image, jsonResp] = await Promise.all([
        loadImage(config.image),
        fetch(config.json).then(r => r.json())
      ]);
      const frames = parseFrames(jsonResp);
      this.registerAtlas(atlasId, image, frames);
    })();

    this.loadingPromises.set(atlasId, promise);
    await promise;
    this.loadingPromises.delete(atlasId);
  }

  async getFrameAsync(frameName) {
    const info = this.getFrame(frameName);
    if (info) return info;
    // 尝试从 manifest 查找所属图集并加载
    for (const atlasId of Object.keys(this.manifest)) {
      await this.ensureLoaded(atlasId);
      const info = this.getFrame(frameName);
      if (info) return info;
    }
    return null;
  }
}
```

**渲染时按图集分组**：

```javascript
function renderSprites(renderer, manager, sprites) {
  // 按图集分组，减少纹理切换
  const groups = manager.groupByAtlas(sprites);
  for (const [atlasId, groupSprites] of groups) {
    const atlas = manager.getAtlas(atlasId);
    renderer.bindTexture(atlas.image);
    for (const sprite of groupSprites) {
      renderer.drawSprite(sprite.frame, sprite.x, sprite.y);
    }
  }
  renderer.flush();
}
```

#### 总结

- 多图集管理通过全局帧索引统一访问不同图集的精灵
- 加载策略：预加载（简单）或按需加载（节省内存）
- 渲染前按图集分组精灵，最小化纹理切换次数
- 懒加载需处理并发请求与加载状态管理
- 大型项目必备能力，支撑海量资源的高效调度

---

## 第五章：动画系统

### 第18讲：帧动画原理——时间轴与帧率控制

#### 概念

帧动画（Frame Animation）是 SpriteSheet 最核心的应用形式，通过快速连续播放图集中的多个精灵帧，产生运动视觉效果。帧动画系统需管理时间轴（Timeline）、帧率（FPS）、帧序列（Frame Sequence）三个要素，确保动画播放流畅、可控、可扩展。本讲讲解帧动画的时间驱动原理与帧率控制实现。

#### 原理

帧动画的本质是"按时间切换显示的精灵帧"。时间轴记录动画已播放的时长，帧率决定每秒切换多少帧。例如 12 FPS 的动画，每帧持续 `1000/12 ≈ 83.3ms`。每帧更新时，根据累计时间计算当前应显示第几帧：`currentFrame = floor(elapsedTime / frameDuration) % totalFrames`。

帧率控制的关键是**与渲染解耦**。动画更新逻辑应基于真实经过的时间（deltaTime），而非渲染帧数。这样即使渲染帧率波动（如 60fps、30fps、掉帧），动画播放速度仍保持一致。这种设计称为"时间驱动动画"，是现代游戏引擎的标准做法。

帧序列支持非均匀帧时长——某些关键帧可停留更久（如攻击动画的蓄力帧）。这通过为每帧单独指定 `duration` 实现，而非全局统一帧率。

#### 例子

**基础帧动画类**：

```javascript
class FrameAnimation {
  constructor(frames, fps = 12, loop = true) {
    this.frames = frames;          // 帧名称数组：['walk_01', 'walk_02', ...]
    this.fps = fps;
    this.loop = loop;
    this.frameDuration = 1000 / fps;
    this.elapsed = 0;
    this.currentIndex = 0;
    this.playing = false;
  }

  play() { this.playing = true; this.elapsed = 0; this.currentIndex = 0; }
  pause() { this.playing = false; }
  resume() { this.playing = true; }
  stop() { this.playing = false; this.elapsed = 0; this.currentIndex = 0; }

  update(deltaTime) {
    if (!this.playing) return;
    this.elapsed += deltaTime;
    const totalFrames = this.frames.length;
    const frameIndex = Math.floor(this.elapsed / this.frameDuration);

    if (this.loop) {
      this.currentIndex = frameIndex % totalFrames;
    } else {
      this.currentIndex = Math.min(frameIndex, totalFrames - 1);
      if (this.currentIndex >= totalFrames - 1) {
        this.playing = false;
        this.onComplete?.();
      }
    }
  }

  getCurrentFrame() {
    return this.frames[this.currentIndex];
  }
}
```

**非均匀帧时长（关键帧延长）**：

```javascript
class VariableFrameAnimation {
  constructor(frameData, loop = true) {
    // frameData: [{name: 'attack_01', duration: 100}, {name: 'attack_02', duration: 50}, ...]
    this.frameData = frameData;
    this.loop = loop;
    this.elapsed = 0;
    this.currentIndex = 0;
    this.playing = false;
  }

  update(deltaTime) {
    if (!this.playing) return;
    this.elapsed += deltaTime;

    let remaining = this.elapsed;
    const total = this.getTotalDuration();

    if (this.loop) {
      remaining = remaining % total;
    } else if (remaining >= total) {
      this.currentIndex = this.frameData.length - 1;
      this.playing = false;
      this.onComplete?.();
      return;
    }

    // 逐帧累加时长，找到当前帧
    let acc = 0;
    for (let i = 0; i < this.frameData.length; i++) {
      acc += this.frameData[i].duration;
      if (remaining < acc) {
        this.currentIndex = i;
        break;
      }
    }
  }

  getTotalDuration() {
    return this.frameData.reduce((sum, f) => sum + f.duration, 0);
  }

  getCurrentFrame() {
    return this.frameData[this.currentIndex].name;
  }
}
```

**主循环集成**：

```javascript
let lastTime = performance.now();
const animation = new FrameAnimation(['walk_01', 'walk_02', 'walk_03', 'walk_04'], 12);
animation.play();

function loop(now) {
  const deltaTime = now - lastTime;
  lastTime = now;

  animation.update(deltaTime);
  const frameName = animation.getCurrentFrame();
  const frame = parser.getFrame(frameName);
  renderer.draw(frame, x, y);

  requestAnimationFrame(loop);
}
requestAnimationFrame(loop);
```

#### 总结

- 帧动画 = 按时间切换精灵帧
- 时间驱动：基于 deltaTime 更新，与渲染帧率解耦
- 帧率控制：`frameDuration = 1000 / fps`
- 支持均匀帧率（统一 fps）与非均匀帧时长（每帧独立 duration）
- 循环模式（loop）与单次模式（onComplete 回调）
- 主循环中 `requestAnimationFrame` + deltaTime 是标准集成方式

---

### 第19讲：动画状态机设计

#### 概念

游戏角色通常有多个动画状态——待机（idle）、行走（walk）、奔跑（run）、跳跃（jump）、攻击（attack）、受击（hit）、死亡（die）。这些状态间有特定的切换规则（如不能从死亡直接切换到行走）。动画状态机（Animation State Machine，ASM）用有限状态机（FSM）模型管理状态间的合法切换，确保动画逻辑清晰、可控。

#### 原理

状态机的核心要素：
1. **状态（State）**：每个动画是一个状态，包含帧序列、帧率、是否循环等
2. **转换（Transition）**：定义状态间的合法切换路径与触发条件
3. **事件（Event）**：外部输入（如按键、AI 决策）触发状态转换
4. **当前状态（Current State）**：任意时刻只有一个活动状态

状态机维护一个转换表，记录"从状态 A 通过事件 E 可切换到状态 B"。当收到事件时，查表判断是否允许转换，允许则切换到目标状态并重置动画。非法转换被忽略（如死亡状态下收到"行走"事件不响应）。

进阶设计支持**转换条件**（如只有速度 > 0 才能从 idle 切换到 walk）和**转换过渡**（状态切换时播放过渡动画或混合，详见下一讲）。

#### 例子

**状态机实现**：

```javascript
class AnimationStateMachine {
  constructor() {
    this.states = new Map();       // stateName -> FrameAnimation
    this.transitions = new Map();  // "from:event" -> toState
    this.currentState = null;
    this.currentName = null;
  }

  // 添加状态
  addState(name, animation) {
    this.states.set(name, animation);
    if (!this.currentState) {
      this.currentState = animation;
      this.currentName = name;
      animation.play();
    }
  }

  // 添加转换规则
  addTransition(from, event, to) {
    const key = `${from}:${event}`;
    this.transitions.set(key, to);
  }

  // 触发事件
  trigger(event) {
    const key = `${this.currentName}:${event}`;
    const targetState = this.transitions.get(key);
    if (targetState && targetState !== this.currentName) {
      this.currentState.stop();
      this.currentName = targetState;
      this.currentState = this.states.get(targetState);
      this.currentState.play();
      return true;
    }
    return false;
  }

  update(deltaTime) {
    this.currentState.update(deltaTime);
  }

  getCurrentFrame() {
    return this.currentState.getCurrentFrame();
  }
}
```

**定义角色状态机**：

```javascript
const asm = new AnimationStateMachine();

// 添加状态
asm.addState('idle',  new FrameAnimation(['idle_01','idle_02','idle_03','idle_04'], 8, true));
asm.addState('walk',  new FrameAnimation(['walk_01','walk_02','walk_03','walk_04','walk_05','walk_06'], 12, true));
asm.addState('run',   new FrameAnimation(['run_01','run_02','run_03','run_04','run_05','run_06','run_07','run_08'], 20, true));
asm.addState('jump',  new FrameAnimation(['jump_01','jump_02','jump_03','jump_04'], 12, false));
asm.addState('attack',new FrameAnimation(['atk_01','atk_02','atk_03','atk_04','atk_05'], 15, false));
asm.addState('die',   new FrameAnimation(['die_01','die_02','die_03','die_04','die_05','die_06'], 10, false));

// 定义转换规则
asm.addTransition('idle', 'move',     'walk');
asm.addTransition('idle', 'jump',     'jump');
asm.addTransition('idle', 'attack',   'attack');
asm.addTransition('idle', 'die',      'die');

asm.addTransition('walk', 'stop',     'idle');
asm.addTransition('walk', 'run',      'run');
asm.addTransition('walk', 'jump',     'jump');

asm.addTransition('run',  'stop',     'idle');
asm.addTransition('run',  'walk',     'walk');

// 攻击/跳跃完成后自动回到 idle
asm.states.get('attack').onComplete = () => asm.trigger('finish');
asm.states.get('jump').onComplete = () => asm.trigger('finish');
asm.addTransition('attack', 'finish', 'idle');
asm.addTransition('jump', 'finish', 'idle');

// 使用
asm.trigger('move');   // idle -> walk
asm.trigger('run');    // walk -> run
asm.trigger('attack'); // 非法！run 状态下不能直接攻击，需先 stop
asm.trigger('stop');   // run -> idle
asm.trigger('attack'); // idle -> attack
```

#### 总结

- 状态机用 FSM 模型管理多动画间的合法切换
- 核心要素：状态、转换、事件、当前状态
- 转换表定义"from + event → to"的映射
- 非法转换被忽略，保证动画逻辑正确
- 支持完成回调（onComplete）实现自动状态回归
- 是游戏角色动画的标准架构

---

### 第20讲：混合动画与平滑过渡

#### 概念

状态机切换动画时通常是"硬切"——立即从当前帧跳到新动画第一帧，视觉上会显得突兀。混合动画（Animation Blending）通过在状态切换时插入过渡，使两个动画的帧平滑交叉淡入淡出，消除视觉跳跃。本讲讲解基于透明度混合的过渡技术与实现。

#### 原理

混合过渡的核心是**交叉淡入淡出（Cross-fade）**：在过渡期间同时渲染前一动画的当前帧与后一动画的第一帧（或对应时间帧），通过透明度权重叠加。过渡开始时，前一动画权重为 1、后一为 0；过渡结束时，前一为 0、后一为 1；中间线性插值。

过渡时长通常为 100-300ms，过短看不出效果，过长显得拖沓。过渡期间两个动画都继续更新（前一动画可暂停在最后一帧或继续播放）。

混合方式有两种：
1. **简单混合**：前一帧与后一帧按权重叠加渲染（需两次 drawImage + globalAlpha）
2. **时间对齐混合**：根据过渡进度，从两个动画中分别取出对应时间点的帧进行混合（更精确但复杂）

#### 例子

**交叉淡入淡出实现**：

```javascript
class BlendableStateMachine extends AnimationStateMachine {
  constructor() {
    super();
    this.transitionDuration = 200; // ms
    this.transitionElapsed = 0;
    this.transitioning = false;
    this.fromAnimation = null;
    this.toAnimation = null;
  }

  trigger(event) {
    const key = `${this.currentName}:${event}`;
    const targetState = this.transitions.get(key);
    if (targetState && targetState !== this.currentName) {
      // 启动过渡
      this.fromAnimation = this.currentState;
      this.toAnimation = this.states.get(targetState);
      this.toAnimation.play();
      this.transitioning = true;
      this.transitionElapsed = 0;
      this.currentName = targetState;
      this.currentState = this.toAnimation;
      return true;
    }
    return false;
  }

  update(deltaTime) {
    if (this.transitioning) {
      this.transitionElapsed += deltaTime;
      const t = this.transitionElapsed / this.transitionDuration;
      if (t >= 1) {
        // 过渡完成
        this.transitioning = false;
        this.fromAnimation = null;
        this.currentState.update(deltaTime);
      } else {
        // 过渡中：两个动画都更新
        this.fromAnimation.update(deltaTime);
        this.toAnimation.update(deltaTime);
      }
    } else {
      this.currentState.update(deltaTime);
    }
  }

  // 渲染时获取混合帧
  getRenderInfo() {
    if (this.transitioning) {
      const t = this.transitionElapsed / this.transitionDuration;
      return {
        blending: true,
        fromFrame: this.fromAnimation.getCurrentFrame(),
        toFrame: this.toAnimation.getCurrentFrame(),
        fromAlpha: 1 - t,
        toAlpha: t
      };
    }
    return {
      blending: false,
      frame: this.currentState.getCurrentFrame()
    };
  }
}
```

**渲染混合帧**：

```javascript
function renderBlended(renderer, parser, renderInfo, x, y) {
  if (renderInfo.blending) {
    // 渲染前一帧（淡出）
    const fromFrame = parser.getFrame(renderInfo.fromFrame);
    renderer.ctx.globalAlpha = renderInfo.fromAlpha;
    renderer.draw(fromFrame, x, y);

    // 渲染后一帧（淡入）
    const toFrame = parser.getFrame(renderInfo.toFrame);
    renderer.ctx.globalAlpha = renderInfo.toAlpha;
    renderer.draw(toFrame, x, y);

    // 恢复透明度
    renderer.ctx.globalAlpha = 1;
  } else {
    const frame = parser.getFrame(renderInfo.frame);
    renderer.draw(frame, x, y);
  }
}
```

**使用**：

```javascript
const asm = new BlendableStateMachine();
// ... 添加状态与转换（同上一讲）...

// 主循环
function loop(now) {
  const dt = now - lastTime;
  lastTime = now;

  asm.update(dt);
  const info = asm.getRenderInfo();
  renderBlended(renderer, parser, info, characterX, characterY);

  requestAnimationFrame(loop);
}
```

#### 总结

- 混合动画通过交叉淡入淡出消除状态切换的视觉跳跃
- 过渡期间同时渲染两个动画帧，按权重叠加透明度
- 过渡时长通常 100-300ms
- 简单混合：两帧 globalAlpha 叠加；时间对齐混合：按进度取对应帧
- 需渲染器支持透明度（Canvas 的 globalAlpha 或 WebGL 的 blend）
- 提升动画流畅度与视觉品质的关键技术

---

### 第21讲：事件触发与音画同步

#### 概念

动画播放过程中常需在特定帧触发事件——如攻击动画在第 3 帧产生伤害判定、跳跃动画在第 1 帧播放起跳音效、行走动画每隔一帧播放脚步声。这种"帧事件"（Frame Event）机制实现音画同步与游戏逻辑联动，是动画系统的高级能力。本讲讲解帧事件的设计与实现。

#### 原理

帧事件的核心是**在动画的特定帧上注册回调**。每个动画维护一个事件表，记录"第 N 帧 → 触发什么事件"。动画更新时，检测当前帧是否变化，若新帧注册了事件则触发对应回调。

为防止事件重复触发（同一帧被 update 多次），需记录"上次触发的帧索引"，只有帧索引变化时才检查事件。循环动画中，每次循环到事件帧都应重新触发。

事件类型通常包括：
- **音效事件**：播放对应音效（如脚步、挥剑、爆炸）
- **逻辑事件**：触发游戏逻辑（如伤害判定、生成投射物）
- **特效事件**：在角色身上播放粒子特效

事件数据可在外部配置文件（JSON）中定义，与动画数据解耦，便于策划调整。

#### 例子

**帧事件系统**：

```javascript
class EventFrameAnimation extends FrameAnimation {
  constructor(frames, fps, loop) {
    super(frames, fps, loop);
    this.frameEvents = new Map(); // frameIndex -> [eventHandlers]
    this.lastTriggeredIndex = -1;
  }

  // 在某帧注册事件
  addFrameEvent(frameIndex, handler) {
    if (!this.frameEvents.has(frameIndex)) {
      this.frameEvents.set(frameIndex, []);
    }
    this.frameEvents.get(frameIndex).push(handler);
  }

  update(deltaTime) {
    const prevIndex = this.currentIndex;
    super.update(deltaTime);

    // 帧变化时检查事件
    if (this.currentIndex !== this.lastTriggeredIndex) {
      // 处理循环时跨帧的情况（跳过多帧）
      this.checkEvents(prevIndex, this.currentIndex);
      this.lastTriggeredIndex = this.currentIndex;
    }
  }

  checkEvents(fromIndex, toIndex) {
    // 触发 from 到 to 之间所有帧的事件
    if (toIndex > fromIndex) {
      for (let i = fromIndex + 1; i <= toIndex; i++) {
        this.fireEvents(i);
      }
    } else if (toIndex < fromIndex) {
      // 循环回绕
      for (let i = fromIndex + 1; i < this.frames.length; i++) {
        this.fireEvents(i);
      }
      for (let i = 0; i <= toIndex; i++) {
        this.fireEvents(i);
      }
    }
  }

  fireEvents(frameIndex) {
    const events = this.frameEvents.get(frameIndex);
    if (events) {
      events.forEach(handler => handler(frameIndex));
    }
  }
}
```

**配置化事件（JSON）**：

```json
{
  "attack": {
    "frames": ["atk_01","atk_02","atk_03","atk_04","atk_05"],
    "fps": 15,
    "loop": false,
    "events": {
      "2": [{"type": "sound", "name": "sword_swing.wav"}],
      "3": [
        {"type": "hitbox", "x": 40, "y": 20, "w": 60, "h": 80, "damage": 50},
        {"type": "effect", "name": "slash_spark", "x": 50, "y": 40}
      ],
      "4": [{"type": "sound", "name": "sword_sheath.wav"}]
    }
  },
  "walk": {
    "frames": ["walk_01","walk_02","walk_03","walk_04","walk_05","walk_06"],
    "fps": 12,
    "loop": true,
    "events": {
      "1": [{"type": "sound", "name": "footstep.wav"}],
      "4": [{"type": "sound", "name": "footstep.wav"}]
    }
  }
}
```

**事件处理器注册**：

```javascript
function setupAnimation(config, soundManager, gameLogic) {
  const anim = new EventFrameAnimation(config.frames, config.fps, config.loop);

  for (const [frameStr, events] of Object.entries(config.events || {})) {
    const frameIndex = parseInt(frameStr);
    for (const evt of events) {
      anim.addFrameEvent(frameIndex, () => {
        switch (evt.type) {
          case 'sound':
            soundManager.play(evt.name);
            break;
          case 'hitbox':
            gameLogic.spawnHitbox(evt);
            break;
          case 'effect':
            gameLogic.spawnEffect(evt.name, evt.x, evt.y);
            break;
        }
      });
    }
  }
  return anim;
}

// 使用
const attackAnim = setupAnimation(configData.attack, soundManager, gameLogic);
// 第 2 帧播放挥剑音效，第 3 帧产生伤害判定与特效，第 4 帧播放收剑音效
```

#### 总结

- 帧事件在特定帧触发回调，实现音画同步与逻辑联动
- 核心机制：帧变化时检查事件表，记录 lastTriggeredIndex 防重复
- 事件类型：音效、逻辑判定、特效生成
- 事件数据应外部配置化（JSON），与代码解耦
- 循环动画需处理帧回绕，触发跨帧事件
- 是游戏战斗系统、音效系统的核心集成点

---

## 第六章：性能优化

### 第22讲：纹理内存与 GPU 优化

#### 概念

SpriteSheet 的性能优势源于减少纹理切换，但图集本身的内存占用与 GPU 上传成本同样需优化。一张 2048×2048 的 RGBA 图集占用 `2048×2048×4 = 16MB` 显存，若管理不当（如重复上传、未释放、尺寸过大），会导致内存暴涨与帧率下降。本讲讲解纹理内存计算、GPU 上传优化与显存管理策略。

#### 原理

**纹理内存计算**：显存占用 = 宽 × 高 × 每像素字节数。常见格式：
- RGBA8888：4 字节/像素（最高质量，默认）
- RGBA4444：2 字节/像素（颜色精度降低，适合 UI）
- RGB565：2 字节/像素（无透明通道，适合背景）
- RGBA5551：2 字节/像素（1 位透明，适合二值透明图）
- ETC2/PVRTC/ASTC：压缩格式，0.5-1 字节/像素（移动端专用）

**GPU 上传优化**：纹理从 CPU 内存上传到 GPU 显存是耗时操作，应避免每帧上传。策略：
1. 启动时一次性上传所有图集，运行时只更新 UV 坐标
2. 动态生成的图集（如运行时合图）上传后缓存，避免重复
3. 使用 `gl.texStorage2D` 预分配不可变纹理，比 `gl.texImage2D` 更高效

**显存管理**：不再使用的图集应及时释放（`gl.deleteTexture`），避免显存泄漏。场景切换时卸载旧场景图集，加载新场景图集。

#### 例子

**纹理内存计算工具**：

```javascript
function calculateTextureMemory(width, height, format) {
  const bytesPerPixel = {
    'RGBA8888': 4,
    'RGBA4444': 2,
    'RGB565': 2,
    'RGBA5551': 2,
    'ETC2_RGBA': 1,    // 4×4 块压缩，8 字节/块
    'PVRTC4_RGBA': 0.5 // 4×4 块压缩，4 字节/块
  };
  const bpp = bytesPerPixel[format] || 4;
  const bytes = width * height * bpp;
  return {
    bytes,
    mb: (bytes / 1024 / 1024).toFixed(2),
    format
  };
}

// 示例
console.log(calculateTextureMemory(2048, 2048, 'RGBA8888'));
// { bytes: 16777216, mb: "16.00", format: "RGBA8888" }

console.log(calculateTextureMemory(2048, 2048, 'RGBA4444'));
// { bytes: 8388608, mb: "8.00", format: "RGBA4444" }  节省 50%

console.log(calculateTextureMemory(2048, 2048, 'ETC2_RGBA'));
// { bytes: 4194304, mb: "4.00", format: "ETC2_RGBA" }  节省 75%
```

**WebGL 纹理上传与缓存**：

```javascript
class TextureManager {
  constructor(gl) {
    this.gl = gl;
    this.cache = new Map(); // imageSrc -> WebGLTexture
  }

  getTexture(image) {
    if (this.cache.has(image.src)) {
      return this.cache.get(image.src);
    }

    const gl = this.gl;
    const texture = gl.createTexture();
    gl.bindTexture(gl.TEXTURE_2D, texture);

    // 上传纹理（一次性）
    gl.texImage2D(
      gl.TEXTURE_2D, 0, gl.RGBA,
      gl.RGBA, gl.UNSIGNED_BYTE, image
    );

    // 设置参数
    gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_S, gl.CLAMP_TO_EDGE);
    gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_T, gl.CLAMP_TO_EDGE);
    gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, gl.LINEAR);
    gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MAG_FILTER, gl.LINEAR);

    // 生成 mipmap（适合会缩放的图集）
    // gl.generateMipmap(gl.TEXTURE_2D);

    this.cache.set(image.src, texture);
    return texture;
  }

  releaseTexture(imageSrc) {
    const texture = this.cache.get(imageSrc);
    if (texture) {
      this.gl.deleteTexture(texture);
      this.cache.delete(imageSrc);
    }
  }

  releaseAll() {
    for (const texture of this.cache.values()) {
      this.gl.deleteTexture(texture);
    }
    this.cache.clear();
  }
}
```

**按场景管理显存**：

```javascript
class SceneTextureManager {
  constructor(gl) {
    this.gl = gl;
    this.textureManager = new TextureManager(gl);
    this.sceneCache = new Map(); // sceneName -> Set<imageSrc>
  }

  loadScene(sceneName, images) {
    // 卸载旧场景（引用计数为 0 的才释放）
    for (const [name, srcs] of this.sceneCache) {
      if (name !== sceneName) {
        srcs.forEach(src => this.textureManager.releaseTexture(src));
        this.sceneCache.delete(name);
      }
    }

    // 加载新场景
    const loaded = new Set();
    for (const image of images) {
      this.textureManager.getTexture(image);
      loaded.add(image.src);
    }
    this.sceneCache.set(sceneName, loaded);
  }
}
```

#### 总结

- 纹理内存 = 宽 × 高 × 每像素字节数
- RGBA8888 占用最高，RGBA4444/RGB565 节省 50%，压缩格式节省 75%+
- GPU 上传是耗时操作，应缓存纹理避免重复上传
- 显存管理：场景切换时释放旧图集，避免泄漏
- 移动端优先使用压缩格式（ETC2/PVRTC/ASTC）
- `gl.deleteTexture` 释放显存，`texStorage2D` 预分配更高效

---

### 第23讲：批量渲染与 DrawCall 合并

#### 概念

DrawCall（绘制调用）是 CPU 向 GPU 发出的渲染指令，每次调用都有固定开销（状态验证、命令编码、上下文切换）。若每个精灵单独一次 DrawCall，渲染 1000 个精灵就是 1000 次调用，CPU 成为瓶颈。批量渲染（Batching）将多个精灵的顶点数据合并到一个缓冲区，用一次 DrawCall 渲染全部，是 SpriteSheet 性能优化的核心手段。

#### 原理

批量渲染的前提是所有精灵共享同一纹理（同一图集）。若精灵来自不同图集，需按图集分组，每组一次 DrawCall。这就是第 17 讲强调"按图集分组渲染"的原因。

实现方式：
1. **动态批处理**：每帧收集所有精灵的顶点数据，写入一个大 Float32Array，一次 `drawArrays` 渲染。适合精灵数量动态变化的场景。
2. **静态批处理**：不变化的精灵（如背景瓦片）顶点数据预生成并缓存，多帧复用。适合静态场景。
3. **实例化渲染（Instancing）**：WebGL2 特性，一次 DrawCall 用不同变换矩阵渲染同一几何体。适合大量相同精灵（如粒子）。

顶点数据布局通常为：`[x, y, u, v, r, g, b, a]`（每顶点 8 个 float），支持位置、UV、顶点颜色（用于着色与透明度）。

#### 例子

**动态批处理渲染器**：

```javascript
class BatchRenderer {
  constructor(gl, texture) {
    this.gl = gl;
    this.texture = texture;
    this.maxSprites = 10000;
    this.vertexSize = 8; // x, y, u, v, r, g, b, a
    this.vertices = new Float32Array(this.maxSprites * 6 * this.vertexSize);
    this.vertexCount = 0;
    this.init();
  }

  init() {
    const gl = this.gl;
    this.vertexBuffer = gl.createBuffer();
    gl.bindBuffer(gl.ARRAY_BUFFER, this.vertexBuffer);
    gl.bufferData(gl.ARRAY_BUFFER, this.vertices.byteLength, gl.DYNAMIC_DRAW);

    this.indexBuffer = gl.createBuffer();
    const indices = this.createIndices();
    gl.bindBuffer(gl.ELEMENT_ARRAY_BUFFER, this.indexBuffer);
    gl.bufferData(gl.ELEMENT_ARRAY_BUFFER, indices, gl.STATIC_DRAW);

    this.initShader();
  }

  createIndices() {
    const indices = new Uint16Array(this.maxSprites * 6);
    for (let i = 0; i < this.maxSprites; i++) {
      const offset = i * 4;
      indices[i * 6]     = offset;
      indices[i * 6 + 1] = offset + 1;
      indices[i * 6 + 2] = offset + 2;
      indices[i * 6 + 3] = offset;
      indices[i * 6 + 4] = offset + 2;
      indices[i * 6 + 5] = offset + 3;
    }
    return indices;
  }

  // 添加精灵到批次
  addSprite(frame, x, y, w, h, color = [1,1,1,1]) {
    if (this.vertexCount / 6 >= this.maxSprites) {
      this.flush();
    }

    const uv = this.frameToUV(frame);
    let idx = this.vertexCount * this.vertexSize;

    // 4 个顶点（使用索引复用为 2 三角形）
    const vertices = [
      x,     y,     uv.u0, uv.v0, ...color,
      x + w, y,     uv.u1, uv.v0, ...color,
      x + w, y + h, uv.u1, uv.v1, ...color,
      x,     y + h, uv.u0, uv.v1, ...color
    ];

    for (const v of vertices) {
      this.vertices[idx++] = v;
    }
    this.vertexCount += 4;
  }

  frameToUV(frame) {
    const tw = this.texture.width, th = this.texture.height;
    return {
      u0: frame.sx / tw,
      v0: 1 - (frame.sy + frame.sh) / th,
      u1: (frame.sx + frame.sw) / tw,
      v1: 1 - frame.sy / th
    };
  }

  flush() {
    if (this.vertexCount === 0) return;
    const gl = this.gl;

    gl.bindBuffer(gl.ARRAY_BUFFER, this.vertexBuffer);
    gl.bufferSubData(gl.ARRAY_BUFFER, 0,
      this.vertices.subarray(0, this.vertexCount * this.vertexSize));

    gl.useProgram(this.program);
    gl.bindTexture(gl.TEXTURE_2D, this.texture);

    const indexCount = (this.vertexCount / 4) * 6;
    gl.drawElements(gl.TRIANGLES, indexCount, gl.UNSIGNED_SHORT, 0);

    this.vertexCount = 0;
  }
}
```

**使用：一次 DrawCall 渲染 1000 个精灵**：

```javascript
const batch = new BatchRenderer(gl, atlasTexture);

// 添加 1000 个精灵（同一图集）
for (let i = 0; i < 1000; i++) {
  const frame = parser.getFrame(`tile_${i % 16}`);
  batch.addSprite(frame, i * 32 % 800, Math.floor(i * 32 / 800) * 32, 32, 32);
}

batch.flush(); // 仅 1 次 DrawCall！
// 对比：逐精灵 drawImage 需 1000 次调用
```

#### 总结

- DrawCall 有固定 CPU 开销，是渲染性能的主要瓶颈
- 批量渲染将多精灵顶点合并，一次 DrawCall 渲染全部
- 前提：精灵需共享同一图集纹理
- 动态批处理：每帧重写顶点缓冲，适合动态场景
- 静态批处理：预生成缓存，适合不变场景
- 实例化渲染（WebGL2）：适合大量相同几何体
- 顶点数据布局：位置 + UV + 颜色（支持着色与透明度）

---

### 第24讲：图集尺寸与压缩策略

#### 概念

图集尺寸的选择直接影响内存占用、加载速度与渲染效率。过大的图集浪费显存且加载慢，过小的图集导致资源分散、DrawCall 增多。本讲讲解图集尺寸的选型原则、纹理压缩格式选择，以及 PNG/WebP/压缩纹理的权衡。

#### 原理

**尺寸选型原则**：
1. **遵循 2 的幂次（POT）**：1, 2, 4, ..., 256, 512, 1024, 2048, 4096。部分旧 GPU 要求 POT，现代 GPU 虽支持 NPOT（非 2 幂）但 mipmap 与压缩格式仍需 POT。
2. **不超过平台上限**：移动端通常 2048×2048（部分老旧设备 1024），桌面端 4096×4096 或更高。超出上限会被缩放或拒绝。
3. **按资源类别分图集**：角色、UI、特效分开，避免单图集过大且便于按需加载。
4. **留 10-20% 余量**：便于后续添加新精灵而不必重新分图集。

**压缩策略**：
- **PNG**：无损压缩，文件小但解码慢，运行时占满 RGBA 显存。适合静态资源。
- **WebP**：比 PNG 小 25-35%，解码速度接近，现代浏览器全支持。
- **JPEG**：有损，无透明通道，仅适合背景。
- **压缩纹理（ETC2/PVRTC/ASTC）**：GPU 直接解码，无需运行时解压，显存占用降低 75%+。但需离线工具生成，且不同平台格式不同。

**ASTC 格式**是现代移动端首选，质量与压缩率均优于 ETC2/PVRTC，iOS 与 Android 均支持。

#### 例子

**图集尺寸选型参考表**：

```javascript
const ATLAS_SIZE_GUIDE = {
  // 移动端
  mobile: {
    max: 2048,
    recommended: [512, 1024, 2048],
    compression: 'ASTC',  // 首选
    fallback: 'ETC2'      // Android 旧设备
  },
  // 桌面 Web
  desktop: {
    max: 4096,
    recommended: [1024, 2048, 4096],
    compression: 'RGBA8888', // 桌面显存充裕，用无损
    fallback: 'RGBA4444'
  },
  // 小程序/轻量
  mini: {
    max: 1024,
    recommended: [256, 512, 1024],
    compression: 'RGBA4444',
    fallback: 'RGB565'
  }
};

function chooseAtlasSize(spriteCount, avgSpriteSize, platform) {
  const guide = ATLAS_SIZE_GUIDE[platform];
  const totalPixels = spriteCount * avgSpriteSize * avgSpriteSize * 1.2; // 20% 余量
  const sideLength = Math.ceil(Math.sqrt(totalPixels));

  // 向上取整到最近的 POT
  let size = 256;
  while (size < sideLength && size < guide.max) {
    size *= 2;
  }
  return Math.min(size, guide.max);
}

// 示例：100 个 128×128 精灵，移动端
console.log(chooseAtlasSize(100, 128, 'mobile'));
// 总像素 100×128×128×1.2 = 1966080，边长 ≈ 1402，向上取整 → 2048
```

**TexturePacker 压缩格式配置**：

```
# ASTC 压缩（移动端首选）
TexturePacker --texture-format astc \
  --astc-quality medium \
  --astc-block-size 4x4 \
  --data atlas.json --sheet atlas.astc.png sprites/

# RGBA4444（桌面/通用）
TexturePacker --texture-format png \
  --png-optimize-colors \
  --dither-type-none \
  --data atlas.json --sheet atlas.png sprites/

# WebP（Web 传输优化）
TexturePacker --texture-format webp \
  --webp-quality 80 \
  --data atlas.json --sheet atlas.webp sprites/
```

**运行时根据平台选择图集**：

```javascript
async function loadOptimalAtlas(baseName) {
  const platform = detectPlatform();

  let extension;
  if (platform.isMobile && platform.supportsASTC) {
    extension = '-astc.ktx';  // 压缩纹理
  } else if (platform.supportsWebP) {
    extension = '.webp';
  } else {
    extension = '.png';
  }

  const imageUrl = `${baseName}${extension}`;
  const jsonUrl = `${baseName}.json`;

  const [image, json] = await Promise.all([
    loadImage(imageUrl),
    fetch(jsonUrl).then(r => r.json())
  ]);

  return { image, json };
}

function detectPlatform() {
  const canvas = document.createElement('canvas');
  const gl = canvas.getContext('webgl2') || canvas.getContext('webgl');
  const extensions = gl ? gl.getSupportedExtensions() : [];

  return {
    isMobile: /Android|iPhone|iPad/i.test(navigator.userAgent),
    supportsASTC: extensions?.some(e => e.includes('ASTC')),
    supportsWebP: (() => {
      const c = document.createElement('canvas');
      return c.toDataURL('image/webp').startsWith('data:image/webp');
    })()
  };
}
```

#### 总结

- 图集尺寸遵循 2 幂次，移动端上限 2048，桌面端 4096
- 按资源类别分图集，留 10-20% 余量
- PNG 无损但显存大，WebP 传输小，压缩纹理显存最小
- ASTC 是移动端首选，质量与压缩率均优
- 运行时检测平台能力，动态选择最优格式
- 桌面端显存充裕可用 RGBA8888 保证质量

---

### 第25讲：移动端适配与降级方案

#### 概念

移动端设备性能差异巨大——从高端 iPhone 到老旧 Android 低端机，GPU 能力、显存上限、纹理格式支持各不相同。SpriteSheet 系统需具备适配能力：检测设备能力，动态选择渲染策略、图集质量、批量大小，在低端设备上降级以保证帧率。本讲讲解移动端适配的检测与降级方案。

#### 原理

**能力检测**是适配的基础，包括：
1. **GPU 性能等级**：通过渲染基准测试或设备型号推断
2. **最大纹理尺寸**：`gl.getParameter(gl.MAX_TEXTURE_SIZE)`
3. **压缩纹理支持**：查询 WebGL 扩展列表
4. **设备像素比（DPR）**：高 DPR 设备需更高分辨率图集

**降级策略**按性能等级分层：
- **高端**：RGBA8888 无损、4096 图集、大批量（5000+ 精灵/批次）
- **中端**：RGBA4444、2048 图集、中批量（2000 精灵/批次）
- **低端**：RGB565/压缩纹理、1024 图集、小批量（500 精灵/批次）、降低动画帧率

**帧率自适应**：监测实际帧率，低于阈值（如 30fps）时自动降低批量大小或切换低质量图集。

#### 例子

**设备能力检测**：

```javascript
class DeviceCapability {
  constructor() {
    this.detect();
  }

  detect() {
    const canvas = document.createElement('canvas');
    const gl = canvas.getContext('webgl2') || canvas.getContext('webgl');

    this.maxTextureSize = gl ? gl.getParameter(gl.MAX_TEXTURE_SIZE) : 2048;
    this.supportsASTC = this.checkExtension(gl, 'ASTC');
    this.supportsETC2 = this.checkExtension(gl, 'ETC2');
    this.supportsPVRTC = this.checkExtension(gl, 'PVRTC');
    this.dpr = window.devicePixelRatio || 1;
    this.isMobile = /Android|iPhone|iPad/i.test(navigator.userAgent);
    this.isLowEnd = this.detectLowEnd();

    this.performanceLevel = this.assessPerformance();
  }

  checkExtension(gl, name) {
    if (!gl) return false;
    const exts = gl.getSupportedExtensions() || [];
    return exts.some(e => e.includes(name));
  }

  detectLowEnd() {
    // 简易判断：设备核心数与内存
    const cores = navigator.hardwareConcurrency || 4;
    const memory = navigator.deviceMemory || 4;
    return cores <= 4 || memory <= 2;
  }

  assessPerformance() {
    if (!this.isMobile && !this.isLowEnd) return 'high';
    if (this.isLowEnd) return 'low';
    return 'medium';
  }

  getRecommendedConfig() {
    const configs = {
      high: {
        textureFormat: 'RGBA8888',
        maxAtlasSize: 4096,
        batchSize: 5000,
        targetFPS: 60
      },
      medium: {
        textureFormat: 'RGBA4444',
        maxAtlasSize: 2048,
        batchSize: 2000,
        targetFPS: 60
      },
      low: {
        textureFormat: this.supportsASTC ? 'ASTC' : 'RGBA4444',
        maxAtlasSize: 1024,
        batchSize: 500,
        targetFPS: 30
      }
    };
    return configs[this.performanceLevel];
  }
}
```

**帧率自适应降级**：

```javascript
class AdaptiveRenderer {
  constructor(gl, device) {
    this.gl = gl;
    this.device = device;
    this.config = device.getRecommendedConfig();
    this.frameTimes = [];
    this.degraded = false;
  }

  monitorPerformance(deltaTime) {
    this.frameTimes.push(deltaTime);
    if (this.frameTimes.length > 60) {
      this.frameTimes.shift();
    }

    // 每 60 帧评估一次
    if (this.frameTimes.length === 60) {
      const avgFrameTime = this.frameTimes.reduce((a, b) => a + b) / 60;
      const avgFPS = 1000 / avgFrameTime;

      if (avgFPS < 30 && !this.degraded) {
        this.degrade();
      }
    }
  }

  degrade() {
    console.warn('帧率过低，降级渲染质量');
    this.degraded = true;
    this.config.batchSize = Math.floor(this.config.batchSize / 2);
    this.config.targetFPS = 30;
    // 可触发图集重新加载为低质量版本
    this.onDegrade?.();
  }
}
```

**按 DPR 加载合适图集**：

```javascript
async function loadAtlasForDPR(baseName, device) {
  // 高 DPR 设备加载 @2x 或 @3x 图集
  let suffix = '';
  if (device.dpr >= 3) suffix = '@3x';
  else if (device.dpr >= 2) suffix = '@2x';

  const config = device.getRecommendedConfig();
  const maxSize = Math.min(device.maxTextureSize, config.maxAtlasSize);

  const jsonUrl = `${baseName}${suffix}.json`;
  const imageUrl = `${baseName}${suffix}.${config.textureFormat === 'ASTC' ? 'ktx' : 'png'}`;

  return await loadAtlas(imageUrl, jsonUrl);
}
```

#### 总结

- 移动端设备差异大，需能力检测与动态适配
- 检测项：最大纹理尺寸、压缩格式支持、DPR、性能等级
- 降级分层：高端无损、中端 RGBA4444、低端压缩纹理 + 小批量
- 帧率自适应：监测 FPS，低于阈值自动降级
- 按 DPR 加载 @2x/@3x 图集保证清晰度
- 降级是保障低端设备可用性的关键策略

---

## 第七章：实战应用

### 第26讲：游戏角色动画系统实战

#### 概念

本讲将前述知识整合，实现一个完整的游戏角色动画系统。系统包含角色图集加载、多状态动画管理（待机、行走、跳跃、攻击）、键盘输入控制、帧事件触发（攻击伤害判定）、Canvas 渲染等模块。这是 2D 横版游戏的最小可运行原型，可作为实际游戏项目的起点。

#### 原理

角色动画系统的架构分为四层：
1. **资源层**：图集 PNG + JSON 元数据，定义所有动画帧
2. **解析层**：SpriteSheetParser 将元数据转为标准帧对象
3. **动画层**：状态机管理多动画切换，帧事件系统触发逻辑
4. **控制层**：键盘输入转换为状态机事件，驱动角色行为

数据流：键盘输入 → 控制层转换为事件 → 状态机切换动画 → 动画更新计算当前帧 → 渲染层绘制帧。帧事件在动画更新时触发，调用游戏逻辑（如伤害判定）。

角色配置应外部化（JSON），包含每个状态的帧序列、帧率、是否循环、帧事件等，便于策划调整而无需改代码。

#### 例子

**角色配置文件**：

```json
{
  "atlas": "hero.png",
  "data": "hero.json",
  "states": {
    "idle": {
      "frames": ["hero_idle_01","hero_idle_02","hero_idle_03","hero_idle_04"],
      "fps": 8, "loop": true
    },
    "walk": {
      "frames": ["hero_walk_01","hero_walk_02","hero_walk_03","hero_walk_04","hero_walk_05","hero_walk_06"],
      "fps": 12, "loop": true,
      "events": {
        "1": [{"type":"sound","name":"footstep.wav"}],
        "4": [{"type":"sound","name":"footstep.wav"}]
      }
    },
    "attack": {
      "frames": ["hero_atk_01","hero_atk_02","hero_atk_03","hero_atk_04","hero_atk_05"],
      "fps": 15, "loop": false,
      "events": {
        "2": [{"type":"sound","name":"swing.wav"}],
        "3": [{"type":"hitbox","x":40,"y":20,"w":60,"h":80,"damage":50}]
      }
    }
  },
  "transitions": [
    {"from":"idle","event":"move","to":"walk"},
    {"from":"walk","event":"stop","to":"idle"},
    {"from":"idle","event":"attack","to":"attack"},
    {"from":"walk","event":"attack","to":"attack"},
    {"from":"attack","event":"finish","to":"idle"}
  ]
}
```

**完整角色系统实现**：

```javascript
class GameCharacter {
  constructor(config, parser, renderer, soundManager, gameLogic) {
    this.parser = parser;
    this.renderer = renderer;
    this.soundManager = soundManager;
    this.gameLogic = gameLogic;
    this.x = 100;
    this.y = 300;
    this.facing = 1; // 1: 右, -1: 左

    this.asm = new AnimationStateMachine();
    this.setupStates(config);
    this.setupTransitions(config);
  }

  setupStates(config) {
    for (const [name, stateConfig] of Object.entries(config.states)) {
      const anim = new EventFrameAnimation(
        stateConfig.frames, stateConfig.fps, stateConfig.loop
      );
      // 注册帧事件
      if (stateConfig.events) {
        for (const [frameStr, events] of Object.entries(stateConfig.events)) {
          const frameIndex = parseInt(frameStr);
          for (const evt of events) {
            anim.addFrameEvent(frameIndex, () => this.handleEvent(evt));
          }
        }
      }
      // 非循环动画完成后触发 finish 事件
      if (!stateConfig.loop) {
        anim.onComplete = () => this.asm.trigger('finish');
      }
      this.asm.addState(name, anim);
    }
  }

  setupTransitions(config) {
    for (const t of config.transitions) {
      this.asm.addTransition(t.from, t.event, t.to);
    }
  }

  handleEvent(evt) {
    switch (evt.type) {
      case 'sound':
        this.soundManager.play(evt.name);
        break;
      case 'hitbox':
        // 根据朝向调整 hitbox 位置
        const hx = this.facing === 1 ? this.x + evt.x : this.x - evt.x - evt.w;
        this.gameLogic.spawnHitbox(hx, this.y + evt.y, evt.w, evt.h, evt.damage);
        break;
    }
  }

  update(deltaTime, input) {
    // 处理输入
    if (input.attack && this.asm.currentName !== 'attack') {
      this.asm.trigger('attack');
    } else if (this.asm.currentName !== 'attack') {
      // 攻击中不可移动
      if (input.left || input.right) {
        if (this.asm.currentName !== 'walk') this.asm.trigger('move');
        this.facing = input.left ? -1 : 1;
        this.x += (input.left ? -1 : 1) * 2;
      } else {
        if (this.asm.currentName === 'walk') this.asm.trigger('stop');
      }
    }

    this.asm.update(deltaTime);
  }

  render() {
    const frameName = this.asm.getCurrentFrame();
    const frame = this.parser.getFrame(frameName);

    // 根据朝向翻转
    this.renderer.ctx.save();
    if (this.facing === -1) {
      this.renderer.ctx.scale(-1, 1);
      this.renderer.ctx.translate(-this.x * 2 - frame.dw, 0);
    }
    this.renderer.draw(frame, this.x, this.y);
    this.renderer.ctx.restore();
  }
}
```

**主程序**：

```javascript
async function main() {
  const canvas = document.getElementById('game');
  const ctx = canvas.getContext('2d');
  const renderer = new CanvasSpriteRenderer(ctx, null);

  // 加载资源
  const config = await fetch('hero_config.json').then(r => r.json());
  const atlasImage = await loadImage(config.atlas);
  const jsonData = await fetch(config.data).then(r => r.json());
  const parser = new SpriteSheetParser(atlasImage, jsonData);
  renderer.atlas = atlasImage;

  // 创建角色
  const character = new GameCharacter(
    config, parser, renderer,
    soundManager, gameLogic
  );

  // 输入处理
  const input = { left: false, right: false, attack: false };
  window.addEventListener('keydown', (e) => {
    if (e.key === 'ArrowLeft') input.left = true;
    if (e.key === 'ArrowRight') input.right = true;
    if (e.key === ' ') input.attack = true;
  });
  window.addEventListener('keyup', (e) => {
    if (e.key === 'ArrowLeft') input.left = false;
    if (e.key === 'ArrowRight') input.right = false;
    if (e.key === ' ') input.attack = false;
  });

  // 主循环
  let lastTime = performance.now();
  function loop(now) {
    const dt = now - lastTime;
    lastTime = now;

    ctx.clearRect(0, 0, canvas.width, canvas.height);
    character.update(dt, input);
    character.render();

    requestAnimationFrame(loop);
  }
  requestAnimationFrame(loop);
}
main();
```

#### 总结

- 角色系统四层架构：资源 → 解析 → 动画 → 控制
- 配置外部化（JSON）便于策划调整
- 状态机管理动画切换，帧事件触发游戏逻辑
- 输入处理与动画状态分离，攻击中锁定移动
- 朝向翻转通过 `ctx.scale(-1, 1)` 实现
- 此架构可扩展为完整 2D 游戏的角色基础

---

### 第27讲：Web UI 精灵动画实战

#### 概念

SpriteSheet 在 Web 前端常用于 UI 动画——加载动画、按钮反馈、图标动效、引导提示等。与游戏角色动画不同，UI 动画通常规模小、交互简单，但要求与 DOM 良好集成、响应式适配、低性能开销。本讲实现一套 Web UI 精灵动画组件，支持声明式使用、自动播放、交互触发。

#### 原理

Web UI 精灵动画有两种实现路径：
1. **纯 CSS 方案**：用 `@keyframes` + `steps()` 切换 `background-position`，零 JS、性能最优，但灵活性低（无法动态控制帧、事件触发）
2. **Canvas/DOM 方案**：用 JS 控制帧切换，可动态播放/暂停/跳转、触发事件，灵活但需 JS 开销

实际项目常混合使用：简单循环动画用 CSS，需交互控制的用 JS。

声明式 UI 组件设计：通过 HTML `data-*` 属性或自定义元素声明动画配置，JS 自动初始化。这使设计师能在 HTML 中直接使用，无需写 JS。

响应式适配：根据容器尺寸与 DPR 选择合适图集分辨率，CSS 控制显示尺寸。

#### 例子

**声明式 UI 组件**：

```html
<!-- 自动播放的加载动画 -->
<div class="sprite-ui"
     data-atlas="loader.png"
     data-frames="8"
     data-fps="12"
     data-loop="true"
     data-width="64"
     data-height="64">
</div>

<!-- 点击触发的按钮特效 -->
<button class="sprite-button"
        data-atlas="btn_effect.png"
        data-frames="12"
        data-fps="20"
        data-loop="false"
        data-trigger="click"
        data-width="120"
        data-height="40">
  点击有特效
</button>
```

**组件初始化 JS**：

```javascript
class SpriteUI {
  static init(selector = '.sprite-ui, .sprite-button') {
    document.querySelectorAll(selector).forEach(el => {
      new SpriteUI(el);
    });
  }

  constructor(element) {
    this.el = element;
    this.atlas = element.dataset.atlas;
    this.frameCount = parseInt(element.dataset.frames);
    this.fps = parseInt(element.dataset.fps) || 12;
    this.loop = element.dataset.loop === 'true';
    this.width = parseInt(element.dataset.width);
    this.height = parseInt(element.dataset.height);
    this.trigger = element.dataset.trigger || 'auto';

    this.currentFrame = 0;
    this.elapsed = 0;
    this.playing = this.trigger === 'auto';
    this.onComplete = null;

    this.setupElement();
    this.loadAtlas();

    if (this.trigger !== 'auto') {
      this.el.addEventListener(this.trigger, () => this.play());
    }
  }

  setupElement() {
    this.el.style.width = this.width + 'px';
    this.el.style.height = this.height + 'px';
    this.el.style.backgroundRepeat = 'no-repeat';
    this.el.style.display = 'inline-block';
  }

  async loadAtlas() {
    this.image = await loadImage(this.atlas);
    // 假设网格排列，单行
    this.frameW = this.image.width / this.frameCount;
    this.frameH = this.image.height;
    if (this.playing) this.startLoop();
  }

  play() {
    this.currentFrame = 0;
    this.elapsed = 0;
    this.playing = true;
    this.startLoop();
  }

  startLoop() {
    if (this.rafId) return;
    let lastTime = performance.now();
    const loop = (now) => {
      const dt = now - lastTime;
      lastTime = now;
      this.update(dt);
      if (this.playing) {
        this.rafId = requestAnimationFrame(loop);
      } else {
        this.rafId = null;
      }
    };
    this.rafId = requestAnimationFrame(loop);
  }

  update(dt) {
    this.elapsed += dt;
    const frameDuration = 1000 / this.fps;
    const frameIndex = Math.floor(this.elapsed / frameDuration);

    if (this.loop) {
      this.currentFrame = frameIndex % this.frameCount;
    } else {
      this.currentFrame = Math.min(frameIndex, this.frameCount - 1);
      if (this.currentFrame >= this.frameCount - 1) {
        this.playing = false;
        this.onComplete?.();
      }
    }

    this.render();
  }

  render() {
    const x = -this.currentFrame * this.frameW;
    this.el.style.backgroundPosition = `${x}px 0`;
    this.el.style.backgroundImage = `url(${this.atlas})`;
  }

  destroy() {
    if (this.rafId) cancelAnimationFrame(this.rafId);
  }
}

// 自动初始化
document.addEventListener('DOMContentLoaded', () => {
  SpriteUI.init();
});
```

**纯 CSS 方案（性能最优）**：

```css
/* 声明式：只需添加 class */
.sprite-loader {
  width: 64px;
  height: 64px;
  background-image: url('loader.png');
  background-repeat: no-repeat;
  animation: sprite-play 0.8s steps(8) infinite;
}

@keyframes sprite-play {
  from { background-position: 0 0; }
  to   { background-position: -512px 0; } /* 8帧 × 64px */
}

/* 悬停触发的按钮特效 */
.sprite-btn {
  width: 120px;
  height: 40px;
  background-image: url('btn_effect.png');
  background-repeat: no-repeat;
  background-position: 0 0;
  transition: none;
}
.sprite-btn:hover {
  animation: btn-effect 0.6s steps(12) 1;
}
@keyframes btn-effect {
  from { background-position: 0 0; }
  to   { background-position: -1440px 0; } /* 12帧 × 120px */
}
```

```html
<div class="sprite-loader"></div>
<button class="sprite-btn">悬停看特效</button>
```

#### 总结

- Web UI 精灵动画两种路径：纯 CSS（性能优、灵活性低）与 JS（灵活、可交互）
- 声明式组件通过 `data-*` 属性配置，设计师友好
- CSS 方案用 `@keyframes` + `steps()` 实现逐帧动画
- JS 方案支持播放控制、事件回调、动态帧率
- 响应式适配：按 DPR 与容器尺寸选择图集分辨率
- 适合加载动画、按钮反馈、图标动效、引导提示等场景

---

### 第28讲：完整项目案例——从零到一

#### 概念

本讲作为课程总结，将所有知识整合为一个完整项目——"精灵动画演示平台"。项目包含资源打包、多格式解析、Canvas/WebGL 双渲染器、动画状态机、帧事件、性能监控、移动端适配等全部模块，是 SpriteSheet 技术栈的综合应用。通过此项目，读者可建立完整的知识体系与实践能力。

#### 原理

完整项目的架构遵循**分层解耦**原则：

```
┌─────────────────────────────────────┐
│         应用层（主程序、UI）          │
├─────────────────────────────────────┤
│  控制层（输入处理、状态机驱动）        │
├─────────────────────────────────────┤
│  动画层（FrameAnimation、状态机、事件）│
├─────────────────────────────────────┤
│  渲染层（CanvasRenderer、BatchRenderer）│
├─────────────────────────────────────┤
│  解析层（格式适配器、Parser）          │
├─────────────────────────────────────┤
│  资源层（图集加载、纹理管理、缓存）     │
└─────────────────────────────────────┘
```

每层只依赖下层接口，不跨层调用。这种设计使各模块可独立替换——如切换渲染器不影响动画逻辑，更换解析格式不影响渲染。

项目还应包含**开发工具**：性能监控面板（FPS、DrawCall、内存）、图集预览器、动画调试器，便于开发期排查问题。

#### 例子

**项目目录结构**：

```
sprite-demo/
├── src/
│   ├── core/                    # 核心层
│   │   ├── Parser.js            # 解析器与适配器
│   │   ├── TextureManager.js    # 纹理管理
│   │   └── AssetLoader.js       # 资源加载
│   ├── render/                  # 渲染层
│   │   ├── CanvasRenderer.js    # Canvas 渲染器
│   │   ├── BatchRenderer.js     # WebGL 批量渲染
│   │   └── RendererFactory.js   # 渲染器工厂
│   ├── animation/               # 动画层
│   │   ├── FrameAnimation.js    # 帧动画
│   │   ├── StateMachine.js      # 状态机
│   │   └── FrameEvent.js        # 帧事件
│   ├── game/                    # 应用层
│   │   ├── Character.js         # 角色控制
│   │   ├── InputManager.js      # 输入管理
│   │   └── GameLoop.js          # 主循环
│   ├── tools/                   # 开发工具
│   │   ├── PerfMonitor.js       # 性能监控
│   │   └── AtlasViewer.js       # 图集预览
│   └── main.js                  # 入口
├── assets/
│   ├── sprites/                 # 源图
│   └── atlases/                 # 打包后图集
├── tools/
│   └── pack.config.json         # 打包配置
├── index.html
└── package.json
```

**主程序入口**：

```javascript
// src/main.js
import { AssetLoader } from './core/AssetLoader';
import { RendererFactory } from './render/RendererFactory';
import { Character } from './game/Character';
import { InputManager } from './game/InputManager';
import { GameLoop } from './game/GameLoop';
import { PerfMonitor } from './tools/PerfMonitor';
import { DeviceCapability } from './core/DeviceCapability';

async function main() {
  // 1. 检测设备能力
  const device = new DeviceCapability();
  const config = device.getRecommendedConfig();
  console.log('设备性能等级:', device.performanceLevel, config);

  // 2. 初始化渲染器（根据设备选择 Canvas 或 WebGL）
  const canvas = document.getElementById('canvas');
  const renderer = RendererFactory.create(canvas, device);

  // 3. 加载资源
  const loader = new AssetLoader();
  const assets = await loader.loadManifest('assets/manifest.json');
  // manifest.json 列出所有图集及其格式

  // 4. 注册图集到渲染器
  for (const atlas of assets) {
    renderer.registerAtlas(atlas.id, atlas.image, atlas.frames);
  }

  // 5. 创建角色
  const heroConfig = await loader.loadConfig('hero_config.json');
  const hero = new Character(heroConfig, renderer, 100, 300);

  // 6. 输入管理
  const input = new InputManager();
  input.bindKeys({
    'ArrowLeft': 'left',
    'ArrowRight': 'right',
    ' ': 'attack'
  });

  // 7. 性能监控
  const perf = new PerfMonitor();
  perf.attachToDOM(document.getElementById('perf-panel'));

  // 8. 主循环
  const loop = new GameLoop();
  loop.onUpdate((dt) => {
    hero.update(dt, input.getState());
    perf.update(dt);
  });
  loop.onRender(() => {
    renderer.clear();
    hero.render();
    renderer.flush();
    perf.render();
  });
  loop.start();
}

main().catch(err => console.error('启动失败:', err));
```

**渲染器工厂（自动选择 Canvas 或 WebGL）**：

```javascript
// src/render/RendererFactory.js
import { CanvasSpriteRenderer } from './CanvasRenderer';
import { BatchRenderer } from './BatchRenderer';

export class RendererFactory {
  static create(canvas, device) {
    // 低端设备用 Canvas（兼容性好、开销稳定）
    // 中高端用 WebGL（批量渲染、高性能）
    if (device.performanceLevel === 'low') {
      console.log('使用 Canvas 渲染器');
      return new CanvasSpriteRenderer(canvas);
    }

    const gl = canvas.getContext('webgl2') || canvas.getContext('webgl');
    if (gl) {
      console.log('使用 WebGL 批量渲染器');
      return new WebGLRendererAdapter(gl, device);
    }

    console.log('WebGL 不可用，回退 Canvas');
    return new CanvasSpriteRenderer(canvas);
  }
}

// WebGL 渲染器适配（统一接口）
class WebGLRendererAdapter {
  constructor(gl, device) {
    this.gl = gl;
    this.device = device;
    this.textureManager = new TextureManager(gl);
    this.batchRenderers = new Map(); // atlasId -> BatchRenderer
    this.currentBatch = null;
  }

  registerAtlas(atlasId, image, frames) {
    const texture = this.textureManager.getTexture(image);
    const batch = new BatchRenderer(this.gl, texture);
    batch.atlasId = atlasId;
    batch.frames = frames;
    this.batchRenderers.set(atlasId, batch);
  }

  draw(frame, x, y) {
    const batch = this.batchRenderers.get(frame.atlasId);
    if (this.currentBatch !== batch) {
      this.currentBatch?.flush();
      this.currentBatch = batch;
    }
    batch.addSprite(frame, x, y, frame.dw, frame.dh);
  }

  flush() {
    this.currentBatch?.flush();
    this.currentBatch = null;
  }

  clear() {
    this.gl.clear(this.gl.COLOR_BUFFER_BIT);
  }
}
```

**性能监控工具**：

```javascript
// src/tools/PerfMonitor.js
export class PerfMonitor {
  constructor() {
    this.fps = 0;
    this.frameCount = 0;
    this.lastFpsUpdate = performance.now();
    this.drawCalls = 0;
    this.memoryUsage = 0;
  }

  update(dt) {
    this.frameCount++;
    const now = performance.now();
    if (now - this.lastFpsUpdate >= 1000) {
      this.fps = this.frameCount;
      this.frameCount = 0;
      this.lastFpsUpdate = now;
      this.updateDisplay();
    }
  }

  render() {
    // 可绘制 FPS 图表等
  }

  updateDisplay() {
    if (this.panel) {
      this.panel.innerHTML = `
        <div>FPS: ${this.fps}</div>
        <div>DrawCalls: ${this.drawCalls}</div>
        <div>内存: ${this.memoryUsage}MB</div>
      `;
    }
  }

  attachToDOM(element) {
    this.panel = element;
    this.panel.style.cssText = `
      position: fixed; top: 10px; right: 10px;
      background: rgba(0,0,0,0.7); color: #0f0;
      padding: 8px 12px; font-family: monospace;
      font-size: 12px; border-radius: 4px;
      pointer-events: none; z-index: 9999;
    `;
  }
}
```

**资源清单文件**：

```json
// assets/manifest.json
{
  "atlases": [
    {
      "id": "characters",
      "image": "assets/atlases/characters.png",
      "data": "assets/atlases/characters.json",
      "format": "json-hash"
    },
    {
      "id": "ui",
      "image": "assets/atlases/ui.png",
      "data": "assets/atlases/ui.json",
      "format": "json-hash"
    },
    {
      "id": "effects",
      "image": "assets/atlases/effects.png",
      "data": "assets/atlases/effects.json",
      "format": "json-hash"
    }
  ],
  "configs": [
    "hero_config.json",
    "enemy_config.json"
  ]
}
```

#### 总结

- 完整项目遵循分层解耦架构：资源 → 解析 → 渲染 → 动画 → 控制 → 应用
- 每层只依赖下层接口，模块可独立替换
- 渲染器工厂根据设备能力自动选择 Canvas 或 WebGL
- 资源清单（manifest）统一管理所有图集与配置
- 开发工具（性能监控、图集预览）是开发期必备
- 此架构可作为实际游戏或复杂 Web 动画项目的基础框架

---

## 课程结语

本教程从 SpriteSheet 的基础概念出发，系统讲解了制作工具、数据格式、渲染技术、动画系统、性能优化与实战应用，共 28 讲。通过本课程，您应已掌握：

1. **核心原理**：理解 SpriteSheet 为何能提升性能（减少纹理切换、降低 DrawCall）
2. **完整工作流**：从美术设计到打包、加载、渲染、动画的全链路
3. **多种渲染方式**：Canvas、WebGL、CSS 三种技术路径及适用场景
4. **动画系统设计**：帧动画、状态机、混合过渡、帧事件的完整体系
5. **性能优化策略**：批量渲染、纹理压缩、移动端适配、降级方案
6. **实战能力**：能够独立构建游戏角色动画与 Web UI 精灵项目

SpriteSheet 虽是经典技术，但在 2D 游戏与 Web 动画领域仍是不可或缺的基础。掌握本课程内容后，建议进一步学习：
- **Spine / DragonBones**：基于骨骼的 2D 动画（比帧动画更节省资源）
- **Live2D**：基于网格变形的 2D 动画（适合角色表情）
- **WebGPU**：下一代图形 API，性能优于 WebGL
- **游戏引擎内置方案**：Phaser、PixiJS、Cocos Creator 的 SpriteSheet 系统

希望本教程能为您打开 2D 渲染与动画的大门，祝您在游戏与前端开发之路上不断精进。
