# uv 基础知识、常用命令与 Python 项目管理

## 1. uv 是什么

`uv` 是 Astral 开发的 Python 工具链，可统一管理：

- Python 版本；
- 项目虚拟环境；
- 直接依赖和传递依赖；
- 依赖锁定与环境同步；
- 项目命令、临时工具和独立脚本；
- Python 包的构建与发布。

在常见项目中，它可以承担 `pyenv`、`venv`、`pip`、`pip-tools` 和部分 Poetry 工作流的职责。uv 兼容现有 Python 标准，以 `pyproject.toml` 作为项目配置文件。

## 2. 核心模型

```text
pyproject.toml   声明项目元数据、Python 要求和直接依赖
       ↓
uv.lock          记录解析后的精确依赖版本
       ↓
.venv/           安装项目实际使用的 Python 和依赖
       ↓
uv run           在同步后的项目环境中运行命令
```

常见文件：

| 文件或目录 | 作用 | 是否提交 Git |
| --- | --- | --- |
| `pyproject.toml` | 项目元数据、Python 版本范围和依赖声明 | 是 |
| `uv.lock` | 跨平台的精确依赖锁文件，由 uv 管理 | 是 |
| `.python-version` | 项目默认使用的 Python 版本 | 通常是 |
| `.venv/` | 当前机器上的项目虚拟环境 | 否 |

`pyproject.toml` 表达项目允许使用哪些依赖版本，`uv.lock` 记录实际解析结果。应用项目和团队项目通常应同时提交二者，不要手动编辑 `uv.lock`。

## 3. 安装和更新 uv

### Windows

```powershell
winget install --id=astral-sh.uv -e

# 或使用 Scoop
scoop install main/uv
```

### macOS

```bash
brew install uv
```

### macOS 和 Linux 官方安装脚本

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

验证安装：

```powershell
uv --version
uv help
```

通过官方安装脚本安装时，可执行以下命令升级：

```powershell
uv self update
```

通过 WinGet、Scoop 或 Homebrew 安装时，应使用对应包管理器升级。

## 4. 管理 Python 版本

```powershell
uv python list                 # 查看可用和已安装的 Python
uv python install 3.12         # 安装最新的 Python 3.12.x
uv python install 3.11 3.12    # 安装多个版本
uv python find 3.12            # 查找符合要求的解释器
uv python pin 3.12             # 为当前项目写入 .python-version
uv python uninstall 3.11       # 卸载 uv 管理的 Python 3.11
```

uv 会优先发现可用的 Python；找不到符合项目要求的版本时，默认可以自动下载。一般只需在项目中固定次版本，例如 `3.12`，补丁版本由 uv 选择。

项目兼容的 Python 范围由 `pyproject.toml` 声明：

```toml
[project]
requires-python = ">=3.12"
```

`.python-version` 决定本机默认选用哪个版本，`requires-python` 则声明项目允许哪些版本，二者用途不同。

## 5. 创建项目

创建新项目：

```powershell
uv init hello
Set-Location hello
uv python pin 3.12
```

在当前目录初始化：

```powershell
uv init
```

第一次执行 `uv run`、`uv sync` 或 `uv lock` 时，uv 会按需创建 `.venv` 和 `uv.lock`。

初始化后可先运行：

```powershell
uv run python -c "print('Hello, uv')"
```

## 6. 管理项目依赖

### 添加和删除依赖

```powershell
uv add requests                       # 添加运行依赖
uv add "django>=5,<6"                 # 添加带版本范围的依赖
uv add "requests[socks]"              # 添加额外功能依赖
uv add --dev pytest ruff              # 添加到默认开发依赖组
uv add --group docs mkdocs            # 添加到自定义依赖组
uv add --optional cli typer           # 添加可选依赖
uv remove requests                    # 删除运行依赖
uv remove --dev pytest                # 删除开发依赖
```

`uv add` 和 `uv remove` 会同时维护 `pyproject.toml`、`uv.lock` 和项目环境。

### 查看依赖

```powershell
uv tree                               # 查看依赖树
uv tree --outdated                    # 查看依赖树中的可更新包
```

### 更新依赖

```powershell
uv lock --upgrade-package requests    # 只升级一个包及必要关联依赖
uv lock --upgrade                     # 升级所有符合约束的依赖
uv sync                               # 将环境同步到新锁文件
```

uv 不会仅因为软件仓库出现新版本就自动改变已经锁定的版本。需要升级时，应显式执行升级命令。

## 7. 锁定与同步

```powershell
uv lock                    # 解析依赖并创建或更新 uv.lock
uv lock --check            # 检查锁文件是否与项目配置一致
uv sync                    # 按锁文件创建或同步 .venv
uv sync --locked           # 锁文件过期时直接报错，不自动更新
uv sync --frozen           # 直接使用现有锁文件，不检查是否过期
uv sync --all-groups       # 同步所有依赖组
```

日常开发通常不必手动执行 `uv lock`：`uv add`、`uv sync` 和 `uv run` 会在需要时自动锁定和同步。

常用选择：

- 本地开发：`uv sync` 或直接 `uv run ...`；
- 团队验收和 CI：`uv sync --locked`，避免任务过程中悄悄修改锁文件；
- 只有明确需要忽略配置与锁文件是否一致时才使用 `--frozen`。

## 8. 运行项目命令

优先使用 `uv run`，通常不需要手动激活虚拟环境：

```powershell
uv run python main.py
uv run pytest
uv run ruff check .
uv run flask run
```

