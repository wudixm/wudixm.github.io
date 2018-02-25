---
title: Java 性能调优
excerpt: |
  Java 性能调优
category: Java 高级
feature_image: "https://picsum.photos/2560/600?image=872"
---
## Java Stack + Heap with Reference & Instance Variables

- Hit the run button
  - There are two memory locations that become reserved for your java application to run successfully. 
  - These are resources of your computer, your RAM, one of them one desert you can think of these as memory areas, reserved for the successful completion of your application 
  - One area is referred to as the **stack** and the other is referred to as **heap** 

## The Fundamentals of JVM Tunin

### Agenda

- What you need to know about GC
- What you need to know about JIT compilation
- Tools to help you

### Java HotSpot VM Heap Layout

1. Young Generation
2. Old Generation
3. Permanent Generation

- Eden space is the place where object allocated, so that is part of the Young Generation.
- The Survivor spaces are used to age objects.
- **Once Eden space becomes full, a minor gabage collection occurs anything that survives gets moved into a Survivor space. Anything still surviving from the From Survivor space also gets moved to the To Survivor space**
- At some point in time, through an age threshold, **object that are greater than that threshold will get promoted into the Old Generation space or if there are not enough space in the Survivor Spaces, it will take the oldest objects and move those to the Old Generation **
- Objects are promoted into Old Generation that are longer lived

### Important Concepts (1 of 4)

- **Frequency of minor GC is dictated by**
  - Application object allocation rate
  - Size of the Eden space
- Frequency of object promotion into Old Generation is dictated by 
  - Frequency of minor GCs (how quickly objects age)
  - Size of the Survivor spaces (large enough to age effectively)
    - Ideally promote as little as possible (more on this)

### Important Concepts (2 of 4)

- Object retention can degrade performance more than object allocation
  - In other words, the longer an object lives, the greater the impact on throughput, latency and footprint
- Objects retained for a longer period of time
  - Occupy available space in
