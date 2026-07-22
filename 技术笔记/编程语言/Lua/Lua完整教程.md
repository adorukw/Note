# Lua 完整教程：从入门到实战

> 本教程以教科书形式系统讲解 Lua 语言，共 30 讲，分为 10 章。
> 每讲包含「概念」「原理」「例子」「总结」四个标准化部分，循序渐进，从基础语法到实战应用。

---

## 课程总览

- **预计讲数**：30 讲
- **章节划分**：10 章，每章 2-4 讲
- **学习目标**：
  1. 掌握 Lua 的基本语法、数据类型与控制结构
  2. 深入理解 Table（表）这一核心数据结构及其应用
  3. 掌握函数、闭包、高阶函数等函数式编程特性
  4. 理解元表与元方法，能够实现面向对象编程
  5. 掌握协程、错误处理等进阶特性
  6. 了解模块系统、弱引用、环境等高级主题
  7. 能够将 Lua 嵌入 C 程序，并在游戏/Web 等场景中实战应用

---

## 详细章节目录

### 第一章：Lua 入门基础
- 第 1 讲：Lua 简介与环境搭建
- 第 2 讲：第一个 Lua 程序与基本语法
- 第 3 讲：注释、关键字与代码风格

### 第二章：数据类型与变量
- 第 4 讲：Lua 的八大数据类型
- 第 5 讲：变量与作用域（local 与 global）
- 第 6 讲：运算符与表达式

### 第三章：控制结构
- 第 7 讲：条件判断（if / elseif / else）
- 第 8 讲：循环结构（while / repeat / for）
- 第 9 讲：break、return 与 goto

### 第四章：函数
- 第 10 讲：函数定义与多返回值
- 第 11 讲：可变参数函数
- 第 12 讲：闭包与高阶函数

### 第五章：表（Table）—— Lua 的核心数据结构
- 第 13 讲：表的基础与数组用法
- 第 14 讲：表作为字典 / 哈希表
- 第 15 讲：表的遍历（ipairs 与 pairs）
- 第 16 讲：表标准库与复杂数据结构

### 第六章：字符串
- 第 17 讲：字符串基础操作
- 第 18 讲：模式匹配（Pattern）

### 第七章：元表与面向对象
- 第 19 讲：元表与元方法概念
- 第 20 讲：算术与关系元方法
- 第 21 讲：__index 与 __newindex
- 第 22 讲：面向对象编程实现

### 第八章：协程与错误处理
- 第 23 讲：协程基础
- 第 24 讲：协程进阶应用
- 第 25 讲：错误处理机制（pcall / error / assert）

### 第九章：模块与进阶主题
- 第 26 讲：模块系统
- 第 27 讲：弱引用表
- 第 28 讲：环境（_ENV）与垃圾回收

### 第十章：实战应用
- 第 29 讲：Lua 与 C 的交互
- 第 30 讲：Lua 实战应用场景

---

# 第一章：Lua 入门基础

本章介绍 Lua 语言的历史背景、特点、应用领域，指导你搭建开发环境，并编写第一个 Lua 程序。我们将从最基础的概念开始，逐步建立对 Lua 语言的整体认识。

---

## 第 1 讲：Lua 简介与环境搭建

### 概念

Lua（葡萄牙语意为"月亮"）是一种轻量、小巧、可扩展的脚本语言，由巴西里约热内卢天主教大学（PUC-Rio）的 Roberto Ierusalimschy、Waldemar Celes 和 Luiz Henrique de Figueiredo 于 1993 年开发。Lua 的设计目标是：作为一种嵌入式语言，能够方便地嵌入到宿主应用程序中，为宿主程序提供灵活的脚本扩展能力。

Lua 的核心特点包括：**轻量级**（解释器源码仅约 2 万行 C 代码，编译后仅数百 KB）、**高效**（LuaJIT 性能可媲美原生 C）、**可嵌入**（提供简洁的 C API）、**跨平台**（支持几乎所有操作系统）、**垃圾回收**（自动内存管理）。Lua 广泛应用于游戏开发（如《魔兽世界》《愤怒的小鸟》、Roblox 平台）、嵌入式系统、Web 服务器（如 OpenResty/Nginx-Lua）、数据库扩展（如 Redis）等领域。

### 原理

Lua 采用**寄存器式虚拟机**（Register-based VM）架构，这与大多数采用栈式虚拟机的脚本语言（如 Python 的字节码虚拟机）不同。寄存器式虚拟机的优势在于：执行同样的逻辑所需的指令条数更少，因为大部分操作可以直接在寄存器之间完成，而不需要频繁的 push/pop 操作。这是 Lua 性能优异的重要原因之一。

Lua 的设计哲学是**"提供机制而非策略"**（Mechanisms, not policies）。Lua 不内置丰富的数据类型和库函数，而是提供少量但足够灵活的原语（如 Table、元表、协程），让开发者自行构建所需的高级抽象。例如，Lua 没有内置的"类"和"继承"概念，但通过 Table 和元表可以轻松实现面向对象编程。这种哲学使 Lua 核心保持精简，同时具备极强的表达力。

### 例子

**安装 Lua（Linux/macOS）：**

```bash
# Ubuntu / Debian
sudo apt-get install lua5.4

# macOS (Homebrew)
brew install lua

# 验证安装
lua -v
# 输出: Lua 5.4.x  Copyright (C) 1994-2024 Lua.org, PUC-Rio
```

**安装 LuaJIT（高性能版本）：**

```bash
# Ubuntu
sudo apt-get install luajit

# 验证
luajit -v
# 输出: LuaJIT 2.1.x
```

**交互式运行（REPL）：**

```bash
$ lua
Lua 5.4.6  Copyright (C) 1994-2024 Lua.org, PUC-Rio
> print("Hello, Lua!")
Hello, Lua!
> print(_VERSION)
Lua 5.4
> -- 按 Ctrl+D 退出
```

**脚本文件运行：**

创建文件 `hello.lua`：
```lua
print("Hello, World!")
print("Lua version:", _VERSION)
```

执行：
```bash
lua hello.lua
# 输出:
# Hello, World!
# Lua version:    Lua 5.4
```

### 总结

- Lua 是一种轻量、高效、可嵌入的脚本语言，由 C 语言实现，核心极其精简。
- Lua 采用寄存器式虚拟机，性能优于许多同类脚本语言；LuaJIT 进一步将性能提升至接近原生 C 的水平。
- Lua 广泛应用于游戏开发、Web 服务器、嵌入式系统等领域。
- 安装 Lua 后可通过 `lua` 命令进入交互模式或执行 `.lua` 脚本文件。
- **注意事项**：Lua 5.1 / 5.2 / 5.3 / 5.4 之间存在一些不兼容的改动（如 5.3 引入整数类型、5.2 引入 `_ENV`）。学习时请注意所用版本，本教程以 Lua 5.4 为主。

---

## 第 2 讲：第一个 Lua 程序与基本语法

### 概念

本讲通过编写第一个完整的 Lua 程序，介绍 Lua 的基本语法要素：语句结构、`print` 函数、字符串字面量、表达式等。Lua 的语法风格介于 Pascal 和 Python 之间，不使用大括号 `{}` 划分代码块，也不依赖缩进，而是使用关键字（如 `end`、`then`、`do`）来界定代码块。

### 原理

Lua 语句的执行方式是**自上而下逐条执行**的。每条语句可以以分号 `;` 结尾，但分号是**可选的**——Lua 将分号视为空语句，不强制要求。Lua 是**动态类型语言**，变量本身没有类型，只有值有类型；同一个变量可以在运行时持有不同类型的值。

Lua 的代码块通过关键字界定，例如 `if ... then ... end`、`function ... end`、`while ... do ... end`。这种设计避免了"悬挂 else"和大括号匹配等常见问题，使代码结构清晰。Lua 不支持 Python 式的缩进语法，缩进纯粹是代码风格，不影响语义。

### 例子

**示例 1：基本输出**

```lua
-- 这是单行注释
print("Hello, Lua!")           -- 输出字符串
print(42)                       -- 输出数字
print(3.14)                     -- 输出浮点数
print(true)                     -- 输出布尔值
print(nil)                      -- 输出 nil
```

运行结果：
```
Hello, Lua!
42
3.14
true
nil
```

**示例 2：变量赋值与多赋值**

```lua
-- 单个赋值
local name = "Alice"

-- 多重赋值
local x, y, z = 1, 2, 3
print(name, x, y, z)  -- 输出: Alice    1    2    3

-- 交换变量值
x, y = y, x
print(x, y)  -- 输出: 2    1
```

**示例 3：一个完整的小程序**

```lua
-- 计算圆的面积
local pi = 3.14159
local radius = 5
local area = pi * radius * radius

print("半径:", radius)
print("面积:", area)
```

运行结果：
```
半径:  5
面积:  78.53975
```

### 总结

- Lua 语句末尾的分号可选，通常省略不写。
- Lua 是动态类型语言，变量无类型，值有类型。
- 代码块通过 `end`、`then`、`do` 等关键字界定，不使用大括号，也不依赖缩进。
- `print` 是最常用的输出函数，支持多参数输出（参数间用制表符分隔）。
- **注意事项**：Lua 中多条语句可以写在同一行（用空格或分号分隔），但为了可读性，建议每行一条语句。

---

## 第 3 讲：注释、关键字与代码风格

### 概念

注释是代码中不被解释器执行的部分，用于说明代码逻辑。Lua 支持单行注释和多行注释。关键字（也称保留字）是 Lua 语言保留的标识符，不能用作变量名或表字段名。良好的代码风格能显著提升代码的可读性和可维护性。

### 原理

Lua 的注释机制简单直接：单行注释以 `--` 开头，直到行尾；多行注释以 `--[[` 开头，以 `]]` 结尾。Lua 5.1 还支持长括号注释 `--[==[ ... ]==]`，其中等号数量可变，用于处理注释内容中包含 `]]` 的情况。

Lua 共有 22 个关键字，涵盖控制流（`if`、`then`、`else`、`elseif`、`end`、`while`、`repeat`、`until`、`for`、`do`、`break`、`return`、`goto`）、声明（`local`、`function`）、逻辑值（`true`、`false`、`nil`）、逻辑运算（`and`、`or`、`not`）、循环变量（`in`）等。这些关键字构成了 Lua 语法的骨架。

### 例子

**示例 1：注释的用法**

```lua
-- 这是单行注释

--[[
这是多行注释
可以跨越多行
]]

-- 推荐的多行注释写法（便于取消注释）
--[[
print("这行被注释了")
print("这行也被注释了")
--]]

-- 只需在开头加一个 - 即可取消注释：
---[[
print("这行会执行")
--]]
```

**示例 2：长括号注释（处理嵌套场景）**

```lua
--[==[
    这里的 ]] 不会结束注释
    因为使用的是 ]==] 作为结束标记
]==]
print("注释结束，正常执行")
```

**示例 3：Lua 关键字一览**

```lua
-- 以下都是 Lua 关键字，不能用作变量名：
-- and    break   do      else    elseif
-- end    false   for     function goto
-- if     in      local   nil     not
-- or     repeat  return  then    true
-- until  while

-- 错误示范（会报语法错误）：
-- local function = 10   -- 'function' 是关键字
-- local if = 20         -- 'if' 是关键字

-- 正确示范：
local func = 10
local condition = 20
print(func, condition)
```

**示例 4：推荐的代码风格**

```lua
-- 1. 使用 local 声明局部变量
local count = 0

-- 2. 变量名使用小写字母，常量可用全大写
local MAX_SIZE = 100
local userName = "Bob"

-- 3. 函数名使用小写或驼峰
local function calculateArea(width, height)
    return width * height
end

-- 4. 运算符两侧加空格
local result = a + b * c

-- 5. 缩进使用 4 个空格（社区惯例）
if x > 0 then
    print("正数")
end
```

### 总结

- Lua 支持单行注释（`--`）和多行注释（`--[[ ... ]]`）。
- 推荐使用 `--[[ ... --]]` 格式的多行注释，便于通过添加/删除一个 `-` 来切换注释状态。
- Lua 共有 22 个关键字，不能用作标识符。
- 良好的代码风格包括：优先使用 `local`、合理的命名规范、适当的空格和缩进。
- **注意事项**：Lua 区分大小写，`Print` 和 `print` 是不同的标识符。`nil`、`true`、`false` 必须小写。

---

# 第二章：数据类型与变量

本章深入讲解 Lua 的八种数据类型、变量作用域规则以及各类运算符。理解数据类型是编写正确 Lua 程序的基础，而掌握作用域规则对于编写高效、无 bug 的代码至关重要。

---

## 第 4 讲：Lua 的八大数据类型

### 概念

Lua 是动态类型语言，共有八种基本数据类型：**nil**（空）、**boolean**（布尔）、**number**（数字）、**string**（字符串）、**function**（函数）、**table**（表）、**userdata**（用户数据）、**thread**（线程/协程）。每种类型都有其特定的用途和操作方式。可以使用 `type()` 函数获取任意值的类型名称。

### 原理

Lua 的类型系统设计极为简洁。在 Lua 5.3 之前，`number` 类型只有双精度浮点数一种表示；从 5.3 开始，`number` 内部区分为整数子类型和浮点数子类型，但对外仍统一表现为 `number` 类型。`nil` 类型只有一个值 `nil`，表示"没有值"的状态，与任何其他类型都不同。

`function` 在 Lua 中是**一等公民**（first-class value），可以像普通值一样赋值给变量、作为参数传递、作为返回值返回。`table` 是 Lua 中唯一的复合数据结构，既可以用作数组，也可以用作哈希表，还能用来实现对象、模块等高级抽象。`userdata` 用于将 C 语言的数据结构暴露给 Lua，通常在嵌入式中使用。`thread` 实际上指的是协程（coroutine），而非操作系统线程。

### 例子

**示例 1：查看各种值的类型**

```lua
print(type(nil))        -- nil
print(type(true))       -- boolean
print(type(42))         -- number
print(type(3.14))       -- number
print(type("hello"))    -- string
print(type(print))      -- function
print(type({}))         -- table
print(type(coroutine.create(function() end)))  -- thread
```

**示例 2：nil 的使用**

```lua
local x = nil           -- 显式赋值为 nil
local y                 -- 未赋值的局部变量，默认为 nil
print(x, y)             -- nil    nil

local z = 10
z = nil                 -- 将变量"清空"
print(z)                -- nil
print(type(z))          -- nil
```

**示例 3：number 的整数与浮点子类型（Lua 5.3+）**

```lua
local a = 10            -- 整数
local b = 10.0          -- 浮点数
local c = 3.14          -- 浮点数

print(type(a))          -- number
print(type(b))          -- number
print(math.type(a))     -- integer
print(math.type(b))     -- float
print(a == b)           -- true  （值相等）
```

**示例 4：boolean 的真假值**

```lua
-- 在 Lua 中，只有 nil 和 false 为"假"，其余所有值（包括 0 和空字符串）都为"真"
local a = 0
local b = ""
local c = {}

if a then print("0 为真") end       -- 会执行
if b then print("空字符串为真") end  -- 会执行
if c then print("空表为真") end      -- 会执行
```

### 总结

- Lua 有八种数据类型：nil、boolean、number、string、function、table、userdata、thread。
- `type()` 函数返回类型名称（字符串），可用于运行时类型检查。
- Lua 5.3+ 的 `number` 内部区分整数和浮点数，但 `type()` 统一返回 `"number"`，可用 `math.type()` 区分。
- **关键易错点**：在 Lua 中，`0` 和空字符串 `""` 都被视为"真"，这与 C/Python 等语言不同。只有 `nil` 和 `false` 为"假"。
- **注意事项**：`nil` 只能与 `nil` 比较（`nil == nil` 为 `true`），不能与数字 `0` 混淆。

---

## 第 5 讲：变量与作用域（local 与 global）

### 概念

Lua 中的变量分为**局部变量**（local variable）和**全局变量**（global variable）。局部变量通过 `local` 关键字声明，其作用域限于声明所在的代码块；全局变量无需声明，直接赋值即可创建，存储在全局环境表 `_G` 中。Lua 的作用域规则遵循**词法作用域**（lexical scoping），即变量的可见性由代码的静态结构决定。

### 原理

Lua 默认所有变量都是全局变量，除非使用 `local` 显式声明。这是 Lua 与许多语言（如 Python、Java 默认局部变量）的一个重要区别。局部变量存储在 Lua 虚拟机的**寄存器或栈**上，访问速度快；全局变量存储在全局表 `_G` 中，每次访问都需要进行哈希查找，速度较慢。

局部变量的作用域从声明处开始，到包含声明的最内层代码块结束。当内层作用域声明了与外层同名的局部变量时，内层变量会**遮蔽**（shadow）外层变量，但不会修改外层变量的值。词法作用域还支持**闭包**：内层函数可以访问定义时所在作用域中的局部变量，即使外层函数已经返回。

