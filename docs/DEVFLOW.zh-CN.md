# DEVFLOW · AI Native 软件工程控制系统

[English](DEVFLOW.md) | [中文](DEVFLOW.zh-CN.md)

> 本文档回答一个核心问题：<br>
> **知几 Agent 是怎样在 Human、ChatGPT、Codex、Terminal / CI 与 Git / GitHub 共同参与的情况下，被持续、可控地开发出来的。**

DevFlow 不是知几 Agent 的业务功能，也不是一个已经产品化的通用开发平台。

它来源于知几 Agent 的真实开发过程：

> 当一个个人 AI 项目从少量代码逐渐演变为包含大量 Module、Phase、Task、状态、验证与修复的复杂软件系统后，问题不再只是“AI 能不能写代码”，而变成“如何让 AI 连续、可控、可恢复地完成复杂工程任务”。

因此，DevFlow 的定位是：

> **一套实际支撑知几 Agent 开发的个人 AI 软件工程控制系统。**

它试图解决的不是模型生成能力不足，而是 AI 深度参与软件开发之后出现的工程控制问题：

- Context Loss：跨会话后上下文和当前事实丢失；
- Scope Drift：长任务中逐渐偏离原任务边界；
- State Ambiguity：无法准确判断现在执行到哪里；
- False Completion：AI 说“完成了”，但没有足够工程证据；
- Repair Loop：一次修复可能引入新的问题；
- Recovery Difficulty：执行失败或会话切换后难以恢复可信现场。

DevFlow 的核心思路是：

> **把开发过程从“依赖聊天上下文的连续协作”，转化成“由显式任务、状态和工程证据约束的执行系统”。**

---

## 1. Role Separation · 为什么要拆分角色

DevFlow 不把“规划、编码、验证、验收、版本事实”全部交给一个 Agent。

它把开发参与方划分为六类职责单元：

```text
Human
  ↓
目标 / 边界 / 风险 / 最终验收

ChatGPT
  ↓
需求分析 / 方案规划 / 技术审查 / 问题诊断

Codex
  ↓
代码实现 / 测试 / 修复

DevFlow
  ↓
任务编排 / 状态控制 / 证据归档

Terminal / CI
  ↓
本地执行 / 自动化验证

Git / GitHub
  ↓
Branch / Commit / PR / Merge 等版本事实
```

这里的核心不是工具名称，而是**责任分离**。

可以概括为：

> **方向由 Human 给出，分析与执行由 AI 承担，过程由 DevFlow 控制，结果由工程证据与 Human 共同确认。**

---

### 1.1 Human · 最终目标与风险权威

Human 负责：

- 产品目标；
- 需求边界；
- 关键架构取舍；
- 风险判断；
- 是否接受重大 Scope 变化；
- 最终功能验收；
- Merge / destructive recovery 等高风险操作的最终授权。

Human 不需要亲自完成全部代码诊断，但必须保留：

```text
Final Product Authority
Final Risk Authority
Final Acceptance Authority
```

---

### 1.2 ChatGPT · 规划、分析与审查

ChatGPT 主要承担：

- 需求理解；
- 方案设计；
- 架构规划；
- Task 拆解；
- Artifact 审查；
- 失败分类；
- Root Cause 分析；
- Repair 方案设计；
- Resume / Stop / Replan 判断。

它更接近：

```text
Planner
+
Reviewer
+
Diagnosis Layer
```

但 ChatGPT 的自然语言判断本身不构成工程事实。

---

### 1.3 Codex · 受约束的执行者

Codex 负责：

- 读取正式 Task Contract；
- 检查真实 worktree；
- 在允许范围内修改代码；
- 运行 focused checks；
- 修复实现问题；
- 输出执行结果。

它不应自行：

- 扩大 Scope；
- 修改正式 Task 定义；
- 绕过失败测试；
- 为了“通过”而削弱验证；
- 宣布任务已经正式验收。

因此：

> **Codex 可以执行，但执行权限来自 Contract，而不是来自它自己的判断。**

---

### 1.4 DevFlow · 控制平面

DevFlow 自身不负责写业务代码，也不负责判断需求是否合理。

它负责控制：

```text
现在应该执行哪个 Task
Task 可以修改什么
执行后产生了什么 Artifact
验证是否通过
当前 Phase / Task 处于什么状态
失败后应该暂停在哪里
恢复时从哪些事实重新开始
```

