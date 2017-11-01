---
layout: post
title:  "Iterator & generator"
date:   2016-08-08
category: "python"
tags: [python, iterator, generator]
---

# Iterator
예제를 보자.

```python
>>> a = [0,1,2,3,4]
>>> b = iter(a)
>>> b
<listiterator object at 0x106984a50>
>>> b.next()
0
>>> b.next()
1
# ...
>>> b.next()
4
>>> b.next()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
>>> 
```

a라는 공간에 list를 생성하였고, a에는 0,1,2,3,4가 순차적으로 저장되어있다. b는 iter() 함수의 인자로 a를 주었다. 이제 b는 메모리 주소 `0x106984a50`안에 위치한다.

> 
  * iterator : b
  * iteration : iterator에서 순차적으로 요소를 가져오는 행위
  * iterable : iteration이 가능한 경우

*list*는 *iterable*한 객체이다. 이에 대해서 `iter(b)`로 해당 객체의 *iterator*를 얻어올 수 있다. 그 후 이 iterator의 `next()`를 호출해서 *iteration*이 가능하다. `next()`를 호출 시에 더이상 줄 값이 없다면 `StopIteration` Exception이 나게된다.

```python
>>> a = [0,1,2,3,4]
>>> for i in a:
...     print i,
... 
0 1 2 3 4
```

위의 코드는 이렇게도 쓸 수 있다.

```python
>>> a = [0,1,2,3,4]
>>> b = iter(a)
>>> 
>>> while True:
...     try:
...         i = b.next()
...     except StopIteration:
...         break
...     print i,
... 
0 1 2 3 4
```

# Generator
이것도 먼저 예제를 보자!

```python
>>> def num_generator(n):
...     print "Function Start"
...     while n < 6:
...             yield n
...             n += 1
...     print "Function End"
... 
>>> for i in num_generator(0):
...     print i
... 
Function Start
0
1
2
3
4
5
Function End
```

**Generator는 함수의 형태를 가진다.** 일반적으로 for문과 같이 사용된다. **yield를 사용하는 함수 == generator**라고 봐도 무방하다.

## 이거 언제쓰냐?

List Comprehension 구문은 python에서 자주 쓰이는 방법이다.
```python
a = [0,1,2,3,4]
b = [i**2 for i in a]

for j in b:
    ...
```
위의 코드를 보면 a를 이용해 list b를 만들고 이를 이용하여 어떤 조작을 하려고 한다. a, b가 모두 메모리를 차지한다. 작은 리스트는 괜찮지만 a가 엄청 많은 데이터를 가지고있을 때 비슷한 사이즈의 메모리를 차지하는 것은 별로 효율적이지 못하다. 이 때 generator를 쓰면 list a만 유지하며 각 iteration에 맞는 값을 제공할 수 있다.

* 장점
  * 한번 쓰고 버릴 리스트에 대한 메모리 절약
* 단점?
  * b가 앞으로도 계속 쓰일 것이라면 어떤 것이 비용이 더 클까? (나중에 조사해서 쓰자)

```python
>>> a = [0,1,2,3,4]
>>> 
>>> def genTest(l):
...     for i in a:
...             yield i**2
... 
>>> for j in genTest(a):
...     print j,
... 
0 1 4 9 16
```

## yield?

python에서 yield란 return과 비슷하나 좀 다르다. return처럼 yield를 만나는 순간 값을 리턴하나, **함수가 종료되지 않고 다음 호출 시 거기서부터 시작된다!**

이 예제 좋구만

```python
>>> def generator(n):
...     print "Generator start!"
...     while n < 6:
...             print "Generator : I give control to the Main"
...             yield n
...             print "Generator : I received a control."
...             n += 1
...             print "Generator : n += 1"
...     print "Generator End!"
... 
>>> for i in generator(0):
...     print "Main: I received a control."
...     print i
...     print "Main: I give control to Generator"
... 
Generator start!
Generator : I give control to the Main
Main: I received a control.
0
Main: I give control to Generator
Generator : I received a control.
Generator : n += 1
Generator : I give control to the Main
Main: I received a control.
1
.........
5
Main: I give control to Generator
Generator : I received a control.
Generator : n += 1
Generator End!
```

`for문`을 맨 위에서 봤던 `동치 while문`으로 바꿔 생각하면 쉽게 이해할 수 있다.

> while문에서 `b.next()`를 호출할 때 generator가 호출된다