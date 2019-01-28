---
layout: post
title: "CA Lecture Note 7 - Pipelining"
tagline: "7th lecture of Spring 2015 at MIT"
categories: [CA]
image: /thumbnail-mobile.png
author: "Hyung-Kwon Ko"
meta: "Seoul"
---

### [](#header-3)Pipelining
Pipelining is a way allowing us to use our components so that some are working on earlier inputs and other working on later inputs.

We are interested in the steady-state behavior of the system (imagine there is an infinite supply of inputs we have to take care of)

스테디 스테이트 값을 취하는 방향으로...

여기서 교수가 MIT방식과 하버드 방식이 있다고 빨래 예를 들어서 얘기하는데
하버드 = 그냥 1개씩 처리하는 것 (파이프라이닝 X)
MIT = 동시 처리 (파이프라이닝 O)
이렇게 이해하면 된다.

### [](#header-3)Performance measures
1. Latency: The delay from when an input is established until the output associated with that input becomes valid
- Harvard Laundry = 90 mins (하나씩 작동하므로 빨래 30분 + 드라이 60분 해서 90분)
- MIT Laundry = 120 mins (원래 있던 빨래감이 drier에서 60분동안 작동하므로 기존 빨랫감 60분 + 내가 지금 작업 중인 빨랫감의 laundry = 60분이 되어서 총 120분이 된다)

2. Throughput: The rate at which inputs or outputs are processed
- Harvard Laundry = 1/90 outputs / mins
- MIT Laundry = 1/60 outputs / mins

which one is better? there is no right answer


**So our job today is to figure out more of a systematic design technique for how to pipeline our circuits to increase the throughput and their price will pay for that is an increased latency.**

-------

### [](#header-3)Pipelining with circuit example
<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic43.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Pipelining with circuit, <a href="https://www.youtube.com/watch?v=XwLje35w5VQ&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=7">source</a> </div>

when F & G are done, H can work on them.
FG / H piece

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic44.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Pipelining with circuit, <a href="https://www.youtube.com/watch?v=XwLje35w5VQ&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=7">source</a> </div>

- unpipelined의 경우: F & G중 긴 것 = 20이니까 20에다가 H가 25니까 더하면 45
- 2-stage-pipeine의 경우: H에서 이미 작업 중인 거 생각하면 그게 끝날 때까지 25걸리니까 그거 더하기 새로 들어온거 작업 하는데 또 똑같이 H에서 25 걸리기 때문에 합치면 50이다. 그대신 throughput의 경우 1/25로 상승 (계속 일하니까)

-----


### [](#header-3)Pipeline diagrams
columns: successive clock cycles
rows: pipeline stages (keeping track of what is in the register for any clock cycles)

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic45.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Pipeline diagram, <a href="https://www.youtube.com/watch?v=XwLje35w5VQ&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=7">source</a> </div>

- the result associated with a particular set of input data moves diagonally through the diagram, progressing through one pipeline stage each clock cycle.


### [](#header-3)Pipeline conventions

Definition:
- k-stage pipeline is an acyclic circuit having exactly K registers on every path from an input to an output
- combinational circuit is thus an 0-stage pipeline

Convention:
- every pipeline stage, hence every K-stage pipeline, has a register on its output

Always:
- the clock common to all registers must have a period sufficient to cover propagation over combinational paths PLUS register PLUS register t_setup



### [](#header-3)Ill-formed pipelines

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic47.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, ill formed example, <a href="https://www.youtube.com/watch?v=XwLje35w5VQ&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=7">source</a> </div>

Problem:
- successive inputs get mixed: this happened because some paths from inputs to outputs have 2 registers, and some have only 1 **(BAD)**

**Strategy:** focus your attention on placing pipelining registers around the slowest circuit elements (BOTTLENECKS).

**파이프라인을 잘 그리는 방법:** 가장 긴 T를 갖는 circuit이 독립된 파이프 스테이지를 가지게 하면 된다. 제일 오래 걸리는 걸 독립된 파이프 스테이지!! 그래야지 throughput을 증가시킬 수 있음. 그리고 그 제일 긴게 throughput을 결정짓는 T가 된다.


### [](#header-3)Pipeline example

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic46.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Pipeline example, <a href="https://www.youtube.com/watch?v=XwLje35w5VQ&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=7">source</a> </div>

- 1 pipeline improves neither L nor T.
- T improves by breaking long combinational paths, allowing faster clock
- too many stages cost L, don't improve T
- back-to-back registers are often required to keep pipeline well-formed

in general will increase the latency, although if we are particularly good at drawing our contours and all the pipeline stages are exactly balanced, the latency will not increase (일반적으로는 latency를 증가시키는데, 잘 밸런스 되게 나누면 증가 안시키고 throughput만 증가시킬 수도 있다)



### [](#header-3)Pipelined components

Pipelined systems can be hierarchical:
- replacing a slow combinational component with a k-pipe version may increase clock frequency

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic48.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Bottleneck problem, <a href="https://www.youtube.com/watch?v=XwLje35w5VQ&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=7">source</a> </div>

Work around the bottleneck: we could improve throughput by
- finding a pipelined version of C
- interleaving multiple copies of C and running them in parallel (건조기에서 시간이 많이 걸리면 건조기를 많이 설치해놓으면 세탁기가 하나여도 건조기가 여러개니까 throughput이 늘어난다.)



### [](#header-3)Circuit interleaving

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic49.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Circuit interleaving, <a href="https://www.youtube.com/watch?v=XwLje35w5VQ&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=7">source</a> </div>

**can act like a n-stage pipeline**



### [](#header-3)Control structure taxonomy
<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic50.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Chris Terman, Taxonomy, <a href="https://www.youtube.com/watch?v=XwLje35w5VQ&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=7">source</a> </div>

Taxonomy: 분류학


### [](#header-3)References

- *[Computer Architecture MIT 6.004 2015 by Chris Terman][ref1]*   

[ref1]:https://www.youtube.com/watch?v=XwLje35w5VQ&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=7
