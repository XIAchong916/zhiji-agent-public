# Zhiji Agent · 知几

[English](README.md) | [中文](README.zh-CN.md)

> A governed recursive diagnosis and decision-simulation system for enterprise AI transformation.<br>
> 一套面向企业 AI 转型决策的受治理递归诊断与方案推演系统。

**知几：见微知机，因势成解。**

知几 Agent 试图把企业 AI 转型中原本高度依赖专家经验、访谈组织和多轮讨论的判断过程，转化为一套**结构化、可持续推进、可复核的人机协同决策流程**。

它从真实业务问题出发，通过递归诊断业务状态、变化机制和可干预变量，将宽泛的转型问题逐步收敛到具体场景与能力方案，并通过 Evidence、Human Gate、Deterministic Workflow 与持久化状态约束 Agent 的语义权威。

```text
Business Diagnosis
        ↓
Scenario Diagnosis
        ↓
Solution Design
```

当前项目已经实现可持久化、可恢复、具备 Human Gate 和完整 lineage 的端到端 Product Loop；真实 OpenAI-compatible LLM Provider 已进入正式代码路径，Agent 已能够生成受限、可审查的 Semantic Proposal，并在 USER Gate 停止等待确认。

当前仍**不**宣称：

- Agent 已能够自主完成高质量企业诊断；
- 真实企业诊断效果已经验证；
- 推荐方案一定产生预期经营结果；
- 系统已经 production-ready；
- 系统拥有现实业务执行权限。

`Recursive Diagnosis` · `Human-in-the-loop` · `Evidence & State` · `Governed Semantic Proposal`

---

## 1. Why Zhiji · 为什么需要知几

企业开始 AI 转型时，问题往往首先被表达成一个技术需求：

> “做一个知识库。”<br>
> “做一个客服 Agent。”<br>
> “接入大模型。”<br>
> “用 AI 提升某个环节的效率。”

但技术方案本身不能回答更前置的问题：

**企业当前真正需要改变的是什么？<br>
问题产生在哪个业务层级和环节？<br>
哪些变量真正决定业务结果？<br>
哪些变量可以被改变？<br>
AI 是否是合适的干预手段？<br>
在成本、能力和约束条件下，这项投入是否值得？**

因此，知几的基本判断是：

> **企业 AI 转型首先是一个业务决策问题，其次才是一个技术实现问题。**

知几想解决的不是“如何让 AI 给出一个更好的答案”，而是：

> **如何把复杂的企业 AI 转型判断，转化为一个可以被系统持续执行、确认、复核和验证的决策过程。**

为此，知几尝试完成三个转变：

- **业务模型化**：显式表达业务对象、状态、目标、约束、能力与变化机制；
- **分析可执行化**：把从宏观问题到具体场景和方案的分析组织成可递归推进的流程；
- **决策可信化**：把 AI 的概率性判断与 Evidence、State、Human Gate 和后续 Measurement 分开治理。

### Intended Value · 目标价值

- **降低前置判断成本**：把原本高度依赖人工组织和专家经验的部分分析过程，转化为可重复执行的诊断与推演流程；
- **减少直接试错风险**：在投入资源之前显式暴露变化机制、干预路径、关键假设和潜在影响；
- **提高结论可复核性**：让判断建立在结构化模型、Evidence、状态记录和 Human Gate 上，而不是只依赖一次模型回答或专家个人信用。

> 以上是知几希望验证的产品价值，而不是已经完成真实企业验证的效果结论。

### What Zhiji Is Not · 知几不是什么

- **不是通用聊天机器人**：Agent 输出首先是 Proposal / Hypothesis，不直接成为业务事实；
- **不是一次性报告生成器**：最终 Output 由 Journey 中已经持久化和确认的状态派生；
- **不是自动化执行平台**：Confirm / Select / Approve 表示分析路径或方案被接受，不代表系统获得现实执行权限。

### Design Transparency

