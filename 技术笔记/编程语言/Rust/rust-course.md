# Rust 系统化教程：从入门到实战

> 本教程以"教科书式"的讲解方式，按"概念 → 原理 → 例子 → 总结"四段式结构，循序渐进地讲解 Rust 编程语言。每讲聚焦一个核心知识点，配合可运行代码示例，帮助读者建立完整的 Rust 知识体系。

---

## 课程总览

- **预计讲数**：30 讲（8 章）
- **学习目标**：
  1. 掌握 Rust 的核心语法与类型系统
  2. 深入理解所有权、借用、生命周期三大基石
  3. 熟练使用 trait、泛型构建抽象
  4. 掌握 Rust 的并发与异步编程模型
  5. 具备独立开发、测试、发布 Rust 项目的能力
- **适用读者**：有任意一门编程语言基础，希望系统学习 Rust 的开发者
- **学习建议**：按章节顺序学习，每讲先读"概念"和"原理"建立认知，再动手运行"例子"中的代码，最后用"总结"回顾要点

---

## 详细章节目录

### 第 1 章：Rust 入门基础
- 第 1 讲：Rust 简介与环境搭建
- 第 2 讲：Hello World 与 Cargo 项目管理
- 第 3 讲：变量与数据类型
- 第 4 讲：控制流：条件与循环

### 第 2 章：所有权与借用
- 第 5 讲：所有权机制
- 第 6 讲：引用与借用
- 第 7 讲：生命周期
- 第 8 讲：切片与字符串

### 第 3 章：结构化数据
- 第 9 讲：结构体
- 第 10 讲：枚举与模式匹配
- 第 11 讲：集合类型：Vec 与 HashMap
- 第 12 讲：错误处理：Option 与 Result

### 第 4 章：函数与模块
- 第 13 讲：函数与闭包
- 第 14 讲：迭代器
- 第 15 讲：模块系统与包管理

### 第 5 章：trait 与泛型
- 第 16 讲：方法与 trait 基础
- 第 17 讲：泛型
- 第 18 讲：trait 对象与静态/动态分发
- 第 19 讲：生命周期进阶与 trait 约束

### 第 6 章：并发编程
- 第 20 讲：线程与消息传递
- 第 21 讲：共享状态与 Mutex
- 第 22 讲：Send 与 Sync
- 第 23 讲：异步编程 async/await

### 第 7 章：高级特性
- 第 24 讲：智能指针：Box、Rc、RefCell
- 第 25 讲：不安全 Rust
- 第 26 讲：宏系统
- 第 27 讲：类型系统进阶

### 第 8 章：工程实践
- 第 28 讲：测试与文档
- 第 29 讲：Cargo 高级用法与发布
- 第 30 讲：Rust 项目实战：CLI 工具开发

---

# 第 1 章：Rust 入门基础

本章带你走进 Rust 的世界。我们将从 Rust 的设计哲学讲起，搭建开发环境，编写第一个程序，并学习最基本的语法元素——变量、数据类型和控制流。这些是后续所有章节的基础，请务必动手实践每一行代码。

## 第 1 讲：Rust 简介与环境搭建

### 概念

Rust 是一门系统级编程语言，由 Mozilla 研究院于 2010 年发起，首个稳定版本 1.0 于 2015 年发布。它兼具"底层控制力"与"高层安全性"两大特征：既能像 C/C++ 一样直接操作内存和硬件，又能通过编译期检查避免空指针、悬垂指针、数据竞争等常见内存错误。Rust 连续多年在 Stack Overflow 开发者调查中被评为"最受喜爱的编程语言"，广泛应用于操作系统组件、浏览器引擎、命令行工具、WebAssembly、嵌入式等领域。

### 原理

Rust 的核心创新在于**所有权（Ownership）系统**。传统语言在内存管理上有两条路线：一是 C/C++ 的手动管理，性能高但易出错；二是 Java/Python 的垃圾回收（GC），安全但有运行时开销。Rust 走出第三条路——通过编译期的所有权规则与借用检查器（Borrow Checker），在零运行时开销的前提下保证内存安全。这意味着 Rust 程序在编译完成后，其内存安全保证已经"固化"在二进制文件中，运行时不再需要 GC 介入。此外，Rust 的类型系统还天然支持"无畏并发"（Fearless Concurrency），让编译器在编译期就拦截数据竞争。

### 例子

在 Linux/macOS 上安装 Rust（使用官方推荐的 rustup 工具）：

```bash
# 1. 下载并运行 rustup 安装脚本
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# 2. 让环境变量生效（或重启终端）
source "$HOME/.cargo/env"

# 3. 验证安装
rustc --version    # 输出类似：rustc 1.75.0 (82e160d7a 2023-12-21)
cargo --version    # 输出类似：cargo 1.75.0 (1d8b05cdd 2023-11-20)
```

