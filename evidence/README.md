# EVIDENCE · Engineering Evidence Index

[English](README.md) | [中文](README.zh-CN.md)

> This directory answers one question:<br>
> **What concrete engineering facts support the claims in the Public Repo that something has been implemented, run, or verified?**

This directory does not introduce a new product definition and is not another STATUS document.

Its only responsibilities are to provide:

- a verifiable Verification Snapshot;
- Demo run evidence;
- raw terminal and Prompt / Response audit artifacts;
- explicit boundaries for what the evidence does and does not support.

Therefore:

```text
docs/
define and explain
        ↓
STATUS
states what currently exists
        ↓
EVIDENCE
provides engineering facts supporting those claims
```

---

## 1. Evidence Principles

### 1.1 Evidence ≠ Claim

A sentence saying “implemented” is not, by itself, evidence.

Engineering claims should map, whenever possible, to one or more concrete facts such as:

```text
Commit / Git tree
CI result
Test result
Migration result
Runtime trace
Persisted output
Prompt / Response trace
```

### 1.2 Evidence ≠ Source-level Audit

The Private Source Repository remains private.

The Public Repo can therefore provide:

- runtime results;
- test and CI summaries;
- raw Demo traces;
- Prompt / Response audit traces;
- sanitized engineering evidence.

But this must not be interpreted as external reviewers having full source-level audit access.

```text
Engineering Evidence
≠
Source-level Audit
```

### 1.3 Demo Evidence ≠ Business Validation

A complete Demo can show that a system path actually ran, but it cannot prove that:

```text
Real enterprise diagnosis quality has been validated
Projection equals Actual Outcome
ChangeLaw has been causally proven
The recommended solution will produce the expected ROI
```

Those conclusions require later real-enterprise Cases, Measurement, and Actual Outcome evidence.

---

## 2. Current Evidence Snapshot

The current Public Evidence Snapshot is anchored to the Private Source Repository mainline:

```text
repository: XIAchong916/zhiji-agent
branch: main
commit: 49ec33c
date: 2026-09-24
```

This commit is `main` after PR #58 was merged.

The PR #58 CI ran on merge tree `aad34a8`. That merge tree and final `main` commit `49ec33c` share the same Git tree:

```text
tree: f3abf089a7689d48ca95a657ae82d8c2fb8b56f0
```

The current Verification Snapshot can therefore be associated with that `main` state.

See:

[verification-snapshot.md](verification-snapshot.md)

---

## 3. Deterministic Verification

The corresponding CI results are:

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

Both primary GitHub Actions jobs passed:

```text
deterministic   PASS
postgres        PASS
```

These results support the following engineering-level claims:

- the current code passes the ordinary deterministic quality gate;
- the PostgreSQL persistence / migration / integration path passes isolated verification;
- the current type-checking and Ruff gates are green;
- the Public Repo's claim that the engineering loop is not merely an architectural concept is grounded in actual execution evidence.

They do **not** directly prove:

- real-model diagnosis quality;
- cross-industry generalization;
- real business outcomes;
- production deployment readiness.

---

## 4. Demo Evidence · Real Product Loop Evidence

This directory also preserves raw engineering evidence from the current real-model Demo.

Directory:

```text
demo-product-loop-v3/
├── run-summary.md
├── zhiji_demo_product_loop_v3.cast
└── zhiji_demo_product_loop_v3.prompts.txt
```

### 4.1 `run-summary.md`

Explains:

- the Demo run conditions;
- the type of Case used;
- Provider / Persistence / Human Gate boundaries;
- major runtime stages;
- the final Output;
- known limitations;
- what the run can and cannot prove.

[Read the run summary →](demo-product-loop-v3/run-summary.md)

### 4.2 `.cast` · Raw Terminal Trace

`zhiji_demo_product_loop_v3.cast` is the raw asciinema v2 terminal recording.

Its purpose is to answer:

> **“What actually happened in the terminal?”**

rather than to reinterpret the product design.

It can be used to verify:

- the actual CLI execution sequence;
- Human input;
- where Agent Proposals appear;
- Human Gates;
- Layer / Journey progression;
- the final `PRODUCT_OUTPUT_READY` state.

