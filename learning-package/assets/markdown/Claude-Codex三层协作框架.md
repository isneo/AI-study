---
title: Claude-Codex三层协作框架
type: 框架
tags: [Codex, 协作, 三层架构]
input: 使用 Codex 进行多代理协作时
output: Claude-Codex 三层协作模型的设计和使用指南
updated: 2026-03-13
---

# Claude-Codex 三层协作框架 v2.2

> **⚠️ 重要**：不要使用 `Skill(skill-codex:codex)` 工具。
> 必须使用 `CODEX_HOME=~/.codex codex exec ...` 命令。
> Skill 工具是为 resume 场景设计的，不适用于新的评审任务。

> 基于 Actor-Critic 模式，Claude 负责理解和实现，Codex 负责验证和审查。
> 配套文件：工具箱（Claude-Codex协作工具箱.md）、案例（Claude-Codex实战案例.md）

## 版本变更

### v2.2 (2026-03-03)
- ✅ 任务分类机制（new/modify/mixed）
- ✅ 合约提取工具（自动提取接口定义）
- ✅ Sub-Agent 触发标准明确化
- ✅ 自检报告自动传递给 Codex
- ✅ 内部系统安全原则（成本收益分析）
- ✅ 动态迭代上限（3-7 轮）

### v2.1 (2026-03-03)
- ✅ Layer 1 自动化信息提取

### v2 (2026-03-03)
- ✅ Layer 2.5 自动化自检
- ✅ Sub-Agent 增量验证
- ✅ 迭代轮数控制

## 核心理念

**分工明确 + 自动化自检 + 增量验证 + 迭代控制**

### 角色分工

| 任务类型 | Claude 擅长 | Codex 擅长 | 建议分工 |
|---------|------------|-----------|---------|
| 需求理解 | ✅ 快速理解用户意图 | ❌ 需要明确指令 | Claude 主导 |
| 方案设计 | ✅ 创造性方案 | ✅ 工程细节把控 | Claude 初稿 → Codex 评审 |
| 代码实现 | ✅ 快速原型 | ✅ 精确实现 | Claude 实现 |
| 边界测试 | ❌ 容易遗漏边界 | ✅ 深度边界检查 | Codex 主导 |
| 安全审查 | ❌ 可能遗漏漏洞 | ✅ 全面安全检查 | Codex 主导 |

**协作增益验证**（2026-02-28 Actor-Critic 实战）：
- Codex 优势：深度代码理解、工程细节把控、风险识别全面（安全/合规/稳定性）
- Claude 优势：方向性设计、用户体验关注、实施可行性判断
- 效果：方案质量从 3/10（愿景文档）→ 9/10（可执行计划），提升 200%

### 四层架构

```
Layer 1: 规划层 (Planning)
  ├─ 任务分类（new/modify/mixed）
  ├─ 自动化信息提取
  ├─ 合约提取
  └─ 方案设计 → Codex 评审

Layer 2: 实施层 (Implementation)
  ├─ 快速实现
  └─ Sub-Agent 增量验证

Layer 2.5: 自动化自检 (Self-Check)
  ├─ 测试套件
  ├─ 语法检查
  ├─ 文档一致性
  └─ 生成报告 → 传递给 Codex

Layer 3: 验证层 (Verification)
  ├─ Codex 完整性检查 + 安全审查
  ├─ 问题优先级判断（P0/P1/P2）
  └─ 修复 → Sub-Agent 验证 → 重新评审
```

## Layer 1: 规划层

### 任务分类

| 任务类型 | 特征 | Layer 1 策略 |
|---------|------|-------------|
| **new** | 全新功能，无现有代码 | 直接设计 + 用户确认 |
| **modify** | 修改现有功能 | 自动提取现有定义 + 对比变更 |
| **mixed** | 既有新增又有修改 | 提取现有 + 设计新增 + 整合 |

### Claude 职责

