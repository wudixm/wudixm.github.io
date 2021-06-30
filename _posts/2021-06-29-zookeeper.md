### 安装

brew 安装


## 视频教程

[原文](https://www.youtube.com/watch?v=BhosKsE8up8)

### 什么是一致性

#### CAP 理论

对于一个分布式系统，不能同时满足以下三点：

- 一致性(Consistency)
- 可用性(Availability)
- 分区容错性(Partition Tolerance)

#### 一致性模型

- 弱一致性
    - 最终一致性--DNS(Domain Name System)
- 强一致性
    - 同步
    - Paxos
    - Raft (multi-paxos)
    - ZAB (multi-paxos)

#### 明确问题

状态机复制的共识算法

### basic paxos

#### 角色介绍

Client: 系统外部角色，请求发起者
Propser：接受client请求，向集群提出提议(propose)。并在冲突发生时，起到冲突调节的作用。像议员、替民众提出议案
Accpetor(Voter): 提议投票和接收者，只有在形成法定人数(Quorum，一般即为majority多数派)时，提议才会最终被接受。像国会
Learner: 提议接受者，backup，备份，对集群一致性没什么影响。像记录员

#### 步骤、阶段(phase)

1. Phase 1a: Prepare---proposer提出一个提案，编号为N，此N大于这个proposer之前提出提案编号。请求accepter的quorum接受
2. Phase 1b: Promise---如果N大于此acceptor之前接受的任何提案编号则接受，否则拒绝
3. Phase 2a: Accept----如果达到了多数派，proposer会发出accept请求，此请求包含提案编号N，以及提案内容
4. Phase 2b: Accepted--如果此acceptor在此期间没有收到任何编号大于N的提案，则接受此提案内容，否则忽略

### raft


[raft协议](http://thesecretlivesofdata.com/raft/)

状态机复制的共识算法

### zab

基本与raft相同
有一些名词的叫法上有些区别：如ZAB将某个leader的周期称为epoch，而raft则称为term(任期)。
实现上也有些不同：如raft保证日志连续性，心跳方向为leader至follower，ZAB相反

Zookeeper 使用单一的主进程 Leader 来接收和处理客户端所有事务请求，并采用 ZAB 协议的原子广播协议，将事务请求以 Proposal 提议广播到所有 Follower 节点，当集群中有过半的Follower 服务器进行正确的 ACK 反馈，那么Leader就会再次向所有的 Follower 服务器发送commit 消息，将此次提案进行提交。这个过程可以简称为 2pc 事务提交

## 本地测试命令


### ls

```sh
[zk: localhost:2181(CONNECTED) 11] ls /
[tttt0000000001, tttt0000000002, zookeeper]

[zk: localhost:2181(CONNECTED) 10] ls -s /
[tttt0000000001, tttt0000000002, zookeeper]
cZxid = 0x0
ctime = Thu Jan 01 08:00:00 CST 1970
mZxid = 0x0
mtime = Thu Jan 01 08:00:00 CST 1970
pZxid = 0x1b
cversion = 3
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 0
numChildren = 3

[zk: localhost:2181(CONNECTED) 12] ls -R /
/
/tttt0000000001
/tttt0000000002
/zookeeper
/zookeeper/config
/zookeeper/quota
```

### get

要访问顺序节点，必须输入znode的完整路径。

```sh
[zk: localhost:2181(CONNECTED) 11] get /test
1
[zk: localhost:2181(CONNECTED) 12] get -s /test
1
cZxid = 0x3
ctime = Tue Jun 29 15:21:26 CST 2021
mZxid = 0x3
mtime = Tue Jun 29 15:21:26 CST 2021
pZxid = 0x3
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 1
numChildren = 0
```

#### watch

```sh
[zk: localhost:2181(CONNECTED) 7] get -w /test
2
[zk: localhost:2181(CONNECTED) 8]    # 在另一个cli里，set /test 4，修改了watch 的数据的值时，会收到通知如下
WATCHER::

WatchedEvent state:SyncConnected type:NodeDataChanged path:/test

```

#### getAllChildrenNumber

```sh
[zk: localhost:2181(CONNECTED) 13] ls /test
[test10000000001, test10000000002, test10000000004]
[zk: localhost:2181(CONNECTED) 10] getAllChildrenNumber /test
3
```

#### getEphemerals

getEphemerals命令查询临时节点
```sh
[zk: localhost:2181(CONNECTED) 15] getEphemerals /test
[/test/test10000000004]
```

### set
set 命令用于修改节点存储的数据。

格式：

set path data [version]
path：节点路径。
data：需要存储的数据。
[version]：可选项，版本号(可用作乐观锁)。

只有正确的版本号才能设置成功

```sh
[zk: localhost:2181(CONNECTED) 26] set -s -v 5 /test 6
cZxid = 0x1c
ctime = Tue Jun 29 15:54:53 CST 2021
mZxid = 0x24
mtime = Tue Jun 29 15:56:22 CST 2021
pZxid = 0x1c
cversion = 0
dataVersion = 6
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 1
numChildren = 0
[zk: localhost:2181(CONNECTED) 28] set -s -v 7 /test 7
version No is not valid : /test
[zk: localhost:2181(CONNECTED) 29] set -s -v 6 /test 7
cZxid = 0x1c
ctime = Tue Jun 29 15:54:53 CST 2021
mZxid = 0x27
mtime = Tue Jun 29 15:56:34 CST 2021
pZxid = 0x1c
cversion = 0
dataVersion = 7
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 1
numChildren = 0
```

### stat

检查状态
状态描述指定的znode的元数据。它包含时间戳，版本号，ACL，数据长度和子znode等细项
```sh
[zk: localhost:2181(CONNECTED) 14] stat /test
cZxid = 0x3
ctime = Tue Jun 29 15:21:26 CST 2021
mZxid = 0x3
mtime = Tue Jun 29 15:21:26 CST 2021
pZxid = 0x3
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 1
numChildren = 0
```

### create

create 命令用于创建节点并赋值。

[-s] [-e]：-s 和 -e 都是可选的，-s 代表顺序节点， -e 代表临时节点，注意其中 -s 和 -e 可以同时使用的，并且临时节点不能再创建子节点。
path：指定要创建节点的路径，比如 /runoob。
data：要在此节点存储的数据。
acl：访问权限相关，默认是 world，相当于全世界都能访问。

用给定的路径创建一个znode。flag参数指定创建的znode是临时的，持久的还是顺序的。默认情况下，所有znode都是持久的。
当会话过期或客户端断开连接时，临时节点（flag：-e）将被自动删除。
顺序节点保证znode路径将是唯一的。

要创建顺序节点，请添加flag：-s
要创建临时节点，请添加flag：-e 



```sh
[zk: localhost:2181(CONNECTED) 9] create /test 1
Created /test

[zk: localhost:2181(CONNECTED) 21] create -s /tttt
Created /tttt0000000001
```

### delete

```sh
[zk: localhost:2181(CONNECTED) 0] ls /
[test, tttt0000000001, tttt0000000002, zookeeper]
[zk: localhost:2181(CONNECTED) 1] ls /test
[test1, test2, test3, test4, test5]
[zk: localhost:2181(CONNECTED) 2] delete /test/test2
[zk: localhost:2181(CONNECTED) 3] ls /test
[test1, test3, test4, test5]
```

### deleteall

```sh
[zk: localhost:2181(CONNECTED) 5] deleteall /test
[zk: localhost:2181(CONNECTED) 6] ls /
[tttt0000000001, tttt0000000002, zookeeper]
```

### 四字命令
[原文](https://stackoverflow.com/questions/62667788/how-do-i-initialize-the-whitelist-for-apache-zookeeper)
要添加到whitelist中，否则查看不了

方法1
```sh
KAFKA_OPTS="-Dzookeeper.4lw.commands.whitelist=*" bin/zookeeper-server-start.sh config/zookeeper.properties
```

方法2
For mac, I added

4lw.commands.whitelist=stat, ruok, conf, isro

in /usr/local/etc/zookeeper/zoo.cfg

## w3school 教程

[原文](https://www.runoob.com/w3cnote/zookeeper-znode-data-model.html)

### 5.0 ZooKeeper 数据模型 znode 结构详解

#### 数据模型

在 zookeeper 中，可以说 zookeeper 中的所有存储的数据是由 znode 组成的，节点也称为 znode，并以 key/value 形式存储数据。
整体结构类似于 linux 文件系统的模式以树形结构存储。其中根路径以 / 开头。


### 9.0 Zookeeper 节点特性
1、同一级节点 key 名称是唯一的
2、创建节点时，必须要带上全路径
3、session 关闭，临时节点清除
4、自动创建顺序节点
5、watch 机制，监听节点变化
- 监听事件被单次触发后，事件就失效了
6、delete 命令只能一层一层删除

有了上述众多节点特性，使得 zookeeper 能开发不出不同的经典应用场景，比如：

1. 数据发布/订阅
2. 负载均衡
3. 分布式协调/通知
4. 集群管理
5. 集群管理
6. master 管理
7. 分布式锁
8. 分布式队列

### 10.0 Zookeeper 权限控制 ACL
zookeeper 的 ACL（Access Control List，访问控制表）权限在生产环境是特别重要的，所以本章节特别介绍一下。

ACL 权限可以针对节点设置相关读写等权限，保障数据安全性。

permissions 可以指定不同的权限范围及角色。

ACL 命令行
getAcl 命令：获取某个节点的 acl 权限信息。
setAcl 命令：设置某个节点的 acl 权限信息。
addauth 命令：输入认证授权信息，注册时输入明文密码，加密形式保存。
ACL 构成
zookeeper 的 acl 通过 [scheme:id:permissions] 来构成权限列表。

1、scheme：代表采用的某种权限机制，包括 world、auth、digest、ip、super 几种。
2、id：代表允许访问的用户。
3、permissions：权限组合字符串，由 cdrwa 组成，其中每个字母代表支持不同权限， 创建权限 create(c)、删除权限 delete(d)、读权限 read(r)、写权限 write(w)、管理权限admin(a)。

### 14.0 Zookeeper 分布式锁实现原理

#### 排他锁

排他锁（Exclusive Locks），又被称为写锁或独占锁，如果事务T1对数据对象O1加上排他锁，那么整个加锁期间，只允许事务T1对O1进行读取和更新操作，其他任何事务都不能进行读或写。

定义锁：

/exclusive_lock/lock
实现方式：

利用 zookeeper 的同级节点的唯一性特性，在需要获取排他锁时，所有的客户端试图通过调用 create() 接口，在 /exclusive_lock 节点下创建临时子节点 /exclusive_lock/lock，最终只有一个客户端能创建成功，那么此客户端就获得了分布式锁。同时，所有没有获取到锁的客户端可以在 /exclusive_lock 节点上注册一个子节点变更的 watcher 监听事件，以便重新争取获得锁。


#### 分布式锁
客户端A要获取分布式锁的时候首先到locker下创建一个临时顺序节点（node_n），然后立即获取locker下的所有（一级）子节点。

此时因为会有多个客户端同一时间争取锁，因此locker下的子节点数量就会大于1。对于顺序节点，特点是节点名称后面自动有一个数字编号，

先创建的节点数字编号小于后创建的，因此可以将子节点按照节点名称后缀的数字顺序从小到大排序，这样排在第一位的就是最先创建的顺序节点，

此时它就代表了最先争取到锁的客户端！此时判断最小的这个节点是否为客户端A之前创建出来的node_n，如果是则表示客户端A获取到了锁，

如果不是则表示锁已经被其它客户端获取，因此客户端A要等待它释放锁，也就是等待获取到锁的那个客户端B把自己创建的那个节点删除。

此时就通过监听比node_n次小的那个顺序节点的删除事件来知道客户端B是否已经释放了锁，如果是，此时客户端A再次获取locker下的所有子节点，

再次与自己创建的node_n节点对比，直到自己创建的node_n是locker的所有子节点中顺序号最小的，此时表示客户端A获取到了锁！

#### 羊群效应

[原文](https://blog.csdn.net/qiangcuo6087/article/details/79067136)

最后，对于这个算法有个极大的优化点：假如当前有1000个节点在等待锁，如果获得锁的客户端释放锁时，这1000个客户端都会被唤醒，这种情况称为“羊群效应”；在这种羊群效应中，zookeeper需要通知1000个客户端，这会阻塞其他的操作，最好的情况应该只唤醒新的最小节点对应的客户端。应该怎么做呢？在设置事件监听时，每个客户端应该对刚好在它之前的子节点设置事件监听，例如子节点列表为/lock/lock-0000000000、/lock/lock-0000000001、/lock/lock-0000000002，序号为1的客户端监听序号为0的子节点删除消息，序号为2的监听序号为1的子节点删除消息。

#### 改进后逻辑

所以调整后的分布式锁算法流程如下：

- 客户端连接zookeeper，并在/lock下创建临时的且有序的子节点，第一个客户端对应的子节点为/lock/lock-0000000000，第二个为/lock/lock-0000000001，以此类推；
- 客户端获取/lock下的子节点列表，判断自己创建的子节点是否为当前子节点列表中序号最小的子节点，如果是则认为获得锁，否则监听刚好在自己之前一位的子节点删除消息，获得子节点变更通知后重复此步骤直至获得锁；
- 执行业务代码；
- 完成业务流程后，删除对应的子节点释放锁。


#### curator
其实对于Zookeeper的一些常用功能是有一些成熟的包实现的，像Curator。Curator的确是足够牛逼，不仅封装了Zookeeper的常用API，也包装了很多常用Case的实现。但是它的编程风格其实还是吧比较难以接受的。
可以用Curator轻易的实现一个分布式锁：
```java
InterProcessMutex lock = new InterProcessMutex(client, lockPath);
if ( lock.acquire(maxWait, waitUnit) ) 
{
    try 
    {
        // do some work inside of the critical section here
    }
    finally
    {
        lock.release();
    }
}
```
是的就这么简单，一个直接拿过来可用的轮子。


### 分布式锁

常见的有使用zk的最小版本，redis的set函数，数据库锁来实现，本节我们谈谈使用zookeeper的序列节点机制来实现一个分布式锁。

## zk应用

### 配置管理

如果集群中的机器的程序配置都是一样的，而且需要动态修改，我们可以使用发布和订阅模式，把配置做统一的管理。
故名思议就是一方把数据发布出来，另一方通过某种手段可以得到这些数据。通常数据订阅有两种方式：推模式和拉模式。
推模式一般是服务器n主动向客户端推送信息，拉模式是客户端主动去服务器获取数据（通常是采用定时轮询的方式）。
zk采用两种方式相结合，发布者将数据发布到zk集群节点上，订阅者通过一定的方法告诉服务器，我对哪个节点的数据感兴趣，那服务器在这些节点的数据发生变化时，就通知客户端，客户端得到通知后可以去服务器获取信息。

### 集群管理（Group Membership）/服务发现

集群管理，在分布式系统中，我们常常需要知道某个机器是否可用，传统开发中，可以通过ping某个机器来实现，ping得通说明对方是可用的，相反是不可用的。zk中我们所有的机器都注册一个临时节点，我们判断一个机器是否可用只需要判断这个节点在zk中是否存在就可以了，不需要直接去连接需要检查的机器，降低系统的复杂度。
         服务发现，对集群中的服务上下线做统一的管理，每个工作服务器都可以作为数据的发布方，向集群注册自己的基本信息。而让某些监控服务器作为订阅方，订阅工作服务器的基本信息，当工作服务器的信息发生改变时，如服务上下线、服务器的角色等发生变更时，监控服务器可以得到通知，并响应这些变化。


### 用作注册中心
zookeeper就是个分布式文件系统，每当一个服务提供者部署后都要将自己的服务注册到zookeeper的某一路径上: /{service}/{version}/{ip:port}, 比如我们的HelloWorldService部署到两台机器，那么zookeeper上就会创建两条目录：分别为/HelloWorldService/1.0.0/100.19.20.01:16888 /HelloWorldService/1.0.0/100.19.20.02:16888。

感知服务的下线&上线
zookeeper提供了“心跳检测”功能，它会定时向各个服务提供者发送一个请求（实际上建立的是一个 socket 长连接），如果长期没有响应，服务中心就认为该服务提供者已经“挂了”，并将其剔除，比如100.19.20.02这台机器如果宕机了，那么zookeeper上的路径就会只剩/HelloWorldService/1.0.0/100.19.20.01:16888。

服务消费者会去监听相应路径（/HelloWorldService/1.0.0），一旦路径上的数据有任务变化（增加或减少），zookeeper都会通知服务消费方服务提供者地址列表已经发生改变，从而进行更新。

更为重要的是zookeeper 与生俱来的容错容灾能力（比如leader选举），可以确保服务注册表的高可用性。

使用 zookeeper 作为注册中心时，客户端订阅服务时会向 zookeeper 注册自身；主要是方便对调用方进行统计、管理。但订阅时是否注册 client 不是必要行为，和不同的注册中心实现有关，例如使用 consul 时便没有注册。

### 用作负载均衡

[原文](https://blog.csdn.net/dshf_1/article/details/81087447)

zookeeper实现负载均衡其实原理很简单，zookeeper 的数据存储类似于liunx的目录结构。首先建立servers节点，并建立监听器监视servers子节点的状态（用于在服务器增添时及时同步当前集群中服务器列表）。在每个服务器启动时，在servers节点下建立子节点worker server（可以用服务器地址命名）,并在对应的字节点下存入服务器的相关信息。这样，我们在zookeeper服务器上可以获取当前集群中的服务器列表及相关信息，可以自定义一个负载均衡算法，在每个请求过来时从zookeeper服务器中获取当前集群服务器列表，根据算法选出其中一个服务器来处理请求。

### 用作分布式队列

[原文](https://ifeve.com/zookeeper%EF%BC%8Dcurator/)
 根据Netflix的Curator作者所说， ZooKeeper真心不适合做Queue，或者说ZK没有实现一个好的Queue，详细内容可以看 Tech Note 4， 原因有五：

ZK有1MB 的传输限制。 实践中ZNode必须相对较小，而队列包含成千上万的消息，非常的大。
如果有很多节点，ZK启动时相当的慢。 而使用queue会导致好多ZNode. 你需要显著增大 initLimit 和 syncLimit.
ZNode很大的时候很难清理。Netflix不得不创建了一个专门的程序做这事。
当很大量的包含成千上万的子节点的ZNode时， ZK的性能变得不好
ZK的数据库完全放在内存中。 大量的Queue意味着会占用很多的内存空间。
尽管如此， Curator还是创建了各种Queue的实现。 如果Queue的数据量不太多，数据量不太大的情况下，酌情考虑，还是可以使用的。

1. 对于生产者进程，可以使用"create()"创建一个新的有序持久型结点，数据为指定的整型，并将元素增加到队列。创建方法如下
2. 为了消费一个元素，一个消费者进程获取root结点的孩子结点，找到顺序最小的结点，并返回这个元素。可以调用getChildren获取root结点的孩子列表；互相比较，找出顺序最小的孩子结点；获取该节点的数据，并删除该节点；最后返回结点的值即可。当消费者进程消费完成之后，队列中结点也将清空。你可以使用ls /app1命令查看队列中元素信息，如下图

### 队列2

[原文](https://www.jb51.net/article/123957.htm)

基于ZooKeeper实现队列源码
实现原理
先进先出队列是最常用的队列，使用Zookeeper实现先进先出队列就是在特定的目录下创建PERSISTENT_EQUENTIAL节点，创建成功时Watcher通知等待的队列，队列删除序列号最小的节点用以消费。此场景下Zookeeper的znode用于消息存储，znode存储的数据就是消息队列中的消息内容，SEQUENTIAL序列号就是消息的编号，按序取出即可。由于创建的节点是持久化的，所以不必担心队列消息的丢失问题。

应用场景
Zookeeper队列不太适合要求高性能的场合，但可以在数据量不大的情况下考虑使用。比如已在项目中使用Zookeeper又需要小规模的队列应用，这时可以使用Zookeeper实现的队列;毕竟引进一个消息中间件会增加系统的复杂性和运维的压力。
