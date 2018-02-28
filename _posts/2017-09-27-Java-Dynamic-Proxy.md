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

### Proxy.newInstance() 

```
public static Object newProxyInstance(ClassLoader loader,
                                      Class<?>[] interfaces,
                                      InvocationHandler h)
                               throws IllegalArgumentException
```

返回一个指定接口的代理类实例，该接口可以将方法调用指派到指定的调用处理程序。此方法相当于：     Proxy.getProxyClass(loader, interfaces).
​         getConstructor(new Class[] { InvocationHandler.class }).
​         newInstance(new Object[] { handler });
 Proxy.newProxyInstance 抛出 IllegalArgumentException，原因与 Proxy.getProxyClass 相同。

- 参数：
  - loader - 定义代理类的类加载器
  - interfaces - 代理类要实现的接口列表
  - h - 指派方法调用的调用处理程序
- 返回：
  - 一个带有代理类的指定调用处理程序的代理实例，它由指定的类加载器定义，并实现指定的接口
- 抛出：
  - IllegalArgumentException - 如果违反传递到 getProxyClass 的参数上的任何限制
  - NullPointerException - 如果 interfaces 数组参数或其任何元素为 null，或如果调用处理程序 h 为 null

### invoke

```
Object invoke(Object proxy,
              Method method,
              Object[] args)
              throws Throwable
```

- **参数：**

  `proxy` - 在其上调用方法的代理实例

  如果在InvocationHandler 中有如下代码：

  ```java
  // 无限循环，直到栈溢出
  @Override
  public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
      System.out.println("before");
      Object invoke = method.invoke(proxy, args); // 这一步出错，不能在proxy 对象上再调用method.invoke()方法，会再进到这个invoke 方法中，导致会一直打印before，直到栈溢出
      System.out.println("after"); // 这一打印执行不到
      return invoke;
  }
  ```

  `method` - 对应于在代理实例上调用的接口方法的 `Method` 实例。`Method` 对象的声明类将是在其中声明方法的接口，该接口可以是代理类赖以继承方法的代理接口的超接口。

  `args` - 包含传入代理实例上方法调用的参数值的对象数组，如果接口方法不使用参数，则为 `null`。基本类型的参数被包装在适当基本包装器类（如 `java.lang.Integer` 或 `java.lang.Boolean`）的实例中。

- **返回：**

  从代理实例的方法调用返回的值。如果接口方法的声明返回类型是基本类型，则此方法返回的值一定是相应基本包装对象类的实例；否则，它一定是可分配到声明返回类型的类型。如果此方法返回的值为 `null` 并且接口方法的返回类型是基本类型，则代理实例上的方法调用将抛出 `NullPointerException`。否则，如果此方法返回的值与上述接口方法的声明返回类型不兼容，则代理实例上的方法调用将抛出 `ClassCastException`。

- **抛出：**

  `Throwable` - 从代理实例上的方法调用抛出的异常。该异常的类型必须可以分配到在接口方法的 `throws` 子句中声明的任一异常类型或未经检查的异常类型 `java.lang.RuntimeException` 或 `java.lang.Error`。如果此方法抛出经过检查的异常，该异常不可分配到在接口方法的 `throws` 子句中声明的任一异常类型，代理实例的方法调用将抛出包含此方法曾抛出的异常的 [`UndeclaredThrowableException`](../../../java/lang/reflect/UndeclaredThrowableException.html)。

- **另请参见：**

  [`UndeclaredThrowableException`](../../../java/lang/reflect/UndeclaredThrowableException.html)

### 例子

```java
// AIntf.java
public interface AIntf {
    void soutA();
}

// BIntf.java
public interface BIntf {
    void soutB();
}

// Processor.java
public class Processor implements AIntf,BIntf{
    @Override
    public void soutA() {
        System.out.println("processor a ");
    }

    @Override
    public void soutB() {
        System.out.println("processor b ");
    }
}

// MyHandler.java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

public class MyHandler implements InvocationHandler {
    private Processor processor;

    public MyHandler(Processor processor) {
        this.processor = processor;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("before");
        Object invoke = method.invoke(proxy, args);
        System.out.println("after");
        return invoke;
    }
}

// Test.java
package DesignPattern.ProxyPattern.WorkProblem;

import java.lang.reflect.Proxy;

public class Test {
    public static void main(String[] args) {
        ClassLoader classLoader = Processor.class.getClassLoader();
        Class<?>[] interfaces = Processor.class.getInterfaces();

        Processor p = new Processor();
        BIntf a = (BIntf) Proxy.newProxyInstance(classLoader, interfaces, new MyHandler(p));
        a.soutB();
        // 正确
        
        // AIntf a = (BIntf) Proxy.newProxyInstance(classLoader, interfaces, new MyHandler(p));
        // a.soutA();
        // 也是正确的

    }
}

```

