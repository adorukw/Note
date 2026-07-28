# CMake 系统教程

> 本教程以教科书形式系统讲解 CMake，从基础到高级共 30 讲，分 8 章。每讲包含「概念 / 原理 / 例子 / 总结」四个标准部分，循序渐进，注重实战。

---

## 课程总览

| 项目 | 说明 |
|------|------|
| 预计讲数 | 30 讲（8 章） |
| 学习目标 | 从零基础到独立管理中大型 C/C++ 项目的构建、依赖、测试与发布 |
| 适用人群 | C/C++ 开发者、嵌入式工程师、跨平台项目维护者 |
| 学习路径 | 基础入门 → 核心语法 → 构建管理 → 依赖管理 → 跨平台 → 测试部署 → 现代实践 → 高级主题 |
| 推荐版本 | CMake 3.20+（部分高级特性需要 3.24+） |

---

## 详细章节目录

### 第 1 章: CMake 基础入门 (4 讲)
- 第 1 讲: CMake 是什么与为什么
- 第 2 讲: 安装与环境配置
- 第 3 讲: 第一个 CMake 项目
- 第 4 讲: CMake 构建流程详解

### 第 2 章: CMakeLists.txt 核心语法 (5 讲)
- 第 5 讲: 项目声明 (cmake_minimum_required, project)
- 第 6 讲: 变量与缓存
- 第 7 讲: 构建目标 (add_executable, add_library)
- 第 8 讲: 目标属性 (include_directories, link_libraries)
- 第 9 讲: 条件判断与流程控制

### 第 3 章: 构建目标管理 (4 讲)
- 第 10 讲: 库的类型 (静态/动态/对象/接口)
- 第 11 讲: target 属性详解
- 第 12 讲: 自定义命令与目标
- 第 13 讲: 安装规则 (install)

### 第 4 章: 依赖管理 (4 讲)
- 第 14 讲: find_package 基础
- 第 15 讲: 编写 Config 模块
- 第 16 讲: FetchContent
- 第 17 讲: ExternalProject

### 第 5 章: 跨平台与编译器 (4 讲)
- 第 18 讲: 平台与编译器检测
- 第 19 讲: 编译选项与特性
- 第 20 讲: 生成器表达式
- 第 21 讲: 工具链文件 (Toolchain)

### 第 6 章: 测试与部署 (3 讲)
- 第 22 讲: CTest 测试框架
- 第 23 讲: CPack 打包
- 第 24 讲: CDash 与持续集成

### 第 7 章: 现代 CMake 实践 (3 讲)
- 第 25 讲: 现代 CMake 理念 (target-based)
- 第 26 讲: CMakePresets
- 第 27 讲: 项目组织最佳实践

### 第 8 章: 高级主题 (3 讲)
- 第 28 讲: 函数与宏
- 第 29 讲: CMake 脚本模式
- 第 30 讲: 编写可复用的 CMake 包

---

# 第 1 章: CMake 基础入门

## 第 1 讲: CMake 是什么与为什么

### 概念

CMake 是一个**跨平台的构建系统生成工具**（build system generator）。它本身不直接编译代码，而是根据 `CMakeLists.txt` 文件描述的项目结构，生成对应构建系统（如 Makefile、Ninja、Visual Studio 工程、Xcode 工程）的文件，再由这些构建系统完成实际的编译链接工作。

### 原理

CMake 的核心思想是「**描述而非命令**」。开发者用声明式语言描述项目由哪些源文件组成、依赖哪些库、需要哪些编译选项，CMake 根据平台和编译器自动生成合适的构建脚本。这种「生成器」模式让同一份 `CMakeLists.txt` 可以在 Windows、Linux、macOS 上无缝工作。

CMake 的工作流程分为三个阶段：

1. **配置阶段 (Configure)**：解析 `CMakeLists.txt`，检测编译器、依赖库，缓存变量
2. **生成阶段 (Generate)**：输出具体的 Makefile / 工程文件
3. **构建阶段 (Build)**：调用底层工具 (make / ninja / msbuild) 完成编译链接

这种分离设计让 CMake 既能跨平台，又能利用各平台原生构建工具的性能。

### 例子

最简单的 `CMakeLists.txt`：

```cmake
cmake_minimum_required(VERSION 3.15)
project(Hello)

add_executable(hello hello.c)
```

构建命令：

```bash
mkdir build && cd build
cmake ..           # 配置 + 生成
cmake --build .    # 构建
./hello            # 运行
```

### 总结

- CMake 是构建系统**生成器**，不是构建系统本身
- 核心文件是 `CMakeLists.txt`
- 工作流程：配置 → 生成 → 构建
- 优势：跨平台、支持多种编译器、生态成熟
- **注意事项**：永远在 `build` 目录中构建（out-of-source build），不要污染源码目录

---

## 第 2 讲: 安装与环境配置

### 概念

在使用 CMake 之前，需要先安装 CMake 本身和一个它支持的编译器工具链。CMake 是一个独立的可执行程序，不依赖任何编译器，但生成的构建文件需要调用编译器才能工作。

### 原理

CMake 的运行依赖两个层次：

1. **CMake 程序本身**：用 C++ 编写，提供 `cmake`、`ctest`、`cpack` 三个命令行工具
2. **底层工具链**：编译器 (gcc/clang/msvc)、构建工具 (make/ninja)、调试器等

CMake 通过「生成器 (Generator)」概念桥接这两层。生成器决定了 CMake 输出哪种构建文件，例如 `Unix Makefiles`、`Ninja`、`Visual Studio 17 2022`、`Xcode`。可以用 `cmake -G` 指定，也可以让 CMake 选择默认值。

### 例子

**各平台安装：**

```bash
# Ubuntu / Debian
sudo apt install cmake ninja-build g++

# macOS (Homebrew)
brew install cmake ninja

# Windows (winget)
winget install Kitware.CMake
# 或使用 Visual Studio Installer 勾选 "C++ CMake tools for Windows"
```

**验证安装：**

```bash
cmake --version
cmake --help          # 查看所有命令
cmake --help          # 查看支持的生成器列表
```

**指定生成器：**

```bash
cmake -G "Ninja" ..           # 使用 Ninja
cmake -G "Unix Makefiles" ..  # 使用 Make
```

### 总结

- CMake 与编译器是分离的，需要分别安装
- 生成器决定输出格式，可用 `-G` 指定
- 推荐使用 `Ninja` 作为生成器（比 Make 快很多）
- **注意事项**：Windows 上推荐用 `Visual Studio` 生成器或 `Ninja`；Linux/macOS 推荐 `Ninja` 或 `Unix Makefiles`

---

## 第 3 讲: 第一个 CMake 项目

### 概念

本讲通过一个完整的「Hello World」项目，演示 CMake 项目的目录结构、源文件组织、构建与运行全流程。这是后续所有讲次的基础模板。

### 原理

一个典型的 CMake 项目包含三类文件：

1. **源文件**：`.c` / `.cpp` 等，包含程序逻辑
2. **头文件**：`.h` / `.hpp`，声明接口
3. **CMakeLists.txt**：描述如何构建

CMake 推荐的项目结构是「源码目录 + 构建目录」分离。源码目录保持干净，所有生成的中间文件、可执行文件都放在独立的 `build` 目录中，便于清理（直接删除 `build` 即可）。

### 例子

**项目结构：**

```
hello/
├── CMakeLists.txt
├── src/
│   ├── main.c
│   └── greet.h
└── build/        # 构建时自动创建
```

**`src/main.c`：**

```c
#include <stdio.h>
#include "greet.h"

int main(void) {
    greet("World");
    return 0;
}
```

**`src/greet.h`：**

```c
#ifndef GREET_H
#define GREET_H

void greet(const char* name);

#endif
```

**`src/greet.c`（补全实现）：**

```c
#include <stdio.h>
#include "greet.h"

void greet(const char* name) {
    printf("Hello, %s!\n", name);
}
```

**`CMakeLists.txt`：**

```cmake
cmake_minimum_required(VERSION 3.15)
project(Hello VERSION 1.0 LANGUAGES C)

# 指定 C 标准
set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED ON)

# 添加可执行文件
add_executable(hello
    src/main.c
    src/greet.c
)

# 指定头文件搜索路径
target_include_directories(hello PRIVATE src)
```

**构建与运行：**

```bash
cd hello
cmake -B build -S .          # 配置（-B 指定构建目录，-S 指定源目录）
cmake --build build          # 构建
./build/hello                # 运行
```

### 总结

- 项目结构：源码目录 + 独立 build 目录
- `cmake -B build -S .` 是现代写法，等价于 `mkdir build && cd build && cmake ..`
- `target_include_directories` 用 `PRIVATE` 表示该路径只对当前目标可见
- **注意事项**：不要把 `build` 目录提交到版本控制，应在 `.gitignore` 中忽略

---

## 第 4 讲: CMake 构建流程详解

### 概念

本讲深入讲解 CMake 的「配置 → 生成 → 构建」三阶段，以及每个阶段发生的事情。理解这个流程是排查构建问题的基础。

### 原理

**阶段 1：配置 (Configure)**

CMake 解析 `CMakeLists.txt`，执行其中的命令（`set`、`add_executable` 等），同时进行系统检测（编译器是否存在、依赖库在哪里）。检测结果会写入 `CMakeCache.txt`，这是一个键值对缓存文件，下次配置时可以直接复用，避免重复检测。

**阶段 2：生成 (Generate)**

根据配置阶段收集的信息和选择的生成器，CMake 输出具体的构建文件。例如选择 `Unix Makefiles` 生成器，会输出 `Makefile`、`CMakeFiles/` 目录；选择 `Ninja`，会输出 `build.ninja`。

**阶段 3：构建 (Build)**

调用底层构建工具执行实际的编译链接。CMake 通过 `cmake --build` 命令统一封装了不同工具的调用方式，无需用户记忆 `make`、`ninja`、`msbuild` 的不同参数。

### 例子

**完整流程演示：**

```bash
# 1. 配置（首次或 CMakeLists.txt 改动后）
cmake -B build -S . -DCMAKE_BUILD_TYPE=Release

# 2. 构建
cmake --build build --parallel 8

# 3. 安装（可选）
cmake --install build --prefix /usr/local

# 4. 清理构建（删除整个 build 目录）
rm -rf build
```

**查看配置结果：**

```bash
# 查看缓存变量
cmake -B build -S . -LH          # 列出所有缓存变量及帮助
cmake -B build -S . -LH CMAKE_BUILD_TYPE  # 查看特定变量
```

**重新配置而不重新生成：**

```bash
# 只修改缓存变量，不重新跑完整配置
cmake -B build -S . -DCMAKE_BUILD_TYPE=Debug
```

### 总结

- 三阶段：配置 → 生成 → 构建，每阶段产物不同
- `CMakeCache.txt` 缓存检测结果，加速二次配置
- `cmake --build` 是跨生成器的统一构建命令
- **注意事项**：修改 `CMakeLists.txt` 后只需重新运行 `cmake --build`，CMake 会自动判断是否需要重新配置；但删除源文件或改了大的结构时，建议删除 `build` 目录重新配置

---

# 第 2 章: CMakeLists.txt 核心语法

## 第 5 讲: 项目声明 (cmake_minimum_required, project)

### 概念

每个 `CMakeLists.txt` 的开头必须有两条命令：`cmake_minimum_required` 和 `project`。前者声明最低 CMake 版本，后者声明项目名称和元信息。这是 CMake 项目的「身份证」。

### 原理

**`cmake_minimum_required`** 的作用有两点：

1. **版本兼容**：CMake 不同版本行为有差异，设定最低版本后，CMake 会以该版本的行为模式运行（通过 `cmake_policy` 实现），保证脚本向前兼容
2. **错误提示**：若用户安装的 CMake 版本低于要求，会立即报错而不是产生奇怪的构建问题

**`project`** 命令声明项目元信息，会设置以下变量：

- `PROJECT_NAME`：项目名
- `PROJECT_VERSION`：版本号
- `PROJECT_SOURCE_DIR` / `PROJECT_BINARY_DIR`：源码/构建目录
- `<lang>_COMPILER`：检测到的编译器路径
- `<lang>_FLAGS`：编译器默认选项

