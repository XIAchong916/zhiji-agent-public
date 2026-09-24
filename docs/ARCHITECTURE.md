# ARCHITECTURE · Zhiji Software Architecture

[English](ARCHITECTURE.md) | [中文](ARCHITECTURE.zh-CN.md)

> This document answers one core question:<br>
> **How are the analytical method defined in METHOD and the human–AI authority boundaries defined in GOVERNANCE implemented as a runnable, recoverable, traceable software system?**

Zhiji's architecture was not designed by starting from “how do we call an LLM?”

It first accepts two upstream constraints:

```text
METHOD
Defines how the business world is represented and simulated
        ↓
GOVERNANCE
Defines who may propose, confirm, and change what
        ↓
ARCHITECTURE
Turns those constraints into software boundaries and runtime rules
```

The core architectural goal is therefore not to let the Agent do as much as possible. It is to satisfy two conditions at the same time:

> **Allow probabilistic semantic capability to enter the system;<br>
> while keeping business state, authority, and process correct independently of the model itself.**

This creates Zhiji's most important software-design principle:

> **Probabilistic semantic judgment belongs to the Agent; deterministic state, computation, and process control belong to the software system.**

---

## 1. Architecture at a Glance

![Zhiji system architecture](../assets/system-architecture.svg)

> This diagram shows how METHOD and GOVERNANCE become software layers: semantic capability enters through a controlled boundary; Workflow / Engine / Domain retain deterministic governance; Persistence plus Evidence / Lineage / Recovery make execution recoverable and auditable.

Zhiji can be summarized as:

```text
Interaction Surface
CLI / API / future UI
        ↓
Application Services
        ↓
Deterministic Workflow
        ↓
Diagnosis Engine
        ↓
Domain Model
        ↓
Persistence

      ↘ Semantic Proposal Boundary
      ↘ Evidence / Lineage
      ↘ Recovery / Audit
```

These layers are not arbitrary technical decomposition. Each carries a distinct responsibility.

| Layer | Core responsibility |
|---|---|
| **Interaction** | Accept Human input and present actions currently allowed by the system |
| **Application** | Coordinate Case, Journey, Proposal, Confirm / Select, cross-Agent handoff, and Product Output |
| **Workflow** | Deterministically control current state, legal transitions, Human Gates, pause, and recovery |
| **Engine** | Provide deterministic, industry-agnostic diagnostic operators |
| **Domain** | Represent the objects, states, relationships, and lifecycle allowed in the business model |
| **Persistence** | Store Domain, Journey, Decision, Evidence lineage, and recovery information |
| **Semantic Proposal Boundary** | Allow Agent / LLM to generate non-authoritative semantic Proposals without bypassing governance |

The key separation is:

```text
Semantic Intelligence
responsible for understanding, generating, simulating

Deterministic System
responsible for constraints, records, state transitions, recovery
```

The two collaborate, but they must not collapse into one “universal Agent.”

---

## 2. Domain Model · Turning METHOD into Persistent Business Objects

METHOD uses conceptual language:

```text
System
→ Object
→ Position
→ InternalState
→ ChangeLaw
→ Intervention
→ New Position
```

Architecture first turns these concepts into explicit, validatable, referencable software objects.

Current core Domain types include:

```text
SystemDefinition
SystemBoundary
SystemRule

ObjectDefinition

PositionSnapshot
InternalStateSnapshot

ChangeLaw
InterventionOption
PositionProjection

Observation

SourceDocument
EvidenceItem
EvidencePack
```

These objects do not exist merely to “structure everything.” They allow the system to distinguish:

> **Are we storing a fact, a current state, a causal hypothesis, an intervention option, or a future forecast?**

### 2.1 Actual State and Forecast Must Stay Separate

Position is an important example.

The architecture deliberately separates:

```text
PositionSnapshot
versioned representation of actual/current state

PositionProjection
pre-implementation forecast

Observation / Measurement
post-implementation raw observation

ActualOutcome
reviewed result derived from Measurement
```

Therefore this transition is forbidden:

```text
PositionProjection
      ↓
automatically becomes
ActualOutcome
```

Selecting a solution also cannot write its projected result as though it had already happened.

This makes the GOVERNANCE rule:

```text
Projection ≠ Actual Outcome
```

true at the software-object level.

