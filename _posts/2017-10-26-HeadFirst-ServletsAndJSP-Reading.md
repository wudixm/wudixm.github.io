---
title: Servlet & JSP Head First
excerpt: |
  Servlet & JSP Head First
category: Books
feature_image: "https://picsum.photos/2560/600?image=872"
---
## 第1章 前言与概述-为什么使用Servlet & JSP ##

今，用户需要的网站应该是动态的、交互式的，而且应该能够由客户定制。通过后面的介绍，你会了解如何把Web网站变成真正的Web应用。

#### 你的Web服务器做些什么？

**Web服务器接收客户的请求，然后向客户返回一些结果。**

谈到“服务器”时，可能是指物理主机（硬件），也可能是指Web服务器应用（软件）。

#### Web客户做些什么？

**Web客户允许用户请求服务器上的某个资源，并且向用户显示请求的结果。**

谈到客户时，通常是指人类用户，或者浏览器应用，也可能二者都包括。

**浏览器就是一个软件，它知道怎么与服务器通信。浏览器还有一个重要的任务，这就是解释HTML代码，并把Web页面呈现给用户。**

#### 客户和服务器都知道HTML 和HTTP

##### HTML

当服务器对一个请求做出回答时，通常会向浏览器发送某种类型的内容，以便浏览器显示。服务器一般会向浏览器发送一级用HTML编写的指令。HTML（Hyper-Text Markup Language）HTML告诉浏览器怎样把内容呈现给用户。

##### HTTP

Web上客户和服务器之间的大多数会话都是使用HTTP协议完成的，HTTP协议支持简单的请求和响应会话。客户发送一个HTTP请求，服务器会用一个HTTP响应做出应答。关键是：**如果你是一个Web服务器，就必须讲HTTP。**

Web服务器向客户发送HTML页面时，就是使用HTTP发送的。

#### HTML速成指南

**HTML包含数十个标记，还有成百上千个标记属性。HTML的目标是拿到一个文本文档，然后为它增加一些标记，告诉浏览器如何对这个文本格式化。**

| 标记             | 描述                 |
| :------------- | ------------------ |
| \<\!\-\- \-\-> | 注释                 |
| <a>            | 锚点— 通常用来放一个超链接     |
| <align>        | 对内容左对齐、右对齐、居中或调整行距 |
| <body>         | 定义文本体的边界           |
| <br>           | 行分隔                |
| <center>       | 将内容居中              |
| <form>         | 定义一个表单（通常提供了输入域）   |
| <h1>           | 一级标题               |
| <head>         | 定义文档首部的边界          |
| <html>         | 定义HTML文档的边界        |
| <input type>   | 在表单中定义一个输入组件       |
| <p>            | 一个新段落              |
| <title>        | HTML文档的标题          |

#### 什么是HTTP协议？

HTTP是TCP/IP的上层协议。

TCP负责确保从一个网络节点向另一个网络节点发送的文件能作为一个完整的文件到达目的地，尽管在具体传送过程中这个文件可能会分解为小块传输。

IP是一个底层协议，负责把数据块（数据包）沿路移动/路由到目的地。

HTTP则是另一个网络协议，有一些Web特定的特性，不过它要依赖于TCP/IP从一处向另一处完整地传送请求和响应。**HTTP会话的结构是一个简单的请求/响应序列：浏览器发出请求，服务器做出响应。**

#### GET是一个简单的请求，POST可以发送用户数据

GET是最简单的HTTP方法，它的主要任务就是要求服务器获得一个资源并把资源发回来。

POST是一种更强大的请求，利用POST，可以请求某个东西，与此同时向服务器发送一些表单数据。

除了GET、POST，还有HEAD、TRACE、PUT、DELETE、OPTIONS和CONNECT。

#### 确实能用GET发送一点点数据

但是你可能不想这样做，使用POST而不是GET的原因如下：

1. GET中的总字符数有限的（取决于服务器）
2. 用GET发送的数据会追加到URL的后面，在浏览器地址栏中显示出来，所以你发送的数据会完全暴露出来。最好不要把口令或其他敏感数据作为GET请求的一部分发送。
3. 由于前面的第2点，如果你使用POST而不是GET，用户就不能对一个表单提交建立书签。

