---
title: Head First之工厂模式
excerpt: |
  Head First之工厂模式
category: 设计模式
feature_image: "https://picsum.photos/2560/600?image=872"
---
## 基础原则

- 当看到"new" 时就会想到“具体”
- 针对接口编程可以隔离掉系统以后可能发生的一大堆改变。为什么呢？如果代码是针对接口而写，那么通过多态，它可以与任何新类实现该接口。但是当代码使用大量的具体类时，等于是自找麻烦，因为一旦加入新的具体类，就必须改变代码。也就是说，你的代码并非“对修改关闭”。想用新的具体类型来扩展，必须重新打开它。
- 将实例化具体类的代码从应用中抽离、或者封闭起来，使他们不会干扰应用的其他部分。
- 我们的第一个原则用来处理改变，并帮助我们“找出会变化的方面，把它们从不变的部分分离出来。”
- 把创建对象的代码抽离，然后把这部分代码搬到另一个对象中，这个新对象只管如何创建Pizza。如果任何对象想要创建Pizza，找它就对了。
- 我们称这个新对象为“工厂”。



### 简单工厂

```
public class SimplePizzaFactory {
    public static Pizza createPizza(String type) {
        if (type  null || type.isEmpty()) {
            return null;
        }
        Pizza pizza = null;
        if (type.equalsIgnoreCase("cheese")) {
            pizza = new CheesePizza();
        }
        return pizza;
    }
}
```
- 工厂（factory）处理创建对象的细节。一旦有了SimplePizzaFactory，orderPizza()就变成此对象的客户。当需要比萨时，就叫比萨工厂做一个。现在orderPizza()方法只关心从工厂得到了一个比萨，而这个比萨实现了Pizza接口，所以它可以用prepare()、bake()、cut()、box()来分别进行准备、烘烤、切片、装盒。
- createXXX()，所有客户用此方法实例化新对象。

### Dumb Questions

- **问：这么做有什么好处？似乎只是把问题搬到另一个对象罢了，问题依然存在。**
  答：别忘了，SimplePizzaFactory可以有许多的客户。虽然目前只看到orderPizza()是它的客户，然而，可能还有PizzaShopMenu（比萨店菜单）类，会利用这个工厂类来取得比萨的价钱和描述。可能还有一个HomeDelivery（宅急送）类，会以与PizzaShop类不同的方式来处理Pizza。总而言之，SimplePizzaFactory可以有许多的客户。所以，把创建比萨的代码包装进一个类，当以后实现改变时，只需修改这个类即可。别忘了，我们也正要把具体实例化的过程，从客户的代码中删除！
- **问：我曾看过一个类似的设计方式，把工厂定义成一个静态的方法。这有什么差别？**
  答：利用静态方法定义一个简单的工厂，这是很常见的技巧，常被称为静态工厂。为何使用静态方法？因为不需要使用创建对象的方法来实例化对象。但请记住，这也有缺点，不能通过继承来改变创建方法的行为。
