# Matt Pocock 27 个 Skills 使用说明

> 2026-10-10 复核，依据 [mattpocock/skills 固定 HEAD `49dd158`](https://github.com/mattpocock/skills/tree/49dd158d1076134a641b33efb035946536778336) 整理；最新发布版仍为 [v1.3.1](https://github.com/mattpocock/skills/releases/tag/v1.3.1)。

Skill 是给编程 Agent 使用的工作方法说明。当前上游主推清单共 27 个：`Engineering` 20 个、`Productivity` 7 个；另有 7 个 `In Progress` 和 4 个 `Misc`，因此仓库合计 38 个。这里的“27 个”仅指主推清单，不代表其余分类无法安装。

最新发布版本为 `v1.3.1`；其后 `main` 的本轮更新修复 `wizard` 模板，并改写多 Agent 的安装说明。本机仍安装 38 项，103 个上游文件与固定 HEAD 一致，Skills CLI 为 `1.7.2`。`skills update` 只刷新已安装项，不会加入新增技能或清理上游删除项。详见[2026-10-10 更新核查](github-skills-updates-2026-10-10.md)。

当前 `domain-modeling` 在讨论代码库术语、编写或编辑 `GLOSSARY.md`，以及记录或编辑 ADR 时可触发；不再使用旧的 `CONTEXT.md` 命名。

2026-08-15 的 `main`（同样尚未形成新的版本号）又统一了 Skill 间的调用规则：依赖其他 Skill 时显式调用 Skill tool，每次调用一个；只有 Model-invoked Skill 可被其他 Skill 调用。User-invoked Skill 必须提示用户主动输入 `/skill`，不能由其他 Skill 代调用。依据[官方调用规则](https://github.com/mattpocock/skills/blob/main/.agents/invocation.md)及[最新提交](https://github.com/mattpocock/skills/commit/068b6e0)。

2026-08-20 的全局更新涉及 28 个已安装的 Matt Pocock skill，包括稳定 Engineering、Productivity、Beta `In Progress` 和 `Misc`。其中多数是格式维护；已确认所有更新写入 `%USERPROFILE%\.agents\skills\`，不表示稳定 Skill 总数增加。详见[近期更新核查](2026-08-20-mattpocock-skills-近期更新核查.md)。

2026-09-30 的上游变更、本机删除已移除技能的操作以及 wx skill 的 resolver 更新见[两仓库更新核查](github-skills-updates-2026-09-30.md)。

2026-10-05 的 `ask-matt` 修正与 `v1.3.1` 发布见[当日更新核查](github-skills-updates-2026-10-05.md)；后续五项修复、新实验技能和维护范围变化见[2026-10-07 更新核查](github-skills-updates-2026-10-07.md)。

2026-10-08 的十项修复及其对项目配置的影响见[当日更新核查](github-skills-updates-2026-10-08.md)；后续向导模板和安装路线变化见[2026-10-10 更新核查](github-skills-updates-2026-10-10.md)。

## 安装方式

上游现在优先介绍托管插件；Skills CLI 继续用于安装可编辑文件。每个 Agent 选择其中一种，避免同一技能重复加载。插件只包含主推的 27 项，Skills CLI 可选择仓库全部 38 项。

### Codex 托管插件

上游给出的安装命令为：

```powershell
codex plugin marketplace add mattpocock/skills
codex plugin add mattpocock-skills@mattpocock
```

上游将该路线描述为启动时自动更新，但仓库自有 marketplace 按 `plugin.json` 的版本变化更新，不能认为每个 `main` 修复都已分发。当前插件仍为 `1.3.1`，不据此认定已包含本次 `wizard` 修复。[固定安装说明](https://github.com/mattpocock/skills/blob/49dd158d1076134a641b33efb035946536778336/.agents/install-block.md)

本机 `codex plugin marketplace add --help` 和 `codex plugin add --help` 已确认上述参数形式；[OpenAI 官方插件文档](https://developers.openai.com/plugins/build/plugins#add-a-marketplace-from-the-cli)也说明了 marketplace 添加方式。本次未实际安装插件或验证自动更新。

### Skills CLI：可编辑文件与手动更新

```bash
# 全局安装仓库的全部技能到所有支持的 Agent
skills add mattpocock/skills --global --all

# 如只需 Codex，可显式指定 Agent 并选择全部技能
skills add mattpocock/skills --global --agent codex --skill '*'

# 项目级安装到 Codex
skills add mattpocock/skills --agent codex
```

`--all` 显式选择全部技能和全部 Agent。单用 `add -g -y` 默认选择检测到的 Agent 和通用目录目标；日志中的“79 agents”不是成功安装数量。2026-10-07 的用户日志中，38 次失败均为 PromptScript 不支持全局安装；本机 38 项安装记录和技能文件已核对。完整解释见[CLI 说明](skills%20CLI与常用命令.md)。上游目录若删除技能，需另外运行 `skills remove -g <skill-name> -y`。

### 其他 Agent 的上游安装路线

| Agent | 路线及更新条件 |
| --- | --- |
| Claude Code | 官方 marketplace：`claude plugin install mattpocock-skills@claude-plugins-official`；默认自动更新，仍受官方固定提交更新影响 |
| Copilot CLI | 仓库自有 `@mattpocock` marketplace；需在设置中一次性启用 `autoUpdate: true` |
| VS Code | 执行 **Chat: Install Plugin From Source** 并输入仓库 URL；上游说明为每日更新 |
| Gemini CLI | 分别安装 `skills/engineering` 和 `skills/productivity`，重跑安装命令更新 |
| 其他 Agent 或需要编辑技能 | Skills CLI，可用 `-a <agent>` 选目标，手动 `update`，新增技能重新 `add` |

完整命令和 Copilot 设置见[上游 README](https://github.com/mattpocock/skills/blob/49dd158d1076134a641b33efb035946536778336/README.md)。上述安装和自动更新行为未在本机逐项验证。

### 项目初始化

安装后，在项目中运行一次：

```text
/setup-matt-pocock-skills
```

它会配置 Issue tracker、triage 标签，以及 `GLOSSARY.md` 和 ADR 的位置；在 GitHub/GitLab 标签配置确认后，还会创建 tracker 缺失的实际标签。已有 `CLAUDE.md` 时优先编辑它；否则使用 `AGENTS.md`，两者都没有时再询问创建哪一个。

安装或更新技能不会刷新项目中已生成的 `docs/agents/*.md`。10 月 8 日的 GitHub JSON 读取和缺失标签创建规则已同步到工作区；本轮模板和安装说明变化不影响 tracker、标签或领域配置。工作区当前配置见 [issue-tracker.md](../../docs/agents/issue-tracker.md)。

## 调用方式

- **User-invoked**：只能由用户主动输入，例如 `/to-spec`、`/implement`
- **Model-invoked**：用户可以调用，Agent 也可以根据任务自动调用，例如 `tdd`、`code-review`

Skill 间的依赖必须显式调用 Skill tool，且一次只调用一个 Skill。User-invoked Skill 不能被其他 Skill 调用；如果缺少这类初始化 Skill，应提示用户主动执行对应的 `/skill`。

## Engineering：20 个

### User-invoked：11 个

| Skill | 用途 |
|---|---|
| `/ask-matt` | 先读取目标 Skill 的实际定义再推荐流程；Bug 修复后指向同一会话中的 `/retro` |
| `/grill-with-docs` | 澄清需求，同时维护术语和架构决策 |
| `/triage` | 按状态机处理 Issue 和外部 PR |
| `/improve-codebase-architecture` | 发现并筛选代码库的架构改进机会，使用跨 Agent 的子代理探索 |
| `/setup-matt-pocock-skills` | 配置 tracker、标签和领域文档，并创建 GitHub/GitLab 缺失的已配置标签 |
| `/to-spec` | 把已有讨论整理成 spec 并发布到 Issue tracker |
| `/to-tickets` | 把 spec 或计划拆成带阻塞关系的任务；源为已有 Issue 时挂为其子 Issue，原生阻塞边不重复写入正文 |
| `/implement` | 先获取 ticket 并复述标题，再实现代码；显式调用 `tdd` 和 `code-review` |
| `/implement-spec` | 在一个集成分支上按任务图实现完整 spec，并行处理已解除阻塞的任务 |
| `/wayfinder` | 建立决策地图，仅用 `wayfinder:` 标签和真实 Issue 引用；研究分支不创建 PR |
| `/retro` | 回顾工程会话，始终读取 `writing-for-agents` 作为评价标准，再按严重程度建议改善环境 |

### Model-invoked：9 个

| Skill | 用途 |
|---|---|
| `prototype` | 用单文件 HTML 或 UI 变体验证设计问题 |
| `diagnosing-bugs` | 脱敏后按反馈循环诊断故障；用变更制造测试失败时先验证变更确实生效 |
| `research` | 调查一手资料并生成带引用的 Markdown 研究记录 |
| `tdd` | 以垂直切片执行 TDD；确认测试边界前说明各边界能检查与遗漏的行为 |
| `domain-modeling` | 讨论代码库术语，或编写、编辑 `GLOSSARY.md` 与 ADR 时建立和校准领域模型 |
| `codebase-design` | 设计隐藏实现、暴露小接口的深模块，并用通用子代理并行比较方案 |
| `code-review` | 搜索仓库内编码规范，前台并行执行 Standards/Spec 审查并收集报告；读取已配置 tracker |
| `pr` | 为 pull request 撰写说明，包含变更摘要、前后证据和合并风险判断 |
| `wizard` | 生成人工操作的 Bash 向导；模板支持 `.env` 安全读写、阶段预解析、清屏回退及 shell 变量同步 |

编写向导时复制当前 `template.sh`，将阶段保留在 `run_wizard` 中，并保留末尾调用。普通双引号 `.env` 值可去除外层引号后保留；内部含反斜杠、`$` 或双引号时重新输入。`write_env` 同步当前 shell 变量，但不会自动 `export`。已生成的脚本是独立副本，不随技能更新自动修复；Windows Git Bash 的浏览器打开问题仍未在本轮处理。[模板修复与边界](github-skills-updates-2026-10-10.md)

## Productivity：7 个

### User-invoked：5 个

| Skill | 用途 |
|---|---|
| `/grill-me` | 通过连续提问澄清计划或设计 |
| `/handoff` | 生成交接文档，Windows 放 `%TEMP%`，其他系统放 `$TMPDIR` 或 `/tmp` |
| `/teach` | 在调用时的工作目录保存教学材料；测验正确答案的位置随题目变化 |
| `/to-questionnaire` | 把无法独自回答的决策整理成问卷 |
| `/wait-what` | 重新解释没有被理解的上一条消息 |

### Model-invoked：2 个

| Skill | 用途 |
|---|---|
| `grilling` | 分轮提问和决策澄清；回答“是”即接受推荐方案 |
| `writing-for-agents` | 编写 Agent 会读取的 Skill、`AGENTS.md`、`CLAUDE.md` 和其他文档 |

## 推荐流程

```text
复杂功能：/grill-with-docs → /to-spec → /to-tickets → /implement 或 /implement-spec → /code-review
复杂 Bug：描述问题 → diagnosing-bugs → 修复与回归检查 → 同一会话主动 /retro
大型工作：/wayfinder → /to-spec → /to-tickets → /implement
```

小修改可以直接实现，再运行 `code-review`。不确定该从哪里开始时，先使用 `/ask-matt`。

Bug 修复后，`/retro` 回顾怎样预防问题；若发现缺少适合测试的模块边界，再由用户启动 `/improve-codebase-architecture`。这两个入口由用户调用，`diagnosing-bugs` 不会自动调用它们。[v1.3.1 诊断说明](https://github.com/mattpocock/skills/blob/v1.3.1/docs/engineering/diagnosing-bugs.md)

## 其他仓库分类

- `skills/in-progress/`：7 个 Beta 技能，可能变化或消失，不进入官方插件。新增 `chief-of-staff`，由用户显式启动后协调长期目标、后台子代理和定期安排；`claude-handoff` 先将摘要写入临时文件，再作为提示词传给后台 Claude，避免 Shell 解释摘要中的特殊字符。
- `skills/misc/`：4 个不常用的辅助工具，不进入官方插件；上游已明确冻结维护，问题和改进由用户维护本机副本或 fork。
- `skills/deprecated/`：已废弃技能，目前为空

上游的一等 tracker 支持为 GitHub、GitLab、本地 Markdown；其他工具由用户提供流程说明。新技能贡献、宿主专用配置和子代理递归限制等按 [SCOPE.md](https://github.com/mattpocock/skills/blob/6fd947921b935b7e1e69293a200400f0fdd5c15f/SCOPE.md) 处理。上游的 `needs-info` 14 天关闭规则是其仓库 Actions，不会自动应用到用户项目。

完整出处：仓库 [README](https://github.com/mattpocock/skills/blob/main/README.md)、[Engineering README](https://github.com/mattpocock/skills/blob/main/skills/engineering/README.md)、[Productivity README](https://github.com/mattpocock/skills/blob/main/skills/productivity/README.md)、[更新日志](https://github.com/mattpocock/skills/blob/main/CHANGELOG.md)和[调用规则](https://github.com/mattpocock/skills/blob/main/.agents/invocation.md)。
