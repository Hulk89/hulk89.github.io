---
layout: post
title:  "tensorfow eager execution"
date:   2018-01-15
category: "machine learning"
tags: [tensorflow, eager execution]
---

[tensorflow doc 원문](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/contrib/eager/python/g3doc/guide.md)

# Eager execution

* eager execution을 enable하는 것은 tensorflow 함수들의 동작을 바꾸는 것이다.
  * 예를 들면, `Tensor` object가 이전에는 computational graph의 노드에 대한 symbolic node였는데
  * 그냥 value를 가리키게 된다.
* 결과로, enable은 프로그램 처음 시작시에 해야하며, 중간에 disable은 불가능하다.



```python
import tensorflow as tf
import tensorflow.contrib.eager as tfe

tfe.enable_eager_execution()
```

위의 코드로 enable을 한다.


```python
x = tf.matmul([[1,2],
               [3,4]],
              [[4,5],
               [6,7]])
y = tf.add(x, 1)

z = tf.random_uniform([5, 3])

print(x)
print(y)
print(z)
```

    tf.Tensor(
    [[16 19]
     [36 43]], shape=(2, 2), dtype=int32)
    tf.Tensor(
    [[17 20]
     [37 44]], shape=(2, 2), dtype=int32)
    tf.Tensor(
    [[ 0.8922441   0.6574986   0.13251901]
     [ 0.52682853  0.73264265  0.95296896]
     [ 0.5486275   0.70670152  0.87073851]
     [ 0.43007874  0.50991511  0.6052494 ]
     [ 0.15779459  0.66595078  0.8245461 ]], shape=(5, 3), dtype=float32)


# NumPy array로 왔다갔다...

* `NumPy array`와 `Tensor`는 자동으로 호환된다.
  * tensorflow의 operation에 numpy array가 들어오면 `Tensor`로 바뀜
  * numpy operation에 `Tensor`가 들어와도 numpy array로 바뀜


```python
import numpy as np

x = tf.add(1, 1)                     # tf.Tensor with a value of 2
y = tf.add(np.array(1), np.array(1)) # tf.Tensor with a value of 2
z = np.multiply(x, y)                # numpy.int64 with a value of 4
```

* 사용자가 `Tensor` object로부터 직접 numpy array를 얻고싶으면 `numpy()`함수를 호출하면 된다.


```python
print(y)
print(y.numpy())
```

    tf.Tensor(2, shape=(), dtype=int64)
    2


# GPU acceleration

* Eager execution을 하면 노드를 GPU에 수동할당해야함!
  * `with tf.device('/gpu:0')`로 하면 된다.
  * `tfe.num_gpus()`로 gpu 갯수를 알 수 있다.


```python
x = tf.random_normal([10, 10])

x_gpu0 = x.gpu()
x_cpu = x.cpu()

_ = tf.matmul(x_cpu, x_cpu)  # Runs on CPU
_ = tf.matmul(x_gpu0, x_gpu0)  # Runs on GPU:0

if tfe.num_gpus() > 1:
    x_gpu1 = x.gpu(1)
    _ = tf.matmul(x_gpu1, x_gpu1)  # Runs on GPU:1
```

# Automatic Differentiation

* `tfe.gradients_function(f)`: python 함수 `f`에 대해서 derivative들을 리턴한다. 물론 `f`의 인자들에 대해서 미분한다.
  * 제약조건: `f`는 scalar를 return해야한다.
  * return: `Tensor` object들의 list
* `tfe.value_and_gradients_function(f)`: 위의 함수와 비슷하나, `f`의 value도 같이 return한다.

인자 하나짜리로 실험을 해보자!


```python
def f(x):
    return x*x

df = tfe.gradients_function(f)
```


```python
print(df(3))             # df 함수의 결과값
print(type(df(3)))       # df 함수의 결과값의 type
print(len(df(3)))        # df 함수의 결과값의 길이
print(df(3)[0].numpy())  # numpy 값
```

    [<tf.Tensor: id=108, shape=(), dtype=int32, numpy=6>]
    <class 'list'>
    1
    6



```python
'''
3개짜리 인자로 order가 지켜지는지 보자
'''
def f2(x, y, z):
    return x*y*z

df2 = tfe.gradients_function(f2)
```


```python
print(df2(3 ,4, 5))  # order는 지켜지는 것 같다..
```

    [<tf.Tensor: id=149, shape=(), dtype=int32, numpy=20>, <tf.Tensor: id=150, shape=(), dtype=int32, numpy=15>, <tf.Tensor: id=148, shape=(), dtype=int32, numpy=12>]


