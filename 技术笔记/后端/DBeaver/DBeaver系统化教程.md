# DBeaver 系统化教程

> 从零基础到精通的 30 讲完整课程，覆盖连接管理、SQL 编辑、数据操作、对象管理、ER 建模、数据迁移与性能优化等核心模块。

---

## 课程总览

### 学习目标

完成本课程后，您将能够：

1. **熟练安装与配置** DBeaver 社区版与企业版，理解其架构与适用场景
2. **管理多种数据库连接**，包括 MySQL、PostgreSQL、Oracle、SQL Server、SQLite 等
3. **高效编写与执行 SQL**，掌握自动补全、格式化、参数化查询等高级技巧
4. **浏览与编辑数据**，完成导入导出、批量操作与事务管理
5. **管理数据库对象**，包括表、视图、索引、约束、存储过程、触发器
6. **生成与使用 ER 图**，进行数据建模与可视化分析
7. **完成数据迁移与同步**，进行结构与数据的比较、复制与合并
8. **分析执行计划与性能**，定位慢查询并提出优化建议
9. **定制与扩展 DBeaver**，使用插件、模板、快捷键提升工作效率

### 课程结构

| 章节 | 主题 | 讲数 | 难度 |
|------|------|------|------|
| 第 1 章 | DBeaver 入门基础 | 3 | 基础 |
| 第 2 章 | 连接管理 | 4 | 基础 |
| 第 3 章 | SQL 编辑器 | 5 | 核心 |
| 第 4 章 | 数据浏览与编辑 | 4 | 核心 |
| 第 5 章 | 数据库对象管理 | 4 | 核心 |
| 第 6 章 | ER 图与数据建模 | 2 | 进阶 |
| 第 7 章 | 数据传输与同步 | 3 | 进阶 |
| 第 8 章 | 高级功能 | 3 | 进阶 |
| 第 9 章 | 性能分析与优化 | 2 | 高级 |

**总计：30 讲**

### 详细章节目录

#### 第 1 章 DBeaver 入门基础
- 第 1 讲：DBeaver 简介与安装
- 第 2 讲：界面布局与工作区
- 第 3 讲：首次启动与全局配置

#### 第 2 章 连接管理
- 第 4 讲：创建数据库连接
- 第 5 讲：连接配置详解
- 第 6 讲：驱动管理与下载
- 第 7 讲：连接池与高级设置

#### 第 3 章 SQL 编辑器
- 第 8 讲：SQL 编辑器基础
- 第 9 讲：SQL 自动补全与代码提示
- 第 10 讲：SQL 格式化与美化
- 第 11 讲：SQL 执行与结果处理
- 第 12 讲：参数化 SQL 与脚本执行

#### 第 4 章 数据浏览与编辑
- 第 13 讲：数据浏览与筛选
- 第 14 讲：数据编辑与提交
- 第 15 讲：数据导出
- 第 16 讲：数据导入

#### 第 5 章 数据库对象管理
- 第 17 讲：表结构查看与设计
- 第 18 讲：视图管理
- 第 19 讲：索引与约束
- 第 20 讲：存储过程、函数与触发器

#### 第 6 章 ER 图与数据建模
- 第 21 讲：ER 图生成与查看
- 第 22 讲：数据建模与导出

#### 第 7 章 数据传输与同步
- 第 23 讲：数据库迁移
- 第 24 讲：数据比较与同步
- 第 25 讲：结构比较

#### 第 8 章 高级功能
- 第 26 讲：事务管理
- 第 27 讲：自定义 SQL 模板与片段
- 第 28 讲：脚本与批处理

#### 第 9 章 性能分析与优化
- 第 29 讲：执行计划分析
- 第 30 讲：性能监控与优化建议

---

## 第 1 章 DBeaver 入门基础

本章带您从零开始认识 DBeaver，完成安装并熟悉工作环境。这是后续所有操作的基础，建议初学者按顺序学习。

### 第 1 讲：DBeaver 简介与安装

#### 概念

DBeaver 是一款免费、开源、跨平台的通用数据库管理工具，基于 Java（Eclipse RCP）开发，支持几乎所有主流数据库，包括 MySQL、PostgreSQL、Oracle、SQL Server、SQLite、MariaDB、DB2、Sybase、Firebird、H2 等 80 多种数据库。它提供统一的图形界面，让开发者和 DBA 能够在一个工具中管理多种数据库，无需为每种数据库安装专用客户端。

DBeaver 有两个主要版本：
- **Community Edition（CE，社区版）**：免费开源，基于 Apache 2.0 协议，覆盖绝大多数日常需求
- **Enterprise Edition（EE，企业版）**：商业版本，增加对 NoSQL（MongoDB、Redis、Cassandra）、Office 文档导出、AI 辅助 SQL 等高级功能的支持

#### 原理

DBeaver 之所以能支持众多数据库，核心在于其**驱动架构**。它通过 JDBC（Java Database Connectivity）标准接口连接数据库，每种数据库只需提供对应的 JDBC 驱动（一个 `.jar` 文件），DBeaver 就能通过统一的 API 与之交互。这意味着：

1. **统一抽象层**：DBeaver 内部维护一套通用的元数据模型，将不同数据库的对象（表、列、索引等）映射为统一表示
2. **驱动按需下载**：首次连接某类数据库时，DBeaver 会自动从 Maven 中央仓库下载对应驱动
3. **方言适配**：针对不同数据库的 SQL 方言差异，DBeaver 内置了语法解析器，能识别各数据库特有的关键字与函数

这种架构使 DBeaver 成为真正的"万能数据库客户端"，同时保持了较小的安装体积。

#### 例子

**安装步骤（以 Windows 为例）：**

1. 访问官网 `https://dbeaver.io/download/`，下载 Community Edition 安装包（`dbeaver-ce-x.x.x-x86_64-setup.exe`）
2. 双击安装包，按向导完成安装（建议保持默认路径）
3. 首次启动时，DBeaver 会提示选择工作区（Workspace）目录，用于存放连接配置、SQL 脚本等

**macOS 安装：**

```bash
# 使用 Homebrew 安装
brew install --cask dbeaver-community
```

**Linux 安装：**

```bash
# Debian/Ubuntu
wget https://dbeaver.io/files/dbeaver-ce_latest_amd64.deb
sudo dpkg -i dbeaver-ce_latest_amd64.deb

# 或使用 Snap
sudo snap install dbeaver-ce
```

**验证安装：** 启动 DBeaver，看到欢迎界面和左侧空的 Database 导航视图，即表示安装成功。

#### 总结

- DBeaver 是基于 Java 的跨平台通用数据库工具，通过 JDBC 支持多种数据库
- 社区版免费且功能完备，足以满足日常开发需求
- 安装时需注意 Java 运行环境（安装包通常已内置）
- 首次启动需选择工作区目录，建议选择非系统盘的稳定路径

---

### 第 2 讲：界面布局与工作区

#### 概念

DBeaver 的界面基于 Eclipse RCP 框架，采用**多视图（View）+ 多编辑器（Editor）**的经典 IDE 布局。理解界面布局是高效使用 DBeaver 的前提，因为所有操作都通过特定视图完成。主要组成包括：菜单栏、工具栏、Database 导航视图、项目视图、编辑器区域、属性视图、状态栏。

#### 原理

Eclipse RCP 框架的核心思想是**一切皆视图**。每个视图负责一类信息展示，编辑器负责内容编辑，两者通过**选择事件（Selection Event）**联动。例如，在 Database 视图中选中一张表，Properties 视图会自动显示该表的属性，ER 图视图会高亮该表的关系。这种联动机制让用户无需在多个窗口间切换，所有相关信息集中在一个工作区内。

DBeaver 的视图可以自由拖拽、停靠、最小化、最大化，甚至可以拖出到独立窗口（Detached Mode），适合多显示器工作环境。

#### 例子

**默认布局示意：**

```
┌─────────────────────────────────────────────────┐
│  菜单栏：File Edit View Window Help             │
├─────────────────────────────────────────────────┤
│  工具栏：连接 刷新 新建SQL 执行 导出 ...          │
├──────────┬──────────────────────────┬───────────┤
│ Database │                          │ Properties│
│ Navigator│      编辑器区域           │  视图     │
│  视图    │  (SQL编辑器/数据网格/ER图) │           │
│          │                          ├───────────┤
│ Project  │                          │  Outline  │
│  视图    │                          │   视图    │
├──────────┴──────────────────────────┴───────────┤
│  状态栏：连接状态 执行时间 行数 ...              │
└─────────────────────────────────────────────────┘
```

**常用视图说明：**

| 视图名称 | 功能 | 快捷打开方式 |
|---------|------|-------------|
| Database Navigator | 浏览数据库对象树 | Window → Show View |
| Project | 管理脚本、笔记、书签 | 默认显示 |
| Properties | 显示选中对象属性 | F4 |
| Outline | 显示当前编辑器大纲 | 默认显示 |
| Search | 全局搜索数据库对象 | Ctrl+H |
| Error Log | 查看错误日志 | Window → Show View |

**重置布局：** 如果界面被弄乱，可通过 `Window → Perspective → Reset Perspective...` 恢复默认布局。

#### 总结

- DBeaver 采用 Eclipse RCP 的多视图布局，视图间通过选择事件联动
- Database Navigator 是最核心的视图，所有数据库操作都从这里发起
- 视图可自由拖拽、停靠、分离，支持多显示器
- 界面混乱时可通过 Reset Perspective 恢复默认布局

---

### 第 3 讲：首次启动与全局配置

#### 概念

首次启动 DBeaver 后，需要进行一些全局配置以优化使用体验。全局配置影响所有连接和项目，包括外观主题、字体大小、SQL 编辑器行为、结果集显示、网络代理等。合理的全局配置能显著提升后续工作效率，避免在每个连接中重复设置。

#### 原理

DBeaver 的配置存储在两个层面：
1. **工作区配置（Workspace）**：存储在 `.metadata` 目录下，包含项目、连接、书签等用户数据
2. **全局首选项（Preferences）**：存储在 `.plugins/org.eclipse.core.runtime/.settings/` 下，以键值对形式保存界面、编辑器、网络等设置

首选项采用**继承覆盖**机制：全局设置是默认值，连接级设置可覆盖全局设置。例如，全局设置结果集每页显示 200 行，但某个连接可单独设置为 1000 行。这种分层设计既保证一致性，又允许局部定制。

#### 例子

**关键全局配置项（Window → Preferences）：**

1. **界面外观配置**
   - 路径：`User Interface → Appearance`
   - 设置主题（Light/Dark）、字体大小
   - 推荐字体：Consolas（Windows）、Menlo（macOS）、DejaVu Sans Mono（Linux）

2. **SQL 编辑器配置**
   - 路径：`Editors → SQL Editor`
   - 关键设置：
     ```
     Statement delimiter: ; （语句分隔符）
     Auto-save on close: 关闭时自动保存
     Show line numbers: 显示行号
     Word wrap: 自动换行
     ```

3. **结果集配置**
   - 路径：`Editors → Data Editor`
   - 关键设置：
     ```
     Result Set Max Rows: 200 （默认每页行数）
     Fetch Size: 200 （每次从数据库获取的行数）
     Show null values as: [NULL] （空值显示方式）
     Use native order: 按数据库原始顺序
     ```

4. **网络代理配置**
   - 路径：`Network Connections`
   - 若公司网络需代理，配置 HTTP/HTTPS 代理服务器
   - 这影响驱动下载和在线功能

5. **数据库连接配置**
   - 路径：`Database → Connections`
   - 设置默认连接超时、查询超时
   - 推荐查询超时：60 秒（避免长时间阻塞）

**配置导出与导入：**
```
File → Export → DBeaver → Project（导出所有配置）
File → Import → DBeaver → Project（导入配置）
```

#### 总结

- 全局配置在 Preferences 中设置，影响所有连接和项目
- 重点配置项：外观、SQL 编辑器、结果集、网络代理
- 配置采用分层覆盖机制，连接级设置优先于全局设置
- 建议导出配置备份，便于迁移到新机器或团队共享

---

## 第 2 章 连接管理

连接是 DBeaver 与数据库交互的桥梁。本章详细讲解如何创建、配置、管理数据库连接，是后续所有操作的基础。

### 第 4 讲：创建数据库连接

#### 概念

数据库连接（Connection）是 DBeaver 与目标数据库之间的一条通信通道，包含连接信息（主机、端口、数据库名、用户名、密码）和驱动信息。一个 DBeaver 工作区可以管理任意数量的连接，连接以树形结构组织在 Database Navigator 视图中。

#### 原理

DBeaver 创建连接的本质是构建一个 JDBC 连接字符串（URL），并通过驱动建立 TCP 会话。连接的生命周期包括：
1. **创建**：用户填写连接参数，DBeaver 生成 JDBC URL
2. **测试**：发送测试连接请求，验证参数正确性
3. **持久化**：将连接信息保存到工作区（密码可选加密存储）
4. **激活**：双击连接，建立实际数据库会话
5. **使用**：执行查询、浏览对象
6. **关闭**：断开会话，释放数据库资源

DBeaver 默认采用**懒连接**策略：连接创建后不会立即建立会话，只有当用户展开连接树或执行查询时才真正连接。这避免了启动时建立大量无用会话。

#### 例子

**创建 MySQL 连接的完整步骤：**

1. 点击工具栏的"新建连接"按钮（插座图标），或按 `Ctrl+Shift+N`
2. 在数据库选择对话框中，选择 `MySQL → MySQL (8.0+)`
3. 填写连接参数：
   ```
   Server Host: 127.0.0.1
   Port: 3306
   Database: mydb
   Username: root
   Password: ******（输入密码）
   ```
4. 点击左下角"Test Connection"按钮
   - 首次会提示下载 MySQL 驱动，点击 Download
   - 等待驱动下载完成（约 5MB）
   - 看到 "Connected" 提示表示成功
5. 点击 "Finish" 保存连接

**连接 URL 解析：**

