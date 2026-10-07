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

## 当前保留的权限配置

2026-10-07 核验的用户级配置如下；这是当前选择的自定义组合，不是所有环境必须采用的安全默认值。

```toml
sandbox_mode = "danger-full-access"
approval_policy = "on-request"
approvals_reviewer = "auto_review"

[windows]
sandbox = "elevated"
```

前三项分别控制本地命令的沙箱边界、批准时机与审核者：当前没有文件与网络沙箱限制，符合条件的批准请求交给自动审核代理；已允许的命令不会逐条自动审核。`windows.sandbox` 指定 Windows 原生沙箱实现，保留它不表示当前命令仍受沙箱隔离。

`[sandbox_workspace_write]` 中的字段只在 `workspace-write` 模式下生效。与三个标准预设的区别及字段选择，见 [权限配置速查：当前自定义配置](codex-permissions-config.md#当前自定义配置2026-10-07-核验)。插件整理不应顺带修改这些权限选择。

## 本次整理

- 移除了旧 `@openai-curated-remote` 配置项和 CLI 实验副本。
- 保留 App 的远程缓存、官方市场、Runtime/Bundled 插件与 Ponytail。
- 删除了三个无内容的 TOML 空表：两项主题字体表和空的 `[hooks.state]` 父表。

官方说明只确认插件可扩展 ChatGPT 与 Codex；上述本机缓存和配置分工来自实际安装、卸载实验。[OpenAI Developers](https://developers.openai.com/)
