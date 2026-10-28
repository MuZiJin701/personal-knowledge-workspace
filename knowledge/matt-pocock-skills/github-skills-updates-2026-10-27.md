# Matt Pocock Skills 2026-10-27 更新核查

检查日期：2026-10-27（Asia/Shanghai）。接续 [2026-10-21 核查](github-skills-updates-2026-10-21.md)，依据上游 GitHub API、固定源码及本机只读比对。本轮用户未提供 CLI 更新日志。

## 基准与结论

比较范围固定为 [`5beb35b...59db609`](https://github.com/mattpocock/skills/compare/5beb35b040e3de97e54fa9e386d7e42d15c295c2...59db609eb1e18aaf59b63284d0e52255aff0c63c)：2 个提交、10 个文件，新增 93 行、删除 19 行。HEAD `59db609eb1e18aaf59b63284d0e52255aff0c63c` 的提交时间为北京时间 2026-10-27 15:44:12。

| 提交 | 北京时间 | 内容 |
| --- | --- | --- |
| [`ed0de59`](https://github.com/mattpocock/skills/commit/ed0de59eb5de8c2730a650cc69f588ffa64c1040) | 2026-10-27 15:39:47 | setup 的远端可访问性、旧领域文件迁移及 tracker 模板修复。 |
| [`59db609`](https://github.com/mattpocock/skills/commit/59db609eb1e18aaf59b63284d0e52255aff0c63c) | 2026-10-27 15:44:12 | 贡献规则、Issue 表单与 Discussions 分流，以及避免向技能上游误投项目任务。 |

只有 `setup-matt-pocock-skills` 技能包发生变化：`SKILL.md` 和 GitHub、GitLab 两份 tracker 模板。其余变化是两份 changeset、三份 Issue 表单配置、新增 `CONTRIBUTING.md` 和领域建模说明页；`wizard`、`chief-of-staff` 等其余 37 项技能内容未变。[完整差异](https://github.com/mattpocock/skills/compare/5beb35b040e3de97e54fa9e386d7e42d15c295c2...59db609eb1e18aaf59b63284d0e52255aff0c63c)

固定目录树仍有 38 个 `SKILL.md`：Engineering 20、Productivity 7、In Progress 7、Misc 4。插件仍列出 27 项稳定技能，包含 setup；最新 GitHub Release 和插件版本仍为 [`v1.3.1`](https://github.com/mattpocock/skills/releases/tag/v1.3.1)，本次属于发布后的 `main` 更新。[固定目录树](https://github.com/mattpocock/skills/tree/59db609eb1e18aaf59b63284d0e52255aff0c63c/skills)、[插件清单](https://github.com/mattpocock/skills/blob/59db609eb1e18aaf59b63284d0e52255aff0c63c/.claude-plugin/plugin.json)

## setup 的用户可见变化

setup 仍标注 `disable-model-invocation: true`，只在用户明确调用后启动。新规则有两项：

- 发现旧的 `CONTEXT.md` / `CONTEXT-MAP.md` 时，提出用 `git mv` 迁移至 `GLOSSARY.md` / `GLOSSARY-MAP.md`，并更新 `docs/agents/domain.md` 中的名称。
- 用户选择 GitHub/GitLab tracker、但项目没有指向相应主机的 remote 时，先取得仓库 URL 并配置远端；以目录内 `gh repo view` / `glab repo view` 能读到项目为完成条件，失败时说明缺失条件并停止继续配置。

这些来自 [PR #1228](https://github.com/mattpocock/skills/pull/1228)及[固定技能源码](https://github.com/mattpocock/skills/blob/59db609eb1e18aaf59b63284d0e52255aff0c63c/skills/engineering/setup-matt-pocock-skills/SKILL.md)。领域建模说明页同时修正旧版替换造成的 FAQ 错误，明确 `CONTEXT` 名称沿用至 1.3.0 前，并指向 setup 的迁移流程；没有修改 `domain-modeling` 技能本身。[固定说明页](https://github.com/mattpocock/skills/blob/59db609eb1e18aaf59b63284d0e52255aff0c63c/docs/engineering/domain-modeling.md)

## GitHub / GitLab 模板修复

| 模板 | 变化及影响 |
| --- | --- |
| GitHub：目标仓库 | 每会话首次创建 Issue 前，用 `gh repo view --json nameWithOwner` 核对目标；fork 可能默认指向上游，目标不是用户仓库时须确认。项目任务不应误投 `mattpocock/skills`。 |
| GitHub：关闭 Issue | 改为先 `gh issue comment`、再 `gh issue close`；避免对已关闭 Issue 使用 `close --comment` 时评论被丢弃却返回成功。 |
| GitHub：读取 PR | 先普通 `gh pr view` 读取正文，再用 `--comments` 读取评论；后者在非 TTY 输出中不包含正文。 |
| GitHub：依赖关系 | 将 Blocking 从 Wayfinding 移至通用 Conventions，让拆票流程也能找到；增加 `--blocked-by` / `--add-blocked-by` 原生命令，要求 `gh` 2.94+，保留旧版 API 及不可用时的文本回退。 |
| GitHub：开放阻塞数 | 明确用 API 读取 `.issue_dependencies_summary.blocked_by`；若用 `gh issue view --json blockedBy`，必须筛选 `OPEN`，避免把已关闭阻塞项算进去。 |
| GitLab：MR 列表 | 将 `glab mr list -F json` 修正为 `-O json`。 |
| GitLab：地图子任务 | 创建时增加 `--linked-issues <map> --link-type relates_to`；frontier 从地图的 links API 枚举开放、带 `wayfinder:<type>` 标签的任务，再排除有开放阻塞项或已有负责人者。 |

依据 [GitHub 模板](https://github.com/mattpocock/skills/blob/59db609eb1e18aaf59b63284d0e52255aff0c63c/skills/engineering/setup-matt-pocock-skills/issue-tracker-github.md)、[GitLab 模板](https://github.com/mattpocock/skills/blob/59db609eb1e18aaf59b63284d0e52255aff0c63c/skills/engineering/setup-matt-pocock-skills/issue-tracker-gitlab.md)及 [PR #1228 的复现说明](https://github.com/mattpocock/skills/pull/1228)。这些是上游模板声明，本次没有创建、关闭或编辑远端 Issue 来复测。

## 向上游反馈的规则

[新增 CONTRIBUTING.md](https://github.com/mattpocock/skills/blob/59db609eb1e18aaf59b63284d0e52255aff0c63c/CONTRIBUTING.md)将反馈分流：技能 Bug、既有技能修改继续用 Issue；使用问题去 Discussions Q&A；新技能、捐赠技能、其他 harness/tracker 支持建议去 Discussions Ideas，并先检查范围限制。PR 仅面向协作者开放，其他反馈者在 Issue 中描述问题或修复建议。

AI 辅助撰写的 Issue 也要求一事一票、简短、复现步骤在前、不粘贴完整方案或重写的 `SKILL.md`。Bug 表单新增必填版本/提交号；Bug 和修改建议表单都增加单问题与重复搜索检查，并分别自动标记 `bug, needs-triage` 和 `enhancement, needs-triage`。[PR #1169](https://github.com/mattpocock/skills/pull/1169)

`blank_issues_enabled: false` 在比较基准中已经存在，本次不是首次关闭空白 Issue；变化是细分 Discussions 路由及表单约束。这些标签和反馈政策属于上游仓库，并不自动替换本项目的标签映射。[固定表单配置](https://github.com/mattpocock/skills/tree/59db609eb1e18aaf59b63284d0e52255aff0c63c/.github/ISSUE_TEMPLATE)

## 本机证据与边界

本机只读检查得到 Skills CLI `1.7.4`。`%USERPROFILE%\.agents\.skill-lock.json` 中 38 项目录哈希全部匹配固定 HEAD，更新时间为北京时间 2026-10-27 17:39:54.862 至 17:39:55.263。进一步比对 `%USERPROFILE%\.agents\skills\` 中的 103 个上游技能包文件，归一化 CRLF/LF 后 Git blob 全部一致，零缺失、零内容差异；分类目录 README 未纳入，也未审计本机额外文件。

这些证据说明核查时本机技能文件与固定源码一致。本轮用户没有提供 CLI 更新日志，不能据此还原 `skills update` 显示的更新数量或每个安装目标的成功状态；本次没有验证各 Agent 的运行时加载。

## 对现有项目的影响

更新技能文件不会重写项目中已经生成的 `docs/agents/*.md`；已有项目若需要上述 tracker 修复，须另行同步本地配置，或在用户明确启动 setup 后按项目约定刷新。上游新增目标核验也不代表本次调查获准创建或写入远端仓库。插件版本尚未变化，不能仅凭插件安装方式认定已取得这些 `main` 补丁。[PR #1228 的影响范围](https://github.com/mattpocock/skills/pull/1228)、[固定插件清单](https://github.com/mattpocock/skills/blob/59db609eb1e18aaf59b63284d0e52255aff0c63c/.claude-plugin/plugin.json)

本项目的 [Issue tracker 配置](../../docs/agents/issue-tracker.md)在核查时仍使用 `gh issue close --comment`、只通过 `--comments` 读取 PR，且未规定首次创建 Issue 前核验目标仓库；这些是核查时识别的同步点，处理结果见下文。[领域文档配置](../../docs/agents/domain.md)已采用 `GLOSSARY` 布局，无需因本次旧命名迁移提示立即改名。

本次只研究源码并保存核查报告，没有运行 setup、迁移领域文件、修改 remote、创建 Issue 或调整本项目的 `AGENTS.md`、tracker 与标签配置；没有验证各 Agent 的运行时加载。

## 后续文档同步

已将目标仓库与可访问性核验、评论后关闭、PR 正文读取及开放阻塞查询同步到[项目 tracker 文档](../../docs/agents/issue-tracker.md)，保留现有标签映射和外部 PR 分诊关闭状态；同步更新[使用指南](Matt-Pocock稳定Skills使用说明.md)、[CLI 指南](skills%20CLI与常用命令.md)、[目录约定](按照Matt-Pocock设计理念的标准工作目录结构.md)、[本机 CLI 记录](../node-js/本机Node.js与npm环境记录.md)和历史导航。

本机 `gh` 2.103.3 的帮助确认了父子、阻塞、仓库查询及 PR 读取参数，也明确了非 TTY 下 `--comments` 不输出 PR 正文；未通过远端写入复测操作。本项目未发现旧 `CONTEXT.md` / `CONTEXT-MAP.md`，本次没有执行迁移、配置 remote 或运行 setup。