DBeaver 生成的 JDBC URL 格式如下：
```
jdbc:mysql://127.0.0.1:3306/mydb?useUnicode=true&characterEncoding=utf8
```

各部分含义：
- `jdbc:mysql://`：协议标识
- `127.0.0.1:3306`：主机和端口
- `mydb`：数据库名
- `?useUnicode=true&...`：连接参数

**连接组织：** 可在 Database Navigator 中创建文件夹（右键 → Create Folder），将连接分组管理，例如按环境分组：
```
📁 开发环境
  📁 MySQL
    🔌 dev-mysql-01
  📁 PostgreSQL
    🔌 dev-pg-01
📁 生产环境
  🔌 prod-mysql-01
```

#### 总结

- 连接是 DBeaver 与数据库的通信通道，包含主机、端口、库名、凭据等信息
- 创建连接时务必点击 Test Connection 验证参数
- 首次连接某类数据库需下载对应驱动
- 建议用文件夹组织连接，按环境或项目分组
- DBeaver 采用懒连接策略，连接创建后不会立即占用数据库会话

---

### 第 5 讲：连接配置详解

#### 概念

创建连接时填写的参数只是冰山一角，DBeaver 的连接属性对话框包含数十个高级配置项，涵盖初始化 SQL、SSL、SSH 隧道、连接隔离级别、自动提交等。理解这些配置能解决复杂网络环境下的连接问题，并优化连接性能。

#### 原理

连接配置分为几个层次：
1. **基础参数**：主机、端口、库、用户、密码
2. **驱动参数**：JDBC URL 自定义参数、驱动属性
3. **网络层**：SSL、SSH 隧道、代理
4. **会话层**：初始化 SQL、隔离级别、自动提交、只读模式
5. **行为层**：是否自动提交事务、是否自动断开空闲连接

这些参数最终都会被翻译为 JDBC API 调用。例如，"自动提交"对应 `connection.setAutoCommit(true)`，"只读模式"对应 `connection.setReadOnly(true)`。

#### 例子

**关键配置项详解（连接属性 → Driver properties / Connection settings）：**

1. **Initialization SQL（初始化 SQL）**
   - 路径：`Connection settings → Initialization`
   - 用途：每次建立连接时自动执行的 SQL
   - 示例（Oracle 设置会话日期格式）：
     ```sql
     ALTER SESSION SET NLS_DATE_FORMAT = 'YYYY-MM-DD HH24:MI:SS';
     ALTER SESSION SET NLS_LANGUAGE = 'SIMPLIFIED CHINESE';
     ```

2. **SSH 隧道**
   - 路径：`Connection settings → SSH`
   - 用途：通过跳板机访问内网数据库
   - 配置：
     ```
     Host: jump.example.com
     Port: 22
     User: deploy
     Authentication: Public Key
     Private Key: ~/.ssh/id_rsa
     ```

3. **SSL 配置**
   - 路径：`Connection settings → SSL`
   - 用途：加密客户端与数据库之间的通信
   - 配置：
     ```
     Use SSL: ✓
     SSL Mode: verify-ca （验证证书）
     Trust Store: /path/to/truststore.jks
     Trust Store Password: ******
     ```

4. **Keep-Alive（保活）**
   - 路径：`Connection settings → Keep-Alive`
   - 用途：定期发送心跳包，防止连接被防火墙断开
   - 配置：
     ```
     Keep alive: ✓
     Interval: 60 seconds
     ```

5. **自动提交与隔离级别**
   - 路径：`Connection settings → Transaction`
   - 配置：
     ```
     Auto-commit: 默认开启（每条 SQL 自动提交）
     Isolation: READ_COMMITTED （读已提交）
     ```

**只读连接示例：** 对于生产环境，建议开启只读模式，防止误操作：
```
Connection settings → Connection type → Read-only
```
开启后，所有 UPDATE/DELETE/INSERT 语句将被拒绝执行。

#### 总结

- 连接配置远不止基础参数，包含初始化 SQL、SSL、SSH 隧道、保活等高级选项
- 初始化 SQL 用于设置会话级参数，如日期格式、字符集
- SSH 隧道是访问内网数据库的常用方式
- 生产环境连接建议开启只读模式，防止误操作
- Keep-Alive 配置可解决防火墙断连问题

---

### 第 6 讲：驱动管理与下载

#### 概念

驱动（Driver）是 DBeaver 连接数据库的"翻译官"，每种数据库需要对应的 JDBC 驱动。DBeaver 内置了 80 多种数据库的驱动定义，但驱动文件（`.jar`）需要按需下载。驱动管理涉及驱动版本选择、手动添加驱动、离线驱动配置等场景。

#### 原理

DBeaver 的驱动管理基于 **Maven 依赖解析机制**。每个驱动定义包含：
- **驱动类名**（Driver Class）：如 `com.mysql.cj.jdbc.Driver`
- **Maven 坐标**：`groupId:artifactId:version`，如 `mysql:mysql-connector-java:8.0.33`
- **下载源**（Repository）：默认 Maven 中央仓库，可配置私有仓库

当用户首次连接某类数据库时，DBeaver 根据 Maven 坐标从仓库下载 `.jar` 文件，缓存到本地（默认 `~/.dbeaver-drivers/`），后续连接直接使用缓存。这种机制保证了驱动按需获取，且支持版本升级。

#### 例子

**查看与编辑驱动定义：**

1. 菜单 `Database → Driver Manager`，打开驱动管理器
2. 选择"MySQL"，点击"Edit"
3. 查看驱动属性：
   ```
   Driver Name: MySQL
   Driver Type: MySQL
   Class Name: com.mysql.cj.jdbc.Driver
   URL Template: jdbc:mysql://{host}[:{port}]/[{database}]
   Libraries:
     - mysql:mysql-connector-java:8.0.33 (Maven)
   ```

**手动添加自定义驱动（以国产数据库达梦为例）：**

1. `Driver Manager → New`
2. 填写驱动信息：
   ```
   Driver Name: DM (Dameng)
   Driver Type: Generic
   Class Name: dm.jdbc.driver.DmDriver
   URL Template: jdbc:dm://{host}:{port}
   ```
3. 在 Libraries 标签页，点击 "Add File"，选择本地 `DmJdbcDriver.jar`
4. 点击 "OK" 保存

**离线环境配置驱动：**

若机器无法访问 Maven 仓库，可手动下载 `.jar` 文件并添加：
1. 从其他能联网的机器下载驱动 jar（如 `mysql-connector-java-8.0.33.jar`）
2. 拷贝到目标机器
3. 在驱动定义中，删除 Maven 依赖，添加本地文件路径

**驱动版本升级：**

当数据库升级后，可能需要新版本驱动：
1. `Driver Manager → Edit → Libraries`
2. 双击现有 Maven 依赖，修改版本号
3. 点击 "Download" 重新下载
4. 测试连接验证兼容性

#### 总结

- 驱动是 DBeaver 连接数据库的核心，基于 Maven 机制管理
- 首次连接会自动下载驱动，缓存到本地
- 可通过 Driver Manager 查看和编辑驱动定义
- 离线环境可手动添加本地 jar 文件
- 数据库升级后需同步升级驱动版本

---

### 第 7 讲：连接池与高级设置

#### 概念

默认情况下，DBeaver 为每个连接创建单一 JDBC 会话。但在高并发查询或长时间运行场景下，单一会话可能成为瓶颈。DBeaver 支持连接池（Connection Pool），允许复用多个会话，提升查询效率。此外，还有连接隔离、读写分离等高级设置。

#### 原理

连接池的核心思想是**会话复用**。传统模式下，每次查询都创建新会话，查询完成后销毁，频繁创建销毁开销大。连接池预先创建一批会话放入池中，查询时从池中借用，用完归还，避免重复创建。

DBeaver 支持的连接池实现：
- **HikariCP**：高性能连接池，默认推荐
- **C3P0**：老牌连接池，兼容性好
- **DBCP**：Apache 出品，配置灵活

连接池的关键参数：
- **最小空闲连接**（minimumIdle）：池中保持的最小空闲会话数
- **最大连接数**（maximumPoolSize）：池中最大会话数
- **连接超时**（connectionTimeout）：获取连接的等待时间
- **空闲超时**（idleTimeout）：空闲会话的最大存活时间

#### 例子

**启用连接池（以 MySQL 为例）：**

1. 打开连接属性，切换到 "Connection Pool" 标签
2. 勾选 "Use connection pool"
3. 选择实现：HikariCP
4. 配置参数：
   ```
   Minimum idle connections: 2
   Maximum pool size: 10
   Connection timeout: 30000 ms
   Idle timeout: 600000 ms
   Max lifetime: 1800000 ms
   ```

**多会话模式：**

除了连接池，DBeaver 还支持"多会话模式"（Allow concurrent queries），允许同一连接同时执行多个查询：
```
Connection settings → Type → Allow concurrent queries
```
开启后，每个 SQL 编辑器使用独立会话，互不阻塞。适合需要同时跑多个长查询的场景。

**读写分离配置：**

对于主从架构的数据库，可配置读写分离：
1. 创建主库连接（读写）
2. 创建从库连接（只读）
3. 在主库连接属性中，配置 "Read-only replicas"，关联从库连接
4. DBeaver 会自动将 SELECT 路由到从库，写操作路由到主库

**连接健康检查：**

配置连接池后，建议开启健康检查：
```
Connection pool → Validation query: SELECT 1
Test on borrow: ✓
Test while idle: ✓
```
这能确保借出的连接是有效的，避免因网络抖动导致查询失败。

#### 总结

- 连接池通过会话复用提升性能，推荐 HikariCP 实现
- 关键参数：最小空闲、最大连接数、超时时间
- 多会话模式允许同一连接并发执行查询，适合长查询场景
- 读写分离可将读操作路由到从库，减轻主库压力
- 开启连接健康检查，避免使用失效连接

---

## 第 3 章 SQL 编辑器

SQL 编辑器是 DBeaver 最核心的功能模块，是日常开发中使用频率最高的工具。本章将系统讲解 SQL 编辑器的各项功能，从基础操作到高级技巧。

### 第 8 讲：SQL 编辑器基础

#### 概念

SQL 编辑器（SQL Editor）是 DBeaver 提供的专用 SQL 代码编辑环境，支持语法高亮、代码折叠、行号显示、错误标记等基础编辑功能。每个 SQL 编辑器绑定一个数据库连接，编辑器中执行的 SQL 都在该连接对应的数据库上运行。

#### 原理

SQL 编辑器基于 Eclipse 的文本编辑框架（Text Editor Framework），并扩展了 SQL 语法解析器。其工作流程为：
1. **词法分析**：将输入文本分解为关键字、标识符、字符串、数字等 token
2. **语法解析**：根据数据库方言构建语法树，识别语句边界
3. **语义分析**：结合数据库元数据，识别表名、列名是否有效
4. **渲染**：根据 token 类型应用不同颜色（关键字蓝色、字符串绿色、注释灰色）
5. **执行**：将选中的语句或当前语句发送到数据库执行

DBeaver 通过"语句分隔符"（默认 `;`）识别单条 SQL，支持一次执行多条语句。

#### 例子

**打开 SQL 编辑器：**

- 方式 1：选中连接，按 `F3` 或 `Ctrl+Enter`
- 方式 2：右键连接 → SQL Editor → New SQL Editor
- 方式 3：工具栏"New SQL Editor"按钮

**基础操作示例：**

```sql
-- 创建测试表
CREATE TABLE employees (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    department VARCHAR(50),
    salary DECIMAL(10, 2),
    hire_date DATE
);

-- 插入示例数据
INSERT INTO employees (name, department, salary, hire_date)
VALUES 
    ('张三', '研发部', 15000.00, '2023-01-15'),
    ('李四', '市场部', 12000.00, '2023-03-20'),
    ('王五', '研发部', 18000.00, '2022-11-01');

-- 查询数据
SELECT * FROM employees WHERE department = '研发部';
```

**执行方式：**

| 操作 | 快捷键 | 说明 |
|------|--------|------|
| 执行当前语句 | `Ctrl+Enter` | 执行光标所在语句 |
| 执行选中语句 | `Ctrl+Enter` | 执行选中文本 |
| 执行全部脚本 | `Alt+X` | 执行编辑器中所有语句 |
| 执行并解释计划 | `Ctrl+Shift+E` | 显示执行计划 |
| 自动提交切换 | `Alt+C` | 切换自动提交模式 |

**结果面板：** 执行 SELECT 后，下方显示结果网格，包含：
- **Result Set**：查询结果表格
- **Output**：执行日志，包括语句、耗时、影响行数
- **Execution Plan**：执行计划（需数据库支持）

#### 总结

- SQL 编辑器是 DBeaver 最核心的功能，支持语法高亮和智能解析
- 每个编辑器绑定一个连接，SQL 在该连接的数据库上执行
- 通过语句分隔符识别单条 SQL，支持批量执行
- 熟练掌握快捷键（Ctrl+Enter、Alt+X）能显著提升效率
- 结果面板包含结果集、执行日志、执行计划三个标签

---

### 第 9 讲：SQL 自动补全与代码提示

#### 概念

自动补全（Auto-completion）是 SQL 编辑器的智能辅助功能，能在输入时提示表名、列名、函数名、关键字，减少手动输入和拼写错误。DBeaver 的自动补全基于数据库元数据，能感知当前连接的 schema、表、列结构。

#### 原理

DBeaver 的自动补全依赖**元数据缓存**。当用户首次展开某个 schema 时，DBeaver 会查询数据库的系统表（如 MySQL 的 `information_schema`、PostgreSQL 的 `pg_catalog`），获取所有表、列、视图、函数的定义，缓存到本地。输入时，编辑器根据上下文（如 `SELECT * FROM ` 后应提示表名）从缓存中匹配候选词。

补全触发方式：
- **自动触发**：输入 `.` 后自动提示（如 `employees.` 后提示列名）
- **手动触发**：按 `Ctrl+Space` 强制唤出补全列表