#### HTTP GET请求剖析

请求行 由HTTP方法，资源路径，参数（以？开头，&号分隔各个参数）和Web浏览器请求协议的版本

请求首部

例子：

```http
GET /select HTTP/1.1   //请求行，底下都是首部
Host: localhost:9999
Upgrade-Insecure-Requests: 1
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_6) AppleWebKit/603.3.8 (KHTML, like Gecko) Version/10.1.2 Safari/603.3.8
Accept-Language: zh-cn
Accept-Encoding: gzip, deflate
Connection: keep-alive
```

#### HTTP POST请求剖析

请求行 由HTTP方法，资源路径 Web浏览器请求协议的版本构成

请求首部

空行

消息体，有时也称为“负载（payload）”

```http
POST /a HTTP/1.1  //请求行
Host: localhost:9999 //首部开始
Connection: keep-alive
Content-Length: 12
Cache-Control: max-age=0
Origin: http://localhost:63342
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.113 Safari/537.36
Content-Type: application/x-www-form-urlencoded
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Referer: http://localhost:63342/Practise0824/PractiseTest/NetPractise/TestPost.html?_ijt=5cncijls26tvgbvmrpj63u4r91
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.8,en;q=0.6
Cookie: Idea-9008180b=6500439c-ed1a-45ce-b707-5532c778a05c //首部结束

color=dark&taste=malty   //消息体(负载)
```

#### HTTP响应剖析，什么是MIME类型？

HTTP响应包括一个首部和一个体。首部信息告诉浏览器使用了什么协议，请求是否成功，以及体中包括何种类型的内容。体包含了让浏览器显示的具体内容（例如，HTML）

**响应行由Web服务器使用的协议版本，响应的HTTP状态码，状态码的相应文本构成**

```http
HTTP/1.1 200 OK   //HTTP响应首部开始
Set-Cookie:JSESSIONID=0AA....
Content-Type:text/html
Content-Length:397
Date:Wed, 19 Nov 2003 03:25:40 GMT
Server:Apache-Coyote/1.1
Connection:close   //HTTP响应首部结束

<html>   //体中包含HTML或者其他需要显示的内容。
...
</html>
```

**Content-Type（内容类型）响应首部的值称为MIME类型。MIME类型告诉浏览器要接收的数据是什么类型，这样浏览器才能知道如何显示这些数据。**

#### URL

`http://` 协议：告诉服务器使用的是什么通信协议

`www.wickedlysmart.com` 服务器：所请求物理服务器的唯一名。这个名字映射到一个唯一的IP地址。IP地址是一个数字，形式“xxx.yyy.zzz.aaa”。在这里也可以指定一个IP地址而不是服务器名，不过服务器名更容易记。

`:80`端口：URL 的这一部分是可选的。一个服务器可以支持多个端口。一个服务器应用由端口标识。如果在URL中没有指定端口，默认端口则是端口80，而且很幸运，这正是Web服务器的默认端口。

`/beeradvice/select`路径：所请求资源在服务器上的路径。因为Web上大多数较早的服务器都采用Unix系统，因此还是用Unix语法来描述Web服务器的目录层次结构。

`beer1.html`资源：所请求内容的名字。可以是一个HTML页面，一个Servlet，一个图像，PDF，音乐，视频，或者服务器能提供的任何资源。这部分是可选的，如果URL中没有这一部分，大多数Web服务器都会默认地查找index.html。

还有可选的查询串：要记住，如果这是一个GET请求，额外的信息（参数）会追加到这个URL的最后，以一个问号开头，各个参数（名/值对）之间用&分隔。

#### 端口是一个16位数，标识服务器硬件上一个特定的软件程序

Internet Web(HTTP) 服务器软件在端口80上运行。这是一个标准，Telnet服务器在端口23上运行，FTP在21等等。可以把端口看作是唯一的标识符。端口表示与服务器硬件上运行的一个特定软件的逻辑连接。仅此而已。别指望能在你的硬件主机里找到一个TCP端口。一方面，服务器上有65535个端口，另一方面，端口并不表示一个可以插入物理设备的位置。它们只是表示服务器应用的逻辑数而已。

