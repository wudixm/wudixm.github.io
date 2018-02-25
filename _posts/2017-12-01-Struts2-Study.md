---
title: Struts2 入门
excerpt: |
  Struts2 入门
category: Java Frameworks
feature_image: "https://picsum.photos/2560/600?image=872"
---
## Struts2

#### 使用Filter 作为控制器MVC

#### MVC 设计模式概览

- 实现MVC（Model、View、Controller）模式的应用程序由3 部分构成：
  1. 模式：封装应用程序的数据和业务逻辑 — **POJO（Plain old Java Object）**
  2. 视图：实现应用程序的信息显示功能 — **JSP**
  3. 控制器：接收来自用户的输入，调用模型层，响应对应的视图组件 — **Servlet Filter**

#### 使用Filter 作为控制器的好处

- 使用一个过滤器来作为控制器，可以方便地在应用程序里对所有资源（包括静态资源）进行控制访问

#### Servlet VS Filter

1. Servlet 能做的Filter 是否都可以完成吗？可以
2. Filter 能做的的Servlet 都可以完成吗？拦截资源却不是Servlet 所擅长的！Filter 中有一个FilterChain，这个API 在Servlet 中没有。

### Struts2 概述

- Struts2 是一个用来开发MVC 应用程序的框架，它**提供了Web 应用程序开发过程中的一些常见问题的解决方案**：
  - 对来自用户的**输入数据进行合法性验证**
  - 统一的布局
  - 可扩展性
  - 国际化和本地化
  - 支持Ajax
  - 表单的重复提交
  - 文件的上传下载
  - ...
- Struts 1 升级到Struts2
  - Struts1 里使用ActionServlet 作为控制器，Struts2 使用了一个过滤器为控制器
  - Struts1 中每个HTML 表单都对应一个ActionForm 实例，Struts2 中HTML 表单将被直接映射到一个POJO
  - Struts1 的验证逻辑编写在ActionForm 中，Struts2 中的验证逻辑编写在Action 中
  - Struts1 中Action 类必须继承Action 类，Struts2 中任何一个POJO 都可以是一个Action 类
  - Struts2 在页面里使用OGNL 来显示各种对象模式，可以不再使用EL 和JSTL

### 搭建Struts2 的环境：

1. 加入Jar 包
2. 在web.xml  文件中配置struts2，复制struts2 的web.xml 文件中的过滤器到当前web 应用中的web.xml 文件中
3. 在当前web 应用的classpath 下添加struts2 的配置文件struts.xml

```xml
<!--
	package : 包，struts2 使用package 来组织模块，name 随便写，就是模块名字
	name 属性：必填，用于其它的包应用当前包
	extends： 当前包继承哪个包，继承的，即可以继承其中的所有的配置，通常都继承struts-default
			，用到定义好的结果类型，配置的拦截器，拦截器栈，默认的Action
-->
<package name="helloworld" extends="struts-default">
	<!--
		配置一个action，一个Struts2 的请求就是一个action
		name：对应一个Struts2 的请求的名字，不包括扩展名
		result: 结果
	-->
	<action name="product-input">
		<result>/WEB-INF/pages/input.jsp</result>
	</action>

</package>
```

#### 与自己实现有哪些不同

1. 搭建Struts2 的开发环境

2. 不需要显式地定义Filter，而使用的是Struts2 的配置文件

3. Details.jsp 比以前变得简单了

4. 步骤：

   1. 有index.jsp 页面的`product-input.action` 转到 `/WEB-INF/pages/input.jsp` — 在struts2 中配置一个action。

      ```xml
      <action name="product-input">
      	<result>/WEB-INF/pages/input.jsp</result>
      </action>
      ```

   2. 由input.jsp 页面的action product-save.action 到Product 类的save 方法，再到 details.jsp 文件

      ```xml
      <action name="product-save" class="com.struts2.helloworld.Product"
              method="save">
          <result name="details">/WEB-INF/pages/details.jsp</result>
      </action>
      ```

      在Product 中定义一个save 方法，且返回值为 details 字符串


####  知识点总结

- Package 名字是为了区分，好维护，例如，想找一个客户管理的action，那么就去找客户管理的package 下的action，
- 一定不要忘记`extends struts-default`，

```xml
<!--
    package : 包，struts2 使用package 来组织模块，name 随便写，就是模块名字
    name 属性：必填，用于其它的包应用当前包
    extends： 当前包继承哪个包，继承的，即可以继承其中的所有的配置，通常都继承struts-default
            ，用到定义好的结果类型，配置的拦截器，拦截器栈，默认的Action
    namespace: 可选，如果没有给出，则以 / 为默认值
               如果配置一个非默认值的话，则要想调用这个包里的action，
               就必须把这个属性所定义的命名空间添加到有关的URI 字符串里

               http://localhost:8080/struts2-01/namespace/product-save.action
               http://localhost:8080/contextPath/namespace/actionName.action

-->
<package name="helloworld" extends="struts-default" namespace="/">
    <!--
        配置一个action，一个Struts2 的请求就是一个action
        name：对应一个Struts2 的请求的名字，不包括扩展名
        class 的默认值为：http://localhost:8080/struts2-01/product-save.action
        method 的默认值为：execute
        result: 结果

    -->
    <action name="product-input" class="http://localhost:8080/struts2-01/product-save.action"
            method="execute">
        <!--
            result：结果，表示Action 方法执行后可能返回的一个结果，所以一个action 节点可能会有多个
                    result 子节点，使用name 值来区分
            name: 标识一个result，和action 方法的返回值对应，默认值为success
            type: 表示结果的类型，默认值为 dispatcher（转发到结果）
        -->
        <result name="success" type="dispatch">/WEB-INF/pages/input.jsp</result>
    </action>

    <action name="product-save" class="com.struts2.helloworld.Product"
            method="save">
        <result name="details">/WEB-INF/pages/details.jsp</result>
    </action>
</package>
```

### Action

#### 搭建struts2 开发环境 — 三个步骤（复习）

#### action VS Action 类到底是什么

1. action：代表一个Struts2 的请求
2. Action 类：能够处理Struts2 的请求的类

#### Action 类的特点

- 属性的名字必须遵守与JavaBeans 属性名相同的命名规则**（属性名字一定要和Action 里面的set 方法一致），**属性的类型可以是任意类型，从字符串到非字符串（基本数据类型）之间的数据转换可以自动发生
- 必须有一个不带参数的构造器 — 因为是通过反射，利用全类名创建的Action 的实例
- 至少有一个供Struts2 在执行这个action 时调用的方法
- 同一个Action 类可以包含多个action 方法
- Struts2 会为每一个HTTP 请求创建一个新的Action 实例，即Action 不是单例的，所以是线程安全的

### 在Action 中访问WEB 资源

#### 概述

1. 什么是WEB 资源？
   - HttpServletRequest, HttpSession, ServletContext 等原生的Servlet API
2. 为什么访问WEB 资源
   - B\S 的应用的Controller 中必然需要访问WEB 资源，向域对象中读写属性，读写Cookie，获取realPath 等等
3. 如何访问？
   - **和Servlet API 解耦的方式：**只能访问有限的Servlet API 对象，且只能访问其有限的方法（读取请求参数，读写域对象的属性，使Session 失效，一些常用的功能）
     - 使用ActionContext
     - 实现XXXAware 接口
   - **和Servlet API 耦合的方式：**可以访问更多的Servlet API 对象，且可以调用其原生的方法
     - 使用ServletActionContext
     - 实现ServletXXXAware 接口

### 和Servlet API 解耦的方式

为了避免与Servlet API 耦合在一起，方便Action 做单元测试，Struts2 对HttpServletRequest, HttpSession 和ServletContext 进行了封装，构造了3 个Map 对象来替代这3 个对象，在Action 中可以直接使用HttpServletRequest, HttpServletSession, ServletContext 对应的**Map 对象来保存和读取数据，只能操作其属性，不能调用其原生的方法**

#### 通过ActionContext 访问Web 资源

- **ActionContext 是Action 执行的上下文对象，在ActionContext 中保存了Action 执行所需要的所有对象，包括parameters, request, session, application 等**
- 获取HttpSession 对应的Map 对象：
  - public Map getSession()
- 获取ServletContext 对应的Map 对象：
  - public Map getApplication()
- 获取请求参数对应的Map 对象：
  - public Map getParameters()
- 获取HttpServletRequest 对应的Map 对象：
  - public Object get(Object key): ActionContext 类中没有提供类似`getRequest()` 这样的方法来获取HttpServletRequest 对应的Map 对象，要得到HttpServletRequest 对应的Map 对象，可以**通过为get() 方法传递“request” 参数实现**

