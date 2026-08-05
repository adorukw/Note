# Pandoc 系统教程：从入门到精通

> 一本像教科书一样循序渐进的 Pandoc 学习指南
> 共 6 章 · 32 讲 · 涵盖基础语法、格式转换、模板定制、过滤器编程与实战工作流

---

## 课程总览

### 学习目标

本课程旨在帮助读者从零开始系统掌握 Pandoc——这款被誉为"文档转换界的瑞士军刀"的命令行工具。通过 32 讲的学习，你将能够：

1. **理解 Pandoc 的工作原理**：掌握其"中间抽象语法树（AST）"的核心思想，理解为何一种工具能支持数十种格式互转。
2. **熟练使用 Pandoc Markdown**：掌握 Pandoc 对标准 Markdown 的扩展语法，包括元数据、定义列表、表格、脚注、数学公式、引用等。
3. **完成多格式转换实战**：能在 Markdown、HTML、PDF、Word、LaTeX、EPUB、幻灯片等格式之间自由转换。
4. **定制模板与样式**：通过自定义模板、参考文档、CSS 等手段，让输出文档符合个人或机构的排版规范。
5. **编写与使用过滤器**：掌握 Lua 过滤器这一强大的扩展机制，实现自动化文档处理。
6. **构建完整工作流**：将 Pandoc 融入学术论文写作、电子书制作、文档网站生成、CI/CD 流水线等真实场景。

### 适用读者

- 需要频繁在多种文档格式间转换的写作者、研究者、技术作者
- 希望用 Markdown 作为"单一真相来源"进行多目标发布的内容创作者
- 对文档自动化处理、静态网站生成、学术出版工具链感兴趣的开发者
- 任何希望摆脱 Word/PDF 反复手动排版之苦的人

### 学习建议

- **循序渐进**：本课程按"基础 → 核心 → 实战 → 进阶 → 高级"的顺序编排，建议按顺序学习。
- **动手实践**：每讲都配有可运行的命令与示例，请在本机安装 Pandoc 后跟随操作。
- **建立素材库**：建议准备一个 Markdown 笔记文件，边学边把每讲的语法点记录下来，作为日后查阅的速查表。

---

## 详细章节目录

### 第 1 章 Pandoc 基础入门
- 第 1 讲：Pandoc 是什么——简介与安装
- 第 2 讲：第一次文档转换——基本命令与流程
- 第 3 讲：支持的格式全景——输入与输出格式地图
- 第 4 讲：命令行参数与选项基础

### 第 2 章 Pandoc Markdown 语法精讲
- 第 5 讲：Pandoc Markdown 扩展语法概览
- 第 6 讲：文档元数据与 YAML 块
- 第 7 讲：标题层级、章节编号与目录
- 第 8 讲：列表、定义表与任务列表
- 第 9 讲：表格的多种写法
- 第 10 讲：代码块、语法高亮与行号
- 第 11 讲：链接、图片、脚注与行内引用

### 第 3 章 格式转换实战
- 第 12 讲：Markdown 转 HTML
- 第 13 讲：Markdown 转 PDF（LaTeX 引擎）
- 第 14 讲：Markdown 转 Word（docx）
- 第 15 讲：Markdown 转 LaTeX
- 第 16 讲：Markdown 转 EPUB 电子书
- 第 17 讲：Markdown 转幻灯片（Beamer / reveal.js）

### 第 4 章 模板与样式定制
- 第 18 讲：模板系统与变量机制
- 第 19 讲：自定义 HTML 模板
- 第 20 讲：LaTeX 模板与中文支持
- 第 21 讲：docx 参考文档与样式映射
- 第 22 讲：CSS 样式与网页美化

### 第 5 章 过滤器与引文
- 第 23 讲：过滤器原理与生态
- 第 24 讲：Lua 过滤器基础语法
- 第 25 讲：常用 Lua 过滤器实战
- 第 26 讲：引文与参考文献（BibTeX / CSL）
- 第 27 讲：JSON 过滤器与 Pandoc Python

### 第 6 章 高级应用与工作流
- 第 28 讲：默认配置文件 defaults.yaml
- 第 29 讲：学术论文写作工作流
- 第 30 讲：电子书制作工作流
- 第 31 讲：文档网站生成与 CI/CD 集成
- 第 32 讲：性能优化与最佳实践

---

# 第 1 章 Pandoc 基础入门

本章是整个课程的起点。我们将认识 Pandoc 这款工具的本质，完成安装，执行第一次文档转换，并熟悉它的命令行界面。学完本章后，你将拥有一个可用的 Pandoc 环境，并理解它"以中间 AST 为枢纽"的核心设计思想。

---

## 第 1 讲：Pandoc 是什么——简介与安装

### 概念

Pandoc 是一款由 John MacFarlane（加州大学伯克利分校哲学教授）开发的开源命令行文档转换工具，使用 Haskell 编写。它的核心使命只有一个：**在数十种文档格式之间自由转换**。截至当前版本，Pandoc 能够读取约 40 种输入格式、写入约 60 种输出格式，覆盖 Markdown、HTML、LaTeX、Word（docx）、PDF、EPUB、OpenOffice、reStructuredText、AsciiDoc、Org mode、MediaWiki 标记、JSON 等几乎所有主流文档格式。

与"在线格式转换网站"或"单一用途脚本"不同，Pandoc 不是一个简单的格式包装器，而是一个**完整的文档处理引擎**。它内部维护一棵抽象语法树（AST），所有输入格式先被解析为这棵统一的 AST，再由 AST 序列化成目标格式。这种"漏斗式"架构使得新增一种格式只需编写一个读取器或写入器，而无需为每对格式组合单独编写转换逻辑。

### 原理

Pandoc 的转换流程可以概括为三步：

1. **读取（Read）**：调用对应输入格式的 Reader，将源文档解析为 Pandoc AST。AST 是一棵由 `Para`（段落）、`Header`（标题）、`CodeBlock`（代码块）、`Table`（表格）等节点构成的树。
2. **处理（Process）**：可选地运行一系列过滤器（Filter）或 Lua 脚本，对 AST 进行变换，例如自动编号、替换链接、插入元数据等。
3. **写入（Write）**：调用对应输出格式的 Writer，将 AST 序列化为目标格式的文本或二进制。

这种设计带来三个重要后果：第一，格式之间是"多对多"关系而非"一对一"，新增格式的边际成本极低；第二，过滤器可以在 AST 层面统一处理所有格式，无需为每种格式重复编写逻辑；第三，Pandoc 可以输出 JSON 形式的 AST，从而允许外部程序（Python、Ruby、Node.js）参与文档处理流水线。

### 例子

**安装 Pandoc**（以 Ubuntu/Debian 为例）：

```bash
# 方式一：系统包管理器（版本可能较旧）
sudo apt update && sudo apt install -y pandoc

# 方式二：下载官方预编译二进制（推荐，版本最新）
# 访问 https://github.com/jgm/pandoc/releases 下载对应平台的 .deb 或 .tar.gz
sudo dpkg -i pandoc-3.x.x-amd64.deb
```

macOS 用户可使用 Homebrew：

```bash
brew install pandoc
```

Windows 用户可下载 `.msi` 安装包，或使用 Chocolatey：

```powershell
choco install pandoc
```

**验证安装**：

```bash
pandoc --version
# 输出类似：
# pandoc 3.x
# Features: +PDF ...
```

**查看支持的格式**：

```bash
pandoc --list-input-formats    # 列出所有可读格式
pandoc --list-output-formats   # 列出所有可写格式
```

### 总结

- Pandoc 是一款基于 Haskell 的开源文档转换引擎，支持数十种格式互转。
- 其核心是"中间 AST"设计：读取 → 处理 → 写入三阶段流水线。
- 安装方式因平台而异，推荐使用官方预编译二进制以获得最新版本。
- 学完本讲后，请确保 `pandoc --version` 能正常输出，再进入下一讲。

---

## 第 2 讲：第一次文档转换——基本命令与流程

### 概念

Pandoc 的基本用法极其简洁，核心命令模板为：

```bash
pandoc 输入文件 -o 输出文件
```

其中 `-o`（output）指定输出文件名，Pandoc 会根据文件扩展名自动推断目标格式。例如 `report.md -o report.html` 会把 Markdown 转为 HTML，`report.md -o report.docx` 会转为 Word 文档。这种"扩展名即格式"的设计让命令极其直观。

### 原理

Pandoc 在执行转换时，会做以下几件事：

1. **推断输入格式**：默认根据输入文件扩展名推断；若扩展名不明确，可用 `-f`（from）显式指定，如 `-f markdown`。
2. **推断输出格式**：根据 `-o` 指定的文件扩展名推断；可用 `-t`（to）显式指定，如 `-t html`。
3. **应用默认扩展集**：每种格式都有一组默认启用的扩展（如 Markdown 默认启用 `pipe_tables`、`yaml_metadata_block` 等），扩展决定了语法的宽松或严格程度。
4. **执行转换并写入**：将 AST 序列化后写入输出文件；若是二进制格式（docx/epub/pdf），则打包成对应容器。

理解"扩展名推断"这一点很重要：它让简单命令足够简单，但当你需要精确控制时，`-f` 和 `-t` 始终可以覆盖默认行为。

### 例子

准备一个简单的 Markdown 文件 `hello.md`：

```markdown
# 你好，Pandoc

这是一段**加粗**与*斜体*混排的文字。

- 列表项一
- 列表项二
- 列表项三

> 引用：Pandoc 是文档转换的瑞士军刀。
```

**转换为 HTML**：

```bash
pandoc hello.md -o hello.html
```

打开 `hello.html`，你会看到一个完整的 HTML 文档（含 `<html>`、`<head>`、`<body>` 标签）。

**转换为 Word 文档**：

```bash
pandoc hello.md -o hello.docx
```

用 Word 或 LibreOffice 打开 `hello.docx`，可以看到标题、加粗、列表、引用都被正确渲染。

**转换为 PDF（需要 LaTeX 引擎）**：

```bash
pandoc hello.md -o hello.pdf
```

> 注意：转 PDF 需要系统安装 TeX 发行版（如 TeX Live、MiKTeX）。若未安装，会报错 `pdflatex not found`。详见第 13 讲。

**显式指定格式**（当扩展名无法推断时）：

```bash
# 从标准输入读取 Markdown，输出 HTML 到标准输出
echo "# 标题" | pandoc -f markdown -t html
# 输出：<h1 id="标题">标题</h1>
```

### 总结

- Pandoc 基本命令：`pandoc 输入 -o 输出`，扩展名决定格式。
- `-f`/`-t` 可显式指定输入/输出格式，覆盖扩展名推断。
- 转换流程：读取 → AST → 写入，对用户透明。
- 转 PDF 需要额外的 LaTeX 引擎，这是初学者最常遇到的"第一个坑"。
- 建议先用 Markdown ↔ HTML/DOCX 练手，再尝试 PDF。

---

## 第 3 讲：支持的格式全景——输入与输出格式地图

### 概念

Pandoc 支持的格式数量庞大，初学者容易感到眼花缭乱。实际上，这些格式可以按"家族"分类记忆。掌握这张"格式地图"后，你就能在任何场景下迅速判断该用哪种格式作为输入、哪种作为输出。

### 原理

Pandoc 的格式支持分为三个层次：

1. **完整读写**：既能读又能写，且尽量保留语义。如 Markdown、HTML、LaTeX、docx、org、rst 等。
2. **只读**：只能作为输入解析，不能作为输出。如某些专有格式。
3. **只写**：只能作为输出，不能作为输入。如 PDF（因为 PDF 是排版结果，难以逆向还原为结构化文档）。

此外，Pandoc 引入了"扩展（extension）"机制：每种格式有一组可开关的语法扩展，例如 Markdown 的 `+pipe_tables` 启用管道表格、`-raw_html` 禁用原始 HTML 嵌入。格式与扩展的组合写作 `markdown+pipe_tables+yaml_metadata_block`，这种表达方式让你能精确控制解析行为。

### 例子

**主流格式家族速查表**：

| 家族 | 输入格式 | 输出格式 |
|------|---------|---------|
| 轻量标记 | markdown, commonmark, gfm, rst, asciidoc, org, ipynb | markdown, commonmark, gfm, rst, asciidoc, org, ipynb |
| 网页 | html, html5, jira | html, html4, html5 |
| 排版 | latex, context, typst | latex, context, typst |
| 富文本 | docx, odt, rtf, epub | docx, odt, rtf, epub |
| 终端 | man, ms | man, ms |
| 幻灯片 | — | beamer, revealjs, s5, slideous, slidy, pptx |
| 数据 | json (AST) | json (AST) |
| 终态 | — | pdf（通过 LaTeX/ConTeXt/Typst/weasyprint/wkhtmltopdf） |

**查看某种格式的默认扩展**：

```bash
pandoc --list-extensions=markdown
# 输出类似：
# +pipe_tables        （默认启用）
# +yaml_metadata_block
# -raw_tex            （默认禁用）
# ...
```

**组合扩展以启用 GFM 风格**：

```bash
pandoc input.md -f markdown+gfm_auto_identifiers -t html -o output.html
```

### 总结

- Pandoc 格式可按家族分类记忆：轻量标记、网页、排版、富文本、幻灯片、数据、终态。
- PDF 是"终态"格式，只能写不能读；它依赖外部引擎（LaTeX/Typst/weasyprint 等）。
- 每种格式有一组可开关的"扩展"，用 `+`/`-` 前缀控制。
- 用 `--list-input-formats`、`--list-output-formats`、`--list-extensions=格式名` 随时查阅。
- 熟悉这张地图后，遇到新需求时第一反应就是"它属于哪个家族？默认扩展够不够用？"

---

## 第 4 讲：命令行参数与选项基础

