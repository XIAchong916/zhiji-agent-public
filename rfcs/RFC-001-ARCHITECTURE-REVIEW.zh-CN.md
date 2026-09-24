# RFC-001 · Architecture Review · 架构审查

[English](RFC-001-ARCHITECTURE-REVIEW.md) | [中文](RFC-001-ARCHITECTURE-REVIEW.zh-CN.md)

> **Status · 状态：** Draft for External Review<br>
> **Scope · 范围：** Zhiji Agent core method, governance and architecture assumptions<br>
> **Purpose · 目的：** 在真实 Semantic Intelligence 已进入受治理路径、真实企业案例仍待验证的阶段，主动暴露最可能失败的核心架构假设。

本文不是另一篇项目介绍。

Public Repo 中已有几篇稳定文档分别回答：

- [`METHOD`](../docs/METHOD.md)：知几如何建模并逐步收敛复杂业务问题；
- [`GOVERNANCE`](../docs/GOVERNANCE.md)：谁可以提出、确认和改变什么；
- [`ARCHITECTURE`](../docs/ARCHITECTURE.md)：这些方法与治理规则如何被软件保证；
- [`STATUS`](../docs/STATUS.md)：当前哪些能力已经实现，哪些尚未实现。

本文只做一件不同的事：

> **把当前仍然不确定、最值得被外部挑战的架构假设显式暴露出来，并说明为什么暂时这样设计、已知风险是什么、什么证据会推动我们修改设计。**

这里的目标不是证明当前架构“正确”。

而是尽早回答：

> **当真实模型和真实企业进入系统以后，这套架构最可能先在哪个假设上失败？**

---

## 1. Review Principles · 审查原则

当前项目刻意把几类容易在 AI 系统中混在一起的概念分开：

```text
Evidence ≠ Hypothesis

Proposal ≠ Accepted State

Human Approval ≠ Causal Proof

Projection ≠ Actual Outcome

Selection ≠ Execution Authorization
```

同时，知几采用五层递归业务抽象：

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

其核心递归关系为：

```text
Layer(n).Object = Layer(n+1).System
```

并尝试通过双向机制维持跨层连续性：

```text
Downward
Confirmed StatePackage
        ↓
Next-layer Diagnosis

Upward
Local Change
        ↓
Candidate Upper-layer Impact
```

这些都是**当前设计选择**，不是已经完成真实企业验证的普适真理。

下面六个问题，是目前最值得外部 Reviewer 挑战的部分。

---

# Q1. Semantic Authority · Agent 应该拥有多大的语义生成权？

## 当前设计 · Current Design

知几将“语义生成能力”和“业务权威”明确分开。

当前治理链路是：

```text
Evidence / Confirmed State
          ↓
Agent Reasoning
          ↓
Proposal / Hypothesis
          ↓
Validation
          ↓
Human Gate
          ↓
Accepted Domain State
```

Agent 可以产生非权威的语义判断，例如：

- Position Hypothesis；
- ChangeLaw Hypothesis；
- Intervention Proposal；
- Position Projection；
- Candidate Analysis。

但部分信息仍然不会开放给模型自由生成或直接改写，尤其包括：

- 对象身份类事实；
- 已确认业务事实；
- 目标与优先级；
- 硬约束；
- 能力边界；
- 最终选择与决策。

当前原则是：

> **Semantic Intelligence 的提高，不自动意味着 Semantic Authority 的扩大。**

也就是说，模型可以越来越强，但“它能判断什么”和“系统允许它决定什么”仍然是两个不同问题。

---

## 为什么这样设计 · Why This Design

企业诊断不是一次性回答，而是一条长链路。

如果 Agent 在某一步产生的推断被静默当成事实，后续层级就可能继续在错误前提上推演：

```text
Model Inference
      ↓
Treated as Fact
      ↓
Used by Next Layer
      ↓
Further Inference
      ↓
Compounded Error
```

因此，知几试图始终保留两件事的区别：

