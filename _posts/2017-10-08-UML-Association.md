---
title: UML 关系
excerpt: |
  UML 关系
category: 设计模式
feature_image: "https://picsum.photos/2560/600?image=872"
---
### UML关系元素

UML模型是由各种事物以及这些事物之间的各种关系构成的。关系是指支配、协调各种模型元素存在并相互使用的规则。UML中主要包含四种关系，分别是**依赖、关联、泛化和实现**

```
package OOA.ClassDiagram;

/**
 * AggregationAssociation 是聚合关系，属于关联关系，在Java语言中，关联关系一般使用成员变量来实现
 * ，聚合关系会调用属性对象的方法
 * 聚合关系在UML的ClassDiagram中用带空心菱形头的实线表示，菱形头指向整体
 */
public class AggregationAssociation {
    Car car;

    public  void useCar() {
        car.run();
    }
}


class Car{
    public void run() {
        System.out.println("car run method");
    }
}

/**
 * Driver 是更强关联的组合关系，也属于关联关系，会调用属性的方法，并且在构造器里面初始化了属性
 * 组合关系在UML的ClassDiagram中用带实心菱形头的实线表示，菱形头指向整体
 */
class Driver{
    Car car;

    public Driver(Car car) {
        this.car = car;
    }
}

/**
 * 这里是依赖关系，依赖关系在Java语言中体现为局域变量、方法的形参，或者对静态方法的调用
 * 依赖关系在UML的ClassDiagram中用虚线带箭头的线表示
 */
class Person{
    //只会利用传入的参数进行调用
    public void useCar(Car car) {
        car.run();
    }
}

```