```java
// 0. 获取ActionContext 对象
// ActionContext 是Action 的上下文对象，可以从中获取到当前Action 需要的一切信息
ActionContext context = ActionContext.getContext();

// 1. 获取application 对应的Map，并向其中添加一个属性，获取其中添加一个属性
//    通过调用ActionContext 对象的getApplication() 方法来获取application 对应的Map 对象
Map<String, Object> applicationMap = context.getApplication();

// 设置属性
applicationMap.put("applicationKey", "applicationValue"); // ${applicationScope.applicationKey }

// 获取属性
Object date = applicationMap.get("date");
System.out.println(date);


//2. session
Map<String, Object> sessionMap = context.getSession();
sessionMap.put("sessionKey", "sessionValue"); //${sessionScope.sessionKey }

//3. request
//   ActionContext 中并没有提供getRequest 方法来获取request 对应的Map
//   需要手工调用get() 方法，传入 request 字符串来获取
Map<String, Object> request = (Map<String, Object>) context.get("request");
request.put("requetsKey", "requestValue"); // ${requestScope.requestKey }

//4. 获取请求参数对应的Map，并获取指定的参数值
//   键：请求参数的名字， 值：请求参数的值对应的字符串数组
//   注意： 1. getParameters 的返回值为Map<String, Object>，而不是Map<String, String>
//         2. parameters 这个Map 只能读，不能写入数据，如果定稿，不会出错，但是不起作用
Map<String, Object> parameters = context.getParameters();
parameters.get("name");

parameters.put("age", 100); // ${params.age} 前台是取不出来的，原因是以上第2点
return "success";
```

#### 使用Aware 接口获取Web 资源

- 实现ApplicationAware ,SessionAware, RequestAware, ParameterAware 接口，并添加set 方法，依赖注入会把这些Map 注入到类中
- 选用的建议：若一个Action 类中有多个action 方法，且多个方法都需要使用域对象的Map 或Parameters，则建议使用实现Aware 接口的方式，因为不用在每个Map 中多次获取

```java
public class TestAwareAction implements ApplicationAware ,SessionAware, RequestAware, ParameterAware{

    // 先定义成员变量，再在setXXX 方法里面对这个成员变量进行赋值
    // 这些setXXX 方法都是struts2 调用的，struts2 把已经封装好的Map
    // 对象传进来，我们在这里只需要接收就好了，依赖注入（DI）

    // ActionContext 
    private Map<String, Object> appplication = null;

    public String execute() {
        //1. 向application 中加入一个属性：key - value
        appplication.put("applicationKey2", "applicationValue2");

        //2. 从application 中读取一个属性：并打印
        System.out.println(appplication.get("date"));
        return "success";
    }

    @Override
    public void setApplication(Map<String, Object> map) {
        this.appplication = map;
    }

    @Override
    public void setParameters(Map<String, String[]> map) {... }

    @Override
    public void setRequest(Map<String, Object> map) {...}

    @Override
    public void setSession(Map<String, Object> map) {...}
}
```

- session 对应的Map 实际上是SessionMap 类型的，强转后若调用其invalidate() 方法，可以使其session 失效

```java
//2. session
Map<String, Object> sessionMap = context.getSession();
sessionMap.put("sessionKey", "sessionValue");         //${sessionScope.sessionKey }

if (sessionMap instanceof SessionMap) {
    SessionMap sm = (SessionMap) sessionMap;
    sm.invalidate();
}
```

### 与Servlet 耦合的方式访问Web 资源

非耦合的方式只能获取Map 等内容，有时候我们需要一些原生的API，比如getRealPath()，这个方法用Map 是无法提供的，这个时候我们就要用到原生的API

也是两种方法，同非耦合的方式非常相似：

1. 利用ServletActionContext 对象

   - 直接获取HttpServletRequest 对象：`ServletActionContext.getRequest()`
   - 直接获取HttpSession 对象：`ServletActionContext.getRequest().getSession()`
   - 直接获取ServletContext 对象：`ServletActionContext.getServletContext()`

2. 通过实现ServletRequestAware, ServletContextAware 等接口的方式

   ```java
   /**
    * 通过实现ServletXXXAware 接口的方式可以由Struts2 注入
    * 需要的Servlet 相关的对象
    *
    * ServletRequestAware：注入HttpServletRequest 对象（比较常用）
    * ServletContextAware：注入ServletContext 对象（比较常用）
    * ServletResponseAware：注入HttpServletResponse对象
    */
   public class TestServletAwareAction implements ServletRequestAware, ServletContextAware, ServletResponseAware{}
   ```

#### 关于Struts2 请求的扩展名问题

1. 打开 org.apache.struts2 包下的default.properties 文件中配置了Struts2 应用个的一些常量
2. struts.action.extension 定义了当前Struts2 应用可以接受的请求的扩展名
3. 可以在struts.xml 文件中以常量配置的方式修改default.properties 中所配置的常量

```xml
<constant name="struts.action.extension" value="action,do,"></constant>
```

### Action Support

1. ActionSupport 是默认的类：若某个action 节点没有配置class 属性，则ActionSupport 即为等待执行的Action 类，而execute 方法即为默认要执行的action 方法
2. **在手工完成字段验证，显示错误消息，国际化**等情况下，推荐继承ActionSupport

#### 通配符映射

- 一个Web 应用可能有成百上千个action 声明，可以利用Struts 提供的**通配符映射机制**把多个彼此相似的映射关系简化为一个映射关系
- 通配符映射规则：
  - 若找到多个匹配，**没有通配符的那个将胜出**，因为是精确匹配
  - **若指定的动作不存在，**Struts 将会尝试把这个URI 与任何一个包含着通配符\* 的动作名进行匹配
  - **被通配符匹配到的URI 字符串的子串可以用 `{1}`、`{2}` 来引用。**`{1}` 匹配第一个子串，`{2}` 匹配第二个子串...
  - **`{0}` 匹配整个URI**
  - 若Struts 找到的带有通配符的匹配不止一个，**则按先后顺序进行匹配**
  - \* 可以匹配零个或多个字符，但不包括 / 字符，如果想把 / 字符包括在内，需要使用 \*\*，如果需要对某个字符进行转义，需要使用 \。

```xml
<action name="UserAction-save" class="com.struts2.helloworld.User"
        method="save">
    <result name="save-success">/success.jsp</result>
</action>
<action name="UserAction-query" class="com.struts2.helloworld.User"
        method="query">
    <result name="query-success">/success.jsp</result>
</action>
<action name="UserAction-update" class="com.struts2.helloworld.User"
        method="update">
    <result name="update-success">/success.jsp</result>
</action>
<!-- 使用通配符 -->
<action name="UserAction-*" class="com.struts2.helloworld.User"
        method="{1}">
    <result name="{1}-success">/success.jsp</result>
</action>
```

#### 动态方法调用

- 动态方法调用：通过URL 动态调用Action 中的方法
- URI：
  - /struts-app2/Product.action；Struts 调用Product 类的execute() 方法
  - /struts-app2/Product!save.action；Struts 调用Product 类的save() 方法
- 默认情况下，Struts 的动态方法调用是处于禁用状态

```xml
<!-- 打开允许动态方法调用的开关，默认是false -->
<constant name="struts.enable.DynamicMethodInvocation" value="true"></constant>
```

- 在servletPath 后面添加感叹号和一个方法名，会去调用类中相应的方法
- 在开发过程中，不推荐这么做，因为会暴露需要调用的方法名

### Struts 值栈

- valueStack 值栈：贯穿整个Action 的生命周期（每个Action 类的对象实例都拥有一个ValueStack 对象），相当于一个数据的中转站，在其中保存当前Action 对象和其他相关对象
- Struts 框架把ValueStack 对象保存在名为"struts.valueStack" 的请求属性中
- **之所以在页面上能够获取一个一个属性值的话，并不是在请求域里面真的有那个属性，而是从值栈里面获取的**
- 在ValueStack 对象的内部有两个逻辑部分：
  - ContextMap - Map栈：Struts 把各种各样的映射关系（一些Map 类型的对象）压入ContextMap 中，实际上就是ActionContext 的一个引用
  - ObjectStack - 对象栈：Struts 把Action 和相关对象压入ObjectStack 中，是一个栈的数据结构，根是root

#### 关于值栈：

1. helloworld 时，${productName} 读取productName 值，实际上该属性并不在request 等域对象中，而是从值栈中获取的
2. ValueStack：
   1. 可以从ActionContext 中获取值栈对象
   2. 值栈分为两个逻辑部分
      - Map 栈：实际上是OgnlContext 类型，是一个Map，也是对ActionContext 的一个引用，里面保存着各种Map：requestMap, sessionMap, parametersMap, applicationMap, attr
      - 对象栈：实际上是CompoundRoot 类型，是一个使用ArrayList 定义的栈，里面保存各种和当前Action 实例相关的对象，是一个数据结构意义的栈

