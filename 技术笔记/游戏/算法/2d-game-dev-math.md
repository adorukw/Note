# 2D游戏开发中的数学

> 一本面向游戏开发者的系统化数学教程，从向量基础到实战算法，共33讲。

---

## 课程总览

### 学习目标

本课程旨在系统讲解2D游戏开发中所需的全部数学知识。完成本课程后，你将能够：

1. **理解向量与矩阵**：熟练运用向量运算和矩阵变换处理游戏中的位置、方向与运动
2. **实现碰撞检测**：掌握从简单的圆碰撞到复杂的SAT分离轴定理的完整算法体系
3. **构建物理系统**：实现速度、加速度、重力、反弹、摩擦等真实物理效果
4. **制作平滑动画**：运用插值、缓动函数与贝塞尔曲线制作专业级动画
5. **解决实战问题**：实现角色瞄准、导弹追踪、摄像机跟随、程序化生成等游戏核心功能

### 课程结构

本课程共33讲，分为8章，遵循"基础→核心→进阶→实战"的渐进结构：

| 章节 | 标题 | 讲数 | 核心内容 |
|------|------|------|----------|
| 第1章 | 数学基础 | 4讲 | 坐标系、三角函数、向量运算、归一化 |
| 第2章 | 向量进阶 | 4讲 | 点积、叉积、反射、综合应用 |
| 第3章 | 矩阵与变换 | 5讲 | 矩阵运算、平移/缩放/旋转、复合变换 |
| 第4章 | 几何图元 | 3讲 | 点线面、圆、矩形与包围盒 |
| 第5章 | 碰撞检测 | 5讲 | 点碰撞、圆碰撞、AABB、SAT |
| 第6章 | 运动与物理 | 4讲 | 运动方程、抛物运动、反弹、摩擦 |
| 第7章 | 插值与动画 | 4讲 | Lerp、缓动、贝塞尔、角度插值 |
| 第8章 | 实战应用 | 4讲 | 瞄准、追踪、摄像机、程序化生成 |

### 学习方法建议

- **每讲结构**：概念 → 原理 → 例子 → 总结
- **代码示例**：使用Python伪代码风格，便于移植到Unity、Godot、Pygame等任何引擎
- **实践建议**：每学完一讲，尝试在真实游戏项目中应用所学知识
- **复习策略**：向量与矩阵是后续所有章节的基础，务必扎实掌握

### 详细目录

**第1章 数学基础**
- 第1讲：标量、向量与坐标系
- 第2讲：三角函数与角度弧度
- 第3讲：向量基础运算（加减与数乘）
- 第4讲：向量归一化与距离计算

**第2章 向量进阶**
- 第5讲：点积与投影
- 第6讲：叉积与方向判定
- 第7讲：向量反射与镜像
- 第8讲：向量在游戏中的综合应用

**第3章 矩阵与变换**
- 第9讲：矩阵基础与运算
- 第10讲：平移变换
- 第11讲：缩放变换
- 第12讲：旋转变换
- 第13讲：复合变换与齐次坐标

**第4章 几何图元**
- 第14讲：点、线、线段与射线
- 第15讲：圆与球的几何
- 第16讲：矩形与包围盒

**第5章 碰撞检测**
- 第17讲：点与图元碰撞
- 第18讲：圆与圆碰撞
- 第19讲：AABB碰撞检测
- 第20讲：圆与矩形碰撞
- 第21讲：SAT分离轴定理

**第6章 运动与物理**
- 第22讲：速度、加速度与运动方程
- 第23讲：抛物运动与重力
- 第24讲：反弹与碰撞响应
- 第25讲：摩擦与阻尼

**第7章 插值与动画**
- 第26讲：线性插值Lerp
- 第27讲：缓动函数
- 第28讲：贝塞尔曲线
- 第29讲：角度插值与最短路径

**第8章 实战应用**
- 第30讲：角色面向与瞄准
- 第31讲：子弹追踪与导弹算法
- 第32讲：摄像机跟随与平滑
- 第33讲：程序化生成与Perlin噪声

---

## 第1章 数学基础

数学是游戏开发的底层语言。在2D游戏中，角色的位置、敌人的移动方向、子弹的轨迹、碰撞的判定，无一不依赖数学。本章将带你从最基础的标量、向量与坐标系开始，逐步建立游戏数学的知识体系。这些概念看似简单，却是后续所有高级主题的基石。

### 第1讲：标量、向量与坐标系

#### 概念

**标量（Scalar）** 是一个只有大小、没有方向的量，例如生命值100、速度5、时间3秒。**向量（Vector）** 则是同时具有大小和方向的量，例如"向右上方移动3米"、"以每秒50像素的速度向左移动"。在2D游戏中，向量通常用两个分量表示：`(x, y)`，分别对应水平方向和垂直方向的位移。

**坐标系（Coordinate System）** 是描述空间中位置的一套规则。2D游戏中最常用的是**笛卡尔坐标系**，由两条互相垂直的数轴（x轴和y轴）组成，原点(0,0)是它们的交点。屏幕上的每一个像素位置都可以用一对坐标 `(x, y)` 唯一确定。

#### 原理

游戏中的坐标系有一个关键特性需要特别注意：**屏幕坐标系与数学坐标系的y轴方向相反**。在标准数学坐标系中，y轴向上为正；但在大多数图形API（如屏幕渲染）中，y轴向下为正，原点在屏幕左上角。这意味着"向上移动"在数学中是y增加，在屏幕中却是y减少。

向量的几何意义可以从两个角度理解：一是**位置向量**，表示从原点指向某点的箭头，用于描述物体位置；二是**方向向量**，表示一个纯粹的位移方向，与起点无关。同一个向量 `(3, 4)` 既可以表示"物体在位置(3,4)"，也可以表示"物体向右移动3、向下移动4"。理解这种双重性是掌握向量的关键。

#### 例子

下面用Python定义一个最基础的2D向量类，这是后续所有数学运算的基石：

```python
import math

class Vector2:
    """2D向量类：游戏数学的基础数据结构"""
    def __init__(self, x=0.0, y=0.0):
        self.x = x
        self.y = y
    
    def __repr__(self):
        return f"Vector2({self.x}, {self.y})"
    
    # 判断两个向量是否相等（考虑浮点误差）
    def __eq__(self, other):
        return (math.isclose(self.x, other.x) and 
                math.isclose(self.y, other.y))

# 游戏中的实际应用
player_pos = Vector2(100, 200)      # 玩家位置（位置向量）
move_direction = Vector2(1, 0)      # 向右移动（方向向量）
enemy_pos = Vector2(300, 400)       # 敌人位置

# 屏幕坐标系示例：原点在左上角，y向下为正
# 玩家在屏幕左上角附近，敌人在右下角
print(f"玩家位置: {player_pos}")    # Vector2(100, 200)
print(f"敌人位置: {enemy_pos}")    # Vector2(300, 400)
```

一个直观的坐标系示意图（文本表示）：

```
屏幕坐标系（原点在左上角）
(0,0) ──────→ x轴（向右为正）
  │
  │
  ↓
 y轴（向下为正）

  ┌─────────────────────┐
  │ 玩家(100,200)        │
  │      ●               │
  │                      │
  │                      │
  │              ●(300,400)敌人
  │                      │
  └─────────────────────┘
```

#### 总结

- **标量**只有大小，**向量**有大小和方向，2D向量用 `(x, y)` 表示
- 游戏中**屏幕坐标系y轴向下**，与数学坐标系相反，这是新手最容易混淆的点
- 向量有"位置向量"和"方向向量"双重含义，需根据上下文理解
- 建议为项目封装一个 `Vector2` 类，统一管理向量运算，避免散落的裸x、y变量
- 浮点数比较要用 `isclose` 而非 `==`，避免精度问题导致的bug

---

### 第2讲：三角函数与角度弧度

#### 概念

**角度（Degree）** 是我们日常熟悉的度量方式，一个圆周为360度。**弧度（Radian）** 则是数学上更自然的度量方式，定义为弧长与半径之比，一个圆周为 `2π` 弧度（约6.283）。**三角函数**（正弦sin、余弦cos、正切tan）描述了直角三角形中边与角的关系，是连接角度与坐标的桥梁。

在游戏开发中，三角函数无处不在：计算角色面向方向、旋转精灵、生成圆形轨迹、实现钟摆运动等。理解弧度与角度的转换，以及三角函数的几何意义，是必备技能。

#### 原理

角度与弧度的转换公式为：`弧度 = 角度 × (π / 180)`，`角度 = 弧度 × (180 / π)`。大多数编程语言的数学库（如Python的 `math` 模块）使用弧度作为参数，因此需要频繁转换。

三角函数的几何意义至关重要：对于一个单位向量（长度为1的向量），若它与x轴正方向的夹角为θ，则其x分量为 `cos(θ)`，y分量为 `sin(θ)`。这意味着**任何方向都可以用角度表示，也可以用向量表示，二者等价且可互转**。这是游戏开发中最常用的数学关系之一。

需要注意的是，在屏幕坐标系中（y向下为正），正角度通常表示顺时针旋转，这与数学坐标系（y向上为正，正角度为逆时针）相反。许多新手在此处踩坑，导致角色旋转方向错误。

#### 例子

```python
import math

# 角度与弧度互转
def deg2rad(deg):
    return deg * math.pi / 180.0

def rad2deg(rad):
    return rad * 180.0 / math.pi

# 从角度生成方向向量（常用于角色面向、子弹发射方向）
def angle_to_vector(angle_deg):
    """将角度转换为单位方向向量"""
    rad = deg2rad(angle_deg)
    return Vector2(math.cos(rad), math.sin(rad))

# 从方向向量求角度（常用于让角色面向目标）
def vector_to_angle(v):
    """将方向向量转换为角度（度）"""
    return rad2deg(math.atan2(v.y, v.x))

# 实际应用：让角色面向鼠标
player = Vector2(100, 100)
mouse = Vector2(300, 200)
direction = Vector2(mouse.x - player.x, mouse.y - player.y)
facing_angle = vector_to_angle(direction)
print(f"角色面向角度: {facing_angle:.1f}°")  # 约45°（屏幕坐标系下）

# 圆形轨迹：让物体绕中心做圆周运动
center = Vector2(400, 300)
radius = 100
time = 0
def update(dt):
    global time
    time += dt
    angle = deg2rad(time * 90)  # 每秒转90度
    # 物体位置 = 中心 + 半径 × (cos, sin)
    obj_x = center.x + radius * math.cos(angle)
    obj_y = center.y + radius * math.sin(angle)
    return Vector2(obj_x, obj_y)
```

角度与向量的对应关系示意：

```
角度0°   → 方向(1, 0)   向右
角度90°  → 方向(0, 1)   向下（屏幕坐标系）
角度180° → 方向(-1, 0)  向左
角度270° → 方向(0, -1)  向上（屏幕坐标系）

        270° (0,-1)
          ↑
          │
180° ←────●────→ 0° (1,0)
(-1,0)    │
          ↓
        90° (0,1)
```

#### 总结

- 弧度是数学库的标准单位，角度是人类友好单位，二者需频繁转换
- **核心公式**：方向向量 = `(cos(θ), sin(θ))`，角度 = `atan2(y, x)`
- 屏幕坐标系下正角度通常为顺时针，与数学坐标系相反
- `atan2(y, x)` 比 `atan(y/x)` 更安全，能正确处理x=0的情况并返回完整象限
- 圆周运动、钟摆、波浪运动等都依赖三角函数，是动画的基础工具

---

### 第3讲：向量基础运算（加减与数乘）

#### 概念

向量运算包括**加法**、**减法**和**数乘（标量乘法）**。向量加法是将两个向量的对应分量相加，几何上表现为"首尾相接"法则：把第二个向量的起点接到第一个向量的终点，结果向量从第一个向量起点指向第二个向量终点。向量减法 `a - b` 等价于 `a + (-b)`，几何上表示从b指向a的向量。数乘是将向量的每个分量乘以一个标量，会缩放向量长度但不改变其方向（负数则反向）。

#### 原理

向量加法的物理意义是**位移的合成**。如果角色先向右移动3格，再向上移动4格，总位移就是 `(3, 4)`，这与先向右再向上的顺序无关——这就是向量加法的交换律 `a + b = b + a`。在游戏中，这常用于将多个力（重力、风力、推力）合成为物体的总速度。

向量减法 `a - b` 的核心用途是**求两点间的方向与距离**。若玩家在A点，敌人在B点，则 `B - A` 给出从玩家指向敌人的向量，这是瞄准、追踪、视野判断的基础。

数乘的几何意义是**缩放**：乘以2，向量变长一倍；乘以0.5，向量缩短一半；乘以-1，向量反向。这在调整速度大小、缩放位移时极为常用。

#### 例子

为 `Vector2` 类添加基础运算：

```python
class Vector2:
    def __init__(self, x=0.0, y=0.0):
        self.x = x
        self.y = y
    
    # 向量加法
    def __add__(self, other):
        return Vector2(self.x + other.x, self.y + other.y)
    
    # 向量减法
    def __sub__(self, other):
        return Vector2(self.x - other.x, self.y - other.y)
    
    # 数乘（标量乘法）
    def __mul__(self, scalar):
        return Vector2(self.x * scalar, self.y * scalar)
    
    # 反向
    def __neg__(self):
        return Vector2(-self.x, -self.y)
    
    def __repr__(self):
        return f"Vector2({self.x}, {self.y})"

# 应用1：合成多个力（如重力 + 风力 + 推力）
gravity = Vector2(0, 200)        # 向下重力
wind = Vector2(-50, 0)           # 向左风力
thrust = Vector2(0, -300)        # 向上推力
total_force = gravity + wind + thrust
print(f"合力: {total_force}")    # Vector2(-50, -100)

# 应用2：求玩家到敌人的方向
player = Vector2(100, 100)
enemy = Vector2(400, 300)
to_enemy = enemy - player         # 从玩家指向敌人
print(f"指向敌人的向量: {to_enemy}")  # Vector2(300, 200)

# 应用3：按速度移动角色
velocity = Vector2(5, 0)          # 每帧向右移动5像素
dt = 0.5                          # 时间步长
player = player + velocity * dt   # 位置 += 速度 × 时间
print(f"移动后位置: {player}")    # Vector2(102.5, 100)
```

向量加法的"首尾相接"几何意义：

```
向量a = (3, 1)，向量b = (1, 3)

  a + b 的几何意义（三角形法则）：
  
     ┌───→ b (1,3)
     │    ╲
     │     ╲  a+b = (4,4)
  a  │      ╲
  (3,1)     ●────→
     │      │
     ●──────●
    起点    a的终点=b的起点
```

#### 总结

- 向量加法满足交换律和结合律，是力合成、位移叠加的基础
- **`B - A` 表示从A指向B的向量**，这是求方向、求距离的核心操作
- 数乘只改变长度不改变方向（负数反向），用于缩放速度和位移
- 游戏中"位置 += 速度 × 时间"是最频繁的运算，本质是向量加法与数乘
- 实现向量类时务必重载 `+`、`-`、`*` 运算符，让代码更接近数学表达

---

### 第4讲：向量归一化与距离计算

#### 概念

**向量的长度（模）** 是向量的大小，记作 `|v|`，由勾股定理计算：`|v| = sqrt(x² + y²)`。**归一化（Normalization）** 是将向量缩放为长度为1的单位向量，方向保持不变，公式为 `v / |v|`。**两点间距离** 就是这两点所构成向量的长度，是碰撞检测、范围判断、AI寻路的基础。

归一化后的向量称为**单位向量**，它只表示方向不含大小信息。在游戏中，我们经常需要"以固定速度沿某方向移动"，这时就要先求方向向量，归一化后乘以速度大小，得到实际位移。

#### 原理

归一化的必要性在于**分离方向与大小**。假设玩家在(100,100)，敌人在(400,300)，方向向量为(300,200)，长度约360.6。如果直接用这个向量作为速度，角色会以每帧360像素的速度移动——太快了。正确做法是归一化得到(0.832, 0.555)，再乘以期望速度（如5像素/帧），得到(4.16, 2.77)，这样无论目标多远，移动速度恒定。

距离公式 `d = sqrt((x2-x1)² + (y2-y1)²)` 本质就是向量长度的应用。在性能敏感场景中，常使用**距离平方**比较（`d² = (x2-x1)² + (y2-y1)²`），避免昂贵的开方运算。例如判断"敌人是否在攻击范围100内"，可以比较 `d² < 100²`，结果相同但速度快得多。

需要注意**零向量不能归一化**（长度为0，除以0未定义），代码中必须特判。

#### 例子

```python
import math

class Vector2:
    def __init__(self, x=0.0, y=0.0):
        self.x = x
        self.y = y
    
    def __add__(self, other): return Vector2(self.x + other.x, self.y + other.y)
    def __sub__(self, other): return Vector2(self.x - other.x, self.y - other.y)
    def __mul__(self, s): return Vector2(self.x * s, self.y * s)
    def __truediv__(self, s): return Vector2(self.x / s, self.y / s)
    
    # 向量长度（模）
    def length(self):
        return math.sqrt(self.x * self.x + self.y * self.y)
    
    # 长度平方（避免开方，用于性能优化）
    def length_squared(self):
        return self.x * self.x + self.y * self.y
    
    # 归一化：返回单位向量
    def normalize(self):
        l = self.length()
        if l < 1e-9:  # 避免除零
            return Vector2(0, 0)
        return Vector2(self.x / l, self.y / l)
    
    def __repr__(self):
        return f"Vector2({self.x:.3f}, {self.y:.3f})"

# 应用1：以固定速度向目标移动
player = Vector2(100, 100)
target = Vector2(400, 300)
speed = 5  # 每帧5像素

direction = (target - player).normalize()  # 单位方向向量
velocity = direction * speed               # 实际速度
player = player + velocity                 # 移动
print(f"移动后: {player}")  # Vector2(104.16, 102.77)

# 应用2：判断敌人是否在攻击范围内（用距离平方优化）
def in_attack_range(attacker, target, range_):
    diff = target - attacker
    return diff.length_squared() < range_ * range_

enemy = Vector2(150, 120)
print(in_attack_range(player, enemy, 100))  # True（距离约22.4 < 100）

# 应用3：范围伤害——计算所有在爆炸半径内的敌人
explosion_center = Vector2(200, 200)
explosion_radius = 80
enemies = [Vector2(250, 200), Vector2(200, 250), Vector2(500, 500)]
affected = [e for e in enemies 
            if (e - explosion_center).length_squared() < explosion_radius ** 2]
print(f"受影响的敌人数: {len(affected)}")  # 2
```

归一化的几何直观：

```
原始向量 (300, 200)，长度 ≈ 360.6
归一化后 (0.832, 0.555)，长度 = 1.0

方向不变，只调整大小：
   ●─────────────→  原向量（长360.6）
   ●──→             归一化（长1.0）
   ●──────────→     乘以速度5（长5.0）
```

#### 总结

- 向量长度公式 `sqrt(x² + y²)`，本质是勾股定理
- **归一化分离方向与大小**，是"恒定速度移动"的标准做法
- 性能优化：用**距离平方**比较，避免开方运算，在大量判定时收益显著
- 零向量归一化必须特判，否则会得到NaN导致后续计算全部出错
- 距离计算是碰撞检测、范围伤害、AI视野的基础，几乎每帧都在执行

---

## 第2章 向量进阶

掌握了向量的基础运算后，本章将深入两个最重要的向量运算：点积与叉积。它们看似抽象，实则是游戏开发中最高频使用的数学工具。点积用于判断方向关系、计算投影与夹角；叉积用于判定左右方向、计算面积与法线。配合反射运算，它们能解决从光照到弹跳的各类问题。本章是向量知识的精华所在。

### 第5讲：点积与投影

#### 概念

**点积（Dot Product）**，又称内积，是两个向量运算后得到一个标量的运算。对于2D向量 `a = (ax, ay)` 和 `b = (bx, by)`，点积定义为 `a · b = ax * bx + ay * by`。点积有一个等价的几何定义：`a · b = |a| |b| cos(θ)`，其中θ是两向量夹角。**投影（Projection）** 是点积的直接应用，指一个向量在另一个向量方向上的"影子"长度。

#### 原理

点积最强大的特性是**通过符号判断方向关系**：
- `a · b > 0`：两向量夹角小于90°，方向相近（同向）
- `a · b = 0`：两向量垂直（正交）
- `a · b < 0`：两向量夹角大于90°，方向相反

这一特性在游戏中极为有用：判断敌人是否在玩家视野内、子弹是否击中目标的正面、角色是否朝向目标移动等。

由几何定义 `a · b = |a| |b| cos(θ)` 可推导出夹角公式：`cos(θ) = (a · b) / (|a| |b|)`。若a、b都是单位向量，则 `cos(θ) = a · b`，直接得到夹角余弦，无需除法。

投影公式为：`proj_b(a) = (a · b / |b|²) * b`，表示向量a在向量b方向上的投影向量。这在"物体沿斜面滑动"、"光线反射"等场景中是核心计算。

#### 例子

```python
import math

class Vector2:
    def __init__(self, x=0.0, y=0.0):
        self.x = x; self.y = y
    def __add__(self, o): return Vector2(self.x+o.x, self.y+o.y)
    def __sub__(self, o): return Vector2(self.x-o.x, self.y-o.y)
    def __mul__(self, s): return Vector2(self.x*s, self.y*s)
    def length(self): return math.sqrt(self.x**2 + self.y**2)
    def length_squared(self): return self.x**2 + self.y**2
    def normalize(self):
        l = self.length()
        return Vector2(0,0) if l < 1e-9 else Vector2(self.x/l, self.y/l)
    
    # 点积
    def dot(self, other):
        return self.x * other.x + self.y * other.y
    
    # 投影：self在other方向上的投影向量
    def project_onto(self, other):
        d = self.dot(other)
        l2 = other.length_squared()
        if l2 < 1e-9: return Vector2(0,0)
        return other * (d / l2)
    
    def __repr__(self): return f"V({self.x:.2f}, {self.y:.2f})"

# 应用1：判断敌人是否在玩家视野锥内
def in_field_of_view(player_pos, player_facing, enemy_pos, fov_deg):
    """fov_deg为视野张角（如90°表示前方±45°）"""
    to_enemy = (enemy_pos - player_pos).normalize()
    # facing和to_enemy都归一化后，点积=cos(夹角)
    cos_angle = player_facing.normalize().dot(to_enemy)
    cos_half_fov = math.cos(math.radians(fov_deg / 2))
    return cos_angle > cos_half_fov  # 夹角小于fov/2则在视野内

player = Vector2(100, 100)
facing = Vector2(1, 0)  # 向右看
enemy1 = Vector2(200, 110)  # 几乎正前方
enemy2 = Vector2(100, 200)  # 正下方
print(f"敌人1在视野内: {in_field_of_view(player, facing, enemy1, 90)}")  # True
print(f"敌人2在视野内: {in_field_of_view(player, facing, enemy2, 90)}")  # False

# 应用2：计算两向量夹角
def angle_between(a, b):
    cos_a = a.normalize().dot(b.normalize())
    cos_a = max(-1, min(1, cos_a))  # 钳制，避免浮点误差
    return math.degrees(math.acos(cos_a))

print(f"夹角: {angle_between(Vector2(1,0), Vector2(1,1))):.1f}°")  # 45°

# 应用3：物体沿斜面分解力（重力分解为沿斜面+垂直斜面）
gravity = Vector2(0, 100)  # 向下重力
slope_dir = Vector2(1, 1).normalize()  # 45°斜面方向
# 重力沿斜面方向的分量（使物体下滑的力）
along_slope = gravity.project_onto(slope_dir)
print(f"沿斜面分力: {along_slope}")  # V(35.36, 35.36)
```

点积判断方向的直观示意：

```
玩家朝向 facing →
              ╱ 视野锥（90°）
            ╱    │
          ╱      │
   ●─────╱────────│────────
  玩家   ╲        │
          ╲       │
            ╲     │  ← 敌人在此区域外（点积<cos45°）

点积判断：
  facing · to_enemy > cos(45°) ≈ 0.707  → 在视野内
  facing · to_enemy < cos(45°)          → 在视野外
```

#### 总结

- 点积公式 `a · b = ax*bx + ay*by`，结果为标量
- **点积符号判断方向**：正同向、零垂直、负反向，这是最常用的特性
- 两单位向量的点积等于夹角余弦，省去除法
- 投影公式 `proj = (a·b / |b|²) * b`，用于力分解、阴影计算
- 视野判断、光照计算、斜面运动都依赖点积，是游戏AI与物理的核心

---

### 第6讲：叉积与方向判定

#### 概念

**叉积（Cross Product）** 在3D中产生一个垂直于两向量的新向量，但在2D中，叉积退化为一个标量（称为**2D伪叉积**或**楔积**）。对于2D向量 `a = (ax, ay)` 和 `b = (bx, by)`，2D叉积定义为 `a × b = ax * by - ay * bx`。这个标量的**符号**告诉我们b相对于a的方向：正表示b在a的逆时针方向（左侧），负表示顺时针方向（右侧），零表示共线。

#### 原理

2D叉积的几何意义是**两向量张成的平行四边形的有向面积**。`|a × b| = |a| |b| sin(θ)`，绝对值就是平行四边形面积，符号反映方向。这一特性使叉积成为**方向判定**的利器。

在游戏开发中，叉积的核心应用包括：
1. **判定点在直线的哪一侧**：常用于多边形裁剪、视野遮挡
2. **判定三个点的转向**：是凸包算法、多边形面积计算的基础
3. **计算法线**：给定一条边，叉积能快速得到其垂直方向
4. **判定点是否在三角形/多边形内**：通过同侧判定

