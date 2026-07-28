# llama.cpp 系统教程

> 本教程以教科书形式系统讲解 llama.cpp，从基础到高级共 30 讲，分 8 章。每讲包含「概念 / 原理 / 例子 / 总结」四个标准部分，循序渐进，注重实战。

---

## 课程总览

| 项目 | 说明 |
|------|------|
| 预计讲数 | 30 讲（8 章） |
| 学习目标 | 从零基础到独立部署、调优、扩展 llama.cpp，能在本地高效运行各类开源大模型 |
| 适用人群 | AI 应用开发者、本地 LLM 爱好者、嵌入式/边缘计算工程师、隐私敏感场景开发者 |
| 学习路径 | 基础入门 → 模型与量化 → 命令行推理 → 硬件加速 → Server/API → 高级特性 → 性能部署 → 集成扩展 |
| 推荐硬件 | 至少 8GB RAM（运行 7B Q4 模型），16GB+ 更佳；可选 GPU 加速 |
| 软件版本 | llama.cpp master 分支（2024+ 版本，使用 llama-cli / llama-server 等新命名） |

---

## 详细章节目录

### 第 1 章: 基础入门 (4 讲)
- 第 1 讲: llama.cpp 是什么与为什么
- 第 2 讲: 核心概念 (GGUF、量化、KV Cache)
- 第 3 讲: 环境准备与依赖
- 第 4 讲: 从源码编译

### 第 2 章: 模型与量化 (4 讲)
- 第 5 讲: GGUF 模型格式
- 第 6 讲: 量化原理与类型
- 第 7 讲: 模型转换与量化操作
- 第 8 讲: 模型选择与下载

### 第 3 章: 命令行推理 (4 讲)
- 第 9 讲: llama-cli 基础用法
- 第 10 讲: 生成参数详解
- 第 11 讲: 上下文与 KV Cache 管理
- 第 12 讲: 交互式对话与角色扮演

### 第 4 章: 硬件加速 (5 讲)
- 第 13 讲: CPU 优化与线程
- 第 14 讲: CUDA 加速
- 第 15 讲: Metal (Apple Silicon) 加速
- 第 16 讲: Vulkan 与 OpenCL
- 第 17 讲: 多 GPU 与张量拆分

### 第 5 章: Server 与 API (4 讲)
- 第 18 讲: llama-server 启动
- 第 19 讲: OpenAI 兼容 API
- 第 20 讲: 流式输出与函数调用
- 第 21 讲: 嵌入向量生成

### 第 6 章: 高级特性 (4 讲)
- 第 22 讲: Speculative Decoding
- 第 23 讲: LoRA 适配器
- 第 24 讲: 多模态模型 (LLaVA)
- 第 25 讲: Grammar 约束生成

### 第 7 章: 性能与部署 (3 讲)
- 第 26 讲: 性能调优与基准测试
- 第 27 讲: 容器化部署
- 第 28 讲: 生产环境最佳实践

### 第 8 章: 集成与扩展 (2 讲)
- 第 29 讲: Python 绑定 (llama-cpp-python)
- 第 30 讲: 二次开发与 API 扩展

---

# 第 1 章: 基础入门

## 第 1 讲: llama.cpp 是什么与为什么

### 概念

**llama.cpp** 是由 Georgi Gerganov 开发的开源 C/C++ 库，用于在本地高效运行大语言模型（LLM）。它最初为了运行 Meta 的 LLaMA 模型而生，现已支持 LLaMA、Mistral、Qwen、DeepSeek、Phi、Gemma 等几乎所有主流开源模型架构。项目以纯 C/C++ 实现，无第三方依赖，可在 CPU、GPU、嵌入式设备上运行。

### 原理

llama.cpp 的核心设计哲学是「**轻量 + 高效 + 跨平台**」：

1. **纯 C/C++ 实现**：不依赖 PyTorch、TensorFlow 等重型框架，编译后只有几 MB，启动快、内存占用低。
2. **量化推理**：把模型权重从 16 位浮点压缩到 4-8 位整数，大幅降低内存和带宽需求，让消费级硬件也能跑大模型。
3. **算子重写**：手写矩阵乘法、attention 等核心算子，针对各 CPU 架构（x86 AVX、ARM NEON）和 GPU（CUDA、Metal、Vulkan）做极致优化。
4. **统一抽象**：用 GGUF 格式统一封装不同模型架构，同一套推理代码支持几十种模型。

与 HuggingFace Transformers + PyTorch 方案相比，llama.cpp 的优势在于：内存占用低 3-5 倍、启动快、可在无 GPU 设备上运行、部署简单（单二进制文件）。代价是灵活性略低，不支持训练，仅做推理。

### 例子

**最简单的运行示例：**

```bash
# 下载一个量化模型（约 4GB）
wget https://huggingface.co/TheBloke/Llama-2-7B-Chat-GGUF/resolve/main/llama-2-7b-chat.Q4_K_M.gguf

# 用 llama-cli 跑推理
./llama-cli -m llama-2-7b-chat.Q4_K_M.gguf -p "你好，请介绍一下自己" -n 256
```

**输出示例：**

```
你好，请介绍一下自己

我是一个AI语言模型，由Meta训练...
```

**项目结构（源码）：**

```
llama.cpp/
├── llama/           # 核心库源码
├── ggml/            # GGML 张量库（底层算子）
├── tools/
│   ├── main/        # llama-cli
│   ├── server/      # llama-server
│   ├── quantize/    # llama-quantize
│   └── ...
├── models/         # 模型转换脚本
├── CMakeLists.txt
└── Makefile
```

### 总结

- llama.cpp 是纯 C/C++ 实现的本地 LLM 推理引擎
- 核心优势：轻量、量化、跨平台、单文件部署
- 支持几乎所有主流开源模型（通过 GGUF 格式）
- **注意事项**：仅做推理，不支持训练；项目迭代快，CLI 命名可能变化（旧版 `main`、`server`，新版 `llama-cli`、`llama-server`）

---

## 第 2 讲: 核心概念 (GGUF、量化、KV Cache)

### 概念

理解 llama.cpp 需要掌握三个核心概念：**GGUF**（模型文件格式）、**量化 (Quantization)**（权重压缩）、**KV Cache**（推理加速缓存）。这三者共同决定了 llama.cpp 的性能特征和使用方式。

### 原理

**1. GGUF (GPT-Generated Unified Format)**

GGUF 是 llama.cpp 专用的模型文件格式（前身是 GGML，已废弃）。它把模型权重、tokenizer、超参数、元数据全部打包进一个 `.gguf` 文件，无需额外配置即可加载。GGUF 采用内存映射 (mmap) 加载，大模型可以按需读取，启动快、内存友好。

**2. 量化 (Quantization)**

原始模型权重通常是 FP16/BF16（每个权重 2 字节）。量化把它们压缩到 4-8 位整数：

- **Q4_0 / Q4_1**：4 位量化，最省内存，精度损失略大
- **Q4_K_M / Q5_K_M / Q6_K**：K-quant 系列，分组量化，精度与体积平衡好
- **Q8_0**：8 位量化，几乎无损，体积大
- **F16**：半精度浮点，无量化，体积最大

7B 模型从 FP16 的 14GB 压缩到 Q4 的 4GB，让 8GB 内存的笔记本也能跑。

**3. KV Cache (Key-Value Cache)**

Transformer 自回归生成时，每生成一个 token 都要对前面所有 token 做 attention。如果每次都重算所有历史 token 的 Key/Value 投影会非常慢。KV Cache 把每层的 K、V 缓存下来，下次生成时直接复用。

KV Cache 大小 ≈ `2 × n_layers × n_ctx × n_embd × sizeof(elem)`。比如 Llama-7B（32 层、4096 维、4K 上下文）的 KV Cache 在 FP16 下约 2GB。llama.cpp 支持 8 位量化 KV Cache，可减半。

### 例子

**查看 GGUF 元数据：**

```bash
# 用 llama-cli 查看
./llama-cli -m model.gguf --version

# 或用 gguf-py 工具
python3 -m pip install gguf
python3 -m gguf models/my_model.gguf
```

输出：
```
* gguf: context length     = 4096
* gguf: embedding length   = 4096
* gguf: block count        = 32
* gguf: feed forward length = 11008
* gguf: attention.head count = 32
* gguf: quantize           = q4_k
```

**KV Cache 量化选项：**

```bash
# 默认 FP16 KV Cache
./llama-cli -m model.gguf -p "Hello"

# 8 位量化 KV Cache（省内存，略损精度）
./llama-cli -m model.gguf -p "Hello" --cache-type-k q8_0 --cache-type-v q8_0

# 4 位量化 KV Cache（最省内存）
./llama-cli -m model.gguf -p "Hello" --cache-type-k q4_0 --cache-type-v q4_0
```

**内存占用估算：**

| 模型 | FP16 大小 | Q4_K_M 大小 | KV Cache (4K, FP16) | 总内存（Q4 + FP16 KV） |
|------|-----------|-------------|----------------------|------------------------|
| Llama-7B | 14 GB | 4.1 GB | 1.0 GB | ~5.1 GB |
| Llama-13B | 26 GB | 7.4 GB | 1.8 GB | ~9.2 GB |
| Llama-70B | 138 GB | 39.5 GB | 4.1 GB | ~43.6 GB |

### 总结

- GGUF 是 llama.cpp 的统一模型格式，含权重 + tokenizer + 元数据
- 量化把权重从 16 位压到 4-8 位，Q4_K_M 是精度/体积平衡的最佳选择
- KV Cache 缓存历史 token 的 K/V，避免重复计算，可量化以省内存
- **注意事项**：上下文越长 KV Cache 越大，4K 上下文 7B 模型约 1GB，32K 上下文约 8GB；内存紧张时用 `--cache-type-k q8_0`

---

## 第 3 讲: 环境准备与依赖

### 概念

llama.cpp 几乎无外部依赖，但为了发挥硬件性能，需要准备合适的编译工具链和可选的加速库。本讲讲解在不同操作系统上准备开发环境的方法。

### 原理

llama.cpp 的构建依赖：

**必需依赖：**
- C/C++ 编译器（GCC 9+ / Clang 11+ / MSVC 2019+）
- CMake 3.14+
- Git（克隆源码）

**可选依赖（按加速后端）：**
- **CUDA**：NVIDIA GPU 加速，需要 CUDA Toolkit 11.7+
- **Metal**：Apple Silicon 自带，无需安装
- **Vulkan**：跨厂商 GPU 加速，需要 Vulkan SDK
- **OpenCL / CLBlast**：老式 GPU 加速
- **BLAS**：CPU 矩阵运算加速（OpenBLAS、Intel MKL、Apple Accelerate）

**Python 依赖（仅模型转换需要）：**
- Python 3.10+
- `transformers`、`torch`、`gguf`（用于把 HF 模型转 GGUF）

### 例子

**Ubuntu / Debian：**

```bash
# 基础工具
sudo apt update
sudo apt install -y build-essential cmake git

# 可选：CPU 加速（OpenBLAS）
sudo apt install -y libopenblas-dev

# 可选：Vulkan
sudo apt install -y libvulkan-dev vulkan-tools

# 可选：CUDA（按官方文档安装 CUDA Toolkit）
# https://developer.nvidia.com/cuda-downloads
```

**macOS（Apple Silicon）：**

```bash
# Xcode Command Line Tools（含 Clang、Make）
xcode-select --install

# Homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# CMake
brew install cmake

# Metal 已内置，无需额外安装
```

**Windows：**

```powershell
# 方式1：Visual Studio 2022 + CMake
# 下载 VS2022（含 C++ 桌面开发工作负载）
# 下载 CMake: https://cmake.org/download/

# 方式2：MSYS2（推荐，类 Unix 环境）
# 安装 MSYS2 后在 UCRT64 终端：
pacman -S mingw-w64-ucrt-x86_64-gcc mingw-w64-ucrt-x86_64-cmake make git

# 可选：CUDA Toolkit
# https://developer.nvidia.com/cuda-downloads
```

**Python 环境（模型转换用）：**

```bash
# 用 conda 或 venv 隔离
python3 -m venv llama-env
source llama-env/bin/activate  # Windows: llama-env\Scripts\activate

# 安装转换工具
pip install torch transformers gguf accelerate sentencepiece
```

**验证环境：**

```bash
git --version
cmake --version
gcc --version  # 或 clang --version / cl

# GPU 检测
nvidia-smi       # NVIDIA
vulkaninfo       # Vulkan
system_profiler SPDisplaysDataType  # macOS Metal
```

### 总结

- 必需：编译器、CMake、Git
- 可选：CUDA、Vulkan、OpenBLAS 等加速库
- Python 环境仅模型转换需要
- **注意事项**：Windows 推荐用 MSYS2 或 WSL2；macOS 用 Homebrew；Linux 用系统包管理器；GPU 加速库要和驱动版本匹配

---

## 第 4 讲: 从源码编译

### 概念

llama.cpp 提供两种构建方式：**Makefile**（简单快速）和 **CMake**（功能完整、跨平台）。本讲讲解如何用 CMake 编译出 CPU 版本和各 GPU 加速版本。

### 原理

llama.cpp 用 CMake 作为主构建系统，提供大量编译选项控制加速后端、优化级别、特性开关。关键 CMake 选项：

- `GGML_CUDA=ON`：启用 NVIDIA CUDA
- `GGML_METAL=ON`：启用 Apple Metal（macOS 默认开启）
- `GGML_VULKAN=ON`：启用 Vulkan
- `GGML_OPENBLAS=ON`：启用 OpenBLAS
- `GGML_NATIVE=ON`：启用本机指令集（AVX/NEON）
- `LLAMA_BUILD_SERVER=ON`：构建 llama-server

编译产物在 `build/bin/` 目录下，包含 `llama-cli`、`llama-server`、`llama-quantize`、`llama-bench` 等工具。

### 例子

**CPU 版本（所有平台通用）：**

```bash
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp

cmake -B build -DGGML_NATIVE=ON -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release -j

# 产物在 build/bin/
ls build/bin/
# llama-cli  llama-server  llama-quantize  llama-bench  ...
```

**CUDA 版本（NVIDIA GPU）：**

```bash
cmake -B build -DGGML_CUDA=ON -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release -j

# 运行时指定 GPU 层数（多少层放 GPU）
./build/bin/llama-cli -m model.gguf -p "Hello" -ngl 99
```

**Metal 版本（Apple Silicon，默认开启）：**

```bash
cmake -B build -DGGML_METAL=ON -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release -j

# 运行时指定 GPU 层数
./build/bin/llama-cli -m model.gguf -p "Hello" -ngl 99
```

**Vulkan 版本（跨厂商 GPU）：**

```bash
cmake -B build -DGGML_VULKAN=ON -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release -j
```

**Windows + Visual Studio：**

```powershell
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp

cmake -B build -G "Visual Studio 17 2022" -A x64 -DGGML_CUDA=ON
cmake --build build --config Release
```

**Docker 构建（隔离环境）：**

