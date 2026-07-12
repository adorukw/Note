# .vscode 配置完全教程

> 从零开始，系统掌握 VS Code 工作区配置的完整体系

---

## 课程总览

- **预计讲数**：18 讲（7 章）
- **学习目标**：从零掌握 .vscode 目录的完整配置体系，能独立为各类项目编写专业级配置
- **渐进结构**：基础认知 → 核心配置 → 任务系统 → 调试配置 → 个性化定制 → 团队协作 → 实战整合

### 详细章节目录

| 章节 | 讲次 | 标题 |
|------|------|------|
| **第一章 入门基础** | 第1讲 | .vscode 目录全景认知 |
| | 第2讲 | settings.json 配置基础 |
| | 第3讲 | 配置作用域：用户 vs 工作区 vs 文件夹 |
| **第二章 编辑器核心配置** | 第4讲 | 编辑器外观与行为配置 |
| | 第5讲 | 文件管理与排除规则 |
| | 第6讲 | 语言服务与格式化配置 |
| **第三章 任务系统** | 第7讲 | tasks.json 任务系统入门 |
| | 第8讲 | 任务变量与输入变量 |
| | 第9讲 | 复合任务与问题匹配器 |
| **第四章 调试配置** | 第10讲 | launch.json 调试基础 |
| | 第11讲 | 调试变量与复合配置 |
| | 第12讲 | 常见语言调试实战 |
| **第五章 代码片段与快捷键** | 第13讲 | 自定义代码片段 |
| | 第14讲 | 快捷键定制 |
| **第六章 团队协作与扩展** | 第15讲 | 扩展推荐与团队共享 |
| | 第16讲 | 多根工作区与远程配置 |
| **第七章 实战整合** | 第17讲 | 前端项目完整配置实战 |
| | 第18讲 | Python 后端项目完整配置实战 |

---

# 第一章 入门基础

## 第1讲 .vscode 目录全景认知

### 概念

`.vscode` 是 VS Code 在项目根目录下自动识别的一个特殊文件夹。它用于存放与当前工作区（Workspace）相关的配置文件，包括编辑器设置、任务定义、调试配置、代码片段和扩展推荐等。这个目录本身不是 VS Code 创建的，而是由开发者手动创建或由 VS Code 在你保存工作区设置时自动生成。

`.vscode` 目录的核心价值在于**项目级配置隔离**：它让每个项目可以拥有独立的编辑器行为、构建任务和调试方案，而不会影响其他项目或全局环境。当你把项目分享给团队成员时，`.vscode` 目录中的配置可以一并提交到版本控制，确保所有人获得一致的开发体验。

### 原理

VS Code 的配置体系采用**分层覆盖**机制，配置优先级从低到高依次为：

1. **默认设置**（Default Settings）：VS Code 内置的默认值
2. **用户设置**（User Settings）：全局生效，存储在用户主目录下
3. **工作区设置**（Workspace Settings）：存储在 `.vscode/settings.json` 中，仅对当前项目生效
4. **文件夹设置**（Folder Settings）：多根工作区中，特定文件夹的配置

当多层配置存在冲突时，高优先级的设置会覆盖低优先级的设置。`.vscode` 目录中的 `settings.json` 属于工作区级别，它会覆盖用户级别的同名设置。这种设计使得开发者可以在保持个人偏好的同时，为特定项目定制专属配置。

`.vscode` 目录中常见的配置文件包括：

| 文件 | 作用 |
|------|------|
| `settings.json` | 工作区级别的编辑器设置 |
| `tasks.json` | 自定义构建任务、运行任务 |
| `launch.json` | 调试配置 |
| `extensions.json` | 推荐安装的扩展列表 |
| `keybindings.json` | 工作区级别的快捷键绑定 |
| `*.code-snippets` | 工作区级别的代码片段 |

### 例子

一个典型的前端项目目录结构如下：

```
my-project/
├── .vscode/
│   ├── settings.json       # 编辑器设置
│   ├── tasks.json          # 构建任务
│   ├── launch.json         # 调试配置
│   ├── extensions.json     # 推荐扩展
│   └── react.code-snippets # React 代码片段
├── src/
│   ├── index.tsx
│   └── App.tsx
├── package.json
└── tsconfig.json
```

创建 `.vscode` 目录最简单的方式是直接在项目根目录下新建文件夹，也可以通过 VS Code 界面操作：按下 `Ctrl+Shift+P`（Mac 上是 `Cmd+Shift+P`）打开命令面板，输入 `Preferences: Open Workspace Settings (JSON)`，VS Code 会自动创建 `.vscode` 目录和 `settings.json` 文件。

### 总结

- `.vscode` 是项目级配置的存放目录，核心价值是配置隔离与团队共享
- VS Code 采用分层覆盖机制，工作区设置优先于用户设置
- 目录中可包含 settings、tasks、launch、extensions 等多种配置文件
- 建议将 `.vscode` 目录纳入版本控制（但需注意排除个人偏好类设置）
- 不同配置文件各司其职，后续各讲将逐一深入讲解

---

## 第2讲 settings.json 配置基础

### 概念

`settings.json` 是 `.vscode` 目录中最核心的配置文件，它以 JSON 格式存储编辑器的各项设置。在这个文件中，你可以控制从字体大小、主题颜色到语言服务、格式化工具等几乎所有编辑器行为。工作区级别的 `settings.json` 位于 `.vscode/settings.json`，仅对当前项目生效。

与用户级别的 `settings.json`（全局生效）不同，工作区级别的 `settings.json` 允许你为不同项目定制不同的编辑器行为。例如，一个 Python 项目可能需要使用空格缩进，而一个 Makefile 项目则需要使用 Tab 缩进，这些差异可以通过工作区设置来实现。

### 原理

`settings.json` 的本质是一个 JSON 对象（JSON Object），其中每个键值对代表一项设置。键名采用 `publisher.extension.property` 的点分命名风格，例如 `editor.fontSize` 控制字体大小，`files.exclude` 控制文件排除规则。VS Code 启动时会读取这个文件，将其中定义的设置应用到当前工作区。

设置项的类型多种多样，常见的包括：

- **基本类型**：布尔值（`true`/`false`）、数字、字符串
- **复合类型**：数组、对象
- **枚举类型**：从预定义值中选择

VS Code 对 `settings.json` 提供了完整的 **JSON Schema 智能提示**：当你输入键名时，编辑器会自动补全并显示该设置的说明文档；当你输入值时，会根据类型提供相应的提示。这意味着你不需要死记硬背所有设置项，只需在编辑器中输入即可获得引导。

### 例子

以下是一个前端项目的典型 `settings.json` 配置：

```json
{
  // 编辑器基本设置
  "editor.fontSize": 14,
  "editor.tabSize": 2,
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit"
  },

  // 文件相关设置
  "files.autoSave": "afterDelay",
  "files.autoSaveDelay": 1000,
  "files.eol": "\n",

  // 语言特定设置
  "[javascript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[typescript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[json]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },

  // ESLint 设置
  "eslint.validate": [
    "javascript",
    "typescript",
    "vue"
  ],
  "eslint.workingDirectories": ["./"],

  // 终端设置
  "terminal.integrated.defaultProfile.osx": "zsh",
  "terminal.integrated.scrollback": 10000
}
```

注意几个要点：JSON 文件中允许使用 `//` 注释（VS Code 使用的是 JSONC 格式）；语言特定设置使用 `[语言ID]` 作为键名，可以覆盖全局设置；`"explicit"` 是较新版本的 VS Code 中 `codeActionsOnSave` 推荐使用的值（旧版本使用 `true`）。

### 总结

- `settings.json` 以 JSONC 格式存储，支持注释
- 键名采用点分命名法，如 `editor.fontSize`
- 利用 JSON Schema 智能提示可以高效编写配置
- 语言特定设置通过 `[语言ID]` 语法实现差异化配置
- 工作区设置会覆盖用户设置中的同名项
- 编写时注意区分新旧版本的取值差异（如 `"explicit"` vs `true`）

---

## 第3讲 配置作用域：用户 vs 工作区 vs 文件夹

### 概念

VS Code 的配置系统有三种主要作用域：**用户设置**（User Settings）、**工作区设置**（Workspace Settings）和**文件夹设置**（Folder Settings）。理解这三者的区别和优先级关系，是编写高质量 `.vscode` 配置的前提。简单来说，用户设置是"全局个人偏好"，工作区设置是"项目级约定"，文件夹设置是"多根工作区中特定文件夹的配置"。

### 原理

三种作用域的存储位置和生效范围各不相同：

| 作用域 | 存储位置 | 生效范围 | 典型用途 |
|--------|----------|----------|----------|
| 用户设置 | `~/.config/Code/User/settings.json`（Linux/Mac）或 `%APPDATA%\Code\User\settings.json`（Windows） | 所有项目 | 个人主题、字体、通用快捷键 |
| 工作区设置 | `.vscode/settings.json` | 当前项目 | 项目特定格式化、语言服务 |
| 文件夹设置 | `<folder>/.vscode/settings.json` | 多根工作区中的特定文件夹 | 子项目独立配置 |

**优先级规则**：文件夹设置 > 工作区设置 > 用户设置 > 默认设置。当多个层级定义了同一个设置项时，高优先级的值会覆盖低优先级的值。

**哪些设置可以放在工作区级别？** 并非所有设置都支持工作区作用域。VS Code 将每项设置标记了 `scope` 属性，常见的 scope 包括：

- `application`：仅能用于用户设置（如 `update.mode`）
- `window`：窗口级别，可用于用户和工作区设置
- `resource`：资源级别，可用于用户、工作区和文件夹设置（大多数编辑器设置属于此类）
- `language-overridable`：可被特定语言覆盖（如 `editor.tabSize`）

在设置界面中，如果某项设置旁边出现一个灰色提示"Cannot be overwritten in Workspace"，说明它不支持工作区作用域。

### 例子

**场景**：你同时维护一个 Python 项目和一个 Go 项目，Python 用 4 空格缩进，Go 用 Tab 缩进（gofmt 强制）。

用户设置（`~/.config/Code/User/settings.json`）—— 保持个人通用偏好：

```json
{
  "editor.fontSize": 14,
  "editor.fontFamily": "Fira Code, Consolas, monospace",
  "editor.fontLigatures": true,
  "editor.minimap.enabled": false,
  "workbench.colorTheme": "Default Dark+",
  "files.autoSave": "afterDelay"
}
```

Python 项目的工作区设置（`python-project/.vscode/settings.json`）：

```json
{
  "editor.tabSize": 4,
  "editor.insertSpaces": true,
  "python.defaultInterpreterPath": "./venv/bin/python",
  "python.formatting.provider": "black",
  "python.linting.enabled": true,
  "python.linting.pylintEnabled": true,
  "[python]": {
    "editor.defaultFormatter": "ms-python.black-formatter",
    "editor.formatOnSave": true
  }
}
```

Go 项目的工作区设置（`go-project/.vscode/settings.json`）：

```json
{
  "editor.tabSize": 4,
  "editor.insertSpaces": false,
  "go.formatTool": "goimports",
  "go.lintTool": "golangci-lint",
  "go.lintOnSave": "workspace",
  "gopls": {
    "ui.semanticTokens": true,
    "formatting.gofmt": true
  },
  "[go]": {
    "editor.defaultFormatter": "golang.go",
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
      "source.organizeImports": "explicit"
    }
  }
}
```

通过这种分层配置，两个项目可以共存于同一台机器，各自拥有正确的缩进和格式化策略，而你的个人偏好（字体、主题）在所有项目中保持一致。

### 总结

- 三种作用域：用户（全局）、工作区（项目级）、文件夹（多根工作区中的子项目）
- 优先级：文件夹 > 工作区 > 用户 > 默认
- 并非所有设置都支持工作区作用域，需查看设置的 `scope` 属性
- 最佳实践：个人偏好放用户设置，项目约定放工作区设置
- 工作区设置应提交到版本控制，用户设置通常不提交

---

# 第二章 编辑器核心配置

## 第4讲 编辑器外观与行为配置

### 概念

编辑器外观与行为配置是 `.vscode/settings.json` 中最常用的部分，它控制着编辑器的视觉呈现和交互方式。这类配置包括字体、字号、行高、光标样式、缩进规则、自动保存、代码折叠等数十项设置。合理配置这些选项可以显著提升编码舒适度和效率。

### 原理

编辑器配置主要归属于 `editor.*` 命名空间。VS Code 将编辑器行为拆分为细粒度的设置项，每一项控制一个独立的行为。这些设置大多属于 `resource` 作用域，可以被语言特定设置覆盖。

几个关键的设计原理：

1. **语言覆盖机制**：通过 `[语言ID]` 语法，可以为特定语言定制编辑器行为。例如 `[python]` 块中的设置仅对 Python 文件生效，会覆盖全局的 `editor.*` 设置。

2. **格式化链**：`editor.defaultFormatter` 指定默认格式化器，`editor.formatOnSave` 控制保存时是否自动格式化。多个格式化扩展可能同时注册，需要明确指定使用哪一个。

3. **代码操作**：`editor.codeActionsOnSave` 允许在保存时触发一系列代码操作（如 ESLint 自动修复、组织导入），这是保持代码质量的重要机制。

### 例子

以下是一个注重可读性和一致性的编辑器配置：

```json
{
  // ===== 字体与排版 =====
  "editor.fontSize": 14,
  "editor.fontFamily": "'Fira Code', 'JetBrains Mono', Consolas, monospace",
  "editor.fontLigatures": true,
  "editor.lineHeight": 22,
  "editor.letterSpacing": 0.5,

  // ===== 光标与选中 =====
  "editor.cursorStyle": "line",
  "editor.cursorBlinking": "smooth",
  "editor.cursorSmoothCaretAnimation": "on",
  "editor.multiCursorModifier": "ctrlCmd",
  "editor.selectionHighlight": true,

  // ===== 缩进与换行 =====
  "editor.tabSize": 2,
  "editor.insertSpaces": true,
  "editor.detectIndentation": true,
  "editor.wordWrap": "on",
  "editor.wrappingIndent": "indent",

  // ===== 保存行为 =====
  "editor.formatOnSave": true,
  "editor.formatOnPaste": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit",
    "source.organizeImports": "never"
  },

  // ===== 显示增强 =====
  "editor.renderWhitespace": "boundary",
  "editor.renderControlCharacters": true,
  "editor.renderLineHighlight": "all",
  "editor.guides.bracketPairs": "active",
  "editor.bracketPairColorization.enabled": true,
  "editor.minimap.enabled": false,
  "editor.stickyScroll.enabled": true,
  "editor.inlayHints.enabled": "onUnlessPressed",

  // ===== 语言特定覆盖 =====
  "[python]": {
    "editor.tabSize": 4,
    "editor.defaultFormatter": "ms-python.black-formatter",
    "editor.formatOnSave": true
  },
  "[rust]": {
    "editor.defaultFormatter": "rust-lang.rust-analyzer",
    "editor.formatOnSave": true
  },
  "[markdown]": {
    "editor.wordWrap": "on",
    "editor.quickSuggestions": {
      "other": true,
      "comments": true,
      "strings": true
    }
  },
  "[jsonc]": {
    "editor.defaultFormatter": "vscode.json-language-features"
  }
}
```

**逐项解析重点配置**：

- `editor.fontLigatures: true`：启用连字（如 `=>` 显示为箭头），需字体支持（Fira Code、JetBrains Mono 等）
- `editor.renderWhitespace: "boundary"`：仅显示单词间的空格，避免视觉干扰
- `editor.guides.bracketPairs: "active"`：高亮当前光标所在的括号对，配合 `bracketPairColorization` 提升可读性
- `editor.stickyScroll.enabled: true`：滚动时顶部固定显示当前所在的函数/类定义，是 VS Code 1.70+ 的新特性
- `editor.inlayHints.enabled: "onUnlessPressed"`：显示内联类型提示，按 Ctrl 时隐藏，平衡信息量与简洁度

### 总结

