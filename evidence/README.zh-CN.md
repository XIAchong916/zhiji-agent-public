# EVIDENCE · 工程证据索引

[English](README.md) | [中文](README.zh-CN.md)

> 本目录回答一个问题：<br>
> **Public Repo 中关于“已经实现、已经跑通、已经验证”的主张，有哪些实际工程事实可以支撑？**

这里保存的不是新的产品定义，也不是另一份 STATUS。

它只负责提供：

- 可核验的 Verification Snapshot；
- Demo 运行证据；
- 原始终端与 Prompt / Response 审计材料；
- 证据能够支持什么、不能支持什么的明确边界。

因此：

```text
docs/
定义与解释
        ↓
STATUS
声明当前做到哪里
        ↓
EVIDENCE
提供支持这些声明的工程事实
```

---

## 1. Evidence Principles · 证据原则

### 1.1 Evidence ≠ Claim

文档中的一句“已实现”本身不是证据。

工程主张应尽量能够落到：

```text
Commit / Git tree
CI result
Test result
Migration result
Runtime trace
Persisted output
Prompt / Response trace
```

中的一种或多种实际事实。

### 1.2 Evidence ≠ Source-level Audit

当前 Private Source Repository 保持私有。

因此本 Public Repo 可以提供：

- 运行结果；
- 测试与 CI 摘要；
- Demo 原始 trace；
- Prompt / Response 审计轨迹；
- 脱敏后的工程证据。

但不能因此声称外部 Reviewer 已经获得完整源码级审计能力。

```text
Engineering Evidence
≠
Source-level Audit
```

### 1.3 Demo Evidence ≠ Business Validation

一次完整 Demo 可以证明系统路径真实运行过，但不能证明：

```text
真实企业诊断质量已经验证
Projection 等于 Actual Outcome
ChangeLaw 已被因果证明
推荐方案一定产生预期 ROI
```

这些结论必须依赖后续真实企业 Case、Measurement 与 Actual Outcome。

---

## 2. Current Evidence Snapshot · 当前证据锚点

当前 Public Evidence Snapshot 以 Private Source Repository 的主线状态为锚点：

```text
repository: XIAchong916/zhiji-agent
branch: main
commit: 49ec33c
date: 2026-09-24
```

该提交对应 PR #58 合并后的 `main`。

PR #58 的 CI 运行在 merge tree `aad34a8` 上；该 merge tree 与最终 `main` commit `49ec33c` 使用相同 Git tree：

```text
tree: f3abf089a7689d48ca95a657ae82d8c2fb8b56f0
```

因此当前 Verification Snapshot 可以与这一 `main` 状态对应。

详细结果见：

[verification-snapshot.md](verification-snapshot.md)

---

## 3. Deterministic Verification · 确定性工程验证

当前对应 CI 的主要结果为：

| Verification | Result |
|---|---|
| Test collection | `2945` items |
| pytest | `2860 passed, 85 skipped` |
| strict mypy | `174` source files, zero issues |
| Ruff check | all checks passed |
| Ruff format | `655` Python files already formatted |
| PostgreSQL gate · Run 1 | `83 passed` |
| PostgreSQL gate · Run 2 | `83 passed` |
| PostgreSQL selected tests | `83 / 2945` |

GitHub Actions 的两个主要 Job 均通过：

```text
deterministic   PASS
postgres        PASS
```

这些结果主要支持：

- 当前代码能够通过 ordinary deterministic quality gate；
- 当前 PostgreSQL persistence / migration / integration 路径能够通过隔离验证；
- 当前类型检查与 Ruff gate 为 green；
- Public Repo 中关于工程闭环“不是纯架构设想”的主张具有实际运行依据。

它们**不**直接证明：

- 真实模型诊断质量；
- 跨行业泛化；
- 真实业务效果；
- production deployment readiness。

---

## 4. Demo Evidence · 真实 Product Loop 证据

本目录同时保存当前真实模型 Demo 的原始工程证据。

目录：

```text
demo-product-loop-v3/
├── run-summary.md
├── zhiji_demo_product_loop_v3.cast
└── zhiji_demo_product_loop_v3.prompts.txt
```

### 4.1 `run-summary.md`

用于说明：

- 本次 Demo 的运行条件；
- 使用的 Case 类型；
- Provider / Persistence / Human Gate 边界；
- 关键运行阶段；
- 最终 Output；
- 已知限制；
- 该运行能够证明什么、不能证明什么。

[Read the run summary →](demo-product-loop-v3/run-summary.md)

### 4.2 `.cast` · Raw Terminal Trace

`zhiji_demo_product_loop_v3.cast` 是 asciinema v2 原始终端录制。

它承担的是：

> **“实际终端中发生了什么？”**

而不是重新解释产品设计。

它可以用于核验：

- CLI 的真实运行顺序；
- Human 输入；
- Agent Proposal 出现位置；
- Human Gate；
- Layer / Journey 推进；
- 最终 `PRODUCT_OUTPUT_READY`。

