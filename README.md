# Agent Constitution Framework

> 如果你已经得到概念，那么下载即用。

一个可复制到任何代码库的多 Agent 协作治理模板。

Agent Constitution Framework 是一套可迁移的多 Agent 协作治理框架。它通过项目宪法、角色边界、状态画板、委派协议和质量门禁，把“自由发挥式 AI 编程”收束成可审计、可交接、可验证的工程流程。

它不绑定具体语言、框架或业务领域。你可以把它放进任何代码库，用来约束 Claude Code、Codex、Cursor、Windsurf 或其他具备文件读写能力的 Agent 工作流。

## Why It Exists

多 Agent 协作的主要问题通常不是“模型不会写代码”，而是：

- Agent 在没有边界的情况下过度修改文件。
- 不同 Agent 重复读取上下文，浪费 token 和运行时间。
- 子任务交接依赖长聊天记录，后续 Agent 容易遗漏关键状态。
- Review 和 Verification 没有固定格式，结果难以审计。
- 高复杂度任务没有分流机制，轻量提示词被迫承担架构治理责任。

本框架的目标是给 Agent 一个稳定的工程制度层：先明确任务边界，再分配角色，再记录状态，再验证结果。

## Core Idea

本框架由四个核心部件组成：

- `CLAUDE.md`：项目最高宪法，定义所有 Agent 必须遵守的行为原则。
- `.claude/agents/*.md`：角色宪法，约束 explorer、planner、implementer、reviewer、verifier、supervisor 的职责和权限。
- `.claude/rules/*.md`：专题规则，沉淀委派、状态画板、质量门禁、工具权限等可复用制度。
- `AGENT_BOARD.md`：运行时状态画板，用低成本结构化记录替代反复读取长对话上下文。

相比把整段上下文反复塞给 Agent、让它在长对话里“记住”状态的传统方式，状态画板把关键任务信息压缩为固定字段，能显著减少 token 消耗，并提升后续 Agent 接手时的响应速度。

## State Board Advantage

`AGENT_BOARD.md` 是这套框架的关键设计。它不是普通 TODO，也不是最终文档，而是多 Agent 工作时共享的状态画板。

它记录：

- 当前目标和完成定义。
- 允许修改和禁止修改的范围。
- 关键上下文、决策、风险和人工确认项。
- 每个 Agent 的交接记录。
- 工作包、验证队列和最终摘要。

这样做的直接收益是减少 Agent 运行时上下文消耗：

- 新 Agent 不必重新读取整段聊天历史，只需读取画板和相关文件。
- 每次交接都被压缩成固定字段，降低遗漏和幻觉概率。
- Supervisor 可以从画板恢复任务状态，而不是依赖隐含记忆。
- Review 和 Verification 可以直接追踪前序 Agent 的声明、证据和风险。
- 长任务可以分阶段推进，避免上下文膨胀后失控。

## Orchestration Modes

框架默认采用轻量级软约束模式，适合大多数日常代码任务。

当任务命中以下任一条件时，应视为高复杂度任务，并暂停让用户选择是否启用 LangGraph 严格模式：

- 预计修改或新增文件数量超过 3 个。
- 涉及数据库 schema 修改。
- 涉及对外暴露 API 变更。
- 涉及核心业务架构重构。

推荐分流：

- 轻量级模式：速度快，适合小范围修改、文档、局部 bugfix、低风险重构。
- LangGraph 严格模式：适合复杂架构变更、长链路状态机、人工审批门禁、可恢复执行和安全关键流程。

## Repository Layout

```text
project-root/
├── CLAUDE.md
├── AGENT_BOARD.md
├── .claw-policy.json
├── .claw-teams.json
├── migration-checklist.md
├── .claude/
│   ├── agents/
│   │   ├── explorer.md
│   │   ├── planner.md
│   │   ├── implementer.md
│   │   ├── reviewer.md
│   │   ├── verifier.md
│   │   └── supervisor.md
│   └── rules/
│       ├── delegation.md
│       ├── agent-board.md
│       ├── quality-gates.md
│       └── tool-permissions.md
├── LICENSE
└── README.md
```

## Files

- `CLAUDE.md`：最高行为宪法，定义任务理解、最小变更、证据要求、失败协议和人工升级条件。
- `AGENT_BOARD.md`：任务级状态画板，记录目标、范围、交接、决策、验证和最终摘要。
- `.claude/agents/*.md`：角色说明文件，规定每类 Agent 的职责、权限和输出格式。
- `.claude/rules/*.md`：专题规则文件，用于复用委派、状态画板、质量门禁和工具权限制度。
- `.claw-policy.json`：机器可读取的策略配置，声明默认编排模式、预算、禁用工具和 hook 提醒。
- `.claw-teams.json`：多 Agent 团队模板，用于快速组织常见协作队形。
- `migration-checklist.md`：迁移到新项目时的检查清单。

## Quick Start

1. 将本仓库复制到目标项目根目录，或只复制以下文件：

```text
CLAUDE.md
AGENT_BOARD.md
.claw-policy.json
.claw-teams.json
.claude/
migration-checklist.md
```

2. 根据项目实际情况修改：

- 项目目标和业务边界。
- 构建、测试、lint、类型检查命令。
- 禁止触碰的目录、文件或 API。
- 必须人工确认的高风险操作。
- 是否启用 LangGraph 严格模式。

3. 在每次复杂任务开始前，让 Agent 先读取：

```text
CLAUDE.md
AGENT_BOARD.md
.claude/agents/<current-role>.md
.claude/rules/*.md
```

4. 要求每个 Agent 完成阶段后更新 `AGENT_BOARD.md`。

## Recommended Workflow

```text
explorer -> planner -> supervisor -> implementer -> reviewer -> verifier -> supervisor
```

- `explorer` 只读探索，回答“现状是什么”。
- `planner` 制定方案、风险、关键文件和执行顺序。
- `supervisor` 拆分任务、约束边界、决定是否并行。
- `implementer` 只在明确范围内修改文件。
- `reviewer` 做代码审查，优先找缺陷和架构风险。
- `verifier` 运行测试、构建、lint、类型检查并给出证据。
- `supervisor` 汇总结果，判断是否达到完成定义。

## Example Agent Instruction

```md
Read CLAUDE.md and AGENT_BOARD.md first.
Act as the implementer role.
Only modify files listed in Files or Areas Allowed to Change.
After finishing, append a handoff entry to AGENT_BOARD.md with files changed, commands run, evidence, risks, and next recommended agent.
```

## When to Use This

适合：

- 需要多 Agent 协作的代码库。
- 需要严格区分探索、计划、执行、审查和验证的工程流程。
- 需要减少上下文消耗、减少重复探索、提高交接可靠性的长任务。
- 需要把 AI 编程过程沉淀成可审计制度的团队。
- 计划从提示词约束升级到 LangGraph 状态机约束的项目。

不适合：

- 一次性脚本。
- 极小型个人实验。
- 不需要审计、不需要交接、不关心验证证据的任务。

## Roadmap

- 增加 `examples/`，展示低复杂度任务和高复杂度任务的完整运行样例。
- 增加 LangGraph 严格模式状态机模板。
- 增加标准化输出模板：triage、plan、review、verification、handoff。
- 增加跨平台适配说明：Claude Code、Codex、Cursor、Windsurf。
- 将 `.claw-policy.json` 与 `CLAUDE.md` 的复杂度分流规则完全同步。

## License

This project is licensed under the Apache License 2.0. See `LICENSE` for details.
