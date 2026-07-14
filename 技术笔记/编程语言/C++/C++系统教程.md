# C++ 系统教程

> 本教程共 24 讲，分为 8 章，从零基础逐步深入到现代 C++ 与实战应用。每讲包含「概念—原理—例子—总结」四个标准部分，循序渐进，前后连贯。

---

## 目录

- [第1章 C++ 入门基础](#第1章-c-入门基础)
  - [第1讲 C++ 概述与环境搭建](#第1讲-c-概述与环境搭建)
  - [第2讲 数据类型、变量与运算符](#第2讲-数据类型变量与运算符)
  - [第3讲 控制流程](#第3讲-控制流程)
- [第2章 函数与复合类型](#第2章-函数与复合类型)
  - [第4讲 函数与参数传递](#第4讲-函数与参数传递)
  - [第5讲 数组、字符串与指针](#第5讲-数组字符串与指针)
  - [第6讲 引用与作用域](#第6讲-引用与作用域)
- [第3章 面向对象编程](#第3章-面向对象编程)
  - [第7讲 类与对象](#第7讲-类与对象)
  - [第8讲 构造、析构与拷贝控制](#第8讲-构造析构与拷贝控制)
  - [第9讲 运算符重载与友元](#第9讲-运算符重载与友元)
- [第4章 继承与多态](#第4章-继承与多态)
  - [第10讲 继承基础](#第10讲-继承基础)
  - [第11讲 虚函数与多态](#第11讲-虚函数与多态)
  - [第12讲 多重继承与抽象类](#第12讲-多重继承与抽象类)
- [第5章 模板与泛型编程](#第5章-模板与泛型编程)
  - [第13讲 函数模板](#第13讲-函数模板)
  - [第14讲 类模板](#第14讲-类模板)
  - [第15讲 模板进阶](#第15讲-模板进阶)
- [第6章 内存管理与异常](#第6章-内存管理与异常)
  - [第16讲 动态内存与智能指针](#第16讲-动态内存与智能指针)
  - [第17讲 异常处理](#第17讲-异常处理)
  - [第18讲 移动语义与右值引用](#第18讲-移动语义与右值引用)
- [第7章 标准模板库 STL](#第7章-标准模板库-stl)
  - [第19讲 容器](#第19讲-容器)
  - [第20讲 迭代器与算法](#第20讲-迭代器与算法)
  - [第21讲 函数对象与 Lambda](#第21讲-函数对象与-lambda)
- [第8章 现代 C++ 与实战](#第8章-现代-c-与实战)
  - [第22讲 类型推导与 auto/decltype](#第22讲-类型推导与-autodecltype)
  - [第23讲 C++11/14/17/20 新特性](#第23讲-c11141720-新特性)
  - [第24讲 综合实战项目](#第24讲-综合实战项目)

---

## 第1章 C++ 入门基础

本章带你走进 C++ 的世界，从语言概述、环境搭建开始，逐步学习基本数据类型、变量、运算符以及控制流程，为后续学习打下坚实基础。

### 第1讲 C++ 概述与环境搭建

#### 概念

C++ 是一种由 Bjarne Stroustrup 于 1979 年在贝尔实验室开发的通用编程语言，它在 C 语言的基础上增加了面向对象编程、泛型编程等特性。C++ 是一种**编译型、静态类型、多范式**的编程语言，支持过程式编程、面向对象编程和泛型编程三种编程范式。它既保留了 C 语言的高效性和对底层硬件的控制能力，又引入了类、继承、多态、模板等高级抽象机制，使其既能编写接近硬件的系统级代码，也能构建大型复杂的应用软件。

#### 原理

C++ 程序的执行需要经过**编译**过程。源代码文件（`.cpp`）首先经过**预处理器**处理 `#include`、`#define` 等指令，然后由**编译器**将预处理后的代码翻译成汇编代码，再由**汇编器**生成目标文件（`.o` 或 `.obj`），最后由**链接器**将多个目标文件和库文件链接成可执行文件。这种编译方式使得 C++ 程序运行效率极高，因为代码在运行前已经被翻译成机器码，不需要像解释型语言那样在运行时翻译。

C++ 的标准由国际标准化组织（ISO）维护，先后发布了 C++98、C++03、C++11、C++14、C++17、C++20、C++23 等版本。其中 C++11 是一次重大变革，引入了智能指针、Lambda 表达式、右值引用等现代特性，使 C++ 编程更加安全和便捷。

#### 例子

下面是 C++ 的经典入门程序 "Hello, World!"：

```cpp
#include <iostream>  // 引入输入输出流标准库

int main() {
    std::cout << "Hello, World!" << std::endl;  // 输出文本到控制台
    return 0;  // 返回 0 表示程序正常结束
}
```

**代码解析**：
- `#include <iostream>`：预处理指令，引入标准输入输出库，使我们可以使用 `std::cout` 和 `std::endl`。
- `int main()`：程序入口函数，每个 C++ 程序都必须有一个 `main` 函数，返回值类型为 `int`。
- `std::cout`：标准输出流对象，`<<` 是流插入运算符，将右侧内容输出到左侧的流中。
- `std::endl`：插入换行符并刷新输出缓冲区。
- `return 0;`：向操作系统返回 0，表示程序正常退出。

**编译与运行**（以 g++ 为例）：

```bash
g++ -o hello hello.cpp    # 编译源文件，生成可执行文件 hello
./hello                    # 运行程序
```

**常用编译器**：
- **GCC/g++**：Linux 下最常用的编译器，开源免费。
- **Clang**：macOS 默认编译器，错误提示友好。
- **MSVC**：微软 Visual Studio 自带编译器，Windows 开发首选。
- **IDE 推荐**：Visual Studio（Windows）、CLion（跨平台）、VS Code（轻量级）。

#### 总结

- C++ 是在 C 语言基础上发展而来的多范式编程语言，兼具高效性和抽象能力。
- C++ 程序需要经过预处理、编译、汇编、链接四个阶段才能生成可执行文件。
- `main` 函数是每个 C++ 程序的入口，返回 0 表示正常退出。
- 推荐使用 g++ 或 Clang 编译器，配合 VS Code 或 CLion 进行开发。
- 初学者应先搭建好开发环境，确保能编译运行 "Hello, World!" 程序后再继续学习。

---

### 第2讲 数据类型、变量与运算符

#### 概念

**数据类型**决定了变量在内存中占用的空间大小以及可以进行的操作。C++ 的数据类型分为**基本类型**（整型、浮点型、字符型、布尔型）、**复合类型**（数组、指针、引用）和**自定义类型**（类、枚举、结构体）。**变量**是内存中存储数据的命名空间，必须先声明后使用。**运算符**是对数据进行操作的符号，包括算术、关系、逻辑、位、赋值等运算符。

#### 原理

C++ 是**静态类型语言**，每个变量在编译时就必须确定类型，编译器据此分配内存空间并进行类型检查。不同数据类型占用的内存大小由编译器和平台决定，但 C++ 标准规定了最小字节数。例如 `int` 至少 16 位，在大多数现代平台上为 32 位（4 字节）。C++ 还提供 `sizeof` 运算符来获取类型或变量占用的字节数。

运算符具有**优先级**和**结合性**。优先级决定了表达式中运算的先后顺序（如乘除优先于加减），结合性决定了同优先级运算符的计算方向（大多数从左到右）。当不确定优先级时，使用括号明确运算顺序是最安全的做法。

#### 例子

```cpp
#include <iostream>
#include <iomanip>

int main() {
    // ===== 基本数据类型 =====
    int age = 25;                    // 整型，通常 4 字节
    long population = 7800000000L;   // 长整型
    double pi = 3.14159265358979;    // 双精度浮点，8 字节
    float temperature = 36.5f;       // 单精度浮点，4 字节
    char grade = 'A';                // 字符型，1 字节
    bool isStudent = true;           // 布尔型，1 字节
    
    // 使用 sizeof 查看各类型大小
    std::cout << "int 大小: " << sizeof(int) << " 字节" << std::endl;
    std::cout << "double 大小: " << sizeof(double) << " 字节" << std::endl;
    std::cout << "char 大小: " << sizeof(char) << " 字节" << std::endl;
    
    // ===== 变量修饰符 =====
    const double TAX_RATE = 0.08;    // const 常量，不可修改
    unsigned int count = 100;        // 无符号整型，只能存非负数
    auto price = 19.99;              // auto 自动类型推导（C++11）
    
    // ===== 算术运算符 =====
    int a = 17, b = 5;
    std::cout << "a + b = " << a + b << std::endl;   // 22
    std::cout << "a - b = " << a - b << std::endl;   // 12
    std::cout << "a * b = " << a * b << std::endl;   // 85
    std::cout << "a / b = " << a / b << std::endl;   // 3（整数除法）
    std::cout << "a % b = " << a % b << std::endl;   // 2（取余）
    std::cout << "a / (double)b = " << a / (double)b << std::endl; // 3.4
    
    // ===== 关系与逻辑运算符 =====
    int x = 10, y = 20;
    bool result = (x < y) && (y < 30);   // 逻辑与，结果为 true
    bool result2 = (x > y) || (y == 20); // 逻辑或，结果为 true
    bool result3 = !(x == y);            // 逻辑非，结果为 true
    std::cout << std::boolalpha;         // 以 true/false 输出布尔值
    std::cout << "result: " << result << std::endl;
    
    // ===== 位运算符 =====
    int m = 0b1100;  // 12
    int n = 0b1010;  // 10
    std::cout << "m & n = " << (m & n) << std::endl;   // 8  (1000)
    std::cout << "m | n = " << (m | n) << std::endl;   // 14 (1110)
    std::cout << "m ^ n = " << (m ^ n) << std::endl;   // 6  (0110)
    std::cout << "m << 2 = " << (m << 2) << std::endl; // 48 (左移2位)
    
    // ===== 复合赋值与自增自减 =====
    int counter = 10;
    counter += 5;   // 等价于 counter = counter + 5
    counter++;      // 后置自增，counter 变为 16
    ++counter;      // 前置自增，counter 变为 17
    std::cout << "counter = " << counter << std::endl;
    
    // ===== 三目运算符 =====
    int max = (x > y) ? x : y;
    std::cout << "较大值: " << max << std::endl;
    
    return 0;
}
```

**输出结果**：
```
int 大小: 4 字节
double 大小: 8 字节
char 大小: 1 字节
a + b = 22
a - b = 12
a * b = 85
a / b = 3
a % b = 2
a / (double)b = 3.4
result: true
m & n = 8
m | n = 14
m ^ n = 6
m << 2 = 48
counter = 17
较大值: 20
```

#### 总结

- C++ 基本数据类型包括整型、浮点型、字符型和布尔型，使用 `sizeof` 可查看占用字节数。
- `const` 定义常量，`auto` 让编译器自动推导类型（C++11 引入）。
- 整数除法会截断小数部分，需转换为浮点型才能得到精确结果。
- 运算符有优先级和结合性，建议用括号明确运算顺序以避免歧义。
- 位运算符直接操作二进制位，常用于底层编程和性能优化。
- 前置自增 `++i` 比 `i++` 效率略高（对自定义类型更明显），因为不需要保存临时值。

---

### 第3讲 控制流程

#### 概念

**控制流程**决定了程序中语句的执行顺序。C++ 提供三种基本控制结构：**顺序结构**（语句按书写顺序执行）、**选择结构**（根据条件选择执行路径）和**循环结构**（重复执行某段代码）。选择结构通过 `if-else` 和 `switch` 实现，循环结构通过 `while`、`do-while` 和 `for` 实现。此外，`break`、`continue` 和 `goto` 用于改变循环的正常执行流程。

#### 原理

`if-else` 语句根据条件表达式的布尔值（`true`/`false`）选择执行分支。C++ 中任何非零值都视为 `true`，零值视为 `false`。`switch` 语句将表达式的值与各 `case` 标签匹配，匹配成功后执行对应分支，需用 `break` 跳出，否则会"穿透"执行后续 case。`switch` 只能用于整型、字符型和枚举类型。

循环的本质是"判断条件—执行循环体—更新状态"的重复过程。`for` 循环将初始化、条件判断、状态更新集中在一行，适合已知循环次数的场景；`while` 先判断后执行，可能一次都不执行；`do-while` 先执行后判断，至少执行一次。

#### 例子

```cpp
#include <iostream>
#include <cstdlib>  // rand, srand
#include <ctime>    // time

int main() {
    // ===== if-else 条件判断 =====
    int score = 85;
    if (score >= 90) {
        std::cout << "等级: A (优秀)" << std::endl;
    } else if (score >= 80) {
        std::cout << "等级: B (良好)" << std::endl;
    } else if (score >= 60) {
        std::cout << "等级: C (及格)" << std::endl;
    } else {
        std::cout << "等级: D (不及格)" << std::endl;
    }
    
    // ===== switch 多分支选择 =====
    int day = 3;
    switch (day) {
        case 1: std::cout << "星期一" << std::endl; break;
        case 2: std::cout << "星期二" << std::endl; break;
        case 3: std::cout << "星期三" << std::endl; break;
        case 4: std::cout << "星期四" << std::endl; break;
        case 5: std::cout << "星期五" << std::endl; break;
        default: std::cout << "周末" << std::endl;
    }
    
    // ===== for 循环：计算 1 到 100 的和 =====
    int sum = 0;
    for (int i = 1; i <= 100; i++) {
        sum += i;
    }
    std::cout << "1到100的和: " << sum << std::endl;  // 5050
    
    // ===== while 循环：计算数字位数 =====
    int num = 12345;
    int digits = 0;
    int temp = num;
    while (temp != 0) {
        temp /= 10;
        digits++;
    }
    std::cout << num << " 有 " << digits << " 位" << std::endl;
    
    // ===== do-while 循环：至少执行一次 =====
    // 猜数字游戏
    srand(time(0));  // 设置随机种子
    int secret = rand() % 100 + 1;  // 1-100 的随机数
    int guess;
    int attempts = 0;
    do {
        std::cout << "请猜一个 1-100 的数字: ";
        std::cin >> guess;
        attempts++;
        if (guess > secret) {
            std::cout << "太大了！" << std::endl;
        } else if (guess < secret) {
            std::cout << "太小了！" << std::endl;
        }
    } while (guess != secret);
    std::cout << "恭喜！你用了 " << attempts << " 次猜中了！" << std::endl;
    
    // ===== break 和 continue =====
    // break：找出第一个能被 7 整除的大于 100 的数
    for (int i = 101; ; i++) {
        if (i % 7 == 0) {
            std::cout << "第一个大于100且能被7整除的数: " << i << std::endl;
            break;
        }
    }
    
    // continue：输出 1-20 中的所有奇数
    std::cout << "1-20 的奇数: ";
    for (int i = 1; i <= 20; i++) {
        if (i % 2 == 0) continue;  // 跳过偶数
        std::cout << i << " ";
    }
    std::cout << std::endl;
    
    // ===== 嵌套循环：打印九九乘法表 =====
    for (int i = 1; i <= 9; i++) {
        for (int j = 1; j <= i; j++) {
            std::cout << j << "*" << i << "=" << i * j << "\t";
        }
        std::cout << std::endl;
    }
    
    return 0;
}
```

**九九乘法表输出**：
```
1*1=1	
1*2=2	2*2=4	
1*3=3	2*3=6	3*3=9	
...（依此类推）
```

#### 总结

- `if-else` 适合范围判断和复杂条件，`switch` 适合离散值的等值匹配。
- `switch` 的 `case` 标签必须是常量表达式，且不要忘记 `break`，除非有意利用穿透特性。
- `for` 适合已知循环次数，`while` 适合不确定次数但知道终止条件，`do-while` 保证至少执行一次。
- `break` 跳出当前循环，`continue` 跳过本次循环剩余部分进入下一次迭代。
- 避免使用 `goto`，它会导致代码难以理解和维护（仅在跳出深层嵌套时偶尔使用）。
- 嵌套循环时注意内层循环的执行次数，避免不必要的性能开销。

---

## 第2章 函数与复合类型

本章介绍 C++ 的函数机制、参数传递方式，以及数组、字符串、指针、引用等复合数据类型，这些是构建复杂程序的基础构件。

### 第4讲 函数与参数传递

#### 概念

**函数**是完成特定任务的独立代码块，通过函数名调用。函数由**返回类型、函数名、参数列表、函数体**四部分组成。C++ 支持多种参数传递方式：**值传递**（复制实参的值）、**引用传递**（传递变量的别名）和**指针传递**（传递变量的地址）。此外，C++ 还支持**默认参数**、**函数重载**（同名函数不同参数）和**内联函数**。

#### 原理

**值传递**时，实参的值被复制到形参中，函数内对形参的修改不影响实参，这种方式安全但开销大（尤其对大对象）。**引用传递**通过 `&` 声明引用类型形参，形参成为实参的别名，对形参的操作直接作用于实参，既避免了拷贝开销又能修改实参。**const 引用传递**（`const Type&`）既能避免拷贝又保证不被修改，是传递大对象的最佳选择。

**函数重载**依靠**名字修饰（Name Mangling）**实现：编译器根据参数类型、个数生成唯一的内部函数名，使同名函数能共存。**默认参数**从右向左依次指定，调用时从左向右匹配，有默认值的参数可以省略。

#### 例子

```cpp
#include <iostream>
#include <string>
#include <cmath>

// ===== 值传递：交换两个数（无法成功） =====
void swapByValue(int a, int b) {
    int temp = a;
    a = b;
    b = temp;
    // 这里交换的是副本，不影响原始变量
}

// ===== 引用传递：交换两个数（成功） =====
void swapByRef(int& a, int& b) {
    int temp = a;
    a = b;
    b = temp;
}

// ===== 指针传递：交换两个数（成功） =====
void swapByPtr(int* a, int* b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

// ===== const 引用传递：避免拷贝，保证安全 =====
double getLength(const std::string& str) {
    return str.length();  // 只读访问，不修改 str
}

// ===== 默认参数 =====
// 默认参数必须从右向左声明
void printInfo(std::string name, int age = 18, std::string city = "北京") {
    std::cout << "姓名: " << name << ", 年龄: " << age << ", 城市: " << city << std::endl;
}

// ===== 函数重载 =====
int add(int a, int b) {
    return a + b;
}

double add(double a, double b) {
    return a + b;
}

int add(int a, int b, int c) {
    return a + b + c;
}

// ===== 内联函数 =====
inline int square(int x) {
    return x * x;
}

// ===== 递归函数：计算阶乘 =====
long long factorial(int n) {
    if (n <= 1) return 1;       // 基准情形
    return n * factorial(n - 1); // 递归调用
}

int main() {
    int x = 10, y = 20;
    
    // 值传递
    swapByValue(x, y);
    std::cout << "值传递后: x=" << x << ", y=" << y << std::endl;  // x=10, y=20
    
    // 引用传递
    swapByRef(x, y);
    std::cout << "引用传递后: x=" << x << ", y=" << y << std::endl;  // x=20, y=10
    
    // 指针传递
    swapByPtr(&x, &y);
    std::cout << "指针传递后: x=" << x << ", y=" << y << std::endl;  // x=10, y=20
    
    // const 引用传递
    std::string message = "Hello, C++!";
    std::cout << "字符串长度: " << getLength(message) << std::endl;
    
    // 默认参数
    printInfo("张三");                    // 姓名: 张三, 年龄: 18, 城市: 北京
    printInfo("李四", 25);                // 姓名: 李四, 年龄: 25, 城市: 北京
    printInfo("王五", 30, "上海");        // 姓名: 王五, 年龄: 30, 城市: 上海
    
    // 函数重载
    std::cout << "add(int, int): " << add(3, 4) << std::endl;          // 7
    std::cout << "add(double, double): " << add(3.5, 4.5) << std::endl; // 8
    std::cout << "add(int, int, int): " << add(1, 2, 3) << std::endl;   // 6
    
    // 内联函数
    std::cout << "5的平方: " << square(5) << std::endl;  // 25
    
    // 递归
    std::cout << "5的阶乘: " << factorial(5) << std::endl;  // 120
    
    return 0;
}
```

#### 总结

- 值传递安全但有拷贝开销，引用传递高效且能修改实参，指针传递类似引用但可为 `nullptr`。
- 传递大对象时优先使用 `const Type&`，既避免拷贝又防止意外修改。
- 默认参数必须从右向左声明，调用时从左向右匹配。
- 函数重载通过参数列表（类型、个数、顺序）区分，返回类型不参与重载判断。
- `inline` 是对编译器的建议，适合简短频繁调用的函数，编译器可能忽略。
- 递归必须有基准情形（终止条件），否则会导致栈溢出；深度递归注意栈空间限制。

---

### 第5讲 数组、字符串与指针

#### 概念

**数组**是相同类型元素的有序集合，在内存中连续存储，通过下标访问（从 0 开始）。**字符串**在 C++ 中有两种表示方式：C 风格字符串（以 `\0` 结尾的字符数组）和 C++ 的 `std::string` 类。**指针**是存储变量内存地址的特殊变量，通过 `&` 取地址、`*` 解引用。指针与数组关系密切：数组名可视为指向首元素的常量指针。

#### 原理

数组在内存中连续存放，元素地址依次递增（步长为 `sizeof(元素类型)`）。因此可以通过指针算术运算访问数组元素：`arr[i]` 等价于 `*(arr + i)`。这种等价性使得指针和数组在很多场景下可互换使用。

C 风格字符串以空字符 `'\0'` 作为结束标志，操作函数（`strlen`、`strcpy`、`strcat`、`strcmp`）定义在 `<cstring>` 中。但 C 风格字符串容易发生缓冲区溢出，C++ 推荐使用 `std::string`，它自动管理内存，提供丰富的成员函数，更加安全便捷。

指针的强大在于它能直接操作内存，但也带来了风险：**野指针**（指向已释放内存）、**悬空指针**（指向无效地址）、**内存泄漏**（忘记释放动态分配的内存）。使用指针时务必初始化，释放后置空。

#### 例子

```cpp
#include <iostream>
#include <string>
#include <cstring>  // C 风格字符串函数

int main() {
    // ===== 一维数组 =====
    int scores[] = {85, 92, 78, 90, 88};
    int size = sizeof(scores) / sizeof(scores[0]);  // 计算数组长度
    
    std::cout << "成绩: ";
    for (int i = 0; i < size; i++) {
        std::cout << scores[i] << " ";
    }
    std::cout << std::endl;
    
    // 数组名是指向首元素的指针
    std::cout << "scores 的地址: " << scores << std::endl;
    std::cout << "&scores[0]: " << &scores[0] << std::endl;
    std::cout << "*scores: " << *scores << std::endl;  // 85
    
    // 指针遍历数组
    int* ptr = scores;
    std::cout << "指针遍历: ";
    for (int i = 0; i < size; i++) {
        std::cout << *(ptr + i) << " ";  // 等价于 ptr[i]
    }
    std::cout << std::endl;
    
    // ===== 二维数组 =====
    int matrix[3][4] = {
        {1, 2, 3, 4},
        {5, 6, 7, 8},
        {9, 10, 11, 12}
    };
    
    std::cout << "矩阵:" << std::endl;
    for (int i = 0; i < 3; i++) {
        for (int j = 0; j < 4; j++) {
            std::cout << matrix[i][j] << "\t";
        }
        std::cout << std::endl;
    }
    
    // ===== C 风格字符串 =====
    char cstr1[20] = "Hello";
    char cstr2[] = " World";
    
    std::cout << "strlen: " << strlen(cstr1) << std::endl;  // 5（不含 '\0'）
    strcat(cstr1, cstr2);  // 拼接
    std::cout << "拼接后: " << cstr1 << std::endl;  // Hello World
    std::cout << "比较: " << strcmp(cstr1, "Hello World") << std::endl;  // 0 表示相等
    
    // ===== C++ string 类 =====
    std::string str1 = "Hello";
    std::string str2 = " C++";
    
    std::string str3 = str1 + str2;  // 拼接
    std::cout << "拼接: " << str3 << std::endl;  // Hello C++
    std::cout << "长度: " << str3.length() << std::endl;
    std::cout << "子串: " << str3.substr(0, 5) << std::endl;  // Hello
    std::cout << "查找: " << str3.find("C++") << std::endl;   // 6（位置）
    
    // string 比较
    if (str1 == "Hello") {
        std::cout << "字符串相等" << std::endl;
    }
    
    // ===== 指针基础 =====
    int value = 42;
    int* pValue = &value;  // 取地址
    
    std::cout << "value 的值: " << value << std::endl;
    std::cout << "value 的地址: " << &value << std::endl;
    std::cout << "pValue 存储的地址: " << pValue << std::endl;
    std::cout << "pValue 指向的值: " << *pValue << std::endl;  // 解引用
    
    *pValue = 100;  // 通过指针修改值
    std::cout << "修改后 value: " << value << std::endl;  // 100
    
    // ===== 空指针 =====
    int* nullPtr = nullptr;  // C++11 推荐的空指针写法
    if (nullPtr == nullptr) {
        std::cout << "指针为空" << std::endl;
    }
    
    // ===== 指针与 const =====
    int data = 10;
    const int* p1 = &data;    // 指向 const int 的指针：不能通过 p1 修改值
    int* const p2 = &data;    // const 指针：不能改变 p2 的指向
    const int* const p3 = &data; // 都不能改
    
    // *p1 = 20;  // 错误：不能通过 p1 修改
    int data2 = 30;
    // p2 = &data2;  // 错误：不能改变 p2 的指向
    
    return 0;
}
```

#### 总结

- 数组在内存中连续存储，数组名可视为指向首元素的常量指针，`arr[i]` 等价于 `*(arr + i)`。
- 计算数组长度用 `sizeof(arr) / sizeof(arr[0])`，但数组作为函数参数时会退化为指针，无法在函数内获取长度。
- C++ 推荐使用 `std::string` 替代 C 风格字符串，更安全、更方便。
- 指针存储内存地址，`&` 取地址，`*` 解引用，使用前必须初始化。
- `const` 修饰指针有两种含义：`const int* p`（指向的值不可改）和 `int* const p`（指针指向不可改）。
- 使用 `nullptr`（C++11）代替 `NULL` 表示空指针，类型更安全。
- 避免野指针和悬空指针：指针初始化、释放后置空、不返回局部变量地址。

---

### 第6讲 引用与作用域

#### 概念

**引用**是已存在变量的别名，对引用的操作就是对原变量的操作。引用分为**左值引用**（`Type&`）和**右值引用**（`Type&&`，C++11 引入）。**作用域**是变量名的可见范围，C++ 有块作用域（`{}`内）、函数作用域、命名空间作用域和类作用域。**命名空间**（`namespace`）用于避免命名冲突，将全局作用域划分为具名的区域。

#### 原理

引用本质上是通过指针实现的，但由编译器自动解引用，使用时无需 `*`。引用一旦绑定到某个变量就不能再绑定其他变量（必须"从一而终"），且声明时必须初始化。这些限制使引用比指针更安全。引用常用于函数参数传递（避免拷贝、修改实参）和函数返回值（支持链式调用）。

作用域规则决定了变量的生命周期和可见性。**局部变量**在函数或块内定义，进入作用域时创建，离开时销毁（存储在栈上）。**全局变量**在所有函数外定义，程序开始时创建，结束时销毁，默认可被所有函数访问。**静态局部变量**（`static`）只在第一次进入函数时初始化，之后保持值不变，但作用域仍限于函数内。变量隐藏（shadowing）指内层作用域的同名变量会隐藏外层变量。

#### 例子

```cpp
#include <iostream>
#include <string>

// ===== 命名空间 =====
namespace Math {
    const double PI = 3.14159265358979;
    
    double circleArea(double r) {
        return PI * r * r;
    }
    
    namespace Inner {
        void greet() {
            std::cout << "来自 Math::Inner 的问候" << std::endl;
        }
    }
}

namespace Physics {
    const double PI = 3.14;  // 不同命名空间可以同名
    double gravity = 9.8;
}

int globalVar = 100;  // 全局变量

// ===== 引用作为函数参数 =====
void increment(int& ref) {
    ref++;  // 直接修改原变量
}

// ===== 引用作为返回值（支持链式调用） =====
int& getElement(int arr[], int index) {
    return arr[index];  // 返回数组元素的引用
}

// ===== 静态局部变量 =====
int counter() {
    static int count = 0;  // 只初始化一次
    count++;
    return count;
}

// ===== 演示变量隐藏 =====
void shadowDemo() {
    int x = 10;
    std::cout << "外层 x = " << x << std::endl;
    {
        int x = 20;  // 隐藏外层的 x
        std::cout << "内层 x = " << x << std::endl;
        std::cout << "外层 x（通过 ::）= " << ::x << std::endl;  // 无法访问局部外层
    }
    std::cout << "外层 x = " << x << std::endl;
}

int main() {
    // ===== 引用基础 =====
    int value = 42;
    int& ref = value;  // ref 是 value 的别名
    
    std::cout << "value = " << value << std::endl;
    std::cout << "ref = " << ref << std::endl;
    
    ref = 100;  // 通过引用修改
    std::cout << "修改后 value = " << value << std::endl;  // 100
    
    // 引用必须初始化，且不能重新绑定
    int another = 200;
    ref = another;  // 这是赋值，不是重新绑定！value 变为 200
    std::cout << "赋值后 value = " << value << std::endl;  // 200
    std::cout << "another = " << another << std::endl;     // 200
    
    // ===== const 引用 =====
    const int& cref = value;
    std::cout << "const 引用 cref = " << cref << std::endl;
    // cref = 50;  // 错误：const 引用不能修改
    
    // const 引用可以绑定到临时值（右值）
    const int& temp = 42;  // 编译器创建临时变量
    std::cout << "temp = " << temp << std::endl;
    
    // ===== 引用作为函数参数 =====
    int num = 5;
    increment(num);
    std::cout << "increment 后 num = " << num << std::endl;  // 6
    
    // ===== 引用作为返回值 =====
    int arr[] = {10, 20, 30, 40, 50};
    getElement(arr, 2) = 99;  // 直接给返回的引用赋值
    std::cout << "arr[2] = " << arr[2] << std::endl;  // 99
    
    // ===== 命名空间使用 =====
    std::cout << "Math::PI = " << Math::PI << std::endl;
    std::cout << "Physics::PI = " << Physics::PI << std::endl;
    std::cout << "圆面积(r=5): " << Math::circleArea(5) << std::endl;
    Math::Inner::greet();
    
    // using 声明：引入特定成员
    using Math::PI;
    std::cout << "使用 using 后 PI = " << PI << std::endl;
    
    // using 指令：引入整个命名空间
    using namespace Physics;
    std::cout << "gravity = " << gravity << std::endl;
    
    // ===== 静态局部变量 =====
    std::cout << "调用 counter 三次: ";
    std::cout << counter() << " " << counter() << " " << counter() << std::endl;  // 1 2 3
    
    // ===== 全局变量 =====
    std::cout << "全局变量 globalVar = " << globalVar << std::endl;
    int globalVar = 50;  // 局部变量隐藏全局变量
    std::cout << "局部 globalVar = " << globalVar << std::endl;
    std::cout << "全局 globalVar（::）= " << ::globalVar << std::endl;  // 100
    
    // ===== 变量隐藏演示 =====
    shadowDemo();
    
    return 0;
}
```

#### 总结

- 引用是变量的别名，必须初始化且不能重新绑定，比指针更安全。
- `const` 引用可以绑定到右值（临时值），常用于函数参数以避免拷贝。
- 引用作为返回值时，不能返回局部变量的引用（悬空引用），可以返回静态变量、全局变量或通过参数传入的对象的引用。
- 命名空间用于避免命名冲突，`using namespace` 应限制在小范围内使用，避免在头文件中使用。
- 局部变量在栈上自动创建销毁，静态局部变量只初始化一次并保持值，全局变量在整个程序生命周期内存在。
- 内层作用域的同名变量会隐藏外层变量，使用 `::` 可访问全局变量。
- 避免过多使用全局变量，它会增加代码耦合度，难以维护。

---

## 第3章 面向对象编程

面向对象编程（OOP）是 C++ 的核心特性之一。本章介绍类与对象、构造析构函数、拷贝控制以及运算符重载，帮助你掌握用 C++ 进行面向对象设计的能力。

### 第7讲 类与对象

#### 概念

**类**是一种用户自定义的数据类型，它将数据（成员变量/属性）和操作数据的函数（成员函数/方法）封装在一起。**对象**是类的实例，每个对象拥有自己独立的数据副本，但共享类的成员函数定义。C++ 类支持**访问控制**（`public`、`private`、`protected`），实现数据隐藏和封装。类的成员函数可以在类内定义（隐式 `inline`），也可以在类外定义（需用 `类名::函数名`）。

#### 原理

类的封装机制通过访问控制符实现：`private` 成员只能在类内访问，`public` 成员可以在任何地方访问，`protected` 成员可以在类内和派生类中访问（与继承相关，后续讲解）。默认情况下，`class` 的成员是 `private`，而 `struct` 的成员是 `public`（这是 class 和 struct 的唯一区别）。

对象在内存中只存储成员变量，成员函数存储在代码段，所有对象共享同一份成员函数代码。当通过对象调用成员函数时，编译器自动将对象的地址作为隐式参数传递给函数，这个隐式参数通过 `this` 指针访问。`this` 指针指向调用该函数的对象本身，使成员函数能区分不同对象的成员变量。

#### 例子

```cpp
#include <iostream>
#include <string>

// ===== 定义一个 Student 类 =====
class Student {
private:  // 私有成员，只能在类内访问
    std::string name;
    int age;
    double score;
    
public:  // 公有成员，外部可访问
    // 设置器（setter）
    void setName(const std::string& n) {
        name = n;
    }
    
    void setAge(int a) {
        if (a < 0 || a > 150) {  // 数据验证
            std::cout << "年龄无效！" << std::endl;
            return;
        }
        age = a;
    }
    
    void setScore(double s) {
        if (s < 0 || s > 100) {
            std::cout << "分数无效！" << std::endl;
            return;
        }
        score = s;
    }
    
    // 获取器（getter）
    std::string getName() const {  // const 表示不修改对象状态
        return name;
    }
    
    int getAge() const {
        return age;
    }
    
    double getScore() const {
        return score;
    }
    
    // 显示学生信息
    void display() const {
        std::cout << "姓名: " << name 
                  << ", 年龄: " << age 
                  << ", 成绩: " << score << std::endl;
    }
    
    // 判断是否及格
    bool isPass() const {
        return score >= 60.0;
    }
};

// ===== 定义一个 BankAccount 类（类外定义成员函数） =====
class BankAccount {
private:
    std::string owner;
    double balance;
    
public:
    // 声明成员函数
    void init(const std::string& name, double initialBalance);
    void deposit(double amount);
    bool withdraw(double amount);
    void showBalance() const;
};

// 类外定义成员函数，需用 BankAccount:: 限定
void BankAccount::init(const std::string& name, double initialBalance) {
    owner = name;
    balance = (initialBalance >= 0) ? initialBalance : 0;
}

void BankAccount::deposit(double amount) {
    if (amount > 0) {
        balance += amount;
        std::cout << owner << " 存入 " << amount 
                  << "，余额: " << balance << std::endl;
    }
}

bool BankAccount::withdraw(double amount) {
    if (amount > 0 && amount <= balance) {
        balance -= amount;
        std::cout << owner << " 取出 " << amount 
                  << "，余额: " << balance << std::endl;
        return true;
    }
    std::cout << "余额不足或金额无效！" << std::endl;
    return false;
}

void BankAccount::showBalance() const {
    std::cout << owner << " 当前余额: " << balance << std::endl;
}

int main() {
    // 创建 Student 对象
    Student s1;
    s1.setName("张三");
    s1.setAge(20);
    s1.setScore(85.5);
    s1.display();
    
    if (s1.isPass()) {
        std::cout << s1.getName() << " 及格了！" << std::endl;
    }
    
    // 数据验证测试
    Student s2;
    s2.setName("李四");
    s2.setAge(-5);    // 输出"年龄无效！"
    s2.setScore(150); // 输出"分数无效！"
    s2.setAge(22);
    s2.setScore(55);
    s2.display();
    
    // ===== BankAccount 使用 =====
    BankAccount account;
    account.init("王五", 1000);
    account.showBalance();
    account.deposit(500);
    account.withdraw(200);
    account.withdraw(2000);  // 余额不足
    account.showBalance();
    
    // ===== this 指针演示 =====
    // 通过一个简单的类演示 this 指针
    class Counter {
        int count;
    public:
        Counter() : count(0) {}
        Counter& increment() {  // 返回引用，支持链式调用
            this->count++;      // this 指向当前对象
            return *this;       // 返回当前对象的引用
        }
        int get() const { return this->count; }
    };
    
    Counter c;
    c.increment().increment().increment();  // 链式调用
    std::cout << "Counter: " << c.get() << std::endl;  // 3
    
    return 0;
}
```

#### 总结

- 类是数据与操作的封装，对象是类的实例，每个对象有独立的数据但共享成员函数。
- 访问控制符 `public`/`private`/`protected` 实现封装，`class` 默认 `private`，`struct` 默认 `public`。
- 成员函数可通过 `this` 指针访问当前对象，`this` 是指向对象自身的常量指针。
- `const` 成员函数承诺不修改对象状态，`const` 对象只能调用 `const` 成员函数。
- 类外定义成员函数需用 `类名::函数名` 语法，适合将声明与实现分离。
- 通过 setter/getter 控制对私有成员的访问，可在其中加入数据验证逻辑。
- 返回 `*this` 的引用可实现链式调用，如 `obj.setA(1).setB(2).setC(3)`。

---

### 第8讲 构造、析构与拷贝控制

#### 概念

**构造函数**在对象创建时自动调用，用于初始化对象。C++ 提供多种构造函数：**默认构造函数**（无参数）、**带参构造函数**、**拷贝构造函数**（用同类型对象初始化）、**移动构造函数**（C++11，窃取资源）。**析构函数**在对象销毁时自动调用，用于释放资源。**拷贝赋值运算符**和**移动赋值运算符**处理对象间的赋值。这五个特殊成员函数合称**"三/五法则"**（C++11 后为五法则）。

#### 原理

构造函数通过**初始化列表**初始化成员变量，初始化列表在对象构造时直接初始化成员，比在构造函数体内赋值更高效（尤其对 `const` 成员和引用成员，必须用初始化列表）。成员变量的初始化顺序由它们在类中声明的顺序决定，而非初始化列表中的顺序。

当对象以值传递、以值返回、用同类型对象初始化时，调用拷贝构造函数。默认的拷贝构造函数执行**浅拷贝**（逐字节复制），对于包含动态分配内存的类，浅拷贝会导致多个对象指向同一块内存，引发**双重释放**和**内存泄漏**。此时需要自定义拷贝构造函数实现**深拷贝**。

析构函数按构造的逆序调用：先构造的后析构。对于全局对象和静态对象，析构函数在 `main` 结束后调用。如果类管理资源（如动态内存、文件句柄），必须定义析构函数释放资源。

#### 例子

```cpp
#include <iostream>
#include <cstring>

// ===== String 类：演示构造、析构、拷贝控制 =====
class String {
private:
    char* data;      // 动态分配的字符数组
    size_t length;
    
public:
    // 默认构造函数
    String() : data(nullptr), length(0) {
        std::cout << "  [默认构造] 空字符串" << std::endl;
    }
    
    // 带参构造函数
    String(const char* str) {
        if (str) {
            length = std::strlen(str);
            data = new char[length + 1];  // +1 存放 '\0'
            std::strcpy(data, str);
        } else {
            data = nullptr;
            length = 0;
        }
        std::cout << "  [带参构造] \"" << (data ? data : "") << "\"" << std::endl;
    }
    
    // 拷贝构造函数（深拷贝）
    String(const String& other) {
        length = other.length;
        if (other.data) {
            data = new char[length + 1];
            std::strcpy(data, other.data);
        } else {
            data = nullptr;
        }
        std::cout << "  [拷贝构造] \"" << (data ? data : "") << "\"" << std::endl;
    }
    
    // 拷贝赋值运算符（深拷贝）
    String& operator=(const String& other) {
        std::cout << "  [拷贝赋值] \"" << (other.data ? other.data : "") 
                  << "\"" << std::endl;
        if (this != &other) {  // 自赋值检查
            delete[] data;     // 释放原有资源
            length = other.length;
            if (other.data) {
                data = new char[length + 1];
                std::strcpy(data, other.data);
            } else {
                data = nullptr;
            }
        }
        return *this;
    }
    
    // 析构函数
    ~String() {
        std::cout << "  [析构] 释放 \"" << (data ? data : "") << "\"" << std::endl;
        delete[] data;  // 释放动态内存
    }
    
    // 获取长度
    size_t size() const { return length; }
    
    // 获取 C 字符串
    const char* c_str() const { return data ? data : ""; }
    
    // 打印
    void print() const {
        std::cout << "\"" << (data ? data : "") << "\" (长度: " << length << ")";
    }
};

// ===== 演示对象生命周期 =====
void demonstrateCopy(String s) {  // 值传递，调用拷贝构造
    std::cout << "  函数内: ";
    s.print();
    std::cout << std::endl;
}  // 函数结束，s 析构

int main() {
    std::cout << "===== 1. 构造函数演示 =====" << std::endl;
    String s1;           // 默认构造
    String s2("Hello");  // 带参构造
    String s3("World");
    
    std::cout << "\n===== 2. 拷贝构造演示 =====" << std::endl;
    String s4 = s2;      // 拷贝构造
    String s5(s3);       // 拷贝构造（另一种写法）
    
    std::cout << "\n===== 3. 值传递触发拷贝 =====" << std::endl;
    demonstrateCopy(s2);
    
    std::cout << "\n===== 4. 拷贝赋值演示 =====" << std::endl;
    s1 = s2;             // 拷贝赋值
    
    std::cout << "\n===== 5. 自赋值检查 =====" << std::endl;
    s2 = s2;             // 自赋值，应安全处理
    
    std::cout << "\n===== 6. 当前状态 =====" << std::endl;
    std::cout << "s1: "; s1.print(); std::cout << std::endl;
    std::cout << "s2: "; s2.print(); std::cout << std::endl;
    std::cout << "s4: "; s4.print(); std::cout << std::endl;
    
    std::cout << "\n===== 7. 析构（离开 main 作用域）=====" << std::endl;
    // s5, s4, s3, s2, s1 按构造逆序析构
    
    return 0;
}
```

**输出（部分）**：
```
===== 1. 构造函数演示 =====
  [默认构造] 空字符串
  [带参构造] "Hello"
  [带参构造] "World"

===== 2. 拷贝构造演示 =====
  [拷贝构造] "Hello"
  [拷贝构造] "World"

===== 7. 析构（离开 main 作用域）=====
  [析构] 释放 "World"
  [析构] 释放 "Hello"
  ...
```

#### 总结

- 构造函数在对象创建时自动调用，析构函数在对象销毁时自动调用，析构顺序与构造相反。
- 初始化列表比构造函数体内赋值更高效，`const` 成员和引用成员必须用初始化列表。
- 包含动态资源的类必须自定义拷贝构造函数和拷贝赋值运算符，实现深拷贝，避免浅拷贝导致的资源问题。
- 拷贝赋值运算符必须处理自赋值情况（`if (this != &other)`），并先释放原有资源。
- "三法则"：如果类需要自定义析构函数、拷贝构造函数、拷贝赋值运算符中的任何一个，通常三个都需要自定义。
- "五法则"（C++11）：再加上移动构造函数和移动赋值运算符。
- 如果不需要自定义，可以使用 `= default` 让编译器生成默认实现，或 `= delete` 禁用。

---

### 第9讲 运算符重载与友元

#### 概念

**运算符重载**允许为自定义类型定义运算符的含义，使对象能像基本类型一样使用运算符（如 `+`、`-`、`==`、`<<` 等）。运算符重载通过定义特殊名称的函数实现：`operator运算符`。**友元**（`friend`）机制允许一个类将其私有成员的访问权限授予指定的外部函数或类，常用于运算符重载（特别是流运算符 `<<` 和 `>>`）。

#### 原理

运算符重载的本质是函数调用。`a + b` 会被编译器转换为 `operator+(a, b)`（全局函数形式）或 `a.operator+(b)`（成员函数形式）。并非所有运算符都可重载：可重载的包括算术、关系、逻辑、位、赋值、下标 `[]`、函数调用 `()`、成员访问 `->`、流 `<<` `>>` 等；不可重载的包括 `.`、`.*`、`::`、`?:`、`sizeof`、`typeid`。

运算符重载可以作为**成员函数**（左操作数必须是该类对象）或**全局函数**（通常声明为友元，左右操作数都可隐式转换）。流运算符 `<<` 和 `>>` 必须作为全局函数重载，因为左操作数是 `ostream`/`istream` 对象，不是我们的自定义类。

友元破坏了封装，应谨慎使用。一个类可以声明某个函数、另一个类、或另一个类的成员函数为友元。友元关系不是相互的，也不可传递。

#### 例子

```cpp
#include <iostream>
#include <cmath>

// ===== Complex 类：演示运算符重载 =====
class Complex {
private:
    double real;
    double imag;
    
public:
    // 构造函数
    Complex(double r = 0.0, double i = 0.0) : real(r), imag(i) {}
    
    // 获取实部和虚部
    double getReal() const { return real; }
    double getImag() const { return imag; }
    
    // ===== 成员函数形式重载算术运算符 =====
    // 加法: c1 + c2
    Complex operator+(const Complex& other) const {
        return Complex(real + other.real, imag + other.imag);
    }
    
    // 减法: c1 - c2
    Complex operator-(const Complex& other) const {
        return Complex(real - other.real, imag - other.imag);
    }
    
    // 乘法: c1 * c2
    Complex operator*(const Complex& other) const {
        return Complex(
            real * other.real - imag * other.imag,
            real * other.imag + imag * other.real
        );
    }
    
    // ===== 重载复合赋值运算符 =====
    Complex& operator+=(const Complex& other) {
        real += other.real;
        imag += other.imag;
        return *this;
    }
    
    // ===== 重载关系运算符 =====
    bool operator==(const Complex& other) const {
        return (real == other.real) && (imag == other.imag);
    }
    
    bool operator!=(const Complex& other) const {
        return !(*this == other);
    }
    
    // ===== 重载前置和后置 ++ =====
    // 前置 ++ (++c)
    Complex& operator++() {
        ++real;
        ++imag;
        return *this;
    }
    
    // 后置 ++ (c++)，用 int 参数区分
    Complex operator++(int) {
        Complex temp = *this;  // 保存旧值
        ++(*this);             // 调用前置 ++
        return temp;           // 返回旧值
    }
    
    // ===== 声明友元函数 =====
    friend std::ostream& operator<<(std::ostream& os, const Complex& c);
    friend std::istream& operator>>(std::istream& is, Complex& c);
    friend double magnitude(const Complex& c);  // 友元函数
};

// ===== 全局函数形式重载流运算符（必须为友元） =====
std::ostream& operator<<(std::ostream& os, const Complex& c) {
    os << "(" << c.real;
    if (c.imag >= 0) os << " + " << c.imag << "i)";
    else os << " - " << -c.imag << "i)";
    return os;  // 返回引用，支持链式输出
}

std::istream& operator>>(std::istream& is, Complex& c) {
    is >> c.real >> c.imag;
    return is;
}

// ===== 友元函数：计算复数的模 =====
double magnitude(const Complex& c) {
    return std::sqrt(c.real * c.real + c.imag * c.imag);
}

// ===== 重载 + 用于 Complex + double =====
Complex operator+(const Complex& c, double d) {
    return Complex(c.getReal() + d, c.getImag());
}

int main() {
    Complex c1(3, 4);
    Complex c2(1, 2);
    
    std::cout << "c1 = " << c1 << std::endl;  // (3 + 4i)
    std::cout << "c2 = " << c2 << std::endl;  // (1 + 2i)
    
    // 算术运算
    std::cout << "c1 + c2 = " << (c1 + c2) << std::endl;  // (4 + 6i)
    std::cout << "c1 - c2 = " << (c1 - c2) << std::endl;  // (2 + 2i)
    std::cout << "c1 * c2 = " << (c1 * c2) << std::endl;  // (-5 + 10i)
    
    // 复合赋值
    Complex c3 = c1;
    c3 += c2;
    std::cout << "c3 += c2: " << c3 << std::endl;
    
    // 关系运算
    std::cout << "c1 == c2? " << (c1 == c2 ? "是" : "否") << std::endl;
    std::cout << "c1 != c2? " << (c1 != c2 ? "是" : "否") << std::endl;
    
    // 前置和后置 ++
    Complex c4 = c1;
    std::cout << "c4++: " << c4++ << std::endl;  // 返回旧值 (3 + 4i)
    std::cout << "c4 现在: " << c4 << std::endl;  // (4 + 5i)
    std::cout << "++c4: " << ++c4 << std::endl;  // (5 + 6i)
    
    // 友元函数
    std::cout << "|c1| = " << magnitude(c1) << std::endl;  // 5
    
    // Complex + double
    std::cout << "c1 + 5.0 = " << (c1 + 5.0) << std::endl;  // (8 + 4i)
    
    // 输入
    std::cout << "输入一个复数（实部 虚部）: ";
    Complex c5;
    std::cin >> c5;
    std::cout << "你输入的是: " << c5 << std::endl;
    
    return 0;
}
```

#### 总结

- 运算符重载本质是函数调用，使自定义类型能像基本类型一样使用运算符。
- 成员函数形式：左操作数是当前对象；全局函数形式（通常友元）：左右操作数都可隐式转换。
- 流运算符 `<<` 和 `>>` 必须作为全局友元函数重载，因为左操作数是 `ostream`/`istream`。
- 前置 `++` 返回引用，后置 `++` 返回值（用 `int` 参数区分），前置效率更高。
- 复合赋值运算符（如 `+=`）应返回引用以支持链式赋值。
- 友元破坏封装，应谨慎使用，主要用于运算符重载和需要直接访问私有成员的辅助函数。
- 不要滥用运算符重载，重载后的语义应与运算符的常规含义一致（如 `+` 表示加法，而非减法）。

---

## 第4章 继承与多态

继承和多态是面向对象编程的核心机制，它们支持代码复用和接口统一。本章讲解继承的基础概念、虚函数实现的多态，以及多重继承和抽象类的设计。

### 第10讲 继承基础

#### 概念

**继承**是面向对象编程的基石之一，它允许创建一个新类（**派生类**/子类）从已有类（**基类**/父类）继承成员变量和成员函数，并可以添加新成员或修改已有成员的行为。C++ 支持三种继承方式：**公有继承**（`public`，最常用，表示 "is-a" 关系）、**保护继承**（`protected`）和**私有继承**（`private`，表示 "has-a" / "用...实现" 关系）。派生类可以访问基类的 `public` 和 `protected` 成员，但不能直接访问 `private` 成员。

#### 原理

继承建立了类之间的层次关系。公有继承下，基类的 `public` 成员在派生类中仍是 `public`，`protected` 成员仍是 `protected`，`private` 成员不可直接访问。派生类对象包含基类子对象，构造时先构造基类部分，再构造派生类部分；析构时顺序相反。

派生类可以**重写**（override/隐藏）基类的成员函数。如果函数签名相同，派生类的函数会隐藏基类的函数（注意：这不是真正的多态，需要虚函数才能实现运行时多态）。可以使用 `基类::函数名()` 显式调用被隐藏的基类函数。派生类构造函数通过初始化列表调用基类构造函数，如果不显式调用，则调用基类的默认构造函数。

#### 例子

```cpp
#include <iostream>
#include <string>

// ===== 基类：Animal =====
class Animal {
protected:  // 派生类可访问
    std::string name;
    int age;
    
public:
    Animal(const std::string& n, int a) : name(n), age(a) {
        std::cout << "  [Animal 构造] " << name << std::endl;
    }
    
    virtual ~Animal() {  // 虚析构函数（后续讲解原因）
        std::cout << "  [Animal 析构] " << name << std::endl;
    }
    
    void eat() const {
        std::cout << name << " 正在吃东西" << std::endl;
    }
    
    void sleep() const {
        std::cout << name << " 正在睡觉" << std::endl;
    }
    
    // 基类的虚函数（派生类可重写）
    virtual void makeSound() const {
        std::cout << name << " 发出声音" << std::endl;
    }
    
    virtual void display() const {
        std::cout << "动物 - 名字: " << name << ", 年龄: " << age;
    }
};

// ===== 派生类：Dog（公有继承） =====
class Dog : public Animal {
private:
    std::string breed;  // 品种（新增成员）
    
public:
    // 通过初始化列表调用基类构造函数
    Dog(const std::string& n, int a, const std::string& b) 
        : Animal(n, a), breed(b) {
        std::cout << "  [Dog 构造] " << name << " (" << breed << ")" << std::endl;
    }
    
    ~Dog() {
        std::cout << "  [Dog 析构] " << name << std::endl;
    }
    
    // 新增成员函数
    void wagTail() const {
        std::cout << name << " 摇尾巴" << std::endl;
    }
    
    // 重写基类的虚函数
    void makeSound() const override {  // override 关键字（C++11）
        std::cout << name << " 汪汪汪！" << std::endl;
    }
    
    void display() const override {
        std::cout << "狗 - 名字: " << name << ", 年龄: " << age 
                  << ", 品种: " << breed;
    }
};

// ===== 派生类：Cat =====
class Cat : public Animal {
private:
    bool isIndoor;
    
public:
    Cat(const std::string& n, int a, bool indoor) 
        : Animal(n, a), isIndoor(indoor) {}
    
    ~Cat() {}
    
    void makeSound() const override {
        std::cout << name << " 喵喵喵！" << std::endl;
    }
    
    void display() const override {
        std::cout << "猫 - 名字: " << name << ", 年龄: " << age 
                  << ", 类型: " << (isIndoor ? "室内" : "室外");
    }
};

// ===== 多级继承 =====
class Puppy : public Dog {  // Puppy 继承自 Dog
private:
    bool vaccinated;
    
public:
    Puppy(const std::string& n, int a, const std::string& b, bool v)
        : Dog(n, a, b), vaccinated(v) {}
    
    void display() const override {
        Dog::display();  // 调用直接基类的 display
        std::cout << ", 已疫苗: " << (vaccinated ? "是" : "否");
    }
};

int main() {
    std::cout << "===== 1. 创建 Dog 对象 =====" << std::endl;
    Dog dog("旺财", 3, "金毛");
    dog.eat();        // 继承自 Animal
    dog.sleep();      // 继承自 Animal
    dog.wagTail();    // Dog 特有
    dog.makeSound();  // 重写的版本：汪汪汪
    dog.display();
    std::cout << std::endl << std::endl;
    
    std::cout << "===== 2. 创建 Cat 对象 =====" << std::endl;
    Cat cat("咪咪", 2, true);
    cat.makeSound();  // 喵喵喵
    cat.display();
    std::cout << std::endl << std::endl;
    
    std::cout << "===== 3. 多级继承 =====" << std::endl;
    Puppy puppy("小宝", 1, "拉布拉多", true);
    puppy.makeSound();  // 继承自 Dog 的版本
    puppy.display();
    std::cout << std::endl << std::endl;
    
    std::cout << "===== 4. 构造和析构顺序 =====" << std::endl;
    {
        std::cout << "--- 创建 Dog ---" << std::endl;
        Dog temp("临时狗", 5, "哈士奇");
        std::cout << "--- 离开作用域，析构 ---" << std::endl;
    }
    // 构造顺序：Animal -> Dog
    // 析构顺序：Dog -> Animal
    
    std::cout << "\n===== 程序结束，析构所有对象 =====" << std::endl;
    return 0;
}
```

#### 总结

- 继承表示 "is-a" 关系（狗是一种动物），派生类继承基类的成员并可扩展。
- 三种继承方式：`public`（最常用，保持访问权限）、`protected`、`private`（默认）。
- 派生类构造时先构造基类部分，析构时顺序相反；通过初始化列表调用基类构造函数。
- 派生类可重写基类的虚函数，使用 `override` 关键字（C++11）确保正确重写。
- 派生类可通过 `基类::函数名()` 显式调用被隐藏的基类成员函数。
- 基类析构函数应声明为 `virtual`，确保通过基类指针删除派生类对象时调用正确的析构函数。
- 多级继承形成继承链，但层次不宜过深（一般不超过 3-4 层），否则增加复杂性。

---

### 第11讲 虚函数与多态

#### 概念

**多态**（Polymorphism）是面向对象编程的三大特性之一（封装、继承、多态），指同一接口对不同对象表现出不同行为。C++ 通过**虚函数**（`virtual`）实现**运行时多态**（动态绑定）：通过基类指针或引用调用虚函数时，实际调用的是对象真实类型的版本。这与**编译时多态**（函数重载、模板）不同，后者在编译期就确定了调用哪个函数。

#### 原理

虚函数的实现依赖于**虚函数表（vtable）**和**虚表指针（vptr）**。每个包含虚函数的类都有一个虚函数表，表中存储该类所有虚函数的地址。每个该类的对象都包含一个隐藏的虚表指针，指向所属类的虚函数表。当通过基类指针调用虚函数时，程序先通过对象的 vptr 找到虚函数表，再根据函数在表中的偏移找到正确的函数地址并调用。这种机制使得调用能在运行时根据对象的真实类型决定，而非指针的静态类型。

虚函数的调用比普通函数稍慢（多一次间接寻址），但带来了极大的灵活性。构造函数不能是虚函数（对象构造时 vptr 还未设置），析构函数在涉及继承时应声明为虚函数。`final` 关键字（C++11）可禁止进一步重写虚函数或禁止进一步继承。

#### 例子

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <memory>

// ===== 基类：Shape（形状） =====
class Shape {
protected:
    std::string name;
    
public:
    Shape(const std::string& n) : name(n) {}
    
    // 虚析构函数：确保派生类对象通过基类指针删除时正确析构
    virtual ~Shape() = default;
    
    // 纯虚函数：派生类必须实现
    virtual double area() const = 0;
    virtual double perimeter() const = 0;
    
    // 普通虚函数：派生类可重写
    virtual void draw() const {
        std::cout << "绘制 " << name << "（面积: " << area() << "）" << std::endl;
    }
    
    // 非虚函数：不参与多态
    std::string getName() const { return name; }
};

// ===== 派生类：Circle =====
class Circle : public Shape {
private:
    double radius;
    
public:
    Circle(double r) : Shape("圆形"), radius(r) {}
    
    double area() const override {
        return 3.14159265358979 * radius * radius;
    }
    
    double perimeter() const override {
        return 2 * 3.14159265358979 * radius;
    }
    
    void draw() const override {
        std::cout << "  ● 绘制圆形，半径: " << radius 
                  << "，面积: " << area() << std::endl;
    }
};

// ===== 派生类：Rectangle =====
class Rectangle : public Shape {
private:
    double width, height;
    
public:
    Rectangle(double w, double h) : Shape("矩形"), width(w), height(h) {}
    
    double area() const override {
        return width * height;
    }
    
    double perimeter() const override {
        return 2 * (width + height);
    }
    
    void draw() const override {
        std::cout << "  ▬ 绘制矩形，宽: " << width << "，高: " << height 
                  << "，面积: " << area() << std::endl;
    }
};

// ===== 派生类：Triangle =====
class Triangle : public Shape {
private:
    double a, b, c;  // 三边
    
public:
    Triangle(double s1, double s2, double s3) 
        : Shape("三角形"), a(s1), b(s2), c(s3) {}
    
    double area() const override {
        // 海伦公式
        double s = (a + b + c) / 2;
        return std::sqrt(s * (s - a) * (s - b) * (s - c));
    }
    
    double perimeter() const override {
        return a + b + c;
    }
    
    void draw() const override {
        std::cout << "  ▲ 绘制三角形，边长: " << a << ", " << b << ", " << c
                  << "，面积: " << area() << std::endl;
    }
};

// ===== 多态演示：统一处理不同形状 =====
void printShapeInfo(const Shape& shape) {  // 基类引用
    std::cout << "名称: " << shape.getName() << std::endl;
    std::cout << "  面积: " << shape.area() << std::endl;
    std::cout << "  周长: " << shape.perimeter() << std::endl;
    shape.draw();  // 多态调用
}

int main() {
    std::cout << "===== 1. 多态：通过基类指针调用 =====" << std::endl;
    Shape* shapes[] = {
        new Circle(5),
        new Rectangle(4, 6),
        new Triangle(3, 4, 5)
    };
    
    for (Shape* s : shapes) {
        s->draw();  // 多态：根据对象实际类型调用
    }
    
    // 释放内存
    for (Shape* s : shapes) {
        delete s;  // 虚析构确保调用正确的析构函数
    }
    
    std::cout << "\n===== 2. 多态：通过基类引用调用 =====" << std::endl;
    Circle circle(10);
    Rectangle rect(3, 8);
    Triangle tri(5, 12, 13);
    
    printShapeInfo(circle);
    std::cout << std::endl;
    printShapeInfo(rect);
    std::cout << std::endl;
    printShapeInfo(tri);
    
    std::cout << "\n===== 3. 使用智能指针管理多态对象 =====" << std::endl;
    std::vector<std::unique_ptr<Shape>> shapeList;
    shapeList.push_back(std::make_unique<Circle>(7));
    shapeList.push_back(std::make_unique<Rectangle>(5, 5));
    shapeList.push_back(std::make_unique<Triangle>(6, 8, 10));
    
    double totalArea = 0;
    for (const auto& s : shapeList) {
        s->draw();
        totalArea += s->area();
    }
    std::cout << "所有形状总面积: " << totalArea << std::endl;
    
    return 0;
}
```

#### 总结

- 多态允许通过基类指针/引用统一处理不同派生类对象，调用实际类型的函数版本。
- 虚函数通过虚函数表（vtable）和虚表指针（vptr）实现运行时动态绑定。
- 基类析构函数必须声明为 `virtual`，否则通过基类指针删除派生类对象时只会调用基类析构函数，导致资源泄漏。
- 纯虚函数（`= 0`）使类成为抽象类，不能实例化，派生类必须实现纯虚函数。
- `override` 关键字（C++11）帮助编译器检查是否正确重写，避免因签名不匹配导致的隐藏。
- `final` 关键字可禁止进一步重写虚函数或禁止继承某个类。
- 构造函数不能是虚函数（对象构造时 vptr 尚未设置），析构函数可以是且应该是虚函数。

---

### 第12讲 多重继承与抽象类

#### 概念

**多重继承**允许一个派生类同时继承多个基类，获得所有基类的成员。多重继承虽然强大，但会带来**菱形继承**问题：当 B 和 C 都继承自 A，D 同时继承 B 和 C 时，D 中会包含两份 A 的成员，造成二义性和冗余。C++ 通过**虚继承**（`virtual`）解决此问题。**抽象类**是包含至少一个纯虚函数的类，不能被实例化，只能作为基类使用，用于定义接口规范。

#### 原理

多重继承下，派生类对象包含每个基类的子对象。当多个基类有同名成员时，需要用 `基类名::成员名` 消除二义性。菱形继承中，虚继承使得最底层的派生类只保留一份虚基类的子对象，而非每条继承路径各一份。虚继承通过虚基类表实现，编译器在对象布局中增加指针指向共享的虚基类子对象。

抽象类定义了接口契约，纯虚函数（`= 0`）只有声明没有定义（也可有定义但需通过类名限定调用）。派生类必须实现所有纯虚函数才能成为具体类（可实例化）。抽象类是面向对象设计中"依赖抽象而非具体"原则的体现，常用于框架和库的设计。

#### 例子

```cpp
#include <iostream>
#include <string>
#include <vector>

// ===== 1. 抽象类：定义接口 =====
class Serializable {
public:
    virtual ~Serializable() = default;
    virtual std::string serialize() const = 0;  // 纯虚函数
    virtual void deserialize(const std::string& data) = 0;
};

class Printable {
public:
    virtual ~Printable() = default;
    virtual void print() const = 0;
};

// ===== 2. 菱形继承结构 =====
// 虚基类
class Person {
protected:
    std::string name;
    int age;
    
public:
    Person(const std::string& n = "", int a = 0) : name(n), age(a) {
        std::cout << "  [Person 构造] " << name << std::endl;
    }
    
    virtual ~Person() {}
    
    void showPerson() const {
        std::cout << "姓名: " << name << ", 年龄: " << age;
    }
    
    void setName(const std::string& n) { name = n; }
    void setAge(int a) { age = a; }
};

// 虚继承 Person
class Student : virtual public Person {
protected:
    std::string studentId;
    
public:
    Student(const std::string& n = "", int a = 0, const std::string& id = "")
        : Person(n, a), studentId(id) {}
    
    void study() const {
        std::cout << name << "（学号: " << studentId << "）正在学习" << std::endl;
    }
};

// 虚继承 Person
class Employee : virtual public Person {
protected:
    std::string employeeId;
    double salary;
    
public:
    Employee(const std::string& n = "", int a = 0, 
             const std::string& id = "", double s = 0)
        : Person(n, a), employeeId(id), salary(s) {}
    
    void work() const {
        std::cout << name << "（工号: " << employeeId 
                  << "）正在工作，薪水: " << salary << std::endl;
    }
};

// 多重继承：既是学生又是员工
class TeachingAssistant : public Student, public Employee,
                          public Serializable, public Printable {
private:
    std::string course;
    
public:
    // 虚继承下，最底层派生类负责初始化虚基类
    TeachingAssistant(const std::string& n, int a, 
                      const std::string& sid, const std::string& eid,
                      double sal, const std::string& c)
        : Person(n, a),            // 直接初始化虚基类
          Student(n, a, sid),      // Student 的 Person 调用被忽略
          Employee(n, a, eid, sal),// Employee 的 Person 调用被忽略
          course(c) {}
    
    void assist() const {
        std::cout << name << " 正在助教课程: " << course << std::endl;
    }
    
    // 实现 Serializable 接口
    std::string serialize() const override {
        return name + "," + std::to_string(age) + "," + 
               studentId + "," + employeeId + "," + 
               std::to_string(salary) + "," + course;
    }
    
    void deserialize(const std::string& data) override {
        // 简化实现
        size_t pos = data.find(',');
        name = data.substr(0, pos);
        // ... 实际实现需完整解析
    }
    
    // 实现 Printable 接口
    void print() const override {
        std::cout << "=== 助教信息 ===" << std::endl;
        std::cout << "  "; showPerson(); std::cout << std::endl;
        std::cout << "  学号: " << studentId << std::endl;
        std::cout << "  工号: " << employeeId << std::endl;
        std::cout << "  薪水: " << salary << std::endl;
        std::cout << "  课程: " << course << std::endl;
    }
};

// ===== 3. 抽象类作为接口的使用 =====
void processSerializable(const Serializable& obj) {
    std::cout << "序列化结果: " << obj.serialize() << std::endl;
}

void processPrintable(const Printable& obj) {
    obj.print();
}

int main() {
    std::cout << "===== 1. 多重继承与菱形继承 =====" << std::endl;
    TeachingAssistant ta("张三", 25, "S001", "E001", 5000, "数据结构");
    
    // 没有二义性：虚继承保证只有一份 Person
    ta.showPerson();  // 直接调用，无需 Person:: 限定
    std::cout << std::endl;
    
    ta.study();    // 来自 Student
    ta.work();     // 来自 Employee
    ta.assist();   // 自己的方法
    std::cout << std::endl;
    
    std::cout << "===== 2. 通过接口统一处理 =====" << std::endl;
    processPrintable(ta);
    std::cout << std::endl;
    processSerializable(ta);
    
    std::cout << "\n===== 3. 抽象类不能实例化 =====" << std::endl;
    // Serializable s;  // 错误：抽象类不能实例化
    // Printable p;     // 错误：抽象类不能实例化
    
    // 但可以用抽象类指针指向具体类
    Printable* p = &ta;
    p->print();
    
    std::cout << "\n===== 4. 多态容器 =====" << std::endl;
    std::vector<Printable*> printables;
    printables.push_back(&ta);
    // 可以添加更多实现了 Printable 的对象
    
    for (Printable* obj : printables) {
        obj->print();
    }
    
    return 0;
}
```

#### 总结

- 多重继承允许一个类继承多个基类，但应谨慎使用以避免复杂性。
- 菱形继承通过虚继承（`virtual public`）解决，保证虚基类只有一份子对象。
- 虚继承下，最底层的派生类负责初始化虚基类，中间类的虚基类构造调用被忽略。
- 同名成员二义性通过 `基类名::成员名` 消除，或使用虚继承避免。
- 抽象类包含纯虚函数，不能实例化，用于定义接口规范。
- 接口类（全部是纯虚函数）是 C++ 实现接口的方式，类似 Java 的 interface。
- 优先使用组合而非继承（"has-a" 优于 "is-a"），多重继承应限于接口继承。

---

## 第5章 模板与泛型编程

模板是 C++ 泛型编程的核心，允许编写与类型无关的代码。本章介绍函数模板、类模板及模板进阶特性，帮助你编写可复用、类型安全的通用代码。

### 第13讲 函数模板

#### 概念

**函数模板**是一种参数化函数的机制，允许用同一段代码处理不同类型的数据。模板定义了一个"蓝图"，编译器根据实际调用时的类型参数自动生成具体的函数实例（**模板实例化**）。函数模板使用 `template <typename T>` 或 `template <class T>` 声明类型参数，`T` 是类型占位符。模板支持多个类型参数和非类型参数（如整数）。

#### 原理

函数模板的实例化发生在编译期。当编译器遇到模板函数调用时，根据实参类型推导出模板参数，然后生成对应的函数实例。这个过程称为**隐式实例化**。如果需要对特定类型提供特殊实现，可以使用**模板特化**（ specialization）。

模板参数推导遵循严格规则：每个类型参数必须能从函数参数中推导出来。如果推导失败或存在歧义，需要显式指定模板参数：`func<int>(a, b)`。模板不支持自动类型转换（推导时），因此调用时类型需精确匹配。函数模板和普通函数可以重载，编译器优先选择非模板函数（如果匹配程度相同）。

#### 例子

```cpp
#include <iostream>
#include <string>
#include <vector>

// ===== 基本函数模板 =====
template <typename T>
T maxValue(const T& a, const T& b) {
    return (a > b) ? a : b;
}

// ===== 多类型参数模板 =====
template <typename T1, typename T2>
void printPair(const T1& a, const T2& b) {
    std::cout << "(" << a << ", " << b << ")" << std::endl;
}

// ===== 非类型参数模板 =====
template <typename T, int size>
void printArray(const T (&arr)[size]) {  // 数组引用，保留大小信息
    for (int i = 0; i < size; i++) {
        std::cout << arr[i] << " ";
    }
    std::cout << std::endl;
}

// ===== 通用交换函数 =====
template <typename T>
void swapValues(T& a, T& b) {
    T temp = a;
    a = b;
    b = temp;
}

// ===== 模板特化：为 const char* 提供特殊实现 =====
template <>
const char* maxValue<const char*>(const char* const& a, const char* const& b) {
    return (std::strcmp(a, b) > 0) ? a : b;
}

// ===== 可变参数模板（C++11）：计算总和 =====
// 递归终止条件
template <typename T>
T sum(T value) {
    return value;
}

// 递归展开
template <typename T, typename... Args>
T sum(T first, Args... rest) {
    return first + sum(rest...);
}

// ===== 模板与容器 =====
template <typename Container>
void printContainer(const Container& c) {
    for (const auto& elem : c) {
        std::cout << elem << " ";
    }
    std::cout << std::endl;
}

// ===== 返回类型后置（C++11）：auto + decltype =====
template <typename T, typename U>
auto add(const T& a, const U& b) -> decltype(a + b) {
    return a + b;
}

int main() {
    // 基本模板：自动推导类型
    std::cout << "max(3, 7) = " << maxValue(3, 7) << std::endl;          // int
    std::cout << "max(3.14, 2.72) = " << maxValue(3.14, 2.72) << std::endl;  // double
    std::cout << "max('a', 'z') = " << maxValue('a', 'z') << std::endl;  // char
    
    // 显式指定类型
    std::cout << "max<int>(3, 7) = " << maxValue<int>(3, 7) << std::endl;
    
    // 多类型参数
    printPair(10, 3.14);        // (10, 3.14)
    printPair("Hello", 42);     // (Hello, 42)
    printPair('A', std::string("BC"));  // (A, BC)
    
    // 非类型参数
    int arr[] = {1, 2, 3, 4, 5};
    double darr[] = {1.1, 2.2, 3.3};
    std::cout << "int 数组: "; printArray(arr);      // 1 2 3 4 5
    std::cout << "double 数组: "; printArray(darr);  // 1.1 2.2 3.3
    
    // 交换
    int x = 10, y = 20;
    std::cout << "交换前: x=" << x << ", y=" << y << std::endl;
    swapValues(x, y);
    std::cout << "交换后: x=" << x << ", y=" << y << std::endl;
    
    std::string s1 = "Hello", s2 = "World";
    swapValues(s1, s2);
    std::cout << "交换后: s1=" << s1 << ", s2=" << s2 << std::endl;
    
    // 模板特化
    const char* str1 = "apple";
    const char* str2 = "banana";
    std::cout << "max字符串: " << maxValue(str1, str2) << std::endl;  // banana
    
    // 可变参数模板
    std::cout << "sum(1, 2, 3) = " << sum(1, 2, 3) << std::endl;
    std::cout << "sum(1, 2, 3, 4, 5) = " << sum(1, 2, 3, 4, 5) << std::endl;
    std::cout << "sum(1.1, 2.2, 3.3) = " << sum(1.1, 2.2, 3.3) << std::endl;
    
    // 模板与容器
    std::vector<int> nums = {10, 20, 30, 40, 50};
    std::vector<std::string> names = {"Alice", "Bob", "Charlie"};
    std::cout << "整数容器: "; printContainer(nums);
    std::cout << "字符串容器: "; printContainer(names);
    
    // 返回类型后置
    std::cout << "add(3, 4.5) = " << add(3, 4.5) << std::endl;  // 7.5 (double)
    std::cout << "add(2.5, 3) = " << add(2.5, 3) << std::endl;  // 5.5
    
    return 0;
}
```

#### 总结

- 函数模板用 `template <typename T>` 声明，编译器根据调用自动推导类型并实例化。
- 模板支持多类型参数和非类型参数（必须是编译期常量，如整数、指针）。
- 模板特化（`template <>`）为特定类型提供特殊实现，如字符串比较需用 `strcmp`。
- 可变参数模板（C++11）通过递归展开处理任意数量的参数。
- 模板代码必须放在头文件中（编译器需要完整定义才能实例化），这是与普通函数的重要区别。
- 模板实例化在编译期完成，没有运行时开销，但会增加编译时间和代码体积。
- 使用 `auto` 和 `decltype`（C++11）可处理复杂的返回类型推导。

---

### 第14讲 类模板

#### 概念

**类模板**允许定义参数化的类，使一个类能处理多种数据类型。类模板在 STL 中广泛使用，如 `std::vector<T>`、`std::map<K, V>`。类模板使用 `template <typename T>` 声明，类型参数可用于成员变量、成员函数的参数和返回值。类模板的成员函数如果在类外定义，需用 `template <typename T>` 前缀和 `类名<T>::` 限定。

#### 原理

类模板本身不是一个类，而是一个生成类的"蓝图"。只有当用具体类型实例化时（如 `Stack<int>`），编译器才生成实际的类定义。不同类型参数生成不同的类，它们之间没有继承关系（`Stack<int>` 和 `Stack<double>` 是完全不同的类型）。

类模板的成员函数也是模板，只有在被调用时才会实例化（惰性实例化）。这意味着如果某个成员函数有语法错误但未被调用，编译可能通过。类模板支持默认模板参数、模板特化（全特化和偏特化）。类模板可作为函数参数，通常以模板形式传递。

#### 例子

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <stdexcept>
#include <initializer_list>

// ===== 类模板：Stack（栈） =====
template <typename T, int MAX_SIZE = 100>  // 默认模板参数
class Stack {
private:
    T data[MAX_SIZE];
    int topIndex;
    
public:
    // 构造函数
    Stack() : topIndex(-1) {}
    
    // 入栈
    void push(const T& value) {
        if (topIndex >= MAX_SIZE - 1) {
            throw std::overflow_error("栈已满！");
        }
        data[++topIndex] = value;
    }
    
    // 出栈
    T pop() {
        if (isEmpty()) {
            throw std::underflow_error("栈为空！");
        }
        return data[topIndex--];
    }
    
    // 查看栈顶
    const T& top() const {
        if (isEmpty()) {
            throw std::underflow_error("栈为空！");
        }
        return data[topIndex];
    }
    
    // 是否为空
    bool isEmpty() const {
        return topIndex == -1;
    }
    
    // 栈大小
    int size() const {
        return topIndex + 1;
    }
    
    // 类模板作为友元
    template <typename U, int M>
    friend std::ostream& operator<<(std::ostream& os, const Stack<U, M>& s);
};

// 类外定义友元函数
template <typename T, int MAX_SIZE>
std::ostream& operator<<(std::ostream& os, const Stack<T, MAX_SIZE>& s) {
    os << "[栈底→栈顶]: ";
    for (int i = 0; i <= s.topIndex; i++) {
        os << s.data[i] << " ";
    }
    return os;
}

// ===== 类模板：Pair（键值对） =====
template <typename K, typename V>
class Pair {
private:
    K key;
    V value;
    
public:
    Pair(const K& k = K(), const V& v = V()) : key(k), value(v) {}
    
    K getKey() const { return key; }
    V getValue() const { return value; }
    void setKey(const K& k) { key = k; }
    void setValue(const V& v) { value = v; }
    
    bool operator==(const Pair& other) const {
        return key == other.key && value == other.value;
    }
    
    void display() const {
        std::cout << "(" << key << ", " << value << ")";
    }
};

// ===== 类模板特化：Pair<string, int> =====
template <>
class Pair<std::string, int> {
private:
    std::string key;
    int value;
    
public:
    Pair(const std::string& k = "", int v = 0) : key(k), value(v) {}
    
    std::string getKey() const { return key; }
    int getValue() const { return value; }
    
    void display() const {
        std::cout << "[特化版] 字符串: \"" << key << "\", 整数: " << value;
    }
};

// ===== 使用类模板的函数模板 =====
template <typename T, int N>
void fillStack(Stack<T, N>& stack, const T& value, int count) {
    for (int i = 0; i < count && i < N; i++) {
        stack.push(value);
    }
}

int main() {
    // ===== Stack 使用 =====
    std::cout << "===== 1. Stack<int> =====" << std::endl;
    Stack<int, 50> intStack;  // 指定类型和大小
    intStack.push(10);
    intStack.push(20);
    intStack.push(30);
    std::cout << intStack << std::endl;
    std::cout << "栈顶: " << intStack.top() << std::endl;
    std::cout << "出栈: " << intStack.pop() << std::endl;
    std::cout << "出栈: " << intStack.pop() << std::endl;
    std::cout << "大小: " << intStack.size() << std::endl;
    
    std::cout << "\n===== 2. Stack<string>（使用默认大小）=====" << std::endl;
    Stack<std::string> strStack;  // 使用默认 MAX_SIZE=100
    strStack.push("Hello");
    strStack.push("World");
    strStack.push("C++");
    std::cout << strStack << std::endl;
    while (!strStack.isEmpty()) {
        std::cout << "弹出: " << strStack.pop() << std::endl;
    }
    
    // ===== Pair 使用 =====
    std::cout << "\n===== 3. Pair 模板 =====" << std::endl;
    Pair<int, std::string> p1(1, "Alice");
    Pair<int, std::string> p2(2, "Bob");
    p1.display(); std::cout << std::endl;
    p2.display(); std::cout << std::endl;
    std::cout << "p1 == p2? " << (p1 == p2 ? "是" : "否") << std::endl;
    
    // 使用特化版本
    Pair<std::string, int> p3("score", 95);
    p3.display(); std::cout << std::endl;
    
    // ===== 使用函数模板填充 Stack =====
    std::cout << "\n===== 4. 函数模板 + 类模板 =====" << std::endl;
    Stack<double, 20> dStack;
    fillStack(dStack, 3.14, 5);
    std::cout << dStack << std::endl;
    
    // ===== 异常处理 =====
    std::cout << "\n===== 5. 异常处理 =====" << std::endl;
    Stack<int, 3> smallStack;
    try {
        smallStack.push(1);
        smallStack.push(2);
        smallStack.push(3);
        smallStack.push(4);  // 会抛出异常
    } catch (const std::exception& e) {
        std::cout << "捕获异常: " << e.what() << std::endl;
    }
    
    return 0;
}
```

#### 总结

- 类模板用 `template <typename T>` 声明，类型参数可用于成员变量、函数参数和返回值。
- 类模板支持默认模板参数（如 `int MAX_SIZE = 100`），调用时可省略有默认值的参数。
- 类模板的成员函数在类外定义时，需用 `template <typename T>` 前缀和 `类名<T>::` 限定。
- 类模板特化（`template <>`）为特定类型组合提供特殊实现。
- 不同类型参数实例化的类模板是不同的类型，不能互相赋值（除非有转换构造函数）。
- 类模板的成员函数是惰性实例化的，未被调用的成员函数即使有错误也不会导致编译失败。
- 类模板广泛用于 STL，如 `vector`、`map`、`set` 等，掌握类模板是使用 STL 的基础。

---

### 第15讲 模板进阶

#### 概念

模板进阶涵盖**模板偏特化**（对部分类型参数特化）、**可变参数模板**（C++11，处理任意数量参数）、**SFINAE**（替换失败不是错误）和**概念**（C++20，约束模板参数）。这些特性使模板更灵活、更强大，是现代 C++ 泛型编程的核心技术。

#### 原理

**模板偏特化**允许对部分模板参数特化，如 `template <typename T> class Vector<T*>` 特化指针类型。偏特化仍需保留未特化的参数。**可变参数模板**通过 `typename... Args` 声明参数包，通过递归模板或折叠表达式（C++17）展开。

**SFINAE**（Substitution Failure Is Not An Error）指模板参数替换失败时不会报错，而是从重载候选中移除。常通过 `std::enable_if` 实现条件重载，根据类型特性选择不同实现。**C++20 概念**（Concepts）用 `requires` 子句直观地约束模板参数，取代了 SFINAE 的复杂写法，使模板代码更易读。

#### 例子

```cpp
#include <iostream>
#include <string>
#include <type_traits>
#include <vector>

// ===== 1. 模板偏特化：针对指针类型 =====
template <typename T>
class TypeInfo {
public:
    static void print() {
        std::cout << "类型: 普通类型" << std::endl;
    }
};

// 偏特化：指针类型
template <typename T>
class TypeInfo<T*> {
public:
    static void print() {
        std::cout << "类型: 指针类型" << std::endl;
    }
};

// 偏特化：引用类型
template <typename T>
class TypeInfo<T&> {
public:
    static void print() {
        std::cout << "类型: 引用类型" << std::endl;
    }
};

// ===== 2. 可变参数模板：类型列表 =====
// 递归终止
template <typename... Args>
struct TypeList;

template <>
struct TypeList<> {
    static const int size = 0;
};

template <typename Head, typename... Tail>
struct TypeList<Head, Tail...> {
    static const int size = 1 + TypeList<Tail...>::size;
};

// ===== 3. 可变参数函数模板：打印任意数量参数 =====
// 递归终止
void printAll() {
    std::cout << std::endl;
}

template <typename T, typename... Args>
void printAll(const T& first, const Args&... rest) {
    std::cout << first;
    if (sizeof...(rest) > 0) std::cout << ", ";
    printAll(rest...);
}

// ===== 4. C++17 折叠表达式 =====
template <typename... Args>
auto sumAll(Args... args) {
    return (args + ...);  // 二元右折叠
}

template <typename... Args>
void printFold(Args... args) {
    ((std::cout << args << " "), ...);  // 逗号折叠
    std::cout << std::endl;
}

// ===== 5. SFINAE：enable_if 条件重载 =====
// 仅对整数类型生效
template <typename T>
typename std::enable_if<std::is_integral<T>::value, T>::type
halfValue(T value) {
    std::cout << "整数版本: ";
    return value / 2;
}

// 仅对浮点类型生效
template <typename T>
typename std::enable_if<std::is_floating_point<T>::value, T>::type
halfValue(T value) {
    std::cout << "浮点版本: ";
    return value / 2.0;
}

// ===== 6. C++20 概念（如果编译器支持） =====
#if __cplusplus >= 202002L
template <typename T>
concept Numeric = std::is_integral<T>::value || std::is_floating_point<T>::value;

template <Numeric T>
T tripleValue(T value) {
    return value * 3;
}
#endif

// ===== 7. constexpr if（C++17）：编译期条件分支 =====
template <typename T>
void processValue(T value) {
    if constexpr (std::is_integral<T>::value) {
        std::cout << "处理整数: " << value << "，平方: " << value * value << std::endl;
    } else if constexpr (std::is_floating_point<T>::value) {
        std::cout << "处理浮点: " << value << "，四舍五入: " << static_cast<long long>(value + 0.5) << std::endl;
    } else {
        std::cout << "处理其他类型: " << value << std::endl;
    }
}

int main() {
    // 模板偏特化
    std::cout << "===== 1. 模板偏特化 =====" << std::endl;
    TypeInfo<int>::print();        // 普通类型
    TypeInfo<int*>::print();       // 指针类型
    TypeInfo<int&>::print();       // 引用类型
    TypeInfo<double>::print();     // 普通类型
    
    // 类型列表大小
    std::cout << "\n===== 2. 可变参数模板 =====" << std::endl;
    std::cout << "TypeList<int, double, char> 大小: " 
              << TypeList<int, double, char>::size << std::endl;
    
    // 打印任意参数
    std::cout << "printAll: ";
    printAll(1, 2.5, "hello", 'A');
    
    // 折叠表达式
    std::cout << "\n===== 3. C++17 折叠表达式 =====" << std::endl;
    std::cout << "sumAll(1, 2, 3, 4, 5) = " << sumAll(1, 2, 3, 4, 5) << std::endl;
    std::cout << "sumAll(1.1, 2.2, 3.3) = " << sumAll(1.1, 2.2, 3.3) << std::endl;
    std::cout << "printFold: ";
    printFold(1, 2, 3, "four", 5.0);
    
    // SFINAE
    std::cout << "\n===== 4. SFINAE 条件重载 =====" << std::endl;
    std::cout << halfValue(10) << std::endl;    // 整数版本: 5
    std::cout << halfValue(7.5) << std::endl;   // 浮点版本: 3.75
    
    // constexpr if
    std::cout << "\n===== 5. constexpr if =====" << std::endl;
    processValue(42);        // 处理整数
    processValue(3.14);      // 处理浮点
    processValue("hello");   // 处理其他类型
    
    // C++20 概念
#if __cplusplus >= 202002L
    std::cout << "\n===== 6. C++20 概念 =====" << std::endl;
    std::cout << "tripleValue(5) = " << tripleValue(5) << std::endl;
    std::cout << "tripleValue(2.5) = " << tripleValue(2.5) << std::endl;
    // tripleValue("hello");  // 编译错误：不满足 Numeric 概念
#endif
    
    return 0;
}
```

#### 总结

- 模板偏特化针对部分参数特化，如指针、引用类型，提供特定优化或行为。
- 可变参数模板通过 `typename... Args` 声明参数包，递归或折叠表达式展开。
- C++17 折叠表达式简化了可变参数的处理，无需递归终止条件。
- SFINAE 通过 `enable_if` 实现条件重载，根据类型特性选择不同实现。
- C++17 `if constexpr` 在编译期分支，未被选择的分支不会实例化，避免编译错误。
- C++20 概念用 `requires` 直观约束模板参数，取代 SFINAE 的复杂写法。
- 模板元编程强大但复杂，应适度使用，优先保证代码可读性。

---

## 第6章 内存管理与异常

C++ 赋予程序员对内存的直接控制权，这既是优势也是风险。本章讲解动态内存管理、智能指针、异常处理和移动语义，帮助你编写安全高效的代码。

### 第16讲 动态内存与智能指针

#### 概念

**动态内存**在程序运行时通过 `new` 分配、`delete` 释放，存储在**堆**（heap）上，生命周期由程序员控制。C++11 引入**智能指针**自动管理动态内存：`std::unique_ptr`（独占所有权）、`std::shared_ptr`（共享所有权，引用计数）、`std::weak_ptr`（弱引用，不增加引用计数）。智能指针利用 **RAII**（资源获取即初始化）模式，在析构时自动释放内存，避免内存泄漏。

#### 原理

`new` 在堆上分配内存并调用构造函数，返回指针；`delete` 调用析构函数并释放内存。`new[]`/`delete[]` 用于数组。忘记 `delete` 会导致内存泄漏，重复 `delete` 会导致未定义行为，`delete` 数组用 `delete`（而非 `delete[]`）也会出问题。

智能指针通过 RAII 管理资源：构造时获取资源，析构时释放资源。`unique_ptr` 独占对象，不能拷贝但可以移动（`std::move`），开销几乎为零。`shared_ptr` 通过引用计数管理共享对象，每次拷贝增加计数，每次析构减少计数，归零时释放对象。`weak_ptr` 解决 `shared_ptr` 循环引用问题：它指向 `shared_ptr` 管理的对象但不增加引用计数，使用前需通过 `lock()` 提升为 `shared_ptr`。

#### 例子

```cpp
#include <iostream>
#include <memory>
#include <vector>
#include <string>

class Resource {
public:
    std::string name;
    Resource(const std::string& n) : name(n) {
        std::cout << "  [构造] Resource: " << name << std::endl;
    }
    ~Resource() {
        std::cout << "  [析构] Resource: " << name << std::endl;
    }
    void use() const {
        std::cout << "  使用 Resource: " << name << std::endl;
    }
};

// ===== 1. 裸指针（需手动管理） =====
void rawPointerDemo() {
    std::cout << "--- 裸指针 ---" << std::endl;
    Resource* raw = new Resource("裸指针资源");
    raw->use();
    delete raw;  // 必须手动释放
    // 如果忘记 delete 或提前 return/抛异常，会导致内存泄漏
}

// ===== 2. unique_ptr：独占所有权 =====
void uniquePtrDemo() {
    std::cout << "\n--- unique_ptr ---" << std::endl;
    // 创建 unique_ptr
    std::unique_ptr<Resource> ptr1 = std::make_unique<Resource>("unique_ptr 资源");
    ptr1->use();
    std::cout << "ptr1 拥有: " << (ptr1 ? ptr1->name : "空") << std::endl;
    
    // 不能拷贝
    // std::unique_ptr<Resource> ptr2 = ptr1;  // 错误
    
    // 可以移动
    std::unique_ptr<Resource> ptr2 = std::move(ptr1);
    std::cout << "移动后 ptr1 拥有: " << (ptr1 ? ptr1->name : "空") << std::endl;
    std::cout << "移动后 ptr2 拥有: " << (ptr2 ? ptr2->name : "空") << std::endl;
    
    // 离开作用域，ptr2 自动释放
}

// ===== 3. shared_ptr：共享所有权 =====
void sharedPtrDemo() {
    std::cout << "\n--- shared_ptr ---" << std::endl;
    std::shared_ptr<Resource> sp1 = std::make_shared<Resource>("shared_ptr 资源");
    std::cout << "引用计数: " << sp1.use_count() << std::endl;  // 1
    
    {
        std::shared_ptr<Resource> sp2 = sp1;  // 拷贝，共享所有权
        std::cout << "sp2 引用计数: " << sp2.use_count() << std::endl;  // 2
        sp2->use();
    }  // sp2 离开作用域，引用计数减为 1
    
    std::cout << "sp1 引用计数: " << sp1.use_count() << std::endl;  // 1
    // 离开作用域，引用计数归零，自动释放
}

// ===== 4. weak_ptr：解决循环引用 =====
class Node {
public:
    std::string name;
    std::shared_ptr<Node> next;
    std::weak_ptr<Node> prev;  // 用 weak_ptr 避免循环引用
    
    Node(const std::string& n) : name(n) {
        std::cout << "  [构造] Node: " << name << std::endl;
    }
    ~Node() {
        std::cout << "  [析构] Node: " << name << std::endl;
    }
};

void weakPtrDemo() {
    std::cout << "\n--- weak_ptr 解决循环引用 ---" << std::endl;
    auto node1 = std::make_shared<Node>("节点1");
    auto node2 = std::make_shared<Node>("节点2");
    
    node1->next = node2;  // node1 持有 node2
    node2->prev = node1;  // node2 弱引用 node1（不增加引用计数）
    
    std::cout << "node1 引用计数: " << node1.use_count() << std::endl;  // 1
    std::cout << "node2 引用计数: " << node2.use_count() << std::endl;  // 2
    
    // 通过 weak_ptr 访问对象
    if (auto locked = node2->prev.lock()) {  // 提升为 shared_ptr
        std::cout << "node2 的前驱: " << locked->name << std::endl;
    }
    
    // 如果 prev 用 shared_ptr，node1 和 node2 互相引用，
    // 引用计数永远不为 0，导致内存泄漏
}

// ===== 5. 智能指针与容器 =====
void containerDemo() {
    std::cout << "\n--- 智能指针与容器 ---" << std::endl;
    std::vector<std::unique_ptr<Resource>> resources;
    resources.push_back(std::make_unique<Resource>("资源A"));
    resources.push_back(std::make_unique<Resource>("资源B"));
    resources.push_back(std::make_unique<Resource>("资源C"));
    
    for (const auto& r : resources) {
        r->use();
    }
    // 容器销毁时，所有 unique_ptr 自动释放
}

// ===== 6. 自定义删除器 =====
void customDeleterDemo() {
    std::cout << "\n--- 自定义删除器 ---" << std::endl;
    auto deleter = [](Resource* p) {
        std::cout << "  [自定义删除] " << p->name << std::endl;
        delete p;
    };
    
    std::unique_ptr<Resource, decltype(deleter)> customPtr(
        new Resource("自定义删除资源"), deleter
    );
    customPtr->use();
}

int main() {
    rawPointerDemo();
    uniquePtrDemo();
    sharedPtrDemo();
    weakPtrDemo();
    containerDemo();
    customDeleterDemo();
    
    std::cout << "\n===== 程序结束 =====" << std::endl;
    return 0;
}
```

#### 总结

- `new`/`delete` 在堆上分配/释放内存，必须配对使用，数组用 `new[]`/`delete[]`。
- 裸指针容易导致内存泄漏、悬空指针、重复释放，现代 C++ 推荐使用智能指针。
- `unique_ptr` 独占所有权，不能拷贝但可移动，开销几乎为零，应作为默认选择。
- `shared_ptr` 共享所有权，通过引用计数管理，适合多个所有者共享对象的场景。
- `weak_ptr` 解决 `shared_ptr` 循环引用问题，使用前需 `lock()` 提升为 `shared_ptr`。
- RAII 模式：资源获取即初始化，构造时获取资源，析构时释放，保证异常安全。
- 优先使用 `std::make_unique` 和 `std::make_shared` 创建智能指针，更安全更高效。
- 智能指针支持自定义删除器，可用于管理文件、套接字等非内存资源。

---

### 第17讲 异常处理

#### 概念

**异常处理**是 C++ 处理运行时错误的机制，将错误检测与错误处理分离。`throw` 抛出异常，`try` 检测异常，`catch` 捕获并处理异常。C++ 标准库提供 `std::exception` 异常基类及其派生类（如 `std::runtime_error`、`std::logic_error`、`std::bad_alloc`）。异常机制使错误处理代码更清晰，避免到处检查返回值。

#### 原理

当 `throw` 执行时，程序暂停当前执行，沿调用栈向上查找匹配的 `catch` 块（**栈展开**，stack unwinding）。栈展开过程中，所有局部对象按构造逆序析构。如果找到匹配的 `catch`，执行处理代码；如果到 `main` 都没找到，调用 `std::terminate` 终止程序。

`catch` 按顺序匹配，先匹配的先执行（与 Java 不同，C++ 不要求异常类型排序）。`catch(...)` 捕获所有异常但无法获取异常信息。异常类型匹配支持基类捕获派生类异常，因此 `catch (const std::exception& e)` 能捕获所有标准异常。异常通过引用捕获避免对象切片，`const` 引用表示不修改异常对象。

**异常安全**等级：基本保证（不泄漏资源、不破坏不变式）、强保证（操作成功或回滚）、不抛出保证（`noexcept`）。RAII 是实现异常安全的关键——即使异常发生，局部对象的析构函数仍会执行，释放资源。

#### 例子

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <stdexcept>
#include <fstream>

// ===== 1. 自定义异常类 =====
class DivisionByZeroError : public std::runtime_error {
public:
    DivisionByZeroError()
        : std::runtime_error("除零错误：除数不能为零") {}
};

class InvalidArgumentError : public std::runtime_error {
private:
    std::string detail;
    
public:
    InvalidArgumentError(const std::string& msg, const std::string& det)
        : std::runtime_error(msg), detail(det) {}
    
    const std::string& getDetail() const { return detail; }
};

// ===== 2. 抛出异常的函数 =====
double divide(double a, double b) {
    if (b == 0) {
        throw DivisionByZeroError();  // 抛出异常
    }
    return a / b;
}

int getElement(const std::vector<int>& vec, size_t index) {
    if (index >= vec.size()) {
        throw std::out_of_range(
            "索引 " + std::to_string(index) + " 超出范围，向量大小: " + 
            std::to_string(vec.size())
        );
    }
    return vec[index];
}

double sqrtValue(double x) {
    if (x < 0) {
        throw InvalidArgumentError("不能对负数开平方", "x = " + std::to_string(x));
    }
    return std::sqrt(x);
}

// ===== 3. 异常安全与 RAII =====
class FileGuard {
    std::fstream* file;
public:
    FileGuard(std::fstream* f) : file(f) {}
    ~FileGuard() {
        if (file && file->is_open()) {
            file->close();
            std::cout << "  [FileGuard] 文件已关闭" << std::endl;
        }
    }
    // 禁止拷贝
    FileGuard(const FileGuard&) = delete;
    FileGuard& operator=(const FileGuard&) = delete;
};

void processFile(const std::string& filename) {
    std::fstream file(filename);
    FileGuard guard(&file);  // RAII：即使异常也会关闭文件
    
    if (!file.is_open()) {
        throw std::runtime_error("无法打开文件: " + filename);
    }
    
    std::cout << "  处理文件: " << filename << std::endl;
    // 模拟处理过程中出错
    throw std::runtime_error("文件处理出错");
    // 即使这里抛出异常，FileGuard 析构函数仍会执行，关闭文件
}

// ===== 4. noexcept 说明 =====
// 标记函数不会抛出异常
int safeAdd(int a, int b) noexcept {
    return a + b;
}

// noexcept 条件：取决于内部操作
template <typename T>
void safeSwap(T& a, T& b) noexcept(noexcept(a = b)) {
    T temp = std::move(a);
    a = std::move(b);
    b = std::move(temp);
}

// ===== 5. 异常重新抛出 =====
void riskyOperation() {
    try {
        throw std::runtime_error("原始错误");
    } catch (const std::exception& e) {
        std::cout << "  [riskyOperation] 捕获: " << e.what() << std::endl;
        throw;  // 重新抛出当前异常
    }
}

int main() {
    // ===== 基本异常处理 =====
    std::cout << "===== 1. 基本异常处理 =====" << std::endl;
    try {
        double result = divide(10, 0);
        std::cout << "结果: " << result << std::endl;
    } catch (const DivisionByZeroError& e) {
        std::cout << "捕获除零异常: " << e.what() << std::endl;
    }
    
    // ===== 捕获标准异常 =====
    std::cout << "\n===== 2. 捕获标准异常 =====" << std::endl;
    std::vector<int> vec = {1, 2, 3};
    try {
        int val = getElement(vec, 10);
        std::cout << "值: " << val << std::endl;
    } catch (const std::out_of_range& e) {
        std::cout << "捕获越界异常: " << e.what() << std::endl;
    }
    
    // ===== 捕获自定义异常 =====
    std::cout << "\n===== 3. 捕获自定义异常 =====" << std::endl;
    try {
        sqrtValue(-5);
    } catch (const InvalidArgumentError& e) {
        std::cout << "捕获异常: " << e.what() << std::endl;
        std::cout << "详细信息: " << e.getDetail() << std::endl;
    }
    
    // ===== 基类捕获派生类异常 =====
    std::cout << "\n===== 4. 基类捕获所有标准异常 =====" << std::endl;
    try {
        throw std::logic_error("逻辑错误");
    } catch (const std::exception& e) {
        std::cout << "捕获 std::exception: " << e.what() << std::endl;
    }
    
    // ===== catch 顺序 =====
    std::cout << "\n===== 5. catch 顺序（派生类在前）=====" << std::endl;
    try {
        throw DivisionByZeroError();
    } catch (const DivisionByZeroError& e) {
        std::cout << "捕获 DivisionByZeroError: " << e.what() << std::endl;
    } catch (const std::exception& e) {
        std::cout << "捕获 std::exception: " << e.what() << std::endl;
    }
    // 如果基类在前，派生类异常会被基类捕获，无法精确处理
    
    // ===== 捕获所有异常 =====
    std::cout << "\n===== 6. 捕获所有异常 =====" << std::endl;
    try {
        throw 42;  // 抛出整数
    } catch (...) {
        std::cout << "捕获未知异常" << std::endl;
    }
    
    // ===== RAII 异常安全 =====
    std::cout << "\n===== 7. RAII 异常安全 =====" << std::endl;
    try {
        processFile("nonexistent.txt");
    } catch (const std::exception& e) {
        std::cout << "捕获: " << e.what() << std::endl;
    }
    
    // ===== 异常重新抛出 =====
    std::cout << "\n===== 8. 异常重新抛出 =====" << std::endl;
    try {
        riskyOperation();
    } catch (const std::exception& e) {
        std::cout << "main 捕获重新抛出的异常: " << e.what() << std::endl;
    }
    
    // ===== noexcept =====
    std::cout << "\n===== 9. noexcept =====" << std::endl;
    std::cout << "safeAdd(3, 4) = " << safeAdd(3, 4) << std::endl;
    std::cout << "safeAdd 是否 noexcept: " << noexcept(safeAdd(3, 4)) << std::endl;
    
    return 0;
}
```

#### 总结

- 异常处理将错误检测（throw）与错误处理（catch）分离，使代码更清晰。
- 自定义异常应继承 `std::exception` 或其派生类，重写 `what()` 方法。
- `catch` 按顺序匹配，派生类异常的 catch 应放在基类之前。
- 通过 `const` 引用捕获异常，避免对象切片和拷贝开销。
- `catch(...)` 捕获所有异常但无法获取异常信息，用于清理后重新抛出。
- 栈展开过程中局部对象自动析构，RAII 是异常安全的关键。
- `noexcept` 标记函数不抛出异常，编译器可优化，违反则调用 `terminate`。
- 不要在析构函数中抛出异常（可能导致 `terminate`），不要用异常替代正常的控制流。

---

### 第18讲 移动语义与右值引用

#### 概念

**移动语义**（C++11）允许转移资源所有权而非拷贝，大幅提升性能。**右值引用**（`T&&`）绑定到右值（临时对象、即将销毁的对象），是移动语义的基础。**移动构造函数**和**移动赋值运算符**"窃取"源对象的资源而非复制。`std::move` 将左值转换为右值引用，表示"我不再需要这个值，你可以移动它"。**完美转发**（`std::forward`）在模板函数中保持参数的值类别。

#### 原理

C++ 将表达式分为**左值**（lvalue，有名字、可取地址，如变量）和**右值**（rvalue，临时值、将亡值，如字面量、函数返回值）。左值引用 `T&` 绑定左值，右值引用 `T&&` 绑定右值。`const T&` 是特殊的，可绑定到右值（延长临时对象生命周期）。

移动语义的核心是"窃取"而非"复制"：移动构造函数将源对象的指针置为 `nullptr`，直接接管其资源，避免了深拷贝的开销。移动后，源对象处于"有效但未指定"的状态，只能析构或重新赋值，不应使用其值。

`std::move` 本质是 `static_cast<T&&>`，不移动任何东西，只是将左值转换为右值引用，使移动构造函数被选中。`std::forward<T>` 是条件转换：如果原始参数是右值，转换为右值引用；如果是左值，保持左值引用，实现完美转发。

#### 例子

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <utility>  // std::move, std::forward

// ===== 1. 演示移动语义的 String 类 =====
class MyString {
private:
    char* data;
    size_t length;
    
public:
    // 普通构造
    MyString(const char* str = "") {
        length = std::strlen(str);
        data = new char[length + 1];
        std::strcpy(data, str);
        std::cout << "  [构造] \"" << data << "\"" << std::endl;
    }
    
    // 拷贝构造（深拷贝）
    MyString(const MyString& other) {
        length = other.length;
        data = new char[length + 1];
        std::strcpy(data, other.data);
        std::cout << "  [拷贝构造] \"" << data << "\" (深拷贝)" << std::endl;
    }
    
    // 移动构造（窃取资源）
    MyString(MyString&& other) noexcept 
        : data(other.data), length(other.length) {
        other.data = nullptr;  // 源对象置空
        other.length = 0;
        std::cout << "  [移动构造] \"" << (data ? data : "") 
                  << "\" (窃取资源)" << std::endl;
    }
    
    // 拷贝赋值
    MyString& operator=(const MyString& other) {
        if (this != &other) {
            delete[] data;
            length = other.length;
            data = new char[length + 1];
            std::strcpy(data, other.data);
            std::cout << "  [拷贝赋值] \"" << data << "\"" << std::endl;
        }
        return *this;
    }
    
    // 移动赋值
    MyString& operator=(MyString&& other) noexcept {
        if (this != &other) {
            delete[] data;        // 释放自己的资源
            data = other.data;    // 窃取源对象资源
            length = other.length;
            other.data = nullptr; // 源对象置空
            other.length = 0;
            std::cout << "  [移动赋值] \"" << (data ? data : "") 
                      << "\"" << std::endl;
        }
        return *this;
    }
    
    ~MyString() {
        std::cout << "  [析构] \"" << (data ? data : "null") << "\"" << std::endl;
        delete[] data;
    }
    
    const char* c_str() const { return data ? data : ""; }
    size_t size() const { return length; }
};

// ===== 2. 返回值优化与移动 =====
MyString createString() {
    MyString temp("临时字符串");
    return temp;  // 可能触发 NRVO 或移动
}

// ===== 3. 完美转发 =====
template <typename T, typename Arg>
void process(T&& obj, Arg&& arg) {
    std::cout << "  处理: " << arg << std::endl;
}

template <typename T, typename... Args>
void emplaceHelper(Args&&... args) {
    // std::forward 保持参数的值类别
    process<T>(T{}, std::forward<Args>(args)...);
}

// ===== 4. 移动语义与容器 =====
void vectorMoveDemo() {
    std::cout << "\n--- vector 与移动语义 ---" << std::endl;
    std::vector<MyString> vec;
    
    std::cout << "push_back 拷贝:" << std::endl;
    MyString s1("拷贝源");
    vec.push_back(s1);  // 拷贝（s1 是左值）
    
    std::cout << "\npush_back 移动:" << std::endl;
    MyString s2("移动源");
    vec.push_back(std::move(s2));  // 移动（s2 被 move 后变成右值）
    std::cout << "移动后 s2: \"" << s2.c_str() << "\" (已为空)" << std::endl;
    
    std::cout << "\npush_back 临时对象（自动移动）:" << std::endl;
    vec.push_back(MyString("临时对象"));  // 临时对象自动移动
}

int main() {
    std::cout << "===== 1. 移动构造 =====" << std::endl;
    MyString a("Hello");
    MyString b = std::move(a);  // 移动构造
    std::cout << "a 移动后: \"" << a.c_str() << "\"" << std::endl;
    std::cout << "b: \"" << b.c_str() << "\"" << std::endl;
    
    std::cout << "\n===== 2. 移动赋值 =====" << std::endl;
    MyString c("World");
    MyString d("Temp");
    d = std::move(c);  // 移动赋值
    std::cout << "c 移动后: \"" << c.c_str() << "\"" << std::endl;
    std::cout << "d: \"" << d.c_str() << "\"" << std::endl;
    
    std::cout << "\n===== 3. 拷贝 vs 移动对比 =====" << std::endl;
    MyString src("测试字符串");
    std::cout << "拷贝:" << std::endl;
    MyString copy = src;  // 拷贝
    std::cout << "移动:" << std::endl;
    MyString moved = std::move(src);  // 移动
    
    std::cout << "\n===== 4. 返回值 =====" << std::endl;
    MyString result = createString();
    std::cout << "result: \"" << result.c_str() << "\"" << std::endl;
    
    // ===== vector 与移动 =====
    vectorMoveDemo();
    
    std::cout << "\n===== 5. std::move 注意事项 =====" << std::endl;
    std::string str = "Hello";
    std::string moved_str = std::move(str);
    std::cout << "moved_str: " << moved_str << std::endl;
    std::cout << "str 移动后（状态未指定，但有效）: \"" << str << "\"" << std::endl;
    // 移动后的 str 处于有效但未指定状态，可以重新赋值
    str = "重新赋值";
    std::cout << "str 重新赋值: " << str << std::endl;
    
    return 0;
}
```

#### 总结

- 右值引用 `T&&` 绑定到右值（临时对象），是移动语义的基础。
- 移动构造函数和移动赋值运算符"窃取"源对象资源，避免深拷贝，大幅提升性能。
- `std::move` 是 `static_cast<T&&>`，将左值转为右值引用，本身不移动任何东西。
- 移动后的源对象处于"有效但未指定"状态，只能析构或重新赋值。
- 移动构造/赋值应标记 `noexcept`，使容器在扩容时优先使用移动而非拷贝。
- 完美转发通过 `std::forward<T>` 保持参数的值类别，常用于泛型工厂函数。
- 实现了移动语义的类应同时实现拷贝控制（三/五法则），未实现移动时自动退化为拷贝。
- `const` 对象无法移动（`const T&&` 无意义），因为移动会修改源对象。

---

## 第7章 标准模板库 STL

STL 是 C++ 标准库的核心，提供容器、迭代器、算法和函数对象。本章介绍常用容器、迭代器与算法、以及函数对象和 Lambda 表达式，帮助你高效编写数据处理代码。

### 第19讲 容器

#### 概念

**STL 容器**是存储和管理数据集合的模板类，分为三大类：**序列容器**（`vector`、`deque`、`list`、`forward_list`、`array`）按顺序存储元素；**关联容器**（`set`、`map`、`multiset`、`multimap`）按键排序存储，基于红黑树；**无序关联容器**（`unordered_set`、`unordered_map` 等，C++11）基于哈希表，平均 O(1) 查找。此外还有**容器适配器**（`stack`、`queue`、`priority_queue`）。

#### 原理

不同容器有不同的数据结构和性能特征，选择合适的容器是高效编程的关键。`vector` 是动态数组，内存连续，随机访问 O(1)，尾部插入 O(1) 均摊，中间插入 O(n)。`list` 是双向链表，插入删除 O(1)（已知位置），不支持随机访问。`map`/`set` 基于红黑树，元素有序，查找/插入/删除 O(log n)。`unordered_map`/`unordered_set` 基于哈希表，平均 O(1) 但最坏 O(n)，元素无序。

容器选择原则：需要随机访问用 `vector`；频繁中间插入删除用 `list`；需要键值对查找用 `map`（有序）或 `unordered_map`（无序，更快）；需要去重用 `set` 或 `unordered_set`。默认情况下优先 `vector`，它在大多数场景下性能最优（缓存友好）。

#### 例子

```cpp
#include <iostream>
#include <vector>
#include <list>
#include <deque>
#include <map>
#include <set>
#include <unordered_map>
#include <unordered_set>
#include <stack>
#include <queue>
#include <string>
#include <algorithm>

int main() {
    // ===== 1. vector：动态数组 =====
    std::cout << "===== 1. vector =====" << std::endl;
    std::vector<int> vec = {1, 2, 3, 4, 5};
    vec.push_back(6);           // 尾部添加
    vec.push_back(7);
    vec.pop_back();             // 尾部删除
    vec[0] = 100;               // 随机访问修改
    
    std::cout << "vector: ";
    for (int v : vec) std::cout << v << " ";
    std::cout << std::endl;
    std::cout << "大小: " << vec.size() << ", 容量: " << vec.capacity() << std::endl;
    std::cout << "第一个: " << vec.front() << ", 最后一个: " << vec.back() << std::endl;
    
    // ===== 2. list：双向链表 =====
    std::cout << "\n===== 2. list =====" << std::endl;
    std::list<std::string> lst = {"B", "D", "F"};
    lst.push_front("A");        // 头部插入 O(1)
    lst.push_back("G");
    auto it = std::find(lst.begin(), lst.end(), "D");
    if (it != lst.end()) {
        lst.insert(it, "C");    // 在 D 前插入 C
    }
    lst.remove("F");            // 删除所有 "F"
    
    std::cout << "list: ";
    for (const auto& s : lst) std::cout << s << " ";
    std::cout << std::endl;
    lst.sort();                 // 链表自带排序
    std::cout << "排序后: ";
    for (const auto& s : lst) std::cout << s << " ";
    std::cout << std::endl;
    
    // ===== 3. deque：双端队列 =====
    std::cout << "\n===== 3. deque =====" << std::endl;
    std::deque<int> dq = {2, 3, 4};
    dq.push_front(1);           // 头部插入 O(1)
    dq.push_back(5);            // 尾部插入 O(1)
    std::cout << "deque: ";
    for (int v : dq) std::cout << v << " ";
    std::cout << std::endl;
    
    // ===== 4. map：有序键值对 =====
    std::cout << "\n===== 4. map =====" << std::endl;
    std::map<std::string, int> scores;
    scores["Alice"] = 90;
    scores["Bob"] = 85;
    scores["Charlie"] = 95;
    scores.insert({"David", 88});
    
    std::cout << "map（按键排序）:" << std::endl;
    for (const auto& [name, score] : scores) {  // C++17 结构化绑定
        std::cout << "  " << name << ": " << score << std::endl;
    }
    
    // 查找
    auto it2 = scores.find("Bob");
    if (it2 != scores.end()) {
        std::cout << "找到 Bob: " << it2->second << std::endl;
    }
    std::cout << "Alice 的分数: " << scores.at("Alice") << std::endl;  // 带边界检查
    // scores.at("Eve");  // 抛出 out_of_range
    // scores["Eve"];     // 不存在则创建（值为 0）
    
    // ===== 5. unordered_map：哈希表 =====
    std::cout << "\n===== 5. unordered_map =====" << std::endl;
    std::unordered_map<std::string, int> umap;
    umap["apple"] = 5;
    umap["banana"] = 3;
    umap["cherry"] = 8;
    
    std::cout << "unordered_map（无序）:" << std::endl;
    for (const auto& [key, val] : umap) {
        std::cout << "  " << key << ": " << val << std::endl;
    }
    std::cout << "桶数量: " << umap.bucket_count() 
              << ", 负载因子: " << umap.load_factor() << std::endl;
    
    // ===== 6. set：有序集合（去重） =====
    std::cout << "\n===== 6. set =====" << std::endl;
    std::set<int> s = {5, 3, 8, 1, 3, 5, 9};  // 重复元素自动去重
    std::cout << "set（有序去重）: ";
    for (int v : s) std::cout << v << " ";
    std::cout << std::endl;
    
    auto range = s.equal_range(5);  // 找到 5 的范围
    std::cout << "大于等于 5 的元素: ";
    for (auto it = range.first; it != range.second; ++it) {
        std::cout << *it << " ";
    }
    std::cout << std::endl;
    
    // ===== 7. multiset：允许重复 =====
    std::cout << "\n===== 7. multiset =====" << std::endl;
    std::multiset<int> ms = {5, 3, 8, 1, 3, 5, 9};
    std::cout << "multiset（允许重复）: ";
    for (int v : ms) std::cout << v << " ";
    std::cout << std::endl;
    std::cout << "5 的个数: " << ms.count(5) << std::endl;
    
    // ===== 8. stack：栈 =====
    std::cout << "\n===== 8. stack =====" << std::endl;
    std::stack<int> stk;
    stk.push(1); stk.push(2); stk.push(3);
    std::cout << "栈顶: " << stk.top() << std::endl;  // 3
    stk.pop();
    std::cout << "弹出后栈顶: " << stk.top() << std::endl;  // 2
    
    // ===== 9. queue：队列 =====
    std::cout << "\n===== 9. queue =====" << std::endl;
    std::queue<std::string> q;
    q.push("First");
    q.push("Second");
    q.push("Third");
    std::cout << "队首: " << q.front() << ", 队尾: " << q.back() << std::endl;
    q.pop();
    std::cout << "弹出后队首: " << q.front() << std::endl;
    
    // ===== 10. priority_queue：优先队列（堆） =====
    std::cout << "\n===== 10. priority_queue =====" << std::endl;
    // 默认最大堆
    std::priority_queue<int> pq;
    pq.push(3); pq.push(1); pq.push(4); pq.push(1); pq.push(5);
    std::cout << "最大堆弹出顺序: ";
    while (!pq.empty()) {
        std::cout << pq.top() << " ";
        pq.pop();
    }
    std::cout << std::endl;
    
    // 最小堆
    std::priority_queue<int, std::vector<int>, std::greater<int>> minHeap;
    minHeap.push(3); minHeap.push(1); minHeap.push(4);
    std::cout << "最小堆弹出顺序: ";
    while (!minHeap.empty()) {
        std::cout << minHeap.top() << " ";
        minHeap.pop();
    }
    std::cout << std::endl;
    
    return 0;
}
```

#### 总结

- 序列容器：`vector`（动态数组，随机访问快）、`list`（链表，插入删除快）、`deque`（双端队列）。
- 关联容器：`map`/`set`（红黑树，有序，O(log n)）、`multimap`/`multiset`（允许重复键）。
- 无序容器：`unordered_map`/`unordered_set`（哈希表，平均 O(1)，无序）。
- 容器适配器：`stack`（栈）、`queue`（队列）、`priority_queue`（优先队列/堆）。
- 选择原则：默认 `vector`；频繁中间插入删除用 `list`；键值查找用 `map` 或 `unordered_map`。
- `map` 的 `[]` 运算符在键不存在时会创建（值为默认构造），`at()` 会抛异常。
- C++17 结构化绑定 `for (const auto& [key, val] : map)` 简化遍历代码。
- `priority_queue` 默认最大堆，用 `std::greater<T>` 作为比较器得到最小堆。

---

### 第20讲 迭代器与算法

#### 概念

**迭代器**是类似指针的对象，提供统一的方式遍历容器元素，是容器与算法之间的桥梁。迭代器分为五类：**输入迭代器**（只读，单向）、**输出迭代器**（只写，单向）、**前向迭代器**（读写，单向）、**双向迭代器**（读写，双向）、**随机访问迭代器**（读写，随机访问）。**STL 算法**是定义在 `<algorithm>` 中的通用函数模板，通过迭代器操作容器，包括查找、排序、变换、数值计算等。

#### 原理

迭代器抽象了容器内部结构，使算法能以统一方式处理不同容器。例如 `std::sort` 需要随机访问迭代器，可用于 `vector` 和 `deque`，但不能用于 `list`（`list` 有自己的 `sort` 成员函数）。迭代器失效是常见陷阱：某些操作（如 `vector` 扩容、`insert`/`erase`）会使迭代器失效，使用前需了解容器的失效规则。

STL 算法分为四类：**非修改式算法**（`find`、`count`、`search`）、**修改式算法**（`copy`、`transform`、`replace`）、**排序算法**（`sort`、`partial_sort`、`nth_element`）、**数值算法**（`accumulate`、`inner_product`，在 `<numeric>` 中）。算法通过迭代器范围 `[first, last)` 指定操作区间，左闭右开。

#### 例子

```cpp
#include <iostream>
#include <vector>
#include <list>
#include <algorithm>
#include <numeric>
#include <string>

int main() {
    std::vector<int> nums = {5, 2, 8, 1, 9, 3, 7, 4, 6};
    std::vector<int> nums2 = nums;
    
    // ===== 1. 非修改式算法 =====
    std::cout << "===== 1. 非修改式算法 =====" << std::endl;
    
    // find：查找元素
    auto it = std::find(nums.begin(), nums.end(), 8);
    if (it != nums.end()) {
        std::cout << "找到 8，位置: " << (it - nums.begin()) << std::endl;
    }
    
    // count：计数
    int cnt = std::count(nums.begin(), nums.end(), 5);
    std::cout << "5 的个数: " << cnt << std::endl;
    
    // min_element / max_element
    auto minIt = std::min_element(nums.begin(), nums.end());
    auto maxIt = std::max_element(nums.begin(), nums.end());
    std::cout << "最小值: " << *minIt << ", 最大值: " << *maxIt << std::endl;
    
    // accumulate：累加（在 <numeric> 中）
    int sum = std::accumulate(nums.begin(), nums.end(), 0);
    std::cout << "总和: " << sum << std::endl;
    double avg = (double)sum / nums.size();
    std::cout << "平均值: " << avg << std::endl;
    
    // all_of / any_of / none_of（C++11）
    bool allPositive = std::all_of(nums.begin(), nums.end(), [](int x) { return x > 0; });
    bool anyGreaterThan8 = std::any_of(nums.begin(), nums.end(), [](int x) { return x > 8; });
    std::cout << "全部为正: " << std::boolalpha << allPositive << std::endl;
    std::cout << "存在大于8: " << anyGreaterThan8 << std::endl;
    
    // ===== 2. 排序算法 =====
    std::cout << "\n===== 2. 排序算法 =====" << std::endl;
    
    // sort：全排序（默认升序）
    std::sort(nums.begin(), nums.end());
    std::cout << "升序: ";
    for (int v : nums) std::cout << v << " ";
    std::cout << std::endl;
    
    // 降序
    std::sort(nums.begin(), nums.end(), std::greater<int>());
    std::cout << "降序: ";
    for (int v : nums) std::cout << v << " ";
    std::cout << std::endl;
    
    // 自定义排序规则
    std::sort(nums.begin(), nums.end(), [](int a, int b) {
        return a % 3 < b % 3;  // 按模 3 排序
    });
    std::cout << "按模3排序: ";
    for (int v : nums) std::cout << v << "(" << v%3 << ") ";
    std::cout << std::endl;
    
    // partial_sort：部分排序（前 N 个有序）
    std::vector<int> nums3 = {5, 2, 8, 1, 9, 3, 7, 4, 6};
    std::partial_sort(nums3.begin(), nums3.begin() + 3, nums3.end());
    std::cout << "前3个最小: ";
    for (int i = 0; i < 3; i++) std::cout << nums3[i] << " ";
    std::cout << std::endl;
    
    // nth_element：第 N 大的元素
    std::vector<int> nums4 = {5, 2, 8, 1, 9, 3, 7, 4, 6};
    std::nth_element(nums4.begin(), nums4.begin() + 4, nums4.end());
    std::cout << "第5小的元素: " << nums4[4] << std::endl;
    
    // ===== 3. 修改式算法 =====
    std::cout << "\n===== 3. 修改式算法 =====" << std::endl;
    
    // reverse：反转
    std::vector<int> v1 = {1, 2, 3, 4, 5};
    std::reverse(v1.begin(), v1.end());
    std::cout << "反转: ";
    for (int v : v1) std::cout << v << " ";
    std::cout << std::endl;
    
    // transform：变换
    std::vector<int> v2 = {1, 2, 3, 4, 5};
    std::vector<int> v2_squared(v2.size());
    std::transform(v2.begin(), v2.end(), v2_squared.begin(),
                   [](int x) { return x * x; });
    std::cout << "平方: ";
    for (int v : v2_squared) std::cout << v << " ";
    std::cout << std::endl;
    
    // copy / copy_if
    std::vector<int> v3 = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    std::vector<int> evens;
    std::copy_if(v3.begin(), v3.end(), std::back_inserter(evens),
                 [](int x) { return x % 2 == 0; });
    std::cout << "偶数: ";
    for (int v : evens) std::cout << v << " ";
    std::cout << std::endl;
    
    // remove + erase：删除元素（erase-remove 惯用法）
    std::vector<int> v4 = {1, 2, 3, 2, 4, 2, 5};
    v4.erase(std::remove(v4.begin(), v4.end(), 2), v4.end());
    std::cout << "删除所有2: ";
    for (int v : v4) std::cout << v << " ";
    std::cout << std::endl;
    
    // unique：去重（需先排序）
    std::vector<int> v5 = {1, 1, 2, 2, 2, 3, 4, 4, 5};
    v5.erase(std::unique(v5.begin(), v5.end()), v5.end());
    std::cout << "去重: ";
    for (int v : v5) std::cout << v << " ";
    std::cout << std::endl;
    
    // ===== 4. 二分查找（需有序） =====
    std::cout << "\n===== 4. 二分查找 =====" << std::endl;
    std::vector<int> sorted = {1, 3, 5, 7, 9, 11, 13, 15};
    
    bool found = std::binary_search(sorted.begin(), sorted.end(), 7);
    std::cout << "7 存在: " << found << std::endl;
    
    auto lower = std::lower_bound(sorted.begin(), sorted.end(), 7);  // 第一个 >= 7
    auto upper = std::upper_bound(sorted.begin(), sorted.end(), 7);  // 第一个 > 7
    std::cout << "7 的范围: [" << (lower - sorted.begin()) 
              << ", " << (upper - sorted.begin()) << ")" << std::endl;
    
    // ===== 5. 迭代器类型演示 =====
    std::cout << "\n===== 5. 迭代器类型 =====" << std::endl;
    
    // 随机访问迭代器（vector）
    std::vector<int>::iterator vit = nums2.begin();
    std::cout << "vector 迭代器随机访问: " << *(vit + 3) << std::endl;
    
    // 双向迭代器（list）
    std::list<int> lst = {1, 2, 3, 4, 5};
    auto lit = lst.begin();
    ++lit;  // 前进
    --lit;  // 后退
    // lit + 3;  // 错误：list 迭代器不支持随机访问
    std::cout << "list 迭代器: " << *lit << std::endl;
    
    // 插入迭代器
    std::vector<int> dest;
    std::fill_n(std::back_inserter(dest), 5, 42);  // 插入 5 个 42
    std::cout << "back_inserter 填充: ";
    for (int v : dest) std::cout << v << " ";
    std::cout << std::endl;
    
    return 0;
}
```

#### 总结

- 迭代器是容器与算法的桥梁，分为输入、输出、前向、双向、随机访问五类。
- 不同容器支持不同迭代器：`vector`/`deque` 支持随机访问，`list` 仅支持双向。
- 算法通过 `[first, last)` 迭代器范围指定操作区间，左闭右开。
- `sort` 需要随机访问迭代器，`list` 不能用 `std::sort`，需用成员函数 `list::sort`。
- `remove` 不真正删除元素，需配合 `erase`（erase-remove 惯用法）。
- `unique` 去重前需先排序，且也需配合 `erase`。
- 二分查找算法（`lower_bound`、`upper_bound`、`binary_search`）要求容器有序。
- 注意迭代器失效：`vector` 扩容后所有迭代器失效，`insert`/`erase` 后相关迭代器失效。

---

### 第21讲 函数对象与 Lambda

#### 概念

**函数对象**（Functor）是重载了 `operator()` 的类对象，可以像函数一样调用，但能携带状态。**Lambda 表达式**（C++11）是匿名函数的语法糖，可以就地定义函数，捕获周围作用域的变量。**`std::function`** 是通用的函数包装器，能存储任意可调用对象（函数指针、函数对象、Lambda、成员函数指针）。**`std::bind`** 绑定参数，创建新的可调用对象。

#### 原理

函数对象相比普通函数的优势在于能携带状态（成员变量）和可被 STL 算法内联优化。STL 内置了许多函数对象：`std::greater`、`std::less`、`std::plus`、`std::minus` 等（在 `<functional>` 中）。

Lambda 表达式的本质是编译器生成的匿名类（重载 `operator()`）。捕获列表 `[]` 决定如何捕获外部变量：`[]` 不捕获、`[=]` 值捕获所有、`[&]` 引用捕获所有、`[x]` 值捕获 x、`[&x]` 引用捕获 x、`[this]` 捕获 this 指针。`mutable` 关键字允许修改值捕获的变量（不影响原始变量）。Lambda 的类型唯一且由编译器决定，通常用 `auto` 或 `std::function` 存储。

`std::function` 是类型擦除的函数包装器，能存储任意签名的可调用对象，但有运行时开销。`std::bind` 通过占位符（`std::placeholders::_1`、`_2`）绑定部分参数，创建偏函数。

#### 例子

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <functional>
#include <string>
#include <map>

// ===== 1. 函数对象（Functor） =====
class Multiplier {
private:
    int factor;
public:
    Multiplier(int f) : factor(f) {}
    
    // 重载 operator()
    int operator()(int x) const {
        return x * factor;
    }
};

// 带状态的函数对象
class Accumulator {
private:
    int sum;
    int count;
public:
    Accumulator() : sum(0), count(0) {}
    
    void operator()(int x) {
        sum += x;
        count++;
    }
    
    int getSum() const { return sum; }
    double getAverage() const {
        return count > 0 ? (double)sum / count : 0;
    }
};

// ===== 2. 比较器函数对象 =====
class CompareByLength {
public:
    bool operator()(const std::string& a, const std::string& b) const {
        if (a.length() != b.length()) return a.length() < b.length();
        return a < b;  // 长度相同按字典序
    }
};

int main() {
    std::vector<int> nums = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    
    // ===== 1. 函数对象使用 =====
    std::cout << "===== 1. 函数对象 =====" << std::endl;
    Multiplier triple(3);
    std::cout << "triple(5) = " << triple(5) << std::endl;  // 15
    std::cout << "triple(10) = " << triple(10) << std::endl; // 30
    
    // 在算法中使用
    std::vector<int> result(nums.size());
    std::transform(nums.begin(), nums.end(), result.begin(), Multiplier(2));
    std::cout << "乘以2: ";
    for (int v : result) std::cout << v << " ";
    std::cout << std::endl;
    
    // 带状态的函数对象
    Accumulator acc;
    acc = std::for_each(nums.begin(), nums.end(), acc);  // for_each 返回函数对象
    std::cout << "总和: " << acc.getSum() 
              << ", 平均: " << acc.getAverage() << std::endl;
    
    // ===== 2. Lambda 表达式 =====
    std::cout << "\n===== 2. Lambda 表达式 =====" << std::endl;
    
    // 基本 Lambda
    auto add = [](int a, int b) { return a + b; };
    std::cout << "add(3, 4) = " << add(3, 4) << std::endl;
    
    // 值捕获
    int x = 10;
    auto captureByValue = [x](int y) { return x + y; };
    std::cout << "值捕获: " << captureByValue(5) << std::endl;  // 15
    
    // 引用捕获
    int counter = 0;
    auto increment = [&counter]() { counter++; };
    increment();
    increment();
    increment();
    std::cout << "引用捕获后 counter = " << counter << std::endl;  // 3
    
    // 混合捕获
    int a = 1, b = 2, c = 3;
    auto mixed = [a, &b, &c](int d) {
        b = a + d;   // b 是引用，可修改
        c = b + 10;  // c 是引用，可修改
        return a + b + c + d;
    };
    std::cout << "混合捕获结果: " << mixed(100) << std::endl;
    std::cout << "a=" << a << ", b=" << b << ", c=" << c << std::endl;
    
    // mutable Lambda
    int n = 0;
    auto mutableLambda = [n](int x) mutable {
        n += x;  // 修改的是副本，不影响外部 n
        return n;
    };
    std::cout << "mutable(5): " << mutableLambda(5) << std::endl;   // 5
    std::cout << "mutable(10): " << mutableLambda(10) << std::endl; // 15
    std::cout << "外部 n = " << n << std::endl;  // 0（未改变）
    
    // ===== 3. Lambda 在算法中的应用 =====
    std::cout << "\n===== 3. Lambda 在算法中 =====" << std::endl;
    
    // 排序：降序
    std::vector<int> v1 = {5, 2, 8, 1, 9, 3};
    std::sort(v1.begin(), v1.end(), [](int a, int b) { return a > b; });
    std::cout << "降序: ";
    for (int v : v1) std::cout << v << " ";
    std::cout << std::endl;
    
    // 按字符串长度排序
    std::vector<std::string> words = {"banana", "apple", "cherry", "date", "fig"};
    std::sort(words.begin(), words.end(), [](const std::string& a, const std::string& b) {
        return a.length() < b.length();
    });
    std::cout << "按长度排序: ";
    for (const auto& w : words) std::cout << w << " ";
    std::cout << std::endl;
    
    // 使用比较器函数对象
    std::sort(words.begin(), words.end(), CompareByLength());
    std::cout << "用 Functor 排序: ";
    for (const auto& w : words) std::cout << w << " ";
    std::cout << std::endl;
    
    // find_if：查找第一个满足条件的
    std::vector<int> v2 = {1, 4, 9, 16, 25, 36};
    auto it = std::find_if(v2.begin(), v2.end(), [](int x) { return x > 20; });
    if (it != v2.end()) {
        std::cout << "第一个大于20的: " << *it << std::endl;  // 25
    }
    
    // count_if：计数
    int evenCount = std::count_if(nums.begin(), nums.end(), 
                                   [](int x) { return x % 2 == 0; });
    std::cout << "偶数个数: " << evenCount << std::endl;
    
    // ===== 4. std::function =====
    std::cout << "\n===== 4. std::function =====" << std::endl;
    
    // 存储不同类型的可调用对象
    std::function<int(int, int)> operation;
    
    // 存储 Lambda
    operation = [](int a, int b) { return a + b; };
    std::cout << "加法: " << operation(3, 4) << std::endl;
    
    // 存储函数对象
    struct Subtract {
        int operator()(int a, int b) const { return a - b; }
    };
    operation = Subtract{};
    std::cout << "减法: " << operation(10, 3) << std::endl;
    
    // 存储函数指针
    operation = std::multiplies<int>();  // STL 内置函数对象
    std::cout << "乘法: " << operation(5, 6) << std::endl;
    
    // ===== 5. std::bind =====
    std::cout << "\n===== 5. std::bind =====" << std::endl;
    using namespace std::placeholders;
    
    auto divide = [](int a, int b) { return b != 0 ? (double)a / b : 0; };
    
    // 绑定第一个参数为 10
    auto divideBy = std::bind(divide, 10, _1);
    std::cout << "10/2 = " << divideBy(2) << std::endl;
    std::cout << "10/5 = " << divideBy(5) << std::endl;
    
    // 绑定第二个参数为 2
    auto half = std::bind(divide, _1, 2);
    std::cout << "10/2 = " << half(10) << std::endl;
    std::cout << "20/2 = " << half(20) << std::endl;
    
    // ===== 6. 回调函数示例 =====
    std::cout << "\n===== 6. 回调函数 =====" << std::endl;
    
    // 事件处理模拟
    std::map<std::string, std::function<void(int)>> handlers;
    
    handlers["onClick"] = [](int id) {
        std::cout << "点击事件，ID: " << id << std::endl;
    };
    handlers["onHover"] = [](int id) {
        std::cout << "悬停事件，ID: " << id << std::endl;
    };
    
    // 触发事件
    handlers["onClick"](101);
    handlers["onHover"](202);
    
    return 0;
}
```

#### 总结

- 函数对象是重载 `operator()` 的类，能像函数调用且可携带状态，适合 STL 算法。
- Lambda 表达式是匿名函数的语法糖，本质是编译器生成的匿名类。
- 捕获列表 `[=]` 值捕获、`[&]` 引用捕获、`[x]`/`[&x]` 指定捕获，`mutable` 允许修改值捕获的副本。
- 引用捕获需注意生命周期：被捕获的局部变量在 Lambda 使用时必须仍然有效。
- `std::function` 是通用函数包装器，能存储任意可调用对象，但有运行时开销。
- `std::bind` 绑定参数创建偏函数，用占位符 `_1`、`_2` 表示未绑定的参数。
- 优先使用 Lambda 而非函数对象和 `std::bind`，代码更简洁可读。
- `std::function` 适合需要存储和传递可调用对象的场景（如回调、事件处理）。

---

## 第8章 现代 C++ 与实战

本章介绍现代 C++（C++11 及以后）的重要特性，包括类型推导、新标准特性，以及一个综合实战项目，帮助你将所学知识融会贯通。

### 第22讲 类型推导与 auto/decltype

#### 概念

**类型推导**让编译器自动推断变量、函数返回值和模板参数的类型，减少冗余类型声明，提高代码可读性和可维护性。C++11 引入 `auto` 和 `decltype`，C++14 扩展了 `auto` 的用途（函数返回类型推导），C++20 引入 `consteval` 和概念。`auto` 根据初始化表达式推导类型，`decltype` 推导表达式的类型（保留引用和 const）。

#### 原理

`auto` 的推导规则与模板参数推导类似：`auto` 相当于模板参数 `T`，初始化表达式相当于实参。`auto` 会忽略顶层的 `const` 和引用，需手动添加（`const auto&`）。`decltype(expr)` 推导 `expr` 的精确类型，保留 `const` 和引用：`decltype(variable)` 是变量的声明类型，`decltype((variable))`（带括号）总是引用类型。

`auto` 的常见用途：迭代器声明（`auto it = map.begin()`）、范围 for 循环、Lambda 参数（C++14）、函数返回类型（C++14 `auto func()`）。`decltype` 常用于模板元编程、`typedef`/`using` 别名、后置返回类型（`auto func() -> decltype(...)`）。

`auto` 的注意事项：避免用于基本类型（`int` 比 `auto` 更清晰），避免导致意外类型转换（如 `auto x = {1, 2, 3}` 推导为 `std::initializer_list<int>`），始终用 `const auto&` 遍历容器避免拷贝。

#### 例子

```cpp
#include <iostream>
#include <vector>
#include <map>
#include <string>
#include <type_traits>
#include <typeinfo>

int main() {
    // ===== 1. auto 基本用法 =====
    std::cout << "===== 1. auto 基本用法 =====" << std::endl;
    
    auto a = 42;              // int
    auto b = 3.14;            // double
    auto c = 'A';             // char
    auto d = "Hello";         // const char*
    auto e = std::string("World");  // std::string
    auto f = true;            // bool
    
    std::cout << "a = " << a << " (int)" << std::endl;
    std::cout << "b = " << b << " (double)" << std::endl;
    std::cout << "e = " << e << " (string)" << std::endl;
    
    // ===== 2. auto 与 const/引用 =====
    std::cout << "\n===== 2. auto 与 const/引用 =====" << std::endl;
    int x = 10;
    const int& rx = x;
    
    auto a1 = rx;        // auto 推导为 int（忽略 const 和 &）
    const auto& a2 = rx; // auto 推导为 int，整体是 const int&
    auto& a3 = rx;       // auto 推导为 const int，a3 是 const int&
    
    a1 = 20;  // OK：a1 是 int
    // a2 = 20;  // 错误：a2 是 const int&
    // a3 = 20;  // 错误：a3 是 const int&
    
    std::cout << "a1 = " << a1 << std::endl;
    
    // ===== 3. auto 与容器 =====
    std::cout << "\n===== 3. auto 与容器 =====" << std::endl;
    std::map<std::string, std::vector<int>> data = {
        {"A", {1, 2, 3}},
        {"B", {4, 5, 6}}
    };
    
    // 不用 auto：冗长
    // std::map<std::string, std::vector<int>>::iterator it = data.begin();
    
    // 用 auto：简洁
    for (auto it = data.begin(); it != data.end(); ++it) {
        std::cout << it->first << ": ";
        for (auto val : it->second) {  // auto 推导 int
            std::cout << val << " ";
        }
        std::cout << std::endl;
    }
    
    // 范围 for 循环 + auto
    for (const auto& [key, values] : data) {  // C++17 结构化绑定
        std::cout << key << " 有 " << values.size() << " 个元素" << std::endl;
    }
    
    // ===== 4. decltype =====
    std::cout << "\n===== 4. decltype =====" << std::endl;
    
    int num = 42;
    decltype(num) num2 = 100;     // int
    decltype((num)) num3 = num;   // int&（带括号，总是引用）
    
    num2 = 200;
    num3 = 300;  // 通过引用修改 num
    std::cout << "num = " << num << ", num2 = " << num2 << std::endl;
    
    auto add = [](int a, int b) { return a + b; };
    decltype(add) add2 = add;  // 同类型的 Lambda
    std::cout << "add2(3, 4) = " << add2(3, 4) << std::endl;
    
    // ===== 5. decltype 用于模板 =====
    std::cout << "\n===== 5. decltype 用于模板 =====" << std::endl;
    
    // 后置返回类型（C++11）
    auto multiply(int a, int b) -> decltype(a * b) {
        return a * b;
    }
    
    std::cout << "multiply(3, 4) = " << multiply(3, 4) << std::endl;
    
    // C++14：auto 返回类型推导
    auto getVector() {
        return std::vector<int>{1, 2, 3, 4, 5};
    }
    
    auto vec = getVector();
    std::cout << "返回的 vector 大小: " << vec.size() << std::endl;
    
    // ===== 6. using 类型别名（替代 typedef） =====
    std::cout << "\n===== 6. using 类型别名 =====" << std::endl;
    
    using StringVector = std::vector<std::string>;
    using IntMap = std::map<int, std::string>;
    
    StringVector names = {"Alice", "Bob", "Charlie"};
    IntMap idToName = {{1, "Alice"}, {2, "Bob"}};
    
    std::cout << "名字: ";
    for (const auto& name : names) std::cout << name << " ";
    std::cout << std::endl;
    
    // 模板别名（typedef 做不到）
    template <typename T>
    using Vec = std::vector<T>;
    
    Vec<int> intVec = {1, 2, 3};
    Vec<double> doubleVec = {1.1, 2.2, 3.3};
    
    std::cout << "Vec<int> 大小: " << intVec.size() << std::endl;
    
    // ===== 7. auto 注意事项 =====
    std::cout << "\n===== 7. auto 注意事项 =====" << std::endl;
    
    // 意外的类型推导
    std::vector<bool> boolVec = {true, false, true};
    auto val = boolVec[0];  // auto 推导为 std::vector<bool>::reference，不是 bool！
    // bool b = boolVec[0];  // 显式类型更安全
    
    // initializer_list
    auto il = {1, 2, 3};  // std::initializer_list<int>
    std::cout << "initializer_list 大小: " << il.size() << std::endl;
    
    // auto&& 万能引用
    auto&& ref1 = 42;     // int&&（右值引用）
    int temp = 100;
    auto&& ref2 = temp;   // int&（左值引用）
    
    return 0;
}
```

#### 总结

- `auto` 根据初始化表达式推导类型，忽略顶层 `const` 和引用，需手动添加。
- `decltype(expr)` 推导表达式的精确类型，保留 `const` 和引用；`decltype((x))` 总是引用。
- `auto` 适合复杂类型（迭代器、模板类型），基本类型用显式声明更清晰。
- 遍历容器用 `const auto&` 避免拷贝，需要修改用 `auto&`。
- C++14 支持 `auto` 函数返回类型推导，C++14 Lambda 参数可用 `auto`。
- `using` 别名（C++11）比 `typedef` 更清晰，且支持模板别名。
- 注意 `auto` 的陷阱：`vector<bool>` 的 `operator[]` 返回代理类型，`auto x = {1,2,3}` 推导为 `initializer_list`。
- `auto&&` 是万能引用，根据初始化值推导为左值引用或右值引用。

---

### 第23讲 C++11/14/17/20 新特性

#### 概念

现代 C++（C++11 及以后）引入了大量改进，使 C++ 更安全、更简洁、更高效。本讲梳理各版本的核心特性：C++11（auto、Lambda、智能指针、右值引用、`nullptr`、`constexpr`、范围 for、初始化列表、强类型枚举）；C++14（函数返回类型推导、泛型 Lambda、`std::make_unique`）；C++17（结构化绑定、`if constexpr`、`std::optional`、`std::variant`、`std::any`、文件系统库）；C++20（概念、协程、模块、范围库、`std::format`）。

#### 原理

每个新标准都在解决前版本的痛点。C++11 是革命性更新，使 C++ 从"带类的 C"转变为现代语言。智能指针解决内存管理问题，Lambda 简化回调代码，右值引用提升性能。C++14 是 C++11 的完善，修复了一些遗漏。C++17 引入结构化绑定和 `if constexpr`，大幅简化模板代码。C++20 是又一次重大更新，概念使模板约束更直观，协程简化异步代码，模块替代头文件加速编译。

新特性并非互斥，而是渐进增强。现代 C++ 鼓励"用更少的代码做更多的事"，同时保持零开销抽象原则——你不为你不使用的东西付出代价，你使用的东西比你手写的更好。

#### 例子

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <map>
#include <memory>
#include <optional>
#include <variant>
#include <any>
#include <tuple>
#include <filesystem>
#include <algorithm>
#include <numeric>

// ===== C++11 特性 =====

// 1. nullptr 替代 NULL
void process(int* p) { std::cout << "指针版本" << std::endl; }
void process(int n) { std::cout << "整数版本" << std::endl; }

// 2. 强类型枚举
enum class Color { Red, Green, Blue };
enum class TrafficLight { Red, Yellow, Green };

// 3. constexpr 编译期计算
constexpr int factorial(int n) {
    return n <= 1 ? 1 : n * factorial(n - 1);
}

// 4. 委托构造
class Widget {
    int a, b;
public:
    Widget(int x, int y) : a(x), b(y) {}
    Widget() : Widget(0, 0) {}  // 委托给另一个构造函数
    Widget(int x) : Widget(x, 0) {}
};

// 5. final 和 override
class Base {
public:
    virtual void foo() final {}  // 不可重写
    virtual void bar() {}
    virtual ~Base() = default;
};

class Derived : public Base {
public:
    void bar() override {}  // 显式重写
};

// ===== C++14 特性 =====

// 1. 函数返回类型推导
auto add(int a, int b) {
    return a + b;  // 编译器推导返回类型为 int
}

// 2. 泛型 Lambda
auto genericLambda = [](auto a, auto b) { return a + b; };

// 3. constexpr 扩展（C++14 可使用循环等）
constexpr int sumUp(int n) {
    int s = 0;
    for (int i = 1; i <= n; i++) s += i;
    return s;
}

// ===== C++17 特性 =====

// 1. 结构化绑定
std::tuple<int, std::string, double> getPerson() {
    return {25, "Alice", 85.5};
}

// 2. if constexpr
template <typename T>
void processValue(T value) {
    if constexpr (std::is_integral_v<T>) {
        std::cout << "整数: " << value << std::endl;
    } else if constexpr (std::is_floating_point_v<T>) {
        std::cout << "浮点: " << value << std::endl;
    } else {
        std::cout << "其他: " << value << std::endl;
    }
}

// 3. std::optional
std::optional<int> findEven(const std::vector<int>& vec) {
    for (int v : vec) {
        if (v % 2 == 0) return v;
    }
    return std::nullopt;  // 没找到
}

// 4. std::variant（类型安全的联合体）
using Variant = std::variant<int, double, std::string>;

// 5. fold expressions（折叠表达式）
template <typename... Args>
auto sumAll(Args... args) {
    return (args + ...);  // C++17 折叠表达式
}

// ===== C++20 特性（如果支持） =====
#if __cplusplus >= 202002L
// 1. 概念
template <typename T>
concept Numeric = std::is_integral_v<T> || std::is_floating_point_v<T>;

template <Numeric T>
T maxValue(T a, T b) {
    return a > b ? a : b;
}

// 2. 范围 for with init
// 3. 三路比较 <=>
#endif

int main() {
    // ===== C++11 =====
    std::cout << "===== C++11 特性 =====" << std::endl;
    
    // nullptr
    process(nullptr);  // 调用指针版本
    
    // 强类型枚举
    Color c = Color::Red;
    // if (c == 0) {}  // 错误：不能隐式转换为 int
    if (c == Color::Red) { std::cout << "红色" << std::endl; }
    
    // constexpr
    constexpr int fact5 = factorial(5);  // 编译期计算
    std::cout << "5! = " << fact5 << std::endl;
    
    // 初始化列表
    std::vector<int> v = {1, 2, 3, 4, 5};
    std::map<std::string, int> m = {{"a", 1}, {"b", 2}};
    
    // 范围 for 循环
    std::cout << "范围 for: ";
    for (int x : v) std::cout << x << " ";
    std::cout << std::endl;
    
    // 智能指针
    auto ptr = std::make_shared<int>(42);
    std::cout << "shared_ptr: " << *ptr << std::endl;
    
    // Lambda
    auto square = [](int x) { return x * x; };
    std::cout << "square(5) = " << square(5) << std::endl;
    
    // ===== C++14 =====
    std::cout << "\n===== C++14 特性 =====" << std::endl;
    
    std::cout << "add(3, 4) = " << add(3, 4) << std::endl;
    std::cout << "泛型 Lambda: " << genericLambda(1, 2) << std::endl;
    std::cout << "泛型 Lambda: " << genericLambda(1.5, 2.5) << std::endl;
    std::cout << "泛型 Lambda: " << genericLambda(std::string("Hello"), std::string(" World")) << std::endl;
    
    constexpr int s = sumUp(100);  // 编译期循环
    std::cout << "sumUp(100) = " << s << std::endl;
    
    auto uptr = std::make_unique<int>(99);  // C++14
    std::cout << "unique_ptr: " << *uptr << std::endl;
    
    // ===== C++17 =====
    std::cout << "\n===== C++17 特性 =====" << std::endl;
    
    // 结构化绑定
    auto [age, name, score] = getPerson();
    std::cout << "结构化绑定: " << name << ", " << age << ", " << score << std::endl;
    
    // 结构化绑定遍历 map
    std::map<std::string, int> scores = {{"Alice", 90}, {"Bob", 85}};
    for (const auto& [n, s] : scores) {
        std::cout << "  " << n << ": " << s << std::endl;
    }
    
    // if constexpr
    processValue(42);
    processValue(3.14);
    processValue("hello");
    
    // std::optional
    std::vector<int> nums = {1, 3, 5, 7, 8, 9};
    auto even = findEven(nums);
    if (even) {
        std::cout << "找到偶数: " << *even << std::endl;
    } else {
        std::cout << "没有偶数" << std::endl;
    }
    
    // std::variant
    Variant var = 42;
    std::cout << "variant 当前类型: " << var.index() << std::endl;
    var = "Hello";
    std::cout << "variant 当前类型: " << var.index() << std::endl;
    
    // std::visit 访问 variant
    var = 3.14;
    std::visit([](auto&& arg) {
        std::cout << "visit: " << arg << std::endl;
    }, var);
    
    // 折叠表达式
    std::cout << "sumAll(1,2,3,4,5) = " << sumAll(1, 2, 3, 4, 5) << std::endl;
    
    // if with initializer
    if (auto it = scores.find("Alice"); it != scores.end()) {
        std::cout << "找到 Alice: " << it->second << std::endl;
    }
    
    // 文件系统库
    namespace fs = std::filesystem;
    std::cout << "当前路径: " << fs::current_path() << std::endl;
    
    // ===== C++20 =====
#if __cplusplus >= 202002L
    std::cout << "\n===== C++20 特性 =====" << std::endl;
    
    std::cout << "maxValue(3, 7) = " << maxValue(3, 7) << std::endl;
    std::cout << "maxValue(3.14, 2.72) = " << maxValue(3.14, 2.72) << std::endl;
    // maxValue("a", "b");  // 编译错误：不满足 Numeric 概念
    
    // 三路比较
    auto cmp = (3 <=> 5);  // std::strong_ordering::less
    std::cout << "3 <=> 5: " << (cmp < 0 ? "小于" : "大于等于") << std::endl;
#endif
    
    return 0;
}
```

#### 总结

- C++11 是革命性更新：`auto`、Lambda、智能指针、右值引用、`nullptr`、`constexpr`、范围 for、初始化列表、强类型枚举。
- C++14 完善 C++11：函数返回类型推导、泛型 Lambda、`std::make_unique`、扩展的 `constexpr`。
- C++17 实用特性：结构化绑定、`if constexpr`、`std::optional`/`variant`/`any`、折叠表达式、文件系统库。
- C++20 重大更新：概念（Concepts）、协程、模块、范围库（Ranges）、`<=>` 三路比较、`std::format`。
- 现代 C++ 原则：用智能指针替代裸指针，用 Lambda 替代函数对象，用 `auto` 减少冗余，用 `constexpr` 将计算移到编译期。
- 编译时用 `-std=c++17` 或 `-std=c++20` 启用新标准，`__cplusplus` 宏判断标准版本。
- 新特性并非必须全部使用，根据项目需求和团队情况选择合适的特性子集。
- 持续关注新标准，但优先掌握 C++11/14/17，它们已被广泛支持且足够强大。

---

### 第24讲 综合实战项目

#### 概念

本讲通过一个综合项目——**学生成绩管理系统**——将前面所学的知识融会贯通。项目涵盖面向对象设计、STL 容器与算法、智能指针、异常处理、文件 I/O、模板等核心技术。我们将实现学生信息的增删改查、成绩统计、数据持久化等功能，展示如何用现代 C++ 构建一个完整的应用程序。

#### 原理

良好的软件设计遵循**单一职责原则**（每个类只做一件事）、**开闭原则**（对扩展开放，对修改关闭）和**RAII 原则**（资源获取即初始化）。本项目分为三层：**数据层**（`Student` 类表示学生实体）、**业务层**（`GradeManager` 类管理学生集合和业务逻辑）、**表示层**（`main` 函数处理用户交互）。

使用 `std::map` 存储学生（以学号为键，便于快速查找），`std::shared_ptr<Student>` 管理学生对象生命周期。文件 I/O 使用 `std::ofstream`/`std::ifstream` 实现数据持久化。异常处理保证程序健壮性，输入验证防止非法数据。算法（`std::sort`、`std::accumulate`、`std::find_if`）处理统计和查询。

#### 例子

```cpp
#include <iostream>
#include <string>
#include <map>
#include <memory>
#include <vector>
#include <algorithm>
#include <numeric>
#include <fstream>
#include <iomanip>
#include <stdexcept>
#include <limits>

// ===== 数据层：Student 类 =====
class Student {
private:
    std::string id;          // 学号
    std::string name;        // 姓名
    std::vector<double> scores;  // 成绩列表
    
public:
    Student(const std::string& id, const std::string& name)
        : id(id), name(name) {}
    
    // 添加成绩
    void addScore(double score) {
        if (score < 0 || score > 100) {
            throw std::invalid_argument("成绩必须在 0-100 之间");
        }
        scores.push_back(score);
    }
    
    // 计算平均分
    double getAverage() const {
        if (scores.empty()) return 0.0;
        return std::accumulate(scores.begin(), scores.end(), 0.0) / scores.size();
    }
    
    // 计算最高分
    double getMax() const {
        if (scores.empty()) return 0.0;
        return *std::max_element(scores.begin(), scores.end());
    }
    
    // 计算最低分
    double getMin() const {
        if (scores.empty()) return 0.0;
        return *std::min_element(scores.begin(), scores.end());
    }
    
    // 是否及格（平均分 >= 60）
    bool isPass() const { return getAverage() >= 60.0; }
    
    // 序列化到文件
    void serialize(std::ofstream& out) const {
        out << id << " " << name << " " << scores.size();
        for (double s : scores) out << " " << s;
        out << "\n";
    }
    
    // 从文件反序列化
    static std::shared_ptr<Student> deserialize(std::ifstream& in) {
        std::string id, name;
        size_t count;
        if (!(in >> id >> name >> count)) return nullptr;
        
        auto student = std::make_shared<Student>(id, name);
        for (size_t i = 0; i < count; i++) {
            double score;
            in >> score;
            student->scores.push_back(score);
        }
        return student;
    }
    
    // 打印信息
    void display() const {
        std::cout << std::left << std::setw(10) << id 
                  << std::setw(15) << name
                  << std::setw(8) << scores.size()
                  << std::fixed << std::setprecision(2)
                  << std::setw(10) << getAverage()
                  << std::setw(8) << getMax()
                  << std::setw(8) << getMin()
                  << (isPass() ? "及格" : "不及格") << std::endl;
    }
    
    // Getter
    const std::string& getId() const { return id; }
    const std::string& getName() const { return name; }
    const std::vector<double>& getScores() const { return scores; }
};

// ===== 业务层：GradeManager 类 =====
class GradeManager {
private:
    std::map<std::string, std::shared_ptr<Student>> students;
    std::string dataFile;
    
public:
    GradeManager(const std::string& file = "students.dat") : dataFile(file) {
        loadFromFile();  // 启动时加载数据
    }
    
    ~GradeManager() {
        saveToFile();  // 退出时保存数据
    }
    
    // 添加学生
    void addStudent(const std::string& id, const std::string& name) {
        if (students.find(id) != students.end()) {
            throw std::invalid_argument("学号 " + id + " 已存在");
        }
        students[id] = std::make_shared<Student>(id, name);
        std::cout << "成功添加学生: " << name << " (" << id << ")" << std::endl;
    }
    
    // 删除学生
    void removeStudent(const std::string& id) {
        auto it = students.find(id);
        if (it == students.end()) {
            throw std::invalid_argument("学号 " + id + " 不存在");
        }
        std::cout << "删除学生: " << it->second->getName() << std::endl;
        students.erase(it);
    }
    
    // 添加成绩
    void addScore(const std::string& id, double score) {
        auto it = students.find(id);
        if (it == students.end()) {
            throw std::invalid_argument("学号 " + id + " 不存在");
        }
        it->second->addScore(score);
        std::cout << "已为 " << it->second->getName() 
                  << " 添加成绩: " << score << std::endl;
    }
    
    // 查询学生
    std::shared_ptr<Student> findStudent(const std::string& id) const {
        auto it = students.find(id);
        if (it == students.end()) return nullptr;
        return it->second;
    }
    
    // 显示所有学生
    void displayAll() const {
        if (students.empty()) {
            std::cout << "暂无学生数据" << std::endl;
            return;
        }
        std::cout << "\n" << std::string(70, '-') << std::endl;
        std::cout << std::left << std::setw(10) << "学号"
                  << std::setw(15) << "姓名"
                  << std::setw(8) << "科目数"
                  << std::setw(10) << "平均分"
                  << std::setw(8) << "最高分"
                  << std::setw(8) << "最低分"
                  << "状态" << std::endl;
        std::cout << std::string(70, '-') << std::endl;
        
        for (const auto& [id, student] : students) {
            student->display();
        }
        std::cout << std::string(70, '-') << std::endl;
        std::cout << "共 " << students.size() << " 名学生" << std::endl;
    }
    
    // 按平均分排序显示
    void displaySortedByAverage() const {
        std::vector<std::shared_ptr<Student>> vec;
        for (const auto& [id, student] : students) {
            vec.push_back(student);
        }
        
        std::sort(vec.begin(), vec.end(), 
            [](const std::shared_ptr<Student>& a, const std::shared_ptr<Student>& b) {
                return a->getAverage() > b->getAverage();  // 降序
            });
        
        std::cout << "\n===== 成绩排名 =====" << std::endl;
        int rank = 1;
        for (const auto& student : vec) {
            std::cout << "第" << rank++ << "名: " 
                      << student->getName() << " - 平均分: "
                      << std::fixed << std::setprecision(2) 
                      << student->getAverage() << std::endl;
        }
    }
    
    // 统计信息
    void showStatistics() const {
        if (students.empty()) {
            std::cout << "暂无数据" << std::endl;
            return;
        }
        
        std::vector<double> averages;
        for (const auto& [id, student] : students) {
            averages.push_back(student->getAverage());
        }
        
        double totalAvg = std::accumulate(averages.begin(), averages.end(), 0.0) / averages.size();
        double maxAvg = *std::max_element(averages.begin(), averages.end());
        double minAvg = *std::min_element(averages.begin(), averages.end());
        
        long passCount = std::count_if(averages.begin(), averages.end(),
            [](double avg) { return avg >= 60.0; });
        
        std::cout << "\n===== 统计信息 =====" << std::endl;
        std::cout << "学生总数: " << students.size() << std::endl;
        std::cout << "平均分: " << std::fixed << std::setprecision(2) << totalAvg << std::endl;
        std::cout << "最高平均分: " << maxAvg << std::endl;
        std::cout << "最低平均分: " << minAvg << std::endl;
        std::cout << "及格人数: " << passCount << "/" << students.size() << std::endl;
        std::cout << "及格率: " << (100.0 * passCount / students.size()) << "%" << std::endl;
    }
    
    // 保存到文件
    void saveToFile() const {
        std::ofstream out(dataFile);
        if (!out) {
            std::cerr << "无法打开文件保存数据: " << dataFile << std::endl;
            return;
        }
        out << students.size() << "\n";
        for (const auto& [id, student] : students) {
            student->serialize(out);
        }
        std::cout << "[数据已保存到 " << dataFile << "]" << std::endl;
    }
    
    // 从文件加载
    void loadFromFile() {
        std::ifstream in(dataFile);
        if (!in) return;  // 文件不存在，首次运行
        
        size_t count;
        in >> count;
        for (size_t i = 0; i < count; i++) {
            auto student = Student::deserialize(in);
            if (student) {
                students[student->getId()] = student;
            }
        }
        std::cout << "[从 " << dataFile << " 加载了 " << students.size() << " 条记录]" << std::endl;
    }
};

// ===== 表示层：用户交互 =====
void printMenu() {
    std::cout << "\n========== 学生成绩管理系统 ==========" << std::endl;
    std::cout << "1. 添加学生" << std::endl;
    std::cout << "2. 删除学生" << std::endl;
    std::cout << "3. 添加成绩" << std::endl;
    std::cout << "4. 查询学生" << std::endl;
    std::cout << "5. 显示所有学生" << std::endl;
    std::cout << "6. 成绩排名" << std::endl;
    std::cout << "7. 统计信息" << std::endl;
    std::cout << "0. 退出" << std::endl;
    std::cout << "======================================" << std::endl;
    std::cout << "请选择操作: ";
}

int getIntInput() {
    int value;
    while (!(std::cin >> value)) {
        std::cin.clear();
        std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
        std::cout << "输入无效，请重新输入: ";
    }
    return value;
}

double getDoubleInput() {
    double value;
    while (!(std::cin >> value)) {
        std::cin.clear();
        std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
        std::cout << "输入无效，请重新输入: ";
    }
    return value;
}

int main() {
    GradeManager manager;
    
    std::cout << "欢迎使用学生成绩管理系统！" << std::endl;
    
    int choice;
    do {
        printMenu();
        choice = getIntInput();
        
        try {
            switch (choice) {
                case 1: {  // 添加学生
                    std::string id, name;
                    std::cout << "请输入学号: ";
                    std::cin >> id;
                    std::cout << "请输入姓名: ";
                    std::cin >> name;
                    manager.addStudent(id, name);
                    break;
                }
                case 2: {  // 删除学生
                    std::string id;
                    std::cout << "请输入要删除的学号: ";
                    std::cin >> id;
                    manager.removeStudent(id);
                    break;
                }
                case 3: {  // 添加成绩
                    std::string id;
                    std::cout << "请输入学号: ";
                    std::cin >> id;
                    std::cout << "请输入成绩(0-100): ";
                    double score = getDoubleInput();
                    manager.addScore(id, score);
                    break;
                }
                case 4: {  // 查询学生
                    std::string id;
                    std::cout << "请输入学号: ";
                    std::cin >> id;
                    auto student = manager.findStudent(id);
                    if (student) {
                        std::cout << "\n查询结果:" << std::endl;
                        student->display();
                    } else {
                        std::cout << "未找到学号 " << id << " 的学生" << std::endl;
                    }
                    break;
                }
                case 5:  // 显示所有
                    manager.displayAll();
                    break;
                case 6:  // 排名
                    manager.displaySortedByAverage();
                    break;
                case 7:  // 统计
                    manager.showStatistics();
                    break;
                case 0:  // 退出
                    std::cout << "感谢使用，再见！" << std::endl;
                    break;
                default:
                    std::cout << "无效选择，请重试" << std::endl;
            }
        } catch (const std::exception& e) {
            std::cout << "错误: " << e.what() << std::endl;
        }
    } while (choice != 0);
    
    return 0;
}
```

**运行示例**：
```
[从 students.dat 加载了 0 条记录]
欢迎使用学生成绩管理系统！

========== 学生成绩管理系统 ==========
1. 添加学生
2. 删除学生
...
请选择操作: 1
请输入学号: 001
请输入姓名: 张三
成功添加学生: 张三 (001)

请选择操作: 3
请输入学号: 001
请输入成绩(0-100): 85
已为 张三 添加成绩: 85

请选择操作: 5
----------------------------------------------------------------------
学号      姓名           科目数  平均分    最高分  最低分  状态
----------------------------------------------------------------------
001       张三           1       85.00    85.00   85.00   及格
----------------------------------------------------------------------
共 1 名学生
```

#### 总结

- 良好的软件设计遵循单一职责、开闭原则和 RAII，将数据层、业务层、表示层分离。
- 面向对象设计：`Student` 封装学生数据和行为，`GradeManager` 管理学生集合和业务逻辑。
- STL 容器：`std::map` 快速查找，`std::vector` 存储成绩列表，`std::sort`/`std::accumulate`/`std::count_if` 处理统计。
- 智能指针：`std::shared_ptr` 管理学生对象，自动释放内存，避免内存泄漏。
- 异常处理：`try-catch` 捕获非法输入和操作错误，保证程序健壮性。
- 文件 I/O：`serialize`/`deserialize` 实现数据持久化，程序重启后数据不丢失。
- 输入验证：检查成绩范围、学号唯一性、输入类型，防止非法数据。
- 这个项目综合运用了本教程的所有核心知识，是检验 C++ 学习成果的良好实践。

---

## 结语

本教程从 C++ 基础语法开始，逐步深入到面向对象编程、继承与多态、模板泛型编程、内存管理与异常处理、STL 标准库，最后介绍了现代 C++ 的新特性并通过综合项目将所学知识融会贯通。

C++ 是一门庞大而复杂的语言，本教程覆盖了其核心内容，但仍有更多值得探索的领域：

- **并发编程**：`std::thread`、`std::mutex`、`std::async`、原子操作
- **网络编程**：Socket 编程、Boost.Asio
- **图形界面**：Qt、ImGui
- **游戏开发**：Unreal Engine、游戏循环、物理引擎
- **嵌入式开发**：底层硬件操作、实时系统
- **性能优化**：缓存友好编程、零开销抽象、内联优化
- **设计模式**：单例、工厂、观察者、策略模式等在 C++ 中的实现

学习 C++ 是一个持续的过程，建议：
1. **多写代码**：实践是最好的老师，将每个知识点都动手实现一遍
2. **阅读优秀源码**：STL 源码、开源项目（如 LLVM、Chromium）
3. **关注新标准**：C++23 已发布，C++26 正在制定中
4. **参与社区**：cppreference.com、Stack Overflow、C++ 中文社区
5. **阅读经典书籍**：《C++ Primer》、《Effective C++》、《The C++ Programming Language》

祝你在 C++ 的学习之路上不断进步，写出优雅、高效、安全的代码！
