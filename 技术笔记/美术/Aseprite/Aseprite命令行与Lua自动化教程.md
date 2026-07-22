# Aseprite 命令行与 Lua 自动化完整教程

> 本教程以教科书形式系统讲解 Aseprite 的命令行接口与 Lua 脚本 API，共 24 讲，分为 7 章。
> 每讲包含「概念」「原理」「例子」「总结」四个标准化部分，从环境配置到实战项目，循序渐进。

---

## 课程总览

- **预计讲数**：24 讲
- **章节划分**：7 章，每章 2-5 讲
- **学习目标**：
  1. 掌握 Aseprite 命令行接口（CLI）的各类参数与批处理模式
  2. 深入理解 Aseprite 的对象模型（Sprite、Image、Cel、Layer、Frame）
  3. 能够使用 Lua 脚本 API 进行像素级图像操作
  4. 掌握调色板、选区、绘图工具、动画时间轴的编程控制
  5. 能够开发完整的 Aseprite 插件（含 GUI 对话框与菜单集成）
  6. 构建批处理流水线，实现像素艺术资产的自动化生产
  7. 将命令行与 Lua 脚本结合，打造高效的内容创作工作流

---

## 详细章节目录

### 第一章：Aseprite 自动化入门
- 第 1 讲：Aseprite 自动化概述与环境配置
- 第 2 讲：命令行基础与批处理模式
- 第 3 讲：Lua 脚本运行机制与第一个脚本

### 第二章：Aseprite 对象模型
- 第 4 讲：app 全局对象与活动文档
- 第 5 讲：Sprite（精灵）对象与文件操作
- 第 6 讲：Image（图像）对象与像素操作
- 第 7 讲：Cel（单元格）与帧管理
- 第 8 讲：Layer（图层）管理

### 第三章：命令行深度应用
- 第 9 讲：命令行导出与格式转换
- 第 10 讲：命令行批处理工作流
- 第 11 讲：命令行与脚本协同

### 第四章：Lua API 核心操作
- 第 12 讲：颜色与调色板操作
- 第 13 讲：选区与变换操作
- 第 14 讲：绘图工具 API
- 第 15 讲：帧动画与时间轴操作

### 第五章：高级自动化
- 第 16 讲：批量处理与文件遍历
- 第 17 讲：图像分析与像素统计
- 第 18 讲：切片（Slice）与导出自动化

### 第六章：插件与扩展开发
- 第 19 讲：插件结构与 package.json
- 第 20 讲：对话框 GUI 开发（Dialog）
- 第 21 讲：命令注册与菜单集成

### 第七章：实战项目
- 第 22 讲：实战：批量生成精灵图集（Sprite Sheet）
- 第 23 讲：实战：程序化像素艺术生成器
- 第 24 讲：实战：动画批处理流水线

---

# 第一章：Aseprite 自动化入门

Aseprite 是一款专为像素艺术设计的图像编辑器，广泛应用于游戏开发、独立游戏、像素动画等领域。除了图形界面，Aseprite 还提供强大的命令行接口和 Lua 脚本 API，使其成为可编程的自动化工具。本章将带您搭建自动化环境，理解基本运行机制。

---

## 第 1 讲：Aseprite 自动化概述与环境配置

### 概念

Aseprite 的自动化能力体现在两个层面：**命令行接口**（CLI）和 **Lua 脚本 API**。命令行接口允许通过终端命令执行打开文件、导出图像、转换格式等操作，无需启动图形界面；Lua 脚本 API 则提供了对 Aseprite 内部对象模型的完整编程访问能力，可以操作像素、图层、帧、调色板等所有元素。两者可以协同工作——命令行可以调用 Lua 脚本，Lua 脚本也可以调用命令行命令。

### 原理

Aseprite 本身是用 C++ 编写的桌面应用，其内部有一套完整的对象模型（Sprite、Image、Layer、Cel、Frame 等）。命令行接口是对这些对象的批量操作封装，而 Lua 脚本 API 则通过 Lua 解释器直接绑定到 C++ 对象。Aseprite 使用 Lua 5.1 作为脚本引擎，并扩展了大量 Aseprite 专有的 API（如 `app`、`Sprite`、`Image` 等全局对象）。

命令行模式通过 `-b` 或 `--batch` 参数启用，此时 Aseprite 不启动 GUI，仅执行命令行指定的操作后退出。这种模式非常适合集成到 CI/CD 流水线、构建脚本或批处理任务中。Lua 脚本则可以通过 `File > Scripts` 菜单在 GUI 中运行，也可以通过命令行 `--script` 参数在批处理模式下运行。

### 例子

**示例 1：验证 Aseprite 安装与命令行可用性**

```bash
# 查看 Aseprite 版本
aseprite --version

# 查看帮助（列出所有命令行参数）
aseprite --help
```

典型输出：
```
Aseprite 1.3.7-x64
Usage:
  aseprite [options] [files] ...
Options:
  -b, --batch                batch mode
  --script <filename>        execute script
  --script-param key=value   pass parameter to script
  -s, --sheet <filename>     export sprite sheet
  ...
```

**示例 2：配置脚本目录**

Aseprite 默认从用户配置目录加载脚本。可以通过 GUI 配置：

```
Edit > Preferences > Extensions > Scripts
```

或在 `aseprite.ini` 中设置：
```ini
[scripts]
path=/home/user/aseprite-scripts
```

将 Lua 脚本放入该目录后，即可在 `File > Scripts` 菜单中看到并运行。

**示例 3：第一个命令行操作——转换文件格式**

```bash
# 将 PNG 转换为 GIF
aseprite -b input.png --save-as output.gif

# 将 Aseprite 文件导出为 PNG 序列
aseprite -b character.aseprite --save-as frames_{frame0001}.png
```

**示例 4：第一个 Lua 脚本——Hello World**

创建文件 `hello.lua`：
```lua
-- hello.lua
-- 在 Aseprite 中通过 File > Scripts > hello 运行
app.alert("Hello, Aseprite!")

-- 打印到控制台（批处理模式下可见）
print("当前活动精灵:", app.activeSprite)
```

在 GUI 中运行：`File > Scripts > hello`

在命令行中运行：
```bash
aseprite -b --script hello.lua
```

**示例 5：检查脚本环境信息**

```lua
-- env_info.lua
-- 输出 Aseprite 脚本环境的详细信息
print("=== Aseprite 脚本环境 ===")
print("版本:", app.version)
print("API 版本:", app.apiVersion)
print("当前平台:", app.os)
print("活动精灵:", app.activeSprite and app.activeSprite.filename or "无")
print("活动图像:", app.activeImage and "存在" or "无")
print("========================")

-- 在 GUI 中显示信息对话框
local dlg = Dialog("环境信息")
dlg:label{label="版本", text=app.version}
dlg:label{label="API", text=tostring(app.apiVersion)}
dlg:button{text="确定"}
dlg:show()
```

### 总结

- Aseprite 自动化有两条路径：命令行接口（CLI）和 Lua 脚本 API。
- 命令行通过 `-b` 进入批处理模式，适合无 GUI 的自动化任务。
- Lua 脚本通过 `File > Scripts` 菜单或 `--script` 参数运行。
- `app` 是 Lua 脚本中的核心全局对象，提供对 Aseprite 状态的访问。
- **注意事项**：确保 Aseprite 可执行文件在系统 PATH 中；脚本目录需正确配置；不同版本的 API 可能有差异，注意查看 `app.apiVersion`。

---

## 第 2 讲：命令行基础与批处理模式

### 概念

Aseprite 命令行接口允许在终端中执行各种图像操作。**批处理模式**（batch mode）通过 `-b` 参数启用，此时 Aseprite 不显示图形界面，仅执行命令行指定的操作。批处理模式支持打开文件、应用命令、导出图像、运行脚本等操作，是自动化工作流的基础。

### 原理

Aseprite 命令行的核心参数包括：
- `-b` / `--batch`：启用批处理模式，不启动 GUI。
- `<files>`：直接指定要打开的文件。
- `--save-as <file>`：将当前打开的文件另存为指定格式。
- `--script <file>`：执行 Lua 脚本。
- `--script-param key=value`：向脚本传递参数。
- `-s` / `--sheet <file>`：导出精灵图集。
- `--sheet-type <type>`：图集类型（horizontal、vertical、rows、columns、packed）。
- `--split-layers`：按图层拆分导出。
- `--split-layers`、`--split-tags`：按图层/标签拆分。

命令行的执行流程：Aseprite 启动 → 进入批处理模式 → 打开指定文件 → 按顺序执行命令行参数 → 退出。多个文件和操作可以串联执行。文件格式由扩展名自动推断（`.aseprite`、`.png`、`.gif`、`.bmp`、`.jpg` 等）。

### 例子

**示例 1：基本文件转换**

```bash
# PNG 转 GIF
aseprite -b input.png --save-as output.gif

# Aseprite 文件导出为 PNG
aseprite -b character.aseprite --save-as character.png

# 转换为 BMP
aseprite -b sprite.png --save-as sprite.bmp
```

**示例 2：导出动画帧序列**

```bash
# 导出所有帧为 PNG 序列
# {frame} 是占位符，会被替换为帧编号
aseprite -b animation.aseprite --save-as frame_{frame0001}.png

# 导出为 4 位编号
aseprite -b animation.aseprite --save-as f_{frame0000}.png
```

**示例 3：导出精灵图集（Sprite Sheet）**

```bash
# 水平排列的图集
aseprite -b character.aseprite --sheet sheet.png --sheet-type horizontal

# 打包式图集（节省空间）
aseprite -b character.aseprite --sheet sheet_packed.png --sheet-type packed

# 同时导出图集数据（JSON）
aseprite -b character.aseprite \
  --sheet sheet.png \
  --sheet-type packed \
  --data sheet.json
```

**示例 4：按图层拆分导出**

```bash
# 每个图层导出为单独的 PNG
aseprite -b character.aseprite --split-layers --save-as layer_{layer}.png

# 按标签（动画片段）拆分
aseprite -b character.aseprite --split-tags --save-as tag_{tag}_{frame0001}.png
```

**示例 5：批量处理多个文件**

```bash
# 使用 shell 循环批量转换
for f in *.aseprite; do
    aseprite -b "$f" --save-as "${f%.aseprite}.png"
done

# 使用 find 命令递归处理
find . -name "*.aseprite" -exec sh -c '
    aseprite -b "$1" --save-as "${1%.aseprite}.png"
' _ {} \;
```

**示例 6：传递参数给脚本**

```bash
# 向脚本传递参数
aseprite -b --script process.lua \
  --script-param input=character.png \
  --script-param output=result.png \
  --script-param scale=2
```

在脚本中读取参数：
```lua
-- process.lua
local input = app.params["input"]
local output = app.params["output"]
local scale = tonumber(app.params["scale"]) or 1

print("输入:", input)
print("输出:", output)
print("缩放:", scale)
```

### 总结

- `-b` 启用批处理模式，是命令行自动化的基础。
- `--save-as` 导出文件，格式由扩展名决定。
- `{frame}`、`{layer}`、`{tag}` 是导出时的占位符。
- `--sheet` + `--data` 组合导出图集及其 JSON 元数据。
- `--script-param` 向脚本传递键值对参数，通过 `app.params` 读取。
- **注意事项**：批处理模式下错误不会弹出对话框，需检查退出码；路径含空格时需用引号包裹；`{frame0001}` 中的数字表示编号位数。

---

## 第 3 讲：Lua 脚本运行机制与第一个脚本

### 概念

Aseprite 的 Lua 脚本运行在其内置的 Lua 5.1 解释器中，并扩展了 Aseprite 专有的 API。脚本可以访问 `app` 全局对象及其下属的所有功能，包括活动文档、命令系统、对话框系统等。理解脚本的运行机制、作用域和生命周期是编写可靠自动化脚本的基础。

### 原理

Aseprite 脚本的运行环境基于 Lua 5.1，但注入了大量 Aseprite 专有的全局对象和类：
- `app`：核心全局对象，提供对 Aseprite 状态和命令的访问。
- `Sprite`、`Image`、`Layer`、`Cel`、`Frame`、`Color`、`Palette` 等类。
- `Dialog`：用于创建 GUI 对话框。
- `Selection`：选区操作。
- `app.command`：调用 Aseprite 内置命令（如菜单项对应的功能）。
- `app.params`：命令行传递的参数表。

脚本的执行方式有两种：
1. **GUI 模式**：通过 `File > Scripts` 菜单运行，脚本可以访问和修改当前打开的活动文档，可以显示对话框与用户交互。
2. **批处理模式**：通过 `--script` 参数运行，通常无 GUI 交互，适合自动化任务。

脚本执行时，Aseprite 会创建一个新的 Lua 协程来运行脚本，脚本结束后协程销毁。脚本中对文档的修改会立即反映到 Aseprite 的撤销/重做历史中（每个脚本默认作为一个撤销步骤）。通过 `app.transaction` 可以将多个操作合并为一个撤销步骤。

### 例子

**示例 1：第一个实用脚本——新建精灵并绘制**

```lua
-- first_script.lua
-- 创建一个 16x16 的精灵并填充背景色

local sprite = Sprite(16, 16, ColorMode.INDEXED)
app.command.ColorMode{mode="indexed"}

-- 获取第一帧的第一个 cel
local cel = sprite.cels[1]

-- 填充颜色（索引 0）
cel.image:drawSprite(0, 0, 0)  -- 这会出错，正确方式见下

-- 正确的填充方式
local img = cel.image
for y = 0, img.height - 1 do
    for x = 0, img.width - 1 do
        img:drawPixel(x, y, 1)  -- 用调色板索引 1 填充
    end
end

app.alert("精灵创建完成！")
```

**示例 2：使用 app.command 调用内置命令**

```lua
-- commands.lua
-- 演示通过 Lua 调用 Aseprite 菜单命令

-- 新建文件
app.command.NewFile{
    ui = false,
    width = 32,
    height = 32,
    colorMode = ColorMode.RGB
}

-- 翻转画布
app.command.Flip{target="mask", orientation="horizontal"}

-- 反转颜色
app.command.InvertColor()

-- 调整大小
app.command.CanvasSize{
    ui=false,
    width=64,
    height=64,
    bounds=Rectangle(0, 0, 64, 64)
}

print("命令执行完成")
```

**示例 3：使用事务（Transaction）合并撤销步骤**

```lua
-- transaction.lua
-- 将多个操作合并为一个撤销步骤

local sprite = app.activeSprite
if not sprite then
    app.alert("请先打开一个精灵")
    return
end

-- 使用事务，所有操作合并为一个撤销步骤
app.transaction(function()
    local cel = sprite.cels[1]
    local img = cel.image

    -- 绘制多个像素
    for i = 0, 15 do
        img:drawPixel(i, i, Color(255, 0, 0, 255))
    end

    -- 添加新帧
    sprite:newFrame()

    -- 添加新图层
    sprite:newLayer()
end)

app.alert("所有操作已合并为一个撤销步骤")
```

**示例 4：脚本中的错误处理**

```lua
-- error_handling.lua
-- 演示脚本中的错误处理

local function safeOperation(fn, desc)
    local ok, err = pcall(fn)
    if not ok then
        app.alert("操作失败 [" .. desc .. "]: " .. tostring(err))
        return false
    end
    return true
end

-- 使用示例
safeOperation(function()
    local sprite = app.activeSprite
    if not sprite then
        error("没有活动精灵")
    end
    -- 执行操作...
    sprite:newFrame()
end, "添加帧")

print("脚本执行完毕")
```

**示例 5：批处理模式下的脚本**

```lua
-- batch_script.lua
-- 设计为通过命令行运行
-- aseprite -b --script batch_script.lua --script-param file=input.png

local filename = app.params["file"]
if not filename then
    print("错误: 未指定 file 参数")
    os.exit(1)
end

print("处理文件:", filename)

-- 打开文件
local sprite = app.open(filename)
if not sprite then
    print("错误: 无法打开文件")
    os.exit(1)
end

-- 执行操作：调整大小为 2 倍
app.command.SpriteSize{
    ui=false,
    scale=2,
    method="nearest-neighbor"
}

-- 保存
local output = filename:gsub("%.%w+$", "_2x.png")
sprite:saveCopyAs(output)
print("已保存:", output)

-- 关闭
sprite:close()
print("完成")
```

运行方式：
```bash
aseprite -b --script batch_script.lua --script-param file=character.png
```

### 总结

- Aseprite 脚本基于 Lua 5.1，扩展了 `app`、`Sprite`、`Image` 等专有 API。
- 脚本可通过 GUI 菜单或命令行 `--script` 运行。
- `app.command` 可调用几乎所有 Aseprite 内置命令。
- `app.transaction` 将多个操作合并为一个撤销步骤，提升用户体验。
- `app.params` 读取命令行传递的参数，实现参数化脚本。
- **注意事项**：脚本中的错误默认会中断执行，重要操作应使用 `pcall` 包裹；批处理模式下用 `print` 输出日志，用 `os.exit` 控制退出码。

---

# 第二章：Aseprite 对象模型

Aseprite 的所有数据都以对象的形式组织。理解对象模型是编写自动化脚本的核心——Sprite（精灵）包含 Layer（图层）和 Frame（帧），每个 Layer 与 Frame 的交叉点是一个 Cel（单元格），Cel 持有 Image（图像）数据。本章将系统讲解这些核心对象及其关系。

---

## 第 4 讲：app 全局对象与活动文档

### 概念

`app` 是 Aseprite Lua 脚本中的核心全局对象，它是访问所有 Aseprite 功能的入口。通过 `app` 可以获取当前活动精灵（`app.activeSprite`）、活动图像（`app.activeImage`）、活动图层（`app.activeLayer`）、活动帧（`app.activeFrame`）等。`app` 还提供了命令系统（`app.command`）、参数系统（`app.params`）、撤销/重做控制（`app.undo`/`app.redo`）等功能。

### 原理

Aseprite 采用"活动文档"（active document）的概念：无论何时，GUI 中当前聚焦的精灵就是"活动精灵"。Lua 脚本默认操作活动精灵，这简化了大多数脚本的编写——用户在 GUI 中打开文件，然后运行脚本，脚本自动作用于该文件。

`app` 对象的主要属性：
- `app.activeSprite`：当前活动的 Sprite 对象，无活动精灵时为 `nil`。
- `app.activeImage`：当前活动 Cel 的 Image 对象。
- `app.activeLayer`：当前活动图层。
- `app.activeFrame`：当前活动帧。
- `app.activeCel`：当前活动 Cel（图层与帧的交叉）。
- `app.activeTag`：当前活动的标签。
- `app.sprites`：所有打开的精灵列表。
- `app.command`：命令系统，可调用菜单功能。
- `app.params`：命令行参数表。
- `app.version` / `app.apiVersion`：版本信息。
- `app.os`：操作系统（"Windows"、"macOS"、"Linux"）。

`app` 还提供方法：`app.open(filename)` 打开文件，`app.Alert` / `app.alert` 显示提示，`app.transaction` 创建事务，`app.undo()` / `app.redo()` 撤销/重做，`app.refresh()` 刷新界面。

### 例子

**示例 1：访问活动文档信息**

```lua
-- active_doc.lua
local sprite = app.activeSprite

if not sprite then
    app.alert("没有打开的精灵！")
    return
end

print("文件名:", sprite.filename)
print("尺寸:", sprite.width .. "x" .. sprite.height)
print("颜色模式:", sprite.colorMode)
print("帧数:", #sprite.frames)
print("图层数:", #sprite.layers)

-- 当前活动图层和帧
print("活动图层:", app.activeLayer.name)
print("活动帧:", app.activeFrame.frameNumber)
```

**示例 2：遍历所有打开的精灵**

```lua
-- list_sprites.lua
print("已打开的精灵数量:", #app.sprites)

for i, sprite in ipairs(app.sprites) do
    print(string.format("  [%d] %s (%dx%d, %d 帧)",
        i,
        sprite.filename or "未保存",
        sprite.width,
        sprite.height,
        #sprite.frames))
end

-- 切换活动精灵
if #app.sprites >= 2 then
    app.activeSprite = app.sprites[2]
    print("已切换到:", app.activeSprite.filename)
end
```

**示例 3：打开和创建文件**

```lua
-- file_ops.lua
-- 打开已有文件
local sprite = app.open("/path/to/character.aseprite")
if sprite then
    print("已打开:", sprite.filename)
end

-- 创建新精灵
local newSprite = Sprite(32, 32, ColorMode.RGB)
newSprite.filename = "new_sprite.aseprite"
print("已创建新精灵:", newSprite.width, "x", newSprite.height)

-- 设置为活动精灵
app.activeSprite = newSprite
```

**示例 4：使用 app.command 调用菜单命令**

```lua
-- use_commands.lua
local sprite = app.activeSprite
if not sprite then return end

-- 调用"翻转水平"命令
app.command.Flip{target="mask", orientation="horizontal"}

-- 调用"画布大小"命令
app.command.CanvasSize{
    ui = false,
    width = sprite.width + 16,
    height = sprite.height + 16
}

-- 调用"精灵大小"（缩放）
app.command.SpriteSize{
    ui = false,
    scale = 2,
    method = "nearest-neighbor"
}

-- 导出
app.command.ExportSpriteSheet{
    ui = false,
    type = SpriteSheetType.HORIZONTAL,
    textureFilename = "sheet.png",
    dataFilename = "sheet.json"
}
```

**示例 5：撤销/重做与刷新**