### OGNL

- 在JSP 页面上可以利用OGNL（Object-Graph Naviagtion Language：对象-图导航语言）访问到值栈（ValueStack）里的对象属性
- **若希望访问值栈中ContextMap 中的数据，需要给OGNL 表达式加上一个前缀字符 #，如果没有前缀字符#，搜索将在ObjectStack 里进行**

- Struts2 利用 `s:property` 标签和OGNL 表达式来读取值栈中的属性值
- 值栈中的属性值是什么：
  - 对象对象栈：对象栈中某一个对象的属性值
  - Map 栈：request, session, application 的一个属性值或一个请求参数的值

#### 读取ObjectStack 里的对象的属性

- 若想访问ObjectStack 里的某个对象的属性，可以使用以下几种形式之一：

  ```java
  object.propertyName
  object[`propertyName`]
  object["propertyName"]
  ```

- **ObjectStack 里的对象可以通过一个从0 开始的下标来引用**。ObjectStack 里的栈顶对象可以用`[0]` 来引用，它下面的那个对象可以用`[1]` 引用，若希望返回栈顶对象的message 属性值：`[0].message` 或`[0]['message']`或`[0]["message"]`

- **若在指定的对象里没有找到指定的属性，则到指定对象的下一个对象里继续搜索，**即[n] 的含义是从第n 个开始搜索，而不是只搜索第n 个对象

- 若从栈顶对象开始搜索，则可以省略下标部分：message

- 结合 s:property 标签：`<s:property value="[0].message"/>` 等同于`<s:property value="message"/>` 

##### 默认情况下，Action 对象会被Struts2 自动地放到值栈的栈顶

```xml
<s:debug></s:debug> 可以查看值栈的状态
ProductPrice: <s:property value="[0].productPrice"></s:property> 利用s 标签来显示值栈人内容
ProductPrice: <s:property value=".productPrice"></s:property> 如果是读取栈顶的元素的属性的情况，[0] 也是可以省略的
```

如果在后台向值栈中压入新的对象，则在前台显示的时候，可能会出现不匹配的问题，后台在值栈中压入新的对象：

```java
System.out.println("save " + this);

//1. 获取值栈
ValueStack valueStack = ActionContext.getContext().getValueStack();

//2. 创建Test 对象，并为其属性赋值
TestProduct object = new TestProduct();
object.setProductDesc("aabbccdd");
object.setProductName("abcd");

//3. 把Test 对象压入值栈的栈顶
valueStack.push(object);

//说明：在push 方法后，值栈的栈顶对象已经从Product 变为了TestProduct 对象，所以在前台获取 productName 的时候，获取的是TestProduct 对象的属性值，但是，在前台获取 productPrice 的时候，因为 TestProduct 对象没有 price 属性，则前台获取的 productPrice 属性还是 Product 对象的属性

return "details";
```

#### 读取Context Map  - Map 栈里的对象的属性

若想访问ContextMap 里的某个对象的属性，可以使用以下几种形式之一：

```java
#object.propertyName
#object[`propertyName`]
#object["propertyName"]
```

示例：

- 返回session 中的code 属性：`#session.code`
- 返回request 中的customer 属性的name 属性值：`#request.customer.name`
- 返回域对象（按request, session, application 的顺序）的lastAccessDate 属性：`#attr.lastAccessDate`

```xml
java 代码中在sessionMap 和requestMap 中加入对象
sessionMap.put("product", this);
requestMap.put("test", object);

JSP 页面中利用EL 和OGNL 表达式获取Map 中的属性
ProductName1 : ${sessionScope.product.productName }
<s:property value="#session.product.productPrice"></s:property>

ProductName2 : ${RequestScope.test.productName }
<s:property value="#request.test.productPrice"></s:property>
```

#### OGNL 调用字段和方法

- 可以利用OGNL 调用：

  - **任何一个Java 类里的静态字段或方法**
  - **被压入到ValueStack 栈的对象上的公共字段和方法**

- 默认情况下，Struts2 不允许调用任意Java 类静态方法，需要重新设置struts.ognl.allowStaticMethodAccess 为true

- 调用静态字段或方法需要使用如下所示的语法：

  - @fullyQualifiedClassName@fieldName

    **@java.util.Calendar@DECEMBER**

  - @fullyQualifiedClassName@methodName(argumentList):

    **@app4.Util@now()**

- 调用一个实例字段或方法的语法，其中object 是ObjectStack 栈里的某个对象的引用：

  - .object.fieldName: [0].datePattern
  - .object.methodName(argumentList): [0].repeat(3, "Hello");

```xml
<!-- 使用OGNL 调用public 类的public 类型的静态字段和静态方法 -->
<s:property value="@java.lang.Math@PI"></s:property>

<!-- 默认不允许调用静态方法，需要在struts.xml 文件里面配置打开struts.ognl.allowStaticMethodAccess -->
<s:property value="@java.lang.Math@cos(0)"></s:property>

<s:property value="productName"></s:property> 打印CPU
<!-- 调用对象栈的方法，为一个属性赋值 -->
<s:property value="setProductName('abcd')"></s:property>
<s:property value="productName"></s:property> 调用了set 方法，将其值改变了
```

#### 访问数组类型的属性

- 有些属性将返回一个对象数组而不是单个对象，可以像读取任何其他对象属性那样读取它们，这种**数组型属性的各个元素以逗号分隔，并且不带方括号**
- 可以使用下标访问数组中指定的元素：`color[0]`
- 可以通过调用其length 字段查出给定数组中有多少个元素：`color.length`

#### 访问List

#### 访问Map 类型的属性

```jsp
<%
    Map<String, String> letters = new HashMap<String, String>();
    request.setAttribute("letters", letters);
    letters.put("AA","a");
    letters.put("BB","b");
    letters.put("CC","c");
    letters.put("DD","d");

%>
<!-- 使用OGNL 访问Map -->
<s:property value="#attr.letters.size"></s:property>
AA: <s:property value="#attr.letters['AA']"></s:property>
```

#### 使用EL 访问值栈中对象的属性

用EL 的，肯定可以使用OGNL，反过来，能用OGNL 的不一定能用EL

#### 异常处理: exception-mapping 元素

- exception-mapping 元素：配置当前action 的**声明式异常处理**
- exception-mapping 元素中有2 个属性
  - exception: 指定需要捕获的异常类型，异常的全类名
  - result：指定一个响应结果，该结果将在捕获到指定异常时被执行，既可以来自当前action 的声明，也可以来自**global-result** 声明
- 可以通过**global-exception-mappings** 元素为应用程序提供一个全局性的异常捕获映射，但在global-exception-mapping 元素下声明的任何exception-mapping 元素只能引用在**global-results** 元素下声明的某个result 元素
- 声明式异常处理机制由ExceptionMappingInterceptor 拦截器负责处理，当某个exception-mapping 元素声明的异常被捕获到时，ExceptionMappingInterceptor 拦截器就会向ValueStack 中添加**两个对象**：
  - exception：表示被捕获异常的Exception 对象
  - exceptionStack: 包含着被捕获异常的栈

```xml
<action name="product-input" class="com.opensymphony.xwork2.ActionSupport"
        method="execute">
    <exception-mapping result="input" exception="java.lang.ArithmeticException"></exception-mapping>
    <result name="input">/input.jsp</result>
    <result name="success" type="dispatcher">/WEB-INF/pages/input.jsp</result>
</action>
```

### 通用标签

#### property 标签

- property 标签用来输出一个值栈属性的值
- **如果value 属性没有给出，ValueStack 值栈栈顶对象的值被输出**
- **在许多情况下，JSP EL 可以提供更简洁的语法**

#### Struts2 自动把Action 对象放入到值栈中

1. 放入的时间点：Struts2 终将调用Action 类的Action 方法。但在调用该方法之前：

   1. 先创建一个StrutsActionProxy 对象
   2. 再创建StrutsActionProxy 之后，对其进行初始化时，把Action对象放入到值栈中

2. `s:property`：打印值栈中的属性值的，对象对象栈，打印值栈中对应的属性值

3. 对于 Map 栈，打印request, session, application 的某个属性值或某个请求参数的值

   ```xml
   <s:property value="#request.test.productPrice"></s:property>
   <s:property value="#parameters.name[0]"></s:property>
   ```

#### url 标签

- url 标签用来动态地创建一个URL
- 常用的属性有：action, includeParams, method, namespace, value, var

