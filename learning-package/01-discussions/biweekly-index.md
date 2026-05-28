# 双周资料索引

本索引用来把最近一到两周新增或重点使用的学习材料，按时间和主题重新归档。它不保存聊天原文，只记录可公开复习的资料、用途和阅读顺序。

## 2026-05-14 至 2026-05-28

### 本期主线

这两周的资料从「Agent 概念学习」推进到「AI Native 风控项目表达」和「Agent 落地体系化」。重点不再是单篇文章或单个工具，而是把材料组织成几条能复习、能面试、能继续扩展的线：

- AI Native 风控平台：从传统 workflow 到 agentic workflow 的演进叙事。
- Agent 架构与 harness：上下文、约束、运行时、工具、trace、memory 的分层表达。
- Memory 与自进化：把规则、案例、SOP、artifact 和 trace 变成长期资产。
- 评估与观测：用 golden set、teacher model、trace、数据飞轮证明 Agent 可控可迭代。
- 面试与项目叙事：把资料转成 5 分钟版、15 分钟版和追问版表达。

### 按周分区

| 周期 | 主题焦点 | 重点资料 | 适合解决的问题 |
|---|---|---|---|
| 2026-05-14 至 2026-05-20 | AI Native 认知、组织变化、风控项目叙事起稿 | `AI_Native风控平台建设.pdf`、`ai-native-risk-control-v1-minimal.html`、`AI-Native组织_从执行很贵到决策很贵_高密度海报.pdf`、`AI_Native_software_production_iteration_5_posters.pdf` | 先讲清楚为什么做 AI Native、风控为什么适合、项目故事怎么起头 |
| 2026-05-21 至 2026-05-28 | Agent harness、memory、trace/eval、面试训练 | `AI_Native_面试训练手册_V2.html`、`ai-native-项目叙事.html`、`ai-native-问题深挖.html`、`agent-harness-讲解.html`、`agent-harness-问题梳理.html`、`ai-native-risk-artifact-memory-evolution.html`、`observability-frameworks-tutorial.html`、`memory-agent-architecture.html`、`recent-agenda-research.html` | 把项目讲深，补齐 Agent 技术边界、评估体系、Memory 和工程落地细节 |

### 按主题分类

#### 1. AI Native 风控与审核体系

| 资料 | 类型 | 用途 |
|---|---|---|
| [AI_Native风控平台建设.pdf](../assets/pdf/AI_Native风控平台建设.pdf) | PDF | 风控平台的体系化方案入口，适合建立整体业务叙事 |
| [AI_Native_risk_review_system.pdf](../assets/pdf/AI_Native_risk_review_system.pdf) | PDF | 审核体系和风控场景细化，适合补充业务落地细节 |
| [ai-native-risk-control-v1-minimal.html](../assets/html/ai-native-risk-control-v1-minimal.html) | HTML | 最小方案表达，适合快速复习和面试前预热 |
| [AI_Native风控审核Agent_训练手册.html](../assets/html/AI_Native风控审核Agent_训练手册.html) | HTML | 把审核 Agent 的训练、边界和讲法串起来 |

#### 2. 项目叙事与面试训练

| 资料 | 类型 | 用途 |
|---|---|---|
| [AI_Native_面试训练手册_V2.html](../assets/html/AI_Native_面试训练手册_V2.html) | HTML | 面试前主入口，用来组织 5 分钟版和追问版表达 |
| [ai-native-项目叙事.html](../assets/html/ai-native-项目叙事.html) | HTML | 把项目讲成「背景-问题-拆解-方案-结果」 |
| [ai-native-问题深挖.html](../assets/html/ai-native-问题深挖.html) | HTML | 准备面试官追问，避免只停留在概念层 |
| [near-term-todos.md](near-term-todos.md) | Markdown | 把待办转成可交付物，例如选型矩阵、Memory 一页纸、Runtime checklist |

#### 3. Agent 架构、Harness 与选型

