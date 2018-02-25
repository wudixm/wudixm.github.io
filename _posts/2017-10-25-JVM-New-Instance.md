---
title: 反编译Java new对象过程后的dup 操作作用
excerpt: |
  反编译Java new对象过程后的dup 操作作用
category: Java 基础
feature_image: "https://picsum.photos/2560/600?image=872"
---
### 问题：

```java
public class Test6{
  public static void main(String[] args){
    Test6 t6 = new Test6();
  }
}
```

反编译后，得到如下：

```java
public class com.zte.practical1.Test6 extends java.lang.Object{ 
public com.zte.practical1.Test6(); 
  Code: 
   0:   aload_0 
   1:   invokespecial   #8; //Method java/lang/Object." <init>":()V 
   4:   return 

public static void main(java.lang.String[]); 
  Code: 
   0:   new     #1; //class com/zte/practical1/Test6 
   3:   dup 
   4:   invokespecial   #16; //Method " <init>":()V 
   7:   astore_1 
   8:   return 
} 
```

为什么要用dup指令？

```java

  0:  new    #1; //从内存中申请一块类 com.zte.practical1.Test6 的对象空间,将空间的地址放入栈顶
  3:  dup //将第0步得到的类Test6 的对象空间地址复制一份,放入栈顶.为什么要复制?
//这是因为:执行第4步(即下一步)的指令(即:调用Test6()构造器时,是需要对象的引用的,即:
//是用Test6()构造器对这个引用所代表的对象空间做初始化的.另外:解析#16时,也要用到它是属于哪个对象的引用.
  4:  invokespecial  #16; //Method " <init>":()V  //执行这一指令时,用到第3步中的引用.
//执行完成后,第3步的引用已经从栈中弹出了
  7:  astore_1  //将此时的栈顶值(正是第0步放入的对象的引用,该引用的对象空间已由3-4两步初始化过了)
//弹出,放入局部变量Test6 t6中.
  8:  	return 
```

**总结：是因为初始化方法需要用一个引用，执行完后会弹出一个栈顶元素。而一些其他方法（例如invokestatic）并不会弹出栈顶元素，就不需要先执行dup命令。**



| 指使码  | 助记符                 | 功能描述                            |                                          |
| ---- | ------------------- | ------------------------------- | ---------------------------------------- |
| 0xB6 | **invokevirtual**   | 调用实例方法（动态绑定）                    | 把objectref和args从stack中弹出，调用constant pool索引指向的实例方法 |
| 0xB7 | **invokespecial**   | 调用超类构造方法、实例初始化方法、私有方法（静态绑定，速度快） | 把objectref和args从stack中弹出，调用constant pool索引指向的实例方法 |
| 0xB8 | **invokestatic**    | 调用静态方法（静态绑定，速度快）                | 把args从stack中弹出，调用constant pool索引指向的实例方法  |
| 0xb9 | **invokeinterface** | 调用接口方法（动态绑定）                    | 把objectref和args从stack中弹出，调用constant pool索引指向的实例方法 |
|      | **invokedynamic**   | JDK 7引入的，主要是为了支持动态语言的方法调用。      |                                          |