在 Windows 上，前往 [https://rustup.rs](https://rustup.rs) 下载 `rustup-init.exe` 并运行，安装过程中会提示安装 MSVC 构建工具。

安装完成后，建议配置国内镜像源以加速依赖下载（编辑 `~/.cargo/config.toml`）：

```toml
[source.crates-io]
replace-with = "ustc"

[source.ustc]
registry = "sparse+https://mirrors.ustc.edu.cn/crates.io-index/"
```

### 总结

- Rust 是一门兼顾性能与安全的系统级语言，核心靠所有权系统在编译期保证内存安全
- 使用 `rustup` 管理 Rust 工具链版本，`rustc` 是编译器，`cargo` 是包管理器与构建工具
- 安装后建议配置国内镜像源，否则下载依赖会非常缓慢
- **常见坑**：Windows 用户必须先安装 Visual Studio C++ 构建工具，否则编译会失败；macOS 用户需要安装 Xcode Command Line Tools

---

## 第 2 讲：Hello World 与 Cargo 项目管理

### 概念

Cargo 是 Rust 官方的构建系统与包管理器，相当于 Rust 世界的 Maven/Gradle/npm。它负责创建项目结构、管理依赖、编译代码、运行测试、生成文档和发布包。几乎所有 Rust 项目都通过 Cargo 管理，因此掌握 Cargo 是学习 Rust 的第一步。一个标准的 Cargo 项目包含 `Cargo.toml`（配置清单）和 `src/` 目录（源代码）。

### 原理

Cargo 的工作流程是：读取 `Cargo.toml` 中的依赖声明 → 从 crates.io（Rust 官方包仓库）或镜像源下载依赖 → 解析依赖版本树 → 调用 `rustc` 编译所有源文件 → 链接生成最终二进制文件。`Cargo.toml` 采用 TOML 格式，分为 `[package]`（包元信息）、`[dependencies]`（依赖列表）等节。Cargo 还会生成 `Cargo.lock` 文件锁定依赖的确切版本，保证团队协作时构建结果可复现，这一点与 Node.js 的 `package-lock.json` 类似。

### 例子

创建并运行第一个 Cargo 项目：

```bash
# 1. 创建新项目
cargo new hello_rust
cd hello_rust

# 2. 查看项目结构
# hello_rust/
# ├── Cargo.toml
# └── src/
#     └── main.rs

# 3. 运行项目
cargo run
# 输出：Hello, world!
```

`Cargo.toml` 的内容：

```toml
[package]
name = "hello_rust"
version = "0.1.0"
edition = "2021"   # Rust 版本（edition），目前推荐 2021

[dependencies]
# 依赖写在这里
```

`src/main.rs` 的内容：

```rust
fn main() {
    println!("Hello, world!");
}
```

添加一个第三方依赖并使用：

```toml
# 在 Cargo.toml 的 [dependencies] 下添加
[dependencies]
rand = "0.8"   # 随机数库
```

```rust
use rand::Rng;

fn main() {
    let n = rand::thread_rng().gen_range(1..=100);
    println!("随机数：{}", n);
}
```

常用 Cargo 命令速查：

| 命令 | 作用 |
|------|------|
| `cargo new <name>` | 创建新项目 |
| `cargo build` | 编译项目 |
| `cargo run` | 编译并运行 |
| `cargo build --release` | 开启优化编译（用于发布） |
| `cargo check` | 只做类型检查，不生成二进制（最快） |
| `cargo test` | 运行测试 |
| `cargo add <crate>` | 添加依赖 |
| `cargo doc --open` | 生成文档并打开 |

### 总结

- Cargo 是 Rust 的瑞士军刀，统一管理构建、依赖、测试、文档
- 项目结构固定：`Cargo.toml` + `src/main.rs`（或 `src/lib.rs`）
- `edition` 字段控制语言版本，目前推荐 `"2021"`
- 开发时用 `cargo check` 快速验证，发布时用 `cargo build --release`
- **常见坑**：修改依赖后必须重新 `cargo build`；`Cargo.lock` 对于二进制项目应提交到版本控制，对于库项目通常不提交

---

## 第 3 讲：变量与数据类型

### 概念

Rust 是一门静态强类型语言，每个变量在编译期就有确定的类型。变量通过 `let` 声明，默认**不可变**（immutable）——这是 Rust 鼓励安全编程的重要设计。若需要可变变量，须显式使用 `let mut`。Rust 的数据类型分为两大类：**标量类型**（整型、浮点型、布尔型、字符型）和**复合类型**（元组、数组）。

### 原理

Rust 默认不可变的设计源于"最小权限原则"：如果一段代码不需要修改变量，就不应该赋予它修改的能力，这样编译器能在编译期发现更多潜在错误。当确实需要修改时，必须用 `mut` 显式声明，使修改变得"显眼"——读者一眼就能看出哪些变量会被改变。类型推断方面，Rust 编译器能在大多数情况下自动推断变量类型，无需手写类型标注；但当类型歧义时，需用 `let x: T = ...` 显式标注。整型默认 `i32`，浮点型默认 `f64`。

### 例子

```rust
fn main() {
    // ===== 不可变变量 =====
    let x = 5;
    // x = 6;  // 编译错误：cannot assign twice to immutable variable
    println!("x = {}", x);

    // ===== 可变变量 =====
    let mut y = 10;
    y += 5;
    println!("y = {}", y);

    // ===== 标量类型 =====
    let a: i32 = -42;           // 32位有符号整数
    let b: u64 = 100;           // 64位无符号整数
    let c: f64 = 3.14159;       // 64位浮点
    let d: bool = true;         // 布尔
    let e: char = '中';         // Unicode 字符（4字节）

    // ===== 元组：固定长度，可异构 =====
    let tup: (i32, f64, &str) = (500, 6.4, "hello");
    let (p, q, r) = tup;        // 解构
    println!("{} {} {}", p, q, r);
    println!("元组第一个元素：{}", tup.0);

    // ===== 数组：固定长度，同构 =====
    let arr: [i32; 5] = [1, 2, 3, 4, 5];
    let zeros = [0; 10];        // 10个0
    println!("arr[2] = {}", arr[2]);

    // ===== 变量遮蔽（shadowing）=====
    let s = "42";
    let s: i32 = s.parse().unwrap();  // 用同名变量承接新类型
    println!("s 现在是整数：{}", s);
}
```

整型字面量写法：

```rust
let decimal     = 98_222;      // 十进制，下划线提升可读性
let hex         = 0xff;        // 十六进制
let octal       = 0o77;        // 八进制
let binary      = 0b1111_0000; // 二进制
let byte        = b'A';        // 字节（u8）
```

### 总结

- 变量默认不可变，需用 `let mut` 声明可变变量——这是 Rust 与多数语言的最大区别之一
- 标量类型：`i8/i16/i32/i64/i128/isize`、`u8/u16/.../usize`、`f32/f64`、`bool`、`char`
- 复合类型：元组 `(T1, T2, ...)` 异构定长，数组 `[T; N]` 同构定长
- 变量遮蔽允许用同名变量承接不同类型的值，比 `mut` 更灵活
- **常见坑**：整数溢出在 debug 模式会 panic，release 模式会回绕（wrap around）；数组越界访问在运行时 panic；`char` 是 4 字节 Unicode 标量值，不是 1 字节

---

## 第 4 讲：控制流：条件与循环

### 概念

控制流决定程序的执行路径。Rust 提供三种控制流结构：**条件分支**（`if`/`else if`/`else`）、**循环**（`loop`、`while`、`for`）和**模式匹配**（`match`，将在第 10 讲详述）。与 C/Java 不同，Rust 的 `if` 是表达式而非语句，可以返回值；`loop` 也可以通过 `break` 返回值。这种"表达式优先"的设计让 Rust 代码更简洁。

### 原理

Rust 中区分**语句**（statement，执行操作但不返回值）和**表达式**（expression，求值并返回结果）。`if`、`loop`、`block`（花括号包裹的代码块）都是表达式。这意味着你可以写 `let x = if cond { 5 } else { 6 };`。但要注意：`if` 的两个分支必须返回相同类型，否则编译错误。循环方面，`loop` 是无条件循环（需手动 `break`），`while` 是条件循环，`for` 用于遍历迭代器——`for` 是 Rust 中最常用、最安全的循环形式。

### 例子

```rust
fn main() {
    // ===== if 表达式 =====
    let number = 6;
    if number % 4 == 0 {
        println!("能被4整除");
    } else if number % 3 == 0 {
        println!("能被3整除");
    } else {
        println!("都不能整除");
    }

    // if 作为表达式返回值
    let condition = true;
    let x = if condition { 5 } else { 6 };
    println!("x = {}", x);

    // ===== loop 循环 =====
    let mut count = 0;
    let result = loop {
        count += 1;
        if count == 10 {
            break count * 2;  // break 带返回值
        }
    };
    println!("loop 结果：{}", result);

    // while 循环
    let mut n = 3;
    while n != 0 {
        println!("{}!", n);
        n -= 1;
    }
    println!("发射！");

    // for 循环（最推荐）
    let arr = [10, 20, 30, 40, 50];
    for element in arr.iter() {
        println!("元素：{}", element);
    }

    // 遍历范围
    for i in 1..=5 {       // 闭区间 1,2,3,4,5
        println!("第 {} 次", i);
    }

    for i in (1..4).rev() { // 反向遍历
        println!("倒计时：{}", i);
    }
}
```

`if let` 语法——条件匹配的简写（第 10 讲会深入）：

```rust
let some_value: Option<i32> = Some(42);

// 等价于 match，但只关心一种情况
if let Some(n) = some_value {
    println!("拿到值：{}", n);
} else {
    println!("没有值");
}
```

### 总结

- `if` 是表达式，可返回值；两个分支必须返回相同类型
- 三种循环：`loop`（无条件，可 `break` 返回值）、`while`（条件循环）、`for`（遍历迭代器，最推荐）
- `for` 配合范围 `a..b`（左闭右开）或 `a..=b`（闭区间）使用
- `if let` 适合只关心一种模式分支的场景，比 `match` 更简洁
- **常见坑**：`if` 条件必须是 `bool` 类型，Rust 不会把 `0`/非零数字自动转成 `bool`；`for` 循环中若要修改元素，需用 `for x in arr.iter_mut()`

---

# 第 2 章：所有权与借用

本章是 Rust 最核心、也最独特的部分。所有权系统是 Rust 实现内存安全的基石，理解它就理解了 Rust 的灵魂。我们将依次讲解所有权规则、引用与借用、生命周期，以及切片与字符串的内存模型。这一章的内容在其他语言中没有直接对应物，请反复阅读并动手实验。

## 第 5 讲：所有权机制

### 概念

**所有权（Ownership）**是 Rust 管理内存的一套规则：每个值在任一时刻有且仅有一个"所有者"（owner）变量；当所有者离开作用域时，值被自动销毁。所有权可以通过"移动"（move）在不同变量间转移，转移后原变量失效。这套机制让 Rust 无需垃圾回收器，也能在编译期保证内存安全。

### 原理

Rust 的内存管理基于**栈（Stack）**和**堆（Heap）**。栈用于存储编译期已知大小的值（如整型、布尔、固定大小数组），按 LIFO 顺序自动分配释放；堆用于存储运行期才知道大小或可能变化的值（如 `String`、`Vec`），需显式分配。所有权的核心规则是：当所有者变量离开作用域时，Rust 自动调用该值的 `drop` 函数释放堆内存。对于堆数据，赋值操作不是"复制"，而是"移动"——原变量失效，避免双重释放。对于实现了 `Copy` trait 的类型（如整型、布尔），赋值是"复制"，原变量仍可用。

### 例子

```rust
fn main() {
    // ===== 栈上数据：Copy 语义 =====
    let a = 5;
    let b = a;       // 复制，a 仍可用
    println!("a={}, b={}", a, b);

    // ===== 堆上数据：Move 语义 =====
    let s1 = String::from("hello");
    let s2 = s1;     // 移动！s1 失效
    // println!("{}", s1);  // 编译错误：value borrowed after move
    println!("s2 = {}", s2);

    // ===== 函数传参也会移动 =====
    let s3 = String::from("world");
    takes_ownership(s3);     // s3 移动到函数内
    // println!("{}", s3);   // 错误：s3 已失效

    let x = 5;
    makes_copy(x);           // x 被复制，仍可用
    println!("x 仍可用：{}", x);

    // ===== 函数返回值转移所有权 =====
    let s4 = gives_ownership();   // 所有权从函数移出
    println!("s4 = {}", s4);

    // 传入再传出
    let s5 = String::from("rust");
    let s6 = takes_and_gives_back(s5);
    // s5 已失效，s6 持有所有权
    println!("s6 = {}", s6);
}

fn takes_ownership(s: String) {
    println!("获得所有权：{}", s);
}   // s 离开作用域，drop 自动释放内存

fn makes_copy(x: i32) {
    println!("复制得到：{}", x);
}

fn gives_ownership() -> String {
    String::from("来自函数")
}

fn takes_and_gives_back(s: String) -> String {
    s
}
```

所有权移动的内存示意图（`let s2 = s1;`）：

```
移动前：                      移动后：
s1 ──┐                       s1 (失效)
     │                       s2 ──┐
     ▼                            │
  ┌──────┬─────┬─────┐            ▼
  │ ptr  │ len │ cap │         ┌──────┬─────┬─────┐
  └──┬───┴─────┴─────┘         │ ptr  │ len │ cap │
     │                          └──┬───┴─────┴─────┘
     ▼                             ▼
   堆: "hello"                   堆: "hello"  (同一块内存)
```

### 总结

- 所有权三规则：每个值有唯一所有者；所有者离开作用域时值被销毁；赋值/传参默认是"移动"
- 栈上数据（实现 `Copy` trait）赋值时复制，原变量仍可用；堆数据移动后原变量失效
- 函数传参会移动所有权，函数返回值会移出所有权——这导致频繁传参时需手动传回，比较繁琐（下一讲的"引用"解决此问题）
- **常见坑**：`String` 是 move 语义，但 `&str` 是 Copy；自定义 `struct` 默认是 move，需手动实现 `Copy` trait 才能复制（且所有字段必须都是 Copy）

---

## 第 6 讲：引用与借用

### 概念

**引用（Reference）**是借用变量所有权的方式，用 `&` 创建。引用本身不拥有数据，只是"指向"数据的指针。通过引用使用数据的行为称为**借用（Borrowing）**。引用分为**不可变引用** `&T`（只读）和**可变引用** `&mut T`（可写）。借用机制让函数可以使用数据而不夺取所有权，大幅简化代码。

### 原理

借用检查器（Borrow Checker）在编译期执行以下规则：① 同一时刻可以有任意多个不可变引用，或② 只能有一个可变引用，二者不能同时存在。这条规则从根本上杜绝了数据竞争：要么多人只读，要么一人独写，不可能"一人写多人读"。此外，引用必须始终有效——不能有"悬垂引用"（dangling pointer），即引用的生命周期不能超过被引用数据。这些规则全部在编译期检查，运行时零开销。

### 例子

```rust
fn main() {
    let s1 = String::from("hello");

    // 不可变借用：不夺取所有权
    let len = calculate_length(&s1);
    println!("'{}' 的长度是 {}", s1, len);  // s1 仍可用

    // 可变借用
    let mut s2 = String::from("hi");
    change(&mut s2);
    println!("修改后：{}", s2);

    // ===== 借用规则示例 =====
    let mut s = String::from("data");

    let r1 = &s;       // 不可变借用
    let r2 = &s;       // 多个不可变借用 OK
    println!("{} {}", r1, r2);
    // 此后 r1, r2 不再使用（NLL: Non-Lexical Lifetimes）

    let r3 = &mut s;   // 可变借用 OK（因为 r1, r2 已不再使用）
    r3.push_str("!");
    println!("{}", r3);

    // 以下会编译错误：不能同时有可变和不可变引用
    // let r4 = &s;
    // let r5 = &mut s;
    // println!("{} {}", r4, r5);
}

fn calculate_length(s: &String) -> usize {
    s.len()
}   // s 离开作用域，但因为它不拥有所有权，什么也不会发生

fn change(s: &mut String) {
    s.push_str(", world");
}
```

悬垂引用会被编译器拒绝：

```rust
// 错误示例：函数返回局部变量的引用
// fn dangle() -> &String {
//     let s = String::from("hello");
//     &s   // s 在函数结束时被释放，引用变成悬垂指针！
// }

// 正确做法：直接返回 String，转移所有权
fn no_dangle() -> String {
    let s = String::from("hello");
    s
}
```

### 总结

- 引用 `&T` 借用数据但不拥有所有权，函数返回后原变量仍可用
- 不可变引用 `&T` 可同时存在多个；可变引用 `&mut T` 同一时刻只能有一个
- 不可变引用和可变引用不能同时存在——这是 Rust 防止数据竞争的核心
- NLL（非词法生命周期）让引用的作用域到"最后一次使用"为止，而非"花括号结束"，使代码更符合直觉
- **常见坑**：可变引用要求原变量本身是 `mut`；引用不能比被引用数据活得长（悬垂引用会被编译器拒绝）

---

## 第 7 讲：生命周期

### 概念

**生命周期（Lifetime）**是编译器用来追踪引用有效范围的标注。它本身不改变任何运行时行为，只是给编译器看的"元信息"，帮助借用检查器判断引用是否有效。大多数情况下生命周期可被编译器自动推断（称为生命周期省略规则），但在某些场景（如函数返回引用、结构体持有引用）需手动标注。生命周期用 `'a`、`'b` 这样的撇号加标识符表示。

### 原理

生命周期的本质是回答一个问题："这个引用能活多久？" 当函数返回一个引用时，编译器需要知道返回的引用依赖于哪个输入引用，以保证返回值不会比被引用数据活得长。生命周期标注 `<'a>` 描述了引用之间的约束关系：`fn longest<'a>(x: &'a str, y: &'a str) -> &'a str` 表示"返回值的生命周期等于 x 和 y 中较短的那个"。`'static` 是特殊生命周期，表示"整个程序运行期"，字符串字面量就是 `&'static str`。

### 例子

```rust
// ===== 需要手动标注生命周期的函数 =====
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}

fn main() {
    let s1 = String::from("long string");
    let s2 = String::from("short");
    let result = longest(s1.as_str(), s2.as_str());
    println!("较长的：{}", result);

    // 生命周期保证：result 的有效期不超过 s1 和 s2
    let result2;
    {
        let s3 = String::from("inner");
        // result2 = longest(s1.as_str(), s3.as_str());
        // 错误：s3 在块结束时释放，result2 会悬垂
    }
    // println!("{}", result2);
}

// ===== 结构体持有引用必须标注生命周期 =====
struct Excerpt<'a> {
    part: &'a str,
}

fn main2() {
    let novel = String::from("我叫李雷。今天天气不错。");
    let first_sentence;
    {
        let s = novel.as_str();
        first_sentence = Excerpt { part: s.split('。').next().unwrap() };
    }
    println!("首句：{}", first_sentence.part);
}

// ===== 生命周期省略规则 =====
// 这两个函数等价（编译器自动补全生命周期）
fn first_word(s: &str) -> &str {
    // 编译器推断为：fn first_word<'a>(s: &'a str) -> &'a str
    let bytes = s.as_bytes();
    for (i, &byte) in bytes.iter().enumerate() {
        if byte == b' ' {
            return &s[0..i];
        }
    }
    s
}

// ===== 'static 生命周期 =====
let s: &'static str = "我活在整个程序期间";  // 字符串字面量
```

三条生命周期省略规则：
1. 每个引用参数都有自己的生命周期 `<'a, 'b>`
2. 若只有一个输入生命周期参数，它被赋给所有输出引用
3. 若有 `&self`/`&mut self`，`self` 的生命周期被赋给所有输出引用

### 总结

- 生命周期是编译期的引用有效范围标注，运行时零开销
- 函数返回引用、结构体持有引用时通常需手动标注 `<'a>`
- `'static` 表示"整个程序运行期"，字符串字面量就是此类型
- 大多数场景编译器可自动省略生命周期标注（三条省略规则）
- **常见坑**：不要滥用 `'static` 来"解决"生命周期错误，应重新设计数据结构；尽量让数据拥有所有权（用 `String` 而非 `&str` 作为结构体字段）以简化生命周期

---

## 第 8 讲：切片与字符串

### 概念

**切片（Slice）**是对集合中一段连续元素的引用，不持有所有权。最常用的是字符串切片 `&str` 和数组切片 `&[T]`。切片是一个"胖指针"（fat pointer），内部包含两部分：指向数据的指针和元素数量。Rust 的字符串类型分两种：`String`（堆分配、可变、拥有所有权）和 `&str`（切片引用、不可变、通常指向栈或静态区）。

### 原理

切片的妙处在于它让"借用集合的一部分"变得安全且零拷贝。例如 `&s[0..5]` 创建一个指向 `s` 前 5 字节的切片，不复制任何数据，且编译器保证切片不会超出 `s` 的有效范围。字符串字面量 `let s = "hello";` 中的 `"hello"` 直接编译进二进制文件的只读数据段，`s` 是指向它的 `&'static str`。而 `String::from("hello")` 会在运行时从堆上分配内存并拷贝数据。理解 `String` 与 `&str` 的关系，类似于理解 `Vec<T>` 与 `&[T]` 的关系——前者拥有所有权，后者是借用。

### 例子

```rust
fn main() {
    // ===== 字符串字面量 vs String =====
    let literal: &str = "字面量";        // &'static str，存在只读段
    let owned: String = String::from("堆上"); // 堆分配，拥有所有权

    // String 可以转 &str（自动 deref）
    let s: &str = &owned;

    // &str 不能直接转 String，需显式转换
    let s2: String = literal.to_string();
    let s3: String = String::from(literal);

    // ===== 字符串切片 =====
    let s = String::from("hello world");
    let hello: &str = &s[0..5];    // "hello"
    let world: &str = &s[6..11];   // "world"
    println!("{} {}", hello, world);

    // 切片可以省略首尾
    let full: &str = &s[..];       // 整个字符串
    let start: &str = &s[..5];     // 前5字节
    let end: &str = &s[6..];       // 第6字节到末尾

    // ===== 数组切片 =====
    let arr = [1, 2, 3, 4, 5];
    let slice: &[i32] = &arr[1..4];  // [2, 3, 4]
    println!("切片：{:?}", slice);

    // ===== 字符串拼接 =====
    let s1 = String::from("Hello, ");
    let s2 = String::from("Rust!");
    let s3 = s1 + &s2;   // s1 被移动，s2 借用
    // println!("{}", s1);  // 错误：s1 已移动
    println!("{}", s3);

    // format! 宏：不移动任何变量
    let s1 = String::from("tic");
    let s2 = String::from("tac");
    let s3 = String::from("toe");
    let s = format!("{}-{}-{}", s1, s2, s3);
    println!("{}", s);  // "tic-tac-toe"

    // ===== 遍历字符串（注意：按 Unicode 标量值）=====
    for c in "你好Rust".chars() {
        println!("{}", c);  // 依次输出：你、好、R、u、s、t
    }

    // 按字节遍历
    for b in "你好".bytes() {
        println!("{}", b);  // 输出 6 个字节（每个汉字 3 字节 UTF-8）
    }
}

// ===== 实战：返回字符串切片，无需拷贝 =====
fn first_word(s: &str) -> &str {
    let bytes = s.as_bytes();
    for (i, &byte) in bytes.iter().enumerate() {
        if byte == b' ' {
            return &s[..i];
        }
    }
    &s[..]
}
```

### 总结

- 切片 `&str`/`&[T]` 是胖指针（指针+长度），借用集合的一段，零拷贝
- `String` 拥有堆内存所有权，`&str` 是只读引用；`String` 可自动转 `&str`，反向需 `to_string()`/`String::from()`
- 字符串字面量是 `&'static str`，存在二进制只读段
- Rust 字符串不支持索引 `s[0]`（因为 UTF-8 边界问题），需用 `chars()` 或 `bytes()` 遍历
- **常见坑**：切片边界必须落在 UTF-8 字符边界上，否则 panic；`+` 运算符会移动左操作数；中文字符串切片要小心字节边界

---

# 第 3 章：结构化数据

掌握了所有权之后，本章学习如何组织数据。结构体和枚举是 Rust 自定义类型的两大支柱，模式匹配是操作枚举的核心语法。我们还会学习标准库中最常用的集合 `Vec` 和 `HashMap`，以及 Rust 独特的错误处理模型 `Option`/`Result`。这些是日常编程中最频繁使用的工具。

## 第 9 讲：结构体

### 概念

**结构体（Struct）**是自定义数据类型，将多个相关字段组合在一起。Rust 支持三种结构体：① **命名字段结构体**（C 风格，最常用）；② **元组结构体**（字段无名字，只有类型）；③ **单元结构体**（无字段，常用于实现 trait）。结构体默认是 move 语义，若所有字段都是 `Copy` 类型，可派生 `Copy` trait。

### 原理

Rust 的结构体与 C 的 struct 在内存布局上类似——字段按声明顺序紧密排列（除非有对齐要求）。但 Rust 结构体不支持继承（没有"父结构体"概念），而是通过 trait 实现多态。结构体的字段默认私有，模块外不可访问，需用 `pub` 标记才公开。Rust 提供 `#[derive(Debug, Clone, Copy, PartialEq)]` 等派生属性，自动为结构体实现常用 trait，避免手写样板代码。结构体初始化使用 `StructName { field: value, ... }` 语法，支持"字段简写"（当变量名与字段名相同时可省略）和"结构体更新语法"（用 `..other` 复用其他实例的字段）。

### 例子

```rust
// ===== 命名字段结构体 =====
#[derive(Debug, Clone, PartialEq)]
struct User {
    username: String,
    email: String,
    age: u32,
    active: bool,
}

fn build_user(username: String, email: String) -> User {
    User {
        username,    // 字段简写：变量名与字段名相同
        email,
        age: 25,
        active: true,
    }
}

// ===== 元组结构体 =====
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

fn main() {
    let user1 = build_user(String::from("alice"), String::from("alice@x.com"));
    println!("{:?}", user1);

    // 访问字段
    println!("用户名：{}", user1.username);

    // 结构体更新语法
    let user2 = User {
        email: String::from("new@x.com"),
        ..user1   // 其余字段复用 user1（注意：会 move String 字段）
    };
    // println!("{}", user1.username);  // 错误：已被 move
    println!("user2: {:?}", user2);

    // 元组结构体
    let red = Color(255, 0, 0);
    let p = Point(10, 20, 30);
    println!("颜色：({}, {}, {})", red.0, red.1, red.2);
    // red 和 p 是不同类型，不能混用，即使内部都是 3 个 i32

    // ===== 单元结构体 =====
    let _unit = AlwaysEqual;
}

// ===== 单元结构体 =====
struct AlwaysEqual;  // 无字段

// ===== 为结构体实现方法 =====
#[derive(Debug)]
struct Rectangle {
    width: f64,
    height: f64,
}

impl Rectangle {
    // 关联函数（类似静态方法）：无 self 参数
    fn new(width: f64, height: f64) -> Self {
        Rectangle { width, height }
    }

    fn square(size: f64) -> Self {
        Rectangle { width: size, height: size }
    }

    // 方法：&self 不可变借用
    fn area(&self) -> f64 {
        self.width * self.height
    }

    // 方法：&mut self 可变借用
    fn scale(&mut self, factor: f64) {
        self.width *= factor;
        self.height *= factor;
    }

    // 方法：self 获取所有权
    fn into_square(self) -> Rectangle {
        let side = (self.width + self.height) / 2.0;
        Rectangle { width: side, height: side }
    }
}

fn main2() {
    let mut rect = Rectangle::new(30.0, 50.0);
    println!("面积：{}", rect.area());

    rect.scale(2.0);
    println!("放大后：{:?}", rect);

    let sq = Rectangle::square(10.0);
    println!("正方形：{:?}", sq);
}
```

### 总结

- 三种结构体：命名字段（最常用）、元组结构体（无字段名）、单元结构体（无字段，用于 trait）
- `#[derive(Debug)]` 让结构体可用 `{:?}` 打印；`#[derive(Clone, Copy)]` 让其可复制
- `impl` 块为结构体定义方法：`&self` 借用、`&mut self` 可变借用、`self` 获取所有权
- 关联函数（无 `self`）类似静态方法，用 `Type::function()` 调用
- **常见坑**：`..other` 更新语法会 move 被复用的字段；结构体字段默认私有，跨模块需 `pub`；`Debug` trait 不会自动实现，必须 derive 或手写

---

## 第 10 讲：枚举与模式匹配

### 概念

**枚举（Enum）**定义一个类型，它可以是几个不同变体（variant）之一。Rust 的枚举远比 C/Java 强大——每个变体可以携带不同类型和数量的数据，是代数数据类型（ADT）的体现。**模式匹配（`match`）**是操作枚举的核心语法，必须穷举所有变体，保证逻辑完备。`Option<T>` 和 `Result<T, E>` 就是标准库中最著名的两个枚举。

### 原理

Rust 枚举的内存布局是：一个"标签"（tag，标识当前是哪个变体）+ 足以容纳最大变体的空间。例如 `enum E { A, B(i32), C(f64, String) }` 占用空间为 `max(sizeof(i32), sizeof(f64)+sizeof(String))) + tag`。这种设计让枚举既能表达"多种可能"，又保持紧凑。模式匹配在编译期检查完备性——若漏掉某个变体，编译器报错，这是 Rust 避免"忘记处理边界情况"的关键机制。`Option<T>` 取代了 null，`Result<T, E>` 取代了异常，让错误处理成为类型系统的一部分。

### 例子

```rust
// ===== 自定义枚举 =====
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}

enum Message {
    Quit,                          // 无数据
    Move { x: i32, y: i32 },       // 命名字段（像结构体）
    Write(String),                 // 单个值
    ChangeColor(i32, i32, i32),    // 多个值
}

// ===== 模式匹配 =====
fn describe(msg: Message) -> String {
    match msg {
        Message::Quit => String::from("退出"),
        Message::Move { x, y } => format!("移动到 ({}, {})", x, y),
        Message::Write(text) => format!("写入：{}", text),
        Message::ChangeColor(r, g, b) => format!("颜色 #{:02X}{:02X}{:02X}", r, g, b),
    }
}

fn main() {
    let home = IpAddr::V4(127, 0, 0, 1);
    let loopback = IpAddr::V6(String::from("::1"));

    let m = Message::Move { x: 10, y: 20 };
    println!("{}", describe(m));

    // ===== match 是表达式 =====
    let n = 5;
    let even_or_odd = match n % 2 {
        0 => "偶数",
        1 => "奇数",
        _ => "不可能",   // _ 是通配符
    };
    println!("{} 是 {}", n, even_or_odd);

    // ===== Option<T>：替代 null =====
    let some_num: Option<i32> = Some(42);
    let none: Option<i32> = None;

    let doubled = match some_num {
        Some(x) => x * 2,
        None => 0,
    };
    println!("doubled = {}", doubled);

    // if let：只关心一种情况
    if let Some(x) = some_num {
        println!("拿到值：{}", x);
    }

    // while let：循环解构
    let mut stack = vec![1, 2, 3];
    while let Some(top) = stack.pop() {
        println!("弹出：{}", top);
    }

    // ===== 解构 =====
    let point = (3, 5);
    let (x, y) = point;
    println!("x={}, y={}", x, y);

    // 解构结构体
    struct Point { x: i32, y: i32 }
    let p = Point { x: 1, y: 2 };
    let Point { x: a, y: b } = p;
    println!("a={}, b={}", a, b);
}
```

`Option<T>` 的定义（标准库）：

```rust
enum Option<T> {
    Some(T),
    None,
}

enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

### 总结

- 枚举变体可携带不同类型数据，是 Rust 表达"和类型"的核心工具
- `match` 必须穷举所有变体，`_` 是通配符；`match` 是表达式可返回值
- `if let`/`while let` 适合只关心一种变体的场景
- `Option<T>` 替代 null，`Result<T, E>` 替代异常——Rust 没有 null 和 try/catch
- **常见坑**：`match` 漏掉变体会编译错误；`Option` 和 `Result` 不能直接当值用，必须显式处理；模式匹配中 `..` 可忽略剩余字段

---

## 第 11 讲：集合类型：Vec 与 HashMap

### 概念

`Vec<T>` 是 Rust 的动态数组（类似 C++ 的 `vector`、Java 的 `ArrayList`），在堆上存储可变长度的同类型元素序列。`HashMap<K, V>` 是哈希表，存储键值对。两者都是堆分配的集合类型，拥有数据所有权。当元素数量在编译期未知或需要动态增删时，使用 `Vec`；当需要按键快速查找值时，使用 `HashMap`。

### 原理

`Vec<T>` 内部维护三个字段：指针（指向堆上数据）、长度（当前元素数）、容量（已分配空间）。当长度超过容量时，Vec 会分配一块更大的内存（通常翻倍），把旧数据拷贝过去，释放旧内存。这种"指数扩容"策略让 `push` 操作的均摊时间复杂度为 O(1)。`HashMap<K, V>` 使用哈希函数将键映射到桶（bucket），Rust 默认使用 SipHash 算法（抗 HashDoS 攻击）。HashMap 的键必须实现 `Hash` 和 `Eq` trait。注意：HashMap 的迭代顺序是不确定的，不要依赖顺序。

### 例子

```rust
use std::collections::HashMap;

fn main() {
    // ===== Vec =====
    let mut v: Vec<i32> = Vec::new();
    v.push(1);
    v.push(2);
    v.push(3);

    let v2 = vec![1, 2, 3, 4, 5];   // 宏创建

    // 访问元素
    let third = &v[2];              // 越界会 panic
    println!("第三个：{}", third);

    let first = v.get(0);           // 返回 Option<&T>，越界返回 None
    match first {
        Some(x) => println!("第一个：{}", x),
        None => println!("空数组"),
    }

    // 遍历
    for n in &v {
        println!("{}", n);
    }

    // 遍历并修改
    let mut v3 = vec![10, 20, 30];
    for n in &mut v3 {
        *n *= 2;   // 解引用后赋值
    }
    println!("{:?}", v3);  // [20, 40, 60]

    // 用枚举在 Vec 中存不同类型
    enum Cell { Int(i32), Float(f64), Text(String) }
    let row = vec![
        Cell::Int(42),
        Cell::Float(3.14),
        Cell::Text(String::from("hello")),
    ];

    // ===== HashMap =====
    let mut scores: HashMap<String, i32> = HashMap::new();
    scores.insert(String::from("Alice"), 10);
    scores.insert(String::from("Bob"), 20);

    // 访问
    let alice_score = scores.get(&String::from("Alice"));  // Option<&i32>
    println!("Alice: {:?}", alice_score);

    // 遍历
    for (name, score) in &scores {
        println!("{}: {}", name, score);
    }

    // 仅在键不存在时插入
    scores.entry(String::from("Alice")).or_insert(100);  // 已存在，不覆盖
    scores.entry(String::from("Carol")).or_insert(30);   // 不存在，插入
    println!("{:?}", scores);

    // 用 HashMap 统计词频
    let text = "hello world hello rust world world";
    let mut word_count: HashMap<&str, i32> = HashMap::new();
    for word in text.split_whitespace() {
        let count = word_count.entry(word).or_insert(0);
        *count += 1;
    }
    println!("词频：{:?}", word_count);

    // ===== Vec 与 HashMap 互转 =====
    let teams = vec![("A".to_string(), 1), ("B".to_string(), 2)];
    let map: HashMap<_, _> = teams.into_iter().collect();
    println!("{:?}", map);
}
```

### 总结

- `Vec<T>` 是动态数组，`push`/`pop` 均摊 O(1)，`get(i)` 返回 `Option`，`v[i]` 越界 panic
- `HashMap<K, V>` 键需实现 `Hash + Eq`，`entry().or_insert()` 是惯用的"不存在则插入"模式
- 用枚举可以在 `Vec` 中存储不同类型元素
- `into_iter().collect()` 可在 `Vec` 和 `HashMap` 间转换
- **常见坑**：HashMap 迭代顺序不确定；`Vec` 扩容时所有引用失效；`HashMap` 默认哈希较慢（安全考虑），可用 `ahash` 等第三方库加速

---

## 第 12 讲：错误处理：Option 与 Result

### 概念

Rust 没有 `null` 和异常（exception），而是用 `Option<T>` 表示"可能有值也可能没有"，用 `Result<T, E>` 表示"可能成功也可能失败"。`Option` 用于正常的"缺失"情况（如查找不存在），`Result` 用于可恢复的错误（如文件读取失败）。对于不可恢复的严重错误，使用 `panic!`。这套机制让错误处理成为类型签名的一部分，强制开发者面对错误。

### 原理

`Option<T>` 和 `Result<T, E>` 都是普通枚举，编译器不会特殊对待。但因为它们无处不在，标准库提供了大量便捷方法：`unwrap()`（有值返回值，无值 panic）、`expect()`（同 unwrap 但带自定义消息）、`?` 运算符（提前返回错误）、`map`/`and_then`（链式处理）。`?` 运算符是错误传播的语法糖：`expr?` 等价于 `match expr { Ok(v) => v, Err(e) => return Err(e.into()) }`，让错误处理代码极其简洁。`panic!` 用于"不该发生"的情况（如数组越界、违反不变式），会展开栈并终止线程。

### 例子

```rust
use std::fs::File;
use std::io::{self, Read};
use std::num::ParseIntError;

// ===== Option 的使用 =====
fn first_char(s: &str) -> Option<char> {
    s.chars().next()
}

fn main() {
    let c = first_char("hello");
    match c {
        Some(ch) => println!("首字符：{}", ch),
        None => println!("空字符串"),
    }

    // 便捷方法
    let x: Option<i32> = Some(5);
    let doubled = x.map(|v| v * 2);          // Some(10)
    let plus_one = x.and_then(|v| Some(v + 1)); // Some(6)
    let unwrapped = x.unwrap_or(0);          // 5
    let unwrapped2: i32 = None.unwrap_or(0); // 0
    println!("{:?} {:?} {} {}", doubled, plus_one, unwrapped, unwrapped2);
}

// ===== Result 的使用 =====
fn parse_number(s: &str) -> Result<i32, ParseIntError> {
    s.parse::<i32>()
}

fn main2() {
    match parse_number("42") {
        Ok(n) => println!("解析成功：{}", n),
        Err(e) => println!("解析失败：{}", e),
    }

    // unwrap：简单但危险
    let n = "42".parse::<i32>().unwrap();
    // let bad = "abc".parse::<i32>().unwrap();  // panic!

    // expect：带消息的 unwrap
    let n = "42".parse::<i32>().expect("应该是数字");
    println!("{}", n);
}

// ===== ? 运算符：错误传播 =====
fn read_username(path: &str) -> Result<String, io::Error> {
    let mut file = File::open(path)?;   // 出错则提前 return Err
    let mut contents = String::new();
    file.read_to_string(&mut contents)?;
    Ok(contents.trim().to_string())
}

// ===== 自定义错误类型 =====
#[derive(Debug)]
enum AppError {
    Io(io::Error),
    Parse(ParseIntError),
}

// From trait 让 ? 自动转换错误类型
impl From<io::Error> for AppError {
    fn from(e: io::Error) -> Self {
        AppError::Io(e)
    }
}
impl From<ParseIntError> for AppError {
    fn from(e: ParseIntError) -> Self {
        AppError::Parse(e)
    }
}

fn read_config(path: &str) -> Result<i32, AppError> {
    let mut file = File::open(path)?;        // io::Error 自动转 AppError
    let mut s = String::new();
    file.read_to_string(&mut s)?;
    let n: i32 = s.trim().parse()?;           // ParseIntError 自动转
    Ok(n)
}

// ===== panic!：不可恢复错误 =====
fn divide(a: i32, b: i32) -> i32 {
    if b == 0 {
        panic!("除数不能为零！");   // 立即终止当前线程
    }
    a / b
}

// Option 与 Result 互转
fn lookup(map: &HashMap<String, i32>, key: &str) -> Result<i32, String> {
    map.get(key)
        .copied()
        .ok_or_else(|| format!("键 {} 不存在", key))
}
```

### 总结

- `Option<T>` 处理"值可能缺失"，`Result<T, E>` 处理"操作可能失败"，`panic!` 处理"不可恢复错误"
- `?` 运算符是错误传播的语法糖，让代码极其简洁；要求函数返回 `Result` 或 `Option`
- `unwrap()`/`expect()` 适合原型快速验证，生产代码慎用
- 自定义错误类型实现 `From` trait，可让 `?` 自动转换不同错误
- **常见坑**：`?` 只能在返回 `Result`/`Option` 的函数中使用；`main` 函数也可返回 `Result`；不要在库代码中 `panic`，应返回 `Result`

---

# 第 4 章：函数与模块

本章关注代码的组织与复用。函数是 Rust 的基本执行单元，闭包是"函数式编程"的入口，迭代器是处理集合的优雅方式，模块系统则管理代码的可见性与层次结构。掌握这些，你就能写出结构清晰、可复用的 Rust 代码。

## 第 13 讲：函数与闭包

### 概念

**函数（Function）**通过 `fn` 关键字定义，是 Rust 组织代码的基本单元。函数参数需显式标注类型，返回类型用 `->` 标注。**闭包（Closure）**是匿名函数，能捕获所在作用域的变量，类似 JavaScript 的箭头函数或 Python 的 lambda。闭包语法比函数更灵活：参数类型和返回类型通常可省略，由编译器推断。

### 原理

闭包与函数的根本区别在于"捕获环境"。普通函数不能访问定义点之外的局部变量，闭包可以。Rust 闭包按捕获方式分为三种 trait：`FnOnce`（获取所有权，只能调用一次）、`FnMut`（可变借用，可多次调用但会修改捕获的变量）、`Fn`（不可变借用，可多次调用且不修改）。编译器会根据闭包体自动推断实现哪个 trait。闭包在内存中是一个包含捕获变量的结构体，因此闭包是有大小的类型。当闭包不捕获任何变量时，它可以被转换为普通函数指针 `fn`。

### 例子

```rust
// ===== 普通函数 =====
fn add(a: i32, b: i32) -> i32 {
    a + b   // 最后一行是返回值，无需 return
}

// 提前返回用 return
fn abs(x: i32) -> i32 {
    if x < 0 {
        return -x;
    }
    x
}

// ===== 闭包 =====
fn main() {
    // 完整形式
    let add = |a: i32, b: i32| -> i32 { a + b };
    println!("{}", add(1, 2));

    // 省略类型和返回类型
    let double = |x| x * 2;
    println!("{}", double(5));

    // 多行闭包
    let compute = |x, y| {
        let sum = x + y;
        sum * sum
    };
    println!("{}", compute(2, 3));  // 25

    // ===== 捕获环境变量 =====
    let factor = 10;
    let multiply = |x| x * factor;   // 捕获 factor
    println!("{}", multiply(5));     // 50

    // FnOnce：获取所有权（用 move 关键字强制）
    let name = String::from("Alice");
    let greet = move || println!("Hello, {}!", name);  // move 捕获
    greet();
    // println!("{}", name);  // 错误：name 已被 move

    // FnMut：可变借用
    let mut count = 0;
    let mut increment = || {
        count += 1;
        println!("count = {}", count);
    };
    increment();
    increment();
    println!("最终 count = {}", count);

    // ===== 闭包作为参数 =====
    let nums = vec![1, 2, 3, 4, 5];
    let doubled: Vec<i32> = nums.iter().map(|x| x * 2).collect();
    println!("{:?}", doubled);  // [2, 4, 6, 8, 10]

    let evens: Vec<&i32> = nums.iter().filter(|x| *x % 2 == 0).collect();
    println!("{:?}", evens);    // [2, 4]
}

// ===== 函数接受闭包参数 =====
// 用泛型 + Fn trait（第 17 讲会讲泛型）
fn apply(f: impl Fn(i32) -> i32, x: i32) -> i32 {
    f(x)
}

fn apply_mut(mut f: impl FnMut(i32), x: i32) {
    f(x);
}

// 返回闭包
fn make_adder(n: i32) -> impl Fn(i32) -> i32 {
    move |x| x + n
}

fn main2() {
    let add5 = make_adder(5);
    println!("{}", add5(10));  // 15
    println!("{}", apply(add5, 20));  // 25
}
```

### 总结

- 函数用 `fn` 定义，参数和返回类型必须显式标注；最后一行无分号即为返回值
- 闭包 `|args| body` 可捕获环境变量，按捕获方式分 `Fn`/`FnMut`/`FnOnce` 三种 trait
- `move` 关键字强制闭包获取捕获变量的所有权，常用于多线程场景
- 闭包可作为参数（用 `impl Fn(...)`）和返回值（用 `-> impl Fn(...)`）
- **常见坑**：返回闭包时若捕获局部变量必须用 `move`；闭包类型不能直接写出来，需用 `impl Trait` 或泛型；多个闭包同时可变借用同一变量会冲突

---

## 第 14 讲：迭代器

### 概念

**迭代器（Iterator）**是遍历序列数据的统一抽象。Rust 的迭代器是**惰性**（lazy）的——只有被消费时才计算。迭代器提供 `map`、`filter`、`fold`、`collect` 等高阶方法，支持函数式风格的链式调用。所有标准库集合（`Vec`、`HashMap`、`Range` 等）都能产生迭代器。Rust 迭代器在编译期被优化为零成本抽象（zero-cost abstraction），性能与手写循环相当。

### 原理

`Iterator` trait 只需实现一个方法 `next(&mut self) -> Option<Item>`，返回 `Some(值)` 或 `None`（结束）。迭代器适配器（如 `map`、`filter`）返回新的迭代器，不立即计算，而是组合成"计算管道"，直到 `collect` 或 `for` 循环触发消费。这种惰性求值避免了中间集合分配。Rust 编译器通过内联和单态化，将迭代器链优化为高效的循环代码，运行时无虚函数调用开销。三种迭代方式：`iter()`（借用 `&T`）、`iter_mut()`（可变借用 `&mut T`）、`into_iter()`（获取所有权 `T`）。

### 例子

```rust
fn main() {
    let v = vec![1, 2, 3, 4, 5];

    // ===== 三种迭代方式 =====
    // iter()：借用元素
    for x in v.iter() {
        print!("{} ", x);   // x: &i32
    }
    println!();

    // iter_mut()：可变借用
    let mut v2 = vec![1, 2, 3];
    for x in v2.iter_mut() {
        *x *= 10;
    }
    println!("{:?}", v2);  // [10, 20, 30]

    // into_iter()：获取所有权（消耗 Vec）
    let v3 = vec![1, 2, 3];
    for x in v3.into_iter() {
        print!("{} ", x);  // x: i32
    }
    println!();

    // ===== for 循环语法糖 =====
    // for x in &v 等价于 for x in v.iter()
    // for x in &mut v 等价于 for x in v.iter_mut()
    // for x in v 等价于 for x in v.into_iter()

    // ===== 常用适配器 =====
    let nums = vec![1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

    // map：变换
    let squared: Vec<i32> = nums.iter().map(|x| x * x).collect();
    println!("平方：{:?}", squared);

    // filter：过滤
    let evens: Vec<&i32> = nums.iter().filter(|x| *x % 2 == 0).collect();
    println!("偶数：{:?}", evens);

    // fold：折叠（类似 reduce）
    let sum: i32 = nums.iter().fold(0, |acc, x| acc + x);
    println!("求和：{}", sum);

    // 链式调用
    let result: Vec<i32> = nums.iter()
        .filter(|&&x| x % 2 == 0)     // 偶数
        .map(|&x| x * x)              // 平方
        .collect();
    println!("偶数的平方：{:?}", result);  // [4, 16, 36, 64, 100]

    // ===== 其他常用方法 =====
    // enumerate：带索引
    for (i, x) in nums.iter().enumerate() {
        println!("[{}] = {}", i, x);
    }

    // zip：拉链
    let names = vec!["Alice", "Bob", "Carol"];
    let ages = vec![25, 30, 35];
    let pairs: Vec<(&&str, &i32)> = names.iter().zip(ages.iter()).collect();
    println!("{:?}", pairs);

    // take / skip
    let first3: Vec<&i32> = nums.iter().take(3).collect();
    let skip3: Vec<&i32> = nums.iter().skip(3).collect();
    println!("前3：{:?}", first3);
    println!("跳过3：{:?}", skip3);

    // any / all
    let has_even = nums.iter().any(|x| *x % 2 == 0);
    let all_positive = nums.iter().all(|x| *x > 0);
    println!("有偶数：{}，全正数：{}", has_even, all_positive);

    // find
    let first_even = nums.iter().find(|x| *x % 2 == 0);
    println!("第一个偶数：{:?}", first_even);

    // min / max / sum
    let total: i32 = nums.iter().sum();
    let max = nums.iter().max();
    let min = nums.iter().min();
    println!("sum={}, max={:?}, min={:?}", total, max, min);

    // ===== 自定义迭代器 =====
    let mut counter = Counter::new();
    for c in Counter::new().take(5) {
        println!("计数：{}", c);
    }
}

struct Counter {
    count: u32,
}

impl Counter {
    fn new() -> Counter {
        Counter { count: 0 }
    }
}

impl Iterator for Counter {
    type Item = u32;

    fn next(&mut self) -> Option<Self::Item> {
        if self.count < 5 {
            self.count += 1;
            Some(self.count)
        } else {
            None
        }
    }
}
```

### 总结

- 迭代器是惰性的，`map`/`filter` 等适配器只构建管道，`collect`/`for` 才触发计算
- 三种迭代：`iter()`（`&T`）、`iter_mut()`（`&mut T`）、`into_iter()`（`T`）
- 实现 `Iterator` trait 只需定义 `next` 方法，关联类型 `Item` 指定元素类型
- 迭代器是零成本抽象，编译后与手写循环性能相当
- **常见坑**：`filter` 的闭包参数是 `&&T`（双重引用），因为 `filter` 接收 `&Self::Item`；`collect` 需标注目标类型；迭代器只能消费一次

---

## 第 15 讲：模块系统与包管理

### 概念

Rust 的模块系统管理代码的层次结构与可见性。核心概念有：**crate**（一个编译单元，可以是二进制或库）、**package**（一个 Cargo 项目，包含一到多个 crate）、**module**（模块，组织代码的命名空间）、**path**（路径，如 `crate::foo::bar`）、**use**（导入路径到作用域）、**pub**（公开可见性）。这套系统让大型项目的代码组织井井有条。

### 原理

每个 crate 有一棵模块树，根模块是 `crate`（库 crate 的根是 `lib.rs`，二进制 crate 的根是 `main.rs`）。模块用 `mod` 声明，可以内联定义，也可以指向单独文件。可见性默认私有，需 `pub` 才能被外部访问。`use` 语句类似 Java 的 import 或 Python 的 from-import，但默认私有（只导入到当前作用域），需 `pub use` 才能重导出。路径以 `crate::`（绝对，从根开始）、`self::`（当前模块）、`super::`（父模块）开头。Rust 2018 后支持"路径式"模块声明：`mod foo;` 会查找 `foo.rs` 或 `foo/mod.rs`。

### 例子

项目结构示例：

```
my_project/
├── Cargo.toml
└── src/
    ├── main.rs        # 二进制 crate 根
    ├── lib.rs         # 库 crate 根（可选）
    ├── config.rs      # 子模块
    └── network/
        ├── mod.rs     # network 模块
        ├── server.rs  # network::server
        └── client.rs  # network::client
```

`src/main.rs`：

```rust
// 声明子模块（指向 src/config.rs 和 src/network/mod.rs）
mod config;
mod network;

use network::server::start_server;   // 导入路径
use config::Config;

fn main() {
    let cfg = Config::load();
    println!("配置：{:?}", cfg);
    start_server(cfg.port);
}
```

`src/config.rs`：

```rust
#[derive(Debug)]
pub struct Config {
    pub port: u16,
    pub host: String,
}

impl Config {
    pub fn load() -> Self {
        Config {
            port: 8080,
            host: String::from("0.0.0.0"),
        }
    }
}

// 私有函数，模块外不可访问
fn parse_env() -> Option<String> {
    None
}
```

`src/network/mod.rs`：

```rust
pub mod server;   // 指向 network/server.rs
pub mod client;   // 指向 network/client.rs

pub fn ping() {
    println!("ping");
}
```

`src/network/server.rs`：

```rust
pub fn start_server(port: u16) {
    println!("服务器启动在端口 {}", port);
    super::ping();   // super:: 访问父模块
}

// 内部函数
fn handle_request() {
    println!("处理请求");
}
```

使用外部 crate（在 `Cargo.toml` 添加依赖后）：

```rust
// Cargo.toml: serde = { version = "1.0", features = ["derive"] }
use serde::{Serialize, Deserialize};

#[derive(Serialize, Deserialize, Debug)]
struct User {
    name: String,
    age: u32,
}

fn main() {
    let u = User { name: "Alice".into(), age: 30 };
    let json = serde_json::to_string(&u).unwrap();
    println!("{}", json);
}
```

`pub use` 重导出：

```rust
// 在 lib.rs 中
mod internal;
pub use internal::PublicType;   // 外部可直接用 my_crate::PublicType
```

### 总结

- crate 是编译单元，package 是 Cargo 项目；一个 package 最多一个库 crate + 多个二进制 crate
- 模块用 `mod` 声明，可内联或指向文件（`foo.rs` 或 `foo/mod.rs`）
- 默认私有，`pub` 公开；`pub(crate)` 限制在 crate 内可见
- `use` 导入路径，`pub use` 重导出；路径前缀 `crate::`/`self::`/`super::`
- **常见坑**：模块路径与文件系统对应关系容易混淆；`use` 默认私有不重导出；结构体字段即使结构体是 `pub`，字段仍需单独标 `pub`

---

# 第 5 章：trait 与泛型

本章是 Rust 抽象能力的核心。trait 定义类型能"做什么"，泛型让代码适用于多种类型。两者结合——trait 约束的泛型——是 Rust 实现多态的主要方式。我们还会讨论静态分发与动态分发的取舍，以及生命周期在泛型中的角色。掌握本章，你就能写出既灵活又高效的通用代码。

## 第 16 讲：方法与 trait 基础

### 概念

**方法（Method）**是定义在类型上的函数，通过 `impl` 块关联，第一个参数是 `self`/`&self`/`&mut self`。**trait** 是 Rust 对"行为契约"的抽象，类似 Java 的接口或 Haskell 的 typeclass。trait 定义一组方法签名，类型通过 `impl Trait for Type` 实现这些方法。trait 可以有默认实现，也可以要求实现者提供具体实现。

### 原理

Rust 的 trait 与面向对象语言的接口有关键区别：① trait 支持**默认方法**（default method），实现者可选择覆盖或复用；② trait 可以有**关联类型**（associated type），表示"实现时才确定的类型"；③ 一个类型可以实现多个 trait，没有"基类"概念；④ trait 可以作为参数类型（`impl Trait`）或 trait 对象（`dyn Trait`）。trait 实现必须满足"孤儿规则"（orphan rule）：要么 trait 在当前 crate 定义，要么类型在当前 crate 定义，避免冲突。这种规则保证了 trait 组合的安全性。

### 例子

```rust
// ===== 定义 trait =====
trait Animal {
    fn name(&self) -> String;
    fn sound(&self) -> String;

    // 默认方法
    fn introduce(&self) -> String {
        format!("我是{}，我会叫：{}", self.name(), self.sound())
    }
}

// ===== 实现 trait =====
struct Dog {
    name: String,
}

impl Animal for Dog {
    fn name(&self) -> String {
        self.name.clone()
    }
    fn sound(&self) -> String {
        String::from("汪汪")
    }
    // 不实现 introduce，使用默认
}

struct Cat {
    name: String,
}

impl Animal for Cat {
    fn name(&self) -> String {
        self.name.clone()
    }
    fn sound(&self) -> String {
        String::from("喵喵")
    }
    // 覆盖默认方法
    fn introduce(&self) -> String {
        format!("本喵叫{}，懒得理你", self.name())
    }
}

fn main() {
    let dog = Dog { name: "旺财".into() };
    let cat = Cat { name: "咪咪".into() };

    println!("{}", dog.introduce());
    println!("{}", cat.introduce());
}

// ===== trait 作为参数 =====
// 方式1：impl Trait 语法（静态分发）
fn print_animal(a: &impl Animal) {
    println!("{}", a.introduce());
}

// 方式2：泛型 + trait 约束（等价于上面）
fn print_animal_generic<T: Animal>(a: &T) {
    println!("{}", a.introduce());
}

// 方式3：trait 对象（动态分发）
fn print_animal_dyn(a: &dyn Animal) {
    println!("{}", a.introduce());
}

// ===== 关联类型 =====
trait Container {
    type Item;   // 关联类型

    fn first(&self) -> Option<Self::Item>;
    fn add(&mut self, item: Self::Item);
}

struct NumberBag {
    items: Vec<i32>,
}

impl Container for NumberBag {
    type Item = i32;   // 指定关联类型

    fn first(&self) -> Option<Self::Item> {
        self.items.first().copied()
    }
    fn add(&mut self, item: Self::Item) {
        self.items.push(item);
    }
}

// ===== 派生 trait =====
#[derive(Debug, Clone, PartialEq, Eq)]
struct Point {
    x: i32,
    y: i32,
}

fn main2() {
    let p1 = Point { x: 1, y: 2 };
    let p2 = p1.clone();
    println!("{:?}", p1);
    println!("相等：{}", p1 == p2);
}

// ===== Display trait：自定义打印格式 =====
use std::fmt;

impl fmt::Display for Point {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "({}, {})", self.x, self.y)
    }
}

