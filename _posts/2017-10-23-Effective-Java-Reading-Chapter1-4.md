---
title: Effective Java 看书总结
excerpt: |
  Effective Java 看书总结
category: Books
feature_image: "https://picsum.photos/2560/600?image=872"
---
## 第1章 引言 ##

Java 5 (发行版本1.5) 中增加了许多新特性，泛型、枚举、注解、for-each循环、自动装箱、varargs、静态导入、java.util.concurrent。

本书中大多数规则都源于少数几条基本的原则：

1. **清晰性和简洁性最为重要**
2. **模块的用户永远也不应该被模块的行为所迷惑(那样就不清晰了)**
3. **模块要尽可能小，但又不能太小[本书中使用的术语`模块（Module）`），是指任何可重用的软件组件，从单个方法，到包含多个包的复杂系统，都可以是一个模块]**
4. **代码应该被重用，而不是被拷贝**
5. **模块之间的依赖性应该尽可能地降到最小**
6. **错误应该尽早被检测出来，最好是在编译时刻。**

**你不应该盲目地遵从这些规则，但是，你应该只在偶尔的情况下，有了充分理由之后才去打破这些规则。**

Java 语言支持四种类型：**接口（interface）、类（class）、数组（array）和基本类型（primitive）。**

前三种类型通常被称为引用类型（reference type)，**类实例和数组是对象（object）**，而基本类型的值则不是对象。

类的成员（member）由它的域（field）、方法（method）、成员类（member class）和成员接口（member interface）组成。

**方法的签名（signature）由它的名称和所有参数类型组成，签名不包括它的返回类型。**

## 第2章 创建和销毁对象 ##

### 第1条：考虑用静态工厂方法代替构造器

**本条目所指的静态工厂方法并不直接对应于设计模式中的工厂方法**

提供静态工厂方法而不是仅有的构造器，有优势也有劣势：

- **静态工厂方法与构造器不同的第一大优势在于，它们有名称。**如果构造器的参数本身没有确切地描述正被返回的对象，那么具有行当名称的静态工厂会更容易使用，产生的客户端代码也更易于阅读。

  一个类只能有一个带有指定签名的构造器。编程人员通常知道如何避开这一限制：**通过提供两个构造器，它们的参数列表只在参数类型的顺序上有所不同。**这是一个坏主意，面对这些的API，用户永远也记不住应该用哪个构造器，结果常常会调用错误的构造器。并且，读到使用了这些构造器的代码时，如果没有参考类的文档，往往不知所云。

  **当一个类需要多个带有相同签名的构造器时，就用静态工厂方法代替构造器，并且慎重地选择名称以便突出它们之间的区别。**

- **静态工厂方法与构造器不同的第二大优势在于，不必在每次调用它们的时候都创建一个新对象。**

  这使得不可变类(immutable class) 可以使用预先构建好的实例，或者将构建好的实例缓存起来，进行重复利用，从而避免创建不必要的重复对象。**Boolean.valueOf(boolan) 方法说明了这项技术，它从来不创建对象**。

  ```java
  /**
       * The {@code Boolean} object corresponding to the primitive
       * value {@code true}.
       */
  public static final Boolean TRUE = new Boolean(true);

  /**
       * The {@code Boolean} object corresponding to the primitive
       * value {@code false}.
       */
  public static final Boolean FALSE = new Boolean(false);

  public static Boolean valueOf(boolean b) {
      return (b ? TRUE : FALSE);
  }//返回的是类的final 变量，True 或者是 False
  ```

  这种方法类似于Flyweight模式，如果程序经常请求创建相同的对象，并且创建对象的代价很高，则这项技术可以极大地提升性能。

  编写实例受控的类(instance-controlled) 有几个原因。实例受控使得类可以确保它是一个Singleton或者是不可实例化的。它还使得不可变的类可以确保不会存在两个相等的实例，即当且仅当a==b的时候才有a.equals(b) 为true。如果类保证了这一点，它的客户端就可以使用==操作符来代替euqals(Object) 方法，这样可以提升性能。枚举（enum）类型保证了这一点。枚举本身是final的。

- **静态工厂方法与构造器不同的第三大优势在于，它们可以返回原返回类型的任何子类型的对象。**这样我们在选择返回对象的类时就有了更大的灵活性。

  这种灵活性的一种应用是，API可以返回对象，同时又不会使对象的类变成公有的。以这种方式隐藏实现类会使得API变得非常简洁。这项技术适用于基于接口的框架（interface-based framework，第18条）。**接口不能有静态方法，因此按照惯例，接口Type的静态工厂方法被放在一个名为Types 的不可实例化的类（第4条）中**

  例如，Java Collections Framework的集合接口有32个使得实现，分别提供了不可修改的集合(UnmodifiableCollection、UnmodifiableMap等)、同步集合（SynchronizedCollection、SynchronizedMap等）等。几乎所有这些实现都通过静态工厂方法在一个不可实例化的类（java.util.Collections）中导出。所有返回对象的在都是非公有的。

  公有的静态工厂方法所返回的对象的类不仅可以是非公有的，而且该类还可以随着每次调用而发生变化，这取决于静态工厂方法的参数值。只要是已声明的返回类型的子类型，都是允许的。为了提升软件的可维护性和性能，返回对象的类也可能随着发生版本的不同而不同。

  发行版本1.5中引入的类java.util.EnumSet没有公有构造器，只有静态工厂方法。返回两种实现类之一，具体则取决于底层枚举类型的大小：如果它的元素有64个或者更少，就像大多数枚举类型一样，静态工厂方法就会返回一个RegalarEnumSet实例，用单个long进行支持；如果枚举类型有65个或者更多元素，工厂就返回JumboEnumSet实例，用long数组进行支持。

  **这两个实现类的存在对象客户端来说是不可见的。如果RegularEnumSet不能再给小的枚举类型提供性能优势，就可能从未来的发行版本中将它删除，不会造成不良影响。同样地，如果事实证明对性能有好处，也可能在未来的发生版本中添加第三甚至第四个EnumSet实现，客户端永远不知道也不关心他们从工厂方法中得到的对象的类，他们只关心它是EnumSet的某个子类即可。**

  ```java
  /**
   * Creates an empty enum set with the specified element type.
   *
   * @param <E> The class of the elements in the set
   * @param elementType the class object of the element type for this enum
   *     set
   * @return An empty enum set of the specified type.
   * @throws NullPointerException if <tt>elementType</tt> is null
   */
  public static <E extends Enum<E>> EnumSet<E> noneOf(Class<E> elementType) {
      Enum<?>[] universe = getUniverse(elementType);
      if (universe == null)
          throw new ClassCastException(elementType + " not an enum");

      if (universe.length <= 64)
          return new RegularEnumSet<>(elementType, universe);
      else
          return new JumboEnumSet<>(elementType, universe);
  }
  ```

  静态工厂方法返回的对象所属的类，在编写包含该静态工厂方法的类时可以不必存在。这个灵活的静态工厂方法构成了`服务提供者框架（Service Provider Framework）`的基础，例如JDBC（Java数据库连接，Java Database Connectivity）API。

- **静态工厂方法的第四大优势在于，在创建参数化类型实例的时候，它们使代码变得更加简洁。**遗憾的是，在调用参数化类的构造器时，即使类型参数很明显，也必须指明。这通常要求你接连两次提供类型参数：

  ```java
  Map<String, List<String>> m = new HashMap<String, List<String>>();
  ```

  有了静态工厂方法，编译器可以替你找到类型参数，这被称为`类型推导（type inference）`。例如，**假设**HashMap提供了静态工厂如下：

  ```java
  public static <K, V> HashMap<K, V> newInstance(){
    return new HashMap<K, V>();
  }
  ```

  上面利用构造器创建的map就可以改为：

  ```java
  Map<String, List<String>> m = HashMap.newInstance();//类型参数并没有传入newInstance方法，java的编译器会帮助我们找到类型参数，这个newInstance方法在java 8中也还没有在hashmap中，在这里是假设的情况
  ```

- **静态工厂方法的主要缺点在于，类如果不含有公有的或者受保护的构造器，就不能被子类化。**对于公有静态工厂所返回的非公有类，也同样如此。例如，想要将Collections的任何方便的实现类子类化，这是不可能的。但是这样也有好处，因为**鼓励了使用组合(composition)，而不是继承。**

- **静态工厂方法的第二个缺点在于，它们与其他的静态方法实际上没有任何区别。**在API文档中，它们没有像构造器那样在API文档中明确标识出来。因此，**对于提供了静态工厂方法而不是构造器的类来说，要想查明如何实例化一个类，这是非常困难的。**(在查找一个类的静态工厂方法去实例化这个类时，在API文档中没有构造器那么方便)

  下面是静态工厂方法的一些惯用名称：

  - valueOf —— 实际上是类型转换方法
  - of —— valueOf的一种更为简洁的替代，在EnumSet中使用并流行起来
  - getInstance —— 返回的实例是通过方法的参数来描述的，但是不能够说与参数具有同样的值。对于Singleton来说，该方法没有参数，并返回唯一实例
  - newInstance —— 像getInstance一样，但newInstance能够确保返回的每个实例都与所有其他实例不同
  - getType —— 像getInstance一样，但是在工厂方法处于不同的类中的时候使用，Type表示工厂方法所返回的对象的类型
  - newType —— 像newInstance一样，但是在工厂方法处于不同的类中的时候使用，Type表示工厂方法所返回的对象类型

### 第2条：遇到多个构造器参数时要考虑用构建器(builder)

**静态工厂和构造器有个共同的局限性：它们都不能很好地扩展到大量的可选参数。**对于大量可选参数的类，程序员一般采用`重叠构造器(telescoping constructor) 模式`，在这个模式下，你提供第一个只有必要参数的构造器，第二个构造器有一个可选参数，第三个有两个可选参数，依此类推，最后一个构造器包含所有可选参数。如下为**HashMap的重叠构造器**举例：

```java
public HashMap() {
    this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
}

public HashMap(int initialCapacity) {
    this(initialCapacity, DEFAULT_LOAD_FACTOR);
}

public HashMap(int initialCapacity, float loadFactor) {...}
```

一句话：**重叠构造器模式可行，但是当有许多参数的时候，客户端代码会很难编写，并且仍然较难阅读。**如果读者想知道那些值是什么意思，必须很仔细地数着这些参数来探个究竟。一长串类型相同的参数会导致一些微妙的错误。如果客户端不小心颠倒了其中两个参数的顺序，编译器也不会出错，但是程序在运行时会出现错误的行为。

遇到许多构造器参数的时候，还有第二种代替办法，即**JavaBeans模式，在这种模式下，调用一个无参数构造器来创建对象，然后调用settter方法来设置每个必要的参数，以及每个相关的可选参数。**

