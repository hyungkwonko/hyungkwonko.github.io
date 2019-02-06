---
layout: post
title: OS Lecture Note 9 - Cooperating processes and deadlock
date: 2019-01-21 17:00:00
categories:
- Operating systems
tags:
- deadlock
author: "Hyung-Kwon Ko"
meta: "Seoul"
---

### [](#header-3) Review
- Semaphores: confusing because dual purpose - Both mutual exclusion and scheduling constraints
  - locks are for mutual exclusion
  - condition variables are for resource constraints - monitor
- monitor = lock + condition variable. Use of monitors is a way of programming paradigm.
- Lock: provides mutual exclusion to shared data
  - always acquire before accessing shared data structure
  - always release after finishing with shared data
- Condition variable: a queue of threads waiting for something inside a critical section
  - allow sleeping inside critical section by atomically releasing lock at time we go to sleep

-------

### [](#header-3) C language support for synchronization

- just make sure you know all the code paths out of a critical section (if some unexpected thing happens, you wanna make sure to catch it)


#### [](#header-4) setjmp and longjmp functions

```C
#include <stdio.h>
#include <setjmp.h>

static jmp_buf buf;

void second(void) {
    printf("second\n");       // 출력
    longjmp(buf,1);           // setjmp가 불려진 곳으로 점프하면서 setjmp가 1을 반환
}

void first(void) {
    second();
    printf("first\n");        // 출력되지 않음
}

int main() {   
    if (!setjmp(buf))
        first();              // 실행되면 setjmp가 0을 반환
    else                      // longjmp 점프가 되돌아가면 setjmp가 1을 반환
        printf("main\n");     // 출력

    return 0;
}
```

- setjmp의 기초적인 예시를 보여준다. 여기서 main()은 second()를 호출하는 first()를 호출한다. 다음, second()은 main()로 점프하면서 first()의 printf()의 호출을 생략한다. 코루틴(콜백함수)하고 비슷한 것 같음.
- 실행하면 다음과 같이 출력한다.

```C
second
main
```

-  way of doing non-local exit (kind of like an exception)


### [](#header-3) C++ language support for synchronization

- languages with exceptions like C++: problematic since it supports exceptions
- condider:
```C++
void Rtn() {
  lock.acquire();
  ...
  DoFoo();
  ...
  lock.release();
}

void DoFoo() {
  ...
  if(exception) throw errException;
  ...
}
```
- notice that an exception in DoFoo() will exit without releasing the lock! (락을 가진채로 exit)
- try catch문을 사용해서 해결한다.

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic61.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, C++ synchronization with try catch, <a href="https://www.youtube.com/watch?v=qcFS2aa8IRg&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=9">source</a> </div>

- 대안으로는 auto pointer를 사용하면 알아서 깔끔하게 lock해제 하는등 알아서 해제해준다.(GOOD!)



### [](#header-3) Java language support for synchronization

- java has explicit support for threads and thread synchronization
- every object has an associated lock which gets automatically acquired and released on entry and exit from a synchronized method.
- example code(**synchronized**):
```Java
class Account {
  private int balance;
  public Account(int iBalance) {
    balance = iBalance;
  }
  public synchronized int getBalance() {
    return balance;
  }
}
```
- also has synchronized statements
- works properly even with exceptions
- every object has a single condition variable associated with it


------


