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




### [](#header-3) Datapath for factorial

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic70.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Circuit for factorial, <a href="https://www.youtube.com/watch?v=TW2peBbHfH8&index=9&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>




## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[Computer Architecture MIT 6.004 2015 by Chris Terman][ref1]*   

[ref1]:https://www.youtube.com/watch?v=FtY7z39FZAM&index=11&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7