| 资料 | 类型 | 用途 |
|---|---|---|
| [agent-3-layer-architecture.html](../assets/html/agent-3-layer-architecture.html) | HTML | 用三层结构讲 Agent，避免框架名堆砌 |
| [agent-ecosystem-map.html](../assets/html/agent-ecosystem-map.html) | HTML | 横向理解框架、SDK、runtime、harness 的生态位置 |
| [agent-frameworks-deep-dive.html](../assets/html/agent-frameworks-deep-dive.html) | HTML | 对比 Agent 框架与工程集成边界 |
| [agent-harness-讲解.html](../assets/html/agent-harness-讲解.html) | HTML | 讲清楚上下文、工具、记忆、观测、权限如何组合 |
| [agent-harness-问题梳理.html](../assets/html/agent-harness-问题梳理.html) | HTML | 梳理常见追问和未决问题 |
| [agent-stack-map-v4.html](../assets/html/agent-stack-map-v4.html) | HTML | 用栈视角统一产品、harness、runtime、tool、eval |
| [agent_handbook_2026Q2_2026-05.pdf](../assets/pdf/agent_handbook_2026Q2_2026-05.pdf) | PDF | 作为 Agent 基础知识和体系化补充 |

#### 4. Memory、Artifact、Trace 与自进化

| 资料 | 类型 | 用途 |
|---|---|---|
| [memory-agent-architecture.html](../../memory-agent-architecture.html) | HTML | Memory 机制和企业场景地图，适合独立专题复习 |
| [ai-native-risk-artifact-memory-evolution.html](../assets/html/ai-native-risk-artifact-memory-evolution.html) | HTML | 风控 artifact、memory 和系统演进之间的关系 |
| [observability-frameworks-tutorial.html](../assets/html/observability-frameworks-tutorial.html) | HTML | OpenTelemetry、LangSmith、LoongSuite 的观测和 trace 入门 |
| [AI_Native_Agent_product_loop_self_evolution_handbook.pdf](../assets/pdf/AI_Native_Agent_product_loop_self_evolution_handbook.pdf) | PDF | Agent 产品循环和自进化体系 |
| [AI_Native_Agent_product_handbook_series_Final.pdf](../assets/pdf/AI_Native_Agent_product_handbook_series_Final.pdf) | PDF | Agent 产品体系化手册 |

#### 5. AI 工具协作与工程方法

| 资料 | 类型 | 用途 |
|---|---|---|
| [Claude-Codex三层协作框架.md](../assets/markdown/Claude-Codex三层协作框架.md) | Markdown | Claude / Codex 分工和协作结构 |
| [Actor-Critic_反转模式Codex_修复_+.md](../assets/markdown/Actor-Critic_反转模式Codex_修复_+.md) | Markdown | 用 Actor-Critic 思路做生成、审查和修复 |
| [claude-code-踩坑.html](../assets/html/claude-code-踩坑.html) | HTML | Claude Code 使用经验、问题和边界 |
| [12factor-me-methodology.md](../../12factor-me-methodology.md) | Markdown | AI 协作中的个人方法论沉淀 |

### 推荐阅读顺序

#### 面试冲刺

1. [AI_Native_面试训练手册_V2.html](../assets/html/AI_Native_面试训练手册_V2.html)
2. [ai-native-项目叙事.html](../assets/html/ai-native-项目叙事.html)
3. [ai-native-问题深挖.html](../assets/html/ai-native-问题深挖.html)
4. [ai-native-risk-control-v1-minimal.html](../assets/html/ai-native-risk-control-v1-minimal.html)
5. [AI_Native风控平台建设.pdf](../assets/pdf/AI_Native风控平台建设.pdf)

#### 架构补课

1. [agent-3-layer-architecture.html](../assets/html/agent-3-layer-architecture.html)
2. [agent-ecosystem-map.html](../assets/html/agent-ecosystem-map.html)
3. [agent-harness-讲解.html](../assets/html/agent-harness-讲解.html)
4. [agent-stack-map-v4.html](../assets/html/agent-stack-map-v4.html)
5. [agent-frameworks-deep-dive.html](../assets/html/agent-frameworks-deep-dive.html)

#### Memory / Eval 专题

1. [memory-agent-architecture.html](../../memory-agent-architecture.html)
2. [ai-native-risk-artifact-memory-evolution.html](../assets/html/ai-native-risk-artifact-memory-evolution.html)
3. [observability-frameworks-tutorial.html](../assets/html/observability-frameworks-tutorial.html)
4. [AI_Native_Agent_product_loop_self_evolution_handbook.pdf](../assets/pdf/AI_Native_Agent_product_loop_self_evolution_handbook.pdf)
5. [recent-agenda-research.html](recent-agenda-research.html)

### 下期归档规则

后续每周或每两周追加一个分区，优先记录：

- 新增或重点使用的资料文件。
- 本期主题焦点和它解决的问题。
- 推荐阅读顺序，而不是只罗列文件名。
- 哪些资料适合面试、方案设计、架构补课、工具使用。

不记录完整聊天原文、会议链接、私人信息、内部人事细节、账号/路径/密钥等敏感内容。
