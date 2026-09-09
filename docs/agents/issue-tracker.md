# Issue tracker: GitHub

本工作区的工程任务使用 GitHub Issues，通过 `gh` CLI 操作。

## 常用操作

- 创建：`gh issue create --title "..." --body "..."`
- 查看：`gh issue view <number> --comments`
- 列出：`gh issue list --state open`
- 评论：`gh issue comment <number> --body "..."`
- 添加或移除标签：`gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- 关闭：`gh issue close <number> --comment "..."`

仓库由当前目录的 Git remote 自动确定。

## Pull requests as a triage surface

**PRs as a request surface: no.**

GitHub Issues 与 Pull Requests 共用编号；遇到裸编号时，先尝试 `gh pr view <number>`，再回退到 `gh issue view <number>`。

## 技能操作映射

- “发布到 issue tracker”：创建 GitHub Issue。
- “获取相关 ticket”：读取对应 GitHub Issue 及评论。
- Wayfinding 使用一个 map issue 和多个 child issues；阻塞关系优先使用 GitHub 原生 issue dependencies。
