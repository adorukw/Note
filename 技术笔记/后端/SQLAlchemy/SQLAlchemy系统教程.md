# SQLAlchemy 系统教程

> 本教程基于 SQLAlchemy 2.x，采用 2.0 风格 API，从零基础到生产实战，共 7 章 26 讲。

---

## 课程总览

- **预计讲数**：26 讲（7 章）
- **学习目标**：从零掌握 SQLAlchemy 2.0 的 Core 与 ORM 两大体系，理解连接管理、声明式模型、Session 工作单元、关系映射、查询优化、事件系统、异步操作及与 FastAPI/Alembic 的工程化集成，具备在生产环境中独立使用 SQLAlchemy 的能力。
- **适用版本**：SQLAlchemy 2.x（2.0 风格 API）
- **渐进结构**：基础 → Core → ORM → 关系 → 进阶查询 → 高级特性 → 实战工程化

### 详细章节目录

| 章 | 章标题 | 讲次 | 讲标题 |
|----|--------|------|--------|
| 第1章 | 入门基础 | 第1讲 | SQLAlchemy 概述与定位 |
| | | 第2讲 | 安装与环境准备 |
| | | 第3讲 | Engine 引擎与连接管理 |
| 第2章 | Core — SQL 表达式语言 | 第4讲 | Metadata 与 Table 对象 |
| | | 第5讲 | 数据类型与列定义 |
| | | 第6讲 | 表的创建与删除 |
| | | 第7讲 | Core 增删改查（DML） |
| | | 第8讲 | Core 查询表达式 |
| 第3章 | ORM 基础 | 第9讲 | 声明式模型（2.0 风格） |
| | | 第10讲 | Session 与工作单元 |
| | | 第11讲 | ORM 基本增删改查 |
| | | 第12讲 | select() 查询（2.0 风格） |
| 第4章 | 关系映射 | 第13讲 | 一对多关系 |
| | | 第14讲 | 多对一与双向关系 |
| | | 第15讲 | 多对多关系 |
| | | 第16讲 | 关系加载策略 |
| 第5章 | 进阶查询与性能 | 第17讲 | 连接查询与子查询 |
| | | 第18讲 | 聚合、分组与窗口函数 |
| | | 第19讲 | 性能优化与 N+1 问题 |
| 第6章 | 高级特性 | 第20讲 | 事件系统与监听器 |
| | | 第21讲 | 混合属性与计算列 |
| | | 第22讲 | 自定义类型与 TypeDecorator |
| | | 第23讲 | 继承映射 |
| 第7章 | 实战与工程化 | 第24讲 | 与 FastAPI/Flask 集成 |
| | | 第25讲 | 异步 SQLAlchemy |
| | | 第26讲 | 与 Alembic 数据库迁移 |

---

## 第1章 入门基础

本章是整个课程的起点。我们将从 SQLAlchemy 的整体定位讲起，理解它在 Python 生态中的角色，然后完成环境搭建，最后深入理解 Engine——SQLAlchemy 一切操作的基石。学完本章，你将能够建立到任意数据库的连接，并理解连接池的工作原理。

---

### 第1讲 SQLAlchemy 概述与定位

#### 概念

SQLAlchemy 是 Python 生态中最成熟、功能最完整的 SQL 工具包与对象关系映射（ORM）框架，自 2005 年由 Michael Bayer 发布以来，已成为 Python 数据库访问的事实标准。它不是一个简单的 ORM，而是一个分层的数据库工具集：底层是 **Core**（SQL 表达式语言），提供对 SQL 的程序化构造与执行；上层是 **ORM**，在 Core 之上构建了对象映射、会话管理、关系导航等高级能力。这种分层设计是 SQLAlchemy 区别于 Django ORM、Tortoise ORM 等框架的核心特征——你可以只使用 Core 而完全不碰 ORM，也可以在 ORM 代码中随时下沉到 Core 层获取精细控制。

#### 原理

SQLAlchemy 的设计哲学是"SQL 是工具，不是要消灭的敌人"。与早期 Hibernate 风格的"完全屏蔽 SQL"不同，SQLAlchemy 认为开发者应当理解 SQL，框架的职责是让 SQL 的构造变得类型安全、可组合、可复用，同时消除手写 SQL 带来的字符串拼接漏洞（如 SQL 注入）和重复样板代码。

其分层架构如下：

```
┌─────────────────────────────────────┐
│           ORM 层（上层）             │  ← 声明式模型、Session、关系导航
│  DeclarativeBase / Session / select │
├─────────────────────────────────────┤
│          Core 层（下层）             │  ← SQL 表达式语言、元数据
│  Table / Metadata / Engine / Dialect│
├─────────────────────────────────────┤
│          DBAPI 驱动层                │  ← psycopg2、pymysql、sqlite3
├─────────────────────────────────────┤
│          数据库服务器                 │  ← PostgreSQL、MySQL、SQLite...
└─────────────────────────────────────┘
```

- **Core**：提供 `Engine`（连接管理）、`MetaData`（模式元数据）、`Table`/`Column`（表结构描述）、SQL 构造器（`select`/`insert`/`update`/`delete`）。Core 的查询是面向"行"的，返回的是 `Row` 对象，不涉及 Python 类映射。
- **ORM**：在 Core 之上提供 `DeclarativeBase`（声明式基类）、`Session`（工作单元）、`relationship`（关系导航）。ORM 的查询面向"对象"，返回的是映射类的实例。
- **Dialect**：方言层，负责将 SQLAlchemy 构造的 SQL 翻译成特定数据库的方言（如 PostgreSQL 的 `RETURNING`、MySQL 的 `LIMIT` 语法、SQLite 的 `AUTOINCREMENT`）。

**Core vs ORM 的选择原则**：当你的数据访问以"批量操作、报表查询、ETL"为主，且不需要将行映射为领域对象时，用 Core 更轻量、更直接；当你的应用以"领域对象的生命周期管理、复杂关系导航"为主时，用 ORM 能大幅减少样板代码。两者并非互斥——ORM 内部完全基于 Core 实现，你可以在 ORM 的 `Session` 中执行 Core 级别的 `select()`，也可以在 ORM 模型上使用 Core 的 SQL 函数。

#### 例子

下面用一个最小示例展示 Core 与 ORM 两种风格的差异。我们使用内置的 SQLite，无需安装数据库服务器。

**Core 风格**（面向行）：

```python
from sqlalchemy import create_engine, Table, Column, Integer, String, MetaData, select

engine = create_engine("sqlite:///:memory:", echo=True)
metadata = MetaData()

users = Table(
    "users", metadata,
    Column("id", Integer, primary_key=True),
    Column("name", String(50), nullable=False),
    Column("email", String(120), unique=True),
)
metadata.create_all(engine)

# 插入
with engine.connect() as conn:
    conn.execute(users.insert(), [{"name": "Alice", "email": "alice@x.com"},
                                   {"name": "Bob",   "email": "bob@x.com"}])
    conn.commit()

    # 查询
    for row in conn.execute(select(users).where(users.c.name == "Alice")):
        print(row.name, row.email)  # Alice alice@x.com
```

**ORM 风格**（面向对象）：

```python
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, Session

class Base(DeclarativeBase):
    pass

class User(Base):
    __tablename__ = "users"
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(50))
    email: Mapped[str] = mapped_column(String(120), unique=True)

Base.metadata.create_all(engine)

with Session(engine) as session:
    alice = User(name="Alice", email="alice@x.com")
    session.add(alice)
    session.commit()

    # 查询返回的是 User 对象，而非裸行
    user = session.scalar(select(User).where(User.name == "Alice"))
    print(user.name, user.email)  # Alice alice@x.com
```

可以看到，ORM 版本将表定义与 Python 类融合，查询返回的是 `User` 实例，可以直接用属性访问；而 Core 版本返回的是 `Row`，需要用 `.name` 这种命名元组方式访问列。

#### 总结

- SQLAlchemy 是**分层架构**：Core（SQL 表达式语言）在下，ORM 在上，Dialect 负责方言适配。
- 设计哲学是"增强 SQL 而非隐藏 SQL"，因此你应当具备基础 SQL 知识。
- **Core 面向行**，适合批量/报表/ETL；**ORM 面向对象**，适合领域模型管理。两者可在同一项目中共存。
- 2.0 风格统一了 Core 与 ORM 的查询 API：都用 `select()`，这是 SQLAlchemy 2.x 最重要的变化。
- 常见误区：以为"用了 ORM 就不用学 SQL"——恰恰相反，理解 SQL 才能用好 SQLAlchemy。

---

### 第2讲 安装与环境准备

#### 概念

在编写任何 SQLAlchemy 代码之前，我们需要安装 SQLAlchemy 本身以及对应数据库的 **DBAPI 驱动**。DBAPI 是 PEP 249 定义的 Python 数据库访问标准接口，所有数据库驱动（psycopg2、pymysql、sqlite3 等）都遵循这一规范。SQLAlchemy 不直接与数据库通信，而是通过 Dialect 调用 DBAPI 驱动来完成实际的 socket 通信和协议编解码。理解"SQLAlchemy → Dialect → DBAPI → 数据库"这条链路，是排查连接问题的前提。

#### 原理

**连接字符串（URL）** 是 SQLAlchemy 识别使用哪个 Dialect 和 DBAPI 的核心机制，格式为：

```
dialect+driver://user:password@host:port/database?param=value
```

- `dialect`：数据库类型（postgresql、mysql、sqlite、oracle、mssql 等）。
- `driver`：可选，指定具体的 DBAPI 驱动。省略时使用该 dialect 的默认驱动。
- `user:password@host:port/database`：标准数据库连接信息。
- `?param=value`：传递给驱动或 SQLAlchemy 的额外参数。

常见数据库的连接字符串示例：

| 数据库 | 连接字符串 | 需安装的驱动 |
|--------|-----------|-------------|
| SQLite（内存） | `sqlite:///:memory:` | 无（Python 内置 sqlite3） |
| SQLite（文件） | `sqlite:///./app.db` | 无 |
| PostgreSQL | `postgresql+psycopg2://user:pwd@localhost/db` | `psycopg2` 或 `psycopg[binary]` |
| PostgreSQL（异步） | `postgresql+asyncpg://user:pwd@localhost/db` | `asyncpg` |
| MySQL | `mysql+pymysql://user:pwd@localhost/db` | `pymysql` |
| MySQL（异步） | `mysql+aiomysql://user:pwd@localhost/db` | `aiomysql` |
| SQL Server | `mssql+pyodbc://user:pwd@host/db?driver=ODBC+Driver+17+for+SQL+Server` | `pyodbc` |

**驱动选择建议**：PostgreSQL 推荐 `psycopg`（v3，新名称）或成熟的 `psycopg2-binary`；MySQL 推荐 `pymysql`（纯 Python，易安装）或 `mysqlclient`（C 扩展，更快）；SQLite 用内置 `sqlite3` 即可。异步场景必须使用异步驱动（asyncpg、aiomysql），不能用同步驱动。

#### 例子

**安装 SQLAlchemy 与驱动**：

```bash
# 核心库（同时包含 Core 和 ORM）
pip install "sqlalchemy>=2.0"

# 按需安装数据库驱动（选其一即可）
pip install psycopg2-binary      # PostgreSQL 同步
pip install "psycopg[binary]"    # PostgreSQL v3（推荐）
pip install pymysql              # MySQL 同步
pip install asyncpg              # PostgreSQL 异步
pip install aiomysql             # MySQL 异步
```

**验证安装与连接**：

```python
from sqlalchemy import create_engine, text

# SQLite 最简验证（无需任何额外驱动）
engine = create_engine("sqlite:///:memory:")

# 执行一条原生 SQL 验证连通性
with engine.connect() as conn:
    result = conn.execute(text("SELECT 1 AS test"))
    print(result.fetchone())  # (1,) 或 Row(test=1)

print("SQLAlchemy 版本:", __import__("sqlalchemy").__version__)
```

**连接 PostgreSQL 的完整示例**：

```python
from sqlalchemy import create_engine, text

# 推荐用变量/环境变量管理密码，不要硬编码
import os
DB_URL = os.getenv(
    "DATABASE_URL",
    "postgresql+psycopg2://postgres:secret@localhost:5432/mydb"
)

# echo=True 会打印实际执行的 SQL，便于学习与调试
engine = create_engine(DB_URL, echo=True, pool_pre_ping=True)

with engine.connect() as conn:
    version = conn.execute(text("SELECT version()")).scalar()
    print("数据库版本:", version)
```

> **注意**：`pool_pre_ping=True` 会在每次从连接池取连接时发一条轻量 `SELECT 1` 探活，避免拿到已被服务端断开的"死连接"。生产环境强烈建议开启。

#### 总结

- 安装分两步：装 `sqlalchemy` 本体 + 装对应数据库的 **DBAPI 驱动**。
- 连接字符串 `dialect+driver://...` 决定使用哪个方言和驱动，记牢这张映射表。
- SQLite 用内置 `sqlite3`，零配置，最适合学习与单元测试。
- 生产环境务必用环境变量管理密码，并开启 `pool_pre_ping` 防止死连接。
- 异步驱动与同步驱动**不可混用**：异步引擎必须配异步驱动。
- `echo=True` 是学习阶段最好的工具，能看到 SQLAlchemy 实际生成的 SQL。

---

### 第3讲 Engine 引擎与连接管理

#### 概念

`Engine` 是 SQLAlchemy 的核心入口，是整个框架与数据库交互的"中枢"。它是一个**单例式**对象：一个应用通常只创建一个 `Engine`（对应一个数据库），由它统一管理连接池、方言（Dialect）和事务策略。`Engine` 本身不直接执行 SQL，而是按需创建 `Connection` 对象——你可以把 Engine 想象成"连接工厂 + 连接池管理员"，而 `Connection` 才是真正执行 SQL 的一次性会话句柄。理解 Engine 的连接池机制，是排查"连接泄漏""Too many connections""数据库连接超时"等生产问题的前提。

#### 原理

**Engine 的内部结构**：

```
Engine
 ├── Dialect（方言：负责 SQL 方言适配）
 │    ├── DBAPI（驱动模块，如 psycopg2）
 │    └── SQLCompiler（将表达式编译为该数据库的 SQL 文本）
 ├── Pool（连接池：管理底层 DBAPI 连接的复用）
 └── Connection（按需创建的一次性连接句柄）
```

**连接的生命周期**：

1. 调用 `engine.connect()` 时，Engine 向 Pool 申请一个底层 DBAPI 连接。
2. 若池中有空闲连接，直接复用；否则新建一个（不超过 `pool_size` 上限）。
3. `Connection` 对象持有这个底层连接，执行 SQL。
4. `Connection` 关闭（退出 `with` 块）时，底层连接**归还给池**而非真正关闭，供下次复用。
5. 真正关闭底层连接的时机：连接超过 `pool_recycle` 秒、池被 dispose、或进程退出。

**连接池的关键参数**（`create_engine` 的参数）：

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `pool_size` | 5 | 池中常驻连接数上限 |
| `max_overflow` | 10 | 超出 pool_size 后还能临时创建的连接数 |
| `pool_timeout` | 30s | 等待空闲连接的超时时间，超时抛 `TimeoutError` |
| `pool_recycle` | -1（不回收） | 连接存活秒数，超过则回收重建（防止数据库/中间件主动断连） |
| `pool_pre_ping` | False | 取连接时先发 `SELECT 1` 探活 |
| `echo` | False | 打印实际执行的 SQL（学习/调试用） |

**两种执行模式**：

- **`engine.connect()`**：显式事务模式。返回的 `Connection` 默认开启事务，必须手动 `commit()` 才会提交。这是 2.0 风格的推荐做法，事务边界清晰。
- **`engine.begin()`**：自动提交的上下文管理器。退出 `with` 块时自动 commit，异常时自动 rollback。适合"一段逻辑要么全成功要么全回滚"的场景。

#### 例子

**基础连接与显式事务**：

```python
from sqlalchemy import create_engine, text

engine = create_engine("sqlite:///./demo.db", echo=True)

# 方式一：显式事务（2.0 推荐风格）
with engine.connect() as conn:
    conn.execute(text("CREATE TABLE IF NOT EXISTS t (id INTEGER PRIMARY KEY, v TEXT)"))
    conn.execute(text("INSERT INTO t (v) VALUES (:v"), {"v": "hello"})
    conn.commit()  # 必须显式提交，否则 INSERT 不生效

# 方式二：自动事务（begin 上下文）
with engine.begin() as conn:
    conn.execute(text("INSERT INTO t (v) VALUES (:v"), {"v": "world"})
    # 退出 with 自动 commit；若中途抛异常则自动 rollback
```

**连接池配置示例**（生产环境典型配置）：

```python
engine = create_engine(
    "postgresql+psycopg2://user:pwd@db-host:5432/app",
    pool_size=10,          # 常驻 10 个连接
    max_overflow=20,       # 高峰期可临时扩到 30
    pool_timeout=30,       # 等待连接最多 30 秒
    pool_recycle=1800,     # 30 分钟回收，防止被数据库/防火墙断开
    pool_pre_ping=True,    # 取连接前探活
    echo=False,            # 生产关闭 SQL 打印
)
```

**查看连接池状态**（调试用）：

```python
pool = engine.pool
print("池类型:", type(pool).__name__)
print("当前检出连接数:", pool.checkedout())
print("空闲连接数:", pool.checkedin())
```

**一次性执行（无连接池）**：某些短脚本或 Serverless 场景不需要连接池，可用 `NullPool`：

```python
from sqlalchemy import create_engine
from sqlalchemy.pool import NullPool

engine = create_engine("postgresql+psycopg2://...", poolclass=NullPool)
# 每次 connect 都新建真实连接，关闭时真正断开，无复用
```

#### 总结

- `Engine` 是单例入口，管理连接池 + 方言；一个数据库对应一个 Engine。
- 连接从池中借出，用完归还（非真正关闭），`pool_recycle` 控制回收周期。
- 生产环境务必配置 `pool_size`/`max_overflow`/`pool_recycle`/`pool_pre_ping`，防止连接泄漏和死连接。
- 2.0 风格推荐 `engine.connect()` + 显式 `commit()`，事务边界最清晰；`engine.begin()` 适合"全成功或全回滚"的原子块。
- Serverless / 短脚本场景用 `NullPool` 避免连接池在进程结束时残留连接。
- 常见坑：`max_overflow` 设得过大导致数据库 `max_connections` 耗尽；`pool_recycle` 不设导致连接被云数据库代理静默断开后报"connection already closed"。

