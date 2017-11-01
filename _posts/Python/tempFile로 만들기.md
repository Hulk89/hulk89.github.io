##tempFile로 저장하고 읽고 지우기

간혹 코딩을 하다보면 하드에 잠시 저장해야할 경우가 생긴다. 이 때 `tempfile` module을 사용하여 잠시동안 쓸 녀석을 만들어줄 수 있다. 바로 필요가 없어진다면 `os.remove`로 지울 수 있다.

```python

import os
import tempfile
import base64

fileName = tempfile.mktemp()
allGraph.save(fileName)

with open(fileName + '.png', 'rb') as f:
    encoded_string = base64.b64encode(f.read())
os.remove(fileName + '.png')
```