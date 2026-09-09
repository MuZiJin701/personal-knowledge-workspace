# Codex 三位一体基础知识

## 文档定位

本文档记录这套架构中稳定、可复用的连接关系、端口约定和排障入口。
具体结构图与验收要求见 [Codex三位一体使用架构说明.md](Codex三位一体使用架构说明.md)。

## 架构要点

- 手机通过本地 Codex App 远程操作云服务器上的 Codex 会话。
- 本地电脑承载 Codex App、SSH 连接和 Clash Verge Rev。
- 云服务器承载 Codex 的代码开发、运行、测试和部署。
- 代理节点位于本机 Clash Verge Rev 之外；OpenAI 是代理链路的目标。

## 端口与 SSH

两端各自使用本机回环地址 `127.0.0.1:7897`：

- 云服务器上的 Codex 使用云端 `127.0.0.1:7897` 作为代理地址。
- Codex 专用 SSH 使用反向端口转发，将云端端口连接到本机 `127.0.0.1:7897`。
- 本机 Clash Verge Rev 只监听并转发本机 `127.0.0.1:7897` 的流量。

本机保留两套 SSH 配置：

- `aliyun_wlcb_proxy`：供 Codex App 使用，包含 `RemoteForward`。
- `aliyun_wlcb`：供 PowerShell、VS Code 和其他普通登录使用，不包含端口转发。

端口职责：

- 普通 SSH：本机随机临时端口连接云服务器 TCP `22`，不占用云端 `7897`。
- Codex 专用 SSH：同样连接云服务器 TCP `22`，并额外让云端监听 `127.0.0.1:7897`。
- 本机 Clash：监听本机 `127.0.0.1:7897`。
- 同一时间只能有一条反向转发连接监听云端 `127.0.0.1:7897`；普通 SSH 会话可以继续共用云端 TCP `22`。

本机 `127.0.0.1:7897` 是同一个监听端口，但可以接收两条不同的 TCP 连接：

- 本机 Codex App 的 SSH 流量：Codex App → 本机 `7897` → Clash Verge Rev。
- 云服务器 Codex 的代理流量：云端 `7897` → `RemoteForward` → 本机 `7897` → Clash Verge Rev。

推荐的反向转发配置：

```sshconfig
RemoteForward 127.0.0.1:7897 127.0.0.1:7897
ExitOnForwardFailure yes
ServerAliveInterval 30
ServerAliveCountMax 3
```

Codex 专用 SSH 是一条双向的加密 TCP 会话。Mermaid 图中的 `<-->` 表示同一条 SSH 会话的双向流量，不表示两条独立连接。`RemoteForward` 只规定云端 `127.0.0.1:7897` 由哪一端监听，并利用这条双向连接把请求和响应转到本机 `127.0.0.1:7897`。普通 SSH 只用于远程登录、VS Code Remote SSH 等场景，不提供该反向代理。

## 代理流量路径

本机 Codex App 的 SSH 流量：

```text
Codex App
→ 本机 127.0.0.1:7897
→ Clash Verge Rev
→ 代理节点
→ OpenAI
```

云服务器 Codex 的代理流量：

```text
云服务器 Codex
→ 云端 127.0.0.1:7897
→ Codex 专用 SSH 反向转发
→ 本机 127.0.0.1:7897
→ Clash Verge Rev
→ 代理节点
→ OpenAI
```

两端的 `7897` 属于不同主机，不构成端口冲突；冲突风险主要来自同一时间建立多条占用云端 `127.0.0.1:7897` 的反向转发连接。

## 代理环境变量

服务器 Codex 可使用以下代理地址：

```dotenv
HTTP_PROXY=http://127.0.0.1:7897
HTTPS_PROXY=http://127.0.0.1:7897
ALL_PROXY=http://127.0.0.1:7897
NO_PROXY=127.0.0.1,localhost
```

## 排障顺序

1. 确认本地电脑联网且 Codex App、Clash Verge Rev 正在运行。
2. 确认本机 Clash Verge Rev 正在监听 `127.0.0.1:7897`。
3. 确认 Codex 专用 SSH 保持连接，且云端 `127.0.0.1:7897` 反向转发成功。
4. 确认服务器 Codex 的代理变量指向云端 `127.0.0.1:7897`。
5. 若仍无法联网，检查是否存在重复的反向转发连接或代理节点不可用。

## 维护边界

- 本文档是项目唯一的基础知识文档，记录稳定知识，不复制完整任务说明或长日志。
- 架构图、连接关系和验收表述维护在 `Codex三位一体使用架构说明.md`；图片布局不作为固定约束。
- 用户要求查看结构图时，直接输出架构说明文档中的 Mermaid 图代码供预览。
- 当前以 Mermaid 图为实现目标；除非用户明确要求，不生成或更新 SVG、PNG。
