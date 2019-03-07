---
layout: post
title: 서효중 교수 컴퓨터 구조 수업 노트 2-1
date: 2019-03-04 17:00:00
categories:
- Computer architecture
tags:
- none
author: "Hyung-Kwon Ko"
meta: "Seoul"
use_math: true
---
# [](#header-1)<span style="color:#088A08"> *Chapter1. Computer Abstraction and Technology* </span>
우리가 architecture라는 대상을 삼을 것을 어떻게 무엇을 대상으로 해야 하는가? 차후에는 성능 개선을 집중으로 잡게 된다. 그 성능에 대한 이해의 기초에 초점을 맞춘다.

<br/>

## [](#header-2)<span style="color:#088A08"> *Focus* </span>
- Program to the machine language = 컴퓨터란 기계어(machine language)를 해석해 연산을 하는 장치이다. 컴퓨터란? 중앙 처리 장치 = CPU = 제어, 연산, 기억장치에 의해 뭔가 계산을 하는 도구이다. 컴퓨터는 Machine Language를 Execute할 수 있는 도구. (하드웨어라고 생각하면 됨)
- The hardware / software interface = 하드웨어는 기계어의 연산 등을 수행할 수 있는 부분이고, 소프트웨어는 한마디로 프로그램이다. 하드웨어는 전자회로 덩어리 그 자체인데, 그 하드웨어가 수행하는 것은 machine language이고, 우리는 고급 언어를 짜서 compiler나 interpreter를 이용을 한다. 궁극적으로는 machine language가 하드웨어 소프트웨어의 interface 역할을 하고 있다.
- Program performance improvement = 프로그램은 빨리 돌아가면 좋은 것이다. 그럼 하드웨어는 프로그램을 빨리 돌릴 수 있게 만들면 좋다.

<br/>

## [](#header-2)<span style="color:#088A08"> *Program code* </span>
- High level language = 철저하게 알고리즘을 쉽게 만들 수 있는 것을 기준으로 한다.
  - abstraction for problem domain
  - productivity and portability = 우리들이 만드는 것은 하이 레벨 랭귀지인데, 이런 언어들은 포커스가 철저하게 알고리즘을 쉽게 짤 수 있도록 하는 것은 초점에 맞춘다. 당연히 핵심은 Productivity이다. 또는 Portability. 굳이 윈도우즈에서만 돌리는 것이 아니라 휴대폰에서 돌릴 수도 있고, 내가 어디서든 쉽게 돌릴 수 있도록 하는 것이 Portability.
- Assembly language = 기계어와 1:1대응이 된다. 1과 0의 덩어리다. Assembly language는 바로 기계어로 대응을 시켜서 그 바뀐 Machine language는 바로 하드웨어가 실행을 할 수가 있다. 인간이 Machine Language를 조금이라도 쉽게 이해할 수 있도록 하기 위해서 만들어진 것이 바로 Assembly Language이다. 당연히 하이 레벨 언어들은 컴파일러를 이용하면 어셈블리 언어로 만들어지고 어셈블러로 바뀌면 바이너리 값으로 바꾼다. 그러면 하드웨어가 바로 실행을 할 수 있는 단계로 바뀐다. 덩어리 안에 있는 값은 0101010.... 다 숫자 값이다. 모든 것은 이진 값으로 작동을 한다.
  - Basic elements
- Machine representation
  - Binary digits (bits)
  - Encoded instructions and data

<br/>

## [](#header-2)<span style="color:#088A08"> *Inside of the Processor(CPU)* </span>
- 가장 중요한 3가지 = Datapath + Control + Memory
- Datapath: data flow = 길
- Control: data handling = 데이터를 어디로 보낼지 = 길이 여러 갈래가 있는데 선택을 하는 것
- Memory: data storage = 데이터 저장. 길로 지나간다고 해도 저장이 되는게 아니다. 데이터 패쓰는 말그대로 길일 뿐이지 길이 그 데이터를 기억하는 것은 아니다. 데이터 값을 저장하는 장소가 있어야 한다. 누적을 할 수 있어야한다. 메모리가 필요하다.

<br/>

## [](#header-2)<span style="color:#088A08"> *Target of Abstraction* </span>
- Complexity handling: 이해는 할 필요가 있지만 모두 보여 줄 필요는 없다.
  - Hide lower level detail
- Instruction set architecture: 머신 랭귀지들이 어떻게 수행을 하는가에 대한 축약이 된다. 단순히 언어가 아니라 수행을 할 수 있는 환경까지 모두 포함.
  - the hardware / software interface = 운영체제가 가지고 있는 function 사용
