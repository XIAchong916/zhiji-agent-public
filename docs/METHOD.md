# METHOD · 知几的诊断与方案推演方法

> 本文档回答一个核心问题：<br>
> **知几如何把一个宽泛、复杂的企业 AI 转型问题，逐步收敛成可干预、可比较、可验证的业务决策问题。**

知几的方法不是从某个 AI 技术能力出发寻找应用场景，而是从企业当前真实业务问题出发，先解释：

1. **当前分析的对象是谁，它处在什么环境中；**
2. **它为什么处在当前位置；**
3. **哪些变量真正影响结果；**
4. **哪些变量可以被现实手段改变；**
5. **在当前目标、约束和能力下，哪些干预真正可行；**
6. **局部改变是否可能向更高层业务系统产生价值。**

整个方法由四部分组成：

```text
Core Model
业务问题如何被统一表达
        ↓
Recursive Diagnosis
复杂问题如何逐层收敛
        ↓
State Inheritance & Impact Propagation
不同层级之间如何连续推演
        ↓
Three Agents
不同认知任务如何由稳定角色执行
```

## Method at a Glance · 方法总览

![Zhiji recursive diagnosis loop](../assets/recursive-loop.svg)

> 同一套 Core Model 在 Industry → Enterprise → Business → Scenario → Solution Capability 五层递归复用；向下继承 Confirmed StatePackage，向上传递 Candidate Impact，而不是直接覆盖上层状态。

---

## 1. Core Model · 如何表达一个业务问题

知几的起点是一个基本判断：

> **业务问题可以被理解为：某个 Object 为什么在当前 System 中处于当前位置，以及如何在现实约束下推动它向目标位置迁移。**

为了让行业、企业、业务、场景和能力方案可以使用同一套分析语言，知几将分析抽象为：

```text
System
  ↓
Object
  ↓
Position
  ↓
ChangeLaw
  ↓
Intervention
  ↓
New Position
```

这条链并不是一组静态分类，而是一条连续的分析逻辑：

> **先确定观察边界，再明确变化承载者；先判断当前位置，再解释变化机制；最后才讨论应该如何干预。**

### 1.1 System · 系统

**System** 是 Object 所处的环境。

它定义当前分析中与 Object 有关的：

- 边界；
- 规则；
- 资源条件；
- 外部约束；
- 与其他对象之间的关系。

同一个 Object 放在不同 System 中，其价值、约束和可行动空间可能完全不同。

因此，开始分析之前首先需要回答：

> **我们究竟在哪个系统中观察这个对象？**

System 的作用不是把所有环境因素都罗列出来，而是明确：**哪些外部条件构成当前判断的有效边界。**

---

### 1.2 Object · 对象

**Object** 是当前分析中真正承担变化的对象。

在不同层级中，Industry、Enterprise、Business、Scenario 和 Solution Capability 都可以成为当前分析的 Object。

明确 Object 的目的，是把一个复杂系统中的大量变量第一次收敛到一个清晰的问题：

> **到底是谁正在发生变化？**

如果 Object 不明确，后面的 Position、ChangeLaw 和 Intervention 很容易混在不同层级中，最终出现：

- 企业问题被当作场景问题解决；
- 局部效率提升被误认为企业价值提升；
- 技术能力建设脱离上层业务目标。

因此，Object 是后续全部推演的变化承载者。

---

### 1.3 Position · 位置

**Position** 描述 Object 在当前 System 中所处的相对状态。

知几不把业务状态简化成单一指标，而是同时关注：

- **Structural Position**：它在系统结构中的位置；
- **Functional Position**：它承担什么功能；
- **Performance Position**：当前表现如何；
- **Evolutionary Position**：它正在向什么方向变化。

因此，一个业务问题不再只是：

> “某个指标下降了。”

而是进一步变成：

> **Object 当前处于什么 Position？为什么处于这里？与目标 Position 之间存在什么差距？**

Position 的意义在于把零散指标重新放回业务系统中理解。

指标可以描述某个结果，而 Position 试图描述：

> **这个结果在整个系统中的业务含义是什么。**

---

### 1.4 Internal State · 内部状态

只知道 Position 仍然不足以解释：

