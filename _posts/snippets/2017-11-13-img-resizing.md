---
layout: post
title:  "image resizing"
date:   2017-11-13
category: "code snippet"
tags: [numpy, scipy, imageio]
---

```python
%matplotlib inline
import matplotlib.pyplot as plt
import numpy as np
import imageio
import scipy.misc

```


```python
im = imageio.imread('test.jpg')  # 읽고
plt.imshow(im)
print(im.shape)
```

    (1044, 1179, 3)

![output_1_1.png](/resources/DE463A7DB4857733E88BA0DF4F00F5B0.png)

```python
i_width = 32
i_height = 32

im2 = scipy.misc.imresize(im, (i_height, i_width))  # resize
```


```python
plt.imshow(im2)
print(im2.shape)
```

    (32, 32, 3)

![output_3_1.png](/resources/8DA55A152CDE0CA72E398A3BC6943132.png)


```python
im2 = im2.astype(np.float32)
im2 = im2/255.0
```