요 함수들로 model을 학습시키는데 쓸 수 있다. linear regression model을 예로 들어보자


```python
def prediction(input_, weight, bias):
    return input_*weight + bias

# toy dataset
num_ex = 1000
training_inputs = tf.random_normal([num_ex])
noise = tf.random_normal([num_ex])
training_outputs = training_inputs * 3 + 2 + noise


def loss(weight, bias):
    error = prediction(training_inputs, weight, bias) - training_outputs
    return tf.reduce_mean(tf.square(error))

grad = tfe.gradients_function(loss)

W = 5.
B = 10.

lr = 0.01
print("initial loss: {}".format(loss(W, B).numpy()))

for i in range(200):
    (dW, dB) = grad(W, B)  # 이러면 귀찮아지는데....
    W -= dW * lr
    B -= dB * lr
    if i % 20 == 0:
        print("loss at step {}: {}".format(i, loss(W,B).numpy()))

print("final loss: {}".format(loss(W,B).numpy()))
print("W:{}, B:{}".format(W.numpy(), B.numpy()))
```

    initial loss: 69.62956237792969
    loss at step 0: 66.85037231445312
    loss at step 20: 29.827312469482422
    loss at step 40: 13.637370109558105
    loss at step 60: 6.5486159324646
    loss at step 80: 3.4408247470855713
    loss at step 100: 2.0765790939331055
    loss at step 120: 1.476935863494873
    loss at step 140: 1.213025689125061
    loss at step 160: 1.0967267751693726
    loss at step 180: 1.045410394668579
    final loss: 1.0234872102737427
    W:3.024306297302246, B:2.182044744491577


## custom gradient

함수가 어떤 지점에서 numerical instability를 가진다면 다음과 같이 gradient를 커스터마이즈해서 쓸 수 있다.


```python
@tfe.custom_gradient
def log1pexp(x):
    e = tf.exp(x)
    def grad(dy):
        return dy * (1 - 1 / (1 + e))  # 위에서 계산한 e를 갖다썼는데, 이게 계산량을 줄여준다
    return tf.log(1 + e), grad
grad_log1pexp = tfe.gradients_function(log1pexp)
```

# model 만들고 학습하기

아까 prediction을 보면 loss에 gradient를 구하고, 거기에 인자로 parameter들을 넣고 최적화함.

실제로 모델을 만들어서 최적화하려면 파라미터도 엄청 많을 것이다. 이대로는 못쓴다.

## Variables and Optimizers

* `tfe.implicit_gradients`
  * `f`에 쓰인 모든 인자의 derivatives를 계산
  * 반환된 함수를 호출하면, (grad val, Variable object) 튜플의 리스트를 반환




```python
class Model(object):
    def __init__(self):
        self.W = tfe.Variable(5., name='weight')
        self.B = tfe.Variable(10., name='bias')

    def predict(self, inputs):
        return inputs * self.W + self.B


# The loss function to be optimized
def loss(model, inputs, targets):
    error = model.predict(inputs) - targets
    return tf.reduce_mean(tf.square(error))

# A toy dataset of points around 3 * x + 2
NUM_EXAMPLES = 1000
training_inputs = tf.random_normal([NUM_EXAMPLES])
noise = tf.random_normal([NUM_EXAMPLES])
training_outputs = training_inputs * 3 + 2 + noise

# Define:
# 1. A model
# 2. Derivatives of a loss function with respect to model parameters
# 3. A strategy for updating the variables based on the derivatives
model = Model()
grad = tfe.implicit_gradients(loss)
optimizer = tf.train.GradientDescentOptimizer(learning_rate=0.01)

# The training loop
print("Initial loss: %f" %
      loss(model, training_inputs, training_outputs).numpy())
for i in range(201):
    optimizer.apply_gradients(grad(model, training_inputs, training_outputs))
    if i % 20 == 0:
        print("Loss at step %d: %f" %
              (i, loss(model, training_inputs, training_outputs).numpy()))
print("Final loss: %f" % loss(model, training_inputs, training_outputs).numpy())
print("W, B = %s, %s" % (model.W.numpy(), model.B.numpy()))
```

    Initial loss: 68.121872
    Loss at step 0: 65.497581
    Loss at step 20: 30.059015
    Loss at step 40: 14.102830
    Loss at step 60: 6.915086
    Loss at step 80: 3.675683
    Loss at step 100: 2.215032
    Loss at step 120: 1.556112
    Loss at step 140: 1.258721
    Loss at step 160: 1.124437
    Loss at step 180: 1.063774
    Loss at step 200: 1.036357
    Final loss: 1.036357
    W, B = 3.00555, 2.15338
