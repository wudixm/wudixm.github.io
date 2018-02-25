---
title: Java 动态代理
excerpt: |
  Java 动态代理
category: 设计模式
feature_image: "https://picsum.photos/2560/600?image=872"
---
视频地址：http://www.iqiyi.com/w_19rrxrm1s1.html

## 本节内容

- 理解动态代理设计模式
- Cglib动态代理

### 动态代理

- **代理模式**是常用的java设计模式，它的特征是代理类与委托类有同样的接口，代理类主要负责为委托类预处理消息、过滤消息、把消息转发给委托类，以及事后处理消息等。代理类与委托类之间通常会存在关联关系，一个代理类的对象与一个委托类的对象关联，**代理类的对象本身并不真正实现服务，而是通过调用委托类的对象的相关方法，来提供特定的服务。**

- **动态代理类的字节码在程序运行时由Java反射机制动态生成**，无需程序员手工编写它的源代码。动态代理类不仅简化了编程工作，而且提高了软件系统的可扩展性，因为Java反射机制可以生成任意类型的动态代理类。**java.lang.reflect 包中的Proxy类和InvocationHandler 接口提供了生成动态代理类的能力**。

- JDK动态代理中包含**一个类和一个接口**：

  - InvocationHandler接口：

    ```java
    public interface InvocationHandler {
    	public Object invoke(Object proxy, Method method, Object[] 			args) throws Throwable;
    }
    ```

    **参数说明：**

    Object proxy：指被代理的对象

    Method method：要调用的方法

    Object[] args：方法调用时所需要的参数

    **invoke方法是在调用委托类对象方法的时候，会自动执行这个invoke方法**

  - Proxy类：

    Proxy类是专门完成代理的操作类，可以通过此类为一个或多个接口动态地生成实现类，此类提供了如下的操作方法：

    ```java
    public static Object newProxyInstance(ClassLoader loader,              		Class<?>[] interfaces, InvocationHandler h)  
    	 throws IllegalArgumentException
    ```

    **参数说明：**

    ClassLoader loader：类加载器

    Class<?>[] interfaces：得到全部的接口

    InvocationHandler h：得到InvocationHandler 接口的子类实例（一般这个参数传入this这个指针）

- 类加载器

  在Java中主要有以下三种类加载器：

  - **Bootstrap ClassLoader：**此加载器采用C++编写，一般开发中是看不到的
  - **Extension ClassLoader：**用来进行扩展类的加载，一般对应的是jre\lib\ext目录中的类
  - **App ClassLoader**：（默认）加载classpath指定的类，是最常使用的一种加载器
