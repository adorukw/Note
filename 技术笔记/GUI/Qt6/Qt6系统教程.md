# Qt6 系统教程

> 本教程以教科书形式，分 30 讲系统讲解 Qt6 框架。每讲包含「概念 → 原理 → 例子 → 总结」四个部分，从基础到实战，循序渐进。

---

## 课程总览

- **预计讲数**：30 讲（9 章）
- **学习目标**：从零基础掌握 Qt6 框架的核心机制、GUI 开发、模型/视图架构、图形绘图、多线程、网络编程及 QML，具备独立开发完整 Qt6 桌面应用的能力
- **适用人群**：有 C++ 基础，希望学习跨平台 GUI 开发的开发者
- **渐进结构**：基础入门 → 核心机制 → 窗口控件 → 事件系统 → 模型视图 → 图形绘图 → 多线程 → 网络数据 → 现代 Qt 实战

### 详细章节目录

#### 第1章 Qt6 入门
- 第1讲 Qt6 简介与环境搭建
- 第2讲 第一个 Qt6 程序
- 第3讲 构建系统：CMake
- 第4讲 Qt 核心模块概览

#### 第2章 Qt 核心机制
- 第5讲 QObject 与对象模型
- 第6讲 信号与槽机制
- 第7讲 父子对象与内存管理
- 第8讲 元对象系统与属性系统

#### 第3章 窗口与控件
- 第9讲 QWidget 与窗口类型
- 第10讲 QMainWindow 主窗口
- 第11讲 常用控件
- 第12讲 布局管理

#### 第4章 事件系统
- 第13讲 事件循环与事件处理
- 第14讲 鼠标键盘事件与事件过滤器
- 第15讲 定时器 QTimer

#### 第5章 模型/视图架构
- 第16讲 Model/View 架构概览
- 第17讲 自定义模型
- 第18讲 代理 Delegate

#### 第6章 图形与绘图
- 第19讲 QPainter 基础
- 第20讲 QGraphicsView 框架
- 第21讲 自定义控件

#### 第7章 多线程与并发
- 第22讲 QThread 基础
- 第23讲 QtConcurrent 与 QFuture
- 第24讲 线程同步

#### 第8章 网络、文件与数据
- 第25讲 网络编程
- 第26讲 文件 I/O 与配置
- 第27讲 JSON/XML/SQLite

#### 第9章 现代 Qt 与实战
- 第28讲 QML 与 Qt Quick 入门
- 第29讲 Qt6 新特性与最佳实践
- 第30讲 项目实战：完整应用开发

---

## 第1章 Qt6 入门

### 第1讲 Qt6 简介与环境搭建

#### 概念

Qt6 是由 Qt Company 开发的跨平台 C++ 应用程序开发框架，于 2020 年 12 月正式发布。它不仅是一个 GUI 库，更是一个完整的应用开发平台，涵盖了图形界面、网络通信、数据库访问、多线程、文件处理、多媒体等几乎所有桌面应用开发所需的功能模块。Qt6 建立在 Qt5 的坚实基础之上，引入了对 C++17 的强制要求，全面采用了 CMake 构建系统，并对底层图形架构进行了重大重构。

Qt6 的核心定位是"一次编写，到处运行"。开发者只需编写一套代码，即可将应用部署到 Windows、macOS、Linux、Android、iOS 甚至嵌入式设备上。这种跨平台能力源于 Qt 对各平台底层 API 的封装——Qt 在每个平台都提供了对应的底层实现，但对上层暴露统一的 C++ 接口。

#### 原理

Qt6 的跨平台能力建立在其分层架构之上。最底层是平台抽象层，负责封装各操作系统的原生 API；中间层是 Qt 核心模块，提供事件循环、对象模型、信号槽等基础设施；最上层是各种功能模块，如 Qt Widgets（传统桌面 GUI）、Qt Quick/QML（声明式 UI）、Qt Network、Qt SQL 等。

Qt6 相比 Qt5 的主要改进包括：强制要求 C++17 标准，使代码更现代、更安全；全面转向 CMake 构建系统，弃用了 qmake；引入了新的图形架构 RHI（Rendering Hardware Interface），使 Qt 能够直接使用 Vulkan、Metal、Direct3D 等底层图形 API；QML 引擎进行了重写，性能大幅提升。

#### 例子

**安装 Qt6（Ubuntu/Debian）：**

```bash
# 安装 Qt6 开发包和 CMake
sudo apt update
sudo apt install qt6-base-dev cmake build-essential

# 验证安装
qmake6 --version
cmake --version
```

**安装 Qt6（Windows/macOS，推荐使用官方 Qt 在线安装器）：**

从 https://www.qt.io/download 下载安装器，选择 Qt 6.x.x 版本，勾选对应平台的开发组件（如 MinGW 或 MSVC 编译器、Qt 6.x.x 库、Qt Creator IDE）。

**最小 CMake 项目验证：**

创建 `CMakeLists.txt`：

```cmake
cmake_minimum_required(VERSION 3.16)
project(Qt6Test VERSION 1.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(Qt6 REQUIRED COMPONENTS Core)

qt_standard_project_setup()
qt_add_executable(qt6test main.cpp)
```

创建 `main.cpp`：

```cpp
#include <QCoreApplication>
#include <QDebug>

int main(int argc, char *argv[]) {
    QCoreApplication app(argc, argv);
    qDebug() << "Qt version:" << QT_VERSION_STR;
    return 0;
}
```

编译运行：

```bash
cmake -B build -S .
cmake --build build
./build/qt6test
# 输出: Qt version: 6.x.x
```

#### 总结

- Qt6 是跨平台 C++ 应用开发框架，覆盖 GUI、网络、数据库等全栈功能
- Qt6 强制 C++17，采用 CMake 构建系统，引入 RHI 图形架构
- Linux 可通过包管理器安装，Windows/macOS 推荐使用官方安装器
- 开发前务必验证 `qmake6 --version` 和 `cmake --version` 确认环境就绪
- 常见问题：找不到 Qt6 模块通常是 `find_package` 路径未配置，需设置 `CMAKE_PREFIX_PATH` 指向 Qt 安装目录

---

### 第2讲 第一个 Qt6 程序

#### 概念

本讲通过编写第一个 Qt6 图形界面程序，建立对 Qt 应用程序结构的直观认识。我们将创建一个带按钮的窗口，点击按钮时在标签上显示文字。这个简单的例子涵盖了 Qt GUI 程序的三大要素：应用程序对象（QApplication）、窗口部件（QWidget）、事件处理（信号与槽）。

#### 原理

每一个 Qt GUI 程序都必须有且仅有一个 QApplication 对象，它负责管理应用程序级别的资源、初始化窗口系统、处理命令行参数，并启动事件循环。事件循环是一个无限循环，它不断从系统消息队列中取出事件（鼠标点击、键盘输入、定时器超时、网络响应等），分发给对应的窗口部件处理。只有当事件循环运行时，窗口才能响应用户操作。

QWidget 是所有可见 UI 元素的基类。一个 QWidget 可以作为顶层窗口独立显示，也可以作为子部件嵌入到其他 QWidget 中。Qt 采用父子对象树管理内存——当父对象销毁时，所有子对象自动销毁。这种机制使开发者无需手动 delete 子部件。

#### 例子

```cpp
#include <QApplication>
#include <QWidget>
#include <QPushButton>
#include <QLabel>
#include <QVBoxLayout>

int main(int argc, char *argv[]) {
    // 1. 创建应用程序对象（每个 GUI 程序必须有且仅有一个）
    QApplication app(argc, argv);

    // 2. 创建主窗口
    QWidget window;
    window.setWindowTitle("我的第一个 Qt6 程序");
    window.resize(400, 200);

    // 3. 创建子部件
    QPushButton *button = new QPushButton("点击我", &window);
    QLabel *label = new QLabel("等待点击...", &window);

    // 4. 使用布局管理子部件的位置
    QVBoxLayout *layout = new QVBoxLayout(&window);
    layout->addWidget(label);
    layout->addWidget(button);

    // 5. 连接信号与槽：按钮点击 → 修改标签文字
    QObject::connect(button, &QPushButton::clicked, [&label]() {
        label->setText("你好，Qt6！");
    });

    // 6. 显示窗口并启动事件循环
    window.show();
    return app.exec();  // 进入事件循环，直到窗口关闭
}
```

**CMakeLists.txt：**

```cmake
cmake_minimum_required(VERSION 3.16)
project(FirstQtApp LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(Qt6 REQUIRED COMPONENTS Widgets)
qt_standard_project_setup()

qt_add_executable(firstqt main.cpp)
target_link_libraries(firstqt PRIVATE Qt6::Widgets)
```

编译运行后，将看到一个带按钮和标签的窗口。点击按钮，标签文字变为"你好，Qt6！"。

#### 总结

- 每个 Qt GUI 程序必须有且仅有一个 QApplication 对象，它启动事件循环
- QWidget 是所有可见 UI 元素的基类，可作为窗口或子部件
- 信号与槽是 Qt 事件处理的核心机制，`connect()` 连接发送者和接收者
- 父子对象树自动管理内存，子部件指定 parent 后无需手动 delete
- `app.exec()` 启动事件循环，程序在此阻塞直到窗口关闭
- 常见问题：忘记调用 `window.show()` 会导致窗口不显示；忘记 `app.exec()` 程序会立即退出

---

### 第3讲 构建系统：CMake

#### 概念

Qt6 全面采用 CMake 作为官方构建系统，弃用了 Qt5 时代的 qmake。CMake 是一个跨平台的构建系统生成器，它本身不直接编译代码，而是根据 `CMakeLists.txt` 配置文件生成对应平台的构建文件（如 Linux 的 Makefile、Windows 的 Visual Studio 工程、macOS 的 Xcode 工程），再由这些原生工具完成实际编译。

#### 原理

CMake 的工作流程分为两个阶段：配置阶段和生成阶段。配置阶段，CMake 读取 `CMakeLists.txt`，执行其中的命令，解析依赖关系，生成 `CMakeCache.txt` 缓存文件。生成阶段，CMake 根据配置结果生成具体平台的构建文件。

Qt6 为 CMake 提供了一系列专用命令：`find_package(Qt6 ...)` 查找 Qt6 安装；`qt_standard_project_setup()` 设置 Qt 项目标准配置（如自动处理 MOC、UIC、RCC）；`qt_add_executable()` 创建可执行文件（替代标准的 `add_executable`，自动处理 Qt 特有的资源编译）；`qt_add_resources()` 添加 Qt 资源文件。

#### 例子

**完整的多文件项目结构：**

```
MyApp/
├── CMakeLists.txt
├── main.cpp
├── mainwindow.h
├── mainwindow.cpp
└── resources.qrc
```

**CMakeLists.txt：**

```cmake
cmake_minimum_required(VERSION 3.16)
project(MyApp VERSION 1.0 LANGUAGES CXX)

# 设置 C++ 标准
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 查找 Qt6 模块
find_package(Qt6 REQUIRED COMPONENTS Widgets Network Sql)

# Qt 项目标准设置（自动启用 MOC/UIC/RCC）
qt_standard_project_setup()

# 创建可执行文件
qt_add_executable(MyApp
    main.cpp
    mainwindow.cpp
    mainwindow.h
)

# 链接 Qt 库
target_link_libraries(MyApp PRIVATE
    Qt6::Widgets
    Qt6::Network
    Qt6::Sql
)

# 添加 Qt 资源文件
qt_add_resources(MyApp "app_resources"
    PREFIX "/"
    FILES resources.qrc
)

# Windows 平台设置：自动复制依赖 DLL
if(WIN32)
    set_target_properties(MyApp PROPERTIES WIN32_EXECUTABLE TRUE)
endif()

# macOS 平台设置：生成 .app 包
if(APPLE)
    set_target_properties(MyApp PROPERTIES MACOSX_BUNDLE TRUE)
endif()
```

**构建命令：**

```bash
# 配置（生成构建文件到 build 目录）
cmake -B build -S . -DCMAKE_PREFIX_PATH=/path/to/qt6

# 编译
cmake --build build

# 运行
./build/MyApp
```

#### 总结

- Qt6 全面采用 CMake，弃用 qmake，CMakeLists.txt 是项目配置核心
- `find_package(Qt6 COMPONENTS ...)` 查找所需 Qt 模块
- `qt_add_executable` 替代 `add_executable`，自动处理 MOC/UIC/RCC
- `qt_standard_project_setup()` 一键启用 Qt 标准配置
- 构建分两步：`cmake -B build -S .`（配置）+ `cmake --build build`（编译）
- 常见问题：找不到 Qt6 时需通过 `CMAKE_PREFIX_PATH` 指定 Qt 安装路径

---

### 第4讲 Qt 核心模块概览

#### 概念

Qt6 由数十个模块组成，每个模块提供特定领域的功能。模块分为两类：Qt 基础模块（Essential Modules）和 Qt 附加模块（Add-on Modules）。基础模块是 Qt 的核心，在所有支持平台上都可用且源码兼容；附加模块提供特定领域功能，可能仅在某些平台可用。

掌握 Qt 模块体系，有助于在开发时快速定位所需功能，避免重复造轮子。例如需要 HTTP 请求就用 Qt Network，需要数据库就用 Qt SQL，需要多媒体就用 Qt Multimedia。

#### 原理

Qt 模块化设计遵循"按需引入"原则。每个模块对应一个独立的动态链接库（如 `libQt6Widgets.so`、`libQt6Network.so`），在 CMake 中通过 `find_package(Qt6 COMPONENTS Widgets Network)` 声明依赖，编译时链接对应的库。这种设计使应用只引入真正需要的模块，减小体积。

Qt6 的基础模块包括：Qt Core（核心非 GUI 功能，如事件循环、文件、线程、JSON）、Qt GUI（基础 GUI，如窗口、图像、字体、OpenGL）、Qt Widgets（传统桌面控件）、Qt Network（网络通信）、Qt SQL（数据库）、Qt QML（QML 引擎）、Qt Quick（Qt Quick 运行时）、Qt Quick Controls（QML 控件）、Qt Multimedia（音视频）、Qt Concurrent（并发编程）等。

#### 例子

**各模块功能速查表：**

| 模块 | 功能 | 典型类 |
|------|------|--------|
| Qt Core | 核心非 GUI 功能 | QObject, QString, QFile, QTimer, QThread |
| Qt GUI | 基础 GUI | QWindow, QImage, QFont, QPainter, QColor |
| Qt Widgets | 桌面控件 | QWidget, QPushButton, QLabel, QMainWindow |
| Qt Network | 网络通信 | QTcpSocket, QNetworkAccessManager |
| Qt SQL | 数据库 | QSqlDatabase, QSqlQuery, QSqlTableModel |
| Qt QML | QML 引擎 | QQmlEngine, QQmlContext |
| Qt Quick | Qt Quick 运行时 | QQuickView, QQuickItem |
| Qt Multimedia | 音视频 | QMediaPlayer, QAudioOutput, QCamera |
| Qt Concurrent | 并发 | QtConcurrent, QFuture, QThreadPool |
| Qt Print Support | 打印 | QPrinter, QPrintDialog |
| Qt SVG | SVG 渲染 | QSvgRenderer, QSvgWidget |
| Qt Charts | 图表 | QChart, QChartView, QLineSeries |

**多模块组合示例：**

```cmake
# 一个需要 GUI + 网络 + 数据库的应用
find_package(Qt6 REQUIRED COMPONENTS Widgets Network Sql Charts)
```

```cpp
// 同时使用多个模块的示例
#include <QApplication>      // Qt Widgets
#include <QNetworkAccessManager>  // Qt Network
#include <QSqlDatabase>     // Qt SQL
#include <QChartView>       // Qt Charts

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    QNetworkAccessManager manager;       // 网络请求
    QSqlDatabase db = QSqlDatabase::addDatabase("QSQLITE");  // 数据库
    QChartView chartView;                 // 图表显示

    // ... 各模块协同工作
    return app.exec();
}
```

#### 总结

- Qt6 采用模块化设计，基础模块全平台可用，附加模块按需引入
- Qt Core 是所有模块的基础，提供事件循环、对象模型、字符串、文件等
- Qt Widgets 用于传统桌面 GUI，Qt Quick/QML 用于现代声明式 UI
- 在 CMake 中通过 `find_package(Qt6 COMPONENTS ...)` 声明模块依赖
- 常见问题：编译报"未定义引用"通常是忘记在 CMake 中链接对应 Qt 模块

---

## 第2章 Qt 核心机制

### 第5讲 QObject 与对象模型

#### 概念

QObject 是 Qt 框架的核心基类，几乎所有 Qt 类都直接或间接继承自它。QObject 实现了 Qt 的对象模型，提供对象树管理、信号与槽、事件处理、属性系统、定时器、对象名称等核心功能。理解 QObject 是理解整个 Qt 框架运作机制的基础。

#### 原理

Qt 对象模型的核心是"对象树"（Object Tree）机制。每个 QObject 可以有一个父对象和多个子对象，形成树状结构。当父对象被销毁时，其所有子对象会自动被销毁。这种机制使开发者无需手动管理子对象的生命周期，有效避免了内存泄漏。

QObject 的另一个核心特性是信号与槽机制，它建立在元对象系统（Meta-Object System）之上。元对象系统通过 MOC（Meta-Object Compiler）在编译前预处理头文件，为每个 QObject 子类生成额外的元信息代码，包括类名、信号列表、槽函数列表、属性列表等。这些元信息使 Qt 能够在运行时进行动态类型识别、信号槽连接、属性读写等操作。

要使用元对象系统，类必须满足三个条件：继承 QObject、在类声明中使用 `Q_OBJECT` 宏、由 MOC 处理（CMake 中 `qt_standard_project_setup()` 自动处理）。

#### 例子

```cpp
#include <QObject>
#include <QDebug>

// 自定义 QObject 子类
class Student : public QObject {
    Q_OBJECT  // 必须放在类声明开头，启用元对象系统
public:
    // parent 参数是 QObject 的关键，用于构建对象树
    explicit Student(const QString &name, QObject *parent = nullptr)
        : QObject(parent), m_name(name) {}

    QString name() const { return m_name; }

private:
    QString m_name;
};

class Classroom : public QObject {
    Q_OBJECT
public:
    explicit Classroom(QObject *parent = nullptr) : QObject(parent) {}

    void addStudent(const QString &name) {
        // 创建子对象时传入 this 作为 parent
        // 当 Classroom 销毁时，所有 Student 自动销毁
        Student *s = new Student(name, this);
        m_students.append(s);
        qDebug() << "添加学生:" << s->name()
                 << "父对象:" << s->parent()->metaObject()->className();
    }

private:
    QList<Student*> m_students;
};

int main() {
    // 顶层对象，无 parent
    Classroom *room = new Classroom();

    room->addStudent("张三");
    room->addStudent("李四");
    room->addStudent("王五");

    // 只需 delete 顶层对象，所有子对象自动销毁
    delete room;
    // 此时所有 Student 对象都已被释放，无需手动 delete
    return 0;
}

#include "main.moc"  // 内联编译时需要包含 MOC 生成的代码
```

#### 总结

- QObject 是 Qt 的核心基类，提供对象树、信号槽、事件处理等机制
- 对象树机制：父对象销毁时自动销毁所有子对象，简化内存管理
- `Q_OBJECT` 宏启用元对象系统，必须放在类声明的 private 区域
- 创建子对象时传入 `parent` 参数将其加入对象树
- 常见问题：忘记 `Q_OBJECT` 宏会导致信号槽无法使用；栈上创建有 parent 的对象会导致重复析构

---

### 第6讲 信号与槽机制

#### 概念

信号与槽（Signals and Slots）是 Qt 独创的事件通信机制，用于对象间的松耦合通信。当某个对象的状态发生变化时，它会发出（emit）一个信号；连接到该信号的槽函数会被自动调用。这种机制取代了传统的回调函数，使代码更清晰、更安全、更灵活。

#### 原理

信号与槽的本质是"观察者模式"的实现。信号是对象状态变化的通知，槽是响应信号的函数。一个信号可以连接多个槽，一个槽也可以接收多个信号。信号发出时，所有连接的槽都会被调用。

Qt 的信号槽连接有五种方式，由 `Qt::ConnectionType` 指定：
- `Qt::DirectConnection`：直接调用，在信号发出者的线程中同步执行槽函数
- `Qt::QueuedConnection`：队列连接，将槽调用事件投递到接收者线程的事件队列，异步执行
- `Qt::AutoConnection`（默认）：自动选择，同线程用 Direct，跨线程用 Queued
- `Qt::BlockingQueuedConnection`：阻塞队列连接，信号发出者阻塞等待槽函数执行完毕（不能同线程使用）
- `Qt::UniqueConnection`：唯一连接，防止重复连接同一信号槽对

Qt5 引入的函数指针式连接语法 `connect(sender, &Sender::signal, receiver, &Receiver::slot)` 相比旧的字符串语法 `SIGNAL()`/`SLOT()` 更安全——编译期即可检查信号槽是否存在、参数是否匹配。

#### 例子

```cpp
#include <QObject>
#include <QDebug>

// 信号发射者
class Counter : public QObject {
    Q_OBJECT
public:
    explicit Counter(int value = 0, QObject *parent = nullptr)
        : QObject(parent), m_value(value) {}

    int value() const { return m_value; }

    // 信号声明：只需声明，无需实现
signals:
    void valueChanged(int newValue);

public slots:
    void setValue(int value) {
        if (m_value == value) return;  // 值未变化，不发射信号
        m_value = value;
        emit valueChanged(m_value);  // 发射信号
    }

private:
    int m_value;
};

// 信号接收者
class Observer : public QObject {
    Q_OBJECT
public slots:
    void onValueChanged(int newValue) {
        qDebug() << "值已更新为:" << newValue;
    }
};

int main() {
    Counter counter(0);
    Observer observer;

    // 方式1：函数指针式连接（推荐，编译期检查）
    QObject::connect(&counter, &Counter::valueChanged,
                     &observer, &Observer::onValueChanged);

    // 方式2：连接到 Lambda（无需定义槽函数）
    QObject::connect(&counter, &Counter::valueChanged, [](int v) {
        qDebug() << "Lambda 收到:" << v;
    });

    // 方式3：一个信号连接多个槽
    QObject::connect(&counter, &Counter::valueChanged, [](int v) {
        qDebug() << "第二个槽也收到:" << v;
    });

    counter.setValue(10);  // 触发信号，三个槽都会被调用
    counter.setValue(10);  // 值未变，不触发信号
    counter.setValue(20);  // 再次触发

    return 0;
}

#include "main.moc"
```

