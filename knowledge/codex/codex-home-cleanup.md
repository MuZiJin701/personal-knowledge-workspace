# Codex 用户目录清理速查

**先查引用，再归档校验，最后删除。目录名带 tmp、cache 或版本旧，都不是删除依据。** Windows 通常位于 `%USERPROFILE%\.codex`，也可由 CODEX_HOME 指定。

## 保留什么

| 内容 | 原则 |
| --- | --- |
| config.toml、auth.json、AGENTS.md、规则和技能 | 保留配置、凭据和自定义行为。 |
| sessions、archived_sessions、历史和状态库 | 保留会话；旧库可能仍有独有记录。 |
| SQLite 主文件、-wal、-shm | 一组处理，运行中不单独删除或压缩。 |
| 插件、市场、程序和近期缓存 | 先查注册、配置、链接、进程和本地修改。 |
| 图片、附件、可视化、宠物 | 用户产物，不能一律当缓存。 |
| 旧日志、失败安装、旧备份 | 无引用、无本地修改且稳定后可归档。 |

最新 .bak、锁文件和空运行目录通常很小，不必反复删除。

容易误判的程序：

- `.sandbox-bin` 的主程序、运行器和沙箱服务用途不同，结合版本、引用和进程判断。
- `bin\wsl\codex` 是 Linux 程序；先查 WSL PATH、MCP、链接和进程。归档它不等于卸载 WSL，Windows 原生 Codex 也不要求 WSL。
- Chrome 插件要查 latest 链接、原生宿主注册和进程；历史版本号不等于有效引用。删除失败需区分占用、权限和只读属性。

## 清理与恢复

1. 确认用户目录和候选清单；跳过 Junction、符号链接和 socket，保留无关内容。
2. 归档至 `%ARCHIVE_ROOT%\codex-cleanup-YYYY-MM-DD-HHMMSS`，保存源路径、目标路径、大小和 SHA256。ARCHIVE_ROOT 是自选归档根目录。
3. 确认源内容未变化、归档校验一致后，才删源副本。部分失败时恢复缺失项，不覆盖现有文件。
4. 检查配置、会话数据和插件引用，记录移出量及未处理项。

恢复前关闭相关程序，按 manifest.json 的 Source、Archive 和状态恢复，再校验启动；inventory.csv 保存逐文件校验信息。归档可能复用早一批副本，以清单为准。已有目标先比较，不盲目覆盖；归档可能含敏感数据，不公开上传。

## 配置警告：先验证当前版本

TOML 能解析，不代表当前核心支持字段；初始化或 config/read 通过，也不证明启动、恢复会话时没有警告。核对版本和实际会话，区分 configWarning、deprecationNotice 与界面旧提示。不要靠清缓存、添加废弃开关或修改安装包来消除提示。

以下均为 **2026-10-07 历史案例**，不代表今天仍存在同一问题：

| 问题 | 当时的证据与处理 |
| --- | --- |
| computer_use.windows.always_allowed_app_ids | 表和官方文档所示数组都被本机两个核心版本拒绝。备份原配置及三条应用记录后移除无效块；应用许可在当前设置核对，不添加新权限规则。 |
| guardianv2.thread_context 废弃提示 | 本地没有该项，恢复会话仍提示。临时会话不传时无提示，传入 features.guardianv2 的 thread_context=true 就出现同一 deprecationNotice，测试未调用模型。 |

当时桌面实验仍下发 thread_context=true，最低核心版本为 0.155.0-alpha.9；核心已忽略该字段，Guardian 上下文始终启用。根因在服务端实验下发与桌面合并，删本地字段或重启不能阻止再次下发。彻底修复需停止下发或由客户端过滤。

当时桌面 26.1002.51308（build 13417）、核心 0.162.0-alpha.2，更新检查为 up_to_date；这不表示问题已修复，未修改安装包或进程。处理顺序：

1. 本地及 profile 若有废弃项就删除；没有则不改，也不添加 thread_context=false。
2. 用 /feedback 反馈。可简述：本地配置无该项，但桌面实验仍传入废弃开关，导致恢复会话警告；请求停止下发或过滤。无需附敏感日志。
3. 后续修复后重启并恢复会话，确认提示消失。无需清空凭据、历史或缓存；应用 UI 许可未逐项验证。

## 清理案例：2026-10-07

最后一批移出沙箱 Codex 0.146.0 及运行器 343.28 MiB、WSL Codex 0.144.5 共 284.67 MiB、Chrome 插件 26.930.61225 共 13.71 MiB，合计 **641.66 MiB**。349 个文件校验通过；Chrome 复用已有归档，未修改权限或主动结束宿主。

保留沙箱运行器 0.160.0、0.160.1 和 Chrome 插件 26.1002.51308。六批累计移出约 **1,079.72 MiB（1.05 GiB）**，是文件逻辑大小，不等于实际释放空间。旧沙箱兼容回退未做完整运行测试，保留归档用于恢复；这些版本不是通用删除清单。

## 参考

- [Windows 沙箱](https://learn.chatgpt.com/docs/windows/windows-sandbox) · [配置基础](https://learn.chatgpt.com/docs/config-file/config-basic)
- [Computer Use](https://learn.chatgpt.com/docs/computer-use) · [App Server](https://learn.chatgpt.com/docs/app-server) · [反馈入口](https://learn.chatgpt.com/docs/reference/slash-commands)
- [权限配置](codex-permissions-config.md) · [插件管理](codex-plugin-marketplace-management.md)