fn main3() {
    let p = Point { x: 3, y: 4 };
    println!("{}", p);   // 用 Display
    println!("{:?}", p); // 用 Debug
}
```

### 总结

- trait 定义行为契约，类型用 `impl Trait for Type` 实现
- trait 可有默认方法，实现者可选择性覆盖
- 关联类型 `type Item;` 让 trait 表达"实现时才确定的类型"
- 孤儿规则：trait 或类型至少有一个在当前 crate 定义才能 impl
- **常见坑**：`Display` 需手写，`Debug` 可 derive；`PartialEq`/`Eq` 区别在于 `Eq` 要求自反性（浮点数只能 `PartialEq`）；trait 对象 `&dyn Trait` 有运行时开销

---

## 第 17 讲：泛型

### 概念

**泛型（Generics）**让代码适用于多种类型，是 Rust 实现代码复用的核心机制。泛型可用于函数、结构体、枚举、impl 块。泛型参数用 `<T>` 声明，通常配合 trait 约束（`<T: SomeTrait>`）限制可用类型。Rust 泛型采用**单态化**（monomorphization）——编译期为每种具体类型生成专用代码，运行时零开销。

### 原理

单态化是 Rust 泛型性能的关键。当你写 `fn max<T: Ord>(a: T, b: T) -> T`，并在代码中调用 `max(1, 2)` 和 `max(1.0, 2.0)`，编译器会生成两个版本：`max_i32` 和 `max_f64`。这意味着泛型代码运行时与手写专用代码一样快，但代价是二进制体积增大（每种类型一份代码）。trait 约束（bound）告诉编译器泛型类型必须支持哪些操作，例如 `<T: Display + Clone>` 要求 `T` 同时实现 `Display` 和 `Clone`。`where` 子句让复杂约束更易读。

### 例子

```rust
// ===== 泛型函数 =====
fn max<T: PartialOrd>(a: T, b: T) -> T {
    if a > b { a } else { b }
}