### 概念

Pandoc 提供了上百个命令行选项，但日常使用中真正高频的只有十几个。本讲聚焦这些核心选项，让你能组合出 80% 场景下所需的命令。掌握它们之后，再阅读 `pandoc --help` 的完整列表就不会感到陌生。

### 原理

Pandoc 的选项大致可分为五类：

1. **格式控制**：`-f`/`--from`、`-t`/`--to`，指定输入输出格式与扩展。
2. **输出控制**：`-o`/`--output`、`-s`/`--standalone`（生成完整文档而非片段）、`--toc`/`--table-of-contents`（生成目录）。
3. **元数据**：`--metadata`/`-M`（设置元数据键值）、`--metadata-file`（从 YAML/JSON 文件加载元数据）。
4. **模板与样式**：`--template`、`--css`、`--reference-doc`、`-V`/`--variable`（向模板注入变量）。
5. **过滤器与引文**：`--filter`、`--lua-filter`、`--citeproc`、`--bibliography`、`--csl`。

理解选项分类的意义在于：当你想实现某个效果时，能迅速定位到该用哪一类选项，而不是在几十个参数里盲目搜索。

### 例子

**生成完整 HTML 文档（含 head/body）**：

```bash
pandoc hello.md -s -o hello.html
# -s = --standalone，生成完整文档而非片段
```

**生成带目录的文档**：

```bash
pandoc report.md -s --toc -o report.html
```

**设置文档标题**：

```bash
pandoc hello.md -s -M title="我的第一个 Pandoc 文档" -o hello.html
```

**指定 CSS 样式表**：

```bash
pandoc hello.md -s --css=style.css -o hello.html
```

**注入模板变量**：

```bash
pandoc report.md -s -V lang=zh-CN -V author="张三" -o report.html
```

**组合多个选项的典型命令**：

```bash
pandoc report.md \
  -s \
  --toc \
  --toc-depth=3 \
  -M title="季度报告" \
  -M author="张三" \
  -M date="2026-08-04" \
  --css=style.css \
  -o report.html
```

**查看帮助与版本**：

```bash
pandoc --help          # 查看所有选项的简要说明
pandoc --version       # 查看版本与编译特性
pandoc --list-extensions=markdown  # 列出某格式的扩展
```

### 总结

- Pandoc 选项虽多，但高频核心只有十几个，按"格式/输出/元数据/模板/过滤器"五类记忆即可。
- `-s`（standalone）是最容易被忽略却极常用的选项，决定输出是完整文档还是片段。
- `--toc`、`-M`、`-V`、`--css` 是日常组合的常客。
- 善用 `pandoc --help` 与 `man pandoc`（Linux/macOS）随时查阅完整选项列表。
- 当命令变长时，用 `\` 换行书写，或使用第 28 讲将介绍的 `defaults.yaml` 来固化常用参数。

---

# 第 2 章 Pandoc Markdown 语法精讲

Markdown 是 Pandoc 最常用的输入格式，但 Pandoc 的 Markdown 并非"标准 Markdown"——它在标准基础上扩展了大量语法，使其足以承载学术论文、技术文档、电子书等复杂内容。本章是全书的"语法核心"，共 7 讲，覆盖元数据、标题、列表、表格、代码、链接等所有关键语法点。学完后你将能用一份 Markdown 源文件驱动 HTML、PDF、Word、EPUB 等多种输出。

---

## 第 5 讲：Pandoc Markdown 扩展语法概览

### 概念

Pandoc Markdown 是 Pandoc 对原始 Markdown 的扩展方言，通过"扩展（extension）"机制在标准 Markdown 之上叠加了元数据块、定义列表、表格、脚注、数学公式、引用、原始块、属性、行内属性等数十种语法能力。这些扩展让 Markdown 从"博客笔记语法"升级为"足以承载正式出版物的语法"。

### 原理

Pandoc 的扩展机制遵循"开关式"设计：每个扩展是一个命名开关，默认状态为开（`+`）或关（`-`）。你可以通过格式字符串 `markdown+扩展名` 或 `markdown-扩展名` 来精确控制。例如：

- `markdown` 等价于启用 Pandoc 默认的一组扩展。
- `markdown-raw_html` 表示禁用原始 HTML 嵌入。
- `markdown+pipe_tables+yaml_metadata_block` 表示在默认基础上确保启用管道表格与 YAML 元数据块。
- `gfm` 是 GitHub Flavored Markdown 的别名，等价于一组特定扩展的组合。

这种设计的好处是：你可以在同一份源文件中按需"收紧"或"放宽"语法，而不必切换到完全不同的方言。

### 例子

**查看 Markdown 默认启用的扩展**：

```bash
pandoc --list-extensions=markdown
```

输出会列出形如 `+pipe_tables`、`+yaml_metadata_block`、`-raw_tex` 的条目，`+` 表示默认启用，`-` 表示默认禁用。

**禁用某扩展**（例如禁止内联 HTML）：

```bash
pandoc input.md -f markdown-raw_html -t html -o output.html
```

**启用某扩展**（例如启用 GitHub 风格的自动标题 ID）：

```bash
pandoc input.md -f markdown+gfm_auto_identifiers -t html -o output.html
```

**使用预设方言**：

```bash
pandoc input.md -f gfm -t html -o output.html      # GitHub 风格
pandoc input.md -f commonmark -t html -o output.html  # CommonMark 严格模式
```

### 总结

- Pandoc Markdown = 标准 Markdown + 一组可开关的扩展。
- 扩展用 `+`/`-` 前缀控制，可组合：`markdown+ext1-ext2`。
- 常用预设方言：`markdown`（Pandoc 默认）、`gfm`（GitHub）、`commonmark`（严格）。
- 用 `--list-extensions=格式名` 随时查阅某格式的扩展清单。
- 理解扩展机制后，后续每一讲的具体语法都对应一个或多个扩展名，便于精确控制。

---

## 第 6 讲：文档元数据与 YAML 块

### 概念

Pandoc Markdown 允许在文档顶部用 YAML 块声明文档的"元数据"：标题、作者、日期、版权信息、自定义变量等。这些元数据不会出现在正文中，但会被传递给模板，用于生成 HTML 的 `<title>`、LaTeX 的 `\title`、docx 的文档属性等。这是 Markdown 从"笔记"升级为"正式文档"的关键能力。

### 原理

Pandoc 在解析 Markdown 时，若文档开头出现以三条 `---` 开始、以三条 `---` 或 `...` 结束的块，会将其识别为 YAML 元数据块（由 `yaml_metadata_block` 扩展控制）。块内的键值对被解析为文档的元数据树，常用字段包括：

- `title`：文档标题
- `author`：作者（可为列表）
- `date`：日期
- `subtitle`：副标题
- `abstract`：摘要
- `keywords`：关键词
- `lang`：文档语言（如 `zh-CN`）
- `rights`：版权声明

除内置字段外，任何自定义键都会作为模板变量传递，可在模板中通过 `$变量名$` 引用。

### 例子

**带完整元数据的 Markdown 文档**：

```markdown
---
title: "Pandoc 入门指南"
subtitle: "从零到一"
author:
  - 张三
  - 李四
date: 2026-08-04
lang: zh-CN
keywords:
  - 文档转换
  - Markdown
rights: "© 2026 示例出版社，保留所有权利。"
abstract: |
  本指南系统介绍 Pandoc 的安装、语法与工作流，
  适合需要频繁进行多格式文档转换的写作者。
---

# 第一章 引言

正文从这里开始……
```

**用命令行覆盖元数据**：

```bash
pandoc input.md -M date="$(date +%F)" -o output.html
```

**从外部 YAML 文件加载元数据**：

```bash
pandoc input.md --metadata-file=meta.yaml -o output.html
```

`meta.yaml` 内容示例：

```yaml
title: 来自外部文件的标题
author: 王五
institute: 示例大学
```

### 总结

- YAML 元数据块以 `---` 开始、`---` 或 `...` 结束，位于文档顶部。
- 内置字段：`title`/`author`/`date`/`lang`/`abstract`/`keywords` 等，会被各格式 Writer 自动使用。
- 自定义字段会作为模板变量传递，可在模板中通过 `$变量名$` 引用。
- `-M` 可在命令行覆盖单个字段，`--metadata-file` 可从外部 YAML 批量加载。
- 元数据是"单一源、多目标发布"的基础：写一次，所有输出格式共享。

---

## 第 7 讲：标题层级、章节编号与目录

### 概念

标题是文档的骨架。Pandoc Markdown 支持 6 级标题（`#` 到 `######`），并能自动为标题生成 ID（用于目录跳转与交叉引用）、自动编号章节、自动生成目录（TOC）。这三项能力让 Markdown 文档具备正式出版物的导航结构。

### 原理

Pandoc 处理标题时涉及三个机制：

1. **自动 ID 生成**：由 `auto_identifiers` 扩展控制。每个标题会根据其文本自动生成一个 HTML 锚点 ID，规则为：转小写、移除标点、空格转连字符、必要时加数字后缀避免冲突。例如标题"你好，世界"会得到 ID `你好世界`。
2. **章节编号**：由 `--number-sections` 选项触发。Pandoc 会按标题层级自动编号，如 1、1.1、1.1.1。
3. **目录生成**：由 `--toc`/`--table-of-contents` 选项触发，配合 `--toc-depth=N` 控制收录到第几级标题。

这三者协同工作：目录中的每一项是一个指向标题 ID 的超链接，点击即可跳转。

### 例子

**Markdown 标题写法**：

```markdown
# 一级标题

## 二级标题

### 三级标题
```

**手动指定标题 ID**（覆盖自动 ID）：

```markdown
# 我的标题 {#my-custom-id}
```

**生成带编号与目录的 HTML**：

```bash
pandoc report.md -s --number-sections --toc --toc-depth=3 -o report.html
```

**交叉引用标题**：

```markdown
详见 [第 3 节](#my-custom-id)。
```

**关闭自动编号但保留目录**：

```bash
pandoc report.md -s --toc --toc-depth=2 -o report.html
```

### 总结

- 标题用 `#`～`######` 表示 6 级；Pandoc 自动生成 ID，可用 `{#自定义id}` 覆盖。
- `--number-sections` 自动编号；`--toc` 生成目录；`--toc-depth=N` 控制目录深度。
- 目录项是锚点链接，依赖标题 ID，因此自动 ID 与目录是协同机制。
- 在 LaTeX 输出中，`--number-sections` 会触发 `\section` 等命令的自动编号；在 docx 中会应用"标题 1/2/3"样式。
- 长文档务必启用 `--toc`，这是读者导航的基础设施。

---

## 第 8 讲：列表、定义表与任务列表

### 概念

列表是文档最常见的结构元素。Pandoc Markdown 支持四种列表：无序列表、有序列表、定义列表（definition list）、任务列表（task list）。其中定义列表与任务列表是 Pandoc 对标准 Markdown 的扩展，分别由 `definition_lists` 与 `task_lists` 扩展控制。

### 原理

- **无序列表**：以 `-`、`*` 或 `+` 开头，缩进表示嵌套。
- **有序列表**：以数字+`.` 或 `)` 开头，如 `1.`、`2)`。Pandoc 会按源文件中的数字输出，而非自动重排。
- **定义列表**：每项由"术语 + 冒号 + 缩进定义"构成，用于术语表、词汇表。
- **任务列表**：以 `- [ ]` 或 `- [x]` 开头，表示未完成/已完成任务，渲染为复选框（HTML）或方框符号（LaTeX）。

列表的"松紧"规则：若列表项之间有空行，则每项被包为 `<p>`（松散列表）；若无空行，则为紧凑列表。这一细节会影响 HTML 与 LaTeX 的渲染间距。

### 例子

**无序列表与嵌套**：

```markdown
- 水果
  - 苹果
  - 香蕉
- 蔬菜
  - 白菜
  - 萝卜
```

**有序列表**：

```markdown
1. 第一步
2. 第二步
3. 第三步
```

**定义列表**：

```markdown
Pandoc
:   一款文档转换工具。

Markdown
:   一种轻量标记语言。
:   由 John Gruber 于 2004 年创造。
```

**任务列表**：

```markdown
- [x] 安装 Pandoc
- [x] 编写第一个 Markdown 文件
- [ ] 转换为 PDF
- [ ] 发布到网站
```

**紧凑列表 vs 松散列表**：

```markdown
- 紧凑：项与项之间无空行
- 紧凑：渲染为单行间距

- 松散：项与项之间有空行

- 松散：渲染为段落间距
```

### 总结

- 四种列表：无序、有序、定义、任务，后两者是 Pandoc 扩展。
- 缩进表示嵌套；有序列表按源数字输出而非自动重排。
- 列表项间空行决定"松紧"，影响渲染间距。
- 任务列表在 HTML 中渲染为复选框，在 LaTeX 中渲染为方框符号。
- 定义列表非常适合做术语表、FAQ、词汇表。

---

## 第 9 讲：表格的多种写法

### 概念

表格是技术文档与数据展示的核心结构。Pandoc Markdown 支持四种表格语法：管道表格（pipe tables）、网格表格（grid tables）、简单表格（simple tables）、多线表格（multiline tables）。其中管道表格最常用，网格表格最灵活，简单表格最简洁，多线表格支持单元格内多段落。

### 原理

四种表格各有特点：

1. **管道表格**：用 `|` 分隔列，`---` 标记表头分隔行，支持对齐标记 `:---`、`:---:`、`---:`。最易书写，但单元格内不能含多段落。
2. **网格表格**：用 `+`、`-`、`|` 绘制网格，可含多段落、任意复杂内容，但书写繁琐。
3. **简单表格**：省略 `|`，仅用空格对齐，最简洁但要求列宽固定。
4. **多线表格**：类似简单表格，但允许单元格内多段落。