```bash
# 项目自带 Dockerfile
docker build -t llama-cpp -f .devops/main.Dockerfile .

# 运行
docker run --rm -v /path/to/models:/models llama-cpp \
    -m /models/llama-7b.gguf -p "Hello"
```

**验证编译：**

```bash
# 查看版本和支持的后端
./build/bin/llama-cli --version
./build/bin/llama-bench --help

# 跑一个简单测试
./build/bin/llama-cli -m model.gguf -p "Hello" -n 32 -ngl 0
```

### 总结

- CMake 是主构建系统，选项控制加速后端
- `GGML_<BACKEND>=ON` 启用对应后端
- 产物在 `build/bin/`，含 cli、server、quantize、bench 等工具
- **注意事项**：CUDA 编译需要 nvcc，确保 CUDA Toolkit 已安装且版本匹配；macOS Metal 默认开启无需特殊处理；编译时间 5-30 分钟，看 CPU 性能

---

# 第 2 章: 模型与量化

## 第 5 讲: GGUF 模型格式

### 概念

**GGUF (GPT-Generated Unified Format)** 是 llama.cpp 专用的模型文件格式，由 GGML 格式演进而来。它把模型权重、tokenizer、超参数、对话模板等全部信息打包进单个 `.gguf` 文件，是 llama.cpp 加载模型的唯一格式。

### 原理

GGUF 的设计目标：

1. **单文件自包含**：权重 + tokenizer + 配置全在一个文件，便于分发和部署
2. **内存映射 (mmap) 友好**：文件结构按内存布局设计，加载时直接 mmap，无需解析复制
3. **可扩展**：用 key-value 元数据存储任意字段，新模型架构只需加新字段
4. **大端字节序**：跨平台一致，避免字节序问题

GGUF 文件结构：

```
[Header]
  - magic: "GGUF"
  - version: 3
  - tensor_count: N
  - metadata_kv_count: M

[Metadata KV]
  - general.architecture = "llama"
  - general.name = "Llama-2-7B"
  - llama.context_length = 4096
  - llama.embedding_length = 4096
  - llama.block_count = 32
  - tokenizer.ggml.model = "llama"
  - tokenizer.ggml.tokens = [...]
  - ...

[Tensor Info]
  - name: "token_embd.weight"
  - n_dims: 2
  - dims: [4096, 32000]
  - type: "Q4_K"

[Tensor Data]
  - raw bytes for each tensor
```

每个张量记录名称、维度、数据类型，然后是连续的字节数据。不同张量可以用不同量化类型（混合精度）。

### 例子

**查看 GGUF 内容：**

```bash
# 安装 gguf Python 包
pip install gguf

# 查看元数据
python3 -m gguf models/llama-2-7b-chat.Q4_K_M.gguf
```

输出示例：
```
* gguf: context length     = 4096
* gguf: embedding length   = 4096
* gguf: block count        = 32
* gguf: feed forward length = 11008
* gguf: attention.head count = 32
* gguf: attention.head count_kv = 32
* gguf: rope.dimension_count = 128
* gguf: rope.freq_base = 10000.0
* gguf: tokenizer.ggml.model = llama
* gguf: tokenizer.ggml.tokens = 32000
* gguf: quantize = q4_k
```

**用 Python 读取 GGUF：**

```python
from gguf import GGUFReader

reader = GGUFReader("model.gguf")

# 读取元数据
for key, field in reader.fields.items():
    print(f"{key}: {field.parts}")

# 读取张量
for tensor in reader.tensors:
    print(f"{tensor.name}: shape={tensor.shape}, dtype={tensor.tensor_type}")
```

**mmap 加载验证：**

```bash
# llama.cpp 默认用 mmap，启动快
./llama-cli -m model.gguf -p "Hello" --mlock

# --mlock 锁定内存，避免被 swap
# --no-mmap 禁用 mmap，全部读入内存（启动慢，但跨进程共享更稳定）
./llama-cli -m model.gguf -p "Hello" --no-mmap
```

### 总结

- GGUF 是 llama.cpp 的统一模型格式，单文件自包含
- 结构：Header + Metadata KV + Tensor Info + Tensor Data
- 默认 mmap 加载，启动快、内存友好
- **注意事项**：旧版 GGML 格式已废弃，需用 `convert.py` 转换为 GGUF；GGUF v3 是当前主流版本

---

## 第 6 讲: 量化原理与类型

### 概念

**量化 (Quantization)** 是把模型权重从高精度浮点（FP16/BF16）压缩到低精度整数（4-8 位）的技术。llama.cpp 提供多种量化方案，在精度损失和文件体积之间权衡。理解各量化类型的特点是选择模型的关键。

### 原理

**量化的基本原理：**

把连续的浮点权重映射到有限的整数级别。比如 4 位量化把权重映射到 16 个级别（-7 到 +7）。每个权重从 16 位（FP16）压缩到 4 位，体积减少 75%。

**分组量化 (Block-wise Quantization)：**

不是对整个张量用同一组缩放因子，而是分成小块（如 32 或 256 个权重一组），每块有自己的缩放因子 (scale) 和零点 (zero point)。这样能更好地适应不同区域的权重分布，精度损失更小。

**llama.cpp 的量化类型：**

| 类型 | 位宽 | 块大小 | 特点 |
|------|------|--------|------|
| F16 | 16 | - | 半精度浮点，无量化，基准 |
| Q8_0 | 8 | 32 | 8 位量化，几乎无损 |
| Q6_K | 6 | 256 | 混合精度，质量高 |
| Q5_K_M | 5 | 256 | 平衡型，常用 |
| Q4_K_M | 4 | 256 | 性价比之王，最常用 |
| Q4_0 | 4 | 32 | 老式 4 位，简单但精度差 |
| Q3_K_M | 3 | 256 | 极致压缩，精度损失明显 |
| Q2_K | 2 | 256 | 最小体积，质量较差 |

**K-quant 系列（推荐）：**

Q4_K、Q5_K、Q6_K 是「K-quant」系列，采用混合精度策略：关键层（如 attention 的 wv、wo）用更高精度，其他层用低精度。在同等体积下质量更好。

### 例子

**量化类型对比（Llama-7B）：**

| 类型 | 文件大小 | 困惑度 (PPL) | 推理速度 | 推荐场景 |
|------|----------|--------------|----------|----------|
| F16 | 13.5 GB | 5.91 | 慢 | 基准对比 |
| Q8_0 | 6.8 GB | 5.95 | 快 | 几乎无损 |
| Q6_K | 5.5 GB | 5.97 | 快 | 高质量 |
| Q5_K_M | 4.8 GB | 5.99 | 很快 | 平衡 |
| Q4_K_M | 4.1 GB | 6.02 | 很快 | **推荐** |
| Q4_0 | 3.8 GB | 6.21 | 最快 | 老式兼容 |
| Q3_K_M | 3.5 GB | 6.45 | 很快 | 内存紧张 |
| Q2_K | 2.7 GB | 7.34 | 快 | 极限压缩 |

**选择建议：**

```bash
# 一般场景：Q4_K_M（4-5GB，质量接近原版）
wget model.Q4_K_M.gguf

# 高质量场景：Q5_K_M 或 Q6_K（5-6GB）
wget model.Q5_K_M.gguf

# 内存紧张（如手机）：Q3_K_M 或 Q2_K
wget model.Q3_K_M.gguf

# 几乎无损：Q8_0（7GB）
wget model.Q8_0.gguf
```

**查看模型量化类型：**

```bash
# 从文件名识别
ls models/
# llama-2-7b-chat.Q4_K_M.gguf  → Q4_K_M
# mistral-7b.Q5_K_M.gguf       → Q5_K_M

# 用 gguf 工具确认
python3 -m gguf models/model.gguf | grep quantize
```

### 总结

- 量化把权重从 16 位压到 4-8 位，体积减少 70-85%
- Q4_K_M 是性价比之王，质量接近原版，体积仅 1/3
- K-quant 系列用混合精度，同等体积下质量更好
- **注意事项**：量化越低，困惑度越高（质量越差）；Q4 是甜点，Q3 以下质量明显下降；小模型（<3B）建议用 Q5 或 Q8，避免量化损失过大

---

## 第 7 讲: 模型转换与量化操作

### 概念

要从 HuggingFace 下载的原始模型（PyTorch 格式）得到 GGUF 文件，需要两步：**转换**（把 HF 格式转 GGUF）和**量化**（把 GGUF 的 FP16 权重压到 Q4/Q8）。llama.cpp 提供两个脚本/工具完成这些操作。

### 原理

**转换流程：**

1. 从 HuggingFace 下载原始模型（`.bin` 或 `.safetensors` 格式，含 FP16 权重）
2. 用 `convert_hf_to_gguf.py` 转换为 GGUF 格式（FP16，未量化）
3. 用 `llama-quantize` 工具把 FP16 GGUF 压缩为 Q4/Q8 等量化版本

**转换脚本的工作：**

- 读取 HF 模型的 `config.json`、`pytorch_model.bin` / `model.safetensors`
- 提取权重张量、tokenizer、超参数
- 按 GGUF 格式重新组织，写入 `.gguf` 文件
- 自动识别模型架构（Llama、Mistral、Qwen、Phi 等）

**量化工具的工作：**

- 读取 FP16 GGUF
- 对每个张量按指定类型（Q4_K_M 等）做分组量化
- 写入新的 GGUF 文件，元数据不变，张量数据替换为量化后的字节

### 例子

**步骤 1：下载 HF 模型**

```bash
# 用 huggingface-cli 下载（推荐）
pip install huggingface-hub
huggingface-cli download meta-llama/Llama-2-7b-chat-hf --local-dir Llama-2-7b-chat

# 或用 git lfs
git lfs install
git clone https://huggingface.co/meta-llama/Llama-2-7b-chat-hf
```

**步骤 2：转换为 GGUF（FP16）**

```bash
cd llama.cpp

# 安装 Python 依赖
pip install -r requirements/requirements-convert_hf_to_gguf.txt

# 转换
python3 convert_hf_to_gguf.py ../Llama-2-7b-chat --outfile ../llama-2-7b-chat-fp16.gguf

# 输出：llama-2-7b-chat-fp16.gguf（约 13GB）
```

**步骤 3：量化**

```bash
# 量化为 Q4_K_M（推荐）
./build/bin/llama-quantize ../llama-2-7b-chat-fp16.gguf ../llama-2-7b-chat-Q4_K_M.gguf Q4_K_M

# 量化为 Q8_0（几乎无损）
./build/bin/llama-quantize ../llama-2-7b-chat-fp16.gguf ../llama-2-7b-chat-Q8_0.gguf Q8_0

# 量化为 Q5_K_M
./build/bin/llama-quantize ../llama-2-7b-chat-fp16.gguf ../llama-2-7b-chat-Q5_K_M.gguf Q5_K_M
```

**一步到位（转换 + 量化）：**

```bash
# 转换时直接指定量化类型（部分模型支持）
python3 convert_hf_to_gguf.py ../Llama-2-7b-chat --outtype q4_k_m --outfile llama-2-7b-chat-q4.gguf
```

**批量量化脚本：**

```bash
#!/bin/bash
# quantize_all.sh
INPUT=$1
for TYPE in Q4_K_M Q5_K_M Q8_0; do
    OUTPUT="${INPUT%.gguf}-${TYPE}.gguf"
    echo "Quantizing to $TYPE..."
    ./build/bin/llama-quantize "$INPUT" "$OUTPUT" "$TYPE"
done

# 用法
./quantize_all.sh model-fp16.gguf
```

**验证量化结果：**

```bash
# 查看量化类型
python3 -m gguf model-Q4_K_M.gguf | grep quantize

# 跑推理对比
./build/bin/llama-cli -m model-Q4_K_M.gguf -p "Hello" -n 32
```

### 总结

- 转换：`convert_hf_to_gguf.py` 把 HF 模型转 GGUF（FP16）
- 量化：`llama-quantize` 把 FP16 GGUF 压为 Q4/Q8
- 支持几乎所有主流模型架构
- **注意事项**：转换需要 Python + torch + transformers，约 5GB 磁盘空间；量化过程不损失架构信息，仅压缩权重；老式 `convert.py` 已被 `convert_hf_to_gguf.py` 取代

---

## 第 8 讲: 模型选择与下载

### 概念

llama.cpp 支持几十种开源模型架构，但并非所有模型都适合所有场景。本讲讲解如何根据任务、硬件、语言需求选择合适的模型，以及从哪里安全下载。

### 原理

**模型选择维度：**

1. **参数规模**：1B（轻量）、7B（平衡）、13B（高质量）、70B（顶级，需大显存）
2. **任务类型**：基础模型（Base，续写）、对话模型（Chat/Instruct，问答）、代码模型（Coder）、多模态（Vision）
3. **语言支持**：英文为主（Llama、Mistral）、中文优化（Qwen、ChatGLM、Yi）、多语言
4. **许可证**：MIT/Apache（商用友好）、Llama License（限制商用）、研究专用

**主流模型对比：**

| 模型 | 参数 | 中文 | 许可证 | 特点 |
|------|------|------|--------|------|
| Llama-3 | 8B/70B | 一般 | Llama License | Meta 出品，生态成熟 |
| Qwen2.5 | 0.5B-72B | 优秀 | Apache 2.0 | 阿里，中文最佳 |
| Mistral | 7B/13B | 一般 | Apache 2.0 | 欧洲，效率高 |
| DeepSeek | 7B/67B | 优秀 | MIT | 国产，推理强 |
| Phi-3 | 3.8B/14B | 一般 | MIT | 微软，小而精 |
| Gemma-2 | 9B/27B | 一般 | Gemma License | Google，质量高 |

**下载渠道：**

1. **HuggingFace**：最大模型库，需账号（部分模型需申请）
2. **ModelScope**：阿里出品，国内访问快，中文模型多
3. **Ollama Hub**：Ollama 维护的模型库，已量化好
4. **社区镜像**：TheBloke、bartowski 等用户上传的量化版

### 例子

**从 HuggingFace 下载量化版（推荐）：**

```bash
# 安装工具
pip install huggingface-hub

# 下载 Qwen2.5-7B-Instruct Q4 量化版
huggingface-cli download Qwen/Qwen2.5-7B-Instruct-GGUF \
    qwen2.5-7b-instruct-q4_k_m.gguf \
    --local-dir ./models

# 下载 Llama-3-8B-Instruct Q4 量化版
huggingface-cli download lmstudio-community/Meta-Llama-3-8B-Instruct-GGUF \
    Meta-Llama-3-8B-Instruct-Q4_K_M.gguf \
    --local-dir ./models
```

**从 ModelScope 下载（国内推荐）：**

```bash
pip install modelscope

# 下载 Qwen2.5-7B-Instruct GGUF
modelscope download --model qwen/Qwen2.5-7B-Instruct-GGUF \
    qwen2.5-7b-instruct-q4_k_m.gguf \
    --local_dir ./models
```

**用 Ollama 下载（最简单）：**

