# ChromaDB 系统教程

> 一门从基础到实战的系统化 ChromaDB 课程，共 15 讲，5 章。
> 每讲包含：概念、原理、例子、总结四个部分。

---

## 课程总览

- **预计讲数**：15 讲（5 章）
- **学习目标**：从向量数据库基本概念出发，系统掌握 ChromaDB 的数据管理、相似性检索、元数据过滤、持久化部署，以及与 LLM 框架集成构建 RAG 应用的能力。
- **适用人群**：AI 应用开发者、LLM 工程师、全栈开发者、向量数据库学习者。
- **学习路径**：基础认知 → 数据管理 → 查询检索 → 进阶特性 → 实战应用。

---

## 详细章节目录

### 第 1 章 ChromaDB 基础（3 讲）
- 第 1 讲：向量数据库与 ChromaDB 概述
- 第 2 讲：安装与环境配置
- 第 3 讲：核心概念——Collection、Document、Embedding

### 第 2 章 数据管理（4 讲）
- 第 4 讲：集合（Collection）管理
- 第 5 讲：文档与嵌入的添加
- 第 6 讲：文档的查询、更新与删除
- 第 7 讲：元数据（Metadata）管理

### 第 3 章 查询与检索（3 讲）
- 第 8 讲：相似性查询基础
- 第 9 讲：元数据过滤查询
- 第 10 讲：查询优化与最佳实践

### 第 4 章 进阶特性（3 讲）
- 第 11 讲：嵌入函数与自定义模型
- 第 12 讲：持久化与客户端-服务器模式
- 第 13 讲：多租户与数据隔离

### 第 5 章 实战应用（2 讲）
- 第 14 讲：与 LangChain/LlamaIndex 集成
- 第 15 讲：实战案例——RAG 知识库系统

---

# 第 1 章 ChromaDB 基础

ChromaDB 是专为 AI/LLM 应用设计的开源向量数据库，是构建 RAG（检索增强生成）应用的核心基础设施。本章带您认识向量数据库的概念、掌握 ChromaDB 的安装配置，并理解其核心数据模型。

---

## 第 1 讲：向量数据库与 ChromaDB 概述

### 概念

**向量数据库（Vector Database）** 是一种专门存储、索引和检索高维向量的数据库系统。向量是 AI 模型（如 BERT、GPT）将文本、图像、音频等非结构化数据转换为的数值数组（如 `[0.12, -0.34, 0.56, ...]`），维度通常从 128 到 1536 不等。向量数据库通过相似性搜索（而非精确匹配）找到语义相近的数据，是现代 AI 应用的关键基础设施。

**ChromaDB**（简称 Chroma）是一个开源的、AI 原生的向量数据库，由 Chroma 公司开发，采用 Apache 2.0 许可证。ChromaDB 的设计目标是让开发者能够轻松地将向量搜索能力集成到 LLM 应用中，特别专注于 RAG（检索增强生成）场景。

ChromaDB 的核心特征：

- **AI 原生**：专为 LLM 应用设计，内置嵌入函数和文档管理。
- **开发者友好**：Python 优先的 API，几行代码即可上手。
- **嵌入式与客户端-服务器**：支持内存模式、本地持久化、客户端-服务器部署。
- **开源免费**：Apache 2.0 许可，可自托管，无供应商锁定。
- **丰富的集成**：与 LangChain、LlamaIndex、OpenAI 等无缝集成。
- **元数据过滤**：支持在向量搜索基础上按元数据条件过滤。

### 原理

**1. 为什么需要向量数据库？**

传统数据库（如 MySQL、PostgreSQL）擅长精确匹配——"查找 ID=100 的用户"、"查找价格<100 的商品"。但 AI 应用需要的是**语义相似性**搜索：

- "如何训练神经网络" 与 "深度学习模型训练方法" 语义相近，但无共同关键词。
- "苹果公司发布新手机" 与 "Apple unveils new iPhone" 跨语言但语义相同。

向量数据库通过将数据转换为向量，然后用向量距离（如余弦相似度）衡量语义相似度，实现"按意思搜索"。这是关键词搜索无法做到的。

**2. 向量数据库 vs 传统数据库**

| 维度 | 传统数据库 | 向量数据库 |
|------|----------|----------|
| 数据类型 | 结构化（数字、文本、日期） | 高维向量 + 元数据 |
| 查询方式 | 精确匹配、范围查询 | 相似性搜索（ANN） |
| 索引结构 | B+树、哈希 | HNSW、IVF、PQ |
| 距离度量 | 无 | 余弦、欧氏、点积 |
| 典型场景 | 业务系统、事务处理 | 语义搜索、推荐、RAG |
| 规模 | GB-TB | 百万-十亿级向量 |

**3. ChromaDB 在 AI 技术栈中的位置**

```
┌─────────────────────────────────────┐
│         用户应用层（Web/App）         │
├─────────────────────────────────────┤
│      LLM 框架（LangChain等）         │
├─────────────────────────────────────┤
│  向量数据库（ChromaDB）  ← 本课程     │
├─────────────────────────────────────┤
│   嵌入模型（OpenAI、Sentence-BERT）   │
├─────────────────────────────────────┤
│      大语言模型（GPT、Claude等）       │
└─────────────────────────────────────┘
```

ChromaDB 处于 LLM 框架之下、嵌入模型之上，负责存储和检索向量化的知识。

**4. ChromaDB 的核心能力**

- **存储**：保存文档、向量、元数据三元组。
- **嵌入**：内置嵌入函数，自动将文本转为向量。
- **检索**：基于向量相似度的 Top-K 检索。
- **过滤**：按元数据条件缩小检索范围。
- **持久化**：数据落盘，重启不丢失。

### 例子

**ChromaDB 的典型应用场景**

```python
# 场景1：语义搜索
# 用户问："如何提高 Python 代码性能？"
# ChromaDB 返回语义相近的文档，即使关键词不同：
# - "Python 代码优化技巧"
# - "提升 Python 程序运行速度的方法"
# - "Python performance optimization"

# 场景2：RAG 知识库
# 用户问："公司的差旅报销标准是什么？"
# ChromaDB 检索公司文档 → 返回相关段落 → LLM 基于段落生成答案

# 场景3：推荐系统
# 用户浏览了"机器学习入门"文章
# ChromaDB 找到向量相近的文章推荐：
# - "深度学习基础"
# - "神经网络教程"

# 场景4：去重与聚类
# 找出语义重复的文档
# 将相似文档聚类分组
```

**ChromaDB 与其他向量数据库对比**

| 数据库 | 类型 | 部署 | 特色 |
|--------|------|------|------|
| ChromaDB | 开源 | 嵌入式/服务器 | AI 原生，开发者友好 |
| Pinecone | 云服务 | SaaS | 全托管，企业级 |
| Weaviate | 开源 | 自托管 | 混合搜索，GraphQL |
| Milvus | 开源 | 分布式 | 大规模，高性能 |
| Qdrant | 开源 | 自托管 | Rust 编写，高性能 |
| pgvector | 扩展 | PostgreSQL | 集成关系数据库 |

ChromaDB 的优势在于**轻量、易用、AI 原生**，适合中小规模 RAG 应用和原型开发。

### 总结

- 向量数据库专门存储和检索高维向量，通过相似性搜索实现语义匹配。
- ChromaDB 是 AI 原生的开源向量数据库，专为 LLM/RAG 应用设计。
- ChromaDB 处于 AI 技术栈中间层，连接嵌入模型和 LLM 框架。
- 典型应用：语义搜索、RAG 知识库、推荐系统、去重聚类。
- **关键要点**：ChromaDB 适合中小规模应用和快速原型；大规模生产可考虑 Milvus、Pinecone。
- **常见误区**：把向量数据库当通用数据库用；忽视嵌入模型质量对检索效果的影响。

---

## 第 2 讲：安装与环境配置

### 概念

ChromaDB 提供多种安装和运行方式：

- **pip 安装**：Python 包，最常用的开发方式。
- **Docker 部署**：容器化运行服务器模式。
- **内存模式**：数据存内存，适合测试和原型。
- **持久化模式**：数据落盘到本地目录。
- **客户端-服务器模式**：独立服务器进程，多客户端共享。

ChromaDB 的 Python SDK 是主要的使用方式，支持同步和异步 API。

### 原理

**1. ChromaDB 的架构模式**

ChromaDB 支持两种运行模式：

- **嵌入式（Embedded）**：ChromaDB 作为 Python 库直接运行在应用进程中，数据存内存或本地文件。适合单机应用和开发测试。
- **客户端-服务器（Client-Server）**：ChromaDB 作为独立服务器运行，应用通过 HTTP 客户端连接。适合多应用共享、生产部署。

**2. 依赖与环境**

ChromaDB 的核心依赖：

- Python 3.8+
- SQLite（持久化存储）
- ONNX Runtime（默认嵌入模型运行时）
- numpy、pandas（数据处理）

**3. 嵌入模型**

ChromaDB 默认使用 `all-MiniLM-L6-v2` 模型（基于 Sentence-Transformers），特点：

- 模型小（~80MB），下载快。
- 向量维度 384。
- 中英文支持良好。
- 本地运行，无需 API Key。

也可配置 OpenAI、Cohere 等云端嵌入模型。

### 例子

**1. pip 安装**

```bash
# 基础安装
pip install chromadb

# 安装完整功能（含客户端依赖）
pip install chromadb[full]

# 指定版本
pip install chromadb==0.4.24

# 升级
pip install --upgrade chromadb

# 验证安装
python -c "import chromadb; print(chromadb.__version__)"
```

**2. 内存模式（最快上手）**

```python
import chromadb

# 创建内存客户端（数据不持久化）
client = chromadb.Client()

# 创建集合
collection = client.create_collection(name="docs")

# 添加文档
collection.add(
    documents=["Hello world", "Foo bar"],
    metadatas=[{"source": "book"}, {"source": "web"}],
    ids=["1", "2"]
)

# 查询
results = collection.query(
    query_texts=["hello"],
    n_results=1
)
print(results)
# {'ids': [['1']], 'documents': [['Hello world']], ...}
```

**3. 持久化模式**

```python
import chromadb

# 创建持久化客户端（数据存到指定目录）
client = chromadb.PersistentClient(path="./my_chroma_db")

# 集合操作会自动持久化
collection = client.get_or_create_collection(name="knowledge_base")

collection.add(
    documents=["Python 是一门编程语言", "Java 也是编程语言"],
    metadatas=[{"category": "tech"}, {"category": "tech"}],
    ids=["doc1", "doc2"]
)

# 重启后数据仍在
# 下次运行：
client = chromadb.PersistentClient(path="./my_chroma_db")
collection = client.get_collection(name="knowledge_base")
print(collection.count())  # 2
```

**4. 客户端-服务器模式**

```bash
# 启动服务器（终端1）
chroma run --path ./chroma_data --host 0.0.0.0 --port 8000
# 输出：Application startup complete. Running on http://0.0.0.0:8000
```

```python
# 客户端连接（终端2或应用中）
import chromadb

# 连接到服务器
client = chromadb.HttpClient(host="localhost", port=8000)

# 使用方式与嵌入式相同
collection = client.get_or_create_collection(name="remote_docs")
collection.add(
    documents=["远程存储的文档"],
    ids=["1"]
)

# 查询
results = collection.query(query_texts=["远程"], n_results=1)
print(results)
```

**5. Docker 部署**

```bash
# 拉取镜像
docker pull chromadb/chroma:latest

# 运行容器
docker run -d \
    --name chromadb \
    -p 8000:8000 \
    -v ./chroma_data:/chroma/chroma \
    chromadb/chroma:latest

# 验证
curl http://localhost:8000/api/v1/heartbeat
# {"nanosecond heartbeat":1234567890}
```

```yaml
# docker-compose.yml
version: '3.8'
services:
  chromadb:
    image: chromadb/chroma:latest
    container_name: chromadb
    ports:
      - "8000:8000"
    volumes:
      - ./chroma_data:/chroma/chroma
    environment:
      - ANONYMIZED_TELEMETRY=false
    restart: unless-stopped
```

```bash
# 启动
docker-compose up -d

# 查看日志
docker logs chromadb

# 停止
docker-compose down
```

**6. 虚拟环境与项目管理**

```bash
# 使用 venv
python -m venv chroma_env
source chroma_env/bin/activate  # Linux/Mac
# chroma_env\Scripts\activate   # Windows

# 安装依赖
pip install chromadb

# 使用 conda
conda create -n chroma python=3.10
conda activate chroma
pip install chromadb

# requirements.txt
echo "chromadb==0.4.24" > requirements.txt
pip install -r requirements.txt
```

**7. 验证安装**

```python
import chromadb

# 验证版本
print(f"ChromaDB 版本: {chromadb.__version__}")

# 验证客户端创建
client = chromadb.Client()
print(f"客户端: {client}")

# 验证心跳
heartbeat = client.heartbeat()
print(f"心跳: {heartbeat}")  # 时间戳

# 验证集合操作
col = client.create_collection("test")
col.add(documents=["test"], ids=["1"])
print(f"文档数: {col.count()}")

# 清理
client.delete_collection("test")
print("安装验证通过！")
```

### 总结

- ChromaDB 支持 pip 安装，提供内存、持久化、客户端-服务器三种模式。
- 内存模式适合测试，持久化模式适合单机应用，客户端-服务器适合生产部署。
- 默认嵌入模型 `all-MiniLM-L6-v2` 本地运行，无需 API Key。
- Docker 部署适合生产环境，配合 docker-compose 管理方便。
- **关键要点**：开发用 PersistentClient，生产用 HttpClient + Docker。
- **常见误区**：内存模式重启丢数据；客户端-服务器模式忘记启动服务；忽视 Python 版本要求。

---

## 第 3 讲：核心概念——Collection、Document、Embedding

### 概念

ChromaDB 的数据模型围绕三个核心概念：

**1. 集合（Collection）**：类似关系数据库的表，是文档的容器。每个集合有名称、嵌入函数、元数据配置。集合内的数据模式一致。

**2. 文档（Document）**：存储的原始文本内容。ChromaDB 自动将文档通过嵌入函数转换为向量。文档是可选的——也可以直接存储向量而不存原文。

**3. 嵌入（Embedding）**：文档的向量表示，是高维浮点数数组。嵌入由嵌入模型生成，捕获文档的语义信息。相似文档的嵌入向量距离近。

**4. 元数据（Metadata）**：附加在文档上的键值对，用于过滤和描述。如 `{"source": "web", "date": "2024-01-15", "category": "tech"}`。

**5. ID**：每个文档的唯一标识，可以是字符串或整数。ID 用于引用、更新、删除文档。

### 原理

**1. 数据模型的关系**

```
Collection（集合）
├── Document 1
│   ├── id: "doc1"
│   ├── document: "Python 是编程语言"
│   ├── embedding: [0.12, -0.34, ...]  ← 自动生成或手动提供
│   └── metadata: {"source": "book", "page": 10}
├── Document 2
│   ├── id: "doc2"
│   ├── document: "Java 也是编程语言"
│   ├── embedding: [0.15, -0.31, ...]
│   └── metadata: {"source": "web"}
└── ...
```

**2. 嵌入的生成方式**

ChromaDB 支持两种嵌入方式：

- **自动嵌入**：只提供文档文本，ChromaDB 用配置的嵌入函数自动生成向量。
- **手动嵌入**：直接提供向量，跳过嵌入函数。适合已预计算向量或使用自定义模型的场景。

