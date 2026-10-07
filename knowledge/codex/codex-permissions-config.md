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

## ChatGPT 权限预设与 config.toml 的对应关系

ChatGPT 桌面端权限菜单中的三个预设，对应以下 `config.toml` 配置组合。映射依据 [官方权限模式说明](https://learn.chatgpt.com/docs/permission-modes)和 [沙箱说明](https://learn.chatgpt.com/docs/sandboxing)。

| 界面预设 | `sandbox_mode` | `approval_policy` | `approvals_reviewer` |
| --- | --- | --- | --- |
| **Ask for approval**（请求用户批准） | `"workspace-write"` | `"on-request"` | `"user"` |
| **Approve for me**（代我审批） | `"workspace-write"` | `"on-request"` | `"auto_review"` |
| **Full access**（完全访问） | `"danger-full-access"` | `"never"` | 本地命令不走批准流程，可省略。 |

Ask for approval 允许在当前工作区读写文件、运行常规命令；需要越过沙箱边界时向用户请求批准。Approve for me 保留同样的边界，将符合条件的批准请求交给自动审核代理；它不等于 `approval_policy = "never"`，也不保证所有请求都会获批。Full access 移除本地命令的文件与网络沙箱边界，并关闭交互批准。

以下示例可在配置文件中复现对应行为，择一使用。`sandbox_mode`、`approval_policy` 和 `approvals_reviewer` 是顶层字段，应放在 `[sandbox_workspace_write]` 等表之前。前两个预设显式设置 `network_access = false`，让命令联网进入批准流程；改为 `true` 会允许沙箱内命令直接出站联网。Full access 下此表不限制命令联网。Web 搜索和插件另有控制。

## 配置示例

### 只读审查

```toml
sandbox_mode = "read-only"
approval_policy = "on-request"
approvals_reviewer = "user"
```

### Ask for approval：请求用户批准

```toml
sandbox_mode = "workspace-write"
approval_policy = "on-request"
approvals_reviewer = "user"

[sandbox_workspace_write]
network_access = false
```

如需让命令联网，将 `network_access` 改为 `true`。

### Approve for me：自动审核批准请求

```toml
sandbox_mode = "workspace-write"
approval_policy = "on-request"
approvals_reviewer = "auto_review"

[sandbox_workspace_write]
network_access = false
```

### Full access：完全访问

```toml
sandbox_mode = "danger-full-access"
approval_policy = "never"
```

这会移除本地沙箱和交互批准保护；仅在你信任当前任务与环境时使用。宿主或工作区策略仍可能施加额外限制。

## 当前自定义配置（2026-10-07 核验）

本次从用户级 `config.toml` 读取到：

```toml
sandbox_mode = "danger-full-access"
approval_policy = "on-request"
approvals_reviewer = "auto_review"
```

该组合关闭本地命令的文件与网络沙箱限制，保留按需批准机制，并将符合条件的批准请求交给自动审核代理。`on-request` 不表示每次操作都询问；`auto_review` 只处理进入审核流程的请求，不会逐条审核已经允许执行的命令，也不保证请求获批。本聊天当时的运行环境也显示沙箱关闭、审核者为 `auto_review`。以上是核验时的状态，后续配置变更或桌面会话覆盖可能改变实际行为。

这是自定义组合：与 Approve for me 相比，`sandbox_mode` 从 `workspace-write` 改为 `danger-full-access`；与 Full access 相比，`approval_policy` 保持 `on-request`，而不是 `never`。

固定使用 `danger-full-access` 时，可按审批需求选择：

| 审批需求 | `approval_policy` | `approvals_reviewer` |
| --- | --- | --- |
| 关闭本地命令审批，与 Full access 预设一致 | `"never"` | 可省略。 |
| 需要审批时由用户决定 | `"on-request"` | `"user"`；默认值，可省略。 |
| 需要审批时由自动审核代理处理（当前配置） | `"on-request"` | `"auto_review"` |

相关字段的处理：

- 正确字段名是 `approvals_reviewer`，不是 `approval_reviewer`。
- `[sandbox_workspace_write]` 下的 `network_access`、`writable_roots` 等只适用于 `workspace-write`；当前模式无需设置，也不能用它们恢复联网或文件访问限制。
- `[windows] sandbox = "elevated"` 指定 Windows 原生沙箱实现；保留该设置不意味着当前 `danger-full-access` 命令仍受沙箱隔离。
- `web_search` 控制搜索功能；插件、连接器审批另有设置，不必随本地沙箱模式一并放开。

行为说明依据 [官方配置参考](https://learn.chatgpt.com/docs/config-file/config-reference)和 [沙箱说明](https://learn.chatgpt.com/docs/sandboxing)。

配置未知字段、Guardian 废弃开关及桌面下发配置的排查，见 [用户目录清理与配置警告处理](codex-home-cleanup.md#配置警告先验证当前版本)。

## 参考

- [ChatGPT 权限模式](https://learn.chatgpt.com/docs/permission-modes)
- [沙箱与权限预设](https://learn.chatgpt.com/docs/sandboxing)
- [Codex 配置参考](https://learn.chatgpt.com/docs/config-file/config-reference)
- [批准与安全说明](https://learn.chatgpt.com/docs/agent-approvals-security)
