# LangChain 系统教程

> 从零基础到生产级 LLM 应用的完整学习路径
> 共 35 讲 / 9 章

---

## 课程总览

### 学习目标

本课程旨在帮助学习者从零开始，系统掌握 LangChain 这一最流行的 LLM 应用开发框架。完成本课程后，你将能够：

1. 理解 LangChain 的核心设计哲学——通过组合可复用组件（模型、提示、链、记忆、检索、Agent）构建复杂 LLM 应用
2. 熟练使用各类 LLM 模型（OpenAI、Anthropic、开源模型），掌握调用、流式、多模态等能力
3. 掌握提示工程，使用 PromptTemplate、FewShot、输出解析器构建结构化交互
4. 理解并运用 LCEL（LangChain Expression Language），用简洁语法构建复杂工作流
5. 实现对话记忆，让应用具备上下文感知能力
6. 构建完整的 RAG（检索增强生成）应用，从文档加载到向量检索
7. 开发 Agent 应用，让 LLM 自主调用工具完成复杂任务
8. 掌握回调、追踪、缓存等高级特性，将应用部署到生产环境

### 课程结构

本课程遵循"基础 → 核心 → 进阶 → 高级 → 实战"的渐进式学习路径：

- **第1章 基础入门**：建立对 LangChain 的整体认知，搭建环境，运行第一个程序
- **第2章 模型**：深入 LLM 与 Chat Model，掌握调用、流式、多模态
- **第3章 提示工程**：PromptTemplate、FewShot、输出解析器
- **第4章 LCEL 表达式语言**：Runnable、链式调用、并行、流式
- **第5章 链与记忆**：经典链结构与对话记忆机制
- **第6章 数据增强**：文档加载、分割、嵌入、向量存储、检索
- **第7章 Agent 与工具**：Agent 架构、工具定义、ReAct 模式
- **第8章 高级特性**：回调、追踪、缓存
- **第9章 实战应用**：RAG、对话机器人、生产部署

### 详细章节目录

#### 第1章 基础入门
- 第1讲：LangChain 简介与生态
- 第2讲：核心概念总览
- 第3讲：环境搭建与 Hello World
- 第4讲：第一个 LangChain 程序

#### 第2章 模型
- 第5讲：LLM 与 Chat Model
- 第6讲：模型调用与参数配置
- 第7讲：流式输出
- 第8讲：多模态模型

#### 第3章 提示工程
- 第9讲：PromptTemplate 基础
- 第10讲：ChatPromptTemplate
- 第11讲：FewShotPromptTemplate
- 第12讲：输出解析器 Output Parser

#### 第4章 LCEL 表达式语言
- 第13讲：LCEL 与 Runnable
- 第14讲：链式调用
- 第15讲：并行与批处理
- 第16讲：流式与异步

#### 第5章 链与记忆
- 第17讲：LLMChain
- 第18讲：SequentialChain
- 第19讲：Memory 概念与类型
- 第20讲：高级记忆策略

#### 第6章 数据增强
- 第21讲：Document Loaders
- 第22讲：Text Splitters
- 第23讲：Embeddings
- 第24讲：Vector Stores
- 第25讲：Retrievers

#### 第7章 Agent 与工具
- 第26讲：Agent 概念与类型
- 第27讲：Tools 工具定义
- 第28讲：ReAct Agent
- 第29讲：Agent Executor

#### 第8章 高级特性
- 第30讲：Callbacks 回调
- 第31讲：追踪与调试
- 第32讲：缓存机制

#### 第9章 实战应用
- 第33讲：RAG 应用实战
- 第34讲：对话机器人实战
- 第35讲：生产部署

---

# 第1章 基础入门

本章是整个课程的起点。我们将从 LangChain 的定位与生态讲起，逐步建立对这一框架的整体认知，搭建开发环境，并运行第一个程序。学完本章，你将理解 LangChain 为什么存在、它解决了什么问题，以及如何快速上手。

---

## 第1讲：LangChain 简介与生态

### 概念

LangChain 是一个用于构建**大语言模型（LLM）应用**的开源框架，由 Harrison Chase 于 2022 年 10 月创建。它的核心思想是将 LLM 应用开发中的常见模式抽象为可复用组件——模型接口、提示模板、链、记忆、检索、Agent——通过组合这些组件，开发者可以快速构建从简单到复杂的各类 LLM 应用。

LangChain 的目标是降低 LLM 应用开发门槛。在没有 LangChain 之前，开发者需要自己处理 API 调用、提示管理、上下文记忆、工具调用等繁琐细节；LangChain 把这些封装为统一接口，让开发者专注于业务逻辑。

### 原理

**LangChain 的设计哲学源于一个核心洞察：LLM 应用开发的难点不在于调用 LLM（那只是一次 API 请求），而在于围绕 LLM 构建完整的工程体系**。

一个真实的 LLM 应用通常需要：管理提示模板、处理对话历史、检索外部知识、调用外部工具、解析结构化输出、处理流式响应、监控调试。LangChain 把这些常见需求抽象为标准组件，提供统一接口，让开发者像搭积木一样组合。

**LangChain 生态的三个层次**：

| 层次 | 包名 | 作用 |
|------|------|------|
| 核心库 | `langchain-core` | 基础抽象：Runnable、PromptTemplate 等 |
| 集成库 | `langchain-openai`、`langchain-anthropic` 等 | 第三方服务集成 |
| 应用层 | `langchain`、`langgraph`、`langsmith` | 完整应用框架 |

**LangChain 生态包含几个关键产品**：
- **LangChain**：核心框架，提供组件抽象和链式组合能力
- **LangGraph**：用于构建有状态、可循环的 Agent 工作流（扩展框架）
- **LangSmith**：LLM 应用的观测、调试、评估平台
- **LangServe**：将 LangChain 应用部署为 REST API

**为什么选择 LangChain**：
1. **生态丰富**：支持几乎所有主流 LLM 和工具
2. **抽象合理**：组件解耦，可自由组合
3. **社区活跃**：大量教程、示例、第三方扩展
4. **持续演进**：从 Chain 到 LCEL 再到 LangGraph，不断进化

### 例子

**LangChain 与原生 API 调用的对比**：

原生 OpenAI API 调用：
```python
import openai
response = openai.ChatCompletion.create(
    model="gpt-4",
    messages=[{"role": "user", "content": "你好"}]
)
print(response.choices[0].message.content)
```

LangChain 调用：
```python
from langchain_openai import ChatOpenAI
llm = ChatOpenAI(model="gpt-4")
response = llm.invoke("你好")
print(response.content)
```

看起来差不多，但当需求变复杂时，LangChain 的优势就显现了：

```python
# LangChain 构建带记忆的对话机器人
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain.memory import ConversationBufferMemory

# 组件化构建
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是友好助手"),
    ("placeholder", "{history}"),
    ("human", "{input}")
])
llm = ChatOpenAI()
memory = ConversationBufferMemory(return_messages=True)

# 用 LCEL 组合
chain = prompt | llm | StrOutputParser()

# 每轮对话自动管理记忆
response = chain.invoke({"input": "你好", "history": memory.load_memory_variables({})["history"]})
memory.save_context({"input": "你好"}, {"output": response})
```

安装 LangChain：

```bash
# 安装核心包
pip install langchain

# 安装特定模型集成
pip install langchain-openai
pip install langchain-anthropic

# 安装向量存储集成
pip install langchain-chroma
pip install langchain-faiss
```

验证安装：

```python
import langchain
print(langchain.__version__)  # 输出版本号，如 0.3.x
```

### 总结

- **核心定位**：LangChain 是 LLM 应用开发框架，将常见模式抽象为可复用组件
- **设计动机**：LLM 应用开发的难点在于围绕 LLM 构建工程体系，LangChain 降低门槛
- **生态层次**：核心库（langchain-core）、集成库（langchain-openai 等）、应用层（langchain、langgraph、langsmith）
- **核心产品**：LangChain（框架）、LangGraph（Agent）、LangSmith（观测）、LangServe（部署）
- **选择理由**：生态丰富、抽象合理、社区活跃、持续演进
- **常见误区**：LangChain 不是 LangGraph 的替代品，两者互补；LangChain 适合快速构建应用，LangGraph 适合复杂 Agent

---

## 第2讲：核心概念总览

### 概念

LangChain 的核心概念可以归纳为"**六大组件**"：

1. **Model（模型）**：LLM 的统一接口，包括文本补全模型（LLM）和对话模型（ChatModel）
2. **Prompt（提示）**：提示模板，将变量注入到预设模板生成最终提示
3. **Chain（链）**：将多个组件串联的机制，如 Prompt → LLM → OutputParser
4. **Memory（记忆）**：对话历史管理，让应用具备上下文感知
5. **Retriever（检索器）**：从外部数据源检索相关信息，用于 RAG
6. **Agent（代理）**：让 LLM 自主决策调用工具完成复杂任务

此外还有贯穿所有组件的 **Runnable** 抽象（LCEL 的基础）和 **Callback**（回调机制）。本讲先建立整体认知，后续章节会逐一深入。

### 原理

**LangChain 的工作机制可以类比为一个"流水线工厂"**：

想象一条生产流水线，原材料（用户输入）依次经过多个工位（组件）加工，每个工位做自己的事——提示工位把输入装进模板，模型工位调用 LLM 生成回复，解析工位把回复转为结构化数据。最终产出成品（应用输出）。

**Runnable 统一接口原理**：LangChain 的所有组件都实现 `Runnable` 接口，提供统一的 `invoke`、`batch`、`stream` 方法。这意味着任何组件都可以像函数一样被调用，也可以像管道一样被串联。这是 LCEL（LangChain Expression Language）的基础——用 `|` 操作符把 Runnable 串联成链。

**组件协作原理**：
- **Prompt** 接收变量，生成提示文本
- **Model** 接收提示，调用 LLM，返回响应
- **OutputParser** 接收响应，解析为结构化数据
- **Memory** 在多轮对话间保存历史
- **Retriever** 从知识库检索相关文档
- **Agent** 根据用户输入决定调用哪些工具

**数据流原理**：LangChain 应用本质是数据流——输入数据经过一系列组件处理，最终输出结果。LCEL 用 `|` 表达这种流向：`prompt | llm | parser`。这种声明式语法让工作流一目了然。

**组件解耦原理**：每个组件只关注自己的输入输出，不关心上下游。比如 LLM 组件不关心提示从哪来，只接收文本返回响应。这种解耦让组件可复用、可替换——换一个 LLM 只需改一行代码，其他组件不变。

### 例子

下面用一个"问答机器人"展示完整的概念流转：

```python
import os
from dotenv import load_dotenv
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

load_dotenv()

# 1. Model（模型）
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)

# 2. Prompt（提示模板）
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个专业的问答助手，回答简洁准确。"),
    ("human", "{question}")
])

# 3. OutputParser（输出解析器）
parser = StrOutputParser()

# 4. Chain（用 LCEL 组合）
chain = prompt | llm | parser

# 5. 执行
answer = chain.invoke({"question": "什么是 LangChain？"})
print(answer)
# 输出：LangChain 是一个用于构建 LLM 应用的开源框架...
```

概念对应关系图：

```
用户输入 {question: "..."}
        ↓
   [Prompt] 生成完整提示
        ↓
    [LLM] 调用模型
        ↓
  [Parser] 解析输出
        ↓
   最终回答
```

**六大组件的协作全景**：

```
[用户输入] → [Memory] 加载历史 → [Prompt] 组装提示 → [Retriever] 检索知识
                                                              ↓
                                                         [LLM] 生成回复
                                                              ↓
                                                        [Parser] 解析
                                                              ↓
                                              [Memory] 保存历史 → [输出]
                                                               ↑
                                              [Agent] 决策是否调用工具
```

### 总结

- **六大组件**：Model、Prompt、Chain、Memory、Retriever、Agent
- **Runnable 统一接口**：所有组件实现 invoke/batch/stream，可串联
- **LCEL 声明式语法**：用 `|` 组合组件，数据流一目了然
- **组件解耦**：每个组件只关注输入输出，可复用可替换
- **数据流思维**：LangChain 应用本质是数据流经组件处理
- **关键认知**：理解 LangChain 的关键在于理解"组件组合"这一模型，后续所有特性都建立在此基础上

---

## 第3讲：环境搭建与 Hello World

### 概念

本讲我们将搭建完整的 LangChain 开发环境，并运行第一个真正意义上的程序。环境搭建包括：Python 环境、LangChain 及相关依赖安装、LLM API Key 配置、开发工具选择。Hello World 程序将展示从模型初始化到调用执行的完整流程。

### 原理

**环境选择原理**：LangChain 是纯 Python 库，对环境要求宽松，但推荐使用 Python 3.9+ 以获得最佳类型注解支持。由于 LangChain 通常需要调用 LLM API，建议配合虚拟环境管理依赖，避免与系统其他项目冲突。

**LLM 接入原理**：LangChain 本身不提供 LLM，需要接入外部模型。最常见的方式是通过官方集成包（如 `langchain-openai`、`langchain-anthropic`）。API Key 通过环境变量传入，避免硬编码。LangChain 支持几十种 LLM 提供商，接口统一。

**包管理原理**：LangChain 采用模块化包设计：
- `langchain-core`：核心抽象，不依赖第三方
- `langchain`：主包，包含常用链和 Agent
- `langchain-{provider}`：特定提供商集成（如 langchain-openai）

这种设计让你只安装需要的部分，避免依赖膨胀。

**项目结构原理**：一个典型的 LangChain 项目结构如下：

```
my_langchain_app/
├── .env                 # 环境变量（API Key 等）
├── requirements.txt     # 依赖
├── chains/              # 链定义
├── prompts/             # 提示模板
├── tools/               # 工具定义
├── data/                # 数据文件
└── main.py              # 入口
```

初学阶段可以全部写在一个文件里，随着复杂度增加再拆分。

### 例子

**步骤1：创建虚拟环境并安装依赖**

```bash
# 创建项目目录
mkdir my_langchain_app && cd my_langchain_app

# 创建虚拟环境
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 安装核心依赖
pip install langchain langchain-openai langchain-community python-dotenv
```

**步骤2：配置环境变量**

创建 `.env` 文件：

```env
OPENAI_API_KEY=sk-your-api-key-here
# 如果用 Anthropic
# ANTHROPIC_API_KEY=sk-ant-your-key
# 如果用其他
# TAVILY_API_KEY=tvly-your-key  # 搜索工具
```

**步骤3：编写 Hello World 程序**

创建 `hello_world.py`：

```python
import os
from dotenv import load_dotenv
from langchain_openai import ChatOpenAI
from langchain_core.messages import HumanMessage

# 加载环境变量
load_dotenv()

# 1. 初始化 LLM
llm = ChatOpenAI(
    model="gpt-4o-mini",
    temperature=0,  # 0 表示确定性输出
    timeout=30,     # 超时30秒
    max_retries=2   # 失败重试2次
)

# 2. 调用 LLM
response = llm.invoke("用一句话介绍 LangChain")

# 3. 输出结果
print("LLM 回复：", response.content)
print("Token 使用：", response.usage_metadata)
```

**步骤4：运行**

```bash
python hello_world.py
```

输出示例：
```
LLM 回复：LangChain 是一个用于构建 LLM 应用的开源框架，提供模型接口、提示模板、链等组件。
Token 使用：{'input_tokens': 10, 'output_tokens': 25, 'total_tokens': 35}
```

**步骤5：使用不同提供商的模型**

```python
# 使用 Anthropic Claude
from langchain_anthropic import ChatAnthropic
claude = ChatAnthropic(model="claude-3-5-sonnet-20241022")
response = claude.invoke("你好")

# 使用开源模型（通过 Ollama）
from langchain_ollama import ChatOllama
local_llm = ChatOllama(model="llama3")
response = local_llm.invoke("你好")

# 使用 Google Gemini
from langchain_google_genai import ChatGoogleGenerativeAI
gemini = ChatGoogleGenerativeAI(model="gemini-pro")
response = gemini.invoke("你好")
```

**步骤6：开发工具推荐**

```bash
# Jupyter Notebook（交互式开发）
pip install jupyter
jupyter notebook

# LangSmith（调试追踪，可选）
pip install langsmith
# 在 .env 中设置
# LANGSMITH_API_KEY=lsv2_your_key
# LANGCHAIN_TRACING_V2=true
```

### 总结

- **环境要点**：Python 3.9+、虚拟环境、`pip install langchain langchain-openai`
- **LLM 接入**：通过官方集成包，API Key 放 `.env`，用 `load_dotenv()` 加载
- **模块化包**：langchain-core、langchain、langchain-{provider} 按需安装
- **统一接口**：不同提供商的 LLM 接口一致，切换只需改一行
- **开发工具**：Jupyter 适合探索，LangSmith 适合调试
- **常见问题**：API Key 未加载（检查 `load_dotenv()`）、包未安装（检查 pip install）、模型名错误（查看官方文档）

---

## 第4讲：第一个 LangChain 程序

### 概念

本讲我们构建一个稍微复杂一点的 LangChain 程序——一个**多步骤翻译器**：接收用户输入，先做语言检测，再翻译为目标语言，最后做质量检查。这个例子将完整展示 Prompt、Model、Parser、Chain 的协同工作，是理解 LangChain 工作流的最佳起点。

### 原理

**多步骤组合原理**：在真实场景中，一个任务往往需要多个步骤完成。LangChain 通过 LCEL（`|` 操作符）将多个组件串联，形成处理流水线。每个组件接收上游输出、产出下游输入，数据在管道中流动。

**LCEL 链式原理**：`prompt | llm | parser` 这种语法的本质是函数组合——`parser(llm(prompt(input)))`。LCEL 让这种组合变得直观，同时自动处理异步、批处理、流式等细节。这是 LangChain 现代写法的核心。

**提示模板原理**：PromptTemplate 将变量注入到预设模板，生成最终提示。比如模板 `"翻译以下{text}为{language}"`，传入 `{"text": "hello", "language": "中文"}`，生成 `"翻译以下hello为中文"`。这让提示可复用、可维护。

**输出解析原理**：LLM 返回的是文本，但应用通常需要结构化数据（如 JSON、列表）。OutputParser 把文本解析为结构化对象。比如 `StrOutputParser` 提取纯文本，`JsonOutputParser` 解析 JSON，`PydanticOutputParser` 解析为 Pydantic 模型。

**数据流原理**：整个链是一个数据流——输入字典经过 Prompt 变为消息列表，经过 LLM 变为 AIMessage，经过 Parser 变为字符串。每一步的输出类型与下一步的输入类型匹配，这是 LCEL 类型安全的基础。

### 例子

```python
import os
from dotenv import load_dotenv
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

load_dotenv()

# 1. 初始化 LLM
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)

# 2. 定义提示模板
translate_prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个专业翻译。将用户输入翻译为{language}。只输出翻译结果，不要解释。"),
    ("human", "{text}")
])

# 3. 定义输出解析器
parser = StrOutputParser()

# 4. 用 LCEL 组合链
translation_chain = translate_prompt | llm | parser

# 5. 执行翻译
result = translation_chain.invoke({
    "text": "LangChain is a framework for building LLM applications.",
    "language": "中文"
})
print("翻译结果：", result)
# 输出：LangChain 是一个用于构建 LLM 应用的框架。
```

**进阶：多步骤组合**

```python
from langchain_core.runnables import RunnablePassthrough

# 步骤1：语言检测
detect_prompt = ChatPromptTemplate.from_template(
    "检测以下文本的语言，只输出语言名称（如 English/Chinese）：\n{text}"
)
detect_chain = detect_prompt | llm | parser

# 步骤2：翻译
translate_prompt = ChatPromptTemplate.from_messages([
    ("system", "将用户输入翻译为{target_language}，只输出翻译结果。"),
    ("human", "{text}")
])
translate_chain = translate_prompt | llm | parser

# 步骤3：质量检查
quality_prompt = ChatPromptTemplate.from_template(
    "评估以下翻译质量，输出'优秀'或'需改进'加一句话理由：\n原文：{original}\n译文：{translated}"
)
quality_chain = quality_prompt | llm | parser

# 组合多步骤链
full_chain = (
    # 先检测语言
    {"detected_language": detect_chain, "text": RunnablePassthrough()}
    # 再翻译
    | (lambda x: {**x, "translated": translate_chain.invoke({"text": x["text"], "target_language": "中文"})})
    # 最后质量检查
    | (lambda x: {**x, "quality": quality_chain.invoke({"original": x["text"], "translated": x["translated"]})})
)

# 执行
result = full_chain.invoke("Hello, how are you?")
print("检测语言：", result["detected_language"])
print("翻译结果：", result["translated"])
print("质量评估：", result["quality"])
```

**执行流程图**：
```
输入: "Hello, how are you?"
        ↓
   [detect_chain] → "English"
        ↓
   [translate_chain] → "你好，你怎么样？"
        ↓
   [quality_chain] → "优秀 - 翻译准确自然"
        ↓
   输出: {detected, translated, quality}
```

**观察数据流**：可以用 stream 观察每步输出：

```python
# 流式观察
for chunk in translation_chain.stream({"text": "Hello", "language": "中文"}):
    print(chunk, end="", flush=True)
# 逐字输出翻译结果
```

**调试技巧**：用 `batch` 批量处理：

```python
# 批量翻译
results = translation_chain.batch([
    {"text": "Hello", "language": "中文"},
    {"text": "Goodbye", "language": "中文"},
    {"text": "Thank you", "language": "中文"}
])
for r in results:
    print(r)
```

### 总结

- **LCEL 链式组合**：用 `|` 串联组件，数据在管道中流动
- **PromptTemplate**：将变量注入模板生成提示，可复用可维护
- **OutputParser**：把 LLM 文本输出解析为结构化数据
- **多步骤组合**：复杂任务拆分为多个链，再组合成完整流程
- **RunnablePassthrough**：传递输入到下游，常用于多分支组合
- **关键认知**：本讲示例体现了 LangChain 的核心工作模式——组件组合 + 数据流。后续所有高级特性都是这一模式的扩展

---

# 第2章 模型

模型是 LangChain 应用的核心——所有 LLM 能力都通过模型组件接入。本章深入 LangChain 的模型体系，包括 LLM 与 ChatModel 的区别、调用参数配置、流式输出、多模态支持。学完本章，你将能够熟练使用各类 LLM 模型。

---

## 第5讲：LLM 与 Chat Model

### 概念

LangChain 提供两种模型抽象：

1. **LLM（大语言模型）**：文本补全模型，输入文本、输出文本。如 OpenAI 的 `text-davinci-003`（已废弃）、开源的 Llama、GPT-2 等。这是早期的模型形态。

2. **ChatModel（对话模型）**：对话接口模型，输入消息列表、输出消息。如 OpenAI 的 `gpt-4o`、Anthropic 的 `claude-3` 等。这是现代主流模型形态。

两者的核心区别在于输入输出格式——LLM 用纯文本，ChatModel 用结构化消息（System/Human/AI/Tool 消息）。现代应用推荐使用 ChatModel，因为它支持系统提示、多角色对话、工具调用等高级特性。

