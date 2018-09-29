---
title: Forward 和 sendRedirect 区别
excerpt: |
  Forward 和 sendRedirect 区别
category: 网络
feature_image: "https://picsum.photos/2560/600?image=872"
---

[原文](https://blog.csdn.net/zsy_gemini/article/details/2840665)

**使用时response.sendRedirect()，如果是跨域跳转，则session会丢失，否则不会。**



**例如：在本地机上有项目名问web1和web2的两个项目同时运行，从web1跳转到web2当然是跨域跳转，session丢失。但如果是从地址<http://localhost:8080/web1>跳转到<http://127.0.0.1:8080/web2>也同样会丢失session。**

**在web1的servlet中执行response.sendRedirect("/web1/page2.jsp")是域内跳转，session不会丢失，但保存在request中的属性值在page2.jsp中无法取到，也就是说在page2.jsp中通过request.getAttribute()的方法无法取得值，所以只能通过把参数写到url中的方式传递参数，例如：response.sendRedirect("/web1/page2.jsp?action=select").**



因为forward()就是转发。sendRedirect就是重定向。两者最大的区别就是一个是：
forward()执行后还是在同一个requestrequest范围，而sendRedirect执行之后就不在同一个request范围里面。

 

RequestDispatcher.forward()是在服务器端起作用,当使用forward()时,Servletengine传递HTTP请求从当前的Servlet or JSP到另外一个Servlet,JSP 或普通HTML文件,也即你的form提交至a.jsp,在a.jsp用到了forward()重定向至b.jsp,此时form提交的所有信息在 b.jsp都可以获得,参数自动传递.

 

response.sendRedirect()是在用户的浏览器端工作,sendRedirect()可以带参数传递,比如servlet?name=frank传至下个页面,同时它可以重定向至不同的主机上,且在浏览器地址栏上会出现重定向页面的URL.