### 例子

**示例 1：局部变量与全局变量**

```lua
-- 全局变量（不使用 local）
greeting = "Hello"

-- 局部变量
local name = "World"

function sayHello()
    -- 函数内可以访问全局变量
    print(greeting .. ", " .. name .. "!")
end

sayHello()  -- Hello, World!

-- 全局变量存储在 _G 中
print(_G.greeting)  -- Hello
print(_G["name"])   -- nil （name 是局部变量，不在 _G 中）
```

**示例 2：作用域与遮蔽**

```lua
local x = 10

do
    local x = 20       -- 内层 x 遮蔽外层 x
    print(x)           -- 20
end

print(x)               -- 10 （外层 x 不受影响）
```

**示例 3：词法作用域与闭包**

```lua
local function makeCounter()
    local count = 0
    local function increment()
        count = count + 1    -- 访问外层作用域的 count
        return count
    end
    return increment
end

local counter = makeCounter()
print(counter())  -- 1
print(counter())  -- 2
print(counter())  -- 3
-- count 变量被闭包"捕获"，即使 makeCounter 已返回仍可访问
```

**示例 4：局部变量的性能优势**

```lua
-- 不推荐：循环中使用全局变量
local i = 1
while i <= 1000000 do
    i = i + 1
end

-- 推荐：使用局部变量
local j = 1
while j <= 1000000 do
    j = j + 1
end

-- 更推荐：将常用全局函数缓存为局部变量
local print = print    -- 缓存全局函数
local floor = math.floor
print(floor(3.7))      -- 3
```

### 总结

- Lua 默认变量为全局变量，必须用 `local` 声明局部变量。
- 局部变量存储在寄存器/栈上，访问速度远快于全局变量（全局变量需哈希查找 `_G` 表）。
- 作用域遵循词法作用域规则，内层同名变量会遮蔽外层变量。
- 闭包可以捕获并持有外层作用域的局部变量。
- **最佳实践**：始终优先使用 `local`，仅在确实需要全局共享时才使用全局变量。将频繁使用的全局函数（如 `print`、`math.floor`）缓存为局部变量可提升性能。
- **注意事项**：未声明的变量访问不会报错，而是返回 `nil`，这可能导致难以发现的 bug。

---

## 第 6 讲：运算符与表达式

### 概念

Lua 提供了丰富的运算符，包括：**算术运算符**（`+` `-` `*` `/` `%` `^` `//`）、**关系运算符**（`<` `>` `<=` `>=` `==` `~=`）、**逻辑运算符**（`and` `or` `not`）、**连接运算符**（`..`）、**长度运算符**（`#`）。表达式由运算符和操作数组成，用于计算和产生值。

### 原理

Lua 的算术运算有一些独特之处：除法 `/` 始终返回浮点数（即使两个操作数都是整数且能整除）；**地板除** `//`（Lua 5.3+）返回不大于商的最大整数；取模 `%` 的结果符号与除数相同（而非被除数），这与 C 语言的 `%` 不同。幂运算 `^` 始终返回浮点数。

Lua 的逻辑运算符 `and` 和 `or` 采用**短路求值**（short-circuit evaluation），且返回值不是布尔值，而是操作数本身。具体规则：`a and b`——若 `a` 为假则返回 `a`，否则返回 `b`；`a or b`——若 `a` 为真则返回 `a`，否则返回 `b`。这一特性常用于实现默认值和三元运算。Lua 不支持 C 风格的三元运算符 `?:`，但可以用 `a and b or c` 模拟（需注意 `b` 不能为假）。

### 例子

**示例 1：算术运算**

```lua
print(10 + 3)    -- 13
print(10 - 3)    -- 7
print(10 * 3)    -- 30
print(10 / 3)    -- 3.3333333333333 （始终返回浮点数）
print(10 // 3)   -- 3  （地板除，Lua 5.3+）
print(10 % 3)    -- 1  （取模）
print(2 ^ 10)    -- 1024.0 （幂运算，返回浮点数）
print(-10 % 3)   -- 2  （结果符号与除数相同）
print(-10 // 3)  -- -4 （向负无穷取整）
```

**示例 2：关系运算**

```lua
print(1 < 2)      -- true
print(1 > 2)      -- false
print(1 <= 1)     -- true
print(1 >= 2)     -- false
print(1 == 1)     -- true
print(1 ~= 2)     -- true  （~= 是"不等于"，而非 !=）
print("a" < "b")  -- true  （字符串按字典序比较）
print(1 == "1")   -- false （不同类型永远不相等）
```

**示例 3：逻辑运算与短路求值**

```lua
-- and: 第一个为假则返回第一个，否则返回第二个
print(nil and 1)    -- nil
print(false and 1)  -- false
print(2 and 3)      -- 3
print(0 and 1)      -- 1 （0 为真！）

-- or: 第一个为真则返回第一个，否则返回第二个
print(nil or 1)     -- 1
print(false or 1)   -- 1
print(2 or 3)       -- 2
print(nil or false) -- false

-- not: 返回布尔值
print(not nil)      -- true
print(not false)    -- true
print(not 0)        -- false （0 为真）
print(not "")       -- false （空字符串为真）
```

**示例 4：模拟三元运算与默认值**

```lua
-- 模拟三元运算: condition ? a : b  =>  condition and a or b
local status = "ok"
local message = status == "ok" and "成功" or "失败"
print(message)  -- 成功

-- 设置默认值
local function greet(name)
    name = name or "匿名用户"    -- 如果 name 为 nil，则使用默认值
    print("你好, " .. name)
end

greet()          -- 你好, 匿名用户
greet("Alice")   -- 你好, Alice
```

**示例 5：字符串连接与长度**

```lua
-- 字符串连接 ..
local s = "Hello" .. ", " .. "World"
print(s)  -- Hello, World

-- 长度运算符 #
print(#"hello")        -- 5 （字符串长度）
print(#"你好")         -- 6 （字节数，不是字符数！UTF-8 中每个中文字符占 3 字节）
print(#{1, 2, 3, 4})   -- 4 （数组长度）
```

### 总结

- 算术运算：`/` 始终返回浮点数，`//` 为地板除，`%` 结果符号与除数相同，`^` 返回浮点数。
- 关系运算：`~=` 表示"不等于"（不是 `!=`），不同类型的值永远不相等。
- 逻辑运算 `and`/`or` 采用短路求值，返回操作数本身而非布尔值。
- `a and b or c` 可模拟三元运算，但要求 `b` 不为假（`nil` 或 `false`）。
- `#` 运算符获取字符串的字节长度或序列的长度，注意 UTF-8 字符串的长度是字节数而非字符数。
- **注意事项**：避免对包含 `nil` 空洞的数组使用 `#`，结果不确定。

---

# 第三章：控制结构

控制结构是编程语言的核心，决定了程序的执行流程。Lua 提供了条件判断、循环和跳转三类控制结构。本章将系统讲解每种控制结构的语法、原理和使用场景。

---

## 第 7 讲：条件判断（if / elseif / else）

### 概念

条件判断语句根据条件的真假选择执行不同的代码分支。Lua 使用 `if`、`then`、`elseif`、`else`、`end` 关键字构成条件判断结构。Lua 没有 `switch` 语句，多重分支通过 `elseif` 实现。

### 原理

Lua 的条件判断遵循"真值测试"规则：只有 `nil` 和 `false` 为假，其他所有值（包括 `0`、空字符串、空表）都为真。条件表达式会被求值，然后进行真值测试。`if` 语句从上到下依次检查每个条件，一旦某个条件为真，就执行对应的代码块并跳过后续所有分支。如果所有条件都不满足，则执行 `else` 块（如果存在）。

`elseif` 是一个单独的关键字（不是 `else if`），用于避免多层嵌套的 `if` 语句，使代码更扁平、更易读。每个 `if` 结构只需一个 `end` 来结束。

### 例子

**示例 1：基本 if 语句**

```lua
local age = 18

if age >= 18 then
    print("成年人")
end
```

**示例 2：if-else 语句**

```lua
local score = 55

if score >= 60 then
    print("及格")
else
    print("不及格")
end
```

**示例 3：if-elseif-else 多分支**

```lua
local score = 85

if score >= 90 then
    print("优秀")
elseif score >= 80 then
    print("良好")
elseif score >= 70 then
    print("中等")
elseif score >= 60 then
    print("及格")
else
    print("不及格")
end
-- 输出: 良好
```

**示例 4：利用真值规则简化条件**

```lua
local name = nil

-- 利用 nil 为假的特性设置默认值
if not name then
    name = "匿名"
end
print(name)  -- 匿名

-- 更简洁的写法
name = name or "匿名"
```

**示例 5：嵌套条件判断**

```lua
local function classify(age, hasLicense)
    if age >= 18 then
        if hasLicense then
            return "可以驾驶"
        else
            return "成年但无驾照"
        end
    else
        return "未成年"
    end
end

print(classify(20, true))   -- 可以驾驶
print(classify(20, false))  -- 成年但无驾照
print(classify(15, false))  -- 未成年
```

### 总结

- Lua 的条件判断使用 `if ... then ... elseif ... else ... end` 结构。
- `elseif` 是一个关键字，不能写成 `else if`（后者会引入不必要的嵌套）。
- 真值规则：只有 `nil` 和 `false` 为假，`0` 和 `""` 为真。
- Lua 没有 `switch` 语句，多重分支用 `elseif` 或表查找实现。
- **注意事项**：条件表达式不需要括号，但加上括号也不会报错（括号只是分组表达式）。

---

## 第 8 讲：循环结构（while / repeat / for）

### 概念

循环结构用于重复执行一段代码。Lua 提供三种循环：`while` 循环（先判断后执行）、`repeat-until` 循环（先执行后判断）、`for` 循环（数值 for 和泛型 for）。每种循环有其适用场景。

### 原理

`while` 循环在每次迭代前检查条件，条件为真则执行循环体，为假则退出。`repeat-until` 循环先执行循环体，然后检查条件，条件为真时退出（注意：与 `while` 相反，`until` 的条件为真时**退出**循环）。`repeat-until` 中循环体至少执行一次。

数值 `for` 循环 `for i = start, stop, step do ... end` 会自动管理循环变量，从 `start` 开始，每次加 `step`（默认为 1），直到超过 `stop`。泛型 `for` 循环 `for k, v in pairs(t) do ... end` 通过迭代器函数遍历集合。`for` 循环的循环变量是局部变量，作用域仅限于循环体内，且在循环体内修改循环变量不会影响下一次迭代。

### 例子

**示例 1：while 循环**

```lua
local i = 1
while i <= 5 do
    print("第 " .. i .. " 次")
    i = i + 1
end
-- 输出 1 到 5
```

**示例 2：repeat-until 循环**

```lua
local count = 0
repeat
    count = count + 1
    print(count)
until count >= 3
-- 输出 1, 2, 3
-- 注意：until 条件为真时退出

-- repeat-until 至少执行一次
local x = 10
repeat
    print("执行了一次")
until x < 10  -- 即使一开始 x >= 10，也会执行一次
```

**示例 3：数值 for 循环**

```lua
-- 基本用法（步长默认为 1）
for i = 1, 5 do
    print(i)  -- 1, 2, 3, 4, 5
end

-- 指定步长
for i = 0, 10, 2 do
    print(i)  -- 0, 2, 4, 6, 8, 10
end

-- 倒序（步长为负）
for i = 5, 1, -1 do
    print(i)  -- 5, 4, 3, 2, 1
end

-- 浮点数步长
for i = 1, 2, 0.25 do
    print(i)  -- 1.0, 1.25, 1.5, 1.75, 2.0
end
```

**示例 4：泛型 for 循环**

```lua
-- 遍历数组
local fruits = {"apple", "banana", "cherry"}
for index, value in ipairs(fruits) do
    print(index, value)
end
-- 1    apple
-- 2    banana
-- 3    cherry

-- 遍历字典
local person = {name = "Alice", age = 30, city = "Beijing"}
for key, value in pairs(person) do
    print(key, value)
end
-- 顺序不确定（哈希表无序）
```

**示例 5：嵌套循环——九九乘法表**

```lua
for i = 1, 9 do
    local line = ""
    for j = 1, i do
        line = line .. string.format("%dx%d=%d\t", j, i, i * j)
    end
    print(line)
end
-- 输出九九乘法表
```

### 总结

- `while`：先判断后执行，条件为真时继续循环。
- `repeat-until`：先执行后判断，条件为真时退出（注意与 while 相反），至少执行一次。
- 数值 `for`：自动管理循环变量，适合已知次数的循环。
- 泛型 `for`：通过迭代器遍历集合，常配合 `ipairs`/`pairs` 使用。
- **注意事项**：`for` 循环变量是局部的，循环体内修改不影响下一次迭代；`repeat-until` 中 `until` 后的表达式可以访问循环体内声明的局部变量。

---

## 第 9 讲：break、return 与 goto

### 概念

除了正常的循环控制，Lua 还提供了三种跳转语句：`break`（跳出当前循环）、`return`（从函数返回）、`goto`（无条件跳转到标签）。这些语句用于在特定条件下改变控制流。

### 原理

`break` 语句会立即终止最内层的 `while`、`repeat` 或 `for` 循环，程序继续执行循环之后的语句。`return` 语句用于从函数中返回值并结束函数执行；`return` 必须是代码块的最后一条语句，如果需要在代码块中间使用 `return`，可以用 `do return end` 包裹。

`goto` 语句（Lua 5.2+）允许跳转到同一函数内的标签（`::label::`）。`goto` 有一些限制：不能跳入局部变量的作用域、不能跳入嵌套块、不能从外层跳入内层循环。`goto` 常用于模拟 `continue` 语句（Lua 没有内置 `continue`）或实现状态机。

### 例子

**示例 1：break 的使用**

```lua
-- 查找第一个大于 5 的数
local numbers = {1, 3, 5, 8, 2, 10}
for i, v in ipairs(numbers) do
    if v > 5 then
        print("找到: " .. v .. " (索引 " .. i .. ")")
        break
    end
end
-- 输出: 找到: 8 (索引 4)
```

**示例 2：return 的使用**

```lua
local function isEven(n)
    if n % 2 == 0 then
        return true
    end
    return false
end

print(isEven(4))  -- true
print(isEven(7))  -- false

-- 在代码块中间 return（需要 do ... end 包裹）
local function process(data)
    if not data then
        do return nil end  -- 提前返回
    end
    -- 正常处理
    return #data
end
```

**示例 3：用 goto 模拟 continue**

```lua
-- 打印 1-10 中的奇数
for i = 1, 10 do
    if i % 2 == 0 then
        goto continue  -- 跳过偶数
    end
    print(i)
    ::continue::
end
-- 输出: 1, 3, 5, 7, 9
```

**示例 4：用 goto 实现重试逻辑**

```lua
local function fetchData(url, maxRetries)
    local retries = 0
    ::retry::
    local success, result = pcall(function()
        -- 模拟网络请求
        if math.random() > 0.5 then
            return "数据: " .. url
        else
            error("请求失败")
        end
    end)

    if success then
        return result
    else
        retries = retries + 1
        if retries <= maxRetries then
            print("重试第 " .. retries .. " 次...")
            goto retry
        end
        return nil, "超过最大重试次数"
    end
end

math.randomseed(os.time())
print(fetchData("http://example.com", 3))
```

**示例 5：goto 的限制**

```lua
-- 错误：不能跳入局部变量作用域
-- goto skip
-- local x = 10
-- ::skip::
-- print(x)  -- x 未定义

-- 正确：标签在变量声明之前
::start::
local input = io.read()
if input ~= "quit" then
    print("你输入了: " .. input)
    goto start
end
```

### 总结

- `break`：终止最内层循环，继续执行循环后的代码。
- `return`：从函数返回值，必须是代码块的最后一条语句。
- `goto`：跳转到同函数内的标签，常用于模拟 `continue` 或实现状态机。
- Lua 没有内置 `continue`，可用 `goto` 模拟。
- **注意事项**：`goto` 不能跳入局部变量作用域、不能跳入嵌套块。过度使用 `goto` 会降低代码可读性，应谨慎使用。

---

# 第四章：函数

函数是组织代码的基本单元。Lua 的函数具有多返回值、可变参数、闭包等强大特性，支持函数式编程风格。本章将深入讲解 Lua 函数的各个方面。

---

## 第 10 讲：函数定义与多返回值

### 概念

函数是一段可重复执行的代码块，接收输入参数并返回结果。Lua 的函数是**一等公民**，可以赋值给变量、作为参数传递、作为返回值返回。Lua 函数支持**多返回值**，即一个函数可以返回多个值，调用者可以接收全部或部分返回值。

