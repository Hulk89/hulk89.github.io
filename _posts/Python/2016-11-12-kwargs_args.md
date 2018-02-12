---
layout: post
title:  "kargs, args"
date:   2016-11-12
category: "python"
tags: [python, args]
---

## `*args`
파라미터를 몇개 받을 지 모르는 경우 사용한다. **튜플** 형태로 전달된다.

```python
def print_param(*args):
    print args
    for p in args:
        print p
 
print_param('a', 'b', 'c', 'd')
#('a', 'b', 'c', 'd')
#a
#b
#c
#d
```

## `**kwargs`
파라미터 명을 같이 보낼 수 있다. **딕셔너리**형태로 전달된다.

```python
def print_param2(**kwargs):
    print kwargs
    print kwargs.keys()
    print kwargs.values()
 
    for name, value in kwargs.items():
        print "%s : %s" % (name, value)
 
print_param2(first = 'a', second = 'b', third = 'c', fourth = 'd')
 
#{'second': 'b', 'fourth': 'd', 'third': 'c', 'first': 'a'}
#['second', 'fourth', 'third', 'first']
#['b', 'd', 'c', 'a']
#second : b
#fourth : d
#third : c
#first : a
```

## 나머지...

```python
def print_param3(*args, **kwargs):
    print args
    print kwargs
 
print_param3('a', 'b')
#('a', 'b')
#{}
 
print_param3(third = 'c', fourth = 'd')
#()
#{'fourth': 'd', 'third': 'c'}
 
print_param3('a', 'b', third = 'c', fourth = 'd')
#('a', 'b')
#{'fourth': 'd', 'third': 'c'}
```

함수의 인자로 List에 *를 붙이면 position arguments로 함수에 집어넣은 것과 똑같은 의미를 갖는다.
Dictionary의 경우 **를 붙이면 keyword arguments로 함수에 집어넣은 것과 같은 의미이다.

```python
def print_param4(a, b, c):
    print a, b, c
 
p = ['a', 'b', 'c']
print_param4(*p)
#a b c
 
p2 = {'c' : '1', 'a' : '2', 'b' : '3'}
print_param4(**p2)
#2 3 1
```