补全类型包括：
- **表名补全**：`FROM`、`JOIN`、`INTO`、`UPDATE` 后
- **列名补全**：`SELECT`、`WHERE`、`GROUP BY` 后，或表别名后输入 `.`
- **函数补全**：输入函数名前几个字母
- **关键字补全**：输入 SQL 关键字

#### 例子

**表名补全示例：**

输入以下 SQL，在 `FROM ` 后按 `Ctrl+Space`：
```sql
SELECT * FROM emp|
```
DBeaver 会弹出候选列表：
```
employees
employee_departments
employee_history
```
用方向键选择，回车确认。

**列名补全示例：**

使用表别名，输入 `.` 后自动提示列名：
```sql
SELECT e.| FROM employees e
```
弹出列名列表：
```
id
name
department
salary
hire_date
```

**JOIN 自动补全：**

输入 `JOIN` 后按 `Ctrl+Space`，DBeaver 会根据外键关系自动生成 ON 条件：
```sql
SELECT * FROM employees e
JOIN departments d ON |
```
自动补全为：
```sql
ON e.department_id = d.id
```

**函数补全：**

输入函数名前缀，提示完整函数签名：
```sql
SELECT DATE_FO|
```
提示：
```
DATE_FORMAT(date, format)
DATE_FORMAT(date, format, locale)
```

**配置补全行为：**

`Preferences → Editors → SQL Editor → Code Completion`：
```
Auto activation: ✓ （自动触发）
Auto activation delay: 200 ms （延迟，避免过于敏感）
Insert single proposal automatically: ✓ （唯一候选时自动插入）
Show type names: ✓ （显示列类型）
```

**刷新元数据缓存：** 当数据库结构变更后，补全可能失效。手动刷新：
- 右键 schema → Refresh
- 或快捷键 `F5`

#### 总结

- 自动补全基于元数据缓存，能提示表名、列名、函数名
- `Ctrl+Space` 手动触发补全，输入 `.` 自动触发列名补全
- JOIN 补全能根据外键关系自动生成 ON 条件
- 数据库结构变更后需刷新元数据缓存
- 合理配置补全延迟和触发方式，平衡响应速度与干扰

---

### 第 10 讲：SQL 格式化与美化

#### 概念

SQL 格式化（SQL Formatting）是将杂乱的 SQL 代码自动整理为规范、易读格式的过程。DBeaver 内置 SQL 格式化器，支持关键字大写、缩进、换行、对齐等规则，能将一行 SQL 拆分为多行，或反之压缩为单行。

#### 原理

DBeaver 的 SQL 格式化器基于**语法树遍历**。它先解析 SQL 为语法树，然后按预设规则重新生成代码：
1. **关键字大小写**：统一为大写或小写
2. **缩进**：根据嵌套层级添加缩进（如子查询、CASE WHEN）
3. **换行**：在关键字（SELECT、FROM、WHERE、AND）前换行
4. **对齐**：SELECT 子句的列对齐
5. **空格**：操作符两侧、逗号后添加空格

格式化规则可在 Preferences 中自定义，并保存为多个 Profile，适应不同团队的代码规范。

#### 例子

**格式化前（杂乱）：**

```sql
select e.name,e.salary,d.department_name from employees e join departments d on e.department_id=d.id where e.salary>10000 and d.department_name='研发部' order by e.salary desc
```

**格式化后（规范）：**

```sql
SELECT 
    e.name,
    e.salary,
    d.department_name
FROM employees e
JOIN departments d ON e.department_id = d.id
WHERE e.salary > 10000
    AND d.department_name = '研发部'
ORDER BY e.salary DESC;
```

**格式化操作：**
- 快捷键 `Ctrl+Shift+F`（与 Eclipse 一致）
- 右键 → Format SQL
- 菜单 Edit → Format SQL

**自定义格式化规则：**

`Preferences → Editors → SQL Editor → Formatting`：

1. **关键字大小写**：Upper / Lower / Preserve
2. **缩进风格**：
   ```
   Indent type: Spaces （空格或 Tab）
   Indent size: 4
   ```
3. **换行规则**：
   ```
   New line before keywords: ✓ （关键字前换行）
   New line before comma: ✗ （逗号前不换行，逗号后换行）
   ```
4. **对齐规则**：
   ```
   Align columns in SELECT: ✓ （SELECT 列对齐）
   Align equality operators: ✓ （= 对齐）
   ```

**多 Profile 管理：**

可创建多个格式化 Profile，适应不同场景：
- "团队规范"：4 空格缩进，关键字大写
- "紧凑模式"：单行，便于复制到日志
- "阅读模式"：每个列单独一行，最大可读性

切换 Profile：`Preferences → Formatting → Active profile`

**格式化选中部分：**

只想格式化部分代码时，选中目标 SQL，再按 `Ctrl+Shift+F`，只格式化选中部分。

#### 总结

- SQL 格式化能将杂乱代码整理为规范格式，提升可读性
- 快捷键 `Ctrl+Shift+F`，支持选中部分格式化
- 格式化规则可自定义，包括大小写、缩进、换行、对齐
- 支持多 Profile，适应不同团队规范
- 建议统一团队格式化规则，避免代码风格冲突

---

### 第 11 讲：SQL 执行与结果处理

#### 概念

SQL 执行是 DBeaver 的核心操作，但执行方式有多种：执行当前语句、执行选中、执行全部、执行并提取执行计划。执行后的结果处理也很丰富：结果集分页、排序、过滤、导出、对比、生成图表等。掌握这些技巧能大幅提升数据分析效率。

#### 原理

DBeaver 执行 SQL 的流程：
1. **解析**：识别要执行的语句（当前/选中/全部）
2. **预处理**：检查语法、参数化变量替换
3. **执行**：通过 JDBC 发送到数据库
4. **获取结果**：分批从数据库拉取结果（Fetch Size）
5. **渲染**：将结果填充到网格

**分页机制**：DBeaver 不会一次性加载所有结果，而是按 Fetch Size（默认 200 行）分批拉取。滚动到末尾时自动加载下一批。这避免了百万行结果撑爆内存。

**结果集类型**：
- **Forward-only**：只能向前滚动，性能最佳
- **Scroll-insensitive**：可双向滚动，不感知数据库变更
- **Scroll-sensitive**：可双向滚动，感知数据库变更（性能差，少用）

#### 例子

**执行方式对比：**

| 操作 | 快捷键 | 适用场景 |
|------|--------|---------|
| 执行当前语句 | `Ctrl+Enter` | 日常单条查询 |
| 执行选中语句 | `Ctrl+Enter` | 执行部分代码 |
| 执行全部脚本 | `Alt+X` | 批量执行多条 |
| 执行到光标位置 | `Alt+Shift+X` | 执行光标前所有语句 |
| 执行并查看计划 | `Ctrl+Shift+E` | 性能分析 |

**结果集操作：**

1. **分页浏览**
   - 默认每页 200 行，可在状态栏调整
   - 点击 "Fetch next" 加载下一批
   - "Fetch all" 一次性加载所有（慎用，大结果可能 OOM）

2. **列操作**
   - 拖动列头调整顺序
   - 右键列头 → Hide Column 隐藏列
   - 右键列头 → Filter → Custom Filter 过滤
   - 点击列头排序（点击多次切换升降序）

3. **行操作**
   - `Ctrl+F` 打开查找框，搜索特定值
   - 右键行 → Copy Row 复制整行
   - 右键行 → Delete Row 删除（需可编辑结果集）

4. **数据导出**
   - 右键结果网格 → Export Resultset
   - 选择格式：CSV、Excel、JSON、HTML、SQL INSERT
   - 配置导出选项（编码、分隔符、是否含表头）

**多结果集处理：**

执行多条 SELECT 时，结果面板显示多个标签：
```sql
SELECT * FROM employees;
SELECT * FROM departments;
SELECT * FROM projects;
```
每个 SELECT 对应一个 Result 标签，可切换查看。

**结果集对比：**

选中两个结果集，右键 → Compare，逐行对比差异，常用于验证数据迁移正确性。

**生成图表：**

右键结果网格 → View → Chart，支持柱状图、折线图、饼图：
```
X 轴：department
Y 轴：COUNT(*)
图表类型：Bar
```
适合快速可视化分析。

#### 总结

- SQL 执行有多种方式，根据场景选择（单条/选中/全部）
- 结果集采用分批拉取机制，避免内存溢出
- 结果支持排序、过滤、隐藏列、查找等丰富操作
- 可直接导出为 CSV、Excel、JSON 等格式
- 结果集对比和图表生成是数据分析的利器

---

### 第 12 讲：参数化 SQL 与脚本执行

#### 概念

参数化 SQL（Parameterized SQL）允许在 SQL 中使用变量占位符，执行时动态传入参数值。这在重复执行相似查询、批量操作、防止 SQL 注入等场景下非常有用。DBeaver 支持多种参数化方式：命名参数、位置参数、变量绑定。

#### 原理

DBeaver 的参数化基于 **PreparedStatement** 机制。SQL 中的占位符在执行前被替换为实际值，数据库驱动负责类型转换和转义。这种方式相比字符串拼接有两个优势：
1. **安全性**：参数值不会被解析为 SQL 语法，天然防止 SQL 注入
2. **性能**：数据库可缓存预编译计划，重复执行更快

DBeaver 支持的参数语法：
- **命名参数**：`${param_name}`，执行时弹出输入框
- **位置参数**：`?`，按顺序绑定
- **变量**：`@var_name`，通过脚本预先定义

#### 例子

**命名参数示例：**

```sql
SELECT * FROM employees
WHERE department = ${dept_name}
  AND salary > ${min_salary}
  AND hire_date > ${hire_date};
```

执行时弹出参数输入对话框：
```
Parameter        Type        Value
dept_name        VARCHAR     研发部
min_salary       DECIMAL     10000
hire_date        DATE        2023-01-01
```
填入参数后执行。下次执行时保留上次值，便于重复查询。

**变量定义与使用：**

```sql
@set dept = '研发部';
@set min_sal = 10000;

SELECT * FROM employees
WHERE department = '${dept}'
  AND salary > ${min_sal};
```

`@set` 是 DBeaver 的脚本命令，定义变量供后续 SQL 使用。

**循环执行（DBeaver 脚本语法）：**

```sql
@set departments = ('研发部', '市场部', '财务部');

@foreach dept in ${departments} {
    SELECT '${dept}' AS dept, COUNT(*) AS cnt
    FROM employees
    WHERE department = '${dept}';
}
```

执行后为每个部门生成一条查询，结果合并显示。

**位置参数示例：**

```sql
SELECT * FROM employees WHERE department = ? AND salary > ?;
```
执行时按顺序输入参数值。

**参数化导出：**

结合参数化与导出，可批量导出多个部门的数据：
```sql
-- 为每个部门生成 CSV 文件
@set depts = SELECT DISTINCT department FROM departments;

@foreach d in ${depts} {
    @export
    SELECT * FROM employees WHERE department = '${d}'
    TO '/tmp/export_${d}.csv' FORMAT CSV;
}
```

**防止 SQL 注入的对比：**

不安全（字符串拼接）：
```sql
-- 用户输入: '; DROP TABLE employees; --
SELECT * FROM employees WHERE name = '' || '${user_input}' || '';
```

安全（参数化）：
```sql
SELECT * FROM employees WHERE name = ${user_input};
```
参数值即使包含 SQL 语法，也只被当作字符串处理。

#### 总结

- 参数化 SQL 使用 `${name}` 或 `?` 占位符，执行时动态传值
- 参数化能防止 SQL 注入，提升安全性
- `@set` 定义变量，`@foreach` 实现循环，支持脚本化操作
- 参数值会被记住，便于重复执行相似查询
- 结合参数化与导出，可实现批量数据导出

---

## 第 4 章 数据浏览与编辑

除了写 SQL，DBeaver 还提供图形化的数据浏览与编辑功能，无需 SQL 即可查看和修改数据。本章讲解数据网格的各项操作。

### 第 13 讲：数据浏览与筛选

#### 概念

数据浏览（Data Browsing）是通过图形化网格查看表数据的功能，无需编写 SELECT 语句。双击表名即可打开数据视图，支持分页、排序、筛选、列定制等操作。对于快速查看数据、验证业务逻辑非常方便。

#### 原理

DBeaver 的数据浏览基于 **ResultSet 机制**。打开表数据时，DBeaver 自动生成 `SELECT * FROM table LIMIT 200` 查询，结果填充到网格。网格采用**虚拟滚动**技术，只渲染可见区域的单元格，即使结果集有百万行也能流畅滚动。

筛选功能分为两种：
- **客户端筛选**：在已加载的数据中过滤，速度快但只筛已加载部分
- **服务端筛选**：重新构造 WHERE 子句查询数据库，结果准确但需往返

DBeaver 默认使用服务端筛选，确保结果准确。

#### 例子

**打开表数据视图：**

- 方式 1：双击表名 → 切换到 "Data" 标签
- 方式 2：右键表 → View Data → Grid
- 方式 3：选中表，按 `Ctrl+Shift+D`

**筛选数据：**

1. **列筛选（快速筛选）**
   - 点击列头的漏斗图标
   - 输入筛选值，如 `研发部`
   - 自动生成 `WHERE department = '研发部'`

2. **自定义筛选**
   - 点击工具栏 "Filter" 按钮
   - 构建条件：
     ```
     department = '研发部' AND salary > 10000
     ```
   - 支持图形化条件构建器，无需手写 SQL

3. **保存筛选**
   - 配置好的筛选可保存为命名筛选
   - 下次直接加载，便于复用

**排序：**

- 点击列头排序（升序/降序/取消）
- 多列排序：按住 Shift 点击多列
- 右键列头 → Sort → Advanced 配置复杂排序

**列定制：**

- 右键列头 → Hide Column 隐藏不需要的列
- 拖动列头调整顺序
- 右键列头 → Set Column Width 自定义宽度
- 配置可保存为 View，便于复用

**分页与计数：**

