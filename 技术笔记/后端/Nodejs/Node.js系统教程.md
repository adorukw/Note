# Node.js 系统教程

> 本教程共 30 讲，分为 7 章，从零基础到生产部署，循序渐进带你掌握 Node.js 完整知识体系。
>
> 每一讲包含四个部分：**概念**（定义核心概念）→ **原理**（解释工作机制）→ **例子**（可运行代码示例）→ **总结**（归纳要点与注意事项）。

---

## 目录

- [第一章：Node.js 基础入门](#第一章nodejs-基础入门)
  - [第 1 讲：Node.js 是什么](#第-1-讲nodejs-是什么)
  - [第 2 讲：环境搭建](#第-2-讲环境搭建)
  - [第 3 讲：第一个 Node.js 程序](#第-3-讲第一个-nodejs-程序)
  - [第 4 讲：全局对象与运行机制概览](#第-4-讲全局对象与运行机制概览)
- [第二章：模块系统与包管理](#第二章模块系统与包管理)
  - [第 5 讲：CommonJS 模块规范](#第-5-讲commonjs-模块规范)
  - [第 6 讲：模块加载机制](#第-6-讲模块加载机制)
  - [第 7 讲：ES Modules](#第-7-讲es-modules)
  - [第 8 讲：npm 与 package.json](#第-8-讲npm-与-packagejson)
- [第三章：核心模块详解](#第三章核心模块详解)
  - [第 9 讲：fs 文件系统模块](#第-9-讲fs-文件系统模块)
  - [第 10 讲：path 路径模块](#第-10-讲path-路径模块)
  - [第 11 讲：os 与 process 模块](#第-11-讲os-与-process-模块)
  - [第 12 讲：events 事件模块](#第-12-讲events-事件模块)
  - [第 13 讲：stream 流模块](#第-13-讲stream-流模块)
- [第四章：异步编程](#第四章异步编程)
  - [第 14 讲：回调函数与回调地狱](#第-14-讲回调函数与回调地狱)
  - [第 15 讲：Promise 详解](#第-15-讲promise-详解)
  - [第 16 讲：async/await](#第-16-讲asyncawait)
  - [第 17 讲：异步错误处理](#第-17-讲异步错误处理)
  - [第 18 讲：事件循环深入理解](#第-18-讲事件循环深入理解)
- [第五章：Web 服务器与 HTTP 开发](#第五章web-服务器与-http-开发)
  - [第 19 讲：http 模块创建服务器](#第-19-讲http-模块创建服务器)
  - [第 20 讲：路由设计](#第-20-讲路由设计)
  - [第 21 讲：Express 框架入门](#第-21-讲express-框架入门)
  - [第 22 讲：Express 中间件机制](#第-22-讲express-中间件机制)
  - [第 23 讲：RESTful API 设计](#第-23-讲restful-api-设计)
- [第六章：数据存储](#第六章数据存储)
  - [第 24 讲：Buffer 与二进制数据](#第-24-讲buffer-与二进制数据)
  - [第 25 讲：文件存储与流式上传](#第-25-讲文件存储与流式上传)
  - [第 26 讲：MongoDB 与 Mongoose](#第-26-讲mongodb-与-mongoose)
- [第七章：进阶与实战](#第七章进阶与实战)
  - [第 27 讲：Cluster 集群](#第-27-讲cluster-集群)
  - [第 28 讲：Worker Threads](#第-28-讲worker-threads)
  - [第 29 讲：性能优化与内存管理](#第-29-讲性能优化与内存管理)
  - [第 30 讲：测试、调试与 PM2 部署](#第-30-讲测试调试与-pm2-部署)

---

## 第一章：Node.js 基础入门

### 第 1 讲：Node.js 是什么

#### 概念

Node.js 是一个基于 Chrome V8 引擎的 **JavaScript 运行时（Runtime）**，它让 JavaScript 能够脱离浏览器在服务器端运行。简单来说，浏览器能跑 JavaScript，是因为浏览器内置了 JS 引擎；Node.js 把这个引擎抽出来，加上文件系统、网络等能力，让 JS 可以写后端程序、命令行工具、甚至桌面应用。

Node.js 由 Ryan Dahl 于 2009 年发布，目前由 OpenJS Foundation 维护，采用 LTS（长期支持版）发布策略，每两年发布一个大版本。

#### 原理

Node.js 的核心由三部分组成：

1. **V8 引擎**：Google 开发的 JavaScript 引擎，负责把 JS 代码编译成机器码执行，速度极快。
2. **libuv 库**：C 语言编写的跨平台异步 I/O 库，提供事件循环（Event Loop）、线程池、网络与文件 I/O 能力。这是 Node.js 异步非阻塞的关键。
3. **核心模块层**：Node.js 内置的 JS 模块（如 `fs`、`http`、`path`），它们封装了底层 C/C++ 能力，暴露给 JS 调用。

Node.js 最大的特点是**单线程 + 事件驱动 + 非阻塞 I/O**。传统服务器（如 Apache）为每个请求分配一个线程，线程开销大；Node.js 用一个主线程处理所有请求，遇到 I/O 操作（读文件、查数据库）时不会等待，而是注册回调后继续处理下一个请求，I/O 完成后通过事件循环通知主线程执行回调。这种模型在 I/O 密集型场景下性能极高。

#### 例子

最经典的例子——用 Node.js 创建一个 HTTP 服务器：

```javascript
// server.js
const http = require('http');

const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hello, Node.js!');
});

server.listen(3000, () => {
  console.log('服务器运行在 http://localhost:3000');
});
```

运行 `node server.js` 后，浏览器访问 `http://localhost:3000` 就能看到响应。短短几行代码就实现了一个 Web 服务器，这在 Java、C# 中需要更多样板代码。

Node.js 的典型应用场景包括：
- **Web 后端 API 服务**：如 Netflix、PayPal、LinkedIn 后台
- **实时应用**：聊天室、在线协作（Socket.io）
- **命令行工具**：Webpack、ESLint、Vue CLI
- **构建工具**：前端工程化的核心
- **微服务**：轻量、启动快、适合容器化

#### 总结

- Node.js = V8 引擎 + libuv + 核心模块，让 JS 跑在服务端
- 核心特性：单线程、事件驱动、非阻塞 I/O
- 适合 I/O 密集型应用，不适合 CPU 密集型（如视频编码、科学计算）
- 学习 Node.js 不仅是学语法（JS 你已经会了），更是学**异步思维**和**系统编程能力**

---

### 第 2 讲：环境搭建

#### 概念

学习 Node.js 的第一步是搭建开发环境。核心包括三部分：**Node.js 运行时**（执行 JS 代码）、**npm/pnpm 包管理器**（管理第三方库）、**代码编辑器**（推荐 VS Code，内置终端与调试器）。

#### 原理

Node.js 官方提供两种发布版本：
- **LTS（Long Term Support）**：长期支持版，稳定，适合生产环境，每 18 个月维护周期
- **Current**：最新版，包含新特性，适合尝鲜

直接从官网下载安装包虽然简单，但存在一个问题：一台电脑上只能装一个版本，切换版本麻烦。所以推荐使用 **nvm（Node Version Manager）** 来管理多版本。

npm 是 Node.js 自带的包管理器（装 Node.js 时自动安装），它从 npm registry（全球最大的开源库仓库）下载包到本地 `node_modules` 目录。pnpm 和 yarn 是 npm 的替代品，速度更快、磁盘占用更小。

#### 例子

**Mac/Linux 安装 nvm 与 Node.js：**

```bash
# 安装 nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

# 重新打开终端后，安装最新 LTS 版本
nvm install --lts
nvm use --lts

# 验证安装
node -v   # 输出如 v20.11.0
npm -v    # 输出如 10.2.4
```

**Windows 用户** 推荐使用 `nvm-windows`（注意这是另一个项目），从 GitHub 下载安装包即可。

**验证安装成功**，创建 `hello.js`：

```javascript
// hello.js
console.log('Node.js 版本:', process.version);
console.log('当前平台:', process.platform);
console.log('Hello, World!');
```

运行：

```bash
node hello.js
```

**推荐配置 npm 国内镜像**（加速下载）：

```bash
npm config set registry https://registry.npmmirror.com
```

**VS Code 推荐插件**：
- ESLint（代码检查）
- Prettier（代码格式化）
- Node.js Extension Pack（Node 开发工具集）

#### 总结

- 推荐用 nvm 管理 Node.js 版本，方便切换
- LTS 版本适合学习和生产，Current 适合尝鲜
- npm 是默认包管理器，pnpm/yarn 是更快的替代
- 国内网络环境下，配置镜像源能大幅提升下载速度
- VS Code 是 Node.js 开发的首选编辑器

---

### 第 3 讲：第一个 Node.js 程序

#### 概念

编写并运行 Node.js 程序有两种方式：**REPL 交互式环境**（适合快速试验）和**执行 JS 文件**（适合正式开发）。本讲通过几个小例子，让你熟悉 Node.js 的开发节奏。

#### 原理

**REPL**（Read-Eval-Print Loop）是一个交互式命令行环境：读取用户输入（Read）→ 执行（Eval）→ 打印结果（Print）→ 循环（Loop）。在终端输入 `node` 回车即可进入。REPL 会自动记住上下文变量，适合做语法试验和 API 查询。

**执行文件**是更常用的方式：`node 文件名.js`。Node.js 会读取文件内容，交给 V8 引擎编译执行。每次修改文件后需要重新运行 `node` 命令。开发时通常配合 `nodemon` 工具实现文件改动自动重启。

Node.js 与浏览器 JS 的关键区别：
- 没有 `window`、`document` 等 BOM/DOM 对象
- 有 `global`（全局对象）、`process`、`__dirname`、`__filename` 等 Node 专属对象
- 可以直接读写文件、监听网络端口

#### 例子

**1. 使用 REPL：**

```bash
$ node
Welcome to Node.js v20.11.0.
Type ".help" for more information.
> 1 + 2
3
> const name = 'Node'
undefined
> name.toUpperCase()
'NODE'
> .exit
```

**2. 命令行参数读取：**

```javascript
// greet.js
// process.argv 是一个数组，保存命令行参数
// 第 0 个是 node 可执行文件路径
// 第 1 个是脚本文件路径
// 第 2 个开始才是用户传入的参数
const args = process.argv.slice(2);
const name = args[0] || 'World';

console.log(`Hello, ${name}!`);
console.log('当前时间:', new Date().toLocaleString());
```

运行：

```bash
node greet.js Alice
# 输出: Hello, Alice!
#       当前时间: 2024/1/15 上午10:30:00
```

**3. 使用 nodemon 自动重启：**

```bash
# 全局安装 nodemon
npm install -g nodemon

# 用 nodemon 运行脚本
nodemon greet.js
# 之后修改 greet.js，nodemon 会自动重启
```

**4. 读取环境变量：**

```javascript
// config.js
// 环境变量通过 process.env 读取
const port = process.env.PORT || 3000;
const mode = process.env.NODE_ENV || 'development';

console.log(`运行模式: ${mode}`);
console.log(`监听端口: ${port}`);
```

运行：

```bash
NODE_ENV=production PORT=8080 node config.js
# 输出: 运行模式: production
#       监听端口: 8080
```

#### 总结

- REPL 适合快速试验语法和 API，输入 `node` 即可进入
- 执行文件用 `node 文件名.js`，是日常开发的主要方式
- `process.argv` 读取命令行参数，`process.env` 读取环境变量
- 开发时用 `nodemon` 实现自动重启，提升效率
- Node.js 没有 DOM/BOM，但有 `process`、`__dirname` 等服务端专属对象

---

### 第 4 讲：全局对象与运行机制概览

#### 概念

Node.js 提供了一批**全局对象**（Global Objects），它们在任何模块中都可以直接使用，无需 `require`。理解这些全局对象是掌握 Node.js 的基础。同时，本讲会概览 Node.js 的运行机制——**事件循环**，为后续深入学习打下基础。

#### 原理

Node.js 的全局对象分两类：
1. **真正的全局对象**：如 `global`、`process`、`console`、`setTimeout`、`Buffer`、`queueMicrotask` 等，无论是否在模块内都可直接使用。
2. **模块级全局变量**：如 `__dirname`、`__filename`、`exports`、`module`、`require`，它们看起来像全局，实际上是每个模块的局部变量（由 CommonJS 包装器注入）。

**事件循环（Event Loop）** 是 Node.js 的心脏。它是一个无限循环，不断从任务队列中取出回调执行。事件循环分为六个阶段：

1. **timers**：执行到期的 `setTimeout`/`setInterval` 回调
2. **pending callbacks**：执行系统级回调（如 TCP 错误）
3. **idle, prepare**：内部使用
4. **poll**：获取新的 I/O 事件，执行 I/O 回调
5. **check**：执行 `setImmediate` 回调
6. **close callbacks**：执行 close 事件回调（如 `socket.on('close')`）

每个阶段有自己的回调队列，执行完当前阶段的所有回调后才进入下一阶段。这种设计保证了 I/O 回调优先级合理。

#### 例子

**1. 常用全局对象：**

```javascript
// globals.js

// console - 控制台输出
console.log('普通日志');
console.error('错误日志');
console.warn('警告');
console.table([{a:1,b:2}, {a:3,b:4}]);  // 表格输出

// process - 进程对象
console.log('进程 PID:', process.pid);
console.log('Node 版本:', process.version);
console.log('当前目录:', process.cwd());

// setTimeout / setInterval - 定时器
setTimeout(() => console.log('2秒后执行'), 2000);
const timer = setInterval(() => console.log('每秒一次'), 1000);
setTimeout(() => clearInterval(timer), 5000);  // 5秒后停止

// __dirname / __filename - 模块级变量
console.log('当前文件:', __filename);
console.log('所在目录:', __dirname);
```

**2. global 与 globalThis：**

```javascript
// global 是 Node.js 的全局对象（类似浏览器的 window）
// globalThis 是 ES2020 标准的全局对象引用（跨环境统一）
global.myVar = '我是全局变量';
console.log(globalThis.myVar);  // '我是全局变量'

// 注意：在模块中直接声明变量不会挂到 global 上
var localVar = '局部变量';
console.log(global.localVar);  // undefined
```

**3. 事件循环阶段演示：**

```javascript
// event-loop.js
console.log('1. 同步代码开始');

setTimeout(() => {
  console.log('4. setTimeout 回调（timers 阶段）');
}, 0);

setImmediate(() => {
  console.log('5. setImmediate 回调（check 阶段）');
});

Promise.resolve().then(() => {
  console.log('3. Promise 微任务');
});

process.nextTick(() => {
  console.log('2. nextTick 微任务（优先级最高）');
});

console.log('1.5 同步代码结束');

// 输出顺序：
// 1. 同步代码开始
// 1.5 同步代码结束
// 2. nextTick 微任务（优先级最高）
// 3. Promise 微任务
// 4. setTimeout 回调（timers 阶段）  ← 注意：4 和 5 的顺序不固定
// 5. setImmediate 回调（check 阶段）
```

#### 总结

- 全局对象无需 `require` 即可使用，分真正全局和模块级两类
- `process` 是最重要的全局对象，提供进程信息与控制能力
- `__dirname`/`__filename` 是模块级变量，不是真正的全局
- 事件循环是 Node.js 异步的核心，分六个阶段
- `process.nextTick` 优先级最高，Promise 次之，都在主任务之后、定时器之前执行
- 不要滥用 `global` 挂载变量，会造成全局污染和内存泄漏

---

## 第二章：模块系统与包管理

### 第 5 讲：CommonJS 模块规范

#### 概念

**模块（Module）** 是 Node.js 组织代码的基本单位。一个文件就是一个模块，模块内部定义的变量、函数、类默认是私有的，不会污染全局作用域。**CommonJS** 是 Node.js 默认的模块规范，它定义了三个核心 API：`require`（导入）、`exports`/`module.exports`（导出）、`module`（模块信息）。

#### 原理

CommonJS 的设计哲学是"每个模块都是一个独立的作用域"。Node.js 在加载模块时，实际上会把模块代码包装进一个函数：

```javascript
(function(exports, require, module, __filename, __dirname) {
  // 你的模块代码
});
```

这就是为什么你能直接使用 `require`、`module`、`__dirname` 等——它们是包装函数的参数。

**导出的关键区别**：
- `module.exports`：模块真正导出的对象，默认是 `{}`
- `exports`：是 `module.exports` 的引用（`exports === module.exports` 初始为 true）
- 如果直接给 `exports` 赋值（如 `exports = {...}`），会断开引用，导出失效
- 推荐始终用 `module.exports` 导出，避免踩坑

CommonJS 是**同步加载**的：`require` 会阻塞后续代码，直到模块加载完成。这在服务端没问题（文件在本地，加载快），但不适合浏览器（需要异步加载，于是有了 AMD、ES Modules）。

#### 例子

**1. 导出与导入基本用法：**

```javascript
// math.js - 定义一个数学工具模块
function add(a, b) {
  return a + b;
}

function subtract(a, b) {
  return a - b;
}

const PI = 3.14159;

// 方式一：逐个挂载到 exports
exports.add = add;
exports.subtract = subtract;
exports.PI = PI;

// 方式二：直接导出对象（推荐）
// module.exports = { add, subtract, PI };

// 方式三：导出单个函数/类
// module.exports = add;
```

```javascript
// app.js - 使用模块
const math = require('./math');

console.log(math.add(1, 2));        // 3
console.log(math.subtract(5, 3));    // 2
console.log(math.PI);                // 3.14159
```

**2. 导出类的例子：**

```javascript
// logger.js
class Logger {
  constructor(prefix) {
    this.prefix = prefix;
  }

  log(message) {
    console.log(`[${this.prefix}] ${new Date().toISOString()} - ${message}`);
  }

  error(message) {
    console.error(`[${this.prefix}] ERROR - ${message}`);
  }
}

module.exports = Logger;
```

```javascript
// app.js
const Logger = require('./logger');
const logger = new Logger('APP');

logger.log('服务启动');  // [APP] 2024-01-15T... - 服务启动
logger.error('连接失败');
```

**3. exports 与 module.exports 的陷阱：**

```javascript
// bad.js - 错误示范
exports = { hello: 'world' };  // 这样写导出会失效！
// 因为 exports 被重新赋值，断开了与 module.exports 的引用

// good.js - 正确写法
module.exports = { hello: 'world' };  // 始终用 module.exports 导出对象
```

#### 总结

- 一个文件一个模块，模块作用域隔离，不会污染全局
- `require` 导入，`module.exports` 导出（推荐），`exports` 是 `module.exports` 的引用
- 不要直接给 `exports` 赋值，会断开引用导致导出失效
- CommonJS 是同步加载，适合服务端
- 模块代码会被包装进函数，所以 `require`、`module` 等看似"全局"，实则是函数参数

---

### 第 6 讲：模块加载机制

#### 概念

`require` 看似简单，背后却有一套完整的**模块查找与加载机制**。本讲深入讲解 require 如何找到模块、模块缓存机制、以及不同类型模块（核心模块、第三方模块、文件模块）的加载差异。

#### 原理

当你调用 `require('xxx')` 时，Node.js 按以下顺序查找：

1. **核心模块优先**：如果 `xxx` 是 Node.js 内置模块名（如 `fs`、`http`、`path`），直接返回核心模块，不再查找文件。
2. **路径模块**：如果以 `./`、`../`、`/` 开头，按相对/绝对路径查找文件。
3. **第三方模块**：否则从当前目录的 `node_modules` 查找，找不到则向上级目录的 `node_modules` 查找，直到根目录。这就是为什么装在项目根目录的包，子目录也能用。

**文件扩展名补全**：如果路径没写扩展名，Node.js 会依次尝试 `.js`、`.json`、`.node`（C++ 扩展）。

**目录加载**：如果 require 的是一个目录，Node.js 会：
1. 读取目录下的 `package.json`，找 `main` 字段指定的文件
2. 如果没有 `package.json` 或 `main` 无效，依次尝试 `index.js`、`index.json`、`index.node`

**模块缓存**：每个模块在第一次 `require` 后会被缓存到 `require.cache` 对象中。再次 `require` 同一模块时，直接返回缓存，不会重新执行模块代码。这意味着模块内的顶层代码只执行一次——这也是单例模式的天然实现。

#### 例子

**1. 模块查找路径演示：**

```javascript
// 查看模块查找路径
console.log(module.paths);
// 输出类似：
// [
//   '/Users/me/project/node_modules',
//   '/Users/me/node_modules',
//   '/Users/node_modules',
//   '/node_modules'
// ]
```

**2. 模块缓存机制：**

```javascript
// counter.js
let count = 0;

module.exports = {
  increment() {
    count++;
    return count;
  },
  getCount() {
    return count;
  }
};
```

```javascript
// app.js
const counterA = require('./counter');
const counterB = require('./counter');  // 返回缓存，不是新实例

console.log(counterA === counterB);  // true，是同一个对象

counterA.increment();
counterA.increment();
console.log(counterB.getCount());  // 2，因为共享同一个 count

// 查看缓存
console.log(Object.keys(require.cache));  // 包含 counter.js 的绝对路径
```

**3. 模拟 require 的查找过程：**

```javascript
// myRequire.js - 简化版 require 实现
function myRequire(modulePath) {
  // 1. 解析为绝对路径
  const absolutePath = require.resolve(modulePath);

  // 2. 检查缓存
  if (require.cache[absolutePath]) {
    return require.cache[absolutePath].exports;
  }

  // 3. 真实的 require 还会：读取文件、包装、编译执行
  // 这里直接调用原生 require 演示
  return require(modulePath);
}
```

**4. 清除缓存（热重载场景）：**

```javascript
// 适合开发时热重载，生产慎用
function reloadModule(modulePath) {
  const absolutePath = require.resolve(modulePath);
  delete require.cache[absolutePath];
  return require(modulePath);
}
```

#### 总结

- require 查找顺序：核心模块 → 路径模块 → node_modules（逐级向上）
- 文件扩展名可省略，Node.js 会自动尝试 `.js`、`.json`、`.node`
- require 目录时，读取 `package.json` 的 `main` 字段，否则用 `index.js`
- 模块第一次加载后被缓存，再次 require 返回缓存，模块代码只执行一次
- 利用缓存可实现单例，但要注意循环依赖问题（require 时返回不完整的 exports）

---

### 第 7 讲：ES Modules

#### 概念

**ES Modules（ESM）** 是 ECMAScript 官方的模块标准（ES2015 引入），使用 `import`/`export` 语法。它是浏览器和现代 JS 的未来方向。Node.js 从 v12 开始支持 ESM，v14 后稳定可用。本讲讲解如何在 Node.js 中使用 ESM，以及它与 CommonJS 的差异。

#### 原理

Node.js 通过文件扩展名或 `package.json` 配置区分模块类型：

1. **`.mjs` 文件**：始终作为 ES Module 处理
2. **`.cjs` 文件**：始终作为 CommonJS 处理
3. **`.js` 文件**：取决于最近的 `package.json` 中的 `type` 字段
   - `"type": "module"` → ESM
   - `"type": "commonjs"` 或不设置 → CommonJS

**ESM 与 CommonJS 的核心差异**：

| 特性 | CommonJS | ES Modules |
|------|----------|-------------|
| 导入 | `require()` | `import` |
| 导出 | `module.exports` | `export` / `export default` |
| 加载方式 | 同步、运行时 | 异步、静态分析（编译时确定依赖） |
| `this` 顶层 | `module.exports` | `undefined` |
| 是否支持顶层 `await` | 否 | 是（v14.8+） |
| `__dirname` | 可用 | 不可用，需用 `import.meta.url` 模拟 |

ESM 的静态分析特性使得**摇树优化（Tree Shaking）**成为可能——打包工具能识别未使用的导出并删除，减小包体积。这是现代前端构建工具（Webpack、Vite、Rollup）的基础。

#### 例子

**1. 启用 ESM（package.json）：**

```json
{
  "name": "my-app",
  "version": "1.0.0",
  "type": "module"
}
```

**2. 基本导入导出：**

```javascript
// math.mjs
export function add(a, b) {
  return a + b;
}

export function multiply(a, b) {
  return a * b;
}

const PI = 3.14159;
export default PI;  // 默认导出，每个模块只能有一个
```

```javascript
// app.mjs
import PI, { add, multiply } from './math.mjs';

console.log(add(2, 3));       // 5
console.log(multiply(4, 5));  // 20
console.log(PI);              // 3.14159

// 导入时重命名
import { add as plus } from './math.mjs';

// 动态导入（返回 Promise）
const module = await import('./math.mjs');
console.log(module.add(1, 1));
```

**3. 顶层 await（ESM 独有特性）：**

```javascript
// config.mjs
// ESM 支持顶层 await，无需包裹在 async 函数中
const response = await fetch('https://api.example.com/config');
const config = await response.json();

export default config;
```

**4. 模拟 __dirname（ESM 中不可用）：**

```javascript
// ESM 中没有 __dirname，需要这样获取
import { fileURLToPath } from 'url';
import { dirname } from 'path';

const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);

console.log(__dirname);
```

**5. CommonJS 与 ESM 互操作：**

```javascript
// ESM 中导入 CommonJS 模块
import pkg from 'commonjs-package';  // pkg 等于 module.exports
import { namedExport } from 'commonjs-package';  // 多数情况下不可用，需用 default

// CommonJS 中导入 ESM（必须用动态 import）
async function loadESM() {
  const esmModule = await import('./esm-module.mjs');
  console.log(esmModule.default);
}
```

#### 总结

- ESM 是官方标准，使用 `import`/`export`，未来趋势
- 通过 `.mjs` 扩展名或 `package.json` 的 `"type": "module"` 启用
- ESM 支持静态分析、Tree Shaking、顶层 await
- ESM 中没有 `__dirname`/`__filename`，需用 `import.meta.url` 模拟
- CommonJS 与 ESM 互操作有限制，新项目推荐统一用 ESM
- 老项目可逐步迁移，Node.js 同时支持两种规范

---

### 第 8 讲：npm 与 package.json

#### 概念

**npm（Node Package Manager）** 是 Node.js 的默认包管理器，也是全球最大的开源软件仓库（注册表 registry.npmjs.org 上有超过 200 万个包）。**package.json** 是项目的"身份证"，记录项目元信息、依赖、脚本等。本讲系统讲解 npm 的核心命令与 package.json 的关键字段。

#### 原理

npm 的工作流程：
1. **安装包**：从 registry 下载包的 tarball，解压到 `node_modules/`
2. **依赖解析**：递归安装包的依赖，处理版本冲突
3. **锁定版本**：生成 `package-lock.json`，记录精确版本树，保证团队环境一致

**package.json 的核心字段**：

| 字段 | 含义 |
|------|------|
| `name` | 包名（小写，无空格） |
| `version` | 语义化版本号 `主.次.修` |
| `description` | 项目描述 |
| `main` | CommonJS 入口文件 |
| `module` | ESM 入口文件 |
| `type` | 模块类型（`module`/`commonjs`） |
| `scripts` | 自定义命令脚本 |
| `dependencies` | 生产依赖 |
| `devDependencies` | 开发依赖 |
| `peerDependencies` | 同伴依赖（插件用） |
| `engines` | Node 版本要求 |

**语义化版本（SemVer）**：`主版本.次版本.修订版本`（如 `1.4.2`）
- 主版本（Major）：不兼容的 API 修改
- 次版本（Minor）：向下兼容的功能新增
- 修订版本（Patch）：向下兼容的 Bug 修复

版本范围符号：
- `^1.4.2`：兼容 1.x.x（最常用，允许次版本和修订版本升级）
- `~1.4.2`：兼容 1.4.x（只允许修订版本升级）
- `1.4.2`：精确版本
- `*` 或 `latest`：最新版

#### 例子

**1. 初始化项目：**

```bash
# 交互式创建 package.json
npm init

# 快速创建（全部用默认值）
npm init -y
```

生成的 `package.json`：

```json
{
  "name": "my-app",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

**2. 安装依赖：**

```bash
# 安装生产依赖（写入 dependencies）
npm install express

# 安装开发依赖（写入 devDependencies）
npm install --save-dev jest
# 简写：npm i -D jest

# 全局安装（命令行工具）
npm install -g nodemon
# 简写：npm i -g nodemon

# 安装指定版本
npm install express@4.18.0

# 安装 package.json 中所有依赖
npm install
# 简写：npm i
```

**3. 自定义脚本（scripts）：**

```json
{
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js",
    "test": "jest",
    "build": "webpack --mode production",
    "lint": "eslint ."
  }
}
```

运行脚本：

```bash
npm run dev      # 运行 dev 脚本
npm start        # start 是特殊脚本，可省略 run
npm test        # test 同理
```

**4. 其他常用命令：**

```bash
# 卸载包
npm uninstall express

# 查看已安装的包
npm list              # 当前项目
npm list --depth=0    # 只看顶层
npm list -g --depth=0 # 全局

# 查看过期的依赖
npm outdated

# 安全更新依赖
npm audit             # 检查安全漏洞
npm audit fix         # 自动修复

# 升级包
npm update express

# 查看包信息
npm info express
```

**5. npx 执行本地命令：**

```bash
# 不全局安装，直接运行
npx create-react-app my-app

# 运行本地 node_modules/.bin 下的命令
npx eslint .
```

#### 总结

- package.json 是项目元信息中心，必须掌握每个字段含义
- 依赖分三类：`dependencies`（生产）、`devDependencies`（开发）、`peerDependencies`（同伴）
- 版本号遵循 SemVer，`^` 最常用（允许次版本升级）
- `package-lock.json` 锁定精确版本，必须提交到 Git
- `scripts` 字段定义命令别名，`npm run xxx` 执行
- `npx` 可直接运行未安装的命令行工具，避免全局污染
- 推荐用 `npm ci` 在 CI 环境安装（更快、严格按 lock 文件）

---

## 第三章：核心模块详解

### 第 9 讲：fs 文件系统模块

#### 概念

`fs`（File System）是 Node.js 最常用的核心模块之一，提供文件和目录的读写、删除、查询等操作。所有方法都提供**同步**（带 `Sync` 后缀）和**异步**两种版本。生产环境几乎只用异步版本，同步版本会阻塞事件循环，仅在启动时加载配置等场景使用。

#### 原理

`fs` 模块底层调用操作系统的文件 API（Linux 的 `open`/`read`/`write`，Windows 的 `CreateFile` 等）。异步方法有两种风格：
- **回调风格**：`fs.readFile(path, callback)`，回调签名为 `(err, data)`
- **Promise 风格**：通过 `require('fs').promises` 访问，返回 Promise

异步 I/O 的实现依赖 libuv 的线程池（默认 4 个线程）。虽然 Node.js 主线程是单线程，但 I/O 操作会委托给线程池中的工作线程执行，完成后通过事件循环通知主线程。所以"Node.js 单线程"严格来说是"JS 执行单线程"，I/O 实际是多线程的。

**文件描述符（fd）**：操作系统用整数表示打开的文件。`fs.open` 返回 fd，后续操作用 fd 引用文件，最后必须 `fs.close` 关闭，否则会泄漏。高层的 `readFile`/`writeFile` 自动管理 fd，推荐使用。

#### 例子

**1. 读取文件：**

```javascript
const fs = require('fs');
const fsp = require('fs').promises;  // Promise 版本

// 异步回调风格
fs.readFile('./data.txt', 'utf8', (err, data) => {
  if (err) return console.error('读取失败:', err);
  console.log('文件内容:', data);
});

// Promise 风格（推荐）
async function readFile() {
  try {
    const data = await fsp.readFile('./data.txt', 'utf8');
    console.log('文件内容:', data);
  } catch (err) {
    console.error('读取失败:', err);
  }
}

// 同步风格（仅启动时用）
const data = fs.readFileSync('./config.json', 'utf8');
const config = JSON.parse(data);
```

**2. 写入文件：**

```javascript
const fsp = require('fs').promises;

async function writeFile() {
  // 覆盖写入
  await fsp.writeFile('./output.txt', 'Hello World', 'utf8');

  // 追加写入
  await fsp.appendFile('./log.txt', `${new Date()} - 服务启动\n`);

  // 写入 JSON
  const config = { port: 3000, debug: true };
  await fsp.writeFile(
    './config.json',
    JSON.stringify(config, null, 2),
    'utf8'
  );
}
```

**3. 目录操作：**

```javascript
const fsp = require('fs').promises;
const path = require('path');

async function dirOperations() {
  // 创建目录（recursive: true 类似 mkdir -p）
  await fsp.mkdir('./data/logs', { recursive: true });

  // 读取目录内容
  const files = await fsp.readdir('./data');
  console.log('目录内容:', files);

  // 读取目录并获取详细信息
  const entries = await fsp.readdir('./data', { withFileTypes: true });
  for (const entry of entries) {
    const type = entry.isDirectory() ? '目录' : '文件';
    console.log(`${type}: ${entry.name}`);
  }

  // 删除空目录
  await fsp.rmdir('./data/empty');

  // 递归删除非空目录
  await fsp.rm('./data/old', { recursive: true, force: true });
}
```

**4. 文件信息查询：**

```javascript
const fsp = require('fs').promises;

async function fileInfo() {
  try {
    const stats = await fsp.stat('./data.txt');
    console.log('是否文件:', stats.isFile());
    console.log('是否目录:', stats.isDirectory());
    console.log('文件大小:', stats.size, '字节');
    console.log('创建时间:', stats.birthtime);
    console.log('修改时间:', stats.mtime);
  } catch (err) {
    if (err.code === 'ENOENT') {
      console.log('文件不存在');
    }
  }

  // 检查文件是否存在（推荐用 access，stat 会抛错）
  try {
    await fsp.access('./data.txt', fs.constants.R_OK);
    console.log('文件可读');
  } catch {
    console.log('文件不可读或不存在');
  }
}
```

**5. 监听文件变化：**

```javascript
const fs = require('fs');

// 监听单个文件
fs.watch('./data.txt', (eventType, filename) => {
  console.log(`文件 ${filename} 发生 ${eventType} 事件`);
});

// 监听目录
fs.watch('./logs', { recursive: true }, (eventType, filename) => {
  console.log(`${filename}: ${eventType}`);
});
```

#### 总结

- `fs` 模块提供同步（`Sync`）和异步两种 API，生产用异步
- 推荐用 `fs.promises`（Promise 风格），配合 async/await 代码更清晰
- `readFile`/`writeFile` 自动管理文件描述符，无需手动 close
- `mkdir` 用 `{ recursive: true }` 实现递归创建
- `fs.watch` 可监听文件变化，但不同平台行为有差异，生产可用 chokidar 库
- 大文件读写用 `stream`，避免一次性占用大量内存

---

### 第 10 讲：path 路径模块

#### 概念

`path` 模块提供跨平台的路径字符串处理工具。不同操作系统路径分隔符不同（Windows 用 `\`，Linux/Mac 用 `/`），直接拼接字符串会导致跨平台问题。`path` 模块屏蔽了这些差异，是处理文件路径的必备工具。

#### 原理

`path` 模块会根据当前操作系统自动选择分隔符：
- `path.sep`：路径分隔符（Windows 为 `\`，POSIX 为 `/`）
- `path.delimiter`：环境变量分隔符（Windows 为 `;`，POSIX 为 `:`）

Node.js 还提供 `path.win32` 和 `path.posix` 两个对象，可以强制使用特定平台的路径规则，这在跨平台工具开发时很有用。

路径相关的几个核心概念：
- **绝对路径**：从根目录开始，如 `/usr/local/bin` 或 `C:\Users\me`
- **相对路径**：相对于当前目录，如 `./src/index.js`
- **规范化路径**：去除冗余的 `.` 和 `..`，如 `a/./b/../c` 规范化为 `a/c`

#### 例子

**1. 路径拼接（最常用）：**

```javascript
const path = require('path');

// path.join - 拼接路径，自动处理分隔符
const fullPath = path.join(__dirname, 'src', 'utils', 'helper.js');
// Linux: /home/me/project/src/utils/helper.js
// Windows: C:\me\project\src\utils\helper.js

// path.resolve - 解析为绝对路径（类似 cd 命令）
const abs1 = path.resolve('src', 'index.js');
// 从当前工作目录解析为绝对路径

const abs2 = path.resolve('/home', 'me', 'project');
// /home/me/project

const abs3 = path.resolve('a/b', '../c');
// 等于 cd a/b && cd ../c 后的绝对路径
```

**2. 解析路径信息：**

```javascript
const path = require('path');

const filePath = '/home/me/project/src/index.js';

// path.parse - 解析路径为对象
const parsed = path.parse(filePath);
console.log(parsed);
/*
{
  root: '/',
  dir: '/home/me/project/src',
  base: 'index.js',
  ext: '.js',
  name: 'index'
}
*/

// 单独获取各部分
console.log(path.dirname(filePath));  // /home/me/project/src
console.log(path.basename(filePath));  // index.js
console.log(path.basename(filePath, '.js'));  // index（去掉扩展名）
console.log(path.extname(filePath));   // .js
```

**3. 路径规范化与相对路径：**

```javascript
const path = require('path');

// normalize - 规范化路径（处理 . 和 ..）
console.log(path.normalize('/home/me/../you/./docs'));
// /home/you/docs

// relative - 计算从 A 到 B 的相对路径
const from = '/home/me/project/src';
const to = '/home/me/project/docs/api.md';
console.log(path.relative(from, to));
// ../docs/api.md

// isAbsolute - 判断是否绝对路径
console.log(path.isAbsolute('/usr/local'));  // true
console.log(path.isAbsolute('src/index'));    // false
```

**4. 跨平台处理：**

```javascript
const path = require('path');

// 当前平台的分隔符
console.log(path.sep);  // Linux: '/'  Windows: '\'

// 强制使用 POSIX 规则（始终用 /）
const posixPath = path.posix.join('/home', 'me', 'file.js');
console.log(posixPath);  // /home/me/file.js

// 强制使用 Windows 规则（始终用 \）
const winPath = path.win32.join('C:', 'Users', 'me', 'file.js');
console.log(winPath);  // C:\Users\me\file.js
```

**5. 实战：动态生成文件路径**

```javascript
const path = require('path');
const fs = require('fs');

// 项目根目录（package.json 所在目录）
const PROJECT_ROOT = path.resolve(__dirname, '..');

// 配置路径别名
const PATHS = {
  src: path.join(PROJECT_ROOT, 'src'),
  dist: path.join(PROJECT_ROOT, 'dist'),
  logs: path.join(PROJECT_ROOT, 'logs'),
  config: path.join(PROJECT_ROOT, 'config.json')
};

// 确保目录存在
function ensureDir(dirPath) {
  if (!fs.existsSync(dirPath)) {
    fs.mkdirSync(dirPath, { recursive: true });
  }
}

ensureDir(PATHS.logs);
ensureDir(PATHS.dist);
```

#### 总结

- 永远用 `path.join` 拼接路径，不要手动用 `+` 或模板字符串
- `path.resolve` 解析为绝对路径，行为类似终端的 `cd`
- `path.parse` 一次性获取路径的所有组成部分
- `__dirname` + `path.join` 是定位项目内文件的标准方式
- 跨平台工具用 `path.posix` 或 `path.win32` 强制规则
- 路径处理看似简单，但跨平台坑多，务必用 `path` 模块

---

### 第 11 讲：os 与 process 模块

#### 概念

`os` 模块提供操作系统相关信息（CPU、内存、网络等），`process` 模块提供当前 Node.js 进程的信息与控制能力。两者都是开发系统工具、监控应用、读取环境配置时的核心模块。

#### 原理

**`os` 模块**直接调用操作系统 API 获取硬件与系统信息。它返回的数据是查询时的快照，不是实时更新的。常用场景：根据 CPU 核数决定 Cluster 进程数、根据内存限制缓存策略、获取本机 IP 启动服务。

**`process` 模块**是一个全局对象（无需 require），代表当前 Node.js 进程。它包含：
- `process.argv`：命令行参数
- `process.env`：环境变量
- `process.cwd()`：当前工作目录
- `process.exit(code)`：退出进程
- `process.stdin/stdout/stderr`：标准输入输出流
- `process.on('exit', callback)`：退出事件

`process` 还提供 `nextTick`，用于将回调放到当前操作完成后立即执行（优先级高于 Promise 的 then）。

#### 例子

**1. os 模块常用 API：**

```javascript
const os = require('os');

console.log('操作系统:', os.type());        // Linux / Darwin / Windows_NT
console.log('平台:', os.platform());        // linux / darwin / win32
console.log('CPU 架构:', os.arch());        // x64 / arm64
console.log('主机名:', os.hostname());
console.log('系统运行时间:', os.uptime(), '秒');

// CPU 信息
console.log('CPU 核数:', os.cpus().length);
console.log('CPU 型号:', os.cpus()[0].model);

// 内存信息（字节）
const totalMem = os.totalmem();
const freeMem = os.freemem();
console.log(`总内存: ${(totalMem / 1024 / 1024 / 1024).toFixed(2)} GB`);
console.log(`可用内存: ${(freeMem / 1024 / 1024 / 1024).toFixed(2)} GB`);
console.log(`内存使用率: ${((1 - freeMem / totalMem) * 100).toFixed(1)}%`);

// 网络接口
const interfaces = os.networkInterfaces();
console.log('IP 地址:', interfaces['eth0']?.[0]?.address);
```

**2. process 模块常用 API：**

```javascript
// 命令行参数
console.log('参数:', process.argv);

// 环境变量
console.log('NODE_ENV:', process.env.NODE_ENV || 'development');
console.log('PATH:', process.env.PATH);

// 当前工作目录
console.log('cwd:', process.cwd());

// 进程信息
console.log('PID:', process.pid);
console.log('Node 版本:', process.version);
console.log('运行平台:', process.platform);

// 内存使用
const mem = process.memoryUsage();
console.log(`RSS: ${(mem.rss / 1024 / 1024).toFixed(2)} MB`);  // 常驻内存
console.log(`Heap: ${(mem.heapUsed / 1024 / 1024).toFixed(2)} / ${(mem.heapTotal / 1024 / 1024).toFixed(2)} MB`);
```

**3. 标准输入输出：**

```javascript
// stdout - 标准输出
process.stdout.write('直接写入 stdout\n');

// stderr - 标准错误
process.stderr.write('错误信息\n');

// stdin - 读取用户输入
process.stdin.setEncoding('utf8');
process.stdout.write('请输入你的名字: ');
process.stdin.on('data', (data) => {
  const name = data.trim();
  console.log(`你好, ${name}!`);
  process.exit(0);
});
```

**4. 进程退出与生命周期：**

```javascript
// 优雅退出
process.on('SIGINT', () => {
  console.log('\n收到 Ctrl+C，正在清理...');
  // 执行清理逻辑：关闭数据库连接、保存状态等
  cleanup().then(() => process.exit(0));
});

process.on('SIGTERM', () => {
  console.log('收到终止信号');
  process.exit(0);
});

// 退出前钩子（同步代码）
process.on('exit', (code) => {
  console.log(`进程退出，退出码: ${code}`);
  // 注意：这里只能执行同步代码，异步操作不会执行
});

// 未捕获异常
process.on('uncaughtException', (err) => {
  console.error('未捕获异常:', err);
  // 记录日志后退出，避免状态不一致
  process.exit(1);
});

// 未处理的 Promise 拒绝
process.on('unhandledRejection', (reason) => {
  console.error('未处理的 Promise 拒绝:', reason);
});
```

**5. 实战：根据 CPU 核数启动 Cluster**

```javascript
const os = require('os');
const cluster = require('cluster');

if (cluster.isPrimary) {
  const cpuCount = os.cpus().length;
  console.log(`主进程 PID ${process.pid}，启动 ${cpuCount} 个工作进程`);

  for (let i = 0; i < cpuCount; i++) {
    cluster.fork();
  }
} else {
  // 工作进程
  console.log(`工作进程 PID ${process.pid}`);
}
```

#### 总结

- `os` 模块提供系统信息，常用于动态调整运行参数（如进程数）
- `process` 是全局对象，提供进程信息与控制能力
- `process.env` 读取环境变量，是配置管理的标准方式
- `process.on('SIGINT'/'SIGTERM')` 实现优雅退出，生产必备
- `process.on('uncaughtException')` 兜底未捕获异常，但建议记录后退出
- `process.memoryUsage()` 监控内存，发现泄漏时定位问题

---

### 第 12 讲：events 事件模块

#### 概念

`events` 模块是 Node.js 事件驱动的基石。它提供 `EventEmitter` 类，用于实现**发布-订阅模式**：一个对象（发射器）发出事件，多个监听器订阅并响应。Node.js 的很多核心模块（`http`、`fs`、`stream`）都基于 EventEmitter 实现。

#### 原理

EventEmitter 的核心思想是**解耦**：事件的生产者不需要知道谁在监听，只负责"发射"事件；监听者按需订阅感兴趣的事件。这种模式让代码松耦合、易扩展。

EventEmitter 内部维护一个事件到回调函数数组的映射：

```javascript
this._events = {
  'data': [callback1, callback2],
  'error': [errorHandler]
};
```

调用 `emit('data')` 时，遍历 `data` 对应的回调数组依次执行。监听器默认按注册顺序同步执行（除非用 `setImmediate` 包裹）。

**关键特性**：
- 一个事件可以有多个监听器（默认最多 10 个，可调整）
- 监听器执行是同步的，但可以用 `setImmediate` 改为异步
- `once` 注册的监听器只执行一次
- `error` 事件特殊：如果没有监听器，触发时会抛出异常导致进程崩溃

#### 例子

**1. 基本用法：**

```javascript
const EventEmitter = require('events');

// 创建发射器
const emitter = new EventEmitter();

// 注册监听器
emitter.on('greet', (name) => {
  console.log(`你好, ${name}!`);
});

emitter.on('greet', (name) => {
  console.log(`Hello, ${name}!`);
});

// 触发事件
emitter.emit('greet', 'Node');
// 输出:
// 你好, Node!
// Hello, Node!
```

**2. 自定义事件类：**

```javascript
const EventEmitter = require('events');

class User extends EventEmitter {
  constructor(name) {
    super();
    this.name = name;
  }

  login() {
    // 业务逻辑
    console.log(`${this.name} 登录中...`);
    // 发射事件
    this.emit('login', { name: this.name, time: new Date() });
  }

  logout() {
    this.emit('logout', this.name);
  }
}

// 使用
const user = new User('Alice');

user.on('login', (data) => {
  console.log(`[日志] ${data.name} 于 ${data.time} 登录`);
});

user.on('logout', (name) => {
  console.log(`[日志] ${name} 退出登录`);
});

user.login();    // 触发 login 事件
user.logout();   // 触发 logout 事件
```

**3. once 与一次性监听：**

```javascript
const emitter = new EventEmitter();

// once - 只监听一次
let count = 0;
emitter.once('tick', () => {
  count++;
  console.log(`第 ${count} 次（仅触发一次）`);
});

emitter.emit('tick');  // 触发
emitter.emit('tick');  // 不触发（监听器已移除）
```

**4. 移除监听器：**

```javascript
const emitter = new EventEmitter();

function onData(data) {
  console.log('收到数据:', data);
}

emitter.on('data', onData);

emitter.emit('data', 'hello');

// 移除特定监听器（必须传同一函数引用）
emitter.off('data', onData);
// 等价于 emitter.removeListener('data', onData);

emitter.emit('data', 'world');  // 不会触发

// 移除某事件的所有监听器
emitter.removeAllListeners('data');

// 移除所有事件的所有监听器
emitter.removeAllListeners();
```

**5. error 事件处理：**

```javascript
const emitter = new EventEmitter();

// 必须监听 error 事件，否则触发时会崩溃
emitter.on('error', (err) => {
  console.error('捕获到错误:', err.message);
});

emitter.emit('error', new Error('数据库连接失败'));
// 输出: 捕获到错误: 数据库连接失败

// 如果没有 error 监听器，上面的 emit 会让进程崩溃
```

**6. 实战：实现一个简单的任务队列**

```javascript
const EventEmitter = require('events');

class TaskQueue extends EventEmitter {
  constructor() {
    super();
    this.tasks = [];
    this.processing = false;
  }

  add(task) {
    this.tasks.push(task);
    this.emit('added', task);
    this.process();
  }

  async process() {
    if (this.processing) return;
    this.processing = true;

    while (this.tasks.length > 0) {
      const task = this.tasks.shift();
      try {
        const result = await task();
        this.emit('done', result);
      } catch (err) {
        this.emit('error', err);
      }
    }

    this.processing = false;
    this.emit('empty');
  }
}

// 使用
const queue = new TaskQueue();

queue.on('added', () => console.log('新任务加入'));
queue.on('done', (r) => console.log('任务完成:', r));
queue.on('error', (e) => console.error('任务失败:', e.message));
queue.on('empty', () => console.log('队列已空'));

queue.add(() => Promise.resolve('任务1结果'));
queue.add(() => Promise.resolve('任务2结果'));
```

#### 总结

- EventEmitter 是 Node.js 事件驱动的核心，实现发布-订阅模式
- `on` 注册监听器，`emit` 触发事件，`once` 只监听一次
- 监听器默认同步执行，按注册顺序调用
- `error` 事件必须监听，否则触发时进程崩溃
- 自定义类继承 EventEmitter，可实现松耦合的事件驱动架构
- 注意监听器泄漏：忘记 `off` 会导致内存泄漏，可用 `emitter.listenerCount()` 检查

---

### 第 13 讲：stream 流模块

#### 概念

`stream`（流）是 Node.js 处理**大数据**的核心抽象。流是一段持续产生或消费的数据序列，特点是**分块处理**——数据不是一次性全部加载到内存，而是边产生边处理。Node.js 的流有四种类型：可读流（Readable）、可写流（Writable）、双工流（Duplex）、转换流（Transform）。

#### 原理

传统方式读取大文件：`fs.readFile` 把整个文件读入内存，1GB 文件就占用 1GB 内存。流的方式：每次只读一小块（默认 64KB），处理完再读下一块，内存占用恒定。

**四种流类型**：
- **Readable**：可读，数据从源头流出（如 `fs.createReadStream`、HTTP 请求体）
- **Writable**：可写，数据流入目的地（如 `fs.createWriteStream`、HTTP 响应体）
- **Duplex**：双工，可读可写，读写独立（如 TCP socket）
- **Transform**：转换，读入数据经过变换后写出（如压缩、加密）

**背压（Backpressure）**：当生产者速度大于消费者速度时，数据会堆积。流通过背压机制自动暂停生产者，等消费者处理完再继续。`pipe()` 方法自动处理背压，是流的推荐用法。

**两种模式**：Readable 流有"流动模式"（flowing，数据自动流出）和"暂停模式"（paused，需手动 `read()`）。监听 `data` 事件会切换到流动模式，`read()` 方法在暂停模式下手动拉取。

#### 例子

**1. 可读流读取大文件：**

```javascript
const fs = require('fs');

// 创建可读流
const readStream = fs.createReadStream('./bigfile.txt', {
  encoding: 'utf8',
  highWaterMark: 64 * 1024  // 每次读 64KB
});

let data = '';
let chunkCount = 0;

readStream.on('data', (chunk) => {
  chunkCount++;
  data += chunk;
  console.log(`收到第 ${chunkCount} 块，大小 ${chunk.length}`);
});

readStream.on('end', () => {
  console.log(`读取完成，共 ${chunkCount} 块，总长度 ${data.length}`);
});

readStream.on('error', (err) => {
  console.error('读取出错:', err);
});
```

**2. 可写流写入文件：**

```javascript
const fs = require('fs');

const writeStream = fs.createWriteStream('./output.txt');

writeStream.write('第一行\n');
writeStream.write('第二行\n');
writeStream.write('第三行\n');
writeStream.end('结束\n');  // end 后不能再 write

writeStream.on('finish', () => {
  console.log('写入完成');
});

writeStream.on('error', (err) => {
  console.error('写入出错:', err);
});
```

**3. pipe 管道（最常用）：**

```javascript
const fs = require('fs');
const zlib = require('zlib');

// 读取文件 → 压缩 → 写入新文件
fs.createReadStream('./bigfile.txt')
  .pipe(zlib.createGzip())              // 压缩流
  .pipe(fs.createWriteStream('./bigfile.txt.gz'))
  .on('finish', () => {
    console.log('压缩完成');
  });

// HTTP 响应流式返回文件
const http = require('http');
http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'video/mp4' });
  fs.createReadStream('./video.mp4').pipe(res);  // 边读边发，内存恒定
}).listen(3000);
```

**4. 转换流（Transform）：**

```javascript
const { Transform } = require('stream');
const fs = require('fs');

// 自定义转换流：把每个字母转大写
const upperCase = new Transform({
  transform(chunk, encoding, callback) {
    // chunk 是 Buffer，转字符串处理后 push
    this.push(chunk.toString().toUpperCase());
    callback();
  }
});

fs.createReadStream('./input.txt')
  .pipe(upperCase)
  .pipe(fs.createWriteStream('./output.txt'));

// 实战：行处理器
const { Transform } = require('stream');
const readline = require('readline');

class LineTransform extends Transform {
  constructor() {
    super();
    this.buffer = '';
  }

  _transform(chunk, encoding, callback) {
    this.buffer += chunk.toString();
    const lines = this.buffer.split('\n');
    this.buffer = lines.pop();  // 最后一行可能不完整，保留
    lines.forEach(line => this.push(line.toUpperCase() + '\n'));
    callback();
  }

  _flush(callback) {
    if (this.buffer) this.push(this.buffer.toUpperCase());
    callback();
  }
}
```

**5. 自定义可读流：**

```javascript
const { Readable } = require('stream');

// 方式一：从数组创建
const stream = Readable.from(['Hello', ' ', 'World', '!']);
stream.on('data', (chunk) => console.log(chunk.toString()));

// 方式二：继承实现
class CounterStream extends Readable {
  constructor(max) {
    super();
    this.count = 0;
    this.max = max;
  }

  _read() {
    if (this.count < this.max) {
      this.push(`${this.count++}\n`);
    } else {
      this.push(null);  // null 表示流结束
    }
  }
}

const counter = new CounterStream(5);
counter.on('data', (chunk) => process.stdout.write(chunk));
counter.on('end', () => console.log('结束'));
```

#### 总结

- 流是处理大数据的核心抽象，分块处理，内存占用恒定
- 四种类型：Readable、Writable、Duplex、Transform
- `pipe()` 自动处理背压，是流的推荐用法
- HTTP 请求/响应体本身就是流，可以 `pipe` 到文件或其他流
- 自定义流继承对应的基类，实现 `_read`/`_write`/`_transform` 方法
- 处理大文件、视频、日志时务必用流，避免内存爆炸

---

## 第四章：异步编程

### 第 14 讲：回调函数与回调地狱

#### 概念

**回调函数（Callback）** 是 Node.js 最早的异步编程方式。简单说，就是把"完成后要做的事"作为参数传给异步函数，异步操作完成后调用这个回调。但当多个异步操作有依赖关系时，回调会层层嵌套，形成**回调地狱（Callback Hell）**——代码缩进过深、难以阅读和维护。

#### 原理

Node.js 的异步 API 采用**错误优先回调（Error-first Callback）**约定：回调函数的第一个参数永远是错误对象（无错误时为 `null`），后续参数才是结果数据。这个约定由 Node.js 核心团队确立，是整个生态的规范。

```javascript
asyncFunction(args, (err, result) => {
  if (err) {
    // 处理错误
    return;
  }
  // 处理结果
});
```

回调地狱的成因：当操作 A 完成后才能执行 B，B 完成后才能执行 C，代码就变成：

```javascript
stepA((err, a) => {
  stepB(a, (err, b) => {
    stepC(b, (err, c) => {
      stepD(c, (err, d) => {
        // 越嵌越深...
      });
    });
  });
});
```

这种代码的问题：
1. **可读性差**：缩进过深，逻辑难以追踪
2. **错误处理繁琐**：每层都要判断 `err`
3. **难以复用**：逻辑耦合在嵌套中
4. **无法 return/throw**：回调中的错误无法被外层 try/catch 捕获

#### 例子

**1. 基本回调：**

```javascript
const fs = require('fs');

// 读取文件，回调风格
fs.readFile('./config.json', 'utf8', (err, data) => {
  if (err) {
    console.error('读取失败:', err);
    return;
  }
  try {
    const config = JSON.parse(data);
    console.log('配置:', config);
  } catch (parseErr) {
    console.error('解析失败:', parseErr);
  }
});
```

**2. 回调地狱的典型例子：**

```javascript
const fs = require('fs');

// 需求：读取用户 → 读取订单 → 读取商品 → 计算总价
fs.readFile('./user.json', 'utf8', (err, userData) => {
  if (err) return console.error(err);
  const user = JSON.parse(userData);

  fs.readFile(`./orders/${user.id}.json`, 'utf8', (err, orderData) => {
    if (err) return console.error(err);
    const order = JSON.parse(orderData);

    fs.readFile(`./products/${order.productId}.json`, 'utf8', (err, productData) => {
      if (err) return console.error(err);
      const product = JSON.parse(productData);

      const total = product.price * order.quantity;
      console.log(`总价: ${total}`);

      // 还要继续？再嵌一层...
    });
  });
});
```

**3. 解决方案一：命名函数拆分：**

```javascript
const fs = require('fs');

function readUser(callback) {
  fs.readFile('./user.json', 'utf8', (err, data) => {
    if (err) return callback(err);
    callback(null, JSON.parse(data));
  });
}

function readOrder(userId, callback) {
  fs.readFile(`./orders/${userId}.json`, 'utf8', (err, data) => {
    if (err) return callback(err);
    callback(null, JSON.parse(data));
  });
}

function readProduct(productId, callback) {
  fs.readFile(`./products/${productId}.json`, 'utf8', (err, data) => {
    if (err) return callback(err);
    callback(null, JSON.parse(data));
  });
}

// 扁平化调用
readUser((err, user) => {
  if (err) return console.error(err);
  readOrder(user.id, (err, order) => {
    if (err) return console.error(err);
    readProduct(order.productId, (err, product) => {
      if (err) return console.error(err);
      console.log(`总价: ${product.price * order.quantity}`);
    });
  });
});
```

**4. 解决方案二：async 库（早期方案）：**

```javascript
const async = require('async');

async.waterfall([
  function(callback) {
    fs.readFile('./user.json', 'utf8', callback);
  },
  function(userData, callback) {
    const user = JSON.parse(userData);
    fs.readFile(`./orders/${user.id}.json`, 'utf8', callback);
  },
  function(orderData, callback) {
    const order = JSON.parse(orderData);
    fs.readFile(`./products/${order.productId}.json`, 'utf8', callback);
  }
], (err, productData) => {
  if (err) return console.error(err);
  console.log('最终结果:', JSON.parse(productData));
});
```

**5. 解决方案三：事件发射器（解耦）：**

```javascript
const EventEmitter = require('events');

class Pipeline extends EventEmitter {
  run() {
    this.emit('start');
    fs.readFile('./user.json', 'utf8', (err, data) => {
      if (err) return this.emit('error', err);
      this.emit('userLoaded', JSON.parse(data));
    });
  }
}

const pipeline = new Pipeline();
pipeline.on('userLoaded', (user) => {
  // 继续下一步
});
pipeline.on('error', console.error);
pipeline.run();
```

#### 总结

- 回调是 Node.js 最早的异步方案，采用错误优先约定
- 多层嵌套形成回调地狱，可读性差、错误处理繁琐
- 拆分命名函数能缓解，但治标不治本
- 真正的解决方案是 Promise 和 async/await（后续讲解）
- 老代码可能仍有回调风格，需要会读会改
- 新代码务必用 Promise/async，避免回调地狱

---

### 第 15 讲：Promise 详解

#### 概念

**Promise** 是 ES6 引入的异步编程标准方案，代表一个"未来会完成"的操作。它有三种状态：**pending（进行中）**、**fulfilled（已成功）**、**rejected（已失败）**。状态一旦从 pending 变为 fulfilled 或 rejected，就不可逆转。Promise 通过链式调用（`.then()`）解决回调地狱，让异步代码像同步代码一样线性书写。

#### 原理

Promise 是一个状态机：

```
pending ──resolve(value)──→ fulfilled
   │
   └──reject(reason)──────→ rejected
```

**核心机制**：
1. **状态不可逆**：一旦 resolve 或 reject，后续的 resolve/reject 无效
2. **then 返回新 Promise**：每次 `.then()` 返回新的 Promise，形成链式调用
3. **值穿透**：`.then()` 不传回调时，值会"穿透"传给下一个 then
4. **错误冒泡**：reject 会沿链向后传递，直到遇到 `.catch()`
5. **微任务执行**：then/catch 的回调在微任务队列执行，晚于同步代码

**链式调用的值传递**：
- `.then(onFulfilled)` 的返回值会成为下一个 then 的输入
- 返回普通值：直接传给下一个 then
- 返回 Promise：等它 resolve 后再传
- 抛出错误：触发下一个 catch

#### 例子

**1. 创建 Promise：**

```javascript
// 基本创建
const promise = new Promise((resolve, reject) => {
  // 异步操作
  const success = Math.random() > 0.5;
  if (success) {
    resolve('操作成功');  // pending → fulfilled
  } else {
    reject(new Error('操作失败'));  // pending → rejected
  }
});

promise.then(
  (value) => console.log('成功:', value),
  (error) => console.error('失败:', error.message)
);
```

**2. 包装回调风格的 API：**

```javascript
const fs = require('fs');

// 把 fs.readFile 包装成 Promise
function readFile(path) {
  return new Promise((resolve, reject) => {
    fs.readFile(path, 'utf8', (err, data) => {
      if (err) reject(err);
      else resolve(data);
    });
  });
}

// 使用
readFile('./config.json')
  .then(data => JSON.parse(data))
  .then(config => console.log('配置:', config))
  .catch(err => console.error('出错:', err.message));
```

**3. 链式调用解决回调地狱：**

```javascript
// 之前的回调地狱，用 Promise 重写
readFile('./user.json')
  .then(data => {
    const user = JSON.parse(data);
    return readFile(`./orders/${user.id}.json`);  // 返回新 Promise
  })
  .then(data => {
    const order = JSON.parse(data);
    return readFile(`./products/${order.productId}.json`);
  })
  .then(data => {
    const product = JSON.parse(data);
    console.log('商品:', product);
  })
  .catch(err => {
    // 统一错误处理，任何一步出错都会到这里
    console.error('流程出错:', err.message);
  });
```

**4. Promise 静态方法：**

```javascript
// Promise.resolve / Promise.reject - 快速创建
const p1 = Promise.resolve('立即成功');
const p2 = Promise.reject(new Error('立即失败'));

// Promise.all - 全部成功才成功，任一失败则失败
Promise.all([
  readFile('./a.txt'),
  readFile('./b.txt'),
  readFile('./c.txt')
]).then(([a, b, c]) => {
  console.log('全部读取完成:', a, b, c);
}).catch(err => {
  console.error('至少一个失败:', err.message);
});

// Promise.allSettled - 等待全部完成，无论成功失败
Promise.allSettled([
  readFile('./a.txt'),
  readFile('./missing.txt')
]).then(results => {
  results.forEach(r => {
    if (r.status === 'fulfilled') {
      console.log('成功:', r.value);
    } else {
      console.log('失败:', r.reason.message);
    }
  });
});

// Promise.race - 第一个完成的（无论成功失败）决定结果
Promise.race([
  fetch('https://api.example.com'),
  new Promise((_, reject) => setTimeout(() => reject(new Error('超时')), 5000))
]).then(data => console.log('成功')).catch(err => console.error(err.message));

// Promise.any - 第一个成功的（v15+）
Promise.any([
  fetch('https://backup1.com'),
  fetch('https://backup2.com')
]).then(data => console.log('至少一个成功'));
```

**5. 并发控制：**

```javascript
// 限制并发数的批量请求
async function mapWithConcurrency(items, limit, asyncFn) {
  const results = [];
  const executing = new Set();

  for (const item of items) {
    const p = asyncFn(item).then(result => {
      results.push(result);
      executing.delete(p);
    });
    executing.add(p);

    if (executing.size >= limit) {
      await Promise.race(executing);
    }
  }

  await Promise.all(executing);
  return results;
}

// 同时最多 3 个请求
const urls = ['url1', 'url2', 'url3', 'url4', 'url5', 'url6'];
mapWithConcurrency(urls, 3, fetch).then(results => {
  console.log('全部完成');
});
```

#### 总结

- Promise 是异步编程的标准方案，三种状态：pending/fulfilled/rejected
- 状态不可逆，resolve/reject 后无法改变
- `.then()` 链式调用解决回调地狱，错误用 `.catch()` 统一处理
- `Promise.all` 全部成功才成功，`Promise.allSettled` 等全部完成
- `Promise.race` 第一个完成的决定结果，`Promise.any` 第一个成功的
- 老的回调 API 可用 `util.promisify` 一键转 Promise
- Promise 是 async/await 的基础，务必先掌握

---

### 第 16 讲：async/await

#### 概念

`async/await` 是 ES2017 引入的异步编程语法糖，让异步代码看起来像同步代码。`async` 关键字声明异步函数（返回 Promise），`await` 关键字等待 Promise 完成。它是 Promise 的语法糖，底层仍是 Promise，但代码可读性大幅提升，是当前 Node.js 异步编程的**首选方案**。

#### 原理

`async` 函数的返回值会被自动包装成 Promise：
- 返回普通值 → `Promise.resolve(值)`
- 返回 Promise → 直接返回该 Promise
- 抛出错误 → `Promise.reject(错误)`

`await` 会暂停 async 函数的执行，等待 Promise 完成：
- Promise resolve → 返回 resolve 的值，继续执行
- Promise reject → 抛出错误，可被 try/catch 捕获
- await 非 Promise 值 → 直接返回该值

**关键点**：`await` 只能在 `async` 函数内使用（ES2022 后支持顶层 await，但需 ESM）。await 暂停的是当前 async 函数，不会阻塞主线程——其他代码（包括其他 async 函数）照常执行。

**错误处理**：async 函数中的错误用 `try/catch` 捕获，比 Promise 的 `.catch()` 更直观，能像同步代码一样处理异常。

#### 例子

**1. 基本用法：**

```javascript
const fs = require('fs').promises;

// async 函数
async function readFile() {
  try {
    const data = await fs.readFile('./config.json', 'utf8');
    const config = JSON.parse(data);
    console.log('配置:', config);
    return config;  // 返回值会被包装成 Promise
  } catch (err) {
    console.error('出错:', err.message);
    throw err;  // 重新抛出，让调用方也能处理
  }
}

// 调用 async 函数
readFile()
  .then(config => console.log('返回:', config))
  .catch(err => console.error('调用方捕获:', err.message));
```

**2. 解决回调地狱（对比第 14 讲）：**

```javascript
const fs = require('fs').promises;

async function getTotalPrice() {
  try {
    // 像同步代码一样线性书写
    const userData = await fs.readFile('./user.json', 'utf8');
    const user = JSON.parse(userData);

    const orderData = await fs.readFile(`./orders/${user.id}.json`, 'utf8');
    const order = JSON.parse(orderData);

    const productData = await fs.readFile(`./products/${order.productId}.json`, 'utf8');
    const product = JSON.parse(productData);

    return product.price * order.quantity;
  } catch (err) {
    console.error('流程出错:', err.message);
    throw err;
  }
}

getTotalPrice().then(total => console.log(`总价: ${total}`));
```

**3. 并发执行（关键技巧）：**

```javascript
const fs = require('fs').promises;

async function loadAll() {
  // ❌ 错误：串行执行，慢
  const a = await fs.readFile('./a.txt', 'utf8');
  const b = await fs.readFile('./b.txt', 'utf8');
  const c = await fs.readFile('./c.txt', 'utf8');

  // ✅ 正确：并发执行，快
  const [a, b, c] = await Promise.all([
    fs.readFile('./a.txt', 'utf8'),
    fs.readFile('./b.txt', 'utf8'),
    fs.readFile('./c.txt', 'utf8')
  ]);

  console.log(a, b, c);
}
```

**4. 循环中的 await：**

```javascript
const fs = require('fs').promises;

// 串行处理（一个接一个）
async function processSerial(files) {
  for (const file of files) {
    const data = await fs.readFile(file, 'utf8');
    console.log(file, ':', data.length);
  }
}

// 并发处理（同时开始）
async function processParallel(files) {
  await Promise.all(files.map(async (file) => {
    const data = await fs.readFile(file, 'utf8');
    console.log(file, ':', data.length);
  }));
}

// 限制并发数（重要实战技巧）
async function processWithLimit(files, limit = 3) {
  const results = [];
  for (let i = 0; i < files.length; i += limit) {
    const batch = files.slice(i, i + limit);
    const batchResults = await Promise.all(
      batch.map(file => fs.readFile(file, 'utf8'))
    );
    results.push(...batchResults);
  }
  return results;
}
```

**5. 错误处理模式：**

```javascript
// 模式一：try/catch
async function fetchUser(id) {
  try {
    const res = await fetch(`/api/users/${id}`);
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    return await res.json();
  } catch (err) {
    console.error('获取用户失败:', err.message);
    return null;  // 返回默认值
  }
}

// 模式二：.catch() 链式
async function fetchUser(id) {
  const data = await fetch(`/api/users/${id}`)
    .then(res => res.json())
    .catch(err => {
      console.error('失败:', err.message);
      return null;
    });
  return data;
}

// 模式三：to 函数（Go 风格，避免 try/catch 嵌套）
async function to(promise) {
  try {
    const data = await promise;
    return [null, data];
  } catch (err) {
    return [err, null];
  }
}

async function fetchUser(id) {
  const [err, user] = await to(fetch(`/api/users/${id}`).then(r => r.json()));
  if (err) return null;
  return user;
}
```

#### 总结

- async/await 是 Promise 的语法糖，让异步代码像同步代码
- `async` 函数返回 Promise，`await` 等待 Promise 完成
- 错误处理用 try/catch，比 `.catch()` 更直观
- 多个独立异步操作用 `Promise.all` 并发，不要串行 await
- 循环中 await 要注意：`for...of` 是串行，`map + Promise.all` 是并发
- 限制并发数是实战必备技巧，避免一次性发起太多请求
- async/await 是当前 Node.js 异步编程的首选方案

---

### 第 17 讲：异步错误处理

#### 概念

异步错误处理是 Node.js 开发的难点。同步代码用 `try/catch` 即可，但异步错误（回调中的错误、Promise 拒绝、async 函数抛出）有专门的机制。本讲系统讲解各种异步场景下的错误处理最佳实践，以及全局兜底机制。

#### 原理

异步错误处理的复杂性源于"调用栈已展开"——异步回调执行时，原始调用栈已经退出，传统的 try/catch 无法捕获。Node.js 提供了多层错误处理机制：

1. **回调风格**：错误优先约定，每层手动判断 `err`
2. **Promise**：`.catch()` 捕获链中任何一步的拒绝
3. **async/await**：try/catch 捕获 await 抛出的错误
4. **全局兜底**：`process.on('uncaughtException')` 和 `process.on('unhandledRejection')`

**关键陷阱**：
- 回调中的 `throw` 无法被外层 try/catch 捕获
- 忘记 `.catch()` 的 Promise 拒绝会变成 `unhandledRejection`
- `setTimeout` 中的错误也不会被 try/catch 捕获
- async 函数中 `forEach` 内的 await 错误不会被外层捕获（应用 `for...of`）

#### 例子

**1. 回调风格的错误处理：**

```javascript
const fs = require('fs');

// 每层都要判断 err
function readConfig(callback) {
  fs.readFile('./config.json', 'utf8', (err, data) => {
    if (err) {
      // 包装错误，附加上下文
      const wrapped = new Error(`读取配置失败: ${err.message}`);
      wrapped.cause = err;
      return callback(wrapped);
    }

    try {
      const config = JSON.parse(data);
      callback(null, config);
    } catch (parseErr) {
      callback(parseErr);
    }
  });
}

readConfig((err, config) => {
  if (err) {
    console.error('最终错误:', err.message);
    return;
  }
  console.log('配置:', config);
});
```

**2. Promise 错误处理：**

```javascript
const fs = require('fs').promises;

// 链式 .catch()
fs.readFile('./config.json', 'utf8')
  .then(data => JSON.parse(data))
  .then(config => {
    if (!config.port) {
      throw new Error('配置缺少 port 字段');  // 主动抛错
    }
    return config;
  })
  .catch(err => {
    // 捕获链中任何一步的错误
    console.error('错误:', err.message);
    return null;  // 提供默认值，链继续
  })
  .then(config => {
    // 即使前面 catch 了，这里也会执行
    console.log('最终:', config);
  });

// 区分错误类型
fetchUser(id)
  .then(user => {
    if (!user) throw new NotFoundError('用户不存在');
    return user;
  })
  .catch(err => {
    if (err instanceof NotFoundError) {
      // 处理特定错误
    } else if (err.code === 'ECONNREFUSED') {
      // 处理网络错误
    } else {
      // 未知错误
    }
  });
```

**3. async/await 错误处理：**

```javascript
async function fetchUser(id) {
  try {
    const res = await fetch(`/api/users/${id}`);

    if (!res.ok) {
      // 根据状态码抛不同错误
      if (res.status === 404) throw new NotFoundError('用户不存在');
      if (res.status === 401) throw new AuthError('未授权');
      throw new Error(`HTTP ${res.status}`);
    }

    return await res.json();
  } catch (err) {
    // 网络错误（fetch 本身抛出）
    if (err.name === 'TypeError') {
      throw new NetworkError('网络请求失败');
    }
    // 已经是我们抛出的业务错误，直接传递
    throw err;
  }
}

// 自定义错误类
class AppError extends Error {
  constructor(message, code) {
    super(message);
    this.name = this.constructor.name;
    this.code = code;
  }
}

class NotFoundError extends AppError {
  constructor(message) {
    super(message, 'NOT_FOUND');
  }
}

class NetworkError extends AppError {
  constructor(message) {
    super(message, 'NETWORK_ERROR');
  }
}
```

**4. forEach 的陷阱：**

```javascript
const fs = require('fs').promises;

// ❌ 错误：forEach 内的 await 错误无法被外层捕获
async function bad() {
  try {
    ['a.txt', 'b.txt'].forEach(async (file) => {
      const data = await fs.readFile(file, 'utf8');  // 这里抛错不会被捕获
      console.log(data);
    });
  } catch (err) {
    // 永远不会到这里
    console.error(err);
  }
}

// ✅ 正确：用 for...of
async function good() {
  try {
    for (const file of ['a.txt', 'b.txt']) {
      const data = await fs.readFile(file, 'utf8');
      console.log(data);
    }
  } catch (err) {
    console.error('捕获:', err.message);
  }
}
```

**5. 全局兜底机制：**

```javascript
// 捕获未处理的同步异常
process.on('uncaughtException', (err) => {
  console.error('未捕获异常:', err);
  // 记录日志后退出（避免状态不一致）
  // 生产环境推荐用 PM2 自动重启
  process.exit(1);
});

// 捕获未处理的 Promise 拒绝
process.on('unhandledRejection', (reason, promise) => {
  console.error('未处理的 Promise 拒绝:', reason);
  // Node.js v15+ 默认会退出进程
});

// 演示
Promise.reject(new Error('忘记 catch 了'));
// → 触发 unhandledRejection

setTimeout(() => {
  throw new Error('定时器中的错误');
}, 1000);
// → 触发 uncaughtException
```

#### 总结

- 回调风格：错误优先，每层手动判断
- Promise：用 `.catch()` 统一捕获链中错误
- async/await：try/catch，最直观
- 自定义错误类继承 Error，附加 code 等业务字段
- `forEach` 内的 await 错误无法捕获，用 `for...of` 替代
- 全局兜底：`uncaughtException` 和 `unhandledRejection`，但只能作为最后防线
- 生产环境务必接入日志系统（如 winston、pino），记录所有错误

---

### 第 18 讲：事件循环深入理解

#### 概念

**事件循环（Event Loop）** 是 Node.js 异步的核心机制。它是一个无限循环，不断从任务队列中取出回调执行。深入理解事件循环，才能写出高性能代码、避免诡异 bug、正确预测代码执行顺序。本讲深入讲解事件循环的六个阶段、微任务与宏任务的优先级。

#### 原理

Node.js 事件循环由 libuv 实现，分为六个阶段（phases），每轮循环依次执行：

```
┌───────────────────────────┐
│   timers (定时器阶段)      │  执行 setTimeout/setInterval 到期的回调
├───────────────────────────┤
│   pending callbacks       │  执行系统级回调（如 TCP 错误）
├───────────────────────────┤
│   idle, prepare           │  内部使用
├───────────────────────────┤
│   poll (轮询阶段)          │  获取新 I/O 事件，执行 I/O 回调
├───────────────────────────┤
│   check (检查阶段)         │  执行 setImmediate 回调
├───────────────────────────┤
│   close callbacks         │  执行 close 事件回调
└───────────────────────────┘
```

**微任务（Microtask）** 不属于这六个阶段，而是在每个阶段切换之间执行：
- `process.nextTick`：优先级最高，当前操作完成后立即执行
- Promise 的 `.then`/`.catch`：nextTick 之后执行

**宏任务（Macrotask）** 就是六个阶段中的回调：`setTimeout`、`setInterval`、`setImmediate`、I/O 回调等。

**执行顺序总结**：
1. 执行同步代码
2. 执行 `process.nextTick` 队列（清空）
3. 执行 Promise 微任务队列（清空）
4. 进入事件循环下一阶段，执行该阶段的宏任务
5. 阶段切换时，再次执行 nextTick 和 Promise 微任务
6. 循环往复

#### 例子

**1. 微任务优先级：**

```javascript
console.log('1. 同步');

setTimeout(() => console.log('4. setTimeout'), 0);
setImmediate(() => console.log('5. setImmediate'));

Promise.resolve().then(() => console.log('3. Promise'));
process.nextTick(() => console.log('2. nextTick'));

console.log('1.5 同步');

// 输出顺序：
// 1. 同步
// 1.5 同步
// 2. nextTick       ← 微任务，优先级最高
// 3. Promise        ← 微任务，nextTick 之后
// 4. setTimeout     ← 宏任务（timers 阶段）
// 5. setImmediate   ← 宏任务（check 阶段）
```

**2. setTimeout vs setImmediate：**

```javascript
// 在主模块中，两者顺序不确定（取决于系统调度）
setTimeout(() => console.log('timeout'), 0);
setImmediate(() => console.log('immediate'));
// 可能 timeout 先，可能 immediate 先

// 但在 I/O 回调中，setImmediate 一定先于 setTimeout
const fs = require('fs');
fs.readFile('./file.txt', () => {
  setTimeout(() => console.log('timeout'), 0);
  setImmediate(() => console.log('immediate'));
  // 一定输出: immediate 先，timeout 后
});
// 原因：I/O 回调在 poll 阶段执行，下一阶段是 check（setImmediate），再下一轮才是 timers
```

**3. 微任务会"插队"：**

```javascript
// 微任务会在每个宏任务之间清空
setTimeout(() => {
  console.log('A');
  Promise.resolve().then(() => console.log('B'));
}, 0);

setTimeout(() => {
  console.log('C');
  Promise.resolve().then(() => console.log('D'));
}, 0);

// 输出：A B C D
// 不是 A C B D
// 因为第一个 setTimeout 后，会清空微任务（B），再执行第二个 setTimeout
```

**4. process.nextTick 的陷阱：**

```javascript
// nextTick 优先级太高，可能"饿死"I/O
function recursive() {
  process.nextTick(recursive);
}
recursive();
// 这会导致 I/O 回调永远无法执行（饥饿）
// 因为 nextTick 队列永远清不完

// 实际场景：避免在 nextTick 中递归
// 用 setImmediate 替代，让 I/O 有机会执行
function safeRecursive() {
  setImmediate(safeRecursive);
}
```

**5. 实战：理解 I/O 密集型任务的执行：**

```javascript
const fs = require('fs');

console.log('开始');

fs.readFile('./file.txt', () => {
  console.log('文件读取完成');

  // 在 I/O 回调中
  setTimeout(() => console.log('内部 timeout'), 0);
  setImmediate(() => console.log('内部 immediate'));
  Promise.resolve().then(() => console.log('内部 promise'));
  process.nextTick(() => console.log('内部 nextTick'));
});

console.log('结束');

// 输出顺序：
// 开始
// 结束
// 文件读取完成
// 内部 nextTick       ← 微任务先
// 内部 promise        ← 微任务
// 内部 immediate      ← check 阶段（紧接 poll）
// 内部 timeout        ← 下一轮 timers
```

#### 总结

- 事件循环分六个阶段：timers → pending → idle → poll → check → close
- 微任务（nextTick、Promise）在每个阶段切换时清空，优先级高于宏任务
- `process.nextTick` 优先级最高，但滥用会导致 I/O 饥饿
- 主模块中 setTimeout 和 setImmediate 顺序不定，I/O 回调中 setImmediate 一定先
- 理解事件循环能预测代码执行顺序，写出高性能异步代码
- CPU 密集任务会阻塞事件循环，应放到 Worker Threads（后续讲解）

---

## 第五章：Web 服务器与 HTTP 开发

### 第 19 讲：http 模块创建服务器

#### 概念

`http` 模块是 Node.js 构建 Web 服务器的核心模块。它提供底层 HTTP 协议的实现，可以创建 HTTP 服务器和客户端。虽然实际开发多用 Express 等框架，但理解原生 `http` 模块是掌握 Node.js Web 开发的基础——所有框架都建立在它之上。

#### 原理

HTTP 协议是基于请求-响应模型的无状态协议。Node.js 的 `http` 模块底层用 C++ 实现的解析器处理 HTTP 报文，把字节流解析成 JavaScript 对象。

**核心对象**：
- `http.Server`：服务器，继承自 `net.Server`（TCP 服务器），监听端口接收请求
- `http.IncomingMessage`：请求对象，是可读流，包含请求方法、URL、headers、body
- `http.ServerResponse`：响应对象，是可写流，用于设置状态码、headers、响应体

**工作流程**：
1. 客户端发起 TCP 连接
2. 服务器接收连接，触发 `connection` 事件
3. Node.js 解析 HTTP 请求行和头部，触发 `request` 事件
4. 业务代码处理请求，通过 `res.end()` 发送响应
5. 连接关闭或保持（Keep-Alive）

**关键特性**：
- 单线程处理所有请求：I/O 是异步的，但 JS 执行是单线程的
- 一个请求对应一个 `request` 事件回调
- 请求体是流，需要手动收集（不像 PHP 自动放入 `$_POST`）
- 响应必须显式 `end()`，否则连接不会关闭

#### 例子

**1. 最简单的 HTTP 服务器：**

```javascript
const http = require('http');

const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hello, Node.js!');
});

server.listen(3000, () => {
  console.log('服务器运行在 http://localhost:3000');
});

// 访问 http://localhost:3000 看到 "Hello, Node.js!"
```

**2. 读取请求信息：**

```javascript
const http = require('http');

const server = http.createServer((req, res) => {
  // 请求方法：GET / POST / PUT / DELETE
  console.log('方法:', req.method);

  // 请求 URL（含查询字符串）
  console.log('URL:', req.url);

  // 请求头（小写）
  console.log('Headers:', req.headers);
  console.log('User-Agent:', req.headers['user-agent']);
  console.log('Content-Type:', req.headers['content-type']);

  // HTTP 版本
  console.log('HTTP 版本:', req.httpVersion);

  // 客户端 IP
  console.log('IP:', req.socket.remoteAddress);

  res.end('OK');
});

server.listen(3000);
```

**3. 解析 URL 和查询参数：**

```javascript
const http = require('http');
const url = require('url');
const querystring = require('querystring');

const server = http.createServer((req, res) => {
  // 解析 URL
  const parsedUrl = url.parse(req.url, true);
  console.log('路径:', parsedUrl.pathname);
  console.log('查询参数:', parsedUrl.query);

  // 访问 /users?name=Alice&age=25
  // pathname: /users
  // query: { name: 'Alice', age: '25' }

  res.writeHead(200, { 'Content-Type': 'application/json' });
  res.end(JSON.stringify({
    path: parsedUrl.pathname,
    query: parsedUrl.query
  }));
});

server.listen(3000);
```

**4. 处理 POST 请求体：**

```javascript
const http = require('http');

const server = http.createServer((req, res) => {
  if (req.method === 'POST') {
    // 请求体是流，需要手动收集
    let body = '';

    req.on('data', (chunk) => {
      body += chunk.toString();
      // 防止超大请求体攻击
      if (body.length > 1e6) {  // 1MB 限制
        req.destroy();
        res.writeHead(413, { 'Content-Type': 'text/plain' });
        res.end('请求体过大');
      }
    });

    req.on('end', () => {
      try {
        const data = JSON.parse(body);
        res.writeHead(200, { 'Content-Type': 'application/json' });
        res.end(JSON.stringify({ received: data }));
      } catch (err) {
        res.writeHead(400, { 'Content-Type': 'text/plain' });
        res.end('无效的 JSON');
      }
    });
  } else {
    res.writeHead(405, { 'Content-Type': 'text/plain' });
    res.end('只支持 POST');
  }
});

server.listen(3000);
```

**5. 返回不同类型的内容：**

```javascript
const http = require('http');
const fs = require('fs');

const server = http.createServer((req, res) => {
  const path = req.url;

  if (path === '/') {
    // HTML
    res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
    res.end('<h1>首页</h1><p>欢迎</p>');

  } else if (path === '/api/users') {
    // JSON
    const users = [{ id: 1, name: 'Alice' }, { id: 2, name: 'Bob' }];
    res.writeHead(200, { 'Content-Type': 'application/json; charset=utf-8' });
    res.end(JSON.stringify(users));

  } else if (path === '/image') {
    // 二进制（图片）
    const imgBuffer = fs.readFileSync('./logo.png');
    res.writeHead(200, { 'Content-Type': 'image/png' });
    res.end(imgBuffer);

  } else if (path === '/download') {
    // 文件下载
    res.writeHead(200, {
      'Content-Type': 'application/octet-stream',
      'Content-Disposition': 'attachment; filename="data.csv"'
    });
    fs.createReadStream('./data.csv').pipe(res);

  } else if (path === '/redirect') {
    // 重定向
    res.writeHead(302, { 'Location': '/' });
    res.end();

  } else {
    // 404
    res.writeHead(404, { 'Content-Type': 'text/plain' });
    res.end('Not Found');
  }
});

server.listen(3000);
```

**6. HTTP 客户端：**

```javascript
const http = require('http');

// GET 请求
http.get('http://localhost:3000/api/users', (res) => {
  let data = '';
  res.on('data', (chunk) => data += chunk);
  res.on('end', () => {
    console.log('响应:', JSON.parse(data));
  });
}).on('error', (err) => {
  console.error('请求失败:', err.message);
});

// POST 请求
const postData = JSON.stringify({ name: 'Alice', age: 25 });

const req = http.request({
  hostname: 'localhost',
  port: 3000,
  path: '/api/users',
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Content-Length': Buffer.byteLength(postData)
  }
}, (res) => {
  let data = '';
  res.on('data', (chunk) => data += chunk);
  res.on('end', () => console.log('响应:', data));
});

req.on('error', (err) => console.error(err));
req.write(postData);  // 写入请求体
req.end();  // 结束请求
```

#### 总结

- `http.createServer` 创建服务器，回调接收 `req`（请求）和 `res`（响应）
- `req` 是可读流，`res` 是可写流，必须 `res.end()` 结束响应
- 请求体需要手动收集（监听 `data` 和 `end` 事件）
- 注意设置正确的 `Content-Type`，中文要加 `charset=utf-8`
- 大文件用 `stream.pipe(res)` 流式返回，避免内存爆炸
- 生产环境推荐用 Express，但理解原生 `http` 是基础

---

### 第 20 讲：路由设计

#### 概念

**路由（Routing）** 是 Web 服务器根据请求的 URL 和方法，分发到不同处理函数的机制。原生 `http` 模块没有内置路由，需要手动解析 URL 并匹配。本讲讲解如何用原生方式实现路由，为理解 Express 路由打下基础。

#### 原理

路由的核心是**模式匹配**。一个路由由三部分组成：
- **HTTP 方法**：GET、POST、PUT、DELETE 等
- **路径模式**：`/users`、`/users/:id`、`/posts/:postId/comments`
- **处理函数**：`(req, res) => {}`

路由匹配算法：
1. 解析请求的 method 和 pathname
2. 遍历路由表，找到第一个匹配的
3. 提取路径参数（如 `/users/:id` 中的 `id`）
4. 调用对应的处理函数

**路径参数提取**：把注册的模式 `/users/:id` 转成正则 `/^\/users\/([^/]+)$/`，匹配时用捕获组提取参数值。

#### 例子

**1. 简单路由：**

```javascript
const http = require('http');
const url = require('url');

const routes = [];

function addRoute(method, path, handler) {
  routes.push({ method, path, handler });
}

// 注册路由
addRoute('GET', '/', (req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('首页');
});

addRoute('GET', '/about', (req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('关于我们');
});

addRoute('GET', '/api/users', (req, res) => {
  res.writeHead(200, { 'Content-Type': 'application/json' });
  res.end(JSON.stringify([{ id: 1, name: 'Alice' }]));
});

const server = http.createServer((req, res) => {
  const parsedUrl = url.parse(req.url, true);
  const pathname = parsedUrl.pathname;

  const route = routes.find(
    r => r.method === req.method && r.path === pathname
  );

  if (route) {
    route.handler(req, res);
  } else {
    res.writeHead(404, { 'Content-Type': 'text/plain' });
    res.end('Not Found');
  }
});

server.listen(3000);
```

**2. 支持路径参数：**

```javascript
const http = require('http');
const url = require('url');

const routes = [];

function addRoute(method, pattern, handler) {
  // 把 /users/:id 转成 /users/([^/]+)
  const paramNames = [];
  const regexStr = pattern.replace(/:([^/]+)/g, (_, name) => {
    paramNames.push(name);
    return '([^/]+)';
  });

  routes.push({
    method,
    pattern,
    regex: new RegExp(`^${regexStr}$`),
    paramNames,
    handler
  });
}

addRoute('GET', '/users/:id', (req, res, params) => {
  res.writeHead(200, { 'Content-Type': 'application/json' });
  res.end(JSON.stringify({ userId: params.id }));
});

addRoute('GET', '/posts/:postId/comments/:commentId', (req, res, params) => {
  res.writeHead(200, { 'Content-Type': 'application/json' });
  res.end(JSON.stringify(params));
});

const server = http.createServer((req, res) => {
  const parsedUrl = url.parse(req.url, true);
  const pathname = parsedUrl.pathname;

  for (const route of routes) {
    if (route.method !== req.method) continue;

    const match = route.regex.exec(pathname);
    if (match) {
      // 提取参数
      const params = {};
      route.paramNames.forEach((name, i) => {
        params[name] = match[i + 1];
      });
      route.handler(req, res, params, parsedUrl.query);
      return;
    }
  }

  res.writeHead(404, { 'Content-Type': 'text/plain' });
  res.end('Not Found');
});

server.listen(3000);
```

**3. 支持查询参数和请求体：**

```javascript
const http = require('http');
const url = require('url');

// 工具：收集请求体
function parseBody(req) {
  return new Promise((resolve) => {
    let body = '';
    req.on('data', (chunk) => body += chunk);
    req.on('end', () => {
      try {
        resolve(body ? JSON.parse(body) : {});
      } catch {
        resolve({});
      }
    });
  });
}

const server = http.createServer(async (req, res) => {
  const parsedUrl = url.parse(req.url, true);
  const { pathname, query } = parsedUrl;

  // GET /search?keyword=node&page=1
  if (req.method === 'GET' && pathname === '/search') {
    res.writeHead(200, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify({
      keyword: query.keyword,
      page: parseInt(query.page) || 1
    }));
    return;
  }

  // POST /api/users  body: {"name":"Alice"}
  if (req.method === 'POST' && pathname === '/api/users') {
    const body = await parseBody(req);
    res.writeHead(201, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify({ id: Date.now(), ...body }));
    return;
  }

  res.writeHead(404, { 'Content-Type': 'text/plain' });
  res.end('Not Found');
});

server.listen(3000);
```

**4. 静态文件服务：**

```javascript
const http = require('http');
const fs = require('fs');
const path = require('path');

const MIME_TYPES = {
  '.html': 'text/html; charset=utf-8',
  '.css': 'text/css',
  '.js': 'application/javascript',
  '.json': 'application/json',
  '.png': 'image/png',
  '.jpg': 'image/jpeg',
  '.gif': 'image/gif',
  '.svg': 'image/svg+xml',
  '.ico': 'image/x-icon',
  '.pdf': 'application/pdf'
};

const PUBLIC_DIR = path.join(__dirname, 'public');

const server = http.createServer((req, res) => {
  let filePath = path.join(PUBLIC_DIR, req.url === '/' ? 'index.html' : req.url);

  // 安全检查：防止路径穿越
  if (!filePath.startsWith(PUBLIC_DIR)) {
    res.writeHead(403);
    res.end('Forbidden');
    return;
  }

  const ext = path.extname(filePath);
  const contentType = MIME_TYPES[ext] || 'application/octet-stream';

  fs.readFile(filePath, (err, data) => {
    if (err) {
      if (err.code === 'ENOENT') {
        res.writeHead(404, { 'Content-Type': 'text/plain' });
        res.end('Not Found');
      } else {
        res.writeHead(500);
        res.end('Server Error');
      }
      return;
    }
    res.writeHead(200, { 'Content-Type': contentType });
    res.end(data);
  });
});

server.listen(3000, () => console.log('静态服务运行在 3000'));
```

#### 总结

- 路由 = HTTP 方法 + 路径模式 + 处理函数
- 路径参数用 `:name` 形式，通过正则提取
- 查询参数用 `url.parse` 解析
- POST 请求体需要手动收集并解析
- 静态文件服务要注意路径穿越攻击
- 原生路由繁琐，实际开发用 Express 等框架

---

### 第 21 讲：Express 框架入门

#### 概念

**Express** 是 Node.js 最流行的 Web 框架，提供简洁灵活的 API 构建 Web 应用和 API。它基于原生 `http` 模块，封装了路由、中间件、请求/响应增强等功能，让开发者专注于业务逻辑。Express 是事实上的标准，几乎所有 Node.js Web 项目都直接或间接使用它。

#### 原理

Express 的核心设计是**中间件机制**：一个请求经过一系列中间件处理，每个中间件可以修改请求/响应、终止请求或传递给下一个中间件。这种"管道"模式让功能拆分清晰、易于扩展。

**Express 应用结构**：
- `app` 对象：核心，注册路由和中间件
- 路由：`app.get/post/put/delete(path, handler)`
- 中间件：`app.use(middleware)` 或 `app.use(path, middleware)`
- 请求增强：`req` 对象扩展了 `params`、`query`、`body`、`cookies` 等
- 响应增强：`res` 对象扩展了 `json()`、`send()`、`render()`、`redirect()` 等

**请求处理流程**：
1. 收到请求，创建 `req` 和 `res` 对象
2. 按注册顺序执行匹配的中间件
3. 每个中间件调用 `next()` 传递控制权
4. 不调用 `next()` 则请求终止
5. 路由处理函数也是中间件的一种

#### 例子

**1. 安装与 Hello World：**

```bash
# 初始化项目
npm init -y

# 安装 Express
npm install express
```

```javascript
const express = require('express');
const app = express();
const PORT = 3000;

app.get('/', (req, res) => {
  res.send('Hello, Express!');
});

app.listen(PORT, () => {
  console.log(`服务器运行在 http://localhost:${PORT}`);
});
```

**2. 路由定义：**

```javascript
const express = require('express');
const app = express();

// 不同 HTTP 方法
app.get('/users', (req, res) => {
  res.json([{ id: 1, name: 'Alice' }, { id: 2, name: 'Bob' }]);
});

app.post('/users', (req, res) => {
  res.status(201).json({ message: '用户创建成功', data: req.body });
});

app.put('/users/:id', (req, res) => {
  res.json({ message: `更新用户 ${req.params.id}`, data: req.body });
});

app.delete('/users/:id', (req, res) => {
  res.json({ message: `删除用户 ${req.params.id}` });
});

// 所有方法
app.all('/health', (req, res) => {
  res.json({ status: 'ok', time: new Date() });
});

// 监听
app.listen(3000);
```

**3. 请求参数：**

```javascript
const express = require('express');
const app = express();

// 解析 JSON 请求体
app.use(express.json());
// 解析 URL 编码请求体
app.use(express.urlencoded({ extended: true }));

// 路径参数
app.get('/users/:userId', (req, res) => {
  console.log(req.params);  // { userId: '123' }
  res.json({ userId: req.params.userId });
});

// 查询参数：/search?keyword=node&limit=10
app.get('/search', (req, res) => {
  console.log(req.query);  // { keyword: 'node', limit: '10' }
  res.json(req.query);
});

// 请求头
app.get('/headers', (req, res) => {
  console.log(req.headers);
  console.log(req.get('User-Agent'));  // 获取特定头
  res.json({ ua: req.get('user-agent') });
});

// 请求体（POST）
app.post('/users', (req, res) => {
  console.log(req.body);  // 已解析的对象
  res.json({ received: req.body });
});

app.listen(3000);
```

**4. 响应方法：**

```javascript
const express = require('express');
const app = express();

// 发送字符串
app.get('/text', (req, res) => {
  res.send('纯文本');
});

// 发送 JSON
app.get('/json', (req, res) => {
  res.json({ message: 'success', data: [1, 2, 3] });
});

// 设置状态码
app.get('/created', (req, res) => {
  res.status(201).json({ created: true });
});

// 重定向
app.get('/old', (req, res) => {
  res.redirect('/new');
});
app.get('/new', (req, res) => {
  res.send('新页面');
});

// 发送文件
app.get('/download', (req, res) => {
  res.download('./file.pdf', 'report.pdf');
});

// 设置响应头
app.get('/custom', (req, res) => {
  res.set('X-Custom-Header', 'hello');
  res.set({
    'X-Request-Id': '12345',
    'X-Timestamp': Date.now().toString()
  });
  res.send('查看响应头');
});

// Cookie
app.get('/set-cookie', (req, res) => {
  res.cookie('token', 'abc123', {
    httpOnly: true,
    maxAge: 24 * 60 * 60 * 1000  // 1 天
  });
  res.send('Cookie 已设置');
});

app.listen(3000);
```

**5. 路由模块化：**

```javascript
// routes/users.js
const express = require('express');
const router = express.Router();

router.get('/', (req, res) => {
  res.json([{ id: 1, name: 'Alice' }]);
});

router.get('/:id', (req, res) => {
  res.json({ id: req.params.id, name: 'Alice' });
});

router.post('/', (req, res) => {
  res.status(201).json({ message: '创建成功' });
});

module.exports = router;

// app.js
const express = require('express');
const app = express();
const usersRouter = require('./routes/users');

app.use('/users', usersRouter);  // 挂载到 /users 路径

app.listen(3000);
// GET    /users        → 列表
// GET    /users/123    → 详情
// POST   /users        → 创建
```

#### 总结

- Express 是 Node.js 最流行的 Web 框架，基于原生 `http` 模块
- 核心是中间件机制，请求经过一系列中间件处理
- `app.get/post/put/delete` 定义路由，`app.use` 注册中间件
- `req` 增强：`params`、`query`、`body`、`headers`
- `res` 增强：`json()`、`send()`、`status()`、`redirect()`、`download()`
- 用 `express.Router()` 模块化路由，大型项目必备
- 生产环境务必加错误处理中间件和安全头（helmet）

---

### 第 22 讲：Express 中间件机制

#### 概念

**中间件（Middleware）** 是 Express 的核心设计。中间件是一个函数，接收 `req`、`res`、`next` 三个参数，可以执行任何代码、修改请求/响应、终止请求或调用 `next()` 传递控制权。Express 应用本质上就是一堆中间件的组合。理解中间件是掌握 Express 的关键。

#### 原理

Express 维护一个中间件栈（数组），请求到来时按顺序执行匹配的中间件。每个中间件可以选择：
- 调用 `next()`：传递给下一个中间件
- 不调用 `next()`：终止请求（如发送响应后）
- 调用 `next(err)`：跳到错误处理中间件

**中间件类型**：
- **应用级中间件**：`app.use(fn)` 或 `app.use('/path', fn)`，对所有请求生效
- **路由级中间件**：`router.use(fn)`，挂载到 `express.Router()`
- **错误处理中间件**：`(err, req, res, next)`，4 个参数，专门处理错误
- **内置中间件**：`express.static`、`express.json`、`express.urlencoded`
- **第三方中间件**：`cors`、`helmet`、`morgan` 等

**执行顺序**：中间件按注册顺序执行。`app.use` 注册的先执行，`app.get/post` 等路由方法也是中间件，按定义顺序匹配。

#### 例子

**1. 基本中间件：**

```javascript
const express = require('express');
const app = express();

// 应用级中间件：每个请求都执行
app.use((req, res, next) => {
  console.log(`${req.method} ${req.url} - ${new Date().toISOString()}`);
  next();  // 必须调用 next，否则请求挂起
});

// 路径特定中间件：只对 /api/* 生效
app.use('/api', (req, res, next) => {
  console.log('API 请求');
  next();
});

// 路由处理（也是中间件）
app.get('/', (req, res) => {
  res.send('首页');
});

app.get('/api/users', (req, res) => {
  res.json([{ id: 1 }]);
});

app.listen(3000);
```

**2. 多个中间件链式调用：**

```javascript
const express = require('express');
const app = express();

// 鉴权中间件
function authMiddleware(req, res, next) {
  const token = req.headers.authorization;
  if (!token) {
    return res.status(401).json({ error: '未提供 token' });
  }
  // 模拟验证
  if (token !== 'Bearer valid-token') {
    return res.status(403).json({ error: 'token 无效' });
  }
  req.user = { id: 1, name: 'Alice' };  // 挂载用户信息
  next();
}

// 日志中间件
function logMiddleware(req, res, next) => {
  req.startTime = Date.now();
  res.on('finish', () => {
    const duration = Date.now() - req.startTime;
    console.log(`${req.method} ${req.url} ${res.statusCode} ${duration}ms`);
  });
  next();
}

// 使用多个中间件
app.use(logMiddleware);

// 公开路由
app.get('/public', (req, res) => {
  res.json({ message: '公开接口' });
});

// 受保护路由（多个中间件）
app.get('/profile', authMiddleware, (req, res) => {
  res.json({ user: req.user });
});

app.get('/settings', authMiddleware, (req, res) => {
  res.json({ user: req.user, settings: {} });
});

app.listen(3000);
```

**3. 错误处理中间件：**

```javascript
const express = require('express');
const app = express();

app.get('/error', (req, res, next) => {
  try {
    throw new Error('出错了');
  } catch (err) {
    next(err);  // 传递给错误处理中间件
  }
});

app.get('/async-error', async (req, res, next) => {
  try {
    // 模拟异步错误
    await Promise.reject(new Error('异步错误'));
  } catch (err) {
    next(err);
  }
});

// 错误处理中间件（4 个参数，必须放最后）
app.use((err, req, res, next) => {
  console.error('错误:', err.message);

  // 根据环境返回不同信息
  const isDev = process.env.NODE_ENV !== 'production';
  res.status(err.status || 500).json({
    error: err.message,
    stack: isDev ? err.stack : undefined
  });
});

app.listen(3000);
```

**4. 常用第三方中间件：**

```javascript
const express = require('express');
const app = express();

// CORS 跨域
const cors = require('cors');
app.use(cors({
  origin: ['http://localhost:8080', 'https://example.com'],
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization'],
  credentials: true
}));

// 安全头
const helmet = require('helmet');
app.use(helmet());

// HTTP 请求日志
const morgan = require('morgan');
app.use(morgan('combined'));  // 或 'dev'、'tiny'

// 解析请求体
app.use(express.json({ limit: '10mb' }));
app.use(express.urlencoded({ extended: true }));

// 静态文件
app.use('/static', express.static('public', {
  maxAge: '1d',
  etag: true
}));

// Cookie 解析
const cookieParser = require('cookie-parser');
app.use(cookieParser());

app.get('/', (req, res) => {
  res.json({ cookies: req.cookies });
});

app.listen(3000);
```

**5. 自定义中间件：请求限流**

```javascript
const express = require('express');
const app = express();

// 简单的限流中间件
function rateLimit(options = {}) {
  const {
    windowMs = 60 * 1000,  // 1 分钟窗口
    max = 100              // 最多 100 次
  } = options;

  const requests = new Map();  // ip -> [timestamps]

  return (req, res, next) => {
    const ip = req.ip;
    const now = Date.now();
    const windowStart = now - windowMs;

    // 清理过期记录
    if (!requests.has(ip)) requests.set(ip, []);
    const timestamps = requests.get(ip).filter(t => t > windowStart);

    if (timestamps.length >= max) {
      return res.status(429).json({
        error: '请求过于频繁',
        retryAfter: Math.ceil(windowMs / 1000)
      });
    }

    timestamps.push(now);
    next();
  };
}

app.use('/api', rateLimit({ windowMs: 60 * 1000, max: 30 }));

app.get('/api/data', (req, res) => {
  res.json({ data: 'success' });
});

app.listen(3000);
```

#### 总结

- 中间件是 Express 的核心，请求经过中间件链处理
- 必须调用 `next()` 传递控制权，否则请求挂起
- 错误处理中间件有 4 个参数，必须放最后
- 中间件可挂载到特定路径，只对该路径生效
- 常用第三方中间件：`cors`、`helmet`、`morgan`、`cookie-parser`
- 自定义中间件可实现鉴权、限流、日志等通用功能
- 中间件顺序很重要，错误顺序会导致逻辑错误

---

### 第 23 讲：RESTful API 设计

#### 概念

**RESTful API** 是一种 Web API 设计风格，基于 HTTP 协议的语义，用 URL 表示资源，用 HTTP 方法表示操作。它简洁、标准、易于理解，是当前 Web API 设计的主流方案。本讲讲解 RESTful 设计原则，并用 Express 实现一个完整的 CRUD API。

#### 原理

REST（Representational State Transfer）的核心原则：
1. **资源导向**：URL 表示资源，如 `/users`、`/posts`，不是动作
2. **HTTP 方法语义化**：
   - `GET`：获取资源（安全、幂等）
   - `POST`：创建资源（非幂等）
   - `PUT`：完整更新资源（幂等）
   - `PATCH`：部分更新资源（幂等）
   - `DELETE`：删除资源（幂等）
3. **状态码标准化**：
   - `200 OK`：成功
   - `201 Created`：创建成功
   - `204 No Content`：成功无内容（如删除）
   - `400 Bad Request`：请求错误
   - `401 Unauthorized`：未认证
   - `403 Forbidden`：无权限
   - `404 Not Found`：资源不存在
   - `500 Internal Server Error`：服务器错误
4. **无状态**：每个请求包含所有信息，服务器不保存客户端状态
5. **统一响应格式**：JSON，结构一致

**URL 设计规范**：
- 用名词复数：`/users` 不是 `/user` 或 `/getUsers`
- 层级表示关系：`/users/:userId/posts`
- 查询参数过滤：`/users?role=admin&page=1`

#### 例子

**1. 完整的 RESTful API 实现：**

```javascript
const express = require('express');
const app = express();

app.use(express.json());

// 模拟数据库
let users = [
  { id: 1, name: 'Alice', email: 'alice@example.com', age: 25 },
  { id: 2, name: 'Bob', email: 'bob@example.com', age: 30 }
];
let nextId = 3;

// 统一响应格式
function success(data, message = 'success') {
  return { code: 0, message, data };
}

function error(message, code = -1) {
  return { code, message, data: null };
}

// GET /users - 获取列表（支持分页、筛选）
app.get('/users', (req, res) => {
  let result = [...users];

  // 筛选
  if (req.query.name) {
    result = result.filter(u =>
      u.name.includes(req.query.name)
    );
  }

  // 分页
  const page = parseInt(req.query.page) || 1;
  const limit = parseInt(req.query.limit) || 10;
  const total = result.length;
  const data = result.slice((page - 1) * limit, page * limit);

  res.json(success({
    list: data,
    pagination: { page, limit, total }
  }));
});

// GET /users/:id - 获取详情
app.get('/users/:id', (req, res) => {
  const user = users.find(u => u.id === parseInt(req.params.id));
  if (!user) {
    return res.status(404).json(error('用户不存在'));
  }
  res.json(success(user));
});

// POST /users - 创建
app.post('/users', (req, res) => {
  const { name, email, age } = req.body;

  // 参数校验
  if (!name || !email) {
    return res.status(400).json(error('name 和 email 必填'));
  }

  // 检查邮箱唯一
  if (users.some(u => u.email === email)) {
    return res.status(409).json(error('邮箱已存在'));
  }

  const user = { id: nextId++, name, email, age: age || 0 };
  users.push(user);
  res.status(201).json(success(user, '创建成功'));
});

// PUT /users/:id - 完整更新
app.put('/users/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const index = users.findIndex(u => u.id === id);
  if (index === -1) {
    return res.status(404).json(error('用户不存在'));
  }

  const { name, email, age } = req.body;
  if (!name || !email) {
    return res.status(400).json(error('name 和 email 必填'));
  }

  users[index] = { id, name, email, age: age || 0 };
  res.json(success(users[index], '更新成功'));
});

// PATCH /users/:id - 部分更新
app.patch('/users/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const user = users.find(u => u.id === id);
  if (!user) {
    return res.status(404).json(error('用户不存在'));
  }

  Object.assign(user, req.body);  // 只更新提供的字段
  res.json(success(user, '更新成功'));
});

// DELETE /users/:id - 删除
app.delete('/users/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const index = users.findIndex(u => u.id === id);
  if (index === -1) {
    return res.status(404).json(error('用户不存在'));
  }

  users.splice(index, 1);
  res.status(204).end();  // 204 无内容
});

app.listen(3000, () => console.log('API 服务运行在 3000'));
```

**2. 嵌套路由：**

```javascript
// /users/:userId/posts - 用户的文章
app.get('/users/:userId/posts', (req, res) => {
  const userId = parseInt(req.params.userId);
  const posts = db.posts.filter(p => p.userId === userId);
  res.json(success(posts));
});

app.post('/users/:userId/posts', (req, res) => {
  const userId = parseInt(req.params.userId);
  const user = db.users.find(u => u.id === userId);
  if (!user) return res.status(404).json(error('用户不存在'));

  const post = {
    id: nextPostId++,
    userId,
    title: req.body.title,
    content: req.body.content
  };
  db.posts.push(post);
  res.status(201).json(success(post));
});
```

**3. API 版本控制：**

```javascript
// 通过 URL 路径
app.use('/v1', v1Router);
app.use('/v2', v2Router);

// 通过 Header
app.use((req, res, next) => {
  req.apiVersion = req.get('Accept-Version') || 'v1';
  next();
});
```

**4. 接口文档（可用 Swagger）：**

```javascript
// 安装：npm install swagger-ui-express swagger-jsdoc
const swaggerUi = require('swagger-ui-express');
const swaggerJsdoc = require('swagger-jsdoc');

const specs = swaggerJsdoc({
  definition: {
    openapi: '3.0.0',
    info: { title: '用户 API', version: '1.0.0' }
  },
  apis: ['./routes/*.js']  // 从注释提取
});

app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(specs));
```

#### 总结

- RESTful 用 URL 表示资源，HTTP 方法表示操作
- 标准方法：GET 查、POST 增、PUT 全量改、PATCH 部分改、DELETE 删
- 状态码要语义化：200/201/204/400/404/500
- 统一响应格式：`{ code, message, data }`
- 分页、筛选用查询参数：`?page=1&limit=10&name=alice`
- 嵌套资源用层级 URL：`/users/:id/posts`
- 生产环境务必加参数校验（如 joi 库）和接口文档

---

## 第六章：数据存储

### 第 24 讲：Buffer 与二进制数据

#### 概念

**Buffer** 是 Node.js 处理二进制数据的核心类。JavaScript 原生没有二进制数据类型（只有字符串），但 Node.js 需要处理文件 I/O、网络数据、加密等二进制场景。Buffer 是一段固定长度的内存区域，类似数组但存储的是字节（0-255）。ES6 引入的 `Uint8Array` 是 Buffer 的底层实现。

#### 原理

Buffer 在 V8 堆外分配内存（C++ 层），直接操作原始字节，性能高。每个字节是 8 位，值范围 0-255。Buffer 不可变大小（创建后长度固定），但内容可修改。

**Buffer vs 字符串**：
- 字符串是文本的抽象，编码后（如 UTF-8）变成字节
- Buffer 是原始字节，可以表示任何二进制数据
- 同一字符串用不同编码，Buffer 长度不同（中文 UTF-8 占 3 字节，GBK 占 2 字节）

**编码类型**：
- `utf8`：默认，Unicode 文本
- `ascii`：纯英文
- `base64`：Base64 编码（图片转文本传输）
- `hex`：十六进制表示
- `binary`/`latin1`：每个字节一个字符
- `ucs2`/`utf16le`：双字节 Unicode

**关键 API**：
- `Buffer.alloc(size)`：分配指定大小（清零，安全）
- `Buffer.allocUnsafe(size)`：分配但不清零（快，可能含旧数据）
- `Buffer.from(...)`：从字符串、数组、其他 Buffer 创建
- `Buffer.concat([...])`：拼接多个 Buffer

#### 例子

**1. 创建 Buffer：**

```javascript
// 从字符串创建
const buf1 = Buffer.from('Hello', 'utf8');
console.log(buf1);  // <Buffer 48 65 6c 6c 6f>
console.log(buf1.length);  // 5（字节数，不是字符数）

// 中文：UTF-8 下每个中文 3 字节
const buf2 = Buffer.from('你好', 'utf8');
console.log(buf2.length);  // 6

// 从数组创建
const buf3 = Buffer.from([0x48, 0x65, 0x6c, 0x6c, 0x6f]);
console.log(buf3.toString());  // Hello

// 分配指定大小
const buf4 = Buffer.alloc(10);  // 10 字节，清零
console.log(buf4);  // <Buffer 00 00 00 00 00 00 00 00 00 00>

const buf5 = Buffer.allocUnsafe(10);  // 不清零，更快但可能含旧数据
buf5.fill(0);  // 手动清零

// 从 Base64 创建
const buf6 = Buffer.from('SGVsbG8=', 'base64');
console.log(buf6.toString());  // Hello
```

**2. Buffer 读写：**

```javascript
const buf = Buffer.alloc(16);

// 写入字符串
buf.write('Hello', 0, 'utf8');  // 从偏移 0 写入
console.log(buf.toString('utf8', 0, 5));  // Hello

// 写入数值（不同字节序）
buf.writeUInt8(255, 5);       // 1 字节，无符号
buf.writeUInt16BE(1000, 6);   // 2 字节，大端序
buf.writeUInt32LE(100000, 8); // 4 字节，小端序

// 读取
console.log(buf.readUInt8(5));       // 255
console.log(buf.readUInt16BE(6));    // 1000
console.log(buf.readUInt32LE(8));    // 100000

// 直接索引访问（每个元素是 0-255）
buf[0] = 72;  // 'H'
console.log(buf[0]);  // 72
```

**3. Buffer 与字符串转换：**

```javascript
const text = 'Hello, 世界!';

// 转 Buffer
const buf = Buffer.from(text, 'utf8');
console.log('字节数:', buf.length);  // 13（中文各 3 字节）

// 转回字符串
console.log(buf.toString('utf8'));  // Hello, 世界!

// Base64 编码（常用于图片传输）
const base64 = buf.toString('base64');
console.log('Base64:', base64);

// 从 Base64 解码
const decoded = Buffer.from(base64, 'base64').toString('utf8');
console.log('解码:', decoded);

// Hex 编码
const hex = buf.toString('hex');
console.log('Hex:', hex);
```

**4. Buffer 拼接与切片：**

```javascript
// 拼接
const buf1 = Buffer.from('Hello ');
const buf2 = Buffer.from('World');
const combined = Buffer.concat([buf1, buf2]);
console.log(combined.toString());  // Hello World

// 拼接时指定总长度（优化）
const combined2 = Buffer.concat([buf1, buf2], 11);

// 切片（共享内存，修改会影响原 Buffer）
const buf = Buffer.from('Hello World');
const slice = buf.subarray(0, 5);  // 推荐 subarray 而非 slice
console.log(slice.toString());  // Hello

slice[0] = 104;  // 改成小写 h
console.log(buf.toString());  // hello World（原 Buffer 也变了）

// 复制（独立内存）
const copy = Buffer.from(buf);
copy[0] = 72;  // 不影响原 Buffer
```

**5. 实战：处理 HTTP 流的二进制数据**

```javascript
const http = require('http');

// 接收二进制数据（如图片上传）
const server = http.createServer((req, res) => {
  const chunks = [];

  req.on('data', (chunk) => {
    chunks.push(chunk);  // chunk 是 Buffer
  });

  req.on('end', () => {
    const buf = Buffer.concat(chunks);  // 合并所有块
    console.log('收到数据:', buf.length, '字节');
    console.log('前 16 字节:', buf.subarray(0, 16));

    // 判断文件类型（魔数）
    if (buf.subarray(0, 3).toString('hex') === 'ffd8ff') {
      console.log('是 JPEG 图片');
    } else if (buf.subarray(0, 4).toString('hex') === '89504e47') {
      console.log('是 PNG 图片');
    }

    res.json({ size: buf.length });
  });
});

server.listen(3000);
```

#### 总结

- Buffer 是 Node.js 处理二进制数据的核心，类似字节数组
- `Buffer.from` 创建，`Buffer.alloc` 分配，`Buffer.concat` 拼接
- 字符串和 Buffer 通过编码转换，UTF-8 中文占 3 字节
- Base64 编码常用于图片等二进制数据的文本传输
- `subarray` 切片共享内存，修改会影响原 Buffer
- 文件 I/O、网络数据都是 Buffer，处理时注意编码

---

### 第 25 讲：文件存储与流式上传

#### 概念

文件上传是 Web 应用的常见需求。传统方式把整个文件读入内存再保存，大文件会撑爆内存。**流式上传**用流（Stream）边接收边写入磁盘，内存占用恒定。本讲讲解如何用 Express 处理文件上传，包括单文件、多文件、大文件流式上传。

#### 原理

文件上传使用 `multipart/form-data` 编码，请求体被分成多个部分（parts），每部分有自己的头和内容。Express 默认不解析这种格式，需要用 `multer` 中间件。

**multer 的工作原理**：
1. 接收请求时，检测 `Content-Type: multipart/form-data`
2. 流式解析请求体，逐个处理 part
3. 文件 part 写入磁盘（或内存），文本 part 解析为字段
4. 把文件信息挂到 `req.file`（单文件）或 `req.files`（多文件）

**大文件处理策略**：
- 用 `diskStorage` 直接写磁盘，不经过内存
- 限制文件大小，防止恶意大文件
- 限制字段数和文件数
- 用流式处理（如视频转码）时，直接 pipe 到处理流

#### 例子

**1. 安装 multer：**

```bash
npm install multer
```

**2. 单文件上传：**

```javascript
const express = require('express');
const multer = require('multer');
const path = require('path');
const fs = require('fs');

const app = express();

// 确保上传目录存在
const UPLOAD_DIR = path.join(__dirname, 'uploads');
if (!fs.existsSync(UPLOAD_DIR)) {
  fs.mkdirSync(UPLOAD_DIR, { recursive: true });
}

// 配置存储
const storage = multer.diskStorage({
  destination: (req, file, cb) => {
    cb(null, UPLOAD_DIR);
  },
  filename: (req, file, cb) => {
    // 用时间戳 + 随机数避免重名
    const ext = path.extname(file.originalname);
    const filename = `${Date.now()}-${Math.round(Math.random() * 1e9)}${ext}`;
    cb(null, filename);
  }
});

// 文件过滤
const fileFilter = (req, file, cb) => {
  const allowedTypes = ['image/jpeg', 'image/png', 'image/gif'];
  if (allowedTypes.includes(file.mimetype)) {
    cb(null, true);
  } else {
    cb(new Error('只支持 JPG/PNG/GIF 图片'), false);
  }
};

const upload = multer({
  storage,
  fileFilter,
  limits: { fileSize: 5 * 1024 * 1024 }  // 5MB 限制
});

// 单文件上传
app.post('/upload', upload.single('avatar'), (req, res) => {
  if (!req.file) {
    return res.status(400).json({ error: '请上传文件' });
  }
  res.json({
    message: '上传成功',
    file: {
      originalName: req.file.originalname,
      filename: req.file.filename,
      size: req.file.size,
      mimetype: req.file.mimetype,
      path: req.file.path
    }
  });
});

// 错误处理
app.use((err, req, res, next) => {
  if (err instanceof multer.MulterError) {
    if (err.code === 'LIMIT_FILE_SIZE') {
      return res.status(400).json({ error: '文件超过 5MB' });
    }
    return res.status(400).json({ error: err.message });
  }
  res.status(500).json({ error: err.message });
});

app.listen(3000);
```

**3. 多文件上传：**

```javascript
// 多文件，同一字段名
app.post('/upload/photos', upload.array('photos', 9), (req, res) => {
  // 最多 9 张
  res.json({
    message: `上传 ${req.files.length} 个文件`,
    files: req.files.map(f => ({
      name: f.filename,
      size: f.size
    }))
  });
});

// 多文件，不同字段名
const cpUpload = upload.fields([
  { name: 'avatar', maxCount: 1 },
  { name: 'photos', maxCount: 9 }
]);
app.post('/profile', cpUpload, (req, res) => {
  console.log(req.files.avatar);  // [{...}]
  console.log(req.files.photos);  // [{...}, ...]
  res.json({ message: '上传成功' });
});
```

**4. 内存存储（小文件）：**

```javascript
const memoryStorage = multer.memoryStorage();
const uploadMem = multer({
  storage: memoryStorage,
  limits: { fileSize: 1 * 1024 * 1024 }  // 1MB
});

app.post('/upload/mem', uploadMem.single('file'), (req, res) => {
  // req.file.buffer 是 Buffer，文件在内存中
  // 适合需要进一步处理（如缩放、上传 OSS）的场景
  console.log('文件大小:', req.file.buffer.length);

  // 写入磁盘
  const fs = require('fs');
  fs.writeFileSync('./output.png', req.file.buffer);

  res.json({ message: '上传成功' });
});
```

**5. 大文件流式上传（分片）：**

```javascript
const express = require('express');
const multer = require('multer');
const path = require('path');
const fs = require('fs');

const app = express();

// 分片上传配置
const CHUNK_DIR = path.join(__dirname, 'chunks');
const MERGE_DIR = path.join(__dirname, 'merged');
[CHUNK_DIR, MERGE_DIR].forEach(d => {
  if (!fs.existsSync(d)) fs.mkdirSync(d, { recursive: true });
});

// 上传分片
app.post('/upload/chunk', multer().single('chunk'), (req, res) => {
  const { index, filename, total } = req.body;
  const chunkDir = path.join(CHUNK_DIR, filename);

  if (!fs.existsSync(chunkDir)) {
    fs.mkdirSync(chunkDir, { recursive: true });
  }

  // 保存分片
  const chunkPath = path.join(chunkDir, `${index}`);
  fs.writeFileSync(chunkPath, req.file.buffer);

  res.json({ message: `分片 ${index}/${total} 上传成功` });
});

// 合并分片
app.post('/upload/merge', (req, res) => {
  const { filename, total } = req.body;
  const chunkDir = path.join(CHUNK_DIR, filename);
  const mergePath = path.join(MERGE_DIR, filename);

  // 按顺序合并
  const writeStream = fs.createWriteStream(mergePath);
  for (let i = 0; i < total; i++) {
    const chunkPath = path.join(chunkDir, `${i}`);
    const chunk = fs.readFileSync(chunkPath);
    writeStream.write(chunk);
  }
  writeStream.end();

  writeStream.on('finish', () => {
    // 清理分片
    fs.rmSync(chunkDir, { recursive: true });
    res.json({ message: '合并成功', path: mergePath });
  });
});

app.listen(3000);
```

#### 总结

- 文件上传用 `multer` 中间件，支持 `multipart/form-data`
- `diskStorage` 写磁盘（大文件），`memoryStorage` 存内存（小文件）
- 务必限制文件大小、类型、数量，防止恶意上传
- 多文件用 `upload.array` 或 `upload.fields`
- 大文件用分片上传：前端切片，后端接收分片，最后合并
- 生产环境推荐用云存储（OSS、S3），减轻服务器压力

---

### 第 26 讲：MongoDB 与 Mongoose

#### 概念

**MongoDB** 是最流行的 NoSQL 文档数据库，存储 JSON 风格的文档（BSON），灵活、易扩展，与 Node.js 配合天然契合。**Mongoose** 是 MongoDB 的 Node.js ODM（对象文档映射）库，提供 Schema 定义、数据验证、中间件等功能，让操作 MongoDB 更规范、更安全。

#### 原理

**MongoDB 核心概念**：
- **Database**：数据库，一个 MongoDB 实例可有多个库
- **Collection**：集合，类似 SQL 的表，存储多个文档
- **Document**：文档，类似 SQL 的行，是 JSON 对象
- **Field**：字段，文档中的键值对
- **_id**：每个文档的唯一标识，默认 ObjectId

**Mongoose 的作用**：
- **Schema**：定义文档结构（字段名、类型、默认值、验证规则）
- **Model**：由 Schema 编译而来，对应一个集合，提供 CRUD 方法
- **Document**：Model 的实例，对应一个文档
- **中间件**：在保存、删除等操作前后执行钩子
- **验证**：自动验证数据类型、必填、范围等

**连接机制**：Mongoose 底层用 MongoDB 驱动，维护连接池（默认 5 个连接），复用连接提高性能。连接字符串格式：`mongodb://user:pass@host:port/dbname`。

#### 例子

**1. 安装与连接：**

```bash
npm install mongoose
```

```javascript
const mongoose = require('mongoose');

// 连接数据库
mongoose.connect('mongodb://localhost:27017/myapp', {
  useNewUrlParser: true,
  useUnifiedTopology: true
})
.then(() => console.log('MongoDB 连接成功'))
.catch(err => console.error('连接失败:', err));

// 连接事件
mongoose.connection.on('connected', () => console.log('已连接'));
mongoose.connection.on('error', err => console.error('错误:', err));
mongoose.connection.on('disconnected', () => console.log('断开连接'));

// 优雅关闭
process.on('SIGINT', async () => {
  await mongoose.connection.close();
  console.log('数据库连接已关闭');
  process.exit(0);
});
```

**2. 定义 Schema 和 Model：**

```javascript
const mongoose = require('mongoose');
const { Schema } = mongoose;

// 定义用户 Schema
const userSchema = new Schema({
  name: {
    type: String,
    required: [true, '用户名必填'],
    trim: true,
    minlength: [2, '用户名至少 2 个字符'],
    maxlength: [50, '用户名最多 50 个字符']
  },
  email: {
    type: String,
    required: true,
    unique: true,
    lowercase: true,
    match: [/^\S+@\S+\.\S+$/, '邮箱格式不正确']
  },
  age: {
    type: Number,
    min: [0, '年龄不能为负'],
    max: [150, '年龄不合法']
  },
  role: {
    type: String,
    enum: ['user', 'admin'],
    default: 'user'
  },
  avatar: {
    type: String,
    default: ''
  },
  posts: [{
    type: Schema.Types.ObjectId,
    ref: 'Post'  // 引用 Post 模型
  }],
  createdAt: {
    type: Date,
    default: Date.now
  },
  updatedAt: {
    type: Date,
    default: Date.now
  }
});

// 实例方法
userSchema.methods.getFullName = function() {
  return this.name;
};

// 静态方法
userSchema.statics.findByEmail = function(email) {
  return this.findOne({ email });
};

// 中间件：保存前更新 updatedAt
userSchema.pre('save', function(next) {
  this.updatedAt = Date.now();
  next();
});

// 创建 Model
const User = mongoose.model('User', userSchema);
```

**3. CRUD 操作：**

```javascript
// 创建
async function createUser() {
  const user = new User({
    name: 'Alice',
    email: 'alice@example.com',
    age: 25
  });

  try {
    const saved = await user.save();
    console.log('创建成功:', saved);
  } catch (err) {
    // 验证错误
    if (err.name === 'ValidationError') {
      console.error('验证失败:', err.errors);
    } else if (err.code === 11000) {
      console.error('邮箱已存在');
    }
  }
}

// 查询
async function findUsers() {
  // 查找全部
  const all = await User.find();

  // 条件查询
  const adults = await User.find({ age: { $gte: 18 } });

  // 单个查询
  const user = await User.findById('64a1b2c3...');
  const byEmail = await User.findOne({ email: 'alice@example.com' });

  // 选择字段
  const names = await User.find().select('name email -_id');

  // 排序、分页
  const page1 = await User.find()
    .sort({ createdAt: -1 })  // 按创建时间倒序
    .skip(0)                  // 跳过
    .limit(10);               // 取 10 条

  // 计数
  const count = await User.countDocuments({ role: 'admin' });
}

// 更新
async function updateUser(id) {
  // findByIdAndUpdate
  const updated = await User.findByIdAndUpdate(
    id,
    { age: 26 },
    { new: true, runValidators: true }  // 返回新文档，运行验证
  );

  // updateOne
  await User.updateOne(
    { email: 'alice@example.com' },
    { $set: { age: 26 } }
  );
}

// 删除
async function deleteUser(id) {
  await User.findByIdAndDelete(id);
  // 或
  await User.deleteOne({ _id: id });
  // 删除多个
  await User.deleteMany({ role: 'user' });
}
```

**4. 关联查询（populate）：**

```javascript
// 定义 Post Schema
const postSchema = new Schema({
  title: String,
  content: String,
  author: { type: Schema.Types.ObjectId, ref: 'User' }
});
const Post = mongoose.model('Post', postSchema);

// 创建带关联的文档
async function createPost(userId) {
  const post = new Post({
    title: '我的第一篇文章',
    content: '内容...',
    author: userId
  });
  await post.save();

  // 把文章加到用户的 posts 数组
  await User.findByIdAndUpdate(userId, {
    $push: { posts: post._id }
  });
}

// 关联查询
async function getUserWithPosts(userId) {
  const user = await User.findById(userId)
    .populate('posts', 'title content -_id');  // 填充 posts，只取部分字段

  console.log(user.posts);  // 已是 Post 文档数组
}

// 深度 populate
async function getPostWithAuthor(postId) {
  const post = await Post.findById(postId)
    .populate({
      path: 'author',
      select: 'name email'
    });
}
```

**5. 在 Express 中使用：**

```javascript
const express = require('express');
const mongoose = require('mongoose');
const app = express();

app.use(express.json());

// 连接数据库
mongoose.connect('mongodb://localhost:27017/myapp');

// 路由
app.get('/users', async (req, res) => {
  const { page = 1, limit = 10, name } = req.query;
  const query = name ? { name: new RegExp(name, 'i') } : {};

  const users = await User.find(query)
    .sort({ createdAt: -1 })
    .skip((page - 1) * limit)
    .limit(parseInt(limit));

  const total = await User.countDocuments(query);

  res.json({
    list: users,
    pagination: { page: parseInt(page), limit: parseInt(limit), total }
  });
});

app.post('/users', async (req, res) => {
  try {
    const user = new User(req.body);
    await user.save();
    res.status(201).json(user);
  } catch (err) {
    res.status(400).json({ error: err.message });
  }
});

app.listen(3000);
```

#### 总结

- MongoDB 是文档数据库，存储 JSON 风格文档
- Mongoose 提供 Schema、验证、中间件，规范操作
- Schema 定义结构，Model 提供操作方法，Document 是实例
- CRUD：`find`/`findOne`/`findById`、`save`/`create`、`findByIdAndUpdate`、`findByIdAndDelete`
- `populate` 实现关联查询，类似 SQL 的 JOIN
- 务必加数据验证，Mongoose 内置类型、必填、范围、正则验证
- 生产环境注意索引优化、连接池配置、错误处理

---

## 第七章：进阶与实战

### 第 27 讲：Cluster 集群与多进程

#### 概念

Node.js 是单线程的，一个进程只能用一个 CPU 核。现代服务器通常有 8 核、16 核甚至更多，单进程无法充分利用硬件。**Cluster 模块**允许创建多个工作进程共享同一端口，充分利用多核 CPU，提升吞吐量和可靠性。它是 Node.js 原生提供的多进程方案，无需第三方依赖。

#### 原理

Cluster 模块采用**主从模式**（Master-Worker）：
- **主进程（Master）**：负责管理，不处理业务，监听端口，把连接分发给工作进程
- **工作进程（Worker）**：实际处理请求，多个 worker 共享同一端口

**端口共享机制**：所有 worker 监听同一端口，但实际是 master 监听，master 把 incoming 连接分发给 worker。分发策略有两种：
- **轮询（默认，除 Windows）**：master 轮流把连接分给 worker，负载均衡
- **共享套接字**：所有 worker 竞争接受连接，可能不均衡

**进程间通信**：master 和 worker 通过 IPC（进程间通信）通道通信，用 `worker.send()` 和 `process.on('message')` 收发消息。worker 之间不能直接通信，需通过 master 中转。

**容错机制**：worker 崩溃后，master 可以重新 fork 一个，保证服务可用。这是生产环境的关键特性——单进程崩溃就重启，不影响其他请求。

#### 例子

**1. 基本 Cluster：**

```javascript
const cluster = require('cluster');
const http = require('http');
const os = require('os');

if (cluster.isPrimary) {
  // 主进程
  const numCPUs = os.cpus().length;
  console.log(`主进程 PID ${process.pid}，CPU 核数 ${numCPUs}`);

  // fork 工作进程
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }

  // 监听工作进程退出
  cluster.on('exit', (worker, code, signal) => {
    console.log(`工作进程 ${worker.process.pid} 退出`);
    // 自动重启
    cluster.fork();
  });

  cluster.on('online', (worker) => {
    console.log(`工作进程 ${worker.process.pid} 已上线`);
  });

} else {
  // 工作进程
  http.createServer((req, res) => {
    res.writeHead(200);
    res.end(`Hello from worker ${process.pid}\n`);
  }).listen(3000);

  console.log(`工作进程 ${process.pid} 启动`);
}
```

**2. 进程间通信：**

```javascript
const cluster = require('cluster');

if (cluster.isPrimary) {
  const worker = cluster.fork();

  // 主进程发消息给 worker
  worker.send({ type: 'task', data: '处理订单' });

  // 接收 worker 的消息
  worker.on('message', (msg) => {
    console.log('主进程收到:', msg);
  });

} else {
  // 工作进程接收消息
  process.on('message', (msg) => {
    console.log('工作进程收到:', msg);

    // 处理后回复
    process.send({ type: 'result', data: '订单处理完成' });
  });
}
```

**3. 优雅重启（零停机）：**

```javascript
const cluster = require('cluster');
const os = require('os');

if (cluster.isPrimary) {
  const workers = [];
  const numCPUs = os.cpus().length;

  for (let i = 0; i < numCPUs; i++) {
    workers.push(cluster.fork());
  }

  // 优雅重启：收到信号后逐个重启 worker
  process.on('SIGUSR2', () => {
    console.log('开始优雅重启...');
    let i = 0;
    const restart = () => {
      if (i >= workers.length) {
        console.log('重启完成');
        return;
      }

      const oldWorker = workers[i];
      const newWorker = cluster.fork();

      newWorker.on('listening', () => {
        console.log(`新 worker ${newWorker.process.pid} 上线`);
        oldWorker.send('shutdown');  // 通知旧 worker 关闭
        workers[i] = newWorker;
        i++;
        restart();
      });
    };
    restart();
  });

  cluster.on('exit', (worker) => {
    console.log(`worker ${worker.process.pid} 退出`);
  });

} else {
  // 工作进程
  require('./server');  // 启动 HTTP 服务

  // 接收关闭信号
  process.on('message', (msg) => {
    if (msg === 'shutdown') {
      // 停止接受新连接，处理完现有请求后退出
      server.close(() => {
        process.exit(0);
      });
    }
  });
}
```

**4. 工作进程任务分发：**

```javascript
const cluster = require('cluster');

if (cluster.isPrimary) {
  const workers = [];
  for (let i = 0; i < 4; i++) {
    workers.push(cluster.fork());
  }

  // 任务队列
  const tasks = Array.from({ length: 100 }, (_, i) => i);
  let taskIndex = 0;

  // 分发任务
  workers.forEach((worker) => {
    const sendTask = () => {
      if (taskIndex >= tasks.length) {
        worker.send({ type: 'done' });
        return;
      }
      worker.send({ type: 'task', data: tasks[taskIndex++] });
    };

    worker.on('message', (msg) => {
      if (msg.type === 'ready') sendTask();
      if (msg.type === 'finished') {
        console.log(`任务 ${msg.taskId} 完成`);
        sendTask();  // 分发下一个
      }
    });

    sendTask();
  });

} else {
  process.on('message', async (msg) => {
    if (msg.type === 'task') {
      // 处理任务
      await new Promise(resolve => setTimeout(resolve, 100));
      process.send({ type: 'finished', taskId: msg.data });
    } else if (msg.type === 'done') {
      process.exit(0);
    }
  });
}
```

#### 总结

- Cluster 让 Node.js 利用多核 CPU，采用主从模式
- master 监听端口，分发给 worker，默认轮询负载均衡
- worker 崩溃后 master 自动重启，保证可用性
- 进程间通过 IPC 通信，`worker.send()` 和 `process.on('message')`
- 优雅重启：逐个重启 worker，实现零停机
- 生产环境推荐用 PM2（封装了 Cluster，更易用）

---

### 第 28 讲：Worker Threads 工作线程

#### 概念

**Worker Threads**（工作线程）是 Node.js 10 引入的功能，用于执行 CPU 密集型任务。Cluster 创建多个进程（每个进程一个 Node 实例），而 Worker Threads 在同一进程内创建多个线程，共享内存（通过 SharedArrayBuffer），开销更小。它解决了 Node.js 单线程无法处理 CPU 密集任务的问题。

#### 原理

Node.js 主线程是单线程的，CPU 密集任务（如加密、图像处理、大数据计算）会阻塞事件循环，导致所有请求卡住。Worker Threads 允许在独立线程中执行 JS 代码，不阻塞主线程。

**Worker Threads vs Cluster**：
- **Cluster**：多进程，独立内存，适合横向扩展 Web 服务
- **Worker Threads**：多线程，可共享内存，适合 CPU 密集任务

**通信机制**：
- `postMessage`：线程间发送消息（结构化克隆，类似深拷贝）
- `MessageChannel`：双向通信通道
- `SharedArrayBuffer`：共享内存，多线程直接读写（需用 Atomics 同步）

**限制**：
- worker 线程有独立的事件循环和 V8 实例
- 不能直接访问主线程的变量，必须通过消息通信
- 模块加载独立，需用 `workerData` 传递初始数据

#### 例子

**1. 基本 Worker：**

```javascript
// main.js - 主线程
const { Worker } = require('worker_threads');

// 创建 worker，执行 worker.js
const worker = new Worker('./worker.js', {
  workerData: { num: 40 }  // 传递初始数据
});

// 接收 worker 的消息
worker.on('message', (result) => {
  console.log('计算结果:', result);
});

worker.on('error', (err) => {
  console.error('worker 错误:', err);
});

worker.on('exit', (code) => {
  console.log('worker 退出，码:', code);
});

console.log('主线程继续执行其他任务...');
```

```javascript
// worker.js - 工作线程
const { parentPort, workerData } = require('worker_threads');

// CPU 密集任务：计算斐波那契
function fib(n) {
  if (n < 2) return n;
  return fib(n - 1) + fib(n - 2);
}

const result = fib(workerData.num);

// 发送结果给主线程
parentPort.postMessage(result);
```

**2. 双向通信：**

```javascript
// main.js
const { Worker } = require('worker_threads');

const worker = new Worker('./worker.js');

worker.postMessage({ task: 'process', data: [1, 2, 3, 4, 5] });

worker.on('message', (msg) => {
  if (msg.type === 'progress') {
    console.log(`进度: ${msg.value}%`);
  } else if (msg.type === 'done') {
    console.log('完成:', msg.result);
  }
});
```

```javascript
// worker.js
const { parentPort } = require('worker_threads');

parentPort.on('message', (msg) => {
  if (msg.task === 'process') {
    const data = msg.data;

    // 模拟处理，发送进度
    data.forEach((item, i) => {
      // 处理 item...
      parentPort.postMessage({
        type: 'progress',
        value: Math.round((i + 1) / data.length * 100)
      });
    });

    parentPort.postMessage({
      type: 'done',
      result: data.map(x => x * 2)
    });
  }
});
```

**3. Worker 池（复用 worker）：**

```javascript
const { Worker } = require('worker_threads');
const path = require('path');

class WorkerPool {
  constructor(size, workerPath) {
    this.workers = [];
    this.queue = [];
    this.workerPath = workerPath;

    for (let i = 0; i < size; i++) {
      this.workers.push(this.createWorker());
    }
  }

  createWorker() {
    const worker = new Worker(this.workerPath);
    worker.busy = false;
    return worker;
  }

  run(data) {
    return new Promise((resolve, reject) => {
      const task = { data, resolve, reject };
      const worker = this.workers.find(w => !w.busy);

      if (worker) {
        this.execute(worker, task);
      } else {
        this.queue.push(task);
      }
    });
  }

  execute(worker, task) {
    worker.busy = true;

    worker.once('message', (result) => {
      worker.busy = false;
      task.resolve(result);

      // 处理队列中的任务
      if (this.queue.length > 0) {
        this.execute(worker, this.queue.shift());
      }
    });

    worker.once('error', (err) => {
      worker.busy = false;
      task.reject(err);
    });

    worker.postMessage(task.data);
  }
}

// 使用
const pool = new WorkerPool(4, './worker.js');

async function main() {
  const tasks = Array.from({ length: 20 }, (_, i) => i);
  const results = await Promise.all(tasks.map(i => pool.run(i)));
  console.log('全部完成:', results);
}
```

**4. 共享内存（高性能）：**

```javascript
const { Worker } = require('worker_threads');

// 创建共享内存
const sharedBuffer = new SharedArrayBuffer(4 * 1024);  // 4KB
const sharedArray = new Int32Array(sharedBuffer);

// 初始化数据
for (let i = 0; i < sharedArray.length; i++) {
  sharedArray[i] = 0;
}

// 创建多个 worker 共享同一内存
const numWorkers = 4;
for (let i = 0; i < numWorkers; i++) {
  const worker = new Worker('./worker.js', {
    workerData: { sharedBuffer, workerId: i }
  });

  worker.on('message', () => {
    console.log(`worker ${i} 完成`);
  });
}

// worker.js
const { parentPort, workerData } = require('worker_threads');
const sharedArray = new Int32Array(workerData.sharedBuffer);

// 原子操作累加
for (let i = 0; i < 1000; i++) {
  Atomics.add(sharedArray, 0, 1);  // 线程安全的累加
}

parentPort.postMessage('done');
```

#### 总结

- Worker Threads 用于 CPU 密集任务，不阻塞主线程
- 与 Cluster 区别：Cluster 多进程扩展 Web 服务，Worker 多线程处理计算
- 通过 `postMessage` 通信，数据结构化克隆
- `SharedArrayBuffer` 共享内存，`Atomics` 保证原子操作
- 创建 worker 有开销，复用 worker 池更高效
- 适合场景：加密、压缩、图像处理、大数据计算

---

### 第 29 讲：性能优化与内存管理

#### 概念

Node.js 应用性能优化是生产环境的关键。本讲讲解常见性能瓶颈、优化策略、内存管理与泄漏排查。Node.js 单线程特性使得性能问题更敏感——一个慢操作会拖垮整个服务，因此性能优化是 Node.js 开发者的必备技能。

#### 原理

**性能瓶颈来源**：
1. **CPU 密集**：阻塞事件循环，所有请求卡住
2. **I/O 密集**：数据库慢查询、外部 API 慢响应
3. **内存**：泄漏导致内存增长，最终 OOM 崩溃
4. **网络**：带宽限制、连接数过多

**V8 内存模型**：
- **新生代（Young Generation）**：短生命周期对象，Scavenge 算法回收，快但小
- **老生代（Old Generation）**：长生命周期对象，Mark-Sweep/Mark-Compact 回收，慢但大
- 默认堆内存限制约 1.5GB（64 位），可用 `--max-old-space-size` 调整

**垃圾回收触发**：
- 新生代满了触发 Scavenge
- 老生代增长到阈值触发 Mark-Sweep
- GC 会暂停 JS 执行（Stop-The-World），影响性能

**内存泄漏常见原因**：
- 全局变量未释放
- 闭包持有大对象
- 事件监听器未移除
- 定时器未清理
- 缓存无限增长

#### 例子

**1. 性能测量：**

```javascript
const { performance, PerformanceObserver } = require('perf_hooks');

// 测量代码执行时间
const start = performance.now();
// ... 待测代码
for (let i = 0; i < 1000000; i++) {}
const end = performance.now();
console.log(`耗时: ${(end - start).toFixed(2)}ms`);

// 使用 PerformanceObserver 监听
const obs = new PerformanceObserver((list) => {
  const entries = list.getEntries();
  entries.forEach(entry => {
    console.log(`${entry.name}: ${entry.duration}ms`);
  });
});
obs.observe({ entryTypes: ['measure'], buffered: true });

// 标记和测量
performance.mark('start');
// ... 异步操作
setTimeout(() => {
  performance.mark('end');
  performance.measure('操作耗时', 'start', 'end');
}, 100);
```

**2. 内存监控：**

```javascript
// 实时内存监控
function logMemory() {
  const used = process.memoryUsage();
  console.log('内存使用:');
  console.log(`  RSS: ${(used.rss / 1024 / 1024).toFixed(2)} MB`);
  console.log(`  Heap Used: ${(used.heapUsed / 1024 / 1024).toFixed(2)} MB`);
  console.log(`  Heap Total: ${(used.heapTotal / 1024 / 1024).toFixed(2)} MB`);
  console.log(`  External: ${(used.external / 1024 / 1024).toFixed(2)} MB`);
}

// 每分钟记录一次
setInterval(logMemory, 60 * 1000);

// 内存超限告警
const MAX_HEAP = 500 * 1024 * 1024;  // 500MB
setInterval(() => {
  if (process.memoryUsage().heapUsed > MAX_HEAP) {
    console.error('内存超限！');
    // 触发告警、重启等
  }
}, 10000);
```

**3. 常见内存泄漏与修复：**

```javascript
// ❌ 泄漏1：全局变量无限增长
const cache = {};
function addToCache(key, value) {
  cache[key] = value;  // 永不清理
}

// ✅ 修复：限制大小或用 LRU
const LRU = require('lru-cache');
const cache = new LRU({ max: 1000, maxAge: 60 * 60 * 1000 });

// ❌ 泄漏2：事件监听器未移除
function setupListener() {
  emitter.on('data', handler);  // 每次调用都添加，不移除
}

// ✅ 修复：先 off 再 on，或用 once
function setupListener() {
  emitter.off('data', handler);
  emitter.on('data', handler);
}

// ❌ 泄漏3：闭包持有大对象
function createHandler() {
  const bigData = new Array(1000000).fill('data');
  return () => {
    console.log('handler');  // bigData 被闭包持有，无法回收
  };
}

// ✅ 修复：用完置空
function createHandler() {
  const bigData = new Array(1000000).fill('data');
  const result = bigData.length;
  bigData.length = 0;  // 释放引用
  return () => console.log(result);
}

// ❌ 泄漏4：定时器未清理
function startTimer() {
  setInterval(() => {
    // 引用了外部对象，定时器永远不清理
  }, 1000);
}

// ✅ 修复：保存引用，需要时清理
let timer = null;
function startTimer() {
  timer = setInterval(() => {}, 1000);
}
function stopTimer() {
  clearInterval(timer);
  timer = null;
}
```

**4. 性能优化技巧：**

```javascript
// 1. 流式处理大文件，避免内存爆炸
const fs = require('fs');
// ❌ 一次性读取
const data = fs.readFileSync('big.csv');  // 占用大量内存
// ✅ 流式处理
fs.createReadStream('big.csv')
  .pipe(transformStream)
  .pipe(fs.createWriteStream('output.csv'));

// 2. 缓存计算结果
const memoize = (fn) => {
  const cache = new Map();
  return (...args) => {
    const key = JSON.stringify(args);
    if (cache.has(key)) return cache.get(key);
    const result = fn(...args);
    cache.set(key, result);
    return result;
  };
};

const expensiveCalc = memoize((n) => {
  // 复杂计算
  return result;
});

// 3. 批量处理，减少 I/O 次数
// ❌ 逐条插入
for (const item of items) {
  await db.insert(item);  // 1000 次 I/O
}
// ✅ 批量插入
await db.insertMany(items);  // 1 次 I/O

// 4. 并发控制
const pLimit = require('p-limit');
const limit = pLimit(5);  // 最多 5 个并发
const results = await Promise.all(
  urls.map(url => limit(() => fetch(url)))
);

// 5. 使用 JSON 缓存
// 频繁序列化的大对象，缓存序列化结果
const jsonCache = new Map();
function stringify(obj) {
  const key = obj.id;
  if (!jsonCache.has(key)) {
    jsonCache.set(key, JSON.stringify(obj));
  }
  return jsonCache.get(key);
}
```

**5. 堆快照排查泄漏：**

```javascript
const v8 = require('v8');
const fs = require('fs');

// 写入堆快照
function dumpHeap(name) {
  const fileName = `./heap-${name}-${Date.now}.heapsnapshot`;
  const snapshot = v8.writeHeapSnapshot();
  fs.writeFileSync(fileName, snapshot);
  console.log(`堆快照已保存: ${fileName}`);
}

// 定时打快照
setInterval(() => dumpHeap('monitor'), 5 * 60 * 1000);

// 内存增长时打快照
let lastHeap = 0;
setInterval(() => {
  const currentHeap = process.memoryUsage().heapUsed;
  if (currentHeap > lastHeap * 1.5) {
    console.log('内存增长 50%，打快照');
    dumpHeap('growth');
  }
  lastHeap = currentHeap;
}, 60000);

// 用 Chrome DevTools 分析 .heapsnapshot 文件
// 对比两个快照，找出增长的对象
```

#### 总结

- 性能瓶颈：CPU 密集、I/O 慢、内存泄漏、网络
- V8 内存分新生代（Scavenge）和老生代（Mark-Sweep）
- 内存泄漏常见原因：全局变量、闭包、监听器、定时器、缓存
- 用 `process.memoryUsage()` 监控内存，设阈值告警
- 优化技巧：流式处理、缓存、批量、并发控制
- 堆快照（`.heapsnapshot`）用 Chrome DevTools 分析泄漏
- 生产环境务必接入 APM（如 New Relic、PM2 Plus）

---

### 第 30 讲：测试、调试与 PM2 生产部署

#### 概念

测试、调试和部署是软件开发的最后环节，也是生产环境的关键。本讲讲解 Node.js 的测试框架（Jest）、调试技巧（Chrome DevTools、VSCode）、日志管理（winston）和生产部署工具（PM2）。掌握这些，才能真正把 Node.js 应用推向生产。

#### 原理

**测试金字塔**：
- **单元测试**：测试单个函数/模块，快、多
- **集成测试**：测试模块间协作，中速、中量
- **端到端测试（E2E）**：测试完整流程，慢、少

**Jest 设计**：零配置、快照测试、mock、覆盖率报告。它用 worker 并行执行测试，速度快。

**调试原理**：Node.js 内置 Inspector 协议，通过 WebSocket 与调试器（Chrome DevTools、VSCode）通信。可以断点、查看变量、调用栈、性能分析。

**PM2 核心功能**：
- 进程管理：启动、停止、重启、监控
- 自动重启：崩溃后自动拉起
- 负载均衡：内置 Cluster
- 日志管理：合并 stdout/stderr，日志轮转
- 零停机重载：逐个重启 worker

#### 例子

**1. Jest 单元测试：**

```bash
npm install --save-dev jest
```

```javascript
// math.js - 被测模块
function add(a, b) {
  return a + b;
}

function divide(a, b) {
  if (b === 0) throw new Error('除数不能为 0');
  return a / b;
}

async function fetchData(url) {
  const res = await fetch(url);
  return res.json();
}

module.exports = { add, divide, fetchData };
```

```javascript
// math.test.js - 测试文件
const { add, divide, fetchData } = require('./math');

describe('数学函数', () => {
  test('add 正确相加', () => {
    expect(add(1, 2)).toBe(3);
    expect(add(-1, 1)).toBe(0);
  });

  test('divide 除以 0 抛错', () => {
    expect(() => divide(1, 0)).toThrow('除数不能为 0');
  });

  test('divide 正常除法', () => {
    expect(divide(6, 2)).toBe(3);
  });
});

// 异步测试
describe('fetchData', () => {
  test('获取数据', async () => {
    const data = await fetchData('https://api.example.com');
    expect(data).toBeDefined();
  });
});

// Mock
jest.mock('./math');
const { fetchData } = require('./math');

test('mock fetchData', async () => {
  fetchData.mockResolvedValue({ id: 1, name: 'Alice' });
  const data = await fetchData('any');
  expect(data.id).toBe(1);
});
```

**2. Express API 测试：**

```javascript
// app.js
const express = require('express');
const app = express();
app.use(express.json());

let users = [];
app.get('/users', (req, res) => res.json(users));
app.post('/users', (req, res) => {
  const user = { id: Date.now(), ...req.body };
  users.push(user);
  res.status(201).json(user);
});

module.exports = app;  // 导出 app，便于测试
```

```javascript
// app.test.js
const request = require('supertest');
const app = require('./app');

describe('用户 API', () => {
  test('GET /users 返回空数组', async () => {
    const res = await request(app).get('/users');
    expect(res.status).toBe(200);
    expect(res.body).toEqual([]);
  });

  test('POST /users 创建用户', async () => {
    const res = await request(app)
      .post('/users')
      .send({ name: 'Alice' });

    expect(res.status).toBe(201);
    expect(res.body.name).toBe('Alice');
    expect(res.body.id).toBeDefined();
  });
});
```

**3. 调试：**

```bash
# 方式1：Chrome DevTools
node --inspect index.js
# 或 --inspect-brk 在第一行断住
node --inspect-brk index.js
# 然后打开 chrome://inspect，点击 inspect

# 方式2：VSCode launch.json
```

```json
// .vscode/launch.json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "调试当前文件",
      "program": "${file}",
      "skipFiles": ["<node_internals>/**"]
    },
    {
      "type": "node",
      "request": "attach",
      "name": "附加到进程",
      "port": 9229
    },
    {
      "type": "node",
      "request": "launch",
      "name": "调试 Jest",
      "program": "${workspaceFolder}/node_modules/.bin/jest",
      "args": ["--runInBand"],
      "console": "integratedTerminal"
    }
  ]
}
```

**4. 日志管理（winston）：**

```javascript
const winston = require('winston');

const logger = winston.createLogger({
  level: process.env.NODE_ENV === 'production' ? 'info' : 'debug',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  transports: [
    // 控制台
    new winston.transports.Console({
      format: winston.format.combine(
        winston.format.colorize(),
        winston.format.simple()
      )
    }),
    // 文件（按级别分）
    new winston.transports.File({
      filename: 'logs/error.log',
      level: 'error'
    }),
    new winston.transports.File({
      filename: 'logs/combined.log'
    })
  ]
});

// 使用
logger.info('服务启动');
logger.error('数据库连接失败', { error: err.message });
logger.warn('内存使用率高', { usage: '80%' });
logger.debug('调试信息', { data });

// 在 Express 中间件中使用
app.use((req, res, next) => {
  const start = Date.now();
  res.on('finish', () => {
    logger.info('请求完成', {
      method: req.method,
      url: req.url,
      status: res.statusCode,
      duration: Date.now() - start
    });
  });
  next();
});
```

**5. PM2 生产部署：**

```bash
# 安装
npm install -g pm2

# 启动应用
pm2 start app.js --name "myapp"

# Cluster 模式（充分利用多核）
pm2 start app.js -i max  # max 表示 CPU 核数
pm2 start app.js -i 4    # 4 个进程

# 常用命令
pm2 list              # 查看进程
pm2 show myapp        # 查看详情
pm2 logs myapp        # 查看日志
pm2 monit             # 监控面板
pm2 restart myapp     # 重启
pm2 reload myapp      # 零停机重载
pm2 stop myapp        # 停止
pm2 delete myapp      # 删除
```

```javascript
// ecosystem.config.js - PM2 配置文件
module.exports = {
  apps: [{
    name: 'myapp',
    script: './app.js',
    instances: 'max',        // CPU 核数
    exec_mode: 'cluster',
    max_memory_restart: '1G', // 内存超 1G 重启
    env: {
      NODE_ENV: 'production',
      PORT: 3000
    },
    env_development: {
      NODE_ENV: 'development'
    },
    error_file: './logs/error.log',
    out_file: './logs/out.log',
    log_date_format: 'YYYY-MM-DD HH:mm:ss',
    merge_logs: true,
    autorestart: true,
    watch: false,
    ignore_watch: ['node_modules', 'logs']
  }]
};
```

```bash
# 用配置文件启动
pm2 start ecosystem.config.js

# 设置开机自启
pm2 startup
pm2 save

# 日志轮转（需安装 pm2-logrotate）
pm2 install pm2-logrotate
pm2 set pm2-logrotate:max_size 10M
pm2 set pm2-logrotate:retain 30
```

#### 总结

- 测试金字塔：单元测试多、集成测试中、E2E 少
- Jest 是 Node.js 最流行的测试框架，零配置、支持 mock
- Express 测试用 supertest，模拟 HTTP 请求
- 调试用 `--inspect` 配合 Chrome DevTools 或 VSCode
- 生产日志用 winston，分级（error/warn/info/debug）+ 文件轮转
- PM2 是生产部署标配：进程管理、Cluster、自动重启、零停机重载
- 务必配置 `ecosystem.config.js`，用 `pm2 startup` 开机自启
- 生产环境监控：PM2 Plus、New Relic、Datadog 等

---

## 课程总结

恭喜你完成了 Node.js 系统教程的全部 30 讲！让我们回顾学习路径：

**第一章 基础入门**：理解 Node.js 的本质（V8 + libuv）、环境搭建、全局对象和运行机制。

**第二章 模块系统**：掌握 CommonJS 和 ES Modules 两种模块规范，理解 require 的查找流程，熟练使用 npm 管理依赖。

**第三章 核心模块**：fs 文件操作、path 路径处理、os/process 系统信息、events 事件驱动、stream 流式处理——这些是 Node.js 的基石。

**第四章 异步编程**：从回调到 Promise 到 async/await，掌握事件循环，理解微任务与宏任务，写出高性能异步代码。

**第五章 Web 开发**：从原生 http 模块到 Express 框架，掌握路由、中间件、RESTful API 设计，构建生产级 Web 服务。

**第六章 数据存储**：Buffer 处理二进制、文件上传、MongoDB + Mongoose 操作数据库。

**第七章 进阶实战**：Cluster 多进程、Worker Threads 多线程、性能优化、内存管理、测试调试、PM2 部署。

**下一步学习建议**：
1. **TypeScript**：为 Node.js 项目增加类型安全
2. **NestJS**：企业级框架，类似 Spring Boot
3. **GraphQL**：API 查询语言，替代 REST
4. **微服务**：消息队列（RabbitMQ、Kafka）、服务发现
5. **容器化**：Docker + Kubernetes 部署
6. **WebSocket**：实时通信（Socket.io）

Node.js 生态丰富，持续学习实践，你会成为一名优秀的后端工程师。祝你编码愉快！