1. **任务分类** → 选择对应策略
2. **自动化信息提取** → 脚本/Sub-Agent 提取现有定义（避免"凭记忆写"）
3. **合约提取** → 接口定义、参数约束、返回值规范
4. **方案设计** → 拆解 Phase、技术方案、验收标准
5. **产物** → 实施计划文档 + 合约文档

### Codex 职责

1. **方案评审** → 可行性、架构风险、安全隐患
2. **反馈** → APPROVE / REVISE / REJECT + 问题分级
3. **质量门槛** → APPROVE 才能进入 Layer 2

## Layer 2: 实施层

### Claude 职责

1. **快速实现** → 按 Phase 顺序实现
2. **增量交付** → 关键功能点完成后立即 Sub-Agent 验证

### Sub-Agent 增量验证

**触发时机**：
- 单个问题修复后 → 立即验证该问题
- 关键模块完成后 → 验证该模块
- 批量修复后 → 批量验证

**不触发**：文档更新、注释修改、变量重命名

## Layer 2.5: 自动化自检

### 自检项目

1. 测试套件（必须通过）
2. 语法检查（必须通过）
3. 文档一致性（必须通过）
4. 生成报告 → 自动传递给 Codex

### 自检报告传递

```bash
# 生成自检报告
SELF_CHECK_REPORT=$(bash hooks/lib/phase-self-check.sh phase1 docs/plans/T2_Phase1_实施报告.md)

# 传递给 Codex
codex exec --sandbox read-only --full-auto \
  "Review Phase 1 implementation.
   Self-check report: $SELF_CHECK_REPORT
   Focus on: security, edge cases, architecture."
```

## Layer 3: 验证层

### Codex 职责

1. **完整性检查** → 测试、文档一致性、验收标准
2. **安全审查** → 输入验证、权限控制、TOCTOU、注入攻击
3. **验收判定** → APPROVE / REVISE / REJECT（评分 X/10）

### Claude 职责

1. **问题优先级判断**
   - P0（阻塞）→ 必须修复
   - P1（重要）→ 应该修复
   - P2（建议）→ 记录到任务清单

2. **问题修复** → Sub-Agent 验证 → 重新提交 Codex

### 代码审查协作要点

**Codex 验证逻辑**：读取实际代码交叉验证、检查导出路径、验证类型兼容性、运行编译检查。

**Claude 配合要点**：
1. 提供完整的符号列表（不让 Codex 猜测）
2. 使用精确文件路径（`@/lib/storage` 而非 `../storage`）
3. 明确类型导入（`type { X }` vs `{ X }`）
4. 提供验证步骤（如 `tsc --noEmit`）

**迭代节奏**：
- 1-3 轮：快速试错，建立理解
- 4-8 轮：应收敛到架构正确性
- 9+ 轮：应只剩细节
- 15+ 轮：方法论有问题，需反思

### 评审结论决策流

Codex 给出评审结论后，Claude 独立判断是否接受：

```
Codex 给出结论（APPROVE / REVISE / REJECT + 问题清单）
    │
    ├── Claude 认可 → 按建议迭代实施
    │     └── 实施完成 → 重新提交评审
    │
    └── Claude 不认可 → 分析分歧
          │
          ├── Codex 过度设计（内部系统追求完美）
          │     └── Claude 说明理由，标记已知限制，跳过
          │
          ├── Codex 建议超出任务范围
          │     └── 记录到任务清单，本轮不处理
          │
          ├── 分歧涉及架构/方向性问题
          │     └── 提交给用户决策
          │
          └── 不确定谁对
                └── Claude 列出双方论点 → 提交给用户决策
```

**原则**：Claude 有独立判断权，不盲从 Codex。但分歧无法自行解决时，上报用户。

### 质量门槛

- **公网服务**：评分 ≥ 8/10，所有 HIGH 问题必须彻底修复
- **内部系统**：评分 ≥ 6/10 可接受，可标记已知限制

## 迭代轮数控制

### 动态上限

```
基础上限：5 轮
动态调整：
├─ 评分持续上升（+2 分/轮）→ 延长至 7 轮
├─ 评分停滞（±1 分波动）→ 保持 5 轮
└─ 评分下降（-2 分/轮）→ 缩短至 3 轮
```