**3. 距离度量**

ChromaDB 支持三种距离度量：

- **余弦距离（cosine）**：衡量向量方向差异，适合文本语义相似度。默认选项。
- **欧氏距离（l2）**：衡量向量绝对距离，适合图像等。
- **点积（ip）**：内积，适合已归一化的向量。

距离越小，向量越相似。

**4. HNSW 索引**

ChromaDB 使用 HNSW（Hierarchical Navigable Small World）算法索引向量：

- **近似最近邻（ANN）**：不保证精确，但速度快。
- **图结构**：构建多层图，快速导航到近邻。
- **参数**：`M`（连接数）、`construction_ef`（构建时搜索宽度）、`search_ef`（查询时搜索宽度）。
- **权衡**：精度 vs 速度，可通过参数调整。

### 例子

**1. 理解集合与文档**

```python
import chromadb

client = chromadb.PersistentClient(path="./chroma_data")

# 创建集合
collection = client.create_collection(
    name="articles",
    metadata={"description": "文章集合"}  # 集合级元数据
)

# 添加文档（自动嵌入）
collection.add(
    documents=[
        "Python 是一门流行的编程语言",
        "JavaScript 主要用于网页开发",
        "机器学习是人工智能的分支"
    ],
    metadatas=[
        {"topic": "programming", "language": "python"},
        {"topic": "programming", "language": "javascript"},
        {"topic": "ai", "language": "python"}
    ],
    ids=["art1", "art2", "art3"]
)

# 查看集合信息
print(f"集合名称: {collection.name}")
print(f"文档数量: {collection.count()}")
print(f"集合元数据: {collection.metadata}")
```

**2. 理解嵌入向量**

```python
# 自动嵌入：只提供文本
collection.add(
    documents=["深度学习使用神经网络"],
    ids=["art4"]
)
# ChromaDB 自动调用嵌入函数生成向量

# 查看生成的嵌入
result = collection.get(ids=["art4"], include=["embeddings"])
print(f"嵌入维度: {len(result['embeddings'][0])}")  # 384（默认模型）
print(f"嵌入前5维: {result['embeddings'][0][:5]}")
# [0.12, -0.34, 0.56, ...]

# 手动嵌入：直接提供向量
collection.add(
    embeddings=[[0.1, 0.2, 0.3, 0.4]],  # 4维向量（示例）
    documents=["手动嵌入的文档"],
    ids=["art5"]
)
# 跳过嵌入函数，直接存储提供的向量
```

**3. 距离度量配置**

```python
# 创建集合时指定距离度量
collection_cosine = client.create_collection(
    name="docs_cosine",
    metadata={"hnsw:space": "cosine"}  # 余弦距离（默认）
)

collection_l2 = client.create_collection(
    name="docs_l2",
    metadata={"hnsw:space": "l2"}  # 欧氏距离
)

collection_ip = client.create_collection(
    name="docs_ip",
    metadata={"hnsw:space": "ip"}  # 点积
)

# HNSW参数配置
collection_tuned = client.create_collection(
    name="docs_tuned",
    metadata={
        "hnsw:space": "cosine",
        "hnsw:M": 64,              # 连接数（默认16，越大越精确）
        "hnsw:construction_ef": 200,  # 构建时搜索宽度（默认100）
        "hnsw:search_ef": 100       # 查询时搜索宽度（默认10）
    }
)
```

**4. 完整的数据操作示例**

```python
import chromadb

client = chromadb.PersistentClient(path="./chroma_data")

# 创建集合
collection = client.get_or_create_collection(
    name="knowledge_base",
    metadata={"hnsw:space": "cosine"}
)

# 添加文档
collection.add(
    documents=[
        "ChromaDB 是向量数据库",
        "Pinecone 是云向量数据库",
        "MySQL 是关系数据库"
    ],
    metadatas=[
        {"type": "vector_db", "open_source": True},
        {"type": "vector_db", "open_source": False},
        {"type": "rdbms", "open_source": True}
    ],
    ids=["doc1", "doc2", "doc3"]
)

# 查询
results = collection.query(
    query_texts=["开源向量数据库"],
    n_results=2
)

print("查询结果:")
for i, (id, doc, dist) in enumerate(zip(
    results['ids'][0],
    results['documents'][0],
    results['distances'][0]
)):
    print(f"  {i+1}. ID={id}, 距离={dist:.4f}, 文档={doc}")

# 获取所有文档
all_docs = collection.get()
print(f"\n所有文档: {all_docs['ids']}")

# 获取特定文档
specific = collection.get(ids=["doc1"], include=["documents", "metadatas"])
print(f"特定文档: {specific}")
```

**5. 嵌入维度与模型的关系**

```python
# 不同嵌入模型产生不同维度的向量

# 默认模型 all-MiniLM-L6-v2：384维
collection_default = client.create_collection(name="default_model")
collection_default.add(documents=["test"], ids=["1"])
emb = collection_default.get(ids=["1"], include=["embeddings"])
print(f"默认模型维度: {len(emb['embeddings'][0])}")  # 384

# OpenAI text-embedding-ada-002：1536维
from chromadb.utils import embedding_functions
openai_ef = embedding_functions.OpenAIEmbeddingFunction(
    api_key="sk-...",
    model_name="text-embedding-ada-002"
)
collection_openai = client.create_collection(
    name="openai_model",
    embedding_function=openai_ef
)
collection_openai.add(documents=["test"], ids=["1"])
emb = collection_openai.get(ids=["1"], include=["embeddings"])
print(f"OpenAI模型维度: {len(emb['embeddings'][0])}")  # 1536

# 注意：同一集合内的向量维度必须一致
```

### 总结

- ChromaDB 核心概念：Collection（集合）、Document（文档）、Embedding（嵌入）、Metadata（元数据）、ID。
- 嵌入可自动生成（提供文本）或手动提供（直接给向量）。
- 距离度量：cosine（默认，文本）、l2（图像）、ip（归一化向量）。
- HNSW 索引参数（M、construction_ef、search_ef）平衡精度与速度。
- **关键要点**：同一集合的向量维度必须一致；距离度量在创建集合时指定，不可更改。
- **常见误区**：混淆文档和嵌入；忽视距离度量的选择；HNSW 参数设置不当影响性能。

---

# 第 2 章 数据管理

本章是 ChromaDB 实操的核心。从集合管理开始，逐步掌握文档的增删改查和元数据管理，最终能够熟练使用 ChromaDB 管理向量数据。

---

## 第 4 讲：集合（Collection）管理

### 概念

**集合（Collection）** 是 ChromaDB 中文档的容器，类似关系数据库的表。每个集合有：

- **名称（name）**：唯一标识，字符串。
- **嵌入函数（embedding_function）**：用于将文本转为向量的函数。
- **元数据（metadata）**：集合级配置，如距离度量、HNSW 参数。
- **文档**：集合内存储的文档列表。

集合管理操作：

- **create_collection()**：创建集合。
- **get_collection()**：获取集合。
- **get_or_create_collection()**：获取或创建（不存在则创建）。
- **list_collections()**：列出所有集合。
- **delete_collection()**：删除集合。
- **rename_collection()**：重命名集合。

### 原理

**1. 集合的命名规则**

- 必须非空字符串。
- 长度 3-63 字符。
- 只能包含字母、数字、下划线、连字符。
- 不能以数字开头。
- 不能包含两个连续下划线。
- 不能是保留字（如 `chroma`）。

**2. 集合的元数据配置**

集合元数据用于配置索引行为：

- `hnsw:space`：距离度量（cosine/l2/ip）。
- `hnsw:M`：HNSW 连接数。
- `hnsw:construction_ef`：构建时搜索宽度。
- `hnsw:search_ef`：查询时搜索宽度。
- 自定义元数据：如 `{"description": "..."}`。

**3. 嵌入函数的绑定**

集合创建时可指定嵌入函数：

- 不指定：使用默认 `all-MiniLM-L6-v2`。
- 指定 OpenAI、Cohere 等云端模型。
- 指定自定义嵌入函数。

嵌入函数一旦绑定，后续添加文档时自动调用。查询时也用同一函数将查询文本转为向量。

**4. 集合的持久化**

- PersistentClient：集合数据自动持久化到指定目录。
- HttpClient：集合数据存在服务器端。
- 内存模式：集合数据随进程结束消失。

### 例子

**1. 创建集合**

```python
import chromadb

client = chromadb.PersistentClient(path="./chroma_data")

# 基本创建
collection = client.create_collection(name="articles")

# 带元数据创建
collection = client.create_collection(
    name="documents",
    metadata={
        "hnsw:space": "cosine",       # 距离度量
        "hnsw:M": 32,                 # 连接数
        "description": "文档集合"      # 自定义元数据
    }
)

# 带嵌入函数创建
from chromadb.utils import embedding_functions
openai_ef = embedding_functions.OpenAIEmbeddingFunction(
    api_key="sk-...",
    model_name="text-embedding-ada-002"
)
collection = client.create_collection(
    name="openai_docs",
    embedding_function=openai_ef
)

# get_or_create（推荐，避免重复创建报错）
collection = client.get_or_create_collection(name="safe_collection")
```

**2. 查看与获取集合**

```python
# 列出所有集合
collections = client.list_collections()
print(f"所有集合: {[c.name for c in collections]}")

# 获取集合
collection = client.get_collection(name="articles")

# 获取集合信息
print(f"名称: {collection.name}")
print(f"元数据: {collection.metadata}")
print(f"文档数: {collection.count()}")
print(f"嵌入函数: {collection._embedding_function}")

# 检查集合是否存在
try:
    collection = client.get_collection(name="nonexistent")
except Exception as e:
    print(f"集合不存在: {e}")
```

**3. 重命名与删除集合**

```python
# 重命名集合
collection = client.get_collection(name="articles")
collection.modify(name="articles_v2")  # 修改名称

# 修改元数据
collection.modify(metadata={"description": "更新后的描述"})

# 删除集合
client.delete_collection(name="articles_v2")

# 安全删除（先检查存在）
try:
    client.delete_collection(name="articles_v2")
    print("删除成功")
except Exception as e:
    print(f"删除失败: {e}")
```

**4. 集合管理工具函数**

```python
def list_all_collections(client):
    """列出所有集合及其统计信息"""
    collections = client.list_collections()
    for col_info in collections:
        col = client.get_collection(col_info.name)
        print(f"  {col.name}: {col.count()} 文档, 元数据={col.metadata}")

def clear_collection(client, name):
    """清空集合（删除后重建）"""
    try:
        client.delete_collection(name)
        print(f"已删除集合: {name}")
    except:
        pass
    return client.get_or_create_collection(name)

def ensure_collection(client, name, metadata=None):
    """确保集合存在"""
    try:
        return client.get_collection(name)
    except:
        return client.create_collection(name, metadata=metadata or {})

# 使用
client = chromadb.PersistentClient(path="./chroma_data")
list_all_collections(client)

col = ensure_collection(
    client,
    "my_docs",
    metadata={"hnsw:space": "cosine"}
)
```

**5. 多集合管理示例**

```python
import chromadb

client = chromadb.PersistentClient(path="./chroma_data")

# 为不同业务创建不同集合
collections_config = {
    "user_profiles": {"hnsw:space": "cosine", "description": "用户画像"},
    "product_catalog": {"hnsw:space": "cosine", "description": "商品目录"},
    "support_tickets": {"hnsw:space": "cosine", "description": "工单"},
    "knowledge_base": {"hnsw:space": "cosine", "description": "知识库"}
}

for name, metadata in collections_config.items():
    client.get_or_create_collection(name=name, metadata=metadata)
    print(f"集合 '{name}' 已就绪")

# 列出所有集合
print("\n所有集合:")
for col in client.list_collections():
    print(f"  - {col.name}")
```

### 总结

- 集合是文档容器，创建时可配置距离度量、HNSW 参数、嵌入函数。
- 推荐用 `get_or_create_collection` 避免重复创建报错。
- 集合名称有命名规则限制（3-63 字符，字母数字下划线连字符）。
- 嵌入函数绑定后，添加文档和查询时自动调用。
- **关键要点**：距离度量创建后不可更改；删除集合会删除所有文档。
- **常见误区**：集合名不符合命名规则；忽视 get_or_create 的便利性；嵌入函数与集合不匹配。

---

## 第 5 讲：文档与嵌入的添加

### 概念

**文档添加** 是向集合中插入数据的核心操作。ChromaDB 的 `add()` 方法支持：

- **documents**：文本内容列表（可选，若提供则自动嵌入）。
- **embeddings**：预计算的向量列表（可选，与 documents 二选一或同时提供）。
- **metadatas**：元数据列表（可选）。
- **ids**：唯一标识列表（必需）。

添加方式：

- **add()**：添加新文档，ID 重复时报错。
- **upsert()**：添加或更新，ID 存在则更新。

### 原理

**1. 自动嵌入流程**

当只提供 `documents` 而不提供 `embeddings` 时：

1. ChromaDB 调用集合的嵌入函数。
2. 嵌入函数将每个文档文本转为向量。
3. 文档、向量、元数据、ID 一起存储。
4. HNSW 索引更新，将新向量加入图结构。

**2. 批量添加的性能**

- 嵌入计算是主要耗时操作（尤其云端 API）。
- 批量添加比逐条添加效率高（减少 API 调用和网络开销）。
- ChromaDB 内部会分批处理，但建议控制单次添加量（如 1000-5000 条）。

**3. ID 的要求**

- 必须唯一（同一集合内）。
- 可以是字符串或整数。
- 不提供时 ChromaDB 自动生成（基于 UUID）。
- 建议用业务 ID（如文档哈希、URL）便于管理。

**4. upsert 的行为**

- ID 不存在：插入新文档。
- ID 已存在：更新文档、嵌入、元数据。
- 适合"同步"场景（如重新索引知识库）。

### 例子

**1. 基本添加**

```python
import chromadb

client = chromadb.PersistentClient(path="./chroma_data")
collection = client.get_or_create_collection(name="docs")

# 添加单个文档
collection.add(
    documents=["Python 是编程语言"],
    metadatas=[{"source": "wiki"}],
    ids=["doc1"]
)

# 添加多个文档
collection.add(
    documents=[
        "Java 也是编程语言",
        "JavaScript 用于网页",
        "Go 语言由 Google 开发"
    ],
    metadatas=[
        {"source": "wiki", "category": "programming"},
        {"source": "web", "category": "programming"},
        {"source": "wiki", "category": "programming"}
    ],
    ids=["doc2", "doc3", "doc4"]
)

# 不提供 ID（自动生成）
collection.add(
    documents=["自动生成ID的文档"],
    metadatas=[{"source": "auto"}]
)
# 可通过返回值获取生成的 ID
```

**2. 手动嵌入**

```python
# 场景：已有预计算的向量，跳过嵌入函数
import numpy as np

# 生成随机向量（示例，实际应用中由嵌入模型生成）
embeddings = np.random.rand(3, 384).tolist()

collection.add(
    embeddings=embeddings,
    documents=["文档1", "文档2", "文档3"],
    metadatas=[{"idx": i} for i in range(3)],
    ids=["emb1", "emb2", "emb3"]
)

# 只提供向量，不提供文档（纯向量存储）
collection.add(
    embeddings=[[0.1]*384, [0.2]*384],
    metadatas=[{"type": "vector_only"}]*2,
    ids=["vec1", "vec2"]
)
```

