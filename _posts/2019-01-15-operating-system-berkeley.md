---
layout: post
title: OS Lecture Note 7 - mutual exclusion, semaphores, monitors, and condition variables
date: 2019-01-15 17:00:00
categories:
- Operating systems
tags:
- mutual exclusion
- semaphore
- monitor
- condition variable
author: "Hyung-Kwon Ko"
meta: "Seoul"
---

Review...
ATM 기계 입금 예제를 통한 문제제기
atomic operation - an operation that always runs to completion or not at all  
busy waiting - 매우 소모적인 쓸모없는 CPU 낭비의 예시이다.


it is more than just load & store being atomic

------

the abstraction of threads are useful - it gives you the sequential execution model
you as a programmer can think it as a single thread and it's up to the OS to multiplex these so multiple threads are running at once you don't have to worry about that.
- 프로그래머는 걱정말고 그냥 한개의 thread인 것처럼 짜면되고, OS가 알아서 한다.

threads can run in parallelism but they introduce the need for a synchronization
anytime you have shared state between two threads they can screw each other up and you have to somehow deal with that.
so we will build a synchronization tool box, explore paradigms.

-------

suppose we have some sort of implementation of a lock
every synchronization operation potentially involves waiting. that is how we avoid screwing things up
if you end up waiting because the lock is busy, you can put that on wait queue, not be taking CPU time

한마디로 싱크로나이제이션은 waiting을 포함하게 되는데, 그러나 이게 무조건 busy waiting이어야 할 필요는 없다는 게 중요하다.
CPU를 잡아먹으면서 돌고 있는게 아니라 wait queue에 집어넣는다든지

lock.acquire = wait until lock is free, then grab
lock.release = unlock, waking up anyone waiting


lock.acquire should be atomic from the standpoint of people using it.  
for example, if two threads are waiting for the lock and both see it is free, only one succeeds to grab the lock  
락을 잡는 행위든, 푸는 행위든, 아토믹하게 이루어져야 한다.

this is easy and good for fraternity
- 우유 사는 예제에 맞춰서 장난식으로 말한 듯

critical section: the section of code between Acquire() and Release() that we are protecting
one thread can be inside of the critical section at once and that is what we are doing with the locks

I can have an arbitrarily large number of individual locks in the system
each one of them could protect something different

-----

So.. how to implement locks?
lock: prevents someone from doing something

(전에 사용했던 방법)load / store: pretty complex and error-prone (어렵고 버그 많음)


Naive use of interrupt enable / disable
can we build multi-instruction atomic operations?  YES
By disabling interrupts, what I am really doing is preventing the scheduler from switching.

저번에 배웠던 것: dispatcher gets control in two ways
1. internal: thread does something to relinquish the CPU
2. external: interrupts cause dispatcher to take CPU

On a uniprocessor, can avoid context-switching by
1. avoiding internal prevents
2. preventing external events by disabling interrupts

disabling interrupts has limited use, you have to be very careful that you are not competing with other CPUs or even other hardware threads as in a hyper threaded scenario.

locking out interrupts is also bad from I/O standpoint
so it is not so good to disable interrupts for a long period of time

---------

Better implementation
Key idea: maintain a lock variable and impose mutual exclusion only during operations on that variable
24분 경
interrupt 끄고 sleep으로 들어가면 누가 깨울 것인가 하는 문제 발생

---------

New lock implementation:
- why do we need to disable interrupts at all? to avoid two threads having lock at once, we treat this as a critical section  

disable / enable: very short time in here  
just during the period of acquiring and releasing the lock, not during the critical section the user programs using
you have to distinguish between the implementation of the lock from the use of the lock
락의 implementation과 use를 구분하는 것이 중요하다.

interrupt disable하는 부분은 바로 acquire 시작하고 하면 되는데, 그럼 enable 언제 하나?

we need to bring the kernel in here to help us a little bit
going to sleep process has to also reenable somehow, ATOMICALLY together

-------

how to re-enable after Sleep()?

interrupts are disabled when you call sleep
- responsibility of the next thread to re-enable interrupts
- when the sleeping thread wakes up, returns to acquire and re-enable interrupts

Other cases
- what about exceptions that occur after lock is acquired? who releases the lock?
  - for example, a = b / 0;
  - process gets killed and the lock is never released

Atomic read-modify-write instructions
- can't give lock implementation to users
- doesn't work well on multiprocessors - disabling interrupts on all processors requires messages and would be very time consuming

Alternative: atomic instruction sequences
- special instructions that do something like read a value from memory and write a value atomically in a way that cannot be interrupted and it turns out that is enough to build locks.

