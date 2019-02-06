---
layout: post
title: CA Lecture Note 9 - Programmable machines
date: 2019-01-24 17:00:00
categories:
- Computer architecture
tags:
- programmable machine
author: "Hyung-Kwon Ko"
meta: "Seoul"
use_math: true
---


### [](#header-3) Chapter 2 - programmable Architectures
- The hardware-software interface
- From high level programming language to the language of hardware
- Modern computer design
  - simple processor implementation
  - the memory hierarchy
  - performance through parallelism (pipelining & multicore)


### [](#header-3) Datapath for factorial

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic70.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Circuit for factorial, <a href="https://www.youtube.com/watch?v=TW2peBbHfH8&index=9&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

Start: a = 1, b = N
loop: a = a * b, b = b - 1
done: a = a, b = b

though there is a lot of hardware here, the actual controlling of the hardware, it turns out to be pretty simple

We need to know whether b is 0 or not, so we have some sort of comparison circuit



### [](#header-3) So far: single purpose hardware

- Systematic way to implement high level FSM as a datapath + control FSM
  - if so, can you draw the truth table? 2^66 = which is a big number. you know, there might be for example 2^80 atoms in the universe... Even though in theory everything that we will talk about for the rest of the semester, conceptually, from a mathematician's point of view is FSM, it turns out not to be the best way to think ahead of it as from a design point of view (트루쓰 테이블 그릴수 없다... 디자인적인 관점에서 모든 케이스를 다 보고 해버리는 것은 별로 좋지 않은 생각이라는 것)

- How can make one machine that can do many different things well?
  - more storage for operands and results
  - a larger repertoire of operations
  - general purpose datapath


selecting one of the registers

control signals identify what todo with the answer

------


### [](#header-3) A simple programmable datapath

actually two separate operations that have to happen one after the other. we can't do them both simultaneously in the same clock cycle of our machine (동시에 불가능)
- a & b registers as the two operands to do a multiply on
- select B as one of the registers and perform a minus one operation


### [](#header-3) New problem = new control FSM

- you can solve many more problems with this datapath
  - but nothing that requires more than 4 registers
- by designing a control FSM, we are programming the datapath
- early digital computers were programmed this way
  - ENIAC is the first general purpose digital computer



### [](#header-3) The von Neumann model (폰노이만 모델)

- many approaches to build a general purpose computer
- almost all modern computers are based on the von Neumann model
- components:

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic71.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, von Neumann machine, <a href="https://www.youtube.com/watch?v=TW2peBbHfH8&index=9&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

main memory: hold everything that we will need to manipulate (우리가 필요한 모든 것을 저장, CPU로 작업이 필요할 때 여기서 불러들여서 작업을 한다.)


#### [](#header-4) Main memory = random-access memory(RAM)

- 2d array of bits, organized in W words of N bits each
  - typically, W is a power of two: W = 2^k

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic72.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, random-access memory, <a href="https://www.youtube.com/watch?v=TW2peBbHfH8&index=9&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

메모리는 읽거나 쓸 때 주소를 이용해 접근하는 특성을 갖는다.

- can read from and write to individual words
  - compare with ROM(read-only memory)
  - many possible implementations (later in the course 나중에 다루겠다)


#### [](#header-4) Stored program computer

- express program as a sequence of coded instructions
- memory holds both data and instructions (데이터 뿐만 아니라 명령까지 포함되어 있다.)
- CPU fetches, interprets, and executes successive instructions of the program (fetch: 가지고 오다, CPU가 연속적인 명령을 가져오고 해석하고 실행한다)

you can tell the computer where the next instruction comes from

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic73.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Anatomy of a von Neumann computer, <a href="https://www.youtube.com/watch?v=TW2peBbHfH8&index=9&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

Datapath(brawn) doesn't do much, it just react to what is given.

Control unit(brain) reads instructions from memory, and figure out for each instruction what is the right setting for the control signals

(사람하고 똑같은 듯.. 뇌가 일을 하는거고 근육은 뇌의 명령에 따라 반응 하는 것 뿐이다.)

