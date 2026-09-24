# Verification Snapshot · Current Engineering Verification Snapshot

[English](verification-snapshot.md) | [中文](verification-snapshot.zh-CN.md)

> This file records the **fixed engineering evidence snapshot** currently used by the Public Repo.<br>
> It answers: **for a specific Source Repository version, which deterministic engineering checks actually ran, and what were the results?**

This file does not define capability status. See:

[../docs/STATUS.md](../docs/STATUS.md)

It also does not replace source-level audit:

```text
Verification Snapshot
≠
Source-level Audit
```

---

## 1. Snapshot Identity

Current evidence anchor:

```text
Repository: XIAchong916/zhiji-agent
Branch: main
Commit: 49ec33c0696fe3e94bd86d382f67ce5e756e4406
Date: 2026-09-24
Git tree: f3abf089a7689d48ca95a657ae82d8c2fb8b56f0
```

`49ec33c` is `main` after PR #58 was merged.

The GitHub Actions run for PR #58 executed on merge tree:

```text
aad34a82b30d1d4bc2119883c8b96c6d4c1f1126
```

That merge tree and final `main` commit share the same Git tree:

```text
f3abf089a7689d48ca95a657ae82d8c2fb8b56f0
```

This file therefore uses that CI result as the public Verification Snapshot for the `49ec33c` code tree.

---

## 2. GitHub Actions · Automated Quality Gates

Corresponding GitHub Actions run:

```text
Workflow: CI
Run ID: 35965424249
Run number: 51
Conclusion: success
```

Primary jobs:

| Job | Result |
|---|---|
| `deterministic` | **PASS** |
| `postgres` | **PASS** |

This shows that the current code tree passed the automated verification corresponding to:

```text
make verify
make verify-postgres
```

But the following distinction must remain explicit:

```text
Automated CI Gate
≠
Enforced Branch Protection
```

Passing these jobs does not grant a production release approval claim for the Private Source Repository.

---

## 3. Deterministic Gate · `make verify`

CI executed:

```text
make verify
```

### 3.1 pytest

Collected:

```text
2945 items
```

Final result:

```text
2860 passed
85 skipped
1 warning
```

Result:

```text
PASS
```

The `85 skipped` tests mainly represent paths that require additional explicit runtime conditions. A skip is neither a failure nor proof that every capability has been verified.

### 3.2 mypy

CI executed:

```text
uv run mypy packages apps/api
```

Result:

```text
Success: no issues found in 174 source files
```

Result:

```text
PASS
```

This supports the narrower statement that:

> Type checking for `packages` and `apps/api` is green under the current CI scope.

It does not mean that all future code, scripts, external dependencies, or runtime environments are free of type risk.

### 3.3 Ruff

CI executed:

```text
uv run ruff check .
uv run ruff format --check .
```

Result:

```text
All checks passed!
655 files already formatted
```

Result:

```text
PASS
```

---

## 4. PostgreSQL Gate · `make verify-postgres`

CI executed:

```text
make verify-postgres
```

The gate uses an isolated PostgreSQL environment and runs the selected PostgreSQL / migration / persistence / restart / integration paths twice.

Selection:

```text
2945 total items
83 selected
2862 deselected
```

### Run 1

```text
83 passed
2862 deselected
5 warnings
```

### Run 2

```text
83 passed
2862 deselected
6 warnings
```

Both runs:

```text
PASS
```

The Public Repo can therefore state:

> **The current PostgreSQL-specific verification passed twice consecutively within the same CI run.**

---

## 5. Migration Evidence

The PostgreSQL CI logs show Alembic migrations executing sequentially from base to the current head.

The snapshot reaches:

```text
20260831_sec02a_project_lineage
```

The observed migration chain includes:

```text
20260713_04d
→ 20260714_07b
→ 20260720_10e
→ 20260720_11d
→ 20260720_12d
→ 20260720_12e
→ 20260721_13f
→ 20260721_14f
→ 20260803_16i
→ 20260811_23b
→ 20260811_23c
→ 20260818_pdl1
→ 20260827_b1_user_accounts
→ 20260831_sec02a_project_lineage
```

This supports the following claims:

- the current schema can migrate from an empty database to head through the formal migration chain;
- PostgreSQL integration tests run on that head;
- the migration path is an engineering fact that has actually been executed.

