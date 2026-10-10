# Matt Pocock Skills 2026-10-10 更新核查

检查日期：2026-10-10（Asia/Shanghai）。接续 [2026-10-08 核查](github-skills-updates-2026-10-08.md)，依据用户提供的 CLI 日志和上游 GitHub API、固定源码。

## 基准与结论

比较范围固定为 [`f3fc563...49dd158`](https://github.com/mattpocock/skills/compare/f3fc5632f401156837ee3872f14fe33ccf1024ea...49dd158d1076134a641b33efb035946536778336)：3 个提交、7 个文件，新增 160 行、删除 49 行。HEAD `49dd158d1076134a641b33efb035946536778336` 的提交时间为北京时间 2026-10-09 18:56:49。

只有 `wizard` 的技能文件发生变化，修改的是 `template.sh`，其 `SKILL.md` 未变。没有新增或删除技能：固定目录树仍有 38 个 `SKILL.md`，Engineering 20、Productivity 7、In Progress 7、Misc 4；插件仍包含 27 项稳定技能。最新 GitHub Release 和插件版本仍为 [`v1.3.1`](https://github.com/mattpocock/skills/releases/tag/v1.3.1)，本次属于发布后的 `main` 更新。[固定目录树](https://github.com/mattpocock/skills/tree/49dd158d1076134a641b33efb035946536778336/skills)、[插件清单](https://github.com/mattpocock/skills/blob/49dd158d1076134a641b33efb035946536778336/.claude-plugin/plugin.json)

## wizard 的五项修复

| 改动 | 用户可见结果 |
| --- | --- |
| `_existing` 识别普通双引号 `.env` 值 | 原有 `KEY="value"` 按 Enter 保留时得到 `value`，不再把外层引号写进值中。内部含反斜杠、`$` 或双引号时拒绝猜测，要求重新输入。 |
| 阶段放入 `run_wizard` | 第一处提示之前先解析完整函数，避免运行中编辑脚本破坏后续阶段。 |
| `_clear` 增加 ANSI 回退 | `tput` 存在但 `tput clear` 失败时仍继续运行，避免 `set -e` 终止向导。 |
| `write_env` 同步 shell 变量 | 写入 `.env` 后，同名变量立即可供当前 shell 的后续阶段使用；此处没有自动 `export`。 |
| 删除未使用的 `RED` | 清理无用颜色变量及相关静态检查问题。 |

以上来自 [PR #1237](https://github.com/mattpocock/skills/pull/1237)及[固定模板源码](https://github.com/mattpocock/skills/blob/49dd158d1076134a641b33efb035946536778336/skills/engineering/wizard/template.sh)。另新增 `.changeset/wizard-template-fixes-2.md` 记录补丁，未发布新版本。已生成的 wizard 是独立副本，本次更新不会修复旧脚本；PR 也明确没有处理 Windows Git Bash 的浏览器打开问题 [#963](https://github.com/mattpocock/skills/issues/963)。

## 其余仓库变化

[PR #1218](https://github.com/mattpocock/skills/pull/1218)修改 `README.md`、`.agents/install-block.md`、ADR 0002 和 `CLAUDE.md`，把可自动更新的插件安装路线放在前面：Claude Code 使用官方 marketplace，Codex 使用 `@mattpocock` marketplace，Copilot CLI 需要一次性开启 `autoUpdate`，VS Code 使用从源码安装插件；Gemini CLI 和 Skills CLI 仍标为手动更新。Skills CLI 可以用 `-a <agent>` 选定目标；`update` 不安装上游新增技能，需重新 `add`。

这些是上游安装文档的说明，本次没有在本机验证各 Agent 的插件安装或自动更新。使用仓库自有 marketplace 的托管路线跟随 `plugin.json` 的版本更新；Claude 官方 marketplace 还受其固定提交更新影响，并非每个 `main` 提交都推送给插件用户。因此不能据此认定插件安装已取得本次 `wizard` 修复。每个 Agent 应选择插件或可编辑文件路线之一，避免重复技能。[固定安装说明](https://github.com/mattpocock/skills/blob/49dd158d1076134a641b33efb035946536778336/.agents/install-block.md)

[PR #1240](https://github.com/mattpocock/skills/pull/1240)只新增 `.out-of-scope/lazy-load-writing-for-agents.md`：记录 `retro` 每次都加载 `writing-for-agents` 的既有决定，因为只读审查也需要其评价标准；没有修改 `retro` 的技能文件。

## 本机证据与边界

用户日志中的 `skills update -g -y` 找到 1 项更新，`wizard` 显示 `Updated`，与上游技能文件差异一致。随后 `skills add mattpocock/skills -g -y` 找到 38 项，显示 `79 agents` 并列出安装目标；粘贴日志没有最终安装完成或失败汇总，不能据此认定所有目标均安装成功。

本机只读检查得到 Skills CLI `1.7.2`。`%USERPROFILE%\.agents\.skill-lock.json` 中 38 项目录哈希全部匹配固定 HEAD，更新时间为北京时间 2026-10-10 11:08:05.942 至 11:08:06.258。进一步比对 `%USERPROFILE%\.agents\skills\` 中的 103 个上游技能包文件，归一化 CRLF/LF 后 Git blob 全部一致，零缺失、零内容差异；技能包之外的 5 个分类目录 README 未纳入。本次不审计本机额外文件，也不验证各 Agent 的运行时加载。

本次仅研究公开源码并记录核查结果，没有运行 wizard、迁移安装方式或改动项目工程配置。上游仅有模板和安装说明变化，Issue tracker、标签映射及领域文档无需因这次更新调整。

后续文档同步已更新[稳定技能指南](Matt-Pocock稳定Skills使用说明.md)、[CLI 指南](skills%20CLI与常用命令.md)、[工作目录约定](按照Matt-Pocock设计理念的标准工作目录结构.md)和[本机 CLI 记录](../node-js/本机Node.js与npm环境记录.md)，并补齐历史记录的导航。本机 Codex CLI 帮助确认了插件命令形式，未执行安装或验证自动更新。
