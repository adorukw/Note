# SQLite 系统教程

> 一门从基础到实战的系统化 SQLite 课程，共 15 讲，5 章。
> 每讲包含：概念、原理、例子、总结四个部分。

---

## 课程总览

- **预计讲数**：15 讲（5 章）
- **学习目标**：从 SQLite 基本概念出发，系统掌握其独特的动态类型系统、SQL 语法、核心机制、进阶特性及实战应用，具备在移动端、嵌入式、桌面应用、数据分析等场景中熟练使用 SQLite 的能力。
- **适用人群**：移动端开发者、桌面应用开发者、数据分析师、嵌入式工程师、数据库初学者。
- **学习路径**：基础认知 → SQL 实操 → 核心机制 → 进阶特性 → 高级实战。

---

## 详细章节目录

### 第 1 章 SQLite 基础（3 讲）
- 第 1 讲：SQLite 概述与核心特点
- 第 2 讲：安装与环境配置
- 第 3 讲：命令行工具与基本操作

### 第 2 章 数据定义与操作（4 讲）
- 第 4 讲：创建数据库与表（DDL）
- 第 5 讲：数据插入、更新与删除（DML）
- 第 6 讲：基础查询
- 第 7 讲：高级查询——连接、子查询与聚合

### 第 3 章 SQLite 核心机制（3 讲）
- 第 8 讲：动态类型系统
- 第 9 讲：约束与数据完整性
- 第 10 讲：索引与查询计划

### 第 4 章 进阶特性（3 讲）
- 第 11 讲：事务与并发控制
- 第 12 讲：视图、触发器与 CTE
- 第 13 讲：内置函数与表达式

### 第 5 章 高级应用与实战（2 讲）
- 第 14 讲：性能调优与最佳实践
- 第 15 讲：应用集成与实战案例

---

# 第 1 章 SQLite 基础

SQLite 是世界上部署最广泛的数据库引擎，存在于每一部手机、每一个浏览器、无数嵌入式设备中。本章带您认识 SQLite 的设计哲学、掌握安装配置和基本操作，为后续学习打下基础。

---

## 第 1 讲：SQLite 概述与核心特点

### 概念

**SQLite** 是一个嵌入式（Embedded）SQL 数据库引擎，由 D. Richard Hipp 于 2000 年开发。与 MySQL、PostgreSQL、Oracle 等客户端/服务器架构的数据库不同，SQLite 不需要独立的服务器进程，整个数据库引擎被嵌入到应用程序中，以库（library）的形式提供。

SQLite 的核心特征：

- **零配置（Zero-configuration）**：无需安装、无需配置、无需管理员，开箱即用。
- **无服务器（Serverless）**：数据库引擎与应用程序运行在同一进程中，没有独立的服务器进程。
- **单文件存储（Single-file）**：一个完整的数据库就是一个跨平台文件，便于备份和迁移。
- **跨平台（Cross-platform）**：数据库文件在 32 位和 64 位、大端和小端、不同操作系统间二进制兼容。
- **公有领域（Public Domain）**：SQLite 没有版权限制，可免费用于任何目的，包括商业产品。

SQLite 的设计目标是"本地存储"而非"企业级数据库"，它强调简单、可靠、便携，而非高并发和大规模。

### 原理

**1. 嵌入式架构 vs 客户端/服务器架构**

传统数据库（如 MySQL）采用客户端/服务器（C/S）架构：应用程序通过 TCP/IP 或本地套接字与独立的数据库服务器进程通信，服务器负责解析 SQL、管理存储、处理并发。

SQLite 采用嵌入式架构：数据库引擎以 C 库形式链接到应用程序中，应用程序直接调用 API 执行 SQL，没有进程间通信开销。这种架构的优势是简单、快速、低延迟；劣势是难以支持多进程高并发写入。

```
传统C/S架构：
[应用] → 网络/IPC → [数据库服务器进程] → [磁盘]
         通信开销

SQLite嵌入式架构：
[应用 + SQLite库] → [磁盘]
   直接调用，无通信开销
```

**2. 单文件存储格式**

SQLite 数据库存储在单个文件中（默认扩展名 `.db` 或 `.sqlite`），文件格式稳定且向后兼容。文件内部按页（Page）组织，默认页大小 4096 字节。文件结构包括：

- **文件头**：前 100 字节，包含魔数、格式版本、页大小等元信息。
- **B-tree 页**：存储表数据和索引，采用 B+ 树变体。
- **空闲页**：已删除数据释放的页，可被重用。
- **指针页**：大型 B-tree 的内部节点。

**3. ACID 事务**

尽管轻量，SQLite 仍提供完整的 ACID 事务支持。原子性和持久性通过预写日志（WAL）或回滚日志（Rollback Journal）实现。SQLite 的事务在进程崩溃、操作系统崩溃甚至断电时仍能保证数据完整性，这是其可靠性的核心。

**4. 适用与不适用场景**

SQLite 官方文档明确指出了适用和不适用场景：

**适用场景**：
- 嵌入式设备与物联网
- 桌面应用的本地存储（如浏览器、邮件客户端）
- 移动端应用（iOS、Android 默认数据库）
- 网站的中低流量数据存储
- 数据分析与文件格式替代（如 CAD 文件）
- 测试与原型开发

**不适用场景**：
- 高并发写入（SQLite 写入需独占锁）
- 客户端/服务器架构需求（需远程访问）
- 海量数据（TB 级，虽然 SQLite 支持但性能受限）
- 需要细粒度用户权限控制的场景

### 例子

**1. SQLite 在现实中的广泛应用**

| 应用场景 | 具体实例 |
|---------|---------|
| 移动端 | iOS 的 Core Data、Android 的数据库默认引擎 |
| 浏览器 | Chrome、Firefox 的书签、历史记录、Cookie 存储 |
| 桌面应用 | Skype、Dropbox、Adobe Lightroom 的本地数据 |
| 操作系统 | Windows 10 的部分组件、macOS 的 Spotlight 索引 |
| 编程语言 | Python 标准库内置 `sqlite3` 模块 |
| 数据格式 | 很多应用用 SQLite 作为文件格式（如 .apkg Anki 牌组） |

**2. 与传统数据库的对比**

```python
# Python 中使用 SQLite（无需安装任何东西）
import sqlite3

# 连接数据库（文件不存在会自动创建）
conn = sqlite3.connect('test.db')  # 或 ':memory:' 用内存数据库
cursor = conn.cursor()

# 创建表
cursor.execute('''
    CREATE TABLE users (
        id INTEGER PRIMARY KEY,
        name TEXT NOT NULL,
        email TEXT UNIQUE
    )
''')

# 插入数据
cursor.execute("INSERT INTO users (name, email) VALUES (?, ?)",
               ('张三', 'zhangsan@example.com'))
conn.commit()

# 查询
cursor.execute("SELECT * FROM users")
print(cursor.fetchall())  # [(1, '张三', 'zhangsan@example.com')]

conn.close()
```

对比使用 MySQL 需要安装服务器、配置账户、建立网络连接，SQLite 只需一行 `import sqlite3` 即可开始。

**3. SQLite 的"零管理"特性**

```bash
# 备份SQLite数据库：直接复制文件
cp myapp.db myapp_backup.db

# 迁移到另一台机器：直接传输文件
scp myapp.db user@server:/data/

# 不需要：导出→传输→导入，不需要停服务，不需要DBA
```

### 总结

- SQLite 是嵌入式、无服务器、单文件、零配置的 SQL 数据库引擎，公有领域开源。
- 嵌入式架构省去了进程间通信，简单高效，但难以支持高并发写入。
- 单文件存储格式跨平台二进制兼容，便于备份和迁移。
- 提供完整 ACID 事务，在崩溃和断电时保证数据完整性。
- **关键要点**：SQLite 的设计哲学是"够用就好"，它不追求替代企业级数据库，而是成为本地数据存储的最佳选择。
- **常见误区**：认为 SQLite "不专业"不能用于生产——实际上它被数十亿设备使用，是最可靠的软件之一；误将 SQLite 用于高并发写入场景导致锁冲突。

---

## 第 2 讲：安装与环境配置

### 概念

SQLite 的安装极其简单，这是其"零配置"理念的体现。SQLite 本身是一个 C 库，但通常我们通过以下方式使用：

- **SQLite 命令行工具（sqlite3 CLI）**：交互式 SQL shell，用于管理和操作数据库。
- **SQLite 库**：C/C++ 原生库，或各语言的绑定（Python 内置、Java JDBC、Node.js 等）。
- **GUI 工具**：DB Browser for SQLite、SQLiteStudio、DBeaver 等图形化管理工具。

本讲重点讲解命令行工具的安装和各语言环境的配置，为后续实操做准备。

### 原理

**1. SQLite 的发布形式**

SQLite 官方提供以下发布物：

- **源代码合并文件（amalgamation）**：将整个 SQLite 源码合并为一个 C 文件（`sqlite3.c`，约 15 万行），方便嵌入 C/C++ 项目。
- **预编译二进制**：为各平台编译好的命令行工具和库。
- **Tcl 扩展**：用于 Tcl 语言的绑定。

**2. 命令行工具的作用**

`sqlite3` 命令行工具是一个交互式 SQL shell，功能包括：

- 执行 SQL 语句（DDL、DML、查询）
- 管理数据库（创建、备份、导入导出）
- 查看数据库结构（`.schema`、`.tables`）
- 性能分析（`.timer`、`EXPLAIN`）
- 执行脚本文件（`.read`）

**3. 各语言的 SQLite 支持**

几乎所有编程语言都内置或提供 SQLite 绑定：

| 语言 | 模块/库 | 说明 |
|------|--------|------|
| Python | `sqlite3`（标准库） | 无需安装，开箱即用 |
| C/C++ | `sqlite3.h` + `sqlite3.c` | 官方原生 |
| Java | `sqlite-jdbc` | JDBC 驱动 |
| Node.js | `better-sqlite3` / `sqlite3` | npm 包 |
| Go | `mattn/go-sqlite3` | CGO 绑定 |
| Rust | `rusqlite` | crates.io |
| C# | `System.Data.SQLite` / `Microsoft.Data.Sqlite` | NuGet |

### 例子

**1. 各平台安装命令行工具**

```bash
# Windows：下载预编译二进制
# 从 https://sqlite.org/download.html 下载 sqlite-tools-win32-*.zip
# 解压后将 sqlite3.exe 放入 PATH 目录

# 或使用包管理器
choco install sqlite
# 或
scoop install sqlite

# macOS（系统自带，但版本较旧）
sqlite3 --version
# 安装最新版
brew install sqlite

# Linux
# Ubuntu/Debian
sudo apt-get install sqlite3

# CentOS/RHEL
sudo yum install sqlite

# 验证安装
sqlite3 --version
# 输出类似：3.45.1 2024-01-30 16:01:20 ...
```

**2. 首次使用命令行工具**

```bash
# 创建/打开数据库（文件不存在则创建）
sqlite3 mydb.db

# 进入交互模式，提示符变为 sqlite>
sqlite> .help              # 查看所有命令
sqlite> .tables            # 列出所有表
sqlite> .schema            # 查看所有表的建表语句
sqlite> .databases         # 列出已附加的数据库
sqlite> .mode column       # 设置输出为列对齐模式
sqlite> .headers on        # 显示列名
sqlite> .quit              # 退出

# 直接执行SQL后退出
sqlite3 mydb.db "SELECT * FROM users;"

# 执行SQL文件
sqlite3 mydb.db < script.sql

# 在交互模式中执行脚本
sqlite> .read script.sql
```

**3. Python 环境（无需安装）**

```python
# Python 2.5+ 标准库内置 sqlite3，无需 pip install
import sqlite3
import sqlite3 as sqlite  # 别名

# 检查SQLite版本
print(sqlite3.sqlite_version)   # SQLite库版本，如 '3.45.1'
print(sqlite3.version)          # Python绑定版本，如 '2.6.0'

# 内存数据库（临时，适合测试）
conn = sqlite3.connect(':memory:')

# 文件数据库
conn = sqlite3.connect('example.db')

# 设置自动提交（默认关闭，需手动commit）
conn.isolation_level = None  # 自动提交模式

# 使用上下文管理器自动提交/回滚
with conn:
    conn.execute("INSERT INTO t VALUES (1)")
```

**4. Node.js 环境**

```bash
# 安装 better-sqlite3（同步API，性能好，推荐）
npm install better-sqlite3

# 或安装 sqlite3（异步API，传统选择）
npm install sqlite3
```

```javascript
// better-sqlite3 示例
const Database = require('better-sqlite3');
const db = new Database('example.db');

// 创建表
db.exec(`
    CREATE TABLE IF NOT EXISTS users (
        id INTEGER PRIMARY KEY,
        name TEXT NOT NULL
    )
`);

// 插入（预处理语句）
const insert = db.prepare('INSERT INTO users (name) VALUES (?)');
insert.run('张三');

// 查询
const rows = db.prepare('SELECT * FROM users').all();
console.log(rows);

db.close();
```

**5. 推荐的 .sqliterc 配置**

在用户主目录创建 `~/.sqliterc` 文件，每次启动 sqlite3 CLI 自动执行：

```sql
-- ~/.sqliterc
.mode column
.headers on
.nullvalue NULL
.timer on
.prompt "sqlite> " "   ...> "
```

### 总结

- SQLite 安装极简：命令行工具从官网下载或用包管理器安装，Python 等语言内置支持。
- `sqlite3` 命令行工具是交互式 SQL shell，以 `.` 开头的是元命令（dot command），SQL 语句以分号结尾。
- 各主流语言都有成熟的 SQLite 绑定，Python 标准库内置无需安装。
- **关键要点**：配置 `.sqliterc` 可大幅提升命令行使用体验；Python 的 `sqlite3` 模块是学习和原型开发的最佳起点。
- **常见误区**：在 Windows 上未将 sqlite3.exe 加入 PATH 导致命令找不到；混淆 SQLite 库版本和语言绑定版本。

---

## 第 3 讲：命令行工具与基本操作

### 概念

`sqlite3` 命令行工具是 SQLite 官方提供的交互式管理工具，是学习、调试和管理 SQLite 数据库的核心工具。本讲系统讲解其常用命令和操作技巧。

命令行工具中的命令分两类：

- **元命令（Dot Commands）**：以 `.` 开头，由命令行工具本身处理，不以分号结尾。如 `.tables`、`.schema`、`.mode`。
- **SQL 语句**：标准 SQL，以分号 `;` 结尾，由 SQLite 引擎执行。如 `SELECT * FROM t;`。

### 原理

**1. 元命令的工作机制**

元命令由命令行工具（CLI）解析和处理，不传递给 SQLite 引擎。它们用于控制输出格式、查看元信息、导入导出数据等。元命令不区分大小写，参数以空格分隔。

**2. 输出模式**

`.mode` 命令控制查询结果的输出格式，常用模式：

| 模式 | 说明 | 示例 |
|------|------|------|
| `list` | 默认，管道分隔 | `1|张三|20` |
| `column` | 列对齐 | 对齐的表格 |
| `csv` | 逗号分隔 | `1,张三,20` |
| `json` | JSON 格式 | `[{"id":1,"name":"张三"}]` |
| `html` | HTML 表格 | `<TABLE>...` |
| `insert` | INSERT 语句 | `INSERT INTO t VALUES(...)` |
| `line` | 每行一个字段 | `id = 1` |
| `tabs` | Tab 分隔 | `1\t张三\t20` |

