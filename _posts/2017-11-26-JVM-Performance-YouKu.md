---
title: Java 之性能优化
excerpt: |
  Java 之性能优化
category: Java 基础
feature_image: "https://picsum.photos/2560/600?image=872"
---
## Java 之性能优化

### java 的性能优化

1. 减少gc 的压力，优先级比较低的线程，它是一个守护线程，回收堆内存
2. 尽量避免new 操作

### String, StringBuffer, StringBuilder

1. String：是一个字符数组实现的，而且是final 的。

   ```java
   private final char value[];
   ```


2. 不要用下列方式创建String：

   ```java
   String a = "aaa";
   String d = new String("aaa");
   System.out.println(a == d); // 打印false，aaa 字符串在栈中，永远只有一份，但是如果调用了new String 这个构造器，那么会在堆中创建一个新的对象，每次调用都会创建一个新的String 在堆中的对象
   ```

3. String 的concat 方法每次返回一个新的String：

   ```java
   ublic String concat(String str) {
       int otherLen = str.length();
       if (otherLen == 0) {
           return this;
       }
       int len = value.length;
       char buf[] = Arrays.copyOf(value, len + otherLen);
       str.getChars(buf, len);
       return new String(buf, true); // 都会new 一个新的对象
   }
   ```


3. StringBuffer 的append 方法每次返回的都是自己

   ```java
   public synchronized StringBuffer append(String str) {
       toStringCache = null;
       super.append(str);
       return this; // 返回的都是自己
   }
   ```

4. StringBuffer 每个方法都是synchronized。

### 尽量用foreach 循环

### 尽量用基本类型

1. 尽量用基本类型，不要用包装类

   ```java
   Integer[] integers = {12, 13}; // 在堆内存中有3个对象，12， 13 还有数组对象
   int[] aa = {12, 13}; // 在堆中只有一个对象，12 和13 都在栈空间中
   ```

### 同步方法和同步代码块的选择

1. **能用同步代码块而不用同步方法就用同步代码块**
2. **同步代码块里面代码越少越好，粒度越小越好**

### transient 关键字

1. transient 关键字可以让虚拟机不序列化它所修饰的变量，也不需要被持久化：

   ```java
   transient String dn = "";
   ```

   好处是：**不需要消耗那么多的内存，不必要的时间开销，内存成本。针对临时变量，不是很关键的变量。**

   HashMap 中有很多：

   ```java
   transient HashMap.Node<K,V>[] table;

   transient Set<Map.Entry<K,V>> entrySet;

   transient int size;
   ```

   例如对于HashMap 中的size，size就是一个临时变量，在序列化的时候不需要这个，因为要序列化的时候Map 的增减的操作已经结束，这个时候size 只是一个临时变量了。

   尽量用final 修饰符

### 尽量用final 修饰符

加final 方法的访问速度比不加final 快

### instanceof 

**接口类型校验速度是最快的，比加具体的实现类快很多，如果我们校验类型时有接口类型，我们就校验它的接口类型就行了**

### 用三元表达式，不用if else

既美观，效率又高很多。

### ArrayList, Vector, LinkedList

ArrayList 需要排序的时候，需要排序取数据的时候，**线性表，对新增删除等操作的时间复杂度非常高，要整个表的动，如新增的时候会把新增后面的所有元素往后挪一位，底层是个数组**

LinkedList 需要删除，新增元素的时候，**链表，一旦插入和删除元素非常方便，利用指针完成新增删除操作，但是遍历非常慢**

Vector 有线程安全问题的时候，同StringBuffer 一样，加了同步
