# 本机 Node.js 与 npm 环境记录

> 更新日期：2026-08-20。内容来自本机 Scoop、fnm、Node.js 和 npm 的只读检查。

## 当前结论

- 当前 PowerShell 优先使用 Scoop 的 `nodejs-lts`：Node.js `24.19.0`、npm `12.0.2`。
- 本机当前只安装并启用了 Scoop 的 LTS 版，以及 fnm 管理的两个版本。
- 不同 Node.js 版本拥有独立的 npm 全局目录；同名工具可能因此出现多个版本。
- `npm update -g` 只更新当前 npm 全局目录中的包，不更新 Node.js 本身。

## 已安装的 Node.js 环境

| 管理方式 | Node.js | npm | 全局 npm 目录 | 当前状态 |
| --- | ---: | ---: | --- | --- |
| Scoop `nodejs-lts` | 24.19.0 | 12.0.2 | `D:\software\scoop\persist\nodejs-lts\bin` | 当前 PATH 优先 |
| fnm | 22.23.1 | 10.9.8 | fnm 安装目录 | 已安装 |
| fnm | 24.14.0 | 11.9.0 | fnm 安装目录 | fnm 默认版本 |

fnm 本身为 `1.39.0`。

## 各 npm 环境安装的全局工具

### Scoop `nodejs-lts` / Node.js 24.19.0

- `@earendil-works/pi-coding-agent@0.83.0`
- `@wecom/cli@0.1.9`
- `skills@1.5.23`

### fnm / Node.js 22.23.1

- 没有额外全局工具。

### fnm / Node.js 24.14.0

- 没有额外全局工具。

## 已移除的 Scoop Current 版

Scoop 的 `nodejs` Current 版（此前为 Node.js `26.5.1`、npm `11.17.0`）已不在安装清单和 PATH 中。以下目录仍存在：

```text
D:\software\scoop\persist\nodejs\bin
```

该目录是旧的 npm 全局数据残留，当前没有对应的 `node.exe`/`npm.cmd`，也不应视为可用的 Node.js 环境。当前直接执行 `node`、`npm` 和 `skills` 均使用 Scoop LTS 环境。

## 使用指定 Node.js 的 npm

PowerShell 中直接调用目标 `npm.cmd` 最可靠：

```powershell
# Scoop LTS
& 'D:\software\scoop\apps\nodejs-lts\current\npm.cmd' ls -g --depth=0
& 'D:\software\scoop\apps\nodejs-lts\current\npm.cmd' install -g <工具名>

# fnm 24.14.0
& 'D:\software\scoop\persist\fnm\node-versions\v24.14.0\installation\npm.cmd' ls -g --depth=0
```

切换 fnm 版本后，也可以直接使用该版本的 npm：

```powershell
fnm use 24.14.0
node --version
npm --version
npm config get prefix
```

查看当前 npm 的全局位置：

```powershell
npm config get prefix
npm root -g
npm ls -g --depth=0
```

## `npm update -g` 输出的含义

- `changed 150 packages`：当前全局目录中的依赖已更新，命令成功完成。
- `deprecated node-domexception@1.0.0`：某个传递依赖已被上游弃用，不等于当前工具立即不可用。本机它位于 `pi-coding-agent` 的 Google GenAI 依赖链中。
- `packages are looking for funding`：仅为开源项目赞助提示。
- `allow-scripts`：`@google/genai` 和 `protobufjs` 含安装脚本，npm 提示它们尚未被脚本审批记录覆盖。这是供应链安全提示，不是更新失败；工具运行正常时不必立即处理。

不要为消除提示而盲目批准所有安装脚本。需要排查时先查看依赖关系：

```powershell
npm ls -g @google/genai protobufjs --all
npm explain -g node-domexception
```

## skills CLI

当前 LTS 环境中的 `skills` 版本为 `1.5.23`。更新全局安装的 skills：

```powershell
skills update -g
skills update -g -y
skills list -g
```

skill 来源和版本记录在：

```text
%USERPROFILE%\.agents\.skill-lock.json
```

当前记录涉及的 GitHub 仓库包括：

- [mattpocock/skills](https://github.com/mattpocock/skills)
- [vercel-labs/agent-skills](https://github.com/vercel-labs/agent-skills)
- [WeComTeam/wecom-cli](https://github.com/WeComTeam/wecom-cli)
- [MuZiJin701/zen-computer-marketplace](https://github.com/MuZiJin701/zen-computer-marketplace)
- [vercel-labs/skills](https://github.com/vercel-labs/skills)
- [MuZiJin701/wx-video-account-notes](https://github.com/MuZiJin701/wx-video-account-notes)

旧的 `unify-windows-theme` 记录已从锁文件清理。
