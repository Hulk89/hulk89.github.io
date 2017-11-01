## Lambda

`진짜진짜 간단한` 함수를 만들때만 쓰자.
형식은 다음과 같다
`lambda args : expression`
코드를 살펴보면 다음과 같다.
```python
>>> f = lambda x, y: x + y
>>> f(1, 1)
2
```

## map() function

람다 함수는 `map()`과 쓰일 때 진가를 발휘한다.
`map()`의 형식은 다음과 같다.
`r = map(func, seq)`
처음 `func`는 함수며, 뒤의 seq는 list같이 iterable한 녀석이다.
> 아웃풋은 2, 3가 다르다. 2의 경우 `list`로 나오며, 3의 경우 `map object`로 나온다.
따라서 list라고 가정하면 안되며 iteration을 돌 때 쓰도록한다.

### python3 코드
```python
>>> Celsius = [39.2, 36.5, 37.3, 37.8]
>>> Fahrenheit = map(lambda x: (float(9)/5)*x + 32, Celsius)
>>> Fahrenheit
<map object at 0x10d721c18>
>>> Fahrenheit[0]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'map' object is not subscriptable
>>> next(Fahrenheit)
102.56
>>> next(Fahrenheit)
97.7
>>> next(Fahrenheit)
99.14
>>> next(Fahrenheit)
100.03999999999999
>>> next(Fahrenheit)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

또한 두 개 이상의 `list`를 받을 수 있다.
```python
>>> a = [1, 2, 3, 4]
>>> b = [3, 4, 5, 6]
>>> r = map(lambda x,y: x+y, a, b)
>>> for item in r:
...     print(item)
... 
4
6
8
10
```
## filter() function

뭐 이름만 봐도 알겠지.

```python
>>> fibonacci = [0,1,1,2,3,5,8,13,21,34,55]
>>> odd_numbers = list(filter(lambda x: x % 2, fibonacci))
>>> print(odd_numbers)
[1, 1, 3, 5, 13, 21, 55]
```
## reduce() function

reduce함수는 iterable object에 function을 계속해서 apply하고 하나의 값을 리턴한다.
`reduce(func, iterable)`형식이다.
```python
>>> from functools import reduce
>>> reduce(lambda a,b: a if (a>b) else b, [47,11,42,102,13])
102
```