Pandoc 还支持"表格标题（caption）"：在表格下方以 `: 表格说明` 开头的行会被识别为表题，便于编号与引用。

### 例子

**管道表格**：

```markdown
| 姓名 | 年龄 | 城市   |
|------|-----:|:------:|
| 张三 |   28 | 北京   |
| 李四 |   34 | 上海   |
```

对齐标记：`:---` 左对齐、`---:` 右对齐、`:---:` 居中。

**简单表格**：

```markdown
  姓名   年龄   城市
  张三    28   北京
  李四    34   上海

  ----   ----  ----
```

**网格表格**（支持多段落单元格）：

```markdown
+----------+----------+
| 姓名     | 备注     |
+==========+==========+
| 张三     | 第一段。 |
|          |          |
|          | 第二段。 |
+----------+----------+
| 李四     | 普通项   |
+----------+----------+
```

**带标题的表格**：

```markdown
| 姓名 | 成绩 |
|------|-----:|
| 张三 |   95 |
| 李四 |   88 |

: 学生成绩表（满分 100）
```

**在 LaTeX 中获得书签式表格**（跨页长表格）需用 `longtable` 选项，详见第 15 讲。

### 总结

- 四种表格：管道（最常用）、网格（最灵活）、简单（最简洁）、多线（多段落）。
- 管道表格用 `|` 与 `---`，对齐用 `:---`/`---:`/`:---:`。
- 表格标题用 `: 说明` 写在表格下方。
- 复杂表格（多段落、合并）优先用网格表格。
- 转 LaTeX 时，长表格需配合 `--variable=longtable` 等选项。

---

## 第 10 讲：代码块、语法高亮与行号

### 概念

代码块是技术文档的灵魂。Pandoc Markdown 支持围栏代码块（fenced code block）与缩进代码块两种写法，并能为代码块指定语言以启用语法高亮、添加行号、添加文件名标签、高亮指定行等。语法高亮由 Pandoc 内置的 highlighting-skills 引擎完成，无需外部工具。

### 原理

Pandoc 的代码块语法基于以下要素：

1. **围栏**：用三个或更多反引号 ```` ``` ```` 或波浪号 `~~~` 包围代码。
2. **语言属性**：在开头围栏后紧跟语言名，如 ```` ```python ````，触发语法高亮。
3. **属性块**：用 `{.python .numberLines startFrom="100"}` 形式指定多个属性，比单纯语言名更强大。
4. **高亮主题**：通过 `--highlight-style` 选项指定主题，如 `pygments`、`kate`、`monochrome`、`breezeDark` 等。

Pandoc 的高亮引擎会将代码解析为 token 流，再根据主题着色。对 HTML 输出，默认生成带 `<span class="kw">` 等类的 HTML，配合 CSS 渲染颜色；对 LaTeX 输出，生成 `\textcolor{...}` 命令。

### 例子

**普通围栏代码块**：

````markdown
```python
def greet(name):
    print(f"Hello, {name}!")
```
````

**带行号的代码块**：

````markdown
```{.python .numberLines}
def greet(name):
    print(f"Hello, {name}!")
```
````

**从第 100 行开始编号**：

````markdown
```{.python .numberLines startFrom="100"}
def greet(name):
    print(f"Hello, {name}!")
```
````

**高亮指定行**（部分版本支持）：

````markdown
```{.python .numberLines .highlight="2"}
def greet(name):
    print(f"Hello, {name}!")  # 这行会被高亮
```
````

**指定高亮主题**：

```bash
pandoc input.md --highlight-style=tango -o output.html
pandoc input.md --highlight-style=breezeDark -o output.html
```

**列出可用主题**：

```bash
pandoc --list-highlight-styles
```

**自定义高亮颜色**（生成自定义主题）：

```bash
pandoc --print-highlight-style=mytheme.json
# 编辑 mytheme.json 后使用：
pandoc --highlight-style=mytheme.json input.md -o output.html
```

### 总结

- 代码块用三反引号或三波浪号围栏；语言名写在开头围栏后。
- 属性块 `{.语言 .numberLines startFrom="N"}` 比单纯语言名更强大。
- `--highlight-style` 控制配色主题，可用 `--list-highlight-styles` 查阅。
- 高亮由 Pandoc 内置引擎完成，无需 Pygments 或 highlight.js。
- 对 LaTeX 输出，高亮会转为 `\textcolor` 命令；对 HTML 输出，转为带 class 的 span。

---

## 第 11 讲：链接、图片、脚注与行内引用

### 概念

超链接与图片是文档"网状结构"的载体；脚注与行内引用是学术写作的基础设施。Pandoc Markdown 全面支持这四类元素，并提供比标准 Markdown 更强的能力：图片可带标题（作为图题）、脚注支持行内与引用两种写法、可定义引用标识符用于交叉引用。

### 原理

- **链接**：`[文本](URL)` 为行内链接，`[文本][id]` 配 `[id]: URL` 为引用式链接，`<URL>` 为自动链接。
- **图片**：`![替代文本](图片路径)`，可选在路径后加 `"标题"` 作为图题。Pandoc 会将图题渲染为 `<figcaption>`（HTML）或 `\caption`（LaTeX）。
- **脚注**：行内脚注 `^[脚注内容]`，引用式脚注 `[^id]` 配 `[^id]: 脚注内容`。脚注内容可跨多行，缩进对齐。
- **行内引用**：通过 `{#label}` 为任意块或行内元素打标签，再用 `[见 @label]` 或 `[链接](#label)` 引用。

### 例子

**行内链接与引用式链接**：

```markdown
行内链接：[Pandoc 官网](https://pandoc.org)

引用式链接：[Pandoc 官网][pandoc]

[pandoc]: https://pandoc.org "Pandoc 官方网站"
```

**自动链接**：

```markdown
<https://pandoc.org>
<mailto:user@example.com>
```

**带图题的图片**：

```markdown
![Pandoc 工作流程图](workflow.png "图 1：Pandoc 三阶段流程")
```

**行内脚注**：

```markdown
Pandoc 由 John MacFarlane 开发^[哲学教授，加州大学伯克利分校。]。
```

**引用式脚注**：

```markdown
Pandoc 使用 Haskell 编写[^haskell]。

[^haskell]: Haskell 是一种纯函数式编程语言。
```

**为段落打标签并交叉引用**：

```markdown
请参考[安装步骤](#install)。

## 安装步骤 {#install}

第一步……
```

### 总结

- 链接三种写法：行内、引用式、自动；图片用 `![alt](path "title")`，title 成为图题。
- 脚注两种写法：行内 `^[...]`、引用式 `[^id]`，内容可跨行。
- 用 `{#label}` 为元素打标签，用 `[文本](#label)` 交叉引用。
- 图题在 HTML 中渲染为 `<figcaption>`，在 LaTeX 中渲染为 `\caption`，便于编号。
- 学术写作中，脚注与交叉引用是必备能力，Pandoc Markdown 完整支持。

---

# 第 3 章 格式转换实战

掌握了 Pandoc Markdown 语法后，本章进入"实战模式"。我们将逐一攻克最常见的六种目标格式：HTML、PDF、Word、LaTeX、EPUB、幻灯片。每种格式都有其特定的引擎、选项与坑点。学完本章后，你将能针对任意场景选择合适的输出格式并写出可用的转换命令。

---

## 第 12 讲：Markdown 转 HTML

### 概念

HTML 是 Pandoc 最基础的输出目标之一，也是"网页发布"场景的首选。Pandoc 能将 Markdown 转为完整的 HTML5 文档（含 `<!DOCTYPE html>`、`<head>`、`<body>`），或仅输出 HTML 片段（用于嵌入到其他页面）。通过 CSS、模板、数学渲染选项，可以定制 HTML 的外观与行为。

### 原理

Pandoc 的 HTML Writer 默认输出 HTML5。当使用 `-s`（standalone）时，会套用内置模板生成完整文档；不带 `-s` 时，仅输出正文片段。HTML Writer 会自动处理：

- 数学公式：默认用 MathJax 渲染（`--mathjax`），也可选 KaTeX（`--katex`）、`webtex`（在线渲染）、`mathml`（浏览器原生 MathML）。
- 语法高亮：生成带 class 的 `<span>`，配合 `--highlight-style` 控制配色，CSS 由 Pandoc 内联或外链。
- 元数据：`title`/`author`/`date` 等会写入 `<head>` 的 `<title>`、`<meta>` 等。
- 目录：`--toc` 会在文档开头生成 `<nav id="TOC">` 块。

### 例子

**生成完整 HTML 文档**：

```bash
pandoc report.md -s -o report.html
```

**带目录、编号、CSS**：

```bash
pandoc report.md \
  -s \
  --toc \
  --toc-depth=3 \
  --number-sections \
  --css=style.css \
  -M title="我的报告" \
  -o report.html
```

**使用 KaTeX 渲染数学公式**：

```bash
pandoc report.md -s --katex -o report.html
```

**生成自包含 HTML（CSS/JS 内联，便于离线分享）**：

```bash
pandoc report.md -s --self-contained -o report.html
# 新版本中改为 --embed-resources --standalone
pandoc report.md -s --embed-resources --standalone -o report.html
```

**生成 HTML 片段（用于嵌入到现有页面）**：

```bash
pandoc report.md -o fragment.html
# 不带 -s，输出仅含正文 HTML
```

**指定 HTML 模板**：

```bash
pandoc report.md -s --template=my-template.html -o report.html
```

### 总结

- HTML 是最基础的输出目标，`-s` 决定完整文档还是片段。
- 数学渲染默认 MathJax，可切换 KaTeX（更快）或 MathML（原生）。
- `--css` 引入样式表；`--self-contained`/`--embed-resources` 内联资源便于分享。
- `--toc` 生成导航目录，`--number-sections` 自动编号。
- HTML 模板可完全自定义，详见第 19 讲。

---

## 第 13 讲：Markdown 转 PDF（LaTeX 引擎）

### 概念

PDF 是文档分发的"终态格式"，跨平台、版式固定。Pandoc 本身不直接生成 PDF，而是先生成 LaTeX，再调用系统中的 LaTeX 引擎（pdflatex、xelatex、lualatex）编译为 PDF。这意味着转 PDF 的前提是安装 TeX 发行版。对于中文文档，必须使用 xelatex 或 lualatex（支持 Unicode 与系统字体）。

### 原理

Pandoc 转 PDF 的内部流程为：

1. 将 Markdown 解析为 AST。
2. 调用 LaTeX Writer，将 AST 序列化为 `.tex` 文件。
3. 调用指定的 LaTeX 引擎（默认 pdflatex）编译 `.tex` 为 PDF。
4. 若编译失败，Pandoc 会保留 `.tex` 文件以便调试。

关键选项：

- `--pdf-engine=xelatex`：指定引擎，中文必选 xelatex 或 lualatex。
- `-V CJKmainfont="Noto Serif CJK SC"`：指定中文字体（xelatex/lualatex 专用）。
- `-V mainfont="..."`、`-V sansfont="..."`：指定西文字体。
- `-V geometry:margin=2.5cm`：页面边距。
- `-V documentclass=ctexart`：使用 ctex 宏包简化中文排版。

### 例子

**最简单的 PDF 转换（英文文档）**：

```bash
pandoc report.md -o report.pdf
```

**中文 PDF（关键命令）**：

```bash
pandoc report.md \
  --pdf-engine=xelatex \
  -V CJKmainfont="Noto Serif CJK SC" \
  -V mainfont="Times New Roman" \
  -V geometry:margin=2.5cm \
  -V documentclass=ctexart \
  -o report.pdf
```

**使用 ctex 宏包简化中文**：

```bash
pandoc report.md \
  --pdf-engine=xelatex \
  -V documentclass=ctexart \
  -o report.pdf
```

**带目录与编号**：

```bash
pandoc report.md \
  --pdf-engine=xelatex \
  -V documentclass=ctexart \
  --toc \
  --number-sections \
  -o report.pdf
```

**保留中间 .tex 文件以便调试**：

```bash
pandoc report.md --pdf-engine=xelatex -o report.tex
# 然后手动 xelatex report.tex 查看详细编译日志
```

**安装 TeX 发行版**：

```bash
# Ubuntu/Debian
sudo apt install texlive-xetex texlive-lang-chinese texlive-fonts-recommended

# macOS（MacTeX）
brew install --cask mactex

# Windows：下载 MiKTeX 或 TeX Live
```

### 总结

- 转 PDF 的本质：Markdown → LaTeX → PDF，依赖 TeX 发行版。
- 中文必须用 `--pdf-engine=xelatex` 或 `lualatex`，并指定中文字体。
- `-V documentclass=ctexart` 是中文排版的捷径，自动处理字体与断行。
- 编译失败时保留 `.tex` 文件手动编译，查看日志定位问题。
- 也可用 `--pdf-engine=weasyprint`、`wkhtmltoprint`、`typst` 等替代引擎，详见第 31 讲。

---

## 第 14 讲：Markdown 转 Word（docx）

### 概念

Word（.docx）是办公协作的事实标准。Pandoc 能将 Markdown 转为符合 OOXML 标准的 docx 文件，并通过"参考文档（reference doc）"机制映射样式，使输出文档符合企业或期刊的样式规范。这是 Pandoc 在"办公场景"中最受欢迎的能力。

### 原理

docx 是一个 ZIP 容器，内部是若干 XML 文件（`document.xml`、`styles.xml` 等）。Pandoc 的 docx Writer 会：

1. 将 AST 中的每种元素映射到 docx 的"样式名"：标题 → `Heading 1/2/3`，正文 → `First Paragraph`/`Body Text`，引用 → `Block Quote` 等。
2. 默认使用内置样式定义生成 docx。
3. 若提供 `--reference-doc=ref.docx`，则从该文档中提取样式定义，替换默认样式。

