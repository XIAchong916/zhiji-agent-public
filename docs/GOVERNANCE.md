# GOVERNANCE · Human–AI Governance and Decision Authority

[English](GOVERNANCE.md) | [中文](GOVERNANCE.zh-CN.md)

> This document answers one core question:<br>
> **When the Agent itself is probabilistic, how can Zhiji let it participate in complex enterprise decisions without allowing model-generated content to directly become business fact, system state, or real-world execution authority?**

Zhiji does not try to eliminate uncertainty by simply “making the model smarter.”

It takes a different approach:

> **Put different kinds of information, judgment, and decision into different authority layers, and make state transitions, human confirmation, and evidence provenance explicit.**

The central governance question is therefore not:

> “Can AI make judgments?”

It is:

> **“What may AI propose? Who may confirm what? What may the system do after confirmation? What must still wait for real-world validation?”**

The governance chain can be summarized as:

```text
Evidence / Confirmed State
          ↓
    Agent Reasoning
          ↓
 Proposal / Hypothesis
          ↓
      Human Gate
          ↓
 Accepted Domain State
          ↓
Deterministic Workflow
          ↓
 Next Analysis Step
          ↓
 Projection / Decision
          ↓
     Measurement
          ↓
   Actual Outcome
```

## Governance at a Glance

![Human-gated state transition](../assets/human-gated-state-transition.svg)

> A Human Gate is used only where real-world authority is required. The Agent produces candidate judgments, Workflow enforces legal state transitions, and Accepted Domain State is the authoritative state that later analysis is allowed to depend on.

---

## 1. Why Governance

The biggest difference between complex enterprise decision-making and ordinary question answering is not only that the questions are harder. It is that:

> **A judgment from one step becomes input to the next step.**

If the chain proceeds through:

```text
Business Diagnosis
        ↓
Scenario Diagnosis
        ↓
Solution Design
        ↓
Projection
        ↓
Decision
```

then an error that is not explicitly typed and governed can compound through every downstream step.

Typical risks include:

- facts and inferences being mixed together;
- the Agent filling missing information and later stages treating the completion as fact;
- Human acceptance of a judgment being misread as proof that the judgment is true;
- Projection being treated as Actual Outcome;
- selection of a solution being misread as real-world execution authority;
- lower-layer analysis unintentionally overwriting confirmed upper-layer conditions;
- a broken conversation forcing the system to rely on what the model “remembers.”

Zhiji therefore separates three questions:

```text
Who produces a judgment?
Who has authority to confirm it?
Who makes process continuation legal?
```

These map to three primary responsibility holders:

```text
Agent
Human
Workflow
```

Formal business state that has entered the system is carried by the Domain.

---

## 2. Four Different Things That Must Not Be Collapsed

The first step in governance is not “add more approvals.” It is to distinguish semantics.

At minimum, Zhiji separates four categories.

### 2.1 Evidence

Evidence describes:

> **What source-supported information do we currently have?**

It may come from:

- user input;
- enterprise materials;
- data;
- external sources;
- existing business records;
- Measurement.

Evidence does not automatically imply a business conclusion. It provides source-grounded material for later judgment.

---

### 2.2 Hypothesis / Proposal

Based on Evidence and confirmed state, the Agent may form:

- Position Hypothesis;
- ChangeLaw Hypothesis;
- Intervention Proposal;
- Candidate Scenario;
- Solution Option;
- Position Projection.

These begin as:

```text
Machine Proposal
```

not:

```text
Accepted Business State
```

Therefore:

> **What the model generated and what the system accepts are different things.**

---

### 2.3 Accepted Domain State

After a Proposal passes legal validation and any required Human Gate, it may become a state that downstream analysis is allowed to depend on.

“Accepted” means:

> **The system allows this judgment to be used as a formal analytical basis for the current Journey.**

It does **not** mean:

> “This judgment has become an unquestionable truth about the real world.”

The phrase `Domain Truth` used in some public descriptions is more precisely understood as:

> **Authoritative Domain State — the currently authoritative business state inside the system.**

It is system authority, not ultimate truth.

---

### 2.4 Actual Outcome

A real-world outcome cannot be created by a Proposal or by Human Approval.

For example:

```text
Intervention
      ↓
Position Projection
```

only expresses:

> what may happen if the assumptions and intervention hold.

The real:

```text
Actual Outcome
```

must come from later Observation / Measurement and the corresponding evaluation.

Therefore:

```text
Projection ≠ Actual Outcome
```

is one of Zhiji's most important long-term governance boundaries.

---

## 3. Authority Separation · Who Can Decide What

Zhiji separates authority across three sources:

```text
Agent
→ Proposal / Hypothesis / Analysis

Human
→ Fact / Constraint / Selection / Decision

Workflow
→ Legal State Transition / Gate Enforcement / Audit
```

Accepted business state is stored by the Domain as:

```text
Authoritative Domain State
```

These roles interact, but none substitutes for another.

### 3.1 Agent · Produces Candidate Cognition

The Agent's job is to advance understanding, not to become a source of fact by itself.

Appropriate Agent responsibilities include:

- information organization;
- Evidence organization;
- gap identification;
- Position analysis;
- ChangeLaw hypothesis generation;
- Candidate generation;
- Intervention proposal;
- Projection reasoning;
- explanation.

A typical Agent output is:

```text
Proposal / Hypothesis
```

The Agent **cannot automatically do any of the following merely because it generated a conclusion**:

- promote a Proposal into Accepted State;
- modify confirmed historical state;
- change Human-confirmed objectives or hard constraints;
- promote a ChangeLaw into a proven causal law;
- write Projection as Actual Outcome;
- interpret Solution Selection as real-world execution authorization.

Core rule:

> **The Agent may expand the candidate space, but it cannot unilaterally expand its own authority.**

---

### 3.2 Human · Holds Real-world Authority

Humans provide authority for information the Agent cannot legitimately obtain or decide on its own.

This primarily includes:

- real-world facts;
- objectives and priorities;
- constraints and risk boundaries;
- supplementation and confirmation of Evidence;
- selection of key analytical paths;
- selection among Candidates / Interventions / Solutions;
- whether to enter the next layer or phase.

The Human's role is not to review every generated sentence word by word.

More precisely:

> **Humans assume responsibility at points where real-world authority is required.**

Human authority also has limits.

A Human may say:

> “This ChangeLaw may be used as the working assumption for the current decision.”

That does not mean:

> “This ChangeLaw has been proven as a true causal mechanism in the real world.”

Therefore:

```text
Human Approval ≠ Causal Proof
```

---

### 3.3 Workflow · Enforces Deterministic Governance

Workflow does not perform business reasoning.

It controls:

- current Layer;
- current Workflow State;
- which Actions are legal next;
- which Proposals exist;
- which results have been confirmed;
- whether a Human Gate is required;
- whether more Evidence is required;
- whether upper-layer review is required;
- whether progression into the next phase is legal;
- how failure can be recovered.

Workflow manages:

```text
Process State
```

not:

```text
Business InternalState
```

The two must stay separate.

Workflow cannot confirm a business judgment because it “looks reasonable,” and it cannot silently mutate Domain records.

Its responsibility is:

> **Deterministically enforce a governance rule that has already been defined.**

---

## 4. Human Gates · Where Human Intervention Is Required

Zhiji does not interpret Human Gate as “ask a person to click confirm on every step.”

A Human Gate exists to stop the analytical chain where real-world authority cannot be delegated to the Agent.

The method currently groups Human Gates into four categories.

### 4.1 Fact Gate

Answers:

> **Is this actually true in the real world?**

Examples:

- enterprise / business identity;
- current operating state;
- data definitions;
- critical facts extracted from materials;
- whether an Evidence item applies to the current Case;
- whether the Current Position description matches reality.

