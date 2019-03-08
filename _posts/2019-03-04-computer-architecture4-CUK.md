---
layout: post
title: 서효중 교수 컴퓨터 구조 수업 노트 5-2
date: 2019-03-04 17:03:00
categories:
- Computer architecture
tags:
- none
author: "Hyung-Kwon Ko"
meta: "Seoul"
use_math: true
---
# [](#header-1)<span style="color:#088A08"> *MIPS instruction set architecture* </span>

<br/>

## [](#header-2)<span style="color:#088A08"> *MIPS register model* </span>
- ARM보다 심플하기도 하고 어느 측면에서는 더 뛰어난 성능을 가지고 있다.
- MIPS의 원형인 MIPS ISA를 배울 것이다.
- MIPS register model: ARM보다 2배 많다. (32bit)
- 첫번째 register = 무조건 0이다. 0은 제일 많이 쓰는 숫자이기 때문에. 매번 immediate instruction을 쓰기가 귀찮기 때문에, register 중 하나의 값을 0으로 fix해 놓은 것이다. 워낙 자주 쓰는 숫자이기 때문에 그렇다.
- 더 심플하고 general하게 만든 것이다.
- Floating point 연산에 대해서 배웠는데, 내부는 우리가 따로 배우지 않는다. floating point 연산을 하기 위한 register가 또 다르게 32bit로 할당되어 있다. 일반적인 용도로는 안쓰고 부동 소수점 용도로만 쓰기 때문에 고려하지 않아도 된다. 확장을 해서 64 비트로 쓸 수도 있다.
- Program counter: 지금 프로그램을 수행하는 코드의 위치를 가리키는 레지스터 (ARM에도 똑같이 있었던 값) 이 값을 바뀜에 따 Branch, Call, function Call 등을 구현한다.
- High & Low register: 곱셈, 나눗셈의 경우 원래 레지스터의 2배 크기가 필요하기 때문에 그렇다.

<br>

## [](#header-2)<span style="color:#088A08"> *MIPS register naming* </span>
- 0번: constant 0
- 1번: reserved for assembler
- 2,3번: return value (function이나 subroutine을 call하면 parameter를 넘겨주고 return value를 돌려받게 되는데 그 return value를 저장하는 곳)
- 5,6,7번: argument, (function이나 subroutine을 돌릴 때 넘겨주는 parameter들을 위한 argument용의 register)
- 8-15번: temporary, 임시 변수 i, j, k 굳이 메모리에 안써도 됨
- 16-23번: permanent, 변하지 않는 값
- 24-25번: temporary, 위와 마찬가지
- 26-27번: OS kernel, 운영체제가 쓴다.
- 28번: global pointer
- 29번: stack pointer
- 30번: activation frame pointer
- 31번: return address = 원래 수행 하던 곳으로 돌아와야할 주소를 저장
- ARM에 비해 훨씬 넉넉하기 때문에 이것 저것 담을 수가 있다.
- ARM에서는 register를 가리키는 형태로 32 bit instruction 중 4 bit를 썼었다. 레지스터가 16개 밖에 없으니까, 0000부터 1111까지 4비트면 커버가 되기 때문에 그렇다. MIPS는 32개 있으니까 5bit면 된다.

<br>

## [](#header-2)<span style="color:#088A08"> *MIPS memory model* </span>
- 크기는 0부터 byte단위로 주소를 붙여서 ffff ffff (8*4 = 32 bit)
- 32bit니까 MIPS는 4GB만큼의 address space 를 가지고 있다. (2^10 = KB, 2^20 = MB, 2^30 = GB)

<br>

## [](#header-2)<span style="color:#088A08"> *MIPS instructions* </span>
- MIPS와 ARM이 다르지만, instruction이 가져야할 form은 뻔하다.
  - e.g.) Shift, AND, OR, NOT 등 메모리에서 가져왔다가 다시 갔다놨다가, if then else, 점프
- arithmetic / logic instructions
- data transfer (LOAD, STORE) instructions
- conditional branch instructions
- unconditional jump instructions

<br>

## [](#header-2)<span style="color:#088A08"> *MIPS instructions detail* </span>
- arithmetic / logical operations
  - three operand format: result and two sources: 3개의 operand 사용될 수 있는 것, ARM과 똑같다.
  - operands registers, 16 bit immediate
  - compare operations: ARM에도 있는, 빼는데 update만 하지 않는 instruction
- integer multiply / divide: 곱셈, 나눗셈
  - iterative in hardware: 루프를 도는 방법
  - uses HI and LOW registers that must be explicitly accessed: 32bit * 32bit = 64bit이기 때문에, 위는 high, 아래는 low, 32비트에 해당하는 2개의 레지스터를 가지고 있는 것이다. 나누는 것도 마찬가지임, 몫과 나머지가 생기기 때문에, 2가지를 다 저장을 한다.
- floating point operations (무시... 다 있는데, 이건 일반적으로 신경 안써도 됨)
  - operate only on floating point registers
  - single and double precision (double uses paired registers)
  - typical operations are add, subtract, multiply, divide

<br>

