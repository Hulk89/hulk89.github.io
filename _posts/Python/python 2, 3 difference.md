여기서는 2용 코드를 3로 포팅하면서 생긴 문제점과 해결 방법을 적는다.

## Encoding

python 2의 기본 인코딩은 `ascii`이기 때문에 `utf8`로 변환하기 위해 다음과 같은 코드가 필요했다.

```python
# coding: utf-8

import sys
if sys.version_info[0] == 2: # 2,3 동시 적용을 위해...
    reload(sys)
    sys.setdefaultencoding('utf8')
```
그러나 python 3 에서는 기본 인코딩이 `utf8`이기 때문에 위의 내용이 필요 없으며, 2, 3사이의 호환을 위해 위 코드를 삽입할 수 있다.

## xrange 제거?!

python 3의 range는 python 2의 xrange와 비슷한 로직으로 변경되었으며 따라서 **xrange가 존재하지 않는다.**
최적화가 필요없는 코드라면 xrange->range로 바꾸고, 필요하다면 
xrange를 range로 바꾸는 분기가 필요하겠다.

## import path 변경

이거는 좀 더 자세히 알아보고 쓰자...

### 해당 스크립트의 상위 폴더를 path에 넣는 방법

다음과 같은 구조에서 `HulkEquationConverter.py`에서 상위 폴더인 `hulkMathUtil`의 `hulkGraph`를 가져오고 싶을 때
```
├── hulkAppKeyChecker.py
├── hulkFlaskFunctions
│   ├── HulkEquationConverter.py
│   ├── __init__.py
│   └── ...
├── hulkMathServer.py
├── hulkMathUtil
│   ├── __init__.py
│   ├── hulkGraph.py
│   └── ...
├── hulkUnittest.py
└── ...
```

요렇게 쓰면 된다.

```python
import sys, os
sys.path.append( os.path.dirname(os.path.abspath(os.path.dirname(__file__))) )
from hulkMathUtil.hulkGraph import *
```

## print가 함수로!

이건 너무 잘 아는 것이니 PASS!!

## str, unicode?!

python 2의 `unicode`가 python 3의 `str`이다 라고 생각하면 된다.
python 2에서 unicode와 str는 +로 append가 가능하나
python 3에서 str과 bytes는 불가능하다.

```python
with open(fileName + '.png', 'rb') as f:
    encoded_string = base64.b64encode(f.read())
os.remove(fileName + '.png')

if sys.version_info[0] == 2:
    return {"Result":    True,
            "ErrorMsg":  None,
            "Image":     encoded_string}
else:
    return {"Result":    True,
            "ErrorMsg":  None,
            "Image":     encoded_string.decode("ascii")}
```

## exec 함수의 변화!
http://stackoverflow.com/questions/15086040/behavior-of-exec-function-in-python-2-and-python-3
나중에 이거보고 정리하자!