需要注意屏幕坐标系下y轴反向，因此"左侧/右侧"的直观感受可能与数学坐标系相反。在屏幕坐标系中，正叉积通常表示b在a的顺时针方向（右侧），与数学坐标系相反。

#### 例子

```python
class Vector2:
    def __init__(self, x=0.0, y=0.0):
        self.x = x; self.y = y
    def __sub__(self, o): return Vector2(self.x-o.x, self.y-o.y)
    
    # 2D叉积（返回标量）
    def cross(self, other):
        return self.x * other.y - self.y * other.x

# 应用1：判断点在向量的左侧还是右侧
def side_of_line(line_start, line_end, point):
    """返回: 正=左侧, 负=右侧, 0=线上"""
    line_vec = line_end - line_start
    to_point = point - line_start
    return line_vec.cross(to_point)

a = Vector2(0, 0)
b = Vector2(1, 0)  # 向右的线
p_left = Vector2(0, -1)   # 屏幕坐标系中"上方"=y更小
p_right = Vector2(0, 1)  # 屏幕坐标系中"下方"=y更大
print(f"点在左侧: {side_of_line(a, b, p_left)}")   # 正（左侧）
print(f"点在右侧: {side_of_line(a, b, p_right)}")  # 负（右侧）

# 应用2：判断点是否在三角形内
def point_in_triangle(p, a, b, c):
    """利用叉积同号判定：点在三角形内当且仅当对三条边同侧"""
    d1 = (b - a).cross(p - a)
    d2 = (c - b).cross(p - b)
    d3 = (a - c).cross(p - c)
    # 全正或全负表示同侧
    has_neg = (d1 < 0) or (d2 < 0) or (d3 < 0)
    has_pos = (d1 > 0) or (d2 > 0) or (d3 > 0)
    return not (has_neg and has_pos)

tri = [Vector2(0,0), Vector2(100,0), Vector2(50,100)]
print(f"点(50,30)在三角形内: {point_in_triangle(Vector2(50,30), *tri)}")  # True
print(f"点(50,150)在三角形内: {point_in_triangle(Vector2(50,150), *tri)}")  # False

# 应用3：计算多边形面积（鞋带公式，本质是叉积累加）
def polygon_area(vertices):
    n = len(vertices)
    area = 0
    for i in range(n):
        j = (i + 1) % n
        area += vertices[i].cross(vertices[j])
    return abs(area) / 2

square = [Vector2(0,0), Vector2(100,0), Vector2(100,100), Vector2(0,100)]
print(f"正方形面积: {polygon_area(square)}")  # 10000

# 应用4：求向量的垂直向量（法线）
def perpendicular(v):
    """返回v的左手法线（屏幕坐标系中）"""
    return Vector2(-v.y, v.x)

edge = Vector2(1, 0)  # 向右的边
normal = perpendicular(edge)  # 法线指向上方
print(f"边的法线: {normal}")  # V(0, 1) 在数学坐标系，屏幕系则相反
```

叉积方向判定的直观示意（屏幕坐标系）：

```
       点P(0,-1) 在线ab左侧（叉积>0）
            ●
            │
  a ●───────●─────── b
            │
            ●
       点P(0,1) 在线ab右侧（叉积<0）

线方向 a→b 向右
  左侧 = 屏幕上方（y更小）→ 叉积为正
  右侧 = 屏幕下方（y更大）→ 叉积为负
```

#### 总结

- 2D叉积 `a × b = ax*by - ay*bx`，结果为标量
- **符号判定方向**：正=左侧/逆时针，负=右侧/顺时针，零=共线
- 屏幕坐标系下方向感受与数学坐标系相反，需注意
- 叉积是凸包、多边形面积、点在多边形内判定的核心
- 求法线只需 `(−y, x)` 或 `(y, −x)`，是叉积的特例

---

### 第7讲：向量反射与镜像

#### 概念

**向量反射（Reflection）** 是指一个向量碰到表面后"弹开"的数学计算。给定入射向量 `v` 和表面法线 `n`，反射向量 `r` 的公式为：`r = v - 2 * (v · n) * n`。这个公式假设n是单位向量。反射在游戏中用于实现弹球、光线弹跳、台球运动、激光武器等。

#### 原理

反射公式的推导基于"分解与重组"思想：入射向量可以分解为两个分量——**沿法线方向的分量**（垂直于表面）和**沿表面方向的分量**（平行于表面）。反射时，平行分量保持不变，垂直分量反向，两者重新组合即得反射向量。

数学上，入射向量v在法线n方向的投影分量为 `(v · n) * n`（当n为单位向量时）。反射就是把这个分量取反：`r = v - 2 * (v · n) * n`。系数2是因为要"减去两次"——一次抵消原分量，一次加上反向分量。

反射的物理应用极广：台球碰撞边界后的弹跳、光线在镜面的反射、声波在墙壁的回响、角色在弹性表面的反弹。在2D游戏中，最常见的是弹球游戏（Breakout/Arkanoid）中球与砖块、墙体的碰撞反弹。

需要注意法线方向必须正确（指向入射方向），否则反射结果会反向。同时入射向量通常应取反方向（从碰撞点指向来源），这是新手常犯的错误。

#### 例子

```python
import math

class Vector2:
    def __init__(self, x=0.0, y=0.0):
        self.x = x; self.y = y
    def __add__(self, o): return Vector2(self.x+o.x, self.y+o.y)
    def __sub__(self, o): return Vector2(self.x-o.x, self.y-o.y)
    def __mul__(self, s): return Vector2(self.x*s, self.y*s)
    def length(self): return math.sqrt(self.x**2 + self.y**2)
    def normalize(self):
        l = self.length()
        return Vector2(0,0) if l < 1e-9 else Vector2(self.x/l, self.y/l)
    def dot(self, o): return self.x*o.x + self.y*o.y
    def __repr__(self): return f"V({self.x:.2f}, {self.y:.2f})"

# 反射公式：r = v - 2*(v·n)*n
def reflect(v, n):
    """v为入射方向，n为表面法线（必须归一化）"""
    return v - n * (2 * v.dot(n))

# 应用1：弹球游戏——球碰墙反弹
ball_vel = Vector2(3, 4)  # 球向右下移动
# 碰到底墙（法线指向上，即y负方向）
wall_normal = Vector2(0, -1)
new_vel = reflect(ball_vel, wall_normal)
print(f"碰底墙后速度: {new_vel}")  # V(3.00, -4.00) y分量反向

# 碰到右墙（法线指向左）
wall_normal = Vector2(-1, 0)
new_vel = reflect(ball_vel, wall_normal)
print(f"碰右墙后速度: {new_vel}")  # V(-3.00, 4.00) x分量反向

# 应用2：斜面反射——球碰45°斜面
# 斜面法线（指向左上方）
slope_normal = Vector2(-1, -1).normalize()
ball_vel = Vector2(2, 0)  # 水平向右
new_vel = reflect(ball_vel, slope_normal)
print(f"碰斜面后速度: {new_vel}")  # V(0.00, 2.00) 变为向下

# 应用3：激光在镜面间反射
def simulate_laser(start, direction, mirrors, max_bounces=10):
    """模拟激光在多面镜子间反射的路径"""
    path = [start]
    pos = start
    dir_ = direction.normalize()
    for _ in range(max_bounces):
        # 简化：假设每次碰到镜面就在镜面位置反射
        # 实际需做射线-线段相交检测
        for mirror_pos, mirror_normal in mirrors:
            if (mirror_pos - pos).length() < 50:  # 简化判定
                dir_ = reflect(dir_, mirror_normal)
                pos = mirror_pos
                path.append(pos)
                break
    return path

# 应用4：角色被弹簧弹飞
def spring_bounce(velocity, spring_normal, spring_power=1.5):
    """弹簧不仅反射，还附加额外能量"""
    reflected = reflect(velocity, spring_normal)
    return reflected * spring_power  # 弹簧让速度变快
```

反射的几何直观：

```
入射向量 v = (3, 4)，法线 n = (0, -1)（指向上）

       v ↘
          ↘
  ────────●──────── 表面
          ↑
          n（法线）

分解：v = v_parallel(3,0) + v_perp(0,4)
反射：v_parallel不变，v_perp反向 → (3,0) + (0,-4) = (3,-4)

结果：r = (3, -4)，球向上弹起
```

#### 总结

- 反射公式 `r = v - 2*(v·n)*n`，n必须归一化
- 原理是"平行分量保留，垂直分量反向"
- 法线方向必须指向入射来源，否则结果错误
- 弹球、台球、激光、光线追踪都依赖反射
- 可在反射基础上乘以系数实现"加速弹跳"或"减速衰减"

---

### 第8讲：向量在游戏中的综合应用

#### 概念

本讲不引入新概念，而是将前几讲的向量知识综合运用到典型游戏场景中。向量是游戏数学的"瑞士军刀"，掌握其灵活组合能解决绝大多数2D游戏问题。我们将通过四个实战案例展示向量的综合威力：扇形范围攻击、击退效果、视野遮挡、平滑跟随。

#### 原理

游戏中的复杂行为往往是简单向量运算的组合。例如"扇形伤害"=距离判定+角度判定（点积）；"击退"=方向向量×力度；"视野遮挡"=射线与障碍物相交；"平滑跟随"=插值+方向向量。理解这些组合模式后，遇到新需求就能拆解为基础运算。

关键设计原则：**先求方向（归一化），再叠加大小（数乘）**。这是处理所有"沿某方向以某力度作用"问题的通用范式。另一个原则是**用点积做角度判定，用叉积做方向判定**，二者配合能覆盖绝大多数空间关系判断。

#### 例子

```python
import math

class Vector2:
    def __init__(self, x=0.0, y=0.0):
        self.x = x; self.y = y
    def __add__(self, o): return Vector2(self.x+o.x, self.y+o.y)
    def __sub__(self, o): return Vector2(self.x-o.x, self.y-o.y)
    def __mul__(self, s): return Vector2(self.x*s, self.y*s)
    def length(self): return math.sqrt(self.x**2 + self.y**2)
    def length_squared(self): return self.x**2 + self.y**2
    def normalize(self):
        l = self.length()
        return Vector2(0,0) if l < 1e-9 else Vector2(self.x/l, self.y/l)
    def dot(self, o): return self.x*o.x + self.y*o.y
    def cross(self, o): return self.x*o.y - self.y*o.x
    def lerp(self, other, t): return self + (other - self) * t
    def __repr__(self): return f"V({self.x:.2f}, {self.y:.2f})"

# 案例1：扇形范围攻击（如战士的横扫）
def fan_attack(attacker, facing, enemies, range_, fov_deg):
    """返回受影响的敌人列表"""
    affected = []
    cos_half_fov = math.cos(math.radians(fov_deg / 2))
    facing_norm = facing.normalize()
    for enemy in enemies:
        to_enemy = enemy - attacker
        if to_enemy.length_squared() > range_ ** 2:
            continue  # 超出范围
        cos_angle = facing_norm.dot(to_enemy.normalize())
        if cos_angle > cos_half_fov:
            affected.append(enemy)
    return affected

hero = Vector2(100, 100)
hero_facing = Vector2(1, 0)  # 朝右
enemies = [Vector2(150, 100), Vector2(100, 150), Vector2(200, 50), Vector2(300, 300)]
hit = fan_attack(hero, hero_facing, enemies, 100, 90)
print(f"横扫命中: {len(hit)} 个敌人")  # 2

# 案例2：击退效果（受击后向反方向飞出）
def knockback(target_pos, attacker_pos, force):
    """计算击退后的位移"""
    direction = (target_pos - attacker_pos).normalize()
    return direction * force

enemy = Vector2(200, 100)
attacker = Vector2(100, 100)
kb = knockback(enemy, attacker, 50)
print(f"击退位移: {kb}")  # V(50.00, 0.00)

# 案例3：平滑跟随（摄像机或宠物跟随主角）
class SmoothFollower:
    def __init__(self, pos):
        self.pos = pos
        self.speed = 0.1  # 跟随速度（0~1，越大越快）
    def update(self, target):
        # 用lerp实现平滑跟随，避免硬贴附
        self.pos = self.pos.lerp(target, self.speed)

camera = SmoothFollower(Vector2(0, 0))
player = Vector2(100, 100)
for _ in range(5):
    camera.update(player)
    print(f"摄像机位置: {camera.pos}")  # 逐渐接近(100,100)

# 案例4：预测拦截——预判移动目标的射击位置
def predict_intercept(shooter, target, target_vel, bullet_speed):
    """计算拦截点，使子弹能命中移动目标"""
    to_target = target - shooter
    dist = to_target.length()
    # 解二次方程求拦截时间
    a = target_vel.length_squared() - bullet_speed ** 2
    b = 2 * to_target.dot(target_vel)
    c = -dist ** 2
    if abs(a) < 1e-9:
        t = -c / b if b != 0 else -1
    else:
        disc = b*b - 4*a*c
        if disc < 0: return None  # 无法拦截
        t = (-b - math.sqrt(disc)) / (2*a)
        if t < 0: t = (-b + math.sqrt(disc)) / (2*a)
    if t < 0: return None
    return target + target_vel * t  # 拦截点

shooter = Vector2(0, 0)
target = Vector2(100, 0)
target_vel = Vector2(0, 50)  # 目标向下移动
intercept = predict_intercept(shooter, target, target_vel, 200)
print(f"拦截点: {intercept}")  # 预判位置
```

#### 总结

- 复杂游戏行为=简单向量运算的组合，关键是拆解问题
- **扇形攻击**=距离判定+点积角度判定
- **击退/推力**=方向归一化×力度
- **平滑跟随**=lerp插值，速度参数控制响应快慢
- **拦截预判**=解二次方程求相遇时间，是AI射击的核心
- 设计原则：先求方向（归一化），再叠加大小（数乘）

---

## 第3章 矩阵与变换

矩阵是游戏开发中描述空间变换的核心工具。如果说向量描述了"在哪里、朝哪个方向"，那么矩阵描述了"如何变换"——平移、缩放、旋转都可用矩阵统一表达。本章从矩阵基础运算讲起，逐步介绍三大基本变换，最后通过齐次坐标与复合变换，让你理解游戏引擎底层的变换机制。这是从"会用向量"到"理解变换系统"的关键一跃。

### 第9讲：矩阵基础与运算

#### 概念

**矩阵（Matrix）** 是一个按行列排列的数字阵列。在2D游戏中，最常用的是 **2×2矩阵**（用于旋转和缩放）和 **3×3矩阵**（用于包含平移的复合变换，借助齐次坐标）。矩阵的运算包括**加法**（对应元素相加）、**数乘**（每个元素乘以标量）和**乘法**（行乘列的累积和，这是最核心也最容易出错的运算）。

矩阵乘法不满足交换律：`A × B ≠ B × A`。这意味着变换的顺序至关重要——先旋转再平移，与先平移再旋转，结果完全不同。这是矩阵运算最重要的特性，也是新手最常踩的坑。

#### 原理

矩阵乘法的规则是"行乘列"：若 `C = A × B`，则 `C[i][j] = Σ A[i][k] * B[k][j]`。直观地说，结果矩阵第i行第j列的元素，等于A的第i行与B的第j列对应元素相乘后求和。这要求A的列数等于B的行数。

矩阵与向量的乘法 `M × v` 是把向量v经过矩阵M变换后得到的新向量。这是矩阵在游戏中最常见的用法——用矩阵表示一个变换，然后乘以位置向量得到变换后的位置。例如旋转矩阵乘以一个点坐标，得到旋转后的新坐标。

矩阵乘法不满足交换律的物理意义：变换是有顺序的。想象"先向右走3步再转身"与"先转身再向右走3步"——前者走到右边，后者走到左边。在游戏中，角色变换通常是 `缩放 → 旋转 → 平移`（SRT顺序），这个顺序不能随意调换。

#### 例子

```python
class Matrix2x2:
    """2x2矩阵：用于旋转和缩放"""
    def __init__(self, a=1, b=0, c=0, d=1):
        # 矩阵形式: | a b |
        #           | c d |
        self.m = [[a, b], [c, d]]
    
    @classmethod
    def identity(cls):
        """单位矩阵：乘以任何矩阵都不变"""
        return cls(1, 0, 0, 1)
    
    def __mul__(self, other):
        """矩阵乘法（行乘列）"""
        if isinstance(other, Matrix2x2):
            a = self.m
            b = other.m
            result = [[0, 0], [0, 0]]
            for i in range(2):
                for j in range(2):
                    for k in range(2):
                        result[i][j] += a[i][k] * b[k][j]
            return Matrix2x2(result[0][0], result[0][1], 
                            result[1][0], result[1][1])
        elif isinstance(other, tuple):
            # 矩阵乘向量
            x, y = other
            a, b = self.m[0]
            c, d = self.m[1]
            return (a*x + b*y, c*x + d*y)
    
    def __repr__(self):
        return f"|{self.m[0][0]:.2f} {self.m[0][1]:.2f}|\n|{self.m[1][0]:.2f} {self.m[1][1]:.2f}|"

# 单位矩阵：不变换
I = Matrix2x2.identity()
print("单位矩阵:")
print(I)
print(f"乘以向量(3,4): {I * (3, 4)}")  # (3, 4)

# 矩阵乘法不满足交换律的演示
A = Matrix2x2(1, 2, 3, 4)
B = Matrix2x2(5, 6, 7, 8)
print("A × B:")
print(A * B)
print("B × A:")
print(B * A)
# 两个结果不同，证明 AB ≠ BA

# 矩阵乘向量：将向量变换
M = Matrix2x2(2, 0, 0, 3)  # x放大2倍，y放大3倍
print(f"变换(1,1): {M * (1, 1)}")  # (2, 3)
```

矩阵乘法"行乘列"的直观示意：

```
矩阵A (2x3)        矩阵B (3x2)        结果C (2x2)
| 1 2 3 |          | 7  8 |          | C00 C01 |
| 4 5 6 |    ×     | 9  10|    =     | C10 C11 |
                   | 11 12|

C[0][0] = 1*7 + 2*9 + 3*11 = 58  （A的第0行 × B的第0列）
C[0][1] = 1*8 + 2*10 + 3*12 = 64 （A的第0行 × B的第1列）
C[1][0] = 4*7 + 5*9 + 6*11 = 139
C[1][1] = 4*8 + 5*10 + 6*12 = 154
```

#### 总结

- 矩阵是按行列排列的数字阵列，2D游戏常用2×2和3×3矩阵
- 矩阵乘法规则是"行乘列"，要求A的列数=B的行数
- **矩阵乘法不满足交换律**，变换顺序至关重要
- 矩阵乘向量 `M × v` 是变换向量的核心操作
- 游戏中变换顺序通常为 `缩放→旋转→平移`（SRT），不可随意调换

---

### 第10讲：平移变换

#### 概念

**平移（Translation）** 是将物体沿某方向移动固定距离的变换。在2D中，平移由一个向量 `t = (tx, ty)` 描述，点 `(x, y)` 平移后变为 `(x + tx, y + ty)`。平移看似简单，但用矩阵表达时需要引入**齐次坐标**——这是数学上的精妙设计，让平移也能用矩阵乘法统一表达。

#### 原理

2D平移无法用2×2矩阵直接表达，因为矩阵乘法是线性变换（保持原点不变），而平移会改变原点。解决方案是**齐次坐标**：在2D点 `(x, y)` 后追加一个分量1，变成 `(x, y, 1)`，然后用3×3矩阵表达平移：

```
| 1 0 tx |   | x |   | x + tx |
| 0 1 ty | × | y | = | y + ty |
| 0 0 1  |   | 1 |   |   1    |
```

这种设计的精妙之处在于：平移、旋转、缩放现在都能用3×3矩阵统一表达，且可以通过矩阵乘法组合（复合变换）。这是游戏引擎底层变换系统的数学基础。

平移矩阵的逆矩阵就是反向平移：把tx、ty取负即可。这在"撤销变换"、"摄像机逆变换"等场景有用。

#### 例子

```python
class Matrix3x3:
    """3x3矩阵：用于包含平移的复合变换"""
    def __init__(self, m=None):
        # 默认单位矩阵
        self.m = m if m else [[1,0,0],[0,1,0],[0,0,1]]
    
    @classmethod
    def translation(cls, tx, ty):
        """平移矩阵"""
        return cls([[1,0,tx],[0,1,ty],[0,0,1]])
    
    def __mul__(self, other):
        if isinstance(other, Matrix3x3):
            result = [[0]*3 for _ in range(3)]
            for i in range(3):
                for j in range(3):
                    for k in range(3):
                        result[i][j] += self.m[i][k] * other.m[k][j]
            return Matrix3x3(result)
        elif isinstance(other, tuple):
            x, y = other
            return (self.m[0][0]*x + self.m[0][1]*y + self.m[0][2],
                    self.m[1][0]*x + self.m[1][1]*y + self.m[1][2])
    
    def inverse_translation(self):
        """平移矩阵的逆：反向平移"""
        return Matrix3x3.translation(-self.m[0][2], -self.m[1][2])

# 应用1：平移一个点
T = Matrix3x3.translation(50, 100)
print(f"平移(10,20)后: {T * (10, 20)}")  # (60, 120)

# 应用2：组合多个平移（矩阵乘法）
T1 = Matrix3x3.translation(10, 20)
T2 = Matrix3x3.translation(30, 40)
T_combined = T1 * T2  # 等价于一次平移(40, 60)
print(f"组合平移(0,0)后: {T_combined * (0, 0)}")  # (40, 60)

# 应用3：摄像机平移——世界坐标转屏幕坐标
# 摄像机在(cam_x, cam_y)，世界点P的屏幕坐标 = P - 摄像机位置
cam_x, cam_y = 100, 50
world_to_screen = Matrix3x3.translation(-cam_x, -cam_y)
player_world = (200, 150)
player_screen = world_to_screen * player_world
print(f"玩家屏幕坐标: {player_screen}")  # (100, 100)

# 应用4：撤销变换（用逆矩阵）
T = Matrix3x3.translation(50, 100)
T_inv = T.inverse_translation()
point = (10, 20)
transformed = T * point
restored = T_inv * transformed
print(f"变换后还原: {restored}")  # (10, 20) 还原成功
```

平移矩阵的结构示意：

```
平移矩阵 T(tx, ty)：
| 1  0  tx |
| 0  1  ty |
| 0  0  1  |

乘以点 (x, y, 1)：
| 1  0  tx |   | x |   | x+tx |
| 0  1  ty | × | y | = | y+ty |
| 0  0  1  |   | 1 |   |  1   |

结果：(x+tx, y+ty) —— 完成平移
```

#### 总结

- 平移是物体沿方向移动固定距离，由向量 `(tx, ty)` 描述
- 2D平移需用3×3矩阵+齐次坐标表达，这是数学上的统一设计
- 平移矩阵的逆就是反向平移（tx、ty取负）
- 摄像机变换本质是世界坐标减去摄像机位置，即平移变换
- 多个平移可通过矩阵乘法组合为一个平移

---

### 第11讲：缩放变换

#### 概念

**缩放（Scaling）** 是改变物体大小的变换。2D缩放由两个因子 `(sx, sy)` 描述，分别控制x方向和y方向的缩放比例。点 `(x, y)` 缩放后变为 `(x*sx, y*sy)`。当sx=sy时为**均匀缩放**（保持长宽比），否则为**非均匀缩放**（拉伸或压扁）。

#### 原理

缩放矩阵是2×2对角矩阵（或3×3齐次形式）：

```
| sx 0  0 |
| 0  sy 0 |
| 0  0  1 |
```

缩放的关键概念是**缩放中心**。默认缩放以原点为中心，物体在缩放的同时也会"移动"——离原点越远，移动距离越大。若想以物体自身中心缩放（保持位置不变），需要"先平移到原点→缩放→平移回去"的三步组合，这本质就是复合变换。

缩放因子为负数时实现**镜像翻转**：sx=-1实现水平翻转，sy=-1实现垂直翻转。这是实现角色面向左/右切换的常用技巧（无需准备两套贴图）。

缩放矩阵的逆矩阵就是把sx、sy取倒数，用于"撤销缩放"。

#### 例子

```python
class Matrix3x3:
    def __init__(self, m=None):
        self.m = m if m else [[1,0,0],[0,1,0],[0,0,1]]
    
    @classmethod
    def translation(cls, tx, ty):
        return cls([[1,0,tx],[0,1,ty],[0,0,1]])
    
    @classmethod
    def scaling(cls, sx, sy):
        """缩放矩阵"""
        return cls([[sx,0,0],[0,sy,0],[0,0,1]])
    
    def __mul__(self, other):
        if isinstance(other, Matrix3x3):
            result = [[0]*3 for _ in range(3)]
            for i in range(3):
                for j in range(3):
                    for k in range(3):
                        result[i][j] += self.m[i][k] * other.m[k][j]
            return Matrix3x3(result)
        elif isinstance(other, tuple):
            x, y = other
            return (self.m[0][0]*x + self.m[0][1]*y + self.m[0][2],
                    self.m[1][0]*x + self.m[1][1]*y + self.m[1][2])

# 应用1：均匀缩放（放大2倍）
S = Matrix3x3.scaling(2, 2)
print(f"缩放(3,4)后: {S * (3, 4)}")  # (6, 8)

# 应用2：非均匀缩放（拉伸）
S = Matrix3x3.scaling(2, 0.5)  # x放大2倍，y缩小一半
print(f"拉伸(10,10)后: {S * (10, 10)}")  # (20, 5)

# 应用3：角色面向切换（水平镜像）
flip_x = Matrix3x3.scaling(-1, 1)
# 角色贴图原本朝右，乘以-1后朝左
print(f"水平翻转(100,50)后: {flip_x * (100, 50)}")  # (-100, 50)

# 应用4：以物体自身中心缩放（关键技巧）
def scale_around_center(point, center, sx, sy):
    """以center为中心缩放point"""
    # 1. 平移到原点（减去center）
    # 2. 缩放
    # 3. 平移回去（加回center）
    T1 = Matrix3x3.translation(-center[0], -center[1])
    S = Matrix3x3.scaling(sx, sy)
    T2 = Matrix3x3.translation(center[0], center[1])
    # 组合：T2 * S * T1（注意顺序：从右到左应用）
    M = T2 * S * T1
    return M * point

# 以(50,50)为中心，放大2倍
print(f"以中心缩放(60,60): {scale_around_center((60,60), (50,50), 2, 2)}")
# 结果(70,70)：相对中心(50,50)偏移(10,10)，放大2倍后偏移(20,20)
```

