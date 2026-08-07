# LangGraph 系统教程

> 从零基础到生产级 Agent 应用的完整学习路径
> 共 32 讲 / 9 章

---

## 课程总览

### 学习目标

本课程旨在帮助学习者从零开始，系统掌握 LangGraph 这一强大的 Agent 编排框架。完成本课程后，你将能够：

1. 理解 LangGraph 的核心设计哲学——将 Agent 视为图（Graph）结构，通过状态（State）在节点（Node）间流转来实现复杂逻辑
2. 独立设计并构建从简单到复杂的各类 Agent 应用，包括对话机器人、工具调用 Agent、多 Agent 协作系统
3. 掌握状态管理、消息处理、工具调用、循环控制、并行执行等核心技术
4. 实现人机交互（Human-in-the-loop）、流式输出、持久化记忆等生产级特性
5. 将 LangGraph 应用部署到生产环境，处理并发、监控、调试等工程问题

### 课程结构

本课程遵循"基础 → 核心 → 进阶 → 高级 → 实战"的渐进式学习路径：

- **第1章 基础入门**：建立对 LangGraph 的整体认知，搭建环境，运行第一个程序
- **第2章 图的构建**：深入 State、Node、Edge 三大核心组件
- **第3章 消息与对话**：掌握消息体系，构建多轮对话 Agent
- **第4章 工具调用**：让 Agent 具备调用外部工具的能力
- **第5章 Agent 架构**：从 ReAct 到多 Agent 协作的架构演进
- **第6章 高级控制流**：循环、并行、子图、命令跳转等高级技巧
- **第7章 人机交互与流式**：让 Agent 更智能、更友好
- **第8章 持久化与记忆**：让 Agent 拥有长期记忆
- **第9章 实战应用**：综合运用，构建真实项目

### 详细章节目录

#### 第1章 基础入门
- 第1讲：LangGraph 简介与定位
- 第2讲：核心概念总览
- 第3讲：环境搭建与 Hello World
- 第4讲：第一个 StateGraph 程序

#### 第2章 图的构建
- 第5讲：State 状态详解
- 第6讲：Node 节点详解
- 第7讲：Edge 边详解
- 第8讲：Conditional Edge 条件边
- 第9讲：Reducer 与状态合并

#### 第3章 消息与对话
- 第10讲：Messages 消息体系
- 第11讲：MessagesState 与对话历史
- 第12讲：多轮对话 Agent 构建

#### 第4章 工具调用
- 第13讲：工具定义与绑定
- 第14讲：ToolNode 工具节点
- 第15讲：完整工具调用流程

#### 第5章 Agent 架构
- 第16讲：ReAct Agent 原理与实现
- 第17讲：create_react_agent 详解
- 第18讲：多 Agent 系统
- 第19讲：Supervisor 层级 Agent

#### 第6章 高级控制流
- 第20讲：循环与迭代控制
- 第21讲：并行节点执行
- 第22讲：子图 Subgraph
- 第23讲：Command 命令与跳转

#### 第7章 人机交互与流式
- 第24讲：Human-in-the-loop 人机交互
- 第25讲：流式输出 Streaming
- 第26讲：中断与恢复

#### 第8章 持久化与记忆
- 第27讲：Checkpoint 持久化
- 第28讲：Memory Store 长期记忆
- 第29讲：跨会话记忆管理

#### 第9章 实战应用
- 第30讲：RAG Agent 实战
- 第31讲：客服机器人实战
- 第32讲：部署与生产化

---

# 第1章 基础入门

本章是整个课程的起点。我们将从 LangGraph 的定位与生态讲起，逐步建立对这一框架的整体认知，搭建开发环境，并运行第一个程序。学完本章，你将理解 LangGraph 为什么存在、它解决了什么问题，以及如何快速上手。

---

## 第1讲：LangGraph 简介与定位

### 概念

LangGraph 是由 LangChain 团队推出的一个用于构建**有状态、多角色、可循环**的大语言模型（LLM）应用的开源框架。它的核心思想是将复杂的 Agent 工作流抽象为**图（Graph）**结构——由节点（Node）和边（Edge）组成，状态（State）在节点之间流转，从而实现灵活的逻辑编排。

与传统的链式调用（Chain）不同，LangGraph 支持循环、条件分支、并行执行、人机交互等复杂控制流，使得构建真正智能的 Agent 成为可能。它既可以独立使用，也可以与 LangChain 生态无缝集成。

### 原理

LangGraph 的设计哲学源于一个核心洞察：**真正的 Agent 不是简单的"输入→LLM→输出"线性流程，而是需要循环、决策、工具调用、状态记忆的复杂系统**。

传统的 LangChain Chain 是有向无环图（DAG），无法表达"思考→行动→观察→再思考"这样的循环逻辑。而 Agent 的本质恰恰是循环的——ReAct 模式、Plan-and-Execute 模式都需要反复迭代。LangGraph 通过引入图结构，让开发者可以自由定义节点间的跳转关系，包括回到之前的节点（循环）、根据条件选择不同路径（分支）、同时执行多个节点（并行）。

底层实现上，LangGraph 基于 **Pregel** 模型（Google Pregel 论文提出的图计算模型），采用"超步（superstep）"机制：每个超步中，所有节点并行执行，执行完毕后状态合并，然后进入下一个超步。这种模型天然支持并行与状态聚合，非常适合 Agent 工作流。

LangGraph 在生态中的定位如下：

| 框架层级 | 代表 | 关注点 |
|---------|------|--------|
| 模型层 | OpenAI、Anthropic、各开源模型 | 提供基础 LLM 能力 |
| 集成层 | LangChain、LlamaIndex | 封装模型、工具、数据源 |
| **编排层** | **LangGraph** | **构建有状态、可循环的 Agent 工作流** |
| 应用层 | 各类 Agent 产品 | 面向用户的最终产品 |

### 例子

下面是一个最简概念示意，展示 LangGraph 与传统 Chain 的区别：

**传统 Chain（线性，无循环）：**
```
用户输入 → LLM → 输出
```

**LangGraph（图结构，支持循环）：**
```
        ┌──────────────────┐
        ↓                  │
[开始] → [Agent节点] → [工具节点] → [判断: 是否完成?]
              ↑                  ↓ 否
              └──────────────────
                                 ↓ 是
                              [结束]
```

安装 LangGraph 非常简单：

```python
# 安装核心包
pip install langgraph

# 如果需要与 LangChain 集成
pip install langgraph langchain langchain-openai
```

验证安装：

```python
import langgraph
print(langgraph.__version__)  # 输出版本号，如 0.2.x
```

### 总结

- **核心定位**：LangGraph 是 Agent 编排框架，将工作流抽象为图结构，支持循环、分支、并行
- **设计动机**：传统 Chain 是 DAG，无法表达 Agent 的循环逻辑；LangGraph 用图解决了这个问题
- **底层模型**：基于 Pregel 图计算模型，采用超步机制实现并行与状态聚合
- **生态位置**：位于集成层（LangChain）之上，应用层之下，专注编排
- **常见误区**：LangGraph 不是 LangChain 的替代品，而是补充；它可以独立使用，但与 LangChain 配合更佳

---

## 第2讲：核心概念总览

### 概念

LangGraph 的核心概念可以归纳为"**一图三件**"：

1. **State（状态）**：在图中流转的数据容器，是所有节点共享的"黑板"
2. **Node（节点）**：执行具体逻辑的函数，接收状态、修改状态、返回状态
3. **Edge（边）**：定义节点间的跳转关系，包括普通边和条件边

此外还有几个辅助概念：**StateGraph**（状态图的容器）、**Compile**（编译图）、**Reducer**（状态合并策略）、**Checkpoint**（状态快照）。本讲先建立整体认知，后续章节会逐一深入。

### 原理

LangGraph 的工作机制可以类比为一个"接力赛 + 共享黑板"的模型：

想象一个团队在解决一个问题，他们共用一块黑板（State）。每个成员（Node）拿到黑板后，根据自己的职责修改黑板上的内容（比如 LLM 节点生成回复、工具节点执行计算），然后把黑板传给下一个成员（Edge 决定传给谁）。成员之间可以反复传递（循环），也可以根据黑板上的内容决定下一步找谁（条件边）。

**状态流转机制**：当一个节点执行完毕返回新状态时，LangGraph 不会直接替换旧状态，而是通过 **Reducer** 决定如何合并。默认 Reducer 是"覆盖"（用新值替换旧值），但你可以自定义 Reducer 实现累加、合并等逻辑。比如对话历史 `messages` 字段，通常用 `add_messages` Reducer，让新消息追加到列表而非覆盖。

**执行流程**：
1. 用户调用 `graph.invoke(input)` 或 `graph.stream(input)`
2. LangGraph 从入口节点（START）开始执行
3. 每个节点接收当前状态，执行逻辑，返回状态更新
4. 状态通过 Reducer 合并到全局状态
5. 根据边定义，决定下一个节点
6. 重复 3-5，直到到达 END 节点或没有下一个节点

**图的编译**：在执行前，图必须被 `compile()` 编译。编译过程会做完整性检查（如是否有环但无出口、节点是否都定义了等），并生成可执行的运行时对象。编译后的图是不可变的，可以安全地被多次调用。

### 例子

下面用一个"问候机器人"展示完整的概念流转：

```python
from typing import TypedDict
from langgraph.graph import StateGraph, START, END

# 1. 定义 State（共享黑板）
class State(TypedDict):
    name: str
    greeting: str

# 2. 定义 Node（团队成员）
def greet_node(state: State) -> dict:
    """生成问候语"""
    name = state["name"]
    return {"greeting": f"你好，{name}！欢迎使用 LangGraph。"}

# 3. 构建 Graph（组建团队并定义传递关系）
graph_builder = StateGraph(State)
graph_builder.add_node("greet", greet_node)      # 添加节点
graph_builder.add_edge(START, "greet")          # 入口边
graph_builder.add_edge("greet", END)            # 出口边

# 4. 编译并执行
graph = graph_builder.compile()
result = graph.invoke({"name": "小明"})
print(result["greeting"])
# 输出：你好，小明！欢迎使用 LangGraph。
```

概念对应关系图：

```
[START] --入口边--> [greet 节点] --出口边--> [END]
                        |
                   读取 state["name"]
                   写入 state["greeting"]
```

### 总结

- **三大核心**：State（数据）、Node（逻辑）、Edge（流转），三者缺一不可
- **State 是共享的**：所有节点读写同一个状态对象，通过 Reducer 合并更新
- **Node 是纯函数**：接收状态、返回状态更新（不是修改原状态，而是返回 diff）
- **Edge 定义流向**：普通边是固定跳转，条件边根据状态动态选择
- **必须编译**：图在执行前必须 `compile()`，编译后不可变
- **关键认知**：理解 LangGraph 的关键在于理解"状态在节点间流转"这一模型，后续所有高级特性都建立在此基础上

---

## 第3讲：环境搭建与 Hello World

### 概念

本讲我们将搭建完整的 LangGraph 开发环境，并运行第一个真正意义上的程序。环境搭建包括：Python 环境、LangGraph 及相关依赖安装、LLM API Key 配置、开发工具选择。Hello World 程序将展示从状态定义到图执行的完整流程。

### 原理

**环境选择原理**：LangGraph 是纯 Python 库，对环境要求宽松，但推荐使用 Python 3.10+ 以获得最佳类型注解支持。由于 LangGraph 通常需要调用 LLM，建议配合虚拟环境管理依赖，避免与系统其他项目冲突。

**LLM 接入原理**：LangGraph 本身不提供 LLM，需要接入外部模型。最常见的方式是通过 LangChain 的 LLM 封装（如 `ChatOpenAI`、`ChatAnthropic`）。你也可以直接使用 OpenAI SDK 或其他 SDK，但配合 LangChain 能减少样板代码。API Key 通过环境变量传入，避免硬编码。

**项目结构原理**：一个典型的 LangGraph 项目结构如下：

```
my_agent/
├── .env                 # 环境变量（API Key 等）
├── requirements.txt     # 依赖
├── graph.py             # 图定义
├── nodes.py             # 节点函数
├── state.py             # 状态定义
├── tools.py             # 工具定义
└── main.py              # 入口
```

初学阶段可以全部写在一个文件里，随着复杂度增加再拆分。

### 例子

**步骤1：创建虚拟环境并安装依赖**

```bash
# 创建项目目录
mkdir my_langgraph_app && cd my_langgraph_app

# 创建虚拟环境
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 安装核心依赖
pip install langgraph langchain langchain-openai python-dotenv
```

**步骤2：配置环境变量**

创建 `.env` 文件：

```env
OPENAI_API_KEY=sk-your-api-key-here
# 如果用 Anthropic
# ANTHROPIC_API_KEY=sk-ant-your-key
```

**步骤3：编写 Hello World 程序**

创建 `hello_world.py`：

```python
import os
from dotenv import load_dotenv
from typing import TypedDict
from langchain_openai import ChatOpenAI
from langgraph.graph import StateGraph, START, END

# 加载环境变量
load_dotenv()

# 1. 定义状态
class State(TypedDict):
    user_input: str
    response: str

# 2. 初始化 LLM
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)

# 3. 定义节点
def chat_node(state: State) -> dict:
    """调用 LLM 生成回复"""
    user_input = state["user_input"]
    # 调用 LLM
    ai_message = llm.invoke(user_input)
    # 返回状态更新
    return {"response": ai_message.content}

# 4. 构建图
graph_builder = StateGraph(State)
graph_builder.add_node("chat", chat_node)
graph_builder.add_edge(START, "chat")
graph_builder.add_edge("chat", END)

# 5. 编译
graph = graph_builder.compile()

# 6. 执行
if __name__ == "__main__":
    result = graph.invoke({"user_input": "用一句话介绍 LangGraph"})
    print("LLM 回复：", result["response"])
```

**步骤4：运行**

```bash
python hello_world.py
```

输出示例：
```
LLM 回复：LangGraph 是一个用于构建有状态、可循环的 LLM 应用的图编排框架。
```

**步骤5：可视化图结构（可选）**

```python
# 在 Jupyter 中显示图结构
from IPython.display import Image, display

display(Image(graph.get_graph().draw_mermaid_png()))
```

### 总结

- **环境要点**：Python 3.10+、虚拟环境、`pip install langgraph`
- **LLM 接入**：通过 LangChain 的 `ChatOpenAI` 等封装，API Key 放 `.env`
- **开发流程**：定义 State → 定义 Node → 构建 Graph → 编译 → 执行
- **调试技巧**：用 `graph.get_graph().draw_mermaid_png()` 可视化图结构，帮助理解
- **常见问题**：API Key 未加载（检查 `load_dotenv()` 是否调用）、节点未添加到图（检查 `add_node`）、图未编译（检查 `compile()`）
- **进阶建议**：随着项目复杂度增加，将 State、Node、Graph 拆分到不同文件，便于维护

---

## 第4讲：第一个 StateGraph 程序

### 概念

本讲我们构建一个稍微复杂一点的 StateGraph 程序——一个**多步骤分析器**：接收用户问题，先做意图识别，再生成回答，最后做质量检查。这个例子将完整展示多节点、多边、状态流转的协同工作，是理解 LangGraph 工作流的最佳起点。

### 原理

**多节点协作原理**：在真实场景中，一个任务往往需要多个步骤完成。LangGraph 通过将每个步骤封装为独立节点，让职责清晰、易于调试和复用。每个节点只关注自己的输入和输出，通过共享状态协作。

**状态流转的"增量更新"原理**：节点返回的不是完整的新状态，而是**状态的增量更新（diff）**。比如状态有 `question`、`intent`、`answer`、`quality` 四个字段，某个节点只修改 `intent`，它只需返回 `{"intent": "..."}`，其他字段保持不变。这种设计让节点解耦，也便于并行执行（多个节点同时返回不同字段的更新）。

**线性执行原理**：当节点间用普通 `add_edge` 连接时，执行是线性的——A 完成后必然执行 B。LangGraph 内部维护一个调度器，按拓扑顺序执行节点，每个节点完成后合并状态，再执行下一个。

**状态合并的默认策略**：默认情况下，新值覆盖旧值。如果两个字段都需要保留（如对话历史），需要使用 Reducer（下一章详解）。本讲示例字段都是覆盖语义，所以用默认策略即可。

### 例子

```python
import os
from dotenv import load_dotenv
from typing import TypedDict
from langchain_openai import ChatOpenAI
from langgraph.graph import StateGraph, START, END

load_dotenv()
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)

# 1. 定义状态：包含整个流程的所有字段
class AnalysisState(TypedDict):
    question: str       # 用户问题
    intent: str         # 识别出的意图
    answer: str         # 生成的回答
    quality: str        # 质量评估

# 2. 定义三个节点
def intent_node(state: AnalysisState) -> dict:
    """意图识别节点"""
    question = state["question"]
    prompt = f"分析以下问题的意图，用一个词概括（如：咨询/投诉/建议/技术）：\n问题：{question}\n意图："
    response = llm.invoke(prompt)
    return {"intent": response.content.strip()}

def answer_node(state: AnalysisState) -> dict:
    """回答生成节点"""
    question = state["question"]
    intent = state["intent"]
    prompt = f"用户意图是「{intent}」。请针对以下问题给出专业、简洁的回答：\n问题：{question}"
    response = llm.invoke(prompt)
    return {"answer": response.content.strip()}

def quality_node(state: AnalysisState) -> dict:
    """质量检查节点"""
    answer = state["answer"]
    prompt = f"评估以下回答的质量，输出「优秀/良好/需改进」加一句话理由：\n回答：{answer}"
    response = llm.invoke(prompt)
    return {"quality": response.content.strip()}

# 3. 构建图
builder = StateGraph(AnalysisState)
builder.add_node("intent", intent_node)
builder.add_node("answer", answer_node)
builder.add_node("quality", quality_node)

# 4. 连接边：线性流程
builder.add_edge(START, "intent")
builder.add_edge("intent", "answer")
builder.add_edge("answer", "quality")
builder.add_edge("quality", END)

# 5. 编译
graph = builder.compile()

# 6. 执行
if __name__ == "__main__":
    result = graph.invoke({
        "question": "我的订单还没到，已经三天了"
    })
    print("问题：", result["question"])
    print("意图：", result["intent"])
    print("回答：", result["answer"])
    print("质量：", result["quality"])
```

输出示例：
```
问题：我的订单还没到，已经三天了
意图：投诉
回答：很抱歉给您带来不便。建议您提供订单号，我立即为您查询物流状态并联系配送...
质量：良好 - 回答专业且具有行动指引
```

**执行流程图**：
```
[START] → [intent] → [answer] → [quality] → [END]
              ↓          ↓          ↓
          写入intent  写入answer  写入quality
          (读取question) (读取question+intent) (读取answer)
```

**观察状态流转**：可以在每个节点后打印状态，观察增量更新：

```python
# 使用 stream 模式观察每步状态
for event in graph.stream({"question": "怎么退款？"}):
    for node_name, node_output in event.items():
        print(f"[{node_name}] 输出：{node_output}")
```

输出：
```
[intent] 输出：{'intent': '咨询'}
[answer] 输出：{'answer': '退款流程如下...'}
[quality] 输出：{'quality': '良好 - 步骤清晰'}
```

### 总结

- **多节点协作**：将复杂任务拆分为多个节点，每个节点职责单一，通过状态协作
- **增量更新**：节点返回的是 diff，不是完整状态，这让节点解耦且可并行
- **线性流程**：用 `add_edge` 串联节点，执行顺序确定
- **状态字段设计**：State 应包含整个流程需要的所有字段，每个节点只更新自己负责的字段
- **stream 调试**：用 `graph.stream()` 观察每步输出，是调试 LangGraph 的利器
- **关键认知**：本讲示例虽然简单，但已经体现了 LangGraph 的核心工作模式——状态在节点间流转，每个节点读取所需、写入所产。后续所有高级特性都是这一模式的扩展

---

# 第2章 图的构建

本章深入 LangGraph 的三大核心组件——State、Node、Edge。我们将逐一拆解它们的设计原理、使用方法和最佳实践，并通过 Reducer 机制理解状态合并的精妙之处。学完本章，你将能够独立构建任意复杂的图结构。

---

## 第5讲：State 状态详解

### 概念

**State（状态）** 是 LangGraph 图中流转的数据容器，是所有节点共享的"数据黑板"。它定义了图执行过程中需要保存和传递的所有信息。在 LangGraph 中，State 通常用 Python 的 `TypedDict` 或 Pydantic 的 `BaseModel` 来定义，明确每个字段的类型。

State 的设计直接决定了 Agent 的能力边界——一个设计良好的 State 应该包含完成任务所需的全部信息，同时避免冗余。常见的 State 字段包括：对话历史（messages）、用户输入、中间结果、工具调用记录、元数据（如时间戳、用户ID）等。

### 原理

**TypedDict vs Pydantic 原理**：LangGraph 支持两种 State 定义方式。`TypedDict` 是 Python 标准库提供的轻量级类型注解，只做类型提示不做运行时校验，适合快速原型开发。`Pydantic BaseModel` 提供运行时类型校验、默认值、字段约束等强大功能，适合生产环境。两者在 LangGraph 中可以互换使用。

**状态不可变原理**：在 LangGraph 中，节点不应该直接修改传入的 state 对象，而是返回一个**状态更新字典（diff）**。框架内部会根据 Reducer 策略将 diff 合并到全局状态。这种"不可变"设计避免了并发执行时的状态竞争问题，也让调试更清晰（每步的更新都可追溯）。

**状态字段的生命周期**：State 中的字段一旦定义，就在整个图执行期间存在。即使某个节点没有更新某字段，该字段也会保留之前的值。这意味着 State 会"累积"信息——这是设计 Agent 时需要权衡的：信息越多上下文越全，但也会增加 LLM 调用的 token 消耗。

**状态与 Channel 的关系**：在 LangGraph 底层，每个 State 字段对应一个"Channel"。Channel 是状态的存储单元，Reducer 是 Channel 的写入策略。理解这一点有助于后续学习 Checkpoint（持久化整个 Channel 状态）和并行执行（多个节点同时写不同 Channel）。

### 例子

**方式1：使用 TypedDict（推荐入门）**

```python
from typing import TypedDict, Annotated
from langgraph.graph.message import add_messages

class AgentState(TypedDict):
    # 普通字段：默认覆盖语义
    user_input: str
    intermediate_steps: list
    
    # 使用 Annotated 指定 Reducer
    messages: Annotated[list, add_messages]  # 新消息追加而非覆盖
    
    # 可选字段
    final_answer: str
```

**方式2：使用 Pydantic（推荐生产）**

```python
from pydantic import BaseModel, Field
from typing import Annotated
from langgraph.graph.message import add_messages

class AgentState(BaseModel):
    user_input: str = Field(description="用户的原始输入")
    messages: Annotated[list, add_messages] = Field(default_factory=list)
    intermediate_steps: list = Field(default_factory=list)
    final_answer: str = ""
```

**完整示例：一个研究 Agent 的 State 设计**

```python
from typing import TypedDict, Annotated, Optional
from langgraph.graph.message import add_messages

class ResearchState(TypedDict):
    """研究 Agent 的状态定义"""
    # 输入
    query: str                          # 研究问题
    max_sources: int                    # 最大检索源数量
    
    # 对话历史（追加）
    messages: Annotated[list, add_messages]
    
    # 中间结果（追加）
    search_results: Annotated[list, lambda x, y: x + y]  # 自定义 Reducer
    extracted_facts: Annotated[list, lambda x, y: x + y]
    
    # 最终输出（覆盖）
    summary: str
    confidence: float
    sources: list

# 使用
state: ResearchState = {
    "query": "LangGraph 的核心特性",
    "max_sources": 5,
    "messages": [],
    "search_results": [],
    "extracted_facts": [],
    "summary": "",
    "confidence": 0.0,
    "sources": []
}
```

**State 设计检查清单**：
- [ ] 是否包含完成任务所需的全部信息？
- [ ] 是否区分了"覆盖"字段和"追加"字段？
- [ ] 是否避免了大对象（如完整文档）直接放入 State？
- [ ] 是否为可选字段提供了默认值或处理逻辑？

### 总结

- **State 是数据黑板**：所有节点共享，通过 Reducer 合并更新
- **两种定义方式**：TypedDict（轻量、快速）和 Pydantic（强校验、生产级）
- **不可变更新**：节点返回 diff，不直接修改 state，避免并发问题
- **字段累积**：State 在执行期间累积信息，需权衡信息量与 token 消耗
- **Reducer 决定合并**：默认覆盖，可用 `Annotated[type, reducer]` 指定追加等策略
- **设计原则**：包含必要信息、区分覆盖/追加、避免大对象、提供默认值

---

## 第6讲：Node 节点详解

### 概念

