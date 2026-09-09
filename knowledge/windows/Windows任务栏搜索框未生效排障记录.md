# Windows 任务栏搜索框未生效排障记录

## 现象

- 设置中选择“搜索框”后，任务栏仍只显示搜索图标。
- 该状态在 Windows 官方“使用 Windows 更新修复问题”完成并重启后仍存在。

## 已验证事实

- 当前 Windows 版本：26200.9168。
- 任务栏并非紧凑模式：TaskbarSmallIcons=0、UseCompactMode=0；未启用自动隐藏，且只有一个活动显示器。
- 未发现任务栏搜索策略、第三方任务栏替换/美化工具，或搜索/开始菜单组件崩溃。
- 搜索相关现代组件正常：Microsoft.BingSearch、Microsoft.Windows.StartMenuExperienceHost、MicrosoftWindows.Client.CBS 均为 Ok。
- 用户设置与 Shell 状态不一致：SearchboxTaskbarMode=2，但 SearchboxTaskbarModeCache=1，对应实际的仅图标显示。

## 与此前 AppX 排障的关系

2026-08-14 15:18 的人工清理脚本曾删除整个旧版
Microsoft.StartExperiencesApp_2021.380.2.0 包族。该操作与开始菜单/任务栏有关，不能再视为无关操作。

但该旧包清单最高适配 Windows 22621，而当前系统为 26200；当前现代开始菜单和搜索组件完整。官方修复安装后图标状态仍未改变，因此不能证明该旧包删除是本问题的直接原因，也**不能**从 AppRepository 备份硬还原它——那会重新引入陈旧脱机包注册和开机弹窗风险。

## 已尝试且结果

| 操作 | 结果 |
| --- | --- |
| 在设置中选择“搜索框” | 视觉状态未改变，内部模式未更新为完整搜索框 |
| 直接修改受保护的用户搜索设置 | 被 Windows 拒绝，未生效 |
| Windows“使用 Windows 更新修复问题” | 已完成并重启，未改变任务栏样式 |
| 临时启用官方策略 SearchOnTaskbarMode=3 并重启资源管理器 | 策略成功写入但视觉状态未改变 |

上述临时策略已删除，当前不存在该策略。

## 当前结论与边界

这是当前 Windows 26200 Shell 对搜索显示选项未正确应用的问题；目前没有证据表明 D 盘应用卷、百度网盘或当前仍存在的 AppX 包损坏导致它。

当前选择是保留仅搜索图标，不再做风险性修复。不要：

- 回写 AppRepository / StateRepository 数据库；
- 重新注册或还原旧版 Microsoft.StartExperiencesApp；
- 为此问题反复重装、重启或修改 D 盘应用卷。

如未来必须再次尝试，应先以新 Windows 累积更新后的实际行为为准，再进行一次可逆、单项验证。

## 证据位置

- AppRepository 变更前备份：D:\backups\AppRepository-before-manual-baidu-20260814-134000
- 清理结果：manual-offline-batch-cleanup-result.json
- 本次用户搜索设置备份：`%TEMP%\Codex-Search-before-reset-20260814.reg`