以中心缩放的"三步法"示意：

```
目标：以C为中心，缩放因子s，变换点P

步骤1：平移使C到原点    P' = P - C
步骤2：缩放            P'' = P' × s
步骤3：平移使原点回C    P''' = P'' + C

矩阵形式：M = T(C) × S(s) × T(-C)
                  ↑     ↑     ↑
              平移回去  缩放  平移到原点

应用顺序（从右到左）：先T(-C)，再S，最后T(C)
```

#### 总结

- 缩放由 `(sx, sy)` 描述，均匀缩放保持长宽比，非均匀缩放会拉伸
- 缩放矩阵是对角矩阵，逆矩阵为各因子取倒数
- **以自身中心缩放**需"平移到原点→缩放→平移回去"三步组合
- 负缩放因子实现镜像翻转，是角色面向切换的常用技巧
- 缩放顺序与平移组合时，顺序至关重要（中心缩放就是典型例子）

---

### 第12讲：旋转变换

#### 概念

**旋转（Rotation）** 是物体绕某点转动一定角度的变换。2D旋转由一个角度θ描述，绕原点旋转θ角度。旋转矩阵是2×2矩阵（或3×3齐次形式），其推导基于三角函数。旋转是游戏中最常见的变换之一：角色面向、炮塔转向、车轮滚动、UI元素动画等。

#### 原理

绕原点旋转θ角度的2D旋转矩阵为：

```
| cos(θ) -sin(θ) 0 |
| sin(θ)  cos(θ) 0 |
|   0       0    1 |
```

推导：点 `(x, y)` 用极坐标表示为 `(r·cos(α), r·sin(α))`，旋转θ后变为 `(r·cos(α+θ), r·sin(α+θ))`，展开后用和角公式即得上式。

与缩放类似，**旋转中心**是个关键问题。默认旋转绕原点，物体在旋转的同时会"公转"。若想绕自身中心旋转（自转），需要"平移到原点→旋转→平移回去"的三步组合，与中心缩放思路完全一致。

旋转矩阵是**正交矩阵**，其逆矩阵等于转置矩阵，物理意义是"逆旋转就是反向旋转角度"。这在摄像机变换、坐标空间转换中极为有用。

需要注意屏幕坐标系下y轴向下，正角度通常表示顺时针旋转，与数学坐标系相反。许多引擎（如Unity 2D）会自动处理这一点，但手写矩阵时需留意。

#### 例子

```python
import math

class Matrix3x3:
    def __init__(self, m=None):
        self.m = m if m else [[1,0,0],[0,1,0],[0,0,1]]
    
    @classmethod
    def translation(cls, tx, ty):
        return cls([[1,0,tx],[0,1,ty],[0,0,1]])
    
    @classmethod
    def rotation(cls, angle_deg):
        """旋转矩阵（屏幕坐标系：正角度=顺时针）"""
        rad = math.radians(angle_deg)
        c, s = math.cos(rad), math.sin(rad)
        return cls([[c,-s,0],[s,c,0],[0,0,1]])
    
    def __mul__(self, other):
        if isinstance(other, Matrix3x3):
            result = [[0]*3 for _ in range(3)]
            for i in range(3):
                for j in range(3):
                    for k in range(3):
                        result[i][j] += self.m[i][k] * other.m[k][j]
            return Matrix3x3(result)
        elif isinstance(other, tuple):
            x, y = other
            return (self.m[0][0]*x + self.m[0][1]*y + self.m[0][2],
                    self.m[1][0]*x + self.m[1][1]*y + self.m[1][2])

# 应用1：绕原点旋转
R = Matrix3x3.rotation(90)  # 顺时针90°
print(f"旋转(1,0)后: {R * (1, 0)}")  # 约(0, 1) 向右变向下（屏幕坐标系）

# 应用2：绕自身中心旋转（关键技巧）
def rotate_around_center(point, center, angle_deg):
    """以center为中心旋转point"""
    T1 = Matrix3x3.translation(-center[0], -center[1])
    R = Matrix3x3.rotation(angle_deg)
    T2 = Matrix3x3.translation(center[0], center[1])
    M = T2 * R * T1  # 从右到左：T1→R→T2
    return M * point

# 角色在(100,100)，绕自身中心旋转90°
# 角色右手的相对位置(20,0)，旋转后应在(0,20)（屏幕坐标系下方）
hand_local = (20, 0)
center = (100, 100)
new_hand = rotate_around_center(hand_local, center, 90)
print(f"旋转后手位置: {new_hand}")  # 约(100, 120)

# 应用3：炮塔旋转面向目标
def rotate_turret_to_target(turret_pos, target_pos, current_angle):
    """让炮塔旋转面向目标，返回新角度"""
    dx = target_pos[0] - turret_pos[0]
    dy = target_pos[1] - turret_pos[1]
    target_angle = math.degrees(math.atan2(dy, dx))
    # 平滑过渡到目标角度（避免瞬间转向）
    diff = target_angle - current_angle
    # 处理角度环绕（如359°→1°应走2°而非-358°）
    while diff > 180: diff -= 360
    while diff < -180: diff += 360
    return current_angle + diff * 0.1  # 每帧转10%

# 应用4：车轮旋转效果（基于速度）
wheel_angle = 0
wheel_radius = 20
velocity = 100  # 像素/秒
dt = 0.016
# 旋转角度 = 移动距离 / 周长 × 360°
wheel_angle += (velocity * dt) / (2 * math.pi * wheel_radius) * 360
```

旋转矩阵的推导与结构：

```
点 P = (r·cosα, r·sinα)
旋转θ后：P' = (r·cos(α+θ), r·sin(α+θ))

展开：
  cos(α+θ) = cosα·cosθ - sinα·sinθ
  sin(α+θ) = sinα·cosθ + cosα·sinθ

所以：
  x' = x·cosθ - y·sinθ
  y' = x·sinθ + y·cosθ

矩阵形式：
  | x' |   | cosθ -sinθ |   | x |
  |    | = |            | × |   |
  | y' |   | sinθ  cosθ |   | y |
```

#### 总结

- 旋转矩阵由cos和sin构成，正角度在屏幕坐标系下为顺时针
- **绕自身中心旋转**需"平移到原点→旋转→平移回去"三步组合
- 旋转矩阵是正交矩阵，逆=转置，逆旋转即反向旋转
- 炮塔转向、角色面向、车轮滚动都依赖旋转
- 角度差计算要处理环绕（359°与1°实际只差2°）

---

### 第13讲：复合变换与齐次坐标

#### 概念

**复合变换（Composite Transformation）** 是将多个基本变换（平移、缩放、旋转）通过矩阵乘法组合为一个矩阵的过程。**齐次坐标（Homogeneous Coordinates）** 是用n+1维坐标表示n维点的方法，使所有基本变换都能用统一大小的方阵表达，从而支持矩阵乘法组合。这是游戏引擎变换系统的数学基石。

#### 原理

齐次坐标的核心思想：2D点 `(x, y)` 表示为 `(x, y, w)`，其中w通常为1。这样平移、旋转、缩放都能用3×3矩阵表达，且可以通过矩阵乘法组合。

复合变换的关键规则是**矩阵顺序与变换顺序相反**：若想"先缩放S，再旋转R，最后平移T"，矩阵乘法是 `M = T × R × S`，应用时 `M × v` 等价于 `T(R(S(v)))`——从右往左执行。这是因为矩阵乘法 `M × v` 中v先与最右侧矩阵相乘。

游戏中的标准变换顺序是 **SRT（Scale → Rotate → Translate）**：先以原点为中心缩放，再绕原点旋转，最后平移到目标位置。这个顺序确保物体在自身坐标系中正确缩放和旋转，最后整体移动到世界位置。若顺序错误，物体会"飘走"或"扭曲"。

复合矩阵的性能优势：与其每帧对每个顶点应用3次变换（9次乘法），不如预先组合成1个矩阵（1次矩阵乘法 = 9次乘法），然后对每个顶点只做1次矩阵乘法。在渲染大量顶点时，这能节省巨量计算。

#### 例子

```python
import math

class Matrix3x3:
    def __init__(self, m=None):
        self.m = m if m else [[1,0,0],[0,1,0],[0,0,1]]
    
    @classmethod
    def translation(cls, tx, ty):
        return cls([[1,0,tx],[0,1,ty],[0,0,1]])
    
    @classmethod
    def scaling(cls, sx, sy):
        return cls([[sx,0,0],[0,sy,0],[0,0,1]])
    
    @classmethod
    def rotation(cls, angle_deg):
        rad = math.radians(angle_deg)
        c, s = math.cos(rad), math.sin(rad)
        return cls([[c,-s,0],[s,c,0],[0,0,1]])
    
    def __mul__(self, other):
        if isinstance(other, Matrix3x3):
            result = [[0]*3 for _ in range(3)]
            for i in range(3):
                for j in range(3):
                    for k in range(3):
                        result[i][j] += self.m[i][k] * other.m[k][j]
            return Matrix3x3(result)
        elif isinstance(other, tuple):
            x, y = other
            return (self.m[0][0]*x + self.m[0][1]*y + self.m[0][2],
                    self.m[1][0]*x + self.m[1][1]*y + self.m[1][2])

# 应用1：标准SRT复合变换
# 角色：缩放2倍，旋转45°，平移到(100,100)
S = Matrix3x3.scaling(2, 2)
R = Matrix3x3.rotation(45)
T = Matrix3x3.translation(100, 100)
# 注意顺序：T × R × S（从右到左执行：先S再R再T）
M = T * R * S

# 角色本地坐标的原点(0,0)变换后应在(100,100)
print(f"原点变换: {M * (0, 0)}")  # (100, 100)
# 角色本地坐标(10,0)（右手位置）变换后
print(f"右手变换: {M * (10, 0)}")  # 旋转45°并放大2倍并平移

# 应用2：父子层级变换（如角色手持武器）
# 角色变换：平移到(200,200)，旋转30°
character_M = Matrix3x3.translation(200, 200) * Matrix3x3.rotation(30)
# 武器相对角色的本地位置(15, 0)（在角色右手）
# 武器世界坐标 = 角色变换 × 武器本地坐标
weapon_world = character_M * (15, 0)
print(f"武器世界坐标: {weapon_world}")

# 应用3：摄像机变换（世界→屏幕）
# 摄像机位置(150,100)，缩放0.5（缩小看更大范围），旋转10°
cam_T = Matrix3x3.translation(-150, -100)  # 世界平移使摄像机到原点
cam_R = Matrix3x3.rotation(-10)  # 反向旋转
cam_S = Matrix3x3.scaling(0.5, 0.5)  # 缩放
view_matrix = cam_S * cam_R * cam_T  # 视图矩阵
# 世界点(300,200)在屏幕上的位置
print(f"世界点屏幕坐标: {view_matrix * (300, 200)}")

# 应用4：骨骼动画的基础（每个骨骼有相对父骨骼的变换）
class Bone:
    def __init__(self, name, local_transform, parent=None):
        self.name = name
        self.local = local_transform  # 相对父骨骼的变换
        self.parent = parent
        self.world = Matrix3x3()  # 计算得到的世界变换
    
    def update_world(self):
        if self.parent:
            self.world = self.parent.world * self.local
        else:
            self.world = self.local

# 手臂骨骼层级
root = Bone("root", Matrix3x3.translation(100, 100))
upper_arm = Bone("upper", Matrix3x3.translation(0, 0) * Matrix3x3.rotation(30), root)
lower_arm = Bone("lower", Matrix3x3.translation(40, 0) * Matrix3x3.rotation(45), upper_arm)
# 更新世界变换
root.update_world()
upper_arm.update_world()
lower_arm.update_world()
print(f"前臂世界原点: {lower_arm.world * (0, 0)}")
```

复合变换的顺序规则示意：

```
目标：先缩放S，再旋转R，最后平移T
矩阵：M = T × R × S（注意顺序与执行顺序相反）

应用 M × v 等价于：
  v1 = S × v       （先缩放）
  v2 = R × v1      （再旋转）
  v3 = T × v2      （最后平移）

为什么顺序相反？
  M × v = (T × R × S) × v = T × (R × (S × v))
  从内到外执行：先S，再R，最后T ✓

常见错误：
  M = S × R × T  → 实际执行顺序是 T→R→S（错误！）
  这会导致物体先平移到远处，再旋转（绕原点公转），最后缩放（位置也缩放）
```

#### 总结

- 齐次坐标用n+1维表示n维点，使所有变换能用统一方阵表达
- **复合变换矩阵顺序与执行顺序相反**：M = T × R × S 表示先S后R后T
- 游戏标准变换顺序是SRT（缩放→旋转→平移）
- 复合矩阵的性能优势：预先组合，每个顶点只做1次矩阵乘法
- 父子层级变换（骨骼、UI嵌套）通过矩阵乘法链式传递
- 摄像机变换本质是世界坐标经过"反向平移+反向旋转+缩放"得到屏幕坐标

---

## 第4章 几何图元

游戏世界由各种几何形状构成：点、线、圆、矩形。理解这些图元的数学性质，是构建碰撞检测、视野计算、空间划分的基础。本章将系统讲解2D游戏中最常用的几何图元，包括它们的表示方法、关键性质和典型应用。这些知识是下一章碰撞检测的前置基础。

### 第14讲：点、线、线段与射线

#### 概念

**点（Point）** 是2D空间中的位置，由 `(x, y)` 表示，无大小无方向。**线（Line）** 是无限延伸的一维几何体，由方向决定。**线段（Line Segment）** 是线的有限部分，由两个端点界定。**射线（Ray）** 从一个起点出发，沿一个方向无限延伸。这四者是2D几何的基础图元，在游戏中分别表示位置、轨迹、边界、子弹路径等。

#### 原理

线段与射线的核心运算是**参数方程表示**。线段AB上的任意点可表示为 `P(t) = A + t*(B - A)`，其中t∈[0,1]。t=0时P=A，t=1时P=B，t=0.5时为中点。射线类似，但t∈[0,∞)。这种参数化表示让线段上的任意点都能精确计算，是相交检测、动画路径的基础。

线段相交检测是2D游戏最高频的几何运算之一。经典算法基于**参数方程联立**：两条线段 `P1 + t*(P2-P1)` 和 `P3 + u*(P4-P3)`，若存在t、u∈[0,1]使二者相等，则相交。通过叉积可以快速判定：若 `cross(P2-P1, P4-P3) = 0` 则平行（可能重合），否则可解出t、u。

射线的典型应用是"鼠标点击检测"——从摄像机位置发射一条射线穿过鼠标点，判断与场景中哪些物体相交。这在2D游戏中简化为"点是否在图元内"，但在3D游戏中是核心交互机制。

#### 例子

```python
import math

class Vector2:
    def __init__(self, x=0.0, y=0.0):
        self.x = x; self.y = y
    def __add__(self, o): return Vector2(self.x+o.x, self.y+o.y)
    def __sub__(self, o): return Vector2(self.x-o.x, self.y-o.y)
    def __mul__(self, s): return Vector2(self.x*s, self.y*s)
    def length(self): return math.sqrt(self.x**2 + self.y**2)
    def dot(self, o): return self.x*o.x + self.y*o.y
    def cross(self, o): return self.x*o.y - self.y*o.x
    def __repr__(self): return f"V({self.x:.2f}, {self.y:.2f})"

# 线段：由两个端点定义
class Segment:
    def __init__(self, p1, p2):
        self.p1 = p1
        self.p2 = p2
    
    def point_at(self, t):
        """参数方程：t∈[0,1]返回线段上的点"""
        return self.p1 + (self.p2 - self.p1) * t
    
    def length(self):
        return (self.p2 - self.p1).length()

# 射线：由起点和方向定义
class Ray:
    def __init__(self, origin, direction):
        self.origin = origin
        self.direction = direction  # 通常归一化
    
    def point_at(self, t):
        """t≥0返回射线上的点"""
        return self.origin + self.direction * t

# 应用1：线段相交检测
def segments_intersect(p1, p2, p3, p4):
    """判断线段p1p2与线段p3p4是否相交，返回(t, u)或None"""
    r = p2 - p1  # 线段1方向
    s = p4 - p3  # 线段2方向
    rxs = r.cross(s)
    if abs(rxs) < 1e-9:  # 平行
        return None
    qp = p3 - p1
    t = qp.cross(s) / rxs
    u = qp.cross(r) / rxs
    if 0 <= t <= 1 and 0 <= u <= 1:
        return (t, u)
    return None

# 两条相交的线段
s1 = (Vector2(0,0), Vector2(100,100))
s2 = (Vector2(0,100), Vector2(100,0))
result = segments_intersect(*s1, *s2)
print(f"线段相交: {result}")  # (0.5, 0.5) 在中点相交

# 应用2：点到线段的最短距离（关键算法）
def point_to_segment_distance(p, a, b):
    """计算点p到线段ab的最短距离"""
    ab = b - a
    ap = p - a
    # 投影参数t，钳制到[0,1]
    t = ap.dot(ab) / ab.dot(ab)
    t = max(0, min(1, t))
    closest = a + ab * t  # 线段上最近的点
    return (p - closest).length()

p = Vector2(50, 100)
a = Vector2(0, 0)
b = Vector2(100, 0)
print(f"点到线段距离: {point_to_segment_distance(p, a, b)}")  # 100

# 应用3：射线与圆相交（子弹击中圆形敌人）
def ray_circle_intersect(ray_origin, ray_dir, circle_center, radius):
    """返回相交点距离t，无相交返回None"""
    oc = ray_origin - circle_center
    a = ray_dir.dot(ray_dir)  # 通常=1（归一化）
    b = 2 * oc.dot(ray_dir)
    c = oc.dot(oc) - radius * radius
    disc = b*b - 4*a*c
    if disc < 0: return None
    sqrt_disc = math.sqrt(disc)
    t1 = (-b - sqrt_disc) / (2*a)
    t2 = (-b + sqrt_disc) / (2*a)
    if t1 > 0: return t1  # 最近交点
    if t2 > 0: return t2
    return None  # 射线起点在圆内或反向

origin = Vector2(0, 50)
direction = Vector2(1, 0)  # 水平射线
center = Vector2(100, 50)
t = ray_circle_intersect(origin, direction, center, 30)
print(f"射线击中距离: {t}")  # 70（100-30）
```

线段参数方程的直观示意：

```
线段AB：A=(0,0), B=(100,0)

P(t) = A + t*(B-A), t∈[0,1]

t=0   t=0.5   t=1
 ●─────●─────────●
 A    中点       B

t<0: 在A的延长线（线段外）
t>1: 在B的延长线（线段外）

线段相交判定：
  线段1: P1 + t*(P2-P1), t∈[0,1]
  线段2: P3 + u*(P4-P3), u∈[0,1]
  
  联立解出t,u，若都在[0,1]内则相交
```

#### 总结

- 点是位置，线无限延伸，线段有限，射线单向无限
- **参数方程** `P(t) = A + t*(B-A)` 是表示线段/射线的标准方法
- 线段相交检测通过参数方程联立，t、u都在[0,1]内则相交
- 点到线段距离需先求投影参数t并钳制到[0,1]
- 射线与圆相交通过二次方程求解，是子弹击中判定的核心

---

### 第15讲：圆与球的几何

#### 概念

**圆（Circle）** 是2D中到定点距离等于定长的所有点的集合，由圆心 `(cx, cy)` 和半径 `r` 定义。圆是2D游戏中最常用的碰撞形状之一，因为其数学性质简单、检测高效。**球（Sphere）** 是圆在3D的推广，但在2D游戏中我们只关心圆。

#### 原理

圆的方程为 `(x-cx)² + (y-cy)² = r²`，展开后是关于x、y的二次方程。圆的关键性质是**对称性**——任意方向的检测复杂度相同，这是圆碰撞检测高效的根本原因。

圆的几个重要运算：
1. **点在圆内**：`(x-cx)² + (y-cy)² ≤ r²`，用平方避免开方
2. **两圆相交**：圆心距离 ≤ 半径之和，即 `|c1-c2| ≤ r1+r2`
3. **圆与直线距离**：圆心到直线的距离 ≤ 半径
4. **圆与线段相交**：圆心到线段的最短距离 ≤ 半径

圆作为碰撞形状的优势是**旋转不变性**——无论物体如何旋转，圆的形状不变，碰撞检测无需考虑方向。劣势是**精度低**——对于长条形物体（如剑、长方形），圆会包含大量"空白区域"，导致误判碰撞。因此实际游戏中常用多个小圆组合逼近复杂形状。

#### 例子

```python
import math

class Vector2:
    def __init__(self, x=0.0, y=0.0):
        self.x = x; self.y = y
    def __sub__(self, o): return Vector2(self.x-o.x, self.y-o.y)
    def length_squared(self): return self.x**2 + self.y**2
    def length(self): return math.sqrt(self.length_squared())
    def __repr__(self): return f"V({self.x:.2f}, {self.y:.2f})"

class Circle:
    def __init__(self, center, radius):
        self.center = center
        self.radius = radius
    
    def contains(self, point):
        """点是否在圆内"""
        diff = point - self.center
        return diff.length_squared() <= self.radius ** 2
    
    def intersects_circle(self, other):
        """两圆是否相交"""
        diff = other.center - self.center
        r_sum = self.radius + other.radius
        return diff.length_squared() <= r_sum ** 2
    
    def area(self):
        return math.pi * self.radius ** 2

# 应用1：点是否在圆内（如鼠标点击圆形按钮）
button = Circle(Vector2(100, 100), 30)
print(f"点(110,110)在按钮内: {button.contains(Vector2(110, 110))}")  # True
print(f"点(200,200)在按钮内: {button.contains(Vector2(200, 200))}")  # False

# 应用2：两圆碰撞（敌人之间的碰撞）
enemy1 = Circle(Vector2(100, 100), 20)
enemy2 = Circle(Vector2(130, 100), 20)
print(f"敌人碰撞: {enemy1.intersects_circle(enemy2)}")  # True（距离30 < 40）

# 应用3：用多个圆组合逼近复杂形状（如人形角色）
class MultiCircleShape:
    """由多个圆组成的复合碰撞形状"""
    def __init__(self, circles):
        self.circles = circles  # 相对中心的圆列表
    
    def intersects(self, other_shape, self_pos, other_pos):
        for c1 in self.circles:
            for c2 in other_shape.circles:
                # 把相对圆转为世界圆
                w1 = Circle(Vector2(self_pos.x + c1.center.x, 
                                    self_pos.y + c1.center.y), c1.radius)
                w2 = Circle(Vector2(other_pos.x + c2.center.x,
                                    other_pos.y + c2.center.y), c2.radius)
                if w1.intersects_circle(w2):
                    return True
        return False

# 人形：头+躯干+四肢
humanoid = MultiCircleShape([
    Circle(Vector2(0, -30), 10),   # 头
    Circle(Vector2(0, 0), 15),      # 躯干
    Circle(Vector2(-10, 20), 8),   # 左腿
    Circle(Vector2(10, 20), 8),    # 右腿
])

# 应用4：圆与线段相交（子弹击中圆形护盾）
def circle_segment_intersect(circle, a, b):
    """圆与线段是否相交"""
    # 圆心到线段的最短距离
    ab = b - a
    ap = circle.center - a
    t = ap.dot(ab) / ab.dot(ab) if ab.length_squared() > 0 else 0
    t = max(0, min(1, t))
    closest = a + ab * t
    return (circle.center - closest).length_squared() <= circle.radius ** 2

# 需要给Vector2添加dot方法
def dot_method(self, o): return self.x*o.x + self.y*o.y
Vector2.dot = dot_method
def add_method(self, o): return Vector2(self.x+o.x, self.y+o.y)
Vector2.__add__ = add_method
def mul_method(self, s): return Vector2(self.x*s, self.y*s)
Vector2.__mul__ = mul_method

shield = Circle(Vector2(100, 100), 40)
bullet_start = Vector2(0, 100)
bullet_end = Vector2(200, 100)
print(f"子弹击中护盾: {circle_segment_intersect(shield, bullet_start, bullet_end)}")  # True
```

圆的几何性质示意：

```
圆 (cx, cy, r)

         ●  圆上点（距离=r）
       ╱   ╲
      ●  ●  ●  圆内点（距离<r）
     ╱   ╳   ╲
    ●   圆心  ●
     ╲   ●   ╱  圆心点
      ●  ●  ●
       ╲   ╱
         ●

关键性质：
1. 旋转不变性：圆旋转后仍是同一个圆
2. 对称性：任意方向检测复杂度相同
3. 方程简单：(x-cx)² + (y-cy)² = r²
4. 多圆组合可逼近任意形状
```

#### 总结

- 圆由圆心和半径定义，方程 `(x-cx)² + (y-cy)² = r²`
- **圆碰撞检测高效**：只需比较圆心距离与半径之和
- 圆的优势是旋转不变、计算简单；劣势是对长形物体精度低
- **多圆组合**可逼近复杂形状，是角色碰撞的常用方案
- 圆与线段相交通过"圆心到线段最短距离≤半径"判定

