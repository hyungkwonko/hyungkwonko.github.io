---
layout: post
title: OS Lecture Note 12 - Protection & Address translation
date: 2019-01-30 17:00:00
categories:
- Operating systems
tags:
- protection
- virtual memory
author: "Hyung-Kwon Ko"
meta: "Seoul"
use_math: true
---

## [](#header-2)<span style="color:#088A08"> *Review* </span>

We started out by talking about multiplexing of memory resources.

Goals of memory resources:
1. Controlled overlap:
  - separate state of threads should not collide in physical memory.
  - conversely, would like the ability to overlap when desired (for communication)
2. Translation:
  - ability to translate accesses from one address space to a different one (virtual to physical, V2P, kind of hiding)
  - side effects:
    - can be used to avoid overlap
    - can be used to give uniform view of memory to programs
3. Protection:
  - prevent access to private memory of other processes
    - different pages of memory can be given special behavior
    - kernel data protected from user program
    - programs protected from themselves





















### [](#header-3) Banker's algorithm for preventing deadlock

<br/>

#### [](#header-4) is there any case that the Banker's algorithm won't work?


<br/><br/><br/>



<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic86.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, limit, <a href="https://www.youtube.com/watch?v=6AH5d1aPKB0&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=12">source</a> </div>


<br/><br/><br/>


## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[Operating Systems and System Programming by John Kubiatowicz][ref1]*   

[ref1]:https://www.youtube.com/watch?v=6AH5d1aPKB0&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=12
