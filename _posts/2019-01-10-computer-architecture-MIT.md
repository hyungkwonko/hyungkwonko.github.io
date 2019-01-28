---
layout: post
title: "CA Lecture Note 2 - Digital abstraction"
tagline: "2nd lecture of Spring 2015 at MIT"
categories: [CA]
image: /thumbnail-mobile.png
author: "Hyung-Kwon Ko"
meta: "Seoul"
---

### [](#header-3) Today's lecture
- Building electrical circuits that allow us to manipulate, store and transmit information  
<br/>

### [](#header-3) What makes a good bit?  
- cheap, small(cause we them need a lot !!)  
- stable, reliable  
- ease of manipulation  
<br/>

### [](#header-3) we use voltage to encode information

_then what is the advantage of using voltages?_
1. easy generation, detection  
2. lots of engineering knowledge  
3. potentially zero power in steady state  

_disadvantages_
1. easily affected by environments  
2. DC connectivity required?  
3. R & C effects slow things down  
<br/>

### [](#header-3) What makes our system imperfect? - voltages...  

Mathematically, there is no room for error, but in reality, things could be simply changed or affected by some unknown factors.  

Keep in mind that the world is not digital, we would just like to engineer it to behave the way. Furthermore, we must use real physical phenomena to implement digital designs.  
<br/>

### [](#header-3) Using voltages "Digitally"  
1. first attempt (X)  
<img src="/images/pic7.PNG">  
This approach is erroneous in case x satisfies the condition, $$ x \in (V_{TH} - \epsilon, V_{TH} + \epsilon). $$

2. second attempt (O?)  
<img src="/images/pic8.PNG">  
Forbidden to ask if V follows, $$ V_{L} < V < V_{H}. $$
<br/>

### [](#header-3) A digital processing element  
A combination device is a circuit element that has  
- one or more digital inputs  
- one or more digital outputs  
- a functional specification that details the value of each output for every possible combination of valid input values  
- a timing specification consisting of an upper bound on the required time for the device to compute the specified output values from an arbitrary set of stable, valid input values... which is about 20 pico seconds  
<br/>

**_will this system work?_**  
<img src="/images/pic9.PNG">  

There might be a noise introduced while the system runs. To solve this problem, different specification for input and output is required.  

<img src="/images/pic10.PNG">  

Noise margins are the key to reliable operation.
<br/>

### [](#header-3) A buffer  
A simple combinational device must have
- GAIN > 1  
- NONLINEAR  

gain: measure of the amplification of the device <br/>

$$GAIN = \frac{\partial V_{out}}{\partial V_{in}}.$$

This means that there is a positive noise margins.
Center region here is narrower than it is high and to get a curve to go through that the slope has to be bigger than 1 someplace.

그러니까 한마디로 기울기가 1보다 크다. 식으로 쓰면, $$V_{OH} - V_{OL} > V_{IH} - V_{IL}.$$

<br/>




### [](#header-2)References

- *[Computer Architecture MIT 6.004 2015 by Chris Terman][ref1]*   

[ref1]:https://www.youtube.com/watch?v=gofiPbZHpss&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=2