当前 Source Repository 保持私有。

本 Public Showcase Repo 公开：

- Domain / Method；
- Human–AI Governance；
- Architecture；
- Implementation Status；
- Engineering Evidence；
- Demo artifacts；
- Known Limitations；
- Architecture RFC。

**Open Design / Closed Source**

本仓库强调：

> **Design Reviewability ≠ Source-level Auditability**

---

## 2. METHOD · 核心模型与递归诊断

知几的底层分析框架来自一个基本判断：

> **业务问题可以被理解为：某个 Object 为什么在当前 System 中处于当前位置，以及如何在现实约束下推动它向目标位置迁移。**

### Core Model

```text
System
  ↓
Object
  ↓
Position
  ↓
InternalState = {R, θ, D, Ω}
  ↓
ChangeLaw
  ↓
Intervention
  ↓
New Position / Projection
```

其中：

- **R — Objectives / References**：目标与评价参照；
- **θ — Constraints**：边界与约束；
- **D — Capabilities**：当前能力与资源；
- **Ω — Feasible Option Space**：当前现实可行的选择空间。

知几不是先寻找“可以用什么 AI 技术”，再倒推业务场景，而是先回答：

```text
什么机制影响业务结果
        ↓
哪些变量可以被改变
        ↓
什么 Intervention 在现实中可行
        ↓
AI 是否适合成为实现这种干预的能力
```

### Recursive Diagnosis

现实企业不是一个单层系统。

知几在五个连续尺度上复用同一套 Core Model：

```text
Industry
   ↓
Enterprise
   ↓
Business
   ↓
Scenario
   ↓
Solution Capability
```

递归关系的核心是：

```text
Layer(n).Object = Layer(n+1).System
```

即：

> **上一层需要继续深入的 Object，在下一层打开内部结构后，成为新的 System。**

每深入一层，分析边界进一步缩小：

> **上一层确定方向，下一层打开内部结构。**

向下通过 Confirmed `StatePackage` 继承已经确认的目标、约束、状态与 Evidence；向上传递的是 Candidate Impact，而不是直接覆盖上层已确认状态。

![Recursive diagnosis loop](assets/recursive-loop.svg)

### Three Agents · 三个 Agent

五层业务尺度没有被拆成五个独立 Agent。

知几使用三个职责稳定的 Agent：

| Agent | 覆盖层级 | 核心任务 |
|---|---|---|
| **Business Diagnosis Agent** | Industry → Enterprise → Business | 定位业务问题、逐层收敛、形成 Candidate Scenarios |
| **Scenario Diagnosis Agent** | Scenario | 因果定位、价值验证、识别关键变量与 ChangeLaw |
| **Solution Design Agent** | Solution Capability | 将已确认干预方向转化为能力组合、Solution、Projection 与 Measurement Plan |

这里区分两个维度：

> **五层结构解决“问题分析到什么业务尺度”；三个 Agent 解决“哪一类认知任务由什么稳定角色负责”。**

三个 Agent 组成的是一条连续认知链，而不是三个彼此独立的聊天机器人。

[Read METHOD →](docs/METHOD.md)

---

## 3. GOVERNANCE · Agent 如何获得有限而明确的权威

知几并不把 Agent 输出直接当作业务事实或最终决策。

复杂业务分析链条长、层级多，而 LLM 本质上仍是概率性系统。单步误差如果未经治理进入下一阶段，可能在长链分析中持续放大。

因此，知几把 **Agent、Human、Workflow** 分成三个不同责任主体：

```text
Agent
→ Analysis / Proposal / Hypothesis / Projection

Human
→ Fact / Constraint / Confirmation / Selection / Decision

Workflow
→ Legal State Transition / Gate / Recovery
```

### Core Boundaries · 核心边界

```text
Evidence ≠ Hypothesis

Proposal ≠ Accepted State

Human Approval ≠ Causal Proof

Projection ≠ Actual Outcome

Selection ≠ Execution Authorization
```

