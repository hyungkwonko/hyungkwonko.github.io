---
layout: post
title: OS Lecture Note 12 - Protection & Address translation
date: 2019-01-30 17:00:00
categories:
- Operating systems
tags:
- segmentation
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

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic87.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, general address translation, <a href="https://www.youtube.com/watch?v=6AH5d1aPKB0&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=12">source</a> </div>

<br/>

By manipulating this blue region, we can get:
- control overlap
- translation
- protection

<br/>

### [](#header-3) Simple segmentation

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic88.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, simple segmentation: vase and bounds, <a href="https://www.youtube.com/watch?v=6AH5d1aPKB0&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=12">source</a> </div>

<br/>

The process running basically gets to think it's got address zero even though it doesn't. Can use base & bounds / limit for dynamic address
- alter every address by adding **BASE**
- generate error if address bigger than limit (protect above the limit)

<br/>

### [](#header-3) Cons for simple segmentation method

Fragmentation problem (complex memory allocation)
- not every process is the same size
- over time, memory space becomes fragmented
- really bad if want space to grow **dynamically**

Other problems for process maintenance
- doesn't allow heap and stack to grow independently
- want to put these as far apart in virtual memory space as possible so that they can grow as needed

Hard to do inter-process sharing
- want to share code segments when possible
- want to share memory between processes

**We want to do something better here**



<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *Goals for today* </span>

- address translation schemes:
  - segmentation
  - multi-level translation
  - paged page tables
  - inverted page tables
- discussion of dual mode operation
- comparison among options



<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *Segmentation* </span>

### [](#header-3)More flexible segmentation

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic89.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, More flexible segmentation, <a href="https://www.youtube.com/watch?v=6AH5d1aPKB0&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=12">source</a> </div>

<br/>

Logical view: multiple separate segments
  - typical: code, data, stack
  - others: memory sharing, etc

Each segment is given region of contiguous memory
  - has a base & limit
  - can reside anywhere in physical memory

still have an issue. what is that? if the size of chunk is same, it wouldn't be a problem. if we make some big ones and small ones, that is a problem.

<br/>

### [](#header-3)Implementation of multi-segment model

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic92.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, segment model, <a href="https://www.youtube.com/watch?v=6AH5d1aPKB0&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=12">source</a> </div>

<br/>

- Segment map resides in processor
  - segment number mapped into base / limit pair
  - base added to offset to generate physical address
  - error check catches offset out of range
- As many chunks of physical memory as entries
  - segment addressed by portion of virtual address
  - however, could be included in instruction instead
- What is V / N
  - can mark segments as invalid;  requires check as well

if the limit for a given segment is exceeded by the offset, that is an error. We may call a segmentation fault.

<br/>

### [](#header-3)Example: four segments (16 bit addresses)

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic90.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, four segments example, <a href="https://www.youtube.com/watch?v=6AH5d1aPKB0&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=12">source</a> </div>

<br/>

- This is the simplest translation you can come up with!
- set top 2 bit as a segment ID
- split the virtual address into four pieces
- left address(virtual address) get translated to the right address(physical address)
- what happens if we try to access an address that is not allocated(white region)? - segment fault happens!
- if we try to find more space for this segment, we could increase the limit, but notice the problem, we can't increase the limit of the pink one, because it is jammed up against the blue chunk. if we really want to allocate more, we have to copy it somewhere else and then we can increase the limit & change base

<br/>

### [](#header-3)Example of segment translation

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic91.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, segment translation example, <a href="https://www.youtube.com/watch?v=6AH5d1aPKB0&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=12">source</a> </div>

<br/>

- 0x240: when we convert this into binary it will be (**00** 00 0010 0100 0000)2 and the top two bits are zero & zero = we know its seg ID is 0 (code)
- all the addresses that the processor deal with are virtual addresses and the only time we ever translate is when we are going to DRAM
- when fetching 0x4050, virtual segment #? 1. why? 0x4050 = (**01** 00 0000 0000 0050)2
- why the offset is 0x50?: if we mask off the 01 at the meginning everything else is (01 **00 0000 0050**)2