```lua
-- undo_redo.lua
local sprite = app.activeSprite
if not sprite then return end

-- 记录操作前的状态
local originalWidth = sprite.width

-- 执行操作
app.command.SpriteSize{ui=false, scale=2}

print("缩放后:", sprite.width)

-- 撤销
app.undo()
print("撤销后:", sprite.width)

-- 重做
app.redo()
print("重做后:", sprite.width)

-- 刷新界面（修改底层状态后需要）
app.refresh()
```

### 总结

- `app` 是 Aseprite Lua API 的核心入口，提供对活动文档和命令系统的访问。
- 活动文档概念：脚本默认操作当前聚焦的精灵。
- `app.command` 可调用几乎所有菜单命令，参数通过表传递。
- `app.transaction` 合并多个操作为一个撤销步骤。
- `app.params` 读取命令行参数，`app.open` 打开文件，`Sprite(w,h,mode)` 创建新精灵。
- **注意事项**：操作前始终检查 `app.activeSprite` 是否为 `nil`；`app.command` 的参数名需与 Aseprite 文档一致。

---

## 第 5 讲：Sprite（精灵）对象与文件操作

### 概念

`Sprite` 是 Aseprite 对象模型的根对象，代表一个完整的像素艺术文件。一个 Sprite 包含尺寸、颜色模式、调色板、图层列表、帧列表、标签（Tag）列表、切片（Slice）列表等属性。所有其他对象（Layer、Frame、Cel、Image）都从属于某个 Sprite。Sprite 可以新建、打开、保存、导出。

### 原理

Sprite 的内部结构：
- **元数据**：宽度、高度、颜色模式（RGB/Indexed/Grayscale）、像素比例、网格大小。
- **调色板**（Palette）：索引颜色模式下的颜色表，每个帧可以有不同的调色板。
- **图层**（Layer）：有序列表，包含普通图层、背景图层、文件夹图层。
- **帧**（Frame）：有序列表，每帧有持续时间（毫秒）。
- **Cel**：图层与帧交叉处的单元格，持有 Image 数据和位置。
- **标签**（Tag）：标记帧范围的命名区间，用于定义动画片段（如"walk"、"jump"）。
- **切片**（Slice）：矩形区域，用于导出时裁剪。

Sprite 的颜色模式：
- `ColorMode.RGB`：每像素 32 位（RGBA）。
- `ColorMode.INDEXED`：每像素 8 位（调色板索引，最多 256 色）。
- `ColorMode.GRAYSCALE`：每像素 16 位（灰度 + Alpha）。

Sprite 的文件操作：
- `Sprite(filename)` 或 `app.open(filename)`：打开文件。
- `sprite:save()`：保存到原文件名。
- `sprite:saveAs(filename)`：另存为。
- `sprite:saveCopyAs(filename)`：保存副本（不影响当前文件状态）。
- `sprite:close()`：关闭精灵。

### 例子

**示例 1：创建不同模式的精灵**

```lua
-- create_sprites.lua
-- 创建 RGB 模式精灵
local rgbSprite = Sprite(32, 32, ColorMode.RGB)
rgbSprite.filename = "rgb.aseprite"
print("RGB 精灵:", rgbSprite.colorMode)  -- 0 (ColorMode.RGB = 0)

-- 创建索引模式精灵
local idxSprite = Sprite(16, 16, ColorMode.INDEXED)
idxSprite.filename = "indexed.aseprite"
print("索引精灵:", idxSprite.colorMode)  -- 1 (ColorMode.INDEXED = 1)

-- 创建灰度模式精灵
local graySprite = Sprite(64, 64, ColorMode.GRAYSCALE)
graySprite.filename = "gray.aseprite"
print("灰度精灵:", graySprite.colorMode)  -- 2 (ColorMode.GRAYSCALE = 2)
```

**示例 2：打开和保存文件**

```lua
-- file_management.lua
-- 打开文件
local sprite = app.open("character.aseprite")
if not sprite then
    app.alert("无法打开文件")
    return
end

-- 修改后保存
sprite:save()
print("已保存到:", sprite.filename)

-- 另存为新文件
sprite:saveAs("character_backup.aseprite")

-- 导出为 PNG（不影响原文件）
sprite:saveCopyAs("character_export.png")
print("已导出 PNG")

-- 关闭精灵
sprite:close()
```

**示例 3：访问 Sprite 的属性和子对象**

```lua
-- sprite_properties.lua
local sprite = app.activeSprite
if not sprite then return end

-- 基本信息
print("=== 精灵信息 ===")
print("文件名:", sprite.filename)
print("尺寸:", sprite.width, "x", sprite.height)
print("颜色模式:", sprite.colorMode)
print("像素比例:", sprite.pixelRatio.width .. ":" .. sprite.pixelRatio.height)
print("网格大小:", sprite.gridBounds.width .. "x" .. sprite.gridBounds.height)

-- 图层
print("\n=== 图层 ===")
for i, layer in ipairs(sprite.layers) do
    print(string.format("  [%d] %s (%s)", i, layer.name,
          layer.isImage and "图像层" or "文件夹"))
end

-- 帧
print("\n=== 帧 ===")
for i, frame in ipairs(sprite.frames) do
    print(string.format("  [%d] %dms", i, frame.duration * 1000))
end

-- 标签
print("\n=== 标签 ===")
for i, tag in ipairs(sprite.tags) do
    print(string.format("  [%d] %s: 帧 %d-%d", i, tag.name,
          tag.fromFrame.frameNumber, tag.toFrame.frameNumber))
end
```

**示例 4：修改帧持续时间**

```lua
-- frame_timing.lua
local sprite = app.activeSprite
if not sprite then return end

-- 设置所有帧为 100ms
for _, frame in ipairs(sprite.frames) do
    frame.duration = 0.1  -- 单位：秒
end

-- 设置特定帧的持续时间
sprite.frames[1].duration = 0.2  -- 第一帧 200ms
sprite.frames[2].duration = 0.05 -- 第二帧 50ms

print("帧时长已修改")
```

**示例 5：批量创建精灵并保存**

```lua
-- batch_create.lua
-- 创建多个不同尺寸的精灵

local sizes = {{16, 16}, {32, 32}, {64, 64}}
local names = {"small", "medium", "large"}

for i, size in ipairs(sizes) do
    local sprite = Sprite(size[1], size[2], ColorMode.RGB)
    sprite.filename = names[i] .. ".aseprite"

    -- 填充随机颜色
    local cel = sprite.cels[1]
    local img = cel.image
    for y = 0, img.height - 1 do
        for x = 0, img.width - 1 do
            img:drawPixel(x, y, Color(
                math.random(0, 255),
                math.random(0, 255),
                math.random(0, 255),
                255
            ))
        end
    end

    sprite:save()
    print("已创建:", sprite.filename)
    sprite:close()
end
```

### 总结

- Sprite 是 Aseprite 对象模型的根，包含图层、帧、Cel、调色板、标签、切片等。
- 颜色模式：RGB（32位）、Indexed（8位）、Grayscale（16位）。
- 文件操作：`save`、`saveAs`、`saveCopyAs`、`close`。
- 帧持续时间以秒为单位（如 `0.1` = 100ms）。
- `sprite.gridBounds`、`sprite.pixelRatio` 等属性影响编辑和导出行为。
- **注意事项**：`saveCopyAs` 不会改变当前文件的"已修改"状态，适合导出；关闭精灵前确保已保存。

---

## 第 6 讲：Image（图像）对象与像素操作

### 概念

`Image` 是 Aseprite 中存储实际像素数据的对象。每个 Cel 持有一个 Image，Image 包含宽度、高度、颜色模式和像素数据。通过 Image 的 API 可以读取和写入单个像素、批量处理像素、进行图像变换等。像素操作是 Aseprite 自动化的核心能力。

### 原理

Image 的像素存储格式取决于颜色模式：
- **RGB 模式**：每像素 32 位（RGBA），使用 `Color(r, g, b, a)` 表示。
- **索引模式**：每像素 8 位，值为调色板索引（0-255）。
- **灰度模式**：每像素 16 位（灰度 + Alpha）。

Image 的核心方法：
- `img.width` / `img.height`：尺寸。
- `img.colorMode`：颜色模式。
- `img:getPixel(x, y)`：获取像素值（RGB 模式返回整数像素值，需用 `app.pixelColor` 分解；索引模式返回调色板索引）。
- `img:drawPixel(x, y, color)`：绘制单个像素。
- `img:drawImage(srcImg, x, y)`：将另一个图像绘制到指定位置。
- `img:clear()` / `img:clear(color)`：清空图像或填充指定颜色。
- `img:clone()`：创建图像副本。
- `Image(width, height, colorMode)`：创建新图像。
- `Image(img)`：从现有图像创建副本。

像素值的处理：在 RGB 模式下，`getPixel` 返回一个 32 位整数，包含 RGBA 四个字节。可以使用 `app.pixelColor.rgba(pixel)` 分解为 r, g, b, a 四个值，或用 `app.pixelColor.rgba(r, g, b, a)` 组合。在索引模式下，`getPixel` 直接返回调色板索引（0-255）。

### 例子

**示例 1：读取和写入像素**

```lua
-- pixel_ops.lua
local sprite = app.activeSprite
if not sprite then return end

local cel = app.activeCel
if not cel then return end

local img = cel.image

-- 读取像素
local pixel = img:getPixel(0, 0)
print("像素 (0,0):", pixel)

-- 根据颜色模式处理
if img.colorMode == ColorMode.RGB then
    local r, g, b, a = app.pixelColor.rgba(pixel)
    print(string.format("RGBA: %d, %d, %d, %d", r, g, b, a))
elseif img.colorMode == ColorMode.INDEXED then
    print("调色板索引:", pixel)
end

-- 写入像素
if img.colorMode == ColorMode.RGB then
    img:drawPixel(0, 0, Color(255, 0, 0, 255))  -- 红色
elseif img.colorMode == ColorMode.INDEXED then
    img:drawPixel(0, 0, 1)  -- 调色板索引 1
end
```

**示例 2：遍历所有像素**

```lua
-- iterate_pixels.lua
local sprite = app.activeSprite
if not sprite then return end

local img = app.activeImage
if not img then return end

-- 统计颜色使用情况
local colorCount = {}

for y = 0, img.height - 1 do
    for x = 0, img.width - 1 do
        local pixel = img:getPixel(x, y)
        colorCount[pixel] = (colorCount[pixel] or 0) + 1
    end
end

-- 输出统计
print("不同颜色数:", #colorCount)
for pixel, count in pairs(colorCount) do
    if img.colorMode == ColorMode.RGB then
        local r, g, b, a = app.pixelColor.rgba(pixel)
        print(string.format("  RGBA(%d,%d,%d,%d): %d 像素", r, g, b, a, count))
    else
        print(string.format("  索引 %d: %d 像素", pixel, count))
    end
end
```

**示例 3：创建新图像并绘制**

```lua
-- create_image.lua
-- 创建一个 32x32 的 RGB 图像
local img = Image(32, 32, ColorMode.RGB)

-- 用渐变填充
for y = 0, img.height - 1 do
    for x = 0, img.width - 1 do
        local r = math.floor(x / img.width * 255)
        local g = math.floor(y / img.height * 255)
        img:drawPixel(x, y, Color(r, g, 128, 255))
    end
end

-- 绘制对角线
for i = 0, 31 do
    img:drawPixel(i, i, Color(255, 255, 255, 255))
end

-- 将图像放入精灵
local sprite = Sprite(32, 32, ColorMode.RGB)
sprite.cels[1].image = img
sprite.cels[1].image:drawPixel(0, 0, Color(255, 0, 0, 255))
```

**示例 4：图像复制与合成**

```lua
-- image_compose.lua
local sprite = app.activeSprite
if not sprite then return end

local srcImg = app.activeImage
if not srcImg then return end

-- 创建副本
local copyImg = srcImg:clone()

-- 创建新图像并合成
local newImg = Image(srcImg.width * 2, srcImg.height, srcImg.colorMode)
newImg:clear()

-- 将原图绘制两次
newImg:drawImage(srcImg, 0, 0)
newImg:drawImage(copyImg, srcImg.width, 0)

-- 应用到精灵
local newCel = sprite:newCel(app.activeLayer, app.activeFrame)
newCel.image = newImg
```

**示例 5：像素级滤镜——反色**

```lua
-- invert_filter.lua
local sprite = app.activeSprite
if not sprite then return end

local img = app.activeImage
if not img then return end

app.transaction(function()
    if img.colorMode == ColorMode.RGB then
        for y = 0, img.height - 1 do
            for x = 0, img.width - 1 do
                local pixel = img:getPixel(x, y)
                local r, g, b, a = app.pixelColor.rgba(pixel)
                img:drawPixel(x, y, Color(255-r, 255-g, 255-b, a))
            end
        end
    elseif img.colorMode == ColorMode.GRAYSCALE then
        for y = 0, img.height - 1 do
            for x = 0, img.width - 1 do
                local pixel = img:getPixel(x, y)
                local v, a = app.pixelColor.graya(pixel)
                img:drawPixel(x, y, app.pixelColor.graya(255-v, a))
            end
        end
    end
    -- 索引模式无法直接反色，需修改调色板
end)

app.alert("反色完成")
```

### 总结

- Image 是像素数据的载体，每个 Cel 持有一个 Image。
- `getPixel` / `drawPixel` 是基本的像素读写方法。
- RGB 模式下像素是 32 位整数，用 `app.pixelColor.rgba` 分解/组合。
- 索引模式下像素值是调色板索引（0-255）。
- `Image(w, h, mode)` 创建新图像，`img:clone()` 创建副本，`img:drawImage(src, x, y)` 合成图像。
- **注意事项**：像素操作应放在 `app.transaction` 中以合并撤销步骤；索引模式的"颜色"实际是调色板索引，修改颜色需操作调色板。

---

## 第 7 讲：Cel（单元格）与帧管理

### 概念

`Cel`（单元格）是 Aseprite 中连接图层和帧的桥梁。每个 Cel 位于某个图层的某帧上，持有 Image 数据和位置信息。Cel 是动画的基本单元——通过在不同帧的 Cel 中放置不同的 Image，形成动画。理解 Cel 的结构对于操作动画至关重要。

### 原理

Aseprite 的数据结构是**二维网格**：一维是图层（Layer），另一维是帧（Frame）。每个图层与帧的交叉点是一个 Cel。并非所有交叉点都有 Cel——没有 Cel 的位置表示该图层在该帧为空（透明）。

Cel 的属性：
- `cel.image`：该 Cel 的 Image 对象。
- `cel.bounds`：Cel 在画布上的位置和尺寸（Rectangle 对象）。
- `cel.position`：Cel 相对于画布原点的位置（Point 对象）。
- `cel.layer`：所属图层。
- `cel.frame`：所属帧。
- `cel.opacity`：透明度（0-255）。
- `cel.color`：用户分配的颜色标签（用于 UI 标识）。

Cel 的操作方法（通过 Sprite 对象）：
- `sprite:newCel(layer, frame)`：在指定图层和帧创建新 Cel。
- `sprite:newCel(layer, frame, image, position)`：创建并指定图像和位置。
- `cel.image = newImage`：替换 Cel 的图像。
- `cel.position = Point(x, y)`：移动 Cel 位置。
- `cel.opacity = 128`：设置透明度。
- `sprite:deleteCel(cel)`：删除 Cel。

帧（Frame）的管理：
- `sprite:newFrame()` / `sprite:newFrame(frameIndex)`：在末尾或指定位置后添加帧。
- `sprite:newEmptyFrame()`：添加空帧。
- `sprite:deleteFrame(frame)`：删除帧。
- `frame.duration`：帧持续时间（秒）。
- `frame.frameNumber`：帧编号（从 1 开始）。

### 例子

**示例 1：理解 Cel 结构**

```lua
-- cel_structure.lua
local sprite = app.activeSprite
if not sprite then return end

-- 遍历所有 Cel
print("=== 所有 Cel ===")
for i, cel in ipairs(sprite.cels) do
    print(string.format("Cel %d: 图层 '%s', 帧 %d, 位置(%d,%d), 尺寸 %dx%d",
        i,
        cel.layer.name,
        cel.frame.frameNumber,
        cel.position.x,
        cel.position.y,
        cel.image.width,
        cel.image.height))
end

-- 获取特定图层和帧的 Cel
local layer = sprite.layers[1]
local frame = sprite.frames[1]
local cel = layer:cel(frame)  -- 或 sprite:getCel(layer, frame)
if cel then
    print("\n第一个图层第一帧的 Cel 存在")
else
    print("\n第一个图层第一帧的 Cel 不存在（空）")
end
```

**示例 2：创建动画帧**

```lua
-- create_animation.lua
-- 创建一个简单的闪烁动画

local sprite = Sprite(16, 16, ColorMode.RGB)
sprite.filename = "blink.aseprite"

-- 第一帧：填充红色
local cel1 = sprite.cels[1]
cel1.image:clear(Color(255, 0, 0, 255))

-- 添加第二帧：填充黑色
local frame2 = sprite:newFrame()
local cel2 = sprite:newCel(sprite.layers[1], frame2)
cel2.image = Image(16, 16, ColorMode.RGB)
cel2.image:clear(Color(0, 0, 0, 255))

-- 设置帧时长
sprite.frames[1].duration = 0.5  -- 500ms
sprite.frames[2].duration = 0.5

-- 保存
sprite:save()
print("动画创建完成")
```

**示例 3：移动 Cel 位置**

```lua
-- move_cel.lua
local sprite = app.activeSprite
if not sprite then return end

local cel = app.activeCel
if not cel then return end

-- 移动 Cel 到新位置
cel.position = Point(10, 10)

-- 相对移动
cel.position = Point(cel.position.x + 5, cel.position.y + 5)

-- 设置透明度
cel.opacity = 128  -- 50% 透明

print("Cel 已移动并调整透明度")
```

**示例 4：复制 Cel 到其他帧**

```lua
-- copy_cel.lua
local sprite = app.activeSprite
if not sprite then return end

local srcCel = app.activeCel
if not srcCel then return end

-- 复制当前 Cel 的图像到第 2、3、4 帧
app.transaction(function()
    for i = 2, 4 do
        if i <= #sprite.frames then
            local frame = sprite.frames[i]
            local targetCel = srcCel.layer:cel(frame)

            if targetCel then
                -- 已有 Cel，替换图像
                targetCel.image = srcCel.image:clone()
                targetCel.position = srcCel.position
            else
                -- 无 Cel，创建新的
                local newCel = sprite:newCel(srcCel.layer, frame)
                newCel.image = srcCel.image:clone()
                newCel.position = srcCel.position
            end
        end
    end
end)

print("已复制 Cel 到第 2-4 帧")
```

**示例 5：帧管理——插入、删除、重排**

```lua
-- frame_management.lua
local sprite = app.activeSprite
if not sprite then return end

-- 在第 2 帧后插入新帧
local newFrame = sprite:newFrame(sprite.frames[2])
newFrame.duration = 0.1

-- 在末尾添加空帧
local emptyFrame = sprite:newEmptyFrame()

-- 删除第 3 帧
sprite:deleteFrame(sprite.frames[3])

-- 修改帧时长
for _, frame in ipairs(sprite.frames) do
    frame.duration = 0.15  -- 统一 150ms
end

print(string.format("当前帧数: %d", #sprite.frames))
for i, frame in ipairs(sprite.frames) do
    print(string.format("  帧 %d: %dms", i, frame.duration * 1000))
end
```

### 总结

- Cel 是图层与帧交叉处的单元格，持有 Image 和位置信息。
- `sprite:newCel(layer, frame, image, position)` 创建 Cel。
- `cel.image`、`cel.position`、`cel.opacity` 是常用属性。
- `sprite:newFrame()` / `sprite:deleteFrame()` 管理帧。
- `frame.duration` 以秒为单位设置帧时长。
- **注意事项**：并非所有图层-帧交叉都有 Cel，空 Cel 表示透明；修改 Cel 应放在事务中；`cel.position` 是 Cel 相对画布的偏移，不是图像内坐标。

---

## 第 8 讲：Layer（图层）管理

### 概念

`Layer` 是 Aseprite 中组织图像内容的层次结构。图层按顺序叠加，上层覆盖下层。Aseprite 支持多种图层类型：图像图层（Image Layer）、背景图层（Background Layer）、文件夹图层（Layer Folder）。图层有可见性、可编辑性、透明度、混合模式等属性。通过 Lua API 可以创建、删除、重排图层，以及修改图层属性。

### 原理

Aseprite 的图层结构是**树形**的：顶层是图层列表，文件夹图层可以包含子图层。图层的顺序决定绘制顺序——列表中靠后的图层在上方。

图层类型：
- **图像图层**（`layer.isImage`）：持有像素数据，是最常见的图层类型。
- **背景图层**（`layer.isBackground`）：特殊的图像图层，位于最底层，不透明。
- **文件夹图层**（`layer.isGroup`）：容器，用于组织其他图层，本身不持有像素。
- **透明图层**（`layer.isTransparent`）：非背景的图像图层。

图层属性：
- `layer.name`：图层名称。
- `layer.opacity`：透明度（0-255）。
- `layer.visible`：是否可见（布尔）。
- `layer.editable`：是否可编辑（布尔，锁定状态）。
- `layer.blendMode`：混合模式（如 `BlendMode.NORMAL`、`BlendMode.MULTIPLY`）。
- `layer.parent`：父图层或父精灵。
- `layer.layers`：子图层列表（仅文件夹图层）。
- `layer.stackIndex`：在同级图层中的索引。