执行前，`uv run` 会检查锁文件和项目环境并按需同步。若需要严格禁止锁文件变化：

```powershell
uv run --locked pytest
```

确实需要激活虚拟环境时：

```powershell
uv sync
.venv\Scripts\Activate.ps1          # Windows PowerShell
```

```bash
uv sync
source .venv/bin/activate            # macOS/Linux
```

## 9. 克隆项目后的标准流程

```powershell
git clone <repository-url>
Set-Location <project-directory>
uv sync --locked
uv run pytest
uv run python main.py
```

前提是仓库已经提交 `pyproject.toml` 和 `uv.lock`。新成员不需要手动创建虚拟环境或逐个安装依赖。

## 10. 从 requirements.txt 迁移

尚未使用 `pyproject.toml` 的项目可以执行：

```powershell
uv init
uv add -r requirements.txt
uv sync
```

确认项目运行和测试正常后，以 `pyproject.toml` 和 `uv.lock` 作为主要依赖来源。若外部系统仍要求 `requirements.txt`，可导出：

```powershell
uv export --format requirements.txt --output-file requirements.txt
```

## 11. 运行临时工具和独立脚本

### 临时运行 CLI 工具

`uvx` 是 `uv tool run` 的简写。它在隔离环境中运行工具，不必把工具加入项目依赖：

```powershell
uvx ruff check .
uvx black .
uv tool install ruff              # 需要长期作为全局命令时安装
uv tool list
uv tool uninstall ruff
```

项目必须固定工具版本时，仍应使用 `uv add --dev` 和 `uv run`。

### 运行单文件脚本

```powershell
uv run script.py
uv run --with requests script.py
uv init --script script.py --python 3.12
uv add --script script.py requests
```

`--with` 适合一次性运行；需要长期复现时，使用脚本内联元数据或正式项目依赖。

## 12. 构建项目

```powershell
uv build                       # 在 dist/ 中构建源码包和 wheel
```

发布到包索引会产生外部写入，应在确认仓库、版本、凭据和目标索引后再使用 `uv publish`。

## 13. `uv add` 与 `uv pip install` 的区别

```powershell
uv add requests                # 推荐：管理正式项目依赖
uv pip install requests        # 低层 pip 兼容接口：直接修改环境
```

正式 uv 项目优先使用 `uv add`，因为它会记录依赖并更新锁文件。`uv pip install` 主要用于兼容传统 pip 工作流，不会自动把依赖变成项目声明。

## 14. 推荐的日常工作流

### 新项目

```powershell
uv init my-project
Set-Location my-project
uv python pin 3.12
uv add requests
uv add --dev pytest ruff
uv run pytest
uv run ruff check .
```

### 日常修改

```powershell
uv add <package>               # 新增依赖
uv remove <package>            # 删除依赖
uv run pytest                  # 运行测试
uv run <command>               # 运行项目命令
git add pyproject.toml uv.lock # 依赖发生变化时提交二者
```

### CI

```powershell
uv sync --locked
uv run --locked pytest
```

## 15. 常用命令速查

| 目标 | 命令 |
| --- | --- |
| 查看 uv 版本 | `uv --version` |
| 查看帮助 | `uv help`、`uv help <command>` |
| 创建项目 | `uv init [目录]` |
| 固定 Python | `uv python pin 3.12` |
| 安装 Python | `uv python install 3.12` |
| 添加依赖 | `uv add <package>` |
| 添加开发依赖 | `uv add --dev <package>` |
| 删除依赖 | `uv remove <package>` |
| 查看依赖树 | `uv tree` |
| 创建或更新锁文件 | `uv lock` |
| 同步环境 | `uv sync` |
| 严格按锁文件同步 | `uv sync --locked` |
| 运行命令 | `uv run <command>` |
| 临时运行工具 | `uvx <tool>` |
| 导出 requirements | `uv export --format requirements.txt` |
| 构建包 | `uv build` |

最需要记住的是：

```text
uv init    创建项目
uv add     声明并安装依赖
uv sync    同步锁文件与虚拟环境
uv run     在项目环境中运行命令
```

## 16. 常见问题

### 是否需要手动创建 `.venv`？

通常不需要。`uv sync` 或 `uv run` 会自动创建项目环境。

### 是否需要先激活虚拟环境？

通常不需要。直接使用 `uv run <command>` 更简单，也更不容易调用错 Python。

### `uv.lock` 是否应该提交？

应用项目和团队项目应该提交，以保证不同机器和 CI 使用一致的依赖解析结果。

### 为什么安装了包却没有写入 `pyproject.toml`？

通常是使用了 `uv pip install`。正式项目应改用 `uv add`。

### 如何确认当前使用的 Python？

```powershell
uv run python --version
uv run python -c "import sys; print(sys.executable)"
```

## 官方资料

- [uv 官方文档](https://docs.astral.sh/uv/)
- [安装 uv](https://docs.astral.sh/uv/getting-started/installation/)
- [管理 Python 版本](https://docs.astral.sh/uv/concepts/python-versions/)
- [使用 uv 管理项目](https://docs.astral.sh/uv/guides/projects/)
- [管理依赖](https://docs.astral.sh/uv/concepts/projects/dependencies/)
- [锁定与同步](https://docs.astral.sh/uv/concepts/projects/sync/)
- [运行项目命令](https://docs.astral.sh/uv/concepts/projects/run/)
- [uv 命令参考](https://docs.astral.sh/uv/reference/cli/)
