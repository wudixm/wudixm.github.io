---
title: Linux 命令
excerpt: |
  Linux 命令
category: Linux
feature_image: "https://picsum.photos/2560/600?image=872"
---
### Linux返回上一次目录

有时候千辛万苦进入了一个很深层的目录，一不小心输入了cd并回车，有什么办法快速回到刚才所在的目录呢？对于bash来说，只需要很管理的一个命令：

cd -

该命令等同于`cd $OLDPWD`，关于这一点在bash的手册页(可使用命令man bash访问其手册页)中有介绍：

```
An argument of - is equivalent to $OLDPWD. 
```

并且它还会返回上一次目录的物理路径。
### 拷贝输出的目录

##### 第一种方式
利用grep先查找到文件，再利用pbcopy 将文件名保存到缓冲区(clipboard buffer)，

ls | grep -i vim | pbcopy

再利用pbpaste获得上一步的结果

vim `pbpaste`

注意：
1. grep -i 可以忽略大小写，case-insensetive
2. pbpaste的两边要加上“`” 符号，位于左上边1 键的左边

##### 第二种方式

find . -iname *vim* -exec vim {} +

注意：
1. -exec是find命令的一个参数，具体可以  man find

##### 第三种方式

vim $(ls | grep -i vim)

##### 第四种方式

ls | grep -i vim | xargs vim

注意：
第四种方式会产生一个问题，退出后命令不再显示，如下有解释


Kote Dekuur wrote: 

> Have anyone seen this error before? 
>
> Vim: Warning: Output is not to a terminal 
>
> I'm writing a bash script to do merges in Mercurial and using vimdiff as 
>  the merge tool.   When there are merge conflicts that requires manual 
> interactions it will open up vimdiff of the two files, but it seems to 
> hang on 
>
> Vim: Warning: Output is not to a terminal   and the terminal is stuck. 
>
> I have no problems executing vimdiff <file1> <file2> 
> manually.  This only happens when running it through a bash script.  I'm 
>  not sure if I'm missing an environment setting somewhere.

If you redirect Vim input it will read commands from there.  If the 
stdin actually contains text use the "-" argument, e.g.: 

        ls . | vim - 

If you redirect Vim output you get the message you mentioned.  You then 
can't see what you are doing, but you can still edit. 

Try typing ":qa!".  If only output was redirected it should get you out 
of the stuck situation. 

If both input and output are redirected, you are really stuck.

#### 前后几行

```
grep -A 5 可以显示匹配内容以及后面的5行内容
grep -B 5 可以显示匹配内容以及前面的5行内容
grep -C 5 可以显示匹配内容以及前后面的5行内容

