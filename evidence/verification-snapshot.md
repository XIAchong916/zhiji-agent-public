# Verification Snapshot · 当前工程验证快照

> 本文件记录 Public Repo 当前使用的一次**固定工程证据快照**。<br>
> 它回答的是：**在指定 Source Repository 版本上，哪些确定性工程验证实际运行过，结果是什么。**

本文件不定义能力状态；能力分类请看：

[../docs/STATUS.md](../docs/STATUS.md)

本文件也不替代源码审计：

```text
Verification Snapshot
≠
Source-level Audit
```

---

## 1. Snapshot Identity · 快照身份

当前证据锚点：

```text
Repository: XIAchong916/zhiji-agent
Branch: main
Commit: 49ec33c0696fe3e94bd86d382f67ce5e756e4406
Date: 2026-09-24
Git tree: f3abf089a7689d48ca95a657ae82d8c2fb8b56f0
```

`49ec33c` 是 PR #58 合并后的 `main`。

PR #58 的 GitHub Actions 实际运行在 merge tree：

```text
aad34a82b30d1d4bc2119883c8b96c6d4c1f1126
```

该 merge tree 与最终 `main` commit 使用相同 Git tree：

```text
f3abf089a7689d48ca95a657ae82d8c2fb8b56f0
```

因此，本文件把这次 CI 结果作为 `49ec33c` 当前代码树的公开 Verification Snapshot。

---

## 2. GitHub Actions · 自动化质量门

对应 GitHub Actions：

```text
Workflow: CI
Run ID: 35965424249
Run number: 51
Conclusion: success
```

两个主要 Job：

| Job | Result |
|---|---|
| `deterministic` | **PASS** |
| `postgres` | **PASS** |

这说明当前代码树已经通过：

```text
make verify
make verify-postgres
```

对应的自动化验证。

但必须保持：

```text
Automated CI Gate
≠
Enforced Branch Protection
```

当前 Private Source Repository 并未因此获得可对外声明的 production release approval。

---

## 3. Deterministic Gate · `make verify`

CI 实际运行：

```text
make verify
```

### 3.1 pytest

收集到：

```text
2945 items
```

最终结果：

```text
2860 passed
85 skipped
1 warning
```

运行结果：

```text
PASS
```

这里的 `85 skipped` 主要包括需要其他显式运行条件的测试路径；Skip 不应被解释为失败，也不应被解释为“全部能力都已验证”。

### 3.2 mypy

CI 实际运行：

```text
uv run mypy packages apps/api
```

结果：

```text
Success: no issues found in 174 source files
```

运行结果：

```text
PASS
```

这个结果支持：

> 当前 CI 口径下的 `packages` 与 `apps/api` 类型检查为 green。

它不表示：

> 所有未来代码、脚本、外部依赖和运行环境都不存在类型风险。

### 3.3 Ruff

CI 实际运行：

```text
uv run ruff check .
uv run ruff format --check .
```

结果：

```text
All checks passed!
655 files already formatted
```

运行结果：

```text
PASS
```

---

## 4. PostgreSQL Gate · `make verify-postgres`

CI 实际运行：

```text
make verify-postgres
```

该门使用隔离 PostgreSQL 环境，并对选定的 PostgreSQL / migration / persistence / restart / integration 路径执行两遍验证。

测试选择结果：

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

两遍均：

```text
PASS
```

因此可以公开声明：

> **当前 PostgreSQL 专项验证在同一 CI run 中连续两遍通过。**

---

## 5. Migration Evidence · 数据库迁移证据

PostgreSQL CI 日志显示 Alembic 从 base 顺序执行到当前 head。

本快照中执行到：

```text
20260831_sec02a_project_lineage
```

日志中的迁移链包括：

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

这支持：

- 当前 schema 可以从空数据库沿正式 migration chain 升级到 head；
- PostgreSQL integration tests 在该 head 上运行；
- migration path 是实际执行过的工程事实。