### 原理

**LLM 与 ChatModel 的本质区别原理**：

LLM 是"文本进、文本出"——你给它一段文本，它续写一段文本。没有角色概念，没有对话结构。这种模式简单但不灵活，难以表达"系统设定 + 用户提问"这种结构。

ChatModel 是"消息进、消息出"——你给它一组消息（每条消息有角色和内容），它返回一条 AI 消息。这种结构天然支持多角色对话、系统提示、工具调用。现代 LLM（如 GPT-4、Claude）都采用这种接口。

**ChatModel 的消息体系原理**：
- **SystemMessage**：系统指令，设定 AI 角色和行为规范
- **HumanMessage**：用户消息
- **AIMessage**：AI 回复，可含 tool_calls（工具调用请求）
- **ToolMessage**：工具执行结果

**统一接口原理**：虽然 LLM 和 ChatModel 内部机制不同，但 LangChain 让它们都实现 Runnable 接口，可以用相同方式调用。LangChain 还提供 `LLMChain` 和 `LLMRouterChain` 等适配器，让两者在链中可互换。

**模型选型原理**：选择模型时考虑：
1. **能力**：推理、代码、多语言、多模态
2. **成本**：GPT-4o-mini 比 GPT-4o 便宜约 30 倍
3. **速度**：小模型更快，适合实时场景
4. **隐私**：开源模型可本地部署，数据不出域
5. **上下文长度**：长文档场景需要大上下文模型

### 例子

**示例1：使用 LLM（文本补全模型）**

```python
from langchain_openai import OpenAI
# 注意：OpenAI 的 text-davinci-003 已废弃，这里用兼容接口
llm = OpenAI(model="gpt-3.5-turbo-instruct", temperature=0.7)

# 文本进、文本出
response = llm.invoke("用一句话介绍 Python：")
print(response)  # 直接是字符串
# 输出：Python 是一种简洁、易学、功能强大的编程语言。
```

**示例2：使用 ChatModel（对话模型）**

```python
from langchain_openai import ChatOpenAI
from langchain_core.messages import SystemMessage, HumanMessage, AIMessage

chat = ChatOpenAI(model="gpt-4o-mini", temperature=0.7)

# 消息进、消息出
messages = [
    SystemMessage(content="你是一个 Python 编程专家，回答简洁专业。"),
    HumanMessage(content="怎么反转字符串？"),
]
response = chat.invoke(messages)
print(response.content)  # AI 回复内容
# 输出：使用切片 [::-1]，如 'hello'[::-1] 得到 'olleh'。
print(type(response))  # <class 'AIMessage'>
```

**示例3：ChatModel 的多轮对话**

```python
chat = ChatOpenAI(model="gpt-4o-mini")

# 多轮对话
conversation = [
    SystemMessage(content="你是友好助手。"),
    HumanMessage(content="你好"),
    AIMessage(content="你好！有什么可以帮你？"),
    HumanMessage(content="我刚才说了什么？"),
]
response = chat.invoke(conversation)
print(response.content)
# 输出：你说了"你好"呀。
```

**示例4：不同提供商的 ChatModel**

```python
# OpenAI
from langchain_openai import ChatOpenAI
gpt = ChatOpenAI(model="gpt-4o")

# Anthropic Claude
from langchain_anthropic import ChatAnthropic
claude = ChatAnthropic(model="claude-3-5-sonnet-20241022")

# Google Gemini
from langchain_google_genai import ChatGoogleGenerativeAI
gemini = ChatGoogleGenerativeAI(model="gemini-1.5-pro")

# 本地模型（通过 Ollama）
from langchain_ollama import ChatOllama
local = ChatOllama(model="llama3", temperature=0.7)

# 统一接口调用
for model in [gpt, claude, gemini, local]:
    response = model.invoke("你好")
    print(f"{model.__class__.__name__}: {response.content[:50]}")
```

**示例5：在 LCEL 链中使用**

```python
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

# ChatModel 可直接用于 LCEL
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是{role}。"),
    ("human", "{question}")
])

# 不同模型可互换
for model in [gpt, claude]:
    chain = prompt | model | StrOutputParser()
    answer = chain.invoke({"role": "历史老师", "question": "讲讲秦始皇"})
    print(f"{model.__class__.__name__}: {answer[:80]}")
```

### 总结

- **两种模型**：LLM（文本进文本出）、ChatModel（消息进消息出）
- **推荐 ChatModel**：支持系统提示、多角色、工具调用，是现代主流
- **消息体系**：System/Human/AI/Tool 四种消息类型
- **统一接口**：都实现 Runnable，可在链中互换
- **模型选型**：考虑能力、成本、速度、隐私、上下文长度
- **多提供商**：OpenAI、Anthropic、Google、Ollama 等，接口一致

---

## 第6讲：模型调用与参数配置

### 概念

本讲深入模型调用的细节，包括各类调用方法（invoke、batch、stream）和关键参数配置（temperature、max_tokens、stop、model_kwargs 等）。理解这些参数让你能精细控制模型行为，满足不同场景需求。

### 原理

**调用方法原理**：
- **invoke(input)**：同步调用，输入一个、输出一个。最常用。
- **batch(inputs)**：批量调用，输入列表、输出列表。内部并发，提升吞吐。
- **stream(input)**：流式调用，逐 token 返回。适合实时显示。
- **ainvoke/abatch/astream**：异步版本，适合高并发场景。

**temperature 原理**：控制输出随机性，范围 0-2（不同模型范围可能不同）。
- **0**：确定性输出，每次相同输入得到相同输出。适合代码、事实问答。
- **0.7**：适度随机，平衡一致性和创造性。适合对话。
- **1.0+**：高随机性，输出多样。适合创意写作、头脑风暴。

**max_tokens 原理**：限制输出 token 数。控制成本和响应长度。注意 token 不等于字符——中文一个字约 1-2 token，英文一个词约 1 token。

**stop 原理**：停止序列，遇到这些字符串时停止生成。用于控制输出格式，如让模型在 `"\n\n"` 处停止。

**model_kwargs 原理**：传递模型特定参数。不同模型有不同参数，如 OpenAI 的 `frequency_penalty`（频率惩罚）、`presence_penalty`（存在惩罚）、`top_p`（核采样）。

**超时与重试原理**：LLM API 调用可能超时或失败。LangChain 内置超时（`timeout`）和重试（`max_retries`）机制，避免网络问题导致应用崩溃。

### 例子

**示例1：基础参数配置**

```python
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(
    model="gpt-4o-mini",
    temperature=0,        # 确定性输出
    max_tokens=200,       # 最多200 token
    timeout=30,           # 超时30秒
    max_retries=2,        # 失败重试2次
    stop=["\n\n\n"],      # 遇到3个换行停止
    model_kwargs={        # 模型特定参数
        "frequency_penalty": 0.5,  # 降低重复
        "presence_penalty": 0.3,   # 鼓励新话题
    }
)

response = llm.invoke("写一首关于春天的诗")
print(response.content)
```

**示例2：不同 temperature 的效果**

```python
prompt = "用一句话描述秋天"

for temp in [0, 0.5, 1.0, 1.5]:
    llm = ChatOpenAI(model="gpt-4o-mini", temperature=temp)
    response = llm.invoke(prompt)
    print(f"temperature={temp}: {response.content}")
    
# temperature=0: 秋天是收获的季节，金黄的落叶铺满大地。
# temperature=0.5: 秋天像一位画家，把世界染成金黄与火红。
# temperature=1.0: 秋风轻抚，落叶如蝶，在金色阳光里翩翩起舞。
# temperature=1.5: 秋天是宇宙写给大地的情书，每一片落叶都是...（可能不太连贯）
```

**示例3：批量调用**

```python
llm = ChatOpenAI(model="gpt-4o-mini")

# 批量调用（内部并发）
questions = [
    "什么是 Python？",
    "什么是 JavaScript？",
    "什么是 Rust？"
]
results = llm.batch([
    [HumanMessage(content=q)] for q in questions
])

for q, r in zip(questions, results):
    print(f"Q: {q}")
    print(f"A: {r.content[:100]}\n")
```

**示例4：max_tokens 控制输出长度**

```python
# 限制输出长度，控制成本
llm_short = ChatOpenAI(model="gpt-4o-mini", max_tokens=50)
response = llm_short.invoke("详细介绍 Python 的历史")
print(f"输出长度: {len(response.content)} 字符")
print(response.content)
# 输出会被截断在50 token左右

# 查看实际 token 使用
print(response.usage_metadata)
# {'input_tokens': 10, 'output_tokens': 50, 'total_tokens': 60}
```

**示例5：stop 序列控制格式**

```python
llm = ChatOpenAI(model="gpt-4o-mini", stop=["###"])

response = llm.invoke("""为以下产品写描述，以###结尾：
产品：智能手表
描述：""")
print(response.content)
# 输出会在 ### 处停止
```

**示例6：超时与重试**

```python
from langchain_openai import ChatOpenAI

# 配置健壮的 LLM
llm = ChatOpenAI(
    model="gpt-4o-mini",
    timeout=60,           # 60秒超时
    max_retries=3,        # 最多重试3次
)

try:
    response = llm.invoke("复杂问题...")
except Exception as e:
    print(f"调用失败: {e}")
    # 降级处理
```

### 总结

- **调用方法**：invoke（单个）、batch（批量）、stream（流式）、异步版本
- **temperature**：0 确定性、0.7 平衡、1.0+ 创造性
- **max_tokens**：控制输出长度和成本
- **stop**：停止序列，控制输出格式
- **model_kwargs**：模型特定参数（frequency_penalty 等）
- **超时重试**：timeout + max_retries 保证健壮性
- **生产建议**：设置合理超时和重试，监控 token 消耗

---

## 第7讲：流式输出

### 概念

**流式输出（Streaming）** 是指 LLM 在生成回复时，逐 token（词元）实时返回，而非等全部生成完才返回。流式输出让用户能实时看到 AI 的"思考过程"，大幅提升用户体验，避免长时间等待的焦虑。

LangChain 的所有模型都支持流式，通过 `stream()` 方法实现。在 LCEL 链中，流式能力自动传递——链中任一组件支持流式，整个链就能流式输出。

### 原理

**LLM 流式原理**：LLM 生成回复时，是逐 token 生成的——每次预测下一个 token，直到结束。传统 API 等全部 token 生成完才返回，而流式 API 每个 token 生成完就立即推送。这让用户能在生成过程中就看到内容，而非等几十秒。

**SSE 协议原理**：OpenAI 等提供商用 Server-Sent Events（SSE）实现流式——服务端持续推送数据块，客户端逐块接收。每个 chunk 包含一小段文本（可能是一个 token 或几个字符）。

**LangChain stream 原理**：调用 `llm.stream(input)` 返回一个迭代器，每次 yield 一个 `AIMessageChunk`。chunk 的 `content` 是这一步的文本片段。拼接所有 chunk 的 content 就是完整回复。

**LCEL 流式传递原理**：在 LCEL 链中，如果所有组件都支持流式，数据会以流的方式经过每个组件——Prompt 流式输出消息、LLM 流式输出 chunk、Parser 流式处理。这种"流式传递"让整个链能实时输出，即使中间有多个组件。

**流式 vs 批量原理**：流式适合实时交互（聊天界面），批量适合高吞吐（批量处理）。流式延迟低（首 token 快），但总时间可能与批量相当。根据场景选择。

### 例子

**示例1：基础流式输出**

```python
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4o-mini", streaming=True)

# 流式调用
print("AI: ", end="")
for chunk in llm.stream("用100字介绍 LangChain"):
    print(chunk.content, end="", flush=True)
# 逐字输出：LangChain 是一个用于构建 LLM 应用的开源框架...
```

**示例2：流式输出到列表**

```python
# 收集所有 chunk
chunks = []
for chunk in llm.stream("写一首诗"):
    chunks.append(chunk)

# 拼接完整内容
full_content = "".join([c.content for c in chunks])
print("完整内容：", full_content)
print(f"共 {len(chunks)} 个 chunk")
```

**示例3：LCEL 链的流式**

```python
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

prompt = ChatPromptTemplate.from_messages([
    ("system", "你是诗人"),
    ("human", "写一首关于{topic}的诗")
])

chain = prompt | llm | StrOutputParser()

# 链式流式输出
print("诗：", end="")
for chunk in chain.stream({"topic": "秋天"}):
    print(chunk, end="", flush=True)
# 实时输出整首诗
```

**示例4：流式与非流式对比**

```python
import time

# 非流式
start = time.time()
response = llm.invoke("用200字介绍 Python")
print(f"非流式总时间: {time.time()-start:.2f}s")
print(response.content)

# 流式
start = time.time()
first_token_time = None
print("\n流式：")
for chunk in llm.stream("用200字介绍 Python"):
    if first_token_time is None:
        first_token_time = time.time()
        print(f"\n首 token 时间: {first_token_time-start:.2f}s")
    print(chunk.content, end="", flush=True)
print(f"\n流式总时间: {time.time()-start:.2f}s")
```

**示例5：异步流式**

```python
import asyncio
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4o-mini", streaming=True)

async def async_stream():
    print("AI: ", end="")
    async for chunk in llm.astream("用100字介绍 LangChain"):
        print(chunk.content, end="", flush=True)

asyncio.run(async_stream())
```

**示例6：流式处理工具调用**

```python
# 流式输出时处理 tool_calls
llm_with_tools = llm.bind_tools([...])

for chunk in llm_with_tools.stream("搜索 LangChain"):
    if chunk.content:
        print(chunk.content, end="", flush=True)
    if chunk.tool_call_chunks:
        print(f"\n[工具调用片段]: {chunk.tool_call_chunks}")
```

### 总结

- **流式输出**：逐 token 实时返回，提升用户体验
- **stream 方法**：返回迭代器，每次 yield AIMessageChunk
- **LCEL 流式传递**：链中组件都支持流式，整个链自动流式
- **首 token 延迟低**：流式让用户快速看到响应开始
- **异步流式**：用 astream，适合高并发
- **适用场景**：聊天界面、实时交互、长文本生成

---

## 第8讲：多模态模型

### 概念

**多模态模型（Multimodal Model）** 是能处理多种输入类型（文本、图片、音频、视频）的 LLM。如 GPT-4o 支持文本+图片，Gemini 1.5 支持文本+图片+音频+视频。多模态让 LLM 能"看"、"听"，拓展了应用边界。

LangChain 通过统一的消息内容格式支持多模态——消息的 `content` 可以是字符串（纯文本）或列表（多模态），列表中每个元素是一个内容块（文本块、图片块等）。

### 原理

**多模态消息格式原理**：传统消息 content 是字符串，多模态消息 content 是列表，每个元素是字典，包含 `type` 和对应内容：
- `{"type": "text", "text": "..."}`：文本块
- `{"type": "image_url", "image_url": {"url": "..."}}`：图片块
- `{"type": "input_audio", "input_audio": {...}}`：音频块（部分模型）

**图片输入方式原理**：
1. **URL**：图片的公开 URL，模型直接下载
2. **Base64**：图片编码为 base64 字符串，适合本地图片
3. **文件路径**：部分集成支持直接传路径

**多模态能力原理**：不同模型支持不同模态：
- GPT-4o：文本 + 图片
- GPT-4o-mini：文本 + 图片（更便宜）
- Gemini 1.5 Pro：文本 + 图片 + 音频 + 视频
- Claude 3.5：文本 + 图片

**多模态应用场景原理**：
- 图片理解：描述图片内容、识别物体、读图表
- 文档分析：OCR + 理解，处理扫描件
- 视觉问答：基于图片回答问题
- 多模态对话：结合图文对话

### 例子

**示例1：图片理解（URL 方式）**

```python
from langchain_openai import ChatOpenAI
from langchain_core.messages import HumanMessage

llm = ChatOpenAI(model="gpt-4o")  # 需要支持视觉的模型

# 用 URL 传入图片
message = HumanMessage(content=[
    {"type": "text", "text": "描述这张图片的内容"},
    {
        "type": "image_url",
        "image_url": {"url": "https://example.com/image.jpg"}
    }
])

response = llm.invoke([message])
print(response.content)
# 输出：图片中是一只橘色的猫，坐在窗台上...
```

**示例2：本地图片（Base64 方式）**

```python
import base64

def encode_image(path):
    with open(path, "rb") as f:
        return base64.b64encode(f.read()).decode()

# 编码本地图片
image_base64 = encode_image("photo.png")
image_url = f"data:image/png;base64,{image_base64}"

message = HumanMessage(content=[
    {"type": "text", "text": "这张图里有什么？"},
    {"type": "image_url", "image_url": {"url": image_url}}
])

response = llm.invoke([message])
print(response.content)
```

**示例3：多图对比**

```python
message = HumanMessage(content=[
    {"type": "text", "text": "比较这两张图片的差异"},
    {"type": "image_url", "image_url": {"url": "image1.jpg"}},
    {"type": "image_url", "image_url": {"url": "image2.jpg"}}
])

response = llm.invoke([message])
```

**示例4：用 LCEL 构建多模态链**

```python
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

prompt = ChatPromptTemplate.from_messages([
    ("system", "你是图片分析专家"),
    ("human", [
        {"type": "text", "text": "分析这张图片，回答：{question}"},
        {"type": "image_url", "image_url": "{image_url}"}
    ])
])

chain = prompt | llm | StrOutputParser()

result = chain.invoke({
    "question": "图中有几个人？",
    "image_url": "https://example.com/people.jpg"
})
print(result)
```

**示例5：文档分析（OCR + 理解）**

```python
# 把扫描文档当图片传给模型，让它做 OCR + 理解
def analyze_document(image_path, question):
    image_base64 = encode_image(image_path)
    image_url = f"data:image/png;base64,{image_base64}"
    
    message = HumanMessage(content=[
        {"type": "text", "text": f"这是一份文档图片。{question}"},
        {"type": "image_url", "image_url": {"url": image_url}}
    ])
    
    return llm.invoke([message]).content

# 提取文档中的关键信息
print(analyze_document("contract.png", "提取合同金额和签订日期"))
```

**示例6：使用 Gemini 处理视频**

```python
from langchain_google_genai import ChatGoogleGenerativeAI

gemini = ChatGoogleGenerativeAI(model="gemini-1.5-pro")

# Gemini 支持视频
message = HumanMessage(content=[
    {"type": "text", "text": "描述这个视频的内容"},
    # 视频通过文件 URI 传入
    {"type": "video_file", "video_file": {"file_uri": "..."}}
])

response = gemini.invoke([message])
```

### 总结

- **多模态模型**：能处理文本、图片、音频、视频的 LLM
- **消息格式**：content 可以是字符串或列表（多模态）
- **图片输入**：URL、Base64、文件路径三种方式
- **模型选型**：GPT-4o（图文）、Gemini（全模态）、Claude（图文）
- **应用场景**：图片理解、文档分析、视觉问答、多模态对话
- **注意事项**：多模态调用 token 消耗大，注意成本控制

---

# 第3章 提示工程

提示工程是 LLM 应用的核心技能——好的提示能让模型发挥最大能力，差的提示则导致答非所问。本章深入 LangChain 的提示体系，包括 PromptTemplate、ChatPromptTemplate、FewShot、输出解析器。学完本章，你将能够设计专业、可复用、结构化的提示。

---

## 第9讲：PromptTemplate 基础

### 概念

**PromptTemplate（提示模板）** 是 LangChain 用于动态生成提示的组件。它将固定文本与变量分离——模板中用 `{variable}` 占位，调用时传入变量值，生成最终提示。这让提示可复用、可维护、可测试。

PromptTemplate 适用于纯文本 LLM（LLM 类型），生成的是字符串。对于对话模型（ChatModel），应使用 ChatPromptTemplate（下一讲详解）。

### 原理

**模板与变量分离原理**：传统做法是用字符串拼接生成提示，如 `f"翻译{text}为{language}"`。这种方式简单但难维护——提示变长后，拼接变得混乱，且难以复用。PromptTemplate 把模板和变量分开，模板是固定结构，变量是动态填充，职责清晰。

**格式化原理**：PromptTemplate 内部用 Python 的 `str.format()` 或 Jinja2 模板引擎格式化。`{variable}` 语法是 `str.format()` 风格，简单直观。传入变量字典，替换占位符生成最终字符串。

**输入变量验证原理**：PromptTemplate 在创建时会解析模板，提取所有 `{variable}` 作为 `input_variables`。调用时如果缺少变量会报错，避免运行时发现提示不完整。这是类型安全的设计。

**部分应用原理**：PromptTemplate 支持 `partial()`——先填充部分变量，返回新的模板，剩余变量稍后填充。这在需要分步构造提示时有用，比如先填入系统设定，再填入用户输入。

**与 LCEL 集成原理**：PromptTemplate 实现 Runnable 接口，可以直接用 `|` 与 LLM、Parser 串联。`prompt | llm | parser` 是最经典的 LCEL 链。

### 例子

**示例1：基础 PromptTemplate**

```python
from langchain_core.prompts import PromptTemplate

# 创建模板
prompt = PromptTemplate.from_template(
    "请将以下文本翻译为{language}：\n{text}"
)

# 查看输入变量
print(prompt.input_variables)  # ['language', 'text']

# 格式化
result = prompt.format(language="中文", text="Hello, world!")
print(result)
# 输出：请将以下文本翻译为中文：
# Hello, world!
```

**示例2：在 LCEL 链中使用**

```python
from langchain_openai import ChatOpenAI
from langchain_core.output_parsers import StrOutputParser

llm = ChatOpenAI(model="gpt-4o-mini")

prompt = PromptTemplate.from_template(
    "你是{role}。请回答以下问题：\n{question}"
)

# LCEL 链
chain = prompt | llm | StrOutputParser()

# 调用
answer = chain.invoke({
    "role": "Python 专家",
    "question": "怎么反转列表？"
})
print(answer)
# 输出：使用切片 [::-1] 或 reversed() 函数...
```

**示例3：部分应用（partial）**

```python
# 先填入角色，稍后填入问题
partial_prompt = prompt.partial(role="历史老师")
print(partial_prompt.input_variables)  # ['question']

# 后续调用只需提供 question
chain = partial_prompt | llm | StrOutputParser()
answer = chain.invoke({"question": "讲讲秦始皇"})
```

**示例4：动态部分应用**

```python
from datetime import datetime

# 用函数动态生成变量值
def get_current_date():
    return datetime.now().strftime("%Y-%m-%d")

prompt = PromptTemplate.from_template(
    "今天是{date}。请基于这个日期回答：\n{question}"
)

# 部分应用，date 用函数动态生成
prompt = prompt.partial(date=get_current_date)

chain = prompt | llm | StrOutputParser()
answer = chain.invoke({"question": "明天是星期几？"})
```

**示例5：模板复用**

