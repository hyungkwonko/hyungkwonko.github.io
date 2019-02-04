---
layout: post
title: OS Lecture Note 13 - Address translation, Caches and TLBs
date: 2019-02-04 17:00:00
categories:
- Operating systems
tags:
- cache
- TLB
author: "Hyung-Kwon Ko"
meta: "Seoul"
use_math: true
---

## [](#header-2)<span style="color:#088A08"> *Review* </span>
The size of segments are different? YES and NO
- the actual part that I can address in the segments are clearly different sizes, however, if you were to look at the segment's in virtual space, you'd see that they are spaced out regularly.
- this address space has all the space there but the actual physical memory I've allocated is varying in sizes (what is allocated to each segment at the time is different)
- 한마디로 현재 상황에는 다르게 할당되어 있긴한데 실제로는 더 늘릴 수 있는 공간이 있기 때문에 다르다고 딱 잘라 말하기는 뭣함..

The reason two-level page table is advantageous is because just by marking things invalid at the first level, we could get rid of the things at the second level.

Do they exist or don't?

Multi-level translation 등에 대한 부분은 저번에 많이 했으므로 skip하겠음.

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic122.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, Two-level page table, <a href="https://www.youtube.com/watch?v=wQcKjMCC_Zk&index=13&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws">source</a> </div>

<br/>

Why the size of a page table entry should be 4-byte? (4 bytes = 32 bits)
- Page base address
- Accessed bit: whether there is an access to the page
- Dirty bit: if it is changed
- Present bit: if there are allocated frame
- Read/Write bit: authorization for reading / writing given page

For detailed explanation, please refer to:
- *[링크1 - Page table entries][ref3]*
- *[링크2 - 위키피디아][ref4]*



<br/><br/><br/>




## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[Operating Systems and System Programming by John Kubiatowicz][ref1]*   

- *[Multi-level page tables][ref2]*   

- *[Page table entries][ref3]*   

- *[페이지테이블 엔트리 - 위키피디아][ref4]*   

[ref1]:https://www.youtube.com/watch?v=wQcKjMCC_Zk&index=13&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws

[ref2]:https://www.youtube.com/watch?v=Z4kSOv49GNc

[ref3]:https://www.youtube.com/watch?v=OaTR3S3MPRw

[ref4]:https://ko.wikipedia.org/wiki/%ED%8E%98%EC%9D%B4%EC%A7%80_%ED%85%8C%EC%9D%B4%EB%B8%94
