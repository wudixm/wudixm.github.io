## Clojure 总结

### Clojure Atom

Atoms provide a way to manage shared, synchronous, independent state. They are a reference type like refs and vars. You create an atom with atom, and can access its state with deref/@. Like refs and agents, atoms support validators. To change the value of an atom, you can use swap!. A lower-level compare-and-set! is also provided. Changes to atoms are always free of race conditions.

As with all reference types, the intended use of atom is to hold one of Clojure’s immutable data structures. And, similar to ref’s alter and agent’s send, you change the value by applying a function to the old value. This is done in an atomic manner by swap! Internally, swap! reads the current value, applies the function to it, and attempts to compare-and-set! it in. Since another thread may have changed the value in the intervening time, it may have to retry, and does so in a spin loop. The net effect is that the value will always be the result of the application of the supplied function to a current value, atomically. However, because the function might be called multiple times, it must be free of side effects.

Atoms are an efficient way to represent some state that will never need to be coordinated with any other, and for which you wish to make synchronous changes (unlike agents, which are similarly independent but asynchronous). A typical usage might be for memoization:

(defn memoize [f]
  (let [mem (atom {})]
​    (fn [& args]
​      (if-let [e (find @mem args)]
​        (val e)
​        (let [ret (apply f args)]
​          (swap! mem assoc args ret)
​          ret)))))

(defn fib [n]
  (if (<= n 1)
​    n
​    (+ (fib (dec n)) (fib (- n 2)))))

(time (fib 35))
user=> "Elapsed time: 941.445 msecs"

(def fib (memoize fib))

(time (fib 35))

user=> "Elapsed time: 0.044 msecs"

```clojure
(ns com.example.test2)
(defn example []
  (def x 1)
  (while (< x 5)
    (do
      (println x)
      (def x (+ x 1))
      )
    )
  )
(example)
(defn example2 []
  (def x (atom 1))
  (while (< @x 5)
    (do
      (println @x)
      (swap! x inc)
      )
    )
  
  )
(example2)
```
### Run clj file
With clojure
I can't believe no one has suggested this yet. You should be able to just do

clojure path/to/script.clj
This works using the clojure installed on Ubuntu with apt-get. Not sure about other installs...

With lein-exec
However, if the script you're working on has any dependencies, I would recommend the lein-exec plugin. This has the advantage of letting you use Leiningen to handle any dependencies, which is sweet. Leiningen already has a lein run command, but it only works for executing main functions within a full Clojure/lein project. So the lein-exec plugin is a really nice complement for small scale scripting.

### if 语句

if 这个special form跟java里面的if的语义是一样的， 它接受三个参数， 第一个是需要判断的条件，第二个表达式是条件成立的时候要执行的表达式，第三个参数是可选的，在条件不成立的时候执行。**如果需要执行多个表达式，那么把多个表达式包在do里面**。看例子：

```clojure
(import '(java.util Calendar GregorianCalendar))
(let [gc (GregorianCalendar.)
      day-of-week (.get gc Calendar/DAY_OF_WEEK)
      is-weekend (or (= day-of-week Calendar/SATURDAY) (= day-of-week Calendar/SUNDAY))]
  (if is-weekend
    (println "play")
    (do (println "work")
        (println "sleep"))))
```
### cond 与case

#### cond

以下是 Cond 声明的一般形式。
```
cond
(expression evaluation1) statement #1
(expression evaluation2) statement #2
(expression evaluationN) statement #N
:else statement #Default
```
Cond 声明一般在以下两种情况下使用:

1. 有多个表达式求值定义，每个都有一个语句被执行。
2. 还有一个默认语句，如果没有任何先前值计算为true，则执行该语句。 这是由else语句定义的。

#### case
语法
```
case expression
value1 statement #1
value2 statement #2
valueN statement #N
statement #Default
```
Case声明一般在以下几种情况下使用:

1. 要评估的表达式放在“case”语句中。 这通常将求值为一个值，该值在随后的语句中使用。
2. 每个值都是根据'case'表达式传递的值来计算的。 根据哪个值成立，将执行后续语句。
3. 还有一个默认语句，如果前面的值都不为真，则执行该语句。

#### cond case 区别
1. case 只能对x 是什么值做相等判断，而不能是表达式，而cond 就是为了表达式出现的。

