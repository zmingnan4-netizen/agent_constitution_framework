# Migration Checklist

将这套 Agent 宪法迁移到新项目时，按以下步骤检查。

## 1. Place Files

- [ ] 将 `CLAUDE.md` 放到项目根目录。
- [ ] 将 `AGENT_BOARD.md` 放到项目根目录。
- [ ] 将 `.claude/agents/*.md` 放到项目根目录的 `.claude/agents/`。
- [ ] 将 `.claude/rules/*.md` 放到项目根目录的 `.claude/rules/`。
- [ ] 将 `.claw-policy.json` 放到项目根目录。
- [ ] 将 `.claw-teams.json` 放到项目根目录。

## 2. Customize Project-Specific Rules

- [ ] 在 `CLAUDE.md` 中补充项目目标。
- [ ] 补充架构边界。
- [ ] 补充禁止修改的目录或文件。
- [ ] 补充依赖管理规则。
- [ ] 补充发布、迁移、部署相关限制。

## 3. Customize Verification

- [ ] 写明测试命令。
- [ ] 写明构建命令。
- [ ] 写明 lint 命令。
- [ ] 写明类型检查命令。
- [ ] 写明手动验证要求。

## 4. Customize Permissions

- [ ] 检查哪些 Agent 可以写文件。
- [ ] 检查哪些工具应被禁用。
- [ ] 检查哪些操作必须人工确认。
- [ ] 检查预算限制是否适合项目规模。

## 5. Test the Constitution

用一个低风险任务试运行：

- [ ] 让 `explorer` 找相关文件。
- [ ] 让 `planner` 给出计划。
- [ ] 让 `implementer` 做小改动。
- [ ] 让 `reviewer` 审查。
- [ ] 让 `verifier` 验证。
- [ ] 让 `supervisor` 汇总。

## 6. Review Outcomes

- [ ] 是否减少了无关改动？
- [ ] 是否更早暴露不确定性？
- [ ] 是否有清楚的验证证据？
- [ ] 是否避免了角色越权？
- [ ] 是否方便审计子任务结果？