<br/>

### [](#header-3) Observations about segmentation
- virtual address space has holes
  - segmentation efficient for sparse address spaces
  - a correct program should never address gaps
- when it is OK to address outside valid range:
  - this is how the stack and heap are allowed to grow
  - for instance, stack takes fault, system automatically increases size of stack
- need protection mode in segment table
  - for example, code segment would be read-only
  - data and stack would be read-write
  - shared segment could be read-only or read-write
- what must be saved / restored on context switch?
  - segment table stored in CPU, not in memory (small)
  - might store all of processes memory onto disk when switched

<br/>

### [](#header-3) Schematic view of swapping
can we have more memory use than we have? **YES** by Swapping

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic93.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, swapping, <a href="https://www.youtube.com/watch?v=6AH5d1aPKB0&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=12">source</a> </div>

<br/>

- extreme form of context switch: swapping
  - in order to make room for next process, some or all of the previous process is moved to disk
  - this greatly increases the cost of context-switching
- can have more flexibility in my memory management - so I can have a process that's not running doesn't have to be taking space in memory anymore. It can be out of disk.
- this is kind of expensive (have to fix a little bit, will cover)
- instead, wouldn't it be nice if we could put parts of a process out to memory? (you don't have to swap a whole segment in this case, better with respect to the cost)

- HOW TO IMPORT PART OF A SEGMENT?? $$\rightarrow$$ **WE NEED TO MAKE IT FINER CHOPPING UP THE SEGMENT** $$\rightarrow$$ **_PAGING_**


<br/><br/><br/>


## [](#header-2)<span style="color:#088A08"> *PAGING: physical memory in fixed size chunks* </span>

- problems with segmentation?
  - must fit variable-size chunks into physical memory
  - may move processes multiple times to fit everything
  - limited options for swapping to disk
- Fragmentation: wasted space
  - External: free gaps between allocated chunks
  - Internal: don't need all memory within allocated chunks
  - 그러니까 external은 덩어리와 덩어리 사이 낭비되는 공간이고 internal은 한 덩어리 안에서 사용중이지 않은 공간. 낭비되고 있다는 점에서 공통적이다.
- solution to fragmentation from segments?
  - allocate physical memory in fixed size chunks ("PAGES")
  - every chunk of physical memory is equivalent
    - can use simple vector of bits to handle allocation: 00110010100...10010
    - each bit represents page of physical memory: 1 = allocated, 0 = free


<br/>

### [](#header-3)How to implement paging?

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic94.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, page table & its pointer, <a href="https://www.youtube.com/watch?v=6AH5d1aPKB0&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=12">source</a> </div>

<br/>

- page table (one per process)
  - resides in physical memory
  - contains physical page and permission for each virtual page
    - permissions include: valid bits, read, write, etc
