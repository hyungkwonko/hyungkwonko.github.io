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














## [](#header-2)<span style="color:#088A08"> *8번째 강의* </span>

### [](#header-3) 8번째 강의

챕터 4 시작 -

MIPS instruction set architecture: ARM보다 심플하기도 하고 어느 측면에서는 더 뛰어난 성능을 가지고 있다.

MIPS의 원형인 MIPS ISA를 배울 것이다.

MIPS register model: ARM보다 2배 많다. (32bit)

첫번째 register = 무조건 0이다. 0은 제일 많이 쓰는 숫자이기 때문에. 매번 immediate instruction을 쓰기가 귀찮기 때문에, register 중 하나의 값을 0으로 fix해 놓은 것이다. 더 심플하고 general히게 만든 것이다. 워낙 자주 쓰는 숫자이기 때문에 그렇다.

Floating point 연산에 대해서 배웠는데, 내부는 우리가 따로 배우지 않는다. floating point 연산을 하기 위한 register가 또 다르게 32bit로 할당되어 있다. 일반적인 용도로는 안쓰고 부동 소수점 용도로만 쓰기 때문에 고려하지 않아도 된다. 확장을 해서 64 비트로 쓸 수도 있다.

Program counter: 지금 프로그램을 수행하는 코드의 위치를 가리키는 레지스터 (ARM에도 똑같이 있었던 값) 이 값을 바뀜에 따 Branch, Call, function Call 등을 구현한다.

High & Low register:

MIPS Register Namings (32개)
0번: constant 0
1번: reserved for assembler
2,3번: return value (function이나 subroutine을 call하면 parameter를 넘겨주고 return value를 돌려받게 되는데 그 return value를 저장하는 곳)
5,6,7번: argument, (function이나 subroutine을 돌릴 때 넘겨주는 parameter들을 위한 argument용의 register)
8-15번: temporary, 임시 변수 i, j, k 굳이 메모리에 안써도 되고,,,
16-23번: permanent, 변하지 않는 값,,,
24-25번: temporary, 위와 마찬가지
26-27번: OS kernel, 운영체제가 쓴다.
28번: global pointer
29번: stack pointer
30번: activation frame pointer
31번: return address

ARM에 비해 훨씬 넉넉하기 때문에 이것 저것 담을 수가 있다.

ARM에서는 register를 가리키는 형태로 32 bit instruction 중 4 bit를 썼었다. 레지스터가 16개 밖에 없으니까, 0000부터 1111까지 4비트면 커버가 되기 때문에 그렇다.
MIPS는 32개 있으니까 5bit면 된다.

MIPS memory model: 주소의 크기는 0부터 byte단위로 주소를 붙여서 ffff ffff (8*4 = 32 bit)

MIPS는 4GB만큼의 address space 를 가지고 있다.  (???)

MIPS와 ARM이 다르지만, instruction이 가져야할 form은 다르다.

Shift, AND, OR, NOT 등 메모리에서 가져왔다가 다시 갔다놨다가, if then else, 점프

MIPS instructions
- arithmetic / logic instructions
- data transfer (LOAD, STORE) instructions
- conditional branch instructions
- unconditional jump instructions

MIPS instructions
- arithmetic / logical operations
  - three operand format: result and two sources: 3개의 operand 사용될 수 있는 것, ARM과 똑같다.
  - operands registers, 16 bit immediate
  - compare operations: ARM에도 있는, 빼는데 update만 하지 않는 instruction
- integer multiply / divide: 곱셈, 나눗셈
  - iterative in hardware: 루프를 도는 방법
  - uses HI and LOW registers that must be explicitly accessed: 32bit * 32bit = 64bit이기 때문에, 위는 high, 아래는 low, 32비트에 해당하는 2개의 레지스터를 가지고 있는 것이다. 나누는 것도 마찬가지임, 몫과 나머지가 생기기 때문에, 2가지르 다 저장을 한다.
- floating point operations (무시... 다 있는데, 이건 일반적으로 신경 안써도 됨)
  - operate only on floating point registers
  - single and double precision (double uses paired registers)
  - typical operations are add, subtract, multiply, divide

Subtract, Add 둘다 signed / unsigned가 있다.
2개 중에 대소를 비교하는 것은?

MOV from high, MOV from LOW 등등 많다.

Logical instruction
Data Transfer instruction (LOAD / STORE)

ARM의 경우 13개의 addressing이 있었는데, 많긴한데 이걸 모두 알아야 할 필요는 없다. 대신 코드를 보고 이해할 수만 있으면 된다.

근본적으로 메모리를 직접적으로 access하는 것은 LOAD, STORE 밖에 없다.

MIPS의 addressing

32비트의 구조라는 것은? CPU안에 있는 register의 구조가 32bit이다. 그리고 안에 ALU가 있는데, 32bit, 32bit 두개를 덧셈을 해가지고 ADD를 하면 32bit 결과를 만들어내고 그런다. 가만히 보면 register는 메모리하고 값을 주고받고 한다. 그럼 메모리 한 단위의 폭은? 메모리가 1byte라면 32bit를 읽어들이려면 4번을 해야한다. 따라서 32bit 컴퓨터가 제대로 되려면 모든 단위가 다 32bit가 되어야 한다. 메모리도 32bit 폭이고..