```text
模型认为是什么
```

和：

```text
系统当前允许后续依赖什么
```

前者属于 Semantic Proposal，后者属于 Authoritative Domain State。

---

## 已知风险 · Known Risk

当前边界可能在两个方向上失败。

### Agent 权限过小

如果太多信息都必须由 Human 明确输入或确认：

- 用户会反复填写 Agent 本可以可靠推断的信息；
- Human Gate 逐渐退化为表单操作；
- 交互成本过高；
- Semantic Intelligence 很难真正发挥价值。

系统最终可能退化成：

> **一个结构化工作流工具，只是在旁边加了 AI 助手。**

### Agent 权限过大

如果模型生成的语义过于容易进入 Accepted State：

- 推断可能逐层污染后续状态；
- 隐含假设越来越难追溯；
- 模型置信感容易被误认为 Evidence Strength；
- Human Review 可能逐渐变成形式确认。

系统最终可能退化成：

> **一个语言非常流畅、但业务权威边界很弱的自主分析器。**

---

## 希望 Reviewer 挑战什么 · Questions for Review

1. 哪些语义类别可以默认允许 Agent 生成 `HYPOTHESIS`？
2. 哪些信息应始终保持 Human / confirmed-source owned？
3. `USER_FACT / CONFIRMED / INHERITED / HYPOTHESIS` 是否足以表达当前 Semantic Authority？
4. Agent Authority 是否应该随 Evidence Strength、Reversibility 或 Business Impact 动态变化？
5. 不同分析层级是否应该拥有不同的 Semantic Authority 规则？
6. 什么情况下 Human Gate 已经不再产生有效增量，而只是增加操作成本？

---

## 设计修正条件 · What Would Change the Design

如果真实案例反复证明以下条件成立，可以考虑扩大 Agent Authority：

- 某类语义能够稳定推断；
- 错误成本低且可逆；
- Evidence provenance 始终明确；
- 下游决策仍然安全；
- Human Review 的边际价值很低。

反之，如果真实案例出现以下情况，则应进一步收紧：

- 用户频繁接受“看起来合理但实际上错误”的 Proposal；
- 推断错误在跨层过程中持续放大；
- 无法根据 lineage 重建一个判断为什么被接受；
- Human Confirmation 无法有效阻止 Authority Drift。

---

# Q2. Human Gate Policy · Gate 应按 Workflow State 还是按 Semantic Risk 设置？

## 当前设计 · Current Design

知几当前在明确的决策节点设置 Human Gate。

方法层面可以概括为四类：

```text
Fact Gate
Goal / Constraint Gate
Evidence Gate
Decision Gate
```

Deterministic Workflow 负责判断：

- 当前状态是否允许继续；
- 当前迁移是否需要 Human；
- 哪种 Actor 有权完成该确认。

当前设计相对简单：

> **系统预先知道哪些状态迁移必须把权威交还给 Human。**

---

## 为什么这样设计 · Why This Design

按 Workflow State 设置 Gate 有几个明显优点：

- 规则显式；
- 状态迁移确定；
- 容易审计；
- 容易测试；
- Agent 很难绕过。

更重要的是，它避免让同一个概率模型自己决定：

> “这次风险不高，所以我认为不需要人工确认。”

治理逻辑不会直接依赖模型自我评估。

---

## 已知风险 · Known Risk

真实业务风险不一定和 Workflow State 一一对应。

两个处于同一 State 的决策，可能分别是：

```text
Low-impact + Reversible
          vs
High-impact + Irreversible
```

但静态 Gate 会把它们近似同等对待。

因此会产生两个相反问题。

### Over-governance · 过度治理

如果 Gate 太多：

- Human 容易疲劳；
- 确认逐渐变成机械点击；
- 诊断效率下降；
- 用户体验变差。

### Under-governance · 治理不足

如果一个形式上相同的 State 实际涉及：

- 较弱 Evidence；
- 较大财务影响；
- 不可逆后果；
- 合规风险；

