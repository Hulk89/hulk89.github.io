---
layout: post
title:  "ch 4. Bayes' rule"
date:   2017-10-30 
category: "Doing Bayesian Data Analysis"
tags: [ml, bayesian inference]
---
# 4.1. Bayes' rule

$$p(y \vert  x) = \frac{p(x\vert y)p(y)}{\sum_y p(x\vert y)p(y)}$$

* $p(y \vert  x)$ : posterior
  * x를 염두에 둔 belief의 정도
* $p(x\vert y)$: likelihood
  * parameter가 y일 때 x가 나올 확률
* $p(y)$: pior
  * x를 염두에 두지 않았을 때 parameter의 믿음의 정도
* $\sum_y p(x\vert y)p(y)$: evidence
  * model에 따른 data의 확률 (parameter와 관계 없다.)

# 4.2.1 Data order invariance
* D가 observe됨 -> $P(\theta \vert  D)$ -> D'이 observe됨 -> $p(\theta \vert  D, D')$
* 위와 같은 상황에서, D, D'이 observe되는 순서에 따라 $p(\theta \vert  D, D')$ 확률이 바뀌는가?
  * model의 likelihood, $p(D \vert  \theta)$에 따라 달려있다.
  * 보통 invariant함 (i.i.d. : independent and identically distributed)


> model이 복잡하면 prior의 확률들이 낮아지고, posterior의 확률들도 낮아짐.

# 4.3.4 Why Bayesian inference can be difficult
1. marginalize하는 term은 결국 적분이 들어가는데 이게 힘들다.
  * MCMC로 sampling을 해본다.
2. prior를 정하기가 힘듬
  * 이거는 그래도 대충 몇개 해보거나 섞어 쓰면 됨
  

# 4.4. Excercise
정리한거 날아감... ㅠㅜ