```python
# 把常用模板保存为变量，多处复用
TRANSLATE_TEMPLATE = PromptTemplate.from_template(
    "将以下{source_lang}文本翻译为{target_lang}，只输出译文：\n{text}"
)

SUMMARIZE_TEMPLATE = PromptTemplate.from_template(
    "用{word_count}字以内总结以下文本：\n{text}"
)

# 在不同链中复用
translate_chain = TRANSLATE_TEMPLATE | llm | StrOutputParser()
summarize_chain = SUMMARIZE_TEMPLATE | llm | StrOutputParser()

print(translate_chain.invoke({
    "source_lang": "英文", "target_lang": "中文", "text": "Hello"
}))
print(summarize_chain.invoke({
    "word_count": 50, "text": "一段长文本..."
}))
```

**示例6：从文件加载模板**

```python
# 保存模板到文件 prompt.txt
# 内容：请将以下文本翻译为{language}：\n{text}

from langchain_core.prompts import load_prompt

# 从文件加载
prompt = load_prompt("prompt.txt")
chain = prompt | llm | StrOutputParser()
```

### 总结

- **PromptTemplate**：模板与变量分离，生成字符串提示
- **格式化**：用 `{variable}` 占位，`format()` 填充
- **输入变量验证**：自动提取变量，缺失会报错
- **部分应用**：`partial()` 分步填充变量
- **LCEL 集成**：实现 Runnable，可用 `|` 串联
- **适用场景**：纯文本 LLM、简单提示、模板复用

---

## 第10讲：ChatPromptTemplate

### 概念

**ChatPromptTemplate（对话提示模板）** 是 LangChain 为对话模型（ChatModel）设计的提示模板。它生成的是消息列表（Message 列表），而非纯字符串。每条消息有角色（System/Human/AI）和内容，符合对话模型的输入格式。

ChatPromptTemplate 是现代 LangChain 应用的首选提示组件——因为现代 LLM（GPT-4、Claude 等）都是对话模型，需要结构化消息输入。

### 原理

**消息列表原理**：对话模型接收的是消息列表，每条消息有 `role`（角色）和 `content`（内容）。ChatPromptTemplate 生成这种结构化输入，而非拼接成字符串。这让模型能区分系统设定、用户输入、AI 回复，生成更准确的响应。

**角色体系原理**：
- **system**：系统消息，设定 AI 的角色、行为规范、约束
- **human**：用户消息
- **ai**：AI 回复（用于 few-shot 示例）
- **placeholder**：占位符，用于插入对话历史

**MessagesPlaceholder 原理**：对话历史是动态的——每轮对话产生新消息。用 `MessagesPlaceholder` 占位，调用时传入消息列表，实现动态历史插入。这是构建多轮对话的关键。

**模板组合原理**：ChatPromptTemplate 可以组合多个消息模板，形成完整对话结构。比如 `[system_msg, history_placeholder, human_msg]` 组成"系统设定 + 历史 + 当前问题"的标准对话结构。

**与 PromptTemplate 对比原理**：PromptTemplate 生成字符串，适合纯文本 LLM；ChatPromptTemplate 生成消息列表，适合对话模型。两者都实现 Runnable，可在 LCEL 中互换，但生成的输出类型不同。

### 例子

**示例1：基础 ChatPromptTemplate**

```python
from langchain_core.prompts import ChatPromptTemplate

# 用元组列表创建
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个{role}，回答专业简洁。"),
    ("human", "{question}")
])

# 格式化生成消息列表
messages = prompt.invoke({"role": "Python 专家", "question": "怎么排序？"})
print(messages)
# 返回 ChatPromptValue，包含消息列表
```

**示例2：在 LCEL 链中使用**

```python
from langchain_openai import ChatOpenAI
from langchain_core.output_parsers import StrOutputParser

llm = ChatOpenAI(model="gpt-4o-mini")

prompt = ChatPromptTemplate.from_messages([
    ("system", "你是{role}。"),
    ("human", "{input}")
])

chain = prompt | llm | StrOutputParser()

answer = chain.invoke({
    "role": "数学老师",
    "input": "解释什么是微积分"
})
print(answer)
```

**示例3：用 MessagesPlaceholder 插入历史**

```python
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
from langchain_core.messages import HumanMessage, AIMessage

prompt = ChatPromptTemplate.from_messages([
    ("system", "你是友好助手。"),
    MessagesPlaceholder(variable_name="history"),
    ("human", "{input}")
])

# 传入历史消息
history = [
    HumanMessage(content="你好"),
    AIMessage(content="你好！有什么可以帮你？"),
]

messages = prompt.invoke({
    "history": history,
    "input": "我刚才说了什么？"
})
# 生成的消息列表：[system, human(你好), ai(你好！), human(我刚才说了什么？)]
```

**示例4：完整对话机器人提示**

```python
prompt = ChatPromptTemplate.from_messages([
    # 系统设定
    ("system", """你是「小智」，一个专业的 AI 助手。

行为规范：
- 回答准确、简洁、有依据
- 不确定时坦诚告知
- 善于用类比解释复杂概念

当前用户：{user_name}"""),
    # 对话历史
    MessagesPlaceholder(variable_name="history"),
    # 当前用户输入
    ("human", "{input}")
])

chain = prompt | llm | StrOutputParser()

# 调用
response = chain.invoke({
    "user_name": "小明",
    "history": [],
    "input": "你好"
})
```

**示例5：Few-shot 示例**

```python
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是情感分析器，输出'正面'或'负面'。"),
    # Few-shot 示例
    ("human", "这个产品太棒了！"),
    ("ai", "正面"),
    ("human", "质量很差，不推荐。"),
    ("ai", "负面"),
    # 实际输入
    ("human", "{input}")
])

chain = prompt | llm | StrOutputParser()
print(chain.invoke({"input": "一般般，凑合用"}))
# 输出：负面（或中性，取决于模型理解）
```

**示例6：动态构造消息**

```python
from langchain_core.messages import SystemMessage

# 根据条件动态构造系统消息
def get_system_prompt(user_type):
    if user_type == "vip":
        return "你是 VIP 专属客服，提供优先服务。"
    return "你是普通客服。"

# 在链中动态构造
from langchain_core.runnables import RunnablePassthrough

chain = (
    {
        "system_msg": lambda x: get_system_prompt(x["user_type"]),
        "input": lambda x: x["input"]
    }
    | (lambda x: ChatPromptTemplate.from_messages([
        ("system", x["system_msg"]),
        ("human", "{input}")
    ]).invoke({"input": x["input"]}))
    | llm
    | StrOutputParser()
)
```

### 总结

- **ChatPromptTemplate**：生成消息列表，适合对话模型
- **角色体系**：system、human、ai、placeholder
- **MessagesPlaceholder**：动态插入对话历史，构建多轮对话的关键
- **模板组合**：多个消息模板组合成完整对话结构
- **Few-shot**：用 human-ai 对话对作为示例
- **推荐使用**：现代应用首选 ChatPromptTemplate

---

## 第11讲：FewShotPromptTemplate

### 概念

**FewShotPromptTemplate（少样本提示模板）** 是 LangChain 用于构建少样本学习提示的组件。它通过在提示中包含少量"输入-输出"示例，让 LLM 学习任务模式，生成更准确的输出。Few-shot 是提示工程的核心技术之一。

Few-shot 的原理是"示例引导"——给 LLM 看几个任务示例，让它理解你想要什么格式的输出，然后对新输入生成符合格式的结果。这比纯指令更有效，尤其适合分类、抽取、格式化等任务。

### 原理

**少样本学习原理**：LLM 是"上下文学习"（In-context Learning）的——它从提示中的示例学习模式，无需微调。给 0 个示例是 zero-shot，给 1 个是 one-shot，给多个是 few-shot。示例越多，LLM 越能理解任务，但也消耗更多 token。

**示例选择原理**：示例质量比数量重要。好的示例应：
1. **代表性**：覆盖任务的典型情况
2. **多样性**：包含不同类型，避免 LLM 过拟合
3. **正确性**：示例答案必须正确
4. **简洁性**：示例不宜过长

**示例选择器原理**：当示例很多时，不能全部放入提示（token 限制）。LangChain 提供 `ExampleSelector`，根据输入动态选择最相关的示例。常见选择器：
- **LengthExampleSelector**：按长度选择，控制总 token
- **SemanticExampleSelector**：按语义相似度选择，选最相关的
- **MaxMarginalRelevanceExampleSelector**：兼顾相关性和多样性

**示例格式原理**：示例需要清晰区分输入和输出。常见格式：
- `输入: ... 输出: ...`
- `Human: ... AI: ...`
- 自定义模板

**与 ChatPromptTemplate 结合原理**：FewShotPromptTemplate 生成字符串，适合 LLM。对于 ChatModel，可以用 ChatPromptTemplate 的 few-shot 模式（上一讲示例5），用 human-ai 对话对作为示例。

### 例子

**示例1：基础 FewShotPromptTemplate**

```python
from langchain_core.prompts import FewShotPromptTemplate, PromptTemplate

# 示例数据
examples = [
    {"input": "高兴", "output": "正面"},
    {"input": "悲伤", "output": "负面"},
    {"input": "愤怒", "output": "负面"},
    {"input": "惊喜", "output": "正面"},
]

# 示例格式模板
example_prompt = PromptTemplate.from_template("输入: {input}\n输出: {output}")

# 创建 FewShot 模板
few_shot_prompt = FewShotPromptTemplate(
    examples=examples,
    example_prompt=example_prompt,
    prefix="分析以下情感，输出'正面'或'负面'：",
    suffix="输入: {input}\n输出:",
    input_variables=["input"]
)

# 格式化
print(few_shot_prompt.format(input="开心"))
# 输出：
# 分析以下情感，输出'正面'或'负面'：
# 输入: 高兴
# 输出: 正面
# 输入: 悲伤
# 输出: 负面
# ...
# 输入: 开心
# 输出:
```

**示例2：在 LCEL 链中使用**

```python
from langchain_openai import ChatOpenAI
from langchain_core.output_parsers import StrOutputParser

llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)

# Few-shot 链
chain = few_shot_prompt | llm | StrOutputParser()

# 测试
print(chain.invoke({"input": "失望"}))   # 负面
print(chain.invoke({"input": "兴奋"}))   # 正面
```

**示例3：用 ExampleSelector 动态选择**

```python
from langchain_core.example_selectors import SemanticSimilarityExampleSelector
from langchain_openai import OpenAIEmbeddings
from langchain_community.vectorstores import FAISS

# 更多示例
examples = [
    {"input": "高兴", "output": "正面"},
    {"input": "悲伤", "output": "负面"},
    {"input": "愤怒", "output": "负面"},
    {"input": "惊喜", "output": "正面"},
    {"input": "沮丧", "output": "负面"},
    {"input": "愉快", "output": "正面"},
    # ... 更多
]

# 语义相似度选择器
example_selector = SemanticSimilarityExampleSelector.from_examples(
    examples,
    OpenAIEmbeddings(),
    FAISS,
    k=2  # 选最相似的2个
)

# 用选择器创建 FewShot
dynamic_prompt = FewShotPromptTemplate(
    example_selector=example_selector,  # 动态选择
    example_prompt=example_prompt,
    prefix="分析情感：",
    suffix="输入: {input}\n输出:",
    input_variables=["input"]
)

# 根据输入动态选择最相关的示例
print(dynamic_prompt.format(input="沮丧"))
# 会选择"悲伤"和"愤怒"等负面示例
```

**示例4：ChatModel 的 Few-shot**

```python
from langchain_core.prompts import ChatPromptTemplate

# 用对话格式做 few-shot
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是翻译器，将中文翻译为英文。"),
    # Few-shot 示例
    ("human", "你好"),
    ("ai", "Hello"),
    ("human", "谢谢"),
    ("ai", "Thank you"),
    ("human", "再见"),
    ("ai", "Goodbye"),
    # 实际输入
    ("human", "{input}")
])

chain = prompt | llm | StrOutputParser()
print(chain.invoke({"input": "早安"}))  # Good morning
```

**示例5：复杂任务 Few-shot——信息抽取**

```python
examples = [
    {
        "input": "张三，25岁，北京人，软件工程师",
        "output": '{"name": "张三", "age": 25, "city": "北京", "job": "软件工程师"}'
    },
    {
        "input": "李四，30岁，上海人，产品经理",
        "output": '{"name": "李四", "age": 30, "city": "上海", "job": "产品经理"}'
    },
]

example_prompt = PromptTemplate.from_template(
    "输入: {input}\n输出: {output}"
)

extract_prompt = FewShotPromptTemplate(
    examples=examples,
    example_prompt=example_prompt,
    prefix="从文本中提取信息，输出 JSON 格式。",
    suffix="输入: {input}\n输出:",
    input_variables=["input"]
)

chain = extract_prompt | llm | StrOutputParser()
result = chain.invoke({"input": "王五，28岁，广州人，设计师"})
print(result)
# {"name": "王五", "age": 28, "city": "广州", "job": "设计师"}
```

### 总结

- **Few-shot**：用示例引导 LLM 学习任务模式
- **示例质量**：代表性、多样性、正确性、简洁性
- **ExampleSelector**：动态选择最相关示例，控制 token
- **选择器类型**：Length、Semantic、MMR
- **ChatModel few-shot**：用 human-ai 对话对作为示例
- **适用场景**：分类、抽取、格式化、翻译等结构化任务

---

## 第12讲：输出解析器 Output Parser

### 概念

**输出解析器（Output Parser）** 是 LangChain 用于将 LLM 的文本输出解析为结构化数据的组件。LLM 返回的是文本，但应用通常需要结构化对象（如字典、列表、Pydantic 模型）。输出解析器填补这一鸿沟，让 LLM 输出可直接用于程序逻辑。

LangChain 提供多种解析器：`StrOutputParser`（提取纯文本）、`JsonOutputParser`（解析 JSON）、`PydanticOutputParser`（解析为 Pydantic 模型）、`CommaSeparatedListOutputParser`（解析逗号分隔列表）等。

### 原理

**解析器的作用原理**：LLM 输出是 `AIMessage` 对象，`content` 是字符串。解析器接收这个字符串，按规则解析为目标类型。比如 JSON 解析器把 `'{"name": "张三"}'` 解析为 `{"name": "张三"}` 字典。

**格式指令原理**：高级解析器（如 PydanticOutputParser）能生成"格式指令"——告诉 LLM 应该输出什么格式。这些指令可以加入提示，引导 LLM 输出可解析的内容。这是"提示 + 解析"的协同设计。

**错误处理与重试原理**：LLM 输出可能不符合预期格式（如 JSON 缺括号）。LangChain 的解析器能检测错误，并通过 `OutputFixingParser` 或 `RetryOutputParser` 自动修复或重试。这大幅提升了鲁棒性。

**LCEL 集成原理**：解析器实现 Runnable 接口，可以用 `|` 串联在 LLM 之后。`prompt | llm | parser` 是标准模式——提示生成输入、LLM 生成文本、解析器转为结构化数据。

**Pydantic 优势原理**：PydanticOutputParser 用 Pydantic 模型定义输出结构，提供：
1. **类型验证**：字段类型不对会报错
2. **字段约束**：可加约束（如 min_length）
3. **文档生成**：自动生成字段说明给 LLM
4. **IDE 补全**：Pydantic 模型有类型提示

### 例子

**示例1：StrOutputParser（最常用）**

```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

llm = ChatOpenAI(model="gpt-4o-mini")
parser = StrOutputParser()

prompt = ChatPromptTemplate.from_messages([
    ("human", "{question}")
])

# StrOutputParser 提取 AIMessage 的 content
chain = prompt | llm | parser

result = chain.invoke({"question": "你好"})
print(type(result))  # <class 'str'>
print(result)  # 你好！有什么可以帮你？
```

**示例2：JsonOutputParser**

```python
from langchain_core.output_parsers import JsonOutputParser
from langchain_core.prompts import PromptTemplate

# 定义输出结构（用 Pydantic）
from pydantic import BaseModel

class Person(BaseModel):
    name: str
    age: int
    city: str

parser = JsonOutputParser(pydantic_object=Person)

# 获取格式指令，加入提示
prompt = PromptTemplate(
    template="从以下文本提取人物信息。\n{format_instructions}\n{text}",
    input_variables=["text"],
    partial_variables={"format_instructions": parser.get_format_instructions()}
)

chain = prompt | llm | parser

result = chain.invoke({"text": "张三，25岁，住在北京"})
print(type(result))  # <class 'dict'>
print(result)  # {'name': '张三', 'age': 25, 'city': '北京'}
```

**示例3：PydanticOutputParser**

```python
from langchain_core.output_parsers import PydanticOutputParser
from pydantic import BaseModel, Field

class BookReview(BaseModel):
    title: str = Field(description="书名")
    rating: int = Field(description="评分1-5")
    summary: str = Field(description="一句话评价")
    recommend: bool = Field(description="是否推荐")

parser = PydanticOutputParser(pydantic_object=BookReview)

prompt = ChatPromptTemplate.from_template(
    "写一条书评。\n{format_instructions}\n书名：{book}"
).partial(format_instructions=parser.get_format_instructions())

chain = prompt | llm | parser

result = chain.invoke({"book": "Python编程从入门到实践"})
print(type(result))  # <class 'BookReview'>
print(result.title)    # Python编程从入门到实践
print(result.rating)    # 5
print(result.recommend) # True
```

**示例4：CommaSeparatedListOutputParser**

```python
from langchain_core.output_parsers import CommaSeparatedListOutputParser

parser = CommaSeparatedListOutputParser()

prompt = ChatPromptTemplate.from_template(
    "列出{n}个{category}。\n{format_instructions}"
).partial(format_instructions=parser.get_format_instructions())

chain = prompt | llm | parser

result = chain.invoke({"n": 5, "category": "编程语言"})
print(type(result))  # <class 'list'>
print(result)  # ['Python', 'JavaScript', 'Java', 'C++', 'Go']
```

**示例5：错误修复解析器**

```python
from langchain_core.output_parsers import OutputFixingParser

# 如果 LLM 输出的 JSON 有问题，自动修复
fixing_parser = OutputFixingParser.from_llm(parser=parser, llm=llm)

chain = prompt | llm | fixing_parser
# 即使 LLM 输出格式略有错误，也能自动修复并解析
```

**示例6：自定义解析器**

```python
from langchain_core.output_parsers import BaseOutputParser

class BooleanOutputParser(BaseOutputParser):
    """解析是/否为布尔值"""
    
    def parse(self, text: str) -> bool:
        text = text.strip().lower()
        if "是" in text or "yes" in text or "true" in text:
            return True
        return False
    
    @property
    def _type(self) -> str:
        return "boolean"

# 使用
parser = BooleanOutputParser()
prompt = ChatPromptTemplate.from_template("问题是{question}，请回答是或否。")
chain = prompt | llm | parser

result = chain.invoke({"question": "Python是编程语言吗"})
print(type(result))  # <class 'bool'>
print(result)  # True
```

### 总结

- **输出解析器**：把 LLM 文本输出转为结构化数据
- **StrOutputParser**：提取纯文本，最常用
- **JsonOutputParser**：解析 JSON，配合 Pydantic 定义结构
- **PydanticOutputParser**：类型验证、字段约束、文档生成
- **格式指令**：解析器生成指令加入提示，引导 LLM 输出
- **错误修复**：OutputFixingParser 自动修复格式错误
- **自定义解析器**：继承 BaseOutputParser 实现特定逻辑

---

# 第4章 LCEL 表达式语言

LCEL（LangChain Expression Language）是 LangChain 的现代核心——一种声明式语法，用 `|` 操作符组合组件，构建复杂工作流。本章深入 LCEL 的设计哲学和用法，包括 Runnable 接口、链式调用、并行批处理、流式异步。学完本章，你将能够用简洁语法构建强大的 LLM 工作流。

---

## 第13讲：LCEL 与 Runnable

### 概念

**LCEL（LangChain Expression Language）** 是 LangChain 0.1 引入的声明式语法，用 `|` 操作符组合组件。`prompt | llm | parser` 这种写法就是 LCEL——它让链式组合变得直观，同时自动支持批处理、流式、异步、重试等特性。

**Runnable** 是 LCEL 的基础接口——所有 LangChain 组件（Prompt、LLM、Parser、Retriever 等）都实现 Runnable 接口，提供统一的 `invoke`、`batch`、`stream`、`ainvoke` 等方法。Runnable 是 LCEL 能用 `|` 组合的前提。

### 原理

**Runnable 统一接口原理**：在 LCEL 之前，LangChain 有各种不同接口的组件（LLM 用 `__call__`，Chain 用 `run`，Tool 用 `run` 等），组合时需要适配。LCEL 引入 Runnable 接口，所有组件统一提供：
- `invoke(input)`：单个调用
- `batch(inputs)`：批量调用
- `stream(input)`：流式调用
- `ainvoke/abatch/astream`：异步版本

这让任何组件都能用相同方式调用，也能用 `|` 串联。

**`|` 操作符原理**：`a | b` 创建一个 `RunnableSequence`，执行时先运行 `a`，把输出传给 `b`。这是函数组合的语法糖——`a | b` 等价于 `RunnableSequence(a, b)`，执行时等价于 `b(a.invoke(input))`。`|` 让组合变得直观，像 Unix 管道一样。

**自动特性原理**：LCEL 链自动获得以下能力：
1. **批处理**：`chain.batch([input1, input2])` 自动并发
2. **流式**：`chain.stream(input)` 逐 token 输出
3. **异步**：`chain.ainvoke(input)` 异步调用
4. **重试**：配置 fallback 自动重试
5. **追踪**：自动接入 LangSmith 追踪

这些能力不需要额外代码，LCEL 在底层处理。

**类型安全原理**：LCEL 链有输入输出类型。`prompt | llm | parser` 的类型是 `dict → str`（输入字典，输出字符串）。如果类型不匹配（如把输出字符串的组件接到输入要字典的组件前），LCEL 会报错。这让错误在开发时就能发现。

**RunnablePassthrough 原理**：`RunnablePassthrough` 是一个特殊 Runnable，它接收输入、原样传递到输出。在多分支链中，用于"传递"某些字段到下游，不修改。比如同时传给 LLM 和保留原始输入。

### 例子

**示例1：基础 LCEL 链**

```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

llm = ChatOpenAI(model="gpt-4o-mini")

# LCEL 链：prompt | llm | parser
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是{role}"),
    ("human", "{input}")
])

chain = prompt | llm | StrOutputParser()

# 调用
result = chain.invoke({"role": "助手", "input": "你好"})
print(result)  # 你好！有什么可以帮你？
```

**示例2：Runnable 接口**

```python
# 所有组件都是 Runnable
from langchain_core.runnables import RunnableLambda, RunnablePassthrough

# RunnableLambda：把普通函数转为 Runnable
def to_upper(text: str) -> str:
    return text.upper()

upper_runnable = RunnableLambda(to_upper)

# 可以用 invoke
print(upper_runnable.invoke("hello"))  # HELLO

# 可以用 | 串联
chain = upper_runnable | llm | StrOutputParser()
# 注意类型：str → AIMessage → str
```

**示例3：RunnablePassthrough 传递字段**

