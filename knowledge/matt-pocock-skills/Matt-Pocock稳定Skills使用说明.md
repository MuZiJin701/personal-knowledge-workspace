# Matt Pocock 稳定 25 个 Skills 使用说明

> 依据 [mattpocock/skills](https://github.com/mattpocock/skills) 当前 `main` 分支整理。

Skill 是给编程 Agent 使用的工作方法说明。当前仓库的稳定 Skills 分为 `Engineering` 和 `Productivity` 两类；另有 `In Progress` Beta 技能和 `Misc` 辅助技能，不计入下面的 25 个稳定 Skills。

当前已发布版本为 1.2.3，更新重点是：跨 Agent 的子代理描述、排障过程的敏感信息脱敏，以及 `wizard` 按阶段数量显示进度。

官方 `main` 在 2026-08-13 又更新了 `domain-modeling` 的触发描述（尚未形成新的版本号）：讨论代码库术语、编写或编辑 `CONTEXT.md`，以及记录或编辑 ADR 时可触发；移除了“其他 Skill 需要维护领域模型”的限定。

2026-08-15 的 `main`（同样尚未形成新的版本号）又统一了 Skill 间的调用规则：依赖其他 Skill 时显式调用 Skill tool，每次调用一个；只有 Model-invoked Skill 可被其他 Skill 调用。User-invoked Skill 必须提示用户主动输入 `/skill`，不能由其他 Skill 代调用。依据[官方调用规则](https://github.com/mattpocock/skills/blob/main/.agents/invocation.md)及[最新提交](https://github.com/mattpocock/skills/commit/068b6e0)。

2026-08-20 的全局更新涉及 28 个已安装的 Matt Pocock skill，包括稳定 Engineering、Productivity、Beta `In Progress` 和 `Misc`。其中多数是格式维护；已确认所有更新写入 `%USERPROFILE%\.agents\skills\`，不表示稳定 Skill 总数增加。详见[近期更新核查](2026-08-20-mattpocock-skills-近期更新核查.md)。

## 在 Codex 中安装

```bash
# 全局安装，并安装到 Codex
skills add mattpocock/skills --global --agent codex

# 项目级安装
skills add mattpocock/skills --agent codex
```

安装后，在项目中运行一次：

```text
/setup-matt-pocock-skills
```

它会配置 Issue tracker、triage 标签，以及 `CONTEXT.md` 和 ADR 的位置。已有 `CLAUDE.md` 时优先编辑它；否则使用 `AGENTS.md`，两者都没有时再询问创建哪一个。

## 调用方式

- **User-invoked**：只能由用户主动输入，例如 `/to-spec`、`/implement`
- **Model-invoked**：用户可以调用，Agent 也可以根据任务自动调用，例如 `tdd`、`code-review`

Skill 间的依赖必须显式调用 Skill tool，且一次只调用一个 Skill。User-invoked Skill 不能被其他 Skill 调用；如果缺少这类初始化 Skill，应提示用户主动执行对应的 `/skill`。

## Engineering：18 个

### User-invoked：9 个

| Skill | 用途 |
|---|---|
| `/ask-matt` | 根据任务选择合适的 Skill 或流程 |
| `/grill-with-docs` | 澄清需求，同时维护术语和架构决策 |
| `/triage` | 按状态机处理 Issue 和外部 PR |
| `/improve-codebase-architecture` | 发现并筛选代码库的架构改进机会，使用跨 Agent 的子代理探索 |
| `/setup-matt-pocock-skills` | 配置项目的 Issue tracker、标签和领域文档 |
| `/to-spec` | 把已有讨论整理成 spec 并发布到 Issue tracker |
| `/to-tickets` | 把 spec 或计划拆成带阻塞关系的实施任务 |
| `/implement` | 按 spec 或任务实现代码，并接入测试和代码审查 |
| `/wayfinder` | 为跨多个会话的大型工作建立决策地图 |

### Model-invoked：9 个

| Skill | 用途 |
|---|---|
| `prototype` | 用单文件 HTML 或 UI 变体验证设计问题 |
| `diagnosing-bugs` | 按反馈循环诊断复杂 Bug 和性能回归，并先脱敏命令、输出和捕获文件 |
| `research` | 调查一手资料并生成带引用的 Markdown 研究记录 |
| `tdd` | 以垂直切片执行测试驱动开发 |
| `domain-modeling` | 讨论代码库术语，或编写、编辑 `CONTEXT.md` 与 ADR 时建立和校准领域模型 |
| `codebase-design` | 设计隐藏实现、暴露小接口的深模块，并用通用子代理并行比较方案 |
| `code-review` | 从 Standards 和 Spec 两个维度审查变更，子代理描述兼容不同 Agent |
| `resolving-merge-conflicts` | 按意图解决 Git merge 或 rebase 冲突 |
| `wizard` | 为必须由人完成的外部操作生成交互式 Bash 向导，按阶段数量显示进度 |

## Productivity：7 个

### User-invoked：5 个

| Skill | 用途 |
|---|---|
| `/grill-me` | 通过连续提问澄清计划或设计 |
| `/handoff` | 生成交接文档，让其他 Agent 继续工作 |
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
复杂功能：/grill-with-docs → /to-spec → /to-tickets → /implement → /code-review
复杂 Bug：描述问题 → diagnosing-bugs → 修复 → 回归测试 → code-review
大型工作：/wayfinder → /to-spec → /to-tickets → /implement
```

小修改可以直接实现，再运行 `code-review`。不确定该从哪里开始时，先使用 `/ask-matt`。

## 其他仓库分类

- `skills/in-progress/`：Beta 技能，可能变化或消失，不进入官方插件
- `skills/misc/`：不常用的辅助工具，不进入官方插件
- `skills/deprecated/`：已废弃技能，目前为空

完整出处：仓库 [README](https://github.com/mattpocock/skills/blob/main/README.md)、[Engineering README](https://github.com/mattpocock/skills/blob/main/skills/engineering/README.md)、[Productivity README](https://github.com/mattpocock/skills/blob/main/skills/productivity/README.md) 和[调用规则](https://github.com/mattpocock/skills/blob/main/.agents/invocation.md)。