---

## 第2章 Core — SQL 表达式语言

本章深入 SQLAlchemy 的下层——Core。Core 提供了一套类型安全的 SQL 构造器，让你用 Python 表达式而非字符串拼接来生成 SQL。我们将从表结构定义（Metadata/Table/Column）讲起，覆盖数据类型、建表删表，然后系统讲解 Core 的增删改查与查询表达式。学完本章，你能用纯 Core 完成绝大多数数据库操作，并为学习 ORM 打下坚实基础。

---

### 第4讲 Metadata 与 Table 对象

#### 概念

`MetaData` 是 SQLAlchemy Core 中"数据库模式（Schema）的容器"，它集中管理一组 `Table` 对象及其约束、索引。可以把 `MetaData` 想象成一个 Python 进程内的"数据库目录"——它记录了"有哪些表、每张表有哪些列、列之间有什么约束"，但**不**代表数据库里真实存在的表，而是一份 Python 侧的描述。`Table` 则是单张表的描述对象，由若干 `Column` 组成。`MetaData` + `Table` + `Column` 三者构成了 Core 的模式定义三件套，是 `create_all()`、`drop_all()` 以及 SQL 构造的基础。

#### 原理

**MetaData 的作用**：

1. **模式注册中心**：所有 `Table` 在构造时都会把自己注册到所属的 `MetaData` 对象的 `tables` 字典中，键为表名。这样一次 `metadata.create_all(engine)` 就能把所有表一次性建到数据库。
2. **跨表操作的基础**：外键约束、JOIN 操作需要引用其他表，`MetaData` 让这些表能互相找到对方。
3. **与 ORM 的桥梁**：ORM 的 `DeclarativeBase` 内部持有一个 `MetaData`（`Base.metadata`），所有继承该 Base 的模型类共享这一个 MetaData。

**Table 对象的本质**：`Table` 是一个 `FromClause` 子类，同时也是列的命名空间。构造后，你可以通过 `table.c.列名` 访问列对象（`c` 是 `columns` 的缩写），这些列对象是构造 SQL 表达式的基本积木：

```python
users.c.id           # Column 对象
users.c.name == "Alice"  # 一个 BinaryExpression，可编译为 WHERE name = 'Alice'
```

**两种创建 Table 的方式**：

- **显式构造**：`Table("users", metadata, Column(...), ...)`，Core 风格。
- **反射（Reflection）**：`Table("users", metadata, autoload_with=engine)`，从已有数据库读取表结构，无需手写列定义。适合对接遗留数据库。

#### 例子

**显式定义多张表**：

```python
from sqlalchemy import (
    create_engine, MetaData, Table, Column,
    Integer, String, ForeignKey, UniqueConstraint
)

engine = create_engine("sqlite:///./core_demo.db", echo=True)
metadata = MetaData()

users = Table(
    "users", metadata,
    Column("id", Integer, primary_key=True),       # primary_key 自动 NOT NULL + 自增
    Column("username", String(50), nullable=False),
    Column("email", String(120), nullable=False, unique=True),
    UniqueConstraint("username", name="uq_username"),
)

posts = Table(
    "posts", metadata,
    Column("id", Integer, primary_key=True),
    Column("title", String(200), nullable=False),
    Column("body", String, nullable=True),          # 不限长度
    Column("author_id", ForeignKey("users.id"), nullable=False),  # 外键
)

# 一次性创建所有表（已存在的会被跳过）
metadata.create_all(engine)

# 查看 metadata 中注册了哪些表
print(list(metadata.tables.keys()))  # ['users', 'posts']
```

**反射已有表**（对接遗留数据库）：

```python
# 假设数据库里已经有一张 legacy_logs 表
metadata2 = MetaData()
logs = Table("legacy_logs", metadata2, autoload_with=engine)
print(logs.columns.keys())  # ['id', 'level', 'message', 'created_at']
print(logs.c.level.type)    # VARCHAR(20)
```

**通过 `c` 访问列并构造表达式**：

```python
# 这些表达式本身不执行，只是"SQL 的 Python 表示"
expr = users.c.username == "alice"   # 等价于 WHERE username = 'alice'
print(expr)                            # users.username = :username_1

from sqlalchemy import select
stmt = select(users).where(users.c.email.like("%@gmail.com"))
print(stmt)  # SELECT ... FROM users WHERE users.email LIKE ?
```

#### 总结

- `MetaData` 是模式容器，`Table` 是单表描述，`Column` 是列描述——三者构成 Core 模式定义三件套。
- `metadata.create_all(engine)` 一次性建所有表；`drop_all()` 一次性删所有表（注意外键依赖顺序）。
- 通过 `table.c.列名` 访问列对象，这是构造所有 SQL 表达式的起点。
- **反射**（`autoload_with`）可从已有数据库逆向出 Table 对象，适合对接遗留库。
- ORM 的 `Base.metadata` 就是一个 MetaData 实例，所有模型类共享它——这解释了为什么 `Base.metadata.create_all()` 能建出所有 ORM 模型对应的表。
- 常见坑：多个 `MetaData` 实例之间无法建立外键引用；反射不读取视图、存储过程，只读表结构。

---

### 第5讲 数据类型与列定义

#### 概念

SQLAlchemy 的类型系统是连接 Python 类型与数据库 SQL 类型的桥梁。每一个 `Column` 都必须指定一个 `TypeEngine` 子类（如 `Integer`、`String`、`DateTime`），它承担三重职责：**①** 在 `CREATE TABLE` 时生成正确的 SQL 类型声明；**②** 在执行 SQL 时把 Python 值编码为 DBAPI 接受的参数（如 `datetime` → ISO 字符串）；**③** 在读取结果时把 DBAPI 返回的值解码为 Python 对象（如时间戳 → `datetime`）。理解类型系统，才能避免"存进去查出来类型变了"这类隐蔽 bug。

#### 原理

**类型的三层映射**：

```
Python 类型  ←→  SQLAlchemy TypeEngine  ←→  数据库 SQL 类型
  int             Integer                     INTEGER / BIGINT
  str             String                      VARCHAR(n) / TEXT
  datetime        DateTime                    TIMESTAMP / DATETIME
  bool            Boolean                     BOOLEAN / TINYINT(1)
  bytes           LargeBinary                 BLOB / BYTEA
  Decimal         Numeric                     NUMERIC(p, s) / DECIMAL
```

**通用类型 vs 方言类型**：

- **通用类型**（`Integer`、`String`、`DateTime` 等）：跨数据库通用，SQLAlchemy 会根据当前 dialect 选择最合适的 SQL 类型。**优先使用通用类型**，保证可移植性。
- **方言类型**（`postgresql.JSONB`、`mysql.LONGTEXT`、`sqlite.JSON`）：使用特定数据库的专有类型，牺牲可移植性换取功能。需从 `sqlalchemy.dialects` 导入。
- **枚举类型**：`Enum("A", "B", "C")` 或 `Enum(MyEnumClass)`，在 PostgreSQL 上生成原生 `ENUM`，在其他库上用 `VARCHAR + CHECK` 模拟。

**类型的关键参数**：

| 类型 | 常用参数 | 说明 |
|------|---------|------|
| `String` | `length` | 字符串长度，省略则不限长（映射为 TEXT） |
| `Numeric` | `precision`, `scale`, `asdecimal` | 金额等需精确计算的场景，默认返回 `Decimal` |
| `DateTime` | `timezone` | 是否带时区 |
| `Enum` | `*labels` 或 `enum_class` | 枚举值或 Python `Enum` 类 |

#### 例子

**通用类型示例**：

```python
from sqlalchemy import (
    create_engine, MetaData, Table, Column,
    Integer, BigInteger, SmallInteger,
    String, Text, Unicode,
    DateTime, Date, Time,
    Boolean,
    Numeric, Float,
    LargeBinary,
    Enum,
)
import enum

engine = create_engine("sqlite:///./types.db", echo=True)
metadata = MetaData()

class Status(enum.Enum):
    draft = "draft"
    published = "published"
    archived = "archived"

articles = Table(
    "articles", metadata,
    Column("id", Integer, primary_key=True),           # 自增主键
    Column("view_count", BigInteger, default=0),       # 大整数
    Column("title", String(200), nullable=False),      # VARCHAR(200)
    Column("content", Text),                            # 不限长文本
    Column("price", Numeric(10, 2)),                    # DECIMAL(10,2)，适合金额
    Column("rating", Float),                            # 浮点
    Column("is_pinned", Boolean, default=False),       # 布尔
    Column("cover", LargeBinary),                       # 二进制（图片等）
    Column("published_at", DateTime(timezone=True)),    # 带时区时间
    Column("status", Enum(Status, native_enum=False)),  # 枚举（用 CHECK 模拟）
)
metadata.create_all(engine)
```

**方言专有类型**（以 PostgreSQL 的 JSONB 为例）：

```python
from sqlalchemy.dialects.postgresql import JSONB, ARRAY, INET

# 仅在 PostgreSQL 上可用
stats = Table(
    "stats", metadata,
    Column("id", Integer, primary_key=True),
    Column("meta", JSONB),                  # 原生 JSONB，支持索引和查询操作符
    Column("tags", ARRAY(String)),          # 原生数组类型
    Column("client_ip", INET),              # IP 地址类型
)
```

**类型自动转换演示**：

```python
from datetime import datetime, timezone
from sqlalchemy import select

with engine.connect() as conn:
    conn.execute(articles.insert(), {
        "title": "Hello",
        "published_at": datetime.now(timezone.utc),  # Python datetime
    })
    conn.commit()

    row = conn.execute(select(articles.c.published_at)).first()
    print(type(row[0]))  # <class 'datetime.datetime'>，自动解码回来
```

#### 总结

- 类型系统承担三重职责：生成 SQL 类型、编码入参、解码结果。
- **优先用通用类型**（`Integer`/`String`/`DateTime` 等）保证可移植性；只在确实需要专有功能时用方言类型。
- 金额、利率等需要精确十进制运算的场景，必须用 `Numeric`，不能用 `Float`（浮点有精度损失）。
- `Enum` 优先传 Python `enum.Enum` 子类，配合 `native_enum=False` 在非 PG 库上也能稳定工作。
- `String` 不传 `length` 会映射为不限长文本（TEXT），需注意某些数据库对 TEXT 不能建唯一索引。
- 常见坑：SQLite 没有 `BOOLEAN`，SQLAlchemy 用 `INTEGER` + 0/1 模拟；`DateTime` 在 SQLite 存为字符串，但 SQLAlchemy 会透明转换。

---

### 第6讲 表的创建与删除

#### 概念

定义好 `Table` 后，需要把它们"物化"到数据库中——这就是 `MetaData.create_all()` 和 `drop_all()` 的职责。但要注意，SQLAlchemy 的 `create_all` **不是迁移工具**：它只负责"建不存在的表"，不会修改已有表的结构（加列、改类型、加索引都不会自动做）。真正的 schema 迁移要靠 Alembic（第26讲）。理解 `create_all` 的"只建不改"特性，能避免"改了模型但数据库没变"的困惑。

#### 原理

**`create_all` 的行为**：

1. 遍历 `metadata.tables` 中的所有 `Table`。
2. 对每张表，先查数据库的系统表（`information_schema` 或 `sqlite_master`）判断该表是否已存在。
3. 不存在则执行 `CREATE TABLE`；已存在则**跳过**，不报错也不修改。
4. 表之间的创建顺序由外键依赖自动推导：被引用的表先建。

**`drop_all` 的行为**：与 `create_all` 相反，按外键依赖的逆序删除所有表。但要注意，如果表之间存在 `DROP` 时的外键约束，可能需要先 `SET FOREIGN_KEY_CHECKS=0`（MySQL）或用 `DROP TABLE ... CASCADE`（PostgreSQL）。

**`checkfirst` 参数**：`create_all(engine, checkfirst=True)`（默认）会先检查再建；设为 `False` 则直接发 `CREATE TABLE`，若表已存在会报错。`checkfirst=False` 适合"确定库是空的"的初始化场景，省去检查开销。

**`if_exists` / `if_not_exists`**：2.0 支持在 `create()`/`drop()` 上加 `if_exists="drop"` 等参数，生成 `CREATE TABLE IF NOT EXISTS` 形式的 SQL。

#### 例子

**基础建表与删表**：

```python
from sqlalchemy import create_engine, MetaData, Table, Column, Integer, String, ForeignKey

engine = create_engine("sqlite:///./schema.db", echo=True)
metadata = MetaData()

users = Table("users", metadata,
    Column("id", Integer, primary_key=True),
    Column("name", String(50)),
)
posts = Table("posts", metadata,
    Column("id", Integer, primary_key=True),
    Column("title", String(200)),
    Column("user_id", ForeignKey("users.id")),  # 外键依赖 users
)

# 一次性建所有表，外键依赖会自动排序：先 users 后 posts
metadata.create_all(engine)

# 再次调用：表已存在，checkfirst=True 会跳过，不报错
metadata.create_all(engine)

# 删除所有表（按依赖逆序：先 posts 后 users）
# metadata.drop_all(engine)
```

**单表操作**：

```python
# 只建一张表
users.create(engine, checkfirst=True)

# 只删一张表
users.drop(engine, checkfirst=True)

# 生成 SQL 但不执行（便于审查）
from sqlalchemy.schema import CreateTable
print(CreateTable(users).compile(engine))
# 输出：
# CREATE TABLE users (
#     id INTEGER NOT NULL,
#     name VARCHAR(50),
#     PRIMARY KEY (id)
# )
```

**带索引和约束的建表**：

```python
from sqlalchemy import Index, UniqueConstraint, CheckConstraint

products = Table("products", metadata,
    Column("id", Integer, primary_key=True),
    Column("sku", String(20), nullable=False),
    Column("price", Numeric(10, 2), nullable=False),
    UniqueConstraint("sku", name="uq_sku"),
    CheckConstraint("price >= 0", name="ck_price_nonneg"),  # 价格非负
    Index("ix_sku", "sku"),                                  # 普通索引
)
products.create(engine)
```

**`create_all` 不会修改已有表**（重要演示）：

```python
# 假设 users 表已存在，现在我们给 Table 定义加一列
users2 = Table("users", metadata,
    Column("id", Integer, primary_key=True),
    Column("name", String(50)),
    Column("email", String(120)),  # 新增的列
)
metadata.create_all(engine)
# 结果：数据库里的 users 表并不会多出 email 列！
# create_all 只建不存在的表，不修改已有表结构。
# 要加列必须用 ALTER TABLE 或 Alembic 迁移。
```

#### 总结

- `create_all` 只建**不存在的表**，不修改已有表——这是它和迁移工具的本质区别。
- 外键依赖会自动决定建表/删表顺序，无需手动排序。
- `checkfirst=True`（默认）先查后建，安全；`False` 直接建，快但表已存在会报错。
- 加列、改类型、加索引等结构变更，`create_all` 无能为力，必须用 `ALTER TABLE` 或 **Alembic**。
- 生产环境**不要**用 `create_all` 管理 schema，一律用 Alembic；`create_all` 只适合开发初始化和测试。
- 常见坑：改了模型重启应用，发现"新列没出现"——因为 `create_all` 跳过了已存在的表。

---

### 第7讲 Core 增删改查（DML）

#### 概念

本讲聚焦 Core 的数据操作语言（DML）：`insert`、`update`、`delete` 以及查询的基础形式。注意 Core 的 DML 返回的是 `CursorResult`，关注的是"受影响的行数"和"返回的行"，不涉及对象映射。2.0 风格统一用 `insert()`/`update()`/`delete()`/`select()` 函数式构造语句，取代了旧版的 `table.insert()` 方法风格。掌握这些构造器，你就能用类型安全的方式完成所有数据操作，彻底告别字符串拼接 SQL。

#### 原理

**DML 构造器的函数式 API**（2.0 风格）：

| 操作 | 构造函数 | 返回 |
|------|---------|------|
| 插入 | `insert(table).values(...)` | `Insert` 语句 |
| 更新 | `update(table).where(...).values(...)` | `Update` 语句 |
| 删除 | `delete(table).where(...)` | `Delete` 语句 |
| 查询 | `select(table).where(...)` | `Select` 语句 |

**参数绑定机制**：所有值都通过**命名参数**绑定，而非字符串拼接，从根本上杜绝 SQL 注入。`values(name="Alice")` 会编译为 `INSERT INTO ... VALUES (:name)`，参数 `:name` 的值由驱动安全转义后发送。

**批量插入的两种方式**：

- **`executemany` 模式**：`conn.execute(insert(table), [dict1, dict2, ...])`，传一个字典列表，驱动会批量执行。适合大批量同结构数据。
- **`insert(...).values([...])`**：2.0 新增，把多行直接写进语句对象，部分数据库支持多值 `INSERT` 优化。

**`returning` 子句**：PostgreSQL、SQLite 3.35+、SQL Server 支持 `INSERT/UPDATE/DELETE ... RETURNING`，可以在一条语句内拿到操作后的行数据，避免"插入后再 SELECT 一次"的二次查询。

#### 例子

**Insert（插入）**：

```python
from sqlalchemy import create_engine, MetaData, Table, Column, Integer, String, insert, select

engine = create_engine("sqlite:///./dml.db", echo=True)
metadata = MetaData()
users = Table("users", metadata,
    Column("id", Integer, primary_key=True),
    Column("name", String(50), nullable=False),
    Column("age", Integer),
)
metadata.create_all(engine)

with engine.connect() as conn:
    # 单行插入
    conn.execute(insert(users).values(name="Alice", age=30))

    # 批量插入（executemany 模式，推荐）
    conn.execute(insert(users), [
        {"name": "Bob", "age": 25},
        {"name": "Carol", "age": 35},
        {"name": "Dave", "age": 28},
    ])
    conn.commit()
```

**Update（更新）**：

```python
from sqlalchemy import update

with engine.connect() as conn:
    # 条件更新
    stmt = update(users).where(users.c.name == "Bob").values(age=26)
    result = conn.execute(stmt)
    print("受影响行数:", result.rowcount)  # 1
    conn.commit()

    # 全表更新（不推荐，但语法支持）
    # conn.execute(update(users).values(age=0))
```

**Delete（删除）**：

```python
from sqlalchemy import delete

with engine.connect() as conn:
    stmt = delete(users).where(users.c.age < 26)
    result = conn.execute(stmt)
    print("删除行数:", result.rowcount)  # 1（Bob 被删）
    conn.commit()
```

