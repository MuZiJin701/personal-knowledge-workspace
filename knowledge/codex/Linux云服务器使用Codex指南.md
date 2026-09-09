# Linux 云服务器使用 Codex 指南

本文适用于以下情况：

- Codex CLI 安装在 Linux 云服务器上；
- 你从 Windows 电脑通过 SSH 连接服务器；
- 服务器不能直接访问 Codex 服务；
- 本机 Clash Verge Rev 的代理端口为 `7897`；
- SSH 主机别名为 `aliyun_wlcb`。

## 一、确认本机 Clash 代理可用

在 Windows PowerShell 中运行：

```powershell
curl.exe -x http://127.0.0.1:7897 https://api.ipify.org
```

如果返回一个公网 IP，说明 Clash 代理可用。

## 二、建立 SSH 代理隧道

在本机 PowerShell 中运行：

```powershell
ssh -NT -R 127.0.0.1:7897:127.0.0.1:7897 aliyun_wlcb
```

含义：把服务器的 `127.0.0.1:7897` 通过 SSH 连接到本机 Clash 的 `127.0.0.1:7897`。

这个 PowerShell 窗口必须保持运行。关闭窗口、退出 Clash 或电脑休眠后，代理都会断开。

## 三、在服务器验证隧道

新开一个 PowerShell，连接服务器：

```powershell
ssh aliyun_wlcb
```

在服务器中运行：

```bash
curl -x http://127.0.0.1:7897 https://api.ipify.org
```

如果返回 Clash 节点的出口 IP，说明隧道成功。

## 四、只为 Codex 设置代理

Codex 会自动读取 `~/.codex/.env`。服务器当前使用 `root` 用户，因此实际路径是 `/root/.codex/.env`。

打开配置文件：

```bash
nano ~/.codex/.env
```

粘贴以下内容：

```dotenv
HTTP_PROXY=http://127.0.0.1:7897
HTTPS_PROXY=http://127.0.0.1:7897
ALL_PROXY=http://127.0.0.1:7897
http_proxy=http://127.0.0.1:7897
https_proxy=http://127.0.0.1:7897
all_proxy=http://127.0.0.1:7897
NO_PROXY=127.0.0.1,localhost
no_proxy=127.0.0.1,localhost
```

保存方法：

1. 按 `Ctrl+O`；
2. 按回车确认；
3. 按 `Ctrl+X` 退出。

限制文件权限：

```bash
chmod 600 ~/.codex/.env
```

这个文件只由 Codex 自动读取，不会让整台服务器的所有流量都经过本机。但 Codex 启动的子进程也可能继承这些代理变量。

## 五、登录 Codex

云服务器没有图形界面，应使用设备码登录：

```bash
codex login --device-auth
```

终端会显示登录网址和验证码。在本机浏览器打开该网址并完成授权。

不要只运行 `codex login`，否则可能使用 `localhost` 回调，导致本机浏览器显示“连接被拒绝”。

检查登录状态：

```bash
codex login status
```

## 六、启动 Codex

确保本机 SSH 隧道仍在运行，然后在服务器执行：

```bash
codex
```

## 七、以后每次使用

1. 启动本机 Clash Verge Rev；
2. 在本机运行并保持以下命令：

   ```powershell
   ssh -NT -R 127.0.0.1:7897:127.0.0.1:7897 aliyun_wlcb
   ```

3. 新开终端登录服务器：

   ```powershell
   ssh aliyun_wlcb
   ```

4. 在服务器启动 Codex：

   ```bash
   codex
   ```

## 常见问题

### 浏览器显示 `localhost` 拒绝连接

取消当前登录：

```text
Ctrl+C
```

然后改用：

```bash
codex login --device-auth
```

### 服务器的代理端口连接失败

检查本机的 SSH 隧道窗口是否仍在运行，并在服务器检查端口：

```bash
ss -lnt | grep 7897
```

### 提示找不到 bubblewrap

如果提示 Codex 将使用内置 bubblewrap，这通常只是警告，不影响继续使用。需要安装系统版本时，应先用以下命令确认 Linux 发行版：

```bash
cat /etc/os-release
```

再使用该发行版对应的软件包管理器安装。