那么仅按 State 决定 Gate 可能又过于粗糙。

---

## 希望 Reviewer 挑战什么 · Questions for Review

1. Human Gate 是否应该继续以 Workflow State 为主？
2. 是否应该逐步引入：
   - Semantic Risk；
   - Evidence Strength；
   - Reversibility；
   - Business Impact；
   - Uncertainty？
3. 谁来计算决定是否触发 Gate 的风险？
4. 是否能使用确定性规则完成 Risk Assessment，而不把治理重新交给 LLM？
5. 重复成功验证后，是否可以降低未来同类 Gate 的强度？
6. 如何避免 Approval Fatigue？

---

## 设计修正条件 · What Would Change the Design

如果真实使用显示：

- 静态 Gate 成为主要交互成本；
- 大量确认并没有带来真实修正；
- 风险维度能够由结构化状态稳定计算；

那么架构应逐步向 Risk-based Gate 演进。

反之，如果动态 Gate：

- 本身变得不透明；
- 主要依赖模型自我判断；
- 同类决策出现不一致权限；
- 无法重复计算；

那么 Human Gate 应继续以 Workflow State 为主。

---

# Q3. Recursive Abstraction · `Object → System` 是否存在 Over-abstraction？

## 当前设计 · Current Design

知几将企业问题组织为五个连续层级：

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

核心递归关系为：

```text
Layer(n).Object = Layer(n+1).System
```

含义是：

> **当当前层某个 Object 需要继续深入分析时，就打开它的内部结构，并把它作为下一层新的 System。**

下一层仍然复用同一套 Core Model：

```text
System
→ Object
→ Position
→ InternalState
→ ChangeLaw
→ Intervention
```

因此，变化的是分析尺度，而不是分析语法。

---

## 为什么这样设计 · Why This Design

如果不使用递归，不同业务尺度很容易变成五套互相独立的分析框架：

```text
Industry Model
Enterprise Model
Business Model
Scenario Model
Solution Model
```

这样会增加：

- 跨层状态继承难度；
- Engine 复用难度；
- 不同 Agent 之间的语义漂移；
- 上下层分析逻辑不一致。

递归设计试图提供一个统一的分析语言。

---

## 已知风险 · Known Risk

真实企业并不是一棵干净的树。

例如：

- 矩阵式组织；
- 平台型业务；
- 多业务共享能力；
- 企业外部生态关系；
- 一个 Scenario 同时影响多个 Business；
- 一个 Solution Capability 同时受到多个上层目标约束。

真实结构可能更接近：

```text
Graph
```

而不是：

```text
Tree
```

另外，五层结构也可能只是一个有效的默认拆解方式，而不是普适的企业 ontology。

---

## 希望 Reviewer 挑战什么 · Questions for Review

1. 哪些企业结构天然适合 `Object → System`？
2. 哪些结构需要：
   - 跳层；
   - 并行层；
   - 多父 System；
   - Cross-layer reference？
3. `Industry → Enterprise → Business → Scenario → Solution Capability` 应被理解为稳定 ontology，还是默认 traversal？
4. 递归关系更适合解释为 Containment、Context Change，还是 Graph Projection？
5. 同一个 Object 是否应允许同时属于多个 System？
6. 统一 Core Model 带来的结构一致性，是否大于它可能造成的业务失真？

---

## 设计修正条件 · What Would Change the Design

如果真实企业案例反复出现：

- 多父关系；
- 并行诊断路径；
- Cross-business Scenario；
- 平台型非层级结构；
- Shared Capability；

那么当前递归需要从层级模型进一步演进为 Graph-like Model。

如果大多数案例仍可以通过五层主路径稳定完成分析，只需要少量例外机制，那么可以继续保留五层作为默认递归骨架。

---

# Q4. Epistemic State · 系统如何表达“我们究竟有多确定”？

## 当前设计 · Current Design

知几当前刻意区分：

