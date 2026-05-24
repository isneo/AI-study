---
title: Actor-Critic 反转模式（Codex 修复 + Claude 评审）
type: 方法论
tags: [Codex协作, Actor-Critic, 评分停滞, 元评审]
input: Claude → Codex 评审循环评分停滞（±0.2）≥2 轮且剩余问题是文本级时
output: 反转角色突破停滞，Codex 修复 + Claude 元评审，B-1 实战 5.9→通过
updated: 2026-04-25
---

# Actor-Critic 反转模式（Codex 修复 + Claude 评审）

> 标准 Codex 三层协作框架的角色互换变体。当标准模式陷入"Claude 反复修但都漏掉同一类东西"的死循环时，把"生成"和"验证"角色对调。

## 标准模式 vs 反转模式

| 模式 | Actor（生成） | Critic（评审） | 闭环动作 |
|------|--------------|---------------|---------|
| **标准** | Claude 实现 | Codex 评审 | Claude 按反馈修复 |
| **反转** | Codex 生成修复（read-only sandbox） | Claude 元评审 | Claude 提交（如通过） |

## 触发条件（同时满足）

1. **评分停滞 ±0.2 持续 ≥2 轮**（如 5.9 → 5.9 → 5.9，未触发 6/10 通过门槛）
2. **剩余问题是文本级**（不是架构/设计/接口分歧），如：D1 文档清单遗漏、命名不一致、措辞模糊
3. **Codex 已给 file:line 级具体定位**（不是模糊的"还有问题"）

三者缺一不可。架构分歧场景反转无意义；评分震荡（如 5.0→6.5→5.5）也不需要反转。

## 为什么反转能突破停滞

标准模式的失败模式：**视角偏差跨轮迭代不消失**。Claude 在自己的视角下修改，每轮都按自己理解的"修复"应用 Codex 反馈，但反复漏掉同一类问题（因为 Claude 的盲点在 Claude 的修复回路里看不到）。

反转模式让 Codex 直接应用自己指出的修复（无视角传递损失），Claude 退到元评审位置（不是再去修复，而是验证 Codex 输出的完整性）。

## 安全约束（反转模式必备）

1. **沙盒隔离**：Codex 必须 read-only 模式跑，输出到 log，不直接写文件（防止 Codex 也产生不期望的 side effect）
2. **Claude 元评审三检查**：
   - (a) Codex 上一轮指出的所有问题点都被修复
   - (b) 没有 scope creep / 引入新设计（反转 ≠ 重写）
   - (c) syntax / format 完整性（如 markdown 结构、frontmatter 字段、代码块闭合）
3. **决策闭环留痕**：每轮反转的 sandbox log 路径 + Claude 元评审通过/拒绝结论必须存档（便于复盘"为什么这次反转有效"）

## 反转流程（5 步）

1. **Claude 决策反转**：判定触发条件全部满足，告知用户切换模式
2. **Codex read-only sandbox 跑**：传入上一轮评审 + 当前文档版本 + 任务"按反馈修订"
3. **Codex 输出 log**：完整修订内容写入 log 文件，不写文件系统（read-only 强制）
4. **Claude 元评审**：从 log 提取修订内容，对照 Codex 上一轮反馈三检查
5. **Claude 落盘 + 提交评审**：通过则 Write 到目标路径并提交 Codex 下一轮评审；不通过则要么用户介入要么继续反转

## 实战案例（B-1 v4 → v5，2026-04-25）

- **背景**：tool_library → organizational 重命名实施计划，Codex Layer-1 评审 4 轮 v1 4/10 → v2 5.8/10 → v3 5.9/10 → v4 5.9/10
- **停滞特征**：v3 → v4 全 REVISE，分数停 5.9（差 0.1 通过），剩余问题都是 D1 文档清单遗漏 / Step 8.2 描述措辞 / programmatic Prep gate 表述（全是文本级）
- **反转执行**：Codex 5.4 read-only sandbox 跑 v5 生成，输出到 `.tmp/codex_b1_v5_generate.log`
- **Claude 元评审**：grep-based 4 点检查 — R4 反馈每条是否对应 v5 改动 + 没有 scope creep + markdown 结构完整 + frontmatter 字段齐全
- **结果**：v5 通过元评审，Claude Write 到官方路径 `docs/plans/...实施计划_v5.md`，B-1 实施全程基于 v5 推进，Cutover 成功 1561 测试零回归

## 不适用场景（反例）

- **架构分歧**：Codex 反对的是设计选择，反转只会让 Codex 强推自己的设计 → 用户必须介入做架构裁决
- **评分震荡**：v3 6.5 → v4 5.0 表示反复挤泡沫，不是停滞；继续标准迭代
- **Codex 反馈模糊**：没给 file:line 定位，Codex 自己也修不出确定的内容；先要求 Codex 出具体 patch 描述再考虑反转

## 与标准模式的搭配

反转模式不是标准模式的替代，而是 **escape hatch**：
- 默认走标准模式（Claude 生成 → Codex 评审）
- 评分停滞才考虑反转（一次反转通过则继续标准模式收尾）
- 反转完成后下一轮立即恢复标准模式（用 Codex 评审反转产出，闭环验证）

## 与 Claude-Codex 三层协作框架的关系

本方法论是 `Claude-Codex三层协作框架.md` 的特殊变体（Layer-1 评审收敛阶段）。三层框架本身的角色定位不变（Layer-1 架构 / Layer-2 实施 / Layer-3 验证），反转只对 Layer-1 评分停滞这一具体卡点生效。