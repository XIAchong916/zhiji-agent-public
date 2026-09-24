# DEVFLOW · AI-Native Software Engineering Control System

[English](DEVFLOW.md) | [中文](DEVFLOW.zh-CN.md)

> This document answers one core question:<br>
> **How was Zhiji Agent developed continuously and controllably with Human, ChatGPT, Codex, Terminal / CI, and Git / GitHub all participating?**

DevFlow is not a business feature of Zhiji Agent, and it is not a productized general-purpose development platform.

It emerged from the real development process of Zhiji Agent:

> As a personal AI project grows from a small amount of code into a complex software system with many Modules, Phases, Tasks, states, verification steps, and repairs, the central problem stops being “can AI write code?” and becomes “how can AI execute complex engineering work continuously, controllably, and recoverably?”

DevFlow is therefore positioned as:

> **A personal AI software-engineering control system used in the actual development of Zhiji Agent.**

It is designed not to compensate for weak model generation, but to solve engineering-control problems that appear once AI becomes deeply involved in software development:

- **Context Loss**: current facts and context disappear across sessions;
- **Scope Drift**: long tasks gradually deviate from their intended boundaries;
- **State Ambiguity**: it becomes unclear where execution actually is;
- **False Completion**: AI says “done” without sufficient engineering evidence;
- **Repair Loop**: one fix may introduce another problem;
- **Recovery Difficulty**: after failure or session change, it is hard to reconstruct a trustworthy engineering state.

The core idea is:

> **Transform development from continuous collaboration that depends on chat context into an execution system constrained by explicit tasks, states, and engineering evidence.**

---

## 1. Role Separation · Why Split Responsibilities

DevFlow does not give planning, coding, verification, acceptance, and version facts to one Agent.

It separates participation into six responsibility units:

```text
Human
  ↓
goals / boundaries / risk / final acceptance

ChatGPT
  ↓
requirements analysis / planning / technical review / problem diagnosis

Codex
  ↓
code implementation / testing / repair

DevFlow
  ↓
task orchestration / state control / evidence archiving

Terminal / CI
  ↓
local execution / automated verification

Git / GitHub
  ↓
version facts: Branch / Commit / PR / Merge
```

The point is not the specific tool names; it is **separation of responsibility**.

In one sentence:

> **Human sets direction; AI performs analysis and execution; DevFlow controls the process; engineering evidence and Human judgment jointly confirm the result.**

### 1.1 Human · Final Authority over Goals and Risk

Human is responsible for:

- product goals;
- requirement boundaries;
- major architectural trade-offs;
- risk judgment;
- whether major Scope changes are acceptable;
- final functional acceptance;
- final authorization for high-risk operations such as Merge or destructive recovery.

Human does not need to perform every code-level diagnosis personally, but retains:

```text
Final Product Authority
Final Risk Authority
Final Acceptance Authority
```

### 1.2 ChatGPT · Planning, Analysis, and Review

ChatGPT mainly handles:

- requirement understanding;
- solution design;
- architecture planning;
- Task decomposition;
- Artifact review;
- failure classification;
- Root Cause analysis;
- Repair design;
- Resume / Stop / Replan decisions.

It acts roughly as:

```text
Planner
+
Reviewer
+
Diagnosis Layer
```

But ChatGPT's natural-language judgment is not itself an engineering fact.

### 1.3 Codex · Constrained Executor

Codex is responsible for:

- reading the formal Task Contract;
- inspecting the real worktree;
- modifying code within the allowed scope;
- running focused checks;
- repairing implementation problems;
- producing execution results.

It should not independently:

- expand Scope;
- modify the formal Task definition;
- bypass failing tests;
- weaken verification merely to “pass”;
- declare formal task acceptance.

Therefore:

> **Codex may execute, but execution authority comes from the Contract, not from Codex's own judgment.**

### 1.4 DevFlow · Control Plane

DevFlow itself does not write business code and does not decide whether requirements are reasonable.

It controls:

```text
which Task should run now
what that Task may modify
what Artifact the execution produced
whether verification passed
what state the current Phase / Task is in
where failure should pause
which facts recovery must reread
```

It is therefore closer to a:

> **Development Control Plane**

than a Coding Agent.

---

## 2. Authoritative Facts · Stop Using Conversation as Engineering State

The most important change in DevFlow is that goals, tasks, state, and results that would otherwise be scattered across chats are turned into explicit control objects.

There are five core types.

### 2.1 Planning Artifact · Overall Direction

Records:

- requirement analysis;
- architecture design;
- Phase / Module planning;
- Task decomposition;
- key boundaries and acceptance strategy.

It answers:

> **What are we planning to build, and why this way?**

### 2.2 Manifest · Formal Task Identity

Manifest defines:

- formal Phases;
- formal Tasks;
- Task IDs;
- Task order;
- dependencies;
- runtime metadata such as precompleted tasks.

It answers:

> **Which Tasks formally belong to this Phase?**

### 2.3 State · Current Execution Position

State records:

- current Phase status;
- current Task;
- completed tasks;
- failed / paused position;
- commit checkpoints;
- latest Artifact reference.

It answers:

> **Where is the engineering work actually at right now?**

Typical Phase states include:

```text
READY
RUNNING
PAUSED
FAILED
COMPLETED
```

State records current position; it does not independently decide what should happen next.

### 2.4 Task Contract · AI Execution Boundary

A Task Contract defines:

- what the Task must implement;
- what may be modified;
- what must not be modified;
- Acceptance Criteria;
- required Verification;
- mandatory stop conditions.

Therefore:

> **The Task Contract is the authority for the boundary of one AI execution.**

Even if an AI believes “it would be better to change a bit more while I'm here,” that does not expand the formal Scope.

### 2.5 Artifact · What Actually Happened

Artifact stores actual execution evidence such as:

- code-execution results;
- Verification;
- logs;
- failure information;
- acceptance information;
- session / attempt evidence.

It answers:

> **What actually happened in this execution attempt?**

A core DevFlow rule is:

> **Natural-language reporting cannot replace an Artifact.**

---

## 3. Question-Specific Authority · Different Questions Use Different Sources of Truth

DevFlow does not use a simplistic model where one file always has the highest authority.

Instead:

| Question | Authoritative source |
|---|---|
| What are the current code / Branch / Commit? | Git / GitHub |
| Which Tasks formally belong to the current Phase? | Manifest |
| Where is execution now? | State, cross-checked with Manifest |
| What may the current Task modify? | Task Contract |
| Did implementation and verification pass? | Verification + Artifact |
| Is Human Review required? | Acceptance Artifact |
| Has the result been formally accepted? | Human Decision |
| Where should the current session resume? | Git + Manifest + State + Contract + Artifact + Handoff |

Therefore:

```text
Skill
Handoff
AI Summary
```

are only:

> **helpers for reading and interpreting authoritative facts.**

They cannot override Git, State, Contract, or Artifact.

---

## 4. Operating Modes · Why Not Every Task Uses the Same Execution Path

DevFlow uses four primary operating modes depending on task type.

### 4.1 Planning First

Used for:

- a new Module;
- a new Phase;
- a materially new capability;
- work whose goal or Scope is not yet frozen.

Flow:

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

Principle:

> **Define before execute.**

### 4.2 Continuous Phase

Used for:

> a standard development stage that has already been planned and can progress through a fixed Task Graph.

The Phase Runner selects the next eligible Task using:

```text
Manifest
+
Dependencies
+
Current State
```

The Task Runner then performs one controlled execution within the Task Contract.

### 4.3 Iterative Repair

Used when:

- execution fails;
- Verification fails;
- Acceptance fails;
- Semantic Review finds implementation drift.

Repair does not redesign the entire system. Instead:

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

Core principle:

> **Use evidence to locate the real cause, then make the smallest repair within the existing Scope.**

A new Fix Artifact does not overwrite the original failure Artifact, preserving the full problem history.

### 4.4 Single Task

Used for:

- independent review;
- governance repair;
- documentation work;
- final acceptance;
- one-off work outside a continuous Phase.

Even without a full Phase Runner, it still follows:

```text
Contract
Evidence
Verification
Acceptance
```

---

## 5. Execution Loop · How a Task Actually Completes

The main DevFlow execution chain is:

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

Expanded:

```text
Human / ChatGPT defines goal and Task
          ↓
Manifest confirms Task identity
          ↓
State confirms current position
          ↓
Task Contract constrains execution
          ↓
Codex modifies code
          ↓
Terminal / CI runs deterministic verification
          ↓
Artifact is produced
          ↓
Semantic / Human Review
          ↓
Accept / Repair / Replan / Stop
          ↓
Git checkpoint
          ↓
State update
          ↓
Next Task
```

The key point is:

> **“AI finished executing” is not the condition for task completion.**

A Task is complete only when there is:

```text
Execution Result
+
Deterministic Evidence
+
Semantic Acceptance
+
Human Decision when required
```

---

## 6. Quality Governance · Technical Pass ≠ Task Pass

DevFlow separates acceptance into three layers.

### 6.1 Deterministic Verification · Technical Verification

Answers:

> **Does the implementation satisfy basic engineering conditions?**

Including:

```text
git diff --check
ruff format
ruff lint
mypy
pytest
PostgreSQL verification
GitHub Actions
```

These results should be:

- repeatable;
- measurable;
- preservable as evidence.

### 6.2 Semantic Acceptance · Semantic Review

Even when every technical check passes, the Task may still be wrong.

Review must also ask:

- was the correct object changed?
- does the implementation truly satisfy the Task Contract?
- did Scope Drift occur?
- did the implementation bypass an existing Architecture Contract?
- were tests or assertions weakened merely to create a “pass”?
- was the Root Cause fixed rather than only the symptom?

Therefore:

```text
Tests Pass
≠
Task Accepted
```

### 6.3 Human Decision · Final Judgment

Human retains final decision authority for:

- product goals;
- Scope changes;
- Architecture trade-offs;
- high-risk operations;
- NEED_HUMAN states;
- final Acceptance;
- Merge.

Therefore:

> **Verification provides facts, Semantic Review provides judgment, and Human provides final responsibility.**

---