因此它更像一个：

> **Development Control Plane**

而不是 Coding Agent。

---

## 2. Authoritative Facts · 不再让对话承担工程事实

DevFlow 最重要的变化，是把原本散落在聊天中的目标、任务、状态与结果，固化成明确的控制对象。

核心有五类。

---

### 2.1 Planning Artifact · 整体方向

记录：

- 需求分析；
- 架构方案；
- Phase / Module 规划；
- Task 拆解；
- 关键边界与验收思路。

它回答：

> **我们准备做什么，为什么这样做？**

---

### 2.2 Manifest · 正式任务身份

Manifest 定义：

- 正式 Phase；
- 正式 Task；
- Task ID；
- Task 顺序；
- 依赖关系；
- precompleted task 等运行信息。

它回答：

> **哪些 Task 真正属于这个 Phase？**

---

### 2.3 State · 当前执行位置

State 记录：

- 当前 Phase 状态；
- 当前 Task；
- completed tasks；
- failed / paused 位置；
- commit checkpoints；
- latest artifact reference。

它回答：

> **当前工程真实进行到哪里？**

典型 Phase 状态包括：

```text
READY
RUNNING
PAUSED
FAILED
COMPLETED
```

State 的职责是记录当前位置，而不是单独决定下一步应该做什么。

---

### 2.4 Task Contract · AI 的执行边界

Task Contract 规定当前 Task：

- 要实现什么；
- 可以修改什么；
- 禁止修改什么；
- Acceptance Criteria 是什么；
- 需要运行哪些 Verification；
- 什么情况下必须停止。

因此：

> **Task Contract 是单次 AI 执行边界的权威来源。**

一个 AI 即使“认为顺手多改一点更好”，也不能因此扩大正式 Scope。

---

### 2.5 Artifact · 实际发生了什么

Artifact 保存真实执行产生的：

- 代码执行结果；
- Verification；
- logs；
- failure information；
- acceptance information；
- session / attempt evidence。

它回答：

> **这一次执行到底发生了什么？**

DevFlow 的核心原则之一就是：

> **自然语言汇报不能替代 Artifact。**

---

## 3. Question-Specific Authority · 不同问题看不同事实源

DevFlow 不采用“某一个文件永远最高优先级”的简单模型。

更准确的方式是：

| 问题 | 权威来源 |
|---|---|
| 当前代码 / Branch / Commit 是什么？ | Git / GitHub |
| 哪些 Task 正式属于当前 Phase？ | Manifest |
| 当前执行到哪里？ | State，并与 Manifest 校验 |
| 当前 Task 可以改什么？ | Task Contract |
| 实现与验证有没有通过？ | Verification + Artifact |
| 是否需要 Human Review？ | Acceptance Artifact |
| 最终是否正式接受？ | Human Decision |
| 当前会话应该从哪里恢复？ | Git + Manifest + State + Contract + Artifact + Handoff |

因此：

```text
Skill
Handoff
AI Summary
```

都只是：

> **帮助读取和解释权威事实。**

它们不能覆盖 Git、State、Contract 或 Artifact。

---

## 4. Operating Modes · 为什么不是所有任务都用同一种执行方式

DevFlow 根据任务性质选择四种主要运行模式。

### 4.1 Planning First

适用于：

- 新 Module；
- 新 Phase；
- 实质性新能力；
- 目标或 Scope 尚未冻结的工作。

流程是：

```text
Requirement
    ↓
Analysis / Design
    ↓
Task Decomposition
    ↓
Human Confirmation
    ↓
Planning Artifact
    ↓
Manifest
    ↓
Task Contract
    ↓
Execution
```

原则是：

> **先定义，再执行。**

---

### 4.2 Continuous Phase

适用于：

> 已经完成规划、可以按照固定 Task Graph 连续推进的标准开发阶段。

Phase Runner 根据：

```text
Manifest
+
Dependencies
+
Current State
```

选择下一项 eligible Task。

Task Runner 再在 Task Contract 范围内完成一次受控执行。

---

### 4.3 Iterative Repair

适用于：

- 执行失败；
- Verification 失败；
- Acceptance 不通过；
- Semantic Review 发现实现偏离。

Repair 不重新设计整个系统，而是：

