---
layout: post
title: CA Lecture Note 11 - Procedures and stacks
date: 2019-02-01 17:00:00
categories:
- Computer architecture
tags:
- procedures
- stacks
author: "Hyung-Kwon Ko"
meta: "Seoul"
use_math: true
---

## [](#header-2)<span style="color:#088A08"> *What we are going to learn* </span>

To understand how the compilers do their job of translating what you write as a program into machine instructions, and partly we're evaluating our machine instructions to make sure we have everything we need.



<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *Multiple procedures* </span>

- procedure: reusable code fragment that performs a specific task
  - single named entry point
  - zero or more input arguments
  - local storage
  - returns control to the caller when finished
- using multiple procedures enables abstraction and reuse
  - compose large programs from collections of simple procedures

<br/>

### [](#header-3) Implementing multiple procedures
- option 1: inlining
  - compiler substitutes procedure call with body
  - problem?
    - code size
    - recursion (recurrence relation idea, divide & conquer strategy)
- option 2: linking
  - produce separate code for each procedure
  - caller evaluates input arguments, stores them and transfers control to the callee's entry point
  - callee runs, stores, result, transfers control to caller

<br/>

### [](#header-3) Procedure linkage: First try

```C
int fact(int n) {
  if(n > 0) {
    return n*fact(n-1);
  } else {
    return 1;
  }
}

fact(4);
```

The answer is: `fact(4) = 4*3*2*1*fact(0)`.

- need **calling convention**: uniform way to transfer data and control between procedures
- proposed convention:
  - pass argument (value of n) in R1
  - pass return address in R28
  - return result in R0

```C
fact(3);

main:   CMOVE(3, r1)    // pass argument
        BR(fact, r28)   // pass return address
        HALT()
```

<br/>

### [](#header-3) Procedure storage needs

- basic requirements for procedure calls:
  - input arguments
  - return address
  - results
- local storage:
  - variables that compiler can't fit in registers
  - space to save caller's register values for registers that we overwrite

Each of these is specific to a particular **activation** of a procedure. We call them the procedure's **activation record**

<br/>

### [](#header-3) Activation records

```C
int fact(int n) {
  if (n > 0) return n * fact(n-1);
  else return 1;
}
```

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic100.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Activation records, <a href="https://www.youtube.com/watch?v=TW2peBbHfH8&index=9&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

<br/>

1. A procedure call creates a new activation record.
2. Caller's record is preserved because we will need it when callee finally returns.
3. return to previous activation record when procedure finishes, permanently discarding activation record created by call we are returning from (kind of Last In First Out algorithm $$\rightarrow$$ this is where **STACK** comes out)
- 그러니까 fact(3)은 fact(2)를 호출하고 fact(2)는 fact(1)을 호출하고.. 이게 계속 연쇄적으로 발생하게 되고, 그 동안 fact(3)은 끝나지 못한다. 이런 LIFO 구조가 되기 때문에 stack이 적절하다.



<br/><br/><br/>


## [](#header-2)<span style="color:#088A08"> *Insight: we need a stack!* </span>

- need data structure to hold activation records
- activation records are created and discarded in last-in-first-out(LIFO) order
- only need access to activation record of currently executing procedure
- STACK: push, pop, access to top element

<br/>

### [](#header-3)Stack implementation

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic101.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, stack implementation, <a href="https://www.youtube.com/watch?v=TW2peBbHfH8&index=9&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

<br/>

- _CONVENTIONS_:
  - Dedicate a register for the stack pointer (SP), R29.
  - Builds up (towards higher addresses) on push
  - SP points to **FIRST UNUSED location**; locations below SP are allocated (protected).
  - Discipline: can use stack at any time; but leave it as you found it.
  - Reserve a large block of memory well away from our program and its data
- We use only software conventions to implement our stack (many architectures dedicate hardware)
- Other possible implementations include stacks that grow "down", SP points to top of stack, etc.

<br/>

### [](#header-3)Stack management macros

- PUSH (RX): push reg[x] onto stack
  - reg[SP] $$\leftarrow$$ reg[SP] + 4;
  - mem[reg[SP] - 4] $$\leftarrow$$ reg[x]
  - first allocate the location by moving the stack pointer
  - second reach back and fill it in
  - allocate the location before we use it
- POP (RX): pop value on top of the stack into reg[x]
  - reg[SP] $$\leftarrow$$ mem[reg[SP] - 4];
  - reg[SP] $$\leftarrow$$ reg[SP] - 4;
  - (reverse order compared to above PUSH instruction)
  - first take the value out of the memory
  - second carefully move the pointer
  - we don't deallocate it until we are completely done with it
- ALLOCATE (k): reserve k words of stack
  - reg[SP] $$\leftarrow$$ reg[SP] + 4*k
- DEALLOCATE (k): release k words of stack
  - ref[SP] $$\leftarrow$$ reg[SP] - 4*k

All of this are manipulating the contents of the SP register which is an memory address.

We are going to use the stack as a place to build our activation records, and when we look at the stack after running a program we will see many procedure calls in it which are currently active procedure calls.

<br/>

### [](#header-3)Fun with stacks

