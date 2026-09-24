# Demo Product Loop v3 · Run Summary

[English](run-summary.md) | [中文](run-summary.zh-CN.md)

> This file records one Zhiji Product Loop run with a **real model, PostgreSQL persistence, and Human-Gate-controlled progression**.<br>
> It answers: **what actually ran, what the Agent generated, what the Human decided, what the system produced, and what this run can and cannot prove.**

---

## 1. Run Identity

```text
Demo: Product Loop v3
Date: 2026-09-24
Runtime: local CLI
Case key: demo-vid-real3
Case: fictional · Qiyuan Technology
Business scenario: B2B lead-conversion efficiency diagnosis
Semantic provider: real model · TokenHub hy3
Persistence: PostgreSQL
Migration: Alembic head
Prompt / answer entries used: 242
Process exit code: 0
```

This was neither an in-memory simulation nor a playback of a fully pre-scripted set of deterministic answers.

The run combined:

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

to complete one full governed diagnosis Product Loop.

---

## 2. Case Boundary

The Demo uses a **fictional company: Qiyuan Technology**.

Diagnosis topic:

```text
B2B lead-conversion efficiency diagnosis
```

The case focuses on:

- lead-volume growth;
- MQL → SQL conversion;
- qualification-review latency;
- manual qualification-review cost;
- finding a feasible intervention path under existing CRM and human-review constraints.

The company, metrics, Evidence, and operating context in this case are Demo inputs.

Therefore:

```text
Fictional Case
≠
Real Customer Case
```

The result cannot be used as proof of real enterprise performance improvement.

---

## 3. Recording Provenance

The public evidence set includes:

```text
Raw terminal trace
Prompt / Response audit trace
Engineering Demo
Public Cut
```

Relationship:

```text
Public Cut
quick understanding of the runtime mechanism
        ↓
Engineering Demo
complete observation of the run
        ↓
.cast + prompts
verification of raw runtime facts
```

The Public Cut and full Engineering Demo come from the same v3 run. The Public Cut only compresses time and edits presentation; it does not alter the underlying run facts.

---

## 4. Run Flow

### 4.1 Case Intake

The run begins by entering the Case and diagnosis context.

Inputs include:

```text
Case key
Phase16 diagnosis inputs · 12
Module17 business-case inputs · 11
```

Together with the Case key, this is 24 human inputs.

These inputs form the Human-provided business context for the later Journey.

---

### 4.2 Controlled Entry

Before entering the formal Journey, the run passes two independent Human Decisions:

```text
Decision Proposal
PROCEED
        ↓
Independent Review
APPROVED
```

This demonstrates that:

> **“Recommend continuing analysis” and “formally authorize entry into the Journey” are not the same action.**

The Agent does not perform either gate by itself.

---

### 4.3 Object Confirmation

The OBJECT family is provided and confirmed by the Human.

Inputs include:

- Object;
- Object type;
- Business identity;
- Object objective summary.

This belongs to explicit Human Authority rather than allowing the model to invent the business identity.

---

### 4.4 Current Position

The Current Position in this Demo is **fully entered by the USER**.

It includes:

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

The reason is not that a model is inherently unable to describe Current Position. The current Authority / Interaction Contract requires:

```text
InternalState {R, θ, D, Ω}
=
USER-owned
```

The current product path cannot yet complete the following within one confirmation flow:

```text
Agent drafts Position
        ↓
USER supplies / corrects InternalState
        ↓
Merged confirmation
```

Therefore this Demo does not merge an Agent-drafted Position with USER-owned InternalState into one authoritative Current Position.

---

### 4.5 Target Position

Target Position is drafted by the real model.

Runtime path:

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

The Agent provides a Proposal, not an Accepted State.

---

### 4.6 ChangeLaw

ChangeLaw is drafted by the real model and then confirmed by a Human.

Governance relationship:

```text
Evidence
        ↓
Agent causal hypothesis
        ↓
ChangeLaw Proposal
        ↓
Human Gate
```

Human Confirm means only that:

> the current Journey may continue using this ChangeLaw as an analysis hypothesis.

It does not mean:

```text
ChangeLaw
=
Causally Proven Law
```

---

### 4.7 Intervention

`InterventionOption` is also drafted by the real model and passes through a Human Gate.

The Intervention references a confirmed `ChangeLaw` ID from the current Journey.

This prevents the model from freely inventing a target mechanism that cannot be grounded in the existing Journey.

Relationship:

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

The Solution stage presents two candidate solutions.

The Human performs:

```text
SELECT
        ↓
Candidate #1
        ↓
Selection rationale
```

An important boundary:

> **The Solution candidates in this run are produced by deterministic assembly, not by `RealLLMProposalAdapter`.**

Therefore this Demo does not support the claim that:

```text
Agent-authored Solution Candidate
=
implemented
```

The Human also records an explicit Selection rationale.

---

### 4.9 Constraint Review

After selecting the Solution, two Hard Constraints are confirmed individually.

