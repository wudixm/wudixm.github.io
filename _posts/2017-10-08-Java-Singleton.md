---
title: 单例模式的静态内部类实现与枚举实现
excerpt: |
  单例模式的静态内部类实现与枚举实现
category: 设计模式
feature_image: "https://picsum.photos/2560/600?image=872"
---
### 单例有几个点需要注意

1. 线程安全
2. 调用效率要高一点
3. 懒加载

### 静态内部类实现单例

1. 当我们初始化类的时候，初始化外部类的时候，并不会初始化静态内部类，只有在调用getInstance()方法的时候，才会初始化内部类，实现了懒加载

   ```java
   class Singleton {
       private static class SingletonInnerClass {
           private static final Singleton instance = new Singleton();
       }

       private Singleton() {}

       public static Singleton getInstance() {
           return SingletonInnerClass.instance;
       }
   }
   ```

   ```java
   Compiled from "Singleton.java"
   class Singleton {
     public static Singleton getInstance();
       Code:
          0: invokestatic  #3                  // Method Singleton$SingletonInnerClass.access$100:()LSingleton;
          3: areturn

     Singleton(Singleton$1);
       Code:
          0: aload_0
          1: invokespecial #1                  // Method "<init>":()V
          4: return
   }
   ```

2. 又因为类加载的过程，是天然线程安全的

3. 在返回内部类对象的时候，返回的实例是一个final 对象，只能被赋值一次，也不存在同步的问题

4. 在调用的时候，直接返回，所以调用效率也不错

   ```java
   package DesignPattern.Singleton.innerclass;

   public class Singleton {
       private static class SingletonInnerClass {
           private static final Singleton instance = new Singleton();
       }

       private Singleton() {}

       public static Singleton getInstance() {
           return SingletonInnerClass.instance;
       }
   }
   ```

### 枚举方法实现单例

1. 枚举类天然就是单例的

2. 创建一个枚举后，反编译出来的是一个final 的类，所以线程安全

   ```java
   Compiled from "A.java"
   public final class A extends java.lang.Enum<A> {
     public static final A a;

     public static final A b;

     public static final A c;

     public static final A d;

     public static A[] values();
       Code:
          0: getstatic     #1                  // Field $VALUES:[LA;
          3: invokevirtual #2                  // Method "[LA;".clone:()Ljava/lang/Object;
          6: checkcast     #3                  // class "[LA;"
          9: areturn

     public static A valueOf(java.lang.String);
       Code:
       ...
   ```

- 优点：
  - 实现简单
  - 枚举本身就是单例模式。由JVM从根本上提供保障
  - 避免通过反射和反序列化的漏洞
- 缺点：
  - 无延迟加载

###  利用反射破解单例(不包含枚举实现)

在构造器里面加入抛出异常的代码

```java
private static Singleton singleton = null;

    private Singleton() {
        if (singleton != null) {
            throw new RuntimeException("fds");
        }
      // 如果不是第一次创建对象，则抛出异常
    }

    public synchronized static Singleton getInstance() {

        if (singleton == null) {
            singleton = new Singleton();
        }
        return singleton;
    }
```

### 利用反序列化破解单例(不包含枚举实现)

在单例里面添加readResolve()方法，方法里面返回instance。

在反序列化时，如果定义了readResolve方法，则直接返回方法指定的对象，而不需要单独创建新对象。

```java
public class Singleton implements Serializable{
    private static Singleton singleton = null;

    private Singleton() {
        if (singleton != null) {
            throw new RuntimeException("fds");
        }
    }

    public synchronized static Singleton getInstance() {

        if (singleton == null) {
            singleton = new Singleton();
        }
        return singleton;
    }

    private Object readResolve(){ //可能需要抛出异常，ObjectStreamException
        return singleton;
    }

}
```