#### Web服务器自己不做的两件事

1. 动态内容
2. 在服务器上保存数据

#### 在servlet中创建一个动态Web页面

**必须把整个HTML打印到一个输出流（这实际上是要打印的HTTP响应流的一部分）**。实际上，在servlet的out.println()里格式化HTML确实不太好。

#### JSP页面

JSP页面就像是一个HTML页面，只不过可以把Java和与Java有关的东西放在这个页面中。

#### HTML中引入Java，这就是JSP

把HTML放在println()语句中的做法很糟糕，也很容易出错，不过JSP可以解决这个问题，**它允许把Java放在HTML页面中，而不是把HTML放到Java代码中。**

## 第2章 高层概述 - Web应用体系结构##

**Servlet也需要帮助。请求到来时，必须有人实例化Servlet，或者至少要建立一个新的线程处理这个请求，必须有人调用servlet的doPost或者doGet方法。另外，那些方法还有一些重要的参数— HTTP请求和HTTP响应对象，所以必须有人把请求和响应交给servlet。还得有人管理servlet的生与死以及servlet的资源。这个“人”就是Web容器。**在这一章中，会介绍Web应用在容器中怎么运行，我们还会第一次用模型— 视图— 控制器（Model View Controller, MVC）设计模式分析Web应用的结构。

#### 容器能提供什么？

- 通信支持
  - 让servlet与Web服务器对话，无需建立自己的ServerSocket、监听端口、创建流等，允许我们只关注如何在servlet中实现业务逻辑
- 生命周期管理
  - 容器控制着servlet 的生与死。**负责加载类、实例化和初始化servlet、调用servlet方法，并使servlet实例被gc。**
- 多线程支持
  - **容器会自动地为它接收的每个servlet请求创建一个新的Java线程。**
- 声明方式实现安全
  - 利用容器，可以使用XML部署描述文件来配置（和修改）安全性，不必硬编码到servlet（或其他）类中。
- JSP支持
  - **负责把JSP代码翻译成真正的Java。**

#### servlet 可以有3 个名字

1. 客户知道的URL名，指向一个servlet
2. 部署人员知道的秘密内部名，web.xml 中的\<servlet-name\>
3. 实际的文件名，如MyServlet.class。

建立servlet名的映射，有助于改善应用的灵活性和安全性。

- 通过映射servlet名，而不是把真实的文件和路径名写入代码，提供了很大的灵活性，使你能轻松地移动文件，而不用担心遭遇维护噩梦。
- 再考虑一下安全性，你真的希望客户对你的服务器上的目录结构了如指掌吗？

#### 部署描述文件

```xml
<servlet>  <!--  内部名映射到完全限定类名-->
    <servlet-name>ch1</servlet-name>
    <servlet-class>com.example.web.Test</servlet-class>
</servlet>

<servlet-mapping> <!-- 内部名映射到公共URL名 -->
    <servlet-name>ch1</servlet-name>
    <url-pattern>/select.do</url-pattern>
</servlet-mapping>
```

**部署文件除了把URL映射到实际的servlet，还可以对Web应用的其他方面进行定制，包括安全角色、错误页面、标记库、初始配置信息等，如果是一个完整的J2EE服务器，甚至可以声明你要访问特定的企业Javabean.**

#### 采用MVC

**采用MVC，不仅要求业务逻辑与表示分离，实际上，业务逻辑甚至根本不知道有表示的存在。**

MVC的关键是，业务逻辑要与表示分享，而且要在二者之间放上别的东西，这样业务逻辑本身就能作为一个可重用的Java类存在，它根本不用对视图有任何了解。

如果把业务逻辑混到了servlet里，就说明无法将业务逻辑用于其他视图（如Swing GUI）。所以应该把业务逻辑放到一个能重用的独立Java类中才对。

如果你对MVC已经很熟悉，就会知道MVC并不是servlet 和JSP 所特有的，**业务逻辑与表示要清晰地分离，不论在哪一种类型的应用中，这都是不变的真理。**对于Web应用来说，这一点则显得尤其重要，因为你不能保证别人只会从Web访问你的业务逻辑。