`LANGUAGES` 参数指定项目使用的语言（C、CXX、Fortran、ASM 等），CMake 会据此检测对应编译器。

### 例子

**基础形式：**

```cmake
cmake_minimum_required(VERSION 3.15)
project(MyApp)
```

**完整形式：**

```cmake
cmake_minimum_required(VERSION 3.20)

project(MyApp
    VERSION 2.1.0
    DESCRIPTION "A cross-platform image processing library"
    HOMEPAGE_URL "https://example.com/myapp"
    LANGUAGES C CXX
)

# 使用 project 设置的变量
message(STATUS "Project: ${PROJECT_NAME}")
message(STATUS "Version: ${PROJECT_VERSION}")
message(STATUS "Source dir: ${PROJECT_SOURCE_DIR}")
```

**多语言项目：**

```cmake
# C++ 项目需要链接 C 库
project(FastMath LANGUAGES C CXX)

# 纯 C 项目（默认）
project(HelloC LANGUAGES C)

# 不指定 LANGUAGES 时默认启用 C 和 CXX
project(DefaultLangs)
```

### 总结

- `cmake_minimum_required` 必须是第一条命令
- `project` 设置项目元信息和触发编译器检测
- `VERSION` 参数会自动拆分为 `PROJECT_VERSION_MAJOR/MINOR/PATCH`
- **注意事项**：推荐最低版本设为 3.15 以上，以使用现代 CMake 特性；不要用过低版本（如 2.8）以避免老式语法陷阱

---

## 第 6 讲: 变量与缓存

### 概念

CMake 的变量系统有三个层次：**普通变量**、**缓存变量**、**环境变量**。理解它们的区别和作用域是写好 CMake 脚本的关键。

### 原理

**1. 普通变量 (Normal Variable)**

作用域是当前的目录及其子目录（通过 `add_subdirectory` 进入）。父目录的变量在子目录可见，但子目录修改不影响父目录。函数内部有独立作用域，需用 `PARENT_SCOPE` 向上传递。

**2. 缓存变量 (Cache Variable)**

存储在 `CMakeCache.txt` 中，跨多次配置持久化。首次设置后，后续配置不会覆盖（除非用 `FORCE`）。用户可通过 `-D` 命令行参数在配置时覆盖。

**3. 环境变量 (Environment Variable)**

通过 `$ENV{VAR}` 读取，`set(ENV{VAR} value)` 设置，仅在当前 CMake 进程内有效。

### 例子

**普通变量：**

```cmake
set(MY_VAR "hello")
message(${MY_VAR})  # 输出 hello

# 列表
set(MY_LIST a b c)
list(APPEND MY_LIST d)        # 追加元素
list(REMOVE_ITEM MY_LIST b)  # 删除元素
message("${MY_LIST}")        # 输出 a;c;d
```

**缓存变量：**

```cmake
# 声明缓存变量，带类型和帮助文本
set(BUILD_SHARED_LIBS OFF CACHE BOOL "Build shared libraries")

# 命令行覆盖
# cmake -DBUILD_SHARED_LIBS=ON ..
```

**作用域与 PARENT_SCOPE：**

```cmake
function(increment var)
    math(EXPR result "${${var}} + 1")
    set(${var} ${result} PARENT_SCOPE)  # 修改父作用域变量
endfunction()

set(COUNT 5)
increment(COUNT)
message("${COUNT}")  # 输出 6
```

**变量检查：**

```cmake
if(DEFINED MY_VAR)        # 变量是否已定义
    message("MY_VAR is defined")
endif()

if(MY_VAR)                # 变量是否为「真值」(非空、非0、非OFF/NO/FALSE)
    message("MY_VAR is truthy")
endif()
```

### 总结

- 普通变量：目录作用域，子目录可见但修改不向上传播
- 缓存变量：持久化，用户可命令行覆盖，适合做配置开关
- 函数内修改父作用域变量必须用 `PARENT_SCOPE`
- **注意事项**：变量引用 `${VAR}` 是字符串展开，空变量引用会展开为空字符串，可能导致命令语法错误，用引号包裹更安全：`"${MY_VAR}"`

---

## 第 7 讲: 构建目标 (add_executable, add_library)

### 概念

**目标 (Target)** 是 CMake 的核心概念。一个目标代表一个构建产物：可执行文件、静态库、动态库等。`add_executable` 和 `add_library` 是创建目标的两个基本命令。

### 原理

CMake 是「以目标为中心」的构建系统。每个目标有自己的属性（源文件、头文件路径、链接库、编译选项），这些属性通过 `target_*` 系列命令设置。目标之间可以建立依赖关系，CMake 会自动处理构建顺序和链接关系。

目标的源文件可以是：

- 直接列出的 `.c` / `.cpp` 文件
- 通过 `file(GLOB)` 收集的文件（不推荐，CMake 官方建议显式列出）
- 自动生成的文件（如 `.cpp` 由 `.ui` 生成）

### 例子

**可执行文件：**

```cmake
add_executable(myapp
    src/main.cpp
    src/utils.cpp
    src/utils.h
)
```

**静态库：**

```cmake
add_library(mylib STATIC
    src/lib.cpp
    src/lib.h
)
```

**动态库（受 `BUILD_SHARED_LIBS` 控制）：**

```cmake
# 用 STATIC/SHARED/MODULE 显式指定
add_library(mylib SHARED src/lib.cpp)

# 或用全局变量控制（推荐用于库的发布）
set(BUILD_SHARED_LIBS ON CACHE BOOL "")
add_library(mylib src/lib.cpp)  # 跟随 BUILD_SHARED_LIBS
```

**目标间依赖：**

```cmake
add_library(utils STATIC src/utils.cpp)
add_executable(app src/main.cpp)

# app 依赖 utils，CMake 自动处理构建顺序和链接
target_link_libraries(app PRIVATE utils)
```

**对象库（编译产物复用）：**

```cmake
# 对象库：只编译不归档，可被多个目标复用
add_library(common OBJECT src/common.cpp)

add_library(lib_a STATIC src/a.cpp)
target_link_libraries(lib_a PUBLIC common)

add_library(lib_b STATIC src/b.cpp)
target_link_libraries(lib_b PUBLIC common)
```

### 总结

- 目标是 CMake 的核心抽象，所有构建围绕目标展开
- 库类型：`STATIC`、`SHARED`、`MODULE`、`OBJECT`、`INTERFACE`
- 目标间通过 `target_link_libraries` 建立依赖
- **注意事项**：避免用 `file(GLOB)` 自动收集源文件，因为新增/删除文件不会触发重新配置；显式列出源文件更安全

---

## 第 8 讲: 目标属性 (include_directories, link_libraries)

### 概念

目标属性 (Target Properties) 描述一个目标的「使用方式」和「构建方式」。本讲重点讲解三个最常用的属性命令：`target_include_directories`、`target_link_libraries`、`target_compile_options`，以及关键的 `PUBLIC/PRIVATE/INTERFACE` 关键字。

### 原理

`PUBLIC`、`PRIVATE`、`INTERFACE` 是 CMake 现代 API 的核心概念，描述属性的传播范围：

- **PRIVATE**：仅当前目标使用，不传播给依赖者
- **INTERFACE**：仅传播给依赖者，当前目标不使用
- **PUBLIC**：当前目标使用，也传播给依赖者

举例：库 `A` 内部用了 `foo.h`（PRIVATE），对外暴露 `bar.h`（INTERFACE），则链接 `A` 的目标只能看到 `bar.h` 的路径，看不到 `foo.h`。这种设计让接口与实现分离，是现代 CMake 的精髓。

### 例子

**头文件路径：**

```cmake
add_library(mylib src/mylib.cpp)

# PRIVATE: 仅 mylib 自己用
target_include_directories(mylib PRIVATE src/internal)

# PUBLIC: mylib 自己用，链接 mylib 的目标也用
target_include_directories(mylib PUBLIC include)

# INTERFACE: 仅链接者用，mylib 自己不用（典型于 header-only 库）
add_library(header_only INTERFACE)
target_include_directories(header_only INTERFACE include)
```

**链接库：**

```cmake
add_library(engine src/engine.cpp)

# engine 内部用 zlib，但对外不暴露 zlib API
target_link_libraries(engine PRIVATE zlib)

# engine 对外暴露 graphics 的 API（用户调用 engine 时也需要 graphics 头文件）
target_link_libraries(engine PUBLIC graphics)
```

**编译选项：**

```cmake
add_executable(app src/main.cpp)

# 警告选项只对 app 自己生效
target_compile_options(app PRIVATE -Wall -Wextra -Werror)

# C++ 标准对 app 和依赖者都生效
set_target_properties(app PROPERTIES CXX_STANDARD 17 CXX_STANDARD_REQUIRED ON)
```

**完整示例：**

```cmake
add_library(math_lib STATIC
    src/math.cpp
    src/math.h
)

# 内部用到的第三方库，不暴露
target_include_directories(math_lib PRIVATE third_party/boost/include)
target_link_libraries(math_lib PRIVATE boost_math)

# 对外暴露的接口
target_include_directories(math_lib PUBLIC include)
target_compile_features(math_lib PUBLIC cxx_std_17)
```

### 总结

- `PUBLIC/PRIVATE/INTERFACE` 是现代 CMake 的核心，描述属性传播范围
- 库的内部依赖用 `PRIVATE`，对外接口用 `PUBLIC` 或 `INTERFACE`
- 优先用 `target_*` 系列命令，避免用全局的 `include_directories` / `link_libraries`
- **注意事项**：`link_libraries`、`include_directories`（无 `target_` 前缀）是全局命令，会污染所有目标，应避免使用

---

## 第 9 讲: 条件判断与流程控制

### 概念

CMake 提供完整的流程控制语句：`if/elseif/else/endif`、`foreach/endforeach`、`while/endwhile`、`function/endfunction`。本讲聚焦条件判断和循环，函数在第 28 讲详解。

### 原理

CMake 的 `if` 语法与一般语言有差异，需要注意：

1. **变量自动展开**：`if(MY_VAR)` 中 `MY_VAR` 会被展开为它的值再判断
2. **常量判断**：`ON/YES/TRUE/Y` 为真，`OFF/NO/FALSE/N/IGNORE/NOTFOUND/空字符串/0` 为假
3. **比较操作符**：`STREQUAL`（字符串相等）、`EQUAL`（数值相等）、`MATCHES`（正则匹配）、`LESS/GREATER`（数值比较）
4. **文件测试**：`EXISTS`、`IS_DIRECTORY`、`EXISTS`

`foreach` 支持三种形式：列表遍历、范围遍历、并行遍历。

### 例子

**条件判断：**

```cmake
if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    add_definitions(-DDEBUG_MODE)
elseif(CMAKE_BUILD_TYPE STREQUAL "Release")
    add_definitions(-DNDEBUG -O3)
else()
    message(STATUS "Unknown build type")
endif()

# 平台判断
if(WIN32)
    message(STATUS "Building on Windows")
elseif(APPLE)
    message(STATUS "Building on macOS")
elseif(UNIX)
    message(STATUS "Building on Linux/Unix")
endif()

# 数值比较
if(CMAKE_MAJOR_VERSION LESS 3)
    message(FATAL_ERROR "Requires CMake >= 3.0")
endif()

# 文件存在性
if(EXISTS "${CMAKE_SOURCE_DIR}/config.h")
    message(STATUS "Found config.h")
endif()
```

**foreach 循环：**

```cmake
# 列表遍历
set(SOURCES a.cpp b.cpp c.cpp)
foreach(src ${SOURCES})
    message(STATUS "Source: ${src}")
endforeach()

# 范围遍历
foreach(i RANGE 0 5)        # 0,1,2,3,4,5
    message(STATUS "i = ${i}")
endforeach()

# 并行遍历两个列表
set(NAMES alice bob)
set(AGES 25 30)
foreach(name age IN ZIP_LISTS NAMES AGES)
    message(STATUS "${name} is ${age}")
endforeach()
```

