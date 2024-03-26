---
title: 통계, SPSS 대신 파이썬으로 해결하기 (T-검정, ANOVA, 카이제곱 등)
date: 2019-12-11
categories: [WORK, Data]
tags: [데이터분석, Python, 심리학]
---

예전에는 나도 통계 돌릴 일이 있으면 대부분 SPSS를 활용했다. 대학교에서 SPSS를 가르쳐줬으니까. 대학교에서는 아직도 많은 사람들이 SPSS를 사용한다. 특히 심리학을 비롯한 사회과학 쪽 분야에서.

그런데 SPSS는 유료다. 물론 학교에서야 라이센스를 구매해서 학생들이 사용할 수 있도록 해두었다 쳐도 정작 사회에 나와서 SPSS를 사용하는 건 어려운 일이다. 그래서 결국 내가 직접 통계 분석을 하려면 R이나 파이썬 같은 오픈 소스를 활용할 수 밖에 없다.

아무튼 난 이제 SPSS 안 쓴다. 그래서 파이썬으로 기본적인 통계적 가설 검정하는 방법을 익혀 놓았다. 아래와 같은 순서로 소개하고자 한다.

- 단일표본 T-검정 (1 Sample T-Test)
- 독립표본 T-검정 (2 Sample T-Test)
- 분산 분석 (ANOVA: Analysis of Variance) + 사후 검정
- 이항 검정 (Binomial Test)
- 카이제곱 검정 (Chi Square Test)

## 1. 단일표본 T-검정 (1 Sample T-Test)

“표본의 평균이 모집단의 평균과 일치한다”라는 귀무가설을 확인하는 방법이다.

파이썬 내장 라이브러리 `scipy`의 `ttest_1samp`를 활용한다.

예를 들어 `data = [32, 34 , 29 , 29 , 22 , 39, 38 , 37 , 38 , 36 , 30, 26 , 22 , 22]`라는 표본이 있고, 내가 가정한 모집단의 평균이 `expected_mean = 30` 이라고 해보자. 그러면 이렇게 확인할 수 있다.

```python
from scipy.stats import ttest_1samp

tstat, pval = ttest_1samp(data, expected_mean)
```

그러면 t-statistic과 p-value, 이렇게 두 개의 결과 값을 돌려준다. (t-statistic에 대해서 다루면 복잡해지니 여기서는 넘어가겠다. 사실 나도 잘 모른다.) 어쨌든 p-value를 확인하자.

(보통 0.05 정도를 기준으로 해석하니까) 만약 p-value가 0.05보다 작으면 앞선 귀무가설(“표본의 평균이 모집단의 평균과 일치한다”)을 기각할 수 있다. 즉, 모집단의 평균과 표본의 평균이 다르다고 자신있게(?) 결론을 내려도 된다는 의미다.

p-value에 대해 다루면 또 더 복잡해지니 자세한 설명은 생략한다. 아무튼 이 결과에 얼마나 자신감을 가질 수 있는지에 대한 지표 정도로 생각하면 된다.

## 2. 독립표본 T-검정 (2 Sample T-Test)

“2개 집단에서 표본을 수집했을 때, 두 집단의 평균이 서로 일치한다”라는 귀무가설을 확인하는 방법이다.

`scipy`의 `ttest_ind`를 활용하면 바로 확인할 수 있다.

예를 들어 2개의 집단에서 각각 `data1`, `data2`라는 표본을 수집했다고 해보자. 그러면 이렇게 확인할 수 있다.

```python
from scipy.stats import ttest_ind

tstat, pval = ttest_ind(data1, data2)
```

마찬가지로 t-statistics와 p-value를 돌려주는데, 어쨌든 p-value만 확인하면 된다.

## 3. 분산 분석 (ANOVA: Analysis of Variance)

T-검정으로는 2개 집단의 평균 비교까지만 가능하다. 3개 이상의 집단의 평균이 같은지(차이가 있는지) 확인하려면 분산 분석(ANOVA: Analysis of Variance)을 해야 한다. 분산 분석의 귀무가설은 “모든 집단의 평균이 같다”가 된다. 즉, 하나라도 평균이 다른 집단이 발견되면 귀무가설을 기각할 수 있다는 뜻이다.

`scipy`의 `f_oneway`를 활용하면 바로 확인할 수 있다.


```python
from scipy.stats import f_oneway

fstat, pval = f_oneway(data1, data2, data3)
```