```
Model：模型，普通Java类，包含具体的业务逻辑和状态，换句话说，模型知道用什么规则来得到和更新状态，系统中只有这部分与数据库通信。
Controller：控制器，Servlet，从请求获得用户输入，并明确这些输入对模型有什么影响。告诉模型自行更新，并且让视图(JSP)能得到新的模型状态。
View：视图，JSP，负责表示方面。它从控制器得到模型的状态(不过不是直接得到：控制器会把模型数据放到视图能找到的一个地方)。另外视图还要获得用户输入，并交给控制器。
```

#### 第2章要点

- 容器为Web应用提供了通信支持，生命周期管理、多线程支持、声明方式安全，以及JSP支持，这样你就专心开发自己的业务逻辑
- 容器创建一个请求对象和一个响应对象，servlet（或Web应用的其他部分）可以用这些对象得到有关请求的信息，并把信息发送给客户
- 典型的servlet是一个扩展了HttpServletRequest 的类，而且覆盖了一个或者多个服务方法（doPost, doGet），分别对应于浏览器调用的HTTP方法
- 部署人员可以把servlet 类映射到一个URL，这样客户可以使用这个URL来请求该servlet。部署名可以与实际的类文件名完全不同

## 第3章 MVC 实战 - MVC 迷你教程 ##

#### 步骤概述

1. 分析**用户的视图**（也就是浏览器要显示的东西），以及高层**体系结构**
2. 创建用于开发这个项目的**开发环境**
3. 创建用于部署这个项目的**部署环境**
4. 对Web应用的各个组件完成迭代式开发和测试（没错，与其说这是一个步骤，不如说是一个策略）

#### 开发环境

我们要使用一个标准的包结构，这样才能得到包的所有好处：

1. 项目管理
2. 命名空间管理
3. 可以移植性和可重用性

#### 部署环境

web.xml 文件必须放到WEB-INF 中。

#### HTML页面

客户根本不能直接访问servlet文件，所以你不能直接使用类文件名，我们会**使用XML部署描述文件(web.xml) 把客户请求的资源（“selectBeer.do”）映射到一个实际的servlet 类文件，**当指向“selectBeer.do” 的请求到达时，容器就会使用这个类文件。对目前来说，可以把“.do” 扩展名当作是逻辑名的一部分（而不是一个实际的文件类型）。在本书后面，你会了解到在servlet 映射中，还可以用其他的方法使用扩展名（实际扩展名或虚构 / 逻辑扩展名）。

#### 回顾已经完成的MVC应用

1. 浏览器把请求数据发送给容器
2. 容器根据URL找到正确的servlet， 并把请求传递给这个servlet
3. servlet 调用Model 获得帮助
4. **这个Model类返回一个回答，servlet 把这个回答增加到请求对象**
5. **servlet 把请求转发给JSP**
6. **JSP 从请求对象得到回答**
7. **JSP为容器生成一个页面**
8. 容器把这个页面返回给用户

#### 调用JSP

我们要把servlet修改为“调用”JSP来生成输出（视图），容器提供了一种称为**“请求分派” 的机制，允许容器管理的一个组件调用另一个组件。**我闪就会使用这种机制，servlet从模型得到信息，把它保存在请求对象中，然后**把请求分派给JSP**。

```java
@Override
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

    String beerType = req.getParameter("beerType"); //获取浏览器传过来的参数
    List<String> beerBrand = BeerExport.getBeerBrand(beerType); //利用模型Model，获得结果
    
    req.setAttribute("beerBrand", beerBrand); //把模型返回的信息保存在请求对象中

    RequestDispatcher requestDispatcher = req.getRequestDispatcher("result.jsp"); //请求分派给JSP    
    requestDispatcher.forward(req, resp);
}
```

## 第4章 请求和响应 - 作为Servlet##

servlet 的实例只有一个，如下，打印servlet的hashcode值，一直是一个。

```java
private static  int count = 0; //count是类的静态的变量，每次一个请求会加一
@Override
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    count++;
    logger.info("count = " + count + " this object = " + this.hashCode()); //打印此servlet实例的hashCode值
    String beerType = req.getParameter("beerType");
    List<String> beerBrand = BeerExport.getBeerBrand(beerType);
    req.setAttribute("beerBrand", beerBrand);
}
```

