---
title: Java -verbose
excerpt: |
  Java -verbose
category: Java 基础
feature_image: "https://picsum.photos/2560/600?image=872"
---
Java -verbose:gc 中参数-verbose:gc 表示输出虚拟机中GC的详细情况

-verbose:gc 等同于-XX:+printGC，都是打印GC的简要信息。

在JVM的启动参数中加入-XX:+PrintGC -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+PrintGCApplicationStoppedTime，按照参数的顺序分别输出GC的简要信息，GC的详细信息、GC的时间信息及GC造成的应用暂停的时间。
