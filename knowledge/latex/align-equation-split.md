# LaTeX：`align`、`equation` 与 `split`

这三个数学环境由 `amsmath` 宏包提供：

```latex
\usepackage{amsmath}
```

## 快速选择

| 环境 | 适用场景 | 多行对齐 | 编号 |
| --- | --- | --- | --- |
| `equation` | 单个公式 | 否 | 整体一个 |
| `align` | 多行公式，按 `&` 对齐 | 是 | 默认每行一个 |
| `split` | 一个公式拆成多行 | 是 | 整体一个；需嵌套使用 |

选择规则：

- 单个短公式：`equation`
- 多行且需要逐行编号：`align`
- 多行但属于同一个公式：`equation` + `split`

## 示例

### 单个公式：`equation`

```latex
\begin{equation}
  E = mc^2
\end{equation}
```

### 多行分别编号：`align`

`&` 标记对齐位置，通常放在等号前：

```latex
\begin{align}
  a &= b + c \\
    &= d + e
\end{align}
```

某行不编号时，在该行加入 `\notag`。

### 多行共用一个编号：`split`

`split` 不能独立使用，通常嵌套在 `equation` 中：

```latex
\begin{equation}
  \begin{split}
    a &= b + c \\
      &= d + e
  \end{split}
\end{equation}
```

## 取消编号

使用带星号的环境：

```latex
\begin{equation*}
  E = mc^2
\end{equation*}

\begin{align*}
  a &= b + c \\
    &= d + e
\end{align*}
```

不要把 `align` 嵌套在 `equation` 中；`align` 本身就是独立数学环境。
