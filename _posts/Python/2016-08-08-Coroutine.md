---
layout: post
title:  "Coroutine"
date:   2016-08-08
category: "python"
tags: [python, coroutine]
---
> Coroutine 정의<br>
**프로그램에서 대등한 관계로 서로 호출하는 것.** 예를 들면, 게임 프로그램에서 각 플레이어의 루틴은 서로 코루틴된다.

### 종속적이지 않고 서로 대화하는 형식의 함수

다음 그래프는 동작 방식을 sequence로 설명한 그림이다.

![coroutine](/resources/coroutine.png)

Coroutine을 직접 만들어보자!

```python
# coding: utf-8

import random
import sys

reload(sys)
sys.setdefaultencoding('utf8')

def game(name):
    print "안녕 " + name
    print "조건 : 0 < num < 101"

    count = 0
    num = random.randrange(1,101)

    while True:
        OoC = (yield)
        count += 1

        if OoC < 0 or OoC > 100:
            print "잘못된 숫자!"
        elif OoC > num:
            print "더 작은 숫자야."
        elif OoC < num:
            print "더 큰 숫자야."
        else:
            print "%d번 만에 맞췄네!" %count

```

`OoC = (yield)` : 요걸로 입력을 받을 때 까지 대기한다.

```python
>>> from coroutine import game
>>> c = game("Hulk")
>>> c.next()
안녕 Hulk
조건 : 0 < num < 101
>>> c.send(50)
더 작은 숫자야.
>>> c.send(1)
더 큰 숫자야.
>>> c.send(20)
더 작은 숫자야.
>>> c.send(10)
더 큰 숫자야.
>>> c.send(15)
더 큰 숫자야.
>>> c.send(17)
더 작은 숫자야.
>>> c.send(16)
7번 만에 맞췄네!
>>> c.close()
```

`c.next()` : 코루틴을 호출한 직후 `next()`를 사용하여 첫 yield까지 동작시켜야 한다.

`c.send()` : send로 데이터를 보내서 yield 뒤를 실행시켜야 함.

`c.close()` : 코루틴을 종료시키려면 close()함수를 사용한다.