> 为什么会处在这里，以及现实中究竟能改变什么？

因此，知几进一步把 Object 的内部状态抽象为：

```text
Object.InternalState = {R, θ, D, Ω}
```

其中：

**R — Objectives / References**

目标与评价参照。

它回答：

> 什么状态才算更好？当前判断到底服务于什么目标？

R 可以来自经营目标、业务目标、阶段性优先级或其他已经确认的评价参照。

**θ — Constraints**

边界与约束。

它回答：

> 哪些事情不能做，或者不能以某种方式做？

例如预算、时间、风险、合规、业务规则以及其他不可突破条件。

**D — Capabilities**

当前能力与资源条件。

它回答：

> 现在真正具备什么能力，缺少什么能力？

D 不只代表技术能力，也可以包括数据、组织、流程、资源和其他能够影响执行的条件。

**Ω — Feasible Option Space**

当前现实可行的选择空间。

它不是所有理论上可能的方案集合，而是：

> **在当前 System、目标、约束和能力条件下，真正能够被考虑的行动空间。**

可以表示为：

```text
Ω = Φ(System, R, θ, D)
```

这意味着，一个理论上“有效”的方案，如果超出当前能力、预算、合规要求或其他现实边界，就不属于当前真正可执行的 Intervention 空间。

因此，知几不把“可能有效”和“当前可做”混为一件事。

---

### 1.5 ChangeLaw · 变化机制

**ChangeLaw** 用来解释：

> **哪些变量发生变化，会通过什么机制改变 Object 的 Position？**

它关注的不是简单相关关系，而是一条结构化变化链：

```text
Driver
   ↓
Mechanism
   ↓
Intermediate Variables
   ↓
Result
```

并进一步考虑：

- **Threshold**：什么条件下变化才会发生；
- **Time Lag**：变化需要多久才能体现；
- **Feedback**：结果是否会形成进一步正向或负向反馈；
- **Expected Transition**：最终预期推动哪种 Position 迁移。

ChangeLaw 的意义在于把：

> “这个地方有问题”

进一步推进成：

> **“什么变量通过什么机制导致当前结果。”**

只有到这一层，后续 Intervention 才有明确作用对象。

同时，ChangeLaw 在知几中首先是**需要 Evidence 支持和后续验证的判断**，不是因为 Agent 生成就自动成为业务事实。

---

### 1.6 Intervention · 干预

**Intervention** 是对可改变变量施加的主动行动。

一个 Intervention 至少需要满足两个条件：

1. 能够作用于已经识别的 ChangeLaw 或关键变量；
2. 位于当前可行选择空间 Ω 内。

因此，知几的顺序不是：

```text
AI 有什么能力
      ↓
找一个地方使用
```

而是：

```text
业务结果由什么机制形成
        ↓
哪些变量真正可改变
        ↓
什么 Intervention 在现实中可行
        ↓
AI 是否适合成为实现这种干预的能力
```

AI 在这里是**可能的能力手段**，不是分析的起点。

---

### 1.7 New Position · 新位置

Intervention 的目标不是“完成一个项目”或“上线一个 AI 功能”，而是推动 Object 的真实业务状态发生变化。

可以抽象为两步：

**State Update**

```text
InternalState(t+1)
    = F(
        InternalState(t),
        Intervention(t),
        System(t)
      )
```

其中变化受 ChangeLaw 约束。

随后：

**Position Migration**

```text
Position(t+1)
    = H(
        InternalState(t+1),
        System(t+1)
      )
```

因此，知几最终关心的问题不是：

> AI 功能有没有上线？

而是：

> **这项 Intervention 是否真的改变了 Object 的 InternalState，并推动它向目标 Position 迁移？**

由此形成完整基础链：

```text
确定 System
    ↓
选择 Object
    ↓
判断 Current Position
    ↓
明确 Target Position
    ↓
解释 ChangeLaw
    ↓
识别可改变变量
    ↓
寻找 Ω 内的 Intervention
    ↓
推演 New Position
    ↓
后续 Measurement 验证
```

---

## 2. Recursive Diagnosis · 为什么需要递归

现实企业不是一个单层系统。

一个 AI 转型问题通常会同时跨越：

