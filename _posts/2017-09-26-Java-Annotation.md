---
title: Java 注解
excerpt: |
  Java 注解
category: Java 基础
feature_image: "https://picsum.photos/2560/600?image=872"
---
视频地址：http://my.tv.sohu.com/pl/9110056/83327807.shtml

## Annotationn 注解

### 内容

- 注解入门
- 内置注解
- 自定义注解、元注解

### 什么是注解

- Annotation是从JDK5.0开始引入的新技术(还有泛型)，一些框架**Struts2，Spring，Hibernate，MyBatis都大量使用了注解**
- Annotation的作用：
  - 并不是程序本身，它可以对程序作出某种解释。（这一点跟注释没什么区别）
  - 可以被其它程序（比如：编译器等）读取，（注解进行信息处理流程，是注解和注释的重大区别）
- Annotation的格式：
  - 注解是以“@注解名” 在代码中存在的，还可以添加一些参数值，例如：**@SuppressWarnings(value="unchecked")**
- Annotation在哪里使用？
  - 可以附加在package, class, method, field 等上面，相当于给它们添加了额外的辅助信息，我们可以通过反射机制编程实现对这些元数据的访问

### 创建一个注解

- @interface 是定义注解的时候使用的一个符号，如`public @interface Override`

### 内置注解（1）

在注解原码里的@Target标签是描述在什么位置可以使用此注解，如Override只能放在方法上，@Override的代码如下：

```java
@Target(ElementType.METHOD) //放在什么位置
@Retention(RetentionPolicy.SOURCE)
public @interface Override {
}
```



- @Override
  - 定义在java.lang.Override中，此注解只适用于修饰方法，表示一个方法声明打算重写超类中的另一个方法声明
- @Deprecated
  - 定义在java.lang.Deprecated中，此注解可用于修饰方法，属性，类，表示不鼓励程序员使用这样的元素，通常是因为它很危险或存在更好的选择
- @SuppressWarnings
  - 定义在java.langSuppressWanrings中，用来抵制编译时的警告信息

### 内置注解（2）

- @SuppressWarnings(value="") value 可以为以下内容：

| 关键字         | 用途                                       |
| ----------- | ---------------------------------------- |
| deprecation | 使用了不赞成使用的类或方法时的警告                        |
| unchecked   | 执行了未检查的转换时的警告，例如当使用集合时没有用泛型 (Generics) 来指定集合保存的类型。 |
| fallthrough | 当 Switch 程序块直接通往下一种情况而没有 Break 时的警告。     |
| path        | 在类路径、源文件路径等中有不存在的路径时的警告。                 |
| serial      | 当在可序列化的类上缺少 serialVersionUID 定义时的警告。     |
| finally     | 任何 finally 子句不能正常完成时的警告。                 |
| all         | 关于以上所有情况的警告。                             |

- @SuppressWarnings(value={"unchecked", "path"}) //多个一起用的时候，value需要传入一个字符串数组

### 自定义注解

- **使用@interface自定义注解时，自动继承了java.lang.annotation.Annotation接口**
- **要点：**
  - @interface用来声明一个注解
    - 格式为：public @interface 注解名 [定义体]
  - 其中的每一个方法实际上是声明了一个配置参数。
    - 方法的名称就是参数的名称
    - 返回值类型就是参数的类型（返回值类型只能是基本类型，Class, String, enum）。
    - 可以通过default来声明参数的默认值
    - 如果只有一个参数成员，一般参数名为value
- 注意：
  - 注解元素必须要有值。我们定义注解元素时，经常使用空字符串，0作为默认值。
  - 也经常使用负数（比如：-1）表示不存在的含义

### 元注解

- **元注解的作用就是负责注解其他注解。Java定义了4个标准的meta-annotation类型，它们被用来提供对其它annotation类型作说明。**
- 这些类型和它们所支持的类在java.lang.annotation 包中可以找到：
  1. @Target
  2. @Retention
  3. @Documented
  4. @Inherited

#### @Target

- 作用：

  - 用于描述注解的使用范围（即被描述的注解可以用在什么地方）
  - Target通过ElementType来指定注解可使用范围的枚举集合

- ElementType的用法

  | 取值              | 注解使用范围                     |
  | --------------- | -------------------------- |
  | METHOD          | 可用于方法上                     |
  | TYPE            | 可用于类或者接口上，枚举               |
  | ANNOTATION_TYPE | 可用于注解类型上（被@interface修饰的类型） |
  | CONSTRUCTOR     | 可用于构造方法上                   |
  | FIELD           | 可用于域上                      |
  | LOCAL_VARIABLE  | 可用于局部变量上                   |
  | PACKAGE         | 用于记录java文件的package信息       |
  | PARAMETER       | 可用于参数上                     |

  **这里重点说明下：ElementType. PACKAGE。它并不是使用在一般的类中，而是用在固定的文件package-info.java中。这里需要强调命名一定是“package-info”。由于package-info.java并不是一个合法的类，使用eclipse创建类的方式会提示不合法，所以需要以创建文件的方式来创建package-info.java。**

  例如，定义可使用范围PACKAGE：

  ```java
  @Target({ElementType.PACKAGE,ElementType.METHOD})
  @Retention(RetentionPolicy.RUNTIME)
  @Inherited
  @Documented
  public @interface AsynLog {
    
  }
  ```

  那么，创建文件：package-info.java，内容如下：

  ```java
  @AsynLog
  package org.my.commons.logs.annotation; 
  ```

  重点说明：注解只能在ElementType设定的范围内使用，否则将会编译报错。例如：范围只包含ElementType.METHOD ，则表明该注解只能使用在类的方法上，超出使用范围将编译异常。

