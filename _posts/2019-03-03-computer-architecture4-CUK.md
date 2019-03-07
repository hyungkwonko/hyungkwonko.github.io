---
layout: post
title: 서효중 교수 컴퓨터 구조 수업 노트 3-2
date: 2019-03-03 17:00:00
categories:
- Computer architecture
tags:
- none
author: "Hyung-Kwon Ko"
meta: "Seoul"
use_math: true
---

## [](#header-2)<span style="color:#088A08"> *Instruction Set* </span>
- 고급언어는 컴파일 등을 통해서 기계가 이해하는 언어로 바뀌었고, 기계 언어를 수행하는 것이 컴퓨터이다.
기계 언어와 그 환경에 해당하는 것은 ISA라고 함. ISA의 하나의 예제를 보는게 오늘 하는 것이다.
- Command set of a computer
- Different processors have slightly different instruction sets = 프로세서가 다르면 instruction이 다르다. (명령어가 slightly 다르다.)
- RISC (reduced instruction set): 간단한 명령을 다 따로 따로 잘라서 가지고 있는 것
- CISC (complex instruction set): 복잡한 명령을 지원하는 것

<br/>

### [](#header-3) RISC 와 CISC 비교
- RISC
  - instruction수는 늘어난다.
  - 명령 수행시 필요한 클럭은 짧아지고
  - CPI도 짧아지고
  - instruction의 수는 늘어나고.
  - ARM (핸드폰)에서 사용
- CISC
  - 한 프로그램을 수행하는 instruction의 수가 줄어들고 복잡해짐
  - instruction이 복잡해지면, 한 명령을 수행하는데 필요로 하는 클럭의 수가 늘어난다
  - 따라서 클럭 자체의 길이를 길게 끌고갈 수밖에 없다.
  - x86에서 사용 (PC)
- 결론은 조삼모사이다. 뭐가 더 좋다고 말하기 애매함. 따라서 현실은 둘이 혼재되어 있다.

<br/>

## [](#header-2)<span style="color:#088A08"> *The ARM Instruction Set* </span>
- Most popular 32-bit instruction set
- Large share of embedded core market
- Typical of many modern RISC ISAs

<br/>

### [](#header-3) ARM 과 MIPS 비교
- ARM = modern
- MIPS = classical (심플하고 공부하기 좋다. 성능이 떨어지지도 않음.)

<br/>

## [](#header-2)<span style="color:#088A08"> *Arithmetic operations* </span>
- Add and Subtract
```
ADD a, b, c  ;   a = b + c
```
- Design Principle 1:
  - 일반적인 것을 단순하게 하는 것이 좋다.
  - 단순하면 더 쉽게 구현 가능.
  - 그리고 단순하면 더 싸다.

- intel은 CISC타입으로 지금까지 만들어 놓은게 많아서 버리지를 못함.
- design관점과, computer architecture관점에서 intel processor는 완전 안좋음.

<br/>

### [](#header-3) Arithmetic example
- C code:
```C
f = (g + h) - (i - j);
```
- Compiled ARM code:
```
ADD t0, g, h  ;   temp t0 = g + h
ADD t1, i, j  ;   temp t1 = i + j
SUB f, t0, t1 ;   f = t0 - t1
```

<br/>

## [](#header-2)<span style="color:#088A08"> *Register Operands* </span>
- Arithmetic instructions use register operands
- ARM has a 16 * 32 bit register file = 16개 = ARM은 32bit 저장 공간을 16개 가지고 있다. 덩어리 = 레지스터 파일
  - used for frequently accessed data
  - registers numbered 0 to 15
  - 32 bit data called a "word" = 32비트 = 4바이트 = 1워드
- Design principle 2: Smaller is faster
- Flip Flop의 값을 불러와서 계산하고 다시 갖다 놓는 것 = 계산
- 계산 결과를 저장하는 곳 = 레지스터
- ijk 배열 잡은 것은 어디에 위치하게 되는가? 변수들은 어디에 저장되나? 주소는 어디 있는가? 주소는 RAM에 있음. 메모리 상에 저장이 되어있다. 계산을 하려면 register에 있어야 하고 register는 16개 뿐이다. 메모리에 있는 것을 계산할 때 그 값을 레지스터로 복사해오는 것이다. 복사해 온 것을 가지고 계산하고 나중에 그 결과를 다시 메모리에 카피해 놓는 것이다. 기억 장치로 봤을 때는 최상위로 보는 것이 register이다.
- 레지스터는 왜 16개밖에 안 가지고 있나? 레지스터를 너무 많이 가지고 있으면 레지스터 자체에 access하는게 느려진다. 너무 많이 가지고 있는 것도 안좋음.

<br/>

### [](#header-3) Register operand example
- C code:
```C
f = (g + h) - (i - j);
```
- Compiled ARM code:
```
ADD r5, r0, r1  ;   register r5 contains g + h
ADD r6, r2, r3  ;   register r6 contains i + j
SUB r4, r5, r6  ;   r4 gets r5 - r6
```

<br/>

## [](#header-2)<span style="color:#088A08"> *Memory Operands* </span>
- Main memory used for composite data: 레지스터와 메모리 사이에 데이터를 주고 받는 명령 = Memory operand
  - arrays, structures, dynamic data
- To apply arithmetic operations
  - Load values from memory into registers = load: 메모리 -> 레지스터로 데이터를 가져오는 것
  - store result from register to memory = store: 레지스터 -> 메모리
- Memory is byte addressed
  - each address identities an 8 bit byte
- Words are aligned in memory
  - address must be a multiple of 4 = 메모리에 데이터가 담겨있을 때, 반드시 한 워드를 맞아 떨어지게 align 해서 4의 n승 단위로 동작을 한다. = 단순하게 만들어서 수행 성능을 더 좋게 한 것이다.