**while 循环：**

```cmake
set(COUNT 0)
while(COUNT LESS 5)
    math(EXPR COUNT "${COUNT} + 1")
    message(STATUS "Count: ${COUNT}")
endwhile()
```

### 总结

- `if` 中变量自动展开，注意 `STREQUAL` 用于字符串、`EQUAL` 用于数值
- `foreach` 支持列表、范围、并行三种形式
- 平台判断用 `WIN32`、`APPLE`、`UNIX` 等内置变量
- **注意事项**：`if(MY_VAR STREQUAL "foo")` 中若 `MY_VAR` 未定义会展开为空字符串，可能误判；用 `if("${MY_VAR}" STREQUAL "foo")` 加引号更安全；CMake 3.24+ 推荐用 `if(DEFINED MY_VAR AND MY_VAR STREQUAL "foo")`

---

# 第 3 章: 构建目标管理

## 第 10 讲: 库的类型 (静态/动态/对象/接口)

### 概念

CMake 支持五种库类型：**静态库 (STATIC)**、**动态库 (SHARED)**、**模块库 (MODULE)**、**对象库 (OBJECT)**、**接口库 (INTERFACE)**。每种类型有不同的用途和链接行为，理解它们的差异是设计项目结构的基础。

### 原理

**1. 静态库 (STATIC)**

编译后归档为 `.a` (Unix) 或 `.lib` (Windows)。链接时整个库被嵌入可执行文件，运行时不依赖外部库文件。优点是部署简单，缺点是体积大、更新需重新链接。

**2. 动态库 (SHARED)**

编译为 `.so` (Linux)、`.dylib` (macOS)、`.dll` (Windows)。运行时动态加载，多个程序可共享同一份，节省内存。需要处理运行时搜索路径 (`RPATH`)。

**3. 模块库 (MODULE)**

类似动态库，但设计为运行时通过 `dlopen` / `LoadLibrary` 加载，不参与链接。常用于插件系统。

**4. 对象库 (OBJECT)**

只编译不归档，编译产物（`.o` 文件）可被多个目标复用，避免源文件被多次编译。适合多个库共享同一份源码的场景。

**5. 接口库 (INTERFACE)**

没有实际构建产物，仅用于传递属性（头文件路径、编译选项、链接库）。典型用于 header-only 库。

### 例子

**静态库与动态库切换：**

```cmake
# 通过缓存变量控制
option(BUILD_SHARED_LIBS "Build shared libraries" ON)

# 不指定类型时，跟随 BUILD_SHARED_LIBS
add_library(mylib src/mylib.cpp)

# 显式指定
add_library(mylib_static STATIC src/mylib.cpp)
add_library(mylib_shared SHARED src/mylib.cpp)
```

**对象库复用源码：**

```cmake
# 公共源码编译一次
add_library(common_obj OBJECT
    src/common.cpp
    src/logger.cpp
)

# 多个库复用 common_obj 的编译产物
add_library(lib_a STATIC src/a.cpp)
target_link_libraries(lib_a PUBLIC common_obj)

add_library(lib_b STATIC src/b.cpp)
target_link_libraries(lib_b PUBLIC common_obj)
```

**接口库（header-only）：**

```cmake
add_library(catch2 INTERFACE)
target_include_directories(catch2 INTERFACE include)
target_compile_features(catch2 INTERFACE cxx_std_17)

# 使用时无需链接实际库
add_executable(tests test.cpp)
target_link_libraries(tests PRIVATE catch2)
```

**模块库（插件）：**

```cmake
# 插件作为 MODULE 库，运行时 dlopen 加载
add_library(my_plugin MODULE src/plugin.cpp)
set_target_properties(my_plugin PROPERTIES PREFIX "")
# 输出 my_plugin.so / my_plugin.dll
```

### 总结

- 静态库：部署简单，体积大
- 动态库：节省内存，需处理 RPATH
- 对象库：复用编译产物，避免重复编译
- 接口库：header-only 库的标准做法
- **注意事项**：动态库在 Windows 上需要导出符号（`__declspec(dllexport)`），可用 CMake 的 `GenerateExportHeader` 模块自动生成

---

## 第 11 讲: target 属性详解

### 概念

每个 target 都有一组属性 (Properties)，描述其构建行为。除了前几讲介绍的 `INCLUDE_DIRECTORIES`、`LINK_LIBRARIES`、`COMPILE_OPTIONS`，还有大量属性控制输出名、版本、位置、特性等。本讲系统介绍常用属性。

### 原理

CMake 的 target 属性可以通过两种方式设置：

1. **专用命令**：`target_include_directories`、`target_compile_features` 等，语义清晰
2. **通用命令**：`set_target_properties` / `set_property`，可设置任意属性

属性分为两类：

- **构建属性**：影响如何编译链接（`CXX_STANDARD`、`POSITION_INDEPENDENT_CODE`）
- **输出属性**：影响产物名和位置（`OUTPUT_NAME`、`ARCHIVE_OUTPUT_DIRECTORY`）

### 例子

**输出控制：**

```cmake
add_library(mylib SHARED src/mylib.cpp)

# 输出文件名（默认是 target 名）
set_target_properties(mylib PROPERTIES OUTPUT_NAME "mylib")

# 版本号（仅 SHARED/MODULE 有效，生成 libmylib.so.1.2.3）
set_target_properties(mylib PROPERTIES
    VERSION 1.2.3       # 实际版本
    SOVERSION 1          # ABI 版本（生成 libmylib.so.1 软链接）
)

# 输出目录
set_target_properties(mylib PROPERTIES
    LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib
    ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib
    RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin
)
```

**编译特性：**

```cmake
add_executable(app src/main.cpp)

# C++ 标准（推荐用 target_compile_features）
target_compile_features(app PRIVATE cxx_std_17)

# 或用属性
set_target_properties(app PROPERTIES
    CXX_STANDARD 17
    CXX_STANDARD_REQUIRED ON       # 不允许编译器降级
    CXX_EXTENSIONS OFF             # 禁用编译器扩展（如 GNU 扩展）
)
```

**位置无关代码 (PIC)：**

```cmake
# 动态库默认开启 PIC，静态库默认关闭
# 若静态库要被链接进动态库，需开启 PIC
add_library(mylib STATIC src/mylib.cpp)
set_target_properties(mylib PROPERTIES POSITION_INDEPENDENT_CODE ON)
```

**文件夹分组（IDE 中显示）：**

```cmake
set_property(GLOBAL PROPERTY USE_FOLDERS ON)

add_library(core src/core.cpp)
set_target_properties(core PROPERTIES FOLDER "Engine/Core")

add_library(renderer src/renderer.cpp)
set_target_properties(renderer PROPERTIES FOLDER "Engine/Renderer")
```

### 总结

- 用 `set_target_properties` 一次设置多个属性
- `VERSION/SOVERSION` 控制动态库版本符号链接
- `CXX_STANDARD` + `CXX_STANDARD_REQUIRED` + `CXX_EXTENSIONS` 控制 C++ 标准
- **注意事项**：`OUTPUT_NAME` 改变文件名但不改变 target 名，CMake 内部引用仍用 target 名

---

## 第 12 讲: 自定义命令与目标

### 概念

`add_custom_command` 和 `add_custom_target` 让 CMake 能执行任意命令，用于代码生成、预处理、后处理等场景。两者区别：前者依附于某个目标或输出文件，后者是独立的目标。

### 原理

**add_custom_command 的两种模式：**

1. **OUTPUT 模式**：声明生成某个文件的命令，其他目标把该文件作为源文件时自动触发
2. **TARGET 模式**：在某个目标的构建前/后执行命令（`PRE_BUILD`、`PRE_LINK`、`POST_BUILD`）

**add_custom_target**：创建一个独立的伪目标，执行指定命令。不输出文件，常用于文档生成、清理任务等。

### 例子

**代码生成（OUTPUT 模式）：**

```cmake
# 用 protoc 生成 .pb.cc / .pb.h
find_package(Protobuf REQUIRED)

add_custom_command(
    OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/message.pb.cc
           ${CMAKE_CURRENT_BINARY_DIR}/message.pb.h
    COMMAND ${Protobuf_PROTOC_EXECUTABLE}
            --cpp_out=${CMAKE_CURRENT_BINARY_DIR}
            --proto_path=${CMAKE_CURRENT_SOURCE_DIR}/proto
            ${CMAKE_CURRENT_SOURCE_DIR}/proto/message.proto
    DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/proto/message.proto
    COMMENT "Generating protobuf sources"
)

# 把生成的文件加入目标
add_library(mymsg ${CMAKE_CURRENT_BINARY_DIR}/message.pb.cc)
target_include_directories(mymsg PUBLIC ${CMAKE_CURRENT_BINARY_DIR})
```

**POST_BUILD 后处理：**

```cmake
add_executable(app src/main.cpp)

# 构建后复制到固定位置
add_custom_command(TARGET app POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy $<TARGET_FILE:app> /opt/myapp/bin
    COMMENT "Installing app to /opt/myapp/bin"
)
```

**自定义目标：**

```cmake
# 文档生成目标
add_custom_target(docs
    COMMAND doxygen Doxyfile
    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
    COMMENT "Generating API documentation"
)

# 清理测试数据
add_custom_target(clean_tests
    COMMAND ${CMAKE_COMMAND} -E remove_directory ${CMAKE_BINARY_DIR}/test_data
    COMMENT "Cleaning test data"
)
```

**依赖关系：**

```cmake
# 让 all 依赖 docs（每次构建都生成文档）
add_custom_target(docs ALL
    COMMAND doxygen Doxyfile
    COMMENT "Generating docs"
)

# 让某个目标依赖自定义命令的输出
add_custom_target(gen_data COMMAND generate_data.py)
add_executable(app src/main.cpp)
add_dependencies(app gen_data)  # app 构建前先跑 gen_data
```

### 总结

- `add_custom_command(OUTPUT ...)` 用于代码生成，自动按需触发
- `add_custom_command(TARGET ... POST_BUILD)` 用于构建后处理
- `add_custom_target` 创建独立伪目标
- **注意事项**：`add_custom_command(OUTPUT ...)` 必须被某个目标引用（作为源文件或依赖），否则不会执行

---

## 第 13 讲: 安装规则 (install)

### 概念

`install` 命令定义项目安装到系统时的规则：哪些文件复制到哪里、什么权限。配合 `cmake --install` 命令完成实际安装。这是项目发布给用户的关键步骤。

### 原理

安装规则在配置阶段声明，在执行 `cmake --install` 时生效。安装分多种类型：

- `TARGETS`：安装构建产物（可执行文件、库）
- `FILES` / `DIRECTORY`：安装文件或目录
- `EXPORT`：安装 target 配置（供下游 `find_package` 使用）

安装位置由 `CMAKE_INSTALL_PREFIX` 控制（默认 `/usr/local` on Unix，`C:/Program Files` on Windows）。每种文件类型有默认子目录（`bin`、`lib`、`include`），可通过 `DESTINATION` 覆盖。

### 例子

**安装目标：**

```cmake
add_library(mylib SHARED src/mylib.cpp)
add_executable(myapp src/main.cpp)

install(TARGETS mylib myapp
    RUNTIME DESTINATION bin            # 可执行文件 → prefix/bin
    LIBRARY DESTINATION lib           # 动态库 → prefix/lib
    ARCHIVE DESTINATION lib           # 静态库 → prefix/lib
    INCLUDES DESTINATION include      # 头文件路径（不实际复制，仅记录）
)

# 安装头文件
install(FILES include/mylib.h DESTINATION include)
install(DIRECTORY include/mylib/ DESTINATION include/mylib)
```

**安装配置（供下游 find_package）：**

```cmake
include(CMakePackageConfigHelpers)

# 生成 mylib-config.cmake
configure_package_config_file(
    cmake/mylib-config.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/mylib-config.cmake
    INSTALL_DESTINATION lib/cmake/mylib
)

# 生成版本文件
write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/mylib-config-version.cmake
    VERSION 1.0.0
    COMPATIBILITY SameMajorVersion
)

install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/mylib-config.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/mylib-config-version.cmake
    DESTINATION lib/cmake/mylib
)

# 导出 target
install(EXPORT mylibTargets
    FILE mylib-targets.cmake
    NAMESPACE MyLib::
    DESTINATION lib/cmake/mylib
)
```

