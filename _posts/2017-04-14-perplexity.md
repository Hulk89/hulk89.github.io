---
layout: post
title:  "perplexity??"
date:   2017-04-14
category: "neural machine translation"
tags: [ml, nmt, entropy]
---

## self information

RV에서 샘플링한 정보를 주고받을 때, 확률이 높은 녀석은 비트를 더 작게 보내면 전체적으로 평균 데이터 양이 적어지겠지... 그래서 확률에 따른 비트 수를 모델링한 것.

$$I(x_i) = -log_2(P(x_i))$$

## entropy

위에 나온 것을 토대로 어떤 Random Variable에서 샘플링된 정보를 주고 받을 때 평균 필요한 비트 수를 entropy라고 볼 수 있다.

$$H(X)=\sum_{i=1}^{n}P(x_i)I(x_i)=-\sum_{i=1}^{n}P(x_i)log_2P(x_i)$$

## cross-entropy

distribution $P$를 모델링한 $Q$가 있다고 가정해보자. $Q$의 distribution을 보고 평균 데이터 양이 적어지도록 만든 encoding은 $Q$의 self-information이다. 그런데 이를 실제 dist. $P$에 대해서 평균을 구해본 것이 `cross-entropy`이다. 즉 이 값이 클수록, $Q$는 $P$에 대한 좋은 모델링이 아니다라고 볼 수 있다.

$$H(P, Q) = -\sum_{i=1}^{n}P(x_i)log_2Q(x_i)$$

## unknown pdf p에 대한 model q의 perplexity

모르는 어떤 distributiuon p를 모델링한 dist q가 존재할 때, q의 perplexity는

$$b^{-\frac{1}{N}\sum_i^Nlog_bq(x_i)}$$

로 정의된다. 
여기서 $\hat{p}(x)$를 $x_i$의 빈도수라 생각하면, $b$의 지수는 cross-entropy로 볼 수 있다. 그렇다면 q가 잘 모델링 안되었을 때, perplexity가 커지겠다.