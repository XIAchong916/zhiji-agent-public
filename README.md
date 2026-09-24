# Zhiji Agent · 知几

[English](README.md) | [中文](README.zh-CN.md)

> A governed recursive diagnosis and decision-simulation system for enterprise AI transformation.

**Zhiji (知几): detect the signal early, understand the situation, and respond to the forces that shape it.**

Zhiji Agent explores how enterprise AI-transformation decisions—work that often depends on expert judgment, interview facilitation, and repeated discussion—can be turned into a **structured, continuously executable, reviewable human–AI decision process**.

It starts from real business problems. Through recursive diagnosis of business state, change mechanisms, and variables that can actually be intervened on, it narrows broad transformation questions into concrete scenarios and capability solutions. Evidence, Human Gates, deterministic workflow, and persisted state constrain the semantic authority of the Agent.

```text
Business Diagnosis
        ↓
Scenario Diagnosis
        ↓
Solution Design
```

The current project has an end-to-end Product Loop with persistence, recovery, Human Gates, and lineage. A real OpenAI-compatible LLM Provider is implemented in the production code path. The Agent can produce bounded, reviewable Semantic Proposals that stop at the USER Gate for confirmation.

The project still **does not claim** that:

- the Agent can autonomously perform high-quality enterprise diagnosis;
- real-enterprise diagnosis quality has been validated;
- recommended solutions are guaranteed to produce expected business outcomes;
- the system is production-ready;
- the system has real-world execution authority.

`Recursive Diagnosis` · `Human-in-the-loop` · `Evidence & State` · `Governed Semantic Proposal`

---

## 1. Why Zhiji

Enterprise AI-transformation initiatives are often first expressed as technology requests:

> “Build a knowledge base.”<br>
> “Build a customer-service Agent.”<br>
> “Connect a large language model.”<br>
> “Use AI to improve efficiency in this process.”

But a technology solution cannot answer the more fundamental questions:

**What does the enterprise actually need to change?<br>
At which business level and process does the problem occur?<br>
Which variables actually determine the outcome?<br>
Which variables can be changed?<br>
Is AI an appropriate intervention mechanism?<br>
Given current cost, capability, and constraints, is the investment worth making?**

Zhiji therefore starts from a basic position:

> **Enterprise AI transformation is first a business-decision problem, and only then a technology-implementation problem.**

Zhiji is not primarily trying to answer “how can AI produce a better response?” It is trying to answer:

> **How can complex enterprise AI-transformation judgment be converted into a decision process that a system can repeatedly execute, confirm, review, and validate?**

It attempts three shifts:

- **Model the business explicitly** — represent business objects, states, objectives, constraints, capabilities, and change mechanisms;
- **Make analysis executable** — organize the path from broad questions to concrete scenarios and solutions as a recursively progressable process;
- **Make decisions reviewable** — govern probabilistic AI judgments separately from Evidence, State, Human Gates, and later Measurement.

### Intended Value

- **Reduce the cost of front-loaded judgment** by turning parts of diagnosis that otherwise depend heavily on coordination and expert experience into repeatable analysis and simulation;
- **Reduce direct trial-and-error risk** by exposing change mechanisms, intervention paths, critical assumptions, and potential impacts before committing resources;
- **Improve reviewability** by grounding judgments in structured models, Evidence, persisted state, and Human Gates rather than a single model answer or an expert's personal credibility.

> These are product values Zhiji intends to validate. They are not claims of proven real-enterprise outcomes.

### What Zhiji Is Not

- **Not a general chatbot** — Agent output begins as Proposal / Hypothesis and does not directly become a business fact;
- **Not a one-shot report generator** — final Output is derived from persisted and confirmed states across a Journey;
- **Not an automated execution platform** — Confirm / Select / Approve means an analytical path or design has been accepted, not that the system has acquired real-world execution authority.

### Design Transparency

The Source Repository remains private.

This Public Showcase Repo exposes:

- Domain / Method;
- Human–AI Governance;
- Architecture;
- Implementation Status;
- Engineering Evidence;
- Demo artifacts;
- Known Limitations;
- Architecture RFC.

**Open Design / Closed Source**

The repository is designed for:

> **Design Reviewability ≠ Source-level Auditability**

---

## 2. METHOD · Core Model and Recursive Diagnosis

Zhiji's analytical framework begins from one basic idea:

> **A business problem can be understood as: why an Object is at its current Position within a System, and how it can be moved toward a target Position under real-world constraints.**

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

Where:

- **R — Objectives / References**: objectives and evaluation references;
- **θ — Constraints**: boundaries and constraints;
- **D — Capabilities**: currently available capabilities and resources;
- **Ω — Feasible Option Space**: the set of options that are realistically feasible now.

Zhiji does not begin by asking “which AI technology can we use?” and then work backward toward a business scenario. It begins with:

```text
What mechanism affects the business outcome?
        ↓
Which variables can actually be changed?
        ↓
Which Intervention is feasible in reality?
        ↓
Is AI an appropriate capability for implementing that Intervention?
```

### Recursive Diagnosis

A real enterprise is not a single-layer system.

Zhiji reuses the same Core Model across five connected scales:

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

The core recursive relationship is:

```text
Layer(n).Object = Layer(n+1).System
```

That is:

> **When an Object at the current layer requires deeper analysis, its internal structure is opened at the next layer and becomes the next System.**

At each deeper layer the analytical boundary becomes narrower:

> **The upper layer determines direction; the lower layer opens internal structure.**

Downward progression inherits confirmed objectives, constraints, state, and Evidence through a confirmed `StatePackage`. Upward progression carries Candidate Impact rather than directly overwriting confirmed upper-layer state.

![Recursive diagnosis loop](assets/recursive-loop.svg)

### Three Agents

The five business scales are not implemented as five independent Agents.

Zhiji uses three stable cognitive roles:

| Agent | Layers | Core responsibility |
|---|---|---|
| **Business Diagnosis Agent** | Industry → Enterprise → Business | Locate the business problem, recursively narrow it, and form Candidate Scenarios |
| **Scenario Diagnosis Agent** | Scenario | Locate causal structure, validate value, and identify critical variables and ChangeLaw |
| **Solution Design Agent** | Solution Capability | Turn confirmed intervention direction into capability combinations, Solution, Projection, and Measurement Plan |

This separates two dimensions:

> **The five-layer structure answers “at what business scale is the problem being analyzed?” The three Agents answer “which stable cognitive role performs each type of reasoning task?”**

The three Agents form one continuous reasoning chain, not three unrelated chatbots.

[Read METHOD →](docs/METHOD.md)

---

## 3. GOVERNANCE · How Agent Authority Is Bounded

Zhiji does not treat Agent output as business fact or final decision.

Enterprise analysis is long-chain and multi-layered, while an LLM remains probabilistic. If an error is allowed to enter the next stage without governance, it can compound through the rest of the chain.

Zhiji therefore separates **Agent, Human, and Workflow** into distinct authority roles:

```text
Agent
→ Analysis / Proposal / Hypothesis / Projection

Human
→ Fact / Constraint / Confirmation / Selection / Decision

Workflow
→ Legal State Transition / Gate / Recovery
```

### Core Boundaries

```text
Evidence ≠ Hypothesis

Proposal ≠ Accepted State

Human Approval ≠ Causal Proof

Projection ≠ Actual Outcome

Selection ≠ Execution Authorization
```

For an Agent judgment to become a state that downstream analysis is allowed to depend on, it must cross an explicit governance boundary:

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
Human Gate (when required)
          ↓
Accepted Domain State
          ↓