```python
from langchain_core.runnables import RunnablePassthrough

# 同时传给 LLM 和保留原始输入
chain = RunnablePassthrough.assign(
    # 生成回复
    response=ChatPromptTemplate.from_template("回复：{input}") | llm | StrOutputParser()
)

result = chain.invoke({"input": "你好"})
print(result)
# {'input': '你好', 'response': '你好！有什么可以帮你？'}
```

**示例4：并行 Runnable**

```python
from langchain_core.runnables import RunnableParallel

# 并行执行多个链
chain = RunnableParallel(
    # 同时做翻译和摘要
    translate=ChatPromptTemplate.from_template("翻译为英文：{text}") | llm | StrOutputParser(),
    summarize=ChatPromptTemplate.from_template("用一句话总结：{text}") | llm | StrOutputParser()
)

result = chain.invoke({"text": "LangChain 是一个 LLM 应用框架，提供组件化开发能力。"})
print(result)
# {
#   'translate': 'LangChain is an LLM application framework...',
#   'summarize': 'LangChain 提供组件化 LLM 开发。'
# }
```

**示例5：用 lambda 自定义处理**

```python
# 在链中插入自定义逻辑
chain = (
    {
        # 用 lambda 处理输入
        "question": lambda x: x["input"],
        "context": lambda x: x.get("context", "")
    }
    | ChatPromptTemplate.from_messages([
        ("system", "基于上下文回答：{context}"),
        ("human", "{question}")
    ])
    | llm
    | StrOutputParser()
)

result = chain.invoke({
    "input": "什么是 LangChain？",
    "context": "LangChain 是 LLM 应用框架"
})
```

**示例6：链的类型检查**

```python
# 查看链的输入输出类型
print(chain.input_schema.schema())
print(chain.output_schema.schema())

# LCEL 会在组合时检查类型兼容性
# 如果类型不匹配会报错
```

### 总结

- **LCEL**：声明式语法，用 `|` 组合组件
- **Runnable**：统一接口，所有组件实现 invoke/batch/stream
- **`|` 操作符**：函数组合的语法糖，等价于 RunnableSequence
- **自动特性**：批处理、流式、异步、重试、追踪
- **类型安全**：链有输入输出类型，不匹配会报错
- **RunnablePassthrough**：传递字段，用于多分支
- **RunnableParallel**：并行执行多个链

---

## 第14讲：链式调用

### 概念

**链式调用（Chaining）** 是 LCEL 的核心用法——用 `|` 把多个组件串联成处理流水线。数据从第一个组件流入，经过每个组件处理，最终从最后一个组件流出。链式调用让复杂工作流变得直观可读。

本讲深入链式调用的进阶用法，包括：多组件串联、条件分支、数据转换、链的组合与嵌套。

### 原理

**数据流原理**：LCEL 链是数据流——输入数据经过每个组件处理，类型可能变化。比如 `dict →(prompt)→ messages →(llm)→ AIMessage →(parser)→ str`。每个组件的输出类型必须与下一个组件的输入类型匹配，否则报错。

**RunnableSequence 原理**：`a | b | c` 创建一个 RunnableSequence，内部按顺序执行 a、b、c。每个组件的输出作为下一个的输入。RunnableSequence 本身也是 Runnable，可以继续用 `|` 串联，形成更长的链。

**字典作为中间数据原理**：复杂链通常用字典作为组件间的数据载体——每个组件接收字典、返回字典。这样多个字段可以并行传递，下游组件按需取用。比如 `{"question": ..., "context": ...}` 同时传给需要 question 的组件和需要 context 的组件。

**RunnablePassthrough.assign 原理**：`RunnablePassthrough.assign(**new_fields)` 接收输入字典，添加新字段，返回扩展后的字典。新字段的值可以是 Runnable（会执行）或静态值。这是"在传递中添加字段"的常用模式。

**链的嵌套原理**：链本身是 Runnable，可以作为另一个链的组件。`outer_chain = inner_chain | another_component`。这让复杂工作流可以模块化——把子链封装为可复用单元。

### 例子

**示例1：多组件串联**

```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnableLambda

llm = ChatOpenAI(model="gpt-4o-mini")

# 多步骤链：分析 → 生成 → 格式化
chain = (
    # 步骤1：分析意图
    ChatPromptTemplate.from_template("分析意图，输出'技术'或'闲聊'：{input}")
    | llm
    | StrOutputParser()
    # 步骤2：根据意图构造提示
    | RunnableLambda(lambda intent: {
        "intent": intent,
        "prompt": f"你是技术专家" if "技术" in intent else f"你是聊天助手"
    })
    # 步骤3：生成回复
    | (lambda x: ChatPromptTemplate.from_messages([
        ("system", x["prompt"]),
        ("human", "{input}")  # 注意：这里需要原始输入
    ]))
    # 这里有个问题：我们丢失了原始输入
)
```

**示例2：用字典保留多字段**

```python
# 正确做法：用字典传递所有需要的字段
chain = (
    # 第一步：构造包含原始输入和意图的字典
    {
        "input": RunnableLambda(lambda x: x["input"]),
        "intent": ChatPromptTemplate.from_template("分析意图(技术/闲聊)：{input}")
                   | llm | StrOutputParser()
    }
    # 第二步：基于意图生成回复
    | (lambda x: ChatPromptTemplate.from_messages([
        ("system", "你是技术专家" if "技术" in x["intent"] else "你是聊天助手"),
        ("human", x["input"])
    ]))
    | llm
    | StrOutputParser()
)

result = chain.invoke({"input": "怎么写 Python？"})
print(result)
```

**示例3：RunnablePassthrough.assign**

```python
from langchain_core.runnables import RunnablePassthrough

# 在传递中添加字段
chain = RunnablePassthrough.assign(
    # 添加 word_count 字段
    word_count=lambda x: len(x["input"].split())
).assign(
    # 添加 response 字段（用链生成）
    response=ChatPromptTemplate.from_template("回答：{input}") | llm | StrOutputParser()
)

result = chain.invoke({"input": "什么是 LangChain"})
print(result)
# {'input': '什么是 LangChain', 'word_count': 2, 'response': 'LangChain 是...'}
```

**示例4：链的嵌套与复用**

```python
# 定义可复用的子链
translate_chain = (
    ChatPromptTemplate.from_template("翻译为{language}：{text}")
    | llm
    | StrOutputParser()
)

summarize_chain = (
    ChatPromptTemplate.from_template("一句话总结：{text}")
    | llm
    | StrOutputParser()
)

# 组合子链
full_chain = (
    # 先翻译
    {
        "translated": lambda x: translate_chain.invoke({"text": x["text"], "language": "中文"}),
        "original": lambda x: x["text"]
    }
    # 再对翻译结果做摘要
    | {
        "translation": lambda x: x["translated"],
        "summary": lambda x: summarize_chain.invoke({"text": x["translated"]})
    }
)

result = full_chain.invoke({"text": "LangChain is a framework"})
print(result)
# {'translation': 'LangChain 是一个框架', 'summary': 'LangChain 是 LLM 框架'}
```

**示例5：条件分支**

```python
from langchain_core.runnables import RunnableBranch

# 根据条件选择不同链
branch = RunnableBranch(
    # (条件函数, 处理链)
    (lambda x: "技术" in x["type"], ChatPromptTemplate.from_template("技术回答：{q}") | llm),
    (lambda x: "闲聊" in x["type"], ChatPromptTemplate.from_template("闲聊回答：{q}") | llm),
    # 默认分支
    ChatPromptTemplate.from_template("通用回答：{q}") | llm
)

chain = branch | StrOutputParser()
result = chain.invoke({"type": "技术", "q": "什么是 Python？"})
```

**示例6：链的调试**

```python
# 用 with_config 添加调试信息
chain = (
    prompt
    | llm.with_config({"run_name": "主LLM"})
    | StrOutputParser()
).with_config({"run_name": "主链"})

# 查看链结构
print(chain.get_graph().draw_ascii())
```

### 总结

- **数据流**：输入经过组件处理，类型可能变化
- **字典载体**：复杂链用字典传递多字段
- **RunnablePassthrough.assign**：在传递中添加字段
- **链嵌套**：链本身是 Runnable，可嵌套复用
- **RunnableBranch**：条件分支，根据输入选择不同链
- **调试**：用 with_config 添加 run_name，用 get_graph 查看结构

---

## 第15讲：并行与批处理

### 概念

**并行（Parallel）** 和 **批处理（Batch）** 是 LCEL 提升效率的两大能力。并行指同时执行多个组件（如同时翻译和摘要），批处理指同时处理多个输入（如同时翻译10段文本）。LCEL 让这两种优化几乎零成本——只需调用 `batch()` 或用 `RunnableParallel`。

### 原理

**并行执行原理**：`RunnableParallel`（或字典语法 `{"a": chain_a, "b": chain_b}`）让多个链同时执行，结果汇总为字典。底层用线程池/异步并发，大幅缩短总时间。适合无依赖的多个任务。

**批处理原理**：`chain.batch([input1, input2, ...])` 让链同时处理多个输入。LCEL 内部用并发机制（默认线程池，异步链用 asyncio）并行调用 LLM。这比循环 `invoke` 快得多——10 个输入可能只需 2-3 倍单次时间，而非 10 倍。

**并发控制原理**：批处理默认并发数有限（避免触发 API 限流）。可通过 `max_concurrency` 参数调整。对于 LLM API，建议并发数 5-10，避免触发 rate limit。

**并行 vs 批处理原理**：
- **并行**：一个输入，多个组件同时处理，产出多个结果
- **批处理**：多个输入，一个链同时处理，产出多个结果

两者可以组合——并行批处理多个输入的多个任务。

**异步原理**：LCEL 链支持异步（`ainvoke`、`abatch`、`astream`）。异步链用 asyncio 并发，比线程池更高效。对于 IO 密集型任务（如调用 LLM API），异步能显著提升吞吐。

### 例子

**示例1：并行执行多个任务**

```python
from langchain_core.runnables import RunnableParallel
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

llm = ChatOpenAI(model="gpt-4o-mini")

# 并行：同时翻译和摘要
chain = RunnableParallel(
    translate=ChatPromptTemplate.from_template("翻译为英文：{text}") | llm | StrOutputParser(),
    summarize=ChatPromptTemplate.from_template("一句话总结：{text}") | llm | StrOutputParser(),
    keywords=ChatPromptTemplate.from_template("提取3个关键词：{text}") | llm | StrOutputParser()
)

result = chain.invoke({"text": "LangChain 是 LLM 应用框架，支持组件化开发。"})
print(result)
# {
#   'translate': 'LangChain is an LLM application framework...',
#   'summarize': 'LangChain 支持组件化 LLM 开发。',
#   'keywords': 'LangChain, LLM, 框架'
# }
```

**示例2：字典语法实现并行**

```python
# 字典语法等价于 RunnableParallel
chain = {
    "translate": ChatPromptTemplate.from_template("翻译：{text}") | llm | StrOutputParser(),
    "summary": ChatPromptTemplate.from_template("总结：{text}") | llm | StrOutputParser()
}

result = chain.invoke({"text": "Hello world"})
# 等价于 RunnableParallel
```

**示例3：批处理多个输入**

```python
# 串行 vs 批处理对比
import time

prompt = ChatPromptTemplate.from_template("翻译为英文：{text}")
chain = prompt | llm | StrOutputParser()

texts = [
    {"text": "你好"},
    {"text": "谢谢"},
    {"text": "再见"},
    {"text": "早安"},
    {"text": "晚安"}
]

# 串行（慢）
start = time.time()
serial_results = [chain.invoke(t) for t in texts]
print(f"串行时间：{time.time()-start:.2f}s")

# 批处理（快）
start = time.time()
batch_results = chain.batch(texts)
print(f"批处理时间：{time.time()-start:.2f}s")

# 批处理 + 并发控制
start = time.time()
batch_results = chain.batch(texts, config={"max_concurrency": 5})
print(f"并发批处理：{time.time()-start:.2f}s")
```

**示例4：并行 + 批处理**

```python
# 对多个输入，每个都做并行任务
chain = RunnableParallel(
    translate=ChatPromptTemplate.from_template("翻译：{text}") | llm | StrOutputParser(),
    sentiment=ChatPromptTemplate.from_template("情感(正/负)：{text}") | llm | StrOutputParser()
)

# 批处理
results = chain.batch([
    {"text": "今天天气真好"},
    {"text": "我很难过"},
    {"text": "一般般"}
])
for r in results:
    print(r)
# {'translate': 'The weather is nice today', 'sentiment': '正面'}
# {'translate': 'I am sad', 'sentiment': '负面'}
# ...
```

**示例5：异步批处理**

```python
import asyncio

# 异步链
async def async_process():
    results = await chain.abatch([
        {"text": "你好"},
        {"text": "谢谢"},
        {"text": "再见"}
    ], config={"max_concurrency": 10})
    return results

results = asyncio.run(async_process())
```

**示例6：实际场景——批量处理文档**

```python
# 批量处理多个文档
documents = [
    "LangChain 是 LLM 框架",
    "LangGraph 是 Agent 框架",
    "LangSmith 是观测平台"
]

summarize_chain = ChatPromptTemplate.from_template("一句话总结：{text}") | llm | StrOutputParser()

# 批量摘要
summaries = summarize_chain.batch([{"text": doc} for doc in documents])
for doc, summary in zip(documents, summaries):
    print(f"原文：{doc}")
    print(f"摘要：{summary}\n")
```

### 总结

- **并行**：一个输入，多个组件同时处理（RunnableParallel）
- **批处理**：多个输入，一个链同时处理（batch）
- **并发控制**：max_concurrency 参数，避免 API 限流
- **异步**：ainvoke/abatch/astream，IO 密集型更高效
- **组合使用**：并行 + 批处理，最大化吞吐
- **性能提升**：批处理比循环 invoke 快数倍

---

## 第16讲：流式与异步

### 概念

**流式（Streaming）** 和 **异步（Async）** 是 LCEL 的高级能力。流式让链能逐 token 输出，提升用户体验；异步让链能高并发执行，提升吞吐。LCEL 的设计让这些能力几乎零成本——只要链中组件支持，整个链就自动支持。

本讲深入 LCEL 的流式和异步机制，包括：stream 方法、astream 方法、流式传递、异步并发。

### 原理

**LCEL 流式传递原理**：LCEL 链的流式能力是"传递"的——如果链中所有组件都支持流式输出，整个链就能流式。比如 `prompt | llm | parser`，Prompt 流式输出消息、LLM 流式输出 chunk、Parser 流式处理 chunk。这种"流式管道"让用户能实时看到生成过程。

**AIMessageChunk 原理**：LLM 流式输出时，每次 yield 一个 `AIMessageChunk`，包含这一步的文本片段。多个 chunk 可以累加（`chunk1 + chunk2`）得到完整消息。Parser 接收 chunk 流，逐个处理。

**异步原理**：LCEL 链支持异步接口——`ainvoke`、`abatch`、`astream`。异步链用 asyncio 并发，不阻塞主线程。对于 IO 密集型任务（如调用 LLM API），异步能显著提升吞吐——一个事件循环能同时处理成百上千个请求。

**同步 vs 异步原理**：同步接口（invoke）阻塞当前线程直到完成；异步接口（ainvoke）立即返回 Future，完成后回调。对于单次调用，两者差不多；对于高并发（如 Web 服务），异步能处理更多请求。

**流式 + 异步原理**：`astream` 是异步流式——异步生成 token，逐个 yield。这是最高效的流式方式，适合高并发实时场景。

### 例子

**示例1：链的流式输出**

```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

llm = ChatOpenAI(model="gpt-4o-mini", streaming=True)

chain = ChatPromptTemplate.from_template("用100字介绍{topic}") | llm | StrOutputParser()

# 流式输出
print("回复：", end="")
for chunk in chain.stream({"topic": "LangChain"}):
    print(chunk, end="", flush=True)
# 逐字输出完整介绍
```

**示例2：流式 + 并行**

```python
from langchain_core.runnables import RunnableParallel

# 并行链的流式
chain = RunnableParallel(
    translate=ChatPromptTemplate.from_template("翻译：{text}") | llm | StrOutputParser(),
    summary=ChatPromptTemplate.from_template("总结：{text}") | llm | StrOutputParser()
)

# 流式输出（两个链同时流式）
for chunk in chain.stream({"text": "LangChain 是 LLM 框架"}):
    print(chunk)
    # 输出：{'translate': 'Lang', 'summary': 'Lang'}
    #       {'translate': 'Chain', 'summary': 'Chain 是'}
    #       ...
```

**示例3：异步调用**

```python
import asyncio

async def async_main():
    # 异步调用
    result = await chain.ainvoke({"topic": "Python"})
    print(result)
    
    # 异步批处理
    results = await chain.abatch([
        {"topic": "Python"},
        {"topic": "Java"},
        {"topic": "Rust"}
    ])
    for r in results:
        print(r[:50])

asyncio.run(async_main())
```

**示例4：异步流式**

```python
async def async_stream():
    # 异步流式
    async for chunk in chain.astream({"topic": "LangChain"}):
        print(chunk, end="", flush=True)

asyncio.run(async_stream())
```

**示例5：流式处理工具调用**

```python
# 流式输出时处理工具调用
llm_with_tools = llm.bind_tools([...])

async for chunk in llm_with_tools.astream("搜索 LangChain"):
    if chunk.content:
        print(chunk.content, end="", flush=True)
    # 工具调用信息也在 chunk 中
```

**示例6：Web 服务中的异步流式**

```python
# FastAPI 中的异步流式
from fastapi import FastAPI
from fastapi.responses import StreamingResponse

app = FastAPI()

@app.post("/chat")
async def chat(request: dict):
    async def event_stream():
        async for chunk in chain.astream({"topic": request["topic"]}):
            yield f"data: {chunk}\n\n"
    
    return StreamingResponse(event_stream(), media_type="text/event-stream")
```

### 总结

- **流式传递**：链中组件都支持流式，整个链自动流式
- **AIMessageChunk**：流式输出的片段，可累加
- **异步接口**：ainvoke、abatch、astream
- **异步优势**：高并发场景吞吐更高
- **astream**：异步流式，最高效的实时输出
- **Web 场景**：FastAPI + astream + SSE 实现实时聊天

---

# 第5章 链与记忆

本章探讨 LangChain 的经典链结构和对话记忆机制。虽然 LCEL 是现代推荐方式，但了解经典链有助于理解 LangChain 的演进。记忆机制让应用具备上下文感知能力，是构建对话机器人的关键。学完本章，你将能够构建具备记忆的多轮对话应用。

---

## 第17讲：LLMChain

### 概念

**LLMChain** 是 LangChain 最经典的链结构——将 Prompt、LLM、OutputParser 组合为一条链。在 LCEL 出现之前，LLMChain 是构建 LLM 应用的标准方式。虽然现代推荐用 LCEL（`prompt | llm | parser`），但 LLMChain 仍在大量代码中使用，理解它有助于阅读旧代码和使用某些高级功能。

### 原理

**LLMChain 的封装原理**：LLMChain 把 Prompt + LLM + Parser 封装为一个类，提供 `run()`、`predict()`、`invoke()` 等方法。内部逻辑是：用 Prompt 格式化输入 → 调用 LLM → 解析输出。这与 LCEL 的 `prompt | llm | parser` 本质相同，只是封装方式不同。

**与 LCEL 对比原理**：
- **LLMChain**：面向对象，配置式，适合简单场景
- **LCEL**：函数式，声明式，适合复杂组合

LCEL 更灵活、更强大，是现代推荐。但 LLMChain 在某些场景仍方便——比如需要快速原型、或使用某些只支持 LLMChain 的功能。

**LLMChain 的输入输出原理**：输入是变量字典（如 `{"input": "你好"}`），输出是字符串（默认）或解析后的结构。LLMChain 内部用 Prompt 的 `input_variables` 验证输入。

**为什么仍要学 LLMChain 原理**：
1. 大量旧代码使用 LLMChain
2. 某些教程和示例仍用 LLMChain
3. 某些高级功能（如 Memory 集成）在 LLMChain 中更方便
4. 理解 LLMChain 有助于理解 LangChain 演进

### 例子

**示例1：基础 LLMChain**

```python
from langchain.chains import LLMChain
from langchain_openai import ChatOpenAI
from langchain_core.prompts import PromptTemplate

llm = ChatOpenAI(model="gpt-4o-mini")

prompt = PromptTemplate.from_template("翻译以下文本为{language}：{text}")

# 创建 LLMChain
chain = LLMChain(
    llm=llm,
    prompt=prompt,
    output_key="translation"  # 输出键名
)

# 调用
result = chain.run(language="中文", text="Hello")
# 或
result = chain.invoke({"language": "中文", "text": "Hello"})
print(result["translation"])
```

**示例2：LLMChain vs LCEL 对比**

```python
# LLMChain 方式
llm_chain = LLMChain(llm=llm, prompt=prompt)
result1 = llm_chain.invoke({"language": "中文", "text": "Hello"})

# LCEL 方式（推荐）
lcel_chain = prompt | llm | StrOutputParser()
result2 = lcel_chain.invoke({"language": "中文", "text": "Hello"})

# 两者输出本质相同，LCEL 更简洁
```

**示例3：带输出解析器的 LLMChain**

```python
from langchain_core.output_parsers import PydanticOutputParser
from pydantic import BaseModel

class Translation(BaseModel):
    translated_text: str
    confidence: float

parser = PydanticOutputParser(pydantic_object=Translation)

prompt = PromptTemplate(
    template="翻译为{language}。\n{format_instructions}\n{text}",
    input_variables=["language", "text"],
    partial_variables={"format_instructions": parser.get_format_instructions()}
)

# LLMChain 不直接支持 parser，需要手动解析
chain = LLMChain(llm=llm, prompt=prompt)
result = chain.invoke({"language": "中文", "text": "Hello"})
parsed = parser.parse(result["text"])
print(parsed.translated_text)
```

**示例4：LLMChain 与 Memory 集成**

```python
from langchain.memory import ConversationBufferMemory

# LLMChain 方便地集成 Memory
memory = ConversationBufferMemory()

chain = LLMChain(
    llm=llm,
    prompt=PromptTemplate.from_template("历史：{history}\n输入：{input}"),
    memory=memory
)

# 多轮对话，memory 自动管理
chain.run(input="你好")
chain.run(input="我刚才说了什么？")
# LLM 能回忆起"你好"
```

### 总结

- **LLMChain**：经典链结构，封装 Prompt + LLM + Parser
- **与 LCEL 对比**：LLMChain 面向对象，LCEL 函数式
- **现代推荐**：LCEL 更灵活强大，是首选
- **仍学 LLMChain**：旧代码、某些功能、理解演进
- **Memory 集成**：LLMChain 方便集成 Memory
- **迁移建议**：新项目用 LCEL，旧项目可逐步迁移

---

## 第18讲：SequentialChain

### 概念

