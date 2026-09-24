# RFC-001 · Architecture Review

[English](RFC-001-ARCHITECTURE-REVIEW.md) | [中文](RFC-001-ARCHITECTURE-REVIEW.zh-CN.md)

> **Status:** Draft for External Review<br>
> **Scope:** Zhiji Agent core method, governance, and architecture assumptions<br>
> **Purpose:** Expose the core architectural assumptions most likely to fail as real Semantic Intelligence and real enterprise cases enter the system.

This is not another project introduction.

The Public Repo already contains stable documents that answer different questions:

- [`METHOD`](../docs/METHOD.md): how Zhiji models and progressively narrows complex business problems;
- [`GOVERNANCE`](../docs/GOVERNANCE.md): who may propose, confirm, and change what;
- [`ARCHITECTURE`](../docs/ARCHITECTURE.md): how the method and governance rules are enforced in software;
- [`STATUS`](../docs/STATUS.md): which capabilities exist today and which do not.

This RFC does something different:

> **It makes the most uncertain and challenge-worthy architectural assumptions explicit, explains why the design currently looks this way, identifies known risks, and states what evidence would cause the design to change.**

The goal is not to prove the current architecture is “correct.”

It is to ask early:

> **As real models and real enterprises enter the system, which assumption is most likely to fail first?**

---

## 1. Review Principles

The project intentionally separates concepts that AI systems often collapse:

```text
Evidence ≠ Hypothesis

Proposal ≠ Accepted State

Human Approval ≠ Causal Proof

Projection ≠ Actual Outcome

Selection ≠ Execution Authorization
```

Zhiji also uses a five-layer recursive business abstraction:

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

with the core relationship:

```text
Layer(n).Object = Layer(n+1).System
```

and a bidirectional mechanism for continuity across layers:

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

These are **current design choices**, not universal truths validated across real enterprises.

The six questions below are the assumptions most worth challenging now.

---

# Q1. Semantic Authority · How Much Semantic-generation Authority Should the Agent Have?

## Current Design

Zhiji explicitly separates semantic-generation capability from business authority.

The governance path is:

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

The Agent may produce non-authoritative semantic judgments such as:

- Position Hypothesis;
- ChangeLaw Hypothesis;
- Intervention Proposal;
- Position Projection;
- Candidate Analysis.

Some information remains unavailable for free model authorship or direct model mutation, especially:

- object-identity facts;
- confirmed business facts;
- objectives and priorities;
- hard constraints;
- capability boundaries;
- final selection and decision.

The current rule is:

> **Greater Semantic Intelligence does not automatically imply greater Semantic Authority.**

A model can become stronger while “what it can infer” and “what the system allows it to decide” remain different questions.

---

## Why This Design

Enterprise diagnosis is a long chain, not a one-shot answer.

If an Agent inference is silently treated as fact, later layers may continue reasoning from a false premise:

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

Zhiji therefore tries to preserve the difference between:

```text
what the model believes may be true
```

and:

```text
what the system currently permits downstream analysis to depend on
```

The first is Semantic Proposal. The second is Authoritative Domain State.

---

## Known Risk

The current boundary can fail in either direction.

### Too Little Agent Authority

If too much information must be explicitly entered or confirmed by a Human:

- users repeatedly fill in information the Agent could infer reliably;
- Human Gates degrade into form-filling;
- interaction cost becomes too high;
- Semantic Intelligence cannot create enough value.

The system could become:

> **a structured workflow tool with an AI assistant attached to the side.**

### Too Much Agent Authority

If model-generated semantics enter Accepted State too easily:

- inference errors may contaminate downstream layers;
- implicit assumptions become difficult to trace;
- model confidence may be mistaken for Evidence Strength;
- Human Review may become ceremonial confirmation.

The system could become:

> **a highly fluent autonomous analyzer with weak business-authority boundaries.**

---

## Questions for Review

1. Which semantic categories should allow Agent-authored `HYPOTHESIS` by default?
2. Which information should always remain Human / confirmed-source owned?
3. Are `USER_FACT / CONFIRMED / INHERITED / HYPOTHESIS` sufficient to express Semantic Authority?
4. Should Agent Authority vary dynamically with Evidence Strength, Reversibility, or Business Impact?
5. Should different analytical layers have different Semantic Authority rules?
6. When does a Human Gate stop adding value and become only operational cost?

---

## What Would Change the Design

Agent Authority could be expanded if real cases repeatedly show that:

- a semantic family can be inferred reliably;
- error cost is low and reversible;
- Evidence provenance remains explicit;
- downstream decisions remain safe;
- marginal value of Human Review is low.

It should be tightened if real cases show that:

- users frequently accept Proposals that sound reasonable but are wrong;
- inference errors compound across layers;
- lineage cannot reconstruct why a judgment was accepted;
- Human Confirmation does not effectively prevent Authority Drift.

---

# Q2. Human Gate Policy · Should Gates Follow Workflow State or Semantic Risk?

## Current Design

Zhiji currently places Human Gates at explicit decision points.

At the method level, they can be summarized as:

```text
Fact Gate
Goal / Constraint Gate
Evidence Gate
Decision Gate
```

Deterministic Workflow decides:

- whether the current state may continue;
- whether the current transition requires a Human;
- which Actor type has authority to complete the confirmation.

The design is intentionally simple:

> **The system knows in advance which state transitions must return authority to a Human.**

---

## Why This Design

Workflow-state Gates have clear advantages:

- explicit rules;
- deterministic transitions;
- easy auditability;
- easy testability;
- difficult for the Agent to bypass.

More importantly, they prevent the same probabilistic model from deciding:

> “This looks low-risk, so I think Human confirmation is unnecessary.”

Governance logic does not directly depend on model self-evaluation.

---

## Known Risk

Real business risk does not always map one-to-one to Workflow State.

Two decisions in the same formal State may be:

```text
Low-impact + Reversible
          vs
High-impact + Irreversible
```

A static Gate treats them approximately the same.

This creates two opposite problems.

### Over-governance

If there are too many Gates:

- Humans fatigue;
- confirmations become mechanical clicks;
- diagnosis slows down;
- user experience degrades.

### Under-governance

If a formally identical State involves:

- weak Evidence;
- large financial impact;
- irreversible consequences;
- compliance risk;

then State-only gating may be too coarse.

---

## Questions for Review

1. Should Human Gates continue to be primarily Workflow-State based?
2. Should the system gradually incorporate:
   - Semantic Risk;
   - Evidence Strength;
   - Reversibility;
   - Business Impact;
   - Uncertainty?
3. Who or what should calculate the risk that triggers a Gate?
4. Can Risk Assessment remain deterministic rather than handing governance back to the LLM?
5. After repeated successful validation, should similar future Gates become lighter?
6. How should the system prevent Approval Fatigue?

---

## What Would Change the Design

If real use shows that:

- static Gates become a major interaction cost;
- many confirmations create no meaningful correction;
- risk dimensions can be calculated reliably from structured state;

then the architecture should gradually evolve toward Risk-based Gates.

If dynamic Gates become:

- opaque;
- heavily dependent on model self-judgment;
- inconsistent in authority for similar decisions;
- non-reproducible;

then Human Gates should remain primarily Workflow-State based.

---

# Q3. Recursive Abstraction · Is `Object → System` an Over-abstraction?

## Current Design

Zhiji organizes enterprise problems into five connected layers:

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

Meaning:

> **When an Object at the current layer requires deeper analysis, its internal structure is opened and it becomes the System of the next layer.**

The next layer reuses the same Core Model:

```text
System
→ Object
→ Position
→ InternalState
→ ChangeLaw
→ Intervention
```

The analytical scale changes; the analytical grammar stays stable.

---

## Why This Design

Without recursion, different business scales can easily become five unrelated frameworks:

```text
Industry Model
Enterprise Model
Business Model
Scenario Model
Solution Model
```

That increases:

- difficulty of cross-layer state inheritance;
- difficulty of Engine reuse;
- semantic drift between Agents;
- inconsistency in reasoning across layers.

Recursion attempts to provide one reusable analytical language.

---

## Known Risk

Real enterprises are not clean trees.

Examples include:

- matrix organizations;
- platform businesses;
- shared capabilities across multiple businesses;
- external ecosystem relationships;
- a Scenario affecting multiple Businesses;
- a Solution Capability constrained by multiple upper-layer objectives.

Real structure may be closer to:

```text
Graph
```

than:

```text
Tree
```

The five-layer structure may therefore be a useful default decomposition rather than a universal enterprise ontology.

---

## Questions for Review

1. Which enterprise structures naturally fit `Object → System`?
2. Which structures require:
   - layer skipping;
   - parallel layers;
   - multiple parent Systems;
   - cross-layer references?
3. Should `Industry → Enterprise → Business → Scenario → Solution Capability` be treated as a stable ontology or a default traversal?
4. Is the recursive relation better understood as Containment, Context Change, or Graph Projection?
5. Should the same Object be allowed to belong to multiple Systems?
6. Does the structural consistency of a unified Core Model outweigh the business distortion it may introduce?

---

## What Would Change the Design

If real enterprise cases repeatedly show:

- multiple-parent relationships;
- parallel diagnosis paths;
- cross-business Scenarios;
- platform-style non-hierarchical structure;
- shared Capabilities;

then the recursive model should evolve toward a Graph-like Model.

