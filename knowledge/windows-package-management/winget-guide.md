# WinGet 知识与常用命令

WinGet 是微软的 Windows 命令行软件管理工具：用命令搜索、安装、升级和卸载软件。通常随“应用安装程序”（App Installer）提供，可在 PowerShell 或 CMD 中使用。[官方介绍](https://learn.microsoft.com/en-us/windows/package-manager/winget/)

核对日期：2026-10-07；常用参数已对照本机 WinGet v1.29.380 的帮助信息。

## 1. 先理解三个概念

| 概念 | 含义 | 示例 |
| --- | --- | --- |
| 软件 ID | 区分软件的标识，安装时比名称更准确 | `Microsoft.VisualStudioCode` |
| 软件源 | 提供软件清单的目录服务 | `winget` 社区源、`msstore` 微软商店源 |
| 安装器 | 真正执行安装的程序，决定路径和权限等行为 | EXE、MSI、MSIX |

软件源可用 `winget source list` 查看；WinGet 的安装选项是否有效，取决于软件提供的安装器。[软件源](https://learn.microsoft.com/en-us/windows/package-manager/winget/source)、[安装说明](https://learn.microsoft.com/en-us/windows/package-manager/winget/install)

### 软件源列表怎么看

`winget source list` 列出当前配置的软件源；已安装软件则用 `winget list` 查看。用户于 2026-10-07 提供的输出如下，与官方文档中的默认源配置一致：

```text
名称        参数                                          显式
---------------------------------------------------------------
msstore     https://storeedgefd.dsx.mp.microsoft.com/v9.0 false
winget      https://cdn.winget.microsoft.com/cache        false
winget-font https://cdn.winget.microsoft.com/fonts        true
```

| 名称 | 用途 | 显式 |
| --- | --- | --- |
| `msstore` | Microsoft Store 应用目录 | `false` |
| `winget` | WinGet 社区软件目录 | `false` |
| `winget-font` | WinGet 社区字体目录 | `true` |

“参数”是源的服务地址。“显式”（Explicit）表示是否必须在使用该源的命令中明确指定它：`false` 表示未指定源时默认纳入查询；`true` 表示需要使用 `--source <源名称>`（或 `-s <源名称>`）才会纳入。`true` 不表示源被禁用，`false` 也不表示源不可用。

例如，查询字体源中的字体：

```powershell
winget search --source winget-font
```

仅凭这份列表无需调整源配置；它说明源已配置，不能证明网络访问正常或软件一定能安装。[官方 source 命令说明](https://learn.microsoft.com/en-us/windows/package-manager/winget/source)

## 2. 常用命令速查

以下以 VS Code 为例；操作其他软件时，将 ID 换成搜索结果中的 ID。

| 要做什么 | 命令 |
| --- | --- |
| 查看 WinGet 版本 | `winget --version` |
| 查看环境、设置和日志目录 | `winget --info` |
| 查看命令帮助 | `winget install --help` |
| 搜索软件 | `winget search vscode` |
| 查看软件详情 | `winget show --id Microsoft.VisualStudioCode -e` |
| 安装软件 | `winget install --id Microsoft.VisualStudioCode -e` |
| 查看已安装软件 | `winget list` |
| 查询某个已安装软件 | `winget list --id Microsoft.VisualStudioCode -e` |
| 查看可升级软件 | `winget upgrade` |
| 升级指定软件 | `winget upgrade --id Microsoft.VisualStudioCode -e` |
| 批量升级可更新的软件 | `winget upgrade --all` |
| 卸载指定软件 | `winget uninstall --id Microsoft.VisualStudioCode -e` |
| 查看软件源 | `winget source list` |
| 刷新软件源信息 | `winget source update` |
| 打开 WinGet 设置 | `winget settings` |

`winget list` 也能列出通过其他方式安装的软件。`winget source update` 刷新软件目录；`winget upgrade` 不带参数时只查看，带 ID 或 `--all` 才执行升级。[已安装软件](https://learn.microsoft.com/en-us/windows/package-manager/winget/list)、[升级](https://learn.microsoft.com/en-us/windows/package-manager/winget/upgrade)、[卸载](https://learn.microsoft.com/en-us/windows/package-manager/winget/uninstall)

## 3. 最常用的操作流程

安装前，先搜索并查看详情，再按 ID 精确安装：

```powershell
winget search vscode --source winget
winget show --id Microsoft.VisualStudioCode -e --source winget
winget install --id Microsoft.VisualStudioCode -e --source winget
```

日常更新时，先运行 `winget upgrade` 查看清单，再选择更新单个软件或运行 `winget upgrade --all`。版本未知或被固定的软件可能跳过，不代表所有软件都会更新。[安装](https://learn.microsoft.com/en-us/windows/package-manager/winget/install)、[升级](https://learn.microsoft.com/en-us/windows/package-manager/winget/upgrade)

## 4. 常用安装参数

参数加在安装命令末尾，按需要选用：

| 参数 | 作用 |
| --- | --- |
| `--id 软件ID` | 按软件 ID 查找 |
| `-e` / `--exact` | 精确匹配，避免模糊匹配到其他软件 |
| `--source winget` | 只从指定源查找 |
| `--scope user` | 请求只为当前用户安装，需安装器支持 |
| `--scope machine` | 请求为整台电脑安装，通常需要管理员权限 |
| `--location "D:\Apps\VSCode"` | 请求安装到指定目录，需安装器支持 |
| `--interactive` | 显示安装界面，手动选择选项 |
| `--silent` | 请求静默安装，权限或协议确认仍可能出现 |

例如，尝试指定安装目录：

```powershell
winget install --id Microsoft.VisualStudioCode -e --location "D:\Apps\VSCode"
```

WinGet 不能强制所有软件使用指定路径。如果安装器不支持，可尝试 `--interactive` 手动选择。普通安装不必预先打开管理员终端，按软件需要处理权限提示。[安装参数](https://learn.microsoft.com/en-us/windows/package-manager/winget/install)

## 5. 导出清单、迁移电脑

在原电脑导出软件清单：

```powershell
winget export -o winget-apps.json
```

把文件复制到新电脑，在文件所在目录执行：

```powershell
winget import -i winget-apps.json
```

默认不记录版本，导入时安装可用的最新版本；需要记录版本时，导出加 `--include-versions`。无法与软件源匹配的软件可能无法导出。清单只记录待安装软件，个人文件和软件设置需要另行备份。[导出](https://learn.microsoft.com/en-us/windows/package-manager/winget/export)、[导入](https://learn.microsoft.com/en-us/windows/package-manager/winget/import)

## 6. 命令异常时先检查入口

在 PowerShell 中查看系统实际调用哪个程序：

```powershell
Get-Command winget -All
where.exe winget
```

如果先出现旧脚本或失效路径，检查 PATH 中的同名入口。使用 Windows 自带安装版本时，可直接验证应用执行别名：

```powershell
& "$env:LOCALAPPDATA\Microsoft\WindowsApps\winget.exe" --version
```

别名位于 `%LOCALAPPDATA%\Microsoft\WindowsApps\winget.exe`。实际程序所在的 App Installer 版本目录会随更新变化；脚本中应优先使用 `winget` 命令或有效别名，避免写死版本目录。

相关：[WinGet 与 Scoop 对比](Windows%20winget与Scoop知识和常用命令.md)。