打印结果：

```
$ tail -f logs/mylog.log
16:12:58.784 [http-bio-8080-exec-1] INFO  com.example.web.Test - init fsa
16:12:58.788 [http-bio-8080-exec-1] INFO  com.example.web.Test - count = 1 this object = 886273382
16:13:42.005 [http-bio-8080-exec-7] INFO  com.example.web.Test - count = 2 this object = 886273382
16:14:43.770 [http-bio-8080-exec-5] INFO  com.example.web.Test - count = 3 this object = 886273382
```

结论：count值在增加，每个请求都走一遍doPost方法，但是，servlet实例只有一个。

#### Servlet 受容器的控制

在第2章，我们了解到，容器全盘控制着servlet 的一生，它会创建请求和响应对象，为servlet 创建一个新线程或分配一个线程，另外调用servlet  的service 方法，并传递请求和响应对象的引用作为参数。

下面是一个简单的回顾：

1. 用户点击一个链接，链接的URL 指向一个servlet。

2. 容器“看出”这个请求指向一个servlet，所以**容器创建两个对象：HttpServletResponse, HttpServletRequest**

3. 容器根据请求中的URL 查找正确的servlet，**为这个请求创建或分配一个线程，并调用servlet 的service() 方法（传递请求和响应对象作为参数）

4. **service() 方法根据客户发出的HTTP 方法（GET、POST等），确定要调用哪个service 方法**

   客户发出了一个HTTP GET 请求，所以service() 方法会调用servlet 的doGet() 方法，并传递请求和响应对象作为参数

5. **servlet 使用响应对象将响应写至客户。响应通过容器传回。**

6. **service() 方法结束，所以线程要么撤销，要么返回到容器管理的一个线程池。**请求和响应对象引用已经出了作用域，所以这些对象已经没有意义(可以垃圾回收)。客户得到响应

#### Servlet 的一生还不只这些

servlet 的生命周期很简单：**只有一个主要的状态——初始化。如果servlet 没有初始化，则要么正在初始化（运行其构造器或init() 方法），正在撤销（运行其destroy() 方法），要么就是还不存在**。

1. Servlet 类的无参数构造函数运行（你不要自己写构造函数，只需使用编译器提供的默认构造函数即可）
2. 在servlet 的一生中只调用一次init 方法，而且必须在容器调用service() 之前完成
3. service() 方法，处理客户请求，doGet，doPost 等，每个请求都在一个单独的线程中运行
4. destroy() 方法，容器调用这个方法，从而在servlet 被杀死（也就是说，可以垃圾回收）之前有机会清理资源。与init 一样，destroy 也只能调用一次

#### 三大重要时刻

- init
  - 何时调用 — servlet 实例创建后，并在**servlet 能为客户请求提供服务之前，容器要对servlet 调用 init()**
  - 作用 — 使你在servlet 处理客户请求之前有机会对其初始化
  - 是否覆盖？ — **有可能，如果有初始化代码（如得到一个数据库连接，或向其他对象注册），就要覆盖servlet 类中的init() 方法

- service

  - 何时调用 — **第一个客户请求到来时，容器会开始一个新线程，或者从线程池分配一个线程，并调用servlet 的service 方法**
  - 作用 — **这个方法会查看请求，确定HTTP 方法，并在servlet 上调用对应的方法，如doGet、doPost 等**
  - 是否覆盖 — **不，不太可能，不应该覆盖service() 方法。**你的任务是覆盖doGet 和 / 或 doPost 方法，而由HttpServlet 中的service() 实现来考虑该调用哪一个方法。

- doGet() 和 / 或 doPost

  - 何时调用 — service() 方法根据请求的HTTP 方法（GET、POST等）来调用doGet或doPost
  - 作用 — 要在这里写你的代码！你的Web 应用想要做什么，就要由这个方法负责。当然也可以调用其他对象的其他方法，不过都要从这里开始
  - 是否覆盖 — **至少要覆盖其中之一，doGet 或doPost，不论覆盖哪一个，都能告诉容器你支持什么类型的请求。**例如，如果没有覆盖doPost()，就是在告诉容器这个servlet 不支持HTTP POST 请求。