If most cases can still be handled reliably through the five-layer main path with only limited exception mechanisms, the five-layer model can remain the default recursive backbone.

---

# Q4. Epistemic State · How Should the System Represent “How Certain Are We?”

## Current Design

Zhiji deliberately distinguishes:

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

One core invariant is:

```text
Human Approval ≠ Causal Proof
```

Human Acceptance may allow a judgment to continue through the decision chain.

It cannot automatically turn a ChangeLaw into a causally proven mechanism.

Likewise:

```text
Projection ≠ Actual Outcome
```

The current system separates lifecycle, authority, and outcome, but does not claim to have a complete business Epistemic Model.

---

## Why This Design

Real business judgments are rarely simple:

```text
true / false
```

A ChangeLaw may be supported by:

- expert experience;
- a single case;
- operational data;
- statistical correlation;
- A/B Test;
- repeated interventions;
- external research.

These sources clearly do not provide the same level of confidence.

Without epistemic separation, the system can easily rewrite:

```text
accepted for decision
```

as:

```text
proven true
```

---

## Known Risk

Using only:

```text
HYPOTHESIS / ACCEPTED
```

may be too coarse.

But adding a simple 0–1 Confidence Score may create false precision.

Open problems include:

- Source Reliability;
- Evidence Freshness;
- Conflicting Evidence;
- Correlation vs. Causation;
- Replication;
- Domain Transfer;
- Uncertainty Calibration.

---

## Questions for Review

1. Should `EpistemicState` become a first-class object?
2. Should Evidence Strength be represented as:
   - categorical;
   - numeric;
   - partially ordered;
   - source-specific?
3. Who has authority to update epistemic status?
4. Should Human Confirmation change epistemic status?
5. How should new Evidence that conflicts with an existing ChangeLaw be handled?
6. Should causal claims have a stricter state model than Position Hypotheses?
7. How should Evidence validated in one enterprise transfer to another enterprise?

---

## What Would Change the Design

A richer Epistemic State Model should be introduced if real cases show that:

- accepted hypotheses have materially different Evidence quality;
- reviewers cannot explain why one ChangeLaw is more credible than another;
- conflicting Evidence is difficult to represent;
- repeated Measurement needs to accumulate into stronger knowledge.

If a complex epistemic schema does not improve real decision quality or traceability and only adds modeling cost, the model should remain lightweight.

---

# Q5. Model Context vs. Audit Trace · How Do We Balance Capability, Privacy, and Auditability?

## Current Design

A real Semantic Agent needs sufficiently rich business context to perform useful analysis.

But the system should not persist all:

- raw enterprise materials;
- Prompts;
- Model Context;
- sensitive business data;

into long-lived Trace storage.

The current design therefore separates:

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

In other words:

> **Model runtime Context and long-term Audit Trace are separate data planes.**

Persisted Trace should still be sufficient to answer:

- which Evidence was used;
- which Records were referenced;
- which Proposal was generated;
- who confirmed it;
- how state changed afterward.

It need not persist every raw input forever.

---

## Why This Design

Persisting complete model context increases:

- Privacy Risk;
- Security Risk;
- Data Minimization problems;
- long-term exposure of sensitive information.

But storing too little creates the opposite problem:

> **We can see the final result but cannot reconstruct why the model produced it.**

Zhiji therefore tries to preserve Lineage without treating Raw Context as long-term Domain State.

---

## Known Risk

Persisting only:

```text
hash
reference
structured output
safe metadata
```

may still be insufficient for real Semantic Audit.

For example:

- the original Source may later change;
- external Retrieval results may disappear;
- Model / Provider / Version may change;
- Prompt-construction logic may change;
- actual runtime Context may be richer than the Trace indicates;
- proprietary models may be inherently non-reproducible.

This creates a real tension among:

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

## Questions for Review

1. What minimum information must be persisted for meaningful semantic audit?
2. If the original Source later becomes unavailable, is a hash enough?
3. Which business materials should be:
   - retained;
   - referenced;
   - summarized;
   - deleted after inference?
4. At what granularity should Model / Version / Prompt Template be recorded?
5. Should high-risk decisions require stronger Context Retention?
6. With nondeterministic external models, is Semantic Reproducibility itself an unrealistic goal?

---

## What Would Change the Design

Trace retention should become richer if real reviews repeatedly fail to reconstruct:

- which Evidence was used;
- what Context the model actually saw;
- why a Proposal was accepted.

It should become more restrictive if real enterprise use shows that:

- long-term Context retention materially increases privacy and security risk;
- references + structured lineage are sufficient for review;
- Full Prompt Retention adds little decision value.

The current real-provider path makes this question immediately testable, but the project still has not established the final production trade-off.

---

