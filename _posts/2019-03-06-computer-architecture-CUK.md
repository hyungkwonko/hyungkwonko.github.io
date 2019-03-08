---
layout: post
title: 서효중 교수 컴퓨터 구조 수업 노트 6-2
date: 2019-03-06 17:00:00
categories:
- Computer architecture
tags:
- none
author: "Hyung-Kwon Ko"
meta: "Seoul"
use_math: true
---
## [](#header-2)<span style="color:#088A08"> *Sequential elements(flip flop)* </span>
- Register: stores data in a circuit
  - uses a clock signal to determine when to update the stored value
  - edge-triggered: update when Clk changes from 0 to 1

<br>

## [](#header-2)<span style="color:#088A08"> *Clocking methodology* </span>
- 어떠한 상태 구성 요소가 조합회로에 의해서 다음 상태를 만들어내는 feedback 회로를 만들어내면 단계적 수행을 하는 것이 가능.
- 하나의 명령을 수행하는 것도 이런 몇 단계의 명령을 거치는 것이고, 한 줄 한 줄의 명령을 수행하는 것도 Program counter값이 증가하면서 한단계씩 상태가 바뀌는 거라고 할 수 있다. 클럭이라는 것을 기준으로 이러한 디지털 기계들이 굴러간다.

<br>

## [](#header-2)<span style="color:#088A08"> *Single cycle implementation* </span>
- 한 클러에 하나의 instruction을 수행하는 구현 - 구현의 대상은 명령이 될 것이다. AND, OR 등의 명령, Branch 등 등, format은 항상 4 byte의 format으로 되어있어서, 6bit의 operation이 앞에 있고, 나머지는 명령어의 종류에 따라 달라지는 부분이다.
- 제일 첫번째 단계 = instruction fetch: Program counter에 저장되어 있는 메모리 주소의 4byte를 instruction register로 불러온다. 그걸 메모리로부터 가져와야 무슨 명령인지 해석할 수가 있기 때문이다. 32 bit
- JUMP를 제외하면, 현재 program counter = program counter + 4 이렇게 된다. 한 명령은 4byte니까. instruction fetch하는 과정을 보면 논리회로 시간에 배운 것을 가지고 그대로 회로로 구현할 수가 있다.

<br/>

### [](#header-3) R-format instruction
- operation-6bit, source-5bit, target-5bit, destination-5bit
- read two register operands
- perform arithmetic / logical operation
- write register result
- ALU가 있어서 덧셈이라는 것을 해야하고, ADD의 결과가 나온 다음에, add source target destination. 계산한 결과 값이 write값으로, 기록할 값으로 들어오면 된다.
- 그러니까 레지스터가 32개가 있는데, 읽어낼 2개의 레지스터(rs, rt)를 지정을 하고, 기록할 하나의 레지스터를 지정을 하고(rd) 읽어진 2개의 레지스터는 ALU에서 연산을 하도록 만들어진다. 그리고 그 결과를 다시 Flip Flop인 D에다가 저장을 한다. R타입의 연산을 하기 위한 기본 요소들이다.

<br/>

### [](#header-3) Load / Store instruction
- operation 6bit, source register 5bit, target register 5bit, immediate 16bit
- 16bit 값을 32bit로 확장하는 조합회로 덩어리 (sign extension, 그냥 빈 곳 0으로 채우면 됨)
- Read register operands
- Calculate address: R + 16-bit offset (using ALU)
- Load: Read memory and update register. Source가 가리키는 레지스터의 값을 끄집어내서 immediate 16비트 값하고 더해서 그 다음에 그 메모리에서 그 값을 끄집어내서, target register에다가 집어넣으면 끝. 필요로 하는 요소는 메모리(메모리에 접근해야 함)
- Store: Write register value to memory. 지정하는 주소를 주면 값을 저장을 할 수 있어야 한다.
- immediate 16 bit 값은 32bit로 확장된 후(나머지 16bit를 0으로 채운 후), ALU의 한 요소로 온다. Read register한 것과 immediate 확장한 값은 더하면 Load나 Store할 주소 번지가 나오게 된다. 따라서 ALU의 연산 결과는 주소이다. 그 주소에 해당되는 것을 Load의 경우 메모리에서 값을 끄집어내서 아까 얘기했던 destination register에 저장을 하게 되면 Load 메모리이다. Store의 경우 읽은 레지스터의 값 하나하고 immediate 16 bit값 하고 더해서 store할 주소를 만들고, Store 메모리는 값을 읽어서 메모리에 기록을 해야 하므로, 값을 읽어서 그 값을 메모리에 write하면 된다.