```



### 显示磁盘空间

以下命令可以查看磁盘各分区大小、已用空间等信息：

```
df -h
```

以下命令可以查看foo目录的大小：

```
du -sh foo
```

有时候，硬盘比较满了，我们想找一些目录来清除，可以用下面命令查看当前目录以下搜索文件和子目录大小。找出特别大的，看里面有没有文件可删：

```
du -sh *
```

如果我们插入了一个U盘或移动硬盘，可以用df命令查看它挂载的地方，通常在/mnt或/media下。如果想卸载USB存储设备，可以用umount命令：

```
umount path
```

以上命令更多参数可查看man或info。相比起man，info功能更强大，并且更适合盲人使用。info的用法和man一样，info后面跟命令名称。进入info界面后，会有一个光标，可以通过键盘方向键移动来读取内容，按ESC+右方向键可以跳到下一个英文单词，按ESC+左方向键可以跳到上一个英文单词。按q退出，按h进入帮助页面，查看更多快捷键。info不是一页纸，而像一本书，里面的内容有链接，可以随时跳到相关的内容页去

### vim中跨文件的多行拷贝

1、用vim打开一个文件，例如：original.trace

2、在普通模式下，输入：":sp"（不含引号）横向切分一个窗口，或者":vsp"纵向切分一个窗口，敲入命令后，你将看到两个窗口打开的是同一个文件

3、在普通模式下，输入：":e new.trace"，在其中一个窗口里打开另一个文件

4、切换到含有源文件（original.trace）的窗口，在普通模式下，把光标移到你需要复制内容的起始行，然后输入你想复制的行的数量（从光标所在行往下计算），在行数后面接着输入yy，这样就将内容复制到临时寄存器里 了（在 普通模式下ctrl+w，再按一下w，可以在两个窗口之间切换）

5、切换到目标文件（new.trace）窗口，把光标移到你接收复制内容的起始行，按一下p，就完成复制了。

### 显示进程

ps ax | grep get

### 统计文件的行数

语法：wc [选项] 文件…

说明：该命令统计给定文件中的字节数、字数、行数。如果没有给出文件名，则从标准输入读取。wc同时也给出所有指定文件的总统计数。字是由空格字符区分开的最大字符串。

该命令各选项含义如下：

　　- c 统计字节数。

　　- l 统计行数。

　　- w 统计字数。

这些选项可以组合使用。

输出列的顺序和数目不受选项的顺序和数目的影响。

总是按下述顺序显示并且每项最多一列。

行数、字数、字节数、文件名

如果命令行中没有文件名，则输出中不出现文件名。

例如：

`$ wc - lcw file1 file24 33 file17 52 file211 11 85 total`

### 免密码

Before using Git add your key to **ssh-agent**

Start **ssh-agent** if not started:

> $ eval ``ssh-agent -s``

Add your **private key** using **ssh-add**

> $ ssh-add ~/.ssh/id_rsa_key
> Enter passphrase for /home/user/.ssh/id_rsa_key:
> Identity added: /home/user/.ssh/id_rsa_key
> (/home/user/.ssh/id_rsa_key)

Check if the key is added (parameter is a lowercase L):

> $ ssh-add -l
> 2048 55:96:1a:b1:31:f6:f0:6f:d8:a7:49:1a:e5:4c:94:6f
> /home/user/.ssh/id_rsa_key (RSA)

Try to connect to your Git server:

> $ ssh git.example.com

Now you can use Git without extra passphrase prompts.



```
➜  ~ ssh-agent -s
SSH_AUTH_SOCK=/var/folders/dn/sk1cwjps05b5j7dlgk16bcwm0000gn/T//ssh-B49vYHRkU2pj/agent.836; export SSH_AUTH_SOCK;
SSH_AGENT_PID=837; export SSH_AGENT_PID;
echo Agent pid 837;
➜  ~ ssh-add .ssh/id_rsa    ## 这个是正在用的
Enter passphrase for .ssh/id_rsa: hoytwu
Identity added: .ssh/id_rsa (.ssh/id_rsa)
```



### 查看端口占用

Mac下使用lsof（list open files）来查看端口占用情况，lsof 是一个列出当前系统打开文件的工具。

使用 lsof 会列举所有占用的端口列表：

使用less可以用于分页展示，如：

 

也可以使用 -i 查看某个端口是否被占用，如：

如果端口被占用，则会返回相关信息，如果没被占用，则不返回任何信息



### 2>&1 含义**

先看一个例子：

0 2 * * * /u01/test.sh >/dev/null 2>&1 &

这句话的意思就是在后台执行这条命令，并将错误输出2重定向到标准输出1，然后将标准输出1全部放到/dev/null 文件，也就是清空。

在这里有有几个数字的意思：

 0表示键盘输入

 1表示标准输出

 2表示错误输出



### linux压缩和解压缩命令大全

​        .tar

　　解包：tar zxvf FileName.tar

　　打包：tar czvf FileName.tar DirName

---------------------------------------------

　　.gz

　　解压1：gunzip FileName.gz

　　解压2：gzip -d FileName.gz

　　压缩：gzip FileName

　　.tar.gz 和 .tgz

　　解压：tar zxvf FileName.tar.gz

　　压缩：tar zcvf FileName.tar.gz DirName

---------------------------------------------

　　.bz2

　　解压1：bzip2 -d FileName.bz2

　　解压2：bunzip2 FileName.bz2

　　压缩： bzip2 -z FileName

　　.tar.bz2

　　解压：tar jxvf FileName.tar.bz2

　　压缩：tar jcvf FileName.tar.bz2 DirName

---------------------------------------------

　　.bz

　　解压1：bzip2 -d FileName.bz

　　解压2：bunzip2 FileName.bz

　　压缩：未知

　　.tar.bz

　　解压：tar jxvf FileName.tar.bz

　　压缩：未知

---------------------------------------------

　　.Z

　　解压：uncompress FileName.Z

　　压缩：compress FileName

　　.tar.Z

　　解压：tar Zxvf FileName.tar.Z

　　压缩：tar Zcvf FileName.tar.Z DirName

---------------------------------------------

　　.zip

　　解压：unzip FileName.zip

　　压缩：zip FileName.zip DirName

---------------------------------------------

　　.rar

　　解压：rar a FileName.rar

　　压缩：r ar e FileName.rar

　　

　　rar请到：

http://www.rarsoft.com/download.htm

 下载！

　　解压后请将rar_static拷贝到/usr/bin目录（其他由$PATH环境变量指定的目录也可以）：

　　[root@www2 tmp]# cp rar_static /usr/bin/rar

---------------------------------------------

　　.lha

　　解压：lha -e FileName.lha

　　压缩：lha -a FileName.lha FileName

　　

　　lha请到：

http://www.infor.kanazawa-it.ac.jp/~ishii/lhaunix/

下载！

　　>解压后请将lha拷贝到/usr/bin目录（其他由$PATH环境变量指定的目录也可以）：

　　[root@www2 tmp]# cp lha /usr/bin/

---------------------------------------------

　　.rpm

　　解包：rpm2cpio FileName.rpm | cpio -div

---------------------------------------------

　　.deb

　　解包：ar p FileName.deb data.tar.gz | tar zxf -

---------------------------------------------

#### jar 文件

➜  classes unzip colorv-bot-0.0.1-SNAPSHOT.jar

### 后台执行

```
root@mng-135:/data1/hoytwu/falsePlay/monitor# nohup tail -f ../../../cv-dockers/colorv-bot/cv-bot.log  | python monitor.py > monitorLog.log &
[1] 23211
root@mng-135:/data1/hoytwu/falsePlay/monitor# nohup: ignoring input and redirecting stderr to stdout
```

### 创建软连接

```
ln -s  /data1/hoytwu/falsePlay/monitor/supervisorFile/configfile cv.bot.event.monitor.conf
```

### alias

```
我们在使用Linux中使用较长的命令而且要经常要使用时，总是会使用别名，这里就简单的介绍一下别名alias