遗憾的是，**JavaBeans模式自身有关很严重的缺点。因为构造过程被分到了几个调用中，在构造过程中，JavaBean可能处于不一致的状态**。类无法仅仅通过检验构造器参数的有效性来保证一致性。试图使用处于不一致状态的对象将会导致失败，这种失败与包含错误的代码大相径庭，因此它调试起来十分困难。与此相关的另一个不足在于，**JavaBeans模式阻止了把类做成不可变类的可能，这就需要程序员付出额外的努力来确保它的线程安全。**

```java
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    
    private int calories ;
    private int fat ;
    private int carbohydrate ;
    private int sodium ;
    
    public static class Builder {
        private final int servingSize;
        private final int servings;

        private int calories = 0;
        private int fat = 0;
        private int carbohydrate = 0;
        private int sodium = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings = servings;
        }

        public Builder calories(int val) {
            calories = val;
            return this;
        }

        public Builder fat(int val) {
            fat = val;
            return this;
        }

        public Builder carbohydrate(int val) {
            carbohydrate = val;
            return this;
        }

        public Builder sodium(int val) {
            sodium = val;
            return this;
        }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }

    public NutritionFacts(Builder builder) {
        this.servingSize = builder.servingSize;
        this.servings = builder.servings;
        this.calories = builder.calories;
        this.fat = builder.fat;
        this.sodium = builder.sodium;
        this.carbohydrate = builder.carbohydrate;
    }
}
```

注意，NutritionFacts是不可变的，所有的默认参数值都单独放在一个地方。**builder的settter方法返回builder本身，以便可以把调用链接起来。**下面就是客户端代码：

```java
NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
        .calories(100).sodium(35).carbohydrate(27).build();
```

这样的客户端代码很容易编写，更为重要的是，易于阅读。**builder模式模拟了具名的可选参数(英文是：The Builder pattern simulates named optional parameters as found in Ada and Python)**，就像Ada和Python中的一样。

**这里不懂，以后再加上**

---

Builder模式的确也有它自身的不足。**为了创建对象，必须先创建它的构建器。**虽然创建构建器的开销在实践中可能不那么明显，但是在某些十分注重性能的情况下，可能就成问题了。

Builder模式还比重叠构造器模式更加冗长，因此**它只在有很多参数的时候才使用，比如4个或者更多个参数。**但是记住，将来你可能需要添加参数。如果一开始就使用构造器或者静态工厂，等到类需要多个参数时才添加构建器，就会无法控制，那些过时的构造器或者静态工厂显得十分不协调。因此，通常最好一开始就使用构建器。

简而言之，**如果类的构造器或者静态工厂中具有多个参数，设计这种类时，Builder模式就是种不错的选择。**特别是当大多数参数都是可选的时候。**与使用传统的重叠构造器模式相比，使用Builder模式的客户端代码将更易于阅读和编写，构建器也比JavaBeans模式更加安全。**

### 第3条：用私有构造器或者枚举类型强化Singleton属性

Singleton指仅仅被实例化一次的类。Singleton通常被用来代表那些本质上唯一的系统组件，比如窗口管理器或者文件系统。**使类成为Singleton会使它的客户端测试变得十分困难，因为无法给Singleton替换模拟实现，除非它实现一个充当其类型的接口**

**在Java 1.5 发行版本之前，实现Singleton有两种方法。这两种方法都要把构造器保持为私有的，并导出公有的静态成员，以便允许客户端能够访问该类的唯一实例**。在第一种方法中，公有静态成员是个final域：

```Java
//Singleton with public final field
public class Elvis{
  public static final Elvis INSTANCE = new Elvis();
  private Elvis(){...}
  public void leaveTheBuilding(){...}
}
```

私有构造器仅被调用一次，用来实例化公有的静态final域Elvis.INSTANCE。由于缺少公有的或者受保护的构造器，所以保证了Elvis的全局唯一性：一旦Elvis类被实例化，只会存在一个Elvis实例，不多也不少。客户端的任何行为都不会改变这一点但要提醒一点：**享有特权的客户端可以借助AccessibleObject.setAccessible方法，通过反射机制调用私有构造器。如果需要抵御这种攻击，可以修改构造器，让它在被要求创建第二个实例的时候抛出异常。**

在实现Singleton的第二种方法中，公有的成员是个静态工厂方法：

```java
//Singleton with static factory
public class Elvis{
  private static final Elvis INSTANCE = new Elvis();
  private Elvis(){...}
  public static Elvis getInstance(){ return INSTANCE; }
  public void leaveTheBuilding(){...}
}
```

对于静态方法Elvis.getInstance()的所有调用，都会返回同一个对象引用，所以，永远不会创建其他的Elvis实例（上述提醒依然适用，**还是可以通过反射去访问私有构造器**）

**公有域方法的主要好处在于，组成类的成员的声明很清楚地表明了这个类是一个Singleton：公有的静态域是final的，所以该域将总是包含相同的对象引用。公有域方法在性能上不再有任何优势：现代的JVM实现几乎都能够将静态工厂方法的调用内联化。（可以推测，以前版本的Java虚拟机对实现这两个Singleton的性能上有一定的差异，而且可以看出是公有域的方式性能更好）**

**工厂方法的优势之一在于，它提供了灵活性：在不改变其API的前提下，我们可以改变该类是否应该为Singleton的想法。工厂方法返回该类的唯一实例，但是，它可以很容易被修改，比如改成为每个调用该方法的线程返回一个唯一的实例。第二个优势与泛型有关（见第27条）。这些优势之间通常都不相关，public 域的方法比较简单。**

**为了使利用这其中一种方法实现的Singleton类变成是可序列化的(Serializable) （见第11章），仅仅在声明中加上“implements Serializable” 是不够的。为了维护并保证Singleton，必须声明所有的实例都瞬时的（transient），并提供一个readResolve方法（见第77条）。否则，每次反序列化一个序列化的实例时，都会创建一个新的实例。**为了防止这种情况，要在Elvis类中加入下面这个readResolve方法

```java
//readResolve method to preserve singleton property
private Object readResolve(){
  //return the one true Elvis and let the grabage collector
  //take care of the Elvis impersonato≥r
  return INSTANCE;
}
```

**从Java 1.5 发行版本起，实现Singleton还有第三种方法。只要编写一个包含单个元素的枚举类型**：

```java
//Enum singleton - the preferred approach
public enum Elvis {
  INSTANCE;
  public void leaveTheBuilding(){...}
}
```

**这种方法在功能上与公有域相近，但是它更加简洁，无偿地提供了序列化机制，绝对防止多次实例化，即使是在面对复杂的序列化或者反射攻击的时候。虽然这种方法还没有广泛采用，但是单元素的枚举类型已经成为实现Singleton的最佳方法。**

### 第4条：通过私有樊笼器强化不可实例化的能力

有时候，**你可能需要编写只包含静态方法和静态域的类(工具类)**。这些类的名声很不好，转化为有些人在面向对象的语言中滥用这样的类来编写过程化的程序（**工具类使用不当容易产生过程化的代码**）。尽管如此，它们也确实有它们特有的用处

**工具类（utility class）不希望被实例化，实例对它没有任何意义**。然而在缺少显式构造器的情况下，编译器会自动提供一个公有的、无参的缺省构造器（default constructor），对于用户而言，这个构造器与其他的构造器没有任何区别。在已发行的API中常常可以看到一些被无意识地实例化的类。

**企图通过将类做成抽象类来强制该类不可被实例化，这是行不通的。**该类可以被子类化，并且该子类也可以被实例化，这样做甚至会误导用户，以为这种类是专门为了继承而设计的（见第17条）。

**由于只有当类不包含显式的构造器时，编译器才会生成缺省的构造器**，因此我们只要让这个类包含私有构造器，它就不能被实例化了：

```java
//Noninstantiable utility class
public class UtilityClass {
  //Suppress default constructor for noninstantiability
  private UtilityClass(){
    throw new AssertionError();
  }
  ... //remainder omitted
}
```

**由于显式的构造器是私有的，所以不可以在该类的外部访问它。AssertionError还是必需的，但是它可以避免不小心在类的内部调用构造器。它保证该类在任何情况下都不会被实例化。这种习惯用法有点违背直觉，好像构造器就是专门设计成不能被调用一样。因此，明智的做法就是在代码中增加一条注释，如上所示。**

这种习惯用法也有副作用，**它使得一个类不能被子类化。所有的构造器都必须显式或隐式地调用超类（superclass）构造器，在私有化后，子类就没有可访问的超类构造器可调用。**

### 第5条：避免创建不必要的对象

重用对象既快速，又流行。**如果对象是不可变的（immutable），它就始终可以被重用。**

代码：

```java
String s = new String("stringette"); //DONT`T DO THIS
```

会在每次调用的时候创建一个新的不必要的实例。改进后为：

```java
String s = "stringette";
```

这个版本只用了一个String 实例，而不是每次执行的时候都创建一个新的实例。而且，它可以保证，对于所有在同一台虚拟机中运行的代码，只要它们包含相同的字符串字面常量，该对象就会被重用。

```java
0: ldc           #2                  // String ab “ab”是常量池中的一个字符串，String s = "ab"是采用这种创建方式，返回一个引用，所以只要都是“ab”字符串，那么都指向同一个对象
2: astore_1

