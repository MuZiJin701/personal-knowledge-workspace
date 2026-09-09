# Windows 应用安装位置与 D 盘系统目录

## 结论

Windows 有两套应用安装方式：

- Microsoft Store、Xbox、MSIX/AppX：由 Windows 管理，通常使用 `WindowsApps`、`WpSystem` 等受保护目录。
- 普通桌面软件：通过 `.exe`、`.msi`、Scoop 或 winget 安装，可以自行选择 D 盘目录。

## 当前状态（2026-08-14）

- 唯一 AppX 应用卷和默认卷均为 C:\Program Files\WindowsApps。
- D:\WindowsApps、D:\WpSystem、D:\Program Files\ModifiableWindowsApps 均不存在。
- RestrictAppToSystemVolume=1 已启用，因此新的 Store/Xbox/MSIX 应用只能安装到 C 盘。
- D 盘可继续用于普通桌面软件、项目和下载；这不影响上述策略。

## D 盘常见目录

| 目录 | 作用 |
| --- | --- |
| `WindowsApps` | Store/MSIX 应用主体文件 |
| `WpSystem` | Store 应用的用户数据、配置和缓存 |
| `Program Files\ModifiableWindowsApps` | Xbox/Store 可修改应用目录 |
| `DeliveryOptimization` | Windows 更新和 Store 下载缓存 |
| `WUDownloadCache` | Windows Update 临时下载缓存 |

这些目录出现在 D 盘，通常说明 D 盘曾被设为 Windows 应用保存位置或注册为外部 AppX 应用卷。

## 推荐配置：应用集中到 C，普通软件可放 D

如果希望 D 盘保持整洁：

1. 将“设置 → 系统 → 存储 → 高级存储设置 → 保存新内容的位置 → 新的应用将保存到”设为 `C:`。
2. 将 Microsoft Store/Xbox 应用移动到 C，或卸载后重新安装到 C。
3. 普通 `.exe`、`.msi` 软件安装到例如：

   ```text
   D:\software\应用名
   ```

4. 不要把普通软件安装到 `D:\WindowsApps`、`D:\WpSystem` 或 `D:\Program Files`。

## 防止 Store 应用再次使用 D

管理员 PowerShell：

```powershell
$policy = 'HKLM:\SOFTWARE\Policies\Microsoft\Windows\Appx'
New-Item -Path $policy -Force | Out-Null
New-ItemProperty -LiteralPath $policy `
  -Name RestrictAppToSystemVolume -PropertyType DWord -Value 1 -Force | Out-Null

Set-AppxDefaultVolume -Volume 'C:\Program Files\WindowsApps'
```

这会限制 Microsoft Store/Xbox/MSIX 应用只能安装到 C 盘，因此设置页面可能不再允许选择 D 盘。

> 该策略只限制新的安装位置，不能自动清除已遗留的 AppX 注册。

## 恢复 Store 应用的多盘选择

如果希望再次主动选择 D 盘安装 Store/Xbox 应用，在管理员 PowerShell 执行：

```powershell
Remove-ItemProperty `
  -LiteralPath 'HKLM:\SOFTWARE\Policies\Microsoft\Windows\Appx' `
  -Name RestrictAppToSystemVolume `
  -ErrorAction SilentlyContinue

Set-AppxDefaultVolume -Volume 'C:\Program Files\WindowsApps'
```

但只要选择 D 盘，Windows 可能重新创建 `WindowsApps`、`WpSystem` 和相关目录。以下目标不能同时满足：

- Store/Xbox 应用可以安装到 D 盘；
- D 盘完全不出现 Windows 应用系统目录。

## 清理原则

- 不直接复制移动 `WindowsApps` 或 `WpSystem` 到 C 盘。
- 应用使用系统“移动”功能，或卸载后重新安装到 C 盘。
- 缓存使用 Windows 自带清理功能；删除后系统可能自动重建空目录。
- 删除前先确认没有应用仍安装在 D 盘。
- `Remove-AppxVolume` 报 `0x80073D0C` 表示 Windows 仍认为该 AppX 卷不是空的；不要据此强删正在使用的应用包。
- 普通软件放在 D 盘不会触发 `WindowsApps` 体系；D 盘可以继续保存项目、下载、Scoop 和传统桌面软件。

## 移除 D 盘 AppX 卷的注意

- 移除 D 盘 AppX 卷是高风险操作：即使目录看似为空，仍可能留下脱机包注册并在开机触发弹窗。
- 不要为了“保持 D 盘干净”而重新添加空卷或强行移除仍被引用的卷。
- 不把 AppRepository / StateRepository 的数据库备份当作日常恢复手段；只在保留完整备份、明确范围和用户授权的极端排障中使用。

## 常用检查命令

```powershell
Get-AppxDefaultVolume |
  Select-Object PackageStorePath, IsSystemVolume

Get-AppxVolume |
  Select-Object PackageStorePath, IsSystemVolume, IsOffline

Get-AppxPackage -AllUsers |
  Where-Object { $_.InstallLocation -like 'D:\WindowsApps\*' } |
  Select-Object Name, PackageFullName, InstallLocation
```

## 相关排障记录

- [百度网盘旧 AppX 包脱机弹窗排障记录](百度网盘旧AppX包脱机弹窗排障记录.md)
- [Windows 任务栏搜索框未生效排障记录](Windows任务栏搜索框未生效排障记录.md)