- 状态栏显示当前行数 / 总行数
- "Fetch next" 加载下一批
- "Fetch all" 加载全部（慎用）
- 右键 → Row Count 执行 `SELECT COUNT(*)` 获取准确总数

**数据查找：**

- `Ctrl+F` 打开查找框
- 支持全字段搜索、正则、大小写敏感
- `F3` 查找下一个，`Shift+F3` 查找上一个

**示例：浏览员工表**

1. 双击 `employees` 表，切换到 Data 标签
2. 点击 `department` 列的漏斗，输入 `研发部`
3. 点击 `salary` 列头排序（降序）
4. 隐藏 `id`、`hire_date` 列
5. 保存为 View "研发部薪资排序"
6. 下次直接加载该 View，无需重新配置

#### 总结

- 数据浏览通过图形化网格查看表数据，无需 SQL
- 双击表名即可打开，支持分页、排序、筛选
- 筛选分客户端和服务端两种，默认服务端确保准确
- 列定制可保存为 View，便于复用
- 虚拟滚动技术支持大数据集流畅浏览

---

### 第 14 讲：数据编辑与提交

#### 概念

数据编辑（Data Editing）允许直接在数据网格中修改、添加、删除记录，无需编写 INSERT/UPDATE/DELETE 语句。修改后通过事务提交或回滚，确保数据一致性。这是 DBeaver 区别于纯查询工具的重要功能。

#### 原理

DBeaver 的数据编辑基于**乐观锁**机制。用户在网格中修改数据时，修改暂存在内存中（标记为"脏数据"），并未立即发送到数据库。用户点击"保存"后，DBeaver 生成对应的 UPDATE/INSERT/DELETE 语句，在一个事务中执行。

乐观锁通过**版本号或时间戳**检测冲突：如果修改期间数据库中的记录已被他人修改，DBeaver 会提示冲突，让用户选择覆盖或放弃。

事务管理：
- **自动提交模式**：每条修改立即提交，无法回滚
- **手动提交模式**：修改暂存，用户决定提交或回滚

#### 例子

**编辑模式开启：**

数据视图默认是只读的，需切换到编辑模式：
- 点击工具栏 "Edit" 按钮（铅笔图标）
- 或右键 → Toggle Edit Mode

**修改记录：**

1. 双击单元格进入编辑状态
2. 修改值，回车确认
3. 修改的单元格高亮显示（黄色背景）
4. 行首显示修改标记（`*` 或 `M`）

**示例：修改员工薪资**
```
修改前: 张三 | 研发部 | 15000.00
修改后: 张三 | 研发部 | 18000.00  ← 黄色高亮
```

**添加记录：**

1. 点击工具栏 "Add new row"（+ 图标）
2. 在新行中输入数据
3. 必填字段未填会标红提示
4. 行首显示新增标记（`+` 或 `I`）

**删除记录：**

1. 选中行（可多选）
2. 点击工具栏 "Delete row"（- 图标），或按 `Delete`
3. 行首显示删除标记（`-` 或 `D`）
4. 行内容变灰，表示待删除

**提交修改：**

1. 点击 "Save" 按钮（Ctrl+S）
2. DBeaver 生成 SQL 并执行：
   ```sql
   -- 自动生成的 SQL
   UPDATE employees SET salary = 18000.00 WHERE id = 1;
   INSERT INTO employees (name, department, salary) VALUES ('赵六', '研发部', 20000);
   DELETE FROM employees WHERE id = 5;
   ```
3. 在 Output 面板查看执行的 SQL

**事务控制：**

- **自动提交开启**：保存即提交，无法撤销
- **自动提交关闭**：保存后修改在事务中，需手动 Commit 或 Rollback
  - 点击工具栏 "Commit"（✓）提交
  - 点击 "Rollback"（✗）回滚

**冲突处理：**

若提交时检测到记录已被他人修改：
```
Conflict detected:
Row was modified by another user.
Current DB value: salary = 17000
Your value: salary = 18000
[Overwrite] [Skip] [Cancel]
```

**复制粘贴行：**

- 选中行 → Ctrl+C 复制
- Ctrl+V 粘贴为新行
- 适合快速创建相似记录

#### 总结

- 数据编辑允许图形化修改、添加、删除记录
- 修改暂存在内存，保存时生成 SQL 执行
- 自动提交模式立即生效，手动模式可回滚
- 乐观锁机制检测并发冲突
- 复制粘贴行是快速创建相似记录的技巧

---

### 第 15 讲：数据导出

#### 概念

数据导出（Data Export）是将查询结果或表数据导出为外部文件的功能，支持 CSV、Excel、JSON、HTML、SQL INSERT、XML 等多种格式。这是数据备份、迁移、共享的常用功能。

#### 原理

DBeaver 的导出基于**流式处理**架构。导出时不会将所有数据加载到内存，而是分批从数据库读取，逐行写入文件。这种设计支持导出超大结果集（百万行以上）而不会内存溢出。

导出流程：
1. **数据源**：结果集、表、自定义查询
2. **格式化器**：根据目标格式（CSV、JSON 等）转换数据
3. **写入器**：流式写入文件
4. **进度反馈**：实时显示导出进度

#### 例子

**导出查询结果为 CSV：**

1. 执行查询，得到结果集
2. 右键结果网格 → Export Resultset
3. 选择格式：CSV
4. 配置选项：
   ```
   Output file: /tmp/employees.csv
   Encoding: UTF-8
   Delimiter: , （分隔符）
   Quote character: " （引号）
   Escape character: \ （转义符）
   Line separator: \n （换行符）
   Include column headers: ✓ （包含表头）
   NULL value: NULL （空值表示）
   ```
5. 点击 "Start" 开始导出

**导出整张表：**

1. 右键表 → Export Data
2. 选择格式和目标
3. 配置 WHERE 条件（可选，导出部分数据）
4. 开始导出

**导出为 Excel：**

```
Format: Excel (xlsx)
Output: /tmp/employees.xlsx
Sheet name: Employees
Include headers: ✓
Format dates: ✓ （日期格式化）
```

**导出为 SQL INSERT 语句：**

适合数据迁移到另一个数据库：
```
Format: SQL INSERT
Output: /tmp/employees.sql
Table name: employees （目标表名）
Include create table: ✓ （包含建表语句）
```
生成内容：
```sql
INSERT INTO employees (id, name, department, salary) VALUES (1, '张三', '研发部', 15000.00);
INSERT INTO employees (id, name, department, salary) VALUES (2, '李四', '市场部', 12000.00);
```

**导出为 JSON：**

```
Format: JSON
Output: /tmp/employees.json
Format: Pretty （美化）
```
生成内容：
```json
[
  {"id": 1, "name": "张三", "department": "研发部", "salary": 15000.00},
  {"id": 2, "name": "李四", "department": "市场部", "salary": 12000.00}
]
```

**批量导出多张表：**

1. 选中多张表（Ctrl+点击）
2. 右键 → Export Data
3. 选择格式和目录
4. 每张表导出为单独文件

**导出向导高级选项：**

- **分批提交**：每 N 行刷新一次，避免长事务
- **压缩**：导出为 gzip 压缩文件
- **加密**：导出为加密文件（企业版）
- **进度日志**：记录导出过程，便于排查

#### 总结

- 数据导出支持 CSV、Excel、JSON、SQL INSERT 等多种格式
- 流式处理架构支持导出超大结果集
- 可导出查询结果、整张表、多张表
- SQL INSERT 格式适合数据库间数据迁移
- 高级选项包括分批提交、压缩、加密

---

### 第 16 讲：数据导入

#### 概念

数据导入（Data Import）是将外部文件数据批量插入数据库表的功能，与导出互为逆操作。支持 CSV、Excel、JSON、SQL 脚本等格式，能自动映射列、处理数据类型转换、跳过错误行。

#### 原理

DBeaver 的导入采用**预解析 + 批量插入**策略：
1. **预解析**：先读取文件前几行，识别列结构
2. **列映射**：将文件列映射到目标表列
3. **类型转换**：根据目标列类型转换数据（如字符串转日期）
4. **批量插入**：使用 JDBC 的 `addBatch` 批量提交，每 N 行执行一次
5. **错误处理**：错误行记录到日志，可选择跳过或中止

批量插入相比单条插入性能提升数十倍，因为减少了网络往返和事务开销。

#### 例子

**从 CSV 导入：**

1. 右键目标表 → Import Data
2. 选择格式：CSV
3. 选择文件：`/tmp/new_employees.csv`
4. 预览数据：
   ```
   name,department,salary,hire_date
   赵六,研发部,16000,2024-01-15
   钱七,市场部,11000,2024-02-20
   ```
5. 列映射：
   ```
   CSV 列        →  表列
   name          →  name
   department    →  department
   salary        →  salary
   hire_date     →  hire_date
   ```
6. 配置选项：
   ```
   Batch size: 1000 （每批插入行数）
   On error: Skip （错误时跳过）
   Commit interval: 1000 （每 N 行提交）
   Truncate table first: ✗ （是否先清空表）
   ```
7. 点击 "Start" 开始导入

**从 Excel 导入：**

```
File: /tmp/employees.xlsx
Sheet: Sheet1
Header row: 1 （表头所在行）
Data start row: 2 （数据起始行）
```
列映射与 CSV 类似，但需注意 Excel 的日期格式可能需要特殊处理。

**从 JSON 导入：**

支持嵌套 JSON：
```json
[
  {"name": "赵六", "dept": {"name": "研发部"}, "salary": 16000},
  {"name": "钱七", "dept": {"name": "市场部"}, "salary": 11000}
]
```
配置 JSONPath 映射：
```
$.name        →  name
$.dept.name   →  department
$.salary      →  salary
```

**从 SQL 脚本导入：**

直接执行 SQL 脚本文件（包含 INSERT 语句）：
1. `File → Open File`，打开 .sql 文件
2. 选择连接
3. `Alt+X` 执行全部

**导入错误处理：**

错误行记录到日志：
```
Row 5: ERROR: invalid input syntax for type numeric: "abc"
Row 12: ERROR: null value in column "name" violates not-null constraint
```
选项：
- **Skip**：跳过错误行，继续导入
- **Abort**：遇到错误立即中止
- **Log and continue**：记录错误并继续

**导入性能优化：**

1. **增大 Batch Size**：从默认 1000 增到 5000-10000
2. **关闭索引**：导入前临时禁用索引，导入后重建
3. **关闭约束检查**：临时禁用外键约束
4. **使用 LOAD DATA**（MySQL）：比 INSERT 快 20 倍
   ```
   Import → Advanced → Use LOAD DATA INFILE
   ```

**导入前预检查：**

建议先导入到临时表，验证数据后再迁移到正式表：
```sql
-- 导入到 staging 表
IMPORT INTO employees_staging FROM '/tmp/employees.csv';

-- 验证数据
SELECT COUNT(*) FROM employees_staging;
SELECT * FROM employees_staging WHERE name IS NULL;

-- 确认无误后迁移
INSERT INTO employees SELECT * FROM employees_staging;
```

#### 总结

- 数据导入支持 CSV、Excel、JSON、SQL 脚本等格式
- 采用批量插入策略，性能远优于单条插入
- 列映射自动匹配，支持手动调整
- 错误处理可选跳过、中止、记录
- 性能优化：增大 Batch Size、禁用索引和约束
- 建议先导入到临时表验证，再迁移到正式表

---

## 第 5 章 数据库对象管理

数据库对象是数据库的"骨架"，包括表、视图、索引、约束、存储过程、触发器等。本章讲解如何用 DBeaver 图形化管理这些对象，无需手写 DDL。

### 第 17 讲：表结构查看与设计

#### 概念

表（Table）是数据库存储数据的基本单元，由列（Column）和行（Row）组成。DBeaver 提供图形化的表设计器（Table Designer），允许查看表结构、修改列定义、设置主键、添加注释，无需编写 CREATE TABLE / ALTER TABLE 语句。

#### 原理

DBeaver 的表设计器通过查询数据库的系统目录（System Catalog）获取表结构元数据。不同数据库的系统目录不同：
- **MySQL**：`information_schema.columns`
- **PostgreSQL**：`pg_catalog.pg_attribute`
- **Oracle**：`ALL_TAB_COLUMNS`
- **SQL Server**：`sys.columns`

DBeaver 将这些差异抽象为统一的元数据模型，呈现给用户统一的界面。修改表结构时，DBeaver 自动生成对应的 ALTER TABLE 语句，用户确认后执行。

#### 例子

**查看表结构：**

1. 双击表名，打开表编辑器
2. 切换到 "Properties" 标签，查看基本信息：
   ```
   Name: employees
   Schema: public
   Type: TABLE
   Comment: 员工信息表
   Row count: 1000
   ```
3. 切换到 "Columns" 标签，查看列定义：
   ```
   Name          Type           Length  Not Null  Default    Comment
   id            INTEGER        10      ✓        AUTO_INC   主键
   name          VARCHAR        100     ✓                   姓名
   department    VARCHAR        50                          部门
   salary        DECIMAL        10,2                        薪资
   hire_date     DATE                                       入职日期
   ```

**修改表结构：**

1. 右键表 → Edit Table（或按 F4）
2. 在 Columns 标签：
   - 点击 "Add Column" 添加新列
   - 双击列定义修改属性
   - 选中列，点击 "Remove" 删除

**示例：添加邮箱列**
```
Name: email
Type: VARCHAR
Length: 200
Not Null: ✗
Default: NULL
Comment: 员工邮箱
```

3. 切换到 "Preview" 标签，查看生成的 SQL：
```sql
ALTER TABLE employees 
ADD COLUMN email VARCHAR(200) NULL;
COMMENT ON COLUMN employees.email IS '员工邮箱';
```

4. 点击 "Save" 执行修改

**主键与索引管理：**

- **Primary Key** 标签：查看和修改主键
- **Foreign Keys** 标签：管理外键关系
- **Indexes** 标签：查看和创建索引
- **Constraints** 标签：管理 CHECK、UNIQUE 等约束