**3. 数据库附加机制**

SQLite 支持同时操作多个数据库文件，通过 `ATTACH DATABASE` 将其他数据库附加到当前连接，用数据库名前缀区分。最多可附加 7 个附加数据库（加上主数据库 `main` 和临时数据库 `temp`，共 9 个）。

### 例子

**1. 创建第一个数据库和表**

```bash
$ sqlite3 school.db
SQLite version 3.45.1 2024-01-30
Enter ".help" for usage hints.
sqlite>
```

```sql
-- 创建学生表
sqlite> CREATE TABLE students (
   ...>     id INTEGER PRIMARY KEY AUTOINCREMENT,
   ...>     name TEXT NOT NULL,
   ...>     age INTEGER,
   ...>     grade REAL
   ...> );

-- 插入数据
sqlite> INSERT INTO students (name, age, grade) VALUES
   ...> ('张三', 20, 85.5),
   ...> ('李四', 21, 92.0),
   ...> ('王五', 19, 78.5);

-- 设置输出格式后查询
sqlite> .mode column
sqlite> .headers on
sqlite> SELECT * FROM students;
id  name  age  grade
--  ----  ---  -----
1   张三   20   85.5
2   李四   21   92.0
3   王五   19   78.5
```

**2. 常用元命令一览**

```sql
-- 查看数据库信息
.databases              -- 列出所有附加的数据库
.tables                 -- 列出所有表
.tables stu%            -- 列出以stu开头的表
.schema                 -- 显示所有表的建表语句
.schema students        -- 显示指定表的建表语句
.indices                -- 列出所有索引
.indices students       -- 列出指定表的索引

-- 查看表结构详情
PRAGMA table_info(students);
-- 输出：cid  name   type     notnull  dflt_value  pk
--       0    id     INTEGER  0        NULL        1
--       1    name   TEXT     1        NULL        0
--       2    age    INTEGER  0        NULL        0
--       3    grade  REAL     0        NULL        0

-- 输出格式控制
.mode column            -- 列对齐
.mode csv               -- CSV格式
.headers on             -- 显示列名
.nullvalue NULL         -- NULL值的显示
.width 5 10 5 8         -- 设置各列宽度

-- 导入导出
.output students.csv    -- 输出重定向到文件
.mode csv
SELECT * FROM students;
.output stdout          -- 恢复输出到屏幕

.import data.csv students  -- 从CSV导入

-- 整库导出为SQL脚本
.dump                   -- 导出整个数据库为SQL语句
.dump students          -- 导出指定表
.output backup.sql
.dump
.output stdout

-- 从SQL脚本恢复
.read backup.sql

-- 执行系统命令
.shell ls -la *.db      -- 执行shell命令
.shell mkdir backup

-- 计时与性能
.timer on               -- 开启SQL执行计时
SELECT COUNT(*) FROM students;
-- 输出：Run Time: real 0.000 user 0.000000 sys 0.000000
```

**3. 附加数据库操作**

```sql
-- 附加另一个数据库
sqlite> ATTACH DATABASE 'archive.db' AS archive;

-- 查看附加的数据库
sqlite> .databases
main: /home/user/school.db
archive: /home/user/archive.db

-- 跨数据库查询（用数据库名前缀）
sqlite> SELECT * FROM main.students
   ...> UNION
   ...> SELECT * FROM archive.students;

-- 跨数据库复制表
sqlite> CREATE TABLE archive.old_students AS
   ...> SELECT * FROM main.students WHERE age > 25;

-- 分离数据库
sqlite> DETACH DATABASE archive;
```

**4. 批处理与脚本化**

```bash
# 方式1：管道输入
echo "SELECT * FROM students;" | sqlite3 school.db

# 方式2：命令行直接执行
sqlite3 school.db "SELECT name, age FROM students WHERE grade > 80;"

# 方式3：执行SQL文件
sqlite3 school.db < query.sql

# 方式4：在脚本中使用.read
# script.sql内容：
# .mode csv
# .headers on
# .output report.csv
# SELECT name, grade FROM students ORDER BY grade DESC;
# .output stdout

sqlite3 school.db < script.sql
# 生成 report.csv 文件
```

**5. SQLite 特有的 PRAGMA 命令**

PRAGMA 是 SQLite 特有的配置命令，用于查询和修改数据库行为：

```sql
-- 查看数据库信息
PRAGMA database_list;        -- 列出所有数据库
PRAGMA page_size;            -- 页大小（默认4096）
PRAGMA page_count;           -- 总页数
PRAGMA integrity_check;      -- 完整性检查

-- 性能相关
PRAGMA journal_mode = WAL;   -- 设置日志模式为WAL
PRAGMA synchronous = NORMAL; -- 同步级别
PRAGMA cache_size = -10000;  -- 缓存大小（负数=KB，正数=页）

-- 查询优化
PRAGMA automatic_index = ON; -- 自动创建索引
PRAGMA query_only = ON;      -- 只读模式

-- 表信息
PRAGMA table_info(students);     -- 表结构
PRAGMA index_list(students);     -- 表的索引列表
PRAGMA foreign_key_list(students); -- 外键信息
```

### 总结

- `sqlite3` 命令行工具是管理 SQLite 数据库的核心工具，元命令以 `.` 开头，SQL 语句以 `;` 结尾。
- 常用元命令：`.tables`、`.schema`、`.mode`、`.headers`、`.dump`、`.import`、`.read`、`.timer`。
- `.mode` 控制输出格式，`column` 模式配合 `.headers on` 是日常使用的最佳组合。
- ATTACH DATABASE 支持跨数据库操作，最多附加 7 个数据库。
- PRAGMA 是 SQLite 特有的配置命令，用于查询和修改数据库行为。
- **关键要点**：`.dump` 导出 SQL 脚本是最可靠的备份方式；`.read` 可执行脚本文件实现批处理。
- **常见误区**：元命令误加分号；忘记 `.output stdout` 导致后续输出都进了文件；混淆 PRAGMA 和 SQL 语法。

---

# 第 2 章 数据定义与操作

本章是 SQLite 实操的核心。从创建数据库和表开始，逐步掌握数据的增删改查，最终能够熟练使用 SQL 解决实际问题。SQLite 支持 SQL-92 标准的大部分语法，同时有自己的特色。

---

## 第 4 讲：创建数据库与表（DDL）

### 概念

**DDL（Data Definition Language，数据定义语言）** 用于定义和修改数据库结构。SQLite 的 DDL 语句包括：

- **CREATE TABLE**：创建表。
- **ALTER TABLE**：修改表结构。
- **DROP TABLE**：删除表。
- **CREATE INDEX**：创建索引。
- **CREATE VIEW**：创建视图。
- **CREATE TRIGGER**：创建触发器。

SQLite 的表创建语法遵循 SQL 标准，但有几个独特之处：使用 `INTEGER PRIMARY KEY` 作为自增主键、支持 `WITHOUT ROWID` 表、支持严格的表选项。

### 原理

**1. SQLite 表的内部结构**

SQLite 的表默认使用 **rowid 表** 结构，每行有一个隐藏的 64 位整数 `rowid`（别名 `rowid`、`oid`、`_rowid_`）。如果定义了 `INTEGER PRIMARY KEY` 列，该列成为 `rowid` 的别名。`rowid` 是行的物理存储标识，B-tree 按其排序。

`WITHOUT ROWID` 表是 SQLite 3.8.2（2013）引入的特殊表类型，不存储 rowid，主键即数据的物理排序键。适合主键是多列且查询频繁按主键的场景，可节省空间和提升性能。

**2. 数据类型**

SQLite 采用**动态类型系统**（详见第 8 讲），但建表时仍需声明列类型。SQLite 支持 5 种存储类（Storage Class）：NULL、INTEGER、REAL、TEXT、BLOB。声明的类型会被归入这 5 类之一，无类型声明的列默认为 BLOB（实际行为类似动态类型）。

**3. 约束**

SQLite 支持以下列级和表级约束：

- `PRIMARY KEY`：主键，唯一且非空。
- `NOT NULL`：非空约束。
- `UNIQUE`：唯一约束。
- `CHECK`：检查约束。
- `DEFAULT`：默认值。
- `COLLATE`：排序规则（NOCASE、BINARY、RTRIM）。
- `REFERENCES`：外键约束（默认关闭，需 `PRAGMA foreign_keys=ON`）。

**4. CREATE TABLE IF NOT EXISTS**

SQLite 支持 `IF NOT EXISTS` 子句，避免表已存在时出错，这在应用初始化脚本中非常实用。

### 例子

**1. 创建基本表**

```sql
-- 创建学生表
CREATE TABLE students (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    email TEXT UNIQUE,
    age INTEGER CHECK (age >= 0 AND age <= 150),
    grade REAL DEFAULT 0.0,
    enrolled_date TEXT DEFAULT (DATE('now'))
);

-- 创建课程表
CREATE TABLE courses (
    code TEXT PRIMARY KEY,           -- TEXT类型也可作主键
    title TEXT NOT NULL,
    credit INTEGER DEFAULT 2 CHECK (credit > 0 AND credit <= 10)
);

-- 创建选课表（复合主键 + 外键）
CREATE TABLE enrollments (
    student_id INTEGER,
    course_code TEXT,
    semester TEXT,
    score REAL,
    PRIMARY KEY (student_id, course_code, semester),
    FOREIGN KEY (student_id) REFERENCES students(id)
        ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY (course_code) REFERENCES courses(code)
        ON DELETE RESTRICT ON UPDATE CASCADE
);
```

**2. 启用外键约束**

```sql
-- SQLite默认关闭外键约束，需手动开启
PRAGMA foreign_keys = ON;

-- 验证是否开启
PRAGMA foreign_keys;
-- 输出：1

-- 注意：每个连接需单独开启，可设为默认（编译选项或连接时设置）
```

**3. 使用 WITHOUT ROWID 优化**

```sql
-- 普通表（有rowid）
CREATE TABLE kv_store (
    key TEXT PRIMARY KEY,
    value BLOB
);
-- 存储：rowid B-tree + 主键索引，数据存两份

-- WITHOUT ROWID表（主键即存储键）
CREATE TABLE kv_store_fast (
    key TEXT PRIMARY KEY,
    value BLOB
) WITHOUT ROWID;
-- 存储：主键B-tree，数据只存一份，查询更快

-- 适合场景：主键较长、按主键查询为主、不需要rowid
```

**4. 修改表结构（ALTER TABLE）**

SQLite 的 ALTER TABLE 功能有限，只支持以下操作：

```sql
-- 重命名表
ALTER TABLE students RENAME TO pupils;

-- 添加列（不能添加有UNIQUE约束的列，除非表为空）
ALTER TABLE students ADD COLUMN phone TEXT;
ALTER TABLE students ADD COLUMN status TEXT DEFAULT 'active' NOT NULL;

-- 重命名列（SQLite 3.25.0+，2018）
ALTER TABLE students RENAME COLUMN name TO full_name;

-- 删除列（SQLite 3.35.0+，2021）
ALTER TABLE students DROP COLUMN phone;

-- SQLite不支持：修改列类型、添加/修改约束、删除主键
-- 变通方法：重建表
```

**5. 表重建模式（修改列类型等复杂操作）**

```sql
-- SQLite官方推荐的表重建步骤
BEGIN TRANSACTION;

-- 1. 创建新表
CREATE TABLE students_new (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    age INTEGER CHECK (age >= 0),
    email TEXT UNIQUE
);

-- 2. 复制数据
INSERT INTO students_new (id, name, age, email)
SELECT id, name, age, email FROM students;

-- 3. 删除旧表
DROP TABLE students;

-- 4. 重命名新表
ALTER TABLE students_new RENAME TO students;

-- 5. 重建索引和触发器
CREATE INDEX idx_students_email ON students(email);

COMMIT;
```

**6. 查看和删除表**

```sql
-- 查看表结构
.schema students
PRAGMA table_info(students);

-- 查看建表SQL
SELECT sql FROM sqlite_master WHERE name = 'students';

-- 删除表
DROP TABLE students;
DROP TABLE IF EXISTS students;  -- 安全删除

-- sqlite_master是系统表，存储所有schema信息
SELECT type, name, sql FROM sqlite_master;
```

### 总结

- SQLite 用 `INTEGER PRIMARY KEY` 实现自增主键（rowid 别名），`AUTOINCREMENT` 关键字改变 ID 复用策略。
- 默认表有 rowid，`WITHOUT ROWID` 表适合主键查询为主的场景。
- 外键约束默认关闭，需 `PRAGMA foreign_keys = ON` 开启。
- ALTER TABLE 功能有限：支持重命名表、添加列、重命名列、删除列；不支持修改列类型和约束。
- **关键要点**：复杂表结构修改用"重建表"模式；`sqlite_master` 系统表存储所有 schema 信息。
- **常见误区**：忘记开启外键约束导致外键失效；在 ALTER TABLE 中尝试不支持的语法；忽视 `WITHOUT ROWID` 的适用场景。

---

## 第 5 讲：数据插入、更新与删除（DML）

### 概念

**DML（Data Manipulation Language，数据操纵语言）** 用于操作表中的数据，包括：

- **INSERT**：插入数据。
- **UPDATE**：更新数据。
- **DELETE**：删除数据。
- **UPSERT**（INSERT ON CONFLICT）：插入或更新（SQLite 3.24+）。
- **REPLACE**：替换（删除冲突行后插入）。

SQLite 的 DML 语法基本遵循 SQL 标准，同时提供了一些便捷扩展。

### 原理

**1. INSERT 的多值与默认值**

SQLite 支持单条 INSERT 插入多行值（VALUES 后跟多个括号），也支持用 `DEFAULT VALUES` 插入全默认值行。INSERT ... SELECT 可从查询结果插入。

**2. UPSERT 机制**

UPSERT（INSERT ON CONFLICT UPDATE）是 SQLite 3.24.0（2018）引入的语法，当插入违反唯一约束/主键冲突时，改为执行 UPDATE。这避免了"先查询再决定插入或更新"的竞态问题，是处理幂等写入的利器。

**3. REPLACE 的行为**

REPLACE 是 SQLite 特有的语句，当插入违反唯一约束时，先删除冲突的旧行，再插入新行。REPLACE 会触发 DELETE 和 INSERT 触发器，可能产生副作用（如外键级联），使用需谨慎。

**4. 参数绑定**

SQLite 强烈推荐使用参数绑定（`?` 或 `:name`）而非字符串拼接，原因：

- **防 SQL 注入**：参数与 SQL 结构分离。
- **性能提升**：预处理语句可复用执行计划。
- **类型安全**：参数按绑定类型处理，避免类型问题。

### 例子

**1. INSERT 插入数据**

```sql
-- 单行插入（指定列）
INSERT INTO students (name, email, age)
VALUES ('张三', 'zhangsan@example.com', 20);

-- 单行插入（全部列，顺序须一致）
INSERT INTO students
VALUES (NULL, '李四', 'lisi@example.com', 21, 0.0, '2024-01-15');
-- NULL让INTEGER PRIMARY KEY AUTOINCREMENT自动生成

-- 多行插入
INSERT INTO students (name, email, age) VALUES
('王五', 'wangwu@example.com', 19),
('赵六', 'zhaoliu@example.com', 22),
('钱七', 'qianqi@example.com', 20);

-- 默认值插入
INSERT INTO students DEFAULT VALUES;

-- INSERT ... SELECT
INSERT INTO honor_students (name, email, age)
SELECT name, email, age FROM students WHERE grade >= 90;
```

