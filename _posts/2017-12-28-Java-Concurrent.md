---
title: Java ThreadLocal
excerpt: |
  Java ThreadLocal
category: Java 基础
feature_image: "https://picsum.photos/2560/600?image=872"
---
### ThreadLocal

#### ThreadLocal 的withInitial() 方法 

```java
/**
 * Creates a thread local variable. The initial value of the variable is
 * determined by invoking the {@code get} method on the {@code Supplier}.
 *
 * @param <S> the type of the thread local's value
 * @param supplier the supplier to be used to determine the initial value
 * @return a new thread local variable
 * @throws NullPointerException if the specified supplier is null
 * @since 1.8
 */
public static <S> ThreadLocal<S> withInitial(Supplier<? extends S> supplier) {
    return new ThreadLocal.SuppliedThreadLocal<>(supplier);
}


private static ThreadLocal<String> tl = new ThreadLocal<String>(){
    @Override
    protected String initialValue() {
        return "" + System.nanoTime();
    }
};

//利用Java 8 里ThreadLocal 新提供的方法withInitial 方法来创建初始化后的ThreadLocal，与上面这个创建的对象是相等的
private static ThreadLocal<String> tl2 = ThreadLocal.withInitial(() -> "" + System.nanoTime());
```

#### ThreadLocal 实现方式

在Thread 类里面，有如下的引用`ThreadLocal.ThreadLocalMap threadLocals = null`：

```java
public
class Thread implements Runnable {
	...
	
    /* ThreadLocal values pertaining to this thread. This map is maintained
     * by the ThreadLocal class. */
    ThreadLocal.ThreadLocalMap threadLocals = null;
	...
}
```

去ThreadLocal 类里面看到ThreadLocalMap 类里面保存的键是ThreadLocal 对象，值是Object 对象。
