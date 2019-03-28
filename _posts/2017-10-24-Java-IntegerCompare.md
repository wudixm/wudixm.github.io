---
title: Integer 的缓存机制
excerpt: |
  Integer 的缓存机制
category: Java 基础
feature_image: "https://picsum.photos/2560/600?image=872"
---
### 问题

```java
Integer itga = 10;
Integer itgb = 10;
System.out.println(itga == intb); // 打印true

Integer itgc = 2000;
Integer itgd = 2000;
System.out.println(itgc == itgd); //打印false
```

经过反编译`javap -c File`后，可以看到如下字节码：

```java
60: bipush        10
62: invokestatic  #8                  // Method java/lang/Integer.valueOf:(I)Ljava/lang/Integer;
65: astore        5
67: bipush        10
69: invokestatic  #8                  // Method java/lang/Integer.valueOf:(I)Ljava/lang/Integer;
72: astore        6
74: getstatic     #3                  // Field java/lang/System.out:Ljava/io/PrintStream;
77: aload         5
79: aload         6
81: if_acmpne     88
84: iconst_1
85: goto          89
88: iconst_0
89: invokevirtual #4                  // Method java/io/PrintStream.println:(Z)V
92: sipush        2000
95: invokestatic  #8                  // Method java/lang/Integer.valueOf:(I)Ljava/lang/Integer;
98: astore        7
100: sipush        2000
103: invokestatic  #8                  // Method java/lang/Integer.valueOf:(I)Ljava/lang/Integer;
106: astore        8
108: getstatic     #3                  // Field java/lang/System.out:Ljava/io/PrintStream;
111: aload         7
113: aload         8
115: if_acmpne     122
118: iconst_1
119: goto          123
122: iconst_0
123: invokevirtual #4                  // Method java/io/PrintStream.println:(Z)V
126: return
```

从以上信息得出：

Integer a = 10；这一句执行的时候是按照如下步骤：

- 把常量10放到栈顶
- 调用Integer的valueOf方法获得一个返回值（一个引用）
- 保存到一个变量中（如astore 5，或者说a）

查看Integer的valueOf(int args)方法：

```java
public static Integer valueOf(int i) {
    if (i >= Integer.IntegerCache.low && i <= Integer.IntegerCache.high)
        return Integer.IntegerCache.cache[i + (-Integer.IntegerCache.low)];//重点
    return new Integer(i);
}
```

IntegerCache类是Integer的私有的静态的内部类，里面有一个static块，会对其cache数组进行默认的一个赋值操作：

```java
private static class IntegerCache {
    static final int low = -128;
    static final int high;
    static final Integer cache[];

    static {
        // high value may be configured by property
        int h = 127;
        String integerCacheHighPropValue =
                sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
        if (integerCacheHighPropValue != null) {
            try {
                int i = parseInt(integerCacheHighPropValue);
                i = Math.max(i, 127);
                // Maximum array size is Integer.MAX_VALUE
                h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
            } catch( NumberFormatException nfe) {
                // If the property cannot be parsed into an int, ignore it.
            }
        }
        high = h;

        cache = new Integer[(high - low) + 1]; //重点，创建一个空的Integer数组，长度为high - low + 1，默认长度为256
        int j = low;
        for(int k = 0; k < cache.length; k++)
            cache[k] = new Integer(j++); //重点，对cache数组赋值，默认为值从-128到127，递增1，如果取值为10的元素，则要取出cache中的第10+ (-low) 号元素

        // range [-128, 127] must be interned (JLS7 5.1.7)
        assert Integer.IntegerCache.high >= 127;
    }

    private IntegerCache() {}
}
```

1. **从最下面的一条注释及后面的判断可知，high 要大于等于127(low没有改变值，一直是-128)，cache数组 的length为`high - low + 1`，这个值默认为`127 - (-128) + 1 = 256`**
2. **在valueOf方法中有判断传入的参数i，在-128 到 127之间会返回cache的(i + (-low))号元素。**
3. 可以得出**在-128 到127 之间的数，去取valueOf()方法的值的时候，会直接返回已经cache好了的cache数组中的某个对象。**
4. high这个值可以配置，sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high")