we can squirrel away(나중에 쓰려고 감춰두다) for later.

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic102.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, save variables for later, <a href="https://www.youtube.com/watch?v=TW2peBbHfH8&index=9&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

<br/>

- data is popped off the stack in the opposite order that it is pushed on

<br/>

### [](#header-3)Solving procedure linkage problems

- Reminder: procedure storage needs
  1. we need a way to pass arguments into procedures
  2. procedures need their own LOCAL storage
  3. procedures need to call other procedures
  4. procedures might call themselves (recursion)
- BUT first, we will commit some more registers:
  - r27 = BP: base pointer, points into stack to the local variables of callee
  - r28 = LP: linkage pointer, return address to caller
  - r29 = SP: stack pointer, points to 1st unused word

<br/>

### [](#header-3)Stack frames as activation records

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic103.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, stack frame as activation record, <a href="https://www.youtube.com/watch?v=TW2peBbHfH8&index=9&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

<br/>

- CALLEE uses stack for all of the following storage needs:
  1. saving return address back to the caller
  2. saving the CALLER's base ptr
  3. creating its own local / temp variables

BP register holds the address of this data structure (all the local and temporary stuff get added into this)

BP is a convention: in theory, it is possible to use SP to access stack frame, but offsets will change due to PUSHs and POPs. For convenience we use BP so we can use constant offsets to find, e.g., the first argument.

<br/>

### [](#header-3)Stack frames details

F(1,2,3,4) translate to:
```C
ADDC(R31, 4, R0)
PUSH(R0)
ADDC(R31, 3, R0)
PUSH(R0)
ADDC(R31, 2, R0)
PUSH(R0)
ADDC(R31, 1, R0)
PUSH(R0)
BR(F, LP)
```

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic104.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, stack frame details, <a href="https://www.youtube.com/watch?v=TW2peBbHfH8&index=9&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

<br/>

`F(1,2,3,4)` $$\rightarrow$$ `4 - 3 - 2 - 1`
In C / Java or any of these language, it will evaluate the right expression first $$\rightarrow$$ left expression second.

why push args in **REVERSE ORDER?**
1. it allows the BP to serve double duties when accessing the local frame
  - to access k-th local variable:
    - LD(BP, k*4, rx) or ST(rx, k*4, BP)
  - to access j-th argument:
    - LD(BP, -4*(j+3), rx) or ST(rx, -4*(j+3), BP)

    <div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic105.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, argument accessing, <a href="https://www.youtube.com/watch?v=TW2peBbHfH8&index=9&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

2. CALLEE can access the first few arguments without knowing how many arguments have been passed

- 한글로 설명하면 BP를 중심으로 위로는 arg[0]를 fixed offset으로 접근하고 local[0]를 fixed offset으로 접근하는 더블 듀티 / 변수가 몇개인지 모르는 상황에서도(n개라고 치면 n개가 몇개인지 몰라도) 처음 몇 개에 접근할 수 있다는 장점이 있다.

<br/>

### [](#header-3)Procedure linkage: the contract

1. The caller will:
  - push args onto stack, in reverse order
  - branch to callee, putting return address into LP
  - remove args from stack on return
2. The callee will:
  - perform promised computation, leaving result in R0
  - branch to return address
  - leave stacked data intact, including stacked args
  - leave regs (except R0) unchanged




<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *Tough problems* </span>

### [](#header-3)NON-LOCAL variable access, particularly in nested procedure definitions

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic107.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, non local variable access, <a href="https://www.youtube.com/watch?v=TW2peBbHfH8&index=9&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

<br/>

- conventional solutions:
  - environments, closures
  - static links in stack frames, pointing to frames of statically enclosing blocks. This allows a run-time discipline which correctly accesses variables in enclosing blocks. BUT enclosing block may no longer exist. (C avoids this problem by outlawing nested procedure declarations!)

- 클로저: 내부 함수가 외부함수의 지역 변수에 접근할 수 있도록 하는 것. 위에 영어 설명이 이해가 안간다면 [생활코딩 링크를 참조 (1-4강)][ref2]

<br/>

### [](#header-3)Dangling references

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic106.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Dangling reference, <a href="https://www.youtube.com/watch?v=TW2peBbHfH8&index=9&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

<br/>

```C
int* p;

int h(int x) {
  int y = x*3;
  p = &y;
  return 37;
}
h(10);
print(*p); // garbage printed. what happened?
```
What do we expect? _crashes, undefined / unreproducible behaviors..._

<br/>

### [](#header-3)Memory safety

- C / C++: unrestricted pointers and pointer arithmetic - easy to shoot yourself in the foot(제 발등을 찍기 쉽다.)

- Java / Python / etc: memory safety:
  - language does not have constructs that can lead to dangling references
  - storage managed automatically: garbage collection, reference counting
  - (easier to write correct programs, and that is why these languages are widely used these days)



<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[Computer Architecture MIT 6.004 2015 by Chris Terman][ref1]*   

- *[자바스크립트 클로저 개념 설명 - 생활코딩][ref2]*   

[ref1]:https://www.youtube.com/watch?v=FtY7z39FZAM&index=11&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7

[ref2]:https://opentutorials.org/course/743/6544
