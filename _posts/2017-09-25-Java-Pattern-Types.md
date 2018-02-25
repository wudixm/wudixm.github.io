---
title: 设计模式类型
excerpt: |
  设计模式类型
category: 设计模式
feature_image: "https://picsum.photos/2560/600?image=872"
---
3 version of proxy

ways the proxy used to control access

1. Remote proxy 
2. Virtual proxy
3. Protection proxy

### Bridge Pattern

- Two views — Long form view and Short form view
- Two Resources — Artist and Books
- If there is more resources or more views, then the composition of these two types of things can be massive, so use the bridge pattern

reduce **cadison product**

### Decorator Pattern

Decorator is a beverege and has a reference of beverege

### Structural Patterns 

- Decorator pattern
- Adapter pattern
- Facade pattern
- Proxy pattern provides a surrogate or placeholder for another object to control access to it
- Bridge pattern



### 创建型模式 — Creational Pattern

- 概念：创建型模式，就是创建对象的模式，抽象了实例化的过程。它帮助一个系统独立于如何创建、组合和表示它的那些对象。关注的是对象的创建，创建型模式将创建对象的过程进行了抽象，也可以理解为将创建对象的过程进行了封装，作为客户程序仅仅需要去使用对象，而不再关心创建对象过程中的逻辑
- 类：
  1. Factory Method
- 对象：
  1. Abstract Factory
  2. Builder
  3. Prototype
  4. Singleton

### 结构型模式 — Structural Pattern

- 概念：结构型模式是为解决怎样组装现有的类，设计他们的交互方式，从而达到实现一定的功能的目的。结构型模式包容了对很多问题的解决。例如：扩展性（外观、组成、代理、装饰）封装性（适配器，桥接）
- 类：
  1. Adapter(类)
- 对象
  1. Adapter(对象)
  2. Bridge
  3. Composite
  4. Decorator
  5. Facade
  6. Flyweight
  7. Proxy

### 行为型模式 — Behavioral Pattern

- 概念：行为型模式涉及到算法和对象间职责的分配，行为模式描述了对象和类的模式，以及它们之间的通信模式，行为型模式刻划了在程序运行时难以跟踪的复杂的控制流。

  可分为行为类模式和行为对象模式1.行为类模式使用继承机制在类间分派行为2.行为对象模式使用对象聚合来分配行为。一些行为对象模式描述了一组对等的对象怎样相互协作以完成其中任何一个对象都无法单独完成的任务。

- 类：
  1. Interpreter
  2. Template Method

- 对象：
  1. Chain of Responsibility
  2. Command
  3. Iterator
  4. Mediator
  5. Memento
  6. Observer
  7. State
  8. Strategy
  9. Visitor