一个 Agent 判断要成为后续分析允许依赖的状态，需要经过治理边界：

```text
Evidence / Confirmed State
          ↓
Agent Analysis
          ↓
Proposal / Hypothesis
          ↓
Structural Validation
+ Evidence Binding
+ Rule Validation
          ↓
Human Gate（如需要）
          ↓
Accepted Domain State
          ↓
Deterministic Workflow
```

![Human-gated state transition](assets/human-gated-state-transition.svg)

### Real Semantic Proposal · 当前真实模型路径

当前真实语义生成路径已经进入正式代码：

```text
Confirmed Context / Evidence
        ↓
Real LLM Provider
        ↓
Untrusted Raw Semantic Output
        ↓
Strict Typed Validation
        ↓
HYPOTHESIS / AGENT Proposal
        ↓
USER Gate
```

Agent 当前可生成五类受限 Semantic Proposal：

```text
CURRENT_POSITION
TARGET_POSITION
CHANGE_LAWS
INTERVENTIONS
PROJECTION
```

这些 Proposal 不会因为来自真实模型就自动变成 Accepted State。

同时：

- `R / θ / D / Ω` 保持 USER-owned；
- Agent 不允许静默改写 USER-owned InternalState；
- 不合法、悬空、过期或不符合 Contract 的输出 fail closed；
- Scenario / Solution Candidate 的 Agent authorship 仍未开放。

[Read GOVERNANCE →](docs/GOVERNANCE.md)

---

## 4. ARCHITECTURE · METHOD + GOVERNANCE 如何被软件保证

知几不是把一组 Prompt 串成一段长对话，而是把**业务状态、语义推理、确定性计算和流程控制拆成不同软件层**。

核心原则是：

> **概率性的语义判断交给 Agent，确定性的状态、计算与流程交给软件系统。**

```text
Interaction
    ↓
Application
    ↓
Workflow
    ↓
Diagnosis Engine
    ↓
Domain

Infrastructure
通过 Ports / Adapters 接入
```

### Architecture Responsibilities

**Interaction**<br>
REST API、CLI 与结构化结果输出。

**Application**<br>
组织 Case Intake、Journey progression、三个 Agent、Semantic Generation、Confirm / Select 与 Product Output。

**Workflow**<br>
管理 Layer、State、合法 Action、Human Gate、异常状态、暂停 / 恢复；不依赖 LLM 记忆“当前走到哪里”。

**Diagnosis Engine**<br>
承载 Evidence Coverage、Diagnosis / Execution Readiness、Intervention Feasibility、Business Gap、Outcome Delta、Scenario Constraint Evaluation 等确定性算子。

**Domain**<br>
定义 `System / Object / Position / InternalState / ChangeLaw / Intervention / Projection / Evidence` 等核心业务语义与生命周期。

**Infrastructure**<br>
通过 Ports / Adapters 接入 PostgreSQL、迁移、Repository / UoW、真实 / Fake LLM Provider、trace 与 recovery。

当前真实模型只能通过受控接口进入系统：

```text
Context + Evidence
        ↓
LLM
        ↓
Untrusted Proposal
        ↓
Validation
        ↓
Human Gate
        ↓
Accepted State
```

因此：

> **Conversation Context 不是系统状态本身。**

![System architecture](assets/system-architecture.svg)

[Read ARCHITECTURE →](docs/ARCHITECTURE.md)

---

## 5. STATUS · 当前真实做到哪里

项目使用四种实现状态：

- **implemented**：存在正式 production-path typed code；
- **fake-only**：该具体能力仍只有 Fake / deterministic seam；
- **contract-only**：已有正式 Contract / tests / boundary，但尚未进入 runtime；
- **deferred**：明确不属于当前已实现范围。

> **Implemented does not mean production-ready.**

### Capability Snapshot

