---
layout: post
title: "OS Lecture Note 4 - Thread Dispatching"
tagline: "4th lecture of Fall 2010 at Berkeley"
categories: [OS]
image: /thumbnail-mobile.png
author: "Hyung-Kwon Ko"
meta: "Seoul"
---

### [](#header-3) Today's lecture
- Further understanding of threads  
- Thread dispatching  
- Beginnings of thread scheduling  
<br/><br/>


### [](#header-3) Process vs. Thread
1. Process: an executing instance of a program
  :: independent of one another  
  <img src="/images/pic126.png">  

2. Thread: a path of execution within a process. A process can contain multiple threads.
  :: share with other threads their code section, data section, and OS resources  
  :: should be careful to do the synchronization  
  <img src="/images/pic127.png">  
<br/><br/>

**_Advantages of Thread over Process_**  
1. Responsiveness: If the process is divided into multiple threads, if one thread completes its execution, then its output can be immediately returned.  

2. Faster context switch: Context switch time between threads is lower compared to process context switch. Process context switching requires more overhead from the CPU.

3. Effective utilization of multiprocessor system: If we have multiple threads in a single process, then we can schedule multiple threads on multiple processor. This will make process execution faster.

4. Resource sharing: Resources like code, data, and files can be shared among all threads within a process.
Note: stack and registers can’t be shared among the threads. Each thread has its own stack and registers.

5. Communication: Communication between multiple threads is easier, as the threads shares common address space. while in process we have to follow some specific communication technique for communication between two process.


To summarize, the advantages of using thread over process is very straightforward. It is more efficient with regard to resource utilization as the number of system calls decrease. This is possible because resources except the stack are shared among threads whereas processes don't(each process has its own stack).
<br/><br/>


### [](#header-3) Thread state  
- state shared by all threads in process / address space  
- state private to each thread(kept in Thread Control Block)  
<br/><br/>


### [](#header-3) Questions if there are two stacks in address space  
if two stacks are on top of each other and one of them is called many times, it could actually go into the other stack and completely mess it up. So the questions are...  

- How do we position stacks relative to each other?  
- What maximum size should we choose for the stacks?  
- What happens if threads violate this?  
- How might you catch violations?  
<br/><br/>

### [](#header-3)Per thread state  

each thread has a Thread Control Block(TCB)  
- execution state: CPU registers, program counter, pointer to stack  
- scheduling info: State, priority, CPU time  
- accounting Information
- various pointers for implementing scheduling queues

Active threads are represented by their TCBs.
<br/><br/>

### [](#header-3)Ready queue and various I/O device queues  
thread not running $\to$ TCB is in some scheduler queue  
<img src="/images/pic6.PNG">
<br/><br/>

### [](#header-3)Running a thread  
_how do I run a thread?_  
1. load its state  
2. load environment  
3. jump to the PC  

_how does the dispatcher get control back?_  
1. Internal events: thread returns control voluntarily  
  :: blocking on I/O(the act of requesting I/O implicitly yields the CPU)
  :: waiting on a signal form other thread(wait())
  :: yield()  
2. External events: thread get preempted  
  :: what if thread never does any I/O, never waits, and never yields control? = Timer & Interrupt can solve  
  :: Timer: like an alarm that goes off every some many milliseconds  
  :: Interrupts: signals from hardware or software that stop the running code and jump to kernel  
<br/><br/>


### [](#header-3)What happens when thread blocks on I/O?  
_how does dispatcher decide what to run?_  
- scheduling priorities(LIFO, random pick, FIFO... many policies)  
- fair scheduler policy? = depends on what your goals are  
<br/><br/>


### [](#header-3)Summary  
- The state of a thread is contained in the TCB  
- Multithreading provides simple illusion of multiple CPUs  
- Switch routine(**must be carefully constructed**)  
- Many scheduling options  
<br/><br/>


### [](#header-2)References

- *[Operating Systems and System Programming by John Kubiatowicz][ref1]*   
- *[The difference between process and thread by Kwanwoo Kang][ref2]*
- *[The difference between process and thread by Heejeong Kwon][ref3]*
- *[process & thread by Hyungjin Kim][ref4]*

[ref1]:https://www.youtube.com/watch?v=5Ip__MiHRxw&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=3
[ref2]:https://brunch.co.kr/@kd4/3
[ref3]:https://gmlwjd9405.github.io/2018/09/14/process-vs-thread.html
[ref4]:https://lalwr.blogspot.com/2016/02/process-thread.html