**生成 DDL：**

右键表 → Generate SQL → DDL，生成完整的建表语句：
```sql
CREATE TABLE employees (
    id INT NOT NULL AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    department VARCHAR(50),
    salary DECIMAL(10, 2),
    hire_date DATE,
    email VARCHAR(200),
    PRIMARY KEY (id)
);

COMMENT ON TABLE employees IS '员工信息表';
COMMENT ON COLUMN employees.name IS '姓名';
```

**复制表结构：**

右键表 → Generate SQL → DDL → 复制，可粘贴到其他数据库执行（注意方言差异）。

#### 总结

- 表设计器通过图形化界面管理表结构，无需手写 DDL
- 通过查询系统目录获取元数据，抽象为统一界面
- 修改时自动生成 ALTER TABLE 语句，预览后执行
- 支持主键、外键、索引、约束的图形化管理
- 可生成完整 DDL，便于复制和迁移

---

### 第 18 讲：视图管理

#### 概念

视图（View）是虚拟表，基于一个或多个表的查询结果定义。视图不存储数据，只存储查询定义，每次访问时动态执行查询。DBeaver 支持创建、修改、删除视图，并提供视图依赖分析。

#### 原理

视图的本质是**预定义的 SELECT 语句**。数据库在解析 SQL 时，遇到视图引用，会将视图定义展开为子查询。例如：
```sql
-- 视图定义
CREATE VIEW high_salary_employees AS
SELECT * FROM employees WHERE salary > 10000;

-- 查询视图
SELECT name FROM high_salary_employees;
-- 实际执行：
SELECT name FROM (SELECT * FROM employees WHERE salary > 10000) t;
```

视图的优点：
- **简化查询**：复杂查询封装为视图，外部使用简单
- **权限控制**：限制用户只能访问视图中的列
- **数据抽象**：底层表结构变更时，视图可保持不变

视图的类型：
- **普通视图**：每次查询时动态执行
- **物化视图**：存储查询结果，定期刷新（部分数据库支持）

#### 例子

**创建视图：**

1. 右键 schema → Create New View
2. 在视图编辑器中填写：
   ```
   Name: v_dept_stats
   Comment: 部门统计视图
   ```
3. 在 SQL 编辑区编写视图定义：
   ```sql
   SELECT 
       d.department_name,
       COUNT(e.id) AS employee_count,
       AVG(e.salary) AS avg_salary,
       MAX(e.salary) AS max_salary,
       MIN(e.salary) AS min_salary
   FROM departments d
   LEFT JOIN employees e ON d.id = e.department_id
   GROUP BY d.department_name
   ```
4. 点击 "Save"，生成并执行：
   ```sql
   CREATE VIEW v_dept_stats AS
   SELECT ... ;
   ```

**修改视图：**

1. 右键视图 → Edit View
2. 修改 SQL 定义
3. 保存时生成 `CREATE OR REPLACE VIEW`

**查看视图数据：**

双击视图，切换到 "Data" 标签，像查询表一样查看视图数据。

**视图依赖分析：**

右键视图 → Dependencies，查看：
- **依赖对象**：视图引用的表、其他视图
- **被依赖对象**：哪些对象引用了此视图

这对于评估修改影响非常重要。例如，删除一个被视图引用的表，会导致视图失效。

**物化视图（PostgreSQL 示例）：**

```sql
CREATE MATERIALIZED VIEW mv_dept_stats AS
SELECT 
    d.department_name,
    COUNT(e.id) AS employee_count,
    AVG(e.salary) AS avg_salary
FROM departments d
LEFT JOIN employees e ON d.id = e.department_id
GROUP BY d.department_name;

-- 刷新物化视图
REFRESH MATERIALIZED VIEW mv_dept_stats;
```

物化视图存储实际数据，查询速度快，但数据可能过期，需定期刷新。

**视图与表的区别：**

| 特性 | 表 | 视图 |
|------|----|----|
| 存储数据 | 是 | 否（物化视图除外） |
| 可编辑 | 是 | 受限（简单视图可编辑） |
| 占用空间 | 是 | 否 |
| 查询性能 | 直接 | 需展开查询 |

#### 总结

- 视图是预定义的 SELECT 语句，不存储数据
- 优点：简化查询、权限控制、数据抽象
- DBeaver 支持图形化创建、修改、删除视图
- 依赖分析帮助评估修改影响
- 物化视图存储结果，查询快但需定期刷新

---

### 第 19 讲：索引与约束

#### 概念

索引（Index）是提升查询性能的数据结构，类似书的目录。约束（Constraint）是保证数据完整性的规则，如主键、外键、唯一、非空、检查。DBeaver 提供图形化管理索引和约束的功能。

#### 原理

**索引原理：**

数据库索引通常采用 **B+ 树**结构。B+ 树是一种平衡多路查找树，特点：
- 所有叶子节点在同一层，查询性能稳定
- 非叶子节点只存储索引键，叶子节点存储数据指针
- 范围查询高效（叶子节点用链表连接）

查询时，数据库先在索引树中查找键值，定位到数据行，避免全表扫描。但索引有代价：
- **写入开销**：INSERT/UPDATE/DELETE 需同步更新索引
- **存储开销**：索引占用额外空间
- **选择性**：低选择性的列（如性别）建索引收益小

**约束原理：**

约束是数据库强制的规则，在数据修改时自动检查：
- **PRIMARY KEY**：主键，唯一且非空
- **FOREIGN KEY**：外键，引用其他表的主键
- **UNIQUE**：唯一约束，列值不重复
- **NOT NULL**：非空约束
- **CHECK**：检查约束，满足指定条件

约束在写入时检查，违反约束的操作会被拒绝，保证数据一致性。

#### 例子

**创建索引：**

1. 右键表 → Edit Table → Indexes 标签
2. 点击 "Add Index"
3. 配置索引：
   ```
   Name: idx_employees_department
   Columns: department, salary
   Type: BTREE
   Unique: ✗
   Comment: 部门+薪资联合索引
   ```
4. 预览生成的 SQL：
   ```sql
   CREATE INDEX idx_employees_department 
   ON employees (department, salary);
   ```
5. 保存执行

**索引类型选择：**

| 类型 | 适用场景 | 特点 |
|------|---------|------|
| BTREE | 通用，等值和范围查询 | 默认类型 |
| HASH | 等值查询 | 不支持范围 |
| GIN | PostgreSQL 全文搜索、JSON | 多值列 |
| GIST | 地理数据、范围类型 | 特殊场景 |
| FULLTEXT | MySQL 全文搜索 | 文本搜索 |

**约束管理：**

1. **主键约束**
   ```
   Edit Table → Primary Key → 选择列
   ```
   生成：`ALTER TABLE employees ADD PRIMARY KEY (id);`

2. **外键约束**
   ```
   Edit Table → Foreign Keys → Add
   Name: fk_emp_dept
   Columns: department_id
   References: departments(id)
   On delete: CASCADE （级联删除）
   On update: CASCADE
   ```
   生成：
   ```sql
   ALTER TABLE employees 
   ADD CONSTRAINT fk_emp_dept 
   FOREIGN KEY (department_id) REFERENCES departments(id)
   ON DELETE CASCADE ON UPDATE CASCADE;
   ```

3. **唯一约束**
   ```
   Edit Table → Constraints → Add → Unique
   Columns: email
   ```
   生成：`ALTER TABLE employees ADD CONSTRAINT uk_email UNIQUE (email);`

4. **检查约束**
   ```
   Edit Table → Constraints → Add → Check
   Condition: salary > 0 AND salary < 1000000
   ```
   生成：
   ```sql
   ALTER TABLE employees 
   ADD CONSTRAINT chk_salary 
   CHECK (salary > 0 AND salary < 1000000);
   ```

**索引使用分析：**

执行查询时查看执行计划，确认索引是否被使用：
```sql
EXPLAIN SELECT * FROM employees WHERE department = '研发部';
```
若执行计划显示 `type: ref` 或 `Index Scan`，表示使用了索引；若显示 `type: ALL` 或 `Seq Scan`，表示全表扫描，需考虑添加索引。

**索引优化建议：**

- 高频查询的 WHERE、JOIN、ORDER BY 列建索引
- 联合索引遵循"最左前缀"原则
- 避免在低选择性列（如性别）建索引
- 写入频繁的表控制索引数量

#### 总结

- 索引采用 B+ 树结构，加速查询但有写入和存储开销
- 约束保证数据完整性，包括主键、外键、唯一、检查
- DBeaver 提供图形化管理索引和约束
- 索引类型多样，根据查询模式选择
- 通过执行计划验证索引使用情况

---

### 第 20 讲：存储过程、函数与触发器

#### 概念

存储过程（Stored Procedure）、函数（Function）、触发器（Trigger）是数据库的可编程对象，将业务逻辑封装在数据库层。DBeaver 支持查看、编辑、调试这些对象，是数据库开发的重要工具。

#### 原理

**存储过程**：预编译的 SQL 语句集合，存储在数据库中，可被多次调用。优点：
- **性能**：预编译，执行计划缓存
- **安全**：通过存储过程控制数据访问
- **复用**：业务逻辑集中管理

**函数**：类似存储过程，但必须返回值，可在 SQL 中使用：
```sql
SELECT calculate_tax(salary) FROM employees;
```

**触发器**：在特定事件（INSERT/UPDATE/DELETE）发生时自动执行的特殊存储过程，常用于审计、级联操作、数据校验。

这三类对象的代码以数据库特定语言编写：
- **MySQL**：SQL/PSM
- **PostgreSQL**：PL/pgSQL
- **Oracle**：PL/SQL
- **SQL Server**：T-SQL

#### 例子

**查看存储过程：**

1. 在 Database Navigator 中展开 `Procedures` 节点
2. 双击存储过程，查看代码：
   ```sql
   CREATE PROCEDURE update_salary(
       IN emp_id INT,
       IN new_salary DECIMAL(10,2)
   )
   BEGIN
       UPDATE employees 
       SET salary = new_salary 
       WHERE id = emp_id;
       
       INSERT INTO salary_log (emp_id, old_salary, new_salary, change_time)
       SELECT emp_id, salary, new_salary, NOW() 
       FROM employees WHERE id = emp_id;
   END;
   ```

**执行存储过程：**

1. 右键存储过程 → Execute
2. 输入参数：
   ```
   emp_id: 1
   new_salary: 20000
   ```
3. 查看执行结果

或在 SQL 编辑器中调用：
```sql
CALL update_salary(1, 20000);
```

**创建函数：**

```sql
CREATE FUNCTION calculate_bonus(
    emp_id INT,
    bonus_rate DECIMAL(3,2)
) RETURNS DECIMAL(10,2)
DETERMINISTIC
BEGIN
    DECLARE emp_salary DECIMAL(10,2);
    SELECT salary INTO emp_salary FROM employees WHERE id = emp_id;
    RETURN emp_salary * bonus_rate;
END;
```

在 DBeaver 中创建：
1. 右键 Functions → Create New Function
2. 编写函数代码
3. 保存执行

**触发器示例：**

```sql
CREATE TRIGGER trg_audit_employee
AFTER UPDATE ON employees
FOR EACH ROW
BEGIN
    INSERT INTO employee_audit (
        emp_id, action, old_salary, new_salary, audit_time
    ) VALUES (
        NEW.id, 'UPDATE', OLD.salary, NEW.salary, NOW()
    );
END;
```

在 DBeaver 中：
1. 右键 Triggers → Create New Trigger
2. 配置触发事件（INSERT/UPDATE/DELETE）
3. 配置触发时机（BEFORE/AFTER）
4. 编写触发器代码

**调试存储过程（企业版功能）：**

1. 右键存储过程 → Debug
2. 设置断点
3. 输入参数
4. 单步执行，查看变量值
5. 查看调用栈

**对象依赖关系：**

右键存储过程 → Dependencies，查看：
- 引用了哪些表、视图
- 被哪些其他对象调用

修改表结构前，检查是否有存储过程依赖，避免失效。

#### 总结

- 存储过程、函数、触发器是数据库的可编程对象
- 存储过程预编译，性能好；函数可嵌入 SQL；触发器自动执行
- DBeaver 支持查看、编辑、执行这些对象
- 企业版支持调试存储过程
- 修改表结构前需检查对象依赖关系

---

## 第 6 章 ER 图与数据建模

ER 图（Entity-Relationship Diagram）是数据库设计的可视化工具，展示表与表之间的关系。本章讲解 DBeaver 的 ER 图功能和数据建模能力。

### 第 21 讲：ER 图生成与查看

#### 概念

ER 图（实体关系图）以图形方式展示数据库表结构及表间关系。DBeaver 能自动从现有数据库生成 ER 图，也支持手动创建自定义图。ER 图是理解复杂数据库结构的利器，特别适合接手新项目时快速了解数据模型。

#### 原理

DBeaver 生成 ER 图的原理：
1. **元数据采集**：查询系统目录，获取表、列、主键、外键信息
2. **关系构建**：根据外键定义，建立表间连线
3. **布局算法**：使用力导向算法（Force-directed）或层次布局，自动排列表的位置
4. **渲染**：将表绘制为矩形，列绘制为行，关系绘制为连线

ER 图的元素：
- **实体（Entity）**：表，显示为矩形
- **属性（Attribute）**：列，显示在矩形内
- **关系（Relationship）**：外键，显示为连线
- **基数（Cardinality）**：1:1、1:N、N:M，显示在连线两端

#### 例子

**生成 ER 图：**

1. 右键 schema → Create New ER Diagram
2. 选择要包含的表（可全选或部分）
3. 点击 "Finish"，DBeaver 自动生成 ER 图

**ER 图示例：**