fn main() {
    println!("{}", max(3, 7));          // i32
    println!("{}", max(3.14, 2.71));    // f64
    println!("{}", max('a', 'z'));      // char
}

// ===== 泛型结构体 =====
struct Pair<T> {
    first: T,
    second: T,
}

impl<T> Pair<T> {
    fn new(first: T, second: T) -> Self {
        Pair { first, second }
    }
}

// 条件方法：只有 T 实现了 PartialOrd + Display 才有此方法
impl<T: PartialOrd + Display> Pair<T> {
    fn larger(&self) -> &T {
        if self.first >= self.second {
            &self.first
        } else {
            &self.second
        }
    }
}

// ===== 泛型枚举（标准库的 Option 和 Result 就是泛型枚举）=====
enum MyResult<T, E> {
    Ok(T),
    Err(E),
}

// ===== 多类型参数 =====
struct KeyValue<K, V> {
    key: K,
    value: V,
}

// ===== trait 约束 =====
// 方式1：直接在泛型参数后
fn print_twice<T: std::fmt::Display>(x: T) {
    println!("{} {}", x, x);
}

// 方式2：where 子句（约束复杂时更清晰）
fn print_diff<T, U>(a: T, b: U)
where
    T: std::fmt::Display,
    U: std::fmt::Debug,
{
    println!("Display: {}, Debug: {:?}", a, b);
}