图层操作（通过 Sprite）：
- `sprite:newLayer()`：创建新图层。
- `sprite:deleteLayer(layer)`：删除图层。
- `sprite:newGroup()`：创建文件夹图层。
- `layer.parent = newParent`：移动图层到另一个父级。
- `layer.stackIndex = n`：调整图层顺序。

### 例子

**示例 1：遍历图层结构**

```lua
-- list_layers.lua
local sprite = app.activeSprite
if not sprite then return end

-- 递归打印图层树
local function printLayer(layer, indent)
    indent = indent or ""
    local type = "图像"
    if layer.isBackground then type = "背景"
    elseif layer.isGroup then type = "文件夹" end

    local vis = layer.visible and "可见" or "隐藏"
    local lock = layer.editable and "可编辑" or "锁定"

    print(string.format("%s[%s] %s (%s, %d%%, %s, %s)",
        indent, type, layer.name, vis,
        math.floor(layer.opacity / 255 * 100), lock,
        layer.blendMode))

    -- 递归子图层
    if layer.isGroup and layer.layers then
        for _, child in ipairs(layer.layers) do
            printLayer(child, indent .. "  ")
        end
    end
end

print("=== 图层结构 ===")
for _, layer in ipairs(sprite.layers) do
    printLayer(layer)
end
```

**示例 2：创建和管理图层**

```lua
-- manage_layers.lua
local sprite = app.activeSprite
if not sprite then return end

-- 创建新图层
local newLayer = sprite:newLayer()
newLayer.name = "特效层"
newLayer.opacity = 180
newLayer.blendMode = BlendMode.ADD  -- 加法混合

-- 创建文件夹
local group = sprite:newGroup()
group.name = "角色"

-- 将新图层移入文件夹
newLayer.parent = group

-- 创建背景图层
local bgLayer = sprite:newLayer()
bgLayer.name = "背景"
bgLayer.isBackground = true  -- 转为背景图层

print("图层创建完成")
```

**示例 3：调整图层顺序和属性**

```lua
-- layer_ops.lua
local sprite = app.activeSprite
if not sprite then return end

-- 获取活动图层
local layer = app.activeLayer
if not layer then return end

-- 修改属性
layer.name = "重命名图层"
layer.opacity = 200
layer.visible = true
layer.editable = false  -- 锁定图层

-- 调整顺序（移到最上面）
layer.stackIndex = #layer.parent.layers

-- 移动到另一个文件夹
if #sprite.layers > 1 then
    local targetGroup = sprite:newGroup()
    targetGroup.name = "新组"
    layer.parent = targetGroup
end

print("图层属性已修改")
```

**示例 4：合并图层**

```lua
-- merge_layers.lua
local sprite = app.activeSprite
if not sprite then return end

-- 使用命令合并可见图层
app.command.MergeDownLayer()  -- 向下合并

-- 或合并所有可见图层
-- app.command.FlattenLayers()

print("图层已合并")
```

**示例 5：按图层导出**

```lua
-- export_layers.lua
local sprite = app.activeSprite
if not sprite then return end

-- 保存当前状态
local originalActiveLayer = app.activeLayer

-- 遍历所有图像图层，单独导出
for _, layer in ipairs(sprite.layers) do
    if layer.isImage then
        -- 隐藏所有图层
        for _, l in ipairs(sprite.layers) do
            l.visible = false
        end
        -- 只显示当前图层
        layer.visible = true

        -- 导出
        local filename = string.format("layer_%s.png", layer.name)
        sprite:saveCopyAs(filename)
        print("已导出:", filename)
    end
end

-- 恢复可见性
for _, l in ipairs(sprite.layers) do
    l.visible = true
end

app.activeLayer = originalActiveLayer
print("所有图层导出完成")
```

### 总结

- 图层是树形结构，文件夹图层可包含子图层。
- 图层类型：图像图层、背景图层、文件夹图层。
- 常用属性：`name`、`opacity`、`visible`、`editable`、`blendMode`。
- `sprite:newLayer()` 创建图层，`sprite:newGroup()` 创建文件夹。
- `layer.parent` 和 `layer.stackIndex` 控制图层位置和顺序。
- **注意事项**：背景图层只能有一个且在最底层；修改图层可见性后导出可实现按图层拆分；混合模式影响与下层图层的合成方式。

---

# 第三章：命令行深度应用

Aseprite 的命令行接口（CLI）不仅能打开文件，还能执行格式转换、批量导出、精灵图集生成等任务。本章深入讲解命令行的各类参数和批处理工作流，以及如何将命令行与 Lua 脚本协同使用。

---

## 第 9 讲：命令行导出与格式转换

### 概念

Aseprite 命令行支持多种导出模式：单帧导出（PNG/BMP/JPEG）、动画导出（GIF）、精灵图集导出（Sprite Sheet）。通过 `--save-as` 参数指定输出文件名，Aseprite 根据扩展名自动选择格式。命令行导出无需打开 GUI，适合集成到构建流水线中。

### 原理

Aseprite 的导出引擎根据输出文件扩展名决定格式：
- `.png`：PNG 格式（支持透明，默认导出第一帧）。
- `.gif`：GIF 动画（导出所有帧为动画）。
- `.bmp` / `.jpg` / `.jpeg`：位图/JPEG 格式。
- `.aseprite` / `.ase`：Aseprite 原生格式（保留图层、帧等信息）。

导出参数：
- `--save-as <filename>`：指定输出文件名。
- `{frame}` / `{tag}` 占位符：批量导出多帧时用于命名。
- `--sheet <filename>`：导出精灵图集。
- `--sheet-type <type>`：图集类型（horizontal/vertical/rows/columns/packed）。
- `--sheet-width <n>` / `--sheet-height <n>`：图集尺寸。
- `--split-layers`：按图层分别导出。
- `--split-tags`：按标签分别导出。
- `--split-frames`：按帧分别导出。
- `--frame-range <from>-<to>`：指定帧范围。
- `--ignore-empty`：跳过空帧。

### 例子

**示例 1：基本格式转换**

```bash
# 将 Aseprite 文件转为 PNG（第一帧）
aseprite -b character.aseprite --save-as character.png

# 转为 GIF 动画
aseprite -b character.aseprite --save-as character.gif

# 转为 JPEG
aseprite -b character.aseprite --save-as character.jpg

# 转为 BMP
aseprite -b character.aseprite --save-as character.bmp
```

**示例 2：批量导出所有帧**

```bash
# 使用 {frame} 占位符导出每一帧
# {frame} 会被替换为帧编号（从 0 开始，可补零）
aseprite -b animation.aseprite --save-as frame_{frame}.png

# 指定帧编号位数（补零到 3 位）
aseprite -b animation.aseprite --save-as frame_{frame001}.png
# 输出: frame_000.png, frame_001.png, ...

# 指定帧范围
aseprite -b animation.aseprite --frame-range 0-9 --save-as frame_{frame}.png
```

**示例 3：按图层分别导出**

```bash
# --split-layers 将每个图层单独导出
aseprite -b character.aseprite --split-layers --save-as layer_{layer}.png
# 输出: layer_body.png, layer_head.png, layer_arms.png, ...

# 按图层和帧组合导出
aseprite -b character.aseprite --split-layers --save-as {layer}_{frame}.png
# 输出: body_0.png, body_1.png, head_0.png, head_1.png, ...
```

**示例 4：按标签导出**

```bash
# 假设精灵有 "walk" 和 "jump" 标签
# --split-tags 按标签范围导出
aseprite -b character.aseprite --split-tags --save-as anim_{tag}.gif
# 输出: anim_walk.gif, anim_jump.gif

# 标签内按帧导出
aseprite -b character.aseprite --split-tags --save-as {tag}_{frame}.png
# 输出: walk_0.png, walk_1.png, jump_0.png, ...
```

**示例 5：导出精灵图集**

```bash
# 水平排列的精灵图集
aseprite -b animation.aseprite \
  --sheet sheet.png \
  --sheet-type horizontal \
  --data sheet.json

# 垂直排列
aseprite -b animation.aseprite \
  --sheet sheet.png \
  --sheet-type vertical

# 按行排列（每行 N 帧）
aseprite -b animation.aseprite \
  --sheet sheet.png \
  --sheet-type rows \
  --sheet-columns 4

# 打包排列（紧凑布局）
aseprite -b animation.aseprite \
  --sheet sheet.png \
  --sheet-type packed

# 指定图集尺寸
aseprite -b animation.aseprite \
  --sheet sheet.png \
  --sheet-width 256 \
  --sheet-height 256

# 同时导出数据文件（JSON 格式，包含每帧位置信息）
aseprite -b animation.aseprite \
  --sheet sheet.png \
  --data sheet.json \
  --format json-array
```

### 总结

- `--save-as` 根据扩展名自动选择导出格式。
- `{frame}`、`{layer}`、`{tag}` 占位符用于批量命名。
- `--split-layers` / `--split-tags` / `--split-frames` 实现按维度拆分导出。
- `--sheet` + `--sheet-type` 导出精灵图集，`--data` 同时导出元数据。
- **注意事项**：GIF 格式有 256 色限制，索引模式精灵导出 GIF 效果最佳；`--frame-range` 的范围从 0 开始。

---

## 第 10 讲：命令行批处理工作流

### 概念

命令行批处理（batch processing）是指对多个文件执行相同操作的工作流。Aseprite 命令行可以接受多个输入文件，对每个文件执行相同的导出操作。结合 Shell 脚本（Bash/PowerShell），可以构建强大的批量处理流水线，如批量格式转换、批量缩放、批量导出图集等。

### 原理

Aseprite 命令行的批处理能力体现在：
1. **多文件输入**：一条命令可以接受多个输入文件，`--save-as` 中的占位符会为每个文件生成对应输出。
2. **`{title}` 占位符**：替换为输入文件名（不含扩展名），用于保持输出文件名与输入对应。
3. **Shell 循环**：用 Bash 的 `for` 循环或 PowerShell 的 `foreach` 遍历文件列表，对每个文件执行 Aseprite 命令。
4. **`--script` 参数**：在批处理中调用 Lua 脚本，实现更复杂的逻辑。

批处理工作流的典型结构：
```
遍历文件 → 对每个文件执行 Aseprite 命令 → 收集结果 → 后处理
```

### 例子

**示例 1：批量格式转换（Bash）**

```bash
#!/bin/bash
# convert_all.sh - 将所有 .aseprite 文件转为 PNG

ASEPRITE="/path/to/aseprite"
INPUT_DIR="./src"
OUTPUT_DIR="./output"

mkdir -p "$OUTPUT_DIR"

for file in "$INPUT_DIR"/*.aseprite; do
    if [ -f "$file" ]; then
        filename=$(basename "$file" .aseprite)
        echo "转换: $file -> $OUTPUT_DIR/${filename}.png"
        "$ASEPRITE" -b "$file" --save-as "$OUTPUT_DIR/${filename}.png"
    fi
done

echo "批量转换完成"
```

**示例 2：批量缩放（PowerShell）**

```powershell
# scale_all.ps1 - 将所有 PNG 放大 2 倍

$aseprite = "C:\Program Files\Aseprite\aseprite.exe"
$inputDir = ".\input"
$outputDir = ".\output"

New-Item -ItemType Directory -Force -Path $outputDir

Get-ChildItem -Path $inputDir -Filter *.png | ForEach-Object {
    $inputFile = $_.FullName
    $outputFile = Join-Path $outputDir "$($_.BaseName)_2x.png"
    Write-Host "缩放: $inputFile -> $outputFile"

    & $aseprite -b $inputFile --scale 2 --save-as $outputFile
}

Write-Host "批量缩放完成"
```

**示例 3：多文件合并为图集**

```bash
#!/bin/bash
# merge_to_sheet.sh - 将多个文件合并为一个精灵图集

ASEPRITE="/path/to/aseprite"

# 方法 1：先合并到一个精灵，再导出图集
# 创建临时 Lua 脚本
cat > /tmp/merge.lua << 'EOF'
local files = app.params["files"]:split(";")
local sprite = Sprite(32, 32, ColorMode.RGB)

for i, file in ipairs(files) do
    local src = app.open(file)
    if src then
        -- 复制第一帧到新精灵
        local frame = sprite:newFrame()
        local cel = sprite:newCel(sprite.layers[1], frame)
        cel.image = src.cels[1].image:clone()
        src:close()
    end
end

-- 删除第一帧（空帧）
sprite:deleteFrame(sprite.frames[1])

-- 导出图集
app.command.ExportSpriteSheet{
    ui = false,
    type = SpriteSheetType.HORIZONTAL,
    textureFilename = "merged_sheet.png",
    dataFilename = "merged_sheet.json"
}

sprite:close()
EOF

# 收集所有文件
FILES=""
for f in src/*.aseprite; do
    if [ -z "$FILES" ]; then
        FILES="$f"
    else
        FILES="$FILES;$f"
    fi
done

"$ASEPRITE" -b --script /tmp/merge.lua --script-param files="$FILES"
```

**示例 4：按文件夹批量导出**

```bash
#!/bin/bash
# batch_export.sh - 遍历子文件夹，每个文件夹导出一个图集

ASEPRITE="/path/to/aseprite"

for dir in characters/*/; do
    if [ -d "$dir" ]; then
        dirname=$(basename "$dir")
        echo "处理文件夹: $dir"

        # 收集该文件夹下所有 .aseprite 文件
        files=()
        for f in "$dir"*.aseprite; do
            if [ -f "$f" ]; then
                files+=("$f")
            fi
        done

        if [ ${#files[@]} -gt 0 ]; then
            # 导出图集
            "$ASEPRITE" -b "${files[@]}" \
                --sheet "output/${dirname}_sheet.png" \
                --data "output/${dirname}_sheet.json" \
                --sheet-type packed \
                --format json-array
            echo "  -> output/${dirname}_sheet.png"
        fi
    fi
done
```

**示例 5：增量处理（只处理修改过的文件）**

```bash
#!/bin/bash
# incremental_export.sh - 只导出有修改的文件

ASEPRITE="/path/to/aseprite"
SRC_DIR="./src"
OUT_DIR="./output"

mkdir -p "$OUT_DIR"

for src_file in "$SRC_DIR"/*.aseprite; do
    if [ ! -f "$src_file" ]; then continue; fi

    filename=$(basename "$src_file" .aseprite)
    out_file="$OUT_DIR/${filename}.png"

    # 比较修改时间：源文件比输出文件新才重新导出
    if [ ! -f "$out_file" ] || [ "$src_file" -nt "$out_file" ]; then
        echo "导出: $filename"
        "$ASEPRITE" -b "$src_file" --save-as "$out_file"
    else
        echo "跳过（未修改）: $filename"
    fi
done

echo "增量导出完成"
```

### 总结

- 命令行支持多文件输入，`{title}` 占位符保留原文件名。
- Shell 循环（Bash `for` / PowerShell `foreach`）实现文件遍历。
- `--script` + Lua 脚本实现复杂批处理逻辑。
- 增量处理通过比较文件修改时间（`-nt`）避免重复工作。
- **注意事项**：批处理脚本要处理路径中的空格（用引号包裹）；大量文件时考虑并行处理（`xargs -P`）。

---

## 第 11 讲：命令行与脚本协同

### 概念

Aseprite 命令行的 `--script` 参数可以在批处理模式下运行 Lua 脚本，`--script-param` 传递参数给脚本。这种协同模式结合了命令行的便捷性和 Lua 脚本的灵活性，是构建自动化流水线的核心能力。脚本可以访问 `app.params` 读取参数，执行复杂操作，然后退出。

### 原理

命令行运行脚本的流程：
1. Aseprite 以批处理模式（`-b`）启动。
2. `--script <file.lua>` 指定要运行的脚本文件。
3. `--script-param key=value` 传递参数（可多次使用）。
4. 脚本中通过 `app.params["key"]` 读取参数值。
5. 脚本执行完毕后，Aseprite 退出。

`app.params` 是一个只读的表，包含所有 `--script-param` 传递的键值对。参数值始终是字符串类型，需要在脚本中自行转换（如 `tonumber`）。

协同模式的优势：
- **参数化**：同一脚本可通过不同参数处理不同文件。
- **可组合**：Shell 脚本调用 Lua 脚本，Lua 脚本再调用 `app.command`。
- **可测试**：脚本可在 GUI 中调试，然后在命令行中使用。

### 例子

**示例 1：参数化脚本**

```lua
-- resize_export.lua
-- 用法: aseprite -b --script resize_export.lua --script-param input=char.aseprite --script-param scale=2 --script-param output=char_2x.png

local input = app.params["input"]
local scale = tonumber(app.params["scale"] or "2")
local output = app.params["output"]

if not input or not output then
    print("用法: --script-param input=<文件> --script-param output=<文件> [--script-param scale=<倍数>]")
    os.exit(1)
end

print(string.format("输入: %s, 缩放: %dx, 输出: %s", input, scale, output))

local sprite = app.open(input)
if not sprite then
    print("错误: 无法打开文件 " .. input)
    os.exit(1)
end

-- 缩放
app.command.SpriteSize{
    ui = false,
    scale = scale,
    method = "nearest-neighbor"
}

-- 导出
sprite:saveCopyAs(output)
print("已导出: " .. output)

sprite:close()
```

运行：
```bash
aseprite -b --script resize_export.lua \
  --script-param input=character.aseprite \
  --script-param scale=3 \
  --script-param output=character_3x.png
```

**示例 2：批量处理脚本**

```lua
-- batch_process.lua
-- 用法: aseprite -b --script batch_process.lua --script-param dir=./src --script-param format=png

local dir = app.params["dir"] or "."
local format = app.params["format"] or "png"

-- 扫描目录中的 .aseprite 文件
local function scanDir(path)
    local files = {}
    -- 使用 lfs（LuaFileSystem）或 io.popen
    local p = io.popen('ls "' .. path .. '"/*.aseprite 2>/dev/null')
    for line in p:lines() do
        files[#files + 1] = line
    end
    p:close()
    return files
end

local files = scanDir(dir)
print(string.format("找到 %d 个文件", #files))

for i, file in ipairs(files) do
    print(string.format("[%d/%d] 处理: %s", i, #files, file))

    local sprite = app.open(file)
    if sprite then
        -- 生成输出文件名
        local basename = file:match("([^/\\]+)%..+$")
        local output = string.format("%s/%s.%s", dir, basename, format)

        -- 导出
        sprite:saveCopyAs(output)
        sprite:close()
        print("  -> " .. output)
    else
        print("  错误: 无法打开")
    end
end

print("批量处理完成")
```

**示例 3：生成调色板报告**

```lua
-- palette_report.lua
-- 分析精灵的调色板并生成报告

local input = app.params["input"]
if not input then
    print("用法: --script-param input=<文件>")
    os.exit(1)
end

local sprite = app.open(input)
if not sprite then
    print("错误: 无法打开 " .. input)
    os.exit(1)
end

local palette = sprite.palettes[1]
print(string.format("\n=== 调色板报告: %s ===", input))
print(string.format("颜色数: %d", #palette))

-- 输出每个颜色
for i = 0, #palette - 1 do
    local color = palette:getColor(i)
    print(string.format("  [%d] RGBA(%d, %d, %d, %d) #%02X%02X%02X",
        i, color.red, color.green, color.blue, color.alpha,
        color.red, color.green, color.blue))
end

sprite:close()
```

运行：
```bash
aseprite -b --script palette_report.lua --script-param input=character.aseprite
```

**示例 4：Shell + Lua 协同流水线**

```bash
#!/bin/bash
# pipeline.sh - 完整的资产处理流水线

ASEPRITE="/path/to/aseprite"
SCRIPT_DIR="./scripts"
INPUT_DIR="./raw"
WORK_DIR="./work"
OUTPUT_DIR="./final"

mkdir -p "$WORK_DIR" "$OUTPUT_DIR"

# 步骤 1：批量缩放
echo "=== 步骤 1: 批量缩放 ==="
for file in "$INPUT_DIR"/*.aseprite; do
    [ -f "$file" ] || continue
    filename=$(basename "$file")
    "$ASEPRITE" -b --script "$SCRIPT_DIR/resize_export.lua" \
        --script-param "input=$file" \
        --script-param "scale=2" \
        --script-param "output=$WORK_DIR/$filename"
done

# 步骤 2：合并为图集
echo "=== 步骤 2: 生成图集 ==="
"$ASEPRITE" -b "$WORK_DIR"/*.aseprite \
    --sheet "$OUTPUT_DIR/atlas.png" \
    --data "$OUTPUT_DIR/atlas.json" \
    --sheet-type packed \
    --format json-array

# 步骤 3：清理
echo "=== 步骤 3: 清理临时文件 ==="
rm -rf "$WORK_DIR"

echo "=== 流水线完成 ==="
echo "输出: $OUTPUT_DIR/atlas.png, $OUTPUT_DIR/atlas.json"
```

**示例 5：错误处理与日志**

```lua
-- robust_process.lua
-- 带完整错误处理和日志的批处理脚本

local input = app.params["input"]
local output = app.params["output"]
local logfile = app.params["log"] or "process.log"

if not input or not output then
    print("错误: 缺少 input 或 output 参数")
    os.exit(1)
end

-- 日志函数
local function log(msg)
    local f = io.open(logfile, "a")
    if f then
        f:write(string.format("[%s] %s\n", os.date("%Y-%m-%d %H:%M:%S"), msg))
        f:close()
    end
    print(msg)
end

log(string.format("开始处理: %s -> %s", input, output))

-- 使用 pcall 安全执行
local ok, err = pcall(function()
    local sprite = app.open(input)
    if not sprite then
        error("无法打开文件: " .. input)
    end

    log(string.format("已打开: %dx%d, %d 帧",
        sprite.width, sprite.height, #sprite.frames))

    -- 执行操作...
    app.command.SpriteSize{ui=false, scale=2, method="nearest-neighbor"}

    sprite:saveCopyAs(output)
    log("已导出: " .. output)

    sprite:close()
end)

if ok then
    log("处理成功")
    os.exit(0)
else
    log("处理失败: " .. tostring(err))
    os.exit(1)
end
```

