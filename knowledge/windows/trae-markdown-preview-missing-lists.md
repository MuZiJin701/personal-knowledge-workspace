# Trae Markdown 内置预览列表内容缺失排障记录

核对日期：2026-10-07；适用版本：本机 Scoop 安装的 Trae `2.3.88407`。以下结论来自用户截图、本机程序源码和浏览器复现，不代表其他版本都有同一问题。

## 现象与结论

用户通过编辑器右上角的“预览”按钮打开 [WinGet 与 Scoop 文档](../windows-package-management/Windows%20winget与Scoop知识和常用命令.md)，第 7、8、9 节只显示标题，列表正文消失；源文件中的内容实际存在，标题和表格仍能显示。

该文件的 Markdown 列表语法完整。问题位于 Trae 内置预览的列表解析与节点属性校验：部分属性的实际类型与声明不一致，导致列表节点被丢弃。此次无需修改文档正文或主题 CSS。

右上角的内置预览使用 Milkdown 渲染路径；`Ctrl+Shift+V` 对应另一套 Markdown 预览扩展。遇到此问题时，可先切回 Markdown 编辑界面，再按 `Ctrl+Shift+V` 尝试另一套预览；该入口和快捷键已核对本机扩展配置，但本次没有直接验证它在 Trae 窗口中的显示结果。

## 根因与修复

本机内置预览的列表属性存在以下类型不一致：

| 属性 | 原声明接受的类型 | 解析器或默认值产生的类型 | 本次修正后的声明 |
| --- | --- | --- | --- |
| `spread` | `boolean` | 字符串 `"true"` / `"false"`，也有布尔默认值 | `boolean\|string` |
| `diffType` | `string` | 无差异标记时为 `null` | `string\|null` |
| `checked` | `boolean` | 普通列表为 `null`，任务列表为布尔值 | `boolean\|null` |

ProseMirror 支持用 `|` 声明可接受的多种属性类型；这里保留校验，并让声明覆盖现有解析器使用的值。[官方属性校验源码](https://github.com/ProseMirror/prosemirror-model/blob/master/src/schema.ts)

用户确认后，于 2026-10-07 应用了本地补丁。修改范围是 Trae 安装目录下的两份 JavaScript 模块，共修正 4 处 `spread` 声明和 2 处可空属性声明：

```text
<Trae 安装目录>\resources\app\node_modules\@byted-icube\desktop-modules\dist\
  391.409a346f.mjs
  441.2cebc2a9.mjs
```

本机补丁绑定版本 `2.3.88407`，写入前检查原文件 SHA-256，备份原文件，写入后核对修正文件的 SHA-256；应用过程中失败会尝试恢复已经写入的文件。

## 让补丁生效

运行中的 Trae 可能仍使用已经加载的旧模块。保存未保存的文件后，任选一种方式重新加载：

1. 按 `Ctrl+Shift+P`，执行 `Developer: Reload Window`（重载窗口）。
2. 完全退出 Trae，再重新打开；仅关闭预览标签页不能确保程序模块重新加载。

无需重启电脑。重新打开右上角的内置预览，检查第 7、8、9 节的正文和链接是否完整。

## 验证结果与范围

浏览器复现直接加载本机 Trae 随附的解析模块、列表节点声明和列表组件。修复前，原文的列表节点数量为 0；应用补丁后，使用磁盘上的实际修改文件验证，结果如下：

| 检查 | 结果 |
| --- | --- |
| 原文列表 | 19 个列表项全部生成，选取的第 7、8、9 节正文检查通过 |
| 列表变体 | 8 个列表项通过，覆盖普通、嵌套、有序、任务和松散列表 |
| 两份程序模块 | `node --check` 语法检查通过 |
| 原文件备份 | SHA-256 与应用前文件一致 |

以上确认了实际模块的渲染结果；本次没有直接操作重启后的 Trae 窗口，也没有收到用户对界面恢复的确认。

本机保留的回归检查可在 PowerShell 中运行：

```powershell
$taskFixDir = Join-Path $env:USERPROFILE '.codex\backups\trae-markdown-list-fix-20261007'
node (Join-Path $taskFixDir 'trae-markdown-repro.cjs')
```

该检查针对本机安装路径、测试文档和已有 Chrome/Node.js 环境，不是跨电脑通用测试。

## 备份、回滚与升级

修复脚本、回归检查和原文件备份保存在本机仓库之外：

```text
%USERPROFILE%\.codex\backups\trae-markdown-list-fix-20261007\
  trae-markdown-list-patch.ps1
  trae-markdown-repro.cjs
  trae-markdown-list-backup-2.3.88407\
    391.409a346f.mjs
    441.2cebc2a9.mjs
```

需要恢复原程序文件时，在 PowerShell 中执行：

```powershell
$taskFixDir = Join-Path $env:USERPROFILE '.codex\backups\trae-markdown-list-fix-20261007'
& (Join-Path $taskFixDir 'trae-markdown-list-patch.ps1') -Rollback
```

回滚会核对当前补丁文件和原文件备份的 SHA-256；文件已变化时会拒绝覆盖。回滚后同样需要重载窗口或完全退出并重新打开 Trae。

这是本机临时修复，Trae 升级或重新安装可能覆盖它。升级后先检查是否仍有列表缺失问题；不要将旧补丁盲目应用到新版本。克隆知识仓库不会获得上述本机脚本或备份。