**安装命令：**

```bash
cmake -B build -S . -DCMAKE_INSTALL_PREFIX=/opt/mylib
cmake --build build
cmake --install build
# 或指定组件
cmake --install build --component runtime
```

### 总结

- `install(TARGETS ...)` 区分 RUNTIME/LIBRARY/ARCHIVE 三种产物
- `CMAKE_INSTALL_PREFIX` 控制安装根目录
- 配合 `EXPORT` 和 config 文件，可让下游 `find_package` 找到你的库
- **注意事项**：安装路径用相对路径（如 `bin`、`lib`），不要硬编码绝对路径；用 `GNUInstallDirs` 模块获取平台标准路径

---

# 第 4 章: 依赖管理

## 第 14 讲: find_package 基础

### 概念

`find_package` 是 CMake 查找外部依赖的标准命令。它能找到系统安装的库（如 zlib、OpenSSL），并设置变量供项目使用。理解其工作原理是处理第三方依赖的基础。

### 原理

`find_package` 有两种查找模式：

**1. Module 模式**

查找 `Find<Package>.cmake` 文件（通常在 CMake 自带的 `Modules/` 目录或项目 `cmake/` 目录）。这种文件由项目作者编写，调用 `find_path`、`find_library` 等底层命令查找库，设置 `<Package>_FOUND`、`<Package>_INCLUDE_DIRS`、`<Package>_LIBRARIES` 变量。

**2. Config 模式**

查找 `<package>-config.cmake` 或 `<package>Config.cmake` 文件（由库的作者在安装时生成）。这种文件直接提供 imported target，使用更方便。

CMake 默认先尝试 Module 模式，失败后尝试 Config 模式。可用 `MODULE`、`CONFIG`、`NO_MODULE` 关键字强制指定。

### 例子

**基础用法：**

```cmake
# 必须找到
find_package(ZLIB REQUIRED)

# 可选
find_package(OpenSSL)

if(OpenSSL_FOUND)
    target_compile_definitions(app PRIVATE HAVE_OPENSSL)
    target_link_libraries(app PRIVATE OpenSSL::SSL)
endif()
```

**指定版本和组件：**

```cmake
# 要求版本 >= 1.2.11
find_package(ZLIB 1.2.11 REQUIRED)

# 要求组件
find_package(Boost 1.70 REQUIRED COMPONENTS filesystem system)
find_package(Qt5 5.15 REQUIRED COMPONENTS Core Widgets Network)
```

**两种模式的结果差异：**

```cmake
# Module 模式（老式 FindBoost）：设置变量
find_package(Boost REQUIRED COMPONENTS filesystem)
target_include_directories(app PRIVATE ${Boost_INCLUDE_DIRS})
target_link_libraries(app PRIVATE ${Boost_LIBRARIES})

# Config 模式（新式 Boost）：提供 imported target
find_package(Boost 1.70 REQUIRED COMPONENTS filesystem)
target_link_libraries(app PRIVATE Boost::filesystem)
# 头文件路径自动包含，无需 target_include_directories
```

**指定查找路径：**

```cmake
# 用户从命令行传入
# cmake -DZLIB_ROOT=/opt/zlib ..

find_package(ZLIB REQUIRED PATHS /opt/zlib)

# 或设置环境变量
# set(ENV{ZLIB_ROOT} /opt/zlib)
```

### 总结

- `find_package` 有 Module 和 Config 两种模式，优先 Module
- 现代库提供 imported target（如 `Boost::filesystem`），优先使用
- `REQUIRED` 表示找不到就报错，否则设置 `<Package>_FOUND`
- **注意事项**：老式 `Find<X>.cmake` 只设置变量，新式 Config 模式提供 target，更推荐后者

---

## 第 15 讲: 编写 Config 模块

### 概念

当你的库要被其他项目通过 `find_package` 使用时，需要提供 Config 模块文件。本讲讲解如何为自己的库编写 `<package>-config.cmake` 和版本文件，让下游项目能像使用系统库一样使用你的库。

### 原理

一个完整的 Config 包包含三个文件：

1. **`<package>-config.cmake`**：主配置文件，定义 imported target
2. **`<package>-config-version.cmake`**：版本兼容性检查
3. **`<package>-targets.cmake`**：由 `export/install(EXPORT)` 自动生成，包含 imported target 定义

CMake 提供 `CMakePackageConfigHelpers` 模块简化生成过程。`configure_package_config_file` 处理路径变量，`write_basic_package_version_file` 处理版本兼容性。

### 例子

**项目结构：**

```
mylib/
├── CMakeLists.txt
├── include/mylib/mylib.h
├── src/mylib.cpp
└── cmake/
    └── mylib-config.cmake.in
```

**`cmake/mylib-config.cmake.in`：**

```cmake
@PACKAGE_INIT@

include("${CMAKE_CURRENT_LIST_DIR}/mylib-targets.cmake")

# 提供便捷变量
set(MYLIB_VERSION @PROJECT_VERSION@)

# 检查依赖
include(CMakeFindDependencyMacro)
find_dependency(ZLIB)

# 引入 targets（已由 mylib-targets.cmake 完成）

check_required_components(mylib)
```

**`CMakeLists.txt`：**

```cmake
cmake_minimum_required(VERSION 3.15)
project(mylib VERSION 1.0.0 LANGUAGES CXX)

add_library(mylib SHARED src/mylib.cpp)
target_include_directories(mylib PUBLIC include)
target_link_libraries(mylib PUBLIC ZLIB::ZLIB)

# 生成 config 文件
include(CMakePackageConfigHelpers)

configure_package_config_file(
    cmake/mylib-config.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/mylib-config.cmake
    INSTALL_DESTINATION lib/cmake/mylib
)

write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/mylib-config-version.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY SameMajorVersion
)

# 安装 target 和 config
install(TARGETS mylib
    EXPORT mylibTargets
    LIBRARY DESTINATION lib
    INCLUDES DESTINATION include
)

install(EXPORT mylibTargets
    FILE mylib-targets.cmake
    NAMESPACE MyLib::
    DESTINATION lib/cmake/mylib
)

install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/mylib-config.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/mylib-config-version.cmake
    DESTINATION lib/cmake/mylib
)
```

**下游使用：**

```cmake
# 安装后，下游项目可以这样使用
find_package(mylib 1.0 REQUIRED)
add_executable(app main.cpp)
target_link_libraries(app PRIVATE MyLib::mylib)
```

### 总结

- Config 包由三个文件组成：config、config-version、targets
- `@PACKAGE_INIT@` 注入路径修正宏，确保安装后路径正确
- `find_dependency` 处理依赖，比 `find_package` 更适合在 config 文件中使用
- **注意事项**：`COMPATIBILITY SameMajorVersion` 表示主版本号相同即兼容，是常用选择

---

## 第 16 讲: FetchContent

### 概念

`FetchContent` 是 CMake 3.11+ 引入的依赖管理机制，可在配置时从 Git 仓库或 URL 下载第三方源码并集成到当前项目。相比 `find_package`，它不需要预先安装依赖；相比 `ExternalProject`，它更简单且支持 IDE 中查看依赖源码。

### 原理

`FetchContent` 的工作流程：

1. **Declare**：声明依赖的 Git 仓库、版本、本地缓存目录
2. **MakeAvailable**：触发下载和 `add_subdirectory`，依赖的 target 进入当前项目
3. **使用**：直接 `target_link_libraries(app PRIVATE <dep_target>)`

下载的源码缓存在 `~/.cache/CMake/` 或 build 目录下的 `_deps/` 中。首次配置时下载，后续配置直接复用缓存。

### 例子

**基础用法：**

```cmake
include(FetchContent)

FetchContent_Declare(
    fmt
    GIT_REPOSITORY https://github.com/fmtlib/fmt.git
    GIT_TAG        10.1.1
)

FetchContent_MakeAvailable(fmt)

# 直接使用 fmt 的 target
add_executable(app src/main.cpp)
target_link_libraries(app PRIVATE fmt::fmt)
```

**多个依赖：**

```cmake
include(FetchContent)

FetchContent_Declare(
    Catch2
    GIT_REPOSITORY https://github.com/catchorg/Catch2.git
    GIT_TAG        v3.4.0
)

FetchContent_Declare(
    nlohmann_json
    GIT_REPOSITORY https://github.com/nlohmann/json.git
    GIT_TAG        v3.11.2
)

# 一次性下载所有
FetchContent_MakeAvailable(Catch2 nlohmann_json)

add_executable(tests test_main.cpp)
target_link_libraries(tests PRIVATE Catch2::Catch2 nlohmann_json::nlohmann_json)
```

**指定本地源码（开发期）：**

```cmake
# 优先用本地路径，便于开发调试
FetchContent_Declare(
    mylib
    SOURCE_DIR /path/to/local/mylib
)

# 或用 GIT_REPOSITORY 指向本地 git
FetchContent_Declare(
    mylib
    GIT_REPOSITORY /home/user/projects/mylib
    GIT_TAG        main
)
```

**从 URL 下载压缩包：**

```cmake
FetchContent_Declare(
    googletest
    URL https://github.com/google/googletest/archive/refs/tags/v1.13.0.tar.gz
    URL_HASH SHA256=...  # 推荐校验
)

FetchContent_MakeAvailable(googletest)
```

### 总结

- `FetchContent` 是现代 CMake 管理第三方源码依赖的首选
- `FetchContent_Declare` + `FetchContent_MakeAvailable` 两步走
- 下载内容缓存在 `_deps/`，二次配置不重复下载
- **注意事项**：`FetchContent_MakeAvailable` 是 3.14+ 的便捷接口，旧版需用 `FetchContent_Populate` + 手动 `add_subdirectory`；建议加 `URL_HASH` 校验下载内容

---

## 第 17 讲: ExternalProject

### 概念

`ExternalProject_Add` 是 CMake 管理外部项目的「重型」工具，能在构建时下载、配置、编译、安装外部项目。与 `FetchContent` 不同，外部项目在独立的 CMake 进程中构建，不共享当前项目的 target。

### 原理

`ExternalProject_Add` 定义一个完整的外部项目构建流程，包含若干阶段：

1. **DOWNLOAD**：从 Git/URL 下载源码
2. **UPDATE**：每次构建时更新（可选）
3. **PATCH**：打补丁
4. **CONFIGURE**：调用外部项目的配置脚本
5. **BUILD**：编译
6. **INSTALL**：安装到指定位置
7. **TEST**：运行测试（可选）

每个阶段都是独立的命令，外部项目有自己的 build 目录和 install 目录，与主项目隔离。这种隔离适合超大型依赖或非 CMake 项目。

### 例子

**基础用法：**

```cmake
include(ExternalProject)

ExternalProject_Add(zlib_ext
    GIT_REPOSITORY https://github.com/madler/zlib.git
    GIT_TAG v1.3
    CMAKE_ARGS
        -DCMAKE_INSTALL_PREFIX=${CMAKE_BINARY_DIR}/zlib_install
        -DCMAKE_BUILD_TYPE=Release
    # 默认会执行 configure/build/install
)

# 主项目依赖 zlib_ext 的安装产物
add_executable(app src/main.cpp)
target_include_directories(app PRIVATE ${CMAKE_BINARY_DIR}/zlib_install/include)
target_link_libraries(app PRIVATE ${CMAKE_BINARY_DIR}/zlib_install/lib/libz.a)
add_dependencies(app zlib_ext)  # 确保 zlib 先构建
```

**非 CMake 项目（如 autoconf）：**

```cmake
ExternalProject_Add(libfoo
    URL https://example.com/libfoo-1.0.tar.gz
    CONFIGURE_COMMAND ./configure --prefix=<INSTALL_DIR>
    BUILD_COMMAND make
    INSTALL_COMMAND make install
    BUILD_IN_SOURCE TRUE
)
```

**自定义阶段命令：**