**3. upsert 操作**

```python
# upsert：存在则更新，不存在则插入
collection.upsert(
    documents=["Python 是一门解释型编程语言"],  # 更新后的内容
    metadatas=[{"source": "wiki", "updated": True}],
    ids=["doc1"]  # 已存在的ID
)

# upsert 新文档
collection.upsert(
    documents=["这是新文档"],
    metadatas=[{"source": "new"}],
    ids=["doc_new"]  # 不存在的ID，会插入
)

# 批量 upsert
collection.upsert(
    documents=["文档A", "文档B", "文档C"],
    metadatas=[{"batch": 1}]*3,
    ids=["up1", "up2", "up3"]
)
```

**4. 批量添加（大数据量）**

```python
import chromadb
from chromadb.utils import embedding_functions

client = chromadb.PersistentClient(path="./chroma_data")
collection = client.get_or_create_collection(name="large_docs")

# 模拟大量文档
def generate_documents(count):
    """生成测试文档"""
    docs = []
    for i in range(count):
        docs.append({
            "id": f"doc_{i}",
            "text": f"这是第 {i} 篇文档的内容，讨论主题 {i % 10}",
            "metadata": {"batch": i // 1000, "index": i}
        })
    return docs

# 分批添加
all_docs = generate_documents(10000)
batch_size = 1000

for i in range(0, len(all_docs), batch_size):
    batch = all_docs[i:i+batch_size]
    collection.add(
        documents=[d["text"] for d in batch],
        metadatas=[d["metadata"] for d in batch],
        ids=[d["id"] for d in batch]
    )
    print(f"已添加 {i + len(batch)}/{len(all_docs)}")

print(f"总计: {collection.count()} 文档")
```

**5. 从文件加载文档**

```python
import os
from pathlib import Path

def load_text_files(directory):
    """从目录加载文本文件"""
    docs = []
    metadatas = []
    ids = []

    for filepath in Path(directory).rglob("*.txt"):
        with open(filepath, 'r', encoding='utf-8') as f:
            content = f.read()
        docs.append(content)
        metadatas.append({
            "source": str(filepath),
            "filename": filepath.name,
            "size": len(content)
        })
        ids.append(filepath.stem)  # 文件名（无扩展名）作为ID

    return docs, metadatas, ids

# 加载并添加
docs, metadatas, ids = load_text_files("./knowledge_docs")
print(f"加载了 {len(docs)} 个文件")

# 分批添加
batch_size = 100
for i in range(0, len(docs), batch_size):
    collection.add(
        documents=docs[i:i+batch_size],
        metadatas=metadatas[i:i+batch_size],
        ids=ids[i:i+batch_size]
    )

print(f"已添加 {collection.count()} 文档到集合")
```

**6. 添加时的错误处理**

```python
def safe_add(collection, documents, metadatas, ids):
    """安全的添加操作，处理重复ID"""
    try:
        collection.add(
            documents=documents,
            metadatas=metadatas,
            ids=ids
        )
        return True, "添加成功"
    except Exception as e:
        if "already exists" in str(e):
            # ID重复，改用upsert
            collection.upsert(
                documents=documents,
                metadatas=metadatas,
                ids=ids
            )
            return True, "ID重复，已更新"
        return False, str(e)

# 使用
success, msg = safe_add(
    collection,
    documents=["测试文档"],
    metadatas=[{"test": True}],
    ids=["doc1"]  # 可能已存在
)
print(msg)
```

### 总结

- add() 添加新文档（ID 重复报错），upsert() 添加或更新。
- 可只提供 documents（自动嵌入）或只提供 embeddings（纯向量）。
- 批量添加效率远高于逐条添加，建议分批（1000-5000 条/批）。
- ID 建议用业务标识（文件名、哈希、URL），便于管理和去重。
- **关键要点**：upsert 适合同步场景；嵌入计算是主要耗时点；分批添加避免内存溢出。
- **常见误区**：循环单条添加导致性能差；忽视 ID 重复错误；不提供 ID 导致难以管理。

---

## 第 6 讲：文档的查询、更新与删除

### 概念

本讲讲解文档的读取、更新和删除操作：

- **get()**：按 ID 获取文档。
- **update()**：更新文档内容、元数据或嵌入。
- **delete()**：删除文档。
- **count()**：统计文档数量。
- **peek()**：预览前几条文档。

### 原理

**1. get() 的包含控制**

`get()` 通过 `include` 参数控制返回内容：

- `documents`：文档文本。
- `embeddings`：嵌入向量。
- `metadatas`：元数据。
- 不指定：返回全部。

默认不返回 embeddings（节省带宽和内存）。

**2. update() 的行为**

- 更新文档文本：重新计算嵌入。
- 更新嵌入：直接替换向量。
- 更新元数据：替换元数据（非合并）。
- ID 不存在：报错（应用 upsert）。

**3. delete() 的行为**

- 删除文档和关联的向量、元数据。
- HNSW 索引中的对应节点标记删除（不立即物理删除）。
- 删除不可逆。

### 例子

**1. get() 获取文档**

```python
import chromadb

client = chromadb.PersistentClient(path="./chroma_data")
collection = client.get_or_create_collection(name="docs")

# 添加测试数据
collection.add(
    documents=["文档A", "文档B", "文档C"],
    metadatas=[{"idx": 1}, {"idx": 2}, {"idx": 3}],
    ids=["a", "b", "c"]
)

# 按ID获取
result = collection.get(ids=["a", "b"])
print(result)
# {'ids': ['a', 'b'], 'documents': ['文档A', '文档B'], 'metadatas': [{'idx': 1}, {'idx': 2}]}

# 获取所有文档
all_docs = collection.get()
print(f"总数: {len(all_docs['ids'])}")

# 控制返回内容
result = collection.get(ids=["a"], include=["embeddings"])
print(f"嵌入维度: {len(result['embeddings'][0])}")

result = collection.get(ids=["a"], include=["documents", "metadatas"])
# 不返回embeddings

# 预览前几条
peek_result = collection.peek(limit=2)
print(f"预览: {peek_result}")
```

**2. 按条件获取**

```python
# 按元数据过滤
result = collection.get(
    where={"idx": 2},  # 元数据条件
    include=["documents", "metadatas"]
)
print(f"idx=2的文档: {result['documents']}")

# 按元数据范围
result = collection.get(
    where={"$and": [
        {"idx": {"$gte": 2}},
        {"idx": {"$lte": 3}}
    ]}
)
print(f"idx在2-3之间的文档: {result['ids']}")

# 限制返回数量
result = collection.get(limit=2)
print(f"前2条: {result['ids']}")

# 分页
result = collection.get(limit=2, offset=1)
print(f"第2-3条: {result['ids']}")
```

**3. update() 更新文档**

```python
# 更新文档内容（会重新计算嵌入）
collection.update(
    ids=["a"],
    documents=["文档A-更新版"]
)

# 更新元数据
collection.update(
    ids=["a"],
    metadatas=[{"idx": 1, "updated": True, "version": 2}]
)

# 同时更新文档和元数据
collection.update(
    ids=["b"],
    documents=["文档B-更新版"],
    metadatas=[{"idx": 2, "updated": True}]
)

# 更新嵌入（手动提供）
collection.update(
    ids=["c"],
    embeddings=[[0.1]*384]  # 新的向量
)

# 批量更新
collection.update(
    ids=["a", "b"],
    documents=["文档A-v3", "文档B-v3"],
    metadatas=[{"version": 3}, {"version": 3}]
)

# 验证更新
result = collection.get(ids=["a"])
print(f"更新后的文档: {result['documents']}")
print(f"更新后的元数据: {result['metadatas']}")
```

**4. delete() 删除文档**

```python
# 按ID删除
collection.delete(ids=["c"])
print(f"删除后文档数: {collection.count()}")

# 按元数据条件删除
collection.delete(where={"idx": 2})
print(f"删除idx=2后: {collection.count()}")

# 删除所有文档（通过where条件）
# 注意：没有delete_all()方法，用空where或重建集合
collection.delete(where={"idx": {"$gte": 0}})  # 删除所有idx>=0的
print(f"清空后: {collection.count()}")

# 更彻底的清空：删除集合重建
client.delete_collection("docs")
collection = client.get_or_create_collection(name="docs")
```

**5. count() 和 peek()**

```python
# 统计文档数
count = collection.count()
print(f"文档总数: {count}")

# 预览（默认前10条）
peek = collection.peek(limit=5)
print(f"预览:")
for i, (id, doc) in enumerate(zip(peek['ids'], peek['documents'])):
    print(f"  {i+1}. {id}: {doc[:50]}...")
```

**6. 文档管理工具类**

```python
class DocumentManager:
    """文档管理工具类"""

    def __init__(self, client, collection_name):
        self.collection = client.get_or_create_collection(collection_name)

    def add_document(self, doc_id, text, metadata=None):
        """添加单个文档"""
        self.collection.upsert(
            documents=[text],
            metadatas=[metadata or {}],
            ids=[doc_id]
        )
        return doc_id

    def get_document(self, doc_id):
        """获取单个文档"""
        result = self.collection.get(ids=[doc_id])
        if result['ids']:
            return {
                'id': result['ids'][0],
                'document': result['documents'][0],
                'metadata': result['metadatas'][0]
            }
        return None

    def update_document(self, doc_id, text=None, metadata=None):
        """更新文档"""
        update_args = {"ids": [doc_id]}
        if text:
            update_args["documents"] = [text]
        if metadata:
            update_args["metadatas"] = [metadata]
        self.collection.update(**update_args)

    def delete_document(self, doc_id):
        """删除文档"""
        self.collection.delete(ids=[doc_id])

    def list_documents(self, limit=100, offset=0):
        """列出文档"""
        return self.collection.get(limit=limit, offset=offset)

    def count(self):
        """统计数量"""
        return self.collection.count()

# 使用
client = chromadb.PersistentClient(path="./chroma_data")
dm = DocumentManager(client, "managed_docs")

dm.add_document("doc1", "Hello World", {"source": "test"})
dm.add_document("doc2", "Foo Bar", {"source": "test"})

print(dm.get_document("doc1"))
print(f"总数: {dm.count()}")

dm.update_document("doc1", text="Hello World Updated")
dm.delete_document("doc2")
print(f"删除后: {dm.count()}")
```

### 总结

- get() 按 ID 或条件获取文档，include 控制返回内容。
- update() 更新文档（重新嵌入）、元数据或向量；ID 不存在报错。
- delete() 按 ID 或条件删除，不可逆。
- count() 统计数量，peek() 预览前几条。
- **关键要点**：默认不返回 embeddings 节省资源；清空集合用删除重建。
- **常见误区**：update 不存在的 ID 报错；delete 后无法恢复；忽视 include 参数。

---

## 第 7 讲：元数据（Metadata）管理

### 概念

**元数据（Metadata）** 是附加在文档上的键值对，用于描述和过滤文档。元数据不参与向量相似度计算，但可以在查询时用于过滤结果。

元数据的特点：

- 键值对形式：`{"key": value}`。
- 值类型：字符串、整数、浮点数、布尔值。
- 每个文档可有不同元数据结构（但建议一致）。
- 支持丰富的过滤操作符。

### 原理

**1. 元数据过滤操作符**

ChromaDB 支持以下过滤操作符：

- **$eq**：等于（默认，可直接写 `{"key": value}`）。
- **$ne**：不等于。
- **$gt**：大于。
- **$gte**：大于等于。
- **$lt**：小于。
- **$lte**：小于等于。
- **$in**：在列表中。
- **$nin**：不在列表中。
- **$and**：逻辑与。
- **$or**：逻辑或。

**2. 元数据的存储**

元数据存储在 SQLite 中（持久化模式），与向量分离。查询时先按元数据过滤，再在结果集内做向量搜索，提升效率。

**3. 元数据的设计原则**

- 用元数据存储可枚举的属性（类别、来源、状态）。
- 避免存储大段文本（用文档本身）。
- 字段名简短，值类型一致。
- 为常用查询条件设计索引字段。

### 例子

**1. 添加带元数据的文档**

```python
import chromadb

client = chromadb.PersistentClient(path="./chroma_data")
collection = client.get_or_create_collection(name="articles")

# 添加带丰富元数据的文档
collection.add(
    documents=[
        "Python 编程入门",
        "机器学习基础",
        "Web 开发指南",
        "深度学习实践",
        "数据库设计"
    ],
    metadatas=[
        {
            "category": "programming",
            "language": "python",
            "difficulty": 1,
            "author": "张三",
            "published": True,
            "date": "2024-01-15"
        },
        {
            "category": "ai",
            "language": "python",
            "difficulty": 3,
            "author": "李四",
            "published": True,
            "date": "2024-02-20"
        },
        {
            "category": "web",
            "language": "javascript",
            "difficulty": 2,
            "author": "王五",
            "published": False,
            "date": "2024-03-10"
        },
        {
            "category": "ai",
            "language": "python",
            "difficulty": 4,
            "author": "李四",
            "published": True,
            "date": "2024-04-05"
        },
        {
            "category": "database",
            "language": "sql",
            "difficulty": 2,
            "author": "赵六",
            "published": True,
            "date": "2024-05-12"
        }
    ],
    ids=["art1", "art2", "art3", "art4", "art5"]
)
```

**2. 基本过滤查询**

```python
# 等值过滤
result = collection.get(where={"category": "ai"})
print(f"AI类文章: {result['ids']}")  # ['art2', 'art4']

# 多条件等值
result = collection.get(where={
    "category": "ai",
    "language": "python"
})
print(f"AI+Python: {result['ids']}")

# 布尔过滤
result = collection.get(where={"published": True})
print(f"已发布: {result['ids']}")

# 字符串过滤
result = collection.get(where={"author": "李四"})
print(f"李四的文章: {result['ids']}")
```

**3. 比较操作符**

```python
# $gt: 大于
result = collection.get(where={"difficulty": {"$gt": 2}})
print(f"难度>2: {result['ids']}")  # ['art2', 'art4']

# $gte: 大于等于
result = collection.get(where={"difficulty": {"$gte": 2}})
print(f"难度>=2: {result['ids']}")

# $lt: 小于
result = collection.get(where={"difficulty": {"$lt": 3}})
print(f"难度<3: {result['ids']}")

# $lte: 小于等于
result = collection.get(where={"difficulty": {"$lte": 2}})

# $ne: 不等于
result = collection.get(where={"language": {"$ne": "python"}})
print(f"非Python: {result['ids']}")

# $in: 在列表中
result = collection.get(where={"category": {"$in": ["ai", "database"]}})
print(f"AI或数据库: {result['ids']}")

# $nin: 不在列表中
result = collection.get(where={"category": {"$nin": ["ai", "database"]}})
print(f"非AI非数据库: {result['ids']}")
```

**4. 逻辑组合**

```python
# $and: 逻辑与
result = collection.get(where={
    "$and": [
        {"category": "ai"},
        {"difficulty": {"$gte": 3}}
    ]
})
print(f"AI且难度>=3: {result['ids']}")

# $or: 逻辑或
result = collection.get(where={
    "$or": [
        {"category": "web"},
        {"category": "database"}
    ]
})
print(f"Web或数据库: {result['ids']}")

# 嵌套逻辑
result = collection.get(where={
    "$and": [
        {"published": True},
        {"$or": [
            {"category": "ai"},
            {"category": "database"}
        ]}
    ]
})
print(f"已发布的AI或数据库文章: {result['ids']}")

# 复杂查询
result = collection.get(where={
    "$and": [
        {"difficulty": {"$gte": 2, "$lte": 3}},
        {"language": {"$in": ["python", "javascript"]}},
        {"published": True}
    ]
})
print(f"复杂查询: {result['ids']}")
```