| Capability | Status |
|---|---|
| Recursive Domain + Workflow | ✅ implemented |
| Business Diagnosis Journey | ✅ implemented |
| Scenario Diagnosis Journey | ✅ implemented |
| Solution Design Journey | ✅ implemented |
| Evidence / Lineage | ✅ implemented |
| PostgreSQL Persistence / Restart / Recovery | ✅ implemented |
| Interactive Product Delivery Loop | ✅ implemented |
| Authentication / Resource Authorization | ✅ implemented |
| Outcome Evaluation | ✅ implemented |
| Automated CI Quality Gates | ✅ implemented |
| Real OpenAI-compatible LLM / Provider | ✅ implemented |
| Agent-authored five-family Semantic Proposal | ✅ implemented |
| Agent-authored Scenario Candidate semantics | ⚪ deferred |
| Agent-authored Solution Candidate semantics | ⚪ deferred |
| Industry / Decision / Collaboration / Experience / World Model enhancement | ⚪ contract-only |
| Final Web Frontend | ⚪ deferred |
| Autonomous external execution | ⚪ deferred |
| Production deployment / observability | ⚪ deferred |

当前最重要的变化是：

```text
Real LLM Provider
        ↓
Agent Semantic Proposal
        ↓
Strict Validation
        ↓
Human Gate
```

已经不再只是规划中的能力，而是真实存在的受治理运行路径。

但：

```text
Real Semantic Proposal
≠
Autonomous Enterprise Diagnosis
```

### Current Known Limitation

Current Position 仍存在一个明确交互边界：

```text
Agent drafts Position
        ↓
USER supplies / corrects InternalState
        ↓
Merged confirmation
```

这条合并确认路径尚未完成，因此当前公开 Demo 的 Current Position 仍由 USER 完整输入。

[Read detailed STATUS →](docs/STATUS.md)

---

## 6. DEMO · 真实模型如何进入 Product Loop

当前 Public Demo 已经不只是 deterministic Engineering Demo。

它使用虚构公司案例，通过真实模型生成受限 Semantic Proposal，并走完整个受控 Product Loop：

```text
Case Intake
     ↓
Case Assessment
     ↓
Human Decision
     ↓
Business Diagnosis
     ↓
Agent Semantic Proposal
     ↓
Human Gate
     ↓
Scenario Diagnosis
     ↓
Solution Design
     ↓
Solution Selection
     ↓
Case Completion
     ↓
PRODUCT_OUTPUT_READY
```

最终 Output 明确保留：

```text
No execution: true
No authorization: true
```

### Recommended Viewing

- **Public Cut**：约 2 分 53 秒，用于快速理解真实模型 Proposal → Human Gate → 跨层诊断 → Solution → `PRODUCT_OUTPUT_READY`；
- **Engineering Demo**：4 分 40 秒，保留更完整的运行过程；
- **Raw `.cast` + Prompt / Response trace**：用于核验原始运行与人机交互事实。

[Watch / inspect the Demo →](demo/README.md)

### What the Demo Proves

当前 Demo 支持以下工程结论：

- 一个 Business Case 可以进入正式 Product Loop；
- Business Diagnosis → Scenario Diagnosis → Solution Design 可以连续衔接；
- Human Gate 可以控制关键状态迁移；
- Evidence 与 lineage 可以跨阶段保留；
- Journey 可以持久化并恢复；
- Product Output 可以从持久化状态重新生成；
- 真实 Provider 可以生成受限、schema-valid 的 Semantic Proposal；
- Agent Proposal 可以以 `HYPOTHESIS` 身份停在 USER Gate 等待确认；
- Solution Selection 不会自动获得现实执行权限。

### What the Demo Does Not Prove

它不证明：

- Agent 已能自主完成高质量企业诊断；
- 真实企业诊断质量已经验证；
- 不同行业已经具有稳定泛化能力；
- ChangeLaw 已被因果证明；
- Projection 等于 Actual Outcome；
- 推荐方案一定产生预期 ROI；
- 系统已经 production-ready。