- 编辑器配置归属于 `editor.*` 命名空间，细粒度控制各项行为
- 语言特定设置通过 `[语言ID]` 覆盖全局设置
- `formatOnSave` + `codeActionsOnSave` 是保持代码质量的核心组合
- 善用 `bracketPairColorization`、`stickyScroll`、`inlayHints` 等新特性提升体验
- 字体连字需配合支持连字的编程字体

---

## 第5讲 文件管理与排除规则

### 概念

文件管理配置控制 VS Code 如何发现、显示和处理项目中的文件。这类配置归属于 `files.*` 命名空间，包括文件排除规则、文件关联、文件监视、编码设置等。其中最重要的是**文件排除规则**（`files.exclude`），它决定哪些文件/文件夹在资源管理器中可见，直接影响编辑器的响应速度和视觉整洁度。

### 原理

VS Code 在打开项目时会扫描整个目录树，构建文件索引。`files.exclude` 告诉编辑器哪些路径应该被隐藏——被排除的文件不会显示在资源管理器中，也不会被搜索（除非显式启用）。这不仅能减少视觉干扰，还能提升大型项目的搜索性能。

**排除规则的匹配机制**使用 glob 模式：

- `*` 匹配单层路径中的任意字符
- `**` 匹配任意层级的路径
- `{a,b}` 匹配 a 或 b
- 排除规则以对象形式定义，键为 glob 模式，值为 `true`（排除）或 `false`（取消排除）

**搜索排除**（`search.exclude`）与 `files.exclude` 的区别：`files.exclude` 同时影响资源管理器显示和搜索；`search.exclude` 仅影响搜索，被排除的文件仍显示在资源管理器中。通常 `search.exclude` 是 `files.exclude` 的超集。

**文件关联**（`files.associations`）告诉 VS Code 如何识别文件类型。默认情况下，VS Code 根据扩展名判断语言，但有些文件（如无扩展名的配置文件）需要手动关联。

### 例子

一个典型的全栈项目文件管理配置：

```json
{
  // ===== 文件排除：资源管理器中隐藏 =====
  "files.exclude": {
    "**/.git": true,
    "**/.svn": true,
    "**/.hg": true,
    "**/CVS": true,
    "**/.DS_Store": true,
    "**/node_modules": true,
    "**/dist": true,
    "**/build": true,
    "**/.next": true,
    "**/.nuxt": true,
    "**/coverage": true,
    "**/*.pyc": true,
    "**/__pycache__": true,
    "**/.venv": true,
    "**/.env.local": true
  },

  // ===== 搜索排除：仅影响搜索 =====
  "search.exclude": {
    "**/package-lock.json": true,
    "**/yarn.lock": true,
    "**/pnpm-lock.yaml": true,
    "**/minified.js": true,
    "**/*.map": true
  },

  // ===== 文件关联 =====
  "files.associations": {
    "*.vue": "vue",
    "*.svelte": "svelte",
    "*.astro": "astro",
    ".env": "dotenv",
    ".env.*": "dotenv",
    "*.conf": "ini",
    "Dockerfile*": "dockerfile",
    "tsconfig.json": "jsonc",
    ".eslintrc": "jsonc",
    ".prettierrc": "jsonc"
  },

  // ===== 编码与换行 =====
  "files.encoding": "utf8",
  "files.eol": "\n",
  "files.insertFinalNewline": true,
  "files.trimTrailingWhitespace": true,
  "files.trimFinalNewlines": true,

  // ===== 自动保存 =====
  "files.autoSave": "afterDelay",
  "files.autoSaveDelay": 1000,

  // ===== 文件监视 =====
  "files.watcherExclude": {
    "**/.git/objects/**": true,
    "**/.git/subtree-cache/**": true,
    "**/node_modules/**": true,
    "**/dist/**": true
  },

  // ===== 默认新建文件类型 =====
  "files.defaultLanguage": "markdown"
}
```

**关键配置解析**：

- `files.eol: "\n"`：强制使用 Unix 换行符，避免跨平台协作时的 CRLF/LF 冲突
- `files.insertFinalNewline: true`：文件末尾自动插入空行，符合 POSIX 标准
- `files.trimTrailingWhitespace: true`：保存时自动删除行尾空格（Markdown 文件可通过 `"[markdown]"` 覆盖关闭）
- `files.watcherExclude`：排除文件监视器扫描的路径，对大型项目（如 monorepo）能显著降低 CPU 占用
- `files.associations` 中将 `tsconfig.json`、`.eslintrc` 等配置文件关联为 `jsonc`，使其支持注释

### 总结

- `files.exclude` 控制资源管理器显示，`search.exclude` 仅控制搜索
- 排除规则使用 glob 模式，`**` 匹配任意层级
- `files.associations` 用于无扩展名或特殊扩展名文件的类型识别
- 跨平台协作务必统一 `files.eol` 为 `\n`
- `files.watcherExclude` 对大型项目的性能优化至关重要
- `trimTrailingWhitespace` + `insertFinalNewline` 是代码整洁的基础

---

## 第6讲 语言服务与格式化配置

### 概念

语言服务配置控制 VS Code 如何为不同编程语言提供智能提示、诊断、格式化和代码操作。这类配置是 `.vscode` 中最复杂也最实用的部分，涉及 TypeScript、ESLint、Prettier、Python、Go 等多种语言工具链的集成。正确配置语言服务可以让 VS Code 从一个"文本编辑器"升级为"智能 IDE"。

### 原理

VS Code 的语言能力由**语言服务器协议**（Language Server Protocol, LSP）驱动。每种语言通常有一个对应的语言服务器（如 TypeScript 的 `tsserver`、Python 的 `Pylance`、Rust 的 `rust-analyzer`），它们在后台运行，为编辑器提供补全、跳转、诊断等功能。

**格式化的工作流**：

1. 保存文件时，`editor.formatOnSave` 触发格式化
2. VS Code 查找 `editor.defaultFormatter` 指定的格式化器
3. 格式化器（如 Prettier）读取自身配置文件（`.prettierrc`）并格式化代码
4. `editor.codeActionsOnSave` 触发额外的代码操作（如 ESLint 修复）

**ESLint 与 Prettier 的协作**：这是前端项目中最常见的组合。ESLint 负责代码质量（未使用变量、隐式类型转换等），Prettier 负责代码风格（缩进、引号、换行）。为避免冲突，通常使用 `eslint-config-prettier` 关闭 ESLint 中与 Prettier 冲突的规则，然后在 VS Code 中配置保存时先运行 Prettier 格式化，再运行 ESLint 修复。

### 例子

一个完整的 TypeScript + React + ESLint + Prettier 项目配置：

```json
{
  // ===== TypeScript 配置 =====
  "typescript.tsdk": "node_modules/typescript/lib",
  "typescript.enablePromptUseWorkspaceTsdk": true,
  "typescript.preferences.importModuleSpecifier": "relative",
  "typescript.preferences.quoteStyle": "single",
  "typescript.updateImportsOnFileMove.enabled": "always",
  "typescript.inlayHints.parameterNames.enabled": "all",
  "typescript.inlayHints.variableTypes.enabled": true,
  "typescript.inlayHints.propertyDeclarationTypes.enabled": true,

  // ===== ESLint 配置 =====
  "eslint.enable": true,
  "eslint.packageManager": "pnpm",
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact",
    "vue",
    "json"
  ],
  "eslint.run": "onType",
  "eslint.options": {
    "overrideConfigFile": ".eslintrc.cjs",
    "cache": true
  },
  "eslint.workingDirectories": [
    {
      "mode": "auto"
    }
  ],

  // ===== Prettier 配置 =====
  "prettier.enable": true,
  "prettier.configPath": ".prettierrc",
  "prettier.ignorePath": ".prettierignore",
  "prettier.requireConfig": true,
  "prettier.useEditorConfig": false,

  // ===== 编辑器格式化行为 =====
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit",
    "source.organizeImports": "never"
  },

  // ===== 语言特定格式化 =====
  "[typescript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[typescriptreact]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[javascript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[json]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[jsonc]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[css]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[markdown]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "editor.wordWrap": "on",
    "editor.quickSuggestions": false
  },

  // ===== 诊断与提示 =====
  "editor.inlineSuggest.enabled": true,
  "editor.suggest.showStatusBar": true,
  "editor.suggest.preview": true,
  "javascript.suggestionActions.enabled": false,
  "javascript.updateImportsOnFileMove.enabled": "always"
}
```

**配置要点解析**：

- `typescript.tsdk`：指定项目使用的 TypeScript 版本（从 `node_modules` 加载），确保编辑器与项目使用同一版本
- `eslint.workingDirectories: [{"mode": "auto"}]`：monorepo 中自动检测每个子项目的 ESLint 配置
- `prettier.requireConfig: true`：仅当项目存在 Prettier 配置文件时才启用格式化，避免对未配置的项目强加风格
- `source.organizeImports: "never"`：禁用保存时自动组织导入（因为可能与 ESLint 的 import 排序规则冲突）
- `typescript.inlayHints.*`：显示参数名、变量类型等内联提示，提升类型可读性

### 总结

- 语言服务由 LSP 驱动，每种语言有对应的服务器
- ESLint 管代码质量，Prettier 管代码风格，两者协作需用 `eslint-config-prettier` 消除冲突
- `typescript.tsdk` 应指向项目本地的 TypeScript，保证版本一致
- monorepo 中用 `eslint.workingDirectories` 的 `auto` 模式自动适配子项目
- `prettier.requireConfig` 避免对未配置项目强加风格
- 保存时的操作顺序：格式化 → 代码操作（ESLint 修复等）

---

# 第三章 任务系统

## 第7讲 tasks.json 任务系统入门

### 概念

`tasks.json` 是 VS Code 任务系统的配置文件，用于定义和自动化项目中的构建、测试、运行等重复性操作。通过任务系统，你可以将常用的命令行操作（如 `npm run build`、`pytest`、`go test`）封装为命名任务，并通过命令面板或快捷键快速执行，无需手动在终端中输入。

任务系统是连接编辑器与外部工具链的桥梁。它不仅执行命令，还能解析命令输出中的错误信息，将其转化为 VS Code 的"问题"（Problems）面板中的可点击条目，实现从错误报告到源码定位的跳转。

### 原理

VS Code 任务系统的核心概念：

1. **任务（Task）**：一个可执行单元，包含命令、参数、工作目录、输出解析规则等
2. **任务类型（type）**：`shell`（通过终端执行命令）或 `process`（直接启动进程）
3. **任务标签（label）**：任务的唯一标识符，用于引用和调用
4. **问题匹配器（problemMatcher）**：解析命令输出，提取错误/警告信息

任务的执行流程：用户触发任务 → VS Code 在集成终端中执行命令 → 命令输出被问题匹配器实时解析 → 错误信息显示在"问题"面板 → 用户点击问题条目跳转到对应代码行。

`tasks.json` 的基本结构：

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "任务名称",
      "type": "shell",
      "command": "要执行的命令",
      "args": ["参数1", "参数2"],
      "options": {
        "cwd": "${workspaceFolder}"
      },
      "problemMatcher": []
    }
  ]
}
```

### 例子

一个前端项目的 `tasks.json`，涵盖开发、构建、测试、部署：

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "dev",
      "detail": "启动开发服务器",
      "type": "shell",
      "command": "pnpm",
      "args": ["dev"],
      "isBackground": true,
      "problemMatcher": [],
      "group": "build",
      "presentation": {
        "reveal": "always",
        "panel": "dedicated",
        "clear": true
      }
    },
    {
      "label": "build",
      "detail": "生产环境构建",
      "type": "shell",
      "command": "pnpm",
      "args": ["build"],
      "group": {
        "kind": "build",
        "isDefault": true
      },
      "problemMatcher": ["$tsc"],
      "presentation": {
        "reveal": "silent",
        "panel": "shared"
      }
    },
    {
      "label": "test",
      "detail": "运行单元测试",
      "type": "shell",
      "command": "pnpm",
      "args": ["test", "--", "--watch=false"],
      "group": {
        "kind": "test",
        "isDefault": true
      },
      "problemMatcher": []
    },
    {
      "label": "lint",
      "detail": "代码检查",
      "type": "shell",
      "command": "pnpm",
      "args": ["lint"],
      "problemMatcher": ["$eslint-stylish"]
    },
    {
      "label": "clean",
      "detail": "清理构建产物",
      "type": "shell",
      "command": "rm",
      "args": ["-rf", "dist", "node_modules/.cache"],
      "windows": {
        "command": "Remove-Item",
        "args": ["-Recurse", "-Force", "dist", "node_modules/.cache"]
      }
    }
  ]
}
```

**关键字段解析**：

- `group`：将任务归类为 `build` 或 `test`，设置 `isDefault: true` 后可用 `Ctrl+Shift+B`（构建）或 `Ctrl+Shift+;`（测试）快速运行
- `isBackground: true`：标记为后台任务（如开发服务器），VS Code 不会等待它结束
- `problemMatcher: ["$tsc"]`：使用内置的 TypeScript 编译器输出匹配器，将编译错误显示在问题面板
- `presentation`：控制终端的呈现方式——`reveal` 决定是否自动显示终端，`panel` 决定是否使用独立面板，`clear` 决定是否清空上次输出
- `windows`：Windows 平台的覆盖配置，实现跨平台兼容

**执行任务的方式**：
- 命令面板 → `Tasks: Run Task` → 选择任务
- 快捷键 `Ctrl+Shift+B` 运行默认构建任务
- 快捷键 `Ctrl+Shift+;` 运行默认测试任务

### 总结

- `tasks.json` 封装命令行操作，实现一键执行
- 任务类型分 `shell` 和 `process`，绝大多数场景用 `shell`
- `problemMatcher` 是任务系统的精髓，将命令输出转化为可点击的问题
- `group` + `isDefault` 配合快捷键实现快速构建/测试
- `presentation` 控制终端呈现，`windows` 字段实现跨平台
- 后台任务（如 dev server）需设置 `isBackground: true`

---

## 第8讲 任务变量与输入变量

### 概念

任务变量（Variables）是 `tasks.json` 中的动态占位符，它们在任务执行时被替换为实际值。VS Code 提供了丰富的预定义变量（如当前文件路径、工作区路径、选中文件名等），以及自定义的**输入变量**（Input Variables）机制，允许在任务执行时弹出交互式提示让用户输入参数。掌握变量系统是编写灵活、可复用任务配置的关键。

### 原理

**预定义变量**在任务执行时自动替换，常用的包括：

| 变量 | 含义 | 示例值 |
|------|------|--------|
| `${workspaceFolder}` | 工作区根目录路径 | `/home/user/my-project` |
| `${workspaceFolderBasename}` | 工作区目录名 | `my-project` |
| `${file}` | 当前文件完整路径 | `/home/user/my-project/src/index.ts` |
| `${fileDirname}` | 当前文件所在目录 | `/home/user/my-project/src` |
| `${fileBasename}` | 当前文件名（含扩展名） | `index.ts` |
| `${fileBasenameNoExtension}` | 当前文件名（不含扩展名） | `index` |
| `${relativeFile}` | 相对于工作区的文件路径 | `src/index.ts` |
| `${lineNumber}` | 当前光标行号 | `42` |
| `${selectedText}` | 当前选中的文本 | `someVariable` |
| `${env:VAR_NAME}` | 环境变量 | `/usr/bin` |

**输入变量**（Input Variables）通过 `inputs` 数组定义，在任务执行时弹出选择框或输入框。支持三种类型：

1. `promptString`：文本输入框
2. `pickString`：下拉选择列表
3. `command`：执行 VS Code 命令获取值

输入变量的引用语法为 `${input:变量名}`。

### 例子

一个支持交互式参数的任务配置，包含环境选择、端口输入、动态命令执行：