**Node（节点）** 是 LangGraph 图中执行具体逻辑的单元。一个节点本质上是一个 Python 函数（或可调用对象），它接收当前 State 作为输入，执行某些操作（如调用 LLM、执行工具、做计算），然后返回 State 的更新（diff）。

节点是 Agent 能力的具体实现——LLM 节点负责推理和生成，工具节点负责执行外部操作，路由节点负责决策下一步走向。设计良好的节点应该职责单一、输入输出清晰、无副作用（或副作用可控）。

### 原理

**节点签名原理**：节点的标准签名是 `def node(state: State) -> dict:`，接收 State 返回 dict。dict 的 key 必须是 State 中已定义的字段，value 是该字段的新值。LangGraph 会根据 Reducer 将这个 dict 合并到全局 State。

**节点类型原理**：根据功能，节点可分为几类：
- **LLM 节点**：调用 LLM 生成回复或做决策
- **工具节点**：执行具体工具（搜索、计算、API 调用）
- **路由节点**：根据状态决定下一步（通常配合条件边）
- **转换节点**：对状态做格式转换或数据处理
- **入口/出口节点**：处理输入输出格式

**节点执行原理**：在 Pregel 模型中，每个"超步"内所有就绪节点并行执行。节点间通过 Channel（State 字段）通信——一个节点写入某 Channel，依赖该 Channel 的节点在下一个超步才能执行。这种数据流驱动模型天然支持并行。

**节点返回值原理**：节点返回的 dict 可以只包含部分字段。如果某字段未在返回值中，该字段保持不变。特殊地，节点可以返回 `Command` 对象（第23讲详解），同时更新状态并指定下一个节点，实现更灵活的控制流。

### 例子

**示例1：基础 LLM 节点**

```python
from langchain_openai import ChatOpenAI
from langchain_core.messages import HumanMessage, AIMessage

llm = ChatOpenAI(model="gpt-4o-mini")

def chat_node(state):
    """简单的 LLM 对话节点"""
    # 读取状态
    messages = state["messages"]
    
    # 调用 LLM
    response = llm.invoke(messages)
    
    # 返回状态更新（只更新 messages 字段）
    return {"messages": [response]}  # 注意是列表，配合 add_messages Reducer
```

**示例2：工具执行节点**

```python
def search_node(state):
    """搜索工具节点"""
    query = state["search_query"]
    
    # 执行搜索（这里用模拟数据）
    results = mock_search_api(query)
    
    # 返回搜索结果
    return {
        "search_results": results,
        "search_count": len(results)
    }
```

**示例3：路由决策节点**

```python
def router_node(state):
    """根据意图路由到不同分支"""
    user_input = state["user_input"]
    
    # 用 LLM 判断意图
    prompt = f"判断以下输入的意图，只输出单词：search/chat/code/translate\n输入：{user_input}"
    intent = llm.invoke(prompt).content.strip().lower()
    
    # 把意图写入状态，供条件边使用
    return {"intent": intent}
```

**示例4：带副作用的节点（需谨慎）**

```python
import logging

logger = logging.getLogger(__name__)

def logging_node(state):
    """记录日志的节点（有副作用但可控）"""
    logger.info(f"当前状态：{state}")
    
    # 可以不返回任何状态更新
    return {}  # 空更新，仅执行副作用
```

**示例5：使用 Pydantic 的节点**

```python
from pydantic import BaseModel

class State(BaseModel):
    count: int = 0
    history: list = []

def increment_node(state: State) -> dict:
    """计数器节点"""
    return {"count": state.count + 1}
```

**节点设计最佳实践**：

```python
# ✅ 好的节点设计：职责单一、输入输出清晰
def extract_keywords_node(state):
    """从文本中提取关键词"""
    text = state["text"]
    prompt = f"从以下文本提取3个关键词，用逗号分隔：\n{text}"
    keywords = llm.invoke(prompt).content
    return {"keywords": [k.strip() for k in keywords.split(",")]}

# ❌ 不好的节点设计：职责混乱、有隐藏副作用
def do_everything_node(state):
    """什么都做的节点"""
    # 调用 LLM
    # 调用工具
    # 修改全局变量（危险！）
    # 写文件（副作用不可控）
    # 返回一堆字段
    pass
```

### 总结

- **节点本质**：接收 State、返回 State 更新的 Python 函数
- **职责单一**：每个节点只做一件事，便于复用和调试
- **返回 diff**：只返回需要更新的字段，未返回的字段保持不变
- **节点分类**：LLM 节点、工具节点、路由节点、转换节点等
- **副作用控制**：节点可以有副作用（如日志、文件操作），但要可控、可追溯
- **命名规范**：节点函数名应清晰表达职责，如 `extract_keywords_node`、`router_node`

---

## 第7讲：Edge 边详解

### 概念

**Edge（边）** 定义了图中节点之间的跳转关系。它回答了"当前节点执行完后，下一个该执行哪个节点"的问题。LangGraph 提供两种边：

1. **普通边（Normal Edge）**：固定的跳转关系，A 完成后必然执行 B
2. **条件边（Conditional Edge）**：根据当前状态动态选择下一个节点

此外还有两个特殊节点：**START**（图的入口）和 **END**（图的出口）。所有图都从 START 开始，到 END 结束。

### 原理

**普通边原理**：用 `add_edge(source, target)` 添加。表示 source 节点执行完后，无条件跳转到 target 节点。这是最简单的连接方式，适合线性流程。普通边形成的是有向图，如果存在环（循环），需要确保有出口，否则图会无限执行。

**条件边原理**：用 `add_conditional_edges(source, path_function, path_map)` 添加。`path_function` 是一个接收 State、返回字符串（目标节点名或键）的函数；`path_map` 是可选的映射，将函数返回值映射到实际节点名。条件边让图具备"决策"能力，是构建智能 Agent 的关键。

**START 和 END 原理**：`START` 和 `END` 是 LangGraph 内置的虚拟节点。`START` 表示图的入口，必须有边从 `START` 出发到第一个真实节点。`END` 表示图的出口，到达 `END` 后图执行结束。一个图可以有多个入口（从 START 到多个节点）和多个出口（多个节点到 END）。

**边的编译原理**：在 `compile()` 时，LangGraph 会检查图的完整性——是否有从 START 可达的所有节点、是否有路径到 END、是否有死循环等。如果发现问题会抛出异常，避免运行时错误。

### 例子

**示例1：普通边（线性流程）**

```python
from langgraph.graph import StateGraph, START, END

builder = StateGraph(dict)
builder.add_node("a", lambda s: {"step": "a done"})
builder.add_node("b", lambda s: {"step": "b done"})
builder.add_node("c", lambda s: {"step": "c done"})

# 线性连接
builder.add_edge(START, "a")
builder.add_edge("a", "b")
builder.add_edge("b", "c")
builder.add_edge("c", END)

graph = builder.compile()
result = graph.invoke({})
print(result)  # {'step': 'c done'}
```

**示例2：条件边（分支流程）**

```python
def route_function(state):
    """根据状态决定下一步"""
    if state.get("score", 0) > 80:
        return "pass"
    else:
        return "fail"

builder.add_conditional_edges(
    "grader",              # 源节点
    route_function,        # 路由函数
    {                      # 路径映射
        "pass": "celebrate_node",
        "fail": "retry_node"
    }
)
```

**示例3：多入口与多出口**

```python
# 多入口：START 同时连接多个节点（会并行执行）
builder.add_edge(START, "fetch_data")
builder.add_edge(START, "fetch_user_info")

# 多出口：多个节点都连到 END
builder.add_edge("success_handler", END)
builder.add_edge("error_handler", END)
```

**示例4：完整示例——带分支的评分系统**

```python
from typing import TypedDict
from langgraph.graph import StateGraph, START, END

class ScoreState(TypedDict):
    answer: str
    score: int
    feedback: str

def grader_node(state):
    """评分节点"""
    answer = state["answer"]
    # 模拟评分
    score = len(answer) * 10  # 简单示例
    return {"score": score}

def celebrate_node(state):
    return {"feedback": f"优秀！得分 {state['score']}"}

def retry_node(state):
    return {"feedback": f"需改进，得分 {state['score']}，请重试"}

def route_by_score(state):
    return "pass" if state["score"] >= 80 else "fail"

builder = StateGraph(ScoreState)
builder.add_node("grader", grader_node)
builder.add_node("celebrate", celebrate_node)
builder.add_node("retry", retry_node)

builder.add_edge(START, "grader")
builder.add_conditional_edges(
    "grader",
    route_by_score,
    {"pass": "celebrate", "fail": "retry"}
)
builder.add_edge("celebrate", END)
builder.add_edge("retry", END)

graph = builder.compile()

# 测试
result1 = graph.invoke({"answer": "这是一个非常详细的回答" * 5})
print(result1["feedback"])  # 优秀！得分 ...

result2 = graph.invoke({"answer": "短"})
print(result2["feedback"])  # 需改进...
```

**图结构可视化**：
```
[START] → [grader] → (score >= 80?) → [celebrate] → [END]
                       ↓ 否
                    [retry] → [END]
```

### 总结

- **两种边**：普通边（固定跳转）和条件边（动态选择）
- **START/END**：虚拟节点，标记图的入口和出口
- **条件边三要素**：源节点、路由函数、路径映射
- **多入口/多出口**：一个图可以有多个起点和终点，支持并行入口
- **编译检查**：`compile()` 会检查图完整性，避免运行时错误
- **避免死循环**：有环图必须有出口条件，否则会无限执行

---

## 第8讲：Conditional Edge 条件边

### 概念

**Conditional Edge（条件边）** 是 LangGraph 中实现"智能决策"的核心机制。它允许节点执行后，根据当前状态动态选择下一个要执行的节点。条件边是构建 Agent 循环（如 ReAct 模式）、多分支工作流、智能路由的基础。

本讲在第7讲基础上，深入条件边的进阶用法，包括：复杂路由逻辑、动态目标、与 LLM 结合做意图路由等。

### 原理

**条件边的工作原理**：当源节点执行完毕，LangGraph 调用路由函数（path_function），传入当前 State。函数返回一个字符串（"路由键"），LangGraph 根据路径映射（path_map）找到实际的目标节点。如果路径映射中找不到，会抛出异常。

**路由函数的纯函数性**：路由函数应该是纯函数——只读 State、不修改 State、无副作用。它只负责"判断"，不负责"执行"。如果需要在路由时修改状态，应该在源节点中完成，而不是在路由函数中。

**路径映射的两种形式**：
1. **显式映射**：`{"key1": "node1", "key2": "node2"}`，路由函数返回 key，映射到 node
2. **隐式映射**：路由函数直接返回节点名，此时 path_map 可省略（但建议显式提供，便于维护）

**与 LLM 结合的原理**：路由函数可以调用 LLM 做意图识别、分类决策。这种"LLM 路由"是 Agent 智能的来源——不是用 if-else 硬编码规则，而是让 LLM 根据语义动态决策。但要注意 LLM 输出的不确定性，建议加 fallback。

**条件边与循环**：条件边可以指向之前的节点，形成循环。这是 ReAct Agent 的核心——"判断是否需要工具→调用工具→判断是否完成→回到 LLM"。循环必须有终止条件（如最大迭代次数、状态标志），否则会死循环。

### 例子

**示例1：基础条件边——意图路由**

```python
from typing import TypedDict
from langgraph.graph import StateGraph, START, END
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)

class ChatState(TypedDict):
    user_input: str
    intent: str
    response: str

def intent_classifier(state):
    """用 LLM 识别意图"""
    prompt = f"""判断用户输入的意图，只输出以下之一：search/chat/code
用户输入：{state['user_input']}
意图："""
    intent = llm.invoke(prompt).content.strip().lower()
    return {"intent": intent}

def search_handler(state):
    return {"response": f"搜索中：{state['user_input']}"}

def chat_handler(state):
    response = llm.invoke(state["user_input"])
    return {"response": response.content}

def code_handler(state):
    return {"response": "代码助手模式"}

def route_by_intent(state):
    """路由函数"""
    intent = state.get("intent", "chat")
    return intent

builder = StateGraph(ChatState)
builder.add_node("classifier", intent_classifier)
builder.add_node("search", search_handler)
builder.add_node("chat", chat_handler)
builder.add_node("code", code_handler)

builder.add_edge(START, "classifier")
builder.add_conditional_edges(
    "classifier",
    route_by_intent,
    {
        "search": "search",
        "chat": "chat",
        "code": "code"
    }
)
# 所有处理节点都到 END
for node in ["search", "chat", "code"]:
    builder.add_edge(node, END)

graph = builder.compile()

# 测试
print(graph.invoke({"user_input": "今天天气怎么样"})["response"])
print(graph.invoke({"user_input": "写个排序算法"})["response"])
```

**示例2：循环条件边——ReAct 雏形**

```python
class AgentState(TypedDict):
    messages: list
    tool_calls: list
    iteration: int

def agent_node(state):
    """LLM 决策节点"""
    iteration = state.get("iteration", 0) + 1
    # 模拟 LLM 决策
    if iteration >= 3:
        return {"messages": ["最终回答"], "iteration": iteration}
    return {"messages": ["需要调用工具"], "tool_calls": ["search"], "iteration": iteration}

def tool_node(state):
    """工具执行节点"""
    return {"messages": ["工具结果"], "tool_calls": []}

def should_continue(state):
    """判断是否继续循环"""
    if state.get("tool_calls"):
        return "tools"
    return END

builder = StateGraph(AgentState)
builder.add_node("agent", agent_node)
builder.add_node("tools", tool_node)

builder.add_edge(START, "agent")
builder.add_conditional_edges("agent", should_continue, {"tools": "tools", END: END})
builder.add_edge("tools", "agent")  # 工具执行后回到 agent，形成循环

graph = builder.compile()
result = graph.invoke({"messages": [], "tool_calls": [], "iteration": 0})
print(result)
```

**示例3：动态目标——不使用 path_map**

```python
def dynamic_router(state):
    """直接返回节点名（不推荐，但可行）"""
    if state["step"] == 1:
        return "node_a"
    elif state["step"] == 2:
        return "node_b"
    return "node_c"

# 不传 path_map，路由函数返回值直接作为节点名
builder.add_conditional_edges("source", dynamic_router)
```

**示例4：复杂路由——多条件组合**

```python
def complex_router(state):
    """多条件组合路由"""
    intent = state.get("intent")
    confidence = state.get("confidence", 0)
    
    if confidence < 0.5:
        return "clarify"  # 置信度低，请求澄清
    if intent == "search":
        return "search"
    elif intent == "chat" and state.get("user_vip"):
        return "vip_chat"  # VIP 用户走专属处理
    elif intent == "chat":
        return "normal_chat"
    return "fallback"

builder.add_conditional_edges(
    "router",
    complex_router,
    {
        "clarify": "clarify_node",
        "search": "search_node",
        "vip_chat": "vip_chat_node",
        "normal_chat": "normal_chat_node",
        "fallback": "fallback_node"
    }
)
```

### 总结

- **条件边三要素**：源节点、路由函数、路径映射
- **路由函数纯函数**：只读 State、返回路由键，不修改状态
- **LLM 路由**：可调用 LLM 做意图识别，是 Agent 智能的来源，但需 fallback
- **循环条件边**：条件边可指向前序节点形成循环，必须有终止条件
- **路径映射**：建议显式提供，便于维护和阅读
- **多条件组合**：路由函数可实现复杂决策逻辑，组合多个状态字段

---

## 第9讲：Reducer 与状态合并

### 概念

**Reducer（归约器）** 是 LangGraph 中决定"节点返回的状态更新如何合并到全局状态"的策略函数。它回答了"当节点返回 `{"messages": [new_msg]}` 时，全局 state 的 `messages` 字段应该怎么更新"的问题。

默认 Reducer 是"覆盖"——新值直接替换旧值。但很多场景需要"追加"（如对话历史）、"合并"（如字典合并）、"累加"（如计数器）等策略。LangGraph 通过 `Annotated` 类型注解，让每个字段可以指定自己的 Reducer。

### 原理

**Reducer 函数签名**：Reducer 是一个二元函数 `reducer(old_value, new_value) -> merged_value`。当节点返回 `{"field": new_value}` 时，LangGraph 调用 `reducer(current_field_value, new_value)` 得到合并后的值，写入全局 State。

**默认 Reducer（覆盖）原理**：如果不指定 Reducer，字段使用默认策略——直接用新值替换旧值。这适合大多数"最新值有效"的字段，如 `current_answer`、`step`、`status` 等。

**追加 Reducer 原理**：对话历史 `messages` 是典型的"追加"场景——每轮对话产生新消息，应该追加到列表而非覆盖。LangGraph 提供 `add_messages` Reducer，它智能处理消息追加：相同 ID 的消息会更新而非重复添加，不同 ID 的消息追加。

**自定义 Reducer 原理**：你可以定义任意 Reducer 函数。常见模式：
- 列表追加：`lambda x, y: x + y`
- 字典合并：`lambda x, y: {**x, **y}`
- 计数累加：`lambda x, y: x + y`（数值）
- 取最大值：`lambda x, y: max(x, y)`
- 自定义业务逻辑

**Annotated 注解原理**：Python 的 `Annotated` 类型允许在类型注解中附加元数据。LangGraph 利用这一机制，通过 `Annotated[Type, reducer]` 将 Reducer 绑定到字段。这是类型安全的、IDE 友好的方式。

**并行执行与 Reducer**：当多个节点并行执行并返回同一字段的更新时，Reducer 决定如何合并这些更新。比如两个节点都返回 `{"count": 1}`，如果 Reducer 是加法，最终 count 增加 2；如果是覆盖，最终是 1（不确定哪个）。这就是为什么并行更新同一字段必须用 Reducer。

### 例子

**示例1：默认 Reducer（覆盖）**

```python
from typing import TypedDict
from langgraph.graph import StateGraph, START, END

class State(TypedDict):
    current_step: str  # 默认覆盖

def step1(state):
    return {"current_step": "step1 done"}

def step2(state):
    return {"current_step": "step2 done"}  # 覆盖 step1 的值

builder = StateGraph(State)
builder.add_node("s1", step1)
builder.add_node("s2", step2)
builder.add_edge(START, "s1")
builder.add_edge("s1", "s2")
builder.add_edge("s2", END)

graph = builder.compile()
result = graph.invoke({"current_step": ""})
print(result["current_step"])  # step2 done
```

**示例2：add_messages Reducer（追加）**

```python
from typing import TypedDict, Annotated
from langgraph.graph import StateGraph, START, END
from langgraph.graph.message import add_messages
from langchain_core.messages import HumanMessage, AIMessage

class ChatState(TypedDict):
    messages: Annotated[list, add_messages]  # 追加语义

def user_node(state):
    return {"messages": [HumanMessage(content="你好")]}  # 追加用户消息

def ai_node(state):
    return {"messages": [AIMessage(content="你好！有什么可以帮你？")]}  # 追加 AI 消息

builder = StateGraph(ChatState)
builder.add_node("user", user_node)
builder.add_node("ai", ai_node)
builder.add_edge(START, "user")
builder.add_edge("user", "ai")
builder.add_edge("ai", END)

graph = builder.compile()
result = graph.invoke({"messages": []})
print(f"消息数量：{len(result['messages'])}")  # 2
for msg in result["messages"]:
    print(f"{msg.type}: {msg.content}")
```

**示例3：自定义 Reducer**

```python
from typing import TypedDict, Annotated
from operator import add

class State(TypedDict):
    # 列表追加
    search_results: Annotated[list, add]  # operator.add 等价于 lambda x, y: x + y
    
    # 计数累加
    visit_count: Annotated[int, add]  # 整数 add 就是数学加法
    
    # 字典合并
    metadata: Annotated[dict, lambda x, y: {**x, **y}]
    
    # 取最大值
    max_score: Annotated[int, lambda x, y: max(x, y)]

def search_node(state):
    return {
        "search_results": ["结果1", "结果2"],
        "visit_count": 1,
        "metadata": {"source": "google"},
        "max_score": 85
    }

def another_node(state):
    return {
        "search_results": ["结果3"],
        "visit_count": 1,
        "metadata": {"time": "2024-01-01"},
        "max_score": 92
    }

builder = StateGraph(State)
builder.add_node("s1", search_node)
builder.add_node("s2", another_node)
builder.add_edge(START, "s1")
builder.add_edge("s1", "s2")
builder.add_edge("s2", END)

graph = builder.compile()
result = graph.invoke({
    "search_results": [],
    "visit_count": 0,
    "metadata": {},
    "max_score": 0
})
print(result)
# {
#   'search_results': ['结果1', '结果2', '结果3'],
#   'visit_count': 2,
#   'metadata': {'source': 'google', 'time': '2024-01-01'},
#   'max_score': 92
# }
```

**示例4：复杂 Reducer——带去重的列表追加**

```python
def dedup_add(old_list, new_items):
    """带去重的列表追加"""
    result = list(old_list)
    for item in new_items:
        if item not in result:
            result.append(item)
    return result

class State(TypedDict):
    unique_tags: Annotated[list, dedup_add]

def tag_node(state):
    return {"unique_tags": ["python", "ai", "python"]}  # 重复的 python

builder = StateGraph(State)
builder.add_node("tagger", tag_node)
builder.add_edge(START, "tagger")
builder.add_edge("tagger", END)

graph = builder.compile()
result = graph.invoke({"unique_tags": ["langgraph"]})
print(result["unique_tags"])  # ['langgraph', 'python', 'ai']  去重了
```

### 总结

- **Reducer 决定合并**：每个字段可有独立 Reducer，决定如何合并节点返回的更新
- **默认覆盖**：不指定 Reducer 时，新值替换旧值
- **add_messages**：LangGraph 内置的消息追加 Reducer，智能处理 ID
- **自定义 Reducer**：用 `Annotated[Type, reducer_func]` 绑定，可实现任意合并逻辑
- **并行必需**：并行节点更新同一字段时，必须用 Reducer 避免不确定结果
- **常见模式**：列表追加（`operator.add`）、字典合并、计数累加、取最大值、去重追加

---

# 第3章 消息与对话

对话是 Agent 最常见的交互形式。本章深入 LangGraph 的消息体系，包括消息类型、MessagesState、对话历史管理，最终构建一个完整的多轮对话 Agent。学完本章，你将能够构建具备上下文记忆的对话机器人。

---

## 第10讲：Messages 消息体系

### 概念

**Messages（消息）** 是 LangGraph 对话系统的基本数据单元。每条消息代表对话中的一个"发言"——可以是用户的话、AI 的回复、系统的提示、工具的返回结果等。LangGraph 复用 LangChain 的消息体系，提供四种核心消息类型：HumanMessage、AIMessage、SystemMessage、ToolMessage。

理解消息体系是构建对话 Agent 的基础——LLM 的输入是一组消息，输出也是消息；对话历史本质上是消息列表；工具调用的请求和结果也通过消息传递。

### 原理

**四种消息类型原理**：

1. **SystemMessage**：系统指令，设定 AI 的角色、行为规范、约束条件。通常放在对话开头，对整个对话生效。例如"你是一个专业的客服助手，回答要简洁有礼"。

2. **HumanMessage**：用户消息，代表用户的输入。可以包含纯文本，也可以包含多模态内容（图片、文件等）。

3. **AIMessage**：AI 回复，代表 LLM 的输出。除了 `content`（文本内容），还可能包含 `tool_calls`（工具调用请求）——当 LLM 决定调用工具时，会在 AIMessage 中携带工具调用信息，而不是直接输出文本。

4. **ToolMessage**：工具返回结果，是对 AIMessage 中 tool_calls 的响应。包含 `tool_call_id`（关联到具体的工具调用）和 `content`（工具执行结果）。

**消息顺序与角色原理**：LLM 期望消息按时间顺序排列，角色交替（user → assistant → user → assistant）。SystemMessage 通常只在开头出现一次。ToolMessage 必须紧跟在对应的 AIMessage（含 tool_calls）之后。违反这些规则可能导致 LLM 报错或行为异常。

**消息内容原理**：消息的 `content` 可以是字符串，也可以是列表（多模态）。例如：
```python
HumanMessage(content=[
    {"type": "text", "text": "这张图是什么？"},
    {"type": "image_url", "image_url": {"url": "..."}}
])
```

**消息 ID 原理**：每条消息可以有唯一 ID。LangGraph 的 `add_messages` Reducer 利用 ID 实现智能合并——相同 ID 的消息会被更新（而非重复添加），不同 ID 的消息追加。这在修改历史消息、流式更新等场景非常有用。

### 例子

**示例1：四种消息类型**