```text
Evidence
   ↓
Hypothesis
   ↓
Proposal
   ↓
Human Acceptance
   ↓
Projection
   ↓
Measurement
   ↓
Actual Outcome
```

其中一个核心不变量是：

```text
Human Approval ≠ Causal Proof
```

Human Acceptance 可以允许某个判断继续进入决策链。

但它不能自动把 ChangeLaw 变成已经被现实证明的因果规律。

同样：

```text
Projection ≠ Actual Outcome
```

当前系统已经区分 lifecycle、authority 和 outcome，但并不声称已经拥有一套完整的业务 Epistemic Model。

---

## 为什么这样设计 · Why This Design

真实业务判断通常不是简单的：

```text
true / false
```

一个 ChangeLaw 可能来自：

- 专家经验；
- 单个案例；
- 运营数据；
- 统计相关；
- A/B Test；
- 重复干预；
- 外部研究。

这些 Evidence 的可信程度显然不同。

如果不区分 epistemic state，系统很容易把：

```text
accepted for decision
```

误写成：

```text
proven true
```

---

## 已知风险 · Known Risk

仅使用：

```text
HYPOTHESIS / ACCEPTED
```

可能太粗。

但简单增加一个 0–1 Confidence Score，又可能制造虚假的精确性。

目前仍有很多问题未解决：

- Source Reliability；
- Evidence Freshness；
- Conflicting Evidence；
- Correlation vs Causation；
- Replication；
- Domain Transfer；
- Uncertainty Calibration。

---

## 希望 Reviewer 挑战什么 · Questions for Review

1. 是否需要一等对象 `EpistemicState`？
2. Evidence Strength 应该使用：
   - categorical；
   - numeric；
   - partially ordered；
   - source-specific？
3. 谁有权更新 epistemic status？
4. Human Confirmation 是否应该改变 epistemic status？
5. 新 Evidence 与已有 ChangeLaw 冲突时如何处理？
6. Causal Claim 是否应拥有比 Position Hypothesis 更严格的状态模型？
7. 一个企业验证出的 Evidence 应如何迁移到另一个企业？

---

## 设计修正条件 · What Would Change the Design

如果真实案例显示：

- 不同 accepted hypothesis 的 Evidence Quality 差异很大；
- Reviewer 无法判断为什么某个 ChangeLaw 比另一个更可信；
- 冲突 Evidence 难以表达；
- 多轮 Measurement 需要逐步沉淀为更强知识；

那么应该引入更完整的 Epistemic State Model。

反之，如果复杂 epistemic schema 并不能提升真实决策质量和可追溯性，只增加模型复杂度，就应保持轻量。

---

# Q5. Model Context vs. Audit Trace · 如何同时满足模型能力、隐私与可审计性？

## 当前设计 · Current Design

真实 Semantic Agent 需要足够丰富的业务上下文才能有效分析。

但系统又不应该把所有：

- 企业原始材料；
- Prompt；
- Model Context；
- 敏感业务数据；

长期持久化到 Trace 中。

因此当前设计区分：

```text
Ephemeral Authorized Runtime Context
             ↓
           Model
             ↓
        Raw Proposal
             ↓
         Validation
             ↓
Safe Digest / References / Structured Result
             ↓
        Persisted Trace
```

也就是说：

> **模型运行时 Context 与长期 Audit Trace 是两个不同的数据面。**

Persisted Trace 应该足以回答：

- 使用了什么 Evidence；
- 引用了哪些 Record；
- 生成了什么 Proposal；
- 谁确认了它；
- 后续状态如何变化。

但不必把全部原始上下文永久保存。

---

## 为什么这样设计 · Why This Design

如果完整保存所有模型上下文，会增加：

- Privacy Risk；
- Security Risk；
- Data Minimization 问题；
- 敏感信息长期暴露风险。

但如果保存得太少，又会出现相反问题：

> **只能看到最终结果，却无法重建模型为什么会产生这个判断。**

