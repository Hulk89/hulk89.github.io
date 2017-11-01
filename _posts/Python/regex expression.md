## 반복 메타 문자
| 메타 문자 | 의미 |
|:-----:|:----|
|*   |0회 이상 반복|
|+   |1회 이상 반복|
|?   |0회 or 1회|
|{m} |m회 반복|
|{m, n} |m회부터 n회까지 반복|


##매칭 메타 문자
|메타 문자|의미|
|:--:|:--|
|.|줄바꿈 문자를 제외한 모든 문자와 매치됨|
|^|문자열의 시작과 매치됨|
|$|문자열의 마지막과 매치됨|
|[ ]|문자 집합 중 한 문자를 의미|
| I |또는(or)를 의미|
|{ }|정규식을 그룹으로 묶음|

## 자주 사용하는 함수들
자주 사용하는 녀석들은 다음과 같은 것들이 있다.
* search
* match
* findall
* sub
* split
* compile

각 사용법은 [여기](http://egloos.zum.com/sweeper/v/3065126)를 참조하자!