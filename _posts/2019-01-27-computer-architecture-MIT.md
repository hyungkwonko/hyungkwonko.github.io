---
layout: post
title: CA Lecture Note 10 - Beta ISA, models of computation
date: 2019-01-27 17:00:00
categories:
- Computer architecture
tags:
- ISA
author: "Hyung-Kwon Ko"
meta: "Seoul"
use_math: true
---

### [](#header-3) CPU = ALU + CU + Register

ALU (arithmetic / logic unit): 산술 연산과 논리 연산을 수행하는 디지털 회로. performs all thr mathematical inside the CPU such as addition, subtraction or even comparison
CU (control unit): 프로그램에 따라 명령과 제어 신호를 생성, 정보와 데이터의 흐름을 결정하고 각종장치의 동작을 제어
Register: CPU에서 사용하는 데이터를 일시적으로 저장

PC (program counter): CPU내부에 있는 레지스터 중 하나로, 다음에 실행될 명령어의 주소를 가지고 있다. 명령어 포인터(instruction pointer)라고도 한다.



### [](#header-3) Beta: Operate-class instructions

thinking of data? instruction? address? which is which? - right mindset is required



#### [](#header-4) Can we solve factorial with ALU instructions?

- No! Recall high level FSM
- factorial needs to loop



### [](#header-3) Branch instructions

- provides a way to conditionally change the PC to point to a nearby location, and optionally remember where we came from
- test a particular register if it is 0 or not
- if it is true(Reg[ra] == 1), **the 16-bit signed constant** will specify a different instruction(branch target) not the instruction following the branch, but is some other instruction in your program.
- the 16-bit signed constant is not the absolute address the next instruction, but an relative to the current location

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic76.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Beta Branch Instructions, <a href="https://www.youtube.com/watch?v=zTfaOHCZky8&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=10">source</a> </div>

- "offset"은 애초 용어 자체가 말하듯이 상대적인 거리라고 생각하면 된다. 절대 주소가 아닌 상대적 위치 차이의 개념. 음수면 먼저 나온 instruction, 나중에 나오면 뒤에 나오는 intruction

- it also remembers the address we branched from, why do we care about this? since we can get back there (branching되었으면 갔다가 어쨌든 돌아와야 하는데 그 돌아오는 작업을 위해서 기억한다고 보면 될 듯)

- BEQ = branch equal(조건이 같을 때 branch한다) / BNE = branch not equal(조건이 다를 때 branch한다)


#### [](#header-4) Can we solve factorial now?

```C
int a = 1;                                // Assume r1 = N
int b = N;            ADDC(r31, 1, r0)    // r0 = 1
while(b != 0) {     L:MUL(r0, r1, r0)     // r0 = r0 * r1
  a = a * b;          SUBC(r1, 1, r1)     // r1 = r1 - 1
  b = b - 1;          BNE(r1, L, r31)     // if r1 != 0, MUL next
}                                         // at this point, r0 = N!
```

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic77.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, FSM states, <a href="https://www.youtube.com/watch?v=zTfaOHCZky8&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=10">source</a> </div>

- 보면 BNE(r1, L, r31)이라고 되어 있는 부분에서 뒤로 올라가는 것인데 L이라고 쓴 이유는 -4, -8 이런식으로 숫자로 썼다가 중간에 코드가 삽입되면 엉망진창이 되는 것을 방지하기 위해서임.


### [](#header-3) JMP instruction

- difference between JMP and Branch?
  - branch target where you go to, target the address of the next instruction actually encoded in the binary of the program
  - jump does **jump**, takes the contents of registers and sticks it into the PC(program counter)
- branch and jump work together

Jmp와 Branch는 같이 사용될 때 유용한데 다음과 같다.

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic78.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Jmp & Branch being used together, <a href="https://www.youtube.com/watch?v=zTfaOHCZky8&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=10">source</a> </div>


우선 왼쪽에 BEQ(R31, ..) 에서 R31이 0인지 생각해보면 R31은 무조건 0이다. 따라서 무조건 sqrt를 실행한다는 명령
BEQ(,,R28)에서 R28은 처음에는 보면 0x104인데 처음 실행된 BEQ를 보면 0x100이다. 그 다음 instruction은 4바이트를 더하면 0x104일텐데 그걸 저장해서 sqrt함수가 다 끝나면 이제 그 R28에 담겨있는 0x104로 돌아와서 sequential하게 코드를 실행시킬 수 있는 것이다. 그렇게 코드가 또 쭉쭉 돌다가 다음 0x678에서 코드가 실행할라 치면 그 다음 instruction인 0x67c를 저장해서 다음에 jmp에서 return할 때 거기로 return하는 것이다. R28이 중요하다기 보다는 이거는 address를 담는 그릇이라고 생각하면 되고 그 안에 content가 중요함.



### [](#header-3) Programming languages

- 32-bit ADD instruction:

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic79.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, ADD instruction, <a href="https://www.youtube.com/watch?v=zTfaOHCZky8&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=10">source</a> </div>

