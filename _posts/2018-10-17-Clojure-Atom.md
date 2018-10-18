## Clojure 总结

### Clojure Atom

Atoms provide a way to manage shared, synchronous, independent state. They are a reference type like refs and vars. You create an atom with atom, and can access its state with deref/@. Like refs and agents, atoms support validators. To change the value of an atom, you can use swap!. A lower-level compare-and-set! is also provided. Changes to atoms are always free of race conditions.

As with all reference types, the intended use of atom is to hold one of Clojure’s immutable data structures. And, similar to ref’s alter and agent’s send, you change the value by applying a function to the old value. This is done in an atomic manner by swap! Internally, swap! reads the current value, applies the function to it, and attempts to compare-and-set! it in. Since another thread may have changed the value in the intervening time, it may have to retry, and does so in a spin loop. The net effect is that the value will always be the result of the application of the supplied function to a current value, atomically. However, because the function might be called multiple times, it must be free of side effects.

Atoms are an efficient way to represent some state that will never need to be coordinated with any other, and for which you wish to make synchronous changes (unlike agents, which are similarly independent but asynchronous). A typical usage might be for memoization:

(defn memoize [f]
  (let [mem (atom {})]
    (fn [& args]
      (if-let [e (find @mem args)]
        (val e)
        (let [ret (apply f args)]
          (swap! mem assoc args ret)
          ret)))))

(defn fib [n]
  (if (<= n 1)
    n
    (+ (fib (dec n)) (fib (- n 2)))))

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