**SequentialChain（顺序链）** 是将多个链按顺序串联的机制——链 A 的输出作为链 B 的输入，链 B 的输出作为链 C 的输入。适合多步骤任务，如"翻译 → 摘要 → 格式化"。

在 LCEL 时代，顺序链可以用 `|` 更简洁地实现。但理解 SequentialChain 有助于理解复杂工作流的组织方式。

### 原理

**顺序执行原理**：SequentialChain 按定义顺序执行子链，每个子链的输出变量作为下一个子链的输入。通过 `input_key` 和 `output_key` 明确数据流。

**变量传递原理**：每个子链有输入键和输出键。SequentialChain 维护一个全局变量字典，每个子链读取所需输入键、写入输出键。这种"变量共享"让多个链能协作。

**SimpleSequentialChain 原理**：SimpleSequentialChain 是 SequentialChain 的简化版——每个链只有一个输入和一个输出，自动串联。适合简单的线性流程。

**与 LCEL 对比原理**：LCEL 的 `chain1 | chain2 | chain3` 更简洁地实现顺序链。SequentialChain 需要显式定义输入输出键，更繁琐但更明确。

### 例子

**示例1：SimpleSequentialChain**

```python
from langchain.chains import SimpleSequentialChain, LLMChain

# 链1：生成故事
story_chain = LLMChain(
    llm=llm,
    prompt=PromptTemplate.from_template("写一个关于{topic}的短故事")
)

# 链2：翻译故事
translate_chain = LLMChain(
    llm=llm,
    prompt=PromptTemplate.from_template("翻译为英文：{story}")
)

# 顺序链
overall_chain = SimpleSequentialChain(
    chains=[story_chain, translate_chain]
)

result = overall_chain.run("一只猫")
# 先生成故事，再翻译
```

**示例2：SequentialChain（带变量名）**

```python
from langchain.chains import SequentialChain

# 链1：生成故事，输出 story
chain1 = LLMChain(
    llm=llm,
    prompt=PromptTemplate.from_template("写关于{topic}的故事"),
    output_key="story"
)

# 链2：摘要故事，输入 story，输出 summary
chain2 = LLMChain(
    llm=llm,
    prompt=PromptTemplate.from_template("一句话总结：{story}"),
    output_key="summary"
)

# 链3：评价摘要，输入 summary，输出 review
chain3 = LLMChain(
    llm=llm,
    prompt=PromptTemplate.from_template("评价这个摘要：{summary}"),
    output_key="review"
)

# 顺序链
overall_chain = SequentialChain(
    chains=[chain1, chain2, chain3],
    input_variables=["topic"],
    output_variables=["story", "summary", "review"]
)

result = overall_chain.invoke({"topic": "太空探险"})
print(result["story"])
print(result["summary"])
print(result["review"])
```

**示例3：用 LCEL 实现顺序链（推荐）**

```python
from langchain_core.runnables import RunnablePassthrough

# LCEL 方式更简洁
chain = (
    # 步骤1：生成故事
    RunnablePassthrough.assign(
        story=ChatPromptTemplate.from_template("写关于{topic}的故事") | llm | StrOutputParser()
    )
    # 步骤2：摘要
    .assign(
        summary=ChatPromptTemplate.from_template("总结：{story}") | llm | StrOutputParser()
    )
    # 步骤3：评价
    .assign(
        review=ChatPromptTemplate.from_template("评价：{summary}") | llm | StrOutputParser()
    )
)

result = chain.invoke({"topic": "太空探险"})
print(result["story"])
print(result["summary"])
print(result["review"])
```

### 总结

- **SequentialChain**：多个链按顺序串联
- **变量传递**：通过 input_key/output_key 共享变量
- **SimpleSequentialChain**：单输入单输出的简化版
- **与 LCEL 对比**：LCEL 用 `|` 更简洁
- **现代推荐**：用 LCEL 的 `|` 和 `RunnablePassthrough.assign`
- **适用场景**：多步骤任务，如生成→处理→评价

---

## 第19讲：Memory 概念与类型

### 概念

**Memory（记忆）** 是 LangChain 用于管理对话历史的机制。它让应用具备"上下文感知"能力——能记住之前说过什么，让对话连贯。没有记忆，每次对话都是独立的，LLM 无法理解上下文。

LangChain 提供多种 Memory 类型，适应不同场景：`ConversationBufferMemory`（完整缓冲）、`ConversationSummaryMemory`（摘要记忆）、`ConversationBufferWindowMemory`（窗口记忆）、`ConversationSummaryBufferMemory`（混合记忆）等。

### 原理

**记忆的核心问题原理**：LLM 本身是无状态的——每次调用独立，不记得之前对话。要实现多轮对话，需要把历史消息加入每次调用。但历史会越来越长，导致 token 消耗线性增长。Memory 的核心问题是"如何在保留上下文的同时控制 token"。

**Buffer Memory 原理**：最简单的记忆——把所有历史消息完整保存。每次调用把全部历史传给 LLM。优点是信息完整，缺点是 token 消耗随对话增长，长对话会超限。

**Summary Memory 原理**：定期把旧消息摘要成一条消息。比如对话超过 10 轮后，把前 5 轮摘要为"用户问了 X，AI 回答了 Y"。优点是 token 消耗稳定，缺点是丢失细节。

**Window Memory 原理**：只保留最近 N 轮对话。比如只保留最近 5 轮，更早的丢弃。优点是简单高效，缺点是丢失早期上下文。

**Summary Buffer Memory 原理**：混合策略——近期消息完整保留，早期消息摘要。兼顾上下文和 token 控制。

**与 LCEL 集成原理**：现代 LangChain 推荐用 LCEL + RunnableWithMessageHistory 管理记忆，而非传统 Memory 类。但传统 Memory 仍在大量使用，理解两者都重要。

### 例子

**示例1：ConversationBufferMemory（完整缓冲）**

```python
from langchain.memory import ConversationBufferMemory
from langchain_openai import ChatOpenAI

memory = ConversationBufferMemory()
llm = ChatOpenAI(model="gpt-4o-mini")

# 模拟多轮对话
def chat(user_input):
    # 加载历史
    history = memory.load_memory_variables({})["history"]
    
    # 构造消息
    messages = []
    if history:
        messages.extend(history)
    messages.append(HumanMessage(content=user_input))
    
    # 调用 LLM
    response = llm.invoke(messages)
    
    # 保存到 memory
    memory.save_context({"input": user_input}, {"output": response.content})
    
    return response.content

print(chat("你好"))
print(chat("我刚才说了什么？"))  # 能回忆起"你好"
```

**示例2：ConversationBufferWindowMemory（窗口）**

```python
from langchain.memory import ConversationBufferWindowMemory

# 只保留最近 2 轮
memory = ConversationBufferWindowMemory(k=2)

# 模拟对话
for i in range(5):
    memory.save_context(
        {"input": f"第{i+1}轮用户输入"},
        {"output": f"第{i+1}轮AI回复"}
    )

# 查看记忆（只有最近2轮）
print(memory.load_memory_variables({}))
# {'history': '第4轮... 第5轮...'}
```

**示例3：ConversationSummaryMemory（摘要）**

```python
from langchain.memory import ConversationSummaryMemory

# 用 LLM 做摘要
memory = ConversationSummaryMemory(llm=llm)

# 多轮对话
memory.save_context({"input": "我叫张三"}, {"output": "你好张三"})
memory.save_context({"input": "我喜欢 Python"}, {"output": "Python 是好语言"})

# 查看摘要
print(memory.load_memory_variables({}))
# {'history': '用户叫张三，喜欢 Python。AI 问候并肯定了 Python。'}
```

**示例4：在 LCEL 链中使用 Memory**

```python
from langchain_core.runnables.history import RunnableWithMessageHistory
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
from langchain_core.output_parsers import StrOutputParser

prompt = ChatPromptTemplate.from_messages([
    ("system", "你是友好助手"),
    MessagesPlaceholder(variable_name="history"),
    ("human", "{input}")
])

chain = prompt | llm | StrOutputParser()

# 用 RunnableWithMessageHistory 包装
from langchain_community.chat_message_histories import ChatMessageHistory

chain_with_history = RunnableWithMessageHistory(
    chain,
    lambda session_id: ChatMessageHistory(),
    input_messages_key="input",
    history_messages_key="history"
)

# 调用（带 session_id）
config = {"configurable": {"session_id": "user_1"}}

chain_with_history.invoke({"input": "你好"}, config=config)
response = chain_with_history.invoke({"input": "我刚才说了什么？"}, config=config)
print(response)  # 能回忆起"你好"
```

### 总结

- **Memory**：管理对话历史，让应用具备上下文感知
- **核心问题**：保留上下文 vs 控制 token
- **Buffer Memory**：完整保存，信息全但 token 增长
- **Summary Memory**：定期摘要，token 稳定但丢细节
- **Window Memory**：只保留最近 N 轮，简单高效
- **Summary Buffer**：混合策略，兼顾两者
- **现代方式**：RunnableWithMessageHistory + LCEL

---

## 第20讲：高级记忆策略

### 概念

本讲深入记忆的进阶用法，包括：长期记忆管理、跨会话记忆、记忆与检索结合、自定义记忆策略。这些策略让 Agent 具备更智能的记忆能力，适合生产级应用。

### 原理

**长期记忆管理原理**：生产级应用需要跨会话记忆——用户下次登录还能"记得"之前的信息。这需要把记忆持久化到数据库，而非内存。LangChain 提供 `RedisChatMessageHistory`、`PostgresChatMessageHistory` 等持久化方案。

**记忆与检索结合原理**：当历史很长时，不能全部传给 LLM。可以用检索——把历史存入向量库，每次调用时检索最相关的几条历史。这种"检索式记忆"适合长对话。

**记忆摘要时机原理**：何时触发摘要？常见策略：
1. **轮数触发**：每 N 轮摘要一次
2. **token 触发**：超过 token 阈值时摘要
3. **每轮触发**：每轮都更新摘要（成本高）

**自定义记忆原理**：继承 `BaseChatMessageHistory` 实现自定义存储，如存到 MongoDB、Elasticsearch 等。

### 例子

**示例1：持久化记忆（Redis）**

```python
from langchain_community.chat_message_histories import RedisChatMessageHistory
from langchain_core.runnables.history import RunnableWithMessageHistory

# 用 Redis 存储历史
def get_history(session_id: str):
    return RedisChatMessageHistory(
        session_id=session_id,
        url="redis://localhost:6379"
    )

chain_with_history = RunnableWithMessageHistory(
    chain,
    get_history,
    input_messages_key="input",
    history_messages_key="history"
)

# 即使进程重启，历史也保留
config = {"configurable": {"session_id": "user_123"}}
chain_with_history.invoke({"input": "我喜欢 Python"}, config=config)
# 下次登录
chain_with_history.invoke({"input": "我喜欢什么？"}, config=config)
# 能回忆起"Python"
```

**示例2：检索式记忆**

```python
from langchain_community.vectorstores import FAISS
from langchain_openai import OpenAIEmbeddings

# 把历史存入向量库
vectorstore = FAISS.from_texts([], OpenAIEmbeddings())

def save_to_memory(text, session_id):
    vectorstore.add_texts([text], metadatas=[{"session_id": session_id}])

def retrieve_memory(query, session_id, k=3):
    # 检索最相关的历史
    docs = vectorstore.similarity_search(
        query, k=k, filter={"session_id": session_id}
    )
    return [d.page_content for d in docs]

# 在对话中使用
def chat_with_retrieval(user_input, session_id):
    # 检索相关历史
    relevant_history = retrieve_memory(user_input, session_id)
    
    # 构造提示
    prompt = f"历史：{relevant_history}\n用户：{user_input}"
    response = llm.invoke(prompt)
    
    # 保存当前对话
    save_to_memory(f"用户：{user_input}\nAI：{response.content}", session_id)
    
    return response.content
```

**示例3：自动摘要记忆**

```python
from langchain.memory import ConversationSummaryBufferMemory

# 混合策略：近期完整，早期摘要
memory = ConversationSummaryBufferMemory(
    llm=llm,
    max_token_limit=200  # 超过200 token 触发摘要
)

# 模拟长对话
for i in range(20):
    memory.save_context(
        {"input": f"第{i+1}轮问题"},
        {"output": f"第{i+1}轮回答"}
    )

# 早期对话被摘要，近期完整保留
print(memory.load_memory_variables({}))
```

**示例4：自定义持久化记忆**

```python
from langchain_core.chat_history import BaseChatMessageHistory
from langchain_core.messages import BaseMessage
import json

class FileChatMessageHistory(BaseChatMessageHistory):
    """把历史存到文件"""
    
    def __init__(self, file_path: str):
        self.file_path = file_path
        self.messages = []
        self._load()
    
    def _load(self):
        try:
            with open(self.file_path, 'r') as f:
                data = json.load(f)
                self.messages = data.get("messages", [])
        except:
            self.messages = []
    
    def add_message(self, message: BaseMessage):
        self.messages.append(message.dict())
        self._save()
    
    def _save(self):
        with open(self.file_path, 'w') as f:
            json.dump({"messages": self.messages}, f)
    
    def get_messages(self):
        return self.messages
    
    def clear(self):
        self.messages = []
        self._save()

# 使用
chain_with_history = RunnableWithMessageHistory(
    chain,
    lambda session_id: FileChatMessageHistory(f"history_{session_id}.json"),
    input_messages_key="input",
    history_messages_key="history"
)
```

### 总结

- **长期记忆**：用 Redis/Postgres 持久化，跨会话保留
- **检索式记忆**：历史存向量库，检索最相关历史
- **自动摘要**：ConversationSummaryBufferMemory 混合策略
- **自定义记忆**：继承 BaseChatMessageHistory 实现特定存储
- **生产建议**：用持久化存储，配合检索和摘要控制 token
- **记忆策略选择**：根据场景选 Buffer/Summary/Window/混合

---

# 第6章 数据增强

数据增强（Retrieval）是 LangChain 最强大的能力之一——让 LLM 能基于外部知识库回答问题，即 RAG（检索增强生成）。本章深入数据增强的完整流程：文档加载、分割、嵌入、向量存储、检索。学完本章，你将能够构建完整的 RAG 应用。

---

## 第21讲：Document Loaders

### 概念

**Document Loader（文档加载器）** 是 LangChain 用于从各种数据源加载文档的组件。它把不同格式（PDF、Word、HTML、CSV、网页等）的数据统一转为 `Document` 对象——包含 `page_content`（文本内容）和 `metadata`（元数据）。

Document Loader 是 RAG 流程的起点——先把数据加载进来，才能分割、嵌入、检索。LangChain 提供上百种 Loader，覆盖几乎所有常见数据源。

### 原理

**Document 对象原理**：LangChain 用统一的 `Document` 类表示所有文档，无论来源。每个 Document 有：
- `page_content`：文本内容（字符串）
- `metadata`：元数据（字典），如来源、页码、作者等

这种统一表示让后续处理（分割、嵌入）不关心数据来源。

**Loader 的两步原理**：Loader 通常做两件事：
1. **加载**：从数据源读取原始数据
2. **转换**：把原始数据转为 Document 对象

比如 PDF Loader 读取 PDF 文件，把每页文本转为 Document，metadata 记录页码。

**Loader 分类原理**：LangChain 的 Loader 按数据源分类：
- **文件类**：PDF、Word、Excel、CSV、HTML、Markdown
- **网页类**：WebBaseLoader、SitemapLoader
- **数据库类**：SQL、MongoDB、Elasticsearch
- **云存储类**：S3、Google Drive、Notion
- **代码类**：GitHub、GitLab

**懒加载原理**：部分 Loader 支持 `lazy_load()`——不一次性加载所有文档，而是按需生成。适合大文档集，避免内存爆炸。

### 例子

**示例1：加载文本文件**

```python
from langchain_community.document_loaders import TextLoader

loader = TextLoader("example.txt")
documents = loader.load()

print(f"文档数：{len(documents)}")
print(f"内容：{documents[0].page_content[:100]}")
print(f"元数据：{documents[0].metadata}")
# {'source': 'example.txt'}
```

**示例2：加载 PDF**

```python
from langchain_community.document_loaders import PyPDFLoader

loader = PyPDFLoader("document.pdf")
pages = loader.load()

print(f"页数：{len(pages)}")
for i, page in enumerate(pages):
    print(f"第{i+1}页：{page.page_content[:50]}")
    print(f"元数据：{page.metadata}")  # {'source': 'document.pdf', 'page': i}
```

**示例3：加载网页**

```python
from langchain_community.document_loaders import WebBaseLoader

loader = WebBaseLoader("https://python.langchain.com/docs/introduction")
docs = loader.load()

print(f"内容长度：{len(docs[0].page_content)}")
print(docs[0].page_content[:200])
```

**示例4：加载 CSV**

```python
from langchain_community.document_loaders import CSVLoader

loader = CSVLoader("data.csv")
docs = loader.load()

# 每行一个 Document
for doc in docs[:3]:
    print(f"内容：{doc.page_content}")
    print(f"元数据：{doc.metadata}")  # {'row': 0, 'source': 'data.csv'}
```

**示例5：批量加载目录**

```python
from langchain_community.document_loaders import DirectoryLoader

# 加载目录下所有 .txt 文件
loader = DirectoryLoader(
    "./documents",
    glob="**/*.txt",
    loader_cls=TextLoader,
    show_progress=True
)
docs = loader.load()
print(f"共加载 {len(docs)} 个文档")
```

**示例6：加载 Markdown**

```python
from langchain_community.document_loaders import UnstructuredMarkdownLoader

loader = UnstructuredMarkdownLoader("README.md")
docs = loader.load()
```

### 总结

- **Document Loader**：从各种数据源加载文档，统一转为 Document 对象
- **Document 对象**：page_content + metadata
- **Loader 分类**：文件、网页、数据库、云存储、代码
- **懒加载**：lazy_load 适合大文档集
- **批量加载**：DirectoryLoader 加载整个目录
- **RAG 起点**：Loader 是 RAG 流程的第一步

---

## 第22讲：Text Splitters

### 概念

**Text Splitter（文本分割器）** 是 LangChain 用于把长文档分割为小块的组件。LLM 的上下文长度有限（如 GPT-4 是 128K token），长文档无法一次性处理。分割器把文档切为合适大小的块，每块单独嵌入和检索。

分割的质量直接影响 RAG 效果——好的分割保持语义完整，坏的分割切断句子，导致检索到无意义片段。

### 原理

**为什么需要分割原理**：
1. **上下文限制**：LLM 上下文有限，长文档无法一次处理
2. **检索精度**：小块检索更精准，大块包含太多无关信息
3. **嵌入质量**：嵌入模型对小段文本效果更好
4. **成本控制**：只检索相关块，而非整篇文档

**分割策略原理**：好的分割应：
1. **保持语义完整**：不在句子中间切断
2. **块大小适中**：太大检索不精准，太小丢失上下文
3. **有重叠**：相邻块有重叠，避免边界信息丢失
4. **尊重结构**：按段落、章节等自然结构分割

**RecursiveCharacterTextSplitter 原理**：LangChain 推荐的分割器。它递归尝试不同分隔符——先用段落分隔，段落太大再用句子，句子太大再用字符。这种"递归"策略保持语义完整。

**块大小与重叠原理**：
- **chunk_size**：每块最大字符数，常用 500-1000
- **chunk_overlap**：相邻块重叠字符数，常用 50-200

重叠避免边界信息丢失——如果关键信息在块边界，重叠确保它出现在两个块中。

**Markdown/Header 分割原理**：对于结构化文档（Markdown、代码），按标题或函数分割比按字符更合理。LangChain 提供 `MarkdownHeaderTextSplitter`、`PythonCodeTextSplitter` 等。

### 例子

**示例1：RecursiveCharacterTextSplitter（推荐）**

```python
from langchain_text_splitters import RecursiveCharacterTextSplitter

text = """LangChain 是一个 LLM 应用框架。
它提供组件化开发能力。
包括模型、提示、链、记忆、检索等。
LangGraph 是其扩展，用于构建 Agent。
LangSmith 是观测平台。"""

splitter = RecursiveCharacterTextSplitter(
    chunk_size=50,        # 每块最大50字符
    chunk_overlap=10,     # 相邻块重叠10字符
    separators=["\n\n", "\n", "。", "，", " "]  # 分隔符优先级
)

chunks = splitter.split_text(text)
for i, chunk in enumerate(chunks):
    print(f"块{i+1}：{chunk}")
```

**示例2：分割 Document**

```python
from langchain_community.document_loaders import TextLoader

# 先加载文档
loader = TextLoader("long_document.txt")
docs = loader.load()

# 分割
splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,
    chunk_overlap=50
)

splits = splitter.split_documents(docs)
print(f"原始文档数：{len(docs)}")
print(f"分割后块数：{len(splits)}")
print(f"第一块元数据：{splits[0].metadata}")  # 保留原始元数据
```

**示例3：Markdown 分割**

```python
from langchain_text_splitters import MarkdownHeaderTextSplitter

md_text = """
# 第一章
## 1.1 简介
LangChain 是 LLM 框架。
## 1.2 特性
支持组件化开发。
# 第二章
## 2.1 安装
pip install langchain
"""

splitter = MarkdownHeaderTextSplitter(
    headers_to_split_on=[
        ("#", "Header 1"),
        ("##", "Header 2"),
    ]
)

md_splits = splitter.split_text(md_text)
for split in md_splits:
    print(f"内容：{split.page_content}")
    print(f"标题：{split.metadata}\n")
```

**示例4：代码分割**

```python
from langchain_text_splitters import RecursiveCharacterTextSplitter, Language

# Python 代码分割
python_splitter = RecursiveCharacterTextSplitter.from_language(
    language=Language.PYTHON,
    chunk_size=500,
    chunk_overlap=50
)

code = """
def hello():
    print("Hello")

def world():
    print("World")
"""

chunks = python_splitter.split_text(code)
# 按函数分割，保持代码完整
```

**示例5：字符分割（简单）**

```python
from langchain_text_splitters import CharacterTextSplitter

# 简单字符分割（不推荐，可能切断句子）
splitter = CharacterTextSplitter(
    separator="\n\n",    # 按双换行分割
    chunk_size=500,
    chunk_overlap=50
)
```

**示例6：Token 分割**

```python
from langchain_text_splitters import TokenTextSplitter

# 按 token 数分割（适合精确控制 token）
splitter = TokenTextSplitter(
    chunk_size=500,    # 每块500 token
    chunk_overlap=50
)
```

### 总结

- **Text Splitter**：把长文档分割为小块
- **分割原因**：上下文限制、检索精度、嵌入质量、成本控制
- **RecursiveCharacterTextSplitter**：推荐，递归尝试分隔符
- **chunk_size**：块大小，常用 500-1000
- **chunk_overlap**：重叠，常用 50-200，避免边界丢失
- **结构化分割**：Markdown、代码按自然结构分割
- **分割质量**：直接影响 RAG 效果，需调优

---

## 第23讲：Embeddings

### 概念