这意味着：你可以在 Word 中调整一份"参考文档"的字体、字号、颜色、间距，然后用它作为 Pandoc 的样式来源，所有输出文档都会自动套用这套样式。

### 例子

**最简单的 docx 转换**：

```bash
pandoc report.md -o report.docx
```

**带元数据与目录**：

```bash
pandoc report.md \
  -M title="季度报告" \
  -M author="张三" \
  --toc \
  --number-sections \
  -o report.docx
```

**生成参考文档以便自定义样式**：

```bash
pandoc -o ref.docx --print-default-data-file=reference.docx
# 或在新版本中：
pandoc --print-default-data-file=reference.docx > ref.docx
```

**在 Word 中编辑 ref.docx**：修改"标题 1""正文"等样式的字体、字号、颜色、段间距，保存。

**使用自定义参考文档**：

```bash
pandoc report.md --reference-doc=ref.docx -o report.docx
```

**高亮代码块**（docx 不支持原生语法高亮，但可用以下方式）：

```bash
pandoc report.md --reference-doc=ref.docx --highlight-style=tango -o report.docx
```

### 总结

- docx 是 OOXML 容器，Pandoc 通过"样式名映射"生成内容。
- `--reference-doc` 是定制 docx 样式的核心机制：编辑参考文档即可改变所有输出。
- 标题映射到 `Heading 1/2/3`，正文映射到 `Body Text`，引用映射到 `Block Quote`。
- 元数据 `title`/`author` 会写入 docx 文档属性，可在 Word 中查看。
- docx 不支持原生语法高亮，但可通过参考文档中的"源代码"样式调整外观。

---

## 第 15 讲：Markdown 转 LaTeX

### 概念

LaTeX 是学术出版的"金标准"。Pandoc 能将 Markdown 转为高质量的 LaTeX 源码，再由用户进一步定制或编译为 PDF。相比直接转 PDF，转 LaTeX 的好处是：可以手动微调生成的 `.tex` 文件，使用任何 LaTeX 宏包，实现极致排版控制。

### 原理

Pandoc 的 LaTeX Writer 会将 AST 转换为 LaTeX 命令：

- 标题 → `\section{}`、`\subsection{}` 等
- 强调 → `\emph{}`、`\textbf{}`
- 列表 → `itemize`、`enumerate`、`description` 环境
- 表格 → `tabular` 或 `longtable` 环境
- 代码块 → `Verbatim` 或 `Highlighting` 环境
- 数学公式 → `$...$`（行内）或 `\[...\]`（行间）

通过 `-V` 选项可控制文档类、宏包、字体、页面尺寸等。Pandoc 默认加载 `amsmath`、`graphicx`、`hyperref` 等常用宏包。

### 例子

**生成 LaTeX 源码**：

```bash
pandoc report.md -s -o report.tex
```

**指定文档类与选项**：

```bash
pandoc report.md -s \
  -V documentclass=report \
  -V geometry:margin=2.5cm \
  -V fontsize=11pt \
  -o report.tex
```

**中文 LaTeX**：

```bash
pandoc report.md -s \
  -V documentclass=ctexart \
  -V CJKmainfont="Noto Serif CJK SC" \
  -o report.tex
# 编译：xelatex report.tex
```

**启用长表格支持**：

```bash
pandoc report.md -s -V longtable -o report.tex
```

**启用章节编号深度**：

```bash
pandoc report.md -s -V secnumdepth=3 -V toc-depth=3 --toc -o report.tex
```

**注入自定义 LaTeX 宏包**：

```bash
pandoc report.md -s -H preamble.tex -o report.tex
# preamble.tex 内容示例：
# \usepackage{booktabs}
# \usepackage{microtype}
```

### 总结

- 转 LaTeX 是"半成品"输出，便于进一步手动定制。
- 标题、列表、表格、代码、数学都有对应的 LaTeX 环境映射。
- `-V documentclass`、`-V geometry`、`-V fontsize` 控制文档类与页面。
- 中文用 `ctexart`/`ctexrep`/`ctexbook` 文档类，配合 xelatex 编译。
- `-H preamble.tex` 可注入自定义宏包前言，扩展 LaTeX 能力。

---

## 第 16 讲：Markdown 转 EPUB 电子书

### 概念

EPUB 是电子书的事实标准，被 Apple Books、Kobo、Calibre 等广泛支持。Pandoc 能将 Markdown 转为符合 EPUB 2/3 标准的电子书，包含封面、目录、元数据、章节分页、CSS 样式等。这是"自出版"场景的核心能力。

### 原理

EPUB 本质是一个 ZIP 包，内含若干 XHTML 文件、CSS、图片、`META-INF/container.xml`、`content.opf`（包描述）、`toc.ncx`（目录）。Pandoc 的 EPUB Writer 会：

1. 按"一级标题"或 `--split-level` 自动分章，每章生成一个 XHTML 文件。
2. 从元数据生成 `content.opf`，包含标题、作者、日期、语言、版权等。
3. 生成 `toc.ncx`（EPUB 2）与 `nav.xhtml`（EPUB 3）目录。
4. 嵌入指定的 CSS、字体、图片资源。

关键选项：`--epub-cover-image`（封面图）、`--epub-css`（样式表）、`--epub-metadata`（元数据 XML）、`--epub-chapter-level`（分章级别）。

### 例子

**最简单的 EPUB 转换**：

```bash
pandoc book.md -o book.epub
```

**带封面、CSS、元数据**：

```bash
pandoc book.md \
  --epub-cover-image=cover.jpg \
  --css=epub.css \
  -M title="我的电子书" \
  -M author="张三" \
  -M lang=zh-CN \
  -o book.epub
```

**按一级标题分章**：

```bash
pandoc book.md --epub-chapter-level=1 -o book.epub
```

**从多个 Markdown 文件合成一本电子书**：

```bash
pandoc chapter1.md chapter2.md chapter3.md \
  --epub-cover-image=cover.jpg \
  --css=epub.css \
  -M title="合集" \
  -M author="张三" \
  -o collection.epub
```

**使用外部元数据文件**：

```bash
pandoc book.md --epub-metadata=meta.xml -o book.epub
```

`meta.xml` 示例：

```xml
<dc:title>我的电子书</dc:title>
<dc:creator>张三</dc:creator>
<dc:language>zh-CN</dc:language>
<dc:date>2026-08-04</dc:date>
<dc:rights>版权所有</dc:rights>
```

### 总结

- EPUB 是 ZIP 容器，内含 XHTML/CSS/图片/描述文件。
- Pandoc 按一级标题自动分章，每章一个 XHTML 文件。
- `--epub-cover-image` 指定封面，`--css` 指定样式，`--epub-metadata` 指定元数据。
- 多个 Markdown 文件可合并为一本电子书，按顺序排列。
- 用 Calibre 或 Apple Books 预览生成的 EPUB，检查排版效果。

---

## 第 17 讲：Markdown 转幻灯片（Beamer / reveal.js）

### 概念

Pandoc 能将 Markdown 转为两种主流幻灯片格式：Beamer（LaTeX 幻灯片，输出 PDF）与 reveal.js（HTML 幻灯片，浏览器演示）。这是"用 Markdown 写演讲稿"的核心场景，让你专注于内容，由模板负责视觉。

### 原理

Pandoc 用"幻灯片分隔符"将 Markdown 切分为一张张幻灯片。三种分隔方式：

1. **水平线**：`---`（三个连字符）分隔幻灯片。
2. **标题级别**：由 `--slide-level=N` 指定，默认为 2，即每个二级标题开始一张新幻灯片。
3. **连续标题**：连续的同级标题会被合并到一张幻灯片。

对 Beamer，Pandoc 生成 `\begin{frame}...\end{frame}` 环境；对 reveal.js，生成 `<section>` 标签。两种格式都支持渐进显示（`<div class="fragment">` 或 `\pause`）、演讲者备注（`:::` 块）。

### 例子

**Markdown 幻灯片源文件**：

```markdown
# 标题页

## 第一张幻灯片

- 要点一
- 要点二

> 这是引用。

## 第二张幻灯片

正文内容……

---

## 第三张幻灯片（用 --- 强制分页）

更多内容……
```

**生成 Beamer PDF（中文）**：

```bash
pandoc slides.md \
  -t beamer \
  --pdf-engine=xelatex \
  -V documentclass=ctexbeamer \
  -V theme=Madrid \
  -o slides.pdf
```

**生成 reveal.js HTML 幻灯片**：

```bash
pandoc slides.md \
  -t revealjs \
  -s \
  -V theme=black \
  -V transition=slide \
  -o slides.html
```

**渐进显示**：

```markdown
## 渐进显示

- 第一项 {.fragment}
- 第二项 {.fragment}
- 第三项 {.fragment}
```

**演讲者备注**：

```markdown
::: notes
这是只有演讲者能看到的备注。
:::
```

**指定幻灯片级别**：

```bash
pandoc slides.md -t beamer --slide-level=2 -o slides.pdf
```

### 总结

- Pandoc 用 `---` 或标题级别切分幻灯片，`--slide-level` 控制切分粒度。
- Beamer 输出 PDF，适合学术演讲；reveal.js 输出 HTML，适合浏览器演示。
- 中文 Beamer 用 `ctexbeamer` 文档类 + xelatex 引擎。
- 渐进显示用 `{.fragment}`（reveal.js）或 `\pause`（Beamer）。
- 演讲者备注用 `::: notes` 块，在演示模式中按 `S` 键查看。

---

# 第 4 章 模板与样式定制

Pandoc 默认输出已经"够用"，但要让它"出彩"，必须掌握模板与样式定制。本章共 5 讲，覆盖模板系统原理、HTML 模板、LaTeX 模板与中文支持、docx 参考文档、CSS 样式定制。学完后你将能让 Pandoc 输出的文档完全符合个人或机构的视觉规范。

---

## 第 18 讲：模板系统与变量机制

### 概念

模板（template）是 Pandoc 控制输出文档"骨架"的机制。每种输出格式都有一份默认模板，定义了文档的整体结构（如 HTML 的 `<html><head>...</head><body>...</body></html>`）。用户可以通过 `--template` 选项指定自定义模板，并通过 `-V`/`--variable` 注入变量，实现完全的版式控制。

### 原理

Pandoc 模板使用一种简化的模板语言，支持以下语法：

- `$变量名$`：插入变量值。
- `$if(变量)$...$endif$`：条件判断，变量为真时输出内容。
- `$for(变量)$...$endfor$`：循环遍历列表变量。
- `$变量名$` 默认转义 HTML/LaTeX 特殊字符；`$变量名.raw$` 不转义。
- `${变量名}$`：等价于 `$变量名$`，用于避免与 LaTeX 数学 `$` 冲突。

变量来源有三层，优先级从高到低：

1. 命令行 `-V key=value` 或 `--variable key=value`。
2. 元数据块（YAML）中的字段。
3. 模板内置默认值（在模板中通过 `$key$` 引用未定义变量时为空）。

Pandoc 还提供一组"内置变量"，如 `$body$`（正文内容）、`$toc$`（目录）、`$title$`、`$author$`、`$date$`、`$lang$` 等，可在模板中直接引用。

### 例子

**导出默认模板以便修改**：

```bash
pandoc -o my-template.html --print-default-data-file=templates/default.html
# 或：
pandoc --print-default-data-file=templates/default.html > my-template.html
```

**最简自定义 HTML 模板** `my-template.html`：

```html
<!DOCTYPE html>
<html lang="$lang$">
<head>
  <meta charset="utf-8">
  <title>$title$</title>
  $if(highlight-css)$<style>$highlight-css$</style>$endif$
</head>
<body>
  $if(title)$<h1>$title$</h1>$endif$
  $for(author)$<p class="author">$author$</p>$endfor$
  $if(date)$<p class="date">$date$</p>$endif$
  $if(toc)$<nav id="TOC">$table-of-contents$</nav>$endif$
  $body$
</body>
</html>
```

**使用自定义模板**：

```bash
pandoc report.md -s --template=my-template.html -o report.html
```

**注入自定义变量**：

```bash
pandoc report.md -s --template=my-template.html \
  -V lang=zh-CN \
  -V title="我的报告" \
  -V author="张三" \
  -o report.html
```

**循环遍历作者列表**（在模板中）：

```html
$for(author)$
<p>$author$</p>
$endfor$
```

### 总结

- 模板用 `$变量$`、`$if$`、`$for$` 语法控制文档骨架。
- 变量来源：命令行 `-V` > YAML 元数据 > 模板默认。
- 内置变量：`$body$`、`$toc$`、`$title$`、`$author$`、`$date$` 等。
- 用 `--print-default-data-file=templates/default.格式` 导出默认模板作为修改起点。
- 模板是"完全控制输出"的最后一步，掌握后即可摆脱默认样式的束缚。

---

## 第 19 讲：自定义 HTML 模板

### 概念

HTML 模板是最常被定制的模板类型。通过自定义 HTML 模板，你可以控制页面布局、引入 CSS 框架（如 Bootstrap、Tailwind）、添加导航栏、页脚、Google Analytics、评论系统等。本讲通过一个完整实例展示如何打造"品牌化"的 HTML 输出。

### 原理

自定义 HTML 模板的关键在于理解 Pandoc 注入的变量与块：

- `$body$`：正文 HTML（已渲染）。
- `$table-of-contents$` 或 `$toc$`：目录 HTML（仅当 `--toc` 启用）。
- `$title$`、`$author$`、`$date$`、`$subtitle$`、`$abstract$`：元数据字段。
- `$lang$`：文档语言。
- `$highlight-css$`：语法高亮 CSS（仅当启用高亮时）。
- `$math$`：数学渲染配置（MathJax/KaTeX 脚本）。
- `$for(css)$...$endfor$`：遍历 `--css` 指定的样式表。
- `$for(header-includes)$...$endfor$`：遍历 `--include-in-header` 注入的内容。

