---
layout: post
title:  "Multi-gpu example"
date:   2017-04-06
category: "pytorch"
tags: [data parallel, pytorch, cuda]
---

[원문](http://pytorch.org/tutorials/beginner/former_torchies/parallelism_tutorial.html)
Data parallelism은 mini-batch를 나누어 더 작은 여러개의 mini-batch로 나누고, 이들을 parallel하게 돌리는 것이다.

Data parallelism은 `torch.nn.DataParallel`에 구현되어있다. module을 `DataParallel`로 감싸면 알아서 잘 multi GPU로 parallelize해준다.

## DataParallel

```python
import torch.nn as nn

class DataParallelModel(nn.Module):

    def __init__(self):
        super().__init__()
        self.block1 = nn.Linear(10, 20)

        # block2만 DataParallel을 한다.
        self.block2 = nn.Linear(20, 20)
        self.block2 = nn.DataParallel(self.block2)

        self.block3 = nn.Linear(20, 20)

    def forward(self, x):
        x = self.block1(x)
        x = self.block2(x)
        x = self.block3(x)
        return x
```

이 코드는 CPU-mode로 바꿀 필요가 없다.(?)

보통, pytorch의 nn.parallel primitive는 각자 쓸 수 있다. 우리는 simple MPI-like primitive들을 구현했다.

* replicate : 모듈을 여러 디바이스에 복사
* scatter : input을 첫번째 dimension으로 나누어 분산
* gather : input을 모아서 첫번째 dimension 기준으로 concatenate
* parallel_apply : 이미 분산된 input을 이미 분산된 모델에 적용

더 확실하게 하기위해, `data_parallel` 함수를 위의 primitive들로 구성한 것이다.

> 찾아보니 실제로 거의 비슷하게 구현되어있음.

```python
def data_parallel(module, input, device_ids, output_device=None):
    if not device_ids:
        return module(input)

    if output_device is None:
        output_device = device_ids[0]

    replicas = nn.parallel.replicate(module, device_ids)
    inputs = nn.parallel.scatter(input, device_ids)
    replicas = replicas[:len(inputs)]
    outputs = nn.parallel.parallel_apply(replicas, inputs)
    return nn.parallel.gather(outputs, output_device)
```

## CPU와 GPU를 모두 쓰는 model

이제 cpu/gpu를 모두 쓰는 예제를 살펴보자.

```python
class DistributedModel(nn.Module):

    def __init__(self):
        super().__init__(
            embedding=nn.Embedding(1000, 10),
            rnn=nn.Linear(10, 10).cuda(0),
        )

    def forward(self, x):
        # Compute embedding on CPU
        x = self.embedding(x)

        # Transfer to GPU
        x = x.cuda(0)

        # Compute RNN on GPU
        x = self.rnn(x)
        return x
```