指令：alias
设置指令的别名

语法：#  alias name='command line'

参数：
-p：    打印出现有的别名（唯一的参数）
若不加任何参数，则列出目前所有的别名设置

用法：
# alias cp='cp -i'
这样就可以用cp来代替cp -i，而且cp -i这条命令依旧有效

查看alias：
列出目前所有的别名设置。
# alias    或    # alias -p

查看具体一条指令的别名
# alias cp

别名永久化：
alias的作用仅在该次登入的操作，即输入一次alias后，这个修改只在当前的Shell生效。如果重新开启一个 Shell，或者重新登录，则这些alias将无法使用。好在linux中提供alias永久化的方法：
<1>.若要每次登入就自动生效别名，则把别名加在/etc/profile或~/.bashrc中。然后# source ~/.bashrc
<2>.若要让每一位用户都生效别名，则把别名加在/etc/bashrc最后面，然后# source /etc/bashrc

重新设置别名：
alias重置的方法与第一次设置的时候一样
# alias cp='cp -i'

删除别名：
格式：unalias name

#  unalias cp

重新创建alias
vi ~/.bashrc

```

### 新添加alias

执行`vi ~/.bashrc`，当前内容如下：

```
# .bashrc

# User specific aliases and functions

alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
alias l="ls -al"
alias git='LANG=en_GB git'
alias gst='git status'

# Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi
```





### grep 

```
grep -C 5 foo file 显示file文件里匹配foo字串那行以及上下5行
grep -B 5 foo file 显示foo及前5行
grep -A 5 foo file 显示foo及后5行

查看grep版本号的方法是
grep -V

假设想升级，升级的方法：最新的源代码（google或者百度搜索主页），编译安装到某个地方，比方 /home/aaa/bin/ 那么以后用的时候就用 /home/aaa/bin/grep ，或者把 /home/aaa/bin 加到PATH环境变量就可以。 假设你把最新编译好的grep覆盖到你如今grep所在文件夹，则升级自然就完成了


