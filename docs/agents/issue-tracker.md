# Issue tracker: GitHub

本工作区的工程任务使用 GitHub Issues，通过 `gh` CLI 操作。

## 常用操作

- 创建：`gh issue create --title "..." --body-file <body-file>`；多行正文先写入临时 UTF-8 文件。
- 查看：`gh issue view <number> --json number,title,body,labels,comments,assignees`；需要阅读展示时再用 `--comments`。
- 列出：`gh issue list --state open --json number,title,body,labels,comments,assignees`，按需要添加 `--label`、`--state`。
- 评论：`gh issue comment <number> --body-file <body-file>`。
- 添加或移除标签：`gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- 关闭：先用 `gh issue comment <number> --body-file <body-file>` 发布说明，成功后执行 `gh issue close <number>`。评论失败时不继续关闭；不依赖 `close --comment`，以免对已关闭 Issue 丢失评论。

## 目标仓库与可访问性

用户启动 setup 并选择 GitHub tracker 后，先确认项目有指向 GitHub 的 remote，且在当前目录执行 `gh repo view` 能读取预期项目。缺少 remote 时先取得用户指定的仓库 URL，按授权配置远端，保留已有 remote；读取失败时说明缺失条件，停止继续配置。

每会话首次创建 Issue 前执行 `gh repo view --json nameWithOwner` 核对目标。fork 中 `gh` 可能默认指向上游，目标不是用户指定的项目时先确认；不得把本项目的 spec 或 ticket 投到技能来源仓库 `mattpocock/skills`。确认后，后续 Issue/PR 写命令显式使用 `--repo <owner>/<repo>`，API 路径也使用同一仓库。目标核验不代替创建 Issue 或其他远端写入所需的授权。

## 标签初始化

用户启动 setup 并确定标签配置后，先用 `gh api --paginate 'repos/{owner}/{repo}/labels' --jq '.[].name'` 获取仓库已有标签；与 [triage-labels.md](triage-labels.md) 中实际标签列比较，仅对缺失项执行 `gh label create <name> --description "..."`。已有标签保留原样，不使用 `--force` 覆盖。创建失败时报告失败项，重试前重新读取现有标签。

## 父子 Issue 与阻塞关系

- 从现有 spec 或 map Issue 拆出的每个 ticket，都挂为源 Issue 的子 Issue。创建时用 `gh issue create --parent <parent> --title "..." --body-file <body-file>`；已创建时用 `gh issue edit <parent> --add-sub-issue <child>`。这两个参数要求 `gh` 2.94+。
- 旧版 CLI 使用 `gh api --method POST repos/<owner>/<repo>/issues/<parent>/sub_issues -F sub_issue_id=<child-db-id>`。数据库 ID 用 `gh api repos/<owner>/<repo>/issues/<child> --jq .id` 获取；它不是 Issue 编号或 `node_id`。
- 原生子 Issue 不可用时，在子 Issue 正文顶部写 `Part of #<parent>`，并在父 Issue 正文维护子任务列表。
- 父子关系表示任务归属；阻塞关系表示执行依赖，两者分别建立。先发布阻塞方，再发布依赖它的任务。创建时用 `gh issue create --blocked-by <blocker-number,...> ...`；已创建时用 `gh issue edit <child> --add-blocked-by <blocker-number>`，使用前检查本机 `--help` 是否支持。
- 旧版 CLI 的阻塞操作用 `gh api --method POST repos/<owner>/<repo>/issues/<child>/dependencies/blocked_by -F issue_id=<blocker-db-id>`，数据库 ID 的获取方式同上。
- 开放阻塞数：`gh api repos/<owner>/<repo>/issues/<number> --jq .issue_dependencies_summary.blocked_by`，仅统计尚未关闭的阻塞方，`0` 表示依赖条件已满足。读取失败或字段缺失时不按 `0` 处理；改查依赖详情或使用下述文本回退。若改用 `gh issue view <number> --json blockedBy`，需只计入状态为 `OPEN` 的项。
- 已建立原生阻塞边时，省略正文中的 `## Blocked by`。平台不支持原生边时，才用该段落列出阻塞任务，并按其关闭状态判断能否开始。
- 关系创建失败时，报告失败并检查现有 Issue，保留已创建的任务；重试关联已有编号，避免重复创建。

## Pull requests as a triage surface

**PRs as a request surface: no.**

若用户启用外部 PR 分诊，使用 GitHub REST API 列出外部作者的开放 PR；`gh pr list --json` 不支持 `authorAssociation`：

```powershell
gh api --paginate 'repos/{owner}/{repo}/pulls?state=open' --jq '.[] | select(.author_association | IN("OWNER","MEMBER","COLLABORATOR") | not) | {number, title, author: .user.login, author_association, labels: [.labels[].name]}'
```

读取 PR 先用 `gh pr view <number>` 获取正文，再用 `gh pr view <number> --comments` 获取评论，差异用 `gh pr diff <number>`。非 TTY 输出下，`--comments` 只打印评论和 review，不包含正文。上述筛选只用于自动发现；用户明确指定的 PR 按请求处理。

GitHub Issues 与 Pull Requests 共用编号；遇到裸编号时，先尝试 `gh pr view <number>`，再回退到 `gh issue view <number>`。

## 技能操作映射

- “发布到 issue tracker”：创建 GitHub Issue。
- “获取相关 ticket”：按上述 JSON 查看操作读取正文、标签和评论。用户传入 ticket 引用时，在开始实现前复述其标题；引用存在歧义时先澄清。

## Wayfinding 操作

- 地图：创建一个带 `wayfinder:map` 标签的 Issue，正文维护 Notes、Decisions-so-far 和 Fog。
- 子任务：按上述父子操作关联到地图，用 `wayfinder:<type>` 标记 `research`、`prototype`、`grilling` 或 `task`。地图和决策任务只带 `wayfinder:` 标签；按类型标签决定处理方式。
- 关联：先创建所有相关 Issue，再用真实编号建立阻塞和交叉引用，避免把占位编号写入正文并链接到无关任务。
- 研究任务：成果保存在临时 `research/<name>` 分支，由 ticket 链接到实际成果；该分支不合并，不创建 PR。上游要求推送研究分支以保留引用，本工作区按提交和推送授权执行。
- 可领取任务：只考虑当前地图的开放子任务，排除已分配或仍有开放阻塞方的任务，按地图中的顺序选取。原生依赖可用时使用上面的开放阻塞数查询；回退为文本时读取阻塞 Issue 的关闭状态。
- 领取：用 `gh issue edit <number> --add-assignee @me` 分配给当前执行者。
- 完成：先评论结论，再关闭子任务，并在地图的 Decisions-so-far 中补充简短结论与 Issue 或产物链接。

命令依据[上游 GitHub tracker 模板](https://github.com/mattpocock/skills/blob/59db609eb1e18aaf59b63284d0e52255aff0c63c/skills/engineering/setup-matt-pocock-skills/issue-tracker-github.md)和 [Wayfinder](https://github.com/mattpocock/skills/blob/59db609eb1e18aaf59b63284d0e52255aff0c63c/skills/engineering/wayfinder/SKILL.md)。2026-10-27 本机 `gh` 2.103.3 的帮助确认了父子、阻塞、仓库查询及 PR 读取参数；远端写入和 API 返回行为未在本次文档同步中实测。