**2. UPSERT（插入或更新）**

```sql
-- 场景：学生存在则更新成绩，不存在则插入
-- SQLite 3.24+ 语法
INSERT INTO enrollments (student_id, course_code, semester, score)
VALUES (1, 'CS101', '2024-Spring', 85.0)
ON CONFLICT(student_id, course_code, semester)
DO UPDATE SET score = excluded.score;

-- excluded是特殊表，引用试图插入的值
-- ON CONFLICT DO NOTHING：冲突时什么都不做
INSERT INTO students (id, name) VALUES (1, '张三')
ON CONFLICT(id) DO NOTHING;

-- 多列更新
INSERT INTO students (id, name, age)
VALUES (1, '张三', 20)
ON CONFLICT(id) DO UPDATE SET
    name = excluded.name,
    age = excluded.age,
    updated_at = CURRENT_TIMESTAMP;
```

**3. REPLACE 替换**

```sql
-- REPLACE：冲突时删除旧行再插入新行
-- 注意：REPLACE会删除旧行，未指定的列会用默认值
REPLACE INTO students (id, name, email)
VALUES (1, '张三', 'new@example.com');
-- 若id=1已存在，先DELETE旧行，再INSERT新行
-- age、grade等未指定列会被重置为默认值

-- 对比UPSERT：UPSERT只更新指定列，其他列不变
INSERT INTO students (id, name, email)
VALUES (1, '张三', 'new@example.com')
ON CONFLICT(id) DO UPDATE SET email = excluded.email;
-- age、grade等列保持不变
```

**4. UPDATE 更新**

```sql
-- 条件更新
UPDATE students SET age = 21 WHERE id = 1;

-- 多列更新
UPDATE students
SET age = age + 1, grade = 90.0
WHERE name = '张三';

-- 基于子查询的更新
UPDATE enrollments
SET score = score * 1.05
WHERE course_code = (
    SELECT code FROM courses WHERE title = '数据库'
);

-- 使用CASE条件更新
UPDATE students
SET grade = CASE
    WHEN grade >= 90 THEN 'A'
    WHEN grade >= 80 THEN 'B'
    WHEN grade >= 70 THEN 'C'
    ELSE 'D'
END;

-- UPDATE ... FROM（SQLite 3.33+，2020）
UPDATE enrollments
SET score = e2.score
FROM enrollments e2
WHERE enrollments.student_id = e2.student_id
  AND enrollments.semester = '2024-Spring'
  AND e2.semester = '2023-Fall';
```

**5. DELETE 删除**

```sql
-- 条件删除
DELETE FROM students WHERE age > 25;

-- 删除所有数据（保留表结构）
DELETE FROM students;

-- 更高效的清空方式（SQLite没有TRUNCATE）
-- 删除表再重建，速度更快
DELETE FROM students;
-- 或
DROP TABLE students;
CREATE TABLE students (...);

-- 基于子查询的删除
DELETE FROM enrollments
WHERE student_id IN (
    SELECT id FROM students WHERE grade < 60
);
```

**6. Python 中的参数绑定**

```python
import sqlite3

conn = sqlite3.connect('school.db')
cursor = conn.cursor()

# 位置参数（?）
cursor.execute(
    "INSERT INTO students (name, age) VALUES (?, ?)",
    ('张三', 20)
)

# 命名参数（:name）
cursor.execute(
    "INSERT INTO students (name, age) VALUES (:name, :age)",
    {'name': '李四', 'age': 21}
)

# 批量插入（executemany）
students = [('王五', 19), ('赵六', 22), ('钱七', 20)]
cursor.executemany(
    "INSERT INTO students (name, age) VALUES (?, ?)",
    students
)

# 安全查询（防注入）
name_input = "张三'; DROP TABLE students; --"  # 恶意输入
cursor.execute(
    "SELECT * FROM students WHERE name = ?",
    (name_input,)  # 参数化，不会执行DROP
)
# 结果：空，而非删除表

conn.commit()
conn.close()
```

### 总结

- INSERT 支持多行插入、默认值插入、INSERT...SELECT；UPSERT 处理冲突幂等写入。
- REPLACE 会删除冲突旧行再插入，未指定列重置为默认值，慎用。
- UPDATE/DELETE 必须带 WHERE，否则影响全表；SQLite 3.33+ 支持 UPDATE...FROM。
- 参数绑定（`?`）是防 SQL 注入和提升性能的标准做法。
- **关键要点**：UPSERT 优于"先查后插"模式，避免竞态；executemany 批量插入性能远优于循环 execute。
- **常见误区**：用字符串拼接 SQL 导致注入；用 REPLACE 误删未指定列数据；忘记 WHERE 导致全表更新/删除。

---

## 第 6 讲：基础查询

### 概念

**SELECT** 是 SQL 中最核心的语句，用于从数据库检索数据。SQLite 的 SELECT 语法遵循 SQL 标准，本讲讲解单表查询的基础语法。

SELECT 语句的完整结构：

```sql
SELECT [DISTINCT] 列名列表
FROM 表名
[WHERE 条件]
[GROUP BY 列名 [HAVING 组条件]]
[ORDER BY 列名 [ASC|DESC]]
[LIMIT 行数 [OFFSET 偏移量]];
```

### 原理

**1. SELECT 的执行顺序**

理解执行顺序对正确编写 SQL 至关重要：

```
书写顺序: SELECT → FROM → WHERE → GROUP BY → HAVING → ORDER BY → LIMIT
执行顺序: FROM → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT
```

执行顺序解释了：WHERE 中不能用 SELECT 别名（别名此时未生成），ORDER BY 中可以用（别名已生成）。

**2. WHERE 条件谓词**

- 比较运算符：`=`, `<>`, `!=`, `>`, `<`, `>=`, `<=`
- 逻辑运算符：`AND`, `OR`, `NOT`
- 范围：`BETWEEN a AND b`
- 集合：`IN (v1, v2, ...)`, `NOT IN`
- 模式匹配：`LIKE`（`%` 任意多字符，`_` 单字符，不区分大小写默认）, `GLOB`（区分大小写，支持 `*` `?` `[abc]`）
- 空值：`IS NULL`, `IS NOT NULL`

**3. SQLite 的 NULL 处理**

NULL 表示"未知"或"不存在"，遵循三值逻辑。`NULL = NULL` 结果为 NULL（视为假），需用 `IS NULL` 判断。聚合函数（除 `COUNT(*)`）自动忽略 NULL。

**4. LIMIT 与分页**

SQLite 用 `LIMIT n OFFSET m` 实现分页，也可简写为 `LIMIT m, n`。OFFSET 性能随偏移量增大而下降（需扫描跳过的行），深分页优化见第 14 讲。

### 例子

**1. 基本查询**

```sql
-- 全表查询
SELECT * FROM students;

-- 投影查询（指定列）
SELECT name, age FROM students;

-- 别名
SELECT name AS 姓名, age AS 年龄 FROM students;

-- 表达式
SELECT name, age, 2024 - age AS 出生年份 FROM students;

-- 去重
SELECT DISTINCT grade FROM students;
SELECT DISTINCT department, grade FROM students;
```

**2. 条件查询**

```sql
-- 比较与逻辑
SELECT * FROM students WHERE age >= 20 AND age <= 22;
SELECT * FROM students WHERE age BETWEEN 20 AND 22;  -- 等价

-- IN 集合
SELECT * FROM students WHERE department IN ('计算机', '数学', '物理');

-- LIKE 模式匹配（不区分大小写）
SELECT * FROM students WHERE name LIKE '张%';   -- 姓张
SELECT * FROM students WHERE name LIKE '张_';   -- 姓张且名字两字
SELECT * FROM students WHERE email LIKE '%@gmail.com';

-- GLOB 模式匹配（区分大小写，类Unix shell）
SELECT * FROM students WHERE name GLOB '张*';
SELECT * FROM students WHERE name GLOB '[张李]*';  -- 姓张或李

-- NULL 判断
SELECT * FROM students WHERE email IS NULL;
SELECT * FROM students WHERE email IS NOT NULL;
```

**3. 排序与分页**

```sql
-- 单列排序
SELECT * FROM students ORDER BY age DESC;  -- 降序

-- 多列排序
SELECT * FROM students ORDER BY department ASC, grade DESC;

-- 按表达式排序
SELECT name, age FROM students ORDER BY 2024 - age;  -- 按出生年份

-- 分页（每页10条，第2页）
SELECT * FROM students ORDER BY id LIMIT 10 OFFSET 10;

-- 简写形式
SELECT * FROM students ORDER BY id LIMIT 10, 10;  -- OFFSET 10, LIMIT 10
```

**4. 聚合函数**

```sql
-- 基本聚合
SELECT COUNT(*) FROM students;                    -- 总行数
SELECT COUNT(email) FROM students;                -- 非空email数
SELECT COUNT(DISTINCT department) FROM students;  -- 不同院系数

SELECT AVG(age) AS 平均年龄,
       MIN(age) AS 最小年龄,
       MAX(age) AS 最大年龄,
       SUM(credit) AS 总学分
FROM students;
```

**5. 分组查询**

```sql
-- 按院系分组统计
SELECT
    department AS 院系,
    COUNT(*) AS 人数,
    AVG(grade) AS 平均成绩,
    MAX(grade) AS 最高分
FROM students
GROUP BY department;

-- HAVING 过滤分组（WHERE过滤行，HAVING过滤组）
SELECT
    department,
    COUNT(*) AS 人数
FROM students
GROUP BY department
HAVING COUNT(*) > 10;

-- 多列分组
SELECT
    department,
    gender,
    COUNT(*) AS 人数
FROM students
GROUP BY department, gender
ORDER BY department, gender;
```

**6. NULL 处理详解**

```sql
-- COUNT的区别
SELECT
    COUNT(*) AS 总行数,         -- 包含NULL行
    COUNT(email) AS 有邮箱数,    -- 忽略NULL
    COUNT(DISTINCT department) AS 院系数
FROM students;

-- NULL参与运算结果为NULL
SELECT name, age + NULL FROM students;  -- 全部为NULL

-- 用COALESCE/IFNULL处理NULL
SELECT
    name,
    COALESCE(email, '未填写') AS 邮箱,
    IFNULL(phone, '无') AS 电话
FROM students;

-- NULLIF：两值相等返回NULL
SELECT NULLIF(grade, 0) FROM students;  -- grade为0则返回NULL
```

### 总结

- SELECT 执行顺序：FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT。
- WHERE 过滤行，HAVING 过滤组；WHERE 中不能用聚合函数和 SELECT 别名。
- SQLite 的 LIKE 不区分大小写，GLOB 区分大小写；NULL 需用 IS NULL 判断。
- 聚合函数 COUNT(*) 包含 NULL 行，COUNT(列) 忽略 NULL。
- **关键要点**：LIMIT OFFSET 实现分页，深分页需优化；COALESCE/IFNULL 是处理 NULL 的利器。
- **常见误区**：在 WHERE 中用 SELECT 别名；用 `= NULL` 而非 `IS NULL`；混淆 LIKE 和 GLOB 的大小写行为。

---

## 第 7 讲：高级查询——连接、子查询与聚合

### 概念

本讲讲解多表查询的核心技术：

**1. 连接查询（JOIN）**：从多个表中按条件组合数据。SQLite 支持内连接（INNER JOIN）、左外连接（LEFT JOIN）、交叉连接（CROSS JOIN）。注意 SQLite 不支持右外连接和全外连接（可用左连接 + UNION 模拟）。

**2. 子查询（Subquery）**：嵌套在其他 SQL 中的查询，可出现在 WHERE、FROM、SELECT 子句中。

**3. 窗口函数（Window Functions）**：SQLite 3.25.0（2018）引入，支持 ROW_NUMBER、RANK、LAG、LEAD 等，用于复杂分析查询。

**4. 集合运算**：UNION、UNION ALL、INTERSECT、EXCEPT。

### 原理

**1. 连接的本质**

连接的本质是计算参与表的笛卡尔积，再按连接条件筛选。SQLite 优化器会采用嵌套循环或哈希连接算法避免实际计算庞大笛卡尔积。

- **INNER JOIN**：只返回两表满足条件的行。
- **LEFT JOIN**：返回左表所有行，右表不匹配填 NULL。
- **CROSS JOIN**：笛卡尔积，左表每行配右表每行。

**2. 子查询的类型**

- **非相关子查询**：独立于外查询，执行一次。
- **相关子查询**：引用外查询列，外查询每行触发一次。
- **标量子查询**：返回单行单列，可用于 SELECT 和 WHERE。
- **EXISTS 子查询**：返回布尔值，判断存在性。

**3. 窗口函数原理**

窗口函数对"窗口"（一组行）计算，返回每行一个值，不改变行数。语法：`函数() OVER (PARTITION BY ... ORDER BY ...)`。与 GROUP BY 的区别：GROUP BY 每组返回一行，窗口函数每行返回一个值。

### 例子

**1. 内连接**

```sql
-- 查询学生及其选课信息
SELECT
    s.name AS 学生,
    c.title AS 课程,
    e.score AS 成绩
FROM students s
INNER JOIN enrollments e ON s.id = e.student_id
INNER JOIN courses c ON e.course_code = c.code;

-- 使用USING简写（当连接列同名时）
-- SELECT * FROM a JOIN b USING(id);
```

**2. 左外连接**

```sql
-- 查询所有学生及其选课（未选课的学生也显示）
SELECT
    s.name,
    c.title,
    e.score
FROM students s
LEFT JOIN enrollments e ON s.id = e.student_id
LEFT JOIN courses c ON e.course_code = c.code;

-- 找出未选任何课程的学生
SELECT s.name
FROM students s
LEFT JOIN enrollments e ON s.id = e.student_id
WHERE e.student_id IS NULL;
```

**3. 自连接**

```sql
-- 假设employees表有manager_id自引用
-- 查询每个员工及其经理
SELECT
    e.name AS 员工,
    m.name AS 经理
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.id;
```

**4. 子查询**

```sql
-- 标量子查询（WHERE中）
SELECT * FROM students
WHERE age > (SELECT AVG(age) FROM students);

-- IN子查询
SELECT name FROM students
WHERE id IN (SELECT student_id FROM enrollments WHERE score >= 90);

-- 相关子查询（EXISTS）
SELECT s.name FROM students s
WHERE EXISTS (
    SELECT 1 FROM enrollments e
    WHERE e.student_id = s.id AND e.score = 100
);

-- 派生表（FROM中的子查询）
SELECT department, avg_age
FROM (
    SELECT department, AVG(age) AS avg_age
    FROM students
    GROUP BY department
) t
WHERE avg_age > 20;

-- SELECT中的标量子查询
SELECT
    s.name,
    (SELECT COUNT(*) FROM enrollments e WHERE e.student_id = s.id) AS 选课数
FROM students s;
```

**5. 窗口函数**