### 2.2 Domain Describes Business Semantics Only

Domain defines:

- which objects may exist;
- which relationships are allowed;
- local invariants of each object type;
- lifecycle and epistemic status.

For example:

```text
System CONTAINS Object

Object HAS PositionSnapshot
Object HAS InternalStateSnapshot

ChangeLaw DESCRIBES change mechanism

Intervention ACTS_ON selected variables / mechanism

Intervention PRODUCES PositionProjection

Observation SUPPORTS later assessment
```

Domain does **not** own:

- database I/O;
- API;
- CLI;
- UI;
- model calls;
- Prompt construction;
- Workflow orchestration.

The lower-level Domain therefore does not need to know which model, database, or interaction surface is used above it.

That separation is the basis for independent evolution of the layers.

---

## 3. Diagnosis Engine · Deterministic Work Is Not Left for the LLM to Guess

Some METHOD tasks require semantic judgment, for example:

- what Position the enterprise may currently occupy;
- whether a ChangeLaw may explain the state;
- which Interventions are worth proposing.

But another class of tasks should produce stable, repeatable results once their inputs are fixed.

Those tasks should not depend on the model “making the judgment again.”

Current deterministic operators in `packages/engine/` include:

```text
evidence_coverage
diagnosis_readiness
execution_readiness
intervention_feasibility
business_gap
outcome_delta
scenario
```

They cover work such as:

- Evidence coverage, gaps, and conflicts;
- whether diagnostic information is sufficient to proceed;
- execution-readiness and provenance chain;
- whether an Intervention satisfies constraints;
- gap between current Position and benchmark;
- delta calculation for outcome metrics;
- Scenario benchmark and hard-constraint evaluation.

The Engine principle is:

> **If a judgment can be deterministically computed from explicit inputs and rules, do not leave it to free-form LLM reasoning.**

Therefore:

```text
Agent
infers in open semantic space

Engine
calculates in deterministic rule space
```

This is also why the three Agents do not maintain three separate “business algorithms.”

Agent is a cognitive role. Engine is shared computational capability.

---

## 4. Workflow · Turning GOVERNANCE into Deterministic State Transition

With only Domain + Agent, a complex Journey can easily degrade into:

```text
Human
  ↕
Agent
  ↕
Long Conversation
```

and then depend on the model remembering:

- where the analysis currently is;
- what has been confirmed;
- what is still only a Proposal;
- whether the next step is legal.

Zhiji therefore uses an explicit Workflow State Machine to manage process state.

### 4.1 Workflow Manages Process State

Workflow controls:

- current Layer;
- current State;
- which Proposals exist;
- which results are Confirmed;
- which Actions are legal next;
- which transitions require Human Gate;
- whether more Evidence is required;
- whether upper-layer Review is required;
- whether a failure is retryable;
- whether the current Layer is complete.

It manages:

```text
Process State
```

not:

```text
Object.InternalState
```

That distinction is critical.

For example:

```text
CHANGE_LAWS_PROPOSED
```

is a process state, while:

```text
ChangeLaw
```

is a Domain record.

Workflow may control whether confirming a ChangeLaw is legal. It cannot treat its own Workflow State as business fact.

### 4.2 Both Happy Path and Exceptional Paths Must Be Explicit

A representative normal path includes:

```text
DRAFT
→ MATERIALS_COLLECTED
→ SYSTEM_PROPOSED
→ SYSTEM_CONFIRMED
→ OBJECT_PROPOSED
→ OBJECT_CONFIRMED
→ CURRENT_POSITION_PROPOSED
→ CURRENT_POSITION_CONFIRMED
→ TARGET_POSITION_PROPOSED
→ TARGET_POSITION_CONFIRMED
→ CHANGE_LAWS_PROPOSED
→ CHANGE_LAWS_CONFIRMED
→ INTERVENTIONS_PROPOSED
→ INTERVENTION_SELECTED
→ RESULT_GENERATED
→ LAYER_COMPLETED
```

The architecture is not defined only by its Happy Path.

It also explicitly represents:

```text
MORE_EVIDENCE_REQUIRED
UPPER_LAYER_REVIEW_REQUIRED
FAILED_RETRYABLE
FAILED_TERMINAL
```

as well as:

- rejection;
- resume;
- retry;
- hard-constraint failure.