The Agent may organize and extract candidate facts, but generation alone cannot promote them into confirmed facts.

---

### 4.2 Goal / Constraint Gate

Answers:

> **What are we actually optimizing, and which boundaries must not be crossed?**

This includes:

- business objectives;
- stage priorities;
- time horizon;
- budget;
- capability conditions;
- risk boundaries;
- compliance requirements;
- business rules.

These directly affect:

```text
R / θ / D / Ω
```

If the Agent could change these conditions by itself, it would no longer be acting only as an analyst—it would be acting as a decision-maker.

These states therefore remain under Human Authority.

---

### 4.3 Evidence Gate

Answers:

> **Do we have enough evidence to continue?**

When a critical judgment lacks necessary Evidence, Zhiji does not require the Agent to “complete the missing information.” It allows the process to stop:

```text
Evidence Gap
     ↓
MORE_EVIDENCE_REQUIRED
     ↓
Request Evidence
     ↓
Human Provides / Confirms
     ↓
Resume
```

An important principle is:

> **Insufficient information is a valid system state, not an exception that the model must fill in.**

---

### 4.4 Decision Gate

Answers:

> **Among multiple feasible directions, which path do we actually choose now?**

Examples:

- whether to continue the current diagnosis;
- which business direction deserves deeper analysis;
- which Candidate Scenario is selected;
- which Intervention should proceed;
- which Solution Option is accepted;
- whether to enter the next analytical layer.

These choices create:

```text
Human Decision
```

but still do not automatically create:

```text
Execution Authorization
```

---

## 5. Proposal → Accepted State · How an Agent Judgment Enters Formal State

The critical governance chain is not:

```text
Prompt
  ↓
Answer
```

It is:

```text
Evidence / Confirmed State
          ↓
Authorized Context
          ↓
Agent Analysis
          ↓
Proposal / Hypothesis
          ↓
Structural Validation
          ↓
Human Gate (when required)
          ↓
Accepted Domain State
          ↓
Deterministic Workflow
          ↓
Next Analysis Step
```

Each step answers a different question.

**Agent Analysis**

> What might be true?

**Validation**

> Does this output satisfy structural, provenance, and current-state requirements?

**Human Gate**

> Does this judgment have the real-world authority required to enter the system?

**Accepted Domain State**

> What is downstream analysis now allowed to depend on?

**Workflow**

> What is legally allowed to happen next?

This preserves an explicit boundary between generated content and system state.

---

## 6. Five Core Boundaries

Zhiji's human–AI governance can be compressed into five `≠` statements.

### 6.1 Evidence ≠ Hypothesis

Evidence describes available information.

A Hypothesis interprets that information.

Even if a Hypothesis is supported by substantial Evidence, it retains its judgment status and must not pretend to be Evidence itself.

### 6.2 Proposal ≠ Accepted State

The Agent may propose:

- Current Position;
- Target Position;
- ChangeLaw;
- Intervention;
- Scenario;
- Solution;
- Projection.

Only a legal process can make a Proposal part of the formal state that later analysis is allowed to rely on.

> **Model output does not acquire authority merely by being generated.**

### 6.3 Human Approval ≠ Causal Proof

Human Approval means:

> “This judgment may be used as a working basis for the current decision or analysis.”

It does not mean:

> “This mechanism has been proven in the real world.”

For ChangeLaw in particular, approval must not silently change epistemic status.

Causal credibility still depends on Evidence, Measurement, and later validation.

### 6.4 Projection ≠ Actual Outcome

Position Projection describes:

> **What is expected to happen if the current assumptions and intervention hold.**

Actual Outcome describes:

> **What actually happened in reality.**

Between them there must be:

```text
Implementation
     ↓
Observation
     ↓
Measurement
     ↓
Outcome Assessment
```

The system therefore cannot manufacture an “effect already happened” state just because a solution was approved.

### 6.5 Selection ≠ Execution Authorization

Selecting a:

- Scenario;
- Intervention;
- Solution Option;

means only:

> **This analytical path or design direction has been accepted.**

It does not mean the system may automatically:

- modify production systems;
- call external enterprise execution APIs;
- spend budget;
- change business rules;
- act on real users.

Real execution requires a separate, explicit authorization mechanism.

Zhiji intentionally preserves:

```text
No execution
No authorization
```

as a product boundary.

---

## 7. Deterministic Enforcement · Governance Cannot Live Only in Prompts

If the boundaries above existed only inside a System Prompt, they would not constitute reliable governance.

Zhiji therefore places critical governance rules in deterministic Workflow.

Key Workflow Contract rules include:

```text
Unknown transition → fail closed
```

That is:

> **Any state transition that is not explicitly allowed is rejected.**

In addition:

- mandatory human gates accept only a `USER` actor;
- each legal transition produces a new version;
- transitions retain actor / time / reason / lineage;
- Domain records enter Workflow by reference and are not silently mutated by Workflow;
- Approval, Workflow State, and Epistemic Status remain separate;
- terminal states do not allow undefined continuation.

Governance is therefore not:

> “Remind the model not to overstep.”

It is:

> **Even if the model attempts to overstep, there is no legal system transition that allows it.**

---

## 8. Pause, Review, and Recovery · The System May Stop When Uncertain

Complex analysis should not always continue forward.

Zhiji explicitly represents several stopping states.

### More Evidence Required

When evidence is insufficient:

```text
Proposed State
      ↓
MORE_EVIDENCE_REQUIRED
      ↓
Evidence Resolution
      ↓
Recorded Resume State
```

The system records where it should return after Evidence is resolved.

### Upper-layer Review Required

If lower-layer analysis discovers that:

> an inherited upper-layer condition may no longer hold,

it cannot directly modify upper-layer state.

Instead it enters:

```text
UPPER_LAYER_REVIEW_REQUIRED
```

and returns the conflict for upper-layer reconsideration.

This matches the METHOD rule:

> **Upward propagation carries Candidate Impact / Conflict, not state overwrite.**

### Failure

Failure is split into:

```text
FAILED_RETRYABLE
FAILED_TERMINAL
```

So:

> “This execution failed” and “this Journey has ended” are not the same state.

Explicit failure semantics are part of governance because they prevent an Agent from fabricating a “looks finished” result after an error.

---

## 9. Cross-layer Governance · Recursive Inheritance Also Needs Authority Boundaries

Zhiji uses:

```text
Layer(n).Object = Layer(n+1).System
```

for recursive analysis.

But recursion cannot mean:

> the lower layer gains permission to rewrite the history of the upper layer.

A StatePackage therefore distinguishes two kinds of inherited content.

### Continues to Participate in Lower-layer Reasoning

```text
R / θ / D / Ω
```

At a finer-grained layer, the system may identify relevant variables and recalculate feasible space.

### Inherited as Baseline

```text
Position
ChangeLaw
Evidence
```

These have explicit provenance.

The lower layer may:

- reference them;
- supplement Evidence;
- form a new Hypothesis;
- discover a conflict.

It may not:

> **silently overwrite a confirmed upper-layer record.**

If lower-layer analysis finds that an inherited condition should change, the correct path is:

```text
Lower-layer Conflict
        ↓
Upper-layer Review
        ↓
Human / Workflow Re-evaluation
        ↓
New Confirmed State
```

not a direct write-back.

Therefore:

> **Recursive analysis allows cognition to deepen, but it does not allow authority to expand without limit as the system drills down.**

---

## 10. Auditability · Why Critical Judgments Need Provenance

Governance is not only about whether the current state is valid. The system must also answer:

> **Why did it become this state?**

Critical state should therefore retain:

- source / evidence references;
- proposal identity;
- actor;
- decision;
- time;
- version;
- parent lineage;
- correlation / causation metadata;
- selection / confirmation records.

This lets the system distinguish:

```text
Who proposed it?
Who confirmed it?
Which Evidence supported it?
In which state was it confirmed?
What changed afterward?
```

