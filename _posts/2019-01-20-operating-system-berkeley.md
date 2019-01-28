---
layout: post
title: "OS Lecture Note 8 - language support for synchronization"
tagline: "8th lecture of Fall 2010 at UCB"
categories: [OS]
image: /thumbnail-mobile.png
author: "Hyung-Kwon Ko"
meta: "Seoul"
---

### [](#header-3) Review

We talked about how to use interrupts disabled as a meta mechanism to construct locks.

Can we build test & set locks without busy waiting?
- can't really, but can minimize.
- Idea: only busy-wait to atomically check lock value (very short busy-wait time, not spinning for long)



### [](#header-3) Semaphores

it has a non-negative integer value and supports two operations:
1. P(): an atomic operation that waits for Semaphore to become positive, then decrements it by 1 (wait operation)
2. V(): an atomic operation that increments the semaphore by 1 (signal operation)

such that the value ends up below zero will never happen

-------

### [](#header-3) Goals for today

- continue with synchronization abstractions
  - monitors and condition variables
- readers-writers problem and solution
- language support for synchronization


mutex is protecting the Enqueue() & Dequeue(). They are kind of non-atomic so they can screw each other up  by switching context and run simultaneously.

이 부분에서 한 학생이 큐말고 링크드 리스트를 사용하면 굳이 락을 쓸 필요가 없지 않냐고 질문했는데, 교수가 그게 훨씬 오류 발생률이 높고 어렵다고 깜.

Semaphores are confusing because they are dual purpose:
- mutual exclusion
- scheduling constraints

cleaner idea: use locks for mutual exclusion & use condition variables for scheduling constraints

Monitor: a lock and zero or more condition variables for managing concurrent access to shared data


<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic35.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, Simple monitor example, <a href="https://www.youtube.com/watch?v=VPwn07XI_n0&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=6">source</a> </div>

problem: we might get null. what do we do with null? I don't know, you loop and keep removing from queue over and over again... busy waiting. (NOT GOOD)
큐에 아무것도 들어있지 않은데 null을 return하는 작업을 반복하게 되고 busy waiting하고... 문제이다.
- it only uses a lock with no condition variable
- cannot put consumer to sleep if no work

How to solve: the thread that tries to execute remove from queue actually gets put to sleep until an item shows up



### [](#header-3) Condition variable

a queue of threads waiting for something inside a critical section
- Key idea: allow sleeping inside critical section by atomically releasing lock at time we go to sleep
- contrast to semaphores: can't wait inside critical section. condition variables are made for sleeping inside of locks while semaphore is not.
컨디션 변수는 크리티컬 섹션 안에서 잠들 수 있다.

operations:
- wait: atomically release lock and go to sleep. re-acquire lock later, before releasing.
- signal: wake up one waiter, if any (하나만 깨우기)
- broadcast: wake up all waiters (모두 깨우기)

rules: must hold lock when doing condition variable operations


<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic36.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, Complete monitor example, <a href="https://www.youtube.com/watch?v=VPwn07XI_n0&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=6">source</a> </div>

we can assure that the return of the removeFromQueue is not null since it wouldn't have exited the while loop if it was null.
watch here that I went to lock with the lock acquired (락이 있는 상태로 sleep했다는 것을 주목)

then you may think that
- if I have the lock
- and go to sleep
  - how can anybody add to the queue because they can never acquire the lock to add something to the queue(락을 가지고 자고있는데 어떻게 queue에 add가 가능?)

the answer is in the wait(): what wait does is to put you into sleep and releases the lock(wait에서 sleep을 시키고 lock을 release한다.)
and before you exit, it re-acquire the lock (다 끝나고 나가기 전에 다시 lock을 acquire)

But this happens inside the condition variable implementation so you as a programmer don't have to worry about it.

Everything between the acquire and the release is under lock and key. So we don't have to worry about anybody else coming in there and changing the conditions out from under us.



### [](#header-3) Mesa vs. Hoare monitors

two types of scheduling

Need to be careful about precise definition of signal and wait

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic37.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, "while" instead of "if", <a href="https://www.youtube.com/watch?v=VPwn07XI_n0&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=6">source</a> </div>

- answer depends on the type of scheduling
  - Hoare-style(여기서는 괜찮다. if써도 된다. 그러나 it is messy to implement for real):
    - Signaler gives the lock, CPU to waiter. waiter runs immediately
    - Waiter gives up lock, processor back to signaler when it exits critical section or if it waits again
  - Mesa-style:
    - signaler keeps lock and processor.
    - Waiter placed on ready queue with no special priority
    - **practically, need to check condition again after wait** (시그널에 의해 깨어난 프로세스가 즉시 실행되지 않고 ready-queue에 올라간다. 따라서 다시 상태를 체크해야 한다.)



