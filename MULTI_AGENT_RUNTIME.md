# Multi-Agent Runtime Landing Guide

本文档说明如何把 Agent Constitution Framework 中的多 Agent 设计真正落地到运行时。

核心结论：

> 仅复制 `CLAUDE.md`、`.claude/agents/`、`.claude/rules/`、`.claw-policy.json`、`.claw-teams.json` 和 `AGENT_BOARD.md`，不会自动创建多个 Agent。

这些文件定义的是治理规则、角色边界、团队模板和状态交接协议。真正的多 Agent 执行必须由具体运行时支持，并由用户、适配器或编排器显式触发。

## 1. Responsibility Split

本框架分为三层：

| Layer | Responsibility | Files |
| --- | --- | --- |
| Constitution | 定义所有 Agent 的最高行为规则 | `CLAUDE.md` |
| Collaboration Protocol | 定义角色、委派、质量门禁和状态白板 | `.claude/agents/*`, `.claude/rules/*`, `AGENT_BOARD.md` |
| Runtime Adapter | 真正创建 Agent、分派任务、收集结果、写回白板 | Codex 子 Agent、Claude Code subagents、LangGraph、或自定义 orchestrator |

前两层由本仓库提供。第三层必须由使用方接入。

## 2. Soft Constitution vs Real Multi-Agent

### Soft Constitution Mode

这是默认模式。

在该模式下，单个 Agent 按角色流程模拟多 Agent 协作：

```text
explorer -> planner -> implementer -> reviewer -> verifier -> supervisor
```

特点：

- 不一定创建真实子 Agent。
- 成本低，适合小型修改和普通代码任务。
- 仍然应遵守角色边界、质量门禁和 `AGENT_BOARD.md` 交接规则。
- 不能提供真正的并行探索或独立审查隔离。

### Real Multi-Agent Mode

在该模式下，运行时必须真实创建多个 Agent 或任务节点。

特点：

- explorer、planner、reviewer、verifier 等角色可以由不同 Agent 独立执行。
- 无依赖的探索任务可以并行。
- 实现任务必须避免多个 Agent 同时修改同一文件。
- 父 Agent 或 supervisor 负责汇总结果、处理冲突、判断是否达到 Definition of Done。
- 所有关键结果必须写入 `AGENT_BOARD.md`。

## 3. Codex Landing Rules

Codex 不会因为项目中存在本模板而自动启动多个子 Agent。

在 Codex 中，真实多 Agent 模式需要用户明确触发，例如：

```text
请使用多 Agent 模式处理这个任务：
1. supervisor 先读取 CLAUDE.md、AGENT_BOARD.md、.claw-policy.json 和 .claw-teams.json。
2. 按 standard-delivery 团队拆分任务。
3. 并行委派 explorer 分析不同代码区域。
4. implementer 只修改 supervisor 指定范围内的文件。
5. reviewer 和 verifier 独立审查与验证。
6. 最后把每个 Agent 的结论写入 AGENT_BOARD.md，并由 supervisor 汇总。
```

如果用户只说“帮我改这个问题”，Codex 通常会以单 Agent 方式完成，并最多采用 soft-constitution 流程。

## 4. Codex Adapter Contract

当在 Codex 中启用真实多 Agent 模式时，supervisor 应执行以下协议。

### Step 1: Load Governance Context

必须读取：

```text
CLAUDE.md
AGENT_BOARD.md
.claw-policy.json
.claw-teams.json
.claude/rules/delegation.md
.claude/rules/agent-board.md
```

按需读取：

```text
.claude/agents/explorer.md
.claude/agents/planner.md
.claude/agents/implementer.md
.claude/agents/reviewer.md
.claude/agents/verifier.md
.claude/agents/supervisor.md
```

### Step 2: Decide Orchestration Mode

supervisor 必须输出：

```text
orchestration_mode=soft-constitution
```

或：

```text
orchestration_mode=real-multi-agent
```

如果涉及以下任一情况，应优先考虑真实多 Agent：

- 需要并行探索多个代码区域。
- 预计修改或新增文件超过 3 个。
- 涉及数据库 schema、公开 API、安全、权限、核心业务流程。
- 需要独立 review 或 verification。
- 任务存在多个清晰、可并行、互不冲突的子任务。

### Step 3: Create Delegation Packets

每个子 Agent 必须收到结构化委派包：

