# Triage Labels

Matt Pocock 技能使用两个类别角色和五个状态角色；本工作区的 GitHub Issues 直接使用相同字符串。技能提到角色时，按下面的映射选择标签。

## 类别标签

| 技能角色 | GitHub 标签 | 含义 |
| --- | --- | --- |
| `bug` | `bug` | 已有行为损坏 |
| `enhancement` | `enhancement` | 新功能或改进 |

## 状态标签

| 技能角色 | GitHub 标签 | 含义 |
| --- | --- | --- |
| `needs-triage` | `needs-triage` | 等待评估 |
| `needs-info` | `needs-info` | 等待补充信息 |
| `ready-for-agent` | `ready-for-agent` | 已明确，可交给 agent |
| `ready-for-human` | `ready-for-human` | 需要人工实现、判断或外部操作 |
| `wontfix` | `wontfix` | 不处理 |

经过分诊的 Issue 使用一个类别标签和一个状态标签。状态标签发生冲突时，先指出冲突并确认如何处理；状态迁移替换旧状态，保留类别与其他用途的标签。从已有 spec 拆出的实施任务默认进入 `ready-for-agent`。

Wayfinding 另外使用 `wayfinder:map` 和 `wayfinder:<type>`（`research`、`prototype`、`grilling`、`task`）标记地图和任务类型；它们不替代上述类别和状态。

如果未来改用其他 issue tracker，修改右侧标签映射和 `docs/agents/issue-tracker.md`。本文件记录角色映射；GitHub 仓库中标签的创建、Issue 状态变更和自动分诊流程按具体任务授权执行。
