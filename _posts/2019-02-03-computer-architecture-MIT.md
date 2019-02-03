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
| :--------- | :--------- | :--------- |
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

- bare minimum for a functional compiler:

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic112.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, compilers, <a href="https://www.youtube.com/watch?v=6X8fVZcD3aY&index=12&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

<br/>

- good compilers:
  - produce meaningful errors on incorrect programs
    - even better: meaningful warnings
  - produce fast, optimized code

- this lecture:
  - simple techniques to compile a C procedure into assembly
  - overview of how modern compilers work

<br/>  

### [](#header-3)Compiling expressions

We are going to use load & store, and it actually needs the address of given variables.

- Variables are assigned memory locations and accessed via LD or ST
- Operators translate to ALU instructions
- Small constants translate to ALU instructions with constant
- Large constants translate to initialized variables

<br/>

### [](#header-3)Data structures: Arrays

Address: Constant base address + variable offset computed from index

<br/>

### [](#header-3)Conditionals & Loops

...

<br/>


## [](#header-2)<span style="color:#088A08"> *Anatomy of a modern compiler* </span>

ORDER: Source code $$\rightarrow$$ **Frontend - Optimizer - Backend** $$\rightarrow$$ Machine code

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic117.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Dave Xiang, LLVM architecture, <a href="https://www.youtube.com/watch?v=KA8hFBh2eiw">source</a> </div>

<br/>

**LLVM**: A collection of compiler and toolchain(bunch of tools), which consists of three layers.

1. Front-end **(NOT Javascript)**
  - takes our favorite languages, and translates it LLVM-IR
2. The middle
  - the most important part of LLVM (intermediate representation)
  - think of this as LLVM's special language. we will call this LLVM-IR from now on.
3. Back-end
  - take LLVM-IR, and translates it into architecture=specific machine code. e.g., X86 / ARM etc

*[LLVM에 대한 자세한 설명 GOOD][ref5]*

<br/>

### [](#header-3)Frontend stages

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic114.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Lexical anaylsis, <a href="https://www.youtube.com/watch?v=6X8fVZcD3aY&index=12&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic115.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, syntactic analysis, <a href="https://www.youtube.com/watch?v=6X8fVZcD3aY&index=12&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic116.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, semantic analysis, <a href="https://www.youtube.com/watch?v=6X8fVZcD3aY&index=12&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

<br/>

- Lexical analysis (scanning): source to tokens: the lexical analyzer basically turns it into a list of tokens, so it understands the syntax of the language
- syntactic analysis (parsing): tokens to syntax tree: figure out the structure you have described. what the expression trees are... and so on.
- semantic analysis (mainly, type checking): show warnings

<br/>

### [](#header-3)intermediate representation (IR)

we turn everything to bits right away, we lose some opportunities to do an intelligent jobs of optimizing the program

- internal compiler language that is:
  - language-independent
  - machine-independent
  - easy to optimize
- why yet another language?
  - assembly does not have enough info to optimize it well
  - enables modularity and reuse

<br/>

### [](#header-3)Common IR: control flow graph

- basic block: sequence of assignments with an optional branch at the end
- control flow graph:
  - nodes: basic blocks
  - edges: jumps or branches between basic blocks

<br/>

#### [](#header-4)Control flow graph for GCD

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic118.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, CFG of GCD, <a href="https://www.youtube.com/watch?v=6X8fVZcD3aY&index=12&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

<br/>

- looks like a high level FSM

<br/>

### [](#header-3)IR optimization

- perform a set of passes over the CFG
  - each pass does a specific, simple task over the CFG
  - by repeating multiple simple passes on the CFG over and over, compilers achieve very complex optimizations
- example optimizations:
  - dead code elimination: eliminate assignments to variables that are never used, or basic blocks that are never reached (안쓰면, 안가면 제거)
  - constant propagation: identify variables that are constant, substitute the constant elsewhere (변수를 상수로)
  - constant folding: compute and substitute constant expressions

<br/>

#### [](#header-4)Example IR optimizations

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic119.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, CFG of GCD, <a href="https://www.youtube.com/watch?v=6X8fVZcD3aY&index=12&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

<br/>

- we can get rid of the instructions that are never used in the program (dead code elim)
- variable $$\rightarrow$$ constant (constant propagation)
- we now can do constant folding make it constant if possible (constant folding)
- 이해 안되면 동영상 40-41분 참고하기

- repeat the process.... until it reaches the unchangeable state (final tree)

- More optimizations by adding passes: common subexpression elimination, loop-invariant code motion, loop unrolling

<br/>

### [](#header-3)Putting it all together: GCD

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic120.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, GCD code, <a href="https://www.youtube.com/watch?v=6X8fVZcD3aY&index=12&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

<br/>

- we can make it compact at last



<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *Summary: Modern compilers* </span>

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic121.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, modern compilers, <a href="https://www.youtube.com/watch?v=6X8fVZcD3aY&index=12&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>




<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[Computer Architecture MIT 6.004 2015 by Chris Terman][ref1]*   

- *[Alternative video for better sound quality, content is the same though][ref2]*   

- *[컴퓨터 과학이 여는 세계 - 상위언어의 해설실행, 서울대학교 이광근][ref3]*   

- *[Compilation vs Interpretation][ref4]*  

- *[What is LLVM? What Makes Swift Possible?][ref5]*

[ref1]:https://www.youtube.com/watch?v=6X8fVZcD3aY&index=12&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7

[ref2]:https://www.youtube.com/watch?v=_njvKwIizzQ

[ref3]:https://www.youtube.com/watch?v=8yMdCnVNai0&list=PL0Nf1KJu6Ui7yoc9RQ2TiiYL9Z0MKoggH&index=50

[ref4]:https://www.youtube.com/watch?v=JNMy969SjyU

[ref5]:https://www.youtube.com/watch?v=KA8hFBh2eiw