22: new           #5                  // class java/lang/String 开辟一块空间放一个String，String s = new String("ab")是这样创建的引用，每次都是一个新的引用
25: dup
26: ldc           #2                  // String ab
28: invokespecial #6                  // Method java/lang/String."<init>":(Ljava/lang/String;)V
31: astore_3
```

对于同时提供了静态工厂方法和构造器的不可变类，通常可以使用静态工厂而不是构造器。例如静态工厂方法Boolean.valueOf(String) 几乎总是优先于构造器Boolean(String)

在代码中，可以初始化的时候创建那些已知不会被修改的可变对象。如Calendar、TimeZone和Date实例等。

可以通过**延迟加载技术，即把对这些域的初始化延迟到具体方法被第一次调用的时候进行，则有可能消除一些不必要的工作**，但是不建议这样做。正如延迟初始化中常见的情况一样，这些做会使方法的实现更加复杂，从而无法将性能显著提高到超过已经达到的水平（见第55条）。

考虑适配器（adapter）的情形，有时也叫做视图（view）。适配器是指这样一个对象：**它把功能委托给一个后备对象（backing object），从而为后备对象提供一个可以替代的接口**。

对于给定的map对象，每次调用keySet方法都返回同样的Set实例。虽然创建多个keySet视图对象的实例并无害处，却也没必要。

自动装箱使得基本类型和装箱基本类型之间的差别变得模糊起来，但是在语义上还有微妙的差别，在性能上也有着比较明显的差别。**要优先使用基本类型而不是装箱基本类型，要当心无意识的自动装箱。**

由于小对象的构造器只做很少量的显式工作，所以小对象的创建和回收是非常廉价的。**通过创建附加的对象，提升程序的清晰性、简洁性和功能性，这通常是好事。**

通过维护自己的对象池（object pool）来避免创建对象并不是一种好的做法，除非池中的对象是非常重量级的。**真正正确使用对象池的典型对象示例就是数据库连接池。建立数据库连接的代价是非常昂贵的。因此重用这些对象非常有意义。**一般而言，维护自己的对象池必定会把代码弄得很乱，同时增加内存占用（footprint），并且还会损害性能。

在提倡使用保护性拷贝的时候，因重用对象而付出的代价要远远大于因创建重复对象而付出的代价。必要时如果没能实施保护性拷贝，将会导致潜在的错误和安全漏洞；而不必要地创建对象则只会影响程序的风格和性能。

### 第6条：消除过期的对象引用

举例为一个栈，利用Object数组—— elements保存对象，Stack的pop方法如下：

```java
public Object pop(){
  if (size == 0){
    throw new EmptyStackException();
  }
  return elements[--size];
}
```

这个程序中有一个问题。不严格的讲，这个程序有一个“内存泄漏”，随着垃圾回收活动的增加，或者由于内存占用的不断增加，程序性能的降低会逐渐表现出来。在极端的情况下，这种内存泄漏会导致磁盘交换（Disk Paging），甚至导致程序失败（OutOfMemoryError错误），但是这种失败情形相对比较少见。

程序中哪里发生了内存泄漏呢？**如果一个栈先是增长，然后再收缩，那么，从栈中弹出来的对象将不会被当作垃圾回收，即使使用栈的程序不再引用这些对象，它们也不会被回收。这是因为，栈内部维护着对这些对象的过期引用（obsolete reference），所谓的过期引用，是指永远也不会再被解除的引用。在本例中，凡是在elements数组的“活动部分（active portion）”之外的任何引用都是过期的。活动部分是指elements中下标小于size的那些元素。**

在支持垃圾回收的语言中，内存泄漏是很隐蔽的（称为`无意识的对象保持（unintentional object retention）`更为恰当）。**如果一个对象引用被无意识地保留起来了，垃圾回收机制不仅不会处理这个对象，而且也不会处理被 这个对象所引用的所有其他对象**。即使只有少量的几个对象引用被无意识地保留下来，也会有许许多多的对象被排除在垃圾回收机制之外，从而对性能造成潜在的重大影响。

这类问题的修复方法很简单：**一旦对象引用已经过期，只需清空这些引用即可。**对于上述例子中的Stack类而言，只要一个单元被弹出栈，指向它的引用就过期了。pop方法的修订版本如下：

```java
public Object pop(){
  if (size == 0){
    throw new EmptyStackException();
  }
  Object result = elements[--size];
  elements[size] = null; // eliminate obsolete reference
  return result;
}
```

**清空过期引用的另一个好处是，如果它们以后又被错误地解除引用，程序就会立即抛出NullPointerException异常，而不是悄悄地错误运行下去。**

**清空对象引用应该是一种例外，而不是一种规范行为**，消除过期引用最好的方法是让包含该引用的变量结束其生命周期。**如果你是在最紧凑的作用域范围内定义每一个变量**（见第45条），这种情形就会自然而然地发生。

那么，何时应该清空引用呢？Stack类的哪方面特性使它易于受到内存泄漏的影响呢？简而言之，问题在于，**Stack类自己管理内存（manage its own memory）。存储池（storage pool）包含了elements数组（对象引用单元，而不是对象本身）的元素。**数组活动区域中的元素是已分配（allocated）的，而数组其余部分的元素则是自由的。但是垃圾回收器并不知道这一点；对于垃圾回收器而言，elements数组中的所有对象引用都同等有效。只有程序员知道数组的非活动部分是不重要的。程序员可以把这个情况告知垃圾回收器，做法很简单：**一旦数组元素变成了非活动部分的一部分，程序员就手工清空这些数组元素。**

一般而言，**只要类是自己管理内存，程序员就应该警惕内存泄漏问题。**一旦元素被释放掉，则该元素中包含的任何对象引用都应该被清空。

**内存泄漏的另一个常见来源是缓存。**一旦你把对象引用放到缓存中，它就很容易被遗忘掉。从而使得它不再有用之后很长一段时间内仍然留在缓存中。对于这个问题，有几种可能的解决方案。

- 如果你正好要实现这样的缓存：**只要在缓存之外存在对菶项的键的引用，该项就有意义，那么就可以用WeakHashMap代表缓存；当缓存中的项过期之后，它们就会自动被删除。**记住，只有当所要的缓存项的生命周期是由该键的外部引用而不是由值决定时，WeakHashMap才有用处。

- 更为常见的情形则是：“缓存项的生命周期是否有意义”并不是很好确定，随着时间的推移，其中的项会变得越来越没有价值。在这种情况下，**缓存应该时不时地清除掉没有用的项。这项清除工作可以由一个后台线程（可能是Timer或者ScheduledThreadPoolExecutor）来完成，或者也可以在给缓存添加新条目的时候顺便进行清理。LinkedHashMap类利用它的removeEldestEntry方法可以很容易地实现后一种方案。**对象更加复杂的缓存，必须直接使用java.lang.ref。

  ```java
  protected boolean removeEldestEntry(Map.Entry<K,V> eldest) {
  	return false;
  }
  ```

  removeEldestEntry方法在afterNodeInsertion方法之后调用：

  ```java
  void afterNodeInsertion(boolean evict) { // possibly remove eldest
      LinkedHashMap.Entry<K,V> first;
      if (evict && (first = head) != null && removeEldestEntry(first)) {
          K key = first.key;
          removeNode(hash(key), key, null, false, true);
      }
  }
  ```

  在父类HashMap里面有afterNodeInsertion方法的调用，在compute、merge方法等等，所以估计要用3个参数的构造器构造的LinkedHashMap—— LinkedHashMap(int,float,boolean) constructor：

  ```java

   * <p>A special {@link #LinkedHashMap(int,float,boolean) constructor} is
   * provided to create a linked hash map whose order of iteration is the order
   * in which its entries were last accessed, from least-recently accessed to
   * most-recently (<i>access-order</i>).  This kind of map is well-suited to
   * building LRU caches.  Invoking the {@code put}, {@code putIfAbsent},
   * {@code get}, {@code getOrDefault}, {@code compute}, {@code computeIfAbsent},
   * {@code computeIfPresent}, or {@code merge} methods results
   * in an access to the corresponding entry (assuming it exists after the
   * invocation completes). The {@code replace} methods only result in an access
   * of the entry if the value is replaced.  The {@code putAll} method generates one
   * entry access for each mapping in the specified map, in the order that
   * key-value mappings are provided by the specified map's entry set iterator.
   * <i>No other methods generate entry accesses.</i>  In particular, operations
   * on collection-views do <i>not</i> affect the order of iteration of the
   * backing map.
   *
  ```

  java 1.6 的API上这样写：

  ```
  可以重写 removeEldestEntry(Map.Entry) 方法来实施策略，以便在将新映射关系添加到映射时自动移除旧的映射关系。
  ```

- **内存泄漏的第三个常见来源是监听器和其他回调。**如果你实现了一个API客户端在这个API中注册回调，却没有显式地取消注册，那么除非你采取某些动作，否则它们就会积聚。**确保回调立即被当作垃圾回收的最佳方法是只保存它们的弱引用（weak reference），例如，只将它们保存成WeakHashMap中的键。**

  由于内存泄漏通常不会表现成明显的失败，所以它们可以在一个系统中存在很多年。往往只有通过仔细检查代码，或者借助于Heap剖析工具（Heap Profiler）才能发现内存泄漏问题。因此，如果能够在内存泄漏发生之前就知道如何预测此类问题，并阻止它们发生，那是最好不过的了。

### 第7条：避免使用终结方法

**终结方法(finalizer)通常是不可预测的，也是很危险的，一般情况下不必要的。**使用终结方法会导致行为不稳定、降低性能，以及可移植性问题。当然，终结方法也有其可用之处，我们将在本条目的最后再做介绍。

**终结方法的缺点在于不能保证会被及时地执行。**

及时地执行终结方法正是垃圾回收算法的一个主要功能，这种算法在不同的JVM实现中会大相径庭。如果程序依赖于终结方法被执行的时间点，那么这个程序的行为在不同的JVM中运行的表现可能就会截然不同。

在很少见的情况下，为类提供终结方法，可能会随意地延迟其实例的回收过程。

Java语言规范不仅不保证终结方法会被及时地执行，而且根本就不保证它们会被执行。**结论是：不应该依赖终结方法来更新重要的持久状态。**例如，依赖终结方法来释放共享资源（比如数据库）上的永久锁，很容易让整个分布式系统垮掉。

**不要被System.gc和System.runFinalization这两个方法所诱惑，它们确实增加了终结方法被执行的机会，但是它们并不保证终结方法一定侍寝执行。唯一声称保证终结方法被执行的方法是System.runFinalizersOnExit以及它臭名昭著的孪生兄弟Runtime.runFinalizersOnExit。这两个方法都有致使的缺陷，已经被废弃了。**

正常情况下，未被捕获的异常将会使线程终止，并打印栈轨迹（Stack Trace），但是，**如果异常发生在终结方法之中，则不会如此，甚至连警告都不会打印出来。**

**使用终结方法有一个非常严重的（severe）性能损失。**

那么，如果类的对象中封装的资源（例如文件或者线程）确实需要终止，应该怎么做才能不用编写终结方法呢？**只需提供一个显式的终止方法，并要求该类的客户端在每个实例不再有用的时候调用这个方法。**值得一提的一个细节是，该实例必须记录下自己是否已经被终止了：**显式的终止方法必须在一个私有域中记录下“该对象已经不再有效”。如果这些方法是在对象已经终止之后被调用，其他的方法就必须检查这个域，并抛出IllegalStateException异常。**

典型例子是InputStream、OutputStream和java.sql.Connection上的close方法。

**显式的终止方法通常与try-finally结构结合起来使用，以确保及时终止。**

那么终结方法有什么好处呢？它们有两个合法用途。**第一种用途是，当对象的所有者忘记调用前面段落中建议的显式终止方法时，终结方法可以充当“安全网（safety net）”。**

终结方法的第二种合理用途与对象的**本地对待体（native peer）有关。**

总之，**除非是作为安全网，或者是为了终止非关键的本地资源，否则请不要使用终结方法。在这些很少见的情况下，既然使用了终结方法，就要记住调用super.finalize。如果用终结方法作为安全网，要记录终结方法的非法用法。最后，如果需要把终结方法与公有的非final类关联起来，请考虑使用终结方法守卫者，以确保即使子类的终结方法未能调用super.finalize，该终结方法也会被执行。**

## 第3章 对于所有对象都通用的方法##

尽管Object是一个具体类，但是设计它主要是为了扩展。它所有的非final方法(equals, hashcode, toString, clone 和finalize)都有明确的**通用约定（general contract）**。任何一个类，它在覆盖这些方法的时候，都有责任遵守这些通用约定；如果不能做到这一点。其他依赖于这些约定的类（例如HashMap和HashSet），就无法结合该类一起正常运作。

#### Object的方法hashCode通用约定：

 1.7 英文的API

The general contract of `hashCode` is:

- Whenever it is invoked on the same object more than once during an execution of a Java application, the `hashCode` method must consistently return the same integer, provided no information used in `equals` comparisons on the object is modified. This integer need not remain consistent from one execution of an application to another execution of the same application.
- If two objects are equal according to the `equals(Object)` method, then calling the `hashCode` method on each of the two objects must produce the same integer result.
- It is *not* required that if two objects are unequal according to the `equals(java.lang.Object)` method, then calling the `hashCode` method on each of the two objects must produce distinct integer results. However, the programmer should be aware that producing distinct integer results for unequal objects may improve the performance of hash tables.

1.6 翻译后的API

- 在 Java 应用程序执行期间，在对同一对象多次调用 `hashCode` 方法时，必须一致地返回相同的整数，前提是将对象进行 `equals` 比较时所用的信息没有被修改。从某一应用程序的一次执行到同一应用程序的另一次执行，该整数无需保持一致。
- 如果根据 `equals(Object)` 方法，两个对象是相等的，那么对这两个对象中的每个对象调用 `hashCode` 方法都必须生成相同的整数结果。
- 如果根据 [`equals(java.lang.Object)`](../../java/lang/Object.html#equals(java.lang.Object)) 方法，两个对象不相等，那么对这两个对象中的任一对象上调用 `hashCode` 方法*不* 要求一定生成不同的整数结果。但是，程序员应该意识到，为不相等的对象生成不同整数结果可以提高哈希表的性能

**其它的方法并没有直接列举通用约定，而是写在方法解释里，如做一些建议等**

### 第8条：覆盖equals时请遵守通用约定

覆盖equals方法看似很简单，但是有许多覆盖方式会导致错误，并且后果非常严重，最容易避免这类问题的办法就是不覆盖equals方法。在这种情况下，类的每个实例都只与它自身相等。

**什么时候应该覆盖Object.equals方法呢？**如果类具有自己特有的“逻辑相等”概念（不同于对象等同的概念），而且超类还没有覆盖equals以实现期望的行为。这时我们就需要覆盖equals方法。**这通常属于“值类（value object）”的情形**。

**值类仅仅是一个表示值的类，例如Integer或者Date。程序员在利用equals方法来比较值对象的引用时，希望知道它们在逻辑上是否相等，而不是想了解它们是否指向同一个对象。为了满足程序员的要求，不仅必须覆盖equals方法，而且这样做也使得这个类的实例可以被用作映射表（map）的键（key），或者集合（set）的元素，使映射或者集合表现出预期的行为。**

有一种“值类”不需要覆盖equals方法，即用实例受控确保“每个值至多只存在一个对象”的类。枚举类型（见第30条）就属于这种类。对于这样的类而言，逻辑相同与对象相同是一回事，因此Object的equals方法等同于逻辑意义上的equals方法。

equals的通用约定：

`equals` 方法在非空对象引用上实现相等关系：

- *自反性*：对于任何非空引用值 `x`，`x.equals(x)` 都应返回 `true`。
- *对称性*：对于任何非空引用值 `x` 和 `y`，当且仅当 `y.equals(x)` 返回 `true` 时，`x.equals(y)` 才应返回 `true`。
- *传递性*：对于任何非空引用值 `x`、`y` 和 `z`，如果 `x.equals(y)` 返回 `true`，并且 `y.equals(z)` 返回 `true`，那么 `x.equals(z)` 应返回 `true`。
- *一致性*：对于任何非空引用值 `x` 和 `y`，多次调用 `x.equals(y)` 始终返回 `true` 或始终返回 `false`，前提是对象上 `equals` 比较中所用的信息没有被修改。
- 对于任何非空引用值 `x`，`x.equals(null)` 都应返回 `false`。

**结合所有这些要求，得出了以下实现高质量equals方法的诀窍：**

1. **使用== 操作符检查“参数是否为这个对象的引用”。**如果是则返回true。这只不过是一种性能优化，如果比较操作有可能很昂贵，这值得这么做。
2. **使用instanceof 操作符检查“参数是否为正确的类型”**。如果不是，返回false。
3. **把参数转换成正确的类型**。因为转换之前进行过instanceof 测试，所以确保会成功。
4. **对于该类中的每个“关键（significant）”域，检查参数中的域是否与该对象中对应的域相匹配**。如果这些测试都成功，返回true，否则返回false。

对于既不是float也不是double类型的基本类型域，可以使用== 操作符进行比较；对于对象引用域，可以递归地调用equals方法；对于float域，可以使用Float.compare方法；对于double域，则使用Double.compare方法。**对于float和double域进行特殊的处理是有必要的，因为存在着Float.NaN、-0.0f 以及类似的double常量；详细参考Float.equals文档**。对于数组域，则要把以上这些指导原则应用到每个元素上。如果数组域中的每个元素都很重要，就可以使用发行版本1.5 中新增的其中一个Arrays.equals方法。

有些对象引用域包含null值可能是合法的，为了防止NullPointerException，可以：

```java
(field == null ? o.field == null : field.equals(o.field))
```

如果field和o.field 通常是相同的对象引用，那么下面的做法会更快一点：

```java
(field == o.field || (field != null && field.equals(o.field))
```

5. **当你编写完成了equals方法之后，应该问自己三个问题：它是否是对称的、传递的、一致的？**当然，equals方法也必须满足其他两个特性，自反性和非空性，但是这两种特性通常会自动满足。

**最后的一些告诫：**

- 覆盖equals时总要覆盖hashCode（见第9条）

- 不要企图让equals方法过于智能

- 不要将equals声明中的Object对象替换为其他的类型。

  ```
  public boolean equals(MyClass o){
  }
  ```

  问题在于，这个方法并没有覆盖Object.equals方法，而是重载。可以用@Override注解来防止这种错误。

### 第9条：覆盖equals时总要覆盖hashCode

**在每个覆盖了equals方法的类中，也必须覆盖hashCode方法。**如果不这样做，会违反Object.hashCode的通用约定。从而导致该类无法结合所有基于散列的集合一起正常运作。如HashMap，HashSet，HashTable等。

例如一个类PhoneNumber，未覆盖hashCode方法，放到Map中的代码为：

```java
Map<PhoneNumber, String> m = new HashMap<PhoneNumber, String>();
m.put(new PhoneNumber(707,867,5309), "Jenny");
```

这时，你可以希望m.get(new PhoneNumber(707,867,5309))会返回“Jenny”，但是实际上返回的是null。因为两个键并不是同一个PhoneNumber的实例。

**由于PhoneNumber类没有覆盖hashCode方法，从而导致两个相等的实例具有不相等的散列码，违反了hashCode的约定。**

修正这个问题非常简单，提供一个适当的hashCode方法就好了。编写一个合法但并不好用的hashCode方法没有任何价值。如下这个方法总是合法的，但是永远都不要正式使用。

```java
public int hashCode(){ return 42;} //never use
```

上面这个是合法的，因为它确保了相等的对象问题具有同样的散列码。但是它也极为恶劣，因为它使得每个对象都具有同样的散列码。因此，每个对象都被映射到同一个散列桶中，使散列表退化为链表（linked list）。它使得本该线性时间运行的程序变成了以平方级时间在运行。对于规模很大的散列表来说，这会关系到散列表能否正常工作。

一个好的散列函数通常倾向于“为不相等的对象产生不相等的散列码”。这正是hashCode约定中第三条的含义。理想情况下，**散列函数应该把集合中不相等的实例均匀地分布到所有可能的散列值上。**简单的解决办法：

1. 把某个非零的常数值，比如说17，保存在一个名为result的int类型的变量中。

2. 对于对象中每个关键域f（指equals方法中涉及的每个域），完成以下步骤：

   - 为该域计算int类型的散列码c：

     - boolean 型的，计算（f ? 1 : 0）
     - byte,char,short, int 类型的，计算 int(f)
     - long 类型的，计算(int)(f ^ (f >>> 32))
     - float 类型的，计算Float.floatToIntBits(f)
     - double类型的，计算Double.doubleToLongBits(f)，然后为得到的long类型值再利用上面的long 类型的方法计算值
     - 对象引用，
     - 数组

   - 按照下面的公式，把上一步得到的c 合并到result中

     ```java
     result = 31 * result + c;
     ```

3. 返回result

4. 写完hashCode方法之后，问问自己，“相等的实例是否都具有相等的散列码”。

**之所以选择31，是因为它是一个奇素数。如果乘数是偶数，并且乘法溢出的话，信息就会丢失，因为与2相乘等价于移位运算，使用素数的好处并不很明显，但是习惯上都使用素数来计算散列结果。31有个很好的特性，即用移位和减法来代替乘法，可以得到更好的性能：31 * i = (i << 5 ) - 1。现代的JVM可以自动完成这种优化。**

### 第10条：始终要覆盖toString

Object类提供的toString 方法包含**类的名称，以及一个@符号，接着是散列码的无符号16进制表示法。**

**toString的通用约定指出，被返回的字符串应该是一个“简洁的，但信息丰富，并且易于阅读的表达形式”，进一步指出，“建议所有的子类都覆盖这个方法”**。

**当对象被传递给println、printf、字符串联操作符(+)以及assert或者被调试器打印出来时，toString方法会被自动调用。**如果没有覆盖toString方法，产生的消息将难以理解。

提供好的toString方法，不仅有益于这个类的实例，同样也有益于那些包含这些实例的引用的对象，特别是集合对象。打印Map时，“Jenny=PhoneNumber@163b91”和“Jenny=(408)867-5309”，你更愿意看到哪个？

**在实际应用中，toString方法应该返回对象包含的所有值得关注的信息。如果对象太大，或者对象中包含的状态信息难以用字符串来表达，这样做就有点不切实际。在这种情况下，toString应该返回一个摘要信息**，例如“Manhattan white page(1487536 listings)” 或者“Thread[main, 5, main]”。理想情况下，字符串应该是自描述的（self-explanatory）。

在实现toString的时候，必须要做一个很重要的决定：**是否在文档中指定返回值的格式**。对于值类（value class)，比如电话号码等，建议这么做。

**指定格式的好处是，它可以被用作一种标准的、明确的、适合人阅读的对象表示法。这种表示法可以用于输入和输出，以及用在永久的适合于人类阅读的数据对象中，例如XML文档。如果你指定了格式，最好再提供一个相匹配的静态工厂或者构造器，以便程序员可以很容易地在对象和它的字符串表示法之间来回转换。Java平台类库中的许多值类都采用了这种做法，包括BigInteger、BigDecimal和绝大多数的基本类型包装类（boxed primitive class）。**

**指定格式的不足之处：如果这个类已经被广泛使用，一旦指定格式，就必须始终如一地坚持这种格式。**程序员将会编写出相应的代码来解析这种字符串表示法，产生字符串表示法，以及把字符串嵌入到持久的数据中。如果将来的发行版本中改变了这种表示法，就会破坏他们的代码和数据。

**无论你是否决定指定格式，都应该在文档中明确表明你的意图。**

- 指定格式的javadoc注释可以如下：

  ```java
  /**
   *  return the string representation of this phone number.
   *  the string consists of fourteen characters whose format is "(XXX) YYY-ZZZZ"
   *  ...
   */
  ```

- 不指定格式，javadoc注释也应该有如下所示信息：

  ```java
  /**
   *  returns a brief description of this potion. The exact details
   *  of the representation are unspecified and subject to change, 
   *  but the following may be regarded as typical:
   *  
   *  "[Potion #9: type=love, smell=turpentine]"
   */
  ```

无论是否指定格式，**都为toString返回值中包含的所有信息，提供一种编程式的访问途径。**例如，PhoneNumber类应该包含针对area code、prefix和line number的访问方法。**如果不这么做，就会迫使那些需要这些信息的程序员不得不自己去解析这些字符串。除了降低了程序的性能，使得程序员们去做这些不必要的工作之外，这个解析过程也很容易出错，会导致系统不稳定，如果格式发生变化，不会导致系统崩溃。**

### 第11条：谨慎地覆盖clone

以后再看

### 第12条：考虑实现Comparable接口

类实现了Comparable接口，就表明它的实例具有内在的排序关系（natural ordering）。为实现Comparable接口的对象数组进行排序就这么简单：

```java
Arrays.sort(a);
```

一旦类实现了Comparable 接口，它就可以跟许多泛型算法（generic algorithm）以及依赖于该接口的集合实现（collection implementation）进行协作。你付出很小的努力就可以获得非常强大的功能。事实上，Java平台类库中的所有值类（value class）都实现了Comparable接口。**如果你正在编写一个值类，它具有非常明显的内在排序关系，比如按字母顺序、按数值顺序或者按年代顺序，那你就应该坚决考虑实现这个接口。**

```java
public interface Comparable<T> {
  public int compareTo(T o);
}
```

就好像违反了hashCode约定的类会破坏其他依赖于散列做法的类一样，**违反compartTo约定的类会破坏其他依赖于比较关系的类。依赖于比较关系的类包括有序集合类TreeSet、TreeMap，以及工具类Collections和Arrays，它们内部包含有搜索和排序算法。**

**因为Comparable接口是参数化的，而且comparable方法是静态的类型，因此不必进行类型检查，也不必对它的参数进行类型转换。如果参数的类型不合适，这个调用甚至无法编译。如果参数为null，这个调用应该抛出NullPointerException异常，并且一旦该方法试图访问它的成员时就应该抛出。**

**如果一个域并没有实现Comparable接口，或者你需要使用一个非标准的排序关系，就可以使用一个显式的Comparator来代替。或者编写自己的Comparator，或者使用已有的Comparator。**

## 第4章 类和接口##

### 第13条：使类和成员的可访问性最小化

要区别设计良好的模块与设计不好的模式，最重要的因素在于，**这个模式对象外部的其他模式而言，是否隐藏其内部数据和其他实现细节。**模式之间只通过它们的API进行通信，一个模式不需要知道其他模式的内部工作情况。这个概念被称为**信息隐藏（information hiding）或者封装（encapsulation）。**

信息隐藏重要的许多原因：

- **它可以有效地解除组成系统的各个模式之间的耦合关系，使得这些模式可以独立地开发、测试、优化、使用、理解和修改。**
- **可以加快系统的开发速度，因为这些模式可以并行开发。**
- **它也减轻了维护的负担，因为程序员可以更快地理解这些模式，并且在高度它们的时候可以不影响其他的模式。**
- **虽然信息隐藏本身不会带来更好的性能，但是它可以有效地调节性能：一旦完成一个系统，并通过剖析确定了哪些模式影响了系统的性能（见第55条），那些模式就可以进一步优化，而不会影响其他模式的正确性。**
- **信息隐藏提高了软件的可重用性，因为模式之间并不紧密相连，除了开发这些模式所使用的环境之外，它们在其他的环境中往往也很有用。**
- **最后，信息隐藏也降低了构建大型系统的风险，因为即使整个系统不可用，但是这些独立的模式却有可能是可用的。**

第一规则很简单：**尽可能地使每个类或者成员不被外界访问。**换句话说，应该使用与你正在编写的软件的对应功能相一致的、尽可能最小的访问级别。

**对于顶层的（非嵌套的）类和接口，只有两种可能的访问级别：包级私有的（package private）和公有的（public）。**

**通过把类或者接口做成包级私有的，它实际上成了这个包的实现的一部分，而不是该包导出的API的一部分，在以后的发行版本中，可以对它进行修改、替换，或者删除而无需担心会影响到现有的客户端程序**。如果你把它做成公有的，你就有责任永远支持它，以保持兼容性。

**如果一个包级私有的顶层类（或者接口）只是在某一个类的内部被用到，就应该考虑使它成为唯一使用它的那个类的私有嵌套类（见第22条）。**

**降低不必要的公有类的可访问性，比降低包级私有的顶层类更重要的多**：因为公有类是包的API的一部分，而包级私有的顶层类则已经是这个包的实现的一部分。

对于成员（域、方法、嵌套类和嵌套接口）有四种可能的访问级别：

- private
- package-private
- protected
- public

 对于公有类的成员，当访问级别从包级私有变成保护级别时，会大大增加可访问性。受保护的成员是类的导出API的一部分，必须永远得到支持。导出的类的受保护成员也代表了该类对于某个实现细节的公开承诺。

有一条规则限制了降低方法的可访问性的能力。**如果方法覆盖了超类中的一个方法，子类中的访问级别就不允许低于超类中的访问级别。**这样就可以确保任何可使用超类的实例的地方，也都可以使用子类的实例。**特殊情形：如果一个类实现了一个接口，那么接口中所有的类方法在这个类中也都必须被声明为公有的，是因为接口的方法隐含着public。**

**实例域决不能是公有的。包含公有可变域的类并不是线程安全的。**

同样的建议也适用于静态域，一种例外，可以用公有的静态final域来暴露常量。按惯例，这种域的名称由大写字母组成，单词之间用下划线隔开。

注意，长度非零的数组总是可变的，所以，**具有公有的静态final数组域，或者返回这种域的访问方法，几乎总是错误的。**如果类具有这样的域或者访问方法，客户端将能够修改数组中的内容，这是安全漏洞的一个常见根源：

```java
//Potential security hole!
public static final Thing[] VALUES = {...};
```

修正这个问题有两种方法，

1. 可以使公有数组变成私有的，并增加一个公有的不可变列表：

   ```java
   private static final Thing[] PRIVATE_VALUES = {...};
   public static final List<Thing> VALUES = Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES));
   ```

2. 可以使数组变成私有的，并添加一个公有方法，返回私有数组的一个备份：

   ```java
   public static final Thing[] PRIVATE_VALUES = {...};
   public static final Thing[] values(){
     return PRIVATE_VALUES.clone();
   }
   ```

### 第14条：在公有类中使用访问方法而非公有域

坚持面向对象的程序员认为，应该用getter 和setter 来取代类中的公有域(public field)。

毫无疑问，说到**公有类**的时候，以上说法是正确的。**如果类可以在它所在的包的外部进行访问，就提供访问方法，以保留将来改变该类的内部表示法的灵活性。**如果公有类暴露了它的数据域，要想在将来改变其内部表示法是不可能的，因为公有类的客户端代码已经遍布各处了。

然而，**如果类是包级私有的，或者是私有的嵌套类，直接暴露它的数据域并没有本质的错误 — 假设这些数据域确实描述了该类所提供的抽象。**虽然客户端代码与该类的内部表示法紧密相连，但是这些代码被限定在包含该类的包中。如有必要，不改变包之外的任何代码而只改变内部数据表示法也是可以的。

**让公有类直接暴露域虽然从来都还是好办法，但是如果域是不可变的，这种做法的危害就小一些。**

### 第15条：使可变性最小化

**不可变类只是其实例不能被修改的类。每个实例中包含的所有信息都必须在创建该实例的时候就提供，并在对象的整个生命周期（lifetime）内固定不变。**

为了使类成为不可变，遵循下面五条规则：

1. **不要提供任何会修改对象状态的方法**（也称为mutator，其实就类似于setter）。
2. **保证类不会被扩展。**防止子类破坏该类的不可变行为。两个方法：
   - 让类成为final 的
   - 私有化构造器，并利用静态工厂来创建实例
3. **使所有域都是final 的。**
4. **使所有域都成为私有的。**防止客户端直接修改域引用的可变对象。
5. **确保对于任何可变组件的互斥访问。**

举例为一个复数表示类，提供了针对实部和虚部的访问方法，以及4种基本算术运算：加减乘除。注意这些算术运算是**创建并返回新的Complex 类实例，而不是修改这个实例。**如：

```java
public Complex add(Complex c){
  return new Complex(re + c.re, im + c.im);
}
```

大多数不可变类都使用了这个模式。它被称为**函数的做法**，因为这些方法返回了一个函数的结果，这些函数对操作数进行运算但并不修改它。

与之相对应的更常见的是**过程的（procedural）或者命令式的（imperative）**做法，使用这些方式时，将一个过程作用在它们的操作数上，会导致它的状态发生改变。

**不可变对象本质上是线程安全的，它们不要求同步。**当多个线程并发访问这样的对象时，它们不会遭到破坏。这无疑是获得线程安全最容易的办法。所以，**不可变对象可以被自由地共享。**不可变类应该充分利用这种优势，鼓励客户端尽可能地重用现有的实例。要做到这一点，一个很简单的办法是，对于频繁用到的值，为它们提供公有的静态final 常量。例如，Complex 类有可能有如下常量：

```java
public static final Complex ZERO = new Complex(0,0);
public static final Complex ONE = new Complex(1,0);
public static final Complex I = new Complex(0,1);
```

这种方法可以被进一步扩展。不可变类可以提供一些静态工厂，它们把频繁被请求的实例缓存起来，从而当现有实例符合请求的时候，就不必创建新的实例。**所有基本类型的包装类和BigInteger都有这样的静态工厂。使用这样的工厂也使得客户端之间可以共享现有的实例，而不用创建新的实例，从而降低了内存战胜和垃圾回收的成本。**

**在设计新的类时，选择用静态工厂代替公有的构造器，可以让你以后有添加缓存的灵活性，而不必影响客户端。**

**不可变类的真正唯一缺点是，对于每个不同的值都需要一个单独的对象。创建这种对象的代价可能很高，特别是对于大型对象的情形。**例如，假设你有一个上百万位的BigInteger，想要改变它的低位：

```java
BigInteger moby = ...
moby = moby.flipBit(0);
```

**如果你执行一个多步骤的操作，并且每个步骤都会产生一个新的对象，除了最后的结果之外其他的对象最终都会被丢弃，此时性能问题就会显露出来。**处理这种问题有两种办法：

1. 如果能够精确地预测出客户端将要在不可变的类上执行哪些复杂的多阶段操作，包级私有的可变配套类的方法就可以工作得很好。
2. **如果无法预测，最好的办法是提供一个公有的可变配套类。**在Java平台类库中，这种方法的主要例子是String 类，它的可变配套类是StringBuilder（和基本上已经废弃的StringBuffer）。

**让不可变类变成final 的另一种办法是，让类的所有构造器都变成私有的或者包级私有的，并添加公有的静态工厂来代替公有的构造器（见第1条）。**

**如果你选择让自己的不可变类实现Serializable接口，并且它包含一个或者多个指向可变对象的域，就必须提供一个显式的readObject 或者readResolve 方法，或者使用ObjectOutputStream.writeUnshared 和ObjectInputStream.readUnshared 方法，即使默认的序列化形式是可以接受的，也是如此。**

**坚决不要为每个get 方法编写一个相应的set 方法。除非有很好的理由要让类成为可变的类，否则就应该是不可变的。**
对于有些类而言，其不可变性是不切实际的。**如果类不能被做成是不可变的，仍然应该尽可能地限制它的可变性。降低对象可以存在的状态数，可以更容易地分析该对象的行为，同时降低出错的可能性。因此，除非有令人信服的理由要使域变成是非final 的，否则要使每个域都是final 的**

**构造器应该创建完全初始化的对象，并建立起所有的约束关系。不要在构造器或者静态工厂之外再提供公有的初始化方法，除非有足够理由，同样，也不应该提供“重新初始化”方法**

可以通过TimerTask 类来说明这些原则。它是可变的，但是它的状态空间被有意地设计得非常小。你可以创建一个实例，对它进行调度使它执行起来，也可以随意地取消它。一旦一个定时器任务(timer task) 已经完成，或者已经被取消，就不可能对它重新调度。

### 第16条：复合优先于继承

继承(inheritance) 是实现代码重用的有力手段，但它并非永远是完成这项工作的最佳工具。使用不当会导致软件变得很脆弱。**在包内部使用继承是非常安全的，在那里，子类和超类的实现都处在同一个程序员的控制之下。对于专门为了继承而设计、并且具有很好的文档说明的类来说（见第17条），使用继承也是非常安全的。**

**然而，对普通的具体类（concrete class）进行跨越包边界的继承，则是非常危险的。**

**与方法调用不同的是，继承打破了封装性。换句话说，子类依赖于其超类中特定功能的实现细节。**超类的实现有可能随着发行版本的不同而有所变化，如果真的发生了变化，子类可能会遭到破坏，即使它的代码完全没有改变。因而，子类必须要跟着其超类的更新而演变，除非超类是专门为了扩展而设计的，并且具有很好的文档说明。

虽然这样得到的类可以正常工作，但是它的功能正确性则需要依赖于这样的事实：**HashSet 的addAll 方法是在它的add 方法上实现的，这种“自用性（self-use）” 是实现细节，不是承诺，不能保证在Java 平台的所有实现中都保持不变，不能保证随着发行版本的不同而不发生变化。因此，得到的类将是非常脆弱的。**

**导致子类脆弱的一个相关的原因是，它们的超类在后续的发行版本中可以获得新的方法。**在把Hashtable 和Vector 加入到Collections Framework 中的时候，就修正了几个这类性质的问题。

上面这两个问题都来源于覆盖（overriding）动作。**如果在扩展一个类的时候，仅仅是增加新的方法，而不覆盖现有的方法，你可能会认为这是安全的。**虽然这种扩展方式比较安全一些，但是也并非完全没有风险。

**如果超类在后续的发行版本中获得了一个新的方法，并且不幸的是，你给子类提供了一个签名相同但返回类型不同的方法，那么这样的子类将无法通过编译。如果给子类提供的方法带有与新的超类完全相同的签名和返回类型，实际上就覆盖了超类中的方法，因此又回到上述的两个问题上去了。此外，你的方法是否能够遵守新的超类方法的约定，这也是很值得怀疑的，因为当你在编写子类方法的时候，这个约定根本没有面世。**

**幸运的是，有一种办法。不用扩展现有的类，而是在新的类中增加一个私有域，它引用现有类的一个实例。这种设计被称做“复合（composition）”，因为现有的类变成了新类的一个组件。新类中的每个实例方法都可以调用被包含的现有类实例中对应的方法，并返回它的结果。这被称为转发（forwarding），新类中的方法被称为转发方法（forwarding method）。这样得到的类将会非常稳固，它不依赖于现有类的实现细节，即使现有的类添加了新的方法，也不会影响新的类。**

**除了获得健壮性之外，这种设计也带来了格外的灵活性。**

**因为每个InstrumentedSet 实例都把另一个Set 实例包装起来，所以它被称做`包装类（wrapper class）`，这也正是Decorator 模式。**因为InstrumentedSet 类对一个集合进行了修饰，为它增加了计数特性。有时候，**复合和转发的结合也被错误地称为“委托（delegation）”，从技术的角度而言，这不是委托，除非包装对象把自身传递给被包装的对象。**

**包装类几乎没有什么缺点，需要注意的一点是，包装类不适合用在回调框架（callback framework）**。

**只有当子类真正是超类的子类型（subtype）时，才适合用继承。换句话说，对于两个类A 和B ，只有当两者之间确实存在“is-a” 关系的时候，类B 才应该扩展类A 。如果答案是否定的，通常情况下，B 应该包含A 的一个私有实例，并且暴露一个较小的、较简单的API：A 本质上不是B 的一部分，只是它的实现细节而已。**

**在Java 平台类库中，有许多明显违反这条原则的地方。例如，栈（stack）并不是向量（vector），所以Stack 不应该扩展Vector。同样地，属性列表也不是散列表，所以Properties 不应该扩展Hashtable。在这两种情况下，复合模式才是恰当的。**

**如果在适合于使用复合的地方使用了继承，则会不必要地暴露实现细节。这样得到的API 会把你限制在原始的实现上，永远限定了类的性能。更为严重的是，由于坩埚了内部的细节，客户端就可能直接访问这些内部细节。这样至少会导致语义上的混淆。**例如，如果p 指向Properties 实例，那么`p.getProperty(key)` 就有可能产生与`p.get(key)` 不同的结果：前者考虑了默认的属性表，而后者是继承自Hashtable 的，它则没有考虑默认属性列表。**最严重的是，客户有可能直接修改超类，从而破坏子类的约束条件。在Properties 的情形中，设计者的目标是，只允许字符串作为key 和value，但是直接访问底层的Hashtable 就可以违反这种约束条件。一旦违反约束条件，就不可能再使用Properties API 的其他部分（load 和store）了。等到发现这个问题时，要改正它已经晚了，因为客户端依赖于使用字符串的key 和value 了。**

在决定使用继承而不是复合之前，还应该问自己最后一个问题，对于你正在试图扩展的类，它的API 中有没有缺陷呢？如果有，你是否愿意把那些缺陷传播到类的API 中？继承机制会把超类API 中的所有缺陷传播到子类中，而复合则允许设计新的API 来隐藏这些缺陷。

简而言之，**继承的功能非常强大，但是也存在诸多问题，因为它违反了封装原则。只有当子类和超类之间确实存在子类型关系时，使用继承才是恰当的。即使如此，如果子类和超类处在不同的包中，并且超类并不是为了继承而设计的，那么继承将会导致脆弱性（fragility）。为了避免这种脆弱性，可以用复合和转发机制来代替继承，尤其是当存在适当的接口可以实现包装类的时候。包装类不仅比子类更加健壮，而且功能也更加强大。**

### 第17条：要么为继承而设计，并提供文档说明，要么就禁止继承

讲文档编写的，先略过

### 第18条：接口优于抽象类

Java 程序设计语言提供两种机制，可以**用来定义允许多个实现的类型：接口和抽象类。**这两种机制之间最明显的区别在于，**抽象类允许包含某些方法的实现，但是接口则不允许。**一个更为重要的区别在于，**为了实现由抽象类定义的类型，类必须成为抽象类的一个子类。任何一个类，只要它定义了所有必要的方法，并且遵守通用约定，它就被允许实现一个接口，而不管这个类是处于类层次（class hierarchy）的哪个位置。因为Java 只允许单继承，所以抽象类作为类型定义受到了极大的限制。**

- **现有的类可以很容易被更新，以实现新的接口。**如果这些方法尚不存在，你所需要做的只是增加必要的方法，然后在类的声明中增加一个implements 子句。例如，当Comparable 接口被引入到Java 平台的时候，会更新许多现有的类，以实现Comparable 接口。一般来说，无法更新现有的类来扩展新的抽象类。如果你希望让两个类扩展同一个抽象类，就必须把抽象类放到类型层次（type hierarchy）的高处，以便这两个类的一个祖先成为它的子类。遗憾的是，这样做会间接地伤害到类层次，迫使这个公共祖先的所有后代类都扩展这个新的抽象类，无论它对于这些后代类是否合适。

- **接口是定义mixin（混合类型）的理想选择。**不严格地讲，mixin是指这样的类型：**类除了实现它的“基本类型（primary type）”之外，还可以实现这个mixin类型，以表明它提供了某些可供选择的行为。**例如，Comparable 是一个mixin 接口，它允许表明它的实例可以与其它的可相互比较的对象进行排序。这样的接口之所以被称为mixin，是因为它允许任选的功能可被混合到类型的主要功能中。抽象类不能被用于定义mixin，同样也是因为它们不能被更新到现有的类中：类不可能有一个以上的父类，类层次结构中也没有适当的地方来插入mixin。

- **接口允许我们构造非层次结构类型的类型框架。**假设我们有一个接口代表一个singer（歌唱家），另一个接口代表一个songwriter（作曲家）。

  ```java
  public interface Singer {
      AudioClip sing(Song song);
  }
  public interface Songwriter {
      Song compose(boolean hit);
  }
  ```

  在现实中，有些歌唱家本身也是作曲家。因为我们使用了接口而不是抽象类来定义这些类型，所以对于单个类而言，它同时实现Singer 和Songwriter 是完全允许的。实际上，我们可以定义第三个接口，它同时扩展了Singer 和Songwriter，并添加了一些适合于这种组合的新的方法：

  ```java
  public interface SingerSongwriter extends Singer, Songwriter {
      AudioClip strum();
      void actSensitive();
  }
  ```

  另一种做法是编写一个臃肿（bloated）的类层次，对于每一种要被支持的属性组合，都包含一个单独的类。**类层次臃肿会导致类也臃肿，这些类包含许多方法，并且这些方法只是在参数的类型上有所不同而已，因为类层次中没有任何类型体现了公共的行为特征。**

通过第16条中介绍的包装类（wrapper class）模式，**接口使得安全地增强类的功能成为可能。**如果使用抽象类来定义类型，那么程序员除了使用继承的手段来增加功能，没有其他的选择。这样得到的类与包装类相比，功能更差，更脆弱。

虽然接口不允许包含方法的实现，但是使用接口来定义类型并不妨碍你为程序员提供实现上的帮助。**通过对你导出的每个重要接口都提供一个抽象的骨架实现（skeletal implementation）类，把接口和抽象类的优点结合起来。**接口的作用仍然是定义类型，但是骨架实现类接管了所有与接口实现相关的工作。

按照惯例，**骨架实现被称为AbstractInterface，这里的Interface 是指所实现的接口的名字。**例如，Collections Framework 为每个重要的集合接口都提供了一个**骨架实现，包括AbstractCollection、AbstractSet、AbstractList 和AbstractMap。**

骨架实现的美妙之处在于，它们为抽象类提供了实现上的帮助，但又不强加“抽象类被用作类型定义时”所特有的严格限制。**对于接口的大多数实现来讲，扩展骨架实现类是个很显然的选择，但并不是必要的**。如果预置的类无法扩展骨架实现类，这个类始终可以手工实现这个接口。此外，**骨架实现类仍然能够有助于接口的实现，实现了这个接口的类可以把对于接口方法的调用，转发到一个内部私有类的实例上，这个内部私有类扩展了骨架实现类，这种方法被称为模拟多重继承（simulated multiple inheritance），它与第16条中讨论的包装类模式密切相关。这项技术具有多重继承的绝大多数优点，同时又避免了相应的缺陷。**

编写骨架实现类相对比较简单，只是有点单调入味。**首先必须认真研究接口，并确定哪些方法是最为基本的（primitive），其他的方法则可以根据它们来实现。这些基本方法将成为骨架实现类中的抽象方法，然后，必须为接口中所有其他的方法提供具体的实现。**

因为骨架实现类是为了继承的目的而设计的，所以应该遵从第17条中介绍的所有关于设计和文档的指导原则。对于骨架实现类而言，好的文档绝对是非常必要的。

骨架实现上有个小小的不同，就是**简单实现（simple implementation）**，AbstractMap.SimpleEntry 就是个例子。简单实现就像个骨架实现，这是因为它实现了接口，并且是为了继承而设计的，但是区别在于它不是抽象的，它是最简单的可能的有效实现。你可以原封不动地使用，也可以看情况将它子类化。

使用抽象类来定义允许多个实现的类型，与使用接口相比有一个明显的优势：**抽象类的演变比接口的演变要容易得多。**如果在后续的发行版本中，你希望在抽象类中增加新的方法，始终可以增加具体方法，它包含合理的默认实现，然后，该抽象类的所有现有实现都将提供这个新的方法，对于接口，这样做是行不通的。

**一般来说，要想在公有接口中增加方法，而不破坏实现这个接口的所有现有的类，这是不可能的。**之前实现该接口的类将会漏掉新增加的方法，并且无法再通过编译。所有不从骨架实现类继承的接口实现仍然会遭到破坏。

因此，**设计公有的接口要非常谨慎。接口一旦被公开发行，并且已被广泛实现，再想改变这个接口几乎是不可能的。**你必须在初次设计的时候就保证接口是正确的。如果接口包含微小的瑕疵，它将会一直影响你以及接口的用户。如果接口具有严重的缺陷，它可以导致API 彻底失败。**在发行新接口的时候，最好的做法是，在接口被“冻结”之前，尽可能让更多的程序员用尽可能多的方式来实现这个新接口。这样有助于在依然可以改正缺陷的时候就发现它们。**

简而言之，**接口通常是定义允许多个实现的类型的最佳途径。这条规则有个例外，即当演变的容易性比灵活性和功能更为重要的时候。在这种情况下，应该使用抽象类来定义类型，但前提是必须理解并且可以接受这些局限性。如果你导出了一个重要的接口，就应该坚决考虑同时提供骨架实现类。最后，应该尽可能谨慎地设计所有的公有接口，并通过编写多个实现来对它们进行全面的测试。**

### 第19条：接口只用于定义类型

当类实现接口时，接口就充当可以引用这个类的实例的类型（type）。因此，类实现了接口，就表明客户端可以对这个类的实例实施某些动作。为了任何其他目的而定义接口是不恰当的。

**有一种接口被称为常量接口（constant interface），它不满足上面的条件。这种接口没有包含任何方法，它只包含静态的final 域，每个域都导出一个常量。使用这些常量的类实现这个接口，以避免用类名来修饰常量名。**

```java
public interface PhysicalConstants{
    static final double AVOGADROS_NUMBER = 6.02214199e23;
    static final double BOLTZMANN_CONSTANT = 1.3806503e-23;
}
```

**常量接口模式是对接口的不良使用。**类在内部使用某些常量，这纯粹是实现细节，实现常量接口，会导致把这样的实现细节泄露到该类的导出API 中。类实现常量接口，这对于这个类的用户来讲并没有什么价值。实际上，这样做反而会使他们更加糊涂。它代表了一种承诺：如果在将来的发行版本中，这个类被修改了，它不再需要使用这些常量了，它依然必须实现这个接口，以确保二进制兼容性。如果非final 类实现了常量接口，它的所有子类的命名空间也会被接口中的常量所“污染”。

**在Java 平台类库中有几个常量接口，例如java.io.ObjectStreamConstants。这些接口应该被认为是反面的典型，不值得效仿。**

**如果要导出常量，有几个合理的选择方案**。

1. 如果这些常量与某个现有的类或者接口紧密相关，就应该把这些常量**添加到这个类或者接口中**。例如，在Java 平台类库中所有的数值包装类，如Integer 和Double，都导出了MIN_VALUE 和MAX_VALUE 常量。
2. **如果这些常量最好被看作枚举类型的成员，就应该用枚举类型（enum type）（见第30条）来导出这些常量。**
3. **否则，应该使用不可实例化的工具类（utility class）（见第4 条）来导出这些常量。**

````java
public class PhysicalConstants{
    private PhysicalConstants(){} //prevent instantiation
    public static final double AVOGADROS_NUMBER = 6.02214199e23;
    public static final double BOLTZMANN_CONSTANT = 1.3806503e-23;
}
````

工具类通常要求客户端要用类名来修饰这些常量名，例如PhysicalConstants.AVOGADROS_NUMBER。如果大量利用工具类导出的常量，可以利用**静态导入（static import）机制**，避免用类名来修饰常量名，不过，静态导入机制是在Java 发行版本1.5 中才引入的。

简而言之，**接口应该只被用来定义类型，它们不应该被用来导出常量。**

### 第20条：类层次优于标签类

有时候，可能会遇到带有两种甚至更多种风格(flavor)的实例的类，并包含表示实例风格的*标签（tag）域*。例如，考虑下面这个类，它能够表示圆形或者矩形：

```java
// Tagged class - vastly inferior to a class hierarchy!
package org.effectivejava.examples.chapter04.Item20.taggedclass;

