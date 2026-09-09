# Git 知识和常用命令

## 1. Git 是什么

Git 是分布式版本控制系统，用于记录文件变化、管理分支、协作开发和恢复历史版本。

核心流程：

~~~text
工作区 → 暂存区 → 本地仓库 → 远程仓库
修改     git add   git commit   git push
~~~

## 2. 初始配置

~~~bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
git config --list                 # 查看配置
~~~

## 3. 创建或获取仓库

~~~bash
git init                          # 在当前目录初始化仓库
git clone <仓库地址>               # 克隆远程仓库
git remote -v                     # 查看远程仓库地址
git remote add origin <仓库地址>    # 添加远程仓库
~~~

## 4. 日常提交

~~~bash
git status                         # 查看工作区和暂存区状态
git add <文件>                     # 暂存指定文件
git add .                          # 暂存当前目录下的变更
git diff                           # 查看未暂存差异
git diff --staged                  # 查看已暂存差异
git commit -m "说明本次变更"        # 创建提交
git log --oneline                  # 简洁查看提交记录
~~~

推荐流程：

~~~bash
git pull
git status
# 修改文件
git diff
git add <文件>
git commit -m "fix: 修复问题"
git push
~~~

## 5. 分支操作

~~~bash
git branch                         # 查看本地分支
git branch <名称>                   # 创建分支
git switch <名称>                  # 切换分支
git switch -c <名称>               # 创建并切换分支
git merge <分支名>                 # 合并分支
git branch -d <名称>               # 删除已合并分支
git push -u origin <名称>          # 首次推送分支
~~~

旧版本 Git 也可使用 git checkout <名称> 切换分支、git checkout -b <名称> 创建并切换分支。

## 6. 远程同步

~~~bash
git fetch                         # 获取远程更新，不自动合并
git pull                          # 获取并合并远程更新
git push                          # 推送当前分支
git push -u origin main           # 首次推送 main 并建立跟踪关系
git branch -a                     # 查看本地和远程分支
~~~

一般情况下：先执行 git pull，完成本地提交后再执行 git push。

## 7. 撤销和恢复

~~~bash
git restore <文件>                # 丢弃工作区未暂存修改
git restore --staged <文件>       # 取消暂存，保留文件修改
git commit --amend                # 修改最近一次提交
git revert <提交ID>               # 通过新提交安全撤销历史提交
git reset --soft <提交ID>         # 回退提交，保留修改并保持暂存
git reset --hard <提交ID>         # 回退并丢弃之后的修改，谨慎使用
~~~

删除或覆盖文件前，先执行 git status 和 git diff 确认目标范围。

## 8. 查看历史

~~~bash
git show <提交ID>                  # 查看某次提交详情
git diff <提交A> <提交B>            # 比较两个提交
git log --oneline --graph --all     # 图形化查看完整分支历史
git blame <文件>                   # 查看每行最后修改者和提交
~~~

## 9. 合并冲突

~~~text
1. git pull 或 git merge 后发现冲突
2. 打开冲突文件，保留正确内容并删除 <<<<<<<、=======、>>>>>>> 标记
3. git add <已解决文件>
4. git commit                     # 完成合并
~~~

如果不想继续合并：

~~~bash
git merge --abort                 # 取消本次合并
~~~

## 10. .gitignore

在项目根目录创建 .gitignore，忽略不应提交的文件：

~~~gitignore
node_modules/
.env
dist/
*.log
~~~

已经被 Git 跟踪的文件不会因后来加入 .gitignore 自动停止跟踪，需要单独处理。

## 11. 常用提交类型

~~~text
feat: 新增功能
fix: 修复问题
docs: 更新文档
refactor: 重构代码
test: 增加测试
chore: 调整工具或配置
~~~

## 12. 一句话速记

~~~text
查看：git status
暂存：git add
提交：git commit
同步：git pull / git push
分支：git switch / git merge
撤销：git restore / git revert
~~~
