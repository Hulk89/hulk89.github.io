---
layout: post
title:  "Faster gaze prediction with dense networks and Fisher pruning"
date:   2018-04-26
category: "machine learning"
tags: [ml, pruning]
---
[논문 링크](https://arxiv.org/abs/1801.05787)
## 세 줄 요약
* 네트워크의 중요하지 않은 파라미터를 찾아서 가지치기를 하는 것을 pruning이라 함.
* 중요하지 않은 파라미터를 찾는데, 근사시켜보니 Fisher information꼴이더라... → Fisher pruning.
* framework에서 지원하기 전까지는 건들지 않는 것이 정신건강에 좋다...

## 좀 더 자세히..
* pruning 방법
    * Loss function과 어떤 parameter를 지운 loss function의 차이를, 테일러 전개하여 2차 근사시키고
      * ![1](/resources/4257940176A3DA7257F954571C8DBB19.png)
      * ![2](/resources/9BCF065F4D91BFCE7E08F6C4DE452503.png)
      * ![3](/resources/65F4F29B436B5167AA94FCA5726581AE.png) ---(a)
    * 이를 전개하다보면(appendix1에 나옴) Fisher Information 꼴이 나옴
      * ![4](/resources/A492211E0CA7E5144B5C6BA736C09E78.png)
    * 이 차이를 최소화 하는 parameter → 중요하지 않은 parameter 므로 가지치기한다!
      * N개의 데이터에 대해서 수식 (a)를 계산한 것...(1st term은 모든 데이터에 대해서 보면 0에 수렴한다고 가정해버려서 없어짐)
      * ![5](/resources/44E8ACC46259B9FB3A6EB0FA88EBEFBD.png)
* computational cost 추가
    * 어떤 feature를 제거하는가는 loss의 영향도 중요하지만, 제거했을 때의 속도향상도 중요함
      * ![6](/resources/18A3D321200EE98B97A03FE28360F929.png)
    * loss + computational cost를 추가해서 pruning을 해본다.
      * ![7](/resources/836A4F4E61B66975D8903AD703DF7302.png)