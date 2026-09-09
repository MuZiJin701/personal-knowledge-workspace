# Matt Pocock Skills 近期更新核查（2026-08-20）

核查对象为 [`mattpocock/skills`](https://github.com/mattpocock/skills) 的 `main` 分支，检索时 HEAD 为 `885e2ca`（2026-08-19）。

## 与本次 `skills update -g` 的关系

- 这次「30 个可更新项」主要不是 30 项新功能：2026-08-19 的 [`3216582`](https://github.com/mattpocock/skills/commit/3216582) 在 99 个文件中移除了 em dash，其中包含输出中列出的 28 个 Matt Pocock skill。
- 随后的 [`5c89081`](https://github.com/mattpocock/skills/commit/5c89081) 修复了 6 个 `SKILL.md` 的 YAML front matter：将含 `: ` 的 description 用引号包裹，否则 `skills.sh` 会跳过发现、`npx skills` 无法安装。受影响的是 `code-review`、`setup-matt-pocock-skills`、`to-spec`、`writing-fragments`、`writing-shape`、`wait-what`；这与您看到的成功更新相符。
- 失败的 `ask-matt`、`improve-codebase-architecture`、`prototype`、`resolving-merge-conflicts` 不在上述 YAML 修复范围内；它们与成功项共同的最新变更只是 `3216582`。

## 本机更新结果

- 2026-08-20 已对上述四项执行等价的直接重装，均成功覆盖到 `%USERPROFILE%\.agents\skills\`。
- 原始批量更新隐藏了子进程输出。直接重装显示的附加失败来自 PromptScript：它被 CLI 自动识别为目标，但不支持全局技能安装；这不影响 Codex 和 `.agents` 安装目标。
- 随后执行 `skills update -g -y` 返回“所有全局技能均为最新”。因此本机已安装的 Matt Pocock skills 已完成更新。

## 2026-08-21 核查：`grilling` 更新

- [`85f83d3`（2026-08-20 10:32 UTC）](https://github.com/mattpocock/skills/commit/85f83d3fde1d3a90d5c9a657f6998c79a6c37308) 将 `grilling` 的输出示例从“单个问题”改为“一个 round”：多问题 round 中相邻问题以水平线 `---` 分隔，并保留每题的推荐答案。实际行为变化仅为多题输出的视觉分块，避免问题连续黏连。
- 已核对 `%USERPROFILE%\.agents\skills\grilling\SKILL.md`：将 Windows 的 CRLF 归一化为 LF 后，其 Git blob SHA 为 `8ca78c6d8f901aab0c5a1f896034b70e666ff2a3`，与该提交中的上游文件一致；本机 `skills update -g -y` 已成功安装此更新。

## 少数有实际行为变化的近期更新

- [`ask-matt`（`fa1e322`，8 月 5 日）](https://github.com/mattpocock/skills/commit/fa1e322)：增加阶段边界的决策路径，涵盖继续、`/clear`、交接/子代理和压缩上下文。
- [`diagnosing-bugs`（`efce423`，8 月 6 日）](https://github.com/mattpocock/skills/commit/efce423)：明确要求对密钥等敏感信息进行脱敏。
- [`code-review`、`codebase-design`、`improve-codebase-architecture`（`14bfbbd`，8 月 6 日）](https://github.com/mattpocock/skills/commit/14bfbbd)：涉及子代理的措辞改为适配不同 agent harness。
- [`wizard`（`c0fd1e9`，8 月 6 日）](https://github.com/mattpocock/skills/commit/c0fd1e9)：移除虚构的分钟数/剩余时间估算。
- [`domain-modeling`（`bd8e81b`，8 月 13 日）](https://github.com/mattpocock/skills/commit/bd8e81b)：明确在编辑 `CONTEXT.md` 或 ADR 时触发。
- [`wait-what`（`d6cd26f`，8 月 19 日）](https://github.com/mattpocock/skills/commit/d6cd26f)：多上下文仓库会从 `CONTEXT-MAP.md` 定位正确的 `CONTEXT.md`。
- [`writing-for-agents`（`4aaccb5`，8 月 5 日）](https://github.com/mattpocock/skills/commit/4aaccb5)：调整了 Codex 的可自动调用性与元数据。

上游近期发行版为 [`v1.2.3`](https://github.com/mattpocock/skills/releases/tag/v1.2.3)；完整发行记录见 [`CHANGELOG.md`](https://github.com/mattpocock/skills/blob/main/CHANGELOG.md)。
