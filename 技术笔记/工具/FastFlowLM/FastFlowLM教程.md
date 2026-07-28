# FastFlowLM 从入门到实战

> 一本面向开发者的 NPU 本地大模型推理教程
>
> 课程总讲数：25 讲 ｜ 共 7 章 ｜ 适用对象：有 Python 基础、对本地 LLM 部署感兴趣的开发者

---

## 课程总览

FastFlowLM（简称 FLM）是一款专为 AMD Ryzen™ AI NPU 打造的轻量级大语言模型推理运行时。它以 17 MB 的极小体积、20 秒的安装速度，提供了 Ollama 式的开发体验和 OpenAI 兼容的 API 接口，支持 LLaMA、Qwen、Gemma、Phi 等主流模型家族，以及视觉、语音、嵌入等多模态能力，上下文窗口最高可达 256k tokens。本课程将带你从零开始，系统掌握 FastFlowLM 的安装、使用与进阶开发。

### 学习目标

完成本课程后，你将能够：

1. 理解 NPU 推理的核心原理与 FastFlowLM 的架构设计哲学
2. 在 Windows 与 Linux 环境下完成 FastFlowLM 的安装与 NPU 驱动配置
3. 熟练使用 CLI 模式进行交互式对话，掌握模型管理命令
4. 启动 Server 模式，通过 OpenAI 兼容 API 开发 Python 应用
5. 实现工具调用、RAG 检索增强、多模态推理等进阶功能
6. 具备性能调优、安全部署与综合项目实战能力

### 课程结构

| 章节 | 主题 | 讲数 | 阶段 |
|------|------|------|------|
| 第1章 | 走进 FastFlowLM（基础认知） | 4 讲 | 基础 |
| 第2章 | 环境准备与安装 | 3 讲 | 基础 |
| 第3章 | CLI 模式——终端交互 | 4 讲 | 核心 |
| 第4章 | Server 模式与 OpenAI 兼容 API | 4 讲 | 核心 |
| 第5章 | 模型生态与选型 | 3 讲 | 核心 |
| 第6章 | 进阶应用开发 | 4 讲 | 进阶 |
| 第7章 | 高级实战与优化 | 3 讲 | 高级/实战 |

### 详细目录