```
┌─────────────────┐         ┌──────────────────┐
│   employees     │         │   departments    │
├─────────────────┤         ├──────────────────┤
│ PK id          │◄───────│ PK id            │
│    name        │  N:1    │    department_name│
│ FK department_id│         │    location      │
│    salary      │         │    manager_id    │
│    hire_date   │         └──────────────────┘
└─────────────────┘                  │
        │                            │
        │ N:1                        │ 1:1
        ▼                            ▼
┌─────────────────┐         ┌──────────────────┐
│  projects      │         │   managers      │
├─────────────────┤         ├──────────────────┤
│ PK id          │         │ PK id           │
│    name        │         │    name         │
│ FK leader_id   │         │    level        │
└─────────────────┘         └──────────────────┘
```

**ER 图操作：**

1. **缩放与平移**
   - 滚轮缩放
   - 按住中键拖动平移
   - `Ctrl+0` 适应窗口

2. **表操作**
   - 双击表，跳转到表编辑器
   - 右键表 → Hide 隐藏
   - 拖动表调整位置

3. **关系操作**
   - 鼠标悬停连线，显示关系详情
   - 右键连线 → Delete 删除关系（不删除外键）

4. **布局调整**
   - 右键 → Layout → Hierarchical（层次布局）
   - 右键 → Layout → Organic（有机布局）

**自定义 ER 图：**

1. 创建空 ER 图：右键 schema → Create New ER Diagram
2. 从 Navigator 拖拽表到图中
3. 手动添加关系：右键表 → Add Relation → 选择目标表
4. 保存为 .erd 文件，便于分享

**导出 ER 图：**

右键 ER 图 → Export Diagram：
- **PNG**：位图格式，适合文档插图
- **SVG**：矢量格式，可缩放不失真
- **PDF**：适合打印
- **GraphML**：可被其他工具编辑

**ER 图的应用场景：**

1. **接手新项目**：快速了解数据库结构
2. **设计评审**：与团队讨论数据模型
3. **文档编写**：生成数据库设计文档
4. **影响分析**：评估修改表结构的影响范围

#### 总结

- ER 图以图形方式展示表结构和关系
- DBeaver 自动从数据库元数据生成 ER 图
- 支持缩放、平移、布局调整等操作
- 可导出为 PNG、SVG、PDF 等格式
- 适合接手新项目、设计评审、文档编写

---

### 第 22 讲：数据建模与导出

#### 概念

数据建模（Data Modeling）是从需求出发设计数据库结构的过程。DBeaver 企业版提供完整的建模工具，支持正向工程（模型→数据库）和逆向工程（数据库→模型）。社区版虽无完整建模功能，但可通过 ER 图和 DDL 生成实现基本建模。

#### 原理

数据建模的三个层次：
1. **概念模型**：实体和关系，不涉及具体字段
2. **逻辑模型**：属性、主键、外键，与具体数据库无关
3. **物理模型**：具体数据类型、索引、约束，针对特定数据库

正向工程流程：
```
需求分析 → 概念模型 → 逻辑模型 → 物理模型 → DDL → 数据库
```

逆向工程流程：
```
数据库 → 元数据 → 物理模型 → 逻辑模型 → 概念模型
```

DBeaver 主要支持逆向工程（从数据库生成模型），正向工程能力有限（需借助 ER 图手动设计）。

#### 例子

**逆向工程（从数据库生成模型）：**

1. 右键 schema → Generate SQL → DDL
2. 生成完整建表语句：
   ```sql
   -- Schema: hr
   CREATE SCHEMA hr;
   
   CREATE TABLE hr.employees (
       id SERIAL PRIMARY KEY,
       name VARCHAR(100) NOT NULL,
       department_id INTEGER REFERENCES hr.departments(id),
       salary DECIMAL(10,2),
       hire_date DATE DEFAULT CURRENT_DATE
   );
   
   CREATE TABLE hr.departments (
       id SERIAL PRIMARY KEY,
       department_name VARCHAR(50) NOT NULL,
       location VARCHAR(100)
   );
   
   CREATE INDEX idx_emp_dept ON hr.employees(department_id);
   ```

3. 同时生成 ER 图，可视化展示模型

**正向工程（从模型生成数据库）：**

1. 创建 ER 图，手动设计表结构
2. 右键 ER 图 → Generate SQL → DDL
3. 生成建表语句
4. 在目标数据库执行

**模型导出格式：**

1. **DDL 脚本**：标准 SQL，可跨数据库执行
   ```
   Export → SQL DDL → schema.sql
   ```

2. **GraphML**：图形格式，可被 yEd、Gephi 编辑
   ```
   Export → GraphML → model.graphml
   ```

3. **HTML 报告**：包含表结构、关系、注释的网页
   ```
   Export → HTML Report → schema.html
   ```

4. **Markdown 文档**：表格形式展示结构
   ```
   Export → Markdown → schema.md
   ```

**HTML 报告示例：**

生成的 HTML 包含：
- 表清单（带链接）
- 每张表的列定义、类型、注释
- 外键关系图
- 索引和约束信息

适合作为数据库设计文档分享给团队。

**模型版本管理：**

将 DDL 脚本纳入版本控制（Git）：
```
db/
  schema/
    001_create_tables.sql
    002_add_indexes.sql
    003_add_constraints.sql
  erd/
    hr.erd
    sales.erd
```

每次修改数据库结构，生成新的迁移脚本，便于追踪变更历史。

**跨数据库迁移：**

DBeaver 的结构比较功能可辅助跨数据库迁移：
1. 在源数据库生成 DDL
2. 在目标数据库执行（注意方言差异）
3. 使用结构比较，检查差异并修正

#### 总结

- 数据建模分概念、逻辑、物理三个层次
- DBeaver 主要支持逆向工程，从数据库生成模型
- 正向工程可通过 ER 图手动设计后生成 DDL
- 模型可导出为 DDL、GraphML、HTML、Markdown 等格式
- 建议将 DDL 纳入版本控制，追踪结构变更

---

## 第 7 章 数据传输与同步

数据迁移与同步是数据库运维的核心任务。本章讲解 DBeaver 的数据传输功能，包括跨数据库迁移、数据比较与同步、结构比较。

### 第 23 讲：数据库迁移

#### 概念

数据库迁移（Database Migration）是将数据从一个数据库转移到另一个数据库的过程，常见场景包括：数据库升级、跨数据库平台迁移、数据归档、环境同步。DBeaver 提供图形化的迁移向导，支持同构和异构数据库迁移。

#### 原理

DBeaver 的数据迁移基于 **ETL（Extract-Transform-Load）** 模型：
1. **Extract（抽取）**：从源数据库读取数据，通过 JDBC ResultSet 流式读取
2. **Transform（转换）**：根据目标数据库类型转换数据格式
   - 数据类型映射（如 MySQL DATETIME → PostgreSQL TIMESTAMP）
   - 字符编码转换
   - NULL 值处理
3. **Load（加载）**：写入目标数据库，使用批量插入提升性能

迁移过程中，DBeaver 维护一个**映射表**，记录源列与目标列的对应关系。对于异构迁移，还需处理 SQL 方言差异（如自增主键、序列、触发器）。

#### 例子

**同构迁移（MySQL → MySQL）：**

1. 右键源表 → Export Data
2. 选择目标：另一个 MySQL 连接
3. 配置：
   ```
   Source: localhost/hr.employees
   Target: production/hr.employees
   Mode: Insert （或 Replace, Append）
   Batch size: 1000
   ```
4. 执行迁移

**异构迁移（MySQL → PostgreSQL）：**

1. 在目标 PostgreSQL 创建对应表（结构相同或使用 DDL 生成）
2. 右键源表 → Export Data → 选择 PostgreSQL 连接
3. 列映射：
   ```
   MySQL 列              PostgreSQL 列
   id INT AUTO_INC   →   id SERIAL
   name VARCHAR(100)  →   name VARCHAR(100)
   salary DECIMAL     →   salary NUMERIC
   hire_date DATETIME →   hire_date TIMESTAMP
   ```
4. 数据类型自动转换
5. 执行迁移

**整库迁移：**

1. 右键源 schema → Tools → Copy Schema
2. 选择目标连接和 schema
3. 配置选项：
   ```
   Copy tables: ✓
   Copy data: ✓
   Copy views: ✓
   Copy indexes: ✓
   Copy constraints: ✓
   Copy triggers: ✓
   ```
4. 执行迁移，DBeaver 按依赖顺序创建对象

**迁移向导高级选项：**

- **Drop target if exists**：目标存在则先删除
- **Create target objects**：自动创建目标表结构
- **Truncate before load**：清空目标表后插入
- **Use transactions**：使用事务，失败可回滚
- **Continue on error**：错误时继续，记录失败行

**增量迁移：**

只迁移新增或修改的数据：
```sql
-- 使用 WHERE 条件
SELECT * FROM employees 
WHERE updated_at > '2024-01-01';
```
配置迁移时添加 WHERE 条件，只迁移符合条件的数据。

**迁移性能优化：**

1. **增大 Batch Size**：1000-5000 行/批
2. **关闭索引**：迁移前禁用，迁移后重建
3. **关闭约束**：临时禁用外键检查
4. **并行迁移**：多表并行迁移（需手动配置）
5. **使用 COPY 命令**（PostgreSQL）：比 INSERT 快 10 倍

**迁移验证：**

迁移后验证数据一致性：
```sql
-- 行数对比
SELECT COUNT(*) FROM source.employees;
SELECT COUNT(*) FROM target.employees;

-- 校验和对比
SELECT SUM(id), COUNT(*) FROM source.employees;
SELECT SUM(id), COUNT(*) FROM target.employees;

-- 抽样对比
SELECT * FROM source.employees ORDER BY id LIMIT 100;
SELECT * FROM target.employees ORDER BY id LIMIT 100;
```

#### 总结

- 数据库迁移基于 ETL 模型：抽取、转换、加载
- 支持同构和异构迁移，自动处理数据类型映射
- 整库迁移按依赖顺序创建对象
- 增量迁移通过 WHERE 条件实现
- 迁移后需验证数据一致性

---

### 第 24 讲：数据比较与同步

#### 概念

数据比较（Data Compare）是对比两个数据库中表数据差异的功能，常用于验证迁移正确性、同步主从库数据。DBeaver 能逐行对比数据，生成差异报告，并支持将差异同步到目标。

#### 原理

DBeaver 的数据比较算法：
1. **主键识别**：通过主键或唯一索引确定行的唯一性
2. **分批读取**：按主键排序，分批从源和目标读取
3. **归并比较**：类似归并排序，按主键顺序对比两侧数据
4. **差异分类**：
   - **Source only**：源有目标无（需 INSERT）
   - **Target only**：目标有源无（需 DELETE）
   - **Different**：两侧都有但内容不同（需 UPDATE）
5. **生成同步脚本**：根据差异生成 INSERT/UPDATE/DELETE 语句

对于无主键的表，数据比较无法进行（无法确定行唯一性）。

#### 例子

**比较两个表的数据：**

1. 右键源表 → Tools → Compare Data
2. 选择目标连接和表
3. 配置：
   ```
   Source: dev.employees
   Target: prod.employees
   Compare by: Primary Key （或指定列）
   Ignore columns: updated_at （忽略时间戳列）
   ```
4. 执行比较

**差异报告：**

```
Data Compare Results
====================
Source: dev.employees (1000 rows)
Target: prod.employees (998 rows)

Summary:
  Identical:   995 rows
  Different:     3 rows
  Source only:   2 rows
  Target only:   0 rows

Details:
[Different]
  ID: 5
    Source: name='张三', salary=18000
    Target: name='张三', salary=15000

[Source only]
  ID: 1001
    name='赵六', salary=20000
  ID: 1002
    name='钱七', salary=22000
```

**同步差异：**

1. 在差异报告中，选择要同步的差异
2. 选择同步方向：Source → Target 或 Target → Source
3. 生成同步脚本：
   ```sql
   -- UPDATE
   UPDATE employees SET salary = 18000 WHERE id = 5;
   
   -- INSERT
   INSERT INTO employees (id, name, salary) VALUES (1001, '赵六', 20000);
   INSERT INTO employees (id, name, salary) VALUES (1002, '钱七', 22000);
   ```
4. 选择：
   - **Preview SQL**：只查看脚本，不执行
   - **Execute**：直接执行同步
   - **Save Script**：保存为 .sql 文件，后续执行

**忽略列配置：**

某些列不应参与比较：
- **时间戳列**（updated_at）：每次修改都变，无法对比
- **自增列**（id）：可能不一致但无意义
- **计算列**：依赖其他列

配置：
```
Ignore columns: updated_at, created_at
```

**批量比较：**

比较多个表：
1. 选中多张表
2. 右键 → Compare Data
3. 选择目标连接
4. 逐表比较，生成汇总报告

**定时同步：**

通过命令行实现定时同步：
```bash
dbeaver-cli -con "mysql:dev" -con "mysql:prod" \
  -compareData "dev.employees" "prod.employees" \
  -syncDirection "source-to-target" \
  -output "/var/log/sync.log"
```
结合 cron 定时执行，实现定期同步。

#### 总结

- 数据比较通过主键识别行，归并算法对比差异
- 差异分为相同、不同、源独有、目标独有四类
- 可生成同步脚本，支持预览、执行、保存
- 可忽略时间戳、自增等列
- 支持批量比较和命令行定时同步

---

### 第 25 讲：结构比较

#### 概念

结构比较（Structure Compare）是对比两个数据库的表结构差异，包括表、列、索引、约束等。常用于同步开发库与生产库结构、追踪结构变更、跨环境部署。

#### 原理

DBeaver 的结构比较基于**元数据对比**：
1. **采集元数据**：分别从源和目标查询系统目录
2. **对象匹配**：按对象名匹配（如表名相同才比较）
3. **属性对比**：逐属性对比（列名、类型、长度、约束等）
4. **差异分类**：
   - **新增对象**：源有目标无
   - **删除对象**：目标有源无
   - **修改对象**：两侧都有但属性不同
5. **生成 DDL**：根据差异生成 ALTER 语句

#### 例子

**比较两个 schema 的结构：**

