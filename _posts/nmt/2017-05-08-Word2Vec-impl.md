---
layout: post
title:  "Word2vec 구현"
date:   2017-05-08
category: "neural machine translation"
tags: [ml, nmt, word embedding]
---
# What is Word2Vec?

Word2Vec은 word embedding의 한 기법이다. 쓰임새같은 것들은 다들 잘 알고있으니 넘어가도록한다.

목차는 다음과 같다.
* Word2Vec Dataset
  * 주어, 목적어, 동사 로 이루어진 셋을 만든다. [['나는', '밥을', '먹는다'], ...]
  * 이를 적절히 셔플하고, 하나로 잇는다. (~~데이터를 많이 만들기 싫어서~~)
* Graph Build & Training
  * 실제 그래프를 만들고 트레이닝을 시켜본다. (Skip-gram model을 만든다)
* TSNE로 embedding 결과 보기
* 유사도 보기
  * tsne로 잘 안보이기 때문에, cos, dot product metric으로 각각 유사도를 비교해본다.
* 나머지...

# Word2Vec Dataset

간단한 toy dataset을 만들어보자!! 

* S+O+V로 된 쉬운 데이터
* vocab

위 두가지를 만들고 `decode_data`라는 idx->word함수를 만든다.


```python
import numpy as np
import tensorflow as tf
import math
import random

        # 0       1      2       3 
vocab = ['나는',   '그녀가',  '너는', '그가',
         '밥을', '콩밥을', '싸움을', '꽃을', 
         '먹었다', '했다',  '샀다', '만들었다']
dataset = [[0, 4, 8], [1, 4, 8], [2, 4, 8], [3, 4, 8],
           [0, 5, 8], [0, 5, 9], [1, 5, 8], [1, 5, 9],
           [2, 5, 8], [2, 5, 9], [3, 5, 8], [3, 5, 9],
           [0, 6, 9], [1, 6, 9], [2, 6, 9], [3, 6, 9],
           [0, 7, 10], [1, 7, 10], [2, 7, 10], [3, 7, 10], 
           [0, 6, 11], [1, 6, 11], [2, 6, 11], [3, 6, 11]]

def decode_data(data, vocab):  
    '''
    idx들의 list로 문장을 만들어주는 함수
    '''
    decoded_list = [vocab[idx] for idx in data]
    return ' '.join(decoded_list)

for data in dataset[0:5]:   # 데이터를 몇개만 찍어보자.
    print(decode_data(data, vocab))
```

    나는 밥을 먹었다
    그녀가 밥을 먹었다
    너는 밥을 먹었다
    그가 밥을 먹었다
    나는 콩밥을 먹었다


위에서 나온 데이터로 batch를 만들어보자!

우리가 만들 모델은 Skip-gram 모델이다. 

이는 하나의 input으로 여러 개의 인접 단어를 유추하는 것인데, 꼼수를 써서 input, label을 각각 하나씩 만드는 방법을 쓴다.(tensorflow 예제자체도 그렇게 구현되어있다)

데이터가 적기때문에 매번 dataset을 shuffle하도록 만들었다.
> 어순이 꼬이지 않게 라인단위로만 셔플을 한다.

* 원래 문장 : `그가 밥을 먹었다` 
  * data : (`밥을`, `그가`), (`밥을`, `먹었다`)


```python
def generate_input(dataset, num_skips):
    random.shuffle(dataset)  # 문장 단위로 셔플한다.

    # 일차원 array로 만든다. (window를 돌리기 위해!)
    flatten = []
    for list_ in dataset:
        flatten += list_

    # (나는, 그녀를 보았다.) => (i:그녀를, l:나는), (i:그녀를, l:보았다)
    data = []
    label = []
    for idx in range(num_skips, len(flatten)-num_skips):
        data.append(flatten[idx])
        data.append(flatten[idx])
        label.append([flatten[idx-1]])
        label.append([flatten[idx+1]])
    return data, label

input_, label = generate_input(dataset, 1)
```

