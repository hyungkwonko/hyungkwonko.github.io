---
layout: post
title: 서효중 교수 컴퓨터 구조 수업 노트 4-1
date: 2019-03-04 17:00:00
categories:
- Computer architecture
tags:
- none
author: "Hyung-Kwon Ko"
meta: "Seoul"
use_math: true
---

## [](#header-2)<span style="color:#088A08"> *Logical operation* </span>
- Instruction for bitwise manipulation

| Operation | C | Java | ARM |
| :---: | :---: | :---: | :---: |
| Shift left | << | << | LSL |
| Shift right | >> | >>> | LSR |
| Bitwise AND | & | & | AND |
| Bitwise OR |  \| | \| | ORR |
| Bitwise NOT | ~ | ~ | MVN |

<br>

## [](#header-2)<span style="color:#088A08"> *Conditional operation* </span>
- 조건부 분기를 뺴면 프로그램이 순차적으로 진행이 되어 왔는데, 뛰어넘는게 생긴다. 그래서 그 특정 부분에 label을 붙여놓는다. 여길 뛰어라 이렇게.
```
CMP reg1, reg2  ;  이렇게 빼보기만 한다. 빼서 0이 되면 두 값이 같은 거다.
BEQ L1          ;  계산 결과가 0면 L1으로 뛴다 라는 얘기다. (Branch if equal)
```
```
CMP reg1, reg2  ;
BNE L1          ;  0이 아니면 뛰는 것이다. (Branch if not equal)
```
```
B exit          ;  무조건 뛰어라 조건 없음. (go to exit, unconditional jump)
```

<br>

### [](#header-3)Compiling IF statement
- C code:
```C
if(i == j) f = g + h;
else f = g - h;
```
- Compiled ARM code:
```
CMP r3, r4             ; load i, j and compare
BNE Else               ; go to Else if i != j
ADD r0, r1, r2         ; f = g + h (skipped if i != j)
B Exit
Else :
SUB r0, r1, r2         ; f = g + h (skipped if i = j)
Exit                   ;
```
- Branch는 뛰는 위치만 있으면 되고, 명령어만 다르다. 명령이 뭐냐, 뛰는 위치가 어디냐 이거만 있으면 된다.
- 어떤 instruction이든 32bit에 맞게 구성이 되어있다.
- 계산을 하는 대상은 반드시 레지스터에 있어야 한다.
- **ELSE, EXIT 이런 Label을 붙인 이유는? 절대 주소를 모르기 때문에 상대 주소(임시 위치)를 사용한 것**
- Label = instruction이 아니다. 명령이 아니라 위치다. 위치는 메모리의 주소이다. 메모리의 주소는 숫자여야 하는데, 숫자가 아니다. 왜 라벨을 달았나? 프로그램은 메모리에 있다. 컴파일 시에는 실행파일 덩어리는 하드디스크에 있다. 프로그램은 실행한다는 것은 하드 디스크에 있는 파일을 메모리로 카피해와서 실행을 하는 것이다. Else라든지 exit이라든지 이건 메모리 번지인데, 프로그램을 수행할 때에야 결정이 된다. 하드에 담겨 있을 때는 위치를 모른다. 프로그램을 실행하는 순간에 BNE가 몇번지, B가 몇번지 이걸 바꿔치기 한다. (컴파일 하게되면 숫자로 바뀌게 된다.) 최종 주소가 확정되는 것은 메모리가 로딩되서 실행될 때다. (상대 주소는 안다. 몇개를 뛰어넘어야 할지 알기 때문에)

<br>

## [](#header-2)<span style="color:#088A08"> *Compiling Loop Statement* </span>
- C code:
```C
while(save[i] == k) i += 1;
```
- Compiled ARM code:
```
LOOP:
ADD r12, r6, r3, LSL #2   ; r12 = address of save[i], 4 * r3 (shift left 2)
LOR r0, [r12, #0]         ; Temp reg r0 = save[i]
CMP r0, r5                ; r5 = k
BNE Exit                  ; go to Exit if save[i] = k
ADD r3, r3, #1            ; i = i + 1
B Loop                    ; go to Loop
Exit:
```
<br>

## [](#header-2)<span style="color:#088A08"> *Procedure Call* </span>
- Procedure call: 어떤 프로그램을 만들든, 여러개의 function을 만들어서 main에서 call을 하는 식인데,
  - parameter 값은 register에 넣어둠
  - control transfer: branch같은 걸로 넘어갔다가 assembly로 실행하고 돌아오는 것
  - acquire storage for procedure: 함수 내 변수에 대한 메모리 공간 할당
  - 실제로 실행
  - return 1, 0 이거 하면 register에 다시 담은 다음에 점프한 곳으로 다시 돌아온다.
- Steps required
  - Place parameters in registers = 파라미터 값을 넘겨줄 때 레지스터에 담는다.
  - Transfer control to procedure = branch로 분기해서 넘어가는 것이 control transfer이다. Branch가 바로 제어가 넘어가는 것이다.
  - Acquire storage for procedure = 함수 안에서 작업이 일 때도 그 안에 메모리를 할당해야 한다.
  - Perform procedure's operations = 함수 수행
  - Place result in register for caller = 리턴해서 값을 받는 것도 레지스터에 저장해서 받으면 되겠다.
  - Return to place of call = 끝나면 원래 점프 했던 곳으로 다시 돌아온다.

<br>

## [](#header-2)<span style="color:#088A08"> *ARM Register Conventions* </span>

원래 main 입장에서는 원래 값들을 보존하기 위해 register를 사용해야 하고, procedure 입장에서는 내부 계산에 register를 써야하고, 복잡하다. 교통정리가 필요하다. 사용자에 대한 규칙을 약속을 해버린다. 16개가 있는데
- 0-3번: argument(parameter) / return result / 용으로 사용한다.
- 2-3번: argument(parameter)
- 4-11번: variable for local routine = 변수용으로 사용하는 register (변수 값이 그대로 살아있기를 기대) = 원래 4-11번에서 쓰던 것은 메모리에다가 잘 보관해 놨다가 다시 복원을 하면 된다. 그러면 4-11번 register를 맘대로 써도 됨. 보존을 잘 해놔야 한다.
- 12번: ip(?)
- 13번: sp(stack pointer)
- 14번: lr(link register)
- 15번: pc(program counter) = 현재 읽고 있는 장소, 평소에는 4씩 증가하다가 점프 뛰면 따른대로 이동



<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[가톨릭대학교 컴퓨터 구조 by 서효중][ref1]*   

[ref1]:http://www.kocw.or.kr/home/cview.do?mty=p&kemId=695204