```cmake
ExternalProject_Add(custom_dep
    SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/third_party/custom
    DOWNLOAD_COMMAND ""           # 不下载，用本地源码
    UPDATE_COMMAND ""             # 不更新
    PATCH_COMMAND git apply ${CMAKE_CURRENT_SOURCE_DIR}/patches/fix.patch
    CONFIGURE_COMMAND cmake -S <SOURCE_DIR> -B <BINARY_DIR>
    BUILD_COMMAND cmake --build <BINARY_DIR>
    INSTALL_COMMAND cmake --install <BINARY_DIR>
)
```

**FetchContent vs ExternalProject 对比：**

| 特性 | FetchContent | ExternalProject |
|------|--------------|-----------------|
| 集成方式 | 同一 CMake 进程，共享 target | 独立进程，仅 install 产物 |
| 配置时机 | 配置阶段 | 构建阶段 |
| IDE 可见性 | 源码可见，可调试 | 源码不可见 |
| 适用场景 | CMake 项目，需深度集成 | 非 CMake 项目，或需隔离 |

### 总结

- `ExternalProject_Add` 适合非 CMake 项目或需隔离的依赖
- 每个阶段可自定义命令，灵活度高
- 与 `FetchContent` 互补，按场景选择
- **注意事项**：`ExternalProject` 在构建阶段才执行，配置阶段无法引用其 target，需用 install 产物路径；这是与 `FetchContent` 的关键差异

---

# 第 5 章: 跨平台与编译器

## 第 18 讲: 平台与编译器检测

### 概念

跨平台项目需要根据操作系统、CPU 架构、编译器类型采取不同的处理。CMake 提供一组内置变量和检测命令，让脚本能在配置时识别环境并作出相应处理。

### 原理

CMake 在配置阶段自动检测环境，设置一系列变量：

**平台变量：**
- `WIN32` / `UNIX` / `APPLE`：操作系统类型（布尔值）
- `CMAKE_HOST_SYSTEM_NAME`：操作系统名（Linux、Windows、Darwin）
- `CMAKE_SYSTEM_PROCESSOR`：CPU 架构（x86_64、arm64）

**编译器变量：**
- `CMAKE_C_COMPILER_ID` / `CMAKE_CXX_COMPILER_ID`：编译器标识（GNU、Clang、MSVC、AppleClang）
- `CMAKE_C_COMPILER_VERSION` / `CMAKE_CXX_COMPILER_VERSION`：编译器版本
- `MSVC`、`MINGW`、`CYGWIN`：特定环境标志

**架构变量：**
- `CMAKE_SIZEOF_VOID_P`：指针大小（8 表示 64 位，4 表示 32 位）
- `CMAKE_LIBRARY_ARCHITECTURE`：多架构标识（如 x86_64-linux-gnu）

### 例子

**平台判断：**

```cmake
if(WIN32)
    if(CMAKE_SIZEOF_VOID_P EQUAL 8)
        message(STATUS "Windows 64-bit")
    else()
        message(STATUS "Windows 32-bit")
    endif()
elseif(APPLE)
    message(STATUS "macOS")
elseif(UNIX AND NOT APPLE)
    message(STATUS "Linux/Unix")
endif()
```

**编译器判断：**

```cmake
if(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    target_compile_options(app PRIVATE -Wall -Wextra)
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
    target_compile_options(app PRIVATE /W4 /permissive-)
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
    target_compile_options(app PRIVATE -Wall -Wextra -Wpedantic)
endif()

# 编译器版本检查
if(CMAKE_CXX_COMPILER_ID STREQUAL "GNU" AND CMAKE_CXX_COMPILER_VERSION VERSION_LESS 9.0)
    message(FATAL_ERROR "GCC >= 9.0 required")
endif()
```

**架构判断：**

```cmake
if(CMAKE_SYSTEM_PROCESSOR MATCHES "x86_64|amd64|AMD64")
    target_compile_definitions(app PRIVATE ARCH_X86_64)
elseif(CMAKE_SYSTEM_PROCESSOR MATCHES "aarch64|arm64")
    target_compile_definitions(app PRIVATE ARCH_ARM64)
endif()

# 32/64 位
if(CMAKE_SIZEOF_VOID_P EQUAL 8)
    target_compile_definitions(app PRIVATE BIT_64)
else()
    target_compile_definitions(app PRIVATE BIT_32)
endif()
```

**运行时检测（try_compile）：**

```cmake
include(CheckCXXSourceCompiles)

check_cxx_source_compiles("
    #include <atomic>
    int main() { std::atomic<int> a; return 0; }
" HAVE_ATOMIC)

if(HAVE_ATOMIC)
    target_compile_definitions(app PRIVATE HAVE_STD_ATOMIC)
endif()
```

### 总结

- 平台用 `WIN32/UNIX/APPLE`，编译器用 `CMAKE_<LANG>_COMPILER_ID`
- 架构用 `CMAKE_SYSTEM_PROCESSOR` 和 `CMAKE_SIZEOF_VOID_P`
- `try_compile` / `check_*` 系列命令做能力检测
- **注意事项**：避免过度依赖平台判断，优先用 `target_compile_features` 让 CMake 自动处理编译器差异

---

## 第 19 讲: 编译选项与特性

### 概念

CMake 提供多种方式控制编译选项：`target_compile_options`（编译器标志）、`target_compile_definitions`（预处理宏）、`target_compile_features`（语言特性）。本讲讲解三者的区别和最佳实践。

### 原理

**1. target_compile_options**

直接传递编译器标志（如 `-Wall`、`/O2`）。需要考虑跨编译器兼容性，因为 GCC 和 MSVC 的标志完全不同。建议配合生成器表达式按编译器分别传递。

**2. target_compile_definitions**

定义预处理宏（`-DXXX`）。跨编译器一致，是设置编译期开关的首选方式。

**3. target_compile_features**

声明目标需要的 C++ 语言特性（如 `cxx_std_17`、`cxx_variadic_templates`）。CMake 会自动添加正确的编译器标志，是现代 CMake 推荐的方式。

### 例子

**编译选项（跨编译器）：**

```cmake
add_library(mylib src/mylib.cpp)

# 用生成器表达式按编译器分别处理
target_compile_options(mylib PRIVATE
    $<$<CXX_COMPILER_ID:GNU,Clang>:-Wall -Wextra -Wpedantic>
    $<$<CXX_COMPILER_ID:MSVC>:/W4 /permissive- /utf-8>
)

# 调试 vs 发布
target_compile_options(mylib PRIVATE
    $<$<CONFIG:Debug>:-O0 -g3>
    $<$<CONFIG:Release>:-O3 -DNDEBUG>
)
```

**预处理宏：**

```cmake
add_executable(app src/main.cpp)

# 版本号
target_compile_definitions(app PRIVATE
    APP_VERSION="${PROJECT_VERSION}"
    APP_MAJOR=${PROJECT_VERSION_MAJOR}
)

# 平台相关宏
if(WIN32)
    target_compile_definitions(app PRIVATE PLATFORM_WINDOWS)
elseif(APPLE)
    target_compile_definitions(app PRIVATE PLATFORM_MACOS)
endif()

# 配置相关
target_compile_definitions(app PRIVATE
    $<$<CONFIG:Debug>:DEBUG_MODE>
    $<$<CONFIG:Release>:NDEBUG>
)
```

**编译特性（推荐）：**

```cmake
add_library(mylib src/mylib.cpp)

# 要求 C++17
target_compile_features(mylib PUBLIC cxx_std_17)

# 或要求特定特性（更细粒度）
target_compile_features(mylib PRIVATE
    cxx_variadic_templates
    cxx_generic_lambdas
    cxx_constexpr
)
```

**全局编译选项（不推荐，但有时需要）：**

```cmake
# 影响所有 target（不推荐，破坏封装）
add_compile_options(-Wall -Wextra)
add_compile_definitions(VERSION=1.0)

# 或针对特定语言
add_compile_options($<$<COMPILE_LANGUAGE:CXX>:-std=c++17>)
```

### 总结

- `target_compile_features` 是设置 C++ 标准的现代方式，优先使用
- `target_compile_definitions` 设置预处理宏，跨编译器一致
- `target_compile_options` 设置编译器标志，需考虑跨编译器
- **注意事项**：避免用 `add_definitions`、`add_compile_options`（无 `target_` 前缀）的全局命令，会污染所有目标

---

## 第 20 讲: 生成器表达式

### 概念

生成器表达式 (Generator Expression) 是 CMake 中用于在生成阶段（而非配置阶段）求值的特殊表达式，形式为 `$<...>`。它让 target 属性可以根据配置、平台、编译器等条件动态变化，是现代 CMake 的强大工具。

### 原理

普通变量在配置阶段求值，但配置阶段不知道最终用哪个构建配置（Debug/Release）。生成器表达式在生成阶段求值，此时所有信息都已确定，能精确控制每个构建的属性。

常见生成器表达式：

- `$<CONFIG:Debug>`：当前构建配置是否为 Debug
- `$<PLATFORM_ID:Windows>`：当前平台是否为 Windows
- `$<CXX_COMPILER_ID:GNU>`：当前 C++ 编译器是否为 GCC
- `$<TARGET_FILE:mylib>`：mylib 的输出文件路径
- `$<TARGET_PROPERTY:mylib,INCLUDE_DIRECTORIES>`：mylib 的头文件路径
- `$<BOOL:value>`：将 value 转为布尔值
- `$<IF:cond,true,false>`：条件表达式

### 例子

**按配置区分编译选项：**

```cmake
target_compile_options(app PRIVATE
    $<$<CONFIG:Debug>:-g -O0>
    $<$<CONFIG:Release>:-O3 -DNDEBUG>
)

# 等价于
target_compile_options(app PRIVATE
    $<IF:$<CONFIG:Debug>,-g -O0,-O3 -DNDEBUG>
)
```

**按编译器区分：**

```cmake
target_compile_options(app PRIVATE
    $<$<CXX_COMPILER_ID:GNU,Clang>:-Wall -Wextra>
    $<$<CXX_COMPILER_ID:MSVC>:/W4>
)
```

**按平台区分链接库：**

```cmake
target_link_libraries(app PRIVATE
    $<$<PLATFORM_ID:Windows>:ws2_32>
    $<$<PLATFORM_ID:Linux>:pthread>
    $<$<PLATFORM_ID:Darwin>:iconv>
)
```

**目标文件路径（构建后处理）：**

```cmake
add_custom_command(TARGET app POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy
        $<TARGET_FILE:app>
        ${CMAKE_BINARY_DIR}/dist/
    COMMAND ${CMAKE_COMMAND} -E copy
        $<TARGET_FILE:mylib>
        ${CMAKE_BINARY_DIR}/dist/
)
```

**条件定义宏：**

```cmake
# 仅在 Debug 配置下定义 DEBUG_MODE
target_compile_definitions(app PRIVATE
    $<$<CONFIG:Debug>:DEBUG_MODE>
)

# 仅在 Windows + 64 位下定义 WIN64
target_compile_definitions(app PRIVATE
    $<$<AND:$<PLATFORM_ID:Windows>,$<BOOL:${CMAKE_SIZEOF_VOID_P EQUAL 8}>>:WIN64>
)
```

**接口属性传递：**

```cmake
# 库对外暴露的接口随配置变化
target_include_directories(mylib INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
)
# 构建时用源码路径，安装后用相对路径
```

### 总结

- 生成器表达式在生成阶段求值，能感知最终配置
- `$<...>` 形式，可嵌套组合
- `BUILD_INTERFACE` / `INSTALL_INTERFACE` 区分构建期和安装期路径
- **注意事项**：生成器表达式不能用于 `message()` 等配置阶段命令；调试时可用 `file(GENERATE ...)` 输出展开结果

---

## 第 21 讲: 工具链文件 (Toolchain)

### 概念

工具链文件 (Toolchain File) 是 CMake 用于交叉编译的核心机制。它是一个 `.cmake` 文件，预先设置目标平台的编译器、库路径、系统信息，让 CMake 在配置时知道要为哪个平台生成代码。

### 原理