```bash
# 安装 Ollama
curl -fsSL https://ollama.com/install.sh | sh

# 下载并运行（自动选量化版）
ollama run qwen2.5:7b
ollama run llama3:8b
ollama run mistral:7b
```

**选择决策树：**

```
任务需求？
├─ 中文为主 → Qwen2.5 系列
│   ├─ 8GB 内存 → Qwen2.5-7B-Instruct Q4
│   └─ 16GB 内存 → Qwen2.5-14B-Instruct Q4
├─ 英文为主 → Llama-3 / Mistral
│   ├─ 8GB 内存 → Llama-3-8B-Instruct Q4
│   └─ 16GB 内存 → Llama-3-8B + 32K 上下文
├─ 代码生成 → DeepSeek-Coder / Qwen2.5-Coder
├─ 边缘设备 → Phi-3-mini (3.8B)
└─ 顶级质量 → Llama-3-70B / Qwen2.5-72B（需 40GB+ 内存）
```

**验证模型完整性：**

```bash
# 检查文件大小（与 HF 页面标注对比）
ls -lh models/*.gguf

# 跑一个简单推理验证
./llama-cli -m models/qwen2.5-7b.gguf -p "你好" -n 32 --no-warmup
```

### 总结

- 中文场景优先 Qwen2.5，英文场景 Llama-3/Mistral
- 8GB 内存选 7B Q4，16GB 选 14B Q4 或 7B Q8
- 下载优先 HuggingFace，国内用 ModelScope 或 Ollama
- **注意事项**：注意模型许可证（Llama 系列商用受限）；下载后核对文件大小和 SHA256；部分模型需要 HF 账号和申请

---

# 第 3 章: 命令行推理

## 第 9 讲: llama-cli 基础用法

### 概念

`llama-cli`（旧名 `main`）是 llama.cpp 的核心命令行工具，用于加载 GGUF 模型并执行文本生成。它支持单次生成、交互式对话、流式输出等多种模式，是本地运行 LLM 的最直接方式。

### 原理

`llama-cli` 的工作流程：

1. **加载模型**：mmap 读取 GGUF 文件，初始化模型权重到内存
2. **初始化上下文**：分配 KV Cache 空间（按 `--ctx-size`）
3. **加载 prompt**：用 tokenizer 把输入文本编码为 token 序列
4. **预填充 (Prefill)**：前向计算 prompt 的所有 token，填充 KV Cache
5. **解码 (Decode)**：自回归生成新 token，每次一个，直到达到 `-n` 或遇到 EOS
6. **输出**：把生成的 token 解码为文本，流式或批量输出

关键参数分组：

- **模型参数**：`-m`（模型路径）、`--ctx-size`（上下文长度）、`-ngl`（GPU 层数）
- **生成参数**：`-p`（prompt）、`-n`（生成长度）、`--temp`（温度）、`--top-k`、`--top-p`
- **交互参数**：`-i`（交互模式）、`-ins`（指令模式）、`--color`（彩色输出）

### 例子

**最简单的单次生成：**

```bash
./llama-cli -m model.gguf -p "Once upon a time" -n 128
```

**完整参数示例：**

```bash
./llama-cli \
    -m models/qwen2.5-7b.gguf \
    -p "请用三句话介绍量子计算" \
    -n 256 \
    --temp 0.7 \
    --top-k 40 \
    --top-p 0.9 \
    --repeat-penalty 1.1 \
    --ctx-size 4096 \
    -ngl 99 \
    --color
```

**交互式对话模式：**

```bash
./llama-cli -m model.gguf -i -ins -n 256 --color

# 进入交互界面
# > 你好
# 你好！我是AI助手...
# > 请介绍一下自己
# ...
```

**从文件读取 prompt：**

```bash
# prompt 文件
cat > prompt.txt <<EOF
请总结以下文章：
...（长文本）...
EOF

./llama-cli -m model.gguf -f prompt.txt -n 512
```

**多轮对话（带系统提示）：**

```bash
./llama-cli -m model.gguf \
    -i -ins \
    -p "你是一个有帮助的中文助手" \
    -n 256 \
    --color \
    --ctx-size 4096
```

**常用参数速查：**

| 参数 | 含义 | 示例 |
|------|------|------|
| `-m` | 模型路径 | `-m model.gguf` |
| `-p` | 输入 prompt | `-p "Hello"` |
| `-n` | 生成 token 数 | `-n 256` |
| `-ngl` | GPU 层数 | `-ngl 99`（全部） |
| `--ctx-size` | 上下文长度 | `--ctx-size 4096` |
| `--temp` | 温度 | `--temp 0.7` |
| `--top-k` | Top-K 采样 | `--top-k 40` |
| `--top-p` | Top-P 采样 | `--top-p 0.9` |
| `--repeat-penalty` | 重复惩罚 | `--repeat-penalty 1.1` |
| `-i` | 交互模式 | `-i` |
| `-ins` | 指令模式 | `-ins` |
| `--color` | 彩色输出 | `--color` |
| `-f` | 从文件读 prompt | `-f prompt.txt` |

### 总结

- `llama-cli` 是核心推理工具，支持单次和交互模式
- 关键参数：`-m`（模型）、`-p`（prompt）、`-n`（长度）、`-ngl`（GPU 层）
- 交互模式用 `-i -ins`，对话模型必备
- **注意事项**：`-ngl 0` 表示纯 CPU；`-ngl 99` 表示全部放 GPU；首次加载慢（mmap + 预热），后续快

---

## 第 10 讲: 生成参数详解

### 概念

生成参数控制模型如何从概率分布中选择下一个 token，直接影响输出质量、多样性、可预测性。本讲详解温度、Top-K、Top-P、重复惩罚等核心参数的原理和调优。

### 原理

**1. 温度 (Temperature)**

模型在每个位置输出一个 logits 向量（vocab_size 维），经 softmax 转为概率分布。温度 T 调整分布的尖锐程度：

- `T < 1`：分布更尖锐，模型更确定，输出更可预测（T=0.1 几乎贪心）
- `T = 1`：原始分布
- `T > 1`：分布更平坦，输出更随机、更有创意

公式：`p_i = exp(logit_i / T) / sum(exp(logit_j / T))`

**2. Top-K 采样**

只从概率最高的 K 个 token 中采样，其他全部置零。K=1 等价于贪心解码。K=40 是常用值。

**3. Top-P (Nucleus) 采样**

按概率从高到低累加，直到累计概率 ≥ P，只从这些 token 中采样。P=0.9 表示保留前 90% 概率质量的 token。比 Top-K 更自适应。

**4. 重复惩罚 (Repeat Penalty)**

对已生成过的 token 降低概率，避免重复。惩罚因子 > 1（如 1.1）表示降低，< 1 表示增强。

**5. 其他参数**

- `--min-p`：最小概率阈值，过滤低概率 token
- `--tfs`：Tail-Free Sampling，尾部截断
- `--typical-p`：典型性采样
- `--mirostat`：自适应温度算法（Mirostat）

### 例子

**不同温度对比：**

```bash
# T=0.1（确定性，几乎贪心）
./llama-cli -m model.gguf -p "The capital of France is" -n 32 --temp 0.1
# 输出：Paris. Paris is the capital...

# T=0.7（平衡，常用）
./llama-cli -m model.gguf -p "The capital of France is" -n 32 --temp 0.7
# 输出：Paris, which is known for...

# T=1.5（高随机性，创意）
./llama-cli -m model.gguf -p "The capital of France is" -n 32 --temp 1.5
# 输出：Paris, or perhaps Lyon...
```

**Top-K 与 Top-P 组合：**

```bash
# 标准 Chat 配置
./llama-cli -m model.gguf -p "Hello" -n 256 \
    --temp 0.7 \
    --top-k 40 \
    --top-p 0.9 \
    --repeat-penalty 1.1

# 创意写作（高温度 + 高 Top-P）
./llama-cli -m model.gguf -p "Write a poem" -n 512 \
    --temp 1.0 \
    --top-p 0.95 \
    --repeat-penalty 1.2

# 代码生成（低温度 + 低 Top-K，确定性高）
./llama-cli -m model.gguf -p "def fibonacci(n):" -n 256 \
    --temp 0.2 \
    --top-k 10 \
    --repeat-penalty 1.0
```

**Mirostat 自适应采样：**

```bash
# Mirostat v2，自动调节温度维持目标困惑度
./llama-cli -m model.gguf -p "Hello" -n 256 \
    --mirostat 2 \
    --mirostat-lr 0.1 \
    --mirostat-ent 5.0
```

**重复惩罚调优：**

```bash
# 惩罚太低（1.0）：可能重复
./llama-cli -m model.gguf -p "Tell me about" -n 256 --repeat-penalty 1.0

# 惩罚适中（1.1）：常用值
./llama-cli -m model.gguf -p "Tell me about" -n 256 --repeat-penalty 1.1

# 惩罚过高（1.5）：可能输出不连贯
./llama-cli -m model.gguf -p "Tell me about" -n 256 --repeat-penalty 1.5
```

**参数推荐配置：**

| 场景 | temp | top_k | top_p | repeat_penalty |
|------|------|-------|-------|----------------|
| 代码生成 | 0.2 | 10 | 0.9 | 1.0 |
| 对话问答 | 0.7 | 40 | 0.9 | 1.1 |
| 创意写作 | 1.0 | 100 | 0.95 | 1.2 |
| 数学推理 | 0.0 | 1 | - | 1.0 |
| 翻译 | 0.3 | 40 | 0.9 | 1.1 |

### 总结

- 温度控制随机性，T=0 几乎贪心，T>1 更有创意
- Top-K 限制候选数，Top-P 限制累计概率
- 重复惩罚避免输出循环
- **注意事项**：参数互相影响，需组合调优；不同模型对参数敏感度不同；Mirostat 是自适应方案，可省去手动调温度

---

## 第 11 讲: 上下文与 KV Cache 管理

### 概念

上下文长度 (Context Size) 决定模型能处理的最大 token 数，KV Cache 是实现高效推理的关键数据结构。本讲讲解如何合理设置上下文、管理 KV Cache、处理长文本场景。

### 原理

**上下文长度 (n_ctx)**

模型训练时有最大上下文长度（如 Llama-2 是 4096，Llama-3 是 8192）。超过训练长度时，模型质量会下降（外推问题）。llama.cpp 通过 RoPE 插值（`--rope-scaling`）扩展上下文，但质量会略降。

**KV Cache 大小**

KV Cache 大小 = `2 × n_layers × n_ctx × n_embd × sizeof(elem)`

- Llama-7B：32 层 × 4096 维
- n_ctx=4096，FP16：2 × 32 × 4096 × 4096 × 2 = 2GB
- n_ctx=32768，FP16：16GB（仅 KV Cache！）

**KV Cache 量化**

可把 KV Cache 量化为 Q8_0 或 Q4_0，减半或减至 1/4：

- `--cache-type-k q8_0 --cache-type-v q8_0`：8 位，质量损失小
- `--cache-type-k q4_0 --cache-type-v q4_0`：4 位，省内存但质量下降

**上下文窗口滑动**

当对话超过 n_ctx 时，llama.cpp 会丢弃最旧的 token（滑动窗口）。可用 `--keep` 保留系统提示等关键 token。

### 例子

**设置上下文长度：**

```bash
# 默认（模型训练长度，如 4096）
./llama-cli -m model.gguf -p "Hello"

# 扩展到 8K
./llama-cli -m model.gguf -p "Hello" --ctx-size 8192

# 扩展到 32K（需 RoPE 插值）
./llama-cli -m model.gguf -p "Hello" --ctx-size 32768 --rope-scaling linear --rope-freq-scale 0.25
```

**KV Cache 量化：**

```bash
# 默认 FP16
./llama-cli -m model.gguf -p "Hello" --ctx-size 8192

# Q8 量化（省一半 KV Cache 内存）
./llama-cli -m model.gguf -p "Hello" --ctx-size 8192 \
    --cache-type-k q8_0 --cache-type-v q8_0

# Q4 量化（省 3/4）
./llama-cli -m model.gguf -p "Hello" --ctx-size 8192 \
    --cache-type-k q4_0 --cache-type-v q4_0
```

**长文本处理（超长文档总结）：**

```bash
# 32K 上下文 + Q8 KV Cache
./llama-cli -m model.gguf \
    -f long_document.txt \
    -p "请总结以上文档" \
    -n 1024 \
    --ctx-size 32768 \
    --cache-type-k q8_0 --cache-type-v q8_0 \
    --rope-scaling linear --rope-freq-scale 0.25
```

**对话中保留系统提示：**

```bash
# --keep 保留前 N 个 token 不被丢弃
./llama-cli -m model.gguf -i -ins \
    -p "You are a helpful assistant" \
    --ctx-size 4096 \
    --keep -1  # 保留所有系统提示
```

**内存占用估算：**

| 模型 | n_ctx | FP16 KV | Q8 KV | Q4 KV |
|------|-------|---------|-------|-------|
| 7B | 4096 | 1.0 GB | 0.5 GB | 0.25 GB |
| 7B | 8192 | 2.0 GB | 1.0 GB | 0.5 GB |
| 7B | 32768 | 8.0 GB | 4.0 GB | 2.0 GB |
| 70B | 4096 | 4.1 GB | 2.0 GB | 1.0 GB |

### 总结

- 上下文越长 KV Cache 越大，内存是主要瓶颈
- Q8 量化 KV Cache 几乎无损，推荐默认开启
- 超过训练长度需 RoPE 插值，质量略降
- **注意事项**：长上下文不仅占内存，prefill 也慢（O(n²)）；32K 上下文 prefill 可能要几十秒；优先用 Q8 KV + 适度上下文

---

## 第 12 讲: 交互式对话与角色扮演

### 概念

`llama-cli` 的交互模式支持多轮对话、角色扮演、系统提示等高级功能。本讲讲解如何配置对话模板、设置角色、实现高质量的交互式体验。

### 原理

**对话模板 (Chat Template)**

不同模型有不同的对话格式（如 Llama 用 `[INST]...[/INST]`，Qwen 用 `<|im_start|>...<|im_end|>`）。GGUF 文件内嵌了对话模板，llama.cpp 自动应用，无需手动拼接。

**指令模式 (-ins)**

启用指令模式后，llama-cli 把每行输入当作用户指令，自动套用对话模板，生成回复后等待下一轮输入。多轮对话的 KV Cache 自动累积。

**系统提示 (System Prompt)**

通过 `-p` 或 `-sys` 设置系统提示，定义 AI 的角色、风格、约束。系统提示通常放在对话开头，全程保留。

**角色扮演**

通过精心设计的系统提示，让模型扮演特定角色（如客服、老师、小说人物）。配合高温度和重复惩罚，输出更有个性。

### 例子

**基础交互对话：**

```bash
./llama-cli -m qwen2.5-7b.gguf -i -ins -n 256 --color --ctx-size 4096

# 交互界面
> 你好
你好！我是Qwen，由阿里训练的AI助手...
> 请讲个笑话
为什么程序员喜欢黑色？因为黑色不反光...
```

