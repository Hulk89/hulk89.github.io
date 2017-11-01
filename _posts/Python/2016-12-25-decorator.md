---
layout: post
title:  "Decorator - 꾸며보자!"
date:   2016-12-25
category: "python"
tags: [python, decorator]
---

# Why?
flask를 쓰다보니 app.route 어쩌구저쩌구 하는 decorator가 보인다. 먼저 decorator가 어떤 역할을 하는지 살펴보자!

# decorator란?
decorator는 python의 함수 본체를 꾸며주는 역할을 한다. 
다음 코드를 보자.

## decorator 예제

```python
def simple_decorator(f):
    def wrapper():
        print "Entering Function"
        f()
        print "Exited Function"
    return wrapper

@simple_decorator
def hello():
    print "Hello world"

hello()

''' 
"Output"
Entering Function
Hello world
Exited Function
'''
```

## 인자를 갖는 decorator 예제
다음은 인자를 갖는 decorator 이다. 이번엔 class로 만들어보았다.

```python
class Verbose:
    def __init__(self, f):
        print "Initializing Verbose."
        self.func = f

    def __call__(self, *args, **kwargs):
        print "Begin", self.func.__name__
        self.func(*args, **kwargs);
        print "End", self.func.__name__

@Verbose
def my_function(name):
    print "hello,", name

my_function("Hulk")

'''
"Output"
Begin my_function
hello, Hulk
End my_function
'''
```

## 다수의 decorator 지원

이런 것도 된다!

```python
def makebold(fn):
    def wrapped():
        return "<b>" + fn() + "</b>"
    return wrapped

def makeitalic(fn):
    def wrapped():
        return "<i>" + fn() + "</i>"
    return wrapped

@makebold
@makeitalic
def hello():
    return "hello world"

print hello() ## returns "<b><i>hello world</i></b>"
```

## decorator에 partial을 끼얹자!

위의 예제에서, 다수의 decorator를 쓸 수 있었다. 그런데 만약, bold, italic, strike-through 등 여러가지의 decorator를 만들어야한다면?

decorator에 argument를 넣을 수는 없을까 고민이 될 것이다.

`functools`의 `partial` 함수를 사용하면 이를 만들 수 있다.

```python
from functools import partial

def addtag(fn, tag):
    def wrapped():
        return "<" + tag + ">" + fn() + "</" + tag + ">" 
    return wrapped

makebold = partial(addtag, tag="b")
makeitalic = partial(addtag, tag="i")
    
@makebold
@makeitalic
def hello():
    return "hello world"

print(hello()) ## returns "<b><i>hello world</i></b>"
```