Deterministic Workflow
```

![Human-gated state transition](assets/human-gated-state-transition.svg)

### Real Semantic Proposal Path

A real semantic-generation path now exists in the production code:

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

The Agent can currently generate five bounded Semantic Proposal families:

```text
CURRENT_POSITION
TARGET_POSITION
CHANGE_LAWS
INTERVENTIONS
PROJECTION
```

These Proposals do not become Accepted State merely because they came from a real model.

At the same time:

- `R / θ / D / Ω` remain USER-owned;
- the Agent cannot silently rewrite USER-owned InternalState;
- invalid, dangling, stale, or contract-violating output fails closed;
- Agent authorship for Scenario / Solution Candidates is still deferred.

[Read GOVERNANCE →](docs/GOVERNANCE.md)

---

## 4. ARCHITECTURE · How METHOD + GOVERNANCE Are Enforced in Software

Zhiji is not a chain of prompts inside one long conversation. It separates **business state, semantic reasoning, deterministic computation, and process control into different software layers**.

The core rule is:

> **Probabilistic semantic judgment belongs to the Agent; deterministic state, computation, and process control belong to the software system.**

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
connected through Ports / Adapters
```

### Architecture Responsibilities

**Interaction**<br>
REST API, CLI, and structured result presentation.

**Application**<br>
Coordinates Case Intake, Journey progression, the three Agents, Semantic Generation, Confirm / Select, and Product Output.

**Workflow**<br>
Controls Layer, State, legal Actions, Human Gates, exceptional states, pause / resume, and does not depend on the LLM remembering “where we are.”

**Diagnosis Engine**<br>
Hosts deterministic operators such as Evidence Coverage, Diagnosis / Execution Readiness, Intervention Feasibility, Business Gap, Outcome Delta, and Scenario Constraint Evaluation.

**Domain**<br>
Defines the core business semantics and lifecycle for `System / Object / Position / InternalState / ChangeLaw / Intervention / Projection / Evidence`.

**Infrastructure**<br>
Connects PostgreSQL, migrations, Repository / UoW, real / Fake LLM Providers, trace, and recovery through Ports / Adapters.

A real model can enter the system only through controlled interfaces:

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

Therefore:

> **Conversation Context is not system state.**

![System architecture](assets/system-architecture.svg)

[Read ARCHITECTURE →](docs/ARCHITECTURE.md)

---

## 5. STATUS · What Actually Exists Today

The project uses four implementation states:

- **implemented** — production-path typed code exists;
- **fake-only** — that specific capability still exists only behind a Fake / deterministic seam;
- **contract-only** — a formal Contract / tests / boundary exists, but no production runtime implementation exists yet;
- **deferred** — explicitly outside the currently implemented scope.

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

The most important recent change is that:

```text
Real LLM Provider
        ↓
Agent Semantic Proposal
        ↓
Strict Validation
        ↓
Human Gate
```

is no longer only a planned capability. It is now a real, governed runtime path.

But:

```text
Real Semantic Proposal
≠
Autonomous Enterprise Diagnosis
```

### Current Known Limitation

Current Position still has one explicit interaction boundary:

```text
Agent drafts Position
        ↓
USER supplies / corrects InternalState
        ↓
Merged confirmation
```

This merged-confirmation path is not yet implemented, so the Current Position in the public Demo is still entered fully by the USER.

[Read detailed STATUS →](docs/STATUS.md)

---

## 6. DEMO · How a Real Model Enters the Product Loop

The current Public Demo is no longer only a deterministic Engineering Demo.

It uses a fictional enterprise case, generates bounded Semantic Proposals with a real model, and proceeds through the complete governed Product Loop:

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

The final Output explicitly preserves:

```text
No execution: true
No authorization: true
```

### Recommended Viewing

- **Public Cut** — ~2:53, for a quick view of real-model Proposal → Human Gate → cross-layer diagnosis → Solution → `PRODUCT_OUTPUT_READY`;
- **Engineering Demo** — 4:40, preserving more of the complete execution flow;
- **Raw `.cast` + Prompt / Response trace** — for verifying the underlying runtime and human–AI interaction facts.

[Watch / inspect the Demo →](demo/README.md)

### What the Demo Proves

The current Demo supports these engineering-level conclusions:

- a Business Case can enter the formal Product Loop;
- Business Diagnosis → Scenario Diagnosis → Solution Design can proceed continuously;
- Human Gates can control critical state transitions;
- Evidence and lineage can survive across stages;
- a Journey can be persisted and recovered;
- Product Output can be regenerated from persisted state;
- a real Provider can generate bounded, schema-valid Semantic Proposals;
- Agent Proposals can stop at the USER Gate as `HYPOTHESIS` and wait for confirmation;
- Solution Selection does not automatically acquire real-world execution authority.

### What the Demo Does Not Prove

It does not prove that:

- the Agent can autonomously perform high-quality enterprise diagnosis;
- real-enterprise diagnosis quality has been validated;
- stable cross-industry generalization has been demonstrated;
- a ChangeLaw has been causally proven;
- Projection equals Actual Outcome;
- a recommended solution will produce expected ROI;
- the system is production-ready.

> What the Demo proves is narrower: **a real model can enter the governance skeleton and produce bounded, reviewable, non-authoritative Proposals.**

### Demo Snapshot vs. Source Main

For readability, the Public Demo contains presentation information such as Layer / Step / Actor labels.

Formal Source Repository capability classification remains anchored to `main`; unmerged display-only changes do not alter Domain, Workflow, Authority, or capability-status conclusions.

---

## 7. EVIDENCE · What Engineering Facts Support These Claims

The current Public Evidence Snapshot is anchored to the Source Repository at:

```text
main commit: 49ec33c
date: 2026-09-24
```

That commit is `main` after PR #58 was merged.

GitHub Actions results for the corresponding identical Git tree:

| Verification | Result |
|---|---|
| Test collection | `2945` items |
| pytest | `2860 passed, 85 skipped` |
| strict mypy | `174` source files, zero issues |
| Ruff check | all checks passed |
| Ruff format | `655` Python files already formatted |
| PostgreSQL gate | `83 passed` × 2 |
| Python test files | `426` |

Two main GitHub Actions jobs:

```text
deterministic   PASS
postgres        PASS
```

These artifacts support one specific claim:

> **The engineering loop described publicly comes from actual execution rather than architecture diagrams alone.**

But because the Source Repository remains private:

> **Engineering Evidence ≠ Source-level Audit**

[See evidence snapshots →](evidence/)

---

## 8. Open Questions · What We Want Reviewers to Challenge

The external feedback Zhiji most needs is not:

> “This architecture looks reasonable.”

It is:

> **Once real Agents and real enterprises enter the system, which core assumption is most likely to fail first?**

Six questions are especially worth challenging:

1. **Semantic Authority**<br>
   How much semantic-generation authority should the Agent have? Which information may be inferred, and which must be supplied or confirmed by a Human?

2. **Human Gate Policy**<br>
   Should Gates primarily follow Workflow State, or should they gradually incorporate Semantic Risk, Evidence Strength, Reversibility, and Business Impact?

3. **Recursive Abstraction**<br>
   Is `Layer(n).Object = Layer(n+1).System` genuinely reusable across business structures, or is it an over-abstraction?

4. **Epistemic State**<br>
   Do Evidence, Hypothesis, Human Acceptance, Measurement, and Actual Outcome require a richer trust / epistemic-state model?

5. **Model Context vs. Audit Trace**<br>
   How can the model receive rich enough enterprise context without permanently persisting sensitive raw material in the trace, while still keeping the process auditable?

6. **Impact Propagation**<br>
   How should the effects of a local Intervention propagate toward Business / Enterprise levels? When is local improvement sufficient evidence of higher-level business value?

[Join the architecture review →](rfcs/RFC-001-ARCHITECTURE-REVIEW.md)

---

## 9. Next Validation

The project is no longer at:

```text
Engineering Loop
        ↓
waiting for Real Semantic Intelligence integration
```

It is closer to:

```text
Governed Engineering System
        +
Real Semantic Proposal Path
        ↓
waiting for real-business quality validation
```

The next validation sequence is:

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

Focus on:

- whether Agent Proposals remain stable, accurate, and information-adding on real business materials;
- how Agent Position + USER InternalState should be merged for Current Position confirmation;
- which Human Gates create real corrections and which begin to create Approval Fatigue;
- whether bounded Agent authorship should be opened for Scenario / Solution Candidates;
- whether Authority should vary with Evidence Strength / Reversibility / Business Impact.

### External Architecture Review

Actively challenge Recursive Diagnosis, Authority, Human Gates, Epistemic State, and Impact Propagation.

### Real Enterprise Case

For the first time, answer:

> **Is Zhiji not only a coherent software architecture, but also a useful enterprise decision tool?**

### Measurement & Feedback

If a solution enters real implementation, build the closed loop:

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

## 10. DevFlow · How Zhiji Was Developed AI-Natively

DevFlow is not a Zhiji business feature and is not presented as a productized general-purpose development platform.

It is a personal AI software-engineering control system developed and used during Zhiji's long-running development. Its purpose is to convert:

> **continuous collaboration that depends on chat context**

into:

> **an execution system constrained by explicit tasks, state, contracts, and engineering evidence.**

Role separation:

```text
Human
→ goals / boundaries / risk / final acceptance

ChatGPT
→ analysis / planning / review / problem diagnosis

Codex
→ code implementation / tests / repair

DevFlow
→ task orchestration / state control / evidence archiving

Terminal / CI
→ local execution / automated verification

Git / GitHub
→ version facts such as Branch / Commit / PR / Merge
```

Core control objects:

```text
Planning Artifact
Manifest
State
Task Contract
Artifact
```

The key DevFlow principle is:

> **Human sets direction; AI performs analysis and execution; DevFlow controls the process; engineering evidence and Human acceptance confirm the result.**

[Read DEVFLOW →](docs/DEVFLOW.md)

---

## 11. Explore

### Project Documents

- [METHOD · Method and Recursive Diagnosis](docs/METHOD.md)
- [GOVERNANCE · Human–AI Authority Boundaries](docs/GOVERNANCE.md)
- [ARCHITECTURE · Software Architecture](docs/ARCHITECTURE.md)
- [STATUS · Current Implementation and Validation Boundary](docs/STATUS.md)
- [DEVFLOW · AI-Native Software Engineering Control](docs/DEVFLOW.md)

### Architecture Review

- [RFC-001 · Architecture Review](rfcs/RFC-001-ARCHITECTURE-REVIEW.md)

### Demo & Evidence

- [Demo](demo/README.md)
- [Evidence](evidence/)

### Suggested Reading Path

```text
README
What is this, and why keep reading?
        ↓
METHOD
How does it actually analyze a business problem?
        ↓
GOVERNANCE
Why does AI output not directly become “truth”?
        ↓
ARCHITECTURE
How are those principles enforced in software?
        ↓
STATUS
What actually exists today?
        ↓
DEMO
How does a real model run inside the system?
        ↓
EVIDENCE
What engineering facts support the claims?
        ↓
RFC
Which core assumptions are most worth challenging?
```

---

## Reproducibility

The Source Repository remains private, so the Public Showcase Repo does not currently provide a source-level Quick Start.

It provides:

- Open Design;
- sanitized Engineering Evidence;
- Public / Engineering Demo;
- raw terminal / Prompt audit artifacts;
- Known Limitations;
- Architecture RFC.

A genuinely external Reproducible Run should be added if and when code or a runnable package is released.

---

## License

The license for the Public Showcase Repo should be finalized before formal publication.

Unless explicitly authorized, public design documents in this repository should not be interpreted as an open-source license for the Private Source Repository.

---

> **Zhiji Agent is still a system under active validation.**
>
> It does not assume the current architecture is the final answer.
>
> The real question this project is trying to test is:
>
> **Can the parts of complex enterprise decision-making that currently depend on experience, discussion, and tacit cognition be gradually turned into a software system that can express, simulate, confirm, validate, and evolve those decisions?**