instead of ending with an untraceable narrative report.

One important principle is:

> **Narrative Summary is not an authoritative reconstruction source for Domain State.**

The system must not depend on natural-language summary to “guess back” formal past state.

---

## 11. Governance Does Not Mean Certainty

Zhiji's human–AI governance does not promise:

> a judgment is correct simply because it passed a Human Gate.

What it provides is:

```text
Uncertainty has a type
        ↓
Judgments have provenance
        ↓
Authority has boundaries
        ↓
State changes are recorded
        ↓
Errors can pause, return, and be corrected
        ↓
Predictions remain distinct from real outcomes
```

The goal is not:

> **Make AI never wrong.**

It is:

> **Prevent errors from quietly becoming system facts through authority confusion, state confusion, and long-chain propagation.**

---

## 12. Current Implementation Boundary

The project has implemented the deterministic governance skeleton: Workflow, Human Gates, Evidence / lineage, persistence, and recovery.

A real OpenAI-compatible LLM Provider is also implemented in the formal code path, and the governed Semantic Generation path can produce five evidence-grounded `HYPOTHESIS / AGENT` proposal families:

```text
CURRENT_POSITION
TARGET_POSITION
CHANGE_LAWS
INTERVENTIONS
PROJECTION
```

Those proposals still stop at the USER Gate and cannot silently modify USER-owned `R / θ / D / Ω`.

Two distinctions remain important:

- the older proposal-only `LLMProposalAdapter` review seam remains fake-only by default and should not be conflated with the separate real completion / semantic-generation path;
- Agent-authored Scenario / Solution Candidate semantics remain deferred under their current authority contracts.

So the project can now demonstrate that:

> **A real model can enter the governance structure and produce bounded, reviewable, non-authoritative Semantic Proposals.**

It still cannot demonstrate that:

> **Real models can autonomously and reliably perform high-quality enterprise diagnosis.**

The system also has no autonomous real-world execution authority.

For the exact implementation boundary, runtime evidence, and Demo behavior, see:

- `docs/STATUS.md` — what is implemented today;
- `demo/README.md` — how one real-model Product Loop runs;
- `evidence/` — which engineering facts support those claims.

---

## 13. Summary · The Core of Governance

Zhiji's human–AI governance can be compressed into one sentence:

> **Let the Agent propose candidates, let the Human provide real-world authority, let Workflow enforce deterministic constraints, let Domain store legally accepted state, and let Measurement separate “what we believed would happen” from “what actually happened.”**

Further compressed:

```text
Agent can propose.
Human can authorize.
Workflow can enforce.
Domain can remember.
Evidence can support.
Measurement can verify.
```

No role can substitute for the others.

That is the core of Zhiji's governance design.

---

## 14. Relationship to Other Documents

This document answers only “who may decide what, and how an Agent judgment may legally enter system state.” It intentionally does not duplicate the full business method, software implementation, or engineering verification.

| If you want to verify... | Read... |
|---|---|
| How Zhiji moves from an enterprise problem to Scenario / Solution | `docs/METHOD.md` |
| How Human Gate, Workflow, Domain, and LLM adapters are implemented | `docs/ARCHITECTURE.md` |
| Which governance capabilities are implemented / fake-only / deferred | `docs/STATUS.md` |
| Where Humans and Agents appear in a real Product Loop | `demo/README.md` |
| Whether Workflow / Human Gate / recovery actually ran | `evidence/` |
| Where to challenge Semantic Authority, Human Gate by State vs. Risk, and related open questions | `rfcs/RFC-001-ARCHITECTURE-REVIEW.md` |

Overall reading relationship:

```text
METHOD
answers “how do we analyze?”
        ↓
GOVERNANCE
answers “who may decide what?”
        ↓
ARCHITECTURE
answers “how does software enforce it?”
        ↓
STATUS / DEMO / EVIDENCE
answer “what actually exists, how does it run, and what proves it?”
```
