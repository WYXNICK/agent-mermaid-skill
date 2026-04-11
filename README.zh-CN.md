<div align="center">

# `agent-mermaid`

### 面向 Multi-Agent System 的专业级 Mermaid 架构文档生成技能

将真实的多智能体系统代码转换为**有依据、可展示、可交付的 Mermaid 架构文档包**——不是只生成一张“看起来不错”的图，而是一套工程团队与技术干系人都能真正使用的专业产出。

<p>
  <a href="./README.md"><strong>English</strong></a>
</p>

<p>
  <img src="https://img.shields.io/badge/Mermaid-Professional-blueviolet" alt="Mermaid Professional" />
  <img src="https://img.shields.io/badge/Multi--Agent-Systems-1f6feb" alt="Multi-Agent Systems" />
  <img src="https://img.shields.io/badge/Framework-Aware-0a7f5a" alt="Framework Aware" />
  <img src="https://img.shields.io/badge/Output-Deliverable%20Package-f59e0b" alt="Deliverable Package" />
  <img src="https://img.shields.io/badge/Quality-Syntax%20Guard%20%2B%20Self--Repair-8b5cf6" alt="Syntax Guard and Self Repair" />
</p>

</div>

---

> **为严肃的智能体架构文档而生。**  
> `agent-mermaid` 会把多智能体系统源码转化为**结构化的架构文档交付包**：既包含框架感知的 Mermaid 图表，也包含源码依据、运行时流程、风险说明，以及严格的 Mermaid 语法保障。

## 一眼看懂

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#E8EAF6','primaryTextColor': '#1A237E','primaryBorderColor': '#3949AB','lineColor': '#546E7A','secondaryColor': '#E3F2FD','tertiaryColor': '#F3E5F5','background': '#FAFAFA','fontSize': '14px'}}}%%
flowchart LR
    A["读取代码库"] --> B["构建系统清单"]
    B --> C["识别框架与模式"]
    C --> D["生成图表套件"]
    D --> E["校验 Mermaid 语法"]
    E --> F["交付文档与风险说明"]