**输出：**
```
值已更新为: 10
Lambda 收到: 10
第二个槽也收到: 10
值已更新为: 20
Lambda 收到: 20
第二个槽也收到: 20
```

#### 总结

- 信号与槽是 Qt 的事件通信机制，实现对象间松耦合
- 信号用 `signals:` 声明，用 `emit` 发射；槽用 `public slots:` / `private slots:` 声明
- 推荐使用函数指针式 `connect()`，编译期检查类型安全
- 一个信号可连接多个槽，一个槽可接收多个信号
- 连接类型默认 `AutoConnection`，自动处理跨线程通信
- 常见问题：信号槽参数必须匹配（槽参数 ≤ 信号参数）；忘记 `emit` 信号不会发射

---

### 第7讲 父子对象与内存管理

#### 概念

Qt 的内存管理基于父子对象树（Parent-Child Object Tree）机制。每个 QObject 可以拥有一个父对象和多个子对象，形成树状结构。当父对象被销毁时，其所有子对象会被自动递归销毁。这种机制使开发者只需管理顶层对象的生命周期，子对象由框架自动管理，大幅降低了内存泄漏的风险。

#### 原理

父子对象树的实现原理：QObject 内部维护一个子对象列表 `QObjectList`，当通过 `setParent()` 或构造函数指定 parent 时，对象被添加到父对象的子列表中。父对象析构时，遍历子列表并逐个 `delete` 子对象，子对象的析构又会递归销毁其子对象。

这种机制的关键约束是：**子对象必须在堆上创建（使用 new）**。如果子对象在栈上创建，当父对象析构时尝试 `delete` 栈对象会导致未定义行为（double free 或崩溃）。

另一个重要规则是：**子对象应先于父对象创建完毕**。如果在父对象已经销毁后再操作子对象，会访问已释放内存。Qt 通过 `deleteLater()` 延迟删除机制缓解部分问题——`deleteLater()` 将删除操作投递到事件队列，在当前事件处理完成后执行。

#### 例子

```cpp
#include <QObject>
#include <QDebug>
#include <QTimer>

class Widget : public QObject {
    Q_OBJECT
public:
    explicit Widget(const QString &name, QObject *parent = nullptr)
        : QObject(parent), m_name(name) {
        qDebug() << m_name << "已创建";
    }
    ~Widget() override {
        qDebug() << m_name << "已销毁";
    }
    QString name() const { return m_name; }
private:
    QString m_name;
};

int main() {
    qDebug() << "=== 场景1：正确的堆上创建 ===";
    {
        Widget *root = new Widget("Root");
        Widget *child1 = new Widget("Child1", root);   // parent = root
        Widget *child2 = new Widget("Child2", root);    // parent = root
        Widget *grandchild = new Widget("Grandchild", child1);  // parent = child1

        // 只需 delete root，child1、child2、grandchild 自动销毁
        delete root;
        // 输出顺序：Root 已销毁 → Child1 已销毁 → Grandchild 已销毁 → Child2 已销毁
    }

    qDebug() << "\n=== 场景2：使用 deleteLater 延迟删除 ===";
    {
        Widget *w = new Widget("Delayed");
        w->deleteLater();  // 不会立即删除，等事件循环执行
        qDebug() << "deleteLater 已调用，但对象仍存在";
        // 实际删除发生在事件循环中
    }

    qDebug() << "\n=== 场景3：动态切换 parent ===";
    {
        Widget *parent1 = new Widget("Parent1");
        Widget *parent2 = new Widget("Parent2");
        Widget *child = new Widget("Child", parent1);  // 初始 parent1

        qDebug() << "Child 的 parent:" << child->parent()->objectName();
        child->setParent(parent2);  // 切换到 parent2
        qDebug() << "切换后 Child 的 parent:" << child->parent()->objectName();

        delete parent1;  // Child 不会被销毁，因为它已不属于 parent1
        delete parent2;  // Child 随 parent2 销毁
    }

    return 0;
}

#include "main.moc"
```

#### 总结

- 父子对象树是 Qt 内存管理的核心，父对象销毁时自动销毁所有子对象
- 子对象必须在堆上 `new` 创建，不能在栈上创建（否则父对象 delete 时崩溃）
- `setParent()` 可动态切换父对象，子对象会从旧父对象的子列表移除
- `deleteLater()` 延迟删除，将删除操作投递到事件队列，避免在信号槽处理中直接 delete
- 常见问题：栈对象设置 parent 会导致 double free；循环引用（A 的 parent 是 B，B 的 parent 是 A）会导致内存泄漏

---

### 第8讲 元对象系统与属性系统

#### 概念

元对象系统（Meta-Object System）是 Qt 对标准 C++ 的扩展，它在 C++ 之上提供了运行时类型信息（RTTI）、动态属性系统、信号槽机制等能力。元对象系统通过 MOC（Meta-Object Compiler）在编译前预处理头文件，为每个包含 `Q_OBJECT` 宏的类生成额外的元信息代码。

属性系统（Property System）建立在元对象系统之上，允许为 QObject 子类声明动态属性，这些属性可以在运行时通过字符串名称读写，支持 QML 绑定、Qt 设计师表单编辑、动画系统等高级功能。

#### 原理

MOC 的工作流程：读取包含 `Q_OBJECT` 宏的头文件 → 解析类声明，提取信号、槽、属性信息 → 生成一个 `moc_xxx.cpp` 文件，包含该类的元对象数据（`staticMetaObject`）→ 这个生成的 cpp 文件与原代码一起编译。

`staticMetaObject` 是每个 QObject 子类都有的静态成员，包含类名、信号列表、槽列表、属性列表等信息。通过 `metaObject()` 方法可在运行时获取对象的元对象，进而调用 `className()`、`property()`、`setProperty()` 等方法。

属性声明使用 `Q_PROPERTY` 宏，它定义属性的名称、类型、读取函数、写入函数、通知信号等。声明后，该属性可通过 `QObject::property("name")` 和 `setProperty("name", value)` 动态访问，也可在 QML 中直接使用。

#### 例子

```cpp
#include <QObject>
#include <QDebug>
#include <QMetaProperty>

class Person : public QObject {
    Q_OBJECT

    // 声明属性：名称、类型、读取函数、写入函数、通知信号
    Q_PROPERTY(QString name READ name WRITE setName NOTIFY nameChanged)
    Q_PROPERTY(int age READ age WRITE setAge NOTIFY ageChanged)

public:
    explicit Person(QObject *parent = nullptr) : QObject(parent), m_age(0) {}

    // 读取函数
    QString name() const { return m_name; }
    int age() const { return m_age; }

    // 写入函数
    void setName(const QString &name) {
        if (m_name != name) {
            m_name = name;
            emit nameChanged(m_name);
        }
    }
    void setAge(int age) {
        if (m_age != age) {
            m_age = age;
            emit ageChanged(m_age);
        }
    }

signals:
    void nameChanged(const QString &name);
    void ageChanged(int age);

private:
    QString m_name;
    int m_age;
};

int main() {
    Person person;

    // 1. 通过元对象获取类信息
    const QMetaObject *meta = person.metaObject();
    qDebug() << "类名:" << meta->className();
    qDebug() << "属性数量:" << meta->propertyCount();
    for (int i = 0; i < meta->propertyCount(); ++i) {
        QMetaProperty prop = meta->property(i);
        qDebug() << "  属性:" << prop.name() << "类型:" << prop.typeName();
    }

    // 2. 通过字符串名称动态读写属性
    person.setProperty("name", "张三");
    person.setProperty("age", 25);
    qDebug() << "动态读取 - 姓名:" << person.property("name")
             << "年龄:" << person.property("age");

    // 3. 动态属性（运行时添加，非 Q_PROPERTY 声明）
    person.setProperty("extraTag", "VIP用户");
    qDebug() << "动态属性 extraTag:" << person.property("extraTag");

    // 4. 运行时类型识别
    QObject *obj = &person;
    qDebug() << "obj 是否为 Person:" << obj->inherits("Person");
    qDebug() << "obj 是否为 QObject:" << obj->inherits("QObject");

    return 0;
}

#include "main.moc"
```

**输出：**
```
类名: Person
属性数量: 4   // name, age + QObject 的 objectName, 2个继承属性
  属性: objectName 类型: QString
  属性: name 类型: QString
  属性: age 类型: int
动态读取 - 姓名: "张三" 年龄: 25
动态属性 extraTag: "VIP用户"
obj 是否为 Person: true
obj 是否为 QObject: true
```

#### 总结

- 元对象系统通过 MOC 预处理为每个 QObject 子类生成 `staticMetaObject` 元数据
- `Q_OBJECT` 宏是启用元对象系统的开关，必须放在类声明中
- `Q_PROPERTY` 声明属性，支持通过字符串名称动态读写
- `metaObject()` 获取运行时元对象，支持类名查询、属性遍历、类型识别
- `inherits()` 判断对象是否继承自某个类（比 `dynamic_cast` 更灵活）
- 常见问题：忘记 `Q_OBJECT` 宏会导致 `metaObject()` 返回父类的元对象；`Q_PROPERTY` 的 READ/WRITE 函数签名必须严格匹配

---

## 第3章 窗口与控件

### 第9讲 QWidget 与窗口类型

#### 概念

QWidget 是 Qt 中所有用户界面元素的基类，无论是顶层窗口还是按钮、标签等子控件，都直接或间接继承自 QWidget。QWidget 提供了窗口的基本能力：显示、隐藏、移动、调整大小、接收输入事件、绘制自身等。理解 QWidget 的窗口属性和控件属性，是构建复杂界面的基础。

#### 原理

QWidget 有两种存在形态：作为顶层窗口和作为子控件。当 QWidget 没有 parent（或 parent 为 nullptr）时，它是一个顶层窗口，拥有自己的窗口边框和标题栏，由窗口管理器管理。当 QWidget 有 parent 时，它是一个子控件，显示在父控件内部，没有独立边框。

QWidget 通过一系列 `Qt::WindowFlags` 控制窗口的外观和行为。常见的窗口标志包括：`Qt::Widget`（默认，子控件或对话框）、`Qt::Window`（顶层窗口，有边框和标题栏）、`Qt::Dialog`（对话框）、`Qt::FramelessWindowHint`（无边框窗口）、`Qt::WindowStaysOnTopHint`（窗口置顶）、`Qt::Tool`（工具窗口，通常较小且无任务栏图标）。

QWidget 的绘制机制基于 `paintEvent()` 事件。当控件需要重绘时（首次显示、被遮挡后重新可见、调用 `update()`），Qt 会触发 `paintEvent()`，开发者在该函数中使用 `QPainter` 绘制内容。

#### 例子

```cpp
#include <QApplication>
#include <QWidget>
#include <QPushButton>
#include <QDebug>

// 自定义无边框窗口
class FramelessWindow : public QWidget {
    Q_OBJECT
public:
    explicit FramelessWindow(QWidget *parent = nullptr) : QWidget(parent) {
        // 设置窗口标志：无边框 + 置顶
        setWindowFlags(Qt::FramelessWindowHint | Qt::WindowStaysOnTopHint);
        // 启用鼠标追踪
        setMouseTracking(true);
        // 设置半透明背景
        setAttribute(Qt::WA_TranslucentBackground);
        resize(300, 200);
    }

protected:
    void paintEvent(QPaintEvent *) override {
        QPainter painter(this);
        painter.setRenderHint(QPainter::Antialiasing);
        // 绘制圆角矩形背景
        QColor bgColor(50, 150, 250, 200);  // 半透明蓝色
        painter.setBrush(bgColor);
        painter.setPen(Qt::NoPen);
        painter.drawRoundedRect(rect(), 15, 15);
    }

private:
    QPoint m_dragPos;
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    // 1. 标准顶层窗口
    QWidget mainWindow;
    mainWindow.setWindowTitle("标准窗口");
    mainWindow.resize(400, 300);

    // 2. 对话框窗口
    QWidget dialog(&mainWindow, Qt::Dialog);
    dialog.setWindowTitle("对话框");
    dialog.resize(200, 150);

    // 3. 无边框置顶窗口
    FramelessWindow frameless;
    frameless.setWindowTitle("无边框窗口");

    mainWindow.show();
    dialog.show();
    frameless.show();

    return app.exec();
}

#include "main.moc"
```

#### 总结

- QWidget 是所有 UI 元素的基类，可作顶层窗口或子控件
- 无 parent 的 QWidget 是顶层窗口，有 parent 的是子控件
- `Qt::WindowFlags` 控制窗口类型：Window、Dialog、FramelessWindowHint 等
- `paintEvent()` 是自定义绘制的入口，使用 `QPainter` 绘制内容
- `setAttribute(Qt::WA_TranslucentBackground)` 启用半透明背景
- 常见问题：无边框窗口无法拖动需自行实现鼠标拖动逻辑；子控件设置 WindowFlags 可能无效

---

### 第10讲 QMainWindow 主窗口

#### 概念

QMainWindow 是 Qt 为桌面应用程序预定义的主窗口类，它提供了标准桌面应用的布局结构：菜单栏（Menu Bar）、工具栏（Tool Bars）、状态栏（Status Bar）、停靠区域（Dock Widgets）和中央控件（Central Widget）。大多数桌面应用的主窗口都应继承 QMainWindow，而非直接使用 QWidget。

#### 原理

QMainWindow 的布局是固定的，由框架管理，开发者不能直接设置它的布局管理器。QMainWindow 内部将窗口区域划分为：顶部菜单栏、菜单栏下方的工具栏区域（可上下左右四个方向）、底部状态栏、四周的停靠区域、中央主区域。中央区域通过 `setCentralWidget()` 设置唯一的主控件，通常是用户内容的主要展示区。

菜单栏 `QMenuBar` 包含多个 `QMenu`，每个 QMenu 包含多个 `QAction`（动作）。QAction 是 Qt 的抽象动作概念，它独立于具体的菜单或工具栏——同一个 QAction 可以同时添加到菜单和工具栏，实现"一次定义，多处使用"。

工具栏 `QToolBar` 是可拖动的按钮容器，可停靠在窗口四周或浮动。状态栏 `QStatusBar` 显示临时或永久的状态信息。停靠窗口 `QDockWidget` 是可隐藏、可拖出的面板，常用于侧边栏。

#### 例子

```cpp
#include <QMainWindow>
#include <QMenuBar>
#include <QToolBar>
#include <QStatusBar>
#include <QAction>
#include <QTextEdit>
#include <QDockWidget>
#include <QApplication>
#include <QFileDialog>
#include <QMessageBox>
#include <QDebug>

class MainWindow : public QMainWindow {
    Q_OBJECT
public:
    explicit MainWindow(QWidget *parent = nullptr) : QMainWindow(parent) {
        setupUI();
        setupMenu();
        setupToolbar();
        setupStatusBar();
        setupDockWidgets();
    }

private:
    QTextEdit *m_textEdit;

    void setupUI() {
        m_textEdit = new QTextEdit(this);
        setCentralWidget(m_textEdit);
        setWindowTitle("文本编辑器");
        resize(800, 600);
    }

    void setupMenu() {
        QMenu *fileMenu = menuBar()->addMenu("文件(&F)");
        QMenu *editMenu = menuBar()->addMenu("编辑(&E)");
        QMenu *helpMenu = menuBar()->addMenu("帮助(&H)");

        // 创建动作（可复用于菜单和工具栏）
        QAction *newAction = new QAction("新建", this);
        newAction->setShortcut(QKeySequence::New);
        newAction->setStatusTip("创建新文件");

        QAction *openAction = new QAction("打开", this);
        openAction->setShortcut(QKeySequence::Open);
        openAction->setStatusTip("打开现有文件");

        QAction *saveAction = new QAction("保存", this);
        saveAction->setShortcut(QKeySequence::Save);

        QAction *exitAction = new QAction("退出", this);
        exitAction->setShortcut(QKeySequence::Quit);

        fileMenu->addAction(newAction);
        fileMenu->addAction(openAction);
        fileMenu->addAction(saveAction);
        fileMenu->addSeparator();
        fileMenu->addAction(exitAction);

        // 连接动作信号
        connect(newAction, &QAction::triggered, this, [this]() {
            m_textEdit->clear();
            statusBar()->showMessage("已新建文件", 2000);
        });
        connect(openAction, &QAction::triggered, this, [this]() {
            QString fileName = QFileDialog::getOpenFileName(this, "打开文件");
            if (!fileName.isEmpty()) {
                statusBar()->showMessage("已打开: " + fileName, 3000);
            }
        });
        connect(exitAction, &QAction::triggered, qApp, &QApplication::quit);
    }

    void setupToolbar() {
        QToolBar *toolbar = addToolBar("主工具栏");
        toolbar->setMovable(true);  // 允许拖动

        // 复用菜单中的动作
        toolbar->addAction("新建");
        toolbar->addAction("打开");
        toolbar->addAction("保存");
        toolbar->addSeparator();
        toolbar->addAction("打印");
    }

    void setupStatusBar() {
        statusBar()->showMessage("就绪");
        // 添加永久状态信息
        QLabel *permanentInfo = new QLabel("行: 1, 列: 1");
        statusBar()->addPermanentWidget(permanentInfo);
    }

    void setupDockWidgets() {
        QDockWidget *dock = new QDockWidget("文件列表", this);
        dock->setAllowedAreas(Qt::LeftDockWidgetArea | Qt::RightDockWidgetArea);
        QListWidget *list = new QListWidget(dock);
        list->addItems({"文件1.txt", "文件2.txt", "文件3.txt"});
        dock->setWidget(list);
        addDockWidget(Qt::LeftDockWidgetArea, dock);
    }
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);
    MainWindow w;
    w.show();
    return app.exec();
}

#include "main.moc"
```

#### 总结

- QMainWindow 提供标准桌面应用布局：菜单栏、工具栏、状态栏、停靠区、中央控件
- 中央控件通过 `setCentralWidget()` 设置，是唯一的主内容区
- QAction 是抽象动作，可同时添加到菜单和工具栏，实现复用
- 工具栏可拖动停靠，状态栏分临时消息和永久信息两部分
- QDockWidget 可拖出浮动，常用于侧边栏面板
- 常见问题：不要对 QMainWindow 直接 `setLayout()`，应使用 `setCentralWidget()`

---

### 第11讲 常用控件

#### 概念

Qt Widgets 模块提供了丰富的预定义控件，涵盖按钮、输入框、显示控件、容器等常见 UI 元素。掌握这些控件的 API 和适用场景，能快速构建功能完整的界面。本讲介绍最常用的几类控件：按钮类（QPushButton、QCheckBox、QRadioButton）、输入类（QLineEdit、QTextEdit、QSpinBox、QComboBox）、显示类（QLabel、QProgressBar）。

#### 原理

Qt 控件的设计遵循"信号驱动"模式：每个控件在状态变化时发出特定信号，开发者连接信号到槽函数处理。例如 QPushButton 点击发出 `clicked()`，QLineEdit 文本变化发出 `textChanged()`，QComboBox 选择变化发出 `currentIndexChanged()`。

控件的数据交互通过 getter/setter 方法：`text()`/`setText()` 读写文本，`value()`/`setValue()` 读写数值，`isChecked()`/`setChecked()` 读写选中状态。这种统一的命名规范使 API 学习成本降低。

QComboBox 是组合框，结合了按钮和弹出列表。它可以通过 `addItem()` 添加项，也可以通过 `setModel()` 绑定数据模型（后续 Model/View 章节详述）。QSpinBox 和 QDoubleSpinBox 是数值输入框，支持上下箭头增减和范围限制。

#### 例子

```cpp
#include <QApplication>
#include <QWidget>
#include <QVBoxLayout>
#include <QFormLayout>
#include <QGroupBox>
#include <QPushButton>
#include <QCheckBox>
#include <QRadioButton>
#include <QButtonGroup>
#include <QLineEdit>
#include <QTextEdit>
#include <QSpinBox>
#include <QDoubleSpinBox>
#include <QComboBox>
#include <QSlider>
#include <QProgressBar>
#include <QLabel>
#include <QDebug>

class FormDemo : public QWidget {
    Q_OBJECT
public:
    explicit FormDemo(QWidget *parent = nullptr) : QWidget(parent) {
        setupUI();
        setWindowTitle("常用控件演示");
        resize(500, 600);
    }

private:
    QLabel *m_resultLabel;
    QProgressBar *m_progressBar;

    void setupUI() {
        QVBoxLayout *mainLayout = new QVBoxLayout(this);

        // 1. 按钮组
        QGroupBox *buttonGroup = createButtonGroup();
        mainLayout->addWidget(buttonGroup);

        // 2. 输入控件组
        QGroupBox *inputGroup = createInputGroup();
        mainLayout->addWidget(inputGroup);

        // 3. 滑块与进度条
        QGroupBox *sliderGroup = createSliderGroup();
        mainLayout->addWidget(sliderGroup);

        // 4. 结果显示
        m_resultLabel = new QLabel("结果将显示在这里", this);
        m_resultLabel->setStyleSheet("background: #f0f0f0; padding: 10px; border: 1px solid #ccc;");
        mainLayout->addWidget(m_resultLabel);

        // 提交按钮
        QPushButton *submitBtn = new QPushButton("提交", this);
        connect(submitBtn, &QPushButton::clicked, this, &FormDemo::onSubmit);
        mainLayout->addWidget(submitBtn);
    }

    QGroupBox* createButtonGroup() {
        QGroupBox *group = new QGroupBox("按钮控件", this);
        QVBoxLayout *layout = new QVBoxLayout(group);

        // 复选框
        QCheckBox *check1 = new QCheckBox("选项A", group);
        QCheckBox *check2 = new QCheckBox("选项B", group);
        check1->setChecked(true);

        // 单选按钮（需用 QButtonGroup 分组）
        QRadioButton *radio1 = new QRadioButton("男", group);
        QRadioButton *radio2 = new QRadioButton("女", group);
        QButtonGroup *bgGroup = new QButtonGroup(group);
        bgGroup->addButton(radio1, 1);
        bgGroup->addButton(radio2, 2);
        radio1->setChecked(true);

        layout->addWidget(check1);
        layout->addWidget(check2);
        layout->addWidget(radio1);
        layout->addWidget(radio2);
        return group;
    }

    QGroupBox* createInputGroup() {
        QGroupBox *group = new QGroupBox("输入控件", this);
        QFormLayout *layout = new QFormLayout(group);

        QLineEdit *nameEdit = new QLineEdit(group);
        nameEdit->setPlaceholderText("请输入姓名");

        QSpinBox *ageSpin = new QSpinBox(group);
        ageSpin->setRange(0, 150);
        ageSpin->setValue(25);

        QDoubleSpinBox *heightSpin = new QDoubleSpinBox(group);
        heightSpin->setRange(0.0, 3.0);
        heightSpin->setSingleStep(0.01);
        heightSpin->setValue(1.75);
        heightSpin->setSuffix(" 米");

        QComboBox *cityCombo = new QComboBox(group);
        cityCombo->addItems({"北京", "上海", "广州", "深圳"});

        QTextEdit *descEdit = new QTextEdit(group);
        descEdit->setMaximumHeight(80);
        descEdit->setPlaceholderText("个人简介...");

        layout->addRow("姓名:", nameEdit);
        layout->addRow("年龄:", ageSpin);
        layout->addRow("身高:", heightSpin);
        layout->addRow("城市:", cityCombo);
        layout->addRow("简介:", descEdit);
        return group;
    }

    QGroupBox* createSliderGroup() {
        QGroupBox *group = new QGroupBox("滑块与进度", this);
        QVBoxLayout *layout = new QVBoxLayout(group);

        QSlider *slider = new QSlider(Qt::Horizontal, group);
        slider->setRange(0, 100);
        slider->setValue(50);

        m_progressBar = new QProgressBar(group);
        m_progressBar->setRange(0, 100);
        m_progressBar->setValue(50);

        connect(slider, &QSlider::valueChanged, m_progressBar, &QProgressBar::setValue);

        layout->addWidget(new QLabel("拖动滑块:"));
        layout->addWidget(slider);
        layout->addWidget(m_progressBar);
        return group;
    }

private slots:
    void onSubmit() {
        m_resultLabel->setText("表单已提交！进度: " +
            QString::number(m_progressBar->value()) + "%");
    }
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);
    FormDemo w;
    w.show();
    return app.exec();
}

#include "main.moc"
```