# Q6. Impact Propagation · When Does Local Improvement Become System-level Value?

## Current Design

Zhiji's recursion is bidirectional.

Downward:

```text
Confirmed StatePackage(n)
          ↓
Lower-layer Diagnosis
```

Upward:

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

The core rule is:

> **Impact Propagation ≠ State Overwrite.**

A local lower-layer improvement cannot directly rewrite upper-layer Position merely because it “appears relevant.”

The system must re-evaluate:

> Is this Local Change actually large and relevant enough to change the parent Object's InternalState or Position?

METHOD therefore preserves:

```text
Local Improvement
      ≠
Enterprise Value
```

---

## Why This Design

Many AI projects can demonstrate improvement in a local metric without demonstrating enterprise value.

For example:

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

Zhiji therefore does not want:

> a better local KPI to be automatically interpreted as proven enterprise-level value.

Local effects must be re-evaluated against upper-layer objectives.

---

## Known Risk

This may be the least validated part of the current METHOD.

Real Impact Propagation may involve:

- amplification;
- attenuation;
- cancellation;
- time lag;
- shared upstream variables;
- multiple simultaneous interventions;
- nonlinear effects;
- attribution ambiguity;
- environmental change.

One lower-layer Object may affect multiple upper-layer Goals.

Multiple lower-layer Objects may jointly determine one upper-layer result.

A simple hierarchical propagation rule may therefore be inadequate.

---

## Questions for Review

1. What conditions should a Local Change satisfy before becoming `Candidate Upper-layer Impact`?
2. Should Propagation be primarily:
   - rule-based;
   - model-based;
   - measurement-based;
   - mixed?
3. How should multiple Scenario impacts aggregate into Business Position?
4. How should the system represent:
   - interaction effects;
   - cancellation;
   - thresholds;
   - time lags?
5. Under what conditions should Impact Propagation stop?
6. How should Attribution be separated from Correlation?
7. Can a local improvement have real value even if no measurable upper-layer Position change is detected?
8. Should Impact Propagation be a Generic Engine capability or remain Domain-specific?

---

## What Would Change the Design

Impact Propagation should become more explicit and more Domain-specific if real cases show that:

- cross-layer effects cannot be represented with generic rules;
- attribution dominates the problem;
- many-to-many relationships are common;
- time lag makes immediate propagation misleading.

It can remain generic at the framework level if a stable set of propagation primitives can be reused across industries while domain differences are mostly expressed through parameters and ChangeLaw.

---

# 7. Review Priority

The six questions do not need to be solved simultaneously before the next product step.

The current validation sequence suggests:

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

P0 / P1 / P2 are **not rankings of importance**. They represent validation dependency.

The reasoning is:

- a real Semantic Proposal path immediately stress-tests Semantic Authority and Human Gate design;
- real enterprise cases most directly challenge recursion and Impact Propagation;
- repeated cases and long-term operation make Epistemic State and Trace Policy increasingly consequential.

---

# 8. How to Review

The most useful feedback is not:

> “This architecture looks reasonable.”

It is closer to:

```text
Assumption
Which current assumption is too strong, wrong, or missing a prerequisite?

Failure Case
What real enterprise / AI scenario breaks it?

Consequence
If it fails, which part of METHOD / GOVERNANCE / ARCHITECTURE fails with it?

Alternative
What simpler or stronger design would you use instead?

Evidence
What observation would distinguish the current design from the alternative?
```

A minimal review template:

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

The most valuable review should be able to:

> **identify a concrete assumption, supply a counterexample, propose an alternative, and design a test.**

---

# 9. What This RFC Does Not Decide

This RFC deliberately does **not** conclude that:

- Agent Authority should be expanded;
- Human Gates should become dynamic / risk-based;
- five-layer recursion should become a Graph;
- a numeric Confidence Score should be introduced;
- full Prompts should be retained long term;
- Impact Propagation should be automated.

Those are the questions under review.

The architecture should change only when:

```text
External Critique
        +
Real Semantic Agent Behavior
        +
Real Enterprise Cases
        +
Measurement / Outcome Evidence
```

provide a stronger basis than the current design.

---

# 10. Relationship to the Public Repo

```text
README
What is Zhiji, and why should I keep reading?
        ↓
METHOD
How does it analyze?
        ↓
GOVERNANCE
Who may decide what?
        ↓
ARCHITECTURE
How does software enforce it?
        ↓
STATUS
What actually exists today?
        ↓
RFC-001
Which assumptions are most likely to be wrong?
        ↓
DEMO / EVIDENCE
How does the current system actually run, and what does that prove?
```

The role of this RFC can therefore be summarized in one sentence:

> **Do not defend the current architecture. Make its most fragile and falsifiable assumptions visible enough to be challenged.**
