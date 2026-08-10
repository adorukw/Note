# C++ 并发编程系统教程

> 本教程以教科书形式，从基础概念到高级实战，系统讲解 C++ 并发编程。每讲包含「概念 → 原理 → 例子 → 总结」四个部分，共 8 章 36 讲。

---

## 目录

- [第一章 并发基础](#第一章-并发基础)
  - [第1讲 并发编程概述](#第1讲-并发编程概述)
  - [第2讲 并发与并行：概念与硬件基础](#第2讲-并发与并行概念与硬件基础)
  - [第3讲 C++ 并发演进史](#第3讲-c-并发演进史)
  - [第4讲 第一个并发程序](#第4讲-第一个并发程序)
- [第二章 线程管理](#第二章-线程管理)
  - [第5讲 std::thread 基础](#第5讲-stdthread-基础)
  - [第6讲 线程参数传递](#第6讲-线程参数传递)
  - [第7讲 线程所有权转移](#第7讲-线程所有权转移)
  - [第8讲 线程标识与硬件并发](#第8讲-线程标识与硬件并发)
  - [第9讲 线程异常安全](#第9讲-线程异常安全)
- [第三章 互斥与共享数据](#第三章-互斥与共享数据)
  - [第10讲 共享数据问题](#第10讲-共享数据问题)
  - [第11讲 std::mutex 与 RAII 锁](#第11讲-stdmutex-与-raii-锁)
  - [第12讲 死锁与避免策略](#第12讲-死锁与避免策略)
  - [第13讲 不同类型的 mutex](#第13讲-不同类型的-mutex)
  - [第14讲 保护共享数据的初始化](#第14讲-保护共享数据的初始化)
- [第四章 条件变量与等待](#第四章-条件变量与等待)
  - [第15讲 条件变量基础](#第15讲-条件变量基础)
  - [第16讲 生产者-消费者模型](#第16讲-生产者-消费者模型)
  - [第17讲 线程安全队列实现](#第17讲-线程安全队列实现)
  - [第18讲 等待一次性事件](#第18讲-等待一次性事件)
- [第五章 Future 与异步](#第五章-future-与异步)
  - [第19讲 std::async 异步任务](#第19讲-stdasync-异步任务)
  - [第20讲 future 与 shared_future](#第20讲-future-与-shared_future)
  - [第21讲 promise 与异步通信](#第21讲-promise-与异步通信)
  - [第22讲 packaged_task](#第22讲-packaged_task)
  - [第23讲 异常处理与超时](#第23讲-异常处理与超时)
- [第六章 原子操作与内存模型](#第六章-原子操作与内存模型)
  - [第24讲 C++ 内存模型基础](#第24讲-c-内存模型基础)
  - [第25讲 std::atomic 原子类型](#第25讲-stdatomic-原子类型)
  - [第26讲 内存序详解](#第26讲-内存序详解)
  - [第27讲 原子指针与原子引用](#第27讲-原子指针与原子引用)
  - [第28讲 无锁编程实践](#第28讲-无锁编程实践)
- [第七章 C++20 并发新特性](#第七章-c20-并发新特性)
  - [第29讲 协程基础](#第29讲-协程基础)
  - [第30讲 jthread 与协作式取消](#第30讲-jthread-与协作式取消)
  - [第31讲 信号量、latch 与 barrier](#第31讲-信号量latch-与-barrier)
  - [第32讲 atomic_ref 与同步流](#第32讲-atomic_ref-与同步流)
- [第八章 并发设计与实战](#第八章-并发设计与实战)
  - [第33讲 线程池设计](#第33讲-线程池设计)
  - [第34讲 并发数据结构](#第34讲-并发数据结构)
  - [第35讲 并行算法](#第35讲-并行算法)
  - [第36讲 调试与最佳实践](#第36讲-调试与最佳实践)

---

## 第一章 并发基础

### 第1讲 并发编程概述

#### 概念

**并发编程（Concurrent Programming）** 是指在一个程序中同时处理多个任务的能力。这里的"同时"并非一定指物理上的同一时刻执行，而是指多个任务在逻辑上处于"进行中"的状态。并发是软件设计层面的概念，它关注如何将一个复杂问题分解为多个可独立执行的子任务。

在 C++ 中，并发编程主要通过**多线程（multithreading）** 来实现。一个进程可以包含多个线程，每个线程拥有独立的执行栈和程序计数器，但共享进程的地址空间、堆内存和文件描述符等资源。这种共享使得线程间通信比进程间通信（IPC）更高效，但也带来了数据竞争和同步等挑战。

#### 原理

并发编程的根本动机来自三个方面：

1. **性能提升**：现代 CPU 普遍采用多核架构，单线程程序无法利用额外的核心。通过将工作分配到多个线程，可以真正实现并行计算，将执行时间缩短为原来的几分之一。
2. **响应性**：在 GUI 应用或网络服务器中，主线程需要保持对用户输入或网络请求的响应。将耗时操作（如文件 I/O、复杂计算）放到后台线程，可以避免界面卡顿或请求超时。
3. **资源利用率**：当某个线程因 I/O 阻塞时，CPU 可以调度其他线程执行，从而提高整体吞吐量。

并发编程的核心挑战在于**正确性**和**性能**的平衡。多个线程访问共享数据时，如果不加保护，就会产生**数据竞争（data race）**，导致未定义行为。同时，过度的同步（如过多使用锁）会降低并发度，甚至引入死锁等更严重的问题。

C++11 引入了标准化的线程库（`<thread>`、`<mutex>`、`<atomic>` 等），使并发编程不再依赖平台特定的 API（如 POSIX 的 pthreads 或 Windows 的 CreateThread）。这极大提升了代码的可移植性和可维护性。

#### 例子

下面是一个最简单的并发示例，展示两个线程同时输出消息：

```cpp
#include <iostream>
#include <thread>
#include <chrono>

void print_message(const std::string& msg, int count) {
    for (int i = 0; i < count; ++i) {
        std::cout << msg << " #" << i << std::endl;
        std::this_thread::sleep_for(std::chrono::milliseconds(100));
    }
}

int main() {
    // 创建两个线程，分别执行不同的任务
    std::thread t1(print_message, "Thread A", 5);
    std::thread t2(print_message, "Thread B", 5);

    // 等待两个线程完成
    t1.join();
    t2.join();

    std::cout << "All threads finished." << std::endl;
    return 0;
}
```

编译运行（需要链接 pthread）：

```bash
g++ -std=c++17 -pthread main.cpp -o main && ./main
```

可能的输出（每次运行顺序可能不同）：

```
Thread A #0
Thread B #0
Thread A #1
Thread B #1
...
All threads finished.
```

注意输出交错的现象——这正是并发的本质特征：线程调度顺序由操作系统决定，程序员不应假设特定顺序。

#### 总结

- 并发是软件设计概念，关注多任务逻辑上的"同时进行"；并行是硬件执行概念，关注物理上的同时执行。
- C++11 起提供了标准化的并发支持，跨平台可移植。
- 并发的三大动机：性能、响应性、资源利用率。
- 并发的核心挑战是数据竞争和同步开销，需要在正确性与性能之间权衡。
- 编译多线程程序时，在 Linux 下通常需要加 `-pthread` 选项。

---

### 第2讲 并发与并行：概念与硬件基础

#### 概念

**并发（Concurrency）** 和**并行（Parallelism）** 是两个常被混淆但本质不同的概念：

- **并发**：指系统具有处理多个任务的能力，任务在逻辑上同时存在。在单核 CPU 上，操作系统通过时间片轮转实现"伪同时"，这叫并发。
- **并行**：指系统在物理上同时执行多个任务。这需要多核 CPU 或多处理器硬件支持。

一个经典的比喻：并发是一个咖啡师同时处理多个订单（轮流做），并行是多个咖啡师同时各做一个订单。

#### 原理

理解并发编程需要了解底层硬件基础：

**1. CPU 架构演进**

早期 CPU 是单核的，通过**时间片轮转（time slicing）** 实现并发：操作系统给每个线程分配几毫秒的执行时间，然后切换到下一个线程。由于切换速度极快，人类感觉所有任务在"同时"运行。这种方式称为**抢占式多任务（preemptive multitasking）**。

2005 年前后，由于功耗和散热限制，CPU 主频提升遇到瓶颈，厂商转向多核架构。如今主流 CPU 都有 4-16 个核心，服务器可达 64 核以上。这使得真正的并行计算成为可能。

**2. 上下文切换（Context Switch）**

当操作系统切换线程时，需要保存当前线程的寄存器状态、栈指针、程序计数器等，并加载下一个线程的状态。这个过程称为上下文切换，开销通常在 1-10 微秒级别。频繁的上下文切换会显著降低性能，因此线程数量并非越多越好。

**3. 缓存与缓存一致性**

现代 CPU 有多级缓存（L1、L2、L3），每个核心有自己的 L1/L2 缓存。当多个核心上的线程修改同一缓存行中的数据时，硬件通过**缓存一致性协议（如 MESI）** 保证数据一致性。这会带来性能开销，称为**伪共享（false sharing）**——即使两个线程访问不同变量，只要它们在同一缓存行（通常 64 字节），也会互相影响。

**4. 内存层次与延迟**

| 层次 | 延迟（约） |
|------|-----------|
| 寄存器 | <1 ns |
| L1 缓存 | ~1 ns |
| L2 缓存 | ~4 ns |
| L3 缓存 | ~12 ns |
| 主内存 | ~100 ns |

理解这个延迟层次对优化并发程序至关重要：让线程尽量访问自己缓存中的数据，避免频繁跨核同步。

#### 例子

下面用代码演示并发与并行的区别，并测量多核加速效果：

```cpp
#include <iostream>
#include <thread>
#include <vector>
#include <chrono>
#include <numeric>

// 一个计算密集型任务：累加一段数字
long long sum_range(long long start, long long end) {
    long long sum = 0;
    for (long long i = start; i < end; ++i) {
        sum += i;
    }
    return sum;
}

int main() {
    const long long N = 100'000'000LL;
    const int num_threads = std::thread::hardware_concurrency();
    std::cout << "Hardware concurrency: " << num_threads << " threads\n";

    // 单线程版本
    auto t0 = std::chrono::high_resolution_clock::now();
    long long result1 = sum_range(1, N);
    auto t1 = std::chrono::high_resolution_clock::now();
    auto single_ms = std::chrono::duration_cast<std::chrono::milliseconds>(t1 - t0).count();

    // 多线程版本
    std::vector<std::thread> threads;
    std::vector<long long> partial(num_threads);
    long long chunk = N / num_threads;

    t0 = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < num_threads; ++i) {
        long long start = 1 + i * chunk;
        long long end = (i == num_threads - 1) ? N : start + chunk;
        threads.emplace_back([i, start, end, &partial]() {
            partial[i] = sum_range(start, end);
        });
    }
    for (auto& t : threads) t.join();
    long long result2 = std::accumulate(partial.begin(), partial.end(), 0LL);
    t1 = std::chrono::high_resolution_clock::now();
    auto multi_ms = std::chrono::duration_cast<std::chrono::milliseconds>(t1 - t0).count();

    std::cout << "Single-thread: " << single_ms << " ms, sum=" << result1 << "\n";
    std::cout << "Multi-thread:  " << multi_ms << " ms, sum=" << result2 << "\n";
    std::cout << "Speedup: " << (double)single_ms / multi_ms << "x\n";
    return 0;
}
```

在 8 核机器上运行，可能得到约 4-6 倍的加速（不会达到 8 倍，因为有线程创建和合并的开销）。

#### 总结

- 并发是逻辑上的多任务处理，并行是物理上的同时执行；并发不一定并行，但并行一定并发。
- 单核通过时间片轮转实现并发，多核实现真正并行。
- 上下文切换有开销，线程数应与硬件能力匹配，通常不超过 `hardware_concurrency()` 的 2 倍。
- 缓存一致性协议保证多核数据一致，但伪共享会降低性能。
- 内存访问延迟差异巨大，优化时应让数据尽量留在缓存中。

---

### 第3讲 C++ 并发演进史

#### 概念

C++ 并发编程能力并非一蹴而就，而是经历了长期演进。理解这一历史有助于我们在不同标准的代码库中游刃有余，并选择合适的工具。

#### 原理

**1. C++98/C++03 时代：无标准并发**

在 2011 年之前，C++ 标准没有线程概念。开发者依赖平台 API：
- POSIX Threads（pthread）：Linux/Unix 系统
- Windows Threads（CreateThread）：Windows 系统
- Boost.Thread：跨平台封装库

这导致代码不可移植，且容易出错。Boost.Thread 后来成为 C++11 标准线程库的基础。

**2. C++11：并发元年（2011）**

C++11 引入了完整的并发支持，这是 C++ 历史上最重要的更新之一：
- `std::thread`：线程管理
- `std::mutex`、`std::lock_guard`：互斥与锁
- `std::condition_variable`：条件变量
- `std::atomic`：原子操作
- `std::future`、`std::promise`、`std::async`：异步编程
- **内存模型**：定义了多线程下内存访问的语义，这是 C++ 并发的理论基石

**3. C++14：小幅增强（2014）**

- `std::shared_timed_mutex`：读写锁
- `std::shared_lock`：配合 shared_mutex 使用

**4. C++17：并行算法（2017）**

- `std::scoped_lock`：一次性锁住多个 mutex，避免死锁
- 并行 STL 算法：`std::execution::par` 等执行策略，让 `for_each`、`transform` 等算法可并行执行

**5. C++20：协程与新同步原语（2020）**

- **协程（Coroutines）**：`co_await`、`co_yield`、`co_return`
- `std::jthread`：自动 join 的线程，支持取消
- `std::stop_token`：协作式取消机制
- `std::counting_semaphore`、`std::binary_semaphore`：信号量
- `std::latch`、`std::barrier`：线程协调原语
- `std::atomic_ref`：对已有变量的原子引用
- `std::jthread` 配合 `std::stop_callback`

**6. C++23：进一步完善（2023）**

- `std::mdspan`：多维视图（利于并行数据处理）
- `std::flat_map`、`std::flat_set`：缓存友好的容器
- 协程库扩展

#### 例子

下面展示不同标准下的并发代码风格演变：

```cpp
#include <iostream>
#include <thread>
#include <mutex>
#include <shared_mutex>
#include <barrier>
#include <latch>
#include <vector>

std::mutex mtx;
int counter = 0;

// C++11 风格：lock_guard
void increment_cpp11() {
    for (int i = 0; i < 1000; ++i) {
        std::lock_guard<std::mutex> lock(mtx);
        ++counter;
    }
}

// C++17 风格：scoped_lock（即使只锁一个也推荐）
void increment_cpp17() {
    for (int i = 0; i < 1000; ++i) {
        std::scoped_lock lock(mtx);
        ++counter;
    }
}

// C++20 风格：barrier 协调多线程
void barrier_example() {
    const int n = 4;
    std::vector<std::jthread> threads;
    std::barrier sync_point(n, []() noexcept {
        std::cout << "--- All threads reached the barrier ---\n";
    });

    for (int i = 0; i < n; ++i) {
        threads.emplace_back([i, &sync_point]() {
            std::cout << "Thread " << i << " phase 1\n";
            sync_point.arrive_and_wait();  // 等待所有线程完成阶段1
            std::cout << "Thread " << i << " phase 2\n";
            sync_point.arrive_and_wait();  // 等待所有线程完成阶段2
        });
    }
}

int main() {
    std::vector<std::thread> ts;
    for (int i = 0; i < 10; ++i) ts.emplace_back(increment_cpp17);
    for (auto& t : ts) t.join();
    std::cout << "Counter: " << counter << "\n";

    barrier_example();
    return 0;
}
```

#### 总结

- C++11 是并发编程的里程碑，引入了线程、互斥、原子操作和内存模型。
- C++14 增加了读写锁，C++17 引入 scoped_lock 和并行算法。
- C++20 是第二大飞跃：协程、jthread、信号量、latch/barrier 等。
- 编写新代码时应优先使用最新标准的设施（如 `jthread` 替代 `thread`，`scoped_lock` 替代 `lock_guard`）。
- 编译时用 `-std=c++20` 或更高版本以启用新特性。

---

### 第4讲 第一个并发程序

#### 概念

本讲通过一个完整的并发程序，介绍 C++ 并发编程的基本骨架：包含头文件、创建线程、传递参数、等待线程结束。这是后续所有内容的基础。

#### 原理

C++ 多线程程序的基本结构包含以下要素：

1. **头文件**：`<thread>` 提供线程类，`<mutex>` 提供互斥锁，`<atomic>` 提供原子操作，`<chrono>` 提供时间工具。
2. **线程函数**：任何可调用对象（函数、lambda、函数对象、成员函数）都可作为线程入口。
3. **线程创建**：`std::thread t(func, args...)` 在构造时立即启动新线程。
4. **线程等待**：`t.join()` 阻塞当前线程直到 t 完成；`t.detach()` 让线程在后台独立运行。
5. **生命周期管理**：`std::thread` 析构前必须 `join()` 或 `detach()`，否则程序终止（调用 `std::terminate`）。

线程对象是**资源句柄**，类似 `std::unique_ptr`。它不可拷贝，但可移动。这意味着线程所有权可以在变量间转移，但不能共享。

#### 例子

下面是一个稍微完整的"Hello Concurrent World"程序，演示多种线程创建方式：

```cpp
#include <iostream>
#include <thread>
#include <chrono>
#include <functional>

// 1. 普通函数作为线程入口
void task_function(int id, int duration_ms) {
    std::this_thread::sleep_for(std::chrono::milliseconds(duration_ms));
    std::cout << "Function task " << id << " done after " << duration_ms << "ms\n";
}

// 2. 函数对象（functor）
struct TaskFunctor {
    int id;
    void operator()() const {
        std::cout << "Functor task " << id << " running\n";
    }
};

int main() {
    std::cout << "Main thread id: " << std::this_thread::get_id() << "\n";

    // 方式1：函数 + 参数
    std::thread t1(task_function, 1, 100);

    // 方式2：lambda 表达式
    std::thread t2([]() {
        std::cout << "Lambda task running on thread " << std::this_thread::get_id() << "\n";
    });

    // 方式3：函数对象
    std::thread t3(TaskFunctor{42});

    // 方式4：成员函数（需要对象实例）
    struct Worker {
        void work() { std::cout << "Member function task\n"; }
    };
    Worker w;
    std::thread t4(&Worker::work, &w);

    // 等待所有线程完成
    t1.join();
    t2.join();
    t3.join();
    t4.join();

    std::cout << "All tasks completed.\n";

    // 演示 detach：后台线程
    std::thread t5([]() {
        std::this_thread::sleep_for(std::chrono::milliseconds(200));
        std::cout << "Detached task finished (may or may not print)\n";
    });
    t5.detach();  // 分离，主线程不等待

    std::cout << "Main exiting.\n";
    return 0;
}
```

**注意事项**：

1. **C++ 最令人头疼的解析（Most Vexing Parse）**：下面这行会被解析为函数声明而非线程对象：
   ```cpp
   std::thread t(TaskFunctor());  // 错误！被解析为函数声明
   ```
   解决方法：用括号初始化 `std::thread t{TaskFunctor()};` 或 `std::thread t((TaskFunctor()));`。

2. **编译命令**：
   ```bash
   g++ -std=c++17 -pthread -O2 main.cpp -o main
   ```
   `-pthread` 在 Linux 下必须加，否则链接错误。

3. **输出交错**：多个线程同时写 `std::cout` 可能导致输出交错。后续章节会讲解如何用互斥锁保护。

#### 总结

- 线程入口可以是任意可调用对象：函数、lambda、函数对象、成员函数。
- `std::thread` 构造时立即启动线程，析构前必须 `join()` 或 `detach()`。
- 参数按值传递给线程函数；若需引用，使用 `std::ref()`。
- 警惕"最令人头疼的解析"，优先使用花括号初始化。
- Linux 下编译必须加 `-pthread` 选项。
- 多线程共享 `std::cout` 会导致输出交错，需要同步机制（后续讲解）。

---

## 第二章 线程管理

### 第5讲 std::thread 基础

#### 概念

`std::thread` 是 C++11 引入的线程管理类，定义在 `<thread>` 头文件中。它是对操作系统原生线程的封装，提供统一的跨平台接口。每个 `std::thread` 对象代表一个执行线程，可以是可结合的（joinable）或不可结合的（non-joinable）。

#### 原理

`std::thread` 的核心状态机如下：

```
                    构造（启动线程）
   [默认构造/移动后空] ──────────────> [可结合 joinable]
                                          │
                              ┌───────────┴───────────┐
                              │                       │
                          join()                   detach()
                              │                       │
                              v                       v
                       [不可结合]              [不可结合]
                       （线程已结束）         （线程在后台运行）
```

**关键方法**：

| 方法 | 说明 |
|------|------|
| `join()` | 阻塞当前线程，等待目标线程结束 |
| `detach()` | 分离线程，让其在后台独立运行 |
| `joinable()` | 返回线程是否可结合（即是否代表一个活动线程） |
| `get_id()` | 返回线程 ID |
| `hardware_concurrency()` | 静态方法，返回硬件支持的并发线程数 |

**RAII 与线程安全**：`std::thread` 析构时如果仍 `joinable()`，会调用 `std::terminate()` 终止程序。这是为了防止程序员忘记等待线程导致资源泄漏或悬挂引用。因此，必须确保在析构前调用 `join()` 或 `detach()`。

**join 与 detach 的选择**：
- **join**：需要等待线程结果，或确保线程在某个时间点前完成。最常用。
- **detach**：线程是"fire and forget"任务，如后台日志、监控。慎用，因为分离后无法控制线程生命周期，主线程结束时分离的线程可能被强制终止。

#### 例子

```cpp
#include <iostream>
#include <thread>
#include <chrono>
#include <stdexcept>

void long_task(int seconds) {
    std::cout << "Task started, will run for " << seconds << "s\n";
    std::this_thread::sleep_for(std::chrono::seconds(seconds));
    std::cout << "Task finished\n";
}

int main() {
    std::cout << "hardware_concurrency: " 
              << std::thread::hardware_concurrency() << "\n";

    std::thread t(long_task, 2);
    std::cout << "Thread id: " << t.get_id() << "\n";
    std::cout << "joinable before join: " << t.joinable() << "\n";

    // 错误示范：忘记 join/detach 会导致 terminate
    // 如果这里直接 return，t 析构时程序崩溃

    t.join();
    std::cout << "joinable after join: " << t.joinable() << "\n";

    // detach 示例
    std::thread t2(long_task, 1);
    t2.detach();
    std::cout << "t2 detached, running in background\n";

    // 主线程稍等一下，让 detach 的线程有机会输出
    std::this_thread::sleep_for(std::chrono::seconds(2));
    return 0;
}
```

**RAII 包装器**：为了避免忘记 join，可以写一个自动 join 的包装类（C++20 的 `std::jthread` 已内置此功能）：

```cpp
class joining_thread {
    std::thread t;
public:
    joining_thread() = default;
    template<typename Callable, typename... Args>
    explicit joining_thread(Callable&& func, Args&&... args)
        : t(std::forward<Callable>(func), std::forward<Args>(args)...) {}
    joining_thread(joining_thread&& other) noexcept : t(std::move(other.t)) {}
    joining_thread& operator=(joining_thread&& other) noexcept {
        if (joinable()) join();
        t = std::move(other.t);
        return *this;
    }
    ~joining_thread() { if (joinable()) join(); }
    bool joinable() const noexcept { return t.joinable(); }
    void join() { t.join(); }
    std::thread::id get_id() const noexcept { return t.get_id(); }
};
```

#### 总结

- `std::thread` 是可移动但不可拷贝的资源句柄。
- 析构前必须 `join()` 或 `detach()`，否则程序终止。
- `join()` 等待线程结束，`detach()` 分离到后台。
- `hardware_concurrency()` 返回硬件并发数，用于决定线程数量。
- 推荐用 RAII 包装（或 C++20 的 `jthread`）避免忘记 join。
- `detach()` 慎用：分离后无法控制线程，主线程退出时分离线程可能被杀。

---

### 第6讲 线程参数传递

#### 概念

向线程函数传递参数是并发编程的常见需求。`std::thread` 的构造函数接受可变参数模板，可以传递任意数量的参数。但参数传递的语义与普通函数调用有重要区别，理解这些区别是避免悬挂引用和未定义行为的关键。

#### 原理

`std::thread` 构造函数的参数传递机制：

1. **默认按值传递**：所有参数会被**复制**到线程的内部存储中，然后在线程启动时以右值形式传给线程函数。这意味着即使线程函数声明为 `void f(int&)`，直接传变量也会因为临时值无法绑定到非 const 左值引用而编译失败。

2. **传递引用**：必须使用 `std::ref()` 或 `std::cref()` 包装，才能将参数以引用形式传递。`std::ref` 返回一个 `std::reference_wrapper`，它可被拷贝，但内部保存指针指向原对象。

3. **传递指针**：指针本身按值复制，但指向的对象是共享的。需确保指针指向的对象在线程访问期间有效。

4. **传递所有权**：使用 `std::move()` 可将 `std::unique_ptr`、`std::thread` 等移动语义对象的所有权转移到新线程。

5. **成员函数作为入口**：第一个参数是成员函数指针，第二个参数是对象指针或引用。

**常见陷阱**：

- **悬挂引用**：传递局部变量的引用，但局部变量在主线程中已销毁。
- **字符串字面量退化**：传递字符串字面量时，参数类型可能是 `const char*` 而非 `std::string`，导致线程函数期望 `std::string` 时出现意外。

#### 例子

```cpp
#include <iostream>
#include <thread>
#include <string>
#include <memory>

void update_value(int& x) { x += 100; }
void print_string(const std::string& s) { std::cout << "String: " << s << "\n"; }
void take_ownership(std::unique_ptr<int> p) { 
    std::cout << "Owned value: " << *p << "\n"; 
}

class Widget {
public:
    void work(int n) { std::cout << "Widget::work(" << n << ")\n"; }
};

int main() {
    // 1. 按值传递（默认）
    int a = 10;
    std::thread t1([](int x) { std::cout << "Value: " << x << "\n"; }, a);
    t1.join();

    // 2. 传递引用：必须用 std::ref
    int b = 50;
    std::thread t2(update_value, std::ref(b));
    t2.join();
    std::cout << "b after update: " << b << "\n";  // 150

    // 3. 传递 const 引用：用 std::cref
    std::string msg = "Hello";
    std::thread t3(print_string, std::cref(msg));
    t3.join();

    // 4. 传递字符串字面量：注意潜在陷阱
    // 下面这行可能有问题：字面量在 thread 构造时可能以 const char* 传递，
    // 然后在线程函数中隐式构造 std::string，但构造时机不确定
    // 安全做法：显式构造 std::string
    std::thread t4(print_string, std::string("World"));
    t4.join();

    // 5. 转移所有权
    auto ptr = std::make_unique<int>(42);
    std::thread t5(take_ownership, std::move(ptr));
    t5.join();
    // 此时 ptr 为 nullptr

    // 6. 成员函数作为线程入口
    Widget w;
    std::thread t6(&Widget::work, &w, 99);
    t6.join();

    return 0;
}
```

**悬挂引用的危险示例**：

```cpp
// 错误示范：返回后局部变量销毁，线程访问悬挂引用
std::thread create_bad_thread() {
    int local = 42;
    // 危险！local 在函数返回后销毁，但线程可能还在使用它
    return std::thread([](int& x) { 
        std::this_thread::sleep_for(std::chrono::seconds(1));
        std::cout << x << "\n";  // 未定义行为！
    }, std::ref(local));
}
```

#### 总结

- `std::thread` 默认按值复制参数，再以右值传给线程函数。
- 传递引用必须用 `std::ref()` / `std::cref()`。
- 传递 `unique_ptr` 等移动语义对象用 `std::move()`。
- 成员函数作为入口：`std::thread(&Class::method, &obj, args...)`。
- 警惕悬挂引用：不要传递局部变量的引用给可能比局部变量生命周期长的线程。
- 字符串字面量建议显式构造为 `std::string` 再传递，避免隐式转换时机问题。

---

### 第7讲 线程所有权转移

#### 概念

`std::thread` 是**移动语义**类型——它可以移动但不能拷贝。这意味着线程的所有权可以在不同的 `std::thread` 对象之间转移，但同一时刻只有一个对象管理该线程。这一特性使得线程可以被存储在容器中、从函数返回、在模块间传递。

#### 原理

`std::thread` 的移动语义规则：

1. **移动构造**：`std::thread t2(std::move(t1));` 后，`t1` 变为不可结合（empty），`t2` 接管线程。
2. **移动赋值**：`t2 = std::move(t1);` 类似，但若 `t2` 当前可结合，会先调用 `std::terminate()`！所以移动赋值前必须确保目标已 join 或 detach。
3. **从函数返回**：编译器会自动进行返回值优化（RVO）或移动，所以 `return std::thread(...);` 是安全的。
4. **存入容器**：`std::vector<std::thread>` 需要 `emplace_back` 或 `push_back(std::move(t))`。

为什么这样设计？因为操作系统线程是单一资源句柄，不能被两个对象同时持有。移动语义清晰地表达了"所有权转移"的意图，避免了共享线程带来的同步问题。

#### 例子

```cpp
#include <iostream>
#include <thread>
#include <vector>
#include <chrono>

void worker(int id) {
    std::cout << "Worker " << id << " running on thread " 
              << std::this_thread::get_id() << "\n";
    std::this_thread::sleep_for(std::chrono::milliseconds(100));
}

// 从函数返回线程（隐式移动）
std::thread create_thread(int id) {
    return std::thread(worker, id);
}

// 接收线程所有权
void take_ownership(std::thread t) {
    std::cout << "Received thread " << t.get_id() << "\n";
    t.join();  // 接收者负责 join
}

int main() {
    // 1. 移动构造
    std::thread t1(worker, 1);
    std::thread t2 = std::move(t1);
    std::cout << "t1 joinable after move: " << t1.joinable() << "\n";  // 0
    t2.join();

    // 2. 从函数返回
    std::thread t3 = create_thread(2);
    t3.join();

    // 3. 传递给函数
    take_ownership(std::thread(worker, 3));

    // 4. 存入容器
    std::vector<std::thread> threads;
    for (int i = 0; i < 5; ++i) {
        threads.emplace_back(worker, 10 + i);
    }
    for (auto& t : threads) {
        t.join();
    }

    // 5. 移动赋值的陷阱
    std::thread tA(worker, 100);
    std::thread tB(worker, 200);
    // tA = std::move(tB);  // 危险！tA 当前 joinable，会 terminate
    tA.join();
    tA = std::move(tB);  // 现在 tA 不可结合，安全
    tA.join();

    return 0;
}
```

**scoped_thread 模式**：将线程所有权封装，确保作用域结束时自动 join：

```cpp
class scoped_thread {
    std::thread t;
public:
    explicit scoped_thread(std::thread t_) : t(std::move(t_)) {
        if (!t.joinable()) throw std::logic_error("No thread");
    }
    ~scoped_thread() { 
        if (t.joinable()) t.join(); 
    }
    scoped_thread(const scoped_thread&) = delete;
    scoped_thread& operator=(const scoped_thread&) = delete;
};

// 使用：
void example() {
    int local = 42;
    scoped_thread st(std::thread([&local]() {
        std::cout << "Using local: " << local << "\n";
    }));
    // 作用域结束时自动 join，即使发生异常也安全
}
```

#### 总结

- `std::thread` 可移动不可拷贝，体现单一所有权。
- 移动后源对象变为不可结合（empty）。
- 移动赋值前必须确保目标已 join/detach，否则 `std::terminate`。
- 从函数返回 `std::thread` 是安全的（RVO 或隐式移动）。
- 可用 `std::vector<std::thread>` 管理多个线程。
- `scoped_thread` 模式提供 RAII 式的自动 join，是异常安全的好实践。

---

### 第8讲 线程标识与硬件并发

#### 概念

`std::thread::id` 是线程标识符类型，用于唯一标识一个线程。`std::thread::hardware_concurrency()` 是静态方法，返回硬件支持的并发线程数。这两个工具在调试、日志、线程池设计中非常有用。

#### 原理

**线程 ID**：
- `std::this_thread::get_id()` 返回当前线程的 ID。
- `std::thread::get_id()` 返回 thread 对象管理的线程 ID；若线程不可结合，返回默认构造的 id。
- `thread::id` 可比较（`==`、`!=`、`<`）和输出到流（`operator<<`），但具体值由实现决定，通常不是整数。
- 默认构造的 `thread::id{}` 表示"无线程"，所有默认构造的 id 都相等。

**hardware_concurrency()**：
- 返回一个提示值，表示硬件能真正并行执行的线程数。
- 通常是 CPU 物理核心数 × 每核线程数（如 8 核 16 线程 CPU 返回 16）。
- 返回 0 表示无法检测，此时应使用默认值（如 4）。
- 该值是**提示**而非保证，实际性能还受其他因素影响。

**线程 ID 的用途**：
1. **调试日志**：记录哪个线程执行了什么操作。
2. **主线程判断**：`if (std::this_thread::get_id() != main_thread_id)` 判断是否在主线程。
3. **线程局部存储**：根据 ID 索引每线程数据。
4. **避免自死锁**：mutex 可记录持有者 ID，检测重复加锁。

#### 例子

```cpp
#include <iostream>
#include <thread>
#include <vector>
#include <map>
#include <mutex>
#include <chrono>

std::mutex log_mtx;
void log(const std::string& msg) {
    std::lock_guard<std::mutex> lock(log_mtx);
    std::cout << "[" << std::this_thread::get_id() << "] " << msg << "\n";
}

int main() {
    // 1. 获取硬件并发数
    unsigned int n = std::thread::hardware_concurrency();
    std::cout << "hardware_concurrency: " << n << "\n";
    if (n == 0) n = 4;  // 检测失败时的回退

    // 2. 主线程 ID
    std::thread::id main_id = std::this_thread::get_id();
    std::cout << "Main thread id: " << main_id << "\n";

    // 3. 工作线程记录自己的 ID
    std::vector<std::thread> threads;
    std::map<std::thread::id, int> id_map;
    std::mutex map_mtx;

    for (int i = 0; i < 5; ++i) {
        threads.emplace_back([&]() {
            auto tid = std::this_thread::get_id();
            log("Thread started");
            {
                std::lock_guard<std::mutex> lock(map_mtx);
                id_map[tid] = i;
            }
            std::this_thread::sleep_for(std::chrono::milliseconds(100));
        });
    }

    for (auto& t : threads) t.join();

    // 4. 输出 ID 映射
    std::cout << "\nThread ID mapping:\n";
    for (const auto& [id, idx] : id_map) {
        std::cout << "  " << id << " -> index " << idx << "\n";
    }

    // 5. 判断是否在主线程
    auto check_main = [&]() {
        if (std::this_thread::get_id() == main_id) {
            std::cout << "Running in main thread\n";
        } else {
            std::cout << "Running in worker thread\n";
        }
    };
    check_main();
    std::thread t(check_main);
    t.join();

    // 6. 默认构造的 id
    std::thread::id empty_id{};
    std::thread empty_thread;
    std::cout << "Empty thread id == default id: " 
              << (empty_thread.get_id() == empty_id) << "\n";  // 1

    return 0;
}
```

#### 总结

- `std::this_thread::get_id()` 获取当前线程 ID，`thread::get_id()` 获取对象管理的线程 ID。
- `thread::id` 可比较和输出，但具体值由实现决定。
- 默认构造的 `thread::id{}` 表示"无线程"，常用于判断 thread 是否有效。
- `hardware_concurrency()` 返回硬件并发数，返回 0 时应回退到默认值。
- 线程 ID 常用于调试日志、主线程判断、线程局部存储。
- 不要假设 ID 是连续整数或可转换为整数，应将其视为不透明类型。

---

### 第9讲 线程异常安全

#### 概念

异常安全是 C++ 并发编程中的关键问题。当线程函数抛出异常，或主线程在 join 之前抛出异常时，如果没有正确处理，会导致程序崩溃或资源泄漏。本讲讲解如何编写异常安全的并发代码。

#### 原理

**异常安全的两个层面**：

1. **线程函数内部的异常**：如果线程函数抛出异常且未被捕获，会调用 `std::terminate()` 终止整个程序。线程函数必须自己捕获所有异常，或通过 `std::promise`/`std::future` 将异常传递给等待方。

2. **主线程在 join 前抛异常**：考虑以下代码：
   ```cpp
   std::thread t(worker);
   do_something_that_might_throw();  // 如果抛异常
   t.join();  // 永远不会执行，t 析构时 terminate
   ```
   如果中间代码抛异常，`t.join()` 不会执行，`t` 析构时调用 `std::terminate`。

**解决方案**：

- **RAII 包装**：用 `scoped_thread` 或 `joining_thread`，在析构时自动 join，无论是否异常。
- **try-catch**：手动捕获异常并在 catch 中 join，但代码冗余且易错。
- **传递异常**：线程函数内部 catch 异常，通过 `promise.set_exception()` 传递给 future。

**异常传播机制**：

当线程函数通过 `std::promise` 设置异常时，等待 `future.get()` 的线程会重新抛出该异常。这是跨线程传递异常的标准方式。

#### 例子

```cpp
#include <iostream>
#include <thread>
#include <stdexcept>
#include <future>
#include <vector>

// RAII 包装器：确保异常安全
class joining_thread {
    std::thread t;
public:
    joining_thread() = default;
    template<typename F, typename... Args>
    explicit joining_thread(F&& f, Args&&... args)
        : t(std::forward<F>(f), std::forward<Args>(args)...) {}
    joining_thread(joining_thread&&) = default;
    joining_thread& operator=(joining_thread&& other) {
        if (t.joinable()) t.join();
        t = std::move(other.t);
        return *this;
    }
    ~joining_thread() { if (t.joinable()) t.join(); }
};

// 危险示例：异常导致 terminate
void dangerous_example() {
    std::thread t([]() {
        std::this_thread::sleep_for(std::chrono::seconds(1));
    });
    throw std::runtime_error("Oops!");  // t 析构时 terminate
    t.join();
}

// 安全示例1：RAII
void safe_example_raii() {
    joining_thread t([]() {
        std::this_thread::sleep_for(std::chrono::seconds(1));
    });
    throw std::runtime_error("Oops!");  // t 析构时自动 join
}

// 安全示例2：线程函数捕获异常
void worker_safely() {
    try {
        // 模拟可能失败的操作
        throw std::runtime_error("Worker error");
    } catch (const std::exception& e) {
        std::cerr << "Worker caught: " << e.what() << "\n";
    }
}

// 通过 future 传递异常
int risky_computation() {
    throw std::runtime_error("Computation failed");
    return 42;
}

int main() {
    // 1. 线程函数自己捕获异常
    std::thread t1(worker_safely);
    t1.join();

    // 2. 通过 async/future 传递异常
    std::future<int> f = std::async(std::launch::async, risky_computation);
    try {
        int result = f.get();  // 这里重新抛出异常
    } catch (const std::exception& e) {
        std::cerr << "Main caught from future: " << e.what() << "\n";
    }

    // 3. RAII 保证异常安全
    try {
        safe_example_raii();
    } catch (const std::exception& e) {
        std::cerr << "Main caught: " << e.what() << "\n";
    }

    // 4. 多线程场景的异常安全
    std::vector<joining_thread> threads;
    try {
        for (int i = 0; i < 5; ++i) {
            threads.emplace_back([](int id) {
                std::this_thread::sleep_for(std::chrono::milliseconds(100 * id));
                std::cout << "Thread " << id << " done\n";
            }, i);
        }
        // 即使这里抛异常，所有线程也会在 vector 析构时被 join
        // throw std::runtime_error("Mid-way error");
    } catch (...) {
        std::cerr << "Caught exception, threads will be joined\n";
    }

    return 0;
}
```

**通过 promise 手动传递异常**：

```cpp
void worker_with_promise(std::promise<int> p) {
    try {
        throw std::runtime_error("Worker failed");
        p.set_value(42);
    } catch (...) {
        // 将当前异常存入 promise
        p.set_exception(std::current_exception());
    }
}

void promise_exception_example() {
    std::promise<int> p;
    std::future<int> f = p.get_future();
    std::thread t(worker_with_promise, std::move(p));
    
    try {
        int v = f.get();  // 重新抛出 worker 中的异常
        std::cout << "Got: " << v << "\n";
    } catch (const std::exception& e) {
        std::cerr << "Caught: " << e.what() << "\n";
    }
    t.join();
}
```

#### 总结

- 线程函数中未捕获的异常会导致 `std::terminate`，必须在线程内捕获。
- 主线程在 join 前抛异常会导致 thread 析构时 terminate。
- RAII 包装（`joining_thread` 或 C++20 `jthread`）是保证异常安全的最佳实践。
- 通过 `promise.set_exception(std::current_exception())` 可跨线程传递异常。
- `std::async` 自动捕获并传递异常，是最简单的异常安全异步方案。
- 容器存储 RAII 线程对象时，即使中途抛异常，所有线程也会在容器析构时被正确 join。

---



## 第三章 互斥与共享数据

### 第10讲 共享数据问题

#### 概念

当多个线程同时访问同一份数据时，就会产生**共享数据问题**。如果至少一个线程在进行写操作，且没有适当的同步机制，就会发生**数据竞争（data race）**，导致未定义行为。这是并发编程中最常见、最危险的 bug 来源。

#### 原理

**数据竞争的定义**：当两个或更多线程同时访问同一内存位置，且至少一个是写操作，且它们没有使用同步机制保证访问的有序性时，就构成数据竞争。C++ 标准规定数据竞争是**未定义行为（Undefined Behavior, UB）**——程序可能产生错误结果、崩溃，或表现出任何不可预测的行为。

**竞态条件（Race Condition）** vs **数据竞争**：
- 数据竞争是语言层面的概念，指无同步的并发内存访问。
- 竞态条件是逻辑层面的概念，指程序的正确性依赖于线程执行的相对时序。
- 数据竞争一定导致竞态条件，但竞态条件不一定有数据竞争（如两个线程都只读，但逻辑上依赖时序）。

**为什么 `++counter` 不是原子的**：

`++counter` 看起来是一条语句，但在机器层面通常分为三步：
1. 从内存读取 counter 到寄存器
2. 寄存器值加 1
3. 将寄存器值写回内存

如果两个线程同时执行，可能发生：
```
线程A: 读 counter=0
线程B: 读 counter=0
线程A: 加1, counter=1
线程B: 加1, counter=1   ← 应该是2，但变成了1
```
这就是经典的"丢失更新"问题。

**解决方案**：
1. **互斥锁（mutex）**：确保同一时刻只有一个线程能访问共享数据。
2. **原子操作（atomic）**：让操作在硬件层面不可分割。
3. **避免共享**：让每个线程操作自己的数据，最后合并结果。

#### 例子

```cpp
#include <iostream>
#include <thread>
#include <vector>
#include <mutex>
#include <atomic>

int counter_unsafe = 0;
int counter_mutex = 0;
std::atomic<int> counter_atomic{0};
std::mutex mtx;

void increment_unsafe(int n) {
    for (int i = 0; i < n; ++i) {
        ++counter_unsafe;  // 数据竞争！
    }
}

void increment_mutex(int n) {
    for (int i = 0; i < n; ++i) {
        std::lock_guard<std::mutex> lock(mtx);
        ++counter_mutex;
    }
}

void increment_atomic(int n) {
    for (int i = 0; i < n; ++i) {
        ++counter_atomic;  // 原子操作，安全
    }
}

int main() {
    const int N = 100000;
    const int T = 10;

    // 1. 不安全版本
    {
        std::vector<std::thread> threads;
        for (int i = 0; i < T; ++i)
            threads.emplace_back(increment_unsafe, N);
        for (auto& t : threads) t.join();
        std::cout << "Unsafe: " << counter_unsafe
                  << " (expected " << N * T << ")\n";
    }

    // 2. mutex 保护
    {
        std::vector<std::thread> threads;
        for (int i = 0; i < T; ++i)
            threads.emplace_back(increment_mutex, N);
        for (auto& t : threads) t.join();
        std::cout << "Mutex: " << counter_mutex
                  << " (expected " << N * T << ")\n";
    }

    // 3. 原子操作
    {
        std::vector<std::thread> threads;
        for (int i = 0; i < T; ++i)
            threads.emplace_back(increment_atomic, N);
        for (auto& t : threads) t.join();
        std::cout << "Atomic: " << counter_atomic
                  << " (expected " << N * T << ")\n";
    }

    return 0;
}
```

可能的输出：
```
Unsafe: 523421 (expected 1000000)   ← 每次结果不同，且小于期望值
Mutex: 1000000 (expected 1000000)
Atomic: 1000000 (expected 1000000)
```

**用 ThreadSanitizer 检测数据竞争**：

```bash
g++ -std=c++17 -fsanitize=thread -g main.cpp -o main && ./main
```

ThreadSanitizer 会在运行时报告数据竞争的位置，是排查并发 bug 的利器。

#### 总结

- 数据竞争是并发编程的头号敌人，会导致未定义行为。
- `++counter` 不是原子操作，多线程下会丢失更新。
- 解决方案：互斥锁、原子操作、避免共享。
- ThreadSanitizer (`-fsanitize=thread`) 是检测数据竞争的强大工具。
- 即使程序"看起来"运行正常，也不能证明没有数据竞争——可能在特定调度下才暴露。
- 永远不要依赖"运气"来避免数据竞争，必须显式同步。

---

### 第11讲 std::mutex 与 RAII 锁

#### 概念

`std::mutex`（互斥量）是最基本的同步原语，用于保护共享数据。它确保同一时刻只有一个线程能进入临界区。C++ 提供了多种 RAII 风格的锁管理器：`std::lock_guard`、`std::unique_lock`、`std::scoped_lock`，它们在构造时加锁、析构时解锁，保证异常安全。

#### 原理

**mutex 的工作机制**：

mutex 内部维护一个状态（锁定/未锁定）和一个等待队列。当线程调用 `lock()` 时：
- 如果 mutex 未锁定，当前线程获得锁，状态变为锁定。
- 如果 mutex 已被其他线程锁定，当前线程阻塞，加入等待队列。
- 如果同一线程再次调用 `lock()`（非递归 mutex），会死锁。

`unlock()` 释放锁，唤醒等待队列中的一个线程。

**RAII 锁管理器**：

直接调用 `lock()`/`unlock()` 容易因异常或提前 return 导致忘记解锁，造成死锁。RAII 锁管理器解决了这个问题：

| 类型 | 特点 | 适用场景 |
|------|------|----------|
| `lock_guard<M>` | 构造加锁，析构解锁，不可解锁 | 简单临界区 |
| `unique_lock<M>` | 可随时 lock/unlock，可移动 | 配合条件变量、延迟加锁 |
| `scoped_lock` (C++17) | 可同时锁多个 mutex | 多锁场景，避免死锁 |

**性能考量**：
- mutex 加锁/解锁通常需要几十纳秒（无竞争时）。
- 竞争激烈时，线程会阻塞（陷入内核态），开销可达微秒级。
- 临界区应尽量短，只包含必要的共享数据操作。

#### 例子

```cpp
#include <iostream>
#include <thread>
#include <vector>
#include <mutex>
#include <stack>

// 线程安全的栈
template<typename T>
class thread_safe_stack {
    std::stack<T> data;
    mutable std::mutex m;
public:
    void push(T value) {
        std::lock_guard<std::mutex> lock(m);
        data.push(std::move(value));
    }

    // 问题：empty() 和 pop() 之间可能有其他线程修改
    bool empty() const {
        std::lock_guard<std::mutex> lock(m);
        return data.empty();
    }

    // 返回是否弹出成功，避免异常安全问题
    bool pop(T& value) {
        std::lock_guard<std::mutex> lock(m);
        if (data.empty()) return false;
        value = std::move(data.top());
        data.pop();
        return true;
    }
};

int main() {
    thread_safe_stack<int> stack;
    std::vector<std::thread> threads;

    // 生产者
    for (int i = 0; i < 3; ++i) {
        threads.emplace_back([&stack, i]() {
            for (int j = 0; j < 100; ++j) {
                stack.push(i * 100 + j);
            }
        });
    }

    // 消费者
    for (int i = 0; i < 3; ++i) {
        threads.emplace_back([&stack]() {
            int value;
            while (true) {
                if (stack.pop(value)) {
                    std::cout << "Popped: " << value << "\n";
                }
            }
        });
    }

    // 简单起见，运行一段时间后退出
    std::this_thread::sleep_for(std::chrono::seconds(1));
    return 0;  // 注意：消费者线程仍在运行，这里会 terminate
}
```

**unique_lock 的灵活性**：

```cpp
#include <mutex>
#include <condition_variable>

std::mutex mtx;
std::condition_variable cv;
bool ready = false;

void worker() {
    std::unique_lock<std::mutex> lock(mtx);
    // unique_lock 可以临时解锁
    lock.unlock();
    std::cout << "Doing non-critical work\n";
    lock.lock();

    // 配合条件变量使用（必须用 unique_lock）
    cv.wait(lock, []() { return ready; });
    std::cout << "Worker proceeding\n";
}
```

**延迟加锁与尝试加锁**：

```cpp
std::mutex mtx;

void try_work() {
    std::unique_lock<std::mutex> lock(mtx, std::defer_lock);  // 不立即加锁
    // 做一些准备工作...
    lock.lock();  // 现在加锁
    // 临界区
    lock.unlock();  // 提前解锁
    // 后续非临界区工作
}

void try_lock_example() {
    std::unique_lock<std::mutex> lock(mtx, std::try_to_lock);
    if (lock.owns_lock()) {
        std::cout << "Got the lock\n";
    } else {
        std::cout << "Lock busy, do something else\n";
    }
}
```

#### 总结

- `std::mutex` 是最基本的同步原语，保护临界区。
- 永远不要直接调用 `lock()`/`unlock()`，使用 RAII 锁管理器。
- `lock_guard` 最简单高效，适合简单临界区。
- `unique_lock` 更灵活，支持延迟加锁、尝试加锁、临时解锁，是条件变量的必备搭档。
- `scoped_lock`（C++17）可同时锁多个 mutex，自动避免死锁。
- 临界区应尽量短，减少锁持有时间。
- 设计线程安全接口时，注意复合操作的原子性（如 empty() + pop() 之间的竞态）。

---

### 第12讲 死锁与避免策略

#### 概念

**死锁（Deadlock）** 是指两个或多个线程互相等待对方持有的资源，导致所有线程都无法继续执行的状态。死锁是并发编程中最棘手的问题之一，因为它不会导致崩溃，而是让程序"挂起"，难以复现和调试。

#### 原理

**死锁的四个必要条件**（Coffman 条件）：

1. **互斥**：资源同一时刻只能被一个线程持有。
2. **持有并等待**：线程持有资源的同时等待其他资源。
3. **不可剥夺**：资源不能被强制夺走，只能由持有者主动释放。
4. **循环等待**：存在线程的循环等待链。

只要破坏其中任何一个条件，就能避免死锁。

**经典死锁场景**：两个线程各自持有一把锁，同时请求对方的锁：

```
线程A: lock(mtx1) → 请求 lock(mtx2)
线程B: lock(mtx2) → 请求 lock(mtx1)
```

如果 A 持有 mtx1 时 B 持有 mtx2，两者互相等待，死锁。

**避免死锁的策略**：

1. **固定加锁顺序**：所有线程按相同顺序获取多把锁，破坏循环等待。
2. **std::lock / std::scoped_lock**：一次性获取多把锁，内部用避免死锁的算法（如 try-and-back-off）。
3. **std::try_lock + 回退**：尝试加锁，失败则释放已持有的锁重试。
4. **层次锁**：将锁分层，规定只能从高层向低层加锁。
5. **避免嵌套锁**：尽量不在持有锁时再获取其他锁。

#### 例子

```cpp
#include <iostream>
#include <thread>
#include <mutex>
#include <chrono>

std::mutex mtx1, mtx2;

// 死锁示例
void deadlock_example() {
    auto f = [](bool first_mtx1) {
        if (first_mtx1) {
            std::lock_guard<std::mutex> l1(mtx1);
            std::this_thread::sleep_for(std::chrono::milliseconds(10));
            std::lock_guard<std::mutex> l2(mtx2);  // 等待 mtx2
            std::cout << "Thread A done\n";
        } else {
            std::lock_guard<std::mutex> l2(mtx2);
            std::this_thread::sleep_for(std::chrono::milliseconds(10));
            std::lock_guard<std::mutex> l1(mtx1);  // 等待 mtx1
            std::cout << "Thread B done\n";
        }
    };
    // std::thread t1(f, true);
    // std::thread t2(f, false);
    // t1.join(); t2.join();  // 死锁！
}

// 解决方案1：固定加锁顺序
void fixed_order_example() {
    auto f = []() {
        // 所有线程都先锁 mtx1 再锁 mtx2
        std::lock_guard<std::mutex> l1(mtx1);
        std::this_thread::sleep_for(std::chrono::milliseconds(10));
        std::lock_guard<std::mutex> l2(mtx2);
        std::cout << "Thread done\n";
    };
    std::thread t1(f), t2(f);
    t1.join(); t2.join();
}

// 解决方案2：std::scoped_lock（C++17，推荐）
void scoped_lock_example() {
    auto f = []() {
        // 一次性锁住两把锁，内部算法避免死锁
        std::scoped_lock lock(mtx1, mtx2);
        std::cout << "Thread done with scoped_lock\n";
    };
    std::thread t1(f), t2(f);
    t1.join(); t2.join();
}

// 解决方案3：std::lock + adopt_lock（C++11/14）
void lock_adopt_example() {
    auto f = []() {
        std::lock(mtx1, mtx2);  // 原子地锁住两把锁
        std::lock_guard<std::mutex> l1(mtx1, std::adopt_lock);
        std::lock_guard<std::mutex> l2(mtx2, std::adopt_lock);
        std::cout << "Thread done with lock+adopt\n";
    };
    std::thread t1(f), t2(f);
    t1.join(); t2.join();
}

// 解决方案4：try_lock + 回退
void try_lock_example() {
    auto f = []() {
        while (true) {
            std::unique_lock<std::mutex> l1(mtx1);
            std::unique_lock<std::mutex> l2(mtx2, std::defer_lock);
            if (l2.try_lock()) {
                std::cout << "Thread got both locks\n";
                return;
            }
            // 失败，释放 l1，稍后重试
            std::this_thread::sleep_for(std::chrono::milliseconds(1));
        }
    };
    std::thread t1(f), t2(f);
    t1.join(); t2.join();
}

int main() {
    fixed_order_example();
    scoped_lock_example();
    lock_adopt_example();
    try_lock_example();
    return 0;
}
```

**层次锁示例**：

```cpp
class hierarchical_mutex {
    std::mutex internal_mutex;
    unsigned long const hierarchy_value;
    unsigned long previous_hierarchy_value;
    static thread_local unsigned long this_thread_hierarchy;

    void check_for_hierarchy_violation() {
        if (this_thread_hierarchy <= hierarchy_value) {
            throw std::logic_error("mutex hierarchy violated");
        }
    }
    void update_hierarchy_value() {
        previous_hierarchy_value = this_thread_hierarchy;
        this_thread_hierarchy = hierarchy_value;
    }
public:
    explicit hierarchical_mutex(unsigned long value)
        : hierarchy_value(value), previous_hierarchy_value(0) {}

    void lock() {
        check_for_hierarchy_violation();
        internal_mutex.lock();
        update_hierarchy_value();
    }
    void unlock() {
        this_thread_hierarchy = previous_hierarchy_value;
        internal_mutex.unlock();
    }
    bool try_lock() {
        check_for_hierarchy_violation();
        if (!internal_mutex.try_lock()) return false;
        update_hierarchy_value();
        return true;
    }
};
thread_local unsigned long hierarchical_mutex::this_thread_hierarchy(ULONG_MAX);

// 使用：规定只能从高层次向低层次加锁
hierarchical_mutex high_mtx(10000);
hierarchical_mutex mid_mtx(5000);
hierarchical_mutex low_mtx(1000);

void safe_func() {
    std::lock_guard<hierarchical_mutex> h(high_mutex);
    std::lock_guard<hierarchical_mutex> m(mid_mutex);
    std::lock_guard<hierarchical_mutex> l(low_mutex);  // 正确：递减
    // ...
}
```

#### 总结

- 死锁的四个必要条件：互斥、持有并等待、不可剥夺、循环等待。
- 最常见的死锁：多个线程以不同顺序获取多把锁。
- 最佳实践：用 `std::scoped_lock`（C++17）一次性锁住多把锁。
- C++11/14 用 `std::lock(m1, m2, ...)` + `adopt_lock`。
- 固定加锁顺序是简单有效的策略，但需要团队纪律。
- 层次锁通过运行时检查强制加锁顺序，适合复杂系统。
- 避免在持有锁时调用未知代码（回调），可能间接获取其他锁导致死锁。
- 用 `std::try_lock` + 回退可实现非阻塞的多锁获取。

---

### 第13讲 不同类型的 mutex

#### 概念

C++ 标准库提供了多种 mutex 变体，适应不同的并发场景。除了基本的 `std::mutex`，还有 `std::recursive_mutex`、`std::timed_mutex`、`std::recursive_timed_mutex`、`std::shared_mutex`（C++17）。理解它们的差异有助于选择合适的同步工具。

#### 原理

**mutex 家族对比**：

| 类型 | 递归加锁 | 超时 | 共享/独占 | 引入标准 |
|------|---------|------|----------|---------|
| `std::mutex` | 否（死锁） | 否 | 仅独占 | C++11 |
| `std::recursive_mutex` | 是 | 否 | 仅独占 | C++11 |
| `std::timed_mutex` | 否 | 是 | 仅独占 | C++11 |
| `std::recursive_timed_mutex` | 是 | 是 | 仅独占 | C++11 |
| `std::shared_mutex` | 否 | 否 | 共享+独占 | C++17 |
| `std::shared_timed_mutex` | 否 | 是 | 共享+独占 | C++14 |

**各类型详解**：

1. **`std::mutex`**：最常用，性能最好。同一线程二次加锁会死锁。

2. **`std::recursive_mutex`**：允许同一线程多次加锁，内部计数器记录次数，需相同次数的 unlock 才真正释放。适用于递归函数或复杂调用链，但通常意味着设计有问题（应重构而非用递归锁）。

3. **`std::timed_mutex`**：支持 `try_lock_for()`、`try_lock_until()`，避免无限等待。

4. **`std::shared_mutex`**（读写锁）：
   - **共享锁（读锁）**：多个线程可同时持有，用 `lock_shared()` / `shared_lock`。
   - **独占锁（写锁）**：只有一个线程能持有，用 `lock()` / `unique_lock`。
   - 适用于读多写少的场景，可显著提高并发度。

**性能注意**：
- `shared_mutex` 比 `mutex` 更重，读操作多时才有优势。
- `recursive_mutex` 比 `mutex` 慢，因为要维护计数器和线程 ID。
- 如果临界区很短，`mutex` 可能比 `shared_mutex` 更快。

#### 例子

```cpp
#include <iostream>
#include <thread>
#include <mutex>
#include <shared_mutex>
#include <chrono>
#include <vector>
#include <map>

// 1. recursive_mutex：递归函数中的锁
class RecursiveCounter {
    std::recursive_mutex mtx;
    int count = 0;
public:
    void increment(int n) {
        std::lock_guard<std::recursive_mutex> lock(mtx);
        ++count;
        if (n > 1) {
            increment(n - 1);  // 递归调用，再次加锁
        }
    }
    int get() {
        std::lock_guard<std::recursive_mutex> lock(mtx);
        return count;
    }
};

// 2. timed_mutex：避免无限等待
std::timed_mutex timed_mtx;
void try_lock_with_timeout() {
    auto start = std::chrono::steady_clock::now();
    std::unique_lock<std::timed_mutex> lock(timed_mtx, std::defer_lock);
    if (lock.try_lock_for(std::chrono::milliseconds(100))) {
        std::cout << "Got lock\n";
    } else {
        auto end = std::chrono::steady_clock::now();
        auto ms = std::chrono::duration_cast<std::chrono::milliseconds>(end - start).count();
        std::cout << "Timeout after " << ms << "ms\n";
    }
}

// 3. shared_mutex：读写锁
class ThreadSafeMap {
    std::map<std::string, int> data;
    mutable std::shared_mutex rw_mtx;
public:
    // 读操作：共享锁
    int get(const std::string& key) const {
        std::shared_lock<std::shared_mutex> lock(rw_mtx);
        auto it = data.find(key);
        return it != data.end() ? it->second : -1;
    }

    // 写操作：独占锁
    void set(const std::string& key, int value) {
        std::unique_lock<std::shared_mutex> lock(rw_mtx);
        data[key] = value;
    }

    // 检查并设置（需要独占锁）
    bool set_if_absent(const std::string& key, int value) {
        std::unique_lock<std::shared_mutex> lock(rw_mtx);
        if (data.find(key) == data.end()) {
            data[key] = value;
            return true;
        }
        return false;
    }
};

int main() {
    // 测试 recursive_mutex
    RecursiveCounter rc;
    rc.increment(5);
    std::cout << "Count: " << rc.get() << "\n";  // 5

    // 测试 timed_mutex
    {
        std::lock_guard<std::timed_mutex> holder(timed_mtx);
        std::thread t(try_lock_with_timeout);
        t.join();
    }

    // 测试 shared_mutex
    ThreadSafeMap map;
    std::vector<std::thread> threads;

    // 写线程
    for (int i = 0; i < 2; ++i) {
        threads.emplace_back([&map, i]() {
            for (int j = 0; j < 100; ++j) {
                map.set("key" + std::to_string(i), i * 100 + j);
            }
        });
    }

    // 读线程（多个并发读）
    for (int i = 0; i < 8; ++i) {
        threads.emplace_back([&map]() {
            for (int j = 0; j < 1000; ++j) {
                map.get("key0");
            }
        });
    }

    for (auto& t : threads) t.join();
    std::cout << "Map operations done\n";

    return 0;
}
```

#### 总结

- `std::mutex` 是默认选择，性能最好。
- `recursive_mutex` 允许同一线程多次加锁，但通常意味着设计缺陷。
- `timed_mutex` 支持超时，避免无限阻塞。
- `shared_mutex`（读写锁）适合读多写少场景，可大幅提升并发度。
- `shared_lock` 用于读，`unique_lock` 用于写。
- 临界区很短时，`mutex` 可能比 `shared_mutex` 更快（锁开销小于读写分离收益）。
- 不要过度使用递归锁，应重构代码避免嵌套加锁。

---

### 第14讲 保护共享数据的初始化

#### 概念

延迟初始化（lazy initialization）是常见模式：在第一次使用时才初始化资源。但在多线程环境下，简单的"检查-初始化"模式会有数据竞争。C++ 提供了多种安全的方法来保护共享数据的初始化。

#### 原理

**问题的根源**：

```cpp
std::shared_ptr<SomeResource> resource;
std::mutex mtx;

void lazy_init() {
    if (!resource) {           // 检查：可能多个线程同时通过
        std::lock_guard<std::mutex> lock(mtx);
        if (!resource) {       // 双重检查
            resource = std::make_shared<SomeResource>();
        }
    }
    resource->do_something();
}
```

这是经典的"双重检查锁定（Double-Checked Locking, DCLP）"模式。但在 C++11 之前，这是有 bug 的：编译器和 CPU 可能重排指令，导致 `resource` 在构造完成前就被设为非空，其他线程看到非空但实际未初始化的对象。

**C++11 的解决方案**：

1. **`std::call_once` + `std::once_flag`**：保证某函数只执行一次，线程安全。
2. **局部静态变量**：C++11 起保证局部静态变量的初始化是线程安全的。
3. **`std::atomic` 初始化**：用原子操作实现 DCLP。

**局部静态变量的线程安全**：

```cpp
Singleton& get_instance() {
    static Singleton instance;  // C++11 保证线程安全初始化
    return instance;
}
```

这是最简洁的单例模式实现，编译器内部用 `__cxa_guard_acquire` / `__cxa_guard_release`（GCC）或类似机制保证原子性。

#### 例子

```cpp
#include <iostream>
#include <thread>
#include <mutex>
#include <memory>
#include <vector>

class HeavyResource {
public:
    HeavyResource() {
        std::cout << "HeavyResource constructing...\n";
        std::this_thread::sleep_for(std::chrono::milliseconds(100));
        std::cout << "HeavyResource constructed\n";
    }
    void use() { std::cout << "Using resource\n"; }
};

// 方法1：双重检查锁定（C++11 起安全，用 atomic）
std::shared_ptr<HeavyResource> resource1;
std::mutex mtx1;
std::atomic<bool> initialized{false};

void use_resource_dclp() {
    if (!initialized.load(std::memory_order_acquire)) {
        std::lock_guard<std::mutex> lock(mtx1);
        if (!initialized.load(std::memory_order_relaxed)) {
            resource1 = std::make_shared<HeavyResource>();
            initialized.store(true, std::memory_order_release);
        }
    }
    resource1->use();
}

// 方法2：std::call_once（推荐）
std::shared_ptr<HeavyResource> resource2;
std::once_flag flag2;

void init_resource2() {
    resource2 = std::make_shared<HeavyResource>();
}

void use_resource_call_once() {
    std::call_once(flag2, init_resource2);
    resource2->use();
}

// 方法3：局部静态变量（最简洁）
HeavyResource& get_resource() {
    static HeavyResource instance;  // 线程安全初始化
    return instance;
}

void use_resource_static() {
    get_resource().use();
}

// 单例模式
class Singleton {
public:
    static Singleton& instance() {
        static Singleton inst;
        return inst;
    }
    Singleton(const Singleton&) = delete;
    Singleton& operator=(const Singleton&) = delete;
private:
    Singleton() { std::cout << "Singleton created\n"; }
};

int main() {
    std::cout << "=== Testing call_once ===\n";
    std::vector<std::thread> threads;
    for (int i = 0; i < 5; ++i) {
        threads.emplace_back(use_resource_call_once);
    }
    for (auto& t : threads) t.join();

    std::cout << "\n=== Testing static local ===\n";
    threads.clear();
    for (int i = 0; i < 5; ++i) {
        threads.emplace_back(use_resource_static);
    }
    for (auto& t : threads) t.join();

    std::cout << "\n=== Testing Singleton ===\n";
    threads.clear();
    for (int i = 0; i < 5; ++i) {
        threads.emplace_back([]() { Singleton::instance(); });
    }
    for (auto& t : threads) t.join();

    return 0;
}
```

**call_once 的灵活性**：

```cpp
// call_once 可以调用任意可调用对象
std::once_flag config_flag;
Config* config = nullptr;

void load_config() {
    std::call_once(config_flag, []() {
        config = Config::load_from_file("config.json");
    });
}

// 也可以带参数
std::once_flag init_flag;
void initialize(int x, const std::string& y) {
    std::call_once(init_flag, [](int a, const std::string& b) {
        // 初始化逻辑
    }, x, y);
}
```

#### 总结

- 延迟初始化在多线程下需要特别小心，简单的"检查-初始化"有数据竞争。
- C++11 起局部静态变量的初始化是线程安全的，是最简洁的方案。
- `std::call_once` + `std::once_flag` 是显式的线程安全一次性初始化机制。
- 双重检查锁定在 C++11 后可用 `std::atomic` 正确实现，但代码复杂，不推荐。
- 单例模式首选 Meyers' Singleton（局部静态变量）。
- `call_once` 比 mutex 更轻量，专门用于一次性初始化。
- 初始化完成后，后续调用 `call_once` 几乎无开销（一次原子读）。

---


## 第四章 条件变量与等待

### 第15讲 条件变量基础

#### 概念

**条件变量（Condition Variable）** 是一种同步原语，用于线程间的等待-通知机制。它允许线程在某个条件不满足时挂起等待，直到另一个线程改变了条件并发出通知。`std::condition_variable` 定义在 `<condition_variable>` 中，是 C++ 中实现生产者-消费者模式的核心工具。

#### 原理

**为什么需要条件变量**：

考虑一个简单的"等待数据"场景：
```cpp
// 轮询方式（低效）
while (!data_ready) {
    // 忙等待，浪费 CPU
}
process(data);
```

这种轮询方式有两个问题：
1. **浪费 CPU**：线程不断循环检查条件，消耗大量 CPU 周期。
2. **延迟**：如果 sleep 时间长，响应慢；sleep 时间短，CPU 浪费多。

条件变量解决了这两个问题：线程在条件不满足时**真正挂起**（不占 CPU），当条件可能满足时被**唤醒**。

**条件变量的工作机制**：

```
等待线程：                          通知线程：
1. lock(mutex)                     1. lock(mutex)
2. 检查条件                         2. 修改共享数据
3. 条件不满足：                     3. unlock(mutex)
   cv.wait(lock) 释放锁并阻塞  <─── 4. cv.notify_one/all() 唤醒等待者
4. 被唤醒，重新获取锁               5. ...
5. 再次检查条件（可能虚假唤醒）
6. 条件满足，继续执行
```

**关键点**：
- `wait()` 会**原子地**释放锁并阻塞线程，被唤醒时**重新获取锁**。
- 必须用 `unique_lock`（不能用 `lock_guard`），因为 `wait` 需要内部解锁/加锁。
- **虚假唤醒（Spurious Wakeup）**：线程可能在没有 notify 的情况下被唤醒，所以必须用循环检查条件。

**wait 的两种形式**：

```cpp
// 形式1：手动循环（容易出错）
std::unique_lock<std::mutex> lock(mtx);
while (!ready) {
    cv.wait(lock);
}

// 形式2：谓词形式（推荐，自动处理虚假唤醒）
std::unique_lock<std::mutex> lock(mtx);
cv.wait(lock, []() { return ready; });
```

#### 例子

```cpp
#include <iostream>
#include <thread>
#include <mutex>
#include <condition_variable>

std::mutex mtx;
std::condition_variable cv;
bool ready = false;

void waiting_worker(int id) {
    std::unique_lock<std::mutex> lock(mtx);
    // 等待 ready 变为 true
    cv.wait(lock, []() { return ready; });
    std::cout << "Worker " << id << " proceeding\n";
}

void go() {
    std::this_thread::sleep_for(std::chrono::seconds(1));
    {
        std::lock_guard<std::mutex> lock(mtx);
        ready = true;
    }
    cv.notify_all();  // 唤醒所有等待者
}

int main() {
    std::thread t1(waiting_worker, 1);
    std::thread t2(waiting_worker, 2);
    std::thread t3(waiting_worker, 3);

    std::thread starter(go);

    t1.join(); t2.join(); t3.join();
    starter.join();
    return 0;
}
```

**notify_one vs notify_all**：

```cpp
// notify_one：只唤醒一个等待线程（适合生产者-消费者，每次处理一个任务）
cv.notify_one();

// notify_all：唤醒所有等待线程（适合广播事件，如"开始"信号）
cv.notify_all();
```

**带超时的等待**：

```cpp
std::condition_variable cv;
std::mutex mtx;

void wait_with_timeout() {
    std::unique_lock<std::mutex> lock(mtx);
    // 等待最多 5 秒
    auto status = cv.wait_for(lock, std::chrono::seconds(5), []() {
        return condition_met;
    });
    if (status) {
        std::cout << "Condition met\n";
    } else {
        std::cout << "Timeout\n";
    }
}
```

#### 总结

- 条件变量用于线程间的等待-通知，避免低效的轮询。
- `wait()` 原子地释放锁并阻塞，被唤醒时重新获取锁。
- 必须配合 `unique_lock` 使用（不能用 `lock_guard`）。
- 必须用谓词形式的 `wait(lock, predicate)` 或 while 循环，防止虚假唤醒。
- `notify_one()` 唤醒一个等待者，`notify_all()` 唤醒所有。
- 修改条件后必须先 unlock 再 notify（或用 `notify_all` 后 unlock 也行，但顺序影响效率）。
- `wait_for` / `wait_until` 支持超时等待。

---

### 第16讲 生产者-消费者模型

#### 概念

**生产者-消费者模型**是并发编程中最经典的模式之一。生产者线程生成数据放入缓冲区，消费者线程从缓冲区取出数据处理。条件变量是实现这一模式的标准工具，用于在缓冲区满/空时协调生产者和消费者。

#### 原理

**模型结构**：

```
┌──────────┐     ┌─────────────────┐     ┌──────────┐
│ Producer │ ──> │  Buffer (Queue) │ <── │ Consumer │
│          │     │  [有限容量]      │     │          │
└──────────┘     └─────────────────┘     └──────────┘
                   ↑                  ↑
              not_full              not_empty
              条件变量              条件变量
```

**同步规则**：
1. 缓冲区满时，生产者必须等待（`not_full`）。
2. 缓冲区空时，消费者必须等待（`not_empty`）。
3. 生产者放入数据后，通知消费者（`not_empty.notify_one()`）。
4. 消费者取出数据后，通知生产者（`not_full.notify_one()`）。

**为什么需要两个条件变量**：

如果只用一个条件变量，`notify_one` 可能唤醒同类型的线程（如生产者唤醒生产者），导致无效唤醒。用两个条件变量（`not_full` 和 `not_empty`）可以精确唤醒对方类型的线程。

**有界缓冲区 vs 无界缓冲区**：
- 有界缓冲区：容量有限，需要 `not_full` 条件，防止生产者淹没消费者。
- 无界缓冲区：容量无限，只需 `not_empty` 条件，但可能内存耗尽。

#### 例子

```cpp
#include <iostream>
#include <thread>
#include <mutex>
#include <condition_variable>
#include <queue>
#include <vector>

template<typename T>
class BoundedBuffer {
    std::queue<T> queue;
    size_t capacity;
    std::mutex mtx;
    std::condition_variable not_full;
    std::condition_variable not_empty;

public:
    explicit BoundedBuffer(size_t cap) : capacity(cap) {}

    void put(T value) {
        std::unique_lock<std::mutex> lock(mtx);
        not_full.wait(lock, [this]() { return queue.size() < capacity; });
        queue.push(std::move(value));
        not_empty.notify_one();  // 通知一个消费者
    }

    T take() {
        std::unique_lock<std::mutex> lock(mtx);
        not_empty.wait(lock, [this]() { return !queue.empty(); });
        T value = std::move(queue.front());
        queue.pop();
        not_full.notify_one();  // 通知一个生产者
        return value;
    }

    size_t size() {
        std::lock_guard<std::mutex> lock(mtx);
        return queue.size();
    }
};

int main() {
    BoundedBuffer<int> buffer(10);  // 容量 10

    // 生产者
    auto producer = [&buffer](int id) {
        for (int i = 0; i < 20; ++i) {
            int item = id * 100 + i;
            buffer.put(item);
            std::cout << "Producer " << id << " put " << item << "\n";
            std::this_thread::sleep_for(std::chrono::milliseconds(10));
        }
    };

    // 消费者
    auto consumer = [&buffer](int id) {
        for (int i = 0; i < 20; ++i) {
            int item = buffer.take();
            std::cout << "Consumer " << id << " got " << item << "\n";
            std::this_thread::sleep_for(std::chrono::milliseconds(50));
        }
    };

    std::vector<std::thread> threads;
    threads.emplace_back(producer, 1);
    threads.emplace_back(producer, 2);
    threads.emplace_back(consumer, 1);
    threads.emplace_back(consumer, 2);

    for (auto& t : threads) t.join();
    return 0;
}
```

**停止机制**：

生产者-消费者需要优雅停止。常见做法是放入"毒丸"（poison pill）：

```cpp
// 用特殊值表示结束
void producer(BoundedBuffer<int>& buf) {
    for (int i = 0; i < 100; ++i) {
        buf.put(i);
    }
    buf.put(-1);  // 毒丸，表示结束
}

void consumer(BoundedBuffer<int>& buf) {
    while (true) {
        int item = buf.take();
        if (item == -1) {
            buf.put(-1);  // 传递给其他消费者
            break;
        }
        process(item);
    }
}
```

或者用原子标志位：

```cpp
std::atomic<bool> stop{false};

void consumer(BoundedBuffer<int>& buf) {
    while (true) {
        int item;
        // 需要带超时的 take，否则永远阻塞
        // 或者修改 BoundedBuffer 支持 stop 检查
        if (buf.try_take(item, stop)) {
            if (stop) break;
            process(item);
        }
    }
}
```

#### 总结

- 生产者-消费者是解耦生产与消费速度的经典模式。
- 有界缓冲区需要两个条件变量：`not_full` 和 `not_empty`。
- 生产者等待 `not_full`，放入后通知 `not_empty`。
- 消费者等待 `not_empty`，取出后通知 `not_full`。
- `notify_one` 比 `notify_all` 更高效（避免惊群效应）。
- 优雅停止需要"毒丸"机制或原子停止标志。
- 缓冲区大小影响吞吐量和延迟，需根据场景调优。

---

### 第17讲 线程安全队列实现

#### 概念

线程安全队列是并发编程中最常用的数据结构之一。本讲将实现一个完整的、可等待的线程安全队列，结合互斥锁和条件变量，支持多生产者多消费者场景，并处理异常安全和停止机制。

#### 原理

**设计要点**：

1. **互斥保护**：用 `std::mutex` 保护内部 `std::queue`。
2. **条件变量**：用 `not_empty` 通知消费者有数据可取。
3. **异常安全**：`pop` 操作返回值而非引用，避免拷贝失败导致数据丢失。
4. **try_pop**：非阻塞版本，立即返回是否有数据。
5. **wait_and_pop**：阻塞版本，等待直到有数据。
6. **停止机制**：支持优雅关闭，唤醒所有等待者。

**为什么 pop 返回值而非引用**：

如果 `pop` 先弹出再拷贝，拷贝时抛异常会导致数据丢失。正确做法是先拷贝到结果变量，再弹出：

```cpp
// 错误：拷贝可能抛异常，数据已弹出
T pop() {
    T value = std::move(queue.front());  // 可能抛异常
    queue.pop();
    return value;
}

// 正确：先移动再弹出
T value = std::move(queue.front());
queue.pop();  // 不会抛异常
return value;
```

#### 例子

```cpp
#include <iostream>
#include <thread>
#include <mutex>
#include <condition_variable>
#include <queue>
#include <memory>
#include <vector>
#include <optional>

template<typename T>
class ThreadSafeQueue {
    mutable std::mutex mtx;
    std::queue<T> queue;
    std::condition_variable cv;
    bool stopped = false;

public:
    ThreadSafeQueue() = default;
    ThreadSafeQueue(const ThreadSafeQueue&) = delete;
    ThreadSafeQueue& operator=(const ThreadSafeQueue&) = delete;

    // 阻塞式取出
    bool wait_and_pop(T& value) {
        std::unique_lock<std::mutex> lock(mtx);
        cv.wait(lock, [this]() { return !queue.empty() || stopped; });
        if (stopped && queue.empty()) return false;
        value = std::move(queue.front());
        queue.pop();
        return true;
    }

    // 非阻塞式取出
    bool try_pop(T& value) {
        std::lock_guard<std::mutex> lock(mtx);
        if (queue.empty()) return false;
        value = std::move(queue.front());
        queue.pop();
        return true;
    }

    // 带超时的取出
    bool wait_and_pop_for(T& value, std::chrono::milliseconds timeout) {
        std::unique_lock<std::mutex> lock(mtx);
        if (!cv.wait_for(lock, timeout, [this]() { 
            return !queue.empty() || stopped; 
        })) {
            return false;  // 超时
        }
        if (stopped && queue.empty()) return false;
        value = std::move(queue.front());
        queue.pop();
        return true;
    }

    // 放入数据
    void push(T value) {
        {
            std::lock_guard<std::mutex> lock(mtx);
            if (stopped) return;  // 已停止，不再接收
            queue.push(std::move(value));
        }
        cv.notify_one();
    }

    // 停止队列，唤醒所有等待者
    void stop() {
        {
            std::lock_guard<std::mutex> lock(mtx);
            stopped = true;
        }
        cv.notify_all();
    }

    bool empty() const {
        std::lock_guard<std::mutex> lock(mtx);
        return queue.empty();
    }

    size_t size() const {
        std::lock_guard<std::mutex> lock(mtx);
        return queue.size();
    }
};

// 使用示例
int main() {
    ThreadSafeQueue<int> queue;

    auto producer = [&queue](int id) {
        for (int i = 0; i < 10; ++i) {
            int item = id * 10 + i;
            queue.push(item);
            std::cout << "Producer " << id << " pushed " << item << "\n";
            std::this_thread::sleep_for(std::chrono::milliseconds(10));
        }
    };

    auto consumer = [&queue](int id) {
        int value;
        while (queue.wait_and_pop(value)) {
            std::cout << "Consumer " << id << " got " << value << "\n";
            std::this_thread::sleep_for(std::chrono::milliseconds(20));
        }
        std::cout << "Consumer " << id << " exiting\n";
    };

    std::vector<std::thread> threads;
    threads.emplace_back(producer, 1);
    threads.emplace_back(producer, 2);
    threads.emplace_back(consumer, 1);
    threads.emplace_back(consumer, 2);

    // 等待生产者完成
    std::this_thread::sleep_for(std::chrono::milliseconds(500));
    
    // 停止队列
    queue.stop();

    for (auto& t : threads) t.join();
    return 0;
}
```

#### 总结

- 线程安全队列是并发编程的基础组件。
- 用 `mutex` + `condition_variable` 实现等待-通知。
- `wait_and_pop` 阻塞等待，`try_pop` 非阻塞，`wait_and_pop_for` 带超时。
- pop 操作要先移动数据再弹出，保证异常安全。
- 停止机制：设置标志位后 `notify_all`，唤醒所有等待者。
- 队列不可拷贝（含 mutex），应明确删除拷贝操作。
- `mutable mutex` 允许 const 方法加锁。

---

### 第18讲 等待一次性事件

#### 概念

有时线程需要等待某个**一次性事件**：如初始化完成、配置加载完毕、超时触发。条件变量虽然可以实现，但有点"杀鸡用牛刀"。C++ 提供了更轻量的工具：`std::future`（配合 `std::promise` 或 `std::async`）用于等待一次性结果，以及简单的布尔标志 + 条件变量组合用于一次性信号。

#### 原理

**一次性事件的特点**：
- 事件只发生一次，发生后永远保持"已发生"状态。
- 多个线程可以同时等待同一事件。
- 事件发生后，所有等待者都应被唤醒。

**实现方式对比**：

| 方式 | 优点 | 缺点 |
|------|------|------|
| bool + condition_variable | 灵活，可重置 | 代码冗余 |
| std::future/promise | 语义清晰，支持结果传递 | 一次性，不可重置 |
| std::experimental::latch (C++20) | 专为一次性等待设计 | 需要 C++20 |
| std::binary_semaphore (C++20) | 轻量，高效 | 语义不如 future 直观 |

**future 作为一次性事件**：

```cpp
std::promise<void> p;
std::future<void> f = p.get_future();

// 等待线程
f.wait();  // 阻塞直到事件发生

// 触发线程
p.set_value();  // 触发事件
```

`std::future<void>` 不携带数据，纯粹作为一次性信号。多个线程等待同一事件需要 `std::shared_future`。

#### 例子

```cpp
#include <iostream>
#include <thread>
#include <future>
#include <vector>
#include <chrono>
#include <mutex>

std::mutex cout_mtx;
void log(const std::string& msg) {
    std::lock_guard<std::mutex> lock(cout_mtx);
    std::cout << "[" << std::this_thread::get_id() << "] " << msg << "\n";
}

// 方式1：promise/future 作为一次性信号
void example_promise_future() {
    std::promise<void> ready_promise;
    std::shared_future<void> ready_future = ready_promise.get_future().share();

    auto worker = [ready_future](int id) {
        log("Worker " + std::to_string(id) + " waiting...");
        ready_future.wait();  // 等待信号
        log("Worker " + std::to_string(id) + " started!");
    };

    std::vector<std::thread> threads;
    for (int i = 0; i < 5; ++i) {
        threads.emplace_back(worker, i);
    }

    std::this_thread::sleep_for(std::chrono::seconds(1));
    log("Main sending ready signal");
    ready_promise.set_value();  // 触发事件

    for (auto& t : threads) t.join();
}

// 方式2：带数据的一次性事件
void example_with_data() {
    std::promise<int> data_promise;
    std::shared_future<int> data_future = data_promise.get_future().share();

    auto worker = [data_future](int id) {
        int data = data_future.get();  // 等待并获取数据
        log("Worker " + std::to_string(id) + " got data: " + std::to_string(data));
    };

    std::vector<std::thread> threads;
    for (int i = 0; i < 3; ++i) {
        threads.emplace_back(worker, i);
    }

    std::this_thread::sleep_for(std::chrono::milliseconds(500));
    data_promise.set_value(42);  // 发送数据

    for (auto& t : threads) t.join();
}

// 方式3：超时等待
void example_timeout() {
    std::promise<void> p;
    std::future<void> f = p.get_future();

    std::thread t([&f]() {
        auto status = f.wait_for(std::chrono::seconds(2));
        if (status == std::future_status::ready) {
            log("Event occurred");
        } else if (status == std::future_status::timeout) {
            log("Timeout!");
        }
    });

    // 不触发事件，让等待超时
    t.join();
}

int main() {
    std::cout << "=== Promise/Future as signal ===\n";
    example_promise_future();

    std::cout << "\n=== With data ===\n";
    example_with_data();

    std::cout << "\n=== Timeout ===\n";
    example_timeout();

    return 0;
}
```

**实际应用：等待初始化完成**：

```cpp
class Application {
    std::promise<void> initialized;
    std::shared_future<void> init_future;

public:
    Application() : init_future(initialized.get_future().share()) {}

    void initialize() {
        // 模拟耗时初始化
        std::this_thread::sleep_for(std::chrono::seconds(2));
        initialized.set_value();  // 通知所有等待者
    }

    void wait_until_ready() {
        init_future.wait();
    }

    bool is_ready() {
        return init_future.wait_for(std::chrono::seconds(0)) 
               == std::future_status::ready;
    }
};
```

#### 总结

- 一次性事件用 `promise<void>` + `shared_future<void>` 实现最清晰。
- `shared_future` 允许多个线程等待同一事件。
- `future.wait()` 阻塞等待，`wait_for` / `wait_until` 支持超时。
- 带数据的事件用 `promise<T>` + `shared_future<T>`。
- 条件变量也能实现，但 future 语义更明确，代码更简洁。
- C++20 的 `std::latch` 和 `std::binary_semaphore` 是更轻量的替代方案。

---

## 第五章 Future 与异步

### 第19讲 std::async 异步任务

#### 概念

`std::async` 是 C++ 提供的高层异步编程接口，定义在 `<future>` 中。它启动一个异步任务并返回 `std::future`，让程序员可以方便地获取异步结果。`std::async` 自动管理线程创建、异常传递，是最简单的并发工具。

#### 原理

**std::async 的函数签名**：

```cpp
template<typename F, typename... Args>
std::future<std::invoke_result_t<F, Args...>> 
async(std::launch policy, F&& f, Args&&... args);
```

**启动策略（std::launch）**：

| 策略 | 行为 |
|------|------|
| `std::launch::async` | 立即创建新线程执行任务 |
| `std::launch::deferred` | 延迟执行，直到 `future.get()` 被调用时才在当前线程同步执行 |
| `std::launch::async \| std::launch::deferred`（默认） | 由实现决定，可能立即异步也可能延迟 |

**默认策略的陷阱**：

默认策略（`async | deferred`）让实现自行选择，这意味着：
- 任务可能不会立即执行（如果是 deferred）。
- 任务可能在调用 `get()` 时才在当前线程同步执行，失去并发性。
- 无法预测任务何时执行，可能导致死锁或性能问题。

**最佳实践**：始终显式指定 `std::launch::async`，除非确实需要延迟执行。

**future 的生命周期**：
- `async` 返回的 future 析构时会阻塞等待任务完成（仅对 `async` 返回的 future 如此）。
- 这意味着即使不调用 `get()`，future 析构也会等待。

#### 例子

```cpp
#include <iostream>
#include <future>
#include <thread>
#include <chrono>
#include <vector>

int slow_computation(int x) {
    std::this_thread::sleep_for(std::chrono::seconds(1));
    return x * x;
}

int main() {
    // 1. 基本用法：异步执行并获取结果
    auto start = std::chrono::steady_clock::now();
    
    std::future<int> f = std::async(std::launch::async, slow_computation, 10);
    
    // 主线程做其他工作
    std::cout << "Doing other work while async runs...\n";
    std::this_thread::sleep_for(std::chrono::milliseconds(500));
    
    int result = f.get();  // 阻塞等待结果
    auto end = std::chrono::steady_clock::now();
    auto ms = std::chrono::duration_cast<std::chrono::milliseconds>(end - start).count();
    
    std::cout << "Result: " << result << " in " << ms << "ms\n";
    // 输出：Result: 100 in ~1000ms（异步并行，总时间约1秒）

    // 2. 并行多个任务
    start = std::chrono::steady_clock::now();
    
    std::vector<std::future<int>> futures;
    for (int i = 1; i <= 5; ++i) {
        futures.push_back(std::async(std::launch::async, slow_computation, i));
    }
    
    int sum = 0;
    for (auto& fut : futures) {
        sum += fut.get();
    }
    
    end = std::chrono::steady_clock::now();
    ms = std::chrono::duration_cast<std::chrono::milliseconds>(end - start).count();
    
    std::cout << "Sum: " << sum << " in " << ms << "ms\n";
    // 5个任务并行，总时间约1秒而非5秒

    // 3. deferred 策略：延迟执行
    std::cout << "\nDeferred example:\n";
    auto f2 = std::async(std::launch::deferred, []() {
        std::cout << "Running in " 
                  << (std::this_thread::get_id() == std::this_thread::get_id() 
                      ? "calling" : "new") 
                  << " thread\n";
        return 42;
    });
    std::cout << "Before get()\n";
    std::cout << "Result: " << f2.get() << "\n";  // 这里才执行
    std::cout << "After get()\n";

    return 0;
}
```

**异常传递**：

```cpp
std::future<int> f = std::async(std::launch::async, []() -> int {
    throw std::runtime_error("Async error");
});

try {
    int v = f.get();  // 这里重新抛出异常
} catch (const std::exception& e) {
    std::cerr << "Caught: " << e.what() << "\n";
}
```

**fire-and-forget 陷阱**：

```cpp
// 危险：future 是临时对象，立即析构，会阻塞等待任务完成
std::async(std::launch::async, []() { 
    long_running_task(); 
});  // 这里会阻塞直到 long_running_task 完成！

// 正确：保存 future
auto f = std::async(std::launch::async, []() { 
    long_running_task(); 
});
// f 在作用域结束时才析构等待
```

#### 总结

- `std::async` 是最简单的异步任务启动方式。
- 始终显式指定 `std::launch::async`，避免默认策略的不确定性。
- `future.get()` 阻塞等待结果，自动传递异常。
- `async` 返回的 future 析构时会阻塞等待任务完成。
- 不要忽略 `async` 的返回值——临时 future 析构会阻塞。
- 并行多个独立任务时，`async` 比手动管理线程更简洁。
- `deferred` 策略用于延迟执行，实际是同步的。

---

### 第20讲 future 与 shared_future

#### 概念

`std::future` 是异步结果的载体，提供访问异步结果的唯一通道。`std::shared_future` 允许多个线程等待同一个异步结果。理解两者的区别和正确使用场景是异步编程的关键。

#### 原理

**future 的语义**：
- **唯一性**：`future` 是移动语义类型，只能有一个 `future` 引用某个异步结果。
- **一次性**：`get()` 只能调用一次，调用后 future 变为无效。
- **不可共享**：多个线程不能同时 `get()` 同一个 `future`。

**shared_future 的语义**：
- **可共享**：`shared_future` 是可拷贝的，多个实例可以引用同一结果。
- **多次 get**：每个 `shared_future` 实例都可以调用 `get()`。
- **一致性**：所有实例看到的结果相同（包括异常）。

**转换关系**：
```cpp
std::promise<int> p;
std::future<int> f = p.get_future();        // 从 promise 获取 future
std::shared_future<int> sf = f.share();     // future 转为 shared_future
std::shared_future<int> sf2 = sf;           // 拷贝
```

**get() 的行为**：
- `future::get()`：移动结果，只能调用一次。
- `shared_future::get()`：返回 const 引用（或拷贝），可多次调用。
- 如果异步任务抛异常，`get()` 会重新抛出该异常。

#### 例子

```cpp
#include <iostream>
#include <future>
#include <thread>
#include <vector>

int main() {
    // 1. future 的唯一性
    {
        std::promise<int> p;
        std::future<int> f = p.get_future();
        
        // std::future<int> f2 = f;  // 编译错误：不可拷贝
        std::future<int> f2 = std::move(f);  // 可以移动
        
        p.set_value(42);
        std::cout << "f2.get() = " << f2.get() << "\n";
        // std::cout << "f2.get() = " << f2.get() << "\n";  // 错误：已 get 过
    }

    // 2. shared_future 的共享性
    {
        std::promise<int> p;
        std::shared_future<int> sf = p.get_future().share();
        
        std::vector<std::thread> threads;
        for (int i = 0; i < 5; ++i) {
            threads.emplace_back([sf, i]() {
                // 每个线程都有自己的 shared_future 拷贝
                int value = sf.get();  // 可以多次 get
                std::cout << "Thread " << i << " got " << value << "\n";
            });
        }
        
        p.set_value(100);
        for (auto& t : threads) t.join();
    }

    // 3. 异常的共享
    {
        std::promise<void> p;
        std::shared_future<void> sf = p.get_future().share();
        
        std::vector<std::thread> threads;
        for (int i = 0; i < 3; ++i) {
            threads.emplace_back([sf, i]() {
                try {
                    sf.get();  // 所有线程都会收到异常
                } catch (const std::exception& e) {
                    std::cout << "Thread " << i << " caught: " << e.what() << "\n";
                }
            });
        }
        
        p.set_exception(std::make_exception_ptr(std::runtime_error("Shared error")));
        for (auto& t : threads) t.join();
    }

    // 4. 等待状态查询
    {
        std::promise<int> p;
        std::future<int> f = p.get_future();
        
        std::thread producer([&p]() {
            std::this_thread::sleep_for(std::chrono::milliseconds(100));
            p.set_value(42);
        });
        
        while (true) {
            auto status = f.wait_for(std::chrono::milliseconds(30));
            if (status == std::future_status::ready) {
                std::cout << "Result ready: " << f.get() << "\n";
                break;
            } else if (status == std::future_status::timeout) {
                std::cout << "Still waiting...\n";
            } else if (status == std::future_status::deferred) {
                std::cout << "Deferred\n";
            }
        }
        
        producer.join();
    }

    return 0;
}
```

#### 总结

- `future` 是唯一的、一次性的异步结果访问通道。
- `shared_future` 允许多个线程共享同一异步结果。
- `future::get()` 只能调用一次，`shared_future::get()` 可多次调用。
- `future.share()` 将 future 转为 shared_future，原 future 失效。
- 异步任务的异常会通过 `get()` 重新抛出，shared_future 让所有等待者都收到。
- `wait_for` / `wait_until` 返回状态：`ready`、`timeout`、`deferred`。
- 多线程等待同一结果时，必须用 `shared_future`。

---

### 第21讲 promise 与异步通信

#### 概念

`std::promise` 是 `std::future` 的"生产者"端。promise 持有一个共享状态，可以设置值或异常，对应的 future 可以获取这些值或异常。promise/future 对是线程间传递结果的标准机制，比直接共享变量更安全、更清晰。

#### 原理

**promise-future 对的工作机制**：

```
┌─────────────┐         ┌──────────────────┐         ┌────────┐
│  Producer   │ ──set──>│  Shared State    │<──get── │Consumer│
│  (promise)  │         │  (值/异常/状态)   │         │(future)│
└─────────────┘         └──────────────────┘         └────────┘
```

1. `promise` 和 `future` 共享一个内部状态（shared state）。
2. `promise::set_value()` 设置结果，`future::get()` 获取结果。
3. 结果只能设置一次，重复设置抛 `std::future_error`。
4. `promise` 析构时如果未设置值，会自动设置 `broken_promise` 异常。

**promise 的关键方法**：
- `set_value(value)`：设置正常结果。
- `set_exception(exception_ptr)`：设置异常。
- `set_value_at_thread_exit(value)`：在线程退出时设置结果（延迟通知）。
- `get_future()`：获取关联的 future，只能调用一次。

**为什么需要 promise**：

`std::async` 隐藏了线程创建细节，但有时需要更精细的控制：
- 使用线程池而非每次创建新线程。
- 任务需要分阶段执行，中间设置结果。
- 多个结果需要分别传递。
- 需要手动控制异常传递。

#### 例子

```cpp
#include <iostream>
#include <future>
#include <thread>
#include <vector>
#include <cmath>

// 基本用法：线程间传递结果
void worker(std::promise<int> result_promise) {
    try {
        std::this_thread::sleep_for(std::chrono::milliseconds(100));
        result_promise.set_value(42);  // 设置结果
    } catch (...) {
        result_promise.set_exception(std::current_exception());
    }
}

void basic_example() {
    std::promise<int> p;
    std::future<int> f = p.get_future();
    std::thread t(worker, std::move(p));
    
    std::cout << "Waiting for result...\n";
    std::cout << "Got: " << f.get() << "\n";
    t.join();
}

// 异常传递
void worker_with_error(std::promise<int> p) {
    try {
        throw std::runtime_error("Something went wrong");
    } catch (...) {
        p.set_exception(std::current_exception());  // 传递异常
    }
}

void exception_example() {
    std::promise<int> p;
    std::future<int> f = p.get_future();
    std::thread t(worker_with_error, std::move(p));
    
    try {
        int v = f.get();  // 重新抛出异常
    } catch (const std::exception& e) {
        std::cout << "Caught: " << e.what() << "\n";
    }
    t.join();
}

// 线程池式任务执行
class ThreadPool {
    std::vector<std::thread> workers;
    std::queue<std::function<void()>> tasks;
    std::mutex mtx;
    std::condition_variable cv;
    bool stop = false;

public:
    ThreadPool(size_t n) {
        for (size_t i = 0; i < n; ++i) {
            workers.emplace_back([this]() {
                while (true) {
                    std::function<void()> task;
                    {
                        std::unique_lock<std::mutex> lock(mtx);
                        cv.wait(lock, [this]() { return stop || !tasks.empty(); });
                        if (stop && tasks.empty()) return;
                        task = std::move(tasks.front());
                        tasks.pop();
                    }
                    task();
                }
            });
        }
    }

    template<typename F, typename... Args>
    auto submit(F&& f, Args&&... args) -> std::future<decltype(f(args...))> {
        using R = decltype(f(args...));
        auto task = std::make_shared<std::packaged_task<R()>>(
            std::bind(std::forward<F>(f), std::forward<Args>(args)...)
        );
        std::future<R> result = task->get_future();
        {
            std::lock_guard<std::mutex> lock(mtx);
            tasks.emplace([task]() { (*task)(); });
        }
        cv.notify_one();
        return result;
    }

    ~ThreadPool() {
        {
            std::lock_guard<std::mutex> lock(mtx);
            stop = true;
        }
        cv.notify_all();
        for (auto& w : workers) w.join();
    }
};

void thread_pool_example() {
    ThreadPool pool(4);
    
    std::vector<std::future<int>> results;
    for (int i = 0; i < 8; ++i) {
        results.push_back(pool.submit([](int x) {
            std::this_thread::sleep_for(std::chrono::milliseconds(100));
            return x * x;
        }, i));
    }
    
    for (auto& f : results) {
        std::cout << "Result: " << f.get() << "\n";
    }
}

int main() {
    std::cout << "=== Basic ===\n";
    basic_example();
    
    std::cout << "\n=== Exception ===\n";
    exception_example();
    
    std::cout << "\n=== Thread Pool ===\n";
    thread_pool_example();
    
    return 0;
}
```

**broken_promise**：

```cpp
void broken_promise_example() {
    std::future<int> f;
    {
        std::promise<int> p;
        f = p.get_future();
        // p 析构时未设置值
    }  // p 在这里析构
    
    try {
        int v = f.get();  // 抛出 broken_promise 异常
    } catch (const std::future_error& e) {
        std::cout << "Future error: " << e.what() << "\n";
        // 输出：broken promise
    }
}
```

#### 总结

- `promise` 是结果的生产者，`future` 是结果的消费者。
- `set_value` 设置正常结果，`set_exception` 传递异常。
- 结果只能设置一次，重复设置抛 `future_error`。
- promise 析构时未设置值，future 会收到 `broken_promise` 异常。
- promise/future 对适用于线程池、任务队列等需要精细控制的场景。
- `set_value_at_thread_exit` 延迟设置，确保线程局部变量先析构。
- promise 不可拷贝，只能移动，通常通过 `std::move` 传递给工作线程。

---

### 第22讲 packaged_task

#### 概念

`std::packaged_task` 是 `std::promise` 的包装器，将一个可调用对象包装为异步任务。它自动管理 promise-future 对，省去手动设置值的麻烦。`packaged_task` 适合将普通函数转换为异步任务，或在线程池中调度任务。

#### 原理

**packaged_task 的工作机制**：

```
┌──────────────────────────────────────┐
│         packaged_task<R(Args...)>    │
│  ┌────────────┐    ┌──────────────┐  │
│  │ 可调用对象  │───>│   promise    │  │
│  │  (task)    │    │              │  │
│  └────────────┘    └──────┬───────┘  │
└───────────────────────────┼──────────┘
                            │
                            v
                     ┌────────────┐
                     │   future   │
                     └────────────┘
```

1. 构造时传入可调用对象，内部创建 promise。
2. `get_future()` 获取关联的 future。
3. 调用 `operator()` 执行任务，结果自动存入 promise。
4. 如果任务抛异常，异常自动存入 promise。

**与 promise 的对比**：

| 特性 | promise | packaged_task |
|------|---------|---------------|
| 设置结果 | 手动 `set_value` | 自动（调用任务后） |
| 异常处理 | 手动 `set_exception` | 自动捕获 |
| 可调用对象 | 无 | 内置 |
| 灵活性 | 高 | 中 |
| 易用性 | 低 | 高 |

**与 async 的对比**：

| 特性 | async | packaged_task |
|------|-------|---------------|
| 线程管理 | 自动创建 | 需手动调度 |
| 执行时机 | 立即（async策略） | 由调用者决定 |
| 适用场景 | 简单异步 | 线程池、任务队列 |

#### 例子

```cpp
#include <iostream>
#include <future>
#include <thread>
#include <functional>

int compute(int x) {
    std::this_thread::sleep_for(std::chrono::milliseconds(100));
    if (x < 0) throw std::invalid_argument("negative input");
    return x * x;
}

int main() {
    // 1. 基本用法
    {
        std::packaged_task<int(int)> task(compute);
        std::future<int> f = task.get_future();
        
        std::thread t(std::move(task), 10);
        std::cout << "Result: " << f.get() << "\n";  // 100
        t.join();
    }

    // 2. 用 lambda
    {
        std::packaged_task<int(int)> task([](int x) {
            return x + 100;
        });
        std::future<int> f = task.get_future();
        
        task(5);  // 可以在当前线程直接调用
        std::cout << "Result: " << f.get() << "\n";  // 105
    }

    // 3. 异常自动传递
    {
        std::packaged_task<int(int)> task(compute);
        std::future<int> f = task.get_future();
        
        std::thread t(std::move(task), -1);
        try {
            f.get();  // 重新抛出异常
        } catch (const std::exception& e) {
            std::cout << "Caught: " << e.what() << "\n";
        }
        t.join();
    }

    // 4. 在线程池中使用
    {
        std::vector<std::packaged_task<int()>> tasks;
        std::vector<std::future<int>> futures;
        
        for (int i = 0; i < 5; ++i) {
            auto pt = std::make_shared<std::packaged_task<int()>>(
                [i]() { return compute(i); }
            );
            futures.push_back(pt->get_future());
            tasks.emplace_back([pt]() { (*pt)(); });
        }
        
        // 在线程中执行
        std::vector<std::thread> threads;
        for (auto& t : tasks) {
            threads.emplace_back(std::move(t));
        }
        
        for (auto& f : futures) {
            std::cout << "Result: " << f.get() << "\n";
        }
        for (auto& t : threads) t.join();
    }

    // 5. 包装成 std::function
    {
        std::packaged_task<int(int, int)> task([](int a, int b) {
            return a + b;
        });
        std::future<int> f = task.get_future();
        
        // packaged_task 是可调用对象，但不能直接转为 std::function
        // 因为 packaged_task 是移动语义
        // 需要用 shared_ptr 包装
        auto sp = std::make_shared<std::packaged_task<int(int,int)>>(std::move(task));
        std::function<void(int, int)> func = [sp](int a, int b) { (*sp)(a, b); };
        
        func(3, 4);
        std::cout << "Result: " << f.get() << "\n";  // 7
    }

    return 0;
}
```

#### 总结

- `packaged_task` 包装可调用对象，自动管理 promise-future 对。
- 调用 `operator()` 执行任务，结果自动存入 future。
- 异常自动捕获并传递给 future。
- 适合线程池、任务队列等需要任务调度的场景。
- `packaged_task` 不可拷贝，只能移动。
- 包装成 `std::function` 时需用 `shared_ptr`。
- 比 `promise` 更易用，比 `async` 更灵活。

---

### 第23讲 异常处理与超时

#### 概念

异步编程中的异常处理和超时管理是两个重要主题。异常需要跨线程边界传递，超时需要避免无限等待。本讲讲解如何正确处理异步任务中的异常，以及如何实现超时机制。

#### 原理

**异步异常传递机制**：

当异步任务抛出异常时：
1. `std::async`、`std::packaged_task`、`std::promise` 都会自动捕获异常。
2. 异常被存储为 `std::exception_ptr`。
3. 调用 `future::get()` 时，异常被重新抛出。
4. `shared_future::get()` 让所有等待者都收到异常。

**超时等待**：

`future` 提供三种等待方式：
- `wait()`：无限等待。
- `wait_for(duration)`：等待指定时长。
- `wait_until(time_point)`：等待到指定时间点。

返回 `std::future_status`：
- `ready`：结果已就绪。
- `timeout`：超时未就绪。
- `deferred`：任务是延迟执行的（需要调用 get 才会执行）。

**超时后的处理**：
- 超时不是错误，只是状态。
- 超时后可以继续等待、放弃、或采取其他行动。
- 注意：超时后任务仍在后台运行，无法取消（C++ 标准未提供取消机制）。

#### 例子

```cpp
#include <iostream>
#include <future>
#include <thread>
#include <chrono>
#include <vector>

// 1. 异常传递
void exception_example() {
    auto f = std::async(std::launch::async, []() -> int {
        throw std::runtime_error("Task failed");
        return 42;
    });

    try {
        int v = f.get();  // 重新抛出异常
    } catch (const std::exception& e) {
        std::cout << "Caught: " << e.what() << "\n";
    }
}

// 2. 超时等待
void timeout_example() {
    auto f = std::async(std::launch::async, []() {
        std::this_thread::sleep_for(std::chrono::seconds(2));
        return 42;
    });

    auto status = f.wait_for(std::chrono::milliseconds(500));
    if (status == std::future_status::ready) {
        std::cout << "Ready: " << f.get() << "\n";
    } else if (status == std::future_status::timeout) {
        std::cout << "Timeout, still waiting...\n";
        // 可以继续等待
        std::cout << "Final: " << f.get() << "\n";
    }
}

// 3. 带超时的轮询
void polling_example() {
    auto f = std::async(std::launch::async, []() {
        std::this_thread::sleep_for(std::chrono::seconds(3));
        return "Done";
    });

    while (true) {
        auto status = f.wait_for(std::chrono::milliseconds(500));
        if (status == std::future_status::ready) {
            std::cout << "Got: " << f.get() << "\n";
            break;
        }
        std::cout << "Working...\n";
    }
}

// 4. 多任务超时：等待任一完成
void wait_any_example() {
    std::vector<std::future<int>> futures;
    for (int i = 0; i < 5; ++i) {
        futures.push_back(std::async(std::launch::async, [](int x) {
            std::this_thread::sleep_for(std::chrono::milliseconds(100 * x));
            return x;
        }, i));
    }

    // 轮询检查哪个完成了
    while (!futures.empty()) {
        for (auto it = futures.begin(); it != futures.end(); ) {
            if (it->wait_for(std::chrono::seconds(0)) == std::future_status::ready) {
                std::cout << "Got: " << it->get() << "\n";
                it = futures.erase(it);
            } else {
                ++it;
            }
        }
        std::this_thread::sleep_for(std::chrono::milliseconds(50));
    }
}

// 5. 实现可取消的任务
class CancellableTask {
    std::atomic<bool> cancel_flag{false};
    std::promise<int> promise;
    std::shared_future<int> future;

public:
    CancellableTask() : future(promise.get_future().share()) {}

    void run() {
        try {
            for (int i = 0; i < 100; ++i) {
                if (cancel_flag.load()) {
                    promise.set_exception(std::make_exception_ptr(
                        std::runtime_error("Cancelled")));
                    return;
                }
                std::this_thread::sleep_for(std::chrono::milliseconds(10));
            }
            promise.set_value(42);
        } catch (...) {
            promise.set_exception(std::current_exception());
        }
    }

    void cancel() { cancel_flag.store(true); }
    int get() { return future.get(); }
    bool is_ready() {
        return future.wait_for(std::chrono::seconds(0)) 
               == std::future_status::ready;
    }
};

void cancellation_example() {
    CancellableTask task;
    std::thread t(&CancellableTask::run, &task);

    std::this_thread::sleep_for(std::chrono::milliseconds(200));
    task.cancel();

    try {
        int v = task.get();
        std::cout << "Result: " << v << "\n";
    } catch (const std::exception& e) {
        std::cout << "Caught: " << e.what() << "\n";
    }
    t.join();
}

int main() {
    std::cout << "=== Exception ===\n";
    exception_example();

    std::cout << "\n=== Timeout ===\n";
    timeout_example();

    std::cout << "\n=== Polling ===\n";
    polling_example();

    std::cout << "\n=== Wait Any ===\n";
    wait_any_example();

    std::cout << "\n=== Cancellation ===\n";
    cancellation_example();

    return 0;
}
```

#### 总结

- 异步任务的异常通过 `future::get()` 重新抛出。
- `shared_future` 让所有等待者都收到异常。
- `wait_for` / `wait_until` 实现超时等待，返回状态枚举。
- 超时后任务仍在运行，C++ 标准未提供强制取消机制。
- 可通过原子标志位实现协作式取消。
- 多任务场景可用轮询实现"等待任一完成"。
- C++20 的 `std::stop_token` 提供了标准的协作式取消机制。

---


## 第六章 原子操作与内存模型

### 第24讲 C++ 内存模型基础

#### 概念

**内存模型（Memory Model）** 定义了多线程程序中内存操作的可见性和顺序性规则。C++11 引入了标准化的内存模型，这是 C++ 并发编程的理论基础。理解内存模型是掌握原子操作和无锁编程的前提。

#### 原理

**为什么需要内存模型**：

在没有内存模型的世界里，编译器和 CPU 会为了优化性能而重排指令。考虑：

```cpp
// 线程1
data = 42;       // (1)
ready = true;    // (2)

// 线程2
while (!ready);  // (3)
print(data);     // (4)
```

直觉上，线程2 应该打印 42。但编译器或 CPU 可能重排 (1) 和 (2)，导致线程2 看到 `ready=true` 但 `data` 还是旧值。内存模型规定了什么重排是允许的，什么是不允许的。

**C++ 内存模型的核心概念**：

1. **顺序一致性（Sequential Consistency, SC）**：
   - 最强的内存序。
   - 所有线程看到统一的操作顺序。
   - 全局有一个总顺序，所有原子操作按此顺序执行。
   - 最直观，但性能最低。

2. **修改顺序（Modification Order）**：
   - 每个原子变量有自己的修改顺序。
   - 所有线程对同一变量的写入达成一致顺序。
   - 但不同变量之间没有全局顺序保证。

3. **happens-before 关系**：
   - 如果操作 A happens-before 操作 B，则 A 的效果对 B 可见。
   - 来源：程序顺序（同一线程内）、同步操作（如 mutex、原子操作）。

4. **synchronizes-with 关系**：
   - 线程A的原子写操作与线程B的原子读操作建立同步。
   - 是 happens-before 的跨线程来源。

**六种内存序**：

| 内存序 | 说明 |
|--------|------|
| `memory_order_seq_cst` | 顺序一致性，最强保证 |
| `memory_order_acq_rel` | 获取-释放（读-改-写操作用） |
| `memory_order_release` | 释放（写操作用） |
| `memory_order_acquire` | 获取（读操作用） |
| `memory_order_consume` | 消费（数据依赖的获取，少用） |
| `memory_order_relaxed` | 宽松，无同步保证 |

**默认是顺序一致性**：不指定内存序时，原子操作使用 `memory_order_seq_cst`，提供最强保证但性能最低。

#### 例子

```cpp
#include <iostream>
#include <thread>
#include <atomic>

// 经典示例：为什么需要内存模型
int data = 0;
std::atomic<bool> ready{false};

void producer() {
    data = 42;  // (1) 普通写
    ready.store(true, std::memory_order_release);  // (2) 原子写，release
}

void consumer() {
    while (!ready.load(std::memory_order_acquire));  // (3) 原子读，acquire
    std::cout << "data = " << data << "\n";  // (4) 保证看到 42
}

int main() {
    std::thread t1(producer);
    std::thread t2(consumer);
    t1.join();
    t2.join();
    return 0;
}
```

**release-acquire 配对的原理**：
- (2) 是 release 写，保证 (1) 在 (2) 之前完成（对其他线程可见）。
- (3) 是 acquire 读，保证 (4) 在 (3) 之后执行。
- (2) synchronizes-with (3)，建立 happens-before 关系。
- 因此 (1) happens-before (4)，(4) 一定能看到 (1) 的写入。

**对比：relaxed 的危险**：

```cpp
int data = 0;
std::atomic<bool> ready{false};

void producer_bad() {
    data = 42;
    ready.store(true, std::memory_order_relaxed);  // 无同步保证！
}

void consumer_bad() {
    while (!ready.load(std::memory_order_relaxed));
    // data 可能是 0，也可能是 42，未定义
    std::cout << "data = " << data << "\n";
}
```

#### 总结

- 内存模型定义了多线程程序中内存操作的可见性和顺序性。
- 顺序一致性（SC）是最强的内存序，提供全局统一顺序。
- happens-before 是可见性的核心概念，来源于程序顺序和同步操作。
- release-acquire 配对建立 synchronizes-with 关系，传递 happens-before。
- 默认 `memory_order_seq_cst` 最安全但性能最低。
- `memory_order_relaxed` 无同步保证，仅保证原子性，慎用。
- 理解内存模型是编写正确无锁代码的前提。

---

### 第25讲 std::atomic 原子类型

#### 概念

`std::atomic` 是 C++ 提供的原子操作模板类，定义在 `<atomic>` 中。它保证对变量的操作是不可分割的，避免数据竞争。`std::atomic` 是无锁编程的基础，比 mutex 更轻量，适合简单的计数器、标志位等场景。

#### 原理

**原子操作的本质**：

原子操作在硬件层面通过特殊指令（如 x86 的 `LOCK` 前缀、`CMPXCHG`）实现，保证：
1. **原子性**：操作不可分割，要么完成，要么未开始。
2. **可见性**：操作结果对其他线程立即可见（配合适当内存序）。
3. **顺序性**：阻止编译器和 CPU 对原子操作周围的重排（配合内存序）。

**std::atomic 的操作**：

| 操作 | 说明 |
|------|------|
| `load()` | 原子读 |
| `store(val)` | 原子写 |
| `exchange(val)` | 原子交换，返回旧值 |
| `compare_exchange_strong(expected, desired)` | CAS，比较并交换 |
| `compare_exchange_weak(expected, desired)` | CAS，可能虚假失败 |
| `fetch_add(val)` | 原子加，返回旧值 |
| `fetch_sub(val)` | 原子减，返回旧值 |
| `fetch_and/or/xor(val)` | 原子位运算 |
| `++` / `--` | 原子自增/自减 |
| `+=` / `-=` 等 | 原子复合赋值 |

**标准原子类型**：

```cpp
std::atomic<bool>           // 原子布尔
std::atomic<int>            // 原子整数
std::atomic<int*>           // 原子指针
std::atomic<size_t>         // 原子无符号
// C++14: std::atomic_shared_ptr, std::atomic_weak_ptr (C++20 移到 std::atomic<std::shared_ptr<T>>)
```

**is_lock_free**：

```cpp
std::atomic<BigStruct> a;
bool lock_free = a.is_lock_free();  // 是否真正无锁
```
某些大类型的原子操作可能内部用锁实现。`is_lock_free()` 检查是否真正无锁。

#### 例子

```cpp
#include <iostream>
#include <atomic>
#include <thread>
#include <vector>

// 1. 原子计数器
std::atomic<int> counter{0};

void increment(int n) {
    for (int i = 0; i < n; ++i) {
        counter.fetch_add(1, std::memory_order_relaxed);
        // 或 counter++;
    }
}

void counter_example() {
    const int N = 100000;
    std::vector<std::thread> threads;
    for (int i = 0; i < 10; ++i) {
        threads.emplace_back(increment, N);
    }
    for (auto& t : threads) t.join();
    std::cout << "Counter: " << counter << " (expected " << 10 * N << ")\n";
}

// 2. 原子标志位
std::atomic<bool> flag{false};

void flag_example() {
    // 设置
    flag.store(true);
    
    // 读取
    bool v = flag.load();
    
    // 交换
    bool old = flag.exchange(false);
    std::cout << "Old flag: " << old << "\n";
}

// 3. CAS (Compare-And-Swap)
void cas_example() {
    std::atomic<int> value{10};
    
    // compare_exchange_strong
    int expected = 10;
    bool success = value.compare_exchange_strong(expected, 20);
    // 如果 value == expected，则 value = 20，返回 true
    // 否则 expected = value（当前值），返回 false
    std::cout << "CAS1: success=" << success << ", value=" << value << "\n";
    
    expected = 10;  // 现在 value 是 20，不匹配
    success = value.compare_exchange_strong(expected, 30);
    std::cout << "CAS2: success=" << success << ", value=" << value 
              << ", expected=" << expected << "\n";
}

// 4. 用 CAS 实现自旋锁
class SpinLock {
    std::atomic_flag locked = ATOMIC_FLAG_INIT;
public:
    void lock() {
        while (locked.test_and_set(std::memory_order_acquire)) {
            // 自旋等待
        }
    }
    void unlock() {
        locked.clear(std::memory_order_release);
    }
};

void spinlock_example() {
    SpinLock spin;
    int sum = 0;
    
    std::vector<std::thread> threads;
    for (int i = 0; i < 10; ++i) {
        threads.emplace_back([&]() {
            for (int j = 0; j < 1000; ++j) {
                std::lock_guard<SpinLock> lk(spin);
                sum++;
            }
        });
    }
    for (auto& t : threads) t.join();
    std::cout << "Sum: " << sum << "\n";
}

// 5. 原子指针
void pointer_example() {
    int x = 10, y = 20;
    std::atomic<int*> ptr{&x};
    
    int* p = ptr.load();
    std::cout << "Points to: " << *p << "\n";  // 10
    
    ptr.store(&y);
    p = ptr.load();
    std::cout << "Points to: " << *p << "\n";  // 20
    
    // 原子指针算术
    int arr[] = {1, 2, 3, 4, 5};
    std::atomic<int*> arr_ptr{arr};
    int* a = arr_ptr.fetch_add(2);  // 返回旧值，指针前进2
    std::cout << "Old: " << *a << ", New: " << *arr_ptr << "\n";  // Old:1, New:3
}

int main() {
    counter_example();
    flag_example();
    cas_example();
    spinlock_example();
    pointer_example();
    return 0;
}
```

#### 总结

- `std::atomic` 保证操作的原子性，避免数据竞争。
- 常用操作：`load`、`store`、`exchange`、`fetch_add`、`compare_exchange`。
- CAS（compare_exchange）是无锁算法的核心原语。
- `atomic_flag` 是最轻量的原子类型，适合实现自旋锁。
- `is_lock_free()` 检查是否真正无锁。
- 原子操作默认使用 `memory_order_seq_cst`，可指定更弱的内存序提升性能。
- 原子类型不可拷贝，只能通过 `load`/`store` 访问。

---

### 第26讲 内存序详解

#### 概念

内存序（Memory Order）控制原子操作周围的指令重排和可见性保证。选择合适的内存序可以在保证正确性的前提下提升性能。本讲详细讲解六种内存序的语义和适用场景。

#### 原理

**六种内存序详解**：

**1. memory_order_relaxed（宽松）**：
- 只保证原子性，不保证顺序和可见性。
- 编译器和 CPU 可以自由重排。
- 适用于不需要同步的计数器（如统计计数）。

```cpp
counter.fetch_add(1, std::memory_order_relaxed);
```

**2. memory_order_release（释放）**：
- 用于写操作（store）。
- 保证本操作之前的所有读写不会被重排到本操作之后。
- 与 acquire 配对，建立 synchronizes-with 关系。

**3. memory_order_acquire（获取）**：
- 用于读操作（load）。
- 保证本操作之后的所有读写不会被重排到本操作之前。
- 看到 release 写入的值时，建立同步关系。

**4. memory_order_acq_rel（获取-释放）**：
- 用于读-改-写操作（如 fetch_add、exchange）。
- 同时具有 acquire 和 release 语义。

**5. memory_order_seq_cst（顺序一致性）**：
- 最强保证。
- 除了 acquire-release 语义外，还保证全局统一顺序。
- 所有 seq_cst 操作有一个全局总顺序，所有线程看到相同顺序。

**6. memory_order_consume（消费）**：
- 类似 acquire，但只对有数据依赖的操作保证顺序。
- 标准委员会不推荐使用，多数实现等同于 acquire。

**release-acquire 配对模式**：

```
线程A:                          线程B:
data = 42;                     while (!ready.load(acquire));
ready.store(true, release);    print(data);  // 保证看到 42
```

**release-seq 模式**（多生产者）：

```
线程A: data1 = 1; flag.store(1, release);
线程B: data2 = 2; flag.store(2, release);
线程C: while (flag.load(acquire) == 0); 
       // 看到 1 或 2 后，对应的 data 也可见
```

#### 例子

```cpp
#include <iostream>
#include <atomic>
#include <thread>
#include <vector>

// 1. relaxed：无同步的计数
std::atomic<int> counter{0};
void relaxed_example() {
    std::vector<std::thread> threads;
    for (int i = 0; i < 10; ++i) {
        threads.emplace_back([]() {
            for (int j = 0; j < 10000; ++j) {
                counter.fetch_add(1, std::memory_order_relaxed);
            }
        });
    }
    for (auto& t : threads) t.join();
    std::cout << "Relaxed counter: " << counter << "\n";
}

// 2. release-acquire：消息传递
int payload = 0;
std::atomic<bool> ready{false};

void producer() {
    payload = 42;
    ready.store(true, std::memory_order_release);
}

void consumer() {
    while (!ready.load(std::memory_order_acquire));
    std::cout << "Received: " << payload << "\n";  // 保证 42
}

void release_acquire_example() {
    std::thread t1(producer);
    std::thread t2(consumer);
    t1.join();
    t2.join();
}

// 3. acq_rel：读-改-写
std::atomic<int> seq{0};
void acq_rel_example() {
    // fetch_add 是读-改-写，用 acq_rel
    int old = seq.fetch_add(1, std::memory_order_acq_rel);
    std::cout << "Got seq: " << old << "\n";
}

// 4. seq_cst：全局顺序
std::atomic<bool> x{false}, y{false};
std::atomic<int> z{0};

void write_x() { x.store(true, std::memory_order_seq_cst); }
void write_y() { y.store(true, std::memory_order_seq_cst); }
void read_x_then_y() {
    while (!x.load(std::memory_order_seq_cst));
    if (y.load(std::memory_order_seq_cst)) z++;
}
void read_y_then_x() {
    while (!y.load(std::memory_order_seq_cst));
    if (x.load(std::memory_order_seq_cst)) z++;
}

void seq_cst_example() {
    x = false; y = false; z = 0;
    std::thread t1(write_x);
    std::thread t2(write_y);
    std::thread t3(read_x_then_y);
    std::thread t4(read_y_then_x);
    t1.join(); t2.join(); t3.join(); t4.join();
    // seq_cst 保证 z 至少为 1
    // 如果用 relaxed，z 可能为 0
    std::cout << "z = " << z << " (>= 1 with seq_cst)\n";
}

// 5. 单生产者-单消费者的环形缓冲区
template<typename T, size_t Size>
class RingBuffer {
    T buffer[Size];
    std::atomic<size_t> head{0};  // 写位置
    std::atomic<size_t> tail{0};  // 读位置

public:
    bool push(const T& value) {
        size_t h = head.load(std::memory_order_relaxed);
        size_t next_h = (h + 1) % Size;
        if (next_h == tail.load(std::memory_order_acquire)) {
            return false;  // 满
        }
        buffer[h] = value;
        head.store(next_h, std::memory_order_release);
        return true;
    }

    bool pop(T& value) {
        size_t t = tail.load(std::memory_order_relaxed);
        if (t == head.load(std::memory_order_acquire)) {
            return false;  // 空
        }
        value = buffer[t];
        tail.store((t + 1) % Size, std::memory_order_release);
        return true;
    }
};

void ring_buffer_example() {
    RingBuffer<int, 16> rb;
    std::thread producer([&]() {
        for (int i = 0; i < 100; ++i) {
            while (!rb.push(i));
        }
    });
    std::thread consumer([&]() {
        for (int i = 0; i < 100; ++i) {
            int v;
            while (!rb.pop(v));
            std::cout << v << " ";
        }
    });
    producer.join();
    consumer.join();
    std::cout << "\n";
}

int main() {
    relaxed_example();
    release_acquire_example();
    acq_rel_example();
    seq_cst_example();
    ring_buffer_example();
    return 0;
}
```

#### 总结

- `relaxed`：仅原子性，无同步，适合独立计数器。
- `release`（写）/ `acquire`（读）：配对使用，建立 happens-before。
- `acq_rel`：用于读-改-写操作（fetch_add 等）。
- `seq_cst`：最强保证，全局统一顺序，默认值。
- `consume`：少用，多数实现等同 acquire。
- release-acquire 是最常用的配对模式，性能优于 seq_cst。
- 单生产者-单消费者缓冲区是 release-acquire 的经典应用。
- 不确定时用默认 seq_cst，正确性优先。

---

### 第27讲 原子指针与原子引用

#### 概念

除了基本类型的原子操作，C++ 还提供了原子指针（`std::atomic<T*>`）和原子引用（`std::atomic_ref<T>`，C++20）。它们允许对指针和外部变量进行原子操作，在无锁数据结构和 RCU（Read-Copy-Update）模式中非常有用。

#### 原理

**原子指针 `std::atomic<T*>`**：

- 支持指针的原子读写和算术运算。
- `fetch_add(n)` / `fetch_sub(n)`：原子移动指针。
- `++` / `--`：原子前进/后退。
- 适用于无锁数组遍历、链表头指针更新等。

**原子引用 `std::atomic_ref<T>`（C++20）**：

- 对**已存在的非原子变量**进行原子操作。
- 不需要将变量声明为 `atomic<T>`。
- 多个 `atomic_ref` 引用同一变量时，操作仍是原子的。
- 析构时不影响原变量。

**为什么需要 atomic_ref**：

有时无法修改变量类型（如第三方库的变量、结构体成员），但需要原子访问。`atomic_ref` 提供了"借用"外部变量进行原子操作的能力。

```cpp
int value = 0;  // 普通变量
std::atomic_ref<int> ref(value);  // 原子引用
ref.fetch_add(1);  // 原子加
// value 现在是 1
```

**atomic_ref 的要求**：
- 类型 T 必须是可平凡拷贝的（trivially copyable）。
- 所有对原变量的访问都必须通过 atomic_ref，否则数据竞争。
- 原子引用本身可拷贝，拷贝的是引用关系。

#### 例子

```cpp
#include <iostream>
#include <atomic>
#include <thread>
#include <vector>

// 1. 原子指针
void atomic_pointer_example() {
    int arr[] = {10, 20, 30, 40, 50};
    std::atomic<int*> ptr{arr};
    
    // 读取当前指针
    int* p = ptr.load();
    std::cout << "Current: " << *p << "\n";  // 10
    
    // 原子前进
    ptr.fetch_add(1);
    std::cout << "After +1: " << *ptr.load() << "\n";  // 20
    
    // 原子后退
    ptr.fetch_sub(1);
    std::cout << "After -1: " << *ptr.load() << "\n";  // 10
    
    // CAS 更新指针
    int* expected = arr;
    ptr.compare_exchange_strong(expected, arr + 2);
    std::cout << "After CAS: " << *ptr.load() << "\n";  // 30
}

// 2. 原子引用 (C++20)
void atomic_ref_example() {
    int value = 0;
    
    std::vector<std::thread> threads;
    for (int i = 0; i < 10; ++i) {
        threads.emplace_back([&value]() {
            std::atomic_ref<int> ref(value);
            for (int j = 0; j < 1000; ++j) {
                ref.fetch_add(1, std::memory_order_relaxed);
            }
        });
    }
    for (auto& t : threads) t.join();
    
    std::cout << "Value: " << value << " (expected 10000)\n";
}

// 3. 用 atomic_ref 操作结构体成员
struct Stats {
    int hits;
    int misses;
    double ratio;  // 非原子，但可单独原子访问 hits/misses
};

void stats_example() {
    Stats stats{0, 0, 0.0};
    
    auto worker = [&stats]() {
        for (int i = 0; i < 1000; ++i) {
            if (i % 10 == 0) {
                std::atomic_ref<int>(stats.hits).fetch_add(1);
            } else {
                std::atomic_ref<int>(stats.misses).fetch_add(1);
            }
        }
    };
    
    std::vector<std::thread> threads;
    for (int i = 0; i < 5; ++i) {
        threads.emplace_back(worker);
    }
    for (auto& t : threads) t.join();
    
    std::cout << "Hits: " << stats.hits << ", Misses: " << stats.misses << "\n";
}

// 4. 无锁链表头指针
template<typename T>
class LockFreeStack {
    struct Node {
        T data;
        Node* next;
        Node(const T& v) : data(v), next(nullptr) {}
    };
    
    std::atomic<Node*> head{nullptr};

public:
    void push(const T& value) {
        Node* new_node = new Node(value);
        new_node->next = head.load(std::memory_order_relaxed);
        while (!head.compare_exchange_weak(
            new_node->next, new_node,
            std::memory_order_release,
            std::memory_order_relaxed
        ));
    }

    bool pop(T& value) {
        Node* old_head = head.load(std::memory_order_acquire);
        while (old_head && 
               !head.compare_exchange_weak(
                   old_head, old_head->next,
                   std::memory_order_acquire,
                   std::memory_order_relaxed
               ));
        if (old_head) {
            value = old_head->data;
            delete old_head;
            return true;
        }
        return false;
    }
};

void stack_example() {
    LockFreeStack<int> stack;
    
    std::vector<std::thread> threads;
    for (int i = 0; i < 5; ++i) {
        threads.emplace_back([&stack, i]() {
            for (int j = 0; j < 100; ++j) {
                stack.push(i * 100 + j);
            }
        });
    }
    
    std::atomic<int> count{0};
    std::vector<std::thread> consumers;
    for (int i = 0; i < 3; ++i) {
        consumers.emplace_back([&stack, &count]() {
            int v;
            while (stack.pop(v)) {
                count++;
            }
        });
    }
    
    for (auto& t : threads) t.join();
    // 等待消费者处理完
    std::this_thread::sleep_for(std::chrono::milliseconds(100));
    std::cout << "Popped " << count << " items\n";
    
    for (auto& t : consumers) t.detach();  // 简化示例
}

int main() {
    atomic_pointer_example();
    atomic_ref_example();
    stats_example();
    stack_example();
    return 0;
}
```

#### 总结

- `std::atomic<T*>` 支持指针的原子读写和算术运算。
- `fetch_add` / `fetch_sub` 原子移动指针，适用于数组遍历。
- `std::atomic_ref<T>`（C++20）对已存在的非原子变量进行原子操作。
- atomic_ref 适用于无法修改变量类型的场景（第三方库、结构体成员）。
- 使用 atomic_ref 时，所有对原变量的访问都必须通过 atomic_ref。
- 无锁链表用 `atomic<Node*>` 管理头指针，CAS 更新。
- 无锁数据结构复杂，需仔细处理 ABA 问题（本教程未深入，建议参考专门资料）。

---

### 第28讲 无锁编程实践

#### 概念

**无锁编程（Lock-Free Programming）** 是不使用互斥锁实现线程安全数据结构的技术。它基于原子操作（特别是 CAS）实现并发访问。无锁编程可以避免死锁、优先级反转等问题，但设计和正确性验证非常困难。

#### 原理

**无锁编程的层次**：

1. **Wait-Free（无等待）**：每个操作在有限步骤内完成，不受其他线程影响。最强保证，最难实现。
2. **Lock-Free（无锁）**：系统整体总有进展，但单个线程可能被重试多次。常见级别。
3. **Obstruction-Free（无阻碍）**：单个线程在没有其他线程干扰时能完成操作。最弱保证。

**CAS 循环模式**：

```cpp
do {
    expected = current_value;
    desired = compute_new_value(expected);
} while (!var.compare_exchange_weak(expected, desired));
```

**ABA 问题**：

线程1读取值 A，线程2将值改为 B 再改回 A，线程1的 CAS 看到 A 认为"没变"，成功更新。但实际上中间发生了变化。

```
线程1: 读 A
线程2: A -> B -> A
线程1: CAS(A, C) 成功，但中间状态被忽略
```

解决方案：
- **版本号/标签**：值附带单调递增的版本号。
- **DCAS（双字 CAS）**：同时 CAS 值和版本号。
- **Hazard Pointer**：延迟回收，防止节点被复用。
- **Epoch-based reclamation**：周期性回收。

**何时用无锁**：
- 高竞争场景，锁开销大。
- 实时系统，不能容忍阻塞。
- 简单数据结构（计数器、队列、栈）。
- **避免**：复杂数据结构、对正确性要求极高但测试不足的场景。

#### 例子

```cpp
#include <iostream>
#include <atomic>
#include <thread>
#include <vector>
#include <memory>

// 1. 无锁计数器
class LockFreeCounter {
    std::atomic<int> value{0};
public:
    void increment() {
        value.fetch_add(1, std::memory_order_relaxed);
    }
    int get() const {
        return value.load(std::memory_order_relaxed);
    }
};

// 2. 无锁栈（Treiber Stack）
template<typename T>
class TreiberStack {
    struct Node {
        T data;
        Node* next;
        Node(const T& d) : data(d), next(nullptr) {}
    };
    
    std::atomic<Node*> head{nullptr};

public:
    void push(const T& value) {
        Node* new_node = new Node(value);
        new_node->next = head.load(std::memory_order_relaxed);
        // CAS 循环
        while (!head.compare_exchange_weak(
            new_node->next, new_node,
            std::memory_order_release,
            std::memory_order_relaxed
        )) {
            // new_node->next 已被更新为当前 head，继续重试
        }
    }

    bool pop(T& result) {
        Node* old_head = head.load(std::memory_order_acquire);
        while (old_head) {
            // 注意：这里存在 ABA 问题（简化示例未处理）
            if (head.compare_exchange_weak(
                old_head, old_head->next,
                std::memory_order_acquire,
                std::memory_order_relaxed
            )) {
                result = old_head->data;
                // 实际中需要延迟删除（hazard pointer 等）
                delete old_head;
                return true;
            }
        }
        return false;
    }
};

// 3. 无锁队列（Michael-Scott 简化版）
template<typename T>
class LockFreeQueue {
    struct Node {
        std::atomic<T*> data;
        std::atomic<Node*> next;
        Node() : data(nullptr), next(nullptr) {}
    };
    
    std::atomic<Node*> head;
    std::atomic<Node*> tail;

public:
    LockFreeQueue() {
        Node* dummy = new Node;
        head.store(dummy);
        tail.store(dummy);
    }

    void enqueue(T value) {
        Node* new_node = new Node;
        new_node->data.store(new T(value));
        
        while (true) {
            Node* t = tail.load(std::memory_order_acquire);
            Node* next = t->next.load(std::memory_order_acquire);
            
            if (t == tail.load(std::memory_order_acquire)) {
                if (next == nullptr) {
                    if (t->next.compare_exchange_weak(
                        next, new_node,
                        std::memory_order_release,
                        std::memory_order_relaxed
                    )) {
                        // 入队成功，尝试推进 tail
                        tail.compare_exchange_strong(
                            t, new_node,
                            std::memory_order_release,
                            std::memory_order_relaxed
                        );
                        return;
                    }
                } else {
                    // tail 落后了，帮助推进
                    tail.compare_exchange_strong(
                        t, next,
                        std::memory_order_release,
                        std::memory_order_relaxed
                    );
                }
            }
        }
    }

    bool dequeue(T& result) {
        while (true) {
            Node* h = head.load(std::memory_order_acquire);
            Node* t = tail.load(std::memory_order_acquire);
            Node* next = h->next.load(std::memory_order_acquire);
            
            if (h == head.load(std::memory_order_acquire)) {
                if (h == t) {
                    if (next == nullptr) {
                        return false;  // 空队列
                    }
                    // tail 落后，帮助推进
                    tail.compare_exchange_strong(
                        t, next,
                        std::memory_order_release,
                        std::memory_order_relaxed
                    );
                } else {
                    T* data = next->data.load(std::memory_order_acquire);
                    if (!head.compare_exchange_weak(
                        h, next,
                        std::memory_order_acquire,
                        std::memory_order_relaxed
                    )) {
                        continue;
                    }
                    result = *data;
                    delete data;
                    delete h;
                    return true;
                }
            }
        }
    }
};

// 测试
int main() {
    // 计数器测试
    {
        LockFreeCounter counter;
        std::vector<std::thread> threads;
        for (int i = 0; i < 10; ++i) {
            threads.emplace_back([&]() {
                for (int j = 0; j < 10000; ++j) {
                    counter.increment();
                }
            });
        }
        for (auto& t : threads) t.join();
        std::cout << "Counter: " << counter.get() << "\n";
    }

    // 栈测试
    {
        TreiberStack<int> stack;
        std::vector<std::thread> threads;
        for (int i = 0; i < 5; ++i) {
            threads.emplace_back([&stack, i]() {
                for (int j = 0; j < 100; ++j) {
                    stack.push(i * 100 + j);
                }
            });
        }
        for (auto& t : threads) t.join();
        
        int v;
        int count = 0;
        while (stack.pop(v)) {
            count++;
        }
        std::cout << "Stack popped: " << count << " items\n";
    }

    // 队列测试
    {
        LockFreeQueue<int> queue;
        std::vector<std::thread> producers;
        for (int i = 0; i < 3; ++i) {
            producers.emplace_back([&queue, i]() {
                for (int j = 0; j < 100; ++j) {
                    queue.enqueue(i * 100 + j);
                }
            });
        }
        
        std::atomic<int> count{0};
        std::vector<std::thread> consumers;
        for (int i = 0; i < 3; ++i) {
            consumers.emplace_back([&queue, &count]() {
                int v;
                while (count.load() < 300) {
                    if (queue.dequeue(v)) {
                        count++;
                    }
                }
            });
        }
        
        for (auto& t : producers) t.join();
        for (auto& t : consumers) t.join();
        std::cout << "Queue processed: " << count << " items\n";
    }

    return 0;
}
```

#### 总结

- 无锁编程基于原子操作（特别是 CAS），避免互斥锁。
- Lock-Free 保证系统整体进展，Wait-Free 保证每个操作有限步完成。
- CAS 循环是无锁算法的基本模式。
- ABA 问题是无锁编程的经典陷阱，需用版本号或延迟回收解决。
- Treiber Stack 是最简单的无锁数据结构。
- Michael-Scott Queue 是经典的无锁队列算法。
- 无锁代码复杂且难以验证，非必要不使用。
- 优先用 mutex，性能瓶颈时再考虑无锁优化。
- 使用 ThreadSanitizer 等工具验证无锁代码的正确性。

---


## 第七章 C++20 并发新特性

### 第29讲 协程基础

#### 概念

**协程（Coroutine）** 是一种可以被挂起和恢复的函数。C++20 引入了协程支持，允许编写异步代码时使用同步的线性风格。协程不是线程，它在一个线程内执行，可以在特定点挂起，让出执行权，稍后恢复。协程是 C++ 异步编程的重大革新。

#### 原理

**协程 vs 线程**：

| 特性 | 线程 | 协程 |
|------|------|------|
| 调度 | 操作系统抢占式 | 用户协作式 |
| 切换成本 | 高（内核态切换） | 低（用户态保存寄存器） |
| 栈大小 | MB 级别 | 可无栈或小栈 |
| 并发 | 真并行（多核） | 单线程内并发 |
| 数据共享 | 需同步 | 通常无共享 |

**C++20 协程的三关键字**：
- `co_await`：挂起协程，等待异步操作完成。
- `co_yield`：产出一个值，挂起协程。
- `co_return`：结束协程，返回最终值。

**协程的机制**：

1. 调用协程函数时，编译器生成一个**协程帧（coroutine frame）**，保存状态。
2. 协程帧包含：局部变量、挂起点、promise 对象。
3. `co_await` / `co_yield` 挂起时，状态保存到帧，控制权返回调用者。
4. 恢复时，从帧中恢复状态，继续执行。

**Promise 接口**：协程通过 promise 对象与调用者交互：
- `get_return_object()`：创建返回给调用者的对象。
- `initial_suspend()`：协程启动时是否挂起。
- `final_suspend()`：协程结束时是否挂起。
- `return_value()` / `return_void()`：处理 co_return。
- `yield_value()`：处理 co_yield。
- `unhandled_exception()`：处理异常。

**Awaitable 接口**：`co_await` 的操作数需要实现：
- `await_ready()`：是否已完成（不需挂起）。
- `await_suspend(handle)`：挂起逻辑（可恢复、可调度到其他线程）。
- `await_resume()`：恢复时返回的值。

#### 例子

```cpp
#include <iostream>
#include <coroutine>
#include <thread>
#include <chrono>

// 1. 最简单的协程：生成器
struct Generator {
    struct promise_type {
        int current_value;
        
        Generator get_return_object() {
            return Generator{std::coroutine_handle<promise_type>::from_promise(*this)};
        }
        std::suspend_always initial_suspend() { return {}; }
        std::suspend_always final_suspend() noexcept { return {}; }
        std::suspend_always yield_value(int val) {
            current_value = val;
            return {};
        }
        void return_void() {}
        void unhandled_exception() { std::terminate(); }
    };
    
    std::coroutine_handle<promise_type> handle;
    
    Generator(std::coroutine_handle<promise_type> h) : handle(h) {}
    ~Generator() { if (handle) handle.destroy(); }
    
    bool next() {
        if (!handle || handle.done()) return false;
        handle.resume();
        return !handle.done();
    }
    
    int value() const { return handle.promise().current_value; }
};

Generator range(int start, int end) {
    for (int i = start; i < end; ++i) {
        co_yield i;
    }
}

void generator_example() {
    auto gen = range(1, 5);
    while (gen.next()) {
        std::cout << gen.value() << " ";
    }
    std::cout << "\n";
}

// 2. 简单的 Task 类型
struct Task {
    struct promise_type {
        Task get_return_object() {
            return Task{std::coroutine_handle<promise_type>::from_promise(*this)};
        }
        std::suspend_never initial_suspend() { return {}; }
        std::suspend_never final_suspend() noexcept { return {}; }
        void return_void() {}
        void unhandled_exception() { std::terminate(); }
    };
    
    std::coroutine_handle<promise_type> handle;
    
    Task(std::coroutine_handle<promise_type> h) : handle(h) {}
    ~Task() { 
        // suspend_never 时协程已自动销毁
    }
};

// 3. 自定义 awaitable：等待一段时间
struct WaitDuration {
    std::chrono::milliseconds duration;
    
    bool await_ready() const noexcept { return false; }
    void await_suspend(std::coroutine_handle<> h) const {
        std::thread([h, d = duration]() {
            std::this_thread::sleep_for(d);
            h.resume();
        }).detach();
    }
    void await_resume() const noexcept {}
};

Task async_print(const std::string& msg, int delay_ms) {
    std::cout << "Start: " << msg << "\n";
    co_await WaitDuration{std::chrono::milliseconds(delay_ms)};
    std::cout << "End: " << msg << "\n";
}

void await_example() {
    async_print("Task A", 100);
    async_print("Task B", 200);
    async_print("Task C", 50);
    // 主线程等待协程完成
    std::this_thread::sleep_for(std::chrono::milliseconds(300));
}

// 4. 斐波那契生成器
Generator fibonacci() {
    int a = 0, b = 1;
    while (true) {
        co_yield a;
        auto next = a + b;
        a = b;
        b = next;
    }
}

void fib_example() {
    auto gen = fibonacci();
    for (int i = 0; i < 10; ++i) {
        gen.next();
        std::cout << gen.value() << " ";
    }
    std::cout << "\n";
}

int main() {
    std::cout << "=== Generator ===\n";
    generator_example();
    
    std::cout << "\n=== Await ===\n";
    await_example();
    
    std::cout << "\n=== Fibonacci ===\n";
    fib_example();
    
    return 0;
}
```

#### 总结

- 协程是可挂起/恢复的函数，C++20 引入。
- 三关键字：`co_await`（等待）、`co_yield`（产出）、`co_return`（结束）。
- 协程不是线程，在单线程内协作式调度。
- 协程帧保存状态，挂起和恢复由编译器管理。
- Promise 接口定义协程与调用者的交互。
- Awaitable 接口定义 `co_await` 的行为。
- 生成器（Generator）是协程的经典应用。
- C++20 只提供协程机制，不提供现成的 Task 类型，需自行实现或用库。
- 协程适合异步 IO、状态机、生成器等场景。

---

### 第30讲 jthread 与协作式取消

#### 概念

`std::jthread`（joining thread）是 C++20 引入的线程类，在 `std::thread` 基础上增加了两个关键特性：**自动 join**（RAII）和**协作式取消**（通过 `std::stop_token`）。它解决了 `std::thread` 忘记 join 导致 terminate 的问题，并提供了标准的线程取消机制。

#### 原理

**jthread 的改进**：

1. **自动 join**：析构时自动调用 `join()`，无需手动管理。
2. **取消支持**：内置 `std::stop_source`，可通过 `request_stop()` 请求停止。
3. **stop_token 传递**：线程函数可接受 `std::stop_token` 参数，检查是否被请求停止。

**协作式取消机制**：

```
主线程:                          工作线程:
jthread t(worker);               void worker(stop_token st) {
                                   while (!st.stop_requested()) {
t.request_stop();    ───────────>     // 检查停止请求
                                   }
t.join(); (自动)                 }
```

- `request_stop()` 设置停止标志。
- `stop_token::stop_requested()` 检查标志。
- 线程函数自己决定何时、如何停止（协作式）。
- `stop_callback` 可注册停止时的回调。

**stop_source / stop_token / stop_callback**：
- `stop_source`：拥有停止状态，可请求停止。
- `stop_token`：查询停止状态，不可请求停止。
- `stop_callback`：注册停止时的回调函数。

#### 例子

```cpp
#include <iostream>
#include <thread>
#include <stop_token>
#include <chrono>
#include <vector>

// 1. 基本 jthread
void basic_jthread() {
    std::jthread t([]() {
        std::cout << "jthread running\n";
        std::this_thread::sleep_for(std::chrono::milliseconds(100));
        std::cout << "jthread done\n";
    });
    // 析构时自动 join，无需手动调用
}

// 2. 接受 stop_token 的工作函数
void worker(std::stop_token st, int id) {
    std::cout << "Worker " << id << " started\n";
    while (!st.stop_requested()) {
        std::cout << "Worker " << id << " working...\n";
        std::this_thread::sleep_for(std::chrono::milliseconds(50));
    }
    std::cout << "Worker " << id << " stopped\n";
}

void cancellation_example() {
    std::jthread t(worker, 1);
    
    std::this_thread::sleep_for(std::chrono::milliseconds(200));
    std::cout << "Requesting stop...\n";
    t.request_stop();
    // 析构时自动 join
}

// 3. stop_callback：注册停止回调
void callback_example() {
    std::jthread t([](std::stop_token st) {
        // 注册回调：停止时执行
        std::stop_callback cb(st, []() {
            std::cout << "Cleanup on stop!\n";
        });
        
        while (!st.stop_requested()) {
            std::this_thread::sleep_for(std::chrono::milliseconds(50));
        }
        std::cout << "Worker exiting\n";
    });
    
    std::this_thread::sleep_for(std::chrono::milliseconds(200));
    t.request_stop();
}

// 4. 独立使用 stop_source
void standalone_stop() {
    std::stop_source source;
    std::stop_token token = source.get_token();
    
    // 注册回调
    std::stop_callback cb(token, []() {
        std::cout << "Stop requested!\n";
    });
    
    std::jthread t([token]() {
        while (!token.stop_requested()) {
            std::this_thread::sleep_for(std::chrono::milliseconds(50));
        }
        std::cout << "Thread done\n";
    });
    
    std::this_thread::sleep_for(std::chrono::milliseconds(200));
    source.request_stop();
}

// 5. 多线程取消
void multi_thread_cancel() {
    std::stop_source source;
    
    auto worker = [token = source.get_token()](int id) {
        while (!token.stop_requested()) {
            std::cout << "Worker " << id << " working\n";
            std::this_thread::sleep_for(std::chrono::milliseconds(50));
        }
        std::cout << "Worker " << id << " stopped\n";
    };
    
    std::jthread t1(worker, 1);
    std::jthread t2(worker, 2);
    std::jthread t3(worker, 3);
    
    std::this_thread::sleep_for(std::chrono::milliseconds(200));
    source.request_stop();  // 同时停止所有线程
}

// 6. jthread 的所有权转移
void ownership_example() {
    std::jthread t1([]() {
        std::this_thread::sleep_for(std::chrono::milliseconds(100));
    });
    
    // 移动构造
    std::jthread t2 = std::move(t1);
    // t1 现在为空
    
    // 存入容器
    std::vector<std::jthread> threads;
    for (int i = 0; i < 3; ++i) {
        threads.emplace_back([](int id) {
            std::this_thread::sleep_for(std::chrono::milliseconds(50));
        }, i);
    }
    // vector 析构时所有 jthread 自动 join
}

int main() {
    std::cout << "=== Basic jthread ===\n";
    basic_jthread();
    
    std::cout << "\n=== Cancellation ===\n";
    cancellation_example();
    
    std::cout << "\n=== Stop Callback ===\n";
    callback_example();
    
    std::cout << "\n=== Standalone Stop ===\n";
    standalone_stop();
    
    std::cout << "\n=== Multi-thread Cancel ===\n";
    multi_thread_cancel();
    
    std::cout << "\n=== Ownership ===\n";
    ownership_example();
    
    return 0;
}
```

#### 总结

- `std::jthread`（C++20）自动 join，避免忘记 join 导致 terminate。
- 内置 `stop_source`，支持协作式取消。
- 线程函数接受 `std::stop_token` 参数，检查 `stop_requested()`。
- `request_stop()` 请求停止，线程自行决定如何响应。
- `std::stop_callback` 注册停止时的回调。
- `stop_source` 可独立使用，控制多个线程的取消。
- jthread 可移动，可存入容器，析构时自动 join。
- 优先使用 jthread 替代 thread，除非需要更细粒度的控制。

---

### 第31讲 信号量、latch 与 barrier

#### 概念

C++20 引入了三个新的同步原语：
- **`std::counting_semaphore`**：计数信号量，控制对 N 个资源的访问。
- **`std::binary_semaphore`**：二元信号量（计数为 1），类似 mutex 但可跨线程释放。
- **`std::latch`**：一次性同步点，线程到达后等待，计数归零后全部放行。
- **`std::barrier`**：可复用的同步点，线程到达后等待，全部到齐后放行并重置。

#### 原理

**信号量（Semaphore）**：

```
初始计数 = N

acquire():           release():
  while (count == 0)    count++
    等待
  count--
```

- 计数 > 0 时，acquire 立即成功。
- 计数 = 0 时，acquire 阻塞。
- release 增加计数，唤醒一个等待者。
- 与 mutex 的区别：mutex 的持有者必须释放，信号量任何线程都可 release。

**Latch（闩锁）**：

```
初始计数 = N

count_down():  计数--
wait():        等待计数归零
count_down_and_wait():  计数-- 并等待
```

- 一次性使用，计数归零后不再重置。
- 用于"等待 N 个线程完成初始化"。
- 归零后 wait() 立即返回，所有后续 wait 也立即返回。

**Barrier（屏障）**：

```
初始计数 = N，预期线程数 = N

arrive_and_wait():
  计数--
  if (计数 == 0):
    调用 completion 函数
    重置计数 = N
    唤醒所有等待者
  else:
    等待
```

- 可复用，每次归零后自动重置。
- 用于多阶段并行计算（如循环中的分步处理）。
- 可指定 completion 函数，在阶段切换时执行。

#### 例子

```cpp
#include <iostream>
#include <thread>
#include <semaphore>
#include <latch>
#include <barrier>
#include <vector>
#include <mutex>

std::mutex cout_mtx;
void log(const std::string& msg) {
    std::lock_guard<std::mutex> lock(cout_mtx);
    std::cout << "[" << std::this_thread::get_id() << "] " << msg << "\n";
}

// 1. 信号量：限制并发数
std::counting_semaphore<3> sem(3);  // 最多3个并发

void limited_resource(int id) {
    sem.acquire();
    log("Start " + std::to_string(id));
    std::this_thread::sleep_for(std::chrono::milliseconds(100));
    log("End " + std::to_string(id));
    sem.release();
}

void semaphore_example() {
    std::vector<std::jthread> threads;
    for (int i = 0; i < 10; ++i) {
        threads.emplace_back(limited_resource, i);
    }
    // 任意时刻最多3个线程在执行
}

// 2. binary_semaphore：生产者-消费者
std::binary_semaphore prod_sem(0);
std::binary_semaphore cons_sem(1);
int shared_data = 0;

void producer_b() {
    for (int i = 1; i <= 5; ++i) {
        cons_sem.acquire();  // 等待消费者就绪
        shared_data = i;
        log("Produced " + std::to_string(i));
        prod_sem.release();  // 通知消费者
    }
}

void consumer_b() {
    for (int i = 0; i < 5; ++i) {
        prod_sem.acquire();  // 等待生产者
        log("Consumed " + std::to_string(shared_data));
        cons_sem.release();  // 通知生产者
    }
}

void binary_sem_example() {
    std::jthread p(producer_b);
    std::jthread c(consumer_b);
}

// 3. Latch：等待初始化完成
std::latch init_latch(5);  // 等待5个线程初始化

void worker_with_init(int id) {
    log("Worker " + std::to_string(id) + " initializing...");
    std::this_thread::sleep_for(std::chrono::milliseconds(50 * id));
    log("Worker " + std::to_string(id) + " initialized");
    init_latch.count_down_and_wait();  // 初始化完成，等待其他
    log("Worker " + std::to_string(id) + " starting work");
}

void latch_example() {
    std::vector<std::jthread> threads;
    for (int i = 0; i < 5; ++i) {
        threads.emplace_back(worker_with_init, i);
    }
    // 所有线程初始化完成后才开始工作
}

// 4. Barrier：多阶段并行
void barrier_example() {
    const int N = 4;
    std::barrier sync_point(N, []() noexcept {
        log("--- Phase complete ---");
    });
    
    auto worker = [&sync_point](int id) {
        for (int phase = 0; phase < 3; ++phase) {
            log("Worker " + std::to_string(id) + " phase " + std::to_string(phase) + " start");
            std::this_thread::sleep_for(std::chrono::milliseconds(50 * (id + 1)));
            log("Worker " + std::to_string(id) + " phase " + std::to_string(phase) + " done");
            sync_point.arrive_and_wait();  // 等待所有线程完成本阶段
        }
    };
    
    std::vector<std::jthread> threads;
    for (int i = 0; i < N; ++i) {
        threads.emplace_back(worker, i);
    }
}

// 5. 信号量实现连接池
class ConnectionPool {
    std::counting_semaphore<5> sem{5};
    std::vector<int> connections{1, 2, 3, 4, 5};
    std::mutex pool_mtx;
    
public:
    int acquire() {
        sem.acquire();
        std::lock_guard<std::mutex> lock(pool_mtx);
        int conn = connections.back();
        connections.pop_back();
        return conn;
    }
    
    void release(int conn) {
        std::lock_guard<std::mutex> lock(pool_mtx);
        connections.push_back(conn);
        sem.release();
    }
};

void pool_example() {
    ConnectionPool pool;
    std::vector<std::jthread> threads;
    for (int i = 0; i < 10; ++i) {
        threads.emplace_back([&pool, i]() {
            int conn = pool.acquire();
            log("Thread " + std::to_string(i) + " got connection " + std::to_string(conn));
            std::this_thread::sleep_for(std::chrono::milliseconds(100));
            pool.release(conn);
        });
    }
}

int main() {
    std::cout << "=== Semaphore ===\n";
    semaphore_example();
    
    std::cout << "\n=== Binary Semaphore ===\n";
    binary_sem_example();
    
    std::cout << "\n=== Latch ===\n";
    latch_example();
    
    std::cout << "\n=== Barrier ===\n";
    barrier_example();
    
    std::cout << "\n=== Connection Pool ===\n";
    pool_example();
    
    return 0;
}
```

#### 总结

- `counting_semaphore<N>` 控制对 N 个资源的访问，acquire/release。
- `binary_semaphore` 是计数为 1 的信号量，类似 mutex 但可跨线程释放。
- 信号量与 mutex 的区别：mutex 有持有者概念，信号量没有。
- `std::latch` 是一次性同步点，用于等待 N 个事件完成。
- `std::barrier` 是可复用同步点，用于多阶段并行计算。
- barrier 可指定 completion 函数，在阶段切换时执行。
- 信号量适合资源池、限流；latch 适合一次性等待；barrier 适合循环并行。
- 这些原语简化了常见同步模式，比手写条件变量更清晰。

---

### 第32讲 atomic_ref 与同步流

#### 概念

C++20 引入了 `std::atomic_ref<T>`（原子引用）和 `std::atomic_flag::test()` 方法，并完善了原子操作接口。`atomic_ref` 允许对已存在的非原子变量进行原子操作，无需修改变量类型。本讲还介绍 `std::osyncstream`（同步流），解决多线程 `std::cout` 输出交错问题。

#### 原理

**atomic_ref 的设计动机**：

有时我们无法将变量声明为 `std::atomic<T>`：
- 第三方库的变量
- 序列化/反序列化的数据结构
- 需要原子访问的旧代码

`std::atomic_ref<T>` 提供"借用"外部变量进行原子操作的能力：
- 构造时绑定到现有变量。
- 操作语义与 `std::atomic<T>` 相同。
- 多个 atomic_ref 可引用同一变量。
- 析构时不影响原变量。

**使用约束**：
- 类型 T 必须是可平凡拷贝的。
- 所有对原变量的访问都必须通过 atomic_ref，否则数据竞争。
- 原子引用的生存期必须小于被引用变量。

**osyncstream（同步流）**：

多线程同时写 `std::cout` 会导致输出交错。`std::osyncstream`（C++20）提供同步输出：
- 内部缓冲，析构时原子地写入底层流。
- 每条消息完整输出，不交错。

```cpp
{
    std::osyncstream sync_out(std::cout);
    sync_out << "Thread " << id << " message\n";
}  // 析构时原子写入
```

#### 例子

```cpp
#include <iostream>
#include <atomic>
#include <thread>
#include <vector>
#include <syncstream>
#include <mutex>

// 1. atomic_ref 基础
void atomic_ref_basic() {
    int value = 0;  // 普通变量
    
    // 创建原子引用
    std::atomic_ref<int> ref(value);
    
    ref.store(42);
    std::cout << "value = " << value << "\n";  // 42
    
    ref.fetch_add(10);
    std::cout << "value = " << value << "\n";  // 52
    
    // 多个引用同一变量
    std::atomic_ref<int> ref2(value);
    ref2.store(100);
    std::cout << "ref = " << ref.load() << "\n";  // 100
}

// 2. 多线程原子引用
void multi_thread_ref() {
    int counter = 0;
    
    std::vector<std::jthread> threads;
    for (int i = 0; i < 10; ++i) {
        threads.emplace_back([&counter]() {
            std::atomic_ref<int> ref(counter);
            for (int j = 0; j < 1000; ++j) {
                ref.fetch_add(1, std::memory_order_relaxed);
            }
        });
    }
    
    for (auto& t : threads) t.join();
    std::cout << "Counter: " << counter << " (expected 10000)\n";
}

// 3. 结构体成员的原子访问
struct Particle {
    double x, y, z;
    int energy;
};

void struct_example() {
    Particle p{0, 0, 0, 0};
    
    std::vector<std::jthread> threads;
    for (int i = 0; i < 5; ++i) {
        threads.emplace_back([&p]() {
            for (int j = 0; j < 1000; ++j) {
                // 只原子更新 energy，不影响 x/y/z
                std::atomic_ref<int>(p.energy).fetch_add(1);
            }
        });
    }
    
    for (auto& t : threads) t.join();
    std::cout << "Energy: " << p.energy << "\n";
}

// 4. osyncstream 解决输出交错
void syncstream_example() {
    auto worker = [](int id) {
        for (int i = 0; i < 3; ++i) {
            // 不用 osyncstream：输出会交错
            // std::cout << "Thread " << id << " iteration " << i << "\n";
            
            // 用 osyncstream：每条消息完整输出
            std::osyncstream(std::cout) 
                << "Thread " << id << " iteration " << i << "\n";
        }
    };
    
    std::vector<std::jthread> threads;
    for (int i = 0; i < 5; ++i) {
        threads.emplace_back(worker, i);
    }
}

// 5. 封装同步日志
class SyncLogger {
    std::osyncstream stream;
public:
    SyncLogger() : stream(std::cout) {}
    
    template<typename... Args>
    void log(Args&&... args) {
        std::osyncstream sync_out(std::cout);
        (sync_out << ... << std::forward<Args>(args)) << '\n';
    }
};

void logger_example() {
    SyncLogger logger;
    std::vector<std::jthread> threads;
    for (int i = 0; i < 5; ++i) {
        threads.emplace_back([&logger, i]() {
            logger.log("Thread ", i, " started");
            std::this_thread::sleep_for(std::chrono::milliseconds(10));
            logger.log("Thread ", i, " finished");
        });
    }
}

// 6. atomic_flag 的 test 方法 (C++20)
std::atomic_flag flag = ATOMIC_FLAG_INIT;

void flag_test_example() {
    std::cout << "Flag set: " << flag.test() << "\n";  // 0
    
    flag.test_and_set();
    std::cout << "Flag set: " << flag.test() << "\n";  // 1
    
    // 等待 flag 被清除（阻塞）
    std::jthread t([]() {
        std::this_thread::sleep_for(std::chrono::milliseconds(100));
        flag.clear();
        std::cout << "Flag cleared\n";
    });
    
    std::cout << "Waiting for flag to clear...\n";
    flag.wait(true);  // 阻塞直到 flag != true
    std::cout << "Flag is now clear\n";
}

// 7. atomic::wait/notify (C++20)
std::atomic<int> value{0};

void wait_notify_example() {
    std::jthread t([]() {
        std::this_thread::sleep_for(std::chrono::milliseconds(100));
        value.store(42);
        value.notify_one();  // 通知等待者
    });
    
    std::cout << "Waiting for value...\n";
    value.wait(0);  // 阻塞直到 value != 0
    std::cout << "Value = " << value << "\n";
}

int main() {
    std::cout << "=== atomic_ref basic ===\n";
    atomic_ref_basic();
    
    std::cout << "\n=== Multi-thread ref ===\n";
    multi_thread_ref();
    
    std::cout << "\n=== Struct member ===\n";
    struct_example();
    
    std::cout << "\n=== osyncstream ===\n";
    syncstream_example();
    
    std::cout << "\n=== Logger ===\n";
    logger_example();
    
    std::cout << "\n=== atomic_flag test ===\n";
    flag_test_example();
    
    std::cout << "\n=== wait/notify ===\n";
    wait_notify_example();
    
    return 0;
}
```

#### 总结

- `std::atomic_ref<T>`（C++20）对已存在的非原子变量进行原子操作。
- 适用于无法修改变量类型的场景（第三方库、结构体成员）。
- 使用 atomic_ref 时，所有对原变量的访问都必须通过 atomic_ref。
- `std::osyncstream`（C++20）解决多线程 cout 输出交错问题。
- osyncstream 内部缓冲，析构时原子写入，保证消息完整。
- C++20 为 `atomic_flag` 和 `atomic` 添加了 `wait`/`notify` 方法。
- `wait(old)` 阻塞直到值不等于 old，`notify_one/all` 唤醒等待者。
- wait/notify 比条件变量更轻量，适合简单的等待-通知场景。

---


## 第八章 并发设计与实战

### 第33讲 线程池设计

#### 概念

**线程池（Thread Pool）** 是一种并发设计模式，预先创建一组工作线程，通过任务队列接收并执行任务。线程池避免了频繁创建/销毁线程的开销，控制并发线程数量，是生产环境中最常用的并发基础设施。本讲从零实现一个功能完整的线程池。

#### 原理

**线程池的核心组件**：

```
┌─────────────────────────────────────────┐
│              ThreadPool                 │
│                                         │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐ │
│  │ Worker  │  │ Worker  │  │ Worker  │ │
│  │ Thread  │  │ Thread  │  │ Thread  │ │
│  └────┬────┘  └────┬────┘  └────┬────┘ │
│       │            │            │       │
│       └────────────┼────────────┘       │
│                    │                     │
│              ┌─────┴─────┐               │
│              │ Task Queue │              │
│              │ (线程安全)  │              │
│              └─────┬─────┘               │
│                    │                     │
│              ┌─────┴─────┐               │
│              │  submit()  │              │
│              └───────────┘               │
└─────────────────────────────────────────┘
```

**工作流程**：
1. 初始化时创建 N 个工作线程，每个线程循环从任务队列取任务。
2. `submit()` 将任务（可调用对象）包装后放入队列。
3. 工作线程从队列取出任务执行，执行完继续取下一个。
4. 队列为空时，工作线程阻塞等待。
5. 析构时设置停止标志，唤醒所有线程，等待它们完成当前任务后退出。

**关键设计点**：
- **任务队列**：线程安全的 FIFO 队列，用 mutex + condition_variable 保护。
- **任务包装**：用 `std::function<void()>` 统一不同类型的任务。
- **返回值处理**：用 `std::future` 让调用者获取任务结果。
- **优雅关闭**：停止标志 + 唤醒所有线程 + join。

#### 例子

```cpp
#include <iostream>
#include <thread>
#include <vector>
#include <queue>
#include <functional>
#include <future>
#include <mutex>
#include <condition_variable>
#include <atomic>
#include <memory>
#include <chrono>

class ThreadPool {
public:
    explicit ThreadPool(size_t num_threads = 
                        std::thread::hardware_concurrency()) 
        : stop(false) 
    {
        for (size_t i = 0; i < num_threads; ++i) {
            workers.emplace_back([this]() {
                for (;;) {
                    std::function<void()> task;
                    {
                        std::unique_lock<std::mutex> lock(queue_mutex);
                        condition.wait(lock, [this]() {
                            return stop || !tasks.empty();
                        });
                        
                        if (stop && tasks.empty()) return;
                        
                        task = std::move(tasks.front());
                        tasks.pop();
                    }
                    task();  // 执行任务
                }
            });
        }
    }
    
    ~ThreadPool() {
        {
            std::unique_lock<std::mutex> lock(queue_mutex);
            stop = true;
        }
        condition.notify_all();
        for (auto& worker : workers) {
            if (worker.joinable()) worker.join();
        }
    }
    
    // 提交任务，返回 future
    template<typename F, typename... Args>
    auto submit(F&& f, Args&&... args) 
        -> std::future<typename std::invoke_result<F, Args...>::type> 
    {
        using return_type = typename std::invoke_result<F, Args...>::type;
        
        auto task = std::make_shared<std::packaged_task<return_type()>>(
            std::bind(std::forward<F>(f), std::forward<Args>(args)...)
        );
        
        std::future<return_type> result = task->get_future();
        {
            std::unique_lock<std::mutex> lock(queue_mutex);
            if (stop) {
                throw std::runtime_error("ThreadPool is stopped");
            }
            tasks.emplace([task]() { (*task)(); });
        }
        condition.notify_one();
        return result;
    }
    
    // 获取工作线程数
    size_t worker_count() const { return workers.size(); }
    
    // 获取待处理任务数
    size_t pending_tasks() const {
        std::unique_lock<std::mutex> lock(queue_mutex);
        return tasks.size();
    }
    
private:
    std::vector<std::thread> workers;
    std::queue<std::function<void()>> tasks;
    
    mutable std::mutex queue_mutex;
    std::condition_variable condition;
    std::atomic<bool> stop;
};

// 使用示例
int main() {
    ThreadPool pool(4);
    std::cout << "Pool with " << pool.worker_count() << " workers\n";
    
    // 1. 提交简单任务
    auto f1 = pool.submit([]() {
        std::this_thread::sleep_for(std::chrono::milliseconds(100));
        return 42;
    });
    std::cout << "Result 1: " << f1.get() << "\n";
    
    // 2. 提交带参数的任务
    auto f2 = pool.submit([](int a, int b) {
        return a + b;
    }, 10, 20);
    std::cout << "Result 2: " << f2.get() << "\n";
    
    // 3. 批量提交任务
    std::vector<std::future<int>> results;
    for (int i = 0; i < 10; ++i) {
        results.push_back(pool.submit([](int id) {
            std::this_thread::sleep_for(std::chrono::milliseconds(50));
            std::cout << "Task " << id << " done\n";
            return id * id;
        }, i));
    }
    
    int sum = 0;
    for (auto& f : results) {
        sum += f.get();
    }
    std::cout << "Sum of squares: " << sum << "\n";
    
    // 4. 异常处理
    auto f3 = pool.submit([]() -> int {
        throw std::runtime_error("Task failed");
    });
    try {
        f3.get();
    } catch (const std::exception& e) {
        std::cout << "Caught: " << e.what() << "\n";
    }
    
    return 0;
}
```

**进阶：支持优先级的线程池**：

```cpp
class PriorityThreadPool {
    struct Task {
        int priority;
        std::function<void()> func;
        bool operator<(const Task& other) const {
            return priority < other.priority;  // 优先级高的先出
        }
    };
    
    std::priority_queue<Task> tasks;
    // ... 其余类似 ThreadPool
};
```

#### 总结

- 线程池避免频繁创建/销毁线程的开销，控制并发数。
- 核心组件：工作线程、任务队列、同步机制。
- 任务用 `std::function<void()>` 统一包装。
- 返回值通过 `std::packaged_task` + `std::future` 传递。
- 优雅关闭：停止标志 + notify_all + join。
- 生产环境考虑：动态调整线程数、任务优先级、任务超时、负载均衡。
- C++ 标准库暂无线程池，需自行实现或使用第三方库（如 Boost.Asio）。
- 线程池大小经验值：CPU 密集型 = 核心数；IO 密集型 = 2×核心数。

---

### 第34讲 并发数据结构

#### 概念

**并发数据结构** 是支持多线程安全访问的数据结构。本讲讲解如何用互斥锁和原子操作实现并发栈、并发队列和并发哈希表。设计并发数据结构需要在**安全性**和**性能**之间权衡：锁粒度越细，并发度越高，但实现越复杂。

#### 原理

**并发数据结构的设计层次**：

1. **粗粒度锁（Coarse-grained）**：整个结构一把锁，简单但并发度低。
2. **细粒度锁（Fine-grained）**：不同部分用不同锁，并发度高但易死锁。
3. **无锁（Lock-free）**：用原子操作，最高并发但实现极复杂。

**设计原则**：
- **保证不变式**：操作前后数据结构始终处于有效状态。
- **避免死锁**：固定锁顺序、避免嵌套锁。
- **异常安全**：操作失败时数据结构不损坏。
- **最小化临界区**：锁只保护必要的操作。

**并发栈的实现策略**：
- 粗粒度：一把 mutex 保护整个栈。
- 无锁：基于单向链表 + CAS（见第28讲 Treiber Stack）。

**并发队列的实现策略**：
- 粗粒度：一把 mutex 保护整个队列。
- 两锁：一把保护头部，一把保护尾部，可同时入队和出队。
- 无锁：Michael-Scott 队列（见第28讲）。

**并发哈希表**：
- 分段锁（Striped Locking）：每个桶一把锁，不同桶可并发访问。
- 读写锁：读多写少时用 `shared_mutex`。

#### 例子

```cpp
#include <iostream>
#include <thread>
#include <vector>
#include <stack>
#include <queue>
#include <mutex>
#include <atomic>
#include <memory>
#include <shared_mutex>
#include <list>
#include <functional>

// 1. 并发栈（粗粒度锁）
template<typename T>
class ConcurrentStack {
    std::stack<T> stack;
    mutable std::mutex mtx;
    
public:
    void push(T value) {
        std::lock_guard<std::mutex> lock(mtx);
        stack.push(std::move(value));
    }
    
    bool pop(T& value) {
        std::lock_guard<std::mutex> lock(mtx);
        if (stack.empty()) return false;
        value = std::move(stack.top());
        stack.pop();
        return true;
    }
    
    bool empty() const {
        std::lock_guard<std::mutex> lock(mtx);
        return stack.empty();
    }
    
    size_t size() const {
        std::lock_guard<std::mutex> lock(mtx);
        return stack.size();
    }
};

// 2. 并发队列（两锁：head 和 tail 分离）
template<typename T>
class ConcurrentQueue {
    struct Node {
        T data;
        std::unique_ptr<Node> next;
        Node(T d) : data(std::move(d)) {}
    };
    
    std::unique_ptr<Node> head;
    Node* tail;
    std::mutex head_mtx;
    std::mutex tail_mtx;
    
    Node* get_tail() {
        std::lock_guard<std::mutex> lock(tail_mtx);
        return tail;
    }
    
public:
    ConcurrentQueue() : head(std::make_unique<Node>(T{})), tail(head.get()) {}
    
    void enqueue(T value) {
        auto new_node = std::make_unique<Node>(value);
        Node* new_tail = new_node.get();
        {
            std::lock_guard<std::mutex> lock(tail_mtx);
            tail->next = std::move(new_node);
            tail = new_tail;
        }
    }
    
    bool dequeue(T& value) {
        std::lock_guard<std::mutex> lock(head_mtx);
        if (head.get() == get_tail()) {
            return false;  // 队列为空
        }
        value = std::move(head->next->data);
        head = std::move(head->next);
        return true;
    }
};

// 3. 并发哈希表（分段锁）
template<typename K, typename V, typename Hash = std::hash<K>>
class ConcurrentHashMap {
    struct Bucket {
        std::list<std::pair<K, V>> entries;
        mutable std::shared_mutex mtx;
    };
    
    std::vector<Bucket> buckets;
    Hash hash_fn;
    
    Bucket& get_bucket(const K& key) {
        return buckets[hash_fn(key) % buckets.size()];
    }
    
public:
    explicit ConcurrentHashMap(size_t num_buckets = 16) 
        : buckets(num_buckets) {}
    
    void insert(const K& key, const V& value) {
        auto& bucket = get_bucket(key);
        std::unique_lock lock(bucket.mtx);
        for (auto& [k, v] : bucket.entries) {
            if (k == key) {
                v = value;
                return;
            }
        }
        bucket.entries.emplace_back(key, value);
    }
    
    bool find(const K& key, V& value) const {
        const auto& bucket = get_bucket(key);
        std::shared_lock lock(bucket.mtx);
        for (const auto& [k, v] : bucket.entries) {
            if (k == key) {
                value = v;
                return true;
            }
        }
        return false;
    }
    
    bool erase(const K& key) {
        auto& bucket = get_bucket(key);
        std::unique_lock lock(bucket.mtx);
        for (auto it = bucket.entries.begin(); it != bucket.entries.end(); ++it) {
            if (it->first == key) {
                bucket.entries.erase(it);
                return true;
            }
        }
        return false;
    }
    
    size_t size() const {
        size_t total = 0;
        for (const auto& bucket : buckets) {
            std::shared_lock lock(bucket.mtx);
            total += bucket.entries.size();
        }
        return total;
    }
};

// 测试
int main() {
    // 测试并发栈
    {
        ConcurrentStack<int> stack;
        std::vector<std::jthread> threads;
        for (int i = 0; i < 4; ++i) {
            threads.emplace_back([&stack, i]() {
                for (int j = 0; j < 100; ++j) {
                    stack.push(i * 100 + j);
                }
            });
        }
        threads.clear();
        
        std::atomic<int> count{0};
        threads.clear();
        for (int i = 0; i < 4; ++i) {
            threads.emplace_back([&stack, &count]() {
                int v;
                while (stack.pop(v)) {
                    count++;
                }
            });
        }
        std::cout << "Stack: popped " << count << " items\n";
    }
    
    // 测试并发队列
    {
        ConcurrentQueue<int> queue;
        std::vector<std::jthread> threads;
        std::atomic<int> produced{0}, consumed{0};
        
        for (int i = 0; i < 3; ++i) {
            threads.emplace_back([&queue, &produced, i]() {
                for (int j = 0; j < 100; ++j) {
                    queue.enqueue(i * 100 + j);
                    produced++;
                }
            });
        }
        
        for (int i = 0; i < 3; ++i) {
            threads.emplace_back([&queue, &consumed]() {
                int v;
                while (consumed.load() < 300) {
                    if (queue.dequeue(v)) {
                        consumed++;
                    }
                }
            });
        }
        
        threads.clear();
        std::cout << "Queue: produced " << produced 
                  << ", consumed " << consumed << "\n";
    }
    
    // 测试并发哈希表
    {
        ConcurrentHashMap<int, std::string> map(8);
        std::vector<std::jthread> threads;
        
        for (int i = 0; i < 4; ++i) {
            threads.emplace_back([&map, i]() {
                for (int j = 0; j < 100; ++j) {
                    int key = i * 100 + j;
                    map.insert(key, "value" + std::to_string(key));
                }
            });
        }
        threads.clear();
        
        std::string val;
        bool found = map.find(150, val);
        std::cout << "Map: find 150 -> " << (found ? val : "not found") << "\n";
        std::cout << "Map size: " << map.size() << "\n";
    }
    
    return 0;
}
```

#### 总结

- 并发数据结构在安全性和性能间权衡。
- 粗粒度锁简单但并发度低，细粒度锁复杂但并发度高。
- 并发栈：一把 mutex 即可，或用无锁 Treiber Stack。
- 并发队列：两锁分离 head/tail，或用无锁 Michael-Scott 队列。
- 并发哈希表：分段锁，每个桶独立锁，读写用 `shared_mutex`。
- 设计原则：保证不变式、避免死锁、异常安全、最小化临界区。
- 锁粒度选择：根据访问模式（读多写少、写多读少）决定。
- 生产环境优先用经过验证的库（如 Intel TBB、Folly）。

---

### 第35讲 并行算法

#### 概念

**并行算法** 利用多核并行处理数据，加速计算密集型任务。C++17 引入了并行算法（`std::execution::par`），可直接将 STL 算法并行化。本讲讲解并行算法的使用，以及如何手写并行分治算法（如并行 reduce、并行 transform）。

#### 原理

**C++17 并行执行策略**：

| 策略 | 说明 |
|------|------|
| `std::execution::seq` | 顺序执行（默认） |
| `std::execution::par` | 并行执行，元素访问可并行 |
| `std::execution::par_unseq` | 并行+向量化，允许无序访问 |

**并行算法的适用场景**：
- 数据量大（至少上万元素）
- 每个元素的计算独立
- 计算量足够大，抵消线程调度开销

**手写并行分治的模式**：

```
parallel_reduce(data, lo, hi):
    if (hi - lo < threshold):  // 数据量小，串行处理
        return sequential_reduce(data, lo, hi)
    
    mid = (lo + hi) / 2
    future left = async(parallel_reduce, data, lo, mid)
    right = parallel_reduce(data, mid, hi)
    return left.get() + right  // 合并结果
```

**关键参数：阈值（threshold）**：
- 太小：线程调度开销大于计算收益。
- 太大：并行度不足。
- 经验值：每个任务至少 1000-10000 次基本操作。

#### 例子

```cpp
#include <iostream>
#include <vector>
#include <numeric>
#include <algorithm>
#include <execution>
#include <thread>
#include <future>
#include <chrono>
#include <random>

// 1. 使用 C++17 并行算法
void parallel_stl_example() {
    std::vector<int> data(10'000'000);
    std::iota(data.begin(), data.end(), 0);
    
    // 串行求和
    auto t1 = std::chrono::high_resolution_clock::now();
    long long sum_seq = std::reduce(data.begin(), data.end(), 0LL);
    auto t2 = std::chrono::high_resolution_clock::now();
    std::cout << "Sequential: " << sum_seq << " in " 
              << std::chrono::duration_cast<std::chrono::milliseconds>(t2 - t1).count() << "ms\n";
    
    // 并行求和
    t1 = std::chrono::high_resolution_clock::now();
    long long sum_par = std::reduce(
        std::execution::par, data.begin(), data.end(), 0LL);
    auto t3 = std::chrono::high_resolution_clock::now();
    std::cout << "Parallel: " << sum_par << " in " 
              << std::chrono::duration_cast<std::chrono::milliseconds>(t3 - t2).count() << "ms\n";
    
    // 并行排序
    std::vector<int> data2(1'000'000);
    std::generate(data2.begin(), data2.end(), []() { return rand(); });
    
    t1 = std::chrono::high_resolution_clock::now();
    std::sort(std::execution::seq, data2.begin(), data2.end());
    t2 = std::chrono::high_resolution_clock::now();
    std::cout << "Sort seq: " 
              << std::chrono::duration_cast<std::chrono::milliseconds>(t2 - t1).count() << "ms\n";
    
    std::generate(data2.begin(), data2.end(), []() { return rand(); });
    t1 = std::chrono::high_resolution_clock::now();
    std::sort(std::execution::par, data2.begin(), data2.end());
    t3 = std::chrono::high_resolution_clock::now();
    std::cout << "Sort par: " 
              << std::chrono::duration_cast<std::chrono::milliseconds>(t3 - t2).count() << "ms\n";
}

// 2. 手写并行 reduce
template<typename T, typename Op>
T parallel_reduce(const std::vector<T>& data, size_t lo, size_t hi, 
                  T init, Op op, size_t threshold = 10000) {
    if (hi - lo <= threshold) {
        T result = init;
        for (size_t i = lo; i < hi; ++i) {
            result = op(result, data[i]);
        }
        return result;
    }
    
    size_t mid = lo + (hi - lo) / 2;
    auto left_future = std::async(std::launch::async, 
        [&]() { return parallel_reduce(data, lo, mid, init, op, threshold); });
    T right = parallel_reduce(data, mid, hi, init, op, threshold);
    return op(left_future.get(), right);
}

void manual_reduce_example() {
    std::vector<long long> data(10'000'000);
    for (int i = 0; i < 10'000'000; ++i) data[i] = i;
    
    auto t1 = std::chrono::high_resolution_clock::now();
    long long sum = parallel_reduce(data, 0, data.size(), 0LL, 
        [](long long a, long long b) { return a + b; });
    auto t2 = std::chrono::high_resolution_clock::now();
    
    std::cout << "Manual parallel reduce: " << sum << " in "
              << std::chrono::duration_cast<std::chrono::milliseconds>(t2 - t1).count() << "ms\n";
}

// 3. 并行 transform
template<typename T, typename U, typename Op>
void parallel_transform(const std::vector<T>& input, std::vector<U>& output,
                        Op op, size_t threshold = 10000) {
    size_t n = input.size();
    output.resize(n);
    
    std::function<void(size_t, size_t)> worker = 
        [&](size_t lo, size_t hi) {
            if (hi - lo <= threshold) {
                for (size_t i = lo; i < hi; ++i) {
                    output[i] = op(input[i]);
                }
                return;
            }
            size_t mid = lo + (hi - lo) / 2;
            auto f = std::async(std::launch::async, 
                [&]() { worker(lo, mid); });
            worker(mid, hi);
            f.get();
        };
    
    worker(0, n);
}

void transform_example() {
    std::vector<double> data(10'000'000);
    for (int i = 0; i < 10'000'000; ++i) data[i] = i * 0.1;
    
    std::vector<double> result;
    auto t1 = std::chrono::high_resolution_clock::now();
    parallel_transform(data, result, 
        [](double x) { return x * x * std::sin(x); });
    auto t2 = std::chrono::high_resolution_clock::now();
    
    std::cout << "Parallel transform: " << result.size() << " items in "
              << std::chrono::duration_cast<std::chrono::milliseconds>(t2 - t1).count() << "ms\n";
}

// 4. 并行 for_each（带线程局部结果）
template<typename T, typename Op>
void parallel_for_each_with_local(std::vector<T>& data, Op op, 
                                   size_t threshold = 10000) {
    auto worker = [&](auto self, size_t lo, size_t hi) -> void {
        if (hi - lo <= threshold) {
            for (size_t i = lo; i < hi; ++i) {
                op(data[i]);
            }
            return;
        }
        size_t mid = lo + (hi - lo) / 2;
        auto f = std::async(std::launch::async, 
            [&]() { self(self, lo, mid); });
        self(self, mid, hi);
        f.get();
    };
    
    worker(worker, 0, data.size());
}

void for_each_example() {
    std::vector<int> data(10'000'000);
    std::iota(data.begin(), data.end(), 0);
    
    std::atomic<long long> sum{0};
    auto t1 = std::chrono::high_resolution_clock::now();
    parallel_for_each_with_local(data, 
        [&sum](int x) { sum.fetch_add(x, std::memory_order_relaxed); });
    auto t2 = std::chrono::high_resolution_clock::now();
    
    std::cout << "Parallel for_each sum: " << sum << " in "
              << std::chrono::duration_cast<std::chrono::milliseconds>(t2 - t1).count() << "ms\n";
}

// 5. 并行分块处理（更高效的模式）
template<typename T, typename Op>
T parallel_chunked_reduce(const std::vector<T>& data, T init, Op op) {
    size_t n = data.size();
    unsigned int num_threads = std::thread::hardware_concurrency();
    if (num_threads == 0) num_threads = 4;
    
    size_t chunk_size = (n + num_threads - 1) / num_threads;
    
    std::vector<std::future<T>> futures;
    for (unsigned int i = 0; i < num_threads; ++i) {
        size_t start = i * chunk_size;
        size_t end = std::min(start + chunk_size, n);
        if (start >= end) break;
        
        futures.push_back(std::async(std::launch::async,
            [&data, start, end, init, op]() {
                T result = init;
                for (size_t i = start; i < end; ++i) {
                    result = op(result, data[i]);
                }
                return result;
            }));
    }
    
    T result = init;
    for (auto& f : futures) {
        result = op(result, f.get());
    }
    return result;
}

void chunked_example() {
    std::vector<long long> data(10'000'000);
    std::iota(data.begin(), data.end(), 0);
    
    auto t1 = std::chrono::high_resolution_clock::now();
    long long sum = parallel_chunked_reduce(data, 0LL, 
        [](long long a, long long b) { return a + b; });
    auto t2 = std::chrono::high_resolution_clock::now();
    
    std::cout << "Chunked reduce: " << sum << " in "
              << std::chrono::duration_cast<std::chrono::milliseconds>(t2 - t1).count() << "ms\n";
}

int main() {
    std::cout << "=== Parallel STL ===\n";
    parallel_stl_example();
    
    std::cout << "\n=== Manual Reduce ===\n";
    manual_reduce_example();
    
    std::cout << "\n=== Transform ===\n";
    transform_example();
    
    std::cout << "\n=== For Each ===\n";
    for_each_example();
    
    std::cout << "\n=== Chunked ===\n";
    chunked_example();
    
    return 0;
}
```

#### 总结

- C++17 并行算法通过 `std::execution::par` 直接并行化 STL。
- 手写并行分治：递归分解 + `std::async` + 合并结果。
- 阈值（threshold）决定何时切换到串行，太小开销大，太大并行度低。
- 分块处理（chunked）比递归分治更高效，减少任务调度开销。
- 并行算法适合数据量大、计算独立的场景。
- 并行不一定更快：数据量小或计算简单时，线程开销可能抵消收益。
- 注意：并行算法要求操作可交换可结合（对于 reduce）。
- `par_unseq` 允许向量化，但要求操作无副作用、无数据依赖。

---

### 第36讲 调试与最佳实践

#### 概念

并发程序的调试和验证是软件开发中最具挑战性的环节。并发 bug 往往难以复现、难以定位。本讲总结并发调试工具、常见陷阱和最佳实践，帮助编写正确、高效的并发代码。

#### 原理

**并发 bug 的特点**：
1. **不确定性**：依赖线程调度时序，难以复现。
2. **低概率**：可能百万次运行才出现一次。
3. **症状诡异**：崩溃在无关代码处，或数据莫名损坏。
4. **压力测试才暴露**：日常测试可能无法触发。

**主要调试工具**：

| 工具 | 类型 | 用途 |
|------|------|------|
| ThreadSanitizer (TSan) | 动态分析 | 检测数据竞争 |
| AddressSanitizer (ASan) | 动态分析 | 检测内存错误 |
| UndefinedBehaviorSanitizer | 动态分析 | 检测未定义行为 |
| Helgrind (Valgrind) | 动态分析 | 检测竞态和死锁 |
| GDB | 调试器 | 线程检查、断点 |
| perf | 性能分析 | 锁竞争分析 |

**ThreadSanitizer 原理**：
- 在编译时插桩，记录所有内存访问。
- 运行时维护"影子内存"跟踪每个位置的访问历史。
- 检测到无同步的并发访问时报告数据竞争。
- 开销大（5-10倍减速），但能发现隐藏的竞争。

#### 例子

```cpp
#include <iostream>
#include <thread>
#include <vector>
#include <mutex>
#include <atomic>
#include <chrono>

// ========== 常见陷阱 ==========

// 陷阱1：数据竞争
int shared_counter = 0;
void data_race_bug() {
    std::vector<std::thread> threads;
    for (int i = 0; i < 10; ++i) {
        threads.emplace_back([]() {
            for (int j = 0; j < 100000; ++j) {
                ++shared_counter;  // 数据竞争！
            }
        });
    }
    for (auto& t : threads) t.join();
    std::cout << "Expected 1000000, got " << shared_counter << "\n";
}

// 陷阱2：死锁（锁顺序不一致）
std::mutex mtx1, mtx2;
void deadlock_bug() {
    auto f1 = []() {
        std::lock_guard<std::mutex> l1(mtx1);
        std::this_thread::sleep_for(std::chrono::milliseconds(1));
        std::lock_guard<std::mutex> l2(mtx2);  // 等待 mtx2
    };
    auto f2 = []() {
        std::lock_guard<std::mutex> l2(mtx2);
        std::this_thread::sleep_for(std::chrono::milliseconds(1));
        std::lock_guard<std::mutex> l1(mtx1);  // 等待 mtx1 → 死锁
    };
    std::thread t1(f1), t2(f2);
    t1.join(); t2.join();
}

// 陷阱3：虚假唤醒未处理
bool ready = false;
std::mutex cv_mtx;
std::condition_variable cv;
void spurious_wakeup_bug() {
    std::thread waiter([]() {
        std::unique_lock<std::mutex> lock(cv_mtx);
        // 错误：if 而非 while，可能虚假唤醒后继续执行
        if (!ready) {
            cv.wait(lock);
        }
        std::cout << "Waiter: ready=" << ready << "\n";
    });
    
    std::this_thread::sleep_for(std::chrono::milliseconds(100));
    {
        std::lock_guard<std::mutex> lock(cv_mtx);
        ready = true;
    }
    cv.notify_one();
    waiter.join();
}

// 陷阱4：引用捕获局部变量
void dangling_reference_bug() {
    std::thread t;
    {
        int local = 42;
        t = std::thread([&local]() {  // 引用捕获，local 将失效
            std::this_thread::sleep_for(std::chrono::milliseconds(100));
            std::cout << local << "\n";  // 未定义行为
        });
    }  // local 在此销毁
    t.join();
}

// ========== 最佳实践 ==========

// 实践1：用 RAII 管理锁
void raii_lock_practice() {
    std::mutex mtx;
    int data = 0;
    
    auto worker = [&]() {
        std::lock_guard<std::mutex> lock(mtx);  // RAII，异常安全
        data++;
        // 即使抛异常，锁也会释放
    };
    
    std::vector<std::thread> threads;
    for (int i = 0; i < 10; ++i) {
        threads.emplace_back(worker);
    }
    for (auto& t : threads) t.join();
    std::cout << "RAII lock: data = " << data << "\n";
}

// 实践2：最小化临界区
void minimize_critical_section() {
    std::mutex mtx;
    
    auto worker = [&]() {
        // 准备数据（在锁外）
        int local_data = 0;
        for (int i = 0; i < 1000; ++i) {
            local_data += i;
        }
        
        // 只在更新共享数据时加锁
        {
            std::lock_guard<std::mutex> lock(mtx);
            static int shared = 0;
            shared += local_data;
        }
    };
}

// 实践3：用 atomic 替代简单计数器
void atomic_counter_practice() {
    std::atomic<int> counter{0};
    
    std::vector<std::thread> threads;
    for (int i = 0; i < 10; ++i) {
        threads.emplace_back([&]() {
            for (int j = 0; j < 100000; ++j) {
                counter.fetch_add(1, std::memory_order_relaxed);
            }
        });
    }
    for (auto& t : threads) t.join();
    std::cout << "Atomic counter: " << counter << "\n";
}

// 实践4：避免双重检查锁定的陷阱
class SafeSingleton {
    static std::atomic<SafeSingleton*> instance;
    static std::mutex mtx;
    
public:
    static SafeSingleton* get() {
        SafeSingleton* p = instance.load(std::memory_order_acquire);
        if (!p) {
            std::lock_guard<std::mutex> lock(mtx);
            p = instance.load(std::memory_order_relaxed);
            if (!p) {
                p = new SafeSingleton();
                instance.store(p, std::memory_order_release);
            }
        }
        return p;
    }
};
std::atomic<SafeSingleton*> SafeSingleton::instance{nullptr};
std::mutex SafeSingleton::mtx;

// 实践5：用 jthread 替代 thread
void jthread_practice() {
    // C++20：自动 join + 可取消
    std::jthread worker([](std::stop_token st) {
        while (!st.stop_requested()) {
            std::cout << "Working...\n";
            std::this_thread::sleep_for(std::chrono::milliseconds(100));
        }
        std::cout << "Stopped\n";
    });
    
    std::this_thread::sleep_for(std::chrono::milliseconds(500));
    worker.request_stop();
    // 自动 join
}

// 实践6：用 osyncstream 解决输出交错
void syncstream_practice() {
    auto worker = [](int id) {
        for (int i = 0; i < 3; ++i) {
            std::osyncstream(std::cout) 
                << "Thread " << id << " step " << i << "\n";
        }
    };
    
    std::vector<std::jthread> threads;
    for (int i = 0; i < 5; ++i) {
        threads.emplace_back(worker, i);
    }
}

// ========== 调试技巧 ==========

// 1. ThreadSanitizer 检测数据竞争
// 编译：g++ -fsanitize=thread -g -O1 main.cpp -o main
// 运行：./main
// TSan 会报告数据竞争的位置

// 2. GDB 调试多线程
// (gdb) info threads          查看所有线程
// (gdb) thread 2              切换到线程2
// (gdb) bt                    查看调用栈
// (gdb) break func thread 2   在线程2的func处设断点
// (gdb) set scheduler-locking on  锁定其他线程

// 3. 日志调试
class ThreadSafeLogger {
    std::mutex mtx;
public:
    template<typename... Args>
    void log(Args&&... args) {
        std::lock_guard<std::mutex> lock(mtx);
        std::cout << "[" << std::this_thread::get_id() << "] ";
        (std::cout << ... << std::forward<Args>(args)) << "\n";
    }
};

void logging_example() {
    ThreadSafeLogger logger;
    std::vector<std::jthread> threads;
    for (int i = 0; i < 3; ++i) {
        threads.emplace_back([&logger, i]() {
            logger.log("Thread ", i, " started");
            std::this_thread::sleep_for(std::chrono::milliseconds(100));
            logger.log("Thread ", i, " finished");
        });
    }
}

int main() {
    std::cout << "=== Best Practices ===\n";
    raii_lock_practice();
    atomic_counter_practice();
    
    std::cout << "\n=== jthread ===\n";
    jthread_practice();
    
    std::cout << "\n=== Syncstream ===\n";
    syncstream_practice();
    
    std::cout << "\n=== Logging ===\n";
    logging_example();
    
    // 以下示例有 bug，仅作演示，不要运行
    // std::cout << "\n=== Data Race Bug ===\n";
    // data_race_bug();
    
    // std::cout << "\n=== Deadlock Bug ===\n";
    // deadlock_bug();
    
    return 0;
}
```

**编译与调试命令汇总**：

```bash
# 基本编译
g++ -std=c++20 -pthread -O2 main.cpp -o main

# 启用 ThreadSanitizer（检测数据竞争）
g++ -std=c++20 -pthread -fsanitize=thread -g -O1 main.cpp -o main_tsan
./main_tsan

# 启用 AddressSanitizer（检测内存错误）
g++ -std=c++20 -pthread -fsanitize=address -g main.cpp -o main_asan
./main_asan

# 启用 UBSan（检测未定义行为）
g++ -std=c++20 -pthread -fsanitize=undefined -g main.cpp -o main_ubsan
./main_ubsan

# GDB 调试
g++ -std=c++20 -pthread -g main.cpp -o main_debug
gdb ./main_debug

# Valgrind/Helgrind
g++ -std=c++20 -pthread -g main.cpp -o main
valgrind --tool=helgrind ./main

# perf 性能分析
perf record ./main
perf report
```

#### 总结

**常见陷阱**：
- 数据竞争：多线程无同步访问共享数据 → 用 mutex 或 atomic。
- 死锁：锁顺序不一致 → 固定顺序或用 `std::lock`。
- 虚假唤醒：`if` 而非 `while` 检查条件 → 用谓词形式的 `wait`。
- 悬挂引用：引用捕获局部变量 → 用值捕获或确保生存期。
- 忘记 join：thread 析构时 terminate → 用 RAII 或 jthread。

**最佳实践**：
- 优先用 RAII 管理锁（`lock_guard`、`unique_lock`）。
- 最小化临界区，锁外做耗时操作。
- 简单计数器用 `atomic`，复杂数据用 mutex。
- 优先用 `jthread`（C++20）替代 `thread`。
- 用 `osyncstream` 解决输出交错。
- 单例用局部静态变量或 `call_once`。
- 优先用高层抽象（`async`、`future`）而非底层 `thread`。

**调试工具**：
- ThreadSanitizer：检测数据竞争，首选工具。
- AddressSanitizer：检测内存错误。
- GDB：`info threads`、`thread N`、`set scheduler-locking`。
- Valgrind/Helgrind：检测竞态和死锁。
- 日志：线程安全日志记录执行轨迹。

**性能建议**：
- 避免锁竞争：细粒度锁、读写锁、无锁数据结构。
- 注意伪共享（false sharing）：线程间数据避免在同一缓存行。
- 减少线程创建：用线程池。
- 合理设置并发数：CPU 密集型 = 核心数，IO 密集型 = 2×核心数。
- 用 `memory_order_relaxed` 等弱内存序优化（需谨慎）。

---

## 课程总结

恭喜完成 C++ 并发编程系统教程！回顾学习路径：

1. **第一章 并发基础**：理解并发概念、硬件基础和 C++ 并发演进。
2. **第二章 线程管理**：掌握 `std::thread` 的创建、参数传递、所有权转移和异常安全。
3. **第三章 互斥与共享数据**：学会用 mutex、RAII 锁保护共享数据，避免死锁。
4. **第四章 条件变量与等待**：掌握条件变量实现等待-通知，构建生产者-消费者模型。
5. **第五章 Future 与异步**：学会用 async、future、promise 实现异步任务和结果传递。
6. **第六章 原子操作与内存模型**：理解内存模型、原子操作、内存序和无锁编程。
7. **第七章 C++20 新特性**：掌握协程、jthread、信号量、latch/barrier 等现代工具。
8. **第八章 并发设计与实战**：学会设计线程池、并发数据结构、并行算法，掌握调试技巧。

**继续学习的方向**：
- 深入阅读《C++ Concurrency in Action》（Anthony Williams）。
- 学习 Actor 模型（如 C++ Actor Framework）。
- 研究协程库（如 CppCoro、Boost.Asio 协程）。
- 探索 GPU 并行（CUDA、OpenCL）。
- 学习分布式系统中的并发问题。

并发编程是深奥而有趣的领域，持续实践和思考是掌握它的关键。祝你在并发编程之路上越走越远！