```python
from langchain_core.messages import (
    SystemMessage, HumanMessage, AIMessage, ToolMessage
)

# 系统消息：设定角色
system_msg = SystemMessage(content="你是一个 Python 编程助手，回答简洁专业。")

# 用户消息
human_msg = HumanMessage(content="怎么反转字符串？")

# AI 消息（含工具调用）
ai_msg = AIMessage(
    content="",
    tool_calls=[{
        "id": "call_123",
        "name": "python_executor",
        "args": {"code": "'hello'[::-1]"}
    }]
)

# 工具消息：返回工具执行结果
tool_msg = ToolMessage(
    content="olleh",
    tool_call_id="call_123"  # 关联到上面的工具调用
)

# 最终 AI 回复
final_ai_msg = AIMessage(content="字符串反转的结果是 'olleh'。")
```

**示例2：构建完整对话历史**

```python
conversation = [
    SystemMessage(content="你是一个友好的助手。"),
    HumanMessage(content="你好！"),
    AIMessage(content="你好！有什么可以帮你？"),
    HumanMessage(content="今天天气怎么样？"),
    AIMessage(content="我无法获取实时天气，但可以陪你聊天。"),
]

# 传给 LLM
from langchain_openai import ChatOpenAI
llm = ChatOpenAI(model="gpt-4o-mini")
response = llm.invoke(conversation)
print(response.content)
```

**示例3：多模态消息**

```python
# 文本 + 图片
msg = HumanMessage(content=[
    {"type": "text", "text": "描述这张图片"},
    {
        "type": "image_url",
        "image_url": {"url": "https://example.com/image.jpg"}
    }
])

# 传给支持视觉的 LLM
from langchain_openai import ChatOpenAI
vlm = ChatOpenAI(model="gpt-4o")  # 支持视觉
response = vlm.invoke([msg])
```

**示例4：消息工具调用流程**

```python
from langchain_core.tools import tool

@tool
def search(query: str) -> str:
    """搜索工具"""
    return f"搜索结果：{query} 的相关信息..."

# 给 LLM 绑定工具
llm_with_tools = llm.bind_tools([search])

# 第一轮：LLM 决定调用工具
human_msg = HumanMessage(content="帮我搜索 LangGraph")
ai_response = llm_with_tools.invoke([human_msg])

print("AI 决定调用工具：", ai_response.tool_calls)
# [{'id': 'call_xxx', 'name': 'search', 'args': {'query': 'LangGraph'}}]

# 执行工具
tool_result = search.invoke(ai_response.tool_calls[0]["args"])

# 构造工具消息
tool_msg = ToolMessage(
    content=tool_result,
    tool_call_id=ai_response.tool_calls[0]["id"]
)

# 第二轮：把工具结果传回 LLM，让它生成最终回复
final_messages = [human_msg, ai_response, tool_msg]
final_response = llm.invoke(final_messages)
print("最终回复：", final_response.content)
```

### 总结

- **四种消息**：SystemMessage（角色）、HumanMessage（用户）、AIMessage（AI，可含 tool_calls）、ToolMessage（工具结果）
- **顺序规则**：SystemMessage 在开头，user/assistant 交替，ToolMessage 紧跟对应 AIMessage
- **多模态**：content 可以是字符串或列表，支持图片等
- **消息 ID**：用于智能合并，相同 ID 更新而非重复
- **工具调用流程**：HumanMessage → AIMessage(含 tool_calls) → ToolMessage → AIMessage(最终回复)
- **核心认知**：对话本质是消息列表，理解消息体系就理解了对话的数据基础

---

## 第11讲：MessagesState 与对话历史

### 概念

**MessagesState** 是 LangGraph 提供的一个预置 State 类，专门用于对话场景。它内置了 `messages` 字段并配置了 `add_messages` Reducer，省去了手动定义的麻烦。使用 MessagesState 可以快速搭建对话 Agent，无需重复编写样板代码。

**对话历史（Conversation History）** 是指 Agent 在多轮对话中累积的消息列表。它让 Agent 具备"记忆"——能理解上下文，知道用户之前说过什么、自己之前回答过什么。对话历史是构建多轮对话 Agent 的核心。

### 原理

**MessagesState 原理**：MessagesState 本质上是一个预定义的 TypedDict：

```python
class MessagesState(TypedDict):
    messages: Annotated[list, add_messages]
```

它只包含一个 `messages` 字段，使用 `add_messages` Reducer（追加语义）。你可以直接继承它来扩展自己的 State，添加业务字段。

**对话历史累积原理**：在多轮对话中，每轮产生的新消息（用户输入、AI 回复、工具调用、工具结果）都追加到 `messages` 列表。下一轮调用 LLM 时，传入完整的 `messages`，LLM 就能"看到"之前的对话上下文。这种"累积+传入"机制是对话记忆的基础。

**历史长度控制原理**：随着对话轮数增加，`messages` 列表会越来越长，导致 LLM 调用的 token 消耗线性增长。常见控制策略：
1. **窗口策略**：只保留最近 N 条消息
2. **摘要策略**：定期把旧消息摘要成一条 SystemMessage
3. **token 限制策略**：根据 token 数动态裁剪
4. **重要消息保留**：SystemMessage 等关键消息始终保留

**消息裁剪原理**：裁剪时要注意消息的完整性——不能把 AIMessage(含 tool_calls) 和对应的 ToolMessage 拆开，否则 LLM 会报错。LangChain 提供 `trim_messages` 工具，支持智能裁剪。

### 例子

**示例1：使用 MessagesState**

```python
from typing import Annotated
from typing_extensions import TypedDict
from langgraph.graph import StateGraph, START, END
from langgraph.graph.message import MessagesState, add_messages
from langchain_openai import ChatOpenAI
from langchain_core.messages import HumanMessage

llm = ChatOpenAI(model="gpt-4o-mini")

# 直接使用 MessagesState
def chatbot(state: MessagesState):
    response = llm.invoke(state["messages"])
    return {"messages": [response]}  # 追加 AI 回复

builder = StateGraph(MessagesState)
builder.add_node("chatbot", chatbot)
builder.add_edge(START, "chatbot")
builder.add_edge("chatbot", END)

graph = builder.compile()

# 第一轮
result = graph.invoke({"messages": [HumanMessage(content="你好")]})
print(result["messages"][-1].content)

# 第二轮（带历史）
result = graph.invoke({
    "messages": result["messages"] + [HumanMessage(content="我刚才说了什么？")]
})
print(result["messages"][-1].content)
# AI 能回忆起"你好"，因为有历史
```

**示例2：扩展 MessagesState 添加业务字段**

```python
from langgraph.graph.message import MessagesState

# 继承 MessagesState 添加字段
class MyAgentState(MessagesState):
    # messages 字段已自动包含
    user_id: str
    conversation_stage: str
    extracted_info: dict

def extract_info_node(state: MyAgentState):
    # 读取对话历史
    last_msg = state["messages"][-1].content
    # 提取信息
    info = {"last_query": last_msg}
    return {"extracted_info": info}

def chat_node(state: MyAgentState):
    # 把业务信息加入 system prompt
    system_msg = SystemMessage(content=f"用户ID：{state['user_id']}，阶段：{state['conversation_stage']}")
    messages = [system_msg] + state["messages"]
    response = llm.invoke(messages)
    return {"messages": [response]}
```

**示例3：对话历史裁剪**

```python
from langchain_core.messages import trim_messages

# 裁剪策略：保留最近 1000 token 的消息
trimmer = trim_messages(
    max_tokens=1000,
    strategy="last",
    token_counter=llm,
    include_system=True,  # 始终保留 SystemMessage
    start_on="human",     # 从 human 消息开始
    allow_partial=False   # 不允许截断单条消息
)

def chatbot_with_trim(state: MessagesState):
    # 裁剪历史
    trimmed = trimmer.invoke(state["messages"])
    # 调用 LLM
    response = llm.invoke(trimmed)
    return {"messages": [response]}
```

**示例4：摘要式历史管理**

```python
def summarize_if_needed(state: MessagesState):
    """当历史过长时，摘要旧消息"""
    messages = state["messages"]
    
    # 超过 20 条消息时触发摘要
    if len(messages) > 20:
        # 取旧消息（保留最近 10 条）
        old_messages = messages[:-10]
        recent_messages = messages[-10:]
        
        # 让 LLM 摘要旧消息
        summary_prompt = HumanMessage(
            content=f"请摘要以下对话的要点：\n{format_messages(old_messages)}"
        )
        summary = llm.invoke([summary_prompt])
        
        # 用摘要替换旧消息
        summary_msg = SystemMessage(content=f"之前对话摘要：{summary.content}")
        return {"messages": [summary_msg] + recent_messages}
    
    return {}  # 不需要摘要
```

### 总结

- **MessagesState**：预置 State，含 `messages` 字段和 `add_messages` Reducer
- **可扩展**：继承 MessagesState 添加业务字段
- **历史累积**：每轮新消息追加，下轮传入完整历史实现记忆
- **长度控制**：窗口、摘要、token 限制三种策略
- **裁剪注意**：不能拆开 AIMessage(tool_calls) 和 ToolMessage
- **trim_messages**：LangChain 提供的智能裁剪工具

---

## 第12讲：多轮对话 Agent 构建

### 概念

本讲综合运用前两讲的知识，构建一个完整的多轮对话 Agent。这个 Agent 具备：上下文记忆、系统人设、历史管理、循环对话能力。我们将从最简单的版本开始，逐步增加功能，最终得到一个生产可用的对话 Agent 雏形。

### 原理

**多轮对话 Agent 的核心原理**：每轮对话中，Agent 接收用户输入，将其作为 HumanMessage 追加到 `messages`，然后调用 LLM 生成回复，将回复作为 AIMessage 追加到 `messages`。下一轮对话时，传入完整的 `messages` 列表，LLM 就能基于历史上下文生成连贯的回复。

**循环对话的实现原理**：在 LangGraph 中，多轮对话通过"外部循环"实现——每次 `invoke` 是一轮对话，调用方负责把上一轮的 `messages` 传入下一轮。这与"内部循环"（如图内的 ReAct 循环）不同——外部循环是用户驱动的，每轮等待用户输入。

**人设保持原理**：通过 SystemMessage 设定 Agent 的角色、语气、行为规范。SystemMessage 通常放在 `messages` 列表开头，每轮对话都保留。这样 LLM 在每轮回复时都会"记住"自己的人设。

**状态持久化原理**：默认情况下，LangGraph 的状态在内存中，进程结束就丢失。要实现跨会话记忆，需要使用 Checkpointer（第27讲详解）。本讲先用内存状态演示多轮对话，后续章节会讲解持久化。

### 例子

**完整示例：多轮对话 Agent**

```python
import os
from dotenv import load_dotenv
from typing import Annotated
from typing_extensions import TypedDict
from langchain_openai import ChatOpenAI
from langchain_core.messages import SystemMessage, HumanMessage, AIMessage
from langgraph.graph import StateGraph, START, END
from langgraph.graph.message import MessagesState, add_messages
from langchain_core.messages import trim_messages

load_dotenv()

# 1. 初始化 LLM
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0.7)

# 2. 定义系统人设
SYSTEM_PROMPT = """你是「小智」，一个友好、专业的 AI 助手。

你的特点：
- 回答简洁明了，避免冗长
- 善于用类比解释复杂概念
- 不确定时坦诚告知，不编造信息
- 适时引导用户深入思考

请始终保持这个人设进行对话。"""

# 3. 定义 State（继承 MessagesState）
class ChatAgentState(MessagesState):
    # messages 已自动包含
    user_name: str  # 用户名（业务字段）

# 4. 定义节点
def chat_node(state: ChatAgentState):
    """对话节点：调用 LLM 生成回复"""
    # 构造消息列表：系统人设 + 对话历史
    messages = [SystemMessage(content=SYSTEM_PROMPT)] + state["messages"]
    
    # 如果知道用户名，加入系统消息
    if state.get("user_name"):
        messages[0] = SystemMessage(
            content=SYSTEM_PROMPT + f"\n\n当前对话用户：{state['user_name']}"
        )
    
    # 裁剪历史（保留最近 20 条）
    if len(state["messages"]) > 20:
        recent = state["messages"][-20:]
        messages = [messages[0]] + recent
    
    # 调用 LLM
    response = llm.invoke(messages)
    
    # 追加 AI 回复
    return {"messages": [response]}

# 5. 构建图
builder = StateGraph(ChatAgentState)
builder.add_node("chat", chat_node)
builder.add_edge(START, "chat")
builder.add_edge("chat", END)

graph = builder.compile()

# 6. 多轮对话循环
def chat_loop():
    """交互式多轮对话"""
    print("🤖 小智已上线，输入 'quit' 退出\n")
    
    # 对话历史（跨轮保留）
    messages = []
    user_name = input("请告诉我你的名字：") or "朋友"
    
    while True:
        user_input = input("\n你：")
        if user_input.lower() in ["quit", "exit", "退出"]:
            print("小智：再见！")
            break
        
        # 追加用户消息
        messages.append(HumanMessage(content=user_input))
        
        # 调用图（传入完整历史）
        result = graph.invoke({
            "messages": messages,
            "user_name": user_name
        })
        
        # 获取 AI 回复
        ai_reply = result["messages"][-1]
        print(f"\n小智：{ai_reply.content}")
        
        # 更新历史（包含 AI 回复）
        messages = result["messages"]

if __name__ == "__main__":
    chat_loop()
```

**运行示例**：
```
🤖 小智已上线，输入 'quit' 退出

请告诉我你的名字：小明

你：你好

小智：你好小明！很高兴认识你。有什么想聊的吗？

你：我刚才说我叫什么？

小智：你叫小明呀，我记着呢。有什么我可以帮你的？

你：用一句话解释什么是递归

小智：递归就像俄罗斯套娃——一个函数调用自己，每次缩小问题，直到遇到最小的那个"基座"才停止。

你：quit
小智：再见！
```

**进阶：带历史摘要的版本**

```python
def chat_node_with_summary(state: ChatAgentState):
    messages = state["messages"]
    
    # 历史过长时摘要
    if len(messages) > 30:
        # 保留 SystemMessage + 最近 10 条
        old_messages = messages[:-10]
        recent = messages[-10:]
        
        # 摘要旧消息
        summary_prompt = f"请用 200 字内摘要以下对话的要点：\n{format_msgs(old_messages)}"
        summary = llm.invoke(summary_prompt).content
        
        # 用摘要替换旧消息
        summary_msg = SystemMessage(content=f"【之前对话摘要】\n{summary}")
        messages = [summary_msg] + recent
    
    # 调用 LLM
    full_messages = [SystemMessage(content=SYSTEM_PROMPT)] + messages
    response = llm.invoke(full_messages)
    return {"messages": [response]}

def format_msgs(msgs):
    return "\n".join([
        f"{m.type}: {m.content}" for m in msgs
    ])
```

### 总结

- **多轮对话核心**：每轮追加消息、传入完整历史、LLM 基于历史生成回复
- **外部循环**：多轮对话通过"外部循环"实现，每轮一次 `invoke`
- **人设保持**：SystemMessage 设定角色，每轮都传入
- **历史管理**：长对话需要裁剪或摘要，控制 token 消耗
- **状态保留**：调用方负责保留 `messages` 列表，跨轮传入
- **生产化要点**：真实场景需要持久化（Checkpoint）、并发控制、错误处理，后续章节详解

---

# 第4章 工具调用

工具调用是 Agent 区别于普通聊天机器人的核心能力。通过工具，Agent 可以查询数据库、调用 API、执行计算、操作文件，从而与真实世界交互。本章将深入 LangGraph 的工具调用机制，包括工具定义、ToolNode、完整调用流程。学完本章，你将能够构建具备工具使用能力的 Agent。

---

## 第13讲：工具定义与绑定

### 概念

**工具（Tool）** 是 Agent 调用外部能力的接口。在 LangGraph 生态中，工具本质上是一个有清晰签名（名称、描述、参数 schema）的可调用函数。LLM 通过阅读工具的描述和参数 schema，决定何时调用、如何调用工具。

**工具绑定（Tool Binding）** 是将工具列表关联到 LLM 的过程。绑定后，LLM 在生成回复时可以"决定"调用某个工具，输出中会包含 `tool_calls` 字段，指明要调用的工具名和参数。LangGraph/LangChain 通过 `llm.bind_tools(tools)` 实现绑定。

### 原理

**工具的本质原理**：一个工具由三部分组成：
1. **函数本身**：实际执行的 Python 函数
2. **名称**：LLM 用来引用工具的标识符
3. **描述**：告诉 LLM 这个工具做什么、何时用
4. **参数 schema**：JSON Schema 描述工具的参数类型和结构

LLM 在对话时，会看到所有可用工具的描述和 schema，然后决定是否调用、调用哪个、传什么参数。

**@tool 装饰器原理**：LangChain 提供 `@tool` 装饰器，自动从函数的 docstring 和类型注解生成工具的描述和参数 schema。这是最便捷的工具定义方式。装饰器会读取：
- 函数名 → 工具名
- docstring → 工具描述
- 参数类型注解 → 参数 schema
- 返回类型注解 → 输出类型

**bind_tools 原理**：`llm.bind_tools(tools)` 返回一个新的 LLM 实例（Runnable），该实例在调用 LLM API 时会传入工具信息。LLM API（如 OpenAI 的 Function Calling）根据这些信息决定是否在响应中包含 `tool_calls`。注意：bind_tools 只是"告诉 LLM 有这些工具"，实际执行工具需要另外处理。

**LLM 工具决策原理**：当 LLM 收到带工具的请求时，它会：
1. 阅读用户输入和工具描述
2. 判断是否需要调用工具来回答
3. 如果需要，输出 `tool_calls`（工具名 + 参数），`content` 可能为空
4. 如果不需要，直接输出文本回复

这种"决策"能力是 Agent 智能的来源——LLM 根据语义选择工具，而非硬编码规则。

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
    # 模拟天气查询
    weather_data = {"北京": "晴 25°C", "上海": "多云 28°C"}
    return weather_data.get(city, f"未找到 {city} 的天气信息")

@tool
def calculate(expression: str) -> str:
    """计算数学表达式。
    
    Args:
        expression: 数学表达式，如 "1+2*3"
    
    Returns:
        计算结果
    """
    try:
        result = eval(expression)  # 注意：生产环境不要用 eval
        return str(result)
    except Exception as e:
        return f"计算错误：{e}"

# 查看工具信息
print(search_weather.name)        # search_weather
print(search_weather.description)  # 查询指定城市的天气...
print(search_weather.args)        # {'city': {'type': 'string', ...}}
```

**示例2：绑定工具到 LLM**

```python
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4o-mini")

# 绑定工具
llm_with_tools = llm.bind_tools([search_weather, calculate])

# 测试：LLM 决定调用工具
response = llm_with_tools.invoke("北京今天天气怎么样？")
print("工具调用：", response.tool_calls)
# [{'name': 'search_weather', 'args': {'city': '北京'}, 'id': 'call_xxx'}]
print("文本内容：", response.content)  # 通常为空

# 测试：LLM 不调用工具
response2 = llm_with_tools.invoke("你好")
print("工具调用：", response2.tool_calls)  # []
print("文本内容：", response2.content)  # 你好！有什么可以帮你？
```

**示例3：自定义工具 schema（高级）**

```python
from langchain_core.tools import StructuredTool
from pydantic import BaseModel, Field

class SearchInput(BaseModel):
    query: str = Field(description="搜索关键词")
    max_results: int = Field(default=5, description="最大结果数")

def search_func(query: str, max_results: int = 5) -> str:
    """执行搜索"""
    return f"搜索 {query}，返回 {max_results} 条结果"

# 用 StructuredTool 创建工具，自定义 schema
search_tool = StructuredTool.from_function(
    func=search_func,
    name="web_search",
    description="搜索网络获取信息",
    args_schema=SearchInput
)
```

**示例4：异步工具**

```python
@tool
async def async_search(query: str) -> str:
    """异步搜索工具"""
    # 模拟异步操作
    import asyncio
    await asyncio.sleep(1)
    return f"异步搜索结果：{query}"

# 异步调用
import asyncio
result = asyncio.run(async_search.ainvoke({"query": "LangGraph"}))
```

### 总结

- **工具三要素**：函数、名称、描述+参数 schema
- **@tool 装饰器**：自动从 docstring 和类型注解生成 schema，最便捷
- **bind_tools**：将工具信息传给 LLM，LLM 决定是否调用
- **LLM 决策**：根据语义选择工具和参数，是 Agent 智能的来源
- **StructuredTool**：需要自定义 schema 时使用
- **异步工具**：支持 `async def`，适合 IO 密集型操作

---

## 第14讲：ToolNode 工具节点

### 概念

**ToolNode** 是 LangGraph 提供的一个预置节点，专门用于执行 LLM 决策调用的工具。它接收包含 `tool_calls` 的 AIMessage，自动执行对应的工具，并返回 ToolMessage 作为结果。ToolNode 大大简化了工具调用的实现——你不需要手动解析 tool_calls、调用工具、构造 ToolMessage，ToolNode 全部帮你处理。

### 原理

**ToolNode 工作原理**：
1. 从 State 中读取最后一条 AIMessage
2. 检查是否有 `tool_calls` 字段
3. 如果有，遍历每个 tool_call，找到对应工具执行
4. 为每个 tool_call 构造 ToolMessage（包含结果和 tool_call_id）
5. 返回 `{"messages": [tool_msg1, tool_msg2, ...]}` 更新 State

**并行执行原理**：如果 LLM 在一条 AIMessage 中调用了多个工具（如同时搜索和计算），ToolNode 会并行执行这些工具，提升效率。这是 LangGraph 并行能力的体现。

**错误处理原理**：如果工具执行抛出异常，ToolNode 会捕获异常，将错误信息作为 ToolMessage 的 content 返回。这样 LLM 能"看到"工具失败的原因，可以决定重试或换一种方式。这种"失败也返回"的设计让 Agent 具备容错能力。

**与条件边配合原理**：ToolNode 通常配合条件边使用——LLM 节点执行后，条件边判断是否有 tool_calls，有则去 ToolNode，没有则结束。ToolNode 执行后，回到 LLM 节点，让 LLM 基于工具结果继续。这就是 ReAct 循环（下一章详解）。

### 例子

**示例1：基础 ToolNode 使用**

```python
from langgraph.graph import StateGraph, START, END
from langgraph.graph.message import MessagesState, add_messages
from langgraph.prebuilt import ToolNode
from langchain_openai import ChatOpenAI
from langchain_core.tools import tool
from langchain_core.messages import HumanMessage

@tool
def search(query: str) -> str:
    """搜索网络信息"""
    return f"搜索结果：关于 {query} 的信息..."

@tool
def calculate(expression: str) -> str:
    """计算数学表达式"""
    try:
        return str(eval(expression))
    except:
        return "计算错误"

# 创建工具列表
tools = [search, calculate]

# 初始化 LLM 并绑定工具
llm = ChatOpenAI(model="gpt-4o-mini")
llm_with_tools = llm.bind_tools(tools)

# 创建 ToolNode
tool_node = ToolNode(tools)

# 定义 LLM 节点
def llm_node(state: MessagesState):
    response = llm_with_tools.invoke(state["messages"])
    return {"messages": [response]}

# 定义路由函数
def should_use_tool(state: MessagesState):
    last_msg = state["messages"][-1]
    if hasattr(last_msg, "tool_calls") and last_msg.tool_calls:
        return "tools"
    return END

# 构建图
builder = StateGraph(MessagesState)
builder.add_node("llm", llm_node)
builder.add_node("tools", tool_node)

builder.add_edge(START, "llm")
builder.add_conditional_edges("llm", should_use_tool, {"tools": "tools", END: END})
builder.add_edge("tools", "llm")  # 工具执行后回到 LLM

graph = builder.compile()

# 测试
result = graph.invoke({
    "messages": [HumanMessage(content="帮我搜索 LangGraph 并计算 1+2*3")]
})
print(result["messages"][-1].content)
```

**示例2：自定义工具节点（不用 ToolNode）**

```python
def custom_tool_node(state: MessagesState):
    """手动实现工具执行节点"""
    last_msg = state["messages"][-1]
    tool_messages = []
    
    if hasattr(last_msg, "tool_calls"):
        for tc in last_msg.tool_calls:
            # 找到对应工具
            tool = next((t for t in tools if t.name == tc["name"]), None)
            if tool:
                try:
                    result = tool.invoke(tc["args"])
                    tool_messages.append(ToolMessage(
                        content=result,
                        tool_call_id=tc["id"]
                    ))
                except Exception as e:
                    tool_messages.append(ToolMessage(
                        content=f"工具执行错误：{e}",
                        tool_call_id=tc["id"]
                    ))
    
    return {"messages": tool_messages}
```

**示例3：带错误处理的工具**

```python
@tool
def divide(a: float, b: float) -> str:
    """除法计算"""
    if b == 0:
        raise ValueError("除数不能为零")
    return str(a / b)