**Embeddings（嵌入）** 是把文本转为向量的技术。向量是数字数组（如 [0.1, 0.3, -0.2, ...]），捕捉了文本的语义信息。语义相近的文本，向量也相近。嵌入是向量检索的基础——把文档和查询都转为向量，计算相似度找到最相关的文档。

LangChain 提供统一的 Embeddings 接口，支持多种嵌入模型：OpenAI、Anthropic、开源模型（如 BGE、E5）等。

### 原理

**嵌入原理**：嵌入模型把文本映射到高维向量空间（如 1536 维）。这个映射是通过深度学习训练的——模型学会了"语义相近的文本，向量也相近"。比如"猫"和"小猫"的向量很近，"猫"和"汽车"的向量很远。

**为什么需要嵌入原理**：计算机无法直接理解文本语义，但能计算向量距离。嵌入把"语义相似度"问题转化为"向量距离"问题，让计算机能做语义检索。这是 RAG 的核心——用向量相似度找到最相关的文档。

**相似度计算原理**：常用余弦相似度——计算两个向量夹角的余弦值，范围 -1 到 1，越接近 1 越相似。也用欧氏距离、点积等。余弦相似度最常用，因为它对向量长度不敏感。

**嵌入模型选型原理**：
- **OpenAI text-embedding-3-small**：性价比高，1536 维
- **OpenAI text-embedding-3-large**：质量高，3072 维
- **BGE（开源）**：中文效果好，可本地部署
- **Cohere**：多语言支持好

**文档 vs 查询嵌入原理**：在 RAG 中，文档预先嵌入存入向量库，查询实时嵌入与文档向量比较。这种"离线嵌入文档、在线嵌入查询"的模式让检索高效。

### 例子

**示例1：OpenAI Embeddings**

```python
from langchain_openai import OpenAIEmbeddings

embeddings = OpenAIEmbeddings(model="text-embedding-3-small")

# 嵌入单个文本
vector = embeddings.embed_query("LangChain 是什么？")
print(f"向量维度：{len(vector)}")
print(f"前5维：{vector[:5]}")

# 嵌入多个文档
texts = ["LangChain 是 LLM 框架", "Python 是编程语言"]
vectors = embeddings.embed_documents(texts)
print(f"文档数：{len(vectors)}")
```

**示例2：计算相似度**

```python
import numpy as np

def cosine_similarity(v1, v2):
    return np.dot(v1, v2) / (np.linalg.norm(v1) * np.linalg.norm(v2))

# 嵌入几个文本
texts = ["猫是宠物", "小猫很可爱", "汽车是交通工具"]
vectors = embeddings.embed_documents(texts)

# 计算相似度
sim_01 = cosine_similarity(vectors[0], vectors[1])
sim_02 = cosine_similarity(vectors[0], vectors[2])

print(f"'猫是宠物' vs '小猫很可爱' 相似度：{sim_01:.3f}")  # 高
print(f"'猫是宠物' vs '汽车是交通工具' 相似度：{sim_02:.3f}")  # 低
```

**示例3：开源嵌入模型（Ollama）**

```python
from langchain_ollama import OllamaEmbeddings

# 本地嵌入模型
embeddings = OllamaEmbeddings(model="nomic-embed-text")
vector = embeddings.embed_query("你好")
```

**示例4：用 HuggingFace 嵌入**

```python
from langchain_huggingface import HuggingFaceEmbeddings

# 用开源模型（如 BGE，中文效果好）
embeddings = HuggingFaceEmbeddings(
    model_name="BAAI/bge-small-zh-v1.5"
)
vector = embeddings.embed_query("LangChain 是什么")
```

**示例5：批量嵌入**

```python
# 批量嵌入（比循环快）
texts = [f"文档{i}" for i in range(100)]
vectors = embeddings.embed_documents(texts)  # 一次性嵌入
print(f"嵌入 {len(vectors)} 个文档")
```

**示例6：异步嵌入**

```python
import asyncio

async def async_embed():
    texts = ["文档1", "文档2", "文档3"]
    vectors = await embeddings.aembed_documents(texts)
    return vectors

vectors = asyncio.run(async_embed())
```

### 总结

- **Embeddings**：把文本转为向量，捕捉语义信息
- **余弦相似度**：计算向量相似度，最常用
- **OpenAI**：text-embedding-3-small（性价比）、3-large（质量）
- **开源模型**：BGE（中文）、可本地部署
- **批量嵌入**：比循环快，用 embed_documents
- **RAG 核心**：文档离线嵌入，查询在线嵌入，向量检索

---

## 第24讲：Vector Stores

### 概念

**Vector Store（向量存储）** 是专门存储和检索向量的数据库。它把文档的嵌入向量与文本一起存储，支持基于向量相似度的快速检索。向量存储是 RAG 的核心组件——文档预先嵌入存入，查询时用向量相似度找到最相关的文档。

LangChain 支持多种向量存储：FAISS（本地）、Chroma（轻量）、Pinecone（云服务）、Weaviate、Milvus、pgvector 等。

### 原理

**向量存储原理**：向量存储同时保存文档文本和嵌入向量。检索时，把查询转为向量，与存储的向量比较相似度，返回最相似的文档。这种"向量相似度检索"是语义检索的基础。

**近似最近邻（ANN）原理**：精确比较所有向量太慢（O(n)）。向量存储用 ANN 算法（如 HNSW、IVF）近似检索，速度大幅提升（O(log n)），精度略有损失但可接受。

**向量存储选型原理**：
- **FAISS**：Facebook 开源，本地使用，速度快，适合原型
- **Chroma**：轻量级，开发友好，适合中小规模
- **Pinecone**：云服务，免运维，适合生产
- **Milvus**：开源，支持大规模，适合企业
- **pgvector**：PostgreSQL 扩展，适合已有 PG 环境

**持久化原理**：向量存储通常支持持久化——把向量存到磁盘或数据库，进程重启不丢失。FAISS 用 `save_local`/`load_local`，Chroma 用持久化目录。

**与 LangChain 集成原理**：所有向量存储实现统一接口——`add_documents`、`similarity_search`、`as_retriever` 等。这让切换向量存储只需改一行代码。

### 例子

**示例1：FAISS（本地）**

```python
from langchain_community.vectorstores import FAISS
from langchain_openai import OpenAIEmbeddings

embeddings = OpenAIEmbeddings()

# 创建向量存储
texts = ["LangChain 是 LLM 框架", "Python 是编程语言", "LangGraph 是 Agent 框架"]
vectorstore = FAISS.from_texts(texts, embeddings)

# 检索
results = vectorstore.similarity_search("什么是 LangChain？", k=2)
for doc in results:
    print(doc.page_content)
# 返回最相关的2个文档
```

**示例2：从 Document 创建**

```python
from langchain_core.documents import Document

docs = [
    Document(page_content="LangChain 提供组件化开发", metadata={"source": "doc1"}),
    Document(page_content="LangGraph 用于构建 Agent", metadata={"source": "doc2"})
]

vectorstore = FAISS.from_documents(docs, embeddings)

# 按 metadata 过滤检索
results = vectorstore.similarity_search(
    "LangChain",
    k=1,
    filter={"source": "doc1"}  # 只在 source=doc1 中检索
)
```

**示例3：持久化**

```python
# 保存到磁盘
vectorstore.save_local("faiss_index")

# 从磁盘加载
loaded_vectorstore = FAISS.load_local("faiss_index", embeddings)
```

**示例4：Chroma（持久化）**

```python
from langchain_chroma import Chroma

# 创建并持久化到目录
vectorstore = Chroma.from_texts(
    texts=texts,
    embedding=embeddings,
    persist_directory="./chroma_db"  # 持久化目录
)

# 从目录加载
vectorstore = Chroma(
    persist_directory="./chroma_db",
    embedding_function=embeddings
)

# 检索
results = vectorstore.similarity_search("LangChain", k=2)
```

**示例5：带分数的检索**

```python
# 返回相似度分数
results = vectorstore.similarity_search_with_score("LangChain 是什么", k=2)
for doc, score in results:
    print(f"分数：{score:.3f}，内容：{doc.page_content}")
# 分数越低越相似（FAISS 用 L2 距离）
```

**示例6：用作 Retriever**

```python
# 转为 Retriever（用于 LCEL 链）
retriever = vectorstore.as_retriever(
    search_type="similarity",  # 相似度检索
    search_kwargs={"k": 3}     # 返回3个
)

# 在链中使用
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough

llm = ChatOpenAI(model="gpt-4o-mini")

prompt = ChatPromptTemplate.from_template("基于以下信息回答：{context}\n问题：{question}")

rag_chain = (
    {
        "context": retriever | (lambda docs: "\n".join([d.page_content for d in docs])),
        "question": RunnablePassthrough()
    }
    | prompt
    | llm
    | StrOutputParser()
)

answer = rag_chain.invoke("什么是 LangChain？")
print(answer)
```

### 总结

- **Vector Store**：存储和检索向量的数据库
- **ANN 算法**：近似最近邻，快速检索
- **选型**：FAISS（本地）、Chroma（轻量）、Pinecone（云）、Milvus（企业）
- **持久化**：save_local/load_local 或 persist_directory
- **统一接口**：add_documents、similarity_search、as_retriever
- **RAG 核心**：文档存入向量库，查询时向量检索

---

## 第25讲：Retrievers

### 概念

**Retriever（检索器）** 是 LangChain 的检索抽象——接收查询，返回相关文档。它是 RAG 流程中连接向量存储和 LLM 的桥梁。Retriever 把向量存储的检索能力封装为 Runnable 接口，可以直接用在 LCEL 链中。

LangChain 提供多种 Retriever：VectorStoreRetriever（向量检索）、MultiQueryRetriever（多查询）、ContextualCompressionRetriever（压缩）、EnsembleRetriever（混合检索）等。

### 原理

**Retriever 抽象原理**：Retriever 是一个 Runnable，输入是查询字符串，输出是 Document 列表。这种抽象让检索可以像其他组件一样用 `|` 串联，融入 LCEL 链。

**检索类型原理**：
- **similarity**：纯相似度检索，最常用
- **mmr**（Max Marginal Relevance）：兼顾相似度和多样性，避免结果太相似
- **similarity_score_threshold**：带分数阈值，只返回足够相关的

**MultiQuery 原理**：用户查询可能不够好（太短、太口语）。MultiQueryRetriever 用 LLM 生成多个改写查询，分别检索，合并去重。这提升了召回率。

**ContextualCompression 原理**：检索到的文档可能很长，包含无关信息。压缩检索器用 LLM/模型提取与查询相关的部分，减少 token 消耗。

**Ensemble 原理**：混合多个检索器（如向量 + 关键词），取长补短。向量检索擅长语义，关键词检索擅长精确匹配，混合效果更好。

### 例子

**示例1：基础 Retriever**

```python
# 从向量存储创建
retriever = vectorstore.as_retriever(
    search_type="similarity",
    search_kwargs={"k": 3}
)

# 检索
docs = retriever.invoke("什么是 LangChain？")
for doc in docs:
    print(doc.page_content)
```

**示例2：MMR 检索（多样性）**

```python
retriever = vectorstore.as_retriever(
    search_type="mmr",  # 最大边际相关性
    search_kwargs={
        "k": 3,
        "fetch_k": 10,    # 先取10个
        "lambda_mult": 0.5  # 多样性权重
    }
)

# 结果更多样，避免太相似
docs = retriever.invoke("LangChain")
```

**示例3：MultiQueryRetriever**

```python
from langchain.retrievers.multi_query import MultiQueryRetriever
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4o-mini")

# 用 LLM 生成多个查询
retriever = MultiQueryRetriever.from_llm(
    retriever=vectorstore.as_retriever(),
    llm=llm
)

# 自动生成多个改写查询，分别检索，合并去重
docs = retriever.invoke("LangChain 怎么用？")
```

**示例4：ContextualCompressionRetriever**

```python
from langchain.retrievers import ContextualCompressionRetriever
from langchain.retrievers.document_compressors import LLMChainExtractor

# 用 LLM 压缩文档，只保留相关部分
compressor = LLMChainExtractor.from_llm(llm)

compression_retriever = ContextualCompressionRetriever(
    base_compressor=compressor,
    base_retriever=vectorstore.as_retriever()
)

# 检索并压缩
docs = compression_retriever.invoke("LangChain 的核心组件")
# 返回的文档只包含与查询相关的部分
```

**示例5：EnsembleRetriever（混合检索）**

```python
from langchain.retrievers import EnsembleRetriever
from langchain_community.retrievers import BM25Retriever

# BM25 关键词检索
bm25_retriever = BM25Retriever.from_texts(texts)
bm25_retriever.k = 3

# 向量检索
vector_retriever = vectorstore.as_retriever(k=3)

# 混合
ensemble_retriever = EnsembleRetriever(
    retrievers=[bm25_retriever, vector_retriever],
    weights=[0.5, 0.5]  # 各占一半权重
)

# 混合检索结果
docs = ensemble_retriever.invoke("LangChain")
```

**示例6：完整 RAG 链**

```python
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough

# 提示模板
prompt = ChatPromptTemplate.from_template("""
基于以下信息回答问题。如果信息不足，说"我不知道"。

信息：{context}

问题：{question}

回答：""")

# 格式化文档
def format_docs(docs):
    return "\n\n".join([d.page_content for d in docs])

# RAG 链
rag_chain = (
    {
        "context": retriever | format_docs,
        "question": RunnablePassthrough()
    }
    | prompt
    | llm
    | StrOutputParser()
)

# 使用
answer = rag_chain.invoke("LangChain 是什么？")
print(answer)
```

### 总结

- **Retriever**：检索抽象，输入查询、输出文档列表
- **检索类型**：similarity（相似度）、mmr（多样性）、score_threshold（阈值）
- **MultiQuery**：LLM 生成多个查询，提升召回率
- **ContextualCompression**：LLM 压缩文档，只留相关部分
- **Ensemble**：混合多个检索器，取长补短
- **RAG 链**：retriever | format_docs → prompt → llm → parser

---

# 第7章 Agent 与工具

Agent 是 LangChain 最激动人心的能力——让 LLM 自主决策、调用工具、完成复杂任务。本章深入 Agent 的架构与实现，包括 Agent 类型、工具定义、ReAct 模式、Agent Executor。学完本章，你将能够构建具备工具调用能力的智能 Agent。

---

## 第26讲：Agent 概念与类型

### 概念

**Agent（代理）** 是 LangChain 中让 LLM 自主决策的机制。与普通链（固定流程）不同，Agent 让 LLM 根据用户输入**动态决定**下一步做什么——调用哪个工具、何时调用、如何组合。Agent 是"LLM + 工具 + 决策循环"的组合。

LangChain 提供多种 Agent 类型，适应不同场景：ReAct（推理+行动）、OpenAI Tools（原生工具调用）、Structured Chat（结构化输入）、Self-Ask（自问自答）等。

### 原理

**Agent vs Chain 原理**：
- **Chain**：流程固定，如 `prompt → llm → parser`，每次执行路径相同
- **Agent**：流程动态，LLM 根据输入决定调用哪些工具、何时结束

Agent 的核心是"决策循环"——LLM 思考下一步、执行行动、观察结果、再思考，直到完成任务。

**Agent 的核心组件原理**：
1. **LLM**：决策大脑，决定调用什么工具
2. **Tools**：可调用的外部能力（搜索、计算、API）
3. **Agent Executor**：执行循环，协调 LLM 和工具

**Agent 类型原理**：
- **ReAct**：经典模式，思考-行动-观察循环
- **OpenAI Tools**：用 OpenAI 原生 function calling，最可靠
- **Structured Chat**：支持多参数工具
- **Self-Ask**：把复杂问题拆为子问题

**现代趋势原理**：LangChain 现代版本推荐用 `create_tool_calling_agent`（基于原生工具调用）或 LangGraph（更灵活的 Agent 框架）。传统 Agent 类型仍可用，但新项目建议用现代方式。

### 例子

**示例1：Agent 与 Chain 的对比**

```python
# Chain：固定流程
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

chain = ChatPromptTemplate.from_template("回答：{input}") | llm | StrOutputParser()
# 每次都走相同路径

# Agent：动态决策
from langchain.agents import create_tool_calling_agent, AgentExecutor

tools = [...]  # 工具列表
agent = create_tool_calling_agent(llm, tools, prompt)
agent_executor = AgentExecutor(agent=agent, tools=tools)
# LLM 决定调用哪些工具、何时结束
```

**示例2：Agent 的决策过程**

```python
# 用户问："北京天气怎么样？"
# Agent 决策过程：
# 1. [Thought] 需要查询天气
# 2. [Action] 调用 weather_tool(city="北京")
# 3. [Observation] 晴 25°C
# 4. [Thought] 获得信息，可以回答
# 5. [Final Answer] 北京今天晴，25度
```

**示例3：不同 Agent 类型**

```python
from langchain.agents import (
    create_openai_tools_agent,
    create_tool_calling_agent,
    create_structured_chat_agent,
    create_react_agent,
)

# OpenAI Tools Agent（推荐，用原生 function calling）
openai_agent = create_openai_tools_agent(llm, tools, prompt)

# 通用工具调用 Agent（支持更多模型）
tool_calling_agent = create_tool_calling_agent(llm, tools, prompt)

# ReAct Agent（经典模式）
react_agent = create_react_agent(llm, tools, prompt)
```

**示例4：Agent 的能力边界**

```python
# Agent 能做的：
# - 根据输入决定调用哪个工具
# - 多次调用工具
# - 组合多个工具的结果
# - 判断何时任务完成

# Agent 不能做的：
# - 执行未定义的工具
# - 超出工具能力的任务
# - 无限循环（需要 max_iterations 限制）
```

### 总结

- **Agent**：LLM 自主决策，动态调用工具
- **vs Chain**：Chain 流程固定，Agent 流程动态
- **核心组件**：LLM + Tools + AgentExecutor
- **Agent 类型**：ReAct、OpenAI Tools、Structured Chat
- **现代推荐**：create_tool_calling_agent 或 LangGraph
- **能力边界**：只能用定义的工具，需限制迭代次数

---

## 第27讲：Tools 工具定义

### 概念

**Tool（工具）** 是 Agent 调用外部能力的接口。一个工具本质上是一个有清晰签名（名称、描述、参数 schema）的可调用函数。LLM 通过阅读工具描述，决定何时调用、如何调用。

LangChain 提供多种工具定义方式：`@tool` 装饰器（最便捷）、`StructuredTool` 类（更灵活）、`BaseTool` 继承（最定制化）。

### 原理

**工具三要素原理**：一个工具由三部分组成：
1. **函数本身**：实际执行的 Python 函数
2. **名称 + 描述**：告诉 LLM 这个工具做什么、何时用
3. **参数 schema**：JSON Schema 描述参数类型和结构

LLM 在对话时看到所有工具的描述和 schema，决定是否调用、调用哪个、传什么参数。

**@tool 装饰器原理**：LangChain 提供 `@tool` 装饰器，自动从函数的 docstring 和类型注解生成工具的描述和参数 schema。这是最便捷的方式。装饰器读取：
- 函数名 → 工具名
- docstring → 工具描述
- 参数类型注解 → 参数 schema

**工具返回值原理**：工具可以返回字符串（最简单）、字典、结构化对象。返回值会被转为 ToolMessage 传给 LLM，LLM 基于结果继续决策。

**工具与 LLM 绑定原理**：通过 `llm.bind_tools(tools)` 把工具信息传给 LLM。LLM 在响应中可能包含 `tool_calls`，指明要调用的工具和参数。Agent Executor 负责执行这些工具调用。

### 例子

**示例1：用 @tool 定义工具**

```python
from langchain_core.tools import tool

@tool
def search_weather(city: str) -> str:
    """查询指定城市的天气。
    
    Args:
        city: 城市名称，如"北京"、"上海"
    
    Returns:
        天气信息字符串
    """
    weather_data = {"北京": "晴 25°C", "上海": "多云 28°C"}
    return weather_data.get(city, f"未找到 {city} 的天气")

@tool
def calculate(expression: str) -> str:
    """计算数学表达式。
    
    Args:
        expression: 数学表达式，如 "1+2*3"
    """
    try:
        return str(eval(expression))
    except Exception as e:
        return f"计算错误：{e}"

# 查看工具信息
print(search_weather.name)        # search_weather
print(search_weather.description)  # 查询指定城市的天气...
print(search_weather.args)        # {'city': {'type': 'string', ...}}
```

**示例2：用 Pydantic 定义复杂参数**

```python
from langchain_core.tools import tool
from pydantic import BaseModel, Field

class SearchInput(BaseModel):
    query: str = Field(description="搜索关键词")
    max_results: int = Field(default=5, description="最大结果数")

@tool(args_schema=SearchInput)
def web_search(query: str, max_results: int = 5) -> str:
    """搜索网络获取信息"""
    return f"搜索 {query}，返回 {max_results} 条结果"
```

**示例3：StructuredTool 方式**

```python
from langchain_core.tools import StructuredTool

def search_func(query: str, max_results: int = 5) -> str:
    """执行搜索"""
    return f"结果：{query}"

# 用 StructuredTool 创建
search_tool = StructuredTool.from_function(
    func=search_func,
    name="web_search",
    description="搜索网络",
    args_schema=SearchInput
)
```

**示例4：异步工具**

```python
@tool
async def async_search(query: str) -> str:
    """异步搜索工具"""
    import asyncio
    await asyncio.sleep(1)  # 模拟异步操作
    return f"异步结果：{query}"
```

**示例5：内置工具**

```python
from langchain_community.tools import (
    DuckDuckGoSearchRun,  # 搜索
    PythonREPLTool,        # Python 执行
    ShellTool,             # Shell 命令
)

# 搜索工具
search = DuckDuckGoSearchRun()
result = search.invoke("LangChain")

# Python 执行工具
python_tool = PythonREPLTool()
result = python_tool.invoke("print(1+1)")
```

**示例6：工具集合**

```python
# 定义一组工具
tools = [
    search_weather,
    calculate,
    web_search,
]

# 给 LLM 绑定工具
llm_with_tools = llm.bind_tools(tools)

# 测试 LLM 是否决定调用工具
response = llm_with_tools.invoke("北京天气怎么样？")
print(response.tool_calls)  # [{'name': 'search_weather', 'args': {'city': '北京'}, ...}]
```

### 总结

- **工具三要素**：函数、名称+描述、参数 schema
- **@tool 装饰器**：自动从 docstring 和类型注解生成 schema
- **Pydantic 参数**：用 BaseModel 定义复杂参数
- **StructuredTool**：更灵活的创建方式
- **异步工具**：支持 async def
- **内置工具**：DuckDuckGo 搜索、Python REPL 等

---

## 第28讲：ReAct Agent

### 概念

**ReAct（Reasoning + Acting）** 是经典 Agent 架构，通过"思考-行动-观察"循环实现智能决策。LLM 先思考该做什么（Thought），然后执行行动（Action/工具调用），观察结果（Observation），再思考下一步，直到完成任务。

