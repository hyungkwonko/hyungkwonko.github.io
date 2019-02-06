---
layout: post
title: CA Lecture Note 8 - Design tradeoffs
date: 2019-01-21 17:00:00
categories:
- Computer architecture
tags:
- design tradeoffs
author: "Hyung-Kwon Ko"
meta: "Seoul"
use_math: true
---

### [](#header-3)Optimizing your design
Optimization metrics:
- Area of the design
- Throughput
- latency
- power consumption - run all day... with teeny tiny battery
- energy of executing a task - energy is the integral power over time, minimizing the energy of executing a task
- etc


### [](#header-3)static power dissipation
<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic51.png" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>기술연구소 개발2팀 김윤주 부장, MOSFET과 FIN-FET, <a href="http://www.amkor.co.kr/archives/590">source</a> </div>

그림에서 보면, 소스 위에 찍힌 노란색 점들은 각각 그 트랜지스터에서 흐를 수 있는 면적을 의미하고 이는 흐를 수 있는 전류의 양과 비례한다. 구조적으로 왼쪽 평면 FET는 2차원적으로 평면에서 한 면으로만 전류가 흐르지만, 오른쪽 Fin-FET은 앞면, 뒷면, 그리고 적게나마 윗면까지 3차원적으로 입체적인 3개 면을 통해 훨씬 많은 양의 전류를 보낼 수 있다. 즉, 평면 FET는 종이의 한 면만 사용하는 반면, Fin-FET은 종이의 앞뒤면을 모두 쓰고 실제 종이에서는 가능하지 않지만 종이의 옆면까지도 사용하는 셈이다.

하지만 그림에서 보는 것처럼 Fin-FET이 실리콘 위에서 차지하는 면적은 오히려 적다. 결국, 또다시 Fin-FET 기술을 통해 그동안 반도체 집적도 개발을 주도해 왔던 전형적인 방법인, 트랜지스터 면적을 줄여 집적도를 높이는 것과 같은 효과를 가져올 수있다. 게다가 Fin-FET은 게이트가 누설전류 없이 좀더 효과적으로 전류의 흐름을 조절할 수 있어서 더욱 더 게이트 렝스를 감소시킬 수 있는 여지를 제공한다.

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic52.png" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>기술연구소 개발2팀 김윤주 부장, MOSFET과 FIN-FET, <a href="http://www.amkor.co.kr/archives/590">source</a> </div>


기존의 평면 FET이 물이 흐르는 호스를 단순하게 위에서 눌러 물의 흐름을 평면적으로 제어했다면, Fin-FET는 움켜쥐는 방식으로 물의 흐름을 입체적으로 조절한다고 볼 수 있다. 그래서 기존 방법보다 더욱 효과적으로 누설전류 없이 전류의 흐름을 매우 효과적으로 조절할 수 있다.

- 요약 1: 집적도 up
- 요약 2: 누설 방지 기술 up

in theory it is off and practice those little drips can add up to many gallons if you had a lot of faucets and you were integrating over a long period of time (바가지 물이 새어가지고 홍수 난다 이말임)


### [](#header-3)Dynamic power dissipation

While charging and discharging capacitor, current flowing through a device dissipates power in the form of heat.

Energy to discharge & recharge can be calculated firmly with mathematics (수식은 굳이 캡쳐 떠서 올리지는 않겠음... 그냥 물리 법칙 P = VI 해서 하는거)

We are pretty much limited by the total power dissipation.



### [](#header-3)How can we reduce power?
- So, how de we reduce power? let's not do work whose output we don't care about

- How do we prevent transitions from happening? to make sure that everybody doesn't go to work. only the unit, which of the all units I am choosing is busy.

- What parts of the circuit are not working in this particular cycle of the computer. Maybe I can make them not work (불필요한 것 최소화)

- dynamic power dissipation이 static power dissipation보다 크기 때문에 필요없는 dynamic power dissipation이 생기는 **transition** 을 최소화한다. 가만히 유지하는건 뭐 어쩔수없고..

- no transition, much less power dissipation


### [](#header-3)Improving speed: Adder Example

#### [](#header-4)Worst case

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic53.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, carry ripple adder, <a href="https://www.youtube.com/watch?v=PttbsFqP5q4&index=8&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

- series of adders
- not only how fast is a particular circuit, but in general how does the speed relate to the number of input bits
- how much fast or slower is it relative to sth?

중간에 Asymptotic notation 설명함. (skip)


#### [](#header-4)Carry select adders

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic54.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Carry select adders, <a href="https://www.youtube.com/watch?v=PttbsFqP5q4&index=8&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

