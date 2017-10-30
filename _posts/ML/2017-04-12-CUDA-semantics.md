---
layout: post
title:  "pytorch - Cuda semantics"
date:   2017-04-06
category: "pytorch"
tags: [ml, nn, cuda, pytorch]
---

[원문](http://pytorch.org/docs/notes/cuda.html)
`torch.cuda`는 현재 선택된 GPU를 계속 씁니다. 여기서 할당한 모든 CUDA tnesor들은 선택된 GPU안에서 만들어집니다. 
`torch.cuda.device`를 `with`절과 함께 사용하여 GPU 선택을 할 수 있습니다.

그러나 한번 tensor가 할당되면, 해당 tensor들을 사용한 operation들은 선택된 device와 관계없이 tensor들과 같은 device에 할당됩니다.

cross-GPU operation은 기본적으로 허용되지 않습니다. 그러나 `copy_()`를 쓰면 가능합니다. peer-to-peer memory access를 enable하지 않으면, 다른 device간에 퍼져있는 tensor들을 사용하는 operation은 error를 냅니다.

밑의 코드는 간단한 예제입니다.

```python
x = torch.cuda.FloatTensor(1)
# x.get_device() == 0
y = torch.FloatTensor(1).cuda()
# y.get_device() == 0

with torch.cuda.device(1):
    # allocates a tensor on GPU 1
    a = torch.cuda.FloatTensor(1)

    # transfers a tensor from CPU to GPU 1
    b = torch.FloatTensor(1).cuda()
    # a.get_device() == b.get_device() == 1

    c = a + b
    # c.get_device() == 1

    z = x + y
    # z.get_device() == 0

    # even within a context, you can give a GPU id to the .cuda call
    d = torch.randn(2).cuda(2)
    # d.get_device() == 2
```

## Best practices

### pinned memory buffer를 사용하라

host에서 GPU로 카피하는 것은 pinned(page-locked) memory를 사용할때 훨씬 빠릅니다. CPU tensor들과 storage들은 `pin_memory()` method[pinned region에 data를 넣은 object의 카피를 리턴하는]를 노출합니다.
(-그러나 실험 결과 그렇게 빨라지지 않음.)

또한, 한번 tensor와 storage를 pin하면, `cuda()`에 `async=True`로 하여 asynchronous GPU copy들을 쓸 수 있습니다. 이는 data transfer와 계산 겹치게 하기위해 쓰일 수 있습니다.(pipe-line을 얘기하는 것 같다.)

batch들을 생성하는 `DataLoader`를 `pin_memory=True`를 사용하여 생성하면, pinned memory에 위치시킬 수 있습니다.

## Use nn.DataParallel instead of multiprocessing

batched input과 multiple GPU를 포함한 많은 케이스에 `DataParallel`을 기본으로 써보는게 좋습니다. GIL이 있더라도, 단일 python process가 multiple GPU를 충분히 돌릴 수 있습니다.

0.1.9 버전에서 8개 이상의 많은 GPU는 fully utilized가 아직 안되지만, 계속 개발중입니다.

multiprocessing 쓰지 마세요.