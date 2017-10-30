---
layout: post
title:  "01. softmax classifier와 neural network 구현"
date:   2017-01-12
category: "neural network"
tags: [ml, nn, softmax]
---

[02. SVM 구현]({% post_url 2017-01-12-Svm %})

[03. RNN 구현]({% post_url 2017-01-13-RNN %})

여기서는 [원문](http://cs231n.github.io/neural-networks-case-study/)을 번역겸 겸사겸사 따라해본다.
## Data manipulation

먼저 데이터를 만들어보자!

```python
import matplotlib.pyplot as plt
import numpy as np

N = 100 # class마다 점의 갯수
D = 2 # point의 dimension
K = 3 # class 갯수

##################### Data Manipulation #####################
X = np.zeros((N*K, D)) # data matrix (각 row가 data point)
y = np.zeros(N*K, dtype='uint8') # class label들

for j in range(K):
    ix = range(N*j, N*(j+1))
    r = np.linspace(0.0, 1, N) # radius
    t = np.linspace(j*4, (j+1)*4, N) + np.random.randn(N)*0.2 # theta

    X[ix] = np.c_[r*np.sin(t), r*np.cos(t)] # concatenation along the second axis.
    y[ix] = j

# visualize data
plt.scatter(X[:, 0], X[:, 1], c=y, s=40, cmap=plt.cm.Spectral)
plt.show()
```

주석에 대충 달아놨다. 

* `np.c_` : list의 각 element를 second axis를 따라 concatenation해주는 함수
* `np.random.randn` : "standard normal" distribution 에서 sample하는 함수
* `np.linspace(start, end, num=50)` : start부터 end까지 num갯수만큼 쪼개서 array를 만들어줌
* `plt.scatter(x, y, c=y, s=40, cmap=plt.cm.Spectral)` : x,y가 그릴 그림의 포인트들. s는 size인데 이는 array일 수 있고 scalar의 경우 그냥 사이즈이다. c와 cmap은 컬러 관련인데 나중에 알아보자... 별로 중요하지 않다고 생각함.

## Softmax

### Score를 구하기 (prediction)
이제 softmax를 써보자! score를 먼저 구해봅시당!

```python
# initialize parameters randomly
W = 0.01 * np.random.randn(D, K)
b = np.zeros((1, K))

# compute class scores for a linear classifier
scores = np.dot(X, W) + b
```

### loss 구하기

#### forward 부터!

먼저 $f$를 single point에 대한 score vector라고 가정한다(즉, 여기서는 3개의 숫자가 적혀진 array라고 볼 수 있다).
이 때, 해당 point에 대한 loss function은 다음과 같이 정의된다.
$$L_i = - log ( \frac{e^{f_{y_i}}} {\Sigma_j e^{f_j} } )$$
Softmax classifier의 f를 (unnormalized) log probability들을 가지고있다고 생각하기 때문에, 이를 exponentiate하면 (unnormalized) 확률을 얻을 수 있다. 이제 이를 normalize하면 확률을 제대로 얻을 수 있다고 보는 것!

이 식을 살펴보면, $f_{y_i}$가 작은 score이면 당연히 $L_i$가 커지고, 이는 큰 loss를 야기한다. 반대의 경우는 $log(1) = 0$이니까 loss가 0에 가까워짐.

이제 Softmax classifier loss의 data loss, regularization loss를 합친 전체 식을 보면 다음과 같다.
$$L = \frac{1}{N} \sum_i L_i + \frac{1}{2}\lambda \sum_k \sum_l W^2_{k,l}$$

이제 이를 코드로 써보자!

```python
#### Loss 계산 ####

### forward ###

# get unnormalized probabilities
exp_scores = np.exp(scores)
# normalize them for each example
probs = exp_scores / np.sum(exp_scores, axis=1, keepdims=True)

correct_logprobs = -np.log(probs[range(N*K),y])

# compute the loss: average cross-entropy loss and regularization
data_loss = np.sum(correct_logprobs)/(N*K)
reg_loss = 0.5*reg*np.sum(W*W)
loss = data_loss + reg_loss
```

#### Backpropagation으로 gradient 구하기

이제 loss를 줄이기 위해 gradient를 구해야한다. 여기서는 중간 파라미터로 $p$를 만들어 쓴다. 

$$ p_k = \frac {e^{f_k}} {\Sigma_j e^{f_j}} \ \ \ \ \ \ \ L_i = -log(p_{y_i}) $$

요렇게 놓고 score $f$에 관한 Loss, $Li$의 gradient를 구해보자!

$$\frac{\partial L_i }{ \partial f_k } = p_k - \mathbb{1}(y_i = k)$$
구하면 요렇게 나온다.

> **내가 해본 증명>** $$\frac{\partial L_i }{ \partial f_k } = \frac{\partial L_i }{ \partial p_{y_i} } \times \frac{\partial p_{y_i} }{ \partial f_k }$$인데 각각 텀을 구해서 곱하면 된다.<br>
일단 처음 term은 $ \frac{\partial L_i }{ \partial p_{y_i} } = -\frac{1}{p_{y_i}} $로 쉽게 구할 수 있다.<br>
$\frac{\partial p_{y_i} }{ \partial f_k }$를 구하려면 (1) $k = y_i$, (2) $k \neq y_i$ 두가지에 대해서 구해야한다.<br>
(1) $k = y_i$ 일 때,<br>
$$\begin{align*}\frac{\partial p_{y_i} }{ \partial f_k } 
&= \frac{\partial p_k }{ \partial f_k } \\
&= \frac{\partial (\frac {e^{f_{k}}} {\Sigma_j e^{f_j}}) }{ \partial f_{k} } \\
&= \frac {e^{f_{k}}} {\Sigma_j e^{f_j}} + e^{f_k} \cdot \frac{\partial(\frac {1} {\Sigma_j e^{f_j}})}{\partial f_k} \\
&= \frac {e^{f_{k}}} {\Sigma_j e^{f_j}} + e^{f_k} \cdot (-(\frac {1} {\Sigma_j e^{f_j}})^2) \cdot e^{f_k} \\
&= p_k - p_k^2
\end{align*}$$ <br>
이므로 gradient는<br>
$$p_k-1\ \ \ \ \ \ \ \ \ (k = y_i이므로...)$$<br>
(2) $k \neq y_i$ 일 때,<br>
$$\begin{align*}\frac{\partial p_{y_i} }{ \partial f_k } 
&= \frac{\partial (\frac {e^{f_{y_i}}} {\Sigma_j e^{f_j}}) }{ \partial f_{k} } \\
&= 0 + e^{f_{y_i}} \cdot \frac{\partial(\frac {1} {\Sigma_j e^{f_j}})}{\partial f_k} \\
&= e^{f_{y_i}} \cdot (-(\frac {1} {\Sigma_j e^{f_j}})^2) \cdot e^{f_k} \\
&= - p_k \cdot p_{y_i}\end{align*}$$<br>
이므로 gradient는<br>
$$p_k$$<br>

따라서 코드는 다음과 같다.

```python
### backward ###
dscores = probs
dscores[range(N*K),y] -= 1
dscores /= N*K

dW = np.dot(X.T, dscores)  ## 요거야 뭐 dimension 맞추다보면 알것고...
db = np.sum(dscores, axis=0, keepdims=True)  # 사실 b는 N개가 복제된 형태라 더한 듯...
dW += reg*W # don't forget the regularization gradient
```

## Neural network

여기서 달라진 것은 hidden layer가 하나 생긴 것 뿐.. 따라서 ReLU에 대한 미분만 제대로 할 수 있으면 된다. 코드만 일단 냉겨놔야지~

```python
import matplotlib.pyplot as plt
import numpy as np
from data import getData, showData

X, y, K = getData()
N, D = X.shape

# hyperparameters
step_size = 1e-0
reg = 1e-3

# initialize parameters randomly
h = 100
W = 0.01 * np.random.randn(D, h)
b = np.zeros((1, h))
W2 = 0.01 * np.random.randn(h, K)
b2 = np.zeros((1, K))

for i in range(10000):
    O = np.dot(X, W) + b
    hidden = np.maximum(0, O)
    scores = np.dot(hidden, W2) + b2
    # forward pass
    exp_scores = np.exp(scores)
    probs = exp_scores / np.sum(exp_scores, axis=1, keepdims=True)
    correct_logprobs = -np.log(probs[range(N),y])
    data_loss = np.sum(correct_logprobs)/(N)
    reg_loss = 0.5*reg*(np.sum(W*W) + np.sum(W2*W2))
    loss = data_loss + reg_loss

    if i % 1000 == 0:
        print("iteration {}: loss {}".format(i, loss))
    # backward
    dscores = probs
    dscores[range(N),y] -= 1
    dscores /= N

    dW2 = np.dot(hidden.T, dscores)
    db2 = np.sum(dscores, axis=0, keepdims=True)
    dW2 += reg*W2
    
    dhidden = np.dot(dscores, W2.T)
    dO = dhidden
    dO[hidden <= 0] = 0
    dW = np.dot(X.T, dO)
    db = np.sum(dO, axis=0, keepdims=True)
    dW += reg*W

    W2 += -step_size * dW2
    b2 += -step_size * db2
    W += -step_size * dW
    b += -step_size * db

# evaluate training set accuracy
hidden_layer = np.maximum(0, np.dot(X, W) + b)
scores = np.dot(hidden_layer, W2) + b2
predicted_class = np.argmax(scores, axis=1)


print('training accuracy: %.2f' % (np.mean(predicted_class == y)))
```