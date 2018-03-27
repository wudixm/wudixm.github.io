---
title: Effective Java Chapter 5
excerpt: |
  Effective Java Chapter 5
category: Books
feature_image: "https://picsum.photos/2560/600?image=872"
---
## 第5章 泛型

Java 1.5 发行版本中增加了泛型(**Generic**)。在没有泛型之前，从集合中读取到的每一个对象都必须进行转换。如果有人不小心插入了类型错误的对象，在运行时的转换处理就会出错。**有了泛型之后，可以告诉编译器每个集合中接受哪些对象类型。编译器自动地为你的插入进行转化，并在编译时告知是否插入了类型错误的对象。**这样可以使程序更加安全，也更加清楚。

### 第23 条：请不要在新代码中使用原生态类型(raw type)

**声明中具有一个或者多个类型参数(type parameter) 的类或者接口，就是泛型(generic) 类或者接口。**泛型类和接口统称为**泛型(generic type)。**

每种泛型定义一组**参数化的类型(parameterized type)**，构成格式为：先是类或者接口的名称，接着用尖括号把对应于泛型形式类型的实际类型参数列表括起来。

最后一点，每个泛型都定义一个**原生态类型(raw type)**，即不带任何实际类型参数的泛型名称。例如，与List\<E\> 相对应的原生态类型是List。原生态类型就像从类型声明中删除了所有泛型信息一样。实际上，原生态类型List 与Java 平台没有泛型之前的接口类型List 完全一样。

**就如本书中经常提到的，出错之后应该尽快发现，最好是编译时就发现。**

**有了泛型，就可以利用改进后的类型声明来代替集合中的这种注释，告诉编译器之前的注释中所隐含的信息：**

```java
//Parameterized collection type - typesafe
private final Collection<Stamp> stamps = ...
```

通过这条声明，**编译器知道stamps 应该只包含Stamp 实例，并给予保证，假设整个代码是利用Java 1.5 及其之后版本的编译器进行编译的，所有代码在编译过程中都没有发出（或者禁止）任何警告。当stamps 利用一个参数化的类型进行声明时，错误的插入会产生一条编译时的错误信息，准确地告诉你哪里出错了**：

```java
Test.java:9: add(Stamp) in Collection><Stamp> cannot be applied
to (Coin)
	stamps.add(new Coin())
					^
```

还有一个好处是，**从集合中删除元素时，不再需要进行手工转换了**。编译器会替你插入隐式的转换，并确保它们不会失败（依然假设所有的代码都是通过支持泛型的编译器进行编译的，并且没有产生或者禁止任何警告）。

如上所述，**如果不提供类型参数，使用集合类型和其他泛型也仍然是合法的，但是不应该这么做。如果使用原生态类型，就失掉了泛型在安全性和表述性方面的所有优势。既然不应该使用原生态类型，为什么Java 的设计者们还要允许使用它们呢？这是为了提供兼容性。因为泛型出现的时候，Java 平台即将进入它的第二个10 年，已经存在大量没有使用泛型的Java 代码。人们认为让所有这些代码保持合法，并且能够与使用泛型的新代码互用，这一点很重要。它必须合法，才能将参数化类型的实例传递给那些被设计成使用普通类型的方法，反之亦然。这种需求被称作移植兼容性（Migration Compatibility），促成了支持原生态类型的决定。**

**虽然不应该在新代码中使用像List 这样的原生态类型，使用参数化的类型以允许插入任意对象，如List\<Object\> ，这还是可以的。原生态类型List 和参数化的类型List\<Object\> 之间到底有什么区别呢？不严格的讲，前者逃避了泛型检查，后者则明确告知编译器，它能够持有任意类型的对象。**

**虽然你可以将List\<String\> 传递给类型List 的参数，但是不能将它传给类型List\<Object\> 的参数。**

泛型有子类型化（subtyping）的规则，List\<String\> 是原生态类型List 的一个子类型，而不是参数化类型List\<Object\> 的子类型。因此，**如果使用像List 这样的原生态类型，就会失掉类型安全性，但是如果使用像List\<Object\> 这样的参数化类型，则不会。**

```java
ArrayList<String> a = new ArrayList<String>();

// 编译无错，合法
List b = a;

// 编译出错：
// Error:(12, 26) java: 不兼容的类型: 
// java.util.ArrayList<java.lang.String>无法转换为java.util.List<java.lang.Object>
List<Object> c = a;
```

