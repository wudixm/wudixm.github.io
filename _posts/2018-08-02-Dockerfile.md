---
title: Dockerfile 用法
excerpt: |
  Dockerfile 用法
category: Docker
feature_image: "https://picsum.photos/2560/600?image=872"
---

[原文](https://www.jianshu.com/p/83a5d4800250)

## 目录

- 什么是Dockerfile
- Dockerfile的语法
- 生成镜像
- 总结

### 什么是dockerfile

docker 有个非常敏捷的地方就是，当你需要一个镜像的时候，去docker 仓库里面search并且pull一个你需要的镜像就行，比如我需要起一个nginx服务的容器，我就去pull一个nginx的镜像。那么当你需要自己的定制镜像时，该怎么办呢，一个方法就是自己下载一个基础的镜像，例如centos、ubuntu，然后在里面配置服务（建议一个容器一个服务）、环境变量等等，然后用commit指令生成一个新镜像。咦？等等。。。这样的话是不是可以做成一个自动化？是的，dockerfile就是干这个的。将所有命令写在一个文件里面，然后执行这个文件，一键生成新镜像，美滋滋。

### dockerfile的语法

dockerfile的语法对我这种勉强认出二十几个英文字母的人来说可以说是非常之友好了，下面结合一个非常简单的起java进程的dockerfile讲讲常用的一些指令
`vim Dockerfile`

```
###基础镜像名字
FROM centos

###作者  米兰的橘子皮
MAINTAINER OrangeLoveMilan

###设置系统语言
ENV LANG en_US.UTF-8

###执行指令，更换容器时间
RUN /bin/cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo 'Asia/Shanghai' >/etc/timezone

###容器内创建文件夹
RUN mkdir -p /app/example

###复制宿主机内文件到容器内
COPY ./example.jar /app/example

###把jdk添加到容器内目录
ADD ./jdk-8u131-linux-x64.tar.gz  /usr/local/

###配置jdk环境变量
ENV JAVA_HOME /usr/local/jdk1.8.0_131
ENV PATH /usr/local/jdk1.8.0_131/bin:/home/maven/apache-maven-3.3.9/bin:/usr/local/jdk1.8.0_131/bin:/usr/local/jdk1.8.0_131/bin:/sbin:/usr/sbin:/bin:/usr/bin
ENV PATH $JAVA_HOME/bin:$PATH
ENV CLASSPATH .:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

###工作目录
WORKDIR /app/example

###执行运行jar包命令
ENTRYPOINT ["java","-jar","example.jar"]

```

这样，一个定制的dockerfile就写完了，使用docker build就可以构建镜像了，每个从这个镜像上起的容器都是起着java应用的容器。可能有人会问，为什么不直接用有java的服务镜像作为基础镜像呢？？？？当时萌新的我也傻傻提出过，被老大一顿暴捶/(ㄒoㄒ)/~~。
既然是定制，尽量用官方（星星特别多）的基础镜像。
jdk必须用oracle_jdk,老大说他们以前用openjdk出过事故。。我自己搭的cdh集群也不支持openjdk。
有点扯远了。。。。。。。。。。。。。
下面搬运下docker官方的dockerfile指令讲解：

#### FROM：

```
FROM <image> [AS <name>]
FROM <image>[:<tag>] [AS <name>]
FROM <image>[@<digest>] [AS <name>]

```

很简单，只说三点：
1.ARG唯一在FROM之前的参数
2.同一个dockerfile里面可以出现多个from，后面的镜像会清除以前的状态
3.可选地，通过添加AS name到 FROM指令，可以将名称赋予新的构建阶段。名称可以在以后的使用FROM和 COPY --from=<name|index>指令是指建在这个阶段的图像
4.该tag或digest值是可选的。如果您忽略其中之一，则latest默认情况下，构建器会采用标记。如果找不到tag值，构建器将返回错误

#### RUN：

```
RUN <command>
RUN ["executable", "param1", "param2"]

```

执行run指令可能遇到的问题：
[https://github.com/moby/moby/issues/783](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2Fmoby%2Fmoby%2Fissues%2F783)
默认shell指令：/bin/sh -c
例子：
`RUN /bin/bash -c 'source $HOME/.bashrc; echo $HOME'`
或者
`RUN ["/bin/bash", "-c", "echo hello"]`
敲黑板！！！RUN的作用作用就是安装软件啊，写内容到文件里面啊这些
docker run 启动容器时使用了命令行参数，那么dockerfile 中的cmd 指令将无效，后面将会解释

#### CMD

```
CMD ["executable","param1","param2"]
CMD ["param1","param2"]
CMD command param1 param2

```

每个dockerfile只执行一个cmd（最后的那个）

#### ENTRYPOINT

```
ENTRYPOINT ["executable", "param1", "param2"]
ENTRYPOINT command param1 param2

```

run、cmd、entrypoint都是执行指令，感觉好像啊，他们有什么区别呢，我该怎么选择呢，上面说到run会覆盖cmd又是什么呢？欢迎走进十万个为什么。。呸，是走进这位老哥的博客，讲得是通俗易懂，看完就能恍然大明白
[http://blog.csdn.net/allocator/article/details/70490218](https://link.jianshu.com?t=http%3A%2F%2Fblog.csdn.net%2Fallocator%2Farticle%2Fdetails%2F70490218)

#### LABEL

`LABEL <key>=<value> <key>=<value> <key>=<value> ...`
一个标签就是一个键值对，可以有多个键值对。使用docker inpect指令查看镜像的标签

#### MAINTAINER

`MAINTAINER <name>`
维护者，请实名，出了事故好第一时间找到谁写的dockerfile（手动滑稽）

#### EXPOSE

`EXPOSE <port> [<port>/<protocol>...]`
暴露的端口
可以选择监听的形式TCP/UDP，默认是TCP

#### ENV

```
ENV <key> <value>
ENV <key>=<value> ...

```

设置环境变量。虽然说有两种方式，但还是推荐第一种写法，应为它提供了一个简单的缓存层，使用docker inspect查看，要是想手动改变容器的环境变量，在启动容器时使用--env <key>=<value>

#### COPY

`COPY <src>... <dest> COPY ["<src>",... "<dest>"]`
copy和add指令有相似之处，区别下面介绍add时将。copy 宿主机文件  镜像内文件，支持shell正则，*、？。
两个目录都推荐绝对路径。或者目标路径是workdir的相对路径也行。个人比较推荐都写绝对路径。COPY ../something /something这种写法不支持

#### ADD

```
ADD <src>... <dest>
ADD ["<src>",... "<dest>"]

```

ADD 也是copy文件到镜像里面，可以理解为是超级塞亚COPY，add比copy强在哪呢？

- ADD tar.gz格式的压缩包可以直接copy后被解压
- ADD 后可以直接加一个目标地址，举个例子：
  ADD [http://example.com/foobar](https://link.jianshu.com?t=http%3A%2F%2Fexample.com%2Ffoobar) / 相当于wget这个文件到/目录下，然后接解压文件到/foobar目录
- 如果你想就copy tar.gz格式的文件（不想解压）到镜像，那就用copy

#### VOLUME

`VOLUME ["/data"]`

通过volume指令生成容器内挂载的目录，一般用于挂载日志啊、数据啊这样。不过如果用dockerfile写的话，无法指定挂载容器内的目录到宿主机哪个目录内。使用docker inpect 查看挂载到宿主机内哪个目录下面。所以我一般不在dockerfile里面写volume，都是生成容器时使用--volume来指定挂载的容器的指定文件夹到宿主机内的指定文件夹

#### USER

`USER <user>[:<group>] or USER <UID>[:<GID>]`
默认使用root的组和用户

#### WORKDIR

`WORKDIR /path/to/workdir`
工作目录，就是RUN, CMD, ENTRYPOINT, COPY and ADD这些命令工作的地方，如果没有的话，workdir就会自动被创建，哪怕他没用
进入容器时，当前的目录就是workdir

## 生成镜像

常用的dockfile指令就介绍到这里了，记得指令一定要大写哦。下面就可以生成镜像了
`docker build -t <name:tag> -f .`
这是比较简单常用的构建镜像的指令-f后面是Dockerfile的位置
`docker image`
查看刚创建的镜像

## 总结

Dockerfile的入门就写到这里，熟练掌握常用的指令，可以很轻松的构建自己定制的镜像，再配合自动化脚本，就可以一键自动升级服务了。
