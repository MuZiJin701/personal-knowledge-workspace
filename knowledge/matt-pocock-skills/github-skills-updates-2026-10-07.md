# Matt Pocock Skills 2026-10-07 更新核查

检查日期：2026-10-07（Asia/Shanghai）。接续 [2026-10-05 核查](github-skills-updates-2026-10-05.md)，依据用户提供的 CLI 日志、GitHub 固定提交和本机只读检查。

## 基准与结论

比较范围固定为 [`24fe0ef...6fd9479`](https://github.com/mattpocock/skills/compare/24fe0ef7737efae15c87225755e9f6f5965e4888...6fd947921b935b7e1e69293a200400f0fdd5c15f)：28 个提交、55 个文件净变化。HEAD `6fd947921b935b7e1e69293a200400f0fdd5c15f` 的提交时间为北京时间 2026-10-06 21:41:22。

最新 GitHub Release 和仓库版本字段仍为 `v1.3.1`。本次五项修复与新实验技能已进入 `main`，尚未形成新的发布版本；不能用版本号是否变化判断通过 Git 源安装的技能是否更新。[HEAD](https://github.com/mattpocock/skills/commit/6fd947921b935b7e1e69293a200400f0fdd5c15f) [版本字段](https://github.com/mattpocock/skills/blob/6fd947921b935b7e1e69293a200400f0fdd5c15f/package.json) [更新日志](https://github.com/mattpocock/skills/blob/6fd947921b935b7e1e69293a200400f0fdd5c15f/CHANGELOG.md)

## 五个已安装技能的修复

| Skill | 最终行为 | 来源 |
| --- | --- | --- |
| `implement` | 明确调用 Skill 工具执行 `tdd` 和 `code-review`，替换仅写 `/skill` 的指令。 | [#1189](https://github.com/mattpocock/skills/pull/1189) |
| `setup-matt-pocock-skills` | 外部 PR 列表改用 REST pulls API，排除 `OWNER`、`MEMBER`、`COLLABORATOR`；补充 GitHub 子 Issue 操作、旧版 CLI API 后备命令。 | [#1187](https://github.com/mattpocock/skills/pull/1187)、[#1188](https://github.com/mattpocock/skills/pull/1188) |
| `to-tickets` | 从已有 Issue 拆出的 ticket 挂为其子 Issue；归属和阻塞分别处理，已有原生阻塞边时省略正文的 `Blocked by` 段落。 | [#1188](https://github.com/mattpocock/skills/pull/1188) |
| `handoff` | 临时目录明确为 Windows `%TEMP%`；其他系统 `$TMPDIR`，未设置时 `/tmp`。最终合并差异没有新增强制报告绝对路径的规则。 | [#1184](https://github.com/mattpocock/skills/pull/1184) |
| `claude-handoff` | 摘要先写临时文件，再作为 `claude --bg` 的提示词传入，避免摘要中的反引号、`$()` 和变量被 Shell 二次解释。 | [#1185](https://github.com/mattpocock/skills/pull/1185) |

`claude-handoff` 上游示例为 Bash 形式 `claude --bg --name "<name>" -- "$(cat <summary file>)"`。Windows PowerShell 应读取临时文件到变量，再将该变量作为单个参数传入；上游示例不代表已提供跨 Shell 脚本。

## 新增实验技能

`chief-of-staff` 于北京时间 10 月 5 日 19:15 新增，10 月 6 日继续修改。它协调后台子代理和定期安排来追踪长期目标，要求同时关注当前任务与后续任务的环境改善，并通过文档、提交等上下文指针减少重复沟通。位于 `skills/in-progress/`，不进入主推插件；`disable-model-invocation: true` 和 `allow_implicit_invocation: false` 表明它必须由用户显式启动。[新增提交](https://github.com/mattpocock/skills/commit/e47c149e9489bccd6acb41a1fb420ed68a5067e1) [固定版本源码](https://github.com/mattpocock/skills/blob/6fd947921b935b7e1e69293a200400f0fdd5c15f/skills/in-progress/chief-of-staff/SKILL.md)

| 分类 | 数量 |
| --- | ---: |
| Engineering | 20 |
| Productivity | 7 |
| In Progress | 7 |
| Misc | 4 |
| 全部 | 38 |

主推清单仍为 27 项；新增项来自实验分类。[固定版本技能目录](https://github.com/mattpocock/skills/tree/6fd947921b935b7e1e69293a200400f0fdd5c15f/skills)

## 文档与仓库维护变化

- 使用说明集中改写措辞；这批 `docs/` 文件变化不等于对应 Skill 实现都变了。
- 新增 `SCOPE.md`、Issue 表单和范围外请求记录。贡献要求提供实际会话中的失败；不接受新技能提案或贡献；`misc/` 冻结维护。tracker 的一等支持限定为 GitHub、GitLab、本地 Markdown，其他工具由用户维护自定义流程。[范围说明](https://github.com/mattpocock/skills/blob/6fd947921b935b7e1e69293a200400f0fdd5c15f/SCOPE.md)
- 技能重名、宿主提问 UI 和子代理递归限制等问题按上游规定交给宿主或用户配置解决；这是上游贡献范围，不替代本工作区自己的规则。[递归范围说明](https://github.com/mattpocock/skills/blob/6fd947921b935b7e1e69293a200400f0fdd5c15f/.out-of-scope/subagent-recursion.md)
- 上游新增 Issue 自动分诊标签和 `needs-info` 14 天无回应关闭流程；没有随技能安装自动部署到用户项目。[#1170](https://github.com/mattpocock/skills/pull/1170)
- 安装说明修正 Claude 官方市场的更新承诺：市场固定提交由 Anthropic 更新，可能落后仓库；直接通过 Git 源安装需与市场发布区分。[安装说明](https://github.com/mattpocock/skills/blob/6fd947921b935b7e1e69293a200400f0fdd5c15f/.agents/install-block.md)

## 本机安装与 CLI 日志

用户先运行 `skills update -g -y`，成功更新 `implement`、`setup-matt-pocock-skills`、`to-tickets`、`handoff`、`claude-handoff` 共 5 项；随后 `skills add mattpocock/skills -g -y` 发现并安装全部 38 项，补入 `chief-of-staff`。新增技能由 `add` 引入，不能归因于只刷新已有项的 `update`。

只读检查 `%USERPROFILE%\.agents\.skill-lock.json`：38 项目录哈希全部与固定 HEAD 的 Git tree 匹配；安装记录时间为北京时间 2026-10-07 14:10:40 至 14:10:41。进一步比对 `%USERPROFILE%\.agents\skills\` 中的 103 个上游文件，归一化 CRLF/LF 后 Git blob 全部一致，没有缺失。此检查不审计本机额外文件，也不验证每个 Agent 的运行时加载。

日志的 `Failed to install 38` 均为 PromptScript 不支持全局安装；不代表 38 个技能均未写入磁盘。`79 agents` 是 CLI 定义总数，不能据此推导全部 Agent 都已安装成功。此次本机 `skills --version` 为 `1.7.1`，与技能仓库 `1.3.1` 分别计数。[CLI 说明](skills%20CLI与常用命令.md)

## 对工作区文档的同步

`skills update/add` 更新技能安装目录，不会重写项目中此前生成的 `docs/agents/*.md`。本次文档任务直接更新现有 GitHub tracker 规则，保留 `PRs as a request surface: no`，补齐父子和阻塞操作；本机 `gh` 2.102.0 的帮助已确认相关参数。没有对 GitHub 创建或修改 Issue，也没有部署上游 Actions。

同时将工作区原有 `CONTEXT-MAP.md` 和 `docs/agents/CONTEXT.md` 迁移为 `GLOSSARY-MAP.md` 和 `docs/agents/GLOSSARY.md`，更新规则、索引和双语 README 的入口。这是补齐此前已进入上游的命名约定，不属于 10 月 6 日新增功能。
