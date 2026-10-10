# Matt Pocock Skills 2026-10-05 更新核查

检查日期：2026-10-05（Asia/Shanghai）。接续 [2026-09-30 核查](github-skills-updates-2026-09-30.md)，比较当时已进入 `main` 的代码与本次检查到的上游 HEAD。

本文保留当日快照；后续五项修复及 38 项技能清单见 [2026-10-07 更新核查](github-skills-updates-2026-10-07.md)，十项修复及项目配置同步见 [2026-10-08 更新核查](github-skills-updates-2026-10-08.md)，最新向导模板与安装路线变化见 [2026-10-10 更新核查](github-skills-updates-2026-10-10.md)。

## 结论

本次技能目录内只有 `ask-matt/SKILL.md` 发生变化：修复 bug 后的复盘入口改为 `/retro`，并删除“`diagnosing-bugs` 会从 post-mortem 自动交接给 `improve-codebase-architecture`”的过时描述。`diagnosing-bugs` 的说明页同步修正，其 Skill 实现没有再次变化。上游已经发布 `v1.3.1`，37 个技能的数量和分类均未改变。[修正提交](https://github.com/mattpocock/skills/commit/c5b98691982c4f0d3a5e40ab09566b3b84721e00) [发布页](https://github.com/mattpocock/skills/releases/tag/v1.3.1)

## 核查基准与时间

| 项目 | Git 证据 | 时间（Asia/Shanghai） |
| --- | --- | --- |
| 上次核查所对应的 `main` HEAD | `d81f3a183412e71a5b1e84ca21bc1a35eea03a60`，合并 PR #1120 | 2026-09-29 20:37:40 |
| 本次修正进入 `main` | `3bf8fb8dd04f868d823e7291469b0b8efb989de9`，合并 PR #1121 | 2026-10-04 20:43:17 |
| 1.3.0 发布变更进入 `main` | `d1caf1e952fe395014ae729445d43ea7c1b40fa0`，合并 PR #849 | 2026-10-04 20:43:50 |
| 当前 HEAD / `v1.3.1` 所指提交 | `24fe0ef7737efae15c87225755e9f6f5965e4888`，合并 PR #1160 | 2026-10-04 20:48:05 |

上述时间取 Git 提交的 committer 时间并换算为北京时间。比较范围固定为 [`d81f3a1...24fe0ef`](https://github.com/mattpocock/skills/compare/d81f3a183412e71a5b1e84ca21bc1a35eea03a60...24fe0ef7737efae15c87225755e9f6f5965e4888)，以免后续 `main` 推进改变此次结果。

修正提交 `c5b9869` 的 author 时间为 `2026-09-24T16:07:54+02:00`，committer 时间为 `2026-09-29T13:37:43+01:00`；它直到 10 月 4 日的 PR 合并才进入 `main`。因此不能按 author 时间认定本次更新在 9 月 30 日已生效。[修正提交](https://github.com/mattpocock/skills/commit/c5b98691982c4f0d3a5e40ab09566b3b84721e00) [合并提交](https://github.com/mattpocock/skills/commit/3bf8fb8dd04f868d823e7291469b0b8efb989de9)

## 技能内容和文档实际改了什么

`skills/engineering/ask-matt/SKILL.md` 第 48 行的一个段落修改如下：

- 仍将难以诊断的故障指向 `/diagnosing-bugs`，保留反馈循环和回归检查的描述。
- 修复完成后，在同一会话运行 `/retro`，讨论什么措施能够预防这类 bug。
- 若发现缺乏合适的测试接口，再由用户启动 `/improve-codebase-architecture`。

这是路由指引修正，未更改 `ask-matt` 的调用策略元数据，也未修改 `diagnosing-bugs` 的诊断阶段。[Skill 差异](https://github.com/mattpocock/skills/commit/c5b98691982c4f0d3a5e40ab09566b3b84721e00)

`docs/engineering/diagnosing-bugs.md` 同步修改三处：增加修复后运行 `retro` 的入口；说明架构改进由用户启动；遇到缺乏合适测试接口时记录这一发现，撤销旧的自动交接说明。文档明确说明 `retro` 由用户调用，`diagnosing-bugs` 不会自行调用它；实际运行时是否加载某技能仍应以当前 Agent 的工具和已安装技能元数据为准。[固定版本说明页](https://github.com/mattpocock/skills/blob/24fe0ef7737efae15c87225755e9f6f5965e4888/docs/engineering/diagnosing-bugs.md)

## 仓库整体变化范围

基准到 HEAD 的净差异为 **18 个文件，67 行增加、93 行删除**：

| 范围 | 变化 |
| --- | --- |
| 技能 | 仅 `skills/engineering/ask-matt/SKILL.md` 修改，1 行增加、1 行删除 |
| 使用文档 | `docs/engineering/diagnosing-bugs.md` 修改，4 行增加、3 行删除 |
| 发布记录 | `CHANGELOG.md` 增加 1.3.0 和 1.3.1 条目，共 60 行 |
| 版本元数据 | `package.json` 和 `.claude-plugin/plugin.json` 的版本从 1.2.3 升至 1.3.1 |
| 发布材料 | 删除基准中存在的 13 份已消费 `.changeset/*.md` 文件；修正提交新增的 changeset 也在 1.3.1 发布时消费，不出现在净差异中 |

1.3.0 的发布记录包含 `implement-spec`、`pr`、`retro` 晋升，删除 `resolving-merge-conflicts`，以及 `GLOSSARY.md` 命名等此前已进入代码的变更。发布日志此时补齐，不代表这些技能本次全部重新发生内容变化。[1.3.0 发布准备提交](https://github.com/mattpocock/skills/commit/984a2c023c9fb42bb6ea40c70a652284a109dc05) [1.3.1 发布准备提交](https://github.com/mattpocock/skills/commit/2629ffc731f62c17543d73d4013585a7e2ba1469) [更新日志](https://github.com/mattpocock/skills/blob/24fe0ef7737efae15c87225755e9f6f5965e4888/CHANGELOG.md)

`v1.3.1` 是已经发布的 GitHub Release，不仅是工作区内的版本字符串；发布页在检查时标为 Latest，并关联 `24fe0ef`。[发布页](https://github.com/mattpocock/skills/releases/tag/v1.3.1)

## 本机状态与技能数量

只读检查 `%USERPROFILE%\.agents\.skill-lock.json`：来自 `mattpocock/skills` 的安装记录为 **37 项**；`ask-matt.updatedAt` 为 `2026-10-05T05:00:37.074Z`，即北京时间 13:00:37。该字段是本机安装记录时间，不是上游发布日期。

本机 `%USERPROFILE%\.agents\skills\ask-matt\SKILL.md` 与上游 HEAD 中对应文件的 Git blob 均为 `8d38b2b2c961c0e2b131bdc8f3596e22a4744e6c`，内容一致。本轮研究仅作只读比对，没有执行安装或更新命令。

进一步逐文件比对本机 37 个技能目录与上游 HEAD：共检查 101 个上游文件，没有缺失或内容差异；文本比较时将 CRLF 与 LF 归一化。此项确认了上游文件已落在全局技能目录中，不验证各 Agent 的运行时加载结果，也不审计本机额外生成的文件。

上游所有 `SKILL.md` 路径统计仍为：

| 分类 | 数量 |
| --- | ---: |
| Engineering | 20 |
| Productivity | 7 |
| In Progress | 6 |
| Misc | 4 |
| 全部 | 37 |

正式主推清单仍为 Engineering 和 Productivity 共 27 项，另外 10 项仍在仓库中。分类、技能路径及元数据在本次基准比较中未发生增删或移动。[固定版本 skills 目录](https://github.com/mattpocock/skills/tree/24fe0ef7737efae15c87225755e9f6f5965e4888/skills)

## 用户 CLI 输出如何解释

用户提供的日志记录了两次操作：`skills update -g -y` 发现并成功更新 1 个技能 `ask-matt`；随后 `skills add mattpocock/skills -g -y` 发现并重装 37 个技能。这与 Git 差异仅涉及一个技能目录相符；重装 37 项并不表示 37 项的内容都变了。

第二次操作的 `Failed to install 37` 是 37 个技能各自在 **PromptScript** 目标失败一次，原因全部为它不支持全局技能安装。其他目标仍有成功记录；本机 `skills list -g --json` 显示来自 Matt 仓库的 37 项，每项的 `agents` 字段有 60 个名称，包括 Codex 和 Claude Code，PromptScript 未列入。

日志中的 `79 agents` 是 CLI 支持的 Agent 定义总数。未指定 `--agent` 时，普通 PowerShell 中的 `-y` 会选择检测到的 Agent 并补入通用目录目标；只有显式 `--agent '*'` 或 `--all` 才选择全部定义。Agent 内运行时，CLI 还可能按当前 harness 自动选择目标。此日志没有 Eve 的失败记录，不能套用 9 月 30 日那次显式全 Agent 安装的失败列表。[CLI v1.7.0 安装逻辑](https://github.com/vercel-labs/skills/blob/v1.7.0/src/add.ts)

要显式选择来源仓库全部技能及全部 Agent，使用：

```powershell
skills add mattpocock/skills --global --all
```

不支持全局安装的目标仍会失败；该命令也不会清理上游已删除的本机技能。技能内容已与本次上游 HEAD 一致，本次核查没有重复安装。

## 说明与限制

本报告依据公开 Git 仓库的固定提交、GitHub 官方提交和发布页面，以及用户提供的 CLI 输出、本机锁文件、安装清单和上游文件内容比对。锁文件数量不能单独证明所有 Agent 都已建立入口；GitHub 发布版本也不等于本机 Skills CLI 版本 `1.7.0`。本次没有检查其他技能来源的新变更，也没有修改本工作区的 Agent 规则配置。