```json
{
  "version": "2.0.0",
  "inputs": [
    {
      "id": "environment",
      "type": "pickString",
      "description": "选择运行环境",
      "options": [
        "development",
        "staging",
        "production"
      ],
      "default": "development"
    },
    {
      "id": "port",
      "type": "promptString",
      "description": "输入端口号",
      "default": "3000"
    },
    {
      "id": "featureBranch",
      "type": "promptString",
      "description": "输入要切换的功能分支名",
      "default": "feature/new-feature"
    },
    {
      "id": "selectedFile",
      "type": "command",
      "command": "extension.commandvariable.file.content",
      "args": {
        "file": "${file}"
      }
    }
  ],
  "tasks": [
    {
      "label": "serve",
      "detail": "指定环境和端口启动服务",
      "type": "shell",
      "command": "pnpm",
      "args": [
        "dev",
        "--",
        "--mode",
        "${input:environment}",
        "--port",
        "${input:port}"
      ],
      "isBackground": true,
      "problemMatcher": []
    },
    {
      "label": "checkout-branch",
      "detail": "切换到指定功能分支",
      "type": "shell",
      "command": "git",
      "args": [
        "checkout",
        "-b",
        "${input:featureBranch}"
      ],
      "problemMatcher": []
    },
    {
      "label": "test-current-file",
      "detail": "测试当前打开的文件",
      "type": "shell",
      "command": "pnpm",
      "args": [
        "test",
        "--",
        "${relativeFile}"
      ],
      "problemMatcher": []
    },
    {
      "label": "build-info",
      "detail": "显示构建信息",
      "type": "shell",
      "command": "echo",
      "args": [
        "项目: ${workspaceFolderBasename}",
        "当前文件: ${file}",
        "行号: ${lineNumber}",
        "Node版本: ${env:NODE_VERSION}"
      ],
      "problemMatcher": []
    }
  ]
}
```

**执行流程示例**（运行 `serve` 任务）：

1. 弹出下拉框：选择 `development` / `staging` / `production`
2. 弹出输入框：输入端口号（默认 3000）
3. 执行命令：`pnpm dev -- --mode development --port 3000`

**变量组合技巧**：

```json
{
  "label": "copy-file",
  "type": "shell",
  "command": "cp",
  "args": [
    "${file}",
    "${fileDirname}/backup/${fileBasenameNoExtension}-copy${fileExtname}"
  ]
}
```

注意：`${fileExtname}` 并非内置变量，但可以通过 `${fileBasename}` 配合 shell 命令提取。实际使用中，灵活组合 `fileDirname`、`fileBasename`、`fileBasenameNoExtension` 已能覆盖绝大多数场景。

### 总结

- 预定义变量在任务执行时自动替换，`${workspaceFolder}` 和 `${file}` 最常用
- 输入变量通过 `inputs` 数组定义，支持 `promptString`、`pickString`、`command` 三种类型
- 输入变量引用语法：`${input:变量名}`
- 环境变量通过 `${env:VAR_NAME}` 引用
- 变量可组合使用，构建动态路径和命令
- 善用输入变量可以让单个任务适应多种运行场景

---

## 第9讲 复合任务与问题匹配器

### 概念

复合任务（Compound Tasks）允许将多个任务按顺序或并行组合执行，实现"一键完成多步骤操作"的能力。问题匹配器（Problem Matcher）则是任务系统的错误解析引擎，它从命令输出中提取错误/警告信息，转化为 VS Code 问题面板中的可点击条目。这两者是 `tasks.json` 进阶使用的核心，掌握后可以构建出媲美专业 IDE 的构建工作流。

### 原理

**复合任务**通过 `dependsOn` 字段定义任务依赖关系。VS Code 支持两种执行模式：

1. **顺序执行**（默认）：`dependsOn` 中的任务按数组顺序依次执行，前一个完成后才执行下一个
2. **并行执行**：设置 `dependsOrder: "parallel"` 后，所有依赖任务同时启动

复合任务可以嵌套，形成多层依赖树。但需注意避免循环依赖。

**问题匹配器**的工作原理：

问题匹配器是一组正则表达式规则，用于从命令的标准输出/错误输出中提取错误信息。提取的信息包括：文件路径、行号、列号、严重级别（错误/警告）、错误消息。VS Code 内置了常见工具的匹配器（如 `$tsc`、`$eslint-stylish`、`$gulp`），也支持自定义匹配器。

自定义问题匹配器的核心字段：

- `owner`：匹配器的唯一标识（用于区分不同工具的输出）
- `fileLocation`：文件路径的定位方式（`relative` 或 `absolute`）
- `pattern`：正则表达式模式及捕获组映射
- `background`：后台任务的开始/结束信号匹配

### 例子

一个完整的构建流水线，包含依赖安装、类型检查、测试、构建，并配自定义问题匹配器：

```json
{
  "version": "2.0.0",
  "tasks": [
    // ===== 基础任务 =====
    {
      "label": "install",
      "type": "shell",
      "command": "pnpm",
      "args": ["install"],
      "problemMatcher": []
    },
    {
      "label": "typecheck",
      "type": "shell",
      "command": "pnpm",
      "args": ["exec", "tsc", "--noEmit"],
      "problemMatcher": ["$tsc"],
      "group": "build"
    },
    {
      "label": "lint",
      "type": "shell",
      "command": "pnpm",
      "args": ["lint"],
      "problemMatcher": ["$eslint-stylish"]
    },
    {
      "label": "test",
      "type": "shell",
      "command": "pnpm",
      "args": ["test", "--", "--watch=false"],
      "group": {
        "kind": "test",
        "isDefault": true
      },
      "problemMatcher": []
    },
    {
      "label": "build",
      "type": "shell",
      "command": "pnpm",
      "args": ["build"],
      "group": {
        "kind": "build",
        "isDefault": true
      },
      "problemMatcher": ["$tsc"]
    },

    // ===== 复合任务：顺序执行 =====
    {
      "label": "pre-commit",
      "detail": "提交前检查（lint + typecheck + test）",
      "dependsOn": [
        "lint",
        "typecheck",
        "test"
      ],
      "dependsOrder": "sequence",
      "problemMatcher": []
    },

    // ===== 复合任务：完整流水线 =====
    {
      "label": "ci",
      "detail": "CI 流水线（install → lint → typecheck → test → build）",
      "dependsOn": [
        "install",
        "lint",
        "typecheck",
        "test",
        "build"
      ],
      "dependsOrder": "sequence",
      "problemMatcher": []
    },

    // ===== 复合任务：并行执行 =====
    {
      "label": "check-all",
      "detail": "并行运行 lint 和 typecheck",
      "dependsOn": [
        "lint",
        "typecheck"
      ],
      "dependsOrder": "parallel",
      "problemMatcher": []
    },

    // ===== 自定义问题匹配器：Python flake8 =====
    {
      "label": "flake8",
      "type": "shell",
      "command": "python",
      "args": ["-m", "flake8", "${file}"],
      "problemMatcher": {
        "owner": "flake8",
        "fileLocation": ["relative", "${workspaceFolder}"],
        "pattern": {
          "regexp": "^([^:]+):(\\d+):(\\d+):\\s+(\\w+)\\s+(\\w+)\\s+(.*)$",
          "file": 1,
          "line": 2,
          "column": 3,
          "severity": 4,
          "code": 5,
          "message": 6
        }
      }
    },

    // ===== 自定义问题匹配器：Go build =====
    {
      "label": "go-build",
      "type": "shell",
      "command": "go",
      "args": ["build", "./..."],
      "problemMatcher": {
        "owner": "go",
        "fileLocation": "relative",
        "pattern": {
          "regexp": "^([^:]+):(\\d+):(?:(\\d+):)?\\s+(.*)$",
          "file": 1,
          "line": 2,
          "column": 3,
          "message": 4
        }
      }
    },

    // ===== 后台任务匹配器：开发服务器 =====
    {
      "label": "dev-server",
      "type": "shell",
      "command": "pnpm",
      "args": ["dev"],
      "isBackground": true,
      "problemMatcher": {
        "owner": "vite",
        "pattern": {
          "regexp": "^([^:]+):(\\d+):(\\d+):\\s+(.*)$",
          "file": 1,
          "line": 2,
          "column": 3,
          "message": 4
        },
        "background": {
          "activeOnStart": true,
          "beginsPattern": "VITE v",
          "endsPattern": "ready in"
        }
      }
    }
  ]
}
```

**复合任务执行逻辑**：

- `pre-commit` 任务：依次执行 `lint` → `typecheck` → `test`，任一步骤失败则停止
- `ci` 任务：完整流水线，适合 CI/CD 场景
- `check-all` 任务：`lint` 和 `typecheck` 同时启动，提升速度

**自定义问题匹配器解析**（以 flake8 为例）：

flake8 的输出格式为 `file:line:col: CODE message`，如 `src/main.py:10:5: E501 line too long`。正则表达式 `^([^:]+):(\\d+):(\\d+):\\s+(\\w+)\\s+(\\w+)\\s+(.*)$` 将其分解为：文件名、行号、列号、严重级别、错误码、消息。VS Code 据此在问题面板生成可点击条目，点击后跳转到对应代码位置。

**后台任务匹配器**：`background.beginsPattern` 和 `endsPattern` 告诉 VS Code 何时认为后台任务已"启动完成"。这对于调试配置中依赖开发服务器的场景至关重要——VS Code 会等待 `endsPattern` 匹配后才认为服务就绪。

### 总结

- 复合任务通过 `dependsOn` 组合多个任务，`dependsOrder` 控制顺序/并行
- 顺序执行适合有依赖关系的流水线，并行执行适合独立的检查任务
- 问题匹配器用正则表达式从命令输出提取错误信息
- 内置匹配器（`$tsc`、`$eslint-stylish`）覆盖常见工具，复杂场景需自定义
- 后台任务需配置 `background.beginsPattern`/`endsPattern` 标识启动完成
- 自定义匹配器的 `pattern` 中通过数字索引映射正则捕获组到字段

---

# 第四章 调试配置

## 第10讲 launch.json 调试基础

### 概念

`launch.json` 是 VS Code 调试系统的配置文件，用于定义如何启动和调试你的应用程序。通过这个文件，你可以配置调试器类型、启动方式、运行参数、环境变量、断点行为等。无论是调试 Node.js 服务、Python 脚本、浏览器前端还是 C++ 程序，`launch.json` 都是连接 VS Code 调试 UI 与底层调试引擎的核心枢纽。

与 `tasks.json` 不同，`launch.json` 关注的是**交互式调试**——设置断点、单步执行、查看变量、调用栈分析，而非简单的命令执行。一个配置良好的 `launch.json` 可以让你用 `F5` 一键启动调试，无需记住复杂的调试命令。

### 原理

VS Code 的调试系统基于**调试适配器协议**（Debug Adapter Protocol, DAP）。每种语言/运行时有一个对应的调试适配器（如 Node.js 的内置调试器、Python 的 `debugpy`、Go 的 `dlv`），它们将 VS Code 的调试指令翻译为底层调试器的操作。

`launch.json` 的核心概念：

1. **配置（Configuration）**：一个完整的调试方案，包含 `name`、`type`、`request` 等字段
2. **请求类型（request）**：
   - `launch`：启动新进程并调试
   - `attach`：附加到已运行的进程进行调试
3. **调试器类型（type）**：如 `node`、`python`、`go`、`chrome`、`cppdbg` 等
4. **preLaunchTask**：调试启动前自动执行的任务（通常是编译）

调试的工作流程：用户按 `F5` → VS Code 读取 `launch.json` 中选中的配置 → 执行 `preLaunchTask`（如有）→ 启动/附加调试器 → 调试器在断点处暂停 → 用户通过调试 UI 进行单步、查看变量等操作。

### 例子

一个 Node.js + TypeScript 项目的 `launch.json`，涵盖多种调试场景：

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "调试当前 TS 文件",
      "type": "node",
      "request": "launch",
      "program": "${file}",
      "preLaunchTask": "tsc: build - tsconfig.json",
      "outFiles": ["${workspaceFolder}/dist/**/*.js"],
      "cwd": "${workspaceFolder}",
      "console": "integratedTerminal",
      "skipFiles": ["<node_internals>/**"]
    },
    {
      "name": "调试 npm start",
      "type": "node",
      "request": "launch",
      "runtimeExecutable": "npm",
      "runtimeArgs": ["run", "start", "--", "--inspect"],
      "port": 9229,
      "skipFiles": ["<node_internals>/**"],
      "console": "integratedTerminal"
    },
    {
      "name": "附加到 Node 进程",
      "type": "node",
      "request": "attach",
      "processId": "${command:PickProcess}",
      "restart": true,
      "skipFiles": ["<node_internals>/**"]
    },
    {
      "name": "调试 Jest 测试",
      "type": "node",
      "request": "launch",
      "program": "${workspaceFolder}/node_modules/.bin/jest",
      "args": [
        "${fileBasenameNoExtension}",
        "--config",
        "jest.config.js",
        "--runInBand"
      ],
      "cwd": "${workspaceFolder}",
      "console": "integratedTerminal",
      "internalConsoleOptions": "neverOpen"
    },
    {
      "name": "调试 Mocha 测试",
      "type": "node",
      "request": "launch",
      "program": "${workspaceFolder}/node_modules/.bin/mocha",
      "args": [
        "--inspect-brk",
        "${file}",
        "--reporter",
        "spec"
      ],
      "port": 9229,
      "console": "integratedTerminal"
    }
  ]
}
```

**关键字段解析**：

- `program`：要调试的入口文件，`${file}` 表示当前打开的文件
- `preLaunchTask`：调试前执行的任务，这里用 VS Code 内置的 TypeScript 编译任务
- `outFiles`：告诉调试器编译后的 JS 文件位置，用于 source map 定位
- `skipFiles: ["<node_internals>/**"]`：单步调试时跳过 Node.js 内部文件，避免陷入源码
- `console: "integratedTerminal"`：使用集成终端而非调试控制台，支持标准输入
- `processId: "${command:PickProcess}"`：附加调试时弹出进程选择器
- `restart: true`：进程崩溃后自动重新附加，适合调试不稳定的服务

**调试 UI 核心操作**：
- `F5`：启动调试 / 继续
- `F9`：切换断点
- `F10`：单步跳过（Step Over）
- `F11`：单步进入（Step Into）
- `Shift+F11`：单步跳出（Step Out）
- `Shift+F5`：停止调试

### 总结

- `launch.json` 定义调试方案，核心字段是 `type`、`request`、`name`
- 两种请求类型：`launch`（启动新进程）和 `attach`（附加已有进程）
- `preLaunchTask` 实现编译后调试的自动化
- `outFiles` + source map 让调试器能定位到源码
- `skipFiles` 跳过框架/运行时内部文件，聚焦业务代码
- `console: "integratedTerminal"` 是 Node.js 调试的推荐设置

---

## 第11讲 调试变量与复合配置

### 概念

调试变量（Debug Variables）是 `launch.json` 中的动态占位符，与 `tasks.json` 中的变量系统类似但有所扩展。复合调试配置（Compound Configurations）允许同时启动多个调试配置，适用于前后端联调、微服务调试等复杂场景。此外，`launch.json` 也支持 `inputs` 输入变量，实现交互式调试参数配置。

### 原理

**调试变量**复用了 `tasks.json` 的变量体系（`${workspaceFolder}`、`${file}` 等），同时增加了调试特有的变量：

- `${command:PickProcess}`：弹出进程选择器，返回选中的进程 ID
- `${command:AskForProgram}`：弹出输入框，让用户输入程序路径

**复合配置**通过 `compounds` 数组定义，它将多个 `configurations` 组合在一起。启动复合配置时，VS Code 会同时启动所有包含的配置，并在调试 UI 中显示多个调试会话。你可以通过调试工具栏的下拉菜单切换当前活跃的调试会话。

复合配置的典型应用场景：
- 前端（Chrome）+ 后端（Node.js）同时调试
- 微服务架构中多个服务同时启动调试
- 客户端 + 服务端 + 数据库代理的组合调试

**启动行为控制**：`compounds` 中的 `stopAll` 字段控制是否在一个配置停止时停止所有配置。默认为 `false`，设置为 `true` 后任一调试会话结束都会终止所有会话。

### 例子

一个全栈项目的复合调试配置，包含前端、后端、数据库：

```json
{
  "version": "0.2.0",
  "inputs": [
    {
      "id": "envFile",
      "type": "pickString",
      "description": "选择环境配置文件",
      "options": [
        ".env.development",
        ".env.staging",
        ".env.local"
      ],
      "default": ".env.development"
    },
    {
      "id": "debugPort",
      "type": "promptString",
      "description": "输入调试端口",
      "default": "9229"
    }
  ],
  "configurations": [
    {
      "name": "后端 API 服务",
      "type": "node",
      "request": "launch",
      "runtimeExecutable": "npm",
      "runtimeArgs": ["run", "dev:server"],
      "envFile": "${workspaceFolder}/${input:envFile}",
      "port": "${input:debugPort}",
      "skipFiles": ["<node_internals>/**"],
      "console": "integratedTerminal",
      "serverReadyAction": {
        "action": "openExternally",
        "pattern": "Server ready on (https?://\\S+)",
        "uriFormat": "%s/api/health"
      }
    },
    {
      "name": "前端开发服务器",
      "type": "node",
      "request": "launch",
      "runtimeExecutable": "npm",
      "runtimeArgs": ["run", "dev:client"],
      "serverReadyAction": {
        "action": "debugWithEdge",
        "pattern": "Local: (https?://\\S+)",
        "webRoot": "${workspaceFolder}/src"
      },
      "console": "integratedTerminal"
    },
    {
      "name": "Chrome 调试前端",
      "type": "chrome",
      "request": "launch",
      "url": "http://localhost:5173",
      "webRoot": "${workspaceFolder}/src",
      "sourceMaps": true,
      "sourceMapPathOverrides": {
        "webpack:///./src/*": "${webRoot}/*"
      },
      "runtimeArgs": ["--auto-open-devtools-for-tabs"]
    },
    {
      "name": "附加到已运行的 API",
      "type": "node",
      "request": "attach",
      "processId": "${command:PickProcess}",
      "restart": true,
      "skipFiles": ["<node_internals>/**"]
    },
    {
      "name": "调试当前测试文件",
      "type": "node",
      "request": "launch",
      "program": "${workspaceFolder}/node_modules/vitest/vitest.mjs",
      "args": ["run", "${file}"],
      "cwd": "${workspaceFolder}",
      "console": "integratedTerminal"
    }
  ],
  "compounds": [
    {
      "name": "全栈调试（前端+后端）",
      "configurations": ["后端 API 服务", "前端开发服务器"],
      "stopAll": true
    },
    {
      "name": "全栈调试（后端+Chrome）",
      "configurations": ["后端 API 服务", "Chrome 调试前端"],
      "stopAll": false
    }
  ]
}
```

**核心特性解析**：

**1. `serverReadyAction` —— 服务就绪后自动操作**

这是 VS Code 调试 Node.js Web 服务的利器。`pattern` 字段用正则表达式匹配服务输出中的 URL，匹配成功后执行 `action` 指定的操作：
- `openExternally`：用系统默认浏览器打开
- `debugWithChrome` / `debugWithEdge`：用浏览器打开并附加调试器
- `openWithDebugEnabled`：用内置浏览器打开

例如后端配置中，当输出匹配 `Server ready on http://localhost:3000` 时，自动打开 `http://localhost:3000/api/health`。