---

### 第16讲：矩形与包围盒

#### 概念

**矩形（Rectangle）** 是2D中由四条边围成的四边形，对边平行且垂直。在游戏中，最常用的是**轴对齐包围盒（AABB, Axis-Aligned Bounding Box）**——边与坐标轴平行的矩形，由最小角点 `(min_x, min_y)` 和最大角点 `(max_x, max_y)` 定义。与之相对的是**有向包围盒（OBB, Oriented Bounding Box）**——可旋转的矩形，能更紧密地包裹物体。

#### 原理

AABB的核心优势是**检测极其高效**：两个AABB相交当且仅当在x轴和y轴上都重叠。x轴重叠条件是 `a.min_x ≤ b.max_x 且 a.max_x ≥ b.min_x`，y轴同理。这种"分离轴"思想是后续SAT算法的特例。

AABB的表示有两种常见方式：
1. **min-max法**：存储左上角和右下角，适合静态矩形
2. **center-size法**：存储中心和半尺寸，适合动态矩形（移动时只需更新中心）

AABB的劣势是**对旋转物体精度低**——一个长条形物体旋转45°后，其AABB会显著变大，包含大量空白。这时需要用OBB或多个AABB组合。

OBB的检测复杂得多，需要用到分离轴定理（SAT），将在第21讲详细讲解。本讲聚焦AABB。

#### 例子

```python
import math

class Vector2:
    def __init__(self, x=0.0, y=0.0):
        self.x = x; self.y = y
    def __sub__(self, o): return Vector2(self.x-o.x, self.y-o.y)
    def length(self): return math.sqrt(self.x**2 + self.y**2)
    def __repr__(self): return f"V({self.x:.2f}, {self.y:.2f})"

class AABB:
    """轴对齐包围盒：min-max表示"""
    def __init__(self, min_x, min_y, max_x, max_y):
        self.min_x = min_x
        self.min_y = min_y
        self.max_x = max_x
        self.max_y = max_y
    
    @classmethod
    def from_center(cls, cx, cy, width, height):
        """从中心和尺寸创建"""
        return cls(cx - width/2, cy - height/2,
                   cx + width/2, cy + height/2)
    
    def intersects(self, other):
        """AABB相交检测：两轴都重叠才相交"""
        # x轴分离
        if self.max_x < other.min_x or other.max_x < self.min_x:
            return False
        # y轴分离
        if self.max_y < other.min_y or other.max_y < self.min_y:
            return False
        return True
    
    def contains(self, point):
        """点是否在矩形内"""
        return (self.min_x <= point.x <= self.max_x and
                self.min_y <= point.y <= self.max_y)
    
    def center(self):
        return Vector2((self.min_x + self.max_x) / 2,
                       (self.min_y + self.max_y) / 2)
    
    def get_corners(self):
        """返回四个角点"""
        return [
            Vector2(self.min_x, self.min_y),  # 左上
            Vector2(self.max_x, self.min_y),  # 右上
            Vector2(self.max_x, self.max_y),  # 右下
            Vector2(self.min_x, self.max_y),  # 左下
        ]

# 应用1：两个AABB碰撞
box1 = AABB(0, 0, 100, 100)
box2 = AABB(50, 50, 150, 150)
print(f"AABB相交: {box1.intersects(box2)}")  # True

box3 = AABB(200, 200, 300, 300)
print(f"远处的AABB相交: {box1.intersects(box3)}")  # False

# 应用2：点是否在矩形内（UI按钮点击）
button = AABB.from_center(100, 100, 80, 40)
print(f"点击(100,100)在按钮内: {button.contains(Vector2(100, 100))}")  # True
print(f"点击(150,100)在按钮内: {button.contains(Vector2(150, 100))}")  # False

# 应用3：移动AABB（角色移动）
class MovingAABB:
    def __init__(self, cx, cy, w, h):
        self.cx, self.cy = cx, cy
        self.w, self.h = w, h
    
    def move(self, dx, dy):
        self.cx += dx
        self.cy += dy
    
    def to_aabb(self):
        return AABB.from_center(self.cx, self.cy, self.w, self.h)

player = MovingAABB(100, 100, 40, 60)
player.move(10, 0)  # 向右移动
print(f"玩家AABB: ({player.to_aabb().min_x}, {player.to_aabb().min_y})")

# 应用4：AABB合并（计算多个物体的包围盒）
def merge_aabbs(boxes):
    if not boxes: return None
    min_x = min(b.min_x for b in boxes)
    min_y = min(b.min_y for b in boxes)
    max_x = max(b.max_x for b in boxes)
    max_y = max(b.max_y for b in boxes)
    return AABB(min_x, min_y, max_x, max_y)

boxes = [AABB(0,0,10,10), AABB(100,100,150,150), AABB(50,50,60,60)]
merged = merge_aabbs(boxes)
print(f"合并后AABB: ({merged.min_x},{merged.min_y})-({merged.max_x},{merged.max_y})")
```

AABB相交检测的"分离轴"思想：

```
两个AABB相交 ⟺ x轴重叠 且 y轴重叠

情况1：x轴分离（不相交）
  box1: [───]
                    [───]  box2
  ←──x轴──→
  box1.max_x < box2.min_x → x轴分离 → 不相交

情况2：两轴都重叠（相交）
  box1: [─────]
        [─────]  box2
  ←──x轴──→  ←──y轴──→
  x轴重叠 且 y轴重叠 → 相交
```

#### 总结

- AABB是轴对齐矩形，由min-max或center-size表示
- **AABB相交检测极高效**：两轴都重叠才相交，是分离轴思想的特例
- center-size表示适合动态物体，min-max适合静态物体
- AABB对旋转物体精度低，旋转后包围盒会变大
- OBB（有向包围盒）能紧密包裹旋转物体，但检测复杂（需SAT算法）
- AABB合并用于计算多个物体的整体包围范围

---

## 第5章 碰撞检测

碰撞检测是2D游戏的核心系统，决定了"物体是否接触"这一关键交互。从简单的点是否在矩形内，到复杂的SAT分离轴定理，本章将系统讲解2D游戏中所有常用碰撞检测算法。掌握这些算法，你就能实现从平台跳跃到弹球游戏、从射击游戏到格斗游戏的各类碰撞交互。本章是游戏数学最具实战价值的部分。

### 第17讲：点与图元碰撞

#### 概念

**点与图元碰撞** 是最基础的碰撞检测，判断一个点是否位于某个几何图元内部。这是鼠标点击检测、粒子命中判定、触发器判定的基础。常见组合包括点-圆、点-矩形、点-多边形等。

#### 原理

点-圆碰撞：点P在圆C内当且仅当 `|P - C.center| ≤ C.radius`。用平方形式 `|P-C|² ≤ r²` 避免开方，提升性能。

点-矩形碰撞：点P在矩形内当且仅当 `min_x ≤ P.x ≤ max_x 且 min_y ≤ P.y ≤ max_y`。这是最简单的碰撞检测，只需4次比较。

点-多边形碰撞有两种经典算法：
1. **射线法（Ray Casting）**：从点向任意方向发射射线，统计与多边形边的交点数。奇数次则在内部，偶数次则在外部。这是最通用的算法。
2. **叉积同号法**：仅适用于凸多边形。点在凸多边形内当且仅当对每条边，点都在同一侧（叉积同号）。

射线法的原理直观：从点出发的射线若要"逃出"多边形，必须穿过边界奇数次（进去一次出来一次为偶数，进去一次为内部）。这个算法对凹多边形也成立，是点-多边形检测的通用方案。

#### 例子

```python
import math

class Vector2:
    def __init__(self, x=0.0, y=0.0):
        self.x = x; self.y = y
    def __sub__(self, o): return Vector2(self.x-o.x, self.y-o.y)
    def length_squared(self): return self.x**2 + self.y**2
    def cross(self, o): return self.x*o.y - self.y*o.x
    def dot(self, o): return self.x*o.x + self.y*o.y
    def __repr__(self): return f"V({self.x:.2f}, {self.y:.2f})"

# 点-圆碰撞
def point_in_circle(p, center, radius):
    return (p - center).length_squared() <= radius ** 2

# 点-矩形碰撞
def point_in_rect(p, min_x, min_y, max_x, max_y):
    return min_x <= p.x <= max_x and min_y <= p.y <= max_y

# 点-凸多边形碰撞（叉积同号法）
def point_in_convex_polygon(p, vertices):
    """vertices为凸多边形顶点列表（顺时针或逆时针）"""
    n = len(vertices)
    sign = 0
    for i in range(n):
        j = (i + 1) % n
        edge = vertices[j] - vertices[i]
        to_point = p - vertices[i]
        cross = edge.cross(to_point)
        if cross != 0:
            if sign == 0:
                sign = 1 if cross > 0 else -1
            elif (cross > 0) != (sign > 0):
                return False
    return True

# 点-任意多边形碰撞（射线法，对凹多边形也成立）
def point_in_polygon(p, vertices):
    """射线法：从p向右发射水平射线，统计与边的交点数"""
    n = len(vertices)
    inside = False
    j = n - 1
    for i in range(n):
        xi, yi = vertices[i].x, vertices[i].y
        xj, yj = vertices[j].x, vertices[j].y
        # 判断射线是否与边vi-vj相交
        if ((yi > p.y) != (yj > p.y)) and \
           (p.x < (xj - xi) * (p.y - yi) / (yj - yi) + xi):
            inside = not inside
        j = i
    return inside

# 应用1：鼠标点击圆形按钮
button_center = Vector2(100, 100)
button_radius = 30
click = Vector2(110, 110)
print(f"点击圆形按钮: {point_in_circle(click, button_center, button_radius)}")  # True

# 应用2：鼠标点击矩形按钮
print(f"点击矩形按钮: {point_in_rect(click, 50, 80, 150, 120)}")  # True

# 应用3：角色是否在凸多边形区域内（如安全区）
safe_zone = [Vector2(0,0), Vector2(100,0), Vector2(100,100), Vector2(0,100)]
player = Vector2(50, 50)
print(f"玩家在安全区: {point_in_convex_polygon(player, safe_zone)}")  # True

# 应用4：角色是否在凹多边形内（如L形房间）
l_shaped = [
    Vector2(0,0), Vector2(100,0), Vector2(100,50),
    Vector2(50,50), Vector2(50,100), Vector2(0,100)
]
print(f"在L形(20,20): {point_in_polygon(Vector2(20,20), l_shaped)}")  # True
print(f"在L形(70,70): {point_in_polygon(Vector2(70,70), l_shaped)}")  # False
```

射线法原理示意：

```
凹多边形（L形）：
●─────────●
│         │
│   ●─────●
│   │
│   │  ← 点(70,70)在外部
│   │
●───●

从点(70,70)向右发射射线：
  ──────────────→
  与边相交1次（奇数）→ 在内部？不，这里相交0次（偶数）→ 在外部

从点(20,20)向右发射射线：
  ──────────────→
  与边相交1次（奇数）→ 在内部 ✓

规则：奇数次相交=内部，偶数次相交=外部
```

#### 总结

- 点-圆碰撞用距离平方比较，避免开方
- 点-矩形碰撞只需4次比较，是最简单的检测
- **凸多边形**用叉积同号法，高效但仅限凸
- **任意多边形**用射线法，统计交点奇偶性
- 射线法是鼠标点击检测、触发器判定的通用方案

---

### 第18讲：圆与圆碰撞

#### 概念

**圆与圆碰撞** 是最简单的形状间碰撞检测。两圆相交当且仅当圆心距离小于半径之和。这是2D游戏中最常用的碰撞方式，因为计算简单、性能高，适合大量物体（如子弹、粒子）的碰撞判定。

#### 原理

两圆C1(center1, r1)和C2(center2, r2)相交的充要条件是 `|center1 - center2| ≤ r1 + r2`。用平方形式 `|c1-c2|² ≤ (r1+r2)²` 避免开方，性能更优。

圆碰撞的几个变体：
1. **完全包含**：`|c1-c2| + r2 ≤ r1`，小圆完全在大圆内
2. **外切**：`|c1-c2| = r1 + r2`，两圆刚好接触
3. **相交**：`|r1-r2| < |c1-c2| < r1+r2`，两圆有重叠区域
4. **相离**：`|c1-c2| > r1+r2`，两圆无交集

圆碰撞的高效性来自其**对称性**——任意方向的检测复杂度相同，无需考虑旋转。这使得圆成为子弹、粒子、小型敌人等大量物体的首选碰撞形状。

实际游戏中，常为不同物体设置不同半径的"碰撞圆"，而非使用完整贴图尺寸。例如角色贴图可能是100×100像素，但碰撞圆半径设为30，让游戏手感更宽容（玩家不会因为擦边而判定为碰撞）。

#### 例子

```python
import math

class Vector2:
    def __init__(self, x=0.0, y=0.0):
        self.x = x; self.y = y
    def __sub__(self, o): return Vector2(self.x-o.x, self.y-o.y)
    def length_squared(self): return self.x**2 + self.y**2
    def length(self): return math.sqrt(self.length_squared())
    def normalize(self):
        l = self.length()
        return Vector2(0,0) if l < 1e-9 else Vector2(self.x/l, self.y/l)
    def __mul__(self, s): return Vector2(self.x*s, self.y*s)
    def __add__(self, o): return Vector2(self.x+o.x, self.y+o.y)
    def __repr__(self): return f"V({self.x:.2f}, {self.y:.2f})"

class Circle:
    def __init__(self, center, radius):
        self.center = center
        self.radius = radius
    
    def collides(self, other):
        """两圆是否碰撞"""
        diff = other.center - self.center
        r_sum = self.radius + other.radius
        return diff.length_squared() <= r_sum ** 2
    
    def contains(self, other):
        """self是否完全包含other"""
        diff = other.center - self.center
        return diff.length() + other.radius <= self.radius
    
    def get_collision_normal(self, other):
        """返回碰撞法线（从self指向other的方向）"""
        diff = other.center - self.center
        return diff.normalize()

# 应用1：基础圆碰撞
player = Circle(Vector2(100, 100), 30)
enemy = Circle(Vector2(120, 100), 30)
print(f"玩家与敌人碰撞: {player.collides(enemy)}")  # True（距离20 < 60）

# 应用2：子弹与敌人碰撞（大量检测）
def check_bullet_hits(bullets, enemies):
    """检测所有子弹与敌人的碰撞"""
    hits = []
    for i, bullet in enumerate(bullets):
        for j, enemy in enumerate(enemies):
            if bullet.collides(enemy):
                hits.append((i, j))
    return hits

bullets = [Circle(Vector2(50,50), 5), Circle(Vector2(200,200), 5)]
enemies = [Circle(Vector2(55,55), 20), Circle(Vector2(300,300), 20)]
print(f"命中: {check_bullet_hits(bullets, enemies)}")  # [(0, 0)]

# 应用3：碰撞响应——分离两物体
def resolve_collision(c1, c2):
    """碰撞后将两圆分离，避免重叠"""
    diff = c2.center - c1.center
    dist = diff.length()
    if dist < 1e-9:  # 完全重合
        return Vector2(1, 0)
    overlap = c1.radius + c2.radius - dist
    if overlap <= 0:
        return None  # 未碰撞
    # 沿法线方向分离
    normal = diff.normalize()
    separation = normal * (overlap / 2)
    return separation  # 各自移动overlap/2

c1 = Circle(Vector2(100, 100), 30)
c2 = Circle(Vector2(120, 100), 30)  # 重叠10
sep = resolve_collision(c1, c2)
if sep:
    c1.center = c1.center - sep
    c2.center = c2.center + sep
    print(f"分离后距离: {(c2.center - c1.center).length()}")  # 60（刚好相切）

# 应用4：触发区域（玩家进入触发圆）
class TriggerZone:
    def __init__(self, center, radius, on_enter=None):
        self.circle = Circle(center, radius)
        self.on_enter = on_enter
        self.occupied = False  # 上一帧是否有玩家
    
    def update(self, player_circle):
        inside = self.circle.collides(player_circle)
        if inside and not self.occupied and self.on_enter:
            self.on_enter()  # 触发进入事件
        self.occupied = inside

def on_player_enter():
    print("玩家进入了触发区域！")

zone = TriggerZone(Vector2(200, 200), 50, on_enter=on_player_enter)
player = Circle(Vector2(100, 100), 20)
zone.update(player)  # 无触发
player.center = Vector2(220, 220)
zone.update(player)  # 触发"玩家进入了触发区域！"
```

圆碰撞与分离响应示意：

```
碰撞前（重叠）：
  ◯◯◯  ← c1 (r=30)
       ◯◯◯  ← c2 (r=30)
  距离=20，重叠=60-20=40

分离后（相切）：
  ◯◯◯
          ◯◯◯
  距离=60（=r1+r2），刚好相切

分离方向：沿圆心连线，各自移动overlap/2
```

#### 总结

- 两圆碰撞条件：圆心距离 ≤ 半径之和
- 用平方形式避免开方，性能最优
- 圆碰撞对称性好，适合大量物体（子弹、粒子）
- **碰撞响应**：沿法线方向分离，避免物体重叠
- 触发区域用圆碰撞实现"进入区域"事件
- 实际游戏中常缩小碰撞圆半径，让游戏手感更宽容

---

### 第19讲：AABB碰撞检测

#### 概念

**AABB碰撞检测** 是两个轴对齐矩形之间的碰撞判定。基于"分离轴"思想：两个AABB相交当且仅当在x轴和y轴上都重叠。这是2D游戏中最常用的碰撞检测方式，适合平台跳跃、Tile地图、矩形物体等场景。

#### 原理

AABB碰撞的核心是**分离轴定理（SAT）的特例**。两个AABB不相交当且仅当存在一条轴（x轴或y轴）使它们在该轴上的投影不重叠。因此检测只需检查两个轴：

- x轴重叠：`a.min_x ≤ b.max_x 且 a.max_x ≥ b.min_x`
- y轴重叠：`a.min_y ≤ b.max_y 且 a.max_y ≥ b.min_y`

两者都满足才相交。这种检测只需4次比较，极其高效。

AABB碰撞的常见应用：
1. **平台跳跃**：角色与平台的落地判定
2. **Tile地图**：角色与瓦片墙的碰撞
3. **矩形物体**：箱子、门、家具等
4. **粗筛**：作为复杂形状（如多边形）的预检测，先排除明显不相交的物体

AABB的局限是无法处理旋转物体。若物体旋转，其AABB会变大，导致精度下降。这时需要用OBB或分解为多个AABB。

碰撞响应（分离）需要计算**最小平移向量（MTV, Minimum Translation Vector）**——将两物体分离所需的最小位移。对AABB，MTV沿x轴或y轴方向，取重叠较小者。

#### 例子

```python
import math

class AABB:
    def __init__(self, min_x, min_y, max_x, max_y):
        self.min_x, self.min_y = min_x, min_y
        self.max_x, self.max_y = max_x, max_y
    
    @classmethod
    def from_center(cls, cx, cy, w, h):
        return cls(cx-w/2, cy-h/2, cx+w/2, cy+h/2)
    
    def intersects(self, other):
        """AABB相交检测"""
        if self.max_x < other.min_x or other.max_x < self.min_x:
            return False
        if self.max_y < other.min_y or other.max_y < self.min_y:
            return False
        return True
    
    def get_overlap(self, other):
        """计算x、y轴的重叠量"""
        overlap_x = min(self.max_x, other.max_x) - max(self.min_x, other.min_x)
        overlap_y = min(self.max_y, other.max_y) - max(self.min_y, other.min_y)
        return overlap_x, overlap_y
    
    def get_mtv(self, other):
        """最小平移向量：分离两物体所需的最小位移"""
        ox, oy = self.get_overlap(other)
        if ox <= 0 or oy <= 0:
            return None  # 不相交
        # 取较小重叠轴作为分离方向
        if ox < oy:
            return (ox if self.min_x < other.min_x else -ox, 0)
        else:
            return (0, oy if self.min_y < other.min_y else -oy)

# 应用1：基础AABB碰撞
box1 = AABB(0, 0, 100, 100)
box2 = AABB(50, 50, 150, 150)
print(f"碰撞: {box1.intersects(box2)}")  # True

# 应用2：平台跳跃——角色落地判定
class Platformer:
    def __init__(self):
        self.player = AABB.from_center(100, 50, 30, 60)
        self.platforms = [AABB(0, 200, 400, 250)]
        self.on_ground = False
    
    def update(self, dy):
        # 应用重力
        self.player.min_y += dy
        self.player.max_y += dy
        # 检测落地
        self.on_ground = False
        for plat in self.platforms:
            if self.player.intersects(plat):
                mtv = self.player.get_mtv(plat)
                if mtv and mtv[1] != 0:  # y方向分离
                    self.player.min_y += mtv[1]
                    self.player.max_y += mtv[1]
                    self.on_ground = True

game = Platformer()
game.update(160)  # 下落
print(f"玩家落地: {game.on_ground}")  # True

# 应用3：Tile地图碰撞
class TileMap:
    def __init__(self, width, height, tile_size):
        self.width = width
        self.height = height
        self.tile_size = tile_size
        # 0=空地，1=墙
        self.tiles = [[0]*width for _ in range(height)]
        # 设置一些墙
        for x in range(5, 10):
            self.tiles[10][x] = 1
    
    def get_solid_tiles_near(self, aabb):
        """获取与AABB可能相交的瓦片"""
        min_tx = max(0, int(aabb.min_x // self.tile_size))
        max_tx = min(self.width-1, int(aabb.max_x // self.tile_size))
        min_ty = max(0, int(aabb.min_y // self.tile_size))
        max_ty = min(self.height-1, int(aabb.max_y // self.tile_size))
        solids = []
        for ty in range(min_ty, max_ty+1):
            for tx in range(min_tx, max_tx+1):
                if self.tiles[ty][tx] == 1:
                    solids.append(AABB(
                        tx*self.tile_size, ty*self.tile_size,
                        (tx+1)*self.tile_size, (ty+1)*self.tile_size
                    ))
        return solids

# 应用4：AABB作为粗筛（优化大量碰撞检测）
def broad_phase(objects, target):
    """用AABB粗筛，排除明显不相交的物体"""
    candidates = []
    for obj in objects:
        if obj.aabb.intersects(target.aabb):
            candidates.append(obj)  # 进入精确检测
    return candidates

# 假设有1000个物体，只有5个AABB相交
# 粗筛后只对5个做精确检测，性能提升200倍
```

AABB碰撞与MTV示意：

```
两个AABB重叠：
  ┌──────┐
  │ box1 │
  │   ┌──┼────┐
  │   │  │    │  重叠区域
  └───┼──┘    │
      │  box2 │
      └───────┘

x轴重叠 = box1.max_x - box2.min_x
y轴重叠 = box1.max_y - box2.min_y

MTV选择较小重叠轴：
  若x重叠 < y重叠 → 沿x轴分离
  若y重叠 < x重叠 → 沿y轴分离

分离方向：将box1推向"远离box2"的方向
```

#### 总结

- AABB碰撞基于分离轴思想：两轴都重叠才相交
- 检测只需4次比较，极其高效
- **MTV（最小平移向量）**用于碰撞响应，沿较小重叠轴分离
- 平台跳跃、Tile地图、矩形物体都用AABB碰撞
- AABB常作为粗筛，排除明显不相交的物体，优化性能
- 局限：无法处理旋转物体，需用OBB或多AABB组合

---

### 第20讲：圆与矩形碰撞

#### 概念

**圆与矩形碰撞** 是混合形状碰撞的典型例子。检测一个圆是否与AABB相交，需要找到矩形上离圆心最近的点，然后比较距离。这种检测在游戏中极为常见：圆形角色与矩形墙、圆形子弹与矩形敌人、圆形碰撞框与Tile地图等。

#### 原理

圆与AABB碰撞的核心算法：
1. 找到矩形上离圆心最近的点：将圆心坐标钳制到矩形范围内
2. 计算圆心到该最近点的距离
3. 若距离 ≤ 半径，则碰撞

最近点的计算：对圆心(cx, cy)，最近点为 `(clamp(cx, min_x, max_x), clamp(cy, min_y, max_y))`。clamp函数将值限制在范围内。

这个算法的精妙之处在于**统一处理所有情况**：
- 圆心在矩形内：最近点就是圆心，距离为0，必然碰撞
- 圆心在矩形外但靠近某边：最近点在该边上
- 圆心在矩形外且靠近某角：最近点在该角上

碰撞响应（分离）的方向就是从最近点指向圆心的方向，分离距离为 `radius - distance`。

#### 例子

