---
layout: post
title: "Thoughts on Gates of Long Short Term Memory"
tagline: "For the deep understanding of types of gates in long short term memory"
categories: [DL]
image: /thumbnail-mobile.png
author: "Hyung-Kwon Ko"
meta: "Seoul"
---

## [](#header-2)Introduction
There are four main gates which work differently to figure out when it would be the best case for the output state. Even though there are tons of papers explaining the mathematical process of long short term memory(LSTM) gates, I wasn't able to find clear description of the sensible meaning on each cell. For example, I wanted to know the reason why there are four gates instead of three or what is the differences of working behavior between input gate and forget gate yet they use the same sigmoid function with the same form of matrix multiplication. I have pondered on this matter for many days with my teammates and write down here not to forget what I have researched on. FYI, I will follow the same notations of variables that C. Olah have used in his blog.


## [](#header-2)Review
Before we start we have to know what is the mathematical interpretation of each cell's working logic. I mean how the forward and backward propagation work to update the weights? But I will skip this part as I have spent many hours to grasp it and there are already many good stuffs that we can follow step by step. For those who have not fully understood the meaning of this part, I recommend you to go through below links before you go further.

[Understanding LSTM by C. Olah](http://colah.github.io/posts/2015-08-Understanding-LSTMs/)   
[LSTM backpropagation with an easy example by A. Gomez](https://medium.com/@aidangomez/let-s-do-this-f9b699de31d9)   
[Explanations on LSTM's weights by A. Mallya](http://arunmallya.github.io/writeups/nn/lstm/index.html#/2)   
[LSTM BPTT code implementation by A. Kristiadi](https://wiseodd.github.io/techblog/2016/08/12/lstm-backprop/)   

After you finish these materials, then I may say that now we are good to go.


## [](#header-2)Two main streams
The distinct feature of LSTM compared to the Vanilla RNN is that there are two main streams of memory which are called the Short Term Memory(STM) and Long Term Memory(LTM). As the names suggest, STM is focusing on remembering the recent memories while LSTM is holding the whole set of past memories. For example, I am currently working on writing novels with artificial intelligence. In this specific work, the STM indicates one n-dimensional vector that is the output of the previous step which is strongly connected to the information of it. Simply speaking, it is the previous sentence. LTM, on the other hand, is containing the overall information of the input data which can be the whole novel itself in this example.


## [](#header-2)Factors and vectors
As we know there are four gates harmoniously working inside of LSTM, and each gate must use sigmoid or hyperbolic tangent function before it escapes to become a vector. But there should be a reason why different activation functions are implemented. I name the reason 'factors and vectors'. Let's see one by one, starting with forget gate.  

### [](#header-3)Forget gate  

$$ f_t = \sigma(W_f \cdot [h_{t-1} , x_t] + b_f) $$  

The reason why activation function is used is simple, to make the network nonlinear so the training session with multiple layers can work properly. The last LTM enters  this is somewhat different from what we usually think as a proper idea.  

vector to be

### [](#header-3)Input gate  

$$i_t = \sigma(W_i \cdot [h_{t-1} , x_t] + b_i) $$  

$${\tilde{C_t}} = tanh(W_c \cdot [h_{t-1}, x_t] + b_C) $$  

### [](#header-3)Update gate  

$$C_t = f_t \ast C_{t-1} + i_t \ast {\tilde{C_t}} $$  

### [](#header-3)Output gate  

$$o_t = \sigma (W_o \cdot [h_{t-1} , x_t] + b_o) $$  

$$h_t = o_t \ast tanh(C_t)$$  


will be continued...
sigmoid -> factor
hyperbolic tangent function -> vector(meaning)


## [](#header-2)References
- *[Understanding LSTM by C. Olah][ref1]*   
- *[LSTM backpropagation with an easy example by A. Gomez][ref2]*    
- *[Explanations on LSTM's weights by A. Mallya][ref3]*   
- *[LSTM BPTT code implementation by A. Kristiadi][ref4]*   
- *[Explanations on each cell of LSTM by Jinsol Kim (Korean)][ref5]*   
- *[Long short term memory by Eugine Kang][ref6]*

[ref1]:http://colah.github.io/posts/2015-08-Understanding-LSTMs/
[ref2]:https://medium.com/@aidangomez/let-s-do-this-f9b699de31d9
[ref3]:http://arunmallya.github.io/writeups/nn/lstm/index.html#/2
[ref4]:https://wiseodd.github.io/techblog/2016/08/12/lstm-backprop/
[ref5]:http://blog.naver.com/PostView.nhn?blogId=infoefficien&logNo=221214491338&parentCategoryNo=&categoryNo=617&viewDate=&isShowPopularPosts=true&from=search
[ref6]: https://medium.com/@kangeugine/long-short-term-memory-lstm-concept-cb3283934359
