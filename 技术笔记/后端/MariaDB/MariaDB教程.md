# MariaDB 系统教程

> 本教程以教科书形式，从基础到高级，系统讲解 MariaDB 数据库的核心知识与实战技能。

---

## 课程总览

- **预计讲数**：35讲（9章）
- **学习目标**：从零基础到掌握 MariaDB 的安装配置、SQL 语法、多表查询、索引优化、存储过程、事务管理、安全配置及高级特性
- **适合人群**：数据库初学者、后端开发者、运维工程师
- **学习建议**：每讲包含「概念→原理→例子→总结」四部分，建议按顺序学习，每讲配合实操练习

---

## 详细章节目录

### 第1章：基础入门
- 第1讲：MariaDB 简介与发展历史
- 第2讲：安装与配置
- 第3讲：客户端工具与基本操作
- 第4讲：数据类型详解

### 第2章：SQL 基础
- 第5讲：创建数据库与表
- 第6讲：SELECT 查询基础
- 第7讲：WHERE 条件过滤
- 第8讲：排序与分页
- 第9讲：数据增删改

### 第3章：多表查询
- 第10讲：JOIN 连接查询
- 第11讲：子查询
- 第12讲：UNION 联合查询
- 第13讲：视图

### 第4章：函数与聚合
- 第14讲：常用内置函数
- 第15讲：聚合函数与 GROUP BY
- 第16讲：HAVING 与分组过滤
- 第17讲：窗口函数

### 第5章：索引与优化
- 第18讲：索引基础
- 第19讲：索引类型与使用
- 第20讲：EXPLAIN 执行计划
- 第21讲：查询优化技巧

### 第6章：存储过程与触发器
- 第22讲：存储过程
- 第23讲：存储函数
- 第24讲：触发器
- 第25讲：游标与流程控制

### 第7章：事务与并发控制
- 第26讲：事务与 ACID
- 第27讲：隔离级别
- 第28讲：锁机制
- 第29讲：存储引擎

### 第8章：安全与管理
- 第30讲：用户与权限管理
- 第31讲：备份与恢复
- 第32讲：日志与监控

### 第9章：高级特性
- 第33讲：JSON 与全文搜索
- 第34讲：复制与高可用
- 第35讲：MariaDB 特有特性

---

# 第1章：基础入门

## 第1讲：MariaDB 简介与发展历史

### 概念

MariaDB 是一个开源的关系型数据库管理系统（RDBMS），由 MySQL 的原始创始人 Michael "Monty" Widenius 于 2009 年创建。它是 MySQL 的一个分支（fork），旨在保持开源、免费，并提供比 MySQL 更丰富的功能和更好的性能。MariaDB 以 Monty 的小女儿 Maria 的名字命名，正如 MySQL 以他的大女儿 My 命名一样。

### 原理

MariaDB 的诞生源于对 MySQL 未来发展的担忧。2008 年，Sun Microsystems 收购了 MySQL AB，随后 Oracle 在 2010 年收购了 Sun。MySQL 社区担心 Oracle 作为商业数据库巨头，可能会限制 MySQL 的开源特性或减缓其发展速度。为了保护 MySQL 的开源精神，Monty 带领核心团队创建了 MariaDB。

MariaDB 的核心设计原则包括：

1. **完全开源**：采用 GPL v2 许可证，没有商业版的闭源限制
2. **高度兼容**：MariaDB 的 API 和协议与 MySQL 完全兼容，大多数情况下可以无缝替换 MySQL
3. **社区驱动**：由独立的 MariaDB 基金会管理，不接受单一商业公司的控制
4. **持续创新**：引入了许多 MySQL 没有的新特性，如更多的存储引擎、更强的优化器等

从技术架构上看，MariaDB 采用了经典的客户端/服务器（C/S）架构。服务器端负责数据存储、查询解析、优化和执行；客户端通过 MySQL 协议（MariaDB 使用相同协议）与服务器通信。MariaDB 支持插件式的存储引擎架构，这意味着不同的表可以使用不同的底层存储方式，以适应不同的应用场景。

### 例子

**MariaDB 与 MySQL 的版本对应关系：**

```
MariaDB 5.1  ←→ MySQL 5.1
MariaDB 5.5  ←→ MySQL 5.5
MariaDB 10.0 ←→ MySQL 5.6（开始分道扬镳）
MariaDB 10.1 ←→ MySQL 5.7（功能差异增大）
MariaDB 10.2+ ← 独立发展，引入大量新特性
MariaDB 10.6 / 10.11 ← 长期支持版本（LTS）
```

**查看 MariaDB 版本信息：**

```sql
-- 方法1：使用 VERSION() 函数
SELECT VERSION();
-- 输出示例：10.11.4-MariaDB-1:10.11.4+maria~ubu2204

-- 方法2：查看服务器状态
SHOW VARIABLES LIKE 'version%';
/*
+-------------------------+----------------------------------+
| Variable_name           | Value                            |
+-------------------------+----------------------------------+
| version                 | 10.11.4-MariaDB-1:10.11.4+maria  |
| version_comment         | Ubuntu 22.04                     |
| version_compile_machine | x86_64                           |
| version_compile_os      | Linux                            |
+-------------------------+----------------------------------+
*/

-- 方法3：查看当前使用的存储引擎
SHOW ENGINES;
```

**MariaDB 相比 MySQL 的主要增强特性：**

| 特性 | 说明 |
|------|------|
| 更多存储引擎 | 支持 Aria、ColumnStore、Spider、Connect 等 |
| 更强的优化器 | 基于成本的优化器，子查询优化更好 |
| 虚拟列 | 支持持久化和非持久化虚拟列 |
| 序列 | 原生支持 SEQUENCE 对象 |
| 系统版本表 | 支持时间旅行查询（System-Versioned Tables） |
| Galera 集群 | 内置多主同步复制方案 |

### 总结

- MariaDB 是 MySQL 的开源分支，由 MySQL 创始人 Monty 创建，旨在保持数据库的开源和自由
- MariaDB 与 MySQL 高度兼容，大多数应用可以无缝迁移
- MariaDB 采用 C/S 架构和插件式存储引擎设计，具有高度的灵活性和可扩展性
- MariaDB 在兼容 MySQL 的基础上，引入了许多创新特性，如更多存储引擎、更强的优化器、系统版本表等
- 选择 MariaDB 的核心理由：完全开源、社区驱动、持续创新、高性能

---

## 第2讲：安装与配置

### 概念

MariaDB 的安装是指将 MariaDB 服务器程序部署到操作系统中的过程。配置则是指通过配置文件（通常是 `my.cnf` 或 `server.cnf`）调整服务器运行参数，以满足不同场景下的性能、安全和功能需求。正确的安装和配置是数据库稳定运行的基础。

### 原理

MariaDB 的安装方式主要有以下几种：

1. **包管理器安装**：通过操作系统的包管理器（如 apt、yum、dnf）安装，这是最简单的方式，适合大多数场景
2. **二进制包安装**：从 MariaDB 官网下载预编译的二进制包手动安装，适合需要特定版本的场景
3. **源码编译安装**：下载源代码自行编译，适合需要自定义编译选项的高级用户
4. **Docker 容器安装**：使用 Docker 镜像快速部署，适合开发测试和容器化环境

MariaDB 的配置文件采用 INI 格式，分为多个段（section），每个段以 `[段名]` 开头。重要的配置段包括：

- `[mysqld]` 或 `[mariadb]`：服务器端配置，是最常用的配置段
- `[client]`：客户端通用配置
- `[mysql]`：mysql 命令行客户端配置
- `[mysqldump]`：备份工具配置

MariaDB 读取配置文件的顺序为：`/etc/my.cnf` → `/etc/mysql/my.cnf` → `~/.my.cnf`。后读取的文件会覆盖先前的配置。配置参数分为动态参数（可以在运行时修改）和静态参数（需要重启服务器才能生效）。

### 例子

**Ubuntu/Debian 系统安装 MariaDB：**

```bash
# 步骤1：更新包索引
sudo apt update

# 步骤2：安装 MariaDB 服务器和客户端
sudo apt install mariadb-server mariadb-client -y

# 步骤3：启动并设置开机自启
sudo systemctl start mariadb
sudo systemctl enable mariadb

# 步骤4：运行安全配置脚本（设置root密码、移除匿名用户等）
sudo mysql_secure_installation

# 步骤5：检查服务状态
sudo systemctl status mariadb
```

**CentOS/RHEL 系统安装 MariaDB：**

```bash
# 步骤1：安装 MariaDB
sudo dnf install mariadb-server mariadb -y

# 步骤2：启动并设置开机自启
sudo systemctl start mariadb
sudo systemctl enable mariadb

# 步骤3：安全配置
sudo mysql_secure_installation
```

**Docker 方式安装 MariaDB：**

```bash
# 拉取 MariaDB 镜像
docker pull mariadb:10.11

# 运行 MariaDB 容器
docker run -d \
  --name mariadb \
  -e MARIADB_ROOT_PASSWORD=your_password \
  -e MARIADB_DATABASE=myapp \
  -e MARIADB_USER=appuser \
  -e MARIADB_PASSWORD=appuser_password \
  -p 3306:3306 \
  -v /data/mariadb:/var/lib/mysql \
  mariadb:10.11
```

**核心配置文件示例（`/etc/mysql/mariadb.conf.d/50-server.cnf`）：**

```ini
[mysqld]
# 基本设置
user                    = mysql
pid-file                = /run/mysqld/mysqld.pid
socket                  = /run/mysqld/mysqld.sock
port                    = 3306
basedir                 = /usr
datadir                 = /var/lib/mysql
tmpdir                  = /tmp

# 字符集设置
character-set-server    = utf8mb4
collation-server        = utf8mb4_unicode_ci

# 存储引擎设置
default_storage_engine  = InnoDB
innodb_buffer_pool_size = 1G
innodb_log_file_size    = 256M
innodb_flush_log_at_trx_commit = 1

# 连接设置
max_connections         = 200
max_allowed_packet      = 64M
wait_timeout            = 600
interactive_timeout     = 600

# 日志设置
log_error               = /var/log/mysql/error.log
slow_query_log          = 1
slow_query_log_file     = /var/log/mysql/slow.log
long_query_time         = 2

# 安全设置
local_infile            = 0
skip_symbolic_links     = yes
```

**验证安装并连接：**

```bash
# 以 root 用户连接
sudo mysql -u root -p

# 连接后查看基本信息
MariaDB [(none)]> SELECT VERSION();
MariaDB [(none)]> SHOW DATABASES;
MariaDB [(none)]> SELECT user, host FROM mysql.user;
```

### 总结

- MariaDB 支持多种安装方式，推荐使用包管理器安装（生产环境）或 Docker 安装（开发测试）
- 安装后务必运行 `mysql_secure_installation` 进行安全加固
- 配置文件采用 INI 格式，`[mysqld]` 段是最重要的服务器配置段
- 关键配置参数包括：字符集（utf8mb4）、存储引擎（InnoDB）、连接数（max_connections）、缓冲池大小（innodb_buffer_pool_size）
- 生产环境中，`innodb_buffer_pool_size` 通常设置为物理内存的 50%-70%
- 修改静态参数后需要重启服务，动态参数可以通过 `SET GLOBAL` 在线修改

---

## 第3讲：客户端工具与基本操作

### 概念

MariaDB 客户端是用于连接 MariaDB 服务器、执行 SQL 语句和管理数据库的工具。最常用的客户端是 `mariadb`（或 `mysql`）命令行工具，它提供了一个交互式的 SQL 执行环境。除了官方命令行工具外，还有图形化工具如 DBeaver、HeidiSQL、phpMyAdmin 等。掌握客户端工具的使用是进行数据库操作的前提。

### 原理

MariaDB 客户端通过 MySQL 协议（MariaDB 使用相同协议）与服务器进行通信。通信过程如下：

1. **建立连接**：客户端通过 TCP/IP 协议（默认端口 3306）或 Unix Socket 连接到服务器
2. **身份认证**：服务器验证客户端提供的用户名、密码和主机信息
3. **会话管理**：认证通过后，服务器为客户端创建一个会话（session），分配一个连接线程
4. **命令执行**：客户端发送 SQL 语句，服务器解析、优化、执行并返回结果
5. **断开连接**：客户端发送退出命令或直接断开 TCP 连接

命令行客户端 `mariadb` 支持两种使用模式：
- **交互模式**：进入 `MariaDB [(none)]>` 提示符，逐条输入 SQL 语句
- **批处理模式**：通过 `-e` 参数执行单条命令，或通过管道/文件执行脚本

客户端还支持丰富的命令快捷操作，如 `\h` 查看帮助、`\s` 查看状态、`source` 执行 SQL 文件等。

### 例子

**连接到 MariaDB 服务器：**

```bash
# 基本连接（会提示输入密码）
mysql -u root -p

# 连接到指定主机和端口
mysql -h 192.168.1.100 -P 3306 -u appuser -p

# 连接并直接选择数据库
mysql -u root -p mydb

# 连接并执行单条命令后退出
mysql -u root -p -e "SELECT VERSION(); SHOW DATABASES;"

# 通过 Socket 文件连接（本地连接，性能更好）
mysql -u root -p --socket=/run/mysqld/mysqld.sock

# 使用配置文件中的凭据连接（避免密码暴露在命令行）
# ~/.my.cnf 文件内容：
# [client]
# user=root
# password=your_password
mysql  # 直接连接，无需输入用户名密码
```

**交互模式下的常用操作：**

```sql
-- 查看所有数据库
SHOW DATABASES;

-- 创建数据库
CREATE DATABASE mydb CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- 选择/切换数据库
USE mydb;

-- 查看当前数据库
SELECT DATABASE();

-- 查看当前数据库中的所有表
SHOW TABLES;

-- 查看表结构
DESC users;
-- 或
SHOW CREATE TABLE users\G

-- 查看服务器状态
STATUS;
-- 或
\s

-- 查看系统变量
SHOW VARIABLES LIKE 'character%';

-- 查看错误信息
SHOW ERRORS;
SHOW WARNINGS;

-- 查看帮助
HELP 'SELECT';
```

**客户端快捷命令：**

```
MariaDB [mydb]> help
/*
List of all MySQL commands:
?         (\?) Synonym for `help'.
clear     (\c) Clear the current input statement.
connect   (\r) Reconnect to the server.
delimiter (\d) Set statement delimiter.
edit      (\e) Edit command with $EDITOR.
ego       (\G) Send command to mysql server, display result vertically.
exit      (\q) Exit mysql. Same as quit.
go        (\g) Send command to mysql server.
help      (\h) Display this help.
notee     (\t) Don't write into outfile.
print     (\p) Print current command.
prompt    (\R) Change your mysql prompt.
quit      (\q) Quit mysql.
rehash    (\#) Rebuild completion hash.
source    (\.) Execute an SQL script file. Takes a file name as an argument.
status    (\s) Get status information from the server.
system    (\!) Execute a system shell command.
tee       (\T) Set outfile [to_outfile]. Append everything into given outfile.
use       (\u) Use another database. Takes database name as argument.
*/
```

**执行 SQL 脚本文件：**

```bash
# 方法1：通过管道执行
mysql -u root -p mydb < /path/to/script.sql

# 方法2：在交互模式中使用 source 命令
MariaDB [mydb]> source /path/to/script.sql
-- 或
MariaDB [mydb]> \. /path/to/script.sql
```

**示例 SQL 脚本文件（init.sql）：**

```sql
-- 创建数据库
CREATE DATABASE IF NOT EXISTS shop CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
USE shop;

-- 创建商品表
CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    price DECIMAL(10, 2) NOT NULL,
    stock INT DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 插入示例数据
INSERT INTO products (name, price, stock) VALUES
('笔记本电脑', 5999.00, 50),
('智能手机', 3999.00, 100),
('平板电脑', 2599.00, 30);

-- 查询验证
SELECT * FROM products;
```

### 总结

- `mariadb`（或 `mysql`）是最常用的命令行客户端，支持交互模式和批处理模式
- 连接时推荐使用 `~/.my.cnf` 配置文件存储凭据，避免密码暴露在命令行或进程列表中
- 交互模式下可以使用 `\` 快捷命令提高效率，如 `\G` 垂直显示结果、`\s` 查看状态
- 使用 `source` 命令或管道方式可以执行 SQL 脚本文件，适合批量操作和初始化
- 除了命令行工具，图形化工具如 DBeaver、HeidiSQL 也提供了更直观的操作界面
- 掌握客户端工具的使用是数据库操作的基础，建议熟练掌握命令行工具后再使用图形化工具

---

## 第4讲：数据类型详解

### 概念

数据类型是数据库中用于定义列可以存储什么类型数据的规范。MariaDB 提供了丰富的数据类型，主要分为五大类：数值类型、字符串类型、日期时间类型、JSON 类型和枚举/集合类型。选择合适的数据类型对于保证数据完整性、节省存储空间和提高查询性能至关重要。

### 原理

数据类型的设计原理基于以下考量：

1. **存储效率**：不同类型占用的存储空间不同，选择最小够用的类型可以节省磁盘和内存
2. **数据完整性**：类型约束确保只有合法的数据才能存入列中，例如 INT 列不能存储字符串
3. **查询性能**：固定长度的类型（如 CHAR）比变长类型（如 VARCHAR）在某些场景下查询更快
4. **精度保证**：数值类型需要考虑精度和范围，日期类型需要考虑时区

数值类型分为整数类型（TINYINT、SMALLINT、MEDIUMINT、INT、BIGINT）和浮点/定点类型（FLOAT、DOUBLE、DECIMAL）。整数类型的显示宽度（如 INT(11)）在 MariaDB 10.2.1+ 中已被废弃，不再影响存储范围，仅 INT 占用 4 字节、BIGINT 占用 8 字节是关键。

字符串类型中，CHAR 是定长类型（不足部分用空格填充），VARCHAR 是变长类型（实际占用 = 数据长度 + 1/2 字节长度前缀）。TEXT 系列用于存储大文本，但不能设置默认值，也不能完全建立索引。

日期时间类型中，DATETIME 存储日期时间不带时区信息（8字节），TIMESTAMP 存储为 UTC 时间戳并自动转换（4字节，范围限于 1970-2038）。

### 例子

**数值类型示例：**

```sql
-- 创建包含各种数值类型的表
CREATE TABLE numeric_examples (
    -- 整数类型
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,  -- 无符号INT，范围0~4294967295
    age TINYINT UNSIGNED,                         -- 无符号TINYINT，范围0~255
    population BIGINT,                            -- 大整数，范围-2^63~2^63-1
    
    -- 浮点类型（近似值）
    temperature FLOAT,                            -- 单精度浮点，4字节
    distance DOUBLE,                              -- 双精度浮点，8字节
    
    -- 定点类型（精确值，用于货币等）
    price DECIMAL(10, 2),                         -- 总10位，小数2位
    exchange_rate DECIMAL(15, 8)                  -- 总15位，小数8位
);

-- 插入数据
INSERT INTO numeric_examples (age, population, temperature, distance, price, exchange_rate)
VALUES (25, 7900000000, 36.5, 384400.0, 9999.99, 6.85250000);

-- 查看存储结果
SELECT * FROM numeric_examples;
/*
+----+------+------------+-------------+----------+----------+---------------+
| id | age  | population | temperature | distance | price    | exchange_rate |
+----+------+------------+-------------+----------+----------+---------------+
|  1 |   25 | 7900000000 |        36.5 |   384400 |  9999.99 |    6.85250000 |
+----+------+------------+-------------+----------+----------+---------------+
*/
```

**字符串类型示例：**

```sql
CREATE TABLE string_examples (
    id INT AUTO_INCREMENT PRIMARY KEY,
    
    -- 定长字符串：始终占用指定长度空间
    country_code CHAR(2),                         -- 国家代码，如 'CN'、'US'
    
    -- 变长字符串：按实际长度存储
    username VARCHAR(50),                         -- 用户名
    email VARCHAR(255),                           -- 邮箱
    
    -- 大文本类型
    description TEXT,                             -- 普通文本，最大65535字节
    article LONGTEXT,                             -- 长文本，最大4GB
    
    -- 二进制类型
    avatar BLOB,                                  -- 二进制大对象
    
    -- 枚举类型：只能取指定值之一
    gender ENUM('male', 'female', 'other'),
    status ENUM('active', 'inactive', 'banned') DEFAULT 'active',
    
    -- 集合类型：可取多个指定值
    tags SET('tech', 'design', 'marketing', 'sales')
);

-- 插入数据
INSERT INTO string_examples 
(country_code, username, email, description, gender, tags)
VALUES 
('CN', 'zhangsan', 'zhangsan@example.com', '一名开发者', 'male', 'tech,design');

-- 查询
SELECT id, country_code, username, gender, tags FROM string_examples;
/*
+----+--------------+----------+--------+------------+
| id | country_code | username | gender | tags       |
+----+--------------+----------+--------+------------+
|  1 | CN           | zhangsan | male   | tech,design|
+----+--------------+----------+--------+------------+
*/
```

**日期时间类型示例：**

```sql
CREATE TABLE datetime_examples (
    id INT AUTO_INCREMENT PRIMARY KEY,
    
    -- 日期类型
    birth_date DATE,                              -- 仅日期，格式 'YYYY-MM-DD'
    
    -- 时间类型
    work_time TIME,                               -- 仅时间，格式 'HH:MM:SS'
    
    -- 日期时间类型（不带时区）
    created_at DATETIME,                          -- 格式 'YYYY-MM-DD HH:MM:SS'
    
    -- 时间戳类型（自动转换为UTC存储）
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP 
        ON UPDATE CURRENT_TIMESTAMP,
    
    -- 年份类型
    graduation_year YEAR
);

-- 插入数据
INSERT INTO datetime_examples 
(birth_date, work_time, created_at, graduation_year)
VALUES 
('1990-05-15', '09:00:00', '2024-01-15 10:30:00', 2012);

-- 查询
SELECT * FROM datetime_examples;
/*
+----+------------+-----------+---------------------+---------------------+------------------+
| id | birth_date | work_time | created_at          | updated_at          | graduation_year  |
+----+------------+-----------+---------------------+---------------------+------------------+
|  1 | 1990-05-15 | 09:00:00 | 2024-01-15 10:30:00 | 2024-01-15 10:30:00 |             2012 |
+----+------------+-----------+---------------------+---------------------+------------------+
*/

-- 使用日期函数查询
SELECT 
    id,
    birth_date,
    YEAR(birth_date) AS birth_year,
    TIMESTAMPDIFF(YEAR, birth_date, CURDATE()) AS age,
    DAYNAME(birth_date) AS birth_day_of_week
FROM datetime_examples;
```

**JSON 类型示例（MariaDB 10.2+）：**

```sql
CREATE TABLE json_examples (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50),
    attributes JSON  -- JSON类型，存储结构化数据
);

-- 插入JSON数据
INSERT INTO json_examples (name, attributes) VALUES
('产品A', '{"color": "红色", "size": "L", "tags": ["热销", "新品"]}'),
('产品B', '{"color": "蓝色", "size": "M", "tags": ["促销"]}');

-- 查询JSON字段
SELECT 
    name,
    JSON_VALUE(attributes, '$.color') AS color,
    JSON_VALUE(attributes, '$.size') AS size,
    JSON_EXTRACT(attributes, '$.tags') AS tags
FROM json_examples;
```

### 总结

- 数值类型选择原则：优先使用最小够用的类型；货币金额必须使用 DECIMAL，不能用 FLOAT/DOUBLE
- 字符串类型选择原则：短且长度固定的用 CHAR（如国家代码），长度可变的用 VARCHAR；大文本用 TEXT 系列
- 日期时间类型选择原则：仅日期用 DATE，需要日期时间用 DATETIME，需要自动更新时间戳用 TIMESTAMP
- ENUM 类型适合存储有限选项的值（如状态、性别），节省空间且保证数据完整性
- JSON 类型适合存储半结构化数据，但不要过度使用，关系型数据仍应使用传统列存储
- 选择数据类型的核心原则：在满足需求的前提下，选择占用空间最小、查询效率最高的类型

---

# 第2章：SQL 基础

## 第5讲：创建数据库与表

### 概念

数据库（Database）是 MariaDB 中组织数据的顶层容器，一个 MariaDB 服务器可以包含多个数据库。表（Table）是数据库中存储数据的基本结构，由行（Row/记录）和列（Column/字段）组成。创建数据库和表是使用 MariaDB 的第一步，良好的表结构设计直接影响后续的数据操作效率和应用程序的可维护性。

### 原理

MariaDB 的数据存储层次结构为：服务器 → 数据库 → 表 → 行/列。在文件系统层面，每个数据库对应 `datadir` 下的一个目录，每个表对应目录下的若干文件（具体文件取决于存储引擎，InnoDB 使用 `.frm`（表结构）和 `.ibd`（数据）文件，或共享表空间）。

创建数据库时，需要指定字符集（Character Set）和排序规则（Collation）：
- **字符集**：定义可以存储哪些字符，如 `utf8mb4` 支持完整的 Unicode（包括 emoji）
- **排序规则**：定义字符的比较和排序规则，如 `utf8mb4_unicode_ci` 表示不区分大小写的 Unicode 排序

创建表时，需要定义：
- **列定义**：列名、数据类型、是否允许 NULL、默认值等
- **约束（Constraints）**：主键、外键、唯一键、检查约束等
- **表选项**：存储引擎、字符集、自增起始值等

主键（Primary Key）是表中唯一标识每行记录的一列或多列组合，具有非空和唯一的特性。MariaDB 会自动为主键创建索引，加速基于主键的查询。

### 例子

**创建数据库：**

```sql
-- 创建基本数据库
CREATE DATABASE mydb;

-- 创建时指定字符集和排序规则（推荐）
CREATE DATABASE shop 
    CHARACTER SET utf8mb4 
    COLLATE utf8mb4_unicode_ci;

-- 如果不存在则创建（避免重复创建报错）
CREATE DATABASE IF NOT EXISTS shop 
    CHARACTER SET utf8mb4 
    COLLATE utf8mb4_unicode_ci;

-- 修改数据库字符集
ALTER DATABASE shop CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;

-- 删除数据库
DROP DATABASE IF EXISTS shop;

-- 查看数据库创建语句
SHOW CREATE DATABASE shop;
```

**创建完整的电商示例表：**

```sql
USE shop;