// ===== 多重约束 =====
fn process<T: Clone + std::fmt::Debug>(x: &T) -> T {
    let cloned = x.clone();
    println!("处理：{:?}", cloned);
    cloned
}

// ===== impl Trait 语法（泛型的语法糖）=====
// 这两个函数等价
fn double1(x: impl Fn(i32) -> i32) -> i32 { x(2) }
fn double2<F: Fn(i32) -> i32>(x: F) -> i32 { x(2) }

// ===== 泛型与生命周期组合 =====
fn longest_in<'a, T>(items: &'a [T]) -> Option<&'a T>
where
    T: PartialOrd,
{
    items.iter().max()
}

fn main2() {
    let nums = vec![3, 1, 4, 1, 5, 9, 2, 6];
    let m = longest_in(&nums);
    println!("最大：{:?}", m);

    let p1 = Pair::new(3, 7);
    println!("较大：{}", p1.larger());

    let p2 = Pair::new("hello", "world");
    println!("较大：{}", p2.larger());
}
```

### 总结

- 泛型用 `<T>` 声明，单态化在编译期为每种类型生成专用代码，运行时零开销
- trait 约束 `<T: Trait>` 限制泛型类型必须支持的操作；多重约束用 `+` 连接
- `where` 子句让复杂约束更易读
- `impl Trait` 是泛型 + trait 约束的语法糖，常用于闭包参数
- **常见坑**：泛型会导致二进制膨胀（每种类型一份代码）；不同具体类型的泛型实例是不同类型；泛型函数的 trait 约束必须能从函数体内推导出来

---

## 第 18 讲：trait 对象与静态/动态分发

### 概念

当需要在运行期处理多种实现了同一 trait 的类型时，有两种方式：**静态分发**（编译期确定具体类型，通过泛型实现）和**动态分发**（运行期通过 trait 对象确定，用 `dyn Trait` 表示）。trait 对象是一个胖指针，包含数据指针和虚表（vtable）指针。静态分发零开销但二进制大，动态分发有虚函数调用开销但更灵活。

### 原理

静态分发的核心是单态化——编译期为每种具体类型生成专用代码，调用直接内联，无运行时开销。动态分发通过 trait 对象实现：trait 对象 `&dyn Trait` 内部是 `(data_ptr, vtable_ptr)`，调用方法时通过 vtable 间接寻址。trait 对象要求 trait 是**对象安全**（object safe）的：① trait 方法不能返回 `Self`；② trait 方法不能有泛型参数；③ trait 不能有 `Sized` 作为 supertrait（除非显式 `?Sized`）。这是因为 trait 对象在编译期不知道具体类型，无法单态化泛型方法。

### 例子

```rust
trait Shape {
    fn area(&self) -> f64;
    fn name(&self) -> &str;
}

struct Circle { radius: f64 }
struct Rectangle { width: f64, height: f64 }

impl Shape for Circle {
    fn area(&self) -> f64 { 3.14159 * self.radius * self.radius }
    fn name(&self) -> &str { "圆形" }
}

impl Shape for Rectangle {
    fn area(&self) -> f64 { self.width * self.height }
    fn name(&self) -> &str { "矩形" }
}

// ===== 静态分发：泛型 =====
fn print_area_static<T: Shape>(s: &T) {
    println!("{} 面积：{:.2}", s.name(), s.area());
}

// ===== 动态分发：trait 对象 =====
fn print_area_dynamic(s: &dyn Shape) {
    println!("{} 面积：{:.2}", s.name(), s.area());
}

fn main() {
    let c = Circle { radius: 5.0 };
    let r = Rectangle { width: 3.0, height: 4.0 };

    // 静态分发：编译期确定类型
    print_area_static(&c);
    print_area_static(&r);

    // 动态分发：运行期通过 vtable 调用
    print_area_dynamic(&c);
    print_area_dynamic(&r);

    // ===== 用 Vec 存储不同类型（必须用 trait 对象）=====
    let shapes: Vec<Box<dyn Shape>> = vec![
        Box::new(Circle { radius: 1.0 }),
        Box::new(Rectangle { width: 2.0, height: 3.0 }),
        Box::new(Circle { radius: 5.0 }),
    ];

    let total_area: f64 = shapes.iter().map(|s| s.area()).sum();
    println!("总面积：{:.2}", total_area);

    for s in &shapes {
        println!("{}: {:.2}", s.name(), s.area());
    }
}

// ===== 对象安全的 trait =====
trait Drawable: Draw {
    fn draw(&self);
}

// 以下 trait 不是对象安全的（不能创建 &dyn BadTrait）
// trait BadTrait {
//     fn make(&self) -> Self;        // 返回 Self，不安全
//     fn process<T>(&self, x: T);    // 泛型方法，不安全
// }

// 对象安全的 trait
trait GoodTrait {
    fn do_something(&self);   // 无 Self，无泛型
}

// ===== dyn Trait 与泛型的选择 =====
// 用泛型当：性能敏感、类型在编译期已知、想避免虚函数开销
// 用 dyn 当：需要存储多种类型、类型在运行期才确定、二进制体积敏感
```

### 总结

- 静态分发（泛型）：编译期单态化，零运行时开销，但每种类型一份代码
- 动态分发（`dyn Trait`）：运行期通过 vtable 调用，有虚函数开销，但可在集合中存多种类型
- trait 对象 `&dyn Trait` / `Box<dyn Trait>` 是胖指针（数据指针 + vtable 指针）
- 对象安全要求：方法不返回 `Self`、无泛型参数
- **常见坑**：`Box<dyn Trait>` 必须用 `Box`（或 `&`）包裹，因为 trait 对象大小编译期未知；对象不安全的 trait 无法创建 trait 对象；`dyn` 关键字在 Rust 2018 后必须显式写

---

## 第 19 讲：生命周期进阶与 trait 约束

### 概念

当泛型与生命周期结合时，会出现"生命周期参数作为 trait 约束"的场景。**生命周期约束**（lifetime bound）形如 `T: 'a`，表示"类型 `T` 的所有引用生命周期不短于 `'a`"。**高阶 trait 约束**（HRTB, Higher-Rank Trait Bounds）形如 `for<'a> F: Fn(&'a str)`，表示"对所有生命周期 `'a`，`F` 都实现 `Fn(&'a str)`"。这些进阶机制让 Rust 能表达复杂的引用关系。

### 原理

`T: 'a` 约束的本质是：如果 `T` 内部包含引用，那些引用的生命周期必须不短于 `'a`。这确保了"持有 `T` 的容器不会比 `T` 内部的引用活得更久"。例如 `struct Ref<'a, T: 'a>(&'a T)` 表示 `T` 内部的引用至少要活 `'a` 久。HRTB `for<'a>` 用于表达"对任意生命周期都成立"，常见于闭包类型——一个闭包 `Fn(&str) -> &str` 实际上是 `for<'a> Fn(&'a str) -> &'a str`，表示它能处理任意生命周期的输入。这些约束让 Rust 编译器能精确追踪引用的有效性。

### 例子

```rust
use std::fmt::Debug;

// ===== 结构体持有引用 + 泛型 =====
struct RefCell<'a, T: 'a> {
    data: &'a T,
}

// ===== T: 'a 约束 =====
// 表示 T 内部的引用（如果有）至少活 'a 久
fn print_ref<'a, T: Debug + 'a>(x: &'a T) {
    println!("{:?}", x);
}

// ===== 高阶 trait 约束（HRTB）=====
// for<'a> 表示"对所有生命周期 'a"
fn apply_fn<F>(f: F)
where
    F: for<'a> Fn(&'a str) -> &'a str,
{
    let s = String::from("hello");
    let result = f(&s);
    println!("{}", result);
}

fn main() {
    let s = String::from("data");
    let r = RefCell { data: &s };
    println!("{:?}", r.data);

    // HRTB 例子
    let identity = |s: &str| s;
    apply_fn(identity);
}

// ===== trait 中的生命周期 =====
trait Parser {
    // 关联类型带生命周期
    type Output<'a> where Self: 'a;
    fn parse<'a>(&'a self, input: &'a str) -> Self::Output<'a>;
}

struct WordParser;

impl Parser for WordParser {
    type Output<'a> = Vec<&'a str>;

    fn parse<'a>(&'a self, input: &'a str) -> Self::Output<'a> {
        input.split_whitespace().collect()
    }
}

// ===== 复杂生命周期场景 =====
// 返回的引用生命周期 = 较短的那个
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}

// 不同生命周期参数
fn first_word<'a>(s: &'a str) -> &'a str {
    match s.find(' ') {
        Some(i) => &s[..i],
        None => s,
    }
}

// ===== Inference 与省略 =====
// 大多数场景编译器自动推断，无需手写
fn add_one(x: &i32) -> i32 {
    x + 1
}

// ===== 'static 约束 =====
fn static_only<T: 'static>(x: T) {
    // T 必须不含非 'static 引用
    let _ = x;
}

fn main2() {
    let s: &'static str = "字面量";  // 'static
    static_only(s);
    static_only(42);                 // i32: 'static
    static_only(String::from("a"));  // String: 'static

    // let local = String::from("local");
    // let r = &local;
    // static_only(r);  // 错误：&local 不是 'static
}
```

### 总结

- `T: 'a` 约束表示 `T` 内部引用（若有）至少活 `'a` 久
- HRTB `for<'a>` 表达"对所有生命周期成立"，常用于闭包类型
- trait 可有带生命周期的关联类型（GAT，generic associated types，Rust 1.65 稳定）
- `'static` 约束表示"无非 'static 引用"，整型、`String`、`'static str` 都满足
- **常见坑**：不要滥用 `'static` 解决生命周期错误；GAT 语法 `type Output<'a> where Self: 'a;` 较新，老代码可能用别的方式绕过；闭包返回引用时编译器自动推断 HRTB

---

# 第 6 章：并发编程

Rust 的并发编程模型是其一大亮点——"无畏并发"（fearless concurrency）。借助所有权和类型系统，Rust 在编译期就能防止数据竞争，让并发代码既高效又安全。本章涵盖线程、消息传递、共享状态、`Send`/`Sync` 标记 trait，以及现代 Rust 的异步编程模型 `async/await`。

## 第 20 讲：线程与消息传递

### 概念

Rust 标准库通过 `std::thread::spawn` 创建原生 OS 线程。线程间通信推荐使用**消息传递**（message passing）模式——通过**通道**（channel）在线程间发送数据，而非共享内存。`std::sync::mpsc` 提供多生产者单消费者通道（MPSC），是 Rust 并发的经典范式。"不要通过共享内存来通信，而要通过通信来共享内存"是这一模式的哲学。

### 原理

`thread::spawn` 接收一个闭包，在新 OS 线程中执行。闭包默认按引用捕获环境变量，但线程可能比创建者活得更久，因此 `spawn` 要求闭包是 `'static` 的——必须用 `move` 关键字强制获取所有权。通道由发送端 `Sender<T>` 和接收端 `Receiver<T>` 组成，发送端 `send()` 会转移数据的所有权到接收线程，从根本上避免共享可变状态。`mpsc` 通道是异步的（发送方不阻塞），接收端 `recv()` 会阻塞直到收到数据或所有发送端关闭。当 `Sender` 被 drop 时，接收端会收到错误表示通道关闭。

### 例子

```rust
use std::thread;
use std::sync::mpsc;
use std::time::Duration;

fn main() {
    // ===== 基本线程 =====
    thread::spawn(|| {
        for i in 1..10 {
            println!("子线程：{}", i);
            thread::sleep(Duration::from_millis(1));
        }
    });

    for i in 1..5 {
        println!("主线程：{}", i);
        thread::sleep(Duration::from_millis(1));
    }
    // 注意：主线程结束时子线程可能被强制终止

    // ===== 等待线程结束：JoinHandle =====
    let handle = thread::spawn(|| {
        println!("子线程工作中...");
        thread::sleep(Duration::from_millis(500));
        42   // 返回值
    });
    let result = handle.join().unwrap();   // 阻塞等待
    println!("子线程返回：{}", result);

    // ===== move 闭包：捕获所有权 =====
    let data = vec![1, 2, 3];
    let handle = thread::spawn(move || {
        println!("子线程拿到数据：{:?}", data);
    });
    handle.join().unwrap();
    // println!("{:?}", data);  // 错误：data 已被 move

    // ===== 通道：消息传递 =====
    let (tx, rx) = mpsc::channel();

    thread::spawn(move || {
        let msgs = vec!["hi", "from", "the", "thread"];
        for m in msgs {
            tx.send(m).unwrap();
            thread::sleep(Duration::from_millis(100));
        }
    });

    for received in rx {   // 迭代接收，直到通道关闭
        println!("收到：{}", received);
    }

    // ===== 多生产者（clone Sender）=====
    let (tx, rx) = mpsc::channel();
    let tx1 = tx.clone();
    let tx2 = tx.clone();

    thread::spawn(move || {
        tx1.send("来自线程1").unwrap();
    });
    thread::spawn(move || {
        tx2.send("来自线程2").unwrap();
    });

    // tx 在这里 drop，否则 rx 不会结束
    drop(tx);

    for received in rx {
        println!("收到：{}", received);
    }

    // ===== 同步通道（有界缓冲）=====
    let (tx, rx) = mpsc::sync_channel(1);   // 缓冲区大小 1
    thread::spawn(move || {
        for i in 1..5 {
            tx.send(i).unwrap();
            println!("发送 {}", i);
        }
    });
    thread::sleep(Duration::from_millis(500));
    for received in rx {
        println!("接收 {}", received);
    }
}
```

### 总结

- `thread::spawn(move || {...})` 创建线程，`move` 强制获取捕获变量所有权
- `JoinHandle::join()` 阻塞等待线程结束并获取返回值
- `mpsc::channel()` 创建异步通道，`send()` 转移所有权，`recv()` 阻塞接收
- `sync_channel(n)` 创建有界同步通道，缓冲满时发送方阻塞
- **常见坑**：闭包必须 `move`（要求 `'static`）；忘记 drop `Sender` 会导致 `rx` 永远阻塞；`recv()` 返回 `Result`，需处理通道关闭情况

---

## 第 21 讲：共享状态与 Mutex

### 概念

当多个线程需要访问同一份可变数据时，使用 `Mutex<T>`（互斥锁）保证安全。`Mutex` 内部封装数据，访问前必须调用 `lock()` 获取锁，访问完自动释放。为了在多线程间共享 `Mutex` 的所有权，需配合 `Arc<T>`（原子引用计数）。`Arc<Mutex<T>>` 是 Rust 共享可变状态的标准组合。

### 原理

`Mutex::lock()` 返回 `MutexGuard`，它是一个 RAII 守卫——`Drop` 时自动释放锁，即使发生 panic 也能保证解锁（除非 panic 时设置了 abort）。`MutexGuard` 通过 `Deref`/`DerefMut` 提供对内部数据的访问。`Arc`（Atomic Reference Counted）通过原子操作实现线程安全的引用计数，多个线程可同时持有同一 `Arc`。`Arc<Mutex<T>>` 的模式：`Arc` 让多线程共享 `Mutex` 的所有权，`Mutex` 让访问串行化。注意 `Rc<T>` 不是线程安全的，不能跨线程发送。

### 例子