#### 总结

- 按钮类：QPushButton（普通按钮）、QCheckBox（多选）、QRadioButton（单选，需 QButtonGroup 分组）
- 输入类：QLineEdit（单行文本）、QTextEdit（多行文本）、QSpinBox（整数）、QDoubleSpinBox（小数）、QComboBox（下拉选择）
- 显示类：QLabel（标签）、QProgressBar（进度条）
- 控件 API 遵循统一规范：`text()`/`setText()`、`value()`/`setValue()`、`isChecked()`/`setChecked()`
- QFormLayout 适合表单类界面，自动对齐标签和输入框
- 常见问题：QRadioButton 不分组会全部互斥；QComboBox 的 `currentIndexChanged` 有 int 和 QString 两个重载，连接时需用 `qOverload<int>`

---

### 第12讲 布局管理

#### 概念

布局管理器（Layout Manager）是 Qt 自动管理控件位置和大小的机制。它根据窗口大小变化自动调整内部控件的几何位置，使界面在不同分辨率和窗口尺寸下都保持美观。Qt 提供四种基本布局：QHBoxLayout（水平）、QVBoxLayout（垂直）、QGridLayout（网格）、QFormLayout（表单），以及 QStackedLayout（堆叠）。

#### 原理

布局管理器的核心是"约束求解"。每个布局根据其类型计算子控件的位置和大小：水平布局将控件从左到右排列，垂直布局从上到下，网格布局按行列排列，表单布局按标签-字段对排列。布局会考虑每个控件的 `sizeHint()`（建议大小）、`sizePolicy()`（大小策略）、`stretch`（拉伸因子）等因素综合计算。

QSizePolicy 定义控件在布局中的拉伸行为：`Fixed`（固定大小）、`Minimum`（最小值不可压缩）、`Maximum`（最大值不可扩展）、`Preferred`（偏好大小）、`Expanding`（可扩展填充空间）。通过设置控件的 sizePolicy 和布局的 stretch 因子，可以精确控制空间分配比例。

布局可以嵌套——一个布局可以包含子布局，形成复杂的界面结构。`addWidget()` 添加控件，`addLayout()` 添加子布局，`addStretch()` 添加弹性空间。

#### 例子

```cpp
#include <QApplication>
#include <QWidget>
#include <QHBoxLayout>
#include <QVBoxLayout>
#include <QGridLayout>
#include <QFormLayout>
#include <QStackedLayout>
#include <QPushButton>
#include <QLabel>
#include <QLineEdit>
#include <QComboBox>
#include <QGroupBox>
#include <QSpinBox>

// 综合布局演示
class LayoutDemo : public QWidget {
    Q_OBJECT
public:
    explicit LayoutDemo(QWidget *parent = nullptr) : QWidget(parent) {
        // 主布局：垂直
        QVBoxLayout *mainLayout = new QVBoxLayout(this);

        // 1. 顶部水平布局：按钮 + 弹性空间
        QHBoxLayout *topLayout = new QHBoxLayout();
        topLayout->addWidget(new QPushButton("按钮1"));
        topLayout->addWidget(new QPushButton("按钮2"));
        topLayout->addStretch();  // 弹性空间，将按钮推到左边
        topLayout->addWidget(new QPushButton("按钮3"));
        mainLayout->addLayout(topLayout);

        // 2. 中间网格布局：计算器风格
        QGridLayout *gridLayout = new QGridLayout();
        QLineEdit *display = new QLineEdit("0");
        display->setAlignment(Qt::AlignRight);
        gridLayout->addWidget(display, 0, 0, 1, 4);  // 跨4列

        QString buttons[4][4] = {
            {"7", "8", "9", "/"},
            {"4", "5", "6", "*"},
            {"1", "2", "3", "-"},
            {"0", ".", "=", "+"}
        };
        for (int row = 0; row < 4; ++row) {
            for (int col = 0; col < 4; ++col) {
                gridLayout->addWidget(new QPushButton(buttons[row][col]),
                                      row + 1, col);
            }
        }
        // 设置列的拉伸因子，使各列等宽
        for (int i = 0; i < 4; ++i)
            gridLayout->setColumnStretch(i, 1);
        mainLayout->addLayout(gridLayout);

        // 3. 表单布局：标签-字段对
        QFormLayout *formLayout = new QFormLayout();
        formLayout->addRow("姓名:", new QLineEdit());
        formLayout->addRow("年龄:", new QSpinBox());
        formLayout->addRow("城市:", new QComboBox());
        QGroupBox *formGroup = new QGroupBox("用户信息");
        formGroup->setLayout(formLayout);
        mainLayout->addWidget(formGroup);

        // 4. 底部：设置拉伸因子控制空间分配
        mainLayout->addStretch(1);  // 顶部弹性小
        // gridLayout 默认占主要空间
        mainLayout->setStretch(1, 3);  // 网格布局占3份

        setWindowTitle("布局管理演示");
        resize(400, 500);
    }
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);
    LayoutDemo w;
    w.show();
    return app.exec();
}

#include "main.moc"
```

#### 总结

- 四种基本布局：QHBoxLayout（水平）、QVBoxLayout（垂直）、QGridLayout（网格）、QFormLayout（表单）
- `addStretch()` 添加弹性空间，`addSpacing()` 添加固定间距
- `setStretch(index, factor)` 设置控件/子布局的拉伸因子，控制空间分配比例
- 网格布局用 `addWidget(widget, row, col, rowSpan, colSpan)` 支持跨行跨列
- 布局可嵌套，通过 `addLayout()` 组合形成复杂界面
- 常见问题：不要手动 `setGeometry()` 管理位置，应交给布局；QGroupBox 设置布局用 `setLayout()` 而非构造函数

---

## 第4章 事件系统

### 第13讲 事件循环与事件处理

#### 概念

事件循环（Event Loop）是 Qt 应用程序的心脏，它是一个无限循环，不断从系统消息队列中取出事件并分发给对应的对象处理。事件包括鼠标点击、键盘按键、窗口重绘、定时器超时、网络响应等。理解事件循环的工作原理，是掌握 Qt 异步编程、避免界面卡顿的关键。

#### 原理

事件循环的核心是 `QCoreApplication::exec()`，它启动一个无限循环。每次循环，事件循环从队列取出一个事件，调用 `QCoreApplication::notify(receiver, event)` 将事件分发给目标对象。分发过程是：调用接收对象的 `event()` 方法 → `event()` 根据事件类型调用具体的 `xxxEvent()` 处理函数（如 `mousePressEvent()`、`keyPressEvent()`、`paintEvent()`）。

事件处理是同步的——在当前事件处理完成前，事件循环不会处理下一个事件。这意味着如果某个事件处理函数执行耗时操作（如网络请求、大量计算），整个界面会卡住无响应。解决方法是：将耗时操作放到子线程，或使用 `QCoreApplication::processEvents()` 临时让事件循环处理积压事件（不推荐，治标不治本）。

事件可以通过 `QCoreApplication::postEvent()` 异步投递（事件进入队列，稍后处理）或 `QCoreApplication::sendEvent()` 同步发送（立即处理，不经过队列）。

#### 例子

```cpp
#include <QApplication>
#include <QWidget>
#include <QKeyEvent>
#include <QMouseEvent>
#include <QPaintEvent>
#include <QPainter>
#include <QLabel>
#include <QVBoxLayout>
#include <QDebug>

class EventWidget : public QWidget {
    Q_OBJECT
public:
    explicit EventWidget(QWidget *parent = nullptr) : QWidget(parent) {
        setWindowTitle("事件处理演示");
        resize(400, 300);
        m_label = new QLabel("在此区域操作鼠标和键盘", this);
        m_label->setAlignment(Qt::AlignCenter);
        QVBoxLayout *layout = new QVBoxLayout(this);
        layout->addWidget(m_label);
    }

protected:
    // 重写 event() 方法：事件分发的总入口
    bool event(QEvent *event) override {
        if (event->type() == QEvent::KeyPress) {
            QKeyEvent *keyEvent = static_cast<QKeyEvent*>(event);
            if (keyEvent->key() == Qt::Key_Escape) {
                qDebug() << "ESC 按下，准备退出";
                close();
                return true;  // 事件已处理，不再传递
            }
        }
        // 其他事件交给父类处理
        return QWidget::event(event);
    }

    // 键盘按下事件
    void keyPressEvent(QKeyEvent *event) override {
        QString keyText = QKeySequence(event->key()).toString();
        QString modifiers;
        if (event->modifiers() & Qt::ControlModifier) modifiers += "Ctrl+";
        if (event->modifiers() & Qt::ShiftModifier) modifiers += "Shift+";
        if (event->modifiers() & Qt::AltModifier) modifiers += "Alt+";

        m_label->setText(QString("按键: %1%2").arg(modifiers, keyText));
        qDebug() << "按键:" << modifiers + keyText;
    }

    // 鼠标按下事件
    void mousePressEvent(QMouseEvent *event) override {
        if (event->button() == Qt::LeftButton) {
            m_label->setText(QString("左键点击: (%1, %2)")
                .arg(event->position().x()).arg(event->position().y()));
        } else if (event->button() == Qt::RightButton) {
            m_label->setText("右键点击");
        }
    }

    // 鼠标移动事件（需启用 mouseTracking 才能在未按键时触发）
    void mouseMoveEvent(QMouseEvent *event) override {
        qDebug() << "鼠标移动到:" << event->position();
    }

    // 绘制事件
    void paintEvent(QPaintEvent *event) override {
        Q_UNUSED(event);
        QPainter painter(this);
        painter.fillRect(rect(), QColor(240, 240, 250));
    }

private:
    QLabel *m_label;
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);
    EventWidget w;
    w.setMouseTracking(true);  // 启用鼠标追踪
    w.show();
    return app.exec();
}

#include "main.moc"
```

#### 总结

- 事件循环是 Qt 应用核心，`exec()` 启动无限循环分发事件
- 事件分发链：`notify()` → `event()` → 具体 `xxxEvent()` 处理函数
- 事件处理是同步的，耗时操作会阻塞界面，应放到子线程
- `postEvent()` 异步投递，`sendEvent()` 同步发送
- `event()` 返回 true 表示事件已处理，不再传递；返回 false 交给父类
- 常见问题：界面卡顿通常是事件处理中执行了耗时操作；`mouseMoveEvent` 默认需按键才触发，需 `setMouseTracking(true)`

---

### 第14讲 鼠标键盘事件与事件过滤器

#### 概念

鼠标和键盘是用户与桌面应用交互的主要方式。Qt 提供了完善的事件处理机制来响应这些输入。本讲深入讲解鼠标事件（按下、释放、移动、双击、滚轮）和键盘事件（按键、释放、组合键）的处理，以及事件过滤器（Event Filter）机制——它允许在一个对象中拦截并处理其他对象的事件。

#### 原理

鼠标事件通过 `QMouseEvent` 传递，包含按键状态（左键/右键/中键）、修饰键（Ctrl/Shift/Alt）、位置坐标等信息。`mousePressEvent`、`mouseReleaseEvent`、`mouseMoveEvent`、`mouseDoubleClickEvent`、`wheelEvent` 分别对应不同操作。默认情况下，`mouseMoveEvent` 只在按键按下时触发，需调用 `setMouseTracking(true)` 才能在未按键时触发。

键盘事件通过 `QKeyEvent` 传递，包含按键代码（`key()`）、文本内容（`text()`）、修饰键状态（`modifiers()`）。`keyPressEvent` 和 `keyReleaseEvent` 分别对应按下和释放。长按按键会重复触发 `keyPressEvent`，可通过 `event->isAutoRepeat()` 判断是否为自动重复。

事件过滤器是一种"全局拦截"机制。通过 `installEventFilter()` 给目标对象安装过滤器，过滤器对象的 `eventFilter()` 方法会在目标对象处理事件之前被调用。如果 `eventFilter()` 返回 true，事件被拦截，目标对象不会收到该事件。这种机制常用于全局快捷键、日志记录、统一事件处理等场景。

#### 例子

```cpp
#include <QApplication>
#include <QWidget>
#include <QLabel>
#include <QLineEdit>
#include <QPushButton>
#include <QVBoxLayout>
#include <QKeyEvent>
#include <QMouseEvent>
#include <QWheelEvent>
#include <QDebug>

// 事件过滤器：拦截所有 QLineEdit 的回车键
class EnterKeyFilter : public QObject {
    Q_OBJECT
protected:
    bool eventFilter(QObject *watched, QEvent *event) override {
        if (event->type() == QEvent::KeyPress) {
            QKeyEvent *keyEvent = static_cast<QKeyEvent*>(event);
            if (keyEvent->key() == Qt::Key_Return || keyEvent->key() == Qt::Key_Enter) {
                qDebug() << "拦截到回车键，来自:" << watched->objectName();
                return true;  // 拦截，不传递给 QLineEdit
            }
        }
        return QObject::eventFilter(watched, event);  // 其他事件正常传递
    }
};

class InputDemo : public QWidget {
    Q_OBJECT
public:
    explicit InputDemo(QWidget *parent = nullptr) : QWidget(parent) {
        setWindowTitle("鼠标键盘与事件过滤器");
        resize(400, 300);

        QVBoxLayout *layout = new QVBoxLayout(this);

        m_label = new QLabel("操作鼠标和键盘", this);
        m_label->setAlignment(Qt::AlignCenter);
        m_label->setMinimumHeight(100);
        m_label->setStyleSheet("background: #f0f0f0; border: 1px solid #ccc;");

        QLineEdit *input1 = new QLineEdit(this);
        input1->setObjectName("input1");
        input1->setPlaceholderText("输入框1（回车被拦截）");

        QLineEdit *input2 = new QLineEdit(this);
        input2->setObjectName("input2");
        input2->setPlaceholderText("输入框2（回车被拦截）");

        layout->addWidget(m_label);
        layout->addWidget(input1);
        layout->addWidget(input2);

        // 安装事件过滤器
        EnterKeyFilter *filter = new EnterKeyFilter(this);
        input1->installEventFilter(filter);
        input2->installEventFilter(filter);

        setMouseTracking(true);
        setFocusPolicy(Qt::StrongFocus);  // 接受键盘焦点
    }

protected:
    void mousePressEvent(QMouseEvent *event) override {
        QString btn;
        switch (event->button()) {
            case Qt::LeftButton: btn = "左键"; break;
            case Qt::RightButton: btn = "右键"; break;
            case Qt::MiddleButton: btn = "中键"; break;
            default: btn = "其他"; break;
        }
        m_label->setText(QString("%1 点击: (%2, %3)")
            .arg(btn).arg(event->position().x()).arg(event->position().y()));
    }

    void mouseDoubleClickEvent(QMouseEvent *event) override {
        m_label->setText("双击事件！");
    }

    void wheelEvent(QWheelEvent *event) override {
        int delta = event->angleDelta().y();
        m_label->setText(delta > 0 ? "滚轮向上" : "滚轮向下");
    }

    void keyPressEvent(QKeyEvent *event) override {
        if (event->isAutoRepeat()) return;  // 忽略自动重复

        QString modifiers;
        if (event->modifiers() & Qt::ControlModifier) modifiers += "Ctrl+";
        if (event->modifiers() & Qt::ShiftModifier) modifiers += "Shift+";
        if (event->modifiers() & Qt::AltModifier) modifiers += "Alt+";

        QString key = QKeySequence(event->key()).toString();
        m_label->setText("按键: " + modifiers + key);

        // Ctrl+S 保存
        if (event->key() == Qt::Key_S && (event->modifiers() & Qt::ControlModifier)) {
            qDebug() << "保存操作";
        }
    }

private:
    QLabel *m_label;
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);
    InputDemo w;
    w.show();
    return app.exec();
}

#include "main.moc"
```

#### 总结

- 鼠标事件：`mousePressEvent`、`mouseReleaseEvent`、`mouseMoveEvent`、`mouseDoubleClickEvent`、`wheelEvent`
- 键盘事件：`keyPressEvent`、`keyReleaseEvent`，通过 `key()` 和 `modifiers()` 获取按键和修饰键
- `setMouseTracking(true)` 启用未按键时的鼠标移动追踪
- 事件过滤器：`installEventFilter()` 安装，`eventFilter()` 拦截处理，返回 true 拦截事件
- 事件过滤器可实现全局快捷键、统一事件处理、日志记录等
- 常见问题：长按按键会重复触发 `keyPressEvent`，用 `isAutoRepeat()` 过滤；事件过滤器返回 false 才会继续传递

---

### 第15讲 定时器 QTimer

#### 概念

QTimer 是 Qt 提供的定时器类，用于在指定时间间隔触发事件。它是实现周期性任务（如刷新数据、动画驱动、超时检测）的核心工具。QTimer 基于事件循环工作，不会阻塞界面，是 Qt 中实现"非阻塞延迟执行"的标准方式。

#### 原理

QTimer 的工作原理：调用 `start(interval)` 后，定时器注册到事件循环，每隔 `interval` 毫秒发出 `timeout()` 信号。事件循环处理该信号时调用连接的槽函数。由于基于事件循环，QTimer 的精度受事件循环繁忙程度影响——如果事件循环正在处理耗时操作，定时器会延迟触发。

QTimer 有两种使用方式：对象式和事件式。对象式创建 QTimer 实例，连接 `timeout()` 信号到槽函数，调用 `start()`。事件式重写 `QObject::timerEvent(QTimerEvent*)`，通过 `startTimer(interval)` 启动，返回定时器 ID 用于区分多个定时器。

QTimer 还支持单次触发模式 `setSingleShot(true)`，定时器只触发一次后自动停止。`QTimer::singleShot(interval, receiver, slot)` 是静态方法，简化单次延迟执行。

#### 例子

```cpp
#include <QApplication>
#include <QWidget>
#include <QVBoxLayout>
#include <QLabel>
#include <QPushButton>
#include <QTimer>
#include <QDateTime>
#include <QDebug>

class TimerDemo : public QWidget {
    Q_OBJECT
public:
    explicit TimerDemo(QWidget *parent = nullptr) : QWidget(parent) {
        setWindowTitle("QTimer 定时器演示");
        resize(400, 250);

        QVBoxLayout *layout = new QVBoxLayout(this);

        // 1. 时钟显示（周期定时器）
        m_clockLabel = new QLabel(this);
        m_clockLabel->setAlignment(Qt::AlignCenter);
        m_clockLabel->setStyleSheet("font-size: 24px;");
        layout->addWidget(m_clockLabel);

        m_timer = new QTimer(this);
        m_timer->setInterval(1000);  // 1秒
        connect(m_timer, &QTimer::timeout, this, &TimerDemo::updateClock);
        m_timer->start();
        updateClock();  // 立即更新一次

        // 2. 计数器（可启停）
        m_countLabel = new QLabel("计数: 0", this);
        m_countLabel->setAlignment(Qt::AlignCenter);
        layout->addWidget(m_countLabel);

        m_counter = 0;
        m_countTimer = new QTimer(this);
        m_countTimer->setInterval(500);  // 0.5秒
        connect(m_countTimer, &QTimer::timeout, this, [this]() {
            m_counter++;
            m_countLabel->setText(QString("计数: %1").arg(m_counter));
        });

        QPushButton *startBtn = new QPushButton("开始计数", this);
        QPushButton *stopBtn = new QPushButton("停止计数", this);
        layout->addWidget(startBtn);
        layout->addWidget(stopBtn);

        connect(startBtn, &QPushButton::clicked, m_countTimer, QOverload<>::of(&QTimer::start));
        connect(stopBtn, &QPushButton::clicked, m_countTimer, &QTimer::stop);

        // 3. 单次定时器（延迟执行）
        QPushButton *delayBtn = new QPushButton("3秒后提示", this);
        layout->addWidget(delayBtn);
        connect(delayBtn, &QPushButton::clicked, this, [this]() {
            delayBtn->setEnabled(false);
            delayBtn->setText("等待中...");
            // 单次定时器，3秒后执行
            QTimer::singleShot(3000, this, [this, delayBtn]() {
                delayBtn->setEnabled(true);
                delayBtn->setText("3秒后提示");
                m_countLabel->setText("单次定时器触发！");
            });
        });

        // 4. 使用 timerEvent 的方式（备选方案）
        m_eventTimerId = startTimer(2000);  // 2秒间隔，返回定时器ID
    }

protected:
    void timerEvent(QTimerEvent *event) override {
        if (event->timerId() == m_eventTimerId) {
            qDebug() << "timerEvent 触发:" << QDateTime::currentDateTime().toString();
        }
    }

private slots:
    void updateClock() {
        m_clockLabel->setText(QDateTime::currentDateTime().toString("yyyy-MM-dd hh:mm:ss"));
    }

private:
    QLabel *m_clockLabel;
    QTimer *m_timer;
    QLabel *m_countLabel;
    QTimer *m_countTimer;
    int m_counter;
    int m_eventTimerId;
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);
    TimerDemo w;
    w.show();
    return app.exec();
}

#include "main.moc"
```

