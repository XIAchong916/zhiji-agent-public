# Demo Product Loop v3 · Run Summary

> 本文件记录一次**真实模型参与、PostgreSQL 持久化、Human Gate 受控推进**的知几 Product Loop 运行。<br>
> 它回答的是：**这一次 Demo 实际运行了什么、哪些内容由 Agent 生成、哪些由 Human 决定、最终产出了什么，以及这次运行能够证明什么。**

---

## 1. Run Identity · 运行身份

```text
Demo: Product Loop v3
Date: 2026-09-24
Runtime: local CLI
Case key: demo-vid-real3
Case: fictional · 启元科技
Business scenario: B2B 线索转化效率诊断
Semantic provider: real model · TokenHub hy3
Persistence: PostgreSQL
Migration: Alembic head
Prompt / answer entries used: 242
Process exit code: 0
```

这次运行不是内存模拟，也不是预置一整套确定性答案后播放结果。

它使用：

```text
Human business input
        +
Real semantic model
        +
Deterministic Workflow / Engine
        +
PostgreSQL persistence
        +
Human Gate
```

共同完成一次完整受控诊断 Product Loop。

---

## 2. Case Boundary · 案例边界

本次案例使用一家**虚构公司：启元科技**。

诊断主题：

```text
B2B 线索转化效率诊断
```

案例描述围绕：

- 线索量增长；
- MQL → SQL 转化；
- 资质审核耗时；
- 人工资格审核成本；
- 在既有 CRM 与人工复核约束下寻找可行干预路径。

本案例中的企业、指标、Evidence 与经营背景均用于 Demo。

因此：

```text
Fictional Case
≠
Real Customer Case
```

也不能用本次结果证明真实企业经营改善。

---

## 3. Recording Provenance · 运行来源

本次公开证据包含：

```text
Raw terminal trace
Prompt / Response audit trace
Engineering Demo
Public Cut
```

对应关系：

```text
Public Cut
快速理解运行机制
        ↓
Engineering Demo
观察完整运行过程
        ↓
.cast + prompts
核验原始运行事实
```

Public Cut 与完整 Engineering Demo 来自同一次 v3 运行；Public Cut 只进行时间压缩与展示剪辑，不改变底层运行事实。

---

## 4. Run Flow · 本次运行的主要阶段

### 4.1 Case Intake

本次运行首先完成 Case 与诊断上下文录入。

共包含：

```text
Case key
Phase16 diagnosis inputs · 12
Module17 business-case inputs · 11
```

加上 Case key，共 24 项人工输入。

这些输入构成后续 Journey 的 Human-provided business context。

---

### 4.2 Controlled Entry

进入正式 Journey 前，先经过两道独立 Human Decision：

```text
Decision Proposal
PROCEED
        ↓
Independent Review
APPROVED
```

这一步用于证明：

> **“建议继续分析”与“正式允许进入 Journey”不是同一个动作。**

Agent 没有自行完成这两个 Gate。

---

### 4.3 Object Confirmation

OBJECT family 由 Human 提供并确认。

本次输入包括：

- Object；
- Object type；
- Business identity；
- Object objective summary。

这一部分属于明确的 Human Authority，而不是由模型自行创造业务身份。

---

### 4.4 Current Position

本次 Demo 的 Current Position **最终由 USER 完整输入**。

包括：

```text
Structural Position
Functional Position
Performance Position
Evolutionary Position
Benchmark
Confidence

R · Objectives
θ · Constraints
D · Capabilities
Ω · Feasible Options
```

原因不是“模型一定无法描述 Current Position”，而是当前 Authority / Interaction Contract 要求：

```text
InternalState {R, θ, D, Ω}
=
USER-owned
```

当前产品路径尚不能在同一次确认流程中完成：

```text
Agent drafts Position
        ↓
USER supplies / corrects InternalState
        ↓
Merged confirmation
```

因此，本次 Demo 没有把 Agent 起草的 Position 与 USER-owned InternalState 合并为一个正式 Current Position。

---

### 4.5 Target Position

Target Position 使用真实模型起草。

运行路径：

