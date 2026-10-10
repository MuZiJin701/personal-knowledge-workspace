# Matt Pocock Skills 2026-10-08 更新核查

检查日期：2026-10-08（Asia/Shanghai）。接续 [2026-10-07 核查](github-skills-updates-2026-10-07.md)，依据用户提供的 CLI 日志、上游固定提交和本机只读比对。

本文保留当日快照；后续 `wizard` 模板修复和安装路线改版见 [2026-10-10 更新核查](github-skills-updates-2026-10-10.md)。

## 基准与结论

比较范围固定为 [`6fd9479...f3fc563`](https://github.com/mattpocock/skills/compare/6fd947921b935b7e1e69293a200400f0fdd5c15f...f3fc5632f401156837ee3872f14fe33ccf1024ea)：15 个提交、30 个文件，新增 113 行、删除 45 行。HEAD `f3fc5632f401156837ee3872f14fe33ccf1024ea` 的提交时间为北京时间 2026-10-07 18:22:44。

本次修改 10 个已有技能，没有新增或删除技能。清单仍为 38 项：Engineering 20、Productivity 7、In Progress 7、Misc 4；主推的稳定技能仍为 27 项。最新 GitHub Release 仍为 [`v1.3.1`](https://github.com/mattpocock/skills/releases/tag/v1.3.1)，本次属于发布后的 `main` 更新。[固定技能索引](https://github.com/mattpocock/skills/blob/f3fc5632f401156837ee3872f14fe33ccf1024ea/README.md)

## 十个技能的变化

| Skill | 最终行为 | 来源 |
| --- | --- | --- |
| `ask-matt` | 描述或推荐某个技能、建议跳过它之前，先读取该技能的 `SKILL.md`。 | [#1194](https://github.com/mattpocock/skills/pull/1194) |
| `code-review` | 搜索所有相关标准文档；存在时始终纳入 `CODING_STANDARDS.md` 和 `CONTRIBUTING.md`。两个审查子代理在前台并行运行，汇总返回的报告；tracker 路径从项目配置读取。 | [#1208](https://github.com/mattpocock/skills/pull/1208) |
| `diagnosing-bugs` | 用代码或 fixture 修改制造失败时，必须与原始副本比较，证明修改确实生效。 | [#1209](https://github.com/mattpocock/skills/pull/1209) |
| `implement` | 用户传入 ticket 引用时先获取并复述标题，再开始实现；引用有歧义时先澄清。 | [#1196](https://github.com/mattpocock/skills/pull/1196) |
| `setup-matt-pocock-skills` | GitHub/GitLab 配置后创建缺失的实际标签；GitHub 读取正文、标签、评论等 JSON 字段。GitLab JSON 输出参数改为 `-O json`，修正子任务关联的 iid 替换；标签映射列明确为 tracker 的实际标签。 | [#1197](https://github.com/mattpocock/skills/pull/1197) |
| `tdd` | 每个拟议测试边界用一行说明能发现什么、会漏掉什么，再请用户确认。 | [#1192](https://github.com/mattpocock/skills/pull/1192) |
| `wayfinder` | 地图和决策任务只用 `wayfinder:*` 标签；创建后用真实编号建立引用。按类型标签处理任务；研究成果保存在推送的临时分支，不创建 PR、不合并。 | [#1181](https://github.com/mattpocock/skills/pull/1181) |
| `grilling` | 问题措辞确保回答“是”表示接受推荐方案。 | [#1193](https://github.com/mattpocock/skills/pull/1193) |
| `teach` | 输出路径以调用时的工作目录为基准，`FORMAT.md` 引用以技能目录为基准；测验正确答案的位置要变化。 | [#1183](https://github.com/mattpocock/skills/pull/1183) |
| `wizard` | 模板支持 readline 编辑；无输入时明确失败；安全引用 `.env` 值，保留已有文件权限并通过符号链接写入，新文件使用 `0600`；缺少浏览器启动器时显示提示。 | [#1198](https://github.com/mattpocock/skills/pull/1198) |

`wizard` 本次修改的是 `template.sh`，其 `SKILL.md` 没有变化。因此只比对技能说明会遗漏实际脚本修复。[固定模板](https://github.com/mattpocock/skills/blob/f3fc5632f401156837ee3872f14fe33ccf1024ea/skills/engineering/wizard/template.sh)

README 另补充 Claude 插件安装排障：提示插件找不到时，运行 `claude plugins marketplace update` 后重试；官方 marketplace 固定版本可能落后于仓库。这与 Skills CLI 的 Git 源安装是不同入口。[#1195](https://github.com/mattpocock/skills/pull/1195)

## 本机证据与边界

用户日志中的 `skills update -g -y` 找到上述 10 项并逐项显示 `Updated`。随后 `skills add mattpocock/skills -g -y` 找到 38 项；`79 agents` 是 CLI 已知 Agent 定义数，不是本机安装成功的 Agent 数。粘贴日志止于安全评估链接，没有最终安装成功或失败汇总，不能套用 10 月 7 日的 PromptScript 失败结论。

本机 `skills --version` 返回 `1.7.1`。`%USERPROFILE%\.agents\.skill-lock.json` 中 38 项目录哈希均匹配固定 HEAD，更新时间为北京时间 2026-10-08 11:32:40 至 11:32:41。进一步比对 `%USERPROFILE%\.agents\skills\` 中的 103 个上游文件，归一化 CRLF/LF 后 Git blob 全部一致，零缺失、零内容差异。这不审计本机额外文件，也不验证每个 Agent 的运行时加载。

## 工作区文档同步

同步了[稳定技能指南](Matt-Pocock稳定Skills使用说明.md)、[CLI 指南](skills%20CLI与常用命令.md)、[工作目录约定](按照Matt-Pocock设计理念的标准工作目录结构.md)、[Issue tracker](../../docs/agents/issue-tracker.md)、[标签映射](../../docs/agents/triage-labels.md)和[本机 CLI 记录](../node-js/本机Node.js与npm环境记录.md)。

`skills update` / `add` 更新本机技能文件，不会自动改写此前生成的项目配置。项目配置需同步完整 ticket 读取、缺失标签初始化，以及 Wayfinding 的专用标签、真实编号引用和研究分支规则。本工作区仍要求明确授权后提交或推送；本次只更新文档，没有创建远端标签、修改 Issue 或推送分支。领域术语与 ADR 规则本次未变，沿用现有领域文档。
