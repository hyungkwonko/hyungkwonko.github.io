---
layout: post
title: 서효중 교수 컴퓨터 구조 수업 노트 7-1
date: 2019-03-07 17:00:00
categories:
- Computer architecture
tags:
- none
author: "Hyung-Kwon Ko"
meta: "Seoul"
use_math: true
---
## [](#header-2)<span style="color:#088A08"> *Performance issues* </span>
- Clock period: longest delay = 가장 오래 걸리는 작업에 초점이 맞춰지게 되어있다.
  - critical path: load instruction = ADD이런건 간단했었는데 반면 Load/Store는 오래걸림, 주소 계산, 로드 메모리, 등등.. 할일이 많기 때문에 가장 오래 걸리는 작업은 데이터 로드이다.
  - instruction memory - register file - ALU - data memory - register file
- Violates design principle
  - making the common case fast: 자주 나타나는 작업을 빨리 처리하게 해야 하는데...
- We will improve Performance by
  - multiple-cycle: 한 클럭에 하나의 명령을 수행하는 것이 아니라, 여러 클럭에 하나의 명령을 수행하게 하면 안될까? 로드같이 오래 걸리는 작업은 여러개의 클럭 싸이클을 쓰게하고, R-TYPE 명령은 클럭 싸이클을 한 개만 쓰게 하면 어떨까?
  - pipelining:

<br/>

## [](#header-2)<span style="color:#088A08"> *Multiple cycle concept* </span>
- ALU가 그 전에는 3개가 있었는데 1개가 되었음.
- 일반적으로는 instruction과 데이터가 하나의 RAM에 다 들어가 있다. single cycle 구현에서는 이게 분리되어있었다.
- Program Counter + 4 를 하는 ALU가 없어졌음. 따라서 1개 남은 ALU가 그 작업을 한다.
- MUX가 2:1이었는데 4:1로 되었다. 또 2:1 MUX가 1개 새로 생겼음.
- instruction register라고 불리는 Flip-Flop을 하나 새로 만듦.
- register에서 꺼낸 값을 저장하는 A, B라는게 생겼음. (보존을 하는 역할)
- ALU out = ALU가 클럭의 경계를 넘어가면서 결과를 저장할 곳이 없는데, 두 개의 입력을 받아서 나중에 ALUout을 뽑아서 write data 로 집어넣을 게 필요하다.

한 명령을 여러 클럭에 나눠서 실행할 수 있게 함으로써, 명령마다 필요로 하는 클럭의 수를 다르게 할 수 있게 되었다. 여러개의 클럭동안 명령을 수행해야 하기 때문에 클럭의 경계를 넘는 동안 저장을 하기위한 네모 박스들이 많이 생겼다. 네모 박스들은 플립 플랍일 뿐이다.

과거에는 single cycle에는 제어기가 그냥 조합 회로이면 되는데, multiple cycle에서는 state machine이 되어야 한다. 명령이 뭐냐에 따라서 state machine의 상태가 달라지면 되고, 그리고 그 상태에 따라서 출력을 만드는 조합회로가 있어서 이 출력으로 제어 워드를 만들면 된다.

<br/>

### [](#header-3) instruction fetch
- Program counter가 가지고 있는 주소번지의 4 byte 값을 끄집어내서 instruction register에다가 넣는다.
- Program counter에 있는 값을 MUX로 가져와서 4를 더해서 그 값을 만들어 대기하고 있다가 다음 클럭 증가하는 순간 출력된 instruction이 Flip-Flop에 저장이 되고 Program counter 값이 업데이트 된다.

<br/>

### [](#header-3) instruction decode & Register fetch
- 이제 명령은 instruction register안에 들어와 있다.
- 이 안에 있는 명령을 끄집어 내서 decode한다. = 이게 뭔지 알아낸다. (fetch 때는 이게 뭔지 모른다.) 앞에 6 bit를 봐야 알 수 있다.
- immediate 값 가져와서 ALU에서 연산해서 ALUOut에 넣는다. = 아직 까지도 명령이 뭔지 모른다.
- Program counter에 beq 해가지고 JUMP할 target address 계산을 해놓는 것 (혹시 명령이 beq일지 모르니까..)

<br/>

### [](#header-3) Execution
- Execution 단계에서부터 명령에 따라 할 일이 달라진다.
- ALU performs one of three functions
- RTL description

<br/>

### [](#header-3) Completion
- r format: ALUOut에 내가 계산한 결과를 저장 해놨음. 그걸 register에 엎어 쓰면 된다.
- Load: ALUOut에 주소를 계산해 놨기 때문에 그 주소를 메모리에 주고 메모리에 있는 값을 끄집어 내서 레지스터에 가져다 논다.
- Store: 주소를 가지고 B라는 register에 저장된 값을 메모리에 엎어쓰면 된다.


<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[가톨릭대학교 컴퓨터 구조 by 서효중][ref1]*   

[ref1]:http://www.kocw.or.kr/home/cview.do?mty=p&kemId=695204