word 단위의 access는 4의 단위로만 access가 가능하다는 제한을 넣으면 모델이 간단해진다.
- all memory access exclusively through loads and stores
- aligned words, halfwords, and bytes
- floating point load / store for FP registers
- single addressing mode
- degenerate cases

MIPS conditional branch instructions: 2개의 register 값을 비교해보고,
MIPS unconditional jump instructions: 어떤 특정한 번지로 바로 뛰는 것이다.

31번째 register = return했다가 다시 돌아올 위치를 저장해둔 곳이다. JUMP and LINK = 현재 돌아올 곳을 저장해놓고 뛰겠다.

----

실제 register format

1. ADD operation
```
add    rd, rs, rt;    // rd = rs + rt
```
  - fetch instruction from memory
  - ADD operation
  - calculate next address (Program counter값을 4만큼 증가시킨다, PC = PC + 4)
- 제일 앞에 6 bit = operation, add라는 명령을 가리키는 것
- register가 총 32개니까 그 중 하나를 가리킨다 하면 각각 5비트가 필요하다.(rs, rt, rd)

사실 ADD나 SUBTRACT나 다를게 별로 없는데, (subtract는 !이거로 반대 만들고 나서 +1하면 됨, 한마디로 a+b가 ADD면 a + !b + 1 이렇게 하면 SUBTRACT임)

2. SUB operation
```
sub    rd, rs, rt;    // rd = rs - rt
```
  - fetch instruction from memory
  - SUB operation (a + b 를 a + !b + 1 이렇게 만들어서 2의 보수법을 취한 것이다.)
  - calculate next address (Program counter값을 4만큼 증가시킨다, PC = PC + 4)

따라서 ALU입장에서 하는 일은 똑같고 따라서 operation의 number가 000000(6bit)으로 똑같다. 둘이 다른 점이 있다면 SUBTRACT는 2의 보수 취해서 더한다는 점. 살짝 빗겨나가는 점만 반영을 해서 뒤에 function에 해당하는 부분만, (6bit, ADD: 100000, SUB: 100010) 이렇게 다르다.

3. lw Operation
```
lw    rt, rs, imm16;
```
  - fetch instruction from memory (메모리에서 instruction을 fetch한다.)
  - compute memory address (내가 access할 메모리의 주소를 계산한다.)
  - load data into register (그 주소 값을 통해서 메모리에 접근한 후 데이터를 LOAD해서 덮어쓴다.)
  - calculate next address (program counter값을 4만큼 증가시킨다.)


operation은 6비트. 따라서 기본 명령은 64가지가 생길 수 있다. 그 다음에 source하고 target 2개 밖에 없으니까 5bit씩 가리키고, 5 + 5 + 6 = 16, 따라서 16비트가 남아서 16bit의 immediate.

MAKE THE COMMON CASE FAST! = 16 bit immediate 만 가지고도 충분히 쓸 수 있다고 생각한다.


4. sw Operation (위에 lw랑 똑같다.)
```
sw    rt, rs, imm16;
```
  - fetch instruction from memory (메모리에서 instruction을 fetch한다.)
  - compute memory address (내가 access할 메모리의 주소를 계산한다.)
  - store data into register (그 주소 값을 통해서 메모리에 접근한 후 데이터를 STORE한다.)
  - calculate next address (program counter값을 4만큼 증가시킨다.)

operation code도 1비트만 다르고 거의 똑같다고 보면 됨. 저장하는지, 불러오는지 그 차이만 있는 것이다.

5. beq Operation (Branch if EQual)
```
beq    rt, rs, imm16;
```
- fetch instruction from memory (메모리에서 instruction을 fetch한다.)
- compute conditional cond (2의 보수법을 이용해 뺄셈을 해서 같은지 다른지 계산을 한다.)
- Fall through if non-zero cond
- Branch if zero cond ( PC = cond ? PC + 4 : PC + 4 + (SignExt(imm16) << 2)) shift2를 하는 이유는?  100을 의미할 때는 25를 의미한다. 왜냐하면 명령어 한 줄의 크기가 4byte이기 때문에 그렇다. << 2 이걸 한다는 말은 4배를 한다는 말이다. 왜냐면 몇 개의 instruction을 뛰어넘냐는 말이 되는데, 4 byte가 1개의 instruction이기 때문에 4를 곱해줘서 몇 byte를 뛰어넘는지를 계산한 것이다.

일단 format은 위랑 똑같다. load word나 store word나 생긴 모양이 비슷하다. 다만 rt와 rs를 비교를 해서 그게 같으냐 다르냐에 따라서 같으면 immediate 16만큼 뛰겠다고 했음. detail하게 보면 instruction register

ARM에 비해서 훨씬 쉽죠?

MIPS Addressing modes









<br/><br/><br/>


## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[가톨릭대학교 컴퓨터 구조 by 서효중][ref1]*   

[ref1]:http://www.kocw.or.kr/home/cview.do?mty=p&kemId=695204
