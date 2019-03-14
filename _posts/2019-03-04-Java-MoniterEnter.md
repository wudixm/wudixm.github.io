[原文](https://juejin.im/post/5abc9de851882555770c8c72)

## 同步方法

1. 同步方法会包含一个ACC_SYNCHCRONIZED标记符
2. 同步代码块会在代码中插入 monitorenter 和 monitorexist 指令

## 同步原理

### monitor监视器

- 每个对象都有一个监视器，在同步代码块中，JVM通过monitorenter和monitorexist指令实现同步锁的获取和释放功能
- 当一个线程获取同步锁时，即是通过获取monitor监视器进而等价为获取到锁
- monitor的实现类似于操作系统中的管程

###monitorenter指令

> Each object is associated with a monitor. A monitor is locked if and only if it has an owner. The thread that executes monitorenter attempts to gain ownership of the monitor associated with objectref, as follows: 
> • If the entry count of the monitor associated with objectref is zero, the thread enters the monitor and sets its entry count to one. The thread is then the owner of the monitor. 
> • If the thread already owns the monitor associated with objectref, it reenters the monitor, incrementing its entry count. 
> • If another thread already owns the monitor associated with objectref, the thread blocks until the monitor's entry count is zero, then tries again to gain ownership.

- 每个对象都有一个监视器。当该监视器被占用时即是锁定状态(或者说获取监视器即是获得同步锁)。线程执行monitorenter指令时会尝试获取监视器的所有权，过程如下：  
- 若该监视器的进入次数为0，则该线程进入监视器并将进入次数设置为1，此时该线程即为该监视器的所有者
- 若线程已经占有该监视器并重入，则进入次数+1
- 若其他线程已经占有该监视器，则线程会被阻塞直到监视器的进入次数为0，之后线程间会竞争获取该监视器的所有权
- 只有首先获得锁的线程才能允许继续获取多个锁

### monitorexit指令

> The thread that executes monitorexit must be the owner of the monitor associated with the instance referenced by objectref. 
> The thread decrements the entry count of the monitor associated with objectref. If as a result the value of the entry count is zero, the thread exits the monitor and is no longer its owner. Other threads that are blocking to enter the monitor are allowed to attempt to do so.

- 执行monitorexit指令将遵循以下步骤：  
- 执行monitorexit指令的线程必须是对象实例所对应的监视器的所有者
- 指令执行时，线程会先将进入次数-1，若-1之后进入次数变成0，则线程退出监视器(即释放锁)
- 其他阻塞在该监视器的线程可以重新竞争该监视器的所有权

### 实现原理

- 在同步代码块中，JVM通过monitorenter和monitorexist指令实现同步锁的获取和释放功能
- monitorenter指令是在编译后插入到同步代码块的开始位置
- monitorexit指令是插入到方法结束处和异常处
- JVM要保证每个monitorenter必须有对应的monitorexit与之配对
- 任何对象都有一个monitor与之关联，当且一个monitor被持有后，它将处于锁定状态
- 线程执行monitorenter指令时，将会尝试获取对象所对应的monitor的所有权，即尝试获得对象的锁
- 线程执行monitorexit指令时，将会将进入次数-1直到变成0时释放监视器
- 同一时刻只有一个线程能够成功，其它失败的线程会被阻塞，并放入到同步队列中，进入BLOCKED状态

## 6.锁优化

## 6.1 自旋锁

- **痛点：**由于线程的阻塞/唤醒需要CPU在用户态和内核态间切换，频繁的转换对CPU负担很重，进而对并发性能带来很大的影响
- **现象：**通过大量分析发现，对象锁的锁状态通常只会持续很短一段时间，没必要频繁地阻塞和唤醒线程
- **原理：**通过执行一段无意义的空循环让线程等待一段时间，不会被立即挂起，看持有锁的线程是否很快释放锁，如果锁很快被释放，那当前线程就有机会不用阻塞就能拿到锁了，从而减少切换，提高性能
- **隐患：**若锁能很快被释放，那么自旋效率就很好(真正执行的自旋次数越少效率越好，等待时间就少)；但若是锁被一直占用，那自旋其实没有做任何有意义的事但又白白占用和浪费了CPU资源，反而造成资源浪费
- **注意：**自旋次数必须有个限度(或者说自旋时间)，如果超过自旋次数(时间)还没获得锁，就要被阻塞挂起
- **使用：** JDK1.6以上默认开启-XX:+UseSpinning，自旋次数可通过-XX:PreBlockSpin调整，默认10次

## 6.2 自适应自旋锁

- **痛点：**由于自旋锁只能指定固定的自旋次数，但由于任务的差异，导致每次的最佳自旋次数有差异
- **原理：**通过引入"智能学习"的概念，由前一次在同一个锁上的自旋时间和锁的持有者的状态来决定自旋的次数，换句话说就是自旋的次数不是固定的，而是可以通过分析上次得出下次，更加智能
- **实现：**若当前线程针对某锁自旋成功，那下次自旋此时可能增加(因为JVM认为这次成功是下次成功的基础)，增加的话成功几率可能更大；反正，若自旋很少成功，那么自旋次数会减少(减少空转浪费)甚至直接省略自旋过程，直接阻塞(因为自旋完全没有意义，还不如直接阻塞)
- **补充：**有了自适应自旋锁，随着程序运行和性能监控信息的不断完善，JVM对锁的状况预测会越来越准确，JVM会变得越来越智能

## 6.3 阻塞锁

## 6.3.1 阻塞锁

- **加锁成功：**当出现锁竞争时，只有获得锁的线程能够继续执行
- **加锁失败：**竞争失败的线程会由running状态进入blocking状态，并被放置到与目标锁相关的一个等待队列中
- **解锁：**当持有锁的线程退出临界区，释放锁后，会将等待队列中的一个阻塞线程唤醒，令其重新参与到锁竞争中
- **补充：**本篇不会涉及到具体的JVM型号的分析，有兴趣的读者可以看看针对HotSopt JVM的分析 [深入JVM锁机制1-synchronized](https://link.juejin.im?target=https%3A%2F%2Flink.zhihu.com%2F%3Ftarget%3Dhttp%253A%2F%2Fblog.csdn.net%2Fchen77716%2Farticle%2Fdetails%2F6618779)

## 6.3.2 公平锁

公平锁就是获得锁的顺序按照先到先得的原则，从实现上说，要求当一个线程竞争某个对象锁时，只要这个锁的等待队列非空，就必须把这个线程阻塞并塞入队尾（插入队尾一般通过一个CAS操作保持插入过程中没有锁释放）

## 6.3.3 非公平锁

相对的，非公平锁场景下，每个线程都先要竞争锁，在竞争失败或当前已被加锁的前提下才会被塞入等待队列，在这种实现下，后到的线程有可能无需进入等待队列直接竞争到锁（随机性）

## 6.4 锁粗化

- **痛点：**多次连接在一起的加锁、解锁操作会造成
- **原理：**将多次连接在一起的加锁、解锁操作合并为一次，将多个连续的锁扩展成一个范围更大的锁
- **使用：**将多个彼此靠近的同步块合同在一个同步块 或 把多个同步方法合并为一个方法
- **补充：**在JDK内置的API中，例如StringBuffer、Vector、HashTable都会存在隐性加锁操作，可合并

```
/**
  * StringBuffer是线程安全的字符串处理类
  * 每次调用stringBuffer.append方法都需要加锁和解锁，如果虚拟机检测到有一系列连串的对同一个对象加锁和解锁操作，就会将其合并成一次范围更大的加锁和解锁操作，即在第一次append方法时进行加锁，最后一次append方法结束后进行解锁
  */
StringBuffer stringBuffer = new StringBuffer();
public void append(){
    stringBuffer.append("kira");
    stringBuffer.append("sally");
    stringBuffer.append("mengmeng");
}
复制代码
```

## 6.5 锁消除

- **痛点：**根据代码逃逸技术，如果判断到一段代码中，堆上的数据不会逃逸出当前线程，那么可以认为这段代码是线程安全的，不必要加锁
- **原理：** JVM在编译时通过对运行上下文的描述，去除不可能存在共享资源竞争的锁，通过这种方式消除无用锁，即删除不必要的加锁操作，从而节省开销
- **使用：** 逃逸分析和锁消除分别可以使用参数-XX:+DoEscapeAnalysis和-XX:+EliminateLocks(锁消除必须在-server模式下)开启
- **补充：**在JDK内置的API中，例如StringBuffer、Vector、HashTable都会存在隐性加锁操作，可消除

```
/**
  * 比如执行10000次字符串的拼接
  */
public static void main(String[] args) {
    SynchronizedDemo synchronizedDemo = new SynchronizedDemo();
    for (int i = 0 ; i < 10000 ; i++){
        synchronizedDemo.append("kira","sally");
    }
}
public void append(String str1,String str2){
    //由于StringBuffer对象被封装在方法内部，不可能存在共享资源竞争的情况
    //因此JVM会认为该加锁是无意义的，会在编译期就删除相关的加锁操作
    //还有一点特别要注明：明知道不会有线程安全问题，代码阶段就应该使用StringBuilder
    //否则在没有开启锁消除的情况下，StringBuffer不会被优化，性能可能只有StringBuilder的1/3
    StringBuffer stringBuffer = new StringBuffer();
    stringBuffer.append(str1).append(str2);
}/** 
复制代码
```

## 6.6 锁的升级

- 从JDK1.6开始，锁一共有四种状态：**无锁状态**、**偏向锁状态**、**轻量锁状态**、**重量锁状态**
- 锁的状态会随着竞争情况逐渐升级，**锁允许升级但不允许降级**
- **不允许降级的目的是提高获得锁和释放锁的效率**
- 后面笔者会通过倒序的方式，即重量级锁->轻量级锁->偏向锁进行讲解，因为通常是前者的优化

**锁的升级过程**

![img](https://user-gold-cdn.xitu.io/2018/3/29/16270c8c504450d0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

## 6.7 重量级锁

- 重量级锁通过对象内部的monitor实现(见上文的Monitor Object模式)
- monitor的本质是依赖于底层操作系统的MutexLock实现，操作系统实现线程间的切换是通过用户态与内核态的切换完成的，而切换成本很高
- MutexLock最核心的理念就是 尝试获取锁.若可得到就占有.若不能,就进入睡眠等待
- 有兴趣的读者可以阅读 [浅谈Mutex (Lock)](https://link.juejin.im?target=https%3A%2F%2Flink.zhihu.com%2F%3Ftarget%3Dhttp%253A%2F%2Fdreamrunner.org%2Fblog%2F2014%2F06%2F29%2Fqian-tan-mutex-lock%2F) ，该篇对Liunx的MutexLock做了很好的讲解

## 6.8 轻量级锁

## 6.8.1 轻量级锁综述

- **痛点：**由于线程的阻塞/唤醒需要CPU在用户态和内核态间切换，频繁的转换对CPU负担很重，进而对并发性能带来很大的影响
- **主要目的：** 在没有多线程竞争的前提下，减少传统的重量级锁使用操作系统互斥量产生的性能消耗
- **升级时机：** 当关闭偏向锁功能或多线程竞争偏向锁会导致偏向锁升级为轻量级锁
- **原理：** 在只有一个线程执行同步块时进一步提高性能
- **数据结构：** 包括**指向栈中锁记录的指针**、**锁标志位**
- **补充：**建议读者先阅读<<深入了解JVM虚拟机>>的第8章虚拟机字节码执行引擎的栈帧相关知识

![img](https://user-gold-cdn.xitu.io/2018/3/29/16270c8c27c4815e?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

## 6.8.2 轻量级锁流程图

**线程1和线程2同时争夺锁，并导致锁膨胀成重量级锁**

![img](https://user-gold-cdn.xitu.io/2018/4/8/162a318a71d936b1?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

## 6.8.3 轻量级锁加锁

- 1.线程在执行同步块之前，JVM会先在当前线程的栈帧中创建用于存储锁记录的空间，并将对象头中的Mark Word复制到锁记录中(Displaced Mark Word-即被取代的Mark Word)做一份拷贝
- 2.拷贝成功后，线程尝试使用CAS将对象头的Mark Word替换为指向锁记录的指针（将对象头的Mark Word更新为指向锁记录的指针，并将锁记录里的Owner指针指向Object Mark Word）  
- 如果更新成功，当前线程获得锁，继续执行同步方法
- 如果更新失败，表示其他线程竞争锁，当前线程便尝试使用自旋来获取锁，若自旋后没有获得锁，此时轻量级锁会升级为重量级锁，当前线程会被阻塞

## 6.8.4 轻量级锁解锁

- 解锁时会使用CAS操作将Displaced Mark Word替换回到对象头，  
- 如果解锁成功，则表示没有竞争发生
- 如果解锁失败，表示当前锁存在竞争，锁会膨胀成重量级锁，需要在释放锁的同时唤醒被阻塞的线程，之后线程间要根据重量级锁规则重新竞争重量级锁

## 6.8.5 轻量级锁注意事项

- **隐患：**对于轻量级锁有个使用前提是"没有多线程竞争环境"，一旦越过这个前提，除了互斥开销外，还会增加额外的CAS操作的开销，在多线程竞争环境下，轻量级锁甚至比重量级锁还要慢

## 6.9 偏向锁

## 6.9.1 偏向锁综述

- **痛点：** Hotspot作者发现在**大多数情况下不存在多线程竞争的情况**，而是**同一个线程多次获取到同一个锁**，为了让线程获得锁代价更低，因此设计了偏向锁 (这个跟业务使用有很大关系)
- **主要目的：** 为了在无多线程竞争的情况下尽量减少不必要的轻量级锁执行路径
- **原理：** 在只有一个线程执行同步块时通过增加标记检查而减少CAS操作进一步提高性能
- **数据结构：** 包括**占有锁的线程id**，**是否是偏向锁**，**epoch(偏向锁的时间戳)**，**对象分代年龄**、**锁标志位**

![img](https://user-gold-cdn.xitu.io/2018/3/29/16270c8c27c4815e?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

## 6.9.2 偏向锁流程图

**线程1演示了偏向锁的初始化过程，线程2演示了偏向锁的撤销锁过程**

![img](https://user-gold-cdn.xitu.io/2018/4/8/162a318a71d59542?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

## 6.9.3 偏向锁初始化

- 当一个线程访问同步块并获取到锁时，会**在对象头和栈帧中的锁记录里存储偏向锁的线程ID**，以后该线程在进入和退出同步块时**不需要花费CAS操作来加锁和解锁**，而是先简单**检查对象头的MarkWord中是否存储了线程：** 
- 如果已存储，说明线程已经获取到锁，继续执行任务即可
- 如果未存储，则需要再**判断当前锁否是偏向锁**(即对象头中偏向锁的标识是否设置为1，锁标识位为01)：  
- 如果**没有设置**，则**使用CAS竞争锁**（说明此时并不是偏向锁，一定是等级高于它的锁）
- 如果**设置**了，则**尝试使用CAS将对象头的偏向锁指向当前线程**，也就是结构中的线程ID

## 6.9.4 偏向锁撤销锁

- 偏向锁使用一种**等到竞争出现才释放锁**的机制，只有当其他线程竞争锁时，持有偏向锁的线程才会释放锁
- 偏向锁的**撤销需要等待全局安全点**(该时间点上没有字节码正在执行)
- 偏向锁的撤销需要遵循以下步骤：  

​               首先会**暂停拥有偏向锁的线程并检查该线程是否存活**：  

1. 如果线程**非活动状态**，则将**对象头设置为无锁状态**（其他线程会重新获取该偏向锁）
2. 如果线程是**活动状态**，拥有偏向锁的栈会被执行，遍历偏向对象的锁记录，并**将对栈中的锁记录和对象头的MarkWord进行重置**：  

- 要么**重新偏向于其他线程**(即将偏向锁交给其他线程，相当于当前线程"被"释放了锁)
- 要么**恢复到无锁**或者**标记锁对象不适合作为偏向锁**(此时锁会被升级为轻量级锁)

**最后唤醒暂停的线程，被阻塞在安全点的线程继续往下执行同步代码块**

## 6.9.5 偏向锁关闭锁

- 偏向锁在JDK1.6以上默认开启，开启后程序启动几秒后才会被激活
- 有必要可以使用JVM参数来关闭延迟 -XX：BiasedLockingStartupDelay = 0
- 如果确定锁通常处于竞争状态，则可通过JVM参数 -XX:-UseBiasedLocking=false 关闭偏向锁，那么默认会进入轻量级锁

## 6.9.6 偏向锁注意事项

- **优势：**偏向锁只需要在置换ThreadID的时候依赖一次CAS原子指令，其余时刻不需要CAS指令(相比其他锁)
- **隐患：**由于一旦出现多线程竞争的情况就必须撤销偏向锁，所以偏向锁的撤销操作的性能损耗必须小于节省下来的CAS原子指令的性能消耗（这个通常只能通过大量压测才可知）
- **对比：**轻量级锁是为了在线程交替执行同步块时提高性能，而偏向锁则是在只有一个线程执行同步块时进一步提高性能

## 6.10 偏向锁 vs 轻量级锁 vs 重量级锁

![img](https://user-gold-cdn.xitu.io/2018/4/8/162a318a71ce992d?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



## Volatile
对一个共享变量使用Volatile关键字保证了线程间对该数据的可见性，即不会读到脏数据。

注：1. 可见性：对一个volatile变量的读，总是能看到（任意线程）对这个volatile变量最后的写入

        2. 原子性：对任意单个volatile变量的读/写具有原子性(long,double这2个8字节的除外)，但类似于volatile++这种复合操作不具有原子性。
    
        3. volatile修饰的变量如果是对象或数组之类的，其含义是对象获数组的地址具有可见性，但是数组或对象内部的成员改变不具备可见性：