**5. 在 query 中使用元数据过滤**

```python
# 查询时同时过滤元数据
results = collection.query(
    query_texts=["编程学习"],
    n_results=5,
    where={"published": True}  # 只在已发布文章中搜索
)

# 组合过滤
results = collection.query(
    query_texts=["机器学习"],
    n_results=3,
    where={
        "category": "ai",
        "difficulty": {"$gte": 3}
    }
)

# 使用 where_document 过滤文档内容
results = collection.query(
    query_texts=["Python"],
    n_results=5,
    where_document={"$contains": "Python"}  # 文档包含"Python"
)
print(f"包含Python的文档: {results['ids']}")
```

**6. 元数据更新与管理**

```python
# 更新单个文档的元数据
collection.update(
    ids=["art3"],
    metadatas=[{
        "category": "web",
        "language": "javascript",
        "difficulty": 2,
        "author": "王五",
        "published": True,  # 改为已发布
        "date": "2024-03-10"
    }]
)

# 批量更新元数据
collection.update(
    ids=["art1", "art2"],
    metadatas=[
        {"category": "programming", "language": "python", "difficulty": 1, "author": "张三", "published": True, "date": "2024-01-15", "viewed": True},
        {"category": "ai", "language": "python", "difficulty": 3, "author": "李四", "published": True, "date": "2024-02-20", "viewed": True}
    ]
)

# 查看元数据
result = collection.get(ids=["art3"], include=["metadatas"])
print(f"更新后的元数据: {result['metadatas'][0]}")
```

### 总结

- 元数据是文档的键值对，用于过滤和描述，不参与相似度计算。
- 过滤操作符：$eq/$ne/$gt/$gte/$lt/$lte/$in/$nin，逻辑组合用 $and/$or。
- 元数据存储在 SQLite，查询时先过滤再搜索，提升效率。
- 设计原则：可枚举属性用元数据，大文本用文档，字段名简短。
- **关键要点**：where 过滤元数据，where_document 过滤文档内容；元数据值类型要一致。
- **常见误区**：元数据值类型不一致导致过滤失败；忽视 $and/$or 的嵌套；用元数据存大段文本。

---

# 第 3 章 查询与检索

查询与检索是 ChromaDB 的核心功能。本章讲解相似性查询、元数据过滤查询和查询优化技巧，帮助您构建高效的语义搜索应用。

---

## 第 8 讲：相似性查询基础

### 概念

**相似性查询（Similarity Search）** 是向量数据库的核心功能，通过计算查询向量与存储向量的距离，返回最相似的文档。ChromaDB 的 `query()` 方法支持：

- **query_texts**：用文本查询（自动嵌入）。
- **query_embeddings**：用向量查询（直接提供）。
- **n_results**：返回结果数量。
- **where**：元数据过滤。
- **where_document**：文档内容过滤。
- **include**：控制返回内容。

查询返回结果包含：

- `ids`：匹配文档的 ID。
- `documents`：匹配文档的文本。
- `metadatas`：匹配文档的元数据。
- `distances`：距离值（越小越相似，cosine 距离）。
- `embeddings`：匹配文档的向量（需 include）。

### 原理

**1. 查询的执行流程**

1. 将查询文本转为向量（用集合的嵌入函数）。
2. 在 HNSW 索引中搜索最相似的向量。
3. 返回相似度最高的 N 个文档及其距离。

**2. 距离值的含义**

距离值取决于距离度量：

- **cosine**：范围 [0, 2]，0 表示完全相同，1 表示正交，2 表示完全相反。越小越相似。
- **l2**：欧几里得距离，范围 [0, ∞)，0 表示完全相同。越小越相似。
- **ip**：内积，范围 (-∞, ∞)，越大越相似（注意：与其他度量相反）。

**3. 多查询**

query() 支持同时执行多个查询，返回嵌套列表：

```python
results = collection.query(query_texts=["query1", "query2"], n_results=3)
# results['ids'] = [['id1', 'id2', 'id3'], ['id4', 'id5', 'id6']]
# 外层列表对应每个查询，内层列表是该查询的结果
```

**4. include 参数**

- `metadatas`：返回元数据（默认）。
- `documents`：返回文档文本（默认）。
- `distances`：返回距离（默认）。
- `embeddings`：返回向量（默认不返回，数据量大）。

### 例子

**1. 基本文本查询**

```python
import chromadb

client = chromadb.PersistentClient(path="./chroma_data")
collection = client.get_or_create_collection(name="knowledge")

# 添加知识库文档
collection.add(
    documents=[
        "Python 是一种解释型、高级编程语言，由 Guido van Rossum 创建。",
        "JavaScript 是一种用于网页开发的脚本语言，使网页具有交互性。",
        "机器学习是人工智能的一个分支，使计算机能够从数据中学习。",
        "数据库是结构化数据的集合，通常存储在计算机系统中。",
        "Docker 是一个开源的容器化平台，用于开发、部署和运行应用程序。",
        "Kubernetes 是用于自动化部署、扩展和管理容器化应用程序的开源系统。"
    ],
    metadatas=[
        {"topic": "programming", "language": "python"},
        {"topic": "programming", "language": "javascript"},
        {"topic": "ai", "language": "python"},
        {"topic": "database", "language": "sql"},
        {"topic": "devops", "language": "docker"},
        {"topic": "devops", "language": "kubernetes"}
    ],
    ids=["doc1", "doc2", "doc3", "doc4", "doc5", "doc6"]
)

# 基本查询
results = collection.query(
    query_texts=["什么是 Python"],
    n_results=3
)

# 解析结果
print("查询: 什么是 Python")
for i, (id, doc, dist) in enumerate(zip(
    results['ids'][0],
    results['documents'][0],
    results['distances'][0]
)):
    print(f"  {i+1}. [距离={dist:.4f}] {id}: {doc[:50]}...")
```

**2. 多查询**

```python
# 同时执行多个查询
results = collection.query(
    query_texts=[
        "编程语言介绍",
        "容器技术",
        "人工智能"
    ],
    n_results=2
)

# 结果是嵌套列表
for q_idx, query in enumerate(["编程语言介绍", "容器技术", "人工智能"]):
    print(f"\n查询{q_idx+1}: {query}")
    for i, (id, doc, dist) in enumerate(zip(
        results['ids'][q_idx],
        results['documents'][q_idx],
        results['distances'][q_idx]
    )):
        print(f"  {i+1}. [距离={dist:.4f}] {doc[:50]}...")
```

**3. 用向量查询**

```python
# 场景：已有查询向量（如由其他模型生成）
from chromadb.utils import embedding_functions

# 用同一嵌入函数生成查询向量
ef = collection._embedding_function
query_embedding = ef(["什么是数据库"])[0]

results = collection.query(
    query_embeddings=[query_embedding],
    n_results=3
)

print("向量查询结果:")
for id, doc, dist in zip(
    results['ids'][0],
    results['documents'][0],
    results['distances'][0]
):
    print(f"  [距离={dist:.4f}] {doc[:50]}...")
```

**4. 控制返回内容**

```python
# 只返回ID和距离
results = collection.query(
    query_texts=["Python"],
    n_results=3,
    include=["distances"]
)
print(f"只含距离: {results}")

# 返回所有内容（包括向量）
results = collection.query(
    query_texts=["Python"],
    n_results=2,
    include=["documents", "metadatas", "distances", "embeddings"]
)
print(f"向量维度: {len(results['embeddings'][0][0])}")
```

**5. 结果解析工具**

```python
def format_query_results(results, query_text="", top_k=None):
    """格式化查询结果，便于阅读"""
    if top_k:
        results = {
            'ids': [r[:top_k] for r in results['ids']],
            'documents': [r[:top_k] for r in results['documents']],
            'metadatas': [r[:top_k] for r in results['metadatas']],
            'distances': [r[:top_k] for r in results['distances']]
        }

    output = []
    for q_idx in range(len(results['ids'])):
        if query_text:
            output.append(f"查询: {query_text[q_idx] if isinstance(query_text, list) else query_text}")
        for i in range(len(results['ids'][q_idx])):
            doc = results['documents'][q_idx][i]
            dist = results['distances'][q_idx][i]
            meta = results['metadatas'][q_idx][i]
            doc_id = results['ids'][q_idx][i]
            output.append(
                f"  {i+1}. [相似度={1-dist:.4f}] ID={doc_id}\n"
                f"     文档: {doc[:80]}...\n"
                f"     元数据: {meta}"
            )
    return "\n".join(output)

# 使用
results = collection.query(
    query_texts=["机器学习是什么"],
    n_results=3
)
print(format_query_results(results, "机器学习是什么"))
```

**6. 相似度阈值过滤**

```python
def query_with_threshold(collection, query_text, threshold=0.5, n_results=10):
    """查询并过滤相似度低于阈值的结果"""
    results = collection.query(
        query_texts=[query_text],
        n_results=n_results
    )

    filtered = {'ids': [], 'documents': [], 'metadatas': [], 'distances': []}
    for i, dist in enumerate(results['distances'][0]):
        if dist <= threshold:  # 距离小于阈值才保留
            filtered['ids'].append(results['ids'][0][i])
            filtered['documents'].append(results['documents'][0][i])
            filtered['metadatas'].append(results['metadatas'][0][i])
            filtered['distances'].append(dist)

    return filtered

# 使用：只返回距离<=0.5的结果
filtered = query_with_threshold(collection, "Python 编程", threshold=0.5)
print(f"过滤后结果数: {len(filtered['ids'])}")
```

### 总结

- query() 是核心查询方法，支持文本查询和向量查询。
- 距离值越小越相似（cosine/l2），ip 相反越大越相似。
- 多查询返回嵌套列表，外层对应查询，内层对应结果。
- include 控制返回内容，默认不返回 embeddings。
- **关键要点**：相似度阈值过滤提升结果质量；多查询批量处理提升效率。
- **常见误区**：混淆距离和相似度；忽视 include 参数导致返回过多数据；不理解嵌套结果结构。

---

## 第 9 讲：元数据过滤查询

### 概念

**元数据过滤查询** 在相似性搜索的同时，按元数据条件过滤结果。这是 ChromaDB 的强大功能，能实现"在特定范围内做语义搜索"。

过滤方式：

- **where**：按元数据过滤文档。
- **where_document**：按文档内容过滤（`$contains`）。

两者可同时使用，实现精确的检索控制。

### 原理

**1. 过滤的执行顺序**

ChromaDB 的过滤查询执行顺序：

1. 按 where 条件过滤文档（元数据匹配）。
2. 按 where_document 条件过滤（内容匹配）。
3. 在过滤后的文档集中做向量相似性搜索。
4. 返回最相似的 N 个结果。

这种"先过滤后搜索"的方式确保结果既满足过滤条件又语义相关。

**2. where_document 的 $contains**

`where_document={"$contains": "关键词"}` 过滤包含指定字符串的文档。这是简单的子串匹配，不支持正则。

**3. 过滤与性能**

- 过滤减少搜索范围，通常提升性能。
- 但过于复杂的过滤条件可能增加元数据查询开销。
- 建议为常用过滤字段设计一致的元数据结构。

### 例子

**1. 基本元数据过滤**

```python
import chromadb

client = chromadb.PersistentClient(path="./chroma_data")
collection = client.get_or_create_collection(name="articles")

# 添加测试数据
collection.add(
    documents=[
        "Python 入门教程",
        "Java 编程指南",
        "机器学习实战",
        "深度学习原理",
        "Web 开发入门",
        "数据库设计"
    ],
    metadatas=[
        {"category": "programming", "difficulty": 1, "year": 2024},
        {"category": "programming", "difficulty": 2, "year": 2023},
        {"category": "ai", "difficulty": 3, "year": 2024},
        {"category": "ai", "difficulty": 4, "year": 2024},
        {"category": "web", "difficulty": 2, "year": 2023},
        {"category": "database", "difficulty": 2, "year": 2024}
    ],
    ids=["a1", "a2", "a3", "a4", "a5", "a6"]
)

# 在编程类文章中搜索
results = collection.query(
    query_texts=["学习编程"],
    n_results=3,
    where={"category": "programming"}
)
print("编程类文章:")
for id, doc in zip(results['ids'][0], results['documents'][0]):
    print(f"  {id}: {doc}")
```

**2. 复合条件过滤**

```python
# 多条件AND（逗号分隔）
results = collection.query(
    query_texts=["编程"],
    n_results=3,
    where={
        "category": "programming",
        "difficulty": {"$lte": 1}
    }
)
print("编程且难度<=1:")
for id, doc in zip(results['ids'][0], results['documents'][0]):
    print(f"  {id}: {doc}")

# 范围查询
results = collection.query(
    query_texts=["技术文章"],
    n_results=5,
    where={
        "difficulty": {"$gte": 2, "$lte": 3}
    }
)
print("\n难度2-3:")
for id, doc in zip(results['ids'][0], results['documents'][0]):
    print(f"  {id}: {doc}")

# $in 查询
results = collection.query(
    query_texts=["学习"],
    n_results=5,
    where={"category": {"$in": ["ai", "database"]}}
)
print("\nAI或数据库:")
for id, doc in zip(results['ids'][0], results['documents'][0]):
    print(f"  {id}: {doc}")
```

**3. 逻辑组合过滤**

```python
# $or 查询
results = collection.query(
    query_texts=["编程"],
    n_results=5,
    where={
        "$or": [
            {"category": "programming"},
            {"category": "web"}
        ]
    }
)
print("编程或Web:")
for id, doc in zip(results['ids'][0], results['documents'][0]):
    print(f"  {id}: {doc}")

# 复杂逻辑
results = collection.query(
    query_texts=["技术"],
    n_results=5,
    where={
        "$and": [
            {"year": 2024},
            {"$or": [
                {"category": "ai"},
                {"category": "database"}
            ]},
            {"difficulty": {"$gte": 2}}
        ]
    }
)
print("\n2024年AI或数据库且难度>=2:")
for id, doc in zip(results['ids'][0], results['documents'][0]):
    print(f"  {id}: {doc}")
```

**4. 文档内容过滤**

```python
# where_document: 按文档内容过滤
results = collection.query(
    query_texts=["学习"],
    n_results=5,
    where_document={"$contains": "Python"}
)
print("包含Python的文档:")
for id, doc in zip(results['ids'][0], results['documents'][0]):
    print(f"  {id}: {doc}")

# 同时使用 where 和 where_document
results = collection.query(
    query_texts=["编程"],
    n_results=5,
    where={"category": "programming"},
    where_document={"$contains": "入门"}
)
print("\n编程类且包含'入门':")
for id, doc in zip(results['ids'][0], results['documents'][0]):
    print(f"  {id}: {doc}")
```

**5. 实际应用：多维度检索**