#### 总结

- QTimer 基于事件循环，不阻塞界面，是周期性任务的标准方案
- 对象式：创建 QTimer，连接 `timeout()` 信号，`start(interval)` 启动
- 事件式：重写 `timerEvent()`，`startTimer()` 返回 ID 区分多个定时器
- `setSingleShot(true)` 设置单次模式，`QTimer::singleShot()` 静态方法简化单次延迟
- 定时器精度受事件循环繁忙程度影响，不适合高精度场景（应使用 QElapsedTimer）
- 常见问题：定时器在子线程中需该线程有事件循环；`stop()` 后 `timeout()` 不再触发

---

## 第5章 模型/视图架构

### 第16讲 Model/View 架构概览

#### 概念

Model/View（模型/视图）架构是 Qt 用于处理"数据-显示"分离的设计模式。它将数据（Model）、展示（View）和用户交互（Delegate）三者解耦，使同一份数据可以用多种视图展示，同一视图可以适配不同数据源。这是 Qt 中处理表格、列表、树形数据的标准方案，相比直接使用 QListWidget、QTableWidget 等便捷类，Model/View 更灵活、更高效，尤其适合大数据量场景。

#### 原理

Model/View 架构包含三个核心角色：

1. **Model（模型）**：继承自 `QAbstractItemModel`，负责管理数据并向视图提供数据访问接口。模型通过索引（`QModelIndex`）定位数据项，每个索引包含行、列、父索引信息。模型实现 `data()`、`rowCount()`、`columnCount()` 等虚函数供视图调用。

2. **View（视图）**：继承自 `QAbstractItemView`，负责数据的可视化展示。视图通过调用模型的接口获取数据并渲染。Qt 提供三种预定义视图：`QListView`（列表）、`QTableView`（表格）、`QTreeView`（树形）。

3. **Delegate（代理）**：继承自 `QStyledItemDelegate`，负责单个数据项的绘制和编辑。代理决定数据如何显示（如用进度条显示数值）、如何编辑（如用下拉框替代默认文本框）。

数据变化时，模型发出 `dataChanged()`、`rowsInserted()` 等信号通知视图更新；用户编辑时，视图通过代理调用模型的 `setData()` 修改数据。这种双向通信机制确保数据和显示始终同步。

#### 例子

```cpp
#include <QApplication>
#include <QWidget>
#include <QVBoxLayout>
#include <QHBoxLayout>
#include <QListView>
#include <QTableView>
#include <QTreeView>
#include <QStandardItemModel>
#include <QPushButton>
#include <QDebug>

class ModelViewDemo : public QWidget {
    Q_OBJECT
public:
    explicit ModelViewDemo(QWidget *parent = nullptr) : QWidget(parent) {
        setWindowTitle("Model/View 架构演示");
        resize(800, 500);

        // 创建一个标准数据模型
        model = new QStandardItemModel(this);
        model->setHorizontalHeaderLabels({"姓名", "年龄", "城市"});

        // 添加数据
        addRow("张三", 25, "北京");
        addRow("李四", 30, "上海");
        addRow("王五", 28, "广州");

        // 添加树形子节点
        QStandardItem *rootItem = model->invisibleRootItem();
        QStandardItem *childItem = new QStandardItem("赵六");
        childItem->setChild(0, 0, new QStandardItem("子项1"));
        childItem->setChild(0, 1, new QStandardItem("22"));
        childItem->setChild(0, 2, new QStandardItem("深圳"));
        rootItem->child(0)->setChild(0, childItem);

        // 布局：三个视图共享同一个模型
        QHBoxLayout *layout = new QHBoxLayout(this);

        QListView *listView = new QListView(this);
        listView->setModel(model);

        QTableView *tableView = new QTableView(this);
        tableView->setModel(model);

        QTreeView *treeView = new QTreeView(this);
        treeView->setModel(model);

        layout->addWidget(listView);
        layout->addWidget(tableView);
        layout->addWidget(treeView);

        // 添加按钮：在任意视图编辑数据，其他视图同步更新
        QVBoxLayout *btnLayout = new QVBoxLayout();
        QPushButton *addBtn = new QPushButton("添加行");
        QPushButton *delBtn = new QPushButton("删除选中行");
        btnLayout->addWidget(addBtn);
        btnLayout->addWidget(delBtn);
        btnLayout->addStretch();
        layout->addLayout(btnLayout);

        connect(addBtn, &QPushButton::clicked, this, [this]() {
            int row = model->rowCount();
            addRow("新用户" + QString::number(row), 20 + row % 30, "新城市");
        });
        connect(delBtn, &QPushButton::clicked, this, [this, tableView]() {
            QModelIndex idx = tableView->currentIndex();
            if (idx.isValid()) {
                model->removeRow(idx.row(), idx.parent());
            }
        });
    }

private:
    QStandardItemModel *model;

    void addRow(const QString &name, int age, const QString &city) {
        QList<QStandardItem*> row;
        row << new QStandardItem(name)
            << new QStandardItem(QString::number(age))
            << new QStandardItem(city);
        model->appendRow(row);
    }
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);
    ModelViewDemo w;
    w.show();
    return app.exec();
}

#include "main.moc"
```

#### 总结

- Model/View 架构将数据（Model）、展示（View）、交互（Delegate）三者解耦
- Model 继承 `QAbstractItemModel`，通过 `QModelIndex` 定位数据，实现 `data()`、`rowCount()` 等接口
- View 有三种：QListView（列表）、QTableView（表格）、QTreeView（树形）
- Delegate 负责单项的绘制和编辑，继承 `QStyledItemDelegate`
- 同一个 Model 可被多个 View 共享，数据修改自动同步到所有视图
- 常见问题：便捷类（QListWidget 等）内部封装了 Model，适合简单场景；大数据量应用自定义 Model

---

### 第17讲 自定义模型

#### 概念

当数据来源不是简单的内存列表（如数据库、网络、自定义数据结构），需要继承 `QAbstractItemModel` 或其子类实现自定义模型。本讲以 `QAbstractTableModel` 为例，演示如何为自定义数据结构创建表格模型，实现数据的展示、编辑、增删等完整功能。

#### 原理

自定义模型需实现以下核心虚函数：

- `rowCount()` / `columnCount()`：返回行数和列数，视图据此确定表格大小
- `data()`：根据索引和角色（`Qt::DisplayRole` 显示文本、`Qt::EditRole` 编辑值、`Qt::TextAlignmentRole` 对齐方式、`Qt::BackgroundRole` 背景色等）返回数据
- `headerData()`：返回表头数据
- `flags()`：返回项的标志（可编辑、可选、可启用等）
- `setData()`：处理用户编辑，修改底层数据并发出 `dataChanged` 信号
- `insertRows()` / `removeRows()`：支持增删行，发出 `rowsInserted` / `rowsRemoved` 信号

视图在需要显示时会调用这些函数获取数据。模型修改数据后必须发出对应信号，视图才能更新显示。这种"拉取式"的数据访问使模型可以按需提供数据，避免一次性加载全部数据到内存。

#### 例子

```cpp
#include <QApplication>
#include <QWidget>
#include <QVBoxLayout>
#include <QTableView>
#include <QAbstractTableModel>
#include <QPushButton>
#include <QHeaderView>
#include <QInputDialog>
#include <QColor>

// 学生数据结构
struct Student {
    QString name;
    int age;
    QString major;
    double score;
};

// 自定义表格模型
class StudentModel : public QAbstractTableModel {
    Q_OBJECT
public:
    explicit StudentModel(QObject *parent = nullptr) : QAbstractTableModel(parent) {
        // 初始数据
        m_students = {
            {"张三", 20, "计算机", 85.5},
            {"李四", 21, "数学", 92.0},
            {"王五", 22, "物理", 78.5}
        };
    }

    // 必须实现的核心函数
    int rowCount(const QModelIndex &parent = QModelIndex()) const override {
        Q_UNUSED(parent);
        return m_students.size();
    }

    int columnCount(const QModelIndex &parent = QModelIndex()) const override {
        Q_UNUSED(parent);
        return 4;  // 姓名、年龄、专业、成绩
    }

    QVariant data(const QModelIndex &index, int role = Qt::DisplayRole) const override {
        if (!index.isValid() || index.row() >= m_students.size())
            return QVariant();

        const Student &s = m_students[index.row()];

        switch (role) {
            case Qt::DisplayRole:    // 显示文本
            case Qt::EditRole:       // 编辑值
                switch (index.column()) {
                    case 0: return s.name;
                    case 1: return s.age;
                    case 2: return s.major;
                    case 3: return s.score;
                }
                break;
            case Qt::TextAlignmentRole:  // 对齐方式
                if (index.column() == 0 || index.column() == 2)
                    return Qt::AlignLeft;
                return Qt::AlignCenter;
            case Qt::BackgroundRole:  // 背景色：成绩低于60标红
                if (index.column() == 3 && s.score < 60)
                    return QColor(255, 200, 200);
                return QVariant();
            case Qt::ToolTipRole:  // 工具提示
                return QString("学生: %1, 学号: %2").arg(s.name).arg(index.row() + 1);
        }
        return QVariant();
    }

    QVariant headerData(int section, Qt::Orientation orientation,
                         int role = Qt::DisplayRole) const override {
        if (role != Qt::DisplayRole) return QVariant();
        if (orientation == Qt::Horizontal) {
            QStringList headers = {"姓名", "年龄", "专业", "成绩"};
            return headers.value(section);
        }
        return section + 1;  // 垂直表头显示行号
    }

    Qt::ItemFlags flags(const QModelIndex &index) const override {
        if (!index.isValid()) return Qt::NoItemFlags;
        return Qt::ItemIsEnabled | Qt::ItemIsSelectable | Qt::ItemIsEditable;
    }

    bool setData(const QModelIndex &index, const QVariant &value, int role = Qt::EditRole) override {
        if (!index.isValid() || role != Qt::EditRole || index.row() >= m_students.size())
            return false;

        Student &s = m_students[index.row()];
        switch (index.column()) {
            case 0: s.name = value.toString(); break;
            case 1: s.age = value.toInt(); break;
            case 2: s.major = value.toString(); break;
            case 3: s.score = value.toDouble(); break;
            default: return false;
        }
        emit dataChanged(index, index);  // 通知视图更新
        return true;
    }

    // 增删行
    bool insertRows(int row, int count, const QModelIndex &parent = QModelIndex()) override {
        beginInsertRows(parent, row, row + count - 1);  // 必须调用
        m_students.insert(row, count, Student{"", 0, "", 0});
        endInsertRows();
        return true;
    }

    bool removeRows(int row, int count, const QModelIndex &parent = QModelIndex()) override {
        beginRemoveRows(parent, row, row + count - 1);
        m_students.remove(row, count);
        endRemoveRows();
        return true;
    }

private:
    QVector<Student> m_students;
};

class CustomModelDemo : public QWidget {
    Q_OBJECT
public:
    explicit CustomModelDemo(QWidget *parent = nullptr) : QWidget(parent) {
        setWindowTitle("自定义模型演示");
        resize(600, 400);

        QVBoxLayout *layout = new QVBoxLayout(this);
        QTableView *view = new QTableView(this);
        model = new StudentModel(this);
        view->setModel(model);

        // 美化表格
        view->horizontalHeader()->setSectionResizeMode(QHeaderView::Stretch);
        view->setAlternatingRowColors(true);
        view->setStyleSheet("alternate-background-color: #f5f5f5;");

        layout->addWidget(view);

        QPushButton *addBtn = new QPushButton("添加学生");
        QPushButton *delBtn = new QPushButton("删除选中");
        layout->addWidget(addBtn);
        layout->addWidget(delBtn);

        connect(addBtn, &QPushButton::clicked, this, [this]() {
            model->insertRow(model->rowCount());
        });
        connect(delBtn, &QPushButton::clicked, this, [this, view]() {
            QModelIndex idx = view->currentIndex();
            if (idx.isValid()) model->removeRow(idx.row());
        });
    }

private:
    StudentModel *model;
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);
    CustomModelDemo w;
    w.show();
    return app.exec();
}

#include "main.moc"
```

#### 总结

- 自定义模型继承 `QAbstractTableModel`（表格）或 `QAbstractListModel`（列表）或 `QAbstractItemModel`（树形）
- 必须实现：`rowCount()`、`columnCount()`、`data()`、`headerData()`、`flags()`
- 支持编辑需实现 `setData()`，支持增删需实现 `insertRows()` / `removeRows()`
- `data()` 通过 role 区分不同用途：DisplayRole（显示）、EditRole（编辑）、BackgroundRole（背景）等
- 增删行必须调用 `beginInsertRows`/`endInsertRows` 等函数，否则视图不会更新
- 常见问题：忘记 `emit dataChanged()` 导致编辑后显示不更新；`flags()` 未返回 `ItemIsEditable` 导致无法编辑

---

### 第18讲 代理 Delegate

#### 概念

代理（Delegate）是 Model/View 架构中负责"单项绘制"和"单项编辑"的组件。默认情况下，视图使用 `QStyledItemDelegate` 统一绘制文本和编辑。当需要自定义显示效果（如用进度条显示数值、用图标显示状态）或自定义编辑控件（如用下拉框、日期选择器替代默认文本框）时，需要实现自定义代理。

#### 原理

代理通过两个核心函数工作：

1. **`paint()`**：负责绘制单项内容。视图在需要重绘某个单元格时调用此函数，传入 `QPainter`、样式选项 `QStyleOptionViewItem` 和模型索引。开发者在此函数中用 `QPainter` 绘制自定义内容（如进度条、图形）。

2. **`createEditor()`**：创建编辑控件。当用户双击单元格进入编辑模式时，视图调用此函数创建编辑器（如 QSpinBox、QComboBox）。`setEditorData()` 将模型数据填入编辑器，`setModelData()` 将编辑器数据写回模型。

自定义代理有两种实现方式：继承 `QStyledItemDelegate` 重写相关函数，或继承 `QItemDelegate`（更底层）。推荐前者，因为它与 Qt 样式系统更好地集成。

#### 例子

```cpp
#include <QApplication>
#include <QWidget>
#include <QVBoxLayout>
#include <QTableView>
#include <QStyledItemDelegate>
#include <QStandardItemModel>
#include <QSpinBox>
#include <QProgressBar>
#include <QPainter>
#include <QStyleOptionViewItem>

// 进度条代理：用进度条显示 0-100 的数值
class ProgressBarDelegate : public QStyledItemDelegate {
    Q_OBJECT
public:
    using QStyledItemDelegate::QStyledItemDelegate;

    // 自定义绘制：画进度条
    void paint(QPainter *painter, const QStyleOptionViewItem &option,
               const QModelIndex &index) const override {
        int value = index.data(Qt::DisplayRole).toInt();

        QStyleOptionProgressBar opt;
        opt.rect = option.rect;
        opt.minimum = 0;
        opt.maximum = 100;
        opt.progress = value;
        opt.text = QString::number(value) + "%";
        opt.textVisible = true;

        // 使用 QApplication::style() 绘制标准进度条
        QApplication::style()->drawControl(QStyle::CE_ProgressBar, &opt, painter);
    }

    // 创建编辑器：用 QSpinBox 编辑数值
    QWidget* createEditor(QWidget *parent, const QStyleOptionViewItem &option,
                          const QModelIndex &index) const override {
        QSpinBox *editor = new QSpinBox(parent);
        editor->setRange(0, 100);
        editor->setSingleStep(5);
        return editor;
    }

    // 将模型数据填入编辑器
    void setEditorData(QWidget *editor, const QModelIndex &index) const override {
        int value = index.data(Qt::EditRole).toInt();
        QSpinBox *spin = qobject_cast<QSpinBox*>(editor);
        spin->setValue(value);
    }

    // 将编辑器数据写回模型
    void setModelData(QWidget *editor, QAbstractItemModel *model,
                      const QModelIndex &index) const override {
        QSpinBox *spin = qobject_cast<QSpinBox*>(editor);
        spin->interpretText();
        model->setData(index, spin->value(), Qt::EditRole);
    }

    // 编辑器大小建议
    void updateEditorGeometry(QWidget *editor, const QStyleOptionViewItem &option,
                              const QModelIndex &index) const override {
        editor->setGeometry(option.rect);
    }
};

class DelegateDemo : public QWidget {
    Q_OBJECT
public:
    explicit DelegateDemo(QWidget *parent = nullptr) : QWidget(parent) {
        setWindowTitle("代理 Delegate 演示");
        resize(500, 300);

        QVBoxLayout *layout = new QVBoxLayout(this);
        QTableView *view = new QTableView(this);

        QStandardItemModel *model = new QStandardItemModel(5, 2, this);
        model->setHorizontalHeaderLabels({"任务名称", "完成进度"});

        QStringList tasks = {"设计文档", "编码实现", "单元测试", "集成测试", "部署上线"};
        for (int i = 0; i < tasks.size(); ++i) {
            model->setItem(i, 0, new QStandardItem(tasks[i]));
            model->setItem(i, 1, new QStandardItem(QString::number(i * 20)));
        }

        view->setModel(model);

        // 为第2列设置进度条代理
        view->setItemDelegateForColumn(1, new ProgressBarDelegate(this));

        layout->addWidget(view);
    }
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);
    DelegateDemo w;
    w.show();
    return app.exec();
}

#include "main.moc"
```

#### 总结

- 代理负责 Model/View 中单项的绘制（`paint()`）和编辑（`createEditor()`）
- `paint()` 用 `QPainter` 绘制自定义内容，可借助 `QApplication::style()->drawControl()` 绘制标准控件
- `createEditor()` 创建编辑控件，`setEditorData()` / `setModelData()` 处理数据读写
- `setItemDelegateForColumn()` / `setItemDelegateForRow()` 为特定列/行设置代理
- 常见问题：`paint()` 中不要忘记处理选中状态的高亮；编辑器需在 `updateEditorGeometry()` 中设置位置

---

## 第6章 图形与绘图

### 第19讲 QPainter 基础

#### 概念

QPainter 是 Qt 的核心绘图类，用于在控件、图片、打印机等设备上绘制图形和文本。它能绘制基本图形（线、矩形、椭圆、多边形、弧线）、文本、图片，并支持抗锯齿、渐变、变换等高级效果。QPainter 是实现自定义控件、数据可视化、图形编辑器的基础工具。

#### 原理

QPainter 采用"状态机"模型：绘图前设置画笔（QPen，控制线条颜色、宽度、样式）、画刷（QBrush，控制填充颜色、渐变、纹理）、字体（QFont，控制文本样式）等状态，然后调用绘图函数。状态会持续影响后续绘制，直到被修改。

QPainter 的坐标系原点在左上角，x 轴向右，y 轴向下。通过 `translate()`、`rotate()`、`scale()` 可进行坐标变换。`save()` / `restore()` 保存和恢复绘图状态，便于在复杂变换中切换。

抗锯齿通过 `setRenderHint(QPainter::Antialiasing)` 开启，使线条和边缘更平滑。绘制文本用 `drawText()`，需配合 `QFont` 设置字体。绘制图片用 `drawImage()` 或 `drawPixmap()`，后者针对屏幕显示优化。

#### 例子

```cpp
#include <QApplication>
#include <QWidget>
#include <QPainter>
#include <QPen>
#include <QBrush>
#include <QLinearGradient>
#include <QRadialGradient>
#include <QFont>
#include <QConicalGradient>

class PainterDemo : public QWidget {
    Q_OBJECT
public:
    explicit PainterDemo(QWidget *parent = nullptr) : QWidget(parent) {
        setWindowTitle("QPainter 绘图演示");
        resize(600, 500);
    }

protected:
    void paintEvent(QPaintEvent *) override {
        QPainter painter(this);
        painter.setRenderHint(QPainter::Antialiasing);  // 开启抗锯齿

        // 1. 绘制基本图形
        drawBasicShapes(painter);

        // 2. 绘制渐变
        painter.save();
        painter.translate(0, 150);
        drawGradients(painter);
        painter.restore();

        // 3. 绘制文本
        painter.save();
        painter.translate(0, 300);
        drawTexts(painter);
        painter.restore();

        // 4. 坐标变换示例
        painter.save();
        painter.translate(400, 100);
        painter.rotate(45);  // 旋转45度
        painter.setBrush(QColor(255, 100, 100, 150));
        painter.drawRect(-30, -30, 60, 60);
        painter.restore();
    }

private:
    void drawBasicShapes(QPainter &painter) {
        // 画笔：红色，2像素宽，实线
        QPen pen(Qt::red, 2, Qt::SolidLine);
        painter.setPen(pen);
        // 画刷：蓝色填充
        painter.setBrush(QBrush(Qt::blue));
        painter.drawRect(20, 20, 80, 60);

        // 椭圆
        painter.setBrush(QBrush(Qt::green, Qt::DiagCrossPattern));
        painter.drawEllipse(120, 20, 80, 60);

        // 直线
        painter.setPen(QPen(Qt::black, 3));
        painter.drawLine(220, 20, 300, 80);

        // 多边形
        QPolygon polygon;
        polygon << QPoint(330, 20) << QPoint(380, 30)
                << QPoint(390, 80) << QPoint(340, 70);
        painter.setBrush(QColor(255, 200, 0, 180));
        painter.drawPolygon(polygon);

        // 圆弧
        painter.setPen(QPen(Qt::magenta, 4));
        painter.setBrush(Qt::NoBrush);
        painter.drawArc(420, 20, 80, 80, 0 * 16, 270 * 16);  // 角度单位为1/16度
    }

    void drawGradients(QPainter &painter) {
        // 线性渐变
        QLinearGradient linear(20, 0, 100, 0);
        linear.setColorAt(0, Qt::red);
        linear.setColorAt(0.5, Qt::yellow);
        linear.setColorAt(1, Qt::blue);
        painter.setBrush(linear);
        painter.drawRect(20, 0, 100, 80);

        // 径向渐变
        QRadialGradient radial(220, 40, 50);
        radial.setColorAt(0, Qt::white);
        radial.setColorAt(1, Qt::darkGreen);
        painter.setBrush(radial);
        painter.drawEllipse(170, 0, 100, 80);

        // 圆锥渐变
        QConicalGradient conical(370, 40, 0);
        conical.setColorAt(0, Qt::red);
        conical.setColorAt(0.33, Qt::green);
        conical.setColorAt(0.66, Qt::blue);
        conical.setColorAt(1, Qt::red);
        painter.setBrush(conical);
        painter.drawEllipse(320, 0, 100, 80);
    }

    void drawTexts(QPainter &painter) {
        // 基本文本
        painter.setPen(Qt::black);
        QFont font1("Microsoft YaHei", 16, QFont::Bold);
        painter.setFont(font1);
        painter.drawText(20, 30, "Qt6 绘图教程");

        // 描边文本
        QFont font2("Arial", 24, QFont::Bold);
        painter.setFont(font2);
        QPainterPath path;
        path.addText(20, 80, font2, "Outline Text");
        painter.setPen(QPen(Qt::blue, 2));
        painter.setBrush(Qt::yellow);
        painter.drawPath(path);
    }
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);
    PainterDemo w;
    w.show();
    return app.exec();
}

#include "main.moc"
```