- virtual address mapping
  - offset from virtual address copied to physical address
    - example: 10 bit offset $$\rightarrow$$ 1024-byte pages
  - virtual page # is all remaining bits
    - example for 32-bits: 32-10 = 22 bits, i.e. 4 million entries
    - physical page # copied from table into physical address
    - check page table bounds(page table size error: check how many pages are in page table) and permissions(access error: whether it's read-only / write-only...)
    - why we are not checking the offset to see whether it's too big or not? because they are all the same size - offset part is not checked and it doesn't need to be(SIMPLE)
    - it doesn't have to be contiguously distributed in DRAM at all

- 설명하자면 페이징 기법에서는 p(페이지번호)와 d(offset) 두가지가 있어서 가상주소를 물리주소로 바꿀 때 p를 이용해서 페이지 번호를 알아내고 거기에 d를 더해서 실제 물리 주소를 알아낸다고 생각하면 된다. 페이지의 크기는 전체 동일하기 때문에 페이지 번호만 알면 해당 번호에 해당하는 페이지에 쉽게 접근할 수가 있다.
- 또한 p = page의 수, d = 페이지의 크기 라고 생각하면 된다. 따라서 p역시 page table 내부적인 offset이라고 생각할 수 있다.
- 페이지의 physical mapping은 연속적이지 않아도 된다는 장점이 있다.

<br/>

### [](#header-3)What about sharing?

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic95.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, shared page, <a href="https://www.youtube.com/watch?v=6AH5d1aPKB0&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=12">source</a> </div>

<br/>


### [](#header-3)Simple page table discussion
- what needs to be switched on a context switch?
  - page table pointer and limit (much simpler than before)
- Pros:
  - simple
  - easy to share
- Cons:
  - what if address space is sparse?
    - e.g. on UNIX, code starts at 0, stack starts at ($$2^{31} - 1$$)
    - with 1K pages, need 4 million page table entries
  - what if table really big?
    - not all pages used all the time $$\rightarrow$$ would be nice to have working set of page table in memory



<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *Multi-level translation: segments + pages* </span>

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic96.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, multi-level scheme, <a href="https://www.youtube.com/watch?v=6AH5d1aPKB0&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=12">source</a> </div>

<br/>

- what about a tree of tables?
  - lowest level page table = memory still allocated with bitmap
  - higher levels often segmented
- could have any number of levels
- what must be saver / restored on context switch?
  - contents of top-level segment registers
  - pointer to top-level table (page table)

<br/>

### [](#header-3)What about sharing?

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic97.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, sharing, <a href="https://www.youtube.com/watch?v=6AH5d1aPKB0&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=12">source</a> </div>

<br/>

- we can completely handle

<br/>

### [](#header-3)Two-level page table

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic98.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, two-level page table, <a href="https://www.youtube.com/watch?v=6AH5d1aPKB0&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=12">source</a> </div>

<br/>

- valid bits on page table entries
  - don't need every 2nd level table
  - even when exist, 2nd level tables can reside on disk if not in use
- Pros:
  - only need to allocate as many page table entries as we need for application
    - sparse address spaces are easy
  - easy memory allocation
  - easy sharing (share at segment or page level)
- Cons:
  - one pointer per page
  - page tables need to be contiguous
  - two(or more) lookups per reference
    - seems very expensive

<br/>

### [](#header-3)Inverted page table
- whti all previous examples ("Forward page tables")
  - size of page table is at least as large as amount of virtual memory allocated to processes
  - physical memory may be much less
    - much of process space may be out on disk or not in use

<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic99.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>John Kubiatowicz, inverted page table, <a href="https://www.youtube.com/watch?v=6AH5d1aPKB0&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=12">source</a> </div>

<br/>

- answer: to use a hash table
  - called an "inverted page table"
  - size is independent of virtual address space
  - directly related to amount of physical memory
  - very attractive option for 64bit address spaces(WHY? 64비트면 엄청나게 남으니까 굳이 다 할당할 필요가 없기 때문에)
- Cons: complexity of managing hash table

<br/>

### [](#header-3)Dual mode
- will cover next time


<br/><br/><br/>


## [](#header-2)<span style="color:#088A08"> *Summary* </span>

- Memory is a resource that must be shared
  - controlled overlap: only shared when appropriate
  - translation: change virtual addresses into physical addresses
  - protection: prevent unauthorized sharing of resources
- segment mapping
  - segment registers within processor
  - segment ID associated with each access
  - each segment contains base and limit information
- page tables
  - memory divided into fixed size chunks of memory
  - virtual page number from virtual address mapped through page table to physical page number
  - offset of virtual address same as physical address
  - large page tables can be places into virtual memory
- multi-level tables
  - virtual address mapped to series of tables
  - permit sparse population of address space



<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[Operating Systems and System Programming by John Kubiatowicz][ref1]*   

[ref1]:https://www.youtube.com/watch?v=6AH5d1aPKB0&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=12
