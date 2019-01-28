---
layout: post
title: "CA Lecture Note 5 - Sequential logic"
tagline: "5th lecture of Spring 2015 at MIT"
categories: [CA]
image: /thumbnail-mobile.png
author: "Hyung-Kwon Ko"
meta: "Seoul"
---

Something that we cannot build yet
what if you were given the following design specification:
버튼을 누르면 불이 켜지고
또 다시 누르면 불이 꺼지는 장치

-> the box must have some memory about what is going on because it treats the two button presses differently

memory device가 필요하다.
따라서 input + memory(current state) -> output

- memory stores CURRENT state, produced at output
- combinational logic computes
  - next state (from input, current state)
  - output bit (from input, current state)
- state changes on load control input

so there is a kind of sequential logic... 연속적인 논리
그리고 지금 그래서 그걸 배우려고 하는 것이다.

sequential logic = regular combinational logic + memory device

we need a writable memory, so off we go (무슨 말인지 모르겠음...)

기존에 가지고 있던 질문: how do we represent information ? - using VOLTAGE (이제 해결 했음)
이제 새로 나온 질문: how can we remember a voltage over any length of time? - using CAPACITORs (축전기 사용)

we have chosen to encode information using voltages and we know from physics that we can store a voltage as charge on a capacitor.

Pros:
1. compact - low cost / bit
Cons:
1. complex interface
2. stable
3. it leaks -> refresh

remain stable on the capacitor plate for about 10 milliseconds. how to handle this? before it goes away, we go back and re-read it before it turned into garbage and then re-write it. and this is refreshing.
날라가기 전에 다시 읽어와서 다시 쓴다. 이걸 refreshing이라고 한다.

-------
Using feedback: use positive feedback to maintain storage indefinitely. our logic gates are build to restore marginal signal levels, so noise should not be a problem.

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic23.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, using feedback to maintain storage indefinitely, <a href="https://www.youtube.com/watch?v=gzuQaHtZpYw">source</a> </div>

even though there is a small amount of noise, the inverter will still output a steady one.
this system recover from any little perturbation.

그러니까 무슨 말이냐면, 기존 capacitor같은 경우는 아주 작은양의 perturbation만 있어도 전압에 오류가 생길 수 있었던 반면에 inverter 2개를 연결시켜놓아서 1개 지나면 다시 1로 갔다가 다시 0으로 돌아오니까 무한히 정보를 오류가 없게 저장할 수 있게 만들어줌.

예를 들어 0 0 0 0 이렇게 가다가 0.05 이렇게 가도 1 갔다가 다시 0 되니까 결국은
0 1 0 1 0.05 1 0 1 0 ... 이런 식으로 다시 0으로 돌아오게 됨.

result: a bi-stable storage element (결과가 1 아니면 0)
as long as the device is plugged in, I am good. I am either 1 or 0.

-------

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic24.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, two end points are stable, <a href="https://www.youtube.com/watch?v=gzuQaHtZpYw">source</a> </div>

stable state....
- but small embarrassing dot in the middle which is an unstable equilibrium
- and those two stable states, zero and one, are the stable states we are going to build our memory device out of this component.
- this middle thing is quite troublesome but pretend it doesn't exist for now.
- 그러니까 그래프에서 y = x 랑 만나는 3개의 점에 대해서 설명하는데 양쪽 끝의 (0,0)이랑 (1,1)을 저장 상태로 쓸 것이고 중간에 문제가 되는 점은 문제가 되는데 어떻게 할 것인지는 나중에 설명할 것이다.(we will agonize over next time)

build a several element out of a multiplexer

pass through when the gate is open
memory mode when the gate is close

if we want to write something into the memory we open the gate and whatever is coming into D comes out of the Q, and when we want to remember it, we close the gate and now the device goes into memory mode.

-----

Settable memory element - lenient MUX

when G = 0, Q is stable
when G = 1, Q = D

close the latch
- when G = 0
- enter the memory mode
- feedback mode
- remember Q
- will reload it for you some later point

open the latch
- when G = 1
- Q follows D

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic25.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, new device: D Latch, <a href="https://www.youtube.com/watch?v=gzuQaHtZpYw">source</a> </div>

그림상에서 D가 high인지 low인지 이런게 중요한 것이 아니고 바뀌고 있는지 stable한지 그게 중요하다.
그래서 G는 확실히 높고 낮음이 표시 되어 있는데(1인지 0인지 구분하려고) D와 Q는 그런게 없고 바뀌는 것만 나와있다.

G = 1이면 Q는 D가 된다.

D가 바뀌면 Q도 바뀜.
$$T_{PD}$$: time that a new value propagate through the multiplexer circuit (이건 바뀌는 시간이라고 보면 될 듯, D에서 바뀐 값이 Q에 전달될 때까지 걸리는 시간)

combinational logic: we are only guarantee that the Q output is stable TPD after G changes

what we are going to do now is to use the leniency property to basically design register that actually behaves correctly as it goes from pass through note mode into memory mode.

We need one more set of constraints in order for my latch to work correctly.

in order for a latch to work correctly, you have to need it setup and hold time.

these are numbers the manufacturer of the latch tells you.

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic26.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, reliable latch, <a href="https://www.youtube.com/watch?v=gzuQaHtZpYw">source</a> </div>


1. setup time tells you how long before the falling edge of G, the D input has to be stable

2. hold time tells you how long after the falling edge of G, the D input has to be stable

그러니까 한마디로 G가 1에서 0으로 바뀌기 전후로 얼마나 D가 stable한 상태를 유지해야 하나(전: setup / 후: hold)

dynamic discipline for our latch:
1. $$T_{SETUP} = 2 T_{PD}$$
2. $$T_{HOLD} = T_{PD}$$

cannot work without tricky timing constraints on G=1 pulse:
- must fit within contamination delay of logic
- must accommodate latch setup, hold times

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic27.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, escapement strategy, <a href="https://www.youtube.com/watch?v=gzuQaHtZpYw">source</a> </div>

너무 긴 시간이어도 안되고 너무 짧은 시간이어도 안되는 문제...!
when time ticks in watch... similar problem.

-> gate를 2개 만들면 된다.
-> there is never a way for more than one car to get through it once because there is not a combinational path through the tollbooth.

So we can put two latch. if one of them is closed, the other is open, vice-versa
둘다 열리면 안됨. 한개가 열리면 한개가 닫히는 식이다.
there is no case that states change like mad. we are letting a new bit value into the register.

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic28.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, clock cycle, <a href="https://www.youtube.com/watch?v=gzuQaHtZpYw">source</a> </div>

Q가 언제 바뀌는지만 보면 된다. 이 싸이클동안 Q는 한번만 바뀐다.
edge triggered D register (sometimes people call them flip-flops)

$$t_{PD}$$: maximum propagation delay, CLK -> Q
$$t_{CD}$$: minimum contamination delay: CLK -> Q
$$t_{SETUP}$$: setup time, guarantee that D has propagated through feedback path before master closes
$$t_{HOLD}$$: hold time, guarantee master is closed and data is stable before allowing D to change

contamination time: minimum amount of time from when an input changes until any output starts to change its value

Timing in a single-clock system
1. $$t_1 = t_{CD, reg1} + t_{CD, L} \geq t_{HOLD, reg2}$$
2. $$t_2 = t_{PD, reg1} + t_{PD, L} + t_{SETUP, reg2} \leq t_{CLK}$$

### [](#header-2)References

- *[Computer Architecture MIT 6.004 2015 by Chris Terman][ref1]*   

[ref1]:https://www.youtube.com/watch?v=gzuQaHtZpYw