```md
Role:
Task:
Context:
Scope:
Forbidden:
Dependencies:
Expected Output:
Acceptance Criteria:
```

禁止把模糊任务直接交给 implementer。

### Step 4: Enforce Write Ownership

并行写入必须满足：

- 每个 implementer 拥有明确且互不重叠的文件范围。
- reviewer、verifier、explorer、planner 默认只读。
- 如果两个 Agent 需要修改同一文件，必须串行执行。
- 父 Agent 不得覆盖用户已有改动或其他 Agent 的未合并成果。

### Step 5: Merge and Record

supervisor 必须收集所有子 Agent 输出，并写入或要求写入 `AGENT_BOARD.md`：

```md
Role:
Work Completed:
Files Read:
Files Changed:
Commands Run:
Evidence:
Open Issues:
Risks:
Next Recommended Agent:
Next Task Goal:
Acceptance Criteria for Next Agent:
```

最终总结必须包含：

```md
Outcome:
Agents Used:
Files Changed:
Verification:
Known Residual Risks:
Follow-Up Tasks:
Final Decision: done | partial | failed
```

## 5. Runtime Adapter Examples

### Codex

Codex 适合用作开发期多 Agent 协作运行时。

落地方式：

- 用户显式请求多 Agent。
- Codex supervisor 使用子 Agent 能力并行委派探索、审查或验证任务。
- 子 Agent 的结果由主 Agent 合并。
- `AGENT_BOARD.md` 作为跨 Agent 的低成本状态白板。

限制：

- 项目文件不能强制 Codex 自动创建子 Agent。
- 是否委派还受平台策略、任务边界和当前会话指令约束。
- 小任务可能被 Codex 合理地降级为 soft-constitution。

### Claude Code

Claude Code 可使用 `.claude/agents/*.md` 作为角色定义基础。

落地方式：

- 将 explorer、planner、implementer、reviewer、verifier、supervisor 注册为可调用 subagents。
- 在 supervisor agent 中启用委派工具。
- 强制每个 subagent 完成后追加 `AGENT_BOARD.md` 交接记录。

### LangGraph

LangGraph 适合严格、可恢复、可重复运行的生产级多 Agent 工作流。

落地方式：

- 每个角色映射为一个 graph node。
- `AGENT_BOARD.md` 可作为人类可读状态摘要。
- 结构化 graph state 作为机器可恢复状态。
- 人工审批、失败重试、回滚和分支路由应由 graph 显式建模。

## 6. Minimal Codex Trigger Prompt

在使用本框架的项目中，如果希望 Codex 真正启用多 Agent，可直接使用：

```text
请按本项目的 Agent Constitution Framework 使用真实多 Agent 模式。

要求：
- 先读取 CLAUDE.md、AGENT_BOARD.md、.claw-policy.json、.claw-teams.json 和 .claude/rules/delegation.md。
- 由 supervisor 判断 orchestration_mode。
- 如果适合并行，创建 explorer/reviewer/verifier 等子 Agent。
- 每个子 Agent 必须有 Role、Task、Scope、Forbidden、Expected Output 和 Acceptance Criteria。
- implementer 只能修改 supervisor 明确分配的文件。
- 完成后把交接记录和最终总结写入 AGENT_BOARD.md。
```

## 7. Migration Checklist

把本框架迁移到新项目后，如果需要真实多 Agent，必须确认：

- [ ] 目标工具是否支持创建子 Agent 或任务节点。
- [ ] 是否有 supervisor 负责读取 `.claw-teams.json` 并拆分任务。
- [ ] 是否有机制把角色定义映射到运行时 Agent。
- [ ] 是否有明确的并行写入限制。
- [ ] 是否有统一的结果合并和冲突处理规则。
- [ ] 是否强制写入 `AGENT_BOARD.md`。
- [ ] 是否定义了何时使用 soft-constitution，何时使用 real-multi-agent 或 LangGraph。

## 8. Product Positioning

推荐在 README 或项目介绍中使用以下表述：

> Agent Constitution Framework 默认提供 soft-constitution 治理模式。它通过宪法、角色边界、委派协议和状态白板约束 Agent 协作。真实多 Agent 执行需要 Codex、Claude Code、LangGraph 或自定义 orchestrator 等运行时适配器支持；仅复制模板文件不会自动创建多个 Agent。

