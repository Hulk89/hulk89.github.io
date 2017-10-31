---
layout: post
title:  "Importance sampling 예제 구현"
date:   2017-05-10
category: "neural machine translation"
tags: [ml, nmt, sampling]
---
# Importance sampling 예제

$N(0, 1)$에서 $[2, 3]$부분만 truncate한 pdf를 만들고 그것의 평균값을 simulation하고 싶다고 가정하자. 

즉 구하고 싶은 것은 $$\int_2^3 x\frac{\phi(x)}{\int_2^3\phi(y)dy}dx$$
> $N(0, 1)$의 pdf를 $\phi$로 쓴다.

## naive

* (1)$N(0, 1)$에서 무작정 draw하고 
* (2) $\frac{\sum_i x_i \cdot \mathbb{1}(x_i \in [2, 3])}{\sum_i \mathbb{1}(x_i \in [2, 3])}$
를 구한다.

$[2, 3]$구간에서 draw될 확률이 적기 때문에, 대부분의 sample을 버려야한다. (TODO: N(0,1), 그림 넣기)


```python
import numpy as np

num_samples = 30000
range_ = (4, 5)

samples = np.random.normal(0, 1, num_samples)

# 쓸만한 sample index를 가져오자.
idxl = samples >= range_[0]
idxr = samples <= range_[1]
idx = idxl & idxr

samples_in_range = samples[idx]
print("{}개 중 쓸 수 있는 sample 갯수: {}개".format(num_samples, len(samples_in_range)))

result = np.sum(samples_in_range)/len(samples_in_range)
print("결과 : {}".format(result))

```

    30000개 중 쓸 수 있는 sample 갯수: 2개
    결과 : 4.350475573377283


## 제대로 구해보기
~~실제 결과와 많이 다를 것 같다.. 해보자!~~ 그렇게 많이 다르진 않구만...


```python
from scipy.integrate import romberg


def integrand_norm(x, s):
    return np.exp(-0.5*(x/s)**2)/(np.sqrt(2*np.pi)*s)

def integrand_normx(x, s):
    return x*np.exp(-0.5*(x/s)**2)/(np.sqrt(2*np.pi)*s)

den = romberg(integrand_norm, range_[0], range_[1], args=(1.0, ))
num = romberg(integrand_normx, range_[0], range_[1], args=(1.0, ))
print(num/den)

```

    4.2168307809


## Importance sampling
이제 importance sampling으로 구해보자!
  * 식을 다음처럼 바꾼다. $$\int_2^3 x\frac{\phi(x)}{\int_2^3\phi(y)dy}dx = \int_2^3 x\frac{\phi(x)}{\int_2^3\phi(y)dy\cdot h(x)} \cdot h(x)\ dx\ \ \ \ \ \ \  (2)$$ 
  $$h(x)=\left\{\begin{matrix} 1& 2 < x < 3\\ 0& otherwise \end{matrix}\right.$$
    > $h(t)$는 $U[2,3]$의 pdf이며, 따라서 식 (2)의 중간에 있는 분수꼴이 importance weight가 된다.
  
  * $U[2, 3]$에서 draw한다. importance weight는 $$w_i=f(w_i)=\frac{\phi(x_i)}{\int_2^3 \phi(y)dy}$$ 이며, simulated mean은 $$\frac{1}{N}\sum_{i=1}^N x_i \cdot w_i$$
이며 **버려지는 sample은 하나도 없다!**


```python
samples = np.random.uniform(range_[0], range_[1], num_samples)

print("결과: {}".format(np.sum(samples*integrand_norm(samples, 1)/den)/num_samples))


```
    결과: 4.1905484171505805