class Figure {
	enum Shape {
		RECTANGLE, CIRCLE
	};

	// Tag field - the shape of this figure
	final Shape shape;

	// These fields are used only if shape is RECTANGLE
	double length;
	double width;

	// This field is used only if shape is CIRCLE
	double radius;

	// Constructor for circle
	Figure(double radius) {
		shape = Shape.CIRCLE;
		this.radius = radius;
	}

	// Constructor for rectangle
	Figure(double length, double width) {
		shape = Shape.RECTANGLE;
		this.length = length;
		this.width = width;
	}

	double area() {
		switch (shape) {
		case RECTANGLE:
			return length * width;
		case CIRCLE:
			return Math.PI * (radius * radius);
		default:
			throw new AssertionError();
		}
	}
}
```

这种标签类（tagged class）有着许多缺点。它们中充斥着样板代码，包括枚举声明、标签域以及条件语句。由于许多实现乱七八糟地挤在单个类中，**破坏了可读性。内在占用也增加了，因为实例承担着属于其他风格的不相关的域。域不能做成是final 的，除非构造器初始化了不相关的域，产生更多的样板代码。构造器必须不借助编译器，来设置标签域，并初始化正确的数据域：如果初始化了错误的域，程序就会在运行时失败。无法给标签类添加风格，除非可以修改它的源文件。如果一定要添加风格，就必须记得给每个条件语句都添加一个条件，否则类就会在运行时失败。最后，实例的数据类型没有提供任何关于其风格的线索。一句话，标签类过于冗长、容易出错，并且效率低下。**

幸运的是，面向对象的语言例如Java，就提供了其他更好的方法来定义能表示多种风格对象的单个数据类型：**子类型化（subtyping）。标签类正是类层次的一种简单的仿效。**

为了将标签类转变成类层次，首先要为标签类中的每个方法都定义一个包含抽象方法的抽象类，这每个方法的行为都依赖于标签值。在Figure 类中，只有一个这样的方法：area。这个抽象类是类层次的根（root）。如果还有其他的方法其行为不依赖于标签的值，就把这样的方法放在这个类中。同样地，如果所有的方法都用到了某些数据域，就应该把它们放在这个类中。在Figure 类中，不存在这种类型独立的方法或者数据域。

接下来，为每种原始标签类都定义根类的具体子类。在前面的例子中，这样的类型有两个：圆形和矩形。在每个子类中都包含特定于该类型的数据域。在我们的示例中，radius 是特定于圆形的，length 和width 是特定于矩形的。同时在每个子类中还包括针对根类中每个抽象方法的相应实现。

```java
// Class hierarchy replacement for a tagged class
package org.effectivejava.examples.chapter04.Item20.hierarchy;