> 当前 Demo 证明的是：**真实模型可以进入治理骨架并产生受限、可审查的非权威 Proposal。**

### Demo Snapshot vs. Source Main

Public Demo 为了可读性包含 Layer / Step / Actor 等展示信息。

当前 Source Repository 的正式状态分类仍以 `main` 为准；未合并的纯展示层改动不改变 Domain、Workflow、Authority 或能力状态判断。

---

## 7. EVIDENCE · 有什么工程事实支持这些主张

当前 Public Evidence Snapshot 的 Source Repository 主线锚点是：

```text
main commit: 49ec33c
date: 2026-09-24
```

该提交为 PR #58 合并后的 `main`。

对应相同 Git tree 的 GitHub Actions 验证结果：

| Verification | Result |
|---|---|
| Test collection | `2945` items |
| pytest | `2860 passed, 85 skipped` |
| strict mypy | `174` source files, zero issues |
| Ruff check | all checks passed |
| Ruff format | `655` Python files already formatted |
| PostgreSQL gate | `83 passed` × 2 |
| Python test files | `426` |

GitHub Actions 两个主要 Job：

```text
deterministic   PASS
postgres        PASS
```

这些证据用于证明：

> **当前公开描述的工程闭环来自实际运行，而不是纯架构设想。**

但由于 Source Repository 仍然保持私有：

> **Engineering Evidence ≠ Source-level Audit**

[See evidence snapshots →](evidence/)

---

## 8. Open Questions · 希望被挑战的问题

知几目前最希望获得的外部反馈，不是：

> “这个架构看起来不错。”

而是：

> **它在真实 Agent 和真实企业进入以后，最可能在哪个核心假设上失败？**

当前最值得挑战的六类问题：

1. **Semantic Authority**<br>
   Agent 应拥有多大的语义生成权？哪些信息可以推断，哪些必须由 Human 提供或确认？

2. **Human Gate Policy**<br>
   Gate 应主要按照 Workflow State 设置，还是应逐步引入 Semantic Risk、Evidence Strength、Reversibility 与 Business Impact？

3. **Recursive Abstraction**<br>
   `Layer(n).Object = Layer(n+1).System` 是否真正具有跨业务通用性，还是存在 over-abstraction？

4. **Epistemic State**<br>
   Evidence、Hypothesis、Human Acceptance、Measurement 和 Actual Outcome 之间，是否需要更完整的可信状态模型？

5. **Model Context vs. Audit Trace**<br>
   如何让模型获得足够丰富的企业上下文，同时避免把敏感原文永久写入 Trace，又保持分析过程可审计？

6. **Impact Propagation**<br>
   局部 Intervention 的影响应该如何向 Business / Enterprise 层传播？什么时候局部改善足以被认为产生更高层业务价值？

[Join the architecture review →](rfcs/RFC-001-ARCHITECTURE-REVIEW.md)

---

## 9. Next Validation · 下一步验证什么

项目当前已经不再处于：

```text
Engineering Loop
        ↓
等待 Real Semantic Intelligence 接入
```

而更接近：

```text
Governed Engineering System
        +
Real Semantic Proposal Path
        ↓
等待真实业务质量验证
```

下一阶段建议沿下面顺序推进：

```text
Semantic Quality & Interaction Hardening
        ↓
External Architecture Review
        ↓
Real Enterprise Case
        ↓
Measurement & Feedback
```

### Semantic Quality & Interaction Hardening

重点验证：

- Agent Proposal 在真实业务材料下是否稳定、准确、有信息增量；
- Current Position 的 Agent Position + USER InternalState 如何合并确认；
- 哪些 Human Gate 真正带来修正，哪些开始形成 Approval Fatigue；
- Scenario / Solution Candidate 是否应该开放受限 Agent authorship；
- Authority 是否应根据 Evidence Strength / Reversibility / Business Impact 动态调整。

