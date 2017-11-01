`ijson`은 json 파일이 클 경우에 유용한 모듈이다. 보통의 json load의 경우 한번에 메모리에 올라가서 큰 파일은 오랜 시간이 걸리는데 비해, `ijson`은 stream으로 처리해서 훨씬 빠르고 적은 메모리로 동작한다.
다음은 `ijson`과 `json`의 코드 snippet이다.

```python
import json

with open(jsonFileName) as f:
  A = json.loads(f.read())
  for i in A:
    print(AA)
```

```python
import ijson
for prefix, theType, value in ijson.parse(open(jsonFileName)):
    print prefix, theType, value
```