**Returning（返回操作后的行）**：

```python
with engine.connect() as conn:
    # 插入并立即拿到生成的 id
    stmt = insert(users).values(name="Eve", age=40).returning(users.c.id, users.c.name)
    result = conn.execute(stmt)
    row = result.fetchone()
    print("新插入:", row.id, row.name)  # 5 Eve
    conn.commit()

    # 更新并返回受影响的行
    stmt = update(users).where(users.c.name == "Eve").values(age=41) \
                        .returning(users.c.id, users.c.age)
    for row in conn.execute(stmt):
        print("更新后:", row.id, row.age)  # 5 41
    conn.commit()
```

**Select（查询基础）**：

```python
with engine.connect() as conn:
    # 查全部列
    for row in conn.execute(select(users)):
        print(row.id, row.name, row.age)

    # 查指定列
    stmt = select(users.c.name, users.c.age).where(users.c.age >= 30)
    for row in conn.execute(stmt):
        print(row.name, row.age)  # Alice 30, Carol 35, Eve 41
```

#### 总结

- 2.0 风格统一用 `insert()`/`update()`/`delete()`/`select()` 函数构造语句，取代旧版 `table.insert()` 方法。
- 所有值通过命名参数绑定，**天然防 SQL 注入**，永远不要用字符串拼接。
- 批量插入用 `conn.execute(insert(table), [dict, ...])` 的 executemany 模式，性能远优于循环单插。
- `returning()` 能在一条语句内拿到操作结果，省去二次查询，PostgreSQL/SQLite 3.35+/SQL Server 支持。
- `result.rowcount` 返回受影响行数，但注意部分驱动对 SELECT 不支持 rowcount。
- 常见坑：忘记 `commit()` 导致 DML 不生效（2.0 默认显式事务）；`update`/`delete` 不加 `where` 会全表操作，生产事故高发点。

---

### 第8讲 Core 查询表达式

#### 概念

上一讲我们接触了最基础的 `select().where()`，本讲系统讲解 Core 查询表达式的完整能力：`where` 条件构造、`order_by` 排序、`group_by` + `having` 分组、`limit`/`offset` 分页、`distinct` 去重、`join` 连接，以及 `func` 调用 SQL 函数。这些构造器全部返回新的语句对象（不可变，链式调用产生新对象），可以自由组合，最终由 `compile()` 编译为具体方言的 SQL。掌握这些，你就能用 Core 表达任意复杂度的查询。

#### 原理

**链式构造与不可变性**：每个方法（`where`/`order_by`/`group_by` 等）都返回一个**新的**语句对象，原对象不变。这意味着你可以安全地复用基础语句，分别加上不同条件派生出多个查询：

```python
base = select(users)
young = base.where(users.c.age < 30)   # 新对象，base 不变
old = base.where(users.c.age >= 30)    # 另一个新对象
```

**`where` 的多重调用**：多次调用 `where` 会用 `AND` 连接；要 `OR` 需用 `or_()` 组合。`where` 也接受多个参数，等价于 `AND`。

**`func` 对象**：`sqlalchemy.func` 是一个动态代理，任何属性访问都会生成对应的 SQL 函数调用：`func.count()`、`func.max()`、`func.now()`、`func.lower()`。它不校验函数是否存在，只是把名字原样编译进 SQL，由数据库负责执行。

**`group_by` + `having`**：`group_by` 按列分组，`having` 对分组结果过滤（区别于 `where` 对原始行过滤）。聚合函数（`count`/`sum`/`avg`/`max`/`min`）通常与 `group_by` 配合使用。

#### 例子

**where 条件构造**：

```python
from sqlalchemy import select, and_, or_, not_

# AND：多次 where 或传多参数
stmt = select(users).where(users.c.age > 20).where(users.c.age < 40)
stmt = select(users).where(users.c.age > 20, users.c.name != "Bob")  # 等价

# OR
stmt = select(users).where(or_(users.c.age < 20, users.c.age > 50))

# NOT
stmt = select(users).where(not_(users.c.name == "Bob"))

# IN / NOT IN
stmt = select(users).where(users.c.name.in_(["Alice", "Carol", "Eve"]))

# LIKE / ILIKE
stmt = select(users).where(users.c.name.like("A%"))      # 大小写敏感
stmt = select(users).where(users.c.name.ilike("a%"))     # 大小写不敏感（PG）

# BETWEEN / IS NULL
stmt = select(users).where(users.c.age.between(20, 40))
stmt = select(users).where(users.c.name.is_(None))
```

**order_by / limit / offset / distinct**：

```python
from sqlalchemy import desc, asc

# 排序
stmt = select(users).order_by(asc(users.c.age))              # 升序
stmt = select(users).order_by(desc(users.c.age), users.c.name)  # 多列排序

# 分页
stmt = select(users).order_by(users.c.id).limit(10).offset(20)  # 第3页，每页10

# 去重
stmt = select(users.c.age).distinct()
```

**group_by + having + 聚合**：

```python
from sqlalchemy import func

# 按年龄分组统计人数
stmt = select(users.c.age, func.count().label("cnt")) \
    .group_by(users.c.age) \
    .having(func.count() > 1) \
    .order_by(desc("cnt"))

with engine.connect() as conn:
    for row in conn.execute(stmt):
        print(row.age, row.cnt)  # 30 2  （30岁有2人）
```

**join（连接查询）**：

```python
from sqlalchemy import ForeignKey

posts = Table("posts", metadata,
    Column("id", Integer, primary_key=True),
    Column("title", String(200)),
    Column("user_id", ForeignKey("users.id")),
)
metadata.create_all(engine)

# 插入测试数据后...

# INNER JOIN
stmt = select(users.c.name, posts.c.title).join(posts, users.c.id == posts.c.user_id)

# LEFT JOIN
stmt = select(users.c.name, posts.c.title) \
    .join(posts, users.c.id == posts.c.user_id, isouter=True)

# join 的简写：当存在外键时，可省略 ON 条件
stmt = select(users.c.name, posts.c.title).join(posts)  # 自动用外键推导 ON

with engine.connect() as conn:
    for row in conn.execute(stmt):
        print(row.name, "->", row.title)
```

**func 调用 SQL 函数**：

```python
# 聚合
stmt = select(func.count(users.c.id), func.avg(users.c.age), func.max(users.c.age))

# 字符串函数
stmt = select(func.lower(users.c.name), func.length(users.c.name))

# 时间函数
stmt = select(func.now(), func.current_date(), func.extract("year", users.c.created_at))

# 标量函数直接用在 SELECT
stmt = select(users.c.name, func.coalesce(users.c.age, 0).label("age_or_zero"))
```

#### 总结

- 查询构造器是**链式 + 不可变**的，每次调用返回新对象，可安全复用基础语句。
- `where` 多次调用等价 `AND`；要 `OR` 用 `or_()`；`in_`/`like`/`between`/`is_` 是列方法。
- `group_by` + `having` 用于分组聚合，`having` 过滤分组、`where` 过滤原始行，二者不可混用。
- `join` 在有外键时可省略 `ON` 条件，SQLAlchemy 自动推导；`isouter=True` 变 LEFT JOIN。
- `func.任意名` 生成对应 SQL 函数调用，名字原样编译，由数据库执行——所以可用方言特有函数。
- 常见坑：`offset` 大值时性能差（数据库仍要扫描跳过的行），深分页应改用"游标/键集分页"。

---

## 第3章 ORM 基础

本章进入 SQLAlchemy 的上层——ORM。我们将学习 2.0 风格的声明式模型（`DeclarativeBase` + `Mapped` + `mapped_column`）、Session 工作单元模式、ORM 的增删改查，以及统一的 `select()` 查询。学完本章，你能用面向对象的方式操作数据库，并为学习关系映射打下基础。

---

### 第9讲 声明式模型（2.0 风格）

#### 概念

声明式模型（Declarative Model）是 ORM 的核心：用 Python 类定义数据库表，类的属性对应表的列，类的实例对应表的一行。SQLAlchemy 2.0 引入了全新的声明式语法，基于 `DeclarativeBase` 基类 + `Mapped` 类型注解 + `mapped_column()` 三件套，取代了旧版的 `Column(...)` 写法。新语法与 Python 的类型系统深度集成，IDE 补全和 mypy 静态检查都能正确识别属性类型，这是 2.0 最重要的改进之一。

#### 原理

**2.0 声明式三件套**：

- **`DeclarativeBase`**：所有模型的基类。继承它后，子类自动注册到 `Base.registry` 和 `Base.metadata`。一个应用通常只有一个 `Base`。
- **`Mapped[T]`**：类型注解，声明该属性映射为数据库列，`T` 是 Python 类型（`int`/`str`/`datetime` 等）。SQLAlchemy 根据 `T` 自动推导 SQL 类型：`int → Integer`、`str → String`、`datetime → DateTime`、`bool → Boolean`。
- **`mapped_column(...)`**：列的配置函数，指定主键、可空性、默认值、索引、外键等。可省略——仅写 `id: Mapped[int] = mapped_column(primary_key=True)` 即可。

**类型推导规则**：

| Python 注解 | 推导的 SQL 类型 | 可空性 |
|------------|----------------|--------|
| `Mapped[int]` | `Integer` | NOT NULL |
| `Mapped[int \| None]` / `Optional[int]` | `Integer` | NULL 允许 |
| `Mapped[str]` | `String`（不限长） | NOT NULL |
| `Mapped[str \| None]` | `String` | NULL 允许 |
| `Mapped[datetime]` | `DateTime` | NOT NULL |
| `Mapped[bool]` | `Boolean` | NOT NULL |
| `Mapped[bytes]` | `LargeBinary` | NOT NULL |
| `Mapped[Decimal]` | `Numeric` | NOT NULL |

关键规则：**`Optional[T]` 或 `T | None` 表示允许 NULL**，纯 `T` 表示 NOT NULL。这是 2.0 用类型系统表达可空性的核心机制，比旧版手写 `nullable=True/False` 更优雅。

**`__tablename__`**：每个模型类必须显式声明表名（除非用抽象基类/单表继承）。类名不会自动转为表名，避免隐式约定带来的意外。

#### 例子

**基础模型定义**：

```python
from datetime import datetime
from typing import Optional
from sqlalchemy import String
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column

class Base(DeclarativeBase):
    pass

class User(Base):
    __tablename__ = "users"

    id: Mapped[int] = mapped_column(primary_key=True)  # 自增主键
    name: Mapped[str] = mapped_column(String(50))       # VARCHAR(50) NOT NULL
    email: Mapped[str] = mapped_column(String(120), unique=True)
    bio: Mapped[Optional[str]] = mapped_column(String(500))  # 可空
    age: Mapped[Optional[int]]                          # 仅注解，无 mapped_column 也可
    created_at: Mapped[datetime] = mapped_column(default=datetime.now)

    def __repr__(self):
        return f"<User id={self.id} name={self.name!r}>"
```

**带外键的模型**：

```python
from sqlalchemy import ForeignKey
from sqlalchemy.orm import relationship

class Post(Base):
    __tablename__ = "posts"

    id: Mapped[int] = mapped_column(primary_key=True)
    title: Mapped[str] = mapped_column(String(200))
    body: Mapped[Optional[str]]
    author_id: Mapped[int] = mapped_column(ForeignKey("users.id"))
    author: Mapped["User"] = relationship(back_populates="posts")  # 关系（第13讲详解）

User.posts: Mapped[list["Post"]] = relationship(back_populates="author", cascade="all, delete-orphan")
```

**建表与验证**：

```python
from sqlalchemy import create_engine

engine = create_engine("sqlite:///./orm.db", echo=True)
Base.metadata.create_all(engine)  # 一次性建所有模型对应的表

# 查看 User 对应的 Table
print(User.__table__)        # Table('users', ...)
print(User.__table__.c.keys())  # ['id', 'name', 'email', 'bio', 'age', 'created_at']
```

**用 Enum 和自定义类型**：

```python
import enum

class Status(enum.Enum):
    draft = "draft"
    published = "published"

class Article(Base):
    __tablename__ = "articles"
    id: Mapped[int] = mapped_column(primary_key=True)
    status: Mapped[Status] = mapped_column(default=Status.draft)  # SQLAlchemy 自动识别 Enum
```

#### 总结

- 2.0 声明式三件套：`DeclarativeBase` + `Mapped[T]` + `mapped_column(...)`。
- **类型推导**：`Mapped[int]` → NOT NULL，`Mapped[Optional[int]]` → 允许 NULL，这是用类型系统表达可空性的核心。
- `__tablename__` 必须显式声明，类名不会自动转表名。
- `Mapped[str]` 不传 `mapped_column` 会推导为不限长 `String`，需要限长就传 `mapped_column(String(50))`。
- 一个应用通常只有一个 `Base`，所有模型共享 `Base.metadata`，因此 `Base.metadata.create_all()` 能一次建所有表。
- 常见坑：忘了 `from __future__ import annotations` 或字符串注解（`Mapped["Post"]`）导致前向引用失败；`Optional` 写成 `| None` 在 Python 3.10+ 才支持。

---

### 第10讲 Session 与工作单元

#### 概念

`Session` 是 ORM 的核心，是"工作单元"（Unit of Work）模式的实现。如果说 `Engine` 管理的是"连接"，那么 `Session` 管理的是"对象的身份与变更追踪"。Session 在内存中维护一个"待提交"的对象集合，记录每个对象从加载到现在的所有属性变更，在 `commit()` 时自动生成并执行对应的 `INSERT`/`UPDATE`/`DELETE`——开发者只需操作 Python 对象，SQL 由 Session 自动生成。理解 Session 的身份映射、变更追踪和事务边界，是用好 ORM 的关键。

#### 原理

**Session 的三大职责**：

1. **身份映射（Identity Map）**：Session 内部维护一个字典 `{(类, 主键): 对象}`。同一条记录在一次 Session 生命周期内只会被加载一次，后续查询命中身份映射直接返回同一个对象，保证"一个主键对应一个 Python 对象"。
2. **变更追踪（Dirty Tracking）**：Session 记录每个对象被加载时的属性快照，当你修改属性时，Session 标记该对象为"脏"（dirty）。`commit()` 时对比快照生成最小化的 `UPDATE`。
3. **事务管理**：Session 持有一个底层 `Connection`，所有操作在同一个事务内。`commit()` 提交事务并清空待写队列；`rollback()` 回滚事务并还原对象到加载时状态。

**对象的状态机**：

```
                  add()
  transient ──────────────► pending
  (未关联Session)            (在Session中，未flush)
                                │ flush()
                                ▼
                             persistent ◄─────────────────┐
                          (已持久化，有主键)                  │
                                │ expire() / commit()       │
                                ▼                            │
                             detached ──────────────────────┘
                          (脱离Session，但对象还在)     re-add()
```

- **transient**：刚创建，未加入 Session，无主键。
- **pending**：`session.add()` 后，等待 `flush()` 写入数据库。
- **persistent**：已写入数据库，有主键，被 Session 追踪。
- **detached**：Session 关闭或对象被 `expunge` 后，脱离 Session 但仍保留属性。

**`flush` vs `commit`**：

- `flush()`：把待写队列的变更生成 SQL 并发给数据库（`INSERT`/`UPDATE`/`DELETE`），但**不提交事务**。主键会在 flush 后回填到对象上。
- `commit()`：先 `flush()`，再 `COMMIT` 事务，最后清空待写队列并 expire 所有对象（下次访问属性会重新查询）。

**`sessionmaker`**：工厂函数，预配置好 `engine`、`expire_on_commit` 等参数，每次调用产出一个新的 `Session`。生产环境推荐用 `sessionmaker` 而非直接 `Session(engine)`。

#### 例子

**基础增删改查与变更追踪**：

```python
from sqlalchemy import create_engine, select
from sqlalchemy.orm import Session, sessionmaker

engine = create_engine("sqlite:///./session.db", echo=True)
Base.metadata.create_all(engine)
SessionLocal = sessionmaker(engine)

# === 增 ===
with SessionLocal() as session:
    alice = User(name="Alice", email="alice@x.com")
    bob = User(name="Bob", email="bob@x.com")
    session.add(alice)
    session.add(bob)
    # 此时 alice.id 仍为 None（未 flush）
    session.flush()           # 发 INSERT，回填 id
    print(alice.id)           # 1
    session.commit()          # 提交事务

# === 改（变更追踪自动生成 UPDATE）===
with SessionLocal() as session:
    alice = session.scalar(select(User).where(User.name == "Alice"))
    alice.email = "alice_new@x.com"   # 修改属性
    # 不需要显式 update，commit 时自动生成 UPDATE
    session.commit()
    # 生成的 SQL：UPDATE users SET email=? WHERE users.id=?

# === 删 ===
with SessionLocal() as session:
    bob = session.scalar(select(User).where(User.name == "Bob"))
    session.delete(bob)
    session.commit()
    # 生成的 SQL：DELETE FROM users WHERE users.id=?
```

**身份映射演示**：

```python
with SessionLocal() as session:
    u1 = session.scalar(select(User).where(User.id == 1))
    u2 = session.scalar(select(User).where(User.id == 1))
    print(u1 is u2)  # True！同一 Session 内同主键返回同一对象
    # 第二次查询甚至不会发 SQL（命中身份映射）
```

**`expire_on_commit` 的行为**：

```python
with SessionLocal() as session:
    alice = session.scalar(select(User).where(User.name == "Alice"))
    alice.email = "a@b.com"
    session.commit()  # 默认 expire_on_commit=True，所有对象属性被标记为过期
    print(alice.email)  # 访问属性触发重新 SELECT，拿到最新值
```

**`expire` / `refresh` / `expunge`**：

```python
session.expire(alice)      # 标记属性过期，下次访问重新查询
session.refresh(alice)     # 立即发 SELECT 刷新所有属性
session.expunge(alice)     # 把对象从 Session 移除，变为 detached
session.merge(alice)       # 把 detached 对象重新并入 Session（按主键查找或新建）
```

#### 总结

- Session = 身份映射 + 变更追踪 + 事务管理，是"工作单元"模式的实现。
- 同一 Session 内同主键返回**同一对象**（身份映射），避免重复加载和对象不一致。
- 修改对象属性后无需手写 `UPDATE`，`commit()` 自动生成最小化更新 SQL。
- `flush` 发 SQL 但不提交；`commit` = flush + COMMIT + expire。主键在 flush 后回填。
- `expire_on_commit=True`（默认）会在提交后让对象属性过期，下次访问重新查询——若不想此行为可设 `expire_on_commit=False`，但要自行注意数据新鲜度。
- 常见坑：跨请求复用同一 Session 导致脏数据累积；`commit` 后访问属性又触发查询（N+1）；detached 对象访问延迟加载属性报 `DetachedInstanceError`。