Each constraint includes:

```text
constraint-satisfied
+
constraint-rationale
```

This ensures that before a Solution is accepted, it is explicitly checked against confirmed constraints rather than merely judged to “look good.”

---

### 4.10 Final Output

The run exits normally:

```text
exit code: 0
```

It reaches:

```text
PRODUCT_OUTPUT_READY
```

and produces:

```text
Business Diagnosis Package
Scenario Diagnosis Package
Solution Package
Complete lineage
```

The final Output explicitly preserves:

```text
No execution: true
No authorization: true
```

Therefore:

> **Completing diagnosis, solution design, and Solution Selection does not grant the system real-world execution authority.**

---

## 5. Human / Agent / System Attribution

### Human

The Human is responsible for:

- Case / business context;
- Object identity;
- Current Position and `R / θ / D / Ω`;
- `PROCEED`;
- independent `APPROVED` review;
- Confirm actions at all critical Semantic Gates;
- Solution `SELECT`;
- Selection rationale;
- Hard Constraint review.

### Agent / Real Model

The real model explicitly participates in:

- Target Position drafting;
- ChangeLaw drafting;
- Intervention drafting;
- governed Semantic Proposal Generation.

All of these enter the system as Proposal / HYPOTHESIS content.

### Deterministic System

Workflow / Application / Engine / Persistence are responsible for:

- current Stage;
- legal Actions;
- Proposal staging;
- Human Gate enforcement;
- identifier / schema boundaries;
- Layer / Journey progression;
- persistence;
- lineage;
- recovery;
- final Product Output assembly.

The run therefore implements:

```text
Agent
proposes candidate understanding

Human
provides real-world authority and key choices

System
provides deterministic governance and state progression
```

not:

```text
LLM
=
System Controller
```

---

## 6. What This Run Proves

This Demo can support the following claims:

- a Case can enter the full Product Loop;
- Business Diagnosis → Scenario Diagnosis → Solution Design can progress continuously;
- PostgreSQL can carry the run state rather than relying on one conversation context;
- a Human Gate can actually prevent an Agent Proposal from automatically becoming authoritative state;
- a real model can produce Semantic Proposals within the defined governance boundary;
- Target Position / ChangeLaw / Intervention can follow `Agent Draft → USER Gate → Confirm`;
- USER-owned InternalState is not silently overwritten by the Agent;
- a Solution Candidate can be explicitly selected with a preserved Selection rationale;
- Hard Constraints can be reviewed individually before solution confirmation;
- the run reaches `PRODUCT_OUTPUT_READY`;
- the final result preserves Business / Scenario / Solution Packages and lineage;
- the process exits normally with `exit code 0`;
- Solution Selection is not interpreted as Execution Authorization.

The most important evidence increment is that the following sequence occurs within one actual run:

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

---

## 7. What This Run Does Not Prove

This Demo does **not** prove:

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

The following boundaries must remain explicit:

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

## 8. Known Limitation

The clearest interaction limitation exposed by this Demo is Current Position.

The desired flow is:

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

The current path cannot yet complete this merge.

Therefore this run uses:

```text
USER provides Current Position
+
USER provides InternalState
```

before continuing through the rest of the flow.

This is not a Demo flaw to hide. It is a real boundary of the current Product Interaction / Authority Contract.

---

## 9. Raw Evidence

### Terminal Trace

[zhiji_demo_product_loop_v3.cast](zhiji_demo_product_loop_v3.cast)

Use it to verify:

- the actual CLI execution order;
- Human input;
- Gates;
- Layer / Journey progression;
- terminal state.

### Prompt / Response Audit Trace

[zhiji_demo_product_loop_v3.prompts.txt](zhiji_demo_product_loop_v3.prompts.txt)

Use it to verify:

- Prompt / answer sequence;
- Agent draft accept / decline paths;
- Human Confirm / Select;
- manual Current Position input;
- Solution selection rationale;
- Constraint review;
- `exit code: 0`.

---

## 10. Relationship to Other Public Evidence

```text
run-summary.md
explains this actual run
        ↓
.cast / prompts
provide raw runtime evidence

verification-snapshot.md
proves deterministic engineering gates for the code tree

docs/STATUS.md
maps these facts to capability status

demo/README.md
explains how a first-time viewer should watch the Demo
```

Therefore:

- for a **quick understanding** → read `demo/README.md` + watch the Public Cut;
- for a **complete observation** → watch the Engineering Demo;
- to **verify this run** → read this file + `.cast` + `.prompts.txt`;
- to **understand overall project maturity** → read `docs/STATUS.md`.

[Back to Evidence Index →](../README.md)

[See Verification Snapshot →](../verification-snapshot.md)

[See Public Demo Guide →](../../demo/README.md)

---

> **The value of this run is not that it proves AI can replace enterprise decision-makers. It proves that a real model can participate in an actual Product Loop inside a governed, persistent, reviewable decision-software system with explicit Human–AI authority boundaries.**