**2. 复合配置的执行**

选择"全栈调试（前端+后端）"后，VS Code 同时启动后端 API 和前端开发服务器，调试工具栏显示两个会话。`stopAll: true` 意味着停止任一会话都会终止全部。

**3. 输入变量的应用**

后端配置中的 `envFile` 和 `debugPort` 使用了输入变量，启动调试时会弹出选择/输入框，实现灵活的环境切换。

**4. Chrome 调试配置**

`sourceMapPathOverrides` 将 webpack 打包后的路径映射回源码路径，确保断点能正确命中。`webRoot` 指定前端源码根目录。

### 总结

- 调试变量复用任务变量体系，`${command:PickProcess}` 是附加调试的常用变量
- 复合配置通过 `compounds` 数组定义，可同时启动多个调试会话
- `stopAll` 控制是否联动停止所有会话
- `serverReadyAction` 是 Web 服务调试的关键，自动打开浏览器或附加浏览器调试
- `sourceMapPathOverrides` 解决打包后断点定位问题
- 输入变量让调试配置支持运行时参数选择

---

## 第12讲 常见语言调试实战

### 概念

不同编程语言的调试配置各有特点，需要根据语言生态选择合适的调试器类型和配置策略。本讲将覆盖 Python、Go、Rust、C++ 四种常见后端/系统语言的调试配置实战，每种语言都有其独特的调试器、构建流程和注意事项。掌握这些配置后，你可以为任何技术栈的项目编写专业的调试方案。

### 原理

每种语言的调试依赖不同的调试引擎：

| 语言 | 调试器 | VS Code 扩展 | type 值 |
|------|--------|-------------|---------|
| Python | debugpy | Python (Pylance) | `python` |
| Go | Delve (dlv) | Go | `go` |
| Rust | lldb / gdb | CodeLLDB / rust-analyzer | `lldb` / `cppdbg` |
| C/C++ | gdb / lldb | C/C++ (cpptools) | `cppdbg` |

调试配置的关键差异在于：
- **编译型语言**（Go、Rust、C++）通常需要先编译再调试，`preLaunchTask` 不可或缺
- **解释型语言**（Python）直接调试源码，配置更简洁
- **调试器特性**：Go 的 Delve 支持 goroutine 调试，Rust 的 CodeLLDB 支持泛型类型显示

### 例子

**1. Python 调试配置**

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Python: 当前文件",
      "type": "python",
      "request": "launch",
      "program": "${file}",
      "console": "integratedTerminal",
      "justMyCode": true,
      "python": "${command:python.interpreterPath}",
      "env": {
        "PYTHONPATH": "${workspaceFolder}/src"
      },
      "envFile": "${workspaceFolder}/.env"
    },
    {
      "name": "Python: FastAPI",
      "type": "python",
      "request": "launch",
      "module": "uvicorn",
      "args": ["main:app", "--reload", "--port", "8000"],
      "jinja": true,
      "console": "integratedTerminal",
      "justMyCode": true
    },
    {
      "name": "Python: Django",
      "type": "python",
      "request": "launch",
      "program": "${workspaceFolder}/manage.py",
      "args": ["runserver", "--noreload"],
      "django": true,
      "console": "integratedTerminal",
      "justMyCode": true
    },
    {
      "name": "Python: pytest",
      "type": "python",
      "request": "launch",
      "module": "pytest",
      "args": ["${file}", "-v", "-s"],
      "console": "integratedTerminal",
      "justMyCode": false
    },
    {
      "name": "Python: 附加到进程",
      "type": "python",
      "request": "attach",
      "connect": {
        "host": "localhost",
        "port": 5678
      },
      "pathMappings": [
        {
          "localRoot": "${workspaceFolder}",
          "remoteRoot": "/app"
        }
      ]
    }
  ]
}
```

Python 调试要点：`justMyCode: true` 只调试用户代码，跳过第三方库；`module` 字段用于以模块方式运行（如 `uvicorn`、`pytest`）；`django: true` 和 `jinja: true` 启用模板调试；`pathMappings` 用于远程/容器调试的路径映射。

**2. Go 调试配置**

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Go: 启动包",
      "type": "go",
      "request": "launch",
      "program": "${workspaceFolder}",
      "mode": "auto",
      "cwd": "${workspaceFolder}",
      "env": {
        "GO_ENV": "development"
      },
      "envFile": "${workspaceFolder}/.env"
    },
    {
      "name": "Go: 调试当前文件",
      "type": "go",
      "request": "launch",
      "program": "${fileDirname}",
      "mode": "debug"
    },
    {
      "name": "Go: 调试测试",
      "type": "go",
      "request": "launch",
      "mode": "test",
      "program": "${fileDirname}"
    },
    {
      "name": "Go: 附加到进程",
      "type": "go",
      "request": "attach",
      "processId": "${command:PickProcess}"
    },
    {
      "name": "Go: 远程调试",
      "type": "go",
      "request": "attach",
      "mode": "remote",
      "host": "127.0.0.1",
      "port": 2345,
      "substitutePath": [
        {
          "from": "${workspaceFolder}",
          "to": "/go/src/app"
        }
      ]
    }
  ]
}
```

Go 调试要点：`mode` 字段决定调试模式——`auto` 自动选择，`debug` 编译并调试，`test` 调试测试，`remote` 远程调试；`substitutePath` 用于远程/容器调试的路径替换；Delve 支持 goroutine 调试，在调用栈面板可查看所有 goroutine。

**3. Rust 调试配置（使用 CodeLLDB）**

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Rust: 调试",
      "type": "lldb",
      "request": "launch",
      "program": "${workspaceFolder}/target/debug/${workspaceFolderBasename}",
      "args": [],
      "cwd": "${workspaceFolder}",
      "preLaunchTask": "cargo build",
      "sourceLanguages": ["rust"]
    },
    {
      "name": "Rust: 调试单元测试",
      "type": "lldb",
      "request": "launch",
      "program": "${workspaceFolder}/target/debug/deps/${workspaceFolderBasename}-<hash>",
      "preLaunchTask": "cargo test --no-run",
      "cwd": "${workspaceFolder}",
      "sourceLanguages": ["rust"]
    },
    {
      "name": "Rust: 附加调试",
      "type": "lldb",
      "request": "attach",
      "program": "${workspaceFolder}/target/debug/${workspaceFolderBasename}",
      "pid": "${command:pickProcess}"
    }
  ]
}
```

Rust 调试要点：`preLaunchTask` 必须先执行 `cargo build`；`program` 指向编译后的二进制文件；`sourceLanguages: ["rust"]` 启用 Rust 源码级调试；测试调试需要先 `cargo test --no-run` 编译测试二进制。

**4. C/C++ 调试配置（使用 cpptools）**

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "C++: 调试",
      "type": "cppdbg",
      "request": "launch",
      "program": "${workspaceFolder}/build/bin/app",
      "args": ["--config", "config.yaml"],
      "stopAtEntry": false,
      "cwd": "${workspaceFolder}",
      "environment": [
        {"name": "LD_LIBRARY_PATH", "value": "${workspaceFolder}/build/lib"}
      ],
      "externalConsole": false,
      "MIMode": "gdb",
      "setupCommands": [
        {
          "description": "为 gdb 启用整齐打印",
          "text": "-enable-pretty-printing",
          "ignoreFailures": true
        },
        {
          "description": "跳过标准库",
          "text": "-interpreter-exec console \"skip -rfile /usr/.*\"",
          "ignoreFailures": true
        }
      ],
      "preLaunchTask": "cmake build"
    },
    {
      "name": "C++: 附加到进程",
      "type": "cppdbg",
      "request": "attach",
      "program": "${workspaceFolder}/build/bin/app",
      "processId": "${command:pickProcess}",
      "MIMode": "gdb"
    }
  ]
}
```

C++ 调试要点：`MIMode` 指定调试器（`gdb` 或 `lldb`）；`setupCommands` 在调试器启动时执行初始化命令（如启用 pretty-printing）；`stopAtEntry` 控制是否在 main 函数入口暂停；`environment` 数组设置环境变量。

### 总结

- 解释型语言（Python）配置简洁，关注 `module`、`justMyCode`、`envFile`
- 编译型语言（Go、Rust、C++）必须配置 `preLaunchTask` 先编译
- Go 的 `mode` 字段决定调试模式，`substitutePath` 用于远程调试
- Rust 推荐使用 CodeLLDB，`sourceLanguages` 启用源码级调试
- C++ 通过 `MIMode` 选择 gdb/lldb，`setupCommands` 初始化调试器
- 框架特定调试（Django、FastAPI）需启用对应标志（`django`、`jinja`）
- 远程/容器调试通过 `pathMappings` 或 `substitutePath` 解决路径差异

---

# 第五章 代码片段与快捷键

## 第13讲 自定义代码片段

### 概念

代码片段（Snippets）是预定义的代码模板，通过简短的触发词快速插入大段代码。VS Code 支持三种级别的代码片段：全局片段、语言片段和工作区片段。工作区片段存放在 `.vscode/*.code-snippets` 文件中，仅对当前项目生效，非常适合为团队定义项目专属的代码模板（如组件模板、API 请求函数、测试用例骨架等）。

### 原理

代码片段文件采用 JSONC 格式，每个片段是一个键值对，键为片段名称，值为片段定义对象。核心字段包括：

- `prefix`：触发词，输入后自动提示
- `body`：代码模板，字符串数组，每行一个元素
- `description`：片段描述
- `scope`：适用的语言（仅全局片段文件需要，工作区片段可省略）

**占位符语法**（Tab Stops）是代码片段的精髓：

| 语法 | 含义 |
|------|------|
| `$1`, `$2`, `$3`... | 按序跳转的光标位置，`Tab` 切换 |
| `$0` | 最终光标位置 |
| `${1:默认值}` | 带默认值的占位符 |
| `${1|选项1,选项2,选项3|}` | 下拉选择占位符 |
| `${1:${2:嵌套}}` | 嵌套占位符 |
| `$name` 或 `${name:default}` | 命名变量 |
| `$BLOCK_COMMENT_START` | 块注释起始（语言相关） |
| `$TM_FILENAME` | 当前文件名 |
| `$TM_FILENAME_BASE` | 文件名（无扩展名） |
| `$TM_DIRECTORY` | 当前文件目录 |
| `$CURRENT_YEAR` | 当前年份 |

**变量转换**：命名变量支持正则替换语法 `${var/正则/替换/标志}`，可以对变量值进行加工。

### 例子

一个前端项目的 `.vscode/react.code-snippets`，包含组件、Hook、测试等模板：

```json
{
  "React 函数组件": {
    "prefix": "rfc",
    "description": "React 函数组件（带 Props 类型）",
    "body": [
      "import React from 'react';",
      "",
      "interface ${1:${TM_FILENAME_BASE}}Props {",
      "  ${2:children}: ${3:React.ReactNode};",
      "}",
      "",
      "export const ${1:${TM_FILENAME_BASE}} = ({ ${2:children} }: ${1:${TM_FILENAME_BASE}}Props) => {",
      "  return (",
      "    <div>",
      "      ${4:${2:children}}",
      "    </div>",
      "  );",
      "};",
      ""
    ]
  },

  "React 带 Children 的组件": {
    "prefix": "rfcc",
    "description": "React 函数组件（带 children prop）",
    "body": [
      "import React, { ReactNode } from 'react';",
      "",
      "interface ${1:${TM_FILENAME_BASE}}Props {",
      "  children: ReactNode;",
      "  ${2}",
      "}",
      "",
      "export const ${1:${TM_FILENAME_BASE}} = ({ children, ...props }: ${1:${TM_FILENAME_BASE}}Props) => {",
      "  return (",
      "    <div {...props}>",
      "      {children}",
      "    </div>",
      "  );",
      "};",
      ""
    ]
  },

  "自定义 Hook": {
    "prefix": "usehook",
    "description": "自定义 React Hook 模板",
    "body": [
      "import { useState, useEffect, useCallback } from 'react';",
      "",
      "export function ${1:useHookName}(${2:params}) {",
      "  const [state, setState] = useState<${3:type}>(${4:initialValue});",
      "",
      "  useEffect(() => {",
      "    ${5:// 副作用逻辑}",
      "  }, [${6:deps}]);",
      "",
      "  const handleAction = useCallback(() => {",
      "    ${7:// 处理函数}",
      "  }, []);",
      "",
      "  return {",
      "    state,",
      "    handleAction,",
      "  };",
      "}",
      ""
    ]
  },

  "API 请求函数": {
    "prefix": "apifn",
    "description": "API 请求函数（带错误处理）",
    "body": [
      "import { request } from '@/utils/request';",
      "",
      "export interface ${1:ApiName}Params {",
      "  ${2:key}: ${3:string};",
      "}",
      "",
      "export interface ${1:ApiName}Response {",
      "  ${4:code}: number;",
      "  ${5:data}: ${6:any};",
      "  message: string;",
      "}",
      "",
      "export async function ${1:ApiName}(params: ${1:ApiName}Params): Promise<${1:ApiName}Response> {",
      "  try {",
      "    const response = await request.${7|get,post,put,delete|}('/api/${1/(.*)/${1:/downcase}/}', params);",
      "    return response.data;",
      "  } catch (error) {",
      "    console.error('${1:ApiName} error:', error);",
      "    throw error;",
      "  }",
      "}",
      ""
    ]
  },

  "测试用例": {
    "prefix": "testcase",
    "description": "测试用例模板",
    "body": [
      "import { describe, it, expect, beforeEach, vi } from 'vitest';",
      "",
      "describe('${1:模块名}', () => {",
      "  beforeEach(() => {",
      "    ${2:// 前置准备}",
      "  });",
      "",
      "  it('${3:应该...}', async () => {",
      "    // Arrange",
      "    ${4:// 准备测试数据}",
      "",
      "    // Act",
      "    ${5:// 执行测试操作}",
      "",
      "    // Assert",
      "    expect(${6:result}).toBe(${7:expected});",
      "  });",
      "});",
      ""
    ]
  },

  "Console 日志": {
    "prefix": "clg",
    "description": "带标签的 console.log",
    "body": [
      "console.log('${1:label}:', ${2:value});"
    ]
  },

  "条件渲染": {
    "prefix": "ifrender",
    "description": "React 条件渲染",
    "body": [
      "{${1:condition} && (",
      "  <${2:Component}>",
      "    ${3:content}",
      "  </${2:Component}>",
      ")}"
    ]
  }
}
```