```jsp
<s:url value="/getProduct" var="url" >
    <!-- 指定url 包含的请求参数，1001 不可能是一个属性名，struts2 把1001 直接做为属性值 -->
    <s:param name="productId" value="1001"></s:param>
</s:url>
${url }

s:url 创建一个URL 字符串的
<s:url value="/getProduct" var="url" >
    <!-- 对于value 值会做自动的OGNL 解析 -->
    <s:param name="productId" value="productId"></s:param>
</s:url>
${url2 }

s:url 创建一个URL 字符串的
<s:url value="/getProduct" var="url" >
    <!-- 对于value 值会做自动的OGNL 解析，若不希望进行OGNL 解析，则使用单引号引起来 -->
    <s:param name="productId" value="'abcdefg'"></s:param>
</s:url>
${url3 }

<!-- 构建一个请求action 的地址 -->
<s:url action="testAction" namespace="/helloworld" method="save" var="url4" >
</s:url>
${url4 }

<!-- includeParams 是显示与不显示访问路径里面带的参数值，可选值有 all 对应get 和post 的方法都显示，get 对应只显示get 方法的参数值， null 不显示-->
<s:url action="testUrl" var="url5" includeParams="all">
</s:url>
${url5 }
```

#### set 标签

- set 标签用来在以下Map 对象里创建一个键值对：
  - ValueStack 值栈的ContextMap 值栈
  - Map 类型的session 对象
  - Map 类型的application 对象
  - Map 类型的request 对象
  - Map 类型的page 对象

#### push 标签

```jsp
s:push ：把一个对象在标签开始后压入到值栈中，标签结束时，弹出值栈
<%
    Person person = new Person();
    person.setName("abcd")
    request.setAttribute("person", person);
%>

<s:push value="#request.person">
    ${name } 打印出正确结果 abcd
</s:push>

--${name }-- 打印不出来
```

#### if, else elseif 标签

```jsp
<!-- 可以直接使用值栈中的属性 -->
s:if, s:else s:elseif

<s:if test="price > 1000">
    I7 处理器
</s:if>
<s:elseif test="price > 500">
    I5 处理器
</s:elseif>
<s:else>
    I3 处理器
</s:else>
```

#### iterator 标签

- iterator 标签用来遍历一个数组，Collection 或者一个Map，**并把这个可遍历对象里的每一个元素依次压入和弹出ValueStack 栈**
- 在开始执行时，iterator 标签会先把IteratorStatus 类的一个实例压入ContextMap，并在每次遍历循环时**更新它**，可以将一个指向IteratorStatus 对象的变量赋给status 属性

```jsp
<%
    List<Person> persons = new ArrayList<Person>();
    persons.add(new Person("AA", 10);
    persons.add(new Person("BB", 20);
    persons.add(new Person("CC", 30);
    persons.add(new Person("DD", 40);
    persons.add(new Person("EE", 50);

    request.setAttribute("persons", persons);
%>

<s:iterator value="#request.persons">
    ${name } - #{age } <!-- 挨个打印persons 内容，iterator 会依次把正在遍历的对象放到值栈的栈顶 -->
</s:iterator>

${name } - #{age } <!-- 打印不了，已经把persons 对象弹出了 -->
```

#### sort 标签

- sort 标签用来对一个可遍历对象里的元素进行排序
- 常用属性：
  - comparator
  - source
  - var

```jsp
<!-- s:sort 可以对集合总的元素进行排序-->
<%
    PersonComparator pc = new PersonComparator();
    request.setAttribute("comparator", pc);
%>

<s:sort comparator="#request.comparator" source="persons" var="person2"></s:sort>
<!-- var 的属性值是放到域对象的key 的值 -->
<s:iterator value="#attr.persons2">
    ${name } - #{age }
</s:iterator>
```

#### date 标签

- date 标签用来对Date 对象进行排版
- format 属性的值必须是java.text.SimpliDateFormat 类定义的日期 / 时间格式之一

```jsp
s:date 可以对Date 对象进行排版

<s:date name="#session.date" format="yyyy-MM-dd hh:mm:ss" var="date2"></s:date>
date: ${date2 }
```

#### a 标签

- a 标签将呈现为一个HTML 连接，这个标签可以接受HTML 语言中的a 元素所能接受的所有属性
- 可以对属性值进行OGNL 解析

```jsp
<s:iterator value="persons">
    <!-- 可以使用%{} 把属性包装起来，使其进行强制的OGNL 解析 -->
    <s:a href="getPerson.action?name=%{name}">${name }</s:a>
</s:iterator>
```

### 表单标签

- 表单标签将在HTML 文档里被呈现为一个表单元素
- 使用表单标签的优点:
  - **表单回显**
  - 对页面进行布局和排版
- 标签的属性可以被赋值为一个静态的值或**一个OGNL 表达式**，如果在赋值时使用了一个OGNL 表达式并把它用`%{}` 括起来，这个表达式将会被求值

#### form 标签

```jsp
<!--
表单标签：
1. 使用和html 的form 标签的感觉差不多
2. Struts2 的form 标签会生成一个table ， 以进行自动的排版
3. 可以对表单提交的值进行回显：从栈顶对象开始匹配属性，
    并把匹配的属性赋值到对应的标签的value 中，
    若栈顶对象没有对应的属性，则依次向下找相对应的属性，
    用处：我们在做修改的时候，需要先填上表单，
-->
<s:form>
    <s:hidden name="userId"></s:hidden>

    <s:textfield name="userName" lable="UserName"></s:textfield>
    <s:password name="password" lable="Password"></s:password>
    <s:textarea name="desc" lable="Desc"></s:textarea>

    <s:submit></s:submit>
</s:form>
```

#### checkbox 标签

- checkbox 标签将呈现为一个HTML 复选框元素，该复选框元素通常用于提交一个布尔值
- 当包含着一个复选框的表单被提交时，如果某个复选框被选中了，它的值将为true，这个复选框在HTTP 请求里增加一个请求参数，但**如果该复选框未被选中，在请求中就不会增加一个请求参数**
- checkbox 标签解决了这个局限性，它采取的办法是为单个复选框元素创建一个配对的不可见字段

#### Radio 标签

#### select 标签

#### checkbox-list 标签

#### optgroup 标签

**服务端需要使用集合类型，以保证能够被正常的回显**

#### 主题

主题：为了让所有的UI 标签能够产生同样的视觉效果而归集到一起的一组模板，**即风格相近的模板被打包为一个主题**

- simple：就没有默认加上的table tr td 等样式了
- xhtml: 默认主题
- css_xhtml
- ajax

修改方式：

1. 标签上写 theme 属性，可以是form 标签，也可以是form 标签的子标签

2. 在域对象上加上theme 属性`request.setAttribute("theme", "simple");`

3. 在 default.properties 文件里有此配置，默认就是 xhtml，如果修改可以在struts.xml 文件中对这个常量进行重新赋值

   ```properties
   ### Change this to reflect which path should be used for JSP control tag templates by default
   struts.ui.theme=xhtml
   ```

   修改当前Struts 应用的主题

   ```xml
   <!-- 修改当前Struts 应用的主题 -->
   <constant name="struts.ui.theme" value="simple"></constant>
   ```

### Params 拦截器与ModelDriven 拦截器

- **Parameters 拦截器将把表单字段映射到ValueStack 栈的栈顶对象的各个属性中。**如果某个字段在模型里没有匹配的属性，Param 拦截器将尝试ValueStack 栈中的下一个对象
- **默认情况下，栈顶对象是当前的Action**，于是，请求参数就给了action 的字段了

#### 把Action 和Model 隔开

- 在使用Struts 作为前端的企业级应用程序时，把Action 和Model 清晰地隔离开是有必要的，有些Action 类不代表任何Model 对象，它们的功能仅限于提供显示服务
- 如果Action 类实现了ModelDriven 接口，该拦截器将把ModelDriven 接口的getModel() 方法返回的对象置于栈顶
- 在Params 拦截器之前会先通过ModelDriven 拦截器，所以等到param 拦截器的时候，会把属性赋值给ModelDrien 拦截器的getModel 方法返回的对象

#### Action实现ModelDriven 接口后的运行流程

1. 先会执行ModelDrivenInterceptor  的intercept 方法

   ```java
   @Override
   public String intercept(ActionInvocation invocation) throws Exception {
       // 获取Action 对象：EmployeeAction 对象，此时Action 已经实现了ModelDriven 接口
       Object action = invocation.getAction();

       // 判断action 是否是ModelDriven 的实例
       if (action instanceof ModelDriven) {
           // 强制转换
           ModelDriven modelDriven = (ModelDriven) action;
           // 获取值栈
           ValueStack stack = invocation.getStack();
           //调用ModelDriven 的getModel 方法
           Object model = modelDriven.getModel();
           if (model != null) {
               // 把getModel 方法的返回值压入到值栈的栈顶
               stack.push(model);
           }
           if (refreshModelBeforeResult) {
               invocation.addPreResultListener(new ModelDrivenInterceptor.RefreshModelBeforeResult(modelDriven, model));
           }
       }
       return invocation.invoke();
   }
   ```

