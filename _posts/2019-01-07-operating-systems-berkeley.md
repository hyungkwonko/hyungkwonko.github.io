---
layout: post
title: OS Lecture Note 3
date: 2019-01-07 17:00:00
categories:
- Operating systems
tags:
- concurrency
- parallelism
image: /thumbnail-mobile.png
author: "Hyung-Kwon Ko"
meta: "Seoul"
---

### [](#header-3)What is the difference between parallelism and concurrency?  
<img src="/images/pic3.PNG">

Concurrency is actually better.  

Concurrency:  
- composition of a independently executing processes  
- it is about dealing with a lot of things at once  
- logical level  
- single core can do it

Parallelism:  
- simultaneous execution of multiple things  
- possibly related, possibly not  
- doing a lot of things at once  
- physical level  
- only multicore can do it

Parallelism you know things are running at the same time for sure. Concurrency is more, they could run at the same time, or they could be interleaved in any arbitrary way

<br/>

### [](#header-3)What happens during execution?  


### [](#header-3)How can we give the illusion of multiple processors?   
<img src="/images/pic4.PNG">  
CPU1 - CPU2 - CPU3 - CPU1 - ...  

### [](#header-3)Each virtual CPU needs a structure to holding  
1. Program Counter(PC), Stack Pointer(SP)
2. Registers (Integer, Floating point, ...)

### [](#header-3)How to switch from one CPU to the next?
1. Save PC, SP and registers in current state block  
2. Load PC, SP and registers from new state block  

### [](#header-3)What triggers switch?  
- Timer, voluntary yield, I/O, other things
- Should happen frequently to maintain the illusion to have multiple things going on at once  
- But if there are too many, there's some overhead to unloading and reloading, ends up with more overhead than computing  

### [](#header-3)Consequences of sharing  
- Each thread can access the data of every other thread(good for sharing, bad for protection)  
- Thread can share instructions  

### [](#header-3)How to protect threads from one another?  
1. Protection of memory  
2. Protection of I/O devices  
3. Protection of access to processor

### [](#header-3)Mapping between virtual and physical address  
- Protection required  

### [](#header-3)How do we multiplex processes?  
- The current state of process held in a process control block(PCB) is called 'snapshot' of the execution and protection environment. Only one PCB can be activated at a time.
- Give out CPU time to different processes. This is called 'Scheduling'. Give more time to important processes.
- Give pieces of resources to different processes(protection)

### [](#header-3)CPU switch from process to process
- this is also called a 'context switch'  
- code executed in kernel is overhead  

### [](#header-3)Process states
<img src="/images/pic5.PNG" width="500">  

- new: the process is being created  
- ready: the process is waiting to run  
- running: instructions are being executed  
- waiting: process waiting for some event to occur  
- terminated(zombie process): the process has finished execution but the resources are not freed yet  

### [](#header-3)CPU yield
- I/O request
- time slice expired
- fork a child
- wait for an interrupt

### [](#header-3)Process =? program
- More to a process than just a program
- Less to a process than a program

### [](#header-3)Multiple processes collaborate on a task
- separate address spaces isolates processes
- shared-memory mapping  
<img src="/images/pic1.PNG" width="500">  
- message passing  

### Thread
<img src="/images/pic2.PNG" width="500">

- Thread is a sequential execution stream within process.
- Multithreading is a single program made up of a number of different concurrent activities
- Every thread has its own stack
- Each of the threads can screw each other up(problem)

<br/><br/>

### [](#header-2)References
- *[Parallelism vs. Concurrency by Dieter Galea][ref1]*   
- *[Nesoy's blog by YoungJae Kwon][ref2]*    
- *[Concurrency Is Not Parallelism by Rob Pike][ref3]*   
- *[Operating Systems and System Programming by John Kubiatowicz][ref4]*   

[ref1]:http://www.dietergalea.com/parallelism-concurrency/
[ref2]:https://nesoy.github.io/articles/2018-09/OS-Concurrency-Parallelism
[ref3]:https://vimeo.com/49718712
[ref4]:https://www.youtube.com/watch?v=5Ip__MiHRxw&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=3
