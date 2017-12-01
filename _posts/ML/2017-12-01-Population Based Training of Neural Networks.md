---
layout: post
title:  "Population Based Training of Neural Networks 정리"
date:   2017-12-01
category: "machine learning"
tags: [ml, optimization]
---

* Sequential optimization
  * 종류
    * hand tuning
    * bayesian optimization
  * 장점 : best solution을 제공한다. 이전 노드의 정보를 fully utilize
  * 단점 : parallelize하기 힘들고, sequential하게 여러번 training을 해야한다.
* Parallel optimization
  * 종류
    * grid search
    * random search
  * 장점 : parallelize
  * 단점 : 같이 돌고있는 다른 노드들의 정보를 쓰지 않는다.

요 두가지의 장점을 가져오기 위해 Genetic algorithm을 들고옴

![1g](/resources/68E6E8AD29B2E3EF31A2702C19B1EAF8.png)

* population을 만들어서
  * 어느 정도 학습을 하고
  * evaluation한 후
  * 잘 안된 녀석은 
    * 잘 된 녀석으로 hparams, params를 엎어친다!(exploit)
    * 그리고 hparams를 새로 만드는데 (explore)
      * 기존꺼에서 살짝 perturbing 
      * prior distribution에서 hyperparameter를 resampling

![2.png](/resources/CBF15176B44240CAA1B33C2F3FA08293.png)


toy example에서 explore는 살짝 도움만 주고 사실상 exploit이 훨씬 더 중요했다고 한다.

실험에서는 algorithm 설명과는 달리 explore시에 hyperparameter만 바꾼다.

대체로 explore시 perturbation을 0.8~1.2로 주며, GAN의 경우 0.5~2.0으로 많이 크게 주었다.

간단한 실험중인데 잘되는 것 같다.