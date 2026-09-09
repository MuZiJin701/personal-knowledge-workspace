# 百度网盘旧 AppX 包脱机弹窗排障记录

## 问题

每次重启后弹出：

> 无法打开这个应用，因为它处于脱机状态。可能缺少存储设备，或者存储设备已断开连接。

## 当前状态（2026-08-14）

- 已完成针对遗留注册的清理；当前开机未再出现该弹窗。
- 当前启动后的验证未发现 CLSID `{499C8928-90D7-411D-8DBD-E35B92529123}` 对应的 DistributedCOM `10001` 事件。
- D 盘版普通百度网盘仍保留在 `D:\software\winget-apps\BaiduNetdisk`。
- 已为该程序添加开始菜单快捷方式；搜索“百度网盘”可启动 `BaiduNetdisk.exe`，不需要重装或重启。

## 历史根因

- 开机后 `System` 日志出现 DistributedCOM 事件 `10001`。
- CLSID：`{499C8928-90D7-411D-8DBD-E35B92529123}`。
- 错误 `2147958106`（`0x80073D5A`），表示 AppX 包不可用。
- 该 CLSID 属于旧包 `BaiduNetdisk.DesktopSyncClient_7.17.5.6_neutral__r5kxaep58dem0` 的资源管理器右键菜单扩展。
- 注册库仍把该包登记为位于：

  ```text
  C:\Program Files\WindowsApps\BaiduNetdisk.DesktopSyncClient_7.17.5.6_neutral__r5kxaep58dem0
  ```

  但该目录和包文件不存在，因此包状态为 `PackageOffline, DataOffline, NotAvailable`。

这是移除 D 盘 AppX 应用卷后遗留的损坏注册；不是 D 盘断开，也不是 Defender 或电脑管家导致。

## 该旧包与当前百度网盘的关系

旧包是“稀疏 AppX 包”：清单在 `BaiduNetdisk.msix`，右键菜单 DLL 依赖百度网盘安装目录中的外部内容。当前安装中仍可见：

```text
D:\software\winget-apps\BaiduNetdisk\sysres\YunShellCommand64.dll
```

但旧 AppX 的 C 盘包目录已经缺失。新版百度网盘主程序可正常存在于 D 盘；问题仅是这个历史 AppX 壳的残留注册。

## 已执行且无效的安全修复

| 操作 | 结果 |
| --- | --- |
| `Remove-AppxPackage -AllUsers` | `0x80073CFA`：无法卸载 |
| `Reset-AppxPackage` | 同样为 `0x80073CFA` |
| `Add-AppxPackage` 重新注册 MSIX | `0x80073CF9`，内部错误 `0x80070002`（文件不存在） |
| `DISM /Online /Cleanup-Image /RestoreHealth` | 已完成，不要求重启 |
| `sfc /scannow` | 未发现需修复的受保护系统文件 |

因此，系统组件本身正常；AppX 部署数据库中的这条孤立注册无法由单包命令自行恢复或移除。

## 后续边界

- 不要手工编辑 `AppModel\StateRepository`、`AppRepository` 或其数据库。
- 不要手工删除、复制或接管 `C:\Program Files\WindowsApps` 的权限。
- 不要再重置 Defender、电脑管家或随意删除 D 盘的普通百度网盘目录；这些都不是根因。
- 不要为恢复旧包而回写 AppRepository / StateRepository 备份。该类手改曾连带移除旧的开始菜单体验包，见 [Windows任务栏搜索框未生效排障记录](Windows任务栏搜索框未生效排障记录.md)。
- 如将来再次出现脱机弹窗，先以事件中的 CLSID 和包名精确定位；不要对整批 AppX 包或应用卷进行手改。

## 已过时的结论

本文早期版本曾将“不要编辑部署数据库”列为未执行边界。后续在用户明确授权下确实进行了带完整备份的实际库清理，弹窗随后消失；这不是可推广的常规修复，只是历史事实。不要据此重复手改。

## 验证命令

重启后，在管理员 PowerShell 运行：

```powershell
$boot = (Get-CimInstance Win32_OperatingSystem).LastBootUpTime

Get-WinEvent -FilterHashtable @{ LogName = 'System'; StartTime = $boot.AddMinutes(-1) } |
  Where-Object {
    $_.Id -eq 10001 -and
    $_.ProviderName -like '*DistributedCOM*' -and
    $_.Message -match '499C8928-90D7-411D-8DBD-E35B92529123|2147958106'
  } |
  Select-Object TimeCreated, Id, Message
```

没有输出才表示这条开机弹窗的根因已清除。
