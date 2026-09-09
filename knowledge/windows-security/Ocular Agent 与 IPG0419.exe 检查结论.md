# Ocular Agent 与 IPG0419.exe 检查结论

## 结论

`IPG0419.exe` 不是单个驱动，而是一个 Ocular Agent 客户端安装包。它在本机安装了一整套终端代理组件，包括普通程序、服务、配置数据和内核驱动。

## 识别依据

- `IPG0419.exe` 元数据：
  - Product：`Ocular Tool`
  - Description：`Ocular Agent V3`
  - Original name：`AgentStp`
  - Version：`4.83.918.0`
- 已安装组件的元数据包含：
  - `systecv3.exe`、`winrdgv3.exe`：Product 为 `OCular Agent`
  - `Agt3Tool.exe`：Product 为 `Ocular Tool`，用于 Agent 维护
- 相关目录：
  - `C:\Windows\SysWOW64\ocular`
  - `C:\ProgramData\Ocular`
- 厂商信息集中指向 `TEC Solutions Limited`，与 Ocular/SurveilStar 产品体系一致。

## 已发现组件

| 组件 | 作用/状态 |
| --- | --- |
| `TsdEncrypt.sys`、`TsdEncryptMF.sys` | 文件系统加密/过滤驱动；当前运行 |
| `TSafeDisk` / `TSDDrv64.sys` | 安全磁盘相关驱动；按需启动，当前停止 |
| `TVDisk.sys` | 虚拟磁盘驱动；按需启动，当前停止 |
| `AKernel3.exe` 等 | Ocular Agent 用户态程序；发现 `AKernel3.exe` 正在运行 |

## 安装时间

Windows 服务日志记录：

- `TVDisk`：2026-07-22 13:54:25
- `TSafeDisk`：2026-07-22 13:54:31

这与运行 `IPG0419.exe` 的时间高度吻合。驱动文件内部版本日期为 2022 年，不代表本机安装日期。

## 对电脑的影响

根据产品能力和本机已加载的文件过滤驱动，它可能用于：

- 监控网站、程序、邮件/聊天或屏幕活动；
- 限制文件复制、外发、打印、USB 和网络传输；
- 对敏感文件加密、保护或加水印；
- 通过文件系统过滤驱动影响文件读写，并带来兼容性或性能开销。

具体功能是否开启取决于管理端策略；仅凭本机文件不能确认截图、键盘记录等功能是否启用。

## 卸载建议

可以卸载，但不要直接删除 `.sys` 文件。应卸载整个 Ocular Agent，优先使用：

```text
C:\Program Files (x86)\Common Files\System\Agt3Tool.exe
```

官方说明使用 `agt3tool ocularadv` 打开 Agent Tool，卸载通常还需要管理端确认码：

- <https://www.surveilstar.com/uninstalling-surveilstar.html>

如果这是公司或学校部署的终端管理软件，应先联系管理员；如果不是本人授权安装的，应先保留驱动和日志，再进行完整卸载排查。