### [](#header-3) What is a resource?
things that threads need to do work: CPU time, disk space, memory
- preemptable: can take it away (I can take the CPU away from a thread - the thread won't make any progress)
- non-preemptable: must leave it with the thread (If I take it away from a thread, it's likely to have bad things that happen, 예를 들어 데이터 영역을 쓰레드에 할당했는데 도로 가져가버리면 쓰레드에 문제가 생길 수 있다.)

critical section에 들어가기 전에 lock을 가지는 이유는 한개의 쓰레드만 그 영역안으로 들어가도록 하는 것인데, preempt해버리면, critical section에 lock을 1개 이상 가지는 것을 허락한다는 말이 된다.

So, lock is a good example of a non-preemptable resource.

Resources can be exclusive or shareable.
- read-only files are clearly shareable
- printers are not shareable during the moment printer is in use

따라서 자원 관리는 운영체제의 중요한 작업 중 하나이다.

resources in general

We should start asking ourselves can we get into situations where too many people are requesting resources in the wrong order and the system gets deadlocked and doesn't make forward progress.


### [](#header-3) Starvation vs. Deadlock
- starvation: thread waits indefinitely (낮은 우선순위 등에 의해서 발생, reader/writer가 있는데 writer가 계속 있어버리면 reader가 진행을 못하는 상황이 있을 수 있다.)
- deadlock: circular waiting for resources(A is waiting for B, B is waiting for A... 꼬리물기)

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic62.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, Deadlock, <a href="https://www.youtube.com/watch?v=qcFS2aa8IRg&index=9&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws">source</a> </div>

- starvation can end
- deadlock can't end without external intervention

데드락은 외부에서 끝내줘야만 하고, starvation은 당연히 high-priority인 작업이 다 끝나면(물론 오래 걸리겠지만) low-priority인 작업이 진행됨.

All deadlock situations have a cycle of some sort involved in them. How to solve? figure out what the right cycle is.

싸이클이 있다고 해서 무조건 deadlock인 것은 아니다. Deadlock should involve a cycle that will never be resolved




<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic63.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, Train example(wormhole-routed network), <a href="https://www.youtube.com/watch?v=qcFS2aa8IRg&index=9&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws">source</a> </div>

- circular dependency (deadlock)
- remove dependency by adding some rules(e.g. east-west first and then north-south, 동서방향으로 먼저 가고 남북으로 하면 오른쪽 위랑 왼쪽 아래는 되는데 나머지 두개는 금지되므로 deadlock방지할 수 있다.)
- called dimension ordering(잡는 순서를 정해놓으면 됨. 그러면 cycle이 생기는 것을 방지할 수 있다.)

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic64.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, Dining lawyers problem, <a href="https://www.youtube.com/watch?v=qcFS2aa8IRg&index=9&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws">source</a> </div>

How to prevent deadlock?
- never let lawyer take last chopstick if no hungry lawyer has two chopsticks afterwards (아무도 2개의 젓가락 들고 있는 사람이 없는데 테이블에 젓가락이 1개만 남았으면 아무도 못들게 하는 것이다. 2개를 가질 사람만 들 수 있음... 데드락을 방지하는 rule!!)

How to fix deadlock?
- monitor (or the banked in this example) will cover the situation where deadlock might happen (전지전능하게 데드락을 방지하도록 하는 모니터링 하는 존재가 있는 것이다. 여기서는 그 존재를 banker라고 부름.)
  - make one of them give up a chopstick
  - eventually everyone will get chance to eat

this is what banker's algorithm is all about



### [](#header-3) Four requirements for deadlock
- mutual exclusion: only one thread at a time can use a resource
- hold and wait: thread holding at least one resource is waiting to acquire additional resources held by other threads
- no preemption: resources are released only voluntarily by the thread holding the resource, after thread is finished with it.
- circular wait: there exists a set of waiting threads
  - A is waiting for a resource that is held by B
  - B is waiting for a resource that is held by C
  - C is waiting for a resource that is held by A

if we have all these four condition, then we have a possibility of having a deadlock!

한개라도 만족을 하지 않으면 데드락이 생기지 않는다.

Could you make your lock system intelligent to prevent the cycles? YES - run the banker's algorithm
But it is potentially expensive and you may not want to do that.



### [](#header-3) Resource allocation graph
<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic65.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, Resource allocation graph, <a href="https://www.youtube.com/watch?v=qcFS2aa8IRg&index=9&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws">source</a> </div>
- 네모: 자원(안에 점이 할당할 수 있는 동일한 자원의 개수이다. 예를들어 R1은 1개, R2는 3개임 따라서 R2는 3개의 쓰레드에 동시에 할당 가능한 듯)
- 원: 쓰레드
- request edge: R자원을 T가 쓰려고 request한 상태(T->R로 가는 edge)
- assignment edge: R자원이 T에 할당된 상태(R->T로 가는 edge)


<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic66.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, Resource allocation graph example, <a href="https://www.youtube.com/watch?v=qcFS2aa8IRg&index=9&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws">source</a> </div>

- 2번째(가운데)는 deadlock
- 3번째(오른쪽)는 deadlock아님 (T2랑 T4는 독립적이니까 끝나면 T1이랑 T3가 순차적으로 R1이랑 R2를 쓰면 됨. starvation은 될 망정 deadlock은 아니다.)



### [](#header-3) methods for handling deadlock
- Allow system to enter deadlock and then recover
  - requires deadlock detection algorithm
  - some technique for forcibly preempting resources and terminating tasks
- Ensure that system will never enter a deadlock
  - need to monitor all lock acquisitions
  - selectively deny those that might lead to deadlock
- Ignore the problem and pretend that deadlocks never occur in the system
  - used by most operating systems, including UNIX


### [](#header-3) Deadlock detection algorithm
if you only have squares of resources where there is a single dot in it, all you have to do is to see if there is a cycle.(동일 자원이 딱 1개면 싸이클이 있는지 보고 있으면 deadlock)



### [](#header-3) References

- *[Operating Systems and System Programming by John Kubiatowicz][ref1]*   

- *[setjmp.h from wikipedia][ref2]*   

[ref1]:https://www.youtube.com/watch?v=qcFS2aa8IRg&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=9

[ref2]:https://ko.wikipedia.org/wiki/Setjmp.h