**占位符进阶用法解析**：

1. **`${1:${TM_FILENAME_BASE}}`**：第一个光标位置，默认值为当前文件名（不含扩展名）。新建 `Button.tsx` 时自动填充 `Button`，省去手动输入。

2. **`${7|get,post,put,delete|}`**：下拉选择占位符，按 `Tab` 跳到此处时弹出选项列表，用方向键选择 HTTP 方法。

3. **`${1/(.*)/${1:/downcase}/}`**：变量转换，将第一个占位符的值转为小写。在 API 函数中，用户输入 `GetUser` 后，URL 路径自动变为 `getuser`。

4. **嵌套占位符 `${1:${2:嵌套}}`**：先编辑外层 `$1`，如果接受默认值则 `Tab` 进入内层 `$2` 编辑。

**使用方式**：在编辑器中输入 `prefix`（如 `rfc`），代码提示会显示对应片段，按 `Tab` 展开模板，然后用 `Tab` 在占位符之间跳转。

### 总结

- 工作区片段存放在 `.vscode/*.code-snippets`，仅对当前项目生效
- 核心字段：`prefix`（触发词）、`body`（模板）、`description`（描述）
- 占位符 `$1`、`$2` 控制光标跳转顺序，`$0` 是最终位置
- `${1:默认值}` 提供默认值，`${1|a,b,c|}` 提供下拉选择
- 变量如 `$TM_FILENAME_BASE` 自动填充文件名，提升效率
- 变量转换 `${var/正则/替换/标志}` 可对值进行加工（如大小写转换）
- 为团队定义统一的组件、API、测试模板，能显著提升代码一致性

---

## 第14讲 快捷键定制

### 概念

快捷键定制是提升编码效率的重要手段。VS Code 的快捷键配置分为两个级别：用户级（全局）和工作区级。工作区级快捷键存放在 `.vscode/keybindings.json` 中，允许为特定项目定义专属的快捷键绑定。通过合理定制快捷键，你可以将常用操作映射到顺手的按键组合，减少鼠标操作，实现"键盘流"编码。

### 原理

VS Code 的快捷键系统基于**命令（Command）** 模型。每个快捷键绑定将一个按键组合映射到一个 VS Code 命令（如 `workbench.action.terminal.focus` 切换到终端）。快捷键配置是一个数组，每个元素定义一个绑定规则。

核心字段：

- `key`：按键组合，如 `ctrl+shift+t`、`cmd+k cmd+c`
- `command`：要执行的命令 ID
- `when`：**上下文条件**，控制快捷键在何时生效（这是工作区快捷键的核心）
- `args`：命令参数（部分命令需要）
- `mac`/`win`/`linux`：平台特定的按键覆盖

**`when` 子句**是快捷键系统的精髓。它允许你根据当前上下文（编辑器语言、焦点位置、选择状态等）激活或禁用快捷键。例如 `editorLangId == 'python' && editorTextFocus` 表示仅在 Python 文件编辑器获得焦点时生效。常用的上下文变量包括：

| 变量 | 含义 |
|------|------|
| `editorTextFocus` | 编辑器有焦点且在编辑文本 |
| `editorLangId` | 当前文件语言 ID |
| `resourceFilename` | 当前文件名 |
| `terminalFocus` | 终端有焦点 |
| `inDebugMode` | 正在调试 |
| `editorHasSelection` | 编辑器中有选中文本 |

### 例子

一个前端项目的工作区快捷键配置 `.vscode/keybindings.json`：

```json
[
  // ===== 终端快捷键 =====
  {
    "key": "ctrl+`",
    "command": "workbench.action.terminal.toggleTerminal",
    "when": "terminalFocus"
  },
  {
    "key": "ctrl+shift+`",
    "command": "workbench.action.terminal.new",
    "when": "terminalFocus"
  },
  {
    "key": "ctrl+shift+c",
    "command": "workbench.action.terminal.kill",
    "when": "terminalFocus"
  },

  // ===== 编辑器导航 =====
  {
    "key": "alt+left",
    "command": "workbench.action.navigateBack",
    "when": "canNavigateBack"
  },
  {
    "key": "alt+right",
    "command": "workbench.action.navigateForward",
    "when": "canNavigateForward"
  },
  {
    "key": "ctrl+shift+o",
    "command": "workbench.action.quickOpen",
    "when": "!inQuickOpen"
  },

  // ===== 文件操作 =====
  {
    "key": "ctrl+n",
    "command": "explorer.newFile",
    "when": "explorerViewletFocus"
  },
  {
    "key": "ctrl+shift+n",
    "command": "explorer.newFolder",
    "when": "explorerViewletFocus"
  },

  // ===== 代码操作 =====
  {
    "key": "ctrl+shift+i",
    "command": "editor.action.formatDocument",
    "when": "editorTextFocus && !editorReadonly"
  },
  {
    "key": "ctrl+i",
    "command": "editor.action.formatSelection",
    "when": "editorTextFocus && editorHasSelection && !editorReadonly"
  },
  {
    "key": "ctrl+d",
    "command": "editor.action.copyLinesDownAction",
    "when": "editorTextFocus && !editorHasSelection"
  },
  {
    "key": "ctrl+shift+d",
    "command": "editor.action.duplicateSelection",
    "when": "editorTextFocus && editorHasSelection"
  },

  // ===== 语言特定快捷键 =====
  {
    "key": "ctrl+enter",
    "command": "python.execSelectionInTerminal",
    "when": "editorTextFocus && editorLangId == 'python'"
  },
  {
    "key": "ctrl+shift+r",
    "command": "python.startREPL",
    "when": "editorTextFocus && editorLangId == 'python'"
  },
  {
    "key": "f5",
    "command": "go.debug.start",
    "when": "editorTextFocus && editorLangId == 'go' && !inDebugMode"
  },

  // ===== 任务快捷键 =====
  {
    "key": "ctrl+alt+t",
    "command": "workbench.action.tasks.runTask",
    "when": "editorTextFocus || terminalFocus"
  },
  {
    "key": "ctrl+alt+b",
    "command": "workbench.action.tasks.build",
    "when": "editorTextFocus"
  },

  // ===== 调试快捷键 =====
  {
    "key": "f9",
    "command": "editor.debug.action.toggleBreakpoint",
    "when": "editorTextFocus && debuggersAvailable"
  },
  {
    "key": "ctrl+f5",
    "command": "workbench.action.debug.start",
    "when": "!inDebugMode && debuggersAvailable"
  },

  // ===== Git 快捷键 =====
  {
    "key": "ctrl+shift+g",
    "command": "workbench.view.scm",
    "when": "!scmProvider"
  },
  {
    "key": "ctrl+enter",
    "command": "git.commit",
    "when": "scmRepository && scmInputIsInFocus"
  },

  // ===== 禁用某些默认快捷键 =====
  {
    "key": "ctrl+w",
    "command": "-workbench.action.closeWindow",
    "when": "!editorIsOpen"
  },

  // ===== 带参数的命令 =====
  {
    "key": "ctrl+alt+l",
    "command": "workbench.action.terminal.sendSequence",
    "when": "terminalFocus",
    "args": {
      "text": "pnpm lint\u000D"
    }
  },
  {
    "key": "ctrl+alt+s",
    "command": "workbench.action.terminal.sendSequence",
    "when": "terminalFocus",
    "args": {
      "text": "pnpm dev\u000D"
    }
  }
]
```

**关键技巧解析**：

**1. `when` 子句的运用**

- `editorLangId == 'python'`：仅在 Python 文件中生效，实现语言特定快捷键
- `terminalFocus`：仅在终端有焦点时生效，避免与编辑器快捷键冲突
- `!inDebugMode`：仅在非调试状态下生效，避免与调试快捷键冲突
- `editorHasSelection`：仅在选中文本时生效（如格式化选中代码）

**2. 禁用默认快捷键**

在 `command` 前加 `-`（减号）可以禁用某个默认绑定。例如 `"command": "-workbench.action.closeWindow"` 禁用了 `Ctrl+W` 关闭窗口的行为（保留关闭编辑器的功能）。

**3. 带参数的命令**

`workbench.action.terminal.sendSequence` 命令配合 `args.text` 可以向终端发送文本序列。`\u000D` 是回车符的 Unicode 表示，相当于按回车键。这让你可以用快捷键在终端中执行预设命令。

**4. 链式快捷键**

`"key": "cmd+k cmd+c"` 定义了一个两步快捷键：先按 `Cmd+K`，再按 `Cmd+C`。这种风格借鉴自 Vim 和 Emacs，适合快捷键数量较多时避免冲突。

**5. 查看命令 ID**

要查找可用的命令 ID，打开快捷键设置界面（`Ctrl+K Ctrl+S`），点击右侧的齿轮图标可以查看命令的完整 ID。也可以通过命令面板执行 `Developer: Toggle Keyboard Shortcuts Troubleshooting`，在控制台中查看按键事件和命令触发情况。

### 总结

- 工作区快捷键存放在 `.vscode/keybindings.json`，仅对当前项目生效
- 核心字段：`key`（按键）、`command`（命令）、`when`（上下文条件）
- `when` 子句是关键，通过上下文变量实现条件激活
- `command` 前加 `-` 可禁用默认快捷键
- `args` 字段为命令提供参数，如向终端发送文本序列
- 语言特定快捷键通过 `editorLangId` 条件实现
- 善用快捷键设置界面查看命令 ID 和调试快捷键冲突

---

# 第六章 团队协作与扩展

## 第15讲 扩展推荐与团队共享

### 概念

`extensions.json` 是 `.vscode` 目录中用于**推荐扩展**的配置文件。它本身不会自动安装扩展，而是当团队成员打开项目时，VS Code 会检测到推荐扩展未安装，并弹出提示询问是否安装。这是实现团队开发环境统一的重要手段——确保所有人使用相同的代码格式化工具、Lint 工具、语言服务等，避免因工具差异导致的代码风格不一致问题。

除了推荐扩展，VS Code 还支持通过 `settings.json` 中的 `extensions.recommendations` 和 `extensions.unwantedRecommendations` 字段进行更精细的控制。推荐扩展是团队协作的起点，配合统一的配置，能显著降低"在我机器上能跑"的问题。

### 原理

`extensions.json` 的工作机制：

1. **检测阶段**：VS Code 启动时读取 `.vscode/extensions.json`，获取推荐扩展列表
2. **比对阶段**：将推荐列表与已安装扩展对比，找出缺失的扩展
3. **提示阶段**：如果有缺失的推荐扩展，在右下角弹出通知"Workspace has recommended extensions"（工作区有推荐扩展）
4. **安装阶段**：用户点击"Install All"一键安装所有推荐扩展

扩展 ID 的格式为 `publisher.extension-name`，如 `esbenp.prettier-vscode`。你可以在 VS Code 扩展市场页面或扩展详情中找到扩展 ID。

**推荐 vs 强制**：`extensions.json` 是推荐而非强制。如果需要更强的约束力，可以结合项目文档（README）和 CI/CD 检查来确保团队成员安装必要扩展。一些团队还会在 `package.json` 的 `devDependencies` 中包含 CLI 版本的工具（如 `prettier`、`eslint`），确保即使不安装 VS Code 扩展，命令行也能执行格式化和检查。

### 例子

一个全栈项目的 `.vscode/extensions.json`，涵盖前端、后端、通用工具：

```json
{
  "recommendations": [
    // ===== 代码格式化 =====
    "esbenp.prettier-vscode",

    // ===== Lint 工具 =====
    "dbaeumer.vscode-eslint",
    "ms-python.flake8",

    // ===== 语言支持 =====
    "ms-python.python",
    "ms-python.vscode-pylance",
    "ms-vscode.vscode-typescript-next",

    // ===== 框架支持 =====
    "vue.volar",
    "svelte.svelte-vscode",
    "bradlc.vscode-tailwindcss",

    // ===== 代码质量 =====
    "streetsidesoftware.code-spell-checker",
    "wayou.vscode-todo-highlight",
    "gruntfuggly.todo-tree",

    // ===== Git 增强 =====
    "eamodio.gitlens",
    "mhutchie.git-graph",

    // ===== 开发体验 =====
    "ritwickdey.liveserver",
    "ms-vscode.live-server",
    "ms-vscode-remote.remote-containers",
    "ms-azuretools.vscode-docker",

    // ===== 测试 =====
    "ms-playwright.playwright",

    // ===== 文档 =====
    "yzane.markdown-all-in-one",
    "shd101wyy.markdown-preview-enhanced",

    // ===== 数据库 =====
    "mtxr.sqltools",
    "mtxr.sqltools-driver-pg"
  ],
  "unwantedRecommendations": [
    "ms-vscode.sublime-keybindings",
    "vscodevim.vim"
  ]
}
```

**配合 `settings.json` 的扩展配置**：

```json
{
  // ===== ESLint 配置 =====
  "eslint.enable": true,
  "eslint.validate": [
    "javascript",
    "typescript",
    "vue",
    "html"
  ],
  "eslint.packageManager": "pnpm",
  "eslint.workingDirectories": ["./frontend", "./backend"],
  "eslint.codeAction.showDocumentation": {
    "enable": true
  },

  // ===== Prettier 配置 =====
  "prettier.enable": true,
  "prettier.configPath": ".prettierrc.js",
  "prettier.ignorePath": ".prettierignore",
  "prettier.requireConfig": true,
  "prettier.useEditorConfig": true,

  // ===== Python 扩展配置 =====
  "python.defaultInterpreterPath": "${workspaceFolder}/.venv/bin/python",
  "python.analysis.typeCheckingMode": "strict",
  "python.analysis.autoImportCompletions": true,
  "python.formatting.provider": "black",
  "python.formatting.blackPath": "black",
  "python.linting.enabled": true,
  "python.linting.flake8Enabled": true,
  "python.linting.mypyEnabled": true,
  "python.testing.pytestEnabled": true,
  "python.testing.pytestArgs": ["tests"],

  // ===== GitLens 配置 =====
  "gitlens.currentLine.enabled": true,
  "gitlens.codeLens.enabled": true,
  "gitlens.hovers.currentLine.over": "line",
  "gitlens.blame.highlight.enabled": true,
  "gitlens.statusBar.enabled": true,

  // ===== Tailwind CSS 配置 =====
  "tailwindCSS.includeLanguages": {
    "vue": "html",
    "svelte": "html"
  },
  "tailwindCSS.emmetCompletions": true,
  "tailwindCSS.files.exclude": [
    "**/.git/**",
    "**/node_modules/**",
    "**/dist/**"
  ],

  // ===== 拼写检查配置 =====
  "cSpell.enabled": true,
  "cSpell.language": "en,en-US",
  "cSpell.words": [
    "vite",
    "pinia",
    "fastapi",
    "sqlalchemy",
    "pydantic"
  ],
  "cSpell.ignorePaths": [
    "package-lock.json",
    "node_modules",
    "*.log"
  ],

  // ===== TODO Highlight 配置 =====
  "todohighlight.isEnable": true,
  "todohighlight.isCaseSensitive": false,
  "todohighlight.keywords": [
    "TODO:",
    "FIXME:",
    "BUG:",
    "HACK:",
    "NOTE:"
  ]
}
```

**团队扩展管理最佳实践**：

1. **分层推荐**：将扩展分为"必需"和"推荐"两类。必需扩展放入 `extensions.json`，推荐扩展在 README 中列出。

2. **版本锁定**：虽然 `extensions.json` 不支持指定扩展版本，但可以通过 `package.json` 的 `devDependencies` 锁定 CLI 工具版本，确保格式化行为一致。

3. **自动安装脚本**：在项目初始化脚本中添加自动安装逻辑：

```json
// package.json
{
  "scripts": {
    "setup": "npm run install:vscode-extensions && npm install",
    "install:vscode-extensions": "code --install-extension esbenp.prettier-vscode && code --install-extension dbaeumer.vscode-eslint"
  }
}
```

4. **`.vscodeignore` 文件**：如果你在开发 VS Code 扩展，`.vscodeignore` 用于指定发布时排除的文件。这与 `.vscode` 目录无关，但容易混淆。

### 总结

- `extensions.json` 定义推荐扩展，VS Code 自动提示安装
- 扩展 ID 格式为 `publisher.extension-name`
- `unwantedRecommendations` 声明不推荐的扩展
- 配合 `settings.json` 中的扩展配置，实现工具行为统一
- 推荐扩展是"建议"而非"强制"，需结合文档和 CI 补充约束
- 通过 `package.json` scripts 实现扩展自动安装，提升新人上手体验
- 分层管理（必需 vs 推荐）避免扩展过载

---

## 第16讲 多根工作区与远程配置

### 概念

**多根工作区**（Multi-root Workspace）允许在一个 VS Code 窗口中同时打开多个项目文件夹，适用于微服务架构、monorepo 管理、前后端联调等场景。多根工作区的配置以 `.code-workspace` 文件形式存在，其中可以包含每个文件夹的独立配置。

**远程开发配置**涉及 Remote-SSH、Remote-Containers、Remote-WSL 等扩展，让 VS Code 连接到远程环境进行开发。远程场景下的 `.vscode` 配置需要特别注意路径映射、端口转发等问题。

### 原理

**多根工作区的工作机制**：

1. **工作区文件**：`.code-workspace` 文件定义了工作区的文件夹列表和共享配置
2. **配置层级**：默认设置 < 用户设置 < 工作区设置 < 文件夹设置
3. **文件夹设置**：多根工作区中，每个文件夹可以有自己的 `.vscode/settings.json`，通过 `folders[].settings` 字段或文件夹内的 `.vscode` 目录定义
4. **配置合并**：当多个文件夹的设置冲突时，后列出的文件夹优先级更高

**远程开发的配置策略**：

- **Remote-SSH**：VS Code 通过 SSH 连接到远程主机，`.vscode` 配置存储在远程主机上
- **Dev Containers**：通过 `.devcontainer/devcontainer.json` 定义容器环境，`.vscode` 配置会被挂载到容器中
- **WSL**：Windows Subsystem for Linux，配置存储在 WSL 文件系统中

远程场景的关键挑战是**路径差异**——本地路径与远程路径不同，调试器的 source map、断点定位都需要路径映射。

### 例子

**1. 多根工作区配置文件**

一个微服务项目的 `microservices.code-workspace`：

```json
{
  "folders": [
    {
      "name": "API Gateway",
      "path": "services/api-gateway"
    },
    {
      "name": "User Service",
      "path": "services/user-service"
    },
    {
      "name": "Order Service",
      "path": "services/order-service"
    },
    {
      "name": "Payment Service",
      "path": "services/payment-service"
    },
    {
      "name": "Shared Lib",
      "path": "packages/shared"
    },
    {
      "name": "Docs",
      "path": "docs"
    }
  ],
  "settings": {
    // 工作区级别的共享设置
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
      "source.fixAll.eslint": "explicit"
    },
    "search.exclude": {
      "**/node_modules": true,
      "**/dist": true,
      "**/coverage": true
    },
    "files.exclude": {
      "**/.git": true,
      "**/.DS_Store": true
    },
    "typescript.tsdk": "packages/shared/node_modules/typescript/lib",
    "eslint.workingDirectories": [
      "services/api-gateway",
      "services/user-service",
      "services/order-service",
      "services/payment-service"
    ],
    "go.gopath": "${workspaceFolder}/.go",
    "python.defaultInterpreterPath": "${workspaceFolder}/.venv/bin/python"
  },
  "extensions": {
    "recommendations": [
      "esbenp.prettier-vscode",
      "dbaeumer.vscode-eslint",
      "ms-python.python",
      "golang.go",
      "ms-azuretools.vscode-docker"
    ]
  },
  "launch": {
    "version": "0.2.0",
    "configurations": [
      {
        "name": "调试所有服务",
        "configurations": [
          "API Gateway",
          "User Service",
          "Order Service",
          "Payment Service"
        ]
      }
    ],
    "compounds": []
  },
  "tasks": {
    "version": "2.0.0",
    "tasks": [
      {
        "label": "启动所有服务",
        "dependsOn": [
          "启动 API Gateway",
          "启动 User Service",
          "启动 Order Service",
          "启动 Payment Service"
        ],
        "dependsOrder": "parallel"
      }
    ]
  }
}
```

**2. Dev Container 配置**

`.devcontainer/devcontainer.json`：

```json
{
  "name": "Full Stack Dev",
  "image": "mcr.microsoft.com/devcontainers/typescript-node:20",

  "features": {
    "ghcr.io/devcontainers/features/python:1": {
      "version": "3.12",
      "installTools": true
    },
    "ghcr.io/devcontainers/features/docker-outside-of-docker:1": {},
    "ghcr.io/devcontainers/features/github-cli:1": {}
  },

  "forwardPorts": [3000, 5173, 8000, 9229],

  "postCreateCommand": "pnpm install && pre-commit install",

  "customizations": {
    "vscode": {
      "extensions": [
        "esbenp.prettier-vscode",
        "dbaeumer.vscode-eslint",
        "ms-python.python",
        "ms-python.vscode-pylance",
        "bradlc.vscode-tailwindcss"
      ],
      "settings": {
        "editor.formatOnSave": true,
        "python.defaultInterpreterPath": "/usr/local/python/current/bin/python",
        "python.linting.enabled": true,
        "python.linting.flake8Enabled": true,
        "typescript.tsdk": "node_modules/typescript/lib"
      }
    }
  },

  "remoteEnv": {
    "DATABASE_URL": "postgresql://dev:dev@localhost:5432/devdb",
    "REDIS_URL": "redis://localhost:6379"
  },

  "mounts": [
    "source=${localWorkspaceFolder}/.env,target=/workspace/.env,type=bind,consistency=cached"
  ],

  "remoteUser": "node"
}
```

**3. Remote-SSH 调试配置**

远程开发场景下的 `launch.json`，处理路径映射：

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Python: 远程调试",
      "type": "python",
      "request": "attach",
      "connect": {
        "host": "remote-server",
        "port": 5678
      },
      "pathMappings": [
        {
          "localRoot": "${workspaceFolder}",
          "remoteRoot": "/home/user/project"
        }
      ],
      "justMyCode": true
    },
    {
      "name": "Node.js: 远程调试",
      "type": "node",
      "request": "attach",
      "address": "remote-server",
      "port": 9229,
      "localRoot": "${workspaceFolder}",
      "remoteRoot": "/home/user/project",
      "skipFiles": ["<node_internals>/**"]
    },
    {
      "name": "Go: 远程调试",
      "type": "go",
      "request": "attach",
      "mode": "remote",
      "host": "remote-server",
      "port": 2345,
      "substitutePath": [
        {
          "from": "${workspaceFolder}",
          "to": "/home/user/project"
        }
      ]
    }
  ]
}
```

