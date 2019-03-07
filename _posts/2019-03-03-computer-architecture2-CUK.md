---
layout: post
title: 서효중 교수 컴퓨터 구조 수업 노트 2-2
date: 2019-03-03 17:01:00
categories:
- Computer architecture
tags:
- none
author: "Hyung-Kwon Ko"
meta: "Seoul"
use_math: true
---


뭔가 내용을 저장하는 것은 플립 플랍에 저장을 할 수밖에 없다. 플립 플랍에 저장을 한 것이 바로 기계의 상태이다. 클럭보다 더 빨리 뭔가가 바뀔 수는 없다. 클럭이 빠를 수록 처리가 빠르다. 성능이 좋다. 물론 100%는 아니지만.

<br/>

## [](#header-2)<span style="color:#088A08"> *CPU Time* </span>
- 한 프로그램 당 싸이클 수
- CPU time = CPU clock cycles * Clock Cycle time = CPU Clock cycles / Clock rate
- CPU time can be improved by
  - reducing number of clock cycles for program = 어떤 프로그램을 돌리는데 필요했던 클럭의 개수 자체를 줄이는 것
  - increasing clock rate = 주파수를 높이는 것이다. 한 클럭의 싸이클을 줄이는 것이다. 동일 시간에 더 많은 주파수 사용
  - trade off = clock rate and cycle count = Clock Period 줄이더라도 동일한 instruction 쓰는데 여러개의 싸이클이 포함될 수 있다. 성능이 좋아지지 않는다. Clock period 가 요즘은 오히려 증가했다.


<br/>

## [](#header-2)<span style="color:#088A08"> *Instruction count and CPI* </span>
- 여기서는 클럭을 하나의 명령에 집중해서 보고 싶은 것이다.
- 한 개의 프로그램은 몇 개의 instruction으로 구성되었고, 총 몇 개의 클럭을 필요로 하였나?
- CPI: Cycle Per Instruction = 한 명령당 들어가는 싸이클 수
- CPU Time = Instruction Count * CPI(Cycle Per Instruction) * Clock Cycle time
= 인스트럭션의 수 * 인스트럭션 마다 필요로 하는 싸이클의 수 * 클럭마다 싸이클 타임
= 몇개의 명령이 쓰였으며 * 명령당 필요로하는 싸이클 수는 몇이며 * 싸이클 당 걸리는 시간은 몇이다.
- 클럭이 높아지면 CPU Time이 줄어들어서 성능이 좋아지는데 뭐가 문제인가? Set up time, Hold time 개념이 필요하다. 플립 플랍에 어떤 값을 입력하려면 그 값이 미리 와있어야 함. 오버 클럭이 발생하는 문제가 생긴다. (클럭 period가 짧아져서 성능이 좋을 것 같은데, 정작 필요로 하는 clock의 변화가 그렇게 급박하지 않음. 계산 시간의 지연이 줄어들지 않는다. 물론 온도가 낮아지면 계산이 빨라져서 클럭 period를 줄여도 돌아가고 그렇기도 한다.)
- Average cycles per Instruction:
  - Determined by CPU hardware
  - If different Instructions have different CPI = 복잡한 명령을 쓰면 쓸수록 instruction의 수는 적지만 지연 시간은 늘어나고, CPI가 늘어나고, 간단해지면 instruction의 개수는 늘어나지만 지연 시간이 줄고, CPI가 줄고, trade off가 있다.
- 따라서 단순하게 뭐가 좋다 말할 수 없다. 아주 철저한 공학의 산물이다. 똑같은 가격을 가지고 얼마나 좋은 성능을 뽑아낼 수 있는가?

<br/>