---

### 第11讲 ORM 基本增删改查

#### 概念

本讲聚焦 ORM 风格的增删改查实操。与 Core 不同，ORM 的操作对象是 Python 类的实例，而非字典和裸行。`session.add()` 加入待写队列，`session.delete()` 标记删除，查询返回的是模型实例。2.0 风格下，ORM 查询也统一用 `select()` 函数，通过 `session.execute(stmt)` 或 `session.scalar(stmt)` 获取结果。掌握这些基本操作，你就能用纯 ORM 完成日常 CRUD。

#### 原理

**ORM CRUD 的方法映射**：

| 操作 | 方法 | 说明 |
|------|------|------|
| 新增 | `session.add(obj)` / `session.add_all([obj1, obj2])` | 加入待写队列，flush 时 INSERT |
| 查询单个 | `session.scalar(select(...).where(...))` | 返回第一个模型实例或 None |
| 查询多个 | `session.scalars(select(...)).all()` | 返回模型实例列表 |
| 按主键查 | `session.get(Model, pk)` | 直接走身份映射/主键查询，最高效 |
| 修改 | 直接改属性，`session.commit()` | 变更追踪自动 UPDATE |
| 删除 | `session.delete(obj)` | flush 时 DELETE |

**`session.get` 的特殊性**：这是唯一"不走 `select()`"的查询方法。它先查身份映射，命中则直接返回；未命中才发 `SELECT ... WHERE id = ?`。对于"按主键取一条"的场景，`get` 比 `scalar(select().where(id==...))` 更高效、更语义化。

**`scalars` vs `execute`**：`session.execute(select(User))` 返回 `Result`，迭代得到的是 `Row` 对象（单元素元组）；`session.scalars(select(User))` 返回 `ScalarResult`，迭代直接得到 `User` 实例。查询整行模型时用 `scalars` 更方便；查询多列时用 `execute` 拿 `Row`。

**`add` 的级联**：如果待 add 的对象通过 `relationship` 关联了其他对象（如 `post.author = user`），且关系配置了 `cascade="save-update"`（默认），那么关联对象也会被自动加入待写队列，无需逐个 `add`。

#### 例子

**新增（含级联）**：

```python
from sqlalchemy.orm import Session

with SessionLocal() as session:
    # 单个
    alice = User(name="Alice", email="alice@x.com", age=30)
    session.add(alice)

    # 批量
    session.add_all([
        User(name="Bob", email="bob@x.com", age=25),
        User(name="Carol", email="carol@x.com", age=35),
    ])
    session.commit()  # 一次性 INSERT 三条
```

**查询**：

```python
from sqlalchemy import select

with SessionLocal() as session:
    # 按主键查（最推荐）
    user = session.get(User, 1)
    print(user.name)  # Alice

    # 查询单个（条件查询）
    user = session.scalar(select(User).where(User.email == "bob@x.com"))
    print(user.name)  # Bob

    # 查询多个
    users = session.scalars(select(User).order_by(User.age)).all()
    for u in users:
        print(u.name, u.age)

    # 只查部分列（返回 Row，不是模型实例）
    rows = session.execute(select(User.name, User.age).where(User.age > 28)).all()
    for r in rows:
        print(r.name, r.age)  # Row 对象，用 .name 访问
```

**修改**：

```python
with SessionLocal() as session:
    user = session.get(User, 1)
    user.age = 31           # 修改属性
    user.email = "alice_v2@x.com"
    session.commit()        # 自动生成 UPDATE users SET age=?, email=? WHERE id=?
    # 只更新变化的列，未变的列不在 UPDATE 中
```

**删除**：

```python
with SessionLocal() as session:
    user = session.get(User, 2)
    session.delete(user)
    session.commit()  # DELETE FROM users WHERE id=2
```

**`merge`（合并 detached 对象）**：

```python
# 场景：对象从 API 反序列化回来，不在任何 Session 中
detached_user = User(id=1, name="Alice Updated", email="alice@x.com")

with SessionLocal() as session:
    # merge 按 id 查找已有对象，把属性覆盖上去；不存在则新建
    managed = session.merge(detached_user)
    session.commit()
```

**`flush` 手动触发**：

```python
with SessionLocal() as session:
    alice = User(name="Alice", email="a@x.com")
    session.add(alice)
    session.flush()        # 立即 INSERT，alice.id 被回填
    print(alice.id)        # 有值了
    # 此时事务尚未提交，其他 Session 看不到这条记录
    session.commit()
```

#### 总结

- ORM CRUD 围绕 `session.add/delete/get/scalar/scalars` 展开，操作的是模型实例。
- **按主键查用 `session.get(Model, pk)`**，走身份映射，最高效；条件查用 `session.scalar(select())`。
- 查整行模型用 `session.scalars(stmt).all()` 直接得实例列表；查多列用 `session.execute(stmt)` 得 `Row`。
- 修改只需改属性 + `commit`，变更追踪自动生成最小化 `UPDATE`。
- `merge` 用于把 detached 对象（如 API 反序列化结果）并入 Session，按主键 upsert。
- 常见坑：`scalar` 返回 None 时未判空就访问属性报 `AttributeError`；`execute` 迭代得到 `Row` 而非模型实例，需用 `.name` 而非直接当模型用。

---

### 第12讲 select() 查询（2.0 风格）

#### 概念

2.0 风格下，ORM 与 Core 共用同一套 `select()` 查询 API，这是 2.0 最重要的统一。本讲系统讲解 ORM 视角下的 `select()`：过滤（`where`/`filter_by`）、排序、分页、只查部分列、实体查询、子查询包装等。与 Core 的区别在于：ORM 的 `select(Model)` 返回模型实例，可以用 `Model.属性` 构造表达式（而非 `table.c.列名`），且查询经过 Session 时自动纳入身份映射。

#### 原理

**ORM 查询的两种过滤写法**：

- **`where(Model.属性 == 值)`**：表达式风格，与 Core 一致，类型安全，支持复杂组合（`and_`/`or_`）。
- **`filter_by(属性=值)`**：关键字参数风格，更简洁，但只支持等值比较，不支持 `<`/`>`/`like` 等复杂条件。

**`select` 的目标类型决定返回**：

- `select(User)`：查整行，`scalars().all()` 得 `list[User]`。
- `select(User.name, User.age)`：查多列，`execute().all()` 得 `list[Row]`。
- `select(User.name)`：查单列，`scalars().all()` 得 `list[str]`。
- `select(func.count(User.id))`：聚合，`scalar()` 得单个值。

**`limit`/`offset` 分页**：与 Core 完全一致。但注意深分页性能问题，生产环境推荐改用"键集分页"（`WHERE id > last_id ORDER BY id LIMIT n`）。

**`with_only_columns` / `options`**：高级查询控制，如只加载部分列（延迟加载大字段）、`load_only` 优化。

#### 例子

**过滤与排序**：

```python
from sqlalchemy import select, or_, desc

with SessionLocal() as session:
    # where 表达式风格（推荐）
    stmt = select(User).where(User.age > 25).order_by(desc(User.age))
    users = session.scalars(stmt).all()

    # filter_by 关键字风格（简洁，仅等值）
    stmt = select(User).filter_by(name="Alice")
    alice = session.scalar(stmt)

    # 复杂条件
    stmt = select(User).where(
        or_(User.age < 20, User.age > 50),
        User.name != "Bob",
    )
```

**分页**：

```python
PAGE_SIZE = 10

def get_users_page(session, page: int):
    stmt = (
        select(User)
        .order_by(User.id)
        .limit(PAGE_SIZE)
        .offset((page - 1) * PAGE_SIZE)
    )
    return session.scalars(stmt).all()

# 键集分页（高性能，避免深分页扫描）
def get_users_after(session, last_id: int):
    stmt = (
        select(User)
        .where(User.id > last_id)
        .order_by(User.id)
        .limit(PAGE_SIZE)
    )
    return session.scalars(stmt).all()
```

**只查部分列**：

```python
with SessionLocal() as session:
    # 查多列，返回 Row
    rows = session.execute(
        select(User.id, User.name, User.age).where(User.age > 25)
    ).all()
    for r in rows:
        print(r.id, r.name, r.age)

    # 查单列，返回标量列表
    names = session.scalars(select(User.name)).all()
    print(names)  # ['Alice', 'Bob', 'Carol']

    # 聚合
    count = session.scalar(select(func.count(User.id)))
    max_age = session.scalar(select(func.max(User.age)))
```

**`first` / `one` / `one_or_none`**：

```python
# first：取第一条，无则 None
u = session.scalars(select(User).where(User.age > 25)).first()

# one：期望恰好一条，多了报 MultipleResultsFound，无则 NoResultFound
u = session.scalars(select(User).where(User.id == 1)).one()

# one_or_none：期望 0 或 1 条，多了报错，无则 None
u = session.scalars(select(User).where(User.email == "x@x.com")).one_or_none()
```

**`load_only` 优化（只加载部分列）**：

```python
from sqlalchemy.orm import load_only

# 只查 id 和 name，不加载 email/bio 等大字段
stmt = select(User).options(load_only(User.id, User.name))
users = session.scalars(stmt).all()
# 访问 user.email 会触发延迟加载（额外查询）
```

**子查询包装 `select_entity`**：

```python
# 把子查询结果包装成实体（高级用法）
subq = select(User.id, User.name, User.age).where(User.age > 25).subquery()
stmt = select(subq.c.name, subq.c.age)
rows = session.execute(stmt).all()
```

#### 总结

- 2.0 统一用 `select()`，ORM 与 Core 共用 API；ORM 用 `Model.属性`，Core 用 `table.c.列名`。
- `where` 表达式风格支持复杂条件，`filter_by` 关键字风格简洁但仅等值——优先用 `where`。
- `select(Model)` 返回模型实例（用 `scalars`）；`select(Model.col1, Model.col2)` 返回 `Row`（用 `execute`）。
- `first`/`one`/`one_or_none` 语义不同：`one` 严格期望单条，多了少了都报错，适合按唯一键查。
- 深分页用键集分页（`WHERE id > last_id`）替代 `offset`，避免扫描跳过的行。
- 常见坑：`scalar` 用于多行查询只返回第一条（静默丢数据）；`one` 在可能无结果时会抛 `NoResultFound`，需 try/except。

---

## 第4章 关系映射

关系映射是 ORM 最强大的能力，也是最容易踩坑的地方。本章系统讲解一对多、多对一、多对多三种关系，以及关系加载策略（lazy/eager）。学完本章，你能用对象导航的方式操作关联数据，如 `user.posts`、`post.tags`，并理解不同加载策略对性能的影响。

---

### 第13讲 一对多关系

#### 概念

一对多是最基础的关系：一个 User 拥有多个 Post。在数据库层面，"多"的一方（Post）持有"一"的一方（User）的主键作为外键；在 ORM 层面，通过 `relationship()` 在两个类上建立双向导航——`user.posts` 返回该用户的所有文章列表，`post.author` 返回该文章的作者对象。`relationship()` 不对应任何数据库列，它是纯 Python 层的"虚拟属性"，由 SQLAlchemy 在内存中维护。

#### 原理

**一对多的数据库结构**：

```
users              posts
─────              ─────
id (PK) ◄─────── author_id (FK)
name                title
                    body
```

外键在"多"的一方（posts.author_id 指向 users.id），这是关系型数据库的标准做法。

**`relationship` 的工作机制**：

- `relationship` 不生成列，而是在类上创建一个 `InstrumentedAttribute` 描述符。
- 访问 `user.posts` 时，SQLAlchemy 自动生成 `SELECT * FROM posts WHERE author_id = ?` 并返回结果。
- 默认是**延迟加载**（lazy）：第一次访问属性才发查询，之后缓存在对象上。
- 双向关系用 `back_populates` 互相引用：在 User 上 `relationship(back_populates="author")`，在 Post 上 `relationship(back_populates="posts")`，两侧属性名必须对应。

**`back_populates` vs `backref`**：

- `back_populates`：两侧都显式声明 `relationship`，互相指名对方的属性名。**2.0 推荐**，显式清晰。
- `backref`：只在一侧声明 `relationship(backref="...")`，SQLAlchemy 自动在另一侧创建反向属性。简洁但隐式，不推荐。

**级联（cascade）**：控制"一"的一方被操作时，"多"的一方如何联动。常用 `cascade="all, delete-orphan"`：删除 User 时自动删除其所有 Post，且从 `user.posts` 列表移除一个 Post 会自动删除该 Post。

#### 例子

**定义一对多双向关系**：

```python
from sqlalchemy import ForeignKey, String
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, relationship

class Base(DeclarativeBase):
    pass

class User(Base):
    __tablename__ = "users"
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(50))

    # 一对多：一个 User 有多个 Post
    posts: Mapped[list["Post"]] = relationship(
        back_populates="author",
        cascade="all, delete-orphan",  # 删除 User 时级联删除其 Post
    )

    def __repr__(self):
        return f"<User {self.name}>"

class Post(Base):
    __tablename__ = "posts"
    id: Mapped[int] = mapped_column(primary_key=True)
    title: Mapped[str] = mapped_column(String(200))
    author_id: Mapped[int] = mapped_column(ForeignKey("users.id"))

    # 多对一：多个 Post 属于一个 User
    author: Mapped["User"] = relationship(back_populates="posts")

    def __repr__(self):
        return f"<Post {self.title}>"
```

**使用关系导航**：

```python
from sqlalchemy import create_engine, select
from sqlalchemy.orm import Session, sessionmaker

engine = create_engine("sqlite:///./rel.db", echo=True)
Base.metadata.create_all(engine)
SessionLocal = sessionmaker(engine)

with SessionLocal() as session:
    # 创建：关联对象自动级联写入
    alice = User(name="Alice")
    alice.posts = [
        Post(title="Hello World"),
        Post(title="ORM Guide"),
    ]
    session.add(alice)  # posts 会被自动加入（cascade save-update）
    session.commit()

    # 查询：通过 user.posts 导航
    alice = session.get(User, 1)
    print(alice.posts)  # [<Post Hello World>, <Post ORM Guide>]
    # 访问 .posts 时自动发 SELECT FROM posts WHERE author_id=1

    # 反向导航
    post = session.get(Post, 1)
    print(post.author)  # <User Alice>
```

**修改关系**：

```python
with SessionLocal() as session:
    alice = session.get(User, 1)
    # 添加新 Post
    alice.posts.append(Post(title="Third Post"))
    session.commit()

    # 把 Post 转给另一个 User
    bob = User(name="Bob")
    session.add(bob)
    post = session.get(Post, 1)
    post.author = bob  # 自动更新 author_id
    session.commit()
```

**级联删除**：

```python
with SessionLocal() as session:
    alice = session.get(User, 1)
    session.delete(alice)
    session.commit()
    # 自动执行：DELETE FROM posts WHERE author_id=1
    #           DELETE FROM users WHERE id=1
```

#### 总结

- 一对多：外键在"多"的一方，`relationship` 在两侧建立双向导航。
- `relationship` 是纯 Python 层虚拟属性，不对应列，访问时自动生成查询。
- **2.0 推荐 `back_populates`**（两侧显式声明），避免 `backref` 的隐式性。
- `cascade="all, delete-orphan"` 实现级联删除，且从列表移除元素也会删除该元素。
- 默认延迟加载（lazy），第一次访问属性才发查询——N+1 问题的根源（第19讲详解）。
- 常见坑：忘了 `back_populates` 导致单向关系；`cascade` 配置不当导致删除时报外键约束错误。

---

### 第14讲 多对一与双向关系

#### 概念

多对一是一对多的反向视角：多个 Post 属于一个 User。从 ORM 建模角度，多对一和一对多是同一组外键的两侧，区别只在于你从哪一侧"看"。本讲重点讲解双向关系的细节：`back_populates` 的同步机制、`foreign`/`primaryjoin` 自定义连接条件、自引用关系（如评论的父评论），以及 `cascade` 的完整选项。理解这些，你能处理任意复杂度的关系建模。

#### 原理

**双向同步机制**：当你在一侧设置关系属性时，SQLAlchemy 自动在另一侧同步。例如 `post.author = alice` 会自动把 `alice.posts` 列表加上这个 post；反之 `alice.posts.append(post)` 会自动设 `post.author = alice`。这个同步由 `back_populates` 驱动——两侧的 `relationship` 互相知道对方的存在。

**`cascade` 的完整选项**（逗号分隔的组合）：

| 选项 | 说明 |
|------|------|
| `save-update` | `session.add` 时级联加入待写队列（默认开启） |
| `merge` | `session.merge` 时级联（默认开启） |
| `refresh-expire` | `session.refresh/expire` 时级联（默认开启） |
| `expunge` | `session.expunge` 时级联（默认开启） |
| `delete` | `session.delete` 时级联删除关联对象 |
| `delete-orphan` | 关联对象从集合移除时自动删除（必须配合 delete） |
| `all` | 除 delete-orphan 外的所有上述选项 |

常用组合：`"all, delete-orphan"`（父子删除联动）、`"save-update, merge"`（默认，不级联删除）。

**自引用关系**：同一张表内的层级关系（如分类树、评论树），需要用 `remote_side` 指定哪一侧是"父"：

```python
parent_id = Column(ForeignKey("categories.id"))
parent = relationship("Category", remote_side=[id])  # id 是父侧
children = relationship("Category")                   # 默认 remote_side 是 parent_id
```

#### 例子

**双向关系同步演示**：

```python
with SessionLocal() as session:
    alice = User(name="Alice")
    post = Post(title="Test")

    # 从一侧设置，另一侧自动同步
    post.author = alice
    print(post in alice.posts)  # True，自动同步

    # 从另一侧设置也一样
    alice.posts.append(Post(title="Another"))
    session.add(alice)
    session.commit()
```

**自引用关系（评论树）**：