交叉编译指在 A 平台编译 B 平台的程序（如在 x86 Linux 上编译 ARM 程序）。CMake 默认检测主机环境，但通过 `-DCMAKE_TOOLCHAIN_FILE=<file>` 指定工具链文件后，会使用文件中预设的编译器和系统信息。

工具链文件通常设置：

- `CMAKE_SYSTEM_NAME`：目标系统名（Linux、Windows、Generic 等）
- `CMAKE_SYSTEM_PROCESSOR`：目标 CPU 架构
- `CMAKE_C_COMPILER` / `CMAKE_CXX_COMPILER`：交叉编译器路径
- `CMAKE_FIND_ROOT_PATH`：目标平台库的搜索根路径
- `CMAKE_FIND_ROOT_PATH_MODE_*`：find_* 命令的搜索模式

### 例子

**ARM Linux 工具链：**

```cmake
# arm-linux-toolchain.cmake
set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_SYSTEM_PROCESSOR arm)

# 指定交叉编译器
set(CMAKE_C_COMPILER arm-linux-gnueabihf-gcc)
set(CMAKE_CXX_COMPILER arm-linux-gnueabihf-g++)

# 目标平台库的搜索路径
set(CMAKE_FIND_ROOT_PATH /usr/arm-linux-gnueabihf)

# find_* 命令的搜索模式
set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)   # 程序：在主机找
set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)    # 库：在目标找
set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)     # 头文件：在目标找
set(CMAKE_FIND_ROOT_PATH_MODE_PACKAGE ONLY)
```

**使用工具链：**

```bash
cmake -B build -S . -DCMAKE_TOOLCHAIN_FILE=arm-linux-toolchain.cmake
cmake --build build
```

**Android NDK 工具链：**

```bash
# NDK 自带工具链文件
cmake -B build -S . \
    -DCMAKE_TOOLCHAIN_FILE=$ANDROID_NDK/build/cmake/android.toolchain.cmake \
    -DANDROID_ABI=arm64-v8a \
    -DANDROID_PLATFORM=android-21
```

**WebAssembly (Emscripten)：**

```bash
# Emscripten 自带工具链
cmake -B build -S . \
    -DCMAKE_TOOLCHAIN_FILE=$EMSDK/upstream/emscripten/cmake/Modules/Platform/Emscripten.cmake
```

**自定义工具链（嵌入式裸机）：**

```cmake
# bare-metal-toolchain.cmake
set(CMAKE_SYSTEM_NAME Generic)
set(CMAKE_SYSTEM_PROCESSOR arm)

set(CMAKE_C_COMPILER arm-none-eabi-gcc)
set(CMAKE_CXX_COMPILER arm-none-eabi-g++)

# 裸机不需要查找系统库
set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY NEVER)
set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE NEVER)
```

### 总结

- 工具链文件用于交叉编译，通过 `-DCMAKE_TOOLCHAIN_FILE` 指定
- 设置目标系统名、编译器、搜索路径
- `CMAKE_FIND_ROOT_PATH_MODE_*` 控制 find_* 命令的搜索范围
- **注意事项**：工具链文件中的设置只在首次配置时生效（写入缓存），修改工具链后需删除 build 目录重新配置

---

# 第 6 章: 测试与部署

## 第 22 讲: CTest 测试框架

### 概念

CTest 是 CMake 内置的测试运行工具，配合 `add_test` 命令注册测试用例，通过 `ctest` 命令批量运行。它支持并行测试、超时控制、标签过滤、失败重试等功能，是 CMake 项目测试的标准方案。

### 原理

CTest 的工作流程：

1. **注册测试**：在 `CMakeLists.txt` 中用 `add_test` 声明测试用例
2. **启用测试**：调用 `enable_testing()` 或在 `project()` 后用 `include(CTest)`
3. **运行测试**：在 build 目录执行 `ctest`

CTest 通过解析 `CTestTestfile.cmake`（由 CMake 自动生成）找到所有测试用例，按顺序或并行执行，记录通过/失败、耗时、输出。

### 例子

**基础测试：**

```cmake
cmake_minimum_required(VERSION 3.15)
project(MyApp CXX)

enable_testing()  # 或 include(CTest)

add_executable(test_math test_math.cpp)
add_test(NAME test_math COMMAND test_math)
```

**带参数和超时：**

```cmake
add_executable(mytest test.cpp)

add_test(NAME basic_test COMMAND mytest --mode=basic)
add_test(NAME stress_test COMMAND mytest --mode=stress)

# 设置超时（秒）和标签
set_tests_properties(stress_test PROPERTIES
    TIMEOUT 60
    LABELS "slow;integration"
)
set_tests_properties(basic_test PROPERTIES
    LABELS "fast;unit"
)
```

**集成 GoogleTest：**

```cmake
include(FetchContent)
FetchContent_Declare(
    googletest
    URL https://github.com/google/googletest/archive/v1.13.0.tar.gz
)
FetchContent_MakeAvailable(googletest)

include(GoogleTest)
enable_testing()

add_executable(tests test.cpp)
target_link_libraries(tests PRIVATE gtest_main)

# 自动注册所有 TEST 和 TEST_F
gtest_discover_tests(tests)
```

**运行测试：**

```bash
cd build
ctest                           # 运行所有测试
ctest --parallel 4              # 并行运行
ctest --output-on-failure       # 失败时显示输出
ctest -R math                   # 只运行名字匹配 math 的测试
ctest -E stress                 # 排除名字匹配 stress 的测试
ctest -L fast                   # 只运行标签为 fast 的测试
ctest --rerun-failed            # 只重跑失败的测试
ctest -C Debug                  # 指定配置（多配置生成器）
```

**自定义测试命令：**

```cmake
# 测试脚本而非可执行文件
add_test(
    NAME integration_test
    COMMAND python ${CMAKE_SOURCE_DIR}/tests/integration.py
    WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}/tests
)

# 测试是否生成特定文件
add_test(NAME check_output COMMAND ${CMAKE_COMMAND}
    -Dfile=output.txt
    -P ${CMAKE_SOURCE_DIR}/cmake/check_file.cmake)
```

### 总结

- `enable_testing()` 启用测试，`add_test` 注册用例
- `set_tests_properties` 设置超时、标签、依赖
- `gtest_discover_tests` 自动注册 GTest 用例
- **注意事项**：测试命令的工作目录默认是 build 目录，需要源码路径时用 `${CMAKE_SOURCE_DIR}`；超时很重要，避免死循环测试卡住 CI

---

## 第 23 讲: CPack 打包

### 概念

CPack 是 CMake 的打包工具，将构建产物打成各平台标准安装包：DEB (Debian/Ubuntu)、RPM (Fedora/RHEL)、ZIP、TGZ、NSIS (Windows)、DragNDrop (macOS)、Bundle (macOS app) 等。

### 原理

CPack 在 `install()` 规则的基础上工作。它先调用 `cmake --install` 把产物安装到临时目录，再根据指定的打包生成器打成最终包。

CPack 支持的常见生成器：

- `ZIP` / `TGZ` / `TBZ2`：通用压缩包
- `DEB`：Debian 包
- `RPM`：Red Hat 包
- `NSIS` / `WIX`：Windows 安装程序
- `Bundle` / `DragNDrop`：macOS 应用包
- `Archive`：通用归档（CMake 3.18+）

### 例子

**基础打包：**

```cmake
cmake_minimum_required(VERSION 3.15)
project(MyApp VERSION 1.0.0)

# 安装规则
install(TARGETS myapp RUNTIME DESTINATION bin)
install(FILES README.md DESTINATION share/myapp)

# CPack 配置
set(CPACK_PACKAGE_NAME "MyApp")
set(CPACK_PACKAGE_VERSION "${PROJECT_VERSION}")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "A sample application")
set(CPACK_PACKAGE_VENDOR "MyCompany")
set(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_SOURCE_DIR}/LICENSE")

include(CPack)
```

**多生成器打包：**

```cmake
# 默认按平台选择生成器
# Linux: TGZ, DEB
# Windows: NSIS, ZIP
# macOS: DragNDrop, TGZ

# 显式指定
set(CPACK_GENERATOR "TGZ;ZIP")

# DEB 特定配置
set(CPACK_DEBIAN_PACKAGE_DEPENDS "libstdc++6, libc6")
set(CPACK_DEBIAN_PACKAGE_SECTION "devel")
set(CPACK_DEBIAN_PACKAGE_MAINTAINER "maintainer@example.com")

# RPM 特定配置
set(CPACK_RPM_PACKAGE_LICENSE "MIT")
set(CPACK_RPM_PACKAGE_REQUIRES "gcc-c++")

# NSIS (Windows) 特定配置
set(CPACK_NSIS_INSTALL_ROOT "C:\\Program Files")
set(CPACK_NSIS_DISPLAY_NAME "MyApp")

include(CPack)
```

**组件化打包：**

```cmake
# 把安装目标分组为组件
install(TARGETS myapp RUNTIME DESTINATION bin COMPONENT applications)
install(TARGETS mylib LIBRARY DESTINATION lib COMPONENT libs)
install(FILES include/mylib.h DESTINATION include COMPONENT dev)

set(CPACK_COMPONENTS_ALL applications libs dev)
set(CPACK_COMPONENT_APPLICATIONS_DISPLAY_NAME "Application")
set(CPACK_COMPONENT_LIBS_DISPLAY_NAME "Libraries")
set(CPACK_COMPONENT_DEV_DISPLAY_NAME "Development Files")
set(CPACK_COMPONENT_DEV_DEPENDS libs)

include(CPack)
```

**打包命令：**

```bash
cd build
cpack                       # 打所有生成器
cpack -G TGZ                # 只打 TGZ
cpack -G "DEB;RPM"          # 打 DEB 和 RPM
cpack -C Release            # 指定配置
cpack --verbose             # 详细输出
```

### 总结

- CPack 基于 `install()` 规则工作，先安装到临时目录再打包
- 各平台有不同生成器，可同时打多个
- 组件化打包让用户能选择性安装
- **注意事项**：DEB/RPM 需要在对应平台打包才能正确处理依赖；跨平台打包推荐用 ZIP/TGZ

---

## 第 24 讲: CDash 与持续集成

### 概念

CDash 是 Kitware 开发的测试结果聚合系统，配合 CTest 的 `ctest -D` 选项实现持续集成 (CI)。它能汇总多台机器、多个配置的测试结果，提供 Web 界面查看趋势。

### 原理

CTest 的 `Dashboard Client` 模式通过 `ctest -D` 触发，分多个阶段：

1. **Start**：开始一个新的测试会话
2. **Update**：从版本控制拉取最新代码
3. **Configure**：运行 cmake 配置
4. **Build**：编译
5. **Test**：运行测试
6. **Coverage**：收集代码覆盖率
7. **MemCheck**：内存检查（如 Valgrind）
8. **Submit**：上传结果到 CDash

每个阶段的结果都会上传到 CDash 服务器，聚合展示。

### 例子

**配置 CDash 提交：**

```cmake
# CTestConfig.cmake（项目根目录）
set(CTEST_PROJECT_NAME "MyProject")
set(CTEST_NIGHTLY_START_TIME "01:00:00 UTC")
set(CTEST_DROP_METHOD "http")
set(CTEST_DROP_SITE "my.cdash.org")
set(CTEST_DROP_LOCATION "/submit.php?project=MyProject")
set(CTEST_DROP_SITE_CDASH TRUE)
```

**手动提交：**

```bash
cd build
ctest -D Experimental     # 实验性提交（不影响主分支）
ctest -D Nightly         # 每日构建
ctest -D Continuous      # 持续构建（检测到代码更新才跑）
```

**GitHub Actions 集成：**

```yaml
# .github/workflows/ci.yml
name: CI
on: [push, pull_request]

jobs:
  build:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
    steps:
      - uses: actions/checkout@v3
      - name: Configure
        run: cmake -B build -S . -DCMAKE_BUILD_TYPE=Release
      - name: Build
        run: cmake --build build --parallel
      - name: Test
        run: ctest --test-dir build --output-on-failure
```

**自定义 CI 脚本：**