1. 右键源 schema → Compare Structure
2. 选择目标连接和 schema
3. 配置比较选项：
   ```
   Compare tables: ✓
   Compare views: ✓
   Compare indexes: ✓
   Compare constraints: ✓
   Compare triggers: ✓
   Ignore column order: ✓ （忽略列顺序差异）
   Ignore comments: ✗ （比较注释）
   ```
4. 执行比较

**差异报告：**

```
Structure Compare Results
=========================
Source: dev.hr
Target: prod.hr

Objects only in source:
  TABLE: employees_audit （新增表）
  INDEX: idx_emp_email （新增索引）

Objects only in target:
  VIEW: v_old_report （已废弃）

Objects with differences:
  TABLE: employees
    Column added: email VARCHAR(200)
    Column modified: salary DECIMAL(10,2) → DECIMAL(12,2)
    Index added: idx_emp_dept
```

**生成同步脚本：**

根据差异生成 ALTER 语句：
```sql
-- 新增表
CREATE TABLE employees_audit (
    id INT PRIMARY KEY,
    emp_id INT,
    action VARCHAR(20),
    audit_time TIMESTAMP
);

-- 修改表
ALTER TABLE employees ADD COLUMN email VARCHAR(200);
ALTER TABLE employees ALTER COLUMN salary TYPE DECIMAL(12,2);
CREATE INDEX idx_emp_dept ON employees(department_id);

-- 删除对象
DROP VIEW v_old_report;
```

**同步选项：**

- **Generate script only**：只生成脚本，不执行
- **Execute immediately**：直接执行
- **Save to file**：保存为迁移脚本

**结构同步最佳实践：**

1. **先备份**：同步前备份目标数据库
2. **预览脚本**：仔细检查生成的 DDL
3. **分批执行**：复杂变更分批执行，便于回滚
4. **测试环境验证**：先在测试环境执行，确认无误再上生产

**版本化结构变更：**

将结构比较生成的脚本纳入版本控制：
```
migrations/
  001_initial_schema.sql
  002_add_email_column.sql
  003_create_audit_table.sql
  004_drop_old_view.sql
```

每次结构变更都生成新的迁移脚本，按顺序执行，便于追踪和回滚。

**跨数据库结构比较：**

DBeaver 支持跨数据库类型比较（如 MySQL vs PostgreSQL），但需注意：
- 数据类型映射（INT vs INTEGER）
- 语法差异（AUTO_INCREMENT vs SERIAL）
- 特有功能（如 PostgreSQL 的 JSONB）

比较结果会标注不兼容的部分，需手动处理。

#### 总结

- 结构比较基于元数据对比，识别对象差异
- 差异分为新增、删除、修改三类
- 生成 ALTER 语句用于同步结构
- 建议先备份、预览脚本、测试环境验证
- 迁移脚本应纳入版本控制

---

## 第 8 章 高级功能

本章讲解 DBeaver 的高级功能，包括事务管理、自定义 SQL 模板、脚本与批处理，帮助您提升工作效率。

### 第 26 讲：事务管理

#### 概念

事务（Transaction）是数据库操作的逻辑单元，具有 ACID 特性：原子性（Atomicity）、一致性（Consistency）、隔离性（Isolation）、持久性（Durability）。DBeaver 提供图形化的事务管理功能，支持手动提交/回滚、隔离级别设置、保存点管理。

#### 原理

**事务的生命周期：**
```
BEGIN → SQL 操作 → COMMIT（提交）或 ROLLBACK（回滚）
```

**自动提交模式：**
- 每条 SQL 自动作为一个事务提交
- 无法回滚，适合简单查询
- DBeaver 默认开启自动提交

**手动提交模式：**
- 显式 BEGIN 开始事务
- 多条 SQL 在同一事务中
- 手动 COMMIT 或 ROLLBACK
- 适合需要原子性的操作

**隔离级别：**
- **READ UNCOMMITTED**：可读未提交数据（脏读）
- **READ COMMITTED**：只能读已提交数据（默认）
- **REPEATABLE READ**：同一事务多次读取结果一致
- **SERIALIZABLE**：完全串行化，最高隔离级别

**保存点（Savepoint）：**
事务中的标记，可回滚到保存点而非整个事务：
```sql
SAVEPOINT sp1;
-- 一些操作
ROLLBACK TO sp1;  -- 回滚到 sp1，sp1 后的操作撤销
```

#### 例子

**切换事务模式：**

1. 在 SQL 编辑器工具栏，点击 "Auto-commit" 切换
2. 或快捷键 `Alt+C`
3. 状态栏显示当前模式：
   - `Auto-commit: ON`：自动提交
   - `Auto-commit: OFF`：手动提交

**手动事务示例：**

```sql
-- 关闭自动提交
-- Auto-commit: OFF

BEGIN;

-- 转账操作
UPDATE accounts SET balance = balance - 1000 WHERE id = 1;
UPDATE accounts SET balance = balance + 1000 WHERE id = 2;

-- 检查余额
SELECT balance FROM accounts WHERE id = 1;
-- 结果: 4000 （足够）

-- 提交事务
COMMIT;
-- 或回滚
-- ROLLBACK;
```

**工具栏事务按钮：**

- **Commit**（✓）：提交当前事务
- **Rollback**（✗）：回滚当前事务
- **Toggle Auto-commit**：切换模式

**隔离级别设置：**

1. 右键连接 → Edit Connection → Isolation Level
2. 选择级别：
   ```
   READ COMMITTED （默认，推荐）
   REPEATABLE READ
   SERIALIZABLE
   ```
3. 或在 SQL 中设置：
   ```sql
   SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
   BEGIN;
   -- 操作
   COMMIT;
   ```

**保存点示例：**

```sql
BEGIN;

INSERT INTO orders (id, customer_id, amount) VALUES (1, 100, 500);
SAVEPOINT sp1;

INSERT INTO order_items (order_id, product_id, qty) VALUES (1, 10, 2);
-- 假设这里出错
ROLLBACK TO sp1;  -- 回滚到 sp1，orders 保留，order_items 撤销

-- 重新插入
INSERT INTO order_items (order_id, product_id, qty) VALUES (1, 10, 3);

COMMIT;
```

**事务超时：**

长事务可能阻塞其他操作，设置超时：
```
Connection settings → Transaction timeout: 300s
```
超时后自动回滚，避免长时间占用锁。

**死锁检测：**

DBeaver 能检测死锁并提示：
```
Deadlock detected!
Transaction was deadlocked on lock resources with another process.
[Rollback] [Retry]
```

**事务监控：**

查看当前活动事务：
```sql
-- MySQL
SELECT * FROM information_schema.innodb_trx;

-- PostgreSQL
SELECT * FROM pg_stat_activity WHERE state = 'active';
```

#### 总结

- 事务具有 ACID 特性，保证操作原子性
- 自动提交模式简单但无法回滚，手动模式可控
- 隔离级别影响并发性能和数据一致性
- 保存点允许部分回滚，灵活处理错误
- 长事务需设置超时，避免阻塞

---

### 第 27 讲：自定义 SQL 模板与片段

#### 概念

SQL 模板（SQL Template）是预定义的 SQL 代码片段，通过快捷方式快速插入常用代码。DBeaver 内置常用模板，也支持自定义。这是提升编码效率的利器，特别适合频繁编写相似 SQL 的场景。

#### 原理

DBeaver 的模板系统基于**代码片段管理**：
1. **模板定义**：包含 SQL 代码和占位符变量
2. **触发方式**：输入模板名 + `Ctrl+Space`
3. **变量填充**：Tab 键在变量间跳转，填入实际值
4. **插入代码**：生成完整 SQL

模板存储在 XML 文件中，可导入导出，便于团队共享。

#### 例子

**使用内置模板：**

输入 `sel` + `Ctrl+Space`，提示：
```
select - SELECT * FROM
select_count - SELECT COUNT(*) FROM
select_top - SELECT * FROM ... LIMIT
```

选择 `select`，插入：
```sql
SELECT * FROM ${table_name}
```
`${table_name}` 是变量，输入表名后 Tab 跳到下一个变量。

**自定义模板：**

1. `Preferences → Editors → SQL Editor → Templates`
2. 点击 "New"
3. 配置：
   ```
   Name: select_by_id
   Description: 按 ID 查询
   Context: SQL
   Pattern:
   SELECT * FROM ${table}
   WHERE id = ${id:INT}
   ```

4. 使用：输入 `select_by_id` + `Ctrl+Space`

**复杂模板示例：**

```
Name: create_table_audit
Description: 创建审计表
Pattern:
CREATE TABLE ${table}_audit (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    record_id BIGINT NOT NULL,
    action VARCHAR(20) NOT NULL,
    changed_by VARCHAR(100),
    changed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    old_values JSON,
    new_values JSON,
    COMMENT = '审计表：${table}'
);

CREATE INDEX idx_${table}_audit_record ON ${table}_audit(record_id);
CREATE INDEX idx_${table}_audit_time ON ${table}_audit(changed_at);
```

**模板变量类型：**

- `${var}`：通用变量
- `${var:INT}`：整数类型，提示输入整数
- `${var:table}`：表名，自动补全表名
- `${var:column(table=emp)}`：列名，根据表补全列
- `${cursor}`：插入后光标位置

**模板导入导出：**

1. 导出：`Preferences → Templates → Export`，保存为 XML
2. 导入：`Preferences → Templates → Import`，加载 XML
3. 团队共享：将 XML 放入版本控制，团队成员导入

**SQL 片段（Snippet）：**

除了模板，DBeaver 还有"SQL 片段"功能，类似代码收藏夹：
1. 选中 SQL 代码
2. 右键 → Save as Snippet
3. 在 Snippet 视图中分类管理
4. 双击片段插入到编辑器

**常用模板推荐：**

1. **分页查询**
   ```
   Name: paginate
   SELECT * FROM ${table}
   ORDER BY ${order_col}
   LIMIT ${limit:INT} OFFSET ${offset:INT}
   ```

2. **UPSERT**
   ```
   Name: upsert
   INSERT INTO ${table} (${columns})
   VALUES (${values})
   ON DUPLICATE KEY UPDATE
   ${update_clause}
   ```

3. **CTE 模板**
   ```
   Name: cte
   WITH ${cte_name} AS (
       ${cte_query}
   )
   SELECT * FROM ${cte_name}
   ```

4. **CASE WHEN**
   ```
   Name: case_when
   CASE 
       WHEN ${condition1} THEN ${value1}
       WHEN ${condition2} THEN ${value2}
       ELSE ${default}
   END
   ```

#### 总结

- SQL 模板通过快捷方式快速插入常用代码
- 支持变量占位符，Tab 键跳转填充
- 可自定义模板，导入导出便于团队共享
- SQL 片段类似代码收藏夹，分类管理
- 建议为常用 SQL 模式创建模板，提升编码效率

---

### 第 28 讲：脚本与批处理

#### 概念

脚本（Script）和批处理（Batch Processing）是自动化数据库操作的手段。DBeaver 支持执行 SQL 脚本文件、命令行批处理、定时任务，适合数据初始化、定期维护、批量操作等场景。

#### 原理

DBeaver 的脚本执行基于**语句解析器**：
1. **读取脚本**：从 .sql 文件读取内容
2. **语句分割**：按分隔符（默认 `;`）分割为单条语句
3. **逐条执行**：按顺序执行每条语句
4. **错误处理**：遇错时继续或中止（可配置）
5. **日志记录**：记录每条语句的执行结果

对于存储过程等包含 `;` 的语句，需使用 DELIMITER 切换分隔符：
```sql
DELIMITER //
CREATE PROCEDURE my_proc()
BEGIN
    -- 这里的 ; 不会分割语句
    SELECT * FROM employees;
END//
DELIMITER ;
```

#### 例子

**执行 SQL 脚本文件：**

1. `File → Open File`，打开 .sql 文件
2. 选择数据库连接
3. 按 `Alt+X` 执行全部
4. 或选中部分，按 `Ctrl+Enter` 执行选中

**脚本示例（init_db.sql）：**

```sql
-- 创建数据库
CREATE DATABASE IF NOT EXISTS hr_system;
USE hr_system;

-- 创建表
CREATE TABLE departments (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50) NOT NULL,
    location VARCHAR(100)
);

CREATE TABLE employees (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    department_id INT,
    salary DECIMAL(10,2),
    hire_date DATE,
    FOREIGN KEY (department_id) REFERENCES departments(id)
);

-- 插入初始数据
INSERT INTO departments (name, location) VALUES
    ('研发部', '北京'),
    ('市场部', '上海'),
    ('财务部', '广州');

INSERT INTO employees (name, department_id, salary, hire_date) VALUES
    ('张三', 1, 15000, '2023-01-15'),
    ('李四', 2, 12000, '2023-03-20'),
    ('王五', 1, 18000, '2022-11-01');

-- 创建索引
CREATE INDEX idx_emp_dept ON employees(department_id);
CREATE INDEX idx_emp_salary ON employees(salary);

-- 验证
SELECT COUNT(*) FROM employees;
SELECT COUNT(*) FROM departments;
```

**命令行执行：**

DBeaver 提供命令行工具 `dbeaver-cli`：
```bash
# 执行 SQL 文件
dbeaver-cli -con "mysql:localhost:3306/hr" -user "root" -password "123456" \
  -script "/path/to/init_db.sql" \
  -log "/var/log/init.log"

# 执行单条 SQL
dbeaver-cli -con "mysql:localhost:3306/hr" -user "root" -password "123456" \
  -sql "SELECT * FROM employees"
```

**批处理脚本（Shell）：**

结合 Shell 脚本实现批处理：
```bash
#!/bin/bash
# backup.sh - 数据库备份脚本

DATE=$(date +%Y%m%d)
BACKUP_DIR="/backup/$DATE"
mkdir -p $BACKUP_DIR

# 导出所有表
TABLES=$(dbeaver-cli -con "mysql:prod" -user "root" -password "123456" \
  -sql "SHOW TABLES" -format csv | tail -n +2)

for TABLE in $TABLES; do
    dbeaver-cli -con "mysql:prod" -user "root" -password "123456" \
      -sql "SELECT * FROM $TABLE" \
      -output "$BACKUP_DIR/$TABLE.csv" \
      -format csv
done

echo "Backup completed: $BACKUP_DIR"
```

