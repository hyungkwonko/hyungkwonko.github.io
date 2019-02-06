---
layout: post
title: OS Lecture Note 6 - synchronization
date: 2019-01-14 17:00:00
categories:
- Operating systems
tags:
- synchronization
author: "Hyung-Kwon Ko"
meta: "Seoul"
---

thread root stub is really set up a return address such that thread route procedure will start running   

why should we switch to the user mode? cause once we start calling this we are running arbitrary code that was given to us when the thread was created and we really do not want to do that in kernel mode. So we get into user mode before we start running    

be careful you are in kernel mode at switch  

in a nutshell,  
1. we go to user mode  
2. we run our function  
3. thread finishes  
4. we are done  

correctness for systems with concurrent threads:  
- if dispatcher can schedule threads in any way, programs must work under all circumstances  
- if there is a bad interleaving of threads the scheduler will run across some sort of weird bug   

what is an ideal?
The ideal is independent threads which have no shared state whatsoever between them, so they are doing their own thing. you will get the same result because they don't depend on each other.   

why this is an ideal?
nothing ever operates independently. everything is dependent.  

it does matter how they interleave because if you don't program correctly, you will get the wrong answer.  

so we start talking about synchronization. it is going to be about being disciplined in how you program.

Cooperating threads:
- non-deterministic  
- non-reproducible  
so, that is a kind of unfortunate, (=Heisenbugs)

Goals for today
- concurrency examples  
- need for synchronization
- examples of valid synchronization

is any program truly independent? **NO**

why should we allow the cooperating threads...
why people cooperate? because you can get more out of the cooperation than not.

ADVANTAGES
1. share resources
2. speedup
3. modularity: more important than you might think  

Web server - threaded web server

how to speedup? with multiple threads..
waiting for I/O basically is what we are really talking about and this is the big advantage.

so you can share files in a process.
threads are much cheaper to create than processes

DISADVANTAGES
too many requests come in at once, what happens?
create lots of threads and then? and you get more overhead than real computation
실제 필요한 것은 10개만 있으면 되는데 쓰레드 200개 생겨서 switching만 하고 있는 경우..

overusing resources...

another option: thread pool
allocate a pool of threads: that is the maximum level of multi programming we want going on at anytime.

- every requests that comes in gets put in the queue
- and there's a finite number of threads here running
- and they go and grab a request off the queue,
- they do something with it, finish it, the next request


- and we only have a fixed number of things actually running in parallel at any time.
- so the master thread might do something like this allocate a bunch of threads
- the master thread is not actually processing anything
- all it is doing is grabbing connections and putting them on the queue, and if it turns out there is a free thread that is sleeping it will wake it up

- 한마디로 마스터 thread는 따른 작업을 한다기 보다는 scheduling을 해주는 것이다.
- 어떤 작업이 들어왔는데 thread가 자고 있는게 있으면 깨워서 할당 시키고.. 그런 개념

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic22.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, thread pools, <a href="https://www.youtube.com/watch?v=9MdRHvx1n8Y&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=5">source</a> </div>

ATM bank server
some central bank, computer somewhere, somehow you've got let them work in parallel w/o corrupting database.

we've got to figure this out and this would be the first case where concurrency really comes home and looks pretty obvious.

how to speedup? the most important thing is clearly gonna be having more than one request going on at once.
여러 request가 동시에 진행될 수 있도록 하면 된다.
여러 ATM작업이 동시에 이루어지면 된다.

there is actually a couple of ways we could do this

suppose we only had one CPU and we still want to overlap IO with computation

in a disk seek = a million instruction (a lot)  
the first thing that we want to try to do is overlap that disk IO with the actual computation
한마디로 디스크에 접근하는것 자체가 굉장히 느리다.

Can thread make this easier?  
threads yield overlapped IO and computation without deconstructing code into non-blocking fragments  
requests proceeds to completion, blocking as required  
we just keep that obvious pathway there so we have one thread per request and the request proceeds to completion
and it goes to sleep if necessary
so the fact that every request gets a thread means that there may be many threads going through deposit routine

한개의 thread가 sleep해도 다른 thread 가 ready to run하기 때문에 상관 없다.

so we don't have to overlap IO explicitly in the code because it happens is a side-effect of using threads  
- 쓰레드를 사용하는 side-effect이기 때문에 명시적으로 IO 오버랩을 할 필요가 없다.
- if one of the threads blocks to go to sleep on IO, it is okay because there's another thread that can immediately take up the CPU.

--------

Atomic operations: basically indivisible operation which means there's no way for any other code to get in the middle of this operation.

전에 했던 ATM 예제에서 deposit, withdraw 등이 atomic하게 작동한다면 문제가 될 것이 없음..
we don't have the problem of things not quite working properly

most machines have atomic memory operations - load and stores

many instructions are not atomic


---------

what are the correctness criteria for whatever it is you are designing
one of them will be about the fact that threaded programs, the two different threads

---------

**Too much milk problem:** can we fix this problem with just loads and stores?
강의 60분 부분

synchronization: using atomic operations to ensure that threads cooperate properly and give you correct behavior

mutual exclusion: only one thread is allowed to do a particular critical thing at a time  

how to ensure? what we are going to do is we will lock before entering a critical section and before accessing shared data and we will unlock when we are done
shared data will be protected that only one thread can mess with the shared data at once

the most important idea in all of synchronization is waiting
the way of fix these weird interleaving problems is we make someone wait so the interleaving goes away

HOW TO wait by not wasting our time... we will talk about this later on

SOLUTION: you put the key on the refrigerator.


THINK FIRST THEN CODE or... you will lose your hair...

-----

there is a synchronization condition built into this code that is a weird interleaving where things switch just at the wrong time
이상한 타이밍에 스위칭이 일어나서 완전 망하는 케이스가 발생할 수 있다. 따라서 atomic하게 해야한다.
--> weird context switching condition

------

lock을 이용해야 한다. lcok을 이용해서 critical section을 보호할 수 있어야 한다.  

Summary
- concurrent threads are a very useful abstraction
- concurrent threads introduce problems when accessing shared data
- important concept: atomic operations
- shoed how to protect a critical section with only atomic load and store --> pretty complex

### [](#header-2)References

- *[Operating Systems and System Programming by John Kubiatowicz][ref1]*   

[ref1]:https://www.youtube.com/watch?v=WutOO6saPBs&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=6