- 容器会调用serlvet 的inti() 方法，但是如果我不覆盖init() 方法，就会运行GenericServlet 中的init() 方法。然后到来一个请求时，容器会开始或分配一个线程，调用service() 方法，这个方法不用覆盖，所以会运行HttpServlet 的service() 方法，HttpServlet service() 方法再调用我覆盖的doGet 或doPost。这样一来，每次运行我的doGet 或doPost 时，它都在一个单独的方法栈中

#### 每个请求都在一个单独的线程中运行

**容器运行多个线程来处理对一个servlet 的多个请求。**

对应每个客户请求，会生成一对新的请求和响应对象。

#### 加载和初始化

- 容器找到servlet 类文件时，servlet 的生命开始。这基本上都是在容器官运时发生（例如，运行Tomcat时）。容器启动时，它会寻找已经部署的Web 应用，然后开始搜索servlet 类文件
- 第二步是加载类，这可能在容器启动时发生，也可能在第一个客户使用时进行。你的容器可能允许你来完成类加载，也可能会在它希望的任何时刻加载类。不论你的容器是早就准备好servlet 还是在第一个客户需要时才即时地加载类，在servlet 没有完全初始化之前绝不能运行servlet 的service() 方法
- **init() 总是在第一个service() 调用之前完成。**

#### Servlet 初始化：对象何时成为一个servlet

servlet 从“不存在” 状态迁移到“初始化” 状态（这意味着已经准备好为客户请求提供服务），首先是从构造函数开始。但是构造函数只是使之成为一个对象，而不是一个servlet。要想成为一个servlet，对象必须具备一些“servlet 特性（servletness）”。

对象成为一个servlet 时，它会得到servlet 该有的所有特权，比如**能够使用ServletContext 引用从容器得到信息**

**不要在servlet 的构造函数中放任何东西！什么都可以放在init() 里。**

#### ServletConfig 对象与ServletContext 对象

- ServletConfig 对象
  - 每个servlet 都有一个ServletConfig 对象
  - 用于向servlet 传递部署时信息（例如数据库或企业bean 的查找名），而你不想把这个信息硬编码写到servlet 中
  - 用于访问ServletContext
  - 参数在部署描述文件中配置
- ServletContext
  - 每个Web 应用有一个ServletContext（应该叫AppContext 才对）
  - 用于访问Web 应用参数（也在部署描述文件中配置）
  - 相当于一种应用公告栏，可以在这里放置消息（称为属性），应用的其他部分可以访问这些消息
  - 用于得到服务器信息，包括容器名和窗口版本及所支持API 的版本等

#### servlet 的真正任务是处理请求，这才是servlet 存在的意义

ServletConfig 和ServletContext 的存在只是**为了支持servlet 的真正任务：处理客户请求！**所以在介绍上下文对象和配置对象如何帮助你完成这个任务之前，先了解一下请求和响应的基础知识

#### HTTP 请求方法确定究竟运行doGet() 还是doPost()

HTTP 1.1 里面有8 个方法，除了GET 和POST 之外，确实还有其他一些HTTP 1.1 方法，这包括**HEAD, TRACE, OPTIONS, PUT, DELETE 和CONNECT。**

对于这8 个方法，除了一个方法外，其余的在HttpServlet 类中都有一个匹配的doXXX() 方法，所以不仅是doGet() 和doPost()，还有doOptions()、doHead()、doTrace()、doPut() 和doDelete()。**servlet API 中没有处理doConnect() 的机制，所以HttpServlet 里没有doConnect()**。

| 方法      | 描述                                       |
| ------- | ---------------------------------------- |
| GET     | 要求得到所请求的URL 上的一个东西（资源 / 文件）              |
| POST    | 要求服务器接受附加到请求的体信息，并提供所请求URL 上的一个东西。这像一个扩展的GET |
| HEAD    | 只要求得到GET 返回结果的首部部分。所以这有点像GET，但是响应中没有体。它能提供所请求URL 的有关信息，但是不会真正返回实际的那个东西 |
| TRACE   | 要求请求消息回送，这样客户能看到另一端上接收到了什么，以便测试或排错       |
| PUT     | 指出要把所包含的偗（体）放在请求的URL 上                   |
| DELETE  | 指出删除所请求URL 上的一个东西（资源 / 文件）               |
| OPTIONS | 要求得到一个HTTP 方法列表，所请求URL 上的东西可以对这些HTTP 方法做出响应） |
| CONNECT | 要求连接以便建立隧道                               |