**带系统提示的对话：**

```bash
./llama-cli -m qwen2.5-7b.gguf \
    -i -ins \
    -p "你是一个幽默的中文助手，回答时总是带一点俏皮话" \
    -n 256 \
    --color \
    --ctx-size 4096 \
    --temp 0.8 \
    --repeat-penalty 1.1
```

**角色扮演（小说人物）：**

```bash
./llama-cli -m llama-3-8b.gguf \
    -i -ins \
    -p "You are Sherlock Holmes, the famous detective. Stay in character and respond as Holmes would." \
    -n 512 \
    --color \
    --temp 0.9 \
    --repeat-penalty 1.15 \
    --ctx-size 8192
```

**多行输入（用文件）：**

```bash
# 把长 prompt 写入文件
cat > system.txt <<EOF
你是一个专业的翻译助手，专门把中文翻译成英文。
要求：
1. 保持原文意思
2. 用地道的英文表达
3. 必要时给出多个译法
EOF

./llama-cli -m qwen2.5-7b.gguf \
    -i -ins \
    -f system.txt \
    -n 256 \
    --color
```

**对话历史保存与恢复：**

```bash
# 保存对话日志
./llama-cli -m model.gguf -i -ins -n 256 --log-file chat.log

# 从历史继续（把日志作为 prompt）
./llama-cli -m model.gguf -i -ins -f chat.log -n 256
```

**反斜杠命令（交互模式）：**

```
> /help        # 显示帮助
> /save file   # 保存对话
> /load file   # 加载对话
> /clear       # 清空上下文
> /quit        # 退出
```

### 总结

- `-i -ins` 启用交互指令模式
- `-p` 设置系统提示，定义角色
- GGUF 内嵌对话模板，自动套用
- **注意事项**：不同模型对话格式不同，混用会出错；长对话会触发上下文滑动，可用 `--keep` 保留系统提示；高温度 + 重复惩罚让角色扮演更生动

---

# 第 4 章: 硬件加速

## 第 13 讲: CPU 优化与线程

### 概念

即使没有 GPU，llama.cpp 在 CPU 上也能高效运行。本讲讲解 CPU 编译选项、线程配置、指令集优化，让纯 CPU 推理达到最佳性能。

### 原理

**CPU 优化维度：**

1. **指令集**：AVX2、AVX-512、NEON、SVE 等 SIMD 指令加速矩阵运算
2. **多线程**：用 OpenMP 或 std::thread 并行计算
3. **NUMA 感知**：多 socket 服务器避免跨 NUMA 内存访问
4. **BLAS**：用 OpenBLAS、Intel MKL、Apple Accelerate 加速矩阵乘法

**线程数选择：**

- 物理核心数（不用超线程）：通常最佳
- 大模型 + 多核：可适当增加
- 小模型 + 多线程：可能因同步开销变慢

**关键参数：**

- `-t` / `--threads`：CPU 线程数
- `--threads-batch`：prefill 阶段线程数（可不同于 decode）
- `-b` / `--batch-size`：prefill 批大小
- `-ub` / `--ubatch-size`：物理批大小

### 例子

**编译时启用本机指令集：**

```bash
# 自动检测本机指令集
cmake -B build -DGGML_NATIVE=ON -DCMAKE_BUILD_TYPE=Release

# 或手动指定（如 AVX2）
cmake -B build -DGGML_AVX2=ON -DGGML_FMA=ON -DGGML_F16C=ON

# Intel CPU 用 MKL
cmake -B build -DGGML_BLAS=ON -DGGML_BLAS_VENDOR=Intel

# Apple Silicon 用 Accelerate（默认）
cmake -B build -DGGML_ACCELERATE=ON
```

**运行时线程调优：**

```bash
# 默认（自动检测）
./llama-cli -m model.gguf -p "Hello"

# 指定线程数（物理核心数）
./llama-cli -m model.gguf -p "Hello" -t 8

# prefill 用更多线程（CPU 空闲时）
./llama-cli -m model.gguf -p "Hello" -t 8 --threads-batch 16

# 大批大小加速 prefill
./llama-cli -m model.gguf -p "Hello" -b 512 -ub 512
```

**性能基准测试：**

```bash
# 用 llama-bench 测不同配置
./llama-bench -m model.gguf -t 4 -t 8 -t 16

# 输出示例
# | config | t/s (prefill) | t/s (decode) |
# |--------|---------------|-------------|
# | t=4    | 150           | 35          |
# | t=8    | 280           | 55          |
# | t=16   | 320           | 50          |
```

**NUMA 优化（多 socket 服务器）：**

```bash
# 启用 NUMA 感知
./llama-cli -m model.gguf -p "Hello" --numa

# 绑定到特定 NUMA 节点
numactl --cpunodebind=0 --membind=0 ./llama-cli -m model.gguf -p "Hello" -t 32
```

**线程数选择经验：**

| CPU | 推荐线程数 | 备注 |
|-----|-----------|------|
| 4 核 8 线程 | 4 | 用物理核 |
| 8 核 16 线程 | 8 | 用物理核 |
| 16 核 32 线程 | 12-16 | 留余量 |
| 32 核 64 线程 | 24-32 | 留余量 |
| 双路 Xeon (56 核) | 28-40 | 注意 NUMA |

### 总结

- 编译时用 `GGML_NATIVE=ON` 自动启用本机指令集
- 线程数用物理核心数，超线程通常无益
- prefill 可用更多线程和更大批大小
- **注意事项**：小模型（<3B）线程过多反而变慢；NUMA 服务器务必用 `--numa`；BLAS 对小模型帮助有限，大模型才显著

---

## 第 14 讲: CUDA 加速

### 概念

NVIDIA GPU 是 llama.cpp 最常用的加速硬件。通过 CUDA 后端，把模型的部分或全部层放到 GPU 显存中，大幅提升推理速度。本讲讲解 CUDA 编译、运行、多 GPU 配置。

### 原理

**CUDA 加速原理：**

llama.cpp 把 Transformer 的核心算子（矩阵乘法、attention、RoPE）用 CUDA 重写。运行时把模型权重的部分层（`-ngl N`）加载到 GPU 显存，剩余层在 CPU。

**层卸载 (Layer Offload)：**

- `-ngl 0`：纯 CPU
- `-ngl N`：前 N 层放 GPU
- `-ngl 99`：全部层放 GPU（显存够的话）

**显存占用：**

模型权重 + KV Cache 都在 GPU 显存。7B Q4 模型约 4GB 权重 + 1GB KV Cache = 5GB 显存。

**关键参数：**

- `-ngl` / `--n-gpu-layers`：GPU 层数
- `--split-mode`：多 GPU 分配模式（layer / row）
- `--tensor-split`：多 GPU 张量分配比例
- `--main-gpu`：主 GPU（用于非张量计算）

### 例子

**编译 CUDA 版本：**

```bash
# 确保 CUDA Toolkit 已安装
nvcc --version  # 检查

# 编译
cmake -B build -DGGML_CUDA=ON -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release -j

# 验证
./build/bin/llama-cli --version
# 应显示 CUDA 支持
```

**运行时配置：**

```bash
# 全部层放 GPU（推荐，显存够时）
./llama-cli -m model.gguf -p "Hello" -ngl 99

# 部分层（显存不够时）
./llama-cli -m model.gguf -p "Hello" -ngl 20

# 纯 CPU（对比基准）
./llama-cli -m model.gguf -p "Hello" -ngl 0
```

**显存占用估算：**

| 模型 | Q4 权重 | KV (4K, FP16) | 总显存 | 推荐 GPU |
|------|---------|---------------|--------|----------|
| 7B | 4.1 GB | 1.0 GB | 5.1 GB | RTX 3060 12GB |
| 13B | 7.4 GB | 1.8 GB | 9.2 GB | RTX 3080 10GB（紧张） |
| 34B | 19.5 GB | 2.5 GB | 22 GB | RTX 3090 24GB |
| 70B | 39.5 GB | 4.1 GB | 43.6 GB | RTX 4090 24GB（不够） |

**多 GPU 配置：**

```bash
# 自动分配（默认 layer 模式）
./llama-cli -m model.gguf -p "Hello" -ngl 99

# 指定张量分配比例（如 2:1）
./llama-cli -m model.gguf -p "Hello" -ngl 99 --tensor-split 2,1

# 指定主 GPU
./llama-cli -m model.gguf -p "Hello" -ngl 99 --main-gpu 0
```

**性能对比（Llama-7B Q4）：**

```bash
# 用 llama-bench 测试
./llama-bench -m model.gguf -ngl 0 -ngl 99

# 典型结果
# | ngl | pp (prefill) | tg (decode) |
# |-----|---------------|-------------|
# | 0   | 150 t/s       | 35 t/s      |  # CPU
# | 99  | 2000 t/s      | 120 t/s     |  # GPU
```

**监控 GPU 使用：**

```bash
# 实时监控
watch -n 1 nvidia-smi

# 或用 nvtop
nvtop
```

### 总结

- CUDA 编译用 `-DGGML_CUDA=ON`
- `-ngl 99` 全部层放 GPU，显存不够时减少
- 7B Q4 需约 5GB 显存，70B 需 40GB+
- **注意事项**：显存不够时 OOM 报错，需减少 `-ngl`；多 GPU 用 `--tensor-split` 分配；KV Cache 也在 GPU，长上下文要算上

---

## 第 15 讲: Metal (Apple Silicon) 加速

### 概念

Apple Silicon（M1/M2/M3/M4）的 Metal 框架让 llama.cpp 在 Mac 上获得 GPU 加速。由于 Mac 的统一内存架构，模型权重和 GPU 共享内存，无需拷贝，效率极高。本讲讲解 Metal 后端的使用和调优。

### 原理

**Apple Silicon 优势：**

1. **统一内存**：CPU 和 GPU 共享同一块内存，无需拷贝，大模型可全放 GPU
2. **大内存容量**：M 系列 Max/Ultra 芯片有 64-192GB 内存，能跑 70B 模型
3. **Metal 框架**：Apple 的 GPU 编程接口，llama.cpp 用 Metal Performance Shaders 加速

**Metal 后端工作方式：**

llama.cpp 把权重加载到统一内存，GPU 直接访问。`-ngl N` 控制多少层用 GPU 计算。由于无拷贝开销，几乎可以全部层放 GPU。

**关键参数：**

- `-ngl`：GPU 层数（Mac 上通常设 99，全放）
- `--metal-preferred-device`：选择 GPU（多 GPU Mac）
- `--batch-size`：批大小，影响 prefill 速度

### 例子

**编译 Metal 版本（macOS 默认）：**

```bash
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp

# Metal 默认开启
cmake -B build -DCMAKE_BUILD_TYPE=Release -DGGML_METAL=ON
cmake --build build --config Release -j

# 验证
./build/bin/llama-cli --version
```

**运行 Metal 加速：**

```bash
# 全部层放 GPU（推荐）
./build/bin/llama-cli -m model.gguf -p "Hello" -ngl 99

# 部分层（极少需要，除非内存极紧张）
./build/bin/llama-cli -m model.gguf -p "Hello" -ngl 10
```

**性能对比（M2 Pro, Llama-7B Q4）：**

```bash
./build/bin/llama-bench -m model.gguf -ngl 0 -ngl 99

# 典型结果
# | ngl | pp (prefill) | tg (decode) |
# |-----|---------------|-------------|
# | 0   | 180 t/s       | 30 t/s      |  # 纯 CPU
# | 99  | 800 t/s       | 65 t/s      |  # Metal
```

**不同 Mac 芯片性能参考：**

| 芯片 | 内存 | 7B Q4 速度 | 13B Q4 速度 | 70B Q4 可行性 |
|------|------|-----------|-------------|---------------|
| M1 | 8GB | 50 t/s | 不够 | 不行 |
| M2 Pro | 16GB | 65 t/s | 35 t/s | 不行 |
| M2 Max | 32GB | 80 t/s | 50 t/s | 紧张 |
| M3 Max | 64GB | 100 t/s | 70 t/s | 可行（Q4） |
| M2 Ultra | 192GB | 120 t/s | 90 t/s | 流畅 |

**长上下文优化：**

```bash
# Mac 长上下文用 Q8 KV Cache 省内存
./build/bin/llama-cli -m model.gguf -p "Hello" \
    -ngl 99 \
    --ctx-size 32768 \
    --cache-type-k q8_0 --cache-type-v q8_0
```

**多 GPU Mac（如 Mac Pro 双 GPU）：**

```bash
# 查看可用 GPU
./build/bin/llama-cli --list-devices

# 指定 GPU
./build/bin/llama-cli -m model.gguf -p "Hello" -ngl 99 --metal-preferred-device 1
```

### 总结

- Apple Silicon 上 Metal 默认开启，性能优秀
- 统一内存架构让大模型可全放 GPU
- `-ngl 99` 是 Mac 上的标准配置
- **注意事项**：8GB 内存的 Mac 只能跑 7B Q4；M1/M2 普通版内存有限，建议选 Pro/Max/Ultra；Metal 性能受散热影响，长时间运行可能降频

---

## 第 16 讲: Vulkan 与 OpenCL

### 概念

Vulkan 和 OpenCL 是跨厂商的 GPU 加速方案，让非 NVIDIA GPU（AMD、Intel）也能加速 llama.cpp。本讲讲解这两种后端的编译和使用。

### 原理

**Vulkan 后端：**

Vulkan 是现代跨平台图形/计算 API，支持 NVIDIA、AMD、Intel GPU。llama.cpp 的 Vulkan 后端用计算着色器实现矩阵运算，性能略低于原生 CUDA/Metal，但兼容性最好。

**OpenCL + CLBlast 后端：**

OpenCL 是老式跨平台 GPU 计算框架，配合 CLBlast（OpenBLAS 的 OpenCL 版）加速矩阵乘法。性能不如 Vulkan，但支持更老的 GPU。

**适用场景：**

- **Vulkan**：AMD GPU、Intel 集显、跨平台部署
- **OpenCL**：老 GPU、嵌入式设备、Vulkan 不支持的场景

### 例子

**Vulkan 编译：**

```bash
# Ubuntu
sudo apt install -y libvulkan-dev vulkan-tools

# 编译
cmake -B build -DGGML_VULKAN=ON -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release -j

# 验证 GPU 检测
vulkaninfo | grep "deviceName"
```

**Vulkan 运行：**

```bash
# 列出可用 GPU
./build/bin/llama-cli --list-devices

# 运行（自动选 GPU）
./build/bin/llama-cli -m model.gguf -p "Hello" -ngl 99

# 指定 GPU（多 GPU 系统）
./build/bin/llama-cli -m model.gguf -p "Hello" -ngl 99 --main-gpu 0
```

**OpenCL + CLBlast 编译：**

```bash
# Ubuntu
sudo apt install -y ocl-icd-opencl-dev clblast

# 编译
cmake -B build -DGGML_OPENCL=ON -DGGML_CLBLAST=ON -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release -j
```