### 原理

Lua 函数定义的语法为 `function name(params) body end`，也可写作 `local name = function(params) body end`（匿名函数赋值）。函数参数列表中的参数是局部变量，调用时按位置匹配。如果调用时提供的参数多于形参，多余的参数被忽略；如果少于形参，不足的参数为 `nil`。

多返回值的机制：函数通过 `return a, b, c` 返回多个值。调用时，如果函数调用是表达式列表的最后一个元素，则所有返回值都会被使用；如果不是最后一个，则只有第一个返回值被保留（其余被截断）。这一规则适用于多重赋值、函数参数列表、表构造器和 `return` 语句。

### 例子

**示例 1：函数定义与调用**

```lua
-- 常规函数定义
local function add(a, b)
    return a + b
end

-- 等价的写法
local add = function(a, b)
    return a + b
end

print(add(3, 5))  -- 8

-- 参数不匹配
print(add(3))      -- 3 + nil => 报错：attempt to perform arithmetic on a nil value
print(add(3, 5, 7)) -- 8 （多余的参数被忽略）
```

**示例 2：多返回值**

```lua
-- 返回多个值
local function minMax(arr)
    local min, max = arr[1], arr[1]
    for i = 2, #arr do
        if arr[i] < min then min = arr[i] end
        if arr[i] > max then max = arr[i] end
    end
    return min, max
end

local arr = {3, 1, 4, 1, 5, 9, 2, 6}
local mn, mx = minMax(arr)
print(mn, mx)  -- 1    9

-- 只接收第一个返回值
local onlyMin = minMax(arr)
print(onlyMin)  -- 1
```

**示例 3：多返回值的截断规则**

```lua
local function multi()
    return 1, 2, 3
end

-- 函数调用是最后一个元素：保留所有返回值
local a, b, c = multi()
print(a, b, c)  -- 1    2    3

-- 函数调用不是最后一个元素：只保留第一个返回值
local x, y = multi(), 10
print(x, y)  -- 1    10

-- 在表构造器中
local t = {multi()}
print(#t)  -- 3

local t2 = {multi(), 10}
print(#t2)  -- 2 （只有 1 和 10）

-- 在函数参数中
local function printAll(...)
    print(...)
end
printAll(multi())     -- 1    2    3
printAll(multi(), 10) -- 1    10
```

**示例 4：用圆括号强制只取第一个返回值**

```lua
local function multi()
    return 1, 2, 3
end

print((multi()))  -- 1 （圆括号强制只取第一个返回值）
```

**示例 5：实际应用——字符串查找**

```lua
local s = "Hello, World"
local startIdx, endIdx = string.find(s, "World")
print(startIdx, endIdx)  -- 8    12

-- 只需要起始位置
local pos = string.find(s, "World")
print(pos)  -- 8
```

### 总结

- Lua 函数是一等公民，支持赋值、传递和返回。
- 函数参数按位置匹配，多余参数被忽略，不足参数为 `nil`。
- 多返回值：`return a, b, c` 可返回多个值。
- 多返回值截断规则：只有作为表达式列表最后一个元素时才保留所有返回值。
- 用圆括号 `(f())` 可强制只取第一个返回值。
- **注意事项**：多返回值的截断规则是 Lua 的常见陷阱，理解"最后一个元素"的语义非常重要。

---

## 第 11 讲：可变参数函数

### 概念

可变参数函数（variadic function）是指可以接受任意数量参数的函数。Lua 使用 `...` 语法表示可变参数。可变参数在函数内部表现为一个特殊的表达式，可以通过 `select` 函数或收集到表中来访问。

### 原理

在函数参数列表中使用 `...` 表示该函数接受可变数量的参数。在函数体内，`...` 是一个表达式，代表所有可变参数。`...` 不能直接作为表使用，但可以用 `{...}` 将所有参数收集到一个表中。

Lua 5.0+ 引入了 `select` 函数，用于操作可变参数：`select("#", ...)` 返回参数个数；`select(n, ...)` 返回从第 n 个开始的所有参数。`select` 不会创建表，因此比 `{...}` 更高效，尤其当只需要访问部分参数时。

### 例子

**示例 1：基本可变参数函数**

```lua
local function sum(...)
    local result = 0
    for _, v in ipairs({...}) do
        result = result + v
    end
    return result
end

print(sum(1, 2, 3))       -- 6
print(sum(1, 2, 3, 4, 5)) -- 15
print(sum())              -- 0
```

**示例 2：使用 select 获取参数个数和值**

```lua
local function printArgs(...)
    local count = select("#", ...)  -- 获取参数个数
    print("参数个数:", count)

    for i = 1, count do
        local arg = select(i, ...)  -- 获取第 i 个参数
        print("  参数 " .. i .. ":", arg)
    end
end

printArgs("a", "b", "c")
-- 参数个数: 3
--   参数 1: a
--   参数 2: b
--   参数 3: c
```

**示例 3：转发可变参数**

```lua
-- 将可变参数转发给另一个函数
local function log(level, ...)
    local msg = string.format(...)
    print(string.format("[%s] %s", level, msg))
end

log("INFO", "用户 %s 登录, ID: %d", "Alice", 1001)
-- [INFO] 用户 Alice 登录, ID: 1001
```

**示例 4：可变参数与固定参数混合**

```lua
local function formatString(separator, ...)
    local args = {...}
    local result = ""
    for i, v in ipairs(args) do
        if i > 1 then result = result .. separator end
        result = result .. tostring(v)
    end
    return result
end

print(formatString(", ", "apple", "banana", "cherry"))
-- apple, banana, cherry

print(formatString(" | ", 1, 2, 3, 4, 5))
-- 1 | 2 | 3 | 4 | 5
```

**示例 5：处理含 nil 的可变参数**

```lua
-- {...} 在遇到 nil 时会停止 ipairs 遍历
-- 使用 select 可以正确处理 nil

local function printAll(...)
    local count = select("#", ...)
    for i = 1, count do
        local v = select(i, ...)
        print(i, v)
    end
end

printAll(1, nil, 3, nil, 5)
-- 1    1
-- 2    nil
-- 3    3
-- 4    nil
-- 5    5
```

### 总结

- `...` 表示可变参数，在函数体内是一个表达式而非表。
- `{...}` 将可变参数收集为表，但 `ipairs` 遇到 `nil` 会停止。
- `select("#", ...)` 获取参数个数，`select(n, ...)` 获取从第 n 个开始的参数。
- 处理含 `nil` 的可变参数时，必须使用 `select` 而非 `{...}` + `ipairs`。
- **注意事项**：Lua 5.1 中有 `arg` 表自动收集可变参数，但 5.2+ 移除了这一特性，应使用 `{...}` 或 `select`。

---

## 第 12 讲：闭包与高阶函数

### 概念

**闭包**（closure）是一个函数加上它所捕获的外层作用域中的变量。当一个内层函数引用了外层函数的局部变量时，即使外层函数已经返回，这些变量仍然存活，被内层函数"捕获"。**高阶函数**（higher-order function）是接受函数作为参数或返回函数的函数。闭包和高阶函数是函数式编程的基石。

### 原理

Lua 的闭包实现基于**词法作用域**和**Upvalue**机制。当一个局部变量被内层函数引用时，Lua 会将该变量作为内层函数的 **upvalue**（上值）保存。upvalue 不是变量的副本，而是对变量的引用——多个闭包可以共享同一个 upvalue，对 upvalue 的修改对所有共享闭包可见。

upvalue 的存储方式：当外层函数活跃时，upvalue 直接指向栈上的变量；当外层函数返回时，Lua 将变量从栈"提升"（close）到堆上，upvalue 转为指向堆上的副本。这一机制保证了闭包可以"逃逸"出定义它的作用域。

高阶函数利用函数是一等公民的特性，实现了强大的抽象能力。常见的高阶函数包括 `map`、`filter`、`reduce` 等，它们将"做什么"与"怎么做"分离，提高了代码的复用性。

### 例子

**示例 1：基本闭包**

```lua
local function makeAdder(n)
    return function(x)
        return x + n  -- n 是 upvalue
    end
end

local add5 = makeAdder(5)
local add10 = makeAdder(10)

print(add5(3))   -- 8
print(add10(3))  -- 13
-- add5 和 add10 各自捕获了不同的 n 值
```

**示例 2：共享 upvalue**

```lua
local function makeAccount(balance)
    local function deposit(amount)
        balance = balance + amount
        return balance
    end
    local function withdraw(amount)
        balance = balance - amount
        return balance
    end
    -- deposit 和 withdraw 共享同一个 balance
    return deposit, withdraw
end

local deposit, withdraw = makeAccount(100)
print(deposit(50))   -- 150
print(withdraw(30))  -- 120 （balance 被修改）
print(deposit(10))   -- 130
```

**示例 3：高阶函数——map / filter / reduce**

```lua
-- map: 对每个元素应用函数
local function map(arr, fn)
    local result = {}
    for i, v in ipairs(arr) do
        result[i] = fn(v)
    end
    return result
end

-- filter: 过滤满足条件的元素
local function filter(arr, fn)
    local result = {}
    for _, v in ipairs(arr) do
        if fn(v) then
            result[#result + 1] = v
        end
    end
    return result
end

-- reduce: 归约
local function reduce(arr, fn, init)
    local acc = init
    for _, v in ipairs(arr) do
        acc = fn(acc, v)
    end
    return acc
end

local nums = {1, 2, 3, 4, 5}

-- 每个元素平方
local squared = map(nums, function(x) return x * x end)
print(table.concat(squared, ", "))  -- 1, 4, 9, 16, 25

-- 过滤偶数
local evens = filter(nums, function(x) return x % 2 == 0 end)
print(table.concat(evens, ", "))  -- 2, 4

-- 求和
local sum = reduce(nums, function(a, b) return a + b end, 0)
print(sum)  -- 15
```

**示例 4：闭包实现迭代器**

```lua
-- 生成斐波那契数列的迭代器
local function fibIterator()
    local a, b = 0, 1
    return function()
        local result = a
        a, b = b, a + b
        return result
    end
end

local next = fibIterator()
for i = 1, 10 do
    print(next())
end
-- 0, 1, 1, 2, 3, 5, 8, 13, 21, 34
```

**示例 5：闭包实现私有状态**

```lua
local function createCounter()
    local count = 0  -- 私有变量，外部无法直接访问
    return {
        increment = function() count = count + 1; return count end,
        decrement = function() count = count - 1; return count end,
        getValue = function() return count end
    }
end

local c = createCounter()
c.increment()
c.increment()
c.increment()
c.decrement()
print(c.getValue())  -- 2
-- count 变量被完全封装，只能通过方法访问
```

### 总结

- 闭包 = 函数 + 捕获的外层变量（upvalue）。
- upvalue 是对变量的引用，多个闭包可以共享同一个 upvalue。
- 高阶函数接受函数作为参数或返回函数，是函数式编程的核心。
- 闭包常用于：创建私有状态、实现迭代器、回调函数、延迟执行等。
- **注意事项**：闭包会持有 upvalue 的引用，如果不注意可能导致内存泄漏（如闭包意外持有大对象）。

---

# 第五章：表（Table）—— Lua 的核心数据结构

Table 是 Lua 中唯一的复合数据结构，它既是数组、又是哈希表、还可以用来实现对象、模块等高级抽象。可以说，掌握 Table 就掌握了 Lua 的精髓。本章将系统讲解 Table 的各种用法。

---

## 第 13 讲：表的基础与数组用法

### 概念

Table 是 Lua 中唯一的数据结构，用于表示数组、字典、集合、对象等。Table 是一种**关联数组**（associative array），可以通过任意类型的键（除 `nil` 和 `NaN` 外）来存储和访问值。当使用从 1 开始的连续整数作为键时，Table 就表现为数组。

### 原理

Table 在 Lua 内部由两部分组成：**数组部分**（array part）和**哈希部分**（hash part）。Lua 会自动将 1 到 n 的连续整数键存储在数组部分以获得更好的性能，其他键存储在哈希部分。这种混合存储使 Table 在作为数组使用时具有接近 C 数组的性能，同时作为哈希表使用时也保持高效。

Table 是**引用类型**：赋值和传参传递的是引用而非副本。多个变量可以引用同一个 Table，通过任一变量修改 Table，其他变量也能看到变化。Table 的键可以是任意类型（除 `nil` 和 `NaN`），但通常使用字符串或整数。Lua 数组的索引**从 1 开始**，而非 0，这是 Lua 与许多语言的重要区别。

### 例子

**示例 1：创建数组**

```lua
-- 创建空表
local arr1 = {}

-- 创建数组（从 1 开始索引）
local arr2 = {10, 20, 30, 40, 50}
print(arr2[1])  -- 10
print(arr2[3])  -- 30

-- 显式指定索引
local arr3 = {[1]=100, [2]=200, [3]=300}
print(arr3[1])  -- 100

-- 混合写法
local arr4 = {"a", "b", [3]="c", "d"}
-- arr4[1]="a", arr4[2]="b", arr4[3]="c", arr4[4]="d"
print(arr4[4])  -- d
```

**示例 2：数组操作**

```lua
local fruits = {"apple", "banana", "cherry"}

-- 获取长度
print(#fruits)  -- 3

-- 修改元素
fruits[2] = "blueberry"
print(fruits[2])  -- blueberry

-- 添加元素（追加到末尾）
fruits[#fruits + 1] = "date"
print(fruits[4])  -- date

-- 使用 table.insert
table.insert(fruits, "elderberry")
print(fruits[5])  -- elderberry

-- 在指定位置插入
table.insert(fruits, 1, "avocado")
print(fruits[1])  -- avocado

-- 删除元素
local removed = table.remove(fruits, 1)  -- 删除并返回第一个元素
print(removed)  -- avocado
print(fruits[1])  -- apple
```

**示例 3：Table 是引用类型**

```lua
local a = {1, 2, 3}
local b = a          -- b 和 a 引用同一个表

b[1] = 100
print(a[1])          -- 100 （a 也被修改了）

-- 复制表（浅拷贝）
local function shallowCopy(t)
    local copy = {}
    for k, v in pairs(t) do
        copy[k] = v
    end
    return copy
end

local c = shallowCopy(a)
c[1] = 999
print(a[1])  -- 100 （a 不受影响）
print(c[1])  -- 999
```

**示例 4：二维数组**

```lua
-- 创建 3x3 矩阵
local matrix = {}
for i = 1, 3 do
    matrix[i] = {}
    for j = 1, 3 do
        matrix[i][j] = i * 10 + j
    end
end

-- 访问
print(matrix[2][3])  -- 23

-- 遍历
for i = 1, #matrix do
    for j = 1, #matrix[i] do
        io.write(matrix[i][j], " ")
    end
    io.write("\n")
end
-- 11 12 13
-- 21 22 23
-- 31 32 33
```

### 总结

- Table 是 Lua 唯一的复合数据结构，内部由数组部分和哈希部分组成。
- 数组索引从 1 开始，而非 0。
- Table 是引用类型，赋值和传参传递引用。
- `#` 运算符获取数组长度，但仅对无 `nil` 空洞的序列有效。
- `table.insert` 和 `table.remove` 提供便捷的数组操作。
- **注意事项**：`#` 对有 `nil` 空洞的表返回不确定的值，应避免创建有空洞的数组。

---

## 第 14 讲：表作为字典 / 哈希表

### 概念

当 Table 使用字符串或其他非整数类型的键时，它就表现为字典（也叫哈希表、映射）。字典存储键值对（key-value pairs），通过键快速查找对应的值。Lua 的字典是动态的，可以随时添加、修改和删除键值对。

### 原理

Lua Table 的哈希部分使用**开放寻址法**（open addressing）实现，所有元素存储在一个连续的数组中，冲突时通过探测序列寻找空位。这种实现方式对缓存友好，性能优异。Table 会根据元素数量自动扩容和 rehash，开发者无需关心容量管理。

Table 的键可以是任意类型（除 `nil` 和 `NaN`），包括函数、表等引用类型。但使用引用类型作为键时需要注意：Table 的相等性判断对于表类型是基于引用相等（identity），即两个不同的表即使内容相同也不相等。字符串键的语法糖 `t.key` 等价于 `t["key"]`，使代码更简洁。

### 例子

**示例 1：创建和访问字典**

```lua
-- 创建字典
local person = {
    name = "Alice",
    age = 30,
    city = "Beijing"
}

-- 两种访问方式
print(person.name)      -- Alice （语法糖）
print(person["name"])   -- Alice （标准方式）

-- 添加新字段
person.email = "alice@example.com"
person["phone"] = "1234567890"

-- 修改字段
person.age = 31

-- 删除字段（赋值为 nil）
person.city = nil

print(person.email)  -- alice@example.com
print(person.city)   -- nil （已删除）
```