理解这些变量后，你可以像写普通 HTML 一样设计模板，只在需要动态内容的位置插入变量占位符。

### 例子

**完整的品牌化 HTML 模板** `brand.html`：

```html
<!DOCTYPE html>
<html lang="$lang$">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>$if(title)$$title$$endif$</title>
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5/dist/css/bootstrap.min.css">
  $for(css)$<link rel="stylesheet" href="$css$">$endfor$
  $if(highlight-css)$<style>$highlight-css$</style>$endif$
  $for(header-includes)$$header-includes$$endfor$
</head>
<body>
  <nav class="navbar navbar-dark bg-dark">
    <div class="container">
      <a class="navbar-brand" href="/">我的品牌</a>
    </div>
  </nav>
  <main class="container my-5">
    $if(title)$<h1 class="mb-3">$title$</h1>$endif$
    $if(subtitle)$<p class="lead">$subtitle$</p>$endif$
    $for(author)$<p class="text-muted">$author$</p>$endfor$
    $if(date)$<p class="text-muted"><small>$date$</small></p>$endif$
    $if(toc)$
    <details class="my-4">
      <summary>目录</summary>
      $table-of-contents$
    </details>
    $endif$
    $body$
  </main>
  <footer class="text-center py-4 text-muted">
    <small>© 2026 我的公司</small>
  </footer>
  $if(math)$<script src="$math$"></script>$endif$
</body>
</html>
```

**使用该模板**：

```bash
pandoc report.md \
  -s \
  --template=brand.html \
  --toc \
  --css=custom.css \
  --katex \
  -M title="季度报告" \
  -M author="张三" \
  -M date="2026-08-04" \
  -M lang=zh-CN \
  -o report.html
```

**注入额外头部内容**（如 Google Analytics）：

```bash
pandoc report.md -s --template=brand.html \
  -H analytics.html \
  -o report.html
```

`analytics.html` 内容：

```html
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXX');
</script>
```

### 总结

- HTML 模板的核心变量：`$body$`、`$title$`、`$author$`、`$toc$`、`$lang$`、`$math$`。
- `$for(css)$`、`$for(header-includes)$` 用于遍历注入的样式与头部内容。
- 模板可引入任何前端框架（Bootstrap、Tailwind、Vue 等）。
- `-H file.html` 可注入额外头部内容（如统计脚本、自定义 CSS）。
- 自定义模板是"品牌化输出"的关键，让 Pandoc 输出符合网站视觉规范。

---

## 第 20 讲：LaTeX 模板与中文支持

### 概念

LaTeX 模板决定了 PDF 输出的整体外观：文档类、宏包、字体、页面布局、标题样式、页眉页脚等。Pandoc 默认的 LaTeX 模板已经相当完善，但中文用户常需要定制以支持中文字体、中文断行、中文标点等。本讲聚焦中文 LaTeX 模板的核心要素。

### 原理

Pandoc 默认 LaTeX 模板的关键部分：

- `\documentclass[...]{...}`：文档类，由 `-V documentclass` 控制。
- `\usepackage{...}`：加载宏包，由模板内置 + `-H preamble.tex` 注入。
- `\title{...}`、`\author{...}`、`\date{...}`：标题信息。
- `\begin{document}` ... `\end{document}`：文档体。
- `$body$`：正文 LaTeX。
- `$if(toc)$\tableofcontents$endif$`：目录。

中文支持的关键：

- 使用 `ctexart`/`ctexrep`/`ctexbook` 文档类（CTeX 宏包），自动处理中文字体、断行、标点。
- 或使用 `xeCJK` 宏包手动配置中文字体。
- 必须用 `xelatex` 或 `lualatex` 编译（pdflatex 不支持 Unicode 中文）。

### 例子

**最简中文 LaTeX 模板** `cn.tex`：

```latex
\documentclass[11pt,a4paper]{ctexart}
\usepackage{geometry}
\geometry{margin=2.5cm}
\usepackage{microtype}
\usepackage{booktabs}
\usepackage{longtable}
\usepackage{hyperref}
\hypersetup{colorlinks=true, linkcolor=blue, urlcolor=blue}

$if(highlighting-macros)$
$highlighting-macros$
$endif$

\title{$title$}
\author{$for(author)$$author$$sep$ \\ $endfor$}
\date{$date$}

\begin{document}
$if(title)$\maketitle$endif$
$if(toc)$\tableofcontents$endif$
$body$
\end{document}
```

**使用该模板**：

```bash
pandoc report.md \
  --pdf-engine=xelatex \
  --template=cn.tex \
  --toc \
  --number-sections \
  -M title="中文报告" \
  -M author="张三" \
  -M date="2026-08-04" \
  -o report.pdf
```

**指定中文字体**：

```bash
pandoc report.md \
  --pdf-engine=xelatex \
  --template=cn.tex \
  -V CJKmainfont="Noto Serif CJK SC" \
  -V CJKsansfont="Noto Sans CJK SC" \
  -V CJKmonofont="Sarasa Mono SC" \
  -o report.pdf
```

**使用 xeCJK 手动配置（不依赖 ctex）**：

```latex
\documentclass[11pt]{article}
\usepackage{xeCJK}
\setCJKmainfont{Noto Serif CJK SC}
\setCJKsansfont{Noto Sans CJK SC}
\setCJKmonofont{Sarasa Mono SC}
% ... 其余同上
```

**生成 LaTeX 后手动微调**：

```bash
pandoc report.md --template=cn.tex -o report.tex
# 编辑 report.tex，添加自定义宏包或调整布局
xelatex report.tex
```

### 总结

- 中文 LaTeX 模板的核心：`ctexart`/`ctexrep`/`ctexbook` 文档类 + xelatex 编译。
- `-V CJKmainfont`/`CJKsansfont`/`CJKmonofont` 指定中文字族。
- 也可用 `xeCJK` 宏包手动配置，灵活性更高。
- 生成 `.tex` 后可手动微调，再编译为 PDF。
- 中文标点、断行由 ctex/xeCJK 自动处理，无需手动干预。

---

## 第 21 讲：docx 参考文档与样式映射

### 概念

docx 不像 HTML/LaTeX 那样有"模板文件"，而是通过"参考文档（reference doc）"机制定制样式。参考文档是一份普通的 .docx 文件，其中定义了所有命名样式的字体、字号、颜色、间距等属性。Pandoc 在生成 docx 时，会从参考文档中提取样式定义，套用到输出内容上。

### 原理

Pandoc 的 docx Writer 会将 AST 元素映射到 docx 命名样式：

| AST 元素 | docx 样式名 |
|---------|------------|
| 一级标题 | `Heading 1` |
| 二级标题 | `Heading 2` |
| ... | ... |
| 正文段落 | `First Paragraph`（首段）/ `Body Text`（后续） |
| 引用块 | `Block Quote` |
| 代码块 | `Source Code` |
| 表格 | `Table` |
| 列表 | `List Paragraph` |
| 题注 | `Image Caption` / `Table Caption` |
| 脚注 | `Footnote Text` |

工作流程：

1. 用 `pandoc --print-default-data-file=reference.docx > ref.docx` 生成默认参考文档。
2. 在 Word 中打开 `ref.docx`，修改各命名样式的属性（字体、字号、颜色、间距）。
3. 用 `--reference-doc=ref.docx` 让 Pandoc 套用这些样式。

### 例子

**生成默认参考文档**：

```bash
pandoc --print-default-data-file=reference.docx > ref.docx
```

**在 Word 中编辑样式**：

1. 打开 `ref.docx`。
2. 在"开始"选项卡的"样式"窗格中，右键"标题 1" → "修改"。
3. 调整字体（如改为"思源宋体"）、字号（如三号）、颜色、段前段后间距。
4. 对"正文""引用""源代码"等样式重复上述操作。
5. 保存 `ref.docx`。

**使用自定义参考文档**：

```bash
pandoc report.md --reference-doc=ref.docx -o report.docx
```

**为不同场景准备多份参考文档**：

```bash
# 学术论文样式
pandoc paper.md --reference-doc=academic-ref.docx -o paper.docx

# 公司报告样式
pandoc report.md --reference-doc=corp-ref.docx -o report.docx

# 图书样式
pandoc book.md --reference-doc=book-ref.docx -o book.docx
```

**在 YAML 中指定参考文档**（配合 defaults 文件，见第 28 讲）：

```yaml
# defaults/docx.yaml
to: docx
reference-doc: ref.docx
```

### 总结

- docx 通过"参考文档"机制定制样式，而非模板文件。
- AST 元素映射到命名样式：`Heading 1/2/3`、`Body Text`、`Block Quote`、`Source Code` 等。
- 用 `--print-default-data-file=reference.docx` 生成默认参考文档，在 Word 中修改样式。
- `--reference-doc=ref.docx` 让 Pandoc 套用自定义样式。
- 为不同场景准备多份参考文档，实现"一份源文件、多种风格"输出。

---

## 第 22 讲：CSS 样式与网页美化

### 概念

Pandoc 生成的 HTML 默认是"无样式"的，需要 CSS 来美化。本讲介绍如何为 Pandoc HTML 输出编写与组织 CSS，覆盖排版、字体、代码高亮、表格、引用、目录等元素的样式，并介绍几种流行的 CSS 框架与 Pandoc 的配合方式。

### 原理

Pandoc 生成的 HTML 有清晰的语义结构，便于用 CSS 选择器精确控制：

- 标题：`<h1>`～`<h6>`，带 `id` 属性。
- 段落：`<p>`。
- 强调：`<strong>`（加粗）、`<em>`（斜体）。
- 引用：`<blockquote>`。
- 代码块：`<pre class="sourceCode"><code class="sourceCode python">`。
- 行内代码：`<code>`。
- 表格：`<table>`，表头 `<thead>`，表体 `<tbody>`。
- 列表：`<ul>`/`<ol>`/`<dl>`。
- 图片：`<figure><img><figcaption></figure>`。
- 脚注：`<section class="footnotes">`。
- 目录：`<nav id="TOC">`。

通过这些选择器，可以精确控制每个元素的样式。

### 例子

**基础排版 CSS** `style.css`：

```css
:root {
  --max-width: 800px;
  --text-color: #333;
  --bg-color: #fff;
  --accent: #0066cc;
  --code-bg: #f5f5f5;
}

body {
  max-width: var(--max-width);
  margin: 2rem auto;
  padding: 0 1rem;
  font-family: "Noto Serif SC", "Noto Serif", Georgia, serif;
  font-size: 16px;
  line-height: 1.8;
  color: var(--text-color);
  background: var(--bg-color);
}

h1, h2, h3, h4, h5, h6 {
  font-family: "Noto Sans SC", "Noto Sans", sans-serif;
  font-weight: 600;
  line-height: 1.3;
  margin-top: 2em;
  margin-bottom: 0.5em;
}

h1 { font-size: 2em; border-bottom: 2px solid var(--accent); padding-bottom: 0.3em; }
h2 { font-size: 1.5em; border-bottom: 1px solid #ddd; padding-bottom: 0.2em; }

a { color: var(--accent); text-decoration: none; }
a:hover { text-decoration: underline; }

blockquote {
  margin: 1em 0;
  padding: 0.5em 1em;
  border-left: 4px solid var(--accent);
  background: #f9f9f9;
  color: #555;
}

pre {
  background: var(--code-bg);
  padding: 1em;
  border-radius: 4px;
  overflow-x: auto;
}

code { font-family: "Sarasa Mono SC", "JetBrains Mono", monospace; }
pre code { background: none; padding: 0; }

table {
  border-collapse: collapse;
  margin: 1em 0;
  width: 100%;
}

th, td {
  border: 1px solid #ddd;
  padding: 0.5em 1em;
  text-align: left;
}

th { background: #f0f0f0; font-weight: 600; }

figure { margin: 1em 0; text-align: center; }
figcaption { font-size: 0.9em; color: #666; margin-top: 0.5em; }

nav#TOC { background: #f9f9f9; padding: 1em 1.5em; border-radius: 4px; margin-bottom: 2em; }
nav#TOC ul { list-style: none; padding-left: 1em; }
nav#TOC a { color: var(--text-color); }
nav#TOC a:hover { color: var(--accent); }
```

**使用该 CSS**：

```bash
pandoc report.md -s --css=style.css --toc -o report.html
```

**引入流行 CSS 框架**（在模板中）：

```html
<!-- 在自定义模板的 <head> 中 -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/water.css@2/out/water.css">
<!-- 或 Sakura、MVP.css 等"无类名"框架，与 Pandoc 输出天然契合 -->
```

**生成自包含 HTML（CSS 内联）**：

```bash
pandoc report.md -s --css=style.css --embed-resources --standalone -o report.html
```

### 总结

- Pandoc HTML 输出有清晰语义结构，便于 CSS 精确控制。
- 关键选择器：`h1`～`h6`、`blockquote`、`pre`、`code`、`table`、`figure`、`nav#TOC`。
- 用 `--css` 引入样式表，`--embed-resources --standalone` 内联资源。
- "无类名"CSS 框架（water.css、sakura、MVP.css）与 Pandoc 输出天然契合。
- 自定义 CSS 是"让 Pandoc 输出好看"的最直接手段。

---

# 第 5 章 过滤器与引文

过滤器是 Pandoc 最强大的扩展机制，能在 AST 层面对文档进行任意变换；引文处理是学术写作的核心需求。本章共 5 讲，覆盖过滤器原理、Lua 过滤器语法、常用过滤器实战、引文与参考文献、JSON 过滤器与 Pandoc Python。学完后你将能编写自定义过滤器，实现任何 Pandoc 内置功能无法完成的文档变换。