它不支持：

- 已完成所有未来生产环境迁移演练；
- 已完成真实线上备份 / 恢复；
- 已完成生产数据库 observability；
- 已完成 disaster recovery 验收。

---

## 6. Repository Inventory · 当前代码树规模锚点

对当前 Git tree 的文件清点：

```text
Python test files: 426
Python files total: 655
```

CI pytest 收集：

```text
2945 test items
```

因此当前公开 Evidence 可以使用下面这一组规模口径：

| Item | Count |
|---|---:|
| pytest items | `2945` |
| Python test files | `426` |
| Python files total | `655` |
| mypy checked source files | `174` |
| PostgreSQL selected tests | `83` |

这些数字只描述当前快照，不代表项目质量本身，也不用于衡量业务价值。

---

## 7. Observed Warnings · 已观察到但未使 Gate 失败的 Warning

本次 CI 不是“零 warning”。

确定性 gate：

```text
1 warning
```

PostgreSQL 两遍分别：

```text
5 warnings
6 warnings
```

从日志中可以观察到的 warning 类型包括：

- Starlette / `httpx` TestClient deprecation warning；
- SQLAlchemy 在 migration metadata comparison 中对 `workflow_heads` / `workflow_snapshots` 循环依赖的 table-sort warning；
- SQLAlchemy dialect option validation warning；
- PostgreSQL 第二遍中还出现了 Pydantic 关于 `Authorization` alias 使用方式的 warning。

这些 warning：

```text
did not fail the current gates
```

但：

```text
Non-failing warning
≠
No maintenance risk
```

如果这些 warning 在后续依赖升级中变成 error，需要作为新的工程事实重新处理，而不能继续引用本快照证明未来版本仍然安全。

---

## 8. What This Snapshot Supports · 这份快照支持什么

这份 Verification Snapshot 可以支持以下工程主张：

- 当前代码树能够通过 ordinary pytest gate；
- 当前 CI 口径下 mypy 为 green；
- Ruff check / format 为 green；
- PostgreSQL 专项测试连续两遍通过；
- Alembic migration chain 实际执行到当前 head；
- GitHub Actions 的 deterministic / postgres 两个 Job 都成功完成；
- Public Repo 对“当前工程闭环来自实际运行而非纯设计”的描述有确定性证据基础。

它与 Demo Evidence 组合后，可以进一步支持：

```text
Engineering Verification
+
Runtime Demo Evidence
```

而不是只依赖文档中的架构描述。

---

## 9. What This Snapshot Does Not Support · 这份快照不支持什么

本文件不能用于证明：

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

特别需要保持：

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

## 10. Relationship to Other Evidence · 与其他证据的关系

```text
verification-snapshot.md
        ↓
证明确定性工程门是否通过

demo-product-loop-v3/run-summary.md
        ↓
解释一次真实模型 Product Loop 运行了什么

demo-product-loop-v3/*.cast
        ↓
保留原始终端运行轨迹

demo-product-loop-v3/*.prompts.txt
        ↓
保留 Prompt / Response 审计轨迹
```

因此：

- 想确认**工程门是否真实通过** → 看本文件；
- 想确认**一次真实模型 Product Loop 是否真实发生** → 看 Demo Evidence；
- 想判断**这些事实对应哪些能力状态** → 看 `docs/STATUS.md`。

[Back to Evidence Index →](README.md)

[Read STATUS →](../docs/STATUS.md)

---

## 11. Snapshot Rule · 如何更新本文件

本文件是一个**快照**，不是动态仪表盘。

只有当 Public Repo 决定切换新的 Source Repository evidence anchor 时，才应更新：

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

更新时应把所有口径作为同一个新的证据集合一起刷新，避免出现：

```text
新 commit
+
旧测试数字
+
旧 migration head
```

这样的跨版本混用。

> **Evidence 的价值来自“事实与版本绑定”，而不是数字本身看起来有多漂亮。**
