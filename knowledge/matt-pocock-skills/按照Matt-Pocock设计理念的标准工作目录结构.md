# Matt Pocock Skills 的标准工作目录结构

> 这是一个可调整的起点，不是必须照抄的模板。Skills 主要需要知道三件事：Issue tracker 在哪里、triage 标签如何映射、领域文档在哪里。

## 推荐的单体项目结构

```text
project/
├── AGENTS.md 或 CLAUDE.md       # Agent 的项目规则
├── GLOSSARY.md                  # 共享术语
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

`src/`、`tests/` 和目录名称不是 Skills 强制规定的内容。小项目可以先只创建 Agent 规则、`GLOSSARY.md` 和 `docs/agents/`；只有使用本地 tracker 时才创建 `.scratch/`，只有有长期架构决策时才创建 `docs/adr/`。

## 项目入口文件

`setup-matt-pocock-skills` 按以下顺序选择入口文件：

1. 有 `CLAUDE.md` 时编辑它。
2. 否则编辑已有 `AGENTS.md`。
3. 两者都没有时，由用户选择创建哪一个。

不会为了运行 Skill 同时创建两个入口文件。

## 领域文档

### `GLOSSARY.md`

根目录的 `GLOSSARY.md` 保存项目成员和 Agent 共同使用的术语、业务对象边界、容易误解的概念，以及稳定的命名约定。

默认使用单一上下文：根目录一个 `GLOSSARY.md` 和 `docs/adr/`。多包仓库或多个独立领域才使用：

```text
GLOSSARY-MAP.md
docs/adr/                         # 系统级 ADR
src/<context>/GLOSSARY.md         # 领域术语
src/<context>/docs/adr/           # 领域专属 ADR
```

`grill-with-docs`、`domain-modeling` 等 Skill 会在术语或重要决策真正确定时按需更新这些文档，不需要预建空文档。当前 `domain-modeling` 还会在讨论代码库术语、编写或编辑 `GLOSSARY.md`，以及记录或编辑 ADR 时触发。

### `docs/agents/`

这是工程 Skill 的配置目录，不是普通知识文档目录：

- `issue-tracker.md`：记录 tracker、父子任务、阻塞关系和操作命令。
- `triage-labels.md`：将技能角色映射到项目标签。
- `domain.md`：记录术语与 ADR 的位置、读取规则。

技能更新只覆盖安装目录，不会重写这些项目文件。已有项目需同步父子 Issue、外部 PR 查询和最新 JSON 读取规则；setup 确认 GitHub/GitLab 标签配置后，还需创建 tracker 缺失的实际标签。保留项目原有的 tracker、标签映射和 PR 分诊开关。详见[2026-10-08 更新核查](github-skills-updates-2026-10-08.md)。

### `docs/adr/`

ADR（Architecture Decision Record）记录重要技术选择、原因、被放弃的替代方案和影响。不要为每个小决定创建 ADR。

## Issue tracker 和本地结构

Issue tracker 可以是：

- GitHub（`gh`）、GitLab（`glab`）或本地 Markdown：上游提供模板。
- 其他 tracker：用户描述流程，写入 `docs/agents/issue-tracker.md`；上游不提供 Jira、Linear 等工具的一等模板。

使用本地 Markdown 时，当前约定是：

```text
.scratch/<feature-slug>/
├── spec.md
├── map.md                         # wayfinder 使用时
└── issues/
    ├── 01-<slug>.md
    └── 02-<slug>.md
```

每个实施任务独立成文件，不使用合并的 `tickets.md`。远程 tracker 按平台创建 Issue：从已有 spec 或 map Issue 拆出的任务建立父子归属，执行依赖另外建立原生阻塞边；已有原生边时省略正文的 `Blocked by`，不支持时才回退为文本。GitHub 操作见工作区的 [issue-tracker.md](../../docs/agents/issue-tracker.md)。

## 术语和 triage

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

Wayfinder 地图和决策任务只使用 `wayfinder:` 标签；先创建真实 Issue，再建立交叉引用和阻塞边，按类型标签执行研究、原型、澄清或任务。研究成果使用临时研究分支与上下文指针，不创建 PR；推送按项目授权执行。它们与 `to-tickets` 生成的实施任务采用不同标签约定。

## 工作流

```text
用户想法
   ↓
/grill-with-docs：需求澄清与术语统一
   ↓
/to-spec → /to-tickets（跨会话工作时）
   ↓
/implement 或 /implement-spec
   ↓
测试、反馈、code-review → 用户主动 /retro
```

`triage` 是外部 Bug 报告、功能请求等原始 Issue 的入口；`to-tickets` 已生成的实施任务直接进入实现流程。复杂 Bug 则从 `diagnosing-bugs` 进入，完成修复和回归检查后在同一会话主动运行 `/retro`；若缺少适合测试的模块边界，再由用户启动 `/improve-codebase-architecture`。参见[2026-10-05 路由修正](github-skills-updates-2026-10-05.md)。

重点是让 Agent 少猜测、少重复询问，并让每一步都有可验证的反馈，而不是预建大量目录或文档。

执行时补充两条安全约定：复杂 Bug 的命令、输出和捕获文件先脱敏，再写入 Issue 或研究记录；需要人操作的 `wizard` 按阶段数量显示进度，不依赖时间估算。

`wizard` 默认是临时产物；需要长期重复使用时可放入 `scripts/`。复制当前模板后，将所有阶段保留在 `run_wizard` 中并保留末尾调用；运行前用 `bash -n <script>` 检查语法。技能更新不改写已有脚本副本，也不自动刷新其中的 `.env` 读取、清屏和变量同步逻辑。[2026-10-10 模板核查](github-skills-updates-2026-10-10.md)

本轮反馈检查还要求：传入 ticket 引用时先获取内容并复述标题；选定测试边界前说明能检查与遗漏什么；若通过修改代码或数据制造失败测试，先比较原始副本确认修改已生效。审查先搜索所有编码规范，再前台并行执行 Standards 与 Spec 两路审查。

流程中的 `/skill` 表示用户主动调用。Skill 内部依赖应显式调用 Skill tool，且一次只调用一个 Model-invoked Skill；User-invoked Skill 只能提示用户执行，不能由其他 Skill 代调用。

`handoff` 是临时交接材料：Windows 使用 `%TEMP%`，其他系统使用 `$TMPDIR` 或 `/tmp`，需要长期保留时再复制到明确的位置。实验技能 `claude-handoff` 先写临时摘要文件，再传给后台 Claude，避免 Shell 解释摘要里的特殊字符。

## 官方来源

- [固定版本 setup Skill](https://github.com/mattpocock/skills/blob/f3fc5632f401156837ee3872f14fe33ccf1024ea/skills/engineering/setup-matt-pocock-skills/SKILL.md)
- [GitHub tracker 模板](https://github.com/mattpocock/skills/blob/f3fc5632f401156837ee3872f14fe33ccf1024ea/skills/engineering/setup-matt-pocock-skills/issue-tracker-github.md)
- [贡献与维护范围](https://github.com/mattpocock/skills/blob/6fd947921b935b7e1e69293a200400f0fdd5c15f/SCOPE.md)
