---
title: OOA 与OOD 分析设计视频Part4 Use Cases
excerpt: |
  OOA 与OOD 分析设计视频Part4 Use Cases
category: 设计模式
feature_image: "https://picsum.photos/2560/600?image=872"
---
原文： https://www.youtube.com/watch?v=bnbwNe-w2U4&list=PL6XklZATqYx9dj72MKG6wLYjljeB2odra&index=7

### Use cases

- Users may be more than just people, also can be softwares, external system that are going to use our system


- **User case** - an activity that the system performs, usually in response to a request by a user
  - Use cases define functional requirements
  - Analysts decompose the system into a set of use cases (functional decomposition. **Decomposition: Account management is a very high level, very vague description, we have to decompose that into all the different things that one has to do in the process**)
  - Two techniques for Identifying use cases
    1. **User goal technique** - (for small project)
    2. **Event decomposition technique** - (more popular, a better way to do )
- Name each use case using **Verb-Noun**

### User Goal Technique (For smaller project)

- **Step One** : **Identify** all possible users for the new "system"
- **Step Two: Classify** all users into functional roles
  - Ex. Shipping, marketing, sales, etc.
- **Step Three: Classify users** into organizational level
  - Ex. Operations ,executive, professional, etc.
- **Step Four: Interview** to find a list of specific goals
  - Start wit current goals, then imagine new functionally that would add value
  - Use verb-noun phrasing
    - Add customer
    - Update order
    - Produce report
- **Step Five: Create** use case list organizaed by user type
- **Step Six: Look** for duplicate use cases and resolve inconsistencies
- **Step Seven: Identify** where different types of users need the same use cases
- **Step Eight: Review** all use cases with each type of user and the stakeholders

### Event Decomposition Technique (for larger, more complicated system, )

- **More Comprehensive and Complete Technique**
  - Identify the events that occur to which the system must respond.
  - For each event, name a use case (verb-noun) that describes what the system does when the event occurs
- **Event** - something that occurs at a specific time and place, can be described, and should be remembered by the system

### Types of Events

- **External Event**
  - an event that occurs outside the system, usually initiated by an external agent or actor
- **Temporal Event**
  - an event that occurs as a result of reaching a point in time
- **State Event**
  - an event that occurs when something happens inside the system that triggers some process
  - reorder point is reached for inventory item

### Finding the actual event

### Tracing a sequence of transactions resulting in events

### Perfect Technology Assumption

- Don`t worry about dunctins built into system because of limits in technology and people. Wait until design.
  - Log in
  - Change password
  - Change preferences
  - System error handling
  - Backups
  - Security

### Steps:

1. Consider the **external event** in the system environment, and **identify and name the use case** that the system requires.
2. Consider the **temporal event** in the system environment, and **identify and name the use case** that the system requires
3. Consider the **state event** in the system environment, and **identify and name the use case** that the system requires
4. When events and use cases are defined, check to see if they are required by using the perfect technology assumption. **Do not include events that involve such system controls as login, logout, change password, and backup or restore the database, as these are put in later.**

### Why is Event Decomposition Tech is better than user goal?

- Event are **broader** than user goal: Capture **temporal** and **state** events
- Help decompose at the right level of analysis: an **elementary bussiness proceee(EBP)**
  - **EBP is a fundamental business process performed by one person, in one place, in response to a business event**
- Uses **perfect technology assumption** to to make suer functions that support the users work are identified and not additional functions for security and system controls

### Create, Read/Report. Update and Delete (CRUD)

- Often introduced in database context
- Technique to validate, refine or cross-check use cases
- NOT for primarily identifying use cases
- For each **domain class**, verify there are use cases for CRUD
  - "Domain Class" - Fancy way of saying a type of user that will interact with the system.

**This is a good way to identify that I have some way to do all the basic functionality for each domain class for each types of users.**

![DomainClassTable](pics/DomainClassTable.png)

### Steps: (Steps for CRUD)

1. **Identify** all the data entities or **domain class** involved in the new system.
2. **For each type** of data (data entity or domain class), **verify** that a use case has been identified that **creates** a new instance, **updates** existing instance, **reads** or reports values of instance, and **deletes** (archives) an instance.
3. If a needed use case has been overlooked, **add a new use case** and then identify the stakeholders.
4. With integrated applications, make sure itis clear which application is responsible for adding and maintaining the data and which system merely uses the data.

### Use case description

1. Once you have your use cases, provide a list with a **brief** description **describing the basic steps to complete the use case.**
2. Example:
   1. **Create Customer Account**
      - User enter new customer account data, and the system assigns account number, creates a customer record, and creates an account record.
3. One of our first deliverables is going to be a list of the use cases for system, and descriptions of each use cases, in-depth description of each one.
4. It only have to be a couple of sentences, a paragraph at most. If you find yourself describing a use case with more than a paragraph, chances are you`ve got more than one use case hidden witin that one use case, and you need to decompose a little bit further.

### Use case diagrams

- **Use case diagram** - a UML model used to graphically show use cases and their relationship to actors
- Recall **UML** is **Unified Modeling Language,** the standard for diagrams and terminology for developing information systems
- **Actor** is the **UML** name for a end user (**could be other systems**, anything outside our system is going to initiate some action within our system, something we are gonna handle)
- **Automation boundary** - the boundary between the computerized portion of the application and the users who operate the application (**everything inside the automation boundary is the system we are designing, everything outside the automation boundary are the actors that are interfacing with our system** )

![UseCaseDiagram](pics/UseCaseDiagramDay4.png)

### Use case diagramming steps:

1. **Identify** all the **stakeholders** and **users** who would benefit by seeing a use case diagram

2. **Determine** what each stakeholder or user needs to review in a use case diagram: each subsystem, for each type of user, for use cases that are of interest

3. For each potential communication need, **select the use cases and actors to show and draw the use case diagram.** There are many software packages that can be used to draw use case diagrams

4. Carefully **name each use cases diagram** and then note how and when the diagram should be used to review use cases with stakeholders and users.

   ​

   **You don`t have to diagram all the use cases. I think it is good practice to diagram most of the use cases. In most projects, we tend to do that to give everybody some visibility and who is going to be interacting with our system and how, so everybody has a understanding of how they interacting with system.**

![ActualUseCaseDiagram](pics/ActualUseCaseDiagram.png)