```sql
-- ROW_NUMBER：为每行编号
SELECT
    name,
    department,
    grade,
    ROW_NUMBER() OVER (PARTITION BY department ORDER BY grade DESC) AS 院内排名
FROM students;

-- RANK 和 DENSE_RANK
SELECT
    name,
    grade,
    RANK() OVER (ORDER BY grade DESC) AS 排名,        -- 1,2,2,4
    DENSE_RANK() OVER (ORDER BY grade DESC) AS 紧凑排名  -- 1,2,2,3
FROM students;

-- LAG 和 LEAD：访问前/后行
SELECT
    date,
    sales,
    LAG(sales, 1) OVER (ORDER BY date) AS 前一天,
    sales - LAG(sales, 1) OVER (ORDER BY date) AS 环比增长,
    LEAD(sales, 1) OVER (ORDER BY date) AS 后一天
FROM daily_sales;

-- 累计求和
SELECT
    date,
    sales,
    SUM(sales) OVER (ORDER BY date) AS 累计销售额,
    AVG(sales) OVER (ORDER BY date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS 7日均值
FROM daily_sales;
```

**6. 集合运算**

```sql
-- UNION：合并去重
SELECT name FROM students WHERE department = '计算机'
UNION
SELECT name FROM students WHERE department = '数学';
-- 等价于 department IN ('计算机', '数学')

-- UNION ALL：合并不去重（更快）
SELECT 'student' AS type, name FROM students
UNION ALL
SELECT 'teacher' AS type, name FROM teachers;

-- INTERSECT：交集
SELECT student_id FROM enrollments WHERE course_code = 'CS101'
INTERSECT
SELECT student_id FROM enrollments WHERE course_code = 'CS102';
-- 同时选修CS101和CS102的学生

-- EXCEPT：差集
SELECT student_id FROM enrollments WHERE course_code = 'CS101'
EXCEPT
SELECT student_id FROM enrollments WHERE course_code = 'CS102';
-- 选修CS101但未选CS102的学生
```

**7. 综合案例：成绩分析报表**

```sql
-- 查询每门课程的选课人数、平均分、最高分、最低分，按平均分降序
SELECT
    c.code,
    c.title,
    COUNT(e.student_id) AS 选课人数,
    ROUND(AVG(e.score), 2) AS 平均分,
    MAX(e.score) AS 最高分,
    MIN(e.score) AS 最低分,
    COUNT(CASE WHEN e.score >= 90 THEN 1 END) AS 优秀人数,
    ROUND(COUNT(CASE WHEN e.score >= 90 THEN 1 END) * 100.0 / COUNT(*), 1) AS 优秀率
FROM courses c
LEFT JOIN enrollments e ON c.code = e.course_code
GROUP BY c.code, c.title
HAVING COUNT(e.student_id) > 0
ORDER BY 平均分 DESC;
```

### 总结

- SQLite 支持内连接、左连接、交叉连接，不支持右连接和全连接（可用左连接+UNION模拟）。
- 子查询分相关和非相关，相关子查询效率较低但表达能力强；EXISTS 适合存在性判断。
- 窗口函数（SQLite 3.25+）是复杂分析的利器，不改变行数，支持分区、排序、帧定义。
- 集合运算：UNION 去重、UNION ALL 不去重（更快）、INTERSECT 交集、EXCEPT 差集。
- **关键要点**：窗口函数解决"排名、累计、环比"类问题；CASE WHEN 实现条件聚合。
- **常见误区**：混淆 INNER JOIN 和 LEFT JOIN 导致遗漏数据；窗口函数与 GROUP BY 混用；UNION 忘记 ALL 导致不必要的去重开销。

---

# 第 3 章 SQLite 核心机制

本章深入 SQLite 的内部机制，讲解其独特的动态类型系统、约束机制和索引原理。理解这些核心机制是高效使用 SQLite 的关键，也是与其他数据库区分的重要知识点。

---

## 第 8 讲：动态类型系统

### 概念

**动态类型系统（Dynamic Type System）** 是 SQLite 最独特的设计之一。与大多数数据库（MySQL、PostgreSQL、Oracle）采用的**静态类型系统**不同，SQLite 中列的类型不是严格强制的，而是"建议性"的。存储在列中的值的实际类型由值本身决定，而非列声明。

SQLite 的类型系统基于两个概念：

- **存储类（Storage Class）**：SQLite 只有 5 种存储类——NULL、INTEGER、REAL、TEXT、BLOB。任何值必属于其一。
- **类型亲和性（Type Affinity）**：列的"偏好"类型，决定 SQLite 如何处理插入的数据。建表时声明的列类型被映射为一种亲和性。

SQLite 3.37.0（2021）引入了 **STRICT 表**模式，可选择启用严格的静态类型检查，兼顾灵活性和安全性。

### 原理

**1. 五种存储类**

| 存储类 | 说明 | 示例 |
|--------|------|------|
| NULL | 空值 | NULL |
| INTEGER | 有符号整数（1/2/3/4/6/8字节，按需分配） | 42, -7 |
| REAL | 8字节IEEE浮点数 | 3.14, -0.5 |
| TEXT | 文本字符串（UTF-8/UTF-16BE/UTF-16LE） | 'hello', '你好' |
| BLOB | 二进制大对象，原样存储 | x'48656c6c6f' |

SQLite 的 INTEGER 根据值大小自动选择 1/2/3/4/6/8 字节存储，节省空间。这是 SQLite 的一个优化细节。

**2. 类型亲和性规则**

建表时声明的列类型按以下规则映射为亲和性：

- 含 "INT" → INTEGER 亲和性（如 INT, INTEGER, BIGINT, TINYINT）
- 含 "CHAR"、"CLOB"、"TEXT" → TEXT 亲和性（如 VARCHAR, CHAR, TEXT）
- 含 "BLOB" 或无类型 → BLOB 亲和性（无偏好）
- 含 "REAL"、"FLOA"、"DOUB" → REAL 亲和性（如 REAL, FLOAT, DOUBLE）
- 其他 → NUMERIC 亲和性（如 DECIMAL, NUMERIC, DATE）

NUMERIC 亲和性最特殊：尝试将值转为 INTEGER 或 REAL，若无法转换则保持原类型。

**3. 亲和性如何影响存储**

当插入值时，SQLite 根据列的亲和性尝试转换：

- TEXT 亲和性列：INTEGER/REAL 值转为 TEXT 存储。
- INTEGER 亲和性列：REAL 值若为整数（如 3.0）转为 INTEGER；TEXT 值尝试转为数字。
- REAL 亲和性列：INTEGER 转为 REAL；TEXT 尝试转为数字。
- BLOB 亲和性列：不做任何转换，按值原类型存储。
- NUMERIC 亲和性列：尝试转为 INTEGER 或 REAL，失败则保持原类型。

**4. 严格表（STRICT Tables）**

SQLite 3.37.0 引入 STRICT 模式，强制列只接受声明类型的值：

```sql
CREATE TABLE strict_table (
    id INTEGER,
    name TEXT
) STRICT;
-- 只能存INTEGER和TEXT，其他类型报错
```

### 例子

**1. 动态类型演示**

```sql
-- 创建一个"无类型"列的表
CREATE TABLE dynamic_test (value);

-- 同一列可存不同类型
INSERT INTO dynamic_test VALUES
    (42),                  -- INTEGER
    (3.14),                -- REAL
    ('hello'),             -- TEXT
    (x'48656c6c6f'),       -- BLOB
    (NULL);                -- NULL

-- 查看每行的实际存储类
SELECT value, typeof(value) FROM dynamic_test;
-- 42|integer
-- 3.14|real
-- hello|text
-- hello|blob
-- NULL|null
```

**2. 亲和性转换示例**

```sql
CREATE TABLE affinity_test (
    int_col INTEGER,
    text_col TEXT,
    real_col REAL,
    blob_col BLOB,
    num_col NUMERIC
);

INSERT INTO affinity_test VALUES
    ('123', 123, '3.14', 42, '100');

SELECT
    typeof(int_col),    -- integer（'123'转为整数123）
    typeof(text_col),   -- text（123转为'123'）
    typeof(real_col),   -- real（'3.14'转为3.14）
    typeof(blob_col),   -- integer（BLOB亲和性不转换）
    typeof(num_col)     -- integer（'100'转为整数100）
FROM affinity_test;
```

**3. NUMERIC 亲和性的特殊行为**

```sql
CREATE TABLE numeric_test (val NUMERIC);

INSERT INTO numeric_test VALUES
    ('100'),       -- 转为INTEGER 100
    ('100.5'),     -- 转为REAL 100.5
    ('hello'),     -- 无法转换，保持TEXT
    (100),         -- INTEGER
    (100.0);       -- 转为INTEGER 100（整数浮点）

SELECT val, typeof(val) FROM numeric_test;
-- 100|integer
-- 100.5|real
-- hello|text
-- 100|integer
-- 100|integer
```

**4. 日期时间的存储**

SQLite 没有专门的 DATE/TIME 类型，通常用以下方式存储：

```sql
CREATE TABLE events (
    id INTEGER PRIMARY KEY,
    name TEXT,
    -- 方式1：TEXT（ISO8601格式，可读性好，可排序）
    created_text TEXT,
    -- 方式2：INTEGER（Unix时间戳，计算方便）
    created_int INTEGER,
    -- 方式3：REAL（Julian日，天文计算用）
    created_real REAL
);

INSERT INTO events VALUES (
    1, '事件A',
    '2024-01-15 10:30:00',           -- TEXT
    strftime('%s', 'now'),            -- INTEGER Unix时间戳
    julianday('now')                  -- REAL Julian日
);

-- SQLite内置日期函数可处理所有格式
SELECT
    name,
    created_text,
    date(created_text) AS 日期,
    datetime(created_int, 'unixepoch') AS 时间戳转日期,
    date(created_real) AS Julian转日期
FROM events;
```

**5. STRICT 表对比**

```sql
-- 普通表（动态类型）
CREATE TABLE loose (val INTEGER);
INSERT INTO loose VALUES ('hello');  -- 成功！存为TEXT
SELECT typeof(val) FROM loose;       -- text

-- STRICT表（严格类型）
CREATE TABLE strict (val INTEGER) STRICT;
INSERT INTO strict VALUES ('hello');  -- 报错：类型不匹配
INSERT INTO strict VALUES (42);       -- 成功
INSERT INTO strict VALUES (3.14);     -- 报错（REAL不能存入INTEGER列）
INSERT INTO strict VALUES (3.0);      -- 报错（即使是整数浮点）

-- STRICT表支持的类型：INTEGER, REAL, TEXT, BLOB, ANY
CREATE TABLE strict2 (
    id INTEGER,
    data ANY  -- ANY列接受任何类型
) STRICT;
```

### 总结

- SQLite 采用动态类型系统，5 种存储类：NULL、INTEGER、REAL、TEXT、BLOB。
- 类型亲和性是列的"偏好"，决定数据转换规则：INTEGER、TEXT、REAL、BLOB、NUMERIC 五种。
- 同一列可存储不同类型的值，这是 SQLite 独有的灵活性。
- SQLite 3.37+ 的 STRICT 表提供严格类型检查，适合需要类型安全的场景。
- **关键要点**：日期时间用 TEXT（ISO8601）存储最通用；typeof() 函数查看值的实际存储类。
- **常见误区**：误以为 SQLite 没有类型（有亲和性）；用 INTEGER 列存 TEXT 导致查询异常；忽视 STRICT 表的严格性。

---

## 第 9 讲：约束与数据完整性

### 概念

**约束（Constraint）** 是数据库保证数据正确性和一致性的规则。SQLite 支持以下约束：

- **PRIMARY KEY**：主键，唯一标识一行，非空且唯一。
- **NOT NULL**：非空约束，列值不能为 NULL。
- **UNIQUE**：唯一约束，列值（或列组合）在表内唯一。
- **CHECK**：检查约束，列值必须满足指定条件。
- **DEFAULT**：默认值，插入未指定时使用。
- **COLLATE**：排序规则，影响比较和排序行为。
- **FOREIGN KEY**：外键约束，保证参照完整性。
- **GENERATED（生成列）**：列值由其他列计算得出（SQLite 3.31+）。

约束分为**列级约束**（跟在列定义后）和**表级约束**（所有列定义后单独定义）。

### 原理

**1. 主键的细节**

- `INTEGER PRIMARY KEY`：成为 rowid 的别名，自动自增（无需 AUTOINCREMENT）。
- `INTEGER PRIMARY KEY AUTOINCREMENT`：自增且不复用已删除行的 ID（ID 单调递增）。
- 非 INTEGER 类型的主键（如 TEXT PRIMARY KEY）：不作为 rowid 别名，是普通唯一索引。
- 复合主键：多列组合作为主键，需用表级约束定义。

**2. 外键的延迟与即时检查**

SQLite 外键默认即时检查（每条语句后检查）。也支持 `DEFERRABLE INITIALLY DEFERRED`，将检查延迟到事务提交时，便于复杂操作（如循环引用插入）。

**3. CHECK 约束的表达式**

CHECK 约束可以是任何返回布尔值的表达式，但不能包含子查询、`CURRENT_TIME` 等部分函数（实际上 SQLite 允许较宽松，但建议简单）。CHECK 在 INSERT/UPDATE 时验证，失败则拒绝操作。

**4. 生成列（Generated Columns）**

SQLite 3.31.0（2020）引入生成列，列值由表达式自动计算：

- `VIRTUAL`：不存储，查询时计算（省空间，耗 CPU）。
- `STORED`：存储实际值（省 CPU，耗空间）。

**5. COLLATE 排序规则**

SQLite 内置三种排序规则：

- `BINARY`（默认）：二进制比较，区分大小写。
- `NOCASE`：不区分大小写（仅 ASCII）。
- `RTRIM`：比较时忽略尾部空格。

### 例子

**1. 主键的各种形式**

```sql
-- 自增主键（rowid别名）
CREATE TABLE t1 (id INTEGER PRIMARY KEY AUTOINCREMENT, name TEXT);

-- 非自增主键（删除后ID可复用）
CREATE TABLE t2 (id INTEGER PRIMARY KEY, name TEXT);

-- TEXT主键（非rowid别名）
CREATE TABLE t3 (code TEXT PRIMARY KEY, name TEXT);

-- 复合主键
CREATE TABLE t4 (
    student_id INTEGER,
    course_id INTEGER,
    score REAL,
    PRIMARY KEY (student_id, course_id)
);

-- 查看主键信息
PRAGMA table_info(t4);
```

**2. 唯一约束与检查约束**

```sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    username TEXT NOT NULL UNIQUE,
    email TEXT UNIQUE NOT NULL,
    age INTEGER CHECK (age >= 0 AND age <= 150),
    role TEXT CHECK (role IN ('admin', 'user', 'guest')) DEFAULT 'user',
    password TEXT CHECK (length(password) >= 8),
    -- 表级约束：组合唯一
    UNIQUE (username, email)
);

-- 违反约束的插入会失败
INSERT INTO users (username, email, age, password)
VALUES ('admin', 'a@b.com', 200, 'short');
-- 报错：CHECK constraint failed: age
```

**3. 外键约束**

