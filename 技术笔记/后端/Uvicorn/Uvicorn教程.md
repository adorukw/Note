# Uvicorn 教程：从入门到生产实战

> 本教程以教科书形式系统讲解 Uvicorn —— Python 生态中最流行的 ASGI 服务器。从基础概念出发，逐步深入到协议规范、配置运行、框架集成、异步并发、生产部署、性能调优与最佳实践，共 35 讲，8 章。

---

## 课程总览

- **预计讲数**：35 讲
- **章节划分**：8 章
- **学习目标**：
  1. 理解 Uvicorn 在 Python Web 生态中的定位与作用
  2. 掌握 ASGI 协议规范及其与 WSGI 的差异
  3. 熟练配置和运行 Uvicorn 服务器
  4. 能够将 Uvicorn 与 FastAPI、Starlette、Django、Flask 等框架集成
  5. 理解异步事件循环、WebSocket、长连接等并发机制
  6. 掌握生产环境部署方案（Gunicorn、Nginx、Docker、systemd、K8s）
  7. 具备性能调优与故障排查能力
  8. 建立完整的生产环境最佳实践体系

- **渐进结构**：基础 → 核心 → 进阶 → 高级/实战

---

## 详细章节目录

### 第1章 Uvicorn 基础入门
- 第1讲 什么是 Uvicorn：Web 服务器与 Python 异步生态
- 第2讲 WSGI vs ASGI：异步时代的演进
- 第3讲 环境安装与第一个 Hello World
- 第4讲 Uvicorn 命令行与 Python API

### 第2章 ASGI 协议深入
- 第5讲 ASGI 协议规范详解
- 第6讲 Scope、Receive、Send 三要素
- 第7讲 HTTP 请求/响应生命周期
- 第8讲 Lifespan 事件机制

### 第3章 配置与运行
- 第9讲 启动参数详解
- 第10讲 Worker 进程与多核利用
- 第11讲 日志系统配置
- 第12讲 SSL/HTTPS 配置
- 第13讲 热重载与开发工作流

### 第4章 与框架集成
- 第14讲 Uvicorn + FastAPI 实战
- 第15讲 Uvicorn + Starlette 集成
- 第16讲 Uvicorn + Django（ASGI 模式）
- 第17讲 Uvicorn + Flask（asgiref 桥接）

### 第5章 异步与并发
- 第18讲 uvloop 与事件循环
- 第19讲 httptools 与 HTTP 解析
- 第20讲 WebSocket 支持
- 第21讲 长连接与连接管理

### 第6章 部署实战
- 第22讲 Gunicorn + Uvicorn 部署模式
- 第23讲 Nginx 反向代理配置
- 第24讲 Docker 容器化部署
- 第25讲 systemd 服务部署
- 第26讲 Kubernetes 部署要点

### 第7章 性能调优
- 第27讲 性能基准测试
- 第28讲 Worker 数量调优
- 第29讲 内存与连接数优化
- 第30讲 常见性能瓶颈分析

### 第8章 高级特性与生产实践
- 第31讲 中间件机制
- 第32讲 信号处理与优雅关闭
- 第33讲 监控与可观测性
- 第34讲 安全加固
- 第35讲 生产环境 Checklist 与故障排查

---

# 第1章 Uvicorn 基础入门

本章将带你走进 Uvicorn 的世界。我们从最基本的概念出发，理解 Web 服务器在 Python 异步生态中的定位，对比 WSGI 与 ASGI 的差异，完成第一个 Hello World 程序，并掌握 Uvicorn 的两种主要使用方式：命令行与 Python API。

---

## 第1讲 什么是 Uvicorn：Web 服务器与 Python 异步生态

### 概念

**Uvicorn** 是一个基于 Python 的 **ASGI（Asynchronous Server Gateway Interface）** Web 服务器实现。它使用 `uvloop`（基于 Cython 的 libuv 绑定）作为事件循环，使用 `httptools`（基于 Node.js 的 HTTP 解析器）作为 HTTP 协议解析器，因此性能极为出色。Uvicorn 是 FastAPI、Starlette 等现代异步 Web 框架的默认推荐服务器。

简单来说：**Uvicorn = ASGI 协议实现 + uvloop 事件循环 + httptools HTTP 解析器**。

### 原理

要理解 Uvicorn，需要先理解 Python Web 生态的三个角色：

1. **Web 框架**（如 FastAPI、Django、Flask）：负责业务逻辑、路由、请求处理
2. **Web 服务器**（如 Uvicorn、Gunicorn）：负责网络 I/O、协议解析、连接管理
3. **网关协议**（如 WSGI、ASGI）：定义框架与服务器之间的接口规范

Uvicorn 的核心职责包括：
- 监听 TCP 端口，接受客户端连接
- 解析 HTTP 协议（请求行、头部、正文）
- 将请求转换为 ASGI 规范的 `scope` 字典
- 调用 ASGI 应用（你的框架代码）
- 将应用的响应数据写回客户端 socket
- 管理连接生命周期、超时、错误处理

Uvicorn 选择 `uvloop` 而非标准库 `asyncio` 的原因：`uvloop` 基于 C 实现的 libuv（Node.js 的底层库），性能比纯 Python 的 asyncio 高 2-4 倍。这使得 Uvicorn 在性能基准测试中常常与 Node.js、Go 语言的服务器处于同一梯队。

### 例子

最简单的 Uvicorn 应用只需几行代码：

```python
# app.py
async def app(scope, receive, send):
    """一个最简的 ASGI 应用"""
    assert scope["type"] == "http"
    await send({
        "type": "http.response.start",
        "status": 200,
        "headers": [
            [b"content-type", b"text/plain"],
        ],
    })
    await send({
        "type": "http.response.body",
        "body": b"Hello, Uvicorn!",
    })
```

启动服务器：

```bash
# 命令行方式
uvicorn app:app --host 0.0.0.0 --port 8000

# 访问测试
curl http://localhost:8000
# 输出: Hello, Uvicorn!
```

Uvicorn 在 Python Web 生态中的位置示意：

```
┌─────────────────────────────────────────┐
│           客户端 (浏览器/curl)            │
└──────────────────┬──────────────────────┘
                   │ HTTP/HTTPS
                   ▼
┌─────────────────────────────────────────┐
│            Uvicorn (ASGI 服务器)          │
│  ┌─────────────┐  ┌──────────────────┐  │
│  │   uvloop    │  │    httptools     │  │
│  │ (事件循环)   │  │  (HTTP 解析器)    │  │
│  └─────────────┘  └──────────────────┘  │
└──────────────────┬──────────────────────┘
                   │ ASGI 协议
                   ▼
┌─────────────────────────────────────────┐
│      ASGI 应用 (FastAPI/Starlette/...)   │
└─────────────────────────────────────────┘
```

### 总结

- Uvicorn 是 Python 生态中性能最强的 ASGI 服务器之一
- 它由三大核心组件构成：ASGI 协议实现、uvloop 事件循环、httptools HTTP 解析器
- Uvicorn 不负责业务逻辑，只负责网络 I/O 和协议解析
- 它是 FastAPI、Starlette 等现代异步框架的推荐服务器
- 理解 Uvicorn 需要先理解"框架—服务器—协议"三层架构

---

## 第2讲 WSGI vs ASGI：异步时代的演进

### 概念

**WSGI（Web Server Gateway Interface）** 是 Python 早期的 Web 服务器网关协议（PEP 3333），定义于 2003 年，采用同步模型。**ASGI（Asynchronous Server Gateway Interface）** 是 WSGI 的异步继承者，由 Django Channels 团队发起，支持异步、WebSocket、HTTP/2 等现代特性。

### 原理

**WSGI 的同步模型**：

WSGI 应用是一个同步的可调用对象，接收 `environ`（环境字典）和 `start_response`（响应启动函数），返回一个可迭代的响应体。每个请求占用一个线程/进程，I/O 等待期间线程被阻塞。

```python
# WSGI 应用签名
def app(environ, start_response):
    start_response("200 OK", [("Content-Type", "text/plain")])
    return [b"Hello, WSGI!"]
```

WSGI 的局限：
1. **同步阻塞**：I/O 等待期间占用线程，无法高效处理大量长连接
2. **不支持 WebSocket**：协议本身是请求-响应模型，无法处理双向通信
3. **不支持 HTTP/2**：多路复用等特性无法表达
4. **无法轻松集成异步库**：如 `aiohttp`、`aioredis`、`asyncpg`

**ASGI 的异步模型**：

ASGI 应用是一个异步可调用对象，接收 `scope`（连接信息）、`receive`（接收请求的异步函数）、`send`（发送响应的异步函数）。基于事件循环，单线程即可处理数千并发连接。

```python
# ASGI 应用签名
async def app(scope, receive, send):
    await send({"type": "http.response.start", "status": 200, ...})
    await send({"type": "http.response.body", "body": b"Hello, ASGI!"})
```

ASGI 的优势：
1. **异步非阻塞**：I/O 等待期间让出事件循环，单线程处理高并发
2. **支持 WebSocket**：通过 `scope["type"] == "websocket"` 区分协议
3. **支持 HTTP/2**：可扩展多路复用
4. **统一协议**：HTTP、WebSocket、Lifespan 共用一套接口

**对比表**：

| 特性 | WSGI | ASGI |
|------|------|------|
| 同步/异步 | 同步 | 异步 |
| 并发模型 | 多线程/多进程 | 事件循环 |
| WebSocket | 不支持 | 原生支持 |
| HTTP/2 | 不支持 | 可扩展 |
| 服务器示例 | Gunicorn、uWSGI | Uvicorn、Daphne、Hypercorn |
| 框架示例 | Flask、Django（传统） | FastAPI、Starlette、Django（ASGI） |

### 例子

同一个"返回当前时间"的需求，两种实现对比：

```python
# === WSGI 版本（同步） ===
import time

def wsgi_app(environ, start_response):
    start_response("200 OK", [("Content-Type", "text/plain")])
    return [f"Time: {time.time()}".encode()]


# === ASGI 版本（异步） ===
import time

async def asgi_app(scope, receive, send):
    await send({
        "type": "http.response.start",
        "status": 200,
        "headers": [[b"content-type", b"text/plain"]],
    })
    await send({
        "type": "http.response.body",
        "body": f"Time: {time.time()}".encode(),
    })
```

异步优势演示 —— 同时等待多个 I/O：

```python
import asyncio
import aiohttp

async def fetch_url(session, url):
    async with session.get(url) as resp:
        return await resp.text()

async def app(scope, receive, send):
    # 同时并发请求 3 个 URL，总耗时 ≈ 最慢的那个
    async with aiohttp.ClientSession() as session:
        urls = ["http://example.com"] * 3
        results = await asyncio.gather(*[fetch_url(session, u) for u in urls])

    body = f"Fetched {len(results)} pages".encode()
    await send({
        "type": "http.response.start",
        "status": 200,
        "headers": [[b"content-type", b"text/plain"]],
    })
    await send({"type": "http.response.body", "body": body})
```

如果是 WSGI，同样的需求需要 3 个线程，且总耗时 = 3 个请求耗时之和。

### 总结

- WSGI 是同步协议，适合传统请求-响应场景；ASGI 是异步协议，适合高并发、WebSocket 等现代场景
- ASGI 是 WSGI 的超集：WSGI 应用可通过 `asgiref` 适配器在 ASGI 服务器上运行，反之不行
- Uvicorn 是 ASGI 服务器，因此能同时服务 FastAPI（原生 ASGI）和 Flask（通过适配）
- 异步的核心价值：单线程内通过事件循环处理大量 I/O 并发，避免线程切换开销
- 选择 ASGI 不等于必须用异步：同步代码也能在 ASGI 服务器上运行，只是无法享受并发优势

---

## 第3讲 环境安装与第一个 Hello World

### 概念

本讲完成 Uvicorn 的环境搭建，并编写第一个可运行的 Hello World 应用。我们将介绍最小依赖安装、可选性能依赖安装，以及虚拟环境的最佳实践。

### 原理

Uvicorn 在 PyPI 上发布为 `uvicorn` 包。它采用**可选依赖**设计：
- **核心依赖**：仅包含 `asgiref`（ASGI 规范参考实现），保证最小安装体积
- **可选依赖**：`uvloop`（高性能事件循环）、`httptools`（高性能 HTTP 解析器）、`websockets`（WebSocket 协议实现）、`watchfiles`（热重载文件监听）

这种设计的好处：核心包轻量，用户按需安装性能组件。但生产环境强烈建议安装全部可选依赖以获得最佳性能。

Python 版本要求：Uvicorn 0.20+ 需要 Python 3.7+，0.30+ 需要 Python 3.8+。推荐使用 Python 3.10+ 以获得最佳异步语法支持。

### 例子

**步骤 1：创建虚拟环境**

```bash
# 使用 venv（标准库）
python -m venv .venv
source .venv/bin/activate  # Linux/macOS
# .venv\Scripts\activate   # Windows

# 或使用 uv（更快的现代工具）
pip install uv
uv venv
source .venv/bin/activate
```

**步骤 2：安装 Uvicorn**

```bash
# 最小安装（不推荐生产使用）
pip install uvicorn

# 标准安装（包含常用性能依赖，推荐）
pip install "uvicorn[standard]"

# 完整安装（包含所有可选依赖）
pip install "uvicorn[standard,all]"
```

`uvicorn[standard]` 实际安装：
- `uvicorn` 核心
- `uvloop>=0.14.0`（Linux/macOS，Windows 不支持）
- `httptools>=0.5.0`
- `websockets>=10.4`
- `python-dotenv>=0.13`
- `PyYAML>=5.4.1`
- `watchfiles>=0.13`
- `watchgod`（旧版兼容）

**步骤 3：验证安装**

```bash
uvicorn --version
# 输出: uvicorn 0.30.x
```

**步骤 4：编写第一个 Hello World**

```python
# hello.py
async def app(scope, receive, send):
    """最简 ASGI 应用"""
    assert scope["type"] == "http"

    # 根据路径返回不同内容
    path = scope["path"].decode() if isinstance(scope["path"], bytes) else scope["path"]

    if path == "/":
        message = b"Hello, World!"
        status = 200
    elif path == "/api/time":
        import time
        message = f'{{"time": {time.time()}}}'.encode()
        status = 200
    else:
        message = b"Not Found"
        status = 404

    await send({
        "type": "http.response.start",
        "status": status,
        "headers": [
            [b"content-type", b"application/json" if path == "/api/time" else b"text/plain"],
        ],
    })
    await send({
        "type": "http.response.body",
        "body": message,
    })
```

**步骤 5：启动服务器**

```bash
uvicorn hello:app --reload --port 8000
```

输出：
```
INFO:     Will watch for changes in these directories: ['/home/user/project']
INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
INFO:     Started reloader process [12345] using WatchFiles
INFO:     Started server process [12346]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
```

**步骤 6：测试访问**

```bash
curl http://localhost:8000/
# Hello, World!

curl http://localhost:8000/api/time
# {"time": 1710000000.123}

curl http://localhost:8000/unknown
# Not Found
```

### 总结

- 推荐使用 `pip install "uvicorn[standard]"` 一次性安装所有性能依赖
- 虚拟环境是 Python 项目的标配，避免污染系统环境
- Uvicorn 的最小应用只需一个异步函数，接收 `scope`、`receive`、`send` 三参数
- `--reload` 参数开启热重载，修改代码后自动重启，适合开发环境
- 生产环境不要使用 `--reload`，会有性能损耗

---

## 第4讲 Uvicorn 命令行与 Python API

### 概念

Uvicorn 提供两种使用方式：
1. **命令行接口（CLI）**：通过 `uvicorn` 命令启动，适合快速测试和简单部署
2. **Python API**：通过 `uvicorn.run()` 或 `uvicorn.Server` 在代码中启动，适合需要编程式控制的场景

### 原理

**命令行方式**：Uvicorn 使用 Python 标准库 `argparse` 解析命令行参数，内部最终调用 `uvicorn.run()`。优点是简单直接，缺点是参数较多时命令冗长。

**Python API 方式**：直接在代码中调用 `uvicorn.run(app, **kwargs)`，所有参数以关键字形式传递。优点是可读性好、可结合配置文件、可在 `if __name__ == "__main__":` 中保护启动逻辑。

两种方式底层都创建 `uvicorn.config.Config` 对象，再实例化 `uvicorn.Server` 并调用 `server.run()`。理解这一点对后续高级用法（如编程式控制服务器生命周期）至关重要。

### 例子

**方式 1：命令行启动**

```bash
# 基本启动
uvicorn main:app

# 完整参数示例
uvicorn main:app \
    --host 0.0.0.0 \
    --port 8000 \
    --workers 4 \
    --reload \
    --reload-dir ./app \
    --log-level info \
    --access-log \
    --ssl-keyfile ./key.pem \
    --ssl-certfile ./cert.pem \
    --loop uvloop \
    --http httptools \
    --ws websockets \
    --lifespan on
```

常用命令行参数速查：

| 参数 | 简写 | 说明 | 默认值 |
|------|------|------|--------|
| `--host` | `-H` | 监听地址 | 127.0.0.1 |
| `--port` | `-p` | 监听端口 | 8000 |
| `--workers` | `-w` | Worker 进程数 | 1 |
| `--reload` | - | 开启热重载 | False |
| `--reload-dir` | - | 监听目录 | 当前目录 |
| `--log-level` | - | 日志级别 | info |
| `--access-log` | - | 启用访问日志 | True |
| `--loop` | - | 事件循环实现 | auto |
| `--http` | - | HTTP 解析器 | auto |
| `--ws` | - | WebSocket 实现 | auto |
| `--lifespan` | - | Lifespan 处理 | auto |
| `--ssl-keyfile` | - | SSL 私钥路径 | - |
| `--ssl-certfile` | - | SSL 证书路径 | - |
| `--app-dir` | - | 应用搜索路径 | - |
| `--factory` | - | 应用为工厂函数 | False |

**方式 2：Python API 启动**

```python
# run_simple.py
import uvicorn

async def app(scope, receive, send):
    await send({
        "type": "http.response.start",
        "status": 200,
        "headers": [[b"content-type", b"text/plain"]],
    })
    await send({"type": "http.response.body", "body": b"Hello from API!"})

if __name__ == "__main__":
    uvicorn.run(
        "run_simple:app",  # 注意：传字符串而非对象，以支持 reload 和 workers
        host="0.0.0.0",
        port=8000,
        reload=True,        # 开发模式
        log_level="info",
    )
```

> **关键点**：`uvicorn.run()` 第一个参数若传字符串（如 `"main:app"`），则支持 `reload=True` 和多 worker；若直接传应用对象（如 `app`），则不支持 reload 和多 worker（因为对象无法跨进程序列化）。

**方式 3：使用 Server 类进行精细控制**

```python
# run_server.py
import asyncio
import uvicorn

async def app(scope, receive, send):
    await send({"type": "http.response.start", "status": 200, "headers": []})
    await send({"type": "http.response.body", "body": b"Hi"})

async def main():
    config = uvicorn.Config(
        app=app,
        host="0.0.0.0",
        port=8000,
        log_level="info",
    )
    server = uvicorn.Server(config)

    # 在已有事件循环中运行（不阻塞当前协程）
    await server.serve()

    # 优雅关闭
    # server.should_exit = True  # 触发优雅退出

if __name__ == "__main__":
    asyncio.run(main())
```

这种方式常用于：
- 将 Uvicorn 嵌入到已有异步应用中（如桌面 GUI、Jupyter Notebook）
- 编程式控制服务器启停
- 与其他异步任务并行运行

**方式 4：使用应用工厂**

```python
# factory_app.py
def create_app():
    async def app(scope, receive, send):
        await send({"type": "http.response.start", "status": 200, "headers": []})
        await send({"type": "http.response.body", "body": b"Factory app"})
    return app

# 启动时加 --factory 标志
# uvicorn factory_app:create_app --factory
```

工厂模式适合需要根据环境变量动态创建应用的场景。

### 总结

- 命令行适合快速测试和简单部署，Python API 适合需要编程控制的场景
- `uvicorn.run("module:app", reload=True)` 传字符串才能支持热重载和多 worker
- `uvicorn.Server` 类提供最细粒度控制，可嵌入到已有事件循环中
- 应用工厂模式（`--factory`）适合动态配置场景
- 生产环境推荐使用 Python API + 配置文件，便于版本控制和环境隔离

---

# 第2章 ASGI 协议深入

要真正掌握 Uvicorn，必须深入理解 ASGI 协议本身。本章将剖析 ASGI 协议规范，详解 Scope、Receive、Send 三要素，追踪 HTTP 请求/响应的完整生命周期，并讲解 Lifespan 事件机制。这些知识是编写高级 ASGI 应用、调试问题、理解框架底层行为的基础。

---

## 第5讲 ASGI 协议规范详解

### 概念