```python
# 场景：知识库检索，支持按时间、类别、难度多维过滤

def search_knowledge_base(collection, query, category=None, difficulty=None,
                          year=None, contains=None, n_results=5):
    """多维度知识库检索"""
    where_conditions = []

    if category:
        if isinstance(category, list):
            where_conditions.append({"category": {"$in": category}})
        else:
            where_conditions.append({"category": category})

    if difficulty:
        if isinstance(difficulty, tuple):
            where_conditions.append({
                "difficulty": {"$gte": difficulty[0], "$lte": difficulty[1]}
            })
        else:
            where_conditions.append({"difficulty": difficulty})

    if year:
        where_conditions.append({"year": year})

    where = None
    if len(where_conditions) == 1:
        where = where_conditions[0]
    elif len(where_conditions) > 1:
        where = {"$and": where_conditions}

    query_kwargs = {
        "query_texts": [query],
        "n_results": n_results
    }
    if where:
        query_kwargs["where"] = where
    if contains:
        query_kwargs["where_document"] = {"$contains": contains}

    return collection.query(**query_kwargs)

# 使用：搜索2024年AI类难度3-4的文章
results = search_knowledge_base(
    collection,
    query="学习AI",
    category="ai",
    difficulty=(3, 4),
    year=2024
)
print("2024年AI类难度3-4:")
for id, doc in zip(results['ids'][0], results['documents'][0]):
    print(f"  {id}: {doc}")

# 搜索包含"入门"的编程或Web文章
results = search_knowledge_base(
    collection,
    query="入门",
    category=["programming", "web"],
    contains="入门"
)
print("\n编程或Web且包含'入门':")
for id, doc in zip(results['ids'][0], results['documents'][0]):
    print(f"  {id}: {doc}")
```

### 总结

- where 按元数据过滤，where_document 按文档内容过滤，可同时使用。
- 过滤操作符：$eq/$ne/$gt/$gte/$lt/$lte/$in/$nin，逻辑用 $and/$or。
- 执行顺序：先过滤后搜索，确保结果满足条件且语义相关。
- 多维度检索通过组合条件实现精确控制。
- **关键要点**：where_document 只支持 $contains 子串匹配；复杂过滤用 $and/$or 嵌套。
- **常见误区**：混淆 where 和 where_document；忽视过滤条件的性能影响；元数据类型不一致。

---

## 第 10 讲：查询优化与最佳实践

### 概念

本讲讲解 ChromaDB 查询的性能优化和最佳实践，包括：

- **n_results 的合理设置**：避免过多结果。
- **HNSW 参数调优**：调整 search_ef 平衡精度与速度。
- **批量查询**：用多查询代替循环单查询。
- **嵌入缓存**：避免重复计算嵌入。
- **数据预处理**：文档分块和清洗。
- **索引重建**：定期优化索引。

### 原理

**1. HNSW 查询参数**

- **search_ef**：查询时搜索宽度，越大越精确但越慢。
- 默认值通常足够，高精度场景可调大。
- 可在创建集合时通过 metadata 设置：`{"hnsw:search_ef": 200}`。

**2. 嵌入计算的开销**

- 嵌入计算是查询的主要耗时（尤其云端 API）。
- 相同查询的嵌入可缓存复用。
- 批量查询比逐条查询效率高（API 批量调用）。

**3. 文档分块策略**

长文档应分块后存储，原因：

- 嵌入模型有输入长度限制（如 512 token）。
- 分块提升检索精度（小段语义更聚焦）。
- 返回结果更精确（避免返回无关的长文档）。

常用分块方法：

- **固定长度分块**：按字符数分块，简单但可能截断语义。
- **句子分块**：按句子分块，语义完整。
- **递归分块**：先按段落，再按句子，兼顾层次。
- **重叠分块**：相邻块有重叠，避免边界信息丢失。

### 例子

**1. HNSW 参数调优**

```python
import chromadb
import time

# 创建不同参数的集合对比
client = chromadb.PersistentClient(path="./chroma_data")

# 默认参数
collection_default = client.get_or_create_collection("default_hnsw")

# 高精度参数
collection_precise = client.get_or_create_collection(
    "precise_hnsw",
    metadata={
        "hnsw:space": "cosine",
        "hnsw:M": 48,                 # 更高连接数
        "hnsw:construction_ef": 400,  # 构建更精细
        "hnsw:search_ef": 200         # 查询更精确
    }
)

# 高速度参数
collection_fast = client.get_or_create_collection(
    "fast_hnsw",
    metadata={
        "hnsw:space": "cosine",
        "hnsw:M": 16,                # 较低连接数
        "hnsw:construction_ef": 100,
        "hnsw:search_ef": 50         # 查询更快
    }
)

# 添加相同数据并对比查询性能
import random
docs = [f"文档内容 {i} 主题 {random.randint(0, 100)}" for i in range(10000)]

for col in [collection_default, collection_precise, collection_fast]:
    col.add(
        documents=docs,
        ids=[f"doc_{i}" for i in range(10000)]
    )

# 对比查询性能
query = "测试查询"
for name, col in [("默认", collection_default), ("高精度", collection_precise), ("高速度", collection_fast)]:
    start = time.time()
    for _ in range(100):
        col.query(query_texts=[query], n_results=10)
    elapsed = time.time() - start
    print(f"{name}: {elapsed:.2f}s (100次查询)")
```

**2. 嵌入缓存**

```python
from functools import lru_cache

class CachedEmbeddingFunction:
    """带缓存的嵌入函数"""

    def __init__(self, embedding_function):
        self.embedding_function = embedding_function
        self.cache = {}

    def __call__(self, texts):
        # 检查缓存
        cached = []
        to_compute = []
        indices = []
        for i, text in enumerate(texts):
            if text in self.cache:
                cached.append((i, self.cache[text]))
            else:
                to_compute.append(text)
                indices.append(i)

        # 计算未缓存的
        if to_compute:
            computed = self.embedding_function(to_compute)
            for text, emb in zip(to_compute, computed):
                self.cache[text] = emb

        # 组装结果
        result = [None] * len(texts)
        for i, emb in cached:
            result[i] = emb
        for i, emb in zip(indices, computed):
            result[i] = emb

        return result

# 使用
from chromadb.utils import embedding_functions

original_ef = embedding_functions.SentenceTransformerEmbeddingFunction(
    model_name="all-MiniLM-L6-v2"
)
cached_ef = CachedEmbeddingFunction(original_ef)

collection = client.get_or_create_collection(
    name="cached_collection",
    embedding_function=cached_ef
)

# 重复查询相同文本时，嵌入从缓存读取
collection.query(query_texts=["测试"], n_results=5)
collection.query(query_texts=["测试"], n_results=5)  # 第二次用缓存
```

**3. 文档分块**

```python
import re

def chunk_text(text, chunk_size=500, overlap=50):
    """将长文本分块，带重叠"""
    chunks = []
    start = 0
    while start < len(text):
        end = start + chunk_size
        chunk = text[start:end]
        chunks.append(chunk)
        start = end - overlap  # 重叠部分
    return chunks

def chunk_by_sentences(text, max_length=500):
    """按句子分块"""
    sentences = re.split(r'[。！？.!?]', text)
    chunks = []
    current_chunk = ""

    for sentence in sentences:
        if not sentence.strip():
            continue
        if len(current_chunk) + len(sentence) < max_length:
            current_chunk += sentence + "。"
        else:
            if current_chunk:
                chunks.append(current_chunk)
            current_chunk = sentence + "。"

    if current_chunk:
        chunks.append(current_chunk)

    return chunks

# 使用：将长文档分块后存储
long_text = """
Python 是一种广泛使用的高级编程语言。它由 Guido van Rossum 于 1991 年首次发布。
Python 强调代码可读性，使用缩进而非花括号来界定代码块。
Python 支持多种编程范式，包括面向对象、命令式、函数式和过程式编程。
Python 拥有丰富的标准库，涵盖网络、文件处理、加密等众多领域。
Python 的生态系统非常庞大，有大量的第三方库可供使用。
在数据科学领域，Python 是最受欢迎的编程语言之一。
NumPy、Pandas、Matplotlib 等库使 Python 成为数据分析的首选。
在机器学习和深度学习领域，Python 同样占据主导地位。
TensorFlow、PyTorch 等框架都支持 Python。
Python 也广泛用于 Web 开发，Django 和 Flask 是流行的框架。
"""

# 分块
chunks = chunk_by_sentences(long_text, max_length=100)
print(f"分块数: {len(chunks)}")
for i, chunk in enumerate(chunks):
    print(f"  块{i+1}: {chunk[:50]}...")

# 存储到ChromaDB
collection = client.get_or_create_collection(name="chunked_docs")
collection.add(
    documents=chunks,
    metadatas=[{"chunk_idx": i, "source": "python_intro"} for i in range(len(chunks))],
    ids=[f"chunk_{i}" for i in range(len(chunks))]
)
```

**4. 批量查询优化**

```python
import time

# 场景：有多个查询需要执行

# 差：循环单查询
queries = [f"查询 {i}" for i in range(100)]

start = time.time()
for q in queries:
    collection.query(query_texts=[q], n_results=5)
sequential_time = time.time() - start
print(f"顺序查询: {sequential_time:.2f}s")

# 好：批量查询
start = time.time()
results = collection.query(query_texts=queries, n_results=5)
batch_time = time.time() - start
print(f"批量查询: {batch_time:.2f}s")
print(f"加速比: {sequential_time/batch_time:.1f}x")
```

**5. 查询结果后处理**

```python
def deduplicate_results(results, key_field='documents'):
    """去重查询结果"""
    seen = set()
    unique = {'ids': [], 'documents': [], 'metadatas': [], 'distances': []}

    for i, doc in enumerate(results['documents'][0]):
        if doc not in seen:
            seen.add(doc)
            unique['ids'].append(results['ids'][0][i])
            unique['documents'].append(doc)
            unique['metadatas'].append(results['metadatas'][0][i])
            unique['distances'].append(results['distances'][0][i])

    return unique

def rerank_by_metadata(results, metadata_key, ascending=True):
    """按元数据重新排序"""
    combined = list(zip(
        results['ids'][0],
        results['documents'][0],
        results['metadatas'][0],
        results['distances'][0]
    ))

    combined.sort(
        key=lambda x: x[2].get(metadata_key, 0),
        reverse=not ascending
    )

    return {
        'ids': [[c[0] for c in combined]],
        'documents': [[c[1] for c in combined]],
        'metadatas': [[c[2] for c in combined]],
        'distances': [[c[3] for c in combined]]
    }

# 使用
results = collection.query(query_texts=["Python"], n_results=10)
deduped = deduplicate_results(results)
print(f"去重前: {len(results['ids'][0])}, 去重后: {len(deduped['ids'])}")
```

### 总结

- HNSW 参数：search_ef 调大提升精度但降低速度，根据场景平衡。
- 嵌入缓存避免重复计算，显著提升重复查询性能。
- 文档分块提升检索精度，推荐按句子分块带重叠。
- 批量查询比循环单查询效率高数倍。
- **关键要点**：长文档必须分块；重复查询用缓存；多查询用批量。
- **常见误区**：不分块直接存长文档；循环单查询导致性能差；忽视 HNSW 参数调优。

---

# 第 4 章 进阶特性

本章讲解 ChromaDB 的进阶特性，包括嵌入函数与自定义模型、持久化与客户端-服务器模式、多租户与数据隔离。这些特性让 ChromaDB 能胜任生产级应用。

---

## 第 11 讲：嵌入函数与自定义模型

### 概念

**嵌入函数（Embedding Function）** 是将文本转换为向量的函数，是 ChromaDB 的核心组件。ChromaDB 内置多种嵌入函数，也支持自定义。

ChromaDB 支持的嵌入函数：

- **SentenceTransformerEmbeddingFunction**：本地 Sentence Transformers 模型（默认 all-MiniLM-L6-v2）。
- **OpenAIEmbeddingFunction**：OpenAI 的嵌入 API（text-embedding-ada-002 等）。
- **CohereEmbeddingFunction**：Cohere 的嵌入 API。
- **HuggingFaceEmbeddingFunction**：HuggingFace Inference API。
- **ONNXEmbeddingFunction**：ONNX 格式的本地模型。
- **GoogleEmbeddingFunction**：Google Vertex AI 嵌入。
- **自定义嵌入函数**：实现 EmbeddingFunction 接口。

### 原理

**1. 嵌入函数的工作机制**

嵌入函数是一个可调用对象（`__call__`），接收文本列表，返回向量列表：

```python
class EmbeddingFunction:
    def __call__(self, input: List[str]) -> List[List[float]]:
        # 将文本转为向量
        return embeddings
```

ChromaDB 在以下场景调用嵌入函数：

- **add()**：提供 documents 时，调用嵌入函数生成向量。
- **query()**：提供 query_texts 时，调用嵌入函数生成查询向量。
- **update()**：更新 documents 时，重新调用嵌入函数。

**2. 本地 vs 云端嵌入**

| 维度 | 本地模型 | 云端 API |
|------|---------|---------|
| 成本 | 免费 | 按 token 计费 |
| 速度 | 取决于硬件 | 网络延迟 |
| 隐私 | 数据不出本地 | 数据发送到云端 |
| 质量 | 中等（小模型） | 高（大模型） |
| 离线 | 支持 | 不支持 |

**3. 嵌入函数的绑定**

- 嵌入函数在创建集合时绑定。
- 绑定后，该集合的所有 add/query 操作都使用同一函数。
- 更换嵌入函数需创建新集合并重新索引。

**4. 自定义嵌入函数的要求**

- 实现 `__call__(self, input)` 方法。
- 输入：字符串列表。
- 输出：浮点数列表的列表（每个文本一个向量）。
- 向量维度必须一致。

### 例子

**1. 使用内置嵌入函数**

```python
import chromadb
from chromadb.utils import embedding_functions

client = chromadb.PersistentClient(path="./chroma_data")

# 1. 默认嵌入函数（all-MiniLM-L6-v2，384维）
collection_default = client.get_or_create_collection("default_ef")

# 2. 指定SentenceTransformer模型
st_ef = embedding_functions.SentenceTransformerEmbeddingFunction(
    model_name="paraphrase-multilingual-MiniLM-L12-v2"  # 多语言模型
)
collection_st = client.get_or_create_collection(
    "st_collection",
    embedding_function=st_ef
)

# 3. OpenAI嵌入函数
openai_ef = embedding_functions.OpenAIEmbeddingFunction(
    api_key="sk-...",
    model_name="text-embedding-ada-002"  # 1536维
)
collection_openai = client.get_or_create_collection(
    "openai_collection",
    embedding_function=openai_ef
)

# 4. Cohere嵌入函数
cohere_ef = embedding_functions.CohereEmbeddingFunction(
    api_key="...",
    model_name="embed-multilingual-v2.0"
)
collection_cohere = client.get_or_create_collection(
    "cohere_collection",
    embedding_function=cohere_ef
)

# 5. HuggingFace嵌入函数
hf_ef = embedding_functions.HuggingFaceEmbeddingFunction(
    api_key="hf_...",
    model_name="sentence-transformers/all-mpnet-base-v2"
)
collection_hf = client.get_or_create_collection(
    "hf_collection",
    embedding_function=hf_ef
)
```

**2. 对比不同嵌入模型**

