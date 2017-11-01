---
layout: post
title:  "MetaClass - class의 class!"
date:   2016-11-13
category: "python"
tags: [python, class, meta class]
---
#Meta class

## Metaclass란?
class를 만드는 어떠한 것. class : instance = metaclass : class로 볼 수 있다.

```python
MyClass = MetaClass()
MyObject = MyClass()
```

우리는 `python Class Master`에서 `type`함수가 `class`를 만드는 것을 보았다.

```python
MyClass = type('MyClass', (), {})
```

`type`함수는 사실 metaclass이며, python에서는 class를 만들기 위해, 속에서는 `type`을 이용하여 class를 만든다.

파이썬의 모든 것은 object이며, 모두 class로부터 나온다.

```python
age = 35
age.__class__
#<type 'int'>

name = 'bob'
name.__class__
#<type 'str'>

def foo(): pass
foo.__class__
#<type 'function'>

class Bar(object): pass
b = Bar()
b.__class__
#<class '__main__.Bar'>
```

또한 이 모든 object의 metaclass는 default로 `type`이다.

```python
>>> age.__class__.__class__
<type 'type'>
>>> name.__class__.__class__
<type 'type'>
>>> foo.__class__.__class__
<type 'type'>
>>> b.__class__.__class__
<type 'type'>
```

## __metaclass__

class를 만들 때 `__metaclass__` attribute를 넣으면 해당 class를 만들기 위해 이 attribute를 쓴다.

```python
class Foo(object):
  __metaclass__ = something...
  [...]
```

`__metaclass__`를 찾는 순서는 다음과 같다.
1. `class`에 `__metaclass__`가 존재한다면 가져다 쓴다.
2. 존재하지 않는다면 자기 모듈에서 찾는다.(아무것도 상속받지 않는 경우)
3. 부모 class중 첫번째 녀석의 `__metaclass__`를 가져다쓴다.(상속을 받는 경우)

### custom metaclass

모든 attribute가 Upper case인 class를 만들 때...

```python
def upper_attr(future_class_name, future_class_parents, future_class_attr):
  uppercase_attr = {}
  for name, val in future_class_attr.items():
      if not name.startswith('__'):
          uppercase_attr[name.upper()] = val
      else:
          uppercase_attr[name] = val
  return type(future_class_name, future_class_parents, uppercase_attr)

__metaclass__ = upper_attr # 이 녀석이 있어서 해당 파일의 모든 class가 이 녀석을 씀

class Foo(): 
  bar = 'bip'

print(hasattr(Foo, 'bar'))
# Out: False
print(hasattr(Foo, 'BAR'))
# Out: True

f = Foo()
print(f.BAR)
# Out: 'bip'
```

다음은 type을 상속받아 만드는 예제이다.

```python
class UpperAttrMetaclass(type): 
    # __new__: __init__전에 불리는 함수. object를 만들고 return한다.
    def __new__(cls, clsname, bases, dct):

        uppercase_attr = {}
        for name, val in dct.items():
            if not name.startswith('__'):
                uppercase_attr[name.upper()] = val
            else:
                uppercase_attr[name] = val

        return super(UpperAttrMetaclass, cls).__new__(cls, clsname, bases, uppercase_attr)
```