**定时任务（Cron）：**

```bash
# 每天凌晨 2 点备份
0 2 * * * /path/to/backup.sh

# 每周日凌晨 3 点统计数据
0 3 * * 0 dbeaver-cli -con "mysql:prod" -script "/scripts/weekly_stats.sql"
```

**DBeaver 内置任务调度：**

企业版支持内置任务调度：
1. `Tools → Task Scheduler`
2. 创建任务：
   ```
   Name: 每日备份
   Type: Execute SQL Script
   Script: /scripts/backup.sql
   Schedule: Daily 02:00
   Output: /var/log/backup.log
   ```
3. 任务自动执行，记录日志

**脚本错误处理：**

```sql
-- 配置遇错行为
@onError continue;  -- 继续
@onError abort;     -- 中止
@onError rollback;  -- 回滚事务

-- 示例
@onError continue;
INSERT INTO invalid_table VALUES (1);  -- 会失败
INSERT INTO valid_table VALUES (1);    -- 继续执行
```

**脚本变量化：**

```sql
@set table_name = 'employees';
@set limit = 100;

SELECT * FROM ${table_name} LIMIT ${limit};
```

便于参数化执行，无需修改脚本内容。

#### 总结

- 脚本执行基于语句分割，逐条执行
- DELIMITER 用于处理存储过程中的分号
- 命令行工具 dbeaver-cli 支持自动化
- 结合 Shell 和 Cron 实现定时批处理
- 企业版内置任务调度，图形化管理
- 脚本可变量化，便于参数化执行

---

## 第 9 章 性能分析与优化

性能是数据库应用的关键指标。本章讲解如何用 DBeaver 分析执行计划、监控性能、提出优化建议，帮助您定位和解决慢查询问题。

### 第 29 讲：执行计划分析

#### 概念

执行计划（Execution Plan）是数据库执行 SQL 语句的详细步骤，包括访问路径、连接方式、索引使用、排序操作等。通过分析执行计划，能定位慢查询的瓶颈，指导优化方向。DBeaver 提供图形化的执行计划查看器，比纯文本更直观。

#### 原理

数据库执行 SQL 的流程：
```
SQL 文本 → 解析器 → 语法树 → 优化器 → 执行计划 → 执行引擎 → 结果
```

**优化器**（Optimizer）是核心，它基于统计信息（行数、唯一性、分布）估算各种执行方案的成本，选择最优方案。成本包括：
- **CPU 成本**：运算次数
- **IO 成本**：磁盘读写次数
- **内存成本**：排序、哈希所需内存

执行计划的常见操作：
- **Table Scan**：全表扫描，最慢
- **Index Scan**：索引扫描，较快
- **Index Seek**：索引查找，最快
- **Nested Loop Join**：嵌套循环连接，适合小表
- **Hash Join**：哈希连接，适合大表
- **Merge Join**：归并连接，适合已排序数据
- **Sort**：排序操作，消耗内存
- **Hash Aggregate**：哈希聚合

#### 例子

**查看执行计划：**

1. 在 SQL 编辑器中编写查询
2. 按 `Ctrl+Shift+E` 或点击 "Explain" 按钮
3. 切换到 "Execution Plan" 标签

**示例查询：**

```sql
SELECT e.name, d.department_name, e.salary
FROM employees e
JOIN departments d ON e.department_id = d.id
WHERE e.salary > 10000
ORDER BY e.salary DESC;
```

**图形化执行计划：**

```
┌─────────────────────────────────┐
│ Sort (salary DESC)              │ Cost: 1000
│   ┌───────────────────────────┐ │
│   │ Hash Join                  │ │ Cost: 800
│   │   ├─ Seq Scan (employees)  │ │ Cost: 500
│   │   │   Filter: salary>10000│ │
│   │   └─ Seq Scan (departments)│ │ Cost: 100
│   └───────────────────────────┘ │
└─────────────────────────────────┘
```

**关键指标解读：**

| 指标 | 含义 | 优化方向 |
|------|------|---------|
| Cost | 成本估算 | 越小越好 |
| Rows | 预估行数 | 与实际对比 |
| Actual Rows | 实际行数 | 偏差大说明统计过期 |
| Loops | 循环次数 | 高需优化 |
| Filter | 过滤条件 | 检查是否用索引 |
| Sort | 排序 | 检查是否可避免 |

**MySQL 执行计划示例：**

```sql
EXPLAIN SELECT * FROM employees WHERE department_id = 1;
```

结果：
```
id | select_type | table     | type | key           | rows | Extra
1  | SIMPLE      | employees | ref  | idx_emp_dept  | 50   | Using index condition
```

字段解读：
- **type: ref**：使用了索引查找（好）
- **type: ALL**：全表扫描（差，需优化）
- **key**：使用的索引名
- **rows**：预估扫描行数
- **Extra**：附加信息
  - `Using index`：覆盖索引，最优
  - `Using filesort`：额外排序，需优化
  - `Using temporary`：使用临时表，需优化

**PostgreSQL 执行计划：**

```sql
EXPLAIN ANALYZE SELECT * FROM employees WHERE salary > 10000;
```

```
Seq Scan on employees  (cost=0.00..35.50 rows=10 width=100) (actual time=0.020..0.450 rows=15 loops=1)
  Filter: (salary > 10000)
  Rows Removed by Filter: 985
Planning Time: 0.150 ms
Execution Time: 0.500 ms
```

- **Seq Scan**：全表扫描，未用索引
- **actual time**：实际耗时
- **Rows Removed by Filter**：被过滤的行数

**优化案例：**

**问题查询：**
```sql
SELECT * FROM employees WHERE department_id = 1;
-- 执行计划：Seq Scan，扫描 1000 行
```

**添加索引后：**
```sql
CREATE INDEX idx_emp_dept ON employees(department_id);
-- 执行计划：Index Scan，扫描 50 行
```

**进一步优化（覆盖索引）：**
```sql
CREATE INDEX idx_emp_dept_covering ON employees(department_id, name, salary);
-- 执行计划：Index Only Scan，无需回表
```

**统计信息更新：**

执行计划依赖统计信息，过期会导致错误估算：
```sql
-- PostgreSQL
ANALYZE employees;

-- MySQL
ANALYZE TABLE employees;
```

建议定期更新统计信息，特别是大量数据变更后。

#### 总结

- 执行计划展示 SQL 的执行步骤和成本
- 优化器基于统计信息选择最优方案
- 关键操作：Table Scan（慢）、Index Scan（快）、各种 Join
- 通过 EXPLAIN 或图形化查看器分析
- 优化方向：添加索引、覆盖索引、更新统计信息

---

### 第 30 讲：性能监控与优化建议

#### 概念

性能监控（Performance Monitoring）是持续观察数据库运行状态的过程，包括查询性能、资源使用、锁等待等。DBeaver 提供多种监控工具，并能基于分析给出优化建议。这是数据库运维的高级技能。

#### 原理

数据库性能受多因素影响：
- **查询层面**：SQL 写法、索引使用、执行计划
- **结构层面**：表设计、范式、分区
- **配置层面**：内存分配、连接数、缓存
- **硬件层面**：CPU、内存、磁盘 IO、网络

性能监控通过采集数据库的运行指标，发现瓶颈：
- **慢查询日志**：记录执行时间超过阈值的 SQL
- **进程列表**：查看当前执行的查询
- **锁等待**：检测锁冲突
- **资源使用**：CPU、内存、IO 利用率

DBeaver 的监控面板实时展示这些指标，并提供可视化图表。

#### 例子

**查看活动会话：**

1. 选中连接 → Tools → Server Management
2. 切换到 "Sessions" 或 "Processes" 标签
3. 查看当前活动会话：
   ```
   ID    User    Host         Database  Time    State    Query
   1001  app     10.0.0.1     hr        120s    executing SELECT * FROM large_table WHERE...
   1002  admin   10.0.0.2     hr        5s      sleeping 
   1003  report  10.0.0.3     hr        300s    executing UPDATE employees SET...
   ```

- **Time**：执行时间，长查询需关注
- **State**：会话状态
- **Query**：当前执行的 SQL

**终止慢查询：**

1. 选中会话
2. 点击 "Kill Session" 或 "Terminate"
3. 该会话的查询被中止

**慢查询分析：**

1. 查看 MySQL 慢查询日志：
   ```sql
   SHOW VARIABLES LIKE 'slow_query_log%';
   SHOW VARIABLES LIKE 'long_query_time';
   ```
2. 在 DBeaver 中查看慢查询日志文件
3. 分析慢查询的执行计划

**锁等待监控：**

```sql
-- MySQL
SELECT * FROM information_schema.innodb_lock_waits;
SELECT * FROM information_schema.innodb_trx;

-- PostgreSQL
SELECT * FROM pg_locks WHERE NOT granted;
SELECT * FROM pg_stat_activity WHERE wait_event IS NOT NULL;
```

DBeaver 图形化展示锁等待关系：
```
Session 1001 (UPDATE employees) 
    → holds lock on employees
    → blocks Session 1003 (SELECT employees)
    → blocks Session 1005 (DELETE employees)
```

**性能仪表盘：**

DBeaver 企业版提供性能仪表盘，实时展示：
- **QPS**：每秒查询数
- **TPS**：每秒事务数
- **连接数**：当前连接数 / 最大连接数
- **缓存命中率**：Buffer Pool Hit Rate
- **慢查询数**：每分钟慢查询数量

**优化建议工具：**

DBeaver 能分析表结构和查询，给出优化建议：

1. 右键表 → Tools → Analyze Performance
2. 分析结果：
   ```
   Table: employees (100,000 rows)
   
   Issues Found:
   1. Missing Index
      Query: SELECT * FROM employees WHERE department_id = ?
      Suggestion: CREATE INDEX idx_emp_dept ON employees(department_id)
      Expected Improvement: 95% faster
   
   2. Unused Index
      Index: idx_emp_old (not used in last 30 days)
      Suggestion: DROP INDEX idx_emp_old
      Benefit: Faster writes
   
   3. Data Type Optimization
      Column: id INT (max value: 100000)
      Suggestion: Consider MEDIUMINT (3 bytes vs 4 bytes)
      Benefit: 25% storage savings
   ```

**常见优化技巧：**

1. **避免 SELECT ***
   ```sql
   -- 差
   SELECT * FROM employees;
   -- 好
   SELECT id, name, salary FROM employees;
   ```

2. **使用 LIMIT**
   ```sql
   SELECT * FROM employees LIMIT 100;
   ```

3. **避免函数包裹索引列**
   ```sql
   -- 差（索引失效）
   WHERE YEAR(hire_date) = 2023
   -- 好
   WHERE hire_date >= '2023-01-01' AND hire_date < '2024-01-01'
   ```

4. **使用 EXISTS 代替 IN**
   ```sql
   -- 差
   SELECT * FROM employees WHERE id IN (SELECT emp_id FROM bonuses);
   -- 好
   SELECT * FROM employees e WHERE EXISTS (SELECT 1 FROM bonuses b WHERE b.emp_id = e.id);
   ```

5. **批量操作代替循环**
   ```sql
   -- 差（1000 次单条插入）
   INSERT INTO logs VALUES (1);
   INSERT INTO logs VALUES (2);
   ...
   -- 好（1 次批量插入）
   INSERT INTO logs VALUES (1), (2), ...;
   ```

**定期维护任务：**

```sql
-- 更新统计信息
ANALYZE TABLE employees;

-- 重建碎片化索引
OPTIMIZE TABLE employees;  -- MySQL
REINDEX TABLE employees;   -- PostgreSQL

-- 清理历史数据
DELETE FROM logs WHERE created_at < '2023-01-01';

-- 检查表完整性
CHECK TABLE employees;
```

建议通过 DBeaver 的任务调度，定期执行维护任务。

#### 总结

- 性能监控通过采集运行指标，发现瓶颈
- DBeaver 提供会话查看、锁监控、性能仪表盘等工具
- 可终止慢查询，释放资源
- 优化建议工具能自动分析并给出改进方案
- 常见优化：避免 SELECT *、用 LIMIT、避免函数包裹索引列
- 定期维护：更新统计、重建索引、清理历史数据

---

## 课程总结

恭喜您完成了 DBeaver 系统化教程的全部 30 讲！通过本课程，您已经从零基础成长为 DBeaver 的熟练使用者。

### 学习成果回顾

| 章节 | 核心技能 |
|------|---------|
| 第 1 章 | 安装配置、界面熟悉、全局设置 |
| 第 2 章 | 连接管理、驱动下载、连接池配置 |
| 第 3 章 | SQL 编辑、自动补全、格式化、参数化查询 |
| 第 4 章 | 数据浏览、编辑、导入导出 |
| 第 5 章 | 表、视图、索引、约束、存储过程管理 |
| 第 6 章 | ER 图生成、数据建模、模型导出 |
| 第 7 章 | 数据库迁移、数据比较、结构同步 |
| 第 8 章 | 事务管理、SQL 模板、脚本批处理 |
| 第 9 章 | 执行计划分析、性能监控、优化建议 |

### 进阶学习建议

1. **深入特定数据库**：选择主力数据库（如 PostgreSQL），深入学习其特性
2. **学习数据库原理**：理解 B+ 树、事务、锁机制等底层原理
3. **实践性能优化**：在真实项目中应用优化技巧
4. **探索企业版**：尝试 DBeaver 企业版的高级功能（数据建模、调试、任务调度）
5. **关注社区**：参与 DBeaver 社区，获取最新动态和技巧

### 常用资源

- **DBeaver 官网**：https://dbeaver.io
- **官方文档**：https://dbeaver.com/docs
- **GitHub 仓库**：https://github.com/dbeaver/dbeaver
- **社区论坛**：https://github.com/dbeaver/dbeaver/issues

祝您在数据库管理和开发的道路上越走越远！