```python
# 用相同文本测试不同嵌入函数
test_texts = [
    "Python 是编程语言",
    "Java 也是编程语言",
    "今天天气很好"
]

for name, ef in [
    ("MiniLM-L6-v2", embedding_functions.SentenceTransformerEmbeddingFunction("all-MiniLM-L6-v2")),
    ("MiniLM-L12-v2", embedding_functions.SentenceTransformerEmbeddingFunction("paraphrase-multilingual-MiniLM-L12-v2"))
]:
    embeddings = ef(test_texts)
    dim = len(embeddings[0])

    # 计算前两个文本的相似度（应该较高）
    import math
    def cosine_similarity(v1, v2):
        dot = sum(a*b for a, b in zip(v1, v2))
        norm1 = math.sqrt(sum(a*a for a in v1))
        norm2 = math.sqrt(sum(b*b for b in v2))
        return dot / (norm1 * norm2)

    sim_12 = cosine_similarity(embeddings[0], embeddings[1])  # Python vs Java
    sim_13 = cosine_similarity(embeddings[0], embeddings[2])  # Python vs 天气

    print(f"{name} (维度={dim}):")
    print(f"  Python-Java相似度: {sim_12:.4f}")
    print(f"  Python-天气相似度: {sim_13:.4f}")
```

**3. 自定义嵌入函数**

```python
from typing import List
import hashlib

class SimpleHashEmbedding:
    """简单的哈希嵌入函数（仅用于演示，不推荐生产使用）"""

    def __init__(self, dim=256):
        self.dim = dim

    def __call__(self, input: List[str]) -> List[List[float]]:
        embeddings = []
        for text in input:
            # 用哈希生成伪向量
            vector = [0.0] * self.dim
            for word in text.split():
                hash_val = int(hashlib.md5(word.encode()).hexdigest(), 16)
                vector[hash_val % self.dim] += 1.0

            # 归一化
            magnitude = sum(v*v for v in vector) ** 0.5
            if magnitude > 0:
                vector = [v / magnitude for v in vector]

            embeddings.append(vector)
        return embeddings

# 使用自定义嵌入函数
custom_ef = SimpleHashEmbedding(dim=256)
collection_custom = client.get_or_create_collection(
    "custom_ef_collection",
    embedding_function=custom_ef
)

collection_custom.add(
    documents=["测试文档1", "测试文档2"],
    ids=["d1", "d2"]
)

results = collection_custom.query(query_texts=["测试"], n_results=2)
print(f"自定义嵌入查询: {results['ids']}")
```

**4. 使用 Ollama 本地大模型嵌入**

```python
import requests
from typing import List

class OllamaEmbeddingFunction:
    """使用本地Ollama服务生成嵌入"""

    def __init__(self, model_name="nomic-embed-text", host="http://localhost:11434"):
        self.model_name = model_name
        self.host = host

    def __call__(self, input: List[str]) -> List[List[float]]:
        embeddings = []
        for text in input:
            response = requests.post(
                f"{self.host}/api/embeddings",
                json={"model": self.model_name, "prompt": text}
            )
            if response.status_code == 200:
                embeddings.append(response.json()["embedding"])
            else:
                raise Exception(f"Ollama API错误: {response.status_code}")
        return embeddings

# 使用（需先安装Ollama并拉取模型）
# ollama pull nomic-embed-text
ollama_ef = OllamaEmbeddingFunction(model_name="nomic-embed-text")
collection_ollama = client.get_or_create_collection(
    "ollama_collection",
    embedding_function=ollama_ef
)

collection_ollama.add(
    documents=["Ollama 本地嵌入", "保护数据隐私"],
    ids=["o1", "o2"]
)
```

**5. 嵌入函数的注意事项**

```python
# 注意1：同一集合必须用同一嵌入函数
collection = client.get_or_create_collection(
    "consistent_ef",
    embedding_function=embedding_functions.SentenceTransformerEmbeddingFunction()
)

# 添加文档（用绑定的嵌入函数）
collection.add(documents=["文档1"], ids=["1"])

# 查询（自动用同一嵌入函数）
collection.query(query_texts=["查询"], n_results=1)

# 注意2：不能混用不同维度的向量
# 错误示例：
# collection.add(embeddings=[[0.1]*384], ids=["a"])  # 384维
# collection.add(embeddings=[[0.1]*768], ids=["b"])  # 768维，报错！

# 注意3：更换嵌入函数需重建集合
# 不能直接修改集合的嵌入函数
# 需创建新集合并迁移数据
```

### 总结

- ChromaDB 内置多种嵌入函数：SentenceTransformer（本地）、OpenAI/Cohere/HuggingFace（云端）。
- 本地模型免费且隐私，云端模型质量高但收费。
- 自定义嵌入函数实现 `__call__` 方法，输入文本列表输出向量列表。
- 同一集合必须用同一嵌入函数，更换需重建集合。
- **关键要点**：多语言场景用 paraphrase-multilingual 模型；隐私敏感用本地模型。
- **常见误区**：混用不同维度的向量；忽视嵌入函数的一致性；用简单哈希做嵌入（质量差）。

---

## 第 12 讲：持久化与客户端-服务器模式

### 概念

ChromaDB 支持三种运行模式：

- **EphemeralClient（内存模式）**：数据存内存，进程结束消失。适合测试。
- **PersistentClient（持久化模式）**：数据存本地磁盘。适合单机应用。
- **HttpClient（客户端-服务器模式）**：连接独立的 ChromaDB 服务器。适合生产环境。

### 原理

**1. EphemeralClient（内存模式）**

- 数据全部存内存，速度最快。
- 进程结束后数据消失。
- 适合单元测试和临时计算。

**2. PersistentClient（持久化模式）**

- 数据自动持久化到指定目录。
- 使用 SQLite 存储元数据，DuckDB/HNSW 存储向量。
- 重启后数据自动加载。
- 适合单机应用和开发环境。

**3. HttpClient（客户端-服务器模式）**

- ChromaDB 作为独立服务器运行。
- 客户端通过 HTTP API 连接。
- 支持多客户端共享同一数据库。
- 适合生产环境和多服务架构。

**4. 服务器部署**

ChromaDB 服务器可通过以下方式部署：

- **Docker**：官方 Docker 镜像。
- **Python 包**：`chroma run` 命令启动。
- **云服务**：Chroma Cloud（托管服务）。

### 例子

**1. 三种客户端模式**

```python
import chromadb

# 1. 内存模式（测试用）
client_memory = chromadb.EphemeralClient()
collection = client_memory.create_collection("test")
collection.add(documents=["测试"], ids=["1"])
# 进程结束后数据消失

# 2. 持久化模式（单机应用）
client_persistent = chromadb.PersistentClient(path="./chroma_data")
collection = client_persistent.get_or_create_collection("docs")
collection.add(documents=["持久化数据"], ids=["1"])
# 重启后数据仍在

# 3. 客户端-服务器模式（生产环境）
client_http = chromadb.HttpClient(
    host="localhost",
    port=8000
)
collection = client_http.get_or_create_collection("prod_docs")
collection.add(documents=["生产数据"], ids=["1"])
# 数据存在服务器端
```

**2. 启动 ChromaDB 服务器**

```bash
# 方式1：Python包启动
pip install chromadb
chroma run --host 0.0.0.0 --port 8000 --path ./chroma_data

# 方式2：Docker启动
docker run -d \
    --name chromadb \
    -p 8000:8000 \
    -v ./chroma_data:/chroma/chroma \
    -e IS_PERSISTENT=TRUE \
    -e ANONYMIZED_TELEMETRY=FALSE \
    chromadb/chroma:latest

# 方式3：Docker Compose
# docker-compose.yml
cat > docker-compose.yml << 'EOF'
version: '3'
services:
  chromadb:
    image: chromadb/chroma:latest
    container_name: chromadb
    ports:
      - "8000:8000"
    volumes:
      - ./chroma_data:/chroma/chroma
    environment:
      - IS_PERSISTENT=TRUE
      - ANONYMIZED_TELEMETRY=FALSE
    restart: unless-stopped
EOF

docker-compose up -d
```

**3. 连接服务器的客户端**

```python
import chromadb

# 基本连接
client = chromadb.HttpClient(host="localhost", port=8000)

# 带认证的连接（如果服务器配置了认证）
client = chromadb.HttpClient(
    host="localhost",
    port=8000,
    headers={"Authorization": "Bearer your-token"}
)

# 使用SSL
client = chromadb.HttpClient(
    host="chroma.example.com",
    port=443,
    ssl=True
)

# 测试连接
try:
    collections = client.list_collections()
    print(f"连接成功，现有集合: {[c.name for c in collections]}")
except Exception as e:
    print(f"连接失败: {e}")
```

**4. 持久化模式的数据管理**

```python
import chromadb
import os
import shutil

# 持久化客户端
client = chromadb.PersistentClient(path="./chroma_data")

# 创建集合并添加数据
collection = client.get_or_create_collection("persistent_docs")
collection.add(
    documents=["文档1", "文档2", "文档3"],
    metadatas=[{"idx": i} for i in range(3)],
    ids=["d1", "d2", "d3"]
)

# 查看数据目录
print("数据目录内容:")
for item in os.listdir("./chroma_data"):
    print(f"  {item}")

# 重启后数据仍在（模拟重启）
client2 = chromadb.PersistentClient(path="./chroma_data")
collection2 = client2.get_collection("persistent_docs")
print(f"重启后文档数: {collection2.count()}")

# 备份数据
shutil.copytree("./chroma_data", "./chroma_data_backup")
print("已备份")

# 恢复数据
shutil.rmtree("./chroma_data")
shutil.copytree("./chroma_data_backup", "./chroma_data")
print("已恢复")
```

**5. 多客户端共享服务器**

```python
# 场景：多个服务共享同一ChromaDB服务器

# 服务A：写入数据
client_a = chromadb.HttpClient(host="localhost", port=8000)
collection = client_a.get_or_create_collection("shared_docs")
collection.add(
    documents=["服务A写入的数据"],
    ids=["a1"]
)

# 服务B：读取数据
client_b = chromadb.HttpClient(host="localhost", port=8000)
collection = client_b.get_collection("shared_docs")
results = collection.query(query_texts=["数据"], n_results=1)
print(f"服务B读取: {results['documents']}")

# 服务C：管理数据
client_c = chromadb.HttpClient(host="localhost", port=8000)
for col in client_c.list_collections():
    print(f"集合: {col.name}, 文档数: {client_c.get_collection(col.name).count()}")
```

**6. 环境适配的工具函数**

```python
import chromadb
import os

def get_chroma_client():
    """根据环境获取ChromaDB客户端"""
    env = os.getenv("CHROMA_ENV", "development")

    if env == "test":
        # 测试环境：内存模式
        return chromadb.EphemeralClient()

    elif env == "development":
        # 开发环境：本地持久化
        return chromadb.PersistentClient(path="./chroma_data")

    elif env == "production":
        # 生产环境：连接服务器
        return chromadb.HttpClient(
            host=os.getenv("CHROMA_HOST", "localhost"),
            port=int(os.getenv("CHROMA_PORT", 8000))
        )

    else:
        raise ValueError(f"未知环境: {env}")

# 使用
client = get_chroma_client()
collection = client.get_or_create_collection("env_aware")
```

### 总结

- 三种模式：EphemeralClient（内存，测试）、PersistentClient（持久化，单机）、HttpClient（服务器，生产）。
- 持久化模式数据存本地目录，重启不丢失。
- 服务器模式支持多客户端共享，适合生产环境。
- 服务器可用 Python 包或 Docker 部署。
- **关键要点**：根据环境选择模式；持久化目录可备份恢复；生产环境用 HttpClient。
- **常见误区**：测试用持久化模式导致数据污染；生产用内存模式导致数据丢失；忽视服务器认证。

---

## 第 13 讲：多租户与数据隔离

### 概念

**多租户（Multi-tenancy）** 是指多个用户或组织共享同一系统，但数据相互隔离。在 ChromaDB 中，多租户通过以下方式实现：

- **集合级隔离**：每个租户一个集合。
- **数据库级隔离**：每个租户一个数据库（ChromaDB 0.5.0+）。
- **元数据隔离**：所有租户共享集合，用元数据区分。

### 原理

**1. 集合级隔离**

- 每个租户创建独立集合（如 `tenant_user1_docs`）。
- 优点：数据物理隔离，查询无需过滤。
- 缺点：集合数量多时管理复杂；跨租户统计需查询多个集合。

**2. 数据库级隔离**

- ChromaDB 0.5.0+ 支持多数据库。
- 每个租户一个数据库，完全隔离。
- 优点：隔离最彻底。
- 缺点：管理开销大。

**3. 元数据隔离**

- 所有租户共享集合，用 `tenant_id` 元数据区分。
- 查询时用 `where={"tenant_id": "xxx"}` 过滤。
- 优点：集合少，管理简单。
- 缺点：查询需带过滤条件，易遗漏导致数据泄露。

**4. 隔离方案选择**

| 方案 | 隔离强度 | 管理复杂度 | 适合场景 |
|------|---------|-----------|---------|
| 数据库级 | 最强 | 高 | 强合规要求 |
| 集合级 | 强 | 中 | 中等租户数 |
| 元数据级 | 弱 | 低 | 大量小租户 |

### 例子

**1. 集合级隔离**

```python
import chromadb

client = chromadb.PersistentClient(path="./chroma_data")

class CollectionLevelTenant:
    """集合级多租户管理"""

    def __init__(self, client):
        self.client = client

    def get_tenant_collection(self, tenant_id):
        """获取租户的集合"""
        collection_name = f"tenant_{tenant_id}_docs"
        return self.client.get_or_create_collection(collection_name)

    def add_document(self, tenant_id, doc_id, text, metadata=None):
        """为租户添加文档"""
        collection = self.get_tenant_collection(tenant_id)
        meta = metadata or {}
        meta["tenant_id"] = tenant_id
        collection.add(
            documents=[text],
            metadatas=[meta],
            ids=[doc_id]
        )

    def query(self, tenant_id, query_text, n_results=5):
        """租户内查询"""
        collection = self.get_tenant_collection(tenant_id)
        return collection.query(
            query_texts=[query_text],
            n_results=n_results
        )

    def list_tenants(self):
        """列出所有租户"""
        tenants = set()
        for col in self.client.list_collections():
            if col.name.startswith("tenant_") and col.name.endswith("_docs"):
                tenant_id = col.name[8:-5]  # 去掉tenant_和_docs
                tenants.add(tenant_id)
        return list(tenants)

# 使用
tenant_mgr = CollectionLevelTenant(client)

# 为不同租户添加数据
tenant_mgr.add_document("user1", "d1", "用户1的文档")
tenant_mgr.add_document("user2", "d1", "用户2的文档")

# 各自查询（数据隔离）
results1 = tenant_mgr.query("user1", "文档")
results2 = tenant_mgr.query("user2", "文档")
print(f"用户1结果: {results1['documents']}")
print(f"用户2结果: {results2['documents']}")

print(f"所有租户: {tenant_mgr.list_tenants()}")
```

**2. 元数据级隔离**