-- 用户表
CREATE TABLE users (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE COMMENT '用户名',
    email VARCHAR(255) NOT NULL UNIQUE COMMENT '邮箱',
    password_hash VARCHAR(255) NOT NULL COMMENT '密码哈希',
    phone CHAR(11) COMMENT '手机号',
    status TINYINT UNSIGNED DEFAULT 1 COMMENT '状态：0禁用 1正常',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间'
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci COMMENT='用户表';

-- 商品分类表
CREATE TABLE categories (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL COMMENT '分类名称',
    parent_id INT UNSIGNED DEFAULT NULL COMMENT '父分类ID',
    sort_order INT DEFAULT 0 COMMENT '排序',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (parent_id) REFERENCES categories(id) ON DELETE SET NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='商品分类表';

-- 商品表
CREATE TABLE products (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    category_id INT UNSIGNED NOT NULL COMMENT '分类ID',
    name VARCHAR(200) NOT NULL COMMENT '商品名称',
    description TEXT COMMENT '商品描述',
    price DECIMAL(10, 2) NOT NULL COMMENT '价格',
    stock INT UNSIGNED DEFAULT 0 COMMENT '库存',
    sku VARCHAR(50) UNIQUE COMMENT '库存单位',
    is_on_sale BOOLEAN DEFAULT TRUE COMMENT '是否上架',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (category_id) REFERENCES categories(id) ON DELETE RESTRICT,
    INDEX idx_name (name),              -- 普通索引
    INDEX idx_category_price (category_id, price)  -- 联合索引
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='商品表';

-- 订单表
CREATE TABLE orders (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    order_no VARCHAR(32) NOT NULL UNIQUE COMMENT '订单号',
    user_id INT UNSIGNED NOT NULL COMMENT '用户ID',
    total_amount DECIMAL(12, 2) NOT NULL COMMENT '订单总金额',
    status ENUM('pending', 'paid', 'shipped', 'completed', 'cancelled') 
        DEFAULT 'pending' COMMENT '订单状态',
    shipping_address VARCHAR(500) COMMENT '收货地址',
    remark VARCHAR(500) COMMENT '备注',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    paid_at TIMESTAMP NULL COMMENT '支付时间',
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE RESTRICT,
    INDEX idx_user (user_id),
    INDEX idx_status_created (status, created_at)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='订单表';

-- 订单详情表
CREATE TABLE order_items (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    order_id BIGINT UNSIGNED NOT NULL COMMENT '订单ID',
    product_id INT UNSIGNED NOT NULL COMMENT '商品ID',
    product_name VARCHAR(200) NOT NULL COMMENT '商品名称（冗余）',
    price DECIMAL(10, 2) NOT NULL COMMENT '购买时单价',
    quantity INT UNSIGNED NOT NULL COMMENT '购买数量',
    subtotal DECIMAL(12, 2) NOT NULL COMMENT '小计',
    FOREIGN KEY (order_id) REFERENCES orders(id) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(id) ON DELETE RESTRICT,
    INDEX idx_order (order_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='订单详情表';
```

**修改表结构：**

```sql
-- 添加列
ALTER TABLE users ADD COLUMN nickname VARCHAR(50) AFTER username;
ALTER TABLE users ADD COLUMN avatar VARCHAR(255) COMMENT '头像URL';

-- 修改列
ALTER TABLE users MODIFY COLUMN phone VARCHAR(20) COMMENT '手机号（支持国际格式）';

-- 重命名列（MariaDB 10.5.2+）
ALTER TABLE users RENAME COLUMN phone TO mobile;

-- 删除列
ALTER TABLE users DROP COLUMN avatar;

-- 添加索引
ALTER TABLE users ADD INDEX idx_created (created_at);

-- 重命名表
RENAME TABLE users TO members;
-- 或
ALTER TABLE members RENAME TO users;

-- 查看表结构
DESC users;
SHOW CREATE TABLE users\G
```

### 总结

- 创建数据库时务必指定 `utf8mb4` 字符集，以支持完整的 Unicode 字符（包括 emoji）
- 表设计应遵循范式原则，每列保持原子性，避免数据冗余
- 合理使用约束（主键、外键、唯一键、非空）保证数据完整性
- 为常用查询条件的列添加索引，但不要过度索引（影响写入性能）
- 使用 `COMMENT` 为表和列添加注释，提高可维护性
- 生产环境修改表结构（ALTER TABLE）要谨慎，大表修改可能锁表导致服务中断
- 推荐使用 `IF NOT EXISTS` 和 `IF EXISTS` 避免脚本重复执行报错

---

## 第6讲：SELECT 查询基础

### 概念

SELECT 是 SQL 中最核心的语句，用于从数据库表中检索数据。它可以根据需要选择特定的列、过滤行、排序结果、聚合数据等。掌握 SELECT 语句是数据库操作的基础，几乎所有的数据查询需求都从 SELECT 开始。一个基本的 SELECT 语句由 `SELECT`（选择列）和 `FROM`（指定表）两个子句组成。

### 原理

SELECT 语句的执行遵循逻辑顺序（与书写顺序不同），理解执行顺序对编写复杂查询至关重要：

1. **FROM**：确定数据来源（表或连接结果）
2. **WHERE**：行级过滤
3. **GROUP BY**：分组
4. **HAVING**：组级过滤
5. **SELECT**：选择列、计算表达式
6. **DISTINCT**：去重
7. **ORDER BY**：排序
8. **LIMIT**：限制返回行数

SELECT 子句支持多种选择方式：
- **列名**：直接选择列，如 `SELECT name, price`
- **`*` 通配符**：选择所有列，如 `SELECT *`
- **表达式**：计算值，如 `SELECT price * 0.9 AS discount_price`
- **函数调用**：如 `SELECT UPPER(name), NOW()`
- **字面量**：如 `SELECT 1, 'hello'`

使用 `AS` 关键字可以为列或表达式指定别名（Alias），使结果更易读。`AS` 关键字可以省略，但建议显式使用以提高可读性。

### 例子

**基本查询：**

```sql
USE shop;

-- 查询所有列（生产环境不推荐，效率低）
SELECT * FROM products;

-- 查询指定列（推荐）
SELECT name, price, stock FROM products;

-- 使用别名
SELECT 
    name AS product_name, 
    price AS unit_price,
    stock AS inventory
FROM products;

-- 别名可以省略 AS
SELECT name product_name, price unit_price FROM products;

-- 包含计算表达式
SELECT 
    name,
    price,
    stock,
    price * stock AS total_value,           -- 库存总价值
    price * 0.9 AS member_price             -- 会员价（9折）
FROM products;

-- 使用字面量和函数
SELECT 
    name,
    price,
    'CNY' AS currency,                       -- 字面量
    UPPER(name) AS name_upper,              -- 函数
    ROUND(price, 0) AS price_rounded,       -- 四舍五入
    NOW() AS query_time                     -- 当前时间
FROM products;
```

**DISTINCT 去重：**

```sql
-- 查询所有商品的分类ID（有重复）
SELECT category_id FROM products;

-- 查询不重复的分类ID
SELECT DISTINCT category_id FROM products;

-- 多列去重
SELECT DISTINCT category_id, is_on_sale FROM products;
```

**条件查询（WHERE 子句）：**

```sql
-- 等值查询
SELECT * FROM products WHERE category_id = 1;

-- 比较查询
SELECT name, price FROM products WHERE price > 100;
SELECT name, price FROM products WHERE price >= 100 AND price <= 500;
SELECT name, price FROM products WHERE price BETWEEN 100 AND 500;  -- 等价写法

-- 不等于
SELECT name FROM products WHERE category_id != 1;
SELECT name FROM products WHERE category_id <> 1;  -- 等价写法

-- 多值匹配
SELECT name, price FROM products 
WHERE category_id IN (1, 2, 3);

-- 模糊查询
SELECT name FROM products WHERE name LIKE '手机%';      -- 以"手机"开头
SELECT name FROM products WHERE name LIKE '%手机';      -- 以"手机"结尾
SELECT name FROM products WHERE name LIKE '%手机%';     -- 包含"手机"
SELECT name FROM products WHERE name LIKE '手机_';      -- "手机"后跟一个字符

-- NULL 判断（不能用 = 或 !=）
SELECT name FROM products WHERE description IS NULL;      -- 为NULL
SELECT name FROM products WHERE description IS NOT NULL;  -- 不为NULL

-- 多条件组合
SELECT name, price, stock FROM products 
WHERE price > 100 
  AND stock > 0 
  AND is_on_sale = TRUE;

SELECT name, price FROM products 
WHERE category_id = 1 OR category_id = 2;
```

**排序与限制（ORDER BY 和 LIMIT）：**

```sql
-- 按价格升序（默认）
SELECT name, price FROM products ORDER BY price;

-- 按价格降序
SELECT name, price FROM products ORDER BY price DESC;

-- 多列排序
SELECT name, category_id, price FROM products 
ORDER BY category_id ASC, price DESC;

-- 限制返回行数
SELECT name, price FROM products LIMIT 5;           -- 前5条
SELECT name, price FROM products LIMIT 5 OFFSET 10; -- 跳过10条，取5条
SELECT name, price FROM products LIMIT 10, 5;       -- 等价写法

-- 结合排序和限制（如：取最贵的3个商品）
SELECT name, price FROM products 
ORDER BY price DESC 
LIMIT 3;
```

**综合示例：**

```sql
-- 查询在售商品中，价格在100-1000之间、库存大于50的商品
-- 按价格降序排列，取前10条
SELECT 
    id,
    name AS 商品名称,
    price AS 价格,
    stock AS 库存,
    price * stock AS 库存总值,
    DATE(created_at) AS 上架日期
FROM products
WHERE is_on_sale = TRUE
  AND price BETWEEN 100 AND 1000
  AND stock > 50
ORDER BY price DESC
LIMIT 10;
```

### 总结

- SELECT 语句的逻辑执行顺序为 FROM→WHERE→GROUP BY→HAVING→SELECT→DISTINCT→ORDER BY→LIMIT
- 生产环境避免使用 `SELECT *`，应明确指定需要的列，提高查询效率和可维护性
- 使用 `AS` 为列指定有意义的别名，使结果集更易读
- WHERE 子句中判断 NULL 必须使用 `IS NULL` 或 `IS NOT NULL`，不能用 `=` 或 `!=`
- LIKE 模糊查询中 `%` 匹配任意字符序列，`_` 匹配单个字符；前缀匹配（如 `'手机%'`）可以利用索引
- ORDER BY 默认升序（ASC），降序需指定 DESC；多列排序时按从左到右优先级
- LIMIT 常用于分页，注意 `LIMIT offset, count` 的写法中 offset 从 0 开始

---

## 第7讲：WHERE 条件过滤

### 概念

WHERE 子句是 SELECT、UPDATE、DELETE 语句中用于过滤数据的条件表达式。它根据指定的条件从表中筛选出符合条件的行，只对这些行进行操作。WHERE 子句是 SQL 查询中最常用、最重要的部分之一，直接决定了查询的精确度和性能。

### 原理

WHERE 子句的工作原理是：对 FROM 子句返回的每一行，计算 WHERE 表达式的值。如果结果为 TRUE，该行被保留；如果为 FALSE 或 NULL（未知），该行被排除。这就是所谓的"行级过滤"。

WHERE 子句支持多种运算符：

| 运算符类型 | 运算符 | 说明 |
|-----------|--------|------|
| 比较 | `=`, `!=`, `<>`, `>`, `<`, `>=`, `<=` | 值比较 |
| 范围 | `BETWEEN ... AND ...` | 在范围内（含边界） |
| 集合 | `IN (...)`, `NOT IN (...)` | 在/不在集合中 |
| 模式匹配 | `LIKE`, `NOT LIKE` | 通配符匹配 |
| NULL 判断 | `IS NULL`, `IS NOT NULL` | NULL 值判断 |
| 逻辑 | `AND`, `OR`, `NOT` | 逻辑组合 |
| 正则 | `REGEXP`, `RLIKE` | 正则表达式匹配 |

**三值逻辑**是 SQL 中一个重要概念：比较运算的结果可以是 TRUE、FALSE 或 NULL（未知）。当操作数为 NULL 时，比较结果为 NULL。例如 `NULL = 1` 的结果是 NULL，不是 FALSE。WHERE 子句只保留结果为 TRUE 的行，NULL 和 FALSE 的行都会被排除。

运算符优先级：比较运算符 > NOT > AND > OR。建议使用括号明确表达优先级，避免歧义。

### 例子

**比较运算符：**

```sql
-- 等值查询
SELECT name, price FROM products WHERE category_id = 1;

-- 不等于
SELECT name FROM products WHERE price <> 99.99;

-- 大于/小于
SELECT name, price FROM products WHERE price > 500;
SELECT name, price FROM products WHERE stock < 10;

-- 大于等于/小于等于
SELECT name FROM products WHERE price >= 100 AND price <= 500;
```

**BETWEEN 范围查询：**

```sql
-- 价格在100到500之间（包含边界）
SELECT name, price FROM products WHERE price BETWEEN 100 AND 500;

-- 等价于
SELECT name, price FROM products WHERE price >= 100 AND price <= 500;

-- 不在范围内
SELECT name, price FROM products WHERE price NOT BETWEEN 100 AND 500;

-- 日期范围查询
SELECT order_no, created_at FROM orders 
WHERE created_at BETWEEN '2024-01-01 00:00:00' AND '2024-01-31 23:59:59';
```

**IN 集合查询：**

```sql
-- 匹配多个值中的任意一个
SELECT name, category_id FROM products 
WHERE category_id IN (1, 2, 3);

-- 等价于
SELECT name, category_id FROM products 
WHERE category_id = 1 OR category_id = 2 OR category_id = 3;

-- 不在集合中
SELECT name, category_id FROM products 
WHERE category_id NOT IN (1, 2, 3);

-- 字符串也可以用 IN
SELECT name, status FROM orders 
WHERE status IN ('paid', 'shipped');
```

**LIKE 模式匹配：**

```sql
-- 以"手机"开头
SELECT name FROM products WHERE name LIKE '手机%';

-- 以"手机"结尾
SELECT name FROM products WHERE name LIKE '%手机';

-- 包含"手机"
SELECT name FROM products WHERE name LIKE '%手机%';

-- 第二个字是"机"（_匹配单个字符）
SELECT name FROM products WHERE name LIKE '_机%';

-- 不匹配模式
SELECT name FROM products WHERE name NOT LIKE '%测试%';

-- 转义特殊字符（使用 ESCAPE 指定转义符）
SELECT name FROM products WHERE name LIKE '%50\%%' ESCAPE '\\';
-- 查找包含"50%"的商品名
```

**NULL 处理：**

```sql
-- 查询有描述的商品
SELECT name FROM products WHERE description IS NOT NULL;

-- 查询没有描述的商品
SELECT name FROM products WHERE description IS NULL;

-- 注意：以下写法无法查询到NULL的行！
SELECT name FROM products WHERE description != '测试';  -- NULL的行不会出现
SELECT name FROM products WHERE description = NULL;     -- 永远返回空结果

-- 安全等于运算符 <=> （可以正确比较NULL）
SELECT name FROM products WHERE description <=> NULL;     -- 等价于 IS NULL
SELECT name FROM products WHERE description <=> '测试';   -- 等价于 = '测试'
```

**逻辑运算符组合：**

```sql
-- AND：所有条件都满足
SELECT name, price, stock FROM products 
WHERE price > 100 AND stock > 0 AND is_on_sale = TRUE;

-- OR：满足任一条件
SELECT name FROM products 
WHERE category_id = 1 OR category_id = 2 OR price < 50;

-- NOT：取反
SELECT name FROM products 
WHERE NOT (category_id = 1 AND price > 100);

-- 使用括号明确优先级（推荐）
SELECT name, price, category_id FROM products 
WHERE (category_id = 1 AND price > 100)
   OR (category_id = 2 AND price > 200);
```

**REGEXP 正则表达式：**

```sql
-- 以数字开头
SELECT name FROM products WHERE name REGEXP '^[0-9]';

-- 包含中文"手机"或"电脑"
SELECT name FROM products WHERE name REGEXP '手机|电脑';

-- 以"手机"结尾
SELECT name FROM products WHERE name REGEXP '手机$';

-- 手机号格式验证（11位数字）
SELECT phone FROM users WHERE phone REGEXP '^1[3-9][0-9]{9}$';
```

### 总结

- WHERE 子句对每行计算条件表达式，只保留结果为 TRUE 的行（NULL 和 FALSE 都被排除）
- SQL 使用三值逻辑（TRUE/FALSE/NULL），NULL 参与的比较结果为 NULL，需用 `IS NULL` 判断
- BETWEEN 包含边界值，等价于 `>= AND <=`
- IN 适合匹配离散值集合，比多个 OR 条件更清晰且通常性能更好
- LIKE 中 `%` 匹配任意长度字符，`_` 匹配单个字符；前缀匹配可走索引，`%前缀` 无法走索引
- 使用括号明确逻辑运算符的优先级，避免依赖默认优先级导致逻辑错误
- `<=>` 是安全等于运算符，可以正确比较 NULL 值，但日常使用建议用 `IS NULL` 更清晰
- WHERE 中不能使用列别名，因为 WHERE 在 SELECT 之前执行（别名此时还未定义）

---

## 第8讲：排序与分页

### 概念

排序（ORDER BY）是将查询结果按指定列的值进行升序或降序排列的功能。分页（LIMIT + OFFSET）是将大量查询结果分割成多个小页面返回的技术，常用于前端列表展示。排序和分页通常配合使用，是 Web 应用中最常见的查询模式。

### 原理

**排序原理：**

ORDER BY 的工作机制是：在 SELECT 子句执行完毕、产生结果集后，对结果集进行排序。MariaDB 使用以下排序算法：

1. **Using index（索引排序）**：如果 ORDER BY 的列上有索引，且顺序一致，可以直接利用索引的有序性，无需额外排序，性能最佳
2. **Using filesort（文件排序）**：无法利用索引时，MariaDB 在内存（sort_buffer）中排序；数据量超过 sort_buffer 时，使用临时文件辅助排序，性能较差

排序时，NULL 值被视为最小值：升序时 NULL 排在最前，降序时 NULL 排在最后。

**分页原理：**

LIMIT 子句的语法为 `LIMIT [offset,] row_count` 或 `LIMIT row_count OFFSET offset`。其工作方式是：先执行完整查询并排序，然后跳过 offset 行，返回 row_count 行。

分页的性能问题在于"深分页"：当 offset 很大时（如 `LIMIT 1000000, 10`），MariaDB 仍然需要扫描并排序前 1000010 行，然后丢弃前 100 万行，只返回 10 行。这会导致严重的性能问题。

优化深分页的常见方案是"延迟关联"或"游标分页"：
- **延迟关联**：先通过子查询利用索引找到需要的 ID，再关联原表获取完整数据
- **游标分页**：记录上一页最后一条记录的 ID，下一页查询 `WHERE id > last_id LIMIT 10`

### 例子

**基本排序：**

```sql
-- 按价格升序（ASC可省略，默认升序）
SELECT name, price FROM products ORDER BY price ASC;

-- 按价格降序
SELECT name, price FROM products ORDER BY price DESC;

-- 按名称排序（字符串按字符集排序规则比较）
SELECT name, price FROM products ORDER BY name;

-- 按创建时间降序（最新的在前）
SELECT name, created_at FROM products ORDER BY created_at DESC;
```

**多列排序：**

```sql
-- 先按分类升序，分类内按价格降序
SELECT name, category_id, price FROM products 
ORDER BY category_id ASC, price DESC;

-- 先按状态升序，再按创建时间降序
SELECT order_no, status, created_at FROM orders 
ORDER BY status ASC, created_at DESC;
```

**使用表达式和别名排序：**

```sql
-- 按计算列排序
SELECT name, price, stock, price * stock AS total_value 
FROM products 
ORDER BY total_value DESC;

-- ORDER BY 中可以使用列别名（因为ORDER BY在SELECT之后执行）
SELECT name, price * 0.9 AS member_price 
FROM products 
ORDER BY member_price;

-- 按表达式排序
SELECT name, price FROM products ORDER BY price * stock DESC;

-- 按列位置排序（不推荐，可读性差）
SELECT name, price, stock FROM products ORDER BY 2 DESC;  -- 按第2列(price)排序
```

**NULL 值排序处理：**

```sql
-- 默认：升序时NULL在最前
SELECT name, paid_at FROM orders ORDER BY paid_at ASC;

-- 将NULL放在最后（升序时）
SELECT name, paid_at FROM orders 
ORDER BY paid_at IS NULL, paid_at ASC;

-- 将NULL放在最前（降序时）
SELECT name, paid_at FROM orders 
ORDER BY paid_at IS NULL DESC, paid_at DESC;
```

**基本分页：**

```sql
-- 每页10条，第1页（offset=0）
SELECT id, name, price FROM products 
ORDER BY id ASC LIMIT 0, 10;
-- 或
SELECT id, name, price FROM products 
ORDER BY id ASC LIMIT 10 OFFSET 0;

-- 第2页（offset=10）
SELECT id, name, price FROM products 
ORDER BY id ASC LIMIT 10, 10;

-- 第3页（offset=20）
SELECT id, name, price FROM products 
ORDER BY id ASC LIMIT 20, 10;

-- 通用分页公式：OFFSET = (页码 - 1) * 每页条数
-- 第N页：LIMIT (N-1)*10, 10
```

**深分页优化示例：**

```sql
-- 问题：深分页性能差（假设有100万条数据）
SELECT id, name, price, description, created_at 
FROM products 
ORDER BY id ASC 
LIMIT 1000000, 10;
-- 问题：需要扫描1000010行，丢弃前100万行

-- 优化方案1：延迟关联（子查询走索引找ID，再关联原表）
SELECT p.id, p.name, p.price, p.description, p.created_at 
FROM products p
INNER JOIN (
    SELECT id FROM products ORDER BY id ASC LIMIT 1000000, 10
) t ON p.id = t.id;
-- 子查询只扫描索引（覆盖索引），速度快很多

-- 优化方案2：游标分页（记住上一页最后的ID）
-- 假设上一页最后一条记录的id=1000000
SELECT id, name, price, description, created_at 
FROM products 
WHERE id > 1000000 
ORDER BY id ASC 
LIMIT 10;
-- 直接从id=1000000之后开始扫描，效率极高
```

**综合示例：商品列表分页查询：**

```sql
-- 需求：查询在售商品，按价格降序、名称升序排列，每页20条，当前第3页
SELECT 
    id,
    name,
    price,
    stock,
    category_id
FROM products
WHERE is_on_sale = TRUE
ORDER BY price DESC, name ASC
LIMIT 40, 20;  -- (3-1) * 20 = 40

-- 带总记录数的分页（用于前端显示总页数）
-- 查询总数
SELECT COUNT(*) AS total FROM products WHERE is_on_sale = TRUE;
-- 查询当前页数据
SELECT id, name, price FROM products 
WHERE is_on_sale = TRUE 
ORDER BY price DESC, name ASC 
LIMIT 40, 20;
```

### 总结

- ORDER BY 在 SELECT 之后执行，因此可以使用列别名进行排序
- 排序时尽量利用索引（ORDER BY 的列和方向与索引一致），避免 Using filesort
- NULL 值在升序时排最前，降序时排最后；可用 `IS NULL` 表达式调整 NULL 的位置
- LIMIT 分页公式：`LIMIT (页码-1)*每页条数, 每页条数`
- 深分页（offset 很大）会导致性能问题，应使用"延迟关联"或"游标分页"优化
- 游标分页只能用于"上一页/下一页"导航，无法直接跳转到指定页
- 分页查询通常需要配合 COUNT(*) 获取总记录数，用于计算总页数
- 生产环境中，排序字段应建立索引，否则大数据量排序会严重影响性能

---

## 第9讲：数据增删改

### 概念

数据的增删改（INSERT、UPDATE、DELETE）是数据库的写操作，合称 DML（Data Manipulation Language）。INSERT 用于向表中插入新数据，UPDATE 用于修改已有数据，DELETE 用于删除数据。这三类操作是应用程序与数据库交互的核心，需要特别注意数据安全和性能。

### 原理

**INSERT 原理：**

INSERT 语句将新行写入表中。执行过程包括：
1. 检查约束（主键、唯一键、外键、非空、检查约束）
2. 触发 BEFORE INSERT 触发器
3. 写入数据
4. 更新索引
5. 触发 AFTER INSERT 触发器

INSERT 支持单行插入和多行批量插入。批量插入比循环单行插入效率高得多，因为减少了网络往返和事务开销。

**UPDATE 原理：**

UPDATE 语句修改符合条件的行。执行过程：
1. 查找符合条件的行（利用索引或全表扫描）
2. 检查新值是否满足约束
3. 触发 BEFORE UPDATE 触发器
4. 更新数据
5. 更新索引（如果更新了索引列）
6. 触发 AFTER UPDATE 触发器

UPDATE 会持有行锁（InnoDB），在事务提交前其他事务无法修改这些行。

**DELETE 原理：**

DELETE 语句删除符合条件的行。执行过程类似 UPDATE，但只删除不修改。DELETE 删除的数据会被记录在 undo log 中，支持事务回滚。DELETE 不会释放磁盘空间（只是标记为可重用），如需释放空间需使用 OPTIMIZE TABLE。

**REPLACE 和 INSERT ... ON DUPLICATE KEY UPDATE：**

- `REPLACE`：遇到主键/唯一键冲突时，先删除旧行再插入新行
- `INSERT ... ON DUPLICATE KEY UPDATE`：遇到冲突时，更新已有行（更高效，推荐使用）

### 例子

**INSERT 插入数据：**

```sql
-- 单行插入（指定所有列）
INSERT INTO categories (id, name, parent_id, sort_order) 
VALUES (1, '电子产品', NULL, 1);

-- 单行插入（省略列名，需按表定义顺序提供所有列值，不推荐）
INSERT INTO categories VALUES (2, '服装', NULL, 2);

-- 单行插入（只指定部分列，未指定的列使用默认值或NULL）
INSERT INTO categories (name, sort_order) 
VALUES ('食品', 3);

-- 多行批量插入（推荐，效率高）
INSERT INTO categories (name, sort_order) 
VALUES 
    ('图书', 4),
    ('家居', 5),
    ('运动', 6),
    ('美妆', 7);

-- 使用 INSERT ... SELECT 从其他表复制数据
INSERT INTO products_archive (name, price, created_at)
SELECT name, price, created_at 
FROM products 
WHERE created_at < '2024-01-01';

-- INSERT IGNORE：遇到唯一键冲突时忽略而非报错
INSERT IGNORE INTO users (username, email) 
VALUES ('zhangsan', 'zhangsan@example.com');
-- 如果username或email已存在，该行被忽略，不报错

-- INSERT ... ON DUPLICATE KEY UPDATE：冲突时更新
INSERT INTO products (id, name, price, stock) 
VALUES (1, '笔记本电脑', 5999.00, 50)
ON DUPLICATE KEY UPDATE 
    price = VALUES(price),
    stock = VALUES(stock);
-- 如果id=1已存在，则更新price和stock

-- MariaDB 10.3+ 支持使用列名引用（替代VALUES()函数）
INSERT INTO products (id, name, price, stock) 
VALUES (1, '笔记本电脑', 5999.00, 50)
ON DUPLICATE KEY UPDATE 
    price = price,      -- 引用新插入的值
    stock = stock;
```

**UPDATE 更新数据：**

```sql
-- 更新单列
UPDATE products SET price = 4999.00 WHERE id = 1;

-- 更新多列
UPDATE products 
SET price = 4999.00, stock = 45, updated_at = NOW() 
WHERE id = 1;

-- 条件更新
UPDATE products 
SET stock = stock - 1 
WHERE id = 1 AND stock > 0;

-- 批量更新（使用CASE表达式）
UPDATE products 
SET price = CASE 
    WHEN category_id = 1 THEN price * 0.9   -- 电子产品9折
    WHEN category_id = 2 THEN price * 0.8   -- 服装8折
    ELSE price * 0.95                        -- 其他95折
END
WHERE is_on_sale = TRUE;

-- 使用子查询更新
UPDATE products 
SET stock = 0 
WHERE id IN (
    SELECT product_id FROM order_items 
    WHERE quantity > 100
);

-- 更新所有行（慎用！务必加WHERE条件）
-- UPDATE products SET is_on_sale = TRUE;  -- 危险！更新所有行

-- 使用 LIMIT 限制更新行数
UPDATE products SET is_on_sale = FALSE 
WHERE stock = 0 
LIMIT 100;
```

**DELETE 删除数据：**

```sql
-- 条件删除
DELETE FROM products WHERE id = 100;

-- 删除多行
DELETE FROM products WHERE stock = 0 AND is_on_sale = FALSE;

-- 删除所有数据（保留表结构）
DELETE FROM products;
-- 注意：这会删除所有行，但不会重置AUTO_INCREMENT

-- 使用TRUNCATE快速清空表（比DELETE快，重置AUTO_INCREMENT，但不触发触发器）
TRUNCATE TABLE products;

-- 使用子查询删除
DELETE FROM order_items 
WHERE order_id IN (
    SELECT id FROM orders WHERE status = 'cancelled'
);

-- 使用JOIN删除（MariaDB扩展语法）
DELETE items 
FROM order_items items
INNER JOIN orders o ON items.order_id = o.id
WHERE o.status = 'cancelled';

-- 限制删除行数
DELETE FROM logs WHERE created_at < '2024-01-01' LIMIT 1000;
```

**REPLACE 替换数据：**

```sql
-- REPLACE：遇到主键/唯一键冲突时，先删除旧行，再插入新行
REPLACE INTO products (id, name, price, stock) 
VALUES (1, '笔记本电脑Pro', 6999.00, 60);
-- 如果id=1已存在，先删除旧行，再插入新行
-- 注意：REPLACE会触发DELETE和INSERT触发器，且会改变AUTO_INCREMENT

-- 多行REPLACE
REPLACE INTO categories (id, name, sort_order) 
VALUES 
    (1, '电子产品', 1),
    (2, '服装', 2),
    (3, '食品', 3);
```

**事务安全的数据操作：**

```sql
-- 开启事务
START TRANSACTION;

-- 执行一系列操作
INSERT INTO orders (order_no, user_id, total_amount, status) 
VALUES ('ORD20240115001', 1, 5999.00, 'paid');

UPDATE products SET stock = stock - 1 WHERE id = 1 AND stock > 0;

-- 检查结果，确认无误后提交
COMMIT;

-- 如果发现问题，回滚
-- ROLLBACK;
```

### 总结

- 批量 INSERT 比循环单行 INSERT 效率高得多，应尽量使用批量插入
- UPDATE 和 DELETE 务必加 WHERE 条件，避免误操作全表；执行前建议先用 SELECT 验证条件
- `INSERT ... ON DUPLICATE KEY UPDATE` 比 `REPLACE` 更高效，因为不需要先删除再插入
- `INSERT IGNORE` 适合"插入如果存在则忽略"的场景，但要注意它会忽略所有约束冲突错误
- TRUNCATE 比 DELETE 快（不记录逐行日志），但不可回滚、不触发触发器、重置自增ID
- 写操作涉及数据安全，生产环境应在事务中执行，确认无误后再提交
- 大批量 UPDATE/DELETE 可能长时间持有锁，建议分批执行（使用 LIMIT）
- 更新索引列比更新非索引列开销大，因为需要维护索引结构

---

# 第3章：多表查询

## 第10讲：JOIN 连接查询

### 概念

JOIN（连接查询）是将两个或多个表按照指定的条件组合成一个结果集的操作。在实际的数据库设计中，数据通常分散在多个表中（遵循范式），需要通过 JOIN 将相关数据关联起来。JOIN 是关系型数据库最强大的功能之一，也是 SQL 面试和实际开发中的重点。

### 原理

JOIN 的工作原理基于笛卡尔积和过滤条件：

1. **笛卡尔积**：将左表的每一行与右表的每一行组合，产生 N×M 行（N、M 分别为两表行数）
2. **连接条件过滤**：根据 ON 子句的条件，从笛卡尔积中筛选出符合条件的行

MariaDB 优化器会自动选择最优的连接算法，避免真正计算庞大的笛卡尔积：
- **Nested Loop Join（嵌套循环连接）**：对外表每行，扫描内表找匹配行。适合小表驱动大表
- **Block Nested Loop（块嵌套循环）**：将外表数据分块加载到内存，减少内表扫描次数
- **Hash Join（哈希连接）**：MariaDB 10.6+ 支持，对内表构建哈希表，外表探测。适合大表等值连接
- **Index Merge**：利用内表索引快速定位匹配行

JOIN 的类型：

| 类型 | 说明 |
|------|------|
| INNER JOIN（内连接） | 只返回两表中满足连接条件的行 |
| LEFT JOIN（左外连接） | 返回左表所有行，右表无匹配则为 NULL |
| RIGHT JOIN（右外连接） | 返回右表所有行，左表无匹配则为 NULL |
| FULL JOIN（全外连接） | 返回两表所有行，无匹配则为 NULL（MariaDB 用 UNION 模拟） |
| CROSS JOIN（交叉连接） | 返回笛卡尔积 |
| SELF JOIN（自连接） | 表与自身连接 |

### 例子

**准备示例数据：**

```sql
-- 部门表
CREATE TABLE departments (
    id INT PRIMARY KEY,
    name VARCHAR(50)
);
INSERT INTO departments VALUES 
(1, '技术部'), (2, '市场部'), (3, '人事部'), (4, '财务部');

-- 员工表
CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(50),
    dept_id INT,
    salary DECIMAL(10,2)
);
INSERT INTO employees VALUES 
(1, '张三', 1, 15000),
(2, '李四', 1, 12000),
(3, '王五', 2, 10000),
(4, '赵六', 2, 9000),
(5, '钱七', NULL, 8000);  -- 没有部门的员工
```

**INNER JOIN（内连接）：**

```sql
-- 查询有部门的员工及其部门名称
SELECT 
    e.id,
    e.name AS employee_name,
    e.salary,
    d.name AS dept_name
FROM employees e
INNER JOIN departments d ON e.dept_id = d.id;
/*
+----+--------------+--------+-----------+
| id | employee_name| salary | dept_name |
+----+--------------+--------+-----------+
|  1 | 张三         | 15000  | 技术部    |
|  2 | 李四         | 12000  | 技术部    |
|  3 | 王五         | 10000  | 市场部    |
|  4 | 赵六         |  9000  | 市场部    |
+----+--------------+--------+-----------+
注意：钱七没有部门，不在结果中
*/

-- INNER JOIN 可以省略 INNER
SELECT e.name, d.name FROM employees e JOIN departments d ON e.dept_id = d.id;

-- 使用 USING 简化（当两表连接列名相同时）
-- SELECT * FROM table1 JOIN table2 USING (column_name);
```

**LEFT JOIN（左外连接）：**

```sql
-- 查询所有员工及其部门（包括没有部门的员工）
SELECT 
    e.id,
    e.name AS employee_name,
    e.salary,
    d.name AS dept_name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.id;
/*
+----+--------------+--------+-----------+
| id | employee_name| salary | dept_name |
+----+--------------+--------+-----------+
|  1 | 张三         | 15000  | 技术部    |
|  2 | 李四         | 12000  | 技术部    |
|  3 | 王五         | 10000  | 市场部    |
|  4 | 赵六         |  9000  | 市场部    |
|  5 | 钱七         |  8000  | NULL      |  ← 左表所有行都保留
+----+--------------+--------+-----------+
*/

-- LEFT JOIN 常用于查找"不匹配"的行
-- 查询没有部门的员工
SELECT e.name FROM employees e 
LEFT JOIN departments d ON e.dept_id = d.id 
WHERE d.id IS NULL;
/*
+------+
| name |
+------+
| 钱七 |
+------+
*/
```

**RIGHT JOIN（右外连接）：**

```sql
-- 查询所有部门及其员工（包括没有员工的部门）
SELECT 
    d.name AS dept_name,
    e.name AS employee_name,
    e.salary
FROM employees e
RIGHT JOIN departments d ON e.dept_id = d.id;
/*
+-----------+--------------+--------+
| dept_name | employee_name| salary |
+-----------+--------------+--------+
| 技术部    | 张三         | 15000  |
| 技术部    | 李四         | 12000  |
| 市场部    | 王五         | 10000  |
| 市场部    | 赵六         |  9000  |
| 人事部    | NULL         | NULL   |  ← 右表所有行都保留
| 财务部    | NULL         | NULL   |
+-----------+--------------+--------+
*/
```

**多表 JOIN：**

```sql
-- 电商场景：查询订单详情（关联用户、订单、商品、订单详情）
SELECT 
    o.order_no,
    u.username,
    o.created_at AS order_time,
    p.name AS product_name,
    oi.price,
    oi.quantity,
    oi.subtotal
FROM orders o
INNER JOIN users u ON o.user_id = u.id
INNER JOIN order_items oi ON o.id = oi.order_id
INNER JOIN products p ON oi.product_id = p.id
WHERE o.status = 'completed'
ORDER BY o.created_at DESC
LIMIT 20;
```

**SELF JOIN（自连接）：**

```sql
-- 员工表增加上级字段
ALTER TABLE employees ADD COLUMN manager_id INT;
UPDATE employees SET manager_id = NULL WHERE id = 1;  -- 张三没有上级
UPDATE employees SET manager_id = 1 WHERE id IN (2, 3);  -- 李四、王五的上级是张三
UPDATE employees SET manager_id = 3 WHERE id = 4;  -- 赵六的上级是王五
UPDATE employees SET manager_id = 2 WHERE id = 5;  -- 钱七的上级是李四

-- 自连接：查询每个员工及其上级姓名
SELECT 
    e.name AS employee,
    m.name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.id;
/*
+----------+---------+
| employee | manager |
+----------+---------+
| 张三     | NULL    |
| 李四     | 张三    |
| 王五     | 张三    |
| 赵六     | 王五    |
| 钱七     | 李四    |
+----------+---------+
*/
```

**CROSS JOIN（交叉连接）：**

```sql
-- 生成所有组合（如：颜色×尺码）
SELECT c.color, s.size 
FROM (SELECT '红' AS color UNION SELECT '蓝' UNION SELECT '绿') c
CROSS JOIN (SELECT 'S' AS size UNION SELECT 'M' UNION SELECT 'L') s;
/*
+-------+------+
| color | size |
+-------+------+
| 红    | S    |
| 红    | M    |
| 红    | L    |
| 蓝    | S    |
| ...   | ...  |
+-------+------+
9 rows in set
*/
```

### 总结

- INNER JOIN 只返回两表都匹配的行；LEFT JOIN 保留左表所有行；RIGHT JOIN 保留右表所有行
- LEFT JOIN 配合 `WHERE 右表.列 IS NULL` 常用于查找"不匹配"的记录
- 多表 JOIN 时，建议从最小表开始连接，让优化器选择最优执行计划
- 自连接（SELF JOIN）是同一表与自己连接，需使用不同别名区分，常用于层级关系查询
- 连接条件应使用索引列，否则性能会急剧下降
- USING 子句是 ON 的简写，当两表连接列名相同时可以使用
- 避免笛卡尔积：忘记写 ON 条件会产生 N×M 行结果，通常是错误

---

## 第11讲：子查询

### 概念

子查询（Subquery）是嵌套在另一个 SQL 语句中的 SELECT 查询。子查询可以出现在 SELECT、FROM、WHERE、HAVING 子句中，用于提供中间结果给主查询使用。子查询使复杂查询可以分步骤表达，提高了 SQL 的表达能力和灵活性。

### 原理

子查询根据返回结果的不同，分为三类：

1. **标量子查询（Scalar Subquery）**：返回单行单列（一个值），可以用在需要单个值的地方（如 `=`, `>`, `<` 比较中）
2. **行子查询（Row Subquery）**：返回单行多列，可以用在行比较中（如 `(a, b) = (SELECT x, y)`）
3. **表子查询（Table Subquery）**：返回多行多列，用在 FROM 子句或 IN/EXISTS 中

根据与主查询的关系，子查询分为：

- **非相关子查询**：子查询不引用主查询的列，可以独立执行，只执行一次
- **相关子查询**：子查询引用主查询的列，主查询每处理一行就执行一次子查询

子查询的关键字：
- `IN` / `NOT IN`：判断值是否在子查询结果集中
- `ANY` / `SOME`：与子查询结果中的任一值比较满足条件即可
- `ALL`：与子查询结果中的所有值比较都满足条件
- `EXISTS` / `NOT EXISTS`：判断子查询是否返回行

MariaDB 优化器会自动将某些子查询改写为 JOIN，以获得更好的性能。

### 例子

**标量子查询：**

```sql
-- 查询价格高于平均价的商品
SELECT name, price FROM products 
WHERE price > (SELECT AVG(price) FROM products);

-- 查询与"张三"同部门的员工
SELECT name, dept_id FROM employees 
WHERE dept_id = (SELECT dept_id FROM employees WHERE name = '张三')
  AND name != '张三';

-- 在SELECT中使用标量子查询
SELECT 
    e.name,
    e.salary,
    (SELECT AVG(salary) FROM employees) AS avg_salary,
    e.salary - (SELECT AVG(salary) FROM employees) AS diff_from_avg
FROM employees e;
```

**IN 子查询：**

```sql
-- 查询有订单的用户
SELECT username FROM users 
WHERE id IN (SELECT DISTINCT user_id FROM orders);

-- 查询没有订单的用户
SELECT username FROM users 
WHERE id NOT IN (SELECT DISTINCT user_id FROM orders);

-- 注意：NOT IN 遇到NULL会有问题
-- 如果子查询结果包含NULL，NOT IN永远返回空
SELECT username FROM users 
WHERE id NOT IN (SELECT user_id FROM orders WHERE user_id IS NOT NULL);
-- 建议加 WHERE user_id IS NOT NULL 或使用 NOT EXISTS
```

**ANY / ALL 子查询：**

```sql
-- ANY：大于子查询结果中的任意一个值（即大于最小值）
SELECT name, salary FROM employees 
WHERE salary > ANY (SELECT salary FROM employees WHERE dept_id = 2);
-- 等价于大于市场部最低工资

-- ALL：大于子查询结果中的所有值（即大于最大值）
SELECT name, salary FROM employees 
WHERE salary > ALL (SELECT salary FROM employees WHERE dept_id = 2);
-- 等价于大于市场部最高工资

-- = ANY 等价于 IN
SELECT name FROM employees 
WHERE dept_id = ANY (SELECT id FROM departments);
```

**EXISTS 子查询：**

```sql
-- EXISTS：查询有订单的用户（比IN更高效，尤其子查询结果大时）
SELECT u.username FROM users u 
WHERE EXISTS (
    SELECT 1 FROM orders o WHERE o.user_id = u.id
);

-- NOT EXISTS：查询没有订单的用户
SELECT u.username FROM users u 
WHERE NOT EXISTS (
    SELECT 1 FROM orders o WHERE o.user_id = u.id
);

-- 相关子查询：子查询引用了主查询的u.id
-- 主查询每检查一个用户，就执行一次子查询
```

**FROM 子句中的子查询（派生表）：**

```sql
-- 查询每个部门的平均工资，再筛选高于10000的部门
SELECT dept_name, avg_salary
FROM (
    SELECT 
        d.name AS dept_name,
        AVG(e.salary) AS avg_salary
    FROM employees e
    INNER JOIN departments d ON e.dept_id = d.id
    GROUP BY d.id, d.name
) dept_stats
WHERE avg_salary > 10000;

-- 派生表必须指定别名
-- 派生表中的列需要有名字（可用别名）
```

**子查询在 UPDATE/DELETE 中：**

```sql
-- 更新：将每个员工的工资设为其部门平均工资的1.1倍
UPDATE employees e
SET salary = (
    SELECT avg_salary * 1.1 
    FROM (SELECT dept_id, AVG(salary) AS avg_salary FROM employees GROUP BY dept_id) d
    WHERE d.dept_id = e.dept_id
);

-- 删除：删除没有订单详情的订单
DELETE FROM orders 
WHERE id NOT IN (SELECT order_id FROM order_items);
```

**子查询 vs JOIN 性能对比：**

```sql
-- 需求：查询有订单的用户信息

-- 方式1：IN 子查询
SELECT * FROM users WHERE id IN (SELECT user_id FROM orders);

-- 方式2：EXISTS 相关子查询
SELECT * FROM users u WHERE EXISTS (SELECT 1 FROM orders o WHERE o.user_id = u.id);

-- 方式3：JOIN + DISTINCT
SELECT DISTINCT u.* FROM users u INNER JOIN orders o ON u.id = o.user_id;

-- 三种方式结果相同，性能视数据量和索引情况而定
-- 一般原则：子查询结果小用IN，外表小用EXISTS，都能走索引时JOIN通常最快
```

### 总结

- 标量子查询返回单值，可用于 SELECT 列表、WHERE 比较等位置
- IN 适合子查询结果较小的情况；EXISTS 适合外表小、子查询表大的情况
- NOT IN 要注意 NULL 值问题，子查询结果含 NULL 会导致整个查询返回空，建议用 NOT EXISTS 替代
- 相关子查询会多次执行，性能通常不如非相关子查询，但表达力更强
- FROM 子句中的子查询（派生表）必须指定别名
- MariaDB 优化器会自动将部分子查询优化为 JOIN，但复杂子查询仍建议手动改写为 JOIN
- 子查询虽然灵活，但过度嵌套会降低可读性和性能，应适度使用

---

## 第12讲：UNION 联合查询

### 概念

UNION 是将多个 SELECT 语句的结果集合并成一个结果集的操作。与 JOIN 不同，JOIN 是横向合并（增加列），而 UNION 是纵向合并（增加行）。UNION 常用于合并结构相同但来源不同的数据，如合并多个表的数据、合并不同条件的查询结果等。

### 原理

UNION 的工作原理：

1. 分别执行每个 SELECT 语句
2. 将所有结果集纵向合并
3. **UNION**：对合并结果进行去重（类似 DISTINCT）
4. **UNION ALL**：保留所有行，不去重

UNION 的规则：
- 每个 SELECT 语句必须返回相同数量的列
- 对应列的数据类型需要兼容（MariaDB 会自动转换）
- 结果集的列名取自第一个 SELECT 语句的列名
- ORDER BY 和 LIMIT 只能出现在最后一个 SELECT 之后，作用于整个结果集

性能方面，UNION ALL 比 UNION 快，因为 UNION 需要额外的去重操作（通常使用临时表）。如果确定各 SELECT 结果无重复，应优先使用 UNION ALL。

### 例子

**基本 UNION：**

```sql
-- 合并两个表的查询结果（自动去重）
SELECT name, email FROM users
UNION
SELECT name, email FROM admins;
-- 如果两个表有相同的(name, email)组合，只保留一行
```

**UNION ALL（不去重）：**

```sql
-- 合并两个表的查询结果（保留所有行）
SELECT name, email FROM users
UNION ALL
SELECT name, email FROM admins;
-- 即使有重复也全部保留，性能更好
```

**合并不同条件的结果：**

```sql
-- 查询：价格最高的3个商品 + 价格最低的3个商品
(SELECT name, price FROM products ORDER BY price DESC LIMIT 3)
UNION ALL
(SELECT name, price FROM products ORDER BY price ASC LIMIT 3);
/*
注意：每个SELECT用括号括起，可以有自己的ORDER BY和LIMIT
最终结果不会排序，如需排序需在最外层加ORDER BY
*/
```

**合并不同表的数据：**

```sql
-- 电商场景：合并订单和退款的流水记录
SELECT 
    'order' AS type,
    order_no AS transaction_no,
    total_amount AS amount,
    created_at
FROM orders
WHERE created_at >= '2024-01-01'

UNION ALL

SELECT 
    'refund' AS type,
    refund_no AS transaction_no,
    -amount AS amount,  -- 退款用负数表示
    created_at
FROM refunds
WHERE created_at >= '2024-01-01'

ORDER BY created_at DESC;
```

**UNION 与排序：**

```sql
-- 整体排序：ORDER BY 放在最后
SELECT name, price, '高价' AS category FROM products WHERE price > 1000
UNION ALL
SELECT name, price, '低价' AS category FROM products WHERE price <= 1000
ORDER BY price DESC;
-- ORDER BY 作用于整个UNION结果

-- 如果需要对每个SELECT单独排序，需要用子查询
SELECT * FROM (
    SELECT name, price, '高价' AS category FROM products WHERE price > 1000
    UNION ALL
    SELECT name, price, '低价' AS category FROM products WHERE price <= 1000
) t
ORDER BY category, price DESC;
```

**UNION 实现全外连接：**

```sql
-- MariaDB不直接支持FULL OUTER JOIN，用LEFT JOIN UNION RIGHT JOIN模拟
SELECT e.name, d.name AS dept_name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.id

UNION

SELECT e.name, d.name AS dept_name
FROM employees e
RIGHT JOIN departments d ON e.dept_id = d.id;
-- 结果包含：有部门的员工、没有部门的员工、没有员工的部门
```

### 总结

- UNION 自动去重，UNION ALL 不去重；确定无重复时优先用 UNION ALL，性能更好
- 每个 SELECT 必须返回相同列数，对应列类型兼容
- 结果集列名取自第一个 SELECT，如需自定义在第一个 SELECT 中使用别名
- ORDER BY 和 LIMIT 只能出现在最后一个 SELECT 后，作用于整个结果集
- 如需对每个 SELECT 单独排序，需将 UNION 包在子查询中再排序
- UNION 可用于合并不同表的数据、不同条件的结果，以及模拟 FULL OUTER JOIN
- 大量 UNION 可能产生临时表，影响性能，可考虑用 CASE WHEN 替代部分场景

---

## 第13讲：视图

### 概念

视图（View）是一个虚拟表，其内容由 SQL 查询定义。与实际表不同，视图不存储数据（物化视图除外），而是每次查询时动态执行底层 SQL 生成结果。视图类似于编程语言中的函数，将复杂查询封装起来，提供简化的访问接口。

### 原理

视图的本质是一条存储的 SELECT 语句。当查询视图时，MariaDB 会：

1. 将视图定义的 SQL 与查询视图的 SQL 合并
2. 重写为对底层表的查询
3. 优化并执行重写后的查询

这个过程称为"查询重写"（Query Rewriting），意味着视图查询的性能与直接查询底层表基本相同（某些复杂视图可能有额外开销）。

视图的作用：
- **简化查询**：将复杂的多表 JOIN、子查询封装为简单的视图
- **数据安全**：通过视图限制用户只能访问特定列或行（行级/列级安全）
- **逻辑抽象**：底层表结构变化时，只需修改视图，不影响应用程序
- **数据一致性**：确保不同用户看到一致的数据格式

视图的局限性：
- 视图不能建立索引（普通视图），查询性能依赖底层表索引
- 复杂视图（含 JOIN、聚合、子查询）可能无法直接 UPDATE/INSERT
- 视图嵌套层次过多会影响可读性和性能

### 例子

**创建基本视图：**

```sql
-- 创建视图：商品销售汇总
CREATE VIEW v_product_sales AS
SELECT 
    p.id,
    p.name,
    p.price,
    p.stock,
    COALESCE(SUM(oi.quantity), 0) AS total_sold,
    COALESCE(SUM(oi.subtotal), 0) AS total_revenue
FROM products p
LEFT JOIN order_items oi ON p.id = oi.product_id
GROUP BY p.id, p.name, p.price, p.stock;

-- 使用视图（像普通表一样查询）
SELECT * FROM v_product_sales;
SELECT name, total_sold, total_revenue 
FROM v_product_sales 
WHERE total_sold > 0 
ORDER BY total_revenue DESC;

-- 创建视图时指定列名
CREATE VIEW v_product_summary (product_name, unit_price, inventory) AS
SELECT name, price, stock FROM products;
```

**创建带条件的视图：**

```sql
-- 只包含在售商品的视图
CREATE VIEW v_active_products AS
SELECT id, name, price, stock, category_id 
FROM products 
WHERE is_on_sale = TRUE 
WITH CHECK OPTION;
-- WITH CHECK OPTION：通过视图INSERT/UPDATE的数据也必须满足WHERE条件

-- 查询视图
SELECT * FROM v_active_products;

-- 尝试通过视图插入下架商品（会被CHECK OPTION拒绝）
-- INSERT INTO v_active_products (name, price, is_on_sale) VALUES ('测试', 100, FALSE);
-- 报错：CHECK OPTION failed
```

**可更新视图：**

```sql
-- 简单视图（单表、无聚合）可以直接INSERT/UPDATE/DELETE
CREATE VIEW v_tech_employees AS
SELECT id, name, salary, dept_id 
FROM employees 
WHERE dept_id = 1;

-- 通过视图更新数据（实际更新底层表）
UPDATE v_tech_employees SET salary = salary + 1000 WHERE id = 1;

-- 通过视图插入数据
INSERT INTO v_tech_employees (name, salary, dept_id) VALUES ('新员工', 10000, 1);

-- 通过视图删除数据
DELETE FROM v_tech_employees WHERE id = 10;
```

**视图用于数据安全：**

```sql
-- 创建只暴露部分列的视图（隐藏敏感信息）
CREATE VIEW v_user_public AS
SELECT id, username, created_at FROM users;
-- 不包含password_hash、email、phone等敏感列

-- 创建只暴露部分行的视图（行级安全）
CREATE VIEW v_own_orders AS
SELECT * FROM orders WHERE user_id = CURRENT_USER_ID();
-- 每个用户只能看到自己的订单（需要配合自定义函数）
```

**管理视图：**

```sql
-- 查看所有视图
SHOW FULL TABLES WHERE Table_type = 'VIEW';

-- 查看视图定义
SHOW CREATE VIEW v_product_sales\G

-- 修改视图（替换定义）
CREATE OR REPLACE VIEW v_product_sales AS
SELECT 
    p.id,
    p.name,
    p.price,
    p.stock,
    p.category_id,
    COALESCE(SUM(oi.quantity), 0) AS total_sold,
    COALESCE(SUM(oi.subtotal), 0) AS total_revenue
FROM products p
LEFT JOIN order_items oi ON p.id = oi.product_id
GROUP BY p.id, p.name, p.price, p.stock, p.category_id;

-- ALTER VIEW 也可以修改视图
ALTER VIEW v_product_sales AS
SELECT id, name, price FROM products;

-- 删除视图
DROP VIEW IF EXISTS v_product_sales;
```

### 总结

- 视图是存储的 SELECT 语句，不实际存储数据，查询时动态生成
- 视图主要用于简化复杂查询、实现数据安全和逻辑抽象
- 简单视图（单表、无聚合）是可更新的，可以直接 INSERT/UPDATE/DELETE
- `WITH CHECK OPTION` 确保通过视图修改的数据仍满足视图条件
- 视图查询性能依赖底层表索引，视图本身不能建索引
- 使用 `CREATE OR REPLACE VIEW` 可以方便地更新视图定义
- 视图嵌套不宜过深（建议不超过 3 层），否则影响可读性和性能
- 生产环境中，视图名称建议加 `v_` 前缀，与表区分

---

# 第4章：函数与聚合

## 第14讲：常用内置函数

### 概念

MariaDB 提供了丰富的内置函数，用于在 SQL 语句中对数据进行计算、转换和处理。内置函数大致分为：字符串函数、数值函数、日期时间函数、流程控制函数、JSON 函数等。熟练使用内置函数可以大大简化 SQL 编写，减少应用程序中的数据处理逻辑。

### 原理

MariaDB 函数的工作原理是在 SQL 执行过程中，对每行数据调用函数进行计算，返回结果。函数可以出现在 SELECT 列表、WHERE 条件、ORDER BY、HAVING 等位置。

函数分为：
- **标量函数**：对每行数据返回一个值，如 `UPPER(name)`、`ROUND(price)`
- **聚合函数**：对多行数据返回一个值，如 `COUNT(*)`、`AVG(price)`（下一讲详解）

需要注意的是，在 WHERE 子句中对列使用函数会导致索引失效。例如 `WHERE YEAR(created_at) = 2024` 无法使用 `created_at` 上的索引，应改写为 `WHERE created_at >= '2024-01-01' AND created_at < '2025-01-01'`。

### 例子

**字符串函数：**

```sql
-- 大小写转换
SELECT UPPER('hello') AS upper_str;      -- 'HELLO'
SELECT LOWER('WORLD') AS lower_str;      -- 'world'

-- 字符串长度
SELECT LENGTH('你好') AS byte_len;        -- 6（UTF-8下中文3字节）
SELECT CHAR_LENGTH('你好') AS char_len;   -- 2（字符数）

-- 字符串拼接
SELECT CONCAT('Hello', ' ', 'World') AS greeting;  -- 'Hello World'
SELECT CONCAT(name, '(', email, ')') AS user_info FROM users LIMIT 1;
-- CONCAT_WS：用分隔符拼接
SELECT CONCAT_WS('-', '2024', '01', '15') AS date_str;  -- '2024-01-15'

-- 子字符串
SELECT SUBSTRING('Hello World', 1, 5) AS sub;   -- 'Hello'（位置从1开始）
SELECT SUBSTRING('Hello World', 7) AS sub;       -- 'World'
SELECT LEFT('Hello', 3) AS left_part;            -- 'Hel'
SELECT RIGHT('Hello', 3) AS right_part;          -- 'llo'

-- 查找与替换
SELECT INSTR('Hello World', 'World') AS pos;     -- 7（位置）
SELECT REPLACE('Hello World', 'World', 'MariaDB') AS replaced;  -- 'Hello MariaDB'

-- 去除空格
SELECT TRIM('  hello  ') AS trimmed;             -- 'hello'
SELECT LTRIM('  hello') AS ltrimmed;             -- 'hello'
SELECT RTRIM('hello  ') AS rtrimmed;             -- 'hello'

-- 填充
SELECT LPAD('5', 3, '0') AS padded;              -- '005'
SELECT RPAD('5', 3, '*') AS padded;              -- '5**'

-- 实际应用：格式化手机号
SELECT 
    phone AS original,
    CONCAT(
        '(', SUBSTRING(phone, 1, 3), ') ',
        SUBSTRING(phone, 4, 4), '-',
        SUBSTRING(phone, 8, 4)
    ) AS formatted
FROM users 
WHERE phone IS NOT NULL 
LIMIT 1;
-- 13812345678 → (138) 1234-5678
```

**数值函数：**

```sql
-- 四舍五入
SELECT ROUND(3.14159, 2) AS rounded;    -- 3.14
SELECT ROUND(3.5) AS rounded;           -- 4
SELECT ROUND(2.5) AS rounded;           -- 3（银行家舍入：四舍六入五成双）

-- 向上/向下取整
SELECT CEIL(3.1) AS ceil_val;           -- 4
SELECT FLOOR(3.9) AS floor_val;         -- 3

-- 截断
SELECT TRUNCATE(3.14159, 2) AS trunc;   -- 3.14

-- 取模
SELECT MOD(10, 3) AS remainder;         -- 1
SELECT 10 % 3 AS remainder;             -- 1（等价）

-- 绝对值
SELECT ABS(-5) AS abs_val;              -- 5

-- 幂与平方根
SELECT POW(2, 10) AS power;             -- 1024
SELECT SQRT(16) AS sqrt_val;            -- 4

-- 随机数
SELECT RAND() AS random_val;            -- 0~1之间随机小数
SELECT FLOOR(RAND() * 100) AS random_int;  -- 0~99随机整数
SELECT FLOOR(RAND() * 100) + 1 AS random_int;  -- 1~100随机整数

-- 实际应用：计算折扣价
SELECT 
    name,
    price,
    ROUND(price * 0.85, 2) AS discount_price,
    ROUND(price * 0.85, 2) - price AS discount_amount
FROM products LIMIT 5;
```

**日期时间函数：**

```sql
-- 获取当前日期时间
SELECT NOW() AS current_datetime;       -- 2024-01-15 10:30:00
SELECT CURDATE() AS current_date;       -- 2024-01-15
SELECT CURTIME() AS current_time;       -- 10:30:00
SELECT SYSDATE() AS sys_datetime;       -- 执行时的时间（与NOW略有不同）

-- 提取日期部分
SELECT YEAR('2024-01-15') AS year;      -- 2024
SELECT MONTH('2024-01-15') AS month;    -- 1
SELECT DAY('2024-01-15') AS day;        -- 15
SELECT DAYNAME('2024-01-15') AS dayname; -- Monday
SELECT MONTHNAME('2024-01-15') AS monthname; -- January
SELECT DAYOFWEEK('2024-01-15') AS dow;  -- 2（周日=1，周一=2）
SELECT WEEK('2024-01-15') AS week;      -- 2（第几周）
SELECT QUARTER('2024-01-15') AS quarter; -- 1（季度）

-- 日期加减
SELECT DATE_ADD('2024-01-15', INTERVAL 1 DAY) AS tomorrow;     -- 2024-01-16
SELECT DATE_ADD('2024-01-15', INTERVAL 1 MONTH) AS next_month; -- 2024-02-15
SELECT DATE_ADD('2024-01-15', INTERVAL -7 DAY) AS last_week;   -- 2024-01-08
SELECT DATE_SUB('2024-01-15', INTERVAL 1 YEAR) AS last_year;   -- 2023-01-15

-- 日期差
SELECT DATEDIFF('2024-01-15', '2024-01-01') AS diff;  -- 14（天数）
SELECT TIMEDIFF('10:30:00', '09:00:00') AS time_diff;  -- 01:30:00
SELECT TIMESTAMPDIFF(YEAR, '1990-05-15', '2024-01-15') AS age;  -- 33（年数）

-- 日期格式化
SELECT DATE_FORMAT('2024-01-15', '%Y年%m月%d日') AS formatted;  -- 2024年01月15日
SELECT DATE_FORMAT(NOW(), '%Y-%m-%d %H:%i:%s') AS formatted;    -- 2024-01-15 10:30:00
SELECT DATE_FORMAT(NOW(), '%W, %M %e, %Y') AS formatted;        -- Monday, January 15, 2024

-- 字符串转日期
SELECT STR_TO_DATE('2024-01-15', '%Y-%m-%d') AS date;
SELECT STR_TO_DATE('15/01/2024', '%d/%m/%Y') AS date;

-- 实际应用：查询本月订单
SELECT order_no, created_at 
FROM orders 
WHERE created_at >= DATE_FORMAT(CURDATE(), '%Y-%m-01')
  AND created_at < DATE_ADD(DATE_FORMAT(CURDATE(), '%Y-%m-01'), INTERVAL 1 MONTH);
```

**流程控制函数：**

```sql
-- IF函数：IF(条件, 真值, 假值)
SELECT name, price, IF(price > 1000, '高价', '低价') AS price_level 
FROM products;

-- IFNULL：IFNULL(值, 默认值)，值为NULL返回默认值
SELECT name, IFNULL(description, '暂无描述') AS description 
FROM products;

-- NULLIF：NULLIF(a, b)，a=b返回NULL，否则返回a
SELECT NULLIF(5, 5) AS result;  -- NULL
SELECT NULLIF(5, 3) AS result;  -- 5

-- COALESCE：返回第一个非NULL值
SELECT 
    name,
    COALESCE(phone, email, '无联系方式') AS contact
FROM users;

-- CASE表达式
-- 简单CASE
SELECT 
    name,
    category_id,
    CASE category_id
        WHEN 1 THEN '电子产品'
        WHEN 2 THEN '服装'
        WHEN 3 THEN '食品'
        ELSE '其他'
    END AS category_name
FROM products;

-- 搜索CASE（更灵活）
SELECT 
    name,
    price,
    CASE 
        WHEN price > 5000 THEN '奢侈品'
        WHEN price > 1000 THEN '高档'
        WHEN price > 100 THEN '中档'
        ELSE '低档'
    END AS price_level,
    CASE 
        WHEN stock = 0 THEN '缺货'
        WHEN stock < 10 THEN '库存不足'
        ELSE '库存充足'
    END AS stock_status
FROM products;
```

### 总结

- 字符串函数中，`LENGTH` 返回字节数，`CHAR_LENGTH` 返回字符数，处理中文时要注意区分
- `CONCAT_WS` 用分隔符拼接，自动跳过 NULL 值，比 `CONCAT` 更安全
- 日期函数中 `DATE_ADD`/`DATE_SUB` 配合 `INTERVAL` 是最灵活的日期计算方式
- `TIMESTAMPDIFF` 可以计算年/月/日/时/分/秒的差值，比 `DATEDIFF`（只算天数）更灵活
- `CASE` 表达式是 SQL 中的条件分支，搜索 CASE 比简单 CASE 更灵活
- `COALESCE` 返回第一个非 NULL 值，适合处理多列的默认值逻辑
- **重要**：WHERE 子句中对列使用函数会导致索引失效，应尽量避免或改写条件

---

## 第15讲：聚合函数与 GROUP BY

### 概念

聚合函数（Aggregate Functions）是对一组值进行计算并返回单个值的函数。常见的聚合函数包括 COUNT、SUM、AVG、MAX、MIN 等。GROUP BY 子句用于将数据按指定列分组，使聚合函数分别计算每个组的统计值。聚合函数与 GROUP BY 配合使用，是数据分析的核心工具。

### 原理

聚合函数的工作原理：

1. **无 GROUP BY**：聚合函数对整个结果集计算，返回一行结果
2. **有 GROUP BY**：先按 GROUP BY 列分组，再对每组计算聚合函数，每组返回一行

执行顺序（结合 WHERE）：
1. FROM：获取数据
2. WHERE：行级过滤（在分组前过滤）
3. GROUP BY：分组
4. 聚合函数计算
5. HAVING：组级过滤（在分组后过滤）
6. SELECT：选择列

关键规则：
- SELECT 中出现非聚合列时，这些列必须出现在 GROUP BY 中（除非关闭 ONLY_FULL_GROUP_BY 模式）
- 聚合函数会自动忽略 NULL 值（COUNT(*) 除外）
- WHERE 中不能使用聚合函数，聚合过滤需用 HAVING

### 例子

**基本聚合函数：**

```sql
-- COUNT：计数
SELECT COUNT(*) FROM products;                    -- 总行数（含NULL）
SELECT COUNT(id) FROM products;                   -- id非NULL的行数
SELECT COUNT(description) FROM products;          -- description非NULL的行数
SELECT COUNT(DISTINCT category_id) FROM products; -- 去重后计数

-- SUM：求和
SELECT SUM(stock) FROM products;                  -- 总库存
SELECT SUM(price * stock) FROM products;          -- 库存总价值

-- AVG：平均值（自动忽略NULL）
SELECT AVG(price) FROM products;                  -- 平均价格
SELECT AVG(price) FROM products WHERE category_id = 1;  -- 分类1的平均价格

-- MAX / MIN：最大/最小值
SELECT MAX(price), MIN(price) FROM products;
SELECT MAX(created_at) FROM orders;  -- 最新订单时间

-- 组合使用
SELECT 
    COUNT(*) AS total_products,
    COUNT(DISTINCT category_id) AS category_count,
    AVG(price) AS avg_price,
    MIN(price) AS min_price,
    MAX(price) AS max_price,
    SUM(stock) AS total_stock
FROM products;
```

**GROUP BY 分组：**

```sql
-- 每个分类的商品数量和平均价格
SELECT 
    category_id,
    COUNT(*) AS product_count,
    AVG(price) AS avg_price,
    MIN(price) AS min_price,
    MAX(price) AS max_price
FROM products
GROUP BY category_id;
/*
+-------------+---------------+------------+------------+------------+
| category_id | product_count | avg_price  | min_price  | max_price  |
+-------------+---------------+------------+------------+------------+
|           1 |            15 |  3299.5000 |   599.00   |   8999.00  |
|           2 |            23 |   456.7826 |    29.00   |   1999.00  |
|           3 |             8 |   125.5000 |    15.00   |    399.00  |
+-------------+---------------+------------+------------+------------+
*/

-- 每个用户的订单数量和总消费
SELECT 
    user_id,
    COUNT(*) AS order_count,
    SUM(total_amount) AS total_spent,
    AVG(total_amount) AS avg_order_value,
    MAX(created_at) AS last_order_time
FROM orders
GROUP BY user_id
ORDER BY total_spent DESC;
```

**多列分组：**

```sql
-- 按分类和是否在售分组统计
SELECT 
    category_id,
    is_on_sale,
    COUNT(*) AS product_count,
    AVG(price) AS avg_price
FROM products
GROUP BY category_id, is_on_sale
ORDER BY category_id, is_on_sale;
/*
+-------------+------------+---------------+------------+
| category_id | is_on_sale | product_count | avg_price  |
+-------------+------------+---------------+------------+
|           1 |          0 |             2 |  4599.5000 |
|           1 |          1 |            13 |  3145.3077 |
|           2 |          0 |             5 |   678.4000 |
|           2 |          1 |            18 |   412.5556 |
+-------------+------------+---------------+------------+
*/
```

**按日期分组统计：**

```sql
-- 每天的订单数量和销售额
SELECT 
    DATE(created_at) AS order_date,
    COUNT(*) AS order_count,
    SUM(total_amount) AS daily_revenue,
    AVG(total_amount) AS avg_order_value
FROM orders
WHERE created_at >= '2024-01-01'
GROUP BY DATE(created_at)
ORDER BY order_date DESC;

-- 按月分组统计
SELECT 
    DATE_FORMAT(created_at, '%Y-%m') AS month,
    COUNT(*) AS order_count,
    SUM(total_amount) AS monthly_revenue
FROM orders
WHERE created_at >= '2023-01-01'
GROUP BY DATE_FORMAT(created_at, '%Y-%m')
ORDER BY month DESC;

-- 按小时分组统计（分析订单高峰时段）
SELECT 
    HOUR(created_at) AS hour,
    COUNT(*) AS order_count
FROM orders
WHERE DATE(created_at) = CURDATE()
GROUP BY HOUR(created_at)
ORDER BY hour;
```

**GROUP BY 与 JOIN 结合：**

```sql
-- 每个分类的销售额排名
SELECT 
    c.name AS category_name,
    COUNT(DISTINCT o.id) AS order_count,
    SUM(oi.subtotal) AS total_revenue,
    AVG(oi.subtotal) AS avg_item_value
FROM categories c
INNER JOIN products p ON c.id = p.category_id
INNER JOIN order_items oi ON p.id = oi.product_id
INNER JOIN orders o ON oi.order_id = o.id
WHERE o.status = 'completed'
GROUP BY c.id, c.name
ORDER BY total_revenue DESC;
```

**GROUP_CONCAT 函数：**

```sql
-- 将分组中的值拼接成字符串
SELECT 
    category_id,
    COUNT(*) AS product_count,
    GROUP_CONCAT(name) AS product_names,
    GROUP_CONCAT(name ORDER BY price DESC SEPARATOR ' | ') AS names_by_price
FROM products
GROUP BY category_id;
/*
+-------------+---------------+------------------------------------------+
| category_id | product_count | product_names                            |
+-------------+---------------+------------------------------------------+
|           1 |             3 | 笔记本电脑,智能手机,平板电脑              |
|           2 |             2 | T恤,牛仔裤                               |
+-------------+---------------+------------------------------------------+
*/

-- 限制拼接长度（默认1024字节）
SET SESSION group_concat_max_len = 10000;
```

### 总结

- `COUNT(*)` 统计所有行（含 NULL），`COUNT(列名)` 统计非 NULL 行，`COUNT(DISTINCT 列)` 去重计数
- 聚合函数自动忽略 NULL 值（COUNT(*) 除外），`AVG` 只对非 NULL 值求平均
- GROUP BY 后 SELECT 中非聚合列必须出现在 GROUP BY 中（ONLY_FULL_GROUP_BY 模式下）
- WHERE 在分组前过滤行，HAVING 在分组后过滤组，两者不能混用
- 多列 GROUP BY 时，按所有列的组合分组
- 按日期分组常用 `DATE()` 或 `DATE_FORMAT()` 函数提取日期部分
- `GROUP_CONCAT` 可以将分组内的值拼接成字符串，常用于生成逗号分隔列表
- 聚合查询的性能依赖分组列上的索引，大数据量分组应确保有合适索引

---

## 第16讲：HAVING 与分组过滤

### 概念

HAVING 子句用于对 GROUP BY 分组后的结果进行过滤。与 WHERE 不同，WHERE 在分组前过滤行，而 HAVING 在分组后过滤组。HAVING 可以使用聚合函数进行条件判断，这是 WHERE 无法做到的。HAVING 是分组查询中不可或缺的组成部分。

### 原理

HAVING 的工作原理和执行时机：

```
FROM → WHERE → GROUP BY → 聚合计算 → HAVING → SELECT → ORDER BY → LIMIT
```

HAVING 在聚合计算之后执行，因此可以使用聚合函数作为过滤条件。例如 `HAVING COUNT(*) > 5` 表示只保留行数大于 5 的组。

WHERE 与 HAVING 的区别：

| 特性 | WHERE | HAVING |
|------|-------|--------|
| 执行时机 | 分组前 | 分组后 |
| 能否用聚合函数 | 不能 | 能 |
| 过滤对象 | 行 | 组 |
| 是否需要GROUP BY | 不需要 | 通常需要（无GROUP BY时HAVING过滤整个结果集） |

实际应用中，可以同时使用 WHERE 和 HAVING：WHERE 先过滤掉不需要参与分组的行（减少分组数据量），HAVING 再过滤不符合条件的组。

### 例子

**基本 HAVING 用法：**

```sql
-- 查询商品数量超过5个的分类
SELECT 
    category_id,
    COUNT(*) AS product_count
FROM products
GROUP BY category_id
HAVING COUNT(*) > 5;

-- 查询平均价格超过1000的分类
SELECT 
    category_id,
    COUNT(*) AS product_count,
    AVG(price) AS avg_price
FROM products
GROUP BY category_id
HAVING AVG(price) > 1000;
```

**WHERE 与 HAVING 配合使用：**

```sql
-- 查询在售商品中，销售额超过10000的分类
SELECT 
    category_id,
    COUNT(*) AS product_count,
    SUM(price * stock) AS total_value
FROM products
WHERE is_on_sale = TRUE        -- 先过滤：只统计在售商品
GROUP BY category_id
HAVING SUM(price * stock) > 10000;  -- 再过滤：总价值超过10000的分类

-- 查询2024年消费超过5000的用户
SELECT 
    user_id,
    COUNT(*) AS order_count,
    SUM(total_amount) AS total_spent
FROM orders
WHERE created_at >= '2024-01-01'    -- 先过滤：只统计2024年订单
  AND status = 'completed'
GROUP BY user_id
HAVING SUM(total_amount) > 5000;     -- 再过滤：消费超过5000的用户
```

**多条件 HAVING：**

```sql
-- 查询订单数>=3且平均金额>500的用户
SELECT 
    user_id,
    COUNT(*) AS order_count,
    AVG(total_amount) AS avg_amount,
    SUM(total_amount) AS total_spent
FROM orders
WHERE status = 'completed'
GROUP BY user_id
HAVING COUNT(*) >= 3 AND AVG(total_amount) > 500;

-- 使用别名（MariaDB支持在HAVING中使用SELECT别名）
SELECT 
    user_id,
    COUNT(*) AS order_count,
    AVG(total_amount) AS avg_amount
FROM orders
GROUP BY user_id
HAVING order_count >= 3 AND avg_amount > 500;
-- 注意：不是所有数据库都支持别名，标准SQL中HAVING不能用别名
```

**HAVING 中的复杂条件：**

```sql
-- 查询销售额前3的分类（先分组过滤，再排序限制）
SELECT 
    c.name AS category_name,
    COUNT(DISTINCT o.id) AS order_count,
    SUM(oi.subtotal) AS total_revenue
FROM categories c
INNER JOIN products p ON c.id = p.category_id
INNER JOIN order_items oi ON p.id = oi.product_id
INNER JOIN orders o ON oi.order_id = o.id
WHERE o.status = 'completed'
GROUP BY c.id, c.name
HAVING total_revenue > 1000
ORDER BY total_revenue DESC
LIMIT 3;

-- 查询库存价值占比超过10%的分类
SELECT 
    category_id,
    SUM(price * stock) AS stock_value,
    SUM(price * stock) / (SELECT SUM(price * stock) FROM products) * 100 AS percentage
FROM products
GROUP BY category_id
HAVING percentage > 10
ORDER BY percentage DESC;
```

### 总结

- HAVING 用于过滤分组后的结果，可以使用聚合函数，这是 WHERE 做不到的
- WHERE 在分组前过滤行（减少分组数据量），HAVING 在分组后过滤组
- 同时使用 WHERE 和 HAVING：WHERE 过滤原始行，HAVING 过滤聚合结果
- 标准SQL中 HAVING 不能使用 SELECT 别名，但 MariaDB 扩展支持（建议避免依赖此特性）
- HAVING 条件中可以使用聚合函数、列名、常量，以及它们的组合
- 性能建议：能在 WHERE 中过滤的尽量在 WHERE 过滤，减少分组计算量
- HAVING 常用于"找出满足某聚合条件的组"的场景，如"消费超过X的用户"、"销量超过Y的商品"

---

## 第17讲：窗口函数

### 概念

窗口函数（Window Functions）是一种特殊的函数，它在结果集的"窗口"（一组相关行）上进行计算，但不像聚合函数那样将多行合并为一行。窗口函数为每行返回一个值，同时可以访问其他行的数据。窗口函数常用于排名、累计计算、移动平均等复杂分析场景。MariaDB 从 10.2 版本开始支持窗口函数。

### 原理

窗口函数的语法结构：

```sql
函数名(参数) OVER (
    [PARTITION BY 分组列]     -- 将数据分区，类似GROUP BY但不合并行
    [ORDER BY 排序列]         -- 区内排序
    [ROWS/RANGE BETWEEN ...]  -- 定义窗口范围
)
```

工作原理：
1. **PARTITION BY**：将结果集按指定列分区，每个分区独立计算
2. **ORDER BY**：在分区内排序，确定计算顺序
3. **窗口范围**：定义参与计算的行范围（如"当前行及前2行"）

窗口函数与聚合函数的区别：
- 聚合函数 + GROUP BY：多行合并为一行
- 窗口函数 + OVER：每行都保留，额外添加计算列

窗口函数分类：
- **排序函数**：ROW_NUMBER、RANK、DENSE_RANK
- **聚合窗口函数**：SUM、AVG、COUNT、MAX、MIN + OVER
- **偏移函数**：LAG、LEAD
- **取值函数**：FIRST_VALUE、LAST_VALUE、NTH_VALUE

### 例子

**排名函数：**

```sql
-- ROW_NUMBER：行号（不重复，即使值相同）
SELECT 
    name,
    category_id,
    price,
    ROW_NUMBER() OVER (ORDER BY price DESC) AS row_num,
    ROW_NUMBER() OVER (PARTITION BY category_id ORDER BY price DESC) AS cat_row_num
FROM products;
/*
+----------+-------------+--------+---------+-------------+
| name     | category_id | price  | row_num | cat_row_num |
+----------+-------------+--------+---------+-------------+
| 笔记本   |           1 | 8999   |       1 |           1 |
| 手机     |           1 | 5999   |       2 |           2 |
| 平板     |           1 | 2599   |       3 |           3 |
| 大衣     |           2 | 1999   |       4 |           1 |
| 裤子     |           2 |  599   |       5 |           2 |
+----------+-------------+--------+---------+-------------+
*/

-- RANK：排名（值相同则排名相同，下一个排名跳过）
SELECT 
    name,
    price,
    RANK() OVER (ORDER BY price DESC) AS rank_num
FROM products;
-- 如果两个商品价格相同并列第2名，下一个是第4名（跳过3）

-- DENSE_RANK：密集排名（值相同排名相同，下一个排名不跳过）
SELECT 
    name,
    price,
    DENSE_RANK() OVER (ORDER BY price DESC) AS dense_rank
FROM products;
-- 如果两个商品并列第2名，下一个是第3名（不跳过）
```

**聚合窗口函数：**

```sql
-- 累计求和：按日期累计销售额
SELECT 
    DATE(created_at) AS order_date,
    total_amount,
    SUM(total_amount) OVER (ORDER BY DATE(created_at)) AS cumulative_total
FROM orders
WHERE status = 'completed'
ORDER BY order_date;
/*
+------------+--------------+-----------------+
| order_date | total_amount | cumulative_total|
+------------+--------------+-----------------+
| 2024-01-01 |       500.00 |          500.00 |
| 2024-01-02 |       300.00 |          800.00 |  ← 累计
| 2024-01-03 |       700.00 |         1500.00 |
| 2024-01-04 |       200.00 |         1700.00 |
+------------+--------------+-----------------+
*/

-- 移动平均：3天移动平均
SELECT 
    DATE(created_at) AS order_date,
    total_amount,
    AVG(total_amount) OVER (
        ORDER BY DATE(created_at)
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ) AS moving_avg
FROM orders
ORDER BY order_date;
-- 计算当前行及前2行的平均值

-- 每个分类内按价格占比
SELECT 
    name,
    category_id,
    price,
    SUM(price) OVER (PARTITION BY category_id) AS category_total,
    price / SUM(price) OVER (PARTITION BY category_id) * 100 AS percentage
FROM products;
```

**偏移函数：**

```sql
-- LAG：访问前N行的值
SELECT 
    order_date,
    daily_revenue,
    LAG(daily_revenue, 1) OVER (ORDER BY order_date) AS prev_day_revenue,
    daily_revenue - LAG(daily_revenue, 1) OVER (ORDER BY order_date) AS revenue_change
FROM (
    SELECT DATE(created_at) AS order_date, SUM(total_amount) AS daily_revenue
    FROM orders WHERE status = 'completed'
    GROUP BY DATE(created_at)
) daily
ORDER BY order_date;
/*
+------------+---------------+------------------+----------------+
| order_date | daily_revenue | prev_day_revenue | revenue_change |
+------------+---------------+------------------+----------------+
| 2024-01-01 |        500.00 |             NULL |           NULL |
| 2024-01-02 |        300.00 |           500.00 |        -200.00 |
| 2024-01-03 |        700.00 |           300.00 |         400.00 |
+------------+---------------+------------------+----------------+
*/

-- LEAD：访问后N行的值
SELECT 
    name,
    price,
    LEAD(price, 1) OVER (ORDER BY price DESC) AS next_price
FROM products;

-- LAG/LEAD 带默认值
SELECT 
    order_date,
    daily_revenue,
    LAG(daily_revenue, 1, 0) OVER (ORDER BY order_date) AS prev_revenue
FROM daily_stats;
-- 第一行的prev_revenue为0（而不是NULL）
```

**取值函数：**

```sql
-- FIRST_VALUE：分区内第一个值
SELECT 
    name,
    category_id,
    price,
    FIRST_VALUE(name) OVER (PARTITION BY category_id ORDER BY price DESC) AS most_expensive
FROM products;
-- 每行都显示该分类最贵的商品名

-- LAST_VALUE：分区内最后一个值（注意窗口范围）
SELECT 
    name,
    category_id,
    price,
    LAST_VALUE(name) OVER (
        PARTITION BY category_id 
        ORDER BY price 
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS cheapest
FROM products;
-- 注意：LAST_VALUE默认窗口是"从第一行到当前行"，需要显式指定范围
```

**NTILE 分桶：**

```sql
-- 将商品按价格分为4档（每档商品数大致相同）
SELECT 
    name,
    price,
    NTILE(4) OVER (ORDER BY price DESC) AS price_quartile
FROM products;
/*
+----------+--------+----------------+
| name     | price  | price_quartile |
+----------+--------+----------------+
| 笔记本   | 8999   |              1 |  ← 最高档
| 手机     | 5999   |              1 |
| 平板     | 2599   |              2 |
| 大衣     | 1999   |              2 |
| 裤子     |  599   |              3 |
| T恤      |  299   |              3 |
| 袜子     |   59   |              4 |  ← 最低档
| 鞋带     |   19   |              4 |
+----------+--------+----------------+
*/
```

### 总结

- 窗口函数为每行返回一个值，同时可以访问其他行，不会像聚合函数那样合并行
- `PARTITION BY` 分区（类似 GROUP BY 但不合并行），`ORDER BY` 区内排序
- ROW_NUMBER（行号不重复）、RANK（排名可重复，跳号）、DENSE_RANK（排名可重复，不跳号）
- 聚合函数 + OVER 可实现累计求和、移动平均等复杂计算
- LAG/LEAD 用于访问前/后行的值，常用于环比、同比分析
- `ROWS BETWEEN N PRECEDING AND M FOLLOWING` 定义窗口范围
- 窗口函数在 GROUP BY、HAVING 之后执行，可以引用聚合结果
- 窗口函数大幅简化了复杂分析查询，是现代 SQL 的重要特性

---

# 第5章：索引与优化

## 第18讲：索引基础

### 概念

索引（Index）是数据库中用于加速数据查询的数据结构。类似于书籍的目录，索引可以帮助数据库快速定位数据，而不必扫描整张表。索引是提升查询性能最重要的手段，合理的索引设计可以使查询速度提升几个数量级。但索引也会带来额外的存储开销和写操作成本，需要权衡使用。

### 原理

MariaDB（InnoDB 引擎）默认使用 **B+ 树（B+ Tree）** 作为索引数据结构。B+ 树的特点：

1. **多路平衡查找树**：每个节点可以有多个子节点，树的高度通常很低（3-4 层就能存储千万级数据）
2. **所有数据存储在叶子节点**：非叶子节点只存储索引键用于路由
3. **叶子节点形成有序链表**：支持范围查询和排序，只需遍历链表即可

B+ 树查询过程：
```
查找 id=30 的记录：
[根节点: 10 | 20 | 30 | 40]  →  30在[30,40)区间，走第3个子节点
        ↓
[中间节点: 30 | 35]  →  30在[30,35)区间，走第1个子节点
        ↓
[叶子节点: 30]  →  找到id=30的记录指针
```

索引的类型：
- **聚簇索引（Clustered Index）**：数据和主键索引存储在一起，一张表只有一个。InnoDB 的主键就是聚簇索引
- **二级索引（Secondary Index）**：非主键索引，叶子节点存储索引键和主键值。查询时可能需要"回表"（用主键再查聚簇索引获取完整数据）

索引的代价：
- **存储空间**：每个索引是一棵独立的 B+ 树，占用额外空间
- **写操作变慢**：INSERT/UPDATE/DELETE 需要同步维护索引结构
- **优化器选择成本**：索引过多会增加优化器选择执行计划的成本

### 例子

**创建索引：**

```sql
-- 建表时创建索引
CREATE TABLE products (
    id INT PRIMARY KEY,                    -- 主键自动创建聚簇索引
    name VARCHAR(100) NOT NULL,
    category_id INT,
    price DECIMAL(10,2),
    created_at DATETIME,
    
    -- 建表时创建索引
    INDEX idx_name (name),                 -- 普通索引
    INDEX idx_category (category_id),      -- 单列索引
    INDEX idx_created (created_at)         -- 单列索引
);

-- 建表后添加索引
CREATE INDEX idx_price ON products(price);
CREATE INDEX idx_category_price ON products(category_id, price);  -- 复合索引

-- 使用ALTER TABLE添加索引
ALTER TABLE products ADD INDEX idx_stock (stock);
ALTER TABLE products ADD UNIQUE INDEX uk_name (name);  -- 唯一索引
```

**查看索引：**

```sql
-- 查看表的索引
SHOW INDEX FROM products;
/*
+----------+------------+-------------+--------------+-------------+
| Table    | Non_unique | Key_name    | Column_name  | Seq_in_index|
+----------+------------+-------------+--------------+-------------+
| products |          0 | PRIMARY     | id           |           1 |
| products |          1 | idx_name    | name         |           1 |
| products |          1 | idx_category| category_id  |           1 |
| products |          1 | idx_price   | price        |           1 |
+----------+------------+-------------+--------------+-------------+
Non_unique: 0=唯一索引, 1=普通索引
Seq_in_index: 复合索引中列的顺序
*/
```

**删除索引：**

```sql
-- 删除索引
DROP INDEX idx_stock ON products;

-- 使用ALTER TABLE删除
ALTER TABLE products DROP INDEX idx_price;
```

**索引使用示例：**

```sql
-- 无索引时：全表扫描（ALL），扫描所有行
SELECT * FROM products WHERE name = '笔记本电脑';
-- 如果name列无索引，需要扫描整张表

-- 有索引时：索引查找（ref），快速定位
SELECT * FROM products WHERE name = '笔记本电脑';
-- 如果name列有索引，通过B+树快速定位

-- 范围查询利用索引
SELECT * FROM products WHERE price BETWEEN 100 AND 500;
-- idx_price支持范围查询（B+树叶子节点有序）

-- 排序利用索引
SELECT * FROM products ORDER BY created_at DESC LIMIT 10;
-- idx_created可以避免filesort（额外排序）
```

### 总结

- 索引是 B+ 树数据结构，用于加速查询，类似书籍目录
- 主键自动创建聚簇索引（数据和索引一体），其他索引是二级索引
- 二级索引查询可能需要"回表"（用主键查聚簇索引获取完整行）
- 索引加速查询但拖慢写入，索引越多写操作越慢
- 索引占用额外存储空间，需权衡数量
- 适合建索引的列：WHERE 条件列、JOIN 连接列、ORDER BY 排序列、GROUP BY 分组列
- 不适合建索引的列：区分度低的列（如性别）、频繁更新的列、数据量很小的表
- 索引不是越多越好，单表索引数量建议控制在 5-6 个以内

---

## 第19讲：索引类型与使用

### 概念

MariaDB 支持多种索引类型，包括普通索引、唯一索引、主键索引、复合索引、全文索引、前缀索引等。不同类型的索引适用于不同场景，正确选择索引类型是数据库优化的关键。本讲重点介绍各种索引的特点和使用方法。

### 原理

**索引类型详解：**

1. **主键索引（PRIMARY KEY）**
   - 唯一且非空
   - InnoDB 中就是聚簇索引，数据按主键顺序存储
   - 一张表只能有一个主键

2. **唯一索引（UNIQUE）**
   - 索引列值必须唯一（允许 NULL，且多个 NULL 不冲突）
   - 用于保证数据唯一性，同时加速查询

3. **普通索引（INDEX/KEY）**
   - 最基本的索引，无约束
   - 仅用于加速查询

4. **复合索引（Composite Index）**
   - 多列组合的索引
   - 遵循"最左前缀原则"：查询必须从索引最左列开始使用
   - 列顺序很重要，区分度高的列放前面

5. **前缀索引（Prefix Index）**
   - 对字符串列的前 N 个字符建索引
   - 节省空间，但无法用于 ORDER BY 和 GROUP BY

6. **全文索引（FULLTEXT）**
   - 用于全文搜索，支持中文分词
   - 适合大文本字段的模糊搜索

**最左前缀原则：**
复合索引 `(a, b, c)` 相当于创建了 `(a)`、`(a, b)`、`(a, b, c)` 三个索引，但不是 `(b)`、`(c)`、`(b, c)`。

### 例子

**复合索引与最左前缀：**

```sql
-- 创建复合索引
CREATE INDEX idx_cat_price_stock ON products(category_id, price, stock);

-- 能使用索引的查询：
SELECT * FROM products WHERE category_id = 1;                    -- ✓ 使用(a)
SELECT * FROM products WHERE category_id = 1 AND price = 100;    -- ✓ 使用(a,b)
SELECT * FROM products WHERE category_id = 1 AND price = 100 AND stock > 0; -- ✓ 使用(a,b,c)
SELECT * FROM products WHERE category_id = 1 AND stock > 0;      -- ✓ 只使用(a)，stock跳过了price

-- 不能使用索引的查询：
SELECT * FROM products WHERE price = 100;          -- ✗ 缺少最左列category_id
SELECT * FROM products WHERE stock > 0;            -- ✗ 缺少最左列category_id
SELECT * FROM products WHERE price = 100 AND stock > 0;  -- ✗ 缺少最左列

-- 范围查询会中断后续列的索引使用
SELECT * FROM products WHERE category_id = 1 AND price > 100 AND stock > 0;
-- 只使用(category_id, price)，stock无法使用索引（因为price是范围查询）
```

**唯一索引：**

```sql
-- 创建唯一索引
CREATE UNIQUE INDEX uk_email ON users(email);
ALTER TABLE users ADD UNIQUE INDEX uk_username (username);

-- 建表时定义
CREATE TABLE users (
    id INT PRIMARY KEY,
    username VARCHAR(50) UNIQUE,    -- 自动创建唯一索引
    email VARCHAR(100),
    UNIQUE KEY uk_email (email)     -- 显式命名
);

-- 唯一索引保证数据唯一性
INSERT INTO users (username, email) VALUES ('user1', 'a@b.com');
INSERT INTO users (username, email) VALUES ('user2', 'a@b.com');
-- 报错：Duplicate entry 'a@b.com' for key 'uk_email'

-- 唯一索引允许多个NULL
INSERT INTO users (username, email) VALUES ('user3', NULL);
INSERT INTO users (username, email) VALUES ('user4', NULL);
-- 成功（NULL不参与唯一性检查）
```

**前缀索引：**

```sql
-- 对长字符串列建前缀索引，节省空间
CREATE INDEX idx_description ON products(description(20));
-- 只索引description前20个字符

-- 选择合适的前缀长度
SELECT 
    COUNT(DISTINCT LEFT(description, 10)) / COUNT(*) AS sel_10,
    COUNT(DISTINCT LEFT(description, 20)) / COUNT(*) AS sel_20,
    COUNT(DISTINCT LEFT(description, 30)) / COUNT(*) AS sel_30
FROM products;
-- 选择性接近完整列选择性的最小前缀长度
```

**覆盖索引（Covering Index）：**

```sql
-- 覆盖索引：查询的列全部包含在索引中，无需回表
CREATE INDEX idx_covering ON products(category_id, price, name);

-- 这个查询可以使用覆盖索引
SELECT category_id, price, name FROM products WHERE category_id = 1;
-- 因为所有查询列都在索引中，不需要回表查聚簇索引，性能更好

-- EXPLAIN中会显示 Using index
EXPLAIN SELECT category_id, price, name FROM products WHERE category_id = 1;
-- Extra: Using index  ← 表示使用了覆盖索引
```

**函数索引（MariaDB 10.5+）：**

```sql
-- 对函数结果建索引（解决WHERE中使用函数导致索引失效的问题）
CREATE INDEX idx_created_date ON orders((DATE(created_at)));

-- 现在以下查询可以使用索引
SELECT * FROM orders WHERE DATE(created_at) = '2024-01-15';
```

### 总结

- 复合索引遵循"最左前缀原则"，查询必须从最左列开始才能使用索引
- 复合索引列顺序：等值查询列在前，范围查询列在后，区分度高的列在前
- 唯一索引既保证数据唯一性，又加速查询，适合用户名、邮箱等唯一字段
- 前缀索引节省空间，但无法用于 ORDER BY 和 GROUP BY
- 覆盖索引（查询列都在索引中）可以避免回表，大幅提升性能
- WHERE 中对列使用函数会导致索引失效，可用函数索引（MariaDB 10.5+）解决
- 避免冗余索引：如已有 `(a, b)` 索引，单独建 `(a)` 索引就是冗余
- 定期使用 `ANALYZE TABLE` 更新索引统计信息，帮助优化器做出正确决策

---

## 第20讲：EXPLAIN 执行计划

### 概念

EXPLAIN 是 MariaDB 提供的查询分析工具，用于查看 SQL 语句的执行计划。通过 EXPLAIN，可以了解 MariaDB 如何执行查询：使用哪些索引、表的连接顺序、扫描了多少行等。EXPLAIN 是 SQL 性能优化的核心工具，掌握它对于排查慢查询至关重要。

### 原理

EXPLAIN 的工作原理是让优化器生成查询执行计划但不实际执行查询。执行计划包含以下关键字段：

| 字段 | 说明 |
|------|------|
| id | 查询序号，相同表示同一组，从大到小执行 |
| select_type | 查询类型（SIMPLE/PRIMARY/SUBQUERY等） |
| table | 表名 |
| type | 访问类型（重要！反映索引使用情况） |
| possible_keys | 可能使用的索引 |
| key | 实际使用的索引 |
| key_len | 使用的索引长度（判断复合索引用了几个列） |
| ref | 索引比较的列或常量 |
| rows | 预估扫描行数（越小越好） |
| Extra | 额外信息（Using index/filesort/temporary等） |

**type 字段的性能排序（从好到差）：**
```
system > const > eq_ref > ref > range > index > ALL
```
- `system`/`const`：主键或唯一索引等值查询，最快
- `eq_ref`：JOIN 时被驱动表使用主键或唯一索引，最多一条匹配
- `ref`：普通索引等值查询
- `range`：索引范围查询（BETWEEN, >, <, IN）
- `index`：扫描整个索引树（比全表扫描好）
- `ALL`：全表扫描，最慢，通常需要优化

### 例子

**基本 EXPLAIN：**

```sql
-- 查看简单查询的执行计划
EXPLAIN SELECT * FROM products WHERE id = 1;
/*
+----+-------------+----------+------------+-------+---------------+---------+
| id | select_type | table    | type       | key   | rows | Extra  |
+----+-------------+----------+------------+-------+---------------+---------+
|  1 | SIMPLE      | products | const      | PRIMARY|    1 |        |
+----+-------------+----------+------------+-------+---------------+---------+
type=const：主键等值查询，性能最好
*/

-- 无索引的查询
EXPLAIN SELECT * FROM products WHERE description = '测试';
/*
+----+-------------+----------+------------+------+---------------+------+
| id | select_type | table    | type       | key  | rows | Extra  |
+----+-------------+----------+------------+------+---------------+------+
|  1 | SIMPLE      | products | ALL        | NULL | 1000 | Using where |
+----+-------------+----------+------------+------+---------------+------+
type=ALL：全表扫描，扫描1000行，需要优化
key=NULL：没有使用索引
*/
```

**EXPLAIN 分析 JOIN：**

```sql
EXPLAIN 
SELECT o.order_no, u.username, p.name
FROM orders o
INNER JOIN users u ON o.user_id = u.id
INNER JOIN order_items oi ON o.id = oi.order_id
INNER JOIN products p ON oi.product_id = p.id
WHERE o.status = 'completed';
/*
+----+-------------+-------+--------+---------------+-----------+------+
| id | select_type | table | type   | key           | rows      | Extra|
+----+-------------+-------+--------+---------------+-----------+------+
|  1 | SIMPLE      | o     | ref    | idx_status    |       500 |      |
|  1 | SIMPLE      | u     | eq_ref | PRIMARY       |         1 |      |
|  1 | SIMPLE      | oi    | ref    | idx_order_id  |         3 |      |
|  1 | SIMPLE      | p     | eq_ref | PRIMARY       |         1 |      |
+----+-------------+-------+--------+---------------+-----------+------+
分析：
- orders表使用idx_status索引，扫描500行
- users表使用主键，每次精确匹配1行（eq_ref）
- order_items使用idx_order_id，每个订单约3条明细
- products表使用主键，精确匹配1行
*/
```

**EXPLAIN FORMAT=JSON（详细输出）：**

```sql
-- JSON格式提供更详细的信息
EXPLAIN FORMAT=JSON
SELECT * FROM products WHERE category_id = 1 AND price > 100;
/*
{
  "query_block": {
    "select_id": 1,
    "table": {
      "table_name": "products",
      "access_type": "range",
      "possible_keys": ["idx_cat_price"],
      "key": "idx_cat_price",
      "key_length": "10",
      "used_key_parts": ["category_id", "price"],
      "rows": 50,
      "filtered": 100,
      "attached_condition": "price > 100"
    }
  }
}
*/
```

**分析慢查询并优化：**

```sql
-- 优化前：全表扫描
EXPLAIN SELECT * FROM orders WHERE DATE(created_at) = '2024-01-15';
-- type=ALL, key=NULL, rows=10000  ← 索引失效

-- 优化后：改写为范围查询
EXPLAIN SELECT * FROM orders 
WHERE created_at >= '2024-01-15 00:00:00' 
  AND created_at < '2024-01-16 00:00:00';
-- type=range, key=idx_created, rows=50  ← 使用索引

-- 优化前：filesort
EXPLAIN SELECT * FROM products WHERE category_id = 1 ORDER BY price;
-- Extra: Using where; Using filesort  ← 额外排序

-- 优化后：建立复合索引
CREATE INDEX idx_cat_price ON products(category_id, price);
EXPLAIN SELECT * FROM products WHERE category_id = 1 ORDER BY price;
-- Extra: Using index condition  ← 无filesort，索引有序
```

### 总结

- EXPLAIN 是 SQL 优化的核心工具，每次优化都应先用 EXPLAIN 分析
- `type` 字段反映访问类型，从 `const` 到 `ALL` 性能递减，应避免 `ALL`（全表扫描）
- `key` 为 NULL 表示未使用索引，`possible_keys` 显示可选索引
- `rows` 是预估扫描行数，越小越好
- `Extra` 中的 `Using index` 表示覆盖索引（好），`Using filesort` 表示额外排序（需优化），`Using temporary` 表示临时表（需优化）
- WHERE 中对列使用函数会导致索引失效，应改写为范围查询
- ORDER BY 列如果在索引中，可以避免 filesort
- 复合索引的 `key_len` 可以判断使用了几个列（每个列占用的字节数）

---

## 第21讲：查询优化技巧

### 概念

查询优化是通过改写 SQL 和调整索引设计，使查询执行更高效的过程。即使有了索引，不合理的 SQL 写法仍可能导致性能问题。本讲总结常见的查询优化技巧和最佳实践，帮助编写高效的 SQL。

### 原理

查询优化的核心原则：

1. **减少数据扫描量**：通过索引和 WHERE 条件减少扫描行数
2. **减少数据传输量**：只查询需要的列，避免 SELECT *
3. **利用索引有序性**：让 ORDER BY、GROUP BY 能使用索引
4. **避免索引失效**：不在索引列上使用函数、运算、类型转换
5. **减少临时表和排序**：合理设计索引避免 filesort 和 temporary

优化器的工作方式：
- 优化器会基于成本选择执行计划（选择哪个索引、表连接顺序等）
- 优化器依赖统计信息（行数、基数等），统计信息不准确会导致错误选择
- 某些复杂查询优化器可能无法自动优化，需要手动改写

### 例子

**避免 SELECT *：**

```sql
-- 差：查询所有列，包括不需要的大字段
SELECT * FROM products WHERE category_id = 1;

-- 好：只查询需要的列，可能使用覆盖索引
SELECT id, name, price FROM products WHERE category_id = 1;
-- 如果有索引(category_id, name, price)，可以使用覆盖索引，无需回表
```

**避免索引失效：**

```sql
-- 1. 避免在索引列上使用函数
-- 差：
SELECT * FROM orders WHERE YEAR(created_at) = 2024;
SELECT * FROM users WHERE LEFT(username, 3) = 'adm';
-- 好：
SELECT * FROM orders WHERE created_at >= '2024-01-01' AND created_at < '2025-01-01';
SELECT * FROM users WHERE username LIKE 'adm%';

-- 2. 避免在索引列上做运算
-- 差：
SELECT * FROM products WHERE price * 0.85 > 1000;
-- 好：
SELECT * FROM products WHERE price > 1000 / 0.85;

-- 3. 避免隐式类型转换
-- 差（category_id是INT，传入字符串导致隐式转换）：
SELECT * FROM products WHERE category_id = '1';
-- 好：
SELECT * FROM products WHERE category_id = 1;

-- 4. 避免使用 != 或 <> （可能导致全表扫描）
-- 差：
SELECT * FROM products WHERE category_id != 1;
-- 好（如果分类少）：
SELECT * FROM products WHERE category_id IN (2, 3, 4, 5);

-- 5. LIKE 左侧通配符导致索引失效
-- 差（前导%导致全表扫描）：
SELECT * FROM products WHERE name LIKE '%电脑';
-- 好（后置%可以使用索引）：
SELECT * FROM products WHERE name LIKE '电脑%';
```

**优化分页查询：**

```sql
-- 差：深度分页，OFFSET越大越慢
SELECT * FROM orders ORDER BY created_at DESC LIMIT 100000, 10;
-- 需要扫描100010行，丢弃前100000行

-- 好：使用游标分页（记住上一页的最大ID）
SELECT * FROM orders 
WHERE id < 上一页最后一条记录的id
ORDER BY id DESC 
LIMIT 10;

-- 好：使用延迟关联（先查ID再关联）
SELECT o.* FROM orders o
INNER JOIN (
    SELECT id FROM orders ORDER BY created_at DESC LIMIT 100000, 10
) t ON o.id = t.id;
-- 子查询只查ID，可以使用覆盖索引，速度快
```

**优化 JOIN：**

```sql
-- 确保JOIN列有索引
-- 差：user_id列无索引
SELECT * FROM orders o INNER JOIN users u ON o.user_id = u.id;
-- 好：为user_id建索引
CREATE INDEX idx_user_id ON orders(user_id);

-- 小表驱动大表
-- 差：大表驱动小表
SELECT * FROM big_table b INNER JOIN small_table s ON b.id = s.bid;
-- 好：小表驱动大表（优化器通常会自动选择，但有时需要手动）
SELECT * FROM small_table s INNER JOIN big_table b ON b.id = s.bid;
```

**优化 COUNT：**

```sql
-- 差：COUNT(*)在大表上很慢（InnoDB需要扫描）
SELECT COUNT(*) FROM orders WHERE status = 'completed';

-- 好：如果不需要精确值，使用估算
SELECT table_rows FROM information_schema.tables 
WHERE table_name = 'orders';

-- 好：维护计数器表
CREATE TABLE order_stats (
    status VARCHAR(20) PRIMARY KEY,
    count INT
);
-- 每次订单状态变化时更新计数器
```

**使用 FORCE INDEX 强制索引：**

```sql
-- 当优化器选择错误索引时，可以强制使用指定索引
SELECT * FROM orders FORCE INDEX (idx_created)
WHERE created_at >= '2024-01-01' AND user_id = 1;
-- 注意：通常应先分析为什么优化器选错，可能是统计信息过期
-- 更新统计信息：ANALYZE TABLE orders;
```

**批量操作优化：**

```sql
-- 差：循环单条插入
-- INSERT INTO logs (msg) VALUES ('msg1');
-- INSERT INTO logs (msg) VALUES ('msg2');
-- ...

-- 好：批量插入
INSERT INTO logs (msg) VALUES ('msg1'), ('msg2'), ('msg3'), ...;

-- 差：循环单条更新
-- UPDATE products SET stock = stock - 1 WHERE id = 1;
-- UPDATE products SET stock = stock - 1 WHERE id = 2;

-- 好：使用CASE WHEN批量更新
UPDATE products SET stock = CASE id
    WHEN 1 THEN stock - 1
    WHEN 2 THEN stock - 1
    WHEN 3 THEN stock - 2
END
WHERE id IN (1, 2, 3);
```

### 总结

- 避免 SELECT *，只查询需要的列，可能利用覆盖索引
- 不在索引列上使用函数、运算、类型转换，否则索引失效
- LIKE 查询避免前导通配符（`%xxx`），使用 `xxx%` 才能走索引
- 深度分页（OFFSET 大）使用游标分页或延迟关联优化
- JOIN 列必须有索引，小表驱动大表
- 大表 COUNT 考虑使用估算值或维护计数器表
- 批量操作远比循环单条操作高效
- 优化器选错索引时，先 `ANALYZE TABLE` 更新统计信息，必要时用 `FORCE INDEX`
- 优化是一个迭代过程：EXPLAIN 分析 → 改写 SQL/调整索引 → 再 EXPLAIN 验证

---

# 第6章：存储过程与触发器

## 第22讲：存储过程

### 概念

存储过程（Stored Procedure）是一组预编译并存储在数据库中的 SQL 语句集合。存储过程可以接受输入参数、返回多个结果集、包含流程控制逻辑，类似于编程语言中的函数。存储过程在服务器端执行，减少了网络传输开销，适合封装复杂业务逻辑。

### 原理

存储过程的工作原理：

1. **创建时**：SQL 语句被解析、编译并存储在数据库中
2. **调用时**：直接执行已编译的代码，无需重新解析和编译
3. **执行时**：在数据库服务器端运行，减少客户端与服务器之间的数据传输

存储过程的优点：
- **性能好**：预编译，减少解析和编译开销；服务器端执行减少网络传输
- **复用性强**：封装复杂逻辑，多个应用可以调用同一存储过程
- **安全性好**：可以限制用户直接访问表，只允许调用存储过程
- **维护方便**：业务逻辑变更只需修改存储过程，不需要修改应用程序

存储过程的缺点：
- **调试困难**：数据库端的调试工具不如编程语言完善
- **可移植性差**：不同数据库的存储过程语法不同
- **增加数据库负载**：复杂逻辑在数据库端执行，可能影响数据库性能
- **版本控制不便**：存储过程代码难以纳入 Git 等版本控制系统

### 例子

**基本存储过程：**

```sql
-- 修改分隔符（因为存储过程内部使用分号）
DELIMITER //

-- 创建存储过程
CREATE PROCEDURE GetProductCount()
BEGIN
    SELECT COUNT(*) AS total FROM products;
END //

-- 恢复分隔符
DELIMITER ;

-- 调用存储过程
CALL GetProductCount();
```

**带参数的存储过程：**

```sql
DELIMITER //

-- IN参数：输入
CREATE PROCEDURE GetProductsByCategory(
    IN cat_id INT
)
BEGIN
    SELECT id, name, price, stock 
    FROM products 
    WHERE category_id = cat_id
    ORDER BY price DESC;
END //

DELIMITER ;

-- 调用
CALL GetProductsByCategory(1);

-- IN和OUT参数
DELIMITER //
CREATE PROCEDURE GetCategoryStats(
    IN cat_id INT,
    OUT product_count INT,
    OUT avg_price DECIMAL(10,2),
    OUT total_stock INT
)
BEGIN
    SELECT COUNT(*), AVG(price), SUM(stock)
    INTO product_count, avg_price, total_stock
    FROM products
    WHERE category_id = cat_id;
END //
DELIMITER ;

-- 调用并获取OUT参数
CALL GetCategoryStats(1, @count, @avg, @stock);
SELECT @count AS product_count, @avg AS avg_price, @stock AS total_stock;
```

**带流程控制的存储过程：**

```sql
DELIMITER //
CREATE PROCEDURE UpdateProductPrice(
    IN p_id INT,
    IN discount_rate DECIMAL(3,2)
)
BEGIN
    DECLARE current_price DECIMAL(10,2);
    DECLARE new_price DECIMAL(10,2);
    
    -- 获取当前价格
    SELECT price INTO current_price FROM products WHERE id = p_id;
    
    -- 条件判断
    IF current_price IS NULL THEN
        SELECT '商品不存在' AS message;
    ELSEIF discount_rate < 0 OR discount_rate > 1 THEN
        SELECT '折扣率必须在0-1之间' AS message;
    ELSE
        -- 计算新价格
        SET new_price = ROUND(current_price * discount_rate, 2);
        
        -- 更新价格
        UPDATE products SET price = new_price WHERE id = p_id;
        
        -- 返回结果
        SELECT p_id AS product_id, current_price AS old_price, 
               new_price AS new_price, '更新成功' AS message;
    END IF;
END //
DELIMITER ;

-- 调用
CALL UpdateProductPrice(1, 0.85);
```

**电商下单存储过程示例：**

```sql
DELIMITER //
CREATE PROCEDURE PlaceOrder(
    IN p_user_id INT,
    IN p_items JSON,  -- [{"product_id": 1, "quantity": 2}, {"product_id": 3, "quantity": 1}]
    OUT p_order_id INT,
    OUT p_message VARCHAR(100)
)
BEGIN
    DECLARE v_total DECIMAL(10,2) DEFAULT 0;
    DECLARE v_stock INT;
    DECLARE v_price DECIMAL(10,2);
    DECLARE v_product_id INT;
    DECLARE v_quantity INT;
    DECLARE v_i INT DEFAULT 0;
    DECLARE v_count INT;
    
    -- 开启事务
    START TRANSACTION;
    
    -- 解析JSON数组长度
    SET v_count = JSON_LENGTH(p_items);
    
    -- 检查库存
    WHILE v_i < v_count DO
        SET v_product_id = JSON_UNQUOTE(JSON_EXTRACT(p_items, CONCAT('$[', v_i, '].product_id')));
        SET v_quantity = JSON_EXTRACT(p_items, CONCAT('$[', v_i, '].quantity'));
        
        SELECT stock, price INTO v_stock, v_price 
        FROM products WHERE id = v_product_id FOR UPDATE;
        
        IF v_stock < v_quantity THEN
            ROLLBACK;
            SET p_message = CONCAT('商品ID=', v_product_id, '库存不足');
            SET p_order_id = 0;
            LEAVE label;
        END IF;
        
        SET v_total = v_total + v_price * v_quantity;
        SET v_i = v_i + 1;
    END WHILE;
    
    -- 创建订单
    INSERT INTO orders (user_id, total_amount, status, created_at)
    VALUES (p_user_id, v_total, 'pending', NOW());
    SET p_order_id = LAST_INSERT_ID();
    
    -- 创建订单详情并扣减库存
    SET v_i = 0;
    WHILE v_i < v_count DO
        SET v_product_id = JSON_UNQUOTE(JSON_EXTRACT(p_items, CONCAT('$[', v_i, '].product_id')));
        SET v_quantity = JSON_EXTRACT(p_items, CONCAT('$[', v_i, '].quantity'));
        
        SELECT price INTO v_price FROM products WHERE id = v_product_id;
        
        INSERT INTO order_items (order_id, product_id, price, quantity, subtotal)
        VALUES (p_order_id, v_product_id, v_price, v_quantity, v_price * v_quantity);
        
        UPDATE products SET stock = stock - v_quantity WHERE id = v_product_id;
        
        SET v_i = v_i + 1;
    END WHILE;
    
    COMMIT;
    SET p_message = '下单成功';
END //
DELIMITER ;

-- 调用
CALL PlaceOrder(1, '[{"product_id": 1, "quantity": 2}, {"product_id": 3, "quantity": 1}]', 
                @order_id, @message);
SELECT @order_id, @message;
```

**管理存储过程：**

```sql
-- 查看存储过程列表
SHOW PROCEDURE STATUS WHERE Db = 'mydb';

-- 查看存储过程定义
SHOW CREATE PROCEDURE GetProductsByCategory\G

-- 删除存储过程
DROP PROCEDURE IF EXISTS GetProductCount;
```

### 总结

- 存储过程是预编译的 SQL 集合，性能好、复用性强、安全性高
- 参数类型：IN（输入）、OUT（输出）、INOUT（输入输出）
- 存储过程内可使用流程控制：IF/CASE、WHILE/REPEAT/LOOP、DECLARE 变量
- 复杂业务逻辑（如电商下单）适合用存储过程封装，保证原子性
- 存储过程在事务中执行，可以确保多步操作的一致性
- 缺点：调试困难、可移植性差、增加数据库负载
- 生产环境使用存储过程需权衡：简单逻辑放应用层，复杂的数据一致性逻辑可用存储过程
- 使用 `DELIMITER` 修改语句分隔符，避免存储过程内部的分号冲突

---

## 第23讲：存储函数

### 概念

存储函数（Stored Function）是用户自定义的函数，接收参数并返回一个值。与存储过程不同，存储函数必须返回一个值，且可以在 SQL 语句中直接使用（像内置函数一样）。存储函数适合封装计算逻辑，使 SQL 更简洁。

### 原理

存储函数与存储过程的区别：

| 特性 | 存储过程 | 存储函数 |
|------|----------|----------|
| 返回值 | 可有可无，可返回多个结果集 | 必须返回一个值 |
| 调用方式 | CALL 语句调用 | 在 SQL 语句中直接使用 |
| 参数类型 | IN/OUT/INOUT | 只有 IN（隐式） |
| 使用场景 | 复杂业务逻辑 | 计算和转换 |
| 能否在SELECT中使用 | 不能 | 能 |

存储函数的限制：
- 不能返回结果集（不能包含 SELECT 返回多行）
- 不能使用事务（不能 COMMIT/ROLLBACK）
- 不能使用某些 SQL 语句（如 INSERT/UPDATE/DELETE，除非声明 DETERMINISTIC 或修改 log_bin_trust_function_creators）

### 例子

**基本存储函数：**

```sql
DELIMITER //

-- 计算商品折扣价
CREATE FUNCTION CalculateDiscountPrice(
    p_price DECIMAL(10,2),
    p_discount DECIMAL(3,2)
) RETURNS DECIMAL(10,2)
DETERMINISTIC
READS SQL DATA
BEGIN
    DECLARE result DECIMAL(10,2);
    SET result = ROUND(p_price * p_discount, 2);
    RETURN result;
END //

DELIMITER ;

-- 在SQL中使用
SELECT 
    name,
    price,
    CalculateDiscountPrice(price, 0.85) AS discount_price
FROM products LIMIT 5;
```

**读取数据的存储函数：**

```sql
DELIMITER //

-- 获取用户订单总数
CREATE FUNCTION GetUserOrderCount(p_user_id INT) 
RETURNS INT
READS SQL DATA
BEGIN
    DECLARE v_count INT;
    SELECT COUNT(*) INTO v_count FROM orders WHERE user_id = p_user_id;
    RETURN v_count;
END //

DELIMITER ;

-- 使用
SELECT 
    id,
    username,
    GetUserOrderCount(id) AS order_count
FROM users;
```

**复杂计算函数：**

```sql
DELIMITER //

-- 计算运费（基于重量和距离）
CREATE FUNCTION CalculateShipping(
    p_weight DECIMAL(10,2),
    p_distance INT
) RETURNS DECIMAL(10,2)
DETERMINISTIC
BEGIN
    DECLARE base_fee DECIMAL(10,2) DEFAULT 10.00;
    DECLARE weight_fee DECIMAL(10,2);
    DECLARE distance_fee DECIMAL(10,2);
    DECLARE total DECIMAL(10,2);
    
    -- 重量费用：每公斤1元，首公斤免费
    IF p_weight <= 1 THEN
        SET weight_fee = 0;
    ELSE
        SET weight_fee = (p_weight - 1) * 1.00;
    END IF;
    
    -- 距离费用：每100公里2元
    SET distance_fee = (p_distance / 100) * 2.00;
    
    -- 总运费
    SET total = base_fee + weight_fee + distance_fee;
    
    -- 超过50元免运费
    IF total > 50 THEN
        SET total = 0;
    END IF;
    
    RETURN ROUND(total, 2);
END //

DELIMITER ;

-- 使用
SELECT 
    order_no,
    total_amount,
    CalculateShipping(2.5, 300) AS shipping_fee
FROM orders LIMIT 5;
```

**管理存储函数：**

```sql
-- 查看函数列表
SHOW FUNCTION STATUS WHERE Db = 'mydb';

-- 查看函数定义
SHOW CREATE FUNCTION CalculateDiscountPrice\G

-- 删除函数
DROP FUNCTION IF EXISTS CalculateDiscountPrice;
```

### 总结

- 存储函数必须返回一个值，可在 SQL 语句中像内置函数一样使用
- 存储函数只有 IN 参数（隐式），不能有 OUT/INOUT
- `DETERMINISTIC` 声明函数对相同输入总是返回相同结果（优化器可缓存结果）
- `READS SQL DATA` 声明函数会读取数据（但不修改）
- 存储函数不能返回结果集，不能使用事务控制语句
- 存储函数适合封装计算逻辑，使 SQL 更简洁可读
- 过度使用存储函数可能导致每行都调用函数，影响性能
- 存储函数与存储过程的选择：需要返回值并在 SQL 中使用选函数，否则选存储过程

---

## 第24讲：触发器

### 概念

触发器（Trigger）是一种特殊的存储过程，它在特定事件（INSERT/UPDATE/DELETE）发生时自动执行。触发器不需要手动调用，而是由数据库在数据变更时自动触发。触发器常用于数据审计、级联更新、数据验证等场景。

### 原理

触发器的工作原理：

1. **触发时机**：BEFORE（操作前执行）或 AFTER（操作后执行）
2. **触发事件**：INSERT、UPDATE、DELETE
3. **触发对象**：表级别，每张表最多可定义 6 个触发器（BEFORE/AFTER × INSERT/UPDATE/DELETE）

触发器的执行流程（以 BEFORE INSERT 为例）：
```
INSERT 语句 → BEFORE INSERT 触发器执行 → 实际 INSERT 执行 → AFTER INSERT 触发器执行
```

触发器中的特殊变量：
- `NEW`：INSERT/UPDATE 时的新数据（可修改）
- `OLD`：UPDATE/DELETE 时的旧数据（只读）

触发器的应用场景：
- 数据审计：记录数据变更历史
- 级联操作：更新关联表数据
- 数据验证：BEFORE 触发器中验证数据有效性
- 自动填充：自动生成计算列、时间戳等

### 例子

**审计日志触发器：**

```sql
-- 创建审计表
CREATE TABLE products_audit (
    id INT AUTO_INCREMENT PRIMARY KEY,
    product_id INT,
    action VARCHAR(10),      -- INSERT/UPDATE/DELETE
    old_data JSON,
    new_data JSON,
    operator VARCHAR(50),
    created_at DATETIME
);

-- AFTER INSERT 触发器：记录新增
DELIMITER //
CREATE TRIGGER trg_product_after_insert
AFTER INSERT ON products
FOR EACH ROW
BEGIN
    INSERT INTO products_audit (product_id, action, new_data, operator, created_at)
    VALUES (
        NEW.id, 
        'INSERT',
        JSON_OBJECT('name', NEW.name, 'price', NEW.price, 'stock', NEW.stock),
        CURRENT_USER(),
        NOW()
    );
END //

-- AFTER UPDATE 触发器：记录修改
DELIMITER //
CREATE TRIGGER trg_product_after_update
AFTER UPDATE ON products
FOR EACH ROW
BEGIN
    INSERT INTO products_audit (product_id, action, old_data, new_data, operator, created_at)
    VALUES (
        NEW.id,
        'UPDATE',
        JSON_OBJECT('name', OLD.name, 'price', OLD.price, 'stock', OLD.stock),
        JSON_OBJECT('name', NEW.name, 'price', NEW.price, 'stock', NEW.stock),
        CURRENT_USER(),
        NOW()
    );
END //

-- AFTER DELETE 触发器：记录删除
DELIMITER //
CREATE TRIGGER trg_product_after_delete
AFTER DELETE ON products
FOR EACH ROW
BEGIN
    INSERT INTO products_audit (product_id, action, old_data, operator, created_at)
    VALUES (
        OLD.id,
        'DELETE',
        JSON_OBJECT('name', OLD.name, 'price', OLD.price, 'stock', OLD.stock),
        CURRENT_USER(),
        NOW()
    );
END //
DELIMITER ;

-- 测试
INSERT INTO products (name, price, stock, category_id) VALUES ('测试商品', 100, 50, 1);
UPDATE products SET price = 120 WHERE name = '测试商品';
DELETE FROM products WHERE name = '测试商品';

-- 查看审计日志
SELECT * FROM products_audit ORDER BY id DESC;
```

**BEFORE 触发器数据验证：**

```sql
-- BEFORE INSERT：验证数据
DELIMITER //
CREATE TRIGGER trg_product_before_insert
BEFORE INSERT ON products
FOR EACH ROW
BEGIN
    -- 价格不能为负
    IF NEW.price < 0 THEN
        SIGNAL SQLSTATE '45000' 
        SET MESSAGE_TEXT = '商品价格不能为负数';
    END IF;
    
    -- 库存不能为负
    IF NEW.stock < 0 THEN
        SIGNAL SQLSTATE '45000' 
        SET MESSAGE_TEXT = '商品库存不能为负数';
    END IF;
    
    -- 自动设置创建时间
    SET NEW.created_at = NOW();
END //
DELIMITER ;

-- 测试
INSERT INTO products (name, price, stock) VALUES ('测试', -10, 5);
-- 报错：商品价格不能为负数
```

**自动更新关联数据：**

```sql
-- 订单状态变更时，更新用户统计
DELIMITER //
CREATE TRIGGER trg_order_after_update
AFTER UPDATE ON orders
FOR EACH ROW
BEGIN
    -- 订单从pending变为completed时，更新用户消费总额
    IF OLD.status = 'pending' AND NEW.status = 'completed' THEN
        UPDATE users 
        SET total_spent = total_spent + NEW.total_amount,
            order_count = order_count + 1
        WHERE id = NEW.user_id;
    END IF;
    
    -- 订单从completed变为cancelled时，回退用户统计
    IF OLD.status = 'completed' AND NEW.status = 'cancelled' THEN
        UPDATE users 
        SET total_spent = total_spent - OLD.total_amount,
            order_count = order_count - 1
        WHERE id = OLD.user_id;
    END IF;
END //
DELIMITER ;
```

**管理触发器：**

```sql
-- 查看触发器
SHOW TRIGGERS;

-- 查看特定表的触发器
SHOW TRIGGERS WHERE Table = 'products';

-- 查看触发器定义
SHOW CREATE TRIGGER trg_product_after_insert\G

-- 删除触发器
DROP TRIGGER IF EXISTS trg_product_after_insert;
```

### 总结

- 触发器在 INSERT/UPDATE/DELETE 时自动执行，无需手动调用
- BEFORE 触发器用于数据验证和修改 NEW 值；AFTER 触发器用于记录日志和级联操作
- `NEW` 表示新数据（INSERT/UPDATE），`OLD` 表示旧数据（UPDATE/DELETE）
- `SIGNAL SQLSTATE '45000'` 用于在触发器中抛出错误，阻止操作
- 触发器常用于审计日志、数据验证、级联更新、自动填充
- 触发器是隐式执行的，可能造成"神秘"的副作用，调试困难
- 触发器会增加写操作开销，大量触发器可能影响性能
- 同一张表同一事件只能有一个触发器（MariaDB 10.2+ 支持多个，用 FOLLOWS/PRECEDES 指定顺序）
- 使用触发器要谨慎，避免过度使用导致逻辑难以追踪

---

## 第25讲：游标与流程控制

### 概念

游标（Cursor）是数据库中用于逐行处理查询结果集的机制。与普通 SELECT 一次性返回所有结果不同，游标允许程序逐行获取数据，适合需要逐行处理的场景。流程控制语句（IF/CASE/WHILE/REPEAT/LOOP）是存储过程中控制执行逻辑的语法，使 SQL 具备编程能力。

### 原理

游标的工作原理：

1. **声明游标**：定义游标关联的 SELECT 语句
2. **打开游标**：执行 SELECT 语句，结果集缓存在内存中
3. **获取数据**：FETCH 逐行获取结果，赋值给变量
4. **关闭游标**：释放结果集资源

游标只能在存储过程/函数/触发器中使用，且是只读的（不能通过游标更新数据）。

游标的限制：
- 游标只能在 BEGIN...END 块中声明
- 游标声明必须在变量声明之后，处理程序声明之前
- 每个游标需要对应的 CONTINUE HANDLER 处理结束状态

流程控制语句：
- `IF...THEN...ELSEIF...ELSE...END IF`：条件分支
- `CASE...WHEN...THEN...ELSE...END CASE`：多分支选择
- `WHILE...DO...END WHILE`：前测试循环
- `REPEAT...UNTIL...END REPEAT`：后测试循环
- `LOOP...END LOOP`：无限循环（配合 LEAVE 退出）
- `ITERATE`：跳过本次循环（类似 continue）

### 例子

**基本游标使用：**

```sql
DELIMITER //
CREATE PROCEDURE ProcessProducts()
BEGIN
    DECLARE v_id INT;
    DECLARE v_name VARCHAR(100);
    DECLARE v_price DECIMAL(10,2);
    DECLARE v_done INT DEFAULT 0;
    
    -- 声明游标结束处理器
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET v_done = 1;
    
    -- 声明游标
    DECLARE cur CURSOR FOR 
        SELECT id, name, price FROM products WHERE stock = 0;
    
    -- 创建临时表存储结果
    CREATE TEMPORARY TABLE IF NOT EXISTS out_of_stock (
        product_id INT,
        product_name VARCHAR(100),
        suggested_action VARCHAR(50)
    );
    
    -- 打开游标
    OPEN cur;
    
    -- 循环获取数据
    read_loop: LOOP
        FETCH cur INTO v_id, v_name, v_price;
        IF v_done = 1 THEN
            LEAVE read_loop;
        END IF;
        
        -- 逐行处理
        IF v_price > 1000 THEN
            INSERT INTO out_of_stock VALUES (v_id, v_name, '紧急补货');
        ELSE
            INSERT INTO out_of_stock VALUES (v_id, v_name, '常规补货');
        END IF;
    END LOOP;
    
    -- 关闭游标
    CLOSE cur;
    
    -- 返回结果
    SELECT * FROM out_of_stock;
    
    -- 清理临时表
    DROP TEMPORARY TABLE IF EXISTS out_of_stock;
END //
DELIMITER ;

-- 调用
CALL ProcessProducts();
```

**流程控制示例：**

```sql
DELIMITER //
CREATE PROCEDURE DemoControlFlow(IN p_value INT)
BEGIN
    DECLARE v_result VARCHAR(100);
    
    -- IF 语句
    IF p_value > 0 THEN
        SET v_result = '正数';
    ELSEIF p_value < 0 THEN
        SET v_result = '负数';
    ELSE
        SET v_result = '零';
    END IF;
    
    SELECT v_result AS if_result;
    
    -- CASE 语句
    CASE
        WHEN p_value > 100 THEN SET v_result = '大';
        WHEN p_value > 10 THEN SET v_result = '中';
        WHEN p_value > 0 THEN SET v_result = '小';
        ELSE SET v_result = '非正';
    END CASE;
    
    SELECT v_result AS case_result;
END //
DELIMITER ;

CALL DemoControlFlow(50);
```

**循环语句示例：**

```sql
DELIMITER //
CREATE PROCEDURE DemoLoops(IN p_count INT)
BEGIN
    DECLARE v_i INT DEFAULT 0;
    DECLARE v_sum INT DEFAULT 0;
    
    -- WHILE 循环（前测试）
    WHILE v_i < p_count DO
        SET v_sum = v_sum + v_i;
        SET v_i = v_i + 1;
    END WHILE;
    SELECT v_sum AS while_sum;
    
    -- REPEAT 循环（后测试）
    SET v_i = 0;
    SET v_sum = 0;
    REPEAT
        SET v_sum = v_sum + v_i;
        SET v_i = v_i + 1;
    UNTIL v_i >= p_count END REPEAT;
    SELECT v_sum AS repeat_sum;
    
    -- LOOP 循环（配合 LEAVE 和 ITERATE）
    SET v_i = 0;
    SET v_sum = 0;
    my_loop: LOOP
        SET v_i = v_i + 1;
        
        -- 跳过偶数（ITERATE 类似 continue）
        IF v_i MOD 2 = 0 THEN
            ITERATE my_loop;
        END IF;
        
        -- 退出循环（LEAVE 类似 break）
        IF v_i > p_count THEN
            LEAVE my_loop;
        END IF;
        
        SET v_sum = v_sum + v_i;
    END LOOP my_loop;
    SELECT v_sum AS loop_sum;  -- 奇数之和
END //
DELIMITER ;

CALL DemoLoops(10);
```

**批量数据处理示例：**

```sql
DELIMITER //
CREATE PROCEDURE BatchUpdatePrices(IN p_category_id INT, IN p_increase_rate DECIMAL(3,2))
BEGIN
    DECLARE v_id INT;
    DECLARE v_current_price DECIMAL(10,2);
    DECLARE v_new_price DECIMAL(10,2);
    DECLARE v_done INT DEFAULT 0;
    DECLARE v_updated INT DEFAULT 0;
    
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET v_done = 1;
    
    DECLARE cur CURSOR FOR 
        SELECT id, price FROM products 
        WHERE category_id = p_category_id AND is_on_sale = TRUE;
    
    OPEN cur;
    
    price_loop: LOOP
        FETCH cur INTO v_id, v_current_price;
        IF v_done = 1 THEN
            LEAVE price_loop;
        END IF;
        
        SET v_new_price = ROUND(v_current_price * (1 + p_increase_rate), 2);
        
        UPDATE products SET price = v_new_price WHERE id = v_id;
        SET v_updated = v_updated + 1;
    END LOOP;
    
    CLOSE cur;
    
    SELECT CONCAT('已更新 ', v_updated, ' 个商品价格') AS result;
END //
DELIMITER ;

CALL BatchUpdatePrices(1, 0.10);  -- 分类1商品涨价10%
```

### 总结

- 游标用于逐行处理结果集，只能在存储过程/函数/触发器中使用
- 游标使用流程：声明 → 打开 → FETCH 获取 → 关闭
- 必须声明 `CONTINUE HANDLER FOR NOT FOUND` 处理游标结束
- 流程控制：IF（条件分支）、CASE（多分支）、WHILE/REPEAT/LOOP（循环）
- LEAVE 退出循环（类似 break），ITERATE 跳过本次（类似 continue）
- 游标处理大数据量时性能较差，应尽量用集合操作（UPDATE...WHERE）替代逐行处理
- 游标会占用内存资源，使用后务必 CLOSE
- 临时表（TEMPORARY TABLE）常与游标配合，存储中间结果
- 批量数据处理优先考虑 SQL 集合操作，游标作为最后手段

---

# 第7章：事务与并发控制

## 第26讲：事务与 ACID

### 概念

事务（Transaction）是数据库中一组操作的逻辑单元，这些操作要么全部成功执行，要么全部不执行。事务是保证数据一致性的核心机制。例如银行转账：从 A 账户扣款和向 B 账户存款必须作为一个事务，不能只执行一半。事务具有 ACID 四个特性。

### 原理

**ACID 特性：**

1. **原子性（Atomicity）**：事务中的所有操作是一个不可分割的整体，要么全部成功，要么全部回滚。通过 undo log（回滚日志）实现，失败时利用 undo log 恢复数据。

2. **一致性（Consistency）**：事务执行前后，数据库从一个一致状态转变为另一个一致状态。例如转账前后，两个账户的总金额不变。一致性由原子性、隔离性、持久性共同保证。

3. **隔离性（Isolation）**：多个事务并发执行时，一个事务的执行不应影响其他事务。通过锁机制和 MVCC（多版本并发控制）实现。

4. **持久性（Durability）**：事务提交后，对数据的修改是永久的，即使系统崩溃也不会丢失。通过 redo log（重做日志）实现，提交时先写 redo log 再写磁盘。

**事务的生命周期：**

```
BEGIN/START TRANSACTION → 执行SQL → COMMIT（提交）/ ROLLBACK（回滚）
```

**事务的隐式提交：**
- MariaDB 默认开启自动提交（autocommit=1），每条 SQL 自动作为一个事务提交
- 使用 `BEGIN` 或 `START TRANSACTION` 可以开启显式事务
- DDL 语句（CREATE/ALTER/DROP）会隐式提交当前事务

### 例子

**基本事务操作：**

```sql
-- 显式事务
START TRANSACTION;

UPDATE accounts SET balance = balance - 500 WHERE id = 1;  -- A扣款
UPDATE accounts SET balance = balance + 500 WHERE id = 2;  -- B存款

-- 检查结果
SELECT balance FROM accounts WHERE id IN (1, 2);

-- 确认无误后提交
COMMIT;

-- 如果发现问题，回滚（撤销所有操作）
-- ROLLBACK;
```

**保存点（SAVEPOINT）：**

```sql
START TRANSACTION;

INSERT INTO orders (order_no, total_amount) VALUES ('ORD001', 100);
SAVEPOINT sp1;  -- 设置保存点

INSERT INTO orders (order_no, total_amount) VALUES ('ORD002', 200);
SAVEPOINT sp2;

INSERT INTO orders (order_no, total_amount) VALUES ('ORD003', 300);

-- 发现ORD003有问题，回滚到sp2（只撤销ORD003）
ROLLBACK TO SAVEPOINT sp2;

-- 此时ORD001和ORD002仍然存在
-- 可以继续提交或回滚
COMMIT;
```

**事务与异常处理：**

```sql
-- 存储过程中的事务处理
DELIMITER //
CREATE PROCEDURE TransferMoney(
    IN p_from_id INT,
    IN p_to_id INT,
    IN p_amount DECIMAL(10,2)
)
BEGIN
    DECLARE EXIT HANDLER FOR SQLEXCEPTION
    BEGIN
        ROLLBACK;
        SELECT '转账失败，已回滚' AS message;
    END;
    
    START TRANSACTION;
    
    -- 检查余额
    IF (SELECT balance FROM accounts WHERE id = p_from_id) < p_amount THEN
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = '余额不足';
    END IF;
    
    -- 执行转账
    UPDATE accounts SET balance = balance - p_amount WHERE id = p_from_id;
    UPDATE accounts SET balance = balance + p_amount WHERE id = p_to_id;
    
    COMMIT;
    SELECT '转账成功' AS message;
END //
DELIMITER ;

-- 调用
CALL TransferMoney(1, 2, 500);
```

**查看事务状态：**

```sql
-- 查看自动提交设置
SELECT @@autocommit;  -- 1表示开启自动提交

-- 关闭自动提交（当前会话）
SET autocommit = 0;
-- 之后所有SQL都在同一事务中，直到手动COMMIT/ROLLBACK

-- 查看当前活跃事务
SELECT * FROM information_schema.innodb_trx;
```

### 总结

- 事务保证 ACID：原子性（全部成功或全部回滚）、一致性（数据正确）、隔离性（并发互不干扰）、持久性（提交后永久）
- `START TRANSACTION` 开启事务，`COMMIT` 提交，`ROLLBACK` 回滚
- `SAVEPOINT` 设置保存点，可以回滚到指定保存点而非整个事务
- MariaDB 默认自动提交，每条 SQL 自动作为一个事务
- DDL 语句会隐式提交当前事务，需注意事务中避免混用 DDL
- 生产环境的重要操作（如转账、下单）必须在事务中执行
- 事务应尽量短小，长时间事务会占用锁和资源，影响并发性能
- 存储过程中使用 `DECLARE EXIT HANDLER FOR SQLEXCEPTION` 捕获异常并回滚

---

## 第27讲：隔离级别

### 概念

隔离级别（Isolation Level）定义了多个事务并发执行时的隔离程度。隔离级别越高，数据一致性越好，但并发性能越低；隔离级别越低，并发性能越高，但可能出现数据不一致问题。MariaDB/InnoDB 提供四种标准隔离级别，默认使用 REPEATABLE READ。

### 原理

**四种隔离级别（从低到高）：**

| 隔离级别 | 脏读 | 不可重复读 | 幻读 |
|----------|------|-----------|------|
| READ UNCOMMITTED（读未提交） | 可能 | 可能 | 可能 |
| READ COMMITTED（读已提交） | 不可能 | 可能 | 可能 |
| REPEATABLE READ（可重复读） | 不可能 | 不可能 | InnoDB不可能 |
| SERIALIZABLE（串行化） | 不可能 | 不可能 | 不可能 |

**三种并发问题：**

1. **脏读（Dirty Read）**：事务 A 读到了事务 B 尚未提交的数据。如果 B 回滚，A 读到的数据就是无效的。

2. **不可重复读（Non-repeatable Read）**：事务 A 两次读取同一行数据，结果不同（因为事务 B 在中间修改并提交了）。

3. **幻读（Phantom Read）**：事务 A 两次执行相同查询，结果集行数不同（因为事务 B 在中间插入/删除了数据）。

**InnoDB 的 MVCC 机制：**
InnoDB 通过 MVCC（多版本并发控制）实现快照读，在 REPEATABLE READ 级别下：
- 普通 SELECT 是快照读，读取事务开始时的数据版本，不会看到其他事务的修改
- InnoDB 在 REPEATABLE READ 下通过 Next-Key Lock 解决了幻读问题

### 例子

**查看和设置隔离级别：**

```sql
-- 查看当前隔离级别
SELECT @@transaction_isolation;
-- MariaDB 10.6+ 使用 transaction_isolation
-- 旧版本使用 tx_isolation

-- 设置隔离级别（当前会话）
SET SESSION transaction_isolation = 'READ-COMMITTED';

-- 设置全局隔离级别
SET GLOBAL transaction_isolation = 'REPEATABLE-READ';

-- 查看所有隔离级别选项
SELECT * FROM information_schema.session_variables 
WHERE Variable_name = 'transaction_isolation';
```

**READ UNCOMMITTED（读未提交）- 可能脏读：**

```sql
-- 会话A
SET SESSION transaction_isolation = 'READ-UNCOMMITTED';
START TRANSACTION;
UPDATE accounts SET balance = balance + 1000 WHERE id = 1;
-- 未提交

-- 会话B
SET SESSION transaction_isolation = 'READ-UNCOMMITTED';
SELECT balance FROM accounts WHERE id = 1;
-- 能看到会话A未提交的修改（脏读）

-- 会话A回滚
ROLLBACK;
-- 会话B之前读到的数据是无效的
```

**READ COMMITTED（读已提交）- 解决脏读：**

```sql
-- 会话A
SET SESSION transaction_isolation = 'READ-COMMITTED';
START TRANSACTION;
UPDATE accounts SET balance = balance + 1000 WHERE id = 1;
-- 未提交

-- 会话B
SET SESSION transaction_isolation = 'READ-COMMITTED';
START TRANSACTION;
SELECT balance FROM accounts WHERE id = 1;
-- 看不到会话A未提交的修改（无脏读）

-- 会话A提交
COMMIT;

-- 会话B再次查询
SELECT balance FROM accounts WHERE id = 1;
-- 现在能看到修改了（不可重复读：同一事务中两次查询结果不同）
COMMIT;
```

**REPEATABLE READ（可重复读）- 解决不可重复读：**

```sql
-- 会话A
SET SESSION transaction_isolation = 'REPEATABLE-READ';
START TRANSACTION;
SELECT balance FROM accounts WHERE id = 1;  -- 假设结果为1000

-- 会话B
UPDATE accounts SET balance = 2000 WHERE id = 1;
COMMIT;

-- 会话A再次查询
SELECT balance FROM accounts WHERE id = 1;
-- 结果仍为1000（可重复读，读取事务开始时的快照）

COMMIT;
-- 会话A提交后再次查询
SELECT balance FROM accounts WHERE id = 1;
-- 结果为2000（新事务读取最新数据）
```

**SERIALIZABLE（串行化）- 最高隔离：**

```sql
-- 会话A
SET SESSION transaction_isolation = 'SERIALIZABLE';
START TRANSACTION;
SELECT * FROM accounts WHERE balance > 1000;  -- 加共享锁

-- 会话B尝试修改
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
-- 被阻塞，等待会话A提交或回滚

-- 会话A提交后，会话B才能执行
COMMIT;
```

### 总结

- 四种隔离级别从低到高：READ UNCOMMITTED < READ COMMITTED < REPEATABLE READ < SERIALIZABLE
- 隔离级别越高，数据一致性越好，但并发性能越低
- MariaDB/InnoDB 默认隔离级别是 REPEATABLE READ，通过 MVCC 解决了脏读、不可重复读和幻读
- READ COMMITTED 适合读多写少、对一致性要求不高的场景（如报表查询）
- REPEATABLE READ 是大多数场景的最佳选择，平衡了一致性和性能
- SERIALIZABLE 并发性能差，通常只在严格要求一致性的场景使用
- 快照读（普通 SELECT）通过 MVCC 读取历史版本，不加锁
- 当前读（SELECT...FOR UPDATE、UPDATE、DELETE）读取最新数据并加锁

---

## 第28讲：锁机制

### 概念

锁（Lock）是数据库用于控制并发访问的机制。当多个事务同时访问同一数据时，锁可以保证数据的一致性。MariaDB/InnoDB 提供了多种锁类型，包括共享锁、排他锁、行锁、表锁、间隙锁等。理解锁机制对于解决并发问题和死锁至关重要。

### 原理

**锁的分类：**

1. **按粒度分：**
   - **表锁**：锁定整张表，粒度大，并发低，开销小
   - **行锁**：锁定单行，粒度小，并发高，开销大
   - **间隙锁（Gap Lock）**：锁定索引区间，防止插入，解决幻读

2. **按类型分：**
   - **共享锁（S锁/读锁）**：多个事务可同时读，但不能写
   - **排他锁（X锁/写锁）**：只有一个事务能读写，其他事务阻塞

3. **按意向分：**
   - **意向共享锁（IS）**：事务打算获取某行的共享锁前，先获取表的 IS 锁
   - **意向排他锁（IX）**：事务打算获取某行的排他锁前，先获取表的 IX 锁

**InnoDB 行锁实现：**
InnoDB 的行锁是加在索引上的，不是加在数据行上。如果没有索引，行锁会升级为表锁。

**锁的兼容性矩阵：**

| | S锁 | X锁 |
|---|---|---|
| S锁 | 兼容 | 冲突 |
| X锁 | 冲突 | 冲突 |

### 例子

**共享锁与排他锁：**

```sql
-- 共享锁（S锁）：SELECT ... LOCK IN SHARE MODE
-- 会话A
START TRANSACTION;
SELECT * FROM products WHERE id = 1 LOCK IN SHARE MODE;
-- 获取id=1的共享锁

-- 会话B
START TRANSACTION;
SELECT * FROM products WHERE id = 1 LOCK IN SHARE MODE;
-- 成功：共享锁之间兼容，可以同时读

UPDATE products SET price = 100 WHERE id = 1;
-- 阻塞：排他锁与共享锁冲突，等待会话A释放锁

-- 会话A提交后，会话B才能执行
COMMIT;

-- 排他锁（X锁）：SELECT ... FOR UPDATE 或 UPDATE/DELETE
-- 会话A
START TRANSACTION;
SELECT * FROM products WHERE id = 1 FOR UPDATE;
-- 获取id=1的排他锁

-- 会话B
START TRANSACTION;
SELECT * FROM products WHERE id = 1 FOR UPDATE;
-- 阻塞：排他锁之间冲突，等待会话A释放
```

**行锁与表锁：**

```sql
-- 行锁（有索引）
-- 会话A
START TRANSACTION;
UPDATE products SET price = 100 WHERE id = 1;  -- id是主键，行锁
-- 只锁定id=1这一行

-- 会话B
START TRANSACTION;
UPDATE products SET price = 200 WHERE id = 2;  -- 可以执行，不冲突
COMMIT;

-- 表锁（无索引）
-- 会话A
START TRANSACTION;
UPDATE products SET price = 100 WHERE name = '笔记本电脑';  -- name无索引
-- 锁定整张表（行锁升级为表锁）

-- 会话B
START TRANSACTION;
UPDATE products SET price = 200 WHERE id = 2;
-- 阻塞：即使id=2不是会话A修改的行，也因表锁而阻塞
```

**间隙锁（Gap Lock）：**

```sql
-- 间隙锁防止幻读，在REPEATABLE READ下生效
-- 假设products表id为: 1, 5, 10, 15

-- 会话A
START TRANSACTION;
SELECT * FROM products WHERE id BETWEEN 5 AND 10 FOR UPDATE;
-- 锁定id=5和id=10的行，以及(5,10)的间隙
-- 实际锁定范围: [5, 10]（Next-Key Lock）

-- 会话B尝试插入id=7
START TRANSACTION;
INSERT INTO products (id, name) VALUES (7, '新商品');
-- 阻塞：id=7在间隙(5,10)内，被间隙锁阻止

-- 会话B尝试插入id=20
INSERT INTO products (id, name) VALUES (20, '新商品');
-- 成功：id=20不在锁定范围内
```

**查看锁信息：**

```sql
-- 查看当前锁等待
SELECT * FROM information_schema.innodb_locks;

-- 查看锁等待关系
SELECT * FROM information_schema.innodb_lock_waits;

-- MariaDB 10.5+ 使用 performance_schema
SELECT * FROM performance_schema.data_locks;
SELECT * FROM performance_schema.data_lock_waits;

-- 查看InnoDB状态（包含锁信息）
SHOW ENGINE INNODB STATUS\G
```

**死锁示例：**

```sql
-- 会话A
START TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;  -- 锁定id=1

-- 会话B
START TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE id = 2;  -- 锁定id=2

-- 会话A
UPDATE accounts SET balance = balance + 100 WHERE id = 2;  -- 等待id=2的锁

-- 会话B
UPDATE accounts SET balance = balance + 100 WHERE id = 1;  -- 等待id=1的锁
-- 死锁！InnoDB检测到死锁，回滚其中一个事务
-- ERROR 1213 (40001): Deadlock found when trying to get lock
```

### 总结

- 共享锁（S锁）允许并发读，排他锁（X锁）独占读写
- `SELECT ... LOCK IN SHARE MODE` 加共享锁，`SELECT ... FOR UPDATE` 加排他锁
- InnoDB 行锁加在索引上，无索引时升级为表锁，性能急剧下降
- 间隙锁（Gap Lock）在 REPEATABLE READ 下防止幻读，锁定索引区间
- Next-Key Lock = 行锁 + 间隙锁，是 InnoDB 在 RR 隔离级别下的默认锁
- 死锁是两个事务互相等待对方释放锁，InnoDB 会自动检测并回滚代价较小的事务
- 避免死锁的方法：按固定顺序访问表和行、事务尽量短小、使用低隔离级别
- 大量更新操作时，按主键排序可以减少锁冲突
- `SHOW ENGINE INNODB STATUS` 可以查看最近的死锁信息

---

## 第29讲：存储引擎

### 概念

存储引擎（Storage Engine）是数据库管理数据存储和检索的底层组件。MariaDB 采用插件式存储引擎架构，同一张表可以使用不同的存储引擎，不同的引擎有不同的特性和适用场景。MariaDB 最常用的存储引擎是 InnoDB（默认）和 MyISAM，此外还有 Aria、Memory、ColumnStore 等特殊用途引擎。

### 原理

**MariaDB 主要存储引擎对比：**

| 特性 | InnoDB | MyISAM | Aria | Memory |
|------|--------|--------|------|--------|
| 事务支持 | ✓ | ✗ | ✗ | ✗ |
| 行锁 | ✓ | ✗（表锁） | ✗（表锁） | ✗（表锁） |
| 外键 | ✓ | ✗ | ✗ | ✗ |
| 崩溃恢复 | ✓ | ✗ | ✓ | ✗ |
| 聚簇索引 | ✓ | ✗ | ✗ | ✗ |
| 全文索引 | ✓ | ✓ | ✓ | ✗ |
| 适用场景 | 通用、高并发 | 只读为主 | 临时表 | 临时数据 |

**InnoDB 特点：**
- 支持 ACID 事务
- 行级锁，高并发性能好
- 支持外键约束
- 聚簇索引，主键查询快
- 支持 MVCC，读写不冲突
- 崩溃后能自动恢复

**MyISAM 特点：**
- 不支持事务
- 表级锁，并发性能差
- 全文索引性能好
- COUNT(*) 不需要扫描，直接读取计数器
- 适合以读为主的场景（如日志表、报表表）

**Aria 特点：**
- MyISAM 的改进版，支持崩溃恢复
- 适合临时表和内部使用
- MariaDB 系统表使用 Aria 引擎

### 例子

**查看和设置存储引擎：**

```sql
-- 查看当前默认存储引擎
SELECT @@default_storage_engine;
-- 通常是 InnoDB

-- 查看所有可用引擎
SHOW ENGINES;
/*
+--------------------+---------+----------------------------------+
| Engine             | Support | Comment                          |
+--------------------+---------+----------------------------------+
| InnoDB             | DEFAULT | Supports transactions...         |
| MyISAM             | YES     | Non-transactional engine...      |
| Aria               | YES     | Crash-safe tables...             |
| MEMORY             | YES     | Hash based, stored in memory...  |
| CSV                | YES     | CSV storage engine...            |
+--------------------+---------+----------------------------------+
*/

-- 设置默认存储引擎（当前会话）
SET SESSION default_storage_engine = 'InnoDB';

-- 设置全局默认存储引擎
SET GLOBAL default_storage_engine = 'InnoDB';
```

**建表时指定引擎：**

```sql
-- 创建InnoDB表（默认）
CREATE TABLE orders (
    id INT PRIMARY KEY,
    order_no VARCHAR(50),
    total_amount DECIMAL(10,2)
) ENGINE=InnoDB;

-- 创建MyISAM表（适合只读日志）
CREATE TABLE access_logs (
    id INT AUTO_INCREMENT PRIMARY KEY,
    ip VARCHAR(45),
    url VARCHAR(500),
    access_time DATETIME
) ENGINE=MyISAM;

-- 创建Memory表（临时数据）
CREATE TABLE session_cache (
    session_id VARCHAR(64) PRIMARY KEY,
    user_id INT,
    expire_time DATETIME
) ENGINE=MEMORY;
```

**修改表的引擎：**

```sql
-- 将MyISAM表转为InnoDB
ALTER TABLE access_logs ENGINE=InnoDB;

-- 注意：转换大表会锁表较长时间，需要在低峰期执行
-- 转换过程：创建新表 → 复制数据 → 重命名表 → 删除旧表
```

**查看表的引擎：**

```sql
-- 查看表信息（包含引擎）
SHOW TABLE STATUS FROM mydb LIKE 'orders'\G
/*
       Name: orders
     Engine: InnoDB
    Version: 10
 Row_format: Dynamic
       Rows: 1000
 Avg_row_length: 80
    Data_length: 80000
Max_data_length: 0
   Index_length: 32768
      Comment: 
*/

-- 查看所有表的引擎
SELECT table_name, engine 
FROM information_schema.tables 
WHERE table_schema = 'mydb';
```

**InnoDB 关键参数：**

```sql
-- 查看InnoDB缓冲池大小（最重要的参数）
SELECT @@innodb_buffer_pool_size;
-- 建议设置为物理内存的50-70%

-- 查看InnoDB日志文件大小
SELECT @@innodb_log_file_size;

-- 查看InnoDB刷盘策略
SELECT @@innodb_flush_log_at_trx_commit;
-- 0: 每秒刷盘（性能好，可能丢1秒数据）
-- 1: 每次提交刷盘（默认，最安全）
-- 2: 每次提交写OS缓存，每秒刷盘（折中）

-- 查看InnoDB锁等待超时
SELECT @@innodb_lock_wait_timeout;  -- 默认50秒
```

### 总结

- InnoDB 是 MariaDB 默认存储引擎，支持事务、行锁、外键，适合大多数场景
- MyISAM 不支持事务，表级锁，适合只读或读多写少的场景
- Aria 是 MyISAM 的改进版，支持崩溃恢复，用于系统表和临时表
- Memory 引擎数据存内存，速度快但不持久，适合缓存
- 建表时通过 `ENGINE=` 指定存储引擎，可用 `ALTER TABLE` 修改
- 生产环境强烈建议使用 InnoDB，除非有特殊需求
- InnoDB 的 `innodb_buffer_pool_size` 是最重要的性能参数，建议设为物理内存的 50-70%
- `innodb_flush_log_at_trx_commit=1` 最安全但性能最低，可根据业务需求调整
- 转换大表引擎会锁表，需在低峰期执行

---

# 第8章：安全与管理

## 第30讲：用户与权限管理

### 概念

用户与权限管理是数据库安全的核心。MariaDB 通过用户账户和权限系统控制谁可以访问数据库以及能执行什么操作。合理的权限管理可以防止未授权访问、数据泄露和误操作。MariaDB 的权限系统采用"用户+主机"的认证方式和分层授权模型。

### 原理

**MariaDB 认证机制：**
- 用户身份由"用户名@主机名"唯一标识，如 `admin@localhost` 和 `admin@192.168.1.1` 是不同用户
- 主机可以使用通配符：`%` 表示任意主机，`192.168.%.%` 表示 192.168.x.x 网段
- 连接时，MariaDB 按照最具体的匹配选择用户

**权限层级：**

| 层级 | 说明 | 示例 |
|------|------|------|
| 全局级 | 所有数据库的权限 | `GRANT ALL ON *.*` |
| 数据库级 | 特定数据库的权限 | `GRANT ALL ON mydb.*` |
| 表级 | 特定表的权限 | `GRANT SELECT ON mydb.users` |
| 列级 | 特定列的权限 | `GRANT SELECT(name) ON mydb.users` |
| 存储过程级 | 特定例程的权限 | `GRANT EXECUTE ON PROCEDURE mydb.proc` |

**常用权限：**

| 权限 | 说明 |
|------|------|
| SELECT | 查询数据 |
| INSERT | 插入数据 |
| UPDATE | 更新数据 |
| DELETE | 删除数据 |
| CREATE | 创建数据库/表 |
| DROP | 删除数据库/表 |
| ALTER | 修改表结构 |
| INDEX | 创建/删除索引 |
| ALL | 所有权限（除GRANT OPTION） |
| GRANT OPTION | 授权给其他用户 |

### 例子

**用户管理：**

```sql
-- 创建用户
CREATE USER 'app_user'@'localhost' IDENTIFIED BY 'password123';
CREATE USER 'app_user'@'192.168.1.%' IDENTIFIED BY 'password123';
CREATE USER 'readonly'@'%' IDENTIFIED BY 'readonly_pass';

-- 创建用户并指定认证插件
CREATE USER 'admin'@'localhost' IDENTIFIED VIA mysql_native_password USING 'admin_pass';

-- 修改用户密码
ALTER USER 'app_user'@'localhost' IDENTIFIED BY 'new_password';

-- 修改当前用户密码
SET PASSWORD = PASSWORD('new_password');

-- 重命名用户
RENAME USER 'app_user'@'localhost' TO 'webapp'@'localhost';

-- 删除用户
DROP USER 'app_user'@'localhost';

-- 查看所有用户
SELECT user, host FROM mysql.user;
```

**授权：**

```sql
-- 授予全局权限
GRANT ALL PRIVILEGES ON *.* TO 'admin'@'localhost' WITH GRANT OPTION;
-- WITH GRANT OPTION: 允许该用户授权给其他用户

-- 授予数据库级权限
GRANT SELECT, INSERT, UPDATE, DELETE ON mydb.* TO 'app_user'@'localhost';

-- 授予表级权限
GRANT SELECT, INSERT ON mydb.products TO 'app_user'@'localhost';

-- 授予列级权限
GRANT SELECT (id, name, price) ON mydb.products TO 'report_user'@'localhost';

-- 授予只读权限（适合报表用户）
GRANT SELECT ON mydb.* TO 'readonly'@'%';

-- 授予存储过程执行权限
GRANT EXECUTE ON PROCEDURE mydb.PlaceOrder TO 'app_user'@'localhost';

-- 刷新权限（通常不需要，GRANT会自动刷新）
FLUSH PRIVILEGES;
```

**查看权限：**

```sql
-- 查看当前用户权限
SHOW GRANTS;
-- 或
SHOW GRANTS FOR CURRENT_USER();

-- 查看指定用户权限
SHOW GRANTS FOR 'app_user'@'localhost';
/*
+--------------------------------------------------+
| Grants for app_user@localhost                    |
+--------------------------------------------------+
| GRANT USAGE ON *.* TO 'app_user'@'localhost'     |
| GRANT SELECT, INSERT, UPDATE, DELETE ON mydb.*...|
+--------------------------------------------------+
*/

-- 查看所有用户的权限
SELECT user, host, Select_priv, Insert_priv, Update_priv, Delete_priv 
FROM mysql.user;
```

**撤销权限：**

```sql
-- 撤销部分权限
REVOKE INSERT, UPDATE ON mydb.* FROM 'app_user'@'localhost';

-- 撤销所有权限
REVOKE ALL PRIVILEGES ON mydb.* FROM 'app_user'@'localhost';

-- 撤销GRANT OPTION权限
REVOKE GRANT OPTION ON mydb.* FROM 'app_user'@'localhost';

-- 注意：撤销权限不会删除用户，用户仍可连接但无权限操作
```

**角色管理（MariaDB 10.0+）：**

```sql
-- 创建角色
CREATE ROLE 'read_only';
CREATE ROLE 'read_write';
CREATE ROLE 'admin_role';

-- 给角色授权
GRANT SELECT ON mydb.* TO 'read_only';
GRANT SELECT, INSERT, UPDATE, DELETE ON mydb.* TO 'read_write';
GRANT ALL ON mydb.* TO 'admin_role';

-- 将角色授予用户
GRANT 'read_only' TO 'report_user'@'%';
GRANT 'read_write' TO 'app_user'@'localhost';

-- 用户启用角色
SET ROLE 'read_only';
-- 或启用所有已授予的角色
SET ROLE ALL;
-- 恢复默认角色
SET ROLE NONE;

-- 设置默认角色（连接时自动启用）
ALTER USER 'report_user'@'%' DEFAULT ROLE 'read_only';

-- 查看角色
SHOW ROLES;
SELECT * FROM mysql.roles_mapping;
```

**安全最佳实践：**

```sql
-- 1. 删除匿名用户
DELETE FROM mysql.user WHERE User = '';
FLUSH PRIVILEGES;

-- 2. 限制root用户只能本地登录
-- 确保没有 root@'%' 用户
SELECT user, host FROM mysql.user WHERE user = 'root';

-- 3. 创建专用应用用户，不使用root
CREATE USER 'myapp'@'10.0.%.%' IDENTIFIED BY 'strong_password';
GRANT SELECT, INSERT, UPDATE, DELETE ON myapp_db.* TO 'myapp'@'10.0.%.%';

-- 4. 设置密码策略
SET GLOBAL validate_password_length = 12;
SET GLOBAL validate_password_policy = 'MEDIUM';

-- 5. 查看连接数限制
SHOW VARIABLES LIKE 'max_user_connections';
```

### 总结

- 用户身份由"用户名@主机名"唯一标识，`%` 表示任意主机
- 权限分层：全局 > 数据库 > 表 > 列，粒度越细控制越精确
- `GRANT` 授权，`REVOKE` 撤权，`SHOW GRANTS` 查看权限
- `WITH GRANT OPTION` 允许用户将自己的权限授予他人
- 角色简化权限管理：创建角色 → 授权给角色 → 角色授予用户
- 生产环境安全原则：删除匿名用户、root 只允许本地登录、应用使用专用用户、最小权限原则
- 密码应使用强密码，可设置密码策略强制要求
- 定期审计用户权限，清理不需要的账户和权限
- `FLUSH PRIVILEGES` 重新加载权限表（直接修改 mysql.user 表后需要）

---

## 第31讲：备份与恢复

### 概念

备份与恢复是数据库运维的核心任务。备份是在正常时定期保存数据副本，恢复是在故障时从备份还原数据。合理的备份策略可以应对硬件故障、人为误操作、数据损坏等各种数据丢失场景，是数据安全的最后一道防线。

### 原理

**备份类型：**

1. **按数据范围：**
   - **全量备份**：备份所有数据，恢复简单但耗时耗空间
   - **增量备份**：只备份上次备份后变化的数据，节省空间但恢复复杂
   - **差异备份**：备份上次全量备份后变化的数据，介于两者之间

2. **按方式：**
   - **逻辑备份**：导出 SQL 语句（如 mysqldump），可跨平台迁移
   - **物理备份**：复制数据文件（如 mariabackup），速度快但平台相关

3. **按状态：**
   - **热备份**：备份时不影响读写（InnoDB 支持）
   - **温备份**：备份时可读不可写
   - **冷备份**：需要停止数据库

**备份工具：**
- `mysqldump`：逻辑备份工具，导出 SQL 语句
- `mariabackup`：物理备份工具（Percona XtraBackup 的分支），支持热备份
- `mysqlbinlog`：二进制日志工具，用于时间点恢复

**二进制日志（binlog）：**
- 记录所有修改数据的 SQL 语句
- 用于复制和时间点恢复
- 开启：`log_bin = mysql-bin`

### 例子

**mysqldump 逻辑备份：**

```bash
# 备份单个数据库
mysqldump -u root -p mydb > /backup/mydb_20240115.sql

# 备份多个数据库
mysqldump -u root -p --databases mydb1 mydb2 > /backup/multi_db.sql

# 备份所有数据库
mysqldump -u root -p --all-databases > /backup/all_db.sql

# 只备份结构（不包含数据）
mysqldump -u root -p --no-data mydb > /backup/mydb_schema.sql

# 只备份数据（不包含结构）
mysqldump -u root -p --no-create-info mydb > /backup/mydb_data.sql

# 备份并压缩
mysqldump -u root -p mydb | gzip > /backup/mydb_20240115.sql.gz

# 备份存储过程和触发器
mysqldump -u root -p --routines --triggers mydb > /backup/mydb_full.sql

# 使用事务（保证一致性快照）
mysqldump -u root -p --single-transaction mydb > /backup/mydb_consistent.sql
```

**恢复数据：**

```bash
# 从SQL文件恢复
mysql -u root -p mydb < /backup/mydb_20240115.sql

# 从压缩文件恢复
gunzip < /backup/mydb_20240115.sql.gz | mysql -u root -p mydb

# 恢复到指定数据库
mysql -u root -p -e "CREATE DATABASE IF NOT EXISTS mydb"
mysql -u root -p mydb < /backup/mydb_20240115.sql
```

**mariabackup 物理备份：**

```bash
# 全量备份
mariabackup --backup --target-dir=/backup/full_20240115 \
  --user=root --password=yourpass

# 准备备份（使备份一致）
mariabackup --prepare --target-dir=/backup/full_20240115

# 增量备份（基于全量备份）
mariabackup --backup --target-dir=/backup/inc_20240116 \
  --incremental-basedir=/backup/full_20240115 \
  --user=root --password=yourpass

# 恢复全量备份
systemctl stop mariadb
mariabackup --copy-back --target-dir=/backup/full_20240115
chown -R mysql:mysql /var/lib/mysql
systemctl start mariadb
```

**二进制日志与时间点恢复：**

```sql
-- 查看binlog状态
SHOW BINARY LOGS;
SHOW VARIABLES LIKE 'log_bin';

-- 查看当前binlog位置
SHOW MASTER STATUS;

-- 创建备份点（记录当前位置）
FLUSH LOGS;  -- 切换新binlog文件
SHOW MASTER STATUS;
-- 记录 File 和 Position
```

```bash
# 查看binlog内容
mysqlbinlog /var/lib/mysql/mysql-bin.000001

# 时间点恢复：先恢复全量备份，再重放binlog
mysql -u root -p mydb < /backup/full_backup.sql

# 重放binlog到指定时间点
mysqlbinlog --stop-datetime="2024-01-15 14:00:00" \
  /var/lib/mysql/mysql-bin.000001 | mysql -u root -p

# 重放binlog到指定位置
mysqlbinlog --stop-position=1234 \
  /var/lib/mysql/mysql-bin.000001 | mysql -u root -p
```

**定时备份脚本示例：**

```bash
#!/bin/bash
# /home/z/my-project/scripts/backup_mariadb.sh

BACKUP_DIR="/backup/mariadb"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="$BACKUP_DIR/mydb_$DATE.sql.gz"
RETENTION_DAYS=7

# 创建备份目录
mkdir -p $BACKUP_DIR

# 执行备份
mysqldump -u root -pYOUR_PASSWORD --single-transaction \
  --routines --triggers mydb | gzip > $BACKUP_FILE

# 检查备份是否成功
if [ $? -eq 0 ]; then
    echo "[$DATE] 备份成功: $BACKUP_FILE"
    
    # 清理旧备份
    find $BACKUP_DIR -name "mydb_*.sql.gz" -mtime +$RETENTION_DAYS -delete
    echo "已清理 $RETENTION_DAYS 天前的备份"
else
    echo "[$DATE] 备份失败!" | mail -s "MariaDB备份失败" admin@example.com
fi
```

```bash
# 设置定时任务（每天凌晨2点备份）
crontab -e
# 添加：
0 2 * * * /home/z/my-project/scripts/backup_mariadb.sh >> /var/log/mariadb_backup.log 2>&1
```

### 总结

- 备份类型：全量/增量/差异，逻辑/物理，热/温/冷
- `mysqldump` 适合小中型数据库逻辑备份，可跨平台迁移
- `mariabackup` 适合大型数据库物理备份，支持热备份和增量备份
- `--single-transaction` 保证 InnoDB 备份一致性（不锁表）
- 二进制日志（binlog）用于时间点恢复，可恢复到任意时间点
- 恢复流程：恢复全量备份 → 重放增量/binlog 到目标时间点
- 备份策略建议：每日全量 + binlog 实时同步，或每周全量 + 每日增量
- 备份文件应异地存储，防止单点故障
- 定期演练恢复流程，确保备份可用
- 备份脚本应包含成功检查和失败告警

---

## 第32讲：日志与监控

### 概念

日志和监控是数据库运维的"眼睛"。MariaDB 提供多种日志记录数据库运行状态，包括错误日志、查询日志、慢查询日志、二进制日志等。通过分析日志和监控指标，可以及时发现性能问题、排查故障、优化系统。

### 原理

**MariaDB 主要日志类型：**

| 日志类型 | 说明 | 用途 |
|----------|------|------|
| 错误日志 | 记录启动/运行/停止时的错误 | 排查故障 |
| 查询日志 | 记录所有 SQL 语句 | 调试（生产慎用） |
| 慢查询日志 | 记录执行超过阈值的 SQL | 性能优化 |
| 二进制日志 | 记录所有修改数据的 SQL | 复制/恢复 |
| 中继日志 | 从库接收的主库 binlog | 主从复制 |

**监控关键指标：**
- 连接数：当前连接数、最大连接数
- QPS/TPS：每秒查询数/事务数
- 缓冲池命中率：InnoDB Buffer Pool Hit Rate
- 锁等待：锁等待次数和时长
- 慢查询数量
- 磁盘使用和 I/O

### 例子

**配置日志：**

```sql
-- 查看日志配置
SHOW VARIABLES LIKE 'log_error';           -- 错误日志路径
SHOW VARIABLES LIKE 'general_log';         -- 查询日志开关
SHOW VARIABLES LIKE 'slow_query_log';      -- 慢查询日志开关
SHOW VARIABLES LIKE 'long_query_time';     -- 慢查询阈值
SHOW VARIABLES LIKE 'log_bin';             -- 二进制日志开关

-- 开启慢查询日志
SET GLOBAL slow_query_log = ON;
SET GLOBAL long_query_time = 1;  -- 超过1秒的查询记录
SET GLOBAL slow_query_log_file = '/var/log/mariadb/slow.log';

-- 开启查询日志（调试用，生产慎用）
SET GLOBAL general_log = ON;
SET GLOBAL general_log_file = '/var/log/mariadb/general.log';

-- 开启二进制日志（需在配置文件设置）
-- my.cnf: log_bin = mysql-bin
-- binlog_format = ROW
```

**慢查询日志分析：**

```sql
-- 查看慢查询日志文件路径
SELECT @@slow_query_log_file;

-- 使用mysqldumpslow分析慢查询
-- 命令行执行：
-- mysqldumpslow -s t -t 10 /var/log/mariadb/slow.log
-- -s t: 按总时间排序
-- -t 10: 显示前10条
```

```bash
# 分析慢查询日志
mysqldumpslow -s t -t 10 /var/log/mariadb/slow.log
# 输出示例：
# Count: 50  Time=2.50s (125s)  Lock=0.00s (0s)  Rows=1000.0 (50000)
# SELECT * FROM orders WHERE DATE(created_at) = 'S'
# 出现50次，平均2.5秒，总耗时125秒

# 按次数排序
mysqldumpslow -s c -t 10 /var/log/mariadb/slow.log

# 按返回行数排序
mysqldumpslow -s r -t 10 /var/log/mariadb/slow.log
```

**查看服务器状态：**

```sql
-- 查看全局状态
SHOW GLOBAL STATUS;

-- 查看关键指标
SHOW GLOBAL STATUS LIKE 'Connections';        -- 总连接数
SHOW GLOBAL STATUS LIKE 'Threads_connected';  -- 当前连接数
SHOW GLOBAL STATUS LIKE 'Uptime';             -- 运行时间
SHOW GLOBAL STATUS LIKE 'Questions';          -- 总查询数
SHOW GLOBAL STATUS LIKE 'Slow_queries';       -- 慢查询数
SHOW GLOBAL STATUS LIKE 'Innodb_buffer_pool_read_requests';  -- 缓冲池请求
SHOW GLOBAL STATUS LIKE 'Innodb_buffer_pool_reads';          -- 磁盘读取

-- 计算QPS（每秒查询数）
SHOW GLOBAL STATUS LIKE 'Questions';
SHOW GLOBAL STATUS LIKE 'Uptime';
-- QPS = Questions / Uptime

-- 计算缓冲池命中率
-- 命中率 = 1 - (Innodb_buffer_pool_reads / Innodb_buffer_pool_read_requests)
-- 应该 > 99%
```

**查看进程列表：**

```sql
-- 查看当前所有连接和正在执行的SQL
SHOW PROCESSLIST;
/*
+----+------+-----------+------+---------+------+----------+------------------+
| Id | User | Host      | db   | Command | Time | State    | Info             |
+----+------+-----------+------+---------+------+----------+------------------+
|  5 | root | localhost | mydb | Query   |    0 | starting | SHOW PROCESSLIST |
|  6 | app  | 10.0.0.1  | mydb | Query   |   15 | updating | UPDATE orders... |
+----+------+-----------+------+---------+------+----------+------------------+
Time=15表示该SQL已执行15秒
*/

-- 完整信息
SHOW FULL PROCESSLIST;

-- 杀掉长时间运行的查询
KILL 6;  -- 杀掉Id为6的连接

-- 查看锁等待
SELECT * FROM information_schema.processlist 
WHERE state LIKE '%lock%';
```

**使用 PERFORMANCE_SCHEMA 监控：**

```sql
-- 查看performance_schema可用表
SELECT * FROM performance_schema.setup_instruments 
WHERE name LIKE '%statement%';

-- 查看SQL执行统计
SELECT * FROM performance_schema.events_statements_summary_by_digest
ORDER BY SUM_TIMER_WAIT DESC
LIMIT 10;
-- 显示执行最频繁/最慢的SQL模式

-- 查看文件I/O统计
SELECT * FROM performance_schema.file_summary_by_instance
ORDER BY SUM_TIMER_READ DESC LIMIT 10;
```

**监控脚本示例：**

```sql
-- 创建监控查询
-- 1. 查看连接数
SELECT 
    '连接数' AS metric,
    VARIABLE_VALUE AS current_value,
    @@max_connections AS max_value,
    ROUND(VARIABLE_VALUE / @@max_connections * 100, 2) AS usage_pct
FROM information_schema.global_status 
WHERE VARIABLE_NAME = 'Threads_connected';

-- 2. 查看缓冲池命中率
SELECT 
    '缓冲池命中率' AS metric,
    CONCAT(ROUND(
        (1 - Innodb_buffer_pool_reads / Innodb_buffer_pool_read_requests) * 100, 2
    ), '%') AS value
FROM (
    SELECT 
        MAX(IF(VARIABLE_NAME='Innodb_buffer_pool_reads', VARIABLE_VALUE, 0)) AS Innodb_buffer_pool_reads,
        MAX(IF(VARIABLE_NAME='Innodb_buffer_pool_read_requests', VARIABLE_VALUE, 0)) AS Innodb_buffer_pool_read_requests
    FROM information_schema.global_status
    WHERE VARIABLE_NAME IN ('Innodb_buffer_pool_reads', 'Innodb_buffer_pool_read_requests')
) t;

-- 3. 查看表大小
SELECT 
    table_name,
    ROUND(data_length/1024/1024, 2) AS data_mb,
    ROUND(index_length/1024/1024, 2) AS index_mb,
    ROUND((data_length + index_length)/1024/1024, 2) AS total_mb,
    table_rows
FROM information_schema.tables
WHERE table_schema = 'mydb'
ORDER BY total_mb DESC;
```

### 总结

- 错误日志记录启动和运行错误，是排查故障的第一站
- 慢查询日志记录执行慢的 SQL，是性能优化的主要依据
- 二进制日志用于复制和时间点恢复，生产环境必须开启
- 查询日志记录所有 SQL，仅用于调试，生产环境慎用（影响性能）
- `SHOW PROCESSLIST` 查看当前连接，`KILL` 终止问题查询
- `SHOW GLOBAL STATUS` 查看服务器状态指标，如连接数、QPS、缓冲池命中率
- 缓冲池命中率应 > 99%，否则需要增加 `innodb_buffer_pool_size`
- `mysqldumpslow` 工具分析慢查询日志，找出最需要优化的 SQL
- 定期监控关键指标，设置告警阈值，及时发现异常
- PERFORMANCE_SCHEMA 提供详细的性能统计，用于深度分析

---

# 第9章：高级特性

## 第33讲：JSON 与全文搜索

### 概念

JSON（JavaScript Object Notation）是一种轻量级数据交换格式，MariaDB 从 10.2 开始原生支持 JSON 类型。JSON 类型允许在数据库中存储和查询半结构化数据。全文搜索（Full-Text Search）是一种基于分词的文本搜索技术，比 LIKE 模糊查询更高效、更智能，适合大文本字段的搜索场景。

### 原理

**JSON 存储：**
- MariaDB 的 JSON 类型内部以 LONGTEXT 存储，但会验证 JSON 格式
- JSON 值可以是对象 `{"key": "value"}`、数组 `[1, 2, 3]` 或标量值
- MariaDB 提供丰富的 JSON 函数进行查询和操作

**全文搜索原理：**
- 全文索引对文本进行分词，建立倒排索引（词 → 文档列表）
- 查询时通过词匹配，而非逐字符比较
- 支持自然语言模式和布尔模式
- MariaDB 支持中文分词（需配置 ngram 解析器）

**全文搜索 vs LIKE：**
- LIKE '%关键词%'：全表扫描，性能差，无法利用索引
- 全文搜索：利用倒排索引，速度快，支持相关性排序

### 例子

**JSON 数据操作：**

```sql
-- 创建包含JSON列的表
CREATE TABLE products (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    attributes JSON
);

-- 插入JSON数据
INSERT INTO products (id, name, attributes) VALUES
(1, '笔记本电脑', '{"brand": "ThinkPad", "cpu": "i7", "ram": 16, "storage": {"type": "SSD", "size": 512}, "tags": ["办公", "轻薄"]}'),
(2, '智能手机', '{"brand": "iPhone", "cpu": "A16", "ram": 6, "storage": {"type": "NVMe", "size": 256}, "tags": ["旗舰", "拍照"]}'),
(3, '平板电脑', '{"brand": "iPad", "cpu": "M2", "ram": 8, "storage": {"type": "SSD", "size": 128}, "tags": ["娱乐", "绘画"]}');

-- 查询JSON数据
SELECT 
    name,
    JSON_EXTRACT(attributes, '$.brand') AS brand,
    JSON_EXTRACT(attributes, '$.ram') AS ram,
    JSON_EXTRACT(attributes, '$.storage.size') AS storage_size
FROM products;

-- 使用 -> 简写（等价于 JSON_EXTRACT）
SELECT 
    name,
    attributes -> '$.brand' AS brand,
    attributes -> '$.storage.size' AS storage_size
FROM products;

-- 使用 ->> 简写（去除引号）
SELECT 
    name,
    attributes ->> '$.brand' AS brand  -- 返回 'ThinkPad' 而非 '"ThinkPad"'
FROM products;

-- 条件查询JSON
SELECT name FROM products 
WHERE attributes ->> '$.brand' = 'ThinkPad';

SELECT name FROM products 
WHERE JSON_EXTRACT(attributes, '$.ram') >= 8;

-- 查询JSON数组包含某元素
SELECT name FROM products 
WHERE JSON_CONTAINS(attributes -> '$.tags', '"办公"');

-- 查询JSON数组长度
SELECT name, JSON_LENGTH(attributes -> '$.tags') AS tag_count
FROM products;
```

**JSON 修改：**

```sql
-- 修改JSON字段（整体替换）
UPDATE products SET attributes = '{"brand": "ThinkPad", "cpu": "i9", "ram": 32}'
WHERE id = 1;

-- 修改JSON中的某个属性（MariaDB 10.9+）
UPDATE products 
SET attributes = JSON_SET(attributes, '$.ram', 32, '$.cpu', 'i9')
WHERE id = 1;

-- 添加新属性
UPDATE products 
SET attributes = JSON_SET(attributes, '$.weight', 1.5)
WHERE id = 1;

-- 删除属性
UPDATE products 
SET attributes = JSON_REMOVE(attributes, '$.tags')
WHERE id = 1;

-- 向数组追加元素
UPDATE products 
SET attributes = JSON_ARRAY_APPEND(attributes, '$.tags', '高性能')
WHERE id = 1;
```

**创建全文索引：**

```sql
-- 创建带全文索引的表
CREATE TABLE articles (
    id INT PRIMARY KEY,
    title VARCHAR(200),
    content TEXT,
    FULLTEXT INDEX ft_title_content (title, content)
) ENGINE=InnoDB;

-- 添加全文索引
ALTER TABLE articles ADD FULLTEXT INDEX ft_content (content);

-- 插入测试数据
INSERT INTO articles (id, title, content) VALUES
(1, 'MariaDB入门教程', 'MariaDB是MySQL的分支，完全开源，支持事务和JSON'),
(2, '数据库优化指南', '索引是数据库优化的关键，合理使用索引可以大幅提升查询性能'),
(3, 'SQL进阶技巧', '窗口函数和CTE是现代SQL的重要特性，简化复杂查询'),
(4, 'MariaDB高可用方案', 'Galera集群提供多主同步复制，实现高可用和负载均衡');
```

**全文搜索查询：**

```sql
-- 自然语言模式（默认）
SELECT id, title, 
       MATCH(title, content) AGAINST('MariaDB') AS relevance
FROM articles
WHERE MATCH(title, content) AGAINST('MariaDB')
ORDER BY relevance DESC;
-- relevance是相关性分数，越高越相关

-- 多词搜索（任意包含）
SELECT title FROM articles 
WHERE MATCH(title, content) AGAINST('MariaDB 索引');
-- 包含"MariaDB"或"索引"的文章

-- 布尔模式
SELECT title FROM articles 
WHERE MATCH(title, content) AGAINST('+MariaDB +优化' IN BOOLEAN MODE);
-- 必须同时包含"MariaDB"和"优化"

SELECT title FROM articles 
WHERE MATCH(title, content) AGAINST('MariaDB -MySQL' IN BOOLEAN MODE);
-- 包含"MariaDB"但不包含"MySQL"

SELECT title FROM articles 
WHERE MATCH(title, content) AGAINST('高*' IN BOOLEAN MODE);
-- 包含以"高"开头的词（如"高可用"）

-- 查询扩展模式（先找匹配，再找相关）
SELECT title FROM articles 
WHERE MATCH(title, content) AGAINST('数据库' WITH QUERY EXPANSION);
```

**中文全文搜索：**

```sql
-- 配置ngram解析器（my.cnf）
-- ngram_token_size = 2

-- 创建使用ngram的全文索引
CREATE TABLE articles_cn (
    id INT PRIMARY KEY,
    content TEXT,
    FULLTEXT INDEX ft_content (content) WITH PARSER ngram
) ENGINE=InnoDB;

-- 中文搜索
SELECT content FROM articles_cn 
WHERE MATCH(content) AGAINST('数据库优化' IN BOOLEAN MODE);
-- ngram将"数据库优化"分为"数据 据库 库优 优化"进行匹配
```

### 总结

- JSON 类型适合存储半结构化数据，如商品属性、配置信息、嵌套数据
- `JSON_EXTRACT` 或 `->` 提取 JSON 值，`->>` 提取并去除引号
- `JSON_SET` 修改/添加属性，`JSON_REMOVE` 删除属性，`JSON_ARRAY_APPEND` 追加数组元素
- 全文索引基于倒排索引，比 LIKE 模糊查询快得多
- `MATCH...AGAINST` 进行全文搜索，支持自然语言、布尔、查询扩展三种模式
- 布尔模式支持 `+`（必须包含）、`-`（不包含）、`*`（通配符）等操作符
- 中文全文搜索需配置 ngram 解析器，设置 `ngram_token_size`
- 全文索引适合大文本字段（如文章内容、商品描述），不适合短文本
- JSON 字段无法直接建索引，但可对 JSON 路径建虚拟列索引

---

## 第34讲：复制与高可用

### 概念

复制（Replication）是将主数据库的数据实时同步到一个或多个从数据库的技术。复制是实现高可用、读写分离、负载均衡的基础。MariaDB 支持多种复制方式，包括传统异步复制、半同步复制和 Galera 集群（多主同步复制）。高可用（High Availability）是指系统在故障时能快速切换，减少停机时间。

### 原理

**传统主从复制原理：**

1. **主库（Master）**：将数据变更写入二进制日志（binlog）
2. **从库（Slave）**：IO 线程拉取主库 binlog，写入中继日志（relay log）
3. **从库**：SQL 线程读取中继日志，重放 SQL 语句，更新数据

```
主库                    从库
┌──────────┐          ┌──────────────┐
│ 写操作    │          │ IO线程        │
│   ↓      │ binlog   │   ↓          │
│ binlog   │ ───────→ │ relay log    │
│          │          │   ↓          │
│          │          │ SQL线程       │
│          │          │   ↓          │
│          │          │ 数据更新       │
└──────────┘          └──────────────┘
```

**复制类型：**
- **异步复制**：主库不等待从库确认，性能好但可能丢数据
- **半同步复制**：主库等待至少一个从库确认收到 binlog，折中方案
- **全同步复制（Galera）**：所有节点同步提交，一致性最强但性能开销大

**复制用途：**
- 读写分离：主库写，从库读，分担负载
- 数据备份：从库作为热备份
- 故障切换：主库故障时提升从库为新主库
- 地理分布：异地数据中心数据同步

### 例子

**配置主库：**

```sql
-- 主库配置（my.cnf）
[mysqld]
server-id = 1
log_bin = mysql-bin
binlog_format = ROW
# 推荐ROW格式，记录行变更，一致性最好

-- 创建复制用户
CREATE USER 'repl'@'192.168.1.%' IDENTIFIED BY 'repl_password';
GRANT REPLICATION SLAVE ON *.* TO 'repl'@'192.168.1.%';
FLUSH PRIVILEGES;

-- 查看主库状态
SHOW MASTER STATUS;
/*
+------------------+----------+--------------+------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+------------------+----------+--------------+------------------+
| mysql-bin.000003 |     1234 |              |                  |
+------------------+----------+--------------+------------------+
记录 File 和 Position，配置从库时需要
*/
```

**配置从库：**

```sql
-- 从库配置（my.cnf）
[mysqld]
server-id = 2
relay_log = relay-bin
read_only = ON  -- 从库只读（防止误写）

-- 配置主库信息
CHANGE MASTER TO
    MASTER_HOST = '192.168.1.100',
    MASTER_PORT = 3306,
    MASTER_USER = 'repl',
    MASTER_PASSWORD = 'repl_password',
    MASTER_LOG_FILE = 'mysql-bin.000003',
    MASTER_LOG_POS = 1234;

-- 启动复制
START SLAVE;

-- 查看复制状态
SHOW SLAVE STATUS\G
/*
关键字段：
Slave_IO_Running: Yes    -- IO线程正常运行
Slave_SQL_Running: Yes   -- SQL线程正常运行
Seconds_Behind_Master: 0 -- 落后主库的秒数（0表示同步）
Last_IO_Error:           -- IO错误信息
Last_SQL_Error:          -- SQL错误信息
*/

-- 停止复制
STOP SLAVE;

-- 重置复制
RESET SLAVE ALL;
```

**读写分离配置：**

```sql
-- 应用层读写分离（伪代码）
-- 写操作 → 主库
INSERT INTO orders ...;
UPDATE products ...;

-- 读操作 → 从库
SELECT * FROM products WHERE ...;
SELECT COUNT(*) FROM orders;

-- 使用代理中间件（如ProxySQL、MaxScale）自动路由
-- 应用只需连接代理，代理根据SQL类型路由到主从
```

**Galera 集群（多主同步复制）：**

```sql
-- Galera集群配置（my.cnf）
[mysqld]
binlog_format = ROW
default_storage_engine = InnoDB
innodb_autoinc_lock_mode = 2

# Galera配置
wsrep_on = ON
wsrep_provider = /usr/lib/galera/libgalera_smm.so
wsrep_cluster_address = gcomm://192.168.1.1,192.168.1.2,192.168.1.3
wsrep_cluster_name = my_cluster
wsrep_node_address = 192.168.1.1
wsrep_node_name = node1
wsrep_sst_method = mariabackup

-- 查看集群状态
SHOW STATUS LIKE 'wsrep_cluster_size';
-- 集群节点数

SHOW STATUS LIKE 'wsrep_cluster_status';
-- Primary: 正常  Non-Primary: 脑裂

SHOW STATUS LIKE 'wsrep_local_state_comment';
-- Synced: 已同步

-- Galera特点：
-- 1. 多主架构：任何节点都可读写
-- 2. 同步复制：数据实时一致
-- 3. 自动成员管理：节点故障自动剔除
-- 4. 无需手动故障切换
```

**故障切换：**

```bash
# 主库故障时，将从库提升为主库

# 1. 确认主库已故障
mysqladmin -h old_master -u root ping

# 2. 在从库上停止复制
mysql -u root -p -e "STOP SLAVE; RESET SLAVE ALL;"

# 3. 取消只读限制
mysql -u root -p -e "SET GLOBAL read_only = OFF;"

# 4. 修改应用配置，指向新主库

# 5. 使用MHA或Orchestrator等工具实现自动故障切换
```

### 总结

- 主从复制通过 binlog 实现数据同步：主库写 binlog → 从库拉取并重放
- 异步复制性能好但可能丢数据，半同步复制折中，Galera 全同步一致性最强
- `SHOW MASTER STATUS` 查看主库 binlog 位置，`SHOW SLAVE STATUS` 查看从库复制状态
- `Slave_IO_Running` 和 `Slave_SQL_Running` 都为 Yes 才表示复制正常
- `Seconds_Behind_Master` 表示从库落后主库的秒数，应接近 0
- 读写分离：主库处理写操作，从库分担读操作，提升整体性能
- Galera 集群提供多主同步复制，自动故障切换，适合高可用场景
- 生产环境建议至少 3 节点 Galera 集群，或 1 主 2 从 + 自动切换工具
- 复制不是备份，误操作（如 DROP TABLE）会同步到从库
- 定期演练故障切换流程，确保故障时能快速恢复

---

## 第35讲：MariaDB 特有特性

### 概念

MariaDB 作为 MySQL 的分支，在兼容 MySQL 的基础上发展了许多独特特性。这些特性使 MariaDB 在性能、功能和易用性方面具有独特优势。本讲介绍 MariaDB 的特有特性，包括 ColumnStore 列式存储引擎、CONNECT 引擎、SEQUENCE 引擎、动态列、RETURNING 语法等。

### 原理

**MariaDB 特有特性概览：**

1. **ColumnStore**：列式存储引擎，适合 OLAP 分析查询，对大数据集的聚合查询性能优异
2. **CONNECT 引擎**：可以访问外部数据源（CSV、JSON、ODBC 等），像访问本地表一样
3. **SEQUENCE 引擎**：生成序列号，无需创建实际表
4. **动态列**：在单列中存储多个键值对，类似简化版 JSON
5. **RETURNING 语法**：DELETE/UPDATE 返回被修改的数据
6. **EXCEPT / INTERSECT**：集合操作，MySQL 原生不支持
7. **系统版本表（System-Versioned Tables）**：自动记录数据历史版本
8. **FLASHBACK**：利用系统版本表实现数据回溯

### 例子

**ColumnStore 列式存储：**

```sql
-- ColumnStore适合OLAP分析场景
-- 创建ColumnStore表
CREATE TABLE sales_records (
    id INT,
    sale_date DATE,
    product_id INT,
    quantity INT,
    amount DECIMAL(10,2),
    region VARCHAR(50)
) ENGINE=ColumnStore;

-- 列式存储优势：
-- 1. 只读取查询涉及的列，减少I/O
-- 2. 高压缩比，节省存储空间
-- 3. 聚合查询性能优异

-- 分析查询（ColumnStore优势场景）
SELECT 
    region,
    product_id,
    SUM(quantity) AS total_quantity,
    SUM(amount) AS total_amount,
    AVG(amount) AS avg_amount
FROM sales_records
WHERE sale_date BETWEEN '2024-01-01' AND '2024-03-31'
GROUP BY region, product_id
ORDER BY total_amount DESC;
-- 在ColumnStore上比InnoDB快几十倍
```

**SEQUENCE 引擎：**

```sql
-- 生成序列号（无需创建表）
SELECT * FROM seq_1_to_10;
/*
+-----+
| seq |
+-----+
|   1 |
|   2 |
| ...
|  10 |
+-----+
*/

-- 生成步长序列
SELECT * FROM seq_1_to_100_step_5;
-- 1, 6, 11, 16, ...

-- 实际应用：生成日期序列
SELECT 
    DATE_ADD('2024-01-01', INTERVAL seq DAY) AS date
FROM seq_0_to_364;
-- 生成2024年所有日期

-- 补全缺失数据
SELECT 
    d.date,
    COALESCE(s.amount, 0) AS amount
FROM (
    SELECT DATE_ADD('2024-01-01', INTERVAL seq DAY) AS date
    FROM seq_0_to_30
) d
LEFT JOIN daily_sales s ON d.date = s.sale_date
ORDER BY d.date;
```

**RETURNING 语法：**

```sql
-- DELETE RETURNING：返回被删除的数据
DELETE FROM products WHERE stock = 0 
RETURNING id, name, price;
/*
+----+-----------+--------+
| id | name      | price  |
+----+-----------+--------+
|  5 | 已下架商品 | 199.00 |
|  8 | 缺货商品   | 599.00 |
+----+-----------+--------+
*/

-- UPDATE RETURNING：返回更新后的数据
UPDATE products SET price = price * 0.9 WHERE category_id = 1
RETURNING id, name, price;
/*
+----+-----------+--------+
| id | name      | price  |
+----+-----------+--------+
|  1 | 笔记本电脑 | 8099.10|
|  2 | 智能手机   | 5399.10|
+----+-----------+--------+
*/

-- INSERT RETURNING：返回插入的数据
INSERT INTO users (username, email) VALUES 
('user1', 'u1@b.com'),
('user2', 'u2@b.com')
RETURNING id, username, created_at;
```

**系统版本表（Time Travel）：**

```sql
-- 创建系统版本表
CREATE TABLE products_history (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    price DECIMAL(10,2),
    stock INT,
    sys_start TIMESTAMP(6) GENERATED ALWAYS AS ROW START,
    sys_end TIMESTAMP(6) GENERATED ALWAYS AS ROW END,
    PERIOD FOR SYSTEM_TIME (sys_start, sys_end)
) WITH SYSTEM VERSIONING;

-- 插入和修改数据
INSERT INTO products_history (id, name, price, stock) VALUES (1, '商品A', 100, 50);
UPDATE products_history SET price = 120 WHERE id = 1;
UPDATE products_history SET price = 150 WHERE id = 1;

-- 查询当前数据
SELECT * FROM products_history;
-- id=1, price=150（最新值）

-- 查询历史数据（指定时间点）
SELECT * FROM products_history 
FOR SYSTEM_TIME AS OF '2024-01-15 10:00:00';
-- 返回该时间点的数据

-- 查询所有历史版本
SELECT * FROM products_history FOR SYSTEM_TIME ALL;
/*
+----+--------+--------+-------+---------------------+---------------------+
| id | name   | price  | stock | sys_start           | sys_end             |
+----+--------+--------+-------+---------------------+---------------------+
|  1 | 商品A  | 100.00 |    50 | 2024-01-15 09:00:00 | 2024-01-15 09:30:00 |
|  1 | 商品A  | 120.00 |    50 | 2024-01-15 09:30:00 | 2024-01-15 10:00:00 |
|  1 | 商品A  | 150.00 |    50 | 2024-01-15 10:00:00 | 9999-12-31 23:59:59 |
+----+--------+--------+-------+---------------------+---------------------+
*/

-- 查询时间范围内的历史
SELECT * FROM products_history 
FOR SYSTEM_TIME FROM '2024-01-15 09:00:00' TO '2024-01-15 10:00:00';
```

**EXCEPT 和 INTERSECT：**

```sql
-- EXCEPT：差集（在A不在B）
SELECT product_id FROM order_items 
EXCEPT
SELECT product_id FROM refund_items;
-- 有订单但无退款的产品

-- INTERSECT：交集（同时在A和B）
SELECT product_id FROM order_items 
INTERSECT
SELECT product_id FROM refund_items;
-- 既有订单又有退款的产品

-- 注意：MySQL不原生支持这两个操作，MariaDB独有
```

**动态列：**

```sql
-- 动态列：在单列存储键值对
CREATE TABLE products_dynamic (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    attrs BLOB  -- 存储动态列
);

-- 插入动态列数据
INSERT INTO products_dynamic VALUES 
(1, '笔记本电脑', COLUMN_CREATE('color', '黑色', 'weight', 1.5, 'warranty', 3)),
(2, '手机', COLUMN_CREATE('color', '白色', 'weight', 0.2, '5g', true));

-- 查询动态列
SELECT 
    name,
    COLUMN_GET(attrs, 'color' AS CHAR) AS color,
    COLUMN_GET(attrs, 'weight' AS DECIMAL(5,2)) AS weight
FROM products_dynamic;

-- 添加动态列
UPDATE products_dynamic 
SET attrs = COLUMN_ADD(attrs, 'brand', 'ThinkPad')
WHERE id = 1;

-- 删除动态列
UPDATE products_dynamic 
SET attrs = COLUMN_DELETE(attrs, 'warranty')
WHERE id = 1;

-- 列出所有动态列名
SELECT name, COLUMN_LIST(attrs) FROM products_dynamic;
```

**CONNECT 引擎（访问外部数据）：**

```sql
-- 访问CSV文件
CREATE TABLE csv_data (
    id INT,
    name VARCHAR(100),
    value DECIMAL(10,2)
) ENGINE=CONNECT TABLE_TYPE=CSV 
FILE_NAME='/data/products.csv';

-- 查询CSV文件像普通表一样
SELECT * FROM csv_data WHERE value > 100;

-- 访问远程MySQL表
CREATE TABLE remote_users (
    id INT,
    username VARCHAR(50)
) ENGINE=CONNECT TABLE_TYPE=MYSQL
CONNECTION='host=192.168.1.100;user=remote;password=pass;database=mydb';

-- 访问JSON文件
CREATE TABLE json_data (
    id INT,
    name VARCHAR(100)
) ENGINE=CONNECT TABLE_TYPE=JSON
FILE_NAME='/data/products.json';
```

### 总结

- ColumnStore 是列式存储引擎，适合 OLAP 分析查询，聚合性能优异
- SEQUENCE 引擎生成序列号，常用于补全缺失数据、生成日期序列
- `RETURNING` 语法让 DELETE/UPDATE/INSERT 返回操作的数据，减少额外查询
- 系统版本表自动记录数据历史，支持时间旅行查询，适合审计和数据回溯
- `EXCEPT` 和 `INTERSECT` 是集合操作，MySQL 不支持，是 MariaDB 独有特性
- 动态列在单列存储键值对，是 JSON 的轻量替代方案
- CONNECT 引擎可以访问 CSV、JSON、远程数据库等外部数据源
- MariaDB 在兼容 MySQL 的基础上提供了更多功能和更好的性能
- 选择 MariaDB 的理由：完全开源、社区活跃、特性丰富、性能优化
- 这些特有特性使 MariaDB 在某些场景下比 MySQL 更有优势

---

## 课程总结

恭喜您完成了 MariaDB 系统教程的学习！通过 35 讲的系统学习，您已经掌握了：

**基础知识**：MariaDB 的安装配置、数据类型、SQL 基本语法
**查询技能**：多表 JOIN、子查询、UNION、视图、窗口函数
**性能优化**：索引设计、EXPLAIN 分析、查询优化技巧
**高级编程**：存储过程、函数、触发器、游标
**事务管理**：ACID、隔离级别、锁机制、存储引擎
**运维管理**：用户权限、备份恢复、日志监控
**高级特性**：JSON、全文搜索、复制高可用、MariaDB 特有功能

**持续学习建议**：
1. 多动手实践，在实际项目中应用所学知识
2. 关注 MariaDB 官方文档和版本更新
3. 参与社区讨论，学习他人经验
4. 定期复习，巩固重点知识
5. 深入研究感兴趣的高级主题，如性能调优、高可用架构

数据库技术是后端开发和运维的核心技能，掌握 MariaDB 将为您的职业发展打下坚实基础。祝您在数据库的学习和使用中不断进步！