**OpenCL 运行：**

```bash
# 检测 OpenCL 设备
clinfo

# 运行
./build/bin/llama-cli -m model.gguf -p "Hello" -ngl 99
```

**性能对比（AMD RX 6700 XT, Llama-7B Q4）：**

```bash
./build/bin/llama-bench -m model.gguf -ngl 0 -ngl 99

# 典型结果
# | 后端 | pp (prefill) | tg (decode) |
# |------|---------------|-------------|
# | CPU  | 150 t/s       | 35 t/s      |
# | Vulkan | 600 t/s     | 80 t/s      |
# | OpenCL | 400 t/s     | 60 t/s      |
```

**Windows + Vulkan（AMD GPU）：**

```powershell
# 安装 Vulkan SDK
# https://vulkan.lunarg.com/

cmake -B build -G "Visual Studio 17 2022" -DGGML_VULKAN=ON
cmake --build build --config Release
```

### 总结

- Vulkan 是非 NVIDIA GPU 的首选加速方案
- OpenCL + CLBlast 适合老 GPU 或嵌入式
- 性能通常：CUDA > Metal > Vulkan > OpenCL > CPU
- **注意事项**：Vulkan 性能受驱动质量影响大；AMD ROCm 后端在 Linux 上更优（但配置复杂）；Intel 集显只能跑小模型

---

## 第 17 讲: 多 GPU 与张量拆分

### 概念

当单个 GPU 显存不够时，llama.cpp 支持把模型拆分到多个 GPU 上。本讲讲解多 GPU 的两种拆分模式（layer / row）、张量分配、性能权衡。

### 原理

**两种拆分模式：**

1. **Layer Split（层拆分，默认）**：把 Transformer 的不同层放到不同 GPU。简单稳定，但 GPU 间需传输中间结果，有通信开销。

2. **Row Split（行拆分）**：把每个张量按行拆分到多个 GPU，并行计算同一层。通信更频繁但更并行，适合高带宽互联（NVLink）。

**张量分配比例：**

`--tensor-split 1,2,1` 表示 GPU 0:1:2 的比例分配。显存大的 GPU 分更多。

**主 GPU：**

非张量计算（如 softmax、sampling）在主 GPU 上进行。通常选显存最大的。

**通信开销：**

- Layer Split：每层边界传输一次，开销小
- Row Split：每层多次通信，开销大但并行度高
- PCIe 互联：Layer Split 更优
- NVLink 互联：Row Split 更优

### 例子

**多 GPU 自动分配（Layer Split）：**

```bash
# 自动检测多 GPU，按显存比例分配
./llama-cli -m model.gguf -p "Hello" -ngl 99

# 查看分配
./llama-cli --list-devices
# 0: NVIDIA GeForce RTX 3090 (24576 MB)
# 1: NVIDIA GeForce RTX 3060 (12288 MB)
```

**手动指定分配比例：**

```bash
# 3090:3060 = 2:1（按显存）
./llama-cli -m model.gguf -p "Hello" -ngl 99 --tensor-split 2,1

# 3090 全部，3060 不用
./llama-cli -m model.gguf -p "Hello" -ngl 99 --tensor-split 1,0
```

**Row Split 模式（NVLink 系统）：**

```bash
# 启用 Row Split
./llama-cli -m model.gguf -p "Hello" -ngl 99 --split-mode row

# 适合 NVLink 互联的双 3090/4090
```

**70B 模型多 GPU 部署：**

```bash
# 70B Q4 约 40GB，单 3090 (24GB) 不够
# 双 3090 (48GB) 够用
./llama-cli -m llama-70b-q4.gguf -p "Hello" -ngl 99 --tensor-split 1,1

# 三 GPU（如 3090 + 3060 + 3060）
./llama-cli -m llama-70b-q4.gguf -p "Hello" -ngl 99 --tensor-split 2,1,1
```

**性能对比（Llama-70B Q4, 双 RTX 3090）：**

```bash
./llama-bench -m model.gguf -ngl 99 --tensor-split 1,1 --split-mode layer --split-mode row

# 典型结果
# | 模式 | pp (prefill) | tg (decode) |
# |------|---------------|-------------|
# | layer | 300 t/s      | 25 t/s      |  # PCIe
# | row   | 250 t/s      | 22 t/s      |  # PCIe（通信开销大）
# | row   | 450 t/s      | 40 t/s      |  # NVLink
```

**混合 GPU + CPU：**

```bash
# GPU 显存不够时，部分层在 CPU
./llama-cli -m llama-70b-q4.gguf -p "Hello" -ngl 60 --tensor-split 1,1
# 前 60 层在 GPU，剩余在 CPU
```

### 总结

- Layer Split 是默认模式，简单稳定，适合 PCIe 互联
- Row Split 适合 NVLink，并行度更高
- `--tensor-split` 控制分配比例
- **注意事项**：多 GPU 通信有开销，性能不是线性提升；混合 GPU+CPU 时，CPU 部分是瓶颈；70B 模型建议至少双 24GB GPU

---

# 第 5 章: Server 与 API

## 第 18 讲: llama-server 启动

### 概念

`llama-server`（旧名 `server`）是 llama.cpp 内置的 HTTP 服务器，把模型推理包装为 REST API。它兼容 OpenAI API 格式，可直接对接现有 LLM 应用，是本地部署 LLM 服务的核心工具。

### 原理

`llama-server` 基于 httplib 轻量 HTTP 库，启动后监听端口，接收 JSON 请求，调用 llama.cpp 推理，返回 JSON 响应。支持：

- **OpenAI 兼容端点**：`/v1/chat/completions`、`/v1/completions`、`/v1/embeddings`
- **原生端点**：`/completion`、`/tokenize`、`/detokenize`、`/infill`
- **多模态端点**：`/v1/chat/completions` 支持图片输入（LLaVA）
- **管理端点**：`/slots`、`/health`、`/props`

服务器维护多个「slot」（推理槽位），可并行处理多个请求。每个 slot 有独立的 KV Cache，避免上下文污染。

### 例子

**基础启动：**

```bash
./llama-server -m model.gguf --port 8080 --host 0.0.0.0
```

**完整配置：**

```bash
./llama-server \
    -m models/qwen2.5-7b.gguf \
    --port 8080 \
    --host 0.0.0.0 \
    -ngl 99 \
    --ctx-size 8192 \
    --cache-type-k q8_0 --cache-type-v q8_0 \
    --parallel 4 \                    # 4 个并行 slot
    --cont-batching \                 # 连续批处理
    --temp 0.7 \
    --top-k 40 \
    --top-p 0.9 \
    --repeat-penalty 1.1 \
    --system-prompt-file system.txt  # 默认系统提示
```

**API 密钥认证：**

```bash
# 启动时设置 API key
./llama-server -m model.gguf --api-key sk-mykey

# 客户端请求时带
curl http://localhost:8080/v1/chat/completions \
    -H "Authorization: Bearer sk-mykey" \
    -H "Content-Type: application/json" \
    -d '{"messages":[{"role":"user","content":"Hello"}]}'
```

**多模型切换（通过别名）：**

```bash
./llama-server \
    -m model1.gguf --alias qwen \
    -m model2.gguf --alias llama \
    --port 8080

# 请求时指定模型
curl http://localhost:8080/v1/chat/completions \
    -d '{"model":"qwen","messages":[...]}'
```

**后台运行（systemd）：**

```bash
# /etc/systemd/system/llama-server.service
[Unit]
Description=llama.cpp Server
After=network.target

[Service]
Type=simple
User=llama
ExecStart=/opt/llama.cpp/llama-server -m /models/qwen.gguf --port 8080 --host 0.0.0.0 -ngl 99
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl enable --now llama-server
```

**健康检查：**

```bash
# 检查服务状态
curl http://localhost:8080/health

# 查看槽位状态
curl http://localhost:8080/slots
```

### 总结

- `llama-server` 提供 OpenAI 兼容 API，可直接对接现有应用
- `--parallel N` 支持多请求并行
- 用 systemd 实现开机自启和自动重启
- **注意事项**：`--host 0.0.0.0` 监听所有网卡，生产环境务必加 `--api-key`；多 slot 会增加内存占用（每个 slot 独立 KV Cache）

---

## 第 19 讲: OpenAI 兼容 API

### 概念

`llama-server` 的 OpenAI 兼容 API 让现有 OpenAI 客户端（如 LangChain、OpenAI Python SDK）只需改 base_url 即可使用本地模型。本讲详解各端点的请求格式和参数。

### 原理

OpenAI API 的核心端点：

1. **`POST /v1/chat/completions`**：对话补全，支持多轮对话、流式、函数调用
2. **`POST /v1/completions`**：文本补全（非对话格式）
3. **`POST /v1/embeddings`**：生成文本嵌入向量
4. **`GET /v1/models`**：列出可用模型

请求格式与 OpenAI 完全一致，参数名也兼容（`temperature`、`top_p`、`max_tokens`、`stream` 等）。

### 例子

**对话补全（非流式）：**

```bash
curl http://localhost:8080/v1/chat/completions \
    -H "Content-Type: application/json" \
    -d '{
        "model": "qwen2.5-7b",
        "messages": [
            {"role": "system", "content": "你是中文助手"},
            {"role": "user", "content": "你好"}
        ],
        "temperature": 0.7,
        "max_tokens": 256
    }'
```

响应：
```json
{
    "id": "chatcmpl-xxx",
    "object": "chat.completion",
    "choices": [{
        "index": 0,
        "message": {"role": "assistant", "content": "你好！有什么..."},
        "finish_reason": "stop"
    }],
    "usage": {"prompt_tokens": 20, "completion_tokens": 30, "total_tokens": 50}
}
```

**Python SDK 调用：**

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:8080/v1",
    api_key="sk-no-key-needed"  # 本地无需真实 key
)

response = client.chat.completions.create(
    model="qwen2.5-7b",
    messages=[
        {"role": "system", "content": "你是中文助手"},
        {"role": "user", "content": "解释量子计算"}
    ],
    temperature=0.7,
    max_tokens=512
)

print(response.choices[0].message.content)
```

**LangChain 集成：**

```python
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(
    base_url="http://localhost:8080/v1",
    api_key="sk-no-key",
    model="qwen2.5-7b",
    temperature=0.7
)

response = llm.invoke("你好，请介绍一下自己")
print(response.content)
```

**文本补全（非对话）：**

```bash
curl http://localhost:8080/v1/completions \
    -d '{
        "model": "qwen2.5-7b",
        "prompt": "Once upon a time",
        "max_tokens": 100,
        "temperature": 0.8
    }'
```

**列出模型：**

```bash
curl http://localhost:8080/v1/models
# {"models":[{"id":"qwen2.5-7b","object":"model"}]}
```

**自定义停止词：**

```bash
curl http://localhost:8080/v1/chat/completions \
    -d '{
        "messages": [{"role":"user","content":"List 3 fruits"}],
        "stop": ["\n4.", "\n5."],
        "max_tokens": 100
    }'
```

### 总结

- `/v1/chat/completions` 是最常用端点，支持多轮对话
- 与 OpenAI SDK/LangChain 无缝对接，只需改 `base_url`
- 参数名与 OpenAI 一致（`temperature`、`max_tokens` 等）
- **注意事项**：`max_tokens` 限制生成长度，但不限制 prompt；流式输出用 `"stream": true`；函数调用需模型支持

---

## 第 20 讲: 流式输出与函数调用

### 概念

流式输出 (Streaming) 让模型生成时实时返回 token，提升用户体验。函数调用 (Function Calling / Tool Use) 让模型能调用外部工具。本讲讲解 llama-server 的流式 API 和函数调用支持。

### 原理

**流式输出原理：**

服务器在生成每个 token 后立即通过 SSE (Server-Sent Events) 推送，客户端逐 token 接收。格式为 `data: {json}\n\n`，最后以 `data: [DONE]\n\n` 结束。

**函数调用原理：**

llama.cpp 通过特殊语法支持函数调用：

1. 客户端在请求中声明可用函数（`tools` 字段）
2. 模型生成时输出函数调用 JSON
3. 服务器解析并返回 `tool_calls`
4. 客户端执行函数，把结果作为新消息发回
5. 模型基于结果继续生成

需要模型本身支持函数调用格式（如 Qwen2.5、Hermes、Llama-3.1）。

### 例子

**流式对话（curl）：**

```bash
curl http://localhost:8080/v1/chat/completions \
    -N \
    -d '{
        "model": "qwen2.5-7b",
        "messages": [{"role":"user","content":"讲个故事"}],
        "stream": true,
        "max_tokens": 256
    }'
```

输出：
```
data: {"choices":[{"delta":{"content":"从"}}]}

data: {"choices":[{"delta":{"content":"前"}}]}

data: {"choices":[{"delta":{"content":"有"}}]}

...

data: [DONE]
```

**Python 流式接收：**

```python
from openai import OpenAI

client = OpenAI(base_url="http://localhost:8080/v1", api_key="sk-no-key")

stream = client.chat.completions.create(
    model="qwen2.5-7b",
    messages=[{"role": "user", "content": "讲个故事"}],
    stream=True,
    max_tokens=256
)

for chunk in stream:
    if chunk.choices[0].delta.content:
        print(chunk.choices[0].delta.content, end="", flush=True)
```

**函数调用（声明工具）：**

```bash
curl http://localhost:8080/v1/chat/completions \
    -d '{
        "model": "qwen2.5-7b",
        "messages": [{"role":"user","content":"北京天气怎么样？"}],
        "tools": [{
            "type": "function",
            "function": {
                "name": "get_weather",
                "description": "获取城市天气",
                "parameters": {
                    "type": "object",
                    "properties": {
                        "city": {"type": "string", "description": "城市名"}
                    },
                    "required": ["city"]
                }
            }
        }],
        "tool_choice": "auto"
    }'
```

响应：
```json
{
    "choices": [{
        "message": {
            "role": "assistant",
            "tool_calls": [{
                "id": "call_xxx",
                "type": "function",
                "function": {
                    "name": "get_weather",
                    "arguments": "{\"city\":\"北京\"}"
                }
            }]
        }
    }]
}
```

**完整函数调用流程（Python）：**

```python
import json
from openai import OpenAI

client = OpenAI(base_url="http://localhost:8080/v1", api_key="sk-no-key")

def get_weather(city):
    # 实际调用天气 API
    return {"city": city, "temp": 25, "weather": "晴"}

tools = [{
    "type": "function",
    "function": {
        "name": "get_weather",
        "description": "获取城市天气",
        "parameters": {
            "type": "object",
            "properties": {"city": {"type": "string"}},
            "required": ["city"]
        }
    }
}]

messages = [{"role": "user", "content": "北京天气怎么样？"}]

# 第一轮：模型决定调用函数
response = client.chat.completions.create(
    model="qwen2.5-7b", messages=messages, tools=tools
)
msg = response.choices[0].message
messages.append(msg)

