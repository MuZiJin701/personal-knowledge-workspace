# Windows Scoop 软件管理器与常用命令

## 1. Scoop 是什么

Scoop 是 Windows 的命令行软件安装与管理工具，定位类似 macOS 的 Homebrew。它适合安装开发工具、命令行工具和便携版软件。

Scoop 的主要特点：

- 通过命令安装、更新和卸载软件；
- 自动处理软件依赖；
- 优先使用便携版软件，减少注册表和系统目录污染；
- 默认按当前用户安装，通常不需要管理员权限；
- 可以把安装命令保存为脚本，重复配置开发环境。

Scoop 使用 JSON manifest 描述软件的下载、解压、依赖和安装步骤。

## 2. 安装 Scoop

在普通 PowerShell 中执行：

```powershell
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser

irm get.scoop.sh -outfile install.ps1
.\install.ps1
```

默认安装位置是：

```text
C:\Users\<用户名>\scoop
```

指定安装到 `D:\software\scoop`：

```powershell
irm get.scoop.sh -outfile install.ps1
.\install.ps1 -ScoopDir 'D:\software\scoop'
```

如需指定全局软件目录：

```powershell
.\install.ps1 `
  -ScoopDir 'D:\software\scoop' `
  -ScoopGlobalDir 'D:\software\scoop-global'
```

也可以在安装前设置环境变量：

```powershell
$env:SCOOP = 'D:\software\scoop'
$env:SCOOP_GLOBAL = 'D:\software\scoop-global'
irm get.scoop.sh | iex
```

## 3. 重要概念

### 普通安装与全局安装

```powershell
scoop install git
scoop install git --global
```

- 普通安装：安装到当前用户的 Scoop 目录；
- `--global`：安装到全局目录，供多个用户使用，通常需要管理员权限。

示例中的：

```powershell
scoop install sudo
sudo scoop install 7zip git openssh --global
```

先安装 Scoop 的 `sudo` 工具，再用它临时提升权限执行全局安装。`sudo` 不是 Scoop 内置命令，而是一个可安装的软件包。

### Bucket（软件源）

Bucket 是存放软件 manifest 的 Git 仓库。`main` 默认启用，`extras` 提供更多桌面软件。

```powershell
scoop bucket list
scoop bucket add extras
scoop bucket rm extras
```

常见 Bucket：

| Bucket | 内容 |
| --- | --- |
| `main` | 常用命令行工具，默认启用 |
| `extras` | 更多桌面应用 |
| `games` | 游戏及相关工具 |
| `nerd-fonts` | Nerd Fonts 字体 |
| `java` | JDK、JRE 和 Java 工具 |
| `sysinternals` | Microsoft Sysinternals 工具 |

### Aria2

`aria2` 是多连接下载工具。安装后，Scoop 可以使用它下载软件：

```powershell
scoop install aria2
scoop config aria2-warning-enabled false
```

第二条命令只关闭 aria2 已启用时的提示，不会关闭 aria2。

| 配置 | 默认值 | 含义 |
| --- | --- | --- |
| `aria2-enabled` | `true` | 是否使用 aria2 下载；下载异常时可设为 `false`，改用 Scoop 默认下载器。 |
| `aria2-warning-enabled` | `true` | 是否显示 aria2 已启用的提示。 |
| `aria2-retry-wait` | `2` | 重试下载前等待的秒数。 |
| `aria2-split` | `5` | 单个文件最多的并行下载连接数。 |
| `aria2-max-connection-per-server` | `5` | 对同一服务器最多使用的连接数。 |
| `aria2-min-split-size` | `5M` | 最小分段尺寸；文件小于其两倍时不分段下载。 |
| `aria2-options` | 空 | 额外传给 aria2 的原始参数；一般无需设置。 |

通常保持默认值即可。调大并行连接不一定更快，也可能触发服务器限流。

## 4. 常用命令

### 搜索和查看

```powershell
scoop search <关键词>       # 搜索软件
scoop info <软件名>         # 查看软件信息
scoop list                  # 查看已安装软件
scoop status                # 查看可更新软件
scoop home <软件名>         # 打开软件主页
scoop prefix <软件名>       # 查看软件安装目录
scoop which <命令>          # 查找命令实际位置
```

### 安装、更新和卸载

```powershell
scoop install <软件名>      # 安装软件
scoop install a b c         # 批量安装
scoop update                # 更新 Scoop
scoop update <软件名>       # 更新指定软件
scoop update *              # 更新所有软件
scoop uninstall <软件名>   # 卸载软件
scoop cleanup               # 清理旧版本
scoop hold <软件名>         # 暂停该软件的更新
scoop unhold <软件名>       # 恢复更新
```

### 配置和帮助

```powershell
scoop help                  # 查看所有帮助
scoop help <命令>           # 查看指定命令帮助
scoop config                # 查看配置
scoop config <键> <值>      # 设置配置
scoop checkup               # 检查潜在问题
```

### 缓存和环境重置

```powershell
scoop cache show            # 查看下载缓存
scoop cache rm <软件名>     # 删除指定软件缓存
scoop cache rm *            # 清空下载缓存
scoop reset <软件名>       # 重建软件链接，解决命令冲突
```

### 导出和恢复环境

```powershell
scoop export > scoopfile.json
scoop import scoopfile.json
```

这适合备份当前软件清单，或在另一台电脑上恢复相近的环境。

## 5. 常用开发环境示例

下面的命令可以保存为 PowerShell 脚本，用于重复配置环境：

```powershell
scoop install sudo
sudo scoop install 7zip git openssh --global
scoop install aria2 curl grep sed less touch
scoop install python ruby go perl
```

含义：

- `7zip`、`git`、`openssh`：压缩、版本控制和 SSH 工具；
- `aria2`、`curl`：下载和网络请求工具；
- `grep`、`sed`、`less`、`touch`：常见文本和文件操作工具；
- `python`、`ruby`、`go`、`perl`：开发语言运行环境。

## 6. 常见注意事项

- Scoop 主要面向 PowerShell；执行安装脚本前要确认执行策略允许运行脚本。
- 并非所有软件都是便携版；`nonportable` 等软件可能修改系统设置或需要管理员权限。
- 软件来源由 Bucket 提供，安装前应确认软件包和来源可信。
- `--global` 只影响软件安装范围，不会自动把普通安装转换成全局安装。
- Scoop 通过 `shims` 将软件命令加入 PATH；安装后若命令未生效，可重新打开 PowerShell。

## 7. 官方资料

- [Scoop 官方仓库](https://github.com/ScoopInstaller/Scoop)
- [Scoop 安装器与高级安装](https://github.com/ScoopInstaller/Install)
- [Scoop 命令参考](https://github.com/ScoopInstaller/Scoop/wiki/Commands)
- [Scoop 文件夹布局](https://github.com/ScoopInstaller/Scoop/wiki/Scoop-Folder-Layout)