- 在编写注解的时候，里面的参数写为如下，value是参数，而不是方法：

  ```java
  public @interface Target {
      ElementType[] value();
  }
  ```

#### @Retention

- **作用**：

  - **表示需要在什么级别保存该注解信息，用于描述注解的生命周期**

    1. RetentionPolicy.SOURCE —— 这种类型的Annotations只在源代码级别保留,编译时就会被忽略（**编译器使用或者加载器使用**）
    2. RetentionPolicy.CLASS —— 这种类型的Annotations编译时被保留,在class文件中存在,但JVM将会忽略（**编译器使用或者加载器使用**）
    3. RetentionPolicy.RUNTIME —— 这种类型的Annotations将被JVM保留,所以他们能在运行时被JVM或其他使用反射机制的代码所读取和使用.（**自定义一般使用这个**，用Runtime则上面两个都有效），**可以在加载这个类之后，被反射来读取到**，Source不行

    ```java
    @Target({ElementType.METHOD})
    @Retention(RetentionPolicy.RUNTIME)
    public @interface MyAnnotation {
    }
    ```

### 自定义注解

- 在注解里面的参数需要写为“String studentName() ”，还可以设置一个默认值，**不设置默认值则在用此注解的时候必须要传入一个值**。

```java
public @interface MyAnnotation {
    String studentName() default ""; //参数及参数类型，通常如果是String 类型的，一般default一个“”，int的default为0
    String[] schools() default {"a", "b"}; //数组的默认值
}
```

- 如果注解只有一个参数，通常把这个属性定义为value()（参数名定义成value）
- 注解的解析需要用到反射

### 什么是ORM？(Object Relationship Mapping)

因为我们数据库一般是关系型数据库，存储数据是按照关系型的存储，而我们代码一般是利用对象(Object)，类(Class)，属性(Attribute)这样的存储方式，所以会经常有关系型的数据与对象之间的转换操作，经常把类与表进行转化，**类中的信息与表中的信息进行转化**，这就是ORM映射。

#### ORM映射的三个基本规则

1. 类和表结构对应
2. 属性和字段对应
3. 对象和记录对应

### 使用注解完成类和表结构的映射关系

- 学习了反射机制后，我们可以定义注解处理流程处理这些注解，实现更复杂的功能

### 完整的示例，将类信息转换为数据库的列

一个注解完整的使用需要三个步骤：

1. 定义这个注解本身
2. 类里面使用这个注解
3. 通过写一个解析程序，把这些注解读出来，进行相关的解析的处理

**在我们实际工作中关于解析注解这部分通常都是由一些框架已经做好了，我们不需要自己去解析，只要学习怎么用**

### 使用反射读取注解信息，模拟处理注解信息的流程

```java
            Class clazz = Class.forName("JavaAnnotationPractise.ORMHomework.SxtStudent");

            //获得类的所有有效注解
            Annotation[] annotations = clazz.getAnnotations();
            for (Annotation annotation : annotations) {
                System.out.println("annotation = " + annotation); // annotation = @JavaAnnotationPractise.ORMHomework.SxtTable(value=TB_Student)
            }

            //获得类的指定的注解
            SxtTable annotation = (SxtTable)clazz.getAnnotation(SxtTable.class);
            System.out.println("annotation.value() = " + annotation.value()); //annotation.value() = TB_Student

            //获得类的属性的注解
            Field studentName = clazz.getDeclaredField("studentName");
            SxtField studentNameAnnotation = studentName.getAnnotation(SxtField.class);
            System.out.println(" studentNameAnnotation.columnName() = " + studentNameAnnotation.columnName());
            System.out.println("studentNameAnnotation.type() = " + studentNameAnnotation.type());
            System.out.println("studentNameAnnotation.length() = " + studentNameAnnotation.length());
//            System.out.println("studentNameAnnotation = " + studentNameAnnotation); //studentNameAnnotation = @JavaAnnotationPractise.ORMHomework.SxtField(columnName=sName, type=varchar, length=10)

            //根据获得的表名，字段的信息，拼出SQL语句，使用JDBC执行这个SQL，在数据库中生成相关的表
...
```

### 反射机制的性能问题

- 反射给我们带来灵活、自由的同时，弊端是让程序变慢
- 我们可以用setAccessible来提高反射的性能

#### setAccessible

- 启用和禁用访问安全检查的开关，值为true时，则指示反射的对象在使用时应该取消Java语言访问检查。值为false时则指示反射的对象应该实施Java语言访问检查，**并不是为true就能访问，为false不能访问**
- 设置setAccessible 为true时，可以将效率提高4倍。
- 例如，不利用反射，直接进行的方式需要2200ms，则accessible 为false(默认值)时，需要耗时62000ms(大概是前者的30倍)，而在设置了accessible 为true时，则需要消耗14000ms(是直接执行方法的7倍)