```python
class Comment(Base):
    __tablename__ = "comments"
    id: Mapped[int] = mapped_column(primary_key=True)
    body: Mapped[str] = mapped_column(String(1000))
    parent_id: Mapped[Optional[int]] = mapped_column(ForeignKey("comments.id"))

    # 多对一：指向父评论
    parent: Mapped[Optional["Comment"]] = relationship(
        "Comment", back_populates="replies", remote_side=[id]
    )
    # 一对多：指向子评论列表
    replies: Mapped[list["Comment"]] = relationship(
        "Comment", back_populates="parent", cascade="all, delete-orphan"
    )

# 使用
with SessionLocal() as session:
    root = Comment(body="根评论")
    root.replies = [
        Comment(body="回复1"),
        Comment(body="回复2"),
    ]
    session.add(root)
    session.commit()

    root = session.get(Comment, 1)
    for reply in root.replies:
        print(reply.body, "→ 父:", reply.parent.body)
```

**自定义连接条件（`foreign` + `primaryjoin`）**：

```python
from sqlalchemy import ForeignKey, and_
from sqlalchemy.orm import foreign

# 场景：Post 有 author_id 和 editor_id 两个外键都指向 users
class Post(Base):
    __tablename__ = "posts"
    id: Mapped[int] = mapped_column(primary_key=True)
    title: Mapped[str]
    author_id: Mapped[int] = mapped_column(ForeignKey("users.id"))
    editor_id: Mapped[Optional[int]] = mapped_column(ForeignKey("users.id"))

    author: Mapped["User"] = relationship(foreign_keys=[author_id])
    editor: Mapped[Optional["User"]] = relationship(foreign_keys=[editor_id])
```

**`cascade` 不同配置对比**：

```python
# 配置1：默认（不级联删除）
posts: Mapped[list["Post"]] = relationship()  # 删 User 时 Post 的 author_id 置 NULL（若允许）或报错

# 配置2：级联删除
posts: Mapped[list["Post"]] = relationship(cascade="all, delete-orphan")

# 配置3：不级联，但删除时设外键为 NULL（需外键允许 NULL）
posts: Mapped[list["Post"]] = relationship(
    passive_deletes=True,  # 让数据库的 ON DELETE SET NULL 生效，不在 ORM 层逐个删
)
```

#### 总结

- 多对一与一对多是同一外键的两侧视角，`back_populates` 实现双向自动同步。
- `cascade="all, delete-orphan"` 是父子关系的标配：删父级联删子，从列表移除也删子。
- 自引用关系用 `remote_side=[id]` 指定父侧，常用于树形结构（评论、分类）。
- 多个外键指向同一表时，必须用 `foreign_keys=[...]` 明确每个 relationship 用哪个外键。
- `passive_deletes=True` 让数据库的 `ON DELETE CASCADE` 生效，比 ORM 层逐个删除更高效。
- 常见坑：`cascade` 写成 `"delete"` 而非 `"all, delete-orphan"` 导致从列表移除不删子；自引用忘了 `remote_side` 导致关系方向反了。

---

### 第15讲 多对多关系

#### 概念

多对多关系需要一张**关联表**（association table）作为中间桥梁：例如 Student 和 Course 是多对多，需要一张 `student_course` 表存储选课记录。关联表通常只有两个外键列（分别指向两端的主键），不设自己的主键（或用复合主键）。ORM 层面，通过 `relationship(secondary=关联表)` 在两端建立双向导航，SQLAlchemy 自动生成 JOIN 查询。理解关联表的设计和 `secondary` 的用法，是处理多对多的关键。

#### 原理

**多对多的数据库结构**：

```
students          student_course          courses
────────          ──────────────          ───────
id (PK) ◄─────── student_id (FK) ──────► id (PK)
name              course_id (FK) ──────►  title
                              ▲
                              └─ 复合主键 (student_id, course_id)
```

关联表 `student_course` 只有两个外键，没有其他业务字段。它的复合主键是 `(student_id, course_id)`，天然防止重复选课。

**`secondary` 参数**：`relationship(secondary=关联表)` 告诉 SQLAlchemy 这是一个多对多关系，查询时自动 JOIN 关联表。`secondary` 接受一个 `Table` 对象（Core 风格）或表名字符串。

**`secondary` 的删除行为**：多对多的级联删除比较特殊——删除一个 Student 时，SQLAlchemy 会自动删除 `student_course` 中该学生的选课记录，但**不会**删除 Course 本身（因为 Course 可能还被其他学生选）。这是通过 `secondary` 隐含的 `delete` 级联实现的。

**带额外字段的关联表**：如果关联表需要额外字段（如选课时间、成绩），就不能用简单的 `secondary`，而要用**关联对象模式**（Association Object）：把关联表也定义成一个 ORM 模型，两端的关系通过它中转。这是多对多的进阶用法。

#### 例子

**基础多对多**：

```python
from sqlalchemy import Table, Column, Integer, String, ForeignKey
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, relationship

class Base(DeclarativeBase):
    pass

# 关联表（Core 风格 Table，不需要 ORM 模型）
student_course = Table(
    "student_course", Base.metadata,
    Column("student_id", ForeignKey("students.id"), primary_key=True),
    Column("course_id", ForeignKey("courses.id"), primary_key=True),
)

class Student(Base):
    __tablename__ = "students"
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(50))

    courses: Mapped[list["Course"]] = relationship(
        secondary=student_course, back_populates="students"
    )

class Course(Base):
    __tablename__ = "courses"
    id: Mapped[int] = mapped_column(primary_key=True)
    title: Mapped[str] = mapped_column(String(100))

    students: Mapped[list["Student"]] = relationship(
        secondary=student_course, back_populates="courses"
    )
```

**使用多对多导航**：

```python
from sqlalchemy import create_engine, select
from sqlalchemy.orm import sessionmaker

engine = create_engine("sqlite:///./m2m.db", echo=True)
Base.metadata.create_all(engine)
SessionLocal = sessionmaker(engine)

with SessionLocal() as session:
    # 创建课程和学生
    math = Course(title="Math")
    physics = Course(title="Physics")
    alice = Student(name="Alice")
    bob = Student(name="Bob")

    # 选课：直接操作关系集合
    alice.courses = [math, physics]
    bob.courses = [math]

    session.add_all([math, physics, alice, bob])
    session.commit()
    # 自动生成：INSERT students/courses + INSERT student_course 多条

    # 查询：学生选了哪些课
    alice = session.get(Student, 1)
    print([c.title for c in alice.courses])  # ['Math', 'Physics']

    # 反向查询：课程有哪些学生
    math = session.get(Course, 1)
    print([s.name for s in math.students])  # ['Alice', 'Bob']
```

**删除自动清理关联表**：

```python
with SessionLocal() as session:
    alice = session.get(Student, 1)
    session.delete(alice)
    session.commit()
    # 自动：DELETE FROM student_course WHERE student_id=1
    #       DELETE FROM students WHERE id=1
    # Course 不受影响
```

**关联对象模式（带额外字段）**：

```python
from datetime import datetime

# 当关联表需要额外字段时，必须定义为 ORM 模型
class Enrollment(Base):
    __tablename__ = "enrollment"
    student_id: Mapped[int] = mapped_column(ForeignKey("students.id"), primary_key=True)
    course_id: Mapped[int] = mapped_column(ForeignKey("courses.id"), primary_key=True)
    enrolled_at: Mapped[datetime] = mapped_column(default=datetime.now)
    grade: Mapped[Optional[str]] = mapped_column(String(2))

    student: Mapped["Student"] = relationship(back_populates="enrollments")
    course: Mapped["Course"] = relationship(back_populates="enrollments")

class Student(Base):
    __tablename__ = "students2"
    # ...
    enrollments: Mapped[list["Enrollment"]] = relationship(back_populates="student")

class Course(Base):
    __tablename__ = "courses2"
    # ...
    enrollments: Mapped[list["Enrollment"]] = relationship(back_populates="course")

# 使用：通过 Enrollment 访问额外字段
enroll = Enrollment(student=alice, course=math, grade="A")
session.add(enroll)
session.commit()
print(enroll.enrolled_at, enroll.grade)
```

#### 总结

- 多对多需要**关联表**（只有两个外键的中间表），`relationship(secondary=关联表)` 建立导航。
- 关联表通常用 Core 的 `Table` 定义即可，无需 ORM 模型（除非需要额外字段）。
- 删除一端时自动清理关联表记录，另一端对象不受影响。
- **关联表需要额外字段时必须用"关联对象模式"**：把关联表定义为 ORM 模型，两端关系通过它中转。
- `secondary` 接受 `Table` 对象或表名字符串；用表名字符串时需确保表已在 metadata 中注册。
- 常见坑：关联表设了自增主键导致复合唯一约束失效；用 `secondary` 又想访问额外字段——必须改用关联对象模式。

---

### 第16讲 关系加载策略

#### 概念

关系加载策略决定 SQLAlchemy **何时**以及**如何**从数据库加载关联对象。默认的 `lazy="select"`（延迟加载）在第一次访问关系属性时才发查询，这会导致经典的 **N+1 查询问题**：查 N 个用户后遍历访问每个用户的文章，会发 1 + N 次查询。本讲系统讲解各种加载策略：`selectinload`（IN 查询预加载）、`joinedload`（JOIN 预加载）、`raiseload`（禁止延迟加载）、`subqueryload`（子查询预加载），以及如何在模型定义和查询时配置它们。

#### 原理

**加载策略一览**：

| 策略 | 机制 | 查询数 | 适用场景 |
|------|------|--------|---------|
| `select`（默认 lazy） | 访问属性时发 `SELECT WHERE fk=?` | N+1 | 关系很少被访问 |
| `selectinload` | 第二次查询用 `SELECT WHERE fk IN (...)` 一次性加载 | 2 | 集合关系预加载（推荐） |
| `joinedload` | 用 JOIN 在一次查询中加载 | 1 | 多对一预加载、单值关系 |
| `subqueryload` | 用子查询一次性加载 | 2 | 兼容旧版，现多用 selectinload |
| `raise` | 访问属性直接抛异常 | - | 强制显式预加载，防 N+1 |
| `noload` | 访问属性返回空 | - | 明确不加载 |
| `write_only` | 只能写不能读 | - | 大集合只追加不查询 |

**N+1 问题详解**：

```python
# N+1：查 100 个用户，访问每个用户的 posts
users = session.scalars(select(User)).all()  # 1 次查询
for u in users:
    print(u.posts)  # 每次访问发 1 次查询 → 共 100 次
# 总计：101 次查询！
```

**`selectinload` 的原理**：第一次查询拿到所有 User 的 id 后，发第二条 `SELECT * FROM posts WHERE author_id IN (1,2,3,...,100)`，一次性把所有 Post 加载到内存，再按 author_id 分配到各 User。总共 2 次查询。

**`joinedload` 的原理**：用 `LEFT OUTER JOIN` 在一次查询中同时取 User 和 Post，结果按 User 主键去重。适合多对一（每个 Post 只有一个 Author，JOIN 不膨胀）；一对多用 joinedload 会导致行膨胀（一个 User 有 N 个 Post 就重复 N 次 User 数据），需配合 `distinct` 或改用 selectinload。

**配置时机**：

- **模型定义时**：`relationship(lazy="selectin")` 设默认策略，所有查询生效。
- **查询时**：`select(User).options(selectinload(User.posts))` 单次查询覆盖默认策略——更灵活，推荐。

#### 例子

**N+1 问题复现**：

```python
with SessionLocal() as session:
    users = session.scalars(select(User)).all()  # 1 次查询
    for u in users:
        print(u.name, len(u.posts))  # 每次访问 .posts 发 1 次查询
    # 日志会看到 1 + N 条 SELECT
```

**`selectinload`（推荐方案）**：

```python
from sqlalchemy.orm import selectinload, joinedload, raiseload

with SessionLocal() as session:
    stmt = select(User).options(selectinload(User.posts))
    users = session.scalars(stmt).all()  # 2 次查询：users + posts WHERE author_id IN (...)
    for u in users:
        print(u.name, len(u.posts))  # 不再发查询，已预加载
```

**`joinedload`（适合多对一）**：

```python
with SessionLocal() as session:
    # 多对一：每个 Post 只有一个 Author，JOIN 不膨胀
    stmt = select(Post).options(joinedload(Post.author))
    posts = session.scalars(stmt).all()  # 1 次查询（JOIN）
    for p in posts:
        print(p.title, p.author.name)  # 不再发查询

    # 一对多用 joinedload 需配合 unique()
    stmt = select(User).options(joinedload(User.posts))
    users = session.scalars(stmt).unique().all()  # 必须调 unique()！
    # 因为 JOIN 会让一个 User 重复多行
```

**`raiseload` 防御性编程**：

```python
from sqlalchemy.orm import raiseload

with SessionLocal() as session:
    # 显式禁止延迟加载：访问未预加载的关系直接报错
    stmt = select(User).options(raiseload(User.posts))
    users = session.scalars(stmt).all()
    for u in users:
        try:
            _ = u.posts  # 抑制 raise，强制开发者显式 selectinload
        except Exception as e:
            print("未预加载，被 raiseload 拦截")
```

**在模型定义时设默认策略**：

```python
class User(Base):
    # ...
    posts: Mapped[list["Post"]] = relationship(
        lazy="selectin",  # 默认预加载，避免 N+1
        cascade="all, delete-orphan",
    )

    # 某些大集合永远不预加载，且禁止隐式延迟加载
    audit_logs: Mapped[list["AuditLog"]] = relationship(lazy="raise")
```

**嵌套预加载**：

```python
# 预加载 User → Posts → Post.tags（三级）
stmt = select(User).options(
    selectinload(User.posts).selectinload(Post.tags)
)
users = session.scalars(stmt).all()  # 3 次查询，无 N+1
```

#### 总结

- 默认 `lazy="select"` 是 N+1 问题的根源——遍历集合访问关系属性时务必预加载。
- **一对多集合预加载首选 `selectinload`**（2 次查询，无行膨胀）；多对一预加载用 `joinedload`（1 次查询）。
- `joinedload` 用于一对多时必须调 `.unique()`，因为 JOIN 会导致主表行重复。
- 查询时用 `.options(...)` 覆盖模型默认策略，比模型定义时设 `lazy` 更灵活——推荐。
- `raiseload` 是防御性编程利器：在 API 层禁止隐式延迟加载，强制开发者显式预加载，从源头消灭 N+1。
- 常见坑：以为 `lazy="select"` 够用结果线上 N+1 把数据库打挂；`joinedload` 一对多忘 `.unique()` 报 `The unique() method must be invoked`。

---

## 第5章 进阶查询与性能

本章深入查询的高级技巧：JOIN 与子查询、聚合与窗口函数，以及性能优化与 N+1 问题的系统解决方案。学完本章，你能写出任意复杂度的查询，并具备在生产环境排查和优化慢查询的能力。

---

### 第17讲 连接查询与子查询

#### 概念

连接查询（JOIN）和子查询（Subquery）是关系型数据库表达"跨表逻辑"的两大手段。SQLAlchemy Core/ORM 都能构造任意层级的 JOIN 和子查询。本讲系统讲解：`join()` 的各种形式（INNER/LEFT/RIGHT/FULL）、多表 JOIN、自连接、`subquery()` 子查询包装、`alias()` 表别名、`exists()` 存在性子查询、`lateral` 横向连接。掌握这些，你能用 SQLAlchemy 表达 SQL 能表达的任何查询。

#### 原理

**`join()` 的签名与变体**：

```python
select(a).join(b, a.c.id == b.c.a_id)        # INNER JOIN，显式 ON
select(a).join(b)                              # 有外键时自动推导 ON
select(a).join(b, isouter=True)               # LEFT OUTER JOIN
select(a).join(b, full=True)                  # FULL OUTER JOIN（PG 支持）
select(a).join(b, a.c.id == b.c.a_id, isouter=True)  # LEFT JOIN + 显式 ON
```

**`join_from` 显式指定左表**：默认 `select(a).join(b)` 以 a 为左表；若想以 b 为左表或从多表 JOIN 起步，用 `select(a, b).join_from(c, d, c.c.id == d.c.c_id)`。

**子查询的两种形态**：

- **`subquery()`**：把一个 `select` 包装成"派生表"，可在外层 `select` 中像表一样引用其 `.c` 列。对应 SQL 的 `SELECT ... FROM (SELECT ...) AS sub`。
- **`scalar_subquery()` / `labeled`**：把子查询作为标量值用在 SELECT 列表或 WHERE 中，如 `WHERE age > (SELECT AVG(age) FROM users)`。

**`alias()` 表别名**：同一张表在查询中多次出现（自连接）时，必须用 `alias` 创建别名，否则列引用会冲突。

**`exists()` 存在性子查询**：生成 `EXISTS (SELECT ...)` 表达式，常用于"存在关联记录才返回"的过滤，比 `IN` 子查询在某些数据库上更高效。

#### 例子

**多表 JOIN**：

```python
from sqlalchemy import select, func

# 假设 users / posts / comments 三张表
# 查询每个用户的文章数和评论数
stmt = (
    select(User.name, func.count(Post.id).label("post_cnt"))
    .join(Post, User.id == Post.author_id)       # users JOIN posts
    .group_by(User.id)
    .order_by(desc("post_cnt"))
)

# 三表 JOIN + 多列
stmt = (
    select(User.name, Post.title, Comment.body)
    .join(Post, User.id == Post.author_id)
    .join(Comment, Post.id == Comment.post_id)
    .where(User.name == "Alice")
)
```

**LEFT JOIN（含无文章的用户）**：

```python
stmt = (
    select(User.name, func.count(Post.id).label("cnt"))
    .join(Post, User.id == Post.author_id, isouter=True)  # LEFT JOIN
    .group_by(User.id)
)
# 没有文章的用户 cnt 为 0（COUNT(NULL) = 0）
```

**自连接（alias）**：

```python
from sqlalchemy.orm import aliased

# 场景：员工表 employees 有 manager_id 自引用外键
# 查询每个员工及其直接上级
Employee = User  # 复用模型
Manager = aliased(User)  # 同一模型的别名

stmt = (
    select(User.name.label("employee"), Manager.name.label("manager"))
    .join(Manager, User.manager_id == Manager.id)
)
```

**子查询（派生表）**：

```python
# 子查询：每个用户的文章数
post_count_subq = (
    select(Post.author_id, func.count(Post.id).label("cnt"))
    .group_by(Post.author_id)
    .subquery()
)

# 外查询：文章数 >= 3 的用户
stmt = (
    select(User.name, post_count_subq.c.cnt)
    .join(post_count_subq, User.id == post_count_subq.c.author_id)
    .where(post_count_subq.c.cnt >= 3)
)
```

**标量子查询**：