# ToolNode 会自动捕获异常，返回错误信息给 LLM
# LLM 看到错误后，可能会换一种方式回答用户
```

**示例4：ToolNode 处理多个工具调用**

```python
# LLM 可能同时调用多个工具
response = llm_with_tools.invoke("同时搜索 LangGraph 和 LangChain")
# response.tool_calls 可能是：
# [
#   {'name': 'search', 'args': {'query': 'LangGraph'}, 'id': 'call_1'},
#   {'name': 'search', 'args': {'query': 'LangChain'}, 'id': 'call_2'}
# ]

# ToolNode 会并行执行这两个搜索，返回两个 ToolMessage
```

### 总结

- **ToolNode**：预置节点，自动执行 LLM 的 tool_calls
- **工作流程**：读取 AIMessage → 执行工具 → 返回 ToolMessage
- **并行执行**：多个 tool_calls 并行处理，提升效率
- **错误容错**：工具失败也返回错误信息，LLM 可据此调整
- **配合条件边**：判断是否有 tool_calls 决定走向，形成 ReAct 循环
- **可自定义**：不用 ToolNode 也能手动实现，但 ToolNode 省心省力

---

## 第15讲：完整工具调用流程

### 概念

本讲综合前两讲知识，构建一个完整的工具调用 Agent。这个 Agent 能够：理解用户意图、决定是否调用工具、执行工具、基于工具结果生成最终回复。我们将实现一个"研究助手"——具备搜索、计算、文件读取三种工具，能处理复杂的多步骤任务。

### 原理

**完整工具调用流程原理**：

```
用户输入
   ↓
[LLM 节点] ←───────┐
   ↓               │
有 tool_calls?     │
   ↓ 是            │
[ToolNode] ────────┘
   ↓ 否
[END]
```

1. 用户输入作为 HumanMessage 进入 State
2. LLM 节点接收 messages，调用带工具的 LLM
3. LLM 决定是否调用工具：
   - 调用工具：返回 AIMessage(含 tool_calls)
   - 不调用：返回 AIMessage(纯文本)
4. 条件边判断：有 tool_calls 去 ToolNode，没有去 END
5. ToolNode 执行工具，返回 ToolMessage
6. 回到 LLM 节点，LLM 基于工具结果继续决策
7. 循环直到 LLM 不再调用工具，输出最终回复

**多轮工具调用原理**：复杂任务可能需要多轮工具调用——LLM 先搜索获取信息，再计算处理，再搜索补充，最后综合回复。LangGraph 的循环结构天然支持这种多轮调用，每轮都基于前一轮的工具结果。

**工具结果反馈原理**：工具执行后，结果作为 ToolMessage 加入 messages，LLM 在下一轮能看到这个结果。这种"反馈"机制让 LLM 能根据工具实际返回调整策略——比如搜索没找到，LLM 可能换关键词重搜。

**终止条件原理**：循环的终止依赖 LLM 的判断——当 LLM 认为信息足够，不再调用工具，直接输出文本回复，条件边路由到 END，循环结束。为防止 LLM 陷入无限循环，建议加最大迭代次数限制。

### 例子

**完整示例：研究助手 Agent**

```python
import os
from dotenv import load_dotenv
from langchain_openai import ChatOpenAI
from langchain_core.tools import tool
from langchain_core.messages import HumanMessage, SystemMessage
from langgraph.graph import StateGraph, START, END
from langgraph.graph.message import MessagesState
from langgraph.prebuilt import ToolNode

load_dotenv()

# 1. 定义工具
@tool
def web_search(query: str) -> str:
    """搜索网络获取信息。当需要查找最新事实、新闻、技术文档时使用。
    
    Args:
        query: 搜索关键词
    """
    # 模拟搜索（实际可用 Tavily、SerpAPI 等）
    mock_results = {
        "LangGraph": "LangGraph 是 LangChain 推出的 Agent 编排框架，支持状态管理和循环。",
        "Python": "Python 是一种广泛使用的高级编程语言，由 Guido van Rossum 创建。",
    }
    for key, value in mock_results.items():
        if key.lower() in query.lower():
            return value
    return f"搜索 '{query}' 未找到相关结果。"

@tool
def calculator(expression: str) -> str:
    """计算数学表达式。当需要精确数值计算时使用。
    
    Args:
        expression: 数学表达式，如 "2*3+4/2"
    """
    try:
        allowed = set("0123456789+-*/(). ")
        if not all(c in allowed for c in expression):
            return "错误：表达式包含非法字符"
        result = eval(expression)
        return f"{expression} = {result}"
    except Exception as e:
        return f"计算错误：{e}"

@tool
def word_count(text: str) -> str:
    """统计文本的字符数和单词数。
    
    Args:
        text: 要统计的文本
    """
    char_count = len(text)
    word_count = len(text.split())
    return f"字符数：{char_count}，单词数：{word_count}"

# 2. 初始化 LLM 和工具
tools = [web_search, calculator, word_count]
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)
llm_with_tools = llm.bind_tools(tools)

# 3. 定义系统提示
SYSTEM_PROMPT = """你是一个研究助手，可以调用以下工具帮助用户：
- web_search：搜索网络信息
- calculator：数学计算
- word_count：文本统计

工作方式：
1. 分析用户需求，决定是否需要工具
2. 如需工具，调用合适的工具
3. 基于工具结果，继续思考或调用更多工具
4. 信息充足时，给出最终回复

请始终基于工具的真实结果回答，不要编造。"""

# 4. 定义节点
def agent_node(state: MessagesState):
    """LLM 决策节点"""
    messages = [SystemMessage(content=SYSTEM_PROMPT)] + state["messages"]
    response = llm_with_tools.invoke(messages)
    return {"messages": [response]}

tool_node = ToolNode(tools)

# 5. 路由函数
def should_continue(state: MessagesState):
    last_msg = state["messages"][-1]
    if hasattr(last_msg, "tool_calls") and last_msg.tool_calls:
        return "tools"
    return END

# 6. 构建图
builder = StateGraph(MessagesState)
builder.add_node("agent", agent_node)
builder.add_node("tools", tool_node)

builder.add_edge(START, "agent")
builder.add_conditional_edges("agent", should_continue, {"tools": "tools", END: END})
builder.add_edge("tools", "agent")

graph = builder.compile()

# 7. 测试
if __name__ == "__main__":
    # 简单工具调用
    result = graph.invoke({
        "messages": [HumanMessage(content="搜索 LangGraph 是什么")]
    })
    print("回复：", result["messages"][-1].content)
    
    # 多工具组合
    result = graph.invoke({
        "messages": [HumanMessage(content="搜索 LangGraph，然后统计返回结果的字数")]
    })
    print("回复：", result["messages"][-1].content)
    
    # 计算任务
    result = graph.invoke({
        "messages": [HumanMessage(content="计算 (15+25)*2 的结果")]
    })
    print("回复：", result["messages"][-1].content)
```

**执行流程示例**（用户问"搜索 LangGraph，然后统计返回结果的字数"）：

```
1. [agent] LLM 决定调用 web_search("LangGraph")
2. [tools] 执行搜索，返回 "LangGraph 是 LangChain 推出的..."
3. [agent] LLM 看到搜索结果，决定调用 word_count(搜索结果)
4. [tools] 执行统计，返回 "字符数：35，单词数：6"
5. [agent] LLM 看到统计结果，生成最终回复
6. [END]
```

**进阶：带最大迭代限制的版本**

```python
from langgraph.errors import GraphRecursionError

def agent_node_with_limit(state: MessagesState):
    """带迭代限制的 agent 节点"""
    messages = state["messages"]
    
    # 计算当前迭代次数（每对 agent+tools 算一次）
    agent_msgs = [m for m in messages if hasattr(m, 'tool_calls')]
    if len(agent_msgs) >= 10:  # 最多 10 次工具调用
        return {"messages": [AIMessage(content="已达最大迭代次数，强制结束。")]}
    
    # 正常调用 LLM
    full_messages = [SystemMessage(content=SYSTEM_PROMPT)] + messages
    response = llm_with_tools.invoke(full_messages)
    return {"messages": [response]}

# 或者用 recursion_limit 参数
result = graph.invoke(
    {"messages": [HumanMessage(content="复杂任务")]},
    config={"recursion_limit": 20}  # 限制总节点执行次数
)
```

### 总结

- **完整流程**：LLM 决策 → 条件边判断 → ToolNode 执行 → 回到 LLM → 循环
- **多轮工具调用**：复杂任务可多轮调用不同工具，每轮基于前轮结果
- **结果反馈**：工具结果作为 ToolMessage 加入 messages，LLM 据此调整
- **终止条件**：LLM 不再调用工具时结束，建议加最大迭代限制
- **recursion_limit**：LangGraph 内置的递归限制，防止无限循环
- **生产化要点**：真实工具需考虑超时、重试、并发限制、日志监控

---

# 第5章 Agent 架构

本章探讨 Agent 的架构模式。从最经典的 ReAct 模式开始，到 LangGraph 提供的预置 Agent，再到多 Agent 协作系统和 Supervisor 层级架构。学完本章，你将能够根据场景选择合适的 Agent 架构，并构建复杂的多 Agent 系统。

---

## 第16讲：ReAct Agent 原理与实现

### 概念

**ReAct（Reasoning + Acting）** 是一种经典的 Agent 架构模式，由 Yao 等人在 2022 年提出。它的核心思想是让 LLM 交替进行"推理（Thought）"和"行动（Action）"——先思考该做什么，然后执行行动（调用工具），观察结果，再思考下一步，如此循环直到完成任务。

ReAct 模式是现代 Agent 的基础范式，LangGraph 中的工具调用 Agent 本质上就是 ReAct 的实现。理解 ReAct 有助于深入理解 Agent 的工作机制。

### 原理

**ReAct 循环原理**：ReAct 的核心是一个"思考-行动-观察"的循环：

```
Thought（思考）：分析当前情况，决定下一步
Action（行动）：执行工具调用
Observation（观察）：获取工具返回结果
↓
Thought（再思考）：基于观察，决定下一步
...
```

这个循环持续进行，直到 LLM 认为任务完成，输出最终答案。

**为什么 ReAct 有效原理**：相比单纯的"思维链"（Chain of Thought，只推理不行动）或单纯的"行动链"（只调用工具不推理），ReAct 的优势在于：
1. **推理指导行动**：每步行动前先思考，避免盲目调用工具
2. **行动反馈推理**：工具结果作为新信息，修正后续推理
3. **可解释性**：思考过程可见，便于调试
4. **容错性**：工具失败时，推理能调整策略

**ReAct 与 LangGraph 的关系原理**：LangGraph 的工具调用 Agent（第15讲）本质上就是 ReAct 实现——LLM 节点对应"Thought+Action 决策"，ToolNode 对应"Action 执行"，工具结果对应"Observation"，循环结构对应 ReAct 循环。LangGraph 让 ReAct 的实现变得非常简洁。

**ReAct 的 Prompt 设计原理**：经典 ReAct 用特定的 Prompt 格式引导 LLM 输出 Thought/Action/Observation。现代 LLM 通过 tool_calls 原生支持函数调用，不再需要特殊 Prompt 格式，但思想一致——LLM 先"决定"调用工具（Thought+Action），工具返回（Observation），LLM 继续。

### 例子

**示例1：经典 ReAct Prompt（理解原理）**

```python
REACT_PROMPT = """Answer the following questions as best you can.

You have access to the following tools:
{tools_description}

Use the following format:
Question: the input question you must answer
Thought: you should always think about what to do
Action: the action to take, should be one of [{tool_names}]
Action Input: the input to the action
Observation: the result of the action
... (this Thought/Action/Action Input/Observation can repeat N times)
Thought: I now know the final answer
Final Answer: the final answer to the original question

Begin!
Question: {input}
Thought:{agent_scratchpad}"""
```

**示例2：LangGraph 实现 ReAct（现代方式）**

```python
from typing import TypedDict, Annotated
from langgraph.graph import StateGraph, START, END
from langgraph.graph.message import add_messages
from langchain_openai import ChatOpenAI
from langchain_core.tools import tool
from langchain_core.messages import HumanMessage, SystemMessage
from langgraph.prebuilt import ToolNode

@tool
def search(query: str) -> str:
    """搜索网络信息"""
    return f"关于 '{query}' 的搜索结果..."

@tool
def calculate(expression: str) -> str:
    """数学计算"""
    return str(eval(expression))

tools = [search, calculate]
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)
llm_with_tools = llm.bind_tools(tools)

# ReAct 系统提示
REACT_SYSTEM = """你是一个 ReAct Agent。

工作流程：
1. Thought: 分析用户问题，思考需要哪些信息
2. Action: 调用合适的工具获取信息
3. Observation: 查看工具返回结果
4. 重复 1-3 直到信息充足
5. Final Answer: 给出最终回答

每次只调用一个工具，基于结果再决定下一步。"""

class AgentState(TypedDict):
    messages: Annotated[list, add_messages]

def react_agent_node(state: AgentState):
    """ReAct 的 Thought + Action 决策节点"""
    messages = [SystemMessage(content=REACT_SYSTEM)] + state["messages"]
    response = llm_with_tools.invoke(messages)
    return {"messages": [response]}

def should_act(state: AgentState):
    """判断是否需要继续行动"""
    last_msg = state["messages"][-1]
    if hasattr(last_msg, "tool_calls") and last_msg.tool_calls:
        return "act"  # 需要 Action
    return END        # 输出 Final Answer

# 构建 ReAct 图
builder = StateGraph(AgentState)
builder.add_node("agent", react_agent_node)  # Thought + Action 决策
builder.add_node("act", ToolNode(tools))       # Action 执行

builder.add_edge(START, "agent")
builder.add_conditional_edges("agent", should_act, {"act": "act", END: END})
builder.add_edge("act", "agent")  # Observation 后回到 Thought

react_graph = builder.compile()

# 测试
result = react_graph.invoke({
    "messages": [HumanMessage(content="搜索 LangGraph，然后计算它的字符数")]
})
```

**ReAct 执行流程**：
```
用户：搜索 LangGraph，然后计算它的字符数

[Thought 1] 需要先搜索 LangGraph 的信息
[Action 1] search("LangGraph")
[Observation 1] "LangGraph 是 LangChain 推出的 Agent 编排框架..."

[Thought 2] 拿到搜索结果，现在需要计算字符数
[Action 2] calculate("len('LangGraph 是 LangChain...')")
[Observation 2] "35"

[Thought 3] 信息充足，可以回答了
[Final Answer] LangGraph 搜索结果的字符数是 35。
```

**示例3：观察 ReAct 的思考过程**

```python
# 用 stream 观察每步
for event in react_graph.stream(
    {"messages": [HumanMessage(content="计算 25 * 4 + 100")]},
    stream_mode="updates"
):
    for node, output in event.items():
        print(f"\n[{node}]")
        if "messages" in output:
            for msg in output["messages"]:
                if hasattr(msg, "tool_calls") and msg.tool_calls:
                    print(f"  Action: {msg.tool_calls}")
                elif hasattr(msg, "content"):
                    print(f"  Content: {msg.content[:100]}")
```

### 总结

- **ReAct = Reasoning + Acting**：思考-行动-观察的循环
- **核心循环**：Thought → Action → Observation → Thought → ...
- **优势**：推理指导行动、行动反馈推理、可解释、容错
- **LangGraph 实现**：LLM 节点（Thought+Action决策）+ ToolNode（Action执行）+ 循环
- **现代 LLM**：通过 tool_calls 原生支持，无需特殊 Prompt 格式
- **调试技巧**：用 stream_mode="updates" 观察每步思考过程

---

## 第17讲：create_react_agent 详解

### 概念

**create_react_agent** 是 LangGraph 提供的一个预置函数，用于快速创建 ReAct Agent。它封装了第16讲中手动构建的所有逻辑——LLM 节点、ToolNode、条件边、循环——一行代码就能创建一个功能完整的 ReAct Agent。这是构建工具调用 Agent 的推荐方式。

### 原理

**create_react_agent 封装原理**：手动构建 ReAct Agent 需要约 20 行代码（定义 State、节点、边、条件边、编译）。`create_react_agent` 把这些封装为一个函数调用，参数包括：
- `model`：使用的 LLM
- `tools`：工具列表
- `prompt`：系统提示（可选）
- `state_schema`：自定义 State（可选）

函数内部自动完成：创建 MessagesState、添加 LLM 节点、添加 ToolNode、连接条件边、编译图。

**预置 Agent 的优势原理**：
1. **简洁**：一行代码创建完整 Agent
2. **可靠**：经过充分测试，处理了边界情况
3. **可扩展**：支持自定义 prompt、state、节点
4. **一致**：官方维护，API 稳定

**何时手动构建原理**：虽然 create_react_agent 很方便，但有些场景需要手动构建：
1. 需要多个 LLM 节点（如路由+生成）
2. 需要复杂的条件逻辑（不只是"有 tool_calls 就执行"）
3. 需要在工具执行前后插入额外节点（如日志、验证）
4. 需要非标准的 State 结构

**create_react_agent 的内部结构原理**：它创建的图结构是：
```
[START] → [agent] → (有 tool_calls?) → [tools] → [agent] → ...
                       ↓ 没有
                     [END]
```
与第16讲手动构建的完全一致，只是封装了样板代码。

### 例子

**示例1：最简 create_react_agent**

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

# 一行创建 ReAct Agent
agent = create_react_agent(
    model=ChatOpenAI(model="gpt-4o-mini"),
    tools=[search, calculate]
)

# 使用
result = agent.invoke({"messages": [("user", "搜索 LangGraph")]})
print(result["messages"][-1].content)
```

**示例2：带系统提示的 Agent**

```python
agent = create_react_agent(
    model=ChatOpenAI(model="gpt-4o-mini", temperature=0),
    tools=[search, calculate],
    prompt="""你是一个专业的研究助手。

工作规范：
1. 回答前先搜索确认信息
2. 涉及计算时使用计算工具
3. 回答简洁、准确、有依据
4. 不确定时坦诚告知"""
)

result = agent.invoke({"messages": [("user", "LangGraph 是什么？")]})
```

**示例3：自定义 State**

```python
from typing import Annotated
from typing_extensions import TypedDict
from langgraph.graph.message import add_messages, MessagesState

class MyState(MessagesState):
    # messages 已自动包含
    user_id: str
    conversation_id: str

agent = create_react_agent(
    model=ChatOpenAI(model="gpt-4o-mini"),
    tools=[search, calculate],
    state_schema=MyState
)

# 使用时传入额外字段
result = agent.invoke({
    "messages": [("user", "你好")],
    "user_id": "user_123",
    "conversation_id": "conv_456"
})
```

**示例4：带状态修改的 Agent**

```python
def modify_state(state: MyState):
    """在 agent 节点前修改状态"""
    # 可以在这里做日志、验证等
    print(f"用户 {state.get('user_id')} 发起请求")
    return {}  # 不修改状态

agent = create_react_agent(
    model=ChatOpenAI(model="gpt-4o-mini"),
    tools=[search, calculate],
    state_schema=MyState,
    prompt="你是助手",
    pre_model_hook=modify_state  # 在 LLM 调用前执行
)
```

**示例5：完整功能示例**

```python
from langchain_openai import ChatOpenAI
from langchain_core.tools import tool
from langchain_core.messages import HumanMessage
from langgraph.prebuilt import create_react_agent

# 工具
@tool
def get_weather(city: str) -> str:
    """获取城市天气"""
    weather = {"北京": "晴 25°C", "上海": "多云 28°C", "广州": "雨 30°C"}
    return weather.get(city, f"未知城市 {city}")

@tool
def get_time(timezone: str = "UTC+8") -> str:
    """获取当前时间"""
    from datetime import datetime, timezone, timedelta
    tz = timezone(timedelta(hours=8)) if "8" in timezone else timezone.utc
    return datetime.now(tz).strftime("%Y-%m-%d %H:%M:%S")

# 创建 Agent
agent = create_react_agent(
    model=ChatOpenAI(model="gpt-4o-mini", temperature=0),
    tools=[get_weather, get_time],
    prompt="""你是生活助手，可以查询天气和时间。
回答要友好、简洁。当用户问多个问题时，逐一处理。"""
)

# 测试
result = agent.invoke({
    "messages": [HumanMessage(content="北京现在天气怎么样？现在几点了？")]
})
print(result["messages"][-1].content)
```

### 总结

- **create_react_agent**：一行代码创建 ReAct Agent，封装所有样板
- **核心参数**：model、tools、prompt、state_schema
- **优势**：简洁、可靠、可扩展、官方维护
- **何时手动构建**：多 LLM 节点、复杂条件逻辑、额外节点、非标准 State
- **内部结构**：与手动构建的 ReAct 完全一致
- **推荐用法**：优先用 create_react_agent，复杂场景再手动构建

---

## 第18讲：多 Agent 系统

### 概念

**多 Agent 系统（Multi-Agent System）** 是指多个 Agent 协作完成复杂任务的架构。每个 Agent 专注于一个领域或子任务，通过消息传递、状态共享协作完成整体目标。比如一个"研究团队"Agent 系统，包含搜索 Agent、分析 Agent、写作 Agent，各司其职。

多 Agent 系统的优势在于：专业化（每个 Agent 专注一个领域）、可扩展（新增 Agent 即新增能力）、可维护（修改一个 Agent 不影响其他）、容错（一个 Agent 失败不致命）。

### 原理

**多 Agent 协作原理**：多 Agent 系统的核心是"分工+协作"。常见协作模式：

1. **顺序协作**：Agent A 完成 → Agent B 接力 → Agent C 收尾。适合流水线任务。
2. **并行协作**：多个 Agent 同时工作，最后汇总。适合独立子任务。
3. **层级协作**：一个 Supervisor Agent 调度其他 Agent。适合复杂任务（下一讲详解）。
4. **对话协作**：Agent 间通过消息对话协商。适合需要讨论的任务。

**状态共享原理**：多 Agent 系统中，Agent 间通过共享 State 传递信息。每个 Agent 读取所需字段、写入产出字段。比如搜索 Agent 写入 `search_results`，分析 Agent 读取 `search_results` 写入 `analysis`，写作 Agent 读取 `analysis` 写入 `report`。

**子图原理**：在 LangGraph 中，每个 Agent 本身是一个图（StateGraph）。多 Agent 系统通过将子图作为节点嵌入主图实现。子图有自己的 State，可以与主图共享 State 或独立。

**消息传递原理**：Agent 间也可以通过消息列表传递信息——Agent A 的输出作为 Agent B 的输入消息。这种方式适合对话式协作。

### 例子

**示例1：顺序协作的多 Agent 系统**

```python
from typing import TypedDict, Annotated
from operator import add
from langgraph.graph import StateGraph, START, END
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)

class ResearchState(TypedDict):
    topic: str
    search_results: str
    analysis: str
    report: str

# Agent 1: 搜索
def search_agent(state: ResearchState):
    prompt = f"针对主题 '{state['topic']}'，列出3个关键信息点。"
    result = llm.invoke(prompt).content
    return {"search_results": result}

# Agent 2: 分析
def analysis_agent(state: ResearchState):
    prompt = f"基于以下信息，分析其含义和影响：\n{state['search_results']}"
    result = llm.invoke(prompt).content
    return {"analysis": result}

# Agent 3: 写作
def writing_agent(state: ResearchState):
    prompt = f"基于以下分析，写一份简洁报告：\n{state['analysis']}"
    result = llm.invoke(prompt).content
    return {"report": result}

# 构建多 Agent 图
builder = StateGraph(ResearchState)
builder.add_node("searcher", search_agent)
builder.add_node("analyzer", analysis_agent)
builder.add_node("writer", writing_agent)

builder.add_edge(START, "searcher")
builder.add_edge("searcher", "analyzer")
builder.add_edge("analyzer", "writer")
builder.add_edge("writer", END)

multi_agent_graph = builder.compile()

# 测试
result = multi_agent_graph.invoke({"topic": "LangGraph 的应用前景"})
print(result["report"])
```

**示例2：并行协作的多 Agent 系统**

```python
class ParallelState(TypedDict):
    topic: str
    tech_view: str
    business_view: str
    summary: str

def tech_agent(state):
    """技术视角分析"""
    prompt = f"从技术角度分析：{state['topic']}"
    return {"tech_view": llm.invoke(prompt).content}

def business_agent(state):
    """商业视角分析"""
    prompt = f"从商业角度分析：{state['topic']}"
    return {"business_view": llm.invoke(prompt).content}

def summary_agent(state):
    """汇总两个视角"""
    prompt = f"综合以下两个视角的分析，给出总结：\n技术：{state['tech_view']}\n商业：{state['business_view']}"
    return {"summary": llm.invoke(prompt).content}

builder = StateGraph(ParallelState)
builder.add_node("tech", tech_agent)
builder.add_node("business", business_agent)
builder.add_node("summarizer", summary_agent)

# 并行：START 同时连到两个 Agent
builder.add_edge(START, "tech")
builder.add_edge(START, "business")
# 两个 Agent 都完成后到 summarizer
builder.add_edge("tech", "summarizer")
builder.add_edge("business", "summarizer")
builder.add_edge("summarizer", END)

parallel_graph = builder.compile()
result = parallel_graph.invoke({"topic": "AI Agent 的未来"})
```