```sql
-- 启用外键
PRAGMA foreign_keys = ON;

CREATE TABLE departments (
    id INTEGER PRIMARY KEY,
    name TEXT UNIQUE NOT NULL
);

CREATE TABLE employees (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    dept_id INTEGER,
    manager_id INTEGER,
    FOREIGN KEY (dept_id) REFERENCES departments(id)
        ON DELETE SET NULL        -- 删部门时员工dept_id置NULL
        ON UPDATE CASCADE,        -- 改部门ID时级联更新
    FOREIGN KEY (manager_id) REFERENCES employees(id)
        ON DELETE SET NULL
        -- 自引用外键
);

-- 延迟外键检查（事务提交时才检查）
CREATE TABLE accounts (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    parent_id INTEGER,
    FOREIGN KEY (parent_id) REFERENCES accounts(id)
        DEFERRABLE INITIALLY DEFERRED
);

-- 延迟检查允许循环引用插入
BEGIN TRANSACTION;
INSERT INTO accounts (id, name, parent_id) VALUES (1, 'A', 2);  -- 临时违反
INSERT INTO accounts (id, name, parent_id) VALUES (2, 'B', 1);  -- 修复
COMMIT;  -- 提交时检查通过
```

**4. 生成列**

```sql
CREATE TABLE products (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    price REAL NOT NULL,
    quantity INTEGER NOT NULL DEFAULT 0,
    -- VIRTUAL生成列：查询时计算
    total_value REAL GENERATED ALWAYS AS (price * quantity) VIRTUAL,
    -- STORED生成列：存储实际值
    display_name TEXT GENERATED ALWAYS AS (name || ' ($' || price || ')') STORED
);

INSERT INTO products (name, price, quantity) VALUES
    ('笔记本', 5999.0, 10),
    ('手机', 3999.0, 20);

SELECT name, price, quantity, total_value, display_name FROM products;
-- 笔记本|5999.0|10|59990.0|笔记本 ($5999.0)
-- 手机|3999.0|20|79980.0|手机 ($3999.0)

-- 不能直接插入生成列的值
INSERT INTO products (name, price, quantity, total_value)
VALUES ('平板', 2999.0, 5, 99999);  -- 报错
```

**5. COLLATE 排序规则**

```sql
CREATE TABLE texts (
    word TEXT
);

INSERT INTO texts VALUES ('Apple'), ('apple'), ('Banana'), ('banana');

-- 默认BINARY（区分大小写）
SELECT * FROM texts ORDER BY word;
-- Apple, Banana, apple, banana

-- NOCASE排序（不区分大小写）
SELECT * FROM texts ORDER BY word COLLATE NOCASE;
-- Apple, apple, Banana, banana

-- 列级COLLATE
CREATE TABLE users2 (
    username TEXT COLLATE NOCASE UNIQUE
);
INSERT INTO users2 VALUES ('Admin');
INSERT INTO users2 VALUES ('admin');  -- 报错：UNIQUE约束冲突（不区分大小写）
```

**6. 约束的启用与禁用**

```sql
-- 临时禁用外键（不推荐，可能破坏完整性）
PRAGMA foreign_keys = OFF;
-- 执行批量操作...
PRAGMA foreign_keys = ON;

-- 查看外键信息
PRAGMA foreign_key_list(employees);

-- 查看所有约束
SELECT sql FROM sqlite_master WHERE name = 'users';
```

### 总结

- SQLite 支持主键、非空、唯一、检查、默认值、外键、生成列等约束。
- `INTEGER PRIMARY KEY` 是 rowid 别名自动自增；`AUTOINCREMENT` 保证 ID 单调递增不复用。
- 外键默认关闭需手动开启；支持 `DEFERRABLE INITIALLY DEFERRED` 延迟到事务提交检查。
- 生成列（SQLite 3.31+）分 VIRTUAL（不存储）和 STORED（存储）两种。
- **关键要点**：COLLATE 影响比较和排序；自引用外键需用延迟检查处理循环引用。
- **常见误区**：忘记开启外键约束；误用 AUTOINCREMENT（普通自增已够用）；在生成列上插入值。

---

## 第 10 讲：索引与查询计划

### 概念

**索引（Index）** 是提升查询性能的辅助数据结构。SQLite 的索引是 B-tree 结构，存储索引键和对应的 rowid（或主键），通过索引可快速定位数据。

SQLite 支持的索引类型：

- **单列索引**：基于单列的索引。
- **复合索引**：基于多列的索引，遵循最左前缀原则。
- **唯一索引**：索引列值唯一。
- **表达式索引**：基于表达式的索引（SQLite 3.9+）。
- **部分索引**：只索引满足条件的行（SQLite 3.8+）。

**EXPLAIN QUERY PLAN** 是 SQLite 分析查询计划的命令，显示 SQL 如何使用索引和扫描表。

### 原理

**1. SQLite 的 B-tree 结构**

SQLite 的表和索引都存储在 B-tree 中：

- **表 B-tree**：rowid 为键，整行为值。按 rowid 排序。
- **索引 B-tree**：索引列为键，rowid 为值。按索引列排序。

查询时，若 WHERE 条件命中索引，先在索引 B-tree 查找得到 rowid，再用 rowid 在表 B-tree 查找整行（"回表"）。若索引覆盖了查询列，则无需回表。

**2. 索引的代价**

- **空间代价**：每个索引是一棵 B-tree，占用磁盘空间。
- **写入代价**：INSERT/UPDATE/DELETE 需同步维护索引。
- **优化器代价**：索引过多增加选择执行计划的成本。

**3. 复合索引的最左前缀原则**

复合索引 `(a, b, c)` 的 B-tree 按 a 排序，相同 a 内按 b 排序，相同 b 内按 c 排序。因此：

- `WHERE a = 1`：命中索引。
- `WHERE a = 1 AND b = 2`：命中索引。
- `WHERE b = 2`：不命中（缺少最左列 a）。
- `WHERE a = 1 AND c = 3`：部分命中（a 命中，c 无法用索引）。

**4. 部分索引与表达式索引**

- **部分索引**：`CREATE INDEX ... WHERE 条件`，只索引满足条件的行，节省空间，适合"热数据"。
- **表达式索引**：`CREATE INDEX ... ON 表(表达式)`，索引函数/表达式的结果，解决"函数导致索引失效"问题。

### 例子

**1. 创建各类索引**

```sql
-- 单列索引
CREATE INDEX idx_name ON students(name);

-- 唯一索引
CREATE UNIQUE INDEX idx_email ON students(email);

-- 复合索引
CREATE INDEX idx_dept_grade ON students(department, grade);

-- 降序索引（SQLite 3.10+）
CREATE INDEX idx_grade_desc ON students(grade DESC);

-- 部分索引：只索引活跃用户
CREATE INDEX idx_active_users ON students(name) WHERE status = 'active';

-- 表达式索引：索引小写后的email
CREATE INDEX idx_email_lower ON students(LOWER(email));
```

**2. EXPLAIN QUERY PLAN 分析**

```sql
-- 无索引：全表扫描
EXPLAIN QUERY PLAN SELECT * FROM students WHERE name = '张三';
-- SCAN TABLE students

-- 有索引：索引查找
CREATE INDEX idx_name ON students(name);
EXPLAIN QUERY PLAN SELECT * FROM students WHERE name = '张三';
-- SEARCH TABLE students USING INDEX idx_name (name=?)

-- 复合索引的最左前缀
CREATE INDEX idx_dept_grade ON students(department, grade);
EXPLAIN QUERY PLAN SELECT * FROM students WHERE department = '计算机';
-- SEARCH TABLE students USING INDEX idx_dept_grade (department=?)

EXPLAIN QUERY PLAN SELECT * FROM students WHERE grade > 80;
-- SCAN TABLE students（未命中，缺少department）

EXPLAIN QUERY PLAN SELECT * FROM students WHERE department = '计算机' AND grade > 80;
-- SEARCH TABLE students USING INDEX idx_dept_grade (department=? AND grade>?)
```

**3. 覆盖索引**

```sql
-- 复合索引覆盖查询列
CREATE INDEX idx_cover ON students(department, grade, name);

-- 查询只需索引列，无需回表
EXPLAIN QUERY PLAN
SELECT department, grade, name FROM students WHERE department = '计算机';
-- SEARCH TABLE students USING COVERING INDEX idx_cover (department=?)
-- 注意：COVERING表示覆盖索引，不回表
```

**4. 表达式索引解决函数失效**

```sql
-- 无表达式索引：函数导致索引失效
CREATE INDEX idx_email ON students(email);
EXPLAIN QUERY PLAN SELECT * FROM students WHERE LOWER(email) = 'admin@example.com';
-- SCAN TABLE students（索引失效）

-- 创建表达式索引
CREATE INDEX idx_email_lower ON students(LOWER(email));
EXPLAIN QUERY PLAN SELECT * FROM students WHERE LOWER(email) = 'admin@example.com';
-- SEARCH TABLE students USING INDEX idx_email_lower (<expr>=?)
```

**5. 部分索引的应用**

```sql
-- 场景：订单表大部分是已完成订单，待处理订单少但查询频繁
CREATE TABLE orders (
    id INTEGER PRIMARY KEY,
    status TEXT,  -- pending/paid/shipped/done
    amount REAL,
    created_at TEXT
);

-- 全量索引：索引所有行（浪费空间）
CREATE INDEX idx_status_all ON orders(status);

-- 部分索引：只索引待处理订单（省空间，查询快）
CREATE INDEX idx_pending ON orders(id) WHERE status = 'pending';

-- 查询待处理订单用部分索引
EXPLAIN QUERY PLAN SELECT * FROM orders WHERE status = 'pending';
-- SEARCH TABLE orders USING INDEX idx_pending
```

**6. 索引的查看与管理**

```sql
-- 查看表的所有索引
PRAGMA index_list(students);

-- 查看索引详情
PRAGMA index_info(idx_dept_grade);

-- 查看索引的创建SQL
SELECT sql FROM sqlite_master WHERE type = 'index' AND tbl_name = 'students';

-- 删除索引
DROP INDEX idx_name;
DROP INDEX IF EXISTS idx_name;

-- 重建索引（碎片整理）
REINDEX idx_name;
REINDEX;  -- 重建所有索引

-- 分析索引使用情况（需开启EXPLAIN）
EXPLAIN QUERY PLAN SELECT * FROM students WHERE department = '计算机' AND grade > 80;
```

**7. 自动索引**

SQLite 优化器在某些情况下会自动创建临时索引（不是持久化的）：

```sql
-- 无索引时，优化器可能自动创建临时索引
EXPLAIN QUERY PLAN
SELECT * FROM students s1 JOIN students s2 ON s1.email = s2.email;
-- 可能显示：SEARCH TABLE students USING AUTOMATIC COVERING INDEX (email=?)

-- 自动索引说明缺少手动索引，建议创建
CREATE INDEX idx_email ON students(email);
```

### 总结

- SQLite 索引是 B-tree 结构，存储索引键和 rowid，查询命中索引可避免全表扫描。
- 复合索引遵循最左前缀原则，列顺序按"等值 → 范围 → 排序"设计。
- 覆盖索引避免回表，是性能优化的利器；表达式索引解决函数导致索引失效。
- 部分索引只索引满足条件的行，适合"热数据"场景，节省空间。
- **关键要点**：用 EXPLAIN QUERY PLAN 分析查询计划，关注 SCAN（全表扫描）vs SEARCH（索引查找）。
- **常见误区**：在索引列用函数导致失效；忽视复合索引列顺序；为所有列建索引忽视写入代价。

---

# 第 4 章 进阶特性

本章讲解 SQLite 的进阶特性，包括事务与并发控制、视图与触发器与 CTE、内置函数与表达式。这些特性让 SQLite 能够应对更复杂的业务场景，也是从"会用"到"用好"的关键。

---

## 第 11 讲：事务与并发控制

### 概念

**事务（Transaction）** 是数据库操作的逻辑执行单位，具有 ACID 特性：原子性（Atomicity）、一致性（Consistency）、隔离性（Isolation）、持久性（Durability）。

SQLite 的事务支持以下语句：

- **BEGIN**：开始事务。
- **COMMIT**：提交事务。
- **ROLLBACK**：回滚事务。
- **SAVEPOINT**：设置保存点，可部分回滚。

**并发控制** 是多连接同时访问数据库时的协调机制。SQLite 采用**文件级锁**实现并发控制，锁模式有：

- **ROLLBACK 日志模式**（默认）：DELETE、TRUNCATE、PERSIST、MEMORY 四种日志子模式。
- **WAL（Write-Ahead Logging）模式**：SQLite 3.7.0 引入，支持读写并发，是推荐的并发模式。

### 原理

**1. SQLite 的锁状态**

SQLite 的锁有 5 种状态，从低到高：

| 锁状态 | 说明 |
|--------|------|
| UNLOCKED | 无锁，初始状态 |
| SHARED | 共享锁，可读不可写，多个连接可同时持有 |
| RESERVED | 保留锁，准备写，同一时刻只能一个连接持有 |
| PENDING | 等待锁，准备升级为 EXCLUSIVE，阻止新的 SHARED |
| EXCLUSIVE | 排他锁，正在写，独占访问 |

锁升级路径：UNLOCKED → SHARED → RESERVED → PENDING → EXCLUSIVE。写操作需获取 EXCLUSIVE 锁，期间其他连接无法读写（ROLLBACK 模式）。

**2. WAL 模式原理**

WAL 模式改变了写入方式：

- **写入**：不修改数据库文件，而是追加到 WAL 文件（`-wal` 后缀）。
- **读取**：先查 WAL 文件，再查数据库文件，合并结果。
- **检查点（Checkpoint）**：定期将 WAL 内容合并回数据库文件。

WAL 的优势：

- **读写并发**：读操作不阻塞写，写操作不阻塞读。
- **性能提升**：写入是顺序追加，比随机修改快。
- **崩溃恢复**：WAL 是预写日志，崩溃后可恢复。

WAL 的限制：

- 不支持网络文件系统（NFS 等）。
- 需要额外的 `-wal` 和 `-shm` 文件。
- 单个 WAL 文件大小有限制（默认 1000 页）。

**3. 事务隔离级别**

SQLite 的事务隔离基于快照：

- **BEGIN**：默认 DEFERRED，延迟获取锁，第一次读或写时确定事务类型。
- **BEGIN IMMEDIATE**：立即获取 RESERVED 锁，确保可写。
- **BEGIN EXCLUSIVE**：立即获取 EXCLUSIVE 锁，独占访问。

SQLite 默认隔离级别是 SERIALIZABLE（串行化），通过锁机制保证。

**4. SAVEPOINT 保存点**

SAVEPOINT 允许在事务内设置命名标记，可回滚到指定保存点而非整个事务，实现部分回滚。

### 例子

**1. 基本事务**

```sql
-- 转账事务
BEGIN TRANSACTION;

UPDATE accounts SET balance = balance - 100 WHERE id = 'A';
UPDATE accounts SET balance = balance + 100 WHERE id = 'B';

-- 检查余额是否合法
SELECT CASE WHEN (SELECT balance FROM accounts WHERE id = 'A') < 0
            THEN 1 ELSE 0 END;

-- 若余额为负，回滚
-- ROLLBACK;

COMMIT;
```

**2. SAVEPOINT 部分回滚**

```sql
BEGIN TRANSACTION;

INSERT INTO orders (id, amount) VALUES (1, 100);
SAVEPOINT sp1;

INSERT INTO order_items (order_id, product) VALUES (1, '商品A');
SAVEPOINT sp2;

INSERT INTO order_items (order_id, product) VALUES (1, '商品B');
-- 发现商品B库存不足

-- 回滚到sp2，只撤销商品B的插入
ROLLBACK TO sp2;

-- 继续插入商品C
INSERT INTO order_items (order_id, product) VALUES (1, '商品C');

COMMIT;
-- 最终结果：orders有1条，order_items有商品A和商品C
```

