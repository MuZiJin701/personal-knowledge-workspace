# Codex 插件市场管理

插件市场（marketplace）提供可安装插件；插件可包含 skills、MCP servers、app integrations 及其所需文件。

## 查看状态

```powershell
codex plugin marketplace list  # 已配置及隐式发现的市场
codex plugin list              # 已安装插件
codex plugin list --available --json  # 同时列出未安装的市场插件
```

在 ChatGPT Desktop 的 **Plugins** 页面也可浏览、安装和管理插件。安装后新开一个 chat 或 task，使新插件进入上下文。

## 添加市场

```powershell
codex plugin marketplace add ./path/to/marketplace
codex plugin marketplace add owner/repo --ref main
codex plugin marketplace add https://github.com/owner/repo.git --ref main
codex plugin marketplace add https://github.com/owner/repo.git --sparse .agents/plugins
```

`--sparse` 只用于 Git 来源；可以重复传入，以包含 marketplace manifest 和其引用的本地插件目录。

## 安装、更新和移除插件

```powershell
# 安装
codex plugin add PLUGIN@MARKETPLACE
codex plugin add PLUGIN --marketplace MARKETPLACE

# 刷新 Git 市场
codex plugin marketplace upgrade
codex plugin marketplace upgrade MARKETPLACE

# 移除插件，再移除不再需要的市场
codex plugin remove PLUGIN@MARKETPLACE
codex plugin marketplace remove MARKETPLACE
```

`codex plugin remove` 会清除该插件的本地配置和缓存；不要先手动删除 `.codex` 下的目录。

## 本地 marketplace manifest

manifest 只描述市场内容，不会自行注册市场。将其放在 marketplace root 的 `.agents/plugins/marketplace.json` 后，仍需执行 `codex plugin marketplace add <root>`，或在受信任的 `config.toml` 中配置 `[marketplaces.<name>]`。

最小结构：

```text
marketplace-root/
  .agents/plugins/marketplace.json
  plugins/my-plugin/
    .codex-plugin/plugin.json
```

`marketplace.json` 中的本地插件路径相对于 marketplace root 解析。

## 排障顺序

```powershell
codex plugin marketplace list
codex plugin list --available --json
```

确认市场名称、manifest 位置和插件状态后，再重试添加或安装。仅当 CLI 状态已正确而界面仍过期时，重启应用或新开一个 task；不要把删除缓存当作常规步骤。

## 参考

- [插件使用与安装](https://learn.chatgpt.com/docs/plugins)
- [Codex 插件命令参考](https://learn.chatgpt.com/docs/developer-commands?surface=cli)
- [Codex 配置参考](https://learn.chatgpt.com/docs/config-file/config-reference)