마우스 있는 부분 (PC: program counter): index and memory of where the next instruction is coming from


### [](#header-3) instructions

- instructions are the fundamental unit of work
- each instruction specifies:
  - an operation or opcode to be performed
  - source and destination operands
- In a von Neumann machine, instructions are executed sequentially

  1. fetch instruction
  2. decode instruction
  3. read src operands (datapath)
  4. execute (datapath)
  5. write dst operands (datapath)
  6. compute next PC

#### [](#header-4) instruction set architecture (ISA)
- ISA: the contract between software and hardware (detailed description of what each instruction does at the bit level, 자세히 뭘 하는지 설명, 하드웨어 - 소프트웨어의 소통)
- precise description of how software can invoke and access them

- the ISA is a new layer of abstraction: what the operations are
  - it specifies what the hardware provids, not how it is implemented
  - hides the complexity of CPU implementation
  - enables fast innovation in hardware
  - Downside: commercially successful ISAs last for decades. There are only two ISAs - x86, ARM(advanced risk machine)


#### [](#header-4) instruction set architecture design

- designing an ISA is hard:
  - how many operations?
  - what types of storage, how much?
  - how to encode instructions?
  - how to future-proof?
- How to decide? Take a quantitative approach
  - take a set of representative benchmark programs
  - evaluate versions of your ISA and implementation with and without feature
  - pick what works best overall
- OPTIMIZE THE COMMON CASE (thing that you do 99% of the time = hardware)


#### [](#header-4) Beta ISA: Storage

r31 = 0 (you get 0 always)

separate 32 bit register that holds the address of the next instruction in main memory, and mostly that is just going from one instruction to the next

registers are very fast, main memory is a lot slower

why separate registers and main memory? tradeoff between size and speed


#### [](#header-4) Storage conventions
- variable live in memory
- registers hold temporary values
- to operate with memory variables
  - load them
  - compute on them
  - store the results


#### [](#header-4) Beta ISA: instructions

- three types of instructions:
  - arithmetic and logical
  - branches: conditionally change the program counter
  - loads and stores: move data between general registers and main memory
- all instructions have a fixed length: 32 bits (4 bytes)
  - tradeoff (vs variable-length instructions):
    - simpler decoding logic, next PC is easy to compute
    - larger code size

그러니까 가변 길이로 인코딩 하면 사이즈가 작아지는 장점이 있고 고정 길이로 인코딩하면 디코딩 로직이 쉽고 next PC가 계산하기 쉽다는 장점이 있고 그게 트레이드 오프이다.



#### [](#header-4) Beta ALU instructions

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic74.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Example coded instruction(ADD), <a href="https://www.youtube.com/watch?v=TW2peBbHfH8&index=9&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

- OPCODE라고 되어 있는 부분에 6개 비트가 있는데 이로써 총 2^6개 = 64개가 표현되어 있고 총 64개의 instruction을 실행할 수 있다는 뜻이 된다. 100000에는 지금 ADD operation이 할당된 것임.
- 다음 5자리는 rc의 레지스터 번호인데, 5자리 = 2^5 = 32개이니까 총 32개의 레지스터 중 한개를 고른다고 생각하면 된다. 다음과 다다음인 ra랑 rb도 마찬가지임
- 나머지 맨 뒤에는 unused


#### [](#header-4) Beta ALU instructions with constant

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic75.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Example coded instruction2(ADD), <a href="https://www.youtube.com/watch?v=TW2peBbHfH8&index=9&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

- 위랑 다른점은 rb가 레지스터에서 온게 아니라 인스트럭션 자체에서 온다는 점이다.


#### [](#header-4) Why have instructions with constants?
- many programs use small constants frequently
  - tradeoff:
    - when used, they save registers and instructions
    - more opcodes = more complex control logic and datapath









### [](#header-3)References

- *[Computer Architecture MIT 6.004 2015 by Chris Terman][ref1]*   

[ref1]:https://www.youtube.com/watch?v=TW2peBbHfH8&index=9&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7
