# Agent Board

这是多 Agent 协作的项目画板模板。它不是宪法本身，而是每轮任务运行时的共享工作台。

迁移到新项目后，建议把本文件放在项目根目录，并要求所有参与协作的 Agent 在完成自己的阶段后更新对应区域。

## Board Rules

- 每个 Agent 完成工作后必须写入一条交接记录。
- 只记录与当前任务有关的信息。
- 不把大段日志、完整测试输出或无关推理写入画板。
- 不覆盖其他 Agent 的记录，除非是在明确修正自己的上一条记录。
- 如果任务失败，必须记录失败原因和建议下一步。
- 如果需要人工决策，必须在 `Human Decisions Needed` 中列出。

## Current Objective

```md
Task:
Requested By:
Started At:
Current Status: not-started | exploring | planning | implementing | reviewing | verifying | blocked | done
Definition of Done:
```

## Shared Context

记录所有角色都需要知道的关键背景。

```md
- 
```

## Scope

### In Scope

```md
- 
```

### Out of Scope

```md
- 
```

### Files or Areas Allowed to Change

```md
- 
```

### Files or Areas Not Allowed to Change

```md
- 
```

## Agent Handoff Log

每个 Agent 完成自己的阶段后，在顶部追加一条记录。

```md
### YYYY-MM-DD HH:MM - agent-name

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

## Work Packages

用于记录监督 Agent 拆出的子任务。

```md
### task-001

Owner Role:
Status: pending | in-progress | blocked | done | cancelled
Goal:
Inputs:
Scope:
Forbidden:
Dependencies:
Expected Output:
Acceptance Criteria:
Result:
```

## Decisions

记录已经做出的关键决策。

```md
### decision-001

Decision:
Reason:
Alternatives Considered:
Made By:
Date:
Impact:
```

## Human Decisions Needed

记录需要用户或维护者确认的问题。

```md
### question-001

Question:
Why It Matters:
Options:
Recommended Option:
Blocked Work:
```

## Verification Queue

记录需要验证的内容。

```md
### verification-001

What to Verify:
Command or Method:
Expected Result:
Actual Result:
Status: pending | pass | fail | skipped
Verifier:
```

## Final Summary

任务结束时由 `supervisor` 填写。

```md
Outcome:
Agents Used:
Files Changed:
Verification:
Known Residual Risks:
Follow-Up Tasks:
Final Decision: done | partial | failed
```