#### 总结

- QPainter 是 Qt 核心绘图类，在 `paintEvent()` 中使用
- 状态机模型：设置 QPen（线条）、QBrush（填充）、QFont（字体）后绘制
- 坐标系原点左上角，`translate()`/`rotate()`/`scale()` 进行变换，`save()`/`restore()` 保存恢复状态
- 渐变：QLinearGradient（线性）、QRadialGradient（径向）、QConicalGradient（圆锥）
- `setRenderHint(Antialiasing)` 开启抗锯齿，使线条更平滑
- 常见问题：`drawArc()` 的角度单位是 1/16 度；绘制大量内容应缓存到 QPixmap 提升性能

---

### 第20讲 QGraphicsView 框架

#### 概念

QGraphicsView 框架是 Qt 提供的"图形视图"架构，用于管理大量 2D 图形项的显示、交互和动画。它采用"场景-视图-项"三层结构，支持项的旋转、缩放、选中、拖动、分组等操作，是开发图形编辑器、流程图工具、地图应用、游戏场景的理想选择。

#### 原理

QGraphicsView 框架包含三个核心类：

1. **QGraphicsScene（场景）**：管理图形项的容器，提供项的增删、查找、碰撞检测等功能。场景有自己的坐标系，可包含成千上万个项。

2. **QGraphicsView（视图）**：负责将场景内容渲染到屏幕上。一个场景可被多个视图同时显示，视图可独立缩放、旋转、平移。视图处理鼠标和键盘事件并转发给场景中的项。

3. **QGraphicsItem（图形项）**：场景中的基本元素，如矩形、椭圆、文本、图片。Qt 提供预定义项（QGraphicsRectItem、QGraphicsEllipseItem、QGraphicsTextItem、QGraphicsPixmapItem 等），也可继承 QGraphicsItem 实现自定义项。

项支持变换（`setRotation()`、`setScale()`、`setPos()`）、交互（`setFlag(ItemIsMovable)`、`ItemIsSelectable`）、父子关系（`setParentItem()`，子项跟随父项变换）。场景通过 BSP 树（二叉空间分割）高效管理大量项的可见区域和碰撞检测。

#### 例子

```cpp
#include <QApplication>
#include <QGraphicsView>
#include <QGraphicsScene>
#include <QGraphicsRectItem>
#include <QGraphicsEllipseItem>
#include <QGraphicsTextItem>
#include <QGraphicsPixmapItem>
#include <QGraphicsItem>
#include <QMouseEvent>
#include <QVBoxLayout>
#include <QPushButton>
#include <QWidget>
#include <QDebug>

// 自定义可交互图形项
class CustomItem : public QGraphicsRectItem {
public:
    CustomItem(const QRectF &rect, const QColor &color) : QGraphicsRectItem(rect) {
        setBrush(color);
        setPen(QPen(Qt::black, 2));
        setFlag(QGraphicsItem::ItemIsMovable, true);      // 可拖动
        setFlag(QGraphicsItem::ItemIsSelectable, true);   // 可选中
        setFlag(QGraphicsItem::ItemSendsGeometryChanges, true);  // 位置变化通知
    }

protected:
    // 选中时改变外观
    QVariant itemChange(GraphicsItemChange change, const QVariant &value) override {
        if (change == QGraphicsItem::ItemSelectedHasChanged) {
            if (isSelected()) {
                setPen(QPen(Qt::red, 3));
            } else {
                setPen(QPen(Qt::black, 2));
            }
        }
        return QGraphicsRectItem::itemChange(change, value);
    }

    // 双击事件
    void mouseDoubleClickEvent(QGraphicsSceneMouseEvent *event) override {
        qDebug() << "项被双击，位置:" << pos();
        // 随机变色
        setBrush(QColor(rand() % 256, rand() % 256, rand() % 256));
        QGraphicsRectItem::mouseDoubleClickEvent(event);
    }
};

class GraphicsViewDemo : public QWidget {
    Q_OBJECT
public:
    explicit GraphicsViewDemo(QWidget *parent = nullptr) : QWidget(parent) {
        setWindowTitle("QGraphicsView 框架演示");
        resize(700, 500);

        QVBoxLayout *layout = new QVBoxLayout(this);

        // 创建场景
        scene = new QGraphicsScene(this);
        scene->setSceneRect(0, 0, 600, 400);

        // 添加图形项
        for (int i = 0; i < 5; ++i) {
            CustomItem *item = new CustomItem(
                QRectF(i * 100 + 20, 50, 80, 80),
                QColor(100 + i * 30, 150, 200)
            );
            scene->addItem(item);
        }

        // 添加椭圆
        QGraphicsEllipseItem *ellipse = scene->addEllipse(
            50, 200, 100, 60, QPen(Qt::darkGreen, 2), QBrush(Qt::green)
        );
        ellipse->setFlag(QGraphicsItem::ItemIsMovable);

        // 添加文本
        QGraphicsTextItem *text = scene->addText("Qt6 图形视图框架");
        text->setPos(200, 250);
        QFont font("Microsoft YaHei", 20, QFont::Bold);
        text->setFont(font);

        // 添加连线
        QGraphicsLineItem *line = scene->addLine(
            100, 150, 500, 150, QPen(Qt::blue, 2, Qt::DashLine)
        );

        // 创建视图
        view = new QGraphicsView(scene, this);
        view->setRenderHint(QPainter::Antialiasing);
        view->setDragMode(QGraphicsView::RubberBandDrag);  // 框选模式
        layout->addWidget(view);

        // 控制按钮
        QHBoxLayout *btnLayout = new QHBoxLayout();
        QPushButton *zoomInBtn = new QPushButton("放大");
        QPushButton *zoomOutBtn = new QPushButton("缩小");
        QPushButton *rotateBtn = new QPushButton("旋转选中项");
        QPushButton *resetBtn = new QPushButton("重置视图");

        btnLayout->addWidget(zoomInBtn);
        btnLayout->addWidget(zoomOutBtn);
        btnLayout->addWidget(rotateBtn);
        btnLayout->addWidget(resetBtn);
        layout->addLayout(btnLayout);

        connect(zoomInBtn, &QPushButton::clicked, [this]() {
            view->scale(1.2, 1.2);
        });
        connect(zoomOutBtn, &QPushButton::clicked, [this]() {
            view->scale(1 / 1.2, 1 / 1.2);
        });
        connect(rotateBtn, &QPushButton::clicked, [this]() {
            for (QGraphicsItem *item : scene->selectedItems()) {
                item->setRotation(item->rotation() + 15);
            }
        });
        connect(resetBtn, &QPushButton::clicked, [this]() {
            view->resetTransform();
        });
    }

private:
    QGraphicsScene *scene;
    QGraphicsView *view;
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);
    GraphicsViewDemo w;
    w.show();
    return app.exec();
}

#include "main.moc"
```

#### 总结

- QGraphicsView 框架采用"场景-视图-项"三层结构
- QGraphicsScene 管理图形项容器，QGraphicsView 渲染场景，QGraphicsItem 是基本元素
- 一个场景可被多个视图显示，视图可独立缩放、旋转
- 项支持变换、交互（拖动、选中）、父子关系
- 预定义项：QGraphicsRectItem、QGraphicsEllipseItem、QGraphicsTextItem、QGraphicsLineItem
- 常见问题：`setSceneRect()` 限制场景范围；大量项时 BSP 树提升性能；`setDragMode(RubberBandDrag)` 启用框选

---

### 第21讲 自定义控件

#### 概念

当 Qt 内置控件无法满足需求时，可以通过继承 QWidget 实现自定义控件。自定义控件能完全控制外观和交互，常用于：特殊视觉效果（仪表盘、水位计、环形进度条）、行业专用控件（心电图、频谱图）、品牌化 UI 等。本讲通过实现一个"环形进度条"控件，演示自定义控件的完整开发流程。

#### 原理

自定义控件的核心是重写 `paintEvent()` 进行绘制，并处理鼠标/键盘事件实现交互。控件的状态（如进度值、颜色）通过属性暴露，支持通过 `Q_PROPERTY` 声明以便在 Qt Designer 中编辑。

自定义控件开发要点：

1. **属性设计**：用 `Q_PROPERTY` 声明可读写的属性，提供 getter/setter 和变化信号
2. **绘制逻辑**：在 `paintEvent()` 中根据当前状态用 QPainter 绘制
3. **更新触发**：状态变化时调用 `update()` 触发重绘（异步，合并多次调用）
4. **尺寸建议**：重写 `sizeHint()` 返回建议大小，`minimumSizeHint()` 返回最小大小
5. **样式支持**：可通过 `QStyle` 或自定义样式表实现主题化

#### 例子

```cpp
#include <QApplication>
#include <QWidget>
#include <QVBoxLayout>
#include <QSlider>
#include <QPainter>
#include <QConicalGradient>
#include <QPainterPath>
#include <QTimer>
#include <cmath>

// 环形进度条控件
class CircularProgress : public QWidget {
    Q_OBJECT
    Q_PROPERTY(int value READ value WRITE setValue NOTIFY valueChanged)
    Q_PROPERTY(QColor progressColor READ progressColor WRITE setProgressColor)
    Q_PROPERTY(QColor backgroundColor READ backgroundColor WRITE setBackgroundColor)

public:
    explicit CircularProgress(QWidget *parent = nullptr) : QWidget(parent) {
        m_value = 0;
        m_progressColor = QColor(50, 150, 250);
        m_backgroundColor = QColor(220, 220, 220);
        setMinimumSize(200, 200);
    }

    // 属性访问器
    int value() const { return m_value; }
    void setValue(int v) {
        if (v < 0) v = 0;
        if (v > 100) v = 100;
        if (m_value != v) {
            m_value = v;
            emit valueChanged(v);
            update();  // 触发重绘
        }
    }

    QColor progressColor() const { return m_progressColor; }
    void setProgressColor(const QColor &c) { m_progressColor = c; update(); }

    QColor backgroundColor() const { return m_backgroundColor; }
    void setBackgroundColor(const QColor &c) { m_backgroundColor = c; update(); }

    QSize sizeHint() const override { return QSize(250, 250); }
    QSize minimumSizeHint() const override { return QSize(100, 100); }

signals:
    void valueChanged(int value);

protected:
    void paintEvent(QPaintEvent *) override {
        QPainter painter(this);
        painter.setRenderHint(QPainter::Antialiasing);

        int side = qMin(width(), height());
        QRectF rect(width() / 2.0 - side / 2.0, height() / 2.0 - side / 2.0,
                    side, side);
        // 内缩留边距
        rect.adjust(10, 10, -10, -10);

        // 1. 绘制背景圆环
        painter.setPen(QPen(m_backgroundColor, 15, Qt::SolidLine, Qt::RoundCap));
        painter.drawArc(rect, 0 * 16, 360 * 16);

        // 2. 绘制进度弧线
        qreal startAngle = 90 * 16;  // 从顶部开始
        qreal spanAngle = -m_value * 3.6 * 16;  // 负值表示顺时针

        QConicalGradient gradient(rect.center(), 90);
        gradient.setColorAt(0, m_progressColor.lighter());
        gradient.setColorAt(1, m_progressColor);

        painter.setPen(QPen(gradient, 15, Qt::SolidLine, Qt::RoundCap));
        painter.drawArc(rect, startAngle, spanAngle);

        // 3. 绘制中心文本
        painter.setPen(Qt::black);
        QFont font("Microsoft YaHei", side / 8, QFont::Bold);
        painter.setFont(font);
        painter.drawText(rect, Qt::AlignCenter, QString::number(m_value) + "%");
    }

private:
    int m_value;
    QColor m_progressColor;
    QColor m_backgroundColor;
};

// 演示窗口
class CustomWidgetDemo : public QWidget {
    Q_OBJECT
public:
    explicit CustomWidgetDemo(QWidget *parent = nullptr) : QWidget(parent) {
        setWindowTitle("自定义控件演示");
        resize(400, 400);

        QVBoxLayout *layout = new QVBoxLayout(this);

        CircularProgress *progress = new CircularProgress(this);
        layout->addWidget(progress, 0, Qt::AlignCenter);

        QSlider *slider = new QSlider(Qt::Horizontal, this);
        slider->setRange(0, 100);
        slider->setValue(50);
        layout->addWidget(slider);

        connect(slider, &QSlider::valueChanged, progress, &CircularProgress::setValue);

        // 自动动画演示
        QTimer *timer = new QTimer(this);
        static int dir = 1;
        connect(timer, &QTimer::timeout, this, [progress, &dir]() {
            int v = progress->value() + dir;
            if (v >= 100) dir = -1;
            if (v <= 0) dir = 1;
            progress->setValue(v);
        });
        // timer->start(50);  // 取消注释启动自动动画
    }
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);
    CustomWidgetDemo w;
    w.show();
    return app.exec();
}

#include "main.moc"
```

#### 总结

- 自定义控件继承 QWidget，重写 `paintEvent()` 绘制，处理事件实现交互
- 用 `Q_PROPERTY` 声明属性，支持 Qt Designer 编辑和样式表
- 状态变化时调用 `update()` 触发重绘（异步合并，性能好）
- 重写 `sizeHint()` 和 `minimumSizeHint()` 提供尺寸建议
- `QConicalGradient` 适合环形渐变，`Qt::RoundCap` 使线条端点圆润
- 常见问题：`paintEvent()` 中不要执行耗时操作；`update()` 比 `repaint()` 更推荐（异步合并）

---

## 第7章 多线程与并发

### 第22讲 QThread 基础

#### 概念

QThread 是 Qt 提供的线程类，用于实现多线程编程。在 Qt 中，耗时操作（如网络请求、大量计算、文件读写）如果放在主线程（GUI 线程）执行，会导致界面卡顿无响应。QThread 允许将这些操作放到后台线程，保持界面流畅响应。理解 QThread 的正确使用方式是 Qt 进阶的关键。

#### 原理

QThread 本质上是一个线程管理类，它封装了操作系统的线程 API。QThread 的核心是 `run()` 方法——线程启动后默认调用 `exec()` 启动事件循环，使该线程能处理信号槽和定时器。

QThread 有两种使用方式：

1. **重写 run() 方式**：继承 QThread，重写 `run()` 方法，在 `run()` 中执行耗时操作。调用 `start()` 启动线程。这种方式简单直接，但线程内无法使用信号槽（除非手动调用 `exec()`）。

2. **Worker 移动方式（推荐）**：创建一个 QObject 子类（Worker），用 `moveToThread()` 将其移动到 QThread 线程。Worker 的槽函数在该线程中执行。这种方式更符合 Qt 的设计哲学，支持信号槽通信。

**关键原则**：QThread 对象本身属于创建它的线程（通常是主线程），不要在 Worker 中直接操作 QThread 对象。线程间通信必须通过信号槽（Qt 自动处理跨线程连接为队列连接）。

#### 例子

```cpp
#include <QApplication>
#include <QWidget>
#include <QVBoxLayout>
#include <QPushButton>
#include <QProgressBar>
#include <QLabel>
#include <QThread>
#include <QDebug>

// 方式一：重写 run()
class ComputeThread : public QThread {
    Q_OBJECT
public:
    explicit ComputeThread(QObject *parent = nullptr) : QThread(parent) {
        m_abort = false;
    }

    void abort() { m_abort = true; }

signals:
    void progress(int percent);
    void finished(const QString &result);

protected:
    void run() override {
        long long sum = 0;
        long long total = 100000000LL;
        for (long long i = 0; i < total; ++i) {
            if (m_abort) {
                emit finished("已取消");
                return;
            }
            sum += i;
            if (i % (total / 100) == 0) {
                emit progress(i * 100 / total);
                msleep(10);  // 模拟耗时
            }
        }
        emit finished(QString("计算完成，结果: %1").arg(sum));
    }

private:
    bool m_abort;
};

// 方式二：Worker 移动方式（推荐）
class Worker : public QObject {
    Q_OBJECT
public:
    explicit Worker(QObject *parent = nullptr) : QObject(parent) {}

public slots:
    void doWork(const QString &param) {
        qDebug() << "Worker 线程:" << QThread::currentThreadId();
        emit statusChanged("开始处理: " + param);

        // 模拟耗时操作
        for (int i = 0; i <= 100; ++i) {
            QThread::msleep(30);
            emit progress(i);
        }
        emit workFinished("处理完成: " + param);
    }

signals:
    void progress(int percent);
    void statusChanged(const QString &status);
    void workFinished(const QString &result);
};

class ThreadDemo : public QWidget {
    Q_OBJECT
public:
    explicit ThreadDemo(QWidget *parent = nullptr) : QWidget(parent) {
        setWindowTitle("QThread 多线程演示");
        resize(400, 250);

        QVBoxLayout *layout = new QVBoxLayout(this);

        m_label = new QLabel("准备就绪", this);
        m_progressBar = new QProgressBar(this);
        QPushButton *startBtn1 = new QPushButton("方式一：重写 run()", this);
        QPushButton *startBtn2 = new QPushButton("方式二：Worker 移动", this);
        QPushButton *cancelBtn = new QPushButton("取消", this);

        layout->addWidget(m_label);
        layout->addWidget(m_progressBar);
        layout->addWidget(startBtn1);
        layout->addWidget(startBtn2);
        layout->addWidget(cancelBtn);

        // 方式一
        connect(startBtn1, &QPushButton::clicked, this, [this]() {
            m_label->setText("方式一运行中...");
            m_computeThread = new ComputeThread(this);
            connect(m_computeThread, &ComputeThread::progress,
                    m_progressBar, &QProgressBar::setValue);
            connect(m_computeThread, &ComputeThread::finished, this, [this](const QString &result) {
                m_label->setText(result);
                m_computeThread->deleteLater();
            });
            connect(m_computeThread, &QThread::finished, m_computeThread, &QObject::deleteLater);
            m_computeThread->start();
        });

        // 方式二：Worker 移动
        connect(startBtn2, &QPushButton::clicked, this, [this]() {
            m_label->setText("方式二运行中...");

            QThread *workerThread = new QThread(this);
            Worker *worker = new Worker();  // 不设 parent

            // 将 Worker 移动到新线程
            worker->moveToThread(workerThread);

            // 连接信号
            connect(worker, &Worker::progress, m_progressBar, &QProgressBar::setValue);
            connect(worker, &Worker::statusChanged, m_label, &QLabel::setText);
            connect(worker, &Worker::workFinished, this, [this, workerThread](const QString &result) {
                m_label->setText(result);
                workerThread->quit();
            });

            // 线程结束后清理
            connect(workerThread, &QThread::finished, worker, &QObject::deleteLater);
            connect(workerThread, &QThread::finished, workerThread, &QObject::deleteLater);

            workerThread->started();  // 启动线程
            workerThread->start();

            // 通过信号触发 Worker 执行（跨线程自动队列连接）
            QMetaObject::invokeMethod(worker, "doWork", Qt::QueuedConnection,
                                      Q_ARG(QString, "测试数据"));
        });

        // 取消
        connect(cancelBtn, &QPushButton::clicked, this, [this]() {
            if (m_computeThread) m_computeThread->abort();
        });
    }

private:
    QLabel *m_label;
    QProgressBar *m_progressBar;
    ComputeThread *m_computeThread = nullptr;
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);
    qDebug() << "主线程:" << QThread::currentThreadId();
    ThreadDemo w;
    w.show();
    return app.exec();
}

#include "main.moc"
```

#### 总结

- QThread 封装操作系统线程，`run()` 默认启动事件循环 `exec()`
- 方式一：继承 QThread 重写 `run()`，简单但不易用信号槽
- 方式二（推荐）：Worker 继承 QObject，`moveToThread()` 移动到新线程，槽函数在线程中执行
- 跨线程信号槽自动使用队列连接（`Qt::QueuedConnection`），线程安全
- QThread 对象属于创建线程，不要在 Worker 中操作 QThread
- 常见问题：不要在 Worker 中直接 `delete` QThread；用 `deleteLater()` 安全释放；耗时循环中检查取消标志

---

### 第23讲 QtConcurrent 与 QFuture

#### 概念

QtConcurrent 是 Qt 提供的高级并发 API，它封装了线程池和任务调度，使开发者无需手动管理 QThread 即可实现并行计算。配合 QFuture（异步结果）和 QFutureWatcher（结果监听），可以优雅地处理"提交任务-等待结果-更新界面"的异步流程。相比 QThread，QtConcurrent 更适合"数据并行"场景（如对列表每个元素执行相同操作）。

#### 原理

QtConcurrent 基于全局线程池 `QThreadPool`，默认线程数为 CPU 核心数。它提供几个核心函数：

- `QtConcurrent::run()`：在后台线程执行一个函数，返回 `QFuture<T>`
- `QtConcurrent::map()`：对序列每个元素原地修改（无返回值）
- `QtConcurrent::mapped()`：对序列每个元素变换，返回新序列
- `QtConcurrent::filter()` / `filtered()`：过滤序列元素
- `QtConcurrent::mappedReduced()`：映射后归约

QFuture 是异步结果的句柄，支持 `result()`（阻塞获取结果）、`isFinished()`（是否完成）、`waitForFinished()`（阻塞等待）等。QFutureWatcher 用于监听 QFuture 的进度和完成信号，避免阻塞主线程。

#### 例子

