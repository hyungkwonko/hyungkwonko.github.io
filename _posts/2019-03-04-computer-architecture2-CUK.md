---
layout: post
title: 서효중 교수 컴퓨터 구조 수업 노트 4-2
date: 2019-03-04 17:01:00
categories:
- Computer architecture
tags:
- none
author: "Hyung-Kwon Ko"
meta: "Seoul"
use_math: true
---
## [](#header-2)<span style="color:#088A08"> *Review* </span>
저장을 하는 장소는 레지스터 밖에 없고, 저장을 해 놨다가 return을 할 때, 이 레지스터의 값을 다시 돌려놓으면 된다. return result를 다시 0, 1번에 넣으면 된다. program counter는 프로그램 그 자체도 메모리에 들어가 있기 때문에, 지금 수행중인 것의 번호를 program counter에다가 저장하는 것이다. 우리가 임의로 넣는 것이 아니라 프로그램의 흐름에 따라서 바뀌는 것이다. Branch가 일어나지 않으면 자연스럽게 4씩 중가할 것이고, Branch가 일어나게 되면 Branch target(졈프하는 장소)에 가게 된다.

<br>

## [](#header-2)<span style="color:#088A08"> *Linked register(r14) & Stack pointer(r13)* </span>
- JUMP가 일어나는 순간에는 번지수(program counter)가 그 점프하는 곳으로 바뀌면 될 것이고, 4-11번 레지스터는 메모리에 저장(복사)을 해두면 될 것이다.
- 근데 subroutine이나 function이 불려질 때 돌아와야 하는 주소값에 대한 정보를 어디 담을 것인가?
- 따라서 link register = 내가 돌아올 곳을 저장해 두는 register.
- 함수 A, B, 쭉 쭉 불러나가다가 다른거를 수행하고 돌아오는 번지에 해당하는 것도 link register도 저장을 해놔야 한다.
- 저장할 때 다른거랑 같이 link register도 같이 저장을 한다. JUMP가 연속적으로 일어나더라도 link register를 바꿔가지고 저장해놓았기 때문에 문제가 되지 않는다.
- link register 메모리의 구조 = STACK 구조
- 우리가 기존 사용하던 메모리를 가지고 스택 구조로 사용하면 된다. (레지스터나 리턴 어드레스 값을 스택 구조로 놓으면 됨.) 메모리 번지는 증가하는 형태로 놔두고, SP라는 특별한 register를 마련한 다음에, stack의 시작 번지라는 것을 체크 해 놓는다. 하나 집어넣고 pointer를 앞으로 이동시키고, 값을 빼내면 pointer를 꺼꾸로 내리면 됨. SP라는 것이 메모리의 특정 위치를 가리키고, 그 안에 우리가 맘대로 값을 집어넣고 뺴면서 작동시키면 된다.

<br>

## [](#header-2)<span style="color:#088A08"> *Procedure Call instruction* </span>
- Procedure call: Branch and link
```
BL ProcedureAddress
# 이렇게 하면 link register에 return address에 저장을 해놓고 뛴다.
```
  - Address of following instruction put in lr = 주소를 lr에 넣고
  - jumps to target address = 타겟 주소로 뛴다
- Procedure return:
```
MOV pc, lr
# 이렇게 하면 쉽게 return할 수 있다.
# program counter에 link register에 저장된 값을 load하면 됨.
```

<br>

### [](#header-3) Leaf Procedure Example
- C code:
  - Argument g = r0
  - Argument h = r1
  - Argument i = r2
  - Argument j = r3
  - f = r4
  - result = r0
```C
int leaf_example (int g,h,i,j) {
  int f;
  f = (g + h) - (i + j);
  return f;
}
```
- ARM code:
```
SUB sp, sp, #12     ; Make room for 3 items (4byte * 3개)
STR r6, [sp, #8]    ; Save r4,r5,r6 on stack (집어넣음, 저장)
STR r5, [sp, #4]
STR r4, [sp, #0]
ADD r5, r0, r1      ; r5 = (g + h)(레지스터 내 맘대로 사용)
ADD r6, r2, r3      ; r6 = (i + j)
SUB r4, r5, r6      ; result = r4
MOV r0, r4          ; Result moved to return value register r0
LDR r4, [sp, #0]    ; Restore r4,r5,r6
LDR r5, [sp, #4]
LDR r6, [sp, #8]
ADD sp, sp, #12
MOV pc, lr          ; Return
```

