# Matt Pocock Skills 的标准工作目录结构

> 这是一个可调整的起点，不是必须照抄的模板。Skills 主要需要知道三件事：Issue tracker 在哪里、triage 标签如何映射、领域文档在哪里。

## 推荐的单体项目结构

```text
project/
├── AGENTS.md 或 CLAUDE.md       # Agent 的项目规则
├── CONTEXT.md                   # 共享术语和领域背景
├── docs/
│   ├── agents/
│   │   ├── issue-tracker.md     # Issue tracker 使用说明
│   │   ├── triage-labels.md     # triage 角色到实际标签的映射
│   │   └── domain.md            # 领域文档读取规则
│   └── adr/                     # 架构决策记录
├── .scratch/                    # 仅本地 Markdown tracker 使用
├── src/
└── tests/
```

`src/`、`tests/` 和目录名称不是 Skills 强制规定的内容。小项目可以先只创建 Agent 规则、`CONTEXT.md` 和 `docs/agents/`；只有使用本地 tracker 时才创建 `.scratch/`，只有有长期架构决策时才创建 `docs/adr/`。

## 项目入口文件

`setup-matt-pocock-skills` 按以下顺序选择入口文件：

1. 已有 `CLAUDE.md`：编辑它
2. 没有 `CLAUDE.md`、但有 `AGENTS.md`：编辑它
3. 两者都没有：询问创建哪一个

不会为了运行 Skill 同时创建两个入口文件。

## 领域文档

### `CONTEXT.md`

+根目录的 `CONTEXT.md` 保存项目成员和 Agent 共同使用的术语、业务对象边界、容易误解的概念，以及稳定的命名约定。

默认使用单一上下文：根目录一个 `CONTEXT.md` 和 `docs/adr/`。多包仓库或多个独立领域才使用：

```text
CONTEXT-MAP.md
docs/adr/                         # 系统级 ADR
src/<context>/CONTEXT.md          # 领域上下文
src/<context>/docs/adr/           # 领域专属 ADR
```

`grill-with-docs`、`domain-modeling` 等 Skill 会在术语或重要决策真正确定时按需更新这些文档，不需要预建空文档。当前 `domain-modeling` 还会在讨论代码库术语、编写或编辑 `CONTEXT.md`，以及记录或编辑 ADR 时触发。

### `docs/agents/`

这是工程 Skill 的配置目录，不是普通知识文档目录：

- `issue-tracker.md`：Issue 存在哪个系统，以及如何读写
- `triage-labels.md`：标准 triage 角色到项目实际标签的映射；仅在安装 `triage` 时需要
- `domain.md`：Agent 在哪里读取 `CONTEXT.md` 和 ADR，以及多上下文项目的规则

### `docs/adr/`

ADR（Architecture Decision Record）记录重要技术选择、原因、被放弃的替代方案和影响。不要为每个小决定创建 ADR。

## Issue tracker 和本地结构

Issue tracker 可以是：

- GitHub Issues
- GitLab Issues
- Linear 或其他项目明确约定的系统
- 本地 Markdown 文件

使用本地 Markdown 时，当前约定是：

```text
.scratch/<feature-slug>/
├── spec.md
├── map.md                         # wayfinder 使用时
└── issues/
    ├── 01-<slug>.md
    └── 02-<slug>.md
```

每个实施任务独立成文件，不使用合并的 `tickets.md`。远程 tracker 则按平台创建 Issue，并使用平台支持的阻塞关系或子 Issue 关系。

## 术语和 triage

- **Issue tracker**：存放和跟踪 Issue 的工具
- **Issue**：tracker 中的一项具体工作，可以是 Bug、功能、spec 或实施切片
- **Decision ticket**：`wayfinder` 中表示待解决决策问题的子 Issue
- **Triage**：按类别和状态处理 Issue 或外部 PR

当前 triage 有两个类别角色和五个状态角色：

| 类别角色          | 含义     |
| ------------- | ------ |
| `bug`         | 某些东西损坏 |
| `enhancement` | 新功能或改进 |

| 状态角色              | 含义             |
| ----------------- | -------------- |
| `needs-triage`    | 等待评估           |
| `needs-info`      | 等待补充信息         |
| `ready-for-agent` | 信息完整，可交给 Agent |
| `ready-for-human` | 需要人实现或判断       |
| `wontfix`         | 确定不处理          |

Skills 使用标准角色名；项目可以在 `docs/agents/triage-labels.md` 中把它们映射到实际标签。官方领域术语优先使用 `Issue`，但 `wayfinder` 保留 `Decision ticket` 这一特定术语。

## 工作流

```text
用户想法
   ↓
需求澄清与术语统一
   ↓
spec 或 Issue
   ↓
Triage
   ↓
Agent 或人实现
   ↓
测试、反馈、代码审查
```

重点是让 Agent 少猜测、少重复询问，并让每一步都有可验证的反馈，而不是预建大量目录或文档。

执行时补充两条安全约定：复杂 Bug 的命令、输出和捕获文件先脱敏，再写入 Issue 或研究记录；需要人操作的 `wizard` 按阶段数量显示进度，不依赖时间估算。

流程中的 `/skill` 表示用户主动调用。Skill 内部依赖应显式调用 Skill tool，且一次只调用一个 Model-invoked Skill；User-invoked Skill 只能提示用户执行，不能由其他 Skill 代调用。

## 官方来源

- [仓库 README](https://github.com/mattpocock/skills/blob/main/README.md)
- [CONTEXT.md](https://github.com/mattpocock/skills/blob/main/CONTEXT.md)
- [Engineering Skills README](https://github.com/mattpocock/skills/blob/main/skills/engineering/README.md)
- [`setup-matt-pocock-skills`](https://github.com/mattpocock/skills/blob/main/skills/engineering/setup-matt-pocock-skills/SKILL.md)
- [本地 Issue tracker 模板](https://github.com/mattpocock/skills/blob/main/skills/engineering/setup-matt-pocock-skills/issue-tracker-local.md)
- [调用规则](https://github.com/mattpocock/skills/blob/main/.agents/invocation.md)
