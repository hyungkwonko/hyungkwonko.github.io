---
layout: post
title: 서효중 교수 컴퓨터 구조 수업 노트 6-1
date: 2019-03-05 17:00:00
categories:
- Computer architecture
tags:
- none
author: "Hyung-Kwon Ko"
meta: "Seoul"
use_math: true
---

## [](#header-2)<span style="color:#088A08"> Miscellaneous </span>
- MIPS가 아직 죽은게 아니다.
- 산술 논리 연산에 해당하는 operation. 32개 중 하나를 지정하는 형태가 되는 것이고, 명령어들은 Memory안에 있어야 하기 때문에, 메모리 번지의 한 word -  4 byte를 끄집어내서 instruction register라는 곳에 가져온다고 했음. memory instruction은 계산을 끄집어내서 이거 저거 하는 동안, 명령이 뭔지를 기억해야하기 때문에, 그래야 그에 맞는 형태로 제어를 할 수 있기 때문에, 기억을 해두기 위한 명령어 register라고 한다. 4 byte를 IR에 가져왔기 때문에, 내용을 보고 이게 뭘하는 명령인지를 알 수 있다. 해독을 해 봤더니 ADD면, source와 target의 내용을 끄집어내서 register of R target 번호를 끄집어 내서 계산을 해낸다. 알맹이를 보니까 6 bit는 명령어 자체를 가리킨다. 따라서 명령어의 개수는 총 2^6 = 64개 이겠구나를 알 수 있다. rs, rt, rd 이거는 32개의 register 중에 한개를 가리키기 때문에, 5bit = 2^5 라는 것을 알 수 있다. ADD 하고 SUB operation은 function code만 다르고 operation 자체는 다를게 없다. rd, rs, rt 그리고 소스 자체는 2의 보수법을 취한 것 빼고는 크게 다를 것이 없고... LOAD, STORE instruction. Load word, Store word, beq, bne, 소스와 타겟의 값을 비교 등... 저번 시간에 했던 것과 똑같다. 몇 개의 명령을 뛸 것이냐? 한 개의 명령은 4 byte이니까, 4를 곱해줘서 뛰어넘으면 된다.
- ARM 에는 address mode가 굉장히 많았는데... MIPS는 적다.
- if then else, 조건부 JMP, 무조건적 JMP. 왜 이런 값들은 다 Label로 얘기할 수 밖에 없는가? 메모리 주소 번지가 상대적인 값이기 때문에, 컴파일 되는 시점에 결정된다. 그래서 기본적으로는 Label로 다 얘기를 한다.

<br>

## [](#header-2)<span style="color:#088A08"> ARM & MIPS similarities </span>
- ARM: the most popular embedded core
- Similar basic set of instructions to MIPS

||  ARM | MIPS     |
| :------------- | :-------------: | :-------------: |
|Data announced|1985|1985|
|instruction size|32 bits|32 bits|
|Address space|32 bit flat|32 bit flat|
| Data alignment | Aligned | Aligned |
| Data addressing modes | 9 | 3 |
| Registers | 15 * 32 bit | 31 * 32 bit |
| Input / Output | Memory mapped | Memory mapped |


<br><br><br>


# [](#header-1)<span style="color:#088A08"> Chapter 4. The processor (implementation) </span>
- CPU performance factors (CPU 성능은 뭐에 달렸다? 여러가지에 달렸다. 원인 다양함)
  - instruction count
    - determined by ISA and compiler
  - CPI and cycle time
    - determined by CPU hardware
- We will examine two MIPS (not ARM) implementation (그 중에서 MIPS instruction을 개선하는 것을 볼 것이다. 결론은 CPU성능 개선을 위한 것이다.)
  - A simple version
  - A more realistic pipelined version (for performance)

<br>

## [](#header-2)<span style="color:#088A08"> Instruction Execution </span>
- PC = instruction memory, fetch instruction (수행 하려면 반드시 DRAM에 올라와야 한다. 우리는 항상 Program counter라는 것을 가지고 그게 명령을 수행할 위치를 가리키고 있어야 한다.)
- Register numbers = register file, read registers (레지스터의 번호를 정해서 내가 access를 할 수 있어야 한다.)
- Depending on instruction class
  - use ALU to calculate (내 명령의 종류에 따라서 ALU를 이용해서 계산을 해야한다.)
    - arithmetic result
    - memory address for load / store (메모리 주소를 계산)
    - branch target address (몇개를 뛰어야 하는지 계산해야 한다.)
  - access data memory for load / store (프로그램용 메모리도 있지만 데이터용 메모리도 있다. 그 메모리를 한개로 합쳐놨을 뿐이다.)
  - PC = target address or PC + 4

<br>

## [](#header-2)<span style="color:#088A08"> Logic design basics (논리 회로에서 봤던 것들을 구현하는 단계) </span>
- information encoded in binary
  - low voltage = 0, high voltage = 1 (하나를 얘기하는 거는 전선 한 가닥을 쓰면 되겠다.)
  - one wire per bit
  - multi bit data encoded on multi wire buses
- combinational element (logic) (ALU를 만들 때는 조합회로를 이용하면 되겠다.)
  - operate on data
  - output is a function of input
- state (sequential) elements (flip flop) (레지스터라든지 저장할 때는 Flip-Flop을 이용하면 되겠다.)
  - store information

- 이런 순서 절차를 따라서 제어를 하는 것 = 논리 회로 시간에 배운 state machine
  - 전선 그 자체 =  bit
  - Flip-Flop 덩어리 = register
  - 조합회로의 덩어리 = ALU
  - 제어를 하기 위한 state machine = 조합회로와 flip flop을 이용하면 된다.
- 이걸 이용해서 실제로 구현을 해 나갈 것이다.

<br>

## [](#header-2)<span style="color:#088A08"> Combinational elements </span>
- AND-gate: Y = A & B
- Adder: Y = A + B
- Multiplexer: Y = S ? l1: l0 (2개의 구성 요소 중에 맘대로 하나를 선택할 수 있는 구조, 물론 2개 이상에 대해서도 가능하다.)
  - 4개 선택을 하는 MUX를 만든다 하면 (~s0*~s1*A + ~s0*s1*B + s0*~s1*C + s0*s1*D)
- Arithmetic / Logic unit: Y = F(A, B)
 - 결국 ALU라는 것 = 어마어마한 조합회로의 덩어리이다. 32bit랑 32bit를 받아서 32bit를 output으로 만들어내면 됨. = 다양한 조합을 결과로 내고 MUX로 선택을 할 수만 있으면 되겠다. 이게 바로 ALU이다.


<br/><br/><br/>


## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[가톨릭대학교 컴퓨터 구조 by 서효중][ref1]*   

- *[Introduction to Multiplexers | MUX Basic][ref2]*   

[ref1]:http://www.kocw.or.kr/home/cview.do?mty=p&kemId=695204

[ref2]:https://www.youtube.com/watch?v=FKvnmxte98A