```

## 概述

很多“画架构图”的提示词，最终只会得到一张静态示意图。  
而 `agent-mermaid` 做得更完整，也更适合真实工程场景：

- 先完整阅读已提供的代码与相关文件
- 盘点 **Agents、Tools、State、Orchestration、Memory、Config、Async、Error Handling**
- 识别**实际使用的框架与协作模式**
- 按项目规模与复杂度生成**有范围控制的 Mermaid 文档包**
- 明确区分哪些关系是**显式**、**推断**、还是**未知**
- 使用专门的 Mermaid 语法守卫进行质量校验
- 最终补齐**架构说明、风险、假设与建议**

你得到的不只是“图”，而是一份可用于以下场景的技术交付物：

- 新工程师入项与系统上手
- 架构评审与设计复核
- 多智能体系统的正式文档沉淀
- 面向技术干系人的清晰可视化说明

---

## 普通一次性画图提示 vs `agent-mermaid`

| 维度 | 普通一次性画图提示 | `agent-mermaid` |
|---|---|---|
| 输入处理 | 往往只扫一眼片段代码 | 先完整读取已提供文件 |
| 框架识别 | 常靠上下文猜测 | 通过代码标记识别真实框架 |
| 关系准确性 | 容易把事实与猜测混在一起 | 严格区分 **Explicit / Inferred / Unknown** |
| 输出范围 | 通常只有一张图 | 完整的架构文档交付包 |
| 运行时视角 | 往往缺失 | 提供时序图与控制流图 |
| Mermaid 稳定性 | 容易生成无法渲染的图 | 内置语法守卫与自修复思路 |
| 大型系统表现 | 容易拥挤失真 | 支持分层、分图与范围退化策略 |
| 不完整代码 | 常默认脑补 | 显式标记未知并透明降级 |
| 架构评审价值 | 更偏展示感 | 真正可用于工程评审与沟通 |

---

## 为什么这个 skill 很突出

### 1）先做完整代码清点，再开始绘图
在真正画任何一张图之前，这个 skill 会先对系统进行结构化盘点，包括：

- **Agents**
- **Tools**
- **State / Schema**
- **Orchestration**
- **Memory / Persistence**
- **Config**
- **Async / Concurrency**
- **Error Handling**

这意味着图不是凭感觉生成的，而是建立在对真实系统结构的理解之上。

### 2）通过代码标记识别框架与模式，而不是猜
它不会主观猜测这是哪种架构，而是对照已知框架的识别标记进行匹配，例如：

- 图式编排（graph-based orchestration）
- 消息传递式协作（message-passing）
- 分层委派（hierarchical delegation）
- ReAct 循环
- RAG 流水线
- 混合框架系统

因此，图表起点更准确，输出风格也更贴合真实实现。

### 3）显式 / 推断 / 未知 三分法，可信度更高
这是本 skill 最有价值的原则之一：

- **Explicit（显式）**：代码中明确表达出来的关系
- **Inferred（推断）**：通过命名、结构或框架行为推导出的关系
- **Unknown（未知）**：代码中被引用，但未在提供文件里出现的组件

它不会把猜测包装成事实，这使得输出在真实工程环境里更可靠。

### 4）内置 Mermaid 语法守卫与自修复思维
仓库中专门包含 Mermaid 语法防护参考，覆盖：

- `%%{init}` 放置规则
- 标签转义
- subgraph 约束
- edge label 写法
- sequence activation 配平
- 复杂度阈值
- 渲染失败时的简化与修复策略

这类能力，是普通 Mermaid 生成方案很少认真处理的。

### 5）具备范围控制与降级策略
并不是每个项目都值得生成 8 张图。  
这个 skill 会根据代码规模、复杂度与可确认信息决定输出深度，坚持：

**宁可少而精，也不要多而碎，更不要多而错。**

### 6）输出是一整套“专业交付物”
它交付的不只是 Mermaid 代码块，还包括：

- 交付头部信息
- 系统清单
- 图表索引
- 关键源码引用
- 阅读指引
- 推断说明
- 架构优点、风险与假设

这使它更像一份正式的工程文档，而不是一次性的临时回答。

### 7）覆盖现代 Agent System 的真实复杂度
它关注的不只是“主路径”，还包括：

- 大型系统
- 混合框架
- 不完整代码
- 异步与并行执行
- Memory / Checkpoint / Vector Retrieval
- 路由逻辑
- 重试与回退
- 错误与恢复流程

---

## 你最终会得到什么

| 交付项 | 包含内容 | 价值 |
|---|---|---|
| 交付头部 | 生成时间、分析源文件、框架、模式 | 建立上下文与可信度 |
| 系统清单 | Agents、Tools、Models、State Fields、APIs、Memory 等统计与命名 | 快速建立全局认知 |
| 图表索引 | 所有图表的编号、类型、标题与回答的问题 | 让整套文档可导航、可阅读 |
| 必选图 1–3 | 架构图、主流程时序图、路由/控制流图 | 覆盖结构、运行时与决策逻辑 |
| 可选图 4–8 | 类图/组件图、数据状态图、并行图、错误恢复图、内存生命周期图 | 在必要时补足深度 |
| 架构说明与风险 | Strengths、Risks、Assumptions、Recommendations | 把“图”提升为真正的工程洞察 |

---

## 图表套件（Diagram Suite）

这个 skill 定义了一套可伸缩的 8 图体系。

| # | 图表类型 | 核心作用 | 何时出现 |
|---|---|---|---|
| 1 | System Architecture | 展示系统静态结构与组件关系 | 始终生成 |
| 2 | Sequence — Primary Flow | 展示主路径下的运行时步骤 | 始终生成 |
| 3 | Routing & Control Flow | 展示路由条件、分支、重试与终止逻辑 | 存在条件路由时生成；也是核心必选图的一部分 |
| 4 | Class / Component Structure | 展示类层级、组合关系、接口/模型结构 | 存在 OOP 结构或 Pydantic/Typed 模型时 |
| 5 | Data / State Transformation | 展示数据和状态如何沿流程演化 | 存在显式 schema / state 转换时 |
| 6 | Parallel / Concurrent Execution | 展示并发、扇出、聚合与异步执行 | 存在 async、并行节点或并发 worker 时 |
| 7 | Error & Recovery Flow | 展示失败、重试、fallback 与恢复路径 | 存在错误处理逻辑时 |
| 8 | Memory & Persistence Lifecycle | 展示 memory、checkpoint、vector retrieval 的读写时机 | 存在 memory / persistence / retrieval 时 |

---

## 框架与模式覆盖范围

`agent-mermaid` 面向真实世界中的多智能体生态与混合架构。

### 支持的框架生态
- **LangGraph**
- **AutoGen / AG2**
- **CrewAI**
- **Pydantic AI**
- **DSPy**
- **Semantic Kernel**
- **LlamaIndex Agents**
- **OpenAI Swarm / Handoff**
- **Custom Architecture（自定义架构）**
- **Mixed Framework Systems（混合框架系统）**

### 支持的协作模式与行为
- **Supervisor / Worker**
- **Pipeline**
- **ReAct**
- **Group Chat / Message-Passing**
- **Swarm / Handoff**
- **RAG Pipeline**
- **Human-in-the-Loop（HITL）**
- **Streaming**
- **Parallel Fan-out / Aggregation**
- **Checkpointed / Memory-backed Flow**

---

## 准确性契约（Accuracy Contract）

这个 skill 对“自己在说什么”非常克制，也非常严格。

| 分类 | 含义 | 图中处理方式 |
|---|---|---|
| **Explicit** | 在源码中直接表达的关系 | 使用实线关系 |
| **Inferred** | 基于命名、结构或框架机制推导出的关系 | 使用虚线，并显式标注推断 |
| **Unknown** | 在当前提供文件中未出现、但被引用的组件 | 明确标记不确定性，而不是擅自补完 |

这也是为什么它的输出更适合真实工程评审，而不是只做视觉展示。

---

## 它是如何工作的

### 1. 先读全
skill 会先对已提供的代码与关联文件做完整预分析，建立系统清单。

### 2. 通过 `references/patterns.md` 识别框架
它会根据代码标记去匹配框架类型与标准模式，再选用合适的图表模板作为起点。

### 3. 选择输出范围
依据项目大小、复杂度和信息确定性，决定是只输出核心图表，还是扩展为更完整的图表包。

### 4. 生成图表
根据实际代码生成架构图、时序图、路由图，以及在必要时补充类图、数据图、并发图、错误图、内存图。

### 5. 用 `references/syntax-guard.md` 做 Mermaid 校验
输出前会对 Mermaid 语法、结构和渲染风险做检查，并在复杂图场景下采用自修复思路。

### 6. 补充说明与风险
最终加入系统优点、潜在问题、假设、推断和建议，形成完整交付。

---

## Prompt 灵感

下面这些提示词，适合在支持 skill 的智能助手中直接使用：

- **请为这个 multi-agent 仓库生成完整的 Mermaid 架构文档包。**
- **分析这个 LangGraph 项目，并输出架构图、时序图和路由图。**
- **可视化我的 AutoGen group chat 流程，并逐步展示运行时交互。**
- **为这个 CrewAI 工作流生成专业级 Mermaid 文档交付物。**
- **请分析这个混合框架 agent system，并明确区分显式关系与推断关系。**
- **为这个项目生成 memory、并行执行、错误恢复相关图表。**
- **帮我审查这个 agent architecture，并附上风险、假设和改进建议。**
- **我只提供了部分代码，请尽可能生成最佳架构文档，并清楚标记未知节点。**

---

## 最适合的使用场景

当你需要的不是“随手一画”，而是**准确 + 专业 + 可展示**的智能体架构文档时，`agent-mermaid` 会非常有价值。

### 典型场景
- Multi-Agent System 架构评审
- 新工程师技术 onboarding
- 内部平台设计文档沉淀
- 面向干系人的系统说明材料
- 重构与迁移方案前的结构梳理
- 复杂编排、路由与状态流调试
- 不同 agent framework 的实现对比
- 把源码快速转换成可维护的可视化文档

### 尤其适合在这些情况下使用
- 代码库跨多个文件
- 存在 supervisor/worker 或复杂路由逻辑
- 并发、memory、checkpoint 行为很关键
- 需要让他人理解架构，而不仅仅是阅读源码
- 一张简单示意图会误导读者

---

## 仓库结构

```text
agent-mermaid-skill/
├─ SKILL.md
└─ references/
   ├─ patterns.md
   ├─ syntax-guard.md
   └─ real-example.md