#### 匿名函数

1. 不用匿名函数时的常规做法

先来看看不使用匿名函数的常规做法，首先得定义一个过滤函数：

```
(defn value-gt-2? [value](> value 2))
```
接着才能执行filter函数进行过滤：

```
(filter
  value-gt-2? [9 10 2 9 19 1 -1 99 139])
```
结果如下：

```
user=> (defn value-gt-2? [value](> value 2))
#'user/value-gt-2?
user=> (filter
  #_=>   value-gt-2? [9 10 2 9 19 1 -1 99 139])
(9 10 9 19 99 139)
user=>
```
2. 循序渐进使用匿名函数

如果`value-gt-2?`函数只是临时个别使用的，上面的代码其实显得蛮累赘的，可以通过匿名函数重新实现需求：

 ```
 (filter
  (fn [value] (> value 2)) [9 10 2 9 19 1 -1 99 139])
 ```
`(fn [value] (> value 2))`就是一个匿名函数了，瞧瞧它真的是没有函数名吖（废话）。
但是不是这样的代码就算最简洁了呢？当然不是，还可以采用隐式参数名称，参数被命名为`%1、%2......`诸如此类的，而第一个参数也可以使用%表示。同时，也省去了匿名函数形式fn，代以#来实现：

```
(filter
  #(> % 2) [9 10 2 9 19 1 -1 99 139])
当然，跟绑定与解构联合使用，威力就更是增加不小！

(let [value-gt-2? (fn [value](> value 2))]
  (filter
    value-gt-2? [9 10 2 9 19 1 -1 99 139]))
(9 10 9 19 99 139)
```
### 绑定匿名函数
[绑定匿名函数](https://my.oschina.net/clopopo/blog/142394)
首先我们看下面这个例子：

```
user=> (def alphabet "abcdefghijklmnopqrstuvwxyz")  ;;将aplhabet绑定到一个字符串
       #'user/alphabet

user=> (def alphabet-length (count apphabet))       ;;将alphabet-length与长度绑定

user=> alphabet                                     ;;此时为26
       26
user=> (def alphabet "abc")                         ;;重新绑定到新的字符串

user=> alphabet                                     ;;依然是26,没有变化
       26
```
上面例子中，我们改变alphabet后，alphabet-length是没有变化的。当然对于上面例子来说，没有变化是正常的。但是我们如何能让alphabet的变化能体现在alphabet-length上呢？这就需要函数绑定了。
我们可以使用关键字  " fn " 来定义一个匿名函数。

```
user=> (fn[](count alphabet))  ;;这里定义了一个匿名函数
        user$eval6036$fn__6037 user$eval6036$fn__6037@6e3ebbb0

user=> (def alphabet-length (fn[](count alphabet)))  ;;这里定义了一个同样的匿名函数，不过绑定到了alphabet-length上
       #'user/alphabet-length
```
上面有一个注意点，函数中使用的alphabet是我们之前已经绑定好的。使用def的绑定属于全局绑定，就是说对于其他函数是可见的。在clojure中，函数总会有一个返回值，那就是函数体的执行能到达的最后一个表达式。所以上面定义的匿名函数最终会返回alphabet的长度。我们将匿名函数绑定到了alphabet-length上，每次我们输入alphabet的时候，都会去执行这个匿名函数。所以我们只要去改变alphabet的值，因为这种改变时全局的，alphabet-length都会重新计算成新的字符串长度。看下面例子：
```
user=> alphabet-length  ;;不执行函数，返回函数字面量
       user$alphabet_length user$alphabet_length@71d408f7

user=> (alphabet-length)  ;;执行函数，返回函数执行结果
       3
```
我们在使用绑定函数的时候要注意,此时alphabet-length是与函数绑定的，换句话说它代表的是一个函数。在clojure中，函数的调用必须要在S表达式中。如果直接使用函数名，会返回函数的字面量，并不会执行函数。
### defmulti defmethod

　　宏defmulti 和defmethod 经常被用在一起来定义 multimethod. 宏defmulti 的参数包括一个方法名以及一个dispatch函数，这个dispatch函数的返回值会被用来选择到底调用哪个重载的函数。宏defmethod 的参数则包括方法名，dispatch的值， 参数列表以及方法体。一个特殊的dispatch值:default 是用来表示默认情况的 — 即如果其它的dispatch值都不匹配的话，那么就调用这个方法。defmethod 多定义的名字一样的方法，它们的参数个数必须一样。传给multimethod的参数会传给dipatch函数的。实现类似java的重载

```
(defmulti what_am_i class)
(defmethod what_am_i Number [args] (println args "is num"))
(defmethod what_am_i String [args] (println args "is String"))
(defmethod what_am_i :default [args] (println args "is default"))
(what_am_i 19)
(what_am_i "luochao")
(what_am_i true)
```
### 可变参数

```
(defn demo [first & others]
  (println (str first (clojure.string/join " " others)))
  )
(demo "a" )
(demo "a" "baba")
(demo "a"  "fdasfdsa" "fdasfdsa" "aaaaaaaa")
```
### clojure 语法特点
Clojure语法

Clojure是LISP家族的一门语言，这意味着在语法上，Clojure和其他的LISP语言非常相似。例如(+ 1 2)这个Clojure表达式， 在其他编程语言里一般写为1 + 2。

LISP 最著名的特性是它的代码形式和数据表示形式一致，并有一个专用词homoiconic描述这种特性。例如上面的(+ 1 2)，如果看作数据，它就是一个普通的列表。在求值时，Clojure的运行环境会把列表的第一个元素作为操作或函数，而把后面的元素作为参数。如果在运 行时不希望把一个列表作为表达式对待，可以在前面加’，例如’(+ 1 2)。这意味着Clojure程序可以像操作数据一样修改和生成其他Clojure程序。这样改变程序结构的程序在LISP语言中称为宏。宏系统使得 LISP家族的语言具有高度可扩展性。使用者可以用宏来定义更高层的领域特定语言，使得用来描述和解决问题的语言更加接近问题本身的领域。

与其他LISP语言相比，Clojure提供了更为丰富的原生数据结构，例如下面这几种。

【vector】

这里的a、b、c是Clojure的符号（Symbol）。每个符号代表运行环境里的某个对象，与其他语言里的标识符（Identifier）概念类似。

【map】

这里的:a和:b是键，而1和2是它们对应的值。:a和:b这样以:开头的表达式被称为关键字（Keyword）。它们和符号有些类似，但只代表它们自己， 而不代表任何其他对象。Ruby中也有类似概念（叫做Symbol，注意不要和Clojure的Symbol混淆）。关键字通常被用作map的键，但也可 以用其他类型做键。上面的map也可以写作{:a 1, :b 2}。逗号“,”没有实际意义，一般只为提高可读性。

【set】

当然，数据结构是可以嵌套的。Clojure并不要求一个vector、map或set里的元素是同一类型，也就是说下面的数据都是合法的：

### loop

```
(defn exampleLoop []
  (loop [x 10]
    (when (> x 0)
      (println x)
      (recur (dec  x ))
      )
    )
  )
(exampleLoop)
```
### reduce

#### reduce
clojure.core
Available since 1.0 (source)
`(reduce f coll)(reduce f val coll)`
f should be a function of 2 arguments. If val is not supplied,
returns the result of applying f to the first 2 items in coll, then
applying f to that result and the 3rd item, etc. If coll contains no
items, f must accept no arguments as well, and reduce returns the
result of calling f with no arguments.  If coll has only 1 item, it
is returned and f is not called.  If val is supplied, returns the
result of applying f to val and the first item in coll, then
applying f to that result and the 2nd item, etc. If coll contains no
items, returns val and f is not called.
#### EXAMPLES
```
(reduce + [1 2 3 4 5])  ;;=> 15
(reduce + [])           ;;=> 0
(reduce + [1])          ;;=> 1
(reduce + [1 2])        ;;=> 3
(reduce + 1 [])         ;;=> 1
(reduce + 1 [2 3])      ;;=> 6
```
### conj
clojure.core
Available since 1.0 (source)
(conj coll x)(conj coll x & xs)
conj[oin]. Returns a new collection with the xs
  'added'. (conj nil item) returns (item).  The 'addition' may
  happen at different 'places' depending on the concrete type.
#### EXAMPLES
```
;; notice that conjoining to a vector is done at the end
(conj [1 2 3] 4)
;;=> [1 2 3 4]

;; notice conjoining to a list is done at the beginning
(conj '(1 2 3) 4)
;;=> (4 1 2 3)

(conj ["a" "b" "c"] "d")
;;=> ["a" "b" "c" "d"]

;; conjoining multiple items is done in order
(conj [1 2] 3 4)
;;=> [1 2 3 4]

(conj '(1 2) 3 4)
;;=> (4 3 1 2)

(conj [[1 2] [3 4]] [5 6])
;;=> [[1 2] [3 4] [5 6]]

;; conjoining to maps only take items as vectors of length exactly 2
(conj {1 2, 3 4} [5 6])
;;=> {5 6, 1 2, 3 4}

(conj {:firstname "John" :lastname "Doe"} {:age 25 :nationality "Chinese"})
;;=> {:nationality "Chinese", :age 25, :firstname "John", :lastname "Doe"}

;; conj on a set
(conj #{1 3 4} 2)
;;=> #{1 2 3 4}
```

### line-seq
clojure.core
Available since 1.0 (source)
`(line-seq rdr)`
Returns the lines of text from rdr as a lazy sequence of strings.
rdr must implement java.io.BufferedReader.
EXAMPLES
```
link
;; Count lines of a file (loses head):
user=> (with-open [rdr (clojure.java.io/reader "/etc/passwd")]
         (count (line-seq rdr)))

link
(import '(java.io BufferedReader StringReader))

;; line terminators are stripped
user=> (line-seq (BufferedReader. (StringReader. "1\n2\n\n3")))
("1" "2" "" "3")

;; empty string gives nil
user=> (line-seq (BufferedReader. (StringReader. "")))
nil
link
;; read from standard input
user=> (nth (line-seq (java.io.BufferedReader. *in*)) 2)
  #_=> 0
  #_=> 1
  #_=> 2
"2"
```

### with-open
clojure.core
Available since 1.0 (source)
`(with-open bindings & body)`
bindings => [name init ...]
 Evaluates body in a try expression with names bound to the values
of the inits, and a finally clause that calls (.close name) on each
name in reverse order.
EXAMPLES
```
;; Opens the file 'myfile.txt' and prints out the contents.  The 
;; 'with-open' ensures that the reader is closed at the end of the 
;; form.  
;; 
;; Please note that reading a file a character at a time is not 
;; very efficient.

user=> (with-open [r (clojure.java.io/input-stream "myfile.txt")] 
         (loop [c (.read r)] 
           (if (not= c -1)
             (do 
               (print (char c)) 
               (recur (.read r))))))
link
(defn write-csv-file
  "Writes a csv file using a key and an s-o-s (sequence of sequences)"
  [out-sos out-file]

  (spit out-file "" :append false)
  (with-open [out-data (io/writer out-file)]
      (csv/write-csv out-data out-sos)))

link
;; Try to read 3 lines of text from a test file and return them (to the REPL)
;; as a list:
(with-open [r (clojure.java.io/reader "test-0.txt")]
  (binding [*in* r] (repeatedly 3 read-line)))

;; The above returns a lazy seq without reading any lines while *in* is bound
;; to the file, resulting in the original *in* (usually stdin) being read.
;; To fix, wrap the body within the *in* binding in (doall ...):
(with-open [r (clojure.java.io/reader "test-0.txt")]
  (binding [*in* r] (doall (repeatedly 3 read-line))))

;; That ensures the sequence will be fully realized with the binding of *in*
;; still in effect, thus reading all 3 lines from the test file.
```
### cons
cons
clojure.core
Available since 1.0 (source)
`(cons x seq)`
Returns a new seq where x is the first element and seq is
  the rest.
EXAMPLES
link
```
;; prepend 1 to a list
(cons 1 '(2 3 4 5 6))
;;=> (1 2 3 4 5 6)

;; notice that the first item is not expanded
(cons [1 2] [4 5 6])
;;=> ([1 2] 4 5 6)
```

### let binding

We know that symbols are names for things, and that when evaluated, Clojure replaces those symbols with their corresponding values. `+`, for instance, is a symbol which points to the verb `#<core$_PLUS_ clojure.core$_PLUS_@12992c>`.

```
user=> +
#<core$_PLUS_ clojure.core$_PLUS_@12992c>

```

When you try to use a symbol which has no defined meaning, Clojure refuses:

```
user=> cats

CompilerException java.lang.RuntimeException: Unable to resolve symbol: cats in this context, compiling:(NO_SOURCE_PATH:0:0)

```

But we can define a meaning for a symbol within a specific expression, using `let`.

```
user=> (let [cats 5] (str "I have " cats " cats."))
"I have 5 cats."

```

The `let` expression first takes a vector of *bindings*: alternating symbols and values that those symbols are *bound* to, within the remainder of the expression. “Let the symbol `cats` be 5, and construct a string composed of `"I have "`, `cats`, and `" cats"`.

Let bindings apply only within the let expression itself. They also override any existing definitions for symbols at that point in the program. For instance, we can redefine addition to mean subtraction, for the duration of a `let`:

```
user=> (let [+ -] (+ 2 3))
-1

```

But that definition doesn’t apply outside the let:

```
user=> (+ 2 3)
5

```

We can also provide *multiple* bindings. Since Clojure doesn’t care about spacing, alignment, or newlines, I’ll write this on multiple lines for clarity.

```
user=> (let [person   "joseph"
             num-cats 186]
         (str person " has " num-cats " cats!"))
"joseph has 186 cats!"

```

When multiple bindings are given, they are evaluated in order. Later bindings can use previous bindings.

```
user=> (let [cats 3
             legs (* 4 cats)]
         (str legs " legs all together"))
"12 legs all together"

```

So fundamentally, `let` defines the meaning of symbols within an expression. When Clojure evaluates a `let`, it replaces all occurrences of those symbols in the rest of the `let` expression with their corresponding values, then evaluates the rest of the expression.

### name
name
clojure.core
Available since 1.0 (source)
(name x)
Returns the name String of a string, symbol or keyword.
如下不加`(name)` 方法的话，会在打印的时候抛出`java.lang.ClassCastException: clojure.lang.Keyword cannot be cast to java.lang.String` 异常

```
(defn wrap-log [handler]
  (fn [request] (println (name (:request-method request) ) (:uri request) )
  ))
```

### ->

```
->
clojure.core
Available since 1.0 (source)
(-> x & forms)
Threads the expr through the forms. Inserts x as the
second item in the first form, making a list of it if it is not a
list already. If there are more forms, inserts the first form as the
second item in second form, etc.
© Rich Hickey. All rights reserved. Eclipse Public License 1.0
9 EXAMPLES
link
;; Use of `->` (the "thread-first" macro) can help make code
;; more readable by removing nesting. It can be especially
;; useful when using host methods:

;; Arguably a bit cumbersome to read:
user=> (first (.split (.replace (.toUpperCase "a b c d") "A" "X") " "))
"X"

;; Perhaps easier to read:
user=> (-> "a b c d" 
           .toUpperCase 
           (.replace "A" "X") 
           (.split " ") 
           first)
"X"

;; It can also be useful for pulling values out of deeply-nested
;; data structures:
user=> (def person 
            {:name "Mark Volkmann"
             :address {:street "644 Glen Summit"
                       :city "St. Charles"
                       :state "Missouri"
                       :zip 63304}
             :employer {:name "Object Computing, Inc."
                        :address {:street "12140 Woodcrest Dr."
                                  :city "Creve Coeur"
                                  :state "Missouri"
                                  :zip 63141}}})
 
user=> (-> person :employer :address :city)
"Creve Coeur"
```

### do 

用do 可以扩展if 的执行语句
```
(if (> 2 1)
            (do
            (print "2 greater than 1") ; with 'do' you can extend if block
            true))
```

### macro1

[原文](http://yangyang.ninja/clojure/2015/06/30/step-by-step-macro.html)


总结
宏可以返回一个未被求值的clojure表达式。*

你可以使用语法引用返回带命名空间的绑定变量，并且可以使用反引用来对引用中的变量进行求值*

你可以使用list 以及原始引用（quote)组装未被求值的clojure表达式，但是会引入额外的开销即过多的quote.*

宏的内容中只会返回一个表达式，所以如果要想在宏中返回多个form，那么请用do把这些表达式包装起来，如前例。*

展开反引用(~@)可以用来阻止多表达式求值，达到字面替换的目的。在有多语句需要执行的场景下很有用*

### macro2 

[原文](https://www.bookstack.cn/read/clojure-learning-notes/getting-started-macro.md)
#### Quote
##### Simple Quoting
如果我们仅仅想得到一个没有evaluated的symbol，我们可以使用quote:

```
user=> (+ 1 2)
3
user=> (quote (+ 1 2))
(+ 1 2)
user=> '(+ 1 2)
(+ 1 2)
user=> '123
123
user=> 123
123
user=> 'hello
hello
user=> hello
CompilerException java.lang.RuntimeException: Unable to resolve symbol: hello in this context
```
##### Syntax Quoting
在前面，我们通过'以及quote了解了simple quoting，Clojure还提供了syntax quoting ```

```
user=> `1
1
user=> `+
clojure.core/+
user=> '+
+
```
可以看到，syntax quoting会返回fully qualified symbol，所以使用syntax quoting能够让我们避免命名冲突。

另一个syntax quoting跟simple quoting不同的地方在于，我们可以在syntax quoting里面使用~来unquote一些form，这等于是说，我要quote这一个expression，但是这个expression里面某一个form先evaluate，譬如:

```
user=> `(+ 1 ~(inc 1))
(clojure.core/+ 1 2)
user=> `(+ 1 (inc 1))
(clojure.core/+ 1 (clojure.core/inc 1))
```
这里还需要注意一下unquote splicing:

```
user=> `(+ ~(list 1 2 3))
(clojure.core/+ (1 2 3))
user=> `(+ ~@(list 1 2 3))
(clojure.core/+ 1 2 3)
```
syntax quoting会让代码更加简洁，具体到前面print那个例子，我们let这些都加了quote，代码看起来挺丑陋的，如果用syntax quoting，如下:

```clojure
user=> (defmacro my-print2
  #_=> [expression]
  #_=> `(let [result# ~expression]
  #_=> (println result#)
  #_=> result#))
#'user/my-print2
user=> (my-print2 1)
1
1
```

宏(Macro)
宏是在读入期（而不是编译期）就进行实际代码替换的一个机制。

反引号(`)
防止宏体内的任何一个表达式被evaluate。这意味着宏体里面的代码会原封不动地替换到使用这个宏的所有的地方 – 除了以波浪号开始的那些表达式。

~和~@
当一个名字前面被加了一个波浪号，并且还在反引号里面，它的值会被替换的。如果这个名字代表的是一个序列，那么我们可以用 ~@ 来替换序列里面的某个具体元素。

id#
在一个标识符背后加上 # 意味着生成一个唯一的symbol, 比如 foo# 实际可 能就是 foo_004 可以看作是let与gensym的等价物,这在避免符号捕捉时很有用。

### 什么时候用宏macro?

[原文](https://lispcast.com/when-to-use-a-macro/)

Clojure macros do have their uses, but why should you avoid them if possible? The principle reason is that macros are not first-class in Clojure. You cannot access them at runtime. You cannot pass them as an argument to a function, nor do any of the other powerful stuff you’ve come to love from functional programming. In short, macros are not functional programming (though they can make use of it).

A function, on the other hand, is a first-class value, and so is available for awesome functional programming constructs. You should prefer functions to macros.

That said, macros are still useful because there are things macros can do that functions cannot. What are the powers of a macro that are unavailable to any other construct in Clojure? If you need any of these abilities, write a macro.

#### 1. THE CODE HAS TO RUN AT COMPILE TIME
There are just some things that need to happen at compile time. I recently wrote a macro that returns the hash of the current git commit so that the hash can be embedded in the ClojureScript compilation. This needs to be done at compile time because the script will be run somewhere else, where it cannot get the commit hash. Another example is performing expensive calculations at compile time as an optimization.

Example:

(defmacro build-time []
  (str (java.util.Date.)))
The build-time macro returns a String representation of the time it is run.

Running code at compile time is not possible in anything other than macros.

#### 2. YOU NEED ACCESS TO UNEVALED ARGUMENTS
Macros are useful for writing new, convenient syntactic constructs. And when we talk about syntax, we are typically talking about raw, unevaluated sexpressions.

Example:

(defmacro when
  "Evaluates test. If logical true, evaluates body in an implicit do."
  {:added "1.0"}
  [test & body]
  (list 'if test (cons 'do body)))
clojure.core/when is a syntactic sugar macro which transforms into an if with a do for a then and no else. The body should not be evaled before the test is checked.

Getting access to the unevaluated arguments is available by quoting (' or (quote ...)), but that is often unacceptable for syntactic constructs. Macros are the only way to do that.

#### 3. YOU NEED TO EMIT INLINE CODE
Sometimes calling a function is unacceptable. That call is either too expensive or is otherwise not the behavior you want.

For instance, in Javascript in the browser, you can call console.log('msg') to print out a message and the line number to the console. In ClojureScript, this becomes something like this: (.log js/console "msg"). Not convenient at all. My first thought was to create a function.1

(defn log [msg]
  (.log js/console msg))
This worked alright for printing the message, but the line numbers were all pointing to the same line: the body of the function! console.log records the line exactly where it is called, so it needs to be inline. I replaced it with a macro, which highlights its purpose as syntactic sugar.

Example:

(defmacro log [msg]
  `(.log js/console ~msg))
The body replaces the call to log, so it is located where it is needed for the proper behavior.

If you need inline code, a macro is the only way.

OTHER CONSIDERATIONS
Of course, any combination of these is also acceptable. And don’t forget that although you might need a macro, macros are only available at compile time. So you should consider providing a function that does the same thing and then wrap it with a macro.

CONCLUSION
Macros are very powerful. Their power comes with a price: they are only available at compile time. Because of that, functions should be preferred to macros. The use of macros should be reserved for those special occasions when their power is needed.


### symbol and var
[原文](https://stackoverflow.com/questions/9113387/difference-between-symbols-and-vars-in-clojure/9114529)

There's a symbol + that you can talk about by quoting it:

user=> '+
+
user=> (class '+)
clojure.lang.Symbol
user=> (resolve '+)
#'clojure.core/+
So it resolves to #'+, which is a Var:

user=> (class #'+)
clojure.lang.Var
The Var references the function object:

user=> (deref #'+)
#<core$_PLUS_ clojure.core$_PLUS_@55a7b0bf>
user=> @#'+
#<core$_PLUS_ clojure.core$_PLUS_@55a7b0bf>
(The @ sign is just shorthand for deref.) Of course the usual way to get to the function is to not quote the symbol:

user=> +
#<core$_PLUS_ clojure.core$_PLUS_@55a7b0bf>
Note that lexical bindings are a different mechanism, and they can shadow Vars, but you can bypass them by referring to the Var explicitly:

user=> (let [+ -] [(+ 1 2) (@#'+ 1 2)])
[-1 3]
In that last example the deref can even be left out:

user=> (let [+ -] [(+ 1 2) (#'+ 1 2)])
[-1 3]
This is because Var implements IFn (the interface for Clojure functions) by calling deref on itself, casting the result to IFn and delegating the function call to that.

The visibility mechanism used when you define private functions with defn- is based on metadata on the symbol. You can bypass it by referring directly to the Var, as above:

user=> (ns foo)
nil
foo=> (defn- private-function [] :secret)
#'foo/private-function
foo=> (in-ns 'user)
#<Namespace user>
user=> (foo/private-function)
java.lang.IllegalStateException: var: #'foo/private-function is not public (NO_SOURCE_FILE:36)
user=> (#'foo/private-function)
:secret

### 在repl 里加入当前ns

```
(ns forum.topic.util.topic-util-redis)
```

### clojure redis doc
```
(use 'clojure.repl)
(doc car/sort)
=> "SORT key [BY pattern] [LIMIT offset count] [GET pattern [GET pattern ...]] [ASC|DESC] [ALPHA] [STORE destination]

Sort the elements in a list, set or sorted set.

Available since: 1.0.0.

Time complexity: O(N+M*log(M)) where N is the number of elements in the list or set to sort, and M the number of returned elements. When the elements are not sorted, complexity is currently O(N) as there is a copy step that will be avoided in next releases."
```

### contains
contains? checks whether the index (0, 1, 2, etc.) exists in the collection.

### ns

```clojure
(ns forum.common.util.db)
```

### vector to map

```
a = ["like_count"
 "0"
 "user_id"
 "11174736"
 "timestamp"
 "1565233068"
 "name"
 "title3"
 "comment_count"
 "0"
 "description"
 "content3"]

 (apply hash-map a)
 {"like_count" "0",
 "user_id" "11174736",
 "timestamp" "1565233068",
 "name" "title3",
 "comment_count" "0",
 "description" "content3"}
```