**示例3：子图作为节点**

```python
# 把单个 Agent 封装为子图
def make_search_subgraph():
    """创建搜索子图"""
    sub_builder = StateGraph(ResearchState)
    sub_builder.add_node("search", search_agent)
    sub_builder.add_edge(START, "search")
    sub_builder.add_edge("search", END)
    return sub_builder.compile()

# 主图使用子图
builder = StateGraph(ResearchState)
builder.add_node("search_subgraph", make_search_subgraph())  # 子图作为节点
builder.add_node("analyzer", analysis_agent)
builder.add_edge(START, "search_subgraph")
builder.add_edge("search_subgraph", "analyzer")
builder.add_edge("analyzer", END)
```

### 总结

- **多 Agent 系统**：多个 Agent 协作完成复杂任务
- **协作模式**：顺序、并行、层级、对话
- **状态共享**：Agent 间通过 State 字段传递信息
- **子图**：每个 Agent 是一个图，可作为节点嵌入主图
- **优势**：专业化、可扩展、可维护、容错
- **设计要点**：明确每个 Agent 的职责、定义清晰的 State 字段、选择合适的协作模式

---

## 第19讲：Supervisor 层级 Agent

### 概念

**Supervisor（主管）Agent** 是多 Agent 系统的一种层级架构。一个 Supervisor Agent 充当"调度员"，根据用户请求决定调用哪个子 Agent，子 Agent 完成任务后回报给 Supervisor，Supervisor 决定是否继续调用其他子 Agent 或返回最终结果。

这种架构模拟了真实团队的工作方式——主管分配任务，成员执行任务，主管汇总结果。Supervisor 模式适合复杂任务，能处理需要多步骤、多领域协作的场景。

### 原理

**Supervisor 工作原理**：
1. 用户请求进入 Supervisor
2. Supervisor 分析请求，决定下一步调用哪个子 Agent（或直接回复）
3. 被选中的子 Agent 执行任务，结果回到 Supervisor
4. Supervisor 基于结果，决定继续调用其他子 Agent 还是结束
5. 循环直到 Supervisor 认为任务完成

**Supervisor 的 LLM 路由原理**：Supervisor 本质上是一个 LLM 节点，它的"工具"就是各个子 Agent。通过 `bind_tools` 把子 Agent 作为"工具"绑定给 Supervisor LLM，Supervisor 决定调用哪个子 Agent，就像普通 Agent 决定调用哪个工具一样。

**层级原理**：Supervisor 模式天然支持多级层级——一个 Supervisor 可以管理多个子 Supervisor，每个子 Supervisor 又管理自己的子 Agent。这种树状结构能处理非常复杂的任务。

**与 ReAct 的关系原理**：Supervisor 模式本质上是 ReAct 的扩展——把"工具"替换为"子 Agent"。每个子 Agent 本身可以是 ReAct Agent，有自己的工具和循环。这样形成了"Agent 套 Agent"的层级结构。

### 例子

**示例1：基础 Supervisor 架构**

```python
from typing import TypedDict, Annotated, Literal
from langgraph.graph import StateGraph, START, END
from langgraph.graph.message import add_messages
from langchain_openai import ChatOpenAI
from langchain_core.messages import HumanMessage
from langchain_core.tools import tool

llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)

class TeamState(TypedDict):
    messages: Annotated[list, add_messages]
    next: str  # 下一个要调用的 Agent

# 子 Agent 1: 研究员
def researcher(state: TeamState):
    """研究 Agent"""
    prompt = f"作为研究员，回答以下问题：\n{state['messages'][-1].content}"
    response = llm.invoke(prompt)
    return {"messages": [response]}

# 子 Agent 2: 写作者
def writer(state: TeamState):
    """写作 Agent"""
    prompt = f"作为写作者，基于对话内容写一份报告：\n{state['messages'][-1].content}"
    response = llm.invoke(prompt)
    return {"messages": [response]}

# Supervisor
def supervisor(state: TeamState):
    """主管 Agent：决定下一步调用谁"""
    # 用 LLM 决策
    prompt = f"""你是团队主管。根据用户请求，决定下一步：
- "researcher": 需要研究/查找信息时调用
- "writer": 需要写作/总结时调用
- "FINISH": 任务完成，结束

对话历史：
{[m.content for m in state['messages']]}

只输出一个词：researcher / writer / FINISH"""
    decision = llm.invoke(prompt).content.strip()
    return {"next": decision}

def route_from_supervisor(state: TeamState):
    """根据 Supervisor 决策路由"""
    next_agent = state["next"]
    if next_agent == "FINISH":
        return END
    return next_agent

# 构建图
builder = StateGraph(TeamState)
builder.add_node("supervisor", supervisor)
builder.add_node("researcher", researcher)
builder.add_node("writer", writer)

builder.add_edge(START, "supervisor")
builder.add_conditional_edges(
    "supervisor",
    route_from_supervisor,
    {"researcher": "researcher", "writer": "writer", END: END}
)
# 子 Agent 完成后回到 Supervisor
builder.add_edge("researcher", "supervisor")
builder.add_edge("writer", "supervisor")

supervisor_graph = builder.compile()

# 测试
result = supervisor_graph.invoke({
    "messages": [HumanMessage(content="研究 LangGraph 并写一份简介")]
})
print(result["messages"][-1].content)
```

**Supervisor 流程**：
```
用户：研究 LangGraph 并写一份简介
   ↓
[Supervisor] → researcher（需要先研究）
   ↓
[Researcher] 执行研究，返回结果
   ↓
[Supervisor] → writer（现在需要写作）
   ↓
[Writer] 执行写作，返回报告
   ↓
[Supervisor] → FINISH（任务完成）
   ↓
[END]
```

**示例2：用 create_react_agent 构建子 Agent**

```python
from langgraph.prebuilt import create_react_agent

# 研究员 Agent（带工具）
@tool
def web_search(query: str) -> str:
    """搜索网络"""
    return f"搜索结果：{query}"

researcher_agent = create_react_agent(
    model=llm,
    tools=[web_search],
    prompt="你是研究员，擅长搜索和整理信息。"
)

# 写作者 Agent
writer_agent = create_react_agent(
    model=llm,
    tools=[],
    prompt="你是写作者，擅长把信息整理成清晰的文章。"
)

# Supervisor 调用子 Agent
def supervisor_with_subagents(state: TeamState):
    prompt = f"""决定下一步调用哪个 Agent：
- "researcher": 研究员
- "writer": 写作者
- "FINISH": 完成

对话：{[m.content for m in state['messages']]}"""
    decision = llm.invoke(prompt).content.strip()
    return {"next": decision}

# 把子 Agent 作为节点
builder = StateGraph(TeamState)
builder.add_node("supervisor", supervisor_with_subagents)
builder.add_node("researcher", researcher_agent)  # 子 Agent 作为节点
builder.add_node("writer", writer_agent)

builder.add_edge(START, "supervisor")
builder.add_conditional_edges("supervisor", route_from_supervisor,
    {"researcher": "researcher", "writer": "writer", END: END})
builder.add_edge("researcher", "supervisor")
builder.add_edge("writer", "supervisor")
```

**示例3：多级 Supervisor**

```python
# 二级 Supervisor：管理子 Supervisor
def chief_supervisor(state):
    """总 Supervisor：决定调用哪个子团队"""
    prompt = f"""你是总管。决定调用哪个团队：
- "research_team": 研究团队
- "writing_team": 写作团队
- "FINISH": 完成"""
    return {"next": llm.invoke(prompt).content.strip()}

# 子 Supervisor
def research_supervisor(state):
    """研究团队主管"""
    # 决定调用研究员还是分析师
    ...
```

### 总结

- **Supervisor 模式**：一个主管 Agent 调度多个子 Agent
- **工作流程**：Supervisor 决策 → 子 Agent 执行 → 回到 Supervisor → 循环
- **LLM 路由**：Supervisor 用 LLM 决定调用哪个子 Agent
- **层级结构**：支持多级 Supervisor，处理复杂任务
- **与 ReAct 关系**：Supervisor 是 ReAct 的扩展，子 Agent 作为"工具"
- **适用场景**：复杂任务、多领域协作、需要调度的场景

---

# 第6章 高级控制流

本章探讨 LangGraph 的高级控制流技巧，包括循环与迭代控制、并行节点执行、子图 Subgraph、Command 命令跳转。这些技巧让你能够构建任意复杂的 Agent 工作流，处理真实世界的复杂场景。学完本章，你将掌握 LangGraph 的全部控制流能力。

---

## 第20讲：循环与迭代控制

### 概念

**循环（Loop）** 是 LangGraph 的核心能力之一——通过条件边让执行流回到之前的节点，形成循环。循环是 ReAct Agent、迭代优化、多轮处理等场景的基础。但循环也带来风险——无限循环会耗尽资源。本讲深入循环的实现与控制。

**迭代控制** 指对循环次数、终止条件的管理，确保循环在合适的时候结束。常见的迭代控制包括：最大次数限制、状态条件终止、超时控制、人工中断。

### 原理

**循环实现原理**：在 LangGraph 中，循环通过"条件边指向前序节点"实现。比如 A → B → 条件边判断 → 回到 A 或去 END。条件边的路由函数返回 A 的名字，就形成循环。LangGraph 的 Pregel 模型天然支持这种循环——每个超步执行后，根据条件决定下一步。

**循环终止原理**：循环必须有终止条件，否则会无限执行。终止条件通常由路由函数判断：
1. **状态条件**：检查 State 中的某字段，如 `task_complete == True` 则结束
2. **计数器**：每次循环递增计数器，超过阈值则结束
3. **LLM 决策**：让 LLM 判断是否完成
4. **质量评估**：评估结果质量，达标则结束

**recursion_limit 原理**：LangGraph 内置了递归限制机制。`graph.invoke(input, config={"recursion_limit": N})` 限制图的总执行步数不超过 N。默认值通常是 25。超过限制会抛出 `GraphRecursionError`。这是防止无限循环的安全网。

**循环与状态累积原理**：循环中，State 会累积信息——每轮的输出都加入 State。这种累积让后续循环能基于之前的结果决策。但也可能导致 State 膨胀，需要适时清理或摘要。

### 例子

**示例1：基础循环——迭代优化**

```python
from typing import TypedDict
from langgraph.graph import StateGraph, START, END
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)

class OptimizeState(TypedDict):
    topic: str
    draft: str
    feedback: str
    iteration: int
    final_article: str

def write_draft(state):
    prompt = f"写一段关于'{state['topic']}'的短文（100字内）"
    draft = llm.invoke(prompt).content
    return {"draft": draft, "iteration": state.get("iteration", 0) + 1}

def review_draft(state):
    prompt = f"评价以下短文，指出1个改进点。如果已经很好，回复'PASS'：\n{state['draft']}"
    feedback = llm.invoke(prompt).content
    return {"feedback": feedback}

def revise_draft(state):
    prompt = f"基于反馈改进以下短文：\n反馈：{state['feedback']}\n原文：{state['draft']}"
    revised = llm.invoke(prompt).content
    return {"draft": revised, "iteration": state["iteration"] + 1}

def should_continue(state):
    # 终止条件：PASS 或超过 5 次
    if "PASS" in state.get("feedback", ""):
        return "done"
    if state.get("iteration", 0) >= 5:
        return "done"
    return "revise"

builder = StateGraph(OptimizeState)
builder.add_node("writer", write_draft)
builder.add_node("reviewer", review_draft)
builder.add_node("reviser", revise_draft)

builder.add_edge(START, "writer")
builder.add_edge("writer", "reviewer")
builder.add_conditional_edges("reviewer", should_continue, {
    "revise": "reviser",
    "done": END
})
builder.add_edge("reviser", "reviewer")  # 回到 reviewer，形成循环

graph = builder.compile()

result = graph.invoke({"topic": "AI 的未来", "iteration": 0, "draft": "", "feedback": "", "final_article": ""})
print(f"迭代次数：{result['iteration']}")
print(f"最终文章：{result['draft']}")
```

**循环流程**：
```
[START] → [writer] → [reviewer] → (PASS or iter>=5?)
                          ↑              ↓ revise
                          └── [reviser] ┘
                          ↓ done
                       [END]
```

**示例2：带 recursion_limit 的安全循环**

```python
# 限制总步数为 20
try:
    result = graph.invoke(
        {"topic": "复杂主题", "iteration": 0, "draft": "", "feedback": "", "final_article": ""},
        config={"recursion_limit": 20}
    )
except Exception as e:
    print(f"循环被中断：{e}")
```

**示例3：计数器控制循环**

```python
class CounterState(TypedDict):
    items: list
    processed: list
    current_index: int

def process_item(state):
    items = state["items"]
    idx = state["current_index"]
    
    if idx >= len(items):
        return {}  # 没有更多项
    
    # 处理当前项
    item = items[idx]
    processed = state.get("processed", [])
    processed.append(f"processed_{item}")
    
    return {
        "processed": processed,
        "current_index": idx + 1
    }

def should_continue(state):
    if state["current_index"] >= len(state["items"]):
        return END
    return "process"

builder = StateGraph(CounterState)
builder.add_node("process", process_item)
builder.add_edge(START, "process")
builder.add_conditional_edges("process", should_continue, {"process": "process", END: END})

graph = builder.compile()
result = graph.invoke({
    "items": ["a", "b", "c", "d"],
    "processed": [],
    "current_index": 0
})
print(result["processed"])  # ['processed_a', 'processed_b', 'processed_c', 'processed_d']
```

**示例4：LLM 决策终止**

```python
def should_finish(state):
    """让 LLM 判断是否完成"""
    prompt = f"""基于以下对话，判断任务是否完成。
回复 'DONE' 或 'CONTINUE'：
{state['messages'][-1].content}"""
    decision = llm.invoke(prompt).content.strip().upper()
    return "done" if "DONE" in decision else "continue"
```

### 总结

- **循环实现**：条件边指向前序节点，形成循环
- **终止条件**：状态条件、计数器、LLM 决策、质量评估
- **recursion_limit**：内置安全网，防止无限循环，默认约 25
- **状态累积**：循环中 State 累积信息，需适时清理
- **常见模式**：迭代优化、批量处理、多轮对话
- **生产建议**：始终设置 recursion_limit，避免无限循环耗尽资源

---

## 第21讲：并行节点执行

### 概念

**并行执行（Parallel Execution）** 是 LangGraph 提升效率的重要能力。当多个节点没有依赖关系时，可以并行执行，而非顺序执行。比如同时搜索多个关键词、同时分析多个文档、同时调用多个 API。并行执行能显著缩短总执行时间。

在 LangGraph 中，并行通过"一个节点同时连到多个节点"或"多个节点同时连到一个节点"实现。前者是"扇出"（fan-out），后者是"扇入"（fan-in）。

### 原理

**扇出原理**：从一个节点出发，多条边连到不同节点，这些节点并行执行。比如 `add_edge("source", "a")` + `add_edge("source", "b")`，a 和 b 同时执行。LangGraph 在一个超步内并行调度所有就绪节点。

**扇入原理**：多个节点同时连到一个节点，该节点等待所有上游节点完成才执行。比如 `add_edge("a", "target")` + `add_edge("b", "target")`，target 等 a 和 b 都完成才执行。这是"屏障"（barrier）语义。

**状态合并原理**：并行节点都返回状态更新时，需要 Reducer 决定如何合并。如果两个节点都更新同一字段且无 Reducer，结果不确定（取决于哪个先完成）。所以并行更新同一字段必须用 Reducer（如 `operator.add` 追加，或自定义合并）。

**超步模型原理**：LangGraph 基于 Pregel 模型，每个超步内所有就绪节点并行执行，超步结束合并状态，下一个超步执行依赖的节点。这种模型天然支持并行——只要节点间无数据依赖，就并行执行。

**并行 vs 顺序原理**：并行不是万能的。当节点间有依赖（B 需要 A 的输出）时，必须顺序执行。并行只适用于无依赖的节点。设计并行工作流的关键是识别哪些任务可以并行。

### 例子

**示例1：扇出+扇入（并行搜索+汇总）**

```python
from typing import TypedDict, Annotated
from operator import add
from langgraph.graph import StateGraph, START, END

class SearchState(TypedDict):
    query: str
    # 用 Reducer 合并并行结果
    results: Annotated[list, add]
    summary: str

def search_a(state):
    """搜索源 A"""
    return {"results": [f"A: 关于 {state['query']} 的结果"]}

def search_b(state):
    """搜索源 B"""
    return {"results": [f"B: 关于 {state['query']} 的结果"]}

def search_c(state):
    """搜索源 C"""
    return {"results": [f"C: 关于 {state['query']} 的结果"]}

def summarize(state):
    """汇总所有结果"""
    all_results = "\n".join(state["results"])
    return {"summary": f"汇总：\n{all_results}"}

builder = StateGraph(SearchState)
builder.add_node("search_a", search_a)
builder.add_node("search_b", search_b)
builder.add_node("search_c", search_c)
builder.add_node("summarizer", summarize)

# 扇出：START 同时连到三个搜索节点
builder.add_edge(START, "search_a")
builder.add_edge(START, "search_b")
builder.add_edge(START, "search_c")

# 扇入：三个搜索节点都到 summarizer
builder.add_edge("search_a", "summarizer")
builder.add_edge("search_b", "summarizer")
builder.add_edge("search_c", "summarizer")

builder.add_edge("summarizer", END)

graph = builder.compile()
result = graph.invoke({"query": "LangGraph", "results": []})
print(result["summary"])
```

**并行流程**：
```
                ┌→ [search_a] ┐
[START] ────────┼→ [search_b] ┼→ [summarizer] → [END]
                └→ [search_c] ┘
         (扇出，并行)     (扇入，等所有完成)
```

**示例2：并行 LLM 分析**

```python
from langchain_openai import ChatOpenAI
llm = ChatOpenAI(model="gpt-4o-mini")

class AnalysisState(TypedDict):
    text: str
    tech_analysis: Annotated[list, add]
    business_analysis: Annotated[list, add]
    legal_analysis: Annotated[list, add]
    final_report: str

def tech_analyst(state):
    prompt = f"从技术角度分析：{state['text']}"
    return {"tech_analysis": [llm.invoke(prompt).content]}

def business_analyst(state):
    prompt = f"从商业角度分析：{state['text']}"
    return {"business_analysis": [llm.invoke(prompt).content]}

def legal_analyst(state):
    prompt = f"从法律角度分析：{state['text']}"
    return {"legal_analysis": [llm.invoke(prompt).content]}

def combine(state):
    report = f"""综合分析报告：
技术：{state['tech_analysis'][0]}
商业：{state['business_analysis'][0]}
法律：{state['legal_analysis'][0]}"""
    return {"final_report": report}

builder = StateGraph(AnalysisState)
builder.add_node("tech", tech_analyst)
builder.add_node("business", business_analyst)
builder.add_node("legal", legal_analyst)
builder.add_node("combiner", combine)

# 三个分析师并行
builder.add_edge(START, "tech")
builder.add_edge(START, "business")
builder.add_edge(START, "legal")

# 都完成后汇总
builder.add_edge("tech", "combiner")
builder.add_edge("business", "combiner")
builder.add_edge("legal", "combiner")
builder.add_edge("combiner", END)

graph = builder.compile()
result = graph.invoke({
    "text": "AI Agent 技术的发展",
    "tech_analysis": [], "business_analysis": [], "legal_analysis": []
})
```

**示例3：条件扇出（动态并行）**

```python
def dispatcher(state):
    """根据任务动态决定扇出到哪些节点"""
    tasks = state["tasks"]
    # 这里可以根据 tasks 内容决定
    return {}  # 实际扇出通过条件边实现

# 用条件边实现动态扇出
builder.add_conditional_edges(
    "dispatcher",
    lambda state: state["tasks"],  # 返回任务列表
    {f"task_{i}": f"task_{i}" for i in range(5)}  # 预定义 5 个任务节点
)
```

**示例4：并行执行的注意事项**

```python
# ❌ 错误：并行更新同一字段无 Reducer
class BadState(TypedDict):
    result: str  # 默认覆盖，并行结果不确定

def node_a(state):
    return {"result": "from_a"}  # 并行执行，谁先完成谁覆盖

def node_b(state):
    return {"result": "from_b"}

# 结果不确定，可能是 "from_a" 或 "from_b"

# ✅ 正确：用 Reducer 合并
class GoodState(TypedDict):
    results: Annotated[list, add]  # 追加，两个结果都保留
```

### 总结

- **扇出**：一个节点同时连到多个节点，并行执行
- **扇入**：多个节点连到一个节点，等待所有完成
- **Reducer 必需**：并行更新同一字段必须用 Reducer，否则结果不确定
- **超步模型**：Pregel 模型天然支持并行，无依赖的节点自动并行
- **适用场景**：多源搜索、多角度分析、批量处理
- **注意事项**：有依赖的节点不能并行，需顺序执行

---

## 第22讲：子图 Subgraph

### 概念

**子图（Subgraph）** 是 LangGraph 中将一个图作为另一个图的节点使用的机制。子图本身是一个完整的 StateGraph，有自己的 State、节点、边。通过子图，可以构建层级化的复杂工作流——主图调用子图，子图内部有自己的流程。

子图的价值在于：模块化（把复杂流程封装为子图）、复用（同一子图可在多处使用）、隔离（子图有独立 State）、可读性（主图更简洁）。

### 原理

**子图作为节点原理**：在 LangGraph 中，编译后的图（CompiledGraph）本身就是一个可调用对象，可以直接作为节点添加到另一个图。主图执行到子图节点时，会调用子图，子图完整执行后返回状态更新。

**State 共享原理**：子图与主图的 State 关系有三种模式：
1. **共享 State**：子图使用与主图相同的 State schema，直接读写主图状态
2. **独立 State**：子图有自己的 State，主图传入部分字段，子图返回部分字段
3. **混合 State**：子图继承主图 State，并扩展自己的字段

**子图嵌套原理**：子图可以嵌套——一个子图内部还可以有子图。这种层级结构能表达非常复杂的工作流。比如主图 → 研究子图 → 搜索子图 → 工具调用子图。

**子图编译原理**：子图必须先 `compile()` 才能作为节点使用。编译后的子图是不可变的，可以被多次实例化（作为不同节点）。

### 例子

**示例1：基础子图——研究子图**

```python
from typing import TypedDict
from langgraph.graph import StateGraph, START, END

# 主图 State
class MainState(TypedDict):
    topic: str
    research_results: str
    final_report: str

# 子图 State（独立）
class ResearchSubState(TypedDict):
    query: str
    findings: str

# 子图节点
def search_node(state: ResearchSubState):
    return {"findings": f"搜索 {state['query']} 的结果"}

def analyze_node(state: ResearchSubState):
    return {"findings": f"分析：{state['findings']}"}

# 构建子图
sub_builder = StateGraph(ResearchSubState)
sub_builder.add_node("search", search_node)
sub_builder.add_node("analyze", analyze_node)
sub_builder.add_edge(START, "search")
sub_builder.add_edge("search", "analyze")
sub_builder.add_edge("analyze", END)
research_subgraph = sub_builder.compile()

# 主图节点：调用子图
def research_wrapper(state: MainState):
    """调用研究子图"""
    # 传入子图需要的字段
    sub_input = {"query": state["topic"]}
    sub_result = research_subgraph.invoke(sub_input)
    # 把子图结果写回主图
    return {"research_results": sub_result["findings"]}

def write_report(state: MainState):
    return {"final_report": f"报告：{state['research_results']}"}

# 主图
main_builder = StateGraph(MainState)
main_builder.add_node("research", research_wrapper)  # 子图作为节点
main_builder.add_node("writer", write_report)
main_builder.add_edge(START, "research")
main_builder.add_edge("research", "writer")
main_builder.add_edge("writer", END)

main_graph = main_builder.compile()
result = main_graph.invoke({"topic": "LangGraph", "research_results": "", "final_report": ""})
print(result["final_report"])
```

**示例2：共享 State 的子图**

```python
# 子图直接使用主图的 State
class SharedState(TypedDict):
    messages: list
    summary: str

# 子图节点直接读写共享 State
def summarize_node(state: SharedState):
    # 直接读取主图的 messages
    msgs = state["messages"]
    return {"summary": f"摘要了 {len(msgs)} 条消息"}

# 子图
sub_builder = StateGraph(SharedState)
sub_builder.add_node("summarizer", summarize_node)
sub_builder.add_edge(START, "summarizer")
sub_builder.add_edge("summarizer", END)
summarize_subgraph = sub_builder.compile()

# 主图直接用子图作为节点（无需 wrapper）
main_builder = StateGraph(SharedState)
main_builder.add_node("summarize", summarize_subgraph)  # 直接作为节点
main_builder.add_edge(START, "summarize")
main_builder.add_edge("summarize", END)
```