因此，知几希望保留 Lineage，同时不把 Raw Context 直接当作长期 Domain State。

---

## 已知风险 · Known Risk

仅保存：

```text
hash
reference
structured output
safe metadata
```

可能仍不足以完成真正 Semantic Audit。

例如：

- 原始 Source 后续被修改；
- 外部 Retrieval 结果失效；
- Model / Provider / Version 改变；
- Prompt Construction Logic 改变；
- 实际授权给模型的 Context 比 Trace 中记录的更丰富；
- Proprietary Model 本身不可重复。

因此存在一组真实张力：

```text
Rich Context
    ↕
Privacy
    ↕
Reproducibility
    ↕
Auditability
```

---

## 希望 Reviewer 挑战什么 · Questions for Review

1. meaningful semantic audit 最少需要持久化哪些信息？
2. 如果原始 Source 后续不可访问，hash 是否仍然足够？
3. 哪些业务材料应该：
   - retain；
   - reference；
   - summarize；
   - delete after inference？
4. Model / Version / Prompt Template 应记录到什么粒度？
5. 高风险决策是否应该要求更强的 Context Retention？
6. 面对非确定性外部模型，Semantic Reproducibility 是否本身就是一个不现实目标？

---

## 设计修正条件 · What Would Change the Design

如果真实 Review 经常无法重建：

- 当时用了哪些 Evidence；
- 模型真正看到哪些 Context；
- 为什么一个 Proposal 会被接受；

则需要增强 Trace Retention。

反之，如果企业真实使用表明：

- 长期保存 Context 显著增加隐私和安全风险；
- Reference + structured lineage 已足够支撑审查；
- Full Prompt Retention 并没有带来明显增量价值；

则应进一步收紧持久化范围。

真实 LLM Provider 路径已经使这个问题可以被直接验证，但当前仍没有形成最终 production trade-off。

---

# Q6. Impact Propagation · 局部改善什么时候可以被认为产生了整体价值？

## 当前设计 · Current Design

知几的递归是双向的。

向下：

```text
Confirmed StatePackage(n)
          ↓
Lower-layer Diagnosis
```

向上：

```text
Intervention(n+1)
       ↓
ΔInternalState(n+1)
ΔPosition(n+1)
       ↓
Candidate Impact(n)
       ↓
Review / Confirmation
```

核心原则是：

> **Impact Propagation ≠ State Overwrite。**

下层局部改善不能因为“看起来有影响”就直接修改上层 Position。

系统需要重新判断：

> 当前 Local Change 是否真的足以影响上一层 Object 的 InternalState 或 Position？

METHOD 因此明确保留：

```text
Local Improvement
      ≠
Enterprise Value
```

---

## 为什么这样设计 · Why This Design

很多 AI 项目能够证明局部指标提升，却不能证明企业价值提升。

例如：

```text
faster task completion
≠
better business outcome

higher model accuracy
≠
higher process value

scenario optimization
≠
enterprise value creation
```

所以知几不希望：

> 局部 KPI 变好，就自动被解释成企业层面的价值成立。

局部效果必须重新回到上层目标中评估。

---

## 已知风险 · Known Risk

这可能是当前 METHOD 中真实验证最弱的一部分。

实际 Impact Propagation 可能同时存在：

- amplification；
- attenuation；
- cancellation；
- time lag；
- shared upstream variables；
- multiple simultaneous interventions；
- nonlinear effects；
- attribution ambiguity；
- environmental change。

一个下层 Object 可能影响多个上层 Goal。

多个下层 Object 也可能共同决定一个上层结果。

因此，简单层级式传播很可能不够。

---

## 希望 Reviewer 挑战什么 · Questions for Review

1. 一个 Local Change 满足什么条件后，才应该形成 `Candidate Upper-layer Impact`？
2. Propagation 应该主要基于：
   - rule；
   - model；
   - measurement；
   - mixed approach？
