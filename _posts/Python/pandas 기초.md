먼저 import 부터!

```python
%matplotlib inline
import pandas as pd
import matplotlib
import matplotlib.pyplot as plt
```

이제 csv형식의 파일을 읽어들이기 위해 `pandas.read_csv()`를 쓰고 info를 출력해보자

```python
data = pd.read_csv('convenient_store.csv')
data.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 177 entries, 0 to 176
    Data columns (total 7 columns):
    area           177 non-null object
    company        177 non-null object
    hourly_wage    177 non-null int64
    area1          177 non-null object
    area2          177 non-null object
    outlier        177 non-null int64
    name           177 non-null object
    dtypes: int64(2), object(5)
    memory usage: 9.8+ KB


pd.read_csv()라는 함수를 써서 csv파일을 읽어오면 pandas의 DataFrame으로 형태가 변환

```python
data[0:10]# 데이터를 찍어보자 전체를 찍으려면 []부분을 없애면 된다.
```

area company hourly\_wage area1 area2 outlier name     0 강남구 삼성동 gs25 오크우드점 5600 강남구 삼성동 0 gs25   1 강남구 삼성동 gs25 코엑스점 5700 강남구 삼성동 0 gs25   2 강서구 화곡동 gs25편의점 5600 강서구 화곡동 0 gs25   3 광진구 군자동 gs25 5580 광진구 군자동 0 gs25   4 광진구 중곡동 gs25중곡대원점 5580 광진구 중곡동 0 gs25   5 구로구 구로동 gs25구로동양점 6000 구로구 구로동 0 gs25   6 구로구 구로동 gs25구로동양점 5580 구로구 구로동 0 gs25   7 동대문구 장안동 gs25장안중앙점 5600 동대문구 장안동 0 gs25   8 마포구 서교동 gs25 홍대아트점 5600 마포구 서교동 0 gs25   9 성동구 금호동4 gs25 서울숲푸르지오점 6000 성동구 금호동4 0 gs25

```python
data.area[0:10] # 관심있는 컬럼만 찍어보자
# data[0:10].area # 요것도 똑같은 방법..
```

0 강남구 삼성동

1 강남구 삼성동

2 강서구 화곡동

3 광진구 군자동

4 광진구 중곡동

5 구로구 구로동

6 구로구 구로동

7 동대문구 장안동

8 마포구 서교동

9 성동구 금호동4

Name: area, dtype: object

```python
data.describe() # int로 구성된 컬럼데이터의 수치를 뽑아준다.
```

hourly\_wage outlier     count 177.000000 177.0   mean 5787.627119 0.0   std 352.318646 0.0   min 5580.000000 0.0   25% 5580.000000 0.0   50% 5600.000000 0.0   75% 6000.000000 0.0   max 7500.000000 0.0

```python
data.hourly_wage.describe() # 하나만 보고싶으면 이렇게...
```

    count     177.000000
    mean     5787.627119
    std       352.318646
    min      5580.000000
    25%      5580.000000
    50%      5600.000000
    75%      6000.000000
    max      7500.000000
    Name: hourly_wage, dtype: float64

```python
data[data.hourly_wage > 6500] # 필터를 걸어서 보자!
```

area company hourly\_wage area1 area2 outlier name     29 강남구 논현동 세븐일레븐편의점 7500 강남구 논현동 0 7/11   37 강서구 등촌동 세븐일레븐 서울호서대점 6600 강서구 등촌동 0 7/11   135 중구 명동2가 세븐일레븐 6690 중구 명동2가 0 7/11   160 서대문구 신촌동 BGF Retail CU 신촌세브란스병원점 6900 서대문구 신촌동 0 CU   172 용산구 한남동 CU 한남리첸시아점 6600 용산구 한남동 0 CU   173 용산구 이태원동 CU 이태원프리덤점 7500 용산구 이태원동 0 CU

```python
data[(data.area1 == '마포구') & (data.hourly_wage > 6000)] # &,|로 여러 필터를 넣을 수 있다.
```

area company hourly\_wage area1 area2 outlier name     72 마포구 동교동 세븐일레븐 마포홍익점 6500 마포구 동교동 0 7/11

```python
cu = data[data.company.str.contains('CU')] # string 검색은 요렇게
cu[0:5]
```

area company hourly\_wage area1 area2 outlier name Seoul LT6000     141 강남구 논현동 CU 논현힐탑점 5600 강남구 논현동 0 CU in seoul 0   142 강남구 논현동 CU논현한미점 6000 강남구 논현동 0 CU in seoul 0   143 강남구 신사동 CU 로데오점 6100 강남구 신사동 0 CU in seoul 1   144 강남구 대치4동 CU 대치본점 6000 강남구 대치4동 0 CU in seoul 0   145 강남구 논현1동 CUBE pc방 6500 강남구 논현1동 0 CU in seoul 1

---

```python
data['Seoul'] = 'in seoul' # seoul field를 추가하고 in seoul 문자열을 모두 넣음
data[0:5]
```

area company hourly\_wage area1 area2 outlier name Seoul     0 강남구 삼성동 gs25 오크우드점 5600 강남구 삼성동 0 gs25 in seoul   1 강남구 삼성동 gs25 코엑스점 5700 강남구 삼성동 0 gs25 in seoul   2 강서구 화곡동 gs25편의점 5600 강서구 화곡동 0 gs25 in seoul   3 광진구 군자동 gs25 5580 광진구 군자동 0 gs25 in seoul   4 광진구 중곡동 gs25중곡대원점 5580 광진구 중곡동 0 gs25 in seoul  

6000원 이상인 곳에서 임금에 대한 통계 내기
--------------------------

물론 위에서 한 것 처럼 필터를 걸어서 할 수도 있지만, 커스텀 함수를 만들어서 해보자. ~~람다도 오랜만에...~~

```python
def larger_than_6000(x): # 커스텀 함수를 하나 만들고..
  if x > 6000:
    return 1
  else:
    return 0
```



```python
data['LT6000'] = data.hourly_wage.map(lambda x: larger_than_6000(x))
data[data['LT6000'] == 1].describe()
```

hourly\_wage outlier LT6000     count 25.000000 25.0 25.0   mean 6518.000000 0.0 1.0   std 343.923441 0.0 0.0   min 6100.000000 0.0 1.0   25% 6300.000000 0.0 1.0   50% 6500.000000 0.0 1.0   75% 6500.000000 0.0 1.0   max 7500.000000 0.0 1.0  

새로운 데이터 가공
----------

구, 임금 컬럼을 6000원 이상인 곳만 추려서 새로 가공해보자

```python
data2 = data[data.LT6000 == 1][['area1', 'hourly_wage']]
data2[0:5]
```

area1 hourly\_wage     13 영등포구 6300   18 강남구 6200   29 강남구 7500   30 강남구 6500   37 강서구 6600

```python
data2.to_csv('data2.csv', index=False) # 저장
```

```python
data.hourly_wage.hist(bins=20)
```

![output_19_1.png](resources/E4A6F7F2F6F2F786B3A66DBBAD7341EF.png)

```python
data.boxplot(column='hourly_wage', by='name')
```

![output_20_1.png](resources/78966E6A0AB7788A196403A5147A1BE1.png)

```python
matplotlib.rc('font',family='AppleGothic')
data.boxplot(column='hourly_wage', by=['area1', 'name'], vert=False, figsize=(15,15))
```

![output_21_1.png](resources/A91945F888A5D623BA77DEA82E00E853.png)