```python
class MetadataLevelTenant:
    """元数据级多租户管理"""

    def __init__(self, client, collection_name="shared_docs"):
        self.collection = client.get_or_create_collection(collection_name)

    def add_document(self, tenant_id, doc_id, text, metadata=None):
        """添加文档（带tenant_id元数据）"""
        meta = metadata or {}
        meta["tenant_id"] = tenant_id
        self.collection.upsert(
            documents=[text],
            metadatas=[meta],
            ids=[f"{tenant_id}_{doc_id}"]  # ID加租户前缀避免冲突
        )

    def query(self, tenant_id, query_text, n_results=5):
        """租户内查询（必须带tenant_id过滤）"""
        return self.collection.query(
            query_texts=[query_text],
            n_results=n_results,
            where={"tenant_id": tenant_id}  # 关键：过滤租户
        )

    def get_document(self, tenant_id, doc_id):
        """获取文档"""
        return self.collection.get(
            ids=[f"{tenant_id}_{doc_id}"],
            where={"tenant_id": tenant_id}  # 双重验证
        )

    def delete_document(self, tenant_id, doc_id):
        """删除文档"""
        self.collection.delete(
            ids=[f"{tenant_id}_{doc_id}"]
        )

    def count_tenant_docs(self, tenant_id):
        """统计租户文档数"""
        result = self.collection.get(where={"tenant_id": tenant_id})
        return len(result['ids'])

# 使用
tenant_mgr = MetadataLevelTenant(client)

tenant_mgr.add_document("user1", "d1", "用户1的文档A")
tenant_mgr.add_document("user1", "d2", "用户1的文档B")
tenant_mgr.add_document("user2", "d1", "用户2的文档A")

print(f"用户1文档数: {tenant_mgr.count_tenant_docs('user1')}")
print(f"用户2文档数: {tenant_mgr.count_tenant_docs('user2')}")

# 查询时数据隔离
results1 = tenant_mgr.query("user1", "文档")
print(f"用户1查询结果数: {len(results1['ids'][0])}")  # 2
```

**3. 数据库级隔离（ChromaDB 0.5.0+）**

```python
import chromadb

client = chromadb.PersistentClient(path="./chroma_data")

class DatabaseLevelTenant:
    """数据库级多租户管理"""

    def __init__(self, client):
        self.client = client

    def get_tenant_db(self, tenant_id):
        """获取租户的数据库"""
        db_name = f"tenant_{tenant_id}_db"
        try:
            return self.client.get_database(db_name)
        except:
            return self.client.create_database(db_name)

    def get_tenant_collection(self, tenant_id, collection_name="docs"):
        """获取租户的集合"""
        db = self.get_tenant_db(tenant_id)
        return db.get_or_create_collection(collection_name)

    def add_document(self, tenant_id, doc_id, text, metadata=None):
        """添加文档"""
        collection = self.get_tenant_collection(tenant_id)
        collection.add(
            documents=[text],
            metadatas=[metadata or {}],
            ids=[doc_id]
        )

    def query(self, tenant_id, query_text, n_results=5):
        """查询"""
        collection = self.get_tenant_collection(tenant_id)
        return collection.query(
            query_texts=[query_text],
            n_results=n_results
        )

# 使用
tenant_mgr = DatabaseLevelTenant(client)
tenant_mgr.add_document("user1", "d1", "用户1的文档")
tenant_mgr.add_document("user2", "d1", "用户2的文档")
```

**4. 多租户管理器（综合方案）**

```python
class TenantManager:
    """综合多租户管理器"""

    def __init__(self, client, strategy="collection"):
        """
        strategy: collection / metadata / database
        """
        self.client = client
        self.strategy = strategy

    def add_document(self, tenant_id, doc_id, text, metadata=None):
        meta = metadata or {}
        meta["tenant_id"] = tenant_id
        meta["updated_at"] = str(datetime.now())

        if self.strategy == "collection":
            col = self._get_collection(tenant_id)
            col.add(documents=[text], metadatas=[meta], ids=[doc_id])
        elif self.strategy == "metadata":
            col = self.client.get_or_create_collection("shared")
            col.upsert(
                documents=[text],
                metadatas=[meta],
                ids=[f"{tenant_id}_{doc_id}"]
            )

    def query(self, tenant_id, query_text, n_results=5, where=None):
        if self.strategy == "collection":
            col = self._get_collection(tenant_id)
            return col.query(query_texts=[query_text], n_results=n_results, where=where)
        elif self.strategy == "metadata":
            col = self.client.get_or_create_collection("shared")
            filter_where = {"tenant_id": tenant_id}
            if where:
                filter_where = {"$and": [filter_where, where]}
            return col.query(
                query_texts=[query_text],
                n_results=n_results,
                where=filter_where
            )

    def _get_collection(self, tenant_id):
        return self.client.get_or_create_collection(f"tenant_{tenant_id}")

    def get_tenant_stats(self, tenant_id):
        """获取租户统计"""
        if self.strategy == "collection":
            col = self._get_collection(tenant_id)
            return {"count": col.count()}
        elif self.strategy == "metadata":
            col = self.client.get_or_create_collection("shared")
            result = col.get(where={"tenant_id": tenant_id})
            return {"count": len(result['ids'])}

from datetime import datetime
# 使用
mgr = TenantManager(client, strategy="metadata")
mgr.add_document("user1", "d1", "文档1")
print(mgr.get_tenant_stats("user1"))
```

### 总结

- 多租户三种方案：集合级（强隔离）、元数据级（弱隔离）、数据库级（最强隔离）。
- 集合级适合中等租户数，元数据级适合大量小租户。
- 元数据级查询必须带 tenant_id 过滤，防止数据泄露。
- **关键要点**：元数据级用 ID 前缀避免冲突；集合级用命名规范管理；选择方案考虑租户规模。
- **常见误区**：元数据级忘记过滤导致数据泄露；集合过多导致管理混乱；忽视租户间 ID 冲突。

---

# 第 5 章 实战应用

本章聚焦 ChromaDB 的实战应用。从与 LangChain/LlamaIndex 等 LLM 框架的集成，到完整的 RAG 知识库系统案例，帮助您将所学知识融会贯通，构建生产级 AI 应用。

---

## 第 14 讲：与 LangChain/LlamaIndex 集成

### 概念

**LangChain** 是最流行的 LLM 应用开发框架，提供文档加载、分块、嵌入、向量存储、检索、链式调用等完整工具链。**LlamaIndex** 是专注于 RAG（检索增强生成）的框架，在数据连接和索引方面更强大。

ChromaDB 与这两个框架深度集成：

- **LangChain Chroma**：`langchain-chroma` 包提供 ChromaDB 的 LangChain 封装。
- **LlamaIndex ChromaVectorStore**：LlamaIndex 内置 ChromaDB 向量存储支持。

集成的优势：

- **简化开发**：框架封装了底层细节，几行代码完成复杂操作。
- **生态丰富**：可结合框架的其他组件（LLM、Prompt、Chain）。
- **标准化**：遵循框架的接口规范，便于切换其他向量数据库。

### 原理

**1. LangChain 的向量存储抽象**

LangChain 定义了 `VectorStore` 接口，ChromaDB 是其实现之一：

```python
from langchain_chroma import Chroma

# 创建向量存储
vectorstore = Chroma(
    collection_name="docs",
    embedding_function=embeddings,
    persist_directory="./chroma_data"
)

# 添加文档
vectorstore.add_documents(documents)

# 检索
results = vectorstore.similarity_search(query, k=5)
```

**2. LlamaIndex 的向量存储抽象**

LlamaIndex 定义了 `VectorStore` 接口，ChromaDB 通过 `ChromaVectorStore` 实现：

```python
from llama_index.vector_stores.chroma import ChromaVectorStore

# 创建向量存储
vector_store = ChromaVectorStore(chroma_collection=collection)

# 创建存储上下文
storage_context = StorageContext.from_defaults(vector_store=vector_store)

# 创建索引
index = VectorStoreIndex.from_documents(documents, storage_context=storage_context)

# 检索
query_engine = index.as_query_engine()
response = query_engine.query("查询问题")
```

**3. 文档处理流水线**

典型的 RAG 流水线：

```
原始文档 → 加载器 → 分块器 → 嵌入 → 向量存储 → 检索器 → LLM → 答案
```

LangChain/LlamaIndex 提供每个环节的组件，ChromaDB 负责"向量存储"环节。

### 例子

**1. LangChain + ChromaDB 基础集成**

```python
# 安装依赖
# pip install langchain langchain-chroma langchain-openai langchain-community

from langchain_chroma import Chroma
from langchain_openai import OpenAIEmbeddings, ChatOpenAI
from langchain_text_splitters import RecursiveCharacterTextSplitter
from langchain_community.document_loaders import TextLoader

# 1. 加载文档
loader = TextLoader("./knowledge.txt")
documents = loader.load()

# 2. 分块
text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,
    chunk_overlap=50,
    separators=["\n\n", "\n", "。", "！", "？", " "]
)
chunks = text_splitter.split_documents(documents)
print(f"分块数: {len(chunks)}")

# 3. 创建向量存储（自动嵌入）
embeddings = OpenAIEmbeddings(api_key="sk-...")
vectorstore = Chroma.from_documents(
    documents=chunks,
    embedding=embeddings,
    collection_name="knowledge_base",
    persist_directory="./chroma_data"
)

# 4. 检索
query = "什么是机器学习？"
results = vectorstore.similarity_search(query, k=3)
for doc in results:
    print(f"内容: {doc.page_content[:100]}...")
    print(f"元数据: {doc.metadata}")
    print("---")
```

**2. LangChain RAG 完整示例**

```python
from langchain_chroma import Chroma
from langchain_openai import OpenAIEmbeddings, ChatOpenAI
from langchain.prompts import ChatPromptTemplate
from langchain_core.runnables import RunnablePassthrough
from langchain_core.output_parsers import StrOutputParser

# 初始化组件
embeddings = OpenAIEmbeddings(api_key="sk-...")
llm = ChatOpenAI(api_key="sk-...", model="gpt-3.5-turbo")

# 加载已有向量存储
vectorstore = Chroma(
    collection_name="knowledge_base",
    embedding_function=embeddings,
    persist_directory="./chroma_data"
)

# 创建检索器
retriever = vectorstore.as_retriever(
    search_type="similarity",
    search_kwargs={"k": 3}
)

# 定义Prompt模板
template = """基于以下上下文回答问题。如果上下文中没有相关信息，请说"我不知道"。

上下文：
{context}

问题：{question}

回答："""
prompt = ChatPromptTemplate.from_template(template)

# 构建RAG链
def format_docs(docs):
    return "\n\n".join(doc.page_content for doc in docs)

rag_chain = (
    {"context": retriever | format_docs, "question": RunnablePassthrough()}
    | prompt
    | llm
    | StrOutputParser()
)

# 提问
question = "什么是机器学习？"
answer = rag_chain.invoke(question)
print(f"问题: {question}")
print(f"回答: {answer}")
```

**3. LangChain 带元数据过滤的检索**

```python
# 添加带元数据的文档
from langchain_core.documents import Document

documents = [
    Document(
        page_content="Python 是编程语言",
        metadata={"category": "programming", "difficulty": 1}
    ),
    Document(
        page_content="机器学习是AI分支",
        metadata={"category": "ai", "difficulty": 3}
    ),
    Document(
        page_content="深度学习使用神经网络",
        metadata={"category": "ai", "difficulty": 4}
    )
]

vectorstore = Chroma.from_documents(
    documents=documents,
    embedding=embeddings,
    collection_name="filtered_docs"
)

# 带过滤的检索
results = vectorstore.similarity_search(
    "学习AI",
    k=5,
    filter={"category": "ai"}  # 只在AI类中搜索
)

for doc in results:
    print(f"内容: {doc.page_content}, 元数据: {doc.metadata}")

# 复杂过滤
results = vectorstore.similarity_search(
    "学习",
    k=5,
    filter={
        "$and": [
            {"category": "ai"},
            {"difficulty": {"$gte": 3}}
        ]
    }
)
```

**4. LlamaIndex + ChromaDB 集成**

```python
# 安装依赖
# pip install llama-index llama-index-vector-stores-chroma

import chromadb
from llama_index.core import VectorStoreIndex, StorageContext, Document
from llama_index.vector_stores.chroma import ChromaVectorStore
from llama_index.embeddings.openai import OpenAIEmbedding
from llama_index.llms.openai import OpenAI

# 1. 创建ChromaDB客户端和集合
db = chromadb.PersistentClient(path="./chroma_data")
chroma_collection = db.get_or_create_collection("llamaindex_docs")

# 2. 创建向量存储
vector_store = ChromaVectorStore(chroma_collection=chroma_collection)
storage_context = StorageContext.from_defaults(vector_store=vector_store)

# 3. 准备文档
documents = [
    Document(text="Python 是一种高级编程语言，由 Guido van Rossum 创建。"),
    Document(text="JavaScript 是网页开发的脚本语言。"),
    Document(text="机器学习是人工智能的分支，使计算机从数据中学习。"),
    Document(text="Docker 是容器化平台，用于应用部署。")
]

# 4. 创建索引（自动嵌入和存储）
import os
os.environ["OPENAI_API_KEY"] = "sk-..."

index = VectorStoreIndex.from_documents(
    documents,
    storage_context=storage_context,
    embed_model=OpenAIEmbedding()
)

# 5. 查询
query_engine = index.as_query_engine(
    llm=OpenAI(model="gpt-3.5-turbo"),
    similarity_top_k=3
)

response = query_engine.query("什么是机器学习？")
print(f"回答: {response}")
print(f"来源:")
for source in response.source_nodes:
    print(f"  - {source.node.text[:80]}...")
```

**5. LlamaIndex 检索器自定义**

```python
from llama_index.core.retrievers import VectorIndexRetriever
from llama_index.core.query_engine import RetrieverQueryEngine
from llama_index.core.postprocessor import SimilarityPostprocessor

# 创建检索器
retriever = VectorIndexRetriever(
    index=index,
    similarity_top_k=10  # 先检索10个
)

# 后处理器：过滤相似度低于阈值的结果
postprocessor = SimilarityPostprocessor(similarity_cutoff=0.7)

# 组装查询引擎
query_engine = RetrieverQueryEngine.from_args(
    retriever,
    node_postprocessors=[postprocessor],
    llm=OpenAI(model="gpt-3.5-turbo")
)

# 查询
response = query_engine.query("解释机器学习")
print(response)
```

**6. 使用本地嵌入模型（免费方案）**

```python
# LangChain + 本地嵌入
from langchain_huggingface import HuggingFaceEmbeddings
from langchain_chroma import Chroma

# 使用免费的本地嵌入模型
embeddings = HuggingFaceEmbeddings(
    model_name="sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2"
)

vectorstore = Chroma.from_documents(
    documents=chunks,
    embedding=embeddings,
    collection_name="local_embed_docs",
    persist_directory="./chroma_data"
)

# LlamaIndex + 本地嵌入
from llama_index.embeddings.huggingface import HuggingFaceEmbedding

embed_model = HuggingFaceEmbedding(
    model_name="sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2"
)

index = VectorStoreIndex.from_documents(
    documents,
    storage_context=storage_context,
    embed_model=embed_model
)
```

### 总结

- LangChain 用 `langchain-chroma` 包，LlamaIndex 用 `ChromaVectorStore`。
- LangChain 适合复杂链式调用，LlamaIndex 专注 RAG 优化。
- 集成后可用框架的文档加载、分块、LLM 调用等完整工具链。
- 本地嵌入模型（HuggingFace）实现免费方案。
- **关键要点**：LangChain 用 `as_retriever()` 创建检索器；LlamaIndex 用 `as_query_engine()` 创建查询引擎。
- **常见误区**：忽视文档分块导致检索质量差；嵌入模型与 LLM 不匹配；不处理元数据过滤。

---

## 第 15 讲：实战案例——RAG 知识库系统

### 概念

本讲通过一个完整的 RAG（检索增强生成）知识库系统案例，将前 14 讲的知识融会贯通。我们将构建一个支持文档上传、自动分块、语义检索、智能问答的知识库系统。

