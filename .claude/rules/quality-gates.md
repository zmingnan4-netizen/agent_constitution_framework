# Quality Gates

本文件定义通用质量门禁。迁移到具体项目时，应补充实际命令。

## General Gates

任何非平凡变更都应至少经过：

- 相关测试。
- 构建或编译检查。
- lint 或格式检查。
- 类型检查，如果项目使用类型系统。
- 手动验证，如果自动测试覆盖不足。

## Change-Specific Gates

### API Changes

需要检查：

- 请求和响应契约。
- 向后兼容性。
- 错误码和错误格式。
- 文档或调用方是否需要更新。

### Data Changes

需要检查：

- schema 迁移。
- 回滚路径。
- 默认值。
- 空数据和历史数据。
- 幂等性。

### UI Changes

需要检查：

- 主要视口。
- 空状态。
- 加载状态。
- 错误状态。
- 可访问性基础要求。

### Security-Sensitive Changes

需要检查：

- 鉴权。
- 授权。
- 输入校验。
- 日志泄露。
- secret 暴露。
- 权限边界。

## Verification Report

验证报告必须包含：

```md
Command:
Result:
Evidence:
Notes:
```

如果未运行某项检查，必须写明：

```md
Skipped:
Reason:
Risk:
```