不包含字符串用 -v，如不包含gz 字符串的文件
access_api.log.20180626_57.log.gz  access.log.20180625_39.log.gz              report_client.log.20180624_19.log.gz          report_push.log.20180625_14.log.gz
access_api.log.20180626_58.log.gz  access.log.20180625_3.log.gz               report_client.log.20180624_1.log.gz           report_push.log.20180625_15.log.gz
access_api.log.20180626_59.log.gz  access.log.20180625_40.log.gz              report_client.log.20180624_20.log.gz          report_push.log.20180625_16.log.gz
root@mng-68:/data1/log/cv# ll | grep -v gz
total 58336536
drwxrwxrwx 2 root     root      25452544 Jun 28 09:42 ./
drwxr-xr-x 7 root     root          4096 Feb 28 17:47 ../
-rw-r--r-- 1 td-agent td-agent   3245412 Jun 24 06:25 error.log.20180624_0.log
-rw-r--r-- 1 td-agent td-agent  17492032 Jun 25 00:10 error.log.20180624_1.log


-n, --line-number
             Each output line is preceded by its relative line number in the file, starting at line 1.  The line number counter is reset for each file pro-
             cessed.  This option is ignored if -c, -L, -l, or -q is specified.
```

### find

```
find   -name april*                     在当前目录下查找以april开始的文件
find   -name   april*   fprint file        在当前目录下查找以april开始的文件，并把结果输出到file中
find   -name ap* -o -name may*   查找以ap或may开头的文件
find   /mnt   -name tom.txt   -ftype vfat   在/mnt下查找名称为tom.txt且文件系统类型为vfat的文件
find   /mnt   -name t.txt ! -ftype vfat   在/mnt下查找名称为tom.txt且文件系统类型不为vfat的文件
find   /tmp   -name wa* -type l            在/tmp下查找名为wa开头且类型为符号链接的文件
find   /home   -mtime   -2                 在/home下查最近两天内改动过的文件
find /home    -atime -1                  查1天之内被存取过的文件
find /home -mmin    +60                  在/home下查60分钟前改动过的文件
find /home   -amin   +30                  查最近30分钟前被存取过的文件
find /home   -newer   tmp.txt             在/home下查更新时间比tmp.txt近的文件或目录
find /home   -anewer   tmp.txt            在/home下查存取时间比tmp.txt近的文件或目录
find   /home   -used   -2                  列出文件或目录被改动过之后，在2日内被存取过的文件或目录
find   /home   -user cnscn                列出/home目录内属于用户cnscn的文件或目录
find   /home   -uid   +501                  列出/home目录内用户的识别码大于501的文件或目录
find   /home   -group   cnscn              列出/home内组为cnscn的文件或目录
find   /home   -gid 501                   列出/home内组id为501的文件或目录
find   /home   -nouser                    列出/home内不属于本地用户的文件或目录
find   /home   -nogroup                   列出/home内不属于本地组的文件或目录
find   /home    -name tmp.txt    -maxdepth   4   列出/home内的tmp.txt 查时深度最多为3层
find   /home   -name tmp.txt   -mindepth   3   从第2层开始查
find   /home   -empty                     查找大小为0的文件或空目录
find   /home   -size   +512k                查大于512k的文件
find   /home   -size   -512k               查小于512k的文件
find   /home   -links   +2                查硬连接数大于2的文件或目录
find   /home   -perm   0700                查权限为700的文件或目录
find   /tmp   -name tmp.txt   -exec cat {} \;
find   /tmp   -name   tmp.txt   -ok   rm {} \;

find    /   -amin    -10     # 查找在系统中最后10分钟访问的文件
find    /   -atime   -2        # 查找在系统中最后48小时访问的文件
find    /   -empty             # 查找在系统中为空的文件或者文件夹
find    /   -group   cat        # 查找在系统中属于 groupcat的文件
find    /   -mmin   -5         # 查找在系统中最后5分钟里修改过的文件
find    /   -mtime   -1       #查找在系统中最后24小时里修改过的文件
find    /   -nouser           #查找在系统中属于作废用户的文件
find    /   -user    fred     #查找在系统中属于FRED这个用户的文件