# 执行函数
if msg.tool_calls:
    for call in msg.tool_calls:
        args = json.loads(call.function.arguments)
        result = get_weather(**args)
        messages.append({
            "role": "tool",
            "tool_call_id": call.id,
            "content": json.dumps(result)
        })

# 第二轮：模型基于结果回答
response = client.chat.completions.create(
    model="qwen2.5-7b", messages=messages, tools=tools
)
print(response.choices[0].message.content)
# "北京现在25度，晴天。"
```

### 总结

- 流式输出用 `"stream": true`，SSE 格式逐 token 返回
- 函数调用需模型支持（Qwen2.5、Hermes、Llama-3.1）
- 完整流程：声明工具 → 模型决定调用 → 执行 → 回传结果 → 模型总结
- **注意事项**：流式输出要正确处理 `data: [DONE]`；函数调用 JSON 解析要容错；不是所有模型都支持函数调用

---

## 第 21 讲: 嵌入向量生成

### 概念

嵌入向量 (Embedding) 把文本转为定长向量，用于语义搜索、聚类、分类等任务。llama.cpp 支持用嵌入模型（如 bge-m3、e5-mistral、nomic-embed）生成向量，本讲讲解嵌入端点的使用。

### 原理

**嵌入模型原理：**

嵌入模型是基于 Transformer 的编码器，把输入文本编码为一个固定维度的向量（如 768、1024、4096 维）。语义相近的文本向量距离近，可用于：

- 语义搜索：把文档和查询都编码为向量，找最近邻
- 聚类：把相似文本聚到一起
- 分类：用向量训练分类器

**llama.cpp 嵌入生成：**

llama.cpp 用模型的最后一层 hidden state（通常取 `[CLS]` token 或平均池化）作为嵌入向量。需要：

1. 模型本身是嵌入模型（架构支持）
2. 启动 server 时用 `--embedding` 参数
3. 调用 `/v1/embeddings` 端点

### 例子

**启动嵌入服务器：**

```bash
# 用 bge-m3 嵌入模型
./llama-server -m bge-m3.gguf --port 8080 --embedding --ctx-size 8192 -ngl 99
```

**生成嵌入（单条）：**

```bash
curl http://localhost:8080/v1/embeddings \
    -d '{
        "model": "bge-m3",
        "input": "机器学习是人工智能的一个分支"
    }'
```

响应：
```json
{
    "object": "list",
    "data": [{
        "object": "embedding",
        "index": 0,
        "embedding": [0.123, -0.456, 0.789, ...]  // 1024 维
    }],
    "model": "bge-m3",
    "usage": {"prompt_tokens": 15}
}
```

**批量生成嵌入：**

```bash
curl http://localhost:8080/v1/embeddings \
    -d '{
        "model": "bge-m3",
        "input": ["文本一", "文本二", "文本三"]
    }'
```

**Python 调用：**

```python
from openai import OpenAI

client = OpenAI(base_url="http://localhost:8080/v1", api_key="sk-no-key")

response = client.embeddings.create(
    model="bge-m3",
    input="机器学习是人工智能的一个分支"
)

embedding = response.data[0].embedding
print(f"维度: {len(embedding)}")  # 1024
print(f"前5维: {embedding[:5]}")
```

**语义搜索示例：**

```python
import numpy as np
from openai import OpenAI

client = OpenAI(base_url="http://localhost:8080/v1", api_key="sk-no-key")

# 文档库
documents = [
    "Python 是一种解释型编程语言",
    "Java 是面向对象的编程语言",
    "机器学习是 AI 的分支",
    "深度学习使用神经网络",
    "今天天气真好"
]

# 生成文档嵌入
doc_embeddings = []
for doc in documents:
    resp = client.embeddings.create(model="bge-m3", input=doc)
    doc_embeddings.append(resp.data[0].embedding)

doc_embeddings = np.array(doc_embeddings)

# 查询
query = "什么是 AI？"
resp = client.embeddings.create(model="bge-m3", input=query)
query_embedding = np.array(resp.data[0].embedding)

# 计算余弦相似度
scores = doc_embeddings @ query_embedding / (
    np.linalg.norm(doc_embeddings, axis=1) * np.linalg.norm(query_embedding)
)

# 排序
for idx in np.argsort(-scores):
    print(f"{scores[idx]:.3f} - {documents[idx]}")
```

**常用嵌入模型：**

| 模型 | 维度 | 大小 | 中文支持 | 适用场景 |
|------|------|------|---------|---------|
| bge-m3 | 1024 | 2.3GB | 优秀 | 通用中文 |
| bge-large-zh | 1024 | 1.3GB | 优秀 | 纯中文 |
| nomic-embed | 768 | 0.5GB | 一般 | 英文 |
| e5-mistral-7b | 4096 | 14GB | 良好 | 高质量 |
| qwen3-embedding | 1024 | 0.6GB | 优秀 | Qwen 生态 |

### 总结

- 嵌入向量用于语义搜索、聚类、分类
- 启动 server 时加 `--embedding` 参数
- `/v1/embeddings` 端点，与 OpenAI 兼容
- **注意事项**：嵌入模型和对话模型架构不同，不能混用；中文场景优先 bge-m3 或 bge-large-zh；批量生成比单条高效

---

# 第 6 章: 高级特性

## 第 22 讲: Speculative Decoding

### 概念

Speculative Decoding（推测解码）是一种加速推理的技术：用一个小的「草稿模型」快速生成候选 token，再用大模型并行验证，正确则跳过大模型的逐 token 解码，错误则回退。能显著提升大模型推理速度。

### 原理

**工作流程：**

1. **草稿生成**：小模型（如 0.5B）自回归生成 N 个候选 token
2. **并行验证**：大模型（如 7B）一次前向计算这 N 个 token 的概率
3. **接受/拒绝**：比较两个模型的输出，接受匹配的 token，遇到不匹配时回退
4. **重复**：继续下一轮草稿生成

**加速原理：**

大模型逐 token 解码时，每次前向只生成 1 个 token，GPU 利用率低。Speculative Decoding 让大模型一次验证多个 token，提升并行度。即使部分草稿被拒绝，整体仍快于纯大模型解码。

**关键参数：**

- `--draft-model`：草稿模型路径
- `--draft-max`：每轮草稿最大 token 数（默认 16）
- `--draft-min`：每轮草稿最小 token 数（默认 5）
- `--draft-p-min`：接受概率阈值（默认 0.9）

### 例子

**启动草稿推理：**

```bash
./llama-cli \
    -m qwen2.5-7b.gguf \                    # 主模型
    --draft-model qwen2.5-0.5b.gguf \       # 草稿模型
    -p "请解释量子计算" \
    -n 256 \
    -ngl 99 \
    --draft-max 16 \
    --draft-p-min 0.9
```

**草稿模型选择：**

```bash
# Qwen2.5 系列：0.5B 草稿 + 7B/14B 主模型
./llama-cli -m qwen2.5-7b.gguf --draft-model qwen2.5-0.5b.gguf -p "Hello"

# Llama 3 系列：Llama-3.2-1B 草稿 + Llama-3-8B 主模型
./llama-cli -m llama-3-8b.gguf --draft-model llama-3.2-1b.gguf -p "Hello"
```

**性能对比：**

```bash
# 纯大模型
./llama-bench -m qwen2.5-7b.gguf -ngl 99
# tg: 55 t/s

# 草稿加速
./llama-bench -m qwen2.5-7b.gguf --draft-model qwen2.5-0.5b.gguf -ngl 99
# tg: 95 t/s（提升 70%）
```

**Server 模式启用草稿：**

```bash
./llama-server \
    -m qwen2.5-7b.gguf \
    --draft-model qwen2.5-0.5b.gguf \
    --port 8080 \
    -ngl 99 \
    --draft-max 16
```

**调优参数：**

```bash
# 草稿长度大：加速多但拒绝率高时浪费
./llama-cli -m model.gguf --draft-model draft.gguf --draft-max 32

# 草稿长度小：拒绝率低但加速少
./llama-cli -m model.gguf --draft-model draft.gguf --draft-max 8

# 接受概率高：质量好但加速少
./llama-cli -m model.gguf --draft-model draft.gguf --draft-p-min 0.95

# 接受概率低：加速多但可能质量下降
./llama-cli -m model.gguf --draft-model draft.gguf --draft-p-min 0.8
```

### 总结

- Speculative Decoding 用小模型加速大模型推理
- 草稿模型和主模型最好同源（同 tokenizer、同架构）
- 典型加速 1.5-3 倍，取决于草稿准确率
- **注意事项**：草稿模型太大会拖慢；草稿模型与主模型 tokenizer 不同会出错；创意任务（高温度）加速效果差，确定性任务加速好

---

## 第 23 讲: LoRA 适配器

### 概念

LoRA (Low-Rank Adaptation) 是一种轻量微调技术，只训练少量低秩矩阵，不修改原模型权重。llama.cpp 支持加载 LoRA 适配器，让一个基础模型适配多种任务，节省存储和切换成本。

### 原理

**LoRA 原理：**

原始权重矩阵 W (d×d) 不动，额外训练两个小矩阵 A (d×r) 和 B (r×d)，其中 r << d（如 r=8）。前向计算为 `W' = W + A @ B`。训练时只更新 A 和 B，参数量从 d² 降到 2dr。

**llama.cpp 加载 LoRA：**

LoRA 适配器存储为单独的 GGUF 文件（通常几 MB 到几十 MB）。运行时通过 `--lora` 加载，叠加到基础模型上。可同时加载多个 LoRA，用 `--lora-scaled` 控制权重。

### 例子

**加载单个 LoRA：**

```bash
./llama-cli \
    -m base-model.gguf \
    --lora chinese-lora.gguf \
    -p "你好" \
    -n 128
```

**加载多个 LoRA（带权重）：**

```bash
./llama-cli \
    -m base-model.gguf \
    --lora-scaled chinese-lora.gguf 1.0 \
    --lora-scaled coding-lora.gguf 0.5 \
    -p "写个 Python 函数" \
    -n 256
```

**Server 模式加载 LoRA：**

```bash
./llama-server \
    -m base-model.gguf \
    --lora chinese-lora.gguf \
    --port 8080
```

**动态切换 LoRA（Server API）：**

```bash
# 启动时声明可用 LoRA
./llama-server \
    -m base.gguf \
    --lora chinese-lora.gguf \
    --lora coding-lora.gguf \
    --port 8080

# 请求时指定 LoRA
curl http://localhost:8080/v1/chat/completions \
    -d '{
        "messages": [{"role":"user","content":"你好"}],
        "lora": ["chinese-lora"]
    }'
```

**转换 LoRA（从 HuggingFace 格式）：**

```bash
# 下载 HuggingFace LoRA
git clone https://huggingface.co/user/chinese-lora

# 转换为 GGUF
python convert_lora_to_gguf.py chinese-lora --out chinese-lora.gguf

# 使用
./llama-cli -m base.gguf --lora chinese-lora.gguf -p "你好"
```

**LoRA 训练（用 llama.cpp 的 train-text-from-scratch）：**

```bash
# 准备训练数据（plain text）
# 用 llama-finetune 训练 LoRA
./llama-finetune \
    --model-base base.gguf \
    --lora-out my-lora.gguf \
    --lora-r 8 \
    --train-data corpus.txt \
    --epochs 3
```

### 总结

- LoRA 是轻量微调，只训练低秩矩阵
- `--lora` 加载单个，`--lora-scaled` 加载多个带权重
- Server 支持运行时切换 LoRA
- **注意事项**：LoRA 必须与基础模型匹配（同架构、同 tokenizer）；多个 LoRA 叠加可能产生意外效果；LoRA 权重过大（>1.0）可能破坏模型

---

## 第 24 讲: 多模态模型 (LLaVA)

### 概念

LLaVA 是开源的多模态模型，能理解图片并回答关于图片的问题。llama.cpp 支持 LLaVA 及其变体（如 Qwen-VL、MiniCPM-V），让本地模型具备视觉能力。本讲讲解多模态模型的编译和使用。

### 原理

**LLaVA 架构：**

1. **视觉编码器 (CLIP)**：把图片编码为视觉特征向量
2. **投影层**：把视觉特征对齐到语言模型的嵌入空间
3. **语言模型 (Llama/Qwen)**：接收文本 + 视觉特征，生成回答

**llama.cpp 多模态支持：**

需要两个文件：
- 语言模型 GGUF（如 llava-v1.6-7b.gguf）
- 视觉编码器 GGUF（如 mmproj-llava-v1.6-7b.gguf，通常几百 MB）

运行时用 `--mmproj` 加载视觉编码器，用 `--image` 传入图片路径。

### 例子

**下载 LLaVA 模型：**

```bash
# 语言模型
huggingface-cli download liuhaotian/llava-v1.6-mistral-7b-gguf \
    llava-v1.6-mistral-7b.Q4_K_M.gguf --local-dir ./models

# 视觉编码器（mmproj）
huggingface-cli download liuhaotian/llava-v1.6-mistral-7b-gguf \
    mmproj-llava-v1.6-mistral-7b-f16.gguf --local-dir ./models
```

**命令行图片问答：**

```bash
./llama-cli \
    -m models/llava-v1.6-mistral-7b.Q4_K_M.gguf \
    --mmproj models/mmproj-llava-v1.6-mistral-7b-f16.gguf \
    --image photo.jpg \
    -p "描述这张图片" \
    -n 256 \
    -ngl 99
```

**Server 模式（多模态）：**

```bash
./llama-server \
    -m models/llava-v1.6-mistral-7b.Q4_K_M.gguf \
    --mmproj models/mmproj-llava-v1.6-mistral-7b-f16.gguf \
    --port 8080 \
    -ngl 99
```

**API 调用（图片问答）：**

```bash
# 图片转 base64
IMAGE_B64=$(base64 -w0 photo.jpg)

curl http://localhost:8080/v1/chat/completions \
    -d "{
        \"messages\": [{
            \"role\": \"user\",
            \"content\": [
                {\"type\": \"text\", \"text\": \"图片里有什么？\"},
                {\"type\": \"image_url\", \"image_url\": {\"url\": \"data:image/jpeg;base64,$IMAGE_B64\"}}
            ]
        }]
    }"
```

**Python 调用：**

```python
import base64
from openai import OpenAI

client = OpenAI(base_url="http://localhost:8080/v1", api_key="sk-no-key")

with open("photo.jpg", "rb") as f:
    img_b64 = base64.b64encode(f.read()).decode()

response = client.chat.completions.create(
    model="llava",
    messages=[{
        "role": "user",
        "content": [
            {"type": "text", "text": "描述这张图片"},
            {"type": "image_url", "image_url": {"url": f"data:image/jpeg;base64,{img_b64}"}}
        ]
    }],
    max_tokens=256
)

print(response.choices[0].message.content)
```

**其他多模态模型：**