[Raw terminal trace →](demo-product-loop-v3/zhiji_demo_product_loop_v3.cast)

### 4.3 `.prompts.txt` · Prompt / Response Audit Trace

`zhiji_demo_product_loop_v3.prompts.txt` preserves the Prompt / Response audit trace for the run.

It can be used to verify:

- which stages actually invoked semantic generation;
- which content came from the Agent;
- which actions were Human Confirm / Select operations;
- whether the Proposal stopped at the USER Gate;
- whether the run completed normally.

[Prompt / Response trace →](demo-product-loop-v3/zhiji_demo_product_loop_v3.prompts.txt)

> Public audit artifacts should contain only sanitized and security-reviewed material. They should not expose API keys, Authorization headers, `.env` contents, database credentials, or unnecessary local absolute paths.

---

## 5. Demo Evidence vs. Public Video

In the Public Repo:

```text
demo/
```

is responsible for:

> **Helping a reader understand how the system runs.**

Whereas:

```text
evidence/demo-product-loop-v3/
```

is responsible for:

> **Helping a reviewer verify that the run actually happened.**

The relationship is:

```text
Public Cut
quick understanding
        ↓
Engineering Demo
complete observation
        ↓
.cast + prompts
raw audit
```

The Public Cut can therefore be edited, but it should not be the only evidence.

The full Demo, Raw Terminal Trace, and Prompt / Response Trace together provide the lower-level verification surface.

[See the Demo →](../demo/README.md)

---

## 6. What Current Evidence Supports

Taken together, the current Verification Snapshot and Demo Evidence support the engineering-level conclusion that:

```text
Business Diagnosis
        ↓
Scenario Diagnosis
        ↓
Solution Design
```

can progress continuously within one Product Loop.

There is also evidence that:

- Workflow / State can continue to progress;
- Human Gates can control critical state transitions;
- Evidence and lineage can be preserved across stages;
- a Journey can be persisted and recovered;
- Product Output can be generated from persisted state;
- the PostgreSQL path runs and passes dedicated verification;
- a real OpenAI-compatible Provider can enter the Semantic Generation path;
- the Agent can generate bounded Semantic Proposals;
- Agent Proposals can stop at the USER Gate with `HYPOTHESIS` status;
- all five Agent Semantic Proposal families — `CURRENT_POSITION / TARGET_POSITION / CHANGE_LAWS / INTERVENTIONS / PROJECTION` — have been verified through a real-provider path;
- Solution Selection does not automatically create real-world execution authority;
- the final Output explicitly preserves:

```text
No execution: true
No authorization: true
```

---

## 7. What Current Evidence Does Not Support

The current public evidence does **not** support the following claims:

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

The following boundaries must remain explicit:

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

## 8. Evidence Directory

```text
evidence/
│
├── README.md
│   └── explains the evidence system and reading path
│
├── verification-snapshot.md
│   └── pytest / mypy / Ruff / PostgreSQL / CI evidence for the current main anchor
│
└── demo-product-loop-v3/
    │
    ├── run-summary.md
    │   └── run summary and validation boundaries for one real-model Product Loop
    │
    ├── zhiji_demo_product_loop_v3.cast
    │   └── raw terminal execution trace
    │
    └── zhiji_demo_product_loop_v3.prompts.txt
        └── Prompt / Response audit trace
```

---

## 9. Recommended Reading Order

For a first look at the project:

```text
../README.md
        ↓
../demo/README.md
        ↓
verification-snapshot.md
```

To verify one real-model run in more depth:

```text
demo-product-loop-v3/run-summary.md
        ↓
.cast
        ↓
.prompts.txt
```

To understand what these facts mean for the project's current capability state:

```text
Evidence
   ↓
../docs/STATUS.md
```

`STATUS.md` owns capability classification; Evidence owns factual support.

[Read STATUS →](../docs/STATUS.md)

---

> **The purpose of Evidence is not to make the project look “finished.” It is to give every engineering claim a clear, traceable factual basis.**
