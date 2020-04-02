## 测试代码

```java
public class test1 {
    public static void main(String[] args) {
        IO i1 = new IO1();
        i1.io();

        IO i2 = new IO2();
        i2.io();

    }
}

interface IO {
    void io();
}

class IO1 implements IO {
    public void io() {
        FileInputStream fis = null;
        InputStreamReader isr = null;
        BufferedReader br = null;
        try {
            fis = new FileInputStream("/Users/wuxiaoming/IdeaProjects/Practise0824/PractiseTest/src/javap/test_try_with_resources/1.txt");
            isr = new InputStreamReader(fis);
            br = new BufferedReader(isr);
            String read = br.readLine();
            System.out.println("read = " + read);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (br != null) {
                try {
                    br.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (isr != null) {
                try {
                    isr.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (fis != null) {
                try {
                    fis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}

class IO2 implements IO {
    public void io() {
        try (FileInputStream fis = new FileInputStream("/Users/wuxiaoming/IdeaProjects/Practise0824/PractiseTest/src/javap/test_try_with_resources/1.txt");
             InputStreamReader isr = new InputStreamReader(fis);
             BufferedReader br = new BufferedReader(isr);
        ) {
            String read = br.readLine();
            System.out.println("read = " + read);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```

## javap 反编译后做对比