```text
Confirmed Context / Evidence
        ↓
Real Model
        ↓
TargetPositionSnapshot
        ↓
HYPOTHESIS / AGENT
        ↓
USER Gate
        ↓
Human Confirm
```

Agent 提供的是 Proposal，而不是 Accepted State。

---

### 4.6 ChangeLaw

ChangeLaw 由真实模型起草，再由 Human 确认。

其治理关系是：

```text
Evidence
        ↓
Agent causal hypothesis
        ↓
ChangeLaw Proposal
        ↓
Human Gate
```

Human Confirm 只代表：

> 当前 Journey 允许把该 ChangeLaw 作为后续分析假设继续使用。

它不代表：

```text
ChangeLaw
=
Causally Proven Law
```

---

### 4.7 Intervention

InterventionOption 同样由真实模型起草，并通过 Human Gate。

本次 Intervention 引用当前 Journey 中已经确认的 `ChangeLaw` ID。

因此模型不能任意编造一个无法落到现有 Journey 的目标机制。

关系为：

```text
Confirmed ChangeLaw
        ↓
Agent Intervention Draft
        ↓
Identifier / Schema Validation
        ↓
HYPOTHESIS
        ↓
USER Gate
```

---

### 4.8 Solution Selection

Solution 阶段呈现两个候选方案。

Human 执行：

```text
SELECT
        ↓
Candidate #1
        ↓
Selection rationale
```

需要特别说明：

> **本次 Solution candidates 由确定性装配路径产生，不是 RealLLMProposalAdapter 生成。**

因此，本次 Demo 不支持：

```text
Agent-authored Solution Candidate
=
implemented
```

Human 还填写了明确的 Selection rationale。

---

### 4.9 Constraint Review

选择 Solution 后，对两条 Hard Constraint 分别进行了确认。

每条约束都包含：

```text
constraint-satisfied
+
constraint-rationale
```

这保证 Solution 被选择前，不只是“看起来不错”，而是显式回到已确认约束进行核对。

---

### 4.10 Final Output

本次运行最终正常结束：

```text
exit code: 0
```

最终进入：

```text
PRODUCT_OUTPUT_READY
```

并形成：

```text
Business Diagnosis Package
Scenario Diagnosis Package
Solution Package
Complete lineage
```

最终 Output 同时明确保留：

```text
No execution: true
No authorization: true
```

因此：

> **完成诊断、方案设计与 Solution Selection，不等于系统获得现实业务执行权限。**

---

## 5. Human / Agent / System Attribution · 本次运行中谁负责什么

### Human

本次 Human 负责：

- Case / business context；
- Object identity；
- Current Position 与 `R / θ / D / Ω`；
- `PROCEED`；
- Independent `APPROVED` review；
- 所有关键 Semantic Gate 的 Confirm；
- Solution `SELECT`；
- Selection rationale；
- Hard Constraint review。

### Agent / Real Model

本次真实模型明确参与：

- Target Position drafting；
- ChangeLaw drafting；
- Intervention drafting；
- 受控 Semantic Proposal Generation。

这些内容都以 Proposal / HYPOTHESIS 身份进入系统。

### Deterministic System

Workflow / Application / Engine / Persistence 负责：

- 当前 Stage；
- 合法 Action；
- Proposal staging；
- Human Gate enforcement；
- identifier / schema boundary；
- Layer / Journey progression；
- persistence；
- lineage；
- recovery；
- final Product Output assembly。

因此本次运行体现的是：

```text
Agent
负责提出候选认知

Human
负责现实权威与关键选择

System
负责确定性治理与状态推进
```

而不是：

```text
LLM
=
System Controller
```

---

## 6. What This Run Proves · 本次运行能够证明什么

本次 Demo 可以作为证据支持：

