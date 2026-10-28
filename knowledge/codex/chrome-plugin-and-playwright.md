# Chrome 插件原理与 Playwright 全局使用

核查日期：2026-10-18。本文区分官方源码证据、本机实现证据与尚未验证的推断。Playwright 源码固定于提交 [`b335271cdc2b57cac2be8ad37c5dd283ac6b8938`](https://github.com/microsoft/playwright/tree/b335271cdc2b57cac2be8ad37c5dd283ac6b8938)，不代表本机任何包恰好使用该版本。

## Playwright 的控制链路

常规 Node.js 库调用 Chromium 的路径可概括为：

```text
JavaScript API / Locator
  → Playwright client channel
  → server dispatcher / 浏览器适配实现
  → Chromium CDP 命令
  → pipe 或 WebSocket
  → Chromium / Chrome
```

这里有两个不同的协议层：Playwright 的 client/server channel 与面向 Chromium 的 CDP。普通 Node.js 本地使用时，client 和 server 可以在同一个 Node.js 进程内交换消息，不能把 channel 误画成必然存在的网络服务或独立进程。[inprocess.ts](https://github.com/microsoft/playwright/blob/b335271cdc2b57cac2be8ad37c5dd283ac6b8938/packages/playwright-core/src/inprocess.ts#L27-L49)

以点击为例，`Locator.click()` 调用 frame，frame 经 `_channel.click()` 发出请求，`FrameDispatcher.click()` 转发到服务端实现；Chromium 输入适配器最终使用 `Input.dispatchMouseEvent` 等 CDP 命令。API 的等待和定位逻辑在底层输入命令之前执行，不等同于直接调用 DOM 的 `element.click()`。[Locator](https://github.com/microsoft/playwright/blob/b335271cdc2b57cac2be8ad37c5dd283ac6b8938/packages/playwright-core/src/client/locator.ts#L122-L124)、[Frame channel](https://github.com/microsoft/playwright/blob/b335271cdc2b57cac2be8ad37c5dd283ac6b8938/packages/playwright-core/src/client/frame.ts#L321-L323)、[dispatcher](https://github.com/microsoft/playwright/blob/b335271cdc2b57cac2be8ad37c5dd283ac6b8938/packages/playwright-core/src/server/dispatchers/frameDispatcher.ts#L146-L148)、[Chromium input](https://github.com/microsoft/playwright/blob/b335271cdc2b57cac2be8ad37c5dd283ac6b8938/packages/playwright-core/src/server/chromium/crInput.ts#L123-L165)

Chromium 的 `CRConnection` 为命令添加 `id`、`method`、`params` 和可选 `sessionId`，交给 transport；本地默认启动参数含 `--remote-debugging-pipe`，通用启动器建立 `PipeTransport`。因此使用 CDP 不表示必须开放 TCP 调试端口。[CRConnection](https://github.com/microsoft/playwright/blob/b335271cdc2b57cac2be8ad37c5dd283ac6b8938/packages/playwright-core/src/server/chromium/crConnection.ts#L67-L76)、[Chromium 默认参数](https://github.com/microsoft/playwright/blob/b335271cdc2b57cac2be8ad37c5dd283ac6b8938/packages/playwright-core/src/server/chromium/chromium.ts#L355-L364)、[transport 选择](https://github.com/microsoft/playwright/blob/b335271cdc2b57cac2be8ad37c5dd283ac6b8938/packages/playwright-core/src/server/browserType.ts#L274-L280)

## 已有浏览器与其他引擎

- Playwright 可以通过 `chromium.connectOverCDP()` 附着已启动、提供 CDP 接入点的 Chrome；HTTP 接入点会被解析为 WebSocket。它不只会启动新浏览器。[官方 API](https://playwright.dev/docs/api/class-browsertype#browser-type-connect-over-cdp)、[实现](https://github.com/microsoft/playwright/blob/b335271cdc2b57cac2be8ad37c5dd283ac6b8938/packages/playwright-core/src/server/chromium/chromium.ts#L84-L115)
- `browserType.connect()` 连接的是 Playwright 服务的 WebSocket，区别于连接 Chrome CDP WebSocket 的 `connectOverCDP()`。官方说明后者的功能保真度较低；自行启动 Chrome 时，启动参数差异也可能影响功能。[官方 API](https://playwright.dev/docs/api/class-browsertype#browser-type-connect)
- Firefox 启动使用 `-juggler-pipe`，WebKit 使用其适配协议与通常的 `--inspector-pipe`；不能把 Chromium 的 CDP 链路直接套到所有引擎。[Firefox](https://github.com/microsoft/playwright/blob/b335271cdc2b57cac2be8ad37c5dd283ac6b8938/packages/playwright-core/src/server/firefox/firefox.ts#L91-L120)、[WebKit](https://github.com/microsoft/playwright/blob/b335271cdc2b57cac2be8ad37c5dd283ac6b8938/packages/playwright-core/src/server/webkit/webkit.ts#L96-L110)
- 官方项目也有 Chrome 扩展入口：Playwright MCP 使用 `--extension` 连接现有浏览器标签页，沿用用户配置中的登录状态。因此“通过扩展控制现有 Chrome”并非 OpenAI 插件独有。[官方扩展 README](https://github.com/microsoft/playwright/blob/b335271cdc2b57cac2be8ad37c5dd283ac6b8938/packages/extension/README.md)

## 本机 OpenAI Chrome 插件的证据

核查对象为 Chrome 扩展 `hehggadaopoacecdllhhajmbjkdcmajg`，版本 `1.26.1005.11759`；本机 `chrome` 插件版本 `26.1013.40934`。以下结论来自静态读取已安装的程序文件，不涉及浏览记录、Cookie、网页内容或运行时抓包。

### 通信与浏览器调试

本机扩展目录记为 `%LOCALAPPDATA%\Google\Chrome\User Data\<profile>\Extensions\hehggadaopoacecdllhhajmbjkdcmajg\1.26.1005.11759_0\`，简称 `<extension>`。本机证据文件为程序代码与配置，不复制整个扩展到知识库。

| 证据位置 | 已验证内容 | 含义 |
| --- | --- | --- |
| `<extension>/manifest.json` | Manifest V3；后台为 `background.js`；包含 `debugger`、`nativeMessaging`、`tabs`、`scripting` 等权限 | 扩展具备调试标签页及与本机程序通信的能力；权限本身不证明所有能力都已执行 |
| `<extension>/background.js:38` | 实际调用 `chrome.runtime.connectNative(this.application)`、`chrome.debugger.attach({tabId:…}, "1.3")`、`chrome.debugger.sendCommand(…)`，并转发 `chrome.debugger.onEvent` | 本机通信和 CDP 传输均有实现证据，不只是权限声明 |
| `%LOCALAPPDATA%/OpenAI/extension/com.openai.codexextension.json` | 宿主名 `com.openai.codexextension`、`type: stdio`、指定 OpenAI 扩展 origins，程序为插件目录内的 `extension-host.exe` | Chrome 扩展经 Native Messaging 与配套本机程序通信 |
| `<extension>/chunks/js-kernel-host-Biytnb6r.js` | 调用 `Input.dispatchMouseEvent`、`Runtime.callFunctionOn`、`Accessibility.getFullAXTree`、`Page.captureScreenshot` 等 CDP 方法 | 点击、页面求值、无障碍树和截图分别使用浏览器调试能力 |

Chrome 官方将 `chrome.debugger` 定义为调试协议的另一种传输方式，扩展可以按标签页发送 CDP 命令并接收事件；Native Messaging 使用 JSON 消息，经宿主进程的标准输入输出传输。[Chrome debugger 文档](https://developer.chrome.com/docs/extensions/reference/api/debugger)、[Native Messaging 文档](https://developer.chrome.com/docs/extensions/develop/concepts/native-messaging)

因此，对本机插件可画出下面的逻辑链路。该图省略桌面端、浏览器服务和扩展内脚本执行环境之间的内部调度，不声称每次操作都经过同一个进程路径：

```text
Codex / Browser Use 操作与权限检查
  ↔ 本机服务 / Native Messaging 宿主 ↔ Chrome 扩展
  → chrome.debugger.attach / sendCommand
  → CDP → Chrome 标签页
```

这条扩展调试链路本身无需通过开放 `localhost:9222` 接入。这里没有核查用户是否另行启用了调试端口，也没有对运行中进程做网络抓包。官方文档说明插件沿用浏览器 profile，并在扩展权限之外再实施网站权限和确认；完整 CDP 开发者能力另受设置与批准控制。[OpenAI 扩展说明](https://learn.chatgpt.com/docs/chrome-extension)、[开发者模式说明](https://learn.chatgpt.com/docs/whats-new#debug-web-apps-with-browser-developer-mode)

### 与 Playwright 的源码关联

扩展的 `chunks/js-kernel-host-Biytnb6r.js` 内嵌了 `PlaywrightInjected.InjectedScript`，并明确执行以下实例化逻辑（节选，仅用于说明）：

```js
window.__codexPlaywrightInjected = new PlaywrightInjected.InjectedScript(window, {
  isUnderTest: false,
  sdkLanguage: "javascript",
  testIdAttributeName: "data-testid",
  stableRafCount: 1,
  browserName: "chromium",
  customEngines: [],
});
```

自有 `ensurePlaywrightInjectedInTarget` 方法通过 CDP `Runtime.evaluate` 安装注入脚本；自有控制类接收 `cdp`、`cua` 等服务对象，再执行选择器操作。安装注入脚本后还配置了自有凭据字段保护逻辑。这是 OpenAI 的集成实现，并非把页面直接交给本机 Python 包执行。

进一步对照上游：扩展中 `strictModeViolationError` 与 `checkDeprecatedSelectorUsage` 两个方法的逻辑、错误文本结构及弃用 selector 列表顺序，与 Playwright 的实现对应；还包含 `internal:role`、`internal:has-text` 等选择器实现。[对应上游方法](https://github.com/microsoft/playwright/blob/b335271cdc2b57cac2be8ad37c5dd283ac6b8938/packages/injected/src/injectedScript.ts#L1338-L1352)

这些源码证据支持明确判断：**插件内嵌并复用／派生了 Playwright 的页面注入及选择器代码，关联不止是 API 名字相似或都使用 CDP。** 但不能进一步断言它调用了完整标准 `playwright`／`playwright-core` 驱动，或与某个正式版完全等价；原始依赖清单、构建锁文件、source map 和修改历史未取得。对照上游提交是研究基线，不是扩展的已确认来源版本。

证据复查信息（压缩文件行号较粗，优先搜索方法名）：

| 文件 | 行号／标识 | SHA-256 |
| --- | --- | --- |
| `background.js` | 第 38 行：`connectNative`、`debugger.attach`、`debugger.sendCommand` | `5559009e49123ca8ed1bb3f44bd55202cf856f40ba09a7e9689c5f366f239088` |
| `chunks/js-kernel-host-Biytnb6r.js` | 第 1220 行：对应上游方法；第 1234 行附近：注入模板及自有控制类；第 1441 行：安装脚本 | `e16f585691654eb9dd8d6b9f79d9da9c712c217d8a6592f1c2d1d8dd96ce0fac` |

## 区别与使用边界

| 维度 | 本机 OpenAI `@Chrome` | Microsoft Playwright |
| --- | --- | --- |
| 交付目标 | AI 使用现有浏览器完成任务，集成桌面 App、权限和确认 | 可编程浏览器自动化库及测试工具，适合脚本、回归测试、CI |
| Chromium 调试入口 | 扩展的 `chrome.debugger` 传输 CDP | 标准驱动通常用 pipe；CDP 附着可用 WebSocket；另有官方扩展入口 |
| 页面定位 | 已确认内嵌 Playwright 注入／选择器代码，加上自有 API 与保护 | 上游 Locator、注入脚本、动作等待及完整驱动实现 |
| 浏览器范围 | 本次核查 Chrome；官方扩展支持多种 Chromium 浏览器 | Chromium、Firefox、WebKit，使用各自适配实现 |
| 能否沿用现有会话 | 可以，受网站与标签页权限控制 | 可以，通过 CDP 或官方扩展；不是 OpenAI 独有能力 |
| 与本机项目依赖的关系 | 已观察到内嵌代码和配套宿主路径；没有证据显示依赖项目 `.venv` 或此前的 npm CLI 缓存 | 安装到具体 Node.js／Python 环境；版本由项目或工具依赖管理 |
| 代码开放情况 | 本机插件清单标注 `Proprietary`；本次仅静态核查已安装代码 | 官方仓库按 Apache-2.0 开源，见仓库 [LICENSE](https://github.com/microsoft/playwright/blob/b335271cdc2b57cac2be8ad37c5dd283ac6b8938/LICENSE) |

相同 CDP 协议、相似 API、复用页面注入代码、依赖完整标准驱动，是不同层面的结论。这里确认了前面三层，未确认最后一层。也不能据此把 OpenAI 扩展等同于 Microsoft 的 Playwright MCP 扩展。

上述源码核查未操作用户浏览器会话、安装依赖、修改扩展或运行端到端流程。原理结论限于所列本机版本的静态实现与固定上游源码；扩展升级后需重新核查。

## 本机全局 CLI 与技能

2026-10-18 复核：用户已完成全局 CLI 和微软原版 skill 安装。本节只记录状态和复现方法；文档更新没有安装、升级软件或修改 Agent 配置。

| 项目 | 已核实状态 |
| --- | --- |
| 全局命令 | `playwright-cli --version` 返回 `0.1.22`，PowerShell 可直接解析命令 |
| npm 安装范围 | 当前 Scoop Node.js LTS 的全局 prefix 为 `D:\software\scoop\persist\nodejs-lts\bin`；CLI 包位于其 `node_modules/@playwright/cli/` |
| CLI 的运行库 | 依赖 `playwright`、`playwright-core` 的版本均为 `1.64.0-alpha-1790635538000`；CLI 版本与运行库版本分别计数 |
| 全局 skill | `%USERPROFILE%/.agents/skills/playwright-cli/SKILL.md`；`skills list -g --json` 返回 `scope: global` |
| skill 来源 | 锁文件记录为 `microsoft/playwright-cli`，上游路径 `skills/playwright-cli/SKILL.md`，安装日期为 2026-10-18 |
| Agent 关联 | Skills CLI 列表关联了 Codex、Claude Code、Cursor、OpenCode 等目标；不等于已逐一验证各 Agent 的运行时加载 |
| 浏览器保留状态 | Scoop Chromium `155.0.8059.85`，可执行文件为 `D:\software\scoop\apps\chromium\current\chrome.exe`；2026-10-18 按用户要求删除 Playwright 的 `chromium-1228` 和 `chromium_headless_shell-1228` 旧缓存，删除前合计约 685 MiB |

CLI 是实际操作工具，skill 是使用说明，两者独立安装、独立更新。全局 CLI 可由同一台电脑上能访问该命令和 PATH 的 Agent 调用，不需要为每个项目创建 Python `.venv`。Skill 需要被对应 Agent 发现；远程主机或隔离容器不会自动获得本机安装。

### 技能来源与 Codex 原有版本

新安装的微软原版名为 `playwright-cli`，来自 [microsoft/playwright-cli](https://github.com/microsoft/playwright-cli/tree/main/skills/playwright-cli)。本机原有的 Codex skill 名为 `playwright`，位于 `%USERPROFILE%/.codex/skills/playwright/`；其 `SKILL.md`、`scripts/playwright_cli.sh`、`agents/openai.yaml` 与 [OpenAI skill 集合](https://github.com/openai/skills/tree/main/skills/.curated/playwright) 对应文件一致。该目录的 `NOTICE.txt` 说明它基于微软 skill 改编，增加了 Codex 包装脚本及参考文档。

两份 skill 功能相近，都是指导 CLI 操作；它们不等同于 OpenAI `@Chrome` 插件，也不证明使用同一个浏览器会话。本机已有文件无需重复安装；无法仅凭用户目录位置确定原有 Codex skill 的历史安装方式。

### 安装、检查与更新命令

以下为复现命令，不表示本次又执行了安装；`skills add` 的 Agent 选择应按需要确认。

```powershell
# 首次安装全局工具及微软 skill；本机已完成
npm install -g @playwright/cli@latest
skills add microsoft/playwright-cli --skill playwright-cli --global

# 只读检查
Get-Command playwright-cli
playwright-cli --version
playwright-cli --help
skills list -g --json | ConvertFrom-Json | Where-Object name -eq 'playwright-cli'

# 按需更新，CLI 与 skill 分别处理
npm install -g @playwright/cli@latest
skills update -g playwright-cli
```

`--global` 安装 skill 到用户范围，不保证所有 Agent 自动识别。Skills CLI 的范围、目标选择与更新规则见 [CLI 说明](../matt-pocock-skills/skills%20CLI与常用命令.md)。CLI 自带的另一种安装方式为 `playwright-cli install --skills=agents --global`，已由本机 `install --help` 确认支持；本机当前通过 Skills CLI 留有来源记录，不必再运行另一条路线重复安装。

### 使用与验证边界

使用微软 Playwright Extension 连接已有 Chrome，可沿用登录状态。它是独立于 ChatGPT 扩展的另一个扩展；先按 [微软官方说明](https://github.com/microsoft/playwright/blob/b335271cdc2b57cac2be8ad37c5dd283ac6b8938/packages/extension/README.md) 安装，再使用：

```powershell
playwright-cli attach --extension=chrome
playwright-cli snapshot
playwright-cli console
playwright-cli requests
playwright-cli screenshot
playwright-cli detach
```

Agent 应先读取快照，依据当前快照中的元素引用执行操作，页面变化后重新取快照。`detach` 断开附着会话并保留外部浏览器运行；独立启动的新会话使用 `playwright-cli open <URL> --headed`，结束时用 `close`。并行 Agent 应使用独立会话，如 `playwright-cli -s=agent-a ...`、`playwright-cli -s=agent-b ...`，并避免控制同一标签页。[官方命令与会话说明](https://github.com/microsoft/playwright-cli)

本次验证了命令解析、版本、帮助、包依赖和全局 skill 来源；没有启动浏览器或验证微软扩展连接。先前 `open --browser=chrome` 曾因查找默认安装位置失败；本机 Scoop Chrome 的实际路径需要单独配置或通过扩展接入，不能把 CLI 安装成功视作浏览器连接已经通过。全局 CLI 也不会替项目安装 Python／Node.js 库依赖。

旧 Chromium 缓存清理后，已验证两项目录不存在、Scoop Chromium 可执行文件和 `chromium` shim 仍存在；没有启动浏览器。依赖该旧缓存的 Python／Node.js 脚本若使用默认 Chromium 启动方式，需要改为显式选择保留的 Scoop Chromium 可执行文件；清理操作本身没有替脚本或 CLI 配置该路径。`ffmpeg-1011`、`winldd-1007` 和 Playwright 包、技能及其他状态目录均保留。
