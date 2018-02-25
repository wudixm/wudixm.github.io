---
title: Java RMI
excerpt: |
  Java RMI
category: Java 基础
feature_image: "https://picsum.photos/2560/600?image=872"
---
## Java 远程调用（RMI）

RMI可以让一个虚拟机上的应用程序请求调用位于网络上另一处虚拟机上的对象。习惯上称发出调用请求的虚拟机为（本地）客户机，称接受并执行请求的虚拟机为（远程）服务器。

1. 远程对象及其代理

   - 远程对象 — 编写一个**UnicastRemoteObject**的子类，该子类必须实现Remote
   - 代理与存根（Stub）— **rmic  RemoteConcreteSubject**  可以生成Stud

2. 启动注册：**rmiregistry**

3. 启动远程对象服务

   ```java
   RemoteConcreteSubject remoteObject = new RemoteConcreteSubject();
   Naming.rebind("rmi://127.0.0.1/rect",remoteObject);
   ```

4. 运行客户端程序

   ```
   Remote remoteObject = Naming.lookup("rmi://127.0.0.1/rect");
   RemoteSubject remoteSubject = (RemoteSubject)remoteObject;
   remoteSubject.setWidth();
   remoteSubject.setHeight();
   remoteSubject.getArea();
   ```

   ​
