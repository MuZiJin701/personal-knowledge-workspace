# Codex 用户目录清理速查

**先确认用途和当前引用，再归档、校验、删除。目录名带 `tmp`、`cache` 或版本较旧，都不能单独作为删除依据。**

Windows 用户目录通常是 `%USERPROFILE%\.codex`，也可由 `CODEX_HOME` 指定。它不只是配置目录，还包含会话、数据库、插件、程序和用户产物。

## 哪些内容要保留

| 内容 | 处理原则 |
| --- | --- |
| `config.toml`、`auth.json`、`AGENTS.md`、规则和技能 | 保留配置、登录与自定义行为。 |
| `sessions`、`archived_sessions`、历史和状态数据库 | 保留会话数据；旧数据库可能仍有独有记录。 |
| SQLite 的主文件、`-wal`、`-shm` | 作为一组处理；程序运行时不要单独删除或压缩。 |
| 当前插件、市场源码、运行程序和近期缓存 | 查配置、注册路径、符号链接和进程后再判断。 |
| 图片、附件、可视化和宠物资源 | 属于用户产物或资源，不能按“缓存”一概删除。 |
| 旧日志、失败安装、过期暂存副本和旧备份 | 无当前引用、无本地修改且内容稳定时，可归档。 |

滚动生成的最新 `.bak`、锁文件和空运行目录通常很小，不必为了目录整齐而反复删除。

## 三类容易误判的程序

- **沙箱程序**：`.sandbox-bin` 内的文件用途不同。主程序、命令运行器和系统沙箱服务不能混为一谈；结合版本、近期日志和服务路径判断，保留正在使用的运行器。
- **WSL 程序**：`bin\wsl\codex` 是 Linux 可执行文件。检查 Windows MCP 配置、WSL 的 PATH、启动配置、符号链接和进程。归档该文件不等于卸载 WSL；Windows 原生 Codex 不要求 WSL。
- **Chrome 旧插件**：同时检查 `latest` 链接、浏览器原生宿主注册和宿主进程。登记文件中的历史版本号不一定是有效引用。删除失败时区分占用、权限和只读属性；看到 Deny ACL 不能直接断言当前用户被拒绝。

## 安全清理与恢复

1. 确定实际用户目录，统计文件大小；跳过 Junction、符号链接和 socket，避免重复计算或误删目标。
2. 明确候选清单，核查配置、进程、注册与本地修改，保留无关内容。
3. 归档到 `D:\data\archive\codex-cleanup-YYYY-MM-DD-HHMMSS`，保存源路径、归档路径、文件大小和 SHA256。
4. 校验归档与源文件一致，并确认源内容未变化后，再删除源副本。部分删除失败时恢复缺失内容，不覆盖已有文件。
5. 验证配置可解析、关键数据存在、当前插件注册有效；记录实际清理量和未处理项。

`manifest.json` 保存每项的 `Source`、`Archive` 和处理状态；`inventory.csv` 保存逐文件校验信息。**恢复时先关闭相关程序，再按清单从 `Archive` 复制回 `Source`，校验后启动。已有目标先比较，不盲目覆盖。**

归档可能复用前一批已验证的副本，因此恢复位置以清单为准，不一定都在最新批次目录里。归档仍可能含敏感数据，不公开上传。

## 配置警告：先验证当前版本

- **未知字段**：TOML 能解析，不代表当前 Codex 支持该字段。先核对本机版本和严格配置检查，再与官方文档比较；不要仅按文档改写就认定有效。
- **废弃开关**：删除明确废弃的项，包含 profile 覆盖；当前配置已无此项时，不添加替代开关。用新诊断进程验证，区分新警告与界面中的旧提示。

本次 `computer_use.windows.always_allowed_app_ids` 既写成了表，也不被本机两个核心版本支持；改成官方文档中的数组仍被拒绝。已备份原配置及三条应用记录，再移除这个无效配置块；未添加新权限规则。应用许可应在当前应用设置中核对。

`guardianv2.thread_context` 已移除，线程所属的 Guardian 上下文始终启用。本地配置没有此开关，但桌面程序重新启动后，在恢复本地会话时仍产生同一条提示。**初始化和 `config/read` 通过，只能证明配置加载正常，不能证明会话路径没有警告。**