- [第1章 走进 FastFlowLM](#第1章-走进-fastflowlm)
  - [第1讲：什么是 FastFlowLM——NPU 优先的 LLM 推理运行时](#第1讲什么是-fastflowlmnpu-优先的-llm-推理运行时)
  - [第2讲：NPU vs GPU vs CPU——为什么需要专用 AI 加速器](#第2讲npu-vs-gpu-vs-cpu为什么需要专用-ai-加速器)
  - [第3讲：FastFlowLM 核心特性与架构总览](#第3讲fastflowlm-核心特性与架构总览)
  - [第4讲：FastFlowLM 与 Ollama、llama.cpp 的对比](#第4讲fastflowlm-与-ollamallamacpp-的对比)
- [第2章 环境准备与安装](#第2章-环境准备与安装)
  - [第5讲：硬件要求与 NPU 驱动检查](#第5讲硬件要求与-npu-驱动检查)
  - [第6讲：Windows 安装详解](#第6讲windows-安装详解)
  - [第7讲：Linux 安装详解](#第7讲linux-安装详解)
- [第3章 CLI 模式——终端交互](#第3章-cli-模式终端交互)
  - [第8讲：flm 命令体系总览](#第8讲flm-命令体系总览)
  - [第9讲：模型管理——pull / list / remove / check](#第9讲模型管理pull--list--remove--check)
  - [第10讲：交互式对话——CLI 模式内置命令](#第10讲交互式对话cli-模式内置命令)
  - [第11讲：运行参数调优——ctx-len / pmode / prefill-chunk](#第11讲运行参数调优ctx-len--pmode--prefill-chunk)
- [第4章 Server 模式与 OpenAI 兼容 API](#第4章-server-模式与-openai-兼容-api)
  - [第12讲：启动 Server——端口、主机、CORS 配置](#第12讲启动-server端口主机cors-配置)
  - [第13讲：OpenAI API 标准与多角色消息格式](#第13讲openai-api-标准与多角色消息格式)
  - [第14讲：Python + OpenAI SDK 调用 FastFlowLM](#第14讲python--openai-sdk-调用-fastflowlm)
  - [第15讲：流式输出与多轮对话](#第15讲流式输出与多轮对话)
- [第5章 模型生态与选型](#第5章-模型生态与选型)
  - [第16讲：支持的模型家族总览](#第16讲支持的模型家族总览)
  - [第17讲：模型标签与版本管理](#第17讲模型标签与版本管理)
  - [第18讲：多模态模型——VLM、Whisper ASR、Embedding](#第18讲多模态模型vlmwhisper-asrembedding)
- [第6章 进阶应用开发](#第6章-进阶应用开发)
  - [第19讲：工具调用（Tool Calling）实战](#第19讲工具调用tool-calling实战)
  - [第20讲：基于 LangChain 构建 RAG 系统](#第20讲基于-langchain-构建-rag-系统)
  - [第21讲：多模态输入——图像 + 文本联合推理](#第21讲多模态输入图像--文本联合推理)
  - [第22讲：批量推理与长文档处理](#第22讲批量推理与长文档处理)
- [第7章 高级实战与优化](#第7章-高级实战与优化)
  - [第23讲：性能调优——NPU 功耗模式与上下文策略](#第23讲性能调优npu-功耗模式与上下文策略)
  - [第24讲：隐私、安全与离线部署](#第24讲隐私安全与离线部署)
  - [第25讲：综合实战——构建本地 AI 知识助手](#第25讲综合实战构建本地-ai-知识助手)

---

## 第1章 走进 FastFlowLM

本章是整个课程的起点，旨在帮助你建立对 FastFlowLM 的整体认知。我们将从"它是什么"出发，逐步深入到"它为什么存在""它如何工作"以及"它与其他方案有何不同"。这一章不涉及具体代码操作，但它是后续所有实战内容的理论基石。理解了本章内容，你就能在面对各种技术选型决策时，清晰地判断 FastFlowLM 是否适合你的场景。

### 第1讲：什么是 FastFlowLM——NPU 优先的 LLM 推理运行时

#### 概念

FastFlowLM（FLM）是一款**专为 AMD Ryzen™ AI NPU 打造的轻量级大语言模型推理运行时**。所谓"运行时"（runtime），是指它不是一个训练框架，而是一个负责把已经训练好的模型加载到硬件上并高效执行推理任务的软件层。FastFlowLM 的核心理念可以用一句话概括：**让每一台搭载 Ryzen AI 芯片的 PC，都能像调用云 API 一样轻松地运行本地大模型**。

FastFlowLM 由一支专注于 NPU 优化的工程团队开发，该团队已于 2026 年 7 月正式加入 AMD，使其成为 AMD 官方生态的重要组成部分。它以约 17 MB 的极小体积分发，安装过程通常在 20 秒内完成，无需复杂的驱动配置或 CUDA 工具链，开箱即用。其编排代码与 CLI 工具采用 MIT 开源协议，而 NPU 加速的二进制内核则对任何用途（包括商业用途）完全免费。

#### 原理

FastFlowLM 之所以能做到"小而快"，关键在于它的**NPU 优先（NPU-first）设计哲学**。传统的本地 LLM 推理方案（如 llama.cpp、Ollama）大多围绕 GPU 或 CPU 设计，NPU 往往处于"闲置硅片"（idle silicon）状态——芯片物理存在，却没有软件能够充分利用它。FastFlowLM 针对性地解决了这个问题。

其工作原理可以拆解为三个层面。第一，**硬件层**：FastFlowLM 专门适配 AMD XDNA2 架构的 NPU（包括 Strix、Strix Halo、Kraken、Gorgon Point 等芯片系列），通过预编译的高度优化二进制内核直接调用 NPU 的 AI 引擎。第二，**模型层**：它从 HuggingFace 拉取经过 NPU 优化的模型内核（而非通用的 GGUF 格式），确保算子与硬件特性深度匹配。第三，**接口层**：它提供 Ollama 风格的 CLI 和 OpenAI 兼容的 REST API，让开发者无需学习新的接口规范即可上手。

这种设计带来了一个显著优势：**能效比**。根据官方数据，FastFlowLM 相比 GPU 优先的推理方案，能效提升超过 10 倍，这意味着在笔记本等移动设备上，你可以用更低的功耗、更长的电池续航运行大模型。

#### 例子

最直观的体验来自 FastFlowLM 的"Hello World"——一行命令启动对话：

```bash
# 安装后，在 PowerShell 或终端执行
flm run llama3.2:1b
```

执行这条命令后，FastFlowLM 会自动完成以下流程：检查本地是否已有 `llama3.2:1b` 模型 → 若没有则从 HuggingFace 下载优化内核 → 将模型加载到 NPU → 启动交互式对话界面。整个过程对用户透明，你只需等待片刻，就能在终端里和模型聊天。

从架构视角看，FastFlowLM 的整体结构如下：

```
┌─────────────────────────────────────────────┐
│           开发者 / 应用层                    │
│   (CLI 终端 / Python SDK / Web UI)          │
└──────────────┬──────────────────────────────┘
               │ OpenAI 兼容 API / CLI 命令
┌──────────────▼──────────────────────────────┐
│         FastFlowLM 运行时 (17 MB)            │
│  ┌─────────┐  ┌──────────┐  ┌────────────┐  │
│  │ CLI 引擎│  │ REST 服务│  │ 模型管理器 │  │
│  └─────────┘  └──────────┘  └────────────┘  │
│  ┌──────────────────────────────────────┐   │
│  │     NPU 优化算子内核 (二进制)        │   │
│  └──────────────────────────────────────┘   │
└──────────────┬──────────────────────────────┘
               │ XRT 驱动接口
┌──────────────▼──────────────────────────────┐
│      AMD Ryzen AI NPU (XDNA2 架构)          │
│   Strix / Strix Halo / Kraken / Gorgon      │
└─────────────────────────────────────────────┘
```

#### 总结

本讲的核心要点如下：FastFlowLM 是一款 NPU 优先的 LLM 推理运行时，专为 AMD Ryzen AI 芯片打造；它体积小（17 MB）、安装快（20 秒）、能效高（比 GPU 方案节能 10 倍以上）；它通过预编译的 NPU 优化内核和 Ollama 式接口，让本地大模型推理变得像云 API 调用一样简单。需要注意的是，FastFlowLM 目前仅支持搭载 XDNA2 NPU 的 Ryzen AI 系列芯片，如果你的设备没有这类 NPU，则无法使用。在后续学习中，请始终记住它的定位——**不是通用推理框架，而是 NPU 专用加速方案**。

---

### 第2讲：NPU vs GPU vs CPU——为什么需要专用 AI 加速器

#### 概念

要理解 FastFlowLM 的价值，必须先搞清楚三种计算硬件在 AI 推理中的定位差异。**CPU**（中央处理器）是通用计算核心，擅长复杂逻辑控制和串行任务；**GPU**（图形处理器）拥有大量并行计算单元，擅长处理大规模矩阵运算；**NPU**（神经网络处理器，Neural Processing Unit）则是专门为神经网络推理设计的专用芯片（ASIC），针对矩阵乘法、激活函数等 AI 典型算子做了硬件级优化。三者并非互相替代，而是面向不同场景的互补关系。

#### 原理

这三种硬件的差异，根源在于**架构设计目标的不同**。CPU 的设计哲学是"低延迟、高通用性"，它有少量强大的核心（通常 8-16 个），每个核心配备大容量缓存和复杂的分支预测单元，能够快速处理任意类型的任务，但并行吞吐能力有限。GPU 的设计哲学是"高吞吐、大数据量"，它有成百上千个相对简单的核心，能够同时处理大量相同的计算任务，因此特别适合训练和推理大模型——但代价是高功耗和高成本。

NPU 则走了一条更极致的路线。它完全抛弃了通用性，**专门针对神经网络推理的计算模式进行硬件定制**。神经网络推理的核心操作是矩阵乘法（GEMM）和逐元素运算（如 ReLU 激活），NPU 在芯片层面直接固化了这些算子的高效执行路径，并配备了专用的片上存储（SRAM）来减少数据搬运。以 AMD XDNA2 NPU 为例，它拥有独立的 AI 引擎阵列，峰值算力可达数十 TOPS（每秒万亿次操作），而功耗却远低于 GPU。

| 维度 | CPU | GPU | NPU |
|------|-----|-----|-----|
| 设计目标 | 通用、低延迟 | 并行吞吐 | AI 推理专用 |
| 核心数量 | 少（8-16） | 多（数千） | 专用 AI 引擎阵列 |
| 能效比 | 低 | 中 | 高 |
| 典型功耗 | 15-65W | 75-450W | 5-30W |
| AI 推理速度 | 慢 | 快 | 快（针对优化模型） |
| 成本 | 已包含 | 昂贵 | 已包含（集成于 SoC） |

关键洞察在于：现代 AI PC（如搭载 Ryzen AI 的笔记本）**已经内置了 NPU**，但大多数推理软件仍然只使用 CPU 或 GPU，导致 NPU 资源闲置。FastFlowLM 的使命就是唤醒这块"沉睡的硅片"。

#### 例子

我们可以用一个生活化的类比来理解三者关系。假设你要把一万封信件按邮编分类：

```python
# CPU 的方式：一个能力很强的邮递员，一封一封地分
# 优点：每封信都处理得很仔细；缺点：总量大时太慢
def cpu_sort(letters):
    sorted_letters = []
    for letter in letters:  # 串行处理
        zip_code = parse(letter)
        sorted_letters.append((zip_code, letter))
    return sorted(sorted_letters)

# GPU 的方式：雇 1000 个临时工，每人分 10 封
# 优点：总量大时极快；缺点：雇人成本高、耗电大
def gpu_sort(letters):
    batches = split_into(letters, 1000)  # 并行分批
    results = parallel_map(sort_batch, batches)
    return merge(results)

# NPU 的方式：一台全自动信件分拣机，专为这个任务设计
# 优点：速度极快、功耗极低；缺点：只能分拣信件，不能干别的
def npu_sort(letters):
    return NPU_ACCELERATED_SORTER.run(letters)  # 硬件级加速
```

在实际的 LLM 推理场景中，FastFlowLM 调用 NPU 后，你可以在任务管理器中观察到 NPU 占用率显著上升，而 GPU 几乎保持空闲——这正是 NPU 优先架构的直观体现。

#### 总结

本讲的核心要点：CPU 通用但慢，GPU 强大但贵且耗电，NPU 是专为 AI 推理定制的高能效芯片。现代 AI PC 已内置 NPU，但传统推理软件未能利用它，造成资源浪费。FastFlowLM 的价值正是激活这块闲置算力，在低功耗下实现高效推理。常见注意事项：不要把 NPU 当作 GPU 的替代品来跑任意模型——它只对经过专门优化的模型才能发挥最大效能；同时，NPU 的算力上限受芯片型号限制，超大模型仍需 GPU 或云端。理解这一点，有助于你在后续选型时做出合理判断。

---

### 第3讲：FastFlowLM 核心特性与架构总览

#### 概念

FastFlowLM 的核心特性可以归纳为五个关键词：**NPU 优先、Ollama 式体验、OpenAI 兼容、多模态支持、超长上下文**。这五个特性共同构成了它的产品定位——一个让开发者能在 AMD Ryzen AI 设备上"零门槛"运行本地大模型的轻量级运行时。它不是要取代 vLLM、TGI 这类服务端推理引擎，而是瞄准 PC 端的本地化、隐私化推理场景。

#### 原理

FastFlowLM 的架构设计围绕"**轻量 + 兼容 + 高效**"三个目标展开。轻量方面，整个运行时打包后仅约 17 MB，这是因为 NPU 加速内核以预编译二进制形式分发，无需在用户机器上编译，同时编排层做了极致精简。兼容方面，它同时提供两套接口：CLI 模式模仿 Ollama 的命令体系（`flm run`、`flm pull`、`flm list` 等），让 Ollama 用户无缝迁移；Server 模式实现 OpenAI API 标准（`/v1/chat/completions`、`/v1/models`、`/v1/embeddings`、`/v1/audio/transcriptions`），让任何使用 OpenAI SDK 的应用都能直接对接。

高效方面，FastFlowLM 在 NPU 上实现了完整的 Transformer 推理流水线，包括 Prefill（预填充）和 Decode（解码）两个阶段。Prefill 阶段批量处理输入 prompt，通过 `--prefill-chunk-len` 参数控制分块大小以平衡延迟与吞吐；Decode 阶段逐 token 生成，利用 NPU 的片上 SRAM 缓存 KV Cache，支持最高 256k 的上下文窗口。此外，它支持多种 NPU 功耗模式（`powersaver`、`balanced`、`turbo`），让用户在性能与续航之间灵活权衡。

多模态支持是 FastFlowLM 的一大亮点。除了文本 LLM，它还支持视觉语言模型（VLM，如 Gemma3、Qwen2.5-VL）、语音识别模型（Whisper）、嵌入模型（EmbeddingGemma）和混合专家模型（MoE，如 gpt-oss）。这意味着你可以在同一运行时内构建多模态 AI 应用，无需切换工具链。

#### 例子

下面是 FastFlowLM 五大特性的具体体现：

```bash
# 特性1：NPU 优先 —— 一行命令启动，自动调用 NPU
flm run llama3.2:1b

# 特性2：Ollama 式体验 —— 命令风格完全一致
flm pull qwen3:4b          # 拉取模型
flm list                    # 列出模型
flm serve llama3.2:1b       # 启动服务

# 特性3：OpenAI 兼容 —— Python 代码几乎不用改
# （详见第14讲）
```

```python
# 特性4：多模态支持 —— 同一套 API 处理文本、图像、语音
from openai import OpenAI
client = OpenAI(base_url="http://127.0.0.1:52625/v1", api_key="flm")

# 文本对话
client.chat.completions.create(model="llama3.2:1b", messages=[...])

# 图像理解（需加载 VLM 模型）
client.chat.completions.create(model="gemma3:4b", messages=[
    {"role": "user", "content": [
        {"type": "text", "text": "描述这张图"},
        {"type": "image_url", "image_url": {"url": "file:///photo.jpg"}}
    ]}
])

# 语音转文字（需加载 Whisper）
client.audio.transcriptions.create(model="whisper-large-v3-turbo", file=audio_file)

# 文本嵌入（需加载 Embedding 模型）
client.embeddings.create(model="embeddinggemma", input="要嵌入的文本")
```

```
# 特性5：超长上下文 —— 最高支持 256k tokens
flm run llama3.2:1b --ctx-len 131072   # 128k 上下文
```

#### 总结

本讲核心要点：FastFlowLM 具备 NPU 优先、Ollama 式 CLI、OpenAI 兼容 API、多模态支持、256k 超长上下文五大核心特性。它的架构由 CLI 引擎、REST 服务、模型管理器和 NPU 优化内核四部分组成，整体仅 17 MB。常见注意事项：多模态能力依赖具体模型支持，并非所有模型都支持视觉或语音；256k 上下文是理论上限，实际可用长度受 NPU 显存（实际是 NPU 专用内存）限制，过大上下文会显著降低解码速度。理解这些特性后，你就能在后续章节中有针对性地学习所需功能。

---

### 第4讲：FastFlowLM 与 Ollama、llama.cpp 的对比

#### 概念

在本地 LLM 推理领域，**Ollama**、**llama.cpp** 和 **FastFlowLM** 是三个常被提及的方案。Ollama 是最受欢迎的本地 LLM 运行时，以极致易用著称；llama.cpp 是底层推理引擎，以跨平台和高性能著称，Ollama 底层就基于它；FastFlowLM 则是 NPU 专用的后起之秀。理解三者的差异，能帮助你在不同场景下做出正确选型。

#### 原理

三者的本质区别在于**优化目标与硬件适配策略**。llama.cpp 是一个 C++ 实现的推理引擎，它的核心价值是跨平台——通过 GGUF 量化格式和 CPU/GPU 后端抽象，它几乎能在任何设备上运行任何模型。但这种通用性也意味着它无法针对特定硬件做极致优化，在 NPU 上几乎没有原生支持。

Ollama 在 llama.cpp 之上封装了一层友好的 CLI 和 REST API，把"下载模型→加载→对话"的流程简化到极致。它的优势是生态成熟、模型丰富、社区活跃，但底层仍然依赖 CPU/GPU，在搭载 NPU 的设备上无法利用 NPU 算力。

FastFlowLM 走了一条不同的路：**放弃通用性，换取 NPU 上的极致能效**。它不使用 GGUF 格式，而是为每个模型提供专门针对 XDNA2 NPU 编译的优化内核；它不支持任意硬件，但支持的硬件上能效比远超另外两者。这种"专用化"策略类似于苹果的 Neural Engine 优化——牺牲灵活性，换取在特定硬件上的最优表现。

| 对比维度 | FastFlowLM | Ollama | llama.cpp |
|----------|------------|--------|-----------|
| 目标硬件 | AMD XDNA2 NPU 专用 | CPU/GPU 通用 | CPU/GPU 通用 |
| 模型格式 | NPU 优化内核 | GGUF | GGUF |
| 安装体积 | ~17 MB | ~150 MB | ~5 MB（核心） |
| 能效比 | 极高（NPU） | 中（GPU）/低（CPU） | 中（GPU）/低（CPU） |
| 模型丰富度 | 中（主流模型已支持） | 高（几乎所有开源模型） | 极高（需手动转换） |
| OpenAI API 兼容 | 是 | 是 | 否（需自行封装） |
| 多模态支持 | 是（VLM/ASR/Embedding） | 是 | 需自行实现 |
| 学习曲线 | 低 | 极低 | 中高 |
| 适用场景 | Ryzen AI 设备本地推理 | 通用本地推理 | 深度定制/研究 |

#### 例子

一个典型的选型决策场景：你有一台搭载 Ryzen AI 9 365 处理器的笔记本，想在本地运行 Qwen3 模型做代码助手。

```bash
# 方案A：用 Ollama（GPU 推理，功耗高、发热大）
ollama run qwen3:4b
# 结果：GPU 占用 80%，风扇狂转，电池续航 1.5 小时

# 方案B：用 FastFlowLM（NPU 推理，功耗低、安静）
flm run qwen3:4b
# 结果：NPU 占用 60%，GPU 空闲，风扇安静，电池续航 4 小时
```

```python
# 方案C：用 llama.cpp（需要手动编译、配置后端）
# ./main -m qwen3-4b.gguf -p "你好"
# 结果：默认走 CPU，速度慢；要上 NPU 需大量适配工作
```

从这个例子可以看出，如果你的设备恰好是 Ryzen AI 系列，FastFlowLM 几乎是无脑选择；如果是其他硬件，则应选 Ollama 或 llama.cpp。

#### 总结

本讲核心要点：llama.cpp 是通用底层引擎，Ollama 是基于它的易用封装，FastFlowLM 是 NPU 专用方案。三者并非竞争关系，而是面向不同硬件和场景的互补。选型建议：Ryzen AI 设备选 FastFlowLM（能效最优），通用设备选 Ollama（易用性最优），深度研究选 llama.cpp（灵活性最优）。常见注意事项：FastFlowLM 的模型生态不如 Ollama 丰富，少数小众模型可能尚未支持；但主流模型（LLaMA、Qwen、Gemma、Phi 等）均已覆盖。如果你依赖某个 FastFlowLM 暂不支持的模型，可以关注其更新节奏或向社区反馈。

---

## 第2章 环境准备与安装

本章是实战的起点。FastFlowLM 虽然以"20 秒安装"著称，但前提是你的硬件和驱动满足要求。我们将依次讲解硬件兼容性检查、NPU 驱动验证，以及 Windows 和 Linux 两大平台的详细安装流程。完成本章后，你将拥有一台可以运行 FastFlowLM 的开发环境，为后续所有章节的实操打下基础。请务必按顺序完成每一步，因为驱动问题是新手最常遇到的障碍。

### 第5讲：硬件要求与 NPU 驱动检查

#### 概念

FastFlowLM 的运行依赖两个硬性条件：**支持的 NPU 硬件**和**正确安装的 NPU 驱动**。硬件方面，它仅支持搭载 AMD XDNA2 架构 NPU 的 Ryzen AI 系列芯片，包括 Strix（Ryzen AI 300 系列，如 Ryzen AI 9 365、9 HX 370）、Strix Halo（Ryzen AI Max 300 系列）、Kraken（Ryzen AI 7 350）和 Gorgon Point 等型号。驱动方面，要求 NPU 驱动版本不低于 32.0.203.304，过低版本会导致内核加载失败。

#### 原理

为什么 FastFlowLM 对硬件和驱动如此严格？这源于它的**专用化设计**。与通用推理框架不同，FastFlowLM 的加速内核是针对 XDNA2 NPU 的特定指令集和内存架构预编译的。XDNA2 架构包含一组可配置的 AI 引擎（AIE-ML），每个引擎支持 INT8/BF16/FP16 等多种数据格式，并配备专用的片上 SRAM。FastFlowLM 的内核直接调用这些引擎的底层接口，因此必须在硬件和驱动都匹配的环境下才能运行。

驱动版本要求则是因为 NPU 驱动（基于 AMD XRT 运行时）会定期修复 Bug 并增加新特性。32.0.203.304 是一个关键版本，它修复了早期版本中 KV Cache 内存管理的若干问题，并增加了对长上下文（128k+）的稳定支持。低于此版本的驱动可能在加载大模型或处理长文本时崩溃。

值得注意的是，FastFlowLM **不需要安装 CUDA、ROCm 或任何 GPU 相关工具链**，这是它相比 GPU 方案的一大优势——环境干净、依赖少、冲突风险低。

#### 例子

**第一步：确认你的 CPU 型号是否支持。** 在 Windows 上，按 `Ctrl + Shift + Esc` 打开任务管理器 → 性能 → CPU，查看型号名称；或运行以下 PowerShell 命令：

```powershell
# 查看 CPU 信息
Get-CimInstance Win32_Processor | Select-Object Name

# 输出示例（支持）：
# Name
# ----
# AMD Ryzen AI 9 365 w/ Radeon 880M

# 输出示例（不支持）：
# Name
# ----
# AMD Ryzen 7 5800X  ← 这是 Zen3 架构，无 NPU
```

在 Linux 上：

```bash
lscpu | grep "Model name"
# 支持的输出示例：AMD Ryzen AI 9 365 w/ Radeon 880M
```

**第二步：检查 NPU 驱动版本。** Windows 上，打开设备管理器 → 系统设备 → 查找 "AMD NPU" 或 "AMD IPU" 设备 → 右键属性 → 驱动程序 → 驱动程序版本：

```powershell
# 也可用 PowerShell 查询
Get-CimInstance Win32_PnPSignedDriver | Where-Object {$_.DeviceName -like "*NPU*" -or $_.DeviceName -like "*IPU*"} | Select-Object DeviceName, DriverVersion
```

```bash
# Linux 上检查 NPU 设备节点
ls -l /dev/accel*    # 存在 /dev/accel0 等节点表示驱动已加载
# 或
lsmod | grep amd      # 查看内核模块
```

**第三步：确认 NPU 算力可用。** 安装 FastFlowLM 后，可以用 `flm check` 命令快速验证：

```bash
flm check
# 成功输出示例：
# ✅ NPU detected: AMD Ryzen AI NPU (XDNA2)
# ✅ Driver version: 32.0.203.304 (meets requirement)
# ✅ NPU is ready for inference
```

#### 总结

本讲核心要点：FastFlowLM 仅支持 AMD XDNA2 NPU（Strix/Strix Halo/Kraken/Gorgon Point 系列），驱动版本需 ≥ 32.0.203.304。安装前务必通过任务管理器或命令行确认硬件型号，通过设备管理器或 `flm check` 确认驱动版本。常见注意事项：如果你的设备是 Intel Core Ultra（带 Intel NPU）或 Apple Silicon（带 Neural Engine），FastFlowLM 无法使用，请改用 Ollama 或相应厂商的方案；驱动版本过低时，请到 AMD 官网下载最新 NPU 驱动包更新，而非依赖 Windows 自动更新（它往往推送旧版驱动）。

---

### 第6讲：Windows 安装详解

#### 概念

Windows 是 FastFlowLM 的主要目标平台，因为大多数搭载 Ryzen AI NPU 的笔记本预装的就是 Windows 11。FastFlowLM 在 Windows 上的安装流程设计得极为简洁：下载安装包 → 运行安装程序 → 验证安装。整个过程不需要命令行编译、不需要配置环境变量，安装程序会自动完成路径注册和快捷方式创建。安装完成后，`flm` 命令即可在 PowerShell 和 CMD 中直接使用。

#### 原理

FastFlowLM 的 Windows 安装包是一个自包含的可执行文件（.exe），它将运行时核心、NPU 加速内核、CLI 工具和必要的依赖库全部打包在一起。安装程序的工作流程包括：解压文件到目标目录（默认 `C:\Users\<用户名>\Documents\flm`）→ 将该目录添加到系统 PATH 环境变量 → 创建模型存储目录（`<安装目录>\models`）→ 注册卸载信息到注册表。

模型存储路径的设计值得注意。默认情况下，模型文件保存在用户文档目录下的 `flm\models` 子文件夹中，这样设计有两个好处：一是避免权限问题（不需要管理员权限写入 Program Files）；二是便于用户手动管理模型文件。安装时你可以选择不同的安装位置，模型路径会随之自动调整。如果后续需要自定义模型存储位置，可以通过环境变量 `FLM_MODEL_PATH` 覆盖默认路径。

另一个关键设计是**版本检查机制**。FastFlowLM 启动时会默认检查是否有新版本，这对保持与最新模型内核的兼容性很重要。但在离线或受限网络环境下，这个检查可能拖慢启动速度，此时可以设置环境变量 `FLM_DISABLE_UPDATE_CHECK=1` 来禁用。

#### 例子

**完整安装步骤：**

```powershell
# 步骤1：下载安装包
# 访问 https://github.com/FastFlowLM/FastFlowLM/releases
# 下载最新的 FastFlowLM-Setup-x.x.x.exe（约 17 MB）

# 步骤2：运行安装程序（双击或命令行启动）
# 安装向导会引导你选择安装路径，建议保持默认
# 勾选 "Add to PATH" 选项（默认已勾选）

# 步骤3：验证安装（新开一个 PowerShell 窗口）
flm --version
# 输出示例：FastFlowLM v0.9.30

flm help
# 输出所有可用命令
```

```powershell
# 步骤4：拉取第一个模型并测试
flm pull llama3.2:1b
# 下载进度示例：
# pulling manifest... done
# pulling kernel... 100% ▕████████████▏ 1.2 GB
# verifying... done
# success

flm run llama3.2:1b
# 进入交互式对话界面
>>> 你好，请用中文自我介绍
你好！我是基于 LLaMA 3.2 的本地模型，运行在你的 AMD NPU 上...
```

**自定义模型存储路径（可选）：**

```powershell
# 方法1：安装时选择不同目录
# 例如选择 C:\AI\flm，则模型存储在 C:\AI\flm\models

# 方法2：通过环境变量覆盖（适用于已安装情况）
# 在系统环境变量中添加：
# 变量名: FLM_MODEL_PATH
# 变量值: D:\MyModels\flm

# 方法3：PowerShell 临时设置（仅当前会话有效）
$env:FLM_MODEL_PATH = "D:\MyModels\flm"
flm pull qwen3:4b   # 模型将下载到 D:\MyModels\flm
```

**禁用启动版本检查（可选）：**

```powershell
# 适用于离线环境或启动较慢的情况
$env:FLM_DISABLE_UPDATE_CHECK = "1"
# 或在系统环境变量中永久设置
```

#### 总结

本讲核心要点：Windows 安装只需下载官方安装包并运行，全程图形化向导，约 20 秒完成。默认安装路径为用户文档目录下的 `flm` 文件夹，模型存储在 `flm\models` 子目录。可通过 `FLM_MODEL_PATH` 环境变量自定义模型路径，通过 `FLM_DISABLE_UPDATE_CHECK=1` 禁用版本检查。常见注意事项：安装后必须**新开** PowerShell 窗口才能使用 `flm` 命令（因为环境变量需要重新加载）；如果 `flm` 命令未识别，检查 PATH 是否包含安装目录；首次拉取模型需要联网访问 HuggingFace，若网络受限请参考第7讲离线方案或手动下载模型文件放入 `models` 目录。

---

### 第7讲：Linux 安装详解

#### 概念

FastFlowLM 在 Linux 上的安装与 Windows 类似，但需要更多手动配置。它通过一个安装脚本完成核心文件的部署和 PATH 配置，支持主流发行版（Ubuntu 22.04+、Fedora 39+、Debian 12+ 等）。Linux 版本同样以预编译二进制形式分发，无需从源码编译，但 NPU 驱动需要单独安装（AMD 提供独立的 `amd-npu` 驱动包）。安装完成后，`flm` 命令在任意终端可用。

#### 原理

Linux 安装的设计遵循 Unix 传统：**脚本化、可定制、透明**。安装脚本（通常是 `install.sh`）的工作流程是：检测系统架构和发行版 → 下载对应的预编译包 → 解压到 `/opt/flm`（系统级）或 `~/.local/share/flm`（用户级）→ 在 `~/.local/bin` 或 `/usr/local/bin` 创建 `flm` 符号链接 → 创建模型目录 `~/.config/flm/models`。

Linux 上 NPU 驱动的安装是关键一步。AMD 为 XDNA2 NPU 提供了开源的 `amd-npu` 内核模块和用户态库（基于 XRT 运行时）。这些驱动通常不包含在发行版默认内核中，需要从 AMD 官方仓库安装。驱动安装后，系统会出现 `/dev/accel0` 等设备节点，用户组通常需要加入 `render` 或 `video` 组才能访问。

与 Windows 不同，Linux 版本的模型默认存储在 `~/.config/flm/models`，遵循 XDG 目录规范。同样支持 `FLM_MODEL_PATH` 环境变量覆盖。Linux 用户通常更习惯命令行操作，因此 FastFlowLM 在 Linux 上的 CLI 体验与 Windows 完全一致，但 Server 模式在 Linux 上更适合作为 systemd 服务长期运行（详见第12讲）。

#### 例子

**完整安装步骤（以 Ubuntu 为例）：**

```bash
# 步骤1：安装 NPU 驱动（关键前置步骤）
# 添加 AMD 官方仓库
wget -qO - https://repo.radeon.com/rocm/rocm.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/rocm.gpg
echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/rocm.gpg] https://repo.radeon.com/amd-npu/ubuntu jammy main" | sudo tee /etc/apt/sources.list.d/amd-npu.list

sudo apt update
sudo apt install -y amd-npu-core amd-npu-firmware

# 验证驱动
ls -l /dev/accel*
# crw-rw---- 1 root render 226, 0 ... /dev/accel0

# 将当前用户加入 render 组（重启后生效）
sudo usermod -aG render $USER
```

```bash
# 步骤2：下载并运行 FastFlowLM 安装脚本
curl -fsSL https://fastflowlm.com/install.sh | bash

# 或手动下载安装包
wget https://github.com/FastFlowLM/FastFlowLM/releases/download/v0.9.30/flm-linux-x86_64.tar.gz
tar -xzf flm-linux-x86_64.tar.gz
cd flm-linux-x86_64
./install.sh    # 安装到 ~/.local/share/flm，创建符号链接到 ~/.local/bin/flm
```

```bash
# 步骤3：验证安装（新开终端或 source 配置）
source ~/.bashrc    # 或 source ~/.zshrc
flm --version
# FastFlowLM v0.9.30

flm check
# ✅ NPU detected: AMD Ryzen AI NPU (XDNA2)
# ✅ Driver version: 32.0.203.304
# ✅ NPU is ready
```

```bash
# 步骤4：拉取模型并测试
flm pull llama3.2:1b
flm run llama3.2:1b
>>> Hello!
Hello! How can I help you today?
```

**自定义配置：**

```bash
# 自定义模型存储路径
echo 'export FLM_MODEL_PATH="/data/flm/models"' >> ~/.bashrc
source ~/.bashrc
mkdir -p /data/flm/models

# 禁用版本检查（离线环境）
echo 'export FLM_DISABLE_UPDATE_CHECK=1' >> ~/.bashrc
```

**离线安装方案（网络受限环境）：**

```bash
# 在有网络的机器上下载模型文件
flm pull llama3.2:1b
# 模型文件位于 ~/.config/flm/models/llama3.2-1b/

# 打包传输到目标机器
tar -czf flm-models.tar.gz -C ~/.config/flm/models .

# 在目标机器解压到模型目录
mkdir -p ~/.config/flm/models
tar -xzf flm-models.tar.gz -C ~/.config/flm/models
flm list    # 应能看到已导入的模型
```

#### 总结

本讲核心要点：Linux 安装分两步——先装 NPU 驱动（`amd-npu-core` 包），再运行 FastFlowLM 安装脚本。默认安装到 `~/.local/share/flm`，模型存储在 `~/.config/flm/models`，可通过 `FLM_MODEL_PATH` 自定义。安装后需将用户加入 `render` 组并重新登录才能访问 NPU 设备。常见注意事项：驱动安装后必须**重启或重新登录**才能生效；不同发行版的包名可能略有差异（如 Fedora 用 `dnf` 安装）；如果 `/dev/accel0` 不存在，说明驱动未正确加载，检查内核日志 `dmesg | grep -i npu` 排查；离线环境下可从其他机器拷贝模型文件，但运行时二进制仍需匹配架构。

---

## 第3章 CLI 模式——终端交互

CLI（命令行界面）是 FastFlowLM 最直接的使用方式，也是开发者最常接触的入口。本章将系统讲解 `flm` 命令的完整体系，包括命令总览、模型管理、交互式对话内置命令，以及运行参数调优。掌握 CLI 模式后，你就能在不写任何代码的情况下完成模型推理、对话调试和性能测试。本章是后续 Server 模式和 API 开发的基础，因为很多概念（如模型标签、上下文长度、功耗模式）在两种模式下是通用的。

### 第8讲：flm 命令体系总览

#### 概念

`flm` 是 FastFlowLM 的统一命令行入口，它采用**子命令式**设计（类似 `git`、`docker`），即主命令 `flm` 后跟一个子命令来指定具体操作。FastFlowLM 的子命令可以分为三类：**模型管理类**（`pull`、`list`、`remove`、`check`、`validate`）、**推理执行类**（`run`、`serve`）、**辅助类**（`help`、`port`、`--version`）。每个子命令有自己的参数和选项，但整体风格与 Ollama 高度一致，降低了迁移成本。

#### 原理

子命令式架构的优势在于**可扩展性和可发现性**。FastFlowLM 的 CLI 内部维护一个命令注册表，每个子命令对应一个处理函数。当用户输入 `flm <subcommand>` 时，CLI 解析器会查找注册表，匹配到对应函数后传入剩余参数执行。这种设计让新增命令变得简单——只需注册新的子命令，而不影响现有功能。

从执行流程看，`flm` 命令的工作路径是：解析命令行参数 → 加载配置（环境变量、默认值）→ 初始化 NPU 运行时 → 执行子命令逻辑 → 输出结果 → 清理资源。其中 NPU 运行时初始化是相对耗时的步骤（约 1-2 秒），因此频繁调用 `flm` 进行短任务时会有可感知的启动开销。这也是为什么 `flm run` 和 `flm serve` 设计为长驻进程——一次初始化，持续服务。

值得理解的是 `flm run` 和 `flm serve` 的本质区别。`flm run` 启动的是一个**交互式会话**，模型加载后直接进入 REPL（读取-求值-输出循环），用户输入即时得到响应，会话结束即卸载模型。而 `flm serve` 启动的是一个**HTTP 服务器**，模型加载后常驻内存，通过 REST API 对外提供服务，适合被其他程序调用。两者共享底层的模型加载和推理逻辑，只是上层接口不同。

#### 例子

**查看所有命令：**

```bash
flm help
# 输出示例：
# FastFlowLM (FLM) - NPU-first LLM inference runtime
#
# Usage: flm [command] [flags]
#
# Available Commands:
#   run         Run a model in interactive CLI mode
#   serve       Start the FastFlowLM server (OpenAI-compatible API)
#   pull        Pull a model from the registry
#   list        List all locally available models
#   remove      Remove a model from local storage
#   check       Check NPU hardware and driver status
#   validate    Validate a model's integrity
#   port        Show or set the default server port
#   help        Help about any command
#   version     Show FastFlowLM version
#
# Flags:
#   -h, --help      help for flm
#   -v, --version   version for flm
#
# Use "flm [command] --help" for more information about a command.
```

**查看单个命令的详细用法：**

```bash
flm run --help
# 输出示例：
# Run a model in interactive CLI mode
#
# Usage: flm run [model] [flags]
#
# Examples:
#   flm run llama3.2:1b
#   flm run qwen3:4b --ctx-len 8192
#   flm run gemma3:4b --pmode turbo
#
# Flags:
#       --ctx-len int           Maximum context length in tokens (default 4096)
#       --pmode string          NPU power mode: powersaver|balanced|turbo (default "balanced")
#       --prefill-chunk-len int Prefill chunk size in tokens (default 2048)
#       --preemption            Enable preemption for long context (default true)
#   -h, --help                  help for run
```

**命令分类速查表：**

| 类别 | 命令 | 作用 | 示例 |
|------|------|------|------|
| 模型管理 | `flm pull` | 下载模型 | `flm pull llama3.2:1b` |
| 模型管理 | `flm list` | 列出本地模型 | `flm list` |
| 模型管理 | `flm remove` | 删除模型 | `flm remove llama3.2:1b` |
| 模型管理 | `flm check` | 检查 NPU 状态 | `flm check` |
| 模型管理 | `flm validate` | 验证模型完整性 | `flm validate llama3.2:1b` |
| 推理执行 | `flm run` | 交互式对话 | `flm run llama3.2:1b` |
| 推理执行 | `flm serve` | 启动 API 服务 | `flm serve llama3.2:1b` |
| 辅助 | `flm port` | 查看/设置端口 | `flm port 8080` |
| 辅助 | `flm version` | 查看版本 | `flm version` |

#### 总结

本讲核心要点：`flm` 采用子命令式架构，命令分为模型管理、推理执行、辅助三类。`flm run` 用于交互式对话，`flm serve` 用于启动 API 服务，两者共享底层推理逻辑但接口不同。使用 `flm help` 和 `flm <command> --help` 可以随时查看用法。常见注意事项：每次调用 `flm` 都有 1-2 秒的 NPU 初始化开销，批量操作时建议用 `flm serve` 常驻服务而非反复调用 `flm run`；命令和参数区分大小写；模型名称中的冒号（如 `llama3.2:1b`）是标签分隔符，不能省略。

---

### 第9讲：模型管理——pull / list / remove / check

#### 概念

模型管理是 FastFlowLM 日常使用中最频繁的操作。`flm pull` 从官方仓库（HuggingFace 镜像）下载模型到本地；`flm list` 列出所有已下载的模型及其基本信息；`flm remove` 删除不再需要的模型以释放磁盘空间；`flm check` 验证 NPU 硬件和驱动状态，是排查问题的首选命令。这四个命令构成了模型生命周期的完整管理闭环。

#### 原理

FastFlowLM 的模型管理基于一个**本地清单（manifest）机制**。每个模型在本地存储为一个目录，包含模型内核文件、配置文件（manifest.json）和元数据。`flm pull` 的工作流程是：根据模型名查询远程仓库获取清单 → 下载内核文件 → 校验文件哈希 → 写入本地清单 → 注册到全局索引。这个全局索引记录了所有本地模型的名称、版本、大小和路径，`flm list` 就是读取这个索引来展示信息。

`flm remove` 的逻辑相对简单：从索引中查找模型 → 删除对应的文件目录 → 更新索引。但需要注意，如果模型正在被 `flm run` 或 `flm serve` 使用，删除会失败并提示先停止相关进程。`flm check` 则是一个独立的诊断命令，它不依赖任何模型，而是直接查询 NPU 硬件信息（通过 XRT 驱动接口）和驱动版本，并执行一个简单的推理测试来验证 NPU 是否真正可用。

模型下载的存储格式与 Ollama 的 GGUF 不同。FastFlowLM 为每个模型存储的是**针对 XDNA2 NPU 预编译的内核文件**（通常是 `.bin` 或 `.so` 格式），而非通用的权重文件。这意味着同一个模型（如 LLaMA 3.2 1B）在 FastFlowLM 和 Ollama 中的文件不能互换——FastFlowLM 的模型文件只能在 XDNA2 NPU 上运行，Ollama 的 GGUF 文件只能在 CPU/GPU 上运行。这种专用化是 FastFlowLM 高能效的代价。

#### 例子

**拉取模型：**

```bash
# 拉取默认版本
flm pull llama3.2:1b
# pulling manifest... done
# pulling kernel (1.2 GB)... 100% ▕████████████████▏
# verifying checksum... done
# success

# 拉取指定版本（标签）
flm pull qwen3:4b
flm pull gemma3:4b

# 查看拉取进度（已下载的模型会跳过）
flm pull llama3.2:1b
# model 'llama3.2:1b' already exists, skipping
```

**列出本地模型：**

```bash
flm list
# 输出示例：
# NAME            SIZE      MODIFIED       CONTEXT    TOOLS
# llama3.2:1b     1.2 GB    2 days ago     128k       no
# qwen3:4b        4.1 GB    1 day ago      128k       yes
# gemma3:4b       3.8 GB    3 hours ago    128k       yes (vision)
# whisper-large   1.5 GB    1 week ago     -          -
```

**删除模型：**

```bash
# 删除单个模型
flm remove llama3.2:1b
# Are you sure you want to remove 'llama3.2:1b'? (y/N): y
# removing model files... done
# success

# 强制删除（不确认）
flm remove llama3.2:1b --force

# 删除时若模型正在使用，会报错
flm remove qwen3:4b
# Error: model 'qwen3:4b' is currently in use. Stop all running instances first.
```

**检查 NPU 状态：**

```bash
flm check
# 输出示例（正常）：
# ✅ NPU detected: AMD Ryzen AI NPU (XDNA2)
# ✅ Driver version: 32.0.203.304 (meets requirement: ≥ 32.0.203.304)
# ✅ NPU compute units: 32
# ✅ Peak INT8 TOPS: 50
# ✅ Available memory: 12.4 GB / 16 GB
# ✅ Quick inference test: PASSED (12.3 ms)
# 
# NPU is ready for inference.

# 输出示例（异常）：
# ❌ NPU not detected
# Please install AMD NPU driver (version ≥ 32.0.203.304)
# Download from: https://www.amd.com/en/support
```

**验证模型完整性：**

```bash
flm validate llama3.2:1b
# Validating model 'llama3.2:1b'...
# ✅ Manifest: valid
# ✅ Kernel file: checksum matched
# ✅ Config: valid
# Model is intact and ready to use.
```

#### 总结

本讲核心要点：`flm pull` 下载模型（自动跳过已存在的），`flm list` 展示本地模型清单，`flm remove` 删除模型（使用中的无法删除），`flm check` 诊断 NPU 状态，`flm validate` 校验模型文件完整性。FastFlowLM 的模型文件是 NPU 专用格式，不能与 Ollama 的 GGUF 互换。常见注意事项：模型文件较大（1B 模型约 1.2 GB，4B 模型约 4 GB），下载前确保磁盘空间充足；`flm check` 是排查"模型加载失败"问题的第一步，先确认 NPU 可用再排查模型；删除模型后磁盘空间不会立即释放，因为 NPU 可能仍占用部分内存，重启后完全释放。

---

### 第10讲：交互式对话——CLI 模式内置命令

#### 概念

`flm run <model>` 启动后进入的是一个**交互式 REPL（读取-求值-输出循环）**界面，类似 Ollama 的对话体验。在这个界面中，除了直接输入文本与模型对话外，还可以使用以 `/` 开头的**内置命令**来控制会话行为。这些命令包括查看模型信息（`/show`）、保存/加载对话（`/save`、`/load`）、清空上下文（`/clear`）、查看状态（`/status`）、切换详细模式（`/verbose`）和退出（`/bye`）等。掌握这些命令能显著提升调试和使用效率。

#### 原理

交互式 REPL 的核心是一个**事件循环**：读取用户输入 → 判断是否为命令（以 `/` 开头）→ 若是命令则执行对应操作，若是普通文本则送入推理引擎 → 输出结果 → 回到读取状态。这个循环的关键在于**上下文管理**。每次用户输入和模型回复都会被追加到对话历史中，作为下一次推理的输入上下文。随着对话进行，上下文会不断增长，直到达到 `--ctx-len` 设置的上限，此时早期的消息会被截断（滑动窗口机制）。

内置命令的设计目的是解决 REPL 使用中的常见痛点。例如，`/clear` 用于清空当前对话历史——这在切换话题时很有用，因为残留的旧上下文可能干扰模型对新话题的理解。`/save` 和 `/load` 则支持对话的持久化，让你可以中断后继续，或备份有价值的对话。`/status` 实时显示 NPU 占用、内存使用、当前上下文长度等信息，是性能监控的利器。`/verbose` 切换详细输出模式，会显示每个 token 的生成耗时、Prefill/Decode 阶段统计等底层信息，对性能调优至关重要。

一个容易被忽视的细节是**多行输入**。在交互式界面中，默认按回车即提交输入。如果需要输入多行文本（如粘贴一段代码），可以使用 `\` 续行符，或先输入 `"""` 进入多行模式，再次输入 `"""` 结束。这个功能在处理长 prompt 时非常实用。

#### 例子

**基本对话流程：**

```bash
flm run llama3.2:1b
# Loading model 'llama3.2:1b' onto NPU...
# Model loaded. Context length: 4096. Type /help for commands.

>>> 你好，请介绍一下你自己
你好！我是一个基于 LLaMA 3.2 架构的语言模型，运行在你的 AMD Ryzen AI NPU 上。
我可以回答问题、协助写作、编写代码等。请问有什么可以帮你的？

>>> /help
# Available commands:
#   /help        Show this help message
#   /show        Show model information
#   /status      Show NPU and session status
#   /clear       Clear conversation history
#   /save <name> Save current conversation
#   /load <name> Load a saved conversation
#   /history     Show conversation history
#   /verbose     Toggle verbose output mode
#   /bye         Exit the session
```

**查看模型信息：**

```bash
>>> /show
# Model: llama3.2:1b
# Architecture: LLaMA 3.2
# Parameters: 1.24B
# Context length: 4096 (configurable up to 128k)
# Quantization: INT8 (NPU-optimized)
# Tools support: no
# Vision support: no
# License: LLaMA 3.2 Community License
```

**查看会话状态：**

```bash
>>> /status
# Session status:
#   NPU utilization: 45%
#   NPU memory: 1.8 GB / 12.4 GB
#   Context used: 1,247 / 4,096 tokens
#   Messages in history: 4
#   Avg decode speed: 38.5 tokens/s
#   Power mode: balanced
```

**保存和加载对话：**

```bash
>>> /save debug_session
# Conversation saved as 'debug_session'

# 退出后重新进入
flm run llama3.2:1b
>>> /load debug_session
# Conversation 'debug_session' loaded (4 messages)

>>> 我们刚才聊到哪里了？
你刚才在询问关于 NPU 推理性能的问题，我正在解释 Prefill 和 Decode 阶段的区别...
```

**清空上下文（切换话题时使用）：**

```bash
>>> /clear
# Conversation history cleared. Context is now empty.

>>> 现在聊一个新话题：请解释什么是 RAG
RAG（Retrieval-Augmented Generation，检索增强生成）是一种结合了信息检索和文本生成的技术...
```

**详细模式（性能调优用）：**

```bash
>>> /verbose
# Verbose mode: ON

>>> 写一首关于春天的诗
# [Prefill] 18 tokens in 45ms (400 tok/s)
# [Decode] token 1: "春" (12ms)
# [Decode] token 2: "风" (11ms)
# [Decode] token 3: "拂" (13ms)
# ...
# [Decode] total: 48 tokens in 1240ms (38.7 tok/s)
# [NPU] peak utilization: 62%
春风拂面花满枝，
细雨润物草生姿。
燕归旧垒寻故友，
人迎新岁赋新诗。
```

**多行输入：**

```bash
>>> """
... def fibonacci(n):
...     if n < 2:
...         return n
...     return fibonacci(n-1) + fibonacci(n-2)
... """
# （输入 """ 结束多行模式，提交给模型）
请解释这段代码的时间复杂度，并给出优化建议。
```

#### 总结

本讲核心要点：`flm run` 进入交互式 REPL，支持以 `/` 开头的内置命令。常用命令包括 `/help`（帮助）、`/show`（模型信息）、`/status`（状态监控）、`/clear`（清空上下文）、`/save`/`/load`（对话持久化）、`/verbose`（详细输出）、`/bye`（退出）。多行输入可用 `"""` 包裹。常见注意事项：对话历史会持续占用上下文，长对话接近上限时及时 `/clear`；`/verbose` 显示的 token 速度是性能调优的关键指标；保存的对话仅绑定当前模型，换模型后无法 `/load`；退出 REPL 用 `/bye` 或 `Ctrl+D`，不要直接关闭终端（可能导致 NPU 资源未释放）。

---

### 第11讲：运行参数调优——ctx-len / pmode / prefill-chunk

#### 概念

`flm run` 和 `flm serve` 都支持一系列运行参数来精细控制推理行为。其中最重要的三个是：`--ctx-len`（上下文长度，决定能处理多长的输入和对话历史）、`--pmode`（NPU 功耗模式，在性能与能耗间权衡）、`--prefill-chunk-len`（预填充分块大小，影响首 token 延迟）。合理配置这些参数，能在不同场景下获得最佳的性能或能效表现。

#### 原理

**`--ctx-len`（上下文长度）** 决定了模型能"记住"多少 token。Transformer 架构的注意力机制复杂度是 O(n²)，因此上下文越长，Prefill 阶段的计算量和内存占用越大。FastFlowLM 支持最高 256k 的上下文，但实际设置需要权衡：过短会导致长文档处理或长对话被截断，过长会显著增加内存占用和 Prefill 时间。NPU 的专用内存（通常 12-16 GB）是硬上限，超过会导致加载失败。默认值 4096 适合大多数对话场景，处理长文档时建议设为 8192-32768。

**`--pmode`（功耗模式）** 控制 NPU 的工作频率和电压。`powersaver` 模式降低 NPU 频率，解码速度较慢但功耗最低（适合电池供电的移动场景）；`balanced` 是默认模式，在速度和功耗间折中；`turbo` 模式拉高 NPU 频率，解码速度最快但功耗最高（适合插电高性能场景）。三者的速度差异通常在 1.5-2 倍之间，功耗差异在 2-3 倍之间。选择哪个模式取决于你的使用场景：移动办公选 powersaver，桌面开发选 balanced，批量处理选 turbo。

**`--prefill-chunk-len`（预填充分块大小）** 是一个较高级的参数。Prefill 阶段需要一次性处理整个输入 prompt，当 prompt 很长时（如几万 token），一次性计算会导致内存峰值过高和首 token 延迟过长。分块机制将长 prompt 切分为多个 chunk 依次处理，降低峰值内存但略微增加总时间。默认值 2048 适合大多数场景；处理超长文档（>32k token）时可适当增大到 4096 以减少分块次数，前提是 NPU 内存充足。

此外还有 `--preemption` 参数（默认开启），它允许在上下文接近上限时自动淘汰最早的对话轮次，避免因上下文溢出而报错。关闭它则会在上下文满时直接报错，适合需要保留完整历史的场景。

#### 例子

**不同上下文长度的场景：**

```bash
# 场景1：日常短对话（默认即可）
flm run llama3.2:1b
# 等同于 --ctx-len 4096

# 场景2：代码助手，需要较长上下文
flm run qwen3:4b --ctx-len 16384

# 场景3：长文档总结（如整本书）
flm run llama3.2:1b --ctx-len 131072
# 注意：1B 模型在 128k 上下文下 Prefill 较慢，建议用 4B+ 模型

# 场景4：极限长上下文（需大内存 NPU）
flm run qwen3:4b --ctx-len 262144
```

**不同功耗模式的对比测试：**

```bash
# 测试 powersaver 模式
flm run llama3.2:1b --pmode powersaver
>>> /verbose
# （输入相同 prompt 后观察）
# [Decode] total: 50 tokens in 2100ms (23.8 tok/s)
# [NPU] power: 8W

# 测试 balanced 模式（默认）
flm run llama3.2:1b --pmode balanced
# [Decode] total: 50 tokens in 1300ms (38.5 tok/s)
# [NPU] power: 15W

# 测试 turbo 模式
flm run llama3.2:1b --pmode turbo
# [Decode] total: 50 tokens in 950ms (52.6 tok/s)
# [NPU] power: 28W
```

**预填充分块调优：**

```bash
# 默认分块（适合短 prompt）
flm run llama3.2:1b --prefill-chunk-len 2048

# 处理长文档时增大分块（减少分块次数）
flm run llama3.2:1b --ctx-len 65536 --prefill-chunk-len 4096

# 内存紧张时减小分块（降低峰值内存）
flm run llama3.2:1b --ctx-len 65536 --prefill-chunk-len 1024
```

**组合参数的实战配置：**

```bash
# 配置1：移动办公（省电优先）
flm run llama3.2:1b --ctx-len 4096 --pmode powersaver

# 配置2：桌面代码助手（性能优先）
flm run qwen3:4b --ctx-len 32768 --pmode turbo --prefill-chunk-len 4096

# 配置3：长文档分析（平衡）
flm run llama3.2:1b --ctx-len 65536 --pmode balanced --prefill-chunk-len 2048

# 配置4：Server 模式（同样适用这些参数）
flm serve qwen3:4b --ctx-len 16384 --pmode balanced --port 52625
```

**参数选择决策表：**

| 场景 | ctx-len | pmode | prefill-chunk | 说明 |
|------|---------|-------|---------------|------|
| 移动短对话 | 4096 | powersaver | 2048 | 省电，续航优先 |
| 桌面日常对话 | 8192 | balanced | 2048 | 默认推荐 |
| 代码助手 | 16384-32768 | balanced | 2048 | 需要长上下文 |
| 长文档总结 | 65536+ | balanced/turbo | 4096 | 大上下文+大分块 |
| 批量处理 | 4096 | turbo | 2048 | 速度优先 |
| 电池模式 | 4096 | powersaver | 1024 | 极致省电 |

#### 总结

本讲核心要点：`--ctx-len` 控制上下文长度（默认 4096，最大 256k），`--pmode` 控制 NPU 功耗（powersaver/balanced/turbo，速度差异 1.5-2 倍），`--prefill-chunk-len` 控制 Prefill 分块（默认 2048，长文档建议 4096）。参数选择应基于场景：移动选省电，桌面选平衡，批量选极速。常见注意事项：上下文长度受 NPU 内存限制，过大设置会导致加载失败；turbo 模式下笔记本会明显发热，长时间使用建议接散热；`--prefill-chunk-len` 不宜过小（<512），否则分块开销过大反而变慢；这些参数在 `flm serve` 中同样适用，是 Server 模式调优的基础。

---

## 第4章 Server 模式与 OpenAI 兼容 API

CLI 模式适合人工交互，但当你需要将 LLM 集成到应用程序中时，就需要 Server 模式了。FastFlowLM 的 Server 模式启动一个 HTTP 服务，提供与 OpenAI API 完全兼容的 REST 接口。这意味着你可以直接使用 OpenAI 官方 SDK、LangChain、LlamaIndex 等生态工具，只需把 `base_url` 指向本地服务即可。本章将讲解 Server 的启动配置、OpenAI API 的消息格式、Python SDK 调用，以及流式输出与多轮对话的实现。这是从"使用者"到"开发者"的关键跨越。

### 第12讲：启动 Server——端口、主机、CORS 配置

#### 概念

`flm serve` 命令启动 FastFlowLM 的 HTTP 服务器，默认监听 `http://127.0.0.1:52625`。Server 模式与 CLI 模式的核心区别在于：它是一个**长驻服务进程**，模型加载一次后常驻内存，通过 REST API 接收请求并返回响应。启动时可以通过参数配置监听端口（`--port`）、绑定主机（`--host`）、跨域策略（`--cors`）等。默认的 API Key 是字符串 `"flm"`，用于兼容需要传 key 的客户端，但本地调用时实际不做鉴权。

#### 原理

FastFlowLM Server 的架构是一个**单模型常驻 + 多请求并发**的设计。启动时指定一个模型（如 `flm serve qwen3:4b`），该模型被加载到 NPU 并常驻。之后所有 API 请求都由这个模型处理。这种设计的好处是避免了每次请求重新加载模型的开销（加载需要 2-5 秒），代价是同一时刻只能服务一个模型。如果需要切换模型，需要重启 Server 并指定新模型。

Server 内部维护一个**请求队列**。当多个 API 请求同时到达时，它们被放入队列，NPU 依次处理（因为 NPU 算力是串行资源）。FastFlowLM 通过 `--preemption` 机制（默认开启）实现智能调度：当一个长请求正在生成时，如果来了一个短请求，系统可以暂停长请求、先处理短请求、再恢复长请求。这种抢占式调度保证了交互式场景下的响应性。

关于网络配置，`--host` 默认是 `127.0.0.1`（仅本机访问），这是出于安全考虑——避免模型服务暴露到网络。如果需要从局域网其他设备访问（如在另一台电脑上调用笔记本的 NPU），可以设置 `--host 0.0.0.0`，但务必确保网络环境可信。`--cors` 参数用于配置跨域资源共享策略，当你的前端应用（运行在浏览器中）需要直接调用 FastFlowLM 时，必须设置允许的源（如 `--cors "http://localhost:3000"`），否则浏览器会因跨域策略拦截请求。

默认端口 52625 是 FastFlowLM 选择的非常规端口，避免与常见的 8080、3000、11434（Ollama 默认）等冲突。可以通过 `flm port <新端口>` 命令修改默认端口，或启动时用 `--port` 临时指定。

#### 例子

**基本启动：**

```bash
# 启动默认配置的 Server（加载 qwen3:4b，监听 127.0.0.1:52625）
flm serve qwen3:4b
# 输出示例：
# Loading model 'qwen3:4b' onto NPU...
# Model loaded successfully.
# Starting FastFlowLM server on http://127.0.0.1:52625
# API endpoint: http://127.0.0.1:52625/v1
# 
# Press Ctrl+C to stop.
```

**自定义端口和主机：**

```bash
# 指定端口
flm serve qwen3:4b --port 8080

# 允许局域网访问（谨慎使用）
flm serve qwen3:4b --host 0.0.0.0 --port 8080

# 组合运行参数
flm serve qwen3:4b --port 8080 --ctx-len 16384 --pmode balanced
```

**配置 CORS（前端调用场景）：**

```bash
# 允许特定源
flm serve qwen3:4b --cors "http://localhost:3000"

# 允许多个源（逗号分隔）
flm serve qwen3:4b --cors "http://localhost:3000,http://localhost:5173"

# 允许所有源（仅开发环境，生产不推荐）
flm serve qwen3:4b --cors "*"
```

**验证 Server 是否正常：**

```bash
# 健康检查
curl http://127.0.0.1:52625/v1/models
# 输出：
# {
#   "object": "list",
#   "data": [
#     {
#       "id": "qwen3:4b",
#       "object": "model",
#       "created": 1718900000,
#       "owned_by": "fastflowlm"
#     }
#   ]
# }

# 简单对话测试
curl http://127.0.0.1:52625/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer flm" \
  -d '{
    "model": "qwen3:4b",
    "messages": [{"role": "user", "content": "你好"}]
  }'
# 输出：
# {
#   "id": "chatcmpl-xxx",
#   "object": "chat.completion",
#   "model": "qwen3:4b",
#   "choices": [{
#     "index": 0,
#     "message": {"role": "assistant", "content": "你好！有什么可以帮你的吗？"},
#     "finish_reason": "stop"
#   }],
#   "usage": {"prompt_tokens": 3, "completion_tokens": 12, "total_tokens": 15}
# }
```

**作为后台服务运行（Linux）：**

```bash
# 方法1：nohup 后台运行
nohup flm serve qwen3:4b --port 52625 > /var/log/flm.log 2>&1 &

# 方法2：创建 systemd 服务（推荐生产环境）
sudo tee /etc/systemd/system/flm.service > /dev/null <<'EOF'
[Unit]
Description=FastFlowLM Server
After=network.target

[Service]
Type=simple
User=your_username
ExecStart=/home/your_username/.local/bin/flm serve qwen3:4b --port 52625
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable flm
sudo systemctl start flm
sudo systemctl status flm    # 查看运行状态
```

#### 总结

本讲核心要点：`flm serve <model>` 启动 HTTP 服务，默认监听 `127.0.0.1:52625`，API 路径为 `/v1`。通过 `--port`、`--host`、`--cors` 配置网络行为。默认 API Key 为 `"flm"`（本地不鉴权，仅为兼容客户端）。Server 是单模型常驻设计，切换模型需重启。常见注意事项：生产环境不要用 `--host 0.0.0.0` 暴露到公网，至少应放在反向代理后并加鉴权；CORS 仅在浏览器前端直连时需要配置，后端调用无需关心；Linux 生产环境推荐用 systemd 管理服务，实现开机自启和崩溃重启；同一端口不能启动多个 Server 实例，端口冲突时会报错。

---

### 第13讲：OpenAI API 标准与多角色消息格式

#### 概念

FastFlowLM Server 提供的是与 OpenAI API **完全兼容**的接口，主要包括四个端点：`/v1/models`（列出可用模型）、`/v1/chat/completions`（对话补全，最常用）、`/v1/audio/transcriptions`（语音转文字）、`/v1/embeddings`（文本向量化）。其中 `/v1/chat/completions` 采用**多角色消息格式**，每条消息有 `role`（角色：system/user/assistant/tool）和 `content`（内容）两个字段。理解这种消息格式是正确调用 API 的基础。

#### 原理

OpenAI 的 Chat Completions API 之所以成为事实标准，在于其**多角色消息设计**清晰地表达了对话的结构。`system` 角色用于设定模型的行为准则和人设（如"你是一个专业的翻译助手"），它在对话开始时发送一次，影响后续所有回复。`user` 角色代表用户的输入。`assistant` 角色代表模型之前的回复——把它包含在消息列表中，模型就能"记住"之前的对话，实现多轮对话。`tool` 角色用于工具调用的返回结果（详见第19讲）。

这种设计的精妙之处在于：**上下文完全由客户端管理**。服务端是无状态的，每次请求都包含完整的对话历史。这意味着客户端可以灵活地截断、修改、注入上下文。例如，你可以在用户消息前插入一段 `system` 消息来动态调整模型行为，或在对话过长时删除最早的几轮以控制 token 数量。FastFlowLM 完全遵循这一设计，因此所有针对 OpenAI 的上下文管理策略都可以直接套用。

请求参数方面，除了 `model` 和 `messages`，还有几个常用可选参数：`temperature`（0-2，控制随机性，越高越有创意，越低越确定）、`max_tokens`（限制生成长度）、`stream`（是否流式输出）、`top_p`（核采样，替代 temperature 的另一种采样策略）。FastFlowLM 支持这些标准参数，但部分高级参数（如 `logprobs`、`n`）可能不支持，调用时需注意。

响应格式遵循 OpenAI 的 JSON Schema，包含 `id`、`object`、`model`、`choices`（数组，每个元素含 `message` 和 `finish_reason`）、`usage`（token 统计）。`finish_reason` 有三种值：`stop`（正常结束）、`length`（达到 max_tokens 截断）、`tool_calls`（模型请求调用工具）。

#### 例子

**基础对话请求：**

```bash
curl http://127.0.0.1:52625/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer flm" \
  -d '{
    "model": "qwen3:4b",
    "messages": [
      {"role": "system", "content": "你是一个简洁的助手，回答不超过50字。"},
      {"role": "user", "content": "什么是 NPU？"}
    ],
    "temperature": 0.7
  }'
```

```json
// 响应示例
{
  "id": "chatcmpl-a1b2c3",
  "object": "chat.completion",
  "created": 1718901234,
  "model": "qwen3:4b",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "NPU是神经网络处理器，专为AI推理设计的专用芯片，能效比高于通用CPU/GPU。"
      },
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 28,
    "completion_tokens": 32,
    "total_tokens": 60
  }
}
```

**多轮对话（携带历史消息）：**

```bash
curl http://127.0.0.1:52625/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer flm" \
  -d '{
    "model": "qwen3:4b",
    "messages": [
      {"role": "user", "content": "我叫小明"},
      {"role": "assistant", "content": "你好小明！很高兴认识你。"},
      {"role": "user", "content": "我叫什么名字？"}
    ]
  }'
```
```json
{
  "choices": [{
    "message": {"role": "assistant", "content": "你叫小明。"},
    "finish_reason": "stop"
  }]
}
```

**动态 system 消息（调整模型行为）：**

```bash
# 同一个模型，不同 system 消息产生不同风格
# 风格1：正式
curl ... -d '{
  "model": "qwen3:4b",
  "messages": [
    {"role": "system", "content": "你是严谨的学术助手，使用正式书面语。"},
    {"role": "user", "content": "解释量子纠缠"}
  ]
}'
# → "量子纠缠是指两个或多个粒子之间存在的一种非经典关联..."

# 风格2：通俗
curl ... -d '{
  "model": "qwen3:4b",
  "messages": [
    {"role": "system", "content": "你用大白话给小学生讲解，多用比喻。"},
    {"role": "user", "content": "解释量子纠缠"}
  ]
}'
# → "想象你有两个魔法骰子，不管隔多远，掷出一个6，另一个也一定是6..."
```

**常用参数对比：**

```bash
# temperature=0（确定性输出，适合代码生成、信息提取）
{"model": "qwen3:4b", "messages": [...], "temperature": 0}

# temperature=0.9（创意输出，适合写作、头脑风暴）
{"model": "qwen3:4b", "messages": [...], "temperature": 0.9}

# max_tokens 限制长度（适合需要短回复的场景）
{"model": "qwen3:4b", "messages": [...], "max_tokens": 100}

# top_p 核采样（替代 temperature，通常二选一）
{"model": "qwen3:4b", "messages": [...], "top_p": 0.9, "temperature": 1}
```

**消息角色速查表：**

| 角色 | 作用 | 出现位置 | 示例 |
|------|------|----------|------|
| system | 设定模型行为 | 消息列表开头，通常1条 | "你是专业翻译" |
| user | 用户输入 | 每轮对话1条 | "翻译这句话" |
| assistant | 模型历史回复 | 多轮对话中携带 | "好的，翻译如下..." |
| tool | 工具返回结果 | 工具调用后1条 | '{"result": 42}' |

#### 总结

本讲核心要点：FastFlowLM 提供 OpenAI 兼容的 `/v1/chat/completions` 端点，采用多角色消息格式（system/user/assistant/tool）。上下文由客户端管理，每次请求携带完整对话历史。常用参数包括 `temperature`（随机性）、`max_tokens`（长度限制）、`stream`（流式）。响应中 `finish_reason` 标识结束原因。常见注意事项：`system` 消息建议放在列表首位且只发一次，重复发送会浪费 token；多轮对话时务必按时间顺序排列消息，乱序会导致模型困惑；`temperature` 和 `top_p` 通常只调一个，同时设置以 `temperature` 为准；`max_tokens` 过小会导致回复被截断（`finish_reason: "length"`）。

---

### 第14讲：Python + OpenAI SDK 调用 FastFlowLM

#### 概念

由于 FastFlowLM 兼容 OpenAI API，你可以直接使用 OpenAI 官方的 Python SDK（`openai` 库）来调用它，只需把 `base_url` 指向本地 Server，`api_key` 填任意字符串即可。这是最推荐的集成方式，因为 OpenAI SDK 经过广泛验证、文档完善、与 LangChain 等框架无缝衔接。本讲将展示如何用 Python 完成从简单对话到结构化输出的各种调用。

#### 原理

OpenAI Python SDK 的设计是**面向接口而非实现**的。它通过 `base_url` 参数将请求发往任意兼容 OpenAI API 的服务端，而不关心背后是 OpenAI 官方、Azure OpenAI 还是 FastFlowLM。SDK 内部处理了 HTTP 请求构造、JSON 序列化、错误重试、流式解析等细节，开发者只需调用高层方法（如 `client.chat.completions.create()`）。

使用 OpenAI SDK 调用 FastFlowLM 的关键配置有三点：第一，`base_url` 设为 `http://127.0.0.1:52625/v1`（注意末尾的 `/v1`，SDK 会自动拼接 `/chat/completions`）；第二，`api_key` 设为 `"flm"` 或任意非空字符串（FastFlowLM 不校验，但 SDK 要求非空）；第三，`model` 参数填 FastFlowLM 中加载的模型名（如 `"qwen3:4b"`）。

SDK 的优势在于**类型安全和错误处理**。响应对象是结构化的（如 `response.choices[0].message.content`），比手动解析 JSON 更安全。SDK 还会自动处理网络超时、连接错误、速率限制等情况，抛出明确的异常类型（`openai.APIError`、`openai.APIConnectionError` 等），便于程序化处理。

#### 例子

**安装与基础调用：**

```bash
pip install openai
```

```python
# basic_chat.py —— 最简单的对话调用
from openai import OpenAI

# 创建客户端，指向本地 FastFlowLM
client = OpenAI(
    base_url="http://127.0.0.1:52625/v1",
    api_key="flm"  # 任意非空字符串
)

response = client.chat.completions.create(
    model="qwen3:4b",
    messages=[
        {"role": "system", "content": "你是一个有帮助的中文助手。"},
        {"role": "user", "content": "用一句话解释什么是 NPU。"}
    ]
)

print(response.choices[0].message.content)
# 输出：NPU是专为神经网络计算设计的处理器，在AI推理任务上比CPU/GPU更高效。
print(f"Token 用量: {response.usage}")
# Token 用量: CompletionUsage(prompt_tokens=25, completion_tokens=30, total_tokens=55)
```

**封装为可复用的对话函数：**

```python
# chat_helper.py —— 封装常用对话逻辑
from openai import OpenAI

client = OpenAI(base_url="http://127.0.0.1:52625/v1", api_key="flm")

def chat(prompt: str, system: str = "你是一个有帮助的助手。", 
         model: str = "qwen3:4b", temperature: float = 0.7) -> str:
    """单轮对话快捷函数"""
    response = client.chat.completions.create(
        model=model,
        messages=[
            {"role": "system", "content": system},
            {"role": "user", "content": prompt}
        ],
        temperature=temperature
    )
    return response.choices[0].message.content

# 使用
answer = chat("写一个 Python 快速排序函数", system="你是资深 Python 工程师")
print(answer)
```

**结构化输出（JSON 模式）：**

```python
# structured_output.py —— 让模型返回结构化数据
import json

client = OpenAI(base_url="http://127.0.0.1:52625/v1", api_key="flm")

prompt = """分析以下产品评论，返回 JSON 格式：
评论："这款笔记本的 NPU 推理速度很快，但电池续航一般。"
返回格式：{"sentiment": "正面/负面/中性", "aspects": [{"feature": "...", "opinion": "..."}]}
只返回 JSON，不要其他内容。"""

response = client.chat.completions.create(
    model="qwen3:4b",
    messages=[{"role": "user", "content": prompt}],
    temperature=0  # 结构化输出用低温度保证稳定
)

result = json.loads(response.choices[0].message.content)
print(json.dumps(result, ensure_ascii=False, indent=2))
# {
#   "sentiment": "中性",
#   "aspects": [
#     {"feature": "NPU推理速度", "opinion": "快"},
#     {"feature": "电池续航", "opinion": "一般"}
#   ]
# }
```

**错误处理：**

```python
# error_handling.py —— 健壮的调用封装
from openai import OpenAI, APIConnectionError, APIError, APITimeoutError

client = OpenAI(base_url="http://127.0.0.1:52625/v1", api_key="flm", timeout=30.0)

def safe_chat(prompt: str, retries: int = 3) -> str:
    for attempt in range(retries):
        try:
            response = client.chat.completions.create(
                model="qwen3:4b",
                messages=[{"role": "user", "content": prompt}]
            )
            return response.choices[0].message.content
        except APIConnectionError:
            print(f"无法连接 Server，请确认 flm serve 已启动 (尝试 {attempt+1}/{retries})")
        except APITimeoutError:
            print(f"请求超时 (尝试 {attempt+1}/{retries})")
        except APIError as e:
            print(f"API 错误: {e} (尝试 {attempt+1}/{retries})")
        if attempt < retries - 1:
            import time; time.sleep(2 ** attempt)  # 指数退避
    raise RuntimeError("所有重试失败")

# 使用
print(safe_chat("你好"))
```

**异步调用（适合高并发场景）：**

```python
# async_chat.py —— 异步批量调用
import asyncio
from openai import AsyncOpenAI

async_client = AsyncOpenAI(base_url="http://127.0.0.1:52625/v1", api_key="flm")

async def chat_one(prompt: str) -> str:
    response = await async_client.chat.completions.create(
        model="qwen3:4b",
        messages=[{"role": "user", "content": prompt}]
    )
    return response.choices[0].message.content

async def batch_chat(prompts: list[str]) -> list[str]:
    # 注意：NPU 是串行资源，并发请求会在 Server 端排队
    # 这里并发发送，但实际处理是串行的
    tasks = [chat_one(p) for p in prompts]
    return await asyncio.gather(*tasks)

# 使用
prompts = ["解释 RAG", "解释 NPU", "解释 Transformer"]
results = asyncio.run(batch_chat(prompts))
for p, r in zip(prompts, results):
    print(f"Q: {p}\nA: {r[:100]}...\n")
```

#### 总结

本讲核心要点：使用 OpenAI Python SDK 调用 FastFlowLM，只需设置 `base_url="http://127.0.0.1:52625/v1"` 和 `api_key="flm"`。SDK 提供类型安全的响应对象和完善的错误处理。结构化输出建议用 `temperature=0` 并在 prompt 中明确要求 JSON 格式。常见注意事项：`base_url` 必须包含 `/v1` 后缀，否则 404；`api_key` 不能为空字符串，否则 SDK 报错；NPU 是串行资源，并发请求会在 Server 端排队，不会真正并行加速；调用前务必确认 `flm serve` 已启动，否则连接失败；超时时间建议设 30 秒以上，长文本生成可能较慢。

---

### 第15讲：流式输出与多轮对话

#### 概念

**流式输出（Streaming）** 是指 Server 在生成 token 的同时逐步返回，而非等全部生成完再一次性返回。这种模式能显著降低用户感知的首字延迟——用户在模型开始生成后立即看到文字逐字出现，体验更自然。**多轮对话**则是指通过在每次请求中携带完整的对话历史（user/assistant 消息交替），让模型保持上下文连贯。两者结合，就能构建出类似 ChatGPT 的交互体验。

#### 原理

流式输出的底层是 **Server-Sent Events（SSE）** 协议。客户端在请求中设置 `"stream": true`，Server 返回的 `Content-Type` 为 `text/event-stream`，响应体由多个 `data: {...}\n\n` 块组成，每个块是一个 JSON 对象，包含当前生成的 token。当生成结束时，Server 发送 `data: [DONE]\n\n` 标记完成。客户端逐块解析并即时渲染，实现打字机效果。

流式的价值在于**感知性能**而非实际性能。从第一个 token 到最后一个 token 的总时间不变，但用户看到第一个字的时间从"等待完整生成"提前到了"生成第一个 token"。对于长回复（如几百字），这种差异非常明显——用户可能等 2 秒看到首字，而非等 10 秒看到整段。

多轮对话的原理在第13讲已介绍：客户端维护消息列表，每次请求携带完整历史。关键在于**上下文管理策略**。随着轮数增加，消息列表会膨胀，最终超过 `--ctx-len` 上限。常见策略有三种：**截断法**（保留最近 N 轮，丢弃更早的）、**摘要法**（用模型对旧对话生成摘要，用摘要替代原始消息）、**滑动窗口法**（结合前两者）。对于 FastFlowLM 这样的本地推理，截断法最简单实用。

OpenAI SDK 对流式输出有原生支持，通过 `stream=True` 参数和迭代器接口，可以方便地逐 token 处理，无需手动解析 SSE。

#### 例子

**流式输出（SDK 方式）：**

```python
# stream_chat.py —— 流式对话
from openai import OpenAI

client = OpenAI(base_url="http://127.0.0.1:52625/v1", api_key="flm")

print("助手: ", end="", flush=True)
stream = client.chat.completions.create(
    model="qwen3:4b",
    messages=[{"role": "user", "content": "写一首关于编程的短诗"}],
    stream=True  # 开启流式
)

for chunk in stream:
    delta = chunk.choices[0].delta.content
    if delta:
        print(delta, end="", flush=True)  # 逐字打印
print()  # 换行

# 输出（逐字出现）：
# 键盘敲击如雨落，
# 屏幕闪烁似星河。
# 逻辑编织千行码，
# 思维构建万重阁。
```

**完整的交互式聊天程序：**

```python
# interactive_chat.py —— 类似 ChatGPT 的终端聊天
from openai import OpenAI

client = OpenAI(base_url="http://127.0.0.1:52625/v1", api_key="flm")

# 对话历史（客户端维护）
messages = [
    {"role": "system", "content": "你是一个有帮助的中文助手，回答简洁明了。"}
]

MAX_HISTORY = 20  # 最多保留 20 条消息（10 轮对话）

print("聊天已启动（输入 'quit' 退出）\n")

while True:
    user_input = input("你: ").strip()
    if user_input.lower() in ("quit", "exit", "q"):
        break
    if not user_input:
        continue
    
    # 添加用户消息
    messages.append({"role": "user", "content": user_input})
    
    # 上下文管理：超过上限时截断（保留 system 消息）
    if len(messages) > MAX_HISTORY:
        messages = [messages[0]] + messages[-(MAX_HISTORY-1):]
    
    # 流式输出回复
    print("助手: ", end="", flush=True)
    assistant_reply = ""
    stream = client.chat.completions.create(
        model="qwen3:4b",
        messages=messages,
        stream=True
    )
    for chunk in stream:
        delta = chunk.choices[0].delta.content
        if delta:
            print(delta, end="", flush=True)
            assistant_reply += delta
    print("\n")
    
    # 将回复加入历史
    messages.append({"role": "assistant", "content": assistant_reply})
```

**运行效果：**

```bash
$ python interactive_chat.py
聊天已启动（输入 'quit' 退出）

你: 我叫小明，请记住
助手: 好的，小明，我记住了。有什么可以帮你的？

你: 我叫什么？
助手: 你叫小明。

你: 请用我的名字写一首藏头诗
助手: 小舟轻泛碧波间，
明月初升照远山。
...
```

**带上下文摘要的多轮对话（高级）：**

```python
# summarized_chat.py —— 当历史过长时自动摘要
from openai import OpenAI

client = OpenAI(base_url="http://127.0.0.1:52625/v1", api_key="flm")

def chat_with_summary(messages: list, user_input: str, max_messages: int = 10) -> list:
    messages.append({"role": "user", "content": user_input})
    
    # 历史过长时，将早期对话摘要
    if len(messages) > max_messages:
        old_messages = messages[1:-4]  # 保留 system 和最近 4 条
        summary_prompt = "请用一段话概括以下对话的要点：\n"
        for m in old_messages:
            summary_prompt += f"{m['role']}: {m['content']}\n"
        
        summary_resp = client.chat.completions.create(
            model="qwen3:4b",
            messages=[{"role": "user", "content": summary_prompt}]
        )
        summary = summary_resp.choices[0].message.content
        
        # 用摘要替代旧消息
        messages = [messages[0]] + [
            {"role": "system", "content": f"之前对话的摘要：{summary}"}
        ] + messages[-4:]
    
    # 正常请求
    response = client.chat.completions.create(
        model="qwen3:4b",
        messages=messages
    )
    reply = response.choices[0].message.content
    messages.append({"role": "assistant", "content": reply})
    return messages

# 使用
messages = [{"role": "system", "content": "你是个人助手。"}]
for i in range(15):
    user_input = f"这是第{i+1}轮对话，讨论话题{i}。"
    messages = chat_with_summary(messages, user_input)
    print(f"轮次 {i+1}: 历史消息数 {len(messages)}")
```

#### 总结

本讲核心要点：流式输出通过 SSE 协议实现，设置 `stream=True` 即可，能显著降低首字延迟。多轮对话由客户端维护消息列表，每次请求携带完整历史。上下文管理策略包括截断法（简单）、摘要法（省 token 但需额外调用）、滑动窗口法（推荐）。常见注意事项：流式输出的 `chunk.choices[0].delta.content` 可能为 `None`（首个 chunk 只有 role），需判空；截断历史时务必保留 `system` 消息，否则模型人设丢失；摘要法会引入额外推理开销，仅在长对话场景使用；多轮对话的 token 消耗会累积，注意监控 `usage` 字段避免超限。

---

## 第5章 模型生态与选型

FastFlowLM 的价值不仅在于推理引擎本身，更在于它支持的丰富模型生态。本章将系统介绍 FastFlowLM 支持的模型家族、模型标签与版本管理机制，以及多模态模型（视觉、语音、嵌入）的使用方法。理解模型生态后，你才能针对具体任务选择最合适的模型，在效果、速度和资源消耗之间取得平衡。本章是进阶开发前的知识储备，也是实际项目中做出正确技术选型的关键。

### 第16讲：支持的模型家族总览

#### 概念

FastFlowLM 支持多个主流开源模型家族，覆盖从 1B 到 30B+ 参数规模的不同需求。主要包括：**LLaMA 系列**（Meta 出品，包括 LLaMA 3.2、gpt-oss 等）、**Qwen 系列**（阿里出品，Qwen3 系列，支持工具调用）、**Gemma 系列**（Google 出品，Gemma 3，支持视觉）、**Phi 系列**（微软出品，小而精）、**DeepSeek 系列**（深度推理能力强）、**LiquidAI LFM 系列**（高效架构）、以及专门的 **Whisper**（语音识别）和 **EmbeddingGemma**（文本嵌入）模型。每个家族有自己的特点和适用场景。

#### 原理

FastFlowLM 支持多模型家族的核心在于其**模型适配层**。虽然底层 NPU 内核是统一的（针对 XDNA2 架构优化），但不同模型家族的架构差异（如注意力机制、归一化层位置、激活函数等）需要不同的计算图映射。FastFlowLM 为每个支持的模型架构维护一套适配代码，将模型权重转换为 NPU 可执行的指令序列。这也是为什么 FastFlowLM 不能像 Ollama 那样支持任意 GGUF 模型——每个架构都需要专门的适配工作。

各模型家族的设计哲学不同，导致它们在不同任务上表现各异。LLaMA 系列以通用性和稳定性著称，适合大多数对话和文本任务。Qwen 系列在中文理解和代码生成上表现突出，且原生支持工具调用（function calling），是构建 Agent 的优选。Gemma 系列体积小、速度快，且 Gemma 3 支持视觉输入，适合多模态场景。Phi 系列以"小模型大能力"为卖点，1B-3B 参数就能达到接近大模型的效果，特别适合资源受限的 NPU 场景。DeepSeek 系列的 R1 推理模型擅长数学和逻辑推理。Whisper 是 OpenAI 的语音识别模型，支持多语言转写。EmbeddingGemma 专门用于生成文本向量，是 RAG 系统的基础组件。

参数规模的选择需要权衡效果和资源。1B 模型占用约 1.2 GB NPU 内存，推理速度最快（50+ tok/s），适合简单对话和轻量任务。4B 模型占用约 4 GB，效果显著优于 1B，是性价比之选。8B+ 模型效果更好但内存占用大，且推理速度下降，需要更大的 NPU 内存。对于搭载 16 GB NPU 内存的 Strix 系列，4B 模型是日常推荐，8B 是上限。

#### 例子

**主流模型家族速览：**

```bash
# 拉取各家族代表模型
flm pull llama3.2:1b          # LLaMA 3.2，1B 参数，轻量通用
flm pull llama3.2:3b          # LLaMA 3.2，3B 参数，平衡之选
flm pull qwen3:4b             # Qwen3，4B，中文+代码+工具调用
flm pull qwen3:8b             # Qwen3，8B，更强能力
flm pull gemma3:4b            # Gemma 3，4B，支持视觉
flm pull phi4:14b             # Phi-4，14B，小模型大能力（需大内存）
flm pull deepseek-r1:8b       # DeepSeek R1，8B，推理能力强
flm pull whisper-large        # Whisper，语音识别
flm pull embeddinggemma:300m  # 嵌入模型，用于 RAG
```

**不同模型的典型输出对比（同一问题"解释递归"）：**

```bash
# LLaMA 3.2 1B（简洁直接）
flm run llama3.2:1b
>>> 用一句话解释递归
递归是函数调用自身来解决问题的编程技巧。

# Qwen3 4B（详细且带例子）
flm run qwen3:4b
>>> 用一句话解释递归
递归是一种函数直接或间接调用自身的算法，通过将大问题分解为同类的子问题来求解，例如计算阶乘时 n! = n × (n-1)!。

# Phi-4 14B（深度且严谨）
flm run phi4:14b
>>> 用一句话解释递归
递归是一种自我指涉的计算范式：一个过程在其定义中引用自身，通过基线条件终止、通过递归条件推进，将复杂问题规约为结构相同但规模更小的子问题直至可解，再逐层合并结果。
```

**模型选型决策表：**

| 任务场景 | 推荐模型 | 理由 |
|----------|----------|------|
| 日常中文对话 | qwen3:4b | 中文理解强，速度快 |
| 英文对话/写作 | llama3.2:3b | 通用性好，英文原生 |
| 代码生成 | qwen3:4b 或 deepseek-r1:8b | 代码训练充分 |
| 数学/逻辑推理 | deepseek-r1:8b | R1 专为推理优化 |
| 图像理解 | gemma3:4b | 支持视觉输入 |
| 语音转文字 | whisper-large | 专为 ASR 设计 |
| RAG 向量化 | embeddinggemma:300m | 专用嵌入模型 |
| 资源受限（移动） | llama3.2:1b | 体积小、速度快 |
| 最佳效果（大内存） | phi4:14b 或 qwen3:8b | 参数量大，能力强 |

**查看模型详细信息：**

```bash
flm list
# NAME                 SIZE      PARAMS    CONTEXT    CAPABILITIES
# llama3.2:1b          1.2 GB    1.24B     128k       text
# qwen3:4b             4.1 GB    4.02B     128k       text, tools
# gemma3:4b            3.8 GB    4.25B     128k       text, vision
# deepseek-r1:8b       8.2 GB    8.10B     64k        text, reasoning
# whisper-large        1.5 GB    1.55B     -          audio
# embeddinggemma:300m  0.3 GB    0.30B     2k         embedding
```

#### 总结

本讲核心要点：FastFlowLM 支持 LLaMA、Qwen、Gemma、Phi、DeepSeek、LFM 等主流文本模型家族，以及 Whisper（语音）和 EmbeddingGemma（嵌入）专用模型。选型应基于任务（中文选 Qwen，推理选 DeepSeek，多模态选 Gemma）和资源（移动选 1B，桌面选 4B，大内存选 8B+）。常见注意事项：不是所有开源模型都被支持，FastFlowLM 需要为每个架构做适配；模型参数量越大效果越好但速度越慢、内存占用越大；4B 模型是 16GB NPU 的性价比甜点；专用模型（Whisper、EmbeddingGemma）不要用于通用对话，效果会很差。

---

### 第17讲：模型标签与版本管理

#### 概念

FastFlowLM 采用 `模型名:标签` 的命名格式（如 `qwen3:4b`、`llama3.2:1b`），其中冒号后的**标签**标识模型的具体版本或规格。标签通常表示参数规模（`1b`、`4b`、`8b`），但也可能表示量化精度、上下文长度变体等。理解标签体系能帮你精确选择所需版本，避免混淆。同时，FastFlowLM 支持同一模型多个标签共存，方便对比测试。

#### 原理

标签系统的设计借鉴了 Docker 镜像标签的思路——同一个"模型名"可以有多个"标签"，每个标签对应一个具体的可执行版本。这种设计的好处是：用户可以用简短的名称引用模型（如 `qwen3:4b`），而不需要记住完整的版本号或文件名；同时，多个版本可以共存，便于 A/B 测试或回滚。

标签的命名遵循一定约定。最常见的格式是**参数规模**（如 `1b`、`3b`、`4b`、`8b`、`14b`），直接反映模型的参数量级，是最直观的选型依据。有时标签会包含**量化信息**（如 `q4`、`q8` 表示 4-bit、8-bit 量化），但 FastFlowLM 默认使用 NPU 优化的 INT8 量化，因此大多数模型不需要在标签中标注量化。少数模型会有**上下文变体**（如 `128k`、`256k`），表示支持的最大上下文长度。

版本管理方面，FastFlowLM 的模型仓库会持续更新。当模型有新版本（如 Qwen3 从 v1 升级到 v2）时，通常通过新标签发布（如 `qwen3:4b-v2`），旧标签保持不变以保证兼容性。用户可以通过 `flm pull` 重新拉取来获取更新，或保持旧版本不变。`flm list` 显示的 `MODIFIED` 时间戳可以帮助判断本地模型是否需要更新。

一个实用技巧是**多版本对比**。你可以同时拉取同一模型的多个标签（如 `qwen3:4b` 和 `qwen3:8b`），通过切换 Server 加载的模型来对比效果。但注意，同时只能运行一个模型，对比时需要重启 Server。

#### 例子

**标签命名规则示例：**

```bash
# 参数规模标签（最常见）
flm pull llama3.2:1b      # 1.24B 参数
flm pull llama3.2:3b      # 3.21B 参数
flm pull qwen3:4b         # 4.02B 参数
flm pull qwen3:8b         # 8.10B 参数

# 上下文变体标签
flm pull llama3.2:1b-128k  # 支持 128k 上下文的变体

# 量化标签（较少见，FastFlowLM 默认 INT8）
flm pull somemodel:4b-q4   # 4-bit 量化版本（体积更小）
```

**多版本共存与对比：**

```bash
# 同时拉取多个版本
flm pull qwen3:4b
flm pull qwen3:8b

flm list
# NAME         SIZE      MODIFIED
# qwen3:4b     4.1 GB    1 hour ago
# qwen3:8b     8.2 GB    5 mins ago

# 用 4b 版本测试
flm serve qwen3:4b --port 52625
# （在另一个终端测试效果后，Ctrl+C 停止）

# 切换到 8b 版本对比
flm serve qwen3:8b --port 52625
```

**检查并更新模型：**

```bash
# 查看本地模型及其更新时间
flm list
# NAME            SIZE      MODIFIED
# llama3.2:1b     1.2 GB    30 days ago   ← 可能过时

# 重新拉取以检查更新
flm pull llama3.2:1b
# Checking for updates...
# New version available! Downloading...
# pulling kernel (1.2 GB)... 100%
# Updated successfully.

# 或显式验证
flm validate llama3.2:1b
# ✅ Model is up to date (v2.1.0)
```

**清理旧版本释放空间：**

```bash
# 查看磁盘占用
flm list
# NAME            SIZE
# qwen3:4b        4.1 GB
# qwen3:8b        8.2 GB
# llama3.2:1b     1.2 GB
# gemma3:4b       3.8 GB
# Total: 17.3 GB

# 删除不再需要的版本
flm remove qwen3:8b
# Are you sure? (y/N): y
# Removed 'qwen3:8b', freed 8.2 GB
```

**在代码中指定模型版本：**

```python
from openai import OpenAI
client = OpenAI(base_url="http://127.0.0.1:52625/v1", api_key="flm")

# model 参数必须与 flm serve 加载的模型名（含标签）一致
response = client.chat.completions.create(
    model="qwen3:4b",  # 必须是 "模型名:标签" 完整格式
    messages=[{"role": "user", "content": "你好"}]
)
```

#### 总结

本讲核心要点：FastFlowLM 采用 `模型名:标签` 命名，标签通常表示参数规模（1b/4b/8b）。同一模型的多版本可共存，便于对比。通过 `flm pull` 重新拉取可获取更新。常见注意事项：API 调用时的 `model` 参数必须与 `flm serve` 加载的模型名完全一致（含标签），否则报错；标签区分大小写；删除模型前确认没有正在运行的服务依赖它；定期用 `flm list` 检查并清理旧版本，避免磁盘浪费；新版本模型可能需要更新 FastFlowLM 运行时本身（`flm version` 检查）。

---

### 第18讲：多模态模型——VLM、Whisper ASR、Embedding

#### 概念

除了文本对话，FastFlowLM 还支持三种多模态能力：**VLM（视觉语言模型）** 如 Gemma 3 能理解图像并回答关于图像的问题；**Whisper ASR（自动语音识别）** 能将音频转写为文字；**Embedding 模型** 能将文本转换为向量，用于语义搜索和 RAG。这三种能力分别通过 `/v1/chat/completions`（带图像输入）、`/v1/audio/transcriptions`、`/v1/embeddings` 三个 API 端点提供，均兼容 OpenAI 格式。

#### 原理

**VLM（Vision-Language Model）** 的工作原理是在文本模型基础上增加一个视觉编码器。图像先经过视觉编码器（通常是 ViT 架构）提取特征向量，这些向量与文本 token 嵌入拼接后送入语言模型。FastFlowLM 中的 Gemma 3 4B 就是这样的多模态模型，它能同时接收图像和文本输入，生成关于图像的描述、回答或推理。图像输入采用 Base64 编码或 URL 引用，放在消息的 `image_url` 字段中。

**Whisper ASR** 采用编码器-解码器架构处理语音。音频先被转换为梅尔频谱图（Mel spectrogram），编码器提取声学特征，解码器逐步生成文本 token。Whisper 支持多语言识别和翻译，`whisper-large` 模型在常见语言上达到接近人类的准确率。FastFlowLM 通过 `/v1/audio/transcriptions` 端点提供这一能力，输入音频文件（mp3/wav/m4a 等），输出转写文本。

**Embedding 模型** 的原理是将文本映射到高维向量空间（如 768 维或 1536 维），使语义相近的文本在向量空间中距离也相近。这种向量是 RAG（检索增强生成）系统的基础——将文档库向量化后，用户查询也向量化，通过向量相似度检索最相关的文档片段。FastFlowLM 的 EmbeddingGemma 模型通过 `/v1/embeddings` 端点提供服务，输入文本，输出浮点数向量。

这三种多模态能力可以组合使用。例如，构建一个"看图说话"应用：先用 VLM 描述图像内容，再用 Embedding 将描述向量化存入知识库；或者构建语音助手：先用 Whisper 将用户语音转文字，再用 LLM 生成回复，最后用 TTS（需外部工具）合成语音。

#### 例子

**视觉语言模型（VLM）——图像理解：**

```bash
# 启动支持视觉的模型
flm serve gemma3:4b --port 52625
```

```python
# vision_chat.py —— 图像理解
import base64
from openai import OpenAI

client = OpenAI(base_url="http://127.0.0.1:52625/v1", api_key="flm")

# 将图片编码为 base64
def encode_image(path: str) -> str:
    with open(path, "rb") as f:
        return base64.b64encode(f.read()).decode()

image_base64 = encode_image("chart.png")

response = client.chat.completions.create(
    model="gemma3:4b",
    messages=[
        {
            "role": "user",
            "content": [
                {"type": "text", "text": "请描述这张图片的内容，并分析其中的数据趋势。"},
                {
                    "type": "image_url",
                    "image_url": {"url": f"data:image/png;base64,{image_base64}"}
                }
            ]
        }
    ]
)
print(response.choices[0].message.content)
# 输出示例：
# 这是一张展示 2024 年各季度销售额的柱状图。
# 从图中可以看出，销售额从 Q1 的 120 万逐步增长到 Q4 的 280 万，
# 整体呈上升趋势，Q4 增长尤为显著（环比增长 25%）。
```

**Whisper 语音识别：**

```bash
# 启动 Whisper 模型（注意 --asr 标志）
flm serve whisper-large --asr --port 52625
```

```python
# transcribe.py —— 语音转文字
from openai import OpenAI

client = OpenAI(base_url="http://127.0.0.1:52625/v1", api_key="flm")

# 转写本地音频文件
with open("meeting.mp3", "rb") as audio_file:
    transcript = client.audio.transcriptions.create(
        model="whisper-large",
        file=audio_file,
        language="zh",  # 指定语言可提升准确率
        response_format="text"  # 也可选 json、verbose_json
    )

print(transcript)
# 输出示例：
# 大家好，今天我们讨论一下项目进度。目前第一阶段已经完成，
# 第二阶段预计下周开始。主要的风险点在于第三方接口的稳定性...
```

```bash
# 用 curl 调用语音识别
curl http://127.0.0.1:52625/v1/audio/transcriptions \
  -H "Authorization: Bearer flm" \
  -F "model=whisper-large" \
  -F "file=@meeting.mp3" \
  -F "language=zh"
```

**Embedding 文本向量化：**

```bash
# 启动嵌入模型
flm serve embeddinggemma:300m --port 52625
```

```python
# embedding.py —— 文本向量化与相似度计算
import numpy as np
from openai import OpenAI

client = OpenAI(base_url="http://127.0.0.1:52625/v1", api_key="flm")

def get_embedding(text: str) -> list[float]:
    response = client.embeddings.create(
        model="embeddinggemma:300m",
        input=text
    )
    return response.data[0].embedding

# 计算两段文本的语义相似度
text1 = "NPU 是专门为 AI 推理设计的处理器"
text2 = "神经网络处理器针对人工智能计算做了优化"
text3 = "今天天气真好，适合出去玩"

emb1 = np.array(get_embedding(text1))
emb2 = np.array(get_embedding(text2))
emb3 = np.array(get_embedding(text3))

# 余弦相似度
sim_12 = np.dot(emb1, emb2) / (np.linalg.norm(emb1) * np.linalg.norm(emb2))
sim_13 = np.dot(emb1, emb3) / (np.linalg.norm(emb1) * np.linalg.norm(emb3))

print(f"文本1 vs 文本2（语义相近）: {sim_12:.4f}")  # ~0.85
print(f"文本1 vs 文本3（语义无关）: {sim_13:.4f}")  # ~0.20
```

**多模态组合应用——语音问答助手：**

```python
# voice_assistant.py —— 语音输入 + 文本回复
import base64
from openai import OpenAI

# 注意：Whisper 和 LLM 需要分别启动 Server（不同端口）
asr_client = OpenAI(base_url="http://127.0.0.1:52626/v1", api_key="flm")  # Whisper
llm_client = OpenAI(base_url="http://127.0.0.1:52625/v1", api_key="flm")  # Qwen3

def voice_qa(audio_path: str) -> str:
    # 第一步：语音转文字
    with open(audio_path, "rb") as f:
        transcript = asr_client.audio.transcriptions.create(
            model="whisper-large", file=f, language="zh"
        )
    user_text = transcript if isinstance(transcript, str) else transcript.text
    print(f"识别到: {user_text}")
    
    # 第二步：LLM 生成回复
    response = llm_client.chat.completions.create(
        model="qwen3:4b",
        messages=[{"role": "user", "content": user_text}]
    )
    return response.choices[0].message.content

answer = voice_qa("question.mp3")
print(f"回复: {answer}")
```

#### 总结

本讲核心要点：FastFlowLM 支持三种多模态能力——VLM（Gemma 3，图像理解，通过 `image_url` 传入 base64 图像）、Whisper ASR（语音转文字，通过 `/v1/audio/transcriptions`）、Embedding（文本向量化，通过 `/v1/embeddings`）。三者均兼容 OpenAI API 格式。常见注意事项：VLM 的图像需 base64 编码，大图片会显著增加 token 消耗；Whisper 启动需加 `--asr` 标志，且与文本模型不能同时加载（需分端口启动）；Embedding 模型不要用于对话，它只输出向量；多模态组合应用时注意各模型需独立 Server 实例；图像理解效果取决于模型规模，4B 模型适合简单场景，复杂图像分析可能需要更大模型。

---

## 第6章 进阶应用开发

掌握了基础对话和 API 调用后，本章将带你进入真正的应用开发领域。我们将依次讲解四个进阶能力：工具调用（让 LLM 能执行外部函数）、RAG 检索增强（让 LLM 基于私有知识回答）、多模态输入（图像+文本联合推理）、批量推理（高效处理大量请求）。这些能力是构建实用 AI 应用的基石，也是 FastFlowLM 从"玩具"走向"工具"的关键。每讲都包含完整的可运行代码，你可以直接作为项目模板使用。

### 第19讲：工具调用（Tool Calling）实战

#### 概念

**工具调用（Tool Calling，又称 Function Calling）** 是指 LLM 根据用户意图，自主决定调用哪个外部函数，并生成符合函数签名参数的调用请求。例如，用户问"北京今天天气如何"，LLM 判断需要调用 `get_weather` 函数，生成 `{"city": "北京"}` 参数，客户端执行该函数后将结果返回给 LLM，LLM 再基于结果生成自然语言回复。FastFlowLM 从 v0.9.26 起支持工具调用，兼容 OpenAI 的 tools API 格式，推荐使用 Qwen3 系列模型（原生支持工具调用）。

#### 原理

工具调用的核心是一个**多轮交互循环**。第一轮，客户端在请求中附带可用工具的定义（名称、描述、参数 schema），LLM 分析用户输入后，如果判断需要调用工具，会在响应中返回 `tool_calls` 字段（包含函数名和参数），此时 `finish_reason` 为 `"tool_calls"`。第二轮，客户端执行对应的函数，将结果以 `role: "tool"` 的消息形式追加到对话历史，再次请求 LLM。LLM 结合工具返回结果生成最终的自然语言回复。

LLM 如何"知道"该调用哪个工具？这依赖于**函数描述的语义匹配**。每个工具的定义包含 `name`（函数名）、`description`（自然语言描述，如"获取指定城市的天气"）、`parameters`（参数的 JSON Schema）。LLM 在推理时，会将用户输入与所有工具的 description 进行语义匹配，选择最相关的工具。因此，**description 的质量直接决定工具调用的准确率**——描述越清晰、越具体，LLM 的选择越准确。

参数生成方面，LLM 根据 `parameters` 中的 JSON Schema（定义了参数名、类型、是否必填、枚举值等）生成符合规范的 JSON。FastFlowLM 的 Qwen3 模型经过专门训练，能稳定生成合法 JSON，但偶尔仍需客户端做容错处理（如重试或参数补全）。

值得注意的是，工具调用支持**链式调用**——LLM 可以在一次回复中调用多个工具，或根据前一个工具的结果决定调用下一个工具。这为构建复杂 Agent 提供了基础。

#### 例子

**定义工具并执行调用：**

```python
# tool_calling.py —— 完整的工具调用流程
import json
from openai import OpenAI

client = OpenAI(base_url="http://127.0.0.1:52625/v1", api_key="flm")

# 定义可用的工具（函数）
def get_weather(city: str) -> str:
    """模拟天气查询（实际项目中调用天气 API）"""
    weather_data = {"北京": "晴，25°C", "上海": "多云，28°C", "广州": "雷阵雨，30°C"}
    return weather_data.get(city, f"未知城市: {city}")

def calculate(expression: str) -> str:
    """安全计算数学表达式"""
    try:
        allowed = set("0123456789+-*/.() ")
        if all(c in allowed for c in expression):
            return str(eval(expression))
        return "表达式包含非法字符"
    except Exception as e:
        return f"计算错误: {e}"

# 工具的 OpenAI 格式定义
tools = [
    {
        "type": "function",
        "function": {
            "name": "get_weather",
            "description": "获取指定城市的当前天气情况",
            "parameters": {
                "type": "object",
                "properties": {
                    "city": {"type": "string", "description": "城市名称，如'北京'"}
                },
                "required": ["city"]
            }
        }
    },
    {
        "type": "function",
        "function": {
            "name": "calculate",
            "description": "计算数学表达式的结果，支持加减乘除",
            "parameters": {
                "type": "object",
                "properties": {
                    "expression": {"type": "string", "description": "数学表达式，如'2+3*4'"}
                },
                "required": ["expression"]
            }
        }
    }
]

# 工具名到函数的映射
tool_functions = {"get_weather": get_weather, "calculate": calculate}

# 工具调用主循环
def chat_with_tools(user_input: str):
    messages = [{"role": "user", "content": user_input}]
    
    while True:  # 循环直到 LLM 不再请求工具
        response = client.chat.completions.create(
            model="qwen3:4b",
            messages=messages,
            tools=tools,
            tool_choice="auto"  # auto 表示让 LLM 自主决定
        )
        
        msg = response.choices[0].message
        messages.append(msg)
        
        # 如果没有工具调用，返回最终回复
        if not msg.tool_calls:
            return msg.content
        
        # 执行每个工具调用
        for tool_call in msg.tool_calls:
            func_name = tool_call.function.name
            func_args = json.loads(tool_call.function.arguments)
            print(f"  [调用工具] {func_name}({func_args})")
            
            # 执行函数
            result = tool_functions[func_name](**func_args)
            print(f"  [工具返回] {result}")
            
            # 将结果加入对话
            messages.append({
                "role": "tool",
                "tool_call_id": tool_call.id,
                "content": str(result)
            })
        # 循环继续，让 LLM 基于工具结果生成回复

# 测试
print(chat_with_tools("北京和上海今天天气怎么样？哪个更热？"))
# 执行过程：
#   [调用工具] get_weather({"city": "北京"})
#   [工具返回] 晴，25°C
#   [调用工具] get_weather({"city": "上海"})
#   [工具返回] 多云，28°C
# 最终回复：北京今天晴，25°C；上海多云，28°C。上海比北京更热，温差约3度。

print(chat_with_tools("帮我计算 (15 + 27) * 3 的结果"))
#   [调用工具] calculate({"expression": "(15 + 27) * 3"})
#   [工具返回] 126
# 最终回复：(15 + 27) × 3 的计算结果是 126。
```

**强制调用特定工具：**

```python
# 强制调用某个工具（不依赖 LLM 判断）
response = client.chat.completions.create(
    model="qwen3:4b",
    messages=[{"role": "user", "content": "随便说点什么"}],
    tools=tools,
    tool_choice={"type": "function", "function": {"name": "get_weather"}}
    # 即使无关也强制调用 get_weather
)
```

**构建一个实用工具集（文件操作助手）：**

```python
# file_assistant.py —— 能读写文件的工具助手
import os, json
from openai import OpenAI

client = OpenAI(base_url="http://127.0.0.1:52625/v1", api_key="flm")

def read_file(path: str) -> str:
    with open(path, "r", encoding="utf-8") as f:
        return f.read()[:5000]  # 限制长度避免超长

def write_file(path: str, content: str) -> str:
    with open(path, "w", encoding="utf-8") as f:
        f.write(content)
    return f"已写入 {len(content)} 字符到 {path}"

def list_dir(path: str = ".") -> str:
    return "\n".join(os.listdir(path)[:50])

tools = [
    {"type": "function", "function": {
        "name": "read_file", "description": "读取文本文件内容",
        "parameters": {"type": "object", "properties": {"path": {"type": "string"}}, "required": ["path"]}
    }},
    {"type": "function", "function": {
        "name": "write_file", "description": "写入内容到文件",
        "parameters": {"type": "object", "properties": {"path": {"type": "string"}, "content": {"type": "string"}}, "required": ["path", "content"]}
    }},
    {"type": "function", "function": {
        "name": "list_dir", "description": "列出目录内容",
        "parameters": {"type": "object", "properties": {"path": {"type": "string", "default": "."}}}
    }},
]

tool_map = {"read_file": read_file, "write_file": write_file, "list_dir": list_dir}

def run_assistant(task: str):
    messages = [
        {"role": "system", "content": "你是文件管理助手，通过工具帮助用户操作文件。"},
        {"role": "user", "content": task}
    ]
    for _ in range(10):  # 最多 10 轮工具调用
        resp = client.chat.completions.create(model="qwen3:4b", messages=messages, tools=tools)
        msg = resp.choices[0].message
        messages.append(msg)
        if not msg.tool_calls:
            print(msg.content)
            break
        for tc in msg.tool_calls:
            args = json.loads(tc.function.arguments)
            result = tool_map[tc.function.name](**args)
            messages.append({"role": "tool", "tool_call_id": tc.id, "content": result})

run_assistant("查看当前目录有哪些文件，然后创建一个 hello.txt 写入'你好世界'")
```

#### 总结

本讲核心要点：工具调用通过 `tools` 参数定义可用函数，LLM 自主决定调用并生成参数，客户端执行后将结果以 `role: "tool"` 消息返回。`tool_choice="auto"` 让 LLM 自主选择，也可强制指定。工具的 `description` 质量决定调用准确率。常见注意事项：必须使用支持工具调用的模型（如 Qwen3，`flm list` 中 `TOOLS` 列为 `yes`）；工具参数的 JSON Schema 要定义清楚 `required` 字段；客户端需循环处理直到 `finish_reason` 不再是 `tool_calls`；工具执行可能失败，建议加 try-except 并将错误信息返回给 LLM 让其处理；限制最大循环次数避免无限调用。

---

### 第20讲：基于 LangChain 构建 RAG 系统

#### 概念

**RAG（Retrieval-Augmented Generation，检索增强生成）** 是让 LLM 基于私有知识回答问题的技术。它的工作流程是：将文档库切分为片段并向量化存入向量数据库；用户提问时，将问题向量化并检索最相关的文档片段；将检索到的片段作为上下文拼入 prompt，让 LLM 基于上下文回答。FastFlowLM 兼容 OpenAI API，因此可以直接使用 LangChain 框架构建 RAG 系统，FastFlowLM 同时承担嵌入模型和生成模型的角色。

#### 原理

RAG 解决的是 LLM 的**知识时效性和私有性问题**。模型训练后获得的知识是静态的，无法访问你的私有文档或最新信息。RAG 通过"先检索、后生成"的方式，将外部知识动态注入到推理过程中，相当于给 LLM 配了一个"开卷考试"的参考书。

RAG 系统的核心组件包括：**文档加载器**（读取 txt/pdf/docx 等）、**文本切分器**（将长文档切为适合检索的小片段，通常 500-1000 字符）、**嵌入模型**（将文本转为向量）、**向量数据库**（存储向量并支持相似度检索，如 Chroma、FAISS）、**生成模型**（基于检索结果生成回答）。

检索阶段使用**向量相似度**（通常是余弦相似度）找到与查询语义最相近的文档片段。为什么不用关键词匹配？因为语义相似不等于字面相同——"如何提高速度"和"性能优化方法"语义相近但无共同关键词，向量检索能捕捉这种语义关联。

生成阶段的 prompt 模板通常是：`"基于以下上下文回答问题。如果上下文中没有相关信息，请说'我不知道'。\n\n上下文：{retrieved_docs}\n\n问题：{question}"`。明确要求"基于上下文"和"不知道就说不知道"，能有效减少幻觉（hallucination）。

FastFlowLM 在 RAG 中的角色是双重的：作为嵌入模型（EmbeddingGemma）生成文档和查询的向量；作为生成模型（Qwen3 等）基于检索结果生成回答。由于两者都是本地 NPU 推理，整个 RAG 流程完全离线、隐私安全。

#### 例子

**安装依赖：**

```bash
pip install langchain langchain-openai chromadb
```

**完整的 RAG 系统：**

```python
# rag_system.py —— 基于 FastFlowLM 的本地 RAG 系统
from langchain_openai import OpenAIEmbeddings, ChatOpenAI
from langchain_community.vectorstores import Chroma
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain.schema import Document
from langchain.prompts import ChatPromptTemplate

# FastFlowLM 配置
BASE_URL = "http://127.0.0.1:52625/v1"
API_KEY = "flm"

# 注意：嵌入和生成需要分别启动 Server（不同端口）
# 终端1: flm serve embeddinggemma:300m --port 52626
# 终端2: flm serve qwen3:4b --port 52625

embeddings = OpenAIEmbeddings(
    base_url="http://127.0.0.1:52626/v1", api_key="flm",
    model="embeddinggemma:300m"
)
llm = ChatOpenAI(
    base_url="http://127.0.0.1:52625/v1", api_key="flm",
    model="qwen3:4b", temperature=0
)

# 第一步：准备文档
documents = [
    Document(page_content="FastFlowLM 是一款专为 AMD Ryzen AI NPU 设计的 LLM 推理运行时，体积仅 17MB。"),
    Document(page_content="FastFlowLM 支持 LLaMA、Qwen、Gemma 等主流模型家族，上下文窗口最高 256k tokens。"),
    Document(page_content="FastFlowLM 提供 OpenAI 兼容 API，默认端口 52625，API Key 为 'flm'。"),
    Document(page_content="FastFlowLM 的 NPU 推理比 GPU 节能 10 倍，适合移动设备本地部署。"),
    Document(page_content="使用 flm pull 命令下载模型，flm serve 命令启动 API 服务。"),
]

# 第二步：切分文档（本例文档较短，实际中文档可能很长）
splitter = RecursiveCharacterTextSplitter(chunk_size=200, chunk_overlap=20)
chunks = splitter.split_documents(documents)
print(f"切分为 {len(chunks)} 个片段")

# 第三步：向量化并存入 Chroma
vectorstore = Chroma.from_documents(chunks, embeddings, persist_directory="./flm_rag_db")
print("向量库构建完成")

# 第四步：创建检索器
retriever = vectorstore.as_retriever(search_kwargs={"k": 3})  # 检索 top3 相关片段

# 第五步：定义 RAG 链
template = """基于以下上下文回答问题。如果上下文中没有相关信息，请说"我不知道"。

上下文：
{context}

问题：{question}

回答："""
prompt = ChatPromptTemplate.from_template(template)

def rag_query(question: str) -> str:
    # 检索相关文档
    docs = retriever.invoke(question)
    context = "\n\n".join(d.page_content for d in docs)
    print(f"[检索到 {len(docs)} 个片段]")
    
    # 生成回答
    chain = prompt | llm
    response = chain.invoke({"context": context, "question": question})
    return response.content

# 测试
print(rag_query("FastFlowLM 支持哪些模型？"))
# [检索到 3 个片段]
# FastFlowLM 支持 LLaMA、Qwen、Gemma 等主流模型家族...

print(rag_query("FastFlowLM 的默认端口是多少？"))
# [检索到 3 个片段]
# FastFlowLM 的默认端口是 52625...

print(rag_query("如何用 Python 写快速排序？"))  # 知识库中无此信息
# [检索到 3 个片段]
# 我不知道。
```

**处理大型文档（如 PDF）：**

```python
# rag_with_pdf.py —— 处理 PDF 文档
from langchain_community.document_loaders import PyPDFLoader

# 加载 PDF
loader = PyPDFLoader("product_manual.pdf")
pages = loader.load()
print(f"加载 {len(pages)} 页")

# 切分（每页可能很长，需进一步切分）
splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,      # 每个片段约 500 字符
    chunk_overlap=50,    # 片段间重叠 50 字符，保证上下文连贯
    separators=["\n\n", "\n", "。", "，", " "]  # 中文友好的分隔符
)
chunks = splitter.split_documents(pages)
print(f"切分为 {len(chunks)} 个片段")

# 后续流程相同
vectorstore = Chroma.from_documents(chunks, embeddings, persist_directory="./pdf_rag_db")
```

**带来源引用的 RAG（增强可信度）：**

```python
def rag_query_with_sources(question: str):
    docs = retriever.invoke(question)
    context = "\n\n".join(f"[片段{i+1}] {d.page_content}" for i, d in enumerate(docs))
    
    template = """基于以下上下文回答问题，并在回答末尾标注引用了哪些片段编号。

上下文：
{context}

问题：{question}

回答（含引用）："""
    
    chain = ChatPromptTemplate.from_template(template) | llm
    response = chain.invoke({"context": context, "question": question})
    return response.content

print(rag_query_with_sources("FastFlowLM 有什么优势？"))
# FastFlowLM 的主要优势包括体积小（17MB）和节能（比GPU省10倍）[片段1][片段4]。
```

#### 总结

本讲核心要点：RAG 通过"检索+生成"让 LLM 基于私有知识回答，解决知识时效性和幻觉问题。核心组件包括文档加载、切分、嵌入、向量库、生成。FastFlowLM 可同时承担嵌入和生成角色，实现完全本地化的 RAG。常见注意事项：嵌入模型和生成模型需分端口启动（FastFlowLM 单模型常驻）；切分时 `chunk_overlap` 建议设为 chunk_size 的 10-20%，保证上下文连贯；中文文档的切分要指定中文分隔符（句号、逗号）；prompt 中明确要求"不知道就说不知道"能有效减少幻觉；向量库持久化（`persist_directory`）避免重复嵌入；检索 top_k 建议设 3-5，过多会稀释信号、增加 token 消耗。

---

### 第21讲：多模态输入——图像 + 文本联合推理

#### 概念

多模态输入是指 LLM 同时接收图像和文本，进行联合理解和推理。FastFlowLM 通过 Gemma 3 等视觉语言模型（VLM）支持这一能力。应用场景包括：图像描述、图表数据提取、文档 OCR 与理解、视觉问答（VQA）、代码截图转代码等。本讲将深入讲解多模态输入的 API 格式、图像预处理技巧，以及几个实用场景的完整实现。

#### 原理

多模态推理的关键在于**跨模态对齐**。VLM 内部包含一个视觉编码器（通常是 ViT，Vision Transformer）和一个语言模型。图像输入后，视觉编码器将其切分为多个 patch（如 16×16 像素的小块），每个 patch 经过线性投影成为一个 token 嵌入，这些视觉 token 与文本 token 拼接后送入语言模型的 Transformer 层，通过自注意力机制实现图文信息的交互。

图像在 API 中的传输采用 **Base64 编码**。原始图像（PNG/JPEG）是二进制数据，无法直接放在 JSON 中，因此先用 Base64 编码为文本字符串，再以 `data:image/png;base64,<编码内容>` 格式放入 `image_url` 字段。这种设计兼容 OpenAI API，但代价是 Base64 编码会使数据体积增大约 33%，大图像会显著增加请求耗时和 token 消耗。

图像的 token 消耗取决于分辨率。VLM 通常将图像缩放到固定尺寸（如 448×448 或 896×896），然后按 patch 切分。一张 896×896 的图像大约消耗 1000-2000 个视觉 token，相当于几百字文本。因此，**图像预处理很重要**——过大的图像应先缩放，过小的图像可能细节不足影响识别。

多模态推理的延迟主要来自视觉编码。图像的 Prefill 比等量文本 token 更慢（因为卷积和 patch 投影开销），因此首 token 延迟会明显高于纯文本。但一旦编码完成，后续的文本生成速度与普通对话相同。

#### 例子

**图像描述与视觉问答：**

```python
# vision_qa.py —— 视觉问答完整示例
import base64
from openai import OpenAI

client = OpenAI(base_url="http://127.0.0.1:52625/v1", api_key="flm")

def encode_image(path: str, max_size: int = 1024) -> str:
    """编码图像为 base64，过大时自动缩放"""
    from PIL import Image
    import io
    img = Image.open(path)
    # 缩放至最大边长 max_size
    if max(img.size) > max_size:
        ratio = max_size / max(img.size)
        img = img.resize((int(img.size[0]*ratio), int(img.size[1]*ratio)))
    buf = io.BytesIO()
    img.save(buf, format="PNG")
    return base64.b64encode(buf.getvalue()).decode()

def vision_qa(image_path: str, question: str) -> str:
    img_b64 = encode_image(image_path)
    response = client.chat.completions.create(
        model="gemma3:4b",
        messages=[{
            "role": "user",
            "content": [
                {"type": "text", "text": question},
                {"type": "image_url", "image_url": {"url": f"data:image/png;base64,{img_b64}"}}
            ]
        }],
        temperature=0
    )
    return response.choices[0].message.content

# 场景1：图像描述
print(vision_qa("photo.jpg", "详细描述这张图片的内容。"))
# 这张图片展示了一个山间的湖泊，远处是连绵的雪山...

# 场景2：图表数据提取
print(vision_qa("sales_chart.png", "提取图表中每个季度的销售额数据，以JSON格式返回。"))
# {"Q1": 120, "Q2": 165, "Q3": 210, "Q4": 280}

# 场景3：文档 OCR 与理解
print(vision_qa("invoice.jpg", "识别这张发票的金额、日期和购买方信息。"))
# 金额：￥3,580.00
# 日期：2024-12-15
# 购买方：XX科技有限公司

# 场景4：代码截图转代码
print(vision_qa("code_screenshot.png", "将图片中的代码转换为文本，保持格式。"))
# def quicksort(arr):
#     if len(arr) <= 1:
#         return arr
#     ...
```

**多图对比分析：**

```python
# multi_image.py —— 多张图像的对比分析
def compare_images(img_paths: list, question: str) -> str:
    content = [{"type": "text", "text": question}]
    for path in img_paths:
        content.append({
            "type": "image_url",
            "image_url": {"url": f"data:image/png;base64,{encode_image(path)}"}
        })
    response = client.chat.completions.create(
        model="gemma3:4b",
        messages=[{"role": "user", "content": content}]
    )
    return response.choices[0].message.content

# 对比两张设计稿
print(compare_images(
    ["design_v1.png", "design_v2.png"],
    "对比这两张UI设计稿，指出主要差异，并评价哪个用户体验更好。"
))
```

**带上下文的多轮视觉对话：**

```python
# vision_conversation.py —— 多轮视觉对话
def vision_conversation():
    messages = []
    while True:
        # 用户可以输入图片路径或文本
        user_input = input("你: ").strip()
        if user_input.lower() in ("quit", "q"):
            break
        
        content = []
        if user_input.startswith("img:"):
            # 格式: img:path/to/image.jpg 你的问题
            parts = user_input[4:].split(" ", 1)
            img_path = parts[0]
            question = parts[1] if len(parts) > 1 else "描述这张图片"
            content.append({"type": "text", "text": question})
            content.append({
                "type": "image_url",
                "image_url": {"url": f"data:image/png;base64,{encode_image(img_path)}"}
            })
        else:
            content.append({"type": "text", "text": user_input})
        
        messages.append({"role": "user", "content": content})
        
        response = client.chat.completions.create(
            model="gemma3:4b", messages=messages, stream=True
        )
        print("助手: ", end="", flush=True)
        reply = ""
        for chunk in response:
            delta = chunk.choices[0].delta.content
            if delta:
                print(delta, end="", flush=True)
                reply += delta
        print()
        messages.append({"role": "assistant", "content": reply})

# 运行
# 你: img:chart.png 这是什么类型的图表？
# 助手: 这是一个柱状图，展示了...
# 你: 哪个柱子最高？
# 助手: Q4 的柱子最高，表示...
```

**实用场景——批量图像标注：**

```python
# batch_caption.py —— 批量为图像生成描述
import os

def batch_caption(image_dir: str, output_file: str):
    results = []
    for fname in os.listdir(image_dir):
        if not fname.lower().endswith((".png", ".jpg", ".jpeg")):
            continue
        path = os.path.join(image_dir, fname)
        caption = vision_qa(path, "用一句话描述这张图片，用于图片搜索索引。")
        results.append({"file": fname, "caption": caption})
        print(f"{fname}: {caption}")
    
    import json
    with open(output_file, "w", encoding="utf-8") as f:
        json.dump(results, f, ensure_ascii=False, indent=2)

batch_caption("./images", "captions.json")
```

#### 总结

本讲核心要点：多模态输入通过 `content` 数组同时传入 `text` 和 `image_url`（base64 编码）实现。VLM 内部通过视觉编码器将图像转为 token 与文本联合推理。应用场景包括图像描述、OCR、图表提取、视觉问答等。常见注意事项：大图像务必预处理缩放（建议最大边 1024px），否则 token 消耗巨大且速度慢；多模态推理的首 token 延迟高于纯文本（视觉编码耗时）；`temperature=0` 适合 OCR 和数据提取等需要准确性的任务；多图对比时图片顺序会影响分析，建议在 prompt 中明确编号；Gemma 3 4B 适合常见场景，复杂图像分析（如医学影像）可能需要更大模型或专用模型。

---

### 第22讲：批量推理与长文档处理

#### 概念

**批量推理**是指高效处理大量请求或长文本的技术。在本地 NPU 场景，批量推理的瓶颈不是网络而是 NPU 的串行处理能力——一次只能处理一个请求。因此批量优化的核心是**减少每个请求的开销**（避免重复加载、复用上下文）和**合理切分长文本**。本讲讲解批量对话、长文档分块总结、Map-Reduce 式文档处理等模式，帮助你高效处理真实业务场景。

#### 原理

NPU 批量推理与 GPU 有本质不同。GPU 推理可以通过 batch 维度并行处理多个请求（一次前向传播处理 batch_size 个输入），而 NPU 的算力资源是串行的，无法真正并行。因此 FastFlowLM 的"批量"更多是指**串行高效处理**——减少每个请求的固定开销（如连接建立、模型加载），而非并行加速。

批量推理的优化策略主要有三种。**第一是连接复用**：使用同一个 OpenAI client 实例发送所有请求，避免重复建立 HTTP 连接。**第二是上下文复用**：如果多个请求共享相同的 system prompt 或前缀，可以将其作为固定前缀，减少重复 Prefill 计算（虽然 FastFlowLM 目前不显式缓存前缀，但保持消息结构一致有助于稳定）。**第三是异步流水线**：用 `AsyncOpenAI` 发送请求，虽然 NPU 串行处理，但 HTTP 层面的异步可以重叠网络等待和推理时间。

长文档处理的核心是**分块策略**。当文档超过模型上下文长度（如 4096 token）时，必须切分为多个片段分别处理。常见模式有：**Map 模式**（每个片段独立处理，如分别总结）、**Map-Reduce 模式**（先 Map 分别处理，再 Reduce 合并结果）、**滑动窗口模式**（片段间重叠，保持上下文连贯）。选择哪种取决于任务——总结适合 Map-Reduce，问答适合滑动窗口+RAG，翻译适合滑动窗口。

一个常被忽视的优化是**温度策略**。批量处理中，不同任务应使用不同温度：信息提取、分类、总结用 `temperature=0`（确定性、可复现）；创意写作、改写用 `temperature=0.7`。批量场景下低温度还能避免输出不稳定导致的解析失败。

#### 例子

**批量对话处理（连接复用）：**

```python
# batch_chat.py —— 批量处理多个对话请求
from openai import OpenAI

client = OpenAI(base_url="http://127.0.0.1:52625/v1", api_key="flm")  # 复用连接

def batch_chat(prompts: list[str], system: str = "你是简洁的助手") -> list[str]:
    results = []
    for i, prompt in enumerate(prompts):
        response = client.chat.completions.create(
            model="qwen3:4b",
            messages=[
                {"role": "system", "content": system},
                {"role": "user", "content": prompt}
            ],
            temperature=0  # 批量场景用低温度保证稳定
        )
        results.append(response.choices[0].message.content)
        print(f"[{i+1}/{len(prompts)}] 完成")
    return results

# 批量生成产品描述
products = ["无线鼠标", "机械键盘", "4K显示器", "USB-C扩展坞", "降噪耳机"]
descriptions = batch_chat([f"为{p}写一句20字以内的广告语" for p in products])
for p, d in zip(products, descriptions):
    print(f"{p}: {d}")
```

**长文档分块总结（Map-Reduce）：**

```python
# long_doc_summary.py —— 长文档分块总结
from langchain.text_splitter import RecursiveCharacterTextSplitter

client = OpenAI(base_url="http://127.0.0.1:52625/v1", api_key="flm")

def summarize(text: str) -> str:
    response = client.chat.completions.create(
        model="qwen3:4b",
        messages=[
            {"role": "system", "content": "用3句话总结以下文本的要点："},
            {"role": "user", "content": text}
        ],
        temperature=0, max_tokens=200
    )
    return response.choices[0].message.content

def long_doc_summary(text: str, chunk_size: int = 2000) -> str:
    # 如果文本不长，直接总结
    if len(text) <= chunk_size:
        return summarize(text)
    
    # Map 阶段：切分并分别总结
    splitter = RecursiveCharacterTextSplitter(
        chunk_size=chunk_size, chunk_overlap=100
    )
    chunks = splitter.split_text(text)
    print(f"文档切分为 {len(chunks)} 块")
    
    chunk_summaries = []
    for i, chunk in enumerate(chunks):
        s = summarize(chunk)
        chunk_summaries.append(s)
        print(f"  块 {i+1} 总结完成")
    
    # Reduce 阶段：合并各块总结
    combined = "\n\n".join(chunk_summaries)
    final = summarize(combined)
    return final

# 测试：总结一篇长文章
with open("long_article.txt", "r", encoding="utf-8") as f:
    article = f.read()
print(f"原文长度: {len(article)} 字符")
print(f"总结: {long_doc_summary(article)}")
```

**批量分类任务：**

```python
# batch_classify.py —— 批量文本分类
def batch_classify(texts: list[str], categories: list[str]) -> list[str]:
    cat_str = "、".join(categories)
    results = []
    for text in texts:
        response = client.chat.completions.create(
            model="qwen3:4b",
            messages=[{
                "role": "user",
                "content": f"将以下文本分类到其中一个类别（{cat_str}），只返回类别名，不要其他内容。\n\n文本：{text}"
            }],
            temperature=0, max_tokens=20
        )
        result = response.choices[0].message.content.strip()
        results.append(result)
    return results

# 批量情感分类
reviews = [
    "这个产品太棒了，强烈推荐！",
    "质量一般，不值这个价。",
    "包装破损，客服态度差。",
    "性价比很高，会回购。",
]
sentiments = batch_classify(reviews, ["正面", "负面", "中性"])
for r, s in zip(reviews, sentiments):
    print(f"[{s}] {r}")
```

**异步批量处理（重叠等待时间）：**

```python
# async_batch.py —— 异步批量推理
import asyncio
from openai import AsyncOpenAI

async_client = AsyncOpenAI(base_url="http://127.0.0.1:52625/v1", api_key="flm")

async def process_one(prompt: str) -> str:
    response = await async_client.chat.completions.create(
        model="qwen3:4b",
        messages=[{"role": "user", "content": prompt}],
        temperature=0
    )
    return response.choices[0].message.content

async def batch_process(prompts: list[str]) -> list[str]:
    # 异步发送所有请求（NPU 串行处理，但 HTTP 层重叠）
    tasks = [process_one(p) for p in prompts]
    return await asyncio.gather(*tasks)

# 处理 20 个请求
prompts = [f"写一句关于'话题{i}'的感悟" for i in range(20)]
results = asyncio.run(batch_process(prompts))
print(f"处理完成 {len(results)} 条")
```

**进度保存与断点续传（长批量任务）：**

```python
# resumable_batch.py —— 可断点续传的批量处理
import json, os

def resumable_batch(tasks: list[dict], output_file: str = "progress.json"):
    # 加载已完成的结果
    done = {}
    if os.path.exists(output_file):
        with open(output_file) as f:
            done = {item["id"]: item["result"] for item in json.load(f)}
    
    results = list(done.items())
    for i, task in enumerate(tasks):
        if task["id"] in done:
            continue  # 跳过已完成
        result = process_one(task["prompt"])
        results.append((task["id"], result))
        
        # 每完成一个就保存（防止中断丢失）
        with open(output_file, "w") as f:
            json.dump([{"id": k, "result": v} for k, v in results], f, ensure_ascii=False)
        print(f"[{i+1}/{len(tasks)}] {task['id']} 完成")
    
    return dict(results)

# 即使中途中断，重新运行会从断点继续
```

#### 总结

本讲核心要点：NPU 批量推理是串行高效处理，优化重点是连接复用、合理分块、异步流水线。长文档处理用 Map-Reduce（总结）或滑动窗口（翻译）。批量场景建议 `temperature=0` 保证稳定。常见注意事项：批量任务务必实现进度保存和断点续传，避免中断后从头重来；长文档分块时 `chunk_overlap` 设 5-10% 保证连贯；Map-Reduce 的 Reduce 阶段如果合并后仍超长，可递归 Reduce；异步批量不会真正加速 NPU 推理，但能重叠 HTTP 等待；监控每个请求的 `usage`，避免 token 超限；批量分类任务建议在 prompt 中限定输出格式（如"只返回类别名"），便于程序解析。

---

## 第7章 高级实战与优化

本章是课程的收官部分，聚焦于真实生产场景中的关键问题。我们将讲解性能调优（如何榨干 NPU 算力）、隐私安全与离线部署（如何确保数据不出本机）、以及一个综合实战项目（构建本地 AI 知识助手）。完成本章后，你将具备将 FastFlowLM 从"能跑"提升到"好用"的完整能力，能够独立设计和交付基于 NPU 的本地 AI 应用。这三讲的内容是前面所有章节知识的综合运用，建议结合实际项目反复实践。

### 第23讲：性能调优——NPU 功耗模式与上下文策略

#### 概念

性能调优是在效果、速度、功耗三者间寻找最佳平衡的过程。FastFlowLM 的性能调优主要围绕三个维度：**NPU 功耗模式**（powersaver/balanced/turbo，决定 NPU 工作频率）、**上下文长度策略**（ctx-len 和 prefill-chunk-len，决定内存占用和首 token 延迟）、**请求调度策略**（preemption 抢占机制，决定多请求场景的响应性）。理解这些参数对性能的影响机制，才能针对具体场景做出最优配置。

#### 原理

NPU 性能的本质是**算力、内存带宽和功耗的三角约束**。XDNA2 NPU 的峰值算力约 50 INT8 TOPS，专用内存带宽约 256 GB/s，但这些都受功耗上限制约。`turbo` 模式下 NPU 拉满频率，算力最高但功耗可达 30W+；`powersaver` 模式降频运行，算力降低但功耗仅 8W 左右。这种差异在移动设备上尤为关键——turbo 模式下笔记本续航可能从 6 小时降到 2 小时。

上下文长度对性能的影响是**非线性的**。Transformer 的注意力机制复杂度为 O(n²)，因此上下文从 4k 增加到 32k，Prefill 计算量增加 64 倍。更关键的是 **KV Cache 内存占用**——每个 token 在 KV Cache 中占用约 32-128 字节（取决于模型层数和头数），32k 上下文可能占用 2-4 GB NPU 内存。当内存接近上限时，系统会频繁在 NPU 内存和系统内存间搬运数据，导致性能急剧下降。

Prefill 分块（`--prefill-chunk-len`）是应对长上下文的关键优化。它将长 prompt 的 Prefill 拆分为多个小块依次计算，降低单次计算的内存峰值。但分块不是免费的——每块之间需要保存中间状态，增加了少量开销。因此分块大小需要权衡：太大导致内存峰值高，太小导致分块开销大。经验值是 2048（默认）适合中等上下文，4096 适合长上下文且内存充足。

请求调度方面，`--preemption`（默认开启）允许 NPU 在处理长请求时被短请求"抢占"。例如，一个长文档总结任务正在生成（可能需要 30 秒），此时来了一个简短问答请求，preemption 会暂停总结任务、先处理问答（2 秒）、再恢复总结。这保证了交互式场景的响应性，代价是切换有少量开销。对于纯批量场景（不需要交互），可以关闭 preemption 以获得最高吞吐。

#### 例子

**性能基准测试脚本：**

```python
# benchmark.py —— 性能基准测试
import time
from openai import OpenAI

client = OpenAI(base_url="http://127.0.0.1:52625/v1", api_key="flm")

def benchmark(prompt: str, max_tokens: int = 200, runs: int = 3):
    """测试推理性能，返回平均速度"""
    speeds = []
    for _ in range(runs):
        start = time.time()
        response = client.chat.completions.create(
            model="qwen3:4b",
            messages=[{"role": "user", "content": prompt}],
            max_tokens=max_tokens,
            stream=True
        )
        first_token_time = None
        token_count = 0
        for chunk in response:
            if chunk.choices[0].delta.content:
                if first_token_time is None:
                    first_token_time = time.time() - start
                token_count += 1
        total_time = time.time() - start
        decode_speed = token_count / (total_time - first_token_time) if first_token_time else 0
        speeds.append({
            "first_token": first_token_time,
            "total": total_time,
            "tokens": token_count,
            "decode_speed": decode_speed
        })
    
    avg = {k: sum(s[k] for s in speeds)/len(speeds) for k in speeds[0]}
    print(f"首 token 延迟: {avg['first_token']:.2f}s")
    print(f"总耗时: {avg['total']:.2f}s")
    print(f"生成 token 数: {avg['tokens']:.0f}")
    print(f"解码速度: {avg['decode_speed']:.1f} tok/s")
    return avg

# 测试短 prompt
print("=== 短 prompt 测试 ===")
benchmark("写一首关于春天的诗", max_tokens=100)

# 测试长 prompt（观察 Prefill 影响）
print("\n=== 长 prompt 测试 ===")
long_prompt = "请总结以下文本：\n" + "人工智能是计算机科学的一个分支。" * 500
benchmark(long_prompt, max_tokens=100)
```

**不同功耗模式对比：**

```bash
# 分别启动三种模式并运行 benchmark.py

# 1. Powersaver 模式（省电）
flm serve qwen3:4b --pmode powersaver --port 52625
# 运行 benchmark.py:
# 首 token 延迟: 0.85s
# 解码速度: 23.5 tok/s
# NPU 功耗: ~8W

# 2. Balanced 模式（默认）
flm serve qwen3:4b --pmode balanced --port 52625
# 首 token 延迟: 0.52s
# 解码速度: 38.7 tok/s
# NPU 功耗: ~15W

# 3. Turbo 模式（极速）
flm serve qwen3:4b --pmode turbo --port 52625
# 首 token 延迟: 0.38s
# 解码速度: 52.6 tok/s
# NPU 功耗: ~28W
```

**上下文长度对内存的影响：**

```bash
# 4k 上下文（内存占用小）
flm serve qwen3:4b --ctx-len 4096 --port 52625
# NPU 内存占用: ~5.2 GB

# 32k 上下文（内存占用中等）
flm serve qwen3:4b --ctx-len 32768 --port 52625
# NPU 内存占用: ~8.1 GB

# 128k 上下文（内存占用大）
flm serve qwen3:4b --ctx-len 131072 --port 52625
# NPU 内存占用: ~14.5 GB（接近 16GB 上限）
```

**场景化配置推荐：**

```bash
# 场景1：移动办公（电池供电）
flm serve qwen3:4b \
  --ctx-len 4096 \
  --pmode powersaver \
  --prefill-chunk-len 1024 \
  --port 52625
# 优先续航，速度可接受

# 场景2：桌面代码助手（插电）
flm serve qwen3:4b \
  --ctx-len 32768 \
  --pmode turbo \
  --prefill-chunk-len 4096 \
  --port 52625
# 优先速度和上下文长度

# 场景3：长文档处理（批量）
flm serve qwen3:4b \
  --ctx-len 65536 \
  --pmode balanced \
  --prefill-chunk-len 4096 \
  --preemption=false \
  --port 52625
# 关闭抢占，最大化批量吞吐

# 场景4：交互式问答（响应优先）
flm serve qwen3:4b \
  --ctx-len 8192 \
  --pmode balanced \
  --prefill-chunk-len 2048 \
  --preemption=true \
  --port 52625
# 开启抢占，保证短请求快速响应
```

**监控 NPU 状态（实时性能观察）：**

```python
# monitor.py —— 实时监控推理性能
import time
from openai import OpenAI

client = OpenAI(base_url="http://127.0.0.1:52625/v1", api_key="flm")

def monitored_chat(prompt: str):
    start = time.time()
    stream = client.chat.completions.create(
        model="qwen3:4b",
        messages=[{"role": "user", "content": prompt}],
        stream=True
    )
    
    tokens = 0
    first_token = None
    last_time = start
    
    print("回复: ", end="", flush=True)
    for chunk in stream:
        content = chunk.choices[0].delta.content
        if content:
            now = time.time()
            if first_token is None:
                first_token = now - start
                print(f"\n[首token: {first_token:.2f}s]", end="")
            tokens += 1
            last_time = now
            print(content, end="", flush=True)
    
    total = time.time() - start
    decode_time = total - first_token if first_token else 0
    print(f"\n--- 性能统计 ---")
    print(f"总 token: {tokens}")
    print(f"首 token 延迟: {first_token:.2f}s")
    print(f"解码速度: {tokens/decode_time:.1f} tok/s" if decode_time > 0 else "N/A")
    print(f"总耗时: {total:.2f}s")

monitored_chat("解释什么是 Transformer 架构，详细说明。")
```

#### 总结

本讲核心要点：性能调优围绕功耗模式（powersaver/balanced/turbo）、上下文长度（ctx-len）、分块大小（prefill-chunk-len）、调度策略（preemption）四个维度。turbo 速度最快但功耗高，powersaver 省电但速度慢。上下文越长内存占用越大且 Prefill 越慢。常见注意事项：移动场景务必用 powersaver，否则续航骤降；长上下文（>32k）需确保 NPU 内存充足，否则性能急剧下降；批量场景关闭 preemption 提升吞吐，交互场景开启保证响应；建议用 benchmark 脚本量化不同配置的实际效果，而非凭感觉调参；turbo 模式下注意散热，长时间高负载可能导致 NPU 降频保护。

---

### 第24讲：隐私、安全与离线部署

#### 概念

FastFlowLM 最大的价值之一是**数据完全本地化**——所有推理在 NPU 上完成，数据不出本机，不依赖云端。这对于处理敏感数据（医疗、金融、法律、个人隐私）的场景至关重要。本讲讲解 FastFlowLM 的隐私保障机制、网络安全配置（避免服务暴露）、离线部署方案（完全无网络运行），以及访问控制策略，帮助你构建安全可靠的本地 AI 系统。

#### 原理

FastFlowLM 的隐私保障是**架构性的**，而非附加功能。由于模型和推理引擎都运行在本地 NPU 上，用户数据（prompt、上下文、生成内容）从不离开本机内存。这与云端 API（如 OpenAI、Anthropic）形成根本区别——云端服务可能记录、存储甚至用你的数据训练模型（取决于服务条款），而 FastFlowLM 连网络请求都不需要发起（模型下载完成后）。

但"本地运行"不等于"绝对安全"，仍需注意几个风险点。**第一是网络暴露风险**：默认 `flm serve` 监听 `127.0.0.1`（仅本机），但如果配置 `--host 0.0.0.0` 且无防火墙，局域网内任何人都能调用你的模型服务，间接访问你的 NPU 资源。**第二是模型文件安全**：模型文件存储在本地磁盘，如果设备被物理访问或被恶意软件感染，模型文件可能被篡改（如植入后门）。**第三是日志泄露**：调试时打印的对话内容可能被日志系统记录，需注意清理。

离线部署的关键是**预下载所有依赖**。FastFlowLM 运行时本身是自包含的（不需要运行时下载），但模型文件需要提前在有网络时下载。一旦模型就位，整个系统可以完全离线运行——不联网、不打电话回家、不上报遥测。FastFlowLM 默认会在启动时检查更新（联网行为），可通过 `FLM_DISABLE_UPDATE_CHECK=1` 禁用，实现真正的离线运行。

访问控制方面，FastFlowLM 默认的 API Key `"flm"` 不做真实鉴权（任何字符串都通过），这在本地单用户场景没问题。但如果需要多用户共享或部署在半信任网络，建议在前面加一层反向代理（如 Nginx）实现鉴权和速率限制。

#### 例子

**完全离线部署流程：**

```bash
# === 在有网络的机器上准备 ===

# 步骤1：安装 FastFlowLM 并下载所有需要的模型
flm pull qwen3:4b
flm pull embeddinggemma:300m
flm pull gemma3:4b

# 步骤2：打包 FastFlowLM 安装目录和模型目录
# Windows:
# 压缩 C:\Users\<用户名>\Documents\flm 为 flm-offline.zip
# Linux:
tar -czf flm-offline.tar.gz -C ~/.local/share/flm .
tar -czf flm-models.tar.gz -C ~/.config/flm/models .

# 步骤3：导出安装脚本（如果需要）
# 保存 install.sh 或安装包
```

```bash
# === 在离线机器上部署 ===

# 步骤1：解压 FastFlowLM
# Windows: 解压 flm-offline.zip 到 C:\Users\<用户名>\Documents\flm
# Linux:
mkdir -p ~/.local/share/flm
tar -xzf flm-offline.tar.gz -C ~/.local/share/flm
ln -s ~/.local/share/flm/flm ~/.local/bin/flm

# 步骤2：解压模型文件
mkdir -p ~/.config/flm/models
tar -xzf flm-models.tar.gz -C ~/.config/flm/models

# 步骤3：配置离线环境变量
echo 'export FLM_DISABLE_UPDATE_CHECK=1' >> ~/.bashrc
source ~/.bashrc

# 步骤4：验证离线运行（断开网络后）
flm check    # 应显示 NPU 正常
flm list     # 应显示已导入的模型
flm run qwen3:4b   # 应能正常对话
```

**网络安全配置：**

```bash
# 安全配置1：仅本机访问（默认，最安全）
flm serve qwen3:4b --host 127.0.0.1 --port 52625

# 安全配置2：局域网访问 + 防火墙限制
flm serve qwen3:4b --host 0.0.0.0 --port 52625
# 配置防火墙只允许特定 IP 访问
sudo ufw allow from 192.168.1.100 to any port 52625
sudo ufw deny 52625

# 安全配置3：通过 Nginx 反向代理 + 鉴权（生产推荐）
# nginx.conf 片段：
# location /flm/ {
#     proxy_pass http://127.0.0.1:52625/v1/;
#     # API Key 鉴权
#     if ($http_authorization != "Bearer your-secret-key") {
#         return 401;
#     }
#     # 速率限制
#     limit_req zone=flm burst=10 nodelay;
# }
```

**Python 客户端的安全调用封装：**

```python
# secure_client.py —— 带安全措施的客户端封装
import os
from openai import OpenAI

class SecureFLMClient:
    """安全的 FastFlowLM 客户端封装"""
    
    def __init__(self, base_url: str = None, api_key: str = None):
        # 从环境变量读取配置，避免硬编码
        self.base_url = base_url or os.getenv("FLM_BASE_URL", "http://127.0.0.1:52625/v1")
        self.api_key = api_key or os.getenv("FLM_API_KEY", "flm")
        self.client = OpenAI(base_url=self.base_url, api_key=self.api_key, timeout=60.0)
    
    def chat(self, prompt: str, sanitize: bool = True) -> str:
        """安全对话，可选输入净化"""
        if sanitize:
            # 基本的输入净化（防止 prompt 注入的简单措施）
            prompt = self._sanitize_input(prompt)
        
        response = self.client.chat.completions.create(
            model=os.getenv("FLM_MODEL", "qwen3:4b"),
            messages=[{"role": "user", "content": prompt}]
        )
        return response.choices[0].message.content
    
    def _sanitize_input(self, text: str) -> str:
        """简单的输入净化"""
        # 限制长度
        if len(text) > 10000:
            text = text[:10000]
        # 移除可能的控制字符
        import re
        text = re.sub(r'[\x00-\x08\x0b\x0c\x0e-\x1f]', '', text)
        return text

# 使用（配置在环境变量中，不硬编码）
# export FLM_API_KEY="your-secret-key"
# export FLM_MODEL="qwen3:4b"
client = SecureFLMClient()
print(client.chat("你好"))
```

**日志安全（避免对话内容泄露）：**

```python
# safe_logging.py —— 避免记录敏感对话内容
import logging

# 配置日志：不记录消息内容
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)
logger = logging.getLogger(__name__)

def safe_chat(client, prompt: str) -> str:
    """记录元数据但不记录内容"""
    logger.info(f"推理请求 - prompt长度: {len(prompt)}")
    
    response = client.chat.completions.create(
        model="qwen3:4b",
        messages=[{"role": "user", "content": prompt}]
    )
    result = response.choices[0].message.content
    
    # 只记录长度和 token 数，不记录内容
    logger.info(f"推理完成 - 回复长度: {len(result)}, tokens: {response.usage.total_tokens}")
    return result
```

**数据隔离（多用户场景）：**

```python
# isolated_sessions.py —— 多用户会话隔离
import uuid
from openai import OpenAI

client = OpenAI(base_url="http://127.0.0.1:52625/v1", api_key="flm")

class SessionManager:
    """每个用户的对话历史独立存储，互不干扰"""
    
    def __init__(self):
        self.sessions = {}  # 实际项目用数据库或 Redis
    
    def create_session(self, user_id: str) -> str:
        session_id = str(uuid.uuid4())
        self.sessions[session_id] = {
            "user_id": user_id,
            "messages": [{"role": "system", "content": "你是个人助手。"}]
        }
        return session_id
    
    def chat(self, session_id: str, user_input: str) -> str:
        if session_id not in self.sessions:
            raise ValueError("无效会话")
        
        session = self.sessions[session_id]
        session["messages"].append({"role": "user", "content": user_input})
        
        response = client.chat.completions.create(
            model="qwen3:4b",
            messages=session["messages"]
        )
        reply = response.choices[0].message.content
        session["messages"].append({"role": "assistant", "content": reply})
        return reply
    
    def delete_session(self, session_id: str):
        """删除会话（清理敏感数据）"""
        self.sessions.pop(session_id, None)

# 使用
mgr = SessionManager()
sid = mgr.create_session("user_001")
print(mgr.chat(sid, "我叫张三"))
mgr.delete_session(sid)  # 清理数据
```

#### 总结

本讲核心要点：FastFlowLM 的隐私保障是架构性的（数据不出本机），但仍需注意网络暴露、模型文件安全、日志泄露三个风险点。离线部署需预下载模型并设置 `FLM_DISABLE_UPDATE_CHECK=1`。生产环境建议用 Nginx 反向代理加鉴权。常见注意事项：默认 `--host 127.0.0.1` 最安全，改 `0.0.0.0` 必须配合防火墙；API Key `"flm"` 不做真实鉴权，多用户场景需自建鉴权层；日志只记录元数据（长度、token 数），不记录对话内容；多用户场景务必做会话隔离，避免历史泄露；模型文件定期用 `flm validate` 校验完整性，防止篡改；离线环境确认无任何网络请求后再投入使用。

---

### 第25讲：综合实战——构建本地 AI 知识助手

#### 概念

本讲是全课程的综合实战项目——构建一个**本地 AI 知识助手**。它整合了前面所有章节的知识：用 FastFlowLM 作为推理引擎，结合 RAG（检索增强生成）实现私有知识问答，支持工具调用（搜索、计算、文件操作），具备多轮对话能力，并包含完整的命令行交互界面。这个项目可以直接作为个人知识管理工具使用，也是你掌握 FastFlowLM 的最佳证明。

#### 原理

本地 AI 知识助手的架构分为四层。**知识层**：用户的文档库（PDF、Markdown、TXT 等）被切分、向量化后存入本地向量数据库，作为 RAG 的知识源。**推理层**：FastFlowLM Server 提供嵌入（EmbeddingGemma）和生成（Qwen3）两类模型服务。**能力层**：通过工具调用机制扩展 LLM 的能力，包括知识库检索、网络搜索（可选）、计算、文件读写等。**交互层**：命令行界面接收用户输入，协调各层完成问答。

工作流程是：用户提问 → LLM 判断是否需要检索知识库 → 若需要，调用检索工具获取相关文档片段 → LLM 基于检索结果和对话历史生成回答 → 若涉及计算或文件操作，调用相应工具 → 返回最终回复。整个过程中，所有数据都在本地处理，隐私安全。

这个项目的设计要点是**模块化和可扩展**。知识库、工具集、模型配置都是独立模块，可以单独替换或扩展。例如，知识库可以从 Chroma 换成 FAISS，工具可以随时新增，模型可以升级为更大版本。这种设计保证了项目的可维护性和成长性。

#### 例子

**完整项目代码：**

```python
# knowledge_assistant.py —— 本地 AI 知识助手
"""
功能：
1. 基于本地文档库的 RAG 问答
2. 多轮对话（保持上下文）
3. 工具调用（计算、文件操作、知识检索）
4. 流式输出
5. 会话管理（保存/加载）

依赖：pip install openai chromadb langchain
"""

import os
import json
import base64
from datetime import datetime
from openai import OpenAI
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_community.vectorstores import Chroma
from langchain_openai import OpenAIEmbeddings

# ============ 配置 ============
EMBEDDING_URL = "http://127.0.0.1:52626/v1"  # 嵌入模型服务
LLM_URL = "http://127.0.0.1:52625/v1"        # 生成模型服务
API_KEY = "flm"
EMBEDDING_MODEL = "embeddinggemma:300m"
LLM_MODEL = "qwen3:4b"
KNOWLEDGE_DIR = "./knowledge"          # 文档库目录
DB_DIR = "./assistant_db"              # 向量数据库目录
HISTORY_DIR = "./assistant_history"    # 对话历史目录

os.makedirs(KNOWLEDGE_DIR, exist_ok=True)
os.makedirs(HISTORY_DIR, exist_ok=True)

# ============ 初始化客户端 ============
embeddings = OpenAIEmbeddings(base_url=EMBEDDING_URL, api_key=API_KEY, model=EMBEDDING_MODEL)
llm_client = OpenAI(base_url=LLM_URL, api_key=API_KEY)

# ============ 知识库管理 ============
class KnowledgeBase:
    """管理本地文档库的加载、切分、向量化、检索"""
    
    def __init__(self):
        self.splitter = RecursiveCharacterTextSplitter(
            chunk_size=500, chunk_overlap=50,
            separators=["\n\n", "\n", "。", "，", " "]
        )
        self.vectorstore = None
    
    def load_documents(self):
        """加载知识目录下的所有文档"""
        documents = []
        for fname in os.listdir(KNOWLEDGE_DIR):
            path = os.path.join(KNOWLEDGE_DIR, fname)
            if fname.endswith((".txt", ".md")):
                with open(path, "r", encoding="utf-8") as f:
                    documents.append({"content": f.read(), "source": fname})
        return documents
    
    def build_index(self):
        """构建或加载向量索引"""
        if os.path.exists(DB_DIR):
            self.vectorstore = Chroma(persist_directory=DB_DIR, embedding_function=embeddings)
            print(f"[知识库] 已加载现有索引")
        else:
            docs = self.load_documents()
            if not docs:
                print(f"[知识库] {KNOWLEDGE_DIR} 中无文档，请添加 .txt 或 .md 文件")
                self.vectorstore = None
                return
            
            chunks = []
            for doc in docs:
                splits = self.splitter.split_text(doc["content"])
                for s in splits:
                    chunks.append({"content": s, "source": doc["source"]})
            
            from langchain.schema import Document
            langchain_docs = [Document(page_content=c["content"], metadata={"source": c["source"]}) for c in chunks]
            self.vectorstore = Chroma.from_documents(langchain_docs, embeddings, persist_directory=DB_DIR)
            print(f"[知识库] 索引构建完成：{len(docs)} 个文档，{len(chunks)} 个片段")
    
    def search(self, query: str, k: int = 3) -> str:
        """检索与查询相关的文档片段"""
        if not self.vectorstore:
            return "知识库为空"
        results = self.vectorstore.similarity_search(query, k=k)
        return "\n\n---\n\n".join(f"[来源: {r.metadata['source']}]\n{r.page_content}" for r in results)

# ============ 工具定义 ============
kb = KnowledgeBase()

def search_knowledge(query: str) -> str:
    """搜索本地知识库"""
    return kb.search(query)

def calculate(expression: str) -> str:
    """计算数学表达式"""
    try:
        allowed = set("0123456789+-*/.() ")
        if all(c in allowed for c in expression):
            return str(eval(expression))
        return "包含非法字符"
    except Exception as e:
        return f"错误: {e}"

def read_file(path: str) -> str:
    """读取文件内容"""
    try:
        with open(path, "r", encoding="utf-8") as f:
            return f.read()[:3000]
    except Exception as e:
        return f"读取失败: {e}"

def get_current_time() -> str:
    """获取当前时间"""
    return datetime.now().strftime("%Y-%m-%d %H:%M:%S")

tools = [
    {"type": "function", "function": {
        "name": "search_knowledge", "description": "搜索本地知识库中的相关文档",
        "parameters": {"type": "object", "properties": {"query": {"type": "string", "description": "搜索关键词"}}, "required": ["query"]}
    }},
    {"type": "function", "function": {
        "name": "calculate", "description": "计算数学表达式",
        "parameters": {"type": "object", "properties": {"expression": {"type": "string"}}, "required": ["expression"]}
    }},
    {"type": "function", "function": {
        "name": "read_file", "description": "读取本地文本文件",
        "parameters": {"type": "object", "properties": {"path": {"type": "string"}}, "required": ["path"]}
    }},
    {"type": "function", "function": {
        "name": "get_current_time", "description": "获取当前日期和时间",
        "parameters": {"type": "object", "properties": {}}
    }},
]
tool_map = {"search_knowledge": search_knowledge, "calculate": calculate, 
            "read_file": read_file, "get_current_time": get_current_time}

# ============ 助手主类 ============
class KnowledgeAssistant:
    def __init__(self):
        self.messages = [{"role": "system", "content": 
            "你是本地 AI 知识助手。回答问题时优先使用 search_knowledge 工具检索知识库。"
            "如果知识库中没有相关信息，坦诚告知并基于自身知识回答。"
            "涉及计算时使用 calculate 工具，确保结果准确。"}]
    
    def chat(self, user_input: str) -> str:
        self.messages.append({"role": "user", "content": user_input})
        
        for _ in range(8):  # 最多 8 轮工具调用
            response = llm_client.chat.completions.create(
                model=LLM_MODEL, messages=self.messages, tools=tools, stream=False
            )
            msg = response.choices[0].message
            self.messages.append(msg)
            
            if not msg.tool_calls:
                return msg.content
            
            for tc in msg.tool_calls:
                name = tc.function.name
                args = json.loads(tc.function.arguments)
                print(f"  [工具] {name}({args})")
                result = tool_map[name](**args)
                self.messages.append({"role": "tool", "tool_call_id": tc.id, "content": str(result)})
        
        return "（工具调用次数超限）"
    
    def chat_stream(self, user_input: str):
        """流式版本"""
        self.messages.append({"role": "user", "content": user_input})
        
        while True:
            response = llm_client.chat.completions.create(
                model=LLM_MODEL, messages=self.messages, tools=tools, stream=True
            )
            
            content = ""
            tool_calls = []
            for chunk in response:
                delta = chunk.choices[0].delta
                if delta.content:
                    print(delta.content, end="", flush=True)
                    content += delta.content
                if delta.tool_calls:
                    for tc in delta.tool_calls:
                        while len(tool_calls) <= tc.index:
                            tool_calls.append({"id": "", "name": "", "args": ""})
                        if tc.id:
                            tool_calls[tc.index]["id"] = tc.id
                        if tc.function and tc.function.name:
                            tool_calls[tc.index]["name"] = tc.function.name
                        if tc.function and tc.function.arguments:
                            tool_calls[tc.index]["args"] += tc.function.arguments
            
            if not tool_calls:
                self.messages.append({"role": "assistant", "content": content})
                print()
                return
            
            assistant_msg = {"role": "assistant", "content": content, "tool_calls": [
                {"id": tc["id"], "type": "function", "function": {"name": tc["name"], "arguments": tc["args"]}}
                for tc in tool_calls
            ]}
            self.messages.append(assistant_msg)
            
            for tc in tool_calls:
                print(f"\n  [工具] {tc['name']}({tc['args']})")
                result = tool_map[tc["name"]](**json.loads(tc["args"]))
                self.messages.append({"role": "tool", "tool_call_id": tc["id"], "content": str(result)})
    
    def save(self, name: str):
        path = os.path.join(HISTORY_DIR, f"{name}.json")
        with open(path, "w", encoding="utf-8") as f:
            json.dump(self.messages, f, ensure_ascii=False, indent=2)
        print(f"会话已保存: {path}")
    
    def load(self, name: str):
        path = os.path.join(HISTORY_DIR, f"{name}.json")
        with open(path, "r", encoding="utf-8") as f:
            self.messages = json.load(f)
        print(f"会话已加载: {path}")

# ============ 主程序 ============
def main():
    print("=" * 60)
    print("  本地 AI 知识助手 (基于 FastFlowLM)")
    print("=" * 60)
    print("命令: /save <名> 保存 | /load <名> 加载 | /clear 清空 | /quit 退出")
    print()
    
    # 初始化知识库
    kb.build_index()
    
    assistant = KnowledgeAssistant()
    print("\n助手已就绪，开始提问吧！\n")
    
    while True:
        try:
            user_input = input("你: ").strip()
        except (EOFError, KeyboardInterrupt):
            print("\n再见！")
            break
        
        if not user_input:
            continue
        
        if user_input == "/quit":
            break
        elif user_input == "/clear":
            assistant = KnowledgeAssistant()
            print("（对话已清空）\n")
            continue
        elif user_input.startswith("/save "):
            assistant.save(user_input[6:])
            continue
        elif user_input.startswith("/load "):
            assistant.load(user_input[6:])
            continue
        
        print("助手: ", end="", flush=True)
        assistant.chat_stream(user_input)
        print()

if __name__ == "__main__":
    main()
```

**使用流程：**

```bash
# 步骤1：启动两个 FastFlowLM Server（嵌入 + 生成）
# 终端1：
flm serve embeddinggemma:300m --port 52626
# 终端2：
flm serve qwen3:4b --port 52625 --ctx-len 8192

# 步骤2：准备知识库
mkdir knowledge
# 将你的文档（.txt/.md）放入 knowledge 目录
echo "FastFlowLM 是 AMD NPU 推理运行时，支持 256k 上下文。" > knowledge/flm_intro.md
echo "Qwen3 是阿里开源的大语言模型，支持工具调用。" > knowledge/models.md

# 步骤3：运行助手
python knowledge_assistant.py

# 首次运行会构建向量索引，之后直接加载
```

**运行效果：**

```
你: FastFlowLM 支持多长的上下文？
助手:   [工具] search_knowledge({"query": "FastFlowLM 上下文长度"})
根据知识库中的信息，FastFlowLM 支持最高 256k tokens 的上下文窗口。

你: 帮我计算 256 * 1024 等于多少
助手:   [工具] calculate({"expression": "256 * 1024"})
256 × 1024 = 262144。

你: 现在几点了？
助手:   [工具] get_current_time({})
现在是 2026-07-25 14:30:15。

你: /save debug
会话已保存: ./assistant_history/debug.json

你: /quit
再见！
```

**项目扩展方向：**

```python
# 扩展1：添加网络搜索工具（需联网）
def web_search(query: str) -> str:
    """调用搜索 API（如 SerpAPI）"""
    # 实现略
    pass

# 扩展2：添加数据库查询工具
def query_database(sql: str) -> str:
    """执行 SQL 查询"""
    import sqlite3
    # 实现略
    pass

# 扩展3：添加邮件发送工具
def send_email(to: str, subject: str, body: str) -> str:
    """发送邮件"""
    # 实现略
    pass

# 扩展4：支持 PDF 文档
# 安装 pypdf 后在 KnowledgeBase.load_documents 中添加 PDF 加载逻辑

# 扩展5：Web 界面
# 用 Streamlit 或 Gradio 包装，提供浏览器访问
```

#### 总结

本讲核心要点：综合实战项目整合了 RAG、工具调用、多轮对话、流式输出等核心能力，构建了一个可用的本地 AI 知识助手。项目采用模块化设计（知识库、工具集、模型独立），便于扩展。常见注意事项：嵌入和生成模型需分端口启动；首次运行需构建向量索引，之后可复用；工具调用循环要设上限（如 8 次）避免无限循环；流式 + 工具调用的组合处理较复杂，需正确拼接 tool_calls 的分块响应；会话历史用 JSON 持久化，注意文件大小（过长时需摘要或截断）；扩展功能时遵循"工具即函数"的模式，定义好 schema 即可接入；生产环境建议加 Web 界面（Gradio/Streamlit）提升易用性。

---

## 课程总结

恭喜你完成了 FastFlowLM 的 25 讲课程！让我们回顾这段学习旅程：

**基础篇（第1-2章）** 建立了对 NPU 推理的认知——FastFlowLM 是专为 AMD XDNA2 NPU 设计的轻量运行时，以 17MB 体积和 10 倍能效比，让本地 LLM 推理变得简单高效。你学会了检查硬件兼容性、安装 NPU 驱动、在 Windows/Linux 上部署 FastFlowLM。

**核心篇（第3-5章）** 掌握了两种使用模式——CLI 模式（`flm run`）适合交互式对话和调试，Server 模式（`flm serve`）适合应用集成。你学会了模型管理、参数调优、OpenAI 兼容 API 调用，以及如何在 LLaMA、Qwen、Gemma 等模型家族中做出选择。

**进阶篇（第6章）** 将 FastFlowLM 从"能用"提升到"好用"——工具调用让 LLM 能执行外部函数，RAG 让 LLM 基于私有知识回答，多模态输入让 LLM 能理解图像，批量推理让 LLM 能高效处理大量请求。

**实战篇（第7章）** 聚焦生产场景——性能调优榨干 NPU 算力，安全部署保障数据隐私，综合项目将所有知识融会贯通，构建出可用的本地 AI 知识助手。

FastFlowLM 代表了 AI 计算的一个重要趋势——**从云端走向边缘，从通用走向专用**。随着 NPU 硬件的普及和生态的成熟，本地 AI 推理将成为越来越多场景的首选。希望本课程能成为你探索这一领域的坚实起点。继续实践，构建你自己的本地 AI 应用吧！