**示例3：子图嵌套**

```python
# 最内层子图：工具调用
def make_tool_subgraph():
    sub = StateGraph(dict)
    sub.add_node("call", lambda s: {"result": "tool result"})
    sub.add_edge(START, "call")
    sub.add_edge("call", END)
    return sub.compile()

# 中间子图：研究流程（包含工具子图）
def make_research_subgraph():
    sub = StateGraph(dict)
    sub.add_node("tool", make_tool_subgraph())  # 嵌套子图
    sub.add_node("analyze", lambda s: {"analysis": "analyzed"})
    sub.add_edge(START, "tool")
    sub.add_edge("tool", "analyze")
    sub.add_edge("analyze", END)
    return sub.compile()

# 主图
main = StateGraph(dict)
main.add_node("research", make_research_subgraph())  # 嵌套
main.add_edge(START, "research")
main.add_edge("research", END)
main_graph = main.compile()
```

**示例4：可复用的子图工厂**

```python
def make_search_agent_subgraph(llm, tools):
    """创建可复用的搜索 Agent 子图"""
    from langgraph.prebuilt import create_react_agent
    return create_react_agent(model=llm, tools=tools)

# 在多处使用
research_sub = make_search_agent_subgraph(llm, [search_tool])
fact_check_sub = make_search_agent_subgraph(llm, [search_tool, verify_tool])

main_builder = StateGraph(dict)
main_builder.add_node("researcher", research_sub)
main_builder.add_node("fact_checker", fact_check_sub)
```

### 总结

- **子图**：一个图作为另一个图的节点，实现层级化工作流
- **State 模式**：共享、独立、混合三种
- **嵌套**：子图可嵌套，表达复杂层级
- **模块化**：把复杂流程封装为子图，提升可读性和复用性
- **编译必需**：子图必须先 compile 才能作为节点
- **复用**：通过工厂函数创建可复用子图

---

## 第23讲：Command 命令与跳转

### 概念

**Command** 是 LangGraph 提供的一个特殊返回类型，让节点能够同时做三件事：更新状态、指定下一个节点、跳转到特定节点。这是对"节点返回 dict + 条件边"模式的增强，让控制流更灵活、更集中。

使用 Command，节点可以直接决定下一步去哪，而不需要单独定义条件边。这在需要"动态跳转"的场景非常有用——比如根据处理结果跳到不同节点，或在多 Agent 系统中跳到特定 Agent。

### 原理

**Command 的三个能力原理**：
1. **状态更新**：`Command(update={...})` 等价于返回 dict，更新 State
2. **指定下一个节点**：`Command(goto="node_name")` 指定下一步去哪
3. **跳转**：`Command(goto="node_name", update={...})` 同时更新状态并跳转

**与条件边对比原理**：条件边需要单独定义路由函数和路径映射，逻辑分散在节点和边两处。Command 把"更新状态"和"决定下一步"集中在节点内部，逻辑更内聚。但 Command 适合简单跳转，复杂路由还是用条件边更清晰。

**动态跳转原理**：Command 的 `goto` 可以是字符串（节点名）或字符串列表（并行跳转）。这让节点能根据运行时状态动态决定跳转目标，比条件边的"预定义路径映射"更灵活。

**Command 与 interrupt 原理**：Command 还支持 `interrupt` 参数，用于人机交互（下一章详解）。节点可以返回 `Command(interrupt=...)` 暂停执行，等待人工干预后继续。

### 例子

**示例1：基础 Command 跳转**

```python
from typing import TypedDict
from langgraph.graph import StateGraph, START, END
from langgraph.types import Command

class State(TypedDict):
    value: int
    path: str

def start_node(state: State) -> Command:
    """用 Command 决定下一步"""
    if state["value"] > 10:
        return Command(goto="high_node", update={"path": "high"})
    else:
        return Command(goto="low_node", update={"path": "low"})

def high_node(state: State):
    return {"value": state["value"] * 2}

def low_node(state: State):
    return {"value": state["value"] + 1}

builder = StateGraph(State)
builder.add_node("start", start_node)
builder.add_node("high", high_node)
builder.add_node("low", low_node)

builder.add_edge(START, "start")
# 不需要条件边，start_node 用 Command 决定跳转
builder.add_edge("high", END)
builder.add_edge("low", END)

graph = builder.compile()

# 测试
result1 = graph.invoke({"value": 15, "path": ""})
print(result1)  # value=30, path=high

result2 = graph.invoke({"value": 5, "path": ""})
print(result2)  # value=6, path=low
```

**示例2：Command 并行跳转**

```python
def dispatcher_node(state: State) -> Command:
    """同时跳转到多个节点（并行）"""
    return Command(
        goto=["node_a", "node_b", "node_c"],  # 并行跳转
        update={"dispatched": True}
    )
```

**示例3：Command 在多 Agent 系统中的应用**

```python
from typing import TypedDict, Annotated
from langgraph.graph import StateGraph, START, END
from langgraph.graph.message import add_messages
from langgraph.types import Command
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4o-mini")

class TeamState(TypedDict):
    messages: Annotated[list, add_messages]
    next_agent: str

def supervisor(state: TeamState) -> Command:
    """Supervisor 用 Command 决定调用哪个子 Agent"""
    last_msg = state["messages"][-1].content
    
    # 用 LLM 决策
    prompt = f"""根据请求决定调用哪个 Agent（researcher/writer/FINISH）：
{last_msg}
只输出一个词。"""
    decision = llm.invoke(prompt).content.strip()
    
    if decision == "FINISH":
        return Command(goto=END)
    return Command(goto=decision)  # 跳到对应子 Agent

def researcher(state: TeamState):
    response = llm.invoke(f"作为研究员回答：{state['messages'][-1].content}")
    return {"messages": [response]}

def writer(state: TeamState):
    response = llm.invoke(f"作为写作者回答：{state['messages'][-1].content}")
    return {"messages": [response]}

builder = StateGraph(TeamState)
builder.add_node("supervisor", supervisor)
builder.add_node("researcher", researcher)
builder.add_node("writer", writer)

builder.add_edge(START, "supervisor")
# supervisor 用 Command 跳转，不需要条件边
builder.add_edge("researcher", "supervisor")  # 子 Agent 完成后回到 supervisor
builder.add_edge("writer", "supervisor")

graph = builder.compile()
```

**示例4：Command 与状态更新结合**

```python
def process_node(state) -> Command:
    """处理数据并决定下一步"""
    data = state["data"]
    
    # 处理
    if data["type"] == "urgent":
        result = "紧急处理"
        next_step = "urgent_handler"
    elif data["type"] == "normal":
        result = "常规处理"
        next_step = "normal_handler"
    else:
        result = "未知类型"
        next_step = "error_handler"
    
    # 同时更新状态和跳转
    return Command(
        update={
            "process_result": result,
            "process_time": "2024-01-01"
        },
        goto=next_step
    )
```

**示例5：Command 用于循环控制**

```python
def agent_node(state) -> Command:
    """Agent 节点用 Command 控制循环"""
    last_msg = state["messages"][-1]
    
    if hasattr(last_msg, "tool_calls") and last_msg.tool_calls:
        # 有工具调用，跳到 tools 节点
        return Command(goto="tools")
    else:
        # 没有工具调用，结束
        return Command(goto=END)
```

### 总结

- **Command 三能力**：更新状态、指定下一个节点、跳转
- **与条件边对比**：Command 逻辑内聚在节点，条件边逻辑分散但清晰
- **动态跳转**：goto 支持字符串或列表（并行跳转）
- **适用场景**：简单跳转、多 Agent 路由、循环控制
- **复杂路由**：仍建议用条件边，更易维护
- **interrupt**：Command 支持 interrupt 参数，用于人机交互（下一章）

---

# 第7章 人机交互与流式

本章探讨 LangGraph 的两大生产级特性——人机交互（Human-in-the-loop）和流式输出（Streaming）。人机交互让 Agent 在关键节点暂停等待人工干预，流式输出让用户实时看到 Agent 的思考过程。学完本章，你将能够构建更智能、更友好的 Agent 应用。

---

## 第24讲：Human-in-the-loop 人机交互

### 概念

**Human-in-the-loop（HITL，人机交互）** 是指在 Agent 执行过程中，于关键节点暂停，等待人工干预（确认、修改、补充信息）后继续执行的机制。HITL 是构建可靠 Agent 的关键——对于高风险操作（如发送邮件、执行交易、删除数据），人工审核能避免 AI 的错误决策造成不可逆后果。

LangGraph 通过 **interrupt** 机制实现 HITL。节点可以调用 `interrupt()` 暂停执行，等待人工输入，然后从暂停点恢复执行。

### 原理

**interrupt 工作原理**：
1. 节点执行中调用 `interrupt(value)`，传入需要人工审核的值
2. LangGraph 暂停图执行，保存当前状态（Checkpoint）
3. 返回 interrupt 的 value 给调用方（前端/CLI）
4. 调用方收集人工输入，调用 `Command(resume=human_input)` 恢复执行
5. `interrupt()` 函数返回人工输入的值，节点继续执行

**Checkpoint 依赖原理**：HITL 必须配合 Checkpointer 使用——暂停时需要保存状态，恢复时需要加载状态。没有 Checkpointer，interrupt 无法工作。常用 `MemorySaver`（内存）或 `SqliteSaver`/`PostgresSaver`（持久化）。

**interrupt 的位置原理**：interrupt 可以在节点的任意位置调用，但通常放在"决策点"——比如工具调用前、最终回复前、关键操作前。interrupt 的 value 是要给人工看的"待审核内容"。

**恢复机制原理**：恢复时调用 `graph.invoke(Command(resume=value), config)`，LangGraph 加载 Checkpoint，从 interrupt 点继续执行，`interrupt()` 函数返回 resume 的 value。这种"暂停-恢复"机制让 Agent 能无缝集成人工干预。

### 例子

**示例1：基础 HITL——工具调用前确认**

```python
from typing import TypedDict, Annotated
from langgraph.graph import StateGraph, START, END
from langgraph.graph.message import add_messages
from langgraph.checkpoint.memory import MemorySaver
from langgraph.types import interrupt, Command
from langchain_core.tools import tool
from langchain_openai import ChatOpenAI
from langchain_core.messages import HumanMessage
from langgraph.prebuilt import ToolNode

@tool
def send_email(to: str, subject: str, body: str) -> str:
    """发送邮件"""
    # 模拟发送
    return f"邮件已发送到 {to}"

@tool
def delete_file(path: str) -> str:
    """删除文件"""
    return f"已删除 {path}"

tools = [send_email, delete_file]
llm = ChatOpenAI(model="gpt-4o-mini").bind_tools(tools)

class State(TypedDict):
    messages: Annotated[list, add_messages]

def agent_node(state):
    response = llm.invoke(state["messages"])
    return {"messages": [response]}

def human_approval_tool_node(state):
    """带人工确认的工具节点"""
    last_msg = state["messages"][-1]
    
    if not hasattr(last_msg, "tool_calls") or not last_msg.tool_calls:
        return {"messages": []}
    
    # 对每个工具调用请求人工确认
    from langchain_core.messages import ToolMessage
    tool_messages = []
    
    for tc in last_msg.tool_calls:
        # interrupt 暂停，等待人工确认
        approval = interrupt({
            "tool": tc["name"],
            "args": tc["args"],
            "question": f"是否允许执行 {tc['name']}？(yes/no)"
        })
        
        if approval.lower() == "yes":
            # 执行工具
            tool = next(t for t in tools if t.name == tc["name"])
            result = tool.invoke(tc["args"])
            tool_messages.append(ToolMessage(
                content=result,
                tool_call_id=tc["id"]
            ))
        else:
            tool_messages.append(ToolMessage(
                content=f"用户拒绝了 {tc['name']} 调用",
                tool_call_id=tc["id"]
            ))
    
    return {"messages": tool_messages}

def should_continue(state):
    last_msg = state["messages"][-1]
    if hasattr(last_msg, "tool_calls") and last_msg.tool_calls:
        return "tools"
    return END

# 构建图
builder = StateGraph(State)
builder.add_node("agent", agent_node)
builder.add_node("tools", human_approval_tool_node)

builder.add_edge(START, "agent")
builder.add_conditional_edges("agent", should_continue, {"tools": "tools", END: END})
builder.add_edge("tools", "agent")

# 必须用 checkpointer 支持 interrupt
checkpointer = MemorySaver()
graph = builder.compile(checkpointer=checkpointer)

# 使用
config = {"configurable": {"thread_id": "thread_1"}}

# 第一次调用，会在工具确认处暂停
result = graph.invoke(
    {"messages": [HumanMessage(content="发邮件给 alice@example.com，主题是问候，内容是你好")]},
    config=config
)
# 此时图暂停，result 包含 interrupt 信息

# 人工确认
user_approval = input("是否允许？(yes/no): ")

# 恢复执行
result = graph.invoke(
    Command(resume=user_approval),
    config=config  # 同一个 thread_id
)
print(result["messages"][-1].content)
```

**示例2：内容审核 HITL**

```python
def content_review_node(state):
    """生成内容后，人工审核"""
    # LLM 生成内容
    draft = llm.invoke(f"写一封{state['email_type']}邮件").content
    
    # 暂停，请求人工审核
    approved_content = interrupt({
        "draft": draft,
        "question": "请审核邮件内容，可修改后返回"
    })
    
    # 使用审核后的内容
    return {"final_content": approved_content}
```

**示例3：多轮 HITL**

```python
def collect_info_node(state):
    """多轮收集信息"""
    info = {}
    
    # 第一轮：收集姓名
    name = interrupt({"question": "请输入姓名"})
    info["name"] = name
    
    # 第二轮：收集邮箱
    email = interrupt({"question": "请输入邮箱"})
    info["email"] = email
    
    return {"user_info": info}

# 恢复时需要多次 invoke
config = {"configurable": {"thread_id": "t1"}}

# 第一次：暂停在收集姓名
graph.invoke({"messages": [...]}, config=config)
# 用户提供姓名，恢复
graph.invoke(Command(resume="张三"), config=config)
# 暂停在收集邮箱
# 用户提供邮箱，恢复
graph.invoke(Command(resume="zhangsan@example.com"), config=config)
# 完成
```

### 总结

- **HITL**：Agent 在关键节点暂停，等待人工干预后继续
- **interrupt 机制**：节点调用 `interrupt(value)` 暂停，`Command(resume=...)` 恢复
- **Checkpoint 依赖**：必须配合 Checkpointer 使用
- **适用场景**：高风险操作前确认、内容审核、信息收集
- **多轮 HITL**：一个节点可多次 interrupt，多次恢复
- **生产价值**：避免 AI 错误决策，提升可靠性

---

## 第25讲：流式输出 Streaming

### 概念

**流式输出（Streaming）** 是指 Agent 在执行过程中，逐步、实时地输出结果，而非等全部完成才返回。流式输出让用户能实时看到 Agent 的思考过程、工具调用、中间结果，大幅提升用户体验。

LangGraph 提供多种流式模式：`values`（完整状态）、`updates`（状态更新）、`messages`（消息流）、`custom`（自定义事件）。每种模式适合不同场景。

### 原理

**流式 vs 批量原理**：传统 `invoke` 是批量模式——等图完整执行才返回结果。`stream` 是流式模式——每完成一个节点或产生一个事件就 yield 出来。流式模式让用户能实时看到进展，避免长时间等待的焦虑。

**四种流式模式原理**：
1. **values**：每次节点执行后，yield 完整的当前 State。适合需要看状态演变的场景。
2. **updates**：每次节点执行后，yield 该节点的状态更新（diff）。适合看每个节点的产出。
3. **messages**：流式输出 LLM 的 token，逐字显示。适合聊天界面实时显示。
4. **custom**：节点内用 `get_stream_writer()` 发送自定义事件。适合细粒度进度展示。

**token 流式原理**：LLM 生成回复时，是逐 token（词元）生成的。LangGraph 的 `messages` 模式能捕获 LLM 的 token 流，实时推送给前端，实现"打字机"效果。这依赖 LLM API 的 streaming 支持。

**多节点流式原理**：图有多个节点时，stream 会按执行顺序依次 yield 每个节点的输出。前端可以根据节点名区分不同阶段的进展，如"搜索中..."→"分析中..."→"生成回复中..."。

### 例子

**示例1：values 模式——看状态演变**

```python
from langgraph.graph import StateGraph, START, END
from typing import TypedDict

class State(TypedDict):
    step: str
    value: int

def node_a(state):
    return {"step": "a", "value": 1}

def node_b(state):
    return {"step": "b", "value": 2}

def node_c(state):
    return {"step": "c", "value": 3}

builder = StateGraph(State)
builder.add_node("a", node_a)
builder.add_node("b", node_b)
builder.add_node("c", node_c)
builder.add_edge(START, "a")
builder.add_edge("a", "b")
builder.add_edge("b", "c")
builder.add_edge("c", END)

graph = builder.compile()

# values 模式：每次 yield 完整状态
print("=== values 模式 ===")
for event in graph.stream({"step": "", "value": 0}, stream_mode="values"):
    print(f"状态：{event}")
# 输出：
# 状态：{'step': '', 'value': 0}
# 状态：{'step': 'a', 'value': 1}
# 状态：{'step': 'b', 'value': 2}
# 状态：{'step': 'c', 'value': 3}
```

**示例2：updates 模式——看节点产出**

```python
print("=== updates 模式 ===")
for event in graph.stream({"step": "", "value": 0}, stream_mode="updates"):
    for node_name, update in event.items():
        print(f"[{node_name}] 更新：{update}")
# 输出：
# [a] 更新：{'step': 'a', 'value': 1}
# [b] 更新：{'step': 'b', 'value': 2}
# [c] 更新：{'step': 'c', 'value': 3}
```

**示例3：messages 模式——LLM token 流**

```python
from langchain_openai import ChatOpenAI
from langgraph.graph.message import MessagesState, add_messages
from langchain_core.messages import HumanMessage

llm = ChatOpenAI(model="gpt-4o-mini", streaming=True)

def chat_node(state):
    response = llm.invoke(state["messages"])
    return {"messages": [response]}

builder = StateGraph(MessagesState)
builder.add_node("chat", chat_node)
builder.add_edge(START, "chat")
builder.add_edge("chat", END)

graph = builder.compile()

# messages 模式：流式输出 LLM token
for msg, metadata in graph.stream(
    {"messages": [HumanMessage(content="用100字介绍 LangGraph")]},
    stream_mode="messages"
):
    if msg.content:
        print(msg.content, end="", flush=True)
# 逐字输出 LLM 的回复
```

**示例4：custom 模式——自定义事件**

```python
from langgraph.config import get_stream_writer

def research_node(state):
    writer = get_stream_writer()
    
    writer({"status": "开始搜索", "progress": 0})
    # 执行搜索...
    writer({"status": "搜索完成，开始分析", "progress": 50})
    # 执行分析...
    writer({"status": "分析完成", "progress": 100})
    
    return {"result": "研究完成"}

builder = StateGraph(dict)
builder.add_node("research", research_node)
builder.add_edge(START, "research")
builder.add_edge("research", END)
graph = builder.compile()

# 接收自定义事件
for event in graph.stream({}, stream_mode="custom"):
    print(f"进度：{event}")
# 输出：
# 进度：{'status': '开始搜索', 'progress': 0}
# 进度：{'status': '搜索完成，开始分析', 'progress': 50}
# 进度：{'status': '分析完成', 'progress': 100}
```

**示例5：多模式组合**

```python
# 同时订阅多种流
for event in graph.stream(
    {"messages": [HumanMessage(content="你好")]},
    stream_mode=["values", "messages"]  # 多模式
):
    mode, data = event
    if mode == "values":
        print(f"\n[状态] {data}")
    elif mode == "messages":
        msg, meta = data
        if msg.content:
            print(msg.content, end="")
```

### 总结

- **流式 vs 批量**：stream 实时输出，invoke 批量返回
- **四种模式**：values（完整状态）、updates（节点更新）、messages（token流）、custom（自定义）
- **messages 模式**：实现"打字机"效果，需 LLM 支持 streaming
- **custom 模式**：用 `get_stream_writer()` 发送自定义事件，细粒度进度
- **多模式组合**：可同时订阅多种流，满足复杂前端需求
- **用户体验**：流式输出大幅提升体验，避免长时间等待焦虑

---

## 第26讲：中断与恢复

### 概念

**中断与恢复（Interrupt & Resume）** 是 LangGraph 的状态管理能力——在图执行过程中暂停（中断），保存当前状态，之后从暂停点继续执行（恢复）。这与第24讲的 HITL 相关，但更广泛——包括人工干预、定时恢复、错误恢复等场景。

本讲深入 interrupt 的进阶用法，包括：多节点中断、状态保存与加载、长时间运行任务的恢复、错误处理与重试。

### 原理

**中断的状态保存原理**：当图执行到 interrupt 点时，LangGraph 调用 Checkpointer 保存当前完整状态（所有 Channel 的值、当前节点、执行位置）。这个保存的状态称为 Checkpoint。恢复时，加载 Checkpoint，从 interrupt 点继续。

**thread_id 原理**：每个图执行实例用一个唯一的 `thread_id` 标识。Checkpointer 按 thread_id 保存和加载状态。同一个 thread_id 的多次 invoke 会共享状态，实现"中断-恢复"。不同 thread_id 是独立的执行实例。

**恢复的精确性原理**：恢复时，LangGraph 不仅恢复状态，还恢复执行位置——从 interrupt 调用点继续，`interrupt()` 函数返回 resume 的值。这种精确恢复让 Agent 能无缝继续之前的工作。

**长时间任务原理**：对于长时间运行的任务（如等待外部事件、定时触发），可以用 interrupt 暂停，保存状态，等条件满足时恢复。这比让进程一直运行更高效。

**错误恢复原理**：节点执行出错时，可以保存状态，人工或自动处理后恢复。这比直接失败更健壮——保留了之前的进展，避免从头开始。

### 例子

**示例1：多节点中断与恢复**

```python
from typing import TypedDict
from langgraph.graph import StateGraph, START, END
from langgraph.checkpoint.memory import MemorySaver
from langgraph.types import interrupt, Command

class State(TypedDict):
    step: str
    user_inputs: list

def step1_node(state):
    # 第一个 interrupt
    name = interrupt({"question": "请输入姓名"})
    return {"step": "step1_done", "user_inputs": [name]}

def step2_node(state):
    # 第二个 interrupt
    email = interrupt({"question": "请输入邮箱"})
    return {"step": "step2_done", "user_inputs": state["user_inputs"] + [email]}

def step3_node(state):
    return {"step": "all_done"}

builder = StateGraph(State)
builder.add_node("step1", step1_node)
builder.add_node("step2", step2_node)
builder.add_node("step3", step3_node)
builder.add_edge(START, "step1")
builder.add_edge("step1", "step2")
builder.add_edge("step2", "step3")
builder.add_edge("step3", END)

checkpointer = MemorySaver()
graph = builder.compile(checkpointer=checkpointer)

config = {"configurable": {"thread_id": "user_123"}}

# 第一次 invoke：在 step1 的 interrupt 暂停
result = graph.invoke({"step": "", "user_inputs": []}, config=config)
print("暂停在：", result.get("step"))

# 恢复：提供姓名
result = graph.invoke(Command(resume="张三"), config=config)
print("暂停在：", result.get("step"))

# 恢复：提供邮箱
result = graph.invoke(Command(resume="zhangsan@example.com"), config=config)
print("最终：", result)
print("收集的信息：", result["user_inputs"])
```

**示例2：查看中断状态**

```python
# 获取当前状态（不恢复）
current_state = graph.get_state(config)
print("当前节点：", current_state.next)
print("当前状态：", current_state.values)
print("是否有未恢复的 interrupt：", current_state.tasks)

# 查看历史状态
for state in graph.get_state_history(config):
    print(f"步骤：{state.next}, 状态：{state.values}")
```

**示例3：错误恢复**

```python
def risky_node(state):
    try:
        # 可能失败的操作
        result = risky_operation(state["input"])
        return {"result": result}
    except Exception as e:
        # 出错时 interrupt，请求人工处理
        human_help = interrupt({
            "error": str(e),
            "question": "操作失败，请提供处理建议或重试"
        })
        # 基于人工建议继续
        return {"result": f"人工处理：{human_help}"}

# 使用
config = {"configurable": {"thread_id": "error_1"}}
result = graph.invoke({"input": "data"}, config=config)
# 出错暂停
result = graph.invoke(Command(resume="跳过此步"), config=config)
# 恢复继续
```

