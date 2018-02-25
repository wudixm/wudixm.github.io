---
title: Java 泛型的实现原理
excerpt: |
  Java 泛型的实现原理
category: Java 基础
feature_image: "https://picsum.photos/2560/600?image=872"
---
## Java 泛型的实现原理

### 类型擦除

Java中的泛型基本上都是在编译器这个层次来实现的。**在生成的Java字节码中是不包含泛型中的类型信息的。使用泛型的时候加上的类型参数，会在编译器在编译的时候去掉。这个过程就称为类型擦除。**

**测试代码一**

编译过的文件中 `13： ` 和 ` 38： ` 行，在add方法调用的时候都是调用的add(Object) 方法，Object 是原始类型：

```Java
public class Test {
    public static void main(String[] args) {
        ArrayList al = new ArrayList();

        String s1 = "abcd";
        al.add(s1);
        String s = (String) al.get(0);

        ArrayList<String> alStr = new ArrayList<String>();
        alStr.add(s1);
        String s2 = alStr.get(0);
    }
}
```

Javap 之后：

```java
Compiled from "Test.java"
public class JavaGeneric.BaiduExample.Test {
  public JavaGeneric.BaiduExample.Test();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: new           #2                  // class java/util/ArrayList
       3: dup
       4: invokespecial #3                  // Method java/util/ArrayList."<init>":()V
       7: astore_1
       8: ldc           #4                  // String abcd
      10: astore_2
      11: aload_1
      12: aload_2
      13: invokevirtual #5                  // Method java/util/ArrayList.add:(Ljava/lang/Object;)Z
      16: pop
      17: aload_1
      18: iconst_0
      19: invokevirtual #6                  // Method java/util/ArrayList.get:(I)Ljava/lang/Object;
      22: checkcast     #7                  // class java/lang/String  在代码中手动转换类型
      25: astore_3
      26: new           #2                  // class java/util/ArrayList
      29: dup
      30: invokespecial #3                  // Method java/util/ArrayList."<init>":()V
      33: astore        4
      35: aload         4
      37: aload_2
      38: invokevirtual #5                  // Method java/util/ArrayList.add:(Ljava/lang/Object;)Z
      41: pop
      42: aload         4
      44: iconst_0
      45: invokevirtual #6                  // Method java/util/ArrayList.get:(I)Ljava/lang/Object;
      48: checkcast     #7                  // class java/lang/String 自动转换了类型
      51: astore        5
      53: return
}
```

**测试代码二：**

利用反射将String 插入到Integer 泛型的ArrayList 中，说明了Integer泛型实例在编译之后被擦除了，只保留了

原始类型。

```java
public static void main(String[] args) throws NoSuchMethodException... {
        ArrayList<Integer> arrayList3 = new ArrayList<Integer>();
        arrayList3.add(1);
        arrayList3.getClass().getMethod("add", Object.class).invoke(arrayList3, "abc");
        for (int i = 0; i < arrayList3.size(); i++) {
            System.out.println(arrayList3.get(i));
        }
        //到这里会打印 1 和 abc 

        ArrayList<String> arrayList4 = new ArrayList<String>();
        arrayList4.add("abc");
        arrayList4.getClass().getMethod("add", Object.class).invoke(arrayList4, 1);
        System.out.println(arrayList4.get(1));
        //在这一行抛出异常
        //Exception in thread "main" java.lang.ClassCastException: java.lang.Integer cannot be cast to java.lang.String
        //遗留问题-- 为什么可以将String 插入到Integer 的ArrayList中，而不能将Integer 插入到String 的list 中？
        for (int i = 0; i < arrayList4.size(); i++) {
            System.out.println(arrayList4.get(i));
        }
    }
```

### 类型擦除之后，怎么保证类型正确性？

**java编译器是通过先检查代码中泛型的类型，然后再进行类型擦除，在进行编译的。**

```java
ArrayList<String> arrayList=new ArrayList<String>();  
arrayList.add("123");  
arrayList.add(123);//编译错误
```

### 泛型信息写到前面与后面的区别

```java
ArrayList<String> as = new ArrayList<>(); //第一种情况
as.add("ab");
as.add(1); //编译错误

ArrayList as2 = new ArrayList<String>(); //第二种情况
as2.add("ab");
as2.add(1); //可以编译成功
```

第一种情况，可以实现与 完全使用泛型参数一样的效果，第二种则完全没效果。

因为，本来类型检查就是编译时完成的。**new ArrayList()只是在内存中开辟一个存储空间，可以存储任何的类型对象。而真正涉及类型检查的是它的引用**，因为我们是使用它引用arrayList1 来调用它的方法，比如说调用add()方法。所以**arrayList1引用能完成泛型类型的检查**，而引用arrayList2没有使用泛型，所以不行。

**我们可以明白，类型检查就是针对引用的，谁是一个引用，用这个引用调用泛型方法，就会对这个引用调用的方法进行类型检测，而无关它真正引用的对象。**

### 添加泛型后，获取的时候不需要强制转换？

因为类型擦除的问题，所以所有的泛型类型变量最后都会被替换为原始类型。这样就引起了一个问题，既然都被替换为原始类型，那么为什么我们在获取的时候，不需要进行强制类型转换呢？看下ArrayList和get方法：

```java
// Positional Access Operations

@SuppressWarnings("unchecked")
E elementData(int index) {
    return (E) elementData[index]; // 会根据泛型变量进行强转
}

public E get(int index) {
    rangeCheck(index);
    return elementData(index);
}
```

看以看到，在return之前，会根据泛型变量进行强转。假设泛型类型变量为Date，虽然泛型信息会被擦除掉，但是会将(E) elementData[index]，编译为(Date)elementData[index]。所以我们不用自己进行强转。