-----

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic81.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Example UASM source file, <a href="https://www.youtube.com/watch?v=zTfaOHCZky8&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=10">source</a> </div>

-------

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic80.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Assembly language, <a href="https://www.youtube.com/watch?v=zTfaOHCZky8&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=10">source</a> </div>

- 이 세개의 슬라이드에 해당(특히 마지막 슬라이드)하는 부분은 그냥 읽거나 동영상 부분을 직접 볼 것. 쉽게 잘 설명하는 것 같음 실제 코드 예제 등..



### [](#header-3) Registers are predefined symbols

- No type checking at all

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic80.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Problem that might happen, <a href="https://www.youtube.com/watch?v=zTfaOHCZky8&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=10">source</a> </div>

- ADDC의 경우 second operand가 constant여야 한다.
- ADD의 경우 symbol이어야 한다.
- assembler is not going to help you, you actually have to keep your eyes open



### [](#header-3) Computability

- Fact: each model studied is capable of computing exactly the same set of integer function
  - proof technique: constructions that translate between models
  - big idea: Computability, independent of computation scheme chosen
- Church's thesis: every discrete function computable by ANY realizable machine is computable by some Turing machine. 증명된 것은 아니지만 universally 이렇게 사용중이다. 튜링 머신으로 계산 가능하다 = computable



### [](#header-3) Uncomputability?

- this particular function cannot be implemented by any realizable machine (한마디로 uncomputable 한게 있다)
- 밑에 동영상 보면 쉽게 이해됨



### [](#header-3) universality

- Universal Turing machine can emulate every other Turing machine(Turing machine for all the other Turing machine)
  - k encodes a program - a description of some arbitrary machine
  - j encodes the input data to be used
  - T_u interprets the program, emulating its processing of the data
- 튜링 머신과 보편 튜링 머신에 대한 설명이 부실한 듯 하며(그냥 대충 대충 넘어감) 이 부분은 컴퓨터 과학이 여는 세계 강의를 보며 보충 할 것

- The universal Turing machine is the paradigm for modern general-purpose computers

- To show your computer is universal: demonstrate that it can emulate some known UTM.
  - Actually given finite memory, can only emulate UTMs + inputs up to a certain size
  - This is not a high bar: conditional branches and some simple arithmetic are enough

------


### [](#header-3) 컴퓨터 과학이 여는 세계 1-3강

- 자연수의 무한함 < 실수의 무한함
- 튜링기계를 만들면 항상 mapping 되는 한개의 자연수가 있다. 따라서 튜링기계 하나면 자연수 하나
- 따라서 튜링 기계의 수는 무한할 수 있지만 자연수의 무한함을 벗어나지 못한다. = 튜링 기계의 급소이다. = 괴델의 불완전선 정리 증명 = 따라서 튜링 기계로는 모든 참인 명제를 만들어 내지는 못한다.
- 따라서 사람이 짤 수 있는 소프트웨어(튜링머신)의 개수 역시 자연수의 개수를 넘지 못한다.


**튜링 머신 하나가 자연수 하나이다.**

- Universal Turing machine: 하나의 튜링기계이지만, 그 입력에 따라 모든 종류의 튜링 기계를 흉내낼 수 있는 특별한 기계

- 칸토르의 대각선 논법: 자연수의 개수보다 자연수의 power set의 개수가 더 많다.

$$ |N| < | 2^N |. $$

- 따라서 무한에도 차이가 있다.

- 유한 시간안에 끝나는지를 판단하는 기계를 만들 수 있다? - halting problem

#### [](#header-4) Halting problem example code

```C++
bool check(string s) {
  if(halt(s, s) == false)
    return true
  else
    loop forever
}
```
- if the program **_check(check)_** ends, that means **_halt(s, s) == false_**, which is a contradiction. Likewise, if it loops forever it means **_halt(s, s) == true_** which also results in a contradictory situation.  
- So we have proved Godel's incompleteness theorem using Turing machine.  


### [](#header-3)References

- *[Computer Architecture MIT 6.004 2015 by Chris Terman][ref1]*   
- *[컴퓨터의 구조][ref2]*   
- *[How a CPU works][ref3]*   
- *[Proof That Computers Can't Do Everything (The Halting Problem)][ref4]*   
- *[컴퓨터 과학이 여는 세계 1-3강, 서울대학교 이광근][ref5]*   


[ref1]:https://www.youtube.com/watch?v=zTfaOHCZky8&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=10

[ref2]:https://bi.snu.ac.kr/~bhkim/lectures/digi_com_10f_snu/02.Architecture.pdf

[ref3]:https://www.youtube.com/watch?v=cNN_tTXABUA

[ref4]:https://www.youtube.com/watch?time_continue=419&v=92WHN-pAFCs

[ref5]:https://www.youtube.com/watch?v=HTWSPoDLmHI&index=1&list=PL0Nf1KJu6Ui7yoc9RQ2TiiYL9Z0MKoggH
