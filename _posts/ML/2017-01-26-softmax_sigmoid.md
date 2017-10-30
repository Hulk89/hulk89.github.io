---
layout: post
title:  "sigmoid와 softmax의 차이"
date:   2017-10-30 
category: "machine learning"
tags: [ml, activation function]
---

# sigmoid
NN의 activation function으로 sigmoid를 많이 썼었지...
처음엔 actiovation function이 step function이었는데 이는 output이 0, 1 밖에 되지 않는 단점이 있었다. 
sigmoid는 $\frac{1}{1+e^{-x}}$ 꼴이라서 0~1사이의 값을 표현할 수 있었다.

# softmax

$$ \sigma (z_j) = \frac{e^{z_j}}{\sum_{k=1}^{K} e^{z_k}} $$

위 식은 $K$차원의 벡터 $z$를 $(0,\ 1)$사이의 범위를 갖도록 치환한다고 볼 수 있다.

# 비교

* sigmoid를 사용하면 해당 뉴런의 입력과 weight, bias로 출력이 결정된다.
* softmax를 사용하면 전체 뉴런들의 출력값과 비교를 해서 출력이 결정된다.(non-locality)

# + softmax cost function

cost function은 위의 softmax함수를 가지고 실제 distribution과 cross-entropy를 구하면, 다음과 같다.
$$ L = -\sum_j p(x_j) \cdot log(\sigma(z_j)) = -log(\sigma(z_{y_i})) = -log(\frac{e^{z_{y_i}}}{\sum_{k=1}^{K} e^{z_k}})$$