---
title: 测试foreach for 的循环性能
excerpt: |
  测试foreach for 的循环性能
category: Java 基础
feature_image: "https://picsum.photos/2560/600?image=872"
---
### 测试foreach for 的循环性能

在[这个](http://v.youku.com/v_show/id_XMTczMzUyNjQ0OA==.html)视频里，测试了for 和foreach 的性能，测试结果是for 遍历 arraylist性能更高，但是我自己测试发现是有一些问题的，原视频中的测试代码如下：

```java
List<String> stringList = new ArrayList<>();
for (int i = 0; i < 1000000; i++) {
    stringList.add(String.valueOf(i));
}


long beforeNormalFor = System.currentTimeMillis();
for (int i = 0; i < 1000; i++) {
    for (int j = 0; j < 1000000; j++) {
		 stringList.get(j);
    }
}
long beforeForEach = System.currentTimeMillis();
for (int i = 0; i < 1000; i++) {
    for (String s : stringList) {

    }
}
long afterForEach = System.currentTimeMillis();
System.out.println(beforeForEach - beforeNormalFor);   
System.out.println(afterForEach - beforeForEach);   

//运行环境信息：
//CPU：2.3 GHz Intel Core i5  内存：8 GB 2133 MHz LPDDR3 
//系统：macOS Sierra 10.12.6
//java版本：
//java version "1.8.0_144"
//Java(TM) SE Runtime Environment (build 1.8.0_144-b01)
//Java HotSpot(TM) 64-Bit Server VM (build 25.144-b01, mixed mode)

打印：
11
3543
```

从打印可以看出，利用for 遍历这个100000 字符串的Arraylist 要平均用时 9 - 15毫秒，而利用foreach 要用3500 - 4000 毫秒左右。**因为这个结果，视频中说的是多用for，因为性能高**。

但是我在自己测试的时候发现，**如果将for 循环的`stringList.get(j)` 改为`String s = stringList.get(j)`，**相当于多加了一个栈中的指针的赋值操作，改变后的for 循环的代码如下(**添加了一个引用**)：

```java
for (int i = 0; i < 1000; i++) {
    for (int j = 0; j < 1000000; j++) {
		 String s = stringList.get(j); // 添加的代码是 String s = 
    }
}

打印：
3260
3181
```

这回打印的结果for 和foreach 都是3000 - 4000，差别不大。foreach 每次运行都会稍微快一点点，2%左右。

结论是，**我们在利用遍历的时候，一般都需要用到遍历的对象，所以一般会用类似于`String s = stringList.get(j)` 这样的代码，并利用s 引用来完成所需的操作。如果是这个前提的话，for 循环与foreach 循环取Arraylist 中的值的时候，两种循环性能相差不大，foreach 性能快2% 左右(粗略估计，可以说相差不大)。**

将代码再改动一下，外层for 都改为10000 次：

```java
for (int i = 0; i < 10000; i++) {
	...
}
```

没有赋值给一个引用的for 与foreach 结果：

```java
第一次
8
33422
第二次
11
35143
```

赋值给一个String 引用的for 与foreach 结果：

```java
第一次
29752
33990
第二次
29947
33658
```

**结论：和前面差不多，不赋值给一个引用的for 循环性能上特别快，但是在赋值给一个引用的时候，for 与foreach 在性能上差不多，for 会快一些(与前面的foreach 速度快不同，在这种情况下for 快一些)**

可以想像到，**对于LinkList 这种不适合查询的数据结构，foreach 在性能上更有优势，for 在查询大的LinkedList 的时候都有可能卡死。**

总结，性能上foreach 也是不错的，再加上对不同数据结构的支持更好，而且Effective Java 书中也说**foreach 循环优先于传统的for 循环(第46条)(就是因为看视频的时候发现不相同，才进行了一次测试)**，所以推荐多用foreach。