<br/>

### [](#header-3) Branch instruction
- beq operation 6bit(64개 종류?), rs 5bit, rt 5bit, immediate 16bit
- Read register operands
- Compare operands
  - use ALU, subtract, zero output?
- Calculate target address (skip instruction)
  - sign-extend displacement
  - shift left 2 places
  - add to PC + 4
- rs와 rt값을 비교를 해서(ALU에서 뺄셈을 해서) 값이 0이면 두개가 같은 것이고, 만약에 JUMP가 맞다면 PC값을 조정해 주어야 한다. 두 개의 값을 비교한게 0이 아니라면 PC = PC + 4를 할 수도 있고. immediate 16값은 shift left 2를 하는데, 4배를 하는 것이다. beq 명령에서 immediate 값은 몇 개의 instruction을 뛰어넘을 것이냐기 때문에 byte단위로 바꿔주기 위해서 곱하기 4를 해주는 것이다.
- rs, rt 두개의 레지스터에서 값을 끄집어 내고, ALU에서는 뺄셈을 해야 한다. 결과 값이 0인지 아닌지 보기 위해서. immediate 16 = 32비트로 확장하고(남는거 0으로 채우기) shift left 2(4배곱)해서 PC값에다가 더해야 한다. 그러면 뛰어 넘어야 할 target address가 나오게 된다. 이거를 선택할지 말아야 할지는 아까 결과 값이 0인지 아닌지에 따라 달렸다.

<br/>

### [](#header-3) R-type, Load/Store Datapath

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic134.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Hyojung Seo, R-type, Load/Store Datapath, <a href="http://www.kocw.or.kr/home/cview.do?mty=p&kemId=695204">source</a> </div>

<br>

- Mux: 두 개가 와서 그 중에 하나를 선택해주는, 하나가 뭐고 하나가 뭐냐? 위에 길을 고르면 ALU에 그냥 꺼낸 값이 들어가는게 된다. ALU = 내가 access할 메모리값을 만들어 내는 역할을 한다.
- MUX: immediate 온 값을 1을 골라주면 되겠다.
- 회로를 만드는데, R-type과 Load / Store 둘 다 수용을 하기 위해서 윗 경로는 R-type을 위한 경로이고, 아랫길은 Load/Store를 위한 경로인데, 둘 중에 선택을 해야하기 때문에 MUX를 썼다.

ALU 앞에 있는 MUX: ALU에 들어가는 2번째 값이 R-type의 ADD 같은 경우는 ALU에 들어가야 하는 값이 Register, Load/Store에서는 immediate값을 받아야 하기 때문에 아래의 경로를 둘 중에 하나를 선택할 수 있게 만듦.

메모리 뒤에 있는 MUX: R-type의 ADD 같은 명령은 ALU의 결과가 write 데이터로 와야하고, Load는 메모리에서 끄집어낸 값이 write 데이터로 와야하니까 둘 중에 하나를 선택할 수 있게 만들어 놓은 것이다.

이 두개를 이용해서 R-type과 Load가 하나의 회로로 가능하게 만들어 놓은 것이다. 두 개 이상의 경로가 하나로 와서 충돌할 때는 반드시 MUX를 썼다. 둘 중에 하나를 선택해야 하니까. 충돌이 나는 것은 반드시 MUX를 써서 해결한 것이고, 하나가 두개로 갈라지는건 상관없다. 그냥 연결하면 된다.

<br>