abstract class Figure {
	abstract double area();
}

class Circle extends Figure {
	final double radius;

	Circle(double radius) {
		this.radius = radius;
	}

	double area() {
		return Math.PI * (radius * radius);
	}
}

class Rectangle extends Figure {
	final double length;
	final double width;

	Rectangle(double length, double width) {
		this.length = length;
		this.width = width;
	}

	double area() {
		return length * width;
	}
}
```

这个类层次纠正了前面提到的标签类的所有缺点。**这段代码简单且清楚，没有包含在原来的版本中所见到的所有样板代码。每个类型的实现都配有自己的类，这些类都没有受到不相关的数据域的拖累。所有的域都是final 的。编译器确保每个类的构造器都初始化它的数据域。对于根类中声明的每个抽象方法，都确保有一个实现。这样就杜绝了由于遗漏switch case 而导致运行时失败的可能性。多个程序员可以独立地扩展层次结构，并且不用访问根类的源代码就能相互操作。每种类型都有一种相关的独立的数据类型，允许程序员指明变量的类型，限制变量，并将参数输入到特殊的类型。**

类层次的另一种好处在于，**它们可以用来反映类型之间本质上的层次关系，有助于增强灵活性，并进行更好的编译时类型检查。**假设上述例子中的标签类也允许表达正方形。类层次可以反映出正方形是一种特殊的矩形这一事实。

**如果层次结构是公有的，不允许域被直接访问，需要通过访问方法。（第14条）**

简而言之，**标签类很少有适用的时候。当你想要编写一个包含显式标签域的时候，应该考虑一下。这个标签是否可以被取消，这个类是否可以用类层次来代替。当你遇到一个包含标签域的现有类时，就要考虑将它重构到一个层次结构中。**

### 第21条：用函数对象表示策略

有些语言支持函数指针（function pointer）、代理（delegate）、lambda 表达式（lambda expression），或者支持类似的机制，允许程序把“调用特殊函数的能力”存储起来并传递这种能力。这种机制通常用于**允许函数的调用者通过传入第二个函数，来指定自己的行为。**通过传递不同的比较器函数，就可以获得各种不同的排列顺序，这正是策略模式的一个例子。比较器函数代表一种为元素排序的策略。

Java 没有提供函数指针，但是可以用对象引用实现同样的功能。调用对象上的方法通常是执行该对象上的某项操作。然而，我们也可以**定义这样的一种对象，它的方法执行其他对象（这些对象被显式地传递给这些方法）上的操作。如果一个类仅仅导出这样的一个方法，它的实例实际上就等同于一个指向该方法的指针。这样的实例被称为函数对象（function object）。**例如：

```java
class StringLengthComparator{
    public int compare(String s1, String s2) {
        return s1.length() - s2.length();
    }
}
```

这个类导出一个带两个字符串参数的方法。**这个方法是一个比较器，它根据长度来给字符串排序，而不是根据更常用的字典顺序。指向StringLengthComparator 对象的引用可以被当作一个指向该比较器的“函数指针（function pointer）”，可以在任意一对字符串上被调用。换句话说，StringLengthComparator 实例是用于字符串比较操作的具体策略（concrete strategy）。**

**作为典型的具体策略类，StringLengthComparator 类是无状态的（stateless）：它没有域，所以，这个类的所有实例在功能上都是相互等价的。因此它作为一个Singleton 是非常合适的，可以节省不必要的对象创建开销。**

```java
class StringLengthComparator {
    private StringLengthComparator() {}

