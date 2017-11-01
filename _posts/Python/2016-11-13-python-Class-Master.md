---
layout: post
title:  "Python Class Master"
date:   2016-11-13
category: "python"
tags: [python, class]
---

[원문](http://stackoverflow.com/questions/100003/what-is-a-metaclass-in-python)

# Class

python에서는 class 또한 object이다. 아래는 다른 언어와 비슷한 용도로 class를 쓰는 방법이다.

```python
class ObjectCreator(object):
    pass
  
my_object = ObjectCreator()

print(my_object)
# <__main__.ObjectCreator object at 0x8974f2c>
```

위처럼 `class`는 object를 생성할 수 있는 녀석이지만 말했듯이 `object`이기 때문에 다음과 같은 것이 가능하다

* variable을 assign 가능
* copy가 가능
* attribute를 추가할 수 있음
* function의 parameter로 넘길 수 있음

```python
### class또한 object이기 때문에 print  가능! ###
print(ObjectCreator) 
#<class '__main__.ObjectCreator'>
def echo(o):
    print(o)
    
### 함수의 파라미터로 넘길 수도 있다. ###
echo(ObjectCreator) 
# <class '__main__.ObjectCreator'>

### 새로운 attribute를 추가할 수 있다. ###
print(hasattr(ObjectCreator, 'new_attribute'))
# False
ObjectCreator.new_attribute = 'foo' 
print(hasattr(ObjectCreator, 'new_attribute'))
# True
print(ObjectCreator.new_attribute)
# foo

### class를 variable에 assign 가능하다. ###
ObjectCreatorMirror = ObjectCreator
print(ObjectCreatorMirror.new_attribute)
# foo
print(ObjectCreatorMirror())
# <__main__.ObjectCreator object at 0x8997b4c>
```

# Creating classes dynamically
class가 object이기 때문에 다이나믹하게 class를 만들 수 있다.
다음은 예제코드이다

```python
def choose_class(name):
    if name == 'foo':
        class Foo(object):
            pass
        return Foo # instance가 아닌 class를 return
    else:
        class Bar(object):
            pass
        return Bar
    
MyClass = choose_class('foo') 
print(MyClass) # 함수가 instance가 아닌 class를 리턴한다.
#<class '__main__.Foo'>
print(MyClass()) # return받은 class로 instance를 만들 수 있다.
#<__main__.Foo object at 0x89c6d4c>
```

예제를 보면 유기적으로 class를 만들지만 좀 복잡해보인다. `class 선언문`을 그대로 집어넣었는데 이를 바꿔볼 순 없나??

그래서 python에서는 `type` 함수를 지원한다.이 함수는 용법이 다음과 같다.
```
type(만들 class의 이름, --(1)
     부모 class들의 tuple, --(2) 
     attribute를 위한 dictionary) --(3)
```
(1) : 정확히 얘기하자면 만들 class.__name__에 들어갈 속성을 의미한다. 보통 class 이름과 같이 쓰고 헷갈리지 않기 위해 이를 지켜주자.

(2) : parent class들의 tuple인데 빈 tuple이 될 수도 있다.

(3) : attribute가 들어간다.

> 사실 python에 type 함수는 어떤 object의 type을 알아보기 위해서도 쓰는데 역할이 다른 하나의 함수는 python에서 보기 힘들다. 이는 예전 호환성을 지키기 위함이고 다른 function이라 생각하자.

`type` 함수를 이용하여 class를 만들어보자!

```python
class Foo(object):
    bar = True

class FooChild(Foo):
    pass

#위와 같은 것은

Foo = type('Foo', (), {'bar':True})
FooChild = type('FooChild', (Foo,), {})

print(FooChild.bar)
# True
```

## 생성 시 attribute 넣기
위의 class에 attribute를 넣을 수 있다.

```python
def echo_bar(self):
    print(self.bar)
FooChild = type('FooChild', (Foo,), {'echo_bar': echo_bar})
hasattr(Foo, 'echo_bar')
# False
hasattr(FooChild, 'echo_bar')
# True
my_foo = FooChild()
my_foo.echo_bar()
# True
```

## 이미 존재하는 class에 attribute를 추가하기

```python
def echo_bar_more(self):
    print('yet another method')

FooChild.echo_bar_more = echo_bar_more
hasattr(FooChild, 'echo_bar_more')
# True
```