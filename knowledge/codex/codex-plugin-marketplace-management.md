# Codex 插件市场管理

插件可提供 skills、MCP 和 app integrations。本篇集中说明安装命令与配置归属；环境准备见[新服务器基础环境配置](linux-server-setup.md)，安装登录和远程协同见 [Linux 云服务器使用 Codex](Linux云服务器使用Codex指南.md)。

## 1. 查找、安装和移除

先看当前版本支持的命令与状态：

```powershell
codex plugin --help
codex plugin marketplace list
codex plugin list
codex plugin list --available --json
```

添加市场，按来源选一条：

```powershell
codex plugin marketplace add ./path/to/marketplace
codex plugin marketplace add owner/repo --ref main
codex plugin marketplace add https://github.com/owner/repo.git --ref main
codex plugin marketplace add https://github.com/owner/repo.git --sparse .agents/plugins
```

--sparse 只用于 Git 来源，可重复指定，须包含 manifest 及其引用的本地插件目录。

```powershell
codex plugin add PLUGIN@MARKETPLACE
codex plugin marketplace upgrade MARKETPLACE
codex plugin remove PLUGIN@MARKETPLACE
codex plugin marketplace remove MARKETPLACE
```

`codex plugin add PLUGIN --marketplace MARKETPLACE` 是另一种写法；upgrade 不带市场名时刷新全部 Git 市场。先移除插件，再移除不用的市场；remove 会清除相应本地配置和缓存，不先手工删目录。

App 插件在 App 的 Plugins 页面管理，CLI 插件用 CLI 管理。安装后新开会话以加载插件。

## 2. App 与 CLI 的配置归属

以下为 **2026-10-07 本机实验**，不保证后续版本目录相同。`%USERPROFILE%\.codex` 由 App 与 CLI 共用，但 config.toml 不是 App 插件完整清单。

| 市场 | 来源与管理方 |
| --- | --- |
| openai-primary-runtime | 本机运行时，App 维护。 |
| openai-bundled | `.codex\.tmp\bundled-marketplaces`，App 维护。 |
| openai-curated | `.codex\.tmp\plugins` 中的官方快照，自动注入，不手工写市场表。 |
| ponytail | 用户添加的 Git 仓库。 |

**openai-curated-remote 是缓存目录名，不是市场名。**

| 安装入口 | 缓存及配置 |
| --- | --- |
| App 官方精选插件 | `plugins\cache\openai-curated-remote\<插件>\<版本>`；可能不写 config.toml。 |
| CLI 的 PLUGIN@openai-curated | `plugins\cache\openai-curated\<插件>\<版本>`；写 `[plugins."PLUGIN@openai-curated"]`。 |
| Bundled / Runtime | 各自缓存目录，启用项写入 `[plugins]`。 |

当时用 Superpowers 对照安装，两种入口产生独立缓存，不复用。不要添加 `[marketplaces.openai-curated]` 或 openai-curated-remote 市场，不凭缓存目录存在判断插件仍启用。

当次清理移除了旧 @openai-curated-remote 配置、CLI 实验副本、两项空主题字体表和空 hooks.state 父表；保留 App 缓存及官方、Runtime、Bundled、Ponytail 配置。上述是历史操作记录，不是当前待执行清单。

## 3. 本地市场与排障

最小目录：

```text
marketplace-root/
  .agents/plugins/marketplace.json
  plugins/my-plugin/
    .codex-plugin/plugin.json
```

manifest 的本地插件路径相对 marketplace root。只创建 manifest 不会注册市场，还需 marketplace add 或受信任的 `[marketplaces.<name>]` 配置。

出问题时按顺序检查：市场名与来源 → manifest 位置 → 插件状态 → 安装端。CLI 状态正常而界面过期时，再新开会话或重启；不把删除缓存当作常规排障。权限选择另见 [权限配置](codex-permissions-config.md)，插件管理不顺带放开沙箱。

## 参考

- [插件使用与安装](https://learn.chatgpt.com/docs/plugins)
- [CLI 命令](https://learn.chatgpt.com/docs/developer-commands?surface=cli)
- [配置参考](https://learn.chatgpt.com/docs/config-file/config-reference)