2. 执行ParametersInterceptor 的intercept 方法：把请求参数的值赋给栈顶对象对应的属性，如果栈顶对象没有对应的属性，则查询值栈中下一个对象对应的属性

3. 注意：getModel 方法不能提供以下实现，的确会返回一个Employee 对象到值栈的栈顶，但是当前的Action 的employee 成员变量却还是null

   ```java
   public Employee getModel(){
       return new Employee();
   }
   ```

#### ModelDriven 拦截器

- 当用户触发add 请求时，ModelDriven 拦截器将调用EmployeeAction 对象的getModel 方法，并把返回的模型（Employee 实例）压入到ValueStack 栈顶
- 接下来Parameters 拦截器将把表单字段映射到ValueStack 栈的栈顶对象的各个属性中，**因为此时ValueStack 栈的栈顶元素是刚被压入的模型（Employee）对象，所以该模型将被填充。**如果某个字段在模型里没有匹配的属性，Param 拦截器将尝试ValueStack 栈中的下一个对象

```java
public String edit() {
    //1.  获取传入的employeeId , employee.getEmployeeId()

    //2. 根据id 获取employee 对象

    Employee emp = dao.get(employee.getEmployeeId());

    //3. 把栈顶对象的属性装配好
    //   目前的employee 对象只有employeeid 属性，其他属性为null
    /*
     * Struts2 表单回显时：从值栈栈顶开始查找匹配的属性，若找到就添加到value 属性中
     */
    emp.setEmail();
    emp.setFirstName();

    //不能够进行表单的回显，因为经过重写赋值的employee 对象已经不再是栈顶对象了
//        employee = dao.get(employee.getEmployeeId());

    //手动的把从数据库中获取的Employee 对象放到值栈的栈顶
    //但此时值栈栈顶及第二个对象均为Employee 对象，不够完美
    ActionContext.getContext().getValueStack().push(dao.get(employee.getEmployeeId()));

    return "edit";
}

public Employee getModel() {
    // 判断是Create 还是Edit
    // 若为Create，则 employee = new Employee
    // 若为Edit，则 employee = dao.get(employeeid)
    // 判定标准为是否有employeeId 请求参数，若有该参数，则视为Edit，若没有该参数，则视为Create
    // 若通过employeeId 来判断，则需要在modelDriven 拦截器之前先执行一个param 拦截器
    // 而这个可以通过使用paramsPrepareParams 拦截器栈实现
    // 需要在struts.xml 文件中配置使用paramsPreparedParams 作为默认的拦截器栈

    if (employId == null) {
        employee = new Employee();
    } else {
        employee = dao.get(employeeId);
    }
}
```

解释：

- **如果页面传过来的employeeId，则在ModelDriven 拦截器调用后，在栈顶放一个从数据库中获取的Employee 对象，如果页面没有传过来employeeid 属性，则ModelDriven 拦截器会在栈顶放一个新的Employee 对象。**


- 一定要配置paramPrepareParamsStack 作为默认的拦截器栈，其特点是会先对Action 的属性进行赋值操作，再调用ModelDriven 的方法，再调用一遍param 拦截器，这个拦截器栈也在struts-default.xml 文件中配置的。

```xml
<package name="helloworld" extends="struts-default" namespace="/">
    <!-- 配置使用paramsPrepareParamsStack 作为默认的拦截器栈 -->
    <default-interceptor-ref name="paramsPrepareParamsStack"></default-interceptor-ref>
</package>
```

- 在Action 里面需要设置一个employeeId 属性，并写get set 方法，好让框架对这个页面传来的参数进行赋值

#### 使用paramsPrepareParamsStack 拦截器栈后的运行流程

- paramsPrepareParamsStack 和defaultStack 一样都是拦截器栈，而struts-default 包默认使用的是defaultStack

- 可以在Struts 的配置文件中通过以下方式修改使用的默认的拦截器栈

  ```xml
  <default-interceptor-ref name="paramsPrepareParamsStack"></default-interceptor-ref>
  ```

- paramsPrepareParamsStack 拦截器在于`params -> modelDriven -> params`，所以可以先把请求参数赋给Action 对应的属性，再根据赋给Action 的那个属性值决定压到值栈栈顶的对象，最后再为栈顶的对象的属性赋值

  对于edit 操作而言：

  1. 先为EmployeeAction 的employeeid 赋值
  2. 根据employeeid 从数据库中加载对应的对象，并放入到值栈的栈顶
  3. 再为栈顶对象的employeeid 赋值（实际上此时id 属性已经存在）
  4. 把栈顶对象的属性回显在表单中

- 关于回显：Struts2 表单标签会从值栈中获取对应的属性值并进行回显

- 存在的问题：

  - 在执行删除的时候employeeid 不为null，但getModel 方法却从数据库加载了一个对象
  - 执行查询全部信息时，也new 了一个Employee 对象，也不应该

#### Preparable 拦截器

- Struts2.0 中的modelDriven 拦截器负责把Action 类以外的一个对象压入到值栈栈顶
- 而prepare 拦截器负责准备为getModel() 方法准备model

**结论：**

- 若Action 实现了Preparable 接口，则Struts 将会执行prepareActionMethodName 方法，若prepareActionMethodName 方法不存在，则尝试执行prepareDoActionMethodName 方法，若都不存在，则都不执行
- 若PrepareInterceptor alwaysInvokePrepare 属性为false，则Struts 将不会调用实现了Preparable 接口的Action 的prepare() 方法
  - 可以为每一个ActionMethod 准备prepareActionMethodName 方法，而抛弃掉原来的prepare() 方法，
  - 将 PrepareInterceptor alwaysInvokePrepare 属性置为false，以避免Struts2 框架再调用prepare() 方法

```java
public String edit() {
    return "edit";
}

public void prepareEdit() {
    employee = dao.get(employeeId);
}

public String save() {
	dao.save(employee); // ModelDriven 拦截器会利用getModel 方法先在栈顶放一个Employee元素，在这里是这个action 的employee 属性，接下来param 拦截器会对栈顶的元素利用页面的参数进行赋值，所以等到走到action 的save 方法时，栈顶的元素已经是按照页面参数赋值好了的employee 对象，所以在save 方法里，只需直接把栈顶的元素保存下来就好了
    return "details";
}

public void prepareSave() {
    employee = new Employee(); // 1. prepare 拦截器会为getModel() 的返回值做准备(对某个Action 的属性进行赋值)  2. 针对不同的请求，实现了Preparable 接口的Action 会去分别尝试执行prepareMethod 方法，所以利用这一点，在不同的请求的时候，getModel 方法可以返回的对象是可以进行定制化地修改  3. 结果表现的就是，针对不同的请求，在栈顶放的对象可能不同，所以添加prepareMethod 方法来区分不同的请求在栈顶放的对象
}

public Employee getModel() {  // 1. 这个方法是ModelDriven 拦截器进行调用的，会在栈顶放一个元素  2. 如果Action 实现了Preparable 接口，那么需要提供prepare() 方法来为getModel() 方法的返回值进行赋值  3. Prepare 拦截器又会针对不同的请求调用不同的prepareMethod 方法(如prepareSave，prepareUpdate等)
    return employee:
}
```

- 如何在配置文件中为拦截器栈的属性赋值

  ```xml
  <!-- 配置使用paramsPrepareParamsStack 作为默认的拦截器栈 -->
  <!-- 修改PrepareInterceptor 拦截器的alwaysInvokePrepare 属性为false -->
  <interceptors>
      <interceptor-stack name="atguigustack">
          <interceptor-ref name="paramsPrepareParamsStack">
              <param name="prepare.alwaysInvokePrepare">false</param>
          </interceptor-ref>
      </interceptor-stack>
  </interceptors>
  <default-interceptor-ref name="atguigustack"/>
  <!--<default-interceptor-ref name="paramsPrepareParamsStack"></default-interceptor-ref>-->
  ```

#### Preparable 拦截器使用方法

1. 若Action 实现了Preparable 接口，则Action 方法需实现prepare() 方法
2. PrepareInterceptor 拦截器将调用prepare() 方法，**prepareActionMethodName() 方法或prepareDoActionMethodName() 方法**
3. PrepareInterceptor 拦截器根据**firstCallPrepareDo** 属性决定获取prepareActionMethodName、prepareDoActionMethodName 的顺序，默认情况下先获取prepareActionMethodName。如果没有该方法，就寻找prepareDoActionMethodName() 方法，如果找到对应的方法就调用该方法
4. PrepareInterceptor 拦截器会根据alwaysInvkePrepare 属性决定是否执行prepare() 方法

