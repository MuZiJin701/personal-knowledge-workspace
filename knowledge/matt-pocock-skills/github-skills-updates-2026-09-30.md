# 两个 Skills 仓库近期更新

检查日期：2026-09-30（Asia/Shanghai）

本文保留当日核查结果。后续 `v1.3.1` 发布、`ask-matt` 路由修正和本机安装复核见[2026-10-05 更新记录](github-skills-updates-2026-10-05.md)。

## 结论

- `mattpocock/skills` 的 `resolving-merge-conflicts` 是上游真实删除，不是单纯改名或移动。2026-09-24 的提交 `daa01d8` 明确删除了 Skill 文件、Codex 元数据、文档页以及各处入口；提交说明为“不再需要”。[删除提交](https://github.com/mattpocock/skills/commit/daa01d8aa68ad5c61b68970ec2018d0ce9567be6)
- `skills update -g -y` 提示 `Skill paths changed; resolving via Git clone`，说明更新器判断其已记录的技能路径映射发生变化，因而改用 Git 克隆解析；这段输出没有指出哪条路径触发，也不能据此断言该技能被改名/搬家。上游 Git 历史可确认 `resolving-merge-conflicts` 被删除，而非移动：该文件 2026-09-24 从 `skills/engineering/` 删除，至检查到的 2026-09-29 `main` HEAD 未重加。GitHub 搜索/旧 issue 页面若仍显示这个路径，不能代替当前分支文件状态。
- 初次更新后，`C:\Users\30733\.agents\.skill-lock.json` 仍记录 `resolving-merge-conflicts` 的旧路径和 2026-08-20 的更新时间，本机目录也仍在；这是 `-y` 跳过删除的结果。随后已执行 `skills remove --global resolving-merge-conflicts -y`，确认锁记录与本机目录均已清除；再运行 `skills update -g -y`，CLI 返回所有全局技能均为最新。其余 Matt 技能记录在 2026-09-29 更新。锁文件是本机状态证据，不是上游事实来源。
- `skills update -g -y` 只更新本机已安装的技能，不会加入上游新技能。复核发现缺少 `implement-spec`、`pr`、`retro` 后，已通过 `skills add mattpocock/skills --global --agent '*' --skill implement-spec pr retro -y` 补齐；本机现在安装上游现存的 37 个 Matt Pocock 技能。CLI 报告 79 个 Agent 目标，Eve 和 PromptScript 不支持全局安装，其余目标成功。后续全量同步可用 `skills add mattpocock/skills --global --all`；被上游删除的技能仍需显式 `skills remove -g <skill-name> -y`。

## mattpocock/skills 更新内容

更新器本次实际报告更新了 `ask-matt`、`codebase-design`、`diagnosing-bugs`、`domain-modeling`、`improve-codebase-architecture`、`setup-matt-pocock-skills`、`tdd`、`triage` 和 `wait-what`；共 9 个仍存在的 Matt 技能。[仓库提交记录](https://github.com/mattpocock/skills/commits/main) [更新日志](https://github.com/mattpocock/skills/blob/main/CHANGELOG.md)

可以确认的用户可见变化包括：

- `diagnosing-bugs` 增加对命令、输出和捕获材料的秘密信息脱敏要求。[对应提交](https://github.com/mattpocock/skills/commit/efce423018fc6468a3239621f1c1bcaacc723801)
- `codebase-design` 和 `improve-codebase-architecture` 的子代理调度指令改为跨 Agent harness 的通用措辞，便于 Codex 等其他运行环境使用。[更新日志](https://github.com/mattpocock/skills/blob/main/CHANGELOG.md)
- `ask-matt` 路由补入 `implement-spec`、`pr`、`retro` 等流程；`retro` 的主流程位置也有调整。[提交记录](https://github.com/mattpocock/skills/commits/main)

截至当前上游 `main` 和本机已安装的 `domain-modeling`、`setup-matt-pocock-skills`，领域术语文件已采用 `GLOSSARY.md` / `GLOSSARY-MAP.md` 命名；工作区内的标准结构说明已同步使用新名称。[上游 README](https://github.com/mattpocock/skills/blob/main/README.md) [当前 domain-modeling Skill](https://github.com/mattpocock/skills/blob/main/skills/engineering/domain-modeling/SKILL.md)

关于删除的本机影响：因为本次运行带 `-y`，删除被跳过，旧技能仍可从本机目录调用；若希望与上游保持一致，需要之后显式清理它，或接受它作为本机保留副本。此次输出不能证明更新器是否会在下次交互式更新时自动处理它。

## MuZiJin701/wx-video-account-notes 更新内容

该仓库当前 `main` 已超过 `v0.2.3` 标签。比较 `v0.2.3..main` 可见，近期主要工作是新增解析服务方案、部署端和错误处理，不只是技能文案调整。[版本比较](https://github.com/MuZiJin701/wx-video-account-notes/compare/v0.2.3...main) [最新提交](https://github.com/MuZiJin701/wx-video-account-notes/commit/83603f08f17b25395f8f6c16c92b6be31551cb6e)

- 新增默认公网 resolver，也允许用 `WX_VIDEO_ACCOUNT_RESOLVE_API` 和 `WX_VIDEO_ACCOUNT_RESOLVE_KEY` 指向自部署服务；失败不会自动切回本地旧流程。[当前 Skill](https://github.com/MuZiJin701/wx-video-account-notes/blob/main/plugins/wx-video-account-notes/skills/wx-video-account-notes/SKILL.md) [Resolver 部署说明](https://github.com/MuZiJin701/wx-video-account-notes/blob/main/docs/resolver-deployment.md)
- 新增 `server/` gateway、部署说明和相关测试，并区分解析凭证无效、限流、上游登录状态过期/变化、feed 不可用等错误。[resolver 提交](https://github.com/MuZiJin701/wx-video-account-notes/commit/4a1bc7f) [变更日志](https://github.com/MuZiJin701/wx-video-account-notes/blob/main/CHANGELOG.md)
- 默认配置的重要信任边界：resolver 地址使用 HTTP；链接和响应会以明文经过网络；随包共享 bearer 值不具保密性；元宝 Cookie 由服务维护者一侧持有。若不接受这条数据路径，可改为自部署 resolver，而不是仅因技能已更新就启用默认服务。[部署说明](https://github.com/MuZiJin701/wx-video-account-notes/blob/main/docs/resolver-deployment.md) [默认配置](https://github.com/MuZiJin701/wx-video-account-notes/blob/main/plugins/wx-video-account-notes/skills/wx-video-account-notes/resources/resolver_config.json)

## 说明与限制

以上描述的是检查日的 GitHub `main` 和用户贴出的更新器输出。上游分支可能继续变化；`skills update` 的输出只列出技能名，不包含每个文件的差异，也不能单独证明某条更新的具体内容。`wx-video-account-notes` 的 `v0.2.3` 标签尚未包含当前 `main` 的这些后续变化。
