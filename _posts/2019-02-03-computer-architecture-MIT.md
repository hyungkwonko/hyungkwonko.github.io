---
layout: post
title: CA Lecture Note 12 - Compilers
date: 2019-02-03 17:00:00
categories:
- Computer architecture
tags:
- compiler
author: "Hyung-Kwon Ko"
meta: "Seoul"
use_math: true
---

## [](#header-2)<span style="color:#088A08"> *Reminder: programming languages* </span>

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic108.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, outline of what we are learning, <a href="https://www.youtube.com/watch?v=6X8fVZcD3aY&index=12&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

<br/>

- last time we talked about assembly language, now we will talk about high level language


<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *High-level languages* </span>

- advantages over assembly:
  - productivity (concise, readable, maintainable)
  - correctness (type checking, etc)
  - portability (run same program on different hardware)
- disadvantages over assembly?
  - efficiency...?

Implementations: interpretation vs. compilation

<br/>

### [](#header-3)Interpretation

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic109.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Model of interpretation, <a href="https://www.youtube.com/watch?v=6X8fVZcD3aY&index=12&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

<br/>

Model of interpretation:
  - start with some hard-to-program universal machine, say M1
  - write a single program for M1 which mimics the behavior of some easier machine, say M2
  - Result: a "virtual" M2

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic110.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Layers of interpretation, <a href="https://www.youtube.com/watch?v=6X8fVZcD3aY&index=12&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

<br/>

Layers of interpretation:
  - often we use several layers of interpretation to achieve desired behavior, e.g.:
  - x86 CPU, running
    - python, running
      - application, interpreting
        - Data, ...

- you don;t have to expense all the compilation steps, cause it's very quick & easy. the same thing can mean different things depending on whether the actual piece of data you have is a string / number / float ...
- interpreter can provide very flexible set of semantics (often typeless, depend on the instantaneous value that you are manipulating)
- virtual machine becomes increasingly tailored to do just the job we wanted to do, and the rest of the details are taken care of by the layers of interpretation

<br/>

### [](#header-3)Compilation

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic111.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, compilation, <a href="https://www.youtube.com/watch?v=6X8fVZcD3aY&index=12&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

<br/>

model of compilation:
- given some hard-to-program machine, say M1...
- find some easier-to-program language L2 (perhaps for a more complicated machine, M2), write programs in that language
- build a translator (compiler) that translates programs from M2's language to M1's language. may run on M1, M2, or some other machine

Interpretation and compilation: two ways to execute high-level languages
- both allow changes in the programming language
- both afford programming applications in platform independent languages
- both are widely used in modern computer systems!



- what the semantics are of a high level language statement, each time it needs to be executed, we think about it once at compile time, we make all the decisions, and then we produce exactly the machine instructions that should be executed
- higher execution performance (we never look at the source code again)
- Java actually uses a hybrid strategy, as they started off as interpreters and they use what's called a just-in-time compiler to convert often use fragment of your code into machine language, which then executes much more efficiently.

<br/>

### [](#header-3)Interpretation vs. compilation
- 두가지 방법의 장단점 비교

Compilation:
- Pros:
  - fast
  - source code private
- Cons:
  - not cross-platform
  - requires extra compiling step (longer to develop)
Interpretation:
- Pros:
  - cross platform
  - no extra step
  - easier debugging
- Cons:
  - slower
  - public source

<br/>

|      | Interpretation     | Compilation |
| :------------- | :------------- | :------------- |
| How it treats input "x+2"       | computes x+2       |generates a program that computes x+2|
|when it happens   |  during execution |before execution   |
|what it complicates   |program execution   |program development   |
|decision made at   |run time   |compile time   |

- major choice we will see repeatedly: do it at compile time or at run time?
  - which is faster?
  - which is more general?

**YOU SEE ALL SORT OF MIXED STRATEGIES THESE DAYS**



<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *Compilers* </span>













<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[Computer Architecture MIT 6.004 2015 by Chris Terman][ref1]*   

- *[Alternative video for better sound quality, content is the same though][ref1]*   

- *[컴퓨터 과학기 여는 세계 - 상위 언어의 해설 실행, 서울대학교 이광근][ref3]*   

- *[Compilation vs Interpretation][ref4]*  

[ref1]:https://www.youtube.com/watch?v=6X8fVZcD3aY&index=12&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7

[ref2]:https://www.youtube.com/watch?v=_njvKwIizzQ

[ref3]:https://www.youtube.com/watch?v=8yMdCnVNai0&list=PL0Nf1KJu6Ui7yoc9RQ2TiiYL9Z0MKoggH&index=50

[ref4]:https://www.youtube.com/watch?v=JNMy969SjyU