```java

// IO1 try{} 普通的try catch

  public void io();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=3, locals=7, args_size=1
         0: aconst_null
         1: astore_1
         2: aconst_null
         3: astore_2
         4: aconst_null
         5: astore_3
         6: new           #2                  // class java/io/FileInputStream
         9: dup
        10: ldc           #3                  // String /Users/wuxiaoming/IdeaProjects/Practise0824/PractiseTest/src/javap/test_try_with_resources/1.txt
        12: invokespecial #4                  // Method java/io/FileInputStream."<init>":(Ljava/lang/String;)V
        15: astore_1
        16: new           #5                  // class java/io/InputStreamReader
        19: dup
        20: aload_1
        21: invokespecial #6                  // Method java/io/InputStreamReader."<init>":(Ljava/io/InputStream;)V
        24: astore_2
        25: new           #7                  // class java/io/BufferedReader
        28: dup
        29: aload_2
        30: invokespecial #8                  // Method java/io/BufferedReader."<init>":(Ljava/io/Reader;)V
        33: astore_3
        34: aload_3
        35: invokevirtual #9                  // Method java/io/BufferedReader.readLine:()Ljava/lang/String;
        38: astore        4
        40: getstatic     #10                 // Field java/lang/System.out:Ljava/io/PrintStream;
        43: new           #11                 // class java/lang/StringBuilder
        46: dup
        47: invokespecial #12                 // Method java/lang/StringBuilder."<init>":()V
        50: ldc           #13                 // String read =
        52: invokevirtual #14                 // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
        55: aload         4
        57: invokevirtual #14                 // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
        60: invokevirtual #15                 // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
        63: invokevirtual #16                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
        66: aload_3
        67: ifnull        84
        70: aload_3
        71: invokevirtual #17                 // Method java/io/BufferedReader.close:()V
        74: goto          84
        77: astore        4
        79: aload         4
        81: invokevirtual #19                 // Method java/io/IOException.printStackTrace:()V
        84: aload_2
        85: ifnull        102
        88: aload_2
        89: invokevirtual #20                 // Method java/io/InputStreamReader.close:()V
        92: goto          102
        95: astore        4
        97: aload         4
        99: invokevirtual #19                 // Method java/io/IOException.printStackTrace:()V
       102: aload_1
       103: ifnull        310
       106: aload_1
       107: invokevirtual #21                 // Method java/io/FileInputStream.close:()V
       110: goto          310
       113: astore        4
       115: aload         4
       117: invokevirtual #19                 // Method java/io/IOException.printStackTrace:()V
       120: goto          310
       123: astore        4
       125: aload         4
       127: invokevirtual #23                 // Method java/io/FileNotFoundException.printStackTrace:()V
       130: aload_3
       131: ifnull        148
       134: aload_3
       135: invokevirtual #17                 // Method java/io/BufferedReader.close:()V
       138: goto          148
       141: astore        4
       143: aload         4
       145: invokevirtual #19                 // Method java/io/IOException.printStackTrace:()V
       148: aload_2
       149: ifnull        166
       152: aload_2
       153: invokevirtual #20                 // Method java/io/InputStreamReader.close:()V
       156: goto          166
       159: astore        4
       161: aload         4
       163: invokevirtual #19                 // Method java/io/IOException.printStackTrace:()V
       166: aload_1
       167: ifnull        310
       170: aload_1
       171: invokevirtual #21                 // Method java/io/FileInputStream.close:()V
       174: goto          310
       177: astore        4
       179: aload         4
       181: invokevirtual #19                 // Method java/io/IOException.printStackTrace:()V
       184: goto          310
       187: astore        4
       189: aload         4
       191: invokevirtual #19                 // Method java/io/IOException.printStackTrace:()V
       194: aload_3
       195: ifnull        212
       198: aload_3
       199: invokevirtual #17                 // Method java/io/BufferedReader.close:()V
       202: goto          212
       205: astore        4
       207: aload         4
       209: invokevirtual #19                 // Method java/io/IOException.printStackTrace:()V
       212: aload_2
       213: ifnull        230
       216: aload_2
       217: invokevirtual #20                 // Method java/io/InputStreamReader.close:()V
       220: goto          230
       223: astore        4
       225: aload         4
       227: invokevirtual #19                 // Method java/io/IOException.printStackTrace:()V
       230: aload_1
       231: ifnull        310
       234: aload_1
       235: invokevirtual #21                 // Method java/io/FileInputStream.close:()V
       238: goto          310
       241: astore        4
       243: aload         4
       245: invokevirtual #19                 // Method java/io/IOException.printStackTrace:()V
       248: goto          310
       251: astore        5
       253: aload_3
       254: ifnull        271
       257: aload_3
       258: invokevirtual #17                 // Method java/io/BufferedReader.close:()V
       261: goto          271
       264: astore        6
       266: aload         6
       268: invokevirtual #19                 // Method java/io/IOException.printStackTrace:()V
       271: aload_2
       272: ifnull        289
       275: aload_2
       276: invokevirtual #20                 // Method java/io/InputStreamReader.close:()V
       279: goto          289
       282: astore        6
       284: aload         6
       286: invokevirtual #19                 // Method java/io/IOException.printStackTrace:()V
       289: aload_1
       290: ifnull        307
       293: aload_1
       294: invokevirtual #21                 // Method java/io/FileInputStream.close:()V
       297: goto          307
       300: astore        6
       302: aload         6
       304: invokevirtual #19                 // Method java/io/IOException.printStackTrace:()V
       307: aload         5
       309: athrow
       310: return
      Exception table:
         from    to  target type
            70    74    77   Class java/io/IOException
            88    92    95   Class java/io/IOException
           106   110   113   Class java/io/IOException
             6    66   123   Class java/io/FileNotFoundException
           134   138   141   Class java/io/IOException
           152   156   159   Class java/io/IOException
           170   174   177   Class java/io/IOException
             6    66   187   Class java/io/IOException
           198   202   205   Class java/io/IOException
           216   220   223   Class java/io/IOException
           234   238   241   Class java/io/IOException
             6    66   251   any
           123   130   251   any
           187   194   251   any
           257   261   264   Class java/io/IOException
           275   279   282   Class java/io/IOException
           293   297   300   Class java/io/IOException
           251   253   251   any


// IO2 try() 这个类型的
  public void io();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=3, locals=14, args_size=1
         0: new           #2                  // class java/io/FileInputStream
         3: dup
         4: ldc           #3                  // String /Users/wuxiaoming/IdeaProjects/Practise0824/PractiseTest/src/javap/test_try_with_resources/1.txt
         6: invokespecial #4                  // Method java/io/FileInputStream."<init>":(Ljava/lang/String;)V
         9: astore_1
        10: aconst_null
        11: astore_2
        12: new           #5                  // class java/io/InputStreamReader
        15: dup
        16: aload_1
        17: invokespecial #6                  // Method java/io/InputStreamReader."<init>":(Ljava/io/InputStream;)V
        20: astore_3
        21: aconst_null
        22: astore        4
        24: new           #7                  // class java/io/BufferedReader
        27: dup
        28: aload_3
        29: invokespecial #8                  // Method java/io/BufferedReader."<init>":(Ljava/io/Reader;)V
        32: astore        5
        34: aconst_null
        35: astore        6
        37: aload         5
        39: invokevirtual #9                  // Method java/io/BufferedReader.readLine:()Ljava/lang/String;
        42: astore        7
        44: getstatic     #10                 // Field java/lang/System.out:Ljava/io/PrintStream;
        47: new           #11                 // class java/lang/StringBuilder
        50: dup
        51: invokespecial #12                 // Method java/lang/StringBuilder."<init>":()V
        54: ldc           #13                 // String read =
        56: invokevirtual #14                 // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
        59: aload         7
        61: invokevirtual #14                 // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
        64: invokevirtual #15                 // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
        67: invokevirtual #16                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
        70: aload         5
        72: ifnull        157
        75: aload         6
        77: ifnull        100
        80: aload         5
        82: invokevirtual #17                 // Method java/io/BufferedReader.close:()V
        85: goto          157
        88: astore        7
        90: aload         6
        92: aload         7
        94: invokevirtual #19                 // Method java/lang/Throwable.addSuppressed:(Ljava/lang/Throwable;)V
        97: goto          157
       100: aload         5
       102: invokevirtual #17                 // Method java/io/BufferedReader.close:()V
       105: goto          157
       108: astore        7
       110: aload         7
       112: astore        6
       114: aload         7
       116: athrow
       117: astore        8
       119: aload         5
       121: ifnull        154
       124: aload         6
       126: ifnull        149
       129: aload         5
       131: invokevirtual #17                 // Method java/io/BufferedReader.close:()V
       134: goto          154
       137: astore        9
       139: aload         6
       141: aload         9
       143: invokevirtual #19                 // Method java/lang/Throwable.addSuppressed:(Ljava/lang/Throwable;)V
       146: goto          154
       149: aload         5
       151: invokevirtual #17                 // Method java/io/BufferedReader.close:()V
       154: aload         8
       156: athrow
       157: aload_3
       158: ifnull        238
       161: aload         4
       163: ifnull        185
       166: aload_3
       167: invokevirtual #20                 // Method java/io/InputStreamReader.close:()V
       170: goto          238
       173: astore        5
       175: aload         4
       177: aload         5
       179: invokevirtual #19                 // Method java/lang/Throwable.addSuppressed:(Ljava/lang/Throwable;)V
       182: goto          238
       185: aload_3
       186: invokevirtual #20                 // Method java/io/InputStreamReader.close:()V
       189: goto          238
       192: astore        5
       194: aload         5
       196: astore        4
       198: aload         5
       200: athrow
       201: astore        10
       203: aload_3
       204: ifnull        235
       207: aload         4
       209: ifnull        231
       212: aload_3
       213: invokevirtual #20                 // Method java/io/InputStreamReader.close:()V
       216: goto          235
       219: astore        11
       221: aload         4
       223: aload         11
       225: invokevirtual #19                 // Method java/lang/Throwable.addSuppressed:(Ljava/lang/Throwable;)V
       228: goto          235
       231: aload_3
       232: invokevirtual #20                 // Method java/io/InputStreamReader.close:()V
       235: aload         10
       237: athrow
       238: aload_1
       239: ifnull        309
       242: aload_2
       243: ifnull        262
       246: aload_1
       247: invokevirtual #21                 // Method java/io/FileInputStream.close:()V
       250: goto          309
       253: astore_3
       254: aload_2
       255: aload_3
       256: invokevirtual #19                 // Method java/lang/Throwable.addSuppressed:(Ljava/lang/Throwable;)V
       259: goto          309
       262: aload_1
       263: invokevirtual #21                 // Method java/io/FileInputStream.close:()V
       266: goto          309
       269: astore_3
       270: aload_3
       271: astore_2
       272: aload_3
       273: athrow
       274: astore        12
       276: aload_1
       277: ifnull        306
       280: aload_2
       281: ifnull        302
       284: aload_1
       285: invokevirtual #21                 // Method java/io/FileInputStream.close:()V
       288: goto          306
       291: astore        13
       293: aload_2
       294: aload         13
       296: invokevirtual #19                 // Method java/lang/Throwable.addSuppressed:(Ljava/lang/Throwable;)V
       299: goto          306
       302: aload_1
       303: invokevirtual #21                 // Method java/io/FileInputStream.close:()V
       306: aload         12
       308: athrow
       309: goto          325
       312: astore_1
       313: aload_1
       314: invokevirtual #23                 // Method java/io/FileNotFoundException.printStackTrace:()V
       317: goto          325
       320: astore_1
       321: aload_1
       322: invokevirtual #25                 // Method java/io/IOException.printStackTrace:()V
       325: return
      Exception table:
         from    to  target type
            80    85    88   Class java/lang/Throwable
            37    70   108   Class java/lang/Throwable
            37    70   117   any
           129   134   137   Class java/lang/Throwable
           108   119   117   any
           166   170   173   Class java/lang/Throwable
            24   157   192   Class java/lang/Throwable
            24   157   201   any
           212   216   219   Class java/lang/Throwable
           192   203   201   any
           246   250   253   Class java/lang/Throwable
            12   238   269   Class java/lang/Throwable
            12   238   274   any
           284   288   291   Class java/lang/Throwable
           269   276   274   any
             0   309   312   Class java/io/FileNotFoundException
             0   309   320   Class java/io/IOException

```