```bash
# Qwen2-VL
./llama-server -m qwen2-vl-7b.gguf --mmproj qwen2-vl-mmproj.gguf --port 8080

# MiniCPM-V
./llama-server -m minicpm-v.gguf --mmproj minicpm-v-mmproj.gguf --port 8080
```

### 总结

- 多模态模型需要语言模型 + 视觉编码器两个文件
- `--mmproj` 加载视觉编码器，`--image` 传入图片
- API 用 `image_url` 字段传 base64 图片
- **注意事项**：图片分辨率影响速度，大图需先压缩；视觉编码器通常用 FP16（精度重要）；不同多模态模型 API 格式略有差异

---

## 第 25 讲: Grammar 约束生成

### 概念

Grammar 约束生成让模型输出严格符合指定语法（如 JSON、正则、自定义 DSL），避免格式错误。llama.cpp 内置 GBNF (Grammar-Based Sampling Format)，可在生成时强制约束 token 选择。

### 原理

**GBNF 原理：**

GBNF 是 llama.cpp 自创的语法描述语言，类似 BNF（Backus-Naur Form）。用户定义语法规则，llama.cpp 在每一步采样时，只允许符合当前语法状态的 token 被选中。

**工作流程：**

1. 解析 GBNF 语法，构建状态机
2. 每生成一个 token 前，计算当前语法允许的 token 集合
3. 在允许集合内采样（用温度、Top-K 等参数）
4. 更新语法状态，继续生成

**优势：**

- 100% 保证输出格式正确
- 不需要重试或后处理
- 适用于 JSON 提取、代码生成、结构化输出

### 例子

**JSON 输出约束：**

```bash
# json.gbnf
cat > json.gbnf <<EOF
root ::= "{" ws "\"name\"" ws ":" ws string "," ws "\"age\"" ws ":" ws number "}"
string ::= "\"" [^"]* "\""
number ::= [0-9]+
ws ::= [ \t]*
EOF

./llama-cli -m model.gguf \
    -p "生成一个人物信息" \
    -n 100 \
    --grammar-file json.gbnf
```

输出（保证是合法 JSON）：
```json
{"name": "Alice", "age": 25}
```

**Server 模式使用 Grammar：**

```bash
curl http://localhost:8080/v1/chat/completions \
    -d '{
        "messages": [{"role":"user","content":"生成一个人物"}],
        "grammar": "root ::= \"{\\\"name\\\": \\\"[^\\\"]*\\\", \\\"age\\\": [0-9]+}\""
    }'
```

**复杂 JSON Schema（数组、嵌套）：**

```gbnf
# person.gbnf
root ::= "{" ws "\"people\"" ws ":" ws "[" ws person ("," ws person)* ws "]" ws "}"
person ::= "{" ws "\"name\"" ws ":" ws string "," ws "\"age\"" ws ":" ws number "," ws "\"hobbies\"" ws ":" ws "[" ws string? ("," ws string)* ws "]" ws "}"
string ::= "\"" [^"]* "\""
number ::= [0-9]+
ws ::= [ \t\n]*
```

```bash
./llama-cli -m model.gguf -p "生成3个人物" -n 500 --grammar-file person.gbnf
```

输出：
```json
{
    "people": [
        {"name": "Alice", "age": 25, "hobbies": ["reading", "coding"]},
        {"name": "Bob", "age": 30, "hobbies": ["music"]},
        {"name": "Carol", "age": 28, "hobbies": ["travel", "photo"]}
    ]
}
```

**正则约束（电话号码）：**

```gbnf
# phone.gbnf
root ::= phone
phone ::= [0-9]{3} "-" [0-9]{3} "-" [0-9]{4}
```

```bash
./llama-cli -m model.gguf -p "生成一个电话号码" -n 20 --grammar-file phone.gbnf
# 输出：123-456-7890
```

**Python 调用（带 grammar）：**

```python
import json
from openai import OpenAI

client = OpenAI(base_url="http://localhost:8080/v1", api_key="sk-no-key")

grammar = '''
root ::= "{" ws "\"name\"" ws ":" ws string "," ws "\"age\"" ws ":" ws number "}"
string ::= "\"" [^"]* "\""
number ::= [0-9]+
ws ::= [ \t]*
'''

response = client.chat.completions.create(
    model="qwen2.5-7b",
    messages=[{"role": "user", "content": "生成一个人物"}],
    extra_body={"grammar": grammar}
)

data = json.loads(response.choices[0].message.content)
print(data)  # {"name": "Alice", "age": 25}
```

### 总结

- GBNF 约束生成保证输出格式 100% 正确
- 适用于 JSON 提取、结构化输出、代码生成
- Server API 用 `grammar` 字段传入
- **注意事项**：grammar 会限制模型创意，质量可能略降；复杂 grammar 会增加采样开销；先用简单 grammar 测试，再逐步复杂化

---

# 第 7 章: 性能与部署

## 第 26 讲: 性能调优与基准测试

### 概念

性能调优是 llama.cpp 实战的核心技能。本讲讲解如何用 `llama-bench` 进行基准测试，分析瓶颈，调优参数，让推理达到最佳性能。

### 原理

**性能指标：**

- **pp (prefill t/s)**：处理 prompt 的速度，影响首 token 延迟
- **tg (decode t/s)**：生成 token 的速度，影响整体吞吐
- **TTFT (Time To First Token)**：首 token 延迟，影响交互体验
- **内存占用**：模型权重 + KV Cache + 中间激活

**性能瓶颈分析：**

1. **内存带宽**：decode 阶段主要瓶颈，每生成 1 token 需读取全部权重
2. **计算量**：prefill 阶段主要瓶颈，并行处理 prompt token
3. **通信开销**：多 GPU 场景的层间数据传输
4. **I/O**：模型加载（首次）和磁盘读取

**调优维度：**

- 量化级别：Q4 vs Q8 vs F16
- 批大小：`-b`（prefill）、`-ub`（物理批）
- 线程数：`-t`（decode）、`--threads-batch`（prefill）
- GPU 层数：`-ngl`
- KV Cache 量化：`--cache-type-k/v`

### 例子

**基础基准测试：**

```bash
./llama-bench -m model.gguf

# 输出示例
# | model | size | threads | ngl | pp (t/s) | tg (t/s) |
# |-------|------|---------|-----|----------|----------|
# | qwen7b | 4.1G | 8 | 0 | 150 | 35 |
# | qwen7b | 4.1G | 8 | 99 | 2000 | 120 |
```

**多配置对比：**

```bash
# 对比不同 ngl
./llama-bench -m model.gguf -ngl 0 -ngl 10 -ngl 20 -ngl 99

# 对比不同线程数
./llama-bench -m model.gguf -t 4 -t 8 -t 16

# 对比不同量化
./llama-bench -m model-q4.gguf -m model-q8.gguf -m model-f16.gguf

# 对比不同批大小
./llama-bench -m model.gguf -b 128 -b 256 -b 512 -b 1024
```

**完整性能测试脚本：**

```bash
#!/bin/bash
# benchmark.sh
MODEL=$1
echo "=== Model: $MODEL ==="

# CPU 基准
echo "--- CPU only ---"
./llama-bench -m $MODEL -ngl 0 -t 8

# GPU 基准
echo "--- GPU full ---"
./llama-bench -m $MODEL -ngl 99

# 不同上下文
echo "--- Context sizes ---"
./llama-bench -m $MODEL -ngl 99 -p 128 -p 512 -p 2048 -p 8192

# KV Cache 量化
echo "--- KV quantization ---"
./llama-bench -m $MODEL -ngl 99 \
    --cache-type-k f16 --cache-type-v f16 \
    --cache-type-k q8_0 --cache-type-v q8_0 \
    --cache-type-k q4_0 --cache-type-v q4_0
```

**实际推理测试（带计时）：**

```bash
# 测 TTFT 和总时间
time ./llama-cli -m model.gguf -p "Hello" -n 128 -ngl 99 --no-warmup

# 测长上下文 prefill
time ./llama-cli -m model.gguf -f long_doc.txt -n 1 -ngl 99 --ctx-size 32768
```

**性能调优决策树：**

```
瓶颈在哪？
├─ prefill 慢
│   ├─ 增加 -b（批大小）
│   ├─ 增加 --threads-batch
│   └─ 用 GPU（-ngl 99）
├─ decode 慢
│   ├─ 用更低量化（Q4_K_M）
│   ├─ 用 GPU（内存带宽高）
│   ├─ 减少 KV Cache（--cache-type-k q8_0）
│   └─ 用 Speculative Decoding
├─ 内存不够
│   ├─ 用更低量化
│   ├─ 减少 --ctx-size
│   └─ KV Cache 量化
└─ 首次加载慢
    ├─ 用 mmap（默认）
    ├─ 模型放 SSD
    └─ 预热（--warmup）
```

**性能参考表（Llama-7B Q4）：**

| 硬件 | pp (t/s) | tg (t/s) | TTFT (4K) |
|------|----------|----------|-----------|
| CPU 8核 | 150 | 35 | 27s |
| RTX 3060 12G | 2000 | 120 | 2s |
| RTX 3090 24G | 3500 | 180 | 1.2s |
| M2 Pro | 800 | 65 | 5s |
| M3 Max | 1200 | 100 | 3s |

### 总结

- 用 `llama-bench` 系统测试不同配置
- prefill 瓶颈在计算，decode 瓶颈在内存带宽
- GPU 对 decode 提升最大（内存带宽高）
- **注意事项**：基准测试要预热（首次加载慢）；不同硬件性能差异大，需实测；生产环境要测真实负载，不只看峰值

---

## 第 27 讲: 容器化部署

### 概念

容器化让 llama.cpp 部署更便携、可复现。本讲讲解用 Docker 封装 llama-server，处理 GPU 加速、模型挂载、配置管理等关键问题。

### 原理

**Docker 部署优势：**

- 环境隔离：避免依赖冲突
- 可复现：同一镜像到处运行
- 易部署：拉取镜像即可用
- 易扩展：配合 K8s 实现弹性伸缩

**GPU 容器挑战：**

- 需要 NVIDIA Container Toolkit（nvidia-docker）
- 容器内访问 GPU 设备
- 显存隔离（默认不限制）

**模型管理：**

- 模型文件大（GB 级），不打入镜像
- 用 volume 挂载模型目录
- 用环境变量配置模型路径

### 例子

**基础 Dockerfile：**

```dockerfile
# Dockerfile
FROM ubuntu:22.04

RUN apt-get update && apt-get install -y \
    build-essential cmake git python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app
RUN git clone https://github.com/ggerganov/llama.cpp && \
    cd llama.cpp && \
    cmake -B build -DGGML_CUDA=ON -DCMAKE_BUILD_TYPE=Release && \
    cmake --build build --config Release -j --target llama-server

# 模型目录（运行时挂载）
VOLUME /models

EXPOSE 8080

ENTRYPOINT ["/app/llama.cpp/build/bin/llama-server"]
CMD ["-m", "/models/model.gguf", "--host", "0.0.0.0", "--port", "8080", "-ngl", "99"]
```

**构建和运行：**

```bash
# 构建
docker build -t llama-server .

# 运行（CPU）
docker run -d --name llama \
    -v /path/to/models:/models \
    -p 8080:8080 \
    llama-server

# 运行（GPU）
docker run -d --name llama \
    --gpus all \
    -v /path/to/models:/models \
    -p 8080:8080 \
    llama-server
```

**docker-compose.yml：**

```yaml
version: '3.8'

services:
  llama-server:
    build: .
    container_name: llama
    restart: unless-stopped
    ports:
      - "8080:8080"
    volumes:
      - ./models:/models:ro
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: all
              capabilities: [gpu]
    command: >
      -m /models/qwen2.5-7b.gguf
      --host 0.0.0.0
      --port 8080
      -ngl 99
      --ctx-size 8192
      --parallel 4
      --api-key sk-mykey
    environment:
      - CUDA_VISIBLE_DEVICES=0
```

**多模型服务：**

```yaml
services:
  llama-7b:
    build: .
    ports: ["8080:8080"]
    volumes: ["./models:/models:ro"]
    deploy:
      resources:
        reservations:
          devices: [{driver: nvidia, device_ids: ['0'], capabilities: [gpu]}]
    command: -m /models/qwen-7b.gguf --host 0.0.0.0 --port 8080 -ngl 99

  llama-14b:
    build: .
    ports: ["8081:8080"]
    volumes: ["./models:/models:ro"]
    deploy:
      resources:
        reservations:
          devices: [{driver: nvidia, device_ids: ['1'], capabilities: [gpu]}]
    command: -m /models/qwen-14b.gguf --host 0.0.0.0 --port 8080 -ngl 99
```

**Nginx 反向代理（负载均衡）：**

```nginx
upstream llama_backend {
    server llama-7b:8080;
    server llama-14b:8080;
}

server {
    listen 80;
    location / {
        proxy_pass http://llama_backend;
        proxy_set_header Host $host;
        proxy_buffering off;  # 流式输出必需
    }
}
```

**健康检查与自动重启：**

```yaml
services:
  llama:
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/health"]
      interval: 30s
      timeout: 10s
      retries: 3
    restart: unless-stopped
```

### 总结

- Docker 部署让 llama.cpp 便携可复现
- GPU 容器需 NVIDIA Container Toolkit
- 模型用 volume 挂载，不打入镜像
- **注意事项**：流式输出要关 Nginx buffering；多模型用不同端口或设备隔离；生产环境加 healthcheck 和自动重启

---

## 第 28 讲: 生产环境最佳实践

### 概念

把 llama.cpp 从「能跑」推进到「生产可用」需要考虑稳定性、安全性、可观测性、成本控制。本讲总结生产环境部署的最佳实践。

### 原理

**生产环境核心关注点：**

1. **稳定性**：长时间运行不崩溃、不泄漏内存
2. **安全性**：API 鉴权、模型访问控制、输入过滤
3. **可观测性**：日志、监控、告警
4. **成本控制**：资源利用率、自动伸缩
5. **可维护性**：平滑升级、配置管理

**关键设计：**

- 多实例负载均衡，避免单点故障
- 请求队列和限流，防止过载
- 模型热加载，不停机更新
- 监控 GPU/CPU/内存/延迟

### 例子

**生产级启动配置：**

```bash
./llama-server \
    -m /models/qwen2.5-7b.gguf \
    --host 0.0.0.0 \
    --port 8080 \
    -ngl 99 \
    --ctx-size 8192 \
    --cache-type-k q8_0 --cache-type-v q8_0 \
    --parallel 4 \
    --cont-batching \
    --metrics \                         # 启用 /metrics 端点
    --api-key sk-prod-key-xxx \
    --system-prompt-file /etc/llama/system.txt \
    --log-file /var/log/llama/server.log \
    --log-enable
```

**Prometheus 监控：**

```bash
# llama-server 启用 --metrics 后，暴露 /metrics 端点
curl http://localhost:8080/metrics

# 输出示例
# llama_tokens_generated_total 12345
# llama_tokens_predicted_total 67890
# llama_request_duration_seconds{quantile="0.5"} 0.5
```

