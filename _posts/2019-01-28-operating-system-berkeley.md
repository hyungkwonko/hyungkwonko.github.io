---
layout: post
title: OS Lecture Note 11 - Thread scheduling & Protection
date: 2019-01-29 17:00:00
categories:
- Operating systems
tags:
- scheduling
- protection
author: "Hyung-Kwon Ko"
meta: "Seoul"
---


### [](#header-3) Review
Last time we talked about
- Deadlock
- Banker's algorithm
- scheduling

<br/>

#### [](#header-4) Banker's algorithm for preventing deadlock
- Banker's algorithm:
  - allocate resources dynamically
    - evaluate each request and grant if some ordering of threads is still deadlock free afterward
    - Technique: pretend each request is granted, then run deadlock detection algorithm, substituting _(Max - Alloc <= Avail) for (Request <= Avail)_
    - keeps system in a **_SAFE_** state, i.e. there exists a sequence {T1, T2, ..., Tn} with T1 requesting all remaining resources, finishing, then T2 requesting all remaining resources, etc...

It acts like a bank, trying its best to avoid bankruptcy, and this is why it's called BANKER's algorithm.

read [**this**][ref2] if you are not fully getting it, especially the Italic formula above. 여기 부분 잘 이해하고 넘어갈 것

<br/>

##### [](#header-5) is there any case that the Banker's algorithm won't work?
if the threads end execution and don' release resources, you're in trouble. often times operating systems will release resources automatically even if you exit without releasing them. (But you should make sure about them!)

<br/>

##### [](#header-5) Dining lawyer's table with one chopstick
it could never start running since single chopstick is always not enough to run even a single thread.

<br/>

#### [](#header-4) Scheduling
select a waiting process from the ready queue and allocating the CPU to it.

1. First come first served
- run threads to completion in order of submission
- Pros: simple (+)
- Cons: short jobs got stuck behind long ones (-)

2. Round-Robin
- give each thread a small amount of CPU time when it executes: cycle between all ready threads
- Pros: better for short jobs (+)
- Cons: poor when jobs are same length (-) because in this case we don't have to switch processes from time to time, the only thing we are getting here is overhead(context switch).

we want to do FCFS with ideal time, because it's always the best only if we can sort them from min to max by time consumed by each process.

shortest - second shortest - ...

<br/><br/><br/>

### [](#header-3) Review
Last time we talked about
















<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic69.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, RR example, <a href="https://www.youtube.com/watch?v=YwECz0Lr1Bk&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=11">source</a> </div>



### [](#header-3) References

- *[Operating Systems and System Programming by John Kubiatowicz][ref1]*   

- *[qodadsfdsfa][ref2]*   

[ref1]:https://www.youtube.com/watch?v=YwECz0Lr1Bk&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=11


[ref2]:https://raisonde.tistory.com/entry/Bankers-Algorithm-cf-Detection-Algorithm