发现在try-with-resources 里会有代码

```
  90: aload         6
  92: aload         7
  94: invokevirtual #19                 // Method java/lang/Throwable.addSuppressed:(Ljava/lang/Throwable;)V
  97: goto          157
  100: aload         5

```

### throwable 里的addSuppressed 方法

开发人员对异常处理的try-catch-finally语句块都比较熟悉。如果在try语句块中抛出了异常，在控制权转移到调用栈上一层代码之前，finally语句块中的语句也会执行。但是finally语句块在执行的过程中，也可能会抛出异常。如果finally语句块也抛出了异常，那么这个异常会往上传递，而之前try语句块中的那个异常就丢失了。

throwable 里的addSuppressed 方法是为了防止finally 语句中也抛出了异常，导致try 中的异常没了，所以扔到了一个list 里。

这么做的好处是不会丢失任何异常。在java7之前，这种做法需要实现自己的异常类，而在java7中，已经对Throwable类进行了修改以支持这种情况。在java7中为Throwable类增加addSuppressed方法。当一个异常被抛出的时候，可能有其他异常因为该异常而被抑制住，从而无法正常抛出。这时可以通过addSuppressed方法把这些被抑制的方法记录下来。被抑制的异常会出现在抛出的异常的堆栈信息中，也可以通过getSuppressed方法来获取这些异常。这样做的好处是不会丢失任何异常，方便开发人员进行调试。如例：


