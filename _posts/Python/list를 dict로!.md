```python
# 요런 걸 만들 수 있지..
a = ['a','b','c']

# 요래버리면 다들 같은 list를 보고있음!!
b = dict(zip(a, [[]]*len(a)))

print(b)
b['a'].append(1)
print(b)
print("-"*50)
# 이렇게 만들어야한다!!
b = dict((el, []) for el in a)

print(b)
b['a'].append(1)
print(b)
```

output :
> {'a': [], 'c': [], 'b': []}
{'a': [1], 'c': [1], 'b': [1]}
\--------------------------------------------------
{'a': [], 'c': [], 'b': []}
{'a': [1], 'c': [], 'b': []}