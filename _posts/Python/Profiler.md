## TOC
* cProfile
* memory profiler
---
## cProfile

### 간단 사용 설명서

요거 돌리면 된다.
`python -m cProfile -s tottime <myScript> <argumentList> > output`

### options

`python -m cProfile [-s option]<myScript.py> > output` 저기에 들어가는게 option!

| options | Description |
|:--:|:--:|
|'calls' |call count|
|'cumulative' |cumulative time|
|'cumtime' |cumulative time|
|'file' |file name|
|'filename' |file name|
|'module' |file name|
|'ncalls' |call count|
|'pcalls' |primitive call count|
|'line' |line number|
|'name' |function name|
|'nfl' |name/file/line|
|'stdname' |standard name|
|'time' |internal time|
|'tottime' |internal time|

## memory profiler

### 설치

```bash
pip install memory_profiler
pip install psutil
```
`psutil`은 `memory_profiler`의 성능 향상을 위해 설치한다고 한다.

### 간단 사용

내가 프로파일링하고싶은 함수 위에 `@profile` 데코레이터를 넣는다.
그 후
`python -m memory_profiler <myScript> <argumentList>`
를 실행하면 그 함수의 라인 별로 memory usage가 나오는데 20배정도 느려지니 감안하고 기다리길...