查当前目录下的所有普通文件
# find . -type f -exec ls -l {} \; 
-rw-r–r–    1 root      root         34928 2003-02-25   ./conf/httpd.conf 
-rw-r–r–    1 root      root         12959 2003-02-25   ./conf/magic 
-rw-r–r–    1 root      root          180 2003-02-25   ./conf.d/README 
查当前目录下的所有普通文件，并在- e x e c选项中使用ls -l命令将它们列出
```

### gzip

```
gzip 是linux中常见的压缩/解压工具，最常见的使用对象是*.gz格式的文件，这里简单介绍下它最常见的用法，

GZIP(1) General Commands Manual GZIP(1)

NAME
     gzip, gunzip, zcat - compress or expand files

SYNOPSIS
     gzip [ -acdfhklLnNrtvV19 ] [--rsyncable] [-S suffix] [ name ... ]
     gunzip [ -acfhklLnNrtvV ] [-S suffix] [ name ... ]
     zcat [ -fhLV ] [ name ... ]

OPTIONS
     -c --stdout --to-stdout 结果写到标准输出，原文件保持不变
     -d --decompress --uncompress 解压
     -k --keep 压缩或者解压过程中，保留原文件
     -r --recursive
     -t --test 检查压缩文件的完整性
     -v --verbose 显示每个文件的名子和压缩率
     -# --fast --best 取值从-1(最快)到-9(最好)，默认是-6

示例1，压缩文件
原文件名为file1.txt，压缩后原文件消失，压缩后文件名为file1.txt.gz
root@ubuntu:/tmp# ls -l file1.*
-rw-r--r-- 1 root root 12383865 Aug 21 08:08 file1.txt
root@ubuntu:/tmp# gzip file1.txt
root@ubuntu:/tmp# ls -l file1.*
-rw-r--r-- 1 root root 134416 Aug 21 08:08 file1.txt.gz

示例2，解压文件
root@ubuntu:/tmp# gzip -d file1.txt.gz
root@ubuntu:/tmp# ls -lh file1.*
-rw-r--r-- 1 root root 12M Aug 21 08:08 file1.txt

示例3，压缩的时候，显示压缩率
root@ubuntu:/tmp# gzip -v file1.txt
file1.txt: 98.9% -- replaced with file1.txt.gz

示例4，一条命令压缩多个文件，压缩之后，是各自分开的：
root@ubuntu:/tmp# gzip file1.txt file2.txt
root@ubuntu:/tmp# ls -l
total 1348
-rw-r--r-- 1 root root 134416 Aug 21 08:08 file1.txt.gz
-rw-r--r-- 1 root root 392 Aug 21 08:15 file2.txt.gz

示例5，压缩过程中，保留原文件
root@ubuntu:/tmp# gzip -k file1.txt
root@ubuntu:/tmp# ls file1.*
file1.txt file1.txt.gz

示例6，压缩到标准输出中
可以连接两个文件
root@ubuntu:/tmp# cat file1.txt file2.txt | gzip > foo.gz
或者
root@ubuntu:/tmp# gzip -c file1.txt file2.txt > foo.gz

```

### tar

```
tar -zcvf archive.tar.gz directory/ 
```

### √分文件压缩

```
假设当前目录下有proc目录。

tar -czf - proc | split -b 2m -d - proc.tar.gz #分卷压缩proc目录，并保持每个压缩包的大小不超过2m字节。命令执行后，会生成proc.tar.gz00、proc.tar.gz01等文件

cat proc.tar.gz* | tar -xzf - #将各个分卷压缩包解压到当前目录

cat proc.tar.gz* > proc.tar.gz #将各个分卷压缩包合成为一个proc.tar.gz文件
```

### scp

```
// 本地拷贝到远程上去
scp -P 2222 backs.tar.gzb* cotilla@captain-cotilla.ddns.net:/data1/deploy/dmgr/material/

//远程到本地
scp root@tapi.colorv.cn:/data2/filerepository/resources/backup.tar.gz .
```

### tmux

```
常用的快捷键
session指的是按下tmux命令后 存在的连接便是session

//创建session
tmux
//创建并指定session名字
tmux new -s $session_name
//删除session
Ctrl+b :kill-session
//临时退出session
Ctrl+b d
//列出session
tmux ls
//进入已存在的session
tmux a -t $session_name
//删除所有session
Ctrl+b :kill-server
//删除指定session
tmux kill-session -t $session_name
window在session里，可以有N个window，并且window可以在不同的session里移动