#### paramsPrepareParamsStack 拦截器栈

1. paramsPrepareParamsStack 从字面上理解来说，这个stack 的拦截器调用的顺序为：**首先params，然后prepare，接下来modelDriven，最后再params**
2. Struts2.0 的设计上要求modelDriven 在params 之前调用，而业务中prepare 要负责准备model，准备model 又需要参数，这就需要在prepare 之前运行params 拦截器设置相关参数，这个也就是创建paramsPrepareParamsStack 的原因
3. 流程如下：
   1. params 拦截器首先给Action 中的相关参数赋值，如id
   2. prepare 拦截器执行prepare 方法，prepare 方法中会根据参数，如id 去调用业务逻辑，设置model 对象
   3. modelDriven 拦截器将model 对象压入ValueStack，这里的model 对象就是在prepare 中创建的
   4. params 拦截器再将参数赋值给model 对象
   5. action 的业务逻辑执行

### 类型转换

 #### 概述

- 从一个HTML 表单到一个Action 对象，**类型转换是从字符串到非字符串**
  - HTTP 没有类型的概念，每一项表单输入只可能是一个字符串或一个字符串数组，在服务器端，必须把String 转换为特定的数据类型
- 在Struts2 中，把请求参数映射到action 属性的工作由**Parameters 拦截器**负责，它是默认的defaultStack 拦截器栈中的一员。**Parameters 拦截器可以自动完成字符串和基本数据类型之间的转换，其它类型的话，需要我们自己定义转换器**

#### 类型转换错误

- 如果类型转换失败：
  - **若Action 类没有实现ValidationAware 接口：**Struts 在遇到类型转换错误时仍会继续调用其Action 方法，就好像什么都没发生一样
  - **若Action 类实现ValidationAware 接口(很多时候要用此方法，可以继承ActionSupport 类)：**Struts 在遇到类型转换错误时将不会继续调用其Action 方法：Struts 将检查相关action 元素的声明是否包含着一个name=input 的result。如果有，Struts 将把控制权转交给那个result 元素；若没有input 结果，Struts 将抛出一个异常。

#### 类型转换错误消息的定制

- 作为默认的default 拦截器的一员，**ConversionError 拦截器负责添加与类型转换有关的出错消息（前提：Action 必须实现了ValidationAware 接口）和保存名请求参数的原始值 **

- 若字段标签使用的不是simple 主题，则非法输入字段将导致一条有着以下格式的出错消息：

  ```
  Invalid field value for field fieldName
  ```

- 覆盖默认的出错消息

  - **在对应的Action 类所在的包中新建ActionClassName.properties 文件，ClassName 即为包含着输入字段的Action 类的类名**
  - 在属性文件中添加如下键值对：`invalid.fieldvalue.fieldName=Custom error message`

- 定制出错消息的样式：

  - 每一条出错消息都被打包在一个HTML span 元素里，可以通过覆盖其行标为errorMessage 的那个css 样式来改变出错消息的格式

- 显示错误消息：如果是simple 主题，可以通过`<s:fielderror name="fieldname"></s:fielderror>` 标签显示错误消息

```jsp
<!--
问题1：如何覆盖默认的错误消息？
1. 在对应的Action 类所在的包中新建
    ActionClassName.properties 文件，ActionClassName 即为包含着输入字段的Action 类的类名
2. 在属性文件中添加如下键值对：invalid.fieldvalue.fieldName=xxx


问题2：如果是simple 主题，还会自动显示错误消息吗？如果不显示，怎么办？
1. 通过debug 标签，可知如果转换出错，则在值栈的Action （实现了ValidationAware 接口）对象中有一个fieldErrors 属性
   该属性的类型为Map<String, List<String>>，键：字段（属性名），值：错误消息组成的List。所以可以使用EL 或OGNL 的方式
   来显示错误消息： ${fieldErrors.age[0] }
2. 还可以使用s:fieldErrors 标签来显示，可以通过fieldName 属性显示描写字段的错误

问题3：若是simple 主题，且使用<s:fielderror fieldName="age"></s:fielderror> 来显示错误消息，
    则该消息在ul li span 中，如何去除ul, li, span 呢？

1. 在template.simple 下面的fielderror.ftl 定义的simple 主题下，s:fielderror 标签显示错误消息的格式，所以修改
    该配置文件即可，在src 下新建 template.simple 包，新建 fielderror.ftl 文件，把原生的fielderror.ftl 文件中的内容
    复制到新建的文件中，然后删除ul，li， span 部分即可

-->
<s:form action="testConversion" theme="simple">
Age: <s:textfield name="age" label="Age"></s:textfield>
${fieldErrors.age[0] }
<s:fielderror fieldName="age"></s:fielderror>

```

#### 类型转换与复杂属性配合使用

```java
/**
 * 1. Department 是模型，实际录入的Department，deptName 可以直接写到
 *    s:textfield 的name 属性中，那mgr 属性如何处理呢？
 *
 * Struts2 表单标签的name 值可以被赋为属性的属性：name=mgr.name, name=mgr.birth
 *
 * 2. mgr 中有一个Date 类型的birth 属性，Struts2 可以完成自动的类型转换吗？
 *
 * 全局的类型转换器可以正常工作
 */
public class Department {
    private String deptName;
    private Integer id;

    public String getDeptName() {
        return deptName;
    }

    public void setDeptName(String deptName) {
        this.deptName = deptName;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    private Manager mgr;

    public Manager getMgr() {
        return mgr;
    }

    public void setMgr(Manager mgr) {
        this.mgr = mgr;
    }
}
```

### Struts2 消息处理与国际化

#### 概述

- 在程序设计领域，把在无需改写源代码即可让开发出来的应用程序能够支持多种语言和数据格式的技术称为国际化
- 与国际化对应的是本地化，指让一个具备国际化支持的应用程序支持某个特定的地区
- Struts2 国际化是建立在Java 国际化基础上的：
  - 为不同国家/语言提供对应的消息资源文件
  - Struts2 框架会根据请求中包含的Locale 加载对应的资源文件
  - 通过程序代码取得该资源文件中指定key 对应的消息

#### 国际化的目标

1. 如何配置国际化资源文件
2. 如何在页面上和Action 类中访问国际化资源文件的value 值
3. 实现通过超链接切换语言

#### 如何配置国际化资源文件

- Action 范围资源文件：在Action 类文件所在的路径建立名为**ActionName_language_country.properties** 的文件

- 包范围资源文件：在包的根路径下建立文件名为**package_language_country.properties** 的属性文件，一旦建立，处于该包下的所有Action 都可以访问该资源文件。注意：**包范围资源文件的baseName 就是package**，不是Action 所在的包名

- **全局资源文件**

  - 命名方式：**basename_language_country.properties**

  - struts.xml

    ```xml
    <constant name="struts.custom.i18n.resources" value="baseName"></constant>
    ```

  - struts.properties

    ```
    strruts.custom.i18n.resources=baseName
    ```

- 临时指定资源文件：`<s:i18N ...>` 标签的name 属性指定临时的国际化资源文件

- 问题：国际化资源文件加载的顺序如何呢？离当前Action 较近的将被优先加载

#### 如何在页面上和Action 类中访问国际化资源文件的value 值

1. 在Action 类中，若Action 实现了TextProvider 接口，则可以调用其getText() 方法获取value 值
   - **通过继承ActionSupport 的方式**
2. 页面上可以使用`s:text` 标签 ；对于表单标签可以使用表单标签的key 属性值
   - 若有占位符，则可以使用s:text 标签的s:param 子标签来填充占位符
   - 可以利用标签和OGNL 表达式直接访问值栈中的属性值（对象栈和Map 栈）

#### 实现通过超链接切换语言

- Struts2 使用**i18n 拦截器** 处理国际化，并且将其注册在默认的拦截器中

- I18n 拦截器在执行Action 方法之前，自动查找请求中一个名为request_locale 的参数。如果该参数存在，拦截器就将其作为参数转换成Locale 对象，并将其设为用户默认的Locale(代表国家/ 语言环境)。并把其设置为session 的WW_TRANS_I18N_LOCALE 属性

- 若request 没有名为request_locale 的参数，则i18n 拦截器会从Session 中获取WW_TRANS_I18N_LOCALE  的属性值，若该值不为空，则将该属性值亩为浏览器的默认Locale

- 若Session 中的WW_TRANS_I18N_LOCALE 的属性值 为空，则从ActionContext 中获取Locale 对象

- 具体实现：只需要在超链接后面附着 request_locale 的请求参数，值是语言国家代码

  ```jsp
  <a href="testI18N.action?rquest_locale=en_US">English</a>
  <a href="testI18N.action?rquest_locale=zh_CN">中文</a>
  ```

