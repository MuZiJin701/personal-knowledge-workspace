# LaTeX 与 TeX Live 快速入门

## 一、核心概念

| 名称 | 作用 |
| --- | --- |
| LaTeX | 使用标记语言编写并排版文档 |
| TeX Live | 提供排版引擎、宏包、字体、文档和管理工具 |
| VS Code | 可选的 `.tex` 编辑器 |
| LaTeX Workshop | 可选的 VS Code 编译与预览集成 |

工作流程：

```text
编辑 .tex → TeX Live 编译 → 生成 PDF
```

本文以终端命令为主。VS Code 只负责编辑，真正完成排版的是 TeX Live。

## 二、确认 TeX Live 可用

在 PowerShell 中执行：

```powershell
where.exe xelatex
xelatex --version
latexmk --version
tlmgr --version
```

如果刚安装完却提示找不到命令，关闭并重新打开 VS Code 或终端，让新的 `PATH` 生效。

## 三、编译引擎的区别

| 命令 | 特点 | 适用场景 |
| --- | --- | --- |
| `pdflatex` | 快速、传统、兼容性好；中文和系统字体使用不便 | 英文文档、期刊指定模板 |
| `xelatex` | 原生 Unicode，可直接使用系统字体 | 中文文档、中文演示文稿 |
| `lualatex` | Unicode、系统字体，并支持 Lua 扩展 | 高级排版、需要 Lua 的宏包 |
| `latexmk` | 自动调用引擎并重复编译，处理目录、引用和参考文献 | 日常编译首选 |

常用组合：

```powershell
# 中文文档：推荐
latexmk -xelatex example.tex

# 英文或模板指定 pdfLaTeX
latexmk -pdf example.tex

# 需要 LuaLaTeX
latexmk -lualatex example.tex
```

直接运行 `xelatex example.tex` 通常只编译一次；`latexmk` 会自动编译到引用等内容稳定。

## 四、最小中文文档

创建 `example.tex`：

```tex
\documentclass{ctexart}

\title{我的第一篇 LaTeX 文档}
\author{作者}
\date{\today}

\begin{document}

\maketitle

\section{介绍}

你好，LaTeX！

行内公式：$E=mc^2$。

\[
  f(x)=\int_0^x t^2\,dt
\]

\begin{itemize}
  \item 第一项
  \item 第二项
\end{itemize}

\end{document}
```

进入文件所在目录并编译：

```powershell
latexmk -xelatex example.tex
```

成功后得到 `example.pdf`。

## 五、终端工作流

在 VS Code 中编辑并保存文件，然后在终端执行：

```powershell
cd <项目目录>
latexmk -xelatex example.tex
Invoke-Item .\example.pdf
```

修改后重新执行编译命令即可。需要持续监听文件变化时：

```powershell
latexmk -xelatex -pvc example.tex
```

LaTeX Workshop 不常用时无需配置；它只是把上述编译、错误跳转和 PDF 预览包装成按钮与快捷键。

## 六、图片和颜色

```tex
\documentclass{ctexart}
\usepackage{graphicx}
\usepackage{xcolor}

\begin{document}

\textcolor{red}{红色文字}

\colorbox{yellow!30}{带背景色的文字}

\includegraphics[width=0.5\textwidth]{photo.png}

\end{document}
```

图片路径默认相对于 `.tex` 文件所在目录。

## 七、制作演示文稿

LaTeX 使用 Beamer 制作演示文稿，最终输出 PDF：

```tex
\documentclass{ctexbeamer}

\usetheme{Madrid}
\title{我的演示文稿}
\author{作者}

\begin{document}

\begin{frame}
  \titlepage
\end{frame}

\begin{frame}{主要内容}
  \begin{itemize}
    \item<1-> 第一项
    \item<2-> 第二项
  \end{itemize}

  \[
    E=mc^2
  \]
\end{frame}

\end{document}
```

编译：

```powershell
latexmk -xelatex slides.tex
```

Beamer 支持图片、颜色、表格、图表、代码和逐步显示。视频、音频及复杂动画依赖 PDF 阅读器，兼容性较差。

## 八、模板资源

- 演示文稿：通用现代风选 [Metropolis](https://github.com/matze/mtheme)，中文学术汇报选 [SJTUBeamer](https://github.com/sjtug/SJTUBeamer)。
- 简历：完整视觉方案选 [Awesome-CV](https://github.com/posquit0/Awesome-CV)，中文选 [billryan/resume](https://github.com/billryan/resume)，简单单页选 [sb2nov/resume](https://github.com/sb2nov/resume)。
- 更多选择及星标、许可证、编译要求和维护状态见[模板精选与调研](latex-template-research.md)。

## 九、常用命令

```powershell
# 编译中文文档
latexmk -xelatex example.tex

# 监听文件变化并自动重新编译
latexmk -xelatex -pvc example.tex

# 清理 .aux、.log 等中间文件，保留 PDF
latexmk -c

# 查找宏包文件
kpsewhich ctexart.cls

# 查看宏包文档
texdoc ctex

# 更新 TeX Live 管理器和所有宏包
tlmgr update --self
tlmgr update --all
```

## 十、最短选择规则

- 中文文档：`ctexart` + `latexmk -xelatex`。
- 中文演示：`ctexbeamer` + `latexmk -xelatex`。
- 日常操作：VS Code 编辑，终端运行 `latexmk`。
- 期刊模板：使用模板明确指定的引擎。
- 需要 Lua 扩展：使用 `latexmk -lualatex`。
- 编译失败：先看第一条错误，而不是最后一条连锁错误。

## 参考资料

- [LaTeX 官方文档](https://www.latex-project.org/help/documentation/)
- [TeX Live](https://www.tug.org/texlive/)
- [LaTeX Workshop](https://marketplace.visualstudio.com/items?itemName=James-Yu.latex-workshop)
