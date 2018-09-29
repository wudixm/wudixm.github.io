---
title: OOA 与OOD 分析设计视频Part5 Domain Class Modeling
excerpt: |
  OOA 与OOD 分析设计视频Part5 Domain Class Modeling
category: 设计模式
feature_image: "https://picsum.photos/2560/600?image=872"
---
原文： https://www.youtube.com/watch?v=bnbwNe-w2U4&list=PL6XklZATqYx9dj72MKG6wLYjljeB2odra&index=7
### Things in the Problem Domain

- **Problem domain** - the specific area (or domain) of the users business need that is within the scope of the new system
- **"Things"** - are those items users work with when accomplishing tasks that **need to be remembered**.
- **Examples of "Things" are products, sales, shippers, customers, invoices, payments, etc.
- These "Things" are modeled as **domain classes** or **data entities**
- In this course, we will call them **domain classes**. In a database course you call them **data entities**.

### Two techniques to identify "things"

- **Brainstorming Technique**
  - Use a checklist of all the usual types of things typically found and brainstorm to identify domain classes of each type
- **Noun Technique**
  - Identify all of the nouns that come up when the system is described and determine if each is a domain class, an attribute, or not something we need to remember.

### Brainstorming Technique

- **Are there any…**

![BrainstormingTechForIdentifyThings](pics/BrainStormToIdentifyThings.png)

 **In most case, the brainstorming techniques involve stakeholders.**

### Brainstorming Technique Steps:

1. **Identify** a user and a set of use cases
2. **Brainstorm** with the user to identify things involved when carrying out the sue case - that is, things about which information should be captured by the system.
3. Use the types of things (categories) to systematically **ask question** about patential things, such as the following:
   - Are there any tangible things you store information about?
   - Are there any locations involved?
   - Are there roles played by people that you need to remember?
4. **Continue** to work with all types of users and stakeholders to **expand** the brainstorming list
5. Merge the results, eliminate any duplicates, and **compile an initial list**

**The brainstorming technique you have to have stakeholder to help you do the brainstorming**

**The Noun Technique, we make the assumption we don`t have a stakeholder or users to help us brainstorm**

### Noun Technique

- A technique to identify problem domain classes (things) by **finding, classifying, and refining a list of nouns that come up in discussions or documents**
- Popular technique. **Systematic** (It is a popular technique for system analysis )
- Does end up with **long lists and many nouns that are not things that need to be stored by the system**
- Difficulty identifying synonyms and things that are really **attributes**
- **Good place to start when there are no users available to help brainstorm** 

![NounTechExample](pics/Part5NounTechExample.png)

Once you complete this list, this is something you can easily give to a stakeholder and say "Can you verify or can you fill in some blanks for us, or can you explain further some of the details"

### Noun Technique Steps:

1. Using the use cases, actors and other information about the system- incluing inputs and outputs - **identify all nouns**
2. Using other information from existing systems, current procedures, and current reports or forms, **add items or categories of information needed**
3. As list of nouns builds, **refine it**. Ask question about each noun to help you decide whether you should include it.
4. Create a master list of all nouns identifiedand then note whether each one should be included, excludes, or researched further
5. Review the list with users, stakeholders and team members and then define the list of things in the problem domain.

**Once we identified all the things in the system, now we can start worrying about how are we going to model the domain, or these entities, or these things and how they relate to one another**. There are two techniques. First Entity Relationship Diagrams or class diagram.

### Entity Relationship Diagrams

- An ERD shows basically the same information as a domain model class diagram
- **It is not a UML diagram**, but it is **widely used by data analysts in database management**
- There really is no standard notation, but most developers use the entity and **crows feet notation** shown in this course
- An ERD is not good for showing generalization / specialization relationships and whole part relationships( **you will learn more about that in Class Diagrams.**)

### First - Context Level Dataflow

- Structured Analysis equivalent to a "use case"

![ContextLevelDiagram](pics/Part5ContextLevelDataFlow.png)

 **Once we have Context Level Diagram, we can decompose that further into level 1, level 2, level 3 diagram.**

### ER-Diagram

**Fix many to many relationship in ER-Diagram**

**Whenever you see many to many relationship, that`s usually an indication you are missing something, some entity you have to add to this diagram**

**Need something to tie that relationship**

![ExampleERD](pics/Part5ERDiagram.png)

**The ER-Diagram would be used for database design**

### Domain Classes

- **Attribute** - describe one piece of information about each instance of the class
  - Customer has first name, last name, phone number
- **Identifier or key**
  - **One attribute uniquely identifies an instance of the class**