### 总结

- `--script` + `--script-param` 实现命令行与 Lua 脚本的协同。
- `app.params["key"]` 读取参数，值始终是字符串。
- 参数化脚本可复用，通过不同参数处理不同任务。
- Shell 脚本调用 Lua 脚本构建完整流水线。
- **注意事项**：参数值需自行类型转换；使用 `pcall` 和 `os.exit` 实现健壮的错误处理；日志文件有助于调试批处理问题。

---

# 第四章：Lua API 核心操作

本章深入讲解 Aseprite Lua API 的核心功能：颜色与调色板操作、选区与变换、绘图工具、帧动画与时间轴。这些 API 是编写像素艺术自动化脚本的基础工具集。

---

## 第 12 讲：颜色与调色板操作

### 概念

Aseprite 中的颜色通过 `Color` 对象表示，包含 RGBA 四个分量。调色板（`Palette`）是索引颜色模式下的颜色表，每个精灵可以有一个或多个调色板。通过 Lua API 可以创建颜色、修改调色板、在 RGB 和索引模式间转换等。

### 原理

**Color 对象**：
- `Color(r, g, b, a)`：创建 RGB 颜色，a 默认 255。
- `color.red` / `color.green` / `color.blue` / `color.alpha`：各分量（0-255）。
- `color.hue` / `color.saturation` / `color.value`：HSV 表示。
- `color:rgbaPixel()`：转换为 32 位整数像素值。
- `app.pixelColor.rgba(r, g, b, a)`：直接创建像素值。

**Palette 对象**：
- `sprite.palettes`：精灵的调色板列表。
- `palette:getColor(index)`：获取指定索引的颜色。
- `palette:setColor(index, color)`：设置指定索引的颜色。
- `palette:resize(n)`：调整调色板大小。
- `palette:getFrame()` / `palette:setFrame(frame)`：调色板关联的帧。
- `Palette(numberOfColors)`：创建新调色板。
- `app.command.AddPalette` / `app.command.SetPalette`：通过命令操作。

调色板与帧的关系：Aseprite 支持每帧不同的调色板（动画调色板）。`sprite.palettes` 中的每个调色板关联一个起始帧，从该帧开始使用此调色板，直到下一个调色板的起始帧。

### 例子

**示例 1：创建和使用颜色**

```lua
-- color_basics.lua
local sprite = app.activeSprite
if not sprite then return end

-- 创建颜色
local red = Color(255, 0, 0, 255)
local green = Color(0, 255, 0)
local transparent = Color(0, 0, 0, 0)

print("红色:", red.red, red.green, red.blue, red.alpha)
print("绿色透明度:", green.alpha)  -- 255（默认）

-- HSV 表示
local color = Color(180, 50, 100)
print(string.format("HSV: H=%d, S=%d, V=%d",
    color.hue, color.saturation, color.value))

-- 转换为像素值
local pixel = red:rgbaPixel()
print("像素值:", pixel)  -- 4278190335 (0xFF0000FF)

-- 从像素值创建颜色
local r, g, b, a = app.pixelColor.rgba(pixel)
print(string.format("从像素值: RGBA(%d,%d,%d,%d)", r, g, b, a))
```

**示例 2：读取和修改调色板**

```lua
-- palette_ops.lua
local sprite = app.activeSprite
if not sprite then return end

local palette = sprite.palettes[1]
print("调色板颜色数:", #palette)

-- 读取颜色
for i = 0, math.min(7, #palette - 1) do
    local color = palette:getColor(i)
    print(string.format("  [%d] RGBA(%d, %d, %d, %d)",
        i, color.red, color.green, color.blue, color.alpha))
end

-- 修改颜色
app.transaction(function()
    palette:setColor(0, Color(255, 0, 0))    -- 索引 0 改为红色
    palette:setColor(1, Color(0, 255, 0))    -- 索引 1 改为绿色
    palette:setColor(2, Color(0, 0, 255))    -- 索引 2 改为蓝色
end)

-- 调整调色板大小
palette:resize(32)  -- 扩展到 32 色
print("新调色板大小:", #palette)
```

**示例 3：创建新调色板**

```lua
-- create_palette.lua
local sprite = app.activeSprite
if not sprite then return end

-- 创建 16 色调色板
local palette = Palette(16)

-- 填充渐变色
for i = 0, 15 do
    local r = math.floor(i / 15 * 255)
    palette:setColor(i, Color(r, 0, 255 - r, 255))
end

-- 应用到精灵
sprite:setPalette(palette)

print("调色板已创建并应用")
```

**示例 4：从图像提取调色板**

```lua
-- extract_palette.lua
local sprite = app.activeSprite
if not sprite then return end

local img = app.activeImage
if not img or img.colorMode ~= ColorMode.RGB then
    app.alert("需要 RGB 模式图像")
    return
end

-- 收集所有颜色
local colorSet = {}
local colorList = {}

for y = 0, img.height - 1 do
    for x = 0, img.width - 1 do
        local pixel = img:getPixel(x, y)
        if not colorSet[pixel] then
            colorSet[pixel] = true
            colorList[#colorList + 1] = pixel
        end
    end
end

print(string.format("提取到 %d 种颜色", #colorList))

-- 创建调色板
local palette = Palette(#colorList)
for i, pixel in ipairs(colorList) do
    local r, g, b, a = app.pixelColor.rgba(pixel)
    palette:setColor(i - 1, Color(r, g, b, a))
end

-- 保存调色板文件
palette:save("extracted.gpl")
print("调色板已保存: extracted.gpl")
```

**示例 5：调色板动画（每帧不同调色板）**

```lua
-- palette_animation.lua
-- 创建一个颜色循环动画

local sprite = Sprite(16, 16, ColorMode.INDEXED)
sprite.filename = "palette_anim.aseprite"

-- 填充图像（使用索引 0-7）
local img = sprite.cels[1].image
for y = 0, 15 do
    for x = 0, 15 do
        img:drawPixel(x, y, (x + y) % 8)
    end
end

-- 创建 8 帧，每帧调色板不同
local baseColors = {
    Color(255, 0, 0), Color(255, 128, 0), Color(255, 255, 0),
    Color(0, 255, 0), Color(0, 255, 255), Color(0, 0, 255),
    Color(128, 0, 255), Color(255, 0, 255)
}

for frame = 1, 8 do
    if frame > 1 then
        sprite:newFrame()
    end
    sprite.frames[frame].duration = 0.1

    -- 为每帧创建调色板（颜色循环偏移）
    local palette = Palette(8)
    for i = 0, 7 do
        local colorIdx = (i + frame - 1) % 8
        palette:setColor(i, baseColors[colorIdx + 1])
    end
    palette:setFrame(sprite.frames[frame])
    sprite:setPalette(palette)
end

sprite:save()
print("调色板动画创建完成")
```

### 总结

- `Color(r, g, b, a)` 创建颜色，支持 RGBA 和 HSV 表示。
- `app.pixelColor.rgba` 在 Color 对象和整数像素值间转换。
- `Palette(n)` 创建调色板，`palette:getColor` / `setColor` 操作颜色。
- `sprite:setPalette(palette)` 应用调色板。
- 调色板可以关联帧，实现调色板动画。
- **注意事项**：索引模式下 `drawPixel` 的颜色参数是调色板索引而非 Color 对象；调色板操作应在事务中执行。

---

## 第 13 讲：选区与变换操作

### 概念

选区（Selection）是 Aseprite 中标记画布上一块区域的对象。通过选区可以限制操作范围、移动内容、进行变换（翻转、旋转、缩放）。Lua API 通过 `app.activeImage` 或直接操作 Selection 对象来管理选区，结合 `app.command` 执行变换命令。

### 原理

Aseprite 的选区是一个矩形或像素掩码集合。选区操作：
- `app.activeSprite.selection`：获取当前选区。
- `Selection()`：创建空选区。
- `selection:select(rect)`：选择矩形区域。
- `selection:selectAll()`：选择全部。
- `selection:deselect()`：取消选择。
- `selection:selectRange(x1, y1, x2, y2)`：选择范围。
- `selection.bounds`：选区的边界矩形。

变换命令（通过 `app.command`）：
- `app.command.Flip{target="mask"/"canvas", orientation="horizontal"/"vertical"}`：翻转。
- `app.command.Rotate{target="mask"/"canvas", angle=90}`：旋转。
- `app.command.SpriteSize{scale=2}`：缩放精灵。
- `app.command.CanvasSize{width=w, height=h}`：调整画布。
- `app.command.Crop`：裁剪到选区。
- `app.command.Trim`：修剪透明边缘。

`target` 参数区分操作对象：
- `"mask"`：只操作选区内的像素内容。
- `"canvas"`：操作整个画布（影响精灵尺寸）。

### 例子

**示例 1：基本选区操作**

```lua
-- selection_basics.lua
local sprite = app.activeSprite
if not sprite then return end

-- 创建选区
local sel = Selection(Rectangle(0, 0, 16, 16))
sprite.selection = sel

-- 获取选区信息
print("选区边界:", sel.bounds.x, sel.bounds.y, sel.bounds.width, sel.bounds.height)

-- 全选
sprite.selection:selectAll()
print("全选后边界:", sprite.selection.bounds.width, "x", sprite.selection.bounds.height)

-- 取消选择
sprite.selection:deselect()
```

**示例 2：选区内翻转**

```lua
-- flip_selection.lua
local sprite = app.activeSprite
if not sprite then return end

-- 选择左半部分
local halfWidth = math.floor(sprite.width / 2)
sprite.selection = Selection(Rectangle(0, 0, halfWidth, sprite.height))

-- 水平翻转选区内容
app.command.Flip{target="mask", orientation="horizontal"}

-- 取消选区
sprite.selection:deselect()
print("选区内容已水平翻转")
```

**示例 3：裁剪和修剪**

```lua
-- crop_trim.lua
local sprite = app.activeSprite
if not sprite then return end

-- 方法 1：裁剪到选区
sprite.selection = Selection(Rectangle(10, 10, 32, 32))
app.command.Crop()
print("已裁剪到选区:", sprite.width, "x", sprite.height)

-- 方法 2：修剪透明边缘
app.command.Trim()
print("已修剪透明边缘:", sprite.width, "x", sprite.height)

-- 方法 3：调整画布大小（居中）
app.command.CanvasSize{
    ui = false,
    width = sprite.width + 16,
    height = sprite.height + 16,
    bounds = Rectangle(-8, -8, sprite.width + 16, sprite.height + 16)
}
```

**示例 4：旋转和缩放**

```lua
-- rotate_scale.lua
local sprite = app.activeSprite
if not sprite then return end

-- 旋转 90 度（整个画布）
app.command.Rotate{target="canvas", angle=90}
print("旋转后:", sprite.width, "x", sprite.height)

-- 缩放精灵（2 倍，最近邻）
app.command.SpriteSize{
    ui = false,
    scale = 2,
    method = "nearest-neighbor"
}
print("缩放后:", sprite.width, "x", sprite.height)

-- 旋转选区内容 45 度
sprite.selection = Selection(Rectangle(0, 0, 32, 32))
app.command.Rotate{target="mask", angle=45}
sprite.selection:deselect()
```

**示例 5：批量变换——镜像生成**

```lua
-- mirror_generate.lua
-- 将左半部分镜像到右半部分

local sprite = app.activeSprite
if not sprite then return end

app.transaction(function()
    local halfWidth = math.floor(sprite.width / 2)

    -- 选择左半部分
    sprite.selection = Selection(Rectangle(0, 0, halfWidth, sprite.height))

    -- 复制
    app.command.Copy()

    -- 粘贴
    app.command.Paste()

    -- 水平翻转粘贴的内容
    app.command.Flip{target="mask", orientation="horizontal"}

    -- 移动到右半部分
    app.command.MoveMask{target="content", x=halfWidth, y=0}

    -- 取消选区
    sprite.selection:deselect()
end)

print("镜像生成完成")
```

### 总结

- `Selection(rect)` 创建选区，`sprite.selection = sel` 设置活动选区。
- `app.command.Flip` / `Rotate` / `SpriteSize` / `CanvasSize` 执行变换。
- `target="mask"` 操作选区内容，`target="canvas"` 操作整个画布。
- `app.command.Crop` 裁剪到选区，`app.command.Trim` 修剪透明边缘。
- **注意事项**：变换命令会修改精灵尺寸或内容，应在事务中执行；`SpriteSize` 的 `method` 参数影响缩放质量（`nearest-neighbor` 适合像素艺术）。

---

## 第 14 讲：绘图工具 API

### 概念

Aseprite 的绘图工具（铅笔、填充、直线、矩形、椭圆等）可以通过 `app.command` 或 `app.tool` 调用。`app.tool` 允许选择工具并设置参数，`app.command` 提供高级绘图操作（如填充、描边）。结合选区，可以实现程序化绘图。

### 原理

Aseprite 的工具系统：
- `app.tool`：当前活动工具（如 `"pencil"`、`"line"`、`"rectangle"`、`"ellipse"`、`"fill"`）。
- `app.command.DrawLine` / `DrawRectangle` / `DrawEllipse`：直接绘制形状。
- `app.command.Fill`：填充选区或整个画布。
- `app.command.Stroke`：描边选区。

工具参数：
- `color`：前景色。
- `backgroundColor`：背景色。
- `app.command.SetColor`：设置颜色。

绘图命令通常需要先设置选区或起点终点，然后调用命令。对于像素级精确绘图，更推荐直接操作 Image 的 `drawPixel` 方法。

### 例子

**示例 1：填充和描边**

```lua
-- fill_stroke.lua
local sprite = app.activeSprite
if not sprite then return end

-- 设置前景色
app.fgColor = Color(255, 0, 0)

-- 填充整个画布
app.command.Fill{
    ui = false,
    color = app.fgColor
}

-- 选择一个区域并填充
sprite.selection = Selection(Rectangle(10, 10, 20, 20))
app.command.Fill{
    ui = false,
    color = Color(0, 255, 0)
}

-- 描边选区
app.command.Stroke{
    ui = false,
    color = Color(0, 0, 255),
    size = 2
}

sprite.selection:deselect()
```

**示例 2：使用工具绘制**

```lua
-- use_tools.lua
local sprite = app.activeSprite
if not sprite then return end

-- 选择铅笔工具
app.tool = "pencil"

-- 设置颜色
app.fgColor = Color(255, 255, 255)

-- 使用命令绘制直线
app.command.DrawLine{
    from = Point(0, 0),
    to = Point(31, 31)
}

-- 绘制矩形
app.command.DrawRectangle{
    from = Point(5, 5),
    to = Point(25, 25)
}

-- 绘制椭圆
app.command.DrawEllipse{
    from = Point(0, 0),
    to = Point(31, 31)
}
```

**示例 3：像素级精确绘图**

```lua
-- pixel_drawing.lua
-- 直接操作 Image 实现精确绘图

local sprite = app.activeSprite
if not sprite then return end

local img = app.activeImage
if not img then return end

-- 画直线（Bresenham 算法）
local function drawLine(img, x1, y1, x2, y2, color)
    local dx = math.abs(x2 - x1)
    local dy = math.abs(y2 - y1)
    local sx = x1 < x2 and 1 or -1
    local sy = y1 < y2 and 1 or -1
    local err = dx - dy

    while true do
        img:drawPixel(x1, y1, color)
        if x1 == x2 and y1 == y2 then break end
        local e2 = 2 * err
        if e2 > -dy then err = err - dy; x1 = x1 + sx end
        if e2 < dx then err = err + dx; y1 = y1 + sy end
    end
end

-- 画矩形
local function drawRect(img, x, y, w, h, color)
    drawLine(img, x, y, x + w - 1, y, color)
    drawLine(img, x, y + h - 1, x + w - 1, y + h - 1, color)
    drawLine(img, x, y, x, y + h - 1, color)
    drawLine(img, x + w - 1, y, x + w - 1, y + h - 1, color)
end

-- 画填充矩形
local function fillRect(img, x, y, w, h, color)
    for py = y, y + h - 1 do
        for px = x, x + w - 1 do
            img:drawPixel(px, py, color)
        end
    end
end

-- 画圆（中点圆算法）
local function drawCircle(img, cx, cy, r, color)
    local x = r
    local y = 0
    local err = 0

    while x >= y do
        img:drawPixel(cx + x, cy + y, color)
        img:drawPixel(cx + y, cy + x, color)
        img:drawPixel(cx - y, cy + x, color)
        img:drawPixel(cx - x, cy + y, color)
        img:drawPixel(cx - x, cy - y, color)
        img:drawPixel(cx - y, cy - x, color)
        img:drawPixel(cx + y, cy - x, color)
        img:drawPixel(cx + x, cy - y, color)

        y = y + 1
        if err <= 0 then
            err = err + 2 * y + 1
        end
        if err > 0 then
            x = x - 1
            err = err - 2 * x + 1
        end
    end
end

-- 使用
local white = Color(255, 255, 255, 255)
local red = Color(255, 0, 0, 255)

drawLine(img, 0, 0, 31, 31, white)
drawRect(img, 5, 5, 10, 10, red)
fillRect(img, 20, 20, 8, 8, white)
drawCircle(img, 16, 16, 8, red)
```

**示例 4：渐变填充**

```lua
-- gradient_fill.lua
local sprite = app.activeSprite
if not sprite then return end

local img = app.activeImage
if not img then return end

-- 水平渐变
local function horizontalGradient(img, x, y, w, h, color1, color2)
    for px = x, x + w - 1 do
        local t = (px - x) / (w - 1)
        local r = math.floor(color1.red + (color2.red - color1.red) * t)
        local g = math.floor(color1.green + (color2.green - color1.green) * t)
        local b = math.floor(color1.blue + (color2.blue - color1.blue) * t)
        local color = Color(r, g, b, 255)
        for py = y, y + h - 1 do
            img:drawPixel(px, py, color)
        end
    end
end

-- 垂直渐变
local function verticalGradient(img, x, y, w, h, color1, color2)
    for py = y, y + h - 1 do
        local t = (py - y) / (h - 1)
        local r = math.floor(color1.red + (color2.red - color1.red) * t)
        local g = math.floor(color1.green + (color2.green - color1.green) * t)
        local b = math.floor(color1.blue + (color2.blue - color1.blue) * t)
        local color = Color(r, g, b, 255)
        for px = x, x + w - 1 do
            img:drawPixel(px, py, color)
        end
    end
end

horizontalGradient(img, 0, 0, img.width, img.height / 2,
    Color(255, 0, 0), Color(0, 0, 255))
verticalGradient(img, 0, img.height / 2, img.width, img.height / 2,
    Color(0, 255, 0), Color(255, 255, 0))
```

**示例 5：图案填充**

```lua
-- pattern_fill.lua
local sprite = app.activeSprite
if not sprite then return end

local img = app.activeImage
if not img then return end

-- 定义图案（8x8 棋盘格）
local pattern = {
    {1, 0, 1, 0, 1, 0, 1, 0},
    {0, 1, 0, 1, 0, 1, 0, 1},
    {1, 0, 1, 0, 1, 0, 1, 0},
    {0, 1, 0, 1, 0, 1, 0, 1},
    {1, 0, 1, 0, 1, 0, 1, 0},
    {0, 1, 0, 1, 0, 1, 0, 1},
    {1, 0, 1, 0, 1, 0, 1, 0},
    {0, 1, 0, 1, 0, 1, 0, 1},
}

local color1 = Color(255, 255, 255, 255)
local color2 = Color(0, 0, 0, 255)

-- 用图案填充
for y = 0, img.height - 1 do
    for x = 0, img.width - 1 do
        local px = (x % 8) + 1
        local py = (y % 8) + 1
        if pattern[py][px] == 1 then
            img:drawPixel(x, y, color1)
        else
            img:drawPixel(x, y, color2)
        end
    end
end
```

### 总结

- `app.command.Fill` / `Stroke` / `DrawLine` / `DrawRectangle` / `DrawEllipse` 提供高级绘图。
- `app.tool` 选择工具，`app.fgColor` / `app.bgColor` 设置颜色。
- 像素级精确绘图推荐直接操作 `Image:drawPixel`，可实现 Bresenham 直线、中点圆等算法。
- 渐变和图案填充通过遍历像素计算颜色实现。
- **注意事项**：`app.command` 绘图依赖选区和活动工具状态；直接操作 Image 更可控但需手动实现算法。

---

## 第 15 讲：帧动画与时间轴操作

### 概念

帧动画是 Aseprite 的核心功能。通过管理帧（Frame）、Cel、标签（Tag），可以创建复杂的动画。Lua API 提供了完整的帧操作能力：添加/删除帧、设置帧时长、创建标签、复制 Cel 等。本讲深入讲解动画时间轴的编程控制。

### 原理

Aseprite 动画的基本组成：
- **帧（Frame）**：动画的单个时间点，有持续时间（duration，秒）。
- **Cel**：某图层在某帧的图像内容。
- **标签（Tag）**：标记帧范围的命名区间，定义动画片段（如 "walk" = 帧 1-8）。
- **洋葱皮（Onion Skin）**：显示前后帧的轮廓，辅助动画制作。

