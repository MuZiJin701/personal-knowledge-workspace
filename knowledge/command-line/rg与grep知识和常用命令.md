# rg 与 grep：知识和常用命令

## 1. 基本认识

- `rg`（ripgrep）：面向现代代码仓库的快速文本搜索工具。
- `grep`：Unix/Linux 传统文本过滤和搜索工具。
- 两者都能从文件或标准输入中查找普通字符串、正则表达式，并输出匹配行。

## 2. 共同点

- 支持搜索文件内容和标准输入：`command | rg "关键词"`。
- 支持正则表达式、大小写控制、显示行号和反向匹配。
- 可配合管道、重定向和其他命令使用。
- 都可以限制文件范围或排除指定内容。

## 3. 主要区别

| 项目 | `rg` | `grep` |
|---|---|---|
| 递归搜索 | 默认递归当前目录 | 使用 `-r` 或 `-R` |
| 忽略规则 | 默认遵守 `.gitignore` | 默认不读取 `.gitignore` |
| 默认范围 | 通常跳过隐藏文件和二进制文件 | 通常按指定参数搜索 |
| 性能 | 通常更适合大型代码仓库 | 小范围搜索和传统脚本足够好 |
| 正则默认语法 | Rust 风格正则 | POSIX 基础正则 |
| 扩展正则 | 默认支持常见扩展语法 | 使用 `-E` |
| PCRE2 | 使用 `-P`，需支持 PCRE2 | GNU grep 可使用 `-P`，并非所有实现都有 |
| 兼容性 | 需额外安装 | Unix/Linux 环境通常自带 |

`rg` 与 `grep` 的参数不能完全互换；迁移命令时应检查具体选项和正则语法。

## 4. `rg` 常用命令

```bash
rg "TODO"                         # 搜索当前目录
rg "TODO" src                     # 搜索指定目录
rg -n "TODO"                     # 显示行号
rg -i "error"                    # 忽略大小写
rg -F "a.b"                      # 按普通字符串搜索，不解释正则
rg -l "TODO"                     # 只显示匹配文件名
rg -c "TODO"                     # 统计每个文件的匹配行数
rg -v "TODO"                     # 显示不匹配的行
rg -g "*.js" "TODO"              # 只搜索 JS 文件
rg -g "!*.min.js" "TODO"         # 排除压缩 JS 文件
rg --hidden "TODO"               # 包含隐藏文件
rg -uu "TODO"                    # 放宽忽略规则，搜索更多文件
rg --files                        # 列出可搜索文件
rg "TODO" -A 2 -B 1              # 显示匹配行前后文
rg -P "(?i)error|warning"        # 使用 PCRE2 正则
```

## 5. `grep` 常用命令

```bash
grep "TODO" file.txt             # 搜索文件
grep -r "TODO" src               # 递归搜索目录
grep -n "TODO" file.txt          # 显示行号
grep -i "error" file.txt         # 忽略大小写
grep -F "a.b" file.txt           # 按普通字符串搜索
grep -l "TODO" *.js              # 只显示匹配文件名
grep -c "TODO" file.txt          # 统计匹配行数
grep -v "TODO" file.txt          # 显示不匹配的行
grep -E "error|warning" file.txt # 使用扩展正则
grep -P "(?i)error|warning" file.txt # 使用 PCRE（实现需支持）
grep -A 2 -B 1 "TODO" file.txt   # 显示匹配行前后文
```

## 6. 正则和字符串搜索

```text
^start       # 以 start 开头
end$         # 以 end 结尾
error|warn   # error 或 warn
\d+          # 一个或多个数字（取决于正则模式）
```

只想查找原样文本时使用 `-F`，避免 `.`, `*`, `[` 等字符被当作正则语法。

## 7. 实用选择

- 搜索项目代码：优先 `rg`，例如 `rg -n "函数名" .`。
- 处理管道文本或维护传统 Shell 脚本：使用 `grep`。
- 需要跨环境运行：优先考虑目标环境是否安装 `rg`。
- 搜索结果异常时：检查 `.gitignore`、隐藏文件、二进制文件和正则模式。

## 8. 一句话记忆

`grep` 是通用且兼容性强的传统搜索工具；`rg` 是默认递归、遵守忽略规则、面向代码仓库优化的现代搜索工具。