**示例4：定时恢复（长时间任务）**

```python
import time

def wait_for_event_node(state):
    """等待外部事件"""
    # interrupt 暂停，等待外部事件触发
    event_data = interrupt({"waiting_for": "external_event"})
    return {"event_data": event_data}

# 第一次 invoke，暂停等待
graph.invoke({"input": "start"}, config=config)

# ... 一段时间后，外部事件发生 ...
time.sleep(60)

# 恢复，传入事件数据
result = graph.invoke(
    Command(resume={"event": "triggered", "data": "..."}),
    config=config
)
```

**示例5：重置与回滚**

```python
# 获取状态历史
states = list(graph.get_state_history(config))

# 回滚到某个历史状态
old_state = states[3]  # 第3个状态
graph.update_state(config, old_state.values, as_node=old_state.next[0])

# 从该状态继续执行
result = graph.invoke(None, config=config)  # None 表示从当前状态继续
```

### 总结

- **中断与恢复**：暂停保存状态，之后从暂停点继续
- **thread_id**：标识执行实例，同 thread_id 共享状态
- **精确恢复**：不仅恢复状态，还恢复执行位置
- **多节点中断**：一个图可有多个 interrupt 点，依次恢复
- **错误恢复**：节点出错可 interrupt 请求人工处理，避免直接失败
- **状态管理**：`get_state`、`get_state_history`、`update_state` 管理执行状态

---

# 第8章 持久化与记忆

本章探讨 LangGraph 的持久化能力——让 Agent 拥有"记忆"。包括 Checkpoint 持久化（保存执行状态）、Memory Store（长期记忆）、跨会话记忆管理。学完本章，你将能够构建具备长期记忆的 Agent，让它在不同会话间保持上下文。

---

## 第27讲：Checkpoint 持久化

### 概念

**Checkpoint（检查点）** 是 LangGraph 保存图执行状态的机制。在图执行过程中，Checkpoint 会定期保存当前状态（所有 Channel 的值、当前节点等），使得即使进程崩溃或主动暂停，也能从 Checkpoint 恢复执行。

**Checkpointer** 是实现 Checkpoint 机制的组件。LangGraph 提供多种 Checkpointer：`MemorySaver`（内存，开发用）、`SqliteSaver`（SQLite，轻量生产）、`PostgresSaver`（PostgreSQL，生产级）。

### 原理

**Checkpoint 工作原理**：每次图执行时，Checkpointer 在以下时机保存状态：
1. 图开始执行时
2. 每个节点执行完毕后
3. 图执行结束或中断时

每个 Checkpoint 包含：当前 State 的完整快照、当前执行位置（next 节点）、配置信息（thread_id 等）、时间戳。

**thread_id 与隔离原理**：Checkpoint 按 `thread_id` 隔离——不同 thread_id 的执行互不影响。同一个 thread_id 的多次 invoke 会共享状态，实现多轮对话、中断恢复等。thread_id 通常对应用户 ID 或会话 ID。

**Checkpointer 选型原理**：
- **MemorySaver**：状态存内存，进程结束就丢失。适合开发测试。
- **SqliteSaver**：状态存 SQLite 文件，轻量持久化。适合单机生产。
- **PostgresSaver**：状态存 PostgreSQL，支持高并发、备份。适合生产级。
- **RedisSaver**：状态存 Redis，高速读写。适合高并发场景。

**Checkpoint 与 interrupt 原理**：interrupt 依赖 Checkpoint——暂停时保存状态，恢复时加载状态。没有 Checkpointer，interrupt 无法工作。这是为什么第24讲的 HITL 示例都用了 MemorySaver。

**历史记录原理**：Checkpointer 不仅保存最新状态，还保存历史状态。通过 `get_state_history` 可以查看执行过程中的所有状态快照，支持回滚到任意历史点。

### 例子

**示例1：MemorySaver（开发用）**

```python
from langgraph.checkpoint.memory import MemorySaver
from langgraph.graph import StateGraph, START, END
from typing import TypedDict, Annotated
from langgraph.graph.message import add_messages
from langchain_core.messages import HumanMessage, AIMessage

class State(TypedDict):
    messages: Annotated[list, add_messages]

def chat_node(state):
    # 模拟 AI 回复
    user_msg = state["messages"][-1].content
    return {"messages": [AIMessage(content=f"回复：{user_msg}")]}

builder = StateGraph(State)
builder.add_node("chat", chat_node)
builder.add_edge(START, "chat")
builder.add_edge("chat", END)

# 用 MemorySaver
checkpointer = MemorySaver()
graph = builder.compile(checkpointer=checkpointer)

# 多轮对话（同 thread_id 共享状态）
config = {"configurable": {"thread_id": "user_1"}}

# 第一轮
result = graph.invoke(
    {"messages": [HumanMessage(content="你好")]},
    config=config
)
print(result["messages"][-1].content)  # 回复：你好

# 第二轮（带历史）
result = graph.invoke(
    {"messages": [HumanMessage(content="我刚才说了什么？")]},
    config=config  # 同一个 thread_id
)
print(result["messages"][-1].content)  # 能回忆起"你好"

# 不同 thread_id 是独立会话
config2 = {"configurable": {"thread_id": "user_2"}}
result = graph.invoke(
    {"messages": [HumanMessage(content="我刚才说了什么？")]},
    config=config2
)
# user_2 不知道 user_1 的对话
```

**示例2：SqliteSaver（持久化）**

```python
from langgraph.checkpoint.sqlite import SqliteSaver
import sqlite3

# 创建 SQLite 连接
conn = sqlite3.connect("checkpoints.db", check_same_thread=False)

# 用 SqliteSaver
checkpointer = SqliteSaver(conn)
graph = builder.compile(checkpointer=checkpointer)

# 现在状态会持久化到 checkpoints.db 文件
# 即使进程重启，状态依然保留
```

**示例3：查看状态历史**

```python
config = {"configurable": {"thread_id": "user_1"}}

# 获取当前状态
current_state = graph.get_state(config)
print("当前状态：", current_state.values)
print("下一步：", current_state.next)

# 获取状态历史
print("\n=== 状态历史 ===")
for state in graph.get_state_history(config):
    print(f"时间：{state.config['configurable']['checkpoint_id']}")
    print(f"  节点：{state.next}")
    print(f"  消息数：{len(state.values.get('messages', []))}")
```

**示例4：回滚到历史状态**

```python
# 获取历史状态列表
history = list(graph.get_state_history(config))

# 回滚到第3个状态
target_state = history[3]
graph.update_state(
    config,
    target_state.values,
    as_node=target_state.next[0] if target_state.next else None
)

# 从回滚点继续执行
result = graph.invoke(None, config=config)
```

**示例5：生产级 PostgresSaver**

```python
from langgraph.checkpoint.postgres import PostgresSaver
from psycopg import connect

# 生产环境用连接池
DB_URI = "postgresql://user:password@localhost:5432/langgraph"
conn = connect(DB_URI)

checkpointer = PostgresSaver(conn)
# 自动创建表
checkpointer.setup()

graph = builder.compile(checkpointer=checkpointer)
```

### 总结

- **Checkpoint**：保存图执行状态，支持恢复
- **Checkpointer 选型**：MemorySaver（开发）、SqliteSaver（轻量生产）、PostgresSaver（生产级）
- **thread_id 隔离**：不同会话独立，同会话共享状态
- **历史记录**：保存所有状态快照，支持回滚
- **interrupt 依赖**：HITL 必须配合 Checkpointer
- **生产建议**：用 PostgresSaver 或 RedisSaver，定期备份

---

## 第28讲：Memory Store 长期记忆

### 概念

**Memory Store（记忆存储）** 是 LangGraph 提供的跨会话长期记忆机制。与 Checkpoint（保存单次执行状态）不同，Memory Store 保存的是"知识"——用户偏好、事实信息、历史摘要等，可以在不同会话间共享。

比如用户在第一次对话中提到"我喜欢 Python"，Memory Store 会保存这个偏好，下次对话时 Agent 能"记得"这个信息，即使是一个全新的会话（不同 thread_id）。

### 原理

**Memory Store vs Checkpoint 原理**：
- **Checkpoint**：保存执行状态，按 thread_id 隔离，用于中断恢复、多轮对话
- **Memory Store**：保存知识/偏好，按 namespace 组织，用于跨会话共享

Checkpoint 是"短期记忆"（会话内），Memory Store 是"长期记忆"（跨会话）。

**Namespace 组织原理**：Memory Store 用 namespace（命名空间）组织记忆。常见模式是按用户 ID 组织：`("user", user_id)`。同一用户的所有记忆在同一个 namespace 下，不同用户隔离。

**记忆的存储原理**：Memory Store 存储的是 `Item` 对象，包含：
- `key`：记忆的唯一标识
- `value`：记忆内容（字典）
- `namespace`：所属命名空间

**记忆的检索原理**：Agent 可以从 Memory Store 检索记忆——按 key 精确查找，或语义搜索（如果配置了向量检索）。检索到的记忆作为上下文加入 LLM 调用，让 Agent "记得"用户信息。

**写入时机原理**：记忆通常在对话过程中写入——Agent 判断某信息值得记住（如用户偏好、重要事实），主动写入 Memory Store。也可以用专门的"记忆节点"在每轮对话后提取并保存关键信息。

### 例子

**示例1：基础 Memory Store**

```python
from langgraph.store.memory import InMemoryStore
from langgraph.graph import StateGraph, START, END
from langgraph.graph.message import MessagesState, add_messages
from langchain_openai import ChatOpenAI
from langchain_core.messages import HumanMessage, SystemMessage

# 创建 Memory Store
store = InMemoryStore()

llm = ChatOpenAI(model="gpt-4o-mini")

def chat_with_memory(state: MessagesState, *, store=store, user_id="user_1"):
    """带长期记忆的对话节点"""
    # 从 store 检索用户记忆
    memories = store.search(("user", user_id), query=state["messages"][-1].content)
    
    # 构造记忆上下文
    memory_context = ""
    if memories:
        memory_context = "\n\n用户记忆：\n" + "\n".join(
            [f"- {m.value['content']}" for m in memories]
        )
    
    # 构造系统提示
    system_msg = SystemMessage(content=f"你是友好助手。{memory_context}")
    
    # 调用 LLM
    messages = [system_msg] + state["messages"]
    response = llm.invoke(messages)
    
    return {"messages": [response]}

def save_memory_node(state: MessagesState, *, store=store, user_id="user_1"):
    """从对话中提取并保存记忆"""
    last_user_msg = state["messages"][-1].content if state["messages"] else ""
    
    # 用 LLM 判断是否有值得记住的信息
    prompt = f"""从以下用户消息中提取值得长期记住的信息（如偏好、事实）。
如果没有，回复"无"。格式：key|value（每行一条）
用户消息：{last_user_msg}"""
    
    extraction = llm.invoke(prompt).content
    
    if extraction != "无":
        for line in extraction.split("\n"):
            if "|" in line:
                key, value = line.split("|", 1)
                store.put(
                    ("user", user_id),
                    key=key.strip(),
                    value={"content": value.strip()}
                )
    
    return {}

# 构建图
builder = StateGraph(MessagesState)
builder.add_node("chat", chat_with_memory)
builder.add_node("save_memory", save_memory_node)
builder.add_edge(START, "chat")
builder.add_edge("chat", "save_memory")
builder.add_edge("save_memory", END)

graph = builder.compile()

# 第一轮对话
result = graph.invoke({
    "messages": [HumanMessage(content="我喜欢用 Python 编程，特别是数据科学方向")]
})
print(result["messages"][-1].content)

# 查看存储的记忆
print("\n存储的记忆：")
for item in store.search(("user", "user_1")):
    print(f"  {item.key}: {item.value}")

# 第二轮对话（新会话，但能"记得"）
result = graph.invoke({
    "messages": [HumanMessage(content="给我推荐学习资源")]
})
print(result["messages"][-1].content)
# Agent 会基于"喜欢 Python 数据科学"的偏好推荐
```

**示例2：带语义搜索的记忆**

```python
from langgraph.store.memory import InMemoryStore
from langchain_openai import OpenAIEmbeddings

# 创建带向量检索的 Store
store = InMemoryStore(
    index={
        "embed": OpenAIEmbeddings(),
        "dims": 1536
    }
)

# 写入记忆
store.put(("user", "1"), "pref_1", {"content": "喜欢 Python 数据科学"})
store.put(("user", "1"), "pref_2", {"content": "在学 LangGraph"})
store.put(("user", "1"), "fact_1", {"content": "住在北京"})

# 语义搜索
results = store.search(("user", "1"), query="编程偏好", limit=2)
for r in results:
    print(r.value)  # 最相关的记忆
```

**示例3：持久化 Store**

```python
from langgraph.store.postgres import PostgresStore

# 生产环境用 PostgresStore
store = PostgresStore.from_conn_string(
    "postgresql://user:pass@localhost:5432/langgraph"
)
store.setup()  # 创建表

# 记忆持久化到数据库，进程重启不丢失
```

### 总结

- **Memory Store**：跨会话长期记忆，保存知识/偏好
- **vs Checkpoint**：Checkpoint 是短期记忆（会话内），Store 是长期记忆（跨会话）
- **Namespace 组织**：按用户 ID 等组织，不同用户隔离
- **写入时机**：对话中提取关键信息主动写入
- **检索方式**：按 key 精确查找或语义搜索
- **生产化**：用 PostgresStore 持久化，配合向量检索

---

## 第29讲：跨会话记忆管理

### 概念

**跨会话记忆管理** 是指 Agent 在不同会话（不同 thread_id）间共享和管理记忆的能力。这包括：记忆的提取与保存、记忆的检索与使用、记忆的更新与遗忘、记忆的组织与索引。

一个具备跨会话记忆的 Agent 能"认识"老用户——知道他们的偏好、历史、习惯，提供个性化服务。这是 Agent 从"工具"走向"助手"的关键能力。

### 原理

**记忆管理流程原理**：完整的跨会话记忆管理包括四个环节：
1. **提取**：从对话中识别值得记住的信息
2. **保存**：写入 Memory Store，组织到合适 namespace
3. **检索**：新会话开始时，检索相关记忆作为上下文
4. **更新/遗忘**：旧记忆过时则更新，不重要则遗忘

**记忆提取原理**：提取是关键——不是所有对话都值得记住。常见策略：
- **LLM 提取**：用 LLM 判断并提取关键信息
- **规则提取**：基于关键词、实体识别提取
- **混合提取**：规则 + LLM，规则快速过滤，LLM 精细提取

**记忆检索原理**：新会话开始时，Agent 从 Store 检索相关记忆：
- **全量检索**：取出用户所有记忆（适合记忆少时）
- **语义检索**：根据当前问题语义检索相关记忆
- **最近优先**：优先检索最近写入的记忆

**记忆更新原理**：用户偏好可能变化——之前喜欢 Python，现在转 Go。记忆更新策略：
- **覆盖**：同 key 的新值覆盖旧值
- **版本化**：保留历史版本，标注时间
- **合并**：新旧值合并（如列表追加）

**记忆遗忘原理**：记忆过多会稀释有用信息，需要遗忘：
- **时间遗忘**：超过一定时间未访问的记忆删除
- **重要性遗忘**：低重要性记忆优先删除
- **用户主动**：用户可主动删除某记忆

### 例子

**完整示例：具备跨会话记忆的 Agent**

```python
import os
from dotenv import load_dotenv
from langchain_openai import ChatOpenAI
from langchain_core.messages import HumanMessage, SystemMessage
from langgraph.graph import StateGraph, START, END
from langgraph.graph.message import MessagesState
from langgraph.store.memory import InMemoryStore

load_dotenv()
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)
store = InMemoryStore()

class AgentState(MessagesState):
    user_id: str

def load_memories_node(state: AgentState):
    """会话开始时加载用户记忆"""
    user_id = state["user_id"]
    memories = store.search(("user", user_id))
    
    # 构造记忆上下文
    memory_text = "\n".join([
        f"- {m.value['content']}" for m in memories
    ]) if memories else "暂无记忆"
    
    return {"messages": [SystemMessage(content=f"【用户记忆】\n{memory_text}")]}

def chat_node(state: AgentState):
    """对话节点"""
    response = llm.invoke(state["messages"])
    return {"messages": [response]}

def extract_and_save_memory(state: AgentState):
    """提取并保存记忆"""
    user_id = state["user_id"]
    
    # 获取对话内容
    conversation = "\n".join([
        f"{m.type}: {m.content}" for m in state["messages"]
        if hasattr(m, 'content')
    ])
    
    # LLM 提取值得记住的信息
    prompt = f"""从以下对话中提取值得长期记住的信息（用户偏好、事实、重要信息）。
格式：每行一条，格式为 "key|value"。如果没有值得记住的，回复"NONE"。

对话：
{conversation}

提取结果："""
    
    extraction = llm.invoke(prompt).content.strip()
    
    if extraction != "NONE":
        for line in extraction.split("\n"):
            if "|" in line:
                parts = line.split("|", 1)
                if len(parts) == 2:
                    key, value = parts[0].strip(), parts[1].strip()
                    # 检查是否已存在（更新而非重复）
                    existing = store.get(("user", user_id), key)
                    if existing:
                        # 更新
                        store.put(("user", user_id), key=key,
                                 value={"content": value, "updated": True})
                    else:
                        # 新增
                        store.put(("user", user_id), key=key,
                                 value={"content": value})
    
    return {}

# 构建图
builder = StateGraph(AgentState)
builder.add_node("load_memories", load_memories_node)
builder.add_node("chat", chat_node)
builder.add_node("save_memory", extract_and_save_memory)

builder.add_edge(START, "load_memories")
builder.add_edge("load_memories", "chat")
builder.add_edge("chat", "save_memory")
builder.add_edge("save_memory", END)

graph = builder.compile()

# 模拟多会话
def chat_session(user_id, user_input):
    """一次会话"""
    result = graph.invoke({
        "messages": [HumanMessage(content=user_input)],
        "user_id": user_id
    })
    return result["messages"][-2].content  # 倒数第二个是 AI 回复（最后是系统消息）

# 会话1：用户透露偏好
print("=== 会话1 ===")
print("用户：我喜欢用 Python，正在学 LangGraph")
print("AI：", chat_session("user_1", "我喜欢用 Python，正在学 LangGraph"))

# 会话2：新会话，Agent 应该"记得"
print("\n=== 会话2（新会话）===")
print("用户：推荐学习资源")
print("AI：", chat_session("user_1", "推荐学习资源"))
# AI 应该基于"喜欢 Python、学 LangGraph"推荐

# 查看存储的记忆
print("\n=== 存储的记忆 ===")
for item in store.search(("user", "user_1")):
    print(f"  {item.key}: {item.value}")
```

**进阶：带遗忘机制的记忆管理**

```python
import time

def cleanup_old_memories(store, namespace, max_age_days=30):
    """清理过时记忆"""
    current_time = time.time()
    
    for item in store.search(namespace):
        # 检查记忆年龄（假设 value 中有 timestamp）
        if "timestamp" in item.value:
            age = current_time - item.value["timestamp"]
            if age > max_age_days * 86400:  # 超过30天
                store.delete(namespace, key=item.key)
                print(f"遗忘：{item.key}")

def save_memory_with_timestamp(state, store, user_id):
    """保存记忆时加时间戳"""
    # ... 提取逻辑 ...
    store.put(
        ("user", user_id),
        key=key,
        value={
            "content": value,
            "timestamp": time.time(),
            "access_count": 0
        }
    )
```

**进阶：记忆重要性评分**

```python
def score_memory_importance(content):
    """用 LLM 给记忆打分"""
    prompt = f"""评估以下信息的重要性（1-10分）：
{content}
只输出数字。"""
    score = llm.invoke(prompt).content.strip()
    try:
        return int(score)
    except:
        return 5  # 默认5分

# 存储时记录重要性
store.put(("user", user_id), key=key, value={
    "content": value,
    "importance": score_memory_importance(value)
})

# 检索时优先返回高重要性记忆
memories = sorted(
    store.search(("user", user_id)),
    key=lambda x: x.value.get("importance", 5),
    reverse=True
)[:5]  # 取前5条最重要的
```

### 总结

- **跨会话记忆**：不同会话间共享记忆，让 Agent "认识"老用户
- **四环节**：提取、保存、检索、更新/遗忘
- **提取策略**：LLM 提取、规则提取、混合提取
- **检索策略**：全量、语义、最近优先
- **更新策略**：覆盖、版本化、合并
- **遗忘机制**：时间遗忘、重要性遗忘、用户主动删除
- **生产化**：配合向量检索、重要性评分、定期清理

---

# 第9章 实战应用

本章是课程的实战篇。我们将综合运用前 8 章的知识，构建三个完整的实战项目：RAG Agent（检索增强生成）、客服机器人、生产部署。学完本章，你将具备从零到一构建生产级 LangGraph 应用的能力。

---

## 第30讲：RAG Agent 实战

### 概念

**RAG（Retrieval-Augmented Generation，检索增强生成）** 是让 LLM 基于外部知识库回答问题的技术。RAG Agent = 检索 + LLM + Agent 工作流。与传统 RAG（单次检索+生成）不同，RAG Agent 具备：多轮检索、查询改写、结果评估、自我纠错等能力。

本讲构建一个完整的 RAG Agent，能根据用户问题智能检索文档、评估检索质量、必要时改写查询重新检索、最终生成有依据的回答。

### 原理

**RAG Agent vs 传统 RAG 原理**：
- **传统 RAG**：用户问题 → 检索 → 拼接上下文 → LLM 生成。单次检索，无反馈。
- **RAG Agent**：用户问题 → 检索 → 评估 → 不满意则改写查询重新检索 → 满意则生成。多轮检索，有反馈循环。

RAG Agent 的核心是"评估-改写"循环——Agent 评估检索结果是否足够回答问题，不够则改写查询重试。这种自我纠错让检索质量大幅提升。

**Agent 工作流原理**：
```
用户问题
   ↓
[查询分析] → 是否需要检索？
   ↓ 是
[检索] → [结果评估] → 质量足够？
   ↓ 否                    ↓ 是
[查询改写] → [重新检索]    [生成回答]
                          ↓
                       [引用标注]
                          ↓
                        [END]
```

**查询改写原理**：原始用户问题可能不适合直接检索（太口语化、太宽泛、太具体）。Agent 用 LLM 改写查询：
- **扩展同义词**：如"AI" → "人工智能"
- **拆分子问题**：复杂问题拆为多个子问题
- **添加关键词**：补充检索关键词
- **改写为陈述句**：问题转为陈述句（更适合语义检索）

**结果评估原理**：检索后，Agent 评估结果质量：
- **相关性**：结果与问题的相关程度
- **完整性**：是否包含回答所需全部信息
- **权威性**：来源是否可靠
- **时效性**：信息是否最新

评估不达标则触发改写重检。

### 例子

**完整 RAG Agent 实现**