---

## 第 23 讲：过滤器原理与生态

### 概念

过滤器（filter）是一种在 Pandoc 解析输入之后、生成输出之前，对中间 AST 进行变换的程序。通过过滤器，你可以修改、添加、删除文档中的任何元素，实现 Pandoc 内置功能无法完成的定制。Pandoc 的过滤器生态是其成为"文档处理平台"而非"单纯转换器"的关键。

### 原理

Pandoc 的处理流程为：

```
输入文件 → Reader → AST → [过滤器1] → [过滤器2] → ... → Writer → 输出文件
```

过滤器接收 JSON 格式的 AST，处理后输出新的 JSON AST。Pandoc 通过 `--filter` 选项调用外部过滤器程序，通过 `--lua-filter` 调用内置 Lua 过滤器。

过滤器分两类：

1. **Lua 过滤器**：用 Lua 语言编写，由 Pandoc 内置 Lua 解释器执行，无需外部依赖，启动快，是 Pandoc 官方推荐的方式。
2. **JSON 过滤器**：用任何语言（Python、Haskell、JavaScript 等）编写，通过 stdin/stdout 与 Pandoc 交换 JSON AST。灵活但启动慢。

过滤器函数按"AST 节点类型"匹配：`Str`（字符串）、`Para`（段落）、`Header`（标题）、`CodeBlock`（代码块）、`Image`（图片）等。每个过滤器函数接收一个节点，返回修改后的节点（或新节点列表，或 `nil` 表示不修改）。

### 例子

**调用 Lua 过滤器**：

```bash
pandoc input.md --lua-filter=myfilter.lua -o output.html
```

**调用 JSON 过滤器**（如 pandoc-citeproc）：

```bash
pandoc input.md --filter=pandoc-citeproc -o output.html
```

**查看 AST 结构**（调试过滤器必备）：

```bash
pandoc input.md -t json | python -m json.tool | head -50
```

**最简 Lua 过滤器** `uppercase.lua`（将所有行内代码转为大写）：

```lua
function Code(el)
  el.text = el.text:upper()
  return el
end
```

**使用该过滤器**：

```bash
pandoc input.md --lua-filter=uppercase.lua -o output.html
```

**多个过滤器串联**（按顺序执行）：

```bash
pandoc input.md \
  --lua-filter=filter1.lua \
  --lua-filter=filter2.lua \
  --filter=pandoc-citeproc \
  -o output.html
```

### 总结

- 过滤器在 Reader 与 Writer 之间对 AST 进行变换，是 Pandoc 最强大的扩展机制。
- 两种类型：Lua 过滤器（内置、快、推荐）与 JSON 过滤器（外部、灵活、慢）。
- 过滤器函数按 AST 节点类型匹配：`Str`、`Para`、`Header`、`CodeBlock` 等。
- 用 `--lua-filter` 调用 Lua 过滤器，`--filter` 调用 JSON 过滤器。
- 调试时用 `pandoc -t json` 查看 AST 结构。

---

## 第 24 讲：Lua 过滤器基础语法

### 概念

Lua 过滤器是 Pandoc 官方推荐的扩展方式。Lua 是一种轻量、快速、易学的脚本语言，Pandoc 内置了 Lua 5.3+ 解释器，无需安装任何外部依赖即可运行 Lua 过滤器。本讲介绍 Lua 过滤器的基本语法与常用模式。

### 原理

Lua 过滤器是一个 `.lua` 文件，其中定义若干函数，每个函数对应一种 AST 节点类型。Pandoc 在遍历 AST 时，遇到对应类型的节点就会调用相应函数。函数签名：

```lua
function NodeType(el)
  -- el 是 AST 节点（table）
  -- 返回值：
  --   nil 或 el：保持原节点（或修改后的 el）
  --   新节点：替换为新节点
  --   {}（空表）：删除该节点
  --   {node1, node2}：替换为多个节点
  return el
end
```

常用节点类型：

- **行内**：`Str`（字符串）、`Emph`（强调）、`Strong`（加粗）、`Code`（行内代码）、`Link`（链接）、`Image`（图片）、`Math`（数学）、`Cite`（引用）、`Note`（脚注）、`Span`（带属性的 span）。
- **块级**：`Para`（段落）、`Plain`（无段落包裹的文本）、`Header`（标题）、`CodeBlock`（代码块）、`BlockQuote`（引用块）、`BulletList`（无序列表）、`OrderedList`（有序列表）、`DefinitionList`（定义列表）、`Table`（表格）、`Div`（带属性的 div）、`RawBlock`（原始块）。
- **元数据**：`Meta`（文档元数据）。

每个节点是一个 Lua table，字段对应其属性。例如 `Header` 节点有 `level`（级别）、`identifier`（ID）、`classes`（类列表）、`attributes`（属性表）、`content`（行内内容列表）。

### 例子

**修改所有标题级别**（将所有标题降一级）：

```lua
function Header(el)
  el.level = el.level + 1
  if el.level > 6 then el.level = 6 end
  return el
end
```

**为所有图片添加 caption**：

```lua
function Image(el)
  if el.caption and #el.caption > 0 then
    -- 已有 caption，跳过
    return el
  end
  el.caption = {pandoc.Str("图片：" .. el.src)}
  return el
end
```

**删除所有链接，只保留文本**：

```lua
function Link(el)
  return el.content
end
```

**为所有代码块添加行号**：

```lua
function CodeBlock(el)
  if el.classes[1] then  -- 有语言类的代码块
    el.attributes.numberLines = ""
  end
  return el
end
```

**遍历元数据并修改**：

```lua
function Meta(m)
  if m.date then
    -- 将日期格式化为中文
    m.date = pandoc.MetaInlines{pandoc.Str("2026 年 8 月 4 日")}
  end
  return m
end
```

**组合多个函数的过滤器文件**：

```lua
-- myfilter.lua
function Header(el)
  el.level = el.level + 1
  return el
end

function Code(el)
  el.text = el.text:upper()
  return el
end

function Para(el)
  -- 段落处理
  return el
end
```

### 总结

- Lua 过滤器是 `.lua` 文件，定义按节点类型命名的函数。
- 函数返回 `nil`/`el`（保留）、新节点（替换）、`{}`（删除）、`{n1,n2}`（展开）。
- 行内类型：`Str`/`Emph`/`Code`/`Link`/`Image` 等；块级类型：`Para`/`Header`/`CodeBlock`/`Table` 等。
- 节点是 Lua table，字段对应属性（如 `Header.level`、`Image.src`）。
- 一个 `.lua` 文件可包含多个函数，Pandoc 会按节点类型自动匹配调用。

---

## 第 25 讲：常用 Lua 过滤器实战

### 概念

Pandoc 社区提供了大量现成的 Lua 过滤器，覆盖常见需求：交叉引用、图表编号、目录增强、内容抽取、格式转换增强等。本讲介绍几个高频实用的过滤器，并演示如何编写解决具体问题的自定义过滤器。

### 原理

