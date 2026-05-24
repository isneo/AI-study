# 历史综合整理

## 1. 讨论主线

这批历史资料大致经历了四个阶段：

| 阶段 | 时间 | 主题 | 形成的资料 |
|---|---|---|---|
| AI 工具与协作方式探索 | 2026-03 至 2026-04 上旬 | Claude Code、Codex、AI 写代码、人类验收、知识系统 | AI Native 知识系统、Claude/Codex 协作框架、Actor-Critic 模式 |
| Agent 与上下文机制深化 | 2026-04 中旬至 2026-05 上旬 | Agent harness、上下文、记忆、编排、sub-agent、工具调用 | agent handbook、Agent 产品循环、自进化、编排 8 阶段 |
| AI Native 组织和生产方式 | 2026-05 中旬 | 软件生产范式、执行成本下降、决策成本上升、组织角色变化 | AI Native 组织、软件生产迭代、学习海报系列 |
| 风控应用和面试叙事 | 2026-05 中下旬 | AI Native 风控平台、审核体系、项目叙事、面试表达 | 风控平台建设、风控审核体系、项目叙事、问题深挖、面试训练手册 |

## 2. 核心结论

### AI Native 的变化不是“工具升级”，而是生产关系变化

群里反复讨论的核心不是某个模型更强，而是执行成本显著下降后，真正稀缺的东西变成：

- 判断力：知道该做什么、不该做什么。
- 验收能力：能识别 AI 输出是否靠谱。
- 任务拆解能力：把模糊目标拆成可执行、可验证的步骤。
- 组织知识：把一次经验沉淀成可复用方法，而不是靠个人记忆。

### Claude Code / Codex 适合形成分工，而不是互相替代

历史讨论里形成了一个实用分工：

- Claude 更适合长上下文理解、方案生成、文档组织。
- Codex 更适合代码执行、工程修改、终端验证。
- Actor-Critic 模式可用于让一个角色生成，另一个角色审查和修正。
- 人类要负责最终验收、边界判断和风险控制。

### Agent 架构要分层看

后续资料把 Agent 拆成多层，而不是直接比较框架名：

- 产品层：用户看到的完整 Agent 产品。
- Harness 层：把模型、工具、记忆、观测、权限组织成可用系统。
- Runtime / 编排层：控制循环、handoff、状态、恢复和 checkpoint。
- Context / Memory 层：决定系统知道什么、记住什么、如何取回。
- Tool / Protocol 层：Function Calling、MCP、A2A 等工具接入方式。
- Guardrail / Evaluation 层：约束、验证、trace 和效果评估。

这个分层有助于判断某个框架到底解决哪一层问题，避免把 SDK、runtime、完整产品混在一起比较。

### 风控是适合 AI Native 落地的 B 端场景

风控场景的特殊优势是更容易形成 Ground Truth 和数据闭环：

- 任务结果相对可验证。
- 审核、拦截、召回、误杀等指标可以回流。
- 可以把规则、案例、SOP、历史决策沉淀为可检索知识。
- 适合把 agentic workflow 和传统 workflow 结合，而不是彻底替代。

### 面试叙事需要从“用了 AI”升级为“为什么这样演进”

资料中逐渐形成三段式表达：

1. 横向认知：行业 Agent / AI Native 正在怎么演进。
2. 风控应用：这些能力为什么适合风控，以及哪些不适合。
3. 差异化故事：为什么我们跑得快、怎么跑、踩过哪些坑、怎么验证。

## 3. 主题索引

### AI Native 软件生产

相关资料：

- `assets/html/AI-Native知识系统架构设计_对话复盘.html`
- `assets/pdf/AI_Native_software_production_iteration_5_posters.pdf`
- `assets/pdf/AI-Native组织_从执行很贵到决策很贵_高密度海报.pdf`
- `assets/pdf/AI_Native_learning_posters_series.pdf`

重点问题：

- 为什么执行变便宜后，决策和验收更重要。
- 如何把 AI 输出从一次性结果变成组织知识。
- 如何设计适合 AI 协作的任务、文档和验收流程。

### Claude / Codex 协作

相关资料：

- `assets/markdown/Claude-Codex三层协作框架.md`
- `assets/markdown/Actor-Critic_反转模式Codex_修复_+.md`
- `assets/markdown/claude.md`
- `assets/html/claude-code-踩坑.html`
- `assets/pdf/Cat_Wu_Claude_Code_近期演讲核心观点_海报集.pdf`

重点问题：

- Claude 和 Codex 如何分工。
- 哪些任务适合模型规划，哪些必须靠工程验证。
- 如何避免 AI 偷懒、局部修补、绕过根因。

### Agent 架构与 Harness

相关资料：

- `assets/html/agent-3-layer-architecture.html`
- `assets/html/agent-ecosystem-map.html`
- `assets/html/agent-frameworks-deep-dive.html`
- `assets/html/agent-harness-讲解.html`
- `assets/html/agent-harness-问题梳理.html`
- `assets/html/agent-stack-map-v4.html`
- `assets/pdf/agent_handbook_2026Q2_2026-05.pdf`
- `assets/pdf/ai-agent-orchestration-8-stage-infographics.pdf`

重点问题：

- Agent runtime、harness、产品、SDK 的边界。
- 上下文、记忆、工具、观测、权限如何组合。
- LangGraph、OpenAI Agents SDK、Spring AI、MCP、A2A 等分别解决什么问题。

### AI Native 风控

相关资料：

- `assets/html/ai-native-risk-control-v1-minimal.html`
- `assets/html/AI_Native风控审核Agent_训练手册.html`
- `assets/pdf/AI_Native风控平台建设.pdf`
- `assets/pdf/AI_Native_risk_review_system.pdf`

重点问题：

- 风控平台如何从传统 workflow 演进为 agentic workflow。
- 审核体系中哪些环节适合 Agent，哪些需要确定性规则。
- Ground Truth、评估集、回归测试如何形成闭环。

### 项目叙事与面试训练

相关资料：

- `assets/html/ai-native-项目叙事.html`
- `assets/html/ai-native-问题深挖.html`
- `assets/html/AI_Native_面试训练手册_V2.html`

重点问题：

- 如何讲清楚项目为什么做、怎么做、做成什么。
- 如何从业务、架构、工程、验证四个层次回答追问。
- 如何把风控经验和 Agent 行业趋势结合起来。

## 4. 未纳入内容说明

没有纳入完整聊天原文。历史讨论里混有大量私人聊天、工作变动、会议、社交互动、餐饮、账号问题等内容；公开资料包只保留学习主题的综合整理和明确资料文件。

图片没有纳入，不是因为不重要，而是当前只读工具只能返回内部 `.dat` 缓存占位，无法还原为可直接查看的图片文件。后续如果从原始客户端手动另存图片，可以放到 `assets/images/` 并在 `resource-catalog.md` 中补充。