临时、不保存的会话对照测试已复现：不传此开关时无提示，传入 `features.guardianv2 = { thread_context = true }` 时产生相同的 `deprecationNotice`。检查应同时关注 `configWarning` 和 `deprecationNotice`，并覆盖会话启动或恢复；测试未调用模型。

本次已确认来源：正在运行的桌面进程中，Guardian 实验配置的唯一开关仍是 `thread_context = true`，最低核心版本为 `0.155.0-alpha.9`。本机桌面代码会将它合并进 `features.guardianv2`，当前核心版本符合下发条件，却已废弃这个开关，因此会话恢复时产生提示。此配置来自桌面的服务端实验分配，删除本地 `config.toml` 中的字段或重启应用都不能阻止重新下发。

彻底消除提示需要服务端停止下发该废弃项，或桌面应用过滤它。2026-10-07 检查桌面版本 **26.1002.51308（build 13417）**，当前更新通道返回 `up_to_date`，没有可安装的更新；这不代表问题已修复。当前未修改安装包或进程。

### 当前处理方法

1. 本地配置如有 `thread_context`，删除该项及 profile 覆盖；本次已确认没有，无需再改。不要添加 `thread_context=false`，它仍是废弃字段。
2. 在输入框输入 `/feedback`，打开反馈窗口，使用下面的英文内容提交问题。日志附件是可选项，本文不附敏感日志。
3. 服务端配置修正或后续桌面版本修复后，重新启动应用并恢复本地会话，确认相同提示不再产生。

当前可以继续使用：该字段被忽略，线程所属的 Guardian 上下文仍始终启用。无需清空会话、登录数据或缓存；每个应用的 UI 许可行为未逐项验证。

### 英文反馈内容

> Windows desktop app version: 26.1002.51308
>
> Bundled core version: 0.162.0-alpha.2
>
> The warning below appears when resuming a local chat, even though `guardianv2.thread_context` is absent from my local `config.toml`:
>
> `[features.guardianv2].thread_context` is deprecated and ignored.
>
> Investigation found that the desktop app’s Guardian experiment configuration still supplies `thread_context=true`. Passing this override to an ephemeral diagnostic session reproduces the same warning; omitting it produces no warning.
>
> Please stop supplying this deprecated field through the experiment configuration, or filter it out in the desktop client before passing configuration overrides to the core.

## 本次案例：2026-10-07

| 最后一批清理项 | 实际移出 |
| --- | ---: |
| 沙箱 `codex.exe` 0.146.0 及配套运行器 | 343.28 MiB |
| WSL Codex 0.144.5 | 284.67 MiB |
| Chrome 插件 26.930.61225 | 13.71 MiB |
| **合计** | **641.66 MiB** |

保留沙箱运行器 0.160.0、0.160.1，以及 Chrome 插件 26.1002.51308。最后一批 349 个文件校验通过；Chrome 复用已有归档，未修改权限或主动结束宿主进程。

全部六批累计移出约 **1,079.72 MiB（1.05 GiB）**。版本和引用检查是当时的本机结果，不是通用删除规则；旧沙箱兼容回退未做完整运行测试，归档应保留用于回滚。这里统计文件逻辑大小，不等同于磁盘实际释放空间；运行中目录大小也会变化。

## 参考

- [官方 OpenAI 文档：Windows 沙箱](https://learn.chatgpt.com/docs/windows/windows-sandbox)
- [官方 OpenAI 文档：配置基础](https://learn.chatgpt.com/docs/config-file/config-basic)
- [官方 OpenAI 文档：Computer Use](https://learn.chatgpt.com/docs/computer-use)
- [官方 OpenAI 文档：App Server 与会话配置覆盖](https://learn.chatgpt.com/docs/app-server)
- [官方 OpenAI 文档：斜杠命令与反馈入口](https://learn.chatgpt.com/docs/reference/slash-commands)
- [权限配置速查](codex-permissions-config.md)
- [插件市场管理](codex-plugin-marketplace-management.md)
