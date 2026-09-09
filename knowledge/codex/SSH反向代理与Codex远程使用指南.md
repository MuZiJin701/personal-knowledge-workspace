# SSH 反向代理与 Codex 远程使用指南

## 推荐方案

在本机保留两套 SSH 配置：

- 普通配置：用于 PowerShell、VS Code 等日常登录。
- 带转发配置：供 Codex App 使用，同时把服务器的代理请求转发到本机 Clash。

## 本机 SSH 配置

编辑本机文件：

```text
%USERPROFILE%\.ssh\config
```

加入以下内容，并把 `你的服务器IP` 替换为实际地址：

```sshconfig
# 普通 SSH 登录
Host aliyun_wlcb
    HostName 你的服务器IP
    User root
    Port 22
    IdentityFile ~/.ssh/id_ed25519
    IdentitiesOnly yes

# SSH 登录并提供反向代理
Host aliyun_wlcb_proxy
    HostName 你的服务器IP
    User root
    Port 22
    IdentityFile ~/.ssh/id_ed25519
    IdentitiesOnly yes

    RemoteForward 127.0.0.1:7897 127.0.0.1:7897
    ExitOnForwardFailure yes
    ServerAliveInterval 30
    ServerAliveCountMax 3
```

其中：

- `7897` 是服务器上的代理入口端口。
- `7897` 是本机 Clash 的 HTTP 或 Mixed 代理端口。
- 如果你的 Clash 端口不是 `7897`，需要改成实际端口。

## 如何使用

普通登录服务器：

```powershell
ssh aliyun_wlcb
```

登录并建立代理转发：

```powershell
ssh aliyun_wlcb_proxy
```

Codex App 连接服务器时，应选择：

```text
aliyun_wlcb_proxy
```

需要再打开 PowerShell 或 VS Code 时，使用普通配置 `aliyun_wlcb`，避免重复占用代理端口。

## 代理如何工作

带转发的 SSH 连接建立后，网络路径是：

```text
服务器 127.0.0.1:7897
        ↓ SSH 反向转发
本机 Clash 127.0.0.1:7897
```

只有明确使用服务器 `127.0.0.1:7897` 代理的程序才会经过本机，服务器的其他流量不会自动走代理。

本机 `127.0.0.1:7897` 也可以接收 Codex App 自身 SSH 流量。此时存在两条不同的 TCP 连接进入同一个本机监听端口：

```text
Codex App SSH 流量 → 本机 127.0.0.1:7897 → Clash
服务器 Codex 代理流量 → 服务器 127.0.0.1:7897 → RemoteForward → 本机 127.0.0.1:7897 → Clash
```

两条连接共用本机监听端口，但不是同一条 TCP 连接。

如果服务器已经在 `~/.codex/.env` 中配置了这个代理，建立转发后可直接启动：

```bash
codex
```

## 端口占用错误

如果出现：

```text
remote port forwarding failed for listen port 7897
```

通常表示另一条 SSH 连接已经占用了服务器的 `7897` 端口。

这不代表服务器只能打开一个 SSH 会话。可以同时存在：

- 一条带 `7897` 转发的 SSH 连接；
- 多条不带该转发的普通 SSH 连接。

不要同时让 Codex App 和 PowerShell 都使用 `aliyun_wlcb_proxy`。额外登录请使用 `aliyun_wlcb`。

## 连接与 Codex 的生命周期

代理隧道的生命周期与承载它的 SSH 连接相同：

- Codex App 建立并保持 SSH 连接时，服务器代理可用。
- 关闭 App 后，如果它的 SSH 连接也关闭，服务器上的 `7897` 代理入口随之消失。
- 服务器上已经运行的 Codex 进程不一定立即退出，但之后通常无法通过该代理联网。
- 本机 Clash 退出、电脑休眠或网络断开，也会导致代理不可用。

因此，可以近似理解为“打开 Codex App 时可用，关闭后不可用”，但严格来说，代理跟随的是 SSH 连接，而不是 Codex 进程本身。

## 三个连接参数

### `ExitOnForwardFailure yes`

如果反向端口转发创建失败，就终止 SSH 连接。这样可以避免出现“SSH 登录成功，但 Codex 实际没有代理”的情况。

### `ServerAliveInterval 30`

SSH 客户端每隔 30 秒向服务器发送一次存活检测，用于及时发现断线，也可减少空闲连接被网络设备清理的概率。

### `ServerAliveCountMax 3`

连续 3 次存活检测没有得到响应后，SSH 客户端结束连接。

配合前一个参数，可以简单理解为：连接失去响应约 90 秒后，SSH 判定连接已经断开。实际时间可能略有差异。

## 日常使用建议

| 场景 | 使用的配置 |
|---|---|
| Codex App 连接服务器并使用本机代理 | `aliyun_wlcb_proxy` |
| PowerShell 普通登录 | `aliyun_wlcb` |
| VS Code Remote SSH | `aliyun_wlcb` |
| 其他额外 SSH 会话 | `aliyun_wlcb` |

核心原则：同一时间只保留一条使用服务器 `7897` 端口的转发连接。
