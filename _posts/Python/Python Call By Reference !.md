## Call By Reference!

** Python 함수에서 모든 argument는 call by reference로 불린다. **

다만 mutable object이냐, immutable object이냐에 따라서 call by value처럼 보이는 것 뿐이다.

### Immutable type

```python
def foo( a ):
    a = 2

b = 3
foo( b )
print b # 3
```
위의 코드처럼 immutable한 경우는 call by value처럼 동작한다.

|0x0001| 주소에 3이라는 값이 있었으면 처음에

b |0x0001|, a |0x0001|가 된다.

이제 a = 2를 하면 |0x0002|에 2라는 값이 생기고 a는 |0x0002|의 주소를 가리키게된다.

따라서 함수 밖에 나왔을 때 b에게는 영향이 없는 것이다.

다음 코드르 보면 확실히 알 수 있을 것이다.

```python
def foo( a ):
	print "Before operation, id(a) is ", id( a )
	a = 3
	print "After operation, id(a) is ", id( a )

b = 2
print "Before calling foo, id(b) is ", id( b )
foo( b )
print "After calling foo, id(b) is ", id( b )
```
### Mutable type

```python
def foo( a ):
    a.append( 2 )

b = [ 3,2,1]
foo(b)
print b # [3, 2, 1, 2]
```

List의 경우 mutable한 녀석이며 위의 foo는 list에 append를 하기 때문에 a와 b의 주소가 바뀌지 않으며 따라서 call by reference로 볼 수 있다.

### Conclusion!

결국, object의 reference를 바꾸지 않는 operation의 경우 call by reference이며, reference를 바꾸면 call by value!