### [](#header-3) MIPS arithmetic instructions
| instruction | example | meaning | comments |
| :------------- | :-------------: | :-------------: | :-------------: |
|add|add \$1,\$2,\$3| \$1 = \$2 + \$3 | 3 operands; exceptions possible|
|subtract|sub \$1,\$2,\$3|\$1 = \$2 - \$3| 3 operands; exceptions possible|
|add immediate|addi \$1,\$2,100|\$1 = \$2 + 100| + constant; exceptions possible|
|add unsigned|addu \$1,\$2,100|\$1 = \$2 + \$3| 3 operands; no exceptions|
|subtract unsigned|subu \$1,\$2,100|\$1 = \$2 - \$3| 3 operands; no exceptions|
|multiply|mult \$2,\$3|Hi,Lo = \$2*\$3| 64 bit signed product|
|divide|div \$2,\$3|Lo = \$2 / \$3, Hi = \$2 mod \$3| Lo = quotient, Hi = remainder|
|Move from HI| mfhi \$1 | \$1 = HI| Used to get copy of Hi|
| Move from Lo | mflo \$1 | \$1 = Lo | Used to get copy of Lo |
Subtract, Add 둘다 signed / unsigned가 있다.
2개 중에 대소를 비교하는 것은?

MOV from High, MOV from Low 등등 많다.

<br>

### [](#header-3) MIPS logical instruction
| instruction | example | meaning | comments |
| :------------- | :-------------: | :-------------: | :-------------: |
| and | and \$1,\$2,\$3 | \$1 = \$2 & \$3 | Bitwise AND |
| or | or \$1,\$2,\$3 | \$1 = \$2 | \$3 | Bitwise OR |
- 기타 많은데 생략하겠음.

<br>

### [](#header-3) MIPS Data transfer instruction
- Data Transfer instruction (LOAD / STORE)

| operation | example | meaning | comments |
| :------------- | :-------------: | :-------------: | :-------------: |
| Store word | sw \$3, 8(\$2) | Mem[\$2 + 8] = \$3 | Store word |
| Load word | lw \$3, 8(\$2) | \$3 = Mem[\$2 + 8] | Load word |
- 기타 많은데 생략하겠음.

<br>

## [](#header-2)<span style="color:#088A08"> *MIPS Displacement addressing* </span>
- ARM의 경우 13개의 addressing이 있었는데, 많긴한데 이걸 모두 알아야 할 필요는 없다. 대신 코드를 보고 이해할 수만 있으면 된다.
- 근본적으로 메모리를 직접적으로 access하는 것은 LOAD, STORE 밖에 없다.
- 32비트의 구조라는 것은? CPU안에 있는 register의 구조가 32bit이다. 그리고 안에 ALU가 있는데, 32bit, 32bit 두개를 덧셈을 해가지고 ADD를 하면 32bit 결과를 만들어내고 그런다. 가만히 보면 register는 메모리하고 값을 주고받고 한다. 그럼 메모리 한 단위의 폭은? 메모리가 1byte(8bit)라면 32bit를 읽어들이려면 4번을 해야한다. 따라서 32bit 컴퓨터가 제대로 되려면 모든 단위가 다 32bit가 되어야 한다. 메모리도 32bit 폭이고..

word 단위의 access는 4의 단위로만 access가 가능하다는 제한을 넣으면 모델이 간단해진다.
- all memory access exclusively through loads and stores
- aligned words, halfwords, and bytes
- floating point load / store for FP registers
- single addressing mode
- degenerate cases

<br>

### [](#header-3) MIPS conditional branch instructions

| operation | example | meaning |
| :------------- | :-------------: | :-------------: |
| branch | beq \$1, \$2, 100| if(\$1 = \$2) PC = PC + 4 + 100 (비교해보고 같았다면 100만큼 뛰어라)|
| branch2 | bne \$1, \$2, 100| if(\$1 != \$2) PC = PC + 4 + 100 (비교해보고 다르다면 100만큼 뛰어라)|

<br>

### [](#header-3) MIPS unconditional jump instructions
- 어떤 특정한 번지로 바로 뛰는 것이다.

| operation | example | meaning | comment |
| :------------- | :-------------: | :-------------: | :-------------: |
| jump | j 10000| PC = 10000| jump to address |
- 많지만 생략

31번째 register = return했다가 다시 돌아올 위치를 저장해둔 곳이다. JUMP and LINK = 현재 돌아올 곳을 저장해놓고 뛰겠다.

----

<br>

## [](#header-2)<span style="color:#088A08"> *실제 register format* </span>

1. ADD operation
```
add    rd, rs, rt          ; rd = rs + rt
  IR = mem[PC]             ; fetch instruction from memory
  R[rd] = R[rs] + R[rt]    ; ADD operation
  PC = PC + 4              ; calculate next address (Program counter값을 4만큼 증가시킨다, PC = PC + 4)
```
- 비트 할당 설명
  - op(6 bit): operation, add라는 명령을 가리키는 것
  - rs, rt, rd(5 bit씩 15 bit): register가 총 32개니까 그 중 하나를 가리킨다 하면 각각 5비트가 필요하다.
  - funct(6bit): instruction을 detail한 부분에서 조금 다르게 하는 것이다.