**示例 2：使用各种类型的键**

```lua
local t = {}

-- 字符串键
t.name = "test"

-- 数字键
t[1] = "one"
t[100] = "hundred"

-- 布尔键
t[true] = "yes"
t[false] = "no"

-- 表作为键
local key = {}
t[key] = "table key"

-- 函数作为键
local fn = function() end
t[fn] = "function key"

print(t.name)    -- test
print(t[1])      -- one
print(t[true])   -- yes
print(t[key])    -- table key
print(t[fn])     -- function key

-- 注意：t.1 或 t.true 是语法错误，必须用 t[1] 或 t[true]
```

**示例 3：嵌套字典**

```lua
local company = {
    name = "TechCorp",
    employees = {
        {name = "Alice", age = 30, department = "Engineering"},
        {name = "Bob", age = 25, department = "Sales"},
        {name = "Charlie", age = 35, department = "Engineering"}
    },
    address = {
        street = "123 Main St",
        city = "Beijing",
        zip = "100000"
    }
}

-- 访问嵌套字段
print(company.name)                        -- TechCorp
print(company.employees[1].name)           -- Alice
print(company.address.city)                -- Beijing

-- 遍历员工
for i, emp in ipairs(company.employees) do
    print(string.format("%d. %s - %s", i, emp.name, emp.department))
end
```

**示例 4：字典的增删改查**

```lua
local cache = {}

-- 增
cache["user:1001"] = {name = "Alice", lastAccess = os.time()}
cache["user:1002"] = {name = "Bob", lastAccess = os.time()}

-- 查
local user = cache["user:1001"]
if user then
    print("找到用户:", user.name)
end

-- 改
cache["user:1001"].lastAccess = os.time()

-- 删
cache["user:1002"] = nil

-- 检查键是否存在（注意：值为 false 时不能用 if t[k] 判断）
cache["disabled"] = false
if cache["disabled"] == nil then
    print("键不存在")
else
    print("键存在，值为:", cache["disabled"])  -- 键存在，值为: false
end
```

### 总结

- Table 使用非整数键时表现为字典/哈希表。
- `t.key` 是 `t["key"]` 的语法糖，仅适用于合法标识符的字符串键。
- 键可以是任意类型（除 `nil` 和 `NaN`），包括表和函数。
- 删除键值对的方法是将值设为 `nil`。
- **注意事项**：判断键是否存在时，应使用 `t[k] == nil` 而非 `if not t[k]`，因为值为 `false` 时后者会误判。

---

## 第 15 讲：表的遍历（ipairs 与 pairs）

### 概念

遍历 Table 是最常见的操作之一。Lua 提供两个内置迭代器：`ipairs` 用于遍历数组部分（整数键 1, 2, 3...），`pairs` 用于遍历所有键值对。此外，Lua 5.3+ 还提供了 `table.move` 等操作。理解两者的区别对于正确遍历 Table 至关重要。

### 原理

`ipairs` 返回一个迭代器函数，每次调用返回索引和对应的值。它从索引 1 开始，递增索引，直到遇到第一个 `nil` 值时停止。因此，`ipairs` 只遍历"序列"部分——即从 1 开始无 `nil` 空洞的连续整数键。如果数组中间有 `nil`，`ipairs` 会在该处停止，后面的元素不会被遍历。

`pairs` 使用 Lua 内部的 `next` 函数遍历 Table 的所有键值对，包括数组部分和哈希部分。`pairs` 的遍历顺序是**不确定的**——不保证按插入顺序或键的排序顺序遍历。如果需要有序遍历，必须先将键收集到数组中排序，再按排序结果访问。

### 例子

**示例 1：ipairs 遍历数组**

```lua
local fruits = {"apple", "banana", "cherry", "date"}

for i, v in ipairs(fruits) do
    print(i, v)
end
-- 1    apple
-- 2    banana
-- 3    cherry
-- 4    date
```

**示例 2：ipairs 遇到 nil 停止**

```lua
local arr = {1, 2, nil, 4, 5}

for i, v in ipairs(arr) do
    print(i, v)
end
-- 1    1
-- 2    2
-- （遇到 nil 停止，4 和 5 不会被遍历）

print(#arr)  -- 2 或 5（不确定！）
```

**示例 3：pairs 遍历所有键值对**

```lua
local person = {name = "Alice", age = 30, city = "Beijing", [1] = "first"}

for k, v in pairs(person) do
    print(k, v)
end
-- 输出顺序不确定，可能为：
-- 1    first
-- name    Alice
-- age     30
-- city    Beijing
```

**示例 4：有序遍历字典**

```lua
local scores = {Alice = 90, Bob = 85, Charlie = 95, David = 88}

-- 收集键并排序
local keys = {}
for k in pairs(scores) do
    keys[#keys + 1] = k
end
table.sort(keys)

-- 按排序后的键遍历
for _, k in ipairs(keys) do
    print(k, scores[k])
end
-- Alice    90
-- Bob      85
-- Charlie  95
-- David    88
```

**示例 5：自定义迭代器**

```lua
-- 反向遍历数组的迭代器
local function ripairs(t)
    local i = #t + 1
    return function()
        i = i - 1
        if i >= 1 then
            return i, t[i]
        end
    end
end

local arr = {"a", "b", "c", "d"}
for i, v in ripairs(arr) do
    print(i, v)
end
-- 4    d
-- 3    c
-- 2    b
-- 1    a
```

### 总结

- `ipairs`：遍历数组部分（从 1 开始的连续整数键），遇到 `nil` 停止。
- `pairs`：遍历所有键值对，顺序不确定。
- 需要有序遍历时，先收集键到数组、排序、再按序访问。
- 自定义迭代器可以实现各种遍历模式。
- **注意事项**：不要依赖 `pairs` 的遍历顺序；避免在有 `nil` 空洞的表上使用 `ipairs` 和 `#`。

---

## 第 16 讲：表标准库与复杂数据结构

### 概念

Lua 提供了 `table` 标准库，包含一系列操作 Table 的函数：`table.insert`、`table.remove`、`table.concat`、`table.sort`、`table.unpack`（Lua 5.1 中为全局 `unpack`）等。利用这些函数和 Table 的灵活性，可以实现栈、队列、集合等常见数据结构。

### 原理

`table.insert(t, [pos,] value)` 在指定位置插入元素，默认追加到末尾；插入位置之后的元素自动后移。`table.remove(t, [pos])` 移除指定位置的元素并返回，默认移除最后一个；移除位置之后的元素自动前移。`table.concat(t, sep, i, j)` 将数组的字符串元素连接成一个字符串，比手动用 `..` 连接高效得多（因为避免了多次内存分配）。

`table.sort(t, [comp])` 对数组进行原地排序，默认升序；可提供比较函数实现自定义排序。排序算法是快速排序的变体，平均时间复杂度 O(n log n)。`table.unpack(t, i, j)`（Lua 5.2+）将数组展开为多个返回值，等价于 Lua 5.1 的全局 `unpack`。

### 例子

**示例 1：table.concat 高效拼接**

```lua
local parts = {"Hello", ", ", "World", "!"}

-- 低效方式（多次内存分配）
local s = ""
for _, v in ipairs(parts) do
    s = s .. v
end

-- 高效方式（一次性分配）
local s2 = table.concat(parts)
print(s2)  -- Hello, World!

-- 带分隔符
local nums = {1, 2, 3, 4, 5}
print(table.concat(nums, ", "))  -- 1, 2, 3, 4, 5

-- 指定范围
print(table.concat(nums, "-", 2, 4))  -- 2-3-4
```

**示例 2：table.sort 排序**

```lua
local nums = {5, 2, 8, 1, 9, 3}

-- 默认升序
table.sort(nums)
print(table.concat(nums, ", "))  -- 1, 2, 3, 5, 8, 9

-- 降序（自定义比较函数）
table.sort(nums, function(a, b) return a > b end)
print(table.concat(nums, ", "))  -- 9, 8, 5, 3, 2, 1

-- 按对象的某个字段排序
local people = {
    {name = "Alice", age = 30},
    {name = "Bob", age = 25},
    {name = "Charlie", age = 35}
}
table.sort(people, function(a, b) return a.age < b.age end)
for _, p in ipairs(people) do
    print(p.name, p.age)
end
-- Bob    25
-- Alice  30
-- Charlie 35
```

**示例 3：实现栈**

```lua
local function createStack()
    local stack = {}
    return {
        push = function(x) table.insert(stack, x) end,
        pop = function() return table.remove(stack) end,
        peek = function() return stack[#stack] end,
        size = function() return #stack end,
        isEmpty = function() return #stack == 0 end
    }
end

local s = createStack()
s.push(10)
s.push(20)
s.push(30)
print(s.peek())  -- 30
print(s.pop())   -- 30
print(s.pop())   -- 20
print(s.size())  -- 1
```

**示例 4：实现集合**

```lua
local function createSet()
    local set = {}
    return {
        add = function(x) set[x] = true end,
        remove = function(x) set[x] = nil end,
        contains = function(x) return set[x] == true end,
        toList = function()
            local list = {}
            for k in pairs(set) do
                list[#list + 1] = k
            end
            return list
        end
    }
end

local colors = createSet()
colors.add("red")
colors.add("green")
colors.add("blue")
colors.add("red")  -- 重复添加无效

print(colors.contains("red"))    -- true
print(colors.contains("yellow")) -- false

colors.remove("green")
print(colors.contains("green"))  -- false
```

**示例 5：table.unpack 展开**

```lua
local arr = {10, 20, 30}

-- 展开为多个值
print(table.unpack(arr))  -- 10    20    30

-- 指定范围
print(table.unpack(arr, 2, 3))  -- 20    30

-- 实际应用：将数组作为函数参数
local function sum(a, b, c)
    return a + b + c
end
local args = {1, 2, 3}
print(sum(table.unpack(args)))  -- 6
```

### 总结

- `table.concat`：高效拼接字符串数组，远优于手动 `..` 连接。
- `table.sort`：原地排序，支持自定义比较函数。
- `table.insert` / `table.remove`：方便的数组增删操作。
- `table.unpack`：将数组展开为多返回值。
- 利用 Table 可以实现栈、队列、集合等数据结构。
- **注意事项**：`table.sort` 是不稳定排序，如果需要稳定排序需自行实现。

---

# 第六章：字符串

Lua 的字符串是不可变的字节序列，支持丰富的操作和强大的模式匹配。本章讲解字符串的基本操作和 Lua 独特的模式匹配（Pattern）机制。

---

## 第 17 讲：字符串基础操作

### 概念

Lua 中的字符串是**不可变**（immutable）的字节序列。字符串一旦创建，其内容不能被修改——所有"修改"字符串的操作实际上都是创建新字符串。Lua 的字符串可以包含任意字节（包括 `\0`），不要求以 `\0` 结尾。Lua 提供了 `string` 标准库进行各种字符串操作。

### 原理

Lua 字符串内部使用**引用计数**和**字符串驻留**（interning）机制。相同内容的字符串字面量在内部只保存一份副本，比较时只需比较指针而非逐字节比较，效率极高。短字符串（<=40 字节）会被自动驻留；长字符串则不驻留，但仍通过引用共享。

字符串的不可变性意味着每次"修改"操作（如连接、替换）都会分配新的内存。在循环中频繁拼接字符串会导致 O(n²) 的时间复杂度，应使用 `table.concat` 代替。Lua 字符串不编码 Unicode——它只是字节序列。处理 UTF-8 字符串需要使用 Lua 5.3+ 的 `utf8` 库。

### 例子

**示例 1：字符串字面量**

```lua
-- 单引号和双引号等价
local s1 = "Hello"
local s2 = 'World'

-- 长括号字符串 [[...]]（不处理转义）
local s3 = [[
这是一个
多行字符串
包含 \n 不会被转义
]]

-- 长括号字符串 [==[...]==]（处理内容中的 ]]）
local s4 = [==[
字符串中包含 ]] 不会结束
]==]

print(s1, s2)
print(s3)
```

**示例 2：转义字符**

```lua
print("换行:\n第二行")
print("制表符:\t结束")
print("引号:\"Hello\"")
print("反斜杠:\\")
print("Unicode码点:\u{4e2d}\u{6587}")  -- 中文 （Lua 5.3+）
```

**示例 3：字符串长度与索引**

```lua
local s = "Hello"

-- 长度（字节数）
print(#s)            -- 5
print(string.len(s)) -- 5

-- 索引（从 1 开始）
print(s:sub(1, 3))   -- Hel （第1到第3个字节）
print(s:sub(2))      -- ello （从第2个字节到末尾）
print(s:sub(-2))     -- lo （最后2个字节）
print(s:sub(-3, -2)) -- llo （倒数第3到第2个字节）
```

**示例 4：大小写转换与反转**

```lua
local s = "Hello World"

print(string.upper(s))  -- HELLO WORLD
print(string.lower(s))  -- hello world
print(string.reverse(s))-- dlroW olleH

-- 注意：原始字符串不变
print(s)  -- Hello World
```

**示例 5：字符串格式化**

```lua
-- string.format 类似 C 的 printf
local name = "Alice"
local age = 30
local score = 95.5

print(string.format("姓名: %s, 年龄: %d", name, age))
-- 姓名: Alice, 年龄: 30

print(string.format("分数: %.2f", score))
-- 分数: 95.50

print(string.format("%05d", 42))
-- 00042

print(string.format("%-10s|", "left"))
-- left      |

print(string.format("十六进制: %x / %X", 255, 255))
-- 十六进制: ff / FF
```

**示例 6：字符串与数字转换**

```lua
-- 字符串转数字
print(tonumber("123"))      -- 123
print(tonumber("3.14"))     -- 3.14
print(tonumber("0xff"))     -- 255 （十六进制）
print(tonumber("abc"))      -- nil （转换失败）
print(tonumber("10", 2))    -- 2 （按二进制解析 "10"）

-- 数字转字符串
print(tostring(123))        -- "123"
print(tostring(3.14))       -- "3.14"
print(string.format("%d", 42))  -- "42"
```

### 总结

- Lua 字符串是不可变的字节序列，修改操作会创建新字符串。
- 字符串字面量支持单引号、双引号和长括号 `[[...]]` 三种形式。
- 字符串索引从 1 开始，支持负索引（从末尾算起）。
- `string.format` 提供 C 风格的格式化功能。
- `tonumber` / `tostring` 实现字符串与数字的互转。
- **注意事项**：`#` 返回字节数而非字符数；处理 UTF-8 需用 `utf8` 库。循环中拼接字符串应使用 `table.concat`。

---

## 第 18 讲：模式匹配（Pattern）

### 概念

Lua 使用自己的**模式匹配**（Pattern）系统，而非标准正则表达式（Regex）。Lua Pattern 功能比 Regex 简单，但足以应对大多数文本处理需求，且实现轻量高效。核心函数包括 `string.find`（查找）、`string.match`（匹配）、`string.gmatch`（全局匹配迭代器）、`string.gsub`（全局替换）。

### 原理

Lua Pattern 的语法与正则表达式相似但有重要区别。字符类用 `%` 前缀而非 `\`：`%a`（字母）、`%d`（数字）、`%s`（空白）、`%w`（字母数字）、`%p`（标点）等。大写形式表示补集：`%A`（非字母）、`%D`（非数字）等。`.` 匹配任意字符，`*` 匹配 0 次或多次（贪婪），`+` 匹配 1 次或多次，`-` 匹配 0 次或多次（非贪婪），`?` 匹配 0 或 1 次。

Lua Pattern 支持**捕获**（capture）：用圆括号 `()` 捕获匹配的子串。`string.match` 返回捕获的内容；如果没有捕获，返回整个匹配。`string.gsub` 的替换字符串中可以用 `%0`（整个匹配）、`%1`-`%9`（第 n 个捕获）引用捕获内容。Lua Pattern 不支持 `|`（或）、`{n,m}`（重复次数）、非捕获组 `(?:...)`、前瞻/后顾等高级特性。

### 例子

**示例 1：string.find 查找**

```lua
local s = "Hello, World! Hello, Lua!"

-- 查找子串，返回起止位置
local startIdx, endIdx = string.find(s, "World")
print(startIdx, endIdx)  -- 8    12

-- 查找不存在
print(string.find(s, "Python"))  -- nil

-- 使用模式：查找数字
local s2 = "价格是 100 元"
print(string.find(s2, "%d+"))  -- 7    9

-- 查找单词
local s3 = "hello world"
print(string.find(s3, "%w+"))  -- 1    5 （"hello"）

-- plain 参数：禁用模式匹配，纯文本查找
print(string.find("a.b.c", ".", 1, true))  -- 2    2 （找到字面的 "."）
```

**示例 2：string.match 匹配**

```lua
local s = "2024-01-15"