- 外部行业环境；
- 企业整体经营；
- 具体业务或价值流；
- 某个实际业务场景；
- 最终需要建设或组合的能力。

如果始终停留在高层：

> 分析容易变成宏观判断，无法形成真正可执行的 Intervention。

如果过早进入局部：

> 又可能直接优化某个场景，却无法确认它是否服务于企业真正重要的目标。

因此，知几将分析组织为五个连续观察层级：

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

这五层不是五套独立方法。

每一层都继续使用同一个 Core Model：

```text
System
→ Object
→ Position
→ InternalState
→ ChangeLaw
→ Intervention
```

区别只是：

> **当前观察的 System 和 Object 发生了变化。**

---

### 2.1 Object → Next-layer System

递归发生的关键是：

> **当前层需要继续深入的 Object，在下一层打开内部结构后，成为新的 System。**

形式化表示为：

```text
Layer(n).Object = Layer(n+1).System
```

例如：

```text
External Business Environment
          ↓
       Industry
          ↓
      Enterprise
          ↓
 Business / Value Stream
          ↓
       Scenario
          ↓
 Solution Capability
```

在较高层观察时，一个 Enterprise 可以作为 Industry System 中的 Object。

当分析需要继续深入 Enterprise 内部，这个 Enterprise 不再只是被观察对象，而成为下一层新的 System，从中继续选择更具体的 Business / Value Stream 作为新的 Object。

因此，递归不是简单地把问题拆成更多层。

它的核心作用是：

> **每进入下一层，都重新明确新的 System 边界，并把上层已经确认的方向转化为下一层更具体的问题。**

所以：

> **递归是方法，逐层收敛才是结果。**

---

### 2.2 五层分别解决什么问题

五层不是为了形成一个复杂分类体系，而是为了防止不同尺度的问题混在一起。

| Layer | 主要回答的问题 |
|---|---|
| **Industry** | 外部环境和产业结构正在如何变化？ |
| **Enterprise** | 企业在当前行业环境中处于什么位置，目标和关键约束是什么？ |
| **Business** | 哪些业务 / 价值流真正影响企业目标，值得继续投入分析？ |
| **Scenario** | 某个具体场景为什么处于当前状态，哪些变量值得干预？ |
| **Solution Capability** | 如果决定干预，需要组合或建设什么能力，如何验证？ |

由此，一个宽泛问题逐步收敛：

```text
企业 AI 转型
      ↓
值得关注的经营方向
      ↓
关键业务 / 价值流
      ↓
值得干预的具体场景
      ↓
可执行的能力方案
```

---

## 3. State Inheritance · 信息如何向下连续传递

进入下一层并不意味着：

```text
换一个 Agent
    ↓
重新开始分析
```

上一层已经确认的分析结果需要成为下一层的分析起点。

知几将这组跨层信息组织为：

```text
StatePackage
```

StatePackage 包含两类信息。

### 3.1 可继续参与推演的状态参数

```text
R / θ / D / Ω
```

即：

- 目标；
- 约束；
- 能力；
- 当前可行选择空间。

进入下一层后，可以基于更细粒度的信息继续识别这些状态中的可调整变量，并重新计算现实可行空间。

### 3.2 作为分析基线继承的信息

```text
Position
ChangeLaw
Evidence
```

这些内容代表上一层已经形成的：

- 状态判断；
- 变化机制；
- 证据基础。

下一层可以进一步补充和细化，但不能无依据地覆盖上一层已经确认的历史状态。

因此，跨层过程更接近：

```text
Confirmed StatePackage(n)
          ↓
      Object(n)
          ↓
    System(n+1)
          ↓
 Continue Diagnosis
```

而不是：

```text
New Layer
   ↓
Start Again
```

这使上层的目标、约束和已有认知不会因为换了分析层级或换了 Agent 而自动丢失。

---

## 4. Impact Propagation · 局部改变如何向上反馈

递归不仅向下收敛，也需要向上判断：

> **局部 Intervention 是否真的产生了更大的业务价值？**

影响可能沿着下面的方向重新评估：

```text
Solution Capability
        ↑
     Scenario
        ↑
     Business
        ↑
    Enterprise
        ↑
     Industry
```

