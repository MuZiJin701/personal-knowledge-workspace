# Node.js 生态与 npm 知识和常用命令

## 生态结构

```text
Node.js 运行时
    └─ npm 客户端
        ├─ package.json：项目依赖、脚本和元数据
        ├─ package-lock.json：精确依赖锁定
        ├─ node_modules：实际安装的包
        └─ npm registry：下载和发布包的仓库
```

- Node.js：运行 JavaScript 的环境，包含 V8 引擎和文件、网络、进程等 API。
- npm：Node.js 生态最常用的包管理器和命令行工具。
- 包：可复用的代码单元；包可以继续依赖其他包，形成依赖树。
- CLI 工具：通常通过 npm 全局安装；项目运行时依赖通常安装到项目本地。

## 本地依赖与全局工具

### 本地安装：项目优先

```powershell
npm install <package>                 # dependencies
npm install -D <package>              # devDependencies
npm install                            # 按 package.json 安装
npm ci                                 # 严格按 package-lock.json 安装
```

本地包安装到当前项目的 `node_modules`，并记录在 `package.json` / `package-lock.json`。项目脚本会优先使用本地版本，适合保证团队和 CI 环境一致。

### 全局安装：只用于 CLI

```powershell
npm install -g <cli-package>
npm ls -g --depth=0
npm uninstall -g <cli-package>
npm update -g
```

全局包属于当前 Node.js/npm 环境。切换 Node.js 版本后，全局包列表可能完全不同；使用 `npm config get prefix` 查看其归属目录。

## 版本范围

常见语义化版本格式为 `MAJOR.MINOR.PATCH`：

- `MAJOR`：可能包含不兼容变更。
- `MINOR`：通常增加向后兼容功能。
- `PATCH`：通常修复问题。

常见范围：

- `1.2.3`：固定版本。
- `^1.2.3`：允许兼容的次版本和补丁版本。
- `~1.2.3`：通常只允许补丁版本更新。
- `latest`：使用 registry 的最新稳定标签，不代表最适合当前项目。

## 常用查询命令

```powershell
node --version                       # Node.js 版本
npm --version                        # npm 版本
where.exe node                       # PATH 中所有 node.exe
Get-Command node -All                # PowerShell 中查看命令解析顺序
npm config get registry              # 当前 registry
npm config get prefix                # 全局安装目录
npm root -g                          # 全局 node_modules
npm list                             # 当前项目依赖树
npm ls -g --depth=0                  # 全局一级依赖
npm outdated                         # 查看可更新依赖
npm view <package> version           # registry 中的最新版本
npm view <package> versions --json   # 所有发布版本
```

## 安装、更新和卸载

```powershell
npm install <package>
npm install <package>@<version>
npm install -D <package>
npm update <package>
npm uninstall <package>

npm install -g <cli>@latest
npm update -g <cli>
npm uninstall -g <cli>
```

`npm update -g` 更新的是当前 npm 全局目录，不更新 Node.js；升级 Node.js 应使用 Scoop、fnm 或其他 Node 版本管理器。

## npm scripts 和 npx

在 `package.json` 中定义脚本：

```json
{
  "scripts": {
    "dev": "node src/index.js",
    "test": "node --test"
  }
}
```

运行脚本：

```powershell
npm run dev
npm test
```

一次性调用项目本地或 registry 中的 CLI：

```PowerShell
npx <cli> <args>
npm exec -- <cli> <args>
```

优先使用项目本地 CLI，避免依赖某个机器上的全局版本。

## 安装脚本与安全

依赖可能声明 `preinstall`、`install`、`postinstall` 或 `prepare` 脚本。它们可能编译原生模块、生成文件，也可能执行任意命令，因此安装第三方包时应注意来源和权限。

npm 11 可能提示包的安装脚本尚未被 `allowScripts` 审批记录覆盖。提示本身不一定表示安装失败；先确认依赖是否正常，再针对可信包审批，不要无条件批准所有脚本。

```powershell
npm ls -g <package> --all            # 查看依赖关系
npm explain -g <package>             # 说明某个包为何被安装
npm audit                            # 检查已知漏洞
npm audit fix                        # 让 npm 尝试修复兼容范围内的问题
```

## 缓存、锁文件和清理

```powershell
npm cache verify                     # 校验缓存
npm cache clean --force              # 谨慎使用，通常不需要
Remove-Item -Recurse -Force node_modules
npm ci
```

删除 `node_modules` 后用 `npm ci` 重建，适合锁文件可信且需要干净安装的场景。不要随意删除 `package-lock.json`，否则可能引入大量依赖版本变化。

## Node.js 版本管理

常见选择：

- LTS：稳定、维护周期长，适合作为默认版本。
- Current：较早获得新特性，适合测试运行时兼容性。
- fnm：在多个 Node.js 版本间切换。

```powershell
fnm list
fnm use 24.14.0
node --version
npm --version
```

使用多个 Node.js 版本时，先确认 `node --version`、`npm --version` 和 `npm config get prefix`，再安装全局工具。

## 常见排障顺序

1. 确认命令来自哪个版本：`Get-Command node,npm -All`。
2. 确认版本：`node --version`、`npm --version`。
3. 确认全局目录：`npm config get prefix`、`npm root -g`。
4. 确认包是否存在：`npm ls -g --depth=0` 或 `npm ls <package>`。
5. 查看依赖来源：`npm explain <package>`。
6. 项目依赖异常时，保留锁文件，删除 `node_modules`，再执行 `npm ci`。

## 官方资料

- [Node.js](https://nodejs.org/)
- [npm CLI 文档](https://docs.npmjs.com/cli/)
- [npm package.json 说明](https://docs.npmjs.com/cli/v11/configuring-npm/package-json)
