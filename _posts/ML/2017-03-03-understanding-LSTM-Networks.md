---
layout: post
title:  "understanding LSTM Networks"
date:   2017-03-03
category: "neural machine translation"
tags: [RNN, LSTM, nn]
---

[원문](http://colah.github.io/posts/2015-08-Understanding-LSTMs/)

## RNN의 Long-term dependencies 문제
RNN의 식을 간단히 살펴보자

$$h_t = \tanh ( W_{hh} h_{t-1} + W_{xh} x_t ) \\y_t = W_{hy} \cdot h_t$$

hidden state를 거치는데, $h_{t}$와 $x_{t}$에 $\tanh$를 한 녀석이 들어가기 때문에, 한참 뒤의 output에 반영이 되기에 어렵다. 물론 이론상으로는 long-term dependency를 잘 핸들하도록 파라미터 튜닝을 잘 하면 된다고 하지만 그래도 어렵다.

![RNN-longtermdependencies.png](/resources/6B911B2E1CB16CF47BF0F86257912C8E.png)

## LSTM Networks
이를 해결하기 위해 LSTM이라는 RNN의 변형이 나오게 된다. 기존의 RNN과 비교를 해보자.
![LSTM3-SimpleRNN.png](/resources/BCE0D99E8BC969FA3C2FFA99F93935C5.png)
![LSTM3-chain.png](/resources/2795BC16B012322F7767CD4D940BA2E3.png)

간단히 그림으로만 봐도, 윗쪽 라인은 non-linear한 연산이 없이 쭉 유지된다. 그렇기에 long term을 잘 유지할 수 있어보인다.

윗쪽 라인은 **Cell state**, 아랫쪽 라인은 **output state**이다.

### Cell state
#### 1번째 세로줄
처음 라인을 보면 Sigmoid연산을 하는데, 이는 0~1 사이의 값을 준다. 거기에 곱셈 연산이니까, 어느정도의 양을 계속 가져갈 것인지 정하는 구간이다.
$$f_t = \sigma ( W_f \cdot [h_{t-1}, x_t] + b_f)$$

#### 2,3번째 세로줄
여기서는 어떤 새로운 정보를 저장할 것인지 정한다.

input gate layer($i_t$) : 값을 update할 것인지 정한다.
$$i_t = \sigma(W_i \cdot [h_{t-1}, x_t] + b_i)$$
tanh layer($\tilde{C_t}$) : 새로운 후보 값을 만든다. 
$$\tilde{C_t} = \tanh(W_C \cdot [h_{t-1}, x_t] + b_C)$$

#### 최종
위의 두개를 합쳐서 Cell state가 업데이트된다. 
$$C_t = f_t * C_{t-1} + i_t * \tilde{C_t}$$

### output state

이제 이를 내보낸다. 식만 써놓자..

$$o_t = \sigma(W_o \cdot [h_{t-1}, x_t] + b_o) \\ 
h_t = o_t * \tanh(C_t)$$