### [](#header-3) Compare & swap for queue (atomic operation)

We can do an atomic add to linked list function with no locking

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic38.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, Compare and swap for queues, <a href="https://www.youtube.com/watch?v=VPwn07XI_n0&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=6">source</a> </div>

- **잘 이해가 안된다... 코드 봐서는... 다시 볼 것**


### [](#header-3) Reader / Writer problem
motivation: consider a shared database
- many readers / only one writer at a time
- readers: just read
- writers: modifying the database

SOLUTION:
- when writers are happening, we want to make sure they are in the simplest case, no readers going on
- read can't occur during a write
- only one writer at a time
- there can be more than one reader at a time
- 그니까 쓸 때는 한명만 쓰고 읽을 때는 쓰면 안됨

we'd like to have a separate synchronization for every record.

reader will wait until there is no writers access the database
it will wake up writers if they are waiting

State variables (protected by a lock):
- int AR: number of active readers
- int WR: number of waiting readers
- int AW: number of active writers
- int WW: number of waiting writers
these variables will make the solution simple.

------

### [](#header-3) Code for a Reader

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic39.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, Code for a Reader, <a href="https://www.youtube.com/watch?v=VPwn07XI_n0&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=6">source</a> </div>

performing is done outside of the lock so I can hold the lock as long as I want (락 바깥 부분에서 이루어지므로 오래 걸려도 상관 없다)
When reading is done, and if there is a waiting writer, I am going to signal a waiting writer.

++, -- 이건 원래 atomic하지가 않아서 함부로 쓰면 안되는데, since we acquired the lock, 우리가 락을 잡았기 때문에 맘대로 쓸 수 있는 것이다.

Why release the lock here? because somebody else can read - there might be a potential for more than one reader to actually make it into the database



### [](#header-3) Code for a Writer

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic40.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, Code for a Writer, <a href="https://www.youtube.com/watch?v=VPwn07XI_n0&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=6">source</a> </div>

if there is an Active Writer or Active Reader, we have to go to sleep.
WW++ is added since it is added to the waiting list
WW-- is followed, since it is no longer waiting

wake one writer(signal), if there is a waiting writer
wake all readers(broadcast), if there is a waiting reader

*even though there should be one writer, why do I release the lock?*
- we make sure that the reader code can actually get in there and generate waiting readers on the reader code
- general rule is that if you can set up your monitors so that you are not holding the lock for a huge period of time but rather for minimum that is required (기왕이면 짧은 구간을 락을 잡는게 좋다)

*why broadcast() instead of signal()?*
- we'd like every reader to be able to wake up. every reader is going to come out a wait one at a time (only one can hold the lock at a time)

*why does the code check for waiting writer first before it checks for waiting readers? why give priority to writers?*
- the writer has more up-to-date information
- we already give precedence to the writers in the above code. so it would be pointless if we change the priority. (이미 writer()코드 위쪽 부분에서 쓴 것 자체가 AW하고 AR있을 때 AW에 우선순위를 주는 식으로 코드를 짜 놓았기 때문에 그 순서를 따르는 것이다.)


### [](#header-3) Simulation of readers / writers solution
consider the following sequence of operators:
- R1, R2, W1, R3

코드 보면서 따라가면 됨.

Questions
- what if we erase the condition check in reader exit?
  - it is okay since they re-check the condition, if not go to sleep
- what if we turn the signal() into broadcast
  - okay since they recheck

However, the written code is the best for the economy. 굳이 깨웠다가 다시 잠드는 작업을 하게 할 필요는 없기 때문에 필요할 때만, 깨워서 뭔가를 진행할 때만 깨우는게 이득이다.

so the while loop is really rechecking the condition



### [](#header-3) Can we construct monitors from semaphores?

you've just deadlocked your system again. wait somehow needs to atomically release the lock after putting you to sleep

condition variables are not commutative
condition variables have history

semaphores are commutative
semaphores doesn't have history

what happens if a thread does signal and there is no one waiting? NOTHING

wait always put you to sleep

**이 부분 잘 모르겠음 다시 볼 것...**



### [](#header-3) Monitor Conclusion
- the logic of the program
- wait if necessary
- signal when a change happen, so waiting threads can proceed

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic42.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, General structure, <a href="https://www.youtube.com/watch?v=VPwn07XI_n0&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=6">source</a> </div>





### [](#header-3) References

- *[Operating Systems and System Programming by John Kubiatowicz][ref1]*   

[ref1]:https://www.youtube.com/watch?v=y-O3cihlfts&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=8