[Raw terminal trace →](demo-product-loop-v3/zhiji_demo_product_loop_v3.cast)

### 4.3 `.prompts.txt` · Prompt / Response Audit Trace

`zhiji_demo_product_loop_v3.prompts.txt` 保存本次 Demo 的 Prompt / Response 审计轨迹。

它用于核验：

- 哪些阶段真正调用了语义生成；
- 哪些内容来自 Agent；
- 哪些动作由 Human Confirm / Select；
- Proposal 是否停在 USER Gate；
- 最终运行是否正常结束。

[Prompt / Response trace →](demo-product-loop-v3/zhiji_demo_product_loop_v3.prompts.txt)

> 公开版本只应保存已经完成脱敏和安全检查的审计材料，不应包含 API Key、Authorization Header、`.env` 内容、数据库凭据或不必要的本地绝对路径。

---

## 5. Demo Evidence vs. Public Video · 为什么两套都保留

Public Repo 中：

```text
demo/
```

负责：

> **让人看懂系统如何运行。**

而：

```text
evidence/demo-product-loop-v3/
```

负责：

> **让人核验这次运行确实发生过。**

两者关系是：

```text
Public Cut
快速理解
        ↓
Engineering Demo
完整观察
        ↓
.cast + prompts
原始审计
```

因此 Public Cut 可以剪辑，但不应成为唯一证据。

完整 Demo、Raw Terminal Trace 与 Prompt / Response Trace 共同承担底层核验作用。

[See the Demo →](../demo/README.md)

---

## 6. What Current Evidence Supports · 当前证据支持什么

结合当前 Verification Snapshot 与 Demo Evidence，可以支持以下工程层结论：

```text
Business Diagnosis
        ↓
Scenario Diagnosis
        ↓
Solution Design
```

能够在同一 Product Loop 中连续推进。

同时已经有证据支持：

- Workflow / State 可以持续推进；
- Human Gate 可以控制关键状态迁移；
- Evidence 与 lineage 可以跨阶段保留；
- Journey 可以持久化和恢复；
- Product Output 可以从持久化状态生成；
- PostgreSQL 路径可以运行并通过专项验证；
- 真实 OpenAI-compatible Provider 可以进入 Semantic Generation 路径；
- Agent 可以生成受限 Semantic Proposal；
- Agent Proposal 可以以 `HYPOTHESIS` 身份停在 USER Gate；
- `CURRENT_POSITION / TARGET_POSITION / CHANGE_LAWS / INTERVENTIONS / PROJECTION` 五类 Agent Semantic Proposal 已经过真实 Provider 路径验证；
- Solution Selection 不会自动产生现实执行权限；
- 最终 Output 明确保留：

```text
No execution: true
No authorization: true
```

---

## 7. What Current Evidence Does Not Support · 当前证据不支持什么

当前公开证据不能支持以下结论：

```text
Agent can autonomously diagnose enterprises             ✗
Real enterprise diagnosis quality is validated          ✗
Cross-industry generalization is validated              ✗
Agent-authored Scenario candidate is implemented        ✗
Agent-authored Solution candidate is implemented        ✗
ChangeLaw is causally proven                            ✗
Projection equals Actual Outcome                        ✗
Selected solution will produce expected ROI             ✗
System is production-ready                              ✗
System has autonomous execution authority               ✗
```

尤其需要保持：

```text
Real Semantic Proposal
≠
Autonomous Diagnosis

Engineering Verification
≠
Business Validation

Human Approval
≠
Causal Proof

Projection
≠
Actual Outcome

Selection
≠
Execution Authorization
```

---

## 8. Evidence Directory · 文件关系

```text
evidence/
│
├── README.md
│   └── 解释证据体系与阅读方式
│
├── verification-snapshot.md
│   └── 当前 main 对应的 pytest / mypy / Ruff / PostgreSQL / CI 证据
│
└── demo-product-loop-v3/
    │
    ├── run-summary.md
    │   └── 一次真实模型 Product Loop 的运行摘要与验证边界
    │
    ├── zhiji_demo_product_loop_v3.cast
    │   └── 原始终端运行轨迹
    │
    └── zhiji_demo_product_loop_v3.prompts.txt
        └── Prompt / Response 审计轨迹
```

---

## 9. Reading Order · 推荐阅读顺序

如果只是第一次了解项目：

```text
../README.md
        ↓
../demo/README.md
        ↓
verification-snapshot.md
```

如果希望进一步核验一次真实模型运行：

```text
demo-product-loop-v3/run-summary.md
        ↓
.cast
        ↓
.prompts.txt
```

如果希望判断“这些证据究竟意味着项目做到哪里”：

```text
Evidence
   ↓
../docs/STATUS.md
```

`STATUS.md` 负责能力判断，Evidence 负责事实支撑。

[Read STATUS →](../docs/STATUS.md)

---

> **Evidence 的目的不是让项目看起来“完成了”，而是让已经提出的工程主张拥有明确、可追踪的事实基础。**
