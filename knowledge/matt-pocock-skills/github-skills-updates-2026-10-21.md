# Matt Pocock Skills 2026-10-21 更新核查

检查日期：2026-10-21（Asia/Shanghai）。接续 [2026-10-10 核查](github-skills-updates-2026-10-10.md)，依据上游 GitHub API、固定源码及本机只读比对。本轮用户未提供 CLI 更新日志。

本文保留当日快照；后续 setup、tracker 模板及贡献流程变化见 [2026-10-27 更新核查](github-skills-updates-2026-10-27.md)。

## 基准与结论

比较范围固定为 [`49dd158...5beb35b`](https://github.com/mattpocock/skills/compare/49dd158d1076134a641b33efb035946536778336...5beb35b040e3de97e54fa9e386d7e42d15c295c2)：1 个提交、2 个文件，新增 29 行、删除 1 行。HEAD `5beb35b040e3de97e54fa9e386d7e42d15c295c2` 的提交时间为北京时间 2026-10-19 23:51:40。

唯一技能内容变化是实验区 `chief-of-staff` 新增持久 Wiki 规则；另新增 `.changeset/chief-of-staff-wiki.md` 补丁记录。没有新增或删除技能，`wizard` 等其余 37 项技能内容未变。[PR #1304](https://github.com/mattpocock/skills/pull/1304)、[固定提交](https://github.com/mattpocock/skills/commit/5beb35b040e3de97e54fa9e386d7e42d15c295c2)

固定目录树仍有 38 个 `SKILL.md`：Engineering 20、Productivity 7、In Progress 7、Misc 4。最新 GitHub Release 和插件版本仍为 [`v1.3.1`](https://github.com/mattpocock/skills/releases/tag/v1.3.1)；插件包含的 27 项稳定技能均未变，且不包含 `chief-of-staff`。本次是发布后的 `main` 更新。[固定目录树](https://github.com/mattpocock/skills/tree/5beb35b040e3de97e54fa9e386d7e42d15c295c2/skills)、[插件清单](https://github.com/mattpocock/skills/blob/5beb35b040e3de97e54fa9e386d7e42d15c295c2/.claude-plugin/plugin.json)

## chief-of-staff 的变化

该技能仍标注 `disable-model-invocation: true`，只在用户明确调用后启动。新增规则服务于长期目标的跨会话记忆，要求使用互相链接的 Markdown Wiki；没有 Wiki 时，首次写入前先与用户确定位置。[固定技能源码](https://github.com/mattpocock/skills/blob/5beb35b040e3de97e54fa9e386d7e42d15c295c2/skills/in-progress/chief-of-staff/SKILL.md)

| 改动 | 使用时的影响 |
| --- | --- |
| `index.md` 加单主题页面 | 索引按类别列出每页链接和一句摘要；决策、反复出现的问题、子系统、用户偏好分别成页，相关页面互相链接。 |
| Ingest / Query / Lint | 学到长期信息时同步所有受影响页面和索引；决策前先查索引及相关页；定期检查矛盾、孤立页、坏链接和过时断言，并建议排期。 |
| 日期和提交依据 | 每项断言注明日期；涉及代码时引用其成立时的仓库与提交，避免无依据地描述“当前”状态。 |
| 保留历史 | 变化发生时新增带日期的断言，并标记旧项已被取代，不覆盖旧记录。 |
| 来源只引用 | Issue、PR、对话记录、子代理报告等原始材料保留原处，Wiki 页面引用它们；子代理交流也可通过 Wiki 页面指针传递上下文。 |

这些规则来自 [PR #1304](https://github.com/mattpocock/skills/pull/1304)。Lint 的排期只是技能提出的建议，没有新增可自动执行的检查程序或调度配置。

## 本机证据与边界

本机只读检查得到 Skills CLI `1.7.2`。`%USERPROFILE%\.agents\.skill-lock.json` 中 38 项目录哈希全部匹配固定 HEAD，更新时间为北京时间 2026-10-21 13:30:46.423 至 13:30:46.814。进一步比对 `%USERPROFILE%\.agents\skills\` 中的 103 个上游技能包文件，归一化 CRLF/LF 后 Git blob 全部一致，零缺失、零内容差异；分类目录 README 未纳入，也未审计本机额外文件。

这些证据说明核查时本机技能文件与固定源码一致。由于本轮没有 CLI 更新日志，无法据此还原本次 `skills update` 显示的更新数量或每个安装目标的成功状态；本次也没有验证各 Agent 的运行时加载。

## 安装与项目文档影响

全量安装的 38 项技能中包含实验技能，因此可取得这次 `chief-of-staff` 变化；仅使用上游插件清单中的 27 项稳定技能时不涉及这次内容变更。安装命令、插件版本、`setup-matt-pocock-skills`、Issue tracker、标签映射及领域建模规则均未因本次提交变化。[完整差异](https://github.com/mattpocock/skills/compare/49dd158d1076134a641b33efb035946536778336...5beb35b040e3de97e54fa9e386d7e42d15c295c2)

本次只研究源码并保存核查报告，没有启动 `chief-of-staff`、创建 Wiki 或自动化，也没有调整项目 `AGENTS.md` 及工程配置。

后续文档同步已更新[稳定技能指南中的实验技能说明](Matt-Pocock稳定Skills使用说明.md#chief-of-staff-的长期记忆)、[CLI 指南](skills%20CLI与常用命令.md)、[工作目录约定](按照Matt-Pocock设计理念的标准工作目录结构.md#chief-of-staff-的长期记忆目录)和[本机 CLI 记录](../node-js/本机Node.js与npm环境记录.md)，并补齐历史记录导航；未创建实际 Wiki 或定期任务。