但这里最重要的一点是：

> **向上传递的是候选影响，不是状态覆盖。**

一个局部变化不应该因为模型认为“有影响”，就直接改写上层已经确认的状态。

更准确的过程是：

```text
Intervention(n+1)
       ↓
ΔInternalState(n+1)
ΔPosition(n+1)
       ↓
evaluate propagation
       ↓
Candidate Impact(n)
       ↓
review / confirm
       ↓
新的上层状态（如果成立）
```

局部影响向上传导时，可能：

- 被放大；
- 被削弱；
- 被其他机制抵消；
- 在某一层停止。

因此，每一层都需要重新判断：

> **当前变化是否足以改变上一层 Object 的 InternalState 或 Position？**

如果成立，新状态经过确认后，才形成新的 StatePackage；如果不成立，则保留原有上层状态并停止继续传播。

由此形成一个双向递归结构：

```text
上层
确定方向 / 目标 / 约束
          ↓
     StatePackage
          ↓
下层
打开结构 / 定位变量 / 设计干预
          ↓
    Intervention
          ↓
局部状态与位置变化
          ↑
 Candidate Impact
          ↑
上层重新判断整体影响
```

知几因此试图同时保持两件事：

> **向下不断收敛，直到找到真正可以执行的干预点；<br>
> 向上持续关联，判断局部改变是否真的产生整体业务价值。**

---

## 5. Three Agents · 为什么是三个 Agent，而不是五个

五个分析层级描述的是：

> **问题需要分析到什么业务尺度。**

Agent 的划分描述的是：

> **哪一种稳定的认知任务由什么角色负责。**

两者不是同一个维度。

因此，知几没有机械地为五层分别建立五个 Agent，而是收敛成三个连续角色：

```text
Business Diagnosis Agent
          ↓
Scenario Diagnosis Agent
          ↓
Solution Design Agent
```

### 5.1 Business Diagnosis Agent · 业务诊断

覆盖：

```text
Industry → Enterprise → Business
```

这一阶段的共同任务是：

> **从企业整体经营出发，逐层缩小问题空间，寻找真正值得继续投入分析的方向。**

它主要关注：

- 宏观环境和产业格局；
- 企业目标与当前经营状态；
- 企业在系统中的 Position 和变化方向；
- 哪些 Business / Value Stream 对目标更加关键；
- 哪些业务问题值得继续下钻；
- 哪些 Scenario 应进入下一阶段。

它的核心职责可以概括为：

> **着眼全局、充分定位、逐层收敛。**

典型输出链：

```text
Industry Position
      ↓
Enterprise Position
      ↓
Business Position
      ↓
Investment Directions
      ↓
Candidate Scenario Portfolio
```

---

### 5.2 Scenario Diagnosis Agent · 场景诊断

运行在：

```text
Scenario
```

它继承 Business Diagnosis 已经确认的目标、约束、业务方向、Position、Evidence 和相关 ChangeLaw。

这一阶段不再主要回答“哪里有问题”，而是进一步回答：

> **这个场景为什么处于当前状态，是否真的值得解决，以及哪些变量可以被现实干预。**

主要分析：

- Current Position；
- Target Position；
- 关键业务变量；
- ChangeLaw；
- 可干预变量；
- Intervention；
- Expected Position Change；
- 当前目标、约束和能力是否允许这种变化。

其核心职责是：

> **因果定位与价值验证。**

最终，一个宽泛 Candidate Scenario 应被收敛成一个更明确的可决策对象：

```text
Current Position
      ↓
Target Position
      ↓
ChangeLaw
      ↓
Intervention
      ↓
Expected Position Change
```

---

### 5.3 Solution Design Agent · 方案设计

运行在：

```text
Solution Capability
```

它不重新寻找业务问题，而是继承已经确认的 Scenario、Target Position、ChangeLaw、Intervention direction、Evidence、目标与约束。

这一阶段主要回答：

> **如果决定改变这个场景，具体需要什么能力组合，以及怎样验证方案是否有效？**

主要负责：

