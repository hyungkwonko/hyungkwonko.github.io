## CRM project

### How to deal with the class imbalance issue?

####1. Performance Metric을 조정한다.
Accuracy is **not** the metric to use when working with an imbalanced dataset. instead you can use something like recall, precision, F1 score and so on.
<br/>

####2. Try Resampling Your Dataset (Not recommended)
- You can add copies of instances from the under-represented class, called **over-sampling**  
- You can delete instances from the over-represented class, called **under-sampling**.  

2개 다 좋은 성과를 낼 수 있다는 보장이 없고 문제점이 있다. 오버샘플링의 경우 오버피팅이 발생하며 정작 accuracy는 변화가 없을 수 있으며 언더샘플링은 보유 데이터를 너무 많이 버리는 꼴이 됨. 예를 들어 4만개 데이터 중에서 4%이면 1600개(1)이고 1600개(0)를 뽑는다고 하면 40000 - 3200 = 36800개는 버리는 꼴이 된다.
<br/>

####3.Try Generate Synthetic Samples
There are systematic algorithms that you can use to generate synthetic samples. The most popular of such algorithms is called SMOTE or the Synthetic Minority Over-sampling Technique.

장점: easy to implement on both R & Python
- In Python, take a look at the “UnbalancedDataset” module. It provides a number of implementations of SMOTE as well as various other resampling techniques that you could try.
- In R, the DMwR package provides an implementation of SMOTE.
<br/>

####4.Try a Different Perspective
_Anomaly detection_
Anomaly detection is the detection of rare events. This might be a machine malfunction indicated through its vibrations or a malicious activity by a program indicated by it’s sequence of system calls. The events are rare and when compared to normal operation. This shift in thinking considers the minor class as the outliers class which might help you think of new ways to separate and classify samples.  

<div style="width:image width px; font-size:80%; text-align:center;"><img src="/images/pic0.png" alt="alternate text" width="width" height="height" style="padding-bottom:0.5em;" /><br/>Devin Soni, Visualization of clustering method for anomaly detection, <a href="https://towardsdatascience.com/dealing-with-imbalanced-classes-in-machine-learning-d43d6fa19d2">source</a> </div>

_Change detection_
Change detection is similar to anomaly detection except rather than looking for an anomaly it is looking for a change or difference. This might be a change in behavior of a user as observed by usage patterns or bank transactions.  
<br/><br/>


### Thoughts on customer segmentation
_is it mendatory?_ YES
1. Purchasing power - 구매력
A person’s level of income doesn’t always say so much about his or her buying power. A person with an annual salary of €36,000 who recently bought a house is likely to have less money in his wallet after the bills have been paid than a person with the same salary who has lived in the same house for ten years. Using data you can deduct whether the entire household’s buying power is high or low.



2. Persona - 개인의 성격 - 소비 패턴 등
Based on the amount of data, Bisnode has divided the population of Belgium into different personality types, so-called personas. Where, for example, the "Hedonist" is a cool person who at the age of 66 would like to fly a year in the Caribbean, saving money to the next generation. All individuals have different preferences and values, which affects both what they buy and how you as companies should communicate with them. It is simply about getting to know the customer at the individual level so that you can personalize your offer.



3. Life Phase - 개인의 생활상 - 결혼 직전 등
What phase of life is your customer in? These days, a 55-year-old man can be a new dad, or a grandfather. The customer’s phase of life often sets the framework for what the customer is interested in as well as how you can best get their attention.



4. History - 구매 이력
By combining large amounts of data with historical consumption patterns, you can calculate what each customer will likely buy next. Today it is possible to say that “with 70 percent probability, Eric will move house within one year, and he will therefore need a mortgage as well as home insurance.”



5. Consumption cycle - 소비 사이클 - 핸드폰의 평균 수명 2년, 마지막 핸드폰 구매가 2년 전
People's lives follow a consumption cycle. In some periods you consume more and in others less. By analyzing the customer's personality together with its life-level and purchasing power, it is possible to predict how much different individuals will consume in the future. It can provide an important indication of the total customer base value for the years to come, as well as helping companies identify those customers that will be particularly valuable in the future.

<br/><br/>


### Questions
- 어떤 rule을 추가할 것인가?  
- 하나의 모델을 제작 $\to$ 제품을 하나의 변수로 사용
- 여러개의 모델을 제작 $\to$ 제품 별로 다른 모델을 선택? / parameter tuning만?
-


<br/><br/>


## References
[8 Tactics to Combat Imbalanced Classes in Your Machine Learning Dataset, Jason Brownlee][ref1]  
[SMOTE: Synthetic Minority Over-sampling Technique, N. Chawla et al.][ref2]  
[Dealing with Imbalanced Classes in Machine Learning, Devin Soni][ref3]  
[A New Model to Predict What Your Customer May Do or Buy Next, Marcos][ref4]  
[5 ways to predict your customer’s next buy, Sara von Schoultz][ref5]  
[Anomaly detection][ref5]  


[ref1]: https://machinelearningmastery.com/tactics-to-combat-imbalanced-classes-in-your-machine-learning-dataset/
[ref2]: https://arxiv.org/pdf/1106.1813.pdf
[ref3]: https://towardsdatascience.com/dealing-with-imbalanced-classes-in-machine-learning-d43d6fa19d2
[ref4]: https://hapibot.com/journal/using-sequential-patterns-to-predict-what-your-customer-will-do-or-buy-next/
[ref5]: https://www.bisnode.be/knowledge/our-thoughts/5-ways-to-predict-next-buy/
[ref6]: http://intothedata.com/02.scholar_category/anomaly_detection/