**3. 启用 WAL 模式**

```sql
-- 设置WAL模式（持久设置，只需一次）
PRAGMA journal_mode = WAL;
-- 输出：wal

-- 查看当前模式
PRAGMA journal_mode;
-- wal

-- WAL相关参数
PRAGMA wal_autocheckpoint = 1000;  -- 自动检查点页数（默认1000）
PRAGMA wal_checkpoint;             -- 手动触发检查点
PRAGMA journal_size_limit = 67108864;  -- WAL文件大小限制（64MB）

-- 切换回ROLLBACK模式
PRAGMA journal_mode = DELETE;
```

**4. 事务类型选择**

```sql
-- DEFERRED（默认）：延迟获取锁
-- 适合读多写少，但可能导致死锁
BEGIN;
SELECT COUNT(*) FROM accounts;  -- 获取SHARED锁
-- 此时其他连接可读可写（但写会等待）
UPDATE accounts SET balance = 100;  -- 尝试升级为EXCLUSIVE
-- 若其他连接持有SHARED锁，需等待
COMMIT;

-- IMMEDIATE：立即获取RESERVED锁
-- 适合写事务，提前声明写意图，避免死锁
BEGIN IMMEDIATE;
-- 立即获取RESERVED锁，其他连接可读但不可写
UPDATE accounts SET balance = 100;
COMMIT;

-- EXCLUSIVE：立即获取EXCLUSIVE锁
-- 适合需要独占访问的场景
BEGIN EXCLUSIVE;
-- 独占访问，其他连接无法读写
UPDATE accounts SET balance = 100;
COMMIT;
```

**5. 并发写入示例（Python）**

```python
import sqlite3
import threading
import time

def setup():
    conn = sqlite3.connect('bank.db')
    conn.execute('PRAGMA journal_mode = WAL')  # 启用WAL
    conn.execute('''CREATE TABLE IF NOT EXISTS accounts (
        id TEXT PRIMARY KEY, balance REAL
    )''')
    conn.execute("INSERT OR REPLACE INTO accounts VALUES ('A', 1000)")
    conn.execute("INSERT OR REPLACE INTO accounts VALUES ('B', 1000)")
    conn.commit()
    conn.close()

def transfer(from_id, to_id, amount, retries=3):
    """带重试的转账，处理并发锁冲突"""
    for attempt in range(retries):
        try:
            conn = sqlite3.connect('bank.db', timeout=5)  # 5秒锁等待
            conn.execute('PRAGMA journal_mode = WAL')
            conn.execute('BEGIN IMMEDIATE')  # 立即获取写锁

            # 查询余额并加锁
            balance = conn.execute(
                "SELECT balance FROM accounts WHERE id = ?", (from_id,)
            ).fetchone()[0]

            if balance < amount:
                conn.rollback()
                return False, "余额不足"

            conn.execute(
                "UPDATE accounts SET balance = balance - ? WHERE id = ?",
                (amount, from_id)
            )
            conn.execute(
                "UPDATE accounts SET balance = balance + ? WHERE id = ?",
                (amount, to_id)
            )
            conn.commit()
            conn.close()
            return True, "成功"

        except sqlite3.OperationalError as e:
            if "database is locked" in str(e):
                time.sleep(0.1 * (attempt + 1))  # 退避重试
                continue
            raise
    return False, "锁冲突重试失败"

# 并发测试
setup()
threads = []
for i in range(10):
    t = threading.Thread(target=transfer, args=('A', 'B', 10))
    threads.append(t)

for t in threads:
    t.start()
for t in threads:
    t.join()

# 验证结果
conn = sqlite3.connect('bank.db')
print(conn.execute("SELECT * FROM accounts").fetchall())
# 总额应仍为2000
conn.close()
```

### 总结

- SQLite 事务支持 ACID，用 BEGIN/COMMIT/ROLLBACK 控制，SAVEPOINT 实现部分回滚。
- 锁有 5 种状态：UNLOCKED → SHARED → RESERVED → PENDING → EXCLUSIVE。
- WAL 模式支持读写并发，是推荐的日志模式；不支持网络文件系统。
- 事务类型：DEFERRED（延迟）、IMMEDIATE（立即写锁）、EXCLUSIVE（独占）。
- **关键要点**：写事务用 BEGIN IMMEDIATE 避免死锁；并发场景设置 timeout 和重试机制。
- **常见误区**：默认 ROLLBACK 模式下读写互斥；长事务持锁导致阻塞；WAL 模式用于网络盘导致损坏。

---

## 第 12 讲：视图、触发器与 CTE

### 概念

本讲讲解 SQLite 的三个高级特性：

**1. 视图（View）**：基于查询的虚拟表，不存储数据，只存储查询定义。视图简化查询、提供逻辑独立性和安全控制。

**2. 触发器（Trigger）**：与表事件（INSERT/UPDATE/DELETE）关联的特殊程序，在事件发生时自动执行。SQLite 支持 BEFORE/AFTER 和 FOR EACH ROW 触发器。

**3. CTE（Common Table Expression，公用表表达式）**：用 WITH 子句定义的临时结果集，可提高复杂查询的可读性。SQLite 支持**递归 CTE**，能处理树形/图结构遍历。

### 原理

**1. 视图的原理**

视图是一种"查询快捷方式"。查询视图时，SQLite 将视图定义与用户查询合并，重写为对基表的查询后执行。视图本身不占存储空间。

SQLite 的视图限制：

- 默认只读，不能直接 INSERT/UPDATE/DELETE。
- 通过 INSTEAD OF 触发器可实现可更新视图。
- 视图可以引用其他视图（嵌套）。

**2. 触发器的原理**

SQLite 的触发器特点：

- 只支持**行级触发器**（FOR EACH ROW），不支持语句级。
- 支持 BEFORE 和 AFTER 时机。
- 支持 INSERT、UPDATE、DELETE 事件，可用 `UPDATE OF 列名` 限定列。
- 触发器体内可访问 NEW（新值）和 OLD（旧值）引用。
- 触发器可用于实现审计日志、级联操作、复杂约束、可更新视图。

**3. 递归 CTE 原理**

递归 CTE 由两部分组成：

- **基础查询（Base Case）**：初始结果集。
- **递归查询（Recursive Part）**：引用 CTE 自身，逐步扩展结果。

执行流程：

1. 执行基础查询，得到初始结果。
2. 用初始结果执行递归查询，得到新行。
3. 用新行重复执行递归查询，直到无新行产生。
4. 合并所有结果。

递归 CTE 适合处理树形结构（组织架构、评论嵌套）、图遍历、生成序列等。

### 例子

**1. 视图**

```sql
-- 创建视图：计算机系学生
CREATE VIEW cs_students AS
SELECT id, name, grade FROM students WHERE department = '计算机';

-- 使用视图
SELECT * FROM cs_students WHERE grade > 80;

-- 创建视图：学生选课统计
CREATE VIEW student_stats AS
SELECT
    s.id,
    s.name,
    COUNT(e.course_code) AS course_count,
    AVG(e.score) AS avg_score
FROM students s
LEFT JOIN enrollments e ON s.id = e.student_id
GROUP BY s.id, s.name;

-- 使用视图
SELECT * FROM student_stats WHERE avg_score >= 80 ORDER BY avg_score DESC;

-- 查看视图定义
SELECT sql FROM sqlite_master WHERE type = 'view' AND name = 'cs_students';

-- 删除视图
DROP VIEW IF EXISTS cs_students;
```

**2. 触发器**

```sql
-- BEFORE INSERT 触发器：数据校验
CREATE TRIGGER trg_check_age
BEFORE INSERT ON students
FOR EACH ROW
WHEN NEW.age < 0 OR NEW.age > 150
BEGIN
    SELECT RAISE(ABORT, '年龄必须在0-150之间');
END;

-- AFTER UPDATE 触发器：审计日志
CREATE TABLE audit_log (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    table_name TEXT,
    operation TEXT,
    old_value TEXT,
    new_value TEXT,
    changed_at TEXT DEFAULT (datetime('now'))
);

CREATE TRIGGER trg_audit_student
AFTER UPDATE ON students
FOR EACH ROW
BEGIN
    INSERT INTO audit_log (table_name, operation, old_value, new_value)
    VALUES (
        'students',
        'UPDATE',
        'id=' || OLD.id || ',name=' || OLD.name || ',grade=' || OLD.grade,
        'id=' || NEW.id || ',name=' || NEW.name || ',grade=' || NEW.grade
    );
END;

-- UPDATE OF 限定列的触发器
CREATE TRIGGER trg_grade_changed
AFTER UPDATE OF grade ON students
FOR EACH ROW
WHEN OLD.grade != NEW.grade
BEGIN
    INSERT INTO grade_history (student_id, old_grade, new_grade, changed_at)
    VALUES (NEW.id, OLD.grade, NEW.grade, datetime('now'));
END;

-- 触发器实现自动更新时间戳
CREATE TRIGGER trg_update_timestamp
AFTER UPDATE ON students
FOR EACH ROW
BEGIN
    UPDATE students SET updated_at = datetime('now') WHERE id = NEW.id;
END;
```

**3. INSTEAD OF 触发器实现可更新视图**

```sql
-- 多表视图（通常不可更新）
CREATE VIEW student_course_view AS
SELECT
    s.id AS student_id,
    s.name AS student_name,
    c.code AS course_code,
    c.title AS course_title,
    e.score
FROM students s
JOIN enrollments e ON s.id = e.student_id
JOIN courses c ON e.course_code = c.code;

-- INSTEAD OF触发器：通过视图插入
CREATE TRIGGER trg_insert_enrollment
INSTEAD OF INSERT ON student_course_view
FOR EACH ROW
BEGIN
    INSERT OR IGNORE INTO students (id, name) VALUES (NEW.student_id, NEW.student_name);
    INSERT OR IGNORE INTO courses (code, title) VALUES (NEW.course_code, NEW.course_title);
    INSERT OR REPLACE INTO enrollments (student_id, course_code, score)
    VALUES (NEW.student_id, NEW.course_code, NEW.score);
END;

-- 现在可以通过视图插入
INSERT INTO student_course_view (student_id, student_name, course_code, course_title, score)
VALUES (1, '张三', 'CS101', '数据库', 90);
```

**4. 非递归 CTE**

```sql
-- 用CTE简化复杂查询
WITH
    high_scorers AS (
        SELECT student_id, AVG(score) AS avg_score
        FROM enrollments
        GROUP BY student_id
        HAVING AVG(score) >= 85
    ),
    cs_students AS (
        SELECT id, name FROM students WHERE department = '计算机'
    )
SELECT
    cs.name,
    hs.avg_score
FROM cs_students cs
JOIN high_scorers hs ON cs.id = hs.student_id
ORDER BY hs.avg_score DESC;

-- CTE vs 子查询：CTE更清晰
-- 等价的子查询写法（更难读）
SELECT cs.name, hs.avg_score
FROM (SELECT id, name FROM students WHERE department = '计算机') cs
JOIN (SELECT student_id, AVG(score) AS avg_score FROM enrollments
      GROUP BY student_id HAVING AVG(score) >= 85) hs
ON cs.id = hs.student_id;
```

**5. 递归 CTE：组织架构遍历**

```sql
-- 组织架构表（自引用）
CREATE TABLE org (
    id INTEGER PRIMARY KEY,
    name TEXT,
    parent_id INTEGER,
    FOREIGN KEY (parent_id) REFERENCES org(id)
);

INSERT INTO org VALUES
    (1, 'CEO', NULL),
    (2, 'CTO', 1),
    (3, 'CFO', 1),
    (4, '工程经理', 2),
    (5, '前端组长', 4),
    (6, '后端组长', 4),
    (7, '财务经理', 3);

-- 递归查询：从CEO开始遍历整个组织树
WITH RECURSIVE org_tree AS (
    -- 基础查询：根节点
    SELECT id, name, parent_id, 0 AS level, name AS path
    FROM org
    WHERE parent_id IS NULL

    UNION ALL

    -- 递归查询：子节点
    SELECT
        o.id,
        o.name,
        o.parent_id,
        ot.level + 1,
        ot.path || ' > ' || o.name
    FROM org o
    JOIN org_tree ot ON o.parent_id = ot.id
)
SELECT level, path FROM org_tree ORDER BY path;
-- 0|CEO
-- 1|CEO > CFO
-- 2|CEO > CFO > 财务经理
-- 1|CEO > CTO
-- 2|CEO > CTO > 工程经理
-- 3|CEO > CTO > 工程经理 > 前端组长
-- 3|CEO > CTO > 工程经理 > 后端组长
```

**6. 递归 CTE：生成序列**

```sql
-- 生成1到10的序列
WITH RECURSIVE numbers(n) AS (
    SELECT 1
    UNION ALL
    SELECT n + 1 FROM numbers WHERE n < 10
)
SELECT n FROM numbers;

-- 生成日期序列
WITH RECURSIVE dates(d) AS (
    SELECT date('2024-01-01')
    UNION ALL
    SELECT date(d, '+1 day') FROM dates WHERE d < '2024-01-31'
)
SELECT d FROM dates;
```

### 总结

- 视图是虚拟表，简化查询、提供安全控制；SQLite 视图默认只读，用 INSTEAD OF 触发器实现可更新。
- 触发器只支持行级，支持 BEFORE/AFTER；NEW 和 OLD 引用访问新旧值；RAISE 函数抛出错误。
- CTE 用 WITH 定义临时结果集，提高可读性；递归 CTE 处理树形/图结构遍历。
- **关键要点**：递归 CTE 由基础查询 + 递归查询组成，用 UNION ALL 连接；触发器注意避免递归触发。
- **常见误区**：在视图上直接 INSERT 导致失败；递归 CTE 忘记终止条件导致无限循环；触发器中执行耗时操作。

---

## 第 13 讲：内置函数与表达式

### 概念

SQLite 提供丰富的内置函数，分为以下几类：

- **聚合函数**：COUNT、SUM、AVG、MIN、MAX、GROUP_CONCAT 等。
- **标量函数**：处理单个值返回单个值，如数学、字符串、日期函数。
- **窗口函数**：ROW_NUMBER、RANK、LAG、LEAD 等（第 7 讲已介绍）。
- **JSON 函数**：SQLite 3.9+ 内置 JSON 支持，json_extract、json_array 等。
- **日期时间函数**：date、time、datetime、strftime、julianday 等。

本讲重点讲解 SQLite 特色函数：JSON 函数、日期函数、字符串函数和特殊表达式。

### 原理

**1. SQLite 函数的特点**

- SQLite 函数在 SQL 语句中直接调用，返回标量值或聚合结果。
- 用户可通过 C API 创建自定义函数（Application-defined Functions），扩展 SQLite 能力。
- SQLite 的 JSON 函数是内置的（3.9+），无需扩展，这是与其他数据库的区别。

**2. JSON 支持**

SQLite 将 JSON 存储为 TEXT，但提供函数解析和操作 JSON：

- `json_extract(json, path)`：提取 JSON 路径的值。
- `json_array(...)`：创建 JSON 数组。
- `json_object(...)`：创建 JSON 对象。
- `json_insert/json_replace/json_set`：修改 JSON。
- `json_each/json_tree`：遍历 JSON（表值函数）。

JSON 路径用 `$.key` 或 `$[index]` 语法。