帧操作 API：
- `sprite:newFrame()` / `sprite:newFrame(frame)`：在末尾或指定帧后添加。
- `sprite:newEmptyFrame()`：添加空帧。
- `sprite:deleteFrame(frame)`：删除帧。
- `frame.duration`：帧时长（秒）。
- `frame.frameNumber`：帧编号（1 开始）。

标签操作 API：
- `sprite:newTag(fromFrame, toFrame)`：创建标签。
- `tag.name`：标签名。
- `tag.fromFrame` / `tag.toFrame`：标签的帧范围。
- `tag.color`：标签颜色。
- `tag.aniDir`：动画方向（`AniDir.FORWARD`/`REVERSE`/`PING_PONG`）。
- `sprite:deleteTag(tag)`：删除标签。

### 例子

**示例 1：创建基础动画**

```lua
-- create_animation.lua
-- 创建一个简单的弹跳球动画

local sprite = Sprite(32, 32, ColorMode.RGB)
sprite.filename = "bounce.aseprite"

-- 创建 8 帧
for i = 2, 8 do
    sprite:newFrame()
    sprite.frames[i].duration = 0.1
end
sprite.frames[1].duration = 0.1

-- 在每帧绘制不同位置的球
local layer = sprite.layers[1]
local ballColor = Color(255, 0, 0, 255)

for i, frame in ipairs(sprite.frames) do
    local cel = sprite:newCel(layer, frame)
    local img = Image(32, 32, ColorMode.RGB)

    -- 计算球的位置（抛物线轨迹）
    local t = (i - 1) / 7  -- 0 到 1
    local x = 4 + t * 20
    local y = 4 + math.sin(t * math.pi) * (-12) + 16  -- 抛物线

    -- 画球
    local cx, cy, r = math.floor(x), math.floor(y), 4
    for py = cy - r, cy + r do
        for px = cx - r, cx + r do
            if (px - cx)^2 + (py - cy)^2 <= r^2 then
                img:drawPixel(px, py, ballColor)
            end
        end
    end

    cel.image = img
end

sprite:save()
print("弹跳球动画创建完成")
```

**示例 2：标签管理**

```lua
-- tag_management.lua
local sprite = app.activeSprite
if not sprite then return end

-- 创建标签
local walkTag = sprite:newTag(sprite.frames[1], sprite.frames[8])
walkTag.name = "walk"
walkTag.color = Color(0, 255, 0)
walkTag.aniDir = AniDir.FORWARD

local jumpTag = sprite:newTag(sprite.frames[9], sprite.frames[12])
jumpTag.name = "jump"
jumpTag.color = Color(255, 0, 0)
jumpTag.aniDir = AniDir.PING_PONG

-- 列出所有标签
print("=== 标签列表 ===")
for _, tag in ipairs(sprite.tags) do
    print(string.format("  %s: 帧 %d-%d (%s)",
        tag.name,
        tag.fromFrame.frameNumber,
        tag.toFrame.frameNumber,
        tag.aniDir))
end

-- 修改标签范围
walkTag.toFrame = sprite.frames[10]
print("walk 标签范围已修改")

-- 删除标签
-- sprite:deleteTag(jumpTag)
```

**示例 3：复制和重排帧**

```lua
-- copy_frames.lua
local sprite = app.activeSprite
if not sprite then return end

-- 复制第 1-4 帧到第 5-8 帧
app.transaction(function()
    for i = 1, 4 do
        local srcFrame = sprite.frames[i]
        local dstFrame = sprite:newFrame()  -- 添加到末尾

        -- 复制每个图层的 Cel
        for _, layer in ipairs(sprite.layers) do
            if layer.isImage then
                local srcCel = layer:cel(srcFrame)
                if srcCel then
                    local newCel = sprite:newCel(layer, dstFrame)
                    newCel.image = srcCel.image:clone()
                    newCel.position = srcCel.position
                    newCel.opacity = srcCel.opacity
                end
            end
        end

        dstFrame.duration = srcFrame.duration
    end
end)

print("帧已复制")
```

**示例 4：反向动画**

```lua
-- reverse_animation.lua
local sprite = app.activeSprite
if not sprite then return end

local frameCount = #sprite.frames
local halfCount = math.floor(frameCount / 2)

app.transaction(function()
    -- 交换每对帧的 Cel
    for i = 1, halfCount do
        local j = frameCount - i + 1
        local frameA = sprite.frames[i]
        local frameB = sprite.frames[j]

        -- 交换每个图层的 Cel 图像
        for _, layer in ipairs(sprite.layers) do
            if layer.isImage then
                local celA = layer:cel(frameA)
                local celB = layer:cel(frameB)

                if celA and celB then
                    local tempImg = celA.image:clone()
                    celA.image = celB.image:clone()
                    celB.image = tempImg
                elseif celA then
                    local newCel = sprite:newCel(layer, frameB)
                    newCel.image = celA.image:clone()
                    newCel.position = celA.position
                    sprite:deleteCel(celA)
                elseif celB then
                    local newCel = sprite:newCel(layer, frameA)
                    newCel.image = celB.image:clone()
                    newCel.position = celB.position
                    sprite:deleteCel(celB)
                end
            end
        end
    end
end)

print("动画已反向")
```

**示例 5：导出动画为序列帧**

```lua
-- export_sequence.lua
local sprite = app.activeSprite
if not sprite then return end

local outputDir = app.params["output"] or "./output"

-- 确保输出目录存在
os.execute("mkdir -p " .. outputDir)

-- 遍历所有帧，导出每一帧
for i, frame in ipairs(sprite.frames) do
    -- 设置活动帧
    app.activeFrame = frame

    -- 生成文件名（补零到 3 位）
    local filename = string.format("%s/frame_%03d.png", outputDir, i - 1)

    -- 导出当前帧
    sprite:saveCopyAs(filename)
    print("已导出: " .. filename)
end

print(string.format("共导出 %d 帧", #sprite.frames))
```

### 总结

- 帧是动画的时间点，`frame.duration` 控制时长（秒）。
- `sprite:newFrame()` / `deleteFrame()` 管理帧。
- 标签标记帧范围，`tag.aniDir` 控制播放方向。
- 复制帧需复制每个图层对应帧的 Cel。
- 反向动画通过交换对称位置的帧实现。
- **注意事项**：帧操作应在事务中执行；`AniDir.PING_PONG` 会让动画正向播放后反向播放；导出序列帧时帧编号通常从 0 开始。

---

# 第五章：高级自动化

本章探讨 Aseprite 自动化的高级主题：批量文件处理、图像分析与像素统计、切片（Slice）管理。这些技术将自动化能力从单文件操作提升到项目级别的资产管理。

---

## 第 16 讲：批量处理与文件遍历

### 概念

批量处理是指对一组文件执行相同或相似操作的工作流。在像素艺术项目中，通常需要处理大量精灵文件（如角色、道具、场景元素）。通过 Lua 脚本结合文件系统遍历，可以实现自动化批量处理，大幅提升生产效率。

### 原理

Aseprite 的 Lua 环境提供了访问文件系统的能力。虽然标准 Lua 的 `io` 库功能有限，但 Aseprite 内置了 `lfs`（LuaFileSystem）模块，可以遍历目录、获取文件信息。此外，也可以通过 `io.popen` 调用系统命令（如 `ls`、`dir`）来列出文件。

批量处理的典型模式：
1. **扫描目录**：收集所有待处理文件路径。
2. **逐个处理**：对每个文件打开、操作、保存、关闭。
3. **错误处理**：单个文件失败不应中断整个批处理。
4. **进度报告**：输出处理进度，便于监控。
5. **资源管理**：及时关闭文件和精灵，避免内存泄漏。

批量处理的注意事项：
- 每个文件处理完后调用 `sprite:close()` 释放资源。
- 使用 `pcall` 包裹单个文件的处理逻辑，避免一个文件出错导致全部中断。
- 大量文件处理时，定期调用 `collectgarbage("collect")` 回收内存。
- 输出详细日志，便于排查问题。

### 例子

**示例 1：使用 lfs 遍历目录**

```lua
-- scan_directory.lua
-- 使用 LuaFileSystem 遍历目录

local lfs = require("lfs")

-- 递归扫描目录
local function scanDir(path, pattern, results)
    results = results or {}
    for file in lfs.dir(path) do
        if file ~= "." and file ~= ".." then
            local fullPath = path .. "/" .. file
            local attr = lfs.attributes(fullPath)

            if attr.mode == "directory" then
                -- 递归扫描子目录
                scanDir(fullPath, pattern, results)
            elseif attr.mode == "file" then
                -- 匹配文件扩展名
                if file:match(pattern .. "$") then
                    results[#results + 1] = fullPath
                end
            end
        end
    end
    return results
end

-- 扫描所有 .aseprite 文件
local files = scanDir("./assets", "%.aseprite")
print(string.format("找到 %d 个文件:", #files))
for i, file in ipairs(files) do
    print(string.format("  [%d] %s", i, file))
end
```

**示例 2：批量格式转换**

```lua
-- batch_convert.lua
-- 批量将 .aseprite 转为 .png

local lfs = require("lfs")

local inputDir = app.params["input"] or "./src"
local outputDir = app.params["output"] or "./output"
local format = app.params["format"] or "png"

-- 确保输出目录存在
os.execute('mkdir -p "' .. outputDir .. '"')

-- 扫描输入目录
local files = {}
for file in lfs.dir(inputDir) do
    if file:match("%.aseprite$") then
        files[#files + 1] = inputDir .. "/" .. file
    end
end

print(string.format("开始批量转换: %d 个文件", #files))

local success = 0
local failed = 0

for i, file in ipairs(files) do
    local basename = file:match("([^/\\]+)%.aseprite$")
    local output = string.format("%s/%s.%s", outputDir, basename, format)

    -- 使用 pcall 安全处理
    local ok, err = pcall(function()
        local sprite = app.open(file)
        if not sprite then
            error("无法打开: " .. file)
        end

        sprite:saveCopyAs(output)
        sprite:close()
    end)

    if ok then
        success = success + 1
        print(string.format("[%d/%d] OK: %s -> %s", i, #files, basename, output))
    else
        failed = failed + 1
        print(string.format("[%d/%d] FAIL: %s (%s)", i, #files, basename, err))
    end

    -- 定期垃圾回收
    if i % 10 == 0 then
        collectgarbage("collect")
    end
end

print(string.format("\n完成: 成功 %d, 失败 %d", success, failed))
```

**示例 3：批量调整尺寸**

```lua
-- batch_resize.lua
-- 批量调整精灵尺寸

local lfs = require("lfs")

local inputDir = app.params["input"]
local scale = tonumber(app.params["scale"] or "2")
local outputDir = app.params["output"]

if not inputDir or not outputDir then
    print("用法: --script-param input=<目录> --script-param output=<目录> --script-param scale=<倍数>")
    os.exit(1)
end

os.execute('mkdir -p "' .. outputDir .. '"')

local count = 0
for file in lfs.dir(inputDir) do
    if file:match("%.aseprite$") then
        local input = inputDir .. "/" .. file
        local output = outputDir .. "/" .. file

        local ok, err = pcall(function()
            local sprite = app.open(input)
            if not sprite then return end

            app.command.SpriteSize{
                ui = false,
                scale = scale,
                method = "nearest-neighbor"
            }

            sprite:saveAs(output)
            sprite:close()
            count = count + 1
            print("已处理: " .. file)
        end)

        if not ok then
            print("错误: " .. file .. " - " .. err)
        end
    end
end

print(string.format("共处理 %d 个文件", count))
```

**示例 4：批量生成缩略图**

```lua
-- batch_thumbnails.lua
-- 为每个精灵生成小尺寸缩略图

local lfs = require("lfs")

local inputDir = app.params["input"] or "."
local outputDir = app.params["output"] or "./thumbnails"
local thumbSize = tonumber(app.params["size"] or "64")

os.execute('mkdir -p "' .. outputDir .. '"')

for file in lfs.dir(inputDir) do
    if file:match("%.aseprite$") then
        local sprite = app.open(inputDir .. "/" .. file)
        if sprite then
            -- 计算缩略图尺寸（保持比例）
            local maxDim = math.max(sprite.width, sprite.height)
            local scale = thumbSize / maxDim

            app.command.SpriteSize{
                ui = false,
                scale = scale,
                method = "nearest-neighbor"
            }

            -- 导出第一帧为 PNG
            local basename = file:match("(.+)%..+$")
            sprite:saveCopyAs(outputDir .. "/" .. basename .. "_thumb.png")
            sprite:close()
            print("缩略图: " .. basename)
        end
    end
end
```

**示例 5：批量验证和报告**

```lua
-- batch_validate.lua
-- 验证所有精灵文件并生成报告

local lfs = require("lfs")

local inputDir = app.params["input"] or "."
local reportFile = app.params["report"] or "validation_report.txt"

local report = io.open(reportFile, "w")
report:write("Aseprite 文件验证报告\n")
report:write(string.format("扫描目录: %s\n", inputDir))
report:write(string.format("时间: %s\n\n", os.date()))

local totalFiles = 0
local issues = {}

for file in lfs.dir(inputDir) do
    if file:match("%.aseprite$") then
        totalFiles = totalFiles + 1
        local path = inputDir .. "/" .. file
        local sprite = app.open(path)

        if sprite then
            report:write(string.format("\n=== %s ===\n", file))
            report:write(string.format("尺寸: %dx%d\n", sprite.width, sprite.height))
            report:write(string.format("颜色模式: %d\n", sprite.colorMode))
            report:write(string.format("帧数: %d\n", #sprite.frames))
            report:write(string.format("图层数: %d\n", #sprite.layers))

            -- 检查潜在问题
            if sprite.width > 256 or sprite.height > 256 then
                issues[#issues + 1] = file .. ": 尺寸过大"
            end

            if #sprite.frames == 0 then
                issues[#issues + 1] = file .. ": 无帧"
            end

            -- 检查空 Cel
            local emptyCels = 0
            for _, cel in ipairs(sprite.cels) do
                if not cel.image then
                    emptyCels = emptyCels + 1
                end
            end
            if emptyCels > 0 then
                report:write(string.format("警告: %d 个空 Cel\n", emptyCels))
            end

            sprite:close()
        else
            issues[#issues + 1] = file .. ": 无法打开"
            report:write(string.format("\n=== %s === 错误: 无法打开\n", file))
        end
    end
end

report:write(string.format("\n\n=== 总结 ===\n"))
report:write(string.format("总文件数: %d\n", totalFiles))
report:write(string.format("问题数: %d\n", #issues))
for _, issue in ipairs(issues) do
    report:write("  - " .. issue .. "\n")
end

report:close()
print(string.format("报告已生成: %s (共 %d 个文件, %d 个问题)",
    reportFile, totalFiles, #issues))
```

### 总结

- `lfs`（LuaFileSystem）模块提供目录遍历能力，`lfs.dir(path)` 迭代目录项。
- 批量处理模式：扫描 → 处理 → 错误处理 → 进度报告。
- 使用 `pcall` 包裹单个文件处理，避免一个失败导致全部中断。
- 定期 `collectgarbage("collect")` 回收内存，防止长时间运行内存泄漏。
- **注意事项**：每个文件处理后调用 `sprite:close()`；路径中的空格和特殊字符需用引号包裹；跨平台路径分隔符差异（`/` vs `\`）。

---

## 第 17 讲：图像分析与像素统计

### 概念

图像分析是指通过编程方式读取和分析图像的像素数据，提取有用信息。在像素艺术中，常见的分析需求包括：统计颜色使用、检测透明区域、计算边界框、分析像素分布等。这些分析结果可用于自动化决策（如优化调色板、裁剪空白、生成元数据）。

### 原理

Aseprite 的 Image 对象提供了 `getPixel(x, y)` 方法，可以逐像素读取图像数据。通过遍历所有像素，可以收集各种统计信息。由于像素艺术通常尺寸较小（几十到几百像素），逐像素遍历的性能完全可接受。

常见的分析任务：
- **颜色统计**：统计每种颜色出现的次数，用于优化调色板或检测颜色泄漏。
- **边界框计算**：找到非透明像素的最小外接矩形，用于裁剪。
- **透明度分析**：计算透明像素比例，判断图像是否过于稀疏。
- **对称性检测**：检查图像是否水平或垂直对称。
- **像素计数**：统计特定颜色或区域的像素数。

对于多帧精灵，分析可以针对单帧、活动 Cel 或所有 Cel 进行。分析结果可以输出到控制台、写入文件，或用于驱动后续的自动化操作。

### 例子

**示例 1：颜色统计**

```lua
-- color_stats.lua
local sprite = app.activeSprite
if not sprite then return end

local img = app.activeImage
if not img then return end

-- 统计颜色使用
local colorStats = {}
local totalPixels = 0
local transparentPixels = 0

for y = 0, img.height - 1 do
    for x = 0, img.width - 1 do
        local pixel = img:getPixel(x, y)
        totalPixels = totalPixels + 1

        if img.colorMode == ColorMode.RGB then
            local r, g, b, a = app.pixelColor.rgba(pixel)
            if a == 0 then
                transparentPixels = transparentPixels + 1
            else
                local key = string.format("#%02X%02X%02X", r, g, b)
                colorStats[key] = (colorStats[key] or 0) + 1
            end
        elseif img.colorMode == ColorMode.INDEXED then
            if pixel == 0 then  -- 假设索引 0 是透明
                transparentPixels = transparentPixels + 1
            else
                colorStats[pixel] = (colorStats[pixel] or 0) + 1
            end
        end
    end
end

-- 输出统计
print(string.format("图像尺寸: %dx%d", img.width, img.height))
print(string.format("总像素: %d", totalPixels))
print(string.format("透明像素: %d (%.1f%%)",
    transparentPixels, transparentPixels / totalPixels * 100))