**案例背景**：构建一个技术文档知识库，支持：

- 多格式文档上传（TXT、Markdown）。
- 自动分块和嵌入。
- 语义检索相关问题。
- 基于 LLM 的智能问答。
- 来源引用和置信度展示。

### 原理

**1. RAG 的工作流程**

```
用户提问 → 查询嵌入 → 向量检索 → 获取相关文档 → 构建Prompt → LLM生成 → 返回答案
```

**2. 系统架构**

```
┌─────────────┐     ┌──────────────┐     ┌─────────────┐
│  文档上传   │ ──→ │  分块+嵌入   │ ──→ │  ChromaDB   │
└─────────────┘     └──────────────┘     └─────────────┘
                                                │
┌─────────────┐     ┌──────────────┐            │
│  答案展示   │ ←── │  LLM 生成    │ ←──────────┘
└─────────────┘     └──────────────┘
                          ↑
                    ┌─────────────┐
                    │  用户提问   │
                    └─────────────┘
```

**3. 关键设计**

- **文档分块**：按句子分块，带重叠，避免截断语义。
- **元数据**：记录来源、标题、块索引，便于引用。
- **检索策略**：先检索 Top-K，再用相似度阈值过滤。
- **Prompt 设计**：明确要求基于上下文回答，避免幻觉。

### 例子

**1. 完整的 RAG 知识库系统**

```python
import chromadb
from chromadb.utils import embedding_functions
import os
import re
from pathlib import Path
from typing import List, Dict, Optional
from dataclasses import dataclass

@dataclass
class SearchResult:
    """检索结果"""
    content: str
    metadata: Dict
    score: float

@dataclass
class Answer:
    """问答结果"""
    question: str
    answer: str
    sources: List[SearchResult]
    confidence: float

class RAGKnowledgeBase:
    """RAG 知识库系统"""

    def __init__(self, persist_path="./rag_data", embedding_model="all-MiniLM-L6-v2"):
        """初始化知识库"""
        self.client = chromadb.PersistentClient(path=persist_path)
        self.embedding_function = embedding_functions.SentenceTransformerEmbeddingFunction(
            model_name=embedding_model
        )
        self.collection = self.client.get_or_create_collection(
            name="knowledge_base",
            embedding_function=self.embedding_function,
            metadata={"hnsw:space": "cosine"}
        )

    def add_document(self, file_path: str, source_name: str = None):
        """添加文档到知识库"""
        path = Path(file_path)
        if not path.exists():
            raise FileNotFoundError(f"文件不存在: {file_path}")

        # 读取文件
        with open(path, 'r', encoding='utf-8') as f:
            content = f.read()

        # 分块
        chunks = self._chunk_text(content, chunk_size=300, overlap=50)

        # 准备数据
        source = source_name or path.name
        documents = []
        metadatas = []
        ids = []

        for i, chunk in enumerate(chunks):
            documents.append(chunk)
            metadatas.append({
                "source": source,
                "chunk_idx": i,
                "total_chunks": len(chunks),
                "file_path": str(path)
            })
            ids.append(f"{source}_chunk_{i}")

        # 添加到ChromaDB
        self.collection.upsert(
            documents=documents,
            metadatas=metadatas,
            ids=ids
        )

        print(f"已添加文档: {source} ({len(chunks)} 块)")
        return len(chunks)

    def _chunk_text(self, text: str, chunk_size: int = 300, overlap: int = 50) -> List[str]:
        """文本分块（按句子，带重叠）"""
        # 按句子分割
        sentences = re.split(r'(?<=[。！？.!?])\s*', text)
        sentences = [s.strip() for s in sentences if s.strip()]

        chunks = []
        current_chunk = ""

        for sentence in sentences:
            if len(current_chunk) + len(sentence) <= chunk_size:
                current_chunk += sentence
            else:
                if current_chunk:
                    chunks.append(current_chunk)
                # 重叠：保留上一块末尾
                if chunks and overlap > 0:
                    current_chunk = chunks[-1][-overlap:] + sentence
                else:
                    current_chunk = sentence

        if current_chunk:
            chunks.append(current_chunk)

        return chunks

    def search(self, query: str, n_results: int = 5,
               similarity_threshold: float = 0.5,
               source_filter: str = None) -> List[SearchResult]:
        """语义检索"""
        where = {"source": source_filter} if source_filter else None

        results = self.collection.query(
            query_texts=[query],
            n_results=n_results,
            where=where
        )

        search_results = []
        for i, (doc, meta, dist) in enumerate(zip(
            results['documents'][0],
            results['metadatas'][0],
            results['distances'][0]
        )):
            # 相似度 = 1 - 距离（cosine）
            similarity = 1 - dist
            if similarity >= similarity_threshold:
                search_results.append(SearchResult(
                    content=doc,
                    metadata=meta,
                    score=similarity
                ))

        return search_results

    def list_documents(self) -> List[str]:
        """列出所有文档"""
        all_docs = self.collection.get(include=["metadatas"])
        sources = set(meta.get("source") for meta in all_docs['metadatas'])
        return sorted(sources)

    def delete_document(self, source_name: str):
        """删除文档"""
        self.collection.delete(where={"source": source_name})
        print(f"已删除文档: {source_name}")

    def get_stats(self) -> Dict:
        """获取知识库统计"""
        all_docs = self.collection.get(include=["metadatas"])
        sources = set(meta.get("source") for meta in all_docs['metadatas'])
        return {
            "total_chunks": len(all_docs['ids']),
            "total_documents": len(sources),
            "documents": sorted(sources)
        }


# 使用示例
kb = RAGKnowledgeBase()

# 创建测试文档
os.makedirs("./docs", exist_ok=True)
with open("./docs/python.txt", "w", encoding="utf-8") as f:
    f.write("""
Python 是一种高级编程语言。它由 Guido van Rossum 于 1991 年创建。
Python 强调代码可读性，使用缩进表示代码块。
Python 支持多种编程范式，包括面向对象和函数式编程。
Python 有丰富的标准库和第三方包。
在数据科学领域，Python 是最流行的语言之一。
NumPy 和 Pandas 是数据分析的核心库。
机器学习领域，Python 同样占据主导地位。
TensorFlow 和 PyTorch 都支持 Python。
""".strip())

with open("./docs/database.txt", "w", encoding="utf-8") as f:
    f.write("""
数据库是结构化数据的集合。关系数据库使用表格存储数据。
SQL 是关系数据库的标准查询语言。
MySQL 和 PostgreSQL 是流行的开源关系数据库。
NoSQL 数据库包括 MongoDB 和 Redis。
向量数据库用于存储和检索高维向量。
ChromaDB 是一个开源的向量数据库。
向量数据库是 AI 应用的基础设施。
""".strip())

# 添加文档
kb.add_document("./docs/python.txt")
kb.add_document("./docs/database.txt")

# 查看统计
print("\n知识库统计:")
stats = kb.get_stats()
print(f"  文档数: {stats['total_documents']}")
print(f"  总块数: {stats['total_chunks']}")
print(f"  文档列表: {stats['documents']}")

# 检索
print("\n检索: 'Python 数据分析'")
results = kb.search("Python 数据分析", n_results=3)
for r in results:
    print(f"  [相似度={r.score:.4f}] 来源={r.metadata['source']}")
    print(f"  内容: {r.content[:80]}...")
    print()
```

**2. 集成 LLM 的问答系统**

```python
class RAGQuestionAnswerer:
    """RAG 问答系统"""

    def __init__(self, knowledge_base: RAGKnowledgeBase, llm_client=None):
        self.kb = knowledge_base
        self.llm_client = llm_client  # 可选的LLM客户端

    def answer(self, question: str, n_results: int = 5,
               similarity_threshold: float = 0.3) -> Answer:
        """回答问题"""
        # 1. 检索相关文档
        results = self.kb.search(
            question,
            n_results=n_results,
            similarity_threshold=similarity_threshold
        )

        if not results:
            return Answer(
                question=question,
                answer="抱歉，知识库中没有找到相关信息。",
                sources=[],
                confidence=0.0
            )

        # 2. 构建上下文
        context = "\n\n".join([
            f"[来源: {r.metadata['source']}, 块{r.metadata['chunk_idx']}]\n{r.content}"
            for r in results
        ])

        # 3. 计算置信度（平均相似度）
        confidence = sum(r.score for r in results) / len(results)

        # 4. 生成回答
        if self.llm_client:
            answer_text = self._generate_with_llm(question, context)
        else:
            answer_text = self._generate_without_llm(question, context, results)

        return Answer(
            question=question,
            answer=answer_text,
            sources=results,
            confidence=confidence
        )

    def _generate_with_llm(self, question: str, context: str) -> str:
        """用LLM生成回答"""
        prompt = f"""基于以下上下文回答问题。要求：
1. 只基于上下文回答，不要编造信息
2. 如果上下文没有相关信息，说明"根据现有资料无法回答"
3. 回答简洁明了

上下文：
{context}

问题：{question}

回答："""

        # 这里用OpenAI示例，实际可替换为任何LLM
        try:
            from openai import OpenAI
            client = OpenAI(api_key="sk-...")
            response = client.chat.completions.create(
                model="gpt-3.5-turbo",
                messages=[{"role": "user", "content": prompt}],
                temperature=0.3
            )
            return response.choices[0].message.content
        except Exception as e:
            return f"LLM调用失败: {e}"

    def _generate_without_llm(self, question: str, context: str,
                               results: List[SearchResult]) -> str:
        """无LLM时返回检索结果"""
        answer_parts = ["根据知识库检索到以下相关信息：\n"]
        for i, r in enumerate(results[:3], 1):
            answer_parts.append(f"{i}. {r.content[:200]}")
        return "\n".join(answer_parts)


# 使用
qa = RAGQuestionAnswerer(kb)

# 提问
questions = [
    "Python 是什么？",
    "什么是向量数据库？",
    "数据分析用什么工具？"
]

for q in questions:
    print(f"\n问题: {q}")
    result = qa.answer(q)
    print(f"回答: {result.answer}")
    print(f"置信度: {result.confidence:.2%}")
    print(f"来源数: {len(result.sources)}")
    for src in result.sources[:2]:
        print(f"  - {src.metadata['source']} (相似度: {src.score:.4f})")
```

**3. 带缓存的查询优化**

```python
from functools import lru_cache
import hashlib

class CachedRAGSystem:
    """带查询缓存的RAG系统"""

    def __init__(self, qa_system: RAGQuestionAnswerer):
        self.qa = qa_system
        self.query_cache = {}

    def _cache_key(self, question: str, n_results: int):
        """生成缓存键"""
        key_str = f"{question}_{n_results}"
        return hashlib.md5(key_str.encode()).hexdigest()

    def answer(self, question: str, n_results: int = 5) -> Answer:
        """带缓存的回答"""
        cache_key = self._cache_key(question, n_results)

        if cache_key in self.query_cache:
            print("（从缓存返回）")
            return self.query_cache[cache_key]

        result = self.qa.answer(question, n_results=n_results)
        self.query_cache[cache_key] = result
        return result

    def clear_cache(self):
        """清空缓存"""
        self.query_cache.clear()

    def cache_stats(self):
        """缓存统计"""
        return {"cached_queries": len(self.query_cache)}


# 使用
cached_rag = CachedRAGSystem(qa)

# 第一次查询（计算）
result1 = cached_rag.answer("Python 是什么？")
print(f"回答: {result1.answer[:100]}...")

# 第二次相同查询（缓存）
result2 = cached_rag.answer("Python 是什么？")
print(f"回答: {result2.answer[:100]}...")

print(f"缓存统计: {cached_rag.cache_stats()}")
```

**4. 批量问答与评估**

```python
class RAGEvaluator:
    """RAG 系统评估工具"""

    def __init__(self, qa_system: RAGQuestionAnswerer):
        self.qa = qa_system

    def evaluate(self, test_cases: List[Dict]) -> Dict:
        """评估RAG系统"""
        results = {
            "total": len(test_cases),
            "answered": 0,
            "no_answer": 0,
            "avg_confidence": 0,
            "avg_sources": 0,
            "details": []
        }

        total_confidence = 0
        total_sources = 0

        for case in test_cases:
            question = case["question"]
            expected_keywords = case.get("expected_keywords", [])

            answer = self.qa.answer(question)

            # 检查关键词
            keyword_hits = sum(
                1 for kw in expected_keywords if kw in answer.answer
            )
            keyword_score = keyword_hits / len(expected_keywords) if expected_keywords else 0

            if answer.sources:
                results["answered"] += 1
                total_confidence += answer.confidence
                total_sources += len(answer.sources)
            else:
                results["no_answer"] += 1

            results["details"].append({
                "question": question,
                "confidence": answer.confidence,
                "sources_count": len(answer.sources),
                "keyword_score": keyword_score
            })

        results["avg_confidence"] = total_confidence / results["answered"] if results["answered"] else 0
        results["avg_sources"] = total_sources / results["answered"] if results["answered"] else 0

        return results

# 使用
evaluator = RAGEvaluator(qa)

test_cases = [
    {
        "question": "Python 是什么？",
        "expected_keywords": ["编程语言", "Guido"]
    },
    {
        "question": "什么是向量数据库？",
        "expected_keywords": ["向量", "ChromaDB"]
    },
    {
        "question": "数据分析用什么？",
        "expected_keywords": ["NumPy", "Pandas"]
    }
]

results = evaluator.evaluate(test_cases)
print("\n评估结果:")
print(f"  总问题数: {results['total']}")
print(f"  有答案: {results['answered']}")
print(f"  无答案: {results['no_answer']}")
print(f"  平均置信度: {results['avg_confidence']:.2%}")
print(f"  平均来源数: {results['avg_sources']:.1f}")
```

### 总结

- RAG 知识库系统核心：文档分块 → 嵌入存储 → 语义检索 → LLM 生成。
- 文档分块按句子带重叠，提升检索精度。
- 元数据记录来源和块索引，便于引用和追溯。
- 查询缓存避免重复计算，提升响应速度。
- **关键要点**：相似度阈值过滤低质量结果；置信度基于平均相似度；来源引用增强可信度。
- **常见误区**：不分块直接存长文档；忽视相似度阈值；LLM 幻觉（不基于上下文回答）。

---

## 课程结语

本课程从 ChromaDB 基础概念出发，系统讲解了其数据模型、CRUD 操作、相似性检索、元数据过滤、嵌入函数、持久化部署、多租户隔离，以及与 LangChain/LlamaIndex 集成构建 RAG 应用，共 15 讲。ChromaDB 作为 AI 原生的向量数据库，以其简洁的 API、丰富的功能和强大的生态，成为构建 LLM 应用的首选向量存储。

ChromaDB 的学习要点在于：理解向量数据库与传统数据库的差异，掌握嵌入函数和相似性搜索的原理，合理设计文档分块和元数据，建立完整的数据管理和检索优化知识体系。建议在学习理论的同时多动手实践——搭建 ChromaDB 环境，构建一个完整的 RAG 知识库系统，尝试不同的嵌入模型和检索策略，与 LangChain 集成开发智能问答应用。

向量数据库是 AI 应用时代的基础设施，ChromaDB 作为其中的优秀代表，值得每一位 AI 应用开发者深入学习。希望本课程能成为您 ChromaDB 学习之路的起点，祝您在向量检索和 RAG 应用的世界里游刃有余！
