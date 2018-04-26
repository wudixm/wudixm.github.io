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
➜  ~ ssh-add .ssh/id_rsa
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