- Application binary executed using
  - ISA, System calls
- Implementation = 구현, 수행은 instruction set 밑 하부 구조이다.
  - Beneath ISA interface =  intel과 AMD가 모두 기계어를 수행해야 하므로 ISA는 같지만 내부 구현(Implementation) 방식이 다르다.

<br/>

## [](#header-2)<span style="color:#088A08"> *Performance factors* </span>
- 어떤 기계가 성능이 좋은가? 프로그램의 실행 시간이 짧아야 한다.
- Algorithm = 알고리즘이 단순하면 된다. (1 + 2 + ... + 100 = (1 + 101) * 100 / 2. 이 둘 중에 뭐가 빠를지 뻔하다)
  - number of operation steps가 적으면 된다.
- Programming language, compiler = 한 스텝의 처리 속도가 빠르면 됨. 메모리를 멀리 두냐 가까이 두냐 이런 것은 컴파일러가 결정하기 때문에... 컴파일러가 같은 작업을 몇개의 머신 인스트럭션으로 할 것이냐를 결정하기 때문에 중요하다.
  - number of machine instructions executed per operation step
- Processor and memory system = 프로세서와 메모리 시스템... 신형 프로세서면 더 빠르다. DDR DDR2 DDR3 이런 식으로 메모리 성능이 좋아지면 성능이 빨라진다.
  - Speed of instructions which executed
- I/O system (including OS) = 입출력하고 연관이 되어있기 때문에 훨씬 더 원활하게 돌아간다.
  - Speed of I/O operations which executed

<br/>

### [](#header-3) Defining Performance
1. Response Time: How long it takes to do a task = 응답시간 = 응답할 때까지의 시간
2. Throughput: total work done per unit time = 단순히 response time이 아니라 전체의 양을 누가 더 빨리 할 수 있는가.
Performance: 여러가지를 고려해야 한다. 목적에 맞게 초점을 맞춰서 최적화해서 쓰면 됨. 일반적으로는 Response time을 고려한다. Throughput은 대수를 늘리면 된다. 차가 많이 못다니면 도로 수를 늘리면 됨.

<br/>

### [](#header-3) Relative Performance
- 성능에 대한 Metric, Performance = 1 / Execution time
- X is n time faster than Y
성능이라는 것은 Execution time의 역수가 되더라.

<br/>
### [](#header-3) Execution Time
- Elapsed Time = 우리가 실제로 느끼는 시간. 이것 저것 다 계산해서 나오는 시간.
  - total response time, including all aspects
    - Processing I/O, OS overhead, idle time
  - determines system performance
- CPU Time = Elapsed Time - I/O time - other jobs (우리가 컴퓨터 구조 시간에 1차적으로 성능을 upgrade 시킨다는 것은 하드디스크를 SSD로 바꾸자는 얘기가 아니다. 똑같은 명령을 수행할 수 있는, 똑같은 instruction set architecture로 정의되어 있는 것을 아까 얘기한 내부에 있는 구현을 어떻게 더 빠르게 할 것이냐는 것에 초점을 둔다.)
  - time spent processing a given job

<br/>

### [](#header-3) CPU time factor: Clock
CPU Clock은 아무도 신경 안쓴다. 비싸고 좋은 스마트폰이 더 클럭이 빨라진다. 근데 클럭이라는게 뭐냐?? 기억장소가 Flip-Flop이었는데, 내부에 뭔가를 저장하는 것이 Flip-Flop인데, 내부에 뭔가를 저장하는 것은 매 클럭마다만 가능하다. 플립 플랍에 뭔가가 저장되는 것은 클럭이 상승하는 순간만 가능하다. 계산을 하려면 계산한 값을 저장하고 더하고 하는 것인데, 더하려면 조합회로만 가지고는 기억을 못한다. 어딘가 기억을 해야만 한다. 내가 계산을 한 것은 저장하고 이거는 결국 플립 플랍이고 모든 것은 클럭에 의해 결정이 되고 따라서 클럭은 Time factor이다. 이거에 따라서 넣고 뺴고 하는 interval이 정해지게 되니까. 결론적으로 클럭이 빠르면 성능이 좋다.
- Clock period: duration of a clock cycle
- Clock frequency: cycles per second



<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[가톨릭대학교 컴퓨터 구조 by 서효중][ref1]*   

[ref1]:http://www.kocw.or.kr/home/cview.do?mty=p&kemId=695204