### External Architecture Review

主动挑战 Recursive Diagnosis、Authority、Human Gate、Epistemic State 与 Impact Propagation。

### Real Enterprise Case

第一次真正回答：

> **知几是否不仅是一套成立的软件架构，也是一套有现实价值的企业决策工具？**

### Measurement & Feedback

如果方案进入真实实施，再建立：

```text
Projection
    ↓
Implementation
    ↓
Measurement
    ↓
Actual Outcome
    ↓
Attribution
    ↓
Method / Knowledge Update
```

---

## 10. DevFlow · 知几又是怎样被 AI Native 地开发出来的

DevFlow 不是知几的业务功能，也不是已经产品化的通用研发平台。

它是一套实际支撑知几长期开发的个人 AI 软件工程控制系统，目标是把：

> **依赖聊天上下文的连续协作**

转化为：

> **由显式任务、状态、合同与工程证据约束的执行系统。**

角色分工：

```text
Human
→ 目标 / 边界 / 风险 / 最终验收

ChatGPT
→ 分析 / 规划 / 审查 / 问题诊断

Codex
→ 代码实现 / 测试 / 修复

DevFlow
→ 任务编排 / 状态控制 / 证据归档

Terminal / CI
→ 本地执行 / 自动验证

Git / GitHub
→ Branch / Commit / PR / Merge 等版本事实
```

核心控制对象：

```text
Planning Artifact
Manifest
State
Task Contract
Artifact
```

DevFlow 的关键原则是：

> **方向由 Human 给出，分析与执行由 AI 承担，过程由 DevFlow 控制，结果由工程证据与 Human 共同确认。**

[Read DEVFLOW →](docs/DEVFLOW.md)

---

## 11. Explore · 深入阅读

### Project Documents

- [METHOD · 方法与递归诊断](docs/METHOD.md)
- [GOVERNANCE · Human–AI 权威边界](docs/GOVERNANCE.md)
- [ARCHITECTURE · 软件实现](docs/ARCHITECTURE.md)
- [STATUS · 当前实现状态与验证边界](docs/STATUS.md)
- [DEVFLOW · AI Native 软件工程控制](docs/DEVFLOW.md)

### Architecture Review

- [RFC-001 · Architecture Review](rfcs/RFC-001-ARCHITECTURE-REVIEW.md)

### Demo & Evidence

- [Demo](demo/README.md)
- [Evidence](evidence/)

### Suggested Reading Path

```text
README
这是什么，为什么值得继续看
        ↓
METHOD
它到底怎么分析业务问题
        ↓
GOVERNANCE
AI 为什么不会直接成为“真理”
        ↓
ARCHITECTURE
这些原则怎么被软件保证
        ↓
STATUS
现在真实做到哪里
        ↓
DEMO
真实模型实际怎么运行
        ↓
EVIDENCE
有什么工程事实支持
        ↓
RFC
哪些核心假设最值得被挑战
```

---

## Reproducibility

当前 Source Repository 保持私有，因此 Public Showcase Repo 暂不提供源码级 Quick Start。

本仓库提供：

- Open Design；
- 脱敏 Engineering Evidence；
- Public / Engineering Demo；
- Raw terminal / Prompt audit artifacts；
- Known Limitations；
- Architecture RFC。

代码或可运行 Package 对外开放后，再提供真正的外部 Reproducible Run。

---

## License

Public Showcase Repo 的许可证在正式发布前确定。

除明确授权内容外，不应将本仓库的公开设计文档理解为对 Private Source Repository 源代码的开放授权。

---

> **Zhiji Agent 仍然是一个持续验证中的系统。**
>
> 它不预设当前架构就是最终答案。
>
> 这个项目真正想验证的是：
>
> **能否把复杂企业决策中原本依赖经验、讨论和隐性认知的部分，逐步变成可以表达、推演、确认、验证和演化的软件系统。**
