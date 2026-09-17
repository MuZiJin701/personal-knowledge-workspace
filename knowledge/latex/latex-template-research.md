# LaTeX 演示文稿与简历模板精选

> 调研日期：2026-09-17。GitHub 星标会变化；下表为当日 API 快照。许可证、归档状态和更新时间均核对了项目 API、README、LICENSE 与默认分支提交记录。

## 直接结论

- 演示文稿：中文优先选 **SJTUBeamer**，通用现代风选 **Metropolis**，极简风选 **Focus**，研究汇报选 **Minimalist LaTeX Presentation**。
- 简历：完整视觉方案选 **Awesome-CV**，中文选 **billryan/resume**，单页开发者简历选 **sb2nov/resume**，多风格选 **latexcv**，图形化双栏选 **AltaCV**。
- 这些模板都可直接使用终端编译，LaTeX Workshop 不是必需项。

## 演示文稿模板

| 项目 | 星标 | 许可证 | 编译要求与价值 | 维护判断 |
|---|---:|---|---|---|
| [Metropolis](https://github.com/matze/mtheme) | 6,887 | CC BY-SA 4.0 | TeX Live 已收录 `beamertheme-metropolis`，在 Beamer 中写 `\usetheme{metropolis}`；Fira Sans 为可选字体。经典、干净、通用，最适合不想维护自定义样式的人。[README](https://github.com/matze/mtheme/blob/master/README.md) · [CTAN](https://ctan.org/pkg/beamertheme-metropolis) | 未归档，但默认分支最后提交为 [2018-08-01](https://github.com/matze/mtheme/commit/2fa6084b9d34fec9d2d5470eb9a17d0bf712b6c8)，CTAN 版本仍是 2017 年的 1.2；**老而经典，不适合期待持续新功能**。[API](https://api.github.com/repos/matze/mtheme) |
| [SJTUBeamer](https://github.com/sjtug/SJTUBeamer) | 820 | Apache-2.0 | 中文用 `ctexbeamer` 与 XeLaTeX；项目明确给出 `latexmk -xelatex main.tex`，包含中英文、16:9、明暗色和参考文献示例。虽带交大品牌，但样式选项丰富，适合中文学术汇报。[README](https://github.com/sjtug/SJTUBeamer/blob/main/README.md) | 未归档；2026-05-20 发布 v3.2.0 并修复封面居中，仍在维护。[实质提交](https://github.com/sjtug/SJTUBeamer/commit/626d603cd8555891ec2da0b679ab777161401559) · [API](https://api.github.com/repos/sjtug/SJTUBeamer) |
| [Focus](https://github.com/pcafrica/focus-beamertheme) | 545 | GPL-3.0 | TeX Live 已收录；依赖 `appendixnumberbeamer`、`fira`、PGF/TikZ，支持换色、脚注栏和进度条。适合强调内容的极简演示。[README](https://github.com/pcafrica/focus-beamertheme/blob/main/README.md) · [CTAN](https://ctan.org/pkg/beamertheme-focus) | 未归档；最后实质修复为 [2024-02-07](https://github.com/pcafrica/focus-beamertheme/commit/98aedb1b08fa905733ebad8d3f57ac0a095d83c0)。**GPL-3.0 属强 copyleft，分发修改后的主题前应检查义务**。[API](https://api.github.com/repos/pcafrica/focus-beamertheme) |
| [Minimalist LaTeX Presentation](https://github.com/pmichaillat/latex-presentation) | 295 | MIT | 使用 Beamer，以 pdfTeX 编译；仓库提供标准比例和 16:9 版本，版式针对学术论证与图表。适合希望直接复制一套研究汇报结构的人。[README](https://github.com/pmichaillat/latex-presentation/blob/main/README.md) | 未归档；2026-04-27 新增 16:9 模板，2026-05 仍更新实例，维护活跃。[实质提交](https://github.com/pmichaillat/latex-presentation/commit/79cfe0bcb31b6d8710bfc1e41a4a1ae9423ce919) · [API](https://api.github.com/repos/pmichaillat/latex-presentation) |

## 简历与 CV 模板

| 项目 | 星标 | 许可证 | 编译要求与价值 | 维护判断 |
|---|---:|---|---|---|
| [Awesome-CV](https://github.com/posquit0/Awesome-CV) | 28,513 | LPPL-1.3c | 使用 XeLaTeX，依赖 Font Awesome 6、Roboto、Source Sans Pro；同时提供简历和求职信。视觉完成度最高，适合愿意按模板结构填写的人。[README](https://github.com/posquit0/Awesome-CV/blob/master/README.md) · [LICENCE](https://github.com/posquit0/Awesome-CV/blob/master/LICENCE) | 未归档；2026-01-30 仍新增 WhatsApp 链接支持，之后持续维护工作流。[实质提交](https://github.com/posquit0/Awesome-CV/commit/1d6a925bb6b0cfd17eb9f7073555f588278f3b49) · [API](https://api.github.com/repos/posquit0/Awesome-CV) |
| [billryan/resume](https://github.com/billryan/resume) | 11,436 | MIT；字体除外 | 使用 XeLaTeX，原生 Unicode/CJK，包含中英文与带照片版本。对中文用户最直接；项目说明部分字体不受 MIT 许可覆盖，公开分发时应改用自己有权使用的系统字体。[README](https://github.com/billryan/resume/blob/master/README.md) | 未归档，但最后实质更新为 [2021-09-01](https://github.com/billryan/resume/commit/71a67c6bb7d026e0f8ee969a7df6498857716127)；**模板可用，但已长期少维护**。[API](https://api.github.com/repos/billryan/resume) |
| [sb2nov/resume](https://github.com/sb2nov/resume) | 6,990 | MIT；示例个人数据除外 | 单页、单栏、基础字体，直接用 pdfLaTeX；结构集中在教育、经历、项目，依赖少，最容易改。[README](https://github.com/sb2nov/resume/blob/master/README.md) | 未归档；2026-05-17 仍更新 pdfLaTeX 输出，维护活跃。[实质提交](https://github.com/sb2nov/resume/commit/7b70fe14876f97180034787f2a7f661597416a17) · [API](https://api.github.com/repos/sb2nov/resume) |
| [latexcv](https://github.com/jankapunkt/latexcv) | 3,336 | MIT | 提供 Classic、Modern、Two Column、Sidebar、Rows、Infographics 等多套布局；最小 TeX Live 即可，明确不要求 XeTeX、LuaTeX 或 KOMA。适合先选样式再替换内容。[README](https://github.com/jankapunkt/latexcv/blob/master/README.md) | 未归档；模板内容在 [2025-02-08](https://github.com/jankapunkt/latexcv/commit/7ee9501a2dac70d92c39db4b7eb5547c428fc3ff) 仍有修正，之后主要是文档和依赖维护。[API](https://api.github.com/repos/jankapunkt/latexcv) |
| [AltaCV](https://github.com/liantze/AltaCV) | 1,575 | LPPL-1.3 或更高版本 | 可用 pdfLaTeX、XeLaTeX 或 LuaLaTeX；出版物列表还需 Biber，示例使用 Lato 与 Roboto Slab。双栏、图标化且可配置，适合信息密度较高的 CV。[README](https://github.com/liantze/AltaCV/blob/main/README.md) · [LICENSE](https://github.com/liantze/AltaCV/blob/main/LICENSE.md) | 未归档；2025-07-30 增强纯文本/ATS 转换支持，维护状态良好。[实质提交](https://github.com/liantze/AltaCV/commit/91373530c55843533a4de12a29d28896f9b14c0d) · [API](https://api.github.com/repos/liantze/AltaCV) |

## 终端使用

下载模板后进入包含主 `.tex` 文件的目录，按模板指定引擎运行：

```powershell
# 中文、系统字体或模板明确要求 XeLaTeX
latexmk -xelatex main.tex

# pdfLaTeX 模板
latexmk -pdf main.tex

# 模板明确要求 LuaLaTeX
latexmk -lualatex main.tex

# 清理辅助文件
latexmk -c
```

先读项目 README，并把 `main.tex` 换成仓库实际入口文件。包含参考文献时，`latexmk` 通常会自动完成所需的多轮编译；模板若明确要求 Biber，再确认系统中存在 `biber`。

## 其他可靠入口

- [Overleaf 演示文稿模板库](https://www.overleaf.com/latex/templates/tagged/presentation)：可按 Beamer、XeLaTeX、中文等标签筛选并在线预览。
- [Overleaf CV/简历模板库](https://www.overleaf.com/latex/templates/tagged/cv)：包含编辑精选，适合先看成品再决定是否下载源码。

选择模板时，优先看最近实质提交、许可证和编译引擎，不只看星标；学校品牌模板还应确认校名、Logo 与配色是否允许对外使用。

## 东南大学（SEU）模板

东南大学教务处当前公开的是[本科毕业设计（论文）2025 年 3 月版参考模板](https://jwc.seu.edu.cn/2025/0320/c21686a522328/page.htm)（Word），研究生院公开的是[研究生学位论文格式规定](https://xxgk.seu.edu.cn/2023/0424/c10892a442680/page.htm)；未发现学校官方维护或背书的 LaTeX 仓库。下列项目均为**社区非官方模板**，定稿前应与本学院最新要求逐项核对。星标为 2026-09-17 的 GitHub API 快照。

| 类别 / 项目 | 星标 / 许可 | 编译与适用范围 | 维护状态与注意事项 |
|---|---:|---|---|
| 本科论文：[SEU Graduation Thesis Template](https://github.com/Moon-Knight1/SEU-Graduation-Thesis-Template) | [0](https://api.github.com/repos/Moon-Knight1/SEU-Graduation-Thesis-Template) / 未声明 | `latexmk -xelatex main.tex`，XeLaTeX + Biber；按 Windows、TeX Live 2025 编写，含 AI 使用说明表。[README](https://github.com/Moon-Knight1/SEU-Graduation-Thesis-Template/blob/main/README.md) | 未归档，最后推送 2026-06-19；最贴近现行格式，但项目新、无许可证且严格依赖 Windows 字体，公开改编或分发前应先获得许可。 |
| 本科论文：[seu-bachelor-thesis-2022](https://github.com/SuikaXhq/seu-bachelor-thesis-2022) | [103](https://api.github.com/repos/SuikaXhq/seu-bachelor-thesis-2022) / GPL-3.0 | XeLaTeX；Windows 运行 `sample_thesis.bat`，也提供 Overleaf/latexmk 用法；面向 2022–2024 届。[README](https://github.com/SuikaXhq/seu-bachelor-thesis-2022/blob/main/README.md) | 未归档，最后推送 2024-03-09；README 明示 TeX Live 2022 有兼容问题，且依据 2024 年规范，使用前必须对照 2025 年官方模板。 |
| 硕士论文：[SEU-master-thesis](https://github.com/TouchFishPioneer/SEU-master-thesis) | [237](https://api.github.com/repos/TouchFishPioneer/SEU-master-thesis) / GPL-3.0 | Windows 运行 `.\make.bat`；底层顺序为 XeLaTeX → BibTeX → MakeIndex → XeLaTeX ×2；支持学术型、专业型硕士。[README](https://github.com/TouchFishPioneer/SEU-master-thesis/blob/master/README.md) | 未归档，最后推送 2024-04-22；不支持博士，且源自较早的 SeuThesis/SeuThesix，送审前要按研究生院最新规定复核。 |
| 演示 / 答辩：[SEU-Beamer-Slide](https://github.com/TouchFishPioneer/SEU-Beamer-Slide) | [424](https://api.github.com/repos/TouchFishPioneer/SEU-Beamer-Slide) / GPL-3.0 | Windows 运行 `.\make.bat`，macOS/Linux 运行 `./make.sh`；适合各层次答辩、学术报告与活动展示。[README](https://github.com/TouchFishPioneer/SEU-Beamer-Slide/blob/master/README.md) | 未归档，但最后推送 2021-10-15；成熟且星标最高，设计和校徽素材较旧，正式场合先检查视觉规范。 |
| 作业：[SEU-ML-Assign](https://github.com/Teddy-van-Jerry/SEU-ML-Assign_LaTeX_Template) | [31](https://api.github.com/repos/Teddy-van-Jerry/SEU-ML-Assign_LaTeX_Template) / MIT | 已进入 TeX Live/CTAN：`tlmgr install seu-ml-assign`，示例用 `latexmk -pdf seu-ml-assign-sample.tex`；支持教师出题与学生解答。[README](https://github.com/Teddy-van-Jerry/SEU-ML-Assign_LaTeX_Template/blob/master/README.md) | 未归档，最后推送 2022-04-15；定位机器学习课程，但类文件可复用于普通作业。 |
| 实验报告：[SEU Circuit Report](https://github.com/Teddy-van-Jerry/SEU_Circuit_Report) | [4](https://api.github.com/repos/Teddy-van-Jerry/SEU_Circuit_Report) / MIT | XeLaTeX + Biber，可用 `latexmk -xelatex "Circuit Experiment Report.tex"`；针对电路实验报告。[README](https://github.com/Teddy-van-Jerry/SEU_Circuit_Report/blob/master/README.md) | 未归档，最后推送 2022-10-24；课程专用且维护较少，其他实验课只能借用版式。 |
| 开题报告：[seu-cloud-proposal](https://github.com/g101418/seu-cloud-proposal) | [9](https://api.github.com/repos/g101418/seu-cloud-proposal) / 未声明 | 类文件使用 `ctexart`、fontspec 与 BibTeX 后端，可用 `latexmk -xelatex 开题报告.tex`；覆盖背景、现状、目标、路线和进度等章节。[README](https://github.com/g101418/seu-cloud-proposal/blob/main/README.md) | 未归档，最后推送 2021-01-23；是计算机学院云计算课题组版本，不是全校通用模板，且未声明许可证。 |
| 中文简历：[SEU-CV](https://github.com/Exception0x0194/SEU-CV) | [35](https://api.github.com/repos/Exception0x0194/SEU-CV) / 未声明 | `latexmk -xelatex main.tex` 或 `latexmk -lualatex main.tex`；内置 Noto Serif SC，使用 SEU 配色与校徽。[README](https://github.com/Exception0x0194/SEU-CV/blob/main/readme.md) | 未归档，最后推送 2025-03-10；属于校主题个人简历，无许可证，公开分发修改版前应先获得许可。 |
