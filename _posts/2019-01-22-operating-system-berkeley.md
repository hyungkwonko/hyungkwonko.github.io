---
layout: post
title: "OS Lecture Note 10 - Deadlock & thread scheduling"
tagline: "10th lecture of Fall 2010 at UCB"
categories: [OS]
image: /thumbnail-mobile.png
author: "Hyung-Kwon Ko"
meta: "Seoul"
---


### [](#header-3) Deadlock detection algorithm

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic66.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, Resource allocation graph example, <a href="https://www.youtube.com/watch?v=H2y762OQwYI&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=10">source</a> </div>

- only one of each type of resource = look for cycles
- more general deadlock detection algorithm:
  - free resources: 자유로운 자원의 수
  - request: current requests from tread X (맨 오른쪽 그림에서 T1의 request vector는 [1, 0]이다. 왜냐하면 R1을 request하고 있고 R2는 아니니까)
  - alloc: current resources held by thread X (T1의 allocation vector = [0,1] R1을 안가지고 있고 R2는 가지고 있으니까, allocate되었으니까)

- pseudo code:
```C
[Avail] = [FreeResources]
Add all nodes to UNFINISHED
do {
  done = true
  Foreach node in UNFINISHED {
    if([Request_node] <= [Avail]) {
      remove node from UNFINISHED
      [Avail] = [Avail] + [Alloc_node]
      done = false
    }
  }
} until(done)
```


### [](#header-3) What to do when detect deadlock?

- terminate thread, force it to give up resources (But who knows what it's got? 중요한게 있을지도.. 무조건 드랍하면 끝낼 수는 있지만 좀 위험하다)
- preempt the resources without killing off thread (락이 critical section만들어서 보호하려고 한건데 자원을 선점해버리면 lock의 의미가 전혀 없고 위험할 수 있음)
- rollback (useful for something like a Database, but 다른 것들에는 적절하지 않음)



### [](#header-3) Techniques for preventing deadlock

- infinite resources: doesn't have to be infinite, just large enough. not as weird as it might sound
- never sharing resources: preventing mutual exclusion (as you know, if one of the four conditions are not satisfied, deadlock never happens)
- don't allow waiting: phone company usually do this. prevent cycles, hold and wait condition
- request everything they need at the beginning (prevent hold and wait scenario, 단점은 자원을 다 할당을 애초에 해버려서 더이상 할당할 자원이 없음 나중에 가면, it is not good gor long running things that go forever)
- force all threads to request resources in a particular order preventing any cyclic use of resources



### [](#header-3) Banker's algorithm for preventing deadlock
don't ever let a graph occur that might be deadlockable (over conservative!!)
- state max resource needs in advance (애초에 max 숫자를 정해놓는다. 예를 들면 최소로 남겨야 하는 resource를 정해 놓는다던지... 그러면 항상 1개는 남아있으니까 deadlock이 발생 안한다.)
- allow particular thread to proceed if:
  - available resources - # of requested >= max
  - remaining that might be needed by any thread

- Banker's algorithm:
  - allocate resources dynamically: evaluate each request and grant if some ordering of threads is still deadlock free afterward (데드락이 아니면 자원 할당, 데드락이면, 안할당)
  - do the simulation, and allocate if there is no problem

- Banker's algorithm with dining lawyers:
  - safe if when try to grab chopstick either:
    - not last chopstick: 마지막이 아니니까 문제가 없다.
    - is last chopstick but someone will have two afterwards: 어쨌든 해결이 될 상황이기 때문에 문제가 없다.


-------

### [](#header-3) CPU scheduling

scheduling: deciding which threads are given access to resources from moment to moment


#### [](#header-4) Scheduling assumption

- many implicit assumptions for CPU scheduling:
  - one program per user
  - one thread per program
  - programs are independent

- what is fair?
  - A는 5개의 작업
  - B는 1개의 작업
  - 어떤게 fair한가?
    1. A와 B가 똑같은 양의 CPU를 받고 A는 그걸 5개로 쪼개서 씀
    2. A가 B의 5배 많은 CPU를 사용

- The high level goal: dole out CPU time to optimize some desired parameters of system (dole out: 조금씩 나눠주다)


#### [](#header-4) Scheduling policy goals / criteria

- minimize response time (it is what user sees, short burst)
- maximize throughput (long burst)
- fairness is not minimizing average response time, better average response time by making system less fair


#### [](#header-4) First come first served(FCFS) scheduling

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic67.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, FCFS example, <a href="https://www.youtube.com/watch?v=H2y762OQwYI&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=10">source</a> </div>

- waiting time: P1 = 0, P2 = 24, P3 = 27
- average waiting time: (0 + 24 + 27) / 3 = 17
- average completion time: (24 + 27  30) / 3 = 27
- **convoy effect: short process behind long process**

improvement can be made by reordering

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic68.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, FCFS example, <a href="https://www.youtube.com/watch?v=H2y762OQwYI&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=10">source</a> </div>

- waiting time: P1 = 6, P2 = 0, P3 = 3
- average waiting time: (6 + 0 + 3) / 3 = 3
- average completion time: (3 + 6  30) / 3 = 13

pros & cons
- simple (+)
- convoy effect (-) (even for processes, not for people, still important)


#### [](#header-4) Round Robin (RR)

- each process gets a small unit of CPU time, usually 10 - 100 milliseconds
- after quantum expires, the process is preempted and added to the end of the ready queue
- n processes in ready queue and time quantum is q
  - each process gets 1/n of the CPU time
  - in chunks of at most q time units
  - No process waits more than (n-1)q time units
- performance
  - q large = FCFS
  - q small = interleaved
  - q must be large with respect to context switch, otherwise overhead is too high

time tick이 있어서 시간이 지나면 넘기고 넘기고 하는 것이다. (직관적이고 이해하기 쉬움!)

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic69.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, RR example, <a href="https://www.youtube.com/watch?v=H2y762OQwYI&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=10">source</a> </div>

- waiting time:
  - P1 = (68 - 20) + (112-88) = 72
  - P2 = (20 - 0) = 20
  - P3 = (28 - 0) + (88 - 40) + (125 - 108) = 85
  - P4 = (48 - 0) + (106 - 68) = 88
- average waiting time: (72 + 20 + 85 + 88) / 4 = 66.25
- average completion time: (125 + 28 + 153 + 112) / 4 = 104.5

- pros & cons
  - better for short jobs, fair(+)
  - context time added (overhead, -)


#### [](#header-4) So which one is better?

- depends on your metrics




### [](#header-3) References

- *[Operating Systems and System Programming by John Kubiatowicz][ref1]*   

[ref1]:https://www.youtube.com/watch?v=H2y762OQwYI&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=10
