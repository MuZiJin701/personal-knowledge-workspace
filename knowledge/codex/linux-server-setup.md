# 新 Linux 云服务器基础环境配置

这篇带你把一台新服务器准备好：能用 SSH 登录、能联网，并装好常用开发工具。完成后再看[第二篇：使用 Codex](Linux云服务器使用Codex指南.md)。

适用环境：Windows PowerShell + Ubuntu 24.04 + Bash。更新：2026-10-17。

本方案用于服务器直接访问部分海外服务不通、需要借用 Windows Clash 的情况。**首次安装时，用 Windows Terminal（WT）手工建立代理；日常使用时，再交给 Codex App 管理。**普通 WT 连接仍可同时使用。

## 开始前先看这几项

| 文中写法 | 你需要知道的意思 |
| --- | --- |
| `SERVER_IP` | 替换成服务器的实际 IP 地址。 |
| `INITIAL_USER` | 替换成云平台提供的初始用户名。 |
| `dev` | 日常使用的 Linux 用户；已有普通用户就用自己的名字。 |
| `dev-server` / `dev-server-proxy` | 稍后设置的 SSH 连接名称，分别用于普通登录和带代理登录。 |
| `~` | 当前用户的主目录；Windows 的 `$env:USERPROFILE` 也是用户主目录。 |

每段命令前会标明在 **Windows** 还是 **Linux** 执行。按步骤运行，遇到报错先停下来处理，不要一次粘贴整篇。

## 1. 检查 Windows SSH 和密钥

SSH 用来从 Windows 登录服务器。先在 **Windows PowerShell** 检查：

```powershell
ssh -V
Test-Path "$env:USERPROFILE\.ssh\id_ed25519*"
```

第一条应显示 SSH 版本。第二条返回 `True` 表示已有相关密钥文件，先检查能否复用；`False` 表示没有。找不到 `ssh` 时，在 Windows“可选功能”中安装 OpenSSH 客户端。

已有密钥就复用，**不要覆盖**。确认没有可用密钥后，才执行：

```powershell
ssh-keygen -t ed25519 -C "dev-windows"
```

`-t ed25519` 选择密钥类型，`-C` 后面是备注。保存位置可接受默认值，并按提示设置私钥口令。

生成后有两个文件：

- `id_ed25519`：私钥，留在 Windows，不上传服务器、手机或仓库。
- `id_ed25519.pub`：公钥，稍后放到服务器上。