```python
# 查询年龄大于平均年龄的用户
avg_age = select(func.avg(User.age)).scalar_subquery()
stmt = select(User).where(User.age > avg_age)

# SELECT * FROM users WHERE age > (SELECT AVG(age) FROM users)
```

**EXISTS 存在性查询**：

```python
from sqlalchemy import exists

# 查询有文章的用户
has_post = exists(Post).where(Post.author_id == User.id)
stmt = select(User).where(has_post)
# SELECT * FROM users WHERE EXISTS (SELECT 1 FROM posts WHERE posts.author_id = users.id)

# 反向：没有文章的用户
stmt = select(User).where(~has_post)
```

**ORM 视角的 JOIN（用关系自动推导 ON）**：

```python
# ORM 的 select 会自动用 relationship 推导 JOIN
stmt = select(User).join(User.posts).where(Post.title.like("%Guide%"))
# 自动 ON users.id = posts.author_id
```

#### 总结

- `join(target, onclause, isouter=, full=)` 是 JOIN 的完整签名；有外键时可省略 `onclause`。
- 多表 JOIN 用链式 `.join()` 依次连接；`join_from` 显式指定左表。
- 自连接必须用 `aliased(Model)` 创建别名，否则列引用冲突。
- `subquery()` 把 SELECT 包装成派生表，外层用 `.c.列名` 引用；`scalar_subquery()` 用作标量值。
- `exists()` 生成 `EXISTS` 子查询，适合"存在关联记录"的过滤，比 `IN` 子查询更高效（尤其关联表大时）。
- 常见坑：多表 JOIN 时 `select` 的列顺序决定结果列顺序；ORM 的 `select(User).join(User.posts)` 会自动去重 User，但 `select(User, Post).join(...)` 不会。

---

### 第18讲 聚合、分组与窗口函数

#### 概念

聚合函数（`COUNT`/`SUM`/`AVG`/`MAX`/`MIN`）配合 `GROUP BY` 是报表查询的核心。窗口函数（Window Function）是 SQL 的高级特性，能在不压缩行数的前提下进行"分组内计算"（如排名、累计求和、移动平均），是分析型查询的利器。本讲讲解 SQLAlchemy 如何构造聚合查询和窗口函数，以及 `func` 调用任意 SQL 函数的能力。

#### 原理

**聚合函数与 `GROUP BY`**：

- `func.count(col)`：计数，`count(User.id)` 计非 NULL 值数，`count()` 或 `count(literal())` 计所有行数。
- `func.sum/avg/max/min(col)`：求和/均值/最大/最小，返回类型与列相关。
- `GROUP BY` 按指定列分组，SELECT 中非聚合列必须出现在 GROUP BY 中（SQL 标准）。
- `HAVING` 过滤分组后的结果（区别于 WHERE 过滤原始行）。

**窗口函数的语法**：

```sql
func() OVER (
    PARTITION BY <分组列>
    ORDER BY <排序列>
    ROWS BETWEEN <frame>
)
```

- `PARTITION BY`：定义窗口分组（类似 GROUP BY，但不压缩行）。
- `ORDER BY`：窗口内排序。
- `ROWS BETWEEN ... AND ...`：定义帧（如"前3行到当前行"），用于移动平均。

**SQLAlchemy 的 `over()` 方法**：任何 `func` 表达式都有 `.over()` 方法，参数 `partition_by=` 和 `order_by=` 对应 SQL 的 PARTITION BY 和 ORDER BY。

**常用窗口函数**：

| 函数 | 用途 |
|------|------|
| `func.row_number().over(...)` | 行号（无并列） |
| `func.rank().over(...)` | 排名（有并列，跳号） |
| `func.dense_rank().over(...)` | 密集排名（有并列，不跳号） |
| `func.lag(col, n).over(...)` | 前 n 行的值 |
| `func.lead(col, n).over(...)` | 后 n 行的值 |
| `func.sum(col).over(...)` | 累计求和 |
| `func.avg(col).over(...)` | 移动平均 |

#### 例子

**基础聚合与分组**：

```python
from sqlalchemy import select, func, desc

# 每个用户的文章数，按数降序
stmt = (
    select(User.name, func.count(Post.id).label("post_count"))
    .join(Post, User.id == Post.author_id, isouter=True)
    .group_by(User.id)
    .having(func.count(Post.id) >= 2)
    .order_by(desc("post_count"))
)

with SessionLocal() as session:
    for row in session.execute(stmt):
        print(row.name, row.post_count)
```

**多维度分组**：

```python
# 按用户和文章状态分组，统计每种组合的数量
stmt = (
    select(
        User.name,
        Post.status,
        func.count().label("cnt"),
        func.avg(Post.view_count).label("avg_views"),
    )
    .join(Post)
    .group_by(User.id, Post.status)
)
```

**窗口函数：排名**：

```python
# 每篇文章在其作者内的浏览量排名
stmt = (
    select(
        Post.title,
        User.name,
        Post.view_count,
        func.rank().over(
            partition_by=User.id,
            order_by=Post.view_count.desc()
        ).label("rank_in_author"),
    )
    .join(User)
)

# 每个作者浏览量最高的文章（rank = 1）
stmt_top = (
    select(ranked.c.title, ranked.c.name, ranked.c.view_count)
    .from_(stmt.subquery().alias("ranked"))
    .where(ranked.c.rank_in_author == 1)
)
```

**窗口函数：累计求和与移动平均**：

```python
from sqlalchemy import literal

# 按日期排序的累计销售额
stmt = (
    select(
        Order.date,
        Order.amount,
        func.sum(Order.amount).over(order_by=Order.date).label("cumulative"),
    )
)

# 3 日移动平均（当前行 + 前2行）
stmt = (
    select(
        Order.date,
        Order.amount,
        func.avg(Order.amount).over(
            order_by=Order.date,
            rows=(-2, 0),  # ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
        ).label("ma3"),
    )
)
```

**`lag` / `lead`（前后行对比）**：

```python
# 每天的销售额与前一天对比
stmt = (
    select(
        Order.date,
        Order.amount,
        func.lag(Order.amount, 1).over(order_by=Order.date).label("prev_amount"),
        (Order.amount - func.lag(Order.amount, 1).over(order_by=Order.date)).label("diff"),
    )
)
```

**`FILTER` 子句（PostgreSQL 特有）**：

```python
# 一次性按多种条件计数（PG/SQLite 3.9+）
stmt = (
    select(
        User.name,
        func.count(Post.id).filter(Post.status == "published").label("published_cnt"),
        func.count(Post.id).filter(Post.status == "draft").label("draft_cnt"),
    )
    .join(Post, isouter=True)
    .group_by(User.id)
)
# 比 CASE WHEN 更简洁
```

#### 总结

- 聚合用 `func.count/sum/avg/max/min`，配合 `group_by` 和 `having`；`having` 过滤分组、`where` 过滤原始行。
- 窗口函数用 `func.xxx().over(partition_by=, order_by=, rows=)`，不压缩行数，适合排名/累计/移动平均。
- `rows=(m, n)` 定义帧：`(-2, 0)` 表示"前2行到当前行"，`(None, 0)` 表示"从开头到当前行"。
- `lag(col, n)` / `lead(col, n)` 取前/后 n 行的值，常用于环比/同比计算。
- `func.count(col)` 计非 NULL 值，`func.count()` 计所有行；想计非 NULL 用 `count(User.id)`，想计所有行用 `count(literal(1))` 或 `count()`。
- 常见坑：`GROUP BY` 后 SELECT 非聚合列必须出现在 GROUP BY 中（SQLite 宽松但其他库严格）；窗口函数的 `over()` 在 MySQL 8.0 之前不支持。

---

### 第19讲 性能优化与 N+1 问题

#### 概念

N+1 问题是 ORM 最臭名昭著的性能陷阱：查询 N 个对象后遍历访问其关系属性，触发 N 次额外查询，总计 N+1 次。本讲系统讲解 N+1 的识别、成因与解决方案，并扩展到批量操作优化（`bulk_insert_mappings`/`bulk_update_mappings`、`returning` 批量、`executemany`）、查询列裁剪（`load_only`）、连接池调优等性能要点。学完本章，你能在生产环境快速定位和解决 ORM 性能问题。

#### 原理

**N+1 的成因**：默认 `lazy="select"` 在第一次访问关系属性时才发查询，且是"逐对象"查询。遍历 N 个对象访问关系属性 = N 次查询，加上初始的 1 次查询 = N+1 次。当 N=1000 时，1001 次查询足以把数据库连接池打满。

**N+1 的三种典型场景**：

1. **遍历集合访问关系**：`for u in users: print(u.posts)` —— 最常见。
2. **序列化时访问关系**：API 返回用户列表时访问 `user.posts` 做 JSON 序列化。
3. **模板渲染时访问关系**：Jinja 模板里 `{{ user.posts }}`。

**解决方案矩阵**：

| 方案 | 适用 | 代价 |
|------|------|------|
| `selectinload` | 集合关系，N 适中 | 2 次查询 |
| `joinedload` | 多对一、单值关系 | 1 次查询，可能行膨胀 |
| `raiseload` | 防御性，强制显式预加载 | 需开发者主动配置 |
| `lazy="raise"` 模型默认 | 全局禁止隐式延迟 | 需逐一配置预加载 |
| `write_only` | 大集合只追加不查询 | 不能直接读集合 |

**批量操作优化**：

- `session.add_all([...])`：仍走工作单元，逐对象 flush，慢。
- `session.bulk_insert_mappings(Model, [dict, ...])`：跳过工作单元，直接 executemany，快 10-100 倍，但失去变更追踪和关系级联。
- `insert(Model).values([...])` + `session.execute()`：2.0 风格批量插入，性能与 bulk 相当，且支持 `returning`。
- `executemany` + `update/delete`：2.0 的 `update(Model).where(...).values(...)` 配合 `executemany` 可批量更新。

#### 例子

**识别 N+1（用 echo 看日志）**：

```python
with SessionLocal() as session:
    users = session.scalars(select(User)).all()
    # 日志：1 条 SELECT FROM users

    for u in users:
        _ = u.posts  # 每次访问发 1 条 SELECT FROM posts WHERE author_id=?
    # 日志：N 条 SELECT FROM posts —— N+1 确认！
```

**方案1：`selectinload`（推荐）**：

```python
from sqlalchemy.orm import selectinload

with SessionLocal() as session:
    stmt = select(User).options(selectinload(User.posts))
    users = session.scalars(stmt).all()
    # 日志：2 条 SELECT（users + posts WHERE author_id IN (...)）

    for u in users:
        _ = u.posts  # 不再发查询
```

**方案2：`raiseload` 全局防御**：

```python
# 在模型上设默认 lazy="raise"，强制所有查询显式预加载
class User(Base):
    posts: Mapped[list["Post"]] = relationship(lazy="raise")

# 任何未预加载的访问直接报错，逼开发者写 selectinload
with SessionLocal() as session:
    users = session.scalars(select(User)).all()
    users[0].posts  # raise! 强制改为 select(User).options(selectinload(User.posts))
```

**方案3：`load_only` 列裁剪**：

```python
from sqlalchemy.orm import load_only

# 只查需要的列，避免加载大字段（如 body TEXT）
stmt = select(Post).options(load_only(Post.id, Post.title, Post.author_id))
# SELECT posts.id, posts.title, posts.author_id FROM posts
# 访问 post.body 会触发延迟加载（额外查询）
```

**批量插入对比**：

```python
import time

data = [{"name": f"user_{i}", "email": f"u{i}@x.com"} for i in range(10000)]

# 方式1：逐个 add（慢）
t0 = time.time()
with SessionLocal() as session:
    for d in data:
        session.add(User(**d))
    session.commit()
print("逐个 add:", time.time() - t0)  # ~5-10s

# 方式2：bulk_insert_mappings（快，跳过工作单元）
t0 = time.time()
with SessionLocal() as session:
    session.bulk_insert_mappings(User, data)
    session.commit()
print("bulk_insert:", time.time() - t0)  # ~0.1-0.5s

# 方式3：2.0 风格批量插入（推荐，支持 returning）
from sqlalchemy import insert
t0 = time.time()
with SessionLocal() as session:
    session.execute(insert(User), data)
    session.commit()
print("2.0 批量:", time.time() - t0)  # ~0.1-0.5s
```

**批量更新（`executemany` + `where`）**：

```python
from sqlalchemy import update

# 批量按主键更新（2.0 风格）
updates = [{"id": 1, "name": "Alice2"}, {"id": 2, "name": "Bob2"}]
with SessionLocal() as session:
    session.execute(
        update(User).where(User.id == bindparam("id")),
        updates,  # executemany 模式
    )
    session.commit()
```

**连接池调优**：

```python
engine = create_engine(
    DB_URL,
    pool_size=20,          # 根据 DB 的 max_connections 和并发量算
    max_overflow=10,       # 高峰期临时扩容
    pool_recycle=1800,     # 30 分钟回收，避免被云 DB 代理断开
    pool_pre_ping=True,    # 探活
    pool_timeout=10,       # 等待连接超时，快速失败而非堆积
)
```

#### 总结

- N+1 根因是默认 `lazy="select"` 的逐对象延迟加载；遍历集合访问关系属性时必发 N+1。
- **首选 `selectinload`** 解决集合 N+1（2 次查询）；多对一用 `joinedload`（1 次查询）。
- 生产环境推荐 `lazy="raise"` 模型默认 + 查询时显式 `selectinload`，从源头杜绝 N+1。
- 批量插入用 `session.execute(insert(Model), [dict, ...])`（2.0 风格）或 `bulk_insert_mappings`，比逐个 `add` 快 10-100 倍。
- `load_only` 裁剪列，避免加载大字段；连接池根据 DB 的 `max_connections` 和应用并发量调优。
- 常见坑：`echo=True` 看日志是识别 N+1 的最快方法；`bulk_*` 跳过工作单元，不触发 `default`/`server_default`/事件监听，慎用。

---

## 第6章 高级特性

本章覆盖 SQLAlchemy 的高级能力：事件系统（监听 INSERT/UPDATE/DELETE 等生命周期事件）、混合属性（`hybrid_property` 让同一属性在 Python 和 SQL 两种语境下工作）、自定义类型（`TypeDecorator`）、继承映射（单表/连接表/具体表三种继承策略）。这些特性让你能处理审计日志、计算字段、特殊数据格式、多态模型等复杂需求。

---

### 第20讲 事件系统与监听器

#### 概念

SQLAlchemy 的事件系统允许你在 ORM/Session/Engine 的生命周期节点上挂载自定义回调，实现"在某事件发生时自动执行某逻辑"。常见用途：审计日志（记录每次修改）、自动填充字段（`created_at`/`updated_at`）、数据校验、缓存失效、软删除等。事件系统是"横切关注点"（cross-cutting concern）的实现手段，让你在不侵入业务代码的前提下统一处理这些逻辑。

#### 原理

**事件的分类与注册方式**：

| 事件目标 | 模块 | 常见事件 |
|---------|------|---------|
| ORM 模型 | `sqlalchemy.orm.event` | `before_insert`/`after_insert`/`before_update`/`after_update`/`before_delete` |
| Session | `sqlalchemy.orm.event` | `before_commit`/`after_commit`/`after_flush`/`after_rollback` |
| Engine/Pool | `sqlalchemy.event` | `connect`/`checkout`/`checkin`/`first_connect` |
| 属性变更 | `sqlalchemy.orm.event` | `set`/`append`/`remove`（关系属性变更） |

**注册方式**：用 `event.listen(target, event_name, callback)` 或装饰器 `@event.listens_for(target, event_name)`。

**`before_insert` / `before_update` 的用途**：在对象写入数据库前修改其属性，如自动填充 `updated_at`、计算派生字段。回调签名 `(mapper, connection, target)`，`target` 是被操作的模型实例。

**`after_flush` 的用途**：在 flush 后、commit 前访问所有待写对象，常用于"根据本次变更触发其他操作"（如更新缓存、发通知）。

**`before_commit` vs `after_commit`**：

- `before_commit`：事务提交前，此时仍可发 SQL（在同一事务内）。
- `after_commit`：事务已提交，不能再发 SQL（连接已归还），适合发消息/通知等副作用。

#### 例子

**自动填充时间戳**：

```python
from sqlalchemy import event
from datetime import datetime

@event.listens_for(User, "before_insert")
def set_created_at(mapper, connection, target):
    target.created_at = datetime.now()

@event.listens_for(User, "before_update")
def set_updated_at(mapper, connection, target):
    target.updated_at = datetime.now()
```

**审计日志（记录每次修改）**：

```python
class AuditLog(Base):
    __tablename__ = "audit_log"
    id: Mapped[int] = mapped_column(primary_key=True)
    table_name: Mapped[str]
    record_id: Mapped[int]
    action: Mapped[str]  # INSERT/UPDATE/DELETE
    changes: Mapped[Optional[str]]  # JSON 字符串
    created_at: Mapped[datetime]

@event.listens_for(User, "after_update")
def log_user_update(mapper, connection, target):
    # 获取变更的列
    state = inspect(target)
    changes = {k: getattr(target, k) for k in state.unloaded}
    # 在同一事务内插入审计记录
    connection.execute(
        AuditLog.__table__.insert().values(
            table_name="users",
            record_id=target.id,
            action="UPDATE",
            changes=str(changes),
            created_at=datetime.now(),
        )
    )
```

**Session 级事件（提交后发通知）**：

```python
from sqlalchemy.orm import sessionmaker, Session

@event.listens_for(Session, "after_commit")
def notify_changes(session):
    # 遍历本次提交涉及的所有对象
    for obj in session:
        if isinstance(obj, User):
            print(f"User {obj.id} 已提交，发缓存失效通知")
            # cache.invalidate(f"user:{obj.id}")
```

**连接池事件（连接时设时区）**：

```python
@event.listens_for(engine, "connect")
def set_timezone(dbapi_conn, conn_record):
    # 每个新连接建立时执行 SQL
    cursor = dbapi_conn.cursor()
    cursor.execute("SET timezone = 'Asia/Shanghai'")
    cursor.close()
```

**属性变更事件**：

```python
@event.listens_for(User.name, "set")
def name_changed(target, value, oldvalue, initiator):
    if oldvalue != value:
        print(f"用户名从 {oldvalue} 改为 {value}")
        # 可用于触发"用户名变更"通知
```

**`remove` 移除监听器**：

```python
# event.listen 返回的函数可用来移除
handler = event.listen(User, "before_insert", set_created_at)
event.remove(User, "before_insert", set_created_at)
```

#### 总结