3. 多个 Scenario Impact 应如何聚合为 Business Position 的变化？
4. 系统应如何表达：
   - interaction effects；
   - cancellation；
   - thresholds；
   - time lags？
5. Impact Propagation 应在什么条件下停止？
6. Attribution 应如何与 Correlation 分离？
7. 如果局部改善没有检测到上层 Position Change，它是否仍可能具有真实价值？
8. Impact Propagation 应该成为 Generic Engine 能力，还是保持 Domain-specific？

---

## 设计修正条件 · What Would Change the Design

如果真实案例显示：

- 跨层影响无法用通用规则表达；
- attribution 才是主要问题；
- many-to-many 关系大量存在；
- time lag 导致即时传播判断严重失真；

那么 Impact Propagation 应进一步显式化，并允许更强的 Domain-specific Model。

反之，如果可以抽象出一组稳定的传播 primitives，而行业差异主要体现在参数与 ChangeLaw 上，则仍可以保留一个 Generic Framework。

---

# 7. Review Priority · 当前优先挑战顺序

六个问题不需要在下一步产品开发前同时解决。

当前更合理的是按照系统接下来真正会遇到的压力顺序推进：

```text
P0
Semantic Authority
Human Gate Policy

P1
Recursive Abstraction
Impact Propagation

P2
Epistemic State
Model Context vs. Audit Trace
```

这里的 P0 / P1 / P2 **不是重要性排名**，而是验证依赖顺序。

原因是：

- 真实 Semantic Proposal 路径已经进入系统，当前首先需要继续压力测试 Semantic Authority 与 Human Gate；
- Real Enterprise Case 进入后，最先挑战递归结构与 Impact Propagation；
- 多案例长期运行之后，Epistemic State 与 Trace Policy 才会逐渐成为更强约束。

---

# 8. How to Review · 希望 Reviewer 怎样反馈

最有价值的反馈不是：

> “这个架构看起来不错。”

而是：

```text
Assumption
你认为哪个当前假设过强、错误或缺少前提？

Failure Case
什么真实企业 / AI 场景会把它打破？

Consequence
如果失败，会导致 METHOD / GOVERNANCE / ARCHITECTURE 哪部分失效？

Alternative
你会使用什么更简单或更稳健的设计？

Evidence
什么观察能够区分当前设计与你提出的替代方案？
```

可以直接使用下面的最小 Review Template：

```markdown
## Question
Q3 — Recursive Abstraction

## Assumption I Challenge
...

## Failure Case
...

## Why It Matters
...

## Alternative
...

## Evidence / Test
...
```

真正有价值的 Review 应尽量做到：

> **可指出具体假设、可给出反例、可提出替代、可设计验证。**

---

# 9. What This RFC Does Not Decide · 本 RFC 不提前决定什么

本文刻意**不**提前得出以下结论：

- Agent Authority 应该扩大；
- Human Gate 应该改成动态风险制；
- 五层递归应该改成 Graph；
- 应该增加数字型 Confidence Score；
- 应该长期保存完整 Prompt；
- Impact Propagation 应该自动化。

这些本来就是需要被挑战的问题。

只有当：

```text
External Critique
        +
Real Semantic Agent Behavior
        +
Real Enterprise Cases
        +
Measurement / Outcome Evidence
```

提供了比当前设计更强的证据以后，架构才应该改变。

---

# 10. Relationship to the Public Repo · 与其他文档的关系

```text
README
这是什么，为什么值得继续看
        ↓
METHOD
怎么分析
        ↓
GOVERNANCE
谁能决定什么
        ↓
ARCHITECTURE
软件如何保证
        ↓
STATUS
现在真实做到哪里
        ↓
RFC-001
哪些核心假设最可能是错的
        ↓
DEMO / EVIDENCE
当前系统实际怎么运行，又能证明什么
```

因此，这篇 RFC 的职责可以压缩成一句话：

> **不要替当前架构辩护，而是把它最脆弱、最值得被证伪的假设暴露出来。**