```text
Failure Artifact
      ↓
Root Cause
      ↓
Narrow Repair Task
      ↓
Fix
      ↓
Verification
      ↓
Review
```

核心原则：

> **基于证据定位真实原因，在原 Scope 内做最小范围修复。**

并且新的 Fix Artifact 不覆盖原失败 Artifact，从而保留完整问题历史。

---

### 4.4 Single Task

适用于：

- 独立评审；
- 治理修复；
- 文档任务；
- 最终验收；
- 不属于连续 Phase 的一次性工作。

虽然没有完整 Phase Runner，但仍然需要遵守：

```text
Contract
Evidence
Verification
Acceptance
```

---

## 5. Execution Loop · 一次任务如何真正完成

DevFlow 的主运行链可以概括为：

```text
Planning
    ↓
Manifest / Task Contract
    ↓
Task Execution
    ↓
Artifact
    ↓
Verification
    ↓
Decision
    ↓
Commit / State Update
    ↓
Next Task
```

展开后：

```text
Human / ChatGPT 定义目标与任务
          ↓
Manifest 确认任务身份
          ↓
State 确认当前位置
          ↓
Task Contract 限定执行边界
          ↓
Codex 执行代码修改
          ↓
Terminal / CI 运行确定性验证
          ↓
生成 Artifact
          ↓
Semantic / Human Review
          ↓
Accept / Repair / Replan / Stop
          ↓
Git checkpoint
          ↓
State 更新
          ↓
下一 Task
```

关键点是：

> **“AI 执行完”不是任务完成条件。**

任务真正完成需要：

```text
Execution Result
+
Deterministic Evidence
+
Semantic Acceptance
+
必要时 Human Decision
```

---

## 6. Quality Governance · 技术通过不等于任务通过

DevFlow 将验收拆成三个不同层次。

### 6.1 Deterministic Verification · 技术验证

负责判断：

> **实现是否满足基本工程条件。**

包括：

```text
git diff --check
ruff format
ruff lint
mypy
pytest
PostgreSQL verification
GitHub Actions
```

这些结果应该是：

- 可重复；
- 可量化；
- 可留证。

---

### 6.2 Semantic Acceptance · 语义验收

技术检查全部通过，仍不意味着任务一定正确。

还需要检查：

- 改的是不是正确对象；
- 是否真正满足 Task Contract；
- 是否发生 Scope Drift；
- 是否绕过原有 Architecture Contract；
- 是否通过修改测试 / 弱化断言制造“通过”；
- 是否解决 Root Cause 而不只是症状。

因此：

```text
Tests Pass
≠
Task Accepted
```

---

### 6.3 Human Decision · 最终判断

涉及以下情况时，最终决策仍由 Human 保留：

- 产品目标；
- Scope 变化；
- Architecture trade-off；
- 高风险操作；
- NEED_HUMAN；
- 最终 Acceptance；
- Merge。

所以：

> **Verification 提供事实，Semantic Review 提供判断，Human 提供最终责任。**

---

## 7. State & Recovery · 为什么可以跨会话继续开发

长周期 AI 开发的一个核心问题是：

> 一旦换聊天窗口，之前积累的“上下文”是不是就丢了？

DevFlow 的答案是：

> **聊天上下文不能作为恢复工程现场的唯一来源。**

恢复时重新读取：

```text
Git
 ↓
Manifest
 ↓
State
 ↓
Task Contract
 ↓
Artifact
 ↓
Handoff
```

重新确定：

- 当前代码版本；
- 当前正式任务；
- 已完成事项；
- 当前失败 / 暂停位置；
- 最新执行证据；
- 下一步允许做什么。

如果不同权威事实之间发生冲突：

> **进入人工确认，而不是让 AI 自动选择一个“看起来合理”的版本。**

同时：

> 正常 Context Recovery 只能读取并重建现有 State。

如果 State 本身需要被修改，就应该作为一个独立的 State Repair / Governance Task：

```text
explicit authorization
→ execution
→ verification
→ evidence
```

而不是在恢复过程中顺手改掉。

---

## 8. Why DevFlow Matters · 它真正解决的不是自动化，而是控制问题

DevFlow 最初可能看起来像：

> “让 AI 帮我自动写代码。”

但随着知几规模扩大，它实际解决的问题越来越接近：

