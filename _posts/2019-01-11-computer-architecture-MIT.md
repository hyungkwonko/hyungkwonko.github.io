---
layout: post
title: "CA Lecture Note 3 - CMOS technology"
tagline: "3rd lecture of Spring 2015 at MIT"
categories: [CA]
image: /thumbnail-mobile.png
author: "Hyung-Kwon Ko"
meta: "Seoul"
---

### [](#header-3) Today's lecture
1. Qualitative MOSFET model  
2. CMOS logic gates  
3. CMOS design issues  
<br/>

### [](#header-3) Combinational device wish list  
- Design our system to tolerate some amount of error  
  :: Add positive noise margins  
  :: VTC: gain > 1 & Nonlinearity  
- Lots of gain(as slope gets sleeper) will lead to big noise big noise margin  
- Cheap, small  
- Changing voltage will require us to dissipate power, but if no voltages are changing, we'd like zero power dissipation  
- Want to build devices with useful functionality  
<br/>

__*Then what is the ideal?*__
it would be something like a graph with an infinite gain in the middle. (중간에 그래프의 기울기가 무한대로 치솟고 양쪽 pink region이 maximize되는 방향이면 되겠다.)  


### [](#header-3) MOSFETS: Gain & Nonlinearity
MOSFETs: metal-oxide-semiconductior field-effect translators (components: gate, source, drain, bulk)  
<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic11.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>MOSFETS, <a href="https://www.youtube.com/watch?v=SkbBM6To9F0&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=3">source</a> </div>

$$ I_{DS} \propto W/L. $$

L should be small to maximize the total $$I_{DS}$$, and it had been shrinking with the Moore's law perfectly fitted up until a few years ago.

p-type: majority carries over holes. Voltage control switches turn on when the gate voltage is low.  
n-type: majority carries over electrons. Voltage control switches turn on when the voltage is high.  

So they are voltage control switches...
<br/>

__*how this works?*__
<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic14.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>FETs come in two flavors, <a href="https://www.youtube.com/watch?v=SkbBM6To9F0&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=3">source</a> </div>

*pull up & pull down*  
PFET(pull up): to charge up the output node to make the output voltage high  
NFET(pull down): to discharger the output node to make the output voltage low  

if they are both on at the same time, that would be a real disaster, they should be designed to be complementary.  
<br/>

### [](#header-3) Why do we love CMOS so much?
1. high gain VTC - get great(large) noise margins  
2. $$V_{OL}$$ ~ 0V, $$V_{OH}$$ ~ $$V_{dd}$$ - no static power dissipation  

these two reasons are too strong that CMOS has been king for so long.  
<br/>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic12.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>NAND gate, <a href="https://www.youtube.com/watch?v=SkbBM6To9F0&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=3">source</a> </div>

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic13.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>NOR gate, <a href="https://www.youtube.com/watch?v=SkbBM6To9F0&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=3">source</a> </div>
<br/>

_how about the AND gate?_
CMOS + invert  
<br/>

### [](#header-3) Big issues
1. how long does it take to send an electrical signal?  
  :: propagation delay- valid input to valid output  
  :: contamination delay - invalid input to invalid output  
2. will be continued on next lecture...
<br/>

### [](#header-2)References

- *[Computer Architecture MIT 6.004 2015 by Chris Terman][ref1]*   

[ref1]:https://www.youtube.com/watch?v=SkbBM6To9F0&index=3&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7