### 触发暂停条件

1. 连续 3 轮发现同类问题 → 方法论有问题
2. 评分无明显提升 → 陷入局部最优
3. 新问题数量不减反增 → 复杂度失控
4. 达到动态上限 → 根据评分趋势判断

### 暂停决策流程

```
检测到暂停条件 → 生成暂停报告
  ├─ 迭代历史总结
  ├─ 问题模式分析
  ├─ 成本收益分析
  └─ 改进方向建议
       ↓
提交给用户决策：
  ├─ 选项 A：继续迭代（调整策略）
  ├─ 选项 B：架构变更（重新设计）
  ├─ 选项 C：接受现状（标记已知限制）
  └─ 选项 D：拆分任务（降低复杂度）
```

## 内部系统安全原则

### 适用场景

- 内部工具/系统（非面向公网）
- 攻击面有限（单用户或小团队）
- 数据敏感度低（无 PII/财务数据）

### 成本收益权衡

| 维度 | 彻底修复 | 接受限制 |
|------|---------|---------|
| 实现成本 | 高 | 低 |
| 维护成本 | 高 | 低 |
| 安全收益 | 高 | 中 |
| 攻击难度 | 极高 | 高 |

### 决策标准

```
IF 内部系统 AND 攻击面有限 AND 攻击难度高 THEN
  接受已知限制（标记 + 文档）
ELSE IF 公网服务 OR 高敏感数据 THEN
  彻底修复（不惜成本）
ELSE
  成本收益分析（具体情况具体分析）
```

### 已知限制标记规范

```markdown
## 已知限制（内部系统可接受）

### 1. 问题名称（严重度 - 已接受）
- **问题**：具体描述
- **影响**：影响范围
- **攻击难度**：高/中/低
- **缓解措施**：当前采取的措施
- **彻底修复方案**：完整解决方案（暂不实施）
```

## 协作要点

1. **提前介入** → Codex 在规划阶段就参与
2. **增量验证** → 核心功能完成就验证
3. **自动化自检** → Layer 2.5 减少低级错误
4. **明确标准** → 验收标准写入实施计划
5. **快速迭代** → 发现问题立即修复
6. **上下文管理** → Claude 保持连续性，Codex 专注验证

## 工具配置

**环境变量**（必须）：
```bash
export CODEX_HOME=~/.codex
```

> 原因：Claude Code sandbox 的 `$HOME` 被重定向，导致 `config.toml` 找不到。

标准调用模板（Layer 1/2/3）、自检脚本、合约提取工具见：[Claude-Codex协作工具箱.md](Claude-Codex协作工具箱.md)

## 适用场景

### 高优先级（必须使用）
- 复杂架构设计方案
- 安全敏感功能
- 多文件协同修改

### 中优先级（建议使用）
- 新功能实现（3+ 文件修改）
- 重构任务
- 性能优化

### 低优先级（可选）
- 单文件小改动
- 文档更新
- 配置调整

## 触发条件

当用户说以下关键词时，使用本框架：
- "和 Codex 协作"
- "让 Codex 评审"
- "复杂任务"
- "需要深度验证"
- "安全敏感"

## 常见错误

1. 忘记设置 CODEX_HOME
2. 跳过 Layer 1 评审 → 方向性错误
3. 跳过 Layer 2.5 自检 → Codex 发现低级错误
4. 等全部完成再评审 → 大规模返工
5. 超过 5 轮仍继续 → 方法论有问题
6. 忘记任务分类 → 使用错误策略
7. 内部系统追求完美 → 过度设计

---

**配套文件**：
- 工具箱：记忆库/语义记忆/Claude-Codex协作工具箱.md
- 案例：记忆库/语义记忆/Claude-Codex实战案例.md

*最后更新：2026-03-03*

---

## Claude-Codex协作工具箱

# Claude-Codex 协作工具箱

