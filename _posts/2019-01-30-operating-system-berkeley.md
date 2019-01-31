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





### [](#header-3) Banker's algorithm for preventing deadlock

<br/>

#### [](#header-4) is there any case that the Banker's algorithm won't work?


<br/><br/><br/>






<br/><br/><br/>


## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[Operating Systems and System Programming by John Kubiatowicz][ref1]*   

[ref1]:https://www.youtube.com/watch?v=6AH5d1aPKB0&list=PLggtecHMfYHA7j2rF7nZFgnepu_uPuYws&index=12
