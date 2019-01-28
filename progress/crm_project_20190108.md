## CRM project

### 최종 목표
매출을 극대화하는 것을 목표로 한다. How can we maximize the sales using CRM data?
<br/>

### Up-selling vs. Cross-selling
크로스 셀링( Cross-Selling )은 기업의 제품과 서비스 중 하나를 구매한 고객에게, 그 기업이 제공하는 다른 제품이나 서비스를 추가적으로 판매하는 전략을 말한다.
- 롤러 스케이트를 사는 사람에게 보호대나 헬멧을 제안하는 것
- DSLR을 사는 사람에게 삼각대 등의 구매를 제안하는 것
- 와이셔츠를 사는 사람에게 어울리는 타이 등을 제안하는 것

업셀링( Up-Selling )은 상향판매, 즉. 특정한 상품 범주 내에서 고객의 상품 구매액을 늘리기 위해 고객으로 하여금 보다 업그레이드된 상품을 구매하도록 유도하는 판매활동의 하나이다.

- USB 구매고객에게 대용량의 메모리를 권유하는 것
- 저가 핸드폰을 구입하려는 사람에게 고성능의 핸드폰을 제안하는 것
- 자동차 구매 고객에게 고객의 생각보다 한단계 상향된 자동차 등을 권유하는 것

고객과 기업의 관계를 오랫동안 지속하다 보면 친밀감과 신뢰가 쌓이게 되고 이렇게 쌓인 신뢰는 고객 가치가 되어 기업에게 견고한 사업기반과 성장 잠재력을 마련해 주게 되는데 이는 곧 기업에게 돌아오는 다양한 형태의 비용절감과 이익증가 등의 효용과 이익을 말한다.
<br/><br/>


### Recall vs. Precision
Precision(정밀성): Positive로 예측한 것 중, 실제 Positive의 비율을 뜻한다.
$$ PRECISION = \frac{TP}{TP+FP} $$

Sensitivity(Recall or True Positive Rate): 실제 Positive 데이터 중 Positive로 올바르게 분류된 비율을 이야기 한다. 예를 들어 원본 데이터에 암 양성이 100개 있었는데, 모델에 있어서 90개가 분류되었으면, Sensitive = 0.9 가된다.
$$ RECALL = \frac{TP}{TP + FN} $$


#### What does it mean in our case?
Precision이 높다 $\to$ FP가 낮다 $\to$ TP가 높다 $\to$ **구매할 고객을 잘 찾아내는데 focus**
- 10명한테 보내서 10명 다사면 GOOD (쓸데없는 문자 발송으로 인한 비용 소비 걱정 Zero, 고객 이탈 위험 걱정 Zero)

Recall이 높다 $\to$ FN이 낮다 $\to$ TN이 높다 $\to$ **구매하지 않은 고객을 잘 찾아내는 것도 focus** $\to$ 굳이...
- 10명한테 보내서 7명이 살 것이라고 예측하고 3명이 안 살 것이라고 예측한다고 해도... 굳이?(물론 의미가 없지는 않다)

--------

We have thousands of free customers registering in our website every week. The call center team wants to call them all, but it is imposible, so they ask me to select those with good chances to be a buyer (with high temperature is how we refer to them). We don't care to call a guy that is not going to buy (so precision is not important) but for us is very important that all of them with high temperature are always in my selection, so they don't go without buying. That means that my model needs to have a high recall, no matter if the precision goes to hell.

--------

Which is more important simply depends on what the costs of each error is.

Precision tends to involve direct costs; the more false positives you have, the more cost per true positive you have. If your costs are low, then precision doesn't matter as much. For instance, if you have 1M email addresses, and it will cost $10 to send an email to all of them, it's probably not worth your time to try to identify the people most likely to respond, rather just spamming all of them.

Recall, on the other had, tends to involve opportunity costs; you are giving up opportunities every time you have a false negative. So recall is least important when the marginal value of additional correct identification is small, e.g. there are multiple opportunities, there is little different between them, and only a limited number can be pursued. For instance, suppose you want to buy an apple. There are 100 apples at the store, and 10 of them are bad. If you have a method of distinguishing bad apples that misses 80% of good ones, then you will identify about 18 good apples. Normally, a recall of 20% would be terrible, but if you only want 5 apples, then missing those other 72 apples doesn't really matter.

So recall is most important when:

-The number of opportunities is small (if there were only 10 good apples, then you would be unlikely to find 5 good ones with a recall rate of only 20%)
-There are significant differences between opportunities (if some apples are better than others, then a recall rate of 20% is enough to get 5 good apples, but they aren't necessarily going to be the best apples)
OR
-The marginal benefit of opportunities remains high, even for a large number of opportunities. For instance, while most shoppers won't have much benefit from more than 18 good apples, the store would like to have more than 18 apples to sell.

Thus, precision will be more important than recall when the cost of acting is high, but the cost of not acting is low. Note that this is the costs of acting/not acting per candidate, not "cost of having any action at all" versus "cost of not having any action at all". In the apple example, it's the cost of buying/not buying a particular apple, not the cost of buying some apples versus the cost of not buying any apples; the cost of not buying a particular apple is low because there are lots of other apples. Since the cost of buying a bad apple is high, but the cost of passing up a particular good apple is low, precision is more important in that example. Another examples would be hiring when there's a lot of similar candidates.

Recall is more important than precision when the cost of acting is low, but the opportunity cost of passing up on a candidate is high. There's the spam example I gave earlier (the cost of missing out on an email address isn't high, but the cost of sending out an email to someone who doesn't respond is even lower), and another example would be identifying candidates for the flu shot: give the flu shot to someone who doesn't need it, and it costs a few dollars, don't give it to someone who does need it, and they could die. Because of this, health care plans will generally offer the flu shot to everyone, disregarding precision entirely.
<br/><br/>


#### Use domain knowledge merging dataset
1. 카드 결제시(멤버십 등으로?) 등급 조회가 가능하다.  
2. 카드 rental $\to$ 신용 등급을 필요로 하고 조회가 가능하다.(소득 구간 등)  
<br/><br/>


#### as of now...
현재 데이터를 받아오는 중 & merge?
<br/><br/>


#### what is the next step?
- choose model that can predict with high precision  
- how to deal with the class imbalance(number of 1s << number of 0s)  
- 파생변수 생성 (군집 numbering...)  
- feature selection  
- 협업 필터링  
- 그래프 이론 / 네트워크 분석  
<br/><br/>


## References
[When is precision more important over recall? - stackoverflow][ref1]  

[ref1]: https://datascience.stackexchange.com/questions/30881/when-is-precision-more-important-over-recall
