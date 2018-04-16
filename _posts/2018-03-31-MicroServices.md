## Microservices##

[clickhere](https://www.youtube.com/watch?v=PY9xSykods4)

### what is a Microservices?

**A microservices is basically a self contained process that provides a unique business capability. Different microservices act in concert as one large system, but behind the scenes, it`s microservices all the way.**

Take Amazon.com for instance.All we see is the website.Behind the scenes, Amazon has a service for accepting orders, a service for determining a list of recommended items to buy, a service to handle wish lists, a service for authenticating credit card and so on.

All of these services are really mini applications performing a single business capability. What makes this organization interesting is, it is not organized around software layers. We don't create a web microservice, business logic microservice or database microservice. We are creating microservices around business capabilities. So we have a wish list microservice, a order microservice, and a processing microservice. Microservices communicate with each other through a well-defined interface, usually REST or a messaging queue. The communication is almost always stateless. The microservice data is federated as well. That means every microservice is responsible for its own data model and data. I think that statement just caused heart palpitations for some DBAs. There might be good business reasons to standardize the data platform for all microservices, but the idea is if we're on the team building the microservice, we should know best which persistence model we need.

### Why Microservices?

Seriously though, microservices make our system loosely coupled. If we need to upgrade, repair, or replace a microservice, we don't need to rebuild our entire application. Just swap out the part that needs it. Rebuilding isn't a big deal for a small desktop app, but it can be a killer for a whole enterprise. Breaking our system into small parts also allows each microservice to focus on a single business capability.

One benefit of this is we can have a small team focused on a simple task. Jeff Bezos from Amazon created the two pizza rule, the ideal size for a microservices team is one you can feed with two pizzas. A simple interface means we can use different languages and tools. If we've got a great well-defined interface, it doesn't matter what language or architecture we use, We could write our service in Visual Basic if we wanted. As long as we publish how other services are to communicate with our service, we're good. That's why the most common methods for communication between mircoservices are HTTP and messaging. Everybody supports that. 

### What makes a good Microsevice?

It focuses on a single business capability. That isn't to say it does only one thing. Just one business thing. It does not mean we divide into a web capability, logic capability, and database capability. That's going back to what we had before. Each microservice should have it's own datastore. This limits what can change under the microservice. If another group is responsible for the data model, they can also change the data model. That can force downstream changes for many microservice teams. Changes that might not help our team at all. Make communication stateless. If our communication is stateless, we can add more copies of the microservice at will. Stateless communication gives our microservice the ability to scale effortlessly. Each interaction with our microservice can be handled by a different instance. Since the communication is stateless, great microservices should be able to continue if another microservice fails. That means if we can't communicate with another service, our service should be able to make due. If we're trying to get a list of suggested titles from another service, we'll have a default ready just incase. Our service should never break, just because another service broke. Finally, a good team makes a good microservice. We need all parts represented. That's a designer, web developer, coder, database admin, and operations all on our microservice team. Not representatives. It's everyone we need to make a fully functional application.Up to two pizzas of course. 



## Microservice Architecture##

[clickhere](https://www.youtube.com/watch?v=QAvYmDeA-mg)

- Loosely Coupled
- Small & Focused
- Language-Neutral
- Bounded Context : meaning that each microservice does not need to understand the implementation for other microservice within the application

### Microservices VS SOA

In fact, SOA and micro service share many similar methodologies in developing applications. Some key differences are debt of business and technical objectives between microservice and SOA.

1. Firstly microservice are design principles for an application whereas SOA is meant primarily for enterprise wide system configuration. Therefore, microservices have a focus on more localized whereas SOA is meant for overall business transformation.
2. On overarching view microservices can be viewed as a subset of SOA because it is focused on application whereas SOA focuses on large-scale enterprise architecture that spans multiple systems both geographically and logically

### Benefits of Utilizing Microservice

1. **There is a lowered learning curve for developers in the team.** This means that developers do not have to go through hundreds of thousands of line of code in order to understand the system.
2. **Bottlenecks can be identified and skilled properly.** 
3. **There can be much faster user acceptance testing.** Every microservice can be tested quickly to check for validity and consistency where as previous monolithic systems require the entire system to be ready in order to test for consistency in systems logic
4. **Application development cycle can be shortened to the use of mocroservices.** this is because each micro service function is independent of one another and that it operates and deploys on its own.
5. Allows the business to align very quickly to the use of micro service to respond to the industry  customer needs and demands.

## Article

[click here](https://dzone.com/articles/java-microservices-code-examples-tutorials-and-more)

## What Are Microservices?

Microservices are a form of service-oriented architecture style (one of the most [important skills for Java developers](https://stackify.com/great-java-developer-skills/)) wherein applications are built as a collection of different smaller services rather than one whole app. Instead of a monolithic app, you have several independent applications that can run on their own and may be created using different coding or programming languages. Big and complicated applications can be made up of simpler and independent programs that are executable by themselves. These smaller programs are grouped together to deliver all the functionalities of the big, monolithic app.

Microservices captures your business scenario, answering the question “*What problem are you trying to solve*?” It is usually developed by an engineering team with only a few members and can be written in any programming language as well as utilize any framework. Each of the involved programs is independently versioned, executed, and scaled. These microservices can interact with other microservices and can have unique URLs or names while being always available and consistent even when failures are experienced.

## What Are the Benefits of Microservices?

There are several benefits to using microservices. For one, because these smaller applications are not dependent on the same coding language, the developers can use the programming language that they are most familiar with. That helps developers come up with a program faster with lower costs and fewer bugs. The agility and low costs can also come from being able to reuse these smaller programs on other projects, making it more efficient.