print(string.format("不同颜色数: %d", #colorStats))

-- 按使用频率排序
local sortedColors = {}
for color, count in pairs(colorStats) do
    sortedColors[#sortedColors + 1] = {color = color, count = count}
end
table.sort(sortedColors, function(a, b) return a.count > b.count end)

print("\n前 10 种颜色:")
for i = 1, math.min(10, #sortedColors) do
    local entry = sortedColors[i]
    print(string.format("  %s: %d 像素 (%.1f%%)",
        entry.color, entry.count, entry.count / totalPixels * 100))
end
```

**示例 2：计算边界框**

```lua
-- bounding_box.lua
-- 找到非透明像素的最小外接矩形

local sprite = app.activeSprite
if not sprite then return end

local img = app.activeImage
if not img then return end

local minX, minY = img.width, img.height
local maxX, maxY = -1, -1

for y = 0, img.height - 1 do
    for x = 0, img.width - 1 do
        local pixel = img:getPixel(x, y)
        local isOpaque = false

        if img.colorMode == ColorMode.RGB then
            local _, _, _, a = app.pixelColor.rgba(pixel)
            isOpaque = a > 0
        elseif img.colorMode == ColorMode.INDEXED then
            isOpaque = pixel > 0  -- 假设索引 0 是透明
        end

        if isOpaque then
            if x < minX then minX = x end
            if y < minY then minY = y end
            if x > maxX then maxX = x end
            if y > maxY then maxY = y end
        end
    end
end

if maxX >= 0 then
    local width = maxX - minX + 1
    local height = maxY - minY + 1
    print(string.format("边界框: (%d, %d) %dx%d", minX, minY, width, height))

    -- 可选：裁剪到边界框
    -- sprite.selection = Selection(Rectangle(minX, minY, width, height))
    -- app.command.Crop()
else
    print("图像完全透明")
end
```

**示例 3：对称性检测**

```lua
-- symmetry_check.lua
-- 检查图像是否水平/垂直对称

local sprite = app.activeSprite
if not sprite then return end

local img = app.activeImage
if not img then return end

-- 检查水平对称（左右对称）
local function isHorizontallySymmetric(img)
    local w, h = img.width, img.height
    for y = 0, h - 1 do
        for x = 0, math.floor(w / 2) - 1 do
            if img:getPixel(x, y) ~= img:getPixel(w - 1 - x, y) then
                return false, x, y
            end
        end
    end
    return true
end

-- 检查垂直对称（上下对称）
local function isVerticallySymmetric(img)
    local w, h = img.width, img.height
    for y = 0, math.floor(h / 2) - 1 do
        for x = 0, w - 1 do
            if img:getPixel(x, y) ~= img:getPixel(x, h - 1 - y) then
                return false, x, y
            end
        end
    end
    return true
end

local hSym = isHorizontallySymmetric(img)
local vSym = isVerticallySymmetric(img)

print("水平对称:", hSym and "是" or "否")
print("垂直对称:", vSym and "是" or "否")

if hSym and vSym then
    print("图像完全对称")
elseif hSym then
    print("图像左右对称（可考虑只存储一半）")
elseif vSym then
    print("图像上下对称")
end
```

**示例 4：分析所有帧**

```lua
-- analyze_all_frames.lua
local sprite = app.activeSprite
if not sprite then return end

print(string.format("精灵分析: %s", sprite.filename))
print(string.format("尺寸: %dx%d, 帧数: %d\n", sprite.width, sprite.height, #sprite.frames))

for i, frame in ipairs(sprite.frames) do
    print(string.format("--- 帧 %d ---", i))

    -- 合成所有可见图层
    local img = Image(sprite.width, sprite.height, sprite.colorMode)
    img:clear()

    for _, layer in ipairs(sprite.layers) do
        if layer.isImage and layer.visible then
            local cel = layer:cel(frame)
            if cel and cel.image then
                img:drawImage(cel.image, cel.position.x, cel.position.y)
            end
        end
    end

    -- 统计
    local opaquePixels = 0
    local totalPixels = img.width * img.height

    for y = 0, img.height - 1 do
        for x = 0, img.width - 1 do
            local pixel = img:getPixel(x, y)
            local isOpaque = false
            if img.colorMode == ColorMode.RGB then
                local _, _, _, a = app.pixelColor.rgba(pixel)
                isOpaque = a > 0
            elseif img.colorMode == ColorMode.INDEXED then
                isOpaque = pixel > 0
            end
            if isOpaque then opaquePixels = opaquePixels + 1 end
        end
    end

    print(string.format("  不透明像素: %d/%d (%.1f%%)",
        opaquePixels, totalPixels, opaquePixels / totalPixels * 100))
    print(string.format("  帧时长: %dms", frame.duration * 1000))
end
```

**示例 5：生成颜色直方图**

```lua
-- color_histogram.lua
-- 生成颜色分布的文本直方图

local sprite = app.activeSprite
if not sprite then return end

local img = app.activeImage
if not img or img.colorMode ~= ColorMode.RGB then
    app.alert("需要 RGB 模式图像")
    return
end

-- 按亮度分桶
local buckets = {}
for i = 0, 15 do buckets[i] = 0 end

for y = 0, img.height - 1 do
    for x = 0, img.width - 1 do
        local pixel = img:getPixel(x, y)
        local r, g, b, a = app.pixelColor.rgba(pixel)
        if a > 0 then
            local brightness = (r + g + b) / 3
            local bucket = math.floor(brightness / 16)
            if bucket > 15 then bucket = 15 end
            buckets[bucket] = buckets[bucket] + 1
        end
    end
end

-- 找最大值用于缩放
local maxCount = 0
for _, count in pairs(buckets) do
    if count > maxCount then maxCount = count end
end

-- 输出直方图
print("\n亮度分布直方图:")
print("亮度范围    像素数    分布")
for i = 0, 15 do
    local barLength = math.floor(buckets[i] / maxCount * 50)
    local bar = string.rep("█", barLength)
    print(string.format("%3d-%3d    %6d    %s",
        i * 16, i * 16 + 15, buckets[i], bar))
end
```

### 总结

- `img:getPixel(x, y)` 逐像素读取，是图像分析的基础。
- 颜色统计：用表记录每种颜色的出现次数。
- 边界框：遍历找非透明像素的最小/最大坐标。
- 对称性检测：比较对称位置的像素值。
- 多帧分析：合成所有可见图层后统计。
- **注意事项**：RGB 模式用 `app.pixelColor.rgba` 分解像素值；索引模式的"透明"通常是索引 0；大图像分析可能较慢，可考虑采样。

---

## 第 18 讲：切片（Slice）与导出自动化

### 概念

切片（Slice）是 Aseprite 中标记画布上矩形区域的对象。切片常用于定义 UI 元素的九宫格、导出时的裁剪区域、或为游戏引擎标记精灵的子区域。通过 Lua API 可以程序化创建和管理切片，实现自动化导出。

### 原理

Aseprite 的切片系统：
- `sprite.slices`：精灵的切片列表。
- `sprite:newSlice(rect)`：在指定矩形创建切片。
- `slice.name`：切片名称。
- `slice.bounds`：切片的矩形区域（Rectangle 对象）。
- `slice.color`：切片颜色标签。
- `slice.data`：用户自定义数据字符串（可存储 JSON 等格式）。
- `slice.center`：九宫格中心区域（用于 9-slice 缩放）。
- `slice.pivot`：锚点位置（用于游戏中的旋转中心）。
- `sprite:deleteSlice(slice)`：删除切片。

切片导出：
- 命令行 `--split-slices` 按切片分别导出。
- `app.command.ExportSpriteSheet` 可以按切片布局导出。
- 每个切片导出为单独的 PNG 文件，文件名使用切片名称。

切片的典型应用：
1. **UI 九宫格**：定义按钮、面板的可缩放区域。
2. **精灵图集**：将多个独立元素放在一个精灵中，用切片标记各自区域。
3. **碰撞框**：为游戏引擎标记碰撞检测区域。
4. **锚点**：标记角色的脚部位置、武器的握持点等。

### 例子

**示例 1：创建和管理切片**

```lua
-- slice_basics.lua
local sprite = app.activeSprite
if not sprite then return end

-- 创建切片
local slice1 = sprite:newSlice(Rectangle(0, 0, 32, 32))
slice1.name = "head"
slice1.color = Color(255, 0, 0)

local slice2 = sprite:newSlice(Rectangle(0, 32, 32, 32))
slice2.name = "body"
slice2.color = Color(0, 255, 0)

local slice3 = sprite:newSlice(Rectangle(0, 64, 32, 32))
slice3.name = "legs"
slice3.color = Color(0, 0, 255)

-- 列出所有切片
print("=== 切片列表 ===")
for i, slice in ipairs(sprite.slices) do
    print(string.format("[%d] %s: (%d,%d) %dx%d",
        i, slice.name,
        slice.bounds.x, slice.bounds.y,
        slice.bounds.width, slice.bounds.height))
end
```

**示例 2：九宫格切片**

```lua
-- nine_slice.lua
-- 为 UI 元素创建九宫格切片

local sprite = app.activeSprite
if not sprite then return end

-- 创建覆盖整个画布的切片
local slice = sprite:newSlice(Rectangle(0, 0, sprite.width, sprite.height))
slice.name = "button_bg"

-- 设置九宫格中心区域（可缩放部分）
-- 假设边框为 4 像素
slice.center = Rectangle(4, 4, sprite.width - 8, sprite.height - 8)

-- 设置锚点（中心底部，用于 UI 定位）
slice.pivot = Point(sprite.width / 2, sprite.height)

-- 存储自定义数据
slice.data = '{"type": "button", "border": 4}'

print("九宫格切片已创建")
print("中心区域:", slice.center.x, slice.center.y, slice.center.width, slice.center.height)
print("锚点:", slice.pivot.x, slice.pivot.y)
```

**示例 3：自动生成切片网格**

```lua
-- auto_slice_grid.lua
-- 将画布按网格自动切分为多个切片

local sprite = app.activeSprite
if not sprite then return end

local cellW = tonumber(app.params["cellW"] or "32")
local cellH = tonumber(app.params["cellH"] or "32")
local prefix = app.params["prefix"] or "cell"

local cols = math.floor(sprite.width / cellW)
local rows = math.floor(sprite.height / cellH)

print(string.format("创建 %dx%d 网格切片 (单元 %dx%d)", cols, rows, cellW, cellH))

app.transaction(function()
    for row = 0, rows - 1 do
        for col = 0, cols - 1 do
            local rect = Rectangle(col * cellW, row * cellH, cellW, cellH)
            local slice = sprite:newSlice(rect)
            slice.name = string.format("%s_%d_%d", prefix, col, row)
        end
    end
end)

print(string.format("已创建 %d 个切片", cols * rows))
```

**示例 4：按切片导出**

```lua
-- export_slices.lua
-- 将每个切片导出为单独的 PNG

local sprite = app.activeSprite
if not sprite then return end

local outputDir = app.params["output"] or "./slices"
os.execute('mkdir -p "' .. outputDir .. '"')

for _, slice in ipairs(sprite.slices) do
    -- 选择切片区域
    sprite.selection = Selection(slice.bounds)

    -- 复制
    app.command.Copy()

    -- 创建新精灵并粘贴
    local newSprite = Sprite(slice.bounds.width, slice.bounds.height, sprite.colorMode)
    app.activeSprite = newSprite
    app.command.Paste()

    -- 保存
    local filename = string.format("%s/%s.png", outputDir, slice.name)
    newSprite:saveCopyAs(filename)
    print("已导出: " .. filename)

    -- 关闭
    newSprite:close()
    app.activeSprite = sprite
end

sprite.selection:deselect()
print("切片导出完成")
```

**示例 5：从 JSON 导入切片定义**

```lua
-- import_slices.lua
-- 从 JSON 文件导入切片定义

local sprite = app.activeSprite
if not sprite then return end

local jsonFile = app.params["json"]
if not jsonFile then
    print("用法: --script-param json=<文件路径>")
    os.exit(1)
end

-- 简单 JSON 解析（实际项目建议使用 dkjson 等 JSON 库）
local function loadSliceDefinitions(path)
    local f = io.open(path, "r")
    if not f then return nil end
    local content = f:read("*a")
    f:close()

    -- 这里简化处理，假设格式为每行 "name,x,y,w,h"
    local defs = {}
    for line in content:gmatch("[^\n]+") do
        local name, x, y, w, h = line:match("([^,]+),(%d+),(%d+),(%d+),(%d+)")
        if name then
            defs[#defs + 1] = {
                name = name,
                x = tonumber(x), y = tonumber(y),
                w = tonumber(w), h = tonumber(h)
            }
        end
    end
    return defs
end

local defs = loadSliceDefinitions(jsonFile)
if not defs then
    print("无法加载切片定义")
    os.exit(1)
end

app.transaction(function()
    for _, def in ipairs(defs) do
        local slice = sprite:newSlice(Rectangle(def.x, def.y, def.w, def.h))
        slice.name = def.name
        print(string.format("创建切片: %s (%d,%d %dx%d)",
            def.name, def.x, def.y, def.w, def.h))
    end
end)

print(string.format("共导入 %d 个切片", #defs))
```

### 总结

- 切片标记画布上的矩形区域，用于导出和元数据。
- `sprite:newSlice(rect)` 创建切片，`slice.name`/`bounds`/`center`/`pivot`/`data` 设置属性。
- 九宫格切片通过 `slice.center` 定义可缩放区域。
- `--split-slices` 命令行参数按切片分别导出。
- **注意事项**：切片操作应在事务中执行；导出切片时需复制到新精灵；`slice.data` 可存储 JSON 等自定义数据供引擎使用。

---

# 第六章：插件与扩展开发

Aseprite 支持通过插件扩展功能。插件可以添加菜单项、对话框、命令等，将自动化脚本封装为可分发的扩展。本章讲解插件的结构、GUI 对话框开发和菜单集成。

---

## 第 19 讲：插件结构与 package.json

### 概念

Aseprite 插件是一个包含 `package.json` 清单文件和 Lua 脚本的文件夹。`package.json` 声明插件的元数据、入口点和贡献点（菜单项、命令等）。插件安装后，其功能集成到 Aseprite 的菜单和命令系统中，用户可以像使用内置功能一样使用插件。

### 原理

Aseprite 插件的标准目录结构：
```
my-plugin/
├── package.json      # 插件清单
├── main.lua          # 主入口脚本
├── commands/         # 命令脚本目录
│   ├── cmd1.lua
│   └── cmd2.lua
└── dialogs/          # 对话框脚本目录
    └── settings.lua
```

`package.json` 的关键字段：
- `name`：插件唯一标识符（小写字母、数字、连字符）。
- `displayName`：显示名称。
- `version`：语义化版本号。
- `description`：插件描述。
- `author`：作者信息。
- `main`：主入口脚本（默认 `main.lua`）。
- `contributes`：贡献点，声明插件提供的功能。
  - `commands`：命令列表，每个命令有 `id`、`title`、`onclick`（脚本路径）。
  - `menus`：菜单项，将命令添加到指定菜单。
  - `dialogs`：对话框。

插件加载流程：
1. Aseprite 启动时扫描插件目录。
2. 读取 `package.json`，解析元数据和贡献点。
3. 执行 `main.lua`，注册命令和菜单。
4. 用户点击菜单项时，执行对应的命令脚本。

### 例子

**示例 1：最小插件结构**

```
my-plugin/
├── package.json
└── main.lua
```

`package.json`：
```json
{
    "name": "my-first-plugin",
    "displayName": "我的第一个插件",
    "version": "1.0.0",
    "description": "一个简单的 Aseprite 插件示例",
    "author": {
        "name": "Your Name",
        "email": "you@example.com",
        "url": "https://example.com"
    },
    "main": "main.lua",
    "contributes": {
        "commands": [
            {
                "id": "HelloCommand",
                "title": "打招呼",
                "onclick": "commands/hello.lua"
            }
        ],
        "menus": [
            {
                "title": "我的插件",
                "items": [
                    "HelloCommand"
                ]
            }
        ]
    }
}
```

`main.lua`：
```lua
-- main.lua
-- 插件入口，在 Aseprite 启动时执行一次

print("我的第一个插件已加载")

-- 可以在这里初始化全局状态
-- 但命令的实际逻辑放在 commands/ 目录
```

`commands/hello.lua`：
```lua
-- commands/hello.lua
-- 当用户点击"打招呼"菜单项时执行

local sprite = app.activeSprite
if sprite then
    app.alert("你好！当前精灵: " .. sprite.filename)
else
    app.alert("你好！请先打开一个精灵")
end
```

**示例 2：多命令插件**

```json
{
    "name": "pixel-tools",
    "displayName": "像素工具集",
    "version": "1.2.0",
    "description": "像素艺术辅助工具集合",
    "author": { "name": "Pixel Artist" },
    "main": "main.lua",
    "contributes": {
        "commands": [
            {
                "id": "InvertColors",
                "title": "反色",
                "onclick": "commands/invert.lua"
            },
            {
                "id": "AddBorder",
                "title": "添加边框",
                "onclick": "commands/border.lua"
            },
            {
                "id": "CountColors",
                "title": "统计颜色",
                "onclick": "commands/count.lua"
            },
            {
                "id": "ExportSlices",
                "title": "导出切片",
                "onclick": "commands/export_slices.lua"
            }
        ],
        "menus": [
            {
                "title": "像素工具",
                "groups": [
                    {
                        "title": "编辑",
                        "items": [
                            "InvertColors",
                            "AddBorder"
                        ]
                    },
                    {
                        "title": "分析",
                        "items": [
                            "CountColors"
                        ]
                    },
                    {
                        "title": "导出",
                        "items": [
                            "ExportSlices"
                        ]
                    }
                ]
            }
        ]
    }
}
```

**示例 3：插件初始化逻辑**

```lua
-- main.lua
-- 插件初始化，在 Aseprite 启动时执行

local PLUGIN_NAME = "像素工具集"
local PLUGIN_VERSION = "1.2.0"

-- 检查 Aseprite 版本
if app.apiVersion < 1 then
    print(PLUGIN_NAME .. " 需要 Aseprite API 1 或更高版本")
    return
end

-- 全局配置表（插件范围内共享）
local config = {
    defaultBorderSize = 1,
    defaultBorderColor = Color(0, 0, 0, 255),
    exportDir = "./export"
}

-- 将配置存到全局，供命令脚本访问
_G.PixelTools = {
    config = config,
    version = PLUGIN_VERSION
}

print(string.format("%s v%s 已加载 (Aseprite API %d)",
    PLUGIN_NAME, PLUGIN_VERSION, app.apiVersion))
```

**示例 4：命令脚本示例**

```lua
-- commands/border.lua
-- 为当前精灵添加边框

local config = _G.PixelTools and _G.PixelTools.config or {}

local sprite = app.activeSprite
if not sprite then
    app.alert("请先打开精灵")
    return
end

local borderSize = config.defaultBorderSize or 1
local borderColor = config.defaultBorderColor or Color(0, 0, 0, 255)

app.transaction(function()
    -- 扩展画布
    local newW = sprite.width + borderSize * 2
    local newH = sprite.height + borderSize * 2
    app.command.CanvasSize{
        ui = false,
        width = newW,
        height = newH,
        bounds = Rectangle(-borderSize, -borderSize, newW, newH)
    }

    -- 在每个 Cel 周围画边框
    for _, cel in ipairs(sprite.cels) do
        local img = cel.image
        local w, h = img.width, img.height

        -- 画边框
        for x = 0, w - 1 do
            img:drawPixel(x, 0, borderColor)
            img:drawPixel(x, h - 1, borderColor)
        end
        for y = 0, h - 1 do
            img:drawPixel(0, y, borderColor)
            img:drawPixel(w - 1, y, borderColor)
        end
    end
end)

app.alert("已添加 " .. borderSize .. " 像素边框")
```

**示例 5：安装和调试插件**

```bash
# 插件安装位置：
# Windows: %APPDATA%/Aseprite/extensions/
# macOS:   ~/Library/Application Support/Aseprite/extensions/
# Linux:   ~/.config/aseprite/extensions/

# 开发时可以创建符号链接，避免反复复制
ln -s /path/to/my-plugin ~/.config/aseprite/extensions/my-plugin

# 调试：在 Aseprite 中 View > Developer > Developer Console 查看输出
# 修改脚本后，在 Extensions 管理器中重新加载插件
```

### 总结

- 插件是包含 `package.json` 和 Lua 脚本的文件夹。
- `package.json` 声明元数据、命令和菜单。
- `contributes.commands` 定义命令，`contributes.menus` 定义菜单结构。
- `main.lua` 在插件加载时执行一次，用于初始化。
- 命令脚本在用户点击菜单项时执行。
- **注意事项**：插件 `name` 必须唯一；命令 `id` 在插件内唯一；开发时用符号链接避免反复复制；通过 Developer Console 查看日志。

---

## 第 20 讲：对话框 GUI 开发（Dialog）

### 概念

`Dialog` 是 Aseprite Lua API 提供的 GUI 对话框构建工具。通过 Dialog 可以创建包含按钮、文本框、滑块、颜色选择器等控件的窗口，实现用户交互。Dialog 是插件开发的核心，使脚本能够接收用户输入而非硬编码参数。

### 原理

Dialog 的工作方式是声明式的：先创建 Dialog 对象，然后链式调用方法添加控件，最后调用 `show()` 显示。每个控件有一个唯一 ID，用户交互后通过 `dialog.data[id]` 获取控件值。

Dialog 的常用控件方法：
- `dialog:label{text=}`：添加标签文本。
- `dialog:entry{id=, label=, text=}`：文本输入框。
- `dialog:number{id=, label=, text=}`：数字输入框。
- `dialog:slider{id=, label=, min=, max=, value=}`：滑块。
- `dialog:color{id=, label=, color=}`：颜色选择器。
- `dialog:button{text=, onclick=}`：按钮。
- `dialog:check{id=, label=, selected=}`：复选框。
- `dialog:radio{id=, label=, text=}`：单选按钮组。
- `dialog:combobox{id=, label=, options=, option=}`：下拉选择框。
- `dialog:file{id=, label=, title=, save=}`：文件选择器。
- `dialog:separator{}`：分隔线。

Dialog 的方法：
- `dialog:show()` / `dialog:show{wait=}`：显示对话框。
- `dialog:close()`：关闭对话框。
- `dialog:modify{id=, ...}`：修改控件属性。
- `dialog.data`：获取所有控件值的表。
- `dialog:repaint()`：重绘对话框。

### 例子

**示例 1：简单对话框**

```lua
-- simple_dialog.lua
local dlg = Dialog("我的工具")

dlg:label{text="这是一个简单对话框"}
dlg:button{text="确定", onclick=function()
    app.alert("你点击了确定")
    dlg:close()
end}
dlg:button{text="取消", onclick=function()
    dlg:close()
end}

dlg:show()
```

**示例 2：参数化操作对话框**

```lua
-- resize_dialog.lua
-- 带参数的缩放对话框

local sprite = app.activeSprite
if not sprite then
    app.alert("请先打开精灵")
    return
end

local dlg = Dialog("缩放精灵")

dlg:slider{id="scale", label="缩放倍数", min=1, max=8, value=2}
dlg:combobox{id="method", label="算法", options={"nearest-neighbor", "bilinear"}, option="nearest-neighbor"}
dlg:check{id="newFile", label="在新文件中打开", selected=false}
dlg:separator{}
dlg:button{text="确定", onclick=function()
    local data = dlg.data

    app.transaction(function()
        if data.newFile then
            -- 复制到新精灵
            app.command.DuplicateSprite()
        end

        app.command.SpriteSize{
            ui = false,
            scale = data.scale,
            method = data.method
        }
    end)

    app.alert(string.format("已缩放 %dx (%s)", data.scale, data.method))
    dlg:close()
end}
dlg:button{text="取消", onclick=function() dlg:close() end}

dlg:show()
```

**示例 3：颜色和文件选择**

```lua
-- border_dialog.lua
-- 添加边框的对话框

local sprite = app.activeSprite
if not sprite then return end

local dlg = Dialog("添加边框")

dlg:number{id="size", label="边框大小", text="1"}
dlg:color{id="color", label="边框颜色", color=Color(0, 0, 0, 255)}
dlg:check{id="allFrames", label="应用到所有帧", selected=true}
dlg:separator{}
dlg:button{text="应用", onclick=function()
    local data = dlg.data
    local size = math.floor(data.size)
    local color = data.color

    app.transaction(function()
        -- 扩展画布
        app.command.CanvasSize{
            ui = false,
            width = sprite.width + size * 2,
            height = sprite.height + size * 2,
            bounds = Rectangle(-size, -size, sprite.width + size * 2, sprite.height + size * 2)
        }

        -- 画边框
        local frames = data.allFrames and sprite.frames or {app.activeFrame}
        for _, frame in ipairs(frames) do
            for _, layer in ipairs(sprite.layers) do
                if layer.isImage then
                    local cel = layer:cel(frame)
                    if cel and cel.image then
                        local img = cel.image
                        for x = 0, img.width - 1 do
                            for s = 0, size - 1 do
                                img:drawPixel(x, s, color)
                                img:drawPixel(x, img.height - 1 - s, color)
                            end
                        end
                        for y = 0, img.height - 1 do
                            for s = 0, size - 1 do
                                img:drawPixel(s, y, color)
                                img:drawPixel(img.width - 1 - s, y, color)
                            end
                        end
                    end
                end
            end
        end
    end)

    dlg:close()
end}
dlg:button{text="取消", onclick=function() dlg:close() end}

dlg:show()
```

**示例 4：动态更新对话框**

```lua
-- dynamic_dialog.lua
-- 根据输入动态更新预览

local sprite = app.activeSprite
if not sprite then return end

local dlg = Dialog("动态预览")

dlg:slider{id="threshold", label="阈值", min=0, max=255, value=128}
dlg:label{id="preview", label="预览", text="阈值: 128"}
dlg:button{text="应用", onclick=function()
    local data = dlg.data
    local img = app.activeImage
    if not img then return end

    app.transaction(function()
        for y = 0, img.height - 1 do
            for x = 0, img.width - 1 do
                local pixel = img:getPixel(x, y)
                local r, g, b, a = app.pixelColor.rgba(pixel)
                local brightness = (r + g + b) / 3
                if brightness > data.threshold then
                    img:drawPixel(x, y, Color(255, 255, 255, a))
                else
                    img:drawPixel(x, y, Color(0, 0, 0, a))
                end
            end
        end
    end)
    dlg:close()
end}

-- 滑块变化时更新预览标签
dlg:slider{id="threshold", label="阈值", min=0, max=255, value=128,
    onchange=function()
        dlg:modify{label="预览", text="阈值: " .. dlg.data.threshold}
    end
}

dlg:show()
```

**示例 5：批量处理对话框**

```lua
-- batch_dialog.lua
-- 批量处理的参数化对话框

local dlg = Dialog("批量处理")

dlg:file{id="inputDir", label="输入目录", title="选择文件夹", filename="", save=false}
dlg:file{id="outputDir", label="输出目录", title="选择输出位置", filename="", save=false}
dlg:combobox{id="format", label="输出格式", options={"png", "gif", "bmp"}, option="png"}
dlg:slider{id="scale", label="缩放", min=1, max=4, value=1}
dlg:check{id="trim", label="修剪透明", selected=false}
dlg:separator{}
dlg:label{text="点击开始将处理输入目录中的所有 .aseprite 文件"}
dlg:button{text="开始", onclick=function()
    local data = dlg.data
    if not data.inputDir or data.inputDir == "" then
        app.alert("请选择输入目录")
        return
    end
    if not data.outputDir or data.outputDir == "" then
        app.alert("请选择输出目录")
        return
    end

    -- 执行批量处理（调用第 16 讲的批处理逻辑）
    local lfs = require("lfs")
    local count = 0

    for file in lfs.dir(data.inputDir) do
        if file:match("%.aseprite$") then
            local ok, err = pcall(function()
                local sprite = app.open(data.inputDir .. "/" .. file)
                if not sprite then return end

                if data.scale > 1 then
                    app.command.SpriteSize{ui=false, scale=data.scale, method="nearest-neighbor"}
                end

                if data.trim then
                    app.command.Trim()
                end

                local basename = file:match("(.+)%..+$")
                sprite:saveCopyAs(data.outputDir .. "/" .. basename .. "." .. data.format)
                sprite:close()
                count = count + 1
            end)

            if not ok then
                print("错误: " .. file .. " - " .. err)
            end
        end
    end

    app.alert(string.format("完成！共处理 %d 个文件", count))
    dlg:close()
end}
dlg:button{text="取消", onclick=function() dlg:close() end}

dlg:show()
```

### 总结

- `Dialog(title)` 创建对话框，链式调用添加控件。
- 常用控件：`entry`、`number`、`slider`、`color`、`check`、`combobox`、`file`、`button`。
- `dialog.data[id]` 获取控件值，`dialog:modify{id=, ...}` 修改控件。
- `onchange` / `onclick` 回调实现交互逻辑。
- **注意事项**：控件 ID 必须唯一；颜色控件返回 `Color` 对象；文件控件返回路径字符串；`dlg:show{wait=true}` 模态显示（阻塞直到关闭）。

---

## 第 21 讲：命令注册与菜单集成

### 概念

命令注册是将 Lua 函数绑定到 Aseprite 命令系统的过程。注册后的命令可以通过菜单、快捷键或 `app.command` 调用。菜单集成将命令添加到 Aseprite 的菜单栏，使用户能方便地访问插件功能。这是插件开发的最后一步，使插件成为 Aseprite 的"一等公民"。

### 原理

Aseprite 的命令系统基于 `Command` 类。通过 `Command` 可以创建自定义命令，注册到命令管理器，并添加到菜单。

命令注册的两种方式：
1. **通过 `package.json`**：在 `contributes.commands` 中声明，`onclick` 指定脚本路径。这是推荐方式，声明清晰。
2. **通过 Lua API**：在 `main.lua` 中使用 `Command` 类动态注册。更灵活，但代码更复杂。

菜单集成：
- `package.json` 的 `contributes.menus` 声明菜单结构。
- 菜单可以分组（`groups`），添加分隔线。
- 菜单项引用命令 ID。

快捷键绑定：
- 用户可以在 Aseprite 设置中为命令分配快捷键。
- 命令的 `title` 会显示在快捷键设置界面。

### 例子

**示例 1：通过 package.json 注册命令**

```json
{
    "name": "quick-tools",
    "displayName": "快捷工具",
    "version": "1.0.0",
    "description": "快速操作工具集",
    "author": { "name": "Developer" },
    "contributes": {
        "commands": [
            {
                "id": "FlipHorizontal",
                "title": "水平翻转",
                "onclick": "commands/flip_h.lua"
            },
            {
                "id": "FlipVertical",
                "title": "垂直翻转",
                "onclick": "commands/flip_v.lua"
            },
            {
                "id": "CenterSprite",
                "title": "居中精灵",
                "onclick": "commands/center.lua"
            }
        ],
        "menus": [
            {
                "title": "快捷工具",
                "groups": [
                    {
                        "title": "翻转",
                        "items": ["FlipHorizontal", "FlipVertical"]
                    },
                    {
                        "items": ["CenterSprite"]
                    }
                ]
            }
        ]
    }
}
```

**示例 2：命令脚本**

```lua
-- commands/flip_h.lua
local sprite = app.activeSprite
if not sprite then
    app.alert("请先打开精灵")
    return
end

app.transaction(function()
    app.command.Flip{target="canvas", orientation="horizontal"}
end)
```

```lua
-- commands/center.lua
-- 将精灵内容居中到画布

local sprite = app.activeSprite
if not sprite then return end

-- 计算所有 Cel 的边界框
local minX, minY = sprite.width, sprite.height
local maxX, maxY = 0, 0

for _, cel in ipairs(sprite.cels) do
    if cel.image then
        local bounds = cel.bounds
        if bounds.x < minX then minX = bounds.x end
        if bounds.y < minY then minY = bounds.y end
        if bounds.x + bounds.width > maxX then maxX = bounds.x + bounds.width end
        if bounds.y + bounds.height > maxY then maxY = bounds.y + bounds.height end
    end
end

if maxX > minX then
    local contentW = maxX - minX
    local contentH = maxY - minY
    local offsetX = math.floor((sprite.width - contentW) / 2) - minX
    local offsetY = math.floor((sprite.height - contentH) / 2) - minY

    app.transaction(function()
        for _, cel in ipairs(sprite.cels) do
            cel.position = Point(cel.position.x + offsetX, cel.position.y + offsetY)
        end
    end)

    app.alert("已居中")
end
```

**示例 3：通过 Lua API 动态注册命令**

```lua
-- main.lua
-- 动态注册命令（不通过 package.json）

-- 定义命令函数
local function flipHorizontal()
    local sprite = app.activeSprite
    if not sprite then
        app.alert("请先打开精灵")
        return
    end
    app.transaction(function()
        app.command.Flip{target="canvas", orientation="horizontal"}
    end)
end

local function flipVertical()
    local sprite = app.activeSprite
    if not sprite then return end
    app.transaction(function()
        app.command.Flip{target="canvas", orientation="vertical"}
    end)
end

-- 注册命令
-- 注意：这种方式需要 Aseprite API 支持
-- 实际中更推荐使用 package.json 声明

-- 将函数存到全局，供 package.json 的 onclick 脚本调用
_G.MyTools = {
    flipHorizontal = flipHorizontal,
    flipVertical = flipVertical
}

print("快捷工具已加载")
```

**示例 4：带快捷键提示的命令**

```json
{
    "name": "shortcut-tools",
    "displayName": "快捷键工具",
    "version": "1.0.0",
    "contributes": {
        "commands": [
            {
                "id": "QuickExport",
                "title": "快速导出 PNG",
                "onclick": "commands/quick_export.lua"
            },
            {
                "id": "QuickResize",
                "title": "快速缩放 2x",
                "onclick": "commands/quick_resize.lua"
            }
        ],
        "menus": [
            {
                "title": "快捷键工具",
                "items": ["QuickExport", "QuickResize"]
            }
        ],
        "keybindings": {
            "QuickExport": "Ctrl+Shift+E",
            "QuickResize": "Ctrl+Shift+R"
        }
    }
}
```

```lua
-- commands/quick_export.lua
local sprite = app.activeSprite
if not sprite then
    app.alert("请先打开精灵")
    return
end

local filename = sprite.filename
if filename == "" then
    app.alert("请先保存精灵")
    return
end

local pngName = filename:gsub("%.aseprite$", ".png")
sprite:saveCopyAs(pngName)
app.alert("已导出: " .. pngName)
```

**示例 5：完整的插件示例**

```lua
-- main.lua
-- 完整插件：像素艺术助手

local PLUGIN = {
    name = "像素艺术助手",
    version = "1.0.0"
}

-- 工具函数
function PLUGIN.countColors(img)
    local colors = {}
    for y = 0, img.height - 1 do
        for x = 0, img.width - 1 do
            local pixel = img:getPixel(x, y)
            colors[pixel] = (colors[pixel] or 0) + 1
        end
    end
    local count = 0
    for _ in pairs(colors) do count = count + 1 end
    return count
end

function PLUGIN.addBorder(sprite, size, color)
    app.transaction(function()
        app.command.CanvasSize{
            ui = false,
            width = sprite.width + size * 2,
            height = sprite.height + size * 2,
            bounds = Rectangle(-size, -size, sprite.width + size * 2, sprite.height + size * 2)
        }
    end)
end

-- 导出到全局
_G.PixelArtHelper = PLUGIN

print(string.format("%s v%s 已加载", PLUGIN.name, PLUGIN.version))
```

```lua
-- commands/count_colors.lua
local sprite = app.activeSprite
if not sprite then
    app.alert("请先打开精灵")
    return
end

local img = app.activeImage
if not img then return end

local count = _G.PixelArtHelper.countColors(img)
app.alert(string.format("颜色数: %d", count))
```

```lua
-- commands/border_dialog.lua
local sprite = app.activeSprite
if not sprite then return end

local dlg = Dialog("添加边框")
dlg:number{id="size", label="大小", text="1"}
dlg:color{id="color", label="颜色", color=Color(0, 0, 0, 255)}
dlg:button{text="确定", onclick=function()
    _G.PixelArtHelper.addBorder(sprite, dlg.data.size, dlg.data.color)
    dlg:close()
end}
dlg:button{text="取消", onclick=function() dlg:close() end}
dlg:show()
```

### 总结

- 命令注册通过 `package.json` 的 `contributes.commands` 声明。
- 菜单集成通过 `contributes.menus`，支持分组和分隔线。
- 快捷键通过 `contributes.keybindings` 绑定。
- 命令脚本在用户点击菜单或按快捷键时执行。
- `main.lua` 可定义全局工具函数，供命令脚本调用。
- **注意事项**：命令 ID 必须唯一；菜单 `title` 是用户可见文本；开发时通过 Developer Console 调试；发布前测试所有命令和快捷键。

---

# 第七章：实战项目

本章通过三个完整的实战项目，综合运用前面所学的知识。每个项目都是真实场景中常见的需求，涵盖批处理、程序化生成、动画流水线等核心应用。通过这些项目，您将掌握 Aseprite 自动化的实际应用能力。

---

## 第 22 讲：实战：批量生成精灵图集（Sprite Sheet）

### 概念

精灵图集（Sprite Sheet）是将多个小图合并为一张大图的技术，广泛用于游戏开发中以减少纹理切换开销。本实战项目将多个独立的 .aseprite 文件合并为一个精灵图集，同时生成 JSON 元数据文件，记录每个精灵在图集中的位置和尺寸。

### 原理

精灵图集生成的核心步骤：
1. **收集输入**：扫描目录，收集所有待合并的 .aseprite 文件。
2. **分析尺寸**：读取每个文件的尺寸，计算图集的布局。
3. **布局算法**：选择排列方式（水平、垂直、网格、打包）。
4. **合成图像**：将每个文件的第一帧绘制到图集的对应位置。
5. **生成元数据**：记录每个精灵的名称、位置、尺寸到 JSON 文件。
6. **导出**：保存图集 PNG 和 JSON 元数据。

布局算法的选择：
- **水平排列**：所有精灵排成一行，适合数量少且高度一致。
- **网格排列**：按固定列数排列，适合尺寸相近的精灵。
- **打包排列**：紧凑排列，最大化空间利用率（Aseprite 内置支持）。

### 例子

**完整项目代码**

```lua
-- sprite_sheet_generator.lua
-- 批量生成精灵图集
-- 用法: aseprite -b --script sprite_sheet_generator.lua --script-param input=./sprites --script-param output=./atlas.png --script-param columns=4

local lfs = require("lfs")

-- ========== 参数读取 ==========
local inputDir = app.params["input"] or "./sprites"
local outputPng = app.params["output"] or "./atlas.png"
local outputJson = outputPng:gsub("%.png$", ".json")
local columns = tonumber(app.params["columns"] or "4")
local padding = tonumber(app.params["padding"] or "0")
local cellSize = tonumber(app.params["cellSize"] or "0")  -- 0 表示自动

print(string.format("输入目录: %s", inputDir))
print(string.format("输出: %s + %s", outputPng, outputJson))
print(string.format("列数: %d, 间距: %d", columns, padding))

-- ========== 收集文件 ==========
local files = {}
for file in lfs.dir(inputDir) do
    if file:match("%.aseprite$") then
        files[#files + 1] = inputDir .. "/" .. file
    end
end

if #files == 0 then
    print("错误: 未找到 .aseprite 文件")
    os.exit(1)
end

print(string.format("找到 %d 个文件", #files))

-- ========== 分析尺寸 ==========
local sprites = {}
local maxW, maxH = 0, 0

for i, file in ipairs(files) do
    local sprite = app.open(file)
    if sprite then
        local w, h = sprite.width, sprite.height
        if cellSize > 0 then
            w, h = cellSize, cellSize
        end
        sprites[#sprites + 1] = {
            file = file,
            name = file:match("([^/\\]+)%.aseprite$"),
            width = w,
            height = h,
            sprite = sprite
        }
        if w > maxW then maxW = w end
        if h > maxH then maxH = h end
    end
end

print(string.format("最大单元尺寸: %dx%d", maxW, maxH))

-- ========== 计算布局 ==========
local rows = math.ceil(#sprites / columns)
local sheetW = columns * (maxW + padding) + padding
local sheetH = rows * (maxH + padding) + padding

print(string.format("图集尺寸: %dx%d (%d行 x %d列)", sheetW, sheetH, rows, columns))

-- ========== 创建图集 ==========
local sheet = Image(sheetW, sheetH, ColorMode.RGB)
sheet:clear()

local frames = {}

for i, entry in ipairs(sprites) do
    local col = (i - 1) % columns
    local row = math.floor((i - 1) / columns)
    local x = padding + col * (maxW + padding)
    local y = padding + row * (maxH + padding)

    -- 获取第一帧的合成图像
    local sprite = entry.sprite
    local frame = sprite.frames[1]

    -- 合成所有可见图层
    local compositeImg = Image(entry.width, entry.height, ColorMode.RGB)
    compositeImg:clear()

    for _, layer in ipairs(sprite.layers) do
        if layer.isImage and layer.visible then
            local cel = layer:cel(frame)
            if cel and cel.image then
                compositeImg:drawImage(cel.image, cel.position.x, cel.position.y)
            end
        end
    end

    -- 绘制到图集
    sheet:drawImage(compositeImg, x, y)

    -- 记录元数据
    frames[#frames + 1] = {
        name = entry.name,
        frame = {
            x = x,
            y = y,
            w = entry.width,
            h = entry.height
        }
    }

    print(string.format("[%d/%d] %s -> (%d, %d)", i, #sprites, entry.name, x, y))

    sprite:close()
end

-- ========== 保存图集 ==========
-- 创建临时精灵保存图集
local sheetSprite = Sprite(sheetW, sheetH, ColorMode.RGB)
sheetSprite.cels[1].image = sheet
sheetSprite:saveCopyAs(outputPng)
sheetSprite:close()

print(string.format("图集已保存: %s", outputPng))

-- ========== 生成 JSON ==========
local jsonFile = io.open(outputJson, "w")
jsonFile:write('{\n')
jsonFile:write(string.format('  "frames": {\n'))

for i, frame in ipairs(frames) do
    local comma = i < #frames and "," or ""
    jsonFile:write(string.format('    "%s": {"frame": {"x":%d,"y":%d,"w":%d,"h":%d}}%s\n',
        frame.name, frame.frame.x, frame.frame.y, frame.frame.w, frame.frame.h, comma))
end

jsonFile:write('  },\n')
jsonFile:write(string.format('  "meta": {\n'))
jsonFile:write(string.format('    "image": "%s",\n', outputPng:match("([^/\\]+)$")))
jsonFile:write(string.format('    "size": {"w":%d,"h":%d},\n', sheetW, sheetH))
jsonFile:write(string.format('    "scale": 1\n'))
jsonFile:write('  }\n')
jsonFile:write('}\n')
jsonFile:close()

print(string.format("元数据已保存: %s", outputJson))
print("=== 精灵图集生成完成 ===")
```

**使用示例**

```bash
# 基本用法
aseprite -b --script sprite_sheet_generator.lua \
  --script-param input=./characters \
  --script-param output=./character_atlas.png

# 指定列数和间距
aseprite -b --script sprite_sheet_generator.lua \
  --script-param input=./tiles \
  --script-param output=./tile_atlas.png \
  --script-param columns=8 \
  --script-param padding=2

# 统一单元尺寸
aseprite -b --script sprite_sheet_generator.lua \
  --script-param input=./icons \
  --script-param output=./icon_atlas.png \
  --script-param cellSize=32 \
  --script-param columns=16
```

**生成的 JSON 格式**

```json
{
  "frames": {
    "hero": {"frame": {"x":0,"y":0,"w":32,"h":32}},
    "goblin": {"frame": {"x":34,"y":0,"w":32,"h":32}},
    "dragon": {"frame": {"x":68,"y":0,"w":32,"h":32}}
  },
  "meta": {
    "image": "character_atlas.png",
    "size": {"w":256,"h":128},
    "scale": 1
  }
}
```

### 总结

- 精灵图集生成包含：文件收集、尺寸分析、布局计算、图像合成、元数据生成。
- 布局算法根据需求选择：水平、网格、打包。
- JSON 元数据记录每个精灵的位置和尺寸，供游戏引擎使用。
- 使用 `lfs` 遍历目录，`Image:drawImage` 合成图像。
- **注意事项**：处理完每个精灵后调用 `close()` 释放资源；JSON 格式需与目标引擎兼容；大图集注意内存限制。

---

## 第 23 讲：实战：程序化像素艺术生成器

### 概念

程序化生成（Procedural Generation）是通过算法自动生成图像内容的技术。本实战项目创建一个像素艺术生成器，能够程序化生成各种图案：随机噪点、对称图案、地形纹理、角色轮廓等。通过参数化控制，用户可以快速生成大量变体，为像素艺术创作提供灵感或基础素材。

### 原理

程序化像素艺术生成的核心算法：
1. **随机噪点**：使用伪随机数生成器为每个像素分配颜色。
2. **对称生成**：只生成一半图像，然后镜像复制，保证对称性。
3. **细胞自动机**：基于规则的迭代演化，生成洞穴、地形等有机图案。
4. **分形递归**：通过递归细分生成复杂图案。
5. **调色板映射**：将灰度值或高度值映射到调色板颜色。

本项目的生成器支持多种模式，通过对话框选择生成类型和参数。每种模式使用不同的算法，但都遵循"生成数据 → 映射颜色 → 绘制像素"的流程。

### 例子

**完整项目代码**

```lua
-- pixel_art_generator.lua
-- 程序化像素艺术生成器

local sprite = app.activeSprite
if not sprite then
    -- 创建新精灵
    sprite = Sprite(32, 32, ColorMode.RGB)
end

-- ========== 生成算法 ==========

-- 1. 随机噪点
local function generateNoise(img, w, h, colors)
    for y = 0, h - 1 do
        for x = 0, w - 1 do
            local colorIdx = math.random(1, #colors)
            img:drawPixel(x, y, colors[colorIdx])
        end
    end
end

-- 2. 对称图案（4 象限对称）
local function generateSymmetric(img, w, h, colors)
    local halfW = math.floor(w / 2)
    local halfH = math.floor(h / 2)

    -- 只生成左上象限
    for y = 0, halfH - 1 do
        for x = 0, halfW - 1 do
            local colorIdx = math.random(1, #colors)
            local color = colors[colorIdx]

            -- 镜像到其他三个象限
            img:drawPixel(x, y, color)
            img:drawPixel(w - 1 - x, y, color)
            img:drawPixel(x, h - 1 - y, color)
            img:drawPixel(w - 1 - x, h - 1 - y, color)
        end
    end
end

-- 3. 细胞自动机（洞穴生成）
local function generateCellular(img, w, h, fgColor, bgColor, fillProb, iterations)
    -- 初始化随机网格
    local grid = {}
    for y = 1, h do
        grid[y] = {}
        for x = 1, w do
            grid[y][x] = math.random() < fillProb and 1 or 0
        end
    end

    -- 迭代演化
    for iter = 1, iterations do
        local newGrid = {}
        for y = 1, h do
            newGrid[y] = {}
            for x = 1, w do
                -- 计算邻居中"墙"的数量
                local wallCount = 0
                for dy = -1, 1 do
                    for dx = -1, 1 do
                        if dx == 0 and dy == 0 then
                            -- 跳过自身
                        elseif x + dx < 1 or x + dx > w or y + dy < 1 or y + dy > h then
                            wallCount = wallCount + 1  -- 边界视为墙
                        elseif grid[y + dy][x + dx] == 1 then
                            wallCount = wallCount + 1
                        end
                    end
                end

                -- 规则：邻居墙数 >= 5 则为墙
                if wallCount >= 5 then
                    newGrid[y][x] = 1
                else
                    newGrid[y][x] = 0
                end
            end
        end
        grid = newGrid
    end

    -- 绘制到图像
    for y = 1, h do
        for x = 1, w do
            if grid[y][x] == 1 then
                img:drawPixel(x - 1, y - 1, fgColor)
            else
                img:drawPixel(x - 1, y - 1, bgColor)
            end
        end
    end
end

-- 4. 渐变图案
local function generateGradient(img, w, h, color1, color2, direction)
    for y = 0, h - 1 do
        for x = 0, w - 1 do
            local t
            if direction == "horizontal" then
                t = x / (w - 1)
            elseif direction == "vertical" then
                t = y / (h - 1)
            else  -- diagonal
                t = (x + y) / (w + h - 2)
            end

            local r = math.floor(color1.red + (color2.red - color1.red) * t)
            local g = math.floor(color1.green + (color2.green - color1.green) * t)
            local b = math.floor(color1.blue + (color2.blue - color1.blue) * t)
            img:drawPixel(x, y, Color(r, g, b, 255))
        end
    end
end

-- 5. 同心圆图案
local function generateRings(img, w, h, colors)
    local cx, cy = w / 2, h / 2
    local maxDist = math.sqrt(cx * cx + cy * cy)

    for y = 0, h - 1 do
        for x = 0, w - 1 do
            local dist = math.sqrt((x - cx)^2 + (y - cy)^2)
            local ringIdx = math.floor(dist / maxDist * #colors) + 1
            if ringIdx > #colors then ringIdx = #colors end
            img:drawPixel(x, y, colors[ringIdx])
        end
    end
end

-- ========== 调色板定义 ==========
local palettes = {
    retro = {
        Color(26, 28, 44), Color(93, 39, 93), Color(177, 62, 83),
        Color(239, 125, 87), Color(255, 205, 117), Color(167, 240, 112),
        Color(56, 183, 100), Color(37, 113, 121), Color(41, 54, 111),
        Color(59, 93, 201), Color(65, 166, 246), Color(115, 239, 247),
        Color(244, 244, 244), Color(148, 176, 194), Color(86, 108, 134),
        Color(51, 60, 87)
    },
    earth = {
        Color(45, 30, 20), Color(74, 50, 35), Color(102, 75, 50),
        Color(140, 100, 70), Color(180, 140, 100), Color(220, 190, 150),
        Color(240, 220, 190), Color(255, 245, 220)
    },
    neon = {
        Color(10, 10, 30), Color(255, 0, 128), Color(0, 255, 200),
        Color(255, 255, 0), Color(128, 0, 255), Color(0, 128, 255)
    }
}

-- ========== 对话框 ==========
local dlg = Dialog("像素艺术生成器")

dlg:combobox{id="mode", label="生成模式",
    options={"随机噪点", "对称图案", "细胞自动机", "渐变", "同心圆"},
    option="对称图案"}
dlg:combobox{id="palette", label="调色板",
    options={"retro", "earth", "neon"},
    option="retro"}
dlg:number{id="width", label="宽度", text="32"}
dlg:number{id="height", label="高度", text="32"}
dlg:slider{id="param", label="参数", min=1, max=100, value=45}
dlg:separator{}
dlg:button{text="生成", onclick=function()
    local data = dlg.data

    -- 创建精灵
    local w = math.floor(data.width)
    local h = math.floor(data.height)
    local genSprite = Sprite(w, h, ColorMode.RGB)
    local img = genSprite.cels[1].image

    local colors = palettes[data.palette]
    math.randomseed(os.time())

    app.transaction(function()
        if data.mode == "随机噪点" then
            generateNoise(img, w, h, colors)
        elseif data.mode == "对称图案" then
            generateSymmetric(img, w, h, colors)
        elseif data.mode == "细胞自动机" then
            generateCellular(img, w, h,
                colors[1], colors[#colors],
                data.param / 100, 5)
        elseif data.mode == "渐变" then
            generateGradient(img, w, h,
                colors[1], colors[#colors], "diagonal")
        elseif data.mode == "同心圆" then
            generateRings(img, w, h, colors)
        end
    end)

    app.alert("已生成: " .. data.mode)
end}
dlg:button{text="关闭", onclick=function() dlg:close() end}

dlg:show()
```

**生成效果说明**

1. **随机噪点**：每个像素随机选择调色板中的颜色，产生电视雪花效果。
2. **对称图案**：4 象限对称，产生类似地毯、曼陀罗的装饰图案。
3. **细胞自动机**：基于 Conway 生命游戏的变体，生成洞穴、地牢等有机地形。
4. **渐变**：线性渐变，适合背景和天空。
5. **同心圆**：从中心向外的环形图案，适合靶心、波纹效果。

### 总结

- 程序化生成通过算法创建图像，支持噪点、对称、细胞自动机、渐变、同心圆等模式。
- 对称生成只计算一半像素，镜像复制提高效率。
- 细胞自动机通过迭代规则演化出有机图案。
- 调色板映射将生成数据转换为可视颜色。
- **注意事项**：使用 `math.randomseed` 初始化随机种子；生成操作放在事务中；大尺寸生成可能较慢，可添加进度提示。

---

## 第 24 讲：实战：动画批处理流水线

### 概念

动画批处理流水线是将多个动画文件按照统一标准进行处理和导出的自动化系统。本实战项目构建一个完整的流水线，能够批量处理动画文件：统一尺寸、优化调色板、添加标签、导出多种格式（GIF、序列帧、图集），并生成处理报告。这是游戏开发中资产管线的典型应用。

### 原理

动画批处理流水线的阶段：
1. **输入扫描**：递归扫描目录，收集所有 .aseprite 文件。
2. **预处理**：统一尺寸（裁剪或填充）、修剪透明边缘。
3. **优化**：减少颜色数、优化调色板。
4. **标签管理**：根据帧范围自动创建标签（如 idle、walk、run）。
5. **多格式导出**：
   - GIF：用于预览动画。
   - 序列帧 PNG：用于引擎导入。
   - 精灵图集：用于批量打包。
   - JSON 元数据：记录动画信息。
6. **报告生成**：统计处理结果，输出日志文件。

流水线的设计原则：
- **幂等性**：多次运行产生相同结果。
- **增量处理**：只处理修改过的文件（通过时间戳判断）。
- **错误恢复**：单个文件失败不中断整个流水线。
- **可配置**：通过参数或配置文件控制行为。

### 例子

**完整项目代码**

```lua
-- animation_pipeline.lua
-- 动画批处理流水线
-- 用法: aseprite -b --script animation_pipeline.lua --script-param config=pipeline_config.lua

local lfs = require("lfs")

-- ========== 配置 ==========
local defaultConfig = {
    inputDir = "./animations",
    outputDir = "./processed",
    targetSize = {w = 32, h = 32},
    trim = true,
    optimizeColors = true,
    maxColors = 16,
    exportGif = true,
    exportSequence = true,
    exportSheet = true,
    sheetColumns = 4,
    incremental = true,
    reportFile = "pipeline_report.txt"
}

-- 加载用户配置
local configFile = app.params["config"]
if configFile then
    local fn, err = loadfile(configFile)
    if fn then
        local userConfig = fn()
        if type(userConfig) == "table" then
            for k, v in pairs(userConfig) do
                defaultConfig[k] = v
            end
        end
    end
end

local config = defaultConfig

-- ========== 工具函数 ==========
local function ensureDir(path)
    os.execute('mkdir -p "' .. path .. '"')
end

local function fileExists(path)
    local f = io.open(path, "r")
    if f then f:close() return true end
    return false
end

local function isNewer(src, dst)
    if not fileExists(dst) then return true end
    local srcAttr = lfs.attributes(src)
    local dstAttr = lfs.attributes(dst)
    return srcAttr.modification > dstAttr.modification
end

-- 递归扫描目录
local function scanDir(path, pattern, results)
    results = results or {}
    for file in lfs.dir(path) do
        if file ~= "." and file ~= ".." then
            local fullPath = path .. "/" .. file
            local attr = lfs.attributes(fullPath)
            if attr.mode == "directory" then
                scanDir(fullPath, pattern, results)
            elseif attr.mode == "file" and file:match(pattern) then
                results[#results + 1] = fullPath
            end
        end
    end
    return results
end

-- ========== 处理阶段 ==========

-- 阶段 1：预处理（修剪和统一尺寸）
local function preprocess(sprite, config)
    if config.trim then
        app.command.Trim()
    end

    -- 如果尺寸不匹配，调整画布
    if sprite.width ~= config.targetSize.w or sprite.height ~= config.targetSize.h then
        local offsetX = math.floor((config.targetSize.w - sprite.width) / 2)
        local offsetY = math.floor((config.targetSize.h - sprite.height) / 2)
        app.command.CanvasSize{
            ui = false,
            width = config.targetSize.w,
            height = config.targetSize.h,
            bounds = Rectangle(offsetX, offsetY, config.targetSize.w, config.targetSize.h)
        }
    end
end

-- 阶段 2：优化调色板
local function optimizePalette(sprite, config)
    if not config.optimizeColors then return end

    -- 转换为索引模式（如果还不是）
    if sprite.colorMode ~= ColorMode.INDEXED then
        app.command.ColorMode{ui=false, mode="indexed"}
    end

    -- 减少颜色数
    if #sprite.palettes[1] > config.maxColors then
        app.command.ColorQuantization{
            ui = false,
            maxColors = config.maxColors
        }
    end
end

-- 阶段 3：自动标签
local function autoTag(sprite)
    local frameCount = #sprite.frames
    if frameCount < 4 then return end

    -- 根据帧数自动划分标签
    local third = math.floor(frameCount / 3)

    if frameCount >= 6 then
        local idleTag = sprite:newTag(sprite.frames[1], sprite.frames[third])
        idleTag.name = "idle"

        local walkTag = sprite:newTag(sprite.frames[third + 1], sprite.frames[third * 2])
        walkTag.name = "walk"

        local runTag = sprite:newTag(sprite.frames[third * 2 + 1], sprite.frames[frameCount])
        runTag.name = "run"
    end
end

-- 阶段 4：多格式导出
local function exportFormats(sprite, basename, outputDir, config)
    local results = {}

    -- 导出 GIF
    if config.exportGif then
        local gifPath = outputDir .. "/" .. basename .. ".gif"
        sprite:saveCopyAs(gifPath)
        results.gif = gifPath
    end

    -- 导出序列帧
    if config.exportSequence then
        local seqDir = outputDir .. "/" .. basename .. "_frames"
        ensureDir(seqDir)
        for i, frame in ipairs(sprite.frames) do
            app.activeFrame = frame
            local framePath = string.format("%s/%s_%03d.png", seqDir, basename, i - 1)
            sprite:saveCopyAs(framePath)
        end
        results.sequence = seqDir
    end

    -- 导出图集
    if config.exportSheet then
        local sheetPath = outputDir .. "/" .. basename .. "_sheet.png"
        local dataPath = outputDir .. "/" .. basename .. "_sheet.json"
        app.command.ExportSpriteSheet{
            ui = false,
            type = SpriteSheetType.ROWS,
            columns = config.sheetColumns,
            textureFilename = sheetPath,
            dataFilename = dataPath,
            dataFormat = "json-array"
        }
        results.sheet = sheetPath
        results.sheetData = dataPath
    end

    return results
end

-- ========== 主流水线 ==========

local function processFile(filePath, config, report)
    local basename = filePath:match("([^/\\]+)%.aseprite$")
    local outputDir = config.outputDir .. "/" .. basename
    ensureDir(outputDir)

    report.total = report.total + 1

    -- 增量处理检查
    if config.incremental then
        local gifPath = outputDir .. "/" .. basename .. ".gif"
        if not isNewer(filePath, gifPath) then
            report.skipped = report.skipped + 1
            print("[跳过] " .. basename .. " (未修改)")
            return
        end
    end

    print(string.format("\n[处理] %s", basename))

    local ok, err = pcall(function()
        local sprite = app.open(filePath)
        if not sprite then
            error("无法打开文件")
        end

        local originalSize = {w = sprite.width, h = sprite.height}
        local originalFrames = #sprite.frames

        -- 执行各阶段
        app.transaction(function()
            preprocess(sprite, config)
            optimizePalette(sprite, config)
            autoTag(sprite)
        end)

        -- 保存处理后的文件
        local processedPath = outputDir .. "/" .. basename .. ".aseprite"
        sprite:saveAs(processedPath)

        -- 导出
        local exports = exportFormats(sprite, basename, outputDir, config)

        sprite:close()

        -- 记录到报告
        report.processed = report.processed + 1
        report.entries[#report.entries + 1] = {
            name = basename,
            original = originalSize,
            final = {w = config.targetSize.w, h = config.targetSize.h},
            frames = originalFrames,
            exports = exports
        }

        print(string.format("  尺寸: %dx%d -> %dx%d",
            originalSize.w, originalSize.h, config.targetSize.w, config.targetSize.h))
        print(string.format("  帧数: %d", originalFrames))
        print(string.format("  导出: %s", table.concat({
            exports.gif and "GIF" or "",
            exports.sequence and "序列帧" or "",
            exports.sheet and "图集" or ""
        }, ", ")))
    end)

    if not ok then
        report.failed = report.failed + 1
        report.entries[#report.entries + 1] = {
            name = basename,
            error = tostring(err)
        }
        print("  [错误] " .. err)
    end

    -- 定期垃圾回收
    if report.total % 5 == 0 then
        collectgarbage("collect")
    end
end

-- ========== 生成报告 ==========

local function generateReport(report, config)
    local f = io.open(config.reportFile, "w")
    f:write("=== 动画批处理流水线报告 ===\n")
    f:write(string.format("时间: %s\n", os.date()))
    f:write(string.format("输入目录: %s\n", config.inputDir))
    f:write(string.format("输出目录: %s\n\n", config.outputDir))

    f:write(string.format("总文件数: %d\n", report.total))
    f:write(string.format("已处理: %d\n", report.processed))
    f:write(string.format("已跳过: %d\n", report.skipped))
    f:write(string.format("失败: %d\n\n", report.failed))

    f:write("=== 详细信息 ===\n")
    for i, entry in ipairs(report.entries) do
        f:write(string.format("\n[%d] %s\n", i, entry.name))
        if entry.error then
            f:write(string.format("  错误: %s\n", entry.error))
        else
            f:write(string.format("  尺寸: %dx%d -> %dx%d\n",
                entry.original.w, entry.original.h,
                entry.final.w, entry.final.h))
            f:write(string.format("  帧数: %d\n", entry.frames))
            if entry.exports.gif then
                f:write(string.format("  GIF: %s\n", entry.exports.gif))
            end
            if entry.exports.sequence then
                f:write(string.format("  序列帧: %s\n", entry.exports.sequence))
            end
            if entry.exports.sheet then
                f:write(string.format("  图集: %s\n", entry.exports.sheet))
            end
        end
    end

    f:close()
    print(string.format("\n报告已生成: %s", config.reportFile))
end

-- ========== 执行流水线 ==========

print("=== 动画批处理流水线启动 ===")
print(string.format("输入: %s", config.inputDir))
print(string.format("输出: %s", config.outputDir))

ensureDir(config.outputDir)

local files = scanDir(config.inputDir, "%.aseprite$")
print(string.format("找到 %d 个文件\n", #files))

local report = {
    total = 0,
    processed = 0,
    skipped = 0,
    failed = 0,
    entries = {}
}

for i, file in ipairs(files) do
    print(string.format("[%d/%d]", i, #files))
    processFile(file, config, report)
end

generateReport(report, config)

print(string.format("\n=== 流水线完成 ==="))
print(string.format("处理: %d, 跳过: %d, 失败: %d",
    report.processed, report.skipped, report.failed))
```

**配置文件示例（pipeline_config.lua）**

```lua
-- pipeline_config.lua
-- 流水线配置文件

return {
    inputDir = "./raw_animations",
    outputDir = "./processed_animations",
    targetSize = {w = 64, h = 64},
    trim = true,
    optimizeColors = true,
    maxColors = 32,
    exportGif = true,
    exportSequence = true,
    exportSheet = true,
    sheetColumns = 8,
    incremental = true,
    reportFile = "pipeline_report.txt"
}
```

**运行方式**

```bash
# 使用默认配置
aseprite -b --script animation_pipeline.lua

# 使用自定义配置
aseprite -b --script animation_pipeline.lua --script-param config=my_config.lua

# 首次运行（处理所有文件）
aseprite -b --script animation_pipeline.lua --script-param config=config_full.lua

# 增量运行（只处理修改过的文件）
aseprite -b --script animation_pipeline.lua --script-param config=config_incremental.lua
```

**输出目录结构**

```
processed_animations/
├── character/
│   ├── character.aseprite       # 处理后的源文件
│   ├── character.gif            # GIF 预览
│   ├── character_frames/        # 序列帧目录
│   │   ├── character_000.png
│   │   ├── character_001.png
│   │   └── ...
│   ├── character_sheet.png      # 精灵图集
│   └── character_sheet.json     # 图集元数据
├── enemy/
│   └── ...
└── pipeline_report.txt          # 处理报告
```

### 总结

- 动画批处理流水线包含：扫描、预处理、优化、标签、导出、报告六个阶段。
- 增量处理通过比较文件修改时间避免重复工作。
- 多格式导出满足不同用途：GIF 预览、序列帧引擎导入、图集批量打包。
- 配置文件使流水线可复用、可定制。
- **注意事项**：使用 `pcall` 处理单个文件错误；定期垃圾回收防止内存泄漏；报告文件记录详细信息便于排查问题；配置文件应版本控制以保证可重复性。

---

## 课程总结

恭喜您完成了 Aseprite 命令行与 Lua 自动化完整教程！让我们回顾整个学习旅程：

### 知识体系回顾

| 章节 | 核心内容 | 关键知识点 |
|------|---------|-----------|
| 第一章 | 入门基础 | 环境配置、CLI 基础、Lua 脚本运行 |
| 第二章 | 对象模型 | app、Sprite、Image、Cel、Layer |
| 第三章 | 命令行深度 | 导出转换、批处理、脚本协同 |
| 第四章 | Lua API 核心 | 颜色调色板、选区变换、绘图工具、帧动画 |
| 第五章 | 高级自动化 | 文件遍历、图像分析、切片管理 |
| 第六章 | 插件开发 | package.json、Dialog GUI、命令注册 |
| 第七章 | 实战项目 | 精灵图集、程序化生成、动画流水线 |

### 核心能力总结

1. **命令行能力**：格式转换、批量导出、精灵图集生成、脚本协同。
2. **Lua 编程能力**：对象模型操作、像素级图像处理、动画时间轴控制。
3. **自动化能力**：文件遍历、批处理、图像分析、增量处理。
4. **插件开发能力**：GUI 对话框、菜单集成、命令注册、快捷键绑定。
5. **工程化能力**：配置管理、错误处理、日志报告、流水线设计。

### 进阶学习建议

- **Aseprite 官方 API 文档**：https://github.com/aseprite/api 阅读完整的 API 参考。
- **开源插件研究**：研究 GitHub 上的 Aseprite 插件源码，学习最佳实践。
- **Lua 语言深入**：学习 Lua 5.1 的高级特性（闭包、元表、协程），提升脚本质量。
- **游戏引擎集成**：学习如何将 Aseprite 导出结果集成到 Unity、Godot、GameMaker 等引擎。
- **CI/CD 集成**：将 Aseprite 命令行集成到 GitHub Actions、GitLab CI 等流水线，实现资产自动化处理。

### 工作流建议

1. **日常创作**：使用 GUI + 简单脚本（如颜色统计、边界框计算）辅助创作。
2. **批量处理**：使用命令行 + Lua 脚本处理大量文件（如格式转换、尺寸统一）。
3. **团队协作**：开发插件统一团队的工作流（如命名规范、导出标准）。
4. **资产管线**：构建完整的 CI/CD 流水线，从源文件到引擎可用资产的自动化转换。

Aseprite 的自动化能力远超表面所见。通过本教程的学习，您已经掌握了从简单脚本到完整插件、从单文件处理到批量流水线的全面能力。希望这些知识能帮助您提升像素艺术创作的效率和质量！

---

*本教程基于 Aseprite 1.3+ 和 Lua 5.1 API 编写。*