**3. 日期时间函数**

SQLite 的日期函数基于三个内部表示：

- **TEXT**：ISO8601 字符串（'YYYY-MM-DD HH:MM:SS'）。
- **INTEGER**：Unix 时间戳（秒）。
- **REAL**：Julian 日（浮点数）。

所有日期函数接受这三种格式，并支持修饰符（'+1 day'、'start of month' 等）。

### 例子

**1. 聚合函数**

```sql
-- 基本聚合
SELECT
    department,
    COUNT(*) AS 人数,
    AVG(grade) AS 平均分,
    MIN(grade) AS 最低分,
    MAX(grade) AS 最高分,
    SUM(grade) AS 总分
FROM students
GROUP BY department;

-- GROUP_CONCAT：拼接字符串
SELECT
    department,
    GROUP_CONCAT(name, ', ') AS 学生名单
FROM students
GROUP BY department;

-- 带DISTINCT的聚合
SELECT COUNT(DISTINCT department) FROM students;

-- 聚合与CASE结合
SELECT
    department,
    COUNT(CASE WHEN grade >= 90 THEN 1 END) AS 优秀数,
    COUNT(CASE WHEN grade >= 60 AND grade < 90 THEN 1 END) AS 及格数,
    COUNT(CASE WHEN grade < 60 THEN 1 END) AS 不及格数
FROM students
GROUP BY department;
```

**2. 字符串函数**

```sql
-- 基本字符串函数
SELECT
    length('Hello') AS 长度,           -- 5
    upper('hello') AS 大写,            -- HELLO
    lower('HELLO') AS 小写,            -- hello
    substr('Hello World', 7, 5) AS 子串, -- World
    trim('  hello  ') AS 去空格,        -- hello
    replace('Hello World', 'World', 'SQLite') AS 替换, -- Hello SQLite
    instr('Hello World', 'World') AS 位置; -- 7

-- 字符串拼接（||操作符）
SELECT '姓名: ' || name || ', 年龄: ' || age FROM students;

-- printf格式化
SELECT printf('%.2f', 3.14159) AS 格式化;  -- 3.14
SELECT printf('%05d', 42) AS 补零;          -- 00042

-- QUOTE：转义为SQL字面量
SELECT quote('It''s a test');  -- 'It''s a test'
```

**3. 数学函数**

```sql
-- 基本数学函数
SELECT
    abs(-5) AS 绝对值,        -- 5
    round(3.14159, 2) AS 四舍五入, -- 3.14
    ceil(3.2) AS 向上取整,    -- 4
    floor(3.8) AS 向下取整,   -- 3
    power(2, 10) AS 幂,       -- 1024
    sqrt(16) AS 平方根,       -- 4
    random() AS 随机数,       -- 随机整数
    randomblob(16) AS 随机BLOB; -- 16字节随机二进制

-- 取模和整除
SELECT 17 % 5 AS 取模,        -- 2
       17 / 5 AS 整除;        -- 3（整数除法）

-- 类型转换
SELECT CAST('42' AS INTEGER) AS 转整数,
       CAST(42 AS TEXT) AS 转文本,
       CAST(3.14 AS INTEGER) AS 浮点转整数; -- 3
```

**4. 日期时间函数**

```sql
-- 当前日期时间
SELECT
    date('now') AS 今天,              -- 2024-01-15
    time('now') AS 当前时间,          -- 10:30:00
    datetime('now') AS 日期时间,      -- 2024-01-15 10:30:00
    strftime('%Y年%m月%d日', 'now') AS 中文格式; -- 2024年01月15日

-- 日期运算
SELECT
    date('now', '+1 day') AS 明天,
    date('now', '-1 month') AS 上月,
    date('now', '+1 year') AS 明年,
    datetime('now', '+2 hours') AS 两小时后;

-- 日期截断
SELECT
    date('now', 'start of month') AS 月初,
    date('now', 'start of year') AS 年初,
    date('now', 'weekday 1') AS 下周一;

-- 日期差
SELECT
    julianday('2024-12-31') - julianday('2024-01-01') AS 年内天数, -- 365
    (julianday('now') - julianday('2000-01-01')) * 24 * 60 AS 距2000年分钟数;

-- Unix时间戳转换
SELECT
    strftime('%s', 'now') AS 当前时间戳,           -- 1705312200
    datetime(1705312200, 'unixepoch') AS 时间戳转日期, -- 2024-01-15 10:30:00
    datetime(1705312200, 'unixepoch', 'localtime') AS 本地时间;

-- 实际应用：按月统计
SELECT
    strftime('%Y-%m', created_at) AS 月份,
    COUNT(*) AS 订单数,
    SUM(amount) AS 总额
FROM orders
GROUP BY strftime('%Y-%m', created_at)
ORDER BY 月份;
```

**5. JSON 函数**

```sql
-- 创建JSON列
CREATE TABLE config (
    id INTEGER PRIMARY KEY,
    data TEXT  -- 存储JSON字符串
);

-- 插入JSON数据
INSERT INTO config (data) VALUES
    ('{"name": "张三", "age": 20, "hobbies": ["读书", "游泳"], "address": {"city": "北京"}}'),
    ('{"name": "李四", "age": 25, "hobbies": ["电影"], "address": {"city": "上海"}}');

-- 提取JSON值
SELECT
    json_extract(data, '$.name') AS 姓名,
    json_extract(data, '$.age') AS 年龄,
    json_extract(data, '$.address.city') AS 城市,
    json_extract(data, '$.hobbies[0]') AS 第一爱好
FROM config;

-- JSON判断和类型
SELECT
    json_type(data) AS 顶层类型,           -- object
    json_type(data, '$.hobbies') AS 爱好类型, -- array
    json_array_length(data, '$.hobbies') AS 爱好数量,
    json_valid(data) AS 是否合法JSON;       -- 1

-- 修改JSON
UPDATE config
SET data = json_set(data, '$.age', 21)
WHERE id = 1;

UPDATE config
SET data = json_insert(data, '$.email', 'zhangsan@example.com')
WHERE id = 1;

-- json_each遍历数组（表值函数）
SELECT
    config.id,
    je.value AS 爱好
FROM config, json_each(config.data, '$.hobbies') je;

-- 创建JSON
SELECT
    json_object('name', '王五', 'age', 30) AS 对象,
    json_array(1, 2, 3, 'four') AS 数组,
    json_quote('需要转义的"字符串"') AS 转义;
```

**6. 条件表达式**

```sql
-- CASE WHEN
SELECT
    name,
    grade,
    CASE
        WHEN grade >= 90 THEN '优秀'
        WHEN grade >= 80 THEN '良好'
        WHEN grade >= 70 THEN '中等'
        WHEN grade >= 60 THEN '及格'
        ELSE '不及格'
    END AS 等级
FROM students;

-- IIF函数（简化三元运算）
SELECT name, IIF(grade >= 60, '及格', '不及格') AS 状态 FROM students;

-- COALESCE和IFNULL
SELECT
    name,
    COALESCE(email, phone, '无联系方式') AS 联系方式
FROM students;

-- NULLIF
SELECT NULLIF(grade, 0) FROM students;  -- grade为0则返回NULL
```

### 总结

- SQLite 提供丰富的内置函数：聚合、字符串、数学、日期、JSON 等。
- JSON 函数是 SQLite 的特色（3.9+ 内置），支持提取、修改、遍历 JSON。
- 日期函数基于 TEXT/INTEGER/REAL 三种表示，支持修饰符进行日期运算。
- CASE WHEN、IIF、COALESCE、NULLIF 是处理条件逻辑的利器。
- **关键要点**：GROUP_CONCAT 拼接字符串；strftime 格式化日期；json_extract 提取 JSON 值。
- **常见误区**：日期运算忘记用 julianday 计算差值；JSON 修改用 json_set 而非字符串拼接；混淆 IFNULL 和 NULLIF。

---

# 第 5 章 高级应用与实战

本章聚焦 SQLite 的实战应用。从性能调优的方法论到多语言集成和完整案例，帮助您将所学知识融会贯通，在实际项目中高效使用 SQLite。

---

## 第 14 讲：性能调优与最佳实践

### 概念

**性能调优** 是通过分析、诊断和优化，使 SQLite 在响应时间、吞吐量、资源利用率等指标上达到最优的过程。SQLite 的调优与客户端/服务器数据库有所不同，重点关注 PRAGMA 配置、索引设计、批量操作和数据库文件管理。

SQLite 性能调优的层次：

1. **SQL 与应用层**：优化 SQL 语句、批量操作、参数绑定。
2. **Schema 层**：表结构、索引、WITHOUT ROWID、数据类型。
3. **PRAGMA 配置层**：WAL 模式、缓存大小、同步级别、页大小。
4. **文件与系统层**：文件系统选择、磁盘 I/O、内存盘。

### 原理

**1. PRAGMA 关键配置**

- **journal_mode = WAL**：启用 WAL 模式，提升并发和写入性能。
- **synchronous**：同步级别，控制数据落盘时机。
  - `FULL`（默认）：每次事务同步等待，最安全最慢。
  - `NORMAL`：WAL 模式下足够安全，性能更好。
  - `OFF`：不同步，最快但崩溃可能丢数据。
- **cache_size**：页缓存大小，负数为 KB，正数为页数。
- **page_size**：页大小，默认 4096，大页适合大数据库。
- **temp_store = MEMORY**：临时表和排序用内存，避免磁盘 I/O。
- **mmap_size**：内存映射 I/O 大小，大数据库可提升读取。

**2. 批量操作优化**

SQLite 默认每个 SQL 语句是一个隐式事务，频繁的单条插入会产生大量事务开销。将多条操作包裹在显式事务中，可提升数十倍性能。

**3. 预处理语句**

预处理语句（Prepared Statement）编译一次 SQL，多次执行，避免重复解析和优化。适合循环执行相同 SQL 的场景。

**4. VACUUM 与碎片整理**

SQLite 删除数据后不立即回收空间，导致数据库文件可能大于实际数据。VACUUM 重建数据库文件，回收空间并整理碎片。

### 例子

**1. PRAGMA 调优配置**

```sql
-- 推荐的生产环境配置（连接时执行）
PRAGMA journal_mode = WAL;           -- WAL模式，读写并发
PRAGMA synchronous = NORMAL;         -- WAL下足够安全，性能好
PRAGMA cache_size = -64000;          -- 64MB缓存
PRAGMA temp_store = MEMORY;          -- 临时表用内存
PRAGMA mmap_size = 268435456;        -- 256MB内存映射
PRAGMA foreign_keys = ON;            -- 启用外键
PRAGMA busy_timeout = 5000;          -- 锁等待5秒

-- 查看当前配置
PRAGMA journal_mode;
PRAGMA synchronous;
PRAGMA cache_size;
```

**2. 批量插入性能对比**

```python
import sqlite3
import time

conn = sqlite3.connect(':memory:')
conn.execute('CREATE TABLE test (id INTEGER, name TEXT)')

# 方式1：无事务，逐条插入（慢）
start = time.time()
for i in range(10000):
    conn.execute('INSERT INTO test VALUES (?, ?)', (i, f'name{i}'))
conn.commit()
print(f'无事务: {time.time() - start:.2f}s')  # 约5-10秒

# 方式2：显式事务（快）
conn.execute('DELETE FROM test')
start = time.time()
conn.execute('BEGIN')
for i in range(10000):
    conn.execute('INSERT INTO test VALUES (?, ?)', (i, f'name{i}'))
conn.commit()
print(f'显式事务: {time.time() - start:.2f}s')  # 约0.1-0.5秒

# 方式3：executemany（最快）
conn.execute('DELETE FROM test')
start = time.time()
data = [(i, f'name{i}') for i in range(10000)]
conn.executemany('INSERT INTO test VALUES (?, ?)', data)
print(f'executemany: {time.time() - start:.2f}s')  # 约0.05-0.2秒

conn.close()
```

**3. 预处理语句复用**

```python
import sqlite3

conn = sqlite3.connect('test.db')

# 预处理语句（编译一次，多次执行）
stmt = conn.prepare('INSERT INTO test VALUES (?, ?)') if hasattr(conn, 'prepare') else None

# Python的sqlite3模块自动缓存预处理语句
# 同一SQL字符串只编译一次
for i in range(1000):
    conn.execute('INSERT INTO test VALUES (?, ?)', (i, f'name{i}'))  # 自动复用

conn.commit()
conn.close()
```

**4. 索引优化实战**

```sql
-- 场景：订单表查询慢
CREATE TABLE orders (
    id INTEGER PRIMARY KEY,
    user_id INTEGER,
    status TEXT,
    amount REAL,
    created_at TEXT
);

-- 查询1：按用户查订单
EXPLAIN QUERY PLAN SELECT * FROM orders WHERE user_id = 100;
-- SCAN TABLE orders（全表扫描）

-- 优化：建索引
CREATE INDEX idx_user ON orders(user_id);
-- SEARCH TABLE orders USING INDEX idx_user (user_id=?)

-- 查询2：按状态和时间查
EXPLAIN QUERY PLAN
SELECT * FROM orders WHERE status = 'pending' ORDER BY created_at DESC;
-- SCAN TABLE orders + USING FILESORT

-- 优化：复合索引（等值列在前，排序列在后）
CREATE INDEX idx_status_created ON orders(status, created_at DESC);
-- SEARCH TABLE orders USING INDEX idx_status_created (status=?)

-- 查询3：深分页优化
-- 慢：OFFSET扫描跳过的行
SELECT * FROM orders ORDER BY id LIMIT 20 OFFSET 100000;

-- 快：用WHERE条件定位
SELECT * FROM orders WHERE id > 100020 ORDER BY id LIMIT 20;
-- 或用覆盖索引+JOIN
SELECT o.* FROM orders o
JOIN (SELECT id FROM orders ORDER BY id LIMIT 20 OFFSET 100000) t
ON o.id = t.id;
```

**5. VACUUM 碎片整理**

```sql
-- 查看数据库文件大小和实际数据大小
PRAGMA page_count;  -- 总页数
PRAGMA page_size;   -- 页大小
-- 文件大小 = page_count * page_size

-- 删除大量数据后
DELETE FROM logs WHERE created_at < '2023-01-01';

-- 查看空闲页
PRAGMA freelist_count;

-- VACUUM：重建数据库，回收空间
VACUUM;

-- 增量VACUUM（需auto_vacuum开启）
PRAGMA auto_vacuum = INCREMENTAL;  -- 建库时设置
PRAGMA incremental_vacuum(100);    -- 每次回收100页

-- VACUUM的注意事项：
-- 1. 需要额外空间（临时复制整个数据库）
-- 2. 执行期间锁定数据库
-- 3. 定期执行（如每月一次）而非频繁执行
```

**6. WITHOUT ROWID 优化**

```sql
-- 场景：键值存储，主键是TEXT，按主键查询为主
-- 普通表：数据存rowid B-tree，主键另建索引，查询需回表
CREATE TABLE kv_normal (key TEXT PRIMARY KEY, value BLOB);
-- 存储：rowid B-tree（数据）+ 主键索引B-tree

-- WITHOUT ROWID表：主键即存储键，无rowid，无回表
CREATE TABLE kv_fast (key TEXT PRIMARY KEY, value BLOB) WITHOUT ROWID;
-- 存储：主键B-tree（数据直接存叶子节点）

-- 性能对比（10万条数据）
-- 插入：WITHOUT ROWID略慢（需维护主键顺序）
-- 按主键查询：WITHOUT ROWID快约30%（无回表）
-- 范围查询：WITHOUT ROWID快约50%

-- 适合WITHOUT ROWID的场景：
-- 1. 主键是多列复合键
-- 2. 按主键查询为主
-- 3. 不需要rowid
-- 4. 表数据量较大
```

