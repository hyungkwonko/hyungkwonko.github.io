---
layout: post
title: 서효중 교수 컴퓨터 구조 수업 노트 5-1
date: 2019-03-04 17:02:00
categories:
- Computer architecture
tags:
- none
author: "Hyung-Kwon Ko"
meta: "Seoul"
use_math: true
---
## [](#header-2)<span style="color:#088A08"> *MIPS instruction set architecture* </span>
- 80386: 32-bit extension(now IA-32, intel architecture 32)
  - Additional addressing modes and operations
  - Paged memory mapping as well as segments (가상 메모리의 등장)
- i486: pipelined, on-chip caches and Floating Point Unit (캐시 메모리의 등장!! 이전에도 캐시가 있긴 했지만 칩 밖에 있었음.)
  - compatitors: AMD, Cytrix (486 전까지는 Cross license를 시행: Processor에 대한 license를 공유하였음, 서로가 서로꺼를 만들 수 있도록, 그러다가 386때 intel이 크게 성공하면서 cross license가 종료됨)

<br>

## [](#header-2)<span style="color:#088A08"> *x86 instruction encoding* </span>
- Variable length encoding = 몇바이트를 가져와야 하는지는 가져와봐야 알 수 있다.
- Complex instruction set = 하드웨어가 더 작은 형태로 instruction을 분해하는 형태로(micro operation) 분해를 해버리는 게 모든게 균일해지고 훨씬 더 실행하는데 효율적이다. CISC -> RISC 이렇게 쪼개는 것이다. 굳이 프로그램은 CISC를 유지하면서 왜 내부의 수행은 마이크로 단위로 짤라가지고 RISC로 실행을 하는가? 왜냐하면 이전 만들어 놓은 코드들과 호환을 해야하는 부분이 있기 때문에 어쩔 수 없이 그렇게 만들어 놓은 것이다. 옛날 CISC 타입을 그대로 끌고 갈 수 밖에 없는 것이다.
- 애초에 컴파일러가 복잡한 명령을 쓰는 것이 효율 개선에 전혀 쓸모가 없다. 호환성 때문에 쓰고 있는 것 뿐이다. 간단한 instruction으로만 쓰는 것이 효율에 더 좋다.

<br>

## [](#header-2)<span style="color:#088A08"> *Fallacies(많은 사람들이 옳다고 믿는 틀린 생각)* </span>
- powerful instructions -> higher performance (결론: 명령을 어떻게 하는게 중요한게 아니고.. 알고리즘 좋은게 짱이다.)
  - fewer instructions required
  - but complex instructions are hard to implement
  - compilers are good at making fast code from simple instructions
- use assembly code for high performance (결론: 똑같은 어셈블리 코드도 배치를 어떻게 하느냐에 따라서 속도가 달라진다. 따라서 성능을 위해서 어셈블리를 짜는 경우는 별로 없다. 대신 어셈블리를 쓸 수밖에 없기 때문에 쓰는 경우가 남아있을 뿐이다. 어셈블리로 짜면 훨씬 더 많은 줄을 만들어내야 하기 때문에 그냥 그렇게 짜는 것 뿐이다.)
  - but modern compilers are better at dealing with modern processors
  - more lines of code - more error and less productivity
- Backward compatibility = instruction set doesn't change
  - but they do accrete more instructions

<br>

## [](#header-2)<span style="color:#088A08"> *Summary* </span>
- Design principles
  - simplicity favors regularity: 일반적인 것을 간단하게 만들면 되고, 그걸 빠르게 수행하면 된다. 우리가 수행하는 대다수의 프로그램은 일반적인 instruction으로 채울 수 있고, 그 일반적인 instruction을 빠르게 수행한다면 전체 프로그램의 속도가 향상될 수 있다. -> 암달의 법칙
  - smaller is faster: 작게 만들수록 빠르다.
  - make the common case fast: 보편적인 것을 빠르게 만들어라
  - good design demands good compromises: 좋은 디자인은 뭐든지 다 잘하려고 하지말고 왜냐하면 뭐든지 다 trade-off가 있기 때문에, 다 적절히 하면 됨
- Layers of software / hardware
- ARM: typical of RISC ISAs



<br><br><br>



# [](#header-1)<span style="color:#088A08"> *Arithmetic for Computers* </span>
- Operations on integers
  - Addition and subtraction
  - Multiplication and division
  - Dealing with overflow
- Floating point real numbers
  - Representation and operations
- Floating point와 integer의 속성이 어떻게 다를지를 고민해 보는 part이다. 한마디로 정수 계산은 딱 떨어진다. integer = 몇 비트 폭으로 채울지에 대해 영향을 받는다. Floating point 같은 것은 어떻게 다룰 것인가? PI 같은 수라든지, 혹은 루트 3 이런 수들은 어떻게 다룰 것인가? 소수 몇째자리까지 뽑아야 하는가?
- 컴퓨터는 제한된 곳이다. 소수 아래 몇번째 까지? floating point real number에 대해서는 자리수를 고정하자는 합의를 한다. IEEE = I triple E 라는 곳에서 표준을 지정한다.

<br>

## [](#header-2)<span style="color:#088A08"> *Integer Addition and Subtraction* </span>
- 그냥 하면 된다. 뺄셈은 2의 보수로 만들어서 더하면 된다.

<br>

## [](#header-2)<span style="color:#088A08"> *Integer Multiplication* </span>
- 2진수의 곱셈: 1010 * 101 = 그대로 쓰든지 쉬프트를 해주든지 해서 나온 값을 더해주기만 하면 된다. 이진수의 곱셈은 덧셈을 반복하는 것인데, 한자리씩 올리면서 더해주면 된다.
- 보다 곱셈을 빨리하고 싶으면 펼쳐서 더하면 됨. 반복하는게 아니라 쭉 펼쳐서 병렬적으로 더해주면 됨.

<br>

## [](#header-2)<span style="color:#088A08"> *Floating point standard* </span>
그렇다면 부동 소수점의 경우는? 소수점 이상이 있는 무지하게 작거나 큰 수는? IEEE 표준에 맞게 처리하면 됨. floating point의 정확도에 대해서는 누가 신경을 쓰는가? scientific code (우주에 뭘 쏘아올린다든지, 과학 계산을 하는 경우에 중요하다.)

<br>

## [](#header-2)<span style="color:#088A08"> *Summary* </span>
- ISA supports arithmetic
  - signed and unsigned integers
  - floating point approximation to reals
- Bounded range and precision
  - operations can overflow and underflow
- ARM core instructions dominate integer SPEC2006 execution and integer and arithmetic core dominate SPEC2006 floating point execution
- floating point 쓰는 일이 사실 많이 없고, 가벼운 processor는 floating point unit이 없는 processor도 굉장히 많다. 왜냐하면 필요가 없기 때문에, 대부분의 경우에
- instruction subset = integer 가 대부분이다. 명령의 종류에서는 percentage를 어느정도 차지는 하는데, integer는 ARM core로 구현을 했을 때, Intel floating point unit... 굳이 필요없는데 쓸 필요 없다.


<br/><br/><br/>


## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[가톨릭대학교 컴퓨터 구조 by 서효중][ref1]*   

[ref1]:http://www.kocw.or.kr/home/cview.do?mty=p&kemId=695204
