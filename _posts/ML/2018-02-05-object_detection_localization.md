---
layout: post
title:  "Object Detection & Localization 요약"
date:   2018-02-05
category: "machine learning"
tags: [ml, segmentation, rcnn, ssd, yolo]
---

# History

![IMAGE](/resources/118DBE40101096B13C5BA651C9F719A1.jpg)

# R-CNN류

## R-CNN

![IMAGE](/resources/A11BD132BEE6E41B6A6DE61F81A5B33B.jpg)
1. CNN fine-tuning (3번에 쓰일 Classifier)
2. Selective search로 ROI 계산
3. ROI들을 warp해서 (2)CNN에 넣고 feature extract를 수행한다.
4. 분석된 Feature들을 softmax classifier나 SVM에 붙여서 각 proposal의 score를 매긴다.
5. Non-Maximum Suppression(NMS)을 이용하여 bounding box를 구한다.
> NMS 예제 ![IMAGE](/resources/E5E538ACE3F54BA60F79D256E9D2FBBE.jpg)

* 문제점
  * CNN, SVM, BB regressor등을 훈련해야함.
  * 비슷한 박스들을 CNN돌리니까 느려...

## Fast R-CNN
![IMAGE](/resources/0F31CFB52EF92A38A4B0AA6591B9E263.jpg)

* CNN network를 전체 이미지에 대해서 한번에 하고 ROI를 즐 뽑아내면 CNN 돌리는 시간이 줄어듬
* SVM, BBR을 하나의 network로 트레이닝함

## Faster R-CNN
Selective Search가 Bottleneck이다. 이를 뒤에있는 CNN의 weight를 이용해서 구현하면 훨씬 빠르고 쉬워진다! ->  Region proposal network(RPN)

## 단점

Selective search를 하든, RPN을 쓰든 어쨌든 느리다. 그래서 사실 논문을 제대로 안보고 스킵스킵...

---

# Single Shot Detector류

![speed.png](/resources/0C9004C470D6B5F3FB0D88C07CC83484.png)
Single Shot Detection으로 오면서 7fps가 45fps로 껑충 뛰게된다.[참조 슬라이드](https://docs.google.com/presentation/d/1kAa7NOamBt4calBU9iHgT8a86RRHz9Yz2oh4-GTdX6M/edit#slide=id.g151008b386_0_0)

## YOLO (You only look once)
![IMAGE](/resources/6A5431596EFB73C58A6F6F9C4498C4E4.jpg)

* 이미지를 grid로 나눠서, 해당 grid안에 center가 있는 boundingbox들의 위치와 confidence를 학습시킴
* 처음에는 VGG를 가져다쓰고, 이를 CNN Layer 여러개를 거치고, FC를 하면 7x7x30의 feature가 나온다.
![IMAGE](/resources/7CC9BB381FF82EA63CCCE9ACA19E0103.jpg)
* 여기서 하나의 그리드에 30이 나오는데
  * [2개의 bounding box(각각 5개 x,y,w,h,c) + 20개의 class에 대한 conditional class prob.]를 의미한다.
  * ![yolo.png](/resources/977D8CDAC8A8DE2A28978B33D7B69FFB.png)
  * > c는 해당 BB의 confidence 즉,p(object). 설명 안하고 넘어갔는데, 사실 bounding box의 확률은, object가 존재하는지와, 해당 object가 어떤 class인지 두가지로 나뉘어져있다.
* 요걸 가지고 비슷한 box에 대해서 Non-maximum suppression을 하면 완성!
  * [NMS 잘 나와있는 곳!](https://docs.google.com/presentation/d/1aeRvtKG21KHdD5lg6Hgyhx5rPq_ZOsGjG5rJ1HP7BbA/pub?start=false&loop=false&delayms=3000&slide=id.g137784ab86_4_3258)

### loss
* ![loss.png](/resources/BC6EEC633F5842DAF6001C9968D4A1F5.png)
  * object가 존재할 때, center 차이의 제곱
  * object가 존재할 때, width, height의 root의 차이의 제곱
  * object가 존재할 때, Confidence score 계산(Ci가 1)
  * object가 존재하지 않을 때, Confidence score 계산(Ci가 0)
  * object가 존재할 때, 각 class의 확률 차이의 제곱
  
### Limitation of YOLO
[베껴옴](https://curt-park.github.io/2017-03-26/yolo/)
* 각각의 grid cell이 하나의 클래스만을 예측할 수 있으므로, 작은 object 여러개가 다닥다닥 붙으면 제대로 예측하지 못한다.
* bounding box의 형태가 training data를 통해서만 학습되므로, 새로운/독특한 형태의 bouding box의 경우 정확히 예측하지 못한다.
* 몇 단계의 layer를 거쳐서 나온 feature map을 대상으로 bouding box를 예측하므로 localization이 다소 부정확해지는 경우가 있다.

---

## SSD(Single Shot multibox Detector)
* Yolo와 거의 비슷하나, Convolution layer의 중간중간에서 모두 feature를 뽑는 점이 크게 다르다.
  * ![IMAGE](/resources/CBFE50915DD65B093A0AF1FC560F3026.jpg)
  * 위의 그림에서 Detections에 들어가는 화살표가 convolution layer 사이사이에 존재함.
* 또한 각 grid에서는 location관련정보 4개[∆(cx, cy, w, h)]와 각 class의 confidence로 이루어진다.
* ![ssd1.png](/resources/F5165DD916D994804C56CFF3FDE66E0B.png)
* 좀 더 자세히 알고싶으면 [여기...](https://www.slideshare.net/xavigiro/ssd-single-shot-multibox-detector)

### loss 계산
* confidence와 location error를 loss로 씀
  * ![1.png](/resources/DB73BB2D1E5050F7BB36DC4D72437733.png)
* location error
  * ![2.png](/resources/2AA8D9EC4EC8C36F1D6C5B7451ADF2A0.png)
* confidence error
  * ![3.png](/resources/AD7254D51079150F2D2F24EFEE19B845.png)
> smooth-L1 loss는 0 근처에서 quadratic인 l1 loss의 변형이다.