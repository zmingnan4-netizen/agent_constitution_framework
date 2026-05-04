# Usage Guide

本文档提供一条可直接交给 Codex、Claude Code、Cursor 或其他 Agent 工具使用的启动 Prompt，用于把 Agent Constitution Framework 接入现有项目。

目标包括：

1. 启动真实多 Agent 或 soft-constitution 协作流程。
2. 探究并整理用户关于项目的想法。
3. 从现有代码库推断项目事实。
4. 将项目概念、边界、风险和验证方式填充进宪法模板。

## Why Concept Discovery Must Come First

需要增加最开始的概念探究步骤。

原因是：宪法模板不是普通 README，它需要承载项目的工程边界和协作规则。如果 Agent 在不了解项目目标、用户角色、核心实体、业务流程、风险边界和验收方式的情况下直接填充模板，结果通常会变成泛化原则，无法真正约束后续多 Agent 工作。

推荐顺序：

```text
concept discovery -> repository inspection -> constitution filling -> orchestration decision -> task execution
```

如果项目已经有代码，Agent 应先从代码、README、配置、测试和目录结构中推断已有事实，再只向用户询问缺失或高风险问题。

如果项目还没有代码，Agent 必须先和用户完成项目概念与边界讨论，再写入宪法模板。

## Bootstrap Prompt

将下面这段 Prompt 复制给 Codex 或其他支持文件读写的 Agent：

```text
请启动当前项目的 Agent Constitution Framework，并按以下流程执行。

第 0 步：项目概念探究
- 先读取现有项目文件，包括 README、package/config/build/test 文件、主要源码目录、CLAUDE.md、AGENT_BOARD.md、.claw-policy.json、.claw-teams.json、MULTI_AGENT_RUNTIME.md。
- 从现有代码库中推断项目目标、用户角色、核心实体、主要工作流、外部集成、风险点、测试方式和 Definition of Done。
- 不要把不确定内容写成事实。对无法从代码中确认、但会影响宪法填写的问题，集中向用户提问。
- 如果项目还没有足够上下文，先询问用户：
  1. 这个项目解决什么问题？
  2. 目标用户是谁？每类用户能做什么？
  3. 核心业务实体和状态流转是什么？
  4. 哪些流程是高风险、不可逆或需要审计的？
  5. 项目第一版完成标准是什么？
  6. 应该使用哪些构建、测试、lint、类型检查或人工验证方式？

第 1 步：填充宪法模板
- 根据已确认事实更新 CLAUDE.md 中的项目目标、边界、风险、验证要求和人工升级条件。
- 根据当前任务初始化或更新 AGENT_BOARD.md。
- 根据项目实际情况调整 .claw-policy.json 中的 orchestration、budget、hooks、denyTools 和 human confirmation 条件。
- 根据项目需要调整 .claw-teams.json 中的团队模板。
- 保留 Agent Constitution Framework 的核心结构，不要把它改成普通项目说明文档。

第 2 步：启动多 Agent 协作
- 按 MULTI_AGENT_RUNTIME.md 判断 orchestration_mode。
- 如果当前 Codex 或运行时支持子 Agent，并且任务适合拆分，请启用真实多 Agent 模式。
- 如果任务较小或运行时无法创建子 Agent，请使用 soft-constitution 模式，但仍按 explorer -> planner -> implementer -> reviewer -> verifier -> supervisor 的职责边界执行。
- supervisor 必须明确输出 orchestration_mode=soft-constitution 或 orchestration_mode=real-multi-agent，并说明原因。

第 3 步：分发任务
- supervisor 负责读取 .claw-teams.json，并选择合适团队。
- 每个子 Agent 必须收到结构化委派包：
  Role:
  Task:
  Context:
  Scope:
  Forbidden:
  Dependencies:
  Expected Output:
  Acceptance Criteria:
- explorer、planner、reviewer、verifier 默认只读。
- implementer 只能修改 supervisor 明确授权的文件或目录。
- 多个 implementer 不得并行修改同一文件。

第 4 步：执行与记录
- 每个 Agent 完成后，必须向 AGENT_BOARD.md 追加交接记录，包括 Role、Work Completed、Files Read、Files Changed、Commands Run、Evidence、Open Issues、Risks 和 Next Recommended Agent。
- reviewer 必须优先报告缺陷、风险、回归和缺失测试。
- verifier 必须运行可用验证命令，或说明无法验证的原因。
- supervisor 最后汇总 Agents Used、Files Changed、Verification、Known Residual Risks、Follow-Up Tasks 和 Final Decision。

第 5 步：完成标准
- 用户目标已经实现，或明确说明无法实现的原因。
- 宪法模板已填入项目特定内容。
- 多 Agent 或 soft-constitution 流程已按规则执行。
- AGENT_BOARD.md 有本轮任务的清晰交接和最终总结。
- 所有验证结果、残余风险和需要用户决策的事项已经说明。
```

## Short Prompt

日常使用时可以使用这个短版本：

```text
请启动当前项目的 Agent Constitution Framework。
先探究并确认项目概念，再把已确认内容填充进 CLAUDE.md、AGENT_BOARD.md、.claw-policy.json 和 .claw-teams.json。
然后按 MULTI_AGENT_RUNTIME.md 判断是否启用真实多 Agent。
如果适合并且运行时支持，请由 supervisor 分发 explorer、planner、implementer、reviewer、verifier 执行任务。
如果不能创建子 Agent，请使用 soft-constitution 模式模拟同样的角色流程。
所有交接和最终总结必须写入 AGENT_BOARD.md。
```

## What This Prompt Guarantees

它能保证的是：

- Agent 有明确指令先探究项目概念，而不是盲填模板。
- Agent 知道需要把项目事实写入宪法和白板。
- Agent 知道何时尝试真实多 Agent，何时降级为 soft-constitution。
- Agent 知道多 Agent 分发时必须遵守角色、范围、禁止事项和验收标准。

它不能保证的是：

- 运行时一定允许创建真实子 Agent。
- 项目文档能覆盖平台级系统规则。
- 小任务一定会被强行拆成多个 Agent。

因此，本 Prompt 的正确定位是：为支持多 Agent 的工具提供明确启动协议；在不支持真实多 Agent 的工具中，提供可审计的 soft-constitution 替代流程。