```python
import math

class Vector2:
    def __init__(self, x=0.0, y=0.0):
        self.x = x; self.y = y
    def __sub__(self, o): return Vector2(self.x-o.x, self.y-o.y)
    def length_squared(self): return self.x**2 + self.y**2
    def length(self): return math.sqrt(self.length_squared())
    def normalize(self):
        l = self.length()
        return Vector2(0,0) if l < 1e-9 else Vector2(self.x/l, self.y/l)
    def __mul__(self, s): return Vector2(self.x*s, self.y*s)
    def __repr__(self): return f"V({self.x:.2f}, {self.y:.2f})"

def clamp(v, lo, hi):
    return max(lo, min(hi, v))

class AABB:
    def __init__(self, min_x, min_y, max_x, max_y):
        self.min_x, self.min_y = min_x, min_y
        self.max_x, self.max_y = max_x, max_y
    
    def closest_point(self, point):
        """矩形上离point最近的点"""
        return Vector2(
            clamp(point.x, self.min_x, self.max_x),
            clamp(point.y, self.min_y, self.max_y)
        )

class Circle:
    def __init__(self, center, radius):
        self.center = center
        self.radius = radius
    
    def collides_aabb(self, aabb):
        """圆与AABB碰撞检测"""
        closest = aabb.closest_point(self.center)
        diff = self.center - closest
        return diff.length_squared() <= self.radius ** 2
    
    def get_collision_normal_aabb(self, aabb):
        """返回碰撞法线（从矩形指向圆心）"""
        closest = aabb.closest_point(self.center)
        diff = self.center - closest
        if diff.length_squared() < 1e-9:
            # 圆心在矩形内，返回任意方向（通常向上）
            return Vector2(0, -1)
        return diff.normalize()

# 应用1：基础圆-矩形碰撞
rect = AABB(100, 100, 200, 200)
circle1 = Circle(Vector2(150, 150), 30)  # 圆心在矩形内
circle2 = Circle(Vector2(220, 150), 30)  # 圆心在矩形外但靠近右边
circle3 = Circle(Vector2(300, 150), 30)  # 远离矩形
print(f"圆1碰撞: {circle1.collides_aabb(rect)}")  # True
print(f"圆2碰撞: {circle2.collides_aabb(rect)}")  # True
print(f"圆3碰撞: {circle3.collides_aabb(rect)}")  # False

# 应用2：圆形角色与Tile地图碰撞
class CircleCharacter:
    def __init__(self, pos, radius):
        self.circle = Circle(pos, radius)
        self.velocity = Vector2(0, 0)
    
    def move_and_collide(self, tiles, dt):
        """移动并处理与瓦片的碰撞"""
        # 先沿x轴移动
        self.circle.center.x += self.velocity.x * dt
        for tile in tiles:
            if self.circle.collides_aabb(tile):
                normal = self.circle.get_collision_normal_aabb(tile)
                # 沿法线方向分离
                closest = tile.closest_point(self.circle.center)
                penetration = self.circle.radius - (self.circle.center - closest).length()
                self.circle.center = self.circle.center + normal * penetration
                self.velocity.x = 0  # 停止x方向运动
        
        # 再沿y轴移动
        self.circle.center.y += self.velocity.y * dt
        for tile in tiles:
            if self.circle.collides_aabb(tile):
                normal = self.circle.get_collision_normal_aabb(tile)
                closest = tile.closest_point(self.circle.center)
                penetration = self.circle.radius - (self.circle.center - closest).length()
                self.circle.center = self.circle.center + normal * penetration
                self.velocity.y = 0

# 应用3：圆形子弹击中矩形敌人
def bullet_hits_enemy(bullet, enemy_aabb):
    return bullet.collides_aabb(enemy_aabb)

# 应用4：圆形触发器与矩形区域
def circle_in_rect_zone(circle, zone):
    """圆是否完全在矩形区域内"""
    return (zone.min_x + circle.radius <= circle.center.x <= zone.max_x - circle.radius and
            zone.min_y + circle.radius <= circle.center.y <= zone.max_y - circle.radius)
```

圆与矩形碰撞的三种情况：

```
情况1：圆心在矩形内（最近点=圆心，距离=0）
  ┌──────────┐
  │          │
  │    ●←圆心│  必然碰撞
  │   ◯      │
  └──────────┘

情况2：圆心在矩形外，靠近某边
  ┌──────────┐
  │          │
  │       ●  │←最近点（在边上）
  └──────◯───┘  距离≤半径则碰撞

情况3：圆心在矩形外，靠近某角
  ┌──────────┐
  │          │
  │          │●  ←最近点（在角上）
  └──────────◯  距离≤半径则碰撞
```

#### 总结

- 圆-矩形碰撞核心：找矩形上离圆心最近的点，比较距离
- 最近点通过clamp函数计算，统一处理所有情况
- 碰撞响应方向：从最近点指向圆心
- 圆形角色与Tile地图、圆形子弹与矩形敌人都用此算法
- 圆心在矩形内时，法线方向需特殊处理（通常返回固定方向）

---

### 第21讲：SAT分离轴定理

#### 概念

**分离轴定理（SAT, Separating Axis Theorem）** 是2D碰撞检测的"终极算法"。它指出：**两个凸多边形不相交当且仅当存在一条轴，使两多边形在该轴上的投影不重叠**。这条轴称为"分离轴"。SAT能处理任意凸多边形（包括旋转矩形OBB、三角形、五边形等），是2D碰撞检测的通用方案。

#### 原理

SAT的核心思想是"投影分离"。对两个凸多边形，若存在任何一条轴使它们的投影不重叠，则它们不相交。关键问题是：**需要检查哪些轴？**

答案：**只需检查两多边形所有边的法线方向**。这是因为若存在分离轴，必然存在一条与某条边平行的分离轴（即边法线方向）。对两个多边形，共有 `m+n` 条边（m、n为各自边数），因此需检查 `m+n` 条轴。

SAT算法步骤：
1. 收集两多边形所有边的法线方向（候选分离轴）
2. 对每条轴，将两多边形的所有顶点投影到该轴
3. 检查投影区间是否重叠
4. 若所有轴都重叠，则相交；若任一轴分离，则不相交

SAT的优势是**通用性**——一个算法处理所有凸多边形。劣势是复杂度 `O(m*n)`，对边数多的多边形性能下降。实践中常先用AABB粗筛，再用SAT精确检测。

SAT还能计算**最小平移向量（MTV）**——在所有重叠轴中，重叠最小者对应的方向就是MTV，用于碰撞响应。

#### 例子

```python
import math

class Vector2:
    def __init__(self, x=0.0, y=0.0):
        self.x = x; self.y = y
    def __sub__(self, o): return Vector2(self.x-o.x, self.y-o.y)
    def __add__(self, o): return Vector2(self.x+o.x, self.y+o.y)
    def __mul__(self, s): return Vector2(self.x*s, self.y*s)
    def dot(self, o): return self.x*o.x + self.y*o.y
    def length(self): return math.sqrt(self.x**2 + self.y**2)
    def normalize(self):
        l = self.length()
        return Vector2(0,0) if l < 1e-9 else Vector2(self.x/l, self.y/l)
    def perpendicular(self):
        """返回垂直向量（法线）"""
        return Vector2(-self.y, self.x)
    def __repr__(self): return f"V({self.x:.2f}, {self.y:.2f})"

class Polygon:
    def __init__(self, vertices):
        self.vertices = vertices
    
    def get_edges(self):
        """获取所有边（作为向量）"""
        edges = []
        n = len(self.vertices)
        for i in range(n):
            j = (i + 1) % n
            edges.append(self.vertices[j] - self.vertices[i])
        return edges
    
    def get_axes(self):
        """获取所有边的法线（候选分离轴）"""
        axes = []
        for edge in self.get_edges():
            axes.append(edge.perpendicular().normalize())
        return axes
    
    def project(self, axis):
        """将多边形投影到轴上，返回(min, max)"""
        projections = [v.dot(axis) for v in self.vertices]
        return min(projections), max(projections)

def sat_collide(poly1, poly2):
    """SAT碰撞检测：返回(是否碰撞, MTV)"""
    axes = poly1.get_axes() + poly2.get_axes()
    min_overlap = float('inf')
    mtv_axis = None
    
    for axis in axes:
        min1, max1 = poly1.project(axis)
        min2, max2 = poly2.project(axis)
        # 检查投影是否重叠
        overlap = min(max1, max2) - max(min1, min2)
        if overlap <= 0:
            return False, None  # 找到分离轴，不相交
        if overlap < min_overlap:
            min_overlap = overlap
            mtv_axis = axis
    
    return True, mtv_axis * min_overlap  # MTV

# 应用1：两个旋转矩形（OBB）碰撞
def make_rect(cx, cy, w, h, angle_deg):
    """创建旋转矩形"""
    rad = math.radians(angle_deg)
    c, s = math.cos(rad), math.sin(rad)
    # 本地坐标的四个角
    local = [Vector2(-w/2, -h/2), Vector2(w/2, -h/2),
             Vector2(w/2, h/2), Vector2(-w/2, h/2)]
    # 旋转并平移
    verts = []
    for p in local:
        x = p.x * c - p.y * s + cx
        y = p.x * s + p.y * c + cy
        verts.append(Vector2(x, y))
    return Polygon(verts)

rect1 = make_rect(100, 100, 80, 40, 30)  # 旋转30°
rect2 = make_rect(130, 100, 80, 40, 0)   # 不旋转
collide, mtv = sat_collide(rect1, rect2)
print(f"OBB碰撞: {collide}, MTV: {mtv}")  # True

# 应用2：三角形与矩形碰撞
triangle = Polygon([Vector2(0,0), Vector2(100,0), Vector2(50,80)])
rect = make_rect(50, 30, 60, 60, 0)
collide, mtv = sat_collide(triangle, rect)
print(f"三角形与矩形碰撞: {collide}")  # True

# 应用3：凸多边形之间的碰撞（如不规则地形）
terrain1 = Polygon([Vector2(0,0), Vector2(100,0), Vector2(80,50), Vector2(20,50)])
terrain2 = Polygon([Vector2(50,30), Vector2(150,30), Vector2(130,80), Vector2(70,80)])
collide, mtv = sat_collide(terrain1, terrain2)
print(f"地形碰撞: {collide}")

# 应用4：SAT + AABB粗筛（性能优化）
def broad_phase_then_sat(objects, target):
    """先用AABB粗筛，再用SAT精确检测"""
    target_aabb = compute_aabb(target)
    candidates = []
    for obj in objects:
        if compute_aabb(obj).intersects(target_aabb):
            candidates.append(obj)
    # 只对候选物体做SAT
    hits = []
    for obj in candidates:
        collide, _ = sat_collide(obj, target)
        if collide:
            hits.append(obj)
    return hits

def compute_aabb(polygon):
    xs = [v.x for v in polygon.vertices]
    ys = [v.y for v in polygon.vertices]
    class B:
        def __init__(self,a,b,c,d): self.min_x,self.min_y,self.max_x,self.max_y=a,b,c,d
        def intersects(self,o): 
            return not (self.max_x<o.min_x or o.max_x<self.min_x or
                       self.max_y<o.min_y or o.max_y<self.min_y)
    return B(min(xs),min(ys),max(xs),max(ys))
```

SAT算法的投影分离示意：

```
两个凸多边形：
  ┌──┐
  │  │      ┌──┐
  │  │      │  │
  └──┘      └──┘
   poly1     poly2

检查每条边的法线轴：

轴1（poly1某边法线）：
  poly1投影: [───]
  poly2投影:      [───]  ← 不重叠！找到分离轴 → 不相交

若所有轴都重叠：
  poly1: [────]
  poly2:    [────]  ← 重叠
  → 相交，MTV=最小重叠对应的方向
```

#### 总结

- SAT是2D凸多边形碰撞检测的通用算法
- 核心思想：存在分离轴则不相交，分离轴必是某条边的法线
- 需检查两多边形所有边的法线（共m+n条轴）
- **MTV**：所有重叠轴中重叠最小者，用于碰撞响应
- SAT复杂度O(m*n)，常配合AABB粗筛优化性能
- 适用于OBB、三角形、任意凸多边形，是2D碰撞检测的"终极方案"
- 局限：仅适用于凸多边形，凹多边形需分解为凸部分

---

## 第6章 运动与物理

游戏世界是动态的——物体在移动、跳跃、飞行、碰撞。本章将讲解如何用数学描述运动，从最基础的速度与加速度，到抛物运动、反弹响应、摩擦阻尼。这些知识让你能实现从平台跳跃到弹球游戏、从射击游戏到赛车游戏的各类物理系统。理解运动学方程是构建真实物理感游戏的关键。

### 第22讲：速度、加速度与运动方程

#### 概念

**速度（Velocity）** 是位置随时间的变化率，描述物体运动的快慢和方向，单位通常是像素/秒。**加速度（Acceleration）** 是速度随时间的变化率，描述速度如何改变。运动学的核心方程描述了位置、速度、加速度与时间的关系，是所有物理模拟的基础。

#### 原理

2D游戏中的运动学基于三个核心方程（类似物理学的匀加速运动）：

1. **位置更新**：`position += velocity * dt`（dt为时间步长）
2. **速度更新**：`velocity += acceleration * dt`
3. **完整运动方程**：`p(t) = p0 + v0*t + 0.5*a*t²`

其中dt是帧时间（如1/60秒），用于将"每秒"的物理量转换为"每帧"的位移。这种**时间步长驱动**的模拟让游戏在不同帧率下表现一致。

游戏物理循环的标准流程：
1. 读取输入，设置加速度（如按键产生水平加速度）
2. 应用重力（增加向下的加速度）
3. 更新速度：`velocity += acceleration * dt`
4. 更新位置：`position += velocity * dt`
5. 处理碰撞（修正位置和速度）
6. 应用阻尼/摩擦（衰减速度）

需要注意**帧率独立性**：所有运动都应乘以dt，否则在60FPS和30FPS下移动速度不同。这是新手最常犯的错误——直接 `position += velocity` 会导致帧率越高移动越快。

#### 例子

```python
import math

class Vector2:
    def __init__(self, x=0.0, y=0.0):
        self.x = x; self.y = y
    def __add__(self, o): 
        if isinstance(o, Vector2): return Vector2(self.x+o.x, self.y+o.y)
        return Vector2(self.x+o, self.y+o)
    def __sub__(self, o): 
        if isinstance(o, Vector2): return Vector2(self.x-o.x, self.y-o.y)
        return Vector2(self.x-o, self.y-o)
    def __mul__(self, s): return Vector2(self.x*s, self.y*s)
    def __repr__(self): return f"V({self.x:.2f}, {self.y:.2f})"

class PhysicsBody:
    """基础物理体：位置、速度、加速度"""
    def __init__(self, pos, vel=None, acc=None):
        self.pos = pos
        self.vel = vel or Vector2(0, 0)
        self.acc = acc or Vector2(0, 0)
    
    def update(self, dt):
        """标准物理更新"""
        # 速度 += 加速度 * dt
        self.vel = self.vel + self.acc * dt
        # 位置 += 速度 * dt
        self.pos = self.pos + self.vel * dt
        # 清零加速度（每帧重新计算）
        self.acc = Vector2(0, 0)

# 应用1：恒定速度移动（无加速度）
body = PhysicsBody(Vector2(0, 0), Vector2(100, 0))  # 向右100像素/秒
dt = 1/60  # 60FPS
for frame in range(60):  # 模拟1秒
    body.update(dt)
print(f"1秒后位置: {body.pos}")  # V(100.00, 0.00)

# 应用2：加速度（如角色奔跑加速）
runner = PhysicsBody(Vector2(0, 0))
runner.acc = Vector2(50, 0)  # 50像素/秒²的加速度
for frame in range(60):  # 1秒
    runner.update(dt)
print(f"加速1秒后位置: {runner.pos}, 速度: {runner.vel}")
# 位置约25，速度约50（匀加速运动）

# 应用3：重力（恒定向下加速度）
class GravityBody(PhysicsBody):
    def __init__(self, pos, vel=None, gravity=200):
        super().__init__(pos, vel)
        self.gravity = gravity  # 像素/秒²
    
    def update(self, dt):
        self.acc = self.acc + Vector2(0, self.gravity)  # 施加重力
        super().update(dt)

# 应用4：完整角色控制器
class Character:
    def __init__(self, pos):
        self.body = GravityBody(pos)
        self.move_speed = 200  # 水平移动速度
        self.jump_force = 400   # 跳跃初速度
        self.on_ground = False
    
    def update(self, dt, input_left, input_right, input_jump):
        # 水平移动（直接设置速度，无加速感）
        if input_left:
            self.body.vel.x = -self.move_speed
        elif input_right:
            self.body.vel.x = self.move_speed
        else:
            self.body.vel.x = 0  # 立即停止
        
        # 跳跃（只在地面时）
        if input_jump and self.on_ground:
            self.body.vel.y = -self.jump_force  # 向上速度
            self.on_ground = False
        
        # 物理更新
        self.body.update(dt)
        
        # 简化的地面检测
        if self.body.pos.y > 400:  # 地面y=400
            self.body.pos.y = 400
            self.body.vel.y = 0
            self.on_ground = True

# 模拟角色跳跃
hero = Character(Vector2(100, 400))
hero.on_ground = True
# 按跳跃键
for frame in range(30):  # 0.5秒
    hero.update(1/60, False, False, frame == 0)
print(f"跳跃0.5秒后位置: {hero.body.pos}")  # 应该在空中
```

运动学方程的直观示意：

```
匀速运动（无加速度）：
  位置: ●─────●─────●─────●─────●  每帧移动相同距离
  速度: ────────────────────────→  恒定

匀加速运动（恒定加速度）：
  位置: ●──●───●────●─────●──────●  距离逐渐增大
  速度: ─→──→───→────→─────→──────→  速度逐渐增大

重力作用（向下加速度）：
  位置: ●  ●   ●    ●     ●      ●  下落越来越快
        ↑  ↑   ↑    ↑     ↑      ↑
        速度向下逐渐增大
```

#### 总结

- 速度是位置变化率，加速度是速度变化率
- 核心方程：`pos += vel*dt`，`vel += acc*dt`
- **帧率独立性**：所有运动必须乘以dt，否则不同帧率下表现不同
- 物理循环：输入→加速度→速度→位置→碰撞→阻尼
- 重力是恒定向下的加速度，跳跃是瞬间的向上速度
- 完整运动方程 `p(t) = p0 + v0*t + 0.5*a*t²` 用于预测轨迹

---

### 第23讲：抛物运动与重力

#### 概念

**抛物运动（Projectile Motion）** 是物体在重力作用下的运动轨迹，呈抛物线形状。这是2D游戏中最常见的运动模式：跳跃的角色、投掷的手雷、射出的箭矢、飞行的角色技能等都遵循抛物运动。理解抛物运动能让你精确控制弹道、预测落点、设计技能轨迹。

#### 原理

抛物运动的核心是**水平方向匀速、竖直方向匀加速**。设初速度为 `(vx, vy)`，重力加速度为g，则t时刻的位置为：

- `x(t) = x0 + vx * t`
- `y(t) = y0 + vy * t + 0.5 * g * t²`

竖直方向是匀加速运动，速度随时间变化：`vy(t) = vy0 + g * t`。当 `vy(t) = 0` 时达到最高点，此时 `t_peak = -vy0 / g`（vy0为负表示向上）。

抛物运动的几个关键计算：
1. **最高点时间**：`t_peak = -vy0 / g`
2. **最高点高度**：`h_peak = -vy0² / (2*g)`（相对起点）
3. **总滞空时间**：`t_total = 2 * t_peak`（落回同一高度）
4. **水平射程**：`range = vx * t_total`
5. **落点位置**：根据初始位置和射程计算

这些公式让游戏设计者能精确控制跳跃高度、滞空时间、射程距离。例如调整重力g和跳跃力vy0，可以创造"轻飘飘"的月球重力感或"沉重"的地球重力感。

抛物运动的预测能力在游戏中极为有用：敌人AI可以预测玩家落点进行拦截，玩家可以预判手雷落点，技能可以显示弹道轨迹。

#### 例子

```python
import math

class Vector2:
    def __init__(self, x=0.0, y=0.0):
        self.x = x; self.y = y
    def __add__(self, o): return Vector2(self.x+o.x, self.y+o.y)
    def __mul__(self, s): return Vector2(self.x*s, self.y*s)
    def __repr__(self): return f"V({self.x:.2f}, {self.y:.2f})"

class Projectile:
    """抛物运动物体"""
    def __init__(self, pos, vel, gravity=200):
        self.pos = pos
        self.vel = vel
        self.gravity = gravity  # 向下为正
        self.alive = True
    
    def update(self, dt):
        """更新位置和速度"""
        # 水平：匀速
        self.pos.x += self.vel.x * dt
        # 竖直：匀加速
        self.vel.y += self.gravity * dt
        self.pos.y += self.vel.y * dt
    
    def predict_landing(self, ground_y):
        """预测落点（返回落点x坐标和总时间）"""
        # 解方程: y0 + vy0*t + 0.5*g*t² = ground_y
        y0 = self.pos.y
        vy0 = self.vel.y
        g = self.gravity
        # 0.5*g*t² + vy0*t + (y0 - ground_y) = 0
        a = 0.5 * g
        b = vy0
        c = y0 - ground_y
        disc = b*b - 4*a*c
        if disc < 0: return None
        t1 = (-b - math.sqrt(disc)) / (2*a)
        t2 = (-b + math.sqrt(disc)) / (2*a)
        t = max(t1, t2)  # 取较晚的根（落地点）
        if t < 0: return None
        landing_x = self.pos.x + self.vel.x * t
        return landing_x, t
    
    def get_trajectory(self, steps=30, dt=0.05):
        """获取轨迹点列表（用于绘制弹道线）"""
        points = []
        pos = Vector2(self.pos.x, self.pos.y)
        vel = Vector2(self.vel.x, self.vel.y)
        for _ in range(steps):
            pos = pos + vel * dt
            vel.y += self.gravity * dt
            points.append(Vector2(pos.x, pos.y))
        return points

# 应用1：角色跳跃轨迹
hero = Projectile(Vector2(100, 400), Vector2(50, -300), gravity=400)
# 模拟跳跃全过程
trajectory = hero.get_trajectory(steps=60)
print(f"跳跃起点: {trajectory[0]}")
print(f"跳跃中点: {trajectory[30]}")
print(f"跳跃终点: {trajectory[-1]}")

# 应用2：手雷投掷——预测落点
grenade = Projectile(Vector2(0, 0), Vector2(200, -300), gravity=300)
landing = grenade.predict_landing(0)  # 落回同一高度
print(f"手雷落点: {landing}")  # (x, t)

# 应用3：求所需初速度击中目标
def solve_projectile_angle(start, target, speed, gravity):
    """求击中目标所需的发射角度（两种解）"""
    dx = target.x - start.x
    dy = target.y - start.y
    v = speed
    g = gravity
    # 弹道方程: tan(θ) = (v² ± sqrt(v⁴ - g(g*dx² + 2*dy*v²))) / (g*dx)
    disc = v**4 - g * (g * dx**2 + 2 * dy * v**2)
    if disc < 0: return None  # 无法击中
    sqrt_disc = math.sqrt(disc)
    g_dx = g * dx
    if abs(g_dx) < 1e-9: return None
    tan1 = (v**2 + sqrt_disc) / g_dx
    tan2 = (v**2 - sqrt_disc) / g_dx
    angle1 = math.atan(tan1)
    angle2 = math.atan(tan2)
    return math.degrees(angle1), math.degrees(angle2)

# 应用4：弹道预测线（如愤怒的小鸟）
def draw_trajectory(start, vel, gravity, color="white"):
    """绘制弹道预测线"""
    proj = Projectile(start, vel, gravity)
    points = proj.get_trajectory(steps=50)
    return [(p.x, p.y) for p in points]

# 应用5：跳跃高度与滞空时间计算
def jump_stats(initial_up_speed, gravity):
    """计算跳跃的最高高度和总滞空时间"""
    peak_height = initial_up_speed ** 2 / (2 * gravity)
    air_time = 2 * initial_up_speed / gravity  # 上升+下降
    return peak_height, air_time

height, time = jump_stats(400, 400)
print(f"跳跃高度: {height}, 滞空时间: {time}")  # 200, 2.0
```

抛物运动的轨迹示意：

```
角色跳跃（初速度向上+水平移动）：

    最高点
      ●
     ╱ ╲
    ╱   ╲
   ╱     ╲
  ╱       ╲
 ●─────────●  起点与落点同高度
 ←──水平──→
 
竖直方向：先减速上升，到最高点速度为0，再加速下落
水平方向：匀速移动

关键公式：
  最高点时间: t_peak = vy0 / g
  最高高度:  h = vy0² / (2g)
  总滞空:   T = 2 * t_peak
  水平射程: R = vx * T
```

#### 总结

- 抛物运动=水平匀速+竖直匀加速
- 位置方程：`x = x0 + vx*t`，`y = y0 + vy*t + 0.5*g*t²`
- 最高点时间 `t = vy0/g`，最高高度 `h = vy0²/(2g)`
- 落点预测通过解二次方程，用于AI拦截和技能瞄准
- 弹道预测线让玩家预判轨迹，提升游戏手感
- 调整重力和初速度可创造不同重力感（月球、地球、木星）

---

### 第24讲：反弹与碰撞响应

#### 概念

**反弹（Bounce）** 是物体碰撞后改变运动方向的现象。在游戏中，弹球、台球、角色被击退、物体落地反弹等都涉及反弹计算。**碰撞响应（Collision Response）** 是处理碰撞后物体如何运动的整套机制，包括位置修正、速度反射、能量损失等。

#### 原理

反弹的核心是**速度反射**，已在第7讲讲解。反射公式 `r = v - 2*(v·n)*n` 让物体沿表面法线方向"弹开"。但真实物理中反弹会损失能量，因此引入**恢复系数（Restitution Coefficient）** `e`，取值0到1：

- `e = 1`：完全弹性碰撞，无能量损失（理想弹球）
- `e = 0`：完全非弹性碰撞，物体贴着表面（如湿泥巴落地）
- `0 < e < 1`：部分弹性，每次反弹高度递减（真实弹球）

带恢复系数的反射公式：`r = v - (1+e)*(v·n)*n`。当e=1时退化为标准反射公式。

碰撞响应的完整流程：
1. **检测碰撞**：判断是否相交
2. **位置修正**：将物体分离，避免重叠
3. **速度反射**：根据法线和恢复系数计算新速度
4. **能量损失**：乘以恢复系数，模拟非弹性碰撞
5. **触发事件**：如得分、特效、音效

对于多物体碰撞（如台球），需考虑动量守恒和能量守恒，计算更复杂。2D弹性碰撞公式涉及相对速度、法线分量、质量比等。

#### 例子