//创建window
Ctrl+b +c

//删除window
Ctrl+b &

//下一个window
Ctrl+b n

//上一个window
Ctrl+b p

//重命名window
Ctrl+b ,

//在多个window里搜索关键字
Ctrl+b f

//在相邻的两个window里切换
Ctrl+b l
pane在window里，可以有N个pane，并且pane可以在不同的window里移动、合并、拆分

//创建pane
//横切split pane horizontal
Ctrl+b ” (问号的上面，shift+’)

//竖切split pane vertical
Ctrl+b % （shift+5）

//按顺序在pane之间移动
Ctrl+b o

//上下左右选择pane
Ctrl+b 方向键上下左右

//调整pane的大小
Ctrl+b :resize-pane -U #向上
Ctrl+b :resize-pane -D #向下
Ctrl+b :resize-pane -L #向左
Ctrl+b :resize-pane -R #向右
在上下左右的调整里，最后的参数可以加数字 用以控制移动的大小，例如：
Ctrl+b :resize-pane -D 50

//在同一个window里左右移动pane
Ctrl+b { （往左边，往上面）
Ctrl+b } （往右边，往下面）

//删除pane
Ctrl+b x
//更换pane排版
Ctrl+b “空格”

//移动pane至window
Ctrl+b !

//移动pane合并至某个window
Ctrl+b :join-pane -t $window_name

//显示pane编号
Ctrl+b q

//按顺序移动pane位置
Ctrl+b Ctrl+o
其他：

复制模式
Ctrl+b [
空格标记复制开始，回车结束复制。

//粘贴最后一个缓冲区内容
Ctrl+b ]

//选择性粘贴缓冲区
Ctrl+b =

//列出缓冲区目标
Ctrl+b :list-buffer

//查看缓冲区内容
Ctrl+b :show-buffer

//vi模式
Ctrl+b :set mode-keys vi

//显示时间
Ctrl+b t

//快捷键帮助
Ctrl+b ? (Ctrl+b :list-keys)

//tmux内置命令帮助
Ctrl+b :list-commands

作者：Hanrydy
链接：https://www.jianshu.com/p/71999b35ead7
來源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

### rsync

```
rsync -r dir1/ dir2
The -r option means recursive, which is necessary for directory syncing.

rsync -a dir1/ dir2
The -a option is a combination flag. It stands for "archive" and syncs recursively and preserves symbolic links, special and device files, modification times, group, owner, and permissions. It is more commonly used than -r and is usually what you want to use.


Always double-check your arguments before executing an rsync command. Rsync provides a method for doing this by passing the -n or --dry-run options. The -v flag (for verbose) is also necessary to get the appropriate output:
rsync -anv dir1/ dir2


rsync -a ~/dir1 username@remote_host:destination_directory
rsync -a username@remote_host:/home/username/dir1 place_to_sync_on_local_machine

rsync -azvP username@remote_host:/home/username/dir1 place_to_sync_on_local_machine


rsync -azvP root@115.28.220.221:/data2/dmgr/target/* .

```

### df

```
linux中df命令的功能是用来检查linux服务器的文件系统的磁盘空间占用情况。可以利用该命令来获取硬盘被占用了多少空间，目前还剩下多少空间等信息。

1．命令格式：
df [选项] [文件]

2．命令功能：
显示指定磁盘文件的可用空间。如果没有文件名被指定，则所有当前被挂载的文件系统的可用空间将被显示。默认情况下，磁盘空间将以 1KB 为单位进行显示，除非环境变量 POSIXLY_CORRECT 被指定，那样将以512字节为单位进行显示

3．命令参数：
必要参数：
-a 全部文件系统列表
-h 方便阅读方式显示
-H 等于“-h”，但是计算式，1K=1000，而不是1K=1024
-i 显示inode信息
-k 区块为1024字节
-l 只显示本地文件系统
-m 区块为1048576字节
--no-sync 忽略 sync 命令
-P 输出格式为POSIX
--sync 在取得磁盘信息前，先执行sync命令
-T 文件系统类型
选择参数：
--block-size=<区块大小> 指定区块大小
-t<文件系统类型> 只显示选定文件系统的磁盘信息
-x<文件系统类型> 不显示选定文件系统的磁盘信息
--help 显示帮助信息
--version 显示版本信息
```

