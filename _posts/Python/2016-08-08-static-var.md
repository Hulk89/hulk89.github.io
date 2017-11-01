---
layout: post
title:  "Python에 static variable을 만들어보자!"
date:   2016-08-08
category: "python"
tags: [python, static variable]
---

[stackoverflow에서 발췌](http://stackoverflow.com/questions/279561/what-is-the-python-equivalent-of-static-variables-inside-a-function)

다음과 같이 static int를 정의하고 쓰던 사람이 파이썬에 놀러와서 질문을 했다.

```c
void foo()
{
    static int counter = 0;
    counter++;
    printf("counter is %d\n", counter);
}
```

"나 C 쓰던 사람인데 이런거 Python으로 어떻게 하니?"

그러자 어떤 현인이 다음과 같은 방식을 제안했다.

```python
def foo():
    foo.counter += 1
    print "Counter is %d" % foo.counter
foo.counter = 0
```

요런 식으로 foo.counter를 초기화 시켜주고 쓰면 돼!

근데 저러면 초기화 안시키면 망하니까 `decorator`를 쓰렴!

```python
def static_vars(**kwargs):
    def decorate(func):
        for k in kwargs:
            setattr(func, k, kwargs[k])
        return func
    return decorate
```

요렇게 정의하구

```python
@static_vars(counter=0)
def foo():
    foo.counter += 1
    print "Counter is %d" % foo.counter
```

요렇게 쓰면 대!
진짜 되나 실험해보았다.

```python
>>> def static_vars(**kwargs):
...     def decorate(func):
...             for k in kwargs:
...                     print k
...                     print kwargs[k]
...                     setattr(func, k, kwargs[k])
...             return func
...     return decorate
... 
>>> @static_vars(counter=0)
... def foo():
...     foo.counter += 1
...     print "Counter is %d" % foo.counter
... 
counter
0
>>> foo()
Counter is 1
>>> foo()
Counter is 2
>>> foo()
Counter is 3
```

참고로 `setattr()`는 다음과 같은 형식이다.

## setattr(object, name, value)
getattr()과 반대되는 녀석. 인자로는 object, string, value가 된다. 이 string은 존재하는 attribute 또는 새로운 attribute의 이름이 된다. 
예시 > setattr(x, 'foobar', 123) == x.foobar = 123.

근데 같다고했는데 func.k = kwargs[k]하면 에러나네..