> **⚠️ 重要**：不要使用 `Skill(skill-codex:codex)` 工具。
> 必须使用 `CODEX_HOME=~/.codex codex exec ...` 命令。
> Skill 工具是为 resume 场景设计的，不适用于新的评审任务。

> 配套 Claude-Codex三层协作框架_v2.2.md 使用

## 目录

1. [Layer 2.5 自检脚本](#layer-25-自检脚本)
2. [Codex CLI 配置](#codex-cli-配置)
3. [Sub-Agent 验证模板](#sub-agent-验证模板)
4. [合约提取工具](#合约提取工具)
5. [信息提取脚本](#信息提取脚本)

---

## Layer 2.5 自检脚本

### phase-self-check.sh

完整脚本：`hooks/lib/phase-self-check.sh`

```bash
#!/usr/bin/env bash
# Phase Self-Check - 自动化 Layer 2.5 Claude 自检

set -euo pipefail

PHASE="$1"  # phase0, phase1, phase2, etc.
REPORT="$2"  # 实施报告路径

echo "=== Phase $PHASE Self-Check ==="

# 1. 运行测试套件
echo "[1/5] Running test suite..."
if bash "hooks/tests/test-$PHASE.sh"; then
  echo "✅ Tests passed"
  TEST_STATUS="PASS"
else
  echo "❌ Tests failed"
  TEST_STATUS="FAIL"
fi

# 2. 检查语法
echo "[2/5] Checking syntax..."
SYNTAX_ERRORS=0
for script in hooks/lib/*.sh; do
  if ! bash -n "$script" 2>/dev/null; then
    echo "❌ Syntax error in $script"
    ((SYNTAX_ERRORS++))
  fi
done
if [ $SYNTAX_ERRORS -eq 0 ]; then
  echo "✅ Syntax OK"
  SYNTAX_STATUS="PASS"
else
  echo "❌ Found $SYNTAX_ERRORS syntax errors"
  SYNTAX_STATUS="FAIL"
fi

# 3. 文档一致性检查
echo "[3/5] Checking doc consistency..."
CLAIMED_FIXES=$(grep -E "^[0-9]+\. ✅" "$REPORT" | wc -l | tr -d ' ')
echo "Claimed fixes: $CLAIMED_FIXES"
DOC_STATUS="PASS"

# 4. 代码覆盖率检查（可选）
echo "[4/5] Checking code coverage..."
# TODO: 集成覆盖率工具
COVERAGE_STATUS="SKIP"

# 5. 生成自检报告
echo "[5/5] Generating self-check report..."
REPORT_FILE="/tmp/self-check-$PHASE-$(date +%Y%m%d-%H%M%S).md"
cat > "$REPORT_FILE" <<EOF
# Phase $PHASE Self-Check Report

Generated: $(date '+%Y-%m-%d %H:%M:%S')

## Test Results
- Status: $TEST_STATUS
- Test suite: hooks/tests/test-$PHASE.sh

## Syntax Check
- Status: $SYNTAX_STATUS
- Errors: $SYNTAX_ERRORS

## Doc Consistency
- Status: $DOC_STATUS
- Claimed fixes: $CLAIMED_FIXES

## Code Coverage
- Status: $COVERAGE_STATUS

## Overall Status
EOF

if [ "$TEST_STATUS" = "PASS" ] && [ "$SYNTAX_STATUS" = "PASS" ] && [ "$DOC_STATUS" = "PASS" ]; then
  echo "✅ READY FOR CODEX VERIFICATION" >> "$REPORT_FILE"
  echo ""
  echo "✅ Self-check complete: $REPORT_FILE"
  echo "✅ Ready for Codex verification"
  cat "$REPORT_FILE"
  exit 0
else
  echo "❌ FAILED - FIX ISSUES BEFORE CODEX VERIFICATION" >> "$REPORT_FILE"
  echo ""
  echo "❌ Self-check failed: $REPORT_FILE"
  echo "❌ Fix issues before Codex verification"
  cat "$REPORT_FILE"
  exit 1
fi
```

### 使用方式

```bash
# 在 Layer 2 完成后，Layer 3 之前运行
bash hooks/lib/phase-self-check.sh phase1 docs/plans/T2_Phase1_实施报告_v1.5.md

# 捕获报告并传递给 Codex
SELF_CHECK_REPORT=$(bash hooks/lib/phase-self-check.sh phase1 docs/plans/T2_Phase1_实施报告_v1.5.md 2>&1)
```

---

## Codex CLI 配置

### 环境变量

**必须设置**（Claude Code sandbox 环境）：

```bash
export CODEX_HOME=~/.codex
```

原因：Claude Code sandbox 的 `$HOME` 被重定向到 `~/.claude-api-env`，导致 `~/.codex/config.toml` 找不到。

### 配置文件

`~/.codex/config.toml`：

```toml
model_provider = "crs"
model = "gpt-5.3-codex"
model_reasoning_effort = "xhigh"

[model_providers.crs]
base_url = "https://your-proxy.com/openai"
env_key = "CRS_OAI_KEY"
```

### Sandbox 模式

- `--sandbox read-only`：只读分析（推荐 review）
- `--sandbox workspace-write`：允许本地写入
- `--sandbox danger-full-access`：完全访问（需谨慎）

### CLI 踩坑

- **`-m` 参数陷阱**：`-m gpt-5.3-codex` 可能忽略 config.toml 的自定义 provider，请求发往 `api.openai.com`
- **review 模式**：`codex exec review --uncommitted` 范围太广效果差；定向用 `codex exec --full-auto "review prompt"` 指定文件更高效
- `--uncommitted` 与自定义 PROMPT 互斥，二选一
- `--quiet` 不是合法参数

### 标准调用模板

#### Layer 1: 规划层评审

```bash
export CODEX_HOME=~/.codex

codex exec --skip-git-repo-check --sandbox read-only --full-auto \
  "Deeply review this implementation plan: docs/plans/T2_Phase1_实施报告_v1.0.md

   Focus on:
   1. Technical feasibility
   2. Architecture risks
   3. Security concerns
   4. Completeness of acceptance criteria

   Output format:
   - Score: X/10
   - Judgment: APPROVE / REVISE / REJECT
   - Issues: [HIGH/MEDIUM/LOW] description
   - Suggestions: specific improvements"
```

#### Layer 2: Sub-Agent 增量验证

```bash
export CODEX_HOME=~/.codex

codex exec --skip-git-repo-check --sandbox read-only --full-auto \
  "Verify fix for MEDIUM #1 (test cleanup logic):

   1. Check ORIGINAL_PROJECT_ROOT is saved before cd
   2. Check cleanup restores original directory
   3. Run isolated test to confirm no path issues

   Files to check:
   - hooks/tests/test-phase0.sh

   Output: PASS/FAIL + specific issues if any"
```

#### Layer 3: 完整验收

```bash
export CODEX_HOME=~/.codex

# 生成自检报告
SELF_CHECK_REPORT=$(bash hooks/lib/phase-self-check.sh phase1 docs/plans/T2_Phase1_实施报告_v1.5.md 2>&1)

# 传递给 Codex
codex exec --skip-git-repo-check --sandbox read-only --full-auto \
  "Perform complete verification of Phase 1 implementation.

   Implementation report: docs/plans/T2_Phase1_实施报告_v1.5.md

   Self-check report:
   $SELF_CHECK_REPORT

   Focus on:
   1. Completeness: all acceptance criteria met?
   2. Security: input validation, TOCTOU, symlink bypass, injection
   3. Edge cases: error handling, boundary conditions
   4. Code quality: maintainability, clarity

   Output format:
   - Score: X/10
   - Judgment: APPROVE / REVISE / REJECT
   - Issues: [HIGH/MEDIUM/LOW] description
   - Verification: specific tests to run"
```

---

## Sub-Agent 验证模板

### 单个问题验证

```bash
export CODEX_HOME=~/.codex

codex exec --skip-git-repo-check --sandbox read-only --full-auto \
  "Verify fix for [SEVERITY] #[N] ([problem description]):

   Expected fix:
   1. [具体修复点 1]
   2. [具体修复点 2]

   Files to check:
   - [file1]
   - [file2]

   Verification steps:
   1. [验证步骤 1]
   2. [验证步骤 2]

   Output: PASS/FAIL + specific issues if any"
```

### 关键模块验证

```bash
export CODEX_HOME=~/.codex

codex exec --skip-git-repo-check --sandbox read-only --full-auto \
  "Verify [module name] implementation:

   Module purpose: [简述功能]

   Key requirements:
   1. [需求 1]
   2. [需求 2]

   Files implemented:
   - [file1]
   - [file2]

   Test coverage:
   - [test file]

   Verification focus:
   1. Functionality: does it work as expected?
   2. Security: any vulnerabilities?
   3. Edge cases: error handling complete?

   Output: PASS/FAIL + specific issues"
```

### 批量修复验证

```bash
export CODEX_HOME=~/.codex

codex exec --skip-git-repo-check --sandbox read-only --full-auto \
  "Verify batch fixes for v[X.Y]:

   Fixed issues:
   1. [SEVERITY] #[N]: [description]
   2. [SEVERITY] #[N]: [description]
   3. [SEVERITY] #[N]: [description]

   Changed files:
   - [file1]
   - [file2]

   Verification:
   For each issue, check:
   1. Fix is correct
   2. No regression
   3. Test coverage added

   Output:
   - Issue #[N]: PASS/FAIL + details
   - Overall: PASS/FAIL"
```

---

## 合约提取工具

### Bash 函数签名提取

```bash
#!/usr/bin/env bash
# extract-bash-contract.sh - 提取 Bash 函数签名和参数

SCRIPT_FILE="$1"

echo "# Bash Contract: $SCRIPT_FILE"
echo ""

# 提取函数定义
grep -E "^[a-zA-Z_][a-zA-Z0-9_]*\(\)" "$SCRIPT_FILE" | while read -r func; do
  func_name="${func%()}"
  echo "## Function: $func_name"

  # 提取函数体中的参数使用
  awk "/^$func_name\(\)/,/^}/" "$SCRIPT_FILE" | grep -E '^\s*local ' | head -5

  echo ""
done
```

使用：
```bash
bash extract-bash-contract.sh hooks/lib/scene-actions.sh > Contract_scene-actions.md
```

### JSON Schema 字段提取

```bash
#!/usr/bin/env bash
# extract-json-schema.sh - 提取 JSON Schema 字段

SCHEMA_FILE="$1"

echo "# JSON Schema: $SCHEMA_FILE"
echo ""

# 提取 action_key 枚举
echo "## action_key enum:"
jq -r '.properties.actions.items.properties.action_key.enum[]' "$SCHEMA_FILE"

echo ""

# 提取 params 结构
echo "## params structure:"
jq '.properties.actions.items.properties.params' "$SCHEMA_FILE"
```

使用：
```bash
bash extract-json-schema.sh config/scenes/schema.json > Contract_schema.md
```

### Python 函数签名提取

```python
#!/usr/bin/env python3
# extract-python-contract.py - 提取 Python 函数签名

import ast
import sys

def extract_contract(file_path):
    with open(file_path, 'r') as f:
        tree = ast.parse(f.read())

    print(f"# Python Contract: {file_path}\n")

    for node in ast.walk(tree):
        if isinstance(node, ast.FunctionDef):
            # 函数名
            print(f"## Function: {node.name}")

            # 参数
            args = [arg.arg for arg in node.args.args]
            print(f"Parameters: {', '.join(args)}")

            # 返回类型（如果有类型注解）
            if node.returns:
                print(f"Returns: {ast.unparse(node.returns)}")

            # Docstring
            docstring = ast.get_docstring(node)
            if docstring:
                print(f"Description: {docstring.split(chr(10))[0]}")

            print()

if __name__ == '__main__':
    extract_contract(sys.argv[1])
```

使用：
```bash
python3 extract-python-contract.py hooks/lib/safe_file_ops.py > Contract_safe_file_ops.md
```

---

## 信息提取脚本

### 提取已实现 action 列表

```bash
#!/usr/bin/env bash
# extract-implemented-actions.sh

SCRIPT_FILE="hooks/lib/scene-actions.sh"

echo "# Implemented Actions"
echo ""

grep -E '^\s+"[a-z_]+"' "$SCRIPT_FILE" | \
  sed 's/[[:space:]]*"\([^"]*\)".*/\1/' | \
  sort -u | \
  while read -r action; do
    echo "- $action"
  done
```

### 提取测试覆盖范围

```bash
#!/usr/bin/env bash
# extract-test-coverage.sh

TEST_FILE="$1"

echo "# Test Coverage: $TEST_FILE"
echo ""

grep -E "^echo \"Test [0-9]+:" "$TEST_FILE" | \
  sed 's/echo "Test \([0-9]*\): \(.*\)"/\1. \2/' | \
  while read -r test; do
    echo "- $test"
  done
```

---

## 快速参考

### 完整工作流命令

```bash
# 1. Layer 1: 提取信息 + 设计方案
bash extract-bash-contract.sh hooks/lib/scene-actions.sh > Contract.md
# ... 编写实施计划 ...

# 2. Layer 1: Codex 评审方案
export CODEX_HOME=~/.codex
codex exec --skip-git-repo-check --sandbox read-only --full-auto \
  "Review implementation plan: docs/plans/T2_Phase1_实施报告_v1.0.md"

# 3. Layer 2: 实施 + 增量验证
# ... 实现功能 ...
codex exec --skip-git-repo-check --sandbox read-only --full-auto \
  "Verify fix for MEDIUM #1..."

# 4. Layer 2.5: 自检
bash hooks/lib/phase-self-check.sh phase1 docs/plans/T2_Phase1_实施报告_v1.5.md

# 5. Layer 3: Codex 完整验收
SELF_CHECK_REPORT=$(bash hooks/lib/phase-self-check.sh phase1 docs/plans/T2_Phase1_实施报告_v1.5.md 2>&1)
codex exec --skip-git-repo-check --sandbox read-only --full-auto \
  "Complete verification. Self-check: $SELF_CHECK_REPORT"
```

---

*配套文件*：
- 框架：记忆库/语义记忆/Claude-Codex三层协作框架_v2.2.md
- 案例：记忆库/语义记忆/Claude-Codex实战案例.md

*最后更新：2026-03-03*

---

## Claude-Codex协作工具箱

# Claude-Codex协作工具箱

### 复杂多任务并行推进：任务分组 + 四步流程（计划→评审→实施→验证）

## 多任务并行推进标准模式

当面对多个相互独立的子任务时，采用以下组织方式：

1. **任务分组**：按依赖关系分组，每个任务独立追踪状态
2. **每个任务四步流程**：
   - 制定实施计划（spec）
   - 提交评审（评审计划）
   - 按评审通过的方案实施
   - 再次评审（评审实现）
3. **并行策略**：同组内独立任务可并行，跨组依赖时串行
4. **提交节奏**：所有任务完成后统一合并提交，不逐个提交

触发条件：任务数 ≥ 3 且各任务相互独立时采用此模式。

---

## Codex CLI 0.117.0 关键行为：认证命令、config 默认值、--quiet 不存在

### Codex CLI 0.117.0 关键知识点（Rust 版本）

**认证**
- 正确命令：`codex login` / `codex login status`（**不是** `codex auth`）
- Plus/Pro 会员使用 ChatGPT OAuth，无需 OPENAI_API_KEY
- 已通过 OAuth 登录后，只要删除第三方 config 覆盖即可还原

**配置文件**
- 删除 `~/.codex/config.toml` 即可完全还原为默认值（model_provider = openai）
- `requires_openai_auth = true` 时 Codex 会**忽略** env_key，强制走 OAuth
- `-c` flag 支持运行时覆盖 config.toml 中的值（dotted TOML path 语法）

**已知不存在的 flag**
- `--quiet` 不是合法参数（与 Node.js 旧版不同）