### du

```
Linux du命令也是查看使用空间的，但是与df命令不同的是Linux du命令是对文件和目录磁盘使用的空间的查看，还是和df命令有一些区别的.

1．命令格式：

du [选项][文件]

2．命令功能：

显示每个文件和目录的磁盘使用空间。

3．命令参数：

-a或-all  显示目录中个别文件的大小。   
-b或-bytes  显示目录或文件大小时，以byte为单位。   
-c或--total  除了显示个别目录或文件的大小外，同时也显示所有目录或文件的总和。 
-k或--kilobytes  以KB(1024bytes)为单位输出。
-m或--megabytes  以MB为单位输出。   
-s或--summarize  仅显示总计，只列出最后加总的值。
-h或--human-readable  以K，M，G为单位，提高信息的可读性。
-x或--one-file-xystem  以一开始处理时的文件系统为准，若遇上其它不同的文件系统目录则略过。 
-L<符号链接>或--dereference<符号链接> 显示选项中所指定符号链接的源文件大小。   
-S或--separate-dirs   显示个别目录的大小时，并不含其子目录的大小。 
-X<文件>或--exclude-from=<文件>  在<文件>指定目录或文件。   
--exclude=<目录或文件>         略过指定的目录或文件。    
-D或--dereference-args   显示指定符号链接的源文件大小。   
-H或--si  与-h参数相同，但是K，M，G是以1000为换算单位。   
-l或--count-links   重复计算硬件链接的文件。  


实例8：显示几个文件或目录各自占用磁盘空间的大小，还统计它们的总和
命令：
du -c log30.tar.gz log31.tar.gz

输出：
[root@localhost test]# du -c log30.tar.gz log31.tar.gz 
4       log30.tar.gz
4       log31.tar.gz
8       总计
[root@localhost test]#
说明：
加上-c选项后，du不仅显示两个目录各自占用磁盘空间的大小，还在最后一行统计它们的总和

实例9：按照空间大小排序
命令：

du|sort -nr|more

输出：
[root@localhost test]# du|sort -nr|more
1288    .
608     ./test6
308     ./test4
32      ./scf
16      ./scf/service
12      ./scf/service/deploy
8       ./test3
4       ./scf/service/deploy/product
4       ./scf/service/deploy/info
4       ./scf/lib
4       ./scf/doc
4       ./scf/bin
[root@localhost test]#
说明：
```

### split 分文件

```
用shell切分文件--split

有个文件要处理，因为很大，所以想把它切成若干份，每份N行，以便并行处理。怎么搞呢？查了下强大的shell，果然有现成的工具--split。
下面记录下基本用法：

[plain] view plain copy
split [-bl] file [prefix]  

参数说明：
-b, --bytes=SIZE：对file进行切分，每个小文件大小为SIZE。可以指定单位b,k,m。
-l, --lines=NUMBER：对file进行切分，每个文件有NUMBER行。
prefix：分割后产生的文件名前缀。

示例：

假设要切分的文件为test.2012-08-16_17，大小1.2M，12081行。

[plain] view plain copy
split -l 5000 test.2012-08-16_17  
生成xaa，xab，xac三个文件。
wc -l 看到三个文件行数如下：
5000 xaa
5000 xab
2081 xac
12081 总计
```

### whereis 

```
colorv@rd-qiniu-1:/data1/transform_data/multith/athread$ whereis upload-qn
upload-qn: /usr/local/bin/upload-qn
```

### nc

```
用nc命令操作memcached
1）存储数据：printf “set key 0 10 6rnresultrn” |nc 192.168.2.34 11211
2）获取数据：printf “get keyrn” |nc 192.168.2.34 11211
3）删除数据：printf “delete keyrn” |nc 192.168.2.34 11211
4）查看状态：printf “statsrn” |nc 192.168.2.34 11211
5）模拟top命令查看状态：watch “echo stats” |nc 192.168.2.34 11211
6）清空缓存：printf “flush_allrn” |nc 192.168.2.34 11211 (小心操作，清空了缓存就没了）
printf 可以改为echo

echo 'delete GroupMembers#3290514#10325' | nc 127.0.0.1 11213
```