### 捕获多个异常

1.7 catch 还可以捕获多个异常

**需要注意的是，在catch子句中声明捕获的这些异常类中，不能出现重复的类型，也不允许其中的某个异常是另外一个异常的子类，否则会出现编译错误。如果在catch子句中声明了多个异常类，那么异常参数的具体类型是所有这些异常类型的最小上界。**

关于一个catch子句中的异常类型不能出现其中一个是另外一个的子类的情况，实际上涉及捕获多个异常的内部实现方式。比如：

```java
// 这个是可以的
try {
        Integer.parseInt("Hello");
    } catch (NumberFormatException | RuntimeException e){}

// 这个是不行的
try {
        Integer.parseInt("Hello");
    } catch (RuntimeException | NumberFormatException e) {}

```

原因在于，编译器的做法其实是把捕获多个异常的catch子句转换成了多个catch子句，在每个catch子句中捕获一个异常。上面这段代码相当于：

```java
 try {
        Integer.parseInt("Hello");
    } catch (RuntimeException e) {
    } catch (NumberFormatException e) {
    }
```

### athrow

**在某个区间抛出了athrow，则在对应的区间在异常处理表中找对应的异常，用handler 去处理**

如果在方法中使用了 try-catch 语句，那么在该方法的 code 属性中就会有一个异常处理表。这里之所以叫表，是因为我们可能会 catch 多个异常，每 catch 一个异常，就会产生一条表项。
在上面的 test 方法中就有异常表，因为我们这里只有一个 catch，所以异常表只有一条表项

{
    start:3
    end:6
    hadlerPC:9
    catchType: #5 (java/lang/NumberFormatException)
}
JVM 中定义的异常处理表项有四个字段：均为整数

startPc
endPc
handlerPc
catchType
其含义如下：
[startPc,endPc) 一个区间:锁定一个指令区间,该区间可能抛出某种异常。test 的异常表表示在字节码 3 到字节码 6(不包含 6)之间，可能会抛出异常，而指令 3 正是调用了抛出异常的 func 方法。
handlerPc:负责处理异常的 catch 块的其实位置，一旦发现某个 catch 可以处理该异常，那么就将 nextPc 改为 handlerPc。
catchType:指向运行时常量池的一个索引,通过该索引可以得到一个异常的类 xxxException 的对象，也就是我们在代码中显式 catch 的异常的类型。