ReAct 是现代 Agent 的基础范式，LangChain 的工具调用 Agent 本质上就是 ReAct 实现。

### 原理

**ReAct 循环原理**：
```
Thought（思考）：分析当前情况，决定下一步
Action（行动）：调用工具
Observation（观察）：获取工具结果
↓
Thought（再思考）：基于观察，决定下一步
...
Final Answer：任务完成，输出最终答案
```

**为什么 ReAct 有效原理**：
1. **推理指导行动**：每步行动前先思考，避免盲目调用
2. **行动反馈推理**：工具结果作为新信息，修正后续推理
3. **可解释性**：思考过程可见，便于调试
4. **容错性**：工具失败时，推理能调整策略

**ReAct Prompt 原理**：经典 ReAct 用特定 Prompt 格式引导 LLM 输出 Thought/Action/Observation。现代 LLM 通过原生 tool_calls 支持，不再需要特殊格式，但思想一致。

**与 LangGraph 关系原理**：LangGraph 是构建 ReAct Agent 的现代方式——用图结构表达思考-行动-观察循环，更灵活。LangChain 的 Agent Executor 是简化版。

### 例子

**示例1：用 create_react_agent（LangChain 现代方式）**

```python
from langchain_openai import ChatOpenAI
from langchain_core.tools import tool
from langgraph.prebuilt import create_react_agent

@tool
def search(query: str) -> str:
    """搜索网络信息"""
    return f"搜索结果：{query}"

@tool
def calculate(expression: str) -> str:
    """数学计算"""
    return str(eval(expression))

llm = ChatOpenAI(model="gpt-4o-mini")

# 一行创建 ReAct Agent
agent = create_react_agent(
    model=llm,
    tools=[search, calculate],
    prompt="你是助手，可以搜索和计算"
)

# 使用
result = agent.invoke({"messages": [("user", "搜索 LangChain 并计算 1+2")]})
print(result["messages"][-1].content)
```

**示例2：用 LangChain Agent Executor**

```python
from langchain.agents import create_tool_calling_agent, AgentExecutor
from langchain_core.prompts import ChatPromptTemplate

prompt = ChatPromptTemplate.from_messages([
    ("system", "你是助手，可以使用工具"),
    ("human", "{input}"),
    ("placeholder", "{agent_scratchpad}"),  # Agent 思考过程
])

# 创建 Agent
agent = create_tool_calling_agent(llm, [search, calculate], prompt)
agent_executor = AgentExecutor(
    agent=agent,
    tools=[search, calculate],
    verbose=True  # 打印思考过程
)

# 执行
result = agent_executor.invoke({"input": "搜索 LangChain"})
print(result["output"])
```

**示例3：观察 ReAct 思考过程**

```python
# verbose=True 会打印思考过程
result = agent_executor.invoke({"input": "计算 25 * 4 + 100"})

# 输出示例：
# > Entering new AgentExecutor chain...
# Thought: 需要计算 25*4+100
# Action: calculate
# Action Input: 25*4+100
# Observation: 200
# Thought: 计算完成
# Final Answer: 25*4+100 = 200
# > Finished chain.
```

**示例4：多工具协作**

```python
@tool
def search(query: str) -> str:
    """搜索网络"""
    return "LangChain 是 LLM 框架"

@tool
def translate(text: str, target_lang: str) -> str:
    """翻译文本"""
    return f"翻译为{target_lang}：{text}"

@tool
def summarize(text: str) -> str:
    """总结文本"""
    return f"摘要：{text[:50]}"

agent = create_react_agent(
    llm,
    tools=[search, translate, summarize],
    prompt="你是助手，可以搜索、翻译、总结"
)

# Agent 会自动决定调用顺序
result = agent.invoke({
    "messages": [("user", "搜索 LangChain，翻译为英文，再总结")]
})
```

### 总结

- **ReAct = Reasoning + Acting**：思考-行动-观察循环
- **核心循环**：Thought → Action → Observation → Thought
- **优势**：推理指导行动、行动反馈推理、可解释、容错
- **现代实现**：create_react_agent（LangGraph）
- **LangChain 方式**：create_tool_calling_agent + AgentExecutor
- **调试**：verbose=True 打印思考过程

---

## 第29讲：Agent Executor

### 概念

**AgentExecutor** 是 LangChain 的 Agent 执行器——协调 LLM 和工具，执行 Agent 决策循环。它接收用户输入，调用 Agent（LLM）决策，执行工具调用，把结果返回给 LLM，循环直到完成或达到迭代上限。

AgentExecutor 处理了 Agent 执行的复杂细节——迭代控制、错误处理、中间步骤记录、最大迭代限制等。

### 原理

**AgentExecutor 工作原理**：
1. 接收用户输入
2. 调用 Agent（LLM + 工具）决策
3. 如果 LLM 决定调用工具，执行工具
4. 把工具结果返回给 LLM
5. 重复 2-4，直到 LLM 输出最终答案或达到迭代上限

**迭代控制原理**：Agent 可能陷入无限循环（反复调用工具不结束）。AgentExecutor 用 `max_iterations` 限制最大迭代次数，默认通常 15-25。超过限制会强制停止，返回当前结果。

**错误处理原理**：工具执行可能失败（API 超时、参数错误等）。AgentExecutor 可以配置错误处理策略——把错误信息返回给 LLM，让它调整策略，而非直接崩溃。

**中间步骤记录原理**：AgentExecutor 记录每步的中间结果（intermediate_steps），包括调用的工具、参数、结果。这些记录可用于调试、追踪、回放。

**返回值控制原理**：AgentExecutor 可以配置 `return_intermediate_steps`（返回中间步骤）、`early_stopping_method`（达到迭代上限时如何处理）等。

### 例子

**示例1：基础 AgentExecutor**

```python
from langchain.agents import create_tool_calling_agent, AgentExecutor
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.tools import tool

@tool
def search(query: str) -> str:
    """搜索网络"""
    return f"搜索结果：{query}"

@tool
def calculate(expression: str) -> str:
    """数学计算"""
    return str(eval(expression))

llm = ChatOpenAI(model="gpt-4o-mini")
tools = [search, calculate]

prompt = ChatPromptTemplate.from_messages([
    ("system", "你是助手"),
    ("human", "{input}"),
    ("placeholder", "{agent_scratchpad}"),
])

agent = create_tool_calling_agent(llm, tools, prompt)

# 创建 AgentExecutor
agent_executor = AgentExecutor(
    agent=agent,
    tools=tools,
    verbose=True,
    max_iterations=10,  # 最多10次迭代
    handle_parsing_errors=True  # 处理解析错误
)

# 执行
result = agent_executor.invoke({"input": "搜索 LangChain"})
print(result["output"])
```

**示例2：配置迭代限制**

```python
# 防止无限循环
agent_executor = AgentExecutor(
    agent=agent,
    tools=tools,
    max_iterations=15,          # 最多15次
    max_execution_time=60,     # 最多60秒
    early_stopping_method="generate",  # 超限时让 LLM 生成最终答案
)
```

**示例3：返回中间步骤**

```python
agent_executor = AgentExecutor(
    agent=agent,
    tools=tools,
    return_intermediate_steps=True,  # 返回中间步骤
    verbose=True
)

result = agent_executor.invoke({"input": "计算 1+1"})

# 查看中间步骤
for step in result["intermediate_steps"]:
    action, observation = step
    print(f"工具：{action.tool}")
    print(f"参数：{action.tool_input}")
    print(f"结果：{observation}\n")
```

**示例4：错误处理**

```python
@tool
def risky_operation(param: str) -> str:
    """可能失败的操作"""
    if "error" in param:
        raise ValueError("参数错误")
    return f"成功：{param}"

agent_executor = AgentExecutor(
    agent=agent,
    tools=[risky_operation],
    handle_parsing_errors=True,
    # 工具失败时，错误信息返回给 LLM
)

# 即使工具失败，Agent 也能继续
result = agent_executor.invoke({"input": "执行 error 操作"})
# LLM 看到错误，可能会换一种方式
```

**示例5：流式输出**

```python
# 流式观察 Agent 执行
for chunk in agent_executor.stream({"input": "搜索 LangChain"}):
    if "actions" in chunk:
        for action in chunk["actions"]:
            print(f"调用工具：{action.tool}")
    elif "steps" in chunk:
        for step in chunk["steps"]:
            print(f"结果：{step.observation}")
    elif "output" in chunk:
        print(f"最终答案：{chunk['output']}")
```

**示例6：异步执行**

```python
import asyncio

# 异步执行
async def async_agent():
    result = await agent_executor.ainvoke({"input": "搜索 LangChain"})
    return result

result = asyncio.run(async_agent())
```

### 总结

- **AgentExecutor**：协调 LLM 和工具，执行 Agent 循环
- **迭代控制**：max_iterations 防止无限循环
- **错误处理**：handle_parsing_errors 处理失败
- **中间步骤**：return_intermediate_steps 记录过程
- **流式输出**：stream 实时观察执行
- **异步支持**：ainvoke 异步执行
- **现代替代**：LangGraph 的 create_react_agent 更灵活

---

# 第8章 高级特性

本章探讨 LangChain 的高级特性——回调、追踪、缓存。这些特性让应用从"能跑"到"可观测、可调试、高性能"。学完本章，你将能够构建生产级可观测的 LLM 应用。

---

## 第30讲：Callbacks 回调

### 概念

**Callback（回调）** 是 LangChain 的钩子机制——在 LLM 调用、链执行、工具调用等事件发生时，自动触发用户定义的函数。回调用于日志记录、监控、追踪、自定义处理等。

LangChain 提供丰富的回调事件：`on_llm_start`、`on_llm_new_token`、`on_chain_start`、`on_tool_start` 等。你可以订阅这些事件，在事件发生时执行自定义逻辑。

### 原理

**回调机制原理**：LangChain 在执行各组件时，会在关键节点触发回调事件。用户实现 `BaseCallbackHandler`，定义各事件的处理逻辑。LangChain 在执行时自动调用这些处理器。

**回调事件原理**：LangChain 定义了丰富的事件类型：
- **LLM 事件**：on_llm_start、on_llm_new_token（流式）、on_llm_end、on_llm_error
- **Chain 事件**：on_chain_start、on_chain_end、on_chain_error
- **Tool 事件**：on_tool_start、on_tool_end、on_tool_error
- **Agent 事件**：on_agent_action、on_agent_finish

**回调用途原理**：
1. **日志记录**：记录每次调用的输入输出
2. **监控**：统计 token 消耗、响应时间
3. **追踪**：可视化执行流程
4. **自定义处理**：如保存到数据库、发送通知

**回调传递原理**：回调通过 `config` 参数传递——`chain.invoke(input, config={"callbacks": [handler]})`。回调会传递给链中所有组件，实现全链路监听。

### 例子

**示例1：自定义回调处理器**

```python
from langchain_core.callbacks import BaseCallbackHandler
from langchain_openai import ChatOpenAI

class MyCallbackHandler(BaseCallbackHandler):
    """自定义回调处理器"""
    
    def on_llm_start(self, serialized, prompts, **kwargs):
        print(f"[LLM 开始] 模型：{serialized.get('name', 'unknown')}")
    
    def on_llm_new_token(self, token, **kwargs):
        print(f"[新 token] {token}", end="")
    
    def on_llm_end(self, response, **kwargs):
        print(f"\n[LLM 结束] token 数：{response.llm_output.get('token_usage')}")
    
    def on_chain_start(self, serialized, inputs, **kwargs):
        print(f"[链开始] 输入：{str(inputs)[:50]}")
    
    def on_chain_end(self, outputs, **kwargs):
        print(f"[链结束] 输出：{str(outputs)[:50]}")
    
    def on_tool_start(self, serialized, input_str, **kwargs):
        print(f"[工具开始] {serialized.get('name')}")
    
    def on_tool_end(self, output, **kwargs):
        print(f"[工具结束] 结果：{str(output)[:50]}")

# 使用
llm = ChatOpenAI(model="gpt-4o-mini", streaming=True)
handler = MyCallbackHandler()

# 通过 config 传递回调
response = llm.invoke(
    "你好",
    config={"callbacks": [handler]}
)
```

**示例2：在 LCEL 链中使用回调**

```python
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

chain = ChatPromptTemplate.from_template("{input}") | llm | StrOutputParser()

# 回调会传递给链中所有组件
result = chain.invoke(
    {"input": "你好"},
    config={"callbacks": [handler]}
)
```

**示例3：内置 StdOutCallbackHandler**

```python
from langchain_core.callbacks import StdOutCallbackHandler

# LangChain 内置的打印回调
handler = StdOutCallbackHandler()

chain.invoke(
    {"input": "你好"},
    config={"callbacks": [handler]}
)
# 会打印执行过程
```

**示例4：统计 token 消耗**

```python
class TokenCounter(BaseCallbackHandler):
    """统计 token 消耗"""
    
    def __init__(self):
        self.total_tokens = 0
        self.total_cost = 0
    
    def on_llm_end(self, response, **kwargs):
        usage = response.llm_output.get("token_usage", {})
        input_tokens = usage.get("prompt_tokens", 0)
        output_tokens = usage.get("completion_tokens", 0)
        self.total_tokens += input_tokens + output_tokens
        # 假设 GPT-4o-mini 价格
        self.total_cost += (input_tokens * 0.15 + output_tokens * 0.6) / 1000
    
    def get_stats(self):
        return {"total_tokens": self.total_tokens, "total_cost": self.total_cost}

counter = TokenCounter()
chain.invoke({"input": "你好"}, config={"callbacks": [counter]})
chain.invoke({"input": "再见"}, config={"callbacks": [counter]})
print(f"统计：{counter.get_stats()}")
```

**示例5：全局回调**

```python
from langchain_core.globals import set_llm_cache, set_debug

# 开启调试模式（全局打印执行过程）
set_debug(True)

chain.invoke({"input": "你好"})
# 会自动打印执行过程
```

### 总结

- **Callback**：在事件发生时触发自定义函数
- **事件类型**：LLM、Chain、Tool、Agent 各类事件
- **用途**：日志、监控、追踪、自定义处理
- **传递方式**：通过 config 的 callbacks 参数
- **内置处理器**：StdOutCallbackHandler 打印过程
- **全局调试**：set_debug(True) 开启全局调试

---

## 第31讲：追踪与调试

### 概念

**追踪（Tracing）** 是 LangChain 提供的执行可视化能力——记录每次 LLM 调用、链执行、工具调用的详细信息，包括输入输出、耗时、token 消耗等。追踪让开发者能"看到"应用内部发生了什么，是调试和优化的利器。

LangSmith 是 LangChain 官方的追踪平台，提供可视化界面、评估、数据集管理。本讲介绍 LangSmith 追踪和本地调试技巧。

### 原理

**追踪原理**：LangChain 在执行时，通过回调机制记录每个组件的执行信息——输入、输出、耗时、token、错误等。这些信息发送到 LangSmith 或本地存储，可视化展示执行流程。

**LangSmith 原理**：LangSmith 是 LangChain 官方的 LLM 应用观测平台。设置 `LANGCHAIN_TRACING_V2=true` 和 API Key 后，所有执行自动上报到 LangSmith，在 Web 界面查看。

**追踪的价值原理**：
1. **调试**：看到每步输入输出，定位问题
2. **性能分析**：识别慢步骤、高 token 消耗
3. **质量评估**：对比不同提示/模型的效果
4. **数据集构建**：从真实执行中收集测试用例

**本地调试原理**：不使用 LangSmith 时，可以用 `set_debug(True)` 打印详细执行信息，或用自定义回调记录到文件。

### 例子

**示例1：配置 LangSmith 追踪**

```bash
# 在 .env 中配置
LANGCHAIN_TRACING_V2=true
LANGCHAIN_API_KEY=lsv2_your_key
LANGCHAIN_PROJECT=my-project
```

```python
import os
from dotenv import load_dotenv
from langchain_openai import ChatOpenAI

load_dotenv()  # 加载环境变量

# 配置后，所有调用自动追踪
llm = ChatOpenAI(model="gpt-4o-mini")
response = llm.invoke("你好")

# 到 LangSmith 界面查看追踪
# https://smith.langchain.com
```

**示例2：本地调试模式**

```python
from langchain_core.globals import set_debug, set_verbose

# 详细调试（打印所有输入输出）
set_debug(True)

# 简洁输出（打印关键步骤）
set_verbose(True)

chain.invoke({"input": "你好"})
# 会打印执行过程
```

**示例3：自定义追踪记录**

```python
import json
import time
from langchain_core.callbacks import BaseCallbackHandler

class FileTracingHandler(BaseCallbackHandler):
    """把追踪信息记录到文件"""
    
    def __init__(self, file_path="trace.jsonl"):
        self.file_path = file_path
        self.start_times = {}
    
    def _log(self, event, data):
        with open(self.file_path, "a") as f:
            f.write(json.dumps({"event": event, "data": data, "time": time.time()}) + "\n")
    
    def on_llm_start(self, serialized, prompts, **kwargs):
        self.start_times["llm"] = time.time()
        self._log("llm_start", {"prompts": prompts})
    
    def on_llm_end(self, response, **kwargs):
        duration = time.time() - self.start_times.get("llm", time.time())
        self._log("llm_end", {
            "duration": duration,
            "output": response.llm_output
        })

# 使用
handler = FileTracingHandler()
chain.invoke({"input": "你好"}, config={"callbacks": [handler]})
```

**示例4：在 LangSmith 中查看追踪**

```python
# 配置追踪后，每次调用都会在 LangSmith 创建一个 trace
# trace 包含：
# - 完整的执行流程（树状）
# - 每步的输入输出
# - 耗时和 token 消耗
# - 错误信息（如果有）

# 可以在 LangSmith 界面：
# - 查看执行流程图
# - 检查每步输入输出
# - 分析性能瓶颈
# - 创建测试数据集
```

**示例5：对比不同配置**

```python
# 用不同模型/提示测试，在 LangSmith 对比
for model_name in ["gpt-4o-mini", "gpt-4o"]:
    llm = ChatOpenAI(model=model_name)
    chain = ChatPromptTemplate.from_template("{input}") | llm | StrOutputParser()
    
    # 每次调用带 metadata，便于在 LangSmith 区分
    result = chain.invoke(
        {"input": "介绍 Python"},
        config={"metadata": {"model": model_name, "experiment": "model_comparison"}}
    )
```

### 总结

- **追踪**：记录执行详情，可视化展示
- **LangSmith**：官方追踪平台，Web 界面查看
- **配置**：设置 LANGCHAIN_TRACING_V2=true 和 API Key
- **本地调试**：set_debug(True) 或 set_verbose(True)
- **自定义追踪**：用回调记录到文件
- **价值**：调试、性能分析、质量评估、数据集构建

---

## 第32讲：缓存机制

### 概念

**缓存（Caching）** 是 LangChain 提升性能和降低成本的机制——把 LLM 调用结果缓存，相同输入直接返回缓存结果，不重复调用 LLM。缓存能大幅降低成本（避免重复调用）和提升速度（直接返回）。

LangChain 提供多种缓存：`InMemoryCache`（内存）、`SQLiteCache`（SQLite）、`RedisCache`（Redis）、`UpstashCache`（云）等。

### 原理

**缓存原理**：LLM 调用是昂贵的——消耗 token 和时间。如果相同输入会得到相同输出（temperature=0 时），缓存结果可以避免重复调用。下次相同输入直接返回缓存，零成本零延迟。

**缓存键原理**：缓存以"模型 + 提示 + 参数"为键。相同键直接返回缓存。temperature > 0 时输出不确定，缓存可能不合适（但 LangChain 仍会缓存，因为相同输入相同参数会得到相同输出）。

**缓存选型原理**：
- **InMemoryCache**：内存缓存，最快，进程结束丢失
- **SQLiteCache**：SQLite 文件，持久化，单机
- **RedisCache**：Redis，分布式，多进程共享
- **UpstashCache**：云 Redis，免运维

**缓存适用场景原理**：
1. **开发测试**：避免重复调用浪费 token
2. **FAQ 场景**：用户常问相同问题
3. **批处理**：处理重复数据
4. **演示 Demo**：快速响应

**缓存局限原理**：
1. **不适合动态内容**：如实时搜索结果
2. **temperature > 0 谨慎**：输出有随机性
3. **缓存失效**：模型更新后旧缓存可能过时
4. **内存管理**：缓存太多占用资源

### 例子

**示例1：内存缓存**

```python
from langchain_core.globals import set_llm_cache
from langchain_core.caches import InMemoryCache
from langchain_openai import ChatOpenAI
import time

# 设置全局缓存
set_llm_cache(InMemoryCache())

llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)

# 第一次调用（实际调用 LLM）
start = time.time()
result1 = llm.invoke("什么是 Python？")
print(f"第一次：{time.time()-start:.2f}s")

# 第二次相同输入（命中缓存）
start = time.time()
result2 = llm.invoke("什么是 Python？")
print(f"第二次：{time.time()-start:.2f}s")  # 几乎0秒

print(f"结果相同：{result1.content == result2.content}")
```

**示例2：SQLite 缓存（持久化）**

```python
from langchain_community.cache import SQLiteCache

# 持久化到文件
set_llm_cache(SQLiteCache(database_path="langchain_cache.db"))

# 第一次调用，缓存到 SQLite
llm.invoke("你好")

# 即使重启程序，相同输入仍命中缓存
# 因为缓存在 langchain_cache.db 文件中
```

**示例3：Redis 缓存（分布式）**

```python
from langchain_community.cache import RedisCache
import redis

# Redis 缓存，多进程共享
redis_client = redis.Redis(host="localhost", port=6379)
set_llm_cache(RedisCache(redis_client))

# 多个进程/服务共享缓存
```

**示例4：缓存与 LCEL 链**

```python
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

chain = ChatPromptTemplate.from_template("{input}") | llm | StrOutputParser()

# 链的缓存基于完整输入
result1 = chain.invoke({"input": "你好"})
result2 = chain.invoke({"input": "你好"})  # 命中缓存
```

**示例5：单次调用缓存**

```python
# 不设置全局缓存，单次调用用 cache
result = llm.invoke(
    "你好",
    config={"cache": True}  # 这次调用启用缓存
)
```

**示例6：缓存统计**

```python
class CacheStats:
    """缓存统计"""
    def __init__(self):
        self.hits = 0
        self.misses = 0

stats = CacheStats()

class StatsCallback(BaseCallbackHandler):
    def on_llm_end(self, response, **kwargs):
        if hasattr(response, 'cached'):
            stats.hits += 1
        else:
            stats.misses += 1

# 使用
chain.invoke({"input": "你好"}, config={"callbacks": [StatsCallback()]})
print(f"命中：{stats.hits}，未命中：{stats.misses}")
```

### 总结

- **缓存**：相同输入直接返回缓存，避免重复调用
- **缓存键**：模型 + 提示 + 参数
- **选型**：InMemory（快）、SQLite（持久）、Redis（分布式）
- **适用场景**：开发测试、FAQ、批处理、Demo
- **局限**：不适合动态内容，temperature > 0 谨慎
- **生产价值**：降低成本、提升速度

