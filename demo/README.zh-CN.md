# 知几 Agent 受控诊断产品环 — Demo 录制说明

[English](README.md) | [中文](README.zh-CN.md)

## 一、这是什么

一段**真实运行**的端到端录制：用一家虚构公司（启元科技）的 B2B 线索转化诊断案例，
从人工输入业务案例开始，走完受控诊断产品环，直到 `PRODUCT_OUTPUT_READY`。

- 语义生成使用**真实模型**（TokenHub `hy3`），不是预置的确定性假数据。
- 全程落库 PostgreSQL（Docker 容器 + Alembic 到 head），不是内存模拟。
- 每个语义门都由人复核后确认，Agent 从未自行确认或选择。

## 二、产物

| 文件 | 说明 |
|---|---|
| `zhiji_demo_public_cut.mp4` | **推荐观看版本**：约 2 分 53 秒，快速展示真实模型 Proposal → Human Gate → 跨层诊断 → Solution → `PRODUCT_OUTPUT_READY` |
| `zhiji_demo_product_loop_v3.mp4` | **完整 Engineering Demo**（2026-09-24）：4 分 40 秒，1822×1238，2.65× 加速 |
| `zhiji_demo_product_loop_v3.cast` | v3 原始录制（asciinema v2，742 秒，634 事件） |
| `zhiji_demo_product_loop_v3.prompts.txt` | v3 提示 / 应答审计轨迹 |

> Public Cut 与完整 Engineering Demo 来自同一次 v3 运行；Public Cut 仅做时间压缩与画面剪辑，不改变底层运行事实。

## 三、Recording Provenance · 录制来源

```text
Runtime: local CLI
Semantic provider: real model (hy3)
Persistence: PostgreSQL
Migration: Alembic head
Raw terminal trace: available in evidence
Prompt/response audit trace: available in evidence
```

原始审计材料：

- [Raw terminal trace](../evidence/demo-product-loop-v3/zhiji_demo_product_loop_v3.cast)
- [Prompt / response audit trace](../evidence/demo-product-loop-v3/zhiji_demo_product_loop_v3.prompts.txt)

## 四、完整 Engineering Demo 中发生了什么

| 阶段 | 内容 |
|---|---|
| 案例录入 | 24 项人工输入：Case key、Phase16 诊断 12 项、Module17 商业案例 11 项 |
| 受控入口 | 决策提案 `PROCEED` → 独立复核 `APPROVED`（两道人工门） |
| 对象确认 | OBJECT 族由人工逐字段确认（对象 / 类型 / 业务身份 / 目标） |
| 当前位置 | **未使用 Agent 起草**：当前 `InternalState {R, θ, D, Ω}` 保持 USER-owned，由人工输入；当前交互路径尚不能将 Agent 起草的 Position 与 USER 提供的 InternalState 合并确认 |
| 目标位置 | **Agent 起草并确认**：`TargetPositionSnapshot`（HYPOTHESIS，停在 USER Gate） |
| 变化规律 | **Agent 起草并确认**：`ChangeLaw` |
| 干预选项 | **Agent 起草并确认**：`InterventionOption`，引用当前 Journey 中已确认的 `ChangeLaw` ID |
| 方案选择 | 呈现 2 个候选方案 → 人工 `SELECT` 第 1 个 + 填写选择理由。**候选由确定性装配产出，不是模型生成**（`RealLLMProposalAdapter` 不参与这一族） |
| 约束核对 | 两条硬约束逐条确认满足 |
| 终态 | `PRODUCT_OUTPUT_READY`，含 Business / Scenario 诊断包、Solution 包与完整 lineage |

## 五、诚实边界（未证明什么）

- 本视频**不**证明 Agent 能自主完成诊断：每个语义门都有人工复核与确认，Agent 只出草案。
- 本视频证明**本次 Demo 路径中真实模型能够在既定治理边界内产生 Semantic Proposal**；这不等于证明模型已经具备稳定、通用的企业诊断能力。
- 本视频**不**包含真实经营结果验证，案例公司为虚构，指标口径与内部证据同构但不是真实客户数据。
- 本视频**不**宣称 production-ready，输出边界沿用公开资料口径（`No execution: true` / `No authorization: true`）。

## 六、Known Limitation · 当前已知限制

当前 Current Position 交互仍有一个明确限制：

- `R / θ / D / Ω` 属于 USER-owned InternalState；
- 当前流程尚不能把 Agent 起草的 Position 描述与 USER 提供的 InternalState 在同一确认流程中合并；
- 因此本次 Demo 的 Current Position 由 USER 完整输入。

后续计划支持：

```text
Agent drafts Position
        ↓
USER supplements / corrects InternalState
        ↓
Merged confirmation
```