어쨌든 가설 검정을 위해서는 p-value만 확인하면 된다.

그런데 여기서 만약 p-value가 0.05보다 작다면? 즉, 집단들의 평균이 모두 똑같지 않다는 뜻인데 이 때는 사후 검정을 통해 대체 어떤 집단이 차이가 있는지 확인해야 한다.

### 사후 검정

가장 널리 쓰이는 방법은 Tukey’s Range Test라고 부르는 방법이다.

파이썬에서는 `statsmodel` 안에 있는 `pairwise_tukeyhsd`를 통해 수행할 수 있다. 이렇게.

```python
from scipy.stats import f_oneway
import numpy as np
from statsmodels.stats.multicomp import pairwise_tukeyhsd

fstat, pval = f_oneway(data1, data2, data3)

# np.concatenate를 통해 데이터를 합쳐 놓는다.
v = np.concatenate([data1, data2, data3])

# 데이터 개수만큼 레이블을 준비한다.
labels = ['data1']*len(data1) + ['data2']*len(data3) + ['data3']*len(data3)

# 사후 검정을 수행한다.
tukey_results = pairwise_tukeyhsd(v, labels, 0.05)
print(tukey_results)
```

결과가 이런 식으로 출력된다.

```
Multiple Comparison of Means - Tukey HSD,FWER=0.05
=============================================
group1 group2 meandiff lower upper reject
---------------------------------------------
data1 data2 7.2767 3.2266 11.3267 True
data1 data3 4.0115 -0.0385 8.0616 False
data2 data3 -3.2651 -7.3152 0.7849 False
---------------------------------------------
```

맨 오른쪽 reject 컬럼 첫 줄에 True가 써있다. data1과 data2가 같다는 귀무 가설을 기각하라는 뜻, 즉 data1과 data2는 평균이 다르다고 해석할 수 있다는 얘기다.

### T-검정과 ANOVA의 전제 조건

T-검정과 ANOVA를 할 떄는 반드시 아래와 같은 조건이 성립되어야 한다.

- 표본이 정규 분포를 그려야 한다. (정규성)
- 2개 이상의 집단을 비교하려면, 각 집단의 표준편차가 서로 같아야 한다. (등분산성)
- 표본은 독립적이어야 하며, 서로 영향을 미치지 않아야 한다.

## 4. 이항 검정 (Binomial Test)

T-검정이나 ANOVA는 모두 분포의 평균을 비교하는 가설 검정이다. 그러나 만약 범주가 2개(예를 들어 성공 또는 실패)로 구성된 자료인 경우 이항 검정을 사용해야 한다.

`scipy`의 `binom_test`를 활용하면 바로 확인할 수 있다. 입력 값은 총 3개가 필요하다. 성공 횟수, 시도 횟수, 기대 성공 확률.

예를 들어 1000번 시도해서 525번 성공했는데, 기대 성공 확률이 0.5라고 하면 이렇게 작성하면 된다.

```python
from scipy.stats import binom_test

pval = binom_test(525, n=1000, p=0.5)
```

역시나 p-value를 돌려주며, 0.05보다 작으면 귀무 가설(“실제 성공 확률은 기대 성공 확률과 같다”)을 기각할 수 있다. 위 예시에서는 성공 확률을 0.5로 예측했으나, 실제 성공 확률은 0.5가 아니라는 의미, 0.5보다 크다는 뜻으로 해석하면 된다.

## 5. 카이제곱 검정 (Chi Square Test)

이항 검정은 범주가 2개로 자료에만 사용할 수 있다. 만약 범주가 3개 이상인 경우에는 카이제곱 검정을 사용해야 한다. 행과 열을 조합해서 발생할 수 있는 경우의 수를 행열로 만들어놓고 활용하면 편하다.

파이썬 `scipy`의 `chi2_contingency`로 수행할 수 있다. 이렇게.

```python
# A B
# -----------------
# 1st | 30 | 10
# 2nd | 35 | 5
# 3rd | 28 | 12
# 4th | 20 | 20

data = [[30, 10],
		[35, 5],
		[28, 12],
		[20, 20]]

from scipy.stats import chi2_contingency

chi2, pval, dof, expected = chi2_contingency(data)
```

`chi2_contingency`는 총 네 개의 결과 값을 돌려주는데 그 중 두 번째가 p-value다. 만약 p-value가 0.05 이하라면 집단 간의 차이가 있다고 해석할 수 있다.

>난 앞으로 SPSS 쓸 일 없을 거다.