**ASGI（Asynchronous Server Gateway Interface）** 是一套 Python 异步 Web 服务器与应用之间的接口规范，由 [ASGI 规范文档](https://asgi.readthedocs.io/) 定义。它定义了服务器如何将连接信息、请求数据传递给应用，以及应用如何将响应数据回传给服务器。ASGI 是协议规范，Uvicorn 是其实现之一。

### 原理

ASGI 规范的核心设计原则：

1. **异步优先**：所有接口都是 `async`/`await` 形式
2. **协议无关**：同一套接口支持 HTTP、WebSocket、Lifespan 等多种协议类型
3. **事件驱动**：通过消息（message）传递数据，而非一次性传完整请求/响应
4. **双向通信**：`receive` 接收客户端消息，`send` 发送响应消息

**ASGI 应用的标准签名**：

```python
async def application(scope, receive, send):
    ...
```

三个参数的含义：
- `scope`：连接的元信息字典（一次连接只创建一次）
- `receive`：异步函数，调用后等待并返回下一条来自客户端的消息
- `send`：异步函数，调用后向客户端发送一条消息

**协议类型（scope["type"]）**：

| 类型 | 说明 | 触发场景 |
|------|------|----------|
| `"http"` | HTTP 请求 | 普通浏览器访问、API 调用 |
| `"websocket"` | WebSocket 连接 | 双向实时通信 |
| `"lifespan"` | 生命周期事件 | 服务器启动/关闭时 |

**消息（Message）结构**：

每条消息是一个字典，必须包含 `type` 字段表示消息类型。例如：

```python
# HTTP 响应起始消息
{
    "type": "http.response.start",
    "status": 200,
    "headers": [(b"content-type", b"text/plain")],
}

# HTTP 响应体消息
{
    "type": "http.response.body",
    "body": b"Hello",
    "more_body": False,  # 是否还有后续 body 消息
}
```

**ASGI 版本**：当前主流版本是 3.0，在 `scope["asgi"]` 中标识：
```python
scope["asgi"] = {"version": "3.0", "spec_version": "2.3"}
```

### 例子

一个完整的 ASGI 应用，展示协议规范的所有要素：

```python
# asgi_spec_demo.py
async def app(scope, receive, send):
    # 1. 根据协议类型分发
    if scope["type"] == "http":
        await handle_http(scope, receive, send)
    elif scope["type"] == "websocket":
        await handle_websocket(scope, receive, send)
    elif scope["type"] == "lifespan":
        await handle_lifespan(scope, receive, send)
    else:
        raise ValueError(f"Unknown scope type: {scope['type']}")


async def handle_http(scope, receive, send):
    # 2. 接收完整的 HTTP 请求（可能有多条 body 消息）
    body = b""
    more_body = True
    while more_body:
        message = await receive()
        body += message.get("body", b"")
        more_body = message.get("more_body", False)

    # 3. 构造响应
    response_body = f"Received {len(body)} bytes on {scope['path']}".encode()

    # 4. 发送响应起始（必须先发 start，再发 body）
    await send({
        "type": "http.response.start",
        "status": 200,
        "headers": [
            (b"content-type", b"text/plain"),
            (b"content-length", str(len(response_body)).encode()),
        ],
    })

    # 5. 发送响应体（可以分多次发送，实现流式响应）
    await send({
        "type": "http.response.body",
        "body": response_body,
    })


async def handle_websocket(scope, receive, send):
    # 接受 WebSocket 连接
    await send({"type": "websocket.accept"})
    # 循环接收消息
    while True:
        message = await receive()
        if message["type"] == "websocket.disconnect":
            break
        # 回显消息
        await send({
            "type": "websocket.send",
            "text": f"Echo: {message.get('text', '')}",
        })


async def handle_lifespan(scope, receive, send):
    while True:
        message = await receive()
        if message["type"] == "lifespan.startup":
            print("Application starting...")
            await send({"type": "lifespan.startup.complete"})
        elif message["type"] == "lifespan.shutdown":
            print("Application shutting down...")
            await send({"type": "lifespan.shutdown.complete"})
            break
```

启动并测试：

```bash
uvicorn asgi_spec_demo:app --port 8000

# 测试 HTTP
curl -X POST -d "hello" http://localhost:8000/test
# 输出: Received 5 bytes on /test

# 测试 WebSocket（使用 websocat 工具）
websocat ws://localhost:8000/ws
> hello
< Echo: hello
```

### 总结

- ASGI 是协议规范，Uvicorn 是其高性能实现
- ASGI 应用 = 异步函数 `(scope, receive, send) -> None`
- `scope["type"]` 决定协议类型：`http`、`websocket`、`lifespan`
- 消息通过 `receive`/`send` 异步传递，支持流式请求和响应
- 一个 HTTP 响应必须先发 `http.response.start`，再发至少一条 `http.response.body`
- 理解 ASGI 规范是阅读 FastAPI、Starlette 源码的基础

---

## 第6讲 Scope、Receive、Send 三要素

### 概念

ASGI 应用的三个参数 `scope`、`receive`、`send` 是协议的核心。**Scope** 是连接的元信息字典，**Receive** 是接收客户端消息的异步函数，**Send** 是发送响应消息的异步函数。理解这三个要素的字段和行为是编写 ASGI 应用的基础。

### 原理

**Scope 字典详解（HTTP 类型）**：

```python
{
    "type": "http",                    # 协议类型
    "asgi": {                          # ASGI 版本信息
        "version": "3.0",
        "spec_version": "2.3"
    },
    "http_version": "1.1",             # HTTP 协议版本（1.0/1.1/2）
    "method": "GET",                   # HTTP 方法
    "scheme": "http",                  # 协议方案（http/https）
    "path": "/api/users",              # 路径（已 URL 解码）
    "raw_path": b"/api/users",         # 原始路径（未解码）
    "query_string": b"page=1&size=10", # 查询字符串（不含 ?）
    "root_path": "",                   # 应用挂载的根路径
    "headers": [                       # 请求头（小写，字节列表）
        (b"host", b"localhost:8000"),
        (b"user-agent", b"curl/7.81.0"),
        (b"content-type", b"application/json"),
    ],
    "client": ["127.0.0.1", 54321],    # 客户端地址 [host, port]
    "server": ["127.0.0.1", 8000],     # 服务器地址 [host, port]
    "extensions": {                    # 服务器支持的扩展
        "http.response.template": {},
    },
    "state": {},                       # 跨中间件共享状态（ASGI 3.0+）
}
```

**Scope 字典详解（WebSocket 类型）**：

```python
{
    "type": "websocket",
    "asgi": {"version": "3.0", "spec_version": "2.3"},
    "http_version": "1.1",
    "scheme": "ws",                    # ws 或 wss
    "path": "/ws/chat",
    "raw_path": b"/ws/chat",
    "query_string": b"room=general",
    "headers": [...],
    "client": ["127.0.0.1", 54321],
    "server": ["127.0.0.1", 8000],
    "subprotocols": ["chat-v1", "chat-v2"],  # 客户端请求的子协议
    "state": {},
}
```

**Receive 函数行为**：
- 返回一个 `dict`，表示一条来自客户端的消息
- HTTP 请求：第一条是 `http.request`，可能有多条（流式上传）
- WebSocket：`websocket.connect` → `websocket.receive`（多次）→ `websocket.disconnect`
- 调用 `receive()` 会阻塞当前协程，直到有消息到达

**Send 函数行为**：
- 接收一个 `dict`，向客户端发送一条消息
- HTTP 响应：必须先发 `http.response.start`，再发 `http.response.body`（可多次）
- WebSocket：`websocket.accept`/`websocket.close`/`websocket.send`

### 例子

**示例 1：解析 Scope 中的请求信息**

```python
# scope_parser.py
async def app(scope, receive, send):
    if scope["type"] != "http":
        return

    # 解析请求信息
    method = scope["method"]
    path = scope["path"]
    query_string = scope["query_string"].decode()
    http_version = scope["http_version"]

    # 解析 headers（注意：headers 是字节列表，键名小写）
    headers = {k.decode(): v.decode() for k, v in scope["headers"]}

    # 解析查询参数
    from urllib.parse import parse_qs
    params = parse_qs(query_string)

    # 客户端信息
    client_host, client_port = scope["client"]

    info = f"""Method: {method}
Path: {path}
HTTP Version: {http_version}
Query: {query_string}
Params: {params}
Client: {client_host}:{client_port}
User-Agent: {headers.get('user-agent', 'N/A')}
"""

    await send({
        "type": "http.response.start",
        "status": 200,
        "headers": [[b"content-type", b"text/plain"]],
    })
    await send({
        "type": "http.response.body",
        "body": info.encode(),
    })
```

测试：
```bash
uvicorn scope_parser:app --port 8000
curl "http://localhost:8000/users?page=1&size=10"
```

**示例 2：流式接收大请求体**

```python
# stream_receiver.py
async def app(scope, receive, send):
    if scope["type"] != "http":
        return

    # 流式接收请求体，避免一次性加载到内存
    total_bytes = 0
    chunks = 0
    more_body = True
    while more_body:
        message = await receive()
        chunk = message.get("body", b"")
        total_bytes += len(chunk)
        chunks += 1
        more_body = message.get("more_body", False)
        print(f"Received chunk {chunks}: {len(chunk)} bytes")

    result = f"Total: {total_bytes} bytes in {chunks} chunks".encode()

    await send({
        "type": "http.response.start",
        "status": 200,
        "headers": [[b"content-type", b"text/plain"]],
    })
    await send({"type": "http.response.body", "body": result})
```

测试大文件上传：
```bash
# 生成 10MB 文件并上传
dd if=/dev/zero of=/tmp/big.bin bs=1M count=10
curl -X POST -T /tmp/big.bin http://localhost:8000/upload
```

**示例 3：流式发送响应（Server-Sent Events 风格）**

```python
# stream_sender.py
import asyncio

async def app(scope, receive, send):
    if scope["type"] != "http":
        return

    await send({
        "type": "http.response.start",
        "status": 200,
        "headers": [[b"content-type", b"text/plain"]],
    })

    # 分多次发送响应体
    for i in range(5):
        chunk = f"Chunk {i}\n".encode()
        await send({
            "type": "http.response.body",
            "body": chunk,
            "more_body": True,  # 标记后续还有数据
        })
        await asyncio.sleep(0.5)  # 模拟耗时操作

    # 最后一次发送，more_body 必须为 False（或省略）
    await send({
        "type": "http.response.body",
        "body": b"Done\n",
    })
```

测试：
```bash
curl http://localhost:8000/
# 会看到每 0.5 秒输出一行
```

### 总结

- `scope` 是连接元信息，一次连接只创建一次，包含路径、方法、头部、客户端信息等
- `receive` 是异步函数，每次调用返回一条客户端消息，支持流式接收
- `send` 是异步函数，每次调用发送一条响应消息，支持流式发送
- HTTP headers 在 scope 中是小写字节列表 `[(b"name", b"value"), ...]`
- 利用 `more_body=True` 可实现流式响应，适合大文件下载、SSE、AI 流式输出
- WebSocket 的 scope 多了 `subprotocols` 字段，用于协议协商

---

## 第7讲 HTTP 请求/响应生命周期

### 概念

本讲追踪一个 HTTP 请求从客户端发出到响应返回的完整生命周期，理解 Uvicorn 内部如何处理连接、解析协议、调用应用、回写响应。这是排查性能问题、理解超时行为、设计中间件的基础。

### 原理

**完整生命周期时序**：

```
客户端                     Uvicorn                    ASGI 应用
  │                          │                           │
  │── TCP 连接 ─────────────▶│                           │
  │                          │                           │
  │── HTTP 请求行 ──────────▶│                           │
  │── HTTP Headers ─────────▶│                           │
  │── HTTP Body ────────────▶│                           │
  │                          │                           │
  │                  ┌───────┴────────┐                  │
  │                  │ httptools 解析  │                  │
  │                  │ 构造 scope      │                  │
  │                  └───────┬────────┘                  │
  │                          │                           │
  │                          │── app(scope, recv, send)─▶│
  │                          │                           │
  │                          │◀── await receive() ───────│
  │                          │── 返回 request 消息 ──────▶│
  │                          │                           │
  │                          │◀── await send(start) ─────│
  │◀── HTTP 响应行 ──────────│                           │
  │◀── HTTP Headers ─────────│                           │
  │                          │                           │
  │                          │◀── await send(body) ──────│
  │◀── HTTP Body ────────────│                           │
  │                          │                           │
  │── TCP FIN ──────────────▶│                           │
  │                          │                           │
  │                  ┌───────┴────────┐                  │
  │                  │ 连接回收        │                  │
  │                  └────────────────┘                  │
```

**关键阶段详解**：

1. **TCP 连接建立**：Uvicorn 在 `accept` 后创建连接对象，分配缓冲区
2. **HTTP 解析**：`httptools` 增量解析字节流，识别请求行、头部、正文边界
3. **Scope 构造**：解析完成后，Uvicorn 构造 scope 字典
4. **应用调用**：`await app(scope, receive, send)` 在事件循环中调度
5. **消息传递**：应用通过 `receive`/`send` 与 Uvicorn 交互
6. **响应回写**：Uvicorn 将 send 的消息转换为 HTTP 字节流写回 socket
7. **连接关闭**：根据 `Connection` 头决定是否保持 keep-alive

**Keep-Alive 机制**：

HTTP/1.1 默认开启 keep-alive，一个 TCP 连接可处理多个请求。Uvicorn 在响应完成后不立即关闭连接，而是等待下一个请求。这显著减少了 TCP 握手开销。

**超时控制**：
- `--timeout-keep-alive`：keep-alive 空闲超时（默认 5 秒）
- `--timeout-graceful-shutdown`：优雅关闭等待时间（默认 30 秒）

### 例子

**示例 1：可视化请求生命周期**

```python
# lifecycle_demo.py
import time
import asyncio

async def app(scope, receive, send):
    if scope["type"] != "http":
        return

    start_time = time.time()
    print(f"[{time.strftime('%H:%M:%S')}] === 请求开始 ===")
    print(f"  scope 创建: method={scope['method']}, path={scope['path']}")

    # 阶段 1：接收请求
    print(f"  [阶段1] 等待 receive()...")
    body = b""
    more_body = True
    while more_body:
        message = await receive()
        body += message.get("body", b"")
        more_body = message.get("more_body", False)
        print(f"  [阶段1] 收到消息: {len(message.get('body', b''))} bytes, more={more_body}")

    # 阶段 2：业务处理
    print(f"  [阶段2] 业务处理中...")
    await asyncio.sleep(0.1)  # 模拟处理耗时
    response_body = f"Processed: {body.decode()}".encode()

    # 阶段 3：发送响应起始
    print(f"  [阶段3] 发送 http.response.start")
    await send({
        "type": "http.response.start",
        "status": 200,
        "headers": [
            [b"content-type", b"text/plain"],
            [b"x-process-time", str(time.time() - start_time).encode()],
        ],
    })

    # 阶段 4：发送响应体
    print(f"  [阶段4] 发送 http.response.body")
    await send({"type": "http.response.body", "body": response_body})

    elapsed = time.time() - start_time
    print(f"[{time.strftime('%H:%M:%S')}] === 请求结束 (耗时 {elapsed:.3f}s) ===")
```

测试：
```bash
uvicorn lifecycle_demo:app --port 8000

# 另一个终端
curl -v -d "hello" http://localhost:8000/
```

服务器日志输出：
```
[14:30:01] === 请求开始 ===
  scope 创建: method=POST, path=/
  [阶段1] 等待 receive()...
  [阶段1] 收到消息: 5 bytes, more=False
  [阶段2] 业务处理中...
  [阶段3] 发送 http.response.start
  [阶段4] 发送 http.response.body
[14:30:01] === 请求结束 (耗时 0.105s) ===
```

**示例 2：Keep-Alive 演示**

```python
# keepalive_demo.py
async def app(scope, receive, send):
    if scope["type"] != "http":
        return

    client = scope["client"]
    print(f"  处理来自 {client} 的请求: {scope['path']}")

    await send({
        "type": "http.response.start",
        "status": 200,
        "headers": [[b"content-type", b"text/plain"]],
    })
    await send({"type": "http.response.body", "body": b"OK"})
```

测试 keep-alive：
```bash
uvicorn keepalive_demo:app --port 8000 --timeout-keep-alive 5

# 使用 curl 复用连接发送 3 个请求
curl http://localhost:8000/a http://localhost:8000/b http://localhost:8000/c

# 服务器日志会显示同一个客户端地址 [127.0.0.1, xxxxx] 发送了 3 个请求
# 5 秒后无新请求，连接被关闭
```

**示例 3：流式响应演示生命周期延长**

```python
# streaming_demo.py
import asyncio

async def app(scope, receive, send):
    if scope["type"] != "http":
        return

    await send({
        "type": "http.response.start",
        "status": 200,
        "headers": [[b"content-type", b"text/plain"]],
    })

    # 持续 10 秒发送数据，连接保持活跃
    for i in range(10):
        await send({
            "type": "http.response.body",
            "body": f"data {i}\n".encode(),
            "more_body": True,
        })
        await asyncio.sleep(1)

    await send({"type": "http.response.body", "body": b"end\n"})
```

这个例子展示了：响应未完成前，连接一直被占用，Uvicorn 不会处理该连接上的新请求。

### 总结

- HTTP 请求生命周期：TCP 连接 → HTTP 解析 → 应用调用 → 响应回写 → 连接回收
- Keep-Alive 允许一个 TCP 连接处理多个请求，大幅提升性能
- `--timeout-keep-alive` 控制空闲连接超时，过短会增加重连开销，过长浪费资源
- 流式响应期间连接被独占，需注意长连接对并发容量的影响
- 理解生命周期有助于排查"请求卡住"、"连接泄漏"等问题
- Uvicorn 的访问日志在响应发送完成后才记录，因此长请求的日志会延迟出现

---

## 第8讲 Lifespan 事件机制

### 概念

**Lifespan** 是 ASGI 协议中用于管理应用生命周期的特殊事件类型。它在服务器启动时触发 `startup` 事件，在关闭时触发 `shutdown` 事件，允许应用执行初始化（如连接数据库、加载模型）和清理（如关闭连接池、刷新缓存）操作。

### 原理

**Lifespan 工作机制**：

1. Uvicorn 启动时，构造一个 `scope["type"] == "lifespan"` 的特殊连接
2. 调用应用 `app(scope, receive, send)`
3. 应用进入循环，等待 `receive()` 返回 lifespan 消息
4. 收到 `lifespan.startup` → 执行初始化 → 发送 `lifespan.startup.complete`
5. 服务器开始接受 HTTP/WebSocket 请求
6. 收到终止信号（SIGINT/SIGTERM）→ 发送 `lifespan.shutdown`
7. 应用执行清理 → 发送 `lifespan.shutdown.complete`
8. 服务器退出

**Lifespan 消息类型**：

| 方向 | 消息类型 | 说明 |
|------|----------|------|
| 服务器→应用 | `lifespan.startup` | 请求应用启动 |
| 应用→服务器 | `lifespan.startup.complete` | 启动成功 |
| 应用→服务器 | `lifespan.startup.failed` | 启动失败（含 message 字段） |
| 服务器→应用 | `lifespan.shutdown` | 请求应用关闭 |
| 应用→服务器 | `lifespan.shutdown.complete` | 关闭完成 |
| 应用→服务器 | `lifespan.shutdown.failed` | 关闭失败 |

**Lifespan 配置**：

Uvicorn 通过 `--lifespan` 参数控制行为：
- `auto`（默认）：自动检测，应用不处理 lifespan 时跳过
- `on`：强制启用，应用必须正确响应
- `off`：禁用，即使应用支持也不调用

**为什么需要 Lifespan**：

- **资源初始化**：数据库连接池、Redis 客户端、ML 模型加载
- **资源清理**：关闭连接、刷新缓冲、保存状态
- **健康检查**：启动时验证依赖服务可用性
- **预热**：JIT 编译、缓存预热

### 例子

**示例 1：手动实现 Lifespan**

```python
# lifespan_manual.py
import asyncio

# 模拟资源
db_pool = None
redis_client = None

async def app(scope, receive, send):
    if scope["type"] == "lifespan":
        await handle_lifespan(scope, receive, send)
        return

    # HTTP 请求处理
    if scope["type"] == "http":
        global db_pool, redis_client
        result = f"DB: {db_pool}, Redis: {redis_client}".encode()
        await send({
            "type": "http.response.start",
            "status": 200,
            "headers": [[b"content-type", b"text/plain"]],
        })
        await send({"type": "http.response.body", "body": result})


async def handle_lifespan(scope, receive, send):
    global db_pool, redis_client
    while True:
        message = await receive()
        if message["type"] == "lifespan.startup":
            print("🚀 启动中: 初始化资源...")
            try:
                await asyncio.sleep(0.5)  # 模拟数据库连接
                db_pool = "connected"
                await asyncio.sleep(0.3)  # 模拟 Redis 连接
                redis_client = "connected"
                print("✅ 启动完成")
                await send({"type": "lifespan.startup.complete"})
            except Exception as e:
                print(f"❌ 启动失败: {e}")
                await send({
                    "type": "lifespan.startup.failed",
                    "message": str(e),
                })

        elif message["type"] == "lifespan.shutdown":
            print("🛑 关闭中: 清理资源...")
            await asyncio.sleep(0.2)  # 模拟关闭连接
            db_pool = None
            redis_client = None
            print("✅ 关闭完成")
            await send({"type": "lifespan.shutdown.complete"})
            break
```

测试：
```bash
uvicorn lifespan_manual:app --port 8000 --lifespan on

# 启动时看到:
# 🚀 启动中: 初始化资源...
# ✅ 启动完成
# INFO: Application startup complete.

# Ctrl+C 关闭时看到:
# 🛑 关闭中: 清理资源...
# ✅ 关闭完成
# INFO: Application shutdown complete.
```

**示例 2：使用 FastAPI 的 Lifespan（推荐方式）**

```python
# lifespan_fastapi.py
from contextlib import asynccontextmanager
from fastapi import FastAPI

# 模拟资源
resources = {}

@asynccontextmanager
async def lifespan(app: FastAPI):
    # === Startup ===
    print("初始化数据库连接...")
    resources["db"] = {"connection": "active"}
    
    print("加载 ML 模型...")
    resources["model"] = {"weights": [0.1, 0.2, 0.3]}
    
    print("预热缓存...")
    resources["cache"] = {"key1": "value1"}
    
    yield  # 应用运行期间
    
    # === Shutdown ===
    print("关闭数据库连接...")
    resources["db"] = None
    
    print("保存模型状态...")
    resources["model"] = None
    
    print("刷新缓存...")
    resources["cache"] = None


app = FastAPI(lifespan=lifespan)

@app.get("/")
async def root():
    return {
        "db": resources.get("db"),
        "model_loaded": resources.get("model") is not None,
    }
```

**示例 3：启动失败处理**

```python
# lifespan_failure.py
from contextlib import asynccontextmanager
from fastapi import FastAPI

@asynccontextmanager
async def lifespan(app: FastAPI):
    # 模拟启动时检查依赖
    import os
    db_url = os.getenv("DATABASE_URL")
    if not db_url:
        # 抛出异常会导致 startup.failed
        raise RuntimeError("DATABASE_URL 环境变量未设置，无法启动")
    
    print(f"连接数据库: {db_url}")
    yield
    print("关闭数据库连接")

app = FastAPI(lifespan=lifespan)
```

测试启动失败：
```bash
# 不设置 DATABASE_URL
uvicorn lifespan_failure:app --port 8000
# 输出:
# ERROR:    Application startup failed. Exiting.
# RuntimeError: DATABASE_URL 环境变量未设置，无法启动
```

### 总结

- Lifespan 是 ASGI 协议管理应用生命周期的机制，包含 startup 和 shutdown 两个阶段
- `--lifespan auto/on/off` 控制是否启用，默认 auto 自动检测
- 启动失败时发送 `lifespan.startup.failed`，Uvicorn 会拒绝接受请求并退出
- FastAPI/Starlette 提供 `@asynccontextmanager` 装饰器简化 Lifespan 编写
- Lifespan 适合做：数据库连接池初始化、模型加载、缓存预热、健康检查
- 不要在 Lifespan 中执行耗时操作（如下载大文件），会阻塞服务器启动
- Shutdown 阶段应快速完成，避免超过 `--timeout-graceful-shutdown`

---

# 第3章 配置与运行

本章深入讲解 Uvicorn 的配置体系。我们将详解所有启动参数、Worker 进程模型、日志系统、SSL/HTTPS 配置以及开发环境的热重载机制。掌握这些配置是高效开发和稳定部署的基础。

---

## 第9讲 启动参数详解

### 概念

Uvicorn 提供了丰富的启动参数，覆盖网络监听、进程管理、协议实现、日志、SSL 等各个方面。本讲系统讲解所有参数的含义、默认值和使用场景，帮助你精准控制服务器行为。

### 原理

Uvicorn 的所有配置最终都汇聚到 `uvicorn.config.Config` 类。无论是命令行参数还是 Python API 关键字参数，都会被解析为 Config 对象的属性。理解 Config 类是掌握 Uvicorn 配置的关键。

参数分类：
1. **网络监听**：host、port、uds（Unix Domain Socket）、fd（文件描述符）
2. **进程管理**：workers、reload、reload_dirs
3. **协议实现**：loop、http、ws、lifespan
4. **SSL/TLS**：ssl_keyfile、ssl_certfile、ssl_keyfile_password、ssl_version
5. **日志**：log_level、log_config、access_log
6. **超时**：timeout_keep_alive、timeout_graceful_shutdown
7. **限制**：limit_concurrency、limit_max_requests、backlog
8. **其他**：proxy_headers、forwarded_allow_ips、root_path、factory

### 例子

**完整参数示例（Python API）**：

```python
# config_full.py
import uvicorn

config = uvicorn.Config(
    app="main:app",
    
    # 网络监听
    host="0.0.0.0",           # 监听所有网卡
    port=8000,                 # 端口
    uds=None,                  # Unix socket 路径（与 host/port 互斥）
    fd=None,                   # 文件描述符（从 socket 继承）
    
    # 进程管理
    workers=4,                 # Worker 进程数
    reload=False,              # 热重载（生产禁用）
    reload_dirs=[],            # 热重载监听目录
    reload_includes=[],        # 热重载包含文件模式
    reload_excludes=[],        # 热重载排除文件模式
    
    # 协议实现
    loop="auto",               # 事件循环：auto/asyncio/uvloop
    http="auto",               # HTTP 解析器：auto/h11/httptools
    ws="auto",                 # WebSocket 实现：auto/websockets/wsproto
    lifespan="auto",           # Lifespan 处理：auto/on/off
    
    # SSL/TLS
    ssl_keyfile=None,          # SSL 私钥
    ssl_certfile=None,         # SSL 证书
    ssl_keyfile_password=None, # 私钥密码
    ssl_version=17,            # SSL 协议版本
    ssl_cert_reqs=0,           # 证书验证级别
    ssl_ca_certs=None,         # CA 证书
    ssl_ciphers="TLSv1",       # 加密套件
    
    # 日志
    log_level="info",          # 日志级别
    log_config=None,           # 日志配置（dict 或文件路径）
    access_log=True,           # 是否记录访问日志
    
    # 超时
    timeout_keep_alive=5,      # keep-alive 超时（秒）
    timeout_graceful_shutdown=30,  # 优雅关闭超时
    
    # 限制
    limit_concurrency=None,    # 最大并发连接数
    limit_max_requests=None,   # 处理多少请求后重启 worker
    backlog=2048,              # TCP 连接队列大小
    
    # 代理
    proxy_headers=False,       # 信任 X-Forwarded-* 头
    forwarded_allow_ips=None,  # 允许的转发 IP
    
    # 其他
    root_path="",              # 应用挂载根路径
    factory=False,             # 应用是否为工厂函数
    headers=[],                # 默认响应头
)

server = uvicorn.Server(config)
server.run()
```

**关键参数详解**：

**1. `loop` - 事件循环选择**：
```bash
# 自动选择（推荐）：优先 uvloop，回退 asyncio
uvicorn main:app --loop auto

# 强制使用 uvloop（性能最佳）
uvicorn main:app --loop uvloop

# 使用标准库 asyncio（兼容性最好）
uvicorn main:app --loop asyncio
```

**2. `http` - HTTP 解析器选择**：
```bash
# 自动选择（推荐）
uvicorn main:app --http auto

# httptools（C 实现，最快）
uvicorn main:app --http httptools

# h11（纯 Python，兼容性好）
uvicorn main:app --http h11
```

**3. `limit_concurrency` - 并发限制**：
```python
# 限制最大并发连接数，超过返回 503
uvicorn.run(
    "main:app",
    limit_concurrency=1000,  # 最多 1000 个并发连接
)
```

**4. `limit_max_requests` - Worker 自动重启**：
```python
# 每个 worker 处理 10000 个请求后自动重启
# 用于防止内存泄漏
uvicorn.run(
    "main:app",
    workers=4,
    limit_max_requests=10000,
)
```

**5. `headers` - 默认响应头**：
```python
uvicorn.run(
    "main:app",
    headers=[
        ["X-Frame-Options", "DENY"],
        ["X-Content-Type-Options", "nosniff"],
        ["X-Powered-By", "MyApp"],
    ],
)
```

**6. `root_path` - 应用挂载根路径**：
```python
# 当应用部署在 /api/v1 子路径下时
uvicorn.run(
    "main:app",
    root_path="/api/v1",
)
# 应用内的路径 /users 实际访问 /api/v1/users
```

### 总结

- Uvicorn 所有配置最终汇聚到 `uvicorn.Config` 类
- 生产环境推荐：`loop=uvloop`、`http=httptools`、`ws=websockets`
- `limit_concurrency` 防止过载，`limit_max_requests` 防止内存泄漏
- `proxy_headers=True` 在反向代理后必须开启，否则获取不到真实客户端 IP
- `root_path` 用于子路径部署，配合 Nginx 的 `proxy_pass` 使用
- 配置参数可通过命令行、Python API、环境变量、配置文件四种方式传递

---

## 第10讲 Worker 进程与多核利用

### 概念

**Worker 进程**是 Uvicorn 实现多核 CPU 利用的机制。默认情况下 Uvicorn 运行单进程，无法利用多核。通过 `--workers N` 参数可以启动 N 个 Worker 进程，每个进程独立运行事件循环，共同监听同一端口，由操作系统进行负载均衡。

### 原理

**为什么需要多 Worker**：

Python 的 GIL（全局解释器锁）限制了单进程同一时刻只能执行一个 Python 字节码线程。虽然异步 I/O 可以在单线程内处理大量并发，但 CPU 密集型任务仍受 GIL 限制。多 Worker 进程通过 fork 创建多个独立 Python 进程，每个进程有自己的 GIL 和事件循环，从而充分利用多核 CPU。

**Uvicorn 的多 Worker 模型**：

```
                    ┌─────────────────────┐
                    │  Master 进程（主进程） │
                    │  - 监听端口           │
                    │  - fork worker 进程   │
                    │  - 监控 worker 健康   │
                    │  - 处理信号           │
                    └──────────┬──────────┘
                               │ fork
            ┌──────────┬───────┴───────┬──────────┐
            ▼          ▼               ▼          ▼
       ┌────────┐ ┌────────┐      ┌────────┐ ┌────────┐
       │Worker 1│ │Worker 2│      │Worker 3│ │Worker 4│
       │事件循环 │ │事件循环 │      │事件循环 │ │事件循环 │
       │ASGI app│ │ASGI app│      │ASGI app│ │ASGI app│
       └────────┘ └────────┘      └────────┘ └────────┘
            │          │               │          │
            └──────────┴───────┬───────┴──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │   操作系统 socket    │
                    │   （SO_REUSEPORT）   │
                    └─────────────────────┘
```

**Worker 数量选择原则**：
- **CPU 密集型**：Worker 数 = CPU 核心数
- **I/O 密集型**：Worker 数 = CPU 核心数 × 2（甚至更多）
- **混合型**：Worker 数 = CPU 核心数 + 1（推荐起点）

**Master 进程的职责**：
1. 启动时 fork 出 N 个 Worker 进程
2. 监控 Worker 健康状态，崩溃时自动重启
3. 接收信号（SIGTERM、SIGINT）并转发给 Worker
4. 协调优雅关闭

**重要限制**：
- 多 Worker 模式下，应用对象无法在进程间共享内存
- 全局变量在每个 Worker 中是独立的副本
- 因此 `uvicorn.run(app_object, workers=N)` 不支持，必须传字符串 `"module:app"`

### 例子

**示例 1：启动多 Worker**

```bash
# 启动 4 个 worker
uvicorn main:app --workers 4 --host 0.0.0.0 --port 8000

# 输出:
# INFO:     Started parent process [12345]
# INFO:     Started server process [12346]
# INFO:     Waiting for application startup.
# INFO:     Application startup complete.
# INFO:     Started server process [12347]
# INFO:     Waiting for application startup.
# INFO:     Application startup complete.
# ... (共 4 个 worker)
```

**示例 2：验证 Worker 独立性**

```python
# worker_isolation.py
import os
import time

# 全局变量 - 每个 worker 独立
request_count = 0

async def app(scope, receive, send):
    global request_count
    request_count += 1
    
    body = f"""Worker PID: {os.getpid()}
Request count in this worker: {request_count}
""".encode()
    
    await send({
        "type": "http.response.start",
        "status": 200,
        "headers": [[b"content-type", b"text/plain"]],
    })
    await send({"type": "http.response.body", "body": body})
```

测试：
```bash
uvicorn worker_isolation:app --workers 3 --port 8000

# 连续请求 6 次
for i in $(seq 1 6); do curl -s http://localhost:8000/; echo "---"; done

# 输出会显示不同的 PID，且每个 worker 的 count 独立增长
# Worker PID: 12346, count: 1
# Worker PID: 12347, count: 1
# Worker PID: 12348, count: 1
# Worker PID: 12346, count: 2
# Worker PID: 12347, count: 2
# Worker PID: 12348, count: 2
```

**示例 3：自动选择 Worker 数量**

```python
# auto_workers.py
import os
import multiprocessing
import uvicorn

def get_recommended_workers():
    """根据 CPU 核心数推荐 worker 数量"""
    cpu_count = multiprocessing.cpu_count()
    # 通用公式：CPU 核心数 + 1
    return cpu_count + 1

if __name__ == "__main__":
    workers = get_recommended_workers()
    print(f"Starting with {workers} workers on {multiprocessing.cpu_count()} CPU cores")
    
    uvicorn.run(
        "auto_workers:app",
        workers=workers,
        host="0.0.0.0",
        port=8000,
    )

async def app(scope, receive, send):
    await send({"type": "http.response.start", "status": 200, "headers": []})
    await send({"type": "http.response.body", "body": b"OK"})
```

**示例 4：Worker 崩溃自动重启**

```python
# worker_crash.py
import os

async def app(scope, receive, send):
    if scope["type"] != "http":
        return
    
    path = scope["path"]
    if path == "/crash":
        # 模拟崩溃
        os.kill(os.getpid(), 9)  # SIGKILL
    
    await send({"type": "http.response.start", "status": 200, "headers": []})
    await send({"type": "http.response.body", "body": b"OK"})
```

测试：
```bash
uvicorn worker_crash:app --workers 2 --port 8000

# 触发崩溃
curl http://localhost:8000/crash
# 服务器日志会显示 worker 崩溃并自动重启
# WARNING:  Worker process 12346 died, restarting...
```

### 总结

- 多 Worker 是 Uvicorn 利用多核 CPU 的唯一方式（受 GIL 限制）
- 推荐起始值：CPU 核心数 + 1，根据实际负载调整
- Worker 进程间内存独立，全局变量不共享，需用 Redis 等外部存储共享状态
- Master 进程负责 fork、监控、重启 Worker，处理信号
- 多 Worker 模式必须传字符串 `"module:app"`，不能传应用对象
- Worker 崩溃会被 Master 自动重启，保证服务可用性
- 生产环境推荐用 Gunicorn 管理 Uvicorn Worker（见第22讲）

---

## 第11讲 日志系统配置

### 概念

Uvicorn 的日志系统基于 Python 标准库 `logging` 模块，分为四类日志：**访问日志**（access log）、**错误日志**（error log）、**应用日志**（user log）、**Lifespan 日志**。本讲讲解如何配置、自定义和集成日志系统。

### 原理

**Uvicorn 的日志器层次**：

```
uvicorn（根日志器）
├── uvicorn.error（错误日志器）
│   └── 记录服务器启动、关闭、异常等
└── uvicorn.access（访问日志器）
    └── 记录每个 HTTP 请求
```

**默认日志格式**：

访问日志：
```
INFO:     127.0.0.1:54321 - "GET /api/users HTTP/1.1" 200 OK
```

错误日志：
```
INFO:     Started server process [12345]
INFO:     Application startup complete.
WARNING:  Invalid HTTP request received.
ERROR:    Exception in ASGI application
```

**日志级别**：
- `critical`：严重错误，服务无法继续
- `error`：错误，影响功能
- `warning`：警告，潜在问题
- `info`：信息，正常运行（默认）
- `debug`：调试信息
- `trace`：最详细，仅开发使用

**日志配置方式**：
1. `--log-level`：全局日志级别
2. `--access-log`：是否启用访问日志
3. `--log-config`：日志配置文件（dict/JSON/YAML/INI）

### 例子

**示例 1：基本日志控制**

```bash
# 关闭访问日志（生产环境可能不需要）
uvicorn main:app --no-access-log

# 设置日志级别为 debug
uvicorn main:app --log-level debug

# 关闭所有日志
uvicorn main:app --log-level critical --no-access-log
```

**示例 2：使用 JSON 格式日志（生产推荐）**

```python
# log_json_config.py
import logging
import json
import sys
from datetime import datetime

class JsonFormatter(logging.Formatter):
    """JSON 格式日志格式化器"""
    
    def format(self, record):
        log_data = {
            "timestamp": datetime.utcnow().isoformat() + "Z",
            "level": record.levelname,
            "logger": record.name,
            "message": record.getMessage(),
        }
        
        # 访问日志特殊字段
        if hasattr(record, "client"):
            log_data.update({
                "client": record.client,
                "method": record.method,
                "path": record.path,
                "status_code": record.status_code,
            })
        
        # 异常信息
        if record.exc_info:
            log_data["exception"] = self.formatException(record.exc_info)
        
        return json.dumps(log_data, ensure_ascii=False)


log_config = {
    "version": 1,
    "disable_existing_loggers": False,
    "formatters": {
        "json": {
            "()": "log_json_config.JsonFormatter",
        },
        "default": {
            "format": "%(asctime)s [%(levelname)s] %(name)s: %(message)s",
        },
    },
    "handlers": {
        "default": {
            "formatter": "json",
            "class": "logging.StreamHandler",
            "stream": "ext://sys.stdout",
        },
    },
    "loggers": {
        "uvicorn": {
            "handlers": ["default"],
            "level": "INFO",
            "propagate": False,
        },
        "uvicorn.error": {
            "level": "INFO",
        },
        "uvicorn.access": {
            "handlers": ["default"],
            "level": "INFO",
            "propagate": False,
        },
    },
}
```

使用：
```python
# main.py
import uvicorn
from log_json_config import log_config

async def app(scope, receive, send):
    await send({"type": "http.response.start", "status": 200, "headers": []})
    await send({"type": "http.response.body", "body": b"OK"})

if __name__ == "__main__":
    uvicorn.run(
        "main:app",
        log_config=log_config,
        log_level="info",
    )
```

输出示例：
```json
{"timestamp": "2026-01-15T10:30:00Z", "level": "INFO", "logger": "uvicorn.access", "message": "127.0.0.1:54321 - \"GET / HTTP/1.1\" 200", "client": "127.0.0.1:54321", "method": "GET", "path": "/", "status_code": 200}
```

**示例 3：自定义访问日志格式**

```python
# log_custom_access.py
import logging

class CustomAccessFormatter(logging.Formatter):
    """自定义访问日志格式"""
    
    def format(self, record):
        # record 属性: client, method, path, status_code, etc.
        client = getattr(record, "client", "-")
        method = getattr(record, "method", "-")
        path = getattr(record, "path", "-")
        status = getattr(record, "status_code", "-")
        
        return f"[ACCESS] {client} {method} {path} -> {status}"


log_config = {
    "version": 1,
    "disable_existing_loggers": False,
    "formatters": {
        "custom": {
            "()": "log_custom_access.CustomAccessFormatter",
        },
    },
    "handlers": {
        "access": {
            "formatter": "custom",
            "class": "logging.StreamHandler",
            "stream": "ext://sys.stdout",
        },
    },
    "loggers": {
        "uvicorn.access": {
            "handlers": ["access"],
            "level": "INFO",
            "propagate": False,
        },
    },
}

import uvicorn
uvicorn.run("main:app", log_config=log_config)
```

**示例 4：集成应用日志**

```python
# log_app_integration.py
import logging
import uvicorn
from fastapi import FastAPI

# 应用日志器
logger = logging.getLogger("myapp")

app = FastAPI()

@app.get("/")
async def root():
    logger.info("处理根路径请求")
    return {"message": "Hello"}

@app.get("/error")
async def trigger_error():
    logger.error("触发了一个错误")
    raise ValueError("Something went wrong")

if __name__ == "__main__":
    # 配置应用日志器使用 uvicorn 的 handler
    logging.config.dictConfig({
        "version": 1,
        "disable_existing_loggers": False,
        "loggers": {
            "myapp": {
                "handlers": ["default"],
                "level": "INFO",
            },
        },
    })
    
    uvicorn.run("log_app_integration:app", log_level="info")
```

### 总结

- Uvicorn 日志分为 `uvicorn.error` 和 `uvicorn.access` 两个日志器
- 生产环境推荐 JSON 格式日志，便于 ELK/Loki 等系统采集
- `--log-config` 接受 dict、JSON、YAML、INI 四种格式
- 访问日志包含 client、method、path、status_code 等字段，可自定义格式
- 应用日志器应集成到 Uvicorn 日志体系，避免日志分裂
- 高并发下访问日志可能成为性能瓶颈，可考虑采样或关闭

---

## 第12讲 SSL/HTTPS 配置

### 概念

**SSL/TLS** 是保障 HTTP 通信安全的核心协议。Uvicorn 原生支持 SSL/TLS，可以通过 `--ssl-keyfile` 和 `--ssl-certfile` 参数启用 HTTPS。本讲讲解如何生成证书、配置 Uvicorn、以及生产环境的 SSL 最佳实践。

### 原理

**SSL/TLS 握手过程**：

```
客户端                         服务器
  │                              │
  │── Client Hello ─────────────▶│  (支持的加密套件、SSL 版本)
  │                              │
  │◀── Server Hello ─────────────│  (选定套件、证书)
  │◀── Certificate ──────────────│  (服务器证书)
  │                              │
  │── Key Exchange ─────────────▶│  (交换密钥)
  │── Change Cipher Spec ───────▶│  (切换到加密)
  │                              │
  │◀── Change Cipher Spec ───────│
  │◀── Finished ─────────────────│
  │── Finished ─────────────────▶│
  │                              │
  │═══ 加密通信开始 ══════════════│
```

**Uvicorn SSL 配置参数**：

| 参数 | 说明 | 示例 |
|------|------|------|
| `ssl_keyfile` | 私钥文件路径 | `./certs/private.key` |
| `ssl_certfile` | 证书文件路径 | `./certs/cert.pem` |
| `ssl_keyfile_password` | 私钥密码 | `mypassword` |
| `ssl_version` | SSL 协议版本 | 17 (TLSv1.2) |
| `ssl_cert_reqs` | 是否验证客户端证书 | 0/1/2 |
| `ssl_ca_certs` | CA 证书路径 | `./certs/ca.pem` |
| `ssl_ciphers` | 加密套件 | `TLSv1.2` |

**生产环境 SSL 注意事项**：
- Uvicorn 直接处理 SSL 会消耗较多 CPU（每个连接都要握手）
- 生产环境推荐：Nginx 处理 SSL，Uvicorn 只处理 HTTP（SSL 卸载）
- 仅在开发测试或简单部署时让 Uvicorn 直接处理 SSL

### 例子

**示例 1：生成自签名证书（开发用）**

```bash
# 生成私钥和证书（一步完成）
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem \
    -days 365 -nodes -subj "/CN=localhost" \
    -addext "subjectAltName=DNS:localhost,IP:127.0.0.1"

# 或分步生成
# 1. 生成私钥
openssl genrsa -out key.pem 4096

# 2. 生成证书签名请求（CSR）
openssl req -new -key key.pem -out csr.pem -subj "/CN=localhost"

# 3. 自签名证书
openssl x509 -req -days 365 -in csr.pem -signkey key.pem -out cert.pem
```

**示例 2：Uvicorn 启用 HTTPS**

```bash
# 命令行方式
uvicorn main:app \
    --ssl-keyfile ./key.pem \
    --ssl-certfile ./cert.pem \
    --port 8443

# Python API 方式
```

```python
# ssl_basic.py
import uvicorn

async def app(scope, receive, send):
    await send({"type": "http.response.start", "status": 200, "headers": []})
    await send({"type": "http.response.body", "body": b"Secure Hello"})

if __name__ == "__main__":
    uvicorn.run(
        "ssl_basic:app",
        host="0.0.0.0",
        port=8443,
        ssl_keyfile="./key.pem",
        ssl_certfile="./cert.pem",
    )
```

测试：
```bash
# 使用 curl 访问（-k 忽略自签名证书警告）
curl -k https://localhost:8443/
# 输出: Secure Hello

# 查看证书信息
openssl s_client -connect localhost:8443 -showcerts < /dev/null
```

**示例 3：使用 Let's Encrypt 证书（生产）**

```bash
# 1. 使用 certbot 获取证书
sudo certbot certonly --standalone -d example.com

# 2. 证书位置
# /etc/letsencrypt/live/example.com/fullchain.pem  (证书)
# /etc/letsencrypt/live/example.com/privkey.pem    (私钥)

# 3. 启动 Uvicorn
uvicorn main:app \
    --ssl-keyfile /etc/letsencrypt/live/example.com/privkey.pem \
    --ssl-certfile /etc/letsencrypt/live/example.com/fullchain.pem \
    --port 443 \
    --host 0.0.0.0
```

**示例 4：HTTP/2 支持（需配合 SSL）**

```python
# ssl_http2.py
import uvicorn

async def app(scope, receive, send):
    http_version = scope.get("http_version", "1.1")
    body = f"HTTP Version: {http_version}".encode()
    
    await send({
        "type": "http.response.start",
        "status": 200,
        "headers": [[b"content-type", b"text/plain"]],
    })
    await send({"type": "http.response.body", "body": body})

if __name__ == "__main__":
    uvicorn.run(
        "ssl_http2:app",
        port=8443,
        ssl_keyfile="./key.pem",
        ssl_certfile="./cert.pem",
        # Uvicorn 本身不支持 HTTP/2，需用 Hypercorn 或 Nginx
    )
```

> **注意**：Uvicorn 目前不支持 HTTP/2。如需 HTTP/2，可使用 Hypercorn，或在 Uvicorn 前部署 Nginx 处理 HTTP/2。

**示例 5：双向 TLS（mTLS）**

```python
# ssl_mtls.py
import uvicorn

async def app(scope, receive, send):
    # 在 mTLS 中，客户端证书信息会在 scope 中
    client_cert = scope.get("extensions", {}).get("tls", {})
    
    body = f"Client cert: {client_cert}".encode()
    await send({
        "type": "http.response.start",
        "status": 200,
        "headers": [[b"content-type", b"text/plain"]],
    })
    await send({"type": "http.response.body", "body": body})

if __name__ == "__main__":
    uvicorn.run(
        "ssl_mtls:app",
        port=8443,
        ssl_keyfile="./server.key",
        ssl_certfile="./server.crt",
        ssl_ca_certs="./ca.crt",          # CA 证书，用于验证客户端
        ssl_cert_reqs=2,                   # 2 = CERT_REQUIRED，强制客户端证书
    )
```

### 总结

- Uvicorn 原生支持 SSL/TLS，通过 `--ssl-keyfile` 和 `--ssl-certfile` 配置
- 开发环境用自签名证书，生产环境用 Let's Encrypt 或商业证书
- 生产环境推荐 SSL 卸载：Nginx 处理 SSL，Uvicorn 处理 HTTP，降低 CPU 压力
- Uvicorn 不支持 HTTP/2，需用 Hypercorn 或前置 Nginx
- mTLS（双向认证）通过 `ssl_ca_certs` + `ssl_cert_reqs=2` 实现
- 证书续期：Let's Encrypt 证书 90 天过期，需配置自动续期

---

## 第13讲 热重载与开发工作流

### 概念

**热重载（Hot Reload）** 是 Uvicorn 在开发环境下的核心特性。开启后，Uvicorn 会监听文件变化，当代码修改保存时自动重启服务器，无需手动停止再启动。这极大提升了开发效率。

### 原理

**热重载工作机制**：

```
┌──────────────────────────────────────────┐
│           Reloader 进程（主进程）          │
│                                          │
│  ┌────────────────────────────────────┐  │
│  │     WatchFiles / WatchGod          │  │
│  │     监听文件系统变化                │  │
│  └──────────────┬─────────────────────┘  │
│                 │ 文件变化                │
│                 ▼                        │
│  ┌────────────────────────────────────┐  │
│  │  发送 SIGTERM 给 Worker            │  │
│  │  等待 Worker 退出                  │  │
│  │  重新 fork 新 Worker               │  │
│  └────────────────────────────────────┘  │
│                                          │
└──────────────────┬───────────────────────┘
                   │ fork
                   ▼
┌──────────────────────────────────────────┐
│           Worker 进程（子进程）            │
│                                          │
│  运行 Uvicorn Server + ASGI 应用          │
│  接收文件变化信号后退出                    │
└──────────────────────────────────────────┘
```

**关键点**：
1. 热重载需要两个进程：Reloader（监听）+ Worker（运行）
2. 文件变化时，Reloader 杀死旧 Worker，fork 新 Worker
3. 因此热重载有"重启延迟"（通常 0.5-2 秒）
4. 热重载基于文件系统通知（inotify/FSEvents），CPU 开销极小

**热重载的限制**：
- 只能检测 Python 文件变化（可配置）
- 无法热重载 C 扩展模块（需完全重启）
- 多 Worker 模式下不支持热重载（`--reload` 与 `--workers` 互斥）
- 修改依赖包（site-packages）默认不触发重载

### 例子

**示例 1：基本热重载**

```bash
# 启动并开启热重载
uvicorn main:app --reload

# 修改 main.py 保存后，服务器自动重启
# INFO:     Reloading...
# INFO:     Waiting for application startup.
# INFO:     Application startup complete.
```

**示例 2：指定监听目录**

```bash
# 只监听 ./app 目录
uvicorn main:app --reload --reload-dir ./app

# 监听多个目录
uvicorn main:app --reload \
    --reload-dir ./app \
    --reload-dir ./lib \
    --reload-dir ./config
```

**示例 3：包含/排除文件模式**

```bash
# 监听所有 .py 和 .html 文件
uvicorn main:app --reload \
    --reload-include "*.py" \
    --reload-include "*.html"

# 排除测试文件
uvicorn main:app --reload \
    --reload-exclude "*test*.py" \
    --reload-exclude "*_old.py"
```

**示例 4：Python API 配置热重载**

```python
# reload_api.py
import uvicorn

if __name__ == "__main__":
    uvicorn.run(
        "main:app",
        reload=True,
        reload_dirs=["./app", "./lib"],  # 注意：Python API 用下划线
        reload_includes=["*.py"],
        reload_excludes=["*test*.py"],
        reload_delay=0.5,  # 重载延迟（秒），避免频繁重启
    )
```

**示例 5：完整的开发工作流配置**

```python
# dev_config.py
"""开发环境配置"""
import uvicorn

DEV_CONFIG = {
    "app": "main:app",
    "host": "0.0.0.0",
    "port": 8000,
    "reload": True,
    "reload_dirs": ["./app", "./lib", "./tests"],
    "reload_includes": ["*.py", "*.html", "*.yaml"],
    "reload_excludes": ["*test*.py", "__pycache__/*"],
    "log_level": "debug",
    "access_log": True,
}

if __name__ == "__main__":
    uvicorn.run(**DEV_CONFIG)
```

**示例 6：使用环境变量切换开发/生产配置**

```python
# env_aware.py
import os
import uvicorn

def get_config():
    env = os.getenv("ENV", "development")
    
    if env == "development":
        return {
            "app": "main:app",
            "host": "127.0.0.1",
            "port": 8000,
            "reload": True,
            "log_level": "debug",
        }
    elif env == "production":
        return {
            "app": "main:app",
            "host": "0.0.0.0",
            "port": 8000,
            "workers": 4,
            "log_level": "info",
            "access_log": True,
            "proxy_headers": True,
        }
    else:
        raise ValueError(f"Unknown ENV: {env}")

if __name__ == "__main__":
    uvicorn.run(**get_config())
```

使用：
```bash
# 开发模式
ENV=development python env_aware.py

# 生产模式
ENV=production python env_aware.py
```

**示例 7：配合 .env 文件**

```bash
# .env 文件
APP_HOST=0.0.0.0
APP_PORT=8000
APP_RELOAD=true
APP_WORKERS=4
APP_LOG_LEVEL=info
```

```python
# dotenv_config.py
import os
from dotenv import load_dotenv
import uvicorn

load_dotenv()  # 加载 .env 文件

if __name__ == "__main__":
    uvicorn.run(
        "main:app",
        host=os.getenv("APP_HOST", "127.0.0.1"),
        port=int(os.getenv("APP_PORT", "8000")),
        reload=os.getenv("APP_RELOAD", "false").lower() == "true",
        workers=int(os.getenv("APP_WORKERS", "1")),
        log_level=os.getenv("APP_LOG_LEVEL", "info"),
    )
```

### 总结

- 热重载通过 Reloader + Worker 双进程实现，文件变化时重启 Worker
- `--reload-dir` 指定监听目录，`--reload-include/exclude` 过滤文件
- 热重载仅用于开发，生产环境必须关闭（性能损耗 + 不稳定）
- `--reload` 与 `--workers` 互斥，多 Worker 时无法热重载
- 推荐使用环境变量 + .env 文件管理开发/生产配置
- 修改 C 扩展、依赖包不会触发重载，需手动重启

---

# 第4章 与框架集成

Uvicorn 是 ASGI 服务器，本身不提供路由、模板、ORM 等功能，这些由上层框架实现。本章讲解 Uvicorn 与主流 Python Web 框架的集成方式，包括 FastAPI、Starlette、Django（ASGI 模式）和 Flask（通过 asgiref 桥接）。

---

## 第14讲 Uvicorn + FastAPI 实战

### 概念

**FastAPI** 是基于 Starlette 和 Pydantic 构建的现代 Python Web 框架，由 Sebastián Ramírez 开发。它提供类型提示驱动的自动文档、数据验证、依赖注入等特性，是当前 Python 生态中最流行的 ASGI 框架。Uvicorn 是 FastAPI 官方推荐的 ASGI 服务器。

### 原理

**FastAPI 与 Uvicorn 的关系**：

```
┌─────────────────────────────────────┐
│           Uvicorn（ASGI 服务器）      │
│  - 监听端口、解析 HTTP                │
│  - 管理事件循环、Worker 进程          │
│  - 调用 ASGI 应用                    │
└──────────────┬──────────────────────┘
               │ ASGI 协议
               ▼
┌─────────────────────────────────────┐
│           Starlette（ASGI 工具包）    │
│  - 路由、中间件、请求/响应对象         │
│  - WebSocket、SSE、后台任务           │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│           FastAPI（API 框架）         │
│  - 类型提示、Pydantic 验证            │
│  - 依赖注入、自动文档（OpenAPI）       │
│  - 路由装饰器、安全工具               │
└─────────────────────────────────────┘
```

FastAPI 应用本身就是一个 ASGI 应用，可以直接传给 Uvicorn 运行。

**FastAPI 应用的 ASGI 接口**：

```python
# FastAPI 的 app 对象实现了 __call__
app = FastAPI()

# 等价于:
async def app(scope, receive, send):
    # FastAPI 内部处理路由、验证、响应
    ...
```

### 例子

**示例 1：最小 FastAPI 应用**

```python
# fastapi_basic.py
from fastapi import FastAPI
import uvicorn

app = FastAPI(title="My API", version="1.0.0")

@app.get("/")
async def root():
    return {"message": "Hello, FastAPI!"}

@app.get("/items/{item_id}")
async def get_item(item_id: int, q: str = None):
    return {"item_id": item_id, "q": q}

if __name__ == "__main__":
    uvicorn.run("fastapi_basic:app", host="0.0.0.0", port=8000, reload=True)
```

运行：
```bash
python fastapi_basic.py
# 或
uvicorn fastapi_basic:app --reload

# 访问 API
curl http://localhost:8000/
# {"message":"Hello, FastAPI!"}

# 访问自动文档
# http://localhost:8000/docs      (Swagger UI)
# http://localhost:8000/redoc     (ReDoc)
```

**示例 2：完整的 CRUD API**

```python
# fastapi_crud.py
from fastapi import FastAPI, HTTPException, status
from pydantic import BaseModel
from typing import List, Optional
import uvicorn

app = FastAPI(title="Todo API")

# 数据模型
class TodoCreate(BaseModel):
    title: str
    description: Optional[str] = None

class TodoResponse(BaseModel):
    id: int
    title: str
    description: Optional[str]
    completed: bool

# 内存存储（生产环境用数据库）
todos: dict[int, dict] = {}
next_id = 1

@app.post("/todos", response_model=TodoResponse, status_code=status.HTTP_201_CREATED)
async def create_todo(todo: TodoCreate):
    global next_id
    todo_dict = {
        "id": next_id,
        "title": todo.title,
        "description": todo.description,
        "completed": False,
    }
    todos[next_id] = todo_dict
    next_id += 1
    return todo_dict

@app.get("/todos", response_model=List[TodoResponse])
async def list_todos():
    return list(todos.values())

@app.get("/todos/{todo_id}", response_model=TodoResponse)
async def get_todo(todo_id: int):
    if todo_id not in todos:
        raise HTTPException(status_code=404, detail="Todo not found")
    return todos[todo_id]

@app.put("/todos/{todo_id}", response_model=TodoResponse)
async def update_todo(todo_id: int, todo: TodoCreate):
    if todo_id not in todos:
        raise HTTPException(status_code=404, detail="Todo not found")
    todos[todo_id].update(todo.dict())
    return todos[todo_id]

@app.delete("/todos/{todo_id}", status_code=status.HTTP_204_NO_CONTENT)
async def delete_todo(todo_id: int):
    if todo_id not in todos:
        raise HTTPException(status_code=404, detail="Todo not found")
    del todos[todo_id]

if __name__ == "__main__":
    uvicorn.run("fastapi_crud:app", host="0.0.0.0", port=8000, reload=True)
```

**示例 3：异步数据库集成（SQLAlchemy + asyncpg）**

```python
# fastapi_async_db.py
from contextlib import asynccontextmanager
from fastapi import FastAPI, Depends
from sqlalchemy.ext.asyncio import AsyncSession, create_async_engine
from sqlalchemy.orm import sessionmaker, declarative_base
from sqlalchemy import Column, Integer, String, select
from pydantic import BaseModel
import uvicorn

# 数据库配置
DATABASE_URL = "postgresql+asyncpg://user:pass@localhost/db"
engine = create_async_engine(DATABASE_URL, echo=True)
async_session = sessionmaker(engine, class_=AsyncSession, expire_on_commit=False)
Base = declarative_base()

# 模型
class User(Base):
    __tablename__ = "users"
    id = Column(Integer, primary_key=True)
    name = Column(String(100))
    email = Column(String(100), unique=True)

# Pydantic 模型
class UserCreate(BaseModel):
    name: str
    email: str

class UserResponse(BaseModel):
    id: int
    name: str
    email: str

# 依赖
async def get_db():
    async with async_session() as session:
        yield session

# Lifespan
@asynccontextmanager
async def lifespan(app: FastAPI):
    # 启动时创建表
    async with engine.begin() as conn:
        await conn.run_sync(Base.metadata.create_all)
    yield
    # 关闭时释放连接池
    await engine.dispose()

app = FastAPI(lifespan=lifespan)

@app.post("/users", response_model=UserResponse)
async def create_user(user: UserCreate, db: AsyncSession = Depends(get_db)):
    db_user = User(name=user.name, email=user.email)
    db.add(db_user)
    await db.commit()
    await db.refresh(db_user)
    return db_user

@app.get("/users", response_model=list[UserResponse])
async def list_users(db: AsyncSession = Depends(get_db)):
    result = await db.execute(select(User))
    return result.scalars().all()

if __name__ == "__main__":
    uvicorn.run("fastapi_async_db:app", host="0.0.0.0", port=8000, reload=True)
```

**示例 4：生产环境启动配置**

```python
# fastapi_prod.py
import uvicorn
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def root():
    return {"status": "ok"}

if __name__ == "__main__":
    uvicorn.run(
        "fastapi_prod:app",
        host="0.0.0.0",
        port=8000,
        workers=4,                # 多 worker
        loop="uvloop",            # 高性能事件循环
        http="httptools",         # 高性能 HTTP 解析
        log_level="info",
        access_log=True,
        proxy_headers=True,       # 信任反向代理头
        timeout_keep_alive=65,    # 配合 Nginx 的 keepalive_timeout
    )
```

### 总结

- FastAPI 是基于 Starlette + Pydantic 的现代 ASGI 框架
- FastAPI 应用本身就是 ASGI 应用，直接传给 Uvicorn 运行
- 开发用 `reload=True`，生产用 `workers=N` + `uvloop` + `httptools`
- FastAPI 自动生成 OpenAPI 文档，访问 `/docs` 和 `/redoc`
- 异步数据库推荐 SQLAlchemy 2.0 + asyncpg（PostgreSQL）或 aiomysql（MySQL）
- 生产环境配合 Gunicorn 管理 Uvicorn Worker 更稳定（见第22讲）

---

## 第15讲 Uvicorn + Starlette 集成

### 概念

**Starlette** 是轻量级 ASGI 框架/工具包，由 Tom Christie 开发，也是 FastAPI 的底层基础。它提供路由、中间件、请求/响应对象、WebSocket 等核心功能，但没有数据验证、自动文档等高级特性。适合需要精细控制或构建自定义框架的场景。

### 原理

**Starlette 的定位**：

```
功能丰富 ◀──────────────────────────────▶ 极简控制
FastAPI  ──▶  Starlette  ──▶  原生 ASGI
(验证+文档)   (路由+中间件)    (scope/recv/send)
```

**Starlette 核心组件**：
- `Starlette`：主应用类，组合路由和中间件
- `Route` / `WebSocketRoute`：路由定义
- `Mount`：子应用挂载
- `Middleware`：中间件
- `Request` / `Response`：请求响应对象
- `TestClient`：测试客户端

### 例子

**示例 1：基本 Starlette 应用**

```python
# starlette_basic.py
from starlette.applications import Starlette
from starlette.routing import Route
from starlette.responses import JSONResponse
import uvicorn

async def homepage(request):
    return JSONResponse({"hello": "world"})

async def user(request):
    user_id = request.path_params["user_id"]
    return JSONResponse({"user_id": user_id})

routes = [
    Route("/", homepage),
    Route("/users/{user_id}", user),
]

app = Starlette(routes=routes)

if __name__ == "__main__":
    uvicorn.run("starlette_basic:app", host="0.0.0.0", port=8000, reload=True)
```

**示例 2：中间件**

```python
# starlette_middleware.py
from starlette.applications import Starlette
from starlette.routing import Route
from starlette.responses import JSONResponse
from starlette.middleware import Middleware
from starlette.middleware.cors import CORSMiddleware
from starlette.middleware.gzip import GZipMiddleware
from starlette.middleware.trustedhost import TrustedHostMiddleware
import time
import uvicorn

# 自定义中间件
class TimingMiddleware:
    def __init__(self, app):
        self.app = app

    async def __call__(self, scope, receive, send):
        if scope["type"] != "http":
            await self.app(scope, receive, send)
            return

        start = time.time()
        
        async def send_wrapper(message):
            if message["type"] == "http.response.start":
                headers = list(message.get("headers", []))
                elapsed = f"{(time.time() - start) * 1000:.2f}ms"
                headers.append((b"x-response-time", elapsed.encode()))
                message["headers"] = headers
            await send(message)
        
        await self.app(scope, receive, send_wrapper)


async def homepage(request):
    return JSONResponse({"message": "Hello"})

routes = [Route("/", homepage)]

middleware = [
    Middleware(TimingMiddleware),
    Middleware(GZipMiddleware, minimum_size=1000),
    Middleware(
        CORSMiddleware,
        allow_origins=["*"],
        allow_methods=["*"],
        allow_headers=["*"],
    ),
    Middleware(TrustedHostMiddleware, allowed_hosts=["localhost", "example.com"]),
]

app = Starlette(routes=routes, middleware=middleware)

if __name__ == "__main__":
    uvicorn.run("starlette_middleware:app", port=8000, reload=True)
```

**示例 3：WebSocket 聊天**

```python
# starlette_websocket.py
from starlette.applications import Starlette
from starlette.routing import Route, WebSocketRoute
from starlette.websockets import WebSocket
import uvicorn

connected_clients = set()

async def websocket_endpoint(websocket: WebSocket):
    await websocket.accept()
    connected_clients.add(websocket)
    
    try:
        while True:
            message = await websocket.receive_text()
            # 广播给所有客户端
            for client in connected_clients:
                await client.send_text(f"User: {message}")
    except Exception:
        connected_clients.remove(websocket)

routes = [
    WebSocketRoute("/ws", websocket_endpoint),
]

app = Starlette(routes=routes)

if __name__ == "__main__":
    uvicorn.run("starlette_websocket:app", port=8000, reload=True)
```

**示例 4：子应用挂载（Mount）**

```python
# starlette_mount.py
from starlette.applications import Starlette
from starlette.routing import Route, Mount
from starlette.responses import JSONResponse, PlainTextResponse
import uvicorn

# 主应用路由
async def homepage(request):
    return JSONResponse({"app": "main"})

# API 子应用
api_routes = [
    Route("/users", lambda req: JSONResponse({"users": []})),
    Route("/posts", lambda req: JSONResponse({"posts": []})),
]
api_app = Starlette(routes=api_routes)

# 静态文件子应用
from starlette.staticfiles import StaticFiles
static_app = StaticFiles(directory="./static")

# 主应用挂载子应用
routes = [
    Route("/", homepage),
    Mount("/api", app=api_app),          # /api/users, /api/posts
    Mount("/static", app=static_app),    # /static/...
]

app = Starlette(routes=routes)

if __name__ == "__main__":
    uvicorn.run("starlette_mount:app", port=8000, reload=True)
```

### 总结

- Starlette 是轻量级 ASGI 框架，提供路由、中间件、请求响应等核心功能
- 适合需要精细控制、构建自定义框架、或不需要 FastAPI 高级特性的场景
- 中间件通过 `Middleware` 类配置，支持自定义和内置（CORS、GZip、TrustedHost）
- `Mount` 可挂载子应用，实现模块化架构
- Starlette 是 FastAPI 的基础，学习它有助于理解 FastAPI 底层
- WebSocket 通过 `WebSocketRoute` 定义，`WebSocket` 对象提供 accept/receive/send

---

## 第16讲 Uvicorn + Django（ASGI 模式）

### 概念

**Django** 从 3.0 开始支持 ASGI，允许异步视图和中间件。Django 4.0+ 进一步增强了 ASGI 支持。通过 Uvicorn 运行 Django，可以利用异步特性处理 WebSocket、长轮询、外部 API 调用等场景。

### 原理

**Django ASGI 应用结构**：

```
Django 项目
└── myproject/
    ├── __init__.py
    ├── asgi.py      ← ASGI 入口
    ├── settings.py
    ├── urls.py
    └── wsgi.py      ← WSGI 入口（传统）
```

**asgi.py 的内容**：

```python
# myproject/asgi.py
import os
from django.core.asgi import get_asgi_application

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "myproject.settings")

application = get_asgi_application()
```

`get_asgi_application()` 返回一个 ASGI 应用，Uvicorn 可以直接运行它。

**Django ASGI 的限制**：
- 数据库 ORM 默认是同步的（Django 4.1+ 支持异步 ORM）
- 同步代码会阻塞事件循环，需用 `sync_to_async` 包装
- 第三方包可能不支持异步

### 例子

**示例 1：创建 Django 项目并配置 ASGI**

```bash
# 安装 Django
pip install django uvicorn

# 创建项目
django-admin startproject myproject
cd myproject

# 创建应用
python manage.py startapp myapp
```

```python
# myproject/settings.py
INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
    "myapp",  # 添加应用
]

# 允许的 host
ALLOWED_HOSTS = ["*"]
```

**示例 2：异步视图**

```python
# myapp/views.py
import asyncio
import httpx
from django.http import JsonResponse

async def async_view(request):
    """异步视图示例"""
    await asyncio.sleep(0.1)  # 模拟异步操作
    return JsonResponse({"message": "Async response"})

async def fetch_external_api(request):
    """异步调用外部 API"""
    async with httpx.AsyncClient() as client:
        response = await client.get("https://api.github.com/users/python")
        data = response.json()
    return JsonResponse({"github_user": data["login"]})

async def concurrent_requests(request):
    """并发请求多个 API"""
    async with httpx.AsyncClient() as client:
        # 并发请求 3 个 API
        results = await asyncio.gather(
            client.get("https://httpbin.org/delay/1"),
            client.get("https://httpbin.org/delay/1"),
            client.get("https://httpbin.org/delay/1"),
        )
    return JsonResponse({"count": len(results), "total_time": "~1s instead of 3s"})
```

```python
# myproject/urls.py
from django.contrib import admin
from django.urls import path
from myapp.views import async_view, fetch_external_api, concurrent_requests

urlpatterns = [
    path("admin/", admin.site.urls),
    path("async/", async_view),
    path("fetch/", fetch_external_api),
    path("concurrent/", concurrent_requests),
]
```

**示例 3：使用 Uvicorn 运行 Django**

```bash
# 方式 1：命令行
uvicorn myproject.asgi:application --host 0.0.0.0 --port 8000 --workers 4

# 方式 2：Django 的 runserver（开发用）
python manage.py runserver
# Django 3.0+ 默认用 ASGI 运行

# 方式 3：Python 脚本
```

```python
# run_django.py
import uvicorn

if __name__ == "__main__":
    uvicorn.run(
        "myproject.asgi:application",
        host="0.0.0.0",
        port=8000,
        workers=4,
        loop="uvloop",
        http="httptools",
        log_level="info",
    )
```

**示例 4：Django Channels（WebSocket 支持）**

```bash
pip install channels
```

```python
# myproject/settings.py
INSTALLED_APPS = [
    "daphne",  # 必须在第一位
    "channels",
    # ... 其他应用
]

ASGI_APPLICATION = "myproject.asgi.application"
```

```python
# myproject/asgi.py
import os
from django.core.asgi import get_asgi_application
from channels.routing import ProtocolTypeRouter, URLRouter
from channels.auth import AuthMiddlewareStack

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "myproject.settings")

django_asgi_app = get_asgi_application()

from myapp.routing import websocket_urlpatterns

application = ProtocolTypeRouter({
    "http": django_asgi_app,
    "websocket": AuthMiddlewareStack(URLRouter(websocket_urlpatterns)),
})
```

```python
# myapp/consumers.py
import json
from channels.generic.websocket import AsyncWebsocketConsumer

class ChatConsumer(AsyncWebsocketConsumer):
    async def connect(self):
        self.room_name = self.scope["url_route"]["kwargs"]["room_name"]
        self.room_group_name = f"chat_{self.room_name}"
        
        await self.channel_layer.group_add(self.room_group_name, self.channel_name)
        await self.accept()
    
    async def disconnect(self, close_code):
        await self.channel_layer.group_discard(self.room_group_name, self.channel_name)
    
    async def receive(self, text_data):
        text_data_json = json.loads(text_data)
        message = text_data_json["message"]
        
        await self.channel_layer.group_send(
            self.room_group_name,
            {"type": "chat_message", "message": message}
        )
    
    async def chat_message(self, event):
        message = event["message"]
        await self.send(text_data=json.dumps({"message": message}))
```

```python
# myapp/routing.py
from django.urls import path
from .consumers import ChatConsumer

websocket_urlpatterns = [
    path("ws/chat/<str:room_name>/", ChatConsumer.as_asgi()),
]
```

### 总结

- Django 3.0+ 支持 ASGI，通过 `asgi.py` 的 `get_asgi_application()` 提供 ASGI 入口
- 异步视图用 `async def` 定义，可使用 `asyncio`、`httpx` 等异步库
- Django ORM 默认同步，4.1+ 支持异步版本（`aget()`、`acreate()` 等）
- 同步代码需用 `sync_to_async` 包装，避免阻塞事件循环
- WebSocket 需安装 Django Channels，通过 `ProtocolTypeRouter` 分发
- 生产环境推荐：Uvicorn + Django ASGI + Redis（Channels 后端）

---

## 第17讲 Uvicorn + Flask（asgiref 桥接）

### 概念

**Flask** 是同步 WSGI 框架，本身不支持 ASGI。但通过 `asgiref` 库的 `WsgiToAsgi` 适配器，可以将 WSGI 应用包装为 ASGI 应用，从而在 Uvicorn 上运行。这种方式适合已有 Flask 项目想体验 ASGI 服务器，但不提供真正的异步能力。

### 原理

**WSGI 到 ASGI 的桥接**：

```
Uvicorn（ASGI 服务器）
    │
    │ ASGI 协议（异步）
    ▼
WsgiToAsgi 适配器（asgiref）
    │
    │ WSGI 协议（同步）
    ▼
Flask 应用（WSGI）
```

**WsgiToAsgi 的工作原理**：
1. 接收 ASGI 的 scope、receive、send
2. 将 scope 转换为 WSGI 的 environ 字典
3. 在线程池中执行 WSGI 应用（避免阻塞事件循环）
4. 将 WSGI 响应转换回 ASGI 消息发送

**重要限制**：
- Flask 代码仍然是同步的，无法使用 `async`/`await`
- 每个请求在线程池中执行，性能不如原生 ASGI
- 不支持 WebSocket（Flask 本身不支持）
- 适合过渡期，新项目推荐用 FastAPI 或 Quart

### 例子

**示例 1：基本 Flask + Uvicorn**

```bash
pip install flask asgiref uvicorn
```

```python
# flask_asgi.py
from flask import Flask, jsonify, request
from asgiref.wsgi import WsgiToAsgi

app = Flask(__name__)

@app.route("/")
def root():
    return jsonify({"message": "Hello from Flask via Uvicorn!"})

@app.route("/users/<int:user_id>")
def get_user(user_id):
    return jsonify({"user_id": user_id, "name": f"User {user_id}"})

@app.route("/echo", methods=["POST"])
def echo():
    data = request.get_json()
    return jsonify({"received": data})

# 关键：用 WsgiToAsgi 包装
asgi_app = WsgiToAsgi(app)
```

运行：
```bash
uvicorn flask_asgi:asgi_app --host 0.0.0.0 --port 8000 --workers 4

# 测试
curl http://localhost:8000/
curl http://localhost:8000/users/123
curl -X POST -H "Content-Type: application/json" -d '{"key":"value"}' http://localhost:8000/echo
```

**示例 2：Flask + 异步任务（不推荐，仅演示）**

```python
# flask_async_workaround.py
from flask import Flask, jsonify
from asgiref.wsgi import WsgiToAsgi
from asgiref.sync import async_to_sync
import asyncio
import httpx

app = Flask(__name__)

async def fetch_data():
    """异步函数"""
    async with httpx.AsyncClient() as client:
        response = await client.get("https://httpbin.org/get")
        return response.json()

@app.route("/async-data")
def async_data():
    # 在同步 Flask 视图中调用异步函数
    data = async_to_sync(fetch_data)()
    return jsonify({"data": data})

asgi_app = WsgiToAsgi(app)
```

> **注意**：这种方式性能不如原生 ASGI，仅作为过渡方案。

**示例 3：Quart - 真正的异步 Flask**

如果需要 Flask 风格的异步框架，推荐使用 **Quart**，它是 Flask 的异步版本，API 几乎完全兼容。

```bash
pip install quart uvicorn
```

```python
# quart_app.py
from quart import Quart, jsonify, request
import asyncio
import httpx

app = Quart(__name__)

@app.route("/")
async def root():
    return jsonify({"message": "Hello from Quart!"})

@app.route("/fetch")
async def fetch():
    async with httpx.AsyncClient() as client:
        response = await client.get("https://httpbin.org/get")
        data = response.json()
    return jsonify({"data": data})

@app.route("/concurrent")
async def concurrent():
    async with httpx.AsyncClient() as client:
        results = await asyncio.gather(
            client.get("https://httpbin.org/delay/1"),
            client.get("https://httpbin.org/delay/1"),
        )
    return jsonify({"count": len(results)})

# 直接运行，无需 WsgiToAsgi
# uvicorn quart_app:app --port 8000
```

**示例 4：迁移 Flask 到 FastAPI**

```python
# 迁移前：Flask
from flask import Flask, request, jsonify
app = Flask(__name__)

@app.route("/users", methods=["POST"])
def create_user():
    data = request.get_json()
    return jsonify({"created": data}), 201
```

```python
# 迁移后：FastAPI（语法相似）
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class User(BaseModel):
    name: str
    email: str

@app.post("/users", status_code=201)
async def create_user(user: User):
    return {"created": user}
```

### 总结

- Flask 是同步 WSGI 框架，通过 `asgiref.WsgiToAsgi` 可在 Uvicorn 运行
- 桥接方式不提供真正的异步能力，每个请求在线程池中执行
- 适合已有 Flask 项目的过渡期，新项目不推荐
- 需要异步 + Flask 风格，推荐用 Quart（API 兼容 Flask）
- 需要异步 + 现代特性，推荐迁移到 FastAPI
- Flask 2.0+ 支持 `async def` 视图，但底层仍是 WSGI，性能有限

---

# 第5章 异步与并发

本章深入 Uvicorn 的异步与并发机制。我们将讲解 uvloop 事件循环、httptools HTTP 解析器、WebSocket 支持以及长连接管理。理解这些底层组件是进行性能调优和排查并发问题的基础。

---

## 第18讲 uvloop 与事件循环

### 概念

**uvloop** 是 asyncio 事件循环的 C 实现，基于 libuv（Node.js 的事件循环库）。它完全替代 Python 标准库的 asyncio 事件循环，性能提升 2-4 倍，是 Uvicorn 高性能的关键组件之一。

### 原理

**asyncio 事件循环的作用**：

事件循环是异步编程的核心，负责：
1. 管理 I/O 多路复用（epoll/kqueue）
2. 调度协程（coroutine）的执行
3. 处理定时器（call_later）
4. 管理任务（Task）和 Future

**uvloop vs asyncio 性能对比**：

```
性能基准（req/s，越高越好）
┌─────────────────────────────────────────────┐
│ asyncio（标准库）  ████████████████  ~15000  │
│ uvloop             ██████████████████████  ~35000  │
│                     +130% 性能提升           │
└─────────────────────────────────────────────┘
```

**uvloop 的优势**：
- C 实现，避免 Python 解释器开销
- 基于 libuv，经过 Node.js 大规模生产验证
- 完全兼容 asyncio API，无需修改代码
- 更高效的 I/O 多路复用和回调调度

**Uvicorn 的事件循环选择**：

| `loop` 参数 | 说明 | 适用场景 |
|-------------|------|----------|
| `auto`（默认） | 自动选择，优先 uvloop | 通用 |
| `uvloop` | 强制使用 uvloop | 生产环境（Linux/macOS） |
| `asyncio` | 使用标准库 asyncio | Windows 或兼容性要求 |

**注意**：uvloop 不支持 Windows，在 Windows 上会自动回退到 asyncio。

### 例子

**示例 1：对比 uvloop 和 asyncio 性能**

```python
# loop_benchmark.py
"""简单的事件循环性能对比"""
import asyncio
import time
import uvloop

async def io_task():
    """模拟 I/O 操作"""
    await asyncio.sleep(0)
    return 1

async def run_tasks(n=100000):
    """并发执行 n 个任务"""
    tasks = [io_task() for _ in range(n)]
    results = await asyncio.gather(*tasks)
    return sum(results)

def benchmark(loop_name, loop=None):
    if loop:
        asyncio.set_event_loop_policy(loop)
    
    start = time.time()
    result = asyncio.run(run_tasks())
    elapsed = time.time() - start
    
    print(f"{loop_name}: {elapsed:.3f}s, result={result}")
    return elapsed

if __name__ == "__main__":
    # 测试 asyncio
    t1 = benchmark("asyncio", asyncio.DefaultEventLoopPolicy())
    
    # 测试 uvloop
    t2 = benchmark("uvloop", uvloop.EventLoopPolicy())
    
    print(f"uvloop 比 asyncio 快 {t1/t2:.2f} 倍")
```

**示例 2：Uvicorn 指定事件循环**

```bash
# 自动选择（推荐）
uvicorn main:app --loop auto

# 强制 uvloop
uvicorn main:app --loop uvloop

# 使用标准库 asyncio
uvicorn main:app --loop asyncio
```

```python
# Python API 方式
import uvicorn

uvicorn.run(
    "main:app",
    loop="uvloop",  # 强制使用 uvloop
    workers=4,
)
```

**示例 3：手动设置 uvloop（不通过 Uvicorn）**

```python
# manual_uvloop.py
import asyncio
import uvloop

# 设置 uvloop 为默认事件循环
asyncio.set_event_loop_policy(uvloop.EventLoopPolicy())

async def main():
    print(f"事件循环类型: {type(asyncio.get_event_loop())}")
    # 输出: 事件循环类型: <class 'uvloop.Loop'>

asyncio.run(main())
```

**示例 4：验证 Uvicorn 使用的事件循环**

```python
# verify_loop.py
import asyncio
import uvicorn

async def app(scope, receive, send):
    loop = asyncio.get_event_loop()
    loop_type = type(loop).__name__
    
    body = f"Event Loop: {loop_type}".encode()
    
    await send({
        "type": "http.response.start",
        "status": 200,
        "headers": [[b"content-type", b"text/plain"]],
    })
    await send({"type": "http.response.body", "body": body})

if __name__ == "__main__":
    uvicorn.run("verify_loop:app", loop="uvloop")

# 访问 http://localhost:8000/ 会显示:
# Event Loop: Loop  (uvloop)
# 或
# Event Loop: _UnixSelectorEventLoop  (asyncio)
```

**示例 5：uvloop 与 asyncio 的兼容性**

```python
# uvloop_compatibility.py
import asyncio
import uvloop

asyncio.set_event_loop_policy(uvloop.EventLoopPolicy())

# 所有 asyncio API 都兼容
async def main():
    # 定时器
    asyncio.get_event_loop().call_later(1, lambda: print("Timer fired"))
    
    # 任务
    task = asyncio.create_task(asyncio.sleep(1))
    await task
    
    # 队列
    queue = asyncio.Queue()
    await queue.put("item")
    item = await queue.get()
    print(f"Got: {item}")
    
    # 锁
    lock = asyncio.Lock()
    async with lock:
        print("In critical section")

asyncio.run(main())
```

### 总结

- uvloop 是 asyncio 事件循环的 C 实现，性能提升 2-4 倍
- Uvicorn 默认 `loop=auto` 会自动选择 uvloop（如果已安装）
- 生产环境推荐显式指定 `loop=uvloop`（Linux/macOS）
- uvloop 不支持 Windows，会自动回退到 asyncio
- uvloop 完全兼容 asyncio API，无需修改业务代码
- 安装：`pip install uvicorn[standard]` 会自动安装 uvloop

---

## 第19讲 httptools 与 HTTP 解析

### 概念

**httptools** 是 Node.js HTTP 解析器（http-parser）的 Python 绑定，由 Python 标准库的 http.parser 替代品。它是 C 实现的高性能 HTTP 协议解析器，比纯 Python 实现的 h11 快 3-5 倍。Uvicorn 默认使用 httptools 解析 HTTP 请求。

### 原理

**HTTP 请求解析过程**：

```
客户端发送的原始字节流:
b"GET /api/users?name=alice HTTP/1.1\r\nHost: localhost\r\nUser-Agent: curl\r\n\r\n"

           │
           ▼
┌─────────────────────────┐
│   httptools 解析器       │
│   (C 实现，增量解析)      │
└───────────┬─────────────┘
            │
            ▼
回调函数触发:
- on_method("GET")
- on_url("/api/users?name=alice")
- on_header("Host", "localhost")
- on_header("User-Agent", "curl")
- on_message_complete()
            │
            ▼
Uvicorn 构造 ASGI scope
```

**httptools vs h11 对比**：

| 特性 | httptools | h11 |
|------|-----------|-----|
| 实现语言 | C（http-parser） | 纯 Python |
| 性能 | 极快（基准） | 较慢（慢 3-5 倍） |
| 内存 | 低 | 较高 |
| 兼容性 | 需编译 | 纯 Python，无需编译 |
| HTTP/2 | 不支持 | 不支持 |
| 调试 | 困难 | 容易（可读源码） |

**Uvicorn 的 HTTP 解析器选择**：

| `http` 参数 | 说明 | 适用场景 |
|-------------|------|----------|
| `auto`（默认） | 自动选择，优先 httptools | 通用 |
| `httptools` | 强制使用 httptools | 生产环境 |
| `h11` | 使用纯 Python h11 | 调试或无 C 编译器环境 |

### 例子

**示例 1：安装与验证**

```bash
# 安装完整版（包含 httptools、uvloop 等）
pip install "uvicorn[standard]"

# 验证 httptools 是否可用
python -c "import httptools; print('httptools version:', httptools.__version__)"
```

**示例 2：指定 HTTP 解析器**

```bash
# 使用 httptools（推荐）
uvicorn main:app --http httptools

# 使用 h11（调试用）
uvicorn main:app --http h11

# 自动选择
uvicorn main:app --http auto
```

**示例 3：手动使用 httptools 解析 HTTP**

```python
# httptools_demo.py
"""演示 httptools 如何解析 HTTP 请求"""
import httptools

class HttpRequestParser:
    """简单的 HTTP 请求解析器"""
    
    def __init__(self):
        self.method = None
        self.url = None
        self.headers = {}
        self.body = b""
        self.complete = False
    
    def on_message_begin(self):
        print("消息开始")
    
    def on_url(self, url):
        self.url = url.decode()
        print(f"URL: {self.url}")
    
    def on_header(self, name, value):
        name = name.decode().lower()
        value = value.decode()
        self.headers[name] = value
        print(f"Header: {name}: {value}")
    
    def on_body(self, body):
        self.body += body
        print(f"Body chunk: {len(body)} bytes")
    
    def on_message_complete(self):
        self.complete = True
        print("消息完成")
    
    def on_method(self, method):
        self.method = method.decode()
        print(f"Method: {self.method}")


# 模拟 HTTP 请求字节流
http_request = (
    b"POST /api/users HTTP/1.1\r\n"
    b"Host: localhost:8000\r\n"
    b"Content-Type: application/json\r\n"
    b"Content-Length: 25\r\n"
    b"\r\n"
    b'{"name": "alice"}'
)

# 解析
parser = HttpRequestParser()
http_parser = httptools.HttpRequestParser(parser)

# 增量解析（可以分多次 feed）
http_parser.feed_data(http_request)

print(f"\n解析结果:")
print(f"  Method: {parser.method}")
print(f"  URL: {parser.url}")
print(f"  Headers: {parser.headers}")
print(f"  Body: {parser.body}")
```

**示例 4：流式解析大请求**

```python
# httptools_stream.py
"""演示 httptools 的流式解析能力"""
import httptools

class StreamingParser:
    def __init__(self):
        self.total_bytes = 0
        self.chunks = 0
    
    def on_body(self, body):
        self.chunks += 1
        self.total_bytes += len(body)
        # 可以在这里处理每个 chunk，不等待完整 body
        print(f"  Chunk {self.chunks}: {len(body)} bytes (total: {self.total_bytes})")
    
    def on_message_begin(self): pass
    def on_url(self, url): pass
    def on_header(self, name, value): pass
    def on_message_complete(self):
        print(f"完成: 共 {self.chunks} 个 chunk, {self.total_bytes} bytes")


parser = StreamingParser()
http_parser = httptools.HttpRequestParser(parser)

# 模拟分块到达的 HTTP 请求
header = (
    b"POST /upload HTTP/1.1\r\n"
    b"Content-Length: 1000\r\n"
    b"\r\n"
)
http_parser.feed_data(header)

# 模拟 body 分块到达
for i in range(10):
    chunk = b"x" * 100
    http_parser.feed_data(chunk)
```

**示例 5：性能对比**

```python
# http_parser_benchmark.py
"""对比 httptools 和 h11 的解析性能"""
import time
import httptools

http_request = (
    b"GET /api/users?name=alice&page=1 HTTP/1.1\r\n"
    b"Host: localhost:8000\r\n"
    b"User-Agent: Mozilla/5.0\r\n"
    b"Accept: application/json\r\n"
    b"Authorization: Bearer token123\r\n"
    b"\r\n"
)

class DummyParser:
    def on_message_begin(self): pass
    def on_url(self, url): pass
    def on_header(self, name, value): pass
    def on_message_complete(self): pass

def benchmark_httptools(n=100000):
    parser = DummyParser()
    http_parser = httptools.HttpRequestParser(parser)
    
    start = time.time()
    for _ in range(n):
        http_parser.feed_data(http_request)
    elapsed = time.time() - start
    print(f"httptools: {n} 次解析耗时 {elapsed:.3f}s ({n/elapsed:.0f} req/s)")

benchmark_httptools()
```

### 总结

- httptools 是 C 实现的高性能 HTTP 解析器，比 h11 快 3-5 倍
- Uvicorn 默认 `http=auto` 会优先使用 httptools
- 生产环境推荐显式指定 `http=httptools`
- httptools 支持增量/流式解析，适合大请求体
- h11 是纯 Python 实现，适合调试或无 C 编译器环境
- 安装 `uvicorn[standard]` 会自动安装 httptools

---

## 第20讲 WebSocket 支持

### 概念

**WebSocket** 是一种在单个 TCP 连接上进行全双工通信的协议。与 HTTP 的请求-响应模式不同，WebSocket 允许服务器主动推送数据给客户端，适合实时聊天、通知、股票行情等场景。Uvicorn 通过 `websockets` 或 `wsproto` 库提供 WebSocket 支持。

### 原理

**WebSocket 连接建立过程**：

```
客户端                              服务器
  │                                   │
  │── HTTP GET (Upgrade: websocket) ─▶│  HTTP 握手
  │                                   │
  │◀── HTTP 101 Switching Protocols ──│  同意升级
  │                                   │
  │═════════ WebSocket 双向通信 ═══════│
  │                                   │
  │── Frame (text/binary) ───────────▶│
  │◀── Frame (text/binary) ───────────│
  │◀── Frame (server push) ───────────│
  │                                   │
  │── Close Frame ───────────────────▶│  关闭
  │◀── Close Frame ───────────────────│
```

**WebSocket 消息类型**：
- `text`：UTF-8 文本消息
- `binary`：二进制消息
- `ping`/`pong`：心跳检测
- `close`：关闭连接

**Uvicorn 的 WebSocket 实现**：

| `ws` 参数 | 说明 | 特点 |
|-----------|------|------|
| `auto`（默认） | 自动选择 | 优先 websockets |
| `websockets` | 使用 websockets 库 | 纯 Python，功能丰富 |
| `wsproto` | 使用 wsproto 库 | 更严格遵循规范 |

**ASGI WebSocket 消息流**：

```
服务器 → 应用                    应用 → 服务器
─────────────────              ─────────────────
websocket.connect              websocket.accept
websocket.receive              websocket.send
websocket.disconnect           websocket.close
```

### 例子

**示例 1：原生 ASGI WebSocket**

```python
# websocket_asgi.py
async def app(scope, receive, send):
    if scope["type"] == "http":
        # HTTP 处理
        await send({"type": "http.response.start", "status": 200, "headers": []})
        await send({"type": "http.response.body", "body": b"Use WebSocket"})
        return
    
    if scope["type"] == "websocket":
        await handle_websocket(scope, receive, send)


async def handle_websocket(scope, receive, send):
    # 等待连接请求
    message = await receive()
    if message["type"] != "websocket.connect":
        return
    
    # 接受连接
    await send({"type": "websocket.accept"})
    
    # 消息循环
    try:
        while True:
            message = await receive()
            
            if message["type"] == "websocket.disconnect":
                print(f"客户端断开: code={message.get('code')}")
                break
            
            if message["type"] == "websocket.receive":
                if "text" in message:
                    # 文本消息
                    text = message["text"]
                    await send({
                        "type": "websocket.send",
                        "text": f"Echo: {text}",
                    })
                elif "bytes" in message:
                    # 二进制消息
                    data = message["bytes"]
                    await send({
                        "type": "websocket.send",
                        "bytes": data,
                    })
    except Exception as e:
        print(f"WebSocket 错误: {e}")
        await send({
            "type": "websocket.close",
            "code": 1011,  # Internal Error
        })
```

**示例 2：FastAPI WebSocket**

```python
# websocket_fastapi.py
from fastapi import FastAPI, WebSocket, WebSocketDisconnect
from fastapi.responses import HTMLResponse
import uvicorn

app = FastAPI()

html = """
<!DOCTYPE html>
<html>
<head><title>WebSocket Chat</title></head>
<body>
    <h1>WebSocket Chat</h1>
    <input id="message" type="text" placeholder="输入消息">
    <button onclick="sendMessage()">发送</button>
    <ul id="messages"></ul>
    
    <script>
        const ws = new WebSocket("ws://localhost:8000/ws");
        ws.onmessage = function(event) {
            const li = document.createElement("li");
            li.textContent = event.data;
            document.getElementById("messages").appendChild(li);
        };
        function sendMessage() {
            const input = document.getElementById("message");
            ws.send(input.value);
            input.value = "";
        }
    </script>
</body>
</html>
"""

@app.get("/")
async def get():
    return HTMLResponse(html)

@app.websocket("/ws")
async def websocket_endpoint(websocket: WebSocket):
    await websocket.accept()
    try:
        while True:
            data = await websocket.receive_text()
            await websocket.send_text(f"Message: {data}")
    except WebSocketDisconnect:
        print("Client disconnected")

if __name__ == "__main__":
    uvicorn.run("websocket_fastapi:app", host="0.0.0.0", port=8000)
```

**示例 3：广播服务器（聊天室）**

```python
# websocket_broadcast.py
from fastapi import FastAPI, WebSocket, WebSocketDisconnect
from typing import List
import asyncio
import uvicorn

app = FastAPI()

class ConnectionManager:
    """管理所有 WebSocket 连接"""
    
    def __init__(self):
        self.active_connections: List[WebSocket] = []
    
    async def connect(self, websocket: WebSocket):
        await websocket.accept()
        self.active_connections.append(websocket)
        await self.broadcast(f"用户加入，当前在线: {len(self.active_connections)}")
    
    def disconnect(self, websocket: WebSocket):
        self.active_connections.remove(websocket)
    
    async def broadcast(self, message: str):
        """广播给所有连接"""
        for connection in self.active_connections:
            try:
                await connection.send_text(message)
            except Exception:
                # 发送失败的连接移除
                self.active_connections.remove(connection)

manager = ConnectionManager()

@app.websocket("/ws/{username}")
async def websocket_endpoint(websocket: WebSocket, username: str):
    await manager.connect(websocket)
    try:
        while True:
            data = await websocket.receive_text()
            await manager.broadcast(f"{username}: {data}")
    except WebSocketDisconnect:
        manager.disconnect(websocket)
        await manager.broadcast(f"{username} 离开")

if __name__ == "__main__":
    uvicorn.run("websocket_broadcast:app", host="0.0.0.0", port=8000)
```

**示例 4：WebSocket 心跳检测**

```python
# websocket_heartbeat.py
import asyncio
from fastapi import FastAPI, WebSocket
import uvicorn

app = FastAPI()

@app.websocket("/ws")
async def websocket_endpoint(websocket: WebSocket):
    await websocket.accept()
    
    # 心跳任务
    async def heartbeat():
        while True:
            await asyncio.sleep(30)  # 每 30 秒发送 ping
            try:
                await websocket.send_json({"type": "ping"})
            except Exception:
                break
    
    # 启动心跳
    heartbeat_task = asyncio.create_task(heartbeat())
    
    try:
        while True:
            data = await websocket.receive_json()
            
            if data.get("type") == "pong":
                continue  # 心跳响应，忽略
            
            # 处理业务消息
            await websocket.send_json({"type": "response", "data": data})
    except Exception:
        pass
    finally:
        heartbeat_task.cancel()

if __name__ == "__main__":
    uvicorn.run("websocket_heartbeat:app", port=8000)
```

### 总结

- WebSocket 提供全双工通信，适合实时应用
- Uvicorn 通过 `websockets` 或 `wsproto` 库支持 WebSocket
- ASGI WebSocket 消息流：connect → accept → receive/send → disconnect
- 生产环境需实现心跳检测，避免连接僵死
- 多 Worker 模式下，WebSocket 连接分布在不同进程，广播需用 Redis Pub/Sub
- WebSocket 连接是长连接，会占用 Worker 资源，需控制最大连接数

---

## 第21讲 长连接与连接管理

### 概念

**长连接（Keep-Alive）** 是 HTTP/1.1 的默认特性，允许一个 TCP 连接处理多个 HTTP 请求，避免重复的 TCP 握手开销。Uvicorn 通过 `--timeout-keep-alive` 参数控制长连接超时。本讲讲解长连接的工作机制、连接管理和性能影响。

### 原理

**短连接 vs 长连接**：

```
短连接（HTTP/1.0 默认）:
请求1: TCP握手 → 请求 → 响应 → TCP关闭
请求2: TCP握手 → 请求 → 响应 → TCP关闭
请求3: TCP握手 → 请求 → 响应 → TCP关闭
（3 次 TCP 握手）

长连接（HTTP/1.1 默认）:
TCP握手 → 请求1 → 响应1 → 请求2 → 响应2 → 请求3 → 响应3 → TCP关闭
（1 次 TCP 握手）
```

**Keep-Alive 的工作流程**：

1. 客户端发送请求，带 `Connection: keep-alive` 头
2. 服务器响应后，不关闭 TCP 连接
3. 连接保持空闲状态，等待下一个请求
4. 超过 `timeout-keep-alive` 秒无新请求，关闭连接
5. 客户端也可主动发送 `Connection: close` 关闭

**连接池容量**：

每个 Worker 进程能同时维护的连接数受以下因素限制：
- 文件描述符限制（`ulimit -n`）
- 内存（每个连接约 50-100KB）
- `--limit-concurrency` 参数

**性能影响**：

```
短连接开销:
- TCP 握手: ~1ms（本地）, ~50ms（跨地域）
- TLS 握手: ~50-200ms（如果用 HTTPS）
- 总开销: 请求越多，开销越大

长连接收益:
- 100 个请求复用 1 个连接: 节省 99 次握手
- 适合 API 高频调用、微服务通信
```

### 例子

**示例 1：观察 Keep-Alive 行为**

```python
# keepalive_observe.py
import asyncio
import time

async def app(scope, receive, send):
    if scope["type"] != "http":
        return
    
    client = scope["client"]
    print(f"[{time.strftime('%H:%M:%S')}] 请求来自 {client}, path={scope['path']}")
    
    await send({
        "type": "http.response.start",
        "status": 200,
        "headers": [
            [b"content-type", b"text/plain"],
            [b"connection", b"keep-alive"],
        ],
    })
    await send({"type": "http.response.body", "body": b"OK"})
```

测试：
```bash
uvicorn keepalive_observe:app --port 8000 --timeout-keep-alive 10

# 使用 curl 复用连接发送 3 个请求
curl http://localhost:8000/a http://localhost:8000/b http://localhost:8000/c

# 服务器日志:
# [14:30:01] 请求来自 ['127.0.0.1', 54321], path=/a
# [14:30:01] 请求来自 ['127.0.0.1', 54321], path=/b  (同一端口，复用连接)
# [14:30:01] 请求来自 ['127.0.0.1', 54321], path=/c

# 10 秒后，连接被关闭
```

**示例 2：调整 Keep-Alive 超时**

```bash
# 默认 5 秒
uvicorn main:app --port 8000

# 延长到 60 秒（适合低频但持续的客户端）
uvicorn main:app --port 8000 --timeout-keep-alive 60

# 关闭 Keep-Alive（每个请求后关闭连接）
uvicorn main:app --port 8000 --timeout-keep-alive 0
```

```python
# Python API
import uvicorn

uvicorn.run(
    "main:app",
    timeout_keep_alive=65,  # 配合 Nginx 的 keepalive_timeout（通常 65）
)
```

**示例 3：限制并发连接数**

```python
# limit_connections.py
import uvicorn

uvicorn.run(
    "main:app",
    limit_concurrency=1000,  # 最大并发连接数
    backlog=2048,            # TCP 连接队列大小
)
```

当并发连接数超过 `limit_concurrency` 时，Uvicorn 会返回 503 Service Unavailable。

**示例 4：监控连接状态**

```python
# connection_monitor.py
import asyncio
import time
from fastapi import FastAPI, Request
import uvicorn

app = FastAPI()

# 全局连接计数
active_connections = 0
total_requests = 0

@app.middleware("http")
async def monitor(request: Request, call_next):
    global active_connections, total_requests
    active_connections += 1
    total_requests += 1
    start = time.time()
    
    try:
        response = await call_next(request)
        return response
    finally:
        active_connections -= 1
        elapsed = time.time() - start
        print(f"Active: {active_connections}, Total: {total_requests}, "
              f"Path: {request.url.path}, Time: {elapsed:.3f}s")

@app.get("/")
async def root():
    await asyncio.sleep(0.1)  # 模拟处理
    return {"status": "ok"}

@app.get("/stats")
async def stats():
    return {
        "active_connections": active_connections,
        "total_requests": total_requests,
    }

if __name__ == "__main__":
    uvicorn.run("connection_monitor:app", port=8000)
```

**示例 5：配合 Nginx 的 Keep-Alive**

```nginx
# nginx.conf
upstream backend {
    server 127.0.0.1:8000;
    keepalive 32;  # 保持 32 个到后端的长连接
}

server {
    listen 80;
    
    location / {
        proxy_pass http://backend;
        proxy_http_version 1.1;
        proxy_set_header Connection "";  # 清除 Connection 头，启用 keep-alive
    }
}
```

```python
# Uvicorn 配置
import uvicorn

uvicorn.run(
    "main:app",
    timeout_keep_alive=65,  # 略大于 Nginx 的 keepalive_timeout
)
```

### 总结

- 长连接（Keep-Alive）复用 TCP 连接，显著减少握手开销
- `--timeout-keep-alive` 控制空闲连接超时，默认 5 秒
- 配合反向代理时，Uvicorn 的超时应略大于代理的超时
- `--limit-concurrency` 限制最大并发连接，防止过载
- 长连接会占用 Worker 资源，需平衡连接数和资源
- WebSocket 是特殊的长连接，生命周期由应用控制，不受 keep-alive 超时影响

---

# 第6章 部署实战

本章是 Uvicorn 教程的核心实战部分。我们将讲解生产环境的各种部署方案，包括 Gunicorn + Uvicorn 组合、Nginx 反向代理、Docker 容器化、systemd 服务管理以及 Kubernetes 部署。掌握这些方案是上线 Uvicorn 应用的必备技能。

---

## 第22讲 Gunicorn + Uvicorn 部署模式

### 概念

**Gunicorn** 是成熟的 Python WSGI HTTP 服务器，提供强大的进程管理能力。通过 `uvicorn-worker` 包，Gunicorn 可以运行 Uvicorn Worker，结合 Gunicorn 的稳定进程管理和 Uvicorn 的 ASGI 高性能。这是 FastAPI 官方推荐的生产部署方式。

### 原理

**为什么用 Gunicorn + Uvicorn**：

```
纯 Uvicorn 多 Worker:
- Master 进程是 Uvicorn 自己实现的
- 功能相对简单
- Worker 崩溃会重启，但 Master 崩溃则全部挂掉

Gunicorn + Uvicorn Worker:
- Master 进程是 Gunicorn（经过 10+ 年生产验证）
- Worker 是 UvicornWorker（ASGI 高性能）
- Gunicorn 提供成熟的进程管理、信号处理、优雅重启
- 支持 preload、热重启（SIGHUP）、零停机部署
```

**架构对比**：

```
方案 A：纯 Uvicorn
┌───────────────────┐
│ Uvicorn Master    │
│  └─ Uvicorn Worker│
│  └─ Uvicorn Worker│
│  └─ Uvicorn Worker│
└───────────────────┘

方案 B：Gunicorn + Uvicorn Worker（推荐）
┌───────────────────┐
│ Gunicorn Master   │  ← 成熟的进程管理
│  └─ UvicornWorker │  ← ASGI 高性能
│  └─ UvicornWorker │
│  └─ UvicornWorker │
└───────────────────┘
```

**Gunicorn 的优势**：
- 10+ 年生产验证，极其稳定
- 支持平滑重启（`kill -HUP`），零停机更新代码
- 支持 preload 模式，减少内存占用
- 丰富的 Worker 类型（sync、gevent、uvicorn）
- 完善的信号处理和日志

**UvicornWorker 的作用**：
- 将 Uvicorn 的 ASGI 能力封装为 Gunicorn Worker
- 继承 Uvicorn 的 uvloop、httptools 等高性能组件
- 支持 WebSocket、Lifespan 等 ASGI 特性

### 例子

**示例 1：安装与基本使用**

```bash
# 安装 gunicorn 和 uvicorn worker
pip install gunicorn uvicorn[standard]

# 启动（-k 指定 worker 类）
gunicorn main:app \
    -w 4 \
    -k uvicorn.workers.UvicornWorker \
    -b 0.0.0.0:8000
```

**示例 2：Gunicorn 配置文件**

```python
# gunicorn_config.py
"""Gunicorn 生产配置"""
import multiprocessing

# 基本配置
bind = "0.0.0.0:8000"                    # 监听地址
workers = multiprocessing.cpu_count() * 2 + 1  # Worker 数量
worker_class = "uvicorn.workers.UvicornWorker"  # Worker 类型
timeout = 120                            # Worker 处理请求超时
graceful_timeout = 30                    # 优雅关闭超时
keepalive = 5                            # keep-alive 超时

# 进程管理
preload_app = True                       # 预加载应用（节省内存）
max_requests = 1000                      # 每个 Worker 处理 1000 请求后重启
max_requests_jitter = 50                 # 随机抖动，避免同时重启
daemon = False                           # 是否后台运行
pidfile = "/tmp/gunicorn.pid"            # PID 文件

# 日志
accesslog = "-"                          # 访问日志（- 表示 stdout）
errorlog = "-"                           # 错误日志
loglevel = "info"                        # 日志级别
logconfig_dict = {}                      # 详细日志配置

# SSL（通常由 Nginx 处理，这里不配置）
# keyfile = "/path/to/key.pem"
# certfile = "/path/to/cert.pem"

# Uvicorn 特有配置（通过 worker_class 传递）
uvloop = True                            # 使用 uvloop
httptools = True                         # 使用 httptools
```

启动：
```bash
gunicorn main:app -c gunicorn_config.py
```

**示例 3：FastAPI 生产部署完整示例**

```python
# app.py
from fastapi import FastAPI
import uvicorn

app = FastAPI(title="Production API")

@app.get("/")
async def root():
    return {"status": "ok", "service": "myapp"}

@app.get("/health")
async def health():
    return {"status": "healthy"}

if __name__ == "__main__":
    # 开发模式
    uvicorn.run("app:app", host="0.0.0.0", port=8000, reload=True)
```

```bash
# 生产启动
gunicorn app:app \
    -w 4 \
    -k uvicorn.workers.UvicornWorker \
    -b 0.0.0.0:8000 \
    --timeout 120 \
    --graceful-timeout 30 \
    --max-requests 1000 \
    --max-requests-jitter 50 \
    --access-logfile - \
    --error-logfile -
```

**示例 4：平滑重启（零停机更新）**

```bash
# 启动 Gunicorn
gunicorn app:app -w 4 -k uvicorn.workers.UvicornWorker -b 0.0.0.0:8000 --pid /tmp/gunicorn.pid

# 更新代码后，发送 SIGHUP 信号
kill -HUP $(cat /tmp/gunicorn.pid)

# Gunicorn 会逐个重启 Worker，期间服务不中断
# 日志:
# [2026-01-15 10:00:00] Booting worker pid=12346
# [2026-01-15 10:00:00] Booting worker pid=12347
# ...
```

**示例 5：Dockerfile 中的 Gunicorn 配置**

```dockerfile
# Dockerfile
FROM python:3.11-slim

WORKDIR /app

# 安装依赖
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# 复制应用代码
COPY . .

# 暴露端口
EXPOSE 8000

# 使用 Gunicorn 启动
CMD ["gunicorn", "app:app", \
     "-w", "4", \
     "-k", "uvicorn.workers.UvicornWorker", \
     "-b", "0.0.0.0:8000", \
     "--timeout", "120", \
     "--access-logfile", "-", \
     "--error-logfile", "-"]
```

### 总结

- Gunicorn + UvicornWorker 是 FastAPI 官方推荐的生产部署方式
- Gunicorn 提供成熟的进程管理，UvicornWorker 提供 ASGI 高性能
- 配置文件 `gunicorn_config.py` 管理所有参数，便于版本控制
- `kill -HUP` 实现零停机平滑重启
- `--max-requests` 防止内存泄漏，Worker 定期重启
- `preload_app=True` 预加载应用，节省内存但影响热重载

---

## 第23讲 Nginx 反向代理配置

### 概念

**Nginx** 是高性能的 HTTP 服务器和反向代理。在生产环境中，通常在 Uvicorn 前部署 Nginx，由 Nginx 处理 SSL、静态文件、负载均衡，将动态请求转发给 Uvicorn。这种架构提供更好的性能、安全性和可扩展性。

### 原理

**Nginx + Uvicorn 架构**：

```
客户端
  │
  ▼
┌─────────────────────────────────┐
│           Nginx                 │
│  - SSL/TLS 终止                 │
│  - 静态文件服务                  │
│  - 负载均衡（多个 Uvicorn）      │
│  - Gzip 压缩                    │
│  - 请求限流                     │
│  - 访问日志                     │
└───────────────┬─────────────────┘
                │ HTTP（内网）
                ▼
┌─────────────────────────────────┐
│         Uvicorn/Gunicorn        │
│  - ASGI 应用                    │
│  - 业务逻辑                     │
└─────────────────────────────────┘
```

**Nginx 的职责**：
1. **SSL 终止**：处理 HTTPS，Uvicorn 只需处理 HTTP
2. **静态文件**：直接服务静态文件，不经过 Uvicorn
3. **负载均衡**：分发请求到多个 Uvicorn 实例
4. **压缩**：Gzip 压缩响应，减少带宽
5. **限流**：防止恶意请求压垮后端
6. **缓冲**：缓冲慢客户端请求，保护后端

**反向代理的关键配置**：
- `proxy_pass`：转发目标
- `proxy_set_header`：设置转发头
- `proxy_http_version 1.1`：使用 HTTP/1.1 支持 keep-alive
- `proxy_set_header Connection ""`：清除 Connection 头

### 例子

**示例 1：基本反向代理**

```nginx
# /etc/nginx/conf.d/myapp.conf
server {
    listen 80;
    server_name example.com;
    
    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

**示例 2：完整生产配置（含 SSL）**

```nginx
# /etc/nginx/conf.d/myapp.conf

# HTTP 重定向到 HTTPS
server {
    listen 80;
    server_name example.com www.example.com;
    return 301 https://$server_name$request_uri;
}

# HTTPS 主配置
server {
    listen 443 ssl http2;
    server_name example.com www.example.com;
    
    # SSL 证书
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512;
    ssl_prefer_server_ciphers off;
    
    # 日志
    access_log /var/log/nginx/myapp_access.log;
    error_log /var/log/nginx/myapp_error.log;
    
    # 静态文件
    location /static/ {
        alias /var/www/myapp/static/;
        expires 30d;
        add_header Cache-Control "public, immutable";
    }
    
    # 上传文件大小限制
    client_max_body_size 50M;
    
    # 反向代理到 Uvicorn
    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # 超时设置
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
        
        # 缓冲设置
        proxy_buffering on;
        proxy_buffer_size 16k;
        proxy_buffers 8 32k;
    }
    
    # WebSocket 支持
    location /ws/ {
        proxy_pass http://127.0.0.1:8000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_read_timeout 86400;  # WebSocket 长连接超时
    }
    
    # 健康检查
    location /health {
        proxy_pass http://127.0.0.1:8000/health;
        access_log off;
    }
}
```

**示例 3：负载均衡（多个 Uvicorn 实例）**

```nginx
# /etc/nginx/conf.d/myapp.conf