- **Compound attributr**
  - **Two or more attributes combined into one structure to simplify the model.** (E.g., address rather than including number, street, city, state, zip separately)

### Attributes and Values

- **Class is a type of thing**. **Object is a specific instance of the class**. Each instance has its own values for an attribute

### Association among things...

- **Association** - A naturally occurring relationship between classes (UML term, In ER-Diagram, we talk about Relationships. **Association in UML, is a specific type of relationship**)

### Just to Clarify...

- Called **association** on class diagram in UML
  - **Multiplicity** is term for the number of associations between classes: **1 to 1 or 1 to many**
  - **We are emphasizing UML in this course**
- Called **relationship** on ER-Diagram in database class
  - **Cardinality** is the term for number of relationships in entity relationship diagrams: **1 to 1 or 1 to many**
- **Associations and Relationships apply in two directions**
  - Read them separately each way
  - A customer places an order
  - An order is placed by a customer

### Min And Max Multiplicity

- Associations have minimum and maximum constraints

### Types of Associations

- **Binary Association** (90% )
  - **Associations between exactly two different classes**
- **Unary Association** (recursive, which is **where something is related to itself**)
  - **Associations between two instances of the same class**
- **Ternary Association (Three)**
- **N-ary Association (between N)**

### Domain Model Class Diagram

- **Class**
  - A category of **classification** used to describe a collection of objects
- **Domain Class**
  - Classes that describe objects in the problem domain
- **Class Diagram**
  - A UML diagraam that shows classes **with attributes and associations (plus methods if it models software classes)**
- **Domain Model Class Diagram**
  - A class diagram that only includes classes from the problem domain, not software classes so no method

**We are not going to diagram methods in Domain Model Class Diagram, but we would use those in the software design, we are now in the analysis phase, we are not in design phase**

**In analysis phase, we are trying to understand the problem domain, and understand what we are designing, so the Domain Model Class Diagram will help us do that, so we are not going to talk methods yet.**

### Domain Class Notation

-  **Domain class has no methods**
- Class name is always capitalized 
- Attribute names are not capitalized and use camelback notation (words run together and second word is capitalized )

![Domain Class Notation](pics/Part5DomainClassNotation.png)

### Simple Domain Model Class Diagram

- **From the Semantic Net(shown previously)**
  - A customer places zero or more orders
  - An order is placed by exactly one customer
  - An order consists of one or more order items
  - An order item is part of exactly one order

![Simple Domain Model Class Diagram](pics/Part5DMCDExample.png)

### UML Notation for Multiplicity

![UML Notation Multiplicity](pics/UMLMultiplicity.png)

**Many to many associations in a class diagram usually tells us that we are missing some kind of class, there is something we have to use in this diagram to track those things.**

![ManyToManyAssociation](pics/Part5ManyToManyDiagram.png)

**Any time you see a  "many-to-many" association in a class diagram, you have to have a third class that can tie those things together.**

### Generalization and Specilazation Relationships

**We can do in Class Diagram, that we can`t do in ER-Diagram**

- Generalization / Specialization 
  - A hierarchical relationship where subordinate classes are special types of the superior classes. Ofter called on Inheritance Hierarchy.
- Superclass
  - the superior or more general class in a generalization / specialization hierarchy
- Subclass
  - the subordinate or more specialized class in a generalization / specialization hierarchy
- Inheratance
  - the concept that subclasses inherit characteristics of the more general superclass

**In database world, this really doesn`t make sense.** In database world, we would have an account table that would have all the attribute in the single table.

**Whenever you have a generalization, it`s typically an abstract class(it is a class that never has an instance or object based on that class)**.

![General/SpecialExample](pics/Part5GeneralizationSpExample.png)

### Whole Part Relationships

- **Whole-part relationships** - a **relationship** between classes where **one class** is **part of** or a component partion of **another class**

  - **Aggregation** - a whole part relationship where the componment part exists separately and **can be removed and replaced**(UML diamond symbol, next slide)
    - Computer has disk storage devices
    - Car has wheels
  - **Composition** - a whole part relationship where the parts **can no longer be removed**(filled in diamond symbol)
    - Hand has fingers
    - Chip has circuits

  ![Whole Part Relationship Example](pics/Part5WholePartRelationShip.png)

  ### Relationships

  - There are actually **three** types of **relationships** in class diagrams
    - **Association Relationships**
      - These are associations discussed previously, just like ER-Diagram relationships
    - **Whole part Relationships**
      - One class is a component or part of another class
    - **Generalizations / Specialization Relationships**
      - **Inheritance**
  - Try not to confuse relationship with association