It does not support claims that:

- every future production migration has been rehearsed;
- real production backup / restore has been completed;
- production database observability is complete;
- disaster-recovery acceptance is complete.

---

## 6. Repository Inventory · Code-tree Size Anchor

Current Git tree inventory:

```text
Python test files: 426
Python files total: 655
```

CI pytest collection:

```text
2945 test items
```

The current Public Evidence may therefore use the following snapshot counts:

| Item | Count |
|---|---:|
| pytest items | `2945` |
| Python test files | `426` |
| Python files total | `655` |
| mypy checked source files | `174` |
| PostgreSQL selected tests | `83` |

These numbers describe the current snapshot only. They are not, by themselves, measures of project quality or business value.

---

## 7. Observed Warnings

This CI run is not warning-free.

Deterministic gate:

```text
1 warning
```

The two PostgreSQL runs report:

```text
5 warnings
6 warnings
```

Observed warning categories include:

- Starlette / `httpx` TestClient deprecation warning;
- SQLAlchemy table-sort warning for the `workflow_heads` / `workflow_snapshots` cyclic dependency during migration metadata comparison;
- SQLAlchemy dialect option validation warning;
- a Pydantic warning in the second PostgreSQL run related to use of the `Authorization` alias.

These warnings:

```text
did not fail the current gates
```

But:

```text
Non-failing warning
≠
No maintenance risk
```

If a dependency upgrade later turns one of these warnings into an error, that becomes a new engineering fact that must be addressed. This snapshot cannot be cited as proof that a future version remains safe.

---

## 8. What This Snapshot Supports

This Verification Snapshot supports the following engineering claims:

- the current code tree passes the ordinary pytest gate;
- mypy is green under the current CI scope;
- Ruff check / format are green;
- PostgreSQL-specific tests pass twice consecutively;
- the Alembic migration chain actually executes to the current head;
- both GitHub Actions jobs, `deterministic` and `postgres`, complete successfully;
- the Public Repo's claim that the current engineering loop is based on actual execution rather than pure design has deterministic evidence behind it.

Combined with Demo Evidence, it supports:

```text
Engineering Verification
+
Runtime Demo Evidence
```

rather than relying on architectural description alone.

---

## 9. What This Snapshot Does Not Support

This file cannot be used to prove:

```text
Real enterprise diagnosis quality is validated          ✗
Agent is autonomous                                     ✗
Cross-industry generalization is validated              ✗
ChangeLaw is causally proven                            ✗
Projection equals Actual Outcome                        ✗
Selected solution produces expected ROI                 ✗
System is production-ready                              ✗
Production deployment / observability is complete       ✗
Source-level external audit has occurred                ✗
```

The following distinctions must remain explicit:

```text
Tests Pass
≠
Task / Product Accepted

Engineering Verification
≠
Business Validation

CI Success
≠
Production Readiness
```

---

## 10. Relationship to Other Evidence

```text
verification-snapshot.md
        ↓
shows whether deterministic engineering gates passed

demo-product-loop-v3/run-summary.md
        ↓
explains what happened in one real-model Product Loop

demo-product-loop-v3/*.cast
        ↓
preserves the raw terminal execution trace

demo-product-loop-v3/*.prompts.txt
        ↓
preserves the Prompt / Response audit trace
```

Therefore:

- to verify **whether engineering gates actually passed** → read this file;
- to verify **whether one real-model Product Loop actually ran** → read the Demo Evidence;
- to understand **what capability status these facts support** → read `docs/STATUS.md`.

[Back to Evidence Index →](README.md)

[Read STATUS →](../docs/STATUS.md)

---

## 11. Snapshot Rule · How to Update This File

This file is a **snapshot**, not a live dashboard.

Only when the Public Repo intentionally switches to a new Source Repository evidence anchor should the following be updated:

```text
commit
Git tree
CI run
pytest result
mypy result
Ruff result
PostgreSQL result
migration head
repository inventory
```

All of these values should be refreshed as one consistent evidence set. Avoid cross-version mixtures such as:

```text
new commit
+
old test numbers
+
old migration head
```

> **The value of Evidence comes from binding facts to a specific version, not from how impressive the numbers look.**