- 事件系统用 `event.listen(target, event_name, callback)` 或 `@event.listens_for` 注册回调。
- `before_insert/before_update` 用于自动填充字段、计算派生属性；`after_insert` 等用于审计日志。
- `after_commit` 不能再发 SQL（连接已归还），适合发消息/通知等副作用；`before_commit` 仍可发 SQL。
- 连接池 `connect` 事件常用于设置会话变量（时区、隔离级别、搜索路径）。
- 属性 `set` 事件可监听单个属性变更，用于细粒度通知。
- 常见坑：`after_commit` 中访问延迟加载属性报 `DetachedInstanceError`（连接已归还）；事件回调抛异常会中断主流程，务必 try/except。

---

### 第21讲 混合属性与计算列

#### 概念

`hybrid_property` 是 SQLAlchemy 提供的特殊描述符，让同一个属性在两种语境下表现不同：**①** 在 Python 中（`user.full_name`）执行 Python 函数；**②** 在 SQL 查询中（`User.full_name`）生成对应的 SQL 表达式。这种"双语境"能力让你能定义计算属性（如 `full_name = first_name + last_name`），既能像普通属性一样访问，又能在 `WHERE`/`ORDER BY` 中作为查询条件，且查询下推到数据库执行，不加载到内存。

#### 原理

**`hybrid_property` 的两种求值模式**：

```python
from sqlalchemy.ext.hybrid import hybrid_property

class User(Base):
    first_name: Mapped[str]
    last_name: Mapped[str]

    @hybrid_property
    def full_name(self):
        # 实例访问时（Python 语境）：user.full_name
        return f"{self.first_name} {self.last_name}"

    @full_name.expression
    def full_name(cls):
        # 类访问时（SQL 语境）：User.full_name 生成 SQL 表达式
        return cls.first_name + " " + cls.last_name
```

- `user.full_name`：调用 Python 函数，返回字符串。
- `User.full_name`：调用 `.expression` 函数，返回 SQL 表达式（`first_name || ' ' || last_name`）。
- `select(User).where(User.full_name == "Alice Smith")`：SQL 语境，生成 `WHERE first_name || ' ' || last_name = 'Alice Smith'`。

**`hybrid_method`**：类似 `hybrid_property`，但是方法形式，可带参数。

**`column_property`**：另一种计算列，把 SQL 表达式直接映射为列属性，结果在查询时计算并填充到对象。区别：`column_property` 是"查询时计算并缓存"，`hybrid_property` 是"访问时计算"。

#### 例子

**基础 hybrid_property**：

```python
from sqlalchemy.ext.hybrid import hybrid_property
from sqlalchemy import select

class User(Base):
    __tablename__ = "users"
    id: Mapped[int] = mapped_column(primary_key=True)
    first_name: Mapped[str] = mapped_column(String(50))
    last_name: Mapped[str] = mapped_column(String(50))
    age: Mapped[int]

    @hybrid_property
    def full_name(self):
        return f"{self.first_name} {self.last_name}"

    @full_name.expression
    def full_name(cls):
        return cls.first_name.concat(" ").concat(cls.last_name)
        # 或用 + 操作符（部分数据库支持字符串 +）

# Python 语境
user = User(first_name="Alice", last_name="Smith")
print(user.full_name)  # "Alice Smith"

# SQL 语境：查询下推到数据库
stmt = select(User).where(User.full_name == "Alice Smith")
# 生成：WHERE users.first_name || ' ' || users.last_name = 'Alice Smith'
```

**条件型 hybrid（带逻辑）**：

```python
class User(Base):
    # ...
    @hybrid_property
    def is_adult(self):
        return self.age >= 18

    @is_adult.expression
    def is_adult(cls):
        return cls.age >= 18

# 查询所有成年人
stmt = select(User).where(User.is_adult)
# WHERE users.age >= 18
```

**复杂计算 hybrid**：

```python
class Product(Base):
    __tablename__ = "products"
    id: Mapped[int] = mapped_column(primary_key=True)
    price: Mapped[float]
    discount: Mapped[float]  # 0-1 的折扣

    @hybrid_property
    def final_price(self):
        return self.price * (1 - self.discount)

    @final_price.expression
    def final_price(cls):
        return cls.price * (1 - cls.discount)

# 查询折后价低于 100 的商品
stmt = select(Product).where(Product.final_price < 100)
# WHERE products.price * (1 - products.discount) < 100
```

**`hybrid_method`（带参数）**：

```python
from sqlalchemy.ext.hybrid import hybrid_method

class Point(Base):
    x: Mapped[float]
    y: Mapped[float]

    @hybrid_method
    def distance_to(self, x, y):
        return ((self.x - x) ** 2 + (self.y - y) ** 2) ** 0.5

    @distance_to.expression
    def distance_to(cls, x, y):
        return func.sqrt((cls.x - x) ** 2 + (cls.y - y) ** 2)

# 查询距离 (0,0) 小于 5 的点
stmt = select(Point).where(Point.distance_to(0, 0) < 5)
```

**`column_property`（查询时计算列）**：

```python
from sqlalchemy.orm import column_property

class User(Base):
    # ...
    # 把 full_name 作为查询时计算列（每次查询都计算）
    full_name_col = column_property(first_name + " " + last_name)

# 查询时 full_name_col 会出现在 SELECT 列表中
stmt = select(User)
# SELECT users.id, ..., (users.first_name || ' ' || users.last_name) AS full_name_col
```

#### 总结

- `hybrid_property` 让同一属性在 Python（实例访问）和 SQL（类访问/查询条件）两种语境下工作。
- 必须同时定义 `@hybrid_property` 的 Python 函数和 `@xxx.expression` 的 SQL 表达式函数。
- 查询条件下推到数据库执行，不加载到内存——这是 hybrid 的核心价值。
- `hybrid_method` 是带参数的版本，如 `Point.distance_to(x, y)`。
- `column_property` 是另一种计算列，查询时计算并填充到对象，适合"每次查询都要"的计算。
- 常见坑：只定义了 Python 函数没定义 `.expression`，查询时退化为 Python 求值（加载所有行到内存再过滤，性能灾难）；字符串拼接用 `+` 在部分数据库（如 MySQL）报错，应用 `.concat()`。

---

### 第22讲 自定义类型与 TypeDecorator

#### 概念

虽然 SQLAlchemy 内置了丰富的数据类型，但有时你需要自定义类型来处理特殊数据格式：如把 JSON 字段自动序列化/反序列化为 Python 对象、把枚举字符串映射为 Enum 实例、加密存储敏感字段、存储自定义 Python 类。`TypeDecorator` 是 SQLAlchemy 提供的类型装饰器基类，让你在现有类型之上添加 Python 侧的转换逻辑，实现"存入数据库前编码、读取后解码"的透明转换。

#### 原理

**`TypeDecorator` 的工作机制**：

```
Python 对象 ──process_bind_param──► DBAPI 值 ──► 数据库列
数据库列    ──process_result_value──► Python 对象
```

- `process_bind_param(value, dialect)`：Python → DBAPI 方向，把 Python 对象转为数据库能存储的值（如 dict → JSON 字符串）。
- `process_result_value(value, dialect)`：DBAPI → Python 方向，把数据库返回的值转为 Python 对象（如 JSON 字符串 → dict）。
- `impl`：底层使用的现有类型（如 `String`/`JSON`/`LargeBinary`），自定义类型在其之上添加转换。

**`TypeDecorator` vs `TypeEngine`**：

- `TypeEngine`：从零实现一个类型，需处理 SQL DDL 生成、参数绑定、结果解码全部三件事，工作量大。
- `TypeDecorator`：在现有类型之上添加转换，只需实现 `process_bind_param` 和 `process_result_value`，推荐。

**`cache_ok`**：2.0 要求自定义类型声明 `cache_ok = True` 才能被 ORM 的 SQL 编译缓存使用，否则每次编译都重新生成，性能下降。

#### 例子

**自定义 JSON 类型（自动序列化）**：

```python
from sqlalchemy.types import TypeDecorator, String, JSON
import json

class JSONEncoded(TypeDecorator):
    """把 dict/list 自动序列化为 JSON 字符串存储"""
    impl = String
    cache_ok = True  # 2.0 必须声明，否则禁用编译缓存

    def process_bind_param(self, value, dialect):
        if value is not None:
            return json.dumps(value, ensure_ascii=False)
        return None

    def process_result_value(self, value, dialect):
        if value is not None:
            return json.loads(value)
        return None

class User(Base):
    __tablename__ = "users"
    id: Mapped[int] = mapped_column(primary_key=True)
    preferences: Mapped[dict] = mapped_column(JSONEncoded(500))

# 使用：透明序列化
user = User(preferences={"theme": "dark", "lang": "zh"})
session.add(user)
session.commit()
# 存储为：'{"theme": "dark", "lang": "zh"}'

loaded = session.get(User, 1)
print(loaded.preferences)  # {'theme': 'dark', 'lang': 'zh'}（自动反序列化）
print(type(loaded.preferences))  # <class 'dict'>
```

**加密类型（敏感字段加密存储）**：

```python
from cryptography.fernet import Fernet
from sqlalchemy.types import TypeDecorator, String

key = Fernet.generate_key()
cipher = Fernet(key)

class EncryptedString(TypeDecorator):
    impl = String
    cache_ok = True

    def process_bind_param(self, value, dialect):
        if value is not None:
            return cipher.encrypt(value.encode()).decode()
        return None

    def process_result_value(self, value, dialect):
        if value is not None:
            return cipher.decrypt(value.encode()).decode()
        return None

class User(Base):
    ssn: Mapped[str] = mapped_column(EncryptedString(500))  # 社保号加密存储
```

**枚举类型（字符串 ↔ Enum 实例）**：

```python
import enum

class Status(enum.Enum):
    draft = "draft"
    published = "published"

class EnumAsString(TypeDecorator):
    impl = String(20)
    cache_ok = True

    def process_bind_param(self, value, dialect):
        if value is not None:
            return value.value if isinstance(value, Status) else value
        return None

    def process_result_value(self, value, dialect):
        if value is not None:
            return Status(value)
        return None

class Post(Base):
    status: Mapped[Status] = mapped_column(EnumAsString)
```

**继承现有类型添加默认值**：

```python
from sqlalchemy import DateTime

class UTCDateTime(TypeDecorator):
    """所有时间统一存为 UTC"""
    impl = DateTime(timezone=True)
    cache_ok = True

    def process_bind_param(self, value, dialect):
        if value is not None and value.tzinfo is None:
            # 假设 naive datetime 是本地时间，转 UTC
            from datetime import timezone
            value = value.replace(tzinfo=timezone.utc)
        return value
```

#### 总结

- `TypeDecorator` 在现有类型之上添加 Python 侧的编解码逻辑，实现透明序列化/加密/类型映射。
- 必须实现 `process_bind_param`（Python→DB）和 `process_result_value`（DB→Python）两个方法。
- `impl` 指定底层类型（如 `String`/`JSON`/`LargeBinary`），自定义类型继承其 SQL DDL 行为。
- **2.0 必须声明 `cache_ok = True`** 才能启用 SQL 编译缓存，否则每次查询都重新编译，性能下降。
- 典型场景：JSON 序列化、字段加密、枚举映射、时区统一、自定义 Python 类的持久化。
- 常见坑：忘了 `cache_ok = True` 导致性能警告；`process_bind_param` 忘了处理 `None` 导致 NULL 报错；加密类型的密钥管理要安全（用环境变量/KMS）。

---

### 第23讲 继承映射

#### 概念

继承映射解决"面向对象的继承如何在关系型数据库中表达"的问题。一个 Python 类继承体系（如 `Employee` → `Manager`/`Engineer`）需要映射到一张或多张表。SQLAlchemy 提供三种继承策略：**单表继承**（所有子类共用一张表，用 discriminator 列区分）、**连接表继承**（父类一张表，每个子类一张表，用外键关联）、**具体表继承**（每个子类一张完整表，含父类字段）。选择哪种策略取决于查询模式、数据量、唯一约束需求。

#### 原理

**三种策略对比**：

| 策略 | 表结构 | 优点 | 缺点 | 适用 |
|------|--------|------|------|------|
| 单表继承（joined-table 无，单表） | 一张表 + discriminator 列 | 简单、查询快、无 JOIN | 子类独有列必须可空、无法加 NOT NULL 约束 | 子类差异小、字段少 |
| 连接表继承 | 父表 + 各子类表（外键关联） | 字段约束清晰、无冗余 | 查询需 JOIN、写入需多表 | 子类差异大、字段多 |
| 具体表继承 | 每个子类一张完整表 | 查询单表无 JOIN | 父类字段冗余、父类查询需 UNION | 子类几乎独立 |

**单表继承（Single Table Inheritance）**：

- 所有子类共用父类的表，加一个 `type` 列（discriminator）区分子类类型。
- 子类独有的列在父表中定义，但必须允许 NULL（因为其他子类不用）。
- `__mapper_args__ = {"polymorphic_identity": "manager", "polymorphic_on": type_col}`。

**连接表继承（Joined Table Inheritance）**：

- 父类一张表存公共字段，每个子类一张表存独有字段，子类表的主键同时是外键（指向父表）。
- 查询父类时 LEFT JOIN 所有子类表；查询子类时 JOIN 父表。
- `__mapper_args__ = {"polymorphic_identity": "manager", "polymorphic_load": "inline"}`。

**具体表继承（Concrete Table Inheritance）**：

- 每个子类一张完整表（含父类字段），无外键关联。
- 父类查询用 UNION ALL 合并所有子类表。
- 配置较复杂，用 `__mapper_args__ = {"concrete": True, "polymorphic_identity": ...}`。

#### 例子

**单表继承**：

```python
from sqlalchemy import String, Column
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column

class Base(DeclarativeBase):
    pass

class Employee(Base):
    __tablename__ = "employees"
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(50))
    type: Mapped[str] = mapped_column(String(20))  # discriminator

    __mapper_args__ = {
        "polymorphic_on": type,
        "polymorphic_identity": "employee",
    }

class Manager(Employee):
    manager_salary: Mapped[Optional[int]]  # 子类独有，必须可空

    __mapper_args__ = {"polymorphic_identity": "manager"}

class Engineer(Employee):
    engineer_skill: Mapped[Optional[str]]

    __mapper_args__ = {"polymorphic_identity": "engineer"}

# 使用
with SessionLocal() as session:
    session.add(Manager(name="Alice", manager_salary=200000))
    session.add(Engineer(name="Bob", engineer_skill="Python"))
    session.commit()

    # 查询父类，自动返回对应子类实例
    employees = session.scalars(select(Employee)).all()
    for e in employees:
        print(type(e).__name__, e.name)
        # Manager Alice
        # Engineer Bob
```

**连接表继承**：

```python
class Employee(Base):
    __tablename__ = "employees"
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(50))
    type: Mapped[str] = mapped_column(String(20))

    __mapper_args__ = {
        "polymorphic_on": type,
        "polymorphic_identity": "employee",
    }

class Manager(Employee):
    __tablename__ = "managers"
    id: Mapped[int] = mapped_column(ForeignKey("employees.id"), primary_key=True)
    manager_salary: Mapped[int]  # 可以 NOT NULL（独立表）

    __mapper_args__ = {"polymorphic_identity": "manager"}

class Engineer(Employee):
    __tablename__ = "engineers"
    id: Mapped[int] = mapped_column(ForeignKey("employees.id"), primary_key=True)
    engineer_skill: Mapped[str]

    __mapper_args__ = {"polymorphic_identity": "engineer"}

# 查询父类自动 LEFT JOIN 所有子类表
stmt = select(Employee)
# SELECT employees.*, managers.*, engineers.* FROM employees
#   LEFT JOIN managers ON ... LEFT JOIN engineers ON ...
```

**具体表继承**：

```python
class Employee(Base):
    __abstract__ = True  # 抽象基类，不建表
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(50))

    __mapper_args__ = {
        "polymorphic_identity": "employee",
        "concrete": True,
    }

class Manager(Employee):
    __tablename__ = "managers_concrete"
    manager_salary: Mapped[int]

    __mapper_args__ = {"polymorphic_identity": "manager", "concrete": True}

class Engineer(Employee):
    __tablename__ = "engineers_concrete"
    engineer_skill: Mapped[str]

    __mapper_args__ = {"polymorphic_identity": "engineer", "concrete": True}

# 父类查询用 UNION ALL
# SELECT id, name FROM managers_concrete
# UNION ALL
# SELECT id, name FROM engineers_concrete
```

#### 总结

- 三种继承策略：单表（共用一表+discriminator）、连接表（父表+子表 JOIN）、具体表（各子类独立完整表）。
- **单表继承**最简单，但子类独有列必须可空，无法加 NOT NULL 约束——适合子类差异小的场景。
- **连接表继承**字段约束清晰，但查询需 JOIN——适合子类差异大、字段多的场景（最常用）。
- **具体表继承**无 JOIN 但父类字段冗余、父类查询需 UNION——适合子类几乎独立的场景。
- `polymorphic_on` 指定 discriminator 列，`polymorphic_identity` 指定该子类的标识值。
- 常见坑：单表继承子类列忘了设 `Optional`（可空）；连接表继承子类表的主键忘了同时是外键；具体表继承忘了 `concrete=True`。

---

## 第7章 实战与工程化

本章把前面学到的知识落地到真实工程：如何与 FastAPI/Flask Web 框架集成（依赖注入管理 Session 生命周期）、如何使用异步 SQLAlchemy（`AsyncSession` + `asyncpg`/`aiomysql`）、如何用 Alembic 做数据库迁移。学完本章，你能在生产项目中规范地使用 SQLAlchemy，处理高并发、异步、schema 演进等工程挑战。

---

### 第24讲 与 FastAPI/Flask 集成

#### 概念

在 Web 应用中，SQLAlchemy 的 Session 生命周期必须与请求绑定：每个请求开始时创建 Session，请求结束时提交或回滚并关闭。FastAPI 用依赖注入（`Depends`）优雅地管理这个生命周期；Flask 用 `g` 对象 + `teardown_appcontext` 钩子。本讲讲解两种框架的标准集成模式，包括 Session 工厂创建、依赖注入函数、错误回滚、测试隔离等工程要点。

#### 原理

**Web 集成的核心原则**：

