---
layout: post
title:  "tensorflow에서 image batch로 만들어 보내는 예제.."
date:   2017-11-13
category: "code snippet"
tags: [batch, image, tensorflow]
---

# CelebA Image batch로...


```python
%matplotlib inline
import matplotlib.pyplot as plt
import numpy as np
import imageio
import tensorflow as tf
```


```python
import os
os.environ["CUDA_DEVICE_ORDER"]="PCI_BUS_ID"
os.environ["CUDA_VISIBLE_DEVICES"]="7"
```


```python
def batch(file_list, batch_size=16):
    fileQ = tf.train.string_input_producer(file_list, shuffle=False)
    reader = tf.WholeFileReader()

    filename, data = reader.read(fileQ)
    image = tf.image.decode_jpeg(data, channels=3)

    img = imageio.imread(file_list[0])
    w, h, c = img.shape
    shape = [w, h, 3]
    
    image.set_shape(shape)
    min_after_dequeue = 10000
    capacity = min_after_dequeue + 3 * batch_size

    queue = tf.train.shuffle_batch(
        [image], batch_size=batch_size,
        num_threads=4, capacity=capacity,
        min_after_dequeue=min_after_dequeue, name='synthetic_inputs')
    
    crop_queue = tf.image.crop_to_bounding_box(queue, 50, 25, 128, 128)
    resized = tf.image.resize_nearest_neighbor(crop_queue, [64, 64])

    return tf.to_float(resized)
```


```python
import os
dir_ = '/root/CelebA/images'
jpgs = os.listdir(dir_)
filelist = [os.path.join(dir_, jpg) for jpg in jpgs]
plt.imshow(imageio.imread(filelist[0]))
```

`<matplotlib.image.AxesImage at 0x7f95ae746d68>`

![output_4_1.png](/resources/87A8B177F199407031533D2418212955.png)


```python
data_loader = batch(filelist)
```

위까지 하면 batch가 만들어지고.. 한번 뽑아보자!

```python
with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    
    coord = tf.train.Coordinator()
    threads = tf.train.start_queue_runners(sess=sess, coord=coord)

    im = sess.run(data_loader)
    
    coord.request_stop()
    coord.join(threads)
```


```python
im = im.astype(np.uint8)
plt.imshow(im[8, :])
```

`<matplotlib.image.AxesImage at 0x7f95ac1a75f8>`

![output_7_1.png](/resources/9B875675623048BFB4C3F746AE7857F9.png)