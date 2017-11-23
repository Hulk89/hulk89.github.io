---
layout: post
title:  "pytorch image loader"
date:   2017-11-23
category: "pytorch"
tags: [ml, pytorch, dataloader]
---

# data folder 구조

먼저 data folder의 구조를 살펴보자.


```python
!tree images
```

    images
    ├── class1
    │   ├── 1.png
    │   ├── 2.png
    │   ├── 3.png
    │   ├── 4.png
    │   ├── 5.png
    │   └── 6.png
    └── class2
        ├── 1.png
        ├── 2.png
        ├── 3.png
        └── 4.png
    
    2 directories, 10 files


class가 2개 있으며, 각각 6개, 4개의 image가 있다.

이제 이것을 뽑아보자!

# ImageFolder dataset을 이용해서 image batcher를 만들기


```python
import torchvision.datasets as dset
import torchvision.transforms as transforms
import torch

dataset = dset.ImageFolder(root="images/",
                           transform=transforms.Compose([
                               transforms.Scale(128),       # 한 축을 128로 조절하고
                               transforms.CenterCrop(128),  # square를 한 후,
                               transforms.ToTensor(),       # Tensor로 바꾸고 (0~1로 자동으로 normalize)
                               transforms.Normalize((0.5, 0.5, 0.5),  # -1 ~ 1 사이로 normalize
                                                    (0.5, 0.5, 0.5)), # (c - m)/s 니까...
                           ]))
dataloader = torch.utils.data.DataLoader(dataset,
                                         batch_size=2,
                                         shuffle=True,
                                         num_workers=8)
for i, data in enumerate(dataloader):
    print(data[0].size())  # input image
    print(data[1])         # class label
```

    torch.Size([2, 3, 128, 128])
    
     1
     1
    [torch.LongTensor of size 2]
    
    torch.Size([2, 3, 128, 128])
    
     0
     1
    [torch.LongTensor of size 2]
    
    torch.Size([2, 3, 128, 128])
    
     0
     0
    [torch.LongTensor of size 2]
    
    torch.Size([2, 3, 128, 128])
    
     1
     0
    [torch.LongTensor of size 2]
    
    torch.Size([2, 3, 128, 128])
    
     0
     0
    [torch.LongTensor of size 2]
    


0이 6개, 1이 4개 나왔다. 이는 각 클래스의 label로 쓰일 수 있다. 
나온 이미지를 한번 확인해보자!


```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt
from torchvision.transforms import ToPILImage
to_img = ToPILImage()

for i, data in enumerate(dataloader):
    img = data[0][0,:]
    break
print(img.size())
print("max: {}, min: {}".format(np.max(img.numpy()), np.min(img.numpy())))
plt.imshow(to_img(img))
```

    torch.Size([3, 128, 128])
    max: 1.0, min: -1.0

    <matplotlib.image.AxesImage at 0x1144d71d0>

![output_5_2.png](/resources/1D2DC117663EB56A91BB0AE79E5581B8.png)


변환 몇개 빼고 해보면 다음처럼 보인다.


```python
dataset = dset.ImageFolder(root="images/",
                           transform=transforms.Compose([
                               transforms.Scale(128),       # 한 축을 128로 조절하고
                               transforms.ToTensor(),       # Tensor로 바꾸고 (0~1로 자동으로 normalize)
                           ]))
dataloader = torch.utils.data.DataLoader(dataset,
                                         batch_size=2,
                                         shuffle=True,
                                         num_workers=8)

for i, data in enumerate(dataloader):
    img = data[0][0,:]
    break
print("max: {}, min: {}".format(np.max(img.numpy()), np.min(img.numpy())))
plt.imshow(to_img(img))
```

    max: 1.0, min: 0.0

    <matplotlib.image.AxesImage at 0x114027f28>

![output_7_2.png](/resources/9AC38B414A30DC7E72954BFE9DAE92A4.png)
