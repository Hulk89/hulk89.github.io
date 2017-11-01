# 예제

```python
import traceback
try:
    1/0
except:
    print(traceback.format_exc())
```

```python
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
ZeroDivisionError: division by zero
```

# 다른 예제

```python
import sys
def printException():
    message = []
    for e in sys.exc_info():
        message.append(str(e))
    message = '\n'.join(message)
    print message
    return message

try:
    raise "Error"
except:
    printException()
```

```python
<type 'exceptions.TypeError'>
exceptions must be old-style classes or derived from BaseException, not str
<traceback object at 0x03C3C8A0>
```