<br>

## [](#header-2)<span style="color:#088A08"> *Non-leaf procedure* </span>
- Procedures that call other procedures = 함수 call한게 내부에서 또 다른 함수를 call하는 것 (nested call)
- For nested call, caller needs to save on the stack
  - its return address (return address를 잘 관리해주면 된다.)
  - any arguments and temporaries needed after the call
- Restore from the stack after the call

<br>

### [](#header-3) Leaf Procedure Example
- C code:
  - Argument n = r0
  - result = r0
```C
int fact (int n) {
  if(n < 1) return f;
  else return n * fact(n-1);
}
```
- ARM code:
```
fact:
  SUB sp, sp, #8      ; Make room for 2 items (4byte * 2개)
  STR lr, [sp, #8]    ; save return address (돌아올 위치 저장)
  STR r0, [sp, #0]    ; save argument n (파라미터 n 저장)
  CMP r0, #1          ; compare n to 1 (같거나 크면 뛴다)
  BG L1               ; Branch if greater than ~ (비교 해서 값이 0보다 크면 점프한다.)
  MOV r0, #1          ; if so, result is 1
  ADD sp, sp, #8      ; pop 2 items from stack
  MOV pc, lr          ; return to caller
L1:
  SUB r0, r0, #1      ; else decrement n (1빼고)
  BL fact             ; recursive call (다시 fact돌게 함)
  MOV r12, r0         ; restore original n
  LDR r0, [sp, #0]    ; and return address
  LDR lr, [sp, #0]    ; pop 2 items from stack
  ADD sp, sp, #8
  MUL r0, r0, r12     ; multiply to get result
  MOV pc, lr          ; return
```

<br>

## [](#header-2)<span style="color:#088A08"> *Load data on the Stack* </span>
- Local data allocated by callee
  - e.g. C automatic variables (로컬 변수는 스택에 넣었다가 삭제)
- Procedure frame (=activation record, 이 구조는 어떤 procedure가 실행될 때 생겼다가 종료되면 없어진다. 한개의 실행이 생길 때 그 procedure는 자신을 위한 한 개의 activation record를 만들게 된다. 그리고 걔가 또 함수를 call하면 그 용도의 activation record를 또 만들게 된다. 또 call하면 또 만들고, 또 call하면 또 만들고... 그리고 return 하면 stack에서 POP하면서 쭉 다 사라짐.)
  - used by some compilers to manage stack storage
- stack pointer: PUSH가 일어나면 보다 적은 번지로 내려가게 되고, POP이 일어나면 높은 번지로 올라간다.
- LOCAL variable은 모두 다 stack에다가 넣었다가 빼면 제일 깔끔하다. stack상에 메모리 기타 등등이 들어가 있는 그것. Procedure frame이라고 불린다. Data structure는 프로세스가 시작할 때 생겼다가 없어지면 삭제된다.
- MAIN - function A - function B - END 이렇게 실행된다고 치면, MAIN의 register - funciton A의 register - function A의 LOCAL Variable - function B의 register - function B의 LOCAL variable, 또 call하면 또

<br>

## [](#header-2)<span style="color:#088A08"> *Memory Layout* </span>
- text: program code
- static data: global variables = string으로 넣어놓은 것들이나, 안 바뀌는 static 값들
- dynamic data: heap = malloc, calloc이니 하는 것들을 여기에 쌓게 된다. malloc을 일정 부분 이상 받게 되면 STACK이랑 깨지게 되니까, segmentation fault 등이 나오게 되는 것이다.
- stack: automatic storage

<br>

## [](#header-2)<span style="color:#088A08"> *Branch instruction format* </span>
- condition(4 bit) + 12 (4 bit, 이건 뭐지???) + address(24 bit) = 32 bit
- condition bit는 4bit이고 따라서 16개가 있어야 하는데, 1개는 비어있어서 15개의 instruction이 있다. (15개의 JUMP type)

<br>

## [](#header-2)<span style="color:#088A08"> *Conditional execution* </span>
- ARM code for executing if statement
```
CMP r3, r4
BNE Else
ADD r3, r1, r2
B Exit
Else: SUB r0, r1, r2
Exit
```
- 위에 코드를 아래로 바꾸면 3줄로 줄어든다.
- 차이가 있다면 위에는 if else와 JUMP를 사용, 밑에는 한번에 비교까지 같이 해서 순차적으로 진행한다는 점.
```
CMP r3, r4;        // compare value (1 = diff, 0 = same)
ADDEQ r0, r1, r2;  // if compare value = 0 (same)
SUBNE r0, r1, r2;  // if compare value = 1 (different)
```

