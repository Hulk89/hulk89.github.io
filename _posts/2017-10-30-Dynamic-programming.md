---
layout: post
title:  "Dynamic programming"
date:   2017-10-30 
category: "Algorithm"
tags: [algorithm, python]
---

# 언제 적용이 가능한가?

* Optimal substructure(가능성 증명)
  * optimal solution of subproblem is in optimal solution of problem.
* recursive하게 구현했을 때, 중복호출이 일어난다.(효율성 증명)

# 예시

* 피보나치 수열 구하기
* 행렬 경로문제

이 중에서 피보나치는 너무 쉬우니 `행렬 경로문제`를 풀어보자!

## 행렬 경로문제

### 문제 정의

문제는 다음과 같이 정의된다.

* 양의 숫자가 쓰여진 nxn행렬이 존재.
* 행렬의 좌상단에서 시작해서 우측 하단에 도착하는 최대(최소) 스코어를 구하는 문제.
* 우측, 하단으로만 이동 가능하다.

> 만약 좌측, 상단으로 이동가능하면 안된다. 이는 나중에 고민해보자

이 문제는 DP로 풀 수 있다. 증명해보자

### Optimal substructure 증명

`(0, 0)~>(i, j)`까지의 최적 해를 `S[i][j]`라 하자.
이 때 `S[i][j]`에 포함된 임의의 경로 `(0, 0)~>(x, y) (x<=i, y <=j, x+y < i+j)`가 `S[x][y]`보다 작은, 즉 최적해가 아니라고 가정해보자.
이렇게 가정했을 때 모순이 있음을 밝히면, S[i][j]는 모든 subproblem의 최적해를 가진다고 볼 수 있다.

모순점) (x, y)까지의 경로가 최적해가 아니라면, S[x][y]로 바꾸면 (i, j)의 경로에 대해 더 큰 최적의 해를 가질 수 있으므로 모순된다.
따라서 Optimal substructure임을 알 수 있다.

### recursive하게 구현했을 때, 중복호출이 일어나는가?

* S[i][j] = max(S[i-1][j], S[i][j-1]) + m[i][j]
* S[i-1][j] = max(S[i-2][j], `S[i-1][j-1]`) + m[i-1][j]
* S[i][j-1] = max(`S[i-1][j-1]`, S[i][j-2]) + m[i][j-1]

즉 중복호출이 일어남을 알 수 있고, 따라서 DP를 적용하면 효율이 좋아진다.

이제 DP로 풀어보자.

### 풀기
코드를 쓰자

```python
import numpy as np

def getHighestScore(mat, scores, endTup):
    x, y = endTup
    if scores.item(x, y) != 0: # score가 존재한다면 return
        return scores.item(x, y)
    else:
        score = max(getHighestScore(mat, scores, (x-1, y)),
                    getHighestScore(mat, scores, (x, y-1))) + mat.item(x,y)
        scores.itemset((x, y), score)
        return score


def initializeScoreMat(mat):
    rows, cols = mat.shape[:2]
    scoreMat = np.zeros((rows, cols), dtype=np.uint8)
    scoreMat.itemset((0, 0), mat.item(0, 0))
    for i in xrange(1, rows):
        score = mat.item(i, 0) + scoreMat.item(i-1, 0)
        scoreMat.itemset((i, 0), score)
    for i in xrange(1, cols):
        score = mat.item(0, i) + scoreMat.item(0, i-1)
        scoreMat.itemset((0, i), score)
    return scoreMat


if __name__ == '__main__':
    testList = [[6, 7, 12,  5],
                [5, 3, 11, 18],
                [7, 17, 3,  3],
                [8, 10, 14, 9]]
    testMat = np.asarray(testList, dtype=np.uint8)

    scoreMat = initializeScoreMat(testMat)
    score = getHighestScore(testMat, scoreMat, (3, 3))

    print("score")
    print(score)
```

## 조약돌 놓기 문제
### 문제 정의

* n*3 테이블의 각 칸에 숫자가 쓰여있다.
* 가로나 세로로 인접한 두 칸에 조약돌이 놓일 수 없다.
* 각 row에는 하나 이상의 조약돌을 놓는다.

이 때 한 행에 놓을 수 있는 패턴은 다음 네가지이다.

|# |pattern|
|:--:|:--:|
|1|100|
|2|010|
|3|001|
|4|101|

### 최대 점수 정의

이 문제에서는 Optimal substructure를 증명하기 전에 최대 점수를 먼저 정의하도록 한다.

$m_{i,1}$ : $i$번째 행의 패턴 1일 때의 점수.
$s_{i,j}$ : $1$행부터 $i$행까지 돌을 놓는데, $i$번째 돌을 패턴$j$로 놓았을 때의 최대 점수.
$s_i = max_j(s_{i,j})$ : $1$행부터 $i$행까지의 최대 점수.
우리가 구하고 싶은 최대 점수는 $s_i$이다.

### Optimal Substructure 증명

먼저 다음과 같이 정의한다.
* $s_i$  : i까지의 최적해, 
* $s'_i$ : i까지의 최적해가 아닌 어떤 해

$s_i$를 구하는 것은 모든 패턴에 대해 $s_{i,j}$를 구하는 것과 동치이다. (max 한번만 취하면 되니까)
$s_{i,1}$에 대해서 살펴보면 i번째 패턴이 `100`이므로 i-1번째 패턴은 `010`, `001` 두가지가 올 수 있다. 

즉 $$s_{i,1} = max(s'_{i-1,2}, s'_{i-1,3}) + m_{i,1}$$ (둘 중 하나만 최적해가 아니라도 되지만...)

만약 이 중에 $$s'_{i-1,2}$$가 선택되었고 이는 사실 최적 해가 아니라고 해보자.
그러면 당연히 $$s'_{i-1,2}$$를 $$s_{i-1,2}$$으로 바꾸면 해가 커지므로 모순이 생긴다.(3을 선택해도 똑같이 증명가능)
따라서 증명 끝!

### recursive하게 호출할 때 중복이 일어나는가?

나머지에 대해서 똑같이 적용하면

* $s_{i,1} = max(s_{i-1,2}, s_{i-1,3}) + m_{i,1}$
* $s_{i,2} = max(s_{i-1,1}, s_{i-1,3}, s_{i-1,4}) + m_{i,2}$
* $s_{i,3} = max(s_{i-1,1}, s_{i-1,2}) + m_{i,3}$
* $s_{i,4} = s_{i-1,2} + m_{i,4}$

이다.
따라서 중복호출!