upstream myapp_backend {
    # 多个 Uvicorn 实例
    server 127.0.0.1:8001 weight=3;
    server 127.0.0.1:8002 weight=3;
    server 127.0.0.1:8003 weight=2;
    server 127.0.0.1:8004 weight=2;
    
    # 保持到后端的长连接
    keepalive 32;
    
    # 健康检查（Nginx Plus 才有主动检查，社区版用被动检查）
    # 失败的 server 会被临时移除
    max_fails 3;
    fail_timeout 30s;
}

server {
    listen 80;
    server_name example.com;
    
    location / {
        proxy_pass http://myapp_backend;
        proxy_http_version 1.1;
        proxy_set_header Connection "";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

**示例 4：Uvicorn 配合 Nginx 的配置**

```python
# main.py
import uvicorn
from fastapi import FastAPI, Request

app = FastAPI()

@app.get("/")
async def root(request: Request):
    # 获取真实客户端 IP（Nginx 转发）
    client_ip = request.headers.get("x-real-ip", request.client.host)
    return {"client_ip": client_ip}

if __name__ == "__main__":
    uvicorn.run(
        "main:app",
        host="127.0.0.1",      # 只监听本地，由 Nginx 转发
        port=8000,
        proxy_headers=True,     # 信任 X-Forwarded-* 头
        forwarded_allow_ips="*",  # 允许所有 IP 转发
        timeout_keep_alive=65,   # 略大于 Nginx 的 keepalive_timeout
    )
```

**示例 5：限流配置**

```nginx
# /etc/nginx/conf.d/myapp.conf

# 定义限流区域
limit_req_zone $binary_remote_addr zone=api_limit:10m rate=10r/s;
limit_req_zone $binary_remote_addr zone=login_limit:10m rate=1r/s;

server {
    listen 80;
    server_name example.com;
    
    # API 限流：每秒 10 个请求
    location /api/ {
        limit_req zone=api_limit burst=20 nodelay;
        proxy_pass http://127.0.0.1:8000;
    }
    
    # 登录限流：每秒 1 个请求
    location /login {
        limit_req zone=login_limit burst=5 nodelay;
        proxy_pass http://127.0.0.1:8000;
    }
}
```

### 总结

- Nginx 作为反向代理，处理 SSL、静态文件、负载均衡、限流
- Uvicorn 只需监听 127.0.0.1，由 Nginx 转发外部请求
- `proxy_headers=True` 让 Uvicorn 信任 X-Forwarded-* 头
- WebSocket 需配置 `Upgrade` 和 `Connection: upgrade` 头
- `timeout_keep_alive` 应略大于 Nginx 的 `keepalive_timeout`
- 限流在 Nginx 层做，保护后端 Uvicorn 不被压垮

---

## 第24讲 Docker 容器化部署

### 概念

**Docker** 是主流的容器化平台，将应用及其依赖打包为标准化容器。Uvicorn 应用容器化部署可以实现环境一致性、快速部署、弹性伸缩。本讲讲解如何编写 Dockerfile、优化镜像、配置 docker-compose。

### 原理

**Docker 容器化的优势**：
1. **环境一致**：开发、测试、生产环境完全相同
2. **快速部署**：镜像即制品，秒级启动
3. **资源隔离**：容器间互不影响
4. **弹性伸缩**：配合 K8s 实现自动扩缩容
5. **版本管理**：镜像版本化，支持回滚

**Docker 镜像优化原则**：
1. **多阶段构建**：分离构建环境和运行环境
2. **小基础镜像**：用 `python:3.11-slim` 而非 `python:3.11`
3. **利用缓存**：先复制 requirements.txt，再复制代码
4. **非 root 用户**：安全性要求
5. **.dockerignore**：排除不必要文件

### 例子

**示例 1：基本 Dockerfile**

```dockerfile
# Dockerfile
FROM python:3.11-slim

# 设置工作目录
WORKDIR /app

# 设置环境变量
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1

# 安装系统依赖
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# 复制依赖文件
COPY requirements.txt .

# 安装 Python 依赖
RUN pip install --no-cache-dir -r requirements.txt

# 复制应用代码
COPY . .

# 暴露端口
EXPOSE 8000

# 启动命令
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

**示例 2：生产级 Dockerfile（多阶段构建）**

```dockerfile
# Dockerfile.prod
# === 阶段 1：构建阶段 ===
FROM python:3.11-slim as builder

WORKDIR /app

# 安装构建依赖
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# 创建虚拟环境
RUN python -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

# 安装 Python 依赖
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# === 阶段 2：运行阶段 ===
FROM python:3.11-slim

WORKDIR /app

# 从构建阶段复制虚拟环境
COPY --from=builder /opt/venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

# 创建非 root 用户
RUN groupadd -r appuser && useradd -r -g appuser appuser

# 复制应用代码
COPY --chown=appuser:appuser . .

# 切换用户
USER appuser

# 暴露端口
EXPOSE 8000

# 健康检查
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:8000/health')" || exit 1

# 启动命令（使用 Gunicorn）
CMD ["gunicorn", "main:app", \
     "-w", "4", \
     "-k", "uvicorn.workers.UvicornWorker", \
     "-b", "0.0.0.0:8000", \
     "--access-logfile", "-", \
     "--error-logfile", "-"]
```

**示例 3：.dockerignore 文件**

```
# .dockerignore
__pycache__
*.pyc
*.pyo
*.pyd
.Python
*.egg-info
.git
.gitignore
.env
.venv
venv
env
.vscode
.idea
*.md
docs/
tests/
*.log
.DS_Store
```

**示例 4：docker-compose.yml**

```yaml
# docker-compose.yml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile.prod
    container_name: myapp
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/myapp
      - REDIS_URL=redis://redis:6379/0
      - ENV=production
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_started
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
      interval: 30s
      timeout: 3s
      retries: 3
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 1G
        reservations:
          cpus: '0.5'
          memory: 256M

  db:
    image: postgres:15-alpine
    container_name: myapp_db
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: myapp
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user -d myapp"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped

  redis:
    image: redis:7-alpine
    container_name: myapp_redis
    restart: unless-stopped

  nginx:
    image: nginx:alpine
    container_name: myapp_nginx
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf:ro
      - ./static:/var/www/static:ro
      - ./certs:/etc/nginx/certs:ro
    depends_on:
      - app
    restart: unless-stopped

volumes:
  postgres_data:
```

**示例 5：构建和运行**

```bash
# 构建镜像
docker build -t myapp:latest -f Dockerfile.prod .

# 运行容器
docker run -d \
    --name myapp \
    -p 8000:8000 \
    -e DATABASE_URL=postgresql://user:pass@db:5432/myapp \
    -e ENV=production \
    --restart unless-stopped \
    myapp:latest

# 使用 docker-compose
docker-compose up -d

# 查看日志
docker logs -f myapp

# 进入容器
docker exec -it myapp bash

# 重新构建并部署
docker-compose up -d --build
```

### 总结

- Docker 容器化实现环境一致性和快速部署
- 多阶段构建减小镜像体积，分离构建和运行环境
- 使用 `python:3.11-slim` 作为基础镜像，减小体积
- 非 root 用户运行，提高安全性
- `.dockerignore` 排除不必要文件，加速构建
- docker-compose 编排多容器应用（app + db + redis + nginx）
- HEALTHCHECK 指令实现容器健康检查

---

## 第25讲 systemd 服务部署

### 概念

**systemd** 是 Linux 系统的初始化系统和服务管理器。通过 systemd 管理 Uvicorn 服务，可以实现开机自启、崩溃自动重启、日志管理、统一的服务控制。这是传统服务器部署（非容器化）的标准方式。

### 原理

**systemd 的优势**：
1. **开机自启**：服务器重启后自动启动服务
2. **自动重启**：服务崩溃后自动重启
3. **日志管理**：通过 journalctl 统一查看日志
4. **依赖管理**：定义服务启动顺序和依赖
5. **用户级服务**：可以以非 root 用户运行
6. **资源限制**：限制 CPU、内存等资源

**systemd 服务文件结构**：

```ini
[Unit]
Description=服务描述
After=network.target  # 依赖

[Service]
Type=notify           # 服务类型
User=appuser          # 运行用户
WorkingDirectory=/app # 工作目录
ExecStart=启动命令
Restart=always        # 重启策略
RestartSec=5          # 重启间隔

[Install]
WantedBy=multi-user.target
```

### 例子

**示例 1：基本 Uvicorn 服务**

```ini
# /etc/systemd/system/myapp.service
[Unit]
Description=MyApp Uvicorn Service
After=network.target postgresql.service
Wants=postgresql.service

[Service]
Type=simple
User=appuser
Group=appuser
WorkingDirectory=/opt/myapp
Environment="PATH=/opt/myapp/venv/bin:/usr/local/bin:/usr/bin"
EnvironmentFile=/opt/myapp/.env
ExecStart=/opt/myapp/venv/bin/uvicorn main:app \
    --host 0.0.0.0 \
    --port 8000 \
    --workers 4 \
    --loop uvloop \
    --http httptools \
    --proxy-headers \
    --log-level info

Restart=always
RestartSec=5
TimeoutStopSec=30

# 资源限制
LimitNOFILE=65536
MemoryMax=2G
CPUQuota=200%

# 日志
StandardOutput=journal
StandardError=journal
SyslogIdentifier=myapp

[Install]
WantedBy=multi-user.target
```

**示例 2：使用 Gunicorn 的服务**

```ini
# /etc/systemd/system/myapp-gunicorn.service
[Unit]
Description=MyApp Gunicorn Service
After=network.target postgresql.service redis.service
Wants=postgresql.service redis.service

[Service]
Type=notify
User=appuser
Group=appuser
WorkingDirectory=/opt/myapp
Environment="PATH=/opt/myapp/venv/bin"
EnvironmentFile=/opt/myapp/.env
ExecStart=/opt/myapp/venv/bin/gunicorn main:app \
    --config /opt/myapp/gunicorn_config.py

ExecReload=/bin/kill -s HUP $MAINPID
KillMode=mixed
TimeoutStopSec=30

Restart=always
RestartSec=5

LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
```

**示例 3：服务管理命令**

```bash
# 重新加载 systemd 配置（修改 service 文件后必须执行）
sudo systemctl daemon-reload

# 启动服务
sudo systemctl start myapp

# 停止服务
sudo systemctl stop myapp

# 重启服务
sudo systemctl restart myapp

# 重新加载配置（不重启，Gunicorn 平滑重启）
sudo systemctl reload myapp

# 查看状态
sudo systemctl status myapp

# 设置开机自启
sudo systemctl enable myapp

# 禁止开机自启
sudo systemctl disable myapp

# 查看日志
sudo journalctl -u myapp -f          # 实时日志
sudo journalctl -u myapp --since today
sudo journalctl -u myapp -n 100      # 最近 100 行
```

**示例 4：多实例部署（端口区分）**

```ini
# /etc/systemd/system/myapp@.service
[Unit]
Description=MyApp Instance %i
After=network.target

[Service]
Type=simple
User=appuser
WorkingDirectory=/opt/myapp
Environment="PATH=/opt/myapp/venv/bin"
EnvironmentFile=/opt/myapp/.env
ExecStart=/opt/myapp/venv/bin/uvicorn main:app \
    --host 0.0.0.0 \
    --port 800%i \
    --workers 2

Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

启动多个实例：
```bash
# 启动实例 1（端口 8001）
sudo systemctl start myapp@1

# 启动实例 2（端口 8002）
sudo systemctl start myapp@2

# 启动实例 3（端口 8003）
sudo systemctl start myapp@3

# 全部开机自启
sudo systemctl enable myapp@1 myapp@2 myapp@3
```

**示例 5：配合 Nginx 的完整部署**

```bash
# 1. 部署应用代码
sudo mkdir -p /opt/myapp
sudo chown appuser:appuser /opt/myapp
cd /opt/myapp
git clone https://github.com/user/myapp.git .
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# 2. 配置环境变量
cp .env.example .env
# 编辑 .env 设置数据库、密钥等

# 3. 安装 systemd 服务
sudo cp deploy/myapp.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable myapp
sudo systemctl start myapp

# 4. 配置 Nginx
sudo cp deploy/myapp.nginx /etc/nginx/conf.d/
sudo nginx -t
sudo systemctl reload nginx

# 5. 验证
curl http://localhost:8000/health
curl https://example.com/health
```

### 总结

- systemd 是 Linux 标准服务管理器，适合传统服务器部署
- `Restart=always` 实现崩溃自动重启
- `EnvironmentFile` 管理环境变量，避免硬编码
- `journalctl -u service_name` 查看服务日志
- `systemctl reload` 实现 Gunicorn 平滑重启
- 模板服务（`@.service`）支持多实例部署
- 配合 Nginx 实现完整的反向代理架构

---

## 第26讲 Kubernetes 部署要点

### 概念

**Kubernetes（K8s）** 是容器编排的事实标准。在 K8s 上部署 Uvicorn 应用，可以实现自动扩缩容、滚动更新、服务发现、负载均衡。本讲讲解 K8s 部署 Uvicorn 的关键配置和最佳实践。

### 原理

**K8s 部署 Uvicorn 的架构**：

```
┌─────────────────────────────────────────────────────┐
│                  Kubernetes Cluster                 │
│                                                     │
│  ┌──────────────────────────────────────────────┐  │
│  │              Ingress Controller               │  │
│  │           (Nginx/Traefik/ALB)                │  │
│  └──────────────────┬───────────────────────────┘  │
│                     │                               │
│  ┌──────────────────▼───────────────────────────┐  │
│  │              Service (ClusterIP)              │  │
│  │           负载均衡到多个 Pod                  │  │
│  └──────────────────┬───────────────────────────┘  │
│                     │                               │
│  ┌─────────┬────────┴────────┬──────────┐         │
│  ▼         ▼                 ▼          ▼         │
│ ┌───┐    ┌───┐             ┌───┐      ┌───┐      │
│ │Pod│    │Pod│             │Pod│      │Pod│      │
│ │Uvi│    │Uvi│             │Uvi│      │Uvi│      │
│ │corn│   │corn│            │corn│     │corn│     │
│ └───┘    └───┘             └───┘      └───┘      │
│                                                     │
│  ┌──────────────────────────────────────────────┐  │
│  │           HPA (水平 Pod 自动扩缩容)           │  │
│  │    根据 CPU/内存/自定义指标自动扩缩 Pod       │  │
│  └──────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────┘
```

**K8s 部署的关键概念**：
- **Deployment**：管理 Pod 副本，支持滚动更新
- **Service**：提供稳定的访问入口和负载均衡
- **Ingress**：HTTP 路由，处理 SSL 和域名
- **HPA**：水平 Pod 自动扩缩容
- **ConfigMap/Secret**：配置和敏感信息管理
- **Liveness/Readiness Probe**：健康检查

### 例子

**示例 1：Deployment 配置**

```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  labels:
    app: myapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: myregistry/myapp:latest
        ports:
        - containerPort: 8000
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: myapp-secrets
              key: database-url
        - name: REDIS_URL
          value: "redis://redis-service:6379/0"
        - name: ENV
          value: "production"
        resources:
          requests:
            cpu: "250m"
            memory: "256Mi"
          limits:
            cpu: "500m"
            memory: "512Mi"
        # 健康检查
        livenessProbe:
          httpGet:
            path: /health
            port: 8000
          initialDelaySeconds: 30
          periodSeconds: 10
          failureThreshold: 3
        readinessProbe:
          httpGet:
            path: /ready
            port: 8000
          initialDelaySeconds: 5
          periodSeconds: 5
          failureThreshold: 3
        # 优雅关闭
        lifecycle:
          preStop:
            exec:
              command: ["/bin/sh", "-c", "sleep 10"]
      # 优雅终止宽限期
      terminationGracePeriodSeconds: 30
```

**示例 2：Service 配置**

```yaml
# service.yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: ClusterIP
  selector:
    app: myapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8000
```

**示例 3：Ingress 配置**

```yaml
# ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: myapp-ingress
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/proxy-body-size: "50m"
    nginx.ingress.kubernetes.io/proxy-read-timeout: "60"
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
spec:
  tls:
  - hosts:
    - example.com
    secretName: myapp-tls
  rules:
  - host: example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: myapp-service
            port:
              number: 80
```

**示例 4：HPA 自动扩缩容**

```yaml
# hpa.yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: myapp-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  minReplicas: 3
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300
      policies:
      - type: Percent
        value: 50
        periodSeconds: 60
```

**示例 5：Uvicorn 在 K8s 中的配置要点**

```python
# main.py
import os
import uvicorn
from fastapi import FastAPI

app = FastAPI()

@app.get("/health")
async def health():
    """存活检查（liveness）"""
    return {"status": "alive"}

@app.get("/ready")
async def ready():
    """就绪检查（readiness）"""
    # 检查依赖服务是否可用
    # 例如：数据库连接、Redis 连接
    return {"status": "ready"}

if __name__ == "__main__":
    # K8s 中通常单 Worker（由副本数扩展）
    # 但也可以根据 CPU limit 设置 Worker 数
    cpu_limit = os.getenv("CPU_LIMIT", "1")
    
    uvicorn.run(
        "main:app",
        host="0.0.0.0",
        port=8000,
        workers=1,  # K8s 中推荐 1，靠 Pod 副本扩展
        loop="uvloop",
        http="httptools",
        log_level="info",
        proxy_headers=True,
        forwarded_allow_ips="*",
    )
```

**关键配置说明**：

1. **Worker 数量**：K8s 中推荐每个 Pod 1 个 Worker，靠 Pod 副本扩展。这样 HPA 可以精确控制资源。

2. **健康检查**：
   - `livenessProbe`：检查应用是否存活，失败则重启 Pod
   - `readinessProbe`：检查应用是否就绪，失败则从负载均衡移除

3. **优雅关闭**：
   - `preStop` 钩子：等待 10 秒，让 Service 更新端点
   - `terminationGracePeriodSeconds`：给应用 30 秒处理完请求

4. **资源限制**：
   - `requests`：保证的最小资源
   - `limits`：最大资源限制

### 总结

- K8s 部署 Uvicorn 实现自动扩缩容、滚动更新、服务发现
- Deployment 管理 Pod 副本，Service 提供负载均衡
- Ingress 处理 HTTP 路由和 SSL
- HPA 根据 CPU/内存自动扩缩 Pod 数量
- K8s 中推荐每 Pod 1 个 Worker，靠副本数扩展
- `livenessProbe` 和 `readinessProbe` 实现健康检查
- `preStop` 钩子 + `terminationGracePeriodSeconds` 实现优雅关闭

---

# 第7章 性能调优

本章聚焦 Uvicorn 的性能优化。我们将讲解性能基准测试方法、Worker 数量调优、内存与连接数优化，以及常见性能瓶颈的分析与解决。掌握这些技能可以让你的 Uvicorn 应用发挥最大性能。

---

## 第27讲 性能基准测试

### 概念

**性能基准测试（Benchmark）** 是衡量服务器性能的标准化方法。通过模拟真实负载，测量吞吐量（QPS）、延迟（Latency）、并发数等关键指标。本讲讲解如何使用 wrk、ab、locust 等工具对 Uvicorn 进行性能测试。

### 原理

**关键性能指标**：

| 指标 | 说明 | 单位 |
|------|------|------|
| QPS/TPS | 每秒请求数/事务数 | req/s |
| 延迟（Latency） | 请求响应时间 | ms |
| P50/P95/P99 | 50%/95%/99% 请求的延迟 | ms |
| 并发数 | 同时处理的请求数 | - |
| 错误率 | 失败请求占比 | % |
| CPU 使用率 | CPU 利用率 | % |
| 内存使用 | 内存占用量 | MB/GB |

**测试工具对比**：

| 工具 | 特点 | 适用场景 |
|------|------|----------|
| wrk | C 实现，高性能，支持 Lua 脚本 | HTTP 基准测试 |
| ab (Apache Bench) | 简单易用，Apache 自带 | 快速测试 |
| locust | Python 编写，支持分布式，可编程 | 复杂场景测试 |
| hey | Go 实现，简单易用 | 快速测试 |
| vegeta | Go 实现，支持恒定吞吐量 | 精确吞吐量测试 |

**测试方法论**：
1. **基线测试**：测量当前性能基线
2. **逐步加压**：从低并发开始，逐步增加
3. **找到拐点**：性能开始下降的并发数
4. **瓶颈分析**：CPU/内存/IO/网络哪个先到瓶颈
5. **优化验证**：优化后重新测试对比

### 例子

**示例 1：准备测试应用**

```python
# benchmark_app.py
from fastapi import FastAPI
import uvicorn

app = FastAPI()

@app.get("/json")
async def json_endpoint():
    """轻量级 JSON 响应"""
    return {"message": "hello", "status": "ok"}

@app.get("/compute")
async def compute_endpoint():
    """CPU 密集型"""
    total = sum(i * i for i in range(10000))
    return {"result": total}

@app.get("/io")
async def io_endpoint():
    """模拟 I/O 操作"""
    import asyncio
    await asyncio.sleep(0.01)  # 10ms 模拟 I/O
    return {"status": "done"}

if __name__ == "__main__":
    uvicorn.run(
        "benchmark_app:app",
        host="0.0.0.0",
        port=8000,
        workers=4,
        loop="uvloop",
        http="httptools",
        log_level="warning",  # 减少日志开销
    )
```

**示例 2：使用 wrk 测试**

```bash
# 安装 wrk
sudo apt install wrk

# 基本测试：2 线程，100 连接，持续 30 秒
wrk -t2 -c100 -d30s http://localhost:8000/json

# 输出示例:
# Running 30s test @ http://localhost:8000/json
#   2 threads and 100 connections
#   Thread Stats   Avg      Stdev     Max   +/- Stdev
#     Latency     5.23ms   2.10ms  25.12ms   75.30%
#     Req/Sec     9.50k     1.20k   12.30k    68.50%
#   569,832 requests in 30.00s, 78.12MB read
# Requests/sec:  18994.40
# Transfer/sec:      2.60MB
```

**示例 3：使用 wrk Lua 脚本测试 POST 请求**

```lua
-- post_test.lua
wrk.method = "POST"
wrk.body = '{"name":"alice","email":"alice@example.com"}'
wrk.headers["Content-Type"] = "application/json"
```

```bash
wrk -t2 -c100 -d30s -s post_test.lua http://localhost:8000/users
```

**示例 4：使用 ab (Apache Bench)**

```bash
# 安装
sudo apt install apache2-utils

# 基本测试：100 请求，10 并发
ab -n 100 -c 10 http://localhost:8000/json

# 输出关键指标:
# Requests per second:    15000.50 [#/sec] (mean)
# Time per request:       0.667 [ms] (mean)
# Time per request:       6.667 [ms] (mean, across all concurrent requests)

# 测试并发能力：10000 请求，100 并发
ab -n 10000 -c 100 -k http://localhost:8000/json
# -k 启用 keep-alive
```

**示例 5：使用 locust 进行复杂测试**

```bash
pip install locust
```

```python
# locustfile.py
from locust import HttpUser, task, between

class WebsiteUser(HttpUser):
    wait_time = between(1, 3)
    host = "http://localhost:8000"
    
    @task(3)
    def view_json(self):
        self.client.get("/json")
    
    @task(2)
    def view_io(self):
        self.client.get("/io")
    
    @task(1)
    def view_compute(self):
        self.client.get("/compute")
```

```bash
# 启动 locust Web UI
locust

# 访问 http://localhost:8089 配置测试参数
# 或命令行模式
locust --headless -u 100 -r 10 -t 30s --host http://localhost:8000
```

**示例 6：对比不同配置的性能**

```bash
#!/bin/bash
# benchmark_compare.sh

echo "=== 测试 1: 单 Worker + asyncio ==="
uvicorn benchmark_app:app --workers 1 --loop asyncio --port 8000 &
sleep 2
wrk -t2 -c100 -d10s http://localhost:8000/json
kill %1

echo "=== 测试 2: 单 Worker + uvloop ==="
uvicorn benchmark_app:app --workers 1 --loop uvloop --port 8000 &
sleep 2
wrk -t2 -c100 -d10s http://localhost:8000/json
kill %1

echo "=== 测试 3: 4 Worker + uvloop ==="
uvicorn benchmark_app:app --workers 4 --loop uvloop --port 8000 &
sleep 2
wrk -t2 -c100 -d10s http://localhost:8000/json
kill %1
```

### 总结

- 性能测试关注 QPS、延迟、P95/P99、错误率等指标
- wrk 适合 HTTP 基准测试，locust 适合复杂场景
- 测试应从低并发开始，逐步加压找到性能拐点
- 对比不同配置（Worker 数、事件循环、HTTP 解析器）的性能差异
- 测试时关闭访问日志（`--log-level warning`）减少干扰
- 生产环境性能测试应在与生产环境相同的硬件上进行

---

## 第28讲 Worker 数量调优

### 概念

**Worker 数量**是影响 Uvicorn 性能的关键参数。Worker 太少无法利用多核，太多则增加上下文切换开销和内存占用。本讲讲解如何根据应用类型和硬件配置选择最优 Worker 数量。

### 原理

**Worker 数量与性能的关系**：

```
性能
  │
  │           ┌─────────┐ ← 最优区间
  │          ╱           ╲
  │         ╱             ╲
  │        ╱               ╲
  │       ╱                 ╲
  │      ╱                   ╲
  │     ╱                     ╲
  │____╱                       ╲____
  └──┬───┬───┬───┬───┬───┬───┬──→ Worker 数
     1   2   3   4   5   6   7
   
  ←太少  最优  太多→
  
  CPU 未充分利用  平衡  上下文切换开销大
```

**选择原则**：

| 应用类型 | 推荐 Worker 数 | 原因 |
|----------|---------------|------|
| CPU 密集型 | CPU 核心数 | 每个 Worker 独占一个核 |
| I/O 密集型 | CPU 核心数 × 2-4 | I/O 等待时切换到其他请求 |
| 混合型 | CPU 核心数 + 1 | 通用推荐 |
| 内存受限 | 根据内存计算 | 每个 Worker 约 100-300MB |

**Gunicorn 官方公式**：
```
workers = (2 × CPU核心数) + 1
```

**为什么不是越多越好**：
1. **上下文切换**：Worker 数过多，CPU 在进程间切换开销大
2. **内存占用**：每个 Worker 独立内存空间，过多会 OOM
3. **连接竞争**：多个 Worker 争抢同一端口，操作系统调度开销
4. **GIL 无关**：多 Worker 是多进程，不受 GIL 限制，但进程间通信开销大

### 例子

**示例 1：自动计算 Worker 数**

```python
# auto_workers.py
import multiprocessing
import psutil
import uvicorn

def calculate_workers():
    """根据 CPU 和内存计算最优 Worker 数"""
    cpu_count = multiprocessing.cpu_count()
    memory_gb = psutil.virtual_memory().total / (1024 ** 3)
    
    # 方法 1：Gunicorn 公式
    workers_gunicorn = (2 * cpu_count) + 1
    
    # 方法 2：根据内存限制（每个 Worker 假设 256MB）
    workers_memory = int(memory_gb * 1024 / 256)
    
    # 取较小值
    workers = min(workers_gunicorn, workers_memory)
    
    print(f"CPU: {cpu_count} cores")
    print(f"Memory: {memory_gb:.1f} GB")
    print(f"Recommended workers: {workers}")
    
    return workers

if __name__ == "__main__":
    uvicorn.run(
        "main:app",
        workers=calculate_workers(),
        host="0.0.0.0",
        port=8000,
    )
```

**示例 2：不同 Worker 数的性能对比**

```bash
#!/bin/bash
# worker_benchmark.sh

APP="benchmark_app:app"
URL="http://localhost:8000/json"

for workers in 1 2 4 8 16; do
    echo "=== Workers: $workers ==="
    uvicorn $APP --workers $workers --port 8000 &
    PID=$!
    sleep 3
    
    # 测试
    wrk -t4 -c200 -d15s $URL 2>&1 | grep "Requests/sec"
    
    kill $PID
    sleep 2
done
```

典型结果（4 核机器）：
```
=== Workers: 1 ===
Requests/sec:  12000

=== Workers: 2 ===
Requests/sec:  23000

=== Workers: 4 ===
Requests/sec:  42000  ← 最优

=== Workers: 8 ===
Requests/sec:  38000  ← 下降

=== Workers: 16 ===
Requests/sec:  32000  ← 继续下降
```

**示例 3：CPU 密集型 vs I/O 密集型**

```python
# cpu_intensive.py
async def app(scope, receive, send):
    if scope["type"] != "http":
        return
    
    # CPU 密集型计算
    total = sum(i * i for i in range(100000))
    
    await send({"type": "http.response.start", "status": 200, "headers": []})
    await send({"type": "http.response.body", "body": str(total).encode()})
```

```python
# io_intensive.py
import asyncio
import httpx

async def app(scope, receive, send):
    if scope["type"] != "http":
        return
    
    # I/O 密集型：调用外部 API
    async with httpx.AsyncClient() as client:
        response = await client.get("https://httpbin.org/get")
    
    await send({"type": "http.response.start", "status": 200, "headers": []})
    await send({"type": "http.response.body", "body": response.content})
```

```bash
# CPU 密集型：Worker 数 = CPU 核心数
uvicorn cpu_intensive:app --workers 4  # 4 核机器

# I/O 密集型：Worker 数 = CPU 核心数 × 2
uvicorn io_intensive:app --workers 8  # 4 核机器
```

**示例 4：动态调整 Worker 数**

```python
# dynamic_workers.py
import os
import multiprocessing
import uvicorn

def get_workers():
    """根据环境变量动态调整"""
    env = os.getenv("APP_ENV", "development")
    cpu = multiprocessing.cpu_count()
    
    if env == "development":
        return 1  # 开发用单 Worker
    elif env == "staging":
        return cpu  # 预发用 CPU 核心数
    elif env == "production":
        return cpu * 2 + 1  # 生产用 Gunicorn 公式
    else:
        return 1

if __name__ == "__main__":
    uvicorn.run(
        "main:app",
        workers=get_workers(),
        host="0.0.0.0",
        port=8000,
    )
```

**示例 5：Gunicorn 配置中的 Worker 计算**

```python
# gunicorn_config.py
import multiprocessing
import os

# 基础公式
workers = multiprocessing.cpu_count() * 2 + 1

# 根据环境变量覆盖
if "WORKERS" in os.environ:
    workers = int(os.environ["WORKERS"])

# 其他配置
worker_class = "uvicorn.workers.UvicornWorker"
max_requests = 1000
max_requests_jitter = 50
preload_app = True
```

### 总结

- Worker 数量选择：CPU 密集型 = CPU 核心数，I/O 密集型 = CPU × 2-4
- Gunicorn 公式：`(2 × CPU核心数) + 1`
- Worker 过多会增加上下文切换和内存开销，反而降低性能
- 通过基准测试找到最优 Worker 数，不同应用类型最优值不同
- 内存受限时按内存计算：`内存GB × 1024 / 单Worker内存MB`
- 生产环境用环境变量管理 Worker 数，便于不同环境调整

---

## 第29讲 内存与连接数优化

### 概念

**内存和连接数**是 Uvicorn 性能的两大资源约束。内存不足会导致 OOM，连接数过多会耗尽文件描述符。本讲讲解如何优化内存使用、管理连接数、配置系统资源限制。

### 原理

**Uvicorn 内存组成**：

```
单个 Worker 进程内存:
├── Python 解释器        ~15MB
├── 应用代码             ~10-50MB
├── 依赖库               ~50-200MB
├── 事件循环缓冲         ~5MB
├── HTTP 解析缓冲        ~1MB/连接
├── WebSocket 连接       ~50KB/连接
└── 业务数据             可变

总内存 = Worker 数 × 单 Worker 内存
```

**连接数限制因素**：

1. **文件描述符**：每个连接占用一个 fd，系统默认 1024
2. **内存**：每个连接约 50-100KB
3. **CPU**：连接数过多增加调度开销
4. **端口范围**：客户端端口范围 32768-60999（约 28000）

**系统资源限制**：

```bash
# 查看当前限制
ulimit -n          # 文件描述符
ulimit -u          # 进程数
ulimit -m          # 内存

# 临时修改
ulimit -n 65536

# 永久修改（/etc/security/limits.conf）
* soft nofile 65536
* hard nofile 65536
```

### 例子

**示例 1：调整文件描述符限制**

```bash
# 临时调整
ulimit -n 65536
uvicorn main:app --workers 4

# 永久调整（/etc/security/limits.conf）
echo "* soft nofile 65536" | sudo tee -a /etc/security/limits.conf
echo "* hard nofile 65536" | sudo tee -a /etc/security/limits.conf

# systemd 服务中调整
# /etc/systemd/system/myapp.service
[Service]
LimitNOFILE=65536
```

**示例 2：限制并发连接数**

```python
# limit_connections.py
import uvicorn

uvicorn.run(
    "main:app",
    workers=4,
    limit_concurrency=1000,  # 每个 Worker 最多 1000 并发
    backlog=2048,            # TCP 连接队列
)
```

```python
# 应用层限流
from fastapi import FastAPI, Request, HTTPException
import asyncio

app = FastAPI()

# 信号量限制并发
semaphore = asyncio.Semaphore(100)

@app.middleware("http")
async def limit_concurrent_requests(request: Request, call_next):
    if not semaphore.locked() or semaphore._value > 0:
        async with semaphore:
            return await call_next(request)
    raise HTTPException(status_code=503, detail="Server busy")

@app.get("/")
async def root():
    await asyncio.sleep(0.1)
    return {"status": "ok"}
```

**示例 3：内存监控**

```python
# memory_monitor.py
import psutil
import os
from fastapi import FastAPI
import uvicorn

app = FastAPI()

@app.get("/metrics")
async def metrics():
    process = psutil.Process(os.getpid())
    mem_info = process.memory_info()
    
    return {
        "pid": os.getpid(),
        "memory_rss_mb": round(mem_info.rss / 1024 / 1024, 2),
        "memory_vms_mb": round(mem_info.vms / 1024 / 1024, 2),
        "cpu_percent": process.cpu_percent(),
        "threads": process.num_threads(),
        "connections": len(process.connections()),
    }

@app.get("/")
async def root():
    return {"status": "ok"}

if __name__ == "__main__":
    uvicorn.run("memory_monitor:app", workers=4, port=8000)
```

**示例 4：防止内存泄漏**

```python
# 防止内存泄漏的配置
import uvicorn

uvicorn.run(
    "main:app",
    workers=4,
    limit_max_requests=10000,  # 处理 10000 请求后重启 Worker
)
```

```python
# gunicorn_config.py
max_requests = 10000          # 每 Worker 处理 10000 请求后重启
max_requests_jitter = 50      # 随机抖动，避免同时重启
```

**示例 5：优化内存使用**

```python
# 优化前：加载大文件到内存
data = open("large_file.json").read()  # 占用大量内存

# 优化后：流式读取
import json
import ijson  # 流式 JSON 解析

def process_large_file():
    with open("large_file.json", "rb") as f:
        for item in ijson.items(f, "item"):
            yield item  # 逐项处理，不全部加载
```

```python
# 优化前：缓存所有数据
cache = {}
for i in range(1000000):
    cache[i] = f"value_{i}"  # 占用大量内存

# 优化后：使用 LRU 缓存限制大小
from functools import lru_cache

@lru_cache(maxsize=10000)
def get_data(key):
    return f"value_{key}"
```

**示例 6：系统级优化**

```bash
# /etc/sysctl.conf 网络优化

# 增加连接队列
net.core.somaxconn = 65535
net.ipv4.tcp_max_syn_backlog = 65535

# 加快 TIME_WAIT 回收
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_fin_timeout = 30

# 增加端口范围
net.ipv4.ip_local_port_range = 10000 65535

# 增加文件描述符
fs.file-max = 1000000

# 应用配置
sudo sysctl -p
```

### 总结

- 每个 Worker 约占用 100-300MB 内存，总内存 = Worker 数 × 单 Worker 内存
- 文件描述符限制（`ulimit -n`）需调整到 65536+
- `--limit-concurrency` 限制并发连接数，防止过载
- `--limit-max-requests` 定期重启 Worker，防止内存泄漏
- 应用层用信号量（Semaphore）实现更精细的并发控制
- 系统级优化：调整 `somaxconn`、`tcp_tw_reuse`、`file-max` 等内核参数
- 监控内存使用，及时发现泄漏

---

## 第30讲 常见性能瓶颈分析

### 概念

性能瓶颈是限制系统整体性能的关键因素。本讲讲解 Uvicorn 应用常见的性能瓶颈类型、诊断方法和解决方案，包括 CPU 瓶颈、I/O 瓶颈、数据库瓶颈、网络瓶颈等。

### 原理

**性能瓶颈分类**：

```
┌─────────────────────────────────────────┐
│           性能瓶颈分类                   │
├─────────────────────────────────────────┤
│                                         │
│  ┌─────────┐  ┌─────────┐  ┌────────┐ │
│  │CPU 瓶颈 │  │I/O 瓶颈 │  │内存瓶颈│ │
│  │         │  │         │  │        │ │
│  │计算密集 │  │磁盘/网络│  │OOM/泄漏│ │
│  └─────────┘  └─────────┘  └────────┘ │
│                                         │
│  ┌─────────┐  ┌─────────┐  ┌────────┐ │
│  │数据库   │  │连接池   │  │GIL     │ │
│  │瓶颈     │  │耗尽     │  │瓶颈    │ │
│  │         │  │         │  │        │ │
│  │慢查询   │  │连接不足 │  │同步代码│ │
│  └─────────┘  └─────────┘  └────────┘ │
│                                         │
└─────────────────────────────────────────┘
```

**诊断方法论**：

1. **监控指标**：CPU、内存、I/O、网络
2. ** profiling**：分析代码执行时间
3. **A/B 测试**：对比不同配置的性能
4. **逐步排除**：隔离变量，定位瓶颈

### 例子

**示例 1：CPU 瓶颈诊断与解决**

```python
# 问题：CPU 密集型计算阻塞事件循环
async def app(scope, receive, send):
    # 这个计算会阻塞整个事件循环
    result = sum(i * i for i in range(10000000))  # ~1秒
    
    await send({"type": "http.response.start", "status": 200, "headers": []})
    await send({"type": "http.response.body", "body": str(result).encode()})
```

```python
# 解决方案 1：使用 run_in_executor 在线程池中执行
import asyncio
import functools

async def app(scope, receive, send):
    if scope["type"] != "http":
        return
    
    loop = asyncio.get_event_loop()
    
    # 在线程池中执行 CPU 密集型任务
    result = await loop.run_in_executor(
        None,  # 使用默认线程池
        functools.partial(sum, (i * i for i in range(10000000)))
    )
    
    await send({"type": "http.response.start", "status": 200, "headers": []})
    await send({"type": "http.response.body", "body": str(result).encode()})
```

```python
# 解决方案 2：使用 ProcessPoolExecutor（多进程）
from concurrent.futures import ProcessPoolExecutor
import asyncio

executor = ProcessPoolExecutor(max_workers=4)

def heavy_compute(n):
    return sum(i * i for i in range(n))

async def app(scope, receive, send):
    if scope["type"] != "http":
        return
    
    loop = asyncio.get_event_loop()
    result = await loop.run_in_executor(executor, heavy_compute, 10000000)
    
    await send({"type": "http.response.start", "status": 200, "headers": []})
    await send({"type": "http.response.body", "body": str(result).encode()})
```

**示例 2：I/O 瓶颈诊断与解决**

```python
# 问题：同步 I/O 阻塞事件循环
import time

async def app(scope, receive, send):
    # 同步文件读取会阻塞事件循环
    with open("large_file.txt", "r") as f:
        data = f.read()  # 阻塞！
    
    await send({"type": "http.response.start", "status": 200, "headers": []})
    await send({"type": "http.response.body", "body": data.encode()})
```

```python
# 解决方案：使用 aiofiles 异步读取文件
import aiofiles
import asyncio

async def app(scope, receive, send):
    if scope["type"] != "http":
        return
    
    async with aiofiles.open("large_file.txt", "r") as f:
        data = await f.read()
    
    await send({"type": "http.response.start", "status": 200, "headers": []})
    await send({"type": "http.response.body", "body": data.encode()})
```

**示例 3：数据库瓶颈诊断与解决**

```python
# 问题：每次请求都创建新连接
import psycopg2

async def get_user(user_id: int):
    # 每次都创建新连接，开销大
    conn = psycopg2.connect("postgresql://user:pass@localhost/db")
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))
    user = cursor.fetchone()
    conn.close()
    return user
```

```python
# 解决方案：使用连接池
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession
from sqlalchemy.orm import sessionmaker

engine = create_async_engine(
    "postgresql+asyncpg://user:pass@localhost/db",
    pool_size=20,        # 连接池大小
    max_overflow=10,     # 最大溢出
    pool_pre_ping=True,  # 连接前检查
    pool_recycle=3600,   # 连接回收时间
)

async_session = sessionmaker(engine, class_=AsyncSession)

async def get_user(user_id: int):
    async with async_session() as session:
        result = await session.execute(
            "SELECT * FROM users WHERE id = :id",
            {"id": user_id}
        )
        return result.fetchone()
```

**示例 4：使用 cProfile 分析性能**

```python
# profile_app.py
import cProfile
import pstats
from fastapi import FastAPI
import uvicorn

app = FastAPI()

@app.get("/profile")
async def profile_endpoint():
    """带性能分析的端点"""
    profiler = cProfile.Profile()
    profiler.enable()
    
    # 业务逻辑
    result = sum(i * i for i in range(100000))
    
    profiler.disable()
    stats = pstats.Stats(profiler)
    stats.sort_stats("cumulative")
    
    # 输出到文件
    stats.dump_stats("/tmp/profile.prof")
    
    return {"result": result}

if __name__ == "__main__":
    uvicorn.run("profile_app:app", port=8000)
```

```bash
# 分析结果
python -m pstats /tmp/profile.prof
# 在交互式界面中:
# sort cumulative
# stats 20
```

**示例 5：使用 py-spy 实时分析**

```bash
# 安装 py-spy
pip install py-spy

# 实时查看函数调用
py-spy top --pid <PID>

# 生成火焰图
py-spy record --pid <PID> -o profile.svg --duration 30
```

### 总结

- CPU 密集型任务用 `run_in_executor` 放到线程池/进程池
- I/O 操作必须用异步库（aiofiles、httpx、asyncpg）
- 数据库用连接池，避免频繁创建连接
- cProfile 和 py-spy 是性能分析利器
- 火焰图直观展示函数调用耗时
- 瓶颈定位遵循"监控→分析→优化→验证"循环
- 优化前先测量，避免盲目优化

---

# 第8章 高级特性与生产实践

本章是 Uvicorn 教程的最后一章，聚焦高级特性和生产环境最佳实践。我们将讲解中间件机制、信号处理与优雅关闭、监控与可观测性、安全加固，以及完整的生产环境 Checklist 和故障排查指南。

---

## 第31讲 中间件机制

### 概念

**中间件（Middleware）** 是 ASGI 应用中位于服务器和业务应用之间的处理层。它可以拦截请求和响应，实现日志、认证、CORS、压缩、限流等横切关注点。Uvicorn 本身不提供中间件，但支持运行任何 ASGI 中间件。

### 原理

**ASGI 中间件的工作原理**：

```
请求流向:
Uvicorn → 中间件1 → 中间件2 → 中间件3 → 应用
                                              │
响应流向:                                       │
Uvicorn ← 中间件1 ← 中间件2 ← 中间件3 ← 响应
```

**中间件的标准结构**：

```python
class MyMiddleware:
    def __init__(self, app):
        """app 是被包装的 ASGI 应用"""
        self.app = app
    
    async def __call__(self, scope, receive, send):
        """拦截请求"""
        # 请求前处理
        await self.app(scope, receive, send)
        # 响应后处理
```

**中间件的执行顺序**：

中间件按"洋葱模型"执行：最外层中间件最先处理请求，最后处理响应。

```
请求 →  [中间件A] → [中间件B] → [中间件C] → 应用
响应 ←  [中间件A] ← [中间件B] ← [中间件C] ← 
```

### 例子

**示例 1：自定义日志中间件**

```python
# logging_middleware.py
import time
import logging

logger = logging.getLogger("myapp")

class LoggingMiddleware:
    def __init__(self, app):
        self.app = app
    
    async def __call__(self, scope, receive, send):
        if scope["type"] != "http":
            await self.app(scope, receive, send)
            return
        
        start_time = time.time()
        method = scope["method"]
        path = scope["path"]
        client = scope.get("client", ["?", 0])[0]
        
        status_code = None
        
        async def send_wrapper(message):
            nonlocal status_code
            if message["type"] == "http.response.start":
                status_code = message["status"]
            await send(message)
        
        try:
            await self.app(scope, receive, send_wrapper)
        except Exception as e:
            logger.exception(f"Error processing {method} {path}")
            raise
        finally:
            elapsed = (time.time() - start_time) * 1000
            logger.info(
                f"{client} - {method} {path} - {status_code} - {elapsed:.2f}ms"
            )


# 使用
async def app(scope, receive, send):
    await send({"type": "http.response.start", "status": 200, "headers": []})
    await send({"type": "http.response.body", "body": b"OK"})

# 包装中间件
app = LoggingMiddleware(app)
```

**示例 2：认证中间件**

```python
# auth_middleware.py
import hmac
import hashlib
import json

class AuthMiddleware:
    def __init__(self, app, secret_key):
        self.app = app
        self.secret_key = secret_key
    
    async def __call__(self, scope, receive, send):
        if scope["type"] != "http":
            await self.app(scope, receive, send)
            return
        
        # 排除公开路径
        if scope["path"] in ["/login", "/health", "/docs"]:
            await self.app(scope, receive, send)
            return
        
        # 验证 token
        headers = dict(scope["headers"])
        auth_header = headers.get(b"authorization", b"").decode()
        
        if not auth_header.startswith("Bearer "):
            await self._send_error(send, 401, "Missing token")
            return
        
        token = auth_header[7:]
        if not self._verify_token(token):
            await self._send_error(send, 401, "Invalid token")
            return
        
        # 将用户信息存入 scope
        scope["user"] = {"id": 1, "name": "alice"}
        
        await self.app(scope, receive, send)
    
    def _verify_token(self, token):
        # 简化的 token 验证
        expected = hmac.new(
            self.secret_key.encode(),
            b"user:1",
            hashlib.sha256
        ).hexdigest()
        return hmac.compare_digest(token, expected)
    
    async def _send_error(self, send, status, message):
        await send({
            "type": "http.response.start",
            "status": status,
            "headers": [[b"content-type", b"application/json"]],
        })
        await send({
            "type": "http.response.body",
            "body": json.dumps({"error": message}).encode(),
        })
```

**示例 3：使用 Starlette 内置中间件**

```python
# starlette_middlewares.py
from starlette.applications import Starlette
from starlette.routing import Route
from starlette.responses import JSONResponse
from starlette.middleware import Middleware
from starlette.middleware.cors import CORSMiddleware
from starlette.middleware.gzip import GZipMiddleware
from starlette.middleware.httpsredirect import HTTPSRedirectMiddleware
from starlette.middleware.trustedhost import TrustedHostMiddleware
from starlette.middleware.sessions import SessionMiddleware
import uvicorn

async def homepage(request):
    return JSONResponse({"hello": "world"})

routes = [Route("/", homepage)]

middleware = [
    # HTTPS 重定向
    Middleware(HTTPSRedirectMiddleware),
    
    # 信任的主机
    Middleware(TrustedHostMiddleware, allowed_hosts=["example.com", "*.example.com"]),
    
    # CORS 跨域
    Middleware(
        CORSMiddleware,
        allow_origins=["https://example.com"],
        allow_methods=["GET", "POST", "PUT", "DELETE"],
        allow_headers=["*"],
        allow_credentials=True,
    ),
    
    # Gzip 压缩
    Middleware(GZipMiddleware, minimum_size=1000),
    
    # Session
    Middleware(SessionMiddleware, secret_key="your-secret-key"),
]

app = Starlette(routes=routes, middleware=middleware)

if __name__ == "__main__":
    uvicorn.run("starlette_middlewares:app", port=8000)
```

**示例 4：FastAPI 中间件**

```python
# fastapi_middleware.py
import time
from fastapi import FastAPI, Request, Response
import uvicorn

app = FastAPI()

@app.middleware("http")
async def add_process_time_header(request: Request, call_next):
    """添加处理时间响应头"""
    start_time = time.time()
    response = await call_next(request)
    process_time = time.time() - start_time
    response.headers["X-Process-Time"] = f"{process_time:.4f}"
    return response

@app.middleware("http")
async def log_requests(request: Request, call_next):
    """记录请求日志"""
    print(f"Request: {request.method} {request.url.path}")
    response = await call_next(request)
    print(f"Response: {response.status_code}")
    return response

@app.middleware("http")
async def rate_limit(request: Request, call_next):
    """简单的速率限制"""
    # 实际应用中用 Redis 实现
    response = await call_next(request)
    response.headers["X-RateLimit-Limit"] = "100"
    response.headers["X-RateLimit-Remaining"] = "99"
    return response

@app.get("/")
async def root():
    return {"message": "Hello"}

if __name__ == "__main__":
    uvicorn.run("fastapi_middleware:app", port=8000)
```

### 总结

- 中间件是 ASGI 应用的横切关注点处理层
- 中间件按"洋葱模型"执行，外层先处理请求，后处理响应
- 自定义中间件实现 `__init__(app)` 和 `__call__(scope, receive, send)`
- Starlette 提供丰富的内置中间件：CORS、GZip、TrustedHost、Session 等
- FastAPI 用 `@app.middleware("http")` 装饰器简化中间件编写
- 中间件顺序很重要：安全相关（HTTPS、TrustedHost）应放最外层
- 避免在中间件中做耗时操作，会阻塞所有请求

---

## 第32讲 信号处理与优雅关闭

### 概念

**信号处理**是 Uvicorn 响应操作系统信号（如 SIGINT、SIGTERM）的机制。**优雅关闭**是指在收到关闭信号后，先处理完当前请求再退出，避免请求中断。这对于零停机部署和保证数据一致性至关重要。

### 原理

**常见信号及其含义**：

| 信号 | 编号 | 含义 | Uvicorn 行为 |
|------|------|------|-------------|
| SIGINT | 2 | Ctrl+C | 优雅关闭 |
| SIGTERM | 15 | 终止请求 | 优雅关闭 |
| SIGHUP | 1 | 挂起 | Gunicorn 平滑重启 |
| SIGKILL | 9 | 强制杀死 | 立即终止（不可捕获） |
| SIGUSR1 | 10 | 用户信号1 | 重新打开日志文件 |

**优雅关闭流程**：

```
1. 收到 SIGTERM/SIGINT
   │
   ▼
2. 停止接受新连接
   │
   ▼
3. 发送 lifespan.shutdown 事件
   │
   ▼
4. 等待当前请求完成（最多 timeout_graceful_shutdown 秒）
   │
   ▼
5. 关闭所有连接
   │
   ▼
6. 退出进程
```

**关键参数**：
- `--timeout-graceful-shutdown`：优雅关闭超时（默认 30 秒）
- 超时后强制关闭剩余连接

### 例子

**示例 1：观察优雅关闭**

```python
# graceful_shutdown.py
import asyncio
import time
from fastapi import FastAPI
import uvicorn

app = FastAPI()

@app.get("/slow")
async def slow_endpoint():
    """模拟慢请求"""
    print(f"开始处理: {time.strftime('%H:%M:%S')}")
    await asyncio.sleep(10)  # 10 秒处理
    print(f"处理完成: {time.strftime('%H:%M:%S')}")
    return {"status": "done"}

if __name__ == "__main__":
    uvicorn.run(
        "graceful_shutdown:app",
        port=8000,
        timeout_graceful_shutdown=30,  # 30 秒优雅关闭
    )
```

测试：
```bash
# 终端 1：启动服务
python graceful_shutdown.py

# 终端 2：发起慢请求
curl http://localhost:8000/slow &

# 终端 3：等待 2 秒后发送 SIGTERM
sleep 2
kill -TERM $(pgrep -f graceful_shutdown)

# 观察:
# - 服务不会立即退出
# - 等待慢请求处理完成
# - 处理完成后才退出
```

**示例 2：Lifespan 中的清理**

```python
# lifespan_cleanup.py
from contextlib import asynccontextmanager
from fastapi import FastAPI
import uvicorn

# 模拟资源
resources = {}

@asynccontextmanager
async def lifespan(app: FastAPI):
    # === Startup ===
    print("初始化资源...")
    resources["db"] = "connected"
    resources["cache"] = "connected"
    print("资源初始化完成")
    
    yield  # 应用运行
    
    # === Shutdown ===
    print("\n收到关闭信号，开始清理...")
    
    # 刷新缓存
    print("刷新缓存...")
    await asyncio.sleep(1)
    resources["cache"] = None
    
    # 关闭数据库连接
    print("关闭数据库连接...")
    await asyncio.sleep(1)
    resources["db"] = None
    
    print("清理完成，安全退出")

app = FastAPI(lifespan=lifespan)

@app.get("/")
async def root():
    return {"resources": resources}

if __name__ == "__main__":
    import asyncio
    uvicorn.run(
        "lifespan_cleanup:app",
        port=8000,
        timeout_graceful_shutdown=30,
    )
```

**示例 3：自定义信号处理**

```python
# custom_signal.py
import signal
import asyncio
from fastapi import FastAPI
import uvicorn

app = FastAPI()
shutdown_event = asyncio.Event()

@app.get("/")
async def root():
    return {"status": "running"}

@app.get("/health")
async def health():
    if shutdown_event.is_set():
        return {"status": "shutting_down"}, 503
    return {"status": "healthy"}

def handle_signal(signum, frame):
    print(f"\n收到信号 {signum}，准备关闭...")
    shutdown_event.set()

if __name__ == "__main__":
    # 注册信号处理器
    signal.signal(signal.SIGTERM, handle_signal)
    signal.signal(signal.SIGINT, handle_signal)
    
    uvicorn.run("custom_signal:app", port=8000)
```

**示例 4：Gunicorn 的平滑重启**

```bash
# 启动 Gunicorn
gunicorn main:app \
    -w 4 \
    -k uvicorn.workers.UvicornWorker \
    -b 0.0.0.0:8000 \
    --pid /tmp/gunicorn.pid \
    --graceful-timeout 30

# 更新代码后，发送 SIGHUP 平滑重启
kill -HUP $(cat /tmp/gunicorn.pid)

# Gunicorn 会:
# 1. 启动新的 Worker 进程
# 2. 旧 Worker 停止接受新请求
# 3. 旧 Worker 处理完当前请求后退出
# 4. 整个过程零停机
```

**示例 5：K8s 中的优雅关闭**

```yaml
# deployment.yaml
spec:
  template:
    spec:
      containers:
      - name: myapp
        lifecycle:
          preStop:
            exec:
              command: ["/bin/sh", "-c", "sleep 10"]
      terminationGracePeriodSeconds: 30
```

```python
# 应用代码配合
from fastapi import FastAPI
import asyncio
import signal

app = FastAPI()
shutting_down = False

@app.on_event("startup")
async def startup():
    # 注册信号处理
    loop = asyncio.get_event_loop()
    for sig in (signal.SIGTERM, signal.SIGINT):
        loop.add_signal_handler(sig, handle_shutdown)

def handle_shutdown():
    global shutting_down
    shutting_down = True

@app.get("/health")
async def health():
    if shutting_down:
        return {"status": "shutting_down"}, 503
    return {"status": "healthy"}

@app.get("/ready")
async def ready():
    if shutting_down:
        return {"status": "not_ready"}, 503
    return {"status": "ready"}
```

### 总结

- SIGINT（Ctrl+C）和 SIGTERM 触发优雅关闭
- 优雅关闭流程：停止接受新连接 → 处理完当前请求 → 关闭连接 → 退出
- `--timeout-graceful-shutdown` 控制优雅关闭超时（默认 30 秒）
- Lifespan 的 shutdown 阶段用于资源清理
- Gunicorn 用 `kill -HUP` 实现零停机平滑重启
- K8s 中用 `preStop` 钩子 + `terminationGracePeriodSeconds` 实现优雅关闭
- 健康检查端点应返回 503，让负载均衡移除即将关闭的实例

---

## 第33讲 监控与可观测性

### 概念

**可观测性（Observability）** 是指从系统外部行为推断内部状态的能力，包含三大支柱：**日志（Logging）**、**指标（Metrics）**、**追踪（Tracing）**。本讲讲解如何为 Uvicorn 应用建立完整的可观测性体系。

### 原理

**可观测性三大支柱**：

```
┌─────────────────────────────────────────────┐
│              可观测性体系                    │
├─────────────────────────────────────────────┤
│                                             │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐ │
│  │  日志    │  │  指标    │  │  追踪    │ │
│  │ Logging  │  │ Metrics  │  │ Tracing  │ │
│  │          │  │          │  │          │ │
│  │ 事件记录 │  │ 数值统计 │  │ 请求链路 │ │
│  │ ELK/Loki │  │Prometheus│  │ Jaeger   │ │
│  └──────────┘  └──────────┘  └──────────┘ │
│                                             │
└─────────────────────────────────────────────┘
```

**三者关系**：
- **日志**：记录离散事件，用于排查具体问题
- **指标**：聚合数值，用于监控和告警
- **追踪**：请求级链路，用于性能分析

**常用工具栈**：
- 日志：ELK（Elasticsearch + Logstash + Kibana）、Loki + Grafana
- 指标：Prometheus + Grafana
- 追踪：Jaeger、Zipkin、OpenTelemetry

### 例子

**示例 1：Prometheus 指标集成**

```bash
pip install prometheus-client prometheus-fastapi-instrumentator
```

```python
# metrics_prometheus.py
from prometheus_fastapi_instrumentator import Instrumentator
from fastapi import FastAPI
import uvicorn

app = FastAPI()

# 注册 Prometheus 指标
Instrumentator().instrument(app).expose(app)

@app.get("/")
async def root():
    return {"message": "Hello"}

@app.get("/slow")
async def slow():
    import asyncio
    await asyncio.sleep(0.5)
    return {"status": "slow"}

if __name__ == "__main__":
    uvicorn.run("metrics_prometheus:app", port=8000)

# 访问 http://localhost:8000/metrics 查看 Prometheus 指标
```

**示例 2：自定义指标**

```python
# custom_metrics.py
from prometheus_client import Counter, Histogram, Gauge
from fastapi import FastAPI, Request
import time
import uvicorn

app = FastAPI()

# 自定义指标
REQUEST_COUNT = Counter(
    "http_requests_total",
    "Total HTTP requests",
    ["method", "endpoint", "status"]
)

REQUEST_LATENCY = Histogram(
    "http_request_duration_seconds",
    "HTTP request latency",
    ["method", "endpoint"],
    buckets=[0.01, 0.05, 0.1, 0.5, 1, 5]
)

ACTIVE_REQUESTS = Gauge(
    "http_active_requests",
    "Active HTTP requests"
)

BUSINESS_OPERATIONS = Counter(
    "business_operations_total",
    "Business operations",
    ["operation", "status"]
)

@app.middleware("http")
async def metrics_middleware(request: Request, call_next):
    ACTIVE_REQUESTS.inc()
    start_time = time.time()
    
    try:
        response = await call_next(request)
        status = response.status_code
    except Exception:
        status = 500
        raise
    finally:
        ACTIVE_REQUESTS.dec()
        duration = time.time() - start_time
        
        REQUEST_COUNT.labels(
            method=request.method,
            endpoint=request.url.path,
            status=status
        ).inc()
        
        REQUEST_LATENCY.labels(
            method=request.method,
            endpoint=request.url.path
        ).observe(duration)
    
    return response

@app.post("/users")
async def create_user():
    # 业务逻辑
    BUSINESS_OPERATIONS.labels(operation="create_user", status="success").inc()
    return {"id": 1}

if __name__ == "__main__":
    uvicorn.run("custom_metrics:app", port=8000)
```

**示例 3：OpenTelemetry 追踪**

```bash
pip install opentelemetry-api opentelemetry-sdk opentelemetry-instrumentation-fastapi
```

```python
# tracing_otel.py
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor, ConsoleSpanExporter
from opentelemetry.instrumentation.fastapi import FastAPIInstrumentor
from fastapi import FastAPI
import uvicorn

# 设置追踪
trace.set_tracer_provider(TracerProvider())
trace.get_tracer_provider().add_span_processor(
    BatchSpanProcessor(ConsoleSpanExporter())
)

app = FastAPI()
FastAPIInstrumentor.instrument_app(app)

tracer = trace.get_tracer(__name__)

@app.get("/")
async def root():
    return {"message": "Hello"}

@app.get("/users/{user_id}")
async def get_user(user_id: int):
    with tracer.start_as_current_span("fetch_user_from_db"):
        # 模拟数据库查询
        import asyncio
        await asyncio.sleep(0.1)
    
    with tracer.start_as_current_span("process_user_data"):
        # 模拟数据处理
        import asyncio
        await asyncio.sleep(0.05)
    
    return {"user_id": user_id, "name": f"User {user_id}"}

if __name__ == "__main__":
    uvicorn.run("tracing_otel:app", port=8000)
```

**示例 4：结构化日志**

```python
# structured_logging.py
import logging
import json
import sys
from datetime import datetime
from fastapi import FastAPI, Request
import uvicorn

class JsonFormatter(logging.Formatter):
    def format(self, record):
        log_data = {
            "timestamp": datetime.utcnow().isoformat() + "Z",
            "level": record.levelname,
            "logger": record.name,
            "message": record.getMessage(),
            "module": record.module,
            "function": record.funcName,
            "line": record.lineno,
        }
        
        # 添加请求上下文
        if hasattr(record, "request_id"):
            log_data["request_id"] = record.request_id
        if hasattr(record, "method"):
            log_data["method"] = record.method
        if hasattr(record, "path"):
            log_data["path"] = record.path
        if hasattr(record, "status_code"):
            log_data["status_code"] = record.status_code
        if hasattr(record, "duration_ms"):
            log_data["duration_ms"] = record.duration_ms
        
        if record.exc_info:
            log_data["exception"] = self.formatException(record.exc_info)
        
        return json.dumps(log_data, ensure_ascii=False)

# 配置日志
handler = logging.StreamHandler(sys.stdout)
handler.setFormatter(JsonFormatter())
logging.basicConfig(level=logging.INFO, handlers=[handler])

logger = logging.getLogger("myapp")

app = FastAPI()

@app.middleware("http")
async def log_requests(request: Request, call_next):
    import time
    import uuid
    
    request_id = str(uuid.uuid4())[:8]
    start = time.time()
    
    response = await call_next(request)
    
    duration = (time.time() - start) * 1000
    
    # 结构化日志
    logger.info(
        "Request processed",
        extra={
            "request_id": request_id,
            "method": request.method,
            "path": request.url.path,
            "status_code": response.status_code,
            "duration_ms": round(duration, 2),
        }
    )
    
    response.headers["X-Request-ID"] = request_id
    return response

@app.get("/")
async def root():
    logger.info("Handling root endpoint")
    return {"message": "Hello"}

if __name__ == "__main__":
    uvicorn.run("structured_logging:app", port=8000, log_level="info")
```

**示例 5：健康检查端点**

```python
# health_check.py
from fastapi import FastAPI
import psutil
import asyncio
import httpx
import uvicorn

app = FastAPI()

# 全局状态
health_status = {
    "db": "unknown",
    "redis": "unknown",
    "external_api": "unknown",
}

@app.on_event("startup")
async def startup():
    # 启动后台健康检查
    asyncio.create_task(periodic_health_check())

async def periodic_health_check():
    """定期检查依赖服务"""
    while True:
        try:
            # 检查数据库
            health_status["db"] = "healthy"
        except Exception:
            health_status["db"] = "unhealthy"
        
        try:
            # 检查 Redis
            health_status["redis"] = "healthy"
        except Exception:
            health_status["redis"] = "unhealthy"
        
        try:
            # 检查外部 API
            async with httpx.AsyncClient() as client:
                resp = await client.get("https://httpbin.org/status/200", timeout=5)
                health_status["external_api"] = "healthy" if resp.status_code == 200 else "unhealthy"
        except Exception:
            health_status["external_api"] = "unhealthy"
        
        await asyncio.sleep(30)

@app.get("/health")
async def health():
    """存活检查"""
    return {"status": "alive"}

@app.get("/ready")
async def ready():
    """就绪检查"""
    all_healthy = all(v == "healthy" for v in health_status.values())
    return {
        "status": "ready" if all_healthy else "not_ready",
        "checks": health_status,
    }

@app.get("/metrics/health")
async def detailed_health():
    """详细健康信息"""
    return {
        "status": health_status,
        "system": {
            "cpu_percent": psutil.cpu_percent(),
            "memory_percent": psutil.virtual_memory().percent,
            "disk_percent": psutil.disk_usage("/").percent,
        },
    }

if __name__ == "__main__":
    uvicorn.run("health_check:app", port=8000)
```

### 总结

- 可观测性三大支柱：日志、指标、追踪
- Prometheus + Grafana 是指标监控的标准方案
- OpenTelemetry 是追踪的统一标准
- 结构化日志（JSON）便于 ELK/Loki 采集和分析
- 健康检查端点：`/health`（存活）、`/ready`（就绪）
- 请求 ID 贯穿日志和追踪，便于关联排查
- 生产环境必须建立完整的监控告警体系

---

## 第34讲 安全加固

### 概念

**安全加固**是生产环境部署的必要步骤。本讲讲解 Uvicorn 应用的安全最佳实践，包括网络安全、认证授权、输入验证、依赖安全、配置安全等方面。

### 原理

**安全防护层次**：

```
┌─────────────────────────────────────────┐
│              互联网                      │
├─────────────────────────────────────────┤
│  ┌───────────────────────────────────┐  │
│  │  WAF / CloudFlare（DDoS 防护）    │  │
│  └───────────────────────────────────┘  │
├─────────────────────────────────────────┤
│  ┌───────────────────────────────────┐  │
│  │  Nginx（SSL、限流、安全头）       │  │
│  └───────────────────────────────────┘  │
├─────────────────────────────────────────┤
│  ┌───────────────────────────────────┐  │
│  │  Uvicorn（非 root、资源限制）     │  │
│  └───────────────────────────────────┘  │
├─────────────────────────────────────────┤
│  ┌───────────────────────────────────┐  │
│  │  应用（认证、授权、输入验证）     │  │
│  └───────────────────────────────────┘  │
├─────────────────────────────────────────┤
│  ┌───────────────────────────────────┐  │
│  │  数据库（加密、最小权限）         │  │
│  └───────────────────────────────────┘  │
└─────────────────────────────────────────┘
```

**安全原则**：
1. **最小权限**：只授予必要的权限
2. **纵深防御**：多层安全防护
3. **默认安全**：默认配置应是安全的
4. **失败安全**：出错时拒绝访问而非允许
5. **不信任输入**：所有输入都需验证

### 例子

**示例 1：安全响应头**

```python
# security_headers.py
from fastapi import FastAPI
from starlette.middleware.base import BaseHTTPMiddleware
import uvicorn

class SecurityHeadersMiddleware(BaseHTTPMiddleware):
    async def dispatch(self, request, call_next):
        response = await call_next(request)
        response.headers["X-Content-Type-Options"] = "nosniff"
        response.headers["X-Frame-Options"] = "DENY"
        response.headers["X-XSS-Protection"] = "1; mode=block"
        response.headers["Referrer-Policy"] = "strict-origin-when-cross-origin"
        response.headers["Permissions-Policy"] = "geolocation=(), microphone=()"
        response.headers["Strict-Transport-Security"] = "max-age=31536000; includeSubDomains"
        response.headers["Content-Security-Policy"] = "default-src 'self'"
        return response

app = FastAPI()
app.add_middleware(SecurityHeadersMiddleware)

@app.get("/")
async def root():
    return {"message": "Secure"}

if __name__ == "__main__":
    uvicorn.run("security_headers:app", port=8000)
```

**示例 2：速率限制**

```python
# rate_limiting.py
import time
from collections import defaultdict
from fastapi import FastAPI, Request, HTTPException
from fastapi.responses import JSONResponse
import uvicorn

app = FastAPI()

# 简单的内存速率限制（生产用 Redis）
rate_limit_store = defaultdict(list)

RATE_LIMIT = 100  # 每分钟 100 请求
RATE_WINDOW = 60  # 60 秒窗口

@app.middleware("http")
async def rate_limit(request: Request, call_next):
    client_ip = request.client.host
    current_time = time.time()
    
    # 清理过期记录
    rate_limit_store[client_ip] = [
        t for t in rate_limit_store[client_ip]
        if current_time - t < RATE_WINDOW
    ]
    
    # 检查速率
    if len(rate_limit_store[client_ip]) >= RATE_LIMIT:
        return JSONResponse(
            status_code=429,
            content={"error": "Rate limit exceeded"},
            headers={
                "Retry-After": str(RATE_WINDOW),
                "X-RateLimit-Limit": str(RATE_LIMIT),
                "X-RateLimit-Remaining": "0",
            }
        )
    
    # 记录请求
    rate_limit_store[client_ip].append(current_time)
    
    response = await call_next(request)
    response.headers["X-RateLimit-Limit"] = str(RATE_LIMIT)
    response.headers["X-RateLimit-Remaining"] = str(
        RATE_LIMIT - len(rate_limit_store[client_ip])
    )
    return response

@app.get("/")
async def root():
    return {"message": "Hello"}

if __name__ == "__main__":
    uvicorn.run("rate_limiting:app", port=8000)
```

**示例 3：CORS 配置**

```python
# cors_security.py
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
import uvicorn

app = FastAPI()

# 正确的 CORS 配置
app.add_middleware(
    CORSMiddleware,
    # 不要用 "*"，明确列出允许的源
    allow_origins=[
        "https://example.com",
        "https://app.example.com",
        "http://localhost:3000",  # 开发环境
    ],
    allow_credentials=True,
    allow_methods=["GET", "POST", "PUT", "DELETE", "PATCH"],
    allow_headers=[
        "Authorization",
        "Content-Type",
        "X-Request-ID",
    ],
    max_age=3600,  # 预检请求缓存 1 小时
)

@app.get("/")
async def root():
    return {"message": "Hello"}

if __name__ == "__main__":
    uvicorn.run("cors_security:app", port=8000)
```

**示例 4：输入验证与 SQL 注入防护**

```python
# input_validation.py
from fastapi import FastAPI, HTTPException, Query
from pydantic import BaseModel, validator, constr
import re
import uvicorn

app = FastAPI()

class UserCreate(BaseModel):
    username: constr(min_length=3, max_length=20, pattern=r"^[a-zA-Z0-9_]+$")
    email: constr(pattern=r"^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$")
    password: constr(min_length=8, max_length=128)
    age: int | None = None
    
    @validator("age")
    def validate_age(cls, v):
        if v is not None and (v < 0 or v > 150):
            raise ValueError("Age must be between 0 and 150")
        return v
    
    @validator("password")
    def validate_password(cls, v):
        if not re.search(r"[A-Z]", v):
            raise ValueError("Password must contain uppercase")
        if not re.search(r"[a-z]", v):
            raise ValueError("Password must contain lowercase")
        if not re.search(r"\d", v):
            raise ValueError("Password must contain digit")
        return v

@app.post("/users")
async def create_user(user: UserCreate):
    # Pydantic 已经验证输入
    return {"username": user.username, "email": user.email}

@app.get("/search")
async def search(
    q: str = Query(..., min_length=1, max_length=100),
    page: int = Query(1, ge=1),
    size: int = Query(10, ge=1, le=100),
):
    # 使用参数化查询防止 SQL 注入
    # 错误: f"SELECT * FROM users WHERE name = '{q}'"
    # 正确: 使用 ORM 或参数化查询
    return {"query": q, "page": page, "size": size}

if __name__ == "__main__":
    uvicorn.run("input_validation:app", port=8000)
```

**示例 5：JWT 认证**

```python
# jwt_auth.py
from datetime import datetime, timedelta
from typing import Optional
import jwt
from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials
from pydantic import BaseModel
import uvicorn

SECRET_KEY = "your-secret-key-keep-it-safe"
ALGORITHM = "HS256"

app = FastAPI()
security = HTTPBearer()

class TokenData(BaseModel):
    username: str
    exp: datetime

def create_access_token(data: dict, expires_delta: Optional[timedelta] = None):
    to_encode = data.copy()
    expire = datetime.utcnow() + (expires_delta or timedelta(minutes=30))
    to_encode.update({"exp": expire})
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)

async def get_current_user(credentials: HTTPAuthorizationCredentials = Depends(security)):
    token = credentials.credentials
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        username = payload.get("sub")
        if username is None:
            raise HTTPException(status_code=401, detail="Invalid token")
        return username
    except jwt.ExpiredSignatureError:
        raise HTTPException(status_code=401, detail="Token expired")
    except jwt.InvalidTokenError:
        raise HTTPException(status_code=401, detail="Invalid token")

@app.post("/login")
async def login(username: str, password: str):
    # 验证用户名密码（实际应用中查数据库）
    if username == "admin" and password == "password":
        token = create_access_token(
            data={"sub": username},
            expires_delta=timedelta(hours=1)
        )
        return {"access_token": token, "token_type": "bearer"}
    raise HTTPException(status_code=401, detail="Invalid credentials")

@app.get("/protected")
async def protected(current_user: str = Depends(get_current_user)):
    return {"user": current_user, "message": "Access granted"}

if __name__ == "__main__":
    uvicorn.run("jwt_auth:app", port=8000)
```

### 总结

- 安全防护是多层防御：WAF → Nginx → Uvicorn → 应用 → 数据库
- 安全响应头（CSP、HSTS、X-Frame-Options 等）防止常见攻击
- 速率限制防止暴力破解和 DDoS
- CORS 不要用 `*`，明确列出允许的源
- 输入验证用 Pydantic，SQL 用参数化查询
- JWT 认证设置合理过期时间，敏感操作需二次验证
- 定期更新依赖，扫描已知漏洞（`pip-audit`、`safety`）

---

## 第35讲 生产环境 Checklist 与故障排查

### 概念

本讲是 Uvicorn 教程的最后一讲，提供完整的生产环境部署 Checklist 和常见故障排查指南。这是将前面所有知识融会贯通的实践总结。

### 原理

**生产环境部署的三大维度**：

1. **配置正确性**：所有参数配置合理
2. **运行稳定性**：服务能稳定运行，异常能自动恢复
3. **可观测性**：能及时发现问题并定位原因

**故障排查方法论**：
1. **现象收集**：什么时间、什么操作、什么错误
2. **日志分析**：查看应用日志、系统日志、访问日志
3. **指标分析**：CPU、内存、网络、错误率
4. **假设验证**：提出假设，验证排除
5. **根因定位**：找到根本原因，而非表面现象

### 例子

**示例 1：生产环境 Checklist**

```markdown
## 生产环境部署 Checklist

### 基础配置
- [ ] Python 版本 >= 3.9
- [ ] 安装 uvicorn[standard]（含 uvloop、httptools）
- [ ] 使用虚拟环境隔离依赖
- [ ] requirements.txt 锁定版本
- [ ] .env 文件管理敏感配置
- [ ] DEBUG 模式已关闭

### Uvicorn 配置
- [ ] workers 数量根据 CPU 核心数设置
- [ ] loop=uvloop（Linux/macOS）
- [ ] http=httptools
- [ ] log_level=info（或 warning）
- [ ] access_log 启用（用于审计）
- [ ] proxy_headers=True（反向代理后）
- [ ] timeout_keep_alive 合理设置
- [ ] limit_concurrency 设置上限
- [ ] limit_max_requests 防止内存泄漏

### 安全配置
- [ ] 非 root 用户运行
- [ ] 文件描述符限制 >= 65536
- [ ] SSL 证书有效（或由 Nginx 处理）
- [ ] 安全响应头已配置
- [ ] CORS 配置正确（非 *）
- [ ] 速率限制已启用
- [ ] 认证授权已实现
- [ ] 输入验证已启用

### 部署架构
- [ ] Nginx 反向代理已配置
- [ ] SSL 由 Nginx 处理
- [ ] 静态文件由 Nginx 服务
- [ ] Gunicorn 管理 Uvicorn Worker
- [ ] systemd 或 Docker 管理进程
- [ ] 自动重启已配置

### 监控告警
- [ ] 健康检查端点 /health 和 /ready
- [ ] Prometheus 指标已暴露
- [ ] Grafana 仪表盘已配置
- [ ] 日志收集已配置（ELK/Loki）
- [ ] 告警规则已设置
- [ ] 错误率告警
- [ ] 延迟告警（P95/P99）
- [ ] 资源使用告警（CPU/内存）

### 备份恢复
- [ ] 数据库定期备份
- [ ] 配置文件版本控制
- [ ] 镜像版本管理
- [ ] 回滚方案已准备
```

**示例 2：常见故障排查**

```python
# troubleshooting.py
"""常见故障及排查方法"""

# === 故障 1：服务无法启动 ===
# 现象：uvicorn 启动后立即退出
# 排查：
# 1. 检查端口是否被占用
#    lsof -i :8000
# 2. 检查应用代码是否有语法错误
#    python -c "import main"
# 3. 检查依赖是否安装
#    pip install -r requirements.txt
# 4. 查看错误日志
#    uvicorn main:app --log-level debug

# === 故障 2：502 Bad Gateway ===
# 现象：Nginx 返回 502
# 排查：
# 1. 检查 Uvicorn 是否运行
#    ps aux | grep uvicorn
# 2. 检查 Uvicorn 端口
#    curl http://127.0.0.1:8000/health
# 3. 检查 Nginx 配置的 proxy_pass 地址
# 4. 检查 Nginx 错误日志
#    tail -f /var/log/nginx/error.log

# === 故障 3：请求超时 ===
# 现象：请求长时间无响应
# 排查：
# 1. 检查是否有阻塞操作
#    - 同步 I/O（用 aiofiles/httpx）
#    - CPU 密集型（用 run_in_executor）
# 2. 检查数据库查询是否慢
#    - 添加索引
#    - 使用 EXPLAIN ANALYZE
# 3. 检查外部 API 调用
#    - 设置超时
#    - 使用断路器
# 4. 检查 Worker 数是否足够

# === 故障 4：内存持续增长 ===
# 现象：内存使用持续上升
# 排查：
# 1. 启用 limit_max_requests 定期重启
# 2. 使用 tracemalloc 定位泄漏
#    import tracemalloc
#    tracemalloc.start()
# 3. 检查全局变量是否持续增长
# 4. 检查缓存是否有上限
# 5. 使用 mem_top 或 objgraph 分析

# === 故障 5：WebSocket 连接断开 ===
# 现象：WebSocket 频繁断开
# 排查：
# 1. 检查 Nginx 的 proxy_read_timeout
# 2. 实现心跳机制
# 3. 检查网络中间件（防火墙、负载均衡）
# 4. 检查 Uvicorn 的 ws 配置

# === 故障 6：CPU 使用率过高 ===
# 现象：CPU 持续 100%
# 排查：
# 1. 使用 py-spy 分析
#    py-spy top --pid <PID>
# 2. 检查是否有死循环
# 3. 检查是否 CPU 密集型任务未用线程池
# 4. 增加 Worker 数
# 5. 优化算法
```

**示例 3：内存泄漏排查脚本**

```python
# memory_leak_debug.py
"""内存泄漏排查工具"""
import tracemalloc
import gc
import psutil
import os
from fastapi import FastAPI
import uvicorn

app = FastAPI()

# 启动内存跟踪
tracemalloc.start(10)  # 保存 10 帧

# 模拟内存泄漏
leak_data = []

@app.get("/leak")
async def leak():
    """模拟内存泄漏"""
    leak_data.append(["data"] * 10000)  # 每次泄漏一些数据
    return {"leak_size": len(leak_data)}

@app.get("/memory/snapshot")
async def memory_snapshot():
    """获取内存快照"""
    snapshot = tracemalloc.take_snapshot()
    top_stats = snapshot.statistics("lineno")
    
    # 获取前 10 个内存占用
    top = []
    for stat in top_stats[:10]:
        top.append({
            "file": str(stat.traceback),
            "size_mb": round(stat.size / 1024 / 1024, 2),
            "count": stat.count,
        })
    
    # 进程内存
    process = psutil.Process(os.getpid())
    mem = process.memory_info()
    
    return {
        "process_memory_mb": round(mem.rss / 1024 / 1024, 2),
        "top_allocations": top,
        "gc_objects": len(gc.get_objects()),
    }

@app.get("/memory/gc")
async def force_gc():
    """强制垃圾回收"""
    before = psutil.Process(os.getpid()).memory_info().rss
    gc.collect()
    after = psutil.Process(os.getpid()).memory_info().rss
    return {
        "before_mb": round(before / 1024 / 1024, 2),
        "after_mb": round(after / 1024 / 1024, 2),
        "freed_mb": round((before - after) / 1024 / 1024, 2),
    }

if __name__ == "__main__":
    uvicorn.run("memory_leak_debug:app", port=8000)
```

**示例 4：完整的生产启动脚本**

```python
# production_main.py
"""生产环境启动入口"""
import os
import logging
import uvicorn
from fastapi import FastAPI
from contextlib import asynccontextmanager

# 配置日志
logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s [%(levelname)s] %(name)s: %(message)s",
)
logger = logging.getLogger(__name__)

@asynccontextmanager
async def lifespan(app: FastAPI):
    """应用生命周期"""
    logger.info("Application starting...")
    
    # 初始化资源
    # - 数据库连接池
    # - Redis 客户端
    # - ML 模型加载
    
    logger.info("Application started successfully")
    yield
    
    logger.info("Application shutting down...")
    # 清理资源
    logger.info("Application stopped")

app = FastAPI(
    title="Production API",
    version="1.0.0",
    lifespan=lifespan,
    docs_url="/docs" if os.getenv("ENV") != "production" else None,
    redoc_url="/redoc" if os.getenv("ENV") != "production" else None,
)

@app.get("/health")
async def health():
    return {"status": "alive"}

@app.get("/ready")
async def ready():
    return {"status": "ready"}

@app.get("/")
async def root():
    return {"service": "production-api", "version": "1.0.0"}

if __name__ == "__main__":
    # 生产环境配置
    config = {
        "app": "production_main:app",
        "host": "0.0.0.0",
        "port": int(os.getenv("PORT", "8000")),
        "workers": int(os.getenv("WORKERS", "4")),
        "loop": "uvloop",
        "http": "httptools",
        "log_level": os.getenv("LOG_LEVEL", "info"),
        "access_log": True,
        "proxy_headers": True,
        "forwarded_allow_ips": "*",
        "timeout_keep_alive": 65,
        "timeout_graceful_shutdown": 30,
        "limit_concurrency": 1000,
        "limit_max_requests": 10000,
    }
    
    logger.info(f"Starting with config: {config}")
    uvicorn.run(**config)
```

**示例 5：Docker Compose 完整生产配置**

```yaml
# docker-compose.prod.yml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile.prod
    container_name: myapp
    ports:
      - "127.0.0.1:8000:8000"  # 只监听本地，由 Nginx 转发
    environment:
      - DATABASE_URL=postgresql://user:${DB_PASSWORD}@db:5432/myapp
      - REDIS_URL=redis://redis:6379/0
      - SECRET_KEY=${SECRET_KEY}
      - ENV=production
      - WORKERS=4
      - LOG_LEVEL=info
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_started
    restart: always
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
      interval: 30s
      timeout: 5s
      retries: 3
      start_period: 30s
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 1G
        reservations:
          cpus: '0.5'
          memory: 256M
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
    networks:
      - backend

  db:
    image: postgres:15-alpine
    container_name: myapp_db
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: ${DB_PASSWORD}
      POSTGRES_DB: myapp
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./db/backups:/backups
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user -d myapp"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: always
    networks:
      - backend

  redis:
    image: redis:7-alpine
    container_name: myapp_redis
    command: redis-server --requirepass ${REDIS_PASSWORD}
    restart: always
    networks:
      - backend

  nginx:
    image: nginx:alpine
    container_name: myapp_nginx
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
      - ./nginx/conf.d:/etc/nginx/conf.d:ro
      - ./certs:/etc/nginx/certs:ro
      - ./static:/var/www/static:ro
      - nginx_logs:/var/log/nginx
    depends_on:
      - app
    restart: always
    networks:
      - backend

  prometheus:
    image: prom/prometheus:latest
    container_name: myapp_prometheus
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml:ro
      - prometheus_data:/prometheus
    ports:
      - "9090:9090"
    restart: always
    networks:
      - backend

  grafana:
    image: grafana/grafana:latest
    container_name: myapp_grafana
    volumes:
      - grafana_data:/var/lib/grafana
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=${GRAFANA_PASSWORD}
    restart: always
    networks:
      - backend

volumes:
  postgres_data:
  prometheus_data:
  grafana_data:
  nginx_logs:

networks:
  backend:
    driver: bridge
```

### 总结

- 生产部署需遵循完整 Checklist：配置、安全、部署、监控、备份
- 故障排查遵循"现象→日志→指标→假设→根因"方法论
- 常见故障：启动失败、502、超时、内存泄漏、WebSocket 断开、CPU 过高
- 内存泄漏用 tracemalloc 定位，CPU 问题用 py-spy 分析
- 生产环境关闭文档端点（docs/redoc），避免信息泄露
- Docker Compose 编排完整生产栈：app + db + redis + nginx + prometheus + grafana
- 建立完善的监控告警，做到问题早发现、早处理

---

## 课程总结

恭喜你完成了 Uvicorn 教程的全部 35 讲！让我们回顾一下学习路径：

**第1章 基础入门**：理解了 Uvicorn 的定位、WSGI vs ASGI 的演进、安装和基本使用。

**第2章 ASGI 协议深入**：掌握了 ASGI 协议规范、Scope/Receive/Send 三要素、HTTP 生命周期和 Lifespan 机制。

**第3章 配置与运行**：学会了所有启动参数、Worker 进程管理、日志配置、SSL/HTTPS 和热重载。

**第4章 与框架集成**：掌握了 Uvicorn 与 FastAPI、Starlette、Django、Flask 的集成方式。

**第5章 异步与并发**：理解了 uvloop、httptools、WebSocket 和长连接管理的底层机制。

**第6章 部署实战**：学会了 Gunicorn+Uvicorn、Nginx 反向代理、Docker、systemd、K8s 五种部署方案。

**第7章 性能调优**：掌握了基准测试、Worker 调优、内存连接优化和瓶颈分析方法。

**第8章 高级特性与生产实践**：学会了中间件、优雅关闭、监控可观测性、安全加固和生产 Checklist。

**下一步学习建议**：
1. 深入阅读 Uvicorn 源码，理解 ASGI 服务器实现细节
2. 学习 Starlette 源码，理解 ASGI 框架设计
3. 研究 FastAPI 的高级特性（依赖注入、中间件、后台任务）
4. 实践 OpenTelemetry 分布式追踪
5. 探索其他 ASGI 服务器（Hypercorn、Daphne）的对比

希望本教程能帮助你在生产环境中自信地使用 Uvicorn！