-- 匹配整个模式
print(string.match(s, "%d+-%d+-%d+"))  -- 2024-01-15

-- 使用捕获
local y, m, d = string.match(s, "(%d+)-(%d+)-(%d+)")
print(y, m, d)  -- 2024    01    15

-- 匹配邮箱（简化版）
local email = "user@example.com"
local user, domain = string.match(email, "(%w+)@(%w+%.%w+)")
print(user, domain)  -- user    example.com
```

**示例 3：string.gmatch 全局匹配**

```lua
local s = "apple=10, banana=20, cherry=30"

-- 遍历所有匹配
for key, value in string.gmatch(s, "(%w+)=(%d+)") do
    print(key, value)
end
-- apple    10
-- banana   20
-- cherry   30

-- 提取所有单词
local words = {}
for w in string.gmatch("Hello World Lua", "%a+") do
    words[#words + 1] = w
end
print(table.concat(words, ", "))  -- Hello, World, Lua
```

**示例 4：string.gsub 替换**

```lua
local s = "Hello, World!"

-- 简单替换
print(string.gsub(s, "World", "Lua"))  -- Hello, Lua!    1

-- 全局替换所有数字
print(string.gsub("a1b2c3", "%d", "#"))  -- a#b#c#    3

-- 使用捕获引用
print(string.gsub("2024-01-15", "(%d+)-(%d+)-(%d+)", "%3/%2/%1"))
-- 15/01/2024    1

-- 使用函数作为替换
local result = string.gsub("hello world", "%w+", function(word)
    return word:sub(1,1):upper() .. word:sub(2)
end)
print(result)  -- Hello World

-- 限制替换次数
print(string.gsub("aaaa", "a", "b", 2))  -- bbaa    2
```

**示例 5：常用模式字符类**

```lua
-- %a: 字母    %A: 非字母
print(string.match("abc123", "%a+"))  -- abc
print(string.match("abc123", "%A+"))  -- 123

-- %d: 数字    %D: 非数字
print(string.match("phone: 12345", "%d+"))  -- 12345

-- %s: 空白    %S: 非空白
print(string.match("  hello  ", "%S+"))  -- hello

-- %w: 字母数字  %W: 非字母数字
print(string.match("hello_world!", "%w+"))  -- hello_world

-- %p: 标点    %l: 小写  %u: 大写
print(string.match("Hello", "%u%l+"))  -- Hello

-- 自定义字符集 [...]
print(string.match("abc123", "[%a]+"))  -- abc
print(string.match("abc123", "[%a%d]+"))  -- abc123
```

### 总结

- Lua Pattern 是简化版的正则表达式，用 `%` 而非 `\` 作为转义前缀。
- 核心函数：`find`（查找位置）、`match`（返回匹配/捕获）、`gmatch`（迭代所有匹配）、`gsub`（替换）。
- 捕获用 `()`，替换中用 `%0`-`%9` 引用。
- `*` 贪婪、`-` 非贪婪、`+` 一次或多次、`?` 零或一次。
- **注意事项**：Lua Pattern 不支持 `|`、`{n,m}`、非捕获组等正则特性。需要复杂正则时，可考虑引入 `lrexlib` 等第三方库。`.` 等特殊字符需要用 `%` 转义（`%.` 匹配字面的点）。

---

# 第七章：元表与面向对象

元表（Metatable）是 Lua 最强大的特性之一，它允许自定义表的行为——运算符重载、默认值、继承等。通过元表，Lua 实现了面向对象编程、代理模式等高级抽象。本章将深入讲解元表与元方法，并实现完整的 OOP 系统。

---

## 第 19 讲：元表与元方法概念

### 概念

**元表**（metatable）是一个普通的表，用于定义另一个表（称为"原表"或"主表"）的某些行为。元表中包含的特殊键称为**元方法**（metamethod），它们以双下划线 `__` 开头，如 `__add`、`__index`、`__tostring` 等。当对原表执行特定操作（如算术运算、索引访问、比较等）时，Lua 会查找其元表中对应的元方法并调用。

### 原理

每个表都可以有一个元表（通过 `setmetatable` 设置）。当 Lua 遇到表参与的操作时（如 `a + b`），它会按以下步骤处理：

1. 检查操作数 `a` 是否有元表，且元表中是否有 `__add` 元方法。
2. 如果没有，检查操作数 `b` 的元表。
3. 如果都没有，且操作数类型不匹配，抛出类型错误。
4. 如果找到元方法，调用 `__add(a, b)` 并返回结果。

元方法使 Lua 能够实现运算符重载、自定义索引行为、模拟面向对象等。元表机制是 Lua 实现 OOP、原型继承、代理模式的基础。需要注意的是，字符串类型有自己的元表（可通过 `getmetatable("")` 查看），但用户不能修改字符串的元表（出于安全考虑）。

### 例子

**示例 1：设置和获取元表**

```lua
local t = {}
local mt = {__metatable = "locked"}  -- 设置 __metatable 可以锁定元表

setmetatable(t, mt)
print(getmetatable(t))  -- locked （返回 __metatable 的值而非真正的元表）

-- 不带 __metatable 的情况
local t2 = {}
local mt2 = {}
setmetatable(t2, mt2)
print(getmetatable(t2) == mt2)  -- true
```

**示例 2：__tostring 元方法**

```lua
local person = {name = "Alice", age = 30}
local mt = {
    __tostring = function(self)
        return string.format("Person(name=%s, age=%d)", self.name, self.age)
    end
}
setmetatable(person, mt)

print(person)  -- Person(name=Alice, age=30)
-- 没有 __tostring 时，print(person) 会输出 table: 0x...
```

**示例 3：__len 元方法**

```lua
local arr = {1, 2, 3}
arr[100] = 100  -- 创建空洞

local mt = {
    __len = function(self)
        local count = 0
        for _ in pairs(self) do
            count = count + 1
        end
        return count
    end
}
setmetatable(arr, mt)

print(#arr)  -- 4 （实际元素个数，而非不确定的值）
```

**示例 4：__pairs 元方法（Lua 5.2+）**

```lua
local orderedKeys = {"name", "age", "city"}
local data = {name = "Alice", age = 30, city = "Beijing"}

local mt = {
    __pairs = function(self)
        local i = 0
        return function()
            i = i + 1
            local k = orderedKeys[i]
            if k then
                return k, self[k]
            end
        end
    end
}
setmetatable(data, mt)

for k, v in pairs(data) do
    print(k, v)
end
-- name    Alice
-- age     30
-- city    Beijing
-- （按指定顺序遍历）
```

### 总结

- 元表是定义表行为的普通表，元方法以 `__` 开头。
- `setmetatable(t, mt)` 设置元表，`getmetatable(t)` 获取元表。
- 设置 `__metatable` 字段可以"锁定"元表，`getmetatable` 返回该字段的值。
- 常用元方法：`__tostring`（字符串表示）、`__len`（长度）、`__pairs`（自定义遍历）。
- **注意事项**：字符串类型的元表不可修改；元表是引用，多个表可以共享同一个元表。

---

## 第 20 讲：算术与关系元方法

### 概念

Lua 允许通过元方法重载算术运算符（`+`、`-`、`*`、`/`、`%`、`^`、`//`、一元 `-`）和关系运算符（`==`、`<`、`<=`）。这使得用户可以定义自定义类型（如向量、复数、矩阵）的运算行为，使代码更自然、更表达力强。

### 原理

算术元方法：当参与运算的操作数不是数字（或其中之一不是数字）时，Lua 查找元方法。例如 `a + b` 会查找 `a` 的 `__add` 或 `b` 的 `__add`。对应的元方法名：`__add`（+）、`__sub`（-）、`__mul`（*）、`__div`（/）、`__mod`（%）、`__pow`（^）、`__idiv`（//）、`__unm`（一元 -）。

关系元方法：`__eq`（==）、`__lt`（<）、`__le`（<=）。注意 Lua 只定义了这三个关系元方法，`~=`、`>`、`>=` 通过这三个推导：`a ~= b` 等价于 `not (a == b)`，`a > b` 等价于 `b < a`，`a >= b` 等价于 `b <= a`。关系元方法只在两个操作数类型相同且都不是数字或字符串时才被查找。`__eq` 只在两个值都是表（且不是同一个引用）时才被调用。

### 例子

**示例 1：向量类——算术运算**

```lua
local Vector = {}
Vector.__index = Vector

function Vector.new(x, y)
    local self = setmetatable({}, Vector)
    self.x = x or 0
    self.y = y or 0
    return self
end

-- 重载加法
function Vector.__add(a, b)
    return Vector.new(a.x + b.x, a.y + b.y)
end

-- 重载减法
function Vector.__sub(a, b)
    return Vector.new(a.x - b.x, a.y - b.y)
end

-- 重载数乘（向量 * 标量）
function Vector.__mul(a, b)
    if type(a) == "number" then
        return Vector.new(a * b.x, a * b.y)
    elseif type(b) == "number" then
        return Vector.new(a.x * b, a.y * b)
    else
        -- 点积
        return a.x * b.x + a.y * b.y
    end
end

-- 重载一元负号
function Vector.__unm(a)
    return Vector.new(-a.x, -a.y)
end

-- 字符串表示
function Vector.__tostring(self)
    return string.format("(%d, %d)", self.x, self.y)
end

-- 使用
local v1 = Vector.new(1, 2)
local v2 = Vector.new(3, 4)

print(v1 + v2)      -- (4, 6)
print(v2 - v1)      -- (2, 2)
print(v1 * 3)       -- (3, 6)
print(2 * v2)       -- (6, 8)
print(v1 * v2)      -- 11 （点积）
print(-v1)          -- (-1, -2)
```

**示例 2：复数类——完整算术**

```lua
local Complex = {}
Complex.__index = Complex

function Complex.new(real, imag)
    return setmetatable({real = real or 0, imag = imag or 0}, Complex)
end

Complex.__add = function(a, b) return Complex.new(a.real+b.real, a.imag+b.imag) end
Complex.__sub = function(a, b) return Complex.new(a.real-b.real, a.imag-b.imag) end
Complex.__mul = function(a, b)
    return Complex.new(a.real*b.real - a.imag*b.imag, a.real*b.imag + a.imag*b.real)
end
Complex.__div = function(a, b)
    local denom = b.real^2 + b.imag^2
    return Complex.new((a.real*b.real + a.imag*b.imag)/denom,
                       (a.imag*b.real - a.real*b.imag)/denom)
end
Complex.__eq = function(a, b) return a.real == b.real and a.imag == b.imag end
Complex.__tostring = function(self)
    if self.imag >= 0 then
        return string.format("%.2f+%.2fi", self.real, self.imag)
    else
        return string.format("%.2f%.2fi", self.real, self.imag)
    end
end

local c1 = Complex.new(1, 2)  -- 1+2i
local c2 = Complex.new(3, -1) -- 3-1i

print(c1 + c2)  -- 4.00+1.00i
print(c1 * c2)  -- 5.00+5.00i
print(c1 == Complex.new(1, 2))  -- true
```

**示例 3：关系运算——版本号比较**

```lua
local Version = {}
Version.__index = Version

function Version.new(major, minor, patch)
    return setmetatable({major=major, minor=minor, patch=patch}, Version)
end

Version.__lt = function(a, b)
    if a.major ~= b.major then return a.major < b.major end
    if a.minor ~= b.minor then return a.minor < b.minor end
    return a.patch < b.patch
end

Version.__le = function(a, b)
    return a < b or a == b
end

Version.__eq = function(a, b)
    return a.major == b.major and a.minor == b.minor and a.patch == b.patch
end

Version.__tostring = function(self)
    return string.format("%d.%d.%d", self.major, self.minor, self.patch)
end

local v1 = Version.new(1, 2, 3)
local v2 = Version.new(1, 3, 0)
local v3 = Version.new(1, 2, 3)

print(v1 < v2)   -- true
print(v1 <= v3)  -- true
print(v1 == v3)  -- true
print(v2 > v1)   -- true （通过 < 推导）
print(v1 ~= v2)  -- true （通过 == 推导）
```

### 总结

- 算术元方法：`__add`、`__sub`、`__mul`、`__div`、`__mod`、`__pow`、`__idiv`、`__unm`。
- 关系元方法：`__eq`（==）、`__lt`（<）、`__le`（<=），其他关系运算符通过这三个推导。
- `__eq` 只在两个操作数都是表（且非同一引用）时才被调用。
- 算术元方法支持混合类型运算（如表与数字），通过 `type()` 判断处理。
- **注意事项**：`~=`、`>`、`>=` 没有对应的元方法，它们通过 `==`、`<`、`<=` 推导。

---

## 第 21 讲：__index 与 __newindex

### 概念

`__index` 和 `__newindex` 是两个最重要的元方法，用于自定义表的**读取**和**写入**行为。`__index` 控制当访问表中不存在的键时如何处理；`__newindex` 控制当向表中不存在的键赋值时如何处理。这两个元方法是实现继承、代理、默认值等功能的核心。

### 原理

**`__index` 元方法**：当执行 `t[key]` 时，如果 `t` 中没有 `key` 这个键，Lua 会查找 `t` 的元表的 `__index` 元方法。`__index` 可以是：
- 一个**表**：Lua 递归地在这个表中查找 `key`（如果这个表也有元表，继续查找，形成继承链）。
- 一个**函数**：Lua 调用 `__index(t, key)`，返回函数的返回值。

**`__newindex` 元方法**：当执行 `t[key] = value` 时，如果 `t` 中没有 `key` 这个键，Lua 会查找 `t` 的元表的 `__newindex` 元方法。`__newindex` 可以是：
- 一个**表**：Lua 在这个表中执行 `newindex_table[key] = value`。
- 一个**函数**：Lua 调用 `__newindex(t, key, value)`。

如果 `t` 中已经有 `key`（即使值为 `nil`），`__index` 和 `__newindex` 都不会被触发。要绕过元方法直接访问表，可以使用 `rawget(t, key)` 和 `rawset(t, key, value)`。

### 例子

**示例 1：__index 作为表——实现继承**

```lua
-- 父类
local Animal = {legs = 4}
function Animal:speak() return "..." end

-- 子类
local Dog = setmetatable({}, {__index = Animal})
function Dog:bark() return "Woof!" end

-- 实例
local myDog = setmetatable({}, {__index = Dog})

print(myDog.legs)     -- 4 （从 Animal 继承）
print(myDog:speak())  -- ... （从 Animal 继承）
print(myDog:bark())   -- Woof! （从 Dog 继承）

-- 继承链：myDog -> Dog -> Animal
```

**示例 2：__index 作为函数——默认值**

```lua
local defaults = {x = 0, y = 0, color = "black"}

local config = setmetatable({}, {
    __index = function(t, key)
        return defaults[key]  -- 返回默认值
    end
})

print(config.x)     -- 0 （默认值）
print(config.color) -- black （默认值）
print(config.z)     -- nil （无默认值）

config.x = 100      -- 设置自己的值
print(config.x)     -- 100 （自己的值，不再查 __index）
```

**示例 3：__newindex——只读表**

```lua
local function readOnly(t)
    local proxy = setmetatable({}, {
        __index = t,  -- 读取转发到原表
        __newindex = function(t, k, v)
            error("attempt to update a read-only table", 2)
        end
    })
    return proxy
end

local config = readOnly({host = "localhost", port = 8080})

print(config.host)  -- localhost
print(config.port)  -- 8080

config.port = 9090  -- 报错：attempt to update a read-only table
```

**示例 4：__newindex 作为表——日志代理**

```lua
local realData = {}
local log = {}

local proxy = setmetatable({}, {
    __index = realData,           -- 读取转发到 realData
    __newindex = function(t, k, v)
        log[#log + 1] = string.format("SET %s = %s", k, tostring(v))
        realData[k] = v           -- 实际存储到 realData
    end
})

proxy.name = "Alice"
proxy.age = 30

print(proxy.name)  -- Alice
print(proxy.age)   -- 30

-- 查看日志
for _, entry in ipairs(log) do
    print(entry)
end
-- SET name = Alice
-- SET age = 30
```

**示例 5：rawget 和 rawset 绕过元方法**

```lua
local t = setmetatable({}, {
    __index = function() return "default" end,
    __newindex = function(t, k, v)
        print("拦截写入: " .. k .. " = " .. tostring(v))
    end
})

t.x = 10  -- 触发 __newindex，打印：拦截写入: x = 10
print(t.x)  -- 触发 __index，输出：default

-- 使用 rawset 绕过 __newindex
rawset(t, "y", 20)  -- 直接写入，不触发 __newindex
print(rawget(t, "y"))  -- 20 （直接读取，不触发 __index）
print(t.y)  -- 20 （t 中已有 y，不触发 __index）
```

### 总结

- `__index`：控制读取不存在的键的行为，可以是表（继承）或函数（自定义查找）。
- `__newindex`：控制写入不存在的键的行为，可以是表或函数。
- 继承链通过 `__index` 指向父表实现，可以多层嵌套。
- `rawget` / `rawset` 绕过元方法直接访问表。
- **注意事项**：只有键不存在时才触发 `__index`/`__newindex`；键存在但值为 `nil` 不会触发。

---

## 第 22 讲：面向对象编程实现

### 概念

Lua 没有内置的类（class）概念，但通过 Table + 元表可以完整实现面向对象编程（OOP），包括类、对象、继承、多态等特性。Lua 的 OOP 基于**原型**（prototype）模型，而非传统的类模型。对象通过克隆原型创建，方法通过 `__index` 元方法继承。

### 原理

Lua OOP 的核心模式：
1. **类定义**：一个 Table，其 `__index` 指向自身（使实例能访问类方法）。
2. **构造函数**：`Class.new(...)` 创建新表，设置元表为类，初始化字段。
3. **方法定义**：`function Class:method(args) ... end`，使用冒号语法糖（隐含 `self` 参数）。
4. **继承**：子类的元表 `__index` 指向父类，形成继承链。

冒号语法 `obj:method(args)` 是 `obj.method(obj, args)` 的语法糖，自动将对象作为第一个参数 `self` 传递。定义方法时 `function Class:method(args)` 等价于 `function Class.method(self, args)`。

多态通过元方法查找机制自然实现：调用 `obj:method()` 时，Lua 沿继承链查找第一个匹配的方法。子类可以覆盖父类方法，也可以通过 `BaseClass.method(self, ...)` 显式调用父类方法。

### 例子

**示例 1：基本类与对象**

```lua
-- 定义类
local Animal = {}
Animal.__index = Animal

-- 构造函数
function Animal.new(name, sound)
    local self = setmetatable({}, Animal)
    self.name = name
    self.sound = sound
    return self
end

-- 方法（使用冒号语法）
function Animal:speak()
    return self.name .. " says " .. self.sound
end

function Animal:getName()
    return self.name
end

-- 创建对象
local dog = Animal.new("Rex", "Woof")
local cat = Animal.new("Whiskers", "Meow")

print(dog:speak())  -- Rex says Woof
print(cat:speak())  -- Whiskers says Meow
print(dog:getName())  -- Rex
```

**示例 2：继承**

```lua
-- 基类
local Shape = {}
Shape.__index = Shape

function Shape.new(name)
    local self = setmetatable({}, Shape)
    self.name = name
    return self
end

function Shape:area()
    return 0  -- 基类默认实现
end

function Shape:describe()
    return string.format("%s, 面积: %.2f", self.name, self:area())
end

-- 子类：圆形
local Circle = setmetatable({}, {__index = Shape})
Circle.__index = Circle

function Circle.new(radius)
    local self = Shape.new("圆形")  -- 调用父类构造函数
    setmetatable(self, Circle)      -- 设置子类元表
    self.radius = radius
    return self
end

function Circle:area()
    return math.pi * self.radius ^ 2
end

-- 子类：矩形
local Rectangle = setmetatable({}, {__index = Shape})
Rectangle.__index = Rectangle

function Rectangle.new(width, height)
    local self = Shape.new("矩形")
    setmetatable(self, Rectangle)
    self.width = width
    self.height = height
    return self
end

function Rectangle:area()
    return self.width * self.height
end

-- 多态
local shapes = {
    Circle.new(5),
    Rectangle.new(4, 6),
    Circle.new(3)
}

for _, shape in ipairs(shapes) do
    print(shape:describe())
end
-- 圆形, 面积: 78.54
-- 矩形, 面积: 24.00
-- 圆形, 面积: 28.27
```

**示例 3：调用父类方法**

```lua
local Base = {}
Base.__index = Base

function Base.new()
    return setmetatable({}, Base)
end

function Base:greet()
    return "Hello from Base"
end

local Derived = setmetatable({}, {__index = Base})
Derived.__index = Derived

function Derived.new()
    return setmetatable({}, Derived)
end

function Derived:greet()
    -- 调用父类方法
    local baseGreeting = Base.greet(self)
    return baseGreeting .. " and Derived"
end

local d = Derived.new()
print(d:greet())  -- Hello from Base and Derived
```

**示例 4：完整的类实现工具**

```lua
-- 通用类创建函数
local function createClass(parent)
    local cls = {}
    -- 设置继承
    if parent then
        setmetatable(cls, {__index = parent})
    end
    cls.__index = cls
    cls.super = parent

    -- 默认构造函数
    cls.new = function(...)
        local obj = setmetatable({}, cls)
        if obj.init then
            obj:init(...)
        end
        return obj
    end

    return cls
end

-- 使用
local Animal = createClass()

function Animal:init(name)
    self.name = name
end

function Animal:speak()
    return self.name .. " makes a sound"
end

local Dog = createClass(Animal)

function Dog:init(name, breed)
    self.super.init(self, name)  -- 调用父类 init
    self.breed = breed
end

function Dog:speak()
    return self.name .. " (" .. self.breed .. ") says Woof"
end

local d = Dog.new("Buddy", "Golden Retriever")
print(d:speak())  -- Buddy (Golden Retriever) says Woof
```

### 总结

- Lua OOP 基于原型模型，通过 Table + 元表实现。
- 类模式：`Class.__index = Class`，构造函数设置元表为类。
- 冒号语法 `obj:method()` 自动传递 `self`。
- 继承：子类元表的 `__index` 指向父类。
- 多态：通过元方法查找自然实现，子类方法覆盖父类。
- 调用父类方法：`ParentClass.method(self, args)`。
- **注意事项**：Lua 没有访问控制（public/private），约定用下划线前缀表示"私有"字段。

---

# 第八章：协程与错误处理

协程（coroutine）是 Lua 的并发编程机制，允许函数暂停和恢复执行。错误处理机制（pcall、error、assert）保证程序在异常情况下的健壮性。本章讲解这两个重要主题。

---

## 第 23 讲：协程基础

### 概念

协程是一种**协作式多任务**机制，允许函数在执行过程中暂停（yield）并稍后恢复（resume）。与线程不同，协程不是由操作系统调度的，而是由程序显式控制切换。Lua 的协程是**非对称协程**（asymmetric coroutine），即有明确的"调用者"和"被调用者"关系。协程适用于生成器、迭代器、状态机、协作式多任务等场景。

### 原理

Lua 协程通过 `coroutine` 标准库管理。创建协程使用 `coroutine.create(f)`，返回一个协程对象（thread 类型）。协程有四种状态：**suspended**（挂起，初始状态）、**running**（运行中）、**normal**（正常，即协程在恢复另一个协程）、**dead**（已结束）。

`coroutine.resume(co, ...)` 恢复协程，传递参数给 `yield` 或协程函数的初始参数。`coroutine.yield(...)` 暂停协程，将值传回给 `resume` 的调用者。`resume` 返回两个值：第一个是布尔值表示是否成功（如果协程内发生错误，返回 `false` 和错误信息），后续是 `yield` 传递的值或协程函数的返回值。

协程的执行流程：`resume` 启动/恢复协程 → 协程执行到 `yield` 暂停 → `resume` 返回 → 再次 `resume` 从 `yield` 处继续。这种"乒乓"式的交互使协程成为实现生成器和协作式调度的理想工具。

### 例子

**示例 1：创建和恢复协程**

```lua
local co = coroutine.create(function(a, b)
    print("协程启动, 参数:", a, b)
    local c = coroutine.yield(a + b)  -- 暂停，返回 a+b，等待下次 resume 的参数
    print("协程恢复, 收到:", c)
    return a + b + c
end)

-- 第一次 resume（传递初始参数）
local ok, result = coroutine.resume(co, 10, 20)
print("第一次 resume 返回:", ok, result)  -- true    30

-- 第二次 resume（传递给 yield 的返回值）
local ok2, result2 = coroutine.resume(co, 100)
print("第二次 resume 返回:", ok2, result2)  -- true    130

-- 第三次 resume（协程已结束）
local ok3, result3 = coroutine.resume(co)
print("第三次 resume 返回:", ok3, result3)  -- false    cannot resume dead coroutine
```

**示例 2：协程状态**

```lua
local co = coroutine.create(function()
    coroutine.yield()
    coroutine.yield()
end)

print(coroutine.status(co))  -- suspended
coroutine.resume(co)
print(coroutine.status(co))  -- suspended
coroutine.resume(co)
print(coroutine.status(co))  -- suspended
coroutine.resume(co)
print(coroutine.status(co))  -- dead
```

**示例 3：用协程实现生成器**

```lua
-- 生成斐波那契数列
local function fibGen()
    local a, b = 0, 1
    while true do
        coroutine.yield(a)
        a, b = b, a + b
    end
end

local co = coroutine.create(fibGen)

-- 获取前 10 个斐波那契数
for i = 1, 10 do
    local ok, val = coroutine.resume(co)
    print(val)
end
-- 0, 1, 1, 2, 3, 5, 8, 13, 21, 34
```

**示例 4：coroutine.wrap 简化用法**

```lua
-- coroutine.wrap 创建一个每次调用就 resume 的函数
local fib = coroutine.wrap(function()
    local a, b = 0, 1
    while true do
        coroutine.yield(a)
        a, b = b, a + b
    end
end)

-- 直接调用，无需 resume
for i = 1, 10 do
    print(fib())  -- 0, 1, 1, 2, 3, 5, 8, 13, 21, 34
end

-- wrap 返回的函数不返回成功标志，出错时直接抛出错误
```

**示例 5：用协程实现迭代器**

```lua
local function range(start, stop, step)
    step = step or 1
    return coroutine.wrap(function()
        for i = start, stop, step do
            coroutine.yield(i)
        end
    end)
end

-- 像内置迭代器一样使用
for v in range(1, 10, 2) do
    print(v)  -- 1, 3, 5, 7, 9
end

-- 也可以手动取值
local gen = range(10, 1, -2)
print(gen())  -- 10
print(gen())  -- 8
print(gen())  -- 6
```

### 总结

- 协程是协作式多任务机制，由程序显式控制切换。
- `coroutine.create` 创建协程，`resume` 恢复，`yield` 暂停。
- 协程状态：suspended、running、normal、dead。
- `coroutine.wrap` 是简化版，返回一个直接调用的函数。
- 协程常用于生成器、迭代器、状态机等。
- **注意事项**：`resume` 返回的第一个值是成功标志，应检查以处理错误。协程内不能调用会阻塞的 C 函数（如 `io.read`）。

---

## 第 24 讲：协程进阶应用

### 概念

协程除了基本的生成器模式，还可以实现更复杂的应用：生产者-消费者模型、协作式多任务调度、异步编程模拟等。本讲探讨协程在实际场景中的高级用法。

### 原理

协程的非对称性意味着每次 `yield` 必须返回到对应的 `resume`。这种特性使得协程天然适合实现"协作式"的并发模型：多个协程交替执行，每个协程在适当的时候主动让出控制权。与抢占式多线程不同，协程不需要锁（因为同一时刻只有一个协程在执行），但要求每个协程在合适的位置 `yield`。

协程还可以用于实现**非阻塞 I/O** 的模拟：在 I/O 操作前 `yield`，让调度器执行其他协程，I/O 完成后再 `resume`。这是 Lua 协程在游戏开发（如 Love2D）和网络编程（如 OpenResty）中的核心应用模式。

### 例子

**示例 1：生产者-消费者**

```lua
-- 生产者协程
local function producer()
    for i = 1, 5 do
        print("生产: " .. i)
        coroutine.yield(i)  -- 产出数据，暂停
    end
end

-- 消费者
local function consume()
    local co = coroutine.create(producer)
    while true do
        local ok, value = coroutine.resume(co)
        if not ok or coroutine.status(co) == "dead" then
            break
        end
        print("消费: " .. value)
    end
end

consume()
-- 生产: 1
-- 消费: 1
-- 生产: 2
-- 消费: 2
-- ...
```

**示例 2：协作式任务调度器**

```lua
local tasks = {}

local function addTask(fn)
    tasks[#tasks + 1] = coroutine.create(fn)
end

local function runScheduler()
    while #tasks > 0 do
        local i = 1
        while i <= #tasks do
            local co = tasks[i]
            local ok = coroutine.resume(co)
            if not ok or coroutine.status(co) == "dead" then
                table.remove(tasks, i)
            else
                i = i + 1
            end
        end
    end
end

-- 定义任务
addTask(function()
    for i = 1, 3 do
        print("任务A - 步骤 " .. i)
        coroutine.yield()
    end
end)

addTask(function()
    for i = 1, 3 do
        print("任务B - 步骤 " .. i)
        coroutine.yield()
    end
end)

runScheduler()
-- 任务A - 步骤 1
-- 任务B - 步骤 1
-- 任务A - 步骤 2
-- 任务B - 步骤 2
-- 任务A - 步骤 3
-- 任务B - 步骤 3
```

**示例 3：模拟异步 I/O**

```lua
-- 模拟异步读取
local function asyncRead(file)
    coroutine.yield("IO_READ", file)
    return "文件内容: " .. file  -- 模拟返回的数据
end

-- 异步任务
local function fetchUserData()
    local data1 = asyncRead("user.json")
    print("收到: " .. data1)
    local data2 = asyncRead("settings.json")
    print("收到: " .. data2)
    return "完成"
end

-- 简单的事件循环
local function runAsync(fn)
    local co = coroutine.create(fn)
    local results = {coroutine.resume(co)}
    while results[1] and coroutine.status(co) ~= "dead" do
        local eventType, file = results[2], results[3]
        if eventType == "IO_READ" then
            print("[IO] 模拟读取 " .. file .. "...")
            -- 模拟 I/O 完成后恢复
            results = {coroutine.resume(co)}
        end
    end
    return results[2]
end

print(runAsync(fetchUserData))
-- [IO] 模拟读取 user.json...
-- 收到: 文件内容: user.json
-- [IO] 模拟读取 settings.json...
-- 收到: 文件内容: settings.json
-- 完成
```

**示例 4：协程实现状态机**

```lua
local function trafficLight()
    local states = {"红", "绿", "黄"}
    local i = 1
    while true do
        coroutine.yield(states[i])
        i = i % 3 + 1  -- 循环 1->2->3->1
    end
end

local light = coroutine.wrap(trafficLight)
for i = 1, 10 do
    print("信号灯:", light())
end
-- 红, 绿, 黄, 红, 绿, 黄, 红, 绿, 黄, 红
```

### 总结

- 协程适合实现生产者-消费者、任务调度、状态机等模式。
- 协作式多任务不需要锁，但要求协程主动 `yield`。
- 协程可以模拟异步 I/O，是非阻塞编程的基础。
- `coroutine.wrap` 适合简单的生成器场景，`create` + `resume` 适合需要错误处理的复杂场景。
- **注意事项**：协程不是真正的并发，不能利用多核 CPU；协程内的 C 函数调用如果阻塞，会阻塞整个程序。

---

## 第 25 讲：错误处理机制（pcall / error / assert）

### 概念

Lua 的错误处理机制包括：`error(msg, level)` 抛出错误，`pcall(f, ...)` 以保护模式调用函数（捕获错误），`assert(v, msg)` 断言（值为假时抛出错误），`xpcall(f, handler)` 带错误处理函数的保护调用。这些机制使 Lua 程序能够优雅地处理异常情况。

### 原理

Lua 的错误处理基于**异常机制**。当调用 `error(msg)` 时，Lua 会中断当前执行流，沿调用栈向上传播错误，直到被 `pcall` 捕获或到达顶层（导致程序终止）。`error` 的第二个参数 `level` 指定错误位置：`1`（默认）指向调用 `error` 的函数，`2` 指向调用者的调用者，`0` 不添加位置信息。

`pcall`（protected call）以保护模式调用函数，返回 `true` 和函数返回值（成功时），或 `false` 和错误信息（失败时）。`pcall` 不会传播错误，使调用者有机会处理异常。`xpcall` 类似但额外接受一个错误处理函数，可以在错误发生时获取调用栈信息（通过 `debug.traceback`）。

`assert(v, msg)` 检查 `v` 是否为真（非 `nil` 和非 `false`），如果为假则调用 `error(msg)` 抛出错误。`assert` 常用于参数检查和前置条件验证。注意 `assert` 返回其参数，因此常用于 `local f = assert(io.open(filename))` 这样的模式。

### 例子

**示例 1：error 抛出错误**

```lua
local function divide(a, b)
    if b == 0 then
        error("除数不能为零", 2)  -- level=2 指向调用 divide 的位置
    end
    return a / b
end

print(divide(10, 2))  -- 5.0
-- print(divide(10, 0))  -- 报错：test.lua:3: 除数不能为零
```

**示例 2：pcall 捕获错误**

```lua
local function riskyOperation(x)
    if x < 0 then
        error("x 不能为负数: " .. x)
    end
    return math.sqrt(x)
end

-- 使用 pcall 安全调用
local ok, result = pcall(riskyOperation, 16)
if ok then
    print("成功:", result)  -- 成功: 4.0
else
    print("失败:", result)  -- result 是错误信息
end

local ok2, result2 = pcall(riskyOperation, -5)
if ok2 then
    print("成功:", result2)
else
    print("失败:", result2)  -- 失败: test.lua:3: x 不能为负数: -5
end
```

**示例 3：assert 断言**

```lua
local function factorial(n)
    assert(type(n) == "number", "n 必须是数字")
    assert(n >= 0, "n 不能为负数")
    assert(n == math.floor(n), "n 必须是整数")

    if n == 0 then return 1 end
    return n * factorial(n - 1)
end

print(factorial(5))  -- 120
-- print(factorial(-1))  -- 报错: n 不能为负数
-- print(factorial(3.5))  -- 报错: n 必须是整数
-- print(factorial("abc"))  -- 报错: n 必须是数字

-- assert 返回参数的常见用法
local function openFile(path)
    local file = assert(io.open(path, "r"))  -- 如果打开失败，assert 会抛出错误
    return file
end
```

**示例 4：xpcall 获取调用栈**

```lua
local function innerFunction()
    error("内部错误")
end

local function middleFunction()
    innerFunction()
end

local function outerFunction()
    middleFunction()
end

-- 使用 xpcall 和 debug.traceback
local function errorHandler(err)
    return debug.traceback("错误: " .. tostring(err), 2)
end

local ok, err = xpcall(outerFunction, errorHandler)
if not ok then
    print(err)
    -- 输出包含完整调用栈的错误信息：
    -- 错误: test.lua:2: 内部错误
    -- stack traceback:
    --     test.lua:2: in function 'innerFunction'
    --     test.lua:6: in function 'middleFunction'
    --     test.lua:10: in function 'outerFunction'
    --     ...
end
```

**示例 5：实际应用——安全执行用户脚本**

```lua
local function runUserCode(code)
    local fn, err = load(code)
    if not fn then
        return false, "语法错误: " .. err
    end

    -- 在保护模式下执行
    local ok, result = pcall(fn)
    if ok then
        return true, result
    else
        return false, "运行时错误: " .. result
    end
end

-- 测试
local code1 = "return 1 + 2"
local ok1, result1 = runUserCode(code1)
print(ok1, result1)  -- true    3

local code2 = "error('用户代码出错')"
local ok2, result2 = runUserCode(code2)
print(ok2, result2)  -- false    运行时错误: 用户代码出错

local code3 = "this is not valid lua"
local ok3, result3 = runUserCode(code3)
print(ok3, result3)  -- false    语法错误: ...
```

### 总结

- `error(msg, level)` 抛出错误，`level` 控制错误位置信息。
- `pcall(f, ...)` 保护模式调用，返回 `true, results` 或 `false, error`。
- `assert(v, msg)` 断言，为假时抛出错误，返回参数本身。
- `xpcall(f, handler)` 带错误处理函数的保护调用，可获取调用栈。
- `load(code)` 编译代码字符串，返回函数或 `nil` + 错误信息。
- **注意事项**：错误信息可以是任意类型（不只是字符串），`pcall` 会原样返回；生产代码应始终检查 `pcall` 的返回值。

---

# 第九章：模块与进阶主题

本章讲解 Lua 的模块系统、弱引用表、环境（_ENV）和垃圾回收等进阶主题。这些特性对于编写大型 Lua 项目和优化性能至关重要。

---

## 第 26 讲：模块系统

### 概念

模块（module）是组织代码的基本单位，将相关的函数和数据封装在一起，通过 `require` 函数加载。Lua 的模块本质上是一个 Table，包含导出的函数和变量。Lua 5.1+ 使用 `require` 函数加载模块，支持模块搜索路径、缓存机制和循环依赖处理。

### 原理

`require(modname)` 的工作流程：
1. 检查 `package.loaded[modname]`，如果模块已加载，直接返回缓存的值。
2. 如果未加载，使用 `package.searchers`（Lua 5.2+，5.1 中为 `package.loaders`）搜索模块。
3. 搜索器按顺序尝试：先找 `package.preload[modname]` 中的预加载函数，再在 `package.path` 指定的路径中查找 Lua 文件，最后在 `package.cpath` 中查找 C 扩展模块。
4. 找到模块后，执行模块代码，将返回值存入 `package.loaded[modname]` 并返回。

模块的标准编写模式：创建一个 Table，将所有导出的函数和变量放入其中，最后 `return` 这个 Table。`require` 返回的就是这个 Table。模块中声明的 `local` 变量是模块私有的，外部无法访问。

### 例子

**示例 1：编写模块（mymath.lua）**

```lua
-- mymath.lua
local M = {}  -- 模块表

-- 私有变量（外部无法访问）
local PI = 3.14159265358979

-- 私有函数
local function validate(n)
    assert(type(n) == "number", "参数必须是数字")
end

-- 公有函数
function M.circleArea(r)
    validate(r)
    return PI * r * r
end

function M.circlePerimeter(r)
    validate(r)
    return 2 * PI * r
end

function M.factorial(n)
    validate(n)
    assert(n >= 0 and n == math.floor(n), "n 必须是非负整数")
    if n <= 1 then return 1 end
    return n * M.factorial(n - 1)
end

-- 导出常量
M.PI = PI

return M
```

**示例 2：使用模块**

```lua
-- main.lua
local mymath = require("mymath")

print(mymath.circleArea(5))      -- 78.54...
print(mymath.circlePerimeter(5)) -- 31.42...
print(mymath.factorial(5))       -- 120
print(mymath.PI)                  -- 3.1415926535898

-- 私有成员无法访问
-- print(mymath.validate)  -- nil
```

**示例 3：模块的多种编写风格**

```lua
-- 风格 1：标准表式（推荐）
local M = {}
function M.foo() print("foo") end
return M

-- 风格 2：直接使用模块名（Lua 5.1 module 函数，已不推荐）
-- local M = {}
-- module("mymodule")
-- function foo() print("foo") end  -- 自动成为 mymodule.foo

-- 风格 3：返回函数（函数式模块）
return function(x)
    return x * 2
end

-- 使用函数式模块
local doubler = require("doubler")
print(doubler(21))  -- 42
```

**示例 4：模块路径与搜索**

```lua
-- 查看 Lua 模块搜索路径
print(package.path)
-- 通常类似: ./?.lua;./?/init.lua;/usr/share/lua/5.3/?.lua;...

-- 查看 C 模块搜索路径
print(package.cpath)

-- 动态修改搜索路径
package.path = package.path .. ";./libs/?.lua;./libs/?/init.lua"

-- 查看已加载的模块
for name, mod in pairs(package.loaded) do
    print(name, mod)
end

-- 重新加载模块（清除缓存）
package.loaded.mymath = nil
local mymath2 = require("mymath")  -- 重新执行模块代码
```

**示例 5：模块的子模块**

```lua
-- 目录结构:
-- myapp/
--   init.lua        (模块 myapp)
--   utils.lua       (模块 myapp.utils)
--   config.lua      (模块 myapp.config)

-- myapp/init.lua
local M = {}
M.utils = require("myapp.utils")
M.config = require("myapp.config")

function M.run()
    print(M.config.appName)
    M.utils.log("应用启动")
end

return M

-- 使用
local app = require("myapp")
app.run()
```

### 总结

- 模块是组织代码的基本单位，本质是一个 Table。
- `require` 加载模块，有缓存机制（`package.loaded`）。
- 标准模式：创建 Table，填充导出内容，`return` 该 Table。
- `local` 变量是模块私有的，实现封装。
- 模块路径通过 `package.path` 配置，支持 `?.lua` 和 `?/init.lua` 两种模式。
- **注意事项**：避免在模块顶层执行有副作用的代码；循环依赖时 `require` 返回 `nil`，需要重构模块结构。

---

## 第 27 讲：弱引用表

### 概念

**弱引用表**（weak table）是一种特殊的表，其键和/或值可以是弱引用。弱引用不会阻止垃圾回收器回收对象——如果一个对象只被弱引用表引用，它仍然会被回收。弱引用表适用于缓存、记忆化、对象关联等场景，可以避免内存泄漏。

### 原理

普通表的键和值都是**强引用**——只要表存在，其中的键和值就不会被回收。弱引用表通过元表的 `__mode` 字段指定弱引用模式：
- `__mode = "k"`：键为弱引用（弱键表）。
- `__mode = "v"`：值为弱引用（弱值表）。
- `__mode = "kv"`：键和值都为弱引用。

当垃圾回收器运行时，会检查弱引用表中的键和值。如果一个对象（表、函数、线程等）除了弱引用外没有其他引用，它会被回收，对应的键值对从弱引用表中移除。注意：**字符串和数字不受弱引用影响**——字符串有内部驻留机制，数字是值类型而非引用。

弱引用表的典型应用是**缓存**：以对象为键缓存计算结果，当对象被回收时，缓存自动清理。另一个应用是**对象属性附加**：在不修改原对象的情况下，通过弱引用表为对象附加额外属性。

### 例子

**示例 1：弱值表**

```lua
-- 创建弱值表
local cache = setmetatable({}, {__mode = "v"})

-- 创建一些对象
local key1 = {name = "obj1"}
local key2 = {name = "obj2"}

cache.a = key1
cache.b = key2

print(cache.a.name)  -- obj1
print(cache.b.name)  -- obj2

-- 移除强引用
key1 = nil

-- 强制垃圾回收
collectgarbage("collect")

-- key1 对应的对象被回收
print(cache.a)  -- nil （弱值被回收）
print(cache.b.name)  -- obj2 （仍有强引用 key2）
```

**示例 2：弱键表**

```lua
-- 创建弱键表（以对象为键）
local objectData = setmetatable({}, {__mode = "k"})

local obj = {id = 1}
objectData[obj] = {createdAt = os.time(), visits = 0}

print(objectData[obj].visits)  -- 0

-- 移除对 obj 的强引用
obj = nil
collectgarbage("collect")

-- obj 被回收，对应的键值对也被移除
local count = 0
for _ in pairs(objectData) do count = count + 1 end
print(count)  -- 0
```

**示例 3：记忆化缓存**

```lua
-- 使用弱表实现记忆化（缓存函数结果）
local function memoize(fn)
    local cache = setmetatable({}, {__mode = "k"})  -- 弱键，参数对象回收时缓存清理
    return function(obj)
        if cache[obj] == nil then
            cache[obj] = fn(obj)
        end
        return cache[obj]
    end
end

-- 计算对象大小的函数（模拟耗时操作）
local function computeSize(obj)
    print("  [计算中...]")
    local count = 0
    for _ in pairs(obj) do count = count + 1 end
    return count
end

local memoized = memoize(computeSize)

local data = {a=1, b=2, c=3}
print(memoized(data))  -- [计算中...] 3
print(memoized(data))  -- 3 （直接返回缓存，不重新计算）

data = nil
collectgarbage("collect")
-- 缓存自动清理
```

**示例 4：临时表与弱引用**

```lua
-- 弱引用表用于关联临时数据
local function createTempTable()
    local tempData = setmetatable({}, {__mode = "k"})
    local objects = {}

    for i = 1, 5 do
        local obj = {id = i}
        objects[i] = obj
        tempData[obj] = "临时数据 " .. i
    end

    return objects, tempData
end

local objs, temp = createTempTable()
print(temp[objs[1]])  -- 临时数据 1

-- 移除部分对象的强引用
objs[1] = nil
objs[3] = nil
collectgarbage("collect")

-- 检查剩余的临时数据
local count = 0
for obj, data in pairs(temp) do
    count = count + 1
    print(obj.id, data)
end
print("剩余: " .. count)  -- 剩余: 3
```

### 总结

- 弱引用表通过 `__mode = "k"` / `"v"` / `"kv"` 指定弱引用模式。
- 弱引用不阻止垃圾回收，对象只被弱引用时会自动回收。
- 弱引用表适用于缓存、记忆化、对象属性附加等场景。
- 字符串和数字不受弱引用影响（字符串驻留、数字值类型）。
- **注意事项**：弱引用表的清理时机取决于垃圾回收器，不能依赖即时清理；避免在弱引用表中存储需要长期保留的数据。

---

## 第 28 讲：环境（_ENV）与垃圾回收

### 概念

Lua 5.2+ 引入了 `_ENV` 机制来管理全局变量。`_ENV` 是一个特殊的局部变量，所有"全局变量"访问实际上都是对 `_ENV` 表的访问。垃圾回收（Garbage Collection, GC）是 Lua 自动管理内存的机制，使用增量式标记-清除算法。理解这两个主题有助于编写更安全、更高效的 Lua 代码。

### 原理

**_ENV 机制**：在 Lua 5.2+ 中，每个代码块（chunk）都有一个隐含的局部变量 `_ENV`，初始值为全局环境表 `_G`。所有"全局变量"的读写（如 `x = 1` 或 `print(x)`）实际上是对 `_ENV.x` 的操作。通过修改 `_ENV`，可以改变代码块的全局环境，实现沙箱、命名空间隔离等功能。

`_G` 是全局环境表，默认情况下 `_ENV = _G`。在 Lua 5.1 中，全局变量直接存储在 `_G` 中，没有 `_ENV` 机制。

**垃圾回收**：Lua 使用增量式标记-清除（incremental mark-and-sweep）垃圾回收器。GC 分为多个阶段：
1. **标记**：从根集合（注册表、全局变量、当前栈）出发，标记所有可达对象。
2. **清除**：遍历所有对象，回收未标记的对象。
3. **增量执行**：GC 分多次小步执行，避免长时间停顿。

Lua 5.1 还支持 generational GC（分代回收，Lua 5.4 默认启用）。通过 `collectgarbage` 函数可以控制 GC 行为：`collect`（完整回收）、`step`（增量步进）、`stop`/`restart`（停止/重启）、`count`（查询内存使用）等。

### 例子

**示例 1：_ENV 基本概念**

```lua
-- 访问全局变量实际上是访问 _ENV
print(_ENV.print == print)  -- true
print(_ENV == _G)           -- true （默认情况下）

-- 设置全局变量等价于设置 _ENV 的字段
x = 100
print(_ENV.x)  -- 100
print(_G.x)    -- 100 （因为 _ENV 默认就是 _G）
```

**示例 2：使用 _ENV 创建沙箱**

```lua
-- 创建沙箱环境
local function runSandboxed(code)
    -- 创建一个隔离的环境
    local env = {
        print = print,       -- 只暴露安全的函数
        tonumber = tonumber,
        tostring = tostring,
        math = math,
        string = string,
        -- 不暴露 io, os, debug, loadfile, dofile 等危险函数
    }
    -- 设置元表，使访问未定义的全局变量返回 nil 而非报错
    setmetatable(env, {__index = function(t, k)
        error("attempt to access unglobal '" .. k .. "'", 2)
    end})

    -- 编译代码，指定环境
    local fn, err = load(code, "sandbox", "t", env)
    if not fn then
        return false, err
    end

    -- 在保护模式下执行
    return pcall(fn)
end

-- 测试
local code = [[
    print("Hello from sandbox!")
    print(math.sqrt(16))
]]

local ok, result = runSandboxed(code)
-- Hello from sandbox!
-- 4.0

-- 尝试访问危险函数
local badCode = [[
    os.remove("/etc/passwd")
]]
local ok2, err = runSandboxed(badCode)
print(ok2, err)  -- false    attempt to access unglobal 'os'
```

**示例 3：_ENV 实现命名空间**

```lua
-- 使用 _ENV 简化模块编写
local module = {}

do
    local _ENV = module  -- 改变环境，所有"全局"赋值都进入 module 表

    function greet(name)
        print("Hello, " .. name)
    end

    function add(a, b)
        return a + b
    end

    PI = 3.14159  -- 这会变成 module.PI
end

-- 使用
module.greet("Alice")  -- Hello, Alice
print(module.add(3, 5))  -- 8
print(module.PI)  -- 3.14159
```

**示例 4：垃圾回收控制**

```lua
-- 查询内存使用（单位 KB）
print(collectgarbage("count"))  -- 例如: 25.5

-- 完整垃圾回收
collectgarbage("collect")
print(collectgarbage("count"))  -- 回收后内存减少

-- 增量回收步进
collectgarbage("step", 100)  -- 执行 100 步

-- 停止/重启 GC（性能敏感场景）
collectgarbage("stop")
-- ... 执行关键代码 ...
collectgarbage("restart")

-- 设置 GC 参数（pause 和 step multiplier）
-- collectgarbage("setpause", 200)   -- GC 间隔（百分比）
-- collectgarbage("setstepmul", 200) -- GC 速度（百分比）
```

**示例 5：监测内存使用**

```lua
local function memoryUsage()
    return collectgarbage("count")  -- KB
end

local function withMemoryCheck(label, fn)
    local before = memoryUsage()
    local result = fn()
    local after = memoryUsage()
    print(string.format("[%s] 内存: %.2f KB -> %.2f KB (差值: %+.2f KB)",
          label, before, after, after - before))
    return result
end

-- 测试
withMemoryCheck("创建大数组", function()
    local t = {}
    for i = 1, 10000 do
        t[i] = {value = i}
    end
    return t
end)

collectgarbage("collect")
print(string.format("GC 后内存: %.2f KB", memoryUsage()))
```

### 总结

- `_ENV` 是隐含的局部变量，所有全局变量访问实际是对 `_ENV` 的访问。
- 修改 `_ENV` 可以创建沙箱环境、实现命名空间隔离。
- `_G` 是全局环境表，默认 `_ENV = _G`。
- Lua 使用增量式标记-清除 GC，通过 `collectgarbage` 函数控制。
- `collectgarbage("count")` 查询内存使用，`"collect"` 完整回收，`"stop"`/`"restart"` 控制 GC。
- **注意事项**：性能敏感场景可临时停止 GC；避免频繁创建大量临时对象以减少 GC 压力。

---

# 第十章：实战应用

Lua 最大的优势在于其可嵌入性——它可以作为 C/C++ 程序的脚本引擎，也可以在各种应用场景中发挥作用。本章讲解 Lua 与 C 的交互，以及 Lua 在游戏开发、Web 服务器等领域的实际应用。

---

## 第 29 讲：Lua 与 C 的交互

### 概念

Lua 与 C 的交互通过 **Lua C API** 实现。C 程序可以创建 Lua 状态机、执行 Lua 代码、读写 Lua 变量、调用 Lua 函数；Lua 代码也可以调用 C 函数（通过注册为 Lua 模块）。这种双向交互是 Lua 作为嵌入式脚本语言的核心能力。Lua C API 基于**栈**（stack）模型进行数据交换。

### 原理

Lua C API 的核心是 **Lua 栈**。C 和 Lua 之间的所有数据交换都通过栈进行：C 将数据压入栈，Lua 从栈中读取；反之亦然。栈是后进先出（LIFO）结构，索引从 1 开始（正索引从栈底算起，负索引从栈顶算起）。

主要操作：
- `lua_push*`：将 C 值压入栈（如 `lua_pushnumber`、`lua_pushstring`、`lua_pushnil`）。
- `lua_to*`：从栈中取值转换为 C 类型（如 `lua_tonumber`、`lua_tostring`）。
- `lua_gettop` / `lua_settop`：获取/设置栈顶位置。
- `lua_getglobal` / `lua_setglobal`：读写 Lua 全局变量。
- `lua_pcall`：保护模式调用 Lua 函数。

C 函数注册为 Lua 函数的签名：`int func(lua_State *L)`。C 函数通过栈接收参数，将返回值压入栈，并返回返回值的个数。

### 例子

**示例 1：C 嵌入 Lua（C 程序）**

```c
/* embed_lua.c */
#include <stdio.h>
#include <lua.h>
#include <lualib.h>
#include <lauxlib.h>

int main() {
    /* 创建 Lua 状态机 */
    lua_State *L = luaL_newstate();
    luaL_openlibs(L);  /* 打开标准库 */

    /* 执行 Lua 代码字符串 */
    if (luaL_dostring(L, "print('Hello from Lua!')") != LUA_OK) {
        fprintf(stderr, "Error: %s\n", lua_tostring(L, -1));
    }

    /* 执行 Lua 文件 */
    if (luaL_dofile(L, "script.lua") != LUA_OK) {
        fprintf(stderr, "Error: %s\n", lua_tostring(L, -1));
    }

    /* 调用 Lua 函数 */
    lua_getglobal(L, "add");    /* 获取全局函数 add */
    lua_pushnumber(L, 10);      /* 压入参数 1 */
    lua_pushnumber(L, 20);      /* 压入参数 2 */
    if (lua_pcall(L, 2, 1, 0) != LUA_OK) {  /* 调用，2参数，1返回值 */
        fprintf(stderr, "Error: %s\n", lua_tostring(L, -1));
    } else {
        double result = lua_tonumber(L, -1);  /* 获取返回值 */
        printf("Result: %f\n", result);
        lua_pop(L, 1);  /* 弹出返回值 */
    }

    lua_close(L);  /* 关闭状态机 */
    return 0;
}
```

对应的 Lua 文件 `script.lua`：
```lua
function add(a, b)
    return a + b
end
```

编译：`gcc embed_lua.c -o embed_lua -llua -lm`

**示例 2：C 函数注册为 Lua 模块**

```c
/* mylib.c */
#include <lua.h>
#include <lauxlib.h>

/* C 函数：计算阶乘 */
static int l_factorial(lua_State *L) {
    int n = luaL_checkinteger(L, 1);  /* 获取参数 */
    long long result = 1;
    for (int i = 2; i <= n; i++) {
        result *= i;
    }
    lua_pushinteger(L, result);  /* 压入返回值 */
    return 1;  /* 返回值个数 */
}

/* C 函数：字符串反转 */
static int l_reverse(lua_State *L) {
    const char *s = luaL_checkstring(L, 1);
    size_t len = strlen(s);
    char *reversed = (char *)malloc(len + 1);
    for (size_t i = 0; i < len; i++) {
        reversed[i] = s[len - 1 - i];
    }
    reversed[len] = '\0';
    lua_pushstring(L, reversed);
    free(reversed);
    return 1;
}

/* 模块注册表 */
static const struct luaL_Reg mylib[] = {
    {"factorial", l_factorial},
    {"reverse", l_reverse},
    {NULL, NULL}
};

/* 模块入口函数 */
int luaopen_mylib(lua_State *L) {
    luaL_newlib(L, mylib);  /* 创建模块表 */
    return 1;
}
```

编译为动态库：`gcc -shared -o mylib.so mylib.c -llua`

在 Lua 中使用：
```lua
local mylib = require("mylib")

print(mylib.factorial(5))  -- 120
print(mylib.reverse("Hello"))  -- olleH
```

**示例 3：Lua 调用 C 函数的栈操作示意**

```
调用 mylib.factorial(5) 时的栈变化:

1. C 函数被调用，栈状态:
   [1] = 5  (参数)

2. luaL_checkinteger(L, 1) 读取参数:
   [1] = 5

3. 计算完成，lua_pushinteger(L, 120) 压入结果:
   [1] = 5
   [2] = 120  (返回值)

4. return 1; 告诉 Lua 返回 1 个值:
   Lua 取走栈顶的 120 作为返回值
```

**示例 4：使用 LuaJIT 的 FFI（更简单的 C 交互）**

```lua
-- LuaJIT 的 FFI 允许直接调用 C 函数，无需编写 C 代码
local ffi = require("ffi")

-- 声明 C 函数签名
ffi.cdef[[
    int printf(const char *fmt, ...);
    double sqrt(double x);
]]

-- 调用 C 函数
ffi.C.printf("Hello %s!\n", "World")
print(ffi.C.sqrt(144))  -- 12.0

-- 加载 C 库
local ffi = require("ffi")
ffi.cdef[[
    typedef struct { int x, y; } Point;
    double distance(Point a, Point b);
]]

-- 假设有编译好的 libgeom.so
local geom = ffi.load("geom")
local p1 = ffi.new("Point", {0, 0})
local p2 = ffi.new("Point", {3, 4})
print(geom.distance(p1, p2))  -- 5.0
```

### 总结

- Lua C API 基于栈模型，所有数据交换通过栈进行。
- C 嵌入 Lua：`luaL_newstate` → `luaL_openlibs` → `luaL_dostring`/`luaL_dofile` → `lua_close`。
- C 函数注册为 Lua 模块：实现 `luaopen_modname`，使用 `luaL_newlib` 注册函数表。
- LuaJIT 的 FFI 提供更简单的 C 交互方式，无需编写 C 代码。
- **注意事项**：C 函数要注意栈平衡（压入和弹出的数量匹配）；使用 `lua_pcall` 而非 `lua_call` 以安全处理错误。

---

## 第 30 讲：Lua 实战应用场景

### 概念

Lua 因其轻量、高效、可嵌入的特性，在众多领域有广泛应用：游戏脚本（World of Warcraft、Roblox、Love2D）、Web 服务器（OpenResty）、数据库扩展（Redis）、网络设备配置（Nginx）等。本讲介绍 Lua 在几个典型场景中的应用。

### 原理

Lua 的设计哲学是"提供简洁高效的机制，将复杂逻辑留给宿主程序"。这使得 Lua 特别适合作为**配置语言**和**扩展脚本**。宿主程序（C/C++ 程序）提供核心功能（如图形渲染、网络 I/O），Lua 负责编写高层逻辑（如游戏逻辑、请求处理）。这种分工使宿主程序保持高性能，同时获得脚本语言的灵活性。

Lua 的成功应用场景共同特点：需要频繁修改的业务逻辑、需要给非程序员（如游戏设计师）提供脚本能力、需要安全隔离的插件系统。Lua 的沙箱机制（通过 `_ENV` 和受限的标准库）使其成为运行不受信任代码的理想选择。

### 例子

**示例 1：游戏脚本（Love2D 框架）**

```lua
-- Love2D 游戏框架中的 Lua 脚本
-- main.lua

local player = {
    x = 100,
    y = 100,
    speed = 200,
    image = nil
}

function love.load()
    player.image = love.graphics.newImage("player.png")
end

function love.update(dt)
    -- 键盘控制
    if love.keyboard.isDown("left") then
        player.x = player.x - player.speed * dt
    end
    if love.keyboard.isDown("right") then
        player.x = player.x + player.speed * dt
    end
    if love.keyboard.isDown("up") then
        player.y = player.y - player.speed * dt
    end
    if love.keyboard.isDown("down") then
        player.y = player.y + player.speed * dt
    end

    -- 边界检查
    player.x = math.max(0, math.min(player.x, love.graphics.getWidth() - player.image:getWidth()))
    player.y = math.max(0, math.min(player.y, love.graphics.getHeight() - player.image:getHeight()))
end

function love.draw()
    love.graphics.draw(player.image, player.x, player.y)
    love.graphics.print("位置: " .. math.floor(player.x) .. ", " .. math.floor(player.y), 10, 10)
end
```

**示例 2：Web 开发（OpenResty）**

```lua
-- OpenResty 中的 Lua 脚本，运行在 Nginx 内部
-- 处理 HTTP 请求

-- 获取请求参数
local args = ngx.req.get_uri_args()
local name = args.name or "World"

-- 返回 JSON 响应
ngx.header.content_type = "application/json"
ngx.say(string.format([[{"message": "Hello, %s!", "time": "%s"}]], name, os.date()))

-- 访问 Redis
local redis = require "resty.redis"
local red = redis:new()
red:set_timeout(1000)  -- 1 秒超时

local ok, err = red:connect("127.0.0.1", 6379)
if not ok then
    ngx.log(ngx.ERR, "Redis 连接失败: ", err)
    return
end

-- 增加访问计数
local count, err = red:incr("visits")
ngx.say(string.format([[{"visits": %d}]], count))

-- 将连接放回连接池
red:set_keepalive(10000, 100)
```

**示例 3：Redis 中的 Lua 脚本**

```lua
-- Redis EVAL 执行 Lua 脚本，保证原子性
-- 实现限流器（每秒最多 100 次请求）

local key = KEYS[1]           -- 限流键
local limit = tonumber(ARGV[1]) -- 限制次数
local window = tonumber(ARGV[2]) -- 时间窗口（秒）

local current = redis.call("GET", key)
if current then
    if tonumber(current) >= limit then
        return 0  -- 被限流
    end
end

-- 增加计数
current = redis.call("INCR", key)
if current == 1 then
    redis.call("EXPIRE", key, window)  -- 第一次设置过期时间
end

return 1  -- 允许访问
```

在 Redis 命令行调用：
```bash
redis-cli EVAL "$(cat ratelimit.lua)" 1 rate:user123 100 1
```

**示例 4：Nginx 配置中的 Lua**

```nginx
# nginx.conf
location /api {
    access_by_lua_block {
        local token = ngx.req.get_headers()["Authorization"]
        if not token then
            ngx.exit(ngx.HTTP_UNAUTHORIZED)
        end

        -- 验证 token（简化示例）
        local expected = "Bearer secret123"
        if token ~= expected then
            ngx.exit(ngx.HTTP_FORBIDDEN)
        end

        -- 记录访问日志
        ngx.log(ngx.INFO, "认证通过: " .. ngx.var.remote_addr)
    }

    content_by_lua_block {
        ngx.say('{"status": "ok"}')
    }
}
```

**示例 5：配置文件解析**

```lua
-- 使用 Lua 作为配置文件格式
-- config.lua
return {
    app = {
        name = "MyApp",
        version = "1.0.0",
        debug = true
    },
    server = {
        host = "0.0.0.0",
        port = 8080,
        workers = 4
    },
    database = {
        driver = "mysql",
        host = "localhost",
        port = 3306,
        name = "mydb",
        user = "root",
        password = "secret"
    },
    log = {
        level = "info",
        file = "/var/log/myapp.log"
    }
}

-- 加载配置
local function loadConfig(path)
    local fn, err = loadfile(path)
    if not fn then
        error("加载配置失败: " .. err)
    end
    return fn()  -- 执行配置文件，返回配置表
end

local config = loadConfig("config.lua")
print("应用名:", config.app.name)
print("服务器:", config.server.host .. ":" .. config.server.port)
print("数据库:", config.database.driver)
```

### 总结

- Lua 广泛应用于游戏脚本、Web 服务器、数据库扩展、网络设备等领域。
- Love2D：使用 Lua 开发 2D 游戏，`love.load`/`update`/`draw` 是核心回调。
- OpenResty：在 Nginx 中嵌入 Lua，实现高性能 Web 应用，支持 Redis 等客户端。
- Redis：使用 Lua 脚本保证操作原子性，常用于限流、计数等场景。
- Lua 作为配置文件格式：比 JSON/TOML 更灵活，支持注释和逻辑运算。
- **核心优势**：轻量嵌入、高性能、安全沙箱、易于学习。

---

## 课程总结

恭喜您完成了 Lua 完整教程！让我们回顾整个学习旅程：

### 知识体系回顾

| 章节 | 核心内容 | 关键知识点 |
|------|---------|-----------|
| 第一章 | 入门基础 | 环境搭建、基本语法、代码风格 |
| 第二章 | 数据类型 | 八大类型、变量作用域、运算符 |
| 第三章 | 控制结构 | if/elseif/else、while/repeat/for、break/return/goto |
| 第四章 | 函数 | 多返回值、可变参数、闭包、高阶函数 |
| 第五章 | 表（Table） | 数组、字典、遍历、表标准库 |
| 第六章 | 字符串 | 字符串操作、模式匹配 |
| 第七章 | 元表与 OOP | 元方法、运算符重载、继承、多态 |
| 第八章 | 协程与错误处理 | 协程、pcall、error、assert |
| 第九章 | 模块与进阶 | 模块系统、弱引用、_ENV、垃圾回收 |
| 第十章 | 实战应用 | C 交互、游戏开发、Web 开发、Redis |

### Lua 的核心设计哲学

1. **简洁**：只有一种复合数据结构（Table），语法精简但表达力强。
2. **可扩展**：通过元表机制，可以模拟 OOP、函数式编程等范式。
3. **可嵌入**：轻量的 C API 使 Lua 易于嵌入宿主程序。
4. **高效**：LuaJIT 的性能接近原生 C 代码。

### 进阶学习建议

- **LuaJIT**：学习 LuaJIT 的 FFI 和 JIT 编译特性，获得接近 C 的性能。
- **OpenResty**：深入学习基于 Nginx + Lua 的高性能 Web 开发。
- **Love2D**：通过游戏开发实践 Lua 的 OOP 和协程。
- **Lua C API**：学习编写 C 扩展模块，深入理解 Lua 内部机制。
- **源码阅读**：阅读 Lua 源码（仅约 2 万行 C 代码），理解解释器实现。

Lua 的简洁设计使其成为学习编程语言实现的绝佳材料。希望本教程能帮助您掌握 Lua，并在实际项目中发挥其强大能力。

---

*本教程基于 Lua 5.3/5.4 语法编写，大部分内容兼容 Lua 5.1+。*
