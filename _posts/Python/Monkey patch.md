## Monkey patch란?

런타임 시 실시간으로 class 등을 교체.

```python
import os
#랑 동치는
os = __import__('os')
```

추가가 필요함...