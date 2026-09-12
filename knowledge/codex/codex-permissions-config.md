# Codex 权限配置速查

Codex 的用户级配置在 `~/.codex/config.toml`；可信项目可用 `.codex/config.toml` 覆盖部分设置。

## 核心参数

| 参数 | 可用值 | 作用 |
| --- | --- | --- |
| `sandbox_mode` | `read-only`、`workspace-write`、`danger-full-access` | 限制命令的文件和网络访问范围。 |
| `approval_policy` | `on-request`、`never`，或 granular 配置 | 控制 Codex 何时暂停并请求批准。 |
| `approvals_reviewer` | `user`、`auto_review` | 指定谁审核可审核的批准请求；不改变沙箱边界。 |
| `sandbox_workspace_write.network_access` | `true`、`false` | 在 `workspace-write` 下是否允许命令出站联网。 |
| `web_search` | `disabled`、`cached`、`indexed`、`live` | 控制 Web 搜索模式，与命令联网权限无关。 |

`approval_policy = "untrusted"` 已不受支持；交互式运行通常使用 `on-request`，非交互式运行使用 `never`。

## 常用预设

### 只读审查

```toml
sandbox_mode = "read-only"
approval_policy = "on-request"
approvals_reviewer = "user"
```

### 日常开发

```toml
sandbox_mode = "workspace-write"
approval_policy = "on-request"
approvals_reviewer = "user"

[sandbox_workspace_write]
network_access = false
```

如需让命令联网，将 `network_access` 改为 `true`。

### 自动审核低风险请求

```toml
sandbox_mode = "workspace-write"
approval_policy = "on-request"
approvals_reviewer = "auto_review"

[sandbox_workspace_write]
network_access = false
```

### 完全访问

```toml
sandbox_mode = "danger-full-access"
approval_policy = "never"
```

这会移除本地沙箱和交互批准保护；仅在你信任当前任务与环境时使用。宿主或工作区策略仍可能施加额外限制。

## 参考

- [Codex 配置参考](https://learn.chatgpt.com/docs/config-file/config-reference)
- [批准与安全说明](https://learn.chatgpt.com/docs/agent-approvals-security)
