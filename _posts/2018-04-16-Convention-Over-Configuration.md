# 约定优于配置[[编辑](https://zh.wikipedia.org/w/index.php?title=%E7%BA%A6%E5%AE%9A%E4%BC%98%E4%BA%8E%E9%85%8D%E7%BD%AE&action=edit&section=0&summary=/*%20%E9%A6%96%E6%AE%B5%20*/%20)]

维基百科，自由的百科全书

**约定优于配置**（**convention over configuration**）[[1\]](https://zh.wikipedia.org/wiki/%E7%BA%A6%E5%AE%9A%E4%BC%98%E4%BA%8E%E9%85%8D%E7%BD%AE#cite_note-1)，也称作**按约定编程**[[2\]](https://zh.wikipedia.org/wiki/%E7%BA%A6%E5%AE%9A%E4%BC%98%E4%BA%8E%E9%85%8D%E7%BD%AE#cite_note-2)，是一种软件[设计范式](https://zh.wikipedia.org/w/index.php?title=%E8%AE%BE%E8%AE%A1%E8%8C%83%E5%BC%8F&action=edit&redlink=1)，旨在减少[软件开发人员](https://zh.wikipedia.org/wiki/%E8%BD%AF%E4%BB%B6%E8%AE%BE%E8%AE%A1%E5%B8%88)需做决定的数量，获得简单的好处，而又不失灵活性。

本质是说，开发人员仅需规定应用中不符约定的部分。例如，如果模型中有个名为Sale的类，那么数据库中对应的表就会默认命名为sales。只有在偏离这一约定时，例如将该表命名为"products_sold"，才需写有关这个名字的配置。

如果您所用工具的约定与你的期待相符，便可省去配置；反之，你可以配置来达到你所期待的方式。

## 目录

 

 [[隐藏]()] 

- [1动机](https://zh.wikipedia.org/wiki/%E7%BA%A6%E5%AE%9A%E4%BC%98%E4%BA%8E%E9%85%8D%E7%BD%AE#%E5%8A%A8%E6%9C%BA)
- [2使用](https://zh.wikipedia.org/wiki/%E7%BA%A6%E5%AE%9A%E4%BC%98%E4%BA%8E%E9%85%8D%E7%BD%AE#%E4%BD%BF%E7%94%A8)
- [3参考文献](https://zh.wikipedia.org/wiki/%E7%BA%A6%E5%AE%9A%E4%BC%98%E4%BA%8E%E9%85%8D%E7%BD%AE#%E5%8F%82%E8%80%83%E6%96%87%E7%8C%AE)
- [4外部链接](https://zh.wikipedia.org/wiki/%E7%BA%A6%E5%AE%9A%E4%BC%98%E4%BA%8E%E9%85%8D%E7%BD%AE#%E5%A4%96%E9%83%A8%E9%93%BE%E6%8E%A5)
- [5参见](https://zh.wikipedia.org/wiki/%E7%BA%A6%E5%AE%9A%E4%BC%98%E4%BA%8E%E9%85%8D%E7%BD%AE#%E5%8F%82%E8%A7%81)

## 动机[[编辑](https://zh.wikipedia.org/w/index.php?title=%E7%BA%A6%E5%AE%9A%E4%BC%98%E4%BA%8E%E9%85%8D%E7%BD%AE&action=edit&section=1)]

设计不好的框架通常需要多个配置文件，每一个都有许多设置。这些配置文件为每一个项目提供信息说明从URL到将类映射到数据库表的各种信息。大量包含太多参数的配置文件通常是过度复杂的应用设计的指标（代码坏味道）[[3\]](https://zh.wikipedia.org/wiki/%E7%BA%A6%E5%AE%9A%E4%BC%98%E4%BA%8E%E9%85%8D%E7%BD%AE#cite_note-toomany-3)

例如，在知名的Java[对象关系映射](https://zh.wikipedia.org/wiki/%E5%AF%B9%E8%B1%A1%E5%85%B3%E7%B3%BB%E6%98%A0%E5%B0%84)框架[Hibernate](https://zh.wikipedia.org/wiki/Hibernate)的早期版本中，将类及其属性映射到数据库上需要是在XML文件中的描述，其中大部分信息都应能够按照约定得到，如将类映射到同名的[数据库](https://zh.wikipedia.org/wiki/%E6%95%B0%E6%8D%AE%E5%BA%93)表，将属性分别映射到表上的字段。后续的版本抛弃了[XML](https://zh.wikipedia.org/wiki/XML)配置文件，而是使用这些恰当的约定，对于不符合这些约定的情形，可以使用[Java 标注](https://zh.wikipedia.org/wiki/Java_%E6%A0%87%E6%B3%A8)来说明（参见下面提供的JavaBeans规范）。

## 使用[[编辑](https://zh.wikipedia.org/w/index.php?title=%E7%BA%A6%E5%AE%9A%E4%BC%98%E4%BA%8E%E9%85%8D%E7%BD%AE&action=edit&section=2)]

![img](https://upload.wikimedia.org/wikipedia/commons/thumb/c/cf/Maven_CoC.svg/200px-Maven_CoC.svg.png)

许多新的框架使用了约定优于配置的方法，包括：[Spring](https://zh.wikipedia.org/wiki/Spring_Framework)[[4\]](https://zh.wikipedia.org/wiki/%E7%BA%A6%E5%AE%9A%E4%BC%98%E4%BA%8E%E9%85%8D%E7%BD%AE#cite_note-spring-4)，[Ruby on Rails](https://zh.wikipedia.org/wiki/Ruby_on_Rails)[[5\]](https://zh.wikipedia.org/wiki/%E7%BA%A6%E5%AE%9A%E4%BC%98%E4%BA%8E%E9%85%8D%E7%BD%AE#cite_note-ruby-5)，Kohana PHP，[Grails](https://zh.wikipedia.org/w/index.php?title=Grails_(Framework)&action=edit&redlink=1)[[6\]](https://zh.wikipedia.org/wiki/%E7%BA%A6%E5%AE%9A%E4%BC%98%E4%BA%8E%E9%85%8D%E7%BD%AE#cite_note-grails-6)，[Grok](https://zh.wikipedia.org/w/index.php?title=Grok_(web_framework)&action=edit&redlink=1)，[Zend Framework](https://zh.wikipedia.org/wiki/Zend_Framework)，[CakePHP](https://zh.wikipedia.org/w/index.php?title=CakePHP&action=edit&redlink=1)，[symfony](https://zh.wikipedia.org/wiki/Symfony)，[Maven](https://zh.wikipedia.org/wiki/Apache_Maven)，[ASP.NET MVC](https://zh.wikipedia.org/wiki/ASP.NET_MVC)，[Web2py](https://zh.wikipedia.org/w/index.php?title=Web2py&action=edit&redlink=1)（MVC），[Apache Wicket](https://zh.wikipedia.org/w/index.php?title=Apache_Wicket&action=edit&redlink=1)。

这是一个古老的概念, 甚至在[Java](https://zh.wikipedia.org/wiki/Java)类库中也可以找出这一概念的踪迹。例如，[JavaBean](https://zh.wikipedia.org/wiki/JavaBean)规范非常多的依赖这一概念。下面摘录[JavaBean](https://zh.wikipedia.org/wiki/JavaBean)s 1.1版规范的一段:[[7\]](https://zh.wikipedia.org/wiki/%E7%BA%A6%E5%AE%9A%E4%BC%98%E4%BA%8E%E9%85%8D%E7%BD%AE#cite_note-7)

> 按照一般的规则，我们不希望造出一个奇怪的java.beans.everything类，其他类需要从该类派生。而是希望在运行时[JavaBean](https://zh.wikipedia.org/wiki/JavaBean)s为一般的对象提供缺省的行为特征，但是允许对象通过继承特定的java.beans.something接口来覆盖缺省的行为特征的一部分。[[8\]](https://zh.wikipedia.org/wiki/%E7%BA%A6%E5%AE%9A%E4%BC%98%E4%BA%8E%E9%85%8D%E7%BD%AE#cite_note-8)