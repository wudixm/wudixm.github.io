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