### [](#header-3) R-type, Load/Store, Branch Datapath

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic135.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Hyojung Seo, R-type, Load/Store, Branch Datapath, <a href="http://www.kocw.or.kr/home/cview.do?mty=p&kemId=695204">source</a> </div>

<br>

- 이제 여기다가 beq같은 Branch를 합친다. 또 MUX를 써야하는데, (PC + 4) 한 값일지 아니면 JUMP한 address인지 선택을 해야하기 때문이다. 일반적인 명령이거나, beq가 고른게 0이 아닌 경우에는 (PC + 4)를 하면 된다. beq = 0이 나온 경우에는 MUX에서 sin extension을 통해 16 bit를 32 bit로 확장한 주소 값을 더한 곳으로 메모리를 JUMP하면 된다.
- 여기까지 하면 R-type, Load/Store, Branch를 하나의 회로로 구성하였다. MUX가 세개 등장했음. 3가지의 명령을 수행할 수 있는 구조를 만들었다. 이렇게 하면 Single cycle datapath 전체를 다 그렸다.

<br>

### [](#header-3) Single cycle datapath
- This datapath supports the following operations:
  - ADD, SUB, AND, OR, SLT, LW, SW, BEQ 까지 모두 해결
- 앞에 추가된 1개의 MUX: R-type은 operation 6bit - source 5bit - target 5bit - destination 5bit인 반면 Load/Store는 operation 6bit - source 5bit = write 5bit, 이렇게 되어 있어서 다르다. 따라서 write register 번호를 지정하는 MUX를 추가한 것이다.
- R-type 명령에서 function code에 따라서 덧셈을 할지 뺄셈을 할지 정해주는 제어 회로를 추가. ALU가 code에 부합하는 function을 돌린다.
- 명령이 뭐냐에 따라서 MUX가 뭐가 될지가 달라질 뿐이다.

<br/>

### [](#header-3) Single cycle control
- RegDst: Select destination register
- RegWrite: Specify if the destination register is written
- ALUSrc: Select whether source is register or immediate
- ALUOp: Specify operation for ALU
- MemWrite: Specify whether memory is to be written: Store메모리의 경우에만 1이다.
- MemRead: Specify whether memory is to be read: Load메모리의 경우에만 1이다.
- MemtoReg: Select whether memory or ALU output is used: Load메모리의 경우 1이다.
- PCSrc: Select whether next PC or computed address is used

그래서 이제 회로를 완성했는데, 한 클럭에 data가 기록되고 ADD 등 연산이 되고 이런 걸 자세히 설명할 수 있으면 될 듯.

<br/>

### [](#header-3) ALU control
- ALU used for
  - load / store: F = add
  - Branch: F = subtract
  - R-type: F depends on function field

ALU control 값이 뭐가될지 비트값을 할당해서 진리표 만들듯이 새로 짜면 된다.

| ALU control | Function |
| :-------------: | :-------------: |
| 0000 | AND |
| 0001 | OR |
| 0010 | ADD |
| 0110 | SUB |
| 0111 | set-on-less-than |
| 1100 | NOR |

- Assume 2-bit ALUOp derived from opcode
- 값이 뭔지 그 자체가 중요하지는 않고 명령에 따라 조합해서 만들면 된다.
- 대신 조합회로 각각 어떻게 작동하는지에 대한 선수적 이해는 필요하겠지...

<br/>

### [](#header-3) Implementing Jump
- jump uses word address
- update PC with concatenation of
  - top 4 bits of old PC
  - 26 bit jump address
  - 00
- JUMP를 해야하기 때문에 MUX가 하나 더 추가된다.
- opcode = 6 bit
- 아래 26 bit + 4배를 해서(shift left 2) 28비트 + Program counter 위에 4비트(이건 변하지 않으니까)를 잘라와서 32비트

<br/>

### [](#header-3) 다음 시간: Performance Issue
- Multiple-cycle concept
- pipelining


<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[가톨릭대학교 컴퓨터 구조 by 서효중][ref1]*   

[ref1]:http://www.kocw.or.kr/home/cview.do?mty=p&kemId=695204
