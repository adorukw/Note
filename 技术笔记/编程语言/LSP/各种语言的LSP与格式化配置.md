# 各种语言的 LSP 与格式化配置 —— 系统教程

> 本教程以教科书体例编写，共 36 讲、10 章。每讲遵循「概念 → 原理 → 例子 → 总结」四段式结构，从 LSP 协议基础出发，逐步深入到各主流语言的 LSP 服务器配置与代码格式化工具链，最终覆盖多语言项目协同、pre-commit 钩子与 CI/CD 集成等高级实战主题。

---

## 目录

- [第 1 章 LSP 基础理论](#第-1-章-lsp-基础理论)
  - [第 1 讲 LSP 是什么：协议起源与设计理念](#第-1-讲-lsp-是什么协议起源与设计理念)
  - [第 2 讲 LSP 架构原理：客户端/服务器模型与通信机制](#第-2-讲-lsp-架构原理客户端服务器模型与通信机制)
  - [第 3 讲 LSP 核心协议消息：请求、响应与通知](#第-3-讲-lsp-核心协议消息请求响应与通知)
  - [第 4 讲 LSP 生命周期：初始化、能力协商与关闭](#第-4-讲-lsp-生命周期初始化能力协商与关闭)
- [第 2 章 编辑器集成与配置](#第-2-章-编辑器集成与配置)
  - [第 5 讲 Neovim 内置 LSP 客户端配置](#第-5-讲-neovim-内置-lsp-客户端配置)
  - [第 6 讲 VSCode LSP 集成机制](#第-6-讲-vscode-lsp-集成机制)
  - [第 7 讲 Emacs lsp-mode 与 eglot](#第-7-讲-emacs-lsp-mode-与-eglot)
  - [第 8 讲 Helix 等现代编辑器的 LSP 支持](#第-8-讲-helix-等现代编辑器的-lsp-支持)
- [第 3 章 格式化基础理论](#第-3-章-格式化基础理论)
  - [第 9 讲 代码格式化的意义与分类](#第-9-讲-代码格式化的意义与分类)
  - [第 10 讲 格式化与 LSP 的关系](#第-10-讲-格式化与-lsp-的关系)
  - [第 11 讲 formatOnSave 与编辑器钩子机制](#第-11-讲-formatonsave-与编辑器钩子机制)
- [第 4 章 Python LSP 与格式化](#第-4-章-python-lsp-与格式化)
  - [第 12 讲 Pyright 与 Pylsp：Python LSP 服务器对比](#第-12-讲-pyright-与-pylsppython-lsp-服务器对比)
  - [第 13 讲 Ruff：现代 Python Linter + Formatter](#第-13-讲-ruff现代-python-linter--formatter)
  - [第 14 讲 Black + isort 经典组合配置](#第-14-讲-black--isort-经典组合配置)
  - [第 15 讲 Python 项目综合配置实战](#第-15-讲-python-项目综合配置实战)
- [第 5 章 JavaScript/TypeScript](#第-5-章-javascripttypescript)
  - [第 16 讲 TypeScript Language Server 配置](#第-16-讲-typescript-language-server-配置)
  - [第 17 讲 ESLint 配置体系详解](#第-17-讲-eslint-配置体系详解)
  - [第 18 讲 Prettier 配置详解](#第-18-讲-prettier-配置详解)
  - [第 19 讲 ESLint + Prettier 协同方案](#第-19-讲-eslint--prettier-协同方案)
  - [第 20 讲 Monorepo 中的配置管理](#第-20-讲-monorepo-中的配置管理)
- [第 6 章 Rust](#第-6-章-rust)
  - [第 21 讲 rust-analyzer 配置详解](#第-21-讲-rust-analyzer-配置详解)
  - [第 22 讲 rustfmt 配置](#第-22-讲-rustfmt-配置)
  - [第 23 讲 Clippy 集成与 Lint 规则](#第-23-讲-clippy-集成与-lint-规则)
- [第 7 章 Go](#第-7-章-go)
  - [第 24 讲 gopls 配置](#第-24-讲-gopls-配置)
  - [第 25 讲 gofmt 与 goimports](#第-25-讲-gofmt-与-goimports)
  - [第 26 讲 golangci-lint 集成](#第-26-讲-golangci-lint-集成)
- [第 8 章 C/C++](#第-8-章-cc)
  - [第 27 讲 clangd 配置](#第-27-讲-clangd-配置)
  - [第 28 讲 clang-format 配置文件详解](#第-28-讲-clang-format-配置文件详解)
  - [第 29 讲 compile_commands.json 生成与管理](#第-29-讲-compile_commandsjson-生成与管理)
- [第 9 章 其他主流语言](#第-9-章-其他主流语言)
  - [第 30 讲 Java：jdtls 与 Google Java Format](#第-30-讲-javajdtls-与-google-java-format)
  - [第 31 讲 Ruby：Solargraph 与 RuboCop](#第-31-讲-rubysolargraph-与-rubocop)
  - [第 32 讲 Lua：lua-language-server 与 StyLua](#第-32-讲-lualua-language-server-与-stylua)
  - [第 33 讲 Shell：bash-language-server 与 shfmt](#第-33-讲-shellbash-language-server-与-shfmt)
- [第 10 章 高级与实战](#第-10-章-高级与实战)
  - [第 34 讲 EditorConfig 跨工具协同](#第-34-讲-editorconfig-跨工具协同)
  - [第 35 讲 pre-commit hooks 集成](#第-35-讲-pre-commit-hooks-集成)
  - [第 36 讲 CI/CD 中的格式化与 Lint 检查](#第-36-讲-cicd-中的格式化与-lint-检查)

---

## 第 1 章 LSP 基础理论

本章是整门课程的基石。在深入任何具体语言的配置之前，我们必须先理解 Language Server Protocol（语言服务器协议，简称 LSP）究竟是什么、它解决了什么问题、它的架构如何运作。这四讲将从历史背景出发，逐步拆解 LSP 的通信模型、消息类型和生命周期，为你后续学习各语言的具体配置打下坚实的理论基础。

### 第 1 讲 LSP 是什么：协议起源与设计理念

#### 概念

Language Server Protocol（LSP，语言服务器协议）是微软在 2016 年为 VSCode 开发时提出的一套标准化协议，随后开源并提交给标准化组织。它的核心目标是定义编辑器（客户端）与语言分析服务（服务器）之间的通信规范，使得任意编辑器只需实现一次客户端逻辑，就能接入任意语言的语言服务器，从而获得自动补全、跳转定义、悬停文档、代码诊断等智能编辑能力。

#### 原理

在 LSP 出现之前，编辑器对编程语言的支持面临一个经典的 **M × N 问题**：如果有 M 个编辑器和 N 种语言，要让每个编辑器都支持每种语言的智能功能，就需要实现 M × N 套独立的集成代码。例如，Emacs 要支持 Python 补全需要写一套代码，Vim 要支持 Python 补全又要写另一套代码，Sublime 又是一套……这导致大量重复劳动，且小众编辑器很难获得高质量的语言支持。

LSP 的设计理念是将这个 M × N 问题降维成 **M + N 问题**：定义一套标准协议，每个编辑器只需实现一个 LSP 客户端（M 次），每种语言只需实现一个 LSP 服务器（N 次），两者通过协议自由组合。这就像 HTTP 协议统一了浏览器与 Web 服务器之间的通信一样，LSP 统一了编辑器与语言工具之间的通信。

协议的底层传输采用 **JSON-RPC 2.0**，这是一种轻量级的远程过程调用协议，使用 JSON 作为消息格式。LSP 规范定义了传输层可以是 stdio（标准输入输出流）、WebSocket 或 TCP socket，其中 stdio 是最常见的方式——编辑器直接启动语言服务器进程，通过管道与之通信。

#### 例子

以下是一个简化的 LSP 通信流程示意，展示编辑器（客户端）与语言服务器之间的交互：

```
┌─────────────┐       JSON-RPC over stdio      ┌──────────────────┐
│   Editor     │  ←──────────────────────────→  │  Language Server  │
│  (Client)    │                                │    (Server)       │
└─────────────┘                                  └──────────────────┘
       │                                                  │
       │  1. initialize 请求 (capabilities)               │
       │ ──────────────────────────────────────────────→  │
       │                                                  │
       │  2. initialize 响应 (server capabilities)         │
       │ ←──────────────────────────────────────────────  │
       │                                                  │
       │  3. textDocument/didOpen 通知                    │
       │ ──────────────────────────────────────────────→  │
       │                                                  │
       │  4. textDocument/publishDiagnostics 通知         │
       │ ←──────────────────────────────────────────────  │
       │                                                  │
       │  5. textDocument/completion 请求                 │
       │ ──────────────────────────────────────────────→  │
       │                                                  │
       │  6. completion 响应 (补全列表)                    │
       │ ←──────────────────────────────────────────────  │
```

一条实际的 JSON-RPC 消息（以 `initialize` 请求为例）如下：

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "initialize",
  "params": {
    "processId": 12345,
    "rootUri": "file:///home/user/myproject",
    "capabilities": {
      "textDocument": {
        "completion": {
          "completionItem": {
            "snippetSupport": true
          }
        }
      }
    }
  }
}
```

#### 总结

- LSP 是微软提出的标准化协议，解决编辑器与语言服务之间的 M × N 集成问题，将其降为 M + N。
- 底层基于 JSON-RPC 2.0，传输方式通常为 stdio。
- 理解 LSP 的设计理念，有助于理解后续各编辑器和语言服务器的配置方式——所有配置本质上都是在调整协议参数。
- 常见注意事项：LSP 是协议而非实现，不同语言服务器对协议的支持程度不同，配置时需查阅具体服务器的文档。

---

### 第 2 讲 LSP 架构原理：客户端/服务器模型与通信机制

#### 概念

LSP 采用经典的客户端/服务器（Client/Server）架构。编辑器充当 LSP 客户端，负责管理用户交互、文件 I/O 和 UI 呈现；语言服务器（Language Server）是独立进程，负责语法分析、类型推断、代码索引等计算密集型任务。两者通过 JSON-RPC 消息进行双向异步通信。

#### 原理

**进程模型**：编辑器启动时会 fork 出一个或多个语言服务器子进程，每个语言服务器通常对应一个项目工作区（workspace）。客户端通过子进程的 stdin/stdout 管道发送和接收 JSON-RPC 消息。这种进程隔离的好处是：语言服务器崩溃不会拖垮编辑器，且可以用不同语言实现服务器（如 Python 的 pyright 用 TypeScript 写，Rust 的 rust-analyzer 用 Rust 写）。

**消息编码**：LSP 规范要求每条 JSON-RPC 消息前必须带有 `Content-Length` 头部，以字节为单位声明消息体的长度。这是因为 stdio 是流式传输，没有天然的消息边界，必须用长度前缀来分帧。完整的消息格式如下：

```
Content-Length: 230\r\n
\r\n
{"jsonrpc":"2.0","id":1,"method":"initialize","params":{...}}
```

**异步通信**：JSON-RPC 2.0 支持三种消息类型——请求（Request，有 id，需响应）、响应（Response，有 id，对应某个请求）、通知（Notification，无 id，无需响应）。客户端和服务器都可以主动发送通知，例如服务器主动推送诊断信息（`textDocument/publishDiagnostics`）。这种异步设计确保了 UI 不会因为语言服务器在处理耗时任务而卡顿。

**文档同步**：客户端不会把文件直接交给服务器读取，而是通过协议消息同步文档内容。当用户打开文件时，客户端发送 `textDocument/didOpen`；当用户编辑时，发送 `textDocument/didChange`（可以是全量替换或增量差异）；保存时发送 `textDocument/didSave`；关闭时发送 `textDocument/didClose`。服务器在内存中维护文档的当前状态，据此进行分析。

#### 例子

以下用 Python 伪代码展示一个最简化的 LSP 客户端如何读取消息：

```python
import json
import sys

def read_message():
    """从 stdin 读取一条 LSP 消息"""
    headers = {}
    while True:
        line = sys.stdin.readline()
        if line == '\r\n' or line == '':
            break
        key, value = line.strip().split(': ', 1)
        headers[key] = value
    
    content_length = int(headers['Content-Length'])
    body = sys.stdin.read(content_length)
    return json.loads(body)

def send_message(message):
    """向 stdout 写入一条 LSP 消息"""
    body = json.dumps(message)
    header = f"Content-Length: {len(body)}\r\n\r\n"
    sys.stdout.write(header + body)
    sys.stdout.flush()

# 接收 initialize 请求
request = read_message()
print(f"收到方法: {request['method']}", file=sys.stderr)

# 返回 initialize 响应
response = {
    "jsonrpc": "2.0",
    "id": request["id"],
    "result": {
        "capabilities": {
            "textDocumentSync": 1,  # 1 = Full 同步
            "completionProvider": {"resolveProvider": False},
            "definitionProvider": True
        },
        "serverInfo": {"name": "demo-ls", "version": "0.1.0"}
    }
}
send_message(response)
```

#### 总结

- LSP 采用进程隔离的客户端/服务器架构，编辑器是客户端，语言服务器是独立子进程。
- 消息通过 stdio 传输，使用 `Content-Length` 头部进行分帧，解决流式传输的消息边界问题。
- 三种消息类型（请求/响应/通知）支持双向异步通信，避免 UI 卡顿。
- 文档同步是 LSP 的核心机制：客户端通过 didOpen/didChange/didSave/didClose 通知服务器文档状态变化。
- 常见注意事项：调试 LSP 通信时，可以查看语言服务器的日志输出（通常重定向到文件），观察实际发送和接收的 JSON-RPC 消息。

---

### 第 3 讲 LSP 核心协议消息：请求、响应与通知

#### 概念

LSP 规范定义了丰富的消息类型，覆盖了代码编辑的方方面面。这些消息大致分为三类：**生命周期管理**（如 initialize、shutdown）、**文档同步**（如 didOpen、didChange）、**语言功能**（如 completion、hover、definition、references、diagnostics）。理解这些消息的语义是配置和调试 LSP 的关键。

#### 原理

LSP 消息的命名采用 `域/方法` 的格式，例如 `textDocument/completion` 表示对文本文档的补全请求。主要的消息域包括：

- **`initialize` / `initialized` / `shutdown` / `exit`**：生命周期管理，控制服务器的启动和关闭。
- **`textDocument/*`**：文档操作，包括同步（didOpen/didChange/didSave/didClose）和查询（completion/hover/definition/references/rename/formatting/codeAction 等）。
- **`workspace/*`**：工作区操作，如符号搜索（workspace/symbol）、配置变更通知（workspace/didChangeConfiguration）。
- **`window/*`**：窗口操作，服务器可向客户端展示消息（window/showMessage）或记录日志（window/logMessage）。

**能力协商（Capability Negotiation）**是 LSP 的一个核心设计。在 `initialize` 阶段，客户端和服务器互相声明自己支持哪些功能。例如，客户端声明支持 snippet 补全，服务器声明支持自动补全和跳转定义。后续通信中，双方只使用协商一致的能力，避免发送对方无法处理的消息。这种设计保证了向后兼容性——新版本的服务器可以利用新特性，同时不破坏旧客户端。

**诊断（Diagnostics）**是 LSP 中一个特殊的通知类型。与请求不同，诊断由服务器主动推送（`textDocument/publishDiagnostics`），客户端不需要主动请求。每当文档内容变化或项目配置变化时，服务器重新分析并推送最新的错误和警告信息。

#### 例子

以下展示几个核心消息的实际 JSON 结构：

**1. 悬停信息请求（textDocument/hover）**：

```json
// 请求
{
  "jsonrpc": "2.0",
  "id": 5,
  "method": "textDocument/hover",
  "params": {
    "textDocument": {"uri": "file:///src/main.rs"},
    "position": {"line": 10, "character": 15}
  }
}

// 响应
{
  "jsonrpc": "2.0",
  "id": 5,
  "result": {
    "contents": {
      "kind": "markdown",
      "value": "```rust\nfn main()\n```\n\n程序入口点"
    },
    "range": {
      "start": {"line": 10, "character": 12},
      "end": {"line": 10, "character": 16}
    }
  }
}
```

**2. 诊断通知（textDocument/publishDiagnostics）**：

```json
{
  "jsonrpc": "2.0",
  "method": "textDocument/publishDiagnostics",
  "params": {
    "uri": "file:///src/main.py",
    "diagnostics": [
      {
        "range": {
          "start": {"line": 5, "character": 0},
          "end": {"line": 5, "character": 10}
        },
        "severity": 1,
        "code": "F821",
        "source": "pyflakes",
        "message": "undefined name 'undefined_var'"
      }
    ]
  }
}
```

severity 字段的含义：1 = Error，2 = Warning，3 = Information，4 = Hint。

#### 总结

- LSP 消息分为生命周期管理、文档同步、语言功能三大类，命名采用 `域/方法` 格式。
- 能力协商机制确保客户端和服务器只使用双方都支持的功能，保证向后兼容。
- 诊断信息由服务器主动推送，不需要客户端请求，这是 LSP 中最常见的通知类型。
- 常见注意事项：position 中的 line 和 character 都是 **从 0 开始** 计数的，这是调试时最容易犯的错误之一。

---

### 第 4 讲 LSP 生命周期：初始化、能力协商与关闭

#### 概念

一个 LSP 会话从编辑器打开项目开始，到关闭项目结束，经历完整的生命周期：**初始化（initialize）→ 已初始化（initialized）→ 运行（正常运行阶段）→ 关闭（shutdown）→ 退出（exit）**。理解这个流程对于排查"语言服务器启动失败"等问题至关重要。

#### 原理

**第一阶段：初始化（initialize）**。客户端启动语言服务器进程后，必须发送的第一个请求就是 `initialize`。这个请求携带了客户端的能力声明（`capabilities`）、项目根路径（`rootUri` 或 `workspaceFolders`）以及初始化选项（`initializationOptions`，用于向服务器传递配置）。服务器在响应中返回自己的能力声明和服务器信息。**在收到 initialize 响应之前，服务器不得处理任何其他请求。**

**第二阶段：已初始化（initialized）**。客户端收到 initialize 响应后，发送 `initialized` 通知，告诉服务器"初始化完成，可以开始正常工作了"。此后，服务器可以开始接收文档同步通知和语言功能请求。

**第三阶段：正常运行**。这是最长的阶段，客户端和服务器持续交互：打开/编辑/关闭文档、请求补全/跳转/格式化等。服务器可能在此阶段进行项目索引（如 rust-analyzer 会解析整个 crate 的依赖树），这可能导致较高的 CPU 和内存占用。

**第四阶段：关闭（shutdown）**。当用户关闭项目或退出编辑器时，客户端发送 `shutdown` 请求。服务器收到后应停止处理新请求，但保持进程存活，等待 `exit` 通知。`shutdown` 的设计目的是让服务器有机会优雅地释放资源。

**第五阶段：退出（exit）**。客户端发送 `exit` 通知（不是请求，无需响应），服务器进程随即退出。如果服务器在未收到 `shutdown` 的情况下收到 `exit`，应以非零状态码退出（表示异常终止）。

**初始化选项（initializationOptions）**是配置语言服务器的关键途径。不同的服务器定义了自己的初始化选项模式，例如 rust-analyzer 接受 `cargo`、`checkOnSave`、`diagnostics` 等配置项；clangd 接受 `clangdArgs`、`fallbackFlags` 等。这些选项通常通过编辑器的 LSP 配置传递。

#### 例子

以下是一个完整的 LSP 生命周期交互序列，以 Neovim 启动 rust-analyzer 为例：

```
Client → Server:  initialize 请求
{
  "method": "initialize",
  "params": {
    "rootUri": "file:///home/user/my-rust-project",
    "initializationOptions": {
      "cargo": { "features": "all" },
      "checkOnSave": { "command": "clippy" }
    },
    "capabilities": {
      "textDocument": {
        "hover": { "contentFormat": ["markdown", "plaintext"] },
        "completion": { "completionItem": { "snippetSupport": true } }
      }
    }
  }
}

Server → Client:  initialize 响应
{
  "result": {
    "capabilities": {
      "textDocumentSync": 2,  // 2 = Incremental 增量同步
      "hoverProvider": true,
      "completionProvider": { "triggerCharacters": [".", ":"] },
      "definitionProvider": true,
      "inlayHintProvider": true
    }
  }
}

Client → Server:  initialized 通知
{ "method": "initialized", "params": {} }

Client → Server:  textDocument/didOpen 通知
{
  "method": "textDocument/didOpen",
  "params": {
    "textDocument": {
      "uri": "file:///home/user/my-rust-project/src/main.rs",
      "languageId": "rust",
      "version": 1,
      "text": "fn main() {\n    println!(\"Hello\");\n}\n"
    }
  }
}

Server → Client:  textDocument/publishDiagnostics 通知
{ "method": "textDocument/publishDiagnostics", "params": {...} }

... (正常运行阶段的各类请求/响应/通知) ...

Client → Server:  shutdown 请求
{ "method": "shutdown" }

Server → Client:  shutdown 响应
{ "result": null }

Client → Server:  exit 通知
{ "method": "exit" }

(服务器进程退出)
```

#### 总结

- LSP 生命周期严格遵循 initialize → initialized → 运行 → shutdown → exit 的顺序，违反顺序会导致协议错误。
- `initializationOptions` 是配置语言服务器的核心途径，不同服务器有不同的选项模式。
- 在正常运行阶段，服务器可能进行项目索引，导致资源占用升高，这是正常现象。
- 常见注意事项：如果语言服务器"不工作"，首先检查 initialize 请求是否成功（查看日志中的 initialize 响应），其次检查 rootUri 是否正确指向项目根目录。

---

## 第 2 章 编辑器集成与配置

理解了 LSP 协议本身后，本章聚焦于编辑器侧的 LSP 客户端配置。不同的编辑器有不同的配置方式和扩展生态，但核心概念相通——都需要配置服务器启动命令、初始化选项、快捷键映射和 UI 呈现。本章覆盖 Neovim、VSCode、Emacs 和 Helix 四种主流编辑器，帮助你建立跨编辑器的配置能力。

### 第 5 讲 Neovim 内置 LSP 客户端配置

#### 概念

自 0.5 版本起，Neovim 内置了 LSP 客户端（通过 `vim.lsp` 模块提供），无需任何插件即可连接语言服务器。配置的核心 API 是 `vim.lsp.start()` 或更高层的 `vim.lsp.config()` / `vim.lsp.enable()`（Neovim 0.11+ 引入的新 API）。社区还提供了 `nvim-lspconfig` 插件，预置了各语言服务器的默认配置，大幅简化配置工作。

#### 原理

Neovim 的 LSP 客户端在内部管理语言服务器进程的生命周期。当你调用 `vim.lsp.start()` 时，Neovim 会 fork 出指定的服务器命令进程，建立 stdio 通信管道，自动处理 JSON-RPC 消息的编解码。Neovim 将 LSP 返回的补全、诊断、悬停等信息通过内置 API 暴露给用户和插件使用。

**nvim-lspconfig** 的工作原理是为每种语言服务器提供一份默认配置表，包含：服务器命令及参数、文件类型匹配（`filetypes`）、项目根目录检测规则（`root_markers` 或 `root_dir`）、默认初始化选项。用户只需指定服务器名称和自定义覆盖项，插件会合并默认配置和用户配置后调用 `vim.lsp.start()`。

**根目录检测**是 LSP 配置中的关键概念。语言服务器需要知道项目的根目录来确定作用域（如查找 `pyproject.toml`、`Cargo.toml`、`tsconfig.json` 等项目标志文件）。lspconfig 通过 `root_markers` 定义每种语言的根目录标志文件，Neovim 在打开文件时向上搜索这些标志文件，找到的第一个目录即为项目根目录。

#### 例子

**方式一：使用 nvim-lspconfig（推荐，Neovim 0.11+ 新 API）**

```lua
-- ~/.config/nvim/lsp/rust_analyzer.lua
return {
    cmd = { 'rust-analyzer' },
    filetypes = { 'rust' },
    root_markers = { 'Cargo.toml', 'Cargo.lock' },
    settings = {
        ['rust-analyzer'] = {
            cargo = { features = 'all', allTargets = true },
            checkOnSave = { command = 'clippy' },
            inlayHints = { chainingHints = true },
        },
    },
}
```

```lua
-- ~/.config/nvim/init.lua 或 ~/.config/nvim/lsp/init.lua
-- 启用 LSP 服务器
vim.lsp.enable('rust_analyzer')
vim.lsp.enable('pyright')
vim.lsp.enable('ts_ls')

-- 全局 LSP 快捷键（在 LSP 附加到 buffer 时设置）
vim.api.nvim_create_autocmd('LspAttach', {
    callback = function(args)
        local opts = { buffer = args.buf }
        vim.keymap.set('n', 'gd', vim.lsp.buf.definition, opts)
        vim.keymap.set('n', 'gr', vim.lsp.buf.references, opts)
        vim.keymap.set('n', 'K',  vim.lsp.buf.hover, opts)
        vim.keymap.set('n', '<leader>rn', vim.lsp.buf.rename, opts)
        vim.keymap.set('n', '<leader>ca', vim.lsp.buf.code_action, opts)
        vim.keymap.set('n', '<leader>f',  vim.lsp.buf.format, opts)
    end,
})
```

**方式二：使用传统 lspconfig API（Neovim 0.10 及以下）**

```lua
local lspconfig = require('lspconfig')

lspconfig.rust_analyzer.setup({
    settings = {
        ['rust-analyzer'] = {
            cargo = { features = 'all' },
            checkOnSave = { command = 'clippy' },
        },
    },
})
```

**查看 LSP 状态**：使用 `:LspInfo` 命令（需安装 lspconfig）或 `:checkhealth lsp` 查看当前 LSP 客户端的运行状态、附加的 buffer、服务器 PID 等信息。

#### 总结

- Neovim 0.5+ 内置 LSP 客户端，0.11+ 引入了更简洁的 `vim.lsp.config` / `vim.lsp.enable` API。
- `nvim-lspconfig` 提供各语言服务器的默认配置，推荐配合使用。
- 根目录检测通过 `root_markers`（项目标志文件）实现，决定了 LSP 的作用域。
- 常见注意事项：配置后用 `:LspInfo` 确认服务器是否成功附加到 buffer；如果未附加，通常是 `filetypes` 或 `root_markers` 配置有误。

---

### 第 6 讲 VSCode LSP 集成机制

#### 概念

VSCode 是 LSP 协议的发源地，其 LSP 集成是最成熟、最透明的。VSCode 通过安装语言扩展来接入语言服务器，每个扩展内部封装了 LSP 客户端逻辑和服务器二进制文件的下载/管理。VSCode 的 `settings.json` 是配置语言服务器行为的主要入口，配置项以 `[服务器名称]` 为前缀。

#### 原理

VSCode 的 LSP 架构分为三层：**扩展层**（Extension）负责声明语言服务器并处理 UI 交互；**LSP 客户端层**（`vscode-languageclient` Node.js 库）负责 JSON-RPC 通信；**语言服务器进程**是独立运行的子进程。

当用户安装一个语言扩展（如 `rust-analyzer` 扩展）时，扩展的 `package.json` 声明了它支持的文件类型和激活事件。当用户打开匹配的文件时，扩展被激活，启动语言服务器进程，并通过 `vscode-languageclient` 建立 LSP 连接。扩展还负责将 VSCode 的 `settings.json` 配置转换为 LSP 的 `initializationOptions` 和 `workspace/didChangeConfiguration` 通知发送给服务器。

VSCode 的配置体系采用 **树状覆盖** 机制：默认设置 < 用户设置（User Settings）< 工作区设置（Workspace Settings）< 文件夹设置。对于 LSP 配置，可以在 `settings.json` 中为特定语言设置覆盖项，例如：

```json
{
  "rust-analyzer.cargo.features": "all",
  "[python]": {
    "editor.defaultFormatter": "charliermarsh.ruff",
    "editor.formatOnSave": true
  }
}
```

VSCode 还提供了 **LSP 日志查看** 功能：在命令面板中执行 `Developer: Show Logs...` → 选择语言服务器名称，即可查看实时的 LSP 通信日志，这对于调试配置问题非常有用。

#### 例子

**1. settings.json 中的 LSP 配置示例（多语言）**

```json
{
  // Python (Pylance/Pyright)
  "python.languageServer": "Pylance",
  "python.analysis.typeCheckingMode": "strict",
  "python.analysis.inlayHints.variableTypes": true,

  // Rust (rust-analyzer)
  "rust-analyzer.cargo.features": "all",
  "rust-analyzer.checkOnSave.command": "clippy",
  "rust-analyzer.inlayHints.chainingHints.enable": true,

  // TypeScript
  "typescript.tsdk": "node_modules/typescript/lib",
  "typescript.updateImportsOnFileMove.enabled": "always",

  // 通用格式化
  "editor.formatOnSave": true,
  "[rust]": {
    "editor.defaultFormatter": "rust-lang.rust-analyzer"
  },
  "[python]": {
    "editor.defaultFormatter": "charliermarsh.ruff",
    "editor.codeActionsOnSave": {
      "source.fixAll": "explicit",
      "source.organizeImports": "explicit"
    }
  }
}
```

**2. 查看语言服务器输出**

在 VSCode 中，按 `Ctrl+Shift+U`（macOS: `Cmd+Shift+U`）打开 Output 面板，在右侧下拉菜单中选择对应的语言服务器（如 "Rust Analyzer Language Server Trace"），即可看到详细的 LSP 通信日志。

**3. 通过 `extensions.json` 推荐团队统一扩展**

```json
// .vscode/extensions.json
{
  "recommendations": [
    "rust-lang.rust-analyzer",
    "charliermarsh.ruff",
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode"
  ]
}
```

#### 总结

- VSCode 通过语言扩展接入 LSP，扩展负责服务器二进制管理和配置转换。
- `settings.json` 是配置核心，支持按语言覆盖（`[language]` 语法）。
- Output 面板可查看 LSP 通信日志，是调试的第一工具。
- 常见注意事项：VSCode 的某些语言服务器（如 Pylance）是闭源的，但完全兼容 LSP 协议；团队协作时，建议将 `.vscode/settings.json` 和 `extensions.json` 提交到版本控制。

---

### 第 7 讲 Emacs lsp-mode 与 eglot

#### 概念

Emacs 有两个主流的 LSP 客户端：**lsp-mode** 和 **eglot**。lsp-mode 是功能丰富的老牌方案，提供大量配置选项和 UI 功能（如 treemacs 集成、lsp-ui 侧边栏等）；eglot 是 Emacs 29+ 内置的轻量方案，设计哲学是"零配置开箱即用"，只提供 LSP 协议要求的最小功能集。从 Emacs 29 开始，eglot 成为官方推荐的 LSP 客户端。

#### 原理

**eglot** 的设计理念是极简主义。它不依赖外部插件，自动根据当前文件类型查找已安装的语言服务器可执行文件（通过预定义的服务器关联表 `eglot-server-programs`）。eglot 只暴露 LSP 协议本身定义的功能，不添加额外的 UI 装饰。这种设计使得 eglot 的配置极其简洁，但也意味着某些高级功能（如 inlay hints 的自定义渲染）需要额外配置。

**lsp-mode** 则走的是"大而全"路线。它通过 `lsp-install-server` 命令自动下载和安装语言服务器，通过 `lsp-ui` 包提供丰富的 UI 增强（如 sideline 信息、文档浮动窗、代码动作菜单），通过 `lsp-treemacs` 提供符号树视图。lsp-mode 的配置项远多于 eglot，适合需要深度定制的用户。

两者的核心差异在于 **配置哲学**：eglot 认为好的默认值比配置选项更重要，lsp-mode 认为用户应该能控制一切。选择哪个取决于你的需求——如果你只想要可靠的 LSP 支持，选 eglot；如果你需要丰富的 UI 和深度定制，选 lsp-mode。

#### 例子

**1. eglot 配置（Emacs 29+ 内置，推荐）**

```elisp
;; ~/.config/emacs/init.el

;; 启用 eglot（Emacs 29+ 已内置）
(add-hook 'prog-mode-hook #'eglot-ensure)

;; 为特定语言关联语言服务器
(with-eval-after-load 'eglot
  (add-to-list 'eglot-server-programs
               '((python-mode python-ts-mode) . ("pyright-langserver" "--stdio")))
  (add-to-list 'eglot-server-programs
               '((rust-mode rust-ts-mode) . ("rust-analyzer")))
  (add-to-list 'eglot-server-programs
               '((go-mode go-ts-mode) . ("gopls"))))

;; 传递初始化选项
(setq eglot-workspace-configuration
      '((:rust-analyzer . (:cargo (:features "all")
                         :checkOnSave (:command "clippy")))))

;; 快捷键
(with-eval-after-load 'eglot
  (define-key eglot-mode-map (kbd "C-c l d") #'eglot-find-declaration)
  (define-key eglot-mode-map (kbd "C-c l r") #'eglot-rename)
  (define-key eglot-mode-map (kbd "C-c l a") #'eglot-code-actions)
  (define-key eglot-mode-map (kbd "C-c l f") #'eglot-format-buffer))
```

**2. lsp-mode 配置**

```elisp
;; ~/.config/emacs/init.el

;; 安装并启用 lsp-mode
(use-package lsp-mode
  :ensure t
  :hook ((python-mode        . lsp)
         (rust-mode          . lsp)
         (go-mode            . lsp)
         (typescript-mode    . lsp))
  :commands (lsp lsp-deferred)
  :config
  ;; rust-analyzer 配置
  (setq lsp-rust-analyzer-cargo-features "all"
        lsp-rust-analyzer-cargo-all-targets t
        lsp-rust-analyzer-check-on-save-command "clippy")
  ;; 性能优化
  (setq lsp-log-io nil            ; 关闭日志以提升性能
        lsp-idle-delay 0.500      ; 防抖延迟
        lsp-completion-provider :capf))

;; UI 增强
(use-package lsp-ui
  :ensure t
  :commands lsp-ui-mode
  :config
  (setq lsp-ui-doc-enable t
        lsp-ui-doc-position 'at-point
        lsp-ui-sideline-enable t
        lsp-ui-sideline-show-diagnostics t))

;; 快捷键
(with-eval-after-load 'lsp-mode
  (define-key lsp-mode-map (kbd "C-c l d") #'lsp-find-definition)
  (define-key lsp-mode-map (kbd "C-c l r") #'lsp-rename)
  (define-key lsp-mode-map (kbd "C-c l a") #'lsp-execute-code-action)
  (define-key lsp-mode-map (kbd "C-c l f") #'lsp-format-buffer))
```

#### 总结

- eglot（Emacs 29+ 内置）是轻量方案，零配置理念，适合追求简洁的用户。
- lsp-mode 是全功能方案，配合 lsp-ui 提供丰富 UI，适合需要深度定制的用户。
- 两者通过 `eglot-server-programs` 或 `lsp-<server>-*` 变量配置语言服务器。
- 常见注意事项：eglot 和 lsp-mode 不要同时启用，会冲突；eglot 的 `eglot-workspace-configuration` 对应 LSP 的 `workspace/didChangeConfiguration` 配置。

---

### 第 8 讲 Helix 等现代编辑器的 LSP 支持

#### 概念

除了传统的 Neovim、VSCode 和 Emacs，近年来涌现了一批现代编辑器，它们将 LSP 支持作为一等公民内置到编辑器核心中。**Helix** 是用 Rust 编写的模态编辑器，内置 LSP 客户端，无需任何插件即可使用 LSP 功能；**Zed** 是 Atom 团队开发的极速编辑器，同样内置 LSP；**Sublime Text** 通过 `LSP` 插件包提供支持。这些编辑器的共同特点是：LSP 配置更加声明式，开箱即用程度更高。

#### 原理

**Helix** 的 LSP 集成完全内置在编辑器核心中。Helix 在 `languages.toml` 配置文件中声明每种语言的语言服务器命令、文件类型关联和初始化选项。当用户打开文件时，Helix 自动根据文件扩展名匹配语言配置，启动对应的语言服务器。Helix 不需要像 Neovim 那样手动调用 `vim.lsp.start()`，一切都在配置文件中声明式完成。

Helix 的 LSP 配置与语言配置（如缩进、注释字符串等）合并在同一个 `languages.toml` 文件中，这体现了"语言是一等公民"的设计理念。配置采用 TOML 格式，结构清晰。

**Zed** 采用类似的声明式配置，通过 `settings.json` 中的 `lsp` 字段配置各语言服务器。Zed 的优势在于其用 Rust 实现的高性能渲染引擎，能流畅处理大量 LSP 诊断和 inlay hints。

**Sublime Text + LSP 插件** 的配置方式介于 Helix 和 VSCode 之间：通过 `LSP.sublime-settings` 文件配置服务器，语法类似 JSON。

#### 例子

**1. Helix 配置（~/.config/helix/languages.toml）**

```toml
# Python - 使用 pyright + ruff
[[language]]
name = "python"
language-servers = ["pyright", "ruff"]

[language-server.pyright]
command = "pyright-langserver"
args = ["--stdio"]

[language-server.ruff]
command = "ruff"
args = ["server"]

# Rust - rust-analyzer
[[language]]
name = "rust"

[language-server.rust-analyzer]
command = "rust-analyzer"
config = { cargo = { features = "all" }, checkOnSave = { command = "clippy" } }

# Go - gopls
[language-server.gopls]
command = "gopls"
config = { gofumpt = true, staticcheck = true }

# TypeScript - ts_ls (原 tsserver)
[language-server.ts_ls]
command = "typescript-language-server"
args = ["--stdio"]
```

**2. Zed 配置（~/.config/zed/settings.json）**

```json
{
  "lsp": {
    "rust-analyzer": {
      "initialization_options": {
        "cargo": { "features": "all" },
        "checkOnSave": { "command": "clippy" }
      }
    },
    "gopls": {
      "settings": {
        "gofumpt": true,
        "staticcheck": true
      }
    }
  },
  "features": {
    "inline_completion": true
  }
}
```

**3. Sublime Text + LSP 插件（LSP.sublime-settings）**

```json
{
  "clients": {
    "rust-analyzer": {
      "command": ["rust-analyzer"],
      "selector": "source.rust",
      "initializationOptions": {
        "cargo": { "features": "all" },
        "checkOnSave": { "command": "clippy" }
      }
    },
    "gopls": {
      "command": ["gopls"],
      "selector": "source.go"
    }
  }
}
```

#### 总结

- Helix、Zed 等现代编辑器将 LSP 内置为核心功能，配置更加声明式。
- Helix 使用 `languages.toml` 统一管理语言和 LSP 配置，Zed 使用 `settings.json` 的 `lsp` 字段。
- 这些编辑器的共同优势是开箱即用，无需安装额外插件即可获得 LSP 支持。
- 常见注意事项：Helix 的 `language-servers` 列表支持为同一语言配置多个服务器（如 Python 同时用 pyright 做类型检查 + ruff 做 lint/格式化），这是较新的 LSP 多服务器特性。

---

## 第 3 章 格式化基础理论

本章从理论层面建立对代码格式化的系统认知。格式化与 LSP 是相关但独立的概念——LSP 协议中包含格式化能力（`textDocument/formatting`），但实际项目中格式化工具往往独立于 LSP 运行。理解格式化的分类、与 LSP 的关系以及编辑器的 formatOnSave 机制，是后续各语言格式化配置的理论基础。

### 第 9 讲 代码格式化的意义与分类

#### 概念

代码格式化（Code Formatting）是指按照预定义的规则自动调整代码的排版样式，包括缩进、空格、换行、对齐等，使代码风格统一。格式化工具（Formatter）根据规则集对源代码进行解析和重新输出，不改变代码的语义行为。按照自动化程度，格式化可分为三类：**全量格式化器**（Opinionated Formatter，如 Prettier、Black，只提供少量配置项，强制统一风格）、**可配置格式化器**（Configurable Formatter，如 clang-format，提供大量细粒度配置项）、**Linter 修复**（Linter Auto-fix，如 ESLint、Ruff，在检查代码问题的同时自动修复风格类问题）。

#### 原理

**全量格式化器**的设计哲学是"减少争论"。这类工具（如 Python 的 Black、JavaScript 的 Prettier、Go 的 gofmt）刻意只暴露极少的配置选项（通常只有行宽和引号风格），强制团队采用统一风格。其理论基础是：代码风格的争论是无效的，重要的是有一致性。这类工具通常采用完整的 AST（抽象语法树）解析，然后按照固定规则重新生成代码，确保输出确定性——同一份输入永远产生同一份输出。

**可配置格式化器**则认为不同项目有不同的风格需求。这类工具（如 C/C++ 的 clang-format、Java 的 Spotless）提供数十甚至上百个配置项，允许精确控制每个排版细节。其优势是灵活性高，劣势是配置复杂、团队需要维护配置文件。这类工具同样基于 AST 解析，但在生成阶段参考用户配置的规则。

**Linter 修复**是一种特殊形式。Linter（如 ESLint、Ruff、golangci-lint）的主要功能是检查代码问题（包括风格问题和潜在 bug），其中部分规则支持自动修复（`--fix`）。与纯格式化器不同，Linter 的修复可能涉及语义层面的调整（如移除未使用的变量、自动导入缺失的模块），因此更强大但也更需谨慎。

三者的关键区别在于：格式化器只改排版不改语义，Linter 修复可能改语义。在实际项目中，通常组合使用——格式化器负责排版统一，Linter 负责代码质量检查。

#### 例子

**同一段 Python 代码经不同工具处理的效果对比**

原始代码（风格混乱）：

```python
import sys,os
def greet( name ):
    x={'name':name,'time':datetime.now()}
    if  x['name']=='world' :
        print( f"Hello, {x['name']}!" )
    return x
```

经 Black 格式化后（统一风格，不可配置细节）：

```python
import sys
import os


def greet(name):
    x = {"name": name, "time": datetime.now()}
    if x["name"] == "world":
        print(f"Hello, {x['name']}!")
    return x
```

经 Ruff format + fix 后（格式化 + 自动修复 import 排序 + 移除未使用导入）：

```python
from datetime import datetime


def greet(name):
    x = {"name": name, "time": datetime.now()}
    if x["name"] == "world":
        print(f"Hello, {x['name']}!")
    return x
```

注意 Ruff 移除了未使用的 `sys` 和 `os` 导入，这是 Linter 修复的语义层面操作，纯格式化器不会做。

#### 总结

- 格式化分为三类：全量格式化器（opinionated，如 Black/Prettier/gofmt）、可配置格式化器（如 clang-format）、Linter 修复（如 ESLint/Ruff）。
- 全量格式化器通过"减少配置项"消除风格争论，是现代项目的首选。
- 格式化器只改排版不改语义，Linter 修复可能改语义，实际项目中通常组合使用。
- 常见注意事项：不要同时配置多个格式化器处理同一语言，会导致冲突；选择一个主格式化器，Linter 仅用于非格式化的代码质量检查。

---

### 第 10 讲 格式化与 LSP 的关系

#### 概念

LSP 协议规范中定义了三个与格式化相关的方法：`textDocument/formatting`（格式化整个文档）、`textDocument/rangeFormatting`（格式化选区）、`textDocument/onTypeFormatting`（输入时格式化）。语言服务器可以选择实现这些方法，将格式化能力暴露给编辑器。然而，在实际项目中，格式化工具经常独立于 LSP 运行——编辑器可以直接调用外部格式化器命令（如 `prettier --write`、`black`），而不经过 LSP 通道。

#### 原理

**LSP 内置格式化**的工作流程是：编辑器发送 `textDocument/formatting` 请求（携带格式化选项，如 `tabSize`、`insertSpaces`），语言服务器在内部调用格式化逻辑，返回一系列 `TextEdit` 操作（文本编辑差异），编辑器应用这些编辑完成格式化。这种方式的优点是配置统一（格式化选项通过 LSP 配置传递），缺点是格式化能力受限于语言服务器的实现——并非所有语言服务器都实现了 formatting 方法。

**外部格式化器**的工作流程是：编辑器直接运行格式化器命令（如 `sh -c "ruff format -"`），将文件内容通过 stdin 传入，格式化器返回格式化后的内容，编辑器替换文件内容。这种方式的优点是灵活——可以使用任何格式化工具，不依赖语言服务器是否支持格式化；缺点是需要编辑器额外配置格式化器命令和参数。

**现代趋势**是两者融合。许多格式化工具（如 Ruff、Prettier、rustfmt）同时提供 CLI 和 LSP 服务器两种接口。当作为 LSP 服务器运行时，它们实现 `textDocument/formatting` 方法；当作为 CLI 运行时，它们直接处理文件。一些工具（如 Ruff）甚至可以作为辅助语言服务器与主语言服务器（如 Pyright）并行运行，专门负责格式化和 lint。

**编辑器格式化调度**是关键。当用户触发格式化时（如保存文件），编辑器需要决定调用哪个格式化器。VSCode 通过 `editor.defaultFormatter` 设置指定；Neovim 通过 `vim.lsp.buf.format()` 调用 LSP 格式化或通过 `conform.nvim` / `null-ls` 等插件调用外部格式化器；Helix 在 `languages.toml` 中通过 `formatter` 字段指定。理解编辑器的调度机制，才能正确配置格式化行为。

#### 例子

**1. LSP 格式化请求/响应**

```json
// 请求
{
  "method": "textDocument/formatting",
  "params": {
    "textDocument": {"uri": "file:///src/main.py"},
    "options": {
      "tabSize": 4,
      "insertSpaces": true
    }
  }
}

// 响应（返回 TextEdit 列表）
{
  "result": [
    {
      "range": {
        "start": {"line": 0, "character": 0},
        "end": {"line": 0, "character": 10}
      },
      "newText": "import os\n"
    }
  ]
}
```

**2. Neovim 中同时使用 LSP 格式化和外部格式化器（conform.nvim）**

```lua
-- 使用 conform.nvim 管理格式化器
require('conform').setup({
    formatters_by_ft = {
        python = { 'ruff_format', 'ruff_organize_imports' },
        javascript = { 'prettierd', 'prettier', stop_after_first = true },
        rust = { 'rustfmt' },
        go = { 'gofmt', 'goimports' },
    },
    format_on_save = {
        timeout_ms = 500,
        lsp_fallback = true,  -- 如果没有配置外部格式化器，回退到 LSP 格式化
    },
})

-- 手动格式化快捷键
vim.keymap.set({ 'n', 'v' }, '<leader>f', function()
    require('conform').format({ async = true, lsp_fallback = true })
end)
```

**3. Helix 中配置外部格式化器（独立于 LSP）**

```toml
# ~/.config/helix/languages.toml

[[language]]
name = "python"
# LSP 服务器用于补全/诊断
language-servers = ["pyright", "ruff"]
# 格式化器独立配置
formatter = { command = "ruff", args = ["format", "-"] }

[[language]]
name = "javascript"
formatter = { command = "prettier", args = ["--parser", "babel"] }
```

#### 总结

- LSP 协议定义了 `textDocument/formatting` 等方法，但格式化器也可独立于 LSP 运行。
- LSP 格式化配置统一但受限于服务器实现；外部格式化器灵活但需额外配置。
- 现代趋势是融合：许多工具（如 Ruff）同时提供 CLI 和 LSP 接口。
- 常见注意事项：编辑器格式化调度需明确指定默认格式化器，避免多个格式化器冲突；Neovim 中推荐使用 `conform.nvim` 统一管理格式化器。

---

### 第 11 讲 formatOnSave 与编辑器钩子机制

#### 概念

`formatOnSave`（保存时格式化）是现代编辑器中最常用的格式化触发方式。当用户保存文件时，编辑器自动调用格式化器，确保写入磁盘的代码始终是格式化后的。这背后依赖编辑器的 **钩子机制**（Hook Mechanism）——编辑器在特定事件（如保存前、保存后、文件类型变更等）触发时执行用户注册的回调函数。理解不同编辑器的钩子机制，是配置可靠的 formatOnSave 的关键。

#### 原理

**保存时格式化的时序**至关重要。理想流程是：用户按下保存 → 编辑器触发 `pre-save` 钩子 → 执行格式化器 → 格式化器修改 buffer 内容 → 编辑器将格式化后的内容写入磁盘 → 触发 `post-save` 钩子。如果格式化器在文件已写入磁盘后才运行，就会出现"保存后文件闪一下又变回去"的问题，因为编辑器内存中的 buffer 与磁盘文件不一致。

**超时机制**是 formatOnSave 的另一个关键。格式化器可能因为各种原因（如冷启动慢、项目大）响应缓慢。如果编辑器无限等待格式化器完成，用户会感到保存卡顿。因此，编辑器通常设置超时（如 Neovim 默认 500ms，VSCode 默认无超时但可配置），超时后放弃格式化直接保存。这意味着格式化器必须在超时内完成，否则 formatOnSave 会静默失败。

**LSP 格式化的异步特性**带来额外复杂性。LSP 格式化是异步请求-响应模式，编辑器发送 `textDocument/formatting` 请求后需要等待响应。如果语言服务器正在处理其他耗时任务（如全项目索引），格式化响应可能延迟。这就是为什么许多用户偏好使用外部 CLI 格式化器——它们是独立进程，不受语言服务器负载影响。

**Code Actions on Save** 是 formatOnSave 的扩展概念。除了纯格式化，保存时还可以触发 LSP 的 code action（如 `source.fixAll` 自动修复、`source.organizeImports` 组织导入）。VSCode 通过 `editor.codeActionsOnSave` 配置，Neovim 通过 `vim.lsp.buf.code_action()` 的 filter 参数实现。

#### 例子

**1. VSCode formatOnSave 配置**

```json
{
  // 全局开启保存时格式化
  "editor.formatOnSave": true,

  // 按语言指定默认格式化器
  "[python]": {
    "editor.defaultFormatter": "charliermarsh.ruff",
    "editor.formatOnSave": true
  },
  "[rust]": {
    "editor.defaultFormatter": "rust-lang.rust-analyzer",
    "editor.formatOnSave": true
  },

  // 保存时执行 code actions（自动修复 + 组织导入）
  "[typescript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "editor.codeActionsOnSave": {
      "source.fixAll.eslint": "explicit",
      "source.organizeImports": "explicit"
    }
  }
}
```

**2. Neovim formatOnSave（使用 conform.nvim）**

```lua
require('conform').setup({
    format_on_save = function(bufnr)
        -- 禁用某些大文件的格式化
        if vim.api.nvim_buf_line_count(bufnr) > 5000 then
            return nil
        end
        return {
            timeout_ms = 1000,  -- 超时 1 秒
            lsp_fallback = true,
        }
    end,
})

-- 或者使用原生 LSP 格式化的 formatOnSave
vim.api.nvim_create_autocmd('BufWritePre', {
    pattern = '*',
    callback = function()
        vim.lsp.buf.format({ async = false, timeout_ms = 1000 })
    end,
})
```

**3. Helix formatOnSave**

Helix 默认在保存时自动格式化（如果配置了格式化器），无需额外配置：

```toml
# languages.toml 中配置了 formatter 后，保存时自动格式化
[[language]]
name = "python"
auto-format = true  # 显式开启（默认即为 true）
formatter = { command = "ruff", args = ["format", "-"] }
```

#### 总结

- formatOnSave 依赖编辑器的 pre-save 钩子，时序正确性至关重要。
- 超时机制防止格式化器卡住保存操作，但可能导致格式化静默失败。
- LSP 格式化是异步的，受语言服务器负载影响；外部 CLI 格式化器更可靠。
- 常见注意事项：如果 formatOnSave "不生效"，首先检查格式化器是否在超时内完成（查看日志），其次检查 `defaultFormatter` 是否正确指定，最后确认格式化器命令是否在 PATH 中可用。


---

## 第 4 章 Python LSP 与格式化

Python 是 LSP 生态最丰富的语言之一，拥有多个成熟的语言服务器和格式化工具。本章将从 LSP 服务器选型（Pyright vs Pylsp）讲起，深入讲解现代工具 Ruff 的配置，再到经典的 Black + isort 组合，最后通过一个完整的 Python 项目实战整合所有配置。Python 的工具链经历了从"碎片化"到"统一化"的演进，理解这个演进过程有助于做出合理的工具选型。

### 第 12 讲 Pyright 与 Pylsp：Python LSP 服务器对比

#### 概念

Python 生态有两个主流的 LSP 服务器：**Pyright**（微软开发，VSCode 的 Pylance 底层也基于 Pyright）和 **Pylsp**（Python Language Server，社区维护，前身为微软的 palantir/python-language-server）。Pyright 专注于类型检查和静态分析，用 TypeScript 编写，性能优异；Pylsp 用 Python 编写，采用插件架构，通过 pylsp-mypy、pyls-isort 等插件扩展功能，灵活性高但性能略逊。

#### 原理

**Pyright** 的核心是一个高性能的 Python 类型检查器。它不依赖运行 Python 环境，而是通过静态解析源代码和类型注解进行分析。Pyright 的类型检查严格度分为四档：`off`、`basic`、`standard`、`strict`，通过 `python.analysis.typeCheckingMode` 配置。Pyright 作为 LSP 服务器运行时，命令是 `pyright-langserver --stdio`，它实现了补全、跳转、悬停、诊断等核心 LSP 方法，但不实现格式化（格式化交给外部工具）。

**Pylsp** 的架构是插件化的。核心进程负责 LSP 通信和文档管理，具体功能由插件提供：`pycodestyle` 插件提供 PEP 8 风格检查，`pyflakes` 插件提供错误检查，`rope` 插件提供重构和自动导入，`mccabe` 插件提供复杂度检查，`autopep8` 或 `black` 插件提供格式化。这种架构的优点是可扩展性强——你可以只启用需要的插件；缺点是配置项分散，且 Python 实现的性能不如 TypeScript 实现的 Pyright。

**选型建议**：如果你主要需要类型检查和智能补全（大多数现代 Python 项目的需求），选 Pyright——它更快、类型检查更准确。如果你需要高度可定制的 lint 规则集（如遗留项目需要兼容特定的 pycodestyle 规则），选 Pylsp。当然，在现代 Python 工作流中，更推荐的做法是：Pyright 做类型检查 + Ruff 做 lint 和格式化（见第 13 讲），Pylsp 的角色逐渐被这个组合取代。

#### 例子

**1. Pyright 配置（Neovim）**

```lua
-- ~/.config/nvim/lsp/pyright.lua
return {
    cmd = { 'pyright-langserver', '--stdio' },
    filetypes = { 'python' },
    root_markers = { 'pyproject.toml', 'setup.py', 'setup.cfg', 'requirements.txt', '.git' },
    settings = {
        ['python'] = {
            analysis = {
                typeCheckingMode = 'strict',       -- off | basic | standard | strict
                autoSearchPaths = true,
                useLibraryCodeForTypes = true,
                diagnosticMode = 'workspace',      -- 'openFilesOnly' | 'workspace'
                inlayHints = {
                    variableTypes = true,
                    functionReturnTypes = true,
                    parameterNames = true,
                },
            },
        },
    },
}
```

**2. Pyright 配置文件（pyrightconfig.json）**

```json
{
  "typeCheckingMode": "strict",
  "include": ["src"],
  "exclude": ["**/node_modules", "**/__pycache__", "tests/fixtures"],
  "reportMissingTypeStubs": false,
  "reportUnknownMemberType": "warning",
  "extraPaths": ["./src", "./libs"],
  "venvPath": ".",
  "venv": ".venv"
}
```

**3. Pylsp 配置（Neovim）**

```lua
-- ~/.config/nvim/lsp/pylsp.lua
return {
    cmd = { 'pylsp' },
    filetypes = { 'python' },
    root_markers = { 'pyproject.toml', 'setup.cfg', '.git' },
    settings = {
        ['pylsp'] = {
            plugins = {
                pycodestyle = { enabled = true, maxLineLength = 100 },
                pyflakes = { enabled = true },
                mccabe = { enabled = true, threshold = 15 },
                -- 禁用不需要的插件
                autopep8 = { enabled = false },
                yapf = { enabled = false },
                -- 启用 rope 用于重构
                rope = { enabled = true },
                -- 启用 mypy 类型检查插件
                pylsp_mypy = { enabled = true, live_mode = false },
            },
        },
    },
}
```

**4. VSCode 中切换语言服务器**

```json
{
  "python.languageServer": "Pylance",  // 或 "Pyright" 或 "Jedi"
  "python.analysis.typeCheckingMode": "strict"
}
```

#### 总结

- Pyright（TypeScript 实现）专注类型检查，性能优异，是现代 Python 项目的首选。
- Pylsp（Python 实现）插件化架构，灵活性强，适合需要定制 lint 规则的场景。
- Pyright 不做格式化，格式化需配合外部工具（Ruff/Black）。
- 常见注意事项：Pyright 的 `venv` 和 `venvPath` 配置很关键，如果类型提示不工作，通常是虚拟环境路径配置有误；Pylsp 的插件需要单独 pip 安装（如 `pip install pylsp-mypy python-lsp-ruff`）。

---

### 第 13 讲 Ruff：现代 Python Linter + Formatter

#### 概念

Ruff 是用 Rust 编写的极速 Python linter 和 formatter，由 Astral 公司开发。它以比传统工具（Flake8、isort、Black）快 10-100 倍的性能著称，同时集成了数十个 Python lint 工具的规则集。Ruff 既可以作为 CLI 工具使用，也可以作为 LSP 服务器运行（`ruff server`），是现代 Python 项目的推荐选择。Ruff 的设计理念是"一个工具替代 Flake8 + isort + Black + 一堆 Flake8 插件"。

#### 原理

**Ruff 的性能优势**来自 Rust 实现和并行解析。传统 Python linter（如 Flake8）逐文件解析，每个插件独立运行，速度受限于 Python 解释器。Ruff 用 Rust 编写解析器，在内存中并行处理多个文件，且所有规则在单次遍历中检查，避免了重复解析。对于大型项目，Ruff 可以在毫秒级完成全项目检查，而 Flake8 可能需要数秒。

**Ruff 的规则体系**采用命名空间设计。每条规则属于一个规则集（rule set），用 `前缀.规则名` 标识，例如 `E501`（行太长，来自 pycodestyle）、`F401`（未使用的导入，来自 Pyflakes）、`I001`（导入排序，来自 isort）。Ruff 通过 `select` 和 `ignore` 配置启用和禁用规则集。Ruff 内置了 80+ 个规则集，覆盖了 Flake8 及其数十个插件的功能。

**Ruff 作为 LSP 服务器**（`ruff server`）是较新的特性（Ruff 0.4.0+）。它实现了 LSP 的诊断推送（`textDocument/publishDiagnostics`）、格式化（`textDocument/formatting`）和 code action（`textDocument/codeAction`，用于自动修复）。Ruff server 可以与 Pyright 并行运行——Pyright 负责类型检查，Ruff 负责风格检查和格式化，两者通过不同的诊断 source 区分。

**Ruff 的格式化器**是 Black 的兼容实现，但速度更快。Ruff format 的输出与 Black 高度兼容（差异极小），且支持一些 Black 不支持的特性（如引号风格配置、魔法尾逗号控制）。Ruff 的格式化器和 linter 共享同一套配置（如行宽、缩进），避免了 Black + isort + Flake8 三个工具配置不一致的问题。

#### 例子

**1. pyproject.toml 中的 Ruff 配置**

```toml
[tool.ruff]
line-length = 100
target-version = "py312"
src = ["src", "tests"]
exclude = ["migrations", ".venv"]

[tool.ruff.lint]
# 启用的规则集
select = [
    "E",    # pycodestyle errors
    "W",    # pycodestyle warnings
    "F",    # Pyflakes
    "I",    # isort (import sorting)
    "B",    # flake8-bugbear
    "C4",   # flake8-comprehensions
    "UP",   # pyupgrade
    "N",    # pep8-naming
    "SIM",  # flake8-simplify
    "RUF",  # Ruff-specific rules
]
# 忽略的规则
ignore = [
    "E501",  # 行太长（由 formatter 处理）
    "B008",  # 函数默认值中使用函数调用（如 FastAPI 的 Depends）
]

# 每文件覆盖规则
[tool.ruff.lint.per-file-ignores]
"tests/*" = ["S101"]  # 测试中允许 assert
"__init__.py" = ["F401"]  # __init__.py 中允许未使用的导入

[tool.ruff.lint.isort]
known-first-party = ["myproject"]

[tool.ruff.format]
quote-style = "double"
indent-style = "space"
line-ending = "lf"
docstring-code-format = true
```

**2. Ruff 作为 LSP 服务器（Neovim 配置）**

```lua
-- ~/.config/nvim/lsp/ruff.lua
return {
    cmd = { 'ruff', 'server' },
    filetypes = { 'python' },
    root_markers = { 'pyproject.toml', 'ruff.toml', '.ruff.toml', '.git' },
    -- 初始化选项
    on_init = function(client, _)
        -- 通知 Ruff 服务器使用项目配置
        client.settings = {
            ['ruff'] = {
                logLevel = 'info',
                organizeImports = true,
                fixAll = true,
                lint = { enable = true },
                format = { enable = true },
            },
        }
    end,
}
```

**3. 多服务器配置：Pyright + Ruff 并行（Neovim）**

```lua
-- ~/.config/nvim/lsp/python.lua
-- 同时启用 Pyright（类型检查）和 Ruff（lint + 格式化）
vim.lsp.config('pyright', {
    settings = {
        python = {
            analysis = {
                typeCheckingMode = 'strict',
                -- 禁用 Pyright 的风格检查（交给 Ruff）
                diagnosticSeverityOverrides = {
                    reportUnusedImport = "none",
                    reportUnusedVariable = "none",
                },
            },
        },
    },
})

vim.lsp.config('ruff', {
    -- Ruff 配置...
})

vim.lsp.enable({ 'pyright', 'ruff' })
```

**4. CLI 使用**

```bash
# 检查所有文件
ruff check .

# 自动修复
ruff check --fix .

# 格式化所有文件
ruff format .

# 查看某条规则的解释
ruff rule E501
```

#### 总结

- Ruff 是 Rust 实现的极速 linter + formatter，可替代 Flake8 + isort + Black 组合。
- Ruff 可作为 LSP 服务器运行，与 Pyright 并行，分工明确（Pyright 做类型检查，Ruff 做 lint + 格式化）。
- 配置集中在 `pyproject.toml` 的 `[tool.ruff]` 段，linter 和 formatter 共享行宽等设置。
- 常见注意事项：Ruff 的 `select` 默认只启用 `E` 和 `F`，需要显式添加 `I`、`B` 等规则集；Ruff server 与 Pyright 并行时，需在 Pyright 中禁用风格类诊断（如 `reportUnusedImport`），避免重复报告。

---

### 第 14 讲 Black + isort 经典组合配置

#### 概念

在 Ruff 出现之前，**Black + isort** 是 Python 项目最主流的格式化组合。Black 是"不妥协的格式化器"（The Uncompromising Code Formatter），只暴露极少的配置项，强制统一代码风格；isort 专门负责 import 语句的排序和分组。虽然 Ruff 正在取代这个组合，但大量遗留项目仍在使用 Black + isort，理解其配置仍然重要。

#### 原理

**Black 的设计哲学**是"减少争论"。它刻意只提供极少的配置选项：行宽（`line-length`，默认 88）、目标 Python 版本（`target-version`）、字符串引号风格（`skip-string-normalization`，默认将所有字符串统一为双引号）、魔法尾逗号（`skip-magic-trailing-comma`）。Black 的理念是：如果你花时间讨论代码风格，那是在浪费时间；Black 替你做决定，你只需接受。

Black 的工作原理是：解析 Python 源代码为 CST（Concrete Syntax Tree，具体语法树），然后按照固定规则重新输出代码。由于输出规则是确定的，同一份输入永远产生同一份输出——这就是 Black 官方所说的"确定性格式化"。

**isort** 专注于 import 语句的组织。它将 import 分为几组：标准库（stdlib）、第三方库（third-party）、本地应用（first-party），每组内按字母排序，组间用空行分隔。isort 的配置项比 Black 多得多，包括分组规则、排序方式（字母序 vs 自然序）、大小写处理、force-sort-within-sections 等。

**Black + isort 的冲突问题**是经典痛点。isort 和 Black 对 import 语句的格式化方式可能不同（如多行 import 的换行风格），导致两者互相覆盖。解决方案是使用 `isort` 的 `profile = "black"` 配置，使 isort 的输出与 Black 兼容。

#### 例子

**1. pyproject.toml 中的 Black + isort 配置**

```toml
# Black 配置
[tool.black]
line-length = 100
target-version = ['py311', 'py312']
include = '\.pyi?$'
extend-exclude = '''
/(
    \.git
  | \.venv
  | build
  | dist
  | migrations
)/
'''

# isort 配置
[tool.isort]
profile = "black"           # 关键：与 Black 兼容
line_length = 100
known_first_party = ["myproject"]
known_third_party = ["django", "celery"]
force_sort_within_sections = true
src_paths = ["src", "tests"]
```

**2. setup.cfg 中的配置（旧式）**

```ini
[flake8]
max-line-length = 100
extend-ignore = E203, W503   # 与 Black 兼容：E203 切片冒号空格，W503 or 换行

[isort]
profile = black
line_length = 100
```

**3. pre-commit 配置**

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/pycqa/isort
    rev: 5.13.2
    hooks:
      - id: isort
        args: ["--profile", "black"]

  - repo: https://github.com/psf/black
    rev: 24.10.0
    hooks:
      - id: black
        language_version: python3.12

  - repo: https://github.com/pycqa/flake8
    rev: 7.1.1
    hooks:
      - id: flake8
        args: ["--max-line-length", "100", "--extend-ignore", "E203,W503"]
```

**4. 编辑器配置（Neovim + conform.nvim）**

```lua
require('conform').setup({
    formatters_by_ft = {
        python = { 'isort', 'black' },  -- 先 isort 排序 import，再 black 格式化
    },
    formatters = {
        black = {
            args = { '--line-length', '100', '--target-version', 'py312', '-' },
        },
        isort = {
            args = { '--profile', 'black', '--line-length', '100', '-' },
        },
    },
})
```

**5. 从 Black + isort 迁移到 Ruff**

```bash
# Ruff 可以自动迁移配置
ruff check --select RUF --fix .
ruff format .

# pyproject.toml 中移除 [tool.black] 和 [tool.isort]
# 添加 [tool.ruff] 配置（见第 13 讲）
```

#### 总结

- Black 是"不妥协的格式化器"，配置项极少，强制统一风格。
- isort 专注 import 排序，必须设置 `profile = "black"` 以避免与 Black 冲突。
- Flake8 需配置 `extend-ignore = E203, W503` 以兼容 Black 的格式化风格。
- 常见注意事项：Black + isort + Flake8 三者配置需保持一致（行宽等），否则会互相冲突；新项目推荐直接使用 Ruff 替代这个组合。

---

### 第 15 讲 Python 项目综合配置实战

#### 概念

本讲通过一个完整的 Python 项目，整合前面所学的所有配置：Pyright 做类型检查、Ruff 做 lint 和格式化、pre-commit 做提交前检查、VSCode/Neovim 编辑器配置。我们将构建一个典型的现代 Python 项目结构，展示各工具如何协同工作，以及如何处理常见的配置冲突。

#### 原理

现代 Python 项目的工具链配置遵循 **关注点分离** 原则：类型检查（Pyright）、代码风格（Ruff linter）、代码格式化（Ruff formatter）、依赖管理（pip/poetry/uv）、测试（pytest）各司其职，通过 `pyproject.toml` 统一管理配置。这种分离确保了每个工具专注于自己的领域，避免功能重叠导致的冲突。

**配置层级**从高到低为：项目级配置（`pyproject.toml`，提交到版本控制）> 编辑器配置（`.vscode/settings.json` 或 Neovim 配置）> 用户级配置（编辑器全局设置）。项目级配置应作为唯一事实来源（single source of truth），编辑器配置只负责调用这些工具，不重复定义规则。

**虚拟环境管理**是 Python LSP 配置的关键。Pyright 需要知道虚拟环境路径才能正确解析第三方库的类型提示。推荐使用 `venvPath` + `venv` 配置或 `python.pythonPath`（VSCode）指向项目的虚拟环境。现代工具如 `uv` 和 `poetry` 会自动创建 `.venv` 目录，Pyright 默认会查找 `.venv`。

#### 例子

**1. 项目结构**

```
my-python-project/
├── pyproject.toml          # 统一配置入口
├── .pre-commit-config.yaml # pre-commit 钩子
├── .vscode/
│   ├── settings.json       # VSCode 项目配置
│   └── extensions.json     # 推荐扩展
├── src/
│   └── myproject/
│       ├── __init__.py
│       └── main.py
├── tests/
│   └── test_main.py
└── .venv/                  # 虚拟环境（不提交）
```

**2. pyproject.toml（完整配置）**

```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "myproject"
version = "0.1.0"
requires-python = ">=3.12"
dependencies = [
    "fastapi>=0.115",
    "pydantic>=2.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=8.0",
    "pytest-cov>=5.0",
    "ruff>=0.8",
    "pyright>=1.1",
    "pre-commit>=4.0",
]

# ─── Ruff 配置 ───
[tool.ruff]
line-length = 100
target-version = "py312"
src = ["src", "tests"]

[tool.ruff.lint]
select = ["E", "W", "F", "I", "B", "C4", "UP", "N", "SIM", "RUF", "S", "PT"]
ignore = ["E501", "B008", "S101"]  # S101: 测试中允许 assert

[tool.ruff.lint.per-file-ignores]
"tests/*" = ["S", "PT"]  # 测试文件放宽安全规则

[tool.ruff.lint.isort]
known-first-party = ["myproject"]

[tool.ruff.format]
quote-style = "double"
indent-style = "space"

# ─── Pyright 配置 ───
[tool.pyright]
include = ["src"]
exclude = ["**/__pycache__", ".venv"]
venvPath = "."
venv = ".venv"
typeCheckingMode = "strict"
reportMissingTypeStubs = "none"
# 禁用与 Ruff 重复的规则
reportUnusedImport = "none"
reportUnusedVariable = "none"

# ─── pytest 配置 ───
[tool.pytest.ini_options]
testpaths = ["tests"]
addopts = "-v --cov=myproject --cov-report=term-missing"
```

**3. .pre-commit-config.yaml**

```yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.8.0
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format

  - repo: https://github.com/microsoft/pyright
    rev: v1.1.390
    hooks:
      - id: pyright
        additional_dependencies: ["fastapi", "pydantic"]

  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v5.0.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-added-large-files
```

**4. .vscode/settings.json**

```json
{
  "python.defaultInterpreterPath": "./.venv/bin/python",
  "python.languageServer": "Pylance",
  "python.analysis.typeCheckingMode": "strict",
  "python.analysis.venvPath": ".",
  "python.analysis.extraPaths": ["./src"],

  "[python]": {
    "editor.defaultFormatter": "charliermarsh.ruff",
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
      "source.fixAll.ruff": "explicit",
      "source.organizeImports.ruff": "explicit"
    }
  },

  "ruff.lineLength": 100,
  "ruff.lint.args": ["--config=pyproject.toml"],
  "ruff.format.args": ["--config=pyproject.toml"]
}
```

**5. Neovim 配置（等价）**

```lua
-- ~/.config/nvim/lsp/pyright.lua
return {
    cmd = { 'pyright-langserver', '--stdio' },
    filetypes = { 'python' },
    root_markers = { 'pyproject.toml', '.git' },
    settings = {
        python = {
            analysis = {
                typeCheckingMode = 'strict',
                extraPaths = { 'src' },
                diagnosticSeverityOverrides = {
                    reportUnusedImport = 'none',
                    reportUnusedVariable = 'none',
                },
            },
            pythonPath = '.venv/bin/python',
            venvPath = '.',
        },
    },
}

-- ~/.config/nvim/lsp/ruff.lua
return {
    cmd = { 'ruff', 'server' },
    filetypes = { 'python' },
    root_markers = { 'pyproject.toml', '.git' },
}

-- 启用两个服务器
vim.lsp.enable({ 'pyright', 'ruff' })

-- 格式化使用 Ruff
require('conform').setup({
    formatters_by_ft = {
        python = { 'ruff_organize_imports', 'ruff_format' },
    },
    format_on_save = { timeout_ms = 1000 },
})
```

#### 总结

- 现代 Python 项目通过 `pyproject.toml` 统一管理所有工具配置，作为唯一事实来源。
- Pyright 负责类型检查，Ruff 负责 lint + 格式化，两者并行运行，需禁用重复规则。
- 虚拟环境路径配置是类型提示正常工作的关键，推荐使用 `.venv` 目录约定。
- 常见注意事项：`pyproject.toml` 中的配置变更需要重启语言服务器才能生效；pre-commit 钩子与编辑器配置应保持一致，避免"编辑器格式化通过但 pre-commit 失败"的问题。

---

## 第 5 章 JavaScript/TypeScript

JavaScript/TypeScript 生态的 LSP 和格式化配置是所有语言中最复杂的——工具链碎片化严重，ESLint 和 Prettier 的关系微妙，monorepo 场景下的配置管理更是一大挑战。本章用 5 讲的篇幅，从 TypeScript Language Server 讲起，深入 ESLint 和 Prettier 的配置体系，解决两者协同的经典难题，最后覆盖 monorepo 场景下的配置管理策略。

### 第 16 讲 TypeScript Language Server 配置

#### 概念

TypeScript Language Server（`typescript-language-server`）是社区维护的 LSP 服务器，封装了 TypeScript 编译器（`tsc`）的语言服务能力。注意它不同于 TypeScript 自带的 `tsserver`——`tsserver` 不是 LSP 服务器，它使用自己的协议；`typescript-language-server` 在 `tsserver` 之上封装了一层 LSP 适配。VSCode 内置的 TypeScript 支持直接使用 `tsserver`，而 Neovim、Helix 等编辑器通过 `typescript-language-server` 接入。

#### 原理

**typescript-language-server 的架构**是双层封装：LSP 客户端 ↔ typescript-language-server（LSP 适配层）↔ tsserver（TypeScript 编译器语言服务）。`typescript-language-server` 接收 LSP 消息，转换为 `tsserver` 的协议消息，再将 `tsserver` 的响应转换回 LSP 格式。这种设计使得任何 LSP 客户端都能利用 TypeScript 编译器的完整能力。

**tsconfig.json 的作用**至关重要。TypeScript Language Server 的行为很大程度上由项目根目录下的 `tsconfig.json` 决定。`tsconfig.json` 定义了编译选项（`strict`、`target`、`module`）、包含/排除的文件（`include`、`exclude`）、路径映射（`paths`）等。语言服务器读取 `tsconfig.json` 来确定类型检查的范围和严格度。如果 `tsconfig.json` 配置有误，LSP 的诊断信息会不准确。

**工作区版本管理**是 TS LSP 的一个重要特性。TypeScript 版本更新频繁，不同项目可能依赖不同版本的 TypeScript。`typescript-language-server` 支持通过 `initializationOptions.tsserver.path` 指定项目本地的 TypeScript 路径（通常是 `node_modules/typescript/lib`），确保 LSP 使用与项目一致的 TypeScript 版本。VSCode 通过 `typescript.tsdk` 设置实现同样的功能。

**JS/TS 混合项目**的支持也是 TS LSP 的核心能力。TypeScript Language Server 同时处理 `.js`、`.jsx`、`.ts`、`.tsx` 文件。对于 JavaScript 文件，可以通过 `allowJs` 和 `checkJs` 选项控制是否进行类型检查。`// @ts-check` 注释可以为单个 JS 文件启用类型检查。

#### 例子

**1. Neovim 配置**

```lua
-- ~/.config/nvim/lsp/ts_ls.lua  (注意：旧名 vtsls 或 tsserver，新名 ts_ls)
return {
    cmd = { 'typescript-language-server', '--stdio' },
    filetypes = { 'javascript', 'javascriptreact', 'typescript', 'typescriptreact' },
    root_markers = { 'tsconfig.json', 'jsconfig.json', 'package.json', '.git' },
    init_options = {
        -- 使用项目本地的 TypeScript 版本
        typescript = {
            tsdk = 'node_modules/typescript/lib',
        },
        javascript = {
            tsdk = 'node_modules/typescript/lib',
        },
        hostInfo = 'neovim',
    },
    settings = {
        typescript = {
            inlayHints = {
                includeInlayParameterNameHints = 'all',
                includeInlayParameterNameHintsWhenArgumentMatchesName = false,
                includeInlayFunctionLikeReturnTypeHints = true,
                includeInlayVariableTypeHints = true,
                includeInlayPropertyDeclarationTypeHints = true,
            },
            updateImportsOnFileMove = 'always',
            suggest = { completeFunctionCalls = true },
        },
        javascript = {
            -- 同 typescript 配置
        },
    },
}
```

**2. VSCode 配置**

```json
{
  "typescript.tsdk": "node_modules/typescript/lib",
  "typescript.enablePromptUseWorkspaceTsdk": true,
  "typescript.updateImportsOnFileMove.enabled": "always",
  "typescript.inlayHints.parameterNames.enabled": "all",
  "typescript.inlayHints.functionLikeReturnTypes.enabled": true,
  "typescript.inlayHints.variableTypes.enabled": true,
  "typescript.preferences.importModuleSpecifier": "relative",
  "typescript.tsserver.maxTsServerMemory": 8192
}
```

**3. tsconfig.json 示例**

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitOverride": true,
    "jsx": "react-jsx",
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"],
      "@components/*": ["./src/components/*"]
    },
    "types": ["node", "vitest/globals"]
  },
  "include": ["src", "tests"],
  "exclude": ["node_modules", "dist"]
}
```

**4. 查看 tsserver 日志（调试用）**

```bash
# Neovim 中启用 tsserver 日志
:lua vim.lsp.set_log_level("debug")
# 日志文件位置
:lua print(vim.lsp.get_log_path())
```

#### 总结

- `typescript-language-server` 是 `tsserver` 的 LSP 封装层，为非 VSCode 编辑器提供 TS 语言服务。
- `tsconfig.json` 是 TypeScript LSP 行为的核心配置，决定了类型检查的范围和严格度。
- 使用项目本地 TypeScript 版本（`node_modules/typescript/lib`）确保 LSP 与项目一致。
- 常见注意事项：`ts_ls` 是 lspconfig 中的新名称（旧名 `tsserver` 已废弃）；大型项目可能需要增加 `maxTsServerMemory`；路径映射（`paths`）配置错误会导致跳转和补全异常。

---

### 第 17 讲 ESLint 配置体系详解

#### 概念

ESLint 是 JavaScript/TypeScript 生态中最主流的 linter，用于检查代码质量问题和风格一致性。ESLint 9.0 引入了新的 **Flat Config**（`eslint.config.js`）格式，取代了旧的 `.eslintrc.*` 层叠配置。Flat Config 采用单一配置文件、JavaScript 模块导出数组的形式，更简洁、更可调试。ESLint 的配置体系包括：解析器（parser）、规则（rules）、插件（plugins）、共享配置（shared configs）。

#### 原理

**ESLint 的工作流程**是：解析源代码为 AST → 遍历 AST 节点 → 对每个节点应用注册的规则 → 收集违规报告。解析器负责将源代码转换为 AST，ESLint 默认使用 `espree` 解析器，但对于 TypeScript 需要 `@typescript-eslint/parser`，对于 JSX 需要 `eslint-plugin-react` 的解析器配置。

**Flat Config 的设计**解决了旧配置体系的几个痛点：旧体系使用层叠的 `.eslintrc` 文件，配置合并行为复杂且不透明；插件和共享配置通过字符串引用（`extends: 'eslint:recommended'`），难以追踪实际生效的规则。Flat Config 改为：所有配置在一个文件中以数组形式声明，数组中每个对象是一个配置块（config block），按顺序应用；插件和共享配置通过实际导入的 JavaScript 对象引用，类型安全且可调试。

**规则严重级别**分为三档：`"off"`（或 0，禁用）、`"warn"`（或 1，警告）、`"error"`（或 2，错误）。只有 `"error"` 级别的违规会导致 ESLint 以非零退出码退出（影响 CI 和 pre-commit）。规则可以接受配置选项，格式为 `["error", { option1: value1 }]`。

**TypeScript ESLint 的特殊处理**：`@typescript-eslint` 插件提供了一套 TypeScript 专用规则，其中许多规则与 ESLint 内置规则重叠（如 `no-unused-vars` vs `@typescript-eslint/no-unused-vars`）。正确做法是禁用内置规则，启用 TypeScript 版本，因为 TypeScript 版本能正确处理类型信息。

#### 例子

**1. eslint.config.js（Flat Config，ESLint 9+）**

```javascript
// eslint.config.js
import js from '@eslint/js';
import tseslint from 'typescript-eslint';
import react from 'eslint-plugin-react';
import reactHooks from 'eslint-plugin-react-hooks';
import importPlugin from 'eslint-plugin-import';

export default tseslint.config(
    // 基础推荐规则
    js.configs.recommended,
    ...tseslint.configs.recommendedTypeChecked,
    ...tseslint.configs.stylisticTypeChecked,

    // 全局配置
    {
        languageOptions: {
            parserOptions: {
                projectService: true,           // 自动为每个文件找到 tsconfig
                tsconfigRootDir: import.meta.dirname,
            },
        },
    },

    // React 配置
    {
        files: ['**/*.{jsx,tsx}'],
        plugins: {
            react,
            'react-hooks': reactHooks,
        },
        languageOptions: {
            parserOptions: {
                ecmaFeatures: { jsx: true },
            },
        },
        settings: {
            react: { version: 'detect' },
        },
        rules: {
            ...react.configs.recommended.rules,
            ...reactHooks.configs.recommended.rules,
        },
    },

    // 自定义规则覆盖
    {
        rules: {
            '@typescript-eslint/no-unused-vars': [
                'error',
                { argsIgnorePattern: '^_', varsIgnorePattern: '^_' },
            ],
            '@typescript-eslint/consistent-type-imports': 'error',
            'import/order': [
                'error',
                {
                    groups: ['builtin', 'external', 'internal', 'parent', 'sibling', 'index'],
                    'newlines-between': 'always',
                    alphabetize: { order: 'asc' },
                },
            ],
        },
    },

    // 测试文件放宽规则
    {
        files: ['tests/**/*.{ts,tsx}'],
        rules: {
            '@typescript-eslint/no-explicit-any': 'off',
            '@typescript-eslint/no-non-null-assertion': 'off',
        },
    },

    // 忽略文件
    {
        ignores: ['dist/', 'node_modules/', '*.config.js'],
    },
);
```

**2. package.json 中的 ESLint 脚本**

```json
{
  "scripts": {
    "lint": "eslint .",
    "lint:fix": "eslint . --fix",
    "lint:check": "eslint . --max-warnings 0"
  }
}
```

**3. 编辑器集成（Neovim）**

```lua
-- 使用 nvim-lint 或 none-ls 运行 ESLint
-- 或使用 ESLint LSP 服务器（eslint-plugin-prettier 之外的方式）

-- 方式一：通过 none-ls (null-ls 替代)
local null_ls = require('null-ls')
null_ls.setup({
    sources = {
        null_ls.builtins.diagnostics.eslint_d,
        null_ls.builtins.code_actions.eslint_d,
        null_ls.builtins.formatting.prettierd,
    },
})

-- 方式二：通过 vscode-eslint 服务器（更完整）
-- 需要安装 vscode-langservers-extracted
vim.lsp.config('eslint', {
    cmd = { 'vscode-eslint-language-server', '--stdio' },
    filetypes = { 'javascript', 'javascriptreact', 'typescript', 'typescriptreact' },
    root_markers = { 'eslint.config.js', '.eslintrc', '.git' },
    settings = {
        codeAction = { disableRuleComment = { enable = true, location = 'separateLine' } },
        codeActionOnSave = { enable = true, mode = 'all' },
        format = { enable = false },  -- 格式化交给 Prettier
        validate = 'on',
    },
})
```

#### 总结

- ESLint 9+ 使用 Flat Config（`eslint.config.js`），取代旧的 `.eslintrc` 层叠配置。
- TypeScript 项目使用 `@typescript-eslint` 插件，需禁用重叠的内置规则。
- 规则严重级别：`off`/`warn`/`error`，只有 `error` 影响 CI 退出码。
- 常见注意事项：`projectService: true` 让 TS ESLint 自动找到每个文件的 tsconfig，比旧的 `project` 选项更方便；ESLint 的 `--fix` 只修复风格类规则，不做格式化（格式化是 Prettier 的工作）。

---

### 第 18 讲 Prettier 配置详解

#### 概念

Prettier 是 JavaScript/TypeScript 生态中最主流的代码格式化器，支持 JS、TS、JSX、CSS、HTML、JSON、Markdown 等多种语言。与 ESLint 不同，Prettier 只关注代码排版（缩进、换行、引号等），不检查代码质量。Prettier 是"opinionated"格式化器的典型代表，只提供少量配置项，强制统一风格。

#### 原理

**Prettier 的格式化流程**是：解析源代码为 AST → 按照固定规则重新生成代码文本。Prettier 的核心设计原则是"确定性"——同一份输入永远产生同一份输出，不受运行环境、时间、随机因素影响。这种确定性使得 Prettier 格式化后的代码不会产生无意义的 diff，非常适合版本控制。

**Prettier 的配置项**刻意精简，主要包括：`printWidth`（行宽，默认 80）、`tabWidth`（缩进宽度，默认 2）、`useTabs`（使用 tab 缩进，默认 false）、`semi`（分号，默认 true）、`singleQuote`（单引号，默认 false）、`trailingComma`（尾逗号，默认 "all"）、`bracketSpacing`（对象花括号空格，默认 true）、`arrowParens`（箭头函数参数括号，默认 "always"）、`endOfLine`（行尾符，默认 "lf"）。

**配置文件查找**遵循就近原则：Prettier 从当前文件所在目录开始向上搜索配置文件，支持 `.prettierrc`、`.prettierrc.json`、`.prettierrc.js`、`.prettierrc.cjs`、`prettier.config.js`、`prettier.config.cjs` 等格式，以及 `package.json` 中的 `prettier` 字段。找到的第一个配置文件即为生效配置。`.prettierignore` 文件用于排除不需要格式化的文件/目录。

**Prettier 与编辑器的集成**有两种方式：通过 `prettier` CLI 或通过 Prettier 的 LSP/编辑器扩展。VSCode 的 Prettier 扩展内部运行 `prettier` npm 包；Neovim 通常通过 `conform.nvim` 或 `null-ls` 调用 `prettierd`（Prettier 守护进程，避免每次启动开销）或 `prettier` CLI。

#### 例子

**1. .prettierrc.json（推荐 JSON 格式）**

```json
{
  "printWidth": 100,
  "tabWidth": 2,
  "useTabs": false,
  "semi": true,
  "singleQuote": true,
  "quoteProps": "as-needed",
  "trailingComma": "all",
  "bracketSpacing": true,
  "bracketSameLine": false,
  "arrowParens": "always",
  "endOfLine": "lf",
  "embeddedLanguageFormatting": "auto"
}
```

**2. .prettierignore**

```
node_modules/
dist/
build/
coverage/
*.min.js
package-lock.json
pnpm-lock.yaml
```

**3. package.json 中的 Prettier 脚本**

```json
{
  "scripts": {
    "format": "prettier --write .",
    "format:check": "prettier --check ."
  }
}
```

**4. 编辑器配置**

VSCode:
```json
{
  "[javascript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[typescript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[json]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[markdown]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "editor.formatOnSave": true
}
```

Neovim (conform.nvim):
```lua
require('conform').setup({
    formatters_by_ft = {
        javascript = { 'prettierd', 'prettier', stop_after_first = true },
        typescript = { 'prettierd', 'prettier', stop_after_first = true },
        javascriptreact = { 'prettierd', 'prettier', stop_after_first = true },
        typescriptreact = { 'prettierd', 'prettier', stop_after_first = true },
        json = { 'prettierd', 'prettier', stop_after_first = true },
        css = { 'prettierd', 'prettier', stop_after_first = true },
        html = { 'prettierd', 'prettier', stop_after_first = true },
        markdown = { 'prettierd', 'prettier', stop_after_first = true },
    },
    formatters = {
        prettierd = {
            env = { PRETTIERD_DEFAULT_CONFIG = vim.fn.getcwd() .. '/.prettierrc.json' },
        },
    },
})
```

**5. Prettier 插件扩展**

```json
// .prettierrc.json - 使用插件
{
  "printWidth": 100,
  "plugins": [
    "@trivago/prettier-plugin-sort-imports",
    "prettier-plugin-tailwindcss"
  ],
  "importOrder": [
    "^react",
    "^@/(.*)$",
    "^[./]"
  ],
  "importOrderSeparation": true,
  "importOrderSortSpecifiers": true
}
```

#### 总结

- Prettier 是 opinionated 格式化器，配置项精简，强制统一风格。
- 配置文件查找遵循就近原则，`.prettierignore` 排除不需要格式化的文件。
- `prettierd`（守护进程）比 `prettier` CLI 更快，适合编辑器实时格式化。
- 常见注意事项：`endOfLine: "lf"` 在跨平台项目中很重要，避免 Windows 的 CRLF 导致 diff 噪音；Prettier 插件（如 tailwindcss 排序）可以扩展 Prettier 的能力，但会增加格式化时间。

---

### 第 19 讲 ESLint + Prettier 协同方案

#### 概念

ESLint 和 Prettier 的关系是 JS/TS 生态中最经典的配置难题。ESLint 既能检查代码质量（如未使用变量），也能检查代码风格（如引号一致性）；Prettier 只做格式化。如果两者都启用风格规则，必然冲突——ESLint 要求单引号，Prettier 格式化为双引号，互相覆盖。正确的协同方案是：让 ESLint 只管代码质量，Prettier 管格式化，通过工具消除两者风格规则的重叠。

#### 原理

**冲突的根源**在于 ESLint 和 Prettier 的功能交集。ESLint 内置了大量风格规则（如 `quotes`、`semi`、`indent`），Prettier 也会格式化这些方面。当 ESLint 的风格规则与 Prettier 的配置不一致时，就会出现"ESLint 报错但 Prettier 不修"或"Prettier 格式化后 ESLint 报错"的死循环。

**历史方案**是 `eslint-config-prettier`。这个共享配置会关闭 ESLint 中所有与 Prettier 冲突的风格规则。使用方式是在 ESLint 配置数组的最后添加 `prettier` 配置（确保它覆盖之前的规则）。这个方案的思路是"各管各的"——ESLint 管质量，Prettier 管格式，互不干涉。

**`eslint-plugin-prettier`** 是另一种方案，它将 Prettier 的格式化结果作为 ESLint 规则运行——如果代码与 Prettier 格式化结果不一致，ESLint 报错。这种方案将 Prettier 集成到 ESLint 中，只需运行一个工具。但官方已不推荐这种方式，因为它增加了不必要的复杂性和性能开销。

**现代推荐方案**是：ESLint（仅代码质量规则）+ Prettier（格式化）+ `eslint-config-prettier`（关闭冲突规则）。在编辑器中，Prettier 作为默认格式化器，ESLint 提供 diagnostics 和 code actions。在 pre-commit 中，先运行 `eslint --fix`（修复质量问题），再运行 `prettier --write`（格式化）。

**保存时的执行顺序**很重要。正确的顺序是：先 ESLint fix（修复质量问题和自动导入），再 Prettier format（统一格式）。如果顺序反了，Prettier 格式化后的代码可能被 ESLint fix 改动，导致格式不一致。VSCode 通过 `editor.codeActionsOnSave` + `editor.formatOnSave` 的执行顺序保证这一点。

#### 例子

**1. eslint.config.js（集成 eslint-config-prettier）**

```javascript
// eslint.config.js
import js from '@eslint/js';
import tseslint from 'typescript-eslint';
import prettierConfig from 'eslint-config-prettier';  // 关闭冲突规则

export default tseslint.config(
    js.configs.recommended,
    ...tseslint.configs.recommended,
    {
        rules: {
            // 代码质量规则（非风格规则）
            '@typescript-eslint/no-unused-vars': 'error',
            '@typescript-eslint/no-explicit-any': 'warn',
            'no-console': ['warn', { allow: ['warn', 'error'] }],
        },
    },
    // 必须放在最后，关闭所有与 Prettier 冲突的规则
    prettierConfig,
);
```

**2. package.json 中的脚本（正确的执行顺序）**

```json
{
  "scripts": {
    "lint": "eslint .",
    "lint:fix": "eslint . --fix",
    "format": "prettier --write .",
    "format:check": "prettier --check .",
    "fix:all": "eslint . --fix && prettier --write ."
  }
}
```

**3. VSCode 配置（保存时先 ESLint fix 再 Prettier format）**

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",

  "[javascript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[typescript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },

  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit"
  },

  "eslint.validate": [
    "javascript",
    "typescript",
    "javascriptreact",
    "typescriptreact"
  ]
}
```

**4. Neovim 配置（ESLint diagnostics + Prettier format）**

```lua
-- ESLint LSP 服务器（提供 diagnostics 和 code actions）
vim.lsp.config('eslint', {
    cmd = { 'vscode-eslint-language-server', '--stdio' },
    filetypes = { 'javascript', 'typescript', 'javascriptreact', 'typescriptreact' },
    root_markers = { 'eslint.config.js', '.git' },
    settings = {
        format = { enable = false },  -- 关闭 ESLint 格式化，交给 Prettier
        codeActionOnSave = { enable = true, mode = 'all' },
    },
})

-- Prettier 格式化（通过 conform.nvim）
require('conform').setup({
    formatters_by_ft = {
        javascript = { 'prettierd' },
        typescript = { 'prettierd' },
    },
    format_on_save = function(bufnr)
        -- 先执行 ESLint fix（通过 LSP code action），再格式化
        vim.lsp.buf.code_action({
            filter = function(action)
                return action.command == 'eslint.applyAllFixes'
            end,
            apply = true,
        })
        return { timeout_ms = 1000 }
    end,
})
```

**5. pre-commit 配置**

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/pre-commit/mirrors-eslint
    rev: v9.15.0
    hooks:
      - id: eslint
        args: [--fix]
        files: \.(js|jsx|ts|tsx)$
        types: [file]

  - repo: https://github.com/pre-commit/mirrors-prettier
    rev: v4.0.0-alpha.8
    hooks:
      - id: prettier
        types_or: [javascript, jsx, ts, tsx, json, css, markdown]
```

#### 总结

- ESLint 管代码质量，Prettier 管格式化，通过 `eslint-config-prettier` 关闭冲突规则。
- 不推荐 `eslint-plugin-prettier`（将 Prettier 作为 ESLint 规则），增加复杂性和性能开销。
- 保存时的执行顺序：先 ESLint fix，再 Prettier format。
- 常见注意事项：`eslint-config-prettier` 必须放在配置数组最后才能正确覆盖；如果保存后代码"闪动"（格式化后又变回去），通常是 ESLint 和 Prettier 配置冲突，检查 `eslint-config-prettier` 是否正确加载。

---

### 第 20 讲 Monorepo 中的配置管理

#### 概念

Monorepo（单一代码仓库）是将多个项目/包放在同一个 Git 仓库中的管理方式。在 JS/TS 生态中，Monorepo 非常流行（如 Nx、Turborepo、pnpm workspaces）。Monorepo 中的 LSP 和格式化配置面临独特挑战：多个包可能有不同的 tsconfig、ESLint 配置和 Prettier 配置；语言服务器需要正确解析跨包导入；格式化工具需要在不同子包中使用不同规则。

#### 原理

**Monorepo 的目录结构**通常为：根目录包含全局配置和工具脚本，`packages/` 或 `apps/` 目录包含各子项目。每个子项目有自己的 `package.json` 和可能的 `tsconfig.json`，根目录有全局的 `tsconfig.base.json`、`eslint.config.js`、`.prettierrc`。

**TypeScript 项目引用（Project References）**是 Monorepo 中 TS LSP 的关键机制。通过 `tsconfig.json` 中的 `references` 字段，可以声明项目间的依赖关系。TypeScript Language Server 利用项目引用来正确解析跨包导入，实现增量编译和精确的类型检查。`composite: true` 选项是项目引用的前提，它要求每个被引用的项目符合一定的结构规范。

**ESLint Flat Config 在 Monorepo 中的优势**：Flat Config 天然支持单文件管理全局配置，通过 `files` 和 `ignores` 模式匹配为不同子包应用不同规则。例如，可以为 `apps/*` 和 `packages/*` 应用不同的规则集，或为特定包覆盖规则。

**Prettier 在 Monorepo 中通常使用根目录的单一配置**，保持全仓库格式统一。如果某些包需要不同的格式化规则，可以在子包目录放置 `.prettierrc`，Prettier 的就近原则会自动生效。

**pnpm workspaces 与 LSP**：pnpm 使用符号链接管理 `node_modules`，TypeScript Language Server 需要正确解析这些符号链接。`tsconfig.json` 中的 `moduleResolution: "bundler"` 或 `"nodenext"` 配合 pnpm 的 `node-linker` 设置可以解决大部分解析问题。

#### 例子

**1. Monorepo 目录结构**

```
my-monorepo/
├── package.json              # 根 package.json (workspaces 配置)
├── pnpm-workspace.yaml       # pnpm workspace 配置
├── tsconfig.base.json        # 基础 TS 配置
├── eslint.config.js          # 全局 ESLint 配置
├── .prettierrc.json          # 全局 Prettier 配置
├── packages/
│   ├── ui/
│   │   ├── package.json
│   │   ├── tsconfig.json     # extends 根 tsconfig.base.json
│   │   └── src/
│   ├── utils/
│   │   ├── package.json
│   │   ├── tsconfig.json
│   │   └── src/
├── apps/
│   ├── web/
│   │   ├── package.json
│   │   ├── tsconfig.json
│   │   └── src/
│   └── api/
│       ├── package.json
│       ├── tsconfig.json
│       └── src/
```

**2. pnpm-workspace.yaml**

```yaml
packages:
  - 'packages/*'
  - 'apps/*'
```

**3. tsconfig.base.json（根基础配置）**

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "strict": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "composite": true,
    "incremental": true,
    "baseUrl": ".",
    "paths": {
      "@myorg/ui": ["./packages/ui/src"],
      "@myorg/utils": ["./packages/utils/src"]
    }
  }
}
```

**4. 子包 tsconfig.json（packages/ui/tsconfig.json）**

```json
{
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "outDir": "./dist",
    "rootDir": "./src"
  },
  "include": ["src"],
  "references": [
    { "path": "../utils" }
  ]
}
```

**5. eslint.config.js（Monorepo 全局配置）**

```javascript
// eslint.config.js
import js from '@eslint/js';
import tseslint from 'typescript-eslint';
import prettierConfig from 'eslint-config-prettier';

export default tseslint.config(
    js.configs.recommended,
    ...tseslint.configs.recommended,

    // 全局 TypeScript 配置
    {
        languageOptions: {
            parserOptions: {
                projectService: {
                    defaultProject: 'tsconfig.base.json',
                    allowDefaultProject: ['*.config.js'],
                },
                tsconfigRootDir: import.meta.dirname,
            },
        },
    },

    // React 应用规则（仅 apps/web）
    {
        files: ['apps/web/**/*.{ts,tsx}'],
        rules: {
            'react/react-in-jsx-scope': 'off',
        },
    },

    // 库代码更严格（packages/*）
    {
        files: ['packages/**/*.{ts,tsx}'],
        rules: {
            '@typescript-eslint/no-explicit-any': 'error',  // 库代码禁止 any
        },
    },

    // 测试文件放宽
    {
        files: ['**/*.test.{ts,tsx}', '**/*.spec.{ts,tsx}'],
        rules: {
            '@typescript-eslint/no-non-null-assertion': 'off',
        },
    },

    {
        ignores: ['**/dist/', '**/node_modules/', '**/.turbo/'],
    },

    prettierConfig,
);
```

**6. 根 package.json 脚本**

```json
{
  "scripts": {
    "lint": "eslint .",
    "lint:fix": "eslint . --fix",
    "format": "prettier --write .",
    "typecheck": "tsc --build",
    "test": "vitest"
  }
}
```

#### 总结

- Monorepo 通过 TypeScript 项目引用（`references` + `composite`）实现跨包类型解析。
- ESLint Flat Config 通过 `files` 模式为不同子包应用不同规则，单文件管理全局配置。
- Prettier 通常使用根目录单一配置保持全仓库格式统一，子包可覆盖。
- 常见注意事项：`projectService` 自动为每个文件找到对应的 tsconfig，是 Monorepo 中 TS ESLint 的推荐配置；pnpm 的符号链接可能导致 LSP 解析问题，确保 `moduleResolution` 配置正确；根目录的 `tsconfig.base.json` 应只包含通用配置，子包通过 `extends` 继承并覆盖。


---

## 第 6 章 Rust

Rust 生态的 LSP 和格式化配置是所有语言中最统一、最简洁的——`rust-analyzer` 是事实上的唯一 LSP 服务器，`rustfmt` 是唯一的格式化器，`Clippy` 是标准的 lint 工具。三者都由 Rust 官方团队维护，集成度高，配置简洁。本章用 3 讲的篇幅，分别讲解这三个工具的配置方法。

### 第 21 讲 rust-analyzer 配置详解

#### 概念

`rust-analyzer` 是 Rust 官方维护的 LSP 服务器，用 Rust 编写，提供代码补全、跳转定义、类型推断、内联提示（inlay hints）、代码诊断等全套 LSP 功能。它是 VSCode Rust 扩展的底层引擎，也是 Neovim、Helix、Emacs 等编辑器的首选 Rust LSP。`rust-analyzer` 的配置通过 LSP 的 `initializationOptions` 和 `workspace/didChangeConfiguration` 传递，配置项以 `rust-analyzer.*` 为前缀。

#### 原理

**rust-analyzer 的架构**是一个长期运行的服务进程，内部维护整个 crate 的语义模型。启动后，rust-analyzer 会解析 `Cargo.toml`，构建依赖树，解析所有源文件为 AST，并进行类型推断。这个过程可能需要数秒到数十秒（取决于项目大小），期间 CPU 占用较高。一旦索引完成，后续的补全、跳转等操作响应极快。

**Cargo 集成**是 rust-analyzer 的核心特性。rust-analyzer 深度理解 Cargo 项目结构：它读取 `Cargo.toml` 获取依赖列表，运行 `cargo metadata` 获取依赖的精确版本和路径，通过 `cargo check` 进行编译诊断。`checkOnSave` 配置项控制是否在保存时运行 `cargo check`（或 `cargo clippy`），这是 rust-analyzer 提供编译诊断的主要方式。

**proc-macro 支持**是 rust-analyzer 的一个重要且复杂的特性。过程宏（proc-macro）在编译时生成代码，rust-analyzer 需要实际执行这些宏才能正确理解生成的代码。`rust-analyzer.procMacro.enable` 配置项控制是否启用 proc-macro 展开，启用后 rust-analyzer 会编译并执行 proc-macro crate。这对于使用 `serde`、`tokio` 等重度依赖过程宏的 crate 非常重要。

**inlay hints**（内联提示）是 rust-analyzer 最受欢迎的功能之一。它在代码中内联显示类型推断结果、参数名称、生命周期等信息，无需悬停。例如，对于 `let x = func(42)`，rust-analyzer 可以在 `x` 旁边显示推断的类型，在 `42` 旁边显示参数名 `count`。inlay hints 通过 LSP 的 `textDocument/inlayHint` 方法提供。

#### 例子

**1. Neovim 配置（rust-analyzer）**

```lua
-- ~/.config/nvim/lsp/rust_analyzer.lua
return {
    cmd = { 'rust-analyzer' },
    filetypes = { 'rust' },
    root_markers = { 'Cargo.toml', 'Cargo.lock' },
    settings = {
        ['rust-analyzer'] = {
            -- Cargo 配置
            cargo = {
                features = 'all',          -- 编译所有 features
                allTargets = true,          -- 编译所有目标（包括 tests/benches）
                buildScripts = { enable = true },
            },
            -- 保存时检查
            checkOnSave = {
                enable = true,
                command = 'clippy',        -- 使用 clippy 替代 cargo check
                extraArgs = { '--', '-W', 'clippy::pedantic' },
            },
            -- proc-macro 支持
            procMacro = {
                enable = true,
                attributes = { enable = true },
            },
            -- inlay hints
            inlayHints = {
                bindingModeHints = { enable = false },
                chainingHints = { enable = true },
                closingBraceHints = { enable = true, minLines = 25 },
                lifetimeElisionHints = { enable = 'always', useParameterNames = true },
                maxLength = 25,
                parameterHints = { enable = true },
                reborrowHints = { enable = 'never' },
                renderColons = true,
                typeHints = { enable = true, hideClosureInitialization = false },
            },
            -- 补全配置
            completion = {
                callable = { snippets = 'fill_arguments' },
                postfix = { enable = true },
                privateEditable = { enable = false },
                snippets = {
                    -- 自定义 snippet
                    ["Arc::new"] = { postfix = "arc", body = "Arc::new(${receiver})", description = "Wrap in Arc" },
                },
            },
            -- 诊断配置
            diagnostics = {
                enable = true,
                experimental = { enable = false },
                disabled = { 'unresolved-import' },
            },
            -- 格式化（使用 rustfmt）
            rustfmt = {
                overrideCommand = { 'leptosfmt', '--stdin', '--rustfmt' },
                extraArgs = { '--edition', '2021' },
            },
            -- 工作区索引
            workspace = { symbol = { search = { kind = 'all_symbols', limit = 128 } } },
            -- lens（代码上方可点击的操作）
            lens = {
                enable = true,
                debug = { enable = true },
                implementations = { enable = true },
                run = { enable = true },
                references = { adt = { enable = true }, method = { enable = true } },
            },
        },
    },
}
```

**2. VSCode 配置（settings.json）**

```json
{
  "rust-analyzer.cargo.features": "all",
  "rust-analyzer.cargo.allTargets": true,
  "rust-analyzer.checkOnSave.command": "clippy",
  "rust-analyzer.checkOnSave.extraArgs": ["--", "-W", "clippy::pedantic"],
  "rust-analyzer.procMacro.enable": true,
  "rust-analyzer.inlayHints.chainingHints.enable": true,
  "rust-analyzer.inlayHints.parameterHints.enable": true,
  "rust-analyzer.inlayHints.typeHints.enable": true,
  "rust-analyzer.lens.enable": true,
  "rust-analyzer.lens.run.enable": true,
  "rust-analyzer.lens.debug.enable": true,
  "rust-analyzer.workspace.symbol.search.limit": 128
}
```

**3. rust-analyzer.toml（项目级配置文件，rust-analyzer 2024+ 支持）**

```toml
# rust-analyzer.toml（放在项目根目录，与 Cargo.toml 同级）
# rust-analyzer 会自动读取此文件，无需编辑器配置

[cargo]
features = "all"
allTargets = true

[checkOnSave]
command = "clippy"
extraArgs = ["--", "-W", "clippy::pedantic"]

[procMacro]
enable = true

[inlayHints]
chainingHints.enable = true
parameterHints.enable = true
typeHints.enable = true
```

**4. .cargo/config.toml（影响 rust-analyzer 的 cargo 行为）**

```toml
# .cargo/config.toml
[build]
# 使用更快的链接器
rustflags = ["-C", "link-arg=-fuse-ld=lld"]

[env]
# 设置环境变量
RUST_LOG = "debug"
```

#### 总结

- rust-analyzer 是 Rust 官方 LSP 服务器，深度集成 Cargo，提供全套 LSP 功能。
- `checkOnSave` 配置控制保存时的编译检查，推荐使用 `clippy` 替代 `cargo check`。
- proc-macro 支持对于使用 serde/tokio 等库的项目至关重要。
- 常见注意事项：首次打开大型项目时 rust-analyzer 需要索引，期间 CPU 占用高是正常的；`cargo.features = "all"` 会编译所有 feature，可能增加索引时间；`rust-analyzer.toml` 是较新的项目级配置文件，优先级高于编辑器配置。

---

### 第 22 讲 rustfmt 配置

#### 概念

`rustfmt` 是 Rust 官方的代码格式化器，由 Rust 团队维护。与 Python 的 Black 类似，rustfmt 是 opinionated 格式器，提供有限的配置项，强制统一风格。rustfmt 既是命令行工具（`cargo fmt`），也可以通过 rust-analyzer 的 `textDocument/formatting` 方法在编辑器中调用。配置文件为 `rustfmt.toml` 或 `.rustfmt.toml`。

#### 原理

**rustfmt 的工作原理**是解析 Rust 源代码为 AST，然后按照固定规则重新生成代码。rustfmt 的格式化规则大部分不可配置——例如它强制使用 4 空格缩进、强制在特定位置换行、强制花括号风格。这种"不妥协"的设计确保了全 Rust 生态的代码风格统一，任何 Rust 开发者打开任何项目都能立即适应其代码风格。

**可配置项**虽然有限，但涵盖了一些常见的风格偏好：`max_width`（行宽，默认 100）、`hard_tabs`（使用 tab 缩进，默认 false）、`tab_spaces`（缩进宽度，默认 4）、`edition`（Rust edition，影响格式化行为）、`use_field_init_shorthand`（使用字段初始化简写，如 `Foo { x }` 替代 `Foo { x: x }`）、`use_try_shorthand`（使用 `?` 替代 `try!`）、`imports_granularity`（import 粒度，控制 use 语句的合并方式）、`group_imports`（import 分组）。

**`cargo fmt` 的工作方式**是：对项目中的每个 `.rs` 文件运行 rustfmt，原地修改文件。`cargo fmt --check` 只检查不修改，用于 CI 中验证代码是否已格式化。`cargo fmt -- --package <name>` 可以格式化特定包。

**rustfmt 与 rust-analyzer 的集成**：rust-analyzer 内部调用 rustfmt 进行格式化。当编辑器发送 `textDocument/formatting` 请求时，rust-analyzer 读取 `rustfmt.toml` 配置，调用 rustfmt 格式化代码，返回 TextEdit 列表。`rust-analyzer.rustfmt.overrideCommand` 配置项允许指定自定义格式化命令（如 `leptosfmt` 用于 Leptos 框架的宏格式化）。

#### 例子

**1. rustfmt.toml（项目根目录）**

```toml
# rustfmt.toml 或 .rustfmt.toml

# 行宽
max_width = 100

# 缩进
hard_tabs = false
tab_spaces = 4

# Edition（影响格式化行为）
edition = "2021"

# 简写
use_field_init_shorthand = true
use_try_shorthand = true

# Import 组织
imports_granularity = "Crate"    # "Preserve" | "Crate" | "Module" | "Item" | "One"
group_imports = "StdExternalCrate"  # "Preserve" | "StdExternalCrate" | "One"

# 注释
wrap_comments = true              # 自动换行长注释
comment_width = 100
normalize_comments = true         # 规范化注释风格
normalize_doc_attributes = true   # 将 #[doc="..."] 转为 ///

# 排序
reorder_imports = true            # 排序 use 语句
reorder_modules = true            # 排序 mod 声明

# 杂项
fn_single_line = false            # 单行函数
where_single_line = false         # 单行 where 子句
trailing_comma = "Vertical"       # 尾逗号策略
match_arm_blocks = true           # match 臂使用块
match_block_trailing_comma = true # match 块尾逗号
```

**2. Cargo.toml 中的配置**

```toml
# Cargo.toml 中也可以指定 rustfmt 配置
[workspace.metadata.rustfmt]
max_width = 100
edition = "2021"
```

**3. 命令行使用**

```bash
# 格式化整个项目
cargo fmt

# 检查是否已格式化（CI 用）
cargo fmt --check

# 格式化单个文件
rustfmt src/main.rs

# 格式化并输出到 stdout（不修改文件）
rustfmt --emit stdout src/main.rs

# 使用自定义配置文件
rustfmt --config-path custom-rustfmt.toml src/main.rs
```

**4. 编辑器配置**

VSCode:
```json
{
  "[rust]": {
    "editor.defaultFormatter": "rust-lang.rust-analyzer",
    "editor.formatOnSave": true
  },
  "rust-analyzer.rustfmt.extraArgs": ["--edition", "2021"]
}
```

Neovim (conform.nvim):
```lua
require('conform').setup({
    formatters_by_ft = {
        rust = { 'rustfmt' },
    },
    formatters = {
        rustfmt = {
            args = { '--edition', '2021', '--emit', 'stdout' },
        },
    },
})
```

**5. pre-commit 配置**

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/doublify/pre-commit-rust
    rev: v1.0
    hooks:
      - id: fmt
        args: ['--check']  # CI 中只检查
```

#### 总结

- rustfmt 是 Rust 官方格式化器，opinionated 风格，配置项有限。
- `rustfmt.toml` 是配置文件，`cargo fmt` 是命令行入口。
- `imports_granularity` 和 `group_imports` 是最常调整的配置项。
- 常见注意事项：`imports_granularity` 和 `group_imports` 在某些 rustfmt 版本中是 unstable 特性，可能需要 nightly rustfmt；`cargo fmt --check` 是 CI 中验证格式化的标准方式。

---

### 第 23 讲 Clippy 集成与 Lint 规则

#### 概念

`Clippy` 是 Rust 官方的 lint 工具，提供数百条 lint 规则，覆盖代码风格、常见错误、性能优化、惯用法等方面。Clippy 是 `rustc` 的编译器插件，通过 `cargo clippy` 命令运行。Clippy 的 lint 规则分为四个级别：`correctness`（正确性，默认 deny）、`suspicious`（可疑，默认 warn）、`style`（风格，默认 warn）、`complexity`（复杂性，默认 warn）、`perf`（性能，默认 warn）、`pedantic`（严格，默认 allow）、`nursery`（实验性，默认 allow）、`cargo`（Cargo 配置检查，默认 warn）。

#### 原理

**Clippy 的工作原理**是作为 rustc 的 lint pass 运行。当执行 `cargo clippy` 时，Clippy 在编译过程中分析 AST 和类型信息，应用所有启用的 lint 规则。与 rustfmt 不同，Clippy 不仅检查风格，还检查潜在的逻辑错误（如 `if x == true` 应简化为 `if x`）、性能问题（如不必要的克隆）、惯用法建议（如使用 `?` 替代 `match` 处理 `Result`）。

**lint 级别控制**通过属性（attribute）实现。可以在代码中使用 `#[allow(clippy::rule_name)]`、`#[warn(clippy::rule_name)]`、`#[deny(clippy::rule_name)]` 控制单条规则的级别。在 `Cargo.toml` 或 `clippy.toml` 中可以设置全局级别。`clippy::pedantic` 和 `clippy::restriction` 是两个 lint 组，包含更严格的规则，默认不启用，需要手动开启。

**与 rust-analyzer 的集成**：rust-analyzer 的 `checkOnSave.command = "clippy"` 配置使得保存文件时运行 `cargo clippy` 而非 `cargo check`，这样 rust-analyzer 的诊断信息就包含了 Clippy 的 lint 结果。这是在编辑器中实时获取 Clippy 建议的推荐方式。

**`clippy.toml`** 是 Clippy 的配置文件，用于调整某些 lint 的阈值。例如，`cognitive-complexity-threshold` 控制 `clippy::cognitive_complexity` 的触发阈值，`too-many-arguments-threshold` 控制 `clippy::too_many_arguments` 的参数数量阈值。

#### 例子

**1. Cargo.toml 中的 Clippy 配置**

```toml
# Cargo.toml
[lints.clippy]
# 启用 pedantic 组的大部分规则
pedantic = { level = "warn", priority = -1 }
# 单独启用/禁用规则
must_use = "warn"
need_pass_by_value = "allow"       # 允许按值传递
module_name_repetitions = "allow"   # 允许模块名重复
missing_errors_doc = "allow"        # 允许缺少错误文档

[lints.rust]
# rustc 内置 lint
unsafe_code = "deny"
unused_must_use = "deny"
rust_2018_idioms = { level = "warn", priority = -1 }
```

**2. clippy.toml（项目根目录）**

```toml
# clippy.toml
cognitive-complexity-threshold = 30
too-many-arguments-threshold = 7
type-complexity-threshold = 250
single-char-binding-names-threshold = 5
# 文件头部检查
# 标准库版本检查
msrv = "1.75.0"
```

**3. 代码中的 lint 属性控制**

```rust
// 全 crate 级别配置（lib.rs 或 main.rs 顶部）
#![warn(clippy::pedantic)]
#![warn(clippy::nursery)]
#![allow(clippy::module_name_repetitions)]
#![allow(clippy::must_use)]

// 单函数级别
#[allow(clippy::too_many_arguments)]
fn process_data(
    a: i32, b: i32, c: i32, d: i32,
    e: i32, f: i32, g: i32, h: i32,
) -> i32 {
    // ...
}

// 临时禁用
#[allow(clippy::needless_return)]
fn foo() -> i32 {
    return 42;  // Clippy 通常建议省略 return
}

// 内联禁用
let _x = 42; // clippy::allow(needless_lifetimes)
```

**4. 命令行使用**

```bash
# 运行 Clippy
cargo clippy

# 运行 Clippy 并自动修复（部分规则支持）
cargo clippy --fix

# 运行所有 pedantic 规则
cargo clippy -- -W clippy::pedantic

# 运行所有 restriction 规则
cargo clippy -- -W clippy::restriction

# 对 workspace 中所有包运行
cargo clippy --workspace --all-targets

# CI 中使用（任何警告都导致失败）
cargo clippy -- -D warnings
```

**5. rust-analyzer 中启用 Clippy 诊断**

```lua
-- Neovim
vim.lsp.config('rust_analyzer', {
    settings = {
        ['rust-analyzer'] = {
            checkOnSave = {
                command = 'clippy',
                extraArgs = { '--', '-W', 'clippy::pedantic' },
            },
        },
    },
})
```

```json
// VSCode
{
  "rust-analyzer.checkOnSave.command": "clippy",
  "rust-analyzer.checkOnSave.extraArgs": ["--", "-W", "clippy::pedantic"]
}
```

#### 总结

- Clippy 是 Rust 官方 lint 工具，覆盖风格、正确性、性能、惯用法。
- 通过 `checkOnSave.command = "clippy"` 在编辑器中实时获取 Clippy 诊断。
- `Cargo.toml` 的 `[lints.clippy]` 段是配置全局 lint 级别的推荐方式（Cargo 1.74+）。
- 常见注意事项：`pedantic` 组的规则较严格，建议以 `warn` 级别启用并逐条 `allow` 不适用的规则；`cargo clippy -- -D warnings` 是 CI 中强制零警告的标准做法。

---

## 第 7 章 Go

Go 语言的工具链以"开箱即用"著称——`gofmt` 是 Go 自带的格式化器，`gopls` 是官方 LSP 服务器，`golangci-lint` 是社区标准的 lint 聚合器。Go 的设计哲学是"一种工具做一件事"，格式化、lint、LSP 各司其职，配置简洁。本章用 3 讲讲解这三个工具的配置。

### 第 24 讲 gopls 配置

#### 概念

`gopls`（发音"go-please"）是 Go 官方团队维护的 LSP 服务器，用 Go 编写。它提供代码补全、跳转、悬停文档、诊断、重构等全套 LSP 功能。gopls 深度理解 Go 模块系统（Go modules），能正确解析跨模块的依赖关系。gopls 的配置通过 LSP 的 `initializationOptions` 传递，配置结构定义在 `gopls/settings` 包中。

#### 原理

**gopls 的架构**是一个长期运行的 Go 进程，内部维护 Go 工作区的语义模型。启动后，gopls 解析 `go.mod`，加载所有依赖包，解析源文件为 AST，进行类型检查。与 rust-analyzer 类似，首次索引需要时间，但后续操作响应快。

**Go modules 集成**是 gopls 的核心。gopls 读取 `go.mod` 确定模块路径和依赖版本，通过 `go list` 命令获取包信息。对于工作区模式（Go 1.18+ 的 `go.work`），gopls 支持多模块工作区，正确解析跨模块的本地替换。

**构建约束（build constraints）**处理是 gopls 的一个重要特性。Go 使用 `//go:build` 注释控制条件编译（如按 OS/架构选择文件）。gopls 需要理解这些约束，只分析当前构建条件下生效的文件。`buildFlags` 配置项允许传递额外的构建标签（如 `-tags=integration`）。

**`go.work` 工作区**支持是 gopls 对 monorepo 场景的解决方案。`go.work` 文件声明工作区中的多个模块，gopls 将它们视为一个统一的代码空间，支持跨模块跳转和重构。这对于在多个相关模块间同时开发非常有用。

#### 例子

**1. Neovim 配置**

```lua
-- ~/.config/nvim/lsp/gopls.lua
return {
    cmd = { 'gopls' },
    filetypes = { 'go', 'gomod', 'gowork', 'gotmpl' },
    root_markers = { 'go.work', 'go.mod', '.git' },
    settings = {
        ['gopls'] = {
            -- 使用 gofumpt（更严格的 gofmt）
            gofumpt = true,
            -- 静态分析
            staticcheck = true,
            -- 目录过滤
            directoryFilters = { '-.git', '-.vscode', '-node_modules' },
            -- 语义令牌（语法高亮增强）
            semanticTokens = true,
            -- 环境变量
            env = { GOFLAGS = '-tags=integration' },
            -- 构建标签
            buildFlags = { '-tags=integration' },
            -- 代码透镜
            hints = {
                assignVariableTypes = true,
                compositeLiteralFields = true,
                compositeLiteralTypes = true,
                constantValues = true,
                functionTypeParameters = true,
                parameterNames = true,
                rangeVariableTypes = true,
            },
            -- 分析器
            analyses = {
                -- 启用额外的分析器
                nilness = true,
                unusedwrite = true,
                useany = true,
                -- 禁用某些分析器
                shadow = false,
            },
            -- 补全
            completion = {
                completionPostfix = true,
                documentation = true,
                deepCompletion = true,
                fuzzyMatching = true,
                labelDetails = true,
            },
            -- 格式化
            formatting = {
                gofumpt = true,
            },
            -- codelens
            codelenses = {
                gc_details = false,
                generate = true,
                regenerate_cgo = true,
                run_govulncheck = true,
                test = true,
                tidy = true,
                upgrade_dependency = true,
                vendor = true,
            },
        },
    },
}
```

**2. VSCode 配置**

```json
{
  "gopls": {
    "gofumpt": true,
    "staticcheck": true,
    "hints": {
      "assignVariableTypes": true,
      "parameterNames": true,
      "rangeVariableTypes": true
    },
    "analyses": {
      "nilness": true,
      "unusedwrite": true
    },
    "build.buildFlags": ["-tags=integration"],
    "ui.diagnostic.staticcheck": true
  },
  "go.useLanguageServer": true,
  "go.lintTool": "golangci-lint",
  "go.lintOnSave": "workspace",
  "[go]": {
    "editor.defaultFormatter": "golang.go",
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
      "source.organizeImports": "explicit"
    }
  }
}
```

**3. go.work 文件（多模块工作区）**

```
// go.work
go 1.22

use (
    ./services/api
    ./services/worker
    ./packages/common
    ./packages/logger
)
```

**4. 查看 gopls 日志**

```bash
# Neovim 中启用 gopls 日志
:lua vim.lsp.set_log_level("debug")

# 或通过 gopls 的 -rpc.trace 选项
# gopls -rpc.trace -logfile /tmp/gopls.log
```

#### 总结

- gopls 是 Go 官方 LSP 服务器，深度集成 Go modules 和 go.work 工作区。
- `staticcheck` 和 `gofumpt` 是两个最常用的配置项，推荐开启。
- `go.work` 支持多模块工作区，是 Go monorepo 的标准方案。
- 常见注意事项：`buildFlags` 中的构建标签需要与实际构建一致，否则 gopls 可能分析错误的文件集；大型项目首次索引可能需要较长时间，可以通过 `directoryFilters` 排除不相关的目录加速。

---

### 第 25 讲 gofmt 与 goimports

#### 概念

`gofmt` 是 Go 自带的代码格式化器，随 Go 工具链分发，无需额外安装。gofmt 是最"opinionated"的格式化器之一——它几乎没有配置项，强制统一所有 Go 代码的格式。`goimports` 是 gofmt 的增强版，在格式化的基础上自动管理 import 语句（添加缺失的导入、移除未使用的导入、排序）。`gofumpt` 是社区维护的更严格版本，在 gofmt 基础上增加了一些额外的格式化规则。

#### 原理

**gofmt 的设计哲学**体现了 Go 语言"一种正确的做法"的理念。gofmt 几乎不可配置——它强制使用 tab 缩进、特定的空格规则、特定的换行位置。这种"零配置"设计确保了全球所有 Go 代码的格式统一，任何 Go 开发者打开任何项目都能立即适应。gofmt 的输出是确定性的，不会因运行环境或版本（同一 Go 版本）产生不同结果。

**gofmt 的工作原理**是解析 Go 源代码为 AST，然后按照固定规则重新输出。gofmt 不改变代码语义，只调整排版。gofmt 处理的内容包括：缩进（tab）、空格（运算符两侧、关键字后）、对齐（连续行的对齐）、换行（长行的换行位置）。

**goimports** 在 gofmt 的基础上增加了 import 管理。它扫描源文件中的标识符使用，自动查找匹配的包路径，添加缺失的 import 语句。同时移除未使用的 import，并按标准库、第三方库的顺序排序。goimports 需要访问 Go 模块缓存来查找包路径，因此比 gofmt 稍慢。

**gofumpt** 是社区维护的增强格式化器，在 gofmt 的基础上增加了一些更严格的规则。例如，gofumpt 强制空 `struct{}` 使用 `struct{}` 而非 `struct {}`，强制简单的错误处理不换行等。gofumpt 的输出与 gofmt 兼容（gofumpt 格式化的代码通过 gofmt 检查），但反过来不成立。gopls 通过 `gofumpt: true` 配置项支持 gofumpt。

#### 例子

**1. gofmt 命令行使用**

```bash
# 格式化文件（原地修改）
gofmt -w main.go

# 格式化整个目录
gofmt -w .

# 检查是否已格式化（输出差异，不修改）
gofmt -l .    # 列出未格式化的文件
gofmt -d .    # 显示差异

# 从 stdin 读取，输出到 stdout
cat main.go | gofmt
```

**2. goimports 安装和使用**

```bash
# 安装
go install golang.org/x/tools/cmd/goimports@latest

# 使用（与 gofmt 相同的参数）
goimports -w main.go
goimports -l .
goimports -d .

# 指定本地包的前缀（放在单独的分组中）
goimports -local mycompany.com/myproject -w .
```

**3. gofumpt 安装和使用**

```bash
# 安装
go install mvdan.cc/gofumpt@latest

# 使用
gofumpt -w .
gofumpt -l .
```

**4. 编辑器配置**

VSCode:
```json
{
  "go.formatTool": "goimports",  // "gofmt" | "goimports" | "gofumpt"
  "goimportsLocalPrefix": "mycompany.com/myproject",
  "[go]": {
    "editor.formatOnSave": true,
    "editor.defaultFormatter": "golang.go"
  }
}
```

Neovim (conform.nvim):
```lua
require('conform').setup({
    formatters_by_ft = {
        go = { 'goimports', 'gofumpt' },
    },
    formatters = {
        goimports = {
            args = { '-srcdir', vim.fn.getcwd() },
        },
        gofumpt = {
            args = { '-extra' },  -- 启用额外规则
        },
    },
})
```

**5. 通过 gopls 格式化（LSP 方式）**

```lua
-- Neovim: 使用 gopls 内置的格式化（基于 gofmt/gofumpt）
vim.lsp.config('gopls', {
    settings = {
        ['gopls'] = {
            gofumpt = true,  -- 使用 gofumpt 规则
            formatting = { gofumpt = true },
        },
    },
})

-- 格式化快捷键
vim.keymap.set('n', '<leader>f', function()
    vim.lsp.buf.format({ name = 'gopls' })
end)
```

**6. pre-commit 配置**

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/dnephin/pre-commit-golang
    rev: v0.5.1
    hooks:
      - id: go-fmt
      - id: go-imports
        args: [-local, mycompany.com/myproject]

  # 或使用 gofumpt
  - repo: https://github.com/tekwiz/pre-commit-gofumpt
    rev: v0.7.0
    hooks:
      - id: gofumpt
```

#### 总结

- gofmt 是 Go 自带的零配置格式化器，强制统一所有 Go 代码格式。
- goimports 在 gofmt 基础上增加 import 自动管理（添加/移除/排序）。
- gofumpt 是更严格的社区增强版，通过 gopls 的 `gofumpt: true` 配置启用。
- 常见注意事项：`goimports -local` 参数将本地包放在单独分组，是团队 monorepo 的常用配置；gofmt 的 `-l` 选项（列出未格式化文件）是 CI 检查的标准方式。

---

### 第 26 讲 golangci-lint 集成

#### 概念

`golangci-lint` 是 Go 生态中最流行的 lint 聚合器，它集成了数十个 Go lint 工具（如 `staticcheck`、`govet`、`errcheck`、`gosec`、`revive` 等），提供统一的配置和运行接口。golangci-lint 不是 LSP 服务器，而是命令行工具，但可以与编辑器集成（通过 LSP 的 `diagnostics` 或编辑器的 lint 插件）和 CI 集成。

#### 原理

**golangci-lint 的架构**是一个 lint 聚合框架。它本身不实现 lint 规则，而是作为运行器，加载和执行各种 Go lint 工具（称为 linter）。golangci-lint 管理这些 linter 的生命周期，提供统一的配置接口，并聚合所有 linter 的输出。这种设计避免了逐个安装和配置每个 lint 工具的麻烦。

**并行执行**是 golangci-lint 的性能优势。它并行运行多个 linter，共享 AST 解析结果（避免每个 linter 重复解析），对于大型项目可以显著减少总检查时间。golangci-lint 还支持缓存，增量检查只分析变更的文件。

**配置文件** `.golangci.yml`（或 `.golangci.yaml`、`.golangci.toml`）是 golangci-lint 的核心配置。配置文件定义启用的 linter、各 linter 的配置、排除规则、严重级别等。golangci-lint 支持从项目根目录向上查找配置文件，也支持 `--config` 参数指定配置文件路径。

**与编辑器的集成**有两种方式：一是通过 gopls 的 `staticcheck` 集成（gopls 内置了 staticcheck，但不支持其他 golangci-lint 的 linter）；二是通过编辑器的 lint 插件直接运行 golangci-lint（如 Neovim 的 `nvim-lint` 或 VSCode 的 Go 扩展的 `go.lintTool: "golangci-lint"` 配置）。后者支持完整的 golangci-lint linter 集，但响应速度不如 gopls 内置的 staticcheck。

#### 例子

**1. .golangci.yml（完整配置）**

```yaml
# .golangci.yml
run:
  timeout: 5m
  go: "1.22"
  modules-download-mode: readonly

linters:
  # 禁用所有默认 linter，显式启用
  disable-all: true
  enable:
    - errcheck          # 检查未处理的错误
    - gosimple          # 简化代码建议
    - govet             # go vet 检查
    - ineffassign       # 检测无效赋值
    - staticcheck       # 静态分析
    - unused            # 检测未使用的代码
    - gosec             # 安全检查
    - revive            # golint 替代品
    - gocritic          # 代码改进建议
    - misspell          # 拼写检查
    - gofmt             # 格式检查
    - goimports         # import 排序检查
    - whitespace        # 空白检查
    - gocyclo           # 圈复杂度
    - bodyclose         # 检查 http.Response.Body 是否关闭
    - prealloc          # 切片预分配建议
    - nolintlint        # 检查 //nolint 注释的使用

linters-settings:
  staticcheck:
    checks: ["all"]
  gocyclo:
    min-complexity: 15
  goimports:
    local-prefixes: mycompany.com/myproject
  gocritic:
    enabled-tags:
      - performance
      - style
    disabled-checks:
      - hugeParam       # 大参数按值传递（有时是故意的）
  revive:
    rules:
      - name: exported
        disabled: true
  errcheck:
    check-type-assertions: true
    check-blank: true
  gosec:
    excludes:
      - G104  # 审计错误未检查（errcheck 已覆盖）

issues:
  # 每个文件的最多问题数
  max-issues-per-linter: 0
  max-same-issues: 0
  # 排除规则
  exclude-rules:
    - path: _test\.go
      linters:
        - gosec        # 测试文件不检查安全
        - errcheck     # 测试文件允许忽略错误
    - path: internal/mocks
      linters:
        - all          # mock 文件不检查
  # 排除内置规则
  exclude-use-default: false

output:
  formats:
    - format: colored-line-number
  sort-results: true
```

**2. 命令行使用**

```bash
# 运行所有启用的 linter
golangci-lint run

# 运行特定 linter
golangci-lint run --enable=gosec,govet

# 自动修复（部分 linter 支持）
golangci-lint run --fix

# 指定配置文件
golangci-lint run --config=.golangci.yml

# 只检查变更的文件（git diff）
golangci-lint run --new-from-rev=HEAD

# 输出 JSON 格式（CI 集成用）
golangci-lint run --out-format=json

# 查看启用的 linter
golangci-lint linters
```

**3. 编辑器集成**

VSCode:
```json
{
  "go.lintTool": "golangci-lint",
  "go.lintFlags": ["--fast"],
  "go.lintOnSave": "workspace"
}
```

Neovim (nvim-lint):
```lua
require('lint').linters_by_ft = {
    go = { 'golangcilint' },
}

-- 自定义 golangci-lint 配置
require('lint').linters.golangcilint.args = {
    'run',
    '--out-format', 'json',
    '--config', '.golangci.yml',
}
```

**4. pre-commit 配置**

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/golangci/golangci-lint
    rev: v1.62.0
    hooks:
      - id: golangci-lint
        args: [--config, .golangci.yml]
```

**5. GitHub Actions CI 配置**

```yaml
# .github/workflows/lint.yml
name: Lint
on: [push, pull_request]

jobs:
  golangci-lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-go@v5
        with:
          go-version: '1.22'
      - name: golangci-lint
        uses: golangci/golangci-lint-action@v6
        with:
          version: v1.62
          args: --config=.golangci.yml
```

#### 总结

- golangci-lint 是 Go lint 聚合器，集成数十个 lint 工具，统一配置和运行。
- `.golangci.yml` 是配置核心，定义启用的 linter、配置项和排除规则。
- 编辑器集成有两种方式：gopls 内置 staticcheck（快但功能少）或直接运行 golangci-lint（全但慢）。
- 常见注意事项：`--fast` 标志只运行快速 linter，适合编辑器实时检查；CI 中使用 `--new-from-rev=HEAD` 只检查变更文件，加速 PR 检查；`max-issues-per-linter: 0` 表示不限制输出数量，避免遗漏问题。

---

## 第 8 章 C/C++

C/C++ 的 LSP 配置是所有语言中最复杂的之一——C/C++ 缺乏统一的构建系统（Make、CMake、Ninja、Bazel 等并存），编译标志多样，头文件搜索路径复杂。`clangd` 是事实上的标准 LSP 服务器，它通过 `compile_commands.json` 获取每个文件的编译标志，从而提供准确的语义分析。本章用 3 讲讲解 clangd 配置、clang-format 配置文件和 compile_commands.json 的生成。

### 第 27 讲 clangd 配置

#### 概念

`clangd` 是 LLVM/Clang 项目提供的 C/C++ LSP 服务器，基于 Clang 编译器前端，提供精确的代码补全、跳转、诊断、重构等功能。clangd 是 VSCode C/C++ 扩展（ms-vscode.cpptools）之外的主流选择，也是 Neovim、Helix 等编辑器的首选 C/C++ LSP。clangd 的核心依赖是 `compile_commands.json`——它告诉 clangd 每个文件应该如何编译（编译器标志、头文件路径、宏定义等）。

#### 原理

**clangd 的工作原理**是利用 Clang 编译器前端进行语义分析。对于每个源文件，clangd 读取 `compile_commands.json` 中对应的编译命令，提取编译器标志（如 `-I` 头文件路径、`-D` 宏定义、`-std=c++20` 标准），然后用这些标志调用 Clang 前端解析文件。这使得 clangd 的分析结果与实际编译结果一致——如果某个头文件路径在编译命令中存在，clangd 就能正确解析其中的声明。

**compile_commands.json** 是 clangd 的关键依赖。这是一个 JSON 数组，每个元素描述一个源文件的编译命令，包含文件路径、编译目录、编译命令（或展开的参数列表）。clangd 在项目根目录或 `build/` 目录中查找此文件。如果没有此文件，clangd 会使用"回退"标志（fallback flags）进行猜测式分析，结果往往不准确。

**索引（Index）**是 clangd 的性能优化机制。clangd 在后台构建整个项目的符号索引，支持快速的全项目符号搜索（`workspace/symbol`）和跨文件跳转。索引数据可以持久化到 `.cache/clangd/index/` 目录，避免每次启动重新构建。对于大型项目，索引可能需要数分钟。

**clangd 配置文件** `.clangd`（YAML 格式）允许项目级配置，覆盖编译标志、索引行为等。这对于处理 `compile_commands.json` 中的不完善配置很有用——例如，某些生成的文件可能不在 compile_commands.json 中，可以通过 `.clangd` 文件为它们提供编译标志。

#### 例子

**1. Neovim 配置**

```lua
-- ~/.config/nvim/lsp/clangd.lua
return {
    cmd = {
        'clangd',
        '--background-index',          -- 后台索引
        '--clang-tidy',                -- 启用 clang-tidy 检查
        '--clang-tidy-checks=*',       -- 启用所有 clang-tidy 检查
        '--cross-file-rename=true',    -- 跨文件重命名
        '--completion-style=detailed', -- 补全风格：detailed | bundled
        '--function-arg-placeholders=true',  -- 函数参数占位符
        '--header-insertion=iwyu',     -- 自动插入头文件
        '--pch-storage=memory',        -- PCH 存储位置：memory | disk
        '--all-scopes-completion',     -- 跨作用域补全
        '--enable-config',             -- 启用 .clangd 配置文件
        '--offset-encoding=utf-16',    -- 偏移编码（兼容 VSCode）
    },
    filetypes = { 'c', 'cpp', 'objc', 'objcpp', 'cuda' },
    root_markers = { 'compile_commands.json', 'compile_flags.txt', '.git' },
    init_options = {
        usePlaceholders = true,
        completeUnimported = true,
        clangdFileStatus = true,
    },
    settings = {},
}
```

**2. VSCode 配置**

```json
{
  "C_Cpp.intelliSenseEngine": "disabled",  // 禁用 cpptools 的 IntelliSense，使用 clangd
  "clangd.path": "/usr/bin/clangd",
  "clangd.arguments": [
    "--background-index",
    "--clang-tidy",
    "--clang-tidy-checks=*",
    "--completion-style=detailed",
    "--function-arg-placeholders=true",
    "--header-insertion=iwyu",
    "--all-scopes-completion",
    "--enable-config"
  ],
  "clangd.checkUpdates": false,
  "clangd.onConfigChanged": "restart",
  "[c]": {
    "editor.defaultFormatter": "llvm-vs-code-extensions.vscode-clangd"
  },
  "[cpp]": {
    "editor.defaultFormatter": "llvm-vs-code-extensions.vscode-clangd"
  }
}
```

**3. .clangd 配置文件（项目根目录）**

```yaml
# .clangd
CompileFlags:
  # 添加编译标志
  Add: [-std=c++20, -Wall, -Wextra, -xc++]
  # 移除编译标志
  Remove: [-Werror]
  # 编译器路径
  Compiler: clang++
  # 回退标志（当 compile_commands.json 中没有对应条目时使用）
  CompilationDatabase: build/

Index:
  # 后台索引
  Background: Build
  # 外部索引（跨项目跳转）
  External:
    - file:///path/to/dependency/.cache/clangd/index/

Diagnostics:
  # 诊断选项
  ClangTidy:
    Add: [modernize-*, performance-*, bugprone-*]
    Remove: [modernize-use-trailing-return-type]
    CheckOptions:
      readability-identifier-naming.VariableCase: lower_case
  UnusedIncludes: Strict
  Suppress: [unused-includes]

InlayHints:
  Enabled: Yes
  ParameterNames: Yes
  DeducedTypes: Yes

Hover:
  ShowAKA: Yes

Completion:
  AllScopes: Yes
```

**4. compile_flags.txt（简单替代方案）**

```
# compile_flags.txt（每行一个编译标志，应用于所有文件）
-std=c++20
-Wall
-Wextra
-Iinclude
-Ithird_party/fmt/include
-Ithird_party/spdlog/include
-DPROJECT_VERSION="1.0.0"
```

#### 总结

- clangd 是基于 Clang 前端的 C/C++ LSP 服务器，依赖 `compile_commands.json` 获取编译标志。
- `.clangd` 配置文件允许项目级配置，覆盖编译标志和 clang-tidy 检查。
- 后台索引（`--background-index`）加速全项目符号搜索，索引数据持久化到 `.cache/clangd/`。
- 常见注意事项：VSCode 中需禁用 cpptools 的 IntelliSense（`C_Cpp.intelliSenseEngine: "disabled"`）以避免与 clangd 冲突；`compile_flags.txt` 是 `compile_commands.json` 的简化替代，适用于简单项目；`--offset-encoding=utf-16` 确保与 VSCode 的字符偏移兼容。

---

### 第 28 讲 clang-format 配置文件详解

#### 概念

`clang-format` 是 LLVM 项目提供的 C/C++ 代码格式化器，是 C/C++ 生态中最主流的格式化工具。与 gofmt 或 Black 不同，clang-format 是高度可配置的——它提供超过 100 个配置项，允许精确控制每个排版细节。配置文件为 `.clang-format`（YAML 格式），支持基于 LLVM、Google、Chromium、Mozilla、WebKit 等预设风格。clang-format 也可用于格式化 Objective-C、Java、JavaScript、Protobuf 等语言。

#### 原理

**clang-format 的工作原理**是解析源代码为 Clang AST，然后按照配置规则重新生成代码文本。与 opinionated 格式器不同，clang-format 的设计理念是"适应现有风格"——它提供了大量配置项，使得任何已有代码风格都能通过适当的配置来匹配。这种灵活性使得 clang-format 能被各种不同风格的大型项目（如 LLVM、Chromium、Google）采用。

**配置文件查找**遵循就近原则：clang-format 从当前文件所在目录开始向上搜索 `.clang-format` 文件，找到的第一个即为生效配置。这使得不同目录可以使用不同的格式化规则——例如，`third_party/` 目录可以有自己的 `.clang-format` 覆盖项目根目录的配置。

**预设风格**是 clang-format 的便捷特性。通过 `BasedOnStyle` 配置项，可以继承一个预设风格（如 `LLVM`、`Google`、`Chromium`、`Mozilla`、`WebKit`、`Microsoft`），然后覆盖特定配置项。`clang-format -style=google -dump-config` 命令可以导出预设风格的完整配置，作为自定义的起点。

**配置项分类**包括：缩进（`IndentWidth`、`TabWidth`、`UseTab`）、行宽（`ColumnLimit`）、大括号风格（`BreakBeforeBraces`）、对齐（`AlignAfterOpenBracket`、`AlignConsecutiveAssignments`）、空格（`SpaceBeforeParens`、`SpacesInParentheses`）、换行（`AllowShortFunctionsOnASingleLine`、`AllowShortIfStatementsOnASingleLine`）、include 排序（`SortIncludes`、`IncludeBlocks`）等。

#### 例子

**1. .clang-format（完整配置示例）**

```yaml
# .clang-format
---
# 基于预设风格
BasedOnStyle: LLVM

# ─── 缩进 ───
IndentWidth: 4
TabWidth: 4
UseTab: Never
ContinuationIndentWidth: 4
AccessModifierOffset: -4    # 访问修饰符（public/private）的额外缩进

# ─── 行宽 ───
ColumnLimit: 100
ReflowComments: true        # 自动换行长注释

# ─── 大括号风格 ───
# 可能值: Attach | Linux | Mozilla | Stroustrup | Allman | GNU | Weber | Custom
BreakBeforeBraces: Custom
BraceWrapping:
  AfterClass: true
  AfterControlStatement: Never
  AfterEnum: true
  AfterFunction: true
  AfterNamespace: true
  AfterStruct: true
  AfterUnion: true
  BeforeCatch: false
  BeforeElse: false
  IndentBraces: false
  SplitEmptyFunction: false
  SplitEmptyRecord: false

# ─── 对齐 ───
AlignAfterOpenBracket: Align
AlignConsecutiveAssignments: Consecutive
AlignConsecutiveDeclarations: None
AlignConsecutiveMacros: Consecutive
AlignEscapedNewlines: Left
AlignOperands: Align
AlignTrailingComments: true

# ─── 空格 ───
SpaceBeforeParens: ControlStatements
SpacesInParentheses: false
SpacesInSquareBrackets: false
SpacesInAngles: false
SpaceAfterCStyleCast: false
SpaceBeforeAssignmentOperators: true
SpaceBeforeColon: false
BitFieldColonSpacing: Both

# ─── 换行 ───
AllowShortBlocksOnASingleLine: Empty
AllowShortCaseLabelsOnASingleLine: false
AllowShortFunctionsOnASingleLine: Inline
AllowShortIfStatementsOnASingleLine: Never
AllowShortLambdasOnASingleLine: Inline
AllowShortLoopsOnASingleLine: false
AlwaysBreakAfterDefinitionReturnType: None
AlwaysBreakAfterReturnType: None
AlwaysBreakBeforeMultilineStrings: false
AlwaysBreakTemplateDeclarations: Yes

# ─── include 排序 ───
SortIncludes: CaseSensitive
IncludeBlocks: Regroup
IncludeCategories:
  - Regex: '^<.*\.h>'         # C 系统头文件
    Priority: 1
  - Regex: '^<.*>'             # C++ 标准库
    Priority: 2
  - Regex: '^".*"'             # 本地头文件
    Priority: 3
  - Regex: '^<Q.*>'            # Qt 头文件
    Priority: 4

# ─── 指针对齐 ───
DerivePointerAlignment: false
PointerAlignment: Left         # int* ptr 而非 int *ptr

# ─── 其他 ───
MaxEmptyLinesToKeep: 2
KeepEmptyLinesAtTheStartOfBlocks: false
FixNamespaceComments: true
Cpp11BracedListStyle: true
Standard: c++20
```

**2. 生成预设风格配置**

```bash
# 导出 Google 风格的完整配置
clang-format -style=google -dump-config > .clang-format

# 导出 LLVM 风格
clang-format -style=llvm -dump-config > .clang-format

# 导出 Mozilla 风格
clang-format -style=mozilla -dump-config > .clang-format
```

**3. 命令行使用**

```bash
# 格式化文件（原地修改）
clang-format -i main.cpp

# 格式化多个文件
clang-format -i src/*.cpp include/*.h

# 检查是否已格式化（输出差异）
clang-format --dry-run --Werror main.cpp

# 从 stdin 读取，输出到 stdout
cat main.cpp | clang-format

# 格式化选区（通过行号）
clang-format -lines=10:20 main.cpp

# 使用指定配置文件
clang-format -style=file main.cpp  # "file" 表示使用 .clang-format
```

**4. 编辑器配置**

VSCode:
```json
{
  "[c]": {
    "editor.defaultFormatter": "llvm-vs-code-extensions.vscode-clangd",
    "editor.formatOnSave": true
  },
  "[cpp]": {
    "editor.defaultFormatter": "llvm-vs-code-extensions.vscode-clangd",
    "editor.formatOnSave": true
  }
}
```

Neovim (conform.nvim):
```lua
require('conform').setup({
    formatters_by_ft = {
        c = { 'clang_format' },
        cpp = { 'clang_format' },
        cuda = { 'clang_format' },
    },
    formatters = {
        clang_format = {
            args = { '-style=file', '--fallback-style=LLVM' },
        },
    },
})
```

**5. .clang-format-ignore（排除文件）**

```
# .clang-format-ignore
third_party/*
generated/*
*.pb.h
*.pb.cc
```

#### 总结

- clang-format 是高度可配置的 C/C++ 格式化器，提供 100+ 配置项。
- `.clang-format`（YAML）是配置文件，通过 `BasedOnStyle` 继承预设风格。
- `IncludeCategories` 配置 include 排序规则，是 C/C++ 项目最常用的功能之一。
- 常见注意事项：`--dry-run --Werror` 是 CI 中检查格式化的标准方式；`PointerAlignment` 是团队风格争论的焦点，需统一；`.clang-format` 的就近原则允许子目录覆盖父目录配置。

---

### 第 29 讲 compile_commands.json 生成与管理

#### 概念

`compile_commands.json` 是 JSON Compilation Database 格式的文件，描述项目中每个源文件的编译命令。它是 clangd（以及许多其他 C/C++ 工具，如 clang-tidy、include-what-you-use）的关键依赖——没有它，clangd 无法知道每个文件的头文件搜索路径、宏定义、编译标准等信息，只能进行猜测式分析。本讲讲解如何使用 CMake、Bear、Make 等工具生成 compile_commands.json。

#### 原理

**JSON Compilation Database 格式**是一个 JSON 数组，每个元素描述一个编译单元（translation unit）。每个元素包含以下字段：`directory`（编译工作目录）、`file`（源文件绝对路径）、`command`（完整的编译命令字符串）或 `arguments`（编译命令参数数组）、`output`（输出文件路径，可选）。clangd 优先使用 `arguments` 数组形式（更安全，避免 shell 转义问题）。

```json
[
  {
    "directory": "/home/user/project/build",
    "file": "/home/user/project/src/main.cpp",
    "arguments": ["/usr/bin/clang++", "-std=c++20", "-I/home/user/project/include", "-c", "src/main.cpp", "-o", "CMakeFiles/main.dir/src/main.cpp.o"],
    "output": "CMakeFiles/main.dir/src/main.cpp.o"
  }
]
```

**CMake 生成**是最常见的方式。CMake 通过 `CMAKE_EXPORT_COMPILE_COMMANDS` 变量控制是否生成 compile_commands.json。设置为 `ON` 后，CMake 在构建目录中生成此文件。注意，CMake 只在生成阶段（`cmake` 命令）创建此文件，不在编译阶段（`make`/`ninja`）更新。如果 `CMakeLists.txt` 修改了编译选项，需要重新运行 `cmake` 以更新 compile_commands.json。

**Bear（Build EAR）** 是一个通用工具，通过拦截编译器的 `exec` 调用来记录编译命令。Bear 适用于任何构建系统（Make、SCons、Bazel 等），用法是 `bear -- <build_command>`。Bear 的工作原理是设置 `LD_PRELOAD` 环境变量，注入一个共享库，拦截子进程的 `execve` 系统调用，记录编译器调用。

**符号链接到项目根目录**是一个常见实践。CMake 生成的 compile_commands.json 位于构建目录（如 `build/`），但 clangd 默认在项目根目录查找。解决方法是在项目根目录创建指向 `build/compile_commands.json` 的符号链接：`ln -sf build/compile_commands.json .`。

#### 例子

**1. CMake 生成 compile_commands.json**

```cmake
# CMakeLists.txt
cmake_minimum_required(VERSION 3.20)
project(myproject CXX)

# 关键：启用 compile_commands.json 生成
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

include_directories(include)
add_executable(main src/main.cpp src/utils.cpp)

# 如果 CMAKE_EXPORT_COMPILE_COMMANDS 不生效，可以手动写入
# （某些 CMake 版本或生成器可能不支持）
```

```bash
# 配置项目（生成 compile_commands.json）
cmake -B build -DCMAKE_EXPORT_COMPILE_COMMANDS=ON

# 或在配置时指定
cmake -B build -S . -DCMAKE_EXPORT_COMPILE_COMMANDS=ON

# 编译
cmake --build build

# 创建符号链接到项目根目录（clangd 默认查找位置）
ln -sf build/compile_commands.json .
```

**2. Bear 生成 compile_commands.json（适用于 Make 等构建系统）**

```bash
# 安装 Bear
# Ubuntu/Debian: sudo apt install bear
# macOS: brew install bear

# 使用 Bear 包装构建命令
bear -- make

# 或指定输出文件
bear --output compile_commands.json -- make -j$(nproc)

# 增量更新（只记录变更的编译命令）
bear --append -- make
```

**3. Makefile 项目手动生成**

```makefile
# Makefile
CXX = g++
CXXFLAGS = -std=c++20 -Wall -Iinclude

SRCS = $(wildcard src/*.cpp)
OBJS = $(SRCS:.cpp=.o)

main: $(OBJS)
	$(CXX) $(OBJS) -o main

%.o: %.cpp
	$(CXX) $(CXXFLAGS) -c $< -o $@

# 生成 compile_commands.json 的目标
compile_commands.json: $(SRCS)
	@echo '[' > $@
	@for src in $(SRCS); do \
		if [ $$src != $(firstword $(SRCS)) ]; then echo ',' >> $@; fi; \
		echo '  {' >> $@; \
		echo '    "directory": "$(CURDIR)",' >> $@; \
		echo "    \"file\": \"$(CURDIR)/$$src\"," >> $@; \
		echo "    \"command\": \"$(CXX) $(CXXFLAGS) -c $$src -o $${src%.cpp}.o\"," >> $@; \
		echo "    \"output\": \"$${src%.cpp}.o\"" >> $@; \
		echo '  }' >> $@; \
	done
	@echo ']' >> $@

.PHONY: compdb
compdb: compile_commands.json
```

**4. Bazel 生成（通过 bazel-compile-commands-extractor）**

```bash
# 安装 bazel-compile-commands-extractor
go install github.com/hedronvision/bazel-compile-commands-extractor@latest

# 在 Bazel 项目中运行
cd /path/to/bazel/project
bazel-compile-commands-extractor

# 生成 compile_commands.json 到项目根目录
```

**5. clangd 配置指定 compile_commands.json 位置**

```yaml
# .clangd
CompileFlags:
  CompilationDatabase: build/  # 指定 compile_commands.json 所在目录
```

```lua
-- Neovim: 通过 root_markers 指定查找位置
vim.lsp.config('clangd', {
    root_markers = { 'compile_commands.json', 'compile_flags.txt', '.git' },
    cmd = {
        'clangd',
        '--compile-commands-dir=build',  -- 指定 compile_commands.json 目录
    },
})
```

**6. 验证 compile_commands.json**

```bash
# 检查文件是否有效 JSON
python3 -c "import json; json.load(open('compile_commands.json'))"

# 查看特定文件的编译命令
python3 -c "
import json
db = json.load(open('compile_commands.json'))
for entry in db:
    if 'main.cpp' in entry['file']:
        print(entry['command'])
"
```

#### 总结

- compile_commands.json 是 clangd 的关键依赖，描述每个源文件的编译命令。
- CMake 通过 `CMAKE_EXPORT_COMPILE_COMMANDS=ON` 生成，是最常见的方式。
- Bear 适用于任何构建系统，通过拦截编译器调用记录编译命令。
- 常见注意事项：compile_commands.json 需要在编译选项变更后重新生成；符号链接到项目根目录是让 clangd 找到它的标准做法；CMake 的 Ninja 生成器支持 compile_commands.json，但 Make 生成器在旧版本可能不支持。


---

## 第 9 章 其他主流语言

本章覆盖 Java、Ruby、Lua、Shell 四种语言的 LSP 与格式化配置。这些语言的工具链成熟度不一——Java 的 jdtls 功能强大但配置复杂，Ruby 的 Solargraph + RuboCop 组合经典，Lua 的 lua-language-server 是 Neovim 配置开发的核心工具，Shell 的 bash-language-server + shfmt 是脚本开发的标配。每讲聚焦一种语言的完整工具链配置。

### 第 30 讲 Java：jdtls 与 Google Java Format

#### 概念

`jdtls`（Eclipse JDT Language Server）是基于 Eclipse JDT 的 Java LSP 服务器，是 Java 生态功能最全面的 LSP 服务器。它提供代码补全、重构、调试支持、Maven/Gradle 集成等全套 Java 开发功能。`Google Java Format` 是 Google 开源的 Java 代码格式化器，强制 Google Java Style 风格，几乎不可配置。`Spotless` 是 Java 生态的格式化聚合插件，支持 Google Java Format、Palantir Java Format、clang-format 等多种格式化器，通过 Maven/Gradle 插件集成。

#### 原理

**jdtls 的架构**是将 Eclipse IDE 的 JDT 编译器包装为 LSP 服务器。jdtls 内部运行一个无头（headless）的 Eclipse 实例，利用 JDT 的 Java 编译器进行语义分析。这使得 jdtls 的分析能力与 Eclipse IDE 完全一致——包括完整的类型推断、泛型支持、注解处理等。jdtls 的启动较慢（需要初始化 Eclipse 工作区），但运行时性能良好。

**Maven/Gradle 集成**是 jdtls 的核心特性。jdtls 读取 `pom.xml`（Maven）或 `build.gradle`（Gradle），解析依赖关系，下载依赖 JAR 包，构建类路径。这使得 jdtls 能正确解析第三方库的 API。当 `pom.xml` 或 `build.gradle` 变更时，jdtls 自动重新导入项目。

**jdtls 的配置**较为复杂，因为它本质上是 Eclipse 的配置。配置通过 `initializationOptions` 传递，包含 `settings`（Java 编译器设置、代码风格设置等）和 `bundles`（额外插件 JAR）。jdtls 还需要指定 `workspace` 目录（用于存储 Eclipse 工作区数据，每个项目应使用独立的 workspace 目录）。

**Google Java Format** 的设计理念与 gofmt 类似——零配置，强制统一风格。它只暴露一个配置项 `--aosp`（使用 AOSP 风格，4 空格缩进替代 2 空格）。Google Java Format 的格式化规则包括：2 空格缩进、行宽 100、特定的大括号风格、import 排序等。它通过 Maven/Gradle 插件或命令行工具运行。

#### 例子

**1. jdtls 配置（Neovim）**

```lua
-- ~/.config/nvim/lsp/jdtls.lua
-- jdtls 需要特殊配置，通常使用 nvim-jdtls 插件
local config = {
    cmd = {
        'jdtls',
        '-data', vim.fn.stdpath('cache') .. '/jdtls-workspace/' .. vim.fn.fnamemodify(vim.fn.getcwd(), ':t'),
    },
    root_markers = { 'pom.xml', 'build.gradle', 'build.gradle.kts', '.git' },
    settings = {
        java = {
            -- Java 版本
            configuration = {
                runtimes = {
                    { name = 'JavaSE-21', path = '/usr/lib/jvm/java-21-openjdk' },
                },
            },
            -- Maven 设置
            maven = {
                downloadSources = true,
            },
            -- Gradle 设置
            gradle = {
                enabled = true,
                wrapper = { enabled = true },
            },
            -- 格式化设置
            format = {
                enabled = true,
                settings = {
                    url = 'https://raw.githubusercontent.com/google/styleguide/gh-pages/eclipse-java-google-style.xml',
                    profile = 'GoogleStyle',
                },
            },
            -- 补全
            completion = {
                enabled = true,
                postfix = { enabled = true },
                guessMethodArguments = 'insertBestGuessedArguments',
            },
            -- 保存时操作
            saveActions = {
                organizeImports = true,
            },
            -- 签名帮助
            signatureHelp = { enabled = true },
            -- 内容支持
            contentProvider = { preferred = 'fernflower' },
            -- 诊断
            diagnostics = {
                enable = true,
            },
            -- inlay hints
            inlayHints = {
                parameterNames = { enabled = 'literals' },
            },
        },
    },
    init_options = {
        bundles = {},  -- 额外 JAR 插件
        extendedClientCapabilities = {
            progressReportProvider = true,
            classFileContentsSupport = true,
            shouldLanguageServerExitOnShutdown = true,
        },
    },
}
```

**2. VSCode 配置**

```json
{
  "java.format.enabled": true,
  "java.format.settings.url": "https://raw.githubusercontent.com/google/styleguide/gh-pages/eclipse-java-google-style.xml",
  "java.format.settings.profile": "GoogleStyle",
  "java.saveActions.organizeImports": true,
  "java.completion.enabled": true,
  "java.signatureHelp.enabled": true,
  "java.configuration.runtimes": [
    { "name": "JavaSE-21", "path": "/usr/lib/jvm/java-21-openjdk", "default": true }
  ],
  "[java]": {
    "editor.defaultFormatter": "redhat.java",
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
      "source.organizeImports": "explicit"
    }
  }
}
```

**3. Google Java Format 命令行使用**

```bash
# 安装
# 下载 google-java-format jar
wget https://github.com/google/google-java-format/releases/download/v1.22.0/google-java-format-1.22.0-all-deps.jar -O ~/google-java-format.jar

# 格式化文件（原地修改）
java -jar ~/google-java-format.jar -i src/Main.java

# 格式化多个文件
java -jar ~/google-java-format.jar -i src/**/*.java

# 检查是否已格式化（不修改）
java -jar ~/google-java-format.jar --dry-run src/Main.java

# 使用 AOSP 风格（4 空格缩进）
java -jar ~/google-java-format.jar -i --aosp src/Main.java
```

**4. Spotless 配置（Gradle）**

```groovy
// build.gradle
plugins {
    id 'com.diffplug.spotless' version '6.25.0'
}

spotless {
    java {
        // 使用 Google Java Format
        googleJavaFormat('1.22.0')
        // 或使用 Palantir Java Format
        // palantirJavaFormat('2.50.0')

        // 移除未使用的 import
        removeUnusedImports()
        // import 排序
        importOrder('java', 'javax', 'org', 'com', '')
        // 自定义替换
        replaceRegex('Remove trailing whitespace', '\\s+$', '')
    }
}

// 格式化任务
// ./gradlew spotlessApply  -- 格式化
// ./gradlew spotlessCheck  -- 检查
```

**5. Spotless 配置（Maven）**

```xml
<!-- pom.xml -->
<plugin>
    <groupId>com.diffplug.spotless</groupId>
    <artifactId>spotless-maven-plugin</artifactId>
    <version>2.43.0</version>
    <configuration>
        <java>
            <googleJavaFormat>
                <version>1.22.0</version>
                <style>GOOGLE</style>
            </googleJavaFormat>
            <removeUnusedImports/>
            <importOrder>
                <order>java,javax,org,com,</order>
            </importOrder>
        </java>
    </configuration>
    <executions>
        <execution>
            <goals>
                <goal>check</goal>
            </goals>
            <phase>verify</phase>
        </execution>
    </executions>
</plugin>
```

#### 总结

- jdtls 是基于 Eclipse JDT 的 Java LSP 服务器，深度集成 Maven/Gradle。
- Google Java Format 是零配置的 Java 格式化器，强制 Google Java Style。
- Spotless 是格式化聚合插件，通过 Maven/Gradle 集成，支持多种格式化器。
- 常见注意事项：jdtls 需要为每个项目指定独立的 workspace 目录（`-data` 参数）；jdtls 启动较慢，首次打开项目需要等待工作区初始化；Google Java Format 的 `--aosp` 选项切换为 4 空格缩进，适用于 Android 项目。

---

### 第 31 讲 Ruby：Solargraph 与 RuboCop

#### 概念

`Solargraph` 是 Ruby 的 LSP 服务器，提供代码补全、跳转定义、文档查询、诊断等功能。Solargraph 通过解析 Ruby 代码和 gem 依赖构建类型信息，支持 Rails 项目的部分特性。`RuboCop` 是 Ruby 生态最流行的 lint 和格式化工具，基于 Ruby Style Guide（社区风格指南），提供数百条 lint 规则和自动格式化功能。`Standard`（Standard Ruby）是基于 RuboCop 的零配置方案，强制统一风格。

#### 原理

**Solargraph 的工作原理**是静态分析 Ruby 源代码，构建符号表和类型信息。Ruby 是动态类型语言，Solargraph 通过解析方法定义、YARD 文档注释、显式类型注解（Sorbet/RBI 文件）来推断类型。Solargraph 的类型推断不如静态类型语言的 LSP 精确，但对于方法补全、跳转定义等基本功能足够。Solargraph 还集成了 RuboCop，可以将 RuboCop 的诊断通过 LSP 的 `textDocument/publishDiagnostics` 推送给编辑器。

**RuboCop 的架构**是一个基于 AST 的 lint 和格式化引擎。RuboCop 使用 Parser gem 解析 Ruby 代码为 AST，然后应用各 Cop（规则）进行检查。Cop 分为多个部门（Department）：`Style`（风格）、`Lint`（错误检测）、`Metrics`（代码度量）、`Layout`（排版格式化）、`Security`（安全检查）、`Rails`（Rails 特定规则）、`Performance`（性能优化）等。`Layout` 部门的 Cop 负责格式化，可以通过 `--auto-correct`（或 `--fix`）自动修复。

**配置文件** `.rubocop.yml` 是 RuboCop 的核心配置。配置文件定义启用的 Cop、各 Cop 的参数、排除文件等。RuboCop 支持继承配置（`inherit_from`），可以从 gem（如 `rubocop-rails`、`rubocop-rspec`）或 URL 继承规则。

**Standard Ruby** 的设计理念是"零配置"——它预定义了一套严格的风格规则，不允许覆盖（通过 `--rubocop` 选项可以查看底层 RuboCop 配置）。Standard 适合不想花时间争论风格的团队，通过 `rubocop --require=standard --config=.standard.yml` 运行。

#### 例子

**1. Solargraph 配置（Neovim）**

```lua
-- ~/.config/nvim/lsp/solargraph.lua
return {
    cmd = { 'solargraph', 'stdio' },
    filetypes = { 'ruby' },
    root_markers = { 'Gemfile', '.git' },
    settings = {
        solargraph = {
            diagnostics = true,          -- 启用诊断（集成 RuboCop）
            formatting = false,          -- 格式化交给 RuboCop 独立处理
            completion = true,
            definitions = true,
            references = true,
            symbols = true,
            rename = true,
            useBundler = true,           -- 使用 bundler 环境
            bundlePath = 'bundle',       -- bundler 命令路径
            commandPath = '',            -- solargraph 命令路径（空则使用 PATH 中的）
            logLevel = 'warn',
        },
    },
}
```

**2. VSCode 配置**

```json
{
  "solargraph.diagnostics": true,
  "solargraph.formatting": false,
  "solargraph.useBundler": true,
  "[ruby]": {
    "editor.defaultFormatter": "misogi.ruby-rubocop",
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
      "source.fixAll.rubocop": "explicit"
    }
  }
}
```

**3. .rubocop.yml（完整配置）**

```yaml
# .rubocop.yml
# 继承默认配置
inherit_from: [.rubocop_todo.yml]

# 启用插件（RuboCop 1.0+ 使用 plugins）
plugins:
  - rubocop-rails
  - rubocop-rspec
  - rubocop-performance

# 全局配置
AllCops:
  NewCops: enable          # 自动启用新 Cop
  TargetRubyVersion: 3.3   # 目标 Ruby 版本
  TargetRailsVersion: 7.1  # 目标 Rails 版本
  Exclude:
    - 'db/**/*'
    - 'config/**/*'
    - 'script/**/*'
    - 'bin/{bundle,rails,rake,setup,update}'
    - 'node_modules/**/*'
    - 'vendor/**/*'

# ─── Layout（格式化）───
Layout/LineLength:
  Max: 120
  AllowedPatterns: ['\A\s*#']  # 允许长注释

Layout/IndentationWidth:
  Width: 2

Layout/AccessModifierIndentation:
  EnforcedStyle: indent

Layout/EndOfLine:
  EnforcedStyle: lf

# ─── Style ───
Style/Documentation:
  Enabled: false             # 不强制文档注释

Style/FrozenStringLiteralComment:
  Enabled: true
  EnforcedStyle: always

Style/StringLiterals:
  EnforcedStyle: single_quotes  # 单引号

Style/ClassAndModuleChildren:
  EnforcedStyle: nested         # module Foo; class Bar 而非 class Foo::Bar

# ─── Metrics ───
Metrics/MethodLength:
  Max: 20

Metrics/BlockLength:
  Exclude:
    - 'spec/**/*'              # 测试文件允许长块
    - 'config/routes.rb'

Metrics/AbcSize:
  Max: 20

# ─── Rails ───
Rails/FilePath:
  EnforcedStyle: arguments

Rails/I18nLocaleTexts:
  Enabled: false
```

**4. RuboCop 命令行使用**

```bash
# 安装
gem install rubocop
gem install rubocop-rails rubocop-rspec rubocop-performance

# 检查
rubocop

# 自动修复（安全修复）
rubocop -a

# 自动修复（所有修复，包括不安全的）
rubocop -A

# 只检查格式化（Layout Cop）
rubocop --only Layout

# 只检查特定文件
rubocop lib/main.rb

# 生成 .rubocop_todo.yml（禁用现有违规）
rubocop --auto-gen-config

# 使用特定配置文件
rubocop --config .rubocop.yml

# 输出 JSON 格式
rubocop --format json
```

**5. Standard Ruby 配置**

```yaml
# .standard.yml
# Standard Ruby 配置（极简）
fix: true        # 默认自动修复

# 排除文件
ignore:
  - 'db/**/*'
  - 'bin/**/*'
  - 'vendor/**/*'

# 插件
plugins:
  - standard-rails
  - standard-performance
```

```bash
# 使用 Standard
gem install standard

# 检查
standardrb

# 自动修复
standardrb --fix

# 查看底层 RuboCop 配置
standardrb --rubocop
```

**6. pre-commit 配置**

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/rubocop/rubocop
    rev: v1.66.0
    hooks:
      - id: rubocop
        args: [--fix]  # 或 -a

  # 或使用 Standard
  - repo: https://github.com/standardrb/standard
    rev: v1.40.0
    hooks:
      - id: standardrb
```

#### 总结

- Solargraph 是 Ruby 的 LSP 服务器，提供补全、跳转等功能，可集成 RuboCop 诊断。
- RuboCop 是 Ruby 生态最流行的 lint + 格式化工具，`.rubocop.yml` 是配置核心。
- Standard Ruby 是基于 RuboCop 的零配置方案，适合不想争论风格的团队。
- 常见注意事项：`rubocop --auto-gen-config` 生成的 `.rubocop_todo.yml` 是渐进式修复技术债的利器；Solargraph 的类型推断不如静态类型语言精确，对于复杂元编程可能失效；`useBundler: true` 确保 Solargraph 使用项目的 gem 环境。

---

### 第 32 讲 Lua：lua-language-server 与 StyLua

#### 概念

`lua-language-server`（又称 lua-lsp）是 sumneko 开发的 Lua LSP 服务器，用 Lua 编写，是 Neovim 插件开发、LÖVE 游戏开发等 Lua 场景的标准 LSP。它提供强大的类型推断、跨文件分析、 EmmyLua 注解支持等功能。`StyLua` 是 Lua 的代码格式化器，用 Rust 编写，性能优异，支持 Lua 5.1/5.2/5.3/5.4 和 Luau。`lua-language-server` 本身也内置了基于 `lua-format` 的格式化能力，但 StyLua 更流行。

#### 原理

**lua-language-server 的工作原理**是解析 Lua 源代码为 AST，结合 EmmyLua 注解和跨文件分析构建类型信息。Lua 是动态类型语言，lua-language-server 通过多种方式推断类型：变量赋值推断（`local x = 1` 推断 x 为 integer）、函数返回值推断、EmmyLua 注解（`---@type`、`---@param`、`---@return`）、class 定义（`---@class`）。对于 Neovim API，lua-language-server 通过 `vim` 全局变量的类型定义文件（`meta/vim.lua`）提供精确的补全和类型检查。

**工作区（Workspace）**概念是 lua-language-server 的核心。lua-language-server 将项目根目录作为工作区，索引所有 `.lua` 文件，构建全局符号表。这使得跨文件跳转和补全成为可能。工作区配置通过 `.luarc.json` 或 `.luarc.jsonc` 文件管理。

**EmmyLua 注解**是 lua-language-server 的类型系统核心。通过注释形式的注解，开发者为动态类型的 Lua 代码添加静态类型信息：

```lua
---@class Player        -- 定义类
---@field name string   -- 字段
---@field hp number

---@param player Player -- 参数类型
---@return boolean      -- 返回值类型
local function is_alive(player)
    return player.hp > 0
end
```

**StyLua 的工作原理**是解析 Lua 代码为 AST，然后按照固定规则重新生成。StyLua 是 opinionated 格式器，配置项有限但覆盖常见需求：缩进宽度、行宽、引号风格、调用括号等。StyLua 用 Rust 编写，格式化速度极快。

#### 例子

**1. lua-language-server 配置（Neovim）**

```lua
-- ~/.config/nvim/lsp/lua_ls.lua
return {
    cmd = { 'lua-language-server' },
    filetypes = { 'lua' },
    root_markers = { '.luarc.json', '.luarc.jsonc', '.luacheckrc', '.stylua.toml', 'selene.toml', '.git' },
    settings = {
        Lua = {
            -- 运行时配置
            runtime = {
                version = 'LuaJIT',     -- Neovim 使用 LuaJIT
                pathStrict = true,
                path = { 'lua/?.lua', 'lua/?/init.lua' },
            },
            -- 工作区配置
            workspace = {
                checkThirdParty = false,
                library = vim.api.nvim_get_runtime_file('', true),  -- Neovim runtime
                -- 或指定特定库
                -- library = {
                --     '/usr/share/nvim/runtime/lua',
                --     '${3rd}/luv/library',  -- luv 库
                -- },
            },
            -- 诊断
            diagnostics = {
                enable = true,
                globals = { 'vim', 'describe', 'it', 'before_each', 'after_each' },
                disable = { 'lowercase-global', 'undefined-global' },
                groupSeverity = {
                    strong = 'Warning',
                    close = 'Warning',
                },
                groupFileStatus = {
                    ['ambiguity'] = 'Opened',
                    ['await'] = 'Opened',
                    ['codestyle'] = 'None',
                    ['duplicate'] = 'Opened',
                    ['global'] = 'Opened',
                    ['luadoc'] = 'Opened',
                    ['redefined'] = 'Opened',
                    ['strict'] = 'Opened',
                    ['strong'] = 'Opened',
                    ['type-check'] = 'Opened',
                    ['unbalanced'] = 'Opened',
                    ['unused'] = 'Opened',
                },
                unusedLocalExclude = { '_*' },  -- 忽略以 _ 开头的未使用变量
            },
            -- 补全
            completion = {
                enable = true,
                callSnippet = 'Replace',  -- 'Disable' | 'Both' | 'Replace'
                displayContext = 10,      -- 显示上下文行数
                keywordSnippet = 'Replace',
                postfix = '@',
            },
            -- 格式化（内置，推荐使用 StyLua 替代）
            format = {
                enable = false,  -- 禁用内置格式化，使用 StyLua
            },
            -- 语义高亮
            semantic = {
                enable = true,
                annotation = true,
                keyword = true,
                variable = true,
            },
            -- 签名帮助
            signatureHelp = {
                enable = true,
            },
            -- 智能提示
            hint = {
                enable = true,
                arrayIndex = 'Auto',     -- 'Enable' | 'Auto' | 'Disable'
                await = true,
                paramName = 'All',       -- 'All' | 'Literal' | 'Disable'
                paramType = true,
                semicolon = 'SameLine',  -- 'All' | 'SameLine' | 'Disable'
                setType = false,
            },
            -- 类型推断严格度
            type = {
                castNumberToInteger = true,
                checkTableShape = true,
                inferTableSize = 100,
                weakNilCheck = false,
                weakUnionCheck = false,
            },
        },
    },
}
```

**2. .luarc.json（项目级配置文件）**

```json
{
  "$schema": "https://raw.githubusercontent.com/LuaLS/vscode-lua/master/setting/schema.json",
  "runtime.version": "LuaJIT",
  "runtime.path": ["lua/?.lua", "lua/?/init.lua"],
  "workspace.library": [
    "${3rd}/luv/library"
  ],
  "workspace.checkThirdParty": false,
  "diagnostics.globals": ["vim"],
  "diagnostics.disable": ["lowercase-global"],
  "format.enable": false,
  "hint.enable": true
}
```

**3. StyLua 配置**

```toml
# .stylua.toml 或 stylua.toml

# 缩进
column_width = 100        # 行宽
indent_type = "Spaces"    # "Spaces" | "Tabs"
indent_width = 4          # 缩进宽度

# 引号
quote_style = "AutoPreferSingle"  # "AutoPreferDouble" | "AutoPreferSingle" | "ForceDouble" | "ForceSingle"

# 调用括号
call_parentheses = "Always"  # "Always" | "NoSingleString" | "NoTable" | "None"

# 空行
collapse_simple_statement = "Never"  # "Never" | "FunctionOnly" | "ConditionalOnly" | "Always"

# 排序
sort_requires = { enabled = false }

# 忽略
[sort_requires]
enabled = false
```

**4. StyLua 命令行使用**

```bash
# 安装
# cargo install stylua
# 或下载预编译二进制: https://github.com/JohnnyMorganz/StyLua/releases

# 格式化文件（原地修改）
stylua lua/

# 格式化单个文件
stylua init.lua

# 检查是否已格式化（不修改）
stylua --check lua/

# 从 stdin 读取
cat init.lua | stylua -

# 使用指定配置文件
stylua --config-path .stylua.toml lua/

# 格式化范围（行号）
stylua --range-start 10 --range-end 20 init.lua
```

**5. 编辑器配置**

VSCode:
```json
{
  "lua-language-server.format.enable": false,
  "[lua]": {
    "editor.defaultFormatter": "JohnnyMorganz.stylua",
    "editor.formatOnSave": true
  }
}
```

Neovim (conform.nvim):
```lua
require('conform').setup({
    formatters_by_ft = {
        lua = { 'stylua' },
    },
    formatters = {
        stylua = {
            args = { '--stdin-filepath', '$FILENAME', '-' },
        },
    },
})
```

**6. EmmyLua 注解示例**

```lua
---定义一个类
---@class Window
---@field bufnr integer  缓冲区号
---@field winnr integer  窗口号
---@field width integer  宽度
local Window = {}
Window.__index = Window

---创建新窗口
---@param width integer 窗口宽度
---@param height integer 窗口高度
---@return Window
function Window.new(width, height)
    local self = setmetatable({}, Window)
    self.width = width
    self.bufnr = vim.api.nvim_create_buf(false, true)
    self.winnr = vim.api.nvim_open_win(self.bufnr, true, {
        width = width,
        height = height,
        relative = 'editor',
        row = 10,
        col = 10,
    })
    return self
end

---关闭窗口
---@param self Window
function Window:close()
    vim.api.nvim_win_close(self.winnr, true)
end

return Window
```

#### 总结

- lua-language-server 是功能强大的 Lua LSP，支持 EmmyLua 注解类型系统。
- `.luarc.json` 是项目级配置文件，配置运行时版本、工作区库、诊断等。
- StyLua 是 Rust 编写的 Lua 格式化器，opinionated 风格，配置简洁。
- 常见注意事项：Neovim 配置开发时，`workspace.library` 应包含 Neovim runtime 路径以获得 `vim` API 补全；EmmyLua 注解是 Lua 静态类型检查的关键，建议为公共 API 添加注解；`runtime.version` 应与实际 Lua 版本匹配（Neovim 使用 LuaJIT）。

---

### 第 33 讲 Shell：bash-language-server 与 shfmt

#### 概念

`bash-language-server`（bash-ls）是 Shell 脚本的 LSP 服务器，支持 Bash 语法，提供代码补全、跳转、诊断、文档查询等功能。它基于 Tree-sitter 解析 Shell 脚本，集成 shellcheck 进行静态分析。`shfmt` 是 mvdan 开发的 Shell 脚本格式化器，用 Go 编写，支持 POSIX Shell、Bash、mksh 等多种 Shell 方言，是 Shell 脚本格式化的事实标准。`shellcheck` 是 Shell 脚本的静态分析工具，检测常见错误和不良实践。

#### 原理

**bash-language-server 的工作原理**是使用 Tree-sitter 解析 Shell 脚本为语法树，然后基于语法树提供 LSP 功能。bash-ls 的补全功能包括：内置命令补全、变量补全、文件路径补全、函数/别名补全。bash-ls 集成 shellcheck 进行诊断——shellcheck 是一个独立的 Shell 静态分析工具，bash-ls 通过运行 shellcheck 并解析其输出来提供诊断信息。

**shfmt 的工作原理**是解析 Shell 脚本为 AST，然后按照配置规则重新生成。shfmt 支持 POSIX Shell、Bash、mksh 等多种 Shell 方言（通过 `-ln` 参数指定），格式化规则包括缩进（空格或 tab）、行宽换行、运算符空格、case 分支缩进等。shfmt 的设计理念是提供合理的默认值同时允许覆盖，是介于 opinionated 和 configurable 之间的格式化器。

**shellcheck 的工作原理**是分析 Shell 脚本的语法和语义，检测常见错误模式。shellcheck 的规则涵盖：未引用的变量（`$var` 应为 `"$var"`）、不安全的命令（`rm $file` 可能误删）、条件判断错误（`[ $x == $y ]` 应为 `[[ ]]` 或加引号）、命令注入风险（`eval $input`）等。shellcheck 的规则通过 SC 编号标识（如 SC2086 表示"未引用的变量"），可以通过注释 `# shellcheck disable=SC2086` 局部禁用。

**配置文件**：bash-language-server 的配置通过 LSP `settings` 传递（主要是 shellcheck 集成配置）；shfmt 通过命令行参数配置（无配置文件，但可以包装在脚本中）；shellcheck 通过 `.shellcheckrc` 文件配置。

#### 例子

**1. bash-language-server 配置（Neovim）**

```lua
-- ~/.config/nvim/lsp/bashls.lua
return {
    cmd = { 'bash-language-server', 'start' },
    filetypes = { 'bash', 'sh' },
    root_markers = { '.git', '.bashrc' },
    settings = {
        bashIde = {
            -- shellcheck 集成
            shellcheckPath = 'shellcheck',  -- shellcheck 可执行文件路径
            shellcheckArguments = {},        -- 额外 shellcheck 参数
            -- 解析器配置
            explainshellEndpoint = '',       -- explainshell.com API 端点（可选）
            -- glob 模式
            globPattern = '**/*@(.sh|.inc|.bash|.command)',
        },
    },
}
```

**2. VSCode 配置**

```json
{
  "bashIde.shellcheckPath": "shellcheck",
  "bashIde.shellcheckArguments": [],
  "[shellscript]": {
    "editor.defaultFormatter": "mkhl.shfmt",
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
      "source.fixAll.shellcheck": "explicit"
    }
  }
}
```

**3. shfmt 使用**

```bash
# 安装
# Go: go install mvdan.cc/sh/v3/cmd/shfmt@latest
# 或下载: https://github.com/mvdan/sh/releases

# 格式化文件（原地修改）
shfmt -i 4 -w script.sh

# 常用参数:
# -i N   缩进宽度（0 表示使用 tab）
# -bn    & 语句换行
# -ci    switch-case 缩进
# -sr    重定向运算符后加空格
# -s     简化（移除冗余）
# -ln    Shell 方言: posix | bash | mksh | bats
# -w     原地写入文件
# -d     显示差异（不修改）
# -l     列出需要格式化的文件

# 格式化为 Bash，4 空格缩进，case 缩进
shfmt -ln bash -i 4 -ci -w script.sh

# 检查是否已格式化
shfmt -ln bash -i 4 -d script.sh

# 格式化整个目录
shfmt -ln bash -i 4 -w scripts/

# 简化代码（移除冗余语法）
shfmt -ln bash -i 4 -s -w script.sh
```

**4. .editorconfig（shfmt 会读取）**

```ini
# .editorconfig
[*.sh]
indent_style = space
indent_size = 4
shell_variant = bash        # posix | bash | mksh | bats
binary_next_line = true     # & 和 | 换行
switch_case_indent = true   # case 缩进
space_redirects = true      # 重定向加空格
keep_padding = false        # 保持对齐
function_next_line = false  # 函数定义换行
```

```bash
# shfmt 会自动读取 .editorconfig，无需额外参数
shfmt -w script.sh
```

**5. .shellcheckrc（shellcheck 配置）**

```ini
# .shellcheckrc
# 禁用的规则
disable=SC1090,SC1091,SC2086

# 启用的可选检查
enable=require-variable-braces,quote-safe-arr

# 排除的文件
exclude=*.bak,*.tmp

# 外部来源（source 命令包含的文件）
external-sources=true

# 检查的 Shell 方言
shell=bash
```

**6. 代码中的 shellcheck 指令**

```bash
#!/bin/bash

# 全文件禁用
# shellcheck disable=SC2086

# 禁用特定规则并说明原因
# shellcheck disable=SC2086  # 故意不引用，需要分词
for file in $FILES; do
    echo "Processing $file"
done

# 禁用下一行
# shellcheck disable=SC2034
UNUSED_VAR="this is intentionally unused"

# 启用特定规则
# shellcheck enable=require-variable-braces
echo "${HOME}"
```

**7. pre-commit 配置**

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/scop/pre-commit-shfmt
    rev: v3.9.0
    hooks:
      - id: shfmt
        args: [-i, '4', -ci, -ln, bash]

  - repo: https://github.com/koalaman/shellcheck-precommit
    rev: v0.10.0
    hooks:
      - id: shellcheck
        args: [--external-sources]
```

#### 总结

- bash-language-server 是 Shell 脚本的 LSP 服务器，集成 shellcheck 提供诊断。
- shfmt 是 Shell 格式化器，支持多种 Shell 方言，可通过 `.editorconfig` 配置。
- shellcheck 是 Shell 静态分析工具，`.shellcheckrc` 是配置文件。
- 常见注意事项：shfmt 的 `-ln` 参数指定 Shell 方言，Bash 脚本应使用 `-ln bash`；shellcheck 的 `# shellcheck disable=SC编号` 注释是局部禁用规则的标准方式；`.editorconfig` 中的 `shell_variant` 等配置会被 shfmt 自动读取，是跨编辑器统一配置的推荐方式。

---

## 第 10 章 高级与实战

本章覆盖跨语言、跨工具的高级配置主题。第 34 讲讲解 EditorConfig 如何在多种格式化工具间统一基础排版规则；第 35 讲讲解 pre-commit 框架如何将格式化和 lint 检查集成到 Git 钩子中；第 36 讲讲解 CI/CD 中如何自动化执行格式化和 lint 检查，确保代码库质量。这三讲是前面所有语言配置的"胶水层"，将分散的工具整合为统一的开发工作流。

### 第 34 讲 EditorConfig 跨工具协同

#### 概念

`EditorConfig` 是一个跨编辑器、跨语言的配置文件标准，用于维护一致的编码风格。`.editorconfig` 文件定义缩进风格、行尾、字符编码、文件末尾空行等基础排版属性。EditorConfig 被几乎所有主流编辑器原生支持或通过插件支持（VSCode、JetBrains、Vim/Neovim、Emacs、Sublime Text 等），也被许多格式化工具（如 shfmt、clang-format 部分支持）读取。EditorConfig 解决的是"不同编辑器默认设置不同"的问题——例如，VSCode 默认 4 空格缩进，而 Vim 默认 tab 缩进，EditorConfig 统一这些差异。

#### 原理

**EditorConfig 的工作原理**是编辑器在打开文件时，从文件所在目录开始向上搜索 `.editorconfig` 文件，找到的配置应用于当前文件。搜索在到达根目录或遇到 `root = true` 的 `.editorconfig` 文件时停止。这种"就近原则"与 `.clang-format`、`.prettierrc` 等配置文件的查找逻辑一致，允许子目录覆盖父目录的配置。

**配置文件格式**是 INI 风格的，使用 glob 模式匹配文件路径。每个 section 以 `[glob模式]` 开头，后跟键值对配置。glob 模式支持 `*`（匹配任意字符，不含 `/`）、`**`（匹配任意路径）、`?`（匹配单个字符）、`[chars]`（字符集）、`{s1,s2}`（枚举）等通配符。特殊的 `[!*]` 模式表示否定匹配。

**支持的属性**包括：`indent_style`（`space` 或 `tab`）、`indent_size`（缩进宽度，数字或 `tab`）、`tab_width`（tab 显示宽度，默认等于 `indent_size`）、`end_of_line`（`lf`、`crlf`、`cr`）、`charset`（`utf-8`、`latin1` 等）、`insert_final_newline`（文件末尾插入空行）、`trim_trailing_whitespace`（移除行尾空格）、`max_line_length`（行宽限制）。这些属性是"基础排版"层面的，不涉及语言特定的格式化规则。

**与格式化工具的关系**：EditorConfig 定义的是编辑器的基础行为（如"按 Tab 插入 4 个空格"），而格式化工具（如 Prettier、shfmt、clang-format）定义的是代码的完整格式化规则。许多格式化工具会读取 `.editorconfig` 作为默认值——例如，Prettier 优先使用 `.editorconfig` 的 `indent_size` 和 `end_of_line` 作为配置；shfmt 完全依赖 `.editorconfig` 进行配置。但并非所有工具都读取 EditorConfig（如 Black、gofmt 不读取），这些工具有自己的配置文件。

#### 例子

**1. 完整的 .editorconfig**

```ini
# .editorconfig（项目根目录）
root = true

# ─── 全局默认 ───
[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
indent_style = space
indent_size = 4
max_line_length = 100

# ─── Python ───
[*.py]
indent_size = 4
max_line_length = 88

# ─── JavaScript/TypeScript ───
[*.{js,jsx,ts,tsx,mjs,cjs}]
indent_size = 2
max_line_length = 80

# ─── JSON/YAML/TOML ───
[*.{json,yml,yaml,toml}]
indent_size = 2

# ─── HTML/CSS/SCSS ───
[*.{html,htm,css,scss,sass,less}]
indent_size = 2

# ─── Go ───
[*.go]
indent_style = tab
indent_size = 4
max_line_length = 100

# ─── Rust ───
[*.rs]
indent_size = 4
max_line_length = 100

# ─── C/C++ ───
[*.{c,h,cpp,hpp,cc,hh,cxx,hxx}]
indent_size = 4
max_line_length = 100

# ─── Shell ───
[*.{sh,bash,zsh}]
indent_size = 4

# ─── Lua ───
[*.lua]
indent_size = 4

# ─── Ruby ───
[*.rb]
indent_size = 2

# ─── Java ───
[*.java]
indent_size = 4

# ─── Markdown（保留行尾空格，Markdown 用两个空格表示换行）───
[*.md]
trim_trailing_whitespace = false
max_line_length = off

# ─── Makefile（必须使用 tab）───
[Makefile]
indent_style = tab

# ─── Dockerfile ───
[Dockerfile]
indent_size = 4

# ─── Git 提交信息 ───
[COMMIT_EDITMSG]
max_line_length = 72

# ─── 排除目录 ───
[**/vendor/**]
charset = unset
end_of_line = unset
insert_final_newline = unset
trim_trailing_whitespace = unset
indent_style = unset
indent_size = unset
```

**2. Neovim 中使用 EditorConfig**

```lua
-- Neovim 0.9+ 内置 editorconfig 支持
-- 只需启用:
vim.g.editorconfig = true

-- 或使用 vim-editorconfig 插件（旧版本）
-- use 'gpanders/editorconfig.nvim'

-- 验证 EditorConfig 是否生效
:lua print(vim.bo.expandtab)  -- 应该根据 .editorconfig 设置
```

**3. VSCode 中使用 EditorConfig**

VSCode 通过 `EditorConfig.EditorConfig` 扩展支持。安装后，打开文件时自动应用 `.editorconfig` 配置，覆盖 VSCode 的默认设置。

```json
// settings.json
{
  "editorconfig.generateAuto": true,  // 自动生成 .editorconfig
  "files.insertFinalNewline": true,   // 全局默认（被 .editorconfig 覆盖）
  "files.trimTrailingWhitespace": true
}
```

**4. 生成 .editorconfig**

```bash
# 使用 editorconfig-cli 生成
# 安装: go install github.com/editorconfig/editorconfig-cli-go/cmd/editorconfig@latest

# 从现有文件推断配置
editorconfig init

# 验证文件是否符合 .editorconfig
editorconfig check src/main.py
```

**5. Prettier 读取 EditorConfig 的行为**

```json
// Prettier 优先级: .prettierrc > .editorconfig > Prettier 默认值
// 如果 .prettierrc 中没有指定 indentSize，Prettier 会使用 .editorconfig 的 indent_size

// .prettierrc（只覆盖 EditorConfig 没有的选项）
{
  "singleQuote": true,
  "semi": false
  // indentSize 和 endOfLine 从 .editorconfig 继承
}
```

#### 总结

- EditorConfig 是跨编辑器、跨语言的基础排版配置标准，`.editorconfig` 是配置文件。
- 通过 glob 模式为不同文件类型定义不同的缩进、行尾、编码等基础属性。
- 许多格式化工具（Prettier、shfmt）会读取 `.editorconfig` 作为默认值。
- 常见注意事项：`root = true` 必须在根目录的 `.editorconfig` 中设置，防止向上继续搜索；`[Makefile]` 必须使用 `indent_style = tab`（Make 语法要求）；Markdown 文件应设置 `trim_trailing_whitespace = false`（两个空格表示换行）；`unset` 值用于在子目录中取消父目录的配置。

---

### 第 35 讲 pre-commit Hooks 集成

#### 概念

`pre-commit` 是一个 Git 钩子管理框架，用于在 `git commit` 之前自动运行代码检查和格式化。它通过 `.pre-commit-config.yaml` 配置文件定义要运行的 hooks（钩子），支持多种语言的工具（Python、Node.js、Go、Rust 等）。pre-commit 框架自动管理 hook 的安装和版本锁定，确保团队成员和 CI 环境使用相同版本的检查工具。pre-commit 是将格式化和 lint 检查集成到开发工作流的标准方案。

#### 原理

**Git Hooks 机制**是 pre-commit 的基础。Git 在 `.git/hooks/` 目录中存放钩子脚本，在特定 Git 操作时自动执行。`pre-commit` 钩子在 `git commit` 之前执行，如果钩子返回非零退出码，提交被阻止。手动管理 `.git/hooks/` 中的脚本不可移植（不随仓库分发），pre-commit 框架通过一个统一的 `pre-commit` 脚本解决此问题——`pre-commit install` 命令在 `.git/hooks/pre-commit` 中安装一个调用 pre-commit 框架的脚本，实际的 hook 配置从 `.pre-commit-config.yaml` 读取。

**`.pre-commit-config.yaml` 结构**定义了要运行的 hooks。每个 repo 段指定一个 Git 仓库（包含 hook 定义），rev 指定版本，hooks 列表指定启用的 hook。pre-commit 框架支持多种语言的 hook 仓库：Python（通过 `language: python`）、Node.js（`language: node`）、Go（`language: golang`）、Rust（`language: rust`）、Docker（`language: docker`）、系统命令（`language: system`）等。pre-commit 自动为每种语言创建隔离的虚拟环境。

**hook 执行流程**：当开发者运行 `git commit` 时，pre-commit 框架被触发。框架读取 `.pre-commit-config.yaml`，按顺序执行每个 hook。hook 接收暂存文件（staged files）的路径作为参数，只检查即将提交的文件（而非整个代码库），这使得检查速度快。如果 hook 修改了文件（如格式化），pre-commit 检测到文件变更，标记 commit 为失败，开发者需要重新 `git add` 并提交。

**`pre-commit autoupdate`** 命令用于更新 hook 仓库的版本引用（rev 字段），将所有 hook 更新到最新版本。这类似于更新依赖版本，应在定期维护中执行。

#### 例子

**1. 安装 pre-commit**

```bash
# Python 安装
pip install pre-commit

# 或使用 pipx
pipx install pre-commit

# 或 Homebrew
brew install pre-commit

# 在 Git 仓库中安装钩子
cd /path/to/repo
pre-commit install

# 安装 pre-push 钩子（在 push 前运行）
pre-commit install --hook-type pre-push

# 安装 commit-msg 钩子（检查提交信息）
pre-commit install --hook-type commit-msg
```

**2. 完整的 .pre-commit-config.yaml（多语言项目）**

```yaml
# .pre-commit-config.yaml
# 顶层配置
default_install_hook_types: [pre-commit, pre-push]
default_stages: [pre-commit]

# 排除文件
exclude: |
  (?x)^(
      vendor/.*|
      third_party/.*|
      .*\.pb\.go|
      .*_gen\.py|
      node_modules/.*
  )$

# ─── 通用 hooks ───
repos:
  # 基础检查（尾随空格、行尾、大文件等）
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v5.0.0
    hooks:
      - id: trailing-whitespace
        args: [--markdown-linebreak-ext=md]
      - id: end-of-file-fixer
      - id: check-yaml
        args: [--allow-multiple-documents]
      - id: check-json
      - id: check-toml
      - id: check-merge-conflict
      - id: check-case-conflict
      - id: check-added-large-files
        args: [--maxkb=500]
      - id: mixed-line-ending
        args: [--fix=lf]
      - id: requirements-txt-fixer
      - id: debug-statements

  # EditorConfig 检查
  - repo: https://github.com/editorconfig-checker/editorconfig-checker
    rev: v3.0.1
    hooks:
      - id: editorconfig-checker
        args: [-disable-indent-size]

  # ─── Python ───
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.7.0
    hooks:
      - id: ruff
        args: [--fix, --exit-non-zero-on-fix]
      - id: ruff-format

  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.13.0
    hooks:
      - id: mypy
        additional_dependencies: [types-requests, pydantic]
        args: [--strict]
        exclude: ^tests/

  # ─── JavaScript/TypeScript ───
  - repo: https://github.com/pre-commit/mirrors-prettier
    rev: v4.0.0-alpha.8
    hooks:
      - id: prettier
        types_or: [javascript, jsx, ts, tsx, json, css, markdown, yaml]
        additional_dependencies:
          - prettier@3.3.3
          - prettier-plugin-tailwindcss@0.6.6

  - repo: https://github.com/pre-commit/mirrors-eslint
    rev: v9.12.0
    hooks:
      - id: eslint
        files: \.(js|jsx|ts|tsx)$
        types: [file]
        args: [--fix]

  # ─── Go ───
  - repo: https://github.com/dnephin/pre-commit-golang
    rev: v0.5.1
    hooks:
      - id: go-fmt
      - id: go-imports
        args: [-local, github.com/myorg/myproject]
      - id: go-mod-tidy

  - repo: https://github.com/golangci/golangci-lint
    rev: v1.62.0
    hooks:
      - id: golangci-lint
        args: [--config, .golangci.yml]

  # ─── Rust ───
  - repo: https://github.com/doublify/pre-commit-rust
    rev: v1.0
    hooks:
      - id: fmt
        args: [--check]
      - id: clippy
        args: [--, -D, warnings]

  # ─── C/C++ ───
  - repo: https://github.com/pocc/pre-commit-hooks
    rev: v1.3.5
    hooks:
      - id: clang-format
        args: [-i]
      - id: clang-tidy
        args: [-p=build]

  # ─── Shell ───
  - repo: https://github.com/scop/pre-commit-shfmt
    rev: v3.9.0
    hooks:
      - id: shfmt
        args: [-i, '4', -ci, -ln, bash]

  - repo: https://github.com/koalaman/shellcheck-precommit
    rev: v0.10.0
    hooks:
      - id: shellcheck

  # ─── Docker ───
  - repo: https://github.com/hadolint/hadolint
    rev: v2.12.0
    hooks:
      - id: hadolint-docker

  # ─── 提交信息检查 ───
  - repo: https://github.com/compilerla/conventional-pre-commit
    rev: v3.4.0
    hooks:
      - id: conventional-pre-commit
        stages: [commit-msg]
        args: [feat, fix, docs, style, refactor, perf, test, chore]
```

**3. 本地 hooks（使用系统安装的工具）**

```yaml
# .pre-commit-config.yaml（续）
  # ─── 本地 hooks ───
  - repo: local
    hooks:
      # 使用本地安装的 black
      - id: black
        name: black
        entry: black
        language: system
        types: [python]
        args: [--check]

      # 自定义检查脚本
      - id: custom-check
        name: Run custom check
        entry: ./scripts/custom-check.sh
        language: script
        pass_filenames: true
        always_run: false

      # 运行测试
      - id: pytest
        name: pytest
        entry: pytest
        language: system
        types: [python]
        pass_filenames: false
        args: [-x, --tb=short]
        stages: [pre-push]  # 只在 push 前运行
```

**4. 常用命令**

```bash
# 安装钩子
pre-commit install

# 手动运行所有 hooks（检查所有文件，不只是暂存文件）
pre-commit run --all-files

# 只运行特定 hook
pre-commit run ruff
pre-commit run ruff --all-files

# 只运行特定阶段的 hooks
pre-commit run --hook-stage pre-push

# 更新 hook 版本
pre-commit autoupdate

# 清理缓存
pre-commit clean

# 查看已安装的 hooks
pre-commit run --hook-stage pre-commit --verbose
```

**5. CI 中运行 pre-commit**

```yaml
# .github/workflows/pre-commit.yml
name: Pre-commit

on: [push, pull_request]

jobs:
  pre-commit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - uses: actions/setup-go@v5
        with:
          go-version: '1.22'
      - run: pip install pre-commit
      - run: pre-commit run --all-files
```

#### 总结

- pre-commit 是 Git 钩子管理框架，`.pre-commit-config.yaml` 定义要运行的 hooks。
- 支持 Python、Node.js、Go、Rust 等多种语言的工具，自动管理隔离环境。
- `pre-commit install` 安装 Git 钩子，`pre-commit run --all-files` 手动运行所有检查。
- 常见注意事项：hook 修改文件后 commit 会失败，需要重新 `git add`；`--exit-non-zero-on-fix` 参数确保即使 hook 自动修复了问题也返回失败，强制开发者重新审查；CI 中使用 `pre-commit run --all-files` 检查整个代码库；`pre-commit autoupdate` 应定期执行以更新工具版本。

---

### 第 36 讲 CI/CD 中的格式化与 Lint 检查

#### 概念

CI/CD（持续集成/持续部署）中的格式化和 lint 检查是代码质量保障的最后一道防线。通过在 CI 流水线中自动运行格式化检查和 lint，确保所有合并到主分支的代码都符合项目规范。本讲讲解如何在 GitHub Actions、GitLab CI、Jenkins 等 CI 平台中配置格式化和 lint 检查，以及如何优化 CI 检查的性能（缓存、增量检查、并行化）。

#### 原理

**CI 中格式化检查的原则**是"检查而非修复"——CI 不应自动修改代码，而应报告问题并阻止合并。这与 pre-commit 钩子的行为不同（pre-commit 可以自动修复）。CI 中的格式化检查通常使用工具的 `--check` 或 `--dry-run` 模式，只报告差异而不修改文件。如果检查失败，CI 作业返回非零退出码，阻止 PR 合并。

**工具的检查模式**：大多数格式化器提供检查模式——`ruff format --check`、`prettier --check`、`cargo fmt --check`、`gofmt -l`、`clang-format --dry-run --Werror`、`shfmt -d`、`stylua --check`。lint 工具通常默认就是检查模式——`ruff check`、`eslint`、`cargo clippy -- -D warnings`、`golangci-lint run`、`shellcheck`。CI 中应将这些命令组合，任何命令失败都导致 CI 失败。

**缓存优化**是 CI 性能的关键。格式化和 lint 工具的安装和运行可能耗时（如 npm install、pip install、cargo build）。CI 平台提供缓存机制——GitHub Actions 的 `actions/cache`、GitLab CI 的 `cache` 关键字。应缓存的内容包括：依赖目录（`node_modules/`、`.venv/`、`~/.cargo/`）、工具缓存（pre-commit 缓存、pip 缓存）、编译产物（`target/`、`build/`）。

**增量检查**通过只检查变更的文件来加速 CI。`pre-commit run` 默认只检查暂存文件，但在 CI 中通常需要检查所有文件（因为 CI 是全新检出）。替代方案是使用基于 git diff 的增量检查——例如，`golangci-lint run --new-from-rev=origin/main` 只检查相对于 main 分支的新问题。这种方法需要 CI 能访问目标分支的 git 历史。

#### 例子

**1. GitHub Actions：多语言格式化和 lint 检查**

```yaml
# .github/workflows/quality.yml
name: Code Quality

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  # ─── Python 检查 ───
  python:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'
          cache: pip
      - run: pip install ruff mypy
      # 格式化检查
      - run: ruff format --check .
      # Lint 检查
      - run: ruff check .
      # 类型检查
      - run: mypy --strict src/

  # ─── JavaScript/TypeScript 检查 ───
  javascript:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: npm
      - run: npm ci
      # 格式化检查
      - run: npx prettier --check .
      # ESLint 检查
      - run: npx eslint .
      # 类型检查
      - run: npx tsc --noEmit

  # ─── Rust 检查 ───
  rust:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: dtolnay/rust-toolchain@stable
        with:
          components: rustfmt, clippy
      - uses: Swatinem/rust-cache@v2
      # 格式化检查
      - run: cargo fmt --check
      # Clippy 检查
      - run: cargo clippy -- -D warnings
      # 编译检查
      - run: cargo check --all-targets

  # ─── Go 检查 ───
  go:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-go@v5
        with:
          go-version: '1.22'
          cache: true
      # 格式化检查
      - run: |
          if [ -n "$(gofmt -l .)" ]; then
            echo "Files need formatting:"
            gofmt -l .
            exit 1
          fi
      # golangci-lint 检查
      - uses: golangci/golangci-lint-action@v6
        with:
          version: v1.62
          args: --config=.golangci.yml

  # ─── C/C++ 检查 ───
  cpp:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: sudo apt install clang-format clang-tidy
      # 格式化检查
      - run: |
          find src include -name '*.cpp' -o -name '*.h' | while read f; do
            if ! clang-format --dry-run --Werror "$f"; then
              echo "File needs formatting: $f"
              exit 1
            fi
          done

  # ─── pre-commit（统一检查）───
  pre-commit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - uses: actions/setup-go@v5
        with:
          go-version: '1.22'
      - run: pip install pre-commit
      - name: Cache pre-commit
        uses: actions/cache@v4
        with:
          path: ~/.cache/pre-commit
          key: pre-commit-${{ runner.os }}-${{ hashFiles('.pre-commit-config.yaml') }}
      - run: pre-commit run --all-files
```

**2. GitLab CI 配置**

```yaml
# .gitlab-ci.yml
stages:
  - lint
  - format

variables:
  PIP_CACHE_DIR: "$CI_PROJECT_DIR/.cache/pip"

cache:
  paths:
    - .cache/pip
    - node_modules/
    - .cargo/

# Python 检查
python-lint:
  stage: lint
  image: python:3.12
  script:
    - pip install ruff mypy
    - ruff check .
    - mypy --strict src/

python-format:
  stage: format
  image: python:3.12
  script:
    - pip install ruff
    - ruff format --check .

# JavaScript 检查
js-lint:
  stage: lint
  image: node:20
  script:
    - npm ci
    - npx eslint .
    - npx prettier --check .

# Rust 检查
rust-check:
  stage: lint
  image: rust:latest
  before_script:
    - rustup component add rustfmt clippy
  script:
    - cargo fmt --check
    - cargo clippy -- -D warnings

# Go 检查
go-check:
  stage: lint
  image: golang:1.22
  script:
    - go install github.com/golangci/golangci-lint/cmd/golangci-lint@latest
    - golangci-lint run --config=.golangci.yml
    - test -z "$(gofmt -l .)" || (echo "Code needs formatting" && exit 1)
```

**3. 使用 pre-commit CI 框架**

```yaml
# .pre-commit-config.yaml 中添加 ci 段
ci:
  # CI 中跳过的 hooks（如需要特殊环境的 hooks）
  skip: [clang-tidy, hadolint-docker]
  # CI 中自动修复的 hooks（生成 PR 修复）
  autofix_commit_msg: |
    [pre-commit.ci] auto fixes from pre-commit.com hooks

    for more information, see https://pre-commit.ci
  autofix_prs: true
  autoupdate_commit_msg: '[pre-commit.ci] pre-commit autoupdate'
  autoupdate_schedule: weekly
  submodules: false
```

```yaml
# .github/workflows/pre-commit-ci.yml
# 使用 pre-commit.ci 服务（自动创建修复 PR）
# 在 https://pre-commit.ci 配置仓库后，自动启用
```

**4. 增量检查优化**

```yaml
# .github/workflows/incremental-lint.yml
name: Incremental Lint

on:
  pull_request:
    branches: [main]

jobs:
  lint-changed-files:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0  # 获取完整历史，用于 git diff

      - name: Get changed files
        id: changed
        run: |
          # 获取相对于 main 分支变更的文件
          FILES=$(git diff --name-only origin/main...HEAD -- '*.py' | tr '\n' ' ')
          echo "files=$FILES" >> $GITHUB_OUTPUT

      - name: Ruff check changed files
        if: steps.changed.outputs.files != ''
        run: |
          pip install ruff
          ruff check ${{ steps.changed.outputs.files }}
          ruff format --check ${{ steps.changed.outputs.files }}
```

**5. 合并保护规则（GitHub Branch Protection）**

```yaml
# 通过 GitHub API 设置分支保护规则（或通过 UI 设置）
# 要求以下检查通过才能合并:
# - python (ruff + mypy)
# - javascript (eslint + prettier)
# - rust (fmt + clippy)
# - pre-commit
#
# 设置:
# - Require status checks to pass before merging
# - Require branches to be up to date before merging
# - Include administrators（管理员也受规则约束）
```

#### 总结

- CI 中的格式化检查使用工具的 `--check` 模式，只报告问题不修复。
- 缓存依赖和工具是 CI 性能优化的关键——缓存 `node_modules/`、`.venv/`、`~/.cargo/` 等。
- `pre-commit run --all-files` 是在 CI 中统一运行所有检查的标准方式。
- 常见注意事项：CI 中应使用 `--check`/`--dry-run` 模式，不要自动修复（自动修复应交给 pre-commit 钩子或 pre-commit.ci 服务）；增量检查通过 git diff 只检查变更文件，加速 PR 检查；分支保护规则确保 CI 检查通过才能合并，是代码质量的最终保障；`fetch-depth: 0` 是增量检查的前提，确保 CI 能访问完整的 git 历史。