```cmake
# cmake/ci.cmake
include(CTest)

# 启用覆盖率（仅 GCC/Clang）
if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
    add_compile_options(--coverage)
    add_link_options(--coverage)
endif()

# 启用内存检查
find_program(VALGRIND valgrind)
if(VALGRIND)
    set(MEMORYCHECK_COMMAND ${VALGRIND})
    set(MEMORYCHECK_COMMAND_OPTIONS "--leak-check=full --error-exitcode=1")
endif()
```

### 总结

- CDash 聚合多平台测试结果，提供 Web 界面
- `ctest -D` 触发 Dashboard 模式，分阶段执行
- 现代 CI 通常直接用 GitHub Actions / GitLab CI，CDash 用于大型项目
- **注意事项**：小型项目用 GitHub Actions 跑 `ctest` 即可，无需 CDash；大型多平台项目才值得部署 CDash

---

# 第 7 章: 现代 CMake 实践

## 第 25 讲: 现代 CMake 理念 (target-based)

### 概念

现代 CMake（3.x 之后）的核心设计理念是 **target-based**：以 target 为中心，通过 target 的属性和传递性管理依赖关系。这与「老式 CMake」基于全局变量（`include_directories`、`add_definitions`）的方式形成鲜明对比。

### 原理

**老式 CMake（全局变量风格）：**

```cmake
# 全局设置，污染所有后续目标
include_directories(include)
add_definitions(-DDEBUG)
link_directories(lib)
add_executable(app main.cpp)
target_link_libraries(app mylib)
```

问题：所有 target 共享同一组设置，无法区分；下游 target 也会继承这些设置，造成耦合。

**现代 CMake（target-based 风格）：**

```cmake
# 每个 target 独立设置
add_library(mylib src/mylib.cpp)
target_include_directories(mylib PUBLIC include)
target_compile_definitions(mylib PRIVATE DEBUG)

add_executable(app main.cpp)
target_link_libraries(app PRIVATE mylib)
# app 自动获得 mylib 的 PUBLIC 头文件路径
```

关键设计：

1. **属性传递**：通过 `PRIVATE` / `PUBLIC` / `INTERFACE` 控制属性如何向下游传递
2. **imported target**：第三方库以 target 形式提供，自带路径和链接信息
3. **namespace 别名**：`MyLib::mylib` 形式避免命名冲突

### 例子

**完整现代风格项目：**

```cmake
cmake_minimum_required(VERSION 3.20)
project(MyApp VERSION 1.0.0 LANGUAGES CXX)

# 库
add_library(mylib STATIC
    src/mylib.cpp
    src/internal.cpp
)
target_include_directories(mylib PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
)
target_compile_features(mylib PUBLIC cxx_std_17)
target_compile_options(mylib PRIVATE -Wall -Wextra)

# 依赖 zlib，但内部使用，不暴露给下游
find_package(ZLIB REQUIRED)
target_link_libraries(mylib PRIVATE ZLIB::ZLIB)

# 别名（供同项目内其他模块使用）
add_library(MyLib::mylib ALIAS mylib)

# 可执行文件
add_executable(myapp src/main.cpp)
target_link_libraries(myapp PRIVATE MyLib::mylib)
# myapp 自动获得 mylib 的 PUBLIC include 路径和 C++17 要求
```

**对比老式风格：**

```cmake
# ❌ 老式风格（不推荐）
include_directories(include)
add_definitions(-DMYLIB_VERSION=1.0)
add_executable(myapp src/main.cpp)
target_link_libraries(myapp mylib)
# 问题：所有 target 都有 include 路径和 MYLIB_VERSION 宏
```

```cmake
# ✅ 现代风格（推荐）
add_library(mylib src/mylib.cpp)
target_include_directories(mylib PUBLIC include)
target_compile_definitions(mylib PUBLIC MYLIB_VERSION=1.0)

add_executable(myapp src/main.cpp)
target_link_libraries(myapp PRIVATE mylib)
# 只有 myapp 通过 mylib 获得这些设置
```

### 总结

- 现代 CMake 以 target 为中心，属性通过 PRIVATE/PUBLIC/INTERFACE 传递
- 避免全局命令（`include_directories`、`add_definitions`、`link_directories`）
- 用 `BUILD_INTERFACE` / `INSTALL_INTERFACE` 区分构建期和安装期路径
- **注意事项**：迁移老项目时，逐步把全局命令改为 target 命令；优先用 imported target 而非变量

---

## 第 26 讲: CMakePresets

### 概念

`CMakePresets.json` 是 CMake 3.19+ 引入的配置预设文件，把常用的 cmake 命令行参数（构建目录、生成器、变量、工具链等）保存为命名的「预设」。用户通过 `--preset` 选择预设，无需记忆复杂参数。

### 原理

`CMakePresets.json` 定义三类预设：

1. **Configure Preset**：配置阶段的预设，包含构建目录、生成器、缓存变量、工具链
2. **Build Preset**：构建阶段的预设，引用某个 configure preset，可指定目标、配置类型
3. **Test Preset**：测试阶段的预设，引用某个 build preset，可指定测试过滤

预设支持继承（`inherits`），可组合多个基础预设。还支持条件（`condition`）和环境变量展开。

### 例子

**基础 CMakePresets.json：**

```json
{
  "version": 3,
  "cmakeMinimumRequired": { "major": 3, "minor": 21, "patch": 0 },
  "configurePresets": [
    {
      "name": "default",
      "displayName": "Default Config",
      "generator": "Ninja",
      "binaryDir": "${sourceDir}/build",
      "cacheVariables": {
        "CMAKE_BUILD_TYPE": "Release"
      }
    },
    {
      "name": "debug",
      "displayName": "Debug Build",
      "inherits": "default",
      "cacheVariables": {
        "CMAKE_BUILD_TYPE": "Debug"
      }
    },
    {
      "name": "release",
      "displayName": "Release Build",
      "inherits": "default",
      "cacheVariables": {
        "CMAKE_BUILD_TYPE": "Release"
      }
    }
  ],
  "buildPresets": [
    {
      "name": "default",
      "configurePreset": "default"
    }
  ],
  "testPresets": [
    {
      "name": "default",
      "configurePreset": "default",
      "output": { "outputOnFailure": true }
    }
  ]
}
```

**使用预设：**

```bash
cmake --preset debug              # 用 debug 预设配置
cmake --build --preset default    # 用 default 构建预设
ctest --preset default            # 用 default 测试预设
```

**多平台预设：**

```json
{
  "version": 3,
  "configurePresets": [
    {
      "name": "windows-base",
      "hidden": true,
      "generator": "Visual Studio 17 2022",
      "binaryDir": "${sourceDir}/build/${presetName}",
      "cacheVariables": {
        "CMAKE_TOOLCHAIN_FILE": "$env{VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake"
      }
    },
    {
      "name": "windows-debug",
      "inherits": "windows-base",
      "cacheVariables": { "CMAKE_BUILD_TYPE": "Debug" }
    },
    {
      "name": "linux-base",
      "hidden": true,
      "generator": "Ninja",
      "binaryDir": "${sourceDir}/build/${presetName}"
    },
    {
      "name": "linux-debug",
      "inherits": "linux-base",
      "cacheVariables": { "CMAKE_BUILD_TYPE": "Debug" }
    }
  ]
}
```

**CMakeUserPresets.json（个人配置，不入版本控制）：**

```json
{
  "version": 3,
  "configurePresets": [
    {
      "name": "local",
      "inherits": "default",
      "cacheVariables": {
        "CMAKE_INSTALL_PREFIX": "/home/user/local"
      }
    }
  ]
}
```

### 总结

- `CMakePresets.json` 入版本控制，团队共享
- `CMakeUserPresets.json` 个人配置，不入版本控制
- 预设支持继承，避免重复
- **注意事项**：`version` 字段必须与 CMake 版本匹配（3.19=1, 3.20=2, 3.21=3, 3.24=4, 3.27=5, 3.28=6, 3.30=7）

---

## 第 27 讲: 项目组织最佳实践

### 概念

随着项目规模增长，CMakeLists.txt 的组织方式直接影响可维护性。本讲总结现代 CMake 项目的目录结构、模块划分、命名约定等最佳实践。

### 原理

**1. 目录结构原则**

- 源码与构建产物分离（`build/` 目录）
- 头文件与源码分离（`include/` 放公开头文件，`src/` 放内部实现）
- 模块化组织（每个子模块有自己的 `CMakeLists.txt`）
- 配置文件统一管理（`cmake/` 目录）

**2. 命名约定**

- target 名用小写或 PascalCase，避免与系统库冲突
- 公开 target 加 namespace 别名（`MyLib::core`）
- 缓存变量加项目前缀（`MYLIB_BUILD_TESTS`）

**3. 模块化原则**

- 每个子目录一个 `CMakeLists.txt`
- 子目录通过 `add_subdirectory` 引入
- 公共配置在根 `CMakeLists.txt`，子目录只关心自己的 target

### 例子

**推荐目录结构：**

```
myproject/
├── CMakeLists.txt              # 根配置
├── CMakePresets.json
├── cmake/                      # 自定义模块和工具
│   ├── MyFeatures.cmake
│   └── CompilerWarnings.cmake
├── include/                    # 公开头文件
│   └── mylib/
│       ├── core.h
│       └── utils.h
├── src/                        # 源码（按模块组织）
│   ├── core/
│   │   ├── CMakeLists.txt
│   │   └── core.cpp
│   ├── utils/
│   │   ├── CMakeLists.txt
│   │   └── utils.cpp
│   └── app/
│       ├── CMakeLists.txt
│       └── main.cpp
├── tests/                      # 测试
│   ├── CMakeLists.txt
│   └── test_core.cpp
└── docs/                       # 文档
```

**根 CMakeLists.txt：**

```cmake
cmake_minimum_required(VERSION 3.20)
project(MyProject VERSION 1.0.0 LANGUAGES CXX)

# 全局设置
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# 默认构建类型
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

# 选项
option(MYPROJECT_BUILD_TESTS "Build tests" ON)
option(MYPROJECT_BUILD_DOCS "Build docs" OFF)

# 自定义模块路径
list(APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake)

# 包含子模块
add_subdirectory(src/core)
add_subdirectory(src/utils)
add_subdirectory(src/app)

if(MYPROJECT_BUILD_TESTS)
    enable_testing()
    add_subdirectory(tests)
endif()
```

**子模块 CMakeLists.txt（src/core/CMakeLists.txt）：**

```cmake
add_library(mylib_core
    core.cpp
)

target_include_directories(mylib_core
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:include>
)

target_compile_features(mylib_core PUBLIC cxx_std_17)

# 别名
add_library(MyProject::Core ALIAS mylib_core)
```

**公共编译选项模块（cmake/CompilerWarnings.cmake）：**

```cmake
function(myproject_set_warnings target)
    target_compile_options(${target} PRIVATE
        $<$<CXX_COMPILER_ID:GNU,Clang>:
            -Wall -Wextra -Wpedantic -Werror
            -Wno-unused-parameter
        >
        $<$<CXX_COMPILER_ID:MSVC>:
            /W4 /permissive- /WX
        >
    )
endfunction()
```

### 总结

- 模块化组织，每个子目录一个 `CMakeLists.txt`
- 公共配置抽到 `cmake/` 目录的函数/宏
- 用 namespace 别名避免命名冲突
- **注意事项**：避免在子目录的 `CMakeLists.txt` 中设置全局变量或全局命令；保持子目录的独立性

---

# 第 8 章: 高级主题

## 第 28 讲: 函数与宏

### 概念

CMake 提供两种自定义命令的方式：**function**（函数）和 **macro**（宏）。它们都能封装重复逻辑，但语义不同：函数有独立作用域，宏在调用处展开。理解差异是编写复杂 CMake 脚本的基础。

### 原理

**function（函数）：**

- 创建新的作用域，函数内 `set` 的变量不影响外部
- 参数通过 `${ARGV0}`、`${ARGC}`、`${ARGN}` 访问
- 用 `set(... PARENT_SCOPE)` 修改父作用域变量
- 支持命名参数（`set(<var> <value>)` 在 `function` 内）

