---
layout: post
title: CA Lecture Note 6 - Finite state machine
date: 2019-01-18 17:00:00
categories:
- Computer architecture
tags:
- finite state machine
author: "Hyung-Kwon Ko"
meta: "Seoul"
use_math: true
---

Now we can build the systems of any size and expect them to be reliable and we can predict exactly how they behave from the behavior of the individual components

We have k bits of state = There could be totally $$ 2^k $$ combinations.

------

Finite state machines

a finite state machine has...
- k states: $$ S_1 , S_2, ..., S_k $$ (one is initial state)
- m inputs: $$ I_1 , I_2, ..., I_m $$
- n outputs: $$ O_1 , O_2, ..., O_n $$
- Transition rules: s' for each state s and input I
- output rules: outs for each state s

graphical representation of what the FSM does

we are going to label all of our states with names that have some sense to us.
initially no inputs have come in (unknown state - state X)

the output value is unlock signal 0.
output is the function of the current state.

we introduce an additional state and there is a transition between each state.

labeled by the value of the in wire. the more inputs that you have, the more complicated the label gets,  

if I am in state S of X and I get a 0(zero) in the input wire and the clock signal happens, I expect a transition to state as 0(zero) meaning that I've seen a 0(zero) on the input. -> remember one bit of history (remember one previous value)

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic29.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, State transition diagram, <a href="https://www.youtube.com/watch?v=VPwn07XI_n0&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=6">source</a> </div>

one path through the states of my machine.
I am trying to recognize the combination so I have labeled each state with the prefix of the combination that I have seen, and once I have seen the entire combination then I turn on the unlock signal.
- 그러니까 한개씩 가면서 비트를 기록하고 전체 다 보게되면 U를 1로 setting한다 (unlock = 1).

if nothing happens = If I stay in the state that I am in, you have to put a arc from the state to itself (자기 자신을 가리키는 화살표를 추가한다.)


why do these go to S0 and S01?
considering the last two digits, I have already seen the first two digits of the combination

0 1 1 0 --> will result in unlock signal

What is this diagram for?
whether I should be turning on motors or lights
or tuning off motors
waiting for the next door switch to close again...
- 그냥 잡다한 일을 하는 순서표라고 생각하면 됨
- 버튼을 키고 끄고 작동을 하고 멈추고 이런 일련의 스위치 같은 작동들
-  State Machine을 이용하여 개별 Object의 행동(동적인 측면)을 Modeling하며 이는 Object가 생명주기 동안 통과하는 State들을 발생하는 순서대로 명시한 행동이며 Object는 사건이 도달되면 반응하고 사건에 응답






<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic30.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Truth table of state diagram, <a href="https://www.youtube.com/watch?v=VPwn07XI_n0&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=6">source</a> </div>

How do I know a state diagram is valid?
arc leaving a state must be:
1. mutually exclusive: there should be one arc labeled 1, one arc labeled 0. (there should never be more than one departing arc)
- 1도 한개만 있고 0도 한개만 있고...

2. collectively exhaustive: every state must specify what happens for each possible input combination. "nothing happens" means arc back to itself. (there should be an arc for every possible input value)
- 1도 한개는 꼭 있고 0도 한개는 꼭 있고...

associated with current state && current input value

--------

FSM(finite state machine) Party Games

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic31.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, how many states at most?, <a href="https://www.youtube.com/watch?v=VPwn07XI_n0&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=6">source</a> </div>

if I have k state bits -> number of states in the state machine written...
Q1. at most $$ 2^k $$
Q2. at most $$ m * n $$ states
Q3. if you know NOTHING about the FSM, you are never sure!
if you have a BOUND on the number of states, you can discover its behavior.

Q3같은 경우는 최대 state 이동하는 횟수의 boundary가 있으면 알 수 있겠지만 아무것도 모르는 상태면 어려움.

FSM equivalence
<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic32.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, FSM equivalence, <a href="https://www.youtube.com/watch?v=VPwn07XI_n0&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=6">source</a> </div>

are they different? NOT in any practical sense. they are externally indistinguishable, hence interchangeable.

FSMs are equivalent iff every input sequence yields identical output sequences.

Engineering goal:
- have an FSM which works... (일단 작동이..)
- want simplest equivalent FSM. (심플한게 비용적인 측면이나 보는 측면이나 더 좋다. in terms of the hardware you have to devote)

**smaller but equivalent machine**

--------

why do we like the four state machine instead of the five state machine?
it has two statements($$2^2$$) instead of three($$2^3$$) -> read only memory is half the size (one less input to the read-only memory)

number of locations that read-only memory is $$ 2^k $$, reducing the number of inputs by one means the size of the read-only memory is half of what it was

(1개 인풋이 작아짐으로써 필요한 ROM사이즈가 절반으로 줄어든다. )

---------

One more thing

what if each button input is an asynchronous 0 / 1 level?

- to build a system with asynchronous inputs, we have to break the rules: we cannot guarantee that setup and hold time requirements are met at the inputs!
- let's use a synchronizer at each input

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic33.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Synchronizer, <a href="https://www.youtube.com/watch?v=VPwn07XI_n0&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=6">source</a> </div>

----

The mysterious metastable state

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic34.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Metastable state, <a href="https://www.youtube.com/watch?v=VPwn07XI_n0&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=6">source</a> </div>

when I am exactly at the middle point, I am at metastable state, unbalanced equilibrium.

how long can it balance at that balance point?
I cannot tell you exactly but it will happen soon anyway. (think of a ball rolling down the top of the mountain)

Metastable state: properties:
1. it corresponds to an invalid logic level
2. it is an unstable equilibrium; a small perturbation will cause it to move toward a stable 0 or 1.
3. it will settle to a valid 0 or 1 eventually.
4. BUT - depending on how close it is to the V_{in} = V_{out} "fixed point" of the device - it may take arbitrarily long to settle out.
5. every bi-stable system exhibits at least one metastable state

- synchronizers, extra flip flops between the asynchronous input and your logic are the best insurance against metastable states.
- the higher the clock rate, the more synchronizers should be considered

Pulse synchronizer: the idea is simply to quarantine(격리하다) it for some period of time. -> the probability of failure here is so low, you don't have to worry about it.


마지막으로: finite state machines can get you out of mazes(개미 탈출 게임...)







<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic24.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, two end points are stable, <a href="https://www.youtube.com/watch?v=gzuQaHtZpYw">source</a> </div>


### [](#header-2)References

- *[Computer Architecture MIT 6.004 2015 by Chris Terman][ref1]*   

[ref1]:https://www.youtube.com/watch?v=VPwn07XI_n0&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=6
