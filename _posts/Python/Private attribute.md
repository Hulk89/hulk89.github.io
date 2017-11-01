## Python은 private variable이 존재하지 않는다.

그치만 private가 좋잖아... pep8에서는 변수 앞에 `__`가 붙으면 private라 생각하자고 명시했다. 또한 저렇게 명시한 경우 바로 접근이 불가능 하다. 다음 코드를 보자

```python
class Foo(object):
    def __init__(self):
    self.__pVar = 1

a = Foo()
a.__pVar
## Traceback (most recent call last):
##   File "<stdin>", line 1, in <module>
## AttributeError: 'Bar' object has no attribute '__baz'

# 그럼 어디에 있을까?
a.__dict__
{'_Foo__pVar': 1}
a._Foo__pVar
## 1
```

그러니까 그냥 `__`붙이면 된다.


근데 한가지 엄청난 이슈가 있다. **이 녀석은 `상속`시에 못써먹는다는 것이다.**
위에서 보면 알 수 있듯이 `_<class>__<var>`형태인데, `class Bar(Foo):`로 상속을 하더라도, 저녀석은 `_Foo__pVar` 형태를 띄고있다. 따라서 child에서 `self.__pVar`로 호출하려고하면 `self._Bar__pVar`를 검색하고 없다고 에러가 난다.

그래서 `약한 internal use`를 말하기 위해 `_`를 하나만 붙인다. 이 경우 어떠한 특별한 사용법도 없다.
다만 `from M import *` 구문 사용 시 import 되지 않는다고한다.