- 사실 ADD나 SUBTRACT나 다를게 별로 없는데, (subtract는 !이거로 반대 만들고 나서 +1하면 됨, 한마디로 a+b가 ADD면 a + !b + 1 이렇게 하면 SUBTRACT임)

<br>

2. SUB operation
```
sub    rd, rs, rt              ; rd = rs - rt
  IR = mem[PC]                 ; fetch instruction from memory
  R[rd] = R[rs] + (-R[rt] + 1) ; SUB operation (a + b 를 a + !b + 1 이렇게 만들어서 2의 보수법을 취한 것이다.)
  PC = PC + 4                  ; calculate next address (Program counter값을 4만큼 증가시킨다, PC = PC + 4)
```
- 따라서 ALU입장에서 하는 일은 똑같고 따라서 operation의 number가 000000(6bit)으로 똑같다. 둘이 다른 점이 있다면 SUBTRACT는 2의 보수 취해서 더한다는 점. 살짝 빗겨나가는 점만 반영을 해서 뒤에 function에 해당하는 부분만, (6bit, ADD: 100000, SUB: 100010) 이렇게 다르다.

<br>

3. lw Operation
```
lw    rt, rs, imm16
  IR = mem[PC]                 ; 메모리에서 instruction을 fetch한다.
  Addr = R[rs] + SignExt(imm16); 16비트 주소값을 32비트로 만들어준다.
  R[rt] = mem[Addr]            ; load data into register (그 주소 값을 통해서 메모리에 접근한 후 데이터를 LOAD해서 덮어쓴다.)
  PC = PC + 4                  ; calculate next address (program counter값을 4만큼 증가시킨다.)
```
- operation은 6비트. 따라서 기본 명령은 64가지가 생길 수 있다. 그 다음에 source하고 target 2개 밖에 없으니까 5bit씩 가리키고, 5 + 5 + 6 = 16, 따라서 16비트가 남아서 16bit의 immediate.
- MAKE THE COMMON CASE FAST! = 16 bit immediate 만 가지고도 충분히 쓸 수 있다고 생각한다.

<br>

4. sw Operation (위에 lw랑 똑같다.)
```
sw    rt, rs, imm16
  IR = mem[PC]
  Addr = R[rs] + SignExt(imm16)
  mem[Addr] = R[rt]            ; store data(그 주소 값을 통해서 메모리에 접근한 후 데이터를 STORE한다.)
  PC = PC + 4
```
- operation code도 1비트만 다르고 거의 똑같다고 보면 됨. 저장하는지, 불러오는지 그 차이만 있는 것이다.

<br>

5. beq Operation (Branch if EQual)
```
beq    rt, rs, imm16
  IR = mem[PC]                        ; 메모리에서 instruction을 fetch한다.
  Cond = R[rs] + ~R[rt] + 1           ; 2의 보수법을 이용해 뺄셈을 해서 같은지 다른지 계산을 한다.
  PC = cond ? PC + 4                  ; cond = 1이되면(두개가 다르면) + 4하고 끝내라
    : PC + 4 + (SignExt(imm16) << 2)  ; 4배(<<2)를 해서 더한다.
```
- 맨 마지막 줄에서 shift2를 하는 이유는?  100을 의미할 때는 25를 의미한다. 왜냐하면 명령어 한 줄의 크기가 4byte이기 때문에 그렇다. << 2 이걸 한다는 말은 4배를 한다는 말이다. 왜냐면 몇 개의 instruction을 뛰어넘냐는 말이 되는데, 4 byte가 1개의 instruction이기 때문에 4를 곱해줘서 몇 byte를 뛰어넘는지를 계산한 것이다.
- 일단 format은 위랑 똑같다. load word나 store word나 생긴 모양이 비슷하다. 다만 rt와 rs를 비교를 해서 그게 같으냐 다르냐에 따라서 같으면 immediate 16만큼 뛰겠다고 했음. detail하게 보면 instruction register
- ARM에 비해서 훨씬 쉽죠?

<br>

## [](#header-2)<span style="color:#088A08"> *C vs. Assembly* </span>
- C code
```C
q = h + A[i]
```
- Assembly code
```
add $t1, $s4, $s4     ; t1 = 2 * s4
add $t1, $t1, $t1     ; t1 = 2 * t1, 메모리(주소) 관점에서 하려고 4배를 해 준 것임.
add $t1, $t1, $s3     ; t1 = t1 + s3
lw $t0, 0($t1)        ; $t0 = Mem[$t1 + 0], 메모리 로드
add $s1, $s2, $t0     ; s1 = s2 + t0
```
  - q is mapped to s1
  - h is mapped to s2
  - s3 contains the base address of array A[]
  - i is mapped to s4


<br/><br/><br/>


## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[가톨릭대학교 컴퓨터 구조 by 서효중][ref1]*   

[ref1]:http://www.kocw.or.kr/home/cview.do?mty=p&kemId=695204