```cpp
#include <QApplication>
#include <QWidget>
#include <QVBoxLayout>
#include <QPushButton>
#include <QProgressBar>
#include <QLabel>
#include <QListWidget>
#include <QtConcurrent>
#include <QFuture>
#include <QFutureWatcher>
#include <QElapsedTimer>
#include <QDebug>

// 耗时计算函数
long long fibonacci(int n) {
    if (n <= 1) return n;
    long long a = 0, b = 1;
    for (int i = 2; i <= n; ++i) {
        long long temp = a + b;
        a = b;
        b = temp;
    }
    return b;
}

// 对列表元素做变换
QString processItem(const int &value) {
    QThread::msleep(50);  // 模拟耗时
    return QString("处理结果: %1 -> %2").arg(value).arg(value * value);
}

class ConcurrentDemo : public QWidget {
    Q_OBJECT
public:
    explicit ConcurrentDemo(QWidget *parent = nullptr) : QWidget(parent) {
        setWindowTitle("QtConcurrent 并发演示");
        resize(500, 400);

        QVBoxLayout *layout = new QVBoxLayout(this);

        m_label = new QLabel("就绪", this);
        m_progressBar = new QProgressBar(this);
        m_listWidget = new QListWidget(this);

        QPushButton *runBtn = new QPushButton("run() 异步执行", this);
        QPushButton *mapBtn = new QPushButton("mapped() 并行处理列表", this);
        QPushButton *parallelBtn = new QPushButton("并行计算多个斐波那契", this);

        layout->addWidget(m_label);
        layout->addWidget(m_progressBar);
        layout->addWidget(m_listWidget);
        layout->addWidget(runBtn);
        layout->addWidget(mapBtn);
        layout->addWidget(parallelBtn);

        // 1. QtConcurrent::run() 执行单个任务
        connect(runBtn, &QPushButton::clicked, this, [this]() {
            m_label->setText("run() 执行中...");
            m_progressBar->setRange(0, 0);  // 不确定进度

            QFuture<long long> future = QtConcurrent::run([]() {
                QThread::sleep(2);  // 模拟耗时
                return fibonacci(50);
            });

            QFutureWatcher<long long> *watcher = new QFutureWatcher<long long>(this);
            connect(watcher, &QFutureWatcher<long long>::finished, this, [this, watcher]() {
                m_label->setText(QString("结果: %1").arg(watcher->result()));
                m_progressBar->setRange(0, 100);
                m_progressBar->setValue(100);
                watcher->deleteLater();
            });
            watcher->setFuture(future);
        });

        // 2. QtConcurrent::mapped() 并行处理列表
        connect(mapBtn, &QPushButton::clicked, this, [this]() {
            m_listWidget->clear();
            m_label->setText("mapped() 并行处理中...");

            QList<int> input;
            for (int i = 1; i <= 20; ++i) input << i;

            QFuture<QString> future = QtConcurrent::mapped(input, processItem);

            QFutureWatcher<QString> *watcher = new QFutureWatcher<QString>(this);
            connect(watcher, &QFutureWatcher<QString>::resultReadyAt, this, [this, watcher](int index) {
                m_listWidget->addItem(watcher->resultAt(index));
            });
            connect(watcher, &QFutureWatcher<QString>::finished, this, [this, watcher]() {
                m_label->setText("处理完成");
                watcher->deleteLater();
            });
            watcher->setFuture(future);
        });

        // 3. 并行计算多个任务
        connect(parallelBtn, &QPushButton::clicked, this, [this]() {
            m_label->setText("并行计算多个斐波那契...");
            m_progressBar->setRange(0, 0);

            QList<int> inputs = {40, 45, 50, 42, 48};
            QElapsedTimer timer;
            timer.start();

            QFuture<long long> future = QtConcurrent::mapped(inputs, [](int n) {
                return fibonacci(n);
            });

            QFutureWatcher<long long> *watcher = new QFutureWatcher<long long>(this);
            connect(watcher, &QFutureWatcher<long long>::finished, this, [this, watcher, inputs, timer]() {
                QString result = "并行计算结果:\n";
                for (int i = 0; i < inputs.size(); ++i) {
                    result += QString("  fib(%1) = %2\n").arg(inputs[i]).arg(watcher->resultAt(i));
                }
                result += QString("耗时: %1 ms").arg(timer.elapsed());
                m_label->setText(result);
                m_progressBar->setRange(0, 100);
                m_progressBar->setValue(100);
                watcher->deleteLater();
            });
            watcher->setFuture(future);
        });
    }

private:
    QLabel *m_label;
    QProgressBar *m_progressBar;
    QListWidget *m_listWidget;
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);
    ConcurrentDemo w;
    w.show();
    return app.exec();
}

#include "main.moc"
```

#### 总结

- QtConcurrent 封装线程池，适合数据并行场景，无需手动管理 QThread
- `run()` 执行单个异步任务，`mapped()` 并行处理序列，`filter()` 过滤
- QFuture 是异步结果句柄，`result()` 阻塞获取，`waitForFinished()` 等待完成
- QFutureWatcher 监听进度和完成信号，避免阻塞主线程
- `resultReadyAt(index)` 信号在每个结果就绪时触发，支持流式处理
- 常见问题：QFuture 的 `result()` 会阻塞调用线程；lambda 捕获需注意生命周期

---

### 第24讲 线程同步

#### 概念

多线程编程中，多个线程可能同时访问共享资源（如全局变量、容器），导致数据竞争和未定义行为。线程同步机制确保同一时刻只有一个线程访问临界区。Qt 提供了多种同步原语：QMutex（互斥锁）、QReadWriteLock（读写锁）、QSemaphore（信号量）、QWaitCondition（条件变量）。

#### 原理

**QMutex** 是最基本的互斥锁，提供 `lock()` 和 `unlock()` 方法。同一时刻只有一个线程能获取锁，其他线程阻塞等待。为避免异常导致忘记 `unlock()`，推荐使用 `QMutexLocker`（RAII 模式，构造时加锁，析构时解锁）。

**QReadWriteLock** 区分读锁和写锁：多个线程可同时读，但写操作独占。适合"读多写少"场景，比 QMutex 并发性更好。

**QSemaphore** 是计数信号量，控制同时访问某资源的线程数。初始值 N 表示允许 N 个线程同时访问。

**QWaitCondition** 配合 QMutex 使用，实现"等待-唤醒"模式：线程检查条件不满足时 `wait()` 释放锁并阻塞，其他线程改变条件后 `wakeOne()` / `wakeAll()` 唤醒。

#### 例子

```cpp
#include <QApplication>
#include <QWidget>
#include <QVBoxLayout>
#include <QPushButton>
#include <QLabel>
#include <QThread>
#include <QMutex>
#include <QMutexLocker>
#include <QReadWriteLock>
#include <QSemaphore>
#include <QWaitCondition>
#include <QList>
#include <QDebug>

// 1. QMutex 示例：线程安全的计数器
class SafeCounter {
public:
    void increment() {
        QMutexLocker locker(&m_mutex);  // RAII 加锁
        m_value++;
    }
    int value() const {
        QMutexLocker locker(&m_mutex);
        return m_value;
    }
private:
    mutable QMutex m_mutex;
    int m_value = 0;
};

// 2. 生产者-消费者模式（QWaitCondition）
class BoundedBuffer {
public:
    explicit BoundedBuffer(int maxSize) : m_maxSize(maxSize) {}

    void put(int value) {
        QMutexLocker locker(&m_mutex);
        // 缓冲区满时等待
        m_notFull.wait(&m_mutex, [this]() { return m_buffer.size() < m_maxSize; });
        m_buffer.append(value);
        m_notEmpty.wakeAll();  // 唤醒消费者
    }

    int get() {
        QMutexLocker locker(&m_mutex);
        // 缓冲区空时等待
        m_notEmpty.wait(&m_mutex, [this]() { return !m_buffer.isEmpty(); });
        int value = m_buffer.takeFirst();
        m_notFull.wakeAll();  // 唤醒生产者
        return value;
    }

private:
    QMutex m_mutex;
    QWaitCondition m_notFull;
    QWaitCondition m_notEmpty;
    QList<int> m_buffer;
    int m_maxSize;
};

// 3. QReadWriteLock 示例：缓存
class ThreadSafeCache {
public:
    void write(const QString &key, const QString &value) {
        QWriteLocker locker(&m_lock);  // 写锁，独占
        m_data[key] = value;
    }
    QString read(const QString &key) const {
        QReadLocker locker(&m_lock);  // 读锁，共享
        return m_data.value(key);
    }
private:
    mutable QReadWriteLock m_lock;
    QMap<QString, QString> m_data;
};

// 生产者线程
class Producer : public QThread {
public:
    Producer(BoundedBuffer *buffer, int id) : m_buffer(buffer), m_id(id) {}
protected:
    void run() override {
        for (int i = 0; i < 5; ++i) {
            int value = m_id * 100 + i;
            m_buffer->put(value);
            qDebug() << "生产者" << m_id << "放入:" << value;
            msleep(100);
        }
    }
private:
    BoundedBuffer *m_buffer;
    int m_id;
};

// 消费者线程
class Consumer : public QThread {
public:
    Consumer(BoundedBuffer *buffer, int id) : m_buffer(buffer), m_id(id) {}
protected:
    void run() override {
        for (int i = 0; i < 5; ++i) {
            int value = m_buffer->get();
            qDebug() << "  消费者" << m_id << "取出:" << value;
            msleep(150);
        }
    }
private:
    BoundedBuffer *m_buffer;
    int m_id;
};

class SyncDemo : public QWidget {
    Q_OBJECT
public:
    explicit SyncDemo(QWidget *parent = nullptr) : QWidget(parent) {
        setWindowTitle("线程同步演示");
        resize(300, 200);

        QVBoxLayout *layout = new QVBoxLayout(this);
        m_label = new QLabel("点击开始测试", this);
        QPushButton *btn = new QPushButton("启动生产者-消费者", this);
        layout->addWidget(m_label);
        layout->addWidget(btn);

        connect(btn, &QPushButton::clicked, this, [this]() {
            m_label->setText("运行中，查看控制台输出...");

            BoundedBuffer buffer(3);  // 容量3的缓冲区

            Producer p1(&buffer, 1), p2(&buffer, 2);
            Consumer c1(&buffer, 1), c2(&buffer, 2);

            p1.start(); p2.start();
            c1.start(); c2.start();

            // 等待所有线程完成
            p1.wait(); p2.wait();
            c1.wait(); c2.wait();

            m_label->setText("完成！查看控制台输出");
        });
    }
private:
    QLabel *m_label;
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);
    SyncDemo w;
    w.show();
    return app.exec();
}

#include "main.moc"
```

#### 总结

- QMutex 互斥锁，`QMutexLocker` 实现 RAII 自动加解锁
- QReadWriteLock 读写锁，读多写少场景性能更好，`QReadLocker` / `QWriteLocker`
- QSemaphore 信号量，控制并发访问数量
- QWaitCondition 条件变量，配合 QMutex 实现"等待-唤醒"模式
- 生产者-消费者是经典多线程协作模式，用 QWaitCondition 实现高效等待
- 常见问题：忘记 `unlock()` 导致死锁；`wait()` 会自动释放锁并在唤醒时重新获取；避免在持有锁时执行耗时操作

---

## 第8章 网络、文件与数据

### 第25讲 网络编程

#### 概念

Qt Network 模块提供了跨平台的网络编程接口，支持 TCP/UDP 套接字通信和 HTTP/HTTPS 请求。QTcpSocket 和 QUdpSocket 用于底层网络通信，QNetworkAccessManager 提供高层 HTTP API。本讲演示 TCP 服务端/客户端通信和 HTTP 请求两种常见场景。

#### 原理

**TCP 通信**：TCP 是面向连接的可靠传输协议。服务端使用 QTcpServer 监听端口，客户端用 QTcpSocket 连接服务端。连接建立后，双方通过 `write()` 发送数据，通过 `readyRead()` 信号接收数据。TCP 保证数据按顺序到达，适合文件传输、聊天等场景。

**HTTP 请求**：QNetworkAccessManager 是高层 API，封装了 HTTP/HTTPS 协议细节。通过 `get()`、`post()`、`put()` 等方法发送请求，返回 QNetworkReply 对象。QNetworkReply 是异步的，通过 `finished()` 信号获取响应。这种方式无需手动管理连接和解析协议，适合 RESTful API 调用、文件下载等。

Qt 网络接口都是异步的——不会阻塞调用线程。数据收发通过信号通知，适合在 GUI 应用中使用。

#### 例子

```cpp
#include <QApplication>
#include <QWidget>
#include <QVBoxLayout>
#include <QHBoxLayout>
#include <QPushButton>
#include <QLabel>
#include <QTextEdit>
#include <QLineEdit>
#include <QTcpServer>
#include <QTcpSocket>
#include <QNetworkAccessManager>
#include <QNetworkRequest>
#include <QNetworkReply>
#include <QJsonDocument>
#include <QJsonObject>
#include <QDebug>

// TCP 服务端
class TcpServer : public QWidget {
    Q_OBJECT
public:
    explicit TcpServer(QWidget *parent = nullptr) : QWidget(parent) {
        setWindowTitle("TCP 服务端");
        resize(400, 300);

        QVBoxLayout *layout = new QVBoxLayout(this);
        m_log = new QTextEdit(this);
        m_log->setReadOnly(true);
        m_input = new QLineEdit(this);
        m_input->setPlaceholderText("输入要发送的消息...");
        QPushButton *sendBtn = new QPushButton("发送", this);

        layout->addWidget(new QLabel("服务端日志:"));
        layout->addWidget(m_log);
        layout->addWidget(m_input);
        layout->addWidget(sendBtn);

        m_server = new QTcpServer(this);
        connect(m_server, &QTcpServer::newConnection, this, [this]() {
            m_client = m_server->nextPendingConnection();
            m_log->append("客户端已连接: " + m_client->peerAddress().toString());
            connect(m_client, &QTcpSocket::readyRead, this, [this]() {
                QByteArray data = m_client->readAll();
                m_log->append("收到: " + QString(data));
            });
            connect(m_client, &QTcpSocket::disconnected, this, [this]() {
                m_log->append("客户端断开");
                m_client = nullptr;
            });
        });

        if (m_server->listen(QHostAddress::Any, 8080)) {
            m_log->append("服务端启动，监听端口 8080");
        }

        connect(sendBtn, &QPushButton::clicked, this, [this]() {
            if (m_client && m_client->state() == QAbstractSocket::ConnectedState) {
                m_client->write(m_input->text().toUtf8());
                m_log->append("发送: " + m_input->text());
                m_input->clear();
            }
        });
    }

private:
    QTcpServer *m_server;
    QTcpSocket *m_client = nullptr;
    QTextEdit *m_log;
    QLineEdit *m_input;
};

// TCP 客户端
class TcpClient : public QWidget {
    Q_OBJECT
public:
    explicit TcpClient(QWidget *parent = nullptr) : QWidget(parent) {
        setWindowTitle("TCP 客户端");
        resize(400, 300);

        QVBoxLayout *layout = new QVBoxLayout(this);
        m_log = new QTextEdit(this);
        m_log->setReadOnly(true);
        m_input = new QLineEdit(this);
        m_input->setPlaceholderText("输入消息...");
        QPushButton *connectBtn = new QPushButton("连接", this);
        QPushButton *sendBtn = new QPushButton("发送", this);

        layout->addWidget(m_log);
        layout->addWidget(m_input);
        layout->addWidget(connectBtn);
        layout->addWidget(sendBtn);

        m_socket = new QTcpSocket(this);
        connect(m_socket, &QTcpSocket::connected, this, [this]() {
            m_log->append("已连接到服务端");
        });
        connect(m_socket, &QTcpSocket::readyRead, this, [this]() {
            m_log->append("收到: " + QString(m_socket->readAll()));
        });
        connect(m_socket, &QTcpSocket::disconnected, this, [this]() {
            m_log->append("连接断开");
        });

        connect(connectBtn, &QPushButton::clicked, this, [this]() {
            m_socket->connectToHost("127.0.0.1", 8080);
            m_log->append("正在连接...");
        });
        connect(sendBtn, &QPushButton::clicked, this, [this]() {
            if (m_socket->state() == QAbstractSocket::ConnectedState) {
                m_socket->write(m_input->text().toUtf8());
                m_log->append("发送: " + m_input->text());
                m_input->clear();
            }
        });
    }

private:
    QTcpSocket *m_socket;
    QTextEdit *m_log;
    QLineEdit *m_input;
};

// HTTP 请求演示
class HttpDemo : public QWidget {
    Q_OBJECT
public:
    explicit HttpDemo(QWidget *parent = nullptr) : QWidget(parent) {
        setWindowTitle("HTTP 请求演示");
        resize(400, 300);

        QVBoxLayout *layout = new QVBoxLayout(this);
        m_result = new QTextEdit(this);
        m_result->setReadOnly(true);
        QPushButton *getBtn = new QPushButton("GET 请求", this);
        QPushButton *postBtn = new QPushButton("POST 请求", this);

        layout->addWidget(m_result);
        layout->addWidget(getBtn);
        layout->addWidget(postBtn);

        m_manager = new QNetworkAccessManager(this);

        // GET 请求
        connect(getBtn, &QPushButton::clicked, this, [this]() {
            m_result->append("发送 GET 请求...");
            QUrl url("https://httpbin.org/get");
            QNetworkRequest request(url);
            request.setHeader(QNetworkRequest::UserAgentHeader, "Qt6 Demo");

            QNetworkReply *reply = m_manager->get(request);
            connect(reply, &QNetworkReply::finished, this, [this, reply]() {
                if (reply->error() == QNetworkReply::NoError) {
                    m_result->append("GET 响应:\n" + reply->readAll());
                } else {
                    m_result->append("错误: " + reply->errorString());
                }
                reply->deleteLater();
            });
        });

        // POST 请求（发送 JSON）
        connect(postBtn, &QPushButton::clicked, this, [this]() {
            m_result->append("发送 POST 请求...");

            QJsonObject json;
            json["name"] = "Qt6";
            json["version"] = "6.5";
            json["features"] = QJsonArray{"CMake", "C++17", "QML"};
            QJsonDocument doc(json);

            QNetworkRequest request(QUrl("https://httpbin.org/post"));
            request.setHeader(QNetworkRequest::ContentTypeHeader, "application/json");

            QNetworkReply *reply = m_manager->post(request, doc.toJson());
            connect(reply, &QNetworkReply::finished, this, [this, reply]() {
                if (reply->error() == QNetworkReply::NoError) {
                    m_result->append("POST 响应:\n" + reply->readAll());
                } else {
                    m_result->append("错误: " + reply->errorString());
                }
                reply->deleteLater();
            });
        });
    }

private:
    QNetworkAccessManager *m_manager;
    QTextEdit *m_result;
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    TcpServer server;
    TcpClient client;
    HttpDemo http;
    server.show();
    client.show();
    http.show();

    return app.exec();
}

#include "main.moc"
```

#### 总结

- QTcpServer 监听端口，`newConnection` 信号处理新连接
- QTcpSocket 用于 TCP 通信，`connectToHost()` 连接，`write()` 发送，`readyRead` 信号接收
- QNetworkAccessManager 高层 HTTP API，`get()` / `post()` 发送请求
- QNetworkReply 异步响应，`finished` 信号获取结果，用完 `deleteLater()`
- 网络接口都是异步的，通过信号通知，不阻塞 GUI
- 常见问题：HTTP 请求需在 `.pro` 中加 `QT += network`；reply 必须手动释放（`deleteLater()`）

---

### 第26讲 文件 I/O 与配置

#### 概念

文件 I/O 是应用程序持久化数据的基本方式。Qt 提供了 QFile（文件读写）、QTextStream（文本流）、QDataStream（二进制流）、QDir（目录操作）、QFileInfo（文件信息）等类。QSettings 用于读写应用配置，支持跨平台的注册表/INI 文件存储。本讲演示文件读写和配置管理。

#### 原理

QFile 提供底层文件操作，支持 `open()`、`read()`、`write()`、`close()`。但直接使用 QFile 读写文本需要处理编码问题，推荐使用 QTextStream——它自动处理字符编码（默认 UTF-8），支持 `<<` 和 `>>` 流操作符。

QDataStream 用于二进制数据读写，支持 Qt 类型（QString、QList、QMap 等）的序列化。二进制格式比文本更紧凑高效，适合保存应用数据。

QSettings 是配置管理类，它根据平台自动选择存储方式：Windows 用注册表，macOS 用 plist，Linux 用 INI 文件。开发者用统一的 API 读写配置，无需关心底层差异。`QSettings("OrgName", "AppName")` 创建配置对象，`value()` / `setValue()` 读写配置项。

#### 例子