```python
import math

class Vector2:
    def __init__(self, x=0.0, y=0.0):
        self.x = x; self.y = y
    def __add__(self, o): return Vector2(self.x+o.x, self.y+o.y)
    def __sub__(self, o): return Vector2(self.x-o.x, self.y-o.y)
    def __mul__(self, s): return Vector2(self.x*s, self.y*s)
    def length(self): return math.sqrt(self.x**2 + self.y**2)
    def length_squared(self): return self.x**2 + self.y**2
    def normalize(self):
        l = self.length()
        return Vector2(0,0) if l < 1e-9 else Vector2(self.x/l, self.y/l)
    def dot(self, o): return self.x*o.x + self.y*o.y
    def __repr__(self): return f"V({self.x:.2f}, {self.y:.2f})"

def reflect_with_restitution(v, n, e=1.0):
    """带恢复系数的反射"""
    # r = v - (1+e)*(v·n)*n
    return v - n * ((1 + e) * v.dot(n))

class Ball:
    """弹球：带恢复系数的反弹"""
    def __init__(self, pos, vel, radius=10, restitution=0.8):
        self.pos = pos
        self.vel = vel
        self.radius = radius
        self.restitution = restitution  # 恢复系数
    
    def update(self, dt, bounds):
        """更新并处理与边界的反弹"""
        self.pos = self.pos + self.vel * dt
        # 检查与边界的碰撞
        min_x, min_y, max_x, max_y = bounds
        # 左墙
        if self.pos.x - self.radius < min_x:
            self.pos.x = min_x + self.radius
            self.vel.x = abs(self.vel.x) * self.restitution
        # 右墙
        if self.pos.x + self.radius > max_x:
            self.pos.x = max_x - self.radius
            self.vel.x = -abs(self.vel.x) * self.restitution
        # 上墙
        if self.pos.y - self.radius < min_y:
            self.pos.y = min_y + self.radius
            self.vel.y = abs(self.vel.y) * self.restitution
        # 下墙（地面）
        if self.pos.y + self.radius > max_y:
            self.pos.y = max_y - self.radius
            self.vel.y = -abs(self.vel.y) * self.restitution

# 应用1：弹球游戏（Breakout风格）
ball = Ball(Vector2(100, 100), Vector2(200, 150), 10, restitution=0.9)
bounds = (0, 0, 400, 400)
for _ in range(60):  # 1秒
    ball.update(1/60, bounds)
print(f"弹球1秒后位置: {ball.pos}, 速度: {ball.vel}")

# 应用2：球与斜面碰撞反弹
def ball_slope_collision(ball, slope_start, slope_end):
    """球与斜面的碰撞反弹"""
    # 计算斜面法线
    slope_vec = slope_end - slope_start
    normal = Vector2(-slope_vec.y, slope_vec.x).normalize()
    # 球到斜面的距离
    to_ball = ball.pos - slope_start
    dist = to_ball.dot(normal)
    if dist < ball.radius:  # 碰撞
        # 位置修正
        penetration = ball.radius - dist
        ball.pos = ball.pos + normal * penetration
        # 速度反射
        ball.vel = reflect_with_restitution(ball.vel, normal, ball.restitution)

# 应用3：两球弹性碰撞（台球风格）
def ball_ball_collision(b1, b2):
    """两球弹性碰撞，考虑质量"""
    diff = b2.pos - b1.pos
    dist = diff.length()
    if dist >= b1.radius + b2.radius:
        return  # 未碰撞
    normal = diff.normalize()
    # 相对速度
    rel_vel = b1.vel - b2.vel
    vel_along_normal = rel_vel.dot(normal)
    if vel_along_normal < 0:
        return  # 已经在分离
    # 冲量计算（假设质量相等，简化）
    e = 0.95  # 恢复系数
    impulse = -(1 + e) * vel_along_normal / 2  # 质量相等的简化
    b1.vel = b1.vel + normal * impulse
    b2.vel = b2.vel - normal * impulse
    # 位置修正
    overlap = b1.radius + b2.radius - dist
    b1.pos = b1.pos - normal * (overlap / 2)
    b2.pos = b2.pos + normal * (overlap / 2)

# 应用4：角色被击退后的反弹
def knockback_with_bounce(character, force, walls):
    """击退并处理墙壁反弹"""
    character.vel = character.vel + force
    for wall_normal in walls:
        # 检查是否撞墙
        if character.vel.dot(wall_normal) < 0:  # 朝墙移动
            character.vel = reflect_with_restitution(
                character.vel, wall_normal, 0.5)  # 半弹性
```

反弹与恢复系数示意：

```
完全弹性（e=1.0）：
  入射 ↓    反射 ↑
       ╲   ╱
        ╲ ╱
  ───────●─────── 表面
  速度大小不变，角度对称

部分弹性（e=0.5）：
  入射 ↓    反射 ↑（更短）
       ╲   ╱
        ╲ ╱
  ───────●─────── 表面
  速度减小，反弹高度递减

完全非弹性（e=0）：
  入射 ↓
       ╲
        ●  贴着表面，无反弹
  ─────────── 表面
```

#### 总结

- 反弹=速度反射，公式 `r = v - (1+e)*(v·n)*n`
- **恢复系数e**控制能量损失：1=完全弹性，0=无反弹
- 碰撞响应流程：检测→位置修正→速度反射→能量损失→事件
- 两球碰撞需考虑动量守恒，用冲量公式计算
- 弹球游戏、台球、击退反弹都基于此原理
- 多次反弹后能量逐渐耗散，物体最终静止

---

### 第25讲：摩擦与阻尼

#### 概念

**摩擦（Friction）** 是物体接触面间阻碍相对运动的力，使物体逐渐减速。**阻尼（Damping）** 是更广义的概念，指任何使速度衰减的效应，包括空气阻力、摩擦、能量耗散等。在游戏中，摩擦让角色停止移动、让球减速滚动、让摆动的钟摆逐渐静止，是营造"真实物理感"的关键。

#### 原理

游戏中的摩擦通常用简化模型，而非真实物理的库仑摩擦。常见有三种：

1. **线性阻尼**：每帧速度乘以一个系数 `vel *= damping`（damping<1）。简单高效，但与帧率相关。
2. **时间无关阻尼**：`vel *= damping^dt`，确保不同帧率下衰减一致。
3. **恒定减速**：每帧速度减少固定值 `vel -= sign(vel) * friction * dt`，模拟地面摩擦。

线性阻尼的物理意义：`v(t+dt) = v(t) * (1 - damping*dt)`，等价于 `dv/dt = -damping * v`，解为 `v(t) = v0 * e^(-damping*t)`，即指数衰减。

摩擦的应用场景：
- **地面摩擦**：角色停止按键后逐渐减速，而非瞬间停止
- **空气阻力**：飞行物体逐渐减速，避免无限滑行
- **旋转阻尼**：旋转的物体（如车轮、风扇）逐渐停止
- **摆动阻尼**：钟摆、弹簧逐渐回到静止

阻尼系数的调参是游戏手感的艺术：阻尼太低物体会"滑冰"，阻尼太高会感觉"粘滞"。通常地面摩擦阻尼在0.8-0.95之间（每帧保留80%-95%速度）。

#### 例子

```python
import math

class Vector2:
    def __init__(self, x=0.0, y=0.0):
        self.x = x; self.y = y
    def __add__(self, o): return Vector2(self.x+o.x, self.y+o.y)
    def __mul__(self, s): return Vector2(self.x*s, self.y*s)
    def length(self): return math.sqrt(self.x**2 + self.y**2)
    def normalize(self):
        l = self.length()
        return Vector2(0,0) if l < 1e-9 else Vector2(self.x/l, self.y/l)
    def __repr__(self): return f"V({self.x:.2f}, {self.y:.2f})"

# 应用1：线性阻尼（简单但帧率相关）
class LinearDampingBody:
    def __init__(self, pos, vel, damping=0.95):
        self.pos = pos
        self.vel = vel
        self.damping = damping  # 每帧保留的速度比例
    
    def update(self, dt):
        self.pos = self.pos + self.vel * dt
        self.vel = self.vel * self.damping  # 简单阻尼

# 应用2：时间无关阻尼（推荐）
class FrameIndependentDampingBody:
    def __init__(self, pos, vel, damping_per_sec=0.5):
        self.pos = pos
        self.vel = vel
        self.damping = damping_per_sec  # 每秒衰减比例
    
    def update(self, dt):
        self.pos = self.pos + self.vel * dt
        # damping^dt 确保帧率无关
        factor = math.pow(1 - self.damping, dt)
        self.vel = self.vel * factor

# 应用3：恒定摩擦力（地面摩擦）
class FrictionBody:
    def __init__(self, pos, vel, friction=100):
        self.pos = pos
        self.vel = vel
        self.friction = friction  # 像素/秒²的减速
    
    def update(self, dt):
        self.pos = self.pos + self.vel * dt
        # 应用恒定摩擦
        speed = self.vel.length()
        if speed > 0:
            decel = self.friction * dt
            if decel >= speed:
                self.vel = Vector2(0, 0)  # 完全停止
            else:
                self.vel = self.vel - self.vel.normalize() * decel

# 应用4：完整角色控制器（带摩擦）
class CharacterWithFriction:
    def __init__(self, pos):
        self.pos = pos
        self.vel = Vector2(0, 0)
        self.move_accel = 500  # 加速度
        self.ground_friction = 300  # 地面摩擦
        self.air_friction = 50  # 空气阻力
    
    def update(self, dt, input_x, on_ground):
        # 应用输入加速度
        self.vel.x += input_x * self.move_accel * dt
        
        # 应用摩擦
        friction = self.ground_friction if on_ground else self.air_friction
        speed_x = abs(self.vel.x)
        if speed_x > 0:
            decel = friction * dt
            if decel >= speed_x:
                self.vel.x = 0
            else:
                self.vel.x -= math.copysign(decel, self.vel.x)
        
        # 更新位置
        self.pos = self.pos + self.vel * dt

# 应用5：旋转阻尼（如车轮逐渐停止）
class RotatingObject:
    def __init__(self, angle, angular_vel, damping=0.98):
        self.angle = angle
        self.angular_vel = angular_vel  # 度/秒
        self.damping = damping
    
    def update(self, dt):
        self.angle += self.angular_vel * dt
        # 旋转阻尼
        self.angular_vel *= math.pow(self.damping, dt * 60)  # 标准化到60FPS

# 应用6：弹簧+阻尼（如UI弹窗回弹）
class SpringDamper:
    def __init__(self, pos, target, stiffness=100, damping=10):
        self.pos = pos
        self.vel = Vector2(0, 0)
        self.target = target
        self.stiffness = stiffness  # 弹簧刚度
        self.damping = damping  # 阻尼系数
    
    def update(self, dt):
        # 弹簧力：朝向目标
        force = (self.target - self.pos) * self.stiffness
        # 阻尼力：与速度反向
        force = force - self.vel * self.damping
        # 加速度=力/质量（假设质量1）
        acc = force
        self.vel = self.vel + acc * dt
        self.pos = self.pos + self.vel * dt

# 测试弹簧效果
spring = SpringDamper(Vector2(0, 0), Vector2(100, 0), stiffness=50, damping=8)
for i in range(30):
    spring.update(1/60)
    if i % 10 == 0:
        print(f"第{i}帧: 位置={spring.pos}, 速度={spring.vel}")
```

阻尼衰减曲线示意：

```
线性阻尼（每帧 *= 0.95）：
  速度
   │●
   │ ●
   │  ●
   │   ●●
   │      ●●●
   │          ●●●●●●
   └────────────────→ 时间
  指数衰减，逐渐趋近0

恒定摩擦（每帧 -= 固定值）：
  速度
   │●
   │ ●
   │  ●
   │   ●
   │    ●
   │     ●
   │      ●
   │       ●→ 停止
   └────────────────→ 时间
  线性衰减，到0后停止

弹簧+阻尼（振荡衰减）：
  位置
   │  ●     ●   ●  ●
   │   ●   ● ● ●●● → 目标
   │    ● ●   ●
   │     ●
   └────────────────→ 时间
  振荡逐渐收敛到目标
```

#### 总结

- 摩擦使物体减速，阻尼是更广义的速度衰减
- **线性阻尼** `vel *= damping` 简单但帧率相关
- **时间无关阻尼** `vel *= damping^dt` 推荐使用
- **恒定摩擦**模拟地面摩擦，到0后停止
- 阻尼系数调参是游戏手感的关键，通常0.8-0.95
- 弹簧+阻尼系统用于UI回弹、镜头跟随等平滑动画

---

## 第7章 插值与动画

插值是游戏动画的灵魂——让物体从A平滑过渡到B，而非瞬间跳变。从最基础的线性插值Lerp，到富有弹性的缓动函数，再到复杂的贝塞尔曲线，本章将系统讲解2D游戏中所有常用插值技术。掌握这些，你就能制作出专业级的UI动画、角色移动、镜头过渡，让游戏告别"机械感"，拥有"丝滑感"。

### 第26讲：线性插值Lerp

#### 概念

**线性插值（Linear Interpolation, Lerp）** 是在两个值之间按比例t进行线性混合的运算。公式为 `Lerp(a, b, t) = a + (b - a) * t`，其中t∈[0,1]。t=0时返回a，t=1时返回b，t=0.5时返回中点。Lerp可作用于标量、向量、颜色等任何可线性混合的数据。

#### 原理

Lerp的几何意义是在a和b之间的线段上按比例t取点。当t从0连续变化到1时，结果从a平滑过渡到b。这种"过渡"是动画的基础——任何"从状态A渐变到状态B"的需求都可用Lerp实现。

Lerp的几个关键变体：
1. **标量Lerp**：`a + (b-a)*t`，用于数值过渡（如血条、经验条）
2. **向量Lerp**：分量分别Lerp，用于位置、缩放过渡
3. **颜色Lerp**：RGB分量分别Lerp，用于颜色渐变
4. **帧率无关Lerp**：`a + (b-a)*(1 - exp(-speed*dt))`，确保不同帧率下过渡速度一致

游戏中最常见的Lerp用法是**平滑跟随**：每帧让物体向目标移动一定比例，而非瞬间到达。公式 `pos = Lerp(pos, target, speed*dt)`，speed越大跟随越快。这种"指数趋近"让物体快速接近目标后逐渐减速，产生自然的"刹车"感。

Lerp的局限是**线性过渡缺乏弹性**——匀速移动看起来机械。因此实际动画常用缓动函数（下一讲）替代纯Lerp，让过渡更有"生命力"。

#### 例子

```python
import math

class Vector2:
    def __init__(self, x=0.0, y=0.0):
        self.x = x; self.y = y
    def __add__(self, o): return Vector2(self.x+o.x, self.y+o.y)
    def __sub__(self, o): return Vector2(self.x-o.x, self.y-o.y)
    def __mul__(self, s): return Vector2(self.x*s, self.y*s)
    def lerp(self, other, t): return self + (other - self) * t
    def __repr__(self): return f"V({self.x:.2f}, {self.y:.2f})"

# 标量Lerp
def lerp(a, b, t):
    return a + (b - a) * t

# 帧率无关Lerp（推荐）
def lerp_frame_independent(a, b, speed, dt):
    """speed: 每秒接近的比例（0~1）"""
    t = 1 - math.exp(-speed * dt)
    return a + (b - a) * t

# 应用1：血条平滑过渡
class HealthBar:
    def __init__(self, max_hp=100):
        self.max_hp = max_hp
        self.current_hp = max_hp  # 实际血量
        self.display_hp = max_hp  # 显示血量（平滑过渡）
    
    def take_damage(self, amount):
        self.current_hp -= amount
    
    def update(self, dt):
        # 显示血量平滑过渡到实际血量
        self.display_hp = lerp(self.display_hp, self.current_hp, 0.1)

bar = HealthBar(100)
bar.take_damage(50)  # 实际血量变50
for i in range(10):
    bar.update(1/60)
    print(f"显示血量: {bar.display_hp:.1f}")  # 逐渐从100降到50

# 应用2：摄像机平滑跟随
class SmoothCamera:
    def __init__(self, pos):
        self.pos = pos
        self.speed = 5  # 跟随速度
    
    def update(self, target, dt):
        # 帧率无关的平滑跟随
        self.pos = lerp_frame_independent(
            self.pos.x, target.x, self.speed, dt), \
            lerp_frame_independent(
            self.pos.y, target.y, self.speed, dt)
        # 简化版：直接用Vector2的lerp
        self.pos = Vector2(*self.pos)
    
    def follow(self, target, dt):
        t = 1 - math.exp(-self.speed * dt)
        self.pos = self.pos.lerp(target, t)

cam = SmoothCamera(Vector2(0, 0))
player = Vector2(100, 100)
for i in range(10):
    cam.follow(player, 1/60)
print(f"摄像机位置: {cam.pos}")  # 逐渐接近(100,100)

# 应用3：颜色渐变（如受伤时屏幕变红）
def lerp_color(c1, c2, t):
    """c1, c2为(r,g,b)元组"""
    return tuple(lerp(c1[i], c2[i], t) for i in range(3))

normal_color = (255, 255, 255)  # 白色
damage_color = (255, 0, 0)      # 红色
# 受伤时屏幕从白渐变到红
for t in [0, 0.25, 0.5, 0.75, 1.0]:
    print(f"t={t}: {lerp_color(normal_color, damage_color, t)}")

# 应用4：UI元素淡入淡出
class UIFade:
    def __init__(self):
        self.alpha = 0  # 透明度0~1
        self.target_alpha = 0
    
    def fade_in(self):
        self.target_alpha = 1
    
    def fade_out(self):
        self.target_alpha = 0
    
    def update(self, dt):
        self.alpha = lerp(self.alpha, self.target_alpha, 0.1)

ui = UIFade()
ui.fade_in()
for i in range(10):
    ui.update(1/60)
    print(f"透明度: {ui.alpha:.2f}")  # 从0渐变到1
```

Lerp过渡的直观示意：

```
位置Lerp（从A到B）：
  t=0    t=0.25   t=0.5   t=0.75   t=1
   ●──────●────────●────────●──────●
   A                                 B
   
每帧t增加固定值，位置匀速移动

平滑跟随（指数趋近）：
  pos = Lerp(pos, target, 0.1)
  
  位置
   │●
   │ ●
   │  ●
   │   ●●
   │      ●●●  ← 快速接近后减速
   │           ●●●●●●●●●●●
   │                          target
   └────────────────────────→ 时间
  先快后慢的"刹车"感
```

#### 总结

- Lerp公式 `a + (b-a)*t`，t∈[0,1]
- 可作用于标量、向量、颜色等任何可线性混合的数据
- **帧率无关Lerp**用 `1-exp(-speed*dt)` 确保跨帧率一致
- 平滑跟随是Lerp最常见用法，产生"指数趋近"效果
- 血条、摄像机、颜色渐变、UI淡入淡出都依赖Lerp
- 局限：线性过渡缺乏弹性，复杂动画需用缓动函数

---

### 第27讲：缓动函数

#### 概念

**缓动函数（Easing Function）** 是让插值t值经过非线性变换的函数，使动画产生"加速"、"减速"、"弹跳"等丰富效果。常见的缓动类型包括：EaseIn（开始慢后加速）、EaseOut（开始快后减速）、EaseInOut（两端慢中间快）、Back（回弹）、Elastic（弹性振荡）、Bounce（弹跳）等。缓动函数是让游戏动画"有生命力"的关键。

#### 原理

缓动函数的本质是**对t值进行非线性映射**。给定线性t∈[0,1]，缓动函数输出新的t'∈[0,1]，然后用t'进行Lerp。不同缓动函数的曲线形状决定了动画的"性格"：

- **Linear**：t'=t，匀速，机械感
- **Quad**：t²（EaseIn）或1-(1-t)²（EaseOut），二次曲线
- **Cubic**：t³，比Quad更陡峭
- **Back**：先反向再前进，产生"蓄力"感
- **Elastic**：振荡衰减，像橡皮筋
- **Bounce**：多次小弹跳，像皮球落地

缓动函数的命名规则：`easeTypeVariation`，如 `easeInOutCubic` 表示"两端慢中间快的三次曲线"。

游戏动画中选择缓动函数的经验：
- **UI出现**：EaseOutBack（带轻微回弹，有"弹出"感）
- **UI消失**：EaseInQuad（逐渐加速消失）
- **角色移动**：EaseInOutQuad（起步和停止都平滑）
- **弹跳效果**：EaseOutBounce（落地弹跳）
- **弹性回弹**：EaseOutElastic（橡皮筋感）

#### 例子

```python
import math

class Vector2:
    def __init__(self, x=0.0, y=0.0):
        self.x = x; self.y = y
    def __add__(self, o): return Vector2(self.x+o.x, self.y+o.y)
    def __sub__(self, o): return Vector2(self.x-o.x, self.y-o.y)
    def __mul__(self, s): return Vector2(self.x*s, self.y*s)
    def lerp(self, other, t): return self + (other - self) * t
    def __repr__(self): return f"V({self.x:.2f}, {self.y:.2f})"

# 缓动函数库
class Easing:
    @staticmethod
    def linear(t): return t
    
    @staticmethod
    def ease_in_quad(t): return t * t
    
    @staticmethod
    def ease_out_quad(t): return 1 - (1 - t) ** 2
    
    @staticmethod
    def ease_in_out_quad(t):
        return 2 * t * t if t < 0.5 else 1 - (-2 * t + 2) ** 2 / 2
    
    @staticmethod
    def ease_in_cubic(t): return t ** 3
    
    @staticmethod
    def ease_out_cubic(t): return 1 - (1 - t) ** 3
    
    @staticmethod
    def ease_out_back(t):
        c1 = 1.70158
        c3 = c1 + 1
        return 1 + c3 * (t - 1) ** 3 + c1 * (t - 1) ** 2
    
    @staticmethod
    def ease_out_elastic(t):
        if t == 0 or t == 1: return t
        c4 = (2 * math.pi) / 3
        return 2 ** (-10 * t) * math.sin((t * 10 - 0.75) * c4) + 1
    
    @staticmethod
    def ease_out_bounce(t):
        n1 = 7.5625
        d1 = 2.75
        if t < 1 / d1:
            return n1 * t * t
        elif t < 2 / d1:
            t -= 1.5 / d1
            return n1 * t * t + 0.75
        elif t < 2.5 / d1:
            t -= 2.25 / d1
            return n1 * t * t + 0.9375
        else:
            t -= 2.625 / d1
            return n1 * t * t + 0.984375

# 通用缓动动画类
class Tween:
    """缓动动画：从start到end，持续duration秒，使用ease_func"""
    def __init__(self, start, end, duration, ease_func=Easing.ease_out_quad):
        self.start = start
        self.end = end
        self.duration = duration
        self.elapsed = 0
        self.ease_func = ease_func
        self.finished = False
    
    def update(self, dt):
        if self.finished: return self.end
        self.elapsed += dt
        t = min(self.elapsed / self.duration, 1.0)
        eased_t = self.ease_func(t)
        result = self.start.lerp(self.end, eased_t) if hasattr(self.start, 'lerp') else \
                 self.start + (self.end - self.start) * eased_t
        if t >= 1.0: self.finished = True
        return result

# 应用1：UI弹出动画（带回弹）
def ui_pop_in():
    start = Vector2(0, 100)  # 屏幕下方
    end = Vector2(0, 0)     # 目标位置
    tween = Tween(start, end, 0.5, Easing.ease_out_back)
    positions = []
    for _ in range(30):  # 0.5秒，60FPS
        pos = tween.update(1/60)
        positions.append(pos)
    return positions

positions = ui_pop_in()
print(f"UI弹出起点: {positions[0]}")
print(f"UI弹出中点: {positions[15]}")
print(f"UI弹出终点: {positions[-1]}")  # 可能略微超过0再回弹

# 应用2：角色移动到目标点（平滑起步停止）
def move_character(start, end, duration=1.0):
    tween = Tween(start, end, duration, Easing.ease_in_out_quad)
    return tween

# 应用3：弹跳落地效果
def bounce_effect(start, end, duration=0.8):
    tween = Tween(start, end, duration, Easing.ease_out_bounce)
    return tween

# 应用4：弹性回弹（如橡皮筋效果）
def elastic_snap(start, end, duration=0.6):
    tween = Tween(start, end, duration, Easing.ease_out_elastic)
    return tween

# 应用5：缓动函数曲线对比
def compare_easings():
    """打印各缓动函数在t=0.25, 0.5, 0.75时的值"""
    easings = {
        'Linear': Easing.linear,
        'EaseInQuad': Easing.ease_in_quad,
        'EaseOutQuad': Easing.ease_out_quad,
        'EaseInOutQuad': Easing.ease_in_out_quad,
        'EaseOutBack': Easing.ease_out_back,
        'EaseOutBounce': Easing.ease_out_bounce,
    }
    for name, func in easings.items():
        values = [func(t) for t in [0.25, 0.5, 0.75]]
        print(f"{name}: t=0.25→{values[0]:.3f}, t=0.5→{values[1]:.3f}, t=0.75→{values[2]:.3f}")

compare_easings()
```

不同缓动函数的曲线对比：

```
位置
 1│                    ●●●  Linear（匀速）
  │              ●●●●●
  │         ●●●●●
  │     ●●●●
  │ ●●●
  │                       
  │   ●                    EaseOutBack（回弹）
  │    ●●                  起步慢，终点略超再回弹
  │      ●●●
  │         ●●●●●●●●●●●●
 0└────────────────────────→ t
  0                       1

EaseOutBounce（弹跳）：
位置
 1│              ●●●
  │          ●●●●   ●●
  │      ●●●           ●●
  │  ●●●                ●
  │ ●                    
  └────────────────────────→ t
  多次小弹跳后到达终点
```