**prometheus.yml：**

```yaml
scrape_configs:
  - job_name: 'llama'
    scrape_interval: 10s
    static_configs:
      - targets: ['llama-1:8080', 'llama-2:8080']
```

**Grafana 告警规则：**

```yaml
# 告警：decode 速度低于 20 t/s
- alert: LowGenerationSpeed
  expr: rate(llama_tokens_generated_total[1m]) < 20
  for: 5m
  annotations:
    summary: "LLM generation speed too low"

# 告警：实例下线
- alert: LlamaServerDown
  expr: up{job="llama"} == 0
  for: 1m
  annotations:
    summary: "Llama server is down"
```

**请求限流（Nginx）：**

```nginx
# 限制每 IP 每秒 5 个请求
limit_req_zone $binary_remote_addr zone=llama:10m rate=5r/s;

server {
    location / {
        limit_req zone=llama burst=10 nodelay;
        proxy_pass http://llama_backend;
    }
}
```

**多实例负载均衡（HAProxy）：**

```
frontend llama_front
    bind *:80
    default_backend llama_back

backend llama_back
    balance roundrobin
    option httpchk GET /health
    server llama1 llama1:8080 check
    server llama2 llama2:8080 check
    server llama3 llama3:8080 check
```

**自动伸缩（K8s HPA）：**

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: llama-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: llama-server
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Pods
    pods:
      metric:
        name: llama_active_requests
      target:
        type: AverageValue
        averageValue: 4
```

**日志收集（ELK）：**

```yaml
# Filebeat 配置
filebeat.inputs:
- type: log
  paths:
    - /var/log/llama/*.log
  fields:
    service: llama

output.logstash:
  hosts: ["logstash:5044"]
```

**安全加固清单：**

```bash
# 1. API 鉴权
--api-key sk-strong-random-key

# 2. 限制监听地址（仅内网）
--host 10.0.0.10

# 3. 限制上下文（防滥用）
--ctx-size 4096

# 4. 限制生成长度
# 在请求层限制 max_tokens

# 5. 输入过滤（应用层实现）
# 过滤敏感词、注入攻击

# 6. HTTPS（Nginx 层）
# 用 Let's Encrypt 证书
```

### 总结

- 生产环境关注稳定性、安全、可观测、成本
- 多实例 + 负载均衡避免单点故障
- 用 Prometheus + Grafana 监控
- **注意事项**：API key 必须强随机；限制 ctx-size 防滥用；流式输出要关代理 buffering；定期备份模型和配置

---

# 第 8 章: 集成与扩展

## 第 29 讲: Python 绑定 (llama-cpp-python)

### 概念

`llama-cpp-python` 是 llama.cpp 的 Python 绑定，提供 Pythonic API，让 Python 应用能直接调用 llama.cpp。它兼容 OpenAI API，可作为本地 OpenAI 替代品，是 Python 生态使用本地 LLM 的主流方案。

### 原理

**llama-cpp-python 架构：**

- 用 pybind11 把 C++ 库封装为 Python 模块
- 提供 `Llama` 类（底层 API）和 `LlamaServer`（HTTP 服务）
- 兼容 OpenAI Python SDK（通过 `llama-cpp-python[server]`）
- 支持所有 llama.cpp 后端（CPU、CUDA、Metal、Vulkan）

**安装方式：**

- `pip install llama-cpp-python`：CPU 版
- `CMAKE_ARGS="-DGGML_CUDA=on" pip install llama-cpp-python`：CUDA 版
- `CMAKE_ARGS="-DGGML_METAL=on" pip install llama-cpp-python`：Metal 版

### 例子

**安装：**

```bash
# CPU 版
pip install llama-cpp-python

# CUDA 版
CMAKE_ARGS="-DGGML_CUDA=on" pip install llama-cpp-python

# Metal 版（Mac）
CMAKE_ARGS="-DGGML_METAL=on" pip install llama-cpp-python

# 带 server 依赖
pip install llama-cpp-python[server]
```

**基础用法：**

```python
from llama_cpp import Llama

# 加载模型
llm = Llama(
    model_path="models/qwen2.5-7b.gguf",
    n_gpu_layers=99,        # GPU 层数
    n_ctx=4096,            # 上下文长度
    verbose=False
)

# 生成文本
response = llm(
    "Once upon a time",
    max_tokens=128,
    temperature=0.7,
    top_p=0.9,
    stop=["\n\n"]
)

print(response["choices"][0]["text"])
```

**对话模式：**

```python
from llama_cpp import Llama

llm = Llama(
    model_path="models/qwen2.5-7b.gguf",
    n_gpu_layers=99,
    n_ctx=4096,
    chat_format="qwen"  # 指定对话格式
)

# 多轮对话
messages = [
    {"role": "system", "content": "你是中文助手"},
    {"role": "user", "content": "你好"}
]

response = llm.create_chat_completion(
    messages=messages,
    max_tokens=256,
    temperature=0.7
)

print(response["choices"][0]["message"]["content"])
```

**流式输出：**

```python
llm = Llama(model_path="model.gguf", n_gpu_layers=99)

stream = llm(
    "讲个故事",
    max_tokens=256,
    stream=True,
    temperature=0.8
)

for chunk in stream:
    if "content" in chunk["choices"][0]:
        print(chunk["choices"][0]["content"], end="", flush=True)
```

**嵌入生成：**

```python
from llama_cpp import Llama

# 加载嵌入模型
embed = Llama(
    model_path="models/bge-m3.gguf",
    embedding=True,
    n_gpu_layers=99
)

# 生成嵌入
embedding = embed.embed("机器学习是AI的分支")
print(f"维度: {len(embedding)}")  # 1024
```

**函数调用：**

```python
llm = Llama(model_path="qwen2.5-7b.gguf", n_gpu_layers=99)

tools = [{
    "type": "function",
    "function": {
        "name": "get_weather",
        "description": "获取天气",
        "parameters": {
            "type": "object",
            "properties": {"city": {"type": "string"}}
        }
    }
}]

response = llm.create_chat_completion(
    messages=[{"role": "user", "content": "北京天气"}],
    tools=tools,
    tool_choice="auto"
)

print(response["choices"][0]["message"])
```

**作为 OpenAI 兼容服务启动：**

```bash
# 启动 server
python -m llama_cpp.server \
    --model models/qwen2.5-7b.gguf \
    --n_gpu_layers 99 \
    --host 0.0.0.0 \
    --port 8080

# 用 OpenAI SDK 调用
```

```python
from openai import OpenAI

client = OpenAI(base_url="http://localhost:8080/v1", api_key="sk-no-key")
response = client.chat.completions.create(
    model="qwen2.5-7b",
    messages=[{"role": "user", "content": "Hello"}]
)
```

**多模态（LLaVA）：**

```python
from llama_cpp import Llama
from llama_cpp.llama_chat_format import Llava15ChatHandler

# 加载视觉编码器
mmproj = "models/mmproj-llava.gguf"
chat_handler = Llava15ChatHandler(mmproj)

llm = Llama(
    model_path="models/llava.gguf",
    chat_handler=chat_handler,
    n_gpu_layers=99
)

# 图片问答
response = llm.create_chat_completion(
    messages=[{
        "role": "user",
        "content": [
            {"type": "text", "text": "描述图片"},
            {"type": "image_url", "image_url": {"url": "data:image/jpeg;base64,..."}}
        ]
    }]
)
```

### 总结

- `llama-cpp-python` 是 Python 生态使用 llama.cpp 的主流方案
- 提供 `Llama` 类（底层）和 OpenAI 兼容 server
- 支持对话、流式、嵌入、函数调用、多模态
- **注意事项**：安装时用 `CMAKE_ARGS` 指定后端；`chat_format` 要匹配模型；大模型加载慢，建议长驻进程

---

## 第 30 讲: 二次开发与 API 扩展

### 概念

llama.cpp 不仅是工具，更是可扩展的平台。本讲讲解如何基于 llama.cpp 进行二次开发，包括自定义采样、扩展 API、集成到现有系统、贡献回上游。

### 原理

**llama.cpp 扩展点：**

1. **C API**：`llama.h` 提供稳定的 C 接口，可被任何语言绑定
2. **采样器 (Sampler)**：可自定义采样逻辑
3. **后端 (Backend)**：可添加新的硬件后端
4. **Server 中间件**：可扩展 HTTP 端点

**二次开发常见场景：**

- 自定义采样策略（如 Beam Search、Constrained Decoding）
- 集成到现有系统（如游戏、IDE、聊天应用）
- 添加自定义 API 端点
- 实现新的量化格式
- 添加新的模型架构支持

### 例子

**C API 基础用法：**

```c
#include "llama.h"

int main() {
    // 初始化
    llama_backend_init();

    // 加载模型
    llama_model_params model_params = llama_model_default_params();
    model_params.n_gpu_layers = 99;
    llama_model * model = llama_model_load_from_file("model.gguf", model_params);

    // 创建上下文
    llama_context_params ctx_params = llama_context_default_params();
    ctx_params.n_ctx = 4096;
    llama_context * ctx = llama_init_from_model(model, ctx_params);

    // tokenize
    const char * prompt = "Hello";
    llama_token tokens[100];
    int n_tokens = llama_tokenize(
        model,
        prompt, strlen(prompt),
        tokens, 100,
        true, true
    );

    // prefill
    llama_decode(ctx, llama_batch_get_one(tokens, n_tokens));

    // 生成
    for (int i = 0; i < 128; i++) {
        llama_token new_token = llama_sampler_sample(ctx, -1);
        if (new_token == llama_token_eos(model)) break;

        // 解码并输出
        char buf[100];
        llama_token_to_piece(model, new_token, buf, 100, 0);
        printf("%s", buf);

        // 继续解码
        llama_decode(ctx, llama_batch_get_one(&new_token, 1));
    }

    // 清理
    llama_free(ctx);
    llama_model_free(model);
    llama_backend_free();
    return 0;
}
```

**编译：**

```bash
g++ my_app.cpp -I llama.cpp/include -L build -lllama -o my_app
```

**自定义采样器（C++）：**

```cpp
#include "llama.h"

// 自定义采样器：总是选第二大概率
struct MySampler {
    llama_sampler * chain;
};

llama_token my_sample(llama_context * ctx, int idx) {
    // 获取 logits
    float * logits = llama_get_logits_ith(ctx, idx);

    // 找前两个最大
    int vocab_size = llama_vocab_size(llama_get_model(ctx));
    int top1 = 0, top2 = 1;
    for (int i = 0; i < vocab_size; i++) {
        if (logits[i] > logits[top1]) {
            top2 = top1;
            top1 = i;
        } else if (logits[i] > logits[top2]) {
            top2 = i;
        }
    }

    return top2;  // 返回第二大概率
}
```

**扩展 Server（添加自定义端点）：**

```cpp
// 修改 llama-server/httplib.h 中的路由
svr.Post("/v1/my-endpoint", [](const httplib::Request & req, httplib::Response & res) {
    // 解析请求
    auto body = nlohmann::json::parse(req.body);

    // 自定义逻辑
    std::string result = my_custom_logic(body["input"]);

    // 返回
    nlohmann::json response = {
        {"result", result}
    };
    res.set_content(response.dump(), "application/json");
});
```

**Python 集成示例（游戏 NPC）：**

```python
from llama_cpp import Llama

class GameNPC:
    def __init__(self, model_path, character_desc):
        self.llm = Llama(
            model_path=model_path,
            n_gpu_layers=99,
            n_ctx=2048,
            chat_format="qwen",
            verbose=False
        )
        self.character = character_desc
        self.history = [
            {"role": "system", "content": character_desc}
        ]

    def talk(self, user_input):
        self.history.append({"role": "user", "content": user_input})

        response = self.llm.create_chat_completion(
            messages=self.history,
            max_tokens=128,
            temperature=0.8
        )

        reply = response["choices"][0]["message"]["content"]
        self.history.append({"role": "assistant", "content": reply})

        # 限制历史长度
        if len(self.history) > 10:
            self.history = [self.history[0]] + self.history[-8:]

        return reply

# 使用
npc = GameNPC("model.gguf", "你是一个 tavern 老板，热情但神秘")
print(npc.talk("你好，有什么推荐的酒？"))
print(npc.talk("听说这里有秘密通道？"))
```

**贡献回上游：**

```bash
# Fork 仓库
git clone https://github.com/yourname/llama.cpp
cd llama.cpp
git remote add upstream https://github.com/ggerganov/llama.cpp

# 创建分支
git checkout -b feature/my-feature

# 修改代码，提交
git add .
git commit -m "Add my feature"

# 推送并创建 PR
git push origin feature/my-feature
# 在 GitHub 上创建 Pull Request
```

**调试技巧：**

```bash
# 编译 Debug 版本
cmake -B build -DCMAKE_BUILD_TYPE=Debug -DGGML_CUDA=ON
cmake --build build --config Debug -j

# 用 GDB 调试
gdb ./build/bin/llama-cli
(gdb) run -m model.gguf -p "Hello"
(gdb) bt  # 崩溃时看回溯

# 用 Valgrind 检查内存
valgrind --leak-check=full ./build/bin/llama-cli -m model.gguf -p "Hello"
```

### 总结

- llama.cpp 提供稳定 C API，可被任何语言调用
- 可自定义采样器、扩展 Server 端点
- 二次开发常见于游戏、IDE、聊天应用集成
- **注意事项**：C API 在大版本间可能变化，注意兼容性；自定义采样器要正确处理 logits 内存；贡献回上游需遵循代码风格和测试规范

---

## 课程总结

恭喜完成 30 讲 llama.cpp 系统教程！回顾学习路径：

1. **基础入门（1-4 讲）**：理解 llama.cpp 定位，掌握编译和基本概念
2. **模型与量化（5-8 讲）**：GGUF 格式、量化原理、模型转换与选择
3. **命令行推理（9-12 讲）**：llama-cli 用法、参数调优、交互对话
4. **硬件加速（13-17 讲）**：CPU、CUDA、Metal、Vulkan、多 GPU
5. **Server 与 API（18-21 讲）**：HTTP 服务、OpenAI 兼容、流式、嵌入
6. **高级特性（22-25 讲）**：Speculative Decoding、LoRA、多模态、Grammar
7. **性能与部署（26-28 讲）**：基准测试、容器化、生产实践
8. **集成与扩展（29-30 讲）**：Python 绑定、二次开发

**继续学习的方向：**

- 关注 llama.cpp GitHub 仓库的更新（每月有新特性）
- 研究新模型架构（Mamba、RWKV、混合专家 MoE）
- 探索 RAG（检索增强生成）与 llama.cpp 的结合
- 实践 Agent 框架（如 LangGraph、AutoGen）与本地模型
- 关注量化算法进展（如 AWQ、GPTQ、AQLM）

llama.cpp 的核心理念是「让大模型在普通硬件上运行」，掌握量化、加速、部署三大技能，就能在本地高效运行各类开源大模型，构建隐私安全、低成本、可定制的 AI 应用。



