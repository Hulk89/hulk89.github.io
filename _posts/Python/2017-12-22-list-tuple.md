---
layout: post
title:  "List와 tuple의 차이"
date:   2017-12-22
category: "python"
tags: [python, list, tuple]
---

# List & Tuple

| | List| Tuple|
|:-:|:-:|:-:|
| 동적할당 | O | X |
| mutable | O | X |

* Tuple은 런타임에 캐싱해서 커널에 메모리 요청을 하지 않아도 됨.
    * 그러니까 안변할 것은 tuple로 만들어서 쓰자!
    * 튜플 두 개를 합치는 것은 O(n)시간이 걸린다.(할당 & 복사)
* List는 동적 할당이 가능하고 새로운 element 추가가 가능하다.
    * 동적 할당을 위해 append()로 하나하나 추가시키면
        * 메모리 추가할당 및 복사, 삭제가 빈번하게 일어나서 엄청 느리다!