**macro（宏）：**

- 文本展开，无新作用域
- 宏内 `set` 的变量直接影响调用处
- 参数通过 `${ARGV0}` 等访问，但本质是字符串替换
- 适合简单文本替换场景

### 例子

**基础函数：**

```cmake
function(add_test_executable name)
    add_executable(${name} ${name}.cpp)
    target_link_libraries(${name} PRIVATE gtest_main)
    add_test(NAME ${name} COMMAND ${name})
endfunction()

# 使用
add_test_executable(test_math)
add_test_executable(test_string)
```

**带命名参数的函数：**

```cmake
function(add_module)
    set(options NO_TEST)
    set(oneValueArgs NAME VERSION)
    set(multiValueArgs SOURCES DEPENDS)
    cmake_parse_arguments(MOD "${options}" "${oneValueArgs}" "${multiValueArgs}" ${ARGN})

    add_library(${MOD_NAME} ${MOD_SOURCES})
    target_link_libraries(${MOD_NAME} PUBLIC ${MOD_DEPENDS})

    if(NOT MOD_NO_TEST)
        add_subdirectory(tests)
    endif()
endfunction()

# 使用
add_module(
    NAME mylib
    VERSION 1.0.0
    SOURCES src/a.cpp src/b.cpp
    DEPENDS zlib fmt
)
```

**宏：**

```cmake
macro(print_var var)
    message(STATUS "${var} = ${${var}}")
endmacro()

set(MY_VAR 42)
print_var(MY_VAR)   # 输出: MY_VAR = 42
```

**函数 vs 宏的差异：**

```cmake
function(func)
    set(X 1)  # 局部变量，不影响外部
endfunction()

macro(macr)
    set(X 1)  # 直接修改外部变量
endmacro()

set(X 0)
func()
message(STATUS "After func: X=${X}")  # X=0

set(X 0)
macr()
message(STATUS "After macr: X=${X}")  # X=1
```

**返回值：**

```cmake
function(get_version out_var)
    set(${out_var} "1.2.3" PARENT_SCOPE)
endfunction()

get_version(VERSION)
message(STATUS "Version: ${VERSION}")  # 1.2.3
```

### 总结

- 函数有独立作用域，宏是文本展开
- 用 `cmake_parse_arguments` 处理复杂参数
- 函数返回值用 `set(... PARENT_SCOPE)`
- **注意事项**：宏的参数是字符串替换，传 `${var}` 时会先展开；函数则按值传递

---

## 第 29 讲: CMake 脚本模式

### 概念

CMake 不仅能作为构建系统生成器，还能作为脚本语言运行。通过 `cmake -P script.cmake` 执行脚本，可用于自动化任务、CI 脚本、文件处理等。脚本模式下没有构建目标，只用 CMake 的命令做通用编程。

### 原理

脚本模式的特点：

- 用 `cmake -P <script>` 执行
- 没有 `project()` 命令，没有构建目标
- 不能用 `add_executable`、`add_library` 等构建命令
- 可用所有通用命令：`file`、`message`、`execute_process`、`string`、`list`
- 可通过 `-D` 传入变量

### 例子

**基础脚本：**

```cmake
# hello.cmake
message(STATUS "Hello from CMake script!")
message(STATUS "Arguments: ${ARGN}")
```

```bash
cmake -P hello.cmake arg1 arg2
# 输出: Hello from CMake script!
#       Arguments: arg1;arg2
```

**文件处理脚本：**

```cmake
# copy_assets.cmake
set(SRC_DIR ${CMAKE_CURRENT_LIST_DIR}/assets)
set(DST_DIR ${CMAKE_CURRENT_LIST_DIR}/build/assets)

# 创建目录
file(MAKE_DIRECTORY ${DST_DIR})

# 复制所有 .png 文件
file(GLOB PNG_FILES "${SRC_DIR}/*.png")
foreach(png ${PNG_FILES})
    file(COPY ${png} DESTINATION ${DST_DIR})
    message(STATUS "Copied: ${png}")
endforeach()

# 统计文件数
list(LENGTH PNG_FILES COUNT)
message(STATUS "Total: ${COUNT} files")
```

**调用外部命令：**

```cmake
# run_tests.cmake
execute_process(
    COMMAND ctest --output-on-failure
    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/build
    RESULT_VARIABLE result
    OUTPUT_VARIABLE output
    ERROR_VARIABLE error
)

if(NOT result EQUAL 0)
    message(FATAL_ERROR "Tests failed:\n${output}\n${error}")
endif()

message(STATUS "All tests passed")
```

**生成配置文件：**

```cmake
# gen_config.cmake
set(VERSION 1.2.3)
set(BUILD_DATE "2026-07-26")

configure_file(
    ${CMAKE_CURRENT_LIST_DIR}/config.h.in
    ${CMAKE_CURRENT_LIST_DIR}/config.h
    @ONLY
)
```

`config.h.in`：
```c
#define APP_VERSION "@VERSION@"
#define BUILD_DATE "@BUILD_DATE@"
```

**CI 脚本（带参数）：**

```cmake
# ci.cmake
# 用法: cmake -DCONFIG=Release -P ci.cmake

if(NOT CONFIG)
    set(CONFIG Release)
endif()

message(STATUS "Building with config: ${CONFIG}")

execute_process(COMMAND cmake -B build -S . -DCMAKE_BUILD_TYPE=${CONFIG})
execute_process(COMMAND cmake --build build --parallel)
execute_process(COMMAND ctest --test-dir build --output-on-failure)
execute_process(COMMAND cpack --config ${CONFIG})
```

### 总结

- 脚本模式用 `cmake -P` 执行，无构建目标
- 适合文件处理、CI 自动化、配置生成
- 可通过 `-D` 传参，`ARGN` 获取额外参数
- **注意事项**：脚本模式没有 `project()`，不能用 `add_executable` 等构建命令；需要执行外部程序时用 `execute_process`

---

## 第 30 讲: 编写可复用的 CMake 包

### 概念

本讲是教程的总结，讲解如何编写一个高质量的、可被其他项目复用的 CMake 包。一个优秀的 CMake 包不仅提供库文件，还要提供清晰的接口、版本管理、依赖声明、文档和示例。

### 原理

可复用 CMake 包的核心要素：

1. **清晰的接口**：用 `PUBLIC` / `PRIVATE` 严格区分对外暴露和内部使用
2. **版本管理**：用 `project(VERSION x.y.z)` 声明版本，提供 config-version 文件
3. **依赖声明**：在 config 文件中用 `find_dependency` 声明传递依赖
4. **命名空间**：用 `MyLib::` 前缀避免冲突
5. **组件支持**：允许下游只使用部分组件
6. **文档**：提供 README 和使用示例

### 例子

**完整可复用包结构：**

```
mylib/
├── CMakeLists.txt
├── CMakePresets.json
├── README.md
├── LICENSE
├── cmake/
│   ├── mylib-config.cmake.in
│   └── MylibUtils.cmake        # 自定义函数
├── include/mylib/
│   ├── core.h
│   ├── net.h
│   └── utils.h
├── src/
│   ├── core/CMakeLists.txt
│   ├── net/CMakeLists.txt
│   └── utils/CMakeLists.txt
├── examples/
│   └── CMakeLists.txt
└── tests/
    └── CMakeLists.txt
```

**根 CMakeLists.txt：**

```cmake
cmake_minimum_required(VERSION 3.20)
project(mylib VERSION 1.0.0 LANGUAGES CXX)

# 选项
option(MYLIB_BUILD_TESTS "Build tests" ON)
option(MYLIB_BUILD_EXAMPLES "Build examples" ON)
option(MYLIB_BUILD_SHARED "Build shared library" ON)

# 全局设置
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 安装路径
include(GNUInstallDirs)
set(MYLIB_CMAKE_DIR ${CMAKE_INSTALL_LIBDIR}/cmake/mylib)

# 子模块
add_subdirectory(src/core)
add_subdirectory(src/net)
add_subdirectory(src/utils)

# 测试和示例
if(MYLIB_BUILD_TESTS)
    enable_testing()
    add_subdirectory(tests)
endif()

if(MYLIB_BUILD_EXAMPLES)
    add_subdirectory(examples)
endif()

# 生成 config 文件
include(CMakePackageConfigHelpers)
configure_package_config_file(
    cmake/mylib-config.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/mylib-config.cmake
    INSTALL_DESTINATION ${MYLIB_CMAKE_DIR}
)
write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/mylib-config-version.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY SameMajorVersion
)

# 安装 config
install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/mylib-config.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/mylib-config-version.cmake
    DESTINATION ${MYLIB_CMAKE_DIR}
)
```

**config 模板（cmake/mylib-config.cmake.in）：**

```cmake
@PACKAGE_INIT@

include(CMakeFindDependencyMacro)

# 声明传递依赖
find_dependency(ZLIB)
find_dependency(Threads)

# 引入组件 targets
include("${CMAKE_CURRENT_LIST_DIR}/mylib-core-targets.cmake")
include("${CMAKE_CURRENT_LIST_DIR}/mylib-net-targets.cmake")
include("${CMAKE_CURRENT_LIST_DIR}/mylib-utils-targets.cmake")

# 提供版本变量
set(MYLIB_VERSION @PROJECT_VERSION@)

# 组件检查
check_required_components(mylib)
```

**下游使用：**

```cmake
# 使用整个包
find_package(mylib 1.0 REQUIRED)
add_executable(app main.cpp)
target_link_libraries(app PRIVATE mylib::core mylib::net)

# 只使用某个组件
find_package(mylib 1.0 REQUIRED COMPONENTS core)
target_link_libraries(app PRIVATE mylib::core)

# 可选使用
find_package(mylib 1.0)
if(mylib_FOUND)
    target_link_libraries(app PRIVATE mylib::core)
endif()
```

**README 中的使用说明：**

```markdown
# MyLib

## 安装
```bash
cmake -B build -S . -DCMAKE_INSTALL_PREFIX=/usr/local
cmake --build build --parallel
cmake --install build
```

## 使用
```cmake
find_package(mylib 1.0 REQUIRED)
target_link_libraries(your_app PRIVATE mylib::core)
```

## 组件
- `mylib::core`：核心功能
- `mylib::net`：网络模块
- `mylib::utils`：工具函数
```

### 总结

- 可复用包要严格区分 PUBLIC/PRIVATE，明确接口
- 用 `find_dependency` 声明传递依赖
- 支持组件化，让下游按需使用
- 提供清晰的 README 和示例
- **注意事项**：版本兼容性用 `SameMajorVersion`，主版本号变更表示有破坏性改动；提供 `MYLIB_BUILD_TESTS` 等选项让下游可关闭不必要的构建

---

## 课程总结

恭喜完成 30 讲 CMake 系统教程！回顾学习路径：

1. **基础入门（1-4 讲）**：理解 CMake 是构建系统生成器，掌握基本工作流程
2. **核心语法（5-9 讲）**：变量、目标、属性、流程控制
3. **构建管理（10-13 讲）**：库类型、target 属性、自定义命令、安装规则
4. **依赖管理（14-17 讲）**：find_package、Config 模块、FetchContent、ExternalProject
5. **跨平台（18-21 讲）**：平台检测、编译选项、生成器表达式、工具链
6. **测试部署（22-24 讲）**：CTest、CPack、CDash/CI
7. **现代实践（25-27 讲）**：target-based 理念、CMakePresets、项目组织
8. **高级主题（28-30 讲）**：函数宏、脚本模式、可复用包

**继续学习的方向：**

- 阅读官方文档：https://cmake.org/cmake/help/latest/
- 研究优秀开源项目的 CMakeLists.txt（如 fmt、nlohmann/json、OpenCV）
- 实践：把现有项目逐步迁移到现代 CMake 风格
- 关注 CMake 新版本特性（每年发布 2-3 个版本）

CMake 的核心理念是「描述而非命令」，掌握 target-based 设计思想，配合生成器表达式和现代依赖管理，就能应对从嵌入式到大型跨平台项目的各种构建需求。