社区过滤器通常发布在 [Pandoc Filters](https://github.com/pandoc/lua-filters) 仓库或个人 GitHub。使用方式：

1. 下载 `.lua` 文件到本地（如 `filters/` 目录）。
2. 用 `--lua-filter=filters/xxx.lua` 调用。
3. 也可将常用过滤器路径写入 `defaults.yaml`（见第 28 讲），自动加载。

编写自定义过滤器的常见模式：

- **遍历 + 条件**：遍历某类节点，按条件修改。
- **状态收集**：先遍历收集信息（如所有图片），再统一处理。
- **元数据驱动**：根据元数据决定变换行为。

### 例子

**例 1：自动为图片编号并生成图片列表**

```lua
-- image-numbering.lua
local img_count = 0

function Image(el)
  img_count = img_count + 1
  local num = img_count
  -- 在 caption 前加"图 N："
  if not el.caption or #el.caption == 0 then
    el.caption = {pandoc.Str("图 " .. num)}
  else
    table.insert(el.caption, 1, pandoc.Str("图 " .. num .. "："))
  end
  el.identifier = "fig-" .. num
  return el
end
```

**例 2：将外部链接改为新窗口打开**

```lua
-- external-links.lua
function Link(el)
  if el.target:match("^https?://") then
    el.attributes.target = "_blank"
    el.attributes.rel = "noopener noreferrer"
  end
  return el
end
```

**例 3：抽取所有代码块到单独文件**

```lua
-- extract-code.lua
local code_blocks = {}

function CodeBlock(el)
  if el.classes[1] then
    local lang = el.classes[1]
    local filename = el.attributes.file or ("snippet-" .. #code_blocks + 1 .. "." .. lang)
    table.insert(code_blocks, {filename = filename, content = el.text})
  end
  return el
end

function Pandoc(doc)
  -- 在文档末尾添加代码清单
  for _, cb in ipairs(code_blocks) do
    local block = pandoc.CodeBlock(cb.content)
    block.attributes.file = cb.filename
    table.insert(doc.blocks, pandoc.Header(2, "代码：" .. cb.filename))
    table.insert(doc.blocks, block)
  end
  return doc
end
```

**例 4：使用社区过滤器 `pandoc-include`（在 Markdown 中嵌入其他文件）**

```bash
# 下载过滤器
curl -o filters/include.lua https://raw.githubusercontent.com/pandoc/lua-filters/master/include/include.lua

# 在 Markdown 中使用：
# ::: include
# chapter1.md
# :::

pandoc book.md --lua-filter=filters/include.lua -o book.html
```

**例 5：使用 `pandoc-crossref` 过滤器（图表/公式/表格交叉引用）**

```bash
# 安装（Haskell 工具）
cabal install pandoc-crossref
# 或下载预编译二进制

# 在 Markdown 中：
# 见 [@fig:workflow] 与 [@tbl:results]。
# ![工作流](workflow.png){#fig:workflow}
# | 指标 | 值 | {#tbl:results}

pandoc report.md --filter=pandoc-crossref -o report.html
```

### 总结

- 社区过滤器覆盖交叉引用、图表编号、内容抽取等常见需求。
- 自定义过滤器常见模式：遍历+条件、状态收集、元数据驱动。
- `Pandoc(doc)` 函数可处理整个文档，访问 `doc.blocks`、`doc.meta`。
- `pandoc-crossref` 是学术写作必备过滤器，实现图表公式表格的自动编号与引用。
- 过滤器路径可写入 `defaults.yaml` 自动加载，避免每次命令行指定。

---

## 第 26 讲：引文与参考文献（BibTeX）

### 概念

学术写作离不开引文与参考文献。Pandoc 内置 `citeproc` 引文处理器，能根据 BibTeX/BibLaTeX/CSL JSON 格式的文献库，自动在文档中插入引文，并在文末生成格式化的参考文献列表。引文格式由 CSL（Citation Style Language）样式文件控制，支持 APA、MLA、Chicago、IEEE、中国国家标准 GB/T 7714 等数千种样式。

### 原理

Pandoc 引文处理流程：

1. 在 Markdown 中用 `@key` 语法引用文献（`key` 是文献库中的条目键）。
2. Pandoc 解析引文标记，生成 `Cite` AST 节点。
3. `citeproc` 过滤器（通过 `--citeproc` 选项启用）查找文献库，根据 CSL 样式格式化引文。
4. 在文末生成参考文献列表（由 `<div id="refs">` 标记位置决定）。

关键选项：

- `--citeproc`：启用引文处理。
- `--bibliography=refs.bib`：指定文献库（BibTeX/BibLaTeX/CSL JSON/CSL YAML）。
- `--csl=style.csl`：指定引文样式（CSL 文件）。
- `--citation-abbreviations=abbr.json`：指定引文缩写表（可选）。

引文语法：

- `[@key]`：括号引文，如 (Smith, 2020)。
- `@key`：行内引文，如 Smith (2020)。
- `[@key1; @key2]`：多引文，如 (Smith, 2020; Jones, 2021)。
- `[-@key]`：抑制作者名，只显示年份，如 (2020)。
- `[see @key, p. 23]`：带前缀与定位，如 (see Smith, 2020, p. 23)。

### 例子

**BibTeX 文献库** `refs.bib`：

```bibtex
@book{knuth1984,
  author    = {Donald E. Knuth},
  title     = {The TeXbook},
  publisher = {Addison-Wesley},
  year      = {1984},
  address   = {Reading, MA}
}

@article{lamport1994,
  author  = {Leslie Lamport},
  title   = {LaTeX: A Document Preparation System},
  journal = {Addison-Wesley},
  year    = {1994}
}

@misc{macfarlane2022,
  author = {John MacFarlane},
  title  = {Pandoc: A Universal Document Converter},
  year   = {2022},
  url    = {https://pandoc.org}
}
```

**Markdown 文档** `paper.md`：

```markdown
---
title: Pandoc 引文示例
bibliography: refs.bib
csl: ieee.csl
---

# 引言

Pandoc 是一款通用文档转换器 [@macfarlane2022]。
它深受 TeX [@knuth1984] 与 LaTeX [@lamport1994] 的影响。

# 参考文献

::: {#refs}
:::
```

**生成带引文的文档**：

```bash
pandoc paper.md --citeproc -o paper.html
```

**使用 GB/T 7714 样式（中国国家标准）**：

```bash
# 下载 CSL 样式
curl -o gb-t-7714.csl \
  https://www.zotero.org/styles/china-national-standard-gb-t-7714-2015-numeric

pandoc paper.md --citeproc --csl=gb-t-7714.csl -o paper.html
```

**在 YAML 中配置引文**（推荐）：

```yaml
---
title: 我的论文
bibliography: refs.bib
csl: ieee.csl
link-citations: true
---
```

**生成带引文的 PDF**：

```bash
pandoc paper.md \
  --citeproc \
  --pdf-engine=xelatex \
  -V documentclass=ctexart \
  -o paper.pdf
```

### 总结

- Pandoc 用 `citeproc` 处理引文，支持 BibTeX/BibLaTeX/CSL JSON 文献库。
- 引文语法：`[@key]`（括号）、`@key`（行内）、`[-@key]`（抑制作者）、`[see @key, p. 23]`（带定位）。
- `--csl` 指定引文样式，支持 APA/MLA/IEEE/GB-T-7714 等数千种。
- 参考文献列表位置由 `<div id="refs">` 标记决定。
- 在 YAML 中配置 `bibliography`/`csl`/`link-citations` 是推荐做法。

---

## 第 27 讲：JSON 过滤器与 Pandoc Python

### 概念

JSON 过滤器是用任意编程语言编写的过滤器，通过 stdin/stdout 与 Pandoc 交换 JSON 格式的 AST。相比 Lua 过滤器，JSON 过滤器更灵活（可用任何语言），但启动开销更大（每次调用都要启动外部进程）。Python 是编写 JSON 过滤器的热门语言，社区提供了 `pandocfilters`、`panflute` 等库简化开发。

### 原理

JSON 过滤器的工作流程：

1. Pandoc 将 AST 序列化为 JSON，通过 stdin 传给过滤器进程。
2. 过滤器进程读取 JSON，解析、修改、重新序列化为 JSON。
3. Pandoc 从过滤器的 stdout 读取修改后的 JSON AST，继续后续处理。

Python 库选择：

- **`pandocfilters`**：最基础的库，提供 `walk`、`attrs` 等工具函数。
- **`panflute`**：更高级的库，提供类似 Lua 过滤器的"按节点类型匹配"API，更易用。

安装：

```bash
pip install pandocfilters
pip install panflute
```

### 例子

**用 `panflute` 编写过滤器** `myfilter.py`：

```python
import panflute as pf

def action(elem, doc):
    if isinstance(elem, pf.Header):
        # 将所有标题降一级
        elem.level += 1
        if elem.level > 6:
            elem.level = 6
        return elem

    if isinstance(elem, pf.Code):
        # 行内代码转大写
        elem.text = elem.text.upper()
        return elem

def main(doc=None):
    return pf.run_filter(action, doc=doc)

if __name__ == "__main__":
    main()
```

**使用该过滤器**：

```bash
pandoc input.md --filter=myfilter.py -o output.html
```

**用 `pandocfilters` 编写过滤器** `simple.py`：

```python
#!/usr/bin/env python
import pandocfilters as pf

def caps(key, value, format, meta):
    if key == "Str":
        return pf.Str(value.upper())

if __name__ == "__main__":
    pf.toJSONFilter(caps)
```

**使用该过滤器**：

```bash
chmod +x simple.py
pandoc input.md --filter=./simple.py -o output.html
```

**用 JavaScript 编写过滤器** `link-checker.js`（检查所有链接是否可访问）：

```javascript
#!/usr/bin/env node
const pandoc = require('pandoc-filter');
const { Link, stringify } = pandoc;

function action(type, value, format, meta) {
    if (type === 'Link') {
        const [inline, [url, title]] = value;
        console.error(`检查链接: ${url}`);
        // 这里可加入 HTTP 请求检查逻辑
    }
}

pandoc.toJSONFilter(action);
```

**调试 JSON 过滤器**（查看输入的 AST）：

```bash
pandoc input.md -t json | python -m json.tool > ast.json
# 编辑 ast.json，模拟过滤器输出
pandoc ast.json -f json -t html -o output.html
```

### 总结

- JSON 过滤器通过 stdin/stdout 与 Pandoc 交换 JSON AST，可用任何语言编写。
- Python 库：`pandocfilters`（基础）、`panflute`（高级，推荐）。
- `panflute` 用 `isinstance(elem, pf.Header)` 按类型匹配，类似 Lua 过滤器。
- JSON 过滤器启动开销大于 Lua，适合复杂逻辑或需要外部库的场景。
- 调试时用 `pandoc -t json` 查看 AST，手动编辑后用 `pandoc -f json` 测试。

---

# 第 6 章 高级应用与工作流

前 5 章覆盖了 Pandoc 的语法、转换、模板、过滤器等"零件"。本章将这些零件组装成完整的"工作流"，覆盖学术写作、电子书制作、文档网站、CI/CD 集成、性能优化等真实场景。学完后你将能把 Pandoc 融入自己的日常工作流，实现"一份源文件、多目标发布"的文档自动化。

---

## 第 28 讲：默认配置文件 defaults.yaml

### 概念

随着 Pandoc 命令变长，每次手动输入几十个选项既繁琐又易错。Pandoc 提供"默认配置文件（defaults file）"机制，允许将常用选项固化到 YAML 文件中，通过 `--defaults=name` 或 `-d name` 加载。这是管理复杂 Pandoc 命令的最佳实践。

### 原理

默认配置文件是 YAML 格式，位于 `~/.pandoc/defaults/`（用户级）或项目目录的 `defaults/` 子目录下。文件名（不含 `.yaml`）即为配置名。配置文件中的键与 Pandoc 命令行选项一一对应，但用 YAML 语法表达：

- 短选项用全名：`to` 而非 `t`，`output` 而非 `o`。
- 重复选项用列表：`lua-filter: [a.lua, b.lua]`。
- 嵌套选项用映射：`variables: {documentclass: ctexart}`。
- 元数据用 `metadata` 键。

加载方式：

- `pandoc -d name input.md -o output.html`：加载 `defaults/name.yaml`。
- 多个配置可叠加：`pandoc -d base -d pdf input.md -o output.pdf`。

### 例子

**用户级默认配置** `~/.pandoc/defaults/common.yaml`：

```yaml
# 公共默认配置
from: markdown
to: html5
standalone: true
toc: true
toc-depth: 3
number-sections: true
css: [style.css, print.css]
metadata:
  lang: zh-CN
  author: 张三
variables:
  lang: zh-CN
```

**PDF 专用配置** `~/.pandoc/defaults/pdf.yaml`：

```yaml
to: pdf
pdf-engine: xelatex
variables:
  documentclass: ctexart
  geometry: margin=2.5cm
  fontsize: 11pt
  CJKmainfont: Noto Serif CJK SC
  CJKsansfont: Noto Sans CJK SC
  CJKmonofont: Sarasa Mono SC
toc: true
number-sections: true
```

**docx 专用配置** `~/.pandoc/defaults/docx.yaml`：

```yaml
to: docx
reference-doc: refs/corporate.docx
toc: true
number-sections: true
```

**学术写作配置** `~/.pandoc/defaults/paper.yaml`：

```yaml
to: pdf
pdf-engine: xelatex
citeproc: true
bibliography: refs.bib
csl: ieee.csl
variables:
  documentclass: ctexart
  geometry: margin=2.5cm
toc: true
number-sections: true
lua-filter:
  - filters/crossref.lua
  - filters/image-numbering.lua
```

**使用配置文件**：

```bash
# 加载单个配置
pandoc -d common input.md -o output.html

# 叠加多个配置（后者覆盖前者）
pandoc -d common -d pdf input.md -o output.pdf

# 命令行选项可覆盖配置文件
pandoc -d pdf -V fontsize=12pt input.md -o output.pdf
```

**项目级配置**（在项目根目录创建 `defaults/`）：

```
my-project/
├── defaults/
│   ├── common.yaml
│   ├── html.yaml
│   └── pdf.yaml
├── chapters/
│   ├── ch1.md
│   └── ch2.md
└── Makefile
```

```bash
# 在项目目录中运行
pandoc -d html chapters/*.md -o book.html
pandoc -d pdf chapters/*.md -o book.pdf
```

### 总结

- 默认配置文件用 YAML 固化常用选项，避免冗长命令行。
- 配置文件位于 `~/.pandoc/defaults/`（用户级）或项目 `defaults/` 目录。
- 键名用全名（`to`/`output`/`standalone`），重复选项用列表，嵌套用映射。
- `-d name` 加载配置，可叠加多个，命令行选项覆盖配置文件。
- 为不同场景（HTML/PDF/docx/论文）准备不同配置，实现"一键发布"。

---

## 第 29 讲：学术论文写作工作流

### 概念

学术论文是 Pandoc 最经典的应用场景之一。通过 Markdown + YAML 元数据 + BibTeX 文献库 + CSL 样式 + LaTeX 模板，可以构建一套"单一源、多目标"的论文写作工作流：一份 Markdown 源文件，同时生成投稿用的 PDF、归档用的 docx、预印本用的 HTML。

### 原理

学术论文工作流的核心要素：

1. **源文件结构**：每篇论文一个目录，含 `paper.md`（正文）、`refs.bib`（文献库）、`meta.yaml`（元数据）、`figures/`（图片）。
2. **元数据**：标题、作者、单位、邮箱、摘要、关键词、JEL/MSC 分类等。
3. **引文**：用 `@key` 语法引用，`citeproc` 处理，CSL 样式控制格式。
4. **数学公式**：Markdown 中用 `$...$`（行内）与 `$$...$$`（行间），Pandoc 自动转为 LaTeX/MathJax。
5. **图表编号**：用 `pandoc-crossref` 过滤器实现自动编号与交叉引用。
6. **多目标输出**：用 defaults 文件分别配置 PDF（投稿）、docx（审稿）、HTML（预印本）。

### 例子

**论文目录结构**：

```
my-paper/
├── paper.md
├── meta.yaml
├── refs.bib
├── csl/
│   └── ieee.csl
├── figures/
│   ├── workflow.png
│   └── results.png
├── templates/
│   └── paper.tex
├── defaults/
│   ├── pdf.yaml
│   ├── docx.yaml
│   └── html.yaml
└── Makefile
```

**`paper.md`**（正文）：

```markdown
---
title: 基于 Pandoc 的学术写作工作流
author:
  - name: 张三
    affiliation: 示例大学
    email: zhangsan@example.edu
  - name: 李四
    affiliation: 示例研究所
date: 2026-08-04
abstract: |
  本文提出一套基于 Pandoc 的学术写作工作流……
keywords: [Pandoc, 学术写作, 文档自动化]
bibliography: refs.bib
csl: csl/ieee.csl
link-citations: true
---

# 引言

学术写作长期被 Word 与 LaTeX 二分天下 [@lamport1994]。
本文提出第三条道路：以 Markdown 为源，以 Pandoc 为引擎。

# 方法

## 工作流设计

本工作流的核心是"单一源、多目标"，如 [@fig:workflow] 所示。

![工作流总览](figures/workflow.png){#fig:workflow}

## 实验结果

实验结果见 [@tbl:results]。

| 方法     | 准确率 | F1   |
|----------|-------:|-----:|
| 基线     |   85.2 | 0.84 |
| 本文方法 |   91.5 | 0.90 |

: 实验结果对比 {#tbl:results}

# 结论

本工作流显著提升了学术写作的效率与可复现性。
```

**`defaults/pdf.yaml`**：

```yaml
to: pdf
pdf-engine: xelatex
citeproc: true
template: templates/paper.tex
variables:
  documentclass: ctexart
  geometry: margin=2.5cm
  fontsize: 11pt
  CJKmainfont: Noto Serif CJK SC
toc: false
number-sections: true
filter:
  - pandoc-crossref
```

**`defaults/docx.yaml`**：

```yaml
to: docx
reference-doc: templates/paper-ref.docx
citeproc: true
number-sections: true
filter:
  - pandoc-crossref
```

**`defaults/html.yaml`**：

```yaml
to: html5
standalone: true
citeproc: true
css: templates/paper.css
katex: true
number-sections: true
filter:
  - pandoc-crossref
```

**`Makefile`**：

```makefile
PAPER = paper

all: pdf docx html

pdf:
        pandoc -d defaults/pdf $(PAPER).md -o $(PAPER).pdf

docx:
        pandoc -d defaults/docx $(PAPER).md -o $(PAPER).docx

html:
        pandoc -d defaults/html $(PAPER).md -o $(PAPER).html

clean:
        rm -f $(PAPER).pdf $(PAPER).docx $(PAPER).html

.PHONY: all pdf docx html clean
```

**一键生成所有格式**：

```bash
make all
```

### 总结

- 论文工作流核心：Markdown 源 + YAML 元数据 + BibTeX 文献库 + CSL 样式。
- 用 `pandoc-crossref` 实现图表/公式/表格的自动编号与交叉引用。
- 为 PDF/docx/HTML 分别准备 defaults 文件，一键生成多目标。
- 用 Makefile 封装命令，`make all` 一键发布。
- 这套工作流的优势：版本可控（纯文本）、可复现、多目标发布、与 Git/CI 无缝集成。

---

## 第 30 讲：电子书制作工作流

### 概念

电子书是 Pandoc 另一经典场景。通过将每章存为独立 Markdown 文件，用 Pandoc 合并为 EPUB 或 PDF，配合封面、CSS、元数据，可以构建一套"自出版"工作流。相比 Scrivener、Sigil 等专用工具，Pandoc 工作流的优势是纯文本、版本可控、可脚本化。

### 原理

电子书工作流的核心要素：

1. **章节拆分**：每章一个 `.md` 文件，便于编辑与版本控制。
2. **元数据**：书名、作者、ISBN、语言、版权、出版日期等。
3. **封面图**：EPUB 必备，PDF 可选。
4. **CSS**：控制电子书的排版（字体、行距、章节标题样式）。
5. **分章策略**：EPUB 按一级标题自动分章，PDF 用 `\chapter` 或 `book` 文档类。
6. **图片资源**：所有图片放入 `images/` 目录，Markdown 中用相对路径引用。

### 例子

**电子书目录结构**：

```
my-book/
├── chapters/
│   ├── 00-frontmatter.md
│   ├── 01-introduction.md
│   ├── 02-chapter1.md
│   ├── 03-chapter2.md
│   └── 99-backmatter.md
├── images/
│   ├── cover.jpg
│   └── fig01.png
├── meta.yaml
├── epub.css
├── defaults/
│   ├── epub.yaml
│   └── pdf.yaml
└── Makefile
```

**`meta.yaml`**：

```yaml
title: 我的电子书
author: 张三
lang: zh-CN
date: 2026-08-04
rights: "© 2026 张三，保留所有权利。"
identifier: "ISBN 978-0-000000-00-0"
description: |
  这是一本关于 Pandoc 的电子书。
```

**`chapters/00-frontmatter.md`**（前言）：

```markdown
---
title: 前言
---

# 前言

本书献给所有热爱文档的人……

![封面](../images/cover.jpg)
```

**`chapters/01-introduction.md`**（第一章）：

```markdown
# 引言

## 为什么用 Pandoc

Pandoc 是一款通用文档转换器……
```

**`epub.css`**：

```css
body {
  font-family: "Noto Serif SC", serif;
  line-height: 1.8;
  margin: 5% 8%;
}

h1, h2, h3 {
  font-family: "Noto Sans SC", sans-serif;
  page-break-before: always;
  margin-top: 2em;
}

h1 { font-size: 1.8em; text-align: center; }
h2 { font-size: 1.4em; }

img { max-width: 100%; }
figure { text-align: center; margin: 1em 0; }
figcaption { font-size: 0.9em; color: #666; }

blockquote {
  margin: 1em 2em;
  font-style: italic;
  color: #555;
}

code { font-family: "Sarasa Mono SC", monospace; }
pre {
  background: #f5f5f5;
  padding: 1em;
  white-space: pre-wrap;
  word-wrap: break-word;
}
```

**`defaults/epub.yaml`**：

```yaml
to: epub
epub-cover-image: images/cover.jpg
css: epub.css
epub-chapter-level: 1
metadata-file: meta.yaml
toc: true
toc-depth: 2
```

**`defaults/pdf.yaml`**：

```yaml
to: pdf
pdf-engine: xelatex
variables:
  documentclass: ctexbook
  geometry: margin=2.5cm
  CJKmainfont: Noto Serif CJK SC
  mainfont: Times New Roman
toc: true
toc-depth: 2
number-sections: true
```

**`Makefile`**：

```makefile
CHAPTERS = $(wildcard chapters/*.md)
BOOK = my-book

all: epub pdf

epub:
        pandoc -d defaults/epub $(CHAPTERS) -o $(BOOK).epub

pdf:
        pandoc -d defaults/pdf $(CHAPTERS) -o $(BOOK).pdf

preview-epub: epub
        ebook-viewer $(BOOK).epub &

clean:
        rm -f $(BOOK).epub $(BOOK).pdf

.PHONY: all epub pdf preview-epub clean
```

**一键生成电子书**：

```bash
make all
```

### 总结

- 电子书工作流：每章一个 Markdown 文件，Pandoc 合并为 EPUB/PDF。
- EPUB 必备封面图（`--epub-cover-image`）与 CSS（`--css`）。
- PDF 用 `ctexbook` 文档类，按章自动分页。
- 元数据用 `--metadata-file` 从外部 YAML 加载。
- 用 Makefile 封装命令，`make all` 一键生成 EPUB + PDF。

---

## 第 31 讲：文档网站生成与 CI/CD 集成

### 概念

Pandoc 不仅是"转换器"，还能作为"静态网站生成器"使用。通过将多个 Markdown 文件转为 HTML 片段，配合导航、索引、搜索，可以构建一个完整的文档网站。结合 Git 与 CI/CD（GitHub Actions、GitLab CI），可以实现"提交即发布"的自动化文档部署。

### 原理

Pandoc 生成文档网站的两种模式：

1. **单文件模式**：将所有 Markdown 合并为一个长 HTML，适合小型文档。
2. **多文件模式**：每个 Markdown 生成一个 HTML 文件，配合导航栏与目录，适合大型文档。

CI/CD 集成思路：

1. 文档源文件托管在 Git 仓库。
2. 每次推送触发 CI 流水线。
3. CI 环境安装 Pandoc，运行构建脚本。
4. 将生成的 HTML 部署到 GitHub Pages、Netlify、Vercel 等。

### 例子

**文档网站目录结构**：

```
docs-site/
├── content/
│   ├── index.md
│   ├── getting-started.md
│   ├── guide/
│   │   ├── installation.md
│   │   ├── usage.md
│   │   └── advanced.md
│   └── api/
│       └── reference.md
├── templates/
│   └── site.html
├── assets/
│   ├── style.css
│   └── nav.html
├── build.sh
├── defaults/
│   └── site.yaml
└── .github/workflows/deploy.yml
```

**`build.sh`**（构建脚本）：

```bash
#!/bin/bash
set -e

CONTENT_DIR="content"
OUTPUT_DIR="public"

rm -rf "$OUTPUT_DIR"
mkdir -p "$OUTPUT_DIR"

# 生成每个 Markdown 的 HTML
find "$CONTENT_DIR" -name "*.md" | while read -r file; do
  rel="${file#$CONTENT_DIR/}"
  out="$OUTPUT_DIR/${rel%.md}.html"
  mkdir -p "$(dirname "$out")"
  pandoc "$file" \
    -d defaults/site.yaml \
    --template=templates/site.html \
    -o "$out"
  echo "生成: $out"
done

# 复制静态资源
cp -r assets/* "$OUTPUT_DIR/"

echo "构建完成：$OUTPUT_DIR"
```

**`templates/site.html`**（网站模板）：

```html
<!DOCTYPE html>
<html lang="$lang$">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>$if(title)$$title$ — $endif$我的文档站</title>
  <link rel="stylesheet" href="/style.css">
</head>
<body>
  <header>
    <nav>
      <a href="/">首页</a> |
      <a href="/getting-started.html">快速开始</a> |
      <a href="/guide/installation.html">安装</a> |
      <a href="/guide/usage.html">使用</a> |
      <a href="/api/reference.html">API</a>
    </nav>
  </header>
  <main>
    $if(title)$<h1>$title$</h1>$endif$
    $body$
  </main>
  <footer>
    <hr>
    <p>© 2026 我的项目 · 由 Pandoc 生成</p>
  </footer>
</body>
</html>
```

**`defaults/site.yaml`**：

```yaml
from: markdown
to: html5
standalone: true
toc: true
toc-depth: 3
section-divs: true
highlight-style: tango
metadata:
  lang: zh-CN
```

**GitHub Actions 部署工作流** `.github/workflows/deploy.yml`：

```yaml
name: Deploy Docs

on:
  push:
    branches: [main]
    paths:
      - 'content/**'
      - 'templates/**'
      - 'assets/**'
      - 'build.sh'
      - '.github/workflows/deploy.yml'

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - uses: actions/checkout@v4

      - name: Install Pandoc
        run: |
          sudo apt-get update
          sudo apt-get install -y pandoc texlive-xetex texlive-lang-chinese

      - name: Build site
        run: bash build.sh

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: public

      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

**本地预览**：

```bash
bash build.sh
cd public && python -m http.server 8000
# 浏览器访问 http://localhost:8000
```

### 总结

- Pandoc 可作为轻量级静态网站生成器，适合文档站、博客、知识库。
- 多文件模式：每个 Markdown 生成一个 HTML，配合导航与目录。
- 用 `build.sh` 脚本批量构建，`find` 遍历所有 Markdown。
- GitHub Actions 实现"提交即发布"：安装 Pandoc → 构建 → 部署到 Pages。
- 相比 Hugo/Jekyll，Pandoc 工作流更简单，适合"以内容为中心"的文档站。

---

## 第 32 讲：性能优化与最佳实践

### 概念

随着文档规模增长（数百页、数千图片、大型文献库），Pandoc 转换可能变慢。本讲介绍 Pandoc 性能优化的常见手段与文档工程的最佳实践，帮助你构建高效、可维护的文档工作流。

### 原理

Pandoc 性能瓶颈通常出现在：

1. **大型文献库**：`citeproc` 每次都要解析整个 `.bib` 文件，文献库越大越慢。
2. **大量图片**：HTML 输出时若启用 `--embed-resources`，所有图片要 base64 编码。
3. **复杂过滤器**：JSON 过滤器每次调用都要启动外部进程，多次调用累积开销大。
4. **LaTeX 编译**：转 PDF 时 LaTeX 引擎编译是主要耗时环节，尤其大型文档。
5. **重复转换**：开发过程中反复运行完整转换，浪费时间。

优化策略：

- 用 Lua 过滤器替代 JSON 过滤器（无进程启动开销）。
- 拆分大型文献库为子库，按主题引用。
- 开发时用 `--to=markdown` 快速预览，最终再用 `--to=pdf`。
- 用 Makefile 的增量构建，只重建变更部分。
- 缓存 LaTeX 编译中间结果（`.aux` 文件）。

### 例子

**优化 1：用 Lua 过滤器替代 JSON 过滤器**

```bash
# 慢：JSON 过滤器，每次启动 Python 进程
pandoc input.md --filter=myfilter.py -o output.html

# 快：等价的 Lua 过滤器，内置执行
pandoc input.md --lua-filter=myfilter.lua -o output.html
```

**优化 2：拆分大型文献库**

```bibtex
# refs-main.bib（主文献库，< 100 条）
@article{key1, ...}

# refs-supplementary.bib（补充文献，按需引用）
@article{key2, ...}
```

```bash
# 只加载需要的文献库
pandoc paper.md --citeproc --bibliography=refs-main.bib -o paper.pdf
```

**优化 3：开发时快速预览**

```bash
# 开发时：转 Markdown 快速检查结构
pandoc draft.md -t gfm -o preview.md

# 最终：转 PDF
pandoc draft.md -d defaults/pdf -o final.pdf
```

**优化 4：Makefile 增量构建**

```makefile
SOURCES = $(wildcard chapters/*.md)
HTML_TARGETS = $(SOURCES:.md=.html)

all: $(HTML_TARGETS)

%.html: %.md
        pandoc $< -d defaults/html -o $@

clean:
        rm -f chapters/*.html

.PHONY: all clean
```

**优化 5：缓存 LaTeX 中间文件**

```makefile
paper.pdf: paper.md refs.bib
        pandoc paper.md -d defaults/pdf -o paper.pdf
        # 保留 .aux 文件以便下次增量编译

# 手动重新编译（利用 .aux 缓存）
paper-fast.pdf: paper.tex
        xelatex -interaction=nonstopmode paper.tex
```

**优化 6：并行构建多目标**

```makefile
all: pdf docx html

pdf:
        pandoc paper.md -d defaults/pdf -o paper.pdf &

docx:
        pandoc paper.md -d defaults/docx -o paper.docx &

html:
        pandoc paper.md -d defaults/html -o paper.html &

wait:
        wait

all: wait
```

**最佳实践清单**：

1. **源文件纯文本**：所有内容用 Markdown/YAML/BibTeX，避免二进制格式。
2. **版本控制**：用 Git 管理文档源，每次提交可追溯。
3. **目录结构清晰**：`chapters/`、`images/`、`templates/`、`defaults/` 分离。
4. **defaults 文件**：固化常用配置，避免冗长命令。
5. **Makefile 封装**：`make pdf`、`make all` 一键操作。
6. **CI/CD 自动化**：提交即构建、即部署。
7. **模块化写作**：每章一个文件，便于协作与复用。
8. **图片优化**：用 WebP 替代 PNG/JPG，体积更小。
9. **文献库管理**：用 Zotero/JabRef 维护 BibTeX，按主题拆分。
10. **定期清理**：`make clean` 删除中间产物，避免污染仓库。

### 总结

- 性能瓶颈：大型文献库、大量图片、JSON 过滤器、LaTeX 编译、重复转换。
- 优化手段：Lua 过滤器、拆分文献库、快速预览、Makefile 增量构建、缓存 `.aux`。
- 最佳实践：纯文本源、Git 版本控制、清晰目录、defaults 文件、Makefile 封装、CI/CD 自动化。
- 模块化写作：每章一个文件，便于协作与复用。
- 这套实践让 Pandoc 工作流从"个人脚本"升级为"团队工程"，支撑大规模文档生产。

---

## 结语

至此，32 讲的 Pandoc 系统教程完结。从第 1 讲的安装与首次转换，到第 32 讲的性能优化与工程实践，我们走过了 Pandoc 的完整知识图谱：

- **第 1 章** 奠定了基础：理解 Pandoc 的 AST 架构与命令行用法。
- **第 2 章** 深入语法：掌握 Pandoc Markdown 的全部扩展能力。
- **第 3 章** 实战转换：覆盖 HTML、PDF、Word、LaTeX、EPUB、幻灯片六大目标。
- **第 4 章** 定制样式：通过模板、参考文档、CSS 完全控制输出外观。
- **第 5 章** 扩展能力：用 Lua/JSON 过滤器实现任意文档变换，用 citeproc 处理引文。
- **第 6 章** 工程实践：将 Pandoc 融入学术写作、电子书、文档网站、CI/CD 等真实场景。

Pandoc 的核心理念是"单一源、多目标"——用一份纯文本源文件，驱动任意格式的输出。这不仅是一种工具用法，更是一种文档哲学：内容与形式分离、源文件可版本控制、发布流程可自动化。掌握这套哲学后，你将彻底摆脱"在 Word 里反复调格式"的痛苦，进入"专注内容、自动发布"的文档新纪元。

愿 Pandoc 成为你写作路上的得力助手。