<br>

## [](#header-2)<span style="color:#088A08"> *Addressing mode* </span>
- Addressing mode: 주소라는 것은 어떻게 지정할 수 있는가? 계산을 받는 그 대상 자체가 어떤 형태로 지정되어 있는가. 왜 addressing인가? 레지스터 번호도 주소고 다 주소로 되어있으니까. 계산을 받는 것을 지정하는 것은 addressing mode. scaled register. word 단위 주소를 byte 단위로 바꾼다든지.
- program counter relative: Branch code = 주소 공간 24bit = 16MB만큼 뛸 수 있다, PC elative, 상대적인 위치만큼 뛰는 것
- Memory를 다룰 때 Array를 굉장히 많이 다룬다. 이런 Array를 Access할 때 어떤 방법이 가장 효율적인지에 대한 논의일 뿐이다. 다양한 형태의 addressing이 있다는 것을 알고 있으면 된다.


ADD r2, r1, 5
ADD r2, r1, r0

scaled register = 4배를 해줬다. 이랬던거 LSL \#2 이렇게 했던거 bit shift했던거 1 -> 100 이렇게 하면 4배 되는거

immediate: Store, Load memory Target register

scaled register offset

immediate offset pre-indexed: 특정 레지스터로

immediate offset post-indexed: ...

<br>

## [](#header-2)<span style="color:#088A08"> *Translation and startup* </span>
- 순서: C Program --[Compiler]--> Assembly language program --[Assembler]--> Object: machine language module --[Linker]--> Executable: machine language program --[Loader]--> Memory
- Object코드: Native한 소스. 여기에 라이브러리가 붙어야 한다.
- static linking: Object를 한 덩어리로 만드는 것
- dynamic linking: 한 덩어리로 만드는 것이 아니라 dll로 분리해 놓는 것이다.
- linker: 바이너리 파일 앞에 OS용 헤더를 붙인다.
- OOS용 헤더: 윈도우와 리눅스(운영체제) 호환.
- Executable: Object에 헤더가 붙은, 하드디스크에 저장된 실행할 수 있는 프로그램(.exe)
- LOADER: 운영체제의 프로그램, 마우스나 더블클릭 등으로 파일을 로드하도록 명령을 내리는 것. 바이너리를 쭉 읽어서 OS에게 메모리를 allocation받고 데이터를 copy해놓고 main entry point에서 실행하라고 명령을 내린다.

<br>

## [](#header-2)<span style="color:#088A08"> *Loading a program* </span>
- Load from image file on disk into memory
  - Read header to determine segment sizes
  - create virtual address space
  - copy text and initialized data into memory
  - set up arguments on stack
  - initialize registers
  - jump to startup routine: 메인으로 들어간다.

<br>

## [](#header-2)<span style="color:#088A08"> *Dynamic linking* </span>
- only link/load library procedure when it is called
  - requires procedure code to be relocatable
  - avoids image bloat caused by static linking of all referenced libraries
  - automatically picks up new library versions

<br>

## [](#header-2)<span style="color:#088A08"> *Starting Java Application* </span>
- JVM (가상머신): 자바 byte code를 interpret해서 돌아가거나 JIT compiler로 돌리든지.

<br>

## [](#header-2)<span style="color:#088A08"> *ARM & MIPS similarities* </span>
- ARM: the most popular embedded core
- similar basic set of instructions to MIPS (둘이 매우 비슷하고 MIPS가 훨씬 간단하다)
- ARM에 비해 MIPS가 data addressing mode가 간단함 (9 -> 3)
- ARM에 비해 MIPS가 register 개수가 2배임 (16 -> 32)
- 둘다 I/O 는 memory mapped

|      | ARM | MIPS |
| :------------- | :-------------: | :-------------: |
|data announced|1985|1985|
|instruction size|32 bits|32bits|
|address space|32 bit flat|32 bit flat|
|data alignment|aligned|aligned|
|data addressing modes|9|3|
|registers|15 * 32-bit|31 * 32-bit|
|I/O|memory mapped|memory mapped|


<br/><br/><br/>


## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[가톨릭대학교 컴퓨터 구조 by 서효중][ref1]*   

[ref1]:http://www.kocw.or.kr/home/cview.do?mty=p&kemId=695204