## 7. State & Recovery · Why Development Can Continue Across Sessions

A central problem in long-running AI development is:

> If the chat window changes, does all accumulated “context” disappear?

DevFlow's answer is:

> **Chat context cannot be the only source used to recover engineering state.**

Recovery rereads:

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

and reconstructs:

- the current code version;
- the current formal Task;
- completed work;
- current failure / pause position;
- latest execution evidence;
- what is allowed next.

If authoritative facts conflict:

> **The system escalates to Human confirmation rather than letting AI automatically pick the version that “looks reasonable.”**

Normal Context Recovery may only read and reconstruct existing State.

If State itself must change, that should be a separate State Repair / Governance Task:

```text
explicit authorization
→ execution
→ verification
→ evidence
```

not an opportunistic edit made during recovery.

---

## 8. Why DevFlow Matters · The Real Problem Is Control, Not Automation

At first, DevFlow may look like:

> “Let AI help me write code automatically.”

As Zhiji grew, however, the real questions became closer to:

```text
How to preserve intent?
How to constrain scope?
How to know current state?
How to distinguish result from claim?
How to repair without losing history?
How to resume from facts instead of memory?
```

Its value is therefore not:

> minimizing Human involvement as much as possible.

It is:

> **placing Human, AI, and engineering tools in the roles they are better suited to perform.**

In compact form:

```text
Human
retains goals, boundaries, and final responsibility

AI
performs high-density analysis and execution

DevFlow
ensures execution has boundaries, state, and evidence

Verification / Git
provide engineering facts independent of AI narration
```

---

## 9. Zhiji × DevFlow · Why the Two Systems Converged on Similar Structures

Zhiji Agent and DevFlow solve different kinds of problems:

```text
Zhiji Agent
manages how AI participates in complex enterprise decisions

DevFlow
manages how AI participates in complex software development
```

Yet they gradually converged on similar principles:

| Zhiji | DevFlow |
|---|---|
| Business State | Engineering State |
| Agent Proposal | AI Plan / Code Change |
| Human Gate | Acceptance / Owner Decision |
| Evidence / Lineage | Artifact / Verification / Git |
| Workflow | Phase / Task Runner |
| Fail Closed | Stop on verification / scope / state conflict |
| Recovery | Context / Phase Recovery |

Both emphasize:

```text
explicit state
clear authority
separation of candidate from fact
deterministic verification
Human Gate
traceable evidence
recoverable failure
```

This does not mean the two systems are the same.

It suggests instead that:

> **When AI moves from “giving advice” into long-chain complex work, state, authority, and evidence become more fundamental than one-shot generation quality.**

---

## 10. Current Boundary · What DevFlow Is Not

Several boundaries should remain explicit in public descriptions of DevFlow.

### 10.1 Not a General Development Platform

DevFlow is:

> **a personal AI software-engineering control system developed and used during Zhiji Agent development.**

It is not currently packaged as:

- a multi-tenant development platform;
- a general Coding Agent SaaS;
- an enterprise SDLC replacement.

### 10.2 Not a Fully Autonomous Software Factory

DevFlow explicitly retains:

```text
Human Decision
```

especially for:

- Scope changes;
- high-risk operations;
- ambiguous recovery;
- final acceptance and Merge.

Therefore:

```text
AI Native
≠
Fully Autonomous
```

### 10.3 Engineering Facts ≠ Product Correctness

Even if:

```text
ruff passed
mypy passed
pytest passed
PostgreSQL passed
```

that proves only the corresponding technical checks.

The project must still answer:

> **Does the implementation actually satisfy the product goal and Task Contract?**

---

## 11. Summary · DevFlow in One Model

DevFlow can be compressed into one sentence:

> **Organize Human goals and boundaries, AI planning and code execution, automated verification, Git version facts, and Human acceptance into a controllable development loop driven by explicit Contract, State, and Artifact.**

Compressed further:

```text
Define before execute.
Constrain before delegate.
Verify before accept.
Record before resume.
Repair from evidence.
Human keeps final authority.
```

The deeper problem DevFlow addresses is:

> **How can AI take on more software-engineering work without also becoming the authority over task boundaries, engineering state, verification facts, and final responsibility?**

---

## 12. Relationship to Other Documents

DevFlow is a second narrative thread in the Public Repo.

Understanding Zhiji Agent itself does not require understanding DevFlow first.

| If you want to know... | Read... |
|---|---|
| How does Zhiji analyze enterprise problems? | `docs/METHOD.md` |
| How are Agent / Human / Workflow authorities divided in Zhiji? | `docs/GOVERNANCE.md` |
| How does Zhiji's software architecture enforce these constraints? | `docs/ARCHITECTURE.md` |
| What has Zhiji actually implemented today? | `docs/STATUS.md` |
| What does one Zhiji Product Loop actually look like? | `demo/README.md` |
| What public engineering verification facts exist? | `evidence/` |

The relationship between the two narratives is:

```text
Zhiji Agent
how business decisions are modeled, governed, and simulated

DevFlow
how complex AI software is planned, executed, verified, and recovered
```

Together they form two practical explorations of AI-native system design within this project.
