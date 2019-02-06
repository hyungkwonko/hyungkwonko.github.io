---
layout: post
title: CA Lecture Note 4 - Synthesis of combinational logic
date: 2019-01-14 17:00:00
categories:
- Computer architecture
tags:
- combinational logic
author: "Hyung-Kwon Ko"
meta: "Seoul"
use_math: true
---

### [](#header-3) Functional specifications  
There are many ways of specifying the function of a combinational device.
- truth tables are a concise description of the combinational system's function  
- Boolean expressions form an algebra whose operations are AND, OR and inverse.  
Any combinational function can be specified as a truth table or an equivalent **sum-of-products** Boolean expression.  
<br/><br/>


### [](#header-3) Sum of products building blocks  
<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic18.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Varun Bhandari
, Sum of products building blocks, <a href="https://www.youtube.com/watch?v=jJQ6OFWfz20&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=4">source</a> </div>
<br/>

- truth table  
*should be added...*
<br/>

- boolean algebra:  
$$ Y = \bar{C}\bar{B}A + \bar{C}BA + CB\bar{A} + CBA $$
<br/>

- Circuit schematic  
<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic19.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Varun Bhandari
, Circuit schematic, <a href="https://www.youtube.com/watch?v=jJQ6OFWfz20&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=4">source</a> </div>

<br/><br/>

### [](#header-3) ANDs and ORs with > 2 inputs  
<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic20.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Varun Bhandari
, Which one should I use?, <a href="https://www.youtube.com/watch?v=jJQ6OFWfz20&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=4">source</a> </div>
<br/>

- if all the inputs arrive at the same time, tree is the best approach, and usually it is true but  
- when the inputs arrive differently, then chain is better  
- think about what might happen if D comes late  
- 따라서 항상 트리가 빠르다.. 이게 아니라 어떤 상황에서 어떤게 빠른지를 보고 생각하고 design하는게 중요하다.  
<br/><br/>

### [](#header-3) More building blocks  

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic21.PNG" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Varun Bhandari
, NAND, NOR, XOR, <a href="https://www.youtube.com/watch?v=jJQ6OFWfz20&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7&index=4">source</a> </div>
<br/>

**_NANDs and NORs are univaersal_**  
any logic function can be implemented using only NANDs(or NORs).
<br/><br/>


### [](#header-3) Logic simplification  
**_Boolean Algebra_**
- OR rules: a + 1 = 1, a + 0 = 0, a + a = a  
- AND rules: a1 = a, a0 = 0, aa = a  
- Commutative: a + b = b + a, ab = ba  
- Associative: (a + b) + c = a + (b + c), (ab)c = a(bc)  
- Distributive: a(b + c) = ab + ac, a + bc = (a + b)(a + c)
- Complements: a + $$ \bar{a} $$ = 1, a $$ \bar{a} $$ = 0  
- Absorption: $$ a + ab = a $$, $$ a + \bar{a}b = a + b $$, $$ a(a + b) = a $$, $$ a(\bar{a} + b) = ab $$
- Reduction: ...  
- DeMorgan's Law: ...  
<br/>

**_Boolean Minimization_**
we can simplify the formula with above logic, and it always produces the same logic. we can double check with the truth table. If two outputs have the same truth table, then they are equivalent.  
<br/>









### [](#header-2)References

- *[Computer Architecture MIT 6.004 2015 by Chris Terman][ref1]*   

[ref1]:https://www.youtube.com/watch?v=SkbBM6To9F0&index=3&list=PLWokBk9W7kzGqZYZz6BiaqtsrHQK_22u7