- 将干预变量映射为能力需求；
- 组合现有能力与新增能力；
- 形成可比较的 Solution Options；
- 分析成本、风险和关键假设；
- 推演可能的 Position Change；
- 设计 Measurement Plan；
- 定义后续成功 / 失败判断条件。

核心职责是：

> **方案决策与工程化设计。**

---

### 5.4 三个 Agent 构成的是一条认知链

三个 Agent 并不是三个彼此独立的聊天机器人。

它们通过已经确认的 StatePackage 连续交接：

```text
Business Diagnosis
       ↓
Confirmed StatePackage
       ↓
Scenario Diagnosis
       ↓
Confirmed StatePackage
       ↓
Solution Design
```

因此，跨 Agent 时：

> **前一个阶段已经确认的目标、约束、状态、变化机制和 Evidence 不重新生成，而是作为下一阶段的分析起点继续使用。**

三个 Agent 最终形成一条连续认知链：

```text
发现值得解决的问题
        ↓
判断问题为什么成立、是否值得解决
        ↓
设计怎样改变它
        ↓
定义如何验证改变是否有效
```

---

## 6. End-to-End Method · 一次完整推演如何发生

把前面的部分合在一起，一次完整方法可以概括为：

```text
Business Problem
      ↓
确定当前 System / Object
      ↓
判断 Current Position
      ↓
明确 R / θ / D / Ω
      ↓
识别关键 ChangeLaw
      ↓
判断 Target Position
      ↓
识别可干预变量
      ↓
形成 Candidate Intervention
      ↓
向下进入更具体层级
      ↓
Scenario Diagnosis
      ↓
Solution Capability Design
      ↓
Position Projection
      ↓
Human Selection
      ↓
Measurement Plan
      ↓
后续 Actual Outcome 验证
```

在这个过程中，有三条原则始终不变：

### 原则一：先解释问题，再设计方案

```text
Position / ChangeLaw
        ↓
Intervention
```

而不是：

```text
Technology
   ↓
Find a use case
```

### 原则二：向下收敛，但不丢失上层目标

```text
Higher-layer confirmed state
            ↓
       StatePackage
            ↓
    Lower-layer diagnosis
```

局部优化必须持续受到更高层目标和约束的限制。

### 原则三：局部变化不能自动等同于整体价值

```text
Local Improvement
      ≠
Enterprise Value
```

局部 Intervention 产生的影响需要重新向上评估，只有当它足以改变更高层的 InternalState 或 Position 时，才可能形成更大的业务价值判断。

---

## 7. Method Boundary · 当前方法的边界

本文描述的是知几当前已经明确的**分析方法和业务抽象**，并不意味着这些假设已经全部完成真实企业验证。

当前需要继续接受外部挑战的部分包括：

- `Layer(n).Object = Layer(n+1).System` 是否对复杂企业结构具有足够通用性；
- 五层结构是否覆盖所有真实企业问题，或是否需要跳层、并行层和多父 System；
- ChangeLaw 在不同证据强度下应该如何表达其可信程度；
- Candidate Impact 的向上传播在真实业务中应如何校准；
- 三个 Agent 的职责边界在真实企业长期运行中是否需要进一步调整。

这些问题不会在 METHOD 中被预设为已经解决，而属于后续 Architecture Review、真实企业案例和 Measurement Feedback 需要继续验证的内容。

---

## 8. Summary · 方法的核心

知几的方法可以压缩成一句话：

> **先把企业问题表达为 Object 在 System 中的 Position 与变化机制问题，再通过五层递归逐步收敛到可干预的 Scenario 和 Solution Capability；上层状态通过 StatePackage 向下继承，局部影响以 Candidate Impact 向上反馈，最终由三个连续 Agent 完成从问题定位、场景验证到方案设计的认知链。**

进一步压缩为：

```text
Understand the System
        ↓
Locate the Object
        ↓
Explain the Position
        ↓
Model the ChangeLaw
        ↓
Find Feasible Intervention
        ↓
Recurse Downward
        ↓
Propagate Impact Upward
        ↓
Design and Validate the Solution
```

知几不是试图让 AI “直接给出答案”。

它试图把：

> **一个原本依赖经验、讨论和隐性认知的复杂决策过程，转化为可以逐层表达、持续推演、人工确认和后续验证的方法。**
