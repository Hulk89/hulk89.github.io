---
layout: post
title:  "decorator pattern"
date:   2016-11-10
category: "python design pattern"
tags: [python, pattern]
---

## Decorator Pattern ?

주어진 상황에 따라 동적으로 어떤 객체에 책임을 붙일 수 있는 패턴으로, 상속 대신 쓸 수 있는 대안이 된다.
python에는 `@decorator`를 통해 비슷한 일을 해줄 수 있다.

## Overview

![decorators_fig1.jpg](/resources/1B798E73D2D143E26D14F97072565B9F.jpg)
요렇게 생겼다.
* 최상위에 interface가 존재하며,
* concrete component들은 그 interface를 구현한 구현체이다.
* decorator는 interface를 상속받고 **interface를 가진다.**
* 그리고 자신의 **abstract method**들을 구현할 때 **가지고있는 interface를 이용하여 구현**한다.

말로 쓰니까 어려워보인다. 코드를 넣자!

## 코드

```python
from abc import ABCMeta, abstractmethod

# Interface
class Widget:
    __metaclass__ = ABCMeta
 
    @abstractmethod
    def draw(self):
        pass

# 구현체
class ConcreteWindow(Widget):
    def __init__(self, w : int, h: int):
        self.mSize = (w, h)

    def draw(self):
        print("my draw size is {}".format(self.mSize))

# decorator
class Decorator(Widget):
    def __init__(self, widget: Widget):
        self.mChildWindow = widget

    def draw(self):
        self.mChildWindow.draw()

class VScrollDecorator(Decorator):
    def draw(self):
        self.mChildWindow.draw()
        print("Draw Vertical Scrollbar")

class HScrollDecorator(Decorator):
    def draw(self):
        self.mChildWindow.draw()
        print("Draw Horizontal Scrollbar")


if __name__ == '__main__':
    testWindow = VScrollDecorator(HScrollDecorator(ConcreteWindow(200,100)))
    testWindow.draw()
```

## output

```python
my draw size is (200, 100)
Draw Horizontal Scrollbar
Draw Vertical Scrollbar
```