**4. 端口转发配置**

在远程开发中，自动转发端口是关键。`settings.json` 中的端口配置：

```json
{
  "remote.autoForwardPorts": true,
  "remote.autoForwardPortsSource": "output",
  "remote.autoForwardPortsHistory": [3000, 5173, 8000, 9229],
  "remote.restoreForwardedPorts": true,
  "remote.portsAttributes": {
    "3000": {
      "label": "API Server",
      "onAutoForward": "openBrowser"
    },
    "5173": {
      "label": "Vite Dev Server",
      "onAutoForward": "openPreview"
    },
    "9229": {
      "label": "Node Debugger",
      "onAutoForward": "silent"
    }
  }
}
```

**多根工作区使用技巧**：

1. **文件夹别名**：通过 `folders[].name` 为文件夹设置显示名称，避免长路径
2. **共享配置**：将通用配置放在工作区级别，减少重复
3. **独立配置**：每个服务文件夹内保留自己的 `.vscode` 目录，存放服务特定的任务和调试配置
4. **搜索范围**：多根工作区中，`Ctrl+Shift+F` 默认搜索所有文件夹，可通过 `search.useIgnoreFiles` 和 `search.exclude` 精细控制

### 总结

- 多根工作区通过 `.code-workspace` 文件定义，适合微服务/monorepo 场景
- 配置层级：默认 < 用户 < 工作区 < 文件夹，后者覆盖前者
- 工作区文件可包含 `settings`、`extensions`、`launch`、`tasks` 等配置
- Dev Container 通过 `devcontainer.json` 定义容器环境，`customizations.vscode` 配置扩展和设置
- 远程调试必须配置路径映射（`pathMappings`、`localRoot`/`remoteRoot`、`substitutePath`）
- `remote.portsAttributes` 精细控制端口转发行为
- 多根工作区中善用文件夹别名和共享配置，提升管理效率

---

# 第七章 实战整合

## 第17讲 前端项目完整配置实战

### 概念

本讲将整合前 16 讲的知识，为一个完整的 React + TypeScript + Vite 前端项目编写全套 `.vscode` 配置。我们将涵盖 `settings.json`、`tasks.json`、`launch.json`、`extensions.json`、代码片段和快捷键，形成一个开箱即用的专业级前端开发环境。这个实战案例将展示如何将各个配置文件协同工作，打造高效的开发工作流。

### 原理

前端项目的 `.vscode` 配置需要围绕以下核心工作流展开：

1. **代码质量工作流**：ESLint（代码检查）+ Prettier（格式化）+ TypeScript（类型检查）三位一体
2. **开发调试工作流**：Vite 开发服务器 + 浏览器调试 + 断点定位
3. **构建部署工作流**：类型检查 → 单元测试 → 构建 → 预览
4. **代码生成工作流**：组件模板、Hook 模板、测试模板的快速插入

配置文件之间的协作关系：`extensions.json` 推荐必要扩展 → `settings.json` 配置扩展行为 → `tasks.json` 定义自动化任务 → `launch.json` 配置调试方案 → 代码片段加速编码 → 快捷键绑定常用操作。

### 例子

**项目结构**：

```
my-react-app/
├── .vscode/
│   ├── settings.json
│   ├── tasks.json
│   ├── launch.json
│   ├── extensions.json
│   ├── keybindings.json
│   └── react.code-snippets
├── src/
├── public/
├── package.json
├── tsconfig.json
├── vite.config.ts
├── .eslintrc.cjs
├── .prettierrc
└── index.html
```

**1. `.vscode/settings.json` —— 完整前端配置**

```json
{
  // ===== 编辑器基础 =====
  "editor.fontSize": 14,
  "editor.fontFamily": "'JetBrains Mono', 'Fira Code', 'Cascadia Code', Consolas, monospace",
  "editor.fontLigatures": true,
  "editor.lineHeight": 22,
  "editor.tabSize": 2,
  "editor.insertSpaces": true,
  "editor.formatOnSave": true,
  "editor.formatOnPaste": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit",
    "source.organizeImports": "never"
  },
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.bracketPairColorization.enabled": true,
  "editor.guides.bracketPairs": "active",
  "editor.minimap.enabled": false,
  "editor.stickyScroll.enabled": true,
  "editor.inlineSuggest.enabled": true,
  "editor.suggestSelection": "first",
  "editor.wordBasedSuggestions": "off",

  // ===== 文件管理 =====
  "files.autoSave": "onFocusChange",
  "files.eol": "\n",
  "files.insertFinalNewline": true,
  "files.trimTrailingWhitespace": true,
  "files.associations": {
    "*.tsx": "typescriptreact",
    "*.ts": "typescript",
    "*.css": "css",
    "*.scss": "scss",
    "*.svg": "xml"
  },
  "files.exclude": {
    "**/.git": true,
    "**/.DS_Store": true,
    "**/node_modules": true,
    "**/dist": true,
    "**/coverage": true,
    "**/*.log": true
  },
  "search.exclude": {
    "**/node_modules": true,
    "**/dist": true,
    "**/pnpm-lock.yaml": true,
    "**/yarn.lock": true
  },

  // ===== TypeScript 配置 =====
  "typescript.tsdk": "node_modules/typescript/lib",
  "typescript.enablePromptUseWorkspaceTsdk": true,
  "typescript.updateImportsOnFileMove.enabled": "always",
  "typescript.suggest.autoImports": true,
  "typescript.inlayHints.parameterNames.enabled": "all",
  "typescript.inlayHints.parameterTypes.enabled": true,
  "typescript.inlayHints.variableTypes.enabled": true,
  "typescript.inlayHints.propertyDeclarationTypes.enabled": true,
  "typescript.inlayHints.functionLikeReturnTypes.enabled": true,
  "typescript.preferences.importModuleSpecifier": "relative",
  "typescript.preferences.quoteStyle": "single",
  "typescript.format.semicolons": "remove",
  "typescript.format.trailingComma": "es5",

  // ===== ESLint 配置 =====
  "eslint.enable": true,
  "eslint.validate": [
    "javascript",
    "typescript",
    "typescriptreact",
    "vue",
    "html",
    "json"
  ],
  "eslint.packageManager": "pnpm",
  "eslint.run": "onType",
  "eslint.codeAction.showDocumentation": {
    "enable": true
  },
  "eslint.workingDirectories": [
    {
      "mode": "auto"
    }
  ],

  // ===== Prettier 配置 =====
  "prettier.enable": true,
  "prettier.configPath": ".prettierrc",
  "prettier.ignorePath": ".prettierignore",
  "prettier.requireConfig": true,
  "prettier.useEditorConfig": false,
  "prettier.documentSelectors": [
    "javascript",
    "typescript",
    "typescriptreact",
    "json",
    "css",
    "scss",
    "markdown"
  ],

  // ===== Emmet 配置 =====
  "emmet.includeLanguages": {
    "typescriptreact": "html",
    "javascriptreact": "html",
    "vue": "html"
  },
  "emmet.triggerExpansionOnTab": true,
  "emmet.showSuggestionsAsSnippets": true,

  // ===== 路径别名 =====
  "path-intellisense.mappings": {
    "@": "${workspaceRoot}/src",
    "@components": "${workspaceRoot}/src/components",
    "@utils": "${workspaceRoot}/src/utils",
    "@hooks": "${workspaceRoot}/src/hooks",
    "@pages": "${workspaceRoot}/src/pages",
    "@assets": "${workspaceRoot}/src/assets"
  },

  // ===== 浏览器预览 =====
  "liveServer.settings.donotShowInfoMsg": true,
  "liveServer.settings.host": "localhost",
  "liveServer.settings.port": 5173,
  "liveServer.settings.CustomBrowser": "chrome",
  "liveServer.settings.AdvanceCustomBrowserCmdLine": "chrome --incognito --auto-open-devtools-for-tabs",

  // ===== 终端配置 =====
  "terminal.integrated.fontSize": 13,
  "terminal.integrated.fontFamily": "'JetBrains Mono', monospace",
  "terminal.integrated.scrollback": 10000,
  "terminal.integrated.defaultProfile.windows": "Git Bash",
  "terminal.integrated.defaultProfile.osx": "zsh",
  "terminal.integrated.defaultProfile.linux": "bash",

  // ===== Git 配置 =====
  "git.autofetch": true,
  "git.confirmSync": false,
  "git.enableSmartCommit": false,
  "gitlens.currentLine.enabled": true,
  "gitlens.codeLens.enabled": true,
  "gitlens.hovers.currentLine.over": "line",

  // ===== 调试配置 =====
  "debug.javascript.autoAttachFilter": "smart",
  "debug.console.closeOnEnd": true,
  "debug.internalConsoleOptions": "openOnSessionStart",

  // ===== 测试配置 =====
  "testing.automaticallyOpenPeekView": "never",
  "testing.followRunningTest": false
}
```

