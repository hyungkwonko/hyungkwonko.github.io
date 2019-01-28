---
layout: post
title: "OS Lecture Note 5 - Cooperating Threads"
tagline: "5th lecture of Fall 2010 at UCB"
categories: [OS]
image: /thumbnail-mobile.png
author: "Hyung-Kwon Ko"
meta: "Seoul"
---

### [](#header-3) Review
- Per thread state  
  1. Each thread has a thread control block(TCB)  
  2. OS keeps track of TCBs in protected memory  
- Yielding through internal events  
  1. you can do threading entirely at user level if you want. switching of threads does not have to be kernel mode because it just involves registers  
  2. once we start switching processes then that's got to be in the kernel because we are changing address spaces. (protection required)  
- Stack for yielding thread  
  1. How do we run a new thread?  
  2. how does dispatcher switch to a new thread?  
- Thread creation / destruction  
<br/><br/>

### [](#header-3) Goals for today  
- More on interrupts  
- Cooperating threads  
<br/><br/>

### [](#header-3) Interrupt controller  
<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic15.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, Interrupt controller, <a href="https://www.youtube.com/watch?v=9MdRHvx1n8Y&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=5">source</a> </div>

- interrupt: physical signal coming from some device  
- yellow thing represents something that's inside the chip talking to the CPU  
- interrupt mask: a bit mask that says which of these interrupts can even bother processor at any time   
- priority encoder: for the case of many interrupts occur at the same time, it will choose amongst the interrupts that actually have been selected. picks the interrupt and turns that into an ID and hands that off to the CPU, at the same time it may gives an interrupt signal
    - what is an ID? some number that says which device the interrupt signal is coming from: e.g. 02 = printer and so on
    - interrupt routine could be different depending on the ID(device) which sounds pretty natural...
- CPU can disable all interrupts with internal flag  
- Non-maskable interrupt line can't be disabled  
<br/><br/>

### [](#header-3) Network interrupt  
- disable / enable all interrupts  
- raise / lower priorities: change interrupt mask  
- software interrupts can be provided entirely in software at priority switching boundaries  
<br/><br/>


### [](#header-3) Review: Preemptive multithreading  
- use the timer interrupt to force scheduling decisions  
- this is called preemptive multithreading since threads are preempted for better scheduling  
<br/>

**_Review: Lifecycle of a thread or (a process)_**  
- we talked about this but... how thread starts??  
<br/><br/>


### [](#header-3) ThreadFork(): Create a new thread  
- this is a user-level procedure that creates a new thread and places it on ready queue(=CreateThread())   
- implementation
  - **sanity check** arguments  
  - enter kernel-mode and **sanity check** arguments again  
  - allocate new stack and TCB  
  - initialize TCB and place on ready list(runnable).  
<br/>

**_why do sanity check twice?_**  
- kernel version is more important(check it really has the permission for all the arguments)  
- first one is optional but help you debugging  
<br/>

**_how do we initialize TCB and stack_**  
- initialize register fields of TCB  
- do we initialize stack data? NO
<br/><br/>

### [](#header-3) How does thread get started?  
- very first thing is we switch the registers and then we execute return but return then causes us to start running the thread route code  
- eventually, run_new_thread() will select this TCB and return into beginning of threadRoot()  
- startup housekeeping  
  - includes things like recording start time of thread  
  - other statistics  
- stack will grow and shrink with execution of thread
- final return from thread returns into threadRoot() which calls threadFinish()  
<br/><br/>


### [](#header-3) How does thread get finished?  
1. after its initialization  
2. we actually execute our routine(function)  
3. when that function returns, then we execute threadFinish()  
4. then we exit, then the thread is dead  

