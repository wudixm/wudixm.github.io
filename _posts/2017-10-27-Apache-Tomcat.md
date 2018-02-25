---
title: 服务器与容器
excerpt: |
  服务器与容器
category: 
feature_image: "https://picsum.photos/2560/600?image=872"
---
# 1、什么是服务器

查看Apache的定义时，会发现，Apache为Web server，也就是Web服务器。
Web服务器是指能够为发出请求的浏览器提供文档的程序。服务器是一种被动程序，只有浏览器发出请求的时候才会响应。应用层使用的是HTTP协议。

# 2、Web服务器，Web容器和应用服务器的区别

Web Server，Web container和Application Server区别。
Web Server，Web服务器，同上面所说，

Web服务器是指能够为发出请求的浏览器提供文档的程序。服务器是一种被动程序，只有浏览器发出请求的时候才会响应。应用层使用的是HTTP协议。目前最主流的三个Web服务器是Apache Nginx IIS。
Web容器是一种服务器程序，在服务器端口就有一个提供相应服务的程序。所以现在知道为什么Tomcat有默认的端口——8080。一个服务器可以有多个容器。
如Tomcat，收到浏览器的请求之后还会解析Servlet，然后再把Servlet处理后的结果返回给浏览器。其实Tomcat既是Web服务器也是Web容器。为什么这样说，第三点会讲到。
而应用服务器，这里有个区分它与Web服务器的答案：
[What is the difference between application server and web server?](http://stackoverflow.com/questions/2469949/tomcat-is-web-server-or-application-server)
Web服务器设计服务于HTTP内容，应用服务器不只限于HTTP。Web服务器服务于静态内容，有插件支持动态语言，
应用服务器也具有Web服务器的这些东西，除此它还支持程序级的服务，如连接池，事务支持，信息服务等。
至于Tomcat是不是应用服务器，那就不好说了。

# 3、Apache和Tomcat的区别

Apache指的是Apache HTTP Server。
stackoverflow里面的这个问题虽然被关闭了，但是有一个评论写的博客挺好的。
[What is Tomcat? Is it a Web Server? What can Tomcat do? How is it different than Apache Web Server?](http://www.tugay.biz/2014/11/what-is-tomcat-is-it-web-server-what.html)
里面就讲到：
Tomcat是一个Servlet/Jsp容器，它同时也作为一个web服务器使用。
Tomcat = ( Web Server + Servlet container + JSP environment )，因为我们知道JSP也是转译为Servlet的，Tomcat接收请求之后，如果是JSP页面的话，Tomcat里面的JSP引擎可以将JSP转换为Servlet类。
从这里也可以看到，

其实Tomcat既是Web服务器也是Web容器。
如果是Servlet类的话，直接就是Servlet引擎加载Servlet类并且执行，然后以HTML的格式输出，再返回给浏览器。
而

Apache是设计服务于静态web页面的web服务器。如果是Apache来接收jsp请求，它根本就不知道这是什么东西，所以返回的并不是我们想要的结果，但是静态页面的话，如http://www.apache.com/index.html这样的请求的时候，Apache就直接将这个页面返回给请求的浏览器了。

# 4、都是服务器，那么我们经常还听到Nginx这样的服务器，和Apache的区别

Nginx是engine x的缩写，与Apache不同的是，Nginx是一款高性能的HTTP和反向代理服务器。
其实这块我是接触很少的，
但是之前见过他人的博客挂掉的时候出了这个Nginx，就一直很好奇这是什么东西，
还有一个师兄用Raspberry Pi加Nginx加PHP发了个Helloworld的时候，也让我感觉很是神奇。