<br/>

### [](#header-3) Memory operand example
- C code:
```C
g = h + A[8];
```
- Compiled ARM code:
```
LDR r5, [r3, #32]  ;   reg r5 gets A[8]
ADD r1, r2, r5     ;   g = h + A[8]
```
- 위에 LDR r5, [r3, #32]에서 r3는 base register(여기서는 A라는 위치), #32는 offset이다. (여기서는 4*8, 주소가 1개당 4씩 증가하기 때문에)

<br/>

### [](#header-3) Memory operand example2
- C code:
```C
A[12] = h + A[8];
```
- Compiled ARM code:
```
LDR r5, [r3, #32]  ;   reg r5 gets A[8]
ADD r5, r2, r5     ;   reg r5 gets h + A[8]
STR r5, [r3, #48]  ;   stores h + A[8] into A[12]
```
- 한 번 이해하면 쉬움.

<br/>

## [](#header-2)<span style="color:#088A08"> *Register VS Memory* </span>
- Registers are faster than memory = 레지스터는 메모리보다 항상 빠르고, 항상 레지스터에 있어야 계산을 할 수가 있다. 컴파일러는 레지스터를 효율적으로 써야하고 그래야 load / store의 횟수가 줄어든다.
- Operating on memory requires loads / stores = 반드시 레지스터 안에 있어야 하니까 메모리에서 Load/Store가 필요하다는 의미임.
- Compiler must use register as much as possible = 컴파일러가 최대한 효율적으로 컴파일을 해서 써야한다. 연산 수행시 컴파일러가 제 역할을 얼마나 잘하느냐에 따라서 효율이 엄청 다르게 나온다.
  - only spill to memory for less frequently used variables = 자주 쓰는 것들은 메모리로 보내지 말고 레지스터에 갖고 있어라.
  - register optimization is important = 레지스터가 16개 밖에 없으니까 어떻게 잘 할당할지 최적화를 잘 해야한다.

<br/>

## [](#header-2)<span style="color:#088A08"> *Immediate operands* </span>
- constant data specified in an instruction = 우리가 많이 쓰는 숫자는 작다 = 0과 1이니까. 1 같이 많이 쓰이는 것들까지 굳이 load / store해야하나? instruction안에 1이라는 값을 가지고 있으면 메모리 로드 안해도 되니까 좋다. = Immediate 하게 작업을 하니 빠르다.
```
ADD r3, r3, #4  ;   r3 = r3 + 4
# 그냥 한줄로 끝나버리는 걸 볼 수 있다.
```
- design Principle 3: Make the common case fast
  - small constants are common
  - immediate operand avoids a load instruction = 로드를 안해도 됨.

<br/>

## [](#header-2)<span style="color:#088A08"> *Representing instructions* </span>
- instructions are encoded in binary
- ARM instructions
  - encoded as 32-bit instruction words = 한 명령은 32bit안에 딱 들어가도록 만들어져 있다.
- Register numbers - r0 to r15 = 16개
- 명령은 몇개 필요한가? + - / * AND OR NOT 등등... 50개 정도? 그럼 명령을 위한 bit는 몇 bit이면 충분한가? 2^6 = 64개니까 6비트면 충분하다.
- 서로 다른 n개의 것을 1대1로 대응시키는 작업을 하는 것이다.

<br/>

## [](#header-2)<span style="color:#088A08"> *ARM Data Processing instructions* </span>
- instruction fields
  - Opcode[4bit]: Basic operation of the instruction = 내가 무슨 명령을 수행할 것이냐 = 4bit = 2^4 = 16개의 명령
  - Rd[4bit]: The destination register operand = 16개 레지스터니까
  - Rn[4bit]: The first register source operand = 16개 레지스터니까
  - Operand2[12bit]: The second source operand = 남는 것은 버리면 그만이다. immediate도 사용될 수 있음.
  - I(Immediate)[1bit]: If I is 0, the second source operand is a register, else the second source is a 12 bit immediate = 만약 0이라면 op2에서 4bit만 보면 된다. 아니면 12bit에 해당하는 수로 보겠다.
  - S[1bit]: Set condition code = for문 같은 경우.. counter라고 해서 레지스터에 100을 넣고 0이 될때까지 1씩 배낸다. 연산의 결과가 0이면 어떤 연산의 형태이든 CPU의 flag에서 0라는 필드가 있는데, 자기 전의 결과가 1이면 0라는 flag를 0으로 만든다. 플래그에 따라 수행할지 말지 세팅
  - Cond[4bit]: condition
  - F[2bit]: instruction format = Load/Store같은 애들은 레지스터가 3개가 애초에 필요없다. 뒤에 있는 포맷이 r-type인지 Load/Store인지 구분해주는 영역이다.


<br/>

### [](#header-3) DP instruction example
```
ADD r5, r1, r2  ;   r5 = r1 + r2
= 11100000100000010101000000000010(2) 이게 하나의 instruction이 32bit로 저장됨
```

<br/>

## [](#header-2)<span style="color:#088A08"> *ARM과 X86의 차이* </span>
- ARM(RISC) = 1개의 instruction은 무조건 32 bit이다.
- X86(CISC) = CISC = instruction이 지저분하고 복잡 = 무조건 4 byte 아니다. (ARM과 다름) 명령을 해석하는 제어기가 복잡하고... 여러모로 복잡하다... 길이도 가변적이고... 한 instruction이 8-32bit로 들쭉 날쭉


<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[가톨릭대학교 컴퓨터 구조 by 서효중][ref1]*   

[ref1]:http://www.kocw.or.kr/home/cview.do?mty=p&kemId=695204