In a complex decision system:

> **“Cannot proceed yet” is itself a legitimate state.**

### 4.3 Fail Closed

A key Workflow rule is:

```text
Unlisted transition
        ↓
     Forbidden
```

That is:

> **Any state transition that is not explicitly allowed is rejected.**

Governance therefore does not depend on a Prompt reminding the model about the rules.

Even if the Agent produces a structurally valid and linguistically plausible Proposal, the system still will not transition if the current Workflow State does not allow that next step.

> **Governance is not “tell the model not to overstep.” It is “make overstepping have no legal system path.”**

### 4.4 Human Gate Is a Workflow Constraint, Not Model Self-restraint

Existing Workflow Contracts explicitly restrict mandatory human approval by actor.

Gates that require human authority can only be completed by:

```text
ActorType.USER
```

At the same time:

```text
Approval
≠ Workflow Transition Audit
≠ Epistemic Status
```

Human Approval may allow the process to continue, but it does not automatically change the epistemic status of a ChangeLaw.

This implements the GOVERNANCE rule:

```text
Human Approval ≠ Causal Proof
```

as a software rule.

---

## 5. Application Services · Assembling the Layers into a Real Product Loop

Domain, Engine, and Workflow each solve local concerns.

Application coordinates them into the continuous behavior experienced by the user.

A simplified flow is:

```text
Case Intake
    ↓
Start / Resume Journey
    ↓
Obtain Semantic Input / Proposal
    ↓
Validate
    ↓
Confirm / Reject / Select
    ↓
Progress Workflow
    ↓
Complete Layer
    ↓
Handoff to Next Layer / Agent
    ↓
Complete Case Design
    ↓
Generate Product Output
```

Current Application services include real paths for:

```text
journey_orchestrator
journey_proposal_progression
product_delivery_loop
business traversal / handoff
scenario portfolio
scenario → solution handoff
solution journey
```

These services are not merely “glue code.” They prevent interaction layers from directly mutating lower-level state.

The intended direction is:

```text
CLI / API
   ↓
Application Service
   ↓
Workflow + Domain Contract
```

not:

```text
CLI / LLM
   ↓
write directly to database
```

---

## 6. Semantic Proposal Boundary · How LLM Capability Enters the System

Zhiji does not place the LLM at the center of system control.

The model enters the architecture as a:

> **Semantic Proposal Provider**

It generates candidate judgments in open semantic space, while existing Authority Contracts continue to govern what those judgments can become.

Semantic families in the broader model include:

```text
SYSTEM
OBJECT
CURRENT_POSITION
TARGET_POSITION
CHANGE_LAWS
INTERVENTIONS
PROJECTION
SCENARIO_CANDIDATES
SOLUTION_CANDIDATES
```

But the families do not share the same authority.

The semantic model already distinguishes statuses such as:

```text
USER_FACT
CONFIRMED
INHERITED
HYPOTHESIS
```

For example:

- `PositionSemantics` can represent an evidence-grounded hypothesis;
- `ChangeLawSemantics` can represent a causal hypothesis;
- `InterventionSemantics` can represent a proposed intervention;
- `ProjectionSemantics` is a non-authoritative forecast;
- `ObjectIdentitySemantics` cannot be freely invented by the provider;
- `InternalStateSemantics` keeps `R / θ / D / Ω` explicitly USER-owned.

Real-model integration therefore does not require a new governance system. It enters the existing chain:

```text
Authorized Context + Evidence
          ↓
   Semantic Provider
          ↓
 Proposal / Hypothesis
          ↓
 Schema / lineage validation
          ↓
 Workflow + Human Gate
          ↓
Accepted Domain State
```

Core principle:

> **Increasing Semantic Intelligence does not automatically increase model Authority.**

### 6.1 Current Reality · Real Provider and Governed Semantic Generation Are Implemented

The current architecture must distinguish several separate facts rather than collapsing them into one “LLM integration” label.

First, the older proposal/review seam still exists:

```text
LLMProposalInput
→ proposal adapter
→ raw proposal
→ strict validation
→ typed candidate
→ PROPOSED Domain record
→ Workflow proposal command
→ safe trace metadata
```

That specific proposal-only `LLMProposalAdapter` path remains fake-only in default production composition.