    public static final StringLengthComparator INSTANCE = new StringLengthComparator();

    public int compare(String s1, String s2) {
        return s1.length() - s2.length();
    }
}
```

为了把StringLengthComparator 实例传递给方法，需要适当的参数类型，使用StringLengthComparator 并不好，因为客户端将无法传递任何其他的比较策略。相反，我们需要定义一个Comparator 接口，并修改StringLengthComparator 来实现这个接口。换句话说，**我们在设计具体的策略类时，还需要定义一个策略接口（strategy interface）。**

```java
public interface Comparator<T>{
    public int compare(T t1, T t2);
}
```

Comparator 接口也出现在java.util 包中。Comparator 接口是泛型的（见第26条），因此**它适合作为object 的比较器（原文翻译有些不准）**。前面的StringLengthComparator 可以实现Comparator\<String\> 

```java
class StringLengthComparator implements Comparator<String> {
	...   // class body is identical to the one shown above
}
```

具体的策略类往往使用匿名类声明（见第22 条）：

```java
Arrays.sort(stringArray, new Comparator<String>() {
    @Override
    public int compare(String t1, String t2) {
        return t1.length() - t2.length();
    }
});
```

但是，以这种方式使用匿名类时，将会在每次执行调用的时候创建一个新的实例。如果它被重复执行，考虑将函数对象存储到一个**私有的静态final 域**里，并重用它。这样可以为这个函数对象取一个有意义的域名称。

因为策略接口被用作所有具体策略实例的类型，所以我们并不需要为了导出具体策略，而把具体策略类做成公有的。相反，“宿主类（host class）”还可以导出公有的静态域（或者静态工厂方法），其类型为策略接口，具体的策略类可以是宿主类的私有嵌套类。

简而言之，**函数指针的主要用途就是实现策略（Strategy）模式。为了在Java 中实现这种模式，要声明一个接口来表示该策略，并且为每个具体策略声明一个实现了该接口的类。当一个具体策略只被使用一次时，通常使用匿名类来声明和实例化这个具体策略类。当一个具体策略是设计用来重复使用的时候，它的类通常就要被实现为私有的静态成员类，并通过公有的静态final 域被导出，其类型为该策略接口。**

### 第22条：优先考虑静态成员类

嵌套类（nested class）是指被定义在另一个类内部的类。嵌套类存在的目的应该只是为它的外围类（enclosing class）提供服务。如果嵌套类将来可能会用于其他的某个环境中，它就应该是顶层类（top-level class）。**嵌套类有四种：静态成员类（static member class）、非静态成员类（nonstatic member class）、匿名类（anonymous class）和局部类（local class）。**除了第种之外，其他三种都被称为内部类（inner class）。本条目将告诉你什么时候应该使用哪种嵌套类，以及这样做的原因。

静态成员类是最简单的一种嵌套类。最好把它看作是普通的类，只是碰巧被声明在另一个类的内部而已，它可以访问外围类的所有成员，包括那些声明为私有的成员。静态成员类是外围类的一个静态成员，与其他的静态成员一样，也遵守同样的可访问性规则。如果它被声明为私有的，它就只能在外围类的内部才可以被访问。

**静态成员类的一种常见用法是作为公有的辅助类，仅当与它的外部类一起使用时才有意义。**例如，Calculator 类的客户端可以用诸如Calculator.Operation.PLUS 和Calculator.Operation.MINUS 这样的名字来引用这些操作。

从语法上讲，静态成员类和非静态成员类之间唯一的区别是，静态成员类的声明中包含修饰符static。**非静态成员类的每个实例都隐含着与外围类的一个外围实例（enclosing instance）相关联。**在非静态成员类的实例方法内部，可以调用外围实例上的方法，或者利用修饰过的this 构造获得外围实例的引用。**在没有外围实例的情况下，要想创建非静态成员类的实例是不可能的。**

当非静态成员类的实例被创建的时候，它和外围实例之间的关联关系也随之被建立起来。而且，这种关联关系以后不能被修改。通常情况下，当在外围类的某个实例方法的内部调用非静态成员类的构造器时，这种关联关系被自动建立起来。

**非静态成员类的一种常见用法是定义一个Adapter，它允许外部类的实例被看作是另一个不相关的类的实例。**例如，Map 接口的实现往往使用非静态成员类来实现它们的集合视图（collection view）。同样，诸如Set 和List 这种集合接口的实现往往也使用非静态成员类来实现它们的迭代器（iterator）：

```java
class MySet<E> extends AbstractSet<E>{
    ... // omittted