- 注意：超链接必须是一个Struts2 的请求，即使i18n 拦截器工作！一定要经过各个拦截器才能工作，如果直接请求某个页面的话，是不经过拦截器，导致这个请求的国际化不正常

### Struts2 运行流程分析

1. 请求发送给StrutsPrepareAndExecuteFilter
2. StrutsPrepareAndExecuteFilter 询问ActionMapper：该请求是否是一个Struts2 请求（即是否返回一个非空的ActionMapping 对象）
3. 若ActionMapper 认为该请求是一个Struts2 请求，则StrutsPrepareAndExecuteFilter 把请求的处理交给ActionProxy
4. ActionProxy 通过Configuration Manager 询问框架的配置文件，确定需要调用的Action 类及Action 方法
5. ActionProxy 创建一个ActionInvocation 的实例，并进行初始化
6. ActionInvocation 实例在调用Action 的过程前后，涉及到相关拦截器(Intercepter) 的调用
7. Action 执行完毕，ActionInvocation 负责根据struts.xml 中的配置找到对应的返回结果。调用结果的execute 方法，渲染结果。在渲染的过程中可以使用Struts2 框架中的标签
8. 执行各个拦截器invocation.invoke() 之后的代码
9. 把结果发送到客户端

#### 相关的API

- ActionMapping：保存当前Action 映射状态的简单的类，通常需要指定name 和namespace，叫Action 映射
- ActionMapper：可能会返回一个ActionMapping，如果返回一个ActionMapping的话，说明这是一个Struts2 请求。如果返回一个null，说明没有一个action 跟它匹配，说明它是一个非struts2 的请求
- ActionProxy：这个是一个XWork 和Action 之间的中间层
- ActionInvocation：是Action 跟各种拦截器的执行状态，由ActionInvocation 去调用各个拦截器，以及执行响应结果，执行Action，再去调用各个拦截器

### Struts2 的输入验证 — 服务验证

- 一个健壮的web 应用程序必须确保用户输入是合法的、有效的
- Struts2 的输入验证 — 分为两种
  - **基于Xwork Validation Framework 的声明式验证：**Struts2 提供了一些基于XWork Validation Framework 的内建验证程序，使用这些验证程序不需要编程，只要在一个XML 文件里对验证程序应该如何工作作出声明就可以了，需要声明的内容包括：
    - 哪些字段需要进行验证
    - 使用什么验证规则
    - 在验证失败时应该把什么样的出错消息发送到浏览器端
  - 编程验证：通过编写代码来验证用户输入

#### Struts2 的验证

- 分为两种：
  - 声明式验证(推荐使用)
    - 对哪个Action 或Model 的哪个字段进行验证
    - 使用什么验证规则
    - 如果验证失败，转向哪一个页面，显示什么错误消息
  - 编程式验证

#### 声明式验证的helloworld

1. 先明确对哪一个Action 的哪一个字段进行验证：age

2. 编写配置文件：

   1. struts2/struts-2.3.34/apps/struts2-blank/WEB-INF/classes/example 下的Login-validation.xml 文件复制到和当前Action 所在的包下。

   2. 把该配置文件改为：**把Login 改为当前Action 的名字**

   3. 编写验证规则：参见文档 — struts2/struts-2.3.34/docs/docs/validation.html

   4. 在配置文件中可以定义错误消息

      ```xml
      <field name="age">
          <field-validator type="int">
              <param name="min">20</param>
              <param name="max">50</param>
              <!--<message>Age need to be between ${min} and ${max}</message>-->
              <message key="error.int"></message>
          </field-validator>
      </field>
      ```

   5. 该错误消息可以国际化吗？在定义的messge 的子标签里，可以定义key 值，这个key 值就对应着i18n properties 文件中的具体的国际化资源的信息，如上的代码中就是error.int 这个属性需要配置在i18n.properties 文件中：`error.int=Age need to be between ${min} and ${max}`。

3. 若验证失败，则转向哪个result。所以需要配置name=input 的result

   ```xml
   <action name="testValidation" class="com.struts2.model.TestValidationAction">
       <result>/success.jsp</result>
       <!-- 若验证失败转向的input -->
       <result name="input">/validation.jsp</result>
   </action>
   ```

4. 如何显示错误消息

   1. 若使用的是非simple 主题，Struts2 的form 标签，则自动显示错误消息
   2. 若使用的是simple 主题，则需要s:fielderror 标签或直接使用EL 表达式 或OGNL 表达式

#### 声明式验证ppt

- 声明式验证程序可以分为两类：
  - **字段验证：判断某个字段属性的输入是否有效**
  - **非字段验证：不只针对某个字段，而是针对多个字段的输入值之间的逻辑关系进行校验。例如，对再次输入密码的判断**
- 使用一个声明式验证程序需要3 个步骤：
  1. **确定哪些Action 字段需要验证**
  2. **编写一个难程序配置文件**，它的文件名必须是以下两种格式之一：
     - 若一个Action 类的多个action 使用同样的验证规则：**ActionClassName-validation.xml**
     - 若一个Action 类的多个action 使用不同的验证规则：**ActionClass-alias-validation.xml**，例如，UserAction-User_create-validation.xml
  3. **确定验证失败时的响应页面**：在struts.xml 文件中定义一个 result name="input" 的元素

#### 同一个Action 类对应多个action 请求使用不同规则 

- 注意：若一个Action 类可以应答多个action 请求，多个action 请求使用不同的验证规则，怎么办？
  - 为每一个不同的action 请求定义其对应的验证文件：ActionClassName-AliasName-validation.xml
  - 不带别名的配置文件：ActionClassName-validation.xml 中的验证规则依然会发生作用，可以把各个action 公有的验证规则配置在其中，但需要注意的是，只适用于某一个action 的请求的验证规则就不要再这里再配置了

#### 声明式验证框架的原理：

1. Struts2 默认的拦截器栈中提供了一个validation 拦截器

2. 每一个具体的验证规则都会对应具体的一个验证器，有一个配置文件把验证规则名称和验证器关联起来了，而实际上验证的是那个验证器，该文件位于com/opensymphony/xwork2/validator/validators/default.xml

   ```xml
   <validator name="required" class="com.opensymphony.xwork2.validator.validators.RequiredFieldValidator"/>
   ```

#### 短路验证

若对一个字段使用多个验证器，默认情况下会执行所有的验证，若希望前面的验证器验证没有通过，后面的就不再验证，可以使用短路验证

```xml
<field name="age">
    <!-- 设置短路验证：若当前验证没有通过，则不再进行下面的验证 -->
    <field-validator type="conversion" short-circuit="true">
        <message>Conversion age error</message>
    </field-validator>
    <field-validator type="int">
        <param name="min">20</param>
        <param name="max">50</param>
        <!--<message>Age need to be between ${min} and ${max}</message>-->
        <message key="error.int"></message>
    </field-validator>
</field>
```

#### 类型转换失败

若类型转换失败，默认情况下还会执行后面的拦截器，还会进行验证，可以通过修改ConversionErrorInterceptor 源代码的方式使当类型转换失败时，不再执行后续的验证拦截器，而直接返回input 的result

```java
Object action = invocation.getAction();
if (action instanceof ValidationAware) {
    ValidationAware va = (ValidationAware) action;
    if (va.hasActionErrors() || va.hasFieldErrors()) {
        return "input";
    }
}
```

#### 关于非字段验证：不是针对某个字段的验证

```xml
<!-- 测试非字段验证 -->
<validator type="expression">
    <param name="expression"><![CDATA[password==password2]]></param>
    <message>password is not the same as the password2</message>
</validator>
```

显示非字段验证的错误消息，使用s:actionerror 标签: `<s:actionerror/>`

#### 不同的字段使用同样的验证规则，而且使用同样的响应消息？

```properties
error.int=${getText(fieldName)} needs to be between ${min} and ${max}

age=\u5e74\u9f84
count=\6570\u91cf
```

#### 自定义验证器

- 自定义验证器必须实现Validator 接口
- ValidatorSupport 和FieldValidatorSupport 实现了Validator 接口
  - 若需要普通的验证程序，可以继承ValidatorSupport 类
  - 若需要字段验证程序，可以继承FieldValidatorSupport 类
  - 若验证程序需要接受一个输入参数，需要为这个参数增加一个相应的属性
- 注册验证程序：自定义验证器需要在类路径里的某个validators.xml 文件里注册；验证框架首先在根目录下找validators.xml 文件，没找到validators.xml 文件，验证框架将调用默认的验证设置，即default.xml 里面的配置信息

#### 自定义一个18 位身份证验证器

- 编写验证器类

