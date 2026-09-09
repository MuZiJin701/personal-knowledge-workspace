# Codex 插件市场与配置结构

## 结论

`%USERPROFILE%\.codex` 同时被 ChatGPT App 和 Codex CLI 使用，但 `config.toml` 不是 App 插件的完整安装清单。

| 市场 | 来源 | 配置归属 |
|---|---|---|
| `openai-primary-runtime` | 本机运行时目录 | App 维护 |
| `openai-bundled` | `.codex\.tmp\bundled-marketplaces` | App 维护 |
| `openai-curated` | `.codex\.tmp\plugins` 官方市场快照 | 自动注入，不写市场表 |
| `ponytail` | Git 仓库 | 用户维护 |

`openai-curated-remote` 不是市场；它只是 App 下载官方精选插件时使用的缓存目录名。

## 安装规则

| 操作 | 缓存位置 | `config.toml` |
|---|---|---|
| App 安装官方精选插件 | `plugins\cache\openai-curated-remote\<插件>\<版本>` | 不可作为安装状态依据；新安装可不写入 |
| CLI：`codex plugin add <插件>@openai-curated` | `plugins\cache\openai-curated\<插件>\<版本>` | 新增 `[plugins."<插件>@openai-curated"]` |
| 安装 Bundled/Runtime 插件 | 对应 `openai-bundled` / `openai-primary-runtime` 缓存 | 启用项写入 `[plugins]` |

实测：App 安装 Superpowers 只生成 `openai-curated-remote` 缓存；CLI 安装同一插件则生成 `openai-curated` 缓存和配置项。两份缓存不复用。

## 维护规则

- 不手工添加 `[marketplaces.openai-curated]` 或任何 `openai-curated-remote` 市场。
- App 插件只通过 App 的插件页安装、卸载；CLI 插件只通过 `codex plugin add/remove` 管理。
- 不直接删除仍在使用的缓存目录；先用对应安装端卸载。
- `config.toml` 中只保留 Runtime、Bundled、Ponytail，以及刻意通过 CLI 安装的 `@openai-curated` 插件。

## 必须保留的安全设置

```toml
sandbox_mode = "danger-full-access"
approval_policy = "on-request"
approvals_reviewer = "auto_review"

[windows]
sandbox = "elevated"
```

它们分别控制总体沙箱、Windows 沙箱层、操作批准时机与批准审查方式，不能合并或按“重复”删除。

`[sandbox_workspace_write]` 当前仅保留 `network_access` 的注释，作为将来的显式开关说明；它不改变当前行为。

## 本次整理

- 移除了旧 `@openai-curated-remote` 配置项和 CLI 实验副本。
- 保留 App 的远程缓存、官方市场、Runtime/Bundled 插件与 Ponytail。
- 删除了三个无内容的 TOML 空表：两项主题字体表和空的 `[hooks.state]` 父表。

官方说明只确认插件可扩展 ChatGPT 与 Codex；上述本机缓存和配置分工来自实际安装、卸载实验。[OpenAI Developers](https://developers.openai.com/)