```cpp
#include <QApplication>
#include <QWidget>
#include <QVBoxLayout>
#include <QPushButton>
#include <QLabel>
#include <QFile>
#include <QTextStream>
#include <QDataStream>
#include <QDir>
#include <QFileInfo>
#include <QSettings>
#include <QDateTime>
#include <QDebug>
#include <QStandardPaths>

class FileIODemo : public QWidget {
    Q_OBJECT
public:
    explicit FileIODemo(QWidget *parent = nullptr) : QWidget(parent) {
        setWindowTitle("文件 I/O 与配置演示");
        resize(400, 400);

        QVBoxLayout *layout = new QVBoxLayout(this);
        m_result = new QLabel("结果将显示在这里", this);
        m_result->setWordWrap(true);
        m_result->setStyleSheet("background: #f5f5f5; padding: 10px;");

        QPushButton *textBtn = new QPushButton("文本文件读写");
        QPushButton *binaryBtn = new QPushButton("二进制文件读写");
        QPushButton *dirBtn = new QPushButton("目录操作");
        QPushButton *settingsBtn = new QPushButton("配置读写");
        QPushButton *loadSettingsBtn = new QPushButton("读取配置");

        layout->addWidget(m_result);
        layout->addWidget(textBtn);
        layout->addWidget(binaryBtn);
        layout->addWidget(dirBtn);
        layout->addWidget(settingsBtn);
        layout->addWidget(loadSettingsBtn);

        // 1. 文本文件读写
        connect(textBtn, &QPushButton::clicked, this, [this]() {
            QString path = QDir::currentPath() + "/test.txt";

            // 写入
            QFile writeFile(path);
            if (writeFile.open(QIODevice::WriteOnly | QIODevice::Text)) {
                QTextStream out(&writeFile);
                out.setEncoding(QStringConverter::Utf8);  // Qt6 设置编码
                out << "Qt6 文件读写演示\n";
                out << "当前时间: " << QDateTime::currentDateTime().toString() << "\n";
                out << "行3: 中文测试\n";
                writeFile.close();
            }

            // 读取
            QFile readFile(path);
            if (readFile.open(QIODevice::ReadOnly | QIODevice::Text)) {
                QTextStream in(&readFile);
                in.setEncoding(QStringConverter::Utf8);
                QString content = in.readAll();
                readFile.close();
                m_result->setText("文本文件内容:\n" + content);
            }
        });

        // 2. 二进制文件读写
        connect(binaryBtn, &QPushButton::clicked, this, [this]() {
            QString path = QDir::currentPath() + "/data.bin";

            // 写入
            QFile writeFile(path);
            if (writeFile.open(QIODevice::WriteOnly)) {
                QDataStream out(&writeFile);
                out.setVersion(QDataStream::Qt_6_0);  // 指定版本保证兼容
                out << QString("测试数据");
                out << 42;
                out << 3.14159;
                out << QStringList{"苹果", "香蕉", "橙子"};
                writeFile.close();
            }

            // 读取
            QFile readFile(path);
            if (readFile.open(QIODevice::ReadOnly)) {
                QDataStream in(&readFile);
                in.setVersion(QDataStream::Qt_6_0);
                QString str;
                int num;
                double pi;
                QStringList list;
                in >> str >> num >> pi >> list;
                readFile.close();
                m_result->setText(QString("二进制读取:\n字符串: %1\n数字: %2\nPI: %3\n列表: %4")
                    .arg(str).arg(num).arg(pi).arg(list.join(", ")));
            }
        });

        // 3. 目录操作
        connect(dirBtn, &QPushButton::clicked, this, [this]() {
            QString docPath = QStandardPaths::writableLocation(
                QStandardPaths::DocumentsLocation);
            QDir dir(docPath);

            QString result = "文档目录: " + docPath + "\n";
            result += "目录是否存在: " + QString(dir.exists() ? "是" : "否") + "\n";

            // 创建子目录
            QString subDir = "Qt6Demo";
            if (!dir.exists(subDir)) {
                dir.mkdir(subDir);
                result += "已创建子目录: " + subDir + "\n";
            }

            // 列出文件
            dir.cd(subDir);
            QStringList files = dir.entryList(QDir::Files);
            result += "子目录文件数: " + QString::number(files.size());

            m_result->setText(result);
        });

        // 4. 写入配置
        connect(settingsBtn, &QPushButton::clicked, this, [this]() {
            QSettings settings("MyCompany", "Qt6Demo");

            settings.beginGroup("User");
            settings.setValue("name", "张三");
            settings.setValue("age", 25);
            settings.setValue("city", "北京");
            settings.endGroup();

            settings.beginGroup("Window");
            settings.setValue("size", QSize(800, 600));
            settings.setValue("pos", QPoint(100, 100));
            settings.setValue("maximized", false);
            settings.endGroup();

            settings.setValue("recentFiles", QStringList{"file1.txt", "file2.txt"});

            m_result->setText("配置已保存到:\n" + settings.fileName());
        });

        // 5. 读取配置
        connect(loadSettingsBtn, &QPushButton::clicked, this, [this]() {
            QSettings settings("MyCompany", "Qt6Demo");

            QString result = "读取配置:\n";
            result += "用户名: " + settings.value("User/name", "默认").toString() + "\n";
            result += "年龄: " + settings.value("User/age", 0).toString() + "\n";
            result += "城市: " + settings.value("User/city").toString() + "\n";
            result += "窗口大小: " + settings.value("Window/size").toSize().toString() + "\n";
            result += "最近文件: " + settings.value("recentFiles").toStringList().join(", ");

            m_result->setText(result);
        });
    }

private:
    QLabel *m_result;
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);
    FileIODemo w;
    w.show();
    return app.exec();
}

#include "main.moc"
```

#### 总结

- QFile 底层文件操作，QTextStream 处理文本（自动编码），QDataStream 处理二进制
- Qt6 中用 `setEncoding(QStringConverter::Utf8)` 替代 Qt5 的 `setCodec()`
- QDataStream 用 `setVersion()` 保证不同 Qt 版本的数据兼容
- QSettings 跨平台配置管理，Windows 用注册表，macOS 用 plist，Linux 用 INI
- `QStandardPaths::writableLocation()` 获取标准目录（文档、下载等）
- 常见问题：文件打开后必须 `close()` 或用 RAII；QSettings 的 `value()` 需提供默认值防止键不存在

---

### 第27讲 JSON/XML/SQLite

#### 概念

现代应用常需处理结构化数据：JSON 用于网络 API 交互，XML 用于配置文件和数据交换，SQLite 用于本地数据存储。Qt 提供了完整的支持：QJsonDocument 等 JSON 类、QXmlStreamReader/Writer XML 类、QSqlDatabase 等 SQL 类。本讲演示三种数据格式的处理。

#### 原理

**JSON 处理**：Qt 的 JSON 类采用"文档-对象-值"层次结构。QJsonDocument 是顶层文档，可从字节流解析或序列化为字节流。QJsonObject 表示键值对集合（类似字典），QJsonArray 表示数组，QJsonValue 是值（可以是字符串、数字、布尔、对象、数组）。Qt6 还引入了 QJsonArray 的更高效实现。

**XML 处理**：Qt 提供两种 XML API——QXmlStreamReader 是流式解析器（SAX 风格，速度快，内存占用低），QDomDocument 是 DOM 树解析器（加载整个文档到内存，便于随机访问）。推荐使用 QXmlStreamReader，它更现代、更高效。

**SQLite 数据库**：Qt 通过 QSqlDatabase 抽象数据库连接，SQLite 是嵌入式数据库无需服务端。QSqlQuery 执行 SQL 语句，支持参数绑定防止 SQL 注入。QSqlTableModel / QSqlRelationalTableModel 提供 Model/View 集成，可直接绑定到 QTableView。

#### 例子

```cpp
#include <QApplication>
#include <QWidget>
#include <QVBoxLayout>
#include <QPushButton>
#include <QLabel>
#include <QJsonDocument>
#include <QJsonObject>
#include <QJsonArray>
#include <QJsonValue>
#include <QXmlStreamReader>
#include <QXmlStreamWriter>
#include <QSqlDatabase>
#include <QSqlQuery>
#include <QSqlError>
#include <QSqlTableModel>
#include <QTableView>
#include <QDebug>

class DataDemo : public QWidget {
    Q_OBJECT
public:
    explicit DataDemo(QWidget *parent = nullptr) : QWidget(parent) {
        setWindowTitle("JSON/XML/SQLite 演示");
        resize(500, 500);

        QVBoxLayout *layout = new QVBoxLayout(this);
        m_result = new QLabel(this);
        m_result->setWordWrap(true);
        m_result->setStyleSheet("background: #f5f5f5; padding: 10px;");

        QPushButton *jsonBtn = new QPushButton("JSON 处理");
        QPushButton *xmlBtn = new QPushButton("XML 处理");
        QPushButton *dbBtn = new QPushButton("SQLite 数据库");

        layout->addWidget(m_result);
        layout->addWidget(jsonBtn);
        layout->addWidget(xmlBtn);
        layout->addWidget(dbBtn);

        // 1. JSON 处理
        connect(jsonBtn, &QPushButton::clicked, this, [this]() {
            // 构建 JSON
            QJsonObject root;
            root["name"] = "Qt6 教程";
            root["version"] = "6.5.0";
            root["price"] = 99.5;
            root["published"] = true;

            QJsonArray chapters;
            chapters.append("入门");
            chapters.append("核心机制");
            chapters.append("实战");
            root["chapters"] = chapters;

            QJsonObject author;
            author["name"] = "张三";
            author["email"] = "zhangsan@example.com";
            root["author"] = author;

            QJsonDocument doc(root);
            QString jsonStr = doc.toJson(QJsonDocument::Indented);
            qDebug() << "JSON:" << jsonStr;

            // 解析 JSON
            QJsonDocument parsed = QJsonDocument::fromJson(jsonStr.toUtf8());
            QJsonObject obj = parsed.object();

            QString result = "JSON 解析结果:\n";
            result += "书名: " + obj["name"].toString() + "\n";
            result += "版本: " + obj["version"].toString() + "\n";
            result += "价格: " + QString::number(obj["price"].toDouble()) + "\n";
            result += "已出版: " + QString(obj["published"].toBool() ? "是" : "否") + "\n";
            result += "章节: ";
            QJsonArray chs = obj["chapters"].toArray();
            for (const QJsonValue &ch : chs) {
                result += ch.toString() + " ";
            }
            result += "\n作者: " + obj["author"].toObject()["name"].toString();

            m_result->setText(result);
        });

        // 2. XML 处理
        connect(xmlBtn, &QPushButton::clicked, this, [this]() {
            // 生成 XML
            QString xmlStr;
            QXmlStreamWriter writer(&xmlStr);
            writer.setAutoFormatting(true);
            writer.writeStartDocument();
            writer.writeStartElement("bookstore");

            writer.writeStartElement("book");
            writer.writeAttribute("category", "编程");
            writer.writeTextElement("title", "Qt6 教程");
            writer.writeTextElement("author", "张三");
            writer.writeTextElement("price", "99.5");
            writer.writeEndElement();  // book

            writer.writeStartElement("book");
            writer.writeAttribute("category", "设计");
            writer.writeTextElement("title", "UI 设计原则");
            writer.writeTextElement("author", "李四");
            writer.writeTextElement("price", "79.0");
            writer.writeEndElement();  // book

            writer.writeEndElement();  // bookstore
            writer.writeEndDocument();

            qDebug() << "XML:" << xmlStr;

            // 解析 XML
            QXmlStreamReader reader(xmlStr);
            QString result = "XML 解析结果:\n";

            while (!reader.atEnd()) {
                QXmlStreamReader::TokenType type = reader.readNext();
                if (type == QXmlStreamReader::StartElement) {
                    if (reader.name() == "book") {
                        result += "类别: " + reader.attributes().value("category") + "\n";
                    } else if (reader.name() == "title") {
                        result += "  书名: " + reader.readElementText() + "\n";
                    } else if (reader.name() == "author") {
                        result += "  作者: " + reader.readElementText() + "\n";
                    } else if (reader.name() == "price") {
                        result += "  价格: " + reader.readElementText() + "\n";
                    }
                }
            }
            if (reader.hasError()) {
                result += "解析错误: " + reader.errorString();
            }

            m_result->setText(result);
        });

        // 3. SQLite 数据库
        connect(dbBtn, &QPushButton::clicked, this, [this]() {
            QSqlDatabase db = QSqlDatabase::addDatabase("QSQLITE");
            db.setDatabaseName("test.db");

            if (!db.open()) {
                m_result->setText("数据库打开失败: " + db.lastError().text());
                return;
            }

            // 创建表
            QSqlQuery query;
            query.exec("CREATE TABLE IF NOT EXISTS students ("
                       "id INTEGER PRIMARY KEY AUTOINCREMENT, "
                       "name TEXT NOT NULL, "
                       "age INTEGER, "
                       "score REAL)");

            // 插入数据（参数绑定）
            query.prepare("INSERT INTO students (name, age, score) VALUES (?, ?, ?)");
            query.addBindValue("张三");
            query.addBindValue(20);
            query.addBindValue(85.5);
            query.exec();

            query.addBindValue("李四");
            query.addBindValue(21);
            query.addBindValue(92.0);
            query.exec();

            // 查询数据
            QString result = "数据库查询结果:\n";
            query.exec("SELECT * FROM students");
            while (query.next()) {
                result += QString("ID:%1 姓名:%2 年龄:%3 成绩:%4\n")
                    .arg(query.value(0).toInt())
                    .arg(query.value(1).toString())
                    .arg(query.value(2).toInt())
                    .arg(query.value(3).toDouble());
            }

            m_result->setText(result);
        });
    }

private:
    QLabel *m_result;
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);
    DataDemo w;
    w.show();
    return app.exec();
}

#include "main.moc"
```

#### 总结

- JSON：QJsonDocument（文档）、QJsonObject（对象）、QJsonArray（数组）、QJsonValue（值）
- `fromJson()` 解析，`toJson()` 序列化，支持紧凑和缩进格式
- XML：QXmlStreamWriter 生成，QXmlStreamReader 流式解析（推荐，高效低内存）
- SQLite：QSqlDatabase 连接，QSqlQuery 执行 SQL，`prepare()` + `addBindValue()` 防注入
- QSqlTableModel 可直接绑定 QTableView，实现数据库的 Model/View 展示
- 常见问题：JSON 值类型用 `isString()` / `isDouble()` 等判断；SQL 语句执行后检查 `lastError()`

---

## 第9章 现代 Qt 与实战

### 第28讲 QML 与 Qt Quick 入门

#### 概念

QML（Qt Modeling Language）是 Qt 提供的声明式 UI 描述语言，基于 JavaScript 语法，专为构建流畅的动态界面设计。Qt Quick 是 QML 的核心模块，提供了 Rectangle、Text、Image、MouseArea、ListView 等基础元素。QML 与 C++ 各有优势：QML 适合快速构建动画丰富的现代 UI，C++ 适合处理性能敏感的逻辑。两者通过信号槽和属性系统无缝集成。

#### 原理

QML 采用声明式编程范式——开发者描述"界面应该是什么样"，而非"如何创建界面"。QML 文件以 `.qml` 为扩展名，根元素通常是 Item 或其子类。每个 QML 元素有属性（如 `width`、`color`）、信号（如 `onClicked`）和方法。

QML 的核心特性是**属性绑定**：属性值可以是表达式，当依赖的属性变化时自动重新计算。例如 `width: parent.width / 2` 表示宽度始终是父元素的一半，父元素宽度变化时自动更新。这种响应式编程使 UI 状态管理极为简洁。

QML 与 C++ 的交互方式：
1. **C++ 暴露给 QML**：将 QObject 对象注册为 QML 上下文属性或 QML 类型，QML 可直接访问其属性和方法
2. **QML 调用 C++**：QML 通过对象名访问 C++ 对象的属性和方法
3. **信号槽跨语言**：C++ 信号可在 QML 中通过 `Connections` 处理，QML 信号可连接到 C++ 槽

#### 例子

**main.cpp - C++ 端**：
```cpp
#include <QGuiApplication>
#include <QQmlApplicationEngine>
#include <QQmlContext>
#include <QObject>
#include <QDateTime>

// 暴露给 QML 的 C++ 数据模型
class AppController : public QObject {
    Q_OBJECT
    Q_PROPERTY(QString currentTime READ currentTime NOTIFY timeChanged)
    Q_PROPERTY(int clickCount READ clickCount WRITE setClickCount NOTIFY clickCountChanged)

public:
    explicit AppController(QObject *parent = nullptr) : QObject(parent), m_clickCount(0) {
        // 每秒更新时间
        connect(&m_timer, &QTimer::timeout, this, [this]() {
            m_currentTime = QDateTime::currentDateTime().toString("hh:mm:ss");
            emit timeChanged();
        });
        m_timer.start(1000);
        m_currentTime = QDateTime::currentDateTime().toString("hh:mm:ss");
    }

    QString currentTime() const { return m_currentTime; }
    int clickCount() const { return m_clickCount; }
    void setClickCount(int count) {
        if (m_clickCount != count) {
            m_clickCount = count;
            emit clickCountChanged();
        }
    }

    // QML 可调用的方法
    Q_INVOKABLE QString greet(const QString &name) {
        return "你好, " + name + "! 当前点击次数: " + QString::number(m_clickCount);
    }

signals:
    void timeChanged();
    void clickCountChanged();

private:
    QTimer m_timer;
    QString m_currentTime;
    int m_clickCount;
};

int main(int argc, char *argv[]) {
    QGuiApplication app(argc, argv);

    QQmlApplicationEngine engine;

    AppController controller;
    // 将 C++ 对象暴露给 QML
    engine.rootContext()->setContextProperty("appController", &controller);

    engine.load(QUrl(QStringLiteral("qrc:/main.qml")));

    if (engine.rootObjects().isEmpty())
        return -1;

    return app.exec();
}

#include "main.moc"
```

**main.qml - QML 端**：
```qml
import QtQuick 2.15
import QtQuick.Controls 2.15
import QtQuick.Layouts 1.15

ApplicationWindow {
    visible: true
    width: 400
    height: 300
    title: "QML 与 C++ 交互演示"

    ColumnLayout {
        anchors.centerIn: parent
        spacing: 20

        // 显示 C++ 提供的时间（属性绑定自动更新）
        Text {
            text: "当前时间: " + appController.currentTime
            font.pixelSize: 24
            Layout.alignment: Qt.AlignHCenter
        }

        // 点击计数器
        Text {
            text: "点击次数: " + appController.clickCount
            font.pixelSize: 18
            Layout.alignment: Qt.AlignHCenter
        }

        // 按钮：点击增加计数
        Button {
            text: "点击我"
            Layout.alignment: Qt.AlignHCenter
            onClicked: {
                appController.clickCount = appController.clickCount + 1
            }
        }

        // 调用 C++ 方法
        Button {
            text: "调用 C++ 方法"
            Layout.alignment: Qt.AlignHCenter
            onClicked: {
                var result = appController.greet("Qt6 学习者")
                greetingLabel.text = result
            }
        }

        Text {
            id: greetingLabel
            text: ""
            font.pixelSize: 16
            color: "blue"
            Layout.alignment: Qt.AlignHCenter
        }
    }
}
```

**CMakeLists.txt**：
```cmake
cmake_minimum_required(VERSION 3.16)
project(QmlDemo)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_AUTOMOC ON)

find_package(Qt6 COMPONENTS Quick QuickControls2 REQUIRED)

qt_add_executable(QmlDemo main.cpp)
qt_add_qml_module(QmlDemo
    URI QmlDemo
    VERSION 1.0
    QML_FILES main.qml
)

target_link_libraries(QmlDemo PRIVATE
    Qt6::Quick
    Qt6::QuickControls2
)
```

#### 总结

- QML 是声明式 UI 语言，基于 JavaScript 语法，适合构建动态界面
- 属性绑定是核心特性：`width: parent.width / 2` 自动响应依赖变化
- C++ 通过 `setContextProperty()` 或 `qmlRegisterType()` 暴露给 QML
- `Q_PROPERTY` 声明的属性可在 QML 中直接访问，`Q_INVOKABLE` 方法可被 QML 调用
- QML 适合 UI 层，C++ 适合逻辑层，两者通过信号槽和属性系统无缝集成
- 常见问题：QML 性能敏感操作应放 C++；属性绑定循环依赖会导致警告

---

### 第29讲 Qt6 新特性与最佳实践

#### 概念

Qt6 相比 Qt5 引入了多项重要改进：强制 C++17、全面采用 CMake、新的图形架构（RHI）、3D 模块升级、QML 性能优化等。本讲总结 Qt6 的关键新特性，并介绍 Qt 开发的最佳实践，帮助开发者写出高质量、可维护的 Qt 代码。

#### 原理

**Qt6 核心新特性**：

1. **C++17 强制要求**：Qt6 大量使用 C++17 特性（如 `std::optional`、`std::variant`、结构化绑定），代码更现代简洁。`QString`、`QList` 等核心类与 C++ 标准库更好地集成。

2. **CMake 构建系统**：Qt6 放弃 qmake，全面采用 CMake。新增 `qt_add_executable()`、`qt_add_qml_module()` 等 Qt 专用命令，简化构建配置。CMake 的跨平台性和扩展性优于 qmake。

3. **RHI（Rendering Hardware Interface）**：Qt6 引入新的图形抽象层，支持 Vulkan、Metal、Direct3D、OpenGL 后端，无需修改代码即可在不同图形 API 间切换。这解决了 Qt5 中 OpenGL 绑定过深的问题。

4. **QList 性能优化**：Qt6 的 QList 统一了 QList 和 QVector，默认分配策略更高效，减少内存拷贝。

5. **QString 转换改进**：用 `QStringConverter` 替代 `QTextCodec`，编码转换更清晰。

**最佳实践**：

1. **内存管理**：优先使用父子对象树（`setParent()`），让父对象自动管理子对象生命周期。避免裸指针，用 `QScopedPointer` 或智能指针管理非 QObject 对象。

2. **信号槽连接**：优先使用函数指针语法 `connect(sender, &Sender::signal, receiver, &Receiver::slot)`，编译期类型检查。Lambda 适合简单逻辑，注意捕获生命周期。

3. **线程安全**：UI 操作只能在主线程，耗时操作放子线程。跨线程通信用信号槽（自动队列连接），不要直接操作其他线程的对象。

4. **资源管理**：用 `.qrc` 资源文件管理图片、QML 等静态资源，编译时嵌入可执行文件。

#### 例子

```cpp
#include <QApplication>
#include <QWidget>
#include <QVBoxLayout>
#include <QLabel>
#include <QPushButton>
#include <QTimer>
#include <QPointer>
#include <QScopedPointer>
#include <QImage>
#include <optional>
#include <variant>
#include <QDebug>

// 1. C++17 特性在 Qt6 中的应用
class ModernQtDemo : public QWidget {
    Q_OBJECT
public:
    explicit ModernQtDemo(QWidget *parent = nullptr) : QWidget(parent) {
        setWindowTitle("Qt6 新特性与最佳实践");
        resize(500, 400);

        QVBoxLayout *layout = new QVBoxLayout(this);

        // 使用 std::optional 处理可能失败的操作
        auto findUser = [](int id) -> std::optional<QString> {
            if (id == 1) return "张三";
            if (id == 2) return "李四";
            return std::nullopt;
        };

        // 使用 std::variant 处理多种类型
        using Value = std::variant<int, double, QString>;
        Value v1 = 42;
        Value v2 = 3.14;
        Value v3 = QString("文本");

        // 结构化绑定遍历 QMap
        QMap<QString, int> scores = {{"语文", 90}, {"数学", 85}, {"英语", 92}};
        QString result = "成绩单:\n";
        for (auto [subject, score] : scores.asKeyValueRange()) {
            result += QString("  %1: %2 分\n").arg(subject).arg(score);
        }

        // std::optional 使用
        auto user = findUser(1);
        if (user.has_value()) {
            result += QString("找到用户: %1\n").arg(user.value());
        }

        // std::variant 使用
        std::visit([&result](auto &&arg) {
            using T = std::decay_t<decltype(arg)>;
            if constexpr (std::is_same_v<T, int>) {
                result += QString("整数: %1\n").arg(arg);
            } else if constexpr (std::is_same_v<T, double>) {
                result += QString("浮点数: %1\n").arg(arg);
            } else if constexpr (std::is_same_v<T, QString>) {
                result += QString("字符串: %1\n").arg(arg);
            }
        }, v3);

        m_label = new QLabel(result, this);
        m_label->setWordWrap(true);
        layout->addWidget(m_label);

        QPushButton *btn = new QPushButton("演示 QPointer", this);
        layout->addWidget(btn);

        // 2. QPointer：指向 QObject 的弱指针，对象销毁后自动置空
        connect(btn, &QPushButton::clicked, this, [this]() {
            QPointer<QLabel> safePtr = new QLabel("临时标签", this);
            qDebug() << "创建前:" << safePtr.isNull();  // false

            delete safePtr;  // 安全删除
            qDebug() << "删除后:" << safePtr.isNull();  // true
        });
    }

private:
    QLabel *m_label;
};

// 3. RAII 资源管理示例
class ResourceManager {
public:
    void processImage(const QString &path) {
        // QScopedPointer 自动管理堆对象
        QScopedPointer<QImage> image(new QImage(path));
        if (image->isNull()) {
            qWarning() << "图片加载失败:" << path;
            return;
        }
        // 使用 image...
        qDebug() << "图片尺寸:" << image->size();
        // 函数结束自动 delete，无需手动释放
    }
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    // Qt6 推荐的高 DPI 支持（自动启用）
    qDebug() << "Qt 版本:" << QT_VERSION_STR;

    ModernQtDemo w;
    w.show();

    return app.exec();
}

#include "main.moc"
```