```java
public class IDCardValidator extends FieldValidatorSupport{
    @Override
    public void validate(Object object) throws ValidationException {
        //1. 获取字段的名字和值
        String fieldName = getFieldName();
        Object value = this.getFieldValue(fieldName, object);

        //2. 验证
        IDCard idCard = new IDCard();
        boolean result = idCard.Verify(((String) value));

        //3. 若验证失败，则...
        if (!result) {
            addFieldError(fieldName, object);
        }
    }
}
```

- 在validators.xml 文件中进行注册

```xml
<validators>
    <validator name="idcard" class="com.struts2.model.IDCardValidator"/>
</validators>
```

- 在验证配置文件中使用

```xml
<!-- 自定义验证器 -->
<field name="idCard">
    <field-validator type="idcard">
        <message>It is not a IDCard!</message>
    </field-validator>
</field>
```

#### 编程式验证

- Struts2 提供了一个Validateable 接口，可以使Action 类实现这个接口以提供编程验证功能 
- ActionSupport 类已经实现了Validateable 接口

```java
@Override
public void validate() {
    if (name == null || name.trim().equalsIgnoreCase("")) {
        addFieldError("name", getText("name.null"));
    }
}
```

### 文件的上传

##### 表单的准备

要想使用HTML 表单上传一个或多个文件

- 需要把HTML 表单的enctype 属性设置为**multipart/form-data**
- 需要把HTML 的表单的method 属性设置为**post**
- 需要添加`<input type="file">` 字段

##### Struts2 的文件上传使用的是Commons FileUpload 组件

需要导入两个 jar 包：

- Commons-fileupload-1.3.jar
- Commons-io-2.0.1.jar

##### Struts2 进行文件上传需要使用FileUpload 拦截器

##### 基本的文件上传：直接在Action 中定义如下3个属性，并提供对应的get set方法

```java
// 文件对应的File 对象
private File [fileFieldName];
// 文件类型
private String [fileFieldName]ContentType;
// 文件名
private String [fileFieldName]FileName;
```

##### 使用IO 流进行文件的上传即可

```java
@Override
public String execute() throws Exception {
    System.out.println(ppt);
    System.out.println(pptContentType);
    System.out.println(pptFileName);
    ServletContext servletContext = ServletActionContext.getServletContext();
    String dir = servletContext.getRealPath("/file/" + pptFileName);
    System.out.println(dir);

    FileOutputStream out = new FileOutputStream(dir);
    FileInputStream in = new FileInputStream(ppt);

    byte[] buffer = new byte[1024];
    int len = 0;

    while ((len = in.read(buffer)) != -1) {
        out.write(buffer, 0, len);
    }

    out.close();
    in.close();
    
    return SUCCESS;
}
```

##### 一次传多个文件怎么办？

若传递多个文件，则上述的3 个属性可以改为List 类型！多个文件域的name 属性值需要一致

##### 可以对上传的文件进行限制吗？例如扩展名，内容类型，上传文件的大小？若可以，则若出错，显示什么错误消息？消息可以定制吗？

- 可以通过配置FileUploadInterceptor 拦截器的参数的方式来进行限制

```
maximumSize (optional) - 默认的最大值为2MB，上传的单个文件的最大值.
allowedTypes (optional) - 允许上传文件的类型，多个使用，分割
allowedExtensions (optional) - 允许的上传文件的扩展名，多个使用，分割
```

- 定制错误消息，可以在国际化资源文件中定义如下的消息：

```
struts.messages.error.uploading - 文件上传出错的消息
struts.messages.error.file.too.large - 文件超过最大值的消息
struts.messages.error.content.type.not.allowed - 文件内容类型不合法的消息
struts.messages.error.file.extension.not.allowed - 文件扩展名不合法的消息
```

注意：在org.apache.struts2 下的default.properties 文件中有对上传文件的总的大小的限制，可以使用常量的方式来修改该限制

### 文件的下载

#### 概述

在某些应用程序里，可能需要动态地把一个文件发送到用户的浏览器中，而这个文件的名字和存放位置在编程时是无法预知的

#### 文件的下载

1. Struts2 中使用 `type=”stream”` 的result 进行下载即可

2. 具体可以参考`struts2/struts-2.3.34/docs/docs/stream-result.html`

3. 可以为stream 的result 设定如下参数

   ```
   contentType - 结果类型
   contentLength - 下载的文件的长度
   contentDisposition - 设定Content-Disposition 响应头，该响应头指定响应是一个文件下载类型，一般取值为	 attachment;filename="document.pdf"
   inputName - 指定文件输入流的getter 定义的那个属性的名字，默认为inputStream
   bufferSize - 缓存的大小，默认1024
   allowCaching - 是否允许使用缓存
   contentCharSet - 指定下载的字符集
   ```

4. **以上参数可以在Action 中以getter 方法的方式提供！**

```java
@Override
public String execute() throws Exception {

    // 确定各个成员变量的值
    contentType = "text/html";
    contentDisposition = " attachment;filename=\"document.pdf\"";
    String fileName = null;
    ServletContext servletContext = ServletActionContext.getServletContext();
    fileName = servletContext.getRealPath("/files/hidden.html");
    inputStream = new FileInputStream(fileName);

    contentLength = inputStream.available();
    return SUCCESS;
}
```

struts.xml

```xml
<action name="testDownload"
        class="com.struts2.model.DownloadAction">
    <result type="stream">
        <param name="bufferSize">2048</param>
    </result>
</action>
```

### 表单重复提交

#### 概述

- 表单的重复提交：

  - 若刷新表单的页面，再提交表单不算重复提交
  - 在不刷新表单页面的前提下：
    - 多次点击提交按钮
    - 已经提交成功，按“回退” 之后，再点击“提交”
    - 在控制器响应页面的形式为转发情况下，若已经提交成功，然后点击“刷新(F5)”
- 重复提交的缺点：

  - 加重了服务器的负担
  - 可能导致错误操作

#### Struts2 解决表单的重复提交问题

1. 在s:form 中添加s:token 子标签
   - 生成一个隐藏域
   - 在session 添加一个属性值
   - 隐藏域的值和session 的值是一致的
2. 使用Token 或TokenSession 拦截器
   - 这两个拦截器均不在默认的拦截器栈中，所以需要手工配置一下
   - 若使用了Token 拦截器，则需要配置一个token.valid 的result
   - 若使用了TokenSession 拦截器，则不需要配置任何其他的result
3. Token VS TokenSession 拦截器
   - 都是解决表单重复提交问题
   - 使用Token 拦截器会转到token.valid 这个result
   - 使用TokenSession 拦截器还会响应那个目标页面，但不会执行tokenSession 的后续拦截器，就像什么都没发生过一样

### Struts2 拦截器

- 拦截器(Interceptor ) 是Struts2 的核心组成部分
- Struts2 的很多功能都是构建在拦截器基础之上的，例如文件的上传和下载、国际化、数据类型转换和数据校验等
- Struts2 拦截器在访问某个Action 方法之前或之后实施拦截
- Struts2 拦截器是可插拔的，拦截器是AOP 面向切面编程的一种实现
- 拦截器栈（Interceptor Stack）：将拦截器按一定的顺序联结成一条链，在访问被拦截的方法时，Struts2 拦截器链中的拦截器就会按其之前定义的顺序被依次调用

#### Interceptor 接口

- Struts2 会依次调用为某个Action 而注册的每一个拦截器的intercept  方法
- 每次调用intercept 方法时，Struts 会传递一个**ActionInvocation** 接口的实例
- **ActionInvocation：代表一个给定Action 的执行状态**。拦截器可以从该类的对象里获得与该Action 相关联的Action 对象和Result 对象。在完成拦截器自己的任务之后，拦截器将调用ActionInvocation 对象的invoke 方法前进到Action 处理流程的下一个环节
- **AbstractInterceptor**类实现了Interceptor 接口，并为init destroy 提供了一个空白的实现

#### 自定义拦截器

1. 具体步骤

   1. 定义一个拦截器的类

      - 可以实现Interceptor 接口
      - 可以继承AbstractInterceptor 抽象类

      ```java
      public class MyInterceptor extends AbstractInterceptor{
          @Override
          public String intercept(ActionInvocation actionInvocation) throws Exception {
              System.out.println("before");
              String result = actionInvocation.invoke();
              System.out.println("after");
              return result;
          }
      }
      ```

   2. 在struts.xml 文件中配置

      ```xml
      <package name="helloworld" extends="struts-default" namespace="/">
          <interceptors>
              <interceptor name="hello" class="com.struts2.model.MyInterceptor"></interceptor>
          </interceptors>
          <action name="testDownload"
              class="com.struts2.model.DownloadAction">
              <interceptor-ref name="hello"></interceptor-ref>
              <result type="stream">
                  <param name="bufferSize">2048</param>
              </result>
          </action>
      </package>
      ```