服务器用公钥确认你的身份，不需要你的私钥。[原理说明](https://man.openbsd.org/sshd.8#AUTHORIZED_KEYS_FILE_FORMAT)

## 2. 首次登录并准备系统

在 **Windows PowerShell** 登录，先替换用户名和 IP：

```powershell
ssh INITIAL_USER@SERVER_IP
```

用云平台提供的密码或已有密钥登录。首次连接时，通过云平台控制台等可信渠道核对服务器指纹；不要跳过身份检查。若连接不上，检查 IP、SSH 端口和云平台安全组。

进入服务器后，以下命令都在 **Linux** 执行：

```bash
cat /etc/os-release   # 查看系统版本
uname -m             # 查看处理器架构
whoami               # 查看当前用户名
```

确认是 Ubuntu 24.04，再安装工具。`sudo` 表示用管理员权限执行；如果当前用户是 root，去掉开头的 `sudo` 即可。

```bash
sudo apt update

# 下载、HTTPS 证书和解压工具
sudo apt install curl ca-certificates unzip

# Git、文本编辑器、目录和网络查看工具
sudo apt install git micro tree iproute2

# Python 和虚拟环境支持
sudo apt install python3 python3-venv
```

`apt update` 只刷新软件清单；需要升级系统时，另行执行 `sudo apt upgrade` 并检查升级内容。一次只运行一个 APT 任务，不要强行中断安装。

后面编辑文件会用到 `micro`：**Ctrl+S 保存，Ctrl+Q 退出**。更多命令见 [APT](../command-line/apt知识和常用命令.md)、[编辑器](../command-line/micro、mcedit与helix：终端编辑器使用指南.md)和 [Git](../command-line/Git知识和常用命令.md)。

## 3. 确定日常用户并安装公钥

### 先准备普通用户

建议日常用普通用户运行开发工具和 Codex。已有能使用 sudo 的普通用户，直接复用，跳过创建步骤。

如果目前只有 root，在 **Linux root 终端**执行。`dev` 已存在时不要重复创建：

```bash
adduser dev           # 创建用户，按提示设置密码
usermod -aG sudo dev  # 允许这个用户使用 sudo
su - dev             # 切换到这个用户
```

用户密码用于 sudo 等授权；它和 Windows 私钥口令是两回事。[Ubuntu 用户管理说明](https://ubuntu.com/server/docs/how-to/security/user-management/)

### 把 Windows 公钥放到这个用户下面

在 **Windows PowerShell** 复制公钥：

```powershell
Get-Content "$env:USERPROFILE\.ssh\id_ed25519.pub" | Set-Clipboard
```

`Get-Content` 读取文件，`| Set-Clipboard` 把内容放进剪贴板。

回到 **Linux 普通用户终端**：

```bash
whoami                         # 应显示日常用户，例如 dev
mkdir -p ~/.ssh                 # 创建 SSH 配置目录，已有目录会保留
chmod 700 ~/.ssh                # 只允许自己访问这个目录
micro ~/.ssh/authorized_keys    # 打开公钥列表
```

把公钥完整追加为一行，保留原有内容。保存退出后：

```bash
chmod 600 ~/.ssh/authorized_keys  # 只允许自己读写这个文件
ls -ld ~/.ssh ~/.ssh/authorized_keys
sudo -v                          # 确认自己能使用 sudo
```

`ls` 输出中的所有者应是当前用户。公钥放在 root 目录下，不会让 `dev` 自动获得登录权限。

**保留原连接**，在新的 **Windows PowerShell** 测试公钥登录：

```powershell
ssh -o PreferredAuthentications=publickey dev@SERVER_IP
```

这里的 `-o` 指定 SSH 选项，要求只用公钥登录。进入服务器后，运行 `whoami` 和 `sudo -v`；两项都正常，再关闭原管理连接。本文不修改 root 登录或密码登录策略。

如果云平台已经把同一公钥下发给这个用户，不必重复添加。平台导入、绑定和重启要求以[厂商说明](https://docs.volcengine.com/docs/ecs/Binding-key-pairs-to-instances?lang=zh)为准；绑定后仍要实际测试登录。

## 4. 按需修改主机名

主机名是 Linux 自己的名字，不会同时改变云平台实例名或 SSH 连接名称。不需要修改时跳过本节。

在 **Linux** 执行：

```bash
sudo hostnamectl set-hostname dev-server
hostnamectl
```

第二条输出中应能看到新名字，重新登录后再看终端提示。检查 `/etc/hosts` 中是否还有旧主机名，修改时保留 `localhost`。重启后名字恢复的处理见末尾排障。[命令说明](https://www.freedesktop.org/software/systemd/man/latest/hostnamectl.html)

## 5. 配置普通 SSH 与代理 SSH

连接名称可以让你以后直接输入 `ssh dev-server`，不用每次写用户名和 IP。

在 **Windows PowerShell** 打开 SSH 配置文件：

```powershell
notepad "$env:USERPROFILE\.ssh\config"
```

把下面配置添加进去，保留已有内容。替换 `SERVER_IP` 和 `dev`；SSH 不是 22 端口时，两处 `Port` 也要修改。文件名是 `config`，不要保存成 `config.txt`。

```sshconfig
Host dev-server
    HostName SERVER_IP
    User dev
    Port 22
    IdentityFile ~/.ssh/id_ed25519
    IdentitiesOnly yes

Host dev-server-proxy
    HostName SERVER_IP
    User dev
    Port 22
    IdentityFile ~/.ssh/id_ed25519
    IdentitiesOnly yes
    RemoteForward 127.0.0.1:7897 127.0.0.1:7897
    ExitOnForwardFailure yes
    ServerAliveInterval 30
    ServerAliveCountMax 3
```

两套配置都能登录同一服务器；后半段还会建立一条反向代理通道：

```text
使用代理的服务器程序 → 服务器 7897 → SSH 通道 → Windows 7897 → Clash → 目标服务
```

`RemoteForward` 左边是服务器入口，右边是 Windows Clash 入口。这里要求 Clash 的 **HTTP 或 Mixed 端口为 7897**；实际端口不同就修改对应一端。

`ExitOnForwardFailure` 表示通道建不起来就退出；`ServerAlive` 两项用于检测断线，不会自动重连。[配置说明](https://man.openbsd.org/ssh_config.5#RemoteForward)

按使用阶段选择连接：

| 连接方式 | 用途 |
| --- | --- |
| WT 中执行 `ssh dev-server` | 普通登录；不另设代理时，程序继续使用服务器原网络，适合日常管理和国内服务。 |
| WT 中执行 `ssh dev-server-proxy` | 登录并建立反向转发，用于首次安装，或不用 App 时手工提供代理。 |
| Codex App 选择 `dev-server-proxy` | 日常由 App 建立和管理带反向转发的 SSH 连接，不必另留一个代理终端。 |

**同一个服务器的 `127.0.0.1:7897`，只让一个连接负责监听。**App 和 WT 不要重复建立这条转发；普通 SSH 会话可以同时存在，多个程序也能共用已有代理入口。

反向转发只是提供入口，不会自动让所有程序走代理。普通 SSH 中的程序若设置了代理，也能使用这个入口；不设代理时，仍按原网络访问。经过 Clash 的请求则由 Clash 规则决定出口。

两台机器使用同号端口不会冲突。保持 `127.0.0.1` 绑定，不开放公网 7897，也不必开启 Clash“允许局域网”；服务器本机其他用户仍可能使用这个入口。

## 6. 验证链路并设置临时下载代理

这里的“临时代理”主要用于**还没装好 Codex、还没配置 `.env` 时，先下载服务器无法直接下载的软件**，例如 fnm、uv 和 Codex 安装包。以后独立下载工具有需要时，也能按同样方式临时启用。

服务器能直接访问所需服务时，可跳过本节。需要代理时，先启动 **Windows Clash**，确认 HTTP/Mixed 端口，再在 **WT 的 PowerShell** 中执行：

```powershell
Get-NetTCPConnection -State Listen -LocalPort 7897
ssh dev-server-proxy
```

第一条应显示监听记录；第二条登录服务器并建立通道，**安装阶段保持这个窗口在线**。此时不要让 App 同时建立同端口转发。

在连接后的 **Linux** 终端设置：

```bash
export http_proxy=http://127.0.0.1:7897
export https_proxy=http://127.0.0.1:7897
export no_proxy=localhost,127.0.0.1,::1
```

`export` 给当前终端启动的程序设置环境变量：前两条指定代理，第三条让访问本机的请求绕过代理。**需要“通道已建立”和“程序使用代理”两项同时满足。**`https_proxy` 使用 `http://` 是因为连接的是 HTTP 代理，目标 HTTPS 仍会校验证书。

测试一次实际下载：

```bash
curl -fL --max-time 30 https://github.com -o /dev/null
```

后面下载命令会重复用到这些参数：

| 参数 | 意思 |
| --- | --- |
| `-fL` | 遇到 HTTP 错误时报错；网址跳转时继续跟随。 |
| `--max-time 30` | 最多等待 30 秒。 |
| `-o 文件路径` | 下载到指定文件；`/dev/null` 表示不保存，只测试下载。 |

命令正常结束且无报错，说明本次下载通过。其他网址、安装包和模型服务仍需分别测试。变量只对当前终端及之后启动的程序有效，不会改变其他普通 WT 会话；sudo/APT 也不一定使用这些变量。[curl 代理说明](https://everything.curl.dev/usingcurl/proxies/env.html)

不用时可执行 `unset http_proxy https_proxy no_proxy`；其他位置已有的代理设置仍可能生效。不必把这几行写进全局启动配置。Codex 安装后保存自己的代理，再[交给 App 管理连接](Linux云服务器使用Codex指南.md#5-用-windows-桌面-app-连接服务器)，日常不用重复执行临时 `export`。

## 7. 安装 fnm、Node.js 和 npm

Node.js 是运行 JavaScript 的工具，npm 用来安装相关软件；fnm 帮你安装和切换 Node.js 版本。本指南使用 [Node.js 24 LTS](https://nodejs.org/en/about/previous-releases)，npm 会一起装好。

以下都在 **Linux 普通用户终端**执行，需要代理时先完成上一节。

先下载 fnm 安装脚本：

```bash
fnm_script=$(mktemp)
curl -fL --max-time 60 https://fnm.vercel.app/install -o "$fnm_script"
```

`mktemp` 创建临时文件，`fnm_script` 记住它的路径，避免覆盖已有文件。下载无报错后，可用 `micro "$fnm_script"` 查看内容，再执行：

```bash
bash "$fnm_script"   # 运行安装脚本
source ~/.bashrc     # 让当前终端加载新配置
fnm --version        # 应显示 fnm 版本
```

fnm 安装成功后，安装并选用 Node.js 24：

```bash
fnm install 24   # 安装 24 系列
fnm use 24       # 当前终端使用它
fnm default 24   # 新终端默认使用它
node --version
npm --version
```

最后两条应显示版本号。不要重复追加 shell 配置，也不要再用 APT 安装另一套 npm。[fnm 安装说明](https://github.com/Schniz/fnm#installation)

以后更新 24 系列，重跑 `install`、`use`、`default` 三条即可。切换 Node.js 后，全局安装的软件可能需要重装。已有 nvm 或其他 Node.js 安装时，先查 `type -a node npm`，不要盲目覆盖。

## 8. 安装 uv，保留系统 Python

uv 用来管理 Python 项目和依赖。它不依赖 Node.js，也不需要替换系统 Python。

在 **Linux 普通用户终端**下载脚本：

```bash
uv_script=$(mktemp)
curl -fL --max-time 60 https://astral.sh/uv/install.sh -o "$uv_script"
```

下载无报错后，可用 `micro "$uv_script"` 查看，再安装：

```bash
sh "$uv_script"                     # 运行安装脚本
source "$HOME/.local/bin/env"        # 让当前终端找到新命令
uv --version
uvx --version
python3 --version
```

应显示各自版本。默认命令目录为 `~/.local/bin`，自定义目录时按安装器提示操作。以后用 `uv self update` 更新；若原来用 pipx 等方式安装，继续用原方式更新。[uv 安装说明](https://docs.astral.sh/uv/getting-started/installation/)

## 9. 新会话验收

安装阶段保持手工代理窗口，在 **WT** 新开 PowerShell，用普通连接检查：

```powershell
ssh dev-server
```

在新连接的 **Linux** 终端检查：

```bash
whoami          # 应是日常普通用户
sudo -v         # 确认管理员授权可用
hostnamectl     # 查看主机名

git --version
fnm --version
node --version
npm --version
python3 --version
uv --version
uvx --version
```

新连接中仍能看到版本号，说明工具配置已生效。使用代理时，再执行：

```bash
curl --proxy http://127.0.0.1:7897 -fL --max-time 30 https://github.com -o /dev/null
```

`--proxy` 让这一条命令使用代理；这也说明普通 SSH 可以共用另一条连接建立的入口。它不改变当前终端其他命令的联网方式。下载正常后，就可以继续[安装和使用 Codex](Linux云服务器使用Codex指南.md)。

## 10. 按失败阶段排障

| 问题 | 先这样检查 |
| --- | --- |
| SSH 连不上 | IP、SSH 端口、安全组和服务器是否在线。 |
| 公钥登录被拒绝 | 用户名、公钥是否放对用户、`.ssh` 和 `authorized_keys` 的权限及所有者。用 `ssh -v dev-server` 查看原因，分享日志前去掉个人信息。 |
| 提示 7897 转发失败 | Linux 用 `ss -lntp` 查看监听端口，找 `:7897` 的占用。App 和 WT 只能选一个建立这条转发，其他连接用普通别名；不结束未知进程。没有占用时再查服务端转发权限。 |
| 通道在，但下载失败 | Windows Clash 是否运行，端口、协议和规则是否正确。纯 SOCKS 端口不能照搬 HTTP 配置。 |
| fnm 脚本地址打不开 | 将下载命令中的网址换成官方备用地址 `https://raw.githubusercontent.com/Schniz/fnm/master/.ci/install.sh`，其余不变。下载成功后再安装。 |
| curl 提示 `(6)` / `(28)` | 分别是域名解析失败 / 超时；换目标或比较直连与代理，逐步定位。 |
| curl 提示 `(35)` / `(60)` | 检查 TLS、系统时间和证书，不用 `-k` 跳过证书检查。 |
| HTTP 403 | 请求被拒绝，需查看具体原因；不能单凭它认定地区限制。 |
| 重连后找不到工具 | 先查登录用户和 shell 配置，不要急着重装。App 找不到 Codex 时看第二篇排障。 |
| 主机名重启后恢复 | 检查 cloud-init。需要保留自定义名称时，在 `/etc/cloud/cloud.cfg.d/` 的配置片段中设置 `preserve_hostname: true`，并检查 `/etc/hosts` 的管理方式。[官方说明](https://docs.cloud-init.io/en/latest/reference/modules.html) |
| APT 升级提示配置冲突 | 未比较 SSH 配置差异前，保留当前版本；键盘布局常见美式 QWERTY 选 `English (US)`。重启前确认安装结束、业务可中断且有控制台恢复入口。 |

比较网络时，固定同一网址：用 `curl --noproxy '*'` 强制直连，用 `curl --proxy http://127.0.0.1:7897` 指定代理，再加上前面下载测试的参数。脚本网址可用不等于安装包也能下载；域名解析结果不同也不一定是故障，不随意修改系统 DNS 或 hosts。[curl 参数说明](https://curl.se/docs/manpage.html)

Windows 睡眠、断网、Clash 退出或承载转发的 SSH 断开都会影响这个代理。交给 App 管理后，关注 App 的远端连接状态；关闭聊天或窗口不一定等于连接已断开。恢复后检查并重新测试。

## 参考与验证说明

更多背景按需阅读：[Node.js/npm](../node-js/Node.js生态与npm知识和常用命令.md)、[uv/Python](../python/uv基础知识、常用命令与Python项目管理.md)、[环境变量](../command-line/Linux与Windows环境变量设置.md)。

官方资料已在 2026-10-17 合并时核对。旧稿记录曾分阶段验证 SSH、工具链和代理；本次只调整文档，没有在新服务器执行安装或重启。请按第九节自行验收。

[第二篇：Linux 云服务器使用 Codex](Linux云服务器使用Codex指南.md) · [返回索引](../../INDEX.md) · [公众号历史材料](公众号发布素材/发布说明.md)
