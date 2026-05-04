# Delegation Protocol

本文件定义多 Agent 委派协议。它适用于任何项目。

## When to Delegate

适合委派的情况：

- 需要并行探索多个代码区域。
- 需要先规划再执行。
- 需要独立审查或验证。
- 任务风险较高，需要监督角色把关。
- 子任务之间边界清晰。

不适合委派的情况：

- 任务很小，直接完成更快。
- 子任务高度耦合，拆分会增加协调成本。
- 目标尚不清楚。
- 委派会让责任边界变模糊。

## Delegation Packet

每个子任务应使用以下结构：

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

## Dependency Rules

- 没有依赖的探索任务可以并行。
- 修改同一文件的实现任务不应并行。
- 审查应发生在实现之后。
- 验证应发生在实现之后，必要时也发生在修复之后。
- 当审查和验证冲突时，监督角色应要求澄清或重新检查。

## Completion Rules

父 Agent 只有在以下条件满足时才能宣布完成：

- 所有必要子任务已完成或明确取消。
- 必要交接记录已经写入 `AGENT_BOARD.md`。
- 所有阻塞问题已解决。
- 验证结论为 PASS，或 PARTIAL 且风险已清楚说明。
- 用户要求的交付物已经产生。

## Failure Rules

子 Agent 失败时，必须记录：

- 失败角色。
- 失败任务。
- 失败证据。
- 是否可重试。
- 是否需要用户决策。