확인해보자!


```python
for i, o in zip(input_[:4], label[:4]):
    input0 = decode_data([i], vocab)
    label0 = decode_data(o, vocab)
    print("data : {}, label : {}".format(input0, label0))
```

    data : 밥을, label : 나는
    data : 밥을, label : 먹었다
    data : 먹었다, label : 밥을
    data : 먹었다, label : 너는


# Graph build & Training

이제 그래프를 만들어볼까? 그 전에 잠깐 이론을 remind하고, 실제 구현과의 차이점을 알아보자.

## loss구하는 식(이론)
* $x$: $V x 1$의 one-hot Vector (input)
* $W$: $N x V$의 matrix (embedding)
* $P$: $V x N$의 matrix (projection)
* $y$: $V x 1$의 one-hot Vector (output)
라 하면

$$o = P \times W \times x$$
로 o를 구한 후에, $softmax(o)$를 각 vocab의 확률로 보고 loss를 구한다.

## 차이점
* Vocab이 많이 커지게 되면 `nce_loss`같이 sampling해서 softmax를 구하는 방식을 쓴다.
* 실제로는 one-hot vector 대신에 idx로 embedding_lookup 함수를 통해 $W \times x$를 구한다.


```python
vocab_size = len(vocab)
embedding_size = 5
batch_size = len(label)
num_sampled = 6  # vocab_size//2

## Graph build
embeddings = tf.Variable(tf.random_uniform([vocab_size,
                                            embedding_size],
                                           -1.0, 1.0))

nce_weights = tf.Variable(tf.truncated_normal([vocab_size,
                                               embedding_size],
                                              stddev=1.0 / math.sqrt(embedding_size)))
nce_biases = tf.Variable(tf.zeros([vocab_size]))

# Placeholders for inputs
train_inputs = tf.placeholder(tf.int32, shape=[batch_size])
train_labels = tf.placeholder(tf.int32, shape=[batch_size, 1])

embed = tf.nn.embedding_lookup(embeddings, train_inputs)

# 매번 음수 라벨링 된 셈플을 이용한 NCE loos 계산
loss = tf.reduce_mean(tf.nn.nce_loss(nce_weights,
                                     nce_biases,
                                     train_labels,
                                     embed,
                                     num_sampled,
                                     vocab_size))

# SGD optimizer 를 사용
optimizer = tf.train.GradientDescentOptimizer(learning_rate=1.0).minimize(loss)

init = tf.global_variables_initializer()
```


```python
with tf.Session() as sess:
    init.run()
    for i in range(1000):
        batch_inputs, batch_labels = generate_input(dataset, 1)
        feed_dict = {train_inputs: batch_inputs, train_labels: batch_labels}
    
        _, loss_val = sess.run([optimizer, loss], feed_dict=feed_dict)
        if i % 100 == 0:
            print(loss_val)
    emb_weights = sess.run(embeddings)

np.savetxt('dataset', emb_weights, fmt='%.5e', delimiter='\t')
with open('meta', 'w') as f:
    for i in vocab:
        f.write("{}\n".format(i))
```

    6.94404
    2.38125
    2.52816
    2.1634
    2.34068
    2.05583
    2.33507
    2.33845
    2.04755
    2.25775


# TSNE로 embedding 결과 보기

간단히 알아본다. 데이터가 별로 없고 해서 결과가 좋게 나오지 않는다.


```python
from sklearn.manifold import TSNE

model = TSNE(n_components=2, random_state=0)
emb_2d = model.fit_transform(emb_weights)
emb_2d = emb_2d * 1000
```


```python
import matplotlib.pyplot as plt
import pylab as Plot

from matplotlib import font_manager, rc
font_name = font_manager.FontProperties(fname="/Library/Fonts/AppleMyungjo.ttf").get_name()
rc('font', family=font_name)

%matplotlib inline

Plot.figure(figsize=(20, 20), dpi=20)
plt.scatter(emb_2d[:,0], emb_2d[:,1], 50)

for idx, word in enumerate(vocab):
    x = emb_2d[idx, 0]
    y = emb_2d[idx, 1]
    plt.text(x, y, word, fontsize=50)
plt.show()
```
> 그림은 ipynb에...

