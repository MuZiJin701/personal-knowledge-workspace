# curl 和 tar 常用命令速查

`curl` 常用来下载文件，`tar` 常用来打包、解压。下面按 Windows PowerShell 举例；Linux/macOS 将 `curl.exe` 改为 `curl` 即可。

网址、文件名和目录名都需要按实际情况替换；`example.com` 是示例地址。

## 1. curl：查看和下载

```powershell
# 查看网址返回的文字内容
curl.exe "https://example.com"

# 下载并自己指定文件名：保存为 local.zip
curl.exe -fL -o local.zip "https://example.com/app.zip"

# 下载并沿用网址末尾的文件名：保存为 app.zip
curl.exe -fLO "https://example.com/app.zip"

# 下载并采用服务器在响应头中提供的文件名；没有则按网址取名
curl.exe -fLOJ "https://example.com/download"
```

记住：**小写 `-o` 后面写保存名，大写 `-O` 按网址取名，`-OJ` 允许服务器提供文件名。**

常用组合 `-fL` 表示“HTTP 出错时报失败，遇到网址跳转则跟随”。上面的下载命令默认保存到当前目录；`-o` 也可以指定路径，例如 `-o "output/local.zip"`，但父目录要先存在。

## 2. tar：查看、解压和打包

```powershell
# 查看 .tar.gz 包里有什么，不解压
tar -tzf package.tar.gz

# 解压 .tar.gz 到当前目录
tar -xzf package.tar.gz

# 解压到指定目录：先创建 output，再解压
mkdir output
tar -xzf package.tar.gz -C output

# 将 my-folder 文件夹打包压缩为 backup.tar.gz
tar -czf backup.tar.gz my-folder
```

**看包用 `-tzf`，解压用 `-xzf`，打包用 `-czf`。** `f` 后面紧跟包名；`z` 表示使用 gzip 压缩。`.tar` 只打包，`.tar.gz` 则在打包后再压缩。

如果处理的是未压缩的 `.tar` 文件，去掉 `z`：

```powershell
tar -tf package.tar                 # 查看
tar -xf package.tar                 # 解压
tar -cf backup.tar my-folder        # 打包
```

## 3. 下载后解压的常用顺序

1. 下载：`curl.exe -fL -o package.tar.gz "真实下载地址"`。
2. 确认下载成功后查看：`tar -tzf package.tar.gz`。
3. 创建一个空目录：`mkdir output`。
4. 解压进去：`tar -xzf package.tar.gz -C output`。

每一步成功后再执行下一步。下载、打包和解压都可能覆盖同名文件，操作前确认目标路径；只解压可信来源的文件。

## 参考

- [curl 官方手册](https://curl.se/docs/manpage.html)：下载及 `-f`、`-L`、`-o`、`-O`、`-J`。
- [libarchive / bsdtar 官方手册源码](https://github.com/libarchive/libarchive/blob/master/tar/bsdtar.1)：打包、解压及 `-C` 等选项；同时核对了本机 `tar --help`。
