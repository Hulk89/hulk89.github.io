---
layout: post
title:  "asyncio의 queue를 써보자"
date:   2018-08-07
category: "python"
tags: [python, asyncio, queue]
---

## asyncio의 queue를 써보자!

asyncio는 비동기 프로그래밍을 위한 라이브러리이다. 모든 것을 정리하자니 귀찮아서 샘플 코드 하나로 끝낸다.

* `asyncio.ensure_future`
  * `loop`에 schedule한다.
  * `coroutine`을 `future`로 감싸서 return한다.
* `queue.put` & `queue.get`
  * 둘 다 coroutine
* `asyncio.get_event_loop`
  * event loop을 가져온다.
* `loop.add_reader(fd, callback)`
  * fd의 변화를 감지해서 callback을 호출한다.
* `loop.run_until_complete(task or future)`
  * 인자로 들어온 task나 future가 끝나기 전까지 돈다.
  * 실제로는 `future.add_done_callback(_run_until_complete_cb)`를 호출하며,
  * `_run_until_complete_cb`는 `future._loop.stop()`을 호출한다.
  * 즉 future가 끝나면 future가 실행되고있는 loop을 stop한다.
  * [source code](https://github.com/python/cpython/blob/944451cd8d3e897138f4b43569de13cd081ee251/Lib/asyncio/base_events.py#L158)
* `loop.remove_reader(fd)`
  * fd 감시를 그만둔다. 

```python
import asyncio
import sys
from functools import partial

def handle_stdin(queue):
    data = sys.stdin.readline().strip()
    if data == 'q':
        loop = asyncio.get_event_loop()
        loop.remove_reader(sys.stdin)
    asyncio.ensure_future(queue.put(data)) 

async def tick(queue):
    stop = False
    while not stop:
        
        data = await queue.get()
        print('Data received: {}'.format(data))
        if data == 'q':
            stop = True
    print("tick finished.")

def main(): 
    queue = asyncio.Queue()
    loop = asyncio.get_event_loop()
    loop.add_reader(sys.stdin, partial(handle_stdin, queue))
    loop.run_until_complete(tick(queue))
    loop.close()

if __name__ == '__main__':
    main()
```