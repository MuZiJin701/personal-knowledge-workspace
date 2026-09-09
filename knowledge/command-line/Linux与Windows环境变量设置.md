# Linux 与 Windows 环境变量设置

## 一、Linux

### 临时环境变量

只对当前终端及其启动的程序有效，关闭终端后失效。

```bash
export MY_VAR="abc"
echo "$MY_VAR"

# 删除
unset MY_VAR
```

### 永久环境变量：当前用户

编辑 `~/.bashrc`：

```bash
export MY_VAR="abc"
```

立即生效：

```bash
source ~/.bashrc
```

登录环境需要生效时，也可以写入 `~/.profile`，然后重新登录。

### 永久环境变量：所有用户

编辑 `/etc/environment`：

```text
MY_VAR="abc"
```

或创建 `/etc/profile.d/my-env.sh`：

```bash
export MY_VAR="abc"
```

系统文件通常需要管理员权限，修改后重新登录生效。

```bash
sudo nano /etc/environment
```

## 二、Windows PowerShell

### 临时环境变量

只对当前 PowerShell 窗口及其启动的程序有效。

```powershell
$env:MY_VAR = "abc"
echo $env:MY_VAR

# 删除
Remove-Item Env:MY_VAR
```

### 永久用户变量

```powershell
[Environment]::SetEnvironmentVariable("MY_VAR", "abc", "User")

# 删除
[Environment]::SetEnvironmentVariable("MY_VAR", $null, "User")
```

### 永久系统变量

需要管理员权限：

```powershell
[Environment]::SetEnvironmentVariable("MY_VAR", "abc", "Machine")

# 删除
[Environment]::SetEnvironmentVariable("MY_VAR", $null, "Machine")
```

## 三、Windows CMD

### 临时环境变量

```cmd
set MY_VAR=abc
echo %MY_VAR%

:: 删除
set MY_VAR=
```

### 永久用户变量

```cmd
setx MY_VAR abc
```

`setx` 修改后不会更新当前窗口，需要重新打开终端。修改 `PATH` 时不建议直接使用 `setx`，避免路径丢失或重复。

## 四、查看变量

### Linux

```bash
echo "$MY_VAR"
printenv MY_VAR
env | grep '^MY_'
```

### Windows PowerShell

```powershell
$env:MY_VAR
Get-ChildItem Env:
```

### Windows CMD

```cmd
echo %MY_VAR%
set
```

## 五、注意事项

- 临时变量只影响当前进程及其子进程。
- 修改永久变量后，重新打开终端；Linux 图形程序通常需要重新登录。
- `.env` 是项目配置文件，不会自动成为系统环境变量，除非程序或 Shell 主动读取。
- 不要把密码、Token 等敏感信息提交到 Git 或公开配置文件。
