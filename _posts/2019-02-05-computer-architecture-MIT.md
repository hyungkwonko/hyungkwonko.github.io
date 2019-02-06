---
layout: post
title: CA Lecture Note 13 - Building the beta
date: 2019-02-05 17:00:00
categories:
- Computer architecture
tags:
- memory hierarchy
author: "Hyung-Kwon Ko"
meta: "Seoul"
use_math: true
---
## [](#header-2)<span style="color:#088A08"> *Processor performance* </span>
- "Iron Law" of performance: <br>
$$\frac{Time}{Program} = \frac{Instructions}{Program} \cdot \frac{Cycles}{Instructions} \cdot \frac{Time}{Cycle}$$ <br>
$$Perf = \frac{1}{Time}$$

- options to reduce execution time:
  - LOW executed Instructions (HIGH work/instruction)
  - LOW cycles per instruction (CPI)
  - LOW cycle time (HIGH frequency)

We are going to have our entire instructions executed in a single cycle $$\rightarrow$$ CPI=1

**_Is it possible to make CPI less than 1?_** if two instructions are in a cycle, then CPI = 0.5

- but today: we will build a model with CRI = 1 and low frequency Beta
  - later: pipelining to increase frequency

## [](#header-2)<span style="color:#088A08"> *Reminder: programming languages* </span>
<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic108.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, outline of what we are learning, <a href="https://www.youtube.com/watch?v=EZJGLkoUw9M&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=13">source</a> </div>

<br/>




<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[Computer Architecture MIT 6.004 2015 by Chris Terman][ref1]*   

[ref1]:https://www.youtube.com/watch?v=EZJGLkoUw9M&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=13