**2. `.vscode/tasks.json` —— 前端自动化任务**

```json
{
  "version": "2.0.0",
  "inputs": [
    {
      "id": "componentName",
      "type": "promptString",
      "description": "输入组件名称（PascalCase）",
      "default": "MyComponent"
    },
    {
      "id": "testPattern",
      "type": "promptString",
      "description": "测试文件匹配模式",
      "default": ""
    }
  ],
  "tasks": [
    {
      "label": "dev",
      "detail": "启动 Vite 开发服务器",
      "type": "shell",
      "command": "pnpm",
      "args": ["dev"],
      "isBackground": true,
      "problemMatcher": {
        "owner": "vite",
        "pattern": {
          "regexp": "^(.*):(\\d+):(\\d+):\\s+(.*)$",
          "file": 1,
          "line": 2,
          "column": 3,
          "message": 4
        },
        "background": {
          "activeOnStart": true,
          "beginsPattern": "VITE v",
          "endsPattern": "ready in"
        }
      },
      "group": "build",
      "presentation": {
        "reveal": "always",
        "panel": "dedicated"
      }
    },
    {
      "label": "build",
      "detail": "生产构建",
      "type": "shell",
      "command": "pnpm",
      "args": ["build"],
      "group": {
        "kind": "build",
        "isDefault": true
      },
      "problemMatcher": ["$tsc"],
      "presentation": {
        "reveal": "silent",
        "panel": "shared"
      }
    },
    {
      "label": "preview",
      "detail": "预览生产构建",
      "type": "shell",
      "command": "pnpm",
      "args": ["preview"],
      "isBackground": true,
      "problemMatcher": []
    },
    {
      "label": "lint",
      "detail": "ESLint 检查",
      "type": "shell",
      "command": "pnpm",
      "args": ["lint", "--max-warnings", "0"],
      "problemMatcher": "$eslint-stylish"
    },
    {
      "label": "lint:fix",
      "detail": "ESLint 自动修复",
      "type": "shell",
      "command": "pnpm",
      "args": ["lint", "--fix"],
      "problemMatcher": []
    },
    {
      "label": "typecheck",
      "detail": "TypeScript 类型检查",
      "type": "shell",
      "command": "pnpm",
      "args": ["tsc", "--noEmit"],
      "problemMatcher": ["$tsc"]
    },
    {
      "label": "test",
      "detail": "运行所有测试",
      "type": "shell",
      "command": "pnpm",
      "args": ["test", "--run"],
      "group": {
        "kind": "test",
        "isDefault": true
      },
      "problemMatcher": []
    },
    {
      "label": "test:watch",
      "detail": "测试监听模式",
      "type": "shell",
      "command": "pnpm",
      "args": ["test"],
      "isBackground": true,
      "problemMatcher": []
    },
    {
      "label": "test:current",
      "detail": "测试当前文件",
      "type": "shell",
      "command": "pnpm",
      "args": ["test", "--run", "${file}"],
      "problemMatcher": []
    },
    {
      "label": "test:coverage",
      "detail": "生成测试覆盖率报告",
      "type": "shell",
      "command": "pnpm",
      "args": ["test", "--run", "--coverage"],
      "problemMatcher": []
    },
    {
      "label": "analyze",
      "detail": "分析构建包大小",
      "type": "shell",
      "command": "pnpm",
      "args": ["build", "--analyze"],
      "problemMatcher": []
    },
    {
      "label": "pre-commit",
      "detail": "提交前检查（lint + typecheck + test）",
      "dependsOn": ["lint", "typecheck", "test"],
      "dependsOrder": "sequence",
      "problemMatcher": []
    },
    {
      "label": "clean",
      "detail": "清理构建产物",
      "type": "shell",
      "command": "rm",
      "args": ["-rf", "dist", "coverage", "node_modules/.vite"],
      "windows": {
        "command": "cmd",
        "args": ["/c", "rmdir", "/s", "/q", "dist", "coverage", "node_modules\\.vite"]
      }
    }
  ]
}
```

**3. `.vscode/launch.json` —— 前端调试配置**

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "启动开发服务器并调试",
      "type": "node",
      "request": "launch",
      "runtimeExecutable": "pnpm",
      "runtimeArgs": ["dev"],
      "skipFiles": ["<node_internals>/**"],
      "console": "integratedTerminal",
      "serverReadyAction": {
        "action": "debugWithChrome",
        "pattern": "Local: (https?://\\S+)",
        "webRoot": "${workspaceFolder}/src"
      }
    },
    {
      "name": "Chrome 调试（需先启动 dev）",
      "type": "chrome",
      "request": "launch",
      "url": "http://localhost:5173",
      "webRoot": "${workspaceFolder}/src",
      "sourceMaps": true,
      "sourceMapPathOverrides": {
        "webpack:///./src/*": "${webRoot}/*",
        "webpack:///src/*": "${webRoot}/*"
      },
      "runtimeArgs": [
        "--auto-open-devtools-for-tabs",
        "--disable-extensions"
      ]
    },
    {
      "name": "附加到 Chrome",
      "type": "chrome",
      "request": "attach",
      "port": 9222,
      "webRoot": "${workspaceFolder}/src",
      "urlFilter": "http://localhost:5173*"
    },
    {
      "name": "Edge 调试",
      "type": "msedge",
      "request": "launch",
      "url": "http://localhost:5173",
      "webRoot": "${workspaceFolder}/src"
    },
    {
      "name": "调试 Vitest 当前文件",
      "type": "node",
      "request": "launch",
      "cwd": "${workspaceFolder}",
      "program": "${workspaceFolder}/node_modules/vitest/vitest.mjs",
      "args": ["run", "${file}"],
      "console": "integratedTerminal",
      "skipFiles": ["<node_internals>/**", "**/node_modules/**"]
    },
    {
      "name": "调试 Vite 配置",
      "type": "node",
      "request": "launch",
      "program": "${workspaceFolder}/node_modules/vite/bin/vite.js",
      "args": ["--debug"],
      "console": "integratedTerminal"
    }
  ],
  "compounds": []
}
```

**4. `.vscode/extensions.json`**

```json
{
  "recommendations": [
    "esbenp.prettier-vscode",
    "dbaeumer.vscode-eslint",
    "ms-vscode.vscode-typescript-next",
    "bradlc.vscode-tailwindcss",
    "christian-kohler.path-intellisense",
    "streetsidesoftware.code-spell-checker",
    "wayou.vscode-todo-highlight",
    "gruntfuggly.todo-tree",
    "eamodio.gitlens",
    "mhutchie.git-graph",
    "ritwickdey.liveserver",
    "ms-playwright.playwright",
    "yzane.markdown-all-in-one",
    "wix.vscode-import-cost",
    "steoates.autoimport",
    "visualstudioexptteam.vscodeintellicode"
  ],
  "unwantedRecommendations": [
    "ms-vscode.sublime-keybindings",
    "vscodevim.vim"
  ]
}
```

**5. `.vscode/react.code-snippets`**（精选片段）

```json
{
  "React 函数组件": {
    "prefix": "rfc",
    "body": [
      "import { type FC } from 'react';",
      "",
      "interface ${1:${TM_FILENAME_BASE}}Props {",
      "  ${2:children?: React.ReactNode;}",
      "}",
      "",
      "export const ${1:${TM_FILENAME_BASE}}: FC<${1:${TM_FILENAME_BASE}}Props> = ({ ${2:children} }) => {",
      "  return (",
      "    <div>",
      "      ${3}",
      "    </div>",
      "  );",
      "};",
      ""
    ]
  },
  "React 状态组件": {
    "prefix": "rfcs",
    "body": [
      "import { useState, type FC } from 'react';",
      "",
      "interface ${1:${TM_FILENAME_BASE}}Props {",
      "  ${2}",
      "}",
      "",
      "export const ${1:${TM_FILENAME_BASE}}: FC<${1:${TM_FILENAME_BASE}}Props> = (props) => {",
      "  const [${3:state}, set${3/(.)/${1:/upcase}/}] = useState(${4:initialValue});",
      "",
      "  return (",
      "    <div>",
      "      ${5}",
      "    </div>",
      "  );",
      "};",
      ""
    ]
  },
  "useEffect": {
    "prefix": "useff",
    "body": [
      "useEffect(() => {",
      "  ${1}",
      "  return () => {",
      "    ${2:// cleanup}",
      "  };",
      "}, [${3}]);"
    ]
  },
  "useState": {
    "prefix": "usest",
    "body": [
      "const [${1:state}, set${1/(.)/${1:/upcase}/}] = useState<${2:type}>(${3:initialValue});"
    ]
  },
  "import 导入": {
    "prefix": "imp",
    "body": "import { ${1:name} } from '${2:module}';"
  },
  "import default": {
    "prefix": "imd",
    "body": "import ${1:name} from '${2:module}';"
  }
}
```

**6. `.vscode/keybindings.json`**（精选快捷键）

```json
[
  {
    "key": "ctrl+alt+d",
    "command": "workbench.action.terminal.sendSequence",
    "when": "terminalFocus",
    "args": { "text": "pnpm dev\u000D" }
  },
  {
    "key": "ctrl+alt+b",
    "command": "workbench.action.terminal.sendSequence",
    "when": "terminalFocus",
    "args": { "text": "pnpm build\u000D" }
  },
  {
    "key": "ctrl+alt+t",
    "command": "workbench.action.terminal.sendSequence",
    "when": "terminalFocus",
    "args": { "text": "pnpm test --run\u000D" }
  },
  {
    "key": "ctrl+alt+l",
    "command": "workbench.action.terminal.sendSequence",
    "when": "terminalFocus",
    "args": { "text": "pnpm lint --fix\u000D" }
  },
  {
    "key": "ctrl+shift+t",
    "command": "testing.runCurrentFile",
    "when": "editorTextFocus"
  },
  {
    "key": "ctrl+shift+alt+t",
    "command": "testing.debugCurrentFile",
    "when": "editorTextFocus"
  }
]
```

### 总结

- 前端配置围绕代码质量、开发调试、构建部署、代码生成四大工作流展开
- `settings.json` 是核心，统一 ESLint、Prettier、TypeScript 的行为
- `tasks.json` 定义 dev/build/test/lint/typecheck 等常用任务
- `launch.json` 利用 `serverReadyAction` 实现一键启动开发服务器并附加浏览器调试
- 代码片段为 React 组件、Hook、测试提供快速模板
- 快捷键通过 `sendSequence` 实现终端命令的快捷执行
- 各配置文件协同工作，形成完整的开发工作流

---

## 第18讲 Python 后端项目完整配置实战

### 概念

本讲将以 FastAPI + SQLAlchemy + pytest 的 Python 后端项目为例，展示完整的 `.vscode` 配置方案。Python 项目的配置重点在于虚拟环境管理、类型检查、测试调试和代码格式化。我们将配置 Pylance（语言服务）、Ruff/Black（格式化与 Lint）、pytest（测试）和 debugpy（调试），打造专业的 Python 后端开发环境。

### 原理

Python 项目的 `.vscode` 配置需要解决以下核心问题：

1. **解释器管理**：确保 VS Code 使用正确的虚拟环境，避免全局环境污染
2. **类型检查**：Pylance 提供静态类型检查，mypy 提供更严格的运行时检查
3. **代码规范**：Black（格式化）+ Ruff（Lint）替代传统的 autopep8 + flake8 + isort 组合
4. **测试集成**：pytest 的发现、运行、调试与 VS Code Testing 面板深度集成
5. **调试支持**：FastAPI/Django 等 Web 框架的特殊调试需求（热重载、模板调试）

Python 配置的一个独特挑战是**环境隔离**——不同项目使用不同的虚拟环境，配置必须正确指向项目专属的解释器。

### 例子

**项目结构**：

```
my-fastapi-app/
├── .vscode/
│   ├── settings.json
│   ├── tasks.json
│   ├── launch.json
│   ├── extensions.json
│   └── python.code-snippets
├── app/
│   ├── __init__.py
│   ├── main.py
│   ├── api/
│   ├── core/
│   ├── models/
│   ├── schemas/
│   ├── services/
│   └── utils/
├── tests/
├── .venv/
├── pyproject.toml
├── requirements.txt
└── alembic.ini
```

**1. `.vscode/settings.json` —— 完整 Python 后端配置**

```json
{
  // ===== Python 解释器 =====
  "python.defaultInterpreterPath": "${workspaceFolder}/.venv/bin/python",
  "python.terminal.activateEnvironment": true,
  "python.terminal.activateEnvInCurrentTerminal": true,
  "python.envFile": "${workspaceFolder}/.env",

  // ===== Pylance 类型检查 =====
  "python.languageServer": "Pylance",
  "python.analysis.typeCheckingMode": "strict",
  "python.analysis.autoImportCompletions": true,
  "python.analysis.indexing": true,
  "python.analysis.packageIndexDepths": [
    {
      "name": "",
      "depth": 3,
      "includeAllSymbols": true
    }
  ],
  "python.analysis.diagnosticSeverityOverrides": {
    "reportMissingImports": "error",
    "reportMissingTypeStubs": "none",
    "reportUnknownMemberType": "warning",
    "reportUnknownVariableType": "warning",
    "reportUnknownArgumentType": "warning",
    "reportGeneralTypeIssues": "error",
    "reportUnusedImport": "warning",
    "reportUnusedVariable": "warning",
    "reportDuplicateImport": "error"
  },
  "python.analysis.inlayHints.variableTypes": true,
  "python.analysis.inlayHints.functionReturnTypes": true,
  "python.analysis.inlayHints.pytestParameters": true,

  // ===== 代码格式化 =====
  "[python]": {
    "editor.defaultFormatter": "charliermarsh.ruff",
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
      "source.fixAll": "explicit",
      "source.organizeImports": "explicit"
    },
    "editor.rulers": [88]
  },

  // ===== Ruff 配置 =====
  "ruff.enable": true,
  "ruff.organizeImports": true,
  "ruff.fixAll": true,
  "ruff.lint.enable": true,
  "ruff.format.args": ["--line-length", "88"],
  "ruff.configuration": "${workspaceFolder}/pyproject.toml",

  // ===== 测试配置 =====
  "python.testing.pytestEnabled": true,
  "python.testing.unittestEnabled": false,
  "python.testing.pytestArgs": [
    "tests",
    "-v",
    "--tb=short"
  ],
  "python.testing.cwd": "${workspaceFolder}",
  "python.testing.autoTestDiscoverOnSaveEnabled": true,
  "testing.automaticallyOpenPeekView": "never",

  // ===== 编辑器通用 =====
  "editor.fontSize": 14,
  "editor.fontFamily": "'JetBrains Mono', 'Fira Code', Consolas, monospace",
  "editor.tabSize": 4,
  "editor.insertSpaces": true,
  "editor.bracketPairColorization.enabled": true,
  "editor.guides.bracketPairs": "active",
  "editor.stickyScroll.enabled": true,
  "editor.minimap.enabled": false,

  // ===== 文件管理 =====
  "files.autoSave": "onFocusChange",
  "files.eol": "\n",
  "files.insertFinalNewline": true,
  "files.trimTrailingWhitespace": true,
  "files.associations": {
    "*.py": "python",
    "*.pyi": "python",
    "requirements*.txt": "pip-requirements",
    "pyproject.toml": "toml",
    ".env": "dotenv",
    ".env.*": "dotenv"
  },
  "files.exclude": {
    "**/.git": true,
    "**/.DS_Store": true,
    "**/__pycache__": true,
    "**/.pytest_cache": true,
    "**/.mypy_cache": true,
    "**/.ruff_cache": true,
    "**/*.egg-info": true,
    "**/.venv": true
  },
  "search.exclude": {
    "**/__pycache__": true,
    "**/.venv": true,
    "**/.pytest_cache": true,
    "**/.mypy_cache": true
  },

  // ===== 终端配置 =====
  "terminal.integrated.fontSize": 13,
  "terminal.integrated.env.osx": {
    "PYTHONPATH": "${workspaceFolder}"
  },
  "terminal.integrated.env.linux": {
    "PYTHONPATH": "${workspaceFolder}"
  },
  "terminal.integrated.env.windows": {
    "PYTHONPATH": "${workspaceFolder}"
  },

  // ===== Git 配置 =====
  "git.autofetch": true,
  "git.confirmSync": false,
  "gitlens.currentLine.enabled": true,

  // ===== 数据库工具 =====
  "sqltools.connections": [
    {
      "name": "Dev Database",
      "driver": "PostgreSQL",
      "previewLimit": 50,
      "server": "localhost",
      "port": 5432,
      "database": "app_db",
      "username": "postgres",
      "password": "postgres"
    }
  ],

  // ===== 调试配置 =====
  "debug.console.closeOnEnd": true,
  "debug.python.debugJustMyCode": true
}
```

**2. `.vscode/tasks.json` —— Python 自动化任务**

```json
{
  "version": "2.0.0",
  "inputs": [
    {
      "id": "migrationMessage",
      "type": "promptString",
      "description": "输入迁移消息",
      "default": "update models"
    },
    {
      "id": "testPath",
      "type": "promptString",
      "description": "测试路径（留空运行全部）",
      "default": ""
    }
  ],
  "tasks": [
    {
      "label": "install",
      "detail": "安装依赖",
      "type": "shell",
      "command": "pip",
      "args": ["install", "-r", "requirements.txt"],
      "problemMatcher": []
    },
    {
      "label": "install:dev",
      "detail": "安装开发依赖",
      "type": "shell",
      "command": "pip",
      "args": ["install", "-r", "requirements-dev.txt"],
      "problemMatcher": []
    },
    {
      "label": "run",
      "detail": "启动 FastAPI 开发服务器",
      "type": "shell",
      "command": "uvicorn",
      "args": [
        "app.main:app",
        "--reload",
        "--host", "0.0.0.0",
        "--port", "8000"
      ],
      "isBackground": true,
      "problemMatcher": {
        "owner": "python",
        "pattern": {
          "regexp": "^\\s*File \"(.*)\", line (\\d+), in (.*)$",
          "file": 1,
          "line": 2,
          "message": 3
        },
        "background": {
          "activeOnStart": true,
          "beginsPattern": "INFO.*Uvicorn",
          "endsPattern": "Application startup complete"
        }
      },
      "group": "build"
    },
    {
      "label": "run:prod",
      "detail": "生产模式启动",
      "type": "shell",
      "command": "gunicorn",
      "args": [
        "app.main:app",
        "-w", "4",
        "-k", "uvicorn.workers.UvicornWorker",
        "-b", "0.0.0.0:8000"
      ],
      "isBackground": true,
      "problemMatcher": []
    },
    {
      "label": "test",
      "detail": "运行所有测试",
      "type": "shell",
      "command": "pytest",
      "args": ["tests/", "-v", "--tb=short"],
      "group": {
        "kind": "test",
        "isDefault": true
      },
      "problemMatcher": []
    },
    {
      "label": "test:current",
      "detail": "测试当前文件",
      "type": "shell",
      "command": "pytest",
      "args": ["${file}", "-v", "--tb=short"],
      "problemMatcher": []
    },
    {
      "label": "test:watch",
      "detail": "测试监听模式",
      "type": "shell",
      "command": "ptw",
      "args": ["--", "${input:testPath}", "-v"],
      "isBackground": true,
      "problemMatcher": []
    },
    {
      "label": "test:coverage",
      "detail": "生成覆盖率报告",
      "type": "shell",
      "command": "pytest",
      "args": [
        "tests/",
        "--cov=app",
        "--cov-report=html",
        "--cov-report=term-missing"
      ],
      "problemMatcher": []
    },
    {
      "label": "lint",
      "detail": "Ruff Lint 检查",
      "type": "shell",
      "command": "ruff",
      "args": ["check", "."],
      "problemMatcher": []
    },
    {
      "label": "lint:fix",
      "detail": "Ruff 自动修复",
      "type": "shell",
      "command": "ruff",
      "args": ["check", ".", "--fix"],
      "problemMatcher": []
    },
    {
      "label": "format",
      "detail": "Black 格式化",
      "type": "shell",
      "command": "black",
      "args": ["."],
      "problemMatcher": []
    },
    {
      "label": "typecheck",
      "detail": "mypy 类型检查",
      "type": "shell",
      "command": "mypy",
      "args": ["app/", "--strict"],
      "problemMatcher": []
    },
    {
      "label": "migrate:create",
      "detail": "创建数据库迁移",
      "type": "shell",
      "command": "alembic",
      "args": ["revision", "--autogenerate", "-m", "${input:migrationMessage}"],
      "problemMatcher": []
    },
    {
      "label": "migrate:upgrade",
      "detail": "执行数据库迁移",
      "type": "shell",
      "command": "alembic",
      "args": ["upgrade", "head"],
      "problemMatcher": []
    },
    {
      "label": "migrate:downgrade",
      "detail": "回滚一个迁移",
      "type": "shell",
      "command": "alembic",
      "args": ["downgrade", "-1"],
      "problemMatcher": []
    },
    {
      "label": "pre-commit",
      "detail": "提交前检查",
      "dependsOn": ["lint", "typecheck", "test"],
      "dependsOrder": "sequence",
      "problemMatcher": []
    },
    {
      "label": "clean",
      "detail": "清理缓存文件",
      "type": "shell",
      "command": "find",
      "args": [
        ".", "-type", "d",
        "-name", "__pycache__",
        "-o", "-name", ".pytest_cache",
        "-o", "-name", ".mypy_cache",
        "-o", "-name", ".ruff_cache"
      ],
      "windows": {
        "command": "cmd",
        "args": ["/c", "for", "/r", ".", "%i", "in", ("__pycache__", ".pytest_cache") "do", "rmdir", "/s", "/q", "%i"]
      }
    }
  ]
}
```

**3. `.vscode/launch.json` —— Python 调试配置**

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "FastAPI 开发",
      "type": "python",
      "request": "launch",
      "module": "uvicorn",
      "args": [
        "app.main:app",
        "--reload",
        "--host", "0.0.0.0",
        "--port", "8000"
      ],
      "jinja": true,
      "console": "integratedTerminal",
      "justMyCode": true,
      "env": {
        "PYTHONPATH": "${workspaceFolder}",
        "ENV": "development"
      },
      "envFile": "${workspaceFolder}/.env"
    },
    {
      "name": "FastAPI 生产模式",
      "type": "python",
      "request": "launch",
      "module": "uvicorn",
      "args": [
        "app.main:app",
        "--host", "0.0.0.0",
        "--port", "8000",
        "--workers", "4"
      ],
      "console": "integratedTerminal",
      "justMyCode": false
    },
    {
      "name": "Python: 当前文件",
      "type": "python",
      "request": "launch",
      "program": "${file}",
      "console": "integratedTerminal",
      "justMyCode": true,
      "env": {
        "PYTHONPATH": "${workspaceFolder}"
      }
    },
    {
      "name": "Python: 交互式 Shell",
      "type": "python",
      "request": "launch",
      "program": "${workspaceFolder}/app/shell.py",
      "console": "integratedTerminal",
      "justMyCode": true
    },
    {
      "name": "pytest: 当前文件",
      "type": "python",
      "request": "launch",
      "module": "pytest",
      "args": ["${file}", "-v", "-s"],
      "console": "integratedTerminal",
      "justMyCode": false
    },
    {
      "name": "pytest: 当前测试（光标处）",
      "type": "python",
      "request": "launch",
      "module": "pytest",
      "args": ["${file}::${selectedText}", "-v", "-s"],
      "console": "integratedTerminal",
      "justMyCode": false
    },
    {
      "name": "Alembic 迁移",
      "type": "python",
      "request": "launch",
      "module": "alembic",
      "args": ["upgrade", "head"],
      "console": "integratedTerminal",
      "justMyCode": true
    },
    {
      "name": "附加到 Docker 容器",
      "type": "python",
      "request": "attach",
      "connect": {
        "host": "localhost",
        "port": 5678
      },
      "pathMappings": [
        {
          "localRoot": "${workspaceFolder}",
          "remoteRoot": "/app"
        }
      ],
      "justMyCode": false
    },
    {
      "name": "附加到远程进程",
      "type": "python",
      "request": "attach",
      "connect": {
        "host": "remote-server",
        "port": 5678
      },
      "pathMappings": [
        {
          "localRoot": "${workspaceFolder}",
          "remoteRoot": "/home/user/app"
        }
      ],
      "justMyCode": true
    }
  ],
  "compounds": []
}
```