Separately, a real OpenAI-compatible completion path is implemented through `LLMCompletionAdapter` and the real provider adapter. `SemanticGenerationService` can use that boundary to generate five evidence-grounded semantic proposal families:

```text
CURRENT_POSITION
TARGET_POSITION
CHANGE_LAWS
INTERVENTIONS
PROJECTION
```

The runtime path is:

```text
Confirmed Context / Evidence
        ↓
Real LLM Provider
        ↓
Untrusted raw semantic output
        ↓
Strict parsing / identifier validation
        ↓
HYPOTHESIS / AGENT Proposal
        ↓
USER Gate
```

All five families have been exercised end-to-end against a real OpenAI-compatible provider. Ordinary CI intentionally does not make live provider calls; provider verification remains opt-in and does not imply production readiness.

Two authority boundaries remain unchanged:

- `R / θ / D / Ω` are USER-owned. The Agent cannot author or silently overwrite them;
- Agent-authored Scenario / Solution Candidate semantics are still deferred because their current authority contracts remain USER-owned.

There is also a current interaction limitation for Current Position: the system can stage an Agent-drafted Position, but the current confirmation path does not yet merge that draft with USER-supplied InternalState in one confirmation transaction. The public Demo therefore uses a fully USER-entered Current Position.

So the architecture now demonstrates more than “how a model should enter the system.” It demonstrates a real governed model path—but still not autonomous enterprise diagnosis or validated semantic quality.

### 6.2 Runtime Context and Persisted Trace Are Separate

A real LLM needs enough business semantics to perform useful diagnosis.

But long-term Trace should not persist every piece of sensitive source material verbatim.

The architecture therefore distinguishes:

```text
Ephemeral Authorized Runtime Context
        ↓
Remote / Local Model
        ↓
Raw Proposal
        ↓
Validation
        ↓
Safe Digest / Structured Result / Metadata
        ↓
Persisted Trace
```

The trace design intentionally preserves information such as:

- record / evidence ID;
- lifecycle / source metadata;
- hash;
- length;
- safe summary;

rather than automatically writing all raw enterprise text into long-lived trace storage.

Therefore:

> **Model runtime context and long-term system audit trace are different data planes.**

That boundary matters for privacy, security, and auditability with real enterprise data.

---

## 7. Persistence & Recovery · Conversation Is Not System State

A business-diagnosis Journey may span:

- multiple Agents;
- multiple Layers;
- multiple Human Gates;
- multiple conversations;
- process restarts.

Zhiji therefore cannot treat model context as system memory.

Core state must be persisted independently, including:

```text
Domain records
Journey / Workflow state
Human decisions
Evidence lineage
Case / Journey binding
Selections
Completion records
Recovery information
```

The current Product Loop has a persistence path based on:

```text
PostgreSQL
SQLAlchemy
Alembic migrations
```

and supports restart / recovery.

Therefore:

> **Conversation Context is not system state.**

Even if:

- a chat window disappears;
- the model changes;
- the program exits;
- the runtime restarts;

confirmed Domain State and Journey progress can still be recovered from persisted facts.

### 7.1 Recovery Depends on Facts, Not Re-inference of History

Recovery should rely on:

```text
persisted Journey state
Domain records
decision / transition audit
lineage
current authoritative head
```

not ask an LLM to infer from a narrative summary:

> “Where were we approximately?”

This is why:

> **Narrative Summary cannot be the authoritative reconstruction source for Domain State.**

---

## 8. Evidence & Lineage · Why Final Results Remain Traceable

Zhiji does not only need to preserve:

> which solution was selected.

It must also answer:

```text
Where did this state come from?
Which Proposal produced it?
Which Evidence supported it?
Who confirmed it?
Which Workflow State did it pass through?
Was it later superseded?
```

The system therefore keeps explicit lineage connecting:

```text
Evidence
   ↓
Proposal
   ↓
Review / Confirmation
   ↓
Accepted State
   ↓
Selection
   ↓
Derived Product Output
```

Across Layers, StatePackage also relies on explicit source references rather than copying a provenance-free narrative summary.

This preserves both METHOD's recursive inheritance and GOVERNANCE's Authority Separation in software.

---

## 9. Dependency Direction · Why Lower Layers Must Not Know the Upper Layers

Zhiji maintains a clear dependency direction:

```text
domain
  ↑
engine
  ↑
workflows
  ↑
application
  ↑
API / CLI / UI
```

The lower the layer:

> **the more stable it should be, and the less it should know about upper-layer implementation details.**

Domain should not need to know:

- whether the user interacts through CLI or Web;
- which LLM is used;
- whether persistence is PostgreSQL or another implementation;
- which API framework is used;
- how the Product UI presents a state.

This one-way dependency yields two important properties.

First:

> model Providers can be replaced without rewriting Domain.

Second:

> interaction surfaces can change without changing core business semantics or Workflow Authority.

Rapidly changing AI / UI capability therefore does not contaminate the most stable business model.

---

## 10. Runtime Separation · What Must Stay Distinct During Execution

The architecture ultimately protects separation among these runtime concepts:

```text
Domain State
business state the system is currently allowed to depend on

Semantic Proposal
non-authoritative candidate generated by Agent / LLM

Human Decision
real-authority event such as confirmation, selection, approval

Position Projection
pre-implementation forecast

Measurement / Actual Outcome
post-implementation real-world result
```

They cannot automatically substitute for one another.

A representative execution path is:

```text
Persisted Domain State + Evidence
             ↓
Authorized Runtime Context
             ↓
Semantic Proposal
             ↓
Validation
             ↓
Human Decision
             ↓
Workflow Transition
             ↓
New Accepted Domain State
             ↓
Position Projection
             ↓
[future real implementation]
             ↓
Observation / Measurement
             ↓
Actual Outcome
```

This is where METHOD, GOVERNANCE, and ARCHITECTURE converge.

---

## 11. Architecture Boundary · What This Architecture Does Not Claim

ARCHITECTURE describes:

> **How the current design organizes business semantics, AI Proposals, Human Authority, Workflow, and Persistence into a governed software system.**

It does not imply that the following have been established:

```text
Real LLM semantic quality is validated             ✗

Real enterprise diagnosis is validated             ✗

Cross-industry generalization is validated         ✗

Production deployment is complete                  ✗

Autonomous external execution is authorized        ✗
```

Likewise:

> a capability existing in code does not automatically mean it is production-ready.

Exact implementation status belongs in:

```text
docs/STATUS.md
```

Actual runtime behavior belongs in:

```text
demo/README.md
```

Engineering facts belong in:

```text
evidence/
```

---

## 12. Summary · The Core of the Architecture

Zhiji's architecture can be compressed into one sentence:

> **Domain preserves business semantics, Engine performs deterministic computation, Workflow controls legal state transitions, Application organizes the complete Journey, Semantic Provider supplies non-authoritative intelligence, Human provides real-world authority, and Persistence plus Lineage make state independent of the conversation and recoverable / traceable.**

Further compressed:

```text
LLM proposes
Human authorizes
Workflow enforces
Engine calculates
Domain represents
Persistence remembers
Evidence explains
```

These responsibilities cannot simply be merged into a “stronger Agent.”

The architecture is not designed to restrict AI. It is designed to:

> **let AI continue to become stronger inside a system with clear boundaries, without requiring business fact, authority, and state governance to be rewritten every time model capability improves.**

---

## 13. Relationship to Other Documents

This document answers only “how METHOD and GOVERNANCE become a software system.” It does not repeat the full business method or mix implementation status and verification evidence into architecture definition.

| If you want to verify... | Read... |
|---|---|
| How System / Object / Position / ChangeLaw / Intervention and five-layer recursion work | `docs/METHOD.md` |
| What authority Agent, Human, and Workflow have; how Proposal becomes Accepted State | `docs/GOVERNANCE.md` |
| Which capabilities are implemented / fake-only / deferred | `docs/STATUS.md` |
| How a real Product Loop passes through these layers | `demo/README.md` |
| Whether Persistence / recovery / Workflow / E2E actually ran | `evidence/` |
| Where to challenge Semantic Authority, Human Gate, Epistemic State, and other assumptions | `rfcs/RFC-001-ARCHITECTURE-REVIEW.md` |

Overall relationship:

```text
METHOD
defines “how business problems are analyzed”
        ↓
GOVERNANCE
defines “who may decide what”
        ↓
ARCHITECTURE
defines “how software enforces it”
        ↓
STATUS / DEMO / EVIDENCE
show “what actually exists today”
```