**_then what is the threadFinishi()?_**
- we clearly have to re-enter kernel mode(to call a system call)  
- it needs to wake up all the threads that might be waiting for it to be finished(=join)
- can't deallocate thread yet  
  - we should switch out of this thread into another one and let a different thread deallocate this thread(뭔 말인지 바로 알겠지? 작동 중인 thread 상에서 free 하면 안된다는 말임. process에서 zombie state하고 같은 개념 정도라고 생각하면 될 듯. you cannot deallocate yourself while you are running)  
  - instead we use a flag that says it is waiting to be destroyed  
- call run_new_thread() to run another thread  
<br/><br/>


### [](#header-3) Additional detail  
- Thread fork is not the same thing as UNIX fork  
  - UNIX fork creates a new process so it has to create a new address space  
- Thread fork is very much like an asynchronous procedure call  
  - runs procedure in separate thread  
  - calling thread does not wait for finish  
<br/><br/>


### [](#header-3) Parent-child relationship  
<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic16.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, Typical process tree for solaris system, <a href="https://www.youtube.com/watch?v=9MdRHvx1n8Y&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=5">source</a> </div>

- the relationship is important... for example, if you kill the init process you reboot the machine
<br/><br/>

### [](#header-3) ThreadJoin() system call  
- one thread can wait for another to finish with the threadJoin(tid) call  
 (tid에 해당하는 thread가 종료할 때까지 wait)  
- where is a logical place to store this wait queue?  
  - on queue inside the TCB  
  - in principle, every thread can have on its own wait queue of people waiting for it  
- similar to wait() system call in UNIX  
<br/>

**_join / synchronization_**
- join은 하나의 thread가 completely finish 하고 나서 다른 thread가 실행되게 하는 것  
- synchronization은 어러 쓰레드가 critical section에 대해서 do not step on each other 하게 하는 것. locking.  
<br/><br/>


### [](#header-3) Kernel versus user-mode thread    
- downside of kernel threads: a bit expensive..  
- even lighter weight option: user threads  
  - user program provides scheduler and thread package  
  - may have several user threads per kernel thread  
  - user threads may be scheduled non-preemptively relative to each other  
  - cheap  
- downside of user threads:  
  - when one thread blocks on I/O, all threads block  
  - kernel cannot adjust scheduling among all threads  
  - option: scheduler activations..  
<br/><br/>


### [](#header-3) Multiprogramming vs. Multiprocessing    
- Definitions
  - Multiprocessing = Multiple CPUs  
  - Multiprogramming = Multiple threads per process  
  - Multithreading = Multiple threads per process  
- What does it mean to run two threads concurrently?  
  - scheduler is free to run threads in any order and interleaving  <br/>
<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic17.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, Multiprocessing vs. Multiprogramming, <a href="https://www.youtube.com/watch?v=9MdRHvx1n8Y&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=5">source</a> </div>
<br/>

**_correctness for system with concurrent threads_**  
- independent threads:
  - no state shared with others  
  - deterministic = input state determines results  
  - reproducible = can recreate starting conditions, I/O  
  - scheduling order doesn't matter  
  - which is.. GOOD  
- Cooperating threads:  
  - shared state between multiple threads  
  - non-deterministic
  - non-reproducible

**_non-deterministic and non-reproducible means that bugs can be intermittent_**  
- sometimes called "Heisenbugs"  
- (non-deterministic이라서 버그가 있을 수도 있고 없을 수도 있다 해서 하이젠베르크의 불확정성의 원리 고양이 그거 따서 말장난하는 것임)  
<br/><br/>

### [](#header-3)Summary  
- interrupts: hardware mechanism for returning control to operating system  
- new thread created with threadFork()  
- threads can wait for other threads using threadJoin()  
- Cooperating threads have many potential advantages  
  - but introduces non-reproducibility and non-determinism which is called.... **heisen-bugs**...
<br/><br/>


### [](#header-2)References

- *[Operating Systems and System Programming by John Kubiatowicz][ref1]*   

[ref1]:https://www.youtube.com/watch?v=5Ip__MiHRxw&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=3