#### 总结

- 缓动函数对t值进行非线性映射，产生丰富动画效果
- 常见类型：Linear、Quad、Cubic、Back、Elastic、Bounce
- 命名规则：easeTypeVariation（如easeInOutCubic）
- **UI出现用EaseOutBack**（带回弹），**消失用EaseInQuad**（加速消失）
- **角色移动用EaseInOut**（平滑起步停止）
- **弹跳用EaseOutBounce**，**橡皮筋用EaseOutElastic**
- 缓动函数让动画从"机械"变"有生命力"

---

### 第28讲：贝塞尔曲线

#### 概念

**贝塞尔曲线（Bézier Curve）** 是由控制点定义的参数曲线，广泛用于游戏中的路径动画、UI轨迹、角色移动路径等。最常用的是**二次贝塞尔曲线**（3个控制点）和**三次贝塞尔曲线**（4个控制点）。贝塞尔曲线让动画路径不再局限于直线，能创造优美的弧线、波浪、螺旋等轨迹。

#### 原理

贝塞尔曲线的参数方程基于**伯恩斯坦多项式**，本质是控制点的加权混合。

**二次贝塞尔曲线**（3点P0, P1, P2）：
`B(t) = (1-t)² * P0 + 2(1-t)t * P1 + t² * P2`，t∈[0,1]

**三次贝塞尔曲线**（4点P0, P1, P2, P3）：
`B(t) = (1-t)³ * P0 + 3(1-t)²t * P1 + 3(1-t)t² * P2 + t³ * P3`

直观理解：曲线从P0出发，被P1"拉向"某方向，再被P2（或P3）"拉回"终点。控制点不一定是曲线上的点，但决定了曲线的形状。

贝塞尔曲线的关键性质：
1. **端点插值**：曲线必过P0和P（最后控制点）
2. **切线方向**：起点切线沿P0→P1方向，终点切线沿P(n-1)→Pn方向
3. **凸包性**：曲线始终在控制点凸包内，不会"跑偏"
4. **仿射不变性**：对控制点做仿射变换，曲线做同样变换

游戏中的应用：
- **路径动画**：让物体沿曲线移动（如飞行轨迹）
- **UI轨迹**：弹窗从某点以弧线飞入
- **角色技能**：魔法弹道沿曲线飞行
- **摄像机路径**：过场动画中摄像机沿曲线运动

#### 例子

```python
import math

class Vector2:
    def __init__(self, x=0.0, y=0.0):
        self.x = x; self.y = y
    def __add__(self, o): return Vector2(self.x+o.x, self.y+o.y)
    def __sub__(self, o): return Vector2(self.x-o.x, self.y-o.y)
    def __mul__(self, s): return Vector2(self.x*s, self.y*s)
    def length(self): return math.sqrt(self.x**2 + self.y**2)
    def normalize(self):
        l = self.length()
        return Vector2(0,0) if l < 1e-9 else Vector2(self.x/l, self.y/l)
    def __repr__(self): return f"V({self.x:.2f}, {self.y:.2f})"

# 二次贝塞尔曲线
def quadratic_bezier(p0, p1, p2, t):
    """3个控制点定义的曲线"""
    u = 1 - t
    return p0 * (u*u) + p1 * (2*u*t) + p2 * (t*t)

# 三次贝塞尔曲线
def cubic_bezier(p0, p1, p2, p3, t):
    """4个控制点定义的曲线"""
    u = 1 - t
    return (p0 * (u**3) + 
            p1 * (3*u*u*t) + 
            p2 * (3*u*t*t) + 
            p3 * (t**3))

# 贝塞尔曲线切线（用于让物体朝向运动方向）
def cubic_bezier_tangent(p0, p1, p2, p3, t):
    """返回t处的切线方向"""
    u = 1 - t
    tangent = (p1 - p0) * (3*u*u) + \
              (p2 - p1) * (6*u*t) + \
              (p3 - p2) * (3*t*t)
    return tangent.normalize()

# 应用1：物体沿贝塞尔曲线移动
class BezierMover:
    """沿三次贝塞尔曲线移动的物体"""
    def __init__(self, p0, p1, p2, p3, duration=2.0):
        self.p0, self.p1, self.p2, self.p3 = p0, p1, p2, p3
        self.duration = duration
        self.elapsed = 0
        self.pos = p0
    
    def update(self, dt):
        self.elapsed += dt
        t = min(self.elapsed / self.duration, 1.0)
        self.pos = cubic_bezier(self.p0, self.p1, self.p2, self.p3, t)
        # 计算朝向（切线方向）
        self.facing = cubic_bezier_tangent(
            self.p0, self.p1, self.p2, self.p3, t)
        return t < 1.0  # 是否还在移动

# 应用2：UI弹窗弧线飞入
def ui_fly_in_arc(start, end):
    """UI从start以弧线飞到end"""
    # 控制点：在start和end中间偏上
    mid = (start + end) * 0.5
    control = Vector2(mid.x, mid.y - 100)  # 向上偏移100像素
    return BezierMover(start, control, end, end, duration=0.5)

# 应用3：魔法弹道（如闪电链）
def lightning_path(start, end, segments=10):
    """生成闪电的折线路径（用贝塞尔加随机扰动）"""
    points = []
    for i in range(segments + 1):
        t = i / segments
        # 基础贝塞尔位置
        mid = (start + end) * 0.5
        # 加随机扰动让闪电"抖动"
        import random
        offset = Vector2(random.uniform(-20, 20), random.uniform(-20, 20))
        pos = quadratic_bezier(start, mid + offset, end, t)
        points.append(pos)
    return points

# 应用4：摄像机过场路径
class CameraCutscene:
    """摄像机沿贝塞尔曲线运动"""
    def __init__(self, path_points, duration=3.0):
        # path_points为多个控制点列表
        self.points = path_points
        self.duration = duration
        self.elapsed = 0
    
    def update(self, dt):
        self.elapsed += dt
        t = min(self.elapsed / self.duration, 1.0)
        if len(self.points) == 4:
            return cubic_bezier(*self.points, t)
        elif len(self.points) == 3:
            return quadratic_bezier(*self.points, t)

# 应用5：连续贝塞尔曲线（Catmull-Rom样条）
def catmull_rom(p0, p1, p2, p3, t):
    """Catmull-Rom样条：曲线必过中间两点p1, p2"""
    t2 = t * t
    t3 = t2 * t
    return (p0 * (-0.5*t3 + t2 - 0.5*t) +
            p1 * (1.5*t3 - 2.5*t2 + 1) +
            p2 * (-1.5*t3 + 2*t2 + 0.5*t) +
            p3 * (0.5*t3 - 0.5*t2))

# 应用6：生成平滑路径（多个控制点连接）
def smooth_path(waypoints, samples_per_segment=20):
    """通过多个路径点生成平滑曲线"""
    if len(waypoints) < 4: return waypoints
    path = []
    for i in range(len(waypoints) - 3):
        p0, p1, p2, p3 = waypoints[i:i+4]
        for j in range(samples_per_segment):
            t = j / samples_per_segment
            path.append(catmull_rom(p0, p1, p2, p3, t))
    return path

# 测试
p0, p1, p2, p3 = Vector2(0,0), Vector2(50,100), Vector2(150,100), Vector2(200,0)
mover = BezierMover(p0, p1, p2, p3, duration=2.0)
for i in range(5):
    mover.update(0.4)
    print(f"位置: {mover.pos}, 朝向: {mover.facing}")
```

贝塞尔曲线的几何直观：

```
二次贝塞尔曲线（3点）：
  P0 ●
      ╲
       ╲ 控制线
        ● P1（控制点，曲线不过此点）
       ╱
      ╱
  P2 ●
  
  曲线从P0出发，被P1"拉"成弧形，到P2结束

三次贝塞尔曲线（4点）：
  P0 ●
      ╲
       ● P1
       
       ● P2
      ╱
  P3 ●
  
  曲线从P0出发，被P1和P2共同影响，到P3结束
  起点切线沿P0→P1，终点切线沿P2→P3
```

#### 总结

- 贝塞尔曲线由控制点定义，二次用3点，三次用4点
- 二次公式：`(1-t)²P0 + 2(1-t)tP1 + t²P2`
- 三次公式：`(1-t)³P0 + 3(1-t)²tP1 + 3(1-t)t²P2 + t³P3`
- 曲线必过端点，控制点决定形状
- **切线方向**让物体朝向运动方向（如飞行物朝向）
- Catmull-Rom样条让曲线过所有控制点，适合路径生成
- 用于UI弧线动画、弹道、摄像机过场、平滑路径

---

### 第29讲：角度插值与最短路径

#### 概念

**角度插值（Angle Interpolation）** 是在两个角度之间平滑过渡的运算。看似简单，实则有个关键陷阱：角度是周期性的（0°与360°等价），直接Lerp会导致"绕远路"。例如从350°到10°，直接Lerp会转340°，而最短路径只需转20°。**最短路径角度插值**解决了这个问题，是角色转向、炮塔旋转、UI旋转动画的基础。

#### 原理

角度插值的核心是**处理角度环绕**。标准Lerp `a + (b-a)*t` 在角度上会出问题：从350°到10°，`b-a = -340°`，Lerp会逆时针转340°，而非顺时针转20°。

解决方案是**计算最短角度差**：
1. 计算原始差值 `diff = b - a`
2. 将diff规范化到[-180°, 180°]：`while diff > 180: diff -= 360; while diff < -180: diff += 360`
3. 用规范后的diff进行Lerp：`result = a + diff * t`

这样从350°到10°，diff会被规范为+20°（顺时针20°），Lerp会走最短路径。

更通用的方法是**角度Lerp函数**：
```python
def angle_lerp(a, b, t):
    diff = (b - a + 540) % 360 - 180  # 规范到[-180, 180]
    return a + diff * t
```

这个公式利用模运算的特性，一行代码完成最短路径计算。

角度插值在游戏中的应用：
- **角色转向**：面向鼠标或目标，平滑旋转
- **炮塔旋转**：缓慢转向目标，避免瞬间锁定
- **摄像机旋转**：过场动画中摄像机平滑转向
- **UI元素旋转**：如转盘、轮盘动画

需要注意角度单位（度vs弧度）的统一，以及屏幕坐标系下正角度方向（通常顺时针为正）。

#### 例子

```python
import math

def angle_lerp_deg(a, b, t):
    """角度Lerp（度），走最短路径"""
    diff = (b - a + 540) % 360 - 180  # 规范到[-180, 180]
    return a + diff * t

def angle_lerp_rad(a, b, t):
    """角度Lerp（弧度），走最短路径"""
    diff = (b - a + 3*math.pi) % (2*math.pi) - math.pi
    return a + diff * t

def normalize_angle(angle):
    """将角度规范到[0, 360)"""
    return angle % 360

def angle_difference(a, b):
    """返回从a到b的最短角度差（-180~180）"""
    return (b - a + 540) % 360 - 180

# 应用1：角色平滑转向目标
class CharacterRotation:
    def __init__(self, angle=0):
        self.angle = angle  # 当前朝向（度）
        self.rotation_speed = 5  # 旋转速度
    
    def turn_towards(self, target_angle, dt):
        """平滑转向目标角度"""
        # 用帧率无关Lerp
        t = 1 - math.exp(-self.rotation_speed * dt)
        self.angle = angle_lerp_deg(self.angle, target_angle, t)
        self.angle = normalize_angle(self.angle)
    
    def face_position(self, current_pos, target_pos):
        """面向目标位置"""
        dx = target_pos[0] - current_pos[0]
        dy = target_pos[1] - current_pos[1]
        return math.degrees(math.atan2(dy, dx))

# 测试：从350°转向10°（应走20°而非340°）
char = CharacterRotation(350)
target = 10
print(f"起始角度: {char.angle}")
for _ in range(20):
    char.turn_towards(target, 1/60)
print(f"转向后角度: {char.angle:.1f}")  # 应接近10

# 应用2：炮塔旋转（限制旋转速度）
class Turret:
    def __init__(self, angle=0, max_rotation_speed=90):
        self.angle = angle
        self.max_speed = max_rotation_speed  # 度/秒
    
    def aim_at(self, target_angle, dt):
        """以最大速度转向目标"""
        diff = angle_difference(self.angle, target_angle)
        # 限制每帧旋转量
        max_step = self.max_speed * dt
        if abs(diff) <= max_step:
            self.angle = target_angle
        else:
            self.angle += math.copysign(max_step, diff)
        self.angle = normalize_angle(self.angle)

# 应用3：摄像机平滑转向
class CameraRotation:
    def __init__(self, angle=0):
        self.angle = angle
    
    def rotate_to(self, target, dt, speed=3):
        t = 1 - math.exp(-speed * dt)
        self.angle = angle_lerp_deg(self.angle, target, t)

# 应用4：UI转盘动画
class SpinWheel:
    def __init__(self):
        self.angle = 0
        self.target_angle = 0
        self.velocity = 0  # 角速度
    
    def spin(self, force):
        """施加旋转力"""
        self.velocity += force
    
    def update(self, dt, damping=0.95):
        # 应用速度
        self.angle += self.velocity * dt
        # 阻尼
        self.velocity *= damping ** (dt * 60)
        # 当速度很小时停止并对齐到最近格
        if abs(self.velocity) < 1:
            self.velocity = 0
            # 对齐到45°的倍数
            self.angle = round(self.angle / 45) * 45

# 应用5：连续转向多个目标
def follow_waypoint_angles(current_angle, waypoints, dt):
    """依次转向每个路径点"""
    angles = []
    angle = current_angle
    for wp in waypoints:
        target = math.degrees(math.atan2(wp[1], wp[0]))
        # 平滑过渡
        for _ in range(10):
            angle = angle_lerp_deg(angle, target, 0.2)
            angles.append(angle)
    return angles

# 测试最短路径
print(f"350°到10°最短差: {angle_difference(350, 10)}")  # 20
print(f"10°到350°最短差: {angle_difference(10, 350)}")  # -20
print(f"90°到270°最短差: {angle_difference(90, 270)}")  # 180或-180
```

角度最短路径示意：

```
角度圆（0°=右，90°=下，180°=左，270°=上）：

       270°
        │
        │
180°────●────0°    从350°到10°：
        │           直接Lerp: 350→340→...→20→10（转340°，绕远路）
        │           最短路径: 350→0→10（转20°，正确）
       90°

错误Lerp（直接）：
  350 ────→ 340 ────→ ... ────→ 20 ────→ 10
  ←──逆时针转340°───

正确Lerp（最短路径）：
  350 ────→ 0 ────→ 10
  ←顺时针转20°→

关键：用 (b-a+540)%360-180 规范差值到[-180, 180]
```

#### 总结

- 角度是周期性的，直接Lerp会"绕远路"
- **最短路径公式**：`diff = (b-a+540)%360-180`，规范到[-180,180]
- 角度Lerp让角色、炮塔、摄像机平滑转向
- 炮塔等需限制最大旋转速度，避免瞬间锁定
- 注意角度单位（度/弧度）和坐标系方向（屏幕系顺时针为正）
- UI转盘、轮盘动画也依赖角度插值

---

## 第8章 实战应用

本章是全课程的集大成，将前7章的数学知识综合运用到游戏开发最典型的实战场景。从角色面向与瞄准，到导弹追踪算法，再到摄像机跟随与程序化生成，每一讲都是真实游戏项目中的核心系统。通过本章，你将看到数学如何转化为游戏功能，理解"数学是游戏的骨架"这句话的真正含义。

### 第30讲：角色面向与瞄准

#### 概念

**角色面向（Character Facing）** 是让游戏角色朝向特定方向的功能，如面向鼠标、面向敌人、面向移动方向。**瞄准（Aiming）** 是面向的延伸，不仅确定方向，还要计算射击角度、预测目标位置。这是射击游戏、塔防游戏、动作游戏的基础交互系统。

#### 原理

角色面向的核心是**用atan2计算角度**。给定从角色到目标的方向向量 `(dx, dy)`，朝向角度为 `atan2(dy, dx)`。atan2相比atan的优势是能处理所有象限（包括dx=0的情况），返回[-π, π]的完整角度。

瞄准系统的几个层次：
1. **静态瞄准**：直接朝向目标位置，适合静止目标
2. **平滑瞄准**：用角度Lerp让转向有过渡感，避免瞬间锁定
3. **限制转速**：炮塔等有最大旋转速度，无法瞬间转向
4. **预测瞄准**：考虑目标移动速度，提前量射击（第8讲已涉及）

角色面向的实现还需考虑**贴图方向**：默认贴图通常朝右，若朝左需水平翻转。判断方法：若角度在(90°, 270°)区间则朝左，否则朝右。这是2D游戏角色面向切换的标准做法。

瞄准线的绘制：从枪口位置沿瞄准方向画一条线段或射线，长度可调。配合射线检测，能实现"瞄准即显示命中点"的反馈。

#### 例子

```python
import math

class Vector2:
    def __init__(self, x=0.0, y=0.0):
        self.x = x; self.y = y
    def __sub__(self, o): return Vector2(self.x-o.x, self.y-o.y)
    def length(self): return math.sqrt(self.x**2 + self.y**2)
    def __repr__(self): return f"V({self.x:.2f}, {self.y:.2f})"

def angle_lerp(a, b, t):
    diff = (b - a + 540) % 360 - 180
    return a + diff * t

class Character:
    """角色：位置、朝向、瞄准"""
    def __init__(self, pos):
        self.pos = pos
        self.facing = 0  # 朝向角度（度），0=右
        self.target_facing = 0
        self.rotation_speed = 8  # 旋转速度
        self.aim_range = 500  # 瞄准线长度
    
    def aim_at(self, target_pos):
        """瞄准目标位置"""
        dx = target_pos.x - self.pos.x
        dy = target_pos.y - self.pos.y
        self.target_facing = math.degrees(math.atan2(dy, dx))
    
    def update(self, dt):
        """平滑转向目标朝向"""
        t = 1 - math.exp(-self.rotation_speed * dt)
        self.facing = angle_lerp(self.facing, self.target_facing, t)
        self.facing %= 360
    
    def get_facing_direction(self):
        """返回单位朝向向量"""
        rad = math.radians(self.facing)
        return Vector2(math.cos(rad), math.sin(rad))
    
    def get_muzzle_pos(self, offset=30):
        """获取枪口位置（角色前方offset像素）"""
        direction = self.get_facing_direction()
        return Vector2(self.pos.x + direction.x * offset,
                       self.pos.y + direction.y * offset)
    
    def is_facing_left(self):
        """是否朝左（用于贴图翻转）"""
        return 90 < self.facing < 270

# 应用1：角色面向鼠标
class MouseAimingController:
    def __init__(self, character):
        self.character = character
    
    def update(self, mouse_pos, dt):
        self.character.aim_at(mouse_pos)
        self.character.update(dt)
        # 返回是否需要翻转贴图
        return self.character.is_facing_left()

hero = Character(Vector2(100, 100))
controller = MouseAimingController(hero)
# 模拟鼠标在右上方
mouse = Vector2(200, 50)
for _ in range(10):
    flip = controller.update(mouse, 1/60)
print(f"角色朝向: {hero.facing:.1f}°, 朝左: {flip}")

# 应用2：炮塔限制转速
class Turret:
    def __init__(self, pos, max_speed=90):
        self.pos = pos
        self.angle = 0
        self.max_speed = max_speed  # 度/秒
    
    def aim_at(self, target, dt):
        dx = target.x - self.pos.x
        dy = target.y - self.pos.y
        target_angle = math.degrees(math.atan2(dy, dx))
        diff = (target_angle - self.angle + 540) % 360 - 180
        # 限制每帧旋转量
        max_step = self.max_speed * dt
        if abs(diff) <= max_step:
            self.angle = target_angle
        else:
            self.angle += math.copysign(max_step, diff)
        self.angle %= 360

# 应用3：瞄准线绘制与命中检测
def draw_aim_line(character, obstacles):
    """绘制瞄准线并检测命中点"""
    muzzle = character.get_muzzle_pos()
    direction = character.get_facing_direction()
    # 沿瞄准方向发射射线，找最近命中点
    closest_hit = None
    closest_dist = character.aim_range
    for obs in obstacles:
        # 简化：假设障碍物是圆
        # 射线-圆相交检测
        oc = muzzle - obs['center']
        b = 2 * (oc.x * direction.x + oc.y * direction.y)
        c = oc.x**2 + oc.y**2 - obs['radius']**2
        disc = b*b - 4*c
        if disc < 0: continue
        t = (-b - math.sqrt(disc)) / 2
        if 0 < t < closest_dist:
            closest_dist = t
            closest_hit = obs
    # 返回瞄准线终点
    end_x = muzzle.x + direction.x * closest_dist
    end_y = muzzle.y + direction.y * closest_dist
    return Vector2(end_x, end_y), closest_hit

# 应用4：连发武器——扇形散射
def shotgun_fire(character, bullet_count=5, spread=15):
    """霰弹枪：多发子弹扇形散射"""
    bullets = []
    base_angle = character.facing
    for i in range(bullet_count):
        # 在base_angle±spread范围内均匀分布
        offset = (i / (bullet_count - 1) - 0.5) * 2 * spread
        angle = base_angle + offset
        rad = math.radians(angle)
        direction = Vector2(math.cos(rad), math.sin(rad))
        bullets.append({
            'pos': character.get_muzzle_pos(),
            'dir': direction,
            'speed': 800
        })
    return bullets

# 应用5：自动瞄准最近敌人
def auto_aim(character, enemies, max_angle=45):
    """在角色前方max_angle范围内自动选最近敌人"""
    facing_dir = character.get_facing_direction()
    best_target = None
    best_dist = float('inf')
    for enemy in enemies:
        to_enemy = enemy - character.pos
        dist = to_enemy.length()
        if dist > best_dist: continue
        # 检查是否在瞄准锥内
        cos_angle = facing_dir.x * (to_enemy.x/dist) + facing_dir.y * (to_enemy.y/dist)
        if cos_angle > math.cos(math.radians(max_angle)):
            best_dist = dist
            best_target = enemy
    if best_target:
        character.aim_at(best_target)
    return best_target
```

角色面向与瞄准示意：

```
角色面向鼠标：

  鼠标 ●
        ╲
         ╲ 瞄准线
          ╲
    ●───────●  角色 → 朝向
   角色

朝向角度计算：
  dx = mouse.x - char.x
  dy = mouse.y - char.y
  angle = atan2(dy, dx)  ← 自动处理所有象限

贴图翻转：
  朝右（0°~90°或270°~360°）：正常贴图
  朝左（90°~270°）：水平翻转贴图

炮塔限制转速：
  目标角度: 90°
  当前角度: 0°
  最大转速: 90°/秒
  每帧旋转: 90° * dt
  → 需要1秒才能转到位（不会瞬间锁定）
```

#### 总结

- 角色面向用 `atan2(dy, dx)` 计算角度，处理所有象限
- 平滑转向用角度Lerp，避免瞬间锁定
- 炮塔等需限制最大转速，模拟真实机械感
- 贴图翻转：角度在(90°, 270°)则水平翻转
- 瞄准线配合射线检测，显示命中点
- 霰弹枪用扇形散射，自动瞄准选前方锥形内最近敌人

---

### 第31讲：子弹追踪与导弹算法

#### 概念

**追踪子弹（Homing Bullet）** 是能自动追击目标的智能弹药，常见于导弹、追踪箭、自导魔法等。追踪算法让子弹每帧调整方向朝向目标，产生"咬住不放"的追击感。这是Boss战、塔防、射击游戏的经典机制，能创造紧张刺激的对抗体验。

#### 原理

追踪子弹的核心是**每帧重新计算朝向**：
1. 计算从子弹到目标的方向向量
2. 归一化得到单位方向
3. 用角度Lerp平滑过渡到新方向（避免瞬间转向）
4. 沿新方向移动

追踪算法的几个变体：
1. **完美追踪**：每帧直接朝向目标，转弯无限制（过于强大）
2. **限制转弯率**：每帧最多转N度，模拟导弹惯性（推荐）
3. **预测追踪**：预判目标未来位置，提前量追击（高级）
4. **比例导引**：根据相对速度调整转弯率（真实导弹算法）

限制转弯率是平衡追踪子弹的关键。若转弯率太高，子弹能"急转弯"咬住任何目标，过于强大；若太低，目标容易甩开。通常转弯率在3-10度/帧之间，配合子弹速度调参。

预测追踪能显著提升命中率：根据目标当前速度，预测其t秒后位置，朝该位置追击而非当前位置。这需要解二次方程求拦截时间（第8讲已涉及）。

#### 例子