    @Override
    public Iterator<E> iterator() {
        return new MyIterator();
    }
    
    private class MyIterator implements Iterator<E>{
        ...
    }
}
```

**如果声明成员类不要访问外围实例，就要始终把static 修饰符放在它的声明中，使它成员静态成员类，而不是非静态成员类。如果省略了static 修饰符，则每个实例都将包含一个额外的指向外围对象的引用。保存这份引用要消耗时间和空间，并且会导致外围实例在符合垃圾回收时（见第6条）时却仍然得以保留。如果在没有外围实例的情况下，也需要分配实例，就不能使用非静态成员类，因为非静态成员类的实例必须要有一个外围实例。**

**私有静态成员类的一种常见用法是用来代表外围类所代表的对象的组件。**例如，考虑一个Map 实例，它把key 和value 关联起来。许多Map 实现的内部都有一个Entry 对象，对应于Map 中的每个k-v对。虽然每个entry 都与一个Map 关联，但是entry 上的方法（getKey、getValue 和setValue）并不需要访问该Map。**因此使用非静态成员来表示entry 是很浪费的：私有的静态成员类是最佳的选择。**

Hashtable 中的Entry 类：

```Java
/**
 * Hashtable bucket collision list entry
 */
private static class Entry<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Hashtable.Entry<K,V> next;

    protected Entry(int hash, K key, V value, Hashtable.Entry<K,V> next) {
        this.hash = hash;
        this.key =  key;
        this.value = value;
        this.next = next;
    }
  
	@SuppressWarnings("unchecked")
    protected Object clone() {...  }
  
    // Map.Entry Ops

    public K getKey() {
        return key;
    }

    public V getValue() {
        return value;
    }

    public V setValue(V value) {
        if (value == null)
            throw new NullPointerException();

        V oldValue = this.value;
        this.value = value;
        return oldValue;
    }

    public boolean equals(Object o) {... }

    public int hashCode() {... }

    public String toString() {... }
}
```

如果不小心漏掉了entry 声明中的static 修饰符，该Map 仍然可以工作，但是每个entry 中将会包含一个指向该Map 的引用，这样就浪费了空间和时间。

如果相关的类是导出类的公有的或受保护的成员，毫无疑问，在静态和非静态成员类之间做出正确的选择是非常重要的。在这种情况下，该成员类就是导出API 元素，在后续的发行版本中，如果不违反二进制兼容性，就不能从非静态成员类变为静态成员类。

匿名类不同于Java 程序设计语言中的其他任何语法单元，正如你所想像的，匿名类没有名字。它还是外围类的一个成员。它并不与其他的成员一直被声明，而是在使用的同时被声明和实例化。匿名类可以出现在代码中任何允许存在表达式的地方。**当且仅当匿名类出现在非静态的环境中时，它才有外围实例。但是即使它们出现在静态的环境中，也不可能拥有任何静态成员。**

匿名类的适用性受到诸多的限制。**除了在它们被声明的时候之外，是无法将它们实例化的。你不能执行instanceof 测试，或者做任何需要命名类的其他事情。你无法声明一个匿名类来实现多个接口，或者扩展一个类，并同时扩展类和实现接口。匿名类的客户端无法调用任何成员，除了从它的超类中继承得到的之外。由于匿名类出现在表达式当中，它们必须保持简短——大约10行或者更少些——否则会影响程序的可读性。**

**匿名类的一种常见用法是动态地创建函数对象（function object，见第21 条）。**

**匿名类的另一种常见用法是创建过程对象（process object），比如Runnable、Thread或者TimerTask 实例。第三种常见的用法是在静态工厂方法的内部（参见第18条中的intArrayAsList方法）**

**局部类是甲种嵌套类中用得最少的类。在任何“可以声明局部变量” 的地方，都可以声明局部类，并且局部类也遵守同样的作用域规则。局部类与其他三种嵌套类中的每一种都有一些共同的属性。与成员类一样，局部类有名字，可以被重复地使用。与匿名类一样，只有当局部类是在非静态环境中定义的时候，都有外围实例，它们也不能包含静态成员。与匿名类一样，它们必须非常简短，以便不会影响到可读性。**

简而言之，**共有四种不同的嵌套类，每一种都有自己的用途。如果一个嵌套类需要在单个方法之外仍然是可见的，或者它太长了，不适合放到方法内部，就应该使用成员类。如果成员类的每个实例都需要一个指向其外围实例的引用，就要把成员类做成非静态的；否则就做成静态的。假设这个嵌套类属于一个方法的内部，如果你只要在一个地方创建实例，并且已经有了一个预置的类型可以说明这个类的特性，就要把它做成匿名类，否则就要做成局部类。**
