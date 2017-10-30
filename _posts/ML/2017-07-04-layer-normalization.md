---
layout: post
title:  "Layer Normalization"
date:   2017-07-04
category: "machine learning"
tags: [ml, normalization]
---
# Layer Normalization

## Introduction

* Batch Normalization
  * input statistics의 running average를 필요로함
  * RNN의 경우 time-step마다 다른 statistics를 적용해야함
  * distributed training에서는 minibatch가 너무 작아서 적용이 힘듬

## Background
* $a_i^l$: l번째 layer의 i번째 hidden unit
* $w_i^l$: parameter
* $b_i^l$: bias parameter

라고 하면

다음과 같이 수식화할 수 있다.
$$a_i^l = w_l^l h^l$$

$$h_i^{l+1}=f(a_i^l+b_i^l)$$

> 문제점: 한 layer의 weight들에 대한 gradient는 전 layer의 output에 높게 관여된다. `internal covariate shift`: 그런데 전 layer의 output이 계속 distribution이 바뀌니까 트레이닝이 느리다.    [BN 설명 블로그](http://sanghyukchun.github.io/88/)

그래서 Batch-normalization에서는 위에서 말한 internal covariate shift를 줄이기 위해 
* batch에 대해서 각 dimension마다 mean, variance를 구하여 normalization하고 (각 dim은 independent하다고 가정)
* linear transform을한다. (실제 independent하진 않을테니..)
* 그리고 linear transform도 같이 트레이닝

$$\bar{a}_i^l=\frac{g_i^l}{\sigma_i^l}(a_i^l-\mu_i^l)$$

근데 위에서 $\sigma$와 $\mu$는 batch의 distribution으로 근사시켜서 구하는데, 이는 실제 distribution과 다르다. 그래서 작은 batch보다는 큰 batch가 좋다.

## Layer Normalization

* hidden unit들에 대해서 mean과 variance를 구함
  * $$\mu^l = \frac{1}{H}\sum_{i=1}^H a_i^l$$
  * $$\sigma^l = \sqrt{\frac{1}{H}\sum_{i=1}^H (a_i^l-\mu^l)^2}$$
* batch size에 관계없으므로
  * training, test시에도 따로 구할 것이 없음
  * RNN에도 좋음