**在不确定或者不在乎集合中的元素类型的情况下，你也许会使用原生态类型。**例如，假设想要编写一个方法，它有两个集合（Set），并从中返回它们共有的元素的数量。如果你对泛型还不熟悉的话，可以参考以下方式来编写这种方法：

```java
// Use of raw type for unknown element type - don`t do this
static int num(Set s1, Set s2) {
    int result = 0;
    for (Object o : s1) {
        if (s2.contains(o)) {
            result++;
        }
    }
    return result;
}
```

这个方法倒是可以，但它使用了原生态类型，这是很危险的。**从Java 1.5 发行版本开始，Java 就提供了一种安全的替代方法，称作无限制的通配符类型（unbounded wildcard type）。如果要使用泛型，但不确定或者不关心实际的类型参数，就可以使用一个问号代替。**

例如，泛型Set\<E\> 的无限制通配符类型为Set\<?\>（读作“某个类型的集合”）。这是最普通的参数化Set 类型，可以持有任何集合。

```java
// Unbounded wildcard type - typesafe and flexible
static int numCommon(Set<?> s1, Set<?> s2) {
    int result = 0;
    for (Object o : s1) {
        if (s2.contains(o)) {
            result++;
        }
    }
    return result;
}
```

**在无限制通配类型Set\<?\> 和原生态类型Set 之间有什么区别呢？这个问号真正起到作用了吗？这一点不需要赘述，但通配符类型是安全的，原生态类型则不安全。由于可以将任何元素放进使用原生态类型的集合中，因此很容易破坏该集合的类型约束条件（可以将元素添加到集合中，但是取出来的时候容易出现类型转换异常），但是不能将任何元素(除了null 之外) 放到Collection\<?\> 中。如果这么做，将会产生一条编译时的错误信息**：

```
Test.java:41: 错误: 对于add(String), 找不到合适的方法
        s1.add("a");
          ^
    方法 Collection.add(CAP#1)不适用
      (参数不匹配; String无法转换为CAP#1)
    方法 Set.add(CAP#1)不适用
      (参数不匹配; String无法转换为CAP#1)
  其中, CAP#1是新类型变量:
    CAP#1从?的捕获扩展Object
1 个错误
```

这样的错误消息显然还无法令人满意，但是编译器已经尽到了它的职责，防止你破坏集合的类型约束条件。**你不仅无法将任何元素（除了null 之外）放进Collection\<?\> 中，而且根本无法猜测你会得到哪种类型的对象。**要是无法接受这些限制，就可以使用泛型方法（generic method，见第27条）或者有限制的通配符类型（bounded wildcard type 见第28条）

不要在新代码中使用原生态类型，这条规则有两个小小的例外，**两者都源于“泛型信息可以在运行时被擦除（见第25条）”这一事实。在类文字(class literal) 中必须使用原生态类型。**规范不允许使用参数化类型。换句话说，List.class，String[].class 和int.class 都是合法的，但是List\<String\>.class 和List\<?\>.class 则不合法。

这条规则的第二个例外与instanceof 操作符有关。**由于泛型信息可以在运行时被擦除，因此在参数化类型而非无限制通配符类型上使用instanceof 操作符是非法的。用无限制通配符类型代替原生态类型，对instanceof 操作符的行为不会产生任何影响。**

```java
if (o instanceof Set<?>) {//正确
}
if (o instanceof Set) {//正确
    Set<?> m = (Set<?>) o;
}
// 下面这个抛出异常，Error:(45, 21) java: instanceof 的泛型类型不合法
//if (o instancegvvvvvvvvvvvvvvvvvvvvvvvhof Set<String>) {
//}
```

**注意，一旦确定这个o 是个Set，就必须将它转换成通配符类型Set\<?\>，而不是转换成原生态类型Set。**这个是受检的(checked) 的转换，因此不会导致编译时警告。

总之，**使用原生态类型会在运行时导致异常，因此不要在新代码中使用。原生态类型只是为了与引入泛型之前的遗留代码进行兼容和互用而提供的。**

**让我们做个快速的回顾：Set\<Object\> 是个参数化的类型，表示可以包含任何对象类型的一个集合；Set\<?\> 则是一个通配符类型，表示只能包含某种未知对象类型的一个集合；Set 则是个原生态类型，它脱离了泛型系统。前两种是安全的，后一种不安全。**

### 第24 条：消除非受检警告

