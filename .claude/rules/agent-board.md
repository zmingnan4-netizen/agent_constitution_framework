# Agent Board Protocol

本文件定义 Agent 如何使用共享项目画板。

## Purpose

`AGENT_BOARD.md` 是任务级共享状态，不是长期规范。它用于帮助多个 Agent 在同一任务中交接上下文，避免重复探索、遗漏验证或责任不清。

## Constitution vs Board

- `CLAUDE.md`：稳定规则，描述 Agent 应如何工作。
- `.claude/agents/*.md`：角色定义，描述每类 Agent 的职责。
- `.claude/rules/*.md`：专题制度，描述跨任务的协作规则。
- `AGENT_BOARD.md`：当前任务画板，描述这一轮任务已经发生了什么、下一步谁该做什么。

## When to Update

Agent 应在以下时机更新画板：

- 完成探索后。
- 完成计划后。
- 完成文件修改后。
- 完成审查后。
- 完成验证后。
- 遇到阻塞时。
- 需要把任务交给另一个 Agent 时。

## What to Record

每条交接记录至少包含：

- 当前 Agent 角色。
- 已完成工作。
- 读取或修改的关键文件。
- 运行过的关键命令。
- 得到的证据。
- 未解决问题。
- 建议下一位 Agent。
- 下一位 Agent 的任务目标。
- 下一位 Agent 的验收标准。

## What Not to Record

不要写入：

- 大段原始日志。
- 与任务无关的推理。
- 敏感信息。
- 未经证实的结论。
- 与当前任务无关的长期愿望。

## Handoff Quality Bar

一条合格的交接记录应让下一位 Agent 不需要重新探索全部上下文，就能继续工作。

如果下一位 Agent 仍必须从零开始理解任务，说明交接记录不合格。

## Supervisor Duties

`supervisor` 负责：

- 初始化 `Current Objective`。
- 创建或更新 `Work Packages`。
- 检查每个 Agent 是否留下交接记录。
- 处理交接记录之间的冲突。
- 在任务结束时填写 `Final Summary`。

