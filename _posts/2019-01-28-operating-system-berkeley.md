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
use_math: true
---

## [](#header-2)<span style="color:#088A08"> *Review* </span>
Last time we talked about
- Deadlock
- Banker's algorithm
- scheduling

<br/>

### [](#header-3) Banker's algorithm for preventing deadlock
- Banker's algorithm:
  - allocate resources dynamically
    - evaluate each request and grant if some ordering of threads is still deadlock free afterward
    - Technique: pretend each request is granted, then run deadlock detection algorithm, substituting _(Max - Alloc <= Avail) for (Request <= Avail)_
    - keeps system in a **_SAFE_** state, i.e. there exists a sequence {T1, T2, ..., Tn} with T1 requesting all remaining resources, finishing, then T2 requesting all remaining resources, etc...

It acts like a bank, trying its best to avoid bankruptcy, and this is why it's called BANKER's algorithm.

read [**this**][ref2] if you are not fully getting it, especially the Italic formula above. 여기 부분 잘 이해하고 넘어갈 것

<br/>

#### [](#header-4) is there any case that the Banker's algorithm won't work?
if the threads end execution and don' release resources, you're in trouble. often times operating systems will release resources automatically even if you exit without releasing them. (But you should make sure about them!)

<br/>

#### [](#header-4) Dining lawyer's table with one chopstick
it could never start running since single chopstick is always not enough to run even a single thread.



<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *Scheduling* </span>
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

<br/>

### [](#header-3) What if we knew the future
- can we always mirror best FCFS?
- shortest job first (SJF):
  - run whatever job has the least amount of computation to do
  - sometimes called shortest time to completion first (STCF)
- shortest remaining time first (SRTF):
  - preemptive version of SJF: if job arrives and has a shorter time to completion than the remaining time on the current job, immediately preempt CPU
  - sometimes called shortest remaining time to completion first (SRTCF)
  - (well... this thing coming in is even shorter than the thing I am running therefore run it RIGHT NOW!)
- there can be applied either to a whole program or the current CPU burst of each program
  - idea is to get short jobs out of the system
  - big effect on short jobs, only small effect on long ones (kind of way to mediate in the bad effect)
  - result is better average response time

- **The biggest problem with this is that WE NEED TO PREDICT THE FUTURE in order to make this work**

<br/>

### [](#header-3) Discussion
- SJF / SRTF are the best you can do at minimizing average response time
  - provably optimal
  - since SRTF is always at least as good as SJF, focus on SRTF
- comparison of SRTF with FCFS and RR
  - what if all jobs the same length
    - SRTF becomes the same as FCFS
  - what if jobs have varying length?
    - SRTF and RR: short jobs not stuck behind long ones (two are not the same but similar with respect to the point they both try to get those short jobs first)
  - 이 부분 다 이해 하겠지?? 여러번 했던 부분이고 어렵지는 않으니 읽어 볼 것.


<br/>

### [](#header-3) Example to illustrate benefits of SRTF

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic83.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, Example, <a href="https://www.youtube.com/watch?v=YwECz0Lr1Bk&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=11">source</a> </div>

<br/>

while C is doing disk I/O, it does nothing even if it holds the process, which means it is best to relinquish CPU and give it to another process such as A / B that are enable to do their job.

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic84.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, Example continued, <a href="https://www.youtube.com/watch?v=YwECz0Lr1Bk&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=11">source</a> </div>

<br/>

- with FIFO:
  - once A / B get in, keep CPU for two weeks (CPU cannot be allocated to C while A / B are working and it should wait two weeks straight... which is awful)

- with RR 100ms time slice:
  - C - A - B - C ....
  - Disk Utilization: (9 / 201) = 4.5 %

- with RR 1ms time slice:
  - C - ABABABABA..BABA - C ....
  - Disk Utilization: nearly 90 % but lots of wakeups

- with SRTF:
  - Disk Utilization: 90 %
  - C - A - C - A - ...
  - be careful there is no B intervened in the middle. (think of what SRTF does!!)

- 이 부분은 한글로 설명하면
  - 맨 위에 RR 100ms 부분은 C(1ms, 이 때는 접근 loop돌았음), A(100ms), B(100ms) 합치면 201ms이고 그 중 1번 loop 돌아서 그 1번에 해당하는 disk 접근 즉 9ms만큼이 I/O 일을 할 수 있는 것이기 때문에 disk Utilization이 9 / 201이 되는 것
  - 위에꺼를 이해 했다면 밑에 두개는 쉬움. 대신 switch를 넘나 많이 하기 때문에 중간것은 90%가 안된다는 것이다.

