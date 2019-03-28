### 编译

首先,Clojure没有口译员.所有Clojure代码在加载时被编译成JVM字节码.我强调这一点,因为这是Clojure的优秀表演故事开始.



其次,你不会真正地“安装”Clojure,就是你做Ruby的意思. Clojure是一个jar文件,它只是一堆Java类;如果将jar文件放在类路径上,可以运行这些类的方法.在这些类中,clojure.main提供了运行REPL和“脚本”的主要方法.所以运行REPL确实运行着一个Java(即JVM)程序;并运行一个clj文件,要求clojure.main加载并运行它(实际工作被交付给其他类在Clojure的实现,但clojure.main是入口点). BTW,这和JRuby完全一样.

每个JVM程序最终都是“只是一个编译的Java文件”,或者是一堆这样的文件.要运行它,您需要加载一个JVM实例并运行相应的main方法.请注意,C程序(例如ruby-the-command)只是不同的,因为操作系统知道如何找到自己的主要功能(相当于Java的类路径的工作方式也不尽相同,但是主要的概念是一样的).使用JVM程序,您需要使用操作系统友好的可执行文件(java / java.exe)来踢出来

[原文](https://liujiacai.net/blog/2017/02/09/clojure-compiler-analyze-2/)

## 类生成规则

JVM 设计之初只是为 Java 语言考虑，所以最基本的概念是 class，除了八种基本类型，其他都是对象。Clojure 作为一本函数式编程语言，最基本的概念是函数，没有类的概念，那么 Clojure 代码生成以类为主的 bytecode 呢？

一种直观的想法是，每个命名空间（namespace）是一个类，命名空间里的函数相当于类的成员函数。但仔细想想会有如下问题：

1. 在 REPL 里面，可以动态添加、修改函数，如果一个命名空间相当于一个类，那么这个类会被反复加载
2. 由于函数和字符串一样是一等成员，这意味这函数既可以作为参数、也可以作为返回值，如果函数作为类的方法，是无法实现的

上述问题 2 就要求必须将函数编译成一个类。根据 [Clojure 官方文档](https://clojure.org/reference/compilation)，对应关系是这样的：

- 函数生成一个类
- 每个文件（相当于一个命名空间）生成一个`<filename>__init` 的加载类
- `gen-class` 生成固定名字的类，方便与 Java 交互
- `defrecord`、`deftype`生成同名的类，`proxy`、`reify`生成匿名的类

需要明确一点，只有在 AOT 编译时，Clojure 才会在本地生成 `.class` 文件，其他情况下生成的类均在内存中。

```
$ cat src/how_clojure_work/core.clj

(ns how-clojure-work.core)

(defn -main [& _]
 (println "Hello, World!"))
```



使用 `lein compile` 编译这个文件，会在`*compile-path*`指定的文件夹（一般是项目的`target`）下生成如下文件：

```
$ ls target/classes/how_clojure_work/

core$fn__38.class
core$loading__5569__auto____36.class
core$main.class
core__init.class
```

`core$main.class`与`core__init.class`分别表示原文件的`main`函数与命名空间加载类，那么剩下两个类是从那里来的呢？

我们知道 Clojure 里面很多“函数”其实是用宏实现的，宏在编译时会进行展开，生成新代码，上面代码中的`ns`、`defn`都是宏