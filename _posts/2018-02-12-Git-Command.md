---
title: Git 基础命令
excerpt: |
  Git 基础命令
category: Linux
feature_image: "https://picsum.photos/2560/600?image=872"
---
### Git 常用命令，后面的命令是alias

`git add`：添加文件到本地缓存区，ga

`git add -a`：添加所有文件到本地缓存区，gaa

`git branch -r`：显示远程branch，gbr

`git branch -a`：显示本地远程branch，gba

`git branch [name]`：新建一个分支，但依然停留在当前分支，gb name

`git branch -b [name]`：# 新建一个分支，并切换到该分支新，gb -b name

`git commit -m "some message"`：提交暂存区到仓库区，提交代码，gcmsg

`git commit -a -m "some message"`：提交暂存区到仓库区，提交所有代码，gcam

`git checkout [branch]`：检出一个分支，gco

`git diff`：显示暂存区和工作区的差异，查看diff，gd

`git diff —cached`：显示暂存区和上一个commit的差异，gdca


`git diff —cached`：gdca

git describe --tags \`git rev-list --tags --max-count=1\` : gdct

`git diff --cached --word-diff`：gdcw
`git diff --word-diff`：gdw

`git fetch [remote]`：下载远程仓库的所有变动，gf

`git help`：帮助，ghh

`git log —stat`：显示commit历史，以及每次commit发生变更的文件，glg

`git merge [branch]`：合并指定分支到当前分支，gm [branch]

`git push [remote] [branch]`：上传本地指定分支到远程仓库，gp 

`git push origin $(git_current_branch)`：推送本地分支到远程仓库，ggpush

`git remote -v`：显示所有远程仓库，grv

`git status -sb`：简略显示带有分支名的当前状态信息，gsb

`git status -s`：简略显示当前状态信息，gss

`git status`：详细显示当前状态，gst

`git diff --shortstat "@{0 day ago}"`：显示今天你写了多少行代码

`git show [commit]`：显示某次提交的元数据和内容变化

`git show —name-only [commit]`：显示某次提交发生变化的文件

`git show [commit]:[filename]`：显示某次提交时，某个文件的内容

