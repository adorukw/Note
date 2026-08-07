# MiMoCode 接入本地 llama.cpp 模型配置指南

> 目标：让 MiMoCode（`mimo`）使用本机 llama.cpp 启动的本地大模型（OpenAI 兼容接口）作为推理后端。
> 环境：AMD Ryzen AI 9 H 365（Strix Point）笔记本 · Linux（Debian 13，内核 6.12）· llama.cpp v10143（预编译发行版）

---

## 目录

1. [环境信息](#环境信息)
2. [第一步：修改 server.sh 支持 --alias](#第一步修改-serversh-支持---alias)
3. [第二步：启动 llama-server 并验证](#第二步启动-llama-server-并验证)
4. [第三步：在 MiMoCode 中注册本地端点](#第三步在-mimocode-中注册本地端点)
5. [第四步：修复配置问题（关键）](#第四步修复配置问题关键)
6. [验证命令](#验证命令)
7. [日常使用注意事项](#日常使用注意事项)
8. [常见问题 FAQ](#常见问题-faq)

---

## 环境信息

| 项目 | 值 |
|---|---|
| CPU | AMD Ryzen AI 9 H 365（10 核：4×Zen5 + 6×Zen5c，支持完整 AVX-512/BF16/VNNI） |
| 核显 | Radeon 880M（RDNA 3.5，GFX1150），通过 Vulkan 后端加速 |
| NPU | XDNA2 50 TOPS —— **llama.cpp 不支持，无法使用** |
| 内存 | 32GB（BIOS 预留 8GB 给核显后系统约 24GB） |
| llama.cpp | v10143（88b47a755），编译含 AVX512/AVX512_BF16/AVX512_VNNI/AVX512_VBMI + Vulkan |
| 模型 | `~/AAAPAN/Sw/models/qwen9b/Qwen3.5-9B-DeepSeek-V4-Flash-Q8_0.gguf`（9B · Q8_0 · 8.95B 参数） |
| 推理速度 | 生成约 6~9 t/s，prompt 解析约 55~100 t/s（带宽瓶颈，非算力瓶颈） |

---

## 第一步：修改 server.sh 支持 --alias

原因：llama-server 默认把 GGUF 的**完整文件路径**当作模型 id（`/v1/models` 返回一长串路径），
在 MiMoCode 里极难使用。通过 `--alias` 给它一个简短名字。

修改 `~/AAAPAN/Sw/llama_cpp/server.sh`（核心改动）：

```bash
# 变量区新增
ALIAS="qwen9b"

# 参数解析新增
-a|--alias)       ALIAS="$2"; shift ;;

# llama-server 启动命令新增
--alias "$ALIAS" \
```

最终启动命令形态：

```bash
llama-server -m <模型路径> --host 127.0.0.1 --port 8080 \
  -c 16384 -ngl 999 -t 10 --alias qwen9b --chat-template chatml
```

参数说明：

| 参数 | 含义 |
|---|---|
| `-ngl 999` | 模型层全部卸载到核显（Vulkan），0 为纯 CPU |
| `-c 16384` | 上下文长度。MiMoCode 是 agent，8192 不够用 |
| `-t 10` | 线程数（用满 10 核） |
| `--alias qwen9b` | 对外模型名，之后 `/v1/models` 的 id 就是它 |
| `--chat-template chatml` | 聊天模板（该模型为 chatml 系） |

---

## 第二步：启动 llama-server 并验证

```bash
cd ~/AAAPAN/Sw/llama_cpp
./server.sh -b          # -b 后台运行；日志 /tmp/llama-server.log
```

验证接口：

```bash
curl http://127.0.0.1:8080/v1/models
# 应返回 "id": "qwen9b"（而不是一长串文件路径）
```

测试对话接口：

```bash
curl -s http://127.0.0.1:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model":"qwen9b","messages":[{"role":"user","content":"你好"}],"max_tokens":32}'
```

查看日志 / 停止：

```bash
tail -f /tmp/llama-server.log   # 看运行状态
kill <PID>                      # 停止（启动时 -b 会打印 PID）
```

---

## 第三步：在 MiMoCode 中注册本地端点

在 MiMoCode 界面输入 `/connect` → 选择 **"+ Custom provider"**，填写：

| 字段 | 值 |
|---|---|
| Provider ID | `custom` |
| Display name | `本地llama.cpp`（纯显示用，随意） |
| First model id | `qwen9b`（必须与服务器 `/v1/models` 返回一致） |
| Base URL | `http://127.0.0.1:8080/v1`（必须带 `/v1`） |
| API key | `local`（llama-server 默认不鉴权，随便填） |

⚠️ `/connect` 生成的配置会把 API key 存成环境变量引用（`env: ["CUSTOM_API_KEY"]`），
**如果该环境变量未设置会导致请求失败**，需要按下一步手动修正。

---

## 第四步：修复配置问题（关键）

`/connect` 写完后，打开全局配置 `~/.config/mimocode/mimocode.jsonc`，
必须修正两点：

1. **补上 apiKey**：删掉 `env` 引用，直接在 `options` 里写死 `"apiKey": "local"`
2. **声明真实上下文窗口**：`limit.context` 必须等于服务器 `-c` 的值（16384），
   否则 mimo 误以为有 1M 窗口，会话变长后发送超长 prompt 导致服务器上下文溢出报错

最终配置全文：

```jsonc
{
  "$schema": "https://mimo.xiaomi.com/mimocode/config.json",
  "provider": {
    "custom": {
      "name": "本地llama_cpp",
      "npm": "@ai-sdk/openai-compatible",
      "options": {
        "baseURL": "http://127.0.0.1:8080/v1",
        "apiKey": "local",
        "setCacheKey": true
      },
      "models": {
        "qwen9b": {
          "name": "qwen9b",
          "limit": {
            "context": 16384,
            "output": 4096
          }
        }
      }
    }
  }
}
```

要点：

- 适配器必须用 `@ai-sdk/openai-compatible`（MiMoCode 自带）
- 字段名严格 camelCase：`baseURL` / `apiKey`（不是 `base_url` / `api_key`）
- `limit` 必须同时包含 `context` 和 `output` 两个数字，否则 schema 校验失败
- 模型 id 含 `/` 没问题（MiMoCode 只把第一个 `/` 当 provider/模型分隔符）

---

## 验证命令

```bash
mimo models custom
# 期望输出: custom/qwen9b — window 16K, compacts at 8K
mimo models | grep custom
```

看到 `custom/qwen9b` 且窗口为 16K 即配置生效。

> 注意：配置只在 mimo **启动时**读取，修改后必须关掉所有 mimo 窗口重开。

---

## 日常使用注意事项

1. **服务器必须先启动**：`./server.sh -b`，否则 mimo 报连接失败
2. **重启电脑后要重新启动服务器**（后台进程不持久）
3. **速度预期**：~6 t/s 生成 + 数千 token 的 prompt（解析 40~100 秒），
   mimo 的每一轮回复可能需要**好几分钟**，这是速度问题不是故障
4. **安全**：`--host` 保持 `127.0.0.1`，不要暴露局域网（服务器无鉴权）
5. **加速手段**：换 Q4_K_M 量化（约 5.3GB，速度翻倍）；或把 BIOS 核显预留
   UMA 从 8GB 调回 1GB 以恢复系统内存
6. **NPU 无法使用**：llama.cpp 无 XDNA 后端；要用 NPU 只能走 Windows +
   ONNX Runtime（AMD NPU EP）路线，与 llama.cpp 无关

---

## 常见问题 FAQ

| 现象 | 原因 | 解决 |
|---|---|---|
| 连接失败 / 请求报错 | 服务器没启动 | `./server.sh -b` 后重开 mimo |
| API key 相关报错 | `/connect` 生成的环境变量引用未设置 | 配置里写死 `"apiKey": "local"`（见第四步） |
| 会话一长就报错/溢出 | mimo 误以为 1M 窗口 | 配置 `limit.context: 16384`（见第四步） |
| `mimo models custom` 报 schema 错误 | `limit` 缺 `output` | 同时写 `context` 和 `output` |
| 改完配置没生效 | mimo 只在启动时读配置 | 完全退出再开新窗口 |
| 生成特别慢 | 内存带宽瓶颈（~90GB/s） | 换 Q4 量化 / 接受现实 |
