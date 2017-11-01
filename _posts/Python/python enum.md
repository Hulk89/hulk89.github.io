## Enum
파이썬에는 enum이 없다.
그래서 누가 만든 코드다.

```python
def enum(*sequential, **named):
    enums = dict(zip(sequential, range(len(sequential))), **named)
    reverse = dict((value, key) for key, value in enums.iteritems())
    enums['reverse_mapping'] = reverse
    return type('Enum', (), enums)
```

> `iteritems`는 python 2에서 iterator를 돌려주는 것으로 python3에서는 `items`를 쓰면 같다.

## 사용법

```python
>>> Numbers = enum('ZERO', 'ONE', 'TWO')
>>> Numbers.ONE
1
>>> Numbers.TWO
2
>>> Numbers.reverse_mapping[1]
'ONE'
```

### 참고자료
#### dict 사용법
```python
dict(**kwarg)
dict(mapping, **kwarg)
dict(iterable, **kwarg)
```