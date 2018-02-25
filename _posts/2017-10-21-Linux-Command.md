---
title: Linux 命令
excerpt: |
  Linux 命令
category: Linux
feature_image: "https://picsum.photos/2560/600?image=872"
---
#### Linux返回上一次目录

有时候千辛万苦进入了一个很深层的目录，一不小心输入了cd并回车，有什么办法快速回到刚才所在的目录呢？对于bash来说，只需要很管理的一个命令：

cd -

该命令等同于`cd $OLDPWD`，关于这一点在bash的手册页(可使用命令man bash访问其手册页)中有介绍：

```
An argument of - is equivalent to $OLDPWD. 
```

并且它还会返回上一次目录的物理路径。
#### 拷贝输出的目录

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