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

### delete untracked files

```
Simple Way to remove untracked files
To remove all untracked files, The simple way is to add all of them first and reset the repo as below

git add --all
git reset --hard HEAD
```

### cherry-pick

**简单用法：**
git cherry-pick <commit id>
**注意：当执行完 cherry-pick 以后，将会生成一个新的提交；这个新的提交的哈希值和原来的不同，但标识名一样；**

**例如：**
$ git checkout v2.0分支
$ git cherry-pick 38361a55    # 这个 38361a55 号码，位于v3.0分支中：

> $ git log
> commit 38361a55138140827b31b72f8bbfd88b3705d77a
> Author: Justin [Justin@xxx.com](https://link.jianshu.com?t=mailto:Justin@xxx.com)
> Date:   Sat Dec 10 00:11:44 2016 +0800

**1. 如果顺利，就会正常提交**。结果：

> Finished one cherry-pick.
> On branch v2.0分支
> Your branch is ahead of 'origin/old_cc' by 3 commits.

**2. 如果在cherry-pick 的过程中出现了冲突**

> Automatic cherry-pick failed.
> After resolving the conflicts,mark the corrected paths with 'git add <paths>' or 'git rm <paths>'and commit the result with:
> git commit -c 15a2b6c61927e5aed6111de89ad9dafba939a90b
> **或者:**
> error: could not apply 0549563... dev
> hint: after resolving the conflicts, mark the corrected paths
> hint: with 'git add <paths>' or 'git rm <paths>'
> hint: and commit the result with 'git commit'

就跟普通的冲突一样，手工解决：

**2.1 $ git status    # 看哪些文件出现冲突**

> both modified:      app/models/MainActivity.java

**2.2 $ vim app/models/MainActivity.java  # 手动解决它。 **

**2.3 $ git add app/models/MainActivity.java**

**2.4 git commit -c <新的commit号码>**

**2.5 再次cherry-pick剩余commit**

若提示:

> error: a cherry-pick or revert is already in progress
> hint: try "git cherry-pick (--continue | --quit | --abort)"
> fatal: cherry-pick failed

则执行对应操作:

> *git cherry-pick* --continue
> *git cherry-pick* --quit
> *git cherry-pick* --abort

------

命令集合:

- git cherry-pick <commit id>:单独合并一个提交
- git cherry-pick  -x <commit id>：同上，不同点：保留原提交者信息。
  **Git从1.7.2版本开始支持批量cherry-pick，就是一次可以cherry-pick一个区间的commit。**
- git cherry-pick <start-commit-id>..<end-commit-id>
- git cherry-pick <start-commit-id>^..<end-commit-id>

前者表示把<start-commit-id>到<end-commit-id>之间(左开右闭，不包含start-commit-id)的提交cherry-pick到当前分支；
后者有"^"标志的表示把<start-commit-id>到<end-commit-id>之间(闭区间，包含start-commit-id)的提交cherry-pick到当前分支。
其中，<start-commit-id>到<end-commit-id>只需要commit-id的前6位即可，并且<start-commit-id>在时间上必须早于<end-commit-id>
注：以上合并，需要手动push代码。

### git stash

```
git stash apply
git stash apply stash@{2}
If you don’t specify a stash, Git assumes the most recent stash and tries to apply it:

git stash drop   remove stash
$ git stash drop stash@{0}
Dropped stash@{0} (364e91f3f268f0900bc3ee613f9f733e82aaed43)

git stash pop
You can also run git stash pop to apply the stash and then immediately drop it from your stack.


```

### discard changes in working directory

```
root@mng-68:/data3/src/colorv-microservice-bot/colorv-bot# git status
On branch bot
Your branch is behind 'origin/bot' by 1 commit, and can be fast-forwarded.
  (use "git pull" to update your local branch)
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   Dockerfile

git checkout -- file
```

### Copy last commit hash

```
git log --pretty=format:'%h' -n 1 | pbcopy
git log --pretty=format:'%H' -n 1 | pbcopy
see also git log --help -> PRETTY FORMAT
```

### git ignore 文件不起作用

```
➜  template git:(master) ✗ gst
On branch master
Your branch is up-to-date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   .idea/template.iml
	modified:   .idea/workspace.xml
	modified:   translate/model.py
	modified:   translate_template.py

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	data/
	desc.json

no changes added to commit (use "git add" and/or "git commit -a")
➜  template git:(master) ✗ git checkout -- .idea/template.iml

可能是因为IDE 或者其它方式，自己把.idea 文件夹下的文件添加到了暂存区，所以看上去一直显示，利用
git checkout -- filename
去掉修改即可
```

### Git 回退到某个commit

[原文](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013744142037508cf42e51debf49668810645e02887691000)

好了，现在我们启动时光穿梭机，准备把`readme.txt`回退到上一个版本，也就是`add distributed`的那个版本，怎么做呢？

首先，Git必须知道当前版本是哪个版本，在Git中，用`HEAD`表示当前版本，也就是最新的提交`1094adb...`（注意我的提交ID和你的肯定不一样），上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个`^`比较容易数不过来，所以写成`HEAD~100`。

现在，我们要把当前版本`append GPL`回退到上一个版本`add distributed`，就可以使用`git reset`命令：

```
$ git reset --hard HEAD^
HEAD is now at e475afc add distributed

```

`--hard`参数有啥意义？这个后面再讲，现在你先放心使用。

看看`readme.txt`的内容是不是版本`add distributed`：

```
$ cat readme.txt
Git is a distributed version control system.
Git is free software.

```

果然被还原了。

还可以继续回退到上一个版本`wrote a readme file`，不过且慢，然我们用`git log`再看看现在版本库的状态：

```
$ git log
commit e475afc93c209a690c39c13a46716e8fa000c366 (HEAD -> master)
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:03:36 2018 +0800

    add distributed

commit eaadf4e385e865d25c48e7ca9c8395c3f7dfaef0
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 20:59:18 2018 +0800

    wrote a readme file

```

最新的那个版本`append GPL`已经看不到了！好比你从21世纪坐时光穿梭机来到了19世纪，想再回去已经回不去了，肿么办？

办法其实还是有的，只要上面的命令行窗口还没有被关掉，你就可以顺着往上找啊找啊，找到那个`append GPL`的`commit id`是`1094adb...`，于是就可以指定回到未来的某个版本：

```
$ git reset --hard 1094a
HEAD is now at 83b0afe append GPL

```

版本号没必要写全，前几位就可以了，Git会自动去找。当然也不能只写前一两位，因为Git可能会找到多个版本号，就无法确定是哪一个了。

再小心翼翼地看看`readme.txt`的内容：

```
$ cat readme.txt
Git is a distributed version control system.
Git is free software distributed under the GPL.

```

果然，我胡汉三又回来了。

Git的版本回退速度非常快，因为Git在内部有个指向当前版本的`HEAD`指针，当你回退版本的时候，Git仅仅是把HEAD从指向`append GPL`：

![git-head](https://cdn.liaoxuefeng.com/cdn/files/attachments/001384907584977fc9d4b96c99f4b5f8e448fbd8589d0b2000/0)

改为指向`add distributed`：

![git-head-move](https://cdn.liaoxuefeng.com/cdn/files/attachments/001384907594057a873c79f14184b45a1a66b1509f90b7a000/0)

然后顺便把工作区的文件更新了。所以你让`HEAD`指向哪个版本号，你就把当前版本定位在哪。

现在，你回退到了某个版本，关掉了电脑，第二天早上就后悔了，想恢复到新版本怎么办？找不到新版本的`commit id`怎么办？

在Git中，总是有后悔药可以吃的。当你用`$ git reset --hard HEAD^`回退到`add distributed`版本时，再想恢复到`append GPL`，就必须找到`append GPL`的commit id。Git提供了一个命令`git reflog`用来记录你的每一次命令：

```
$ git reflog
e475afc HEAD@{1}: reset: moving to HEAD^
1094adb (HEAD -> master) HEAD@{2}: commit: append GPL
e475afc HEAD@{3}: commit: add distributed
eaadf4e HEAD@{4}: commit (initial): wrote a readme file

```

终于舒了口气，从输出可知，`append GPL`的commit id是`1094adb`，现在，你又可以乘坐时光机回到未来了。

### 工作中reset hard

```
➜  cv git:(instantCelery) git reset --hard 0b0eb48fe5e4d469cbcd6b95d8e962b701ee57fb
HEAD is now at 0b0eb48fe bug fix for kind is script not video
➜  cv git:(instantCelery) gst
On branch instantCelery
Your branch is behind 'origin/instantCelery' by 84 commits, and can be fast-forwarded.
  (use "git pull" to update your local branch)

nothing to commit, working tree clean
➜  cv git:(instantCelery) git log
➜  cv git:(instantCelery) gp
To code.aliyun.com:cvteam/cv.git
 ! [rejected]            instantCelery -> instantCelery (non-fast-forward)
error: failed to push some refs to 'git@code.aliyun.com:cvteam/cv.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.

刚刚reset hard 过后，利用git status 肯定会显示your branch is behind origin  by xxx commits。因为本地的已经回退到前面的版本，这个时候如果想push 上去，肯定是不允许的，如上面hint 中的第一点，reject... your branch is behind...

这个时候我用过 git push -f ，不确定这样是不是正确的操作，但是我只是想要硬回退到某个commit 上，远程也需要回退，所以目的达到了。

➜  cv git:(instantCelery) git push -f
Total 0 (delta 0), reused 0 (delta 0)
To code.aliyun.com:cvteam/cv.git
 + 406733b05...0b0eb48fe instantCelery -> instantCelery (forced update)
➜  cv git:(instantCelery) gst
On branch instantCelery
Your branch is up-to-date with 'origin/instantCelery'.

nothing to commit, working tree clean
➜  cv git:(instantCelery) gl
Already up-to-date.
```

### fetch 清空本地对远程分支的缓存

**问题：git delete remotes: remote refs do not exist**

```
You may need to prune your local "cache" of remote branches first. Try running:

git fetch -p origin

before deleting.


➜  mngcv git:(groupNewReqScript) git push origin --delete group
error: unable to delete 'group': remote ref does not exist
error: failed to push some refs to 'git@code.aliyun.com:cvteam/mngcv.git'
➜  mngcv git:(groupNewReqScript) git fetch -p origin



From code.aliyun.com:cvteam/mngcv
 - [deleted]           (none)     -> origin/group
 - [deleted]           (none)     -> origin/group_active_update
 - [deleted]           (none)     -> origin/instantCelery
 - [deleted]           (none)     -> origin/javaverifyhot
 - [deleted]           (none)     -> origin/removeDigestBug
 - [deleted]           (none)     -> origin/wxGroupDailyScript
➜  mngcv git:(groupNewReqScript)

```

### Git stash

```
git stash list [<options>]
git stash show [<stash>]
git stash drop [-q|--quiet] [<stash>]
git stash ( pop | apply ) [--index] [-q|--quiet] [<stash>]
git stash branch <branchname> [<stash>]
git stash [push [-p|--patch] [-k|--[no-]keep-index] [-q|--quiet]
	     [-u|--include-untracked] [-a|--all] [-m|--message <message>]
	     [--] [<pathspec>…​]]
git stash clear
git stash create [<message>]
git stash store [-m|--message <message>] [-q|--quiet] <commit>
```

### git rm

取消跟踪某文件，如果有一些`.iml` 或者idea 自动自成的`xml` 文件被提交到了仓库里，可以利用`git rm` 命令去掉跟踪

```
➜  quiztest1 git:(master) git rm .idea/workspace.xml
rm '.idea/workspace.xml'
➜  quiztest1 git:(master) ✗ git rm quiztest1.iml
rm 'quiztest1.iml'
➜  quiztest1 git:(master) ✗ gst
On branch master
Your branch is up-to-date with 'origin/master'.

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	deleted:    .idea/workspace.xml
	deleted:    quiztest1.iml

➜  quiztest1 git:(master) ✗ gc -m 'delete xml iml file'
[master d305faf] delete xml iml file
 2 files changed, 1193 deletions(-)
 delete mode 100644 .idea/workspace.xml
 delete mode 100644 quiztest1.iml
➜  quiztest1 git:(master)
```