We are going to approximate SRTF. The real problem is that how to approximate it? We might need some prediction technique...
- 그래서 밑에서는 예측 알고리즘을 살펴볼 것이다.

<br/>

### [](#header-3) Predicting the length of the next CPU burst
- Adaptive: changing policy based on past behavior(not actually predicting, guessing based on the past experience)
  - CPU scheduling, in virtual memory, in file systems, etc
  - works because programs have predictable behavior
    - if program was I/O bound in past, likely in future
    - **if computer behavior were random, would not help**... 쩝...

- Example: SRTF with estimated burst length
  - use an estimator function on previous bursts:

$$\tau_n = f(t_{n-1}, t_{n-2}, t_{n-3}, ...)$$

  - function $$f$$ could be one of many different time series estimation schemes (Kalman filters, etc)

<br/>

### [](#header-3)Multi-level feedback scheduling
This is another way to estimate SRT / approximate SRTF.

- using n ready queues, feeding into each other, each with different priority
  - higher priority queues often considered "foreground" tasks
- each queue has its own scheduling algorithm
  - e.g. foreground = RR, background = FCFS

Adjust each job's priority as follows
- jobs starts in highest priority queue
- if timeout expires, drop one level
- if timeout doesn't expire, push up one level

what kind of behavior do we get out of this? shortest jobs end up running first

<br/>

#### Scheduling details
- result approximates SRTF:
  - CPU bound jobs drop like a rock
  - short running I/O bound jobs stay near top
- Scheduling must be done between the queues
  - fixed priority scheduling:
    - serve all from highest priority, then next priority, etc
  - time slice:
    - each queue gets a certain amount of CPU time
    - e.g. 70% to highest, 20% to next, 10% lowest.
  - 한마디로 스케줄링을 줄 세워놓고 hierarchical하게 순서를 매기는 것이다. 첫째 방법(fixed)은 제일 우선순위 높은 것부터 차례로 그냥 무식하게 하는거고, 그러면 starvation문제가 생기니까 두번째 말하는거(time slice)는 비율로 할당해서 주는 것이다. 예를 들면 highest=70 / middle=20 / lowest=10 이런식으로
  - 그럼 70 / 20 / 10 이건 어떻게 고르냐...?? nobody could fully explain it... just a magic (그냥 대충 때려 맞춘다는 말인 것 같음)
  - **what if something hasn't been run for a very long time? slowly crank its priority up until it starts running** (kind of another heuristic problem...)

<br/>

### Scheduling fairness
- strict fixed-priority scheduling between queues is unfair
- must give long running jobs a fraction of the CPU even when there are shorter jobs to run
- Tradeoff: fairness gained by hurting average response time! (당연)
- how to implement fairness?
  - could give each queue some fraction of the CPU

<br/>

### Lottery scheduling

the CPU time is proportional to the number of tickets. The more tickets you have, the more chances to win, and therefore the more CPU time you get

we want short jobs to have more tickets than the long jobs

to avoid starvation, we want to make sure that every jobs gets at least one ticket

the advantage over strict priority scheduling is this behaves gracefully as the load changes so as you add or delete jobs then everybody kind of proportionately slows down or speeds up, and you still make sure that there is no starvation

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic85.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, Lottery scheduling example, <a href="https://www.youtube.com/watch?v=YwECz0Lr1Bk&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=11">source</a> </div>

<br/>

Notice that nobody here is zero.

<br/>

### How to evaluate a scheduling algorithm

- deterministic modeling
  - takes a predetermined workload and compute the performance of each algorithm for that workload
- Queueing models
  - mathematical approach for handling stochastic workloads
- implementation / simulation
  - build system which allows actual algorithms to be run against actual data. Most flexible / general

<br/>

### Final word on scheduling

when do the details of the scheduling policy and fairness really matter?
- when there are not enough resources to go around

when should you simply buy a faster computer instead of modifying your scheduling algorithm?
- most scheduling algorithms work fine in the **"linear"** portion of the load curve, fails otherwise
- argues for buying a faster X when hit **"knee"** of curve



<br/><br/><br/>



##<span style="color:#088A08"> *Virtualizing Resources* </span>

Memory holds the complete working state of the process.

CPU is a kind of transient thing, while the memory has the state

<br/>


### Important aspects of memory multiplexing

- controlled overlap:
  - separate state of threads should not collide in physical memory



<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[Operating Systems and System Programming by John Kubiatowicz][ref1]*   

- *[은행원 알고리즘 설명][ref2]*   

[ref1]:https://www.youtube.com/watch?v=YwECz0Lr1Bk&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=11


[ref2]:https://raisonde.tistory.com/entry/Bankers-Algorithm-cf-Detection-Algorithm
