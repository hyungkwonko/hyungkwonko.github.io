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

model of compilation: 





<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[Computer Architecture MIT 6.004 2015 by Chris Terman][ref1]*   

- *[Alternative video for better sound quality, content is the same though][ref1]*   

[ref1]:https://www.youtube.com/watch?v=6X8fVZcD3aY&index=12&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7

[ref2]:https://www.youtube.com/watch?v=_njvKwIizzQ
