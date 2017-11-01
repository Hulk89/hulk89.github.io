## Garbage Collection with Reference Counter

객체들은 Reference counter를 가지고 카운터가 0이되면 환원한다.

## RefCount 확인법

```python
import sys
z=500
x=z
y=z
sys.getrefcount(z)
#4
```

z의 reference count가 4이다. 3일 것 같은데? 
`getrefcount`가 z객체를 처리하기 위해 추가로 1이 생긴다고한다.
따라서 3이라고 봐야한다.

## Weak Reference

refcount 증가 없이 특정 개체 참조가능
메모리를 많이 쓰나 필수적이진 않은 녀석들 (ex: 이미지 캐싱)

`weakref.ref(obj, callback=None)` : 해당 객체의 weakref를 생성 후 반환. 참조 객체가 없으면 None 반환

```python
cache = None

def get_image():
    if cache is None:
        image = Image(file('image.png', 'rb').read())
        cache = weakref.ref(image)
    return cache()
    
show(get_image())

....

show(get_image())
```