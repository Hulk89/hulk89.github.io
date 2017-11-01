# Why?

cProfile로 프로파일링을 하다보면 분명히 느린 코드인데 더이상 최적화가 불가능할 때가 존재한다. 이 때, cython을 사용하여 c 코드로 만들어주면 더 빨라질 수 있지...

# 사용법

cython으로 모듈 컴파일하는 법을 순서대로 간략히 설명하고 하나씩 파보자!

* myCode.py -> myCode.pyx 로 복사한다.
* setup.py 설정
* `python setup.py build_ext --inplace` 로 컴파일
* myCode.so 복사

쓰고보니 setup.py만 어케 쓰는지 설명하면 되겠다.

```python
from distutils.core import setup
from Cython.Build import cythonize

setup(
    ext_modules = cythonize("myCode.pyx"),
)
```

[참고 문서](http://cython.readthedocs.io/en/latest/src/userguide/source_files_and_compilation.html)