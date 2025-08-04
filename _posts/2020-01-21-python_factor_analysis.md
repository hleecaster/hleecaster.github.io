---
title: 파이썬으로 탐색적 요인분석, 신뢰도 분석하기
date: 2020-01-21
categories: [WORK, Data]
tags: [데이터분석, Python, 심리학]
---

그동안 심리학을 비롯한 사회과학 분야에서 연구할 때 가장 많이 쓰던 통계 프로그램은 SPSS였다.

최근에 회사에서 심리검사 결과를 가지고 이게 잘 만들어진 검사인지 검증하기 위한 통계분석, 구체적으로는 탐색적 요인분석(EFA: Exploratory Factor Analysis)과 신뢰도 계수(Cronbach’s alpha)를 확인하는 작업을 할 일이 있었는데, 난 SPSS가 없어서 파이썬으로 해결했다.

코드 예시를 간략히 메모 차원에서 남겨봐야겠다.

## 준비

파이썬으로 데이터 분석 좀 해본 사람들이라면 numpy, pandas, matplotlib, seaborn까지는 대부분 설치했을 거다. 아, 그리고 jupyter notebook도.

이번에는 ‘요인분석’을 위한 패키지 [factor-analyzer](https://pypi.org/project/factor-analyzer/)까지 설치해주자.

설치 방법은 늘상 그렇듯.

```
pip install factor-analyzer
```

## 분석 코드 예시

### 데이터 살펴보기

일단 필요한 라이브러리 불러오고, 엑셀로 저장된 파일을 pandas 데이터프레임 형태로 불러왔다.

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

df = pd.read_excel("data.xlsx")
```


### 문항 리스트 준비하기

각 문항들은 모두 1~5점 리커트 척도로 구성되었다.

분석에 앞서 문항들을 내가 애초에 설계한 요인 구조에 따라 좀 묶어놓을 필요가 있다.

일단 열(컬럼) 이름을 뽑아보면

```python
df.columns
```

```
Index(['respondent-id', 'sex', 'a-1', 'b-1', 'c-1', 'd-1', 'e-1', 'f-1', 'g-1', 'h-1', 'a-2', 'b-2', 'c-2', 'd-2', 'e-2', 'f-2', 'g-2', 'h-2', 'a-3', 'b-3', 'c-3', 'd-3', 'e-3', 'f-3', 'g-3', 'h-3'], dtype='object')
```

이런 모양새다. 앞에 수검자 번호와 성별 빼고 뒷 부분 24개가 문항이다.

애초에 a부터 h까지 총 8개의 요인, 요인당 3개 문항으로 구성되도록 해놓았다.

이걸 리스트 형태로 변환해서 items라는 변수에 담아주고 정렬을 해놓자.

```python
items = df.columns.tolist()[2:]
items.sort()
```

```
['a-1', 'a-2', 'a-3', 'b-1', 'b-2', 'b-3', 'c-1', 'c-2', 'c-3', 'd-1', 'd-2', 'd-3', 'e-1', 'e-2', 'e-3', 'f-1', 'f-2', 'f-3', 'g-1', 'g-2', 'g-3', 'h-1', 'h-2', 'h-3']
```

이제 원래 데이터프레임 df에서 items에 있는 컬럼들의 값을 가져와 요인분석을 돌릴 거다.

### 탐색적 요인분석(EDA) 수행하기

일단 아까 설치했던 라이브러리를 한 번 불러와보자.

```python
from factor_analyzer import FactorAnalyzer
```

그리고 요인분석 모델을 불러온다. 불러올 때 요인 개수, 회전 방법 등을 선택할 수 있는데, 일단 요인 개수는 8개, 최대우도방법, promax 회전으로 돌려볼 거다. (파라미터 설정에 대해 좀 더 자세히 알고 싶다면 [FactorAnalyzer github](https://github.com/EducationalTestingService/factor_analyzer)를 참고하자.)

```python
fa = FactorAnalyzer(n_factors=8, method="ml", rotation="promax")
fa.fit(df[items])
```

그리고 방금 데이터 넣고 돌릴 때 `.fit()`이라는 이름의 메서드를 사용했는데, 파이썬의 머신러닝 scikit-learn 라이브러리 사용해본 사람은 익숙할 거다.

아무튼 이게 끝이다.

요인 부하량은 `fa.loadings_`를 출력해보면 확인할 수 있는데…

```
array([[ 1.24276282e-01,  3.95265985e-01,  1.87318076e-01,
        -3.12377289e-02, -9.95634664e-02, -6.82004265e-02,
         1.74049220e-01, -2.75130028e-04],
       [-4.15189606e-03,  9.03954874e-01, -4.94827334e-02,
        -4.43622592e-03, -2.98582932e-02, -4.33654049e-02,
        -5.24262732e-02, -4.18444548e-02],
       [-7.78104868e-02,  8.85572048e-01, -8.07603310e-02,
         7.47079191e-02,  1.21708537e-03,  9.08425871e-02,
        -9.51157387e-02, -9.78357663e-02],

       ... (중략) ...
       
       [-1.10158523e-01,  1.63145065e-01,  1.33565470e-01,
         1.10847724e-02,  2.22950841e-01,  5.53432143e-02,
        -3.05526090e-02,  1.73291420e-01]])
```

이걸 어떻게 봐… 그러니 pandas 데이터프레임 형태로 바꿔보자. 이때 index는 아까 정리한 items(문항 번호 리스트)를 넣어주면 된다.

```python
efa_result= pd.DataFrame(fa.loadings_, index=items)
```

이걸 깔끔하게 시각화 하고 싶다면 seaborn으로 히트맵을 그리면 된다. (숫자는 소숫점 두 번째 자리까지만 표기되도록 했다.)

```python
plt.figure(figsize=(6,10))
sns.heatmap(efa_result, cmap="Blues", annot=True, fmt='.2f')
```

일단 탐색적 요인분석 방법은 여기까지.

### 신뢰도 계수(Cronbach’s alpha) 확인하기

각 요인에 할당해놓은 문항들을 묶어서 신뢰도 계수(Cronbach’s alpha)도 한 번 확인해보자.

이건 뭐 별도의 라이브러리를 설치할 필요 없이 numpy로 바로 계산이 가능하다. 누군가가 신뢰도를 구하는 함수를 만들어서 [이렇게](https://stackoverflow.com/a/20799687) 올려놓았더라.

```python
def CronbachAlpha(itemscores):
    itemscores = np.asarray(itemscores)
	itemvars = itemscores.var(axis=0, ddof=1)
	tscores = itemscores.sum(axis=1)
	nitems = itemscores.shape[1]
	return (nitems / (nitems-1)) * (1 - (itemvars.sum() / tscores.var(ddof=1)))
```

이 함수는 각 문항들의 점수를 세트(리스트의 리스트 혹은 다차원 배열)로 넣어줘야 하는 거라… 아까 준비해놓았던 문항 번호 리스트 items를 가지고 준비해보자.

```python
factors = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h']
factors_items_dict = {}

for factor in factors:
	factors_items_dict[factor] = [x for x in items if x[0] == factor]
```

일단 factors_items_dict라는 딕셔너리에다가 a부터 h까지 요인에 해당하는 문항을 담았다. 이런 모양새다.

```
{'a': ['a-1', 'a-2', 'a-3'],
 'b': ['b-1', 'b-2', 'b-3'],
 'c': ['c-1', 'c-2', 'c-3'],
 'd': ['d-1', 'd-2', 'd-3'],
 'e': ['e-1', 'e-2', 'e-3'],
 'f': ['f-1', 'f-2', 'f-3'],
 'g': ['g-1', 'g-2', 'g-3'],
 'h': ['h-1', 'h-2', 'h-3']}
```

이제 여기서 하나씩 불러와서 신뢰도를 찍어보자.

```python
for key, value in factors_items_dict.items():
	print(key)
	print(CronbachAlpha(df[value]))
	print()
```

```
a
0.7405101159919194

b
0.6434213337169863

c
0.7722376346207973

d
0.8132146902628883

e
0.6025806790180286

f
0.7797227511940578

g
0.7797358204347294

h
0.6488698703179792
```

요인 b, e, h는 0.7이 안 되는 수준이다. 나머지 요인들은 꽤 준수하네.

일단 오늘은 여기까지.

> 통계분석은 파이썬으로 합시다.