```rust
use std::sync::{Arc, Mutex};
use std::thread;

fn main() {
    // ===== 单线程 Mutex =====
    let m = Mutex::new(5);
    {
        let mut num = m.lock().unwrap();   // 获取锁
        *num = 6;                          // 修改数据
    }   // num 离开作用域，自动释放锁
    println!("m = {:?}", m);

    // ===== 多线程共享：Arc<Mutex<T>> =====
    let counter = Arc::new(Mutex::new(0));
    let mut handles = vec![];

    for _ in 0..10 {
        let counter = Arc::clone(&counter);
        let handle = thread::spawn(move || {
            let mut num = counter.lock().unwrap();
            *num += 1;
        });
        handles.push(handle);
    }

    for handle in handles {
        handle.join().unwrap();
    }

    println!("最终计数：{}", *counter.lock().unwrap());  // 10

    // ===== 共享复杂结构 =====
    let shared_list = Arc::new(Mutex::new(vec![]));

    let mut handles = vec![];
    for i in 0..5 {
        let list = Arc::clone(&shared_list);
        handles.push(thread::spawn(move || {
            let mut data = list.lock().unwrap();
            data.push(format!("来自线程 {}", i));
        }));
    }
    for h in handles { h.join().unwrap(); }

    println!("结果：{:?}", shared_list.lock().unwrap());

    // ===== RwLock：读写锁 =====
    use std::sync::RwLock;
    let lock = RwLock::new(5);

    // 多个读锁可以同时持有
    {
        let r1 = lock.read().unwrap();
        let r2 = lock.read().unwrap();
        println!("读：{} {}", *r1, *r2);
    }   // 读锁释放

    // 只能有一个写锁
    {
        let mut w = lock.write().unwrap();
        *w += 1;
        println!("写：{}", *w);
    }
}
```

### 总结

- `Mutex<T>` 互斥访问内部数据，`lock()` 返回 `MutexGuard`，RAII 自动释放
- `Arc<T>` 原子引用计数，让多线程共享所有权；`Rc<T>` 不能跨线程
- `Arc<Mutex<T>>` 是共享可变状态的标准模式
- `RwLock<T>` 读写锁，多读单写，读多写少场景性能更好
- **常见坑**：`Mutex` 持有期间不要调用可能阻塞的代码（避免死锁）；`lock()` 返回 `Result`，因为前一个持有者可能 panic 导致"中毒"（poisoned）；`Arc::clone` 是浅拷贝只增加计数

---

## 第 22 讲：Send 与 Sync

### 概念

`Send` 和 `Sync` 是两个标记 trait（marker trait），它们没有方法，仅用于向编译器声明类型的线程安全属性。`Send` 表示类型的所有权可以跨线程转移；`Sync` 表示 `&T` 可以跨线程共享（即 `T` 可以被多线程同时引用）。绝大多数类型自动实现这两个 trait，但 `Rc<T>`、`RefCell<T>`、裸指针等不是。

### 原理

`Send` 和 `Sync` 是自动推导的：如果一个类型的所有字段都是 `Send`，那么该类型自动是 `Send`；`Sync` 同理。`Rc<T>` 不是 `Send`（引用计数非原子操作），`RefCell<T>` 不是 `Sync`（运行期借用检查非线程安全）。`Arc<T>` 是 `Send + Sync`（当 `T: Send + Sync`）。`Mutex<T>` 是 `Send + Sync`（当 `T: Send`），而 `RwLock<T>` 要求 `T: Send + Sync`。手动实现 `Send`/`Sync` 是 `unsafe` 的，因为编译器无法验证你的类型真的线程安全。

### 例子

```rust
use std::sync::{Arc, Mutex, RwLock};
use std::cell::RefCell;
use std::rc::Rc;
use std::thread;

fn main() {
    // ===== Send 的类型可以跨线程转移所有权 =====
    let s = String::from("hello");
    thread::spawn(move || {
        println!("{}", s);   // String: Send
    }).join().unwrap();

    let n = 42;
    thread::spawn(move || {
        println!("{}", n);   // i32: Send + Copy
    }).join().unwrap();

    // ===== Rc 不是 Send，不能跨线程 =====
    // let rc = Rc::new(5);
    // thread::spawn(move || {
    //     println!("{}", rc);  // 编译错误：Rc 不是 Send
    // });

    // ===== RefCell 不是 Sync，不能跨线程共享 =====
    // let cell = RefCell::new(5);
    // let cell_ref = &cell;
    // thread::spawn(move || {
    //     *cell_ref.borrow_mut() = 10;  // 错误：RefCell 不是 Sync
    // });

    // ===== Arc 是 Send + Sync（当 T: Send + Sync）=====
    let arc = Arc::new(5);
    let arc2 = Arc::clone(&arc);
    thread::spawn(move || {
        println!("{}", arc2);
    }).join().unwrap();

    // ===== Mutex<T> 是 Send + Sync（当 T: Send）=====
    let m = Arc::new(Mutex::new(vec![1, 2, 3]));
    let m2 = Arc::clone(&m);
    thread::spawn(move || {
        m2.lock().unwrap().push(4);
    }).join().unwrap();
    println!("{:?}", m.lock().unwrap());

    // ===== 检查类型是否实现 Send/Sync =====
    fn is_send<T: Send>() {}
    fn is_sync<T: Sync>() {}

    is_send::<i32>();
    is_sync::<i32>();
    is_send::<String>();
    is_sync::<String>();
    is_send::<Arc<i32>>();
    is_sync::<Arc<i32>>();
    is_send::<Arc<Mutex<Vec<i32>>>>();
    is_sync::<Arc<Mutex<Vec<i32>>>>();
    // is_send::<Rc<i32>>();       // 编译错误
    // is_sync::<RefCell<i32>>();  // 编译错误
}
```

### 总结

- `Send`：类型所有权可跨线程转移；`Sync`：`&T` 可跨线程共享
- 自动推导：所有字段 `Send` 则类型 `Send`；`Sync` 同理
- `Rc`/`RefCell`/裸指针不是 `Send`/`Sync`；`Arc`/`Mutex`/`RwLock` 是
- 手动实现 `Send`/`Sync` 是 `unsafe` 的，需自己保证线程安全
- **常见坑**：跨线程共享可变数据必须用 `Arc<Mutex<T>>` 或 `Arc<RwLock<T>>`；编译器报"Rc cannot be sent between threads"时改用 `Arc`；`MutexGuard` 不是 `Send`（不能跨线程持有锁）

---

## 第 23 讲：异步编程 async/await

### 概念

`async/await` 是 Rust 的异步编程语法。`async fn` 定义异步函数，返回 `Future` trait 的实现；`.await` 挂起当前任务直到 Future 完成。异步代码看起来像同步代码，但实际是协作式多任务——在 `await` 点让出执行权。Rust 的 Future 是惰性的（创建时不执行），必须由**执行器**（executor）轮询（poll）才会推进。

### 原理

Rust 的异步模型基于**零成本抽象**：`async fn` 被编译为状态机，每个 `.await` 点是一个状态转换。Future 不内置线程，多个 Future 可在单线程上并发执行（通过 `tokio`、`async-std` 等运行时）。`Future::poll` 方法返回 `Poll::Ready(value)` 或 `Poll::Pending`，后者表示"还没好，稍后再 poll"。`Waker` 机制让 Future 在可以继续时通知执行器重新调度。这种设计避免了 OS 线程切换的开销，适合高并发 IO 密集场景。注意：标准库只提供 `Future` trait 和基础原语，运行时需第三方库（最流行的是 `tokio`）。

### 例子

```rust
// Cargo.toml:
// [dependencies]
// tokio = { version = "1", features = ["full"] }

use tokio::{time::sleep, sync::mpsc};
use std::time::Duration;

// ===== async fn 定义异步函数 =====
async fn hello() -> String {
    sleep(Duration::from_millis(100)).await;
    String::from("hello")
}

async fn world() -> String {
    sleep(Duration::from_millis(100)).await;
    String::from("world")
}

// ===== 串行 await =====
async fn sequential() -> String {
    let h = hello().await;
    let w = world().await;
    format!("{} {}", h, w)
}

// ===== 并发 await：join! =====
async fn concurrent() -> String {
    let (h, w) = tokio::join!(hello(), world());  // 并发执行
    format!("{} {}", h, w)
}

#[tokio::main]
async fn main() {
    println!("串行：{}", sequential().await);     // ~200ms
    println!("并发：{}", concurrent().await);     // ~100ms
}

// ===== 异步生成器/流 =====
async fn produce_numbers(tx: mpsc::Sender<i32>) {
    for i in 0..5 {
        tx.send(i).await.unwrap();
        sleep(Duration::from_millis(100)).await;
    }
}

#[tokio::main]
async fn main2() {
    let (tx, mut rx) = mpsc::channel(8);

    tokio::spawn(produce_numbers(tx));

    while let Some(n) = rx.recv().await {
        println!("收到：{}", n);
    }
}

// ===== select!：等待多个 Future 中先完成的 =====
use tokio::select;

#[tokio::main]
async fn main3() {
    let result = select! {
        _ = sleep(Duration::from_millis(50)) => "超时1先到",
        _ = sleep(Duration::from_millis(100)) => "超时2先到",
    };
    println!("{}", result);
}

// ===== async block =====
async fn fetch_data() -> Vec<u8> {
    let data = async {
        sleep(Duration::from_millis(50)).await;
        vec![1, 2, 3]
    }.await;
    data
}

// ===== spawn 创建并发任务 =====
#[tokio::main]
async fn main4() {
    let task1 = tokio::spawn(async {
        sleep(Duration::from_millis(100)).await;
        1
    });
    let task2 = tokio::spawn(async {
        sleep(Duration::from_millis(100)).await;
        2
    });

    let (r1, r2) = tokio::join!(task1, task2);
    println!("{} {}", r1.unwrap(), r2.unwrap());
}
```

### 总结

- `async fn` 返回 `impl Future`，调用它不会执行，需 `.await` 或交给执行器
- `tokio::join!` 并发执行多个 Future；`tokio::spawn` 创建独立并发任务
- `select!` 等待多个 Future 中先完成的，常用于超时控制
- Rust 异步是零成本抽象，编译为状态机；运行时需第三方库（`tokio` 最常用）
- **常见坑**：`.await` 只能在 `async` 函数/块中使用；Future 不轮询不执行；不要在 `async` 中调用阻塞函数（如 `std::thread::sleep`），应用 `tokio::time::sleep`；`async fn` 中的 `?` 处理错误需注意 Future 的错误类型

---

# 第 7 章：高级特性

本章涵盖 Rust 中较为高级的特性：智能指针、不安全 Rust、宏系统，以及类型系统的进阶机制。这些内容在日常编程中不如前几章频繁，但理解它们能让你写出更灵活、更强大的代码，也是阅读标准库和第三方库源码的必备知识。

## 第 24 讲：智能指针：Box、Rc、RefCell

### 概念

**智能指针（Smart Pointer）**是行为类似指针但拥有额外元数据或能力的类型。Rust 最常用的智能指针有：`Box<T>`（堆分配，独占所有权）、`Rc<T>`（引用计数，多所有者共享）、`RefCell<T>`（内部可变性，运行期借用检查）。它们都实现了 `Deref` trait（可像引用一样使用）和 `Drop` trait（离开作用域自动清理）。

### 原理

`Box<T>` 是最简单的智能指针，将数据分配在堆上，栈上只保留指针。它用于：① 在编译期大小未知的类型（如递归类型）；② 转移大型数据时避免拷贝；③ 当你只关心类型实现了某 trait 而非具体类型时（`Box<dyn Trait>`）。`Rc<T>`（Reference Counted）通过非原子引用计数实现多所有者共享，每次 `clone` 增加计数，`drop` 减少，归零时释放数据——但只能用于单线程。`RefCell<T>` 实现"内部可变性"——即使有不可变引用，也能修改内部数据，代价是借用检查推迟到运行期（违反规则会 panic）。`Rc<RefCell<T>>` 是单线程下"共享可变状态"的经典组合。

### 例子

```rust
use std::rc::Rc;
use std::cell::RefCell;

fn main() {
    // ===== Box<T>：堆分配 =====
    let b = Box::new(5);
    println!("b = {}", b);   // 自动 deref，像普通变量一样用

    // 递归类型必须用 Box
    enum List {
        Cons(i32, Box<List>),
        Nil,
    }
    let list = List::Cons(1, Box::new(List::Cons(2, Box::new(List::Nil))));

    // Box<dyn Trait>：trait 对象
    trait Draw { fn draw(&self); }
    struct Button;
    impl Draw for Button { fn draw(&self) { println!("按钮"); } }
    let b: Box<dyn Draw> = Box::new(Button);
    b.draw();

    // ===== Rc<T>：多所有者 =====
    let a = Rc::new(String::from("hello"));
    let b = Rc::clone(&a);   // 引用计数 +1
    let c = Rc::clone(&a);   // 引用计数 +1
    println!("引用计数：{}", Rc::strong_count(&a));  // 3
    println!("{} {} {}", a, b, c);

    // 共享数据结构（如双向链表、图）
    struct Node {
        value: i32,
        next: Option<Rc<RefCell<Node>>>,
    }

    let node1 = Rc::new(RefCell::new(Node { value: 1, next: None }));
    let node2 = Rc::new(RefCell::new(Node { value: 2, next: None }));

    // node1 -> node2
    node1.borrow_mut().next = Some(Rc::clone(&node2));

    // 多个引用指向 node1
    let head1 = Rc::clone(&node1);
    let head2 = Rc::clone(&node1);
    println!("node1 引用计数：{}", Rc::strong_count(&node1));  // 3

    // ===== RefCell<T>：内部可变性 =====
    let data = RefCell::new(vec![1, 2, 3]);

    // 即使 data 是不可变绑定，也能修改内部
    data.borrow_mut().push(4);
    println!("{:?}", data.borrow());

    // 运行期借用检查
    let mut b1 = data.borrow_mut();   // 可变借用
    // let b2 = data.borrow();        // panic！已有可变借用
    b1.push(5);
    println!("{:?}", data.borrow());

    // ===== Rc<RefCell<T>>：共享可变状态（单线程）=====
    let shared = Rc::new(RefCell::new(0));
    let clones: Vec<_> = (0..5).map(|_| Rc::clone(&shared)).collect();

    for c in &clones {
        *c.borrow_mut() += 1;
    }
    println!("最终值：{}", *shared.borrow());  // 5

    // ===== Weak<T>：弱引用，避免循环引用 =====
    use std::rc::Weak;
    let strong = Rc::new(42);
    let weak: Weak<i32> = Rc::downgrade(&strong);

    // weak 不增加强引用计数
    println!("强引用：{}，弱引用：{}", Rc::strong_count(&strong), Rc::weak_count(&strong));

    // 升级 weak 为 Rc（可能失败，如果数据已被释放）
    if let Some(val) = weak.upgrade() {
        println!("弱引用升级成功：{}", val);
    }

    drop(strong);   // 释放数据
    println!("升级结果：{:?}", weak.upgrade());  // None
}
```

### 总结

- `Box<T>` 堆分配，独占所有权；用于递归类型、大对象、trait 对象
- `Rc<T>` 引用计数共享，单线程；`Arc<T>` 是其线程安全版本
- `RefCell<T>` 内部可变性，运行期借用检查；`Mutex<T>` 是其线程安全版本
- `Rc<RefCell<T>>` 是单线程共享可变状态的标准模式
- **常见坑**：`Rc` 不能跨线程（用 `Arc`）；`RefCell` 运行期借用冲突会 panic；`Rc` 循环引用导致内存泄漏，需用 `Weak` 打破

---

## 第 25 讲：不安全 Rust

### 概念

`unsafe` 关键字标记的代码块或函数，可以执行 Rust 安全保证之外的操作。在 `unsafe` 块中可以：① 解引用裸指针；② 调用 unsafe 函数（包括 FFI）；③ 访问/修改可变静态变量；④ 实现 unsafe trait。`unsafe` 并不关闭借用检查器，只是允许上述四类操作。Rust 的安全保证分为"safe Rust"（编译器保证）和"unsafe Rust"（程序员保证）两层。

### 原理

Rust 的安全是分层的：编译器能验证的部分由 safe Rust 覆盖，无法验证的部分（如裸指针解引用、FFI 调用）由 unsafe Rust 处理。unsafe 的责任从编译器转移到程序员——你必须手动确保不违反 Rust 的不变式（如"没有悬垂指针"、"没有数据竞争"）。标准库的 `Vec`、`String`、`Mutex` 等内部都使用了 unsafe，因为它们需要直接操作内存，但对外提供 safe 接口。这种"unsafe 封装在 safe API 后"的模式是 Rust 生态的最佳实践——最小化 unsafe 范围，让安全边界清晰。

### 例子

```rust
// ===== 裸指针 =====
fn main() {
    let mut x = 5;
    let r1 = &x as *const i32;    // 不可变裸指针
    let r2 = &mut x as *mut i32;  // 可变裸指针

    unsafe {
        println!("r1 = {}", *r1);  // 解引用裸指针必须 unsafe
        *r2 = 10;
        println!("x = {}", x);
    }
}

// ===== 调用 unsafe 函数 =====
unsafe fn dangerous() -> i32 {
    42
}

fn main2() {
    let n = unsafe { dangerous() };
    println!("{}", n);
}

// ===== FFI：调用 C 函数 =====
extern "C" {
    fn abs(input: i32) -> i32;
}

fn main3() {
    let n = unsafe { abs(-5) };
    println!("abs(-5) = {}", n);
}

// 从 Rust 导出给 C 调用
#[no_mangle]
pub extern "C" fn call_from_c() {
    println!("C 调用了我！");
}

// ===== 可变静态变量 =====
static mut COUNTER: i32 = 0;

fn increment() {
    unsafe {
        COUNTER += 1;
    }
}

fn main4() {
    increment();
    increment();
    unsafe {
        println!("计数：{}", COUNTER);
    }
}

// ===== 实现 unsafe trait =====
// 某些 trait 是 unsafe 的，因为编译器无法验证实现是否正确
unsafe trait MyTrait {
    fn do_something(&self);
}

struct MyType;

unsafe impl MyTrait for MyType {
    fn do_something(&self) {
        println!("实现 unsafe trait");
    }
}

// ===== Split：安全封装 unsafe =====
// 标准库的 split_at_mut 内部用 unsafe，但对外是 safe 的
fn split_at_mut(values: &mut [i32], mid: usize) -> (&mut [i32], &mut [i32]) {
    let len = values.len();
    assert!(mid <= len);

    let ptr = values.as_mut_ptr();

    // 编译器无法知道两个切片不重叠，需 unsafe
    unsafe {
        (
            std::slice::from_raw_parts_mut(ptr, mid),
            std::slice::from_raw_parts_mut(ptr.add(mid), len - mid),
        )
    }
}

fn main5() {
    let mut v = vec![1, 2, 3, 4, 5];
    let (a, b) = split_at_mut(&mut v, 2);
    println!("{:?} {:?}", a, b);
}
```

### 总结

- `unsafe` 允许四类操作：解引用裸指针、调用 unsafe 函数/FFI、访问可变 static、实现 unsafe trait
- unsafe 不关闭借用检查，只是放宽特定操作的限制
- 最佳实践：将 unsafe 封装在 safe API 后，最小化 unsafe 范围
- FFI 通过 `extern "C"` 与 C 互操作，`#[no_mangle]` 防止名称修饰
- **常见坑**：能用 safe 就不要用 unsafe；unsafe 代码出错（如悬垂指针）会导致未定义行为（UB），编译器不负责；`unsafe fn` 调用必须包在 `unsafe` 块中

---

## 第 26 讲：宏系统

### 概念

Rust 宏分为两类：① **声明宏（declarative macro）**，用 `macro_rules!` 定义，基于模式匹配展开；② **过程宏（procedural macro）**，用 Rust 代码编写，操作 token 流，分为派生宏（`#[derive]`）、属性宏、函数宏三种。宏在编译期展开为代码，与泛型（运行期单态化）不同。`println!`、`vec!`、`format!` 都是声明宏。

### 原理

声明宏的工作方式类似 `match`：匹配输入 token 模式，展开为输出代码。模式中的"重复"用 `$x:expr, *` 这样的语法表示（`*` 是零或多次，`+` 是一或多次）。过程宏更强大，接收 `TokenStream` 输入，输出新的 `TokenStream`，可以执行任意 Rust 代码。派生宏（如 `#[derive(Debug)]`）自动为类型实现 trait；属性宏（如 `#[tokio::main]`）修饰整个项；函数宏（如 `sqlx::query!`）像函数调用但参数在编译期处理。宏与函数的根本区别：宏在编译期展开，可生成代码、操作语法结构；函数在运行期执行，只能处理值。

### 例子

```rust
// ===== 声明宏 =====
macro_rules! say_hello {
    () => {
        println!("Hello!");
    };
}

macro_rules! vec_of {
    ($($x:expr),*) => {{
        let mut v = Vec::new();
        $( v.push($x); )*
        v
    }};
}

// 递归宏
macro_rules! hash_map {
    ($($key:expr => $value:expr),*) => {{
        let mut m = std::collections::HashMap::new();
        $( m.insert($key, $value); )*
        m
    }};
}

fn main() {
    say_hello!();
    let v = vec_of!(1, 2, 3, 4);
    println!("{:?}", v);

    let m = hash_map!("a" => 1, "b" => 2);
    println!("{:?}", m);
}

// ===== 带类型的宏 =====
macro_rules! create_function {
    ($func_name:ident, $ret_type:ty) => {
        fn $func_name() -> $ret_type {
            println!("函数 {:?} 被调用", stringify!($func_name));
            Default::default()
        }
    };
}

create_function!(foo, i32);
create_function!(bar, String);

fn main2() {
    println!("{}", foo());   // 0
    println!("{}", bar());  // ""
}

// ===== 标准库宏 =====
fn main3() {
    // println! 是宏，能做编译期格式检查
    let name = "Alice";
    let age = 30;
    println!("{} 今年 {} 岁", name, age);   // 编译期检查参数数量
    println!("{name} 今年 {age} 岁");        // 内联变量（Rust 1.58+）
    println!("{:?}", vec![1, 2, 3]);
    println!("{:#?}", vec![1, 2, 3]);        // 美化输出

    // format! 返回 String
    let s = format!("{} + {} = {}", 1, 2, 3);

    // vec! 创建 Vec
    let v = vec![1, 2, 3];

    // dbg! 调试打印（返回原值）
    let x = dbg!(5 * 2);  // 打印到 stderr，返回 10
    println!("{}", x);

    // stringify! / concat!
    let s = stringify!(hello world);  // "hello world"
    let s = concat!("a", "b", "c");   // "abc"

    // env! / option_env!
    let path = env!("HOME");  // 编译期读取环境变量
    println!("HOME = {}", path);
}

// ===== 过程宏（需单独 crate）=====
// 派生宏示例（在单独的 crate 中）：
// #[proc_macro_derive(HelloMacro)]
// pub fn hello_macro_derive(input: TokenStream) -> TokenStream {
//     // 解析输入，生成 impl HelloMacro for ...
// }

// 使用派生宏：
// #[derive(HelloMacro)]
// struct Pancakes;
// Pancakes::hello_macro();  // 自动生成的方法
```

### 总结

- 声明宏 `macro_rules!` 基于模式匹配展开，适合简单的代码生成
- 过程宏分三种：派生宏、属性宏、函数宏，需单独 crate
- 宏在编译期展开，可生成代码；函数在运行期执行，处理值
- `println!`/`vec!`/`format!`/`dbg!` 都是宏，能做编译期检查
- **常见坑**：宏调试困难，错误信息可能晦涩；宏卫生性（hygiene）让宏内变量不污染调用处；不要滥用宏，能用函数就用函数

---

## 第 27 讲：类型系统进阶

### 概念

Rust 类型系统有几个进阶特性：**newtype 模式**（用元组结构体创建新类型）、**类型别名**（`type` 关键字）、**Never 类型**（`!`，表示永不返回）、**Sized trait**（编译期已知大小）、**运算符重载**（通过实现 trait）、**Deref 强制转换**。这些特性让类型系统既灵活又安全。

### 原理

**newtype 模式**通过 `struct Wrapper(InnerType)` 创建一个新类型，编译器视为不同类型，避免混淆（如 `Meters` 和 `Kilometers` 都是 `f64` 但不能混用）。它零开销——编译后与内部类型内存布局相同。**Never 类型 `!`** 表示"永不返回"的值，如 `panic!`、无限循环、`process::exit()` 的返回类型。`!` 可以转换为任意类型（因为它不会产生值），这让 `match` 分支能灵活组合。**Deref 强制转换**让 `&String` 自动转为 `&str`、`&Vec<T>` 转为 `&[T]`，因为它们实现了 `Deref` trait。**运算符重载**通过实现 `std::ops` 下的 trait（如 `Add`、`Sub`、`Index`）实现。

### 例子

```rust
use std::ops::{Add, Mul};

// ===== Newtype 模式 =====
struct Meters(f64);
struct Kilometers(f64);

impl Meters {
    fn to_kilometers(self) -> Kilometers {
        Kilometers(self.0 / 1000.0)
    }
}

impl Add for Meters {
    type Output = Meters;
    fn add(self, other: Meters) -> Meters {
        Meters(self.0 + other.0)
    }
}

fn main() {
    let d1 = Meters(500.0);
    let d2 = Meters(300.0);
    let total = d1 + d2;   // 重载的 + 运算符
    println!("{} 米", total.0);

    let km = total.to_kilometers();
    println!("{} 公里", km.0);

    // let wrong = d1 + km;  // 编译错误：类型不匹配
}

// ===== 类型别名 =====
type Kilometers2 = f64;   // 只是别名，不是新类型

fn main2() {
    let x: f64 = 5.0;
    let y: Kilometers2 = 10.0;
    let z = x + y;   // 可以相加，因为是同一类型
    println!("{}", z);
}

// ===== Never 类型 ! =====
fn forever() -> ! {   // 永不返回
    loop {
        std::thread::sleep(std::time::Duration::from_secs(1));
    }
}

fn fail_or_return(may_fail: bool) -> i32 {
    if may_fail {
        panic!("失败");   // panic! 返回 !
    } else {
        42   // 返回 i32
    }
    // ! 可以转为任意类型，所以函数返回 i32 没问题
}

// ===== Deref 强制转换 =====
use std::ops::Deref;

struct MyBox<T>(T);

impl<T> Deref for MyBox<T> {
    type Target = T;
    fn deref(&self) -> &Self::Target {
        &self.0
    }
}

fn main3() {
    let s = MyBox(String::from("hello"));
    // Deref 让 &MyBox<String> 自动转为 &String，再转为 &str
    fn hello(name: &str) {
        println!("Hello, {}!", name);
    }
    hello(&s);   // 自动 deref 多次
}

// ===== 运算符重载 =====
#[derive(Debug, Clone, PartialEq)]
struct Vec2 {
    x: f64,
    y: f64,
}

impl Add for Vec2 {
    type Output = Vec2;
    fn add(self, other: Vec2) -> Vec2 {
        Vec2 { x: self.x + other.x, y: self.y + other.y }
    }
}

impl Mul<f64> for Vec2 {   // 乘以标量
    type Output = Vec2;
    fn mul(self, scalar: f64) -> Vec2 {
        Vec2 { x: self.x * scalar, y: self.y * scalar }
    }
}

impl std::ops::Index<usize> for Vec2 {
    type Output = f64;
    fn index(&self, i: usize) -> &f64 {
        match i {
            0 => &self.x,
            1 => &self.y,
            _ => panic!("索引越界"),
        }
    }
}

fn main4() {
    let v1 = Vec2 { x: 1.0, y: 2.0 };
    let v2 = Vec2 { x: 3.0, y: 4.0 };
    let v3 = v1 + v2;          // Vec2 { x: 4.0, y: 6.0 }
    let v4 = v3 * 2.0;         // Vec2 { x: 8.0, y: 12.0 }
    println!("{:?}", v4);
    println!("v4[0] = {}", v4[0]);   // 用 [] 索引
}

// ===== From / Into 转换 =====
struct Celsius(f64);
struct Fahrenheit(f64);

impl From<Celsius> for Fahrenheit {
    fn from(c: Celsius) -> Self {
        Fahrenheit(c.0 * 9.0 / 5.0 + 32.0)
    }
}

fn main5() {
    let c = Celsius(100.0);
    let f: Fahrenheit = c.into();   // 自动转换
    println!("{}°C = {}°F", 100.0, f.0);
}

// ===== ?Sized 约束 =====
// 允许大小未知的类型（如 [T]、str、dyn Trait）作为泛型参数
fn print_size<T: ?Sized>(x: &T) {
    println!("大小：{}", std::mem::size_of_val(x));
}

fn main6() {
    print_size("hello");   // str: ?Sized
    print_size(&[1, 2, 3]); // [i32]: ?Sized
    print_size(&42);        // i32: Sized
}
```

### 总结

- newtype 模式 `struct Wrapper(Inner)` 创建零开销新类型，避免类型混淆
- 类型别名 `type Name = T` 只是别名，不创建新类型
- Never 类型 `!` 表示永不返回，可转为任意类型
- `Deref` 实现"智能指针"行为，支持链式强制转换
- 运算符重载通过实现 `std::ops` 下的 trait（`Add`/`Sub`/`Mul`/`Index` 等）
- **常见坑**：newtype 与类型别名不同，前者是新类型后者是别名；`Deref` 不应滥用，仅用于智能指针语义；`From`/`Into` 互为反向，实现一个自动获得另一个

---

# 第 8 章：工程实践

学完前 7 章，你已经掌握了 Rust 的核心语法与高级特性。本章关注将 Rust 用于真实项目所需的工程能力：测试与文档、Cargo 高级用法与发布流程，以及一个完整的 CLI 工具实战项目。完成本章后，你将具备独立开发、测试、发布 Rust 项目的能力。

## 第 28 讲：测试与文档

### 概念

Rust 内置测试框架，无需第三方库即可编写单元测试和集成测试。测试用 `#[test]` 属性标记，通过 `cargo test` 运行。文档注释 `///` 支持 Markdown，可通过 `cargo doc` 生成 HTML 文档，文档中的代码示例也会被作为测试运行（doctest）。这种"文档即测试"的实践保证了文档示例的正确性。

### 原理

Rust 测试分三类：① **单元测试**（unit test），与代码同文件，放在 `#[cfg(test)] mod tests` 中，可访问私有项；② **集成测试**（integration test），放在 `tests/` 目录，每个文件是独立 crate，只能访问公开 API；③ **文档测试**（doctest），从 `///` 注释中提取代码块运行。`#[test]` 属性告诉编译器这是测试函数，`assert!`/`assert_eq!`/`assert_ne!` 用于断言。`should_panic` 属性验证函数是否按预期 panic。`cargo test` 自动发现并运行所有测试，支持过滤（`cargo test test_name`）和并发执行。

### 例子

```rust
// src/lib.rs

/// 计算两个数的最大公约数
///
/// # 示例
///
/// ```
/// use my_crate::gcd;
/// assert_eq!(gcd(12, 8), 4);
/// assert_eq!(gcd(15, 25), 5);
/// ```
///
/// # Panics
///
/// 当任一参数为零时 panic
pub fn gcd(a: u32, b: u32) -> u32 {
    assert!(a != 0 && b != 0, "参数不能为零");
    if b == 0 { a } else { gcd(b, a % b) }
}

/// 一个简单的加法器
pub struct Adder {
    base: i32,
}

impl Adder {
    /// 创建新的加法器
    ///
    /// ```
    /// use my_crate::Adder;
    /// let adder = Adder::new(10);
    /// assert_eq!(adder.add(5), 15);
    /// ```
    pub fn new(base: i32) -> Self {
        Adder { base }
    }

    /// 加上一个数
    pub fn add(&self, x: i32) -> i32 {
        self.base + x
    }
}