**4. `.vscode/extensions.json`**

```json
{
  "recommendations": [
    "ms-python.python",
    "ms-python.vscode-pylance",
    "charliermarsh.ruff",
    "ms-python.black-formatter",
    "ms-python.mypy-type-checker",
    "ms-python.isort",
    "mtxr.sqltools",
    "mtxr.sqltools-driver-pg",
    "cweijan.vscode-database-client2",
    "ms-azuretools.vscode-docker",
    "ms-vscode-remote.remote-containers",
    "redhat.vscode-yaml",
    "tamasfe.even-better-toml",
    "eamodio.gitlens",
    "streetsidesoftware.code-spell-checker",
    "gruntfuggly.todo-tree"
  ]
}
```

**5. `.vscode/python.code-snippets`**

```json
{
  "FastAPI 路由": {
    "prefix": "faroute",
    "body": [
      "@router.${1|get,post,put,delete,patch|}(\"${2:/path}\")",
      "async def ${3:handler}(${4:request}: Request) -> ${5:dict}:",
      "    \"\"\"${6:文档字符串}\"\"\"",
      "    ${7:pass}",
      "    return {\"message\": \"${8:ok}\"}",
      ""
    ]
  },
  "Pydantic 模型": {
    "prefix": "pymodel",
    "body": [
      "from pydantic import BaseModel, Field",
      "",
      "class ${1:ModelName}(BaseModel):",
      "    \"\"\"${2:文档字符串}\"\"\"",
      "",
      "    ${3:field}: ${4:str} = Field(..., description=\"${5:字段描述}\")",
      "    ${0}",
      ""
    ]
  },
  "SQLAlchemy 模型": {
    "prefix": "salmodel",
    "body": [
      "from sqlalchemy import Column, Integer, String, DateTime, func",
      "from app.core.database import Base",
      "",
      "class ${1:ModelName}(Base):",
      "    \"\"\"${2:文档字符串}\"\"\"",
      "",
      "    __tablename__ = \"${3:table_name}\"",
      "",
      "    id = Column(Integer, primary_key=True, index=True)",
      "    ${4:name} = Column(String(${5:255}), nullable=False)",
      "    created_at = Column(DateTime, server_default=func.now())",
      "    updated_at = Column(DateTime, server_default=func.now(), onupdate=func.now())",
      ""
    ]
  },
  "pytest 测试函数": {
    "prefix": "ptest",
    "body": [
      "def test_${1:name}():",
      "    \"\"\"测试 ${2:描述}\"\"\"",
      "    # Arrange",
      "    ${3:# 准备}",
      "",
      "    # Act",
      "    ${4:# 执行}",
      "",
      "    # Assert",
      "    assert ${5:actual} == ${6:expected}",
      ""
    ]
  },
  "pytest 异步测试": {
    "prefix": "ptestasync",
    "body": [
      "import pytest",
      "",
      "@pytest.mark.asyncio",
      "async def test_${1:name}():",
      "    \"\"\"测试 ${2:描述}\"\"\"",
      "    # Arrange",
      "    ${3:# 准备}",
      "",
      "    # Act",
      "    ${4:result} = await ${5:func}()",
      "",
      "    # Assert",
      "    assert ${4:result} == ${6:expected}",
      ""
    ]
  },
  "依赖注入": {
    "prefix": "fadep",
    "body": [
      "from fastapi import Depends",
      "",
      "def ${1:get_dependency}():",
      "    \"\"\"${2:文档字符串}\"\"\"",
      "    ${3:pass}",
      "    return ${4:value}",
      ""
    ]
  },
  "类型提示函数": {
    "prefix": "deff",
    "body": [
      "def ${1:func_name}(${2:args}) -> ${3:ReturnType}:",
      "    \"\"\"${4:文档字符串}",
      "",
      "    Args:",
      "        ${2:args}: ${5:参数描述}",
      "",
      "    Returns:",
      "        ${3:ReturnType}: ${6:返回值描述}",
      "    \"\"\"",
      "    ${0:pass}",
      ""
    ]
  }
}
```

### 总结

- Python 配置核心是解释器管理，`python.defaultInterpreterPath` 指向项目虚拟环境
- Pylance 的 `typeCheckingMode: "strict"` 提供严格的类型检查
- Ruff 替代 Black + isort + flake8，一站式解决格式化和 Lint
- pytest 配置与 VS Code Testing 面板深度集成，支持测试发现和调试
- FastAPI 调试使用 `module: "uvicorn"`，`jinja: true` 启用模板调试
- 数据库迁移通过 `alembic` 任务自动化
- Docker/远程调试通过 `pathMappings` 解决路径差异
- 代码片段覆盖路由、模型、测试等高频场景，加速开发

---

## 课程总结

恭喜你完成了《.vscode 配置完全教程》的全部 18 讲！让我们回顾整个学习旅程：

### 知识体系回顾

| 章节 | 核心知识点 |
|------|-----------|
| 第一章 入门基础 | `.vscode` 目录认知、`settings.json` 基础、配置作用域 |
| 第二章 编辑器核心 | 外观行为、文件管理、语言服务与格式化 |
| 第三章 任务系统 | `tasks.json`、变量系统、复合任务、问题匹配器 |
| 第四章 调试配置 | `launch.json`、复合调试、多语言调试实战 |
| 第五章 个性化 | 代码片段、快捷键定制 |
| 第六章 团队协作 | 扩展推荐、多根工作区、远程开发 |
| 第七章 实战整合 | 前端项目、Python 后端项目完整配置 |

### 核心配置文件速查

| 文件 | 作用 | 关键字段 |
|------|------|---------|
| `settings.json` | 编辑器与扩展配置 | `editor.*`、`files.*`、语言特定配置 |
| `tasks.json` | 自动化任务 | `label`、`command`、`args`、`problemMatcher` |
| `launch.json` | 调试配置 | `type`、`request`、`program`、`preLaunchTask` |
| `extensions.json` | 扩展推荐 | `recommendations`、`unwantedRecommendations` |
| `*.code-snippets` | 代码片段 | `prefix`、`body`、占位符语法 |
| `keybindings.json` | 快捷键 | `key`、`command`、`when` |
| `*.code-workspace` | 多根工作区 | `folders`、`settings`、`launch` |

### 最佳实践清单

1. **始终提交 `.vscode` 到版本控制**（排除个人偏好设置）
2. **使用工作区级配置覆盖用户配置**，确保团队一致
3. **善用变量**（`${workspaceFolder}`、`${file}` 等）保证配置可移植
4. **定义 `preLaunchTask`** 实现编译后调试的自动化
5. **配置 `problemMatcher`** 让错误自动跳转到问题面板
6. **为团队定义代码片段**，统一代码风格
7. **利用 `serverReadyAction`** 实现 Web 服务的一键调试
8. **远程调试务必配置路径映射**，避免断点失效
9. **分层管理扩展**（必需 vs 推荐），避免扩展过载
10. **定期审查配置**，移除过时设置，保持配置精简

### 进阶学习方向

- **VS Code 扩展开发**：学习开发自定义扩展，实现项目特定功能
- **Language Server Protocol**：深入理解语言服务的底层协议
- **Debug Adapter Protocol**：为自定义语言开发调试适配器
- **Dev Containers 进阶**：构建完整的容器化开发环境
- **GitHub Codespaces**：云端开发环境的配置与管理

希望这门课程能帮助你掌握 `.vscode` 配置的完整体系，为你的项目打造专业级的开发环境。配置是一门实践的艺术，多动手、多尝试，你会逐渐形成自己的配置风格和最佳实践。