## [](#header-2)<span style="color:#088A08"> *Performance summary* </span>
- CPU Time = (Instructions / Program) * (Clock Cycles / Instruction) * (Seconds / Clock Cycle) = 프로그램당 명령의 수 * 명령 당 클럭의 수 * 클럭의 길이... 어렵지 않죠? 굳이 외울 필요 없다
- Performance depends on: (user에게는 일반적으로 elapsed time이라고 여길 수밖에 없다.)
  1. Algorithm: 여러개 걸리는 instruction을 안써도 되니까. instruction 수가 줄어드니까 당연히 좋다.
  2. Programming language: 자바를 쓰냐 뭐를 쓰냐...
  3. Compiler: 명령 수가 달라짐. 당연히 CPI도 달라짐.
  4. Instruction set architecture: 곱셈을 할 줄 아는 ISA / 곱셈 기능이 없는 ISA (아까 말한 장단점이고 모든 것에 영향을 준다.)

<br/>

## [](#header-2)<span style="color:#088A08"> *Another factor of Performance* </span>
- CMOS IC technology: Power = Capacitive load * Voltage * Frequency
- 설명하자면 Clock을 낮추는 것이 Frequency를 낮추는 것이고, Voltage를 줄이는게 동작 전압을 낮추는거 (물동냥 왔다갔다하는 너비를 줄이는 것, Capacitive load는 집적도)
  1. 클럭을 높일 수 있는대로 계속 했는데, 에너지의 위상을 바꾸는, 위치를 바꾸는 그런 일을 한 것이다. 0과 1을 따지는 것이 전압인데, 전압을 높였다 내렸다 한 것 = 에너지를 올렸다가 내렸다가 한 것이다. = 물통을 올렸다가 내렸다가 하는 것이랑 똑같다 = 클럭을 높이면 높일 수록 에너지를 더 많이 쓴다.(물론 성능이 올라가는 것도 사실이지만)
  2. 디지털이라는 것은 전압이라는 것을 가지고 0과 1이라는 값을 약속 하는 것 뿐이다. 그럼 0과 1 사이의 틈을 좀 줄이면 되지 않을까??? 0V - 10V가 아니라 0V - 5V이렇게 만들면 된다. (동작 전압을 낮추면 된다)
  3. Capacitive load = 모든 논리 회로는 트랜지스터로 이루어져 있다. 트랜지스터의 집적도가 커지면 됨. 0과 1을 구성하는 그 트랜지스터의 부하가 크기가 작아지면 됨. => 전력 소모가 줄어들게 됨.


<br/>

### [](#header-3) Miscellaneous
- 스마트 폰의 클럭 = 1.5GHz, 평소에는 500MHz = 동작 클럭을 낮춘다. 동작 주파수를 떨어뜨리면 전기에너지 소모가 낮아지기 떄문이다.

<br/>

## [](#header-2)<span style="color:#088A08"> *Multiprocessors* </span>
- Multicore microprocessors: 최근에는 더이상 클럭을 높이려 하지 않는다. 클럭을 높여봐야 전력 소모만 높아지고 성능은 크게 증가하지 않는다. => multi processor
- Requires explicitly parallel programming: 명시적으로 여러개가 돌아갈 수 있다라는 것을 프로그래머가 인지해야 한다.
  - compare with instruction level parallelism
  - hard to do (어렵다)
    1. Programming for performance = 우리가 짜는 프로그램의 흐름이 하나 있는데, 선후관계가 있는 경우가 많다. 앞에꺼를 해야 뒤에꺼를 할 수 있는 경우가 많다. 이걸 프로그래머가 다 고려해서 코딩을 해야 한다.
    2. Load balancing이 어렵다 = 일의 경중이 달라서 어떤 건 일이 많고 어떤건 놀고 있는 상황이 발생. 한 쪽은 느리고 한 쪽은 빠르고... 부하가 다르다.
    3. Optimizing communication and synchronization = 두개의 결과를 합치고 해야하는데, Communication 하기가 어렵다. 두개가 동시에 만나는 작업. 계산이 만나야 하는데 이걸 최적화 하기가 어렵다.
- Multi processor의 flow chart = 프로그램을 짤 때 2개 이상의 흐름이 있다. 그리고 그걸 별도의 CPU에 할당한다
- Multi thread programming이 가능한 사람과 못한 사람은 급이 다르다.



<br/><br/><br/>



## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[가톨릭대학교 컴퓨터 구조 by 서효중][ref1]*   

[ref1]:http://www.kocw.or.kr/home/cview.do?mty=p&kemId=695204