1. **每请求一 Session**：Session 不是线程安全的，多请求复用同一 Session 会导致脏数据。每个请求创建独立 Session。
2. **请求结束关闭 Session**：无论成功或异常，都要关闭 Session 归还连接，否则连接池耗尽。
3. **异常自动回滚**：请求处理中抛异常时，Session 应自动 `rollback()` 而非 `commit()`。
4. **依赖注入暴露 Session**：FastAPI 用 `Depends(get_db)`，Flask 用 `g.session`。

**FastAPI 的依赖注入模式**：

```python
# 依赖函数：yield 风格，FastAPI 会在请求结束后执行 finally 块
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

# 路由用 Depends 注入
@app.get("/users/{id}")
def read_user(id: int, db: Session = Depends(get_db)):
    return db.get(User, id)
```

`yield` 依赖是 FastAPI 的关键特性：`yield` 之前的代码在请求开始时执行（创建 Session），`yield` 返回的值注入路由函数，`yield` 之后的代码在请求结束后执行（关闭 Session）。

**Flask 的 `g` + `teardown` 模式**：

```python
from flask import g

@app.before_request
def before():
    g.db = SessionLocal()

@app.teardown_appcontext
def teardown(exc):
    if exc is not None:
        g.db.rollback()
    g.db.close()
```

**测试隔离**：测试时用独立的内存 SQLite + 覆盖 `get_db` 依赖，每个测试函数独立事务并在结束时回滚，保证测试间数据隔离。

#### 例子

**FastAPI 完整集成**：

```python
# database.py
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, DeclarativeBase

DATABASE_URL = "postgresql+psycopg2://user:pass@localhost/mydb"

engine = create_engine(
    DATABASE_URL,
    pool_size=20,
    max_overflow=10,
    pool_pre_ping=True,
    pool_recycle=1800,
)
SessionLocal = sessionmaker(engine, expire_on_commit=False)

class Base(DeclarativeBase):
    pass

# models.py
class User(Base):
    __tablename__ = "users"
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(50))
    email: Mapped[str] = mapped_column(String(120), unique=True)

# deps.py
from typing import Generator
from sqlalchemy.orm import Session

def get_db() -> Generator[Session, None, None]:
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

# main.py
from fastapi import FastAPI, Depends, HTTPException
from pydantic import BaseModel

app = FastAPI()

class UserCreate(BaseModel):
    name: str
    email: str

class UserOut(BaseModel):
    id: int
    name: str
    email: str

    class Config:
        from_attributes = True  # 从 ORM 实例读取

@app.post("/users", response_model=UserOut)
def create_user(data: UserCreate, db: Session = Depends(get_db)):
    user = User(**data.model_dump())
    db.add(user)
    try:
        db.commit()
    except IntegrityError:
        db.rollback()
        raise HTTPException(400, "Email already exists")
    db.refresh(user)
    return user

@app.get("/users/{id}", response_model=UserOut)
def get_user(id: int, db: Session = Depends(get_db)):
    user = db.get(User, id)
    if not user:
        raise HTTPException(404, "User not found")
    return user

@app.get("/users", response_model=list[UserOut])
def list_users(skip: int = 0, limit: int = 100, db: Session = Depends(get_db)):
    stmt = select(User).offset(skip).limit(limit)
    return db.scalars(stmt).all()
```

**Flask 集成**：

```python
from flask import Flask, g, jsonify, request
from sqlalchemy import select

app = Flask(__name__)

@app.before_request
def before():
    g.db = SessionLocal()

@app.teardown_appcontext
def teardown(exc):
    db = g.pop("db", None)
    if db is not None:
        if exc is not None:
            db.rollback()
        db.close()

@app.get("/users/<int:id>")
def get_user(id):
    user = g.db.get(User, id)
    if not user:
        return jsonify({"error": "not found"}), 404
    return jsonify({"id": user.id, "name": user.name, "email": user.email})

@app.post("/users")
def create_user():
    data = request.json
    user = User(**data)
    g.db.add(user)
    try:
        g.db.commit()
    except IntegrityError:
        g.db.rollback()
        return jsonify({"error": "email exists"}), 400
    return jsonify({"id": user.id}), 201
```

**测试隔离（FastAPI）**：

```python
import pytest
from fastapi.testclient import TestClient
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

@pytest.fixture
def client():
    # 内存 SQLite，每个测试独立
    test_engine = create_engine("sqlite:///:memory:")
    Base.metadata.create_all(test_engine)
    TestSession = sessionmaker(test_engine)

    def override_get_db():
        db = TestSession()
        try:
            yield db
        finally:
            db.close()

    app.dependency_overrides[get_db] = override_get_db
    yield TestClient(app)
    app.dependency_overrides.clear()

def test_create_user(client):
    resp = client.post("/users", json={"name": "Alice", "email": "a@x.com"})
    assert resp.status_code == 200
    assert resp.json()["name"] == "Alice"
```

#### 总结

- Web 集成核心：每请求一 Session，请求结束关闭，异常自动回滚。
- FastAPI 用 `yield` 依赖注入（`Depends(get_db)`），优雅管理 Session 生命周期。
- Flask 用 `g.db` + `teardown_appcontext`，异常时回滚。
- `expire_on_commit=False` 在 Web 场景常用：commit 后对象属性不过期，避免序列化时触发额外查询。
- 测试用内存 SQLite + 覆盖 `get_db` 依赖，保证测试隔离。
- 常见坑：忘了 `teardown`/`finally` 导致连接泄漏；`commit` 后访问延迟加载属性报 `DetachedInstanceError`（Session 已关闭）——用 `expire_on_commit=False` 或在关闭前完成序列化。

---

### 第25讲 异步 SQLAlchemy

#### 概念

异步 SQLAlchemy（`AsyncSession` + `create_async_engine`）是 2.0 的重要特性，能与 FastAPI/Starlette 的异步路由配合，避免数据库 I/O 阻塞事件循环。异步 API 与同步 API 几乎对称，但底层用异步驱动（`asyncpg` for PG、`aiomysql` for MySQL、`aiosqlite` for SQLite）。本讲讲解异步引擎创建、`AsyncSession` 用法、异步查询、与 FastAPI 异步路由集成，以及同步/异步混用的注意事项。

#### 原理

**异步架构**：

```
AsyncSession ──► AsyncEngine ──► AsyncAdaptedQueuePool ──► asyncpg/aiomysql
     │
     └─ await session.execute(select(...))
              │
              └─ await conn.execute(...)  # 真正的异步 I/O
```

**关键差异（同步 vs 异步）**：

| 同步 | 异步 |
|------|------|
| `create_engine` | `create_async_engine` |
| `Session` | `AsyncSession` |
| `sessionmaker` | `async_sessionmaker`（2.0 新增） |
| `session.execute(stmt)` | `await session.execute(stmt)` |
| `session.scalars(stmt).all()` | `(await session.scalars(stmt)).all()` |
| `session.commit()` | `await session.commit()` |
| `session.get(Model, id)` | `await session.get(Model, id)` |

**异步驱动的连接字符串**：

- PostgreSQL：`postgresql+asyncpg://user:pass@host/db`
- MySQL：`mysql+aiomysql://user:pass@host/db`
- SQLite：`sqlite+aiosqlite:///./db.sqlite`

**`AsyncSession` 的限制**：

- 不支持同步的 `session.query(...)` 旧 API（必须用 `select()`）。
- 延迟加载（lazy）在异步中不可用——因为访问属性时无法 `await`。必须用 `selectinload`/`joinedload` 显式预加载，或设 `lazy="raise"`。
- `expire_on_commit=True` 在异步中会导致访问属性时触发同步查询（报错），因此异步场景**必须设 `expire_on_commit=False`**。

#### 例子

**异步引擎与 Session**：

```python
from sqlalchemy.ext.asyncio import (
    create_async_engine, async_sessionmaker, AsyncSession
)

DATABASE_URL = "postgresql+asyncpg://user:pass@localhost/mydb"

async_engine = create_async_engine(
    DATABASE_URL,
    pool_size=20,
    max_overflow=10,
    pool_recycle=1800,
)

AsyncSessionLocal = async_sessionmaker(
    async_engine,
    expire_on_commit=False,  # 异步必须 False！
    class_=AsyncSession,
)
```

**异步 CRUD**：

```python
from sqlalchemy import select

async def create_user(name: str, email: str):
    async with AsyncSessionLocal() as session:
        user = User(name=name, email=email)
        session.add(user)
        await session.commit()
        return user

async def get_user(user_id: int):
    async with AsyncSessionLocal() as session:
        return await session.get(User, user_id)

async def list_users():
    async with AsyncSessionLocal() as session:
        stmt = select(User).order_by(User.id)
        result = await session.scalars(stmt)
        return result.all()

async def update_user(user_id: int, name: str):
    async with AsyncSessionLocal() as session:
        user = await session.get(User, user_id)
        user.name = name
        await session.commit()
```

**异步预加载（避免 lazy）**：

```python
from sqlalchemy.orm import selectinload

async def get_users_with_posts():
    async with AsyncSessionLocal() as session:
        stmt = select(User).options(selectinload(User.posts))
        result = await session.scalars(stmt)
        users = result.all()
        # 安全：posts 已预加载，访问不触发同步查询
        for u in users:
            print(u.name, len(u.posts))
```

**与 FastAPI 异步路由集成**：

```python
from fastapi import FastAPI, Depends, HTTPException

app = FastAPI()

async def get_db() -> AsyncSession:
    async with AsyncSessionLocal() as session:
        yield session

@app.get("/users/{id}")
async def get_user(id: int, db: AsyncSession = Depends(get_db)):
    user = await db.get(User, id)
    if not user:
        raise HTTPException(404, "Not found")
    return user

@app.post("/users")
async def create_user(data: UserCreate, db: AsyncSession = Depends(get_db)):
    user = User(**data.model_dump())
    db.add(user)
    try:
        await db.commit()
    except IntegrityError:
        await db.rollback()
        raise HTTPException(400, "Email exists")
    await db.refresh(user)
    return user
```

**建表（异步）**：

```python
async def init_db():
    async with async_engine.begin() as conn:
        await conn.run_sync(Base.metadata.create_all)

# 启动时调用
import asyncio
asyncio.run(init_db())
```

#### 总结

- 异步用 `create_async_engine` + `async_sessionmaker` + `AsyncSession`，API 与同步对称但需 `await`。
- 连接字符串用异步驱动：`asyncpg`（PG）、`aiomysql`（MySQL）、`aiosqlite`（SQLite）。
- **异步必须设 `expire_on_commit=False`**，否则 commit 后访问属性触发同步查询报错。
- 异步不支持延迟加载（lazy），必须用 `selectinload`/`joinedload` 显式预加载——建议模型默认 `lazy="raise"`。
- `run_sync` 桥接同步函数（如 `Base.metadata.create_all`）到异步上下文。
- 常见坑：异步路由里用同步 `Session` 阻塞事件循环；忘了 `await session.commit()`；延迟加载在异步中报 `MissingGreenlet` 错误。

---

### 第26讲 与 Alembic 数据库迁移

#### 概念

`create_all` 只能建表，不能改表（加列、改类型、加索引），生产环境的 schema 演进需要数据库迁移工具。Alembic 是 SQLAlchemy 官方的迁移工具，能自动检测模型变更生成迁移脚本，并按版本号顺序执行。本讲讲解 Alembic 的初始化、自动生成迁移、手动编辑迁移、升级/回滚、与 FastAPI 应用的集成工作流。

#### 原理

**Alembic 的工作机制**：

1. **初始化**：`alembic init alembic` 创建迁移目录和配置。
2. **配置**：`alembic.ini` 设数据库连接，`env.py` 引入你的 `Base.metadata` 作为自动检测的基准。
3. **自动生成**：`alembic revision --autogenerate -m "add phone column"` 对比模型与数据库当前状态，生成迁移脚本。
4. **审查**：自动生成的脚本需人工审查——Alembic 不能检测所有变更（如列重命名、数据迁移），需手动补充。
5. **执行**：`alembic upgrade head` 按版本号顺序执行迁移；`alembic downgrade -1` 回滚一步。

**迁移脚本的结构**：

```python
def upgrade():
    op.add_column("users", sa.Column("phone", sa.String(20)))

def downgrade():
    op.drop_column("users", "phone")
```

每个迁移有 `upgrade`（升级）和 `downgrade`（回滚）两个函数，必须对称——能升级就能回滚。

**`autogenerate` 的能力与局限**：

- 能检测：加/删表、加/删列、改列类型、加/删索引、加/删外键。
- 不能检测：列重命名（会被识别为删旧列+加新列，丢数据）、数据迁移、Check 约束变更、部分方言特有特性。

因此自动生成后**必须人工审查**，尤其是列重命名和数据迁移场景。

#### 例子

**初始化 Alembic**：

```bash
pip install alembic
cd /my/project
alembic init alembic
```

生成的目录结构：

```
my/project/
├── alembic.ini          # 配置（数据库连接等）
├── alembic/
│   ├── env.py           # 迁移环境（需改这里引入 Base.metadata）
│   ├── script.py.mako   # 迁移脚本模板
│   └── versions/        # 迁移脚本存放目录
└── models.py            # 你的模型定义
```

**配置 `env.py`**：

```python
# alembic/env.py 关键修改
from myapp.models import Base  # 引入你的 Base
target_metadata = Base.metadata

# 把 sqlalchemy.url 改为从环境变量读取
import os
config.set_main_option("sqlalchemy.url", os.getenv("DATABASE_URL"))
```

**自动生成迁移**：

```bash
# 修改模型后，生成迁移
alembic revision --autogenerate -m "add phone column to users"

# 生成的文件：alembic/versions/a1b2c3_add_phone_column.py
```

**审查并编辑迁移脚本**：

```python
"""add phone column to users

Revision ID: a1b2c3
Revises: 9z8y7x
Create Date: 2026-07-07
"""
from alembic import op
import sqlalchemy as sa

def upgrade():
    # 自动生成的内容（需审查）
    op.add_column("users", sa.Column("phone", sa.String(20), nullable=True))

    # 手动补充：数据迁移（把旧 phone_old 列的数据复制到 phone）
    # op.execute("UPDATE users SET phone = phone_old")

def downgrade():
    op.drop_column("users", "phone")
```

**执行迁移**：

```bash
# 升级到最新
alembic upgrade head

# 升级到指定版本
alembic upgrade a1b2c3

# 回滚一步
alembic downgrade -1

# 回滚到指定版本
alembic downgrade 9z8y7x

# 查看当前版本
alembic current

# 查看历史
alembic history
```

**列重命名的正确处理**：

```python
# autogenerate 会错误地识别为"删旧列+加新列"，需手动改为 rename
def upgrade():
    # 错误（autogenerate 默认）：
    # op.drop_column("users", "mobile")
    # op.add_column("users", sa.Column("phone", sa.String(20)))

    # 正确（手动改）：
    op.alter_column("users", "mobile", new_column_name="phone")
```

**数据迁移**：

```python
def upgrade():
    # 加新列
    op.add_column("users", sa.Column("full_name", sa.String(100)))
    # 用 first+last 填充
    op.execute("UPDATE users SET full_name = first_name || ' ' || last_name")
    # 删旧列
    op.drop_column("users", "first_name")
    op.drop_column("users", "last_name")
```

**与 FastAPI 应用的集成工作流**：

```python
# 启动时确保迁移到最新（开发环境用，生产用 CLI）
import asyncio
from alembic.config import Config
from alembic import command

def run_migrations():
    alembic_cfg = Config("alembic.ini")
    command.upgrade(alembic_cfg, "head")

# 典型工作流：
# 1. 修改 models.py
# 2. alembic revision --autogenerate -m "description"
# 3. 审查 versions/xxx.py
# 4. alembic upgrade head
# 5. 提交代码（含迁移脚本）
```

**空迁移（只标记版本）**：

```bash
# 有时只想创建一个迁移点但不改 schema
alembic revision -m "empty migration for tag"
```

#### 总结

- Alembic 是 SQLAlchemy 官方迁移工具，用 `alembic init` 初始化，`env.py` 引入 `Base.metadata`。
- `alembic revision --autogenerate -m "..."` 自动生成迁移，但**必须人工审查**——autogenerate 不能检测列重命名和数据迁移。
- `upgrade head` 升级到最新，`downgrade -1` 回滚一步；每个迁移的 upgrade/downgrade 必须对称。
- 列重命名用 `op.alter_column(..., new_column_name=...)`，不要让 autogenerate 误删列。
- 数据迁移用 `op.execute("UPDATE ...")` 在 schema 变更间穿插数据操作。
- 常见坑：autogenerate 后不审查直接 upgrade 导致丢数据；生产环境迁移前不备份；多人并行开发时迁移版本冲突——需及时合并和 rebase。

---

## 课程总结

恭喜你学完了全部 26 讲！让我们回顾整个学习路径：

1. **第1章 入门基础**：理解了 SQLAlchemy 的 Core/ORM 双层架构，掌握了 Engine 与连接池。
2. **第2章 Core**：用 `Table`/`Column`/`Metadata` 定义 schema，用 `select/insert/update/delete` 构造 SQL 表达式。
3. **第3章 ORM 基础**：用 `DeclarativeBase`/`Mapped`/`mapped_column` 定义模型，理解 Session 的工作单元模式，掌握 ORM CRUD。
4. **第4章 关系映射**：掌握一对多、多对多、自引用关系，理解 `back_populates` 和加载策略。
5. **第5章 进阶查询与性能**：能写任意复杂的 JOIN/子查询/窗口函数，能识别和解决 N+1 问题。
6. **第6章 高级特性**：用事件系统、`hybrid_property`、`TypeDecorator`、继承映射处理复杂业务需求。
7. **第7章 实战工程化**：与 FastAPI 集成、异步 SQLAlchemy、Alembic 迁移，具备生产落地能力。

**进阶学习建议**：

- 深入阅读 [SQLAlchemy 官方文档](https://docs.sqlalchemy.org/) 的 ORM Tutorial 和 Core Tutorial。
- 研究 [FastAPI 官方 SQL 示例](https://fastapi.tiangolo.com/tutorial/sql-databases/) 的完整工程实践。
- 关注 SQLAlchemy 2.x 的 [迁移指南](https://docs.sqlalchemy.org/en/20/changelog/migration_20.html)，了解从 1.x 升级的要点。
- 在真实项目中实践：从模型设计开始，逐步加入关系、查询优化、迁移管理，形成完整的工程闭环。

SQLAlchemy 的学习曲线确实陡峭，但一旦掌握，它将成为你 Python 后端工具箱中最强大的数据库利器。祝你在数据持久化的道路上越走越远！
