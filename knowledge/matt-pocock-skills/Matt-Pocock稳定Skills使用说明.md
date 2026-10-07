# Matt Pocock 27 个 Skills 使用说明

> 2026-10-07 复核，依据 [mattpocock/skills 固定 HEAD `6fd9479`](https://github.com/mattpocock/skills/tree/6fd947921b935b7e1e69293a200400f0fdd5c15f) 整理；最新发布版仍为 [v1.3.1](https://github.com/mattpocock/skills/releases/tag/v1.3.1)。

Skill 是给编程 Agent 使用的工作方法说明。当前上游主推清单共 27 个：`Engineering` 20 个、`Productivity` 7 个；另有 7 个 `In Progress` 和 4 个 `Misc`，因此仓库合计 38 个。这里的“27 个”仅指主推清单，不代表其余分类无法安装。

最新发布版本为 `v1.3.1`；其后 `main` 修复了 `implement` 的 Skill 调用、GitHub tracker 的外部 PR 列表和父子 Issue 操作、`to-tickets` 的关系处理，以及两种 handoff 的目录或 Shell 传参问题，并新增实验技能 `chief-of-staff`。本机已安装 38 项，103 个上游文件与固定 HEAD 一致。`skills update` 只刷新已安装项，不会加入新增技能或清理上游删除项。详见[2026-10-07 更新核查](github-skills-updates-2026-10-07.md)。

当前 `domain-modeling` 在讨论代码库术语、编写或编辑 `GLOSSARY.md`，以及记录或编辑 ADR 时可触发；不再使用旧的 `CONTEXT.md` 命名。

2026-08-15 的 `main`（同样尚未形成新的版本号）又统一了 Skill 间的调用规则：依赖其他 Skill 时显式调用 Skill tool，每次调用一个；只有 Model-invoked Skill 可被其他 Skill 调用。User-invoked Skill 必须提示用户主动输入 `/skill`，不能由其他 Skill 代调用。依据[官方调用规则](https://github.com/mattpocock/skills/blob/main/.agents/invocation.md)及[最新提交](https://github.com/mattpocock/skills/commit/068b6e0)。

2026-08-20 的全局更新涉及 28 个已安装的 Matt Pocock skill，包括稳定 Engineering、Productivity、Beta `In Progress` 和 `Misc`。其中多数是格式维护；已确认所有更新写入 `%USERPROFILE%\.agents\skills\`，不表示稳定 Skill 总数增加。详见[近期更新核查](2026-08-20-mattpocock-skills-近期更新核查.md)。

2026-09-30 的上游变更、本机删除已移除技能的操作以及 wx skill 的 resolver 更新见[两仓库更新核查](github-skills-updates-2026-09-30.md)。

2026-10-05 的 `ask-matt` 修正与 `v1.3.1` 发布见[当日更新核查](github-skills-updates-2026-10-05.md)；后续五项修复、新实验技能和维护范围变化见[2026-10-07 更新核查](github-skills-updates-2026-10-07.md)。

## 在 Codex 中安装

```bash
# 全局安装仓库的全部技能到所有支持的 Agent
skills add mattpocock/skills --global --all

# 如只需 Codex，可显式指定 Agent 并选择全部技能
skills add mattpocock/skills --global --agent codex --skill '*'

# 项目级安装到 Codex
skills add mattpocock/skills --agent codex
```

`--all` 显式选择全部技能和全部 Agent。单用 `add -g -y` 默认选择检测到的 Agent 和通用目录目标；日志中的“79 agents”不是成功安装数量。2026-10-07 的用户日志中，38 次失败均为 PromptScript 不支持全局安装；本机 38 项安装记录和技能文件已核对。完整解释见[CLI 说明](skills%20CLI与常用命令.md)。上游目录若删除技能，需另外运行 `skills remove -g <skill-name> -y`。

安装后，在项目中运行一次：

```text
/setup-matt-pocock-skills
```

它会配置 Issue tracker、triage 标签，以及 `GLOSSARY.md` 和 ADR 的位置。已有 `CLAUDE.md` 时优先编辑它；否则使用 `AGENTS.md`，两者都没有时再询问创建哪一个。

安装或更新技能不会刷新项目中已生成的 `docs/agents/*.md`。本次 GitHub 模板新增父子 Issue 命令和外部 PR 的 REST 查询；已有项目需同步这些规则，或由用户重新运行 setup。工作区当前配置见 [issue-tracker.md](../../docs/agents/issue-tracker.md)。

## 调用方式

- **User-invoked**：只能由用户主动输入，例如 `/to-spec`、`/implement`
- **Model-invoked**：用户可以调用，Agent 也可以根据任务自动调用，例如 `tdd`、`code-review`

Skill 间的依赖必须显式调用 Skill tool，且一次只调用一个 Skill。User-invoked Skill 不能被其他 Skill 调用；如果缺少这类初始化 Skill，应提示用户主动执行对应的 `/skill`。

## Engineering：20 个

### User-invoked：11 个

| Skill | 用途 |
|---|---|
| `/ask-matt` | 根据任务选择 Skill 或流程；Bug 修复后指向同一会话中的 `/retro` |
| `/grill-with-docs` | 澄清需求，同时维护术语和架构决策 |
| `/triage` | 按状态机处理 Issue 和外部 PR |
| `/improve-codebase-architecture` | 发现并筛选代码库的架构改进机会，使用跨 Agent 的子代理探索 |
| `/setup-matt-pocock-skills` | 配置项目的 Issue tracker、标签和领域文档 |
| `/to-spec` | 把已有讨论整理成 spec 并发布到 Issue tracker |
| `/to-tickets` | 把 spec 或计划拆成带阻塞关系的任务；源为已有 Issue 时挂为其子 Issue，原生阻塞边不重复写入正文 |
| `/implement` | 按 spec 或任务实现代码，并显式调用 `tdd` 和 `code-review` 的 Skill 工具 |
| `/implement-spec` | 在一个集成分支上按任务图实现完整 spec，并行处理已解除阻塞的任务 |
| `/wayfinder` | 为跨多个会话的大型工作建立决策地图 |
| `/retro` | 回顾一次工程会话，按严重程度建议改善 Agent 环境 |

### Model-invoked：9 个

| Skill | 用途 |
|---|---|
| `prototype` | 用单文件 HTML 或 UI 变体验证设计问题 |
| `diagnosing-bugs` | 按反馈循环诊断复杂 Bug 和性能回归，并先脱敏命令、输出和捕获文件 |
| `research` | 调查一手资料并生成带引用的 Markdown 研究记录 |
| `tdd` | 以垂直切片执行测试驱动开发 |
| `domain-modeling` | 讨论代码库术语，或编写、编辑 `GLOSSARY.md` 与 ADR 时建立和校准领域模型 |
| `codebase-design` | 设计隐藏实现、暴露小接口的深模块，并用通用子代理并行比较方案 |
| `code-review` | 从 Standards 和 Spec 两个维度审查变更，子代理描述兼容不同 Agent |
| `pr` | 为 pull request 撰写说明，包含变更摘要、前后证据和合并风险判断 |
| `wizard` | 为必须由人完成的外部操作生成交互式 Bash 向导，按阶段数量显示进度 |

## Productivity：7 个

### User-invoked：5 个

| Skill | 用途 |
|---|---|
| `/grill-me` | 通过连续提问澄清计划或设计 |
| `/handoff` | 生成交接文档，Windows 放 `%TEMP%`，其他系统放 `$TMPDIR` 或 `/tmp` |
| `/teach` | 跨多个会话教授技能或概念 |
| `/to-questionnaire` | 把无法独自回答的决策整理成问卷 |
| `/wait-what` | 重新解释没有被理解的上一条消息 |

### Model-invoked：2 个

| Skill | 用途 |
|---|---|
| `grilling` | 可复用的分轮提问和决策澄清方法 |
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
