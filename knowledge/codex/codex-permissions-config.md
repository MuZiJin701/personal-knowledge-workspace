# Codex 权限配置速查

用户配置在 `~/.codex/config.toml`；可信项目可覆盖部分设置。先区分沙箱边界、审批时机和审核者。

## 字段与预设

| 字段 | 作用 |
| --- | --- |
| sandbox_mode | read-only、workspace-write、danger-full-access：控制命令的文件与网络边界。 |
| approval_policy | on-request、never 或 granular：何时进入批准流程；untrusted 已不受支持。 |
| approvals_reviewer | user 或 auto_review：谁审核；不改变沙箱。 |
| sandbox_workspace_write.network_access | workspace-write 下是否允许命令直接联网。 |
| web_search | disabled、cached、indexed、live：搜索模式，与命令联网分开。 |

| 桌面预设 | sandbox_mode | approval_policy | approvals_reviewer |
| --- | --- | --- | --- |
| Ask for approval | workspace-write | on-request | user |
| Approve for me | workspace-write | on-request | auto_review |
| Full access | danger-full-access | never | 可省略 |

auto_review 只审核进入流程且可审核的请求，不逐条审核已允许的命令，也不保证获批。Full access 移除本地沙箱与交互审批，宿主策略仍可能另有限制。

## 常用配置

顶层字段放在 `[sandbox_workspace_write]` 等表之前。以下配置择一使用。

工作区内运行，越界时向用户申请：

```toml
sandbox_mode = "workspace-write"
approval_policy = "on-request"
approvals_reviewer = "user"

[sandbox_workspace_write]
network_access = false
```

需要自动审核时，将 reviewer 改为 auto_review；允许工作区命令直接联网时，将 network_access 改为 true。只读审查可将 sandbox_mode 改为 read-only，并移除不适用的 workspace-write 表。

信任环境且明确需要完全访问时：

```toml
sandbox_mode = "danger-full-access"
approval_policy = "never"
```

## 当前自定义配置（2026-10-07 核验）

当时用户配置及聊天运行状态为：

```toml
sandbox_mode = "danger-full-access"
approval_policy = "on-request"
approvals_reviewer = "auto_review"
```

它关闭本地文件与网络沙箱，但保留按需审批，交给自动审核；on-request 不表示每条命令都询问。这是历史自定义组合，后续会话覆盖可能改变实际行为。

在 danger-full-access 下：要关闭审批用 never；要由用户审核用 on-request + user；要自动审核用上面的组合。

字段注意：

- 名称是 approvals_reviewer，不是 approval_reviewer。
- workspace-write 表中的 network_access、writable_roots 不能约束 danger-full-access。
- `[windows] sandbox = "elevated"` 指定原生沙箱实现，不证明当前命令仍被沙箱隔离。
- Web 搜索和插件权限单独设置，不随本地命令权限一起放开。

未知字段和 Guardian 警告的历史案例见 [目录维护](codex-home-cleanup.md#配置警告先验证当前版本)。

## 参考

- [权限模式](https://learn.chatgpt.com/docs/permission-modes)
- [沙箱](https://learn.chatgpt.com/docs/sandboxing)
- [配置参考](https://learn.chatgpt.com/docs/config-file/config-reference)
- [批准与安全](https://learn.chatgpt.com/docs/agent-approvals-security)