```python
import os
from dotenv import load_dotenv
from typing import TypedDict, Annotated, List
from langgraph.graph import StateGraph, START, END
from langgraph.graph.message import add_messages
from langchain_openai import ChatOpenAI, OpenAIEmbeddings
from langchain_core.messages import HumanMessage, AIMessage, SystemMessage
from langchain_core.tools import tool
from langchain_core.documents import Document
from langchain_community.vectorstores import FAISS

load_dotenv()

# 1. 准备知识库（模拟）
KNOWLEDGE_BASE = [
    Document(page_content="LangGraph 是 LangChain 推出的 Agent 编排框架，支持状态管理和循环。",
             metadata={"source": "langgraph_doc", "section": "intro"}),
    Document(page_content="LangGraph 的核心概念包括 State、Node、Edge。State 是数据容器，Node 是执行单元，Edge 定义跳转关系。",
             metadata={"source": "langgraph_doc", "section": "concepts"}),
    Document(page_content="ReAct 是一种 Agent 架构，通过思考-行动-观察循环实现智能决策。",
             metadata={"source": "react_paper", "section": "method"}),
    Document(page_content="LangGraph 通过 create_react_agent 函数快速创建 ReAct Agent。",
             metadata={"source": "langgraph_doc", "section": "api"}),
    Document(page_content="Checkpoint 是 LangGraph 的持久化机制，保存图执行状态，支持中断恢复。",
             metadata={"source": "langgraph_doc", "section": "persistence"}),
]

# 2. 创建向量库
embeddings = OpenAIEmbeddings()
vectorstore = FAISS.from_documents(KNOWLEDGE_BASE, embeddings)
retriever = vectorstore.as_retriever(search_kwargs={"k": 3})

# 3. 初始化 LLM
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)

# 4. 定义 State
class RAGState(TypedDict):
    original_question: str          # 原始问题
    current_query: str              # 当前检索查询
    retrieved_docs: List[Document]  # 检索到的文档
    evaluation: str                 # 评估结果
    iteration: int                  # 迭代次数
    final_answer: str               # 最终回答
    sources: List[str]              # 引用来源

# 5. 定义节点
def analyze_query_node(state: RAGState):
    """分析并改写查询"""
    question = state["original_question"]
    
    prompt = f"""你是查询优化专家。改写以下问题为更适合检索的查询：
1. 提取关键词
2. 补充同义词
3. 改为陈述句

原始问题：{question}
只输出改写后的查询，不要解释。"""
    
    rewritten = llm.invoke(prompt).content.strip()
    return {"current_query": rewritten, "iteration": 1}

def retrieve_node(state: RAGState):
    """检索文档"""
    query = state["current_query"]
    docs = retriever.invoke(query)
    return {"retrieved_docs": docs}

def evaluate_node(state: RAGState):
    """评估检索结果质量"""
    question = state["original_question"]
    docs = state["retrieved_docs"]
    docs_text = "\n\n".join([d.page_content for d in docs])
    
    prompt = f"""评估以下检索结果是否能回答用户问题。

用户问题：{question}

检索结果：
{docs_text}

评估标准：
- SUFFICIENT：结果足够回答问题
- INSUFFICIENT：结果不够，需要重新检索

只输出 SUFFICIENT 或 INSUFFICIENT。"""
    
    evaluation = llm.invoke(prompt).content.strip()
    return {"evaluation": evaluation}

def rewrite_query_node(state: RAGState):
    """改写查询以重新检索"""
    question = state["original_question"]
    old_query = state["current_query"]
    docs = state["retrieved_docs"]
    docs_text = "\n".join([d.page_content[:100] for d in docs])
    
    prompt = f"""之前的检索结果不够。改写查询以获得更好的结果。

原始问题：{question}
旧查询：{old_query}
旧结果摘要：{docs_text}

改写策略：
1. 换用同义词
2. 拆分问题
3. 调整关键词

只输出新查询。"""
    
    new_query = llm.invoke(prompt).content.strip()
    return {
        "current_query": new_query,
        "iteration": state["iteration"] + 1
    }

def generate_answer_node(state: RAGState):
    """生成最终回答"""
    question = state["original_question"]
    docs = state["retrieved_docs"]
    
    # 构造上下文
    context = "\n\n".join([
        f"[来源{i+1}] {d.page_content}\n来源：{d.metadata.get('source', 'unknown')}"
        for i, d in enumerate(docs)
    ])
    
    prompt = f"""基于以下检索结果回答用户问题。

要求：
1. 回答必须基于检索结果，不要编造
2. 在关键信息后标注来源编号，如 [1]、[2]
3. 如果信息不足，坦诚告知

用户问题：{question}

检索结果：
{context}

回答："""
    
    answer = llm.invoke(prompt).content
    sources = [d.metadata.get("source", "unknown") for d in docs]
    
    return {
        "final_answer": answer,
        "sources": sources
    }

def should_retry(state: RAGState):
    """判断是否需要重新检索"""
    if state["evaluation"] == "SUFFICIENT":
        return "generate"
    if state["iteration"] >= 3:  # 最多重试3次
        return "generate"
    return "rewrite"

# 6. 构建图
builder = StateGraph(RAGState)
builder.add_node("analyze", analyze_query_node)
builder.add_node("retrieve", retrieve_node)
builder.add_node("evaluate", evaluate_node)
builder.add_node("rewrite", rewrite_query_node)
builder.add_node("generate", generate_answer_node)

builder.add_edge(START, "analyze")
builder.add_edge("analyze", "retrieve")
builder.add_edge("retrieve", "evaluate")
builder.add_conditional_edges(
    "evaluate",
    should_retry,
    {"rewrite": "rewrite", "generate": "generate"}
)
builder.add_edge("rewrite", "retrieve")  # 改写后重新检索
builder.add_edge("generate", END)

rag_agent = builder.compile()

# 7. 测试
if __name__ == "__main__":
    result = rag_agent.invoke({
        "original_question": "LangGraph 是什么？它的核心概念有哪些？",
        "current_query": "",
        "retrieved_docs": [],
        "evaluation": "",
        "iteration": 0,
        "final_answer": "",
        "sources": []
    })
    
    print("问题：", result["original_question"])
    print(f"\n迭代次数：{result['iteration']}")
    print(f"\n回答：\n{result['final_answer']}")
    print(f"\n来源：{result['sources']}")
```

**RAG Agent 流程图**：
```
[START] → [analyze] → [retrieve] → [evaluate] → (SUFFICIENT?)
                                        ↓ 是              ↓ 否
                                     [generate]      [rewrite] → [retrieve] (循环)
                                        ↓
                                      [END]
```

### 总结

- **RAG Agent**：具备多轮检索、查询改写、结果评估的智能 RAG
- **核心循环**：检索 → 评估 → 不满意则改写 → 重新检索
- **查询改写**：扩展同义词、拆分子问题、调整关键词
- **结果评估**：相关性、完整性、权威性、时效性
- **最大重试**：防止无限循环，建议 3-5 次
- **引用标注**：生成回答时标注来源，提升可信度

---

## 第31讲：客服机器人实战

### 概念

本讲构建一个生产级客服机器人，综合运用对话管理、工具调用、人机交互、记忆管理等能力。这个客服机器人能：理解用户意图、查询订单、处理退款、转人工、记住用户信息。这是 LangGraph 在企业场景的典型应用。

### 原理

**客服机器人的核心能力原理**：
1. **意图识别**：理解用户想做什么（查询、投诉、咨询）
2. **工具调用**：查询订单系统、用户系统、退款系统
3. **多轮对话**：处理需要多轮交互的复杂请求
4. **人机交互**：高风险操作前请求确认，无法处理时转人工
5. **记忆管理**：记住用户信息，提供个性化服务

**架构设计原理**：客服机器人采用 Supervisor 架构——一个主 Agent 负责意图识别和路由，多个子 Agent 处理不同业务（订单、退款、咨询）。这种架构让每个子 Agent 专注一个领域，便于维护和扩展。

**工具设计原理**：客服机器人需要的工具包括：
- `query_order`：查询订单状态
- `query_user_info`：查询用户信息
- `process_refund`：处理退款（高风险，需 HITL）
- `transfer_to_human`：转人工

**HITL 在客服场景的应用原理**：退款、取消订单等高风险操作，Agent 不能直接执行，必须请求用户确认。用 interrupt 暂停，展示操作详情，用户确认后才执行。

### 例子

**完整客服机器人实现**

```python
import os
from dotenv import load_dotenv
from typing import TypedDict, Annotated, Optional
from langgraph.graph import StateGraph, START, END
from langgraph.graph.message import add_messages, MessagesState
from langgraph.checkpoint.memory import MemorySaver
from langgraph.store.memory import InMemoryStore
from langgraph.types import interrupt, Command
from langchain_openai import ChatOpenAI
from langchain_core.tools import tool
from langchain_core.messages import HumanMessage, SystemMessage, AIMessage

load_dotenv()
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)
checkpointer = MemorySaver()
store = InMemoryStore()

# 1. 定义工具
@tool
def query_order(order_id: str) -> str:
    """查询订单状态
    
    Args:
        order_id: 订单号
    """
    # 模拟订单查询
    orders = {
        "ORD001": {"status": "已发货", "amount": 99, "items": ["商品A"]},
        "ORD002": {"status": "处理中", "amount": 199, "items": ["商品B", "商品C"]},
        "ORD003": {"status": "已退款", "amount": 50, "items": ["商品D"]},
    }
    order = orders.get(order_id)
    if order:
        return f"订单 {order_id}：状态={order['status']}，金额={order['amount']}元，商品={order['items']}"
    return f"未找到订单 {order_id}"

@tool
def process_refund(order_id: str, reason: str) -> str:
    """处理退款（需要用户确认）
    
    Args:
        order_id: 订单号
        reason: 退款原因
    """
    # 模拟退款处理
    return f"退款已提交：订单 {order_id}，原因：{reason}，预计3-5个工作日到账"

@tool
def transfer_to_human(reason: str) -> str:
    """转人工客服
    
    Args:
        reason: 转人工原因
    """
    return f"已转人工客服，原因：{reason}。请稍候，客服人员即将接入。"

tools = [query_order, process_refund, transfer_to_human]
llm_with_tools = llm.bind_tools(tools)

# 2. 定义 State
class CustomerServiceState(MessagesState):
    user_id: str
    user_name: str
    intent: str

# 3. 系统提示
SYSTEM_PROMPT = """你是「小客」，一个专业的客服机器人。

工作规范：
1. 友好、专业、高效地服务用户
2. 涉及退款等高风险操作时，必须先向用户确认详情
3. 无法处理的问题，及时转人工
4. 记住用户信息，提供个性化服务

你可以使用以下工具：
- query_order：查询订单
- process_refund：处理退款（高风险）
- transfer_to_human：转人工"""

# 4. 定义节点
def intent_recognition_node(state: CustomerServiceState):
    """意图识别节点"""
    last_msg = state["messages"][-1].content
    
    prompt = f"""识别用户意图，输出以下之一：
- query_order：查询订单
- refund：退款
- consult：咨询
- complaint：投诉
- other：其他

用户输入：{last_msg}
意图："""
    
    intent = llm.invoke(prompt).content.strip()
    return {"intent": intent}

def agent_node(state: CustomerServiceState):
    """主 Agent 节点"""
    # 加载用户记忆
    user_id = state.get("user_id", "anonymous")
    memories = store.search(("user", user_id))
    memory_text = "\n".join([f"- {m.value['content']}" for m in memories])
    
    system_content = SYSTEM_PROMPT
    if memory_text:
        system_content += f"\n\n用户记忆：\n{memory_text}"
    if state.get("user_name"):
        system_content += f"\n\n当前用户：{state['user_name']}"
    
    messages = [SystemMessage(content=system_content)] + state["messages"]
    response = llm_with_tools.invoke(messages)
    return {"messages": [response]}

def tool_node_with_approval(state: CustomerServiceState):
    """带人工确认的工具节点"""
    from langchain_core.messages import ToolMessage
    last_msg = state["messages"][-1]
    
    if not hasattr(last_msg, "tool_calls") or not last_msg.tool_calls:
        return {"messages": []}
    
    tool_messages = []
    for tc in last_msg.tool_calls:
        tool_name = tc["name"]
        tool_args = tc["args"]
        
        # 退款操作需要人工确认
        if tool_name == "process_refund":
            approval = interrupt({
                "operation": "退款",
                "details": tool_args,
                "question": f"确认退款？订单：{tool_args.get('order_id')}，原因：{tool_args.get('reason')}"
            })
            
            if approval.lower() not in ["yes", "确认", "同意"]:
                tool_messages.append(ToolMessage(
                    content="用户取消了退款操作",
                    tool_call_id=tc["id"]
                ))
                continue
        
        # 执行工具
        tool = next((t for t in tools if t.name == tool_name), None)
        if tool:
            try:
                result = tool.invoke(tool_args)
                tool_messages.append(ToolMessage(
                    content=result,
                    tool_call_id=tc["id"]
                ))
            except Exception as e:
                tool_messages.append(ToolMessage(
                    content=f"工具执行错误：{e}",
                    tool_call_id=tc["id"]
                ))
    
    return {"messages": tool_messages}

def save_memory_node(state: CustomerServiceState):
    """保存用户记忆"""
    user_id = state.get("user_id", "anonymous")
    conversation = "\n".join([
        f"{m.type}: {m.content}" for m in state["messages"]
        if hasattr(m, "content")
    ])
    
    # 提取用户信息
    prompt = f"""从对话中提取值得记住的用户信息（偏好、常用订单、问题模式）。
格式：key|value，每行一条。无则输出 NONE。

对话：
{conversation}"""
    
    extraction = llm.invoke(prompt).content.strip()
    if extraction != "NONE":
        for line in extraction.split("\n"):
            if "|" in line:
                key, value = line.split("|", 1)
                store.put(("user", user_id), key=key.strip(),
                         value={"content": value.strip()})
    
    return {}

def should_use_tool(state: CustomerServiceState):
    """判断是否需要调用工具"""
    last_msg = state["messages"][-1]
    if hasattr(last_msg, "tool_calls") and last_msg.tool_calls:
        return "tools"
    return "save_memory"

# 5. 构建图
builder = StateGraph(CustomerServiceState)
builder.add_node("intent", intent_recognition_node)
builder.add_node("agent", agent_node)
builder.add_node("tools", tool_node_with_approval)
builder.add_node("save_memory", save_memory_node)

builder.add_edge(START, "intent")
builder.add_edge("intent", "agent")
builder.add_conditional_edges("agent", should_use_tool,
    {"tools": "tools", "save_memory": "save_memory"})
builder.add_edge("tools", "agent")
builder.add_edge("save_memory", END)

# 6. 编译（带 checkpointer 支持 HITL）
graph = builder.compile(checkpointer=checkpointer)

# 7. 测试
def chat(user_id, user_name, message, config):
    """一次对话"""
    result = graph.invoke(
        {
            "messages": [HumanMessage(content=message)],
            "user_id": user_id,
            "user_name": user_name,
            "intent": ""
        },
        config=config
    )
    return result["messages"][-1].content

config = {"configurable": {"thread_id": "session_1"}}

# 对话1：查询订单
print("用户：查询订单 ORD001")
print("客服：", chat("user_1", "张三", "查询订单 ORD001", config))

# 对话2：退款（会触发 HITL）
print("\n用户：我要退款 ORD002，商品损坏")
try:
    result = graph.invoke(
        {
            "messages": [HumanMessage(content="我要退款 ORD002，商品损坏")],
            "user_id": "user_1",
            "user_name": "张三",
            "intent": ""
        },
        config=config
    )
except Exception as e:
    print("触发人工确认，请确认退款操作")

# 恢复并确认
result = graph.invoke(Command(resume="确认"), config=config)
print("客服：", result["messages"][-1].content)
```

### 总结

- **客服机器人**：综合运用对话、工具、HITL、记忆的完整应用
- **Supervisor 架构**：主 Agent 路由，子 Agent 处理具体业务
- **工具设计**：查询、退款、转人工等核心工具
- **HITL 应用**：退款等高风险操作必须用户确认
- **记忆管理**：保存用户信息，提供个性化服务
- **生产化要点**：错误处理、日志监控、并发控制、人工兜底

---

## 第32讲：部署与生产化

### 概念

本讲探讨如何将 LangGraph 应用部署到生产环境。包括：API 服务封装、并发与性能优化、监控与日志、错误处理与重试、成本控制、安全考虑。这是从"能跑"到"能扛住生产流量"的关键一步。

### 原理

**生产化核心挑战原理**：
1. **并发**：多用户同时使用，需线程安全
2. **性能**：响应时间、吞吐量
3. **可靠性**：错误处理、重试、降级
4. **可观测**：日志、监控、追踪
5. **成本**：LLM 调用成本控制
6. **安全**：输入验证、权限控制

**API 封装原理**：将 LangGraph 图封装为 HTTP API，前端通过 REST 调用。常用框架：FastAPI（Python 原生）、Flask。LangGraph 也提供 `langgraph-api` 官方部署方案。

**并发模型原理**：LangGraph 图本身是线程安全的（编译后不可变），但共享资源（如 Checkpointer、Store）需考虑并发。MemorySaver 不支持多进程，生产环境必须用 PostgresSaver 等支持并发的 Checkpointer。

**流式 API 原理**：生产环境通常用 SSE（Server-Sent Events）或 WebSocket 实现流式输出。LangGraph 的 stream 模式天然支持流式，配合 FastAPI 的 StreamingResponse 即可实现。

**错误处理原理**：生产环境必须处理：
- LLM 调用失败（超时、限流、API 错误）
- 工具执行失败（外部服务不可用）
- 图执行异常（递归超限、状态错误）
- 用户输入异常（恶意输入、超长输入）

**成本控制原理**：LLM 调用是主要成本。控制策略：
- 缓存常见问题答案
- 限制对话历史长度
- 选择合适模型（简单任务用小模型）
- 监控 token 消耗
- 设置用户配额

### 例子

**示例1：FastAPI 封装 LangGraph**

```python
# app.py
import os
from dotenv import load_dotenv
from fastapi import FastAPI, HTTPException
from fastapi.responses import StreamingResponse
from pydantic import BaseModel
from langchain_openai import ChatOpenAI
from langchain_core.messages import HumanMessage
from langgraph.graph import StateGraph, START, END
from langgraph.graph.message import MessagesState
from langgraph.checkpoint.memory import MemorySaver
import json
import asyncio

load_dotenv()
app = FastAPI(title="LangGraph API")

# 初始化
llm = ChatOpenAI(model="gpt-4o-mini", streaming=True)
checkpointer = MemorySaver()

# 构建图
def chat_node(state):
    response = llm.invoke(state["messages"])
    return {"messages": [response]}

builder = StateGraph(MessagesState)
builder.add_node("chat", chat_node)
builder.add_edge(START, "chat")
builder.add_edge("chat", END)
graph = builder.compile(checkpointer=checkpointer)

# 请求模型
class ChatRequest(BaseModel):
    message: str
    thread_id: str = "default"
    user_id: str = "anonymous"

class ChatResponse(BaseModel):
    reply: str
    thread_id: str

# 普通接口
@app.post("/chat", response_model=ChatResponse)
async def chat(request: ChatRequest):
    try:
        config = {"configurable": {"thread_id": request.thread_id}}
        result = graph.invoke(
            {"messages": [HumanMessage(content=request.message)]},
            config=config
        )
        reply = result["messages"][-1].content
        return ChatResponse(reply=reply, thread_id=request.thread_id)
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

# 流式接口（SSE）
@app.post("/chat/stream")
async def chat_stream(request: ChatRequest):
    config = {"configurable": {"thread_id": request.thread_id}}
    
    async def event_stream():
        try:
            for event, metadata in graph.stream(
                {"messages": [HumanMessage(content=request.message)]},
                config=config,
                stream_mode="messages"
            ):
                if event.content:
                    # SSE 格式
                    yield f"data: {json.dumps({'content': event.content})}\n\n"
            yield f"data: {json.dumps({'done': True})}\n\n"
        except Exception as e:
            yield f"data: {json.dumps({'error': str(e)})}\n\n"
    
    return StreamingResponse(
        event_stream(),
        media_type="text/event-stream"
    )

# 健康检查
@app.get("/health")
async def health():
    return {"status": "ok"}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

**示例2：错误处理与重试**

```python
from tenacity import retry, stop_after_attempt, wait_exponential
from langchain_openai import ChatOpenAI

# 带重试的 LLM
llm = ChatOpenAI(
    model="gpt-4o-mini",
    timeout=30,  # 超时30秒
    max_retries=3  # 最多重试3次
)

# 节点级错误处理
def safe_llm_node(state):
    try:
        response = llm.invoke(state["messages"])
        return {"messages": [response]}
    except Exception as e:
        # 记录错误，返回降级回复
        import logging
        logging.error(f"LLM 调用失败：{e}")
        from langchain_core.messages import AIMessage
        return {"messages": [AIMessage(content="抱歉，服务暂时不可用，请稍后重试。")]}

# 工具级错误处理
def safe_tool_call(tool, args):
    try:
        return tool.invoke(args)
    except Exception as e:
        return f"工具 {tool.name} 执行失败：{e}，请稍后重试。"
```

**示例3：监控与日志**

```python
import logging
import time
from functools import wraps

# 配置日志
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('agent.log'),
        logging.StreamHandler()
    ]
)
logger = logging.getLogger(__name__)

# 节点装饰器：记录执行时间和状态
def monitored_node(func):
    @wraps(func)
    def wrapper(state):
        start = time.time()
        node_name = func.__name__
        logger.info(f"[{node_name}] 开始执行，输入状态keys: {list(state.keys())}")
        
        try:
            result = func(state)
            duration = time.time() - start
            logger.info(f"[{node_name}] 执行完成，耗时 {duration:.2f}s")
            return result
        except Exception as e:
            duration = time.time() - start
            logger.error(f"[{node_name}] 执行失败，耗时 {duration:.2f}s，错误：{e}")
            raise
    
    return wrapper

# 使用
@monitored_node
def chat_node(state):
    return {"messages": [llm.invoke(state["messages"])]}
```

**示例4：成本控制**

```python
import tiktoken

def count_tokens(messages, model="gpt-4o-mini"):
    """计算 token 数"""
    enc = tiktoken.encoding_for_model(model)
    total = 0
    for msg in messages:
        total += len(enc.encode(str(msg.content)))
    return total

def cost_controlled_node(state):
    """带成本控制的节点"""
    MAX_TOKENS = 4000  # 单次调用最大 token
    MAX_HISTORY = 20  # 最大历史消息数
    
    messages = state["messages"]
    
    # 裁剪历史
    if len(messages) > MAX_HISTORY:
        messages = messages[-MAX_HISTORY:]
    
    # 检查 token
    token_count = count_tokens(messages)
    if token_count > MAX_TOKENS:
        # 进一步裁剪
        while len(messages) > 2 and count_tokens(messages) > MAX_TOKENS:
            messages = messages[1:]  # 删除最早的消息
    
    logger.info(f"调用 LLM，token 数：{count_tokens(messages)}")
    response = llm.invoke(messages)
    return {"messages": [response]}
```

**示例5：生产部署清单**

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
    # 图配置
    "graph": {
        "recursion_limit": 25,  # 防止无限循环
    },
    # Checkpointer（生产用 Postgres）
    "checkpointer": "postgres://...",
    # Store（生产用 Postgres + 向量检索）
    "store": "postgres://...",
    # 监控
    "monitoring": {
        "log_level": "INFO",
        "metrics": True,
        "tracing": True
    },
    # 限流
    "rate_limit": {
        "requests_per_minute": 60,
        "tokens_per_day": 1000000
    }
}

# Dockerfile
"""
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "4"]
"""

# docker-compose.yml
"""
version: '3.8'
services:
  api:
    build: .
    ports: ["8000:8000"]
    environment:
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - DATABASE_URL=postgresql://user:pass@db:5432/langgraph
    depends_on: [db]
  
  db:
    image: postgres:15
    environment:
      POSTGRES_DB: langgraph
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
    volumes: ["pgdata:/var/lib/postgresql/data"]

volumes:
  pgdata:
"""
```

### 总结

- **生产化挑战**：并发、性能、可靠性、可观测、成本、安全
- **API 封装**：FastAPI + StreamingResponse 实现流式 API
- **并发模型**：用 PostgresSaver 等支持并发的 Checkpointer
- **错误处理**：LLM 失败重试、工具失败降级、图异常兜底
- **监控日志**：节点装饰器记录执行时间、状态、错误
- **成本控制**：限制历史长度、token 上限、用户配额
- **部署方案**：Docker + docker-compose，配合 Postgres 持久化

---

## 课程结语

恭喜你完成了 LangGraph 系统教程的全部 32 讲！让我们回顾这段学习旅程：

**学习路径回顾**：
- **第1章 基础入门**：建立对 LangGraph 的整体认知，搭建环境，运行第一个程序
- **第2章 图的构建**：深入 State、Node、Edge 三大核心组件，掌握 Reducer 状态合并
- **第3章 消息与对话**：理解消息体系，构建多轮对话 Agent
- **第4章 工具调用**：让 Agent 具备调用外部工具的能力
- **第5章 Agent 架构**：从 ReAct 到多 Agent 协作的架构演进
- **第6章 高级控制流**：循环、并行、子图、Command 跳转
- **第7章 人机交互与流式**：HITL、流式输出、中断恢复
- **第8章 持久化与记忆**：Checkpoint、Memory Store、跨会话记忆
- **第9章 实战应用**：RAG Agent、客服机器人、生产部署

**核心知识体系**：
1. **图思维**：将工作流抽象为图，状态在节点间流转
2. **状态管理**：State + Reducer 实现灵活的状态合并
3. **控制流**：普通边、条件边、循环、并行、Command
4. **Agent 架构**：ReAct、多 Agent、Supervisor
5. **生产能力**：HITL、流式、持久化、记忆、监控

**下一步学习建议**：
1. **实践项目**：选择一个真实场景，从零构建完整 Agent
2. **深入源码**：阅读 LangGraph 源码，理解 Pregel 模型实现
3. **社区跟进**：关注 LangGraph 官方更新，学习新特性
4. **扩展生态**：学习 LangSmith（监控）、LangServe（部署）等配套工具
5. **前沿研究**：阅读 Agent 相关论文，如 ReAct、Reflexion、Tree of Thoughts

LangGraph 是一个快速发展的框架，本课程建立的基础让你能够跟上它的演进。祝你在 Agent 构建之路上越走越远！


