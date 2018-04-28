---
title: Git 基础命令
excerpt: |
  Git 基础命令
category: Linux
feature_image: "https://picsum.photos/2560/600?image=872"
---
### Git 常用命令，后面的命令是alias

`git config --global user.name "xxx"`：初始化用户名

`git config --global user.email "xxx@gmail.com"`：初始化邮箱

`git config --list`：查看当前所有的git 配置信息

#### Need Help？

- `git help <verb>`
- `git <verb> --help`

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

`git log --oneline`：按照一行，缩减形式显示日志

```
627a75e (HEAD -> master) update spring boot and test amend finished
7353640 udpate celery
1e0b818 update spring cloud not able to insert bean because of static factory method
2b460db add @RequestParam and @RequestBody differences
f13fa01 update
3bb15a9 update
8481c34 update
```

`git log --oneline --decorate`：一行显示日志  glo

`git commit --amend -m "Welcome To Facebook"`

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

`git remote show origin`：显示版本显示

### 视频教程

原文：[Git Tutorial for Beginners: Command-Line Fundamentals](https://www.youtube.com/watch?v=HVsySz-h9r4)

#### Git Distributed Version Control System

#### Check Version

- `git --version`

```
➜  Study git --version
git version 2.14.1
```

#### Setup Global Config Variables

- `git config --global user.name "xxx"`
- `git config --global user.email "xxx@gmail.com"`
- `git config --list`

```
git config --global user.name "xxx"：初始化用户名

git config --global user.email "xxx@gmail.com"：初始化邮箱

git config --list：查看当前所有的git 配置信息

➜  Study git config --list
...
user.name=wudixm
user.email=wxm66668888@163.com
...
```

#### Need Help?

- `git help <verb>`
- `git <verb> --help`

```
➜  Study git help config
➜  Study git config --help
```

#### Initialize a Repository From Existing Code

- `git init`

```
➜  local-repo ll -a
total 0
drwxr-xr-x  4 wuxiaoming  staff   136B  3 15 22:23 .
drwxr-xr-x  3 wuxiaoming  staff   102B  3 15 22:23 ..
-rw-r--r--  1 wuxiaoming  staff     0B  3 15 22:23 .project
-rw-r--r--  1 wuxiaoming  staff     0B  3 15 22:23 calc.py
➜  local-repo git init
Initialized empty Git repository in /Users/wuxiaoming/local/movies/myVideo/gitLession/local-repo/.git/
➜  local-repo git:(master) ✗ ll -a
total 0
drwxr-xr-x   5 wuxiaoming  staff   170B  3 15 22:24 .
drwxr-xr-x   3 wuxiaoming  staff   102B  3 15 22:23 ..
drwxr-xr-x  10 wuxiaoming  staff   340B  3 15 22:24 .git
-rw-r--r--   1 wuxiaoming  staff     0B  3 15 22:23 .project
-rw-r--r--   1 wuxiaoming  staff     0B  3 15 22:23 calc.py
➜  local-repo git:(master) ✗
```

.git 实际上是一个目录，包含所有和我们的repository 相关的东西，如果不想再利用git 来跟踪我们的项目，我们只需要删除.git 目录就好了。

#### Before Commit

- `git status`

```
➜  local-repo git:(master) ✗ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	.project
	calc.py

nothing added to commit but untracked files present (use "git add" to track)
```

如果有文件不想被跟踪，那么先在目录下创建一个.gitignore 文件，并在里面填写具体的文件名或者是通配符进行自动的匹配

```
➜  local-repo git:(master) ✗ touch .gitignore
```

将如下代码写入.gitignore 文件中：

```
.DS_Store
.project
*.pyc
```

再git status

```
➜  local-repo git:(master) ✗ gst
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	.gitignore
	calc.py

nothing added to commit but untracked files present (use "git add" to track)
➜  local-repo git:(master) ✗
```

#### 3 States we need to know about

1. Working Directory
   - Untracked and modified files will be in working directory
   - Will list that information when we run `git status`
2. Staging Area
   - Where we organize what we want to be commit to the Repository
3. .git Directory(Repository)

#### Add files to Staging Area

- git add -A：add all the files that are currently untracked or modified to the **Staging Area**

```
➜  local-repo git:(master) ✗ git add .gitignore
➜  local-repo git:(master) ✗ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

	new file:   .gitignore

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	calc.py

➜  local-repo git:(master) ✗
```

#### Remove files from Staging Area

- git reset: remove files from Staging Area

```
➜  local-repo git:(master) ✗ gst
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

	new file:   .gitignore
	new file:   calc.py

➜  local-repo git:(master) ✗ git reset calc.py
➜  local-repo git:(master) ✗ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

	new file:   .gitignore

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	calc.py

➜  local-repo git:(master) ✗
```

- `git reset`: if you want to remove everything from the Staging Area, use `git reset`

```
➜  local-repo git:(master) ✗ git reset
➜  local-repo git:(master) ✗ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	.gitignore
	calc.py

nothing added to commit but untracked files present (use "git add" to track)
```

#### Our first Commit

```
git add -A // add everything to the Staging Area
git commit -m 'Initial Commit' // message is importan, you should be detailed about the changes
git status // working directory is clean, we commit, then we dont have any modified or untracked files
git log  // we can see the commit we just made, we can see the author and Date and Message of the commit
```

#### Cloning a Remote Repo

```
git clone <url> <where to clone>
git clone ../remote_repo.git .
```

#### View Information About the Remote Repo

```
git remote -v
git branch -a
```

#### Push Changes

- commit changes like we did previously

```
git diff // changes I made to the code
git status  // working directory status
git add -A  // add to staging area
git commit -m 'message'  // commit file locally
```

- then push

```
git pull origin master  // people always forget the pull, it pulls any changes that`ve been made since the last time that we pull from the repository
git push origin master  // origin is the name of repository, master is the branch name
```

#### Create a Branch

```
git branch calc-divide
git checkout calc-divide
```

- `git branch`: will list all the branches of local repository

#### Push Branch to Remote

```
git push -u origin calc-divide
git branch -a

git pull
git push

-u tells git we want to associate local calc-divide branch with the remote calc-divide branch. Then in the future, instead of doing `git push origin calc-divide`, we can just do `git pull` and `git push`. Git will know those two branches are associated to each other.
```

#### Merge a Branch

```
git checkout master  // checkout local master branch
git pull origin master  // pull all the changes down, already up to date
git branch --merged  // the branches we have merged in so far
git merge calc-divide  // to merge the branch with master
git push origin master  // push those changes to the remote master branch
```

#### Deleting a Branch

```
git branch --merged
git branch -d calc-divide  // only delete branch locally
git branch -a
git push origin --delete calc-divide
```

#### Faster example

```
git branch subtract
git checkout substract
git status
git add -A
git commit -m 'subtract'
git push -u origin subtract
git checkout master
git pull
git merge substract
git push origin master
...
deleting
```

### 查看远程仓库

`git remote -v`

### Discard all local changes in your working directory

`git reset --hard HEAD`

### force pull

```
git fetch --all
git reset --hard origin/master
git pull origin master
git pull
```

### list

```
➜  _posts git:(master) ✗ git ls-files -d
➜  _posts git:(master) ✗ git ls-files -m
2017-10-21-Linux-Command.md
➜  _posts git:(master) ✗ gst
On branch master
Your branch is up-to-date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   2017-10-21-Linux-Command.md

no changes added to commit (use "git add" and/or "git commit -a")


OPTIONS
       -c, --cached
           Show cached files in the output (default)

       -d, --deleted
           Show deleted files in the output

       -m, --modified
           Show modified files in the output

       -o, --others
           Show other (i.e. untracked) files in the output

       -i, --ignored
           Show only ignored files in the output. When showing files in the index, print only those matched by an exclude pattern. When showing "other" files,
           show only those matched by an exclude pattern.
:
```

