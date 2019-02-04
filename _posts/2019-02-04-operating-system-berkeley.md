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

Multi-level translation 등에 대한 부분은 저번에 많이 했으므로 skip하겠음.


<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *Goals for today* </span>
- finish up address translation & protection
- caching and TLBs



<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *More on Address Translation* </span>

### [](#header-3)What is in a PTE (page table entry)

- what is in a PTE?
  - pointer to next-level page table or to actual page
  - permission bits: valid, read-only, read-write, write-only
- example: Intel x86 architecture PTE:
  - address same format previous slide (10, 10, 12-bit offset)
  - intermediate page tables called "directions"

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic123.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, page table entry, <a href="https://www.youtube.com/watch?v=wQcKjMCC_Zk&index=13&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws">source</a> </div>

<br/>

- P: present (same as valid bit in other architecture)
- W: writeable
- U: user accessible
- PWT: page write transparent: external cache write-through
- PCD: page cache disabled (page cannot be cached)
- A: accessed: page has been accessed recently
- D: dirty (PTE only): page has been modified recently
- L: if L=1, there is only **single level page table.** Bottom 22 bits of virtual address serve as offset (you don't need to swap a lot)

---

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

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic124.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Washington Univ., Two-level page table, <a href="https://courses.cs.washington.edu/courses/cse451/13wi/lectures/11%20-%20MM%20Hardware%20Support.pdf">source</a> </div>

<br/>

- master page table contains secondary page table number
- secondary page table contains page frame number (physical address)
---

<br/>

#### [](#header-4)Examples of how to use a PTE

- how do we use the PTE?
  - invalid PTE can imply different things:
    - Region of address space is actually invalid or
    - page/directory is just somewhere else than memory
  - validity checked first
    - OS can use other 31 bits for location info
- usage example: demand paging
  - keep inly active pages in memory
  - place others on disk and mark their PTEs invalid
- usage example: copy on write
  - UNIX fork gives copy of parent address space to child
    - address spaces disconnected after child created
  - how to do this cheaply?
    - make copy of parent's page tables (point at same memory)
    - mark entries in both sets of page tables as read-only
    - page fault on write creates two copies
  - usage example: zero fill on demand
    - new data pages must carry no information (say be zeroed)
    - mark PTEs as invalid; page fault on use gets zeroed page
    - often, OS creates zeroed pages in background

[가상 메모리 쓰기 시 복사 (copy-on-write) / zero-fill-on-demand 설명][ref5]














<br/><br/><br/>


## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[Operating Systems and System Programming by John Kubiatowicz][ref1]*   

- *[Multi-level page tables][ref2]*   

- *[Page table entries][ref3]*   

- *[페이지테이블 엔트리 - 위키피디아][ref4]*   

- *[가상 메모리 쓰기 시 복사 (copy-on-write)][ref5]*

[ref1]:https://www.youtube.com/watch?v=wQcKjMCC_Zk&index=13&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws

[ref2]:https://www.youtube.com/watch?v=Z4kSOv49GNc

[ref3]:https://www.youtube.com/watch?v=OaTR3S3MPRw

[ref4]:https://ko.wikipedia.org/wiki/%ED%8E%98%EC%9D%B4%EC%A7%80_%ED%85%8C%EC%9D%B4%EB%B8%94

[ref5]:https://sqlangeles.com/2018/01/11/%EA%B0%80%EC%83%81-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%93%B0%EA%B8%B0-%EC%8B%9C-%EB%B3%B5%EC%82%AC-copy-on-write/
