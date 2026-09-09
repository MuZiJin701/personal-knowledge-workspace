# APT 知识和常用命令

## 1. APT 是什么

APT（Advanced Package Tool）是 Debian、Ubuntu 等系统的软件包管理工具，用于从软件源安装、升级、查询和卸载软件，并自动处理依赖关系。

日常使用通常需要管理员权限，因此命令前常加 `sudo`。

## 2. 常用命令

### 软件源与升级

```bash
sudo apt update                    # 更新软件包列表，不安装软件
sudo apt upgrade                   # 升级已安装的软件
sudo apt full-upgrade              # 升级并在必要时调整依赖，可能卸载软件
```

### 安装与卸载

```bash
sudo apt install nginx             # 安装软件
sudo apt install nginx git curl    # 一次安装多个软件
sudo apt remove nginx              # 卸载软件，通常保留配置文件
sudo apt purge nginx               # 卸载软件及其配置文件
sudo apt autoremove                # 删除不再需要的依赖
```

### 搜索与查看

```bash
apt search nginx                   # 搜索软件包
apt show nginx                     # 查看软件包信息
apt list --installed               # 列出已安装的软件包
apt list --upgradable              # 列出可升级的软件包
apt policy nginx                   # 查看版本、来源和候选版本
```

### 缓存与故障检查

```bash
sudo apt clean                     # 清理全部下载缓存
sudo apt autoclean                 # 清理已无法下载的旧缓存
sudo apt check                     # 检查依赖关系是否正常
```

## 3. 最常用流程

```bash
sudo apt update
sudo apt install 软件名
```

升级系统：

```bash
sudo apt update && sudo apt upgrade
```

## 4. 使用要点

- `apt update` 只更新软件包列表，不会升级软件。
- `remove` 通常保留配置；需要连配置一起删除时使用 `purge`。
- 执行 `full-upgrade`、`purge` 和 `autoremove` 前，先查看将要删除的内容。
- 交互式操作优先使用 `apt`；脚本中通常使用 `apt-get`，因为其行为和输出更适合脚本兼容性。
- 软件源配置通常位于 `/etc/apt/sources.list` 和 `/etc/apt/sources.list.d/`。

## 5. 一句话记忆

```text
update 更新列表，install 安装，upgrade 升级，remove 卸载，purge 连配置一起删，autoremove 清理无用依赖。
```

## 参考

- [Debian apt(8) 手册](https://manpages.debian.org/trixie/apt/apt.8.en.html)
- [Debian apt-get(8) 手册](https://manpages.debian.org/trixie/apt/apt-get.8.en.html)