#### GET 和POST 的区别

**POST 有一个体**。GET 和POST 都能发送参数，但是利用GET 的话，对参数数据有限制，参数数据只能是放在请求行的内容。

- **GET**
  - **请求行**
    - **由HTTP 方法，Web 服务器上资源的路径，Web 浏览器所请求的协议的版本构成，在一个GET 请求中，参数(如果有)要追加到请求URL 的后面**
  - **请求首部**
  - **没有请求体，只有首部信息**
- **请求行**
  - **由HTTP 方法，路径，协议构成，路径中没有请求参数**
  - **请求首部**
  - **消息体，有时称为负载，参数会显示在这里，因此不会像使用GET 那样受限制，而GET 必须把参数放在请求行中**

#### 另一个问题是安全性

- **GET 请求可以建立书签，POST 请求则不能**。
- 除了数据的大小、安全性和书签问题之外，GET 和POST 之间还有一个重要的差别，这就是这些方法要如何使用。GET 用于得到某些东西，仅此而已，只是简单的获取。**使用POST 时，要这样考虑：这是一个更新。**要把它认为是使用POST 体的数据来修改服务器上的某些东西

#### 幂等

**HTTP 1.1 规范声明GET、HEAD 和PUT 是幂等的，不过你也可以自己写一个非幂等的doGet() 方法（但不应该这样做）。POST 在HTTP 1.1 规范中不认为是幂等的。**

**幂等是很好的。这说明，你可以一遍一遍反复佬同一件事情，而不会有预料不到的副作用。**

#### POST 不是幂等的

**POST 不是幂等的，POST 体中的提交数据可能用于不可逆转的事务。所以使用doPost() 功能时必须特别小心。**

form 中默认的方法是GET。

浏览器会创建请求，这个请求也只有服务器才会看到。

**在Servlet 类中，利用传过来的HttpServletRequest 的getParameter("color") 方法来获取参数，color 一定要与表单中的参数名匹配**。

如果是表单里面有多个参数，那么参数之间会用“&” 符号进行分割。

#### 复选框的处理

有些表单输入类型可以有多个值，如复选框。这说明单个参数可能有多个值，主要取决于用户选中了多少个复选框。

```html
<form action="select.do" method="post">
    <input type="checkbox" name="sizes" value="12oz">
    <input type="checkbox" name="sizes" value="16oz">
    <input type="checkbox" name="sizes" value="22oz">
    <input type="submit">
</form>
```

如果你想看到数组的内容，

```java
String[] sizes = req.getParameterValues("sizes");
for (int i = 0; i < sizes.length; i++) {
    System.out.println(sizes[i]);
}
```

#### 除了参数，我还能从请求对象得到什么？

```java
// 客户的平台和浏览器信息
String client = req.getHeader("User-Agent");
// 与请求相关的cookie
Cookie[] cookies = req.getCookies();
// 与客户相关的会话 session
HttpSession session = req.getSession();
// 请求的HTTP 方法
String method = req.getMethod();
// 请求的输入流
ServletInputStream input = req.getInputStream();
```

##### 问题：

- 为什么需要从请求得到一个InputStream呢？
  - 如果是一个GET 请求，那么除了首部信息以外就没有别的什么了，我们不操心请求体。不过POST 请求不同，它有体信息。**大多数情况下，我们关心的只是用request.getParameter() 抽出参数值，但是这些值可能很大，还有可能请求体中包含要处理的文本或二进制内容。在这种情况下，可以使用getReader() 或getInputStream() 方法。这些流只包含HTTP 请求的体而不包含首部**
- getHeader() 方法和getIntHeader() 有什么区别？
  - 如果你已经知道首部的值表示一个整数，就可以使用getIntHeader 作为便利方法，这样就不用再多做一步把String 转换为一个int 的操作。