```python
import math

class Vector2:
    def __init__(self, x=0.0, y=0.0):
        self.x = x; self.y = y
    def __add__(self, o): return Vector2(self.x+o.x, self.y+o.y)
    def __sub__(self, o): return Vector2(self.x-o.x, self.y-o.y)
    def __mul__(self, s): return Vector2(self.x*s, self.y*s)
    def length(self): return math.sqrt(self.x**2 + self.y**2)
    def length_squared(self): return self.x**2 + self.y**2
    def normalize(self):
        l = self.length()
        return Vector2(0,0) if l < 1e-9 else Vector2(self.x/l, self.y/l)
    def dot(self, o): return self.x*o.x + self.y*o.y
    def __repr__(self): return f"V({self.x:.2f}, {self.y:.2f})"

def angle_lerp(a, b, t):
    diff = (b - a + 540) % 360 - 180
    return a + diff * t

class HomingMissile:
    """追踪导弹"""
    def __init__(self, pos, target, speed=300, turn_rate=5):
        self.pos = pos
        self.target = target  # 目标对象（有pos属性）
        self.speed = speed  # 飞行速度
        self.turn_rate = turn_rate  # 每帧最大转弯度数
        self.angle = 0  # 当前朝向
        # 初始朝向目标
        self._update_angle_to_target(1.0)
    
    def _update_angle_to_target(self, t):
        """朝目标方向转t比例"""
        dx = self.target.pos.x - self.pos.x
        dy = self.target.pos.y - self.pos.y
        target_angle = math.degrees(math.atan2(dy, dx))
        self.angle = angle_lerp(self.angle, target_angle, t)
    
    def update(self, dt):
        """每帧更新"""
        # 计算目标方向
        to_target = self.target.pos - self.pos
        if to_target.length_squared() < 1e-6:
            return  # 已到达
        target_angle = math.degrees(math.atan2(to_target.y, to_target.x))
        # 限制转弯率
        diff = (target_angle - self.angle + 540) % 360 - 180
        max_turn = self.turn_rate * 60 * dt  # 转弯率/秒
        if abs(diff) > max_turn:
            diff = math.copysign(max_turn, diff)
        self.angle += diff
        # 沿当前朝向移动
        rad = math.radians(self.angle)
        direction = Vector2(math.cos(rad), math.sin(rad))
        self.pos = self.pos + direction * (self.speed * dt)

class Target:
    def __init__(self, pos):
        self.pos = pos

# 应用1：基础追踪导弹
missile = HomingMissile(Vector2(0, 0), Target(Vector2(100, 100)), 
                        speed=200, turn_rate=3)
for i in range(30):
    missile.update(1/60)
    print(f"第{i}帧: 位置={missile.pos}, 角度={missile.angle:.1f}")

# 应用2：预测追踪（提前量射击）
class PredictiveMissile(HomingMissile):
    """预测追踪：朝目标未来位置追击"""
    def __init__(self, pos, target, speed=300, turn_rate=5, 
                 prediction_time=0.5):
        super().__init__(pos, target, speed, turn_rate)
        self.prediction_time = prediction_time
    
    def update(self, dt):
        # 预测目标未来位置
        if hasattr(self.target, 'vel'):
            predicted_pos = self.target.pos + self.target.vel * self.prediction_time
        else:
            predicted_pos = self.target.pos
        # 朝预测位置转向
        to_pred = predicted_pos - self.pos
        target_angle = math.degrees(math.atan2(to_pred.y, to_pred.x))
        diff = (target_angle - self.angle + 540) % 360 - 180
        max_turn = self.turn_rate * 60 * dt
        if abs(diff) > max_turn:
            diff = math.copysign(max_turn, diff)
        self.angle += diff
        # 移动
        rad = math.radians(self.angle)
        direction = Vector2(math.cos(rad), math.sin(rad))
        self.pos = self.pos + direction * (self.speed * dt)

# 应用3：完美拦截（解方程求相遇时间）
def perfect_intercept(shooter, target, target_vel, bullet_speed):
    """计算完美拦截的发射角度"""
    to_target = target - shooter
    dist = to_target.length()
    # 解二次方程求拦截时间
    a = target_vel.length_squared() - bullet_speed ** 2
    b = 2 * to_target.dot(target_vel)
    c = -dist ** 2
    if abs(a) < 1e-9:
        t = -c / b if b != 0 else -1
    else:
        disc = b*b - 4*a*c
        if disc < 0: return None
        t = (-b - math.sqrt(disc)) / (2*a)
        if t < 0: t = (-b + math.sqrt(disc)) / (2*a)
    if t < 0: return None
    # 拦截点
    intercept_point = target + target_vel * t
    # 发射角度
    dx = intercept_point.x - shooter.x
    dy = intercept_point.y - shooter.y
    return math.degrees(math.atan2(dy, dx)), intercept_point

# 应用4：多导弹协同攻击（避免重叠）
class MissileSwarm:
    """多导弹协同：每枚导弹有不同初始角度，避免同时到达"""
    def __init__(self, shooter_pos, target, count=5):
        self.missiles = []
        for i in range(count):
            # 不同初始角度形成扇形
            initial_angle = -30 + (60 * i / (count - 1))
            missile = HomingMissile(
                Vector2(shooter_pos.x, shooter_pos.y),
                target, speed=200, turn_rate=2
            )
            missile.angle = initial_angle
            self.missiles.append(missile)
    
    def update(self, dt):
        for m in self.missiles:
            m.update(dt)

# 应用5：规避追踪——目标甩开导弹
class EvasiveTarget:
    """会规避的目标：垂直于导弹方向移动"""
    def __init__(self, pos, speed=100):
        self.pos = pos
        self.vel = Vector2(0, 0)
        self.speed = speed
    
    def evade(self, missile):
        """垂直于导弹到自身的方向移动"""
        to_self = self.pos - missile.pos
        # 垂直方向（两个选择，选远离导弹的）
        perp1 = Vector2(-to_self.y, to_self.x).normalize()
        perp2 = Vector2(to_self.y, -to_self.x).normalize()
        # 选与当前速度同向的
        if self.vel.dot(perp1) > 0:
            self.vel = perp1 * self.speed
        else:
            self.vel = perp2 * self.speed
    
    def update(self, dt):
        self.pos = self.pos + self.vel * dt
```

追踪导弹的转弯率示意：

```
转弯率=∞（完美追踪）：
  目标 ●
       │ ← 子弹每帧直接朝向目标
       │
       ●  子弹
  任何目标都能命中（过于强大）

转弯率=5°/帧（限制转弯）：
  目标 ●────→ 移动
       ╱
      ╱
     ╱  ← 子弹转弯有上限
    ●
  子弹可能被甩开（平衡）

预测追踪：
  目标 ●────→ 未来位置 ●
                       ↑
                       │
                       ● 子弹朝未来位置
  提前量射击，命中率更高
```

#### 总结

- 追踪子弹每帧重新计算朝向，朝向目标移动
- **限制转弯率**是平衡追踪子弹的关键，避免过于强大
- 预测追踪朝目标未来位置追击，命中率更高
- 完美拦截通过解二次方程求相遇时间
- 多导弹协同用不同初始角度形成扇形攻击
- 目标可垂直于导弹方向移动来规避追踪

---

### 第32讲：摄像机跟随与平滑

#### 概念

**摄像机跟随（Camera Follow）** 是让游戏摄像机跟随主角移动的系统。好的摄像机跟随应该平滑、不抖动、有"前瞻性"——能预判玩家移动方向，提前显示前方区域。这是平台跳跃、动作冒险、RPG等游戏的核心体验系统，直接影响游戏手感。

#### 原理

摄像机跟随的几个层次：
1. **硬跟随**：摄像机位置=玩家位置，完全同步（机械感强，不推荐）
2. **Lerp跟随**：用Lerp平滑过渡，有"刹车"感（基础方案）
3. **弹簧跟随**：用弹簧+阻尼系统，有弹性回弹感（高级方案）
4. **前瞻跟随**：根据玩家速度提前移动，显示前方区域（专业方案）
5. **边界限制**：摄像机不超出地图边界（必备方案）

Lerp跟随的公式：`cam_pos = Lerp(cam_pos, target_pos, speed*dt)`，speed越大跟随越快。这种"指数趋近"让摄像机快速接近后逐渐减速，产生自然的"刹车"感。

弹簧跟随用弹簧物理：摄像机受到朝向目标的弹簧力，以及与速度反向的阻尼力。公式：`force = (target - pos) * stiffness - vel * damping`。调整stiffness和damping能创造不同手感——stiffness高则跟随紧，damping低则有弹性振荡。

前瞻跟随根据玩家速度提前移动：`look_ahead = player_vel * prediction_time`，目标位置为 `player_pos + look_ahead`。这让玩家向前跑时摄像机提前显示前方，提升操作感。

边界限制确保摄像机不显示地图外区域：`cam_x = clamp(cam_x, map_left + screen_w/2, map_right - screen_w/2)`。

#### 例子

```python
import math

class Vector2:
    def __init__(self, x=0.0, y=0.0):
        self.x = x; self.y = y
    def __add__(self, o): return Vector2(self.x+o.x, self.y+o.y)
    def __sub__(self, o): return Vector2(self.x-o.x, self.y-o.y)
    def __mul__(self, s): return Vector2(self.x*s, self.y*s)
    def length(self): return math.sqrt(self.x**2 + self.y**2)
    def lerp(self, other, t): return self + (other - self) * t
    def __repr__(self): return f"V({self.x:.2f}, {self.y:.2f})"

def clamp(v, lo, hi):
    return max(lo, min(hi, v))

# 应用1：Lerp跟随（基础方案）
class LerpCamera:
    def __init__(self, pos):
        self.pos = pos
        self.speed = 5  # 跟随速度
    
    def update(self, target, dt):
        t = 1 - math.exp(-self.speed * dt)
        self.pos = self.pos.lerp(target, t)

# 应用2：弹簧跟随（高级方案，有弹性）
class SpringCamera:
    def __init__(self, pos):
        self.pos = pos
        self.vel = Vector2(0, 0)
        self.stiffness = 50  # 弹簧刚度
        self.damping = 8     # 阻尼
    
    def update(self, target, dt):
        # 弹簧力：朝向目标
        force = (target - self.pos) * self.stiffness
        # 阻尼力：与速度反向
        force = force - self.vel * self.damping
        # 加速度=力/质量（质量1）
        acc = force
        self.vel = self.vel + acc * dt
        self.pos = self.pos + self.vel * dt

# 应用3：前瞻跟随（专业方案）
class LookAheadCamera:
    def __init__(self, pos):
        self.pos = pos
        self.target_pos = pos  # 平滑后的目标位置
        self.look_ahead = Vector2(0, 0)  # 前瞻偏移
        self.follow_speed = 5
        self.look_ahead_speed = 3
        self.prediction_time = 0.3  # 预测时间
    
    def update(self, player_pos, player_vel, dt):
        # 计算前瞻位置（玩家未来位置）
        predicted_pos = player_pos + player_vel * self.prediction_time
        # 平滑过渡前瞻偏移
        target_look_ahead = (predicted_pos - player_pos)
        t1 = 1 - math.exp(-self.look_ahead_speed * dt)
        self.look_ahead = self.look_ahead.lerp(target_look_ahead, t1)
        # 目标位置 = 玩家位置 + 前瞻偏移
        target = player_pos + self.look_ahead
        # 平滑跟随
        t2 = 1 - math.exp(-self.follow_speed * dt)
        self.pos = self.pos.lerp(target, t2)

# 应用4：边界限制
class BoundedCamera:
    """带地图边界的摄像机"""
    def __init__(self, pos, screen_w, screen_h, map_bounds):
        self.pos = pos
        self.screen_w = screen_w
        self.screen_h = screen_h
        self.map_left, self.map_top, self.map_right, self.map_bottom = map_bounds
        self.follow_speed = 5
    
    def update(self, target, dt):
        t = 1 - math.exp(-self.follow_speed * dt)
        self.pos = self.pos.lerp(target, t)
        # 限制不超出地图边界
        half_w = self.screen_w / 2
        half_h = self.screen_h / 2
        self.pos.x = clamp(self.pos.x, 
                          self.map_left + half_w, 
                          self.map_right - half_w)
        self.pos.y = clamp(self.pos.y,
                          self.map_top + half_h,
                          self.map_bottom - half_h)
    
    def world_to_screen(self, world_pos):
        """世界坐标转屏幕坐标"""
        return Vector2(world_pos.x - self.pos.x + self.screen_w/2,
                       world_pos.y - self.pos.y + self.screen_h/2)

# 应用5：区域触发摄像机（如Boss战时固定视角）
class ZoneCamera:
    """进入特定区域时摄像机固定"""
    def __init__(self, pos):
        self.pos = pos
        self.target_pos = pos
        self.in_zone = False
        self.follow_speed = 5
    
    def enter_zone(self, zone_center):
        """进入固定区域"""
        self.target_pos = zone_center
        self.in_zone = True
    
    def exit_zone(self):
        """退出固定区域，恢复跟随"""
        self.in_zone = False
    
    def update(self, player_pos, dt):
        if not self.in_zone:
            self.target_pos = player_pos
        t = 1 - math.exp(-self.follow_speed * dt)
        self.pos = self.pos.lerp(self.target_pos, t)

# 应用6：摄像机震动效果
class CameraShake:
    """摄像机震动（受击、爆炸等）"""
    def __init__(self):
        self.intensity = 0
        self.duration = 0
        self.elapsed = 0
    
    def shake(self, intensity, duration):
        self.intensity = intensity
        self.duration = duration
        self.elapsed = 0
    
    def get_offset(self, dt):
        """返回当前震动偏移"""
        if self.elapsed >= self.duration:
            return Vector2(0, 0)
        self.elapsed += dt
        # 衰减强度
        t = self.elapsed / self.duration
        current_intensity = self.intensity * (1 - t)
        # 随机偏移
        import random
        return Vector2(
            random.uniform(-1, 1) * current_intensity,
            random.uniform(-1, 1) * current_intensity
        )

# 测试前瞻跟随
cam = LookAheadCamera(Vector2(0, 0))
player_pos = Vector2(100, 100)
player_vel = Vector2(200, 0)  # 向右快速移动
for i in range(10):
    cam.update(player_pos, player_vel, 1/60)
    player_pos = player_pos + player_vel * (1/60)
print(f"摄像机位置: {cam.pos}")  # 应该在玩家前方
```

摄像机跟随的几种模式对比：

```
硬跟随（不推荐）：
  玩家 ●  摄像机 ●  完全同步
  机械感强，无过渡

Lerp跟随（基础）：
  玩家 ●────→
  摄像机 ●──→  稍微滞后，有刹车感
  平滑，但反应稍慢

弹簧跟随（弹性）：
  玩家 ●────→
  摄像机 ●→←→  有轻微振荡
  有弹性回弹感

前瞻跟随（专业）：
  玩家 ●────→
  摄像机   ●────→  提前在前方
  玩家向前跑时能看到更多前方区域

边界限制：
  地图左边界│     摄像机     │地图右边界
            ●←─── 不能超出
  防止显示地图外区域
```

#### 总结

- 硬跟随机械感强，不推荐
- **Lerp跟随**用指数趋近，基础平滑方案
- **弹簧跟随**有弹性回弹感，调整stiffness和damping控制手感
- **前瞻跟随**根据玩家速度提前移动，显示前方区域
- **边界限制**确保摄像机不显示地图外区域
- 摄像机震动用随机偏移+衰减，用于受击、爆炸反馈
- 区域触发摄像机用于Boss战等固定视角场景

---

### 第33讲：程序化生成与Perlin噪声

#### 概念

**程序化生成（Procedural Generation）** 是用算法而非手工创建游戏内容的技术，包括地形、纹理、关卡、植被等。**Perlin噪声（Perlin Noise）** 是程序化生成的基础算法，由Ken Perlin发明，能生成自然平滑的随机噪声，广泛用于地形生成、云层、木纹、火焰等自然纹理。掌握Perlin噪声是构建无限世界游戏的关键。

#### 原理

普通随机数（如Math.random）产生"白噪声"——每个点独立随机，看起来像电视雪花，不适合自然纹理。Perlin噪声的核心是**梯度噪声**：在网格点定义随机梯度，网格内的值通过插值得到，产生连续平滑的随机场。

Perlin噪声的算法步骤（2D）：
1. 将坐标映射到整数网格点
2. 在每个网格点预定义随机梯度向量
3. 计算每个网格点对当前位置的贡献（梯度与距离向量的点积）
4. 用平滑插值（如 `3t²-2t³` 或 `6t⁵-15t⁴+10t³`）混合四个角的贡献
5. 输出[-1, 1]范围的噪声值

Perlin噪声的关键特性：
- **连续性**：相邻输入产生相近输出，无突变
- **可重复性**：相同种子产生相同结果
- **多尺度**：通过叠加不同频率的噪声（分形布朗运动FBM）产生丰富细节

**分形布朗运动（FBM, Fractal Brownian Motion）** 是叠加多个不同频率和振幅的Perlin噪声，模拟自然界的自相似性。公式：`fbm(x) = Σ noise(x * 2^i) / 2^i`，i从0到N。低频提供大尺度结构，高频提供细节。

游戏中的应用：
- **地形生成**：用2D Perlin噪声生成高度图，创建山脉、平原、海洋
- **云层纹理**：用2D Perlin噪声生成动态云层
- **木纹、大理石**：用Perlin噪声调制颜色或法线
- **洞穴生成**：用2D Perlin噪声的阈值化生成有机形状的洞穴
- **植被分布**：用噪声决定草地、森林、沙漠的分布

#### 例子

```python
import math
import random

class PerlinNoise:
    """2D Perlin噪声生成器"""
    def __init__(self, seed=0):
        random.seed(seed)
        # 排列表（用于哈希查找梯度）
        self.perm = list(range(256))
        random.shuffle(self.perm)
        self.perm = self.perm * 2  # 扩展到512避免取模
    
    def fade(self, t):
        """平滑插值函数：6t⁵-15t⁴+10t³"""
        return t * t * t * (t * (t * 6 - 15) + 10)
    
    def lerp(self, a, b, t):
        return a + t * (b - a)
    
    def grad(self, hash_val, x, y):
        """根据哈希值选择梯度向量并点积"""
        # 8个梯度方向
        gradients = [(1,1),(-1,1),(1,-1),(-1,-1),
                     (1,0),(-1,0),(0,1),(0,-1)]
        g = gradients[hash_val % 8]
        return g[0] * x + g[1] * y
    
    def noise(self, x, y):
        """计算(x,y)处的Perlin噪声值，返回[-1, 1]"""
        # 整数网格坐标
        xi = int(math.floor(x)) & 255
        yi = int(math.floor(y)) & 255
        # 小数部分（网格内位置）
        xf = x - math.floor(x)
        yf = y - math.floor(y)
        # 平滑插值
        u = self.fade(xf)
        v = self.fade(yf)
        # 四个角的哈希值
        aa = self.perm[self.perm[xi] + yi]
        ab = self.perm[self.perm[xi] + yi + 1]
        ba = self.perm[self.perm[xi + 1] + yi]
        bb = self.perm[self.perm[xi + 1] + yi + 1]
        # 四个角的贡献
        x1 = self.lerp(self.grad(aa, xf, yf),
                       self.grad(ba, xf-1, yf), u)
        x2 = self.lerp(self.grad(ab, xf, yf-1),
                       self.grad(bb, xf-1, yf-1), u)
        return self.lerp(x1, x2, v)
    
    def fbm(self, x, y, octaves=4, persistence=0.5, lacunarity=2.0):
        """分形布朗运动：叠加多频率噪声"""
        total = 0
        amplitude = 1
        frequency = 1
        max_value = 0
        for _ in range(octaves):
            total += self.noise(x * frequency, y * frequency) * amplitude
            max_value += amplitude
            amplitude *= persistence
            frequency *= lacunarity
        return total / max_value  # 归一化到[-1, 1]

# 应用1：地形生成
class TerrainGenerator:
    """用Perlin噪声生成2D地形高度图"""
    def __init__(self, seed=0):
        self.noise = PerlinNoise(seed)
    
    def get_height(self, x):
        """获取x位置的地形高度"""
        # 用FBM生成丰富细节
        h = self.noise.fbm(x * 0.01, 0, octaves=4)
        # 映射到合理的高度范围
        return 200 + h * 100  # 基础高度200，振幅100
    
    def get_biome(self, x, y):
        """根据噪声决定生物群系"""
        temp = self.noise.noise(x * 0.005, 0)  # 温度
        moist = self.noise.noise(0, y * 0.005)  # 湿度
        if temp > 0.3 and moist > 0.3: return "森林"
        if temp > 0.3 and moist < -0.3: return "沙漠"
        if temp < -0.3: return "雪原"
        return "草原"

# 应用2：洞穴生成（2D阈值化）
class CaveGenerator:
    """用2D Perlin噪声阈值化生成洞穴"""
    def __init__(self, seed=0):
        self.noise = PerlinNoise(seed)
    
    def is_solid(self, x, y):
        """返回该位置是否为实心方块"""
        # 用两个噪声相乘创造有机形状
        n1 = self.noise.noise(x * 0.05, y * 0.05)
        n2 = self.noise.noise(x * 0.1, y * 0.1)
        # 阈值化：噪声值小于0则空气（洞穴）
        return n1 * n2 > 0  # 调整阈值控制洞穴大小

# 应用3：云层纹理
class CloudTexture:
    """生成动态云层"""
    def __init__(self, seed=0):
        self.noise = PerlinNoise(seed)
    
    def get_density(self, x, y, time=0):
        """获取云层密度（0~1）"""
        # 加入时间维度让云层流动
        n = self.noise.fbm(x * 0.01 + time * 0.1, y * 0.01, octaves=3)
        return (n + 1) / 2  # 归一化到[0, 1]

# 应用4：木纹纹理
class WoodTexture:
    """生成木纹"""
    def __init__(self, seed=0):
        self.noise = PerlinNoise(seed)
    
    def get_color(self, x, y):
        """获取木纹颜色"""
        # 用噪声调制同心圆
        n = self.noise.noise(x * 0.1, y * 0.1)
        # 同心圆基础 + 噪声扰动
        rings = math.sin(math.sqrt(x*x + y*y) * 0.3 + n * 5)
        # 映射到木纹颜色
        if rings > 0:
            return (139, 69, 19)  # 棕色
        else:
            return (160, 82, 45)  # 浅棕

# 应用5：随机分布物体（如草地、石头）
class ObjectScatterer:
    """用噪声决定物体分布密度"""
    def __init__(self, seed=0):
        self.noise = PerlinNoise(seed)
    
    def should_place(self, x, y, threshold=0.3):
        """该位置是否应该放置物体"""
        n = self.noise.noise(x * 0.1, y * 0.1)
        return n > threshold

# 测试
perlin = PerlinNoise(seed=42)
print("单点噪声:", perlin.noise(0.5, 0.5))
print("FBM噪声:", perlin.fbm(0.5, 0.5, octaves=4))

# 生成地形高度图
terrain = TerrainGenerator(seed=42)
heights = [terrain.get_height(x) for x in range(0, 1000, 50)]
print(f"地形高度范围: {min(heights):.1f} ~ {max(heights):.1f}")

# 生成洞穴
cave = CaveGenerator(seed=42)
for y in range(5):
    row = ""
    for x in range(20):
        row += "█" if cave.is_solid(x, y) else " "
    print(row)
```

Perlin噪声与白噪声对比：

```
白噪声（Math.random）：
  ░▒▓█░▒▓█░▒▓█░▒▓█░▒▓█
  每个点独立随机，像电视雪花
  不适合自然纹理

Perlin噪声：
  ░░░▒▒▒▓▓▓██▓▓▓▒▒▒░░░
  相邻点相近，连续平滑
  适合自然纹理

FBM（多频率叠加）：
  ░░▒▓█▓▒░░▒▓█▓▒░░▒▓█▓▒░
  大尺度结构 + 小尺度细节
  最接近自然形态

地形生成应用：
  高度
   │    ╱╲      ╱╲
   │   ╱  ╲    ╱  ╲    山脉
   │  ╱    ╲  ╱    ╲
   │ ╱      ╲╱      ╲
   └────────────────────→ x
   用FBM生成的高度图，自然起伏
```

#### 总结

- Perlin噪声是程序化生成的基础，产生连续平滑的随机场
- 算法核心：网格点梯度 + 平滑插值
- **FBM**叠加多频率噪声，模拟自然界的自相似性
- 地形生成用FBM生成高度图，配合生物群系判定
- 洞穴生成用噪声阈值化，创造有机形状
- 云层、木纹、物体分布都依赖Perlin噪声
- 相同种子产生相同结果，支持无限世界生成

---

## 课程结语

恭喜你完成了《2D游戏开发中的数学》全部33讲！从最基础的标量与向量，到高级的程序化生成，你已经系统掌握了2D游戏开发所需的全部数学知识。

### 知识体系回顾

**基础层（第1-2章）**：向量与坐标系是游戏数学的基石。加减数乘、点积叉积、归一化与反射，这些运算贯穿了后续所有章节。

**变换层（第3章）**：矩阵让你能统一表达平移、缩放、旋转，复合变换是游戏引擎底层的基础机制。

**几何层（第4-5章）**：从点线圆矩形等图元，到AABB、SAT等碰撞算法，这是游戏交互的核心。

**物理层（第6章）**：运动学方程、抛物运动、反弹响应、摩擦阻尼，让游戏世界"活起来"。

**动画层（第7章）**：Lerp、缓动、贝塞尔、角度插值，让游戏告别机械感，拥有丝滑感。

**实战层（第8章）**：瞄准、追踪、摄像机、程序化生成，将数学转化为真实游戏功能。

### 进阶学习建议

1. **3D扩展**：本课程的2D知识大部分可直接扩展到3D，向量变为3维，矩阵变为4×4，SAT扩展为3D版本
2. **物理引擎**：学习Box2D、PhysX等开源物理引擎的实现，理解约束、关节、连续碰撞检测
3. **图形学**：学习渲染管线、着色器、光照模型，理解游戏画面的数学原理
4. **AI算法**：学习A*寻路、行为树、状态机，将数学应用于游戏智能
5. **数值方法**：学习数值积分、微分方程数值解，处理更复杂的物理模拟

### 实践项目建议

- **平台跳跃游戏**：综合运用AABB碰撞、抛物运动、摄像机跟随
- **弹球游戏**：综合运用圆碰撞、反射、恢复系数
- **塔防游戏**：综合运用路径生成、追踪算法、碰撞检测
- **Roguelike地牢**：综合运用Perlin噪声、程序化生成、随机算法

数学是游戏的骨架，创意是游戏的灵魂。希望本课程能让你在创造游戏时，不再被数学困扰，而是能自由地将创意转化为现实。祝你游戏开发之旅愉快！