```text
How to preserve intent?
How to constrain scope?
How to know current state?
How to distinguish result from claim?
How to repair without losing history?
How to resume from facts instead of memory?
```

因此它的价值不是：

> 尽可能减少 Human。

而是：

> **把 Human、AI 与工程工具放到各自更适合承担的位置。**

可以概括成：

```text
Human
保留目标、边界与最终责任

AI
承担高密度分析与执行工作

DevFlow
保证执行过程有边界、有状态、有证据

Verification / Git
提供独立于 AI 叙述的工程事实
```

---

## 9. Zhiji × DevFlow · 两套系统为什么会表现出相似结构

知几 Agent 和 DevFlow 解决的是两类不同问题：

```text
Zhiji Agent
管理 AI 如何参与复杂企业决策

DevFlow
管理 AI 如何参与复杂软件开发
```

但两者逐渐形成了相似原则：

| Zhiji | DevFlow |
|---|---|
| Business State | Engineering State |
| Agent Proposal | AI Plan / Code Change |
| Human Gate | Acceptance / Owner Decision |
| Evidence / Lineage | Artifact / Verification / Git |
| Workflow | Phase / Task Runner |
| Fail Closed | Stop on verification / scope / state conflict |
| Recovery | Context / Phase Recovery |

它们共同强调：

```text
显式状态
明确权限
候选与事实分离
确定性验证
Human Gate
证据可追溯
失败可恢复
```

这种相似性不是说二者是同一套系统。

而是说明：

> **当 AI 从“提供建议”进一步进入长链复杂任务时，状态、权限和证据逐渐成为比单次生成能力更基础的问题。**

---

## 10. Current Boundary · DevFlow 当前不是什么

对外介绍 DevFlow 时，需要保留几个明确边界。

### 10.1 不是通用开发平台

DevFlow 是：

> **知几 Agent 开发过程中形成并实际使用的个人 AI 软件工程控制系统。**

当前没有把它包装成：

- 多租户研发平台；
- 通用 Coding Agent SaaS；
- 企业级 SDLC 替代品。

---

### 10.2 不是全自动软件工厂

DevFlow 仍然明确保留：

```text
Human Decision
```

尤其在：

- Scope 变化；
- 高风险操作；
- ambiguous recovery；
- 最终验收与合并；

等节点。

因此：

```text
AI Native
≠
Fully Autonomous
```

---

### 10.3 工程事实不等于产品正确性

即使：

```text
ruff passed
mypy passed
pytest passed
PostgreSQL passed
```

也只能证明相应的技术验证成立。

最终仍需要回答：

> **实现是否真的满足产品目标与 Task Contract？**

---

## 11. Summary · DevFlow 的核心

DevFlow 可以压缩成一句话：

> **把 Human 的目标与边界、AI 的规划与代码执行、自动化验证、Git 版本事实和人工验收，组织成一条由显式 Contract、State 与 Artifact 驱动的可控开发闭环。**

进一步压缩：

```text
Define before execute.
Constrain before delegate.
Verify before accept.
Record before resume.
Repair from evidence.
Human keeps final authority.
```

它真正试图解决的是：

> **如何在 AI 承担越来越多软件工程工作的同时，不把任务边界、工程状态、验证事实和最终责任一起交给 AI。**

---

## 12. 本文与其他文档的关系

DevFlow 是 Public Repo 中的一条第二故事线。

理解知几 Agent 本身，并不要求先理解 DevFlow。

| 读者还想确认 | 去哪 |
|---|---|
| 知几本身如何分析企业问题？ | `docs/METHOD.md` |
| 知几里的 Agent / Human / Workflow 权限如何划分？ | `docs/GOVERNANCE.md` |
| 知几的软件架构如何实现这些约束？ | `docs/ARCHITECTURE.md` |
| 知几当前真实做到哪里？ | `docs/STATUS.md` |
| 知几实际一次 Product Loop 怎么运行？ | `demo/README.md` |
| 知几有哪些公开工程验证事实？ | `evidence/` |

两条故事线的关系可以理解为：

```text
Zhiji Agent
业务决策如何被建模、治理和推演

DevFlow
复杂 AI 软件如何被规划、执行、验证和恢复
```

它们共同构成这个项目对 AI Native 系统设计的两种实践。
