# Windows winget 与 Scoop 知识和常用命令

## 1. winget 是什么

`winget` 是 Windows 官方命令行包管理器，用于搜索、安装、升级和卸载软件，定位类似 Linux 的 `apt`。

它通常调用软件原生的 `.exe`、`.msi` 或 MSIX 安装程序，因此软件覆盖面较广，但安装路径和安装行为可能由具体安装程序决定。

## 2. winget 常用命令

### 搜索和查看

```powershell
winget search <关键词>                 # 搜索软件
winget show --id <软件ID> -e           # 查看软件详情
winget list                            # 查看已安装软件
winget source list                     # 查看软件源
```

### 安装、升级和卸载

```powershell
winget install --id <软件ID> -e        # 按 ID 精确安装
winget install <软件名>                # 按名称安装
winget upgrade                         # 查看可升级软件
winget upgrade --all                   # 升级全部软件
winget uninstall --id <软件ID> -e      # 卸载软件
```

常用示例：

```powershell
winget install --id Git.Git -e
winget install --id Microsoft.VisualStudioCode -e
```

使用 `--id <ID> -e` 比模糊名称匹配更可靠。软件 ID 可通过 `winget search <关键词>` 或 `winget show` 获取。

### 指定安装路径

```powershell
winget install --id Git.Git -e --location "D:\Apps\Git"
```

`--location` 只有在对应安装程序支持时才有效；`winget` 不能强制所有软件使用指定目录。

如果软件安装器有自己的路径参数，可以通过 `--override` 传入：

```powershell
winget install --id <软件ID> -e --override "/DIR=D:\Apps\软件"
```

具体参数由软件安装器决定，常见安装器参数并不完全相同。需要手动选择路径时，可使用：

```powershell
winget install --id <软件ID> -e --interactive
```

### 软件源和环境迁移

```powershell
winget source list                    # 查看源
winget source update                   # 更新源
winget export -o winget.json          # 导出软件清单
winget import -i winget.json          # 按清单恢复
```

安装过程中如需自动同意协议，可附加：

```powershell
--accept-package-agreements --accept-source-agreements
```

## 3. Scoop 是什么

Scoop 是 Windows 上的第三方命令行软件管理器，尤其适合开发工具、命令行工具和便携版软件。

主要特点：

- 通常按当前用户安装，不需要管理员权限；
- 软件集中放在 Scoop 目录中，路径统一；
- 多数软件通过解压和 `shims` 使用，较少修改注册表；
- 通过 Bucket 管理软件清单和来源；
- 支持普通安装和全局安装。

## 4. Scoop 常用命令

```powershell
scoop search <关键词>                  # 搜索软件
scoop info <软件名>                    # 查看软件信息
scoop install <软件名>                 # 安装软件
scoop list                             # 查看已安装软件
scoop status                           # 查看可更新软件
scoop update                           # 更新 Scoop
scoop update <软件名>                  # 更新指定软件
scoop update *                         # 更新全部软件
scoop uninstall <软件名>              # 卸载软件
scoop cleanup                          # 清理旧版本
scoop prefix <软件名>                  # 查看安装目录
scoop reset <软件名>                   # 重建软件链接
scoop export > scoopfile.json          # 导出软件清单
scoop import scoopfile.json            # 恢复软件清单
```

Bucket 操作：

```powershell
scoop bucket list
scoop bucket add extras
scoop bucket rm extras
```

## 5. 自定义 Scoop 安装路径

安装 Scoop 时可以直接指定目录：

```powershell
irm get.scoop.sh -outfile install.ps1
.\install.ps1 -ScoopDir "D:\Applications\Scoop" `
              -ScoopGlobalDir "D:\Applications\ScoopGlobal"
```

也可以在安装前设置环境变量：

```powershell
$env:SCOOP = "D:\Applications\Scoop"
$env:SCOOP_GLOBAL = "D:\Applications\ScoopGlobal"
irm get.scoop.sh | iex
```

普通安装和全局安装：

```powershell
scoop install git
scoop install git --global
```

默认目录通常是：

```text
C:\Users\<用户名>\scoop
```

## 6. winget 与 Scoop 的区别

| 对比项 | winget | Scoop |
| --- | --- | --- |
| 定位 | Windows 官方包管理器 | 第三方开发者包管理器 |
| 安装方式 | 调用原生安装器 | 通常下载并解压软件包 |
| 路径控制 | 取决于软件安装器 | 统一、容易自定义 |
| 软件范围 | 常规桌面软件覆盖较广 | 命令行和开发工具体验好 |
| 权限 | 某些软件需要管理员权限 | 普通用户安装较方便 |
| 系统改动 | 可能修改注册表、服务和系统目录 | 通常较少修改系统；非便携软件除外 |
| 软件来源 | 配置的软件源，如 `winget`、`msstore` | Bucket 仓库 |
| 典型用途 | 安装 Chrome、微信、IDE 等桌面软件 | 管理 Git、Python、Node.js、CLI 工具 |

## 7. 如何选择

- 安装常规桌面软件：优先使用 `winget`；
- 需要统一放在 D 盘、少改系统、管理开发工具：优先使用 Scoop；
- 两者可以同时使用，但不要让它们重复管理同一个软件；
- 安装前确认软件 ID、来源和发布者，尤其是同名软件。

## 8. 注意事项

- `winget --location` 不是对所有软件都有效；最终行为由安装器支持情况决定；
- `winget --override` 的参数格式取决于具体安装器；
- Scoop 的软件主要来自 Bucket，安装前应确认来源可信；
- Scoop 的 `nonportable` 软件可能修改系统设置或需要管理员权限；
- 修改环境变量或安装路径后，通常需要重新打开 PowerShell。

## 9. 官方资料

- [Microsoft winget 安装命令](https://learn.microsoft.com/windows/package-manager/winget/install)
- [Microsoft Windows Package Manager 文档](https://learn.microsoft.com/windows/package-manager/)
- [Scoop 官方仓库](https://github.com/ScoopInstaller/Scoop)
- [Scoop 安装器与高级安装](https://github.com/ScoopInstaller/Install)
- [Scoop 命令参考](https://github.com/ScoopInstaller/Scoop/wiki/Commands)
