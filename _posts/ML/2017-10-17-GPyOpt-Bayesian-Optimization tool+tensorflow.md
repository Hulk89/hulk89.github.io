---
layout: post
title:  "GPyOpt - Bayesian Optimization and tensorflow 예제 만들기"
date:   2017-10-17
category: "machine learning"
tags: [tensorflow, bayesian inference]
---

# 문서 내용

여기서는 GPyOpt와 tensorflow를 사용하여, ML Model의 parameter를 최적화하는 실험을 한다.

* Data: MNIST
* Model: FNN Model
  * input: 784-dim
  * output: 10-dim
  * parameter
    * learning-rate (0.01, 0.5)
    * layer 수 {2, 3}
    * hidden_size {30, 60}
    * batch_size {500, 1000, 2000}

## tensorflow model with score

여기서는 먼저 tensorflow model을 만든다. Bayesian Optimization는 model을 학습하고 결과를 받는 함수를 objective function으로 생각하기 때문에 accuracy에 -를 취해주어야 한다.

**TODO: Objective function의 scale에 따라서 다르게 최적화가 되는지... 알아봐야할 듯!**


```python
import tensorflow as tf
import numpy as np
import matplotlib.pyplot as plt
from tensorflow.examples.tutorials.mnist import input_data
```


```python
mnist = input_data.read_data_sets('../MNIST_data', one_hot=True)
```

    Extracting ../MNIST_data/train-images-idx3-ubyte.gz
    Extracting ../MNIST_data/train-labels-idx1-ubyte.gz
    Extracting ../MNIST_data/t10k-images-idx3-ubyte.gz
    Extracting ../MNIST_data/t10k-labels-idx1-ubyte.gz



```python
def objective_function(args):
    '''
    args[0] : layer 개수
    args[1] : hidden size
    '''
    lr_rate = args[0, 0]
    num_layer = args[0, 1].astype(int)
    output_size = args[0, 2].astype(int)
    batch_size = args[0, 3].astype(int)

    
    print("실험 시작\nargs: {}".format(args))
    # reset graph
    tf.reset_default_graph()
    
    x = tf.placeholder(tf.float32, shape=[None, 784])
    y_ = tf.placeholder(tf.float32, shape=[None, 10])
    
    input_size = 784
    remain_num_layer = num_layer-1
    
    new_x = x  # feed_dict에 x로 들어가기 위해서...
    for i in range(remain_num_layer):
        W = tf.Variable(tf.random_normal([input_size, output_size]))
        b = tf.Variable(tf.random_normal([output_size]))
        new_x = tf.nn.relu(tf.matmul(new_x, W) + b)
        input_size = output_size

    W = tf.Variable(tf.random_normal([input_size, 10]))
    b = tf.Variable(tf.random_normal([10]))
    
    y = tf.matmul(new_x, W) + b
    cross_entropy = tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits(labels=y_,
                                                                           logits=y))

    global_step = tf.Variable(0, trainable=False)

    lr_rate_decayed = tf.train.exponential_decay(lr_rate,
                                                 global_step,
                                                 10,
                                                 0.99,
                                                 staircase=True)
    train_step = tf.train.GradientDescentOptimizer(lr_rate_decayed).minimize(cross_entropy)

    acc = [0]
    with tf.Session() as sess:
        sess.run(tf.global_variables_initializer())

        for i in range(1000):
            batch = mnist.train.next_batch(batch_size)
            train_step.run(feed_dict={x: batch[0], y_: batch[1]})

            correct_prediction = tf.equal(tf.argmax(y,1), tf.argmax(y_,1))
            accuracy = tf.reduce_mean(tf.cast(correct_prediction, tf.float32))
            if i % 50 is 0:
                new_acc = accuracy.eval(feed_dict={x: mnist.test.images, y_: mnist.test.labels})
                print("중간 결과: {}".format(new_acc))
                if abs(new_acc - acc[-1]) < 0.0001: #효과가 별로 없으면
                    break
                else:
                    acc.append(new_acc)
                
        accuracy = tf.reduce_mean(tf.cast(correct_prediction, tf.float32))
        
        acc = accuracy.eval(feed_dict={x : mnist.test.images,
                                       y_: mnist.test.labels})
        print("실험 하나 종료! 결과\nargs: {}, acc: {}".format(args, acc))
        return 1 - acc  # fMin이기 때문에...
```

# Bayesian Optimization
위 코드 마지막을 보면 `1-acc`로 accuracy에 마이너스를 취해줬지..

이제 다음 코드로 Bayesian Optimization을 하면 끝!


```python
from GPyOpt.methods import BayesianOptimization
import numpy as np

# continuous가 discrete 앞에 위치해야함! BUGBUG!! https://github.com/SheffieldML/GPyOpt/issues/85
domain = [{'name': 'lr_rate',
          'type': 'continuous',
          'domain': (0.01, 0.5),
           'dimensionality': 1},
          {'name': 'num layer',
           'type': 'discrete',
           'domain':(2, 3),
           'dimensionality': 1},
          {'name': 'hidden size',
           'type': 'discrete',
           'domain': (30, 60),
           'dimensionality': 1},
          {'name': 'batch size',
           'type': 'discrete',
           'domain': (500, 1000, 2000),
           'dimensionality': 1}]

# --- Solve your problem
myBopt = BayesianOptimization(f=objective_function,
                              domain=domain,
                              initial_design_numdata=5)
myBopt.run_optimization(max_iter=15)
myBopt.plot_acquisition()
```

    실험 시작
    args: [[  3.27519352e-01   3.00000000e+00   6.00000000e+01   1.00000000e+03]]
    중간 결과: 0.11749999970197678
    ...
    중간 결과: 0.8468000292778015
    중간 결과: 0.8496000170707703
    실험 하나 종료! 결과
    args: [[   0.5    2.    30.   500. ]], acc: 0.8436999917030334


#  결과 보기
다음 코드로 결과를 확인할 수 있다.


```python
print(myBopt.x_opt)  # lr_rate, #layer, hidden_size, batch_size

N, _ = myBopt.X.shape
for i in range(N):
    if np.array_equal(myBopt.x_opt, myBopt.X[i, :]):
        print(1 - myBopt.Y[i,:])  # accuracy 변환
```

    [  2.72432763e-01   2.00000000e+00   6.00000000e+01   2.00000000e+03]
    [ 0.87239999]


# 부록


```python
# 돌릴 필요는 없고 참고삼아...
args = np.array([[2, 10, 500, 0.3]])
objective_function(args)
```