---

# 第9章 实战应用

本章是课程的实战篇。我们将综合运用前 8 章的知识，构建三个完整的实战项目：RAG 应用、对话机器人、生产部署。学完本章，你将具备从零到一构建生产级 LangChain 应用的能力。

---

## 第33讲：RAG 应用实战

### 概念

本讲构建一个完整的 RAG（检索增强生成）应用——基于私有文档的问答系统。这个应用能：加载文档、分割、嵌入、存储、检索、生成回答。我们将综合运用 Document Loader、Text Splitter、Embeddings、Vector Store、Retriever、LCEL 等知识，构建一个生产可用的 RAG 系统。

### 原理

**RAG 完整流程原理**：
```
[文档] → [Loader] → [Splitter] → [Embeddings] → [VectorStore]
                                                    ↓
[用户问题] → [Embeddings] → [检索] → [相关文档] → [LLM] → [回答]
```

1. **离线阶段**：加载文档 → 分割 → 嵌入 → 存入向量库
2. **在线阶段**：用户问题 → 嵌入 → 向量检索 → 取相关文档 → 拼入提示 → LLM 生成回答

**RAG 的核心价值原理**：
1. **知识更新**：无需重新训练模型，更新文档即可
2. **私有数据**：可以基于私有文档回答，不泄露给模型厂商
3. **可追溯**：回答有据可查，标注来源
4. **成本低**：比微调模型便宜得多

**RAG 质量优化原理**：
1. **分割质量**：合理 chunk_size 和 overlap
2. **嵌入质量**：选好的嵌入模型
3. **检索质量**：用 MMR、MultiQuery 等提升召回
4. **提示质量**：明确要求基于上下文回答

### 例子

**完整 RAG 应用实现**

```python
import os
from dotenv import load_dotenv
from langchain_openai import ChatOpenAI, OpenAIEmbeddings
from langchain_community.document_loaders import TextLoader, PyPDFLoader
from langchain_text_splitters import RecursiveCharacterTextSplitter
from langchain_community.vectorstores import FAISS
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough

load_dotenv()

# 1. 初始化组件
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)
embeddings = OpenAIEmbeddings()

# 2. 加载文档
def load_documents(directory="./docs"):
    """加载目录下所有文档"""
    documents = []
    for file in os.listdir(directory):
        path = os.path.join(directory, file)
        if file.endswith(".txt"):
            documents.extend(TextLoader(path).load())
        elif file.endswith(".pdf"):
            documents.extend(PyPDFLoader(path).load())
    return documents

# 3. 分割文档
def split_documents(documents):
    """分割文档为小块"""
    splitter = RecursiveCharacterTextSplitter(
        chunk_size=500,
        chunk_overlap=50,
        separators=["\n\n", "\n", "。", "，", " "]
    )
    return splitter.split_documents(documents)

# 4. 创建向量库
def create_vectorstore(splits):
    """创建向量库"""
    return FAISS.from_documents(splits, embeddings)

# 5. 构建 RAG 链
def create_rag_chain(retriever):
    """创建 RAG 链"""
    
    prompt = ChatPromptTemplate.from_template("""
你是文档问答助手。基于以下检索到的信息回答问题。

要求：
1. 回答必须基于检索信息，不要编造
2. 如果信息不足，坦诚告知
3. 在关键信息后标注来源编号

检索信息：
{context}

问题：{question}

回答：""")
    
    def format_docs(docs):
        return "\n\n".join([
            f"[来源{i+1}] {doc.page_content}\n来源：{doc.metadata.get('source', 'unknown')}"
            for i, doc in enumerate(docs)
        ])
    
    rag_chain = (
        {
            "context": retriever | format_docs,
            "question": RunnablePassthrough()
        }
        | prompt
        | llm
        | StrOutputParser()
    )
    
    return rag_chain

# 6. 完整 RAG 应用
class RAGApp:
    def __init__(self, docs_directory="./docs"):
        # 加载并处理文档
        documents = load_documents(docs_directory)
        splits = split_documents(documents)
        self.vectorstore = create_vectorstore(splits)
        
        # 创建检索器
        self.retriever = self.vectorstore.as_retriever(
            search_type="mmr",  # 兼顾相关性和多样性
            search_kwargs={"k": 3, "fetch_k": 10}
        )
        
        # 创建 RAG 链
        self.chain = create_rag_chain(self.retriever)
    
    def ask(self, question):
        """提问"""
        # 获取相关文档（用于展示来源）
        docs = self.retriever.invoke(question)
        
        # 生成回答
        answer = self.chain.invoke(question)
        
        return {
            "question": question,
            "answer": answer,
            "sources": [d.metadata.get("source", "unknown") for d in docs]
        }

# 7. 使用
if __name__ == "__main__":
    # 初始化 RAG 应用
    rag = RAGApp("./docs")
    
    # 提问
    result = rag.ask("LangChain 的核心组件有哪些？")
    print("问题：", result["question"])
    print("\n回答：", result["answer"])
    print("\n来源：", result["sources"])
```

**进阶：带查询改写的 RAG**

```python
from langchain.retrievers.multi_query import MultiQueryRetriever

# 用 LLM 改写查询，提升召回
retriever = MultiQueryRetriever.from_llm(
    retriever=vectorstore.as_retriever(),
    llm=llm
)

# Agent 会自动生成多个改写查询，分别检索，合并去重
```

**进阶：带对话历史的 RAG**

```python
from langchain_core.runnables.history import RunnableWithMessageHistory
from langchain_community.chat_message_histories import ChatMessageHistory

# 带历史的 RAG 提示
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是文档问答助手。基于检索信息回答。"),
    MessagesPlaceholder(variable_name="history"),
    ("human", "检索信息：{context}\n\n问题：{question}")
])

rag_chain = (
    {
        "context": retriever | format_docs,
        "question": RunnablePassthrough(),
        "history": lambda x: []  # 从历史加载
    }
    | prompt
    | llm
    | StrOutputParser()
)

# 包装为带历史的链
rag_with_history = RunnableWithMessageHistory(
    rag_chain,
    lambda session_id: ChatMessageHistory(),
    input_messages_key="question",
    history_messages_key="history"
)
```

### 总结

- **RAG 流程**：加载→分割→嵌入→存储→检索→生成
- **核心价值**：知识更新、私有数据、可追溯、成本低
- **质量优化**：分割质量、嵌入质量、检索质量、提示质量
- **进阶优化**：查询改写（MultiQuery）、对话历史
- **生产化**：持久化向量库、缓存、监控
- **适用场景**：文档问答、知识库、客服、研究助手

---

## 第34讲：对话机器人实战

### 概念

本讲构建一个生产级对话机器人，综合运用提示工程、记忆管理、流式输出、错误处理等能力。这个机器人能：保持人设、记住对话历史、流式输出、处理错误、支持多用户。这是 LangChain 在对话场景的典型应用。

### 原理

**对话机器人的核心能力原理**：
1. **人设保持**：通过 SystemMessage 设定角色，每轮都传入
2. **记忆管理**：用 RunnableWithMessageHistory 管理对话历史
3. **流式输出**：用 stream/astream 实时输出
4. **多用户支持**：用 session_id 隔离不同用户
5. **错误处理**：LLM 调用失败时降级处理

**人设保持原理**：通过 SystemMessage 设定 Agent 的角色、语气、行为规范。SystemMessage 放在消息列表开头，每轮对话都保留。这样 LLM 在每轮回复时都会"记住"自己的人设。

**多用户隔离原理**：用 session_id（通常是用户 ID）隔离不同用户的对话。每个 session_id 有独立的对话历史，互不影响。这是生产级对话机器人的基础。

**流式输出原理**：用 `astream` 异步流式输出，让用户能实时看到 AI 的回复生成过程。配合 SSE（Server-Sent Events）实现 Web 端实时推送。

### 例子

**完整对话机器人实现**

```python
import os
from dotenv import load_dotenv
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables.history import RunnableWithMessageHistory
from langchain_community.chat_message_histories import ChatMessageHistory

load_dotenv()

# 1. 初始化 LLM
llm = ChatOpenAI(
    model="gpt-4o-mini",
    temperature=0.7,
    streaming=True,  # 启用流式
    timeout=30,
    max_retries=2
)

# 2. 定义人设
SYSTEM_PROMPT = """你是「小智」，一个友好、专业的 AI 助手。

你的特点：
- 回答简洁明了，避免冗长
- 善于用类比解释复杂概念
- 不确定时坦诚告知，不编造信息
- 适时引导用户深入思考

请始终保持这个人设进行对话。"""

# 3. 创建对话链
prompt = ChatPromptTemplate.from_messages([
    ("system", SYSTEM_PROMPT),
    MessagesPlaceholder(variable_name="history"),
    ("human", "{input}")
])

chain = prompt | llm | StrOutputParser()

# 4. 用 RunnableWithMessageHistory 包装
# 存储不同 session 的历史
session_histories = {}

def get_session_history(session_id: str):
    if session_id not in session_histories:
        session_histories[session_id] = ChatMessageHistory()
    return session_histories[session_id]

chain_with_history = RunnableWithMessageHistory(
    chain,
    get_session_history,
    input_messages_key="input",
    history_messages_key="history"
)

# 5. 对话函数
def chat(session_id: str, user_input: str):
    """单轮对话"""
    config = {"configurable": {"session_id": session_id}}
    
    try:
        response = chain_with_history.invoke(
            {"input": user_input},
            config=config
        )
        return response
    except Exception as e:
        return f"抱歉，服务暂时不可用：{e}"

def chat_stream(session_id: str, user_input: str):
    """流式对话"""
    config = {"configurable": {"session_id": session_id}}
    
    try:
        for chunk in chain_with_history.stream(
            {"input": user_input},
            config=config
        ):
            yield chunk
    except Exception as e:
        yield f"抱歉，服务暂时不可用：{e}"

# 6. 交互式对话
def interactive_chat():
    """交互式多轮对话"""
    print("🤖 小智已上线，输入 'quit' 退出\n")
    
    session_id = "user_1"  # 实际从用户登录获取
    
    while True:
        user_input = input("你：")
        if user_input.lower() in ["quit", "exit", "退出"]:
            print("小智：再见！")
            break
        
        print("小智：", end="", flush=True)
        for chunk in chat_stream(session_id, user_input):
            print(chunk, end="", flush=True)
        print()  # 换行

if __name__ == "__main__":
    interactive_chat()
```

**进阶：带工具的对话机器人**

```python
from langchain_core.tools import tool
from langgraph.prebuilt import create_react_agent

@tool
def get_weather(city: str) -> str:
    """获取天气"""
    weather = {"北京": "晴 25°C", "上海": "多云 28°C"}
    return weather.get(city, f"未知城市 {city}")

@tool
def get_time() -> str:
    """获取当前时间"""
    from datetime import datetime
    return datetime.now().strftime("%Y-%m-%d %H:%M:%S")

# 用 LangGraph 创建带工具的对话 Agent
agent = create_react_agent(
    model=llm,
    tools=[get_weather, get_time],
    prompt=SYSTEM_PROMPT + "\n你可以查询天气和时间。"
)

# 带工具的对话
def chat_with_tools(session_id, user_input):
    config = {"configurable": {"session_id": session_id}}
    result = agent.invoke(
        {"messages": [("user", user_input)]},
        config=config
    )
    return result["messages"][-1].content
```

**进阶：Web API 封装**

```python
from fastapi import FastAPI
from fastapi.responses import StreamingResponse
from pydantic import BaseModel

app = FastAPI()

class ChatRequest(BaseModel):
    session_id: str
    message: str

@app.post("/chat")
async def chat(request: ChatRequest):
    """普通对话"""
    response = chat(request.session_id, request.message)
    return {"reply": response}

@app.post("/chat/stream")
async def chat_stream_api(request: ChatRequest):
    """流式对话"""
    async def event_stream():
        for chunk in chat_stream(request.session_id, request.message):
            yield f"data: {chunk}\n\n"
    
    return StreamingResponse(event_stream(), media_type="text/event-stream")
```

### 总结

- **对话机器人核心**：人设、记忆、流式、多用户、错误处理
- **人设保持**：SystemMessage 设定角色，每轮传入
- **记忆管理**：RunnableWithMessageHistory + session_id
- **流式输出**：stream/astream 实时输出
- **多用户隔离**：session_id 区分不同用户
- **工具集成**：用 LangGraph 的 create_react_agent
- **Web 封装**：FastAPI + SSE 实现流式 API

---

## 第35讲：生产部署

### 概念

本讲探讨如何将 LangChain 应用部署到生产环境。包括：API 服务封装、并发与性能优化、监控与日志、错误处理与重试、成本控制、安全考虑。这是从"能跑"到"能扛住生产流量"的关键一步。

### 原理

**生产化核心挑战原理**：
1. **并发**：多用户同时使用，需线程安全
2. **性能**：响应时间、吞吐量
3. **可靠性**：错误处理、重试、降级
4. **可观测**：日志、监控、追踪
5. **成本**：LLM 调用成本控制
6. **安全**：输入验证、权限控制

**API 封装原理**：将 LangChain 应用封装为 HTTP API，前端通过 REST 调用。常用框架：FastAPI（Python 原生）、Flask。LangChain 也提供 LangServe 官方部署方案。

**并发模型原理**：LangChain 链本身是线程安全的（不可变），但共享资源（如 Memory、VectorStore）需考虑并发。用 Redis 等支持并发的存储。

**错误处理原理**：生产环境必须处理：
- LLM 调用失败（超时、限流、API 错误）
- 工具执行失败（外部服务不可用）
- 用户输入异常（恶意输入、超长输入）

**成本控制原理**：LLM 调用是主要成本。控制策略：
- 缓存常见问题答案
- 限制对话历史长度
- 选择合适模型（简单任务用小模型）
- 监控 token 消耗
- 设置用户配额

### 例子

**示例1：FastAPI 封装 LangChain**

```python
import os
from dotenv import load_dotenv
from fastapi import FastAPI, HTTPException
from fastapi.responses import StreamingResponse
from pydantic import BaseModel, Field
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables.history import RunnableWithMessageHistory
from langchain_community.chat_message_histories import RedisChatMessageHistory
import json

load_dotenv()
app = FastAPI(title="LangChain API")

# 初始化
llm = ChatOpenAI(
    model="gpt-4o-mini",
    temperature=0.7,
    streaming=True,
    timeout=30,
    max_retries=3
)

# 提示模板
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是友好助手"),
    ("placeholder", "{history}"),
    ("human", "{input}")
])

chain = prompt | llm | StrOutputParser()

# 用 Redis 存历史（支持多进程）
def get_history(session_id: str):
    return RedisChatMessageHistory(
        session_id=session_id,
        url=os.getenv("REDIS_URL", "redis://localhost:6379")
    )

chain_with_history = RunnableWithMessageHistory(
    chain,
    get_history,
    input_messages_key="input",
    history_messages_key="history"
)

# 请求模型
class ChatRequest(BaseModel):
    session_id: str = Field(..., description="会话ID")
    message: str = Field(..., max_length=1000, description="用户消息")

class ChatResponse(BaseModel):
    reply: str
    session_id: str

# 普通接口
@app.post("/chat", response_model=ChatResponse)
async def chat(request: ChatRequest):
    try:
        config = {"configurable": {"session_id": request.session_id}}
        reply = chain_with_history.invoke(
            {"input": request.message},
            config=config
        )
        return ChatResponse(reply=reply, session_id=request.session_id)
    except Exception as e:
        raise HTTPException(status_code=500, detail=f"服务错误：{e}")

# 流式接口
@app.post("/chat/stream")
async def chat_stream(request: ChatRequest):
    config = {"configurable": {"session_id": request.session_id}}
    
    async def event_stream():
        try:
            async for chunk in chain_with_history.astream(
                {"input": request.message},
                config=config
            ):
                yield f"data: {json.dumps({'content': chunk})}\n\n"
            yield f"data: {json.dumps({'done': True})}\n\n"
        except Exception as e:
            yield f"data: {json.dumps({'error': str(e)})}\n\n"
    
    return StreamingResponse(event_stream(), media_type="text/event-stream")

# 健康检查
@app.get("/health")
async def health():
    return {"status": "ok"}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

**示例2：错误处理与降级**

```python
from langchain_openai import ChatOpenAI
from langchain_core.messages import AIMessage

# 带降级的 LLM
class ResilientLLM:
    """带降级的 LLM 包装"""
    
    def __init__(self):
        self.primary = ChatOpenAI(model="gpt-4o", timeout=30, max_retries=2)
        self.fallback = ChatOpenAI(model="gpt-4o-mini", timeout=15, max_retries=3)
    
    async def ainvoke(self, messages):
        try:
            return await self.primary.ainvoke(messages)
        except Exception as e:
            print(f"主模型失败，降级：{e}")
            try:
                return await self.fallback.ainvoke(messages)
            except Exception as e:
                print(f"降级模型也失败：{e}")
                return AIMessage(content="抱歉，服务暂时不可用，请稍后重试。")

resilient_llm = ResilientLLM()
```

**示例3：成本控制**

```python
import tiktoken

class CostControlledChain:
    """带成本控制的链"""
    
    def __init__(self, llm, max_input_tokens=4000, max_history=20):
        self.llm = llm
        self.max_input_tokens = max_input_tokens
        self.max_history = max_history
    
    def count_tokens(self, messages):
        enc = tiktoken.encoding_for_model("gpt-4o-mini")
        total = 0
        for msg in messages:
            total += len(enc.encode(str(msg.content)))
        return total
    
    def trim_history(self, messages):
        """裁剪历史"""
        # 限制历史长度
        if len(messages) > self.max_history:
            messages = messages[-self.max_history:]
        
        # 限制 token
        while len(messages) > 2 and self.count_tokens(messages) > self.max_input_tokens:
            messages = messages[1:]
        
        return messages
    
    async def ainvoke(self, messages):
        # 裁剪
        messages = self.trim_history(messages)
        
        # 记录 token
        input_tokens = self.count_tokens(messages)
        print(f"输入 token：{input_tokens}")
        
        # 调用
        response = await self.llm.ainvoke(messages)
        
        return response
```

**示例4：监控与日志**

```python
import logging
import time
from langchain_core.callbacks import BaseCallbackHandler

# 配置日志
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('app.log'),
        logging.StreamHandler()
    ]
)
logger = logging.getLogger(__name__)

class MonitoringHandler(BaseCallbackHandler):
    """监控回调"""
    
    def __init__(self):
        self.start_times = {}
        self.total_tokens = 0
        self.total_cost = 0
    
    def on_llm_start(self, serialized, prompts, **kwargs):
        self.start_times["llm"] = time.time()
        logger.info(f"LLM 调用开始")
    
    def on_llm_end(self, response, **kwargs):
        duration = time.time() - self.start_times.get("llm", time.time())
        usage = response.llm_output.get("token_usage", {})
        tokens = usage.get("total_tokens", 0)
        self.total_tokens += tokens
        self.total_cost += tokens * 0.000001  # 简化成本计算
        
        logger.info(f"LLM 调用完成：{duration:.2f}s, {tokens} tokens")
        logger.info(f"累计：{self.total_tokens} tokens, ${self.total_cost:.4f}")

# 使用
monitor = MonitoringHandler()
chain.invoke(
    {"input": "你好"},
    config={"callbacks": [monitor]}
)
```

**示例5：Docker 部署**

```dockerfile
# Dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "4"]
```

```yaml
# docker-compose.yml
version: '3.8'
services:
  api:
    build: .
    ports: ["8000:8000"]
    environment:
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - REDIS_URL=redis://redis:6379
    depends_on: [redis]
  
  redis:
    image: redis:7
    ports: ["6379:6379"]
    volumes: ["redis_data:/data"]

volumes:
  redis_data:
```

**示例6：生产部署清单**

```python
# 生产环境配置
PRODUCTION_CONFIG = {
    # LLM 配置
    "llm": {
        "model": "gpt-4o-mini",
        "temperature": 0,
        "timeout": 30,
        "max_retries": 3,
        "streaming": True
    },
    # 记忆（用 Redis 持久化）
    "memory": {
        "backend": "redis",
        "url": "redis://redis:6379"
    },
    # 成本控制
    "cost_control": {
        "max_input_tokens": 4000,
        "max_history": 20,
        "daily_token_limit": 1000000
    },
    # 监控
    "monitoring": {
        "log_level": "INFO",
        "langsmith_tracing": True,
        "metrics": True
    },
    # 限流
    "rate_limit": {
        "requests_per_minute": 60,
        "tokens_per_day": 1000000
    }
}
```

### 总结

- **生产化挑战**：并发、性能、可靠性、可观测、成本、安全
- **API 封装**：FastAPI + StreamingResponse 实现流式 API
- **并发模型**：用 Redis 等支持并发的存储
- **错误处理**：LLM 失败重试、降级、兜底回复
- **成本控制**：限制历史长度、token 上限、用户配额
- **监控日志**：回调记录耗时、token、成本
- **部署方案**：Docker + docker-compose，配合 Redis 持久化

---

## 课程结语

恭喜你完成了 LangChain 系统教程的全部 35 讲！让我们回顾这段学习旅程：

**学习路径回顾**：
- **第1章 基础入门**：建立对 LangChain 的整体认知，搭建环境，运行第一个程序
- **第2章 模型**：深入 LLM 与 ChatModel，掌握调用、流式、多模态
- **第3章 提示工程**：PromptTemplate、FewShot、输出解析器
- **第4章 LCEL 表达式语言**：Runnable、链式调用、并行、流式
- **第5章 链与记忆**：经典链结构与对话记忆机制
- **第6章 数据增强**：文档加载、分割、嵌入、向量存储、检索
- **第7章 Agent 与工具**：Agent 架构、工具定义、ReAct 模式
- **第8章 高级特性**：回调、追踪、缓存
- **第9章 实战应用**：RAG、对话机器人、生产部署

**核心知识体系**：
1. **组件思维**：将 LLM 应用拆分为可复用组件
2. **LCEL 组合**：用 `|` 声明式组合组件，自动支持批处理、流式、异步
3. **提示工程**：PromptTemplate、FewShot、输出解析器构建结构化交互
4. **记忆管理**：让应用具备上下文感知能力
5. **RAG 流程**：加载→分割→嵌入→存储→检索→生成
6. **Agent 架构**：LLM 自主决策调用工具完成复杂任务
7. **生产能力**：回调、追踪、缓存、监控、部署

**下一步学习建议**：
1. **实践项目**：选择一个真实场景，从零构建完整应用
2. **深入 LangGraph**：学习 LangGraph 构建更复杂的 Agent 工作流
3. **使用 LangSmith**：用 LangSmith 追踪、评估、优化应用
4. **关注社区**：LangChain 生态快速发展，持续学习新特性
5. **阅读源码**：深入 LangChain 源码，理解设计哲学

LangChain 是一个快速发展的生态，本课程建立的基础让你能够跟上它的演进。祝你在 LLM 应用开发之路上越走越远！