test&set operation architecture
zero(0): unlocked
one(1): locked
no matter how many people simultaneously try to get a lock only one of them will be lucky enough to catch the zero(0) and store the one(1)
everybody else will get a one(1) back.
한명만 0으로 설정된 것을 잡을 수 있고 나머지는 다시 1을 돌려 받는다. 1개 프로세스만 딱 0을 return받아서 락을 잡을 수 있음.

how do you release the lock? you write a zero..

compare & swap:

load-linked & store:

these are sophisticated enough that we can build locks out of them

Implementing locks with test & set
- if lock is free, test & set reads 0 and sets value=1, so lock is now busy. It returns 0 so while exits.
- If lock is busy, test & set reads 1 and sets value=1. It returns 1, so while loop continues.
- when we set value = 0, someone else can get lock

problem: busy waiting for lock(spin-lock이라서,,,)
positives for this solution:
1. machine can receive interrupts
2. user code can use this lock
3. works on a multiprocessors
negatives
1. this is very inefficient because the busy waiting thread will consume cycles waiting
2. waiting thread may take cycles away from thread holding lock
3. priority inversion: if busy waiting thread has higher priority than thread holding lock -> no progress!

for semaphores and monitors, waiting thread may wait for an arbitrary length of time... which is kind of bad if we are spin waiting

NEVER PUT BUSY-WAITING to your solution

if high priority one isn't making progress and the lower one is... -> NOT GOOD
- 우선순위에 따른 작동이 전혀 안하고 있음... 문제가 된다.

Better locks using test & set
- can we build test & set locks without busy-waiting?
  - Can't entirely, but we can minimize
  - idea: only busy-wait to atomically check lock value

guard variable and actual lock -
1. we grab the guard lock
2. we do our thing
3. we release the guard lock

2개의 변수를 사용하는 방법이다. 가드락을 추가로 쓰는 것!! -> busy waiting을 굉장히 적게 사용한다. 안쓸수는 없음...
even if you are stuck spinning, the person who's got the lock is going through very fast code and they are gonna release it quickly

이게 busy waiting을 쓰면서도 나름 괜찮은 방법이다. 그러나 justify해야함. busy wait쓰는 부분은...

and the sleep has to be sure to reset the guard variable at the same time you go to sleep

---------

we kind of have two high level synchronization techniques
1. semaphores
2. monitors

semaphores: generalized lock, special type of integer, non-negative inter value, and supports 2 operations
1. P(Proberen = test in Dutch 다익스트라가 네덜런드인 관계로 네덜런드말임) which is an atomic operation that waits for the semaphore to become positive then decrements it by one - weight
2. V(verhogen = increment in Dutch 네덜런드말) which increments the semaphore by one, if anybody is waiting on a P operation, it wakes them up - think of this as a signal

p is like an entrance point and v is like an exit point
p에서 시작해서 v에서 끝?

operations are atomic, so you do two P operations together, you will NEVER get below 0.
아토믹하게 이루어지기 때문에 0 밑으로 내려가지 못한다.  

P & V are atomic with respect to each other. they happen in some order.

can have many more states than locks
lock = 0 / 1
semaphores = 0 / 1 / 2 ... 양의 정수는 다 되니까 좋기도 하고 복잡하기도 하고(there are pros and cons)

Binary semaphore = the same as lock... = Mutual Exclusion = mutex
semaphore.P = acquire
semaphore.V = release

General rule of thumb: use a separate semaphore for each constraint

-----
Consumer / Producer example
빵집 예제
콜라 캔 예제..
다 똑같음

why asymmetry?
producer does: empty.P(), full.V()
consumer does: full.P(), empty.V()
producer / consumer goes to sleep and wake up on different events

the order of the P() matters. -> deadlock

the order of the V() matters...? doesn't matter
might affect schedule efficiency though

two producers and two consumers -> locking problem? no it works... even with 20 producers and 20 consumers, it will work

two items to deal with two different things
use locks for mutual Exclusion
use condition variables for scheduling constraints

in semaphore, you can never go to sleep while you are holding a lock ->deadlock
On contrary, condition variable is exactly what you are dealing with. if condition ain't right, you go to sleep

it is a stylized way of building synchronization and condition variables have been designed so that you can go to sleep while holding the lock...

잘 알아 둘 것 !!
컨디션 변수란 그것을 위한 것이다. 차이점을 잘 알아둘 것












### [](#header-2)References

- *[Operating Systems and System Programming by John Kubiatowicz][ref1]*   

[ref1]:https://www.youtube.com/watch?v=P19nmWurCz8&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=7