#### 总结

- Qt6 强制 C++17，全面采用 CMake，引入 RHI 图形抽象层
- QList 统一了 QVector，QStringConverter 替代 QTextCodec
- 最佳实践：父子对象树管理内存、函数指针语法连接信号槽、耗时操作放子线程
- QPointer 是 QObject 的弱指针，对象销毁后自动置空，安全
- QScopedPointer 实现 RAII 资源管理，避免内存泄漏
- 常见问题：不要在子线程操作 UI；lambda 捕获 this 需注意对象生命周期；用 `Q_DECLARE_METATYPE` 注册自定义类型用于信号槽

---

### 第30讲 项目实战：完整应用开发

#### 概念

本讲作为课程总结，将前面所学的知识整合，开发一个完整的"任务管理器"应用。该应用涵盖：主窗口架构、自定义模型、数据库持久化、多线程、网络请求、配置管理等核心知识点。通过这个实战项目，巩固 Qt6 的综合应用能力。

#### 原理

完整 Qt 应用开发流程：

1. **需求分析**：明确功能需求（任务增删改查、状态管理、数据持久化）
2. **架构设计**：MVC 分层——Model（数据层）、View（界面层）、Controller（业务逻辑）
3. **UI 设计**：主窗口布局、控件选择、交互流程
4. **数据层实现**：SQLite 数据库、自定义模型
5. **业务逻辑**：任务管理、状态转换、异步操作
6. **测试与优化**：单元测试、性能优化、异常处理

项目结构：
```
TaskManager/
├── CMakeLists.txt
├── main.cpp
├── mainwindow.h/cpp      # 主窗口
├── taskmodel.h/cpp       # 自定义数据模型
├── taskdatabase.h/cpp    # 数据库操作层
├── taskworker.h/cpp      # 后台任务处理
└── mainwindow.ui         # 可选：Qt Designer 文件
```

#### 例子

由于完整项目代码较长，这里展示核心模块的关键代码：

**task.h - 数据结构**：
```cpp
#pragma once
#include <QString>
#include <QDateTime>

struct Task {
    int id = 0;
    QString title;
    QString description;
    QString status = "待办";  // 待办/进行中/已完成
    int priority = 1;         // 1-高, 2-中, 3-低
    QDateTime createdAt;
    QDateTime deadline;
};
```

**taskdatabase.h - 数据库层**：
```cpp
#pragma once
#include <QSqlDatabase>
#include <QList>
#include "task.h"

class TaskDatabase {
public:
    static TaskDatabase& instance();

    bool init(const QString &dbName = "tasks.db");
    bool addTask(Task &task);        // 插入并填充 id
    bool updateTask(const Task &task);
    bool deleteTask(int id);
    QList<Task> getAllTasks() const;
    QList<Task> getTasksByStatus(const QString &status) const;

private:
    TaskDatabase() = default;
    QSqlDatabase m_db;
};
```

**taskdatabase.cpp - 实现**：
```cpp
#include "taskdatabase.h"
#include <QSqlQuery>
#include <QSqlError>
#include <QDebug>

TaskDatabase& TaskDatabase::instance() {
    static TaskDatabase instance;
    return instance;
}

bool TaskDatabase::init(const QString &dbName) {
    m_db = QSqlDatabase::addDatabase("QSQLITE");
    m_db.setDatabaseName(dbName);
    if (!m_db.open()) {
        qCritical() << "数据库打开失败:" << m_db.lastError().text();
        return false;
    }

    QSqlQuery query;
    bool ok = query.exec(
        "CREATE TABLE IF NOT EXISTS tasks ("
        "id INTEGER PRIMARY KEY AUTOINCREMENT, "
        "title TEXT NOT NULL, "
        "description TEXT, "
        "status TEXT DEFAULT '待办', "
        "priority INTEGER DEFAULT 2, "
        "created_at TEXT, "
        "deadline TEXT)"
    );
    if (!ok) {
        qCritical() << "建表失败:" << query.lastError().text();
        return false;
    }
    return true;
}

bool TaskDatabase::addTask(Task &task) {
    QSqlQuery query;
    query.prepare("INSERT INTO tasks (title, description, status, priority, created_at, deadline) "
                  "VALUES (?, ?, ?, ?, ?, ?)");
    query.addBindValue(task.title);
    query.addBindValue(task.description);
    query.addBindValue(task.status);
    query.addBindValue(task.priority);
    query.addBindValue(task.createdAt.toString(Qt::ISODate));
    query.addBindValue(task.deadline.toString(Qt::ISODate));

    if (!query.exec()) {
        qWarning() << "插入失败:" << query.lastError().text();
        return false;
    }
    task.id = query.lastInsertId().toInt();
    return true;
}

bool TaskDatabase::updateTask(const Task &task) {
    QSqlQuery query;
    query.prepare("UPDATE tasks SET title=?, description=?, status=?, priority=?, deadline=? "
                  "WHERE id=?");
    query.addBindValue(task.title);
    query.addBindValue(task.description);
    query.addBindValue(task.status);
    query.addBindValue(task.priority);
    query.addBindValue(task.deadline.toString(Qt::ISODate));
    query.addBindValue(task.id);
    return query.exec();
}

bool TaskDatabase::deleteTask(int id) {
    QSqlQuery query;
    query.prepare("DELETE FROM tasks WHERE id=?");
    query.addBindValue(id);
    return query.exec();
}

QList<Task> TaskDatabase::getAllTasks() const {
    QList<Task> tasks;
    QSqlQuery query("SELECT id, title, description, status, priority, created_at, deadline FROM tasks ORDER BY priority, created_at DESC");
    while (query.next()) {
        Task t;
        t.id = query.value(0).toInt();
        t.title = query.value(1).toString();
        t.description = query.value(2).toString();
        t.status = query.value(3).toString();
        t.priority = query.value(4).toInt();
        t.createdAt = QDateTime::fromString(query.value(5).toString(), Qt::ISODate);
        t.deadline = QDateTime::fromString(query.value(6).toString(), Qt::ISODate);
        tasks.append(t);
    }
    return tasks;
}
```

**taskmodel.h - 自定义模型**：
```cpp
#pragma once
#include <QAbstractTableModel>
#include <QList>
#include "task.h"

class TaskModel : public QAbstractTableModel {
    Q_OBJECT
public:
    enum Columns { Id, Title, Status, Priority, Deadline, ColumnCount };
    enum Roles { TitleRole = Qt::UserRole + 1, StatusRole, PriorityRole };

    explicit TaskModel(QObject *parent = nullptr);

    void setTasks(const QList<Task> &tasks);
    void addTask(const Task &task);
    void removeTask(int row);
    Task getTask(int row) const;

    int rowCount(const QModelIndex &parent = QModelIndex()) const override;
    int columnCount(const QModelIndex &parent = QModelIndex()) const override;
    QVariant data(const QModelIndex &index, int role = Qt::DisplayRole) const override;
    QVariant headerData(int section, Qt::Orientation orientation, int role = Qt::DisplayRole) const override;
    Qt::ItemFlags flags(const QModelIndex &index) const override;

private:
    QList<Task> m_tasks;
};
```

**taskmodel.cpp - 实现**：
```cpp
#include "taskmodel.h"
#include <QColor>

TaskModel::TaskModel(QObject *parent) : QAbstractTableModel(parent) {}

void TaskModel::setTasks(const QList<Task> &tasks) {
    beginResetModel();
    m_tasks = tasks;
    endResetModel();
}

void TaskModel::addTask(const Task &task) {
    int row = m_tasks.size();
    beginInsertRows(QModelIndex(), row, row);
    m_tasks.append(task);
    endInsertRows();
}

void TaskModel::removeTask(int row) {
    if (row < 0 || row >= m_tasks.size()) return;
    beginRemoveRows(QModelIndex(), row, row);
    m_tasks.removeAt(row);
    endRemoveRows();
}

Task TaskModel::getTask(int row) const {
    if (row < 0 || row >= m_tasks.size()) return Task();
    return m_tasks.at(row);
}

int TaskModel::rowCount(const QModelIndex &parent) const {
    Q_UNUSED(parent);
    return m_tasks.size();
}

int TaskModel::columnCount(const QModelIndex &parent) const {
    Q_UNUSED(parent);
    return ColumnCount;
}

QVariant TaskModel::data(const QModelIndex &index, int role) const {
    if (!index.isValid() || index.row() >= m_tasks.size())
        return QVariant();

    const Task &task = m_tasks[index.row()];

    if (role == Qt::DisplayRole) {
        switch (index.column()) {
            case Title: return task.title;
            case Status: return task.status;
            case Priority: {
                QStringList names = {"", "高", "中", "低"};
                return names.value(task.priority);
            }
            case Deadline: return task.deadline.toString("yyyy-MM-dd");
        }
    } else if (role == Qt::BackgroundRole) {
        // 根据状态着色
        if (task.status == "已完成") return QColor(220, 255, 220);
        if (task.status == "进行中") return QColor(255, 255, 220);
        if (task.deadline < QDateTime::currentDateTime() && task.status != "已完成")
            return QColor(255, 220, 220);  // 过期标红
    } else if (role == Qt::TextAlignmentRole) {
        return Qt::AlignCenter;
    }
    return QVariant();
}

QVariant TaskModel::headerData(int section, Qt::Orientation orientation, int role) const {
    if (role != Qt::DisplayRole || orientation != Qt::Horizontal)
        return QVariant();
    QStringList headers = {"ID", "标题", "状态", "优先级", "截止日期"};
    return headers.value(section);
}

Qt::ItemFlags TaskModel::flags(const QModelIndex &index) const {
    if (!index.isValid()) return Qt::NoItemFlags;
    return Qt::ItemIsEnabled | Qt::ItemIsSelectable;
}
```

**mainwindow.cpp - 主窗口（核心逻辑）**：
```cpp
#include "mainwindow.h"
#include "taskdatabase.h"
#include <QMenuBar>
#include <QToolBar>
#include <QStatusBar>
#include <QHeaderView>
#include <QInputDialog>
#include <QMessageBox>
#include <QFormLayout>
#include <QLineEdit>
#include <QComboBox>
#include <QDateTimeEdit>
#include <QTextEdit>
#include <QThread>
#include <QJsonDocument>
#include <QNetworkAccessManager>
#include <QNetworkRequest>
#include <QNetworkReply>

MainWindow::MainWindow(QWidget *parent) : QMainWindow(parent) {
    setWindowTitle("任务管理器");
    resize(800, 600);

    // 初始化数据库
    TaskDatabase::instance().init();

    setupUI();
    setupMenu();
    setupToolbar();
    refreshTasks();
}

void MainWindow::setupUI() {
    m_model = new TaskModel(this);
    m_tableView = new QTableView(this);
    m_tableView->setModel(m_model);
    m_tableView->setSelectionBehavior(QAbstractItemView::SelectRows);
    m_tableView->setSelectionMode(QAbstractItemView::SingleSelection);
    m_tableView->horizontalHeader()->setSectionResizeMode(QHeaderView::Stretch);
    m_tableView->setAlternatingRowColors(true);
    m_tableView->setStyleSheet("alternate-background-color: #f9f9f9;");
    m_tableView->setContextMenuPolicy(Qt::CustomContextMenu);

    connect(m_tableView, &QTableView::doubleClicked, this, &MainWindow::onEditTask);
    connect(m_tableView, &QWidget::customContextMenuRequested, this, &MainWindow::showContextMenu);

    setCentralWidget(m_tableView);
    statusBar()->showMessage("就绪");
}

void MainWindow::setupMenu() {
    QMenu *fileMenu = menuBar()->addMenu("文件(&F)");
    QAction *exitAction = fileMenu->addAction("退出");
    exitAction->setShortcut(QKeySequence::Quit);
    connect(exitAction, &QAction::triggered, qApp, &QApplication::quit);

    QMenu *taskMenu = menuBar()->addMenu("任务(&T)");
    QAction *addAction = taskMenu->addAction("添加任务");
    addAction->setShortcut(QKeySequence::New);
    connect(addAction, &QAction::triggered, this, &MainWindow::onAddTask);

    QAction *syncAction = taskMenu->addAction("同步到云端");
    connect(syncAction, &QAction::triggered, this, &MainWindow::onSyncToCloud);
}

void MainWindow::setupToolbar() {
    QToolBar *toolbar = addToolBar("主工具栏");
    toolbar->addAction("添加", this, &MainWindow::onAddTask);
    toolbar->addAction("编辑", this, &MainWindow::onEditTask);
    toolbar->addAction("删除", this, &MainWindow::onDeleteTask);
    toolbar->addSeparator();
    toolbar->addAction("标记完成", this, &MainWindow::onMarkComplete);
    toolbar->addSeparator();
    toolbar->addAction("刷新", this, &MainWindow::refreshTasks);
}

void MainWindow::refreshTasks() {
    QList<Task> tasks = TaskDatabase::instance().getAllTasks();
    m_model->setTasks(tasks);
    statusBar()->showMessage(QString("共 %1 个任务").arg(tasks.size()), 3000);
}

void MainWindow::onAddTask() {
    QDialog dialog(this);
    dialog.setWindowTitle("添加任务");
    QFormLayout *form = new QFormLayout(&dialog);

    QLineEdit *titleEdit = new QLineEdit(&dialog);
    QTextEdit *descEdit = new QTextEdit(&dialog);
    descEdit->setMaximumHeight(80);
    QComboBox *statusCombo = new QComboBox(&dialog);
    statusCombo->addItems({"待办", "进行中", "已完成"});
    QComboBox *priorityCombo = new QComboBox(&dialog);
    priorityCombo->addItems({"高", "中", "低"});
    priorityCombo->setCurrentIndex(1);
    QDateTimeEdit *deadlineEdit = new QDateTimeEdit(QDateTime::currentDateTime().addDays(7), &dialog);
    deadlineEdit->setDisplayFormat("yyyy-MM-dd HH:mm");

    form->addRow("标题:", titleEdit);
    form->addRow("描述:", descEdit);
    form->addRow("状态:", statusCombo);
    form->addRow("优先级:", priorityCombo);
    form->addRow("截止日期:", deadlineEdit);

    QDialogButtonBox *buttons = new QDialogButtonBox(
        QDialogButtonBox::Ok | QDialogButtonBox::Cancel, &dialog);
    form->addRow(buttons);
    connect(buttons, &QDialogButtonBox::accepted, &dialog, &QDialog::accept);
    connect(buttons, &QDialogButtonBox::rejected, &dialog, &QDialog::reject);

    if (dialog.exec() == QDialog::Accepted) {
        Task task;
        task.title = titleEdit->text();
        task.description = descEdit->toPlainText();
        task.status = statusCombo->currentText();
        task.priority = priorityCombo->currentIndex() + 1;
        task.deadline = deadlineEdit->dateTime();
        task.createdAt = QDateTime::currentDateTime();

        if (TaskDatabase::instance().addTask(task)) {
            m_model->addTask(task);
            statusBar()->showMessage("任务已添加", 2000);
        }
    }
}

void MainWindow::onEditTask() {
    int row = m_tableView->currentIndex().row();
    if (row < 0) return;

    Task task = m_model->getTask(row);
    // 类似添加对话框，预填数据，保存后更新数据库和模型
    // ...（代码省略，逻辑类似 onAddTask）
    QMessageBox::information(this, "提示", "编辑功能实现类似添加");
}

void MainWindow::onDeleteTask() {
    int row = m_tableView->currentIndex().row();
    if (row < 0) return;

    Task task = m_model->getTask(row);
    auto reply = QMessageBox::question(this, "确认删除",
        QString("确定删除任务 \"%1\" 吗?").arg(task.title));
    if (reply == QMessageBox::Yes) {
        if (TaskDatabase::instance().deleteTask(task.id)) {
            m_model->removeTask(row);
            statusBar()->showMessage("任务已删除", 2000);
        }
    }
}

void MainWindow::onMarkComplete() {
    int row = m_tableView->currentIndex().row();
    if (row < 0) return;

    Task task = m_model->getTask(row);
    task.status = "已完成";
    if (TaskDatabase::instance().updateTask(task)) {
        refreshTasks();
        statusBar()->showMessage("任务已标记完成", 2000);
    }
}

void MainWindow::showContextMenu(const QPoint &pos) {
    QModelIndex index = m_tableView->indexAt(pos);
    if (!index.isValid()) return;

    QMenu menu(this);
    menu.addAction("编辑", this, &MainWindow::onEditTask);
    menu.addAction("删除", this, &MainWindow::onDeleteTask);
    menu.addAction("标记完成", this, &MainWindow::onMarkComplete);
    menu.exec(m_tableView->viewport()->mapToGlobal(pos));
}

void MainWindow::onSyncToCloud() {
    // 演示：在后台线程执行网络同步
    statusBar()->showMessage("正在同步到云端...");

    QNetworkAccessManager *manager = new QNetworkAccessManager(this);
    QJsonArray tasksArray;
    for (int i = 0; i < m_model->rowCount(); ++i) {
        Task t = m_model->getTask(i);
        QJsonObject obj;
        obj["title"] = t.title;
        obj["status"] = t.status;
        tasksArray.append(obj);
    }

    QNetworkRequest request(QUrl("https://httpbin.org/post"));
    request.setHeader(QNetworkRequest::ContentTypeHeader, "application/json");

    QNetworkReply *reply = manager->post(request, QJsonDocument(tasksArray).toJson());
    connect(reply, &QNetworkReply::finished, this, [this, reply]() {
        if (reply->error() == QNetworkReply::NoError) {
            statusBar()->showMessage("同步完成", 3000);
            QMessageBox::information(this, "成功", "任务已同步到云端");
        } else {
            statusBar()->showMessage("同步失败", 3000);
            QMessageBox::warning(this, "失败", "同步失败: " + reply->errorString());
        }
        reply->deleteLater();
    });
}

void MainWindow::closeEvent(QCloseEvent *event) {
    // 保存窗口状态
    QSettings settings("MyCompany", "TaskManager");
    settings.setValue("geometry", saveGeometry());
    settings.setValue("windowState", saveState());
    QMainWindow::closeEvent(event);
}
```

**CMakeLists.txt**：
```cmake
cmake_minimum_required(VERSION 3.16)
project(TaskManager)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

find_package(Qt6 COMPONENTS Widgets Network Sql REQUIRED)

qt_add_executable(TaskManager
    main.cpp
    mainwindow.cpp mainwindow.h
    taskmodel.cpp taskmodel.h
    taskdatabase.cpp taskdatabase.h
    task.h
)

target_link_libraries(TaskManager PRIVATE
    Qt6::Widgets
    Qt6::Network
    Qt6::Sql
)
```

#### 总结

- 完整 Qt 应用采用分层架构：数据层（数据库）、模型层（自定义 Model）、视图层（界面）、控制层（业务逻辑）
- QSqlDatabase + QSqlQuery 实现数据持久化，QSqlTableModel 可直接绑定视图
- 自定义 QAbstractTableModel 提供灵活的数据展示，支持着色、对齐等自定义
- 主窗口用 QMainWindow，菜单/工具栏复用 QAction，右键菜单用 customContextMenu
- 网络同步用 QNetworkAccessManager 异步请求，不阻塞界面
- 窗口状态用 QSettings 持久化，下次启动恢复
- 常见问题：数据库操作失败要检查 `lastError()`；模型数据变化必须调用 begin/endInsertRows 等函数

---

## 课程总结

恭喜您完成了 Qt6 系统教程的全部 30 讲！让我们回顾学习路径：

### 学习路径回顾

1. **第1-4讲 入门基础**：环境搭建、第一个程序、CMake 构建、核心模块概览
2. **第5-8讲 核心机制**：QObject 对象模型、信号槽、父子内存管理、元对象系统
3. **第9-12讲 窗口控件**：QWidget、QMainWindow、常用控件、布局管理
4. **第13-15讲 事件系统**：事件循环、鼠标键盘事件、事件过滤器、定时器
5. **第16-18讲 模型视图**：Model/View 架构、自定义模型、代理 Delegate
6. **第19-21讲 图形绘图**：QPainter、QGraphicsView 框架、自定义控件
7. **第22-24讲 多线程**：QThread、QtConcurrent、线程同步
8. **第25-27讲 网络数据**：TCP/HTTP 编程、文件 I/O、JSON/XML/SQLite
9. **第28-30讲 现代实战**：QML、Qt6 新特性、完整项目实战

### 核心知识点

- **对象模型**：QObject 是 Qt 的根基，信号槽和父子对象树是核心机制
- **事件驱动**：事件循环 + 信号槽是 Qt 异步编程的基础
- **数据分离**：Model/View 架构实现数据与展示解耦
- **跨平台**：一套代码运行于 Windows、macOS、Linux、移动端
- **现代 C++**：Qt6 全面拥抱 C++17，代码更简洁高效

### 进阶学习方向

1. **Qt Quick 3D**：3D 图形与 QML 集成
2. **Qt for WebAssembly**：将 Qt 应用编译为 Web 应用
3. **Qt for Python (PySide6)**：用 Python 开发 Qt 应用
4. **Qt 设计工具**：Qt Designer、Qt Creator 高级功能
5. **性能优化**：QML 性能分析、内存优化、启动速度优化

希望本教程能帮助您掌握 Qt6 开发，构建出优秀的跨平台应用！
