---
layout: post
title:  "class attribute v.s. instance attribute"
date:   2016-10-27
category: "python"
tags: [python, attribute, class]
---

## class attribute V.S. instance attribute

```python
class Service(object):
    data = []

    def __init__(self, other_data):
        self.other_data = other_data
    ...
```

### class attribute
위의 `data`는 `class attirubte`이며, `[]`로 초기화 된 것이다.

### 동작 방식

```python
class MyClass(object):
    class_var = 1

    def __init__(self, i_var):
        self.i_var = i_var
```

위의 클래스를 가정하고 다음을 실행해보자

```python
foo = MyClass(2)
bar = MyClass(3)

foo.class_var, foo.i_var
## 1, 2
bar.class_var, bar.i_var
## 1, 3
MyClass.class_var ## <— This is key
## 1

MyClass.__dict__
'''
dict_proxy({'__module__': '__main__', 
            '__doc__': None, 
            '__dict__': <attribute '__dict__' of 'MyClass' objects>, 
            '__weakref__': <attribute '__weakref__' of 'MyClass' objects>, 
            'class_var': 2, 
            '__init__': <function __init__ at 0x10895e398>})
'''
```

위에서 foo.class_var를 찾으면 
  * `instance attribute`에서 찾는다. 
  * 있으면 return
  * 없으면 `class attribute`에서 찾는다.
  * 있으면 return
  * 없으면 `throw error`

```python
foo = MyClass(2)
foo.class_var
## 1
MyClass.class_var = 2
foo.class_var
## 2
```

class attribute를 바꾸면 instance에서 접근해도 모두 바뀐다.

```python
foo = MyClass(2)
foo.class_var
## 1
foo.class_var = 2
foo.class_var
## 2
MyClass.class_var
## 1
```

instance에서 `immutable한` class attribute를 바꾸려고하면 `해당 instance만` 이 attribute를 override한다.

```python
class Service(object):
    data = []

    def __init__(self, other_data):
        self.other_data = other_data
    ...

s1 = Service(['a', 'b'])
s2 = Service(['c', 'd'])

s1.data.append(1)

s1.data
## [1]
s2.data
## [1]

s2.data.append(2)

s1.data
## [1, 2]
s2.data
## [1, 2]
```

`mutable`한 class attribute의 경우에는 위와 같이 instance가 override를 하지 않으므로 안쓰는 것이 좋다.

## 그럼 class attribute는 언제 씀?
### constant 저장

```python
class Circle(object):
    pi = 3.14159

    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return Circle.pi * self.radius * self.radius

Circle.pi
## 3.14159

c = Circle(10)
c.pi
## 3.14159
c.area()
## 314.159
```

### default value 저장
예를 들면, Queue를 만드는데 최대 갯수를 기본 10개로 한다던가...

### class의 모든 instance의 정보 조회!
아까 말한 단점인데 패턴으로 쓰면 좋다.

```python
class Person(object):
    all_names = []

    def __init__(self, name):
        self.name = name
        Person.all_names.append(name)

joe = Person('Joe')
bob = Person('Bob')
print Person.all_names
## ['Joe', 'Bob']
```