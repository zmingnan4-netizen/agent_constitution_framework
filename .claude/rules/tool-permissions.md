# Tool Permission Model

本文件定义通用工具权限模型。

## Permission Classes

### Read Only

允许：

- 读取文件。
- 搜索文件。
- 查看 git 状态和 diff。
- 运行不会修改项目的检查命令。

禁止：

- 写文件。
- 删除文件。
- 安装依赖。
- 提交、推送或重置 git。
- 修改配置或环境。

适用角色：

- `explorer`
- `planner`
- `reviewer`
- `verifier`

### Scoped Write

允许：

- 修改任务范围内文件。
- 添加必要测试。
- 运行验证命令。

禁止：

- 修改无关文件。
- 执行破坏性 git 操作。
- 引入未批准依赖。
- 改变公开契约而不说明。

适用角色：

- `implementer`

### Coordinator

允许：

- 读取上下文。
- 委派子任务。
- 汇总结果。
- 请求用户确认。

禁止：

- 越过子 Agent 结论宣布完成。
- 让只读角色写入。
- 忽略策略或预算限制。

适用角色：

- `supervisor`

## High-Risk Operations

以下操作默认需要用户确认：

- 删除文件或目录。
- 数据迁移。
- 大规模重命名。
- 修改认证、授权、加密逻辑。
- 修改部署、CI、生产配置。
- 安装新依赖。
- `git reset`、force push、历史改写。