// ===== 单元测试 =====
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_gcd() {
        assert_eq!(gcd(12, 8), 4);
        assert_eq!(gcd(15, 25), 5);
        assert_eq!(gcd(7, 1), 1);
    }

    #[test]
    fn test_gcd_same() {
        assert_eq!(gcd(5, 5), 5);
    }

    #[test]
    #[should_panic(expected = "参数不能为零")]
    fn test_gcd_zero() {
        gcd(0, 5);
    }

    #[test]
    fn test_adder() {
        let adder = Adder::new(10);
        assert_eq!(adder.add(5), 15);
        assert_eq!(adder.add(-3), 7);
    }

    // 测试私有函数
    fn private_helper() -> i32 { 42 }
    #[test]
    fn test_private() {
        assert_eq!(private_helper(), 42);
    }

    // 使用 Result 而非 panic
    #[test]
    fn test_with_result() -> Result<(), String> {
        if 2 + 2 == 4 {
            Ok(())
        } else {
            Err(String::from("数学错误"))
        }
    }
}
```

集成测试 `tests/integration_test.rs`：

```rust
use my_crate::gcd;

#[test]
fn test_gcd_integration() {
    assert_eq!(gcd(48, 36), 12);
}
```

运行测试：

```bash
cargo test                    # 运行所有测试
cargo test test_gcd           # 只运行名字包含 test_gcd 的测试
cargo test -- --nocapture     # 显示 println! 输出
cargo test -- --test-threads=1  # 单线程运行
cargo bench                   # 运行基准测试（需 nightly 或 criterion）
cargo doc --open              # 生成并打开文档
```

### 总结

- 单元测试放 `#[cfg(test)] mod tests`，可访问私有项；集成测试放 `tests/` 目录
- `assert!`/`assert_eq!`/`assert_ne!` 用于断言；`should_panic` 验证 panic
- 文档注释 `///` 支持 Markdown，`cargo doc` 生成 HTML；doctest 自动运行文档中的代码
- 测试函数可返回 `Result<(), E>`，用 `?` 优雅处理错误
- **常见坑**：单元测试中 `use super::*` 导入被测代码；集成测试只能测 `pub` API；doctest 失败会算作测试失败；`cargo test` 默认并发运行测试

---

## 第 29 讲：Cargo 高级用法与发布

### 概念

Cargo 是 Rust 的官方构建工具和包管理器，负责依赖管理、编译、测试、文档生成、发布等全流程。本章深入 Cargo 的高级功能：依赖版本管理、特性（features）、工作空间（workspace）、条件编译、构建脚本（build.rs）、发布到 crates.io。掌握这些，你就能管理大型 Rust 项目并发布自己的库。

### 原理

Cargo 的依赖版本遵循 SemVer（语义化版本）：`"1.0"` 表示 `>=1.0.0, <2.0.0`，`"=1.0.3"` 表示精确版本，`"^1.0"` 等价于 `"1.0"`，`"~1.0.3"` 表示 `>=1.0.3, <1.1.0`。特性（features）让库按需编译，例如 `serde = { version = "1.0", features = ["derive"] }` 启用 serde 的派生功能。工作空间（workspace）让多个相关 crate 共享同一个 `Cargo.lock` 和 `target/` 目录，适合 monorepo。构建脚本 `build.rs` 在编译前运行，用于生成代码、链接 C 库、查询环境。发布到 crates.io 需要账号、API token，并通过 `cargo publish` 上传。

### 例子

`Cargo.toml` 完整示例：

```toml
[package]
name = "my-awesome-lib"
version = "0.2.1"
edition = "2021"
authors = ["Alice <alice@example.com>"]
license = "MIT"
description = "一个超棒的 Rust 库"
repository = "https://github.com/alice/my-awesome-lib"
documentation = "https://docs.rs/my-awesome-lib"
readme = "README.md"
keywords = ["parser", "cli"]
categories = ["command-line-utilities"]

[dependencies]
serde = { version = "1.0", features = ["derive"] }
tokio = { version = "1", optional = true }
log = "0.4"
anyhow = "1.0"

[dev-dependencies]   # 仅测试时使用
criterion = "0.5"
proptest = "1.0"

[features]
default = []
async = ["tokio"]   # 启用 async 特性会引入 tokio

[[bin]]
name = "my-cli"
path = "src/main.rs"

[profile.release]
opt-level = 3
lto = true          # 链接时优化
codegen-units = 1   # 单线程编译，优化更好但更慢
strip = true        # 移除调试符号
```

工作空间 `Cargo.toml`：

```toml
[workspace]
members = [
    "core",
    "cli",
    "server",
    "utils",
]
```

条件编译：

```rust
// src/lib.rs
#[cfg(feature = "async")]
pub async fn fetch_data() -> String {
    tokio::time::sleep(std::time::Duration::from_millis(100)).await;
    String::from("data")
}

#[cfg(not(feature = "async"))]
pub fn fetch_data() -> String {
    std::thread::sleep(std::time::Duration::from_millis(100));
    String::from("data")
}

// 平台特定代码
#[cfg(target_os = "linux")]
fn platform_specific() -> &'static str { "Linux" }

#[cfg(target_os = "windows")]
fn platform_specific() -> &'static str { "Windows" }

#[cfg(target_os = "macos")]
fn platform_specific() -> &'static str { "macOS" }

// 调试 vs 发布
#[cfg(debug_assertions)]
fn debug_only() { println!("仅调试模式"); }
```

构建脚本 `build.rs`：

```rust
// build.rs
fn main() {
    // 读取环境变量
    let version = env!("CARGO_PKG_VERSION");
    println!("cargo:rustc-env=PACKAGE_VERSION={}", version);

    // 重新运行条件
    println!("cargo:rerun-if-changed=config.toml");

    // 链接 C 库
    // println!("cargo:rustc-link-lib=foo");
}
```

发布流程：

```bash
# 1. 登录 crates.io（首次）
cargo login <your-api-token>

# 2. 检查包
cargo package    # 打包到 target/package/
cargo package --list  # 查看将上传的文件

# 3. 试运行（不实际上传）
cargo publish --dry-run

# 4. 正式发布
cargo publish

# 5. 撤回（yank，不影响已下载的，但新项目不会用此版本）
cargo yank --vers 0.2.1
cargo yank --vers 0.2.1 --undo  # 取消 yank
```

常用 Cargo 命令：

```bash
cargo new my_project        # 创建新项目
cargo new --lib my_lib      # 创建库项目
cargo build                 # 编译
cargo build --release       # 发布模式编译
cargo run                   # 编译并运行
cargo check                 # 只检查不生成二进制（更快）
cargo fmt                   # 格式化代码
cargo clippy                # 运行 linter
cargo doc                   # 生成文档
cargo doc --open            # 生成并打开文档
cargo update                # 更新依赖
cargo tree                  # 显示依赖树
cargo edit add serde        # 添加依赖（需 cargo-edit）
cargo outdated              # 检查过时依赖（需 cargo-outdated）
cargo audit                 # 检查安全漏洞（需 cargo-audit）
```

### 总结

- 依赖版本遵循 SemVer：`"1.0"` 兼容更新，`"=1.0.3"` 精确锁定
- features 实现按需编译，`optional = true` 让依赖成为可选特性
- workspace 让多 crate 共享 `Cargo.lock` 和 `target/`，适合 monorepo
- `build.rs` 在编译前运行，用于生成代码、链接 C 库
- 发布到 crates.io：`cargo login` → `cargo publish`
- **常见坑**：`cargo publish` 会上传所有文件，注意 `.gitignore`；版本号必须递增；`cargo yank` 不能删除版本，只是阻止新项目使用；`#[cfg(feature = "...")]` 依赖 `Cargo.toml` 中的 features 定义

---

## 第 30 讲：Rust 项目实战：CLI 工具开发

### 概念

本讲通过一个完整的 CLI（命令行）工具项目，综合运用前 29 讲所学知识。我们将开发一个 `grrs`（grep 的简化版）工具——从文件中搜索包含指定模式的行。项目涉及：项目结构、命令行参数解析、文件 IO、错误处理、测试、文档。使用 `clap` 解析参数、`anyhow` 处理错误、`env_logger` 记录日志。

### 原理

一个生产级 CLI 工具通常包含以下要素：① 参数解析（`clap` 派生宏让定义参数像写结构体一样简单）；② 错误处理（`anyhow::Result` 提供上下文链）；③ 日志（`log` + `env_logger` 分级日志）；④ 测试（单元测试 + 集成测试 + doctest）；⑤ 文档（`README.md` + `///` 注释）；⑥ CI/CD（GitHub Actions 自动测试发布）。Rust 的强类型让 CLI 工具天然可靠——参数类型在编译期检查，错误处理显式且完整，避免了 shell 脚本中常见的"未处理边界情况"问题。

### 例子

`Cargo.toml`：

```toml
[package]
name = "grrs"
version = "0.1.0"
edition = "2021"

[dependencies]
clap = { version = "4", features = ["derive"] }
anyhow = "1.0"
log = "0.4"
env_logger = "0.10"

[dev-dependencies]
assert_cmd = "2"
predicates = "3"
tempfile = "3"
```

`src/main.rs`：

```rust
use clap::Parser;
use anyhow::{Context, Result};
use std::io::{BufRead, BufReader};
use std::fs::File;
use log::{info, debug};

/// 在文件中搜索包含指定模式的行
#[derive(Parser, Debug)]
#[command(name = "grrs", version, about)]
struct Cli {
    /// 要搜索的模式
    pattern: String,

    /// 要搜索的文件路径
    #[arg(value_name = "FILE")]
    path: std::path::PathBuf,

    /// 是否忽略大小写
    #[arg(short, long)]
    ignore_case: bool,

    /// 显示行号
    #[arg(short, long)]
    line_numbers: bool,

    /// 详细输出
    #[arg(short, long)]
    verbose: bool,
}

fn main() -> Result<()> {
    env_logger::init();

    let args = Cli::parse();
    if args.verbose {
        info!("搜索模式: {}，文件: {:?}", args.pattern, args.path);
    }
    debug!("参数: {:?}", args);

    find_matches(&args)
}

fn find_matches(args: &Cli) -> Result<()> {
    let file = File::open(&args.path)
        .with_context(|| format!("无法打开文件: {:?}", args.path))?;

    let reader = BufReader::new(file);
    let pattern = if args.ignore_case {
        args.pattern.to_lowercase()
    } else {
        args.pattern.clone()
    };

    for (line_num, line) in reader.lines().enumerate() {
        let line = line.context("读取行失败")?;
        let check_line = if args.ignore_case {
            line.to_lowercase()
        } else {
            line.clone()
        };

        if check_line.contains(&pattern) {
            if args.line_numbers {
                println!("{}: {}", line_num + 1, line);
            } else {
                println!("{}", line);
            }
        }
    }

    Ok(())
}

// ===== 单元测试 =====
#[cfg(test)]
mod tests {
    use super::*;
    use std::io::Write;
    use tempfile::NamedTempFile;

    fn create_temp_file(content: &str) -> NamedTempFile {
        let mut file = NamedTempFile::new().unwrap();
        writeln!(file, "{}", content).unwrap();
        file
    }

    #[test]
    fn test_find_matches() {
        let file = create_temp_file("hello world\nfoo bar\nhello rust\n");
        let args = Cli {
            pattern: "hello".to_string(),
            path: file.path().to_path_buf(),
            ignore_case: false,
            line_numbers: false,
            verbose: false,
        };
        // 测试逻辑（实际应捕获 stdout）
        assert!(find_matches(&args).is_ok());
    }

    #[test]
    fn test_case_insensitive() {
        let file = create_temp_file("Hello World\nHELLO\n");
        let args = Cli {
            pattern: "hello".to_string(),
            path: file.path().to_path_buf(),
            ignore_case: true,
            line_numbers: false,
            verbose: false,
        };
        assert!(find_matches(&args).is_ok());
    }
}
```

集成测试 `tests/cli.rs`：

```rust
use assert_cmd::Command;
use predicates::prelude::*;
use tempfile::NamedTempFile;
use std::io::Write;

#[test]
fn test_find_pattern() -> Result<(), Box<dyn std::error::Error>> {
    let mut file = NamedTempFile::new()?;
    writeln!(file, "hello world\nfoo bar\nhello rust")?;

    let mut cmd = Command::cargo_bin("grrs")?;
    cmd.arg("hello").arg(file.path());
    cmd.assert()
        .success()
        .stdout(predicate::str::contains("hello world"))
        .stdout(predicate::str::contains("hello rust"))
        .stdout(predicate::str::contains("foo bar").not());

    Ok(())
}

#[test]
fn test_file_not_found() -> Result<(), Box<dyn std::error::Error>> {
    let mut cmd = Command::cargo_bin("grrs")?;
    cmd.arg("pattern").arg("nonexistent.txt");
    cmd.assert()
        .failure()
        .stderr(predicate::str::contains("无法打开文件"));

    Ok(())
}
```

`README.md`：

```markdown
# grrs

一个用 Rust 编写的简易 grep 工具。

## 安装

```bash
cargo install --path .
```

## 使用

```bash
grrs <pattern> <file>
grrs hello test.txt
grrs --ignore-case HELLO test.txt
grrs -n hello test.txt
```

## 选项

- `-i, --ignore-case`：忽略大小写
- `-n, --line-numbers`：显示行号
- `-v, --verbose`：详细输出
```

GitHub Actions CI `.github/workflows/ci.yml`：

```yaml
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: dtolnay/rust-toolchain@stable
      - run: cargo fmt --all -- --check
      - run: cargo clippy -- -D warnings
      - run: cargo test
```

运行效果：

```bash
$ echo -e "hello world\nfoo bar\nhello rust" > test.txt
$ ./grrs hello test.txt
hello world
hello rust
$ ./grrs -n hello test.txt
1: hello world
3: hello rust
$ ./grrs --ignore-case HELLO test.txt
hello world
hello rust
```

### 总结

- CLI 工具开发流程：定义参数（`clap` 派生）→ 实现逻辑 → 错误处理（`anyhow`）→ 测试（单元 + 集成）→ 文档 → CI
- `clap` 派生宏让参数定义像写结构体，自动生成帮助文本
- `anyhow::Result` + `with_context` 提供有意义的错误链
- `assert_cmd` + `predicates` 是 CLI 集成测试的利器
- **常见坑**：大文件用 `BufReader` 逐行读避免内存爆炸；`?` 配合 `anyhow` 让错误传播简洁；CI 中先 `fmt --check` 和 `clippy -D warnings` 保证代码质量

---

## 课程结语

恭喜你完成了 Rust 系统化教程的全部 30 讲！让我们回顾这段学习旅程：

**第 1-4 章** 建立了 Rust 的基础认知：从环境搭建、基本语法，到所有权、借用、生命周期这三大基石，再到结构体、枚举、错误处理、函数、闭包、迭代器、模块系统。这些是 Rust 编程的日常工具。

**第 5-7 章** 深入了 Rust 的抽象与高级能力：trait 与泛型让你写出通用代码，并发编程模型让你安全地利用多核，智能指针、unsafe、宏、类型系统进阶让你能应对各种复杂场景。

**第 8 章** 将所学应用于工程实践：测试、文档、Cargo、发布、CLI 实战，让你具备将 Rust 用于真实项目的能力。

Rust 的学习曲线确实陡峭，尤其是所有权和生命周期的概念，初学时编译器似乎总在与你作对。但请相信，每一次与借用检查器的"斗争"都在帮你避免潜在的内存 bug。当你习惯了 Rust 的思维模式后，会发现它不仅是一门语言，更是一种关于"如何正确编程"的训练。

继续前进的方向：
- **深入异步生态**：学习 `tokio`、`async-std`、`hyper`、`reqwest`、`sqlx`
- **Web 开发**：尝试 `axum`、`actix-web`、`rocket` 等 Web 框架
- **系统编程**：阅读 `std` 源码，学习 `mio`、`tokio` 的底层实现
- **嵌入式**：探索 `no_std` Rust，开发裸机程序
- **WASM**：用 Rust 编译到 WebAssembly，运行在浏览器中
- **参与开源**：从给喜欢的库提 PR 开始，融入 Rust 社区

Rust 的官方资源也是持续学习的好伙伴：
- [The Rust Book](https://doc.rust-lang.org/book/)：官方教程
- [Rust by Example](https://doc.rust-lang.org/rust-by-example/)：通过例子学 Rust
- [Rust Reference](https://doc.rust-lang.org/reference/)：语言参考
- [Cargo Book](https://doc.rust-lang.org/cargo/)：Cargo 完整文档
- [Rustlings](https://github.com/rust-lang/rustlings)：交互式练习

愿你在 Rust 的世界里，写出既快又安全的代码，享受"无畏并发"的乐趣。Happy Rusting!