```

### 文件职责说明

| 文件 | 作用 |
|---|---|
| `SKILL.md` | 核心 skill 定义：工作流、图表规范、质量门禁、样式策略 |
| `references/patterns.md` | 框架识别标记与标准模板参考 |
| `references/syntax-guard.md` | Mermaid 语法陷阱、防护规则与自修复清单 |
| `references/real-example.md` | 从源码到完整交付物的端到端高质量示例 |

---

## 展示质量与样式能力

这个 skill 追求的不只是“正确”，也包括**清晰、专业、适合展示**。

### 样式原则
- **默认使用浅色主题**，更适合 GitHub / Markdown 文档阅读
- **可按需切换深色主题**，适合演示稿与深色界面
- 通过统一的 `classDef` 调色板表达不同节点语义
- 使用样式化边线区分控制流、工具调用、状态访问与错误路径
- Mermaid 图尽量做到既有表现力，又兼顾主流渲染环境稳定性

换句话说，它既关注图“是否好看”，也关注图“是否真的能稳定渲染”。

---

## 设计哲学

- **准确性优先于装饰性**
- **证据优先于猜测**
- **范围感知优先于图表泛滥**
- **专业交付优先于玩具示例**
- **透明表达不确定性，而不是虚构架构**

---

## 为什么它足够有吸引力

很多图表生成方案都能画出“像架构图”的东西。

但 `agent-mermaid` 的价值在于：它试图生成一份可以真正站到下面这些场景里的产出：

- 工程团队内部评审
- 架构设计讨论
- 技术负责人审阅
- 对产品/业务方的技术说明
- 文档交接与知识沉淀

它是**code-first** 的、**framework-aware** 的、**Mermaid 语法敏感**的，也是**面向正式交付**来设计的。

---

## 最后一句

**如果你想要的是：对工程师足够准确、对架构师足够专业、对技术干系人足够清晰的 Mermaid 架构文档，那么 `agent-mermaid` 就是值得放进工作流里的那个 skill。**