그림이 생각보다 안이쁘다... 뭐가 뭐랑 가까운지 TSNE로 봐도 제대로 안보임.

그래서 유사도를 가지고 비슷한 녀석을 뽑는 것을 만들어보자!

# 유사도 보기



```python
from numpy import linalg as LA

def dot_metric(embed, query):
    return np.dot(embed, query.T)
    
def cos_metric(embed, query):
    scores = np.dot(embed, query.T)
    
    for i in range(scores.shape[0]):
        leng = LA.norm(query) * LA.norm(embed[i,:])
        scores[i] /= leng
    return scores
    
def nNeighbor(query: str, embed, vocab, metric, n=1):
    idx = vocab.index(query)
    query_vec = embed[idx,:]
    score = metric(embed, query_vec.T)
    #best_idx = np.argmax(score, n)
    idxs = (-score).argsort()
    idxs = idxs[:n]
    return decode_data(idxs, vocab)

print("-"*25+"dot product 결과" + "-"*25)
for word in vocab:
    nBest = nNeighbor(word, emb_weights, vocab, dot_metric, 4)
    print("query: %10s, neighbor: %4s" % (word, nBest))

print("-"*25+"cosine 결과" + "-"*25)
for word in vocab:
    nBest = nNeighbor(word, emb_weights, vocab, cos_metric, 4)
    print("query: %10s, neighbor: %4s" % (word, nBest))

```

    -------------------------dot product 결과-------------------------
    query:         나는, neighbor: 너는 그가 나는 그녀가
    query:        그녀가, neighbor: 너는 그가 나는 그녀가
    query:         너는, neighbor: 너는 그가 나는 그녀가
    query:         그가, neighbor: 너는 그가 나는 그녀가
    query:         밥을, neighbor: 밥을 콩밥을 샀다 했다
    query:        콩밥을, neighbor: 콩밥을 밥을 만들었다 싸움을
    query:        싸움을, neighbor: 싸움을 콩밥을 먹었다 만들었다
    query:         꽃을, neighbor: 꽃을 먹었다 했다 만들었다
    query:        먹었다, neighbor: 먹었다 샀다 했다 싸움을
    query:         했다, neighbor: 했다 만들었다 먹었다 밥을
    query:         샀다, neighbor: 샀다 먹었다 밥을 콩밥을
    query:       만들었다, neighbor: 만들었다 했다 콩밥을 밥을
    -------------------------cosine 결과-------------------------
    query:         나는, neighbor: 나는 너는 그녀가 그가
    query:        그녀가, neighbor: 그녀가 나는 그가 너는
    query:         너는, neighbor: 너는 나는 그녀가 그가
    query:         그가, neighbor: 그가 그녀가 나는 너는
    query:         밥을, neighbor: 밥을 콩밥을 했다 샀다
    query:        콩밥을, neighbor: 콩밥을 밥을 싸움을 만들었다
    query:        싸움을, neighbor: 싸움을 콩밥을 꽃을 먹었다
    query:         꽃을, neighbor: 꽃을 했다 만들었다 먹었다
    query:        먹었다, neighbor: 먹었다 했다 샀다 꽃을
    query:         했다, neighbor: 했다 만들었다 밥을 먹었다
    query:         샀다, neighbor: 샀다 먹었다 밥을 콩밥을
    query:       만들었다, neighbor: 만들었다 했다 콩밥을 꽃을


# OSX에서 install된 폰트들 찾기
다음 명령어로 확인할 수 있다.


```python
font_manager.OSXInstalledFonts()
```

    ['/Library/Fonts/Andale Mono.ttf',
      ...
     '/System/Library/Fonts/ZapfDingbats.ttf']