# AI 问答流中的意图识别与复杂卡片组件

> 本文档整理自一次关于 `PowerGrid` 项目的完整分析问答，包含两个问题：
>
> 1. **这个项目是如何让 AI 在问答流中实现正确的意图识别，并输出像「15分钟动态耦合网络 G(t)」这种复杂卡片组件的？**
> 2. **如果要在另一个项目中实现同样能力，该怎么问 AI？详细做法是什么？**
>
> 分析对象：`/home/adorukw/AAAPAN/横向/PowerGrid`
> 整理日期：2026-10-02

---

## 目录

- [第一部分：现有实现全面分析](#第一部分现有实现全面分析)
- [第二部分：如何让 AI 在另一个项目复刻这套能力](#第二部分如何让-ai-在另一个项目复刻这套能力)

---

# 第一部分：现有实现全面分析

## 问题一

> 给我全面分析一下这个让 AI 在问答流中实现正确的意图识别并在问答流中输出像「15分钟动态耦合网络 G(t)」这种复杂的卡片组件的功能是如何实现的，给我详细的文字解答。

---

## 回答一

### 一、总览：两层解耦的设计

这个功能能成立，核心是**把「模型决策」和「组件数据」彻底分开**：

- **意图识别层**：模型只负责「选对工具 + 选对 `analysis_type` + 填对参数」，由三层提示/约束保证；
- **组件输出层**：工具在返回结果里附带 `visualizations` 块，随消息持久化，**完全不经过模型上下文**；前端用 `type → Vue 组件` 注册表渲染。

完整链路：

```text
用户提问
  └─ AssistantService.chat()                          backend/modules/assistant/service.py
       ├─ 取历史消息（只带 role/content）+ 当前问题
       └─ ChatModelClient.chat()  ── 最多 3 轮工具循环
            ├─ system prompt = 基础约束 + 全部 Skill 正文
            ├─ POST /v1/chat/completions (tools=3, tool_choice=auto)
            └─ 模型返回 tool_calls
                 └─ StationCouplingAnalysisTool.execute(args)
                      ├─ _resolve_period()           # 日期意图 → 具体区间
                      ├─ network_provider()          # GET /api/v1/network/coupling/element
                      │    └─ build_dynamic_coupling_network()  # 96 帧 G(t)
                      └─ _analyze() dynamic_network 分支
                           ├─ 文本通道：snapshot_count / strongest_overall_edges / quality（给模型看）
                           └─ 可视通道：_dynamic_network_visualization() 精简块（给前端看）
            ├─ service 抽离 visualizations → 不进模型上下文
            └─ 模型基于文本通道写文字结论
       └─ 落库：message.content + message.visualizations（MEDIUMTEXT）
  └─ 前端 GET messages / POST chat
       └─ AssistantHomePanel: markdown 正文 + v-for 卡片
            └─ AssistantVisualization 外壳 → registry.ts → CouplingNetworkBlock.vue 播放器
```

---

### 二、意图识别是怎么做到「正确」的

模型本身不认识业务，正确性来自四层递进约束。

#### 2.1 基础系统提示词：先划边界

`service.py` 的 `_BASE_SYSTEM_PROMPT`（26-34 行）做了几件事：

- 领域路由：「站内因素耦合/趋势/评估/消纳 → 必须调用 `analyze_station_coupling`；站点之间 → `analyze_cross_station_association`」；
- 防止混用指标：「关系强度、置信度、Pearson 系数和共享用户 Jaccard 是不同指标，不得混用」；
- 防止因果误述；
- 明确「图表由工具结果自动附加，你只需给出文字结论」——从源头压制模型自己画图。

#### 2.2 Skill 注入：真正的「意图映射表」

`SkillLoader`（`modules/assistant/skills.py`）递归读取 `backend/skills/**/*.md`，`render_skills()` 把全文拼进系统提示词。`station-coupling-analysis/SKILL.md` 就是意图识别的主路由表：

| 问题意图 | analysis_type | 关键参数 |
|---|---|---|
| 站内网络是什么样 | `network` | 可选 snapshot_time |
| **15分钟动态耦合网络G(t)、快照播放、网络随时间演变** | **`dynamic_network`** | **无额外参数** |
| 两个因素关系多强 | `relationship` | source_factor / target_factor |
| 储能充放电趋势 | `trend` | factors=["storage"] |
| 五维评估、综合运行指数 | `assessment` | 无 |
| 光伏消纳率环比 | `pv_consumption_change` | 无 |
| … | … | … |

除了映射表，SKILL.md 还规定了：

- **日期意图解析纪律**：用户说「8月15日」必须传 `month=8, day=15`，**不许补当前年份**；只说「8月」传 `month=8`，由工具选最新可用年份；明确年份才传 `year`；
- **回答纪律**：不要在文字中生成 ECharts 配置；`dynamic_network` 的文字总结只引用 `snapshot_count`、`strongest_overall_edges`、`quality`，**不要逐帧罗列**（因为模型根本看不到帧明细）；
- **解释口径纪律**：融合关联强度≠Pearson；`indirect_path` 是多跳作用链；电价含弹性假设；环境是晴空辐照代理；消纳率变化用「百分点」。

这层是「意图识别正确率」的关键：**新增/调整分析类型必须同步改这张表**（文档里明确写了），否则模型不会调用。

#### 2.3 工具 JSON Schema：把意图锁进 enum

`STATION_COUPLING_TOOL_DEFINITION`（`association_tools.py` 33-114 行）用 OpenAI 兼容的 function calling schema：

- `analysis_type` 的 enum 固定 8 个合法值；
- 8 个要素（user/load/storage/pv/transformer/grid/environment/price）全部枚举，避免模型自由造词；
- `required: [station_id, analysis_type]`，`additionalProperties: false`；
- 参数描述里写清语义（如 `snapshot_time` 是 HH:MM、`aggregation` 是 average/total）。

模型即使被 Skill 引导，最终也必须落进这个 schema 才能形成 tool_call。

#### 2.4 工具内部防御：模型错了也不会崩

`execute()`（859-943 行）对模型输出做二次硬校验：

- `analysis_type` 不在 `valid_types` → 返回 `{"ok": false, "error": ...}`；
- `_resolve_period()` 处理所有日期形态：显式 `start/end`、`year+month+day`、仅 `month/day`（选最新可用年份，`period_resolution=latest_available_year`）、仅 `month`（解析月末）；
- `relationship` 必须有合法 `source_factor/target_factor`；`snapshot_time` 必须是 00:00–23:45 的 15 分钟刻度；
- 若五个实测序列覆盖率全为 0，直接返回「所选站点和时间范围没有可用的站内耦合测量数据」；
- 任何异常都被包成 `ok=false + error` 给模型，模型被要求「数据不足时明确说明、不得补造结论」。

也就是说：**提示词负责「引导选中」，schema 负责「合法形状」，工具负责「兜底纠错」**。

---

### 三、G(t) 复杂卡片的数据从哪来

#### 3.1 `dynamic_network` 分支

`_analyze()` 中 1055-1084 行：

```python
if analysis_type == "dynamic_network":
    block = _dynamic_network_visualization(
        payload,
        chart_id=f"station-coupling-dynamic-{start}-{end}",
        title="15分钟动态耦合网络 G(t)",
        subtitle=f"{站点名}，{start} 至 {end}，15分钟日均快照序列",
    )
    return {
        "dynamic_network": {                     # ← 文本通道，模型可见
            "snapshot_count": len(snapshots),
            "resolution_minutes": 15,
            "first_snapshot_time": ..., "last_snapshot_time": ...,
            "strongest_overall_edges": [top3],   # 每条压缩成 strength/confidence/名称等
            "stats": ..., "quality": ...,
        },
        "visualizations": [block] if block else [],   # ← 可视通道，模型不可见
    }
```

数据源是 `network_provider`，默认实现（173-176 行）直接调用页面同款接口 `router.network.coupling.get_dynamic_element_network`——即问答与页面共用同一套 15 分钟口径，不另造一份。

#### 3.2 底层 G(t) 的计算（`modules/coupling_network.py`）

`build_dynamic_coupling_network()`（1202 行起）产出：

- **8 个节点**：user/load/storage/pv/transformer/grid/environment/price；
- **11 条有向机制边**：每条边由三路证据融合：
  - 物理能量流（权重 0.45）：15 分钟站级功率平衡推导的占比；
  - 滞后 Pearson（权重 0.25）：源变量领先 0–4 个点（0–60 分钟），只用于发现关联；
  - 10% 线性化调度反事实（权重 0.30）；
  - 融合强度 ≤0 时乘 0.2 衰减；`causal_claim` 恒为 `false`；
- 置信度 = 覆盖率×0.35 + 关联可靠度×0.20 + 模型质量×0.25 + 功率平衡质量×0.20，假设型边再×0.82；
- **96 帧快照**（15 分钟粒度）+ **区间总体图 `overall`** + `quality`（含 `observable_balance_coverage` 等）；
- 环境节点用晴空辐照代理、变压器容量是推导值，均在 quality/methodology 里显式披露。

---

### 四、卡片块的构造：协议与体积压缩

#### 4.1 块协议（Block Protocol）

见 `docs/智能助手消息组件协议.md`。公共字段：

| 字段 | 说明 |
|---|---|
| `id` | 块唯一标识；**同一次回答内同 id 去重（保留最后一个）**，所以必须带日期/站点等区分参数 |
| `type` | 开放字符串，前端按注册表分发 |
| `version` | 当前一律 1，预留 schema 演进 |
| `title` / `subtitle` / `note` | 卡片外壳统一渲染 |

现有 6 类：`graph`、`line`、`bar`、`coupling-network`、`sub-charts`、`metric-table`，分别由 `_graph_visualization`、`_bar_visualization`、`_metric_table_visualization`、`_dynamic_network_visualization` 等构造函数生成。

#### 4.2 coupling-network 的精简设计（重点）

完整 G(t) 响应里每帧每边都带 `evidence`（物理/关联/反事实三套明细），直接塞进消息会非常大。`_dynamic_network_visualization()`（618-710 行）做了**静态元数据抽离 + 索引对齐**：

```jsonc
{
  "id": "station-coupling-dynamic-2025-08-15-2025-08-31",
  "type": "coupling-network", "version": 1,
  "title": "15分钟动态耦合网络 G(t)",
  "time_axis": { "resolution_minutes": 15, "horizon_steps": 96, "labels": [...96个] },
  "nodes": [{"id","name","unit","color"} × 8],        // 静态骨架只存一份
  "edges": [{"id","source","target","name"} × 11],    // 静态骨架只存一份
  "frames": [{                                         // 每帧只存数值，按索引对齐
    "time": "12:00",
    "nodes": [{"value": 123.4, "available": true}],    // frames[i].nodes[j] ↔ nodes[j]
    "edges": [{"strength": 0.62, "level": "中等", "polarity": "positive",
               "active": true, "confidence": 78.9}]    // frames[i].edges[j] ↔ edges[j]
  }],
  "overall": { "label": "区间总体", "nodes": [...], "edges": [...] },  // 同一套对齐结构
  "coverage": 0.55,
  "note": "..."
}
```

关键点：

- **丢弃 `evidence` 等重字段**，单测专门断言 `frames` 序列化后不含 `"evidence"`；
- 静态部分不重复，实测单块约 136KB，而 96 帧全量会大得多；
- `overall` 与帧结构完全一致，前端同一套索引逻辑即可切换视图；
- `coverage` 取 `quality.observable_balance_coverage`。

---

### 五、会话主循环：抽离、去重、落库

`ChatModelClient.chat()`（service.py 86-146 行）是一个标准的多轮 tool-calling 循环（`ASSISTANT_MAX_TOOL_ROUNDS` 默认 3）：

1. 带 `tools` + `tool_choice: auto` 请求模型；
2. 有 `tool_calls` 时：把 assistant 的 tool_calls 消息追加进上下文 → 逐个执行工具；
3. **执行结果处理是整条链路的枢纽**（127-144 行）：

```python
tool_visualizations = result.get("visualizations")
if isinstance(tool_visualizations, list):
    visualizations.extend(...)          # 收集到独立通道
result_for_model = dict(result)
result_for_model.pop("visualizations", None)   # 从模型可见结果中剔除
model_messages.append({"role": "tool", "content": json.dumps(result_for_model)})
```

块数据**从不进入模型上下文**：模型只看到 `dynamic_network` 摘要，所以既不消耗 token，也不可能被模型改写/复述。

4. 无 tool_calls 时返回 `AssistantReply(content, visualizations)`；
5. `_deduplicate_visualizations()`（212-226 行）按 `id` 去重、保留最后一次结果——这是重试/纠错场景下用户只看到最终版的保证。

`AssistantService.chat()`（285-328 行）：

- 加载历史时**只取 `role/content`**，历史消息的图表不回流给模型，避免上下文膨胀；
- 写入 `Message(content=answer, visualizations_json=json.dumps(visualizations, ensure_ascii=False))`；
- 模型客户端是 Router 模块级单例（`assistant_controller.py:16`），Skill 只在进程启动时加载一次。

#### 持久化与 API

- ORM：`Message.visualizations_json = Column("visualizations", Text)`（`models/assistant.py:68`），数据库列名就是 `visualizations`；
- 读取：`Message.visualizations` property 做安全 JSON 解析（失败返回 `[]`，不会 500）；
- 迁移：`initialize_assistant_tables()` 在 startup 幂等执行——建表、补 `deleted_at`、补 `visualizations` 列，并**把 MySQL 里的 TEXT 升级为 MEDIUMTEXT（16MB）**，因为 136KB 的 coupling-network 块会突破 TEXT 64KB 上限（代码在 git HEAD 的 `backend/main.py`，第 91-119 行）；
- API：`GET /api/v1/assistant/conversations/{id}/messages` 直接返回 `visualizations` 数组；`POST /api/v1/assistant/chat` 返回新消息。

---

### 六、前端渲染管线

#### 6.1 页面挂载

`AssistantHomePanel.vue:44-49`：

```vue
<div class="markdown-body" v-html="renderMarkdown(message.content)" />
<AssistantVisualization
  v-for="visualization in message.visualizations || []"
  :key="visualization.id"
  :visualization="visualization"
/>
```

正文走 markdown-it + DOMPurify；卡片逐块渲染。历史消息与实时回答走同一机制，无需特殊处理。

#### 6.2 外壳 + 注册表分发

`AssistantVisualization.vue` 只负责外壳（标题/副标题/类型徽标/脚注），本体交给：

```ts
const blockRegistry = {
  graph: GraphBlock, line: LineBlock, bar: BarBlock,
  'coupling-network': CouplingNetworkBlock,      // ← G(t) 走这里
  'sub-charts': SubChartsBlock, 'metric-table': MetricTableBlock,
}
```

未注册类型 → `UnknownBlock` 兜底卡，所以**后端先上线新块类型不会打挂旧前端**。

#### 6.3 CouplingNetworkBlock 播放器（481 行）

这是最复杂的组件，实现要点：

- **双视图**：「时间快照 / 区间总体」切换；工具栏显示当前时刻、96点/15分钟、活跃边数、可观测平衡覆盖率（覆盖率标签按 ≥75%/≥45% 变色）；
- **帧对齐**：`viewNodes/viewEdges` 用 `block.nodes[i]`（静态骨架）+ `frame.nodes[i]`（数值）按索引合并，`overall` 模式换源，完全复刻协议约定；
- **播放器**：上一帧/播放/下一帧/滑杆/序号；`setInterval` 900ms 循环，`onBeforeUnmount` 清理定时器和 ResizeObserver；
- **ECharts graph 固定布局**：8 个节点写死坐标（environment/pv/user/price/load/storage/transformer/grid），`layout: 'none'` + `roam: true`，切换帧时 `animationDurationUpdate: 280` 做平滑过渡；
- **视觉编码**：边宽 = 强度、透明度 = 置信度（非活跃边压到 0.16）、负向橙色虚线、混合紫色；tooltip 显示强度%、置信度%、作用方向与等级；
- **数值语义**：储能正值显示「放电」、负值「充电」、0「待机」，区间总体加「净」前缀；不同单位保留不同小数位；
- **自适应**：ResizeObserver 计算缩放比例，`autoresize` 的 v-chart，宽度自适应（满足协议「组件必须能独立渲染」的规定）。

其他块同理：`LineBlock` 按 unit 自动生成多 Y 轴、>24 点自动 dataZoom；`SubChartsBlock` 固定 2×2；`MetricTableBlock` 高亮最后一行综合指数。

---

### 七、可靠性与兼容策略

| 场景 | 处理 |
|---|---|
| 模型选错类型/参数非法 | `execute` 返回 `ok=false+error`，模型被要求如实说明 |
| 没有实测数据 | 提前返回可解释错误，不出空卡 |
| 模型不调工具/多轮调用 | 最多 3 轮，超限抛 `RuntimeError` → API 503 |
| 同 id 重复块 | 保留最后一次 |
| 未知块类型 | 前端 UnknownBlock |
| schema 演进 | `version` 字段；旧消息无 version 照常渲染 |
| 大体积块 | 静态骨架+帧压缩；DB 升 MEDIUMTEXT；建议单消息 <500KB |
| 接口失败 | 站内/站间工具均包 `ok=false`；HTTP 工具另有 host 白名单、只读、200KB 截断 |

---

### 八、测试与文档保障

- `backend/tests/test_assistant_association_tools.py`：`test_dynamic_network_returns_compact_snapshot_frames` 断言 type/version/id 含日期、96 帧、8 节点、帧内字段集、**不含 evidence**、`strongest_overall_edges` 恰 3 条；
- `backend/tests/test_coupling_network.py`：断言 96 快照、8 节点、11 边、每条边三路 evidence、`causal_claim=false` 等底层契约；
- `backend/tests/test_assistant.py`：断言 `AssistantReply.visualizations` 能落库并读回；
- 文档：`docs/智能助手消息组件协议.md`（协议+扩展指南）、`docs/智能助手Skill测试语句.md`（问法→预期参数/图表对照表）。

---

### 九、新增一个复杂卡片要动哪里（协议约定 6 步）

1. 后端写 `_xxx_visualization()` 构造函数（块只放 `result["visualizations"]`，摘要放文本通道）；
2. 若是新分析类型：扩展工具 schema 的 enum + `execute` 的 `valid_types` + `_analyze` 分支（或注册新工具到 `service.py` 的 `_tools/_tool_definitions`）；
3. 更新对应 `SKILL.md` 的「问题→参数映射表」；
4. 补单测（结构 + 体积约束）；
5. 前端 `api/assistant.ts` 加 TS 接口并并入联合类型；
6. 新建 `blocks/XxxBlock.vue` 并在 `registry.ts` 登记两行；最后 `npm run build` + 写一条带块的消息进库目视验收。

---

### 十、设计精髓总结

1. **意图识别 = 「提示词引导（Skill 映射表）+ Schema 约束（enum）+ 工具兜底（防御式解析）」**，三层缺一不可；模型只决定「调什么」，不决定「数据是什么」。
2. **组件输出 = 独立于模型上下文的第二通道**：`result["visualizations"]` 被 service 的 pop 机制抽离，模型看不到大块数据，却能在文字里引用文本通道摘要。这是「复杂卡片不烧 token、不被模型幻觉污染」的根本原因。
3. **体积控制 = 静态骨架存一份 + 帧按索引对齐只存数值 + 丢弃 evidence**，136KB 可离线回放 96 帧。
4. **开放注册表 = 新增组件零管线改动**，未知类型有兜底、旧消息可兼容，前后端可以独立上线。

> **当前工作区状态备注（供参考）**
>
> 分析基于 git HEAD 的完整实现。当前工作区有一些未提交的重构改动：
> `backend/main.py` 已被改写成指向 `app.main` 的兼容入口，但 `backend/app/main.py` 源码目前不存在（只有 `__pycache__`）；
> `frontend/src/router/index.ts` 的工作区版本移除了 `/data/ai-assistant` 路由，而 `AppLayout.vue` 仍在向该路径跳转。
> 这部分重构完成后，MEDIUMTEXT 迁移与助手页面路由需要重新接回。

---

# 第二部分：如何让 AI 在另一个项目复刻这套能力

## 问题二

> 我现在想要在另一个项目中实现这种「问答流中实现正确的意图识别并输出复杂卡片组件的功能」该怎么问 AI 呢，给我详细做法。

---

## 回答二

下面这套方法的核心思想是：**不要问 AI「帮我实现问答流输出图表」，而要让它复刻一套「架构约定」**。AI 只有理解了 5 个关键机制（双通道分离、块协议、三层意图约束、体积压缩、注册表渲染），才可能一次做对。下面是完整的提问方法论。

---

### 一、先备齐信息（缺一项就会来回返工）

提问前把这些整理好，直接写进提示词：

| 类别 | 必须说明的内容 |
|---|---|
| 技术栈 | 后端框架、前端框架、图表库、ORM/数据库类型 |
| LLM 能力 | 用哪家模型、是否 OpenAI 兼容、**是否支持 function calling / tool_calls**、是否流式 |
| 现有链路 | 现有聊天接口文件、消息表结构、前端消息渲染组件位置 |
| 数据源 | 每类卡片的数据来自哪个接口/表，接口是否已存在 |
| 卡片清单 | v1 要哪几种（折线/柱状/表格/关系图/播放器类），各自字段 |
| 约束 | 消息体大小上限、列类型、历史消息兼容要求、是否需要离线回放 |
| 验收问法 | 5~10 条真实用户问法 + 期望的参数/卡片 |

---

### 二、主提示词模板（可直接复制填空）

这是最关键的「第一问」。要点：**要求 AI 先出方案、确认后再写码，并把它最可能漏掉的机制全部写死**。

```text
你是资深全栈架构师，熟悉 LLM tool calling、<后端框架>、<前端框架>。

【先做的事】
先只读探索代码库，输出一份实施方案（不要改代码），包含：目录/文件清单、
数据流图、块协议草案、风险点。等我确认后再分阶段实现。

【项目现状】
- 后端：<框架/入口/现有聊天接口>
- 模型：<厂商/模型/是否支持 tool_calls/是否流式>
- 前端：<框架/现有消息组件/图表库>
- 消息存储：<表名/现有字段/列类型>
- 现有数据接口：<列出可复用的接口>

【目标】
在现有问答会话流中实现完整链路：
用户自然语言提问 → 模型正确选择分析工具并填对参数 → 工具返回结构化卡片块 →
消息持久化 → 前端按类型注册表渲染富组件（含历史消息重新打开）。

【必须遵守的架构机制（硬性要求，逐条实现）】
1. 双通道分离：工具返回 {"<摘要字段>": {...}, "visualizations": [块...]}。
   服务层在把工具结果发给模型之前，必须 pop 掉 visualizations；
   块数据永远不进模型上下文；模型只能依据摘要字段写文字结论。
2. 块协议：每块含 id/type/version/title/subtitle/note + 类型专属字段；
   id 必须带区分参数（日期/站点等）；同一次回答按 id 去重、保留最后一个。
3. 意图识别三层，缺一不可：
   a) 系统提示词写清领域边界和"必须调用某工具"的规则；
   b) 每个领域一个 SKILL.md 文档，含"问题意图 → analysis_type/参数"映射表、
      日期解析规则（如"只说8月15日时传 month/day，不许补当前年份"）、
      回答纪律；运行时作为系统提示词注入；
   c) 工具 JSON Schema：所有可枚举参数用 enum；required 明确；
      additionalProperties: false；
   d) 工具内部对参数二次校验，错误返回 {"ok": false, "error": "..."}，
      不许向模型抛异常；数据不足时返回可解释失败而不是空数据。
4. 回答纪律写进 SKILL：禁止在文字里生成图表配置；只引用摘要字段，
   大块明细不要逐条/逐帧罗列。
5. 大卡片体积控制：静态元数据（节点/边/坐标骨架）只存一份；
   帧序列只存数值，与骨架按索引对齐；删除 evidence 等重字段；
   单块目标 < 150KB。
6. 持久化：消息表新增 visualizations JSON 列；先评估列大小上限，
   必要时做幂等启动迁移（如 TEXT→MEDIUMTEXT）；读取时安全解析，
   坏 JSON 返回空数组不报错。
7. 前端渲染：type→Vue/React 组件注册表；未知 type 渲染兜底卡；
   卡片外壳（标题/副标题/类型徽标/脚注）由统一分发器渲染；
   每个块组件只接受一个 block prop，禁止依赖路由/store/localStorage；
   有定时器/ResizeObserver 的组件必须在卸载时清理。
8. 工具调用循环：最多 N 轮；参数 JSON 解析失败、未知工具名都要转成
   可解释错误；组装历史上下文时只带 role/content，不回传历史卡片数据。

【数据与卡片清单】
v1 卡片：<类型列表 + 各类型字段草案>；数据接口：<逐个列出>

【验收标准】
- 给出 <5~10> 条测试问法，每条写明期望选中的参数和渲染出的卡片；
- 单测覆盖：块结构/帧压缩（断言重字段不出现）/同 id 去重/落库读回；
- 端到端：重启服务后打开历史会话，卡片仍能渲染和播放；
- 失败路径：模型选错参数、无数据、未知卡片类型都有兜底。

【交付方式】
阶段1 方案 → 我确认；阶段2 后端块协议 + 一个最简单卡片端到端打通；
阶段3 意图层（提示词/SKILL/schema/校验）；阶段4 最复杂卡片；
阶段5 前端注册表+兜底；阶段6 测试 + 架构文档。
每阶段结束给出验证命令和自测结果。
```

---

### 三、分阶段追问法（推荐 6 轮，不要一次全要）

一次让 AI 写全链路，结果通常「看起来对、机制全丢」。按下面节奏问：

**第 1 轮：勘察 + 方案（不写码）**

> 「只读代码，不动文件。输出：现有问答链路现状、我列的 8 条机制在你项目里分别落在哪些文件、有什么阻碍（比如模型不支持 tool calling）、分阶段计划。」

这一步要重点看它的方案里有没有：双通道、注册表、SKILL 映射表、体积控制。缺了就在确认前补要求。

**第 2 轮：后端最小闭环（协议 + 一个简单卡片）**

> 「实现块协议 + 消息持久化 + 一个 line 卡片，从工具到前端能显示。先用假数据，不要接真实接口。」

目的：先让链路通。此时要求它写 3 个测试：块落库读回、同 id 去重、服务层发给模型的请求里不含 visualizations（这个测试最重要）。

**第 3 轮：意图层**

> 「为这个工具补齐：系统提示词领域规则、SKILL.md 映射表、JSON Schema enum、工具参数校验。然后给出 5 条问法的预期参数对照表。」

**第 4 轮：接真实数据**

> 「把线卡数据源换成 <真实接口>，保持工具返回结构不变。失败时返回 ok=false 和可解释错误。」

**第 5 轮：最复杂卡片（播放器/网络图类）**

> 「实现 X 卡片：静态骨架 + 帧按索引对齐 + 只存数值 + 删除 evidence；写单测断言帧内不含重字段，并断言体积上限。前端实现 播放/暂停/滑杆/双视图，卸载清理定时器和 ResizeObserver。」

**第 6 轮：泛化 + 文档**

> 「新增第二、第三个卡片类型验证扩展成本：只能改 registry 两行 + 新组件文件。然后写 docs/<块协议文档>.md：协议字段、数据流图、新增卡片 6 步、体积与兼容策略。」

这份文档是给**未来的 AI 会话**用的——下次开新窗口，一句「先读 docs/块协议文档.md 再改代码」，就能恢复全部上下文。

---

### 四、关键机制的「错误问法 vs 正确问法」

| 机制 | ❌ 别这样问 | ✅ 要这样问 |
|---|---|---|
| 意图识别 | 「让 AI 自己判断该查什么」 | 「把问句→参数映射写进 SKILL 文档，工具参数用 enum 锁死，工具内二次校验」 |
| 卡片数据 | 「让模型返回图表 JSON」 | 「工具返回 visualizations 数组，服务层抽离，模型上下文里不得出现块数据」 |
| 卡片分发 | 「在消息组件里 if/else 判断类型」 | 「建 type→组件注册表，未知类型渲染兜底卡」 |
| 大卡片 | 「把完整数据放进消息」 | 「静态骨架一份 + 帧只存数值索引对齐 + 删 evidence；单块 <150KB」 |
| 模型乱说 | 「让它别瞎编」 | 「回答纪律写进 SKILL：只引用摘要字段、不逐帧罗列、相关性不称因果」 |
| 兼容 | 「先实现再说」 | 「历史消息无 version 照常渲染；未知 type 不报错；列类型做幂等迁移」 |
| 前端组件 | 「组件里用一下全局 store」 | 「块组件只收 block prop，禁止路由/store/localStorage 依赖，可独立渲染」 |

---

### 五、如果模型不支持 tool calling 怎么办

这是提问时要让 AI 先确认的第一件事。如果不支持，替代方案是：

1. **两段式**：第一段让模型输出结构化意图 JSON（用 JSON mode / 严格提示词）；
2. **代码侧校验**后用编程方式调用对应工具；
3. 卡片通道、块协议、注册表渲染全部不变。

对应的问法：

> 「模型不支持 tool_calls。请设计两段式方案：意图 JSON 结构化输出 + 本地参数校验 + 普通函数分发，其余机制（双通道/块协议/注册表）保持不变。」

---

### 六、验收时要让 AI 交出的「证据」

光说「已实现」不算，要求它给出：

1. **单测**：
   - 工具参数错误 → `ok=false`；
   - 帧序列化后不含重字段（如 `evidence`）；
   - 同 id 块去重保留最后一个；
   - 带块消息落库→读回结构一致；
   - **抓取发给 LLM 的请求体，断言里面没有 visualizations**（防止双通道被破坏）。
2. **意图对照表**：每条测试问法 → 期望 `analysis_type` + 参数；
3. **端到端演示路径**：不依赖模型的脚本/请求，先写一条带块的消息进库，刷新页面看历史渲染；
4. **真实链路抽测**：2~3 个自然问法走一遍模型，展示最终 `tool_calls` 参数。

---

### 七、常见翻车与追问话术

| AI 的表现 | 你追问 |
|---|---|
| 把图表配置塞给模型生成 | 「撤销。改为工具侧构造块，模型禁止生成图表配置，写进 SKILL 和系统提示词」 |
| 只加字段不抽离 | 「服务层必须在 json.dumps 给模型前 pop visualizations，并补一个请求体断言测试」 |
| 首轮就接真实数据库 | 「先用 provider/依赖注入 + 假数据打通，接口作为可替换 provider 注入」 |
| 块 id 固定不变量 | 「id 必须含日期/站点等区分参数，否则不同查询会互相覆盖，并补去重测试」 |
| 组件 500 行 if/else | 「拆成每个 type 一个组件 + registry.ts，未知类型走 UnknownBlock」 |
| 历史消息卡片不回显 | 「历史加载只回传 role/content 给模型，但 API 必须返回 visualizations，前端同一机制渲染」 |
| 数据量大没管 | 「按静态骨架+帧索引对齐压缩，给出压缩前后字节数对比」 |

---

### 八、一句话版本（时间有限时用）

> 「在 <栈> 项目里实现问答流富卡片：工具返回 `{摘要, visualizations}`，服务层抽离 visualizations 不进模型上下文；块含 id/type/version/title 等字段，按 id 去重；意图识别用『系统提示词 + SKILL 映射表 + JSON Schema enum + 工具校验』四层保证；大卡片用静态骨架+帧索引对齐压缩、<150KB；前端 type→组件注册表、未知类型兜底；消息存 JSON 列并做列容量迁移；给出测试问法对照表和请求体不含 visualizations 的断言测试。先出方案，确认后分 6 阶段实现。」

---

> 如果你把目标项目的技术栈、模型能力、现有聊天代码位置和想要的卡片清单补充进来，可以把上面的主提示词填成一份开箱即用的定制版，并附上针对该项目的分阶段验收清单。

---

*（全文完）*