- 一个 Case 可以进入完整 Product Loop；
- Business Diagnosis → Scenario Diagnosis → Solution Design 能够连续推进；
- PostgreSQL 可以承载本次运行状态，而非依赖一次对话上下文；
- Human Gate 可以实际阻止 Agent Proposal 自动成为正式状态；
- 真实模型可以在既定治理边界内产生 Semantic Proposal；
- Target Position / ChangeLaw / Intervention 可以走 `Agent Draft → USER Gate → Confirm` 路径；
- USER-owned InternalState 没有被 Agent 静默覆盖；
- Solution Candidate 可以被显式 Select，并保留 Selection rationale；
- Hard Constraint 可以在方案确认前被逐项复核；
- 最终可以得到 `PRODUCT_OUTPUT_READY`；
- 最终结果保留 Business / Scenario / Solution Package 与 lineage；
- 本次 Process 正常退出：`exit code 0`；
- Solution Selection 没有被解释成 Execution Authorization。

最重要的证据增量是：

```text
Real Model
        ↓
Semantic Proposal
        ↓
Governance Boundary
        ↓
Human Gate
        ↓
Product Loop
```

已经出现在同一次实际运行中。

---

## 7. What This Run Does Not Prove · 本次运行不能证明什么

本次 Demo **不能**证明：

```text
Agent can autonomously diagnose enterprises             ✗
Real enterprise diagnosis quality is validated          ✗
Cross-industry generalization is validated              ✗
Agent-authored Scenario candidate is implemented        ✗
Agent-authored Solution candidate is implemented        ✗
ChangeLaw is causally proven                            ✗
Projection equals Actual Outcome                        ✗
Selected solution produces expected ROI                 ✗
System is production-ready                              ✗
System has autonomous execution authority               ✗
```

尤其应保持：

```text
Real Semantic Proposal
≠
Autonomous Diagnosis

Human Confirmation
≠
Causal Proof

Engineering Demo
≠
Enterprise Outcome Validation

Selection
≠
Execution Authorization
```

---

## 8. Known Limitation · 本次 Demo 暴露出的明确限制

当前最清晰的交互限制是 Current Position。

理想路径应为：

```text
Agent drafts Position
        ↓
USER reviews / edits
        ↓
USER supplies R / θ / D / Ω
        ↓
Merged Current Position
        ↓
Confirm
```

当前路径尚不能完成这一合并。

因此本次运行采用：

```text
USER provides Current Position
+
USER provides InternalState
```

完整进入后续流程。

这不是一个应该被隐藏的 Demo 瑕疵，而是当前 Product Interaction / Authority Contract 的真实边界。

---

## 9. Raw Evidence · 原始证据

### Terminal Trace

[zhiji_demo_product_loop_v3.cast](zhiji_demo_product_loop_v3.cast)

用于核验：

- 实际 CLI 运行顺序；
- Human 输入；
- Gate；
- Layer / Journey progression；
- 终态。

### Prompt / Response Audit Trace

[zhiji_demo_product_loop_v3.prompts.txt](zhiji_demo_product_loop_v3.prompts.txt)

用于核验：

- Prompt / answer sequence；
- Agent draft accept / decline path；
- Human Confirm / Select；
- Current Position manual input；
- Solution selection rationale；
- Constraint review；
- `exit code: 0`。

---

## 10. Relationship to Other Public Evidence · 与其他公开材料的关系

```text
run-summary.md
解释本次实际运行
        ↓
.cast / prompts
提供原始运行证据

verification-snapshot.md
证明代码树的确定性工程门

docs/STATUS.md
判断这些事实对应什么能力状态

demo/README.md
面向第一次观看者解释如何看 Demo
```

因此：

- 想**快速理解** → 看 `demo/README.md` + Public Cut；
- 想**完整观察** → 看 Engineering Demo；
- 想**核验这一次运行** → 看本文件 + `.cast` + `.prompts.txt`；
- 想**判断项目整体做到哪里** → 看 `docs/STATUS.md`。

[Back to Evidence Index →](../README.md)

[See Verification Snapshot →](../verification-snapshot.md)

[See Public Demo Guide →](../../demo/README.md)

---

> **这次运行的价值，不是证明 AI 已经可以替代企业决策者，而是证明真实模型已经能够进入一套受治理、可持久化、可审查的决策软件系统，并在明确的人机权限边界内参与实际 Product Loop。**