- 위에 방법이랑 비교해서 2배 빠름
- 중간에 단계를 2개로 나누고 앞 절반이 계산되어 나오는 결과가 0 혹은 1이 될테니 뒷 절반이 0에서 시작한 경우 / 1에서 시작한 경우를 모두 돌려서 앞 절반 계산 결과가 나오면 뒷 절반 2개의 case로 한 것 중 하나를 고르면 됨. GOOD 이해하기 쉽고 속도 2배되는거 당연...
- a sort of tradeoff (속도 up by using more hardware)


#### [](#header-4)Faster carry logic

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic55.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Faster carry logic, <a href="https://www.youtube.com/watch?v=PttbsFqP5q4&index=8&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

- if A = B = 1 or 0, $$ C_{in} $$ doesn't matter to the outcome
- if one of them(A or B) is 1 then the outcome is only related to the propagate term(P) not generate term(G).
- 한 쪽만 중점적으로 집중하겠다. 이렇게 생각하면 될 듯


#### [](#header-4)Carry look-ahead adders(CLA)

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic56.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, CLA, <a href="https://www.youtube.com/watch?v=PttbsFqP5q4&index=8&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>


<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic57.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Neso academy, CLA explained, <a href="https://www.youtube.com/watch?v=6Z1WikEWxH0">source</a> </div>

- 캐리 룩 어헤드 에더의 핵심은 계산을 한번에 할 수 있다는 것이다!!
- 원래는 C0를 통해서 C1을 계산하고 그 나온 결과 C1을 통해서 C2를 계산하고 그 나온 결과 C2를 가지고 C3를 계산하는 등 차례대로 해야되기 때문에 그 시간이 4번하면 4만큼 걸리고 10번 하면 10만큼 걸리고 하는 것인데, CLA를 사용하면 C0를 통해서 C1을 구했으면 그 식을 통해서 C3까지 한번에 갈 수 있기 때문에 1번 계산한 만큼만 걸려서 4번가지는걸 1번 하는 양으로 시간을 1/4로 단축시킬 수 있음. 자세한 설명은 ref5의 설명이 아주 GOOD!!
- A1, B1, A2, B2 이것 때문에 헷갈렸는데 이 값들은 정해진 거니까 time delay에 영향을 주는게 아니다. 중요한건 계산하고 그걸 바탕으로 propagate하는데 시간이 오래 걸리는 것이기 때문에 그게 없으면 delay가 줄어들 수 있음.

- growes as log(N) ! not linear...


### [](#header-3)Binary Multiplication

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic58.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Combinational Multiplication, <a href="https://www.youtube.com/watch?v=PttbsFqP5q4&index=8&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

- easy part: forming partial products (= AND gate, 1*1 = 1 others are 0)
- hard part: adding M N bit partial products



### [](#header-3)Reduce area with sequential logic

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic59.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Reduce area with sequential logic, <a href="https://www.youtube.com/watch?v=PttbsFqP5q4&index=8&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

add the previous result to the accumulator and basically repeat that operation. go through a sequence of operating this piece of hardware again and again for n steps, end up getting the right answer out. (n번 반복해서 한개에다가 계속해서 aggregate, update한다고 보면 될 듯)
- longer latency than the previous approach but instead of the N^2 hardware, the size = n hardware.
- saving a lot of hardware here

how to increase the throughput? using the pipelining

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic60.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Carry-save pipelined multiplier, <a href="https://www.youtube.com/watch?v=PttbsFqP5q4&index=8&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7">source</a> </div>

다른건 유지되었지만 Throughput 1로 증가하였다.
결론: 머리를 잘쓰면 돈이 굳는다.


### [](#header-3)References

- *[Computer Architecture MIT 6.004 2015 by Chris Terman][ref1]*   
- *[반도체, 그리고 현재 – 누설전류를 해결하는 기술 HIGH-K와 FIN-FET][ref2]*
- *[Working Principle of MOSFETs Field Effect Transistors][ref3]*
- *[Carry-Lookahead Adders explanation][ref4]*
- *[Carry Lookahead Adder (Part 1) - BEST explanation !!!][ref5]*

[ref1]:https://www.youtube.com/watch?v=PttbsFqP5q4&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=8

[ref2]:http://www.amkor.co.kr/archives/590

[ref3]:https://www.youtube.com/watch?v=uYhScOlWkmQ

[ref4]:https://www.youtube.com/watch?v=-LVxHFUtZHQ

[ref5]:https://www.youtube.com/watch?v=6Z1WikEWxH0