### curl

```
1. curl命令来下载和保存文件
为了简单地下载使用以下语法Curl使用的文件。 -O用于在本地系统上保存文件，远程系统上相同的名称。
# curl -O http://example.com/download/myfile.zip


2.Curl下载并保存其他名称
如果你想保存在本地系统上不同的名字文件，请使用-o用新的文件名。
# curl -o localname.zip http://example.com/download/myfile.zip

3.Curl下载多个文件
Curl还提供了选项，同时下载多个文件。要下载多个文件使用以下语法。所有文件将与原文件名保存。
# curl -O http://example.com/myfile.zip -O http://example.com/myfile2.zip

4.传递登录凭证与Curl下载
如果文件被后面验证HTTP或FTP服务器。你可以通过登录使用像下面的例子-u命令行参数的凭据。
# curl -u user:password -O http://example.com/myfile.zip
# curl -u ftpuser:ftppassword -O ftp://ftp.example.com/myfile.zip

5.通过Curl代理服务器下载文件
如果服务器上的文件只能通过代理服务器，或者你要使用代理下载文件，使用-x其次是代理服务器地址和端口，通过代理服务器来下载文件。
$ curl -x my.proxy.com:3128 -O http://example.net/myfile.zip


curl -H "Content-Type:application/json" -d '{"video_id":"1", "video_digest_status":0,"video_activity_status":1,"user_real_play":1,"coins_so_far":1,"followers":20,"group_member_rank":10,"user_vip":1,"day_time_flag":1,"diamonds_so_far":2,"active_status":"ACTIVE","video_created_at":"2018-07-03 11:11:11"}' http://10.25.172.135:8005/calculate_rate/mock



假设目标url 为：127.0.0.1:8080/login

使用curl发送GET请求：curl protocol://address:port/url?args

curl http://127.0.0.1:8080/login?admin&passwd=12345678

使用curl发送POST请求：curl -d "args" protocol://address:port/url

curl -d "user=admin&passwd=12345678" http://127.0.0.1:8080/login

这种方法是参数直接在header里面的，如需将输出指定到文件可以通过重定向进行操作.
curl -H "Content-Type:application/json" -X POST -d 'json data' URL

curl -H "Content-Type:application/json" -X POST -d '{"user": "admin", "passwd":"12345678"}' http://127.0.0.1:8000/login

```

### cut

```
➜  webcv git:(master) echo $PATH
/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
➜  webcv git:(master) echo $PATH | cut -d ':' -f 1
/usr/local/bin
➜  webcv git:(master) echo $PATH | cut -d ':' -f 2
/usr/bin
➜  webcv git:(master) echo $PATH | cut -d ':' -f 3
/bin
➜  webcv git:(master) echo $PATH | cut -d ':' -f 4
/usr/sbin
➜  webcv git:(master) echo $PATH | cut -d ':' -f 5
/sbin


How can the delimiter be defined as a tab

Two ways:
Press Ctrl-v + Tab

cut -f2 -d'   ' infile
or write it like this:

cut -f2 -d$'\t' infile

```

### sed

```
➜  colorv-microservice git:(hot) ✗ gss | sed s/^//
M  colorv-services/src/main/java/com/colorv/hot/DigestLogController.java
?? colorv-bot/
➜  colorv-microservice git:(hot) ✗ gss | sed s/^..//
 colorv-services/src/main/java/com/colorv/hot/DigestLogController.java
 colorv-bot/
➜  colorv-microservice git:(hot) ✗ gss | sed s/^...//
colorv-services/src/main/java/com/colorv/hot/DigestLogController.java
colorv-bot/
```

### head

```
➜  colorv-microservice git:(hot) ✗ gss | sed s/^...//
colorv-services/src/main/java/com/colorv/hot/DigestLogController.java
colorv-bot/
➜  colorv-microservice git:(hot) ✗ gss | sed s/^...// | head -1
colorv-services/src/main/java/com/colorv/hot/DigestLogController.java
```

### 端口占用

```
netstat -tulpn | grep LISTEN


sudo lsof -i -P -n | grep LISTEN 
sudo netstat -tulpn | grep LISTEN
sudo nmap -sTU -O IP-address-Here
```