### 总结

- PRAGMA 配置是 SQLite 调优的核心：WAL 模式、synchronous=NORMAL、cache_size、temp_store=MEMORY。
- 批量操作用显式事务或 executemany，性能提升数十倍。
- 索引优化：复合索引遵循最左前缀，覆盖索引避免回表，深分页用 WHERE 条件定位。
- VACUUM 回收空间和整理碎片，定期执行而非频繁执行。
- **关键要点**：WITHOUT ROWID 适合主键查询为主的场景；PRAGMA 配置在连接时设置。
- **常见误区**：忽视 WAL 模式导致并发问题；逐条插入不用事务；过度 VACUUM 影响性能。

---

## 第 15 讲：应用集成与实战案例

### 概念

SQLite 的最大优势在于与应用的紧密集成。本讲讲解 SQLite 在不同编程语言中的使用方法，并通过完整实战案例展示 SQLite 的实际应用。

**SQLite 的集成方式**：

- **C/C++**：直接链接 SQLite 库，使用 C API。
- **Python**：标准库 `sqlite3` 模块，开箱即用。
- **Java**：JDBC 驱动（如 sqlite-jdbc）。
- **Node.js**：`better-sqlite3` 或 `sqlite3` 包。
- **Go**：`mattn/go-sqlite3` 驱动。
- **Rust**：`rusqlite` crate。
- **C#**：`Microsoft.Data.Sqlite` 或 `System.Data.SQLite`。

**SQLite 的典型应用场景**：

- **移动应用**：iOS（Core Data 底层）、Android（默认数据库）。
- **桌面应用**：浏览器（Chrome、Firefox）、邮件客户端、办公软件。
- **嵌入式设备**：IoT 设备、智能家居、车载系统。
- **数据分析**：替代 CSV，用 SQL 分析数据。
- **应用配置**：存储应用配置和用户偏好。
- **测试替身**：单元测试中替代生产数据库。
- **原型开发**：快速验证想法，无需搭建数据库服务器。

### 原理

**1. SQLite 的 C API 架构**

SQLite 的核心是 C API，其他语言的绑定都基于此。关键 API：

- `sqlite3_open()`：打开数据库连接。
- `sqlite3_prepare_v2()`：预处理 SQL 语句。
- `sqlite3_bind_*()`：绑定参数。
- `sqlite3_step()`：执行一步（获取一行）。
- `sqlite3_column_*()`：获取列值。
- `sqlite3_finalize()`：释放预处理语句。
- `sqlite3_close()`：关闭连接。

预处理语句的生命周期：prepare → bind → step → (reset → bind → step)* → finalize。

**2. 线程安全**

SQLite 支持两种线程模式：

- **单线程模式**：编译时指定，不支持多线程。
- **多线程模式**（默认）：连接对象不能跨线程共享，但不同线程可用不同连接。
- **串行化模式**：连接对象可跨线程共享，内部加锁保证安全。

Python 的 sqlite3 模块默认禁止跨线程共享连接（`check_same_thread=True`）。

**3. 内存数据库与临时数据库**

- `:memory:`：内存数据库，连接关闭即消失，适合测试和临时计算。
- `''`（空字符串）：临时文件数据库，连接关闭自动删除。
- 普通路径：持久化文件数据库。

### 例子

**1. Python 集成（完整 CRUD）**

```python
import sqlite3
from contextlib import contextmanager

# 连接管理器
@contextmanager
def get_connection(db_path='app.db'):
    conn = sqlite3.connect(db_path)
    conn.row_factory = sqlite3.Row  # 返回字典式行
    conn.execute('PRAGMA journal_mode = WAL')
    conn.execute('PRAGMA foreign_keys = ON')
    try:
        yield conn
        conn.commit()
    except Exception:
        conn.rollback()
        raise
    finally:
        conn.close()

# 初始化数据库
def init_db():
    with get_connection() as conn:
        conn.executescript('''
            CREATE TABLE IF NOT EXISTS users (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                username TEXT UNIQUE NOT NULL,
                email TEXT UNIQUE NOT NULL,
                created_at TEXT DEFAULT (datetime('now'))
            );

            CREATE TABLE IF NOT EXISTS posts (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                user_id INTEGER NOT NULL,
                title TEXT NOT NULL,
                content TEXT,
                published INTEGER DEFAULT 0,
                created_at TEXT DEFAULT (datetime('now')),
                FOREIGN KEY (user_id) REFERENCES users(id)
            );

            CREATE INDEX IF NOT EXISTS idx_posts_user ON posts(user_id);
            CREATE INDEX IF NOT EXISTS idx_posts_published ON posts(published);
        ''')

# CRUD操作
def create_user(username, email):
    with get_connection() as conn:
        cursor = conn.execute(
            'INSERT INTO users (username, email) VALUES (?, ?)',
            (username, email)
        )
        return cursor.lastrowid

def get_user(user_id):
    with get_connection() as conn:
        row = conn.execute(
            'SELECT * FROM users WHERE id = ?', (user_id,)
        ).fetchone()
        return dict(row) if row else None

def get_users_with_posts():
    with get_connection() as conn:
        rows = conn.execute('''
            SELECT
                u.id, u.username, u.email,
                COUNT(p.id) AS post_count,
                MAX(p.created_at) AS last_post
            FROM users u
            LEFT JOIN posts p ON u.id = p.user_id
            GROUP BY u.id, u.username, u.email
            ORDER BY post_count DESC
        ''').fetchall()
        return [dict(row) for row in rows]

def update_user(user_id, **fields):
    with get_connection() as conn:
        set_clause = ', '.join(f'{k} = ?' for k in fields)
        values = list(fields.values()) + [user_id]
        conn.execute(f'UPDATE users SET {set_clause} WHERE id = ?', values)

def delete_user(user_id):
    with get_connection() as conn:
        conn.execute('DELETE FROM users WHERE id = ?', (user_id,))

# 使用
init_db()
uid = create_user('张三', 'zhangsan@example.com')
print(get_user(uid))
```

**2. Node.js 集成（better-sqlite3）**

```javascript
const Database = require('better-sqlite3');

// 同步API，性能优秀
const db = new Database('app.db');
db.pragma('journal_mode = WAL');

// 创建表
db.exec(`
    CREATE TABLE IF NOT EXISTS products (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        name TEXT NOT NULL,
        price REAL NOT NULL,
        stock INTEGER DEFAULT 0
    )
`);

// 预处理语句（编译一次，多次执行）
const insertStmt = db.prepare('INSERT INTO products (name, price, stock) VALUES (?, ?, ?)');
const selectStmt = db.prepare('SELECT * FROM products WHERE price > ? ORDER BY price DESC');
const updateStmt = db.prepare('UPDATE products SET stock = ? WHERE id = ?');

// 批量插入（事务）
const insertMany = db.transaction((items) => {
    for (const item of items) {
        insertStmt.run(item.name, item.price, item.stock);
    }
});

insertMany([
    { name: '笔记本', price: 5999, stock: 10 },
    { name: '手机', price: 3999, stock: 20 },
    { name: '平板', price: 2999, stock: 15 }
]);

// 查询
const expensive = selectStmt.all(3000);
console.log(expensive);

// 更新
updateStmt.run(8, 1);

db.close();
```

**3. 实战案例：个人记账应用**

```python
import sqlite3
from datetime import datetime

class ExpenseTracker:
    def __init__(self, db_path='expenses.db'):
        self.conn = sqlite3.connect(db_path)
        self.conn.row_factory = sqlite3.Row
        self.conn.execute('PRAGMA journal_mode = WAL')
        self.conn.execute('PRAGMA foreign_keys = ON')
        self._init_db()

    def _init_db(self):
        self.conn.executescript('''
            CREATE TABLE IF NOT EXISTS categories (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                name TEXT UNIQUE NOT NULL,
                type TEXT CHECK (type IN ('income', 'expense'))
            );

            CREATE TABLE IF NOT EXISTS transactions (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                amount REAL NOT NULL CHECK (amount > 0),
                category_id INTEGER NOT NULL,
                description TEXT,
                transaction_date TEXT NOT NULL,
                created_at TEXT DEFAULT (datetime('now')),
                FOREIGN KEY (category_id) REFERENCES categories(id)
            );

            CREATE INDEX IF NOT EXISTS idx_trans_date
                ON transactions(transaction_date);
            CREATE INDEX IF NOT EXISTS idx_trans_category
                ON transactions(category_id);

            -- 初始化默认分类
            INSERT OR IGNORE INTO categories (name, type) VALUES
                ('工资', 'income'), ('奖金', 'income'),
                ('餐饮', 'expense'), ('交通', 'expense'),
                ('购物', 'expense'), ('娱乐', 'expense');
        ''')
        self.conn.commit()

    def add_transaction(self, amount, category_name, description='', date=None):
        date = date or datetime.now().strftime('%Y-%m-%d')
        cursor = self.conn.execute('''
            INSERT INTO transactions (amount, category_id, description, transaction_date)
            VALUES (?, (SELECT id FROM categories WHERE name = ?), ?, ?)
        ''', (amount, category_name, description, date))
        self.conn.commit()
        return cursor.lastrowid

    def get_monthly_summary(self, year, month):
        """月度收支汇总"""
        rows = self.conn.execute('''
            SELECT
                c.type,
                c.name AS category,
                SUM(t.amount) AS total,
                COUNT(*) AS count
            FROM transactions t
            JOIN categories c ON t.category_id = c.id
            WHERE strftime('%Y-%m', t.transaction_date) = ?
            GROUP BY c.type, c.name
            ORDER BY c.type, total DESC
        ''', (f'{year:04d}-{month:02d}',)).fetchall()

        income = sum(r['total'] for r in rows if r['type'] == 'income')
        expense = sum(r['total'] for r in rows if r['type'] == 'expense')

        return {
            'details': [dict(r) for r in rows],
            'total_income': income,
            'total_expense': expense,
            'balance': income - expense
        }

    def get_daily_trend(self, year, month):
        """每日支出趋势"""
        rows = self.conn.execute('''
            SELECT
                transaction_date AS date,
                SUM(CASE WHEN c.type = 'expense' THEN t.amount ELSE 0 END) AS expense,
                SUM(CASE WHEN c.type = 'income' THEN t.amount ELSE 0 END) AS income
            FROM transactions t
            JOIN categories c ON t.category_id = c.id
            WHERE strftime('%Y-%m', t.transaction_date) = ?
            GROUP BY transaction_date
            ORDER BY transaction_date
        ''', (f'{year:04d}-{month:02d}',)).fetchall()
        return [dict(r) for r in rows]

    def close(self):
        self.conn.close()

# 使用示例
tracker = ExpenseTracker()

# 记账
tracker.add_transaction(8000, '工资', '1月工资', '2024-01-05')
tracker.add_transaction(35.5, '餐饮', '午餐', '2024-01-15')
tracker.add_transaction(200, '交通', '打车', '2024-01-15')
tracker.add_transaction(599, '购物', '买书', '2024-01-16')

# 月度汇总
summary = tracker.get_monthly_summary(2024, 1)
print(f"收入: {summary['total_income']}")
print(f"支出: {summary['total_expense']}")
print(f"结余: {summary['balance']}")

# 每日趋势
trend = tracker.get_daily_trend(2024, 1)
for day in trend:
    print(f"{day['date']}: 支出{day['expense']}, 收入{day['income']}")

tracker.close()
```

**4. 内存数据库用于测试**

```python
import sqlite3
import unittest

class UserModelTest(unittest.TestCase):
    def setUp(self):
        # 每个测试用内存数据库，隔离且快速
        self.conn = sqlite3.connect(':memory:')
        self.conn.executescript('''
            CREATE TABLE users (id INTEGER PRIMARY KEY, name TEXT, email TEXT);
        ''')

    def tearDown(self):
        self.conn.close()

    def test_create_user(self):
        self.conn.execute(
            'INSERT INTO users (name, email) VALUES (?, ?)',
            ('张三', 'zhangsan@example.com')
        )
        count = self.conn.execute('SELECT COUNT(*) FROM users').fetchone()[0]
        self.assertEqual(count, 1)

    def test_duplicate_email(self):
        self.conn.execute('INSERT INTO users (email) VALUES (?)', ('a@b.com',))
        # 测试唯一约束（需建表时加UNIQUE）
        # ...

if __name__ == '__main__':
    unittest.main()
```

**5. 数据库备份与迁移**

```python
import sqlite3

def backup_database(src_path, dest_path):
    """使用SQLite内置备份API（在线备份）"""
    src = sqlite3.connect(src_path)
    dest = sqlite3.connect(dest_path)
    src.backup(dest)
    dest.close()
    src.close()

def export_to_sql(db_path, sql_path):
    """导出为SQL脚本"""
    conn = sqlite3.connect(db_path)
    with open(sql_path, 'w', encoding='utf-8') as f:
        for line in conn.iterdump():
            f.write(line + '\n')
    conn.close()

def import_from_sql(sql_path, db_path):
    """从SQL脚本导入"""
    conn = sqlite3.connect(db_path)
    with open(sql_path, 'r', encoding='utf-8') as f:
        conn.executescript(f.read())
    conn.commit()
    conn.close()

# 使用
backup_database('app.db', 'app_backup.db')
export_to_sql('app.db', 'app_dump.sql')
import_from_sql('app_dump.sql', 'app_restored.db')
```

### 总结

- SQLite 通过 C API 核心提供多语言绑定，Python 的 sqlite3 模块开箱即用。
- 典型应用场景：移动应用、桌面应用、嵌入式、数据分析、测试替身、原型开发。
- 连接管理用上下文管理器确保正确关闭；预处理语句复用提升性能。
- 内存数据库（`:memory:`）适合测试和临时计算，速度快且隔离。
- **关键要点**：用 SQLite 内置 backup API 做在线备份；iterdump 导出 SQL 脚本。
- **常见误区**：跨线程共享连接导致错误；忘记 commit 导致数据丢失；生产环境用 `:memory:` 导致数据不持久。

---

## 课程结语

本课程从 SQLite 基础概念出发，系统讲解了其动态类型系统、SQL 语法、核心机制、进阶特性和实战应用，共 15 讲。SQLite 以"小而美"的设计哲学，在嵌入式数据库领域独树一帜，成为世界上部署最广泛的数据库引擎。

SQLite 的独特之处在于：动态类型系统提供了灵活性，WAL 模式实现了读写并发，单文件存储带来了极致便携性，零配置降低了使用门槛。这些特性使 SQLite 成为移动应用、桌面软件、嵌入式设备、数据分析等场景的理想选择。

建议在学习理论的同时多动手实践——搭建 SQLite 环境，完成一个完整的记账应用或笔记应用，尝试 WAL 模式和并发写入，用 EXPLAIN QUERY PLAN 分析查询计划。理论与实践结合，才能真正掌握 SQLite。

SQLite 虽小，五脏俱全。希望本课程能成为您 SQLite 学习之路的起点，祝您在轻量级数据管理的世界里游刃有余！
