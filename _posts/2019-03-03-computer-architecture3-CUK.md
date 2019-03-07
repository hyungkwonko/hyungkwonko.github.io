---
layout: post
title: 서효중 교수 컴퓨터 구조 수업 노트 3-1
date: 2019-03-03 17:02:00
categories:
- Computer architecture
tags:
- none
author: "Hyung-Kwon Ko"
meta: "Seoul"
use_math: true
---

## [](#header-2)<span style="color:#088A08"> *Spec CPU Benchmark* </span>
- Programs used to measure performance
  - supposedly typical of actual workload (Performance를 비교할 수 있는 기준: real 프로그램을 돌려서 성능 비교를 하면 됨. 근데 어떤 프로그램을 기준으로 해야 하는가? 왜냐하면 프로그램의 종류가 여러가지가 있기 때문에. 어느 하나를 정해서 비교를 하기가 쉽지가 않다.)
- Standard performance evaluation corp (SPEC)
- SPEC CPU 2006 (SPEC Benchmark: 여러 프로그램의 집합을 만들어 놓은 것이다. Elapsed time을 가지고 비교하면 되는 것이다. 우리가 평소에 많이 쓰는 파일들, 예를 들면 zip파일, Pearl interpreter, 바둑 인공지능 게임 등의 elapsed time 구해서 기하 평균 한 값)
  - elapsed time to execute a selection of programs
  - normalize relative to reference machine
  - summarize as geometric mean of performance ratios(대표 수 = 기하평균을 쓰는데, 왜냐하면 산술 평균의 맹점: outlier에 휘둘리기 쉽다. median이 낫다?)

<br/>

## [](#header-2)<span style="color:#088A08"> *Spec Power Benchmark* </span>
- 위에서 단순히 속도만 계산을 했다고 하면 이제는 전력 소모를 고려한다.
- Power consumption = power benchmark을 통해서 판단한다. = 적은 파워로 많은 것을 생산해내면 좋다.
- 1초 동안에 얼마나 많은 Java operation을 돌릴 수 있는가를 보는 것이다.
- 1초 동안에 몇 Joules의 전력을 먹는가를 보면 됨. 각 부하에서 몇개의 instruction을 수행할 수 있는지 파악
- 뭐든지 기본적으로 켜져 있으면 대기 전력이 있음.
- 구글 데이터 센터의 예를 보면 거의 대부분 10 - 50%의 로드를 사용한다. 부하가 많으면 100%의 부하로 돌리는 것이 좋다. 원래는 필요할 때만 부하를 100%로 쓰고 나머지는 아예 안쓰는 쪽으로 하는 것이 좋다. 그런데 그렇게 하기가 어려우니까...

<br/>

## [](#header-2)<span style="color:#088A08"> *Amdahl's Law* </span>
- improving of performance limited by...
- Corollary: make the common case fast = 일반적인 것을 빠르게 해라. 자주 일어나는 것을 빠르게 하는게 좋다. 왜냐면 그게 전체에서 차지하는 비중이 클테니까. (일반적으로 생각했을 때)
- Core 개수를 증가시키는 것은 비용을 증가시킨다. 어느 부분을 개선을 해야하는 것인가? CPU를 업그레이드 할 것인가? 램을 업그레이드 할 것인가? 어떻게 판단 내리나?
- Elapsed time을 어떻게 줄이나? 뭔가의 elapsed time은 여러 개의 구성 요소로 구성이 되는데, 내가 그 중에 하나를 많이 줄여도 개선할 대상이 차지하는 portion에 따라서 내가 개선할 수 있는 상한선이 제한된다.
- Computer architecture를 개선하는데, Performance를 끌어올릴 수 있는 부분이 큰 것을 올린다. 그게 기준이 시간이 되면 암달의 법칙이다.
- Fallacy: 잘못 알고 있는 것 = 그런 경우가 있긴 한데, 전체는 아닌 것 일부만 그런 것. 기본 먹는게 있기 때문에
- Pitfall: MIPS as a performance metric. 1초당 몇 개의 명령어를 수행하는가? MIPS = Million instruction per second. MIPS가 높은게 일반적으로 좋다. ISA가 다르면 MIPS를 가지고 비교를 할 수가 없다. 단순히 MIPS를 가지고 성능을 비교할 수는 없다. 그래서 spec benchmark같은 것을 쓰는 것이다.

<br/>

## [](#header-2)<span style="color:#088A08"> *Summary* </span>
- Abstraction, ISA
  - the hardware / software interface
- Performance
  - execution time
  - power
- Amdahl's law


<br/><br/><br/>


## [](#header-2)<span style="color:#088A08"> *References* </span>

- *[가톨릭대학교 컴퓨터 구조 by 서효중][ref1]*   

[ref1]:http://www.kocw.or.kr/home/cview.do?mty=p&kemId=695204
