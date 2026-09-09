# skills CLI：Agent Skills 管理与常用命令

`skills` 是 Vercel Labs 开源的 Agent Skills CLI，负责发现、安装、更新、删除和创建包含 `SKILL.md` 的技能目录。它可服务于 Codex、Claude Code、Cursor、OpenCode 等 Agent。

`vercel-labs/skills` 是 CLI 项目本身；`vercel-labs/agent-skills` 是可安装的技能集合。Matt Pocock 的技能来源是 `mattpocock/skills`。

## 安装范围

CLI 有两个范围：

| 范围 | 参数 | 典型用途 |
|---|---|---|
| 项目级 | 默认 | 随项目提交，与团队共享 |
| 全局 | `-g` / `--global` | 所有项目可用 |

实际目录由目标 Agent 决定。CLI 的通用形式是：

```text
项目级：./<agent>/skills/
全局：  ~/<agent>/skills/
```

例如，Codex 的目标目录由 CLI 当前版本和安装目标决定；用 `skills list -g --json` 查看实际路径，不要假设所有 Agent 共用一个目录。

## 安装技能

```bash
# 查看仓库提供的技能
skills add mattpocock/skills --list

# 安装到当前项目，并指定 Codex
skills add mattpocock/skills --skill writing-for-agents --agent codex

# 全局安装，并指定 Codex
skills add mattpocock/skills --skill writing-for-agents --global --agent codex
```

也可以使用 `npx skills@latest` 代替已安装的 `skills` 命令：

```bash
npx skills@latest add owner/repo --skill my-skill
```

支持 GitHub 简写、完整 Git URL、GitLab URL、本地目录和直接下载 URL：

```bash
skills add owner/repo
skills add https://github.com/owner/repo
skills add ./my-local-skills
```

常用安装参数：

| 参数 | 作用 |
|---|---|
| `--skill <name>` / `-s` | 指定技能，可重复使用 |
| `--agent <name>` / `-a` | 指定目标 Agent |
| `--global` / `-g` | 使用全局范围 |
| `--list` / `-l` | 只列出技能，不安装 |
| `--copy` | 复制文件，不使用符号链接 |
| `--yes` / `-y` | 跳过确认 |
| `--all` | 全部技能安装到全部目标 Agent |

默认推荐符号链接；不支持符号链接时再使用 `--copy`。

## 常用命令

| 命令 | 用途 | 示例 |
|---|---|---|
| `skills add` | 安装技能 | `skills add owner/repo --skill my-skill` |
| `skills use` | 不安装，临时生成使用提示 | `skills use owner/repo@my-skill` |
| `skills list` / `ls` | 查看已安装技能 | `skills ls -g` |
| `skills find` | 搜索技能 | `skills find react` |
| `skills update` | 更新已安装技能 | `skills update -g -y` |
| `skills remove` / `rm` | 删除技能 | `skills remove -g my-skill` |
| `skills init` | 创建 `SKILL.md` 模板 | `skills init my-skill` |

常用查询和更新：

```bash
skills list -g
skills list -g --json
skills update -g -y
skills update -g my-skill -y
```

`skills update -y` 会跳过范围询问；`skills update -g -y` 明确只更新全局技能。非交互更新遇到上游删除时，CLI 可能跳过本地删除并给出警告。

### 更新失败的诊断

`✗ Failed to update <skill>` 只表示内部重装子进程未得到成功结果；它可能不显示该子进程的具体错误。先用同一来源和技能名直接重装，以保留完整输出：

```powershell
skills add mattpocock/skills/skills/engineering/<skill-name> -g -s <skill-name> -y
```

本机 2026-08-20 的四项 Matt Pocock skill 更新曾显示失败，但直接执行后确认已覆盖到 `%USERPROFILE%\.agents\skills\`。诊断输出显示额外的 PromptScript 目标不支持全局安装；这不影响 Codex 或 `.agents` 目录中的技能。随后 `skills update -g -y` 已验证所有全局技能为最新。

CLI 会依次检查所有已配置的 Skill 来源。`Found N global update(s)` 是所有来源的全局更新总数；后面的每个 `Updating ...` 才对应一个实际更新的 Skill，因此一次命令可能同时更新多个仓库来源、稳定 Skill 和 Beta Skill。

更新对象是整个 Skill 目录，不只是一份 `SKILL.md`。例如 `codebase-design` 的配套设计说明、`diagnosing-bugs` 的脚本模板和 `wizard` 的 `template.sh` 也可能随 Skill 一起更新。

## 最小工作流

```text
skills find 关键词
    ↓
skills add owner/repo --list
    ↓
skills add owner/repo --skill my-skill
    ↓
skills list
    ↓
skills update
```

## 创建自己的 Skill

```bash
skills init my-skill
```

最小的 `SKILL.md`：

```markdown
---
name: my-skill
description: 说明技能做什么以及什么时候使用
---

# My Skill

写给 Agent 的具体操作说明。
```

必需字段只有：

- `name`：技能唯一名称，通常使用小写字母和连字符
- `description`：技能用途和适用场景

## 使用建议

- 个人长期复用的技能放全局范围
- 项目专用或需要团队共享的技能放项目范围并提交到仓库
- 安装第三方技能前先检查 `SKILL.md`，不要盲目信任其中的命令
- 不要把同一个技能重复安装到全局和项目，避免来源和版本混淆

## 参考资料

- [skills CLI 官方仓库](https://github.com/vercel-labs/skills)
- [Agent Skills 规范](https://agentskills.io)
- [Skills Directory](https://skills.sh)
- [Matt Pocock Skills](https://github.com/mattpocock/skills)
