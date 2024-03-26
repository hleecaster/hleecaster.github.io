---
title: 머신러닝 공부 - 선형회귀(Linear Regression) 파이썬 코드 예시
date: 2019-12-15
categories: [WORK, Data]
tags: [데이터분석, MachineLearning, Python]
---

본 포스팅에서는 파이썬 라이브러리 scikit-learn을 통해 선형회귀(Linear Regression) 분석을 직접 수행하는 예시를 소개한다. 누구나 쉽게 따라할 수 있는 수준으로 작성했다.

## sklearn LinearRegression 사용법

실제 데이터 돌려보기 전에 사용법부터 익히고 가자.

일단 파이썬 머신러닝 라이브러리 싸이킷런을 불러오자.

```python
from sklearn.linear_model import LinearRegression
```

이제 `LinearRegression` 모델을 생성하고, 그 안에 X, y 데이터를 fit 시킨다. 이렇게.

```python
line_fitter = LinearRegression()
line_fitter.fit(X, y)
```

`fit()` 메서드는 선형 회귀 모델에 필요한 두 가지 변수를 전달하는 거다.

- 기울기: `line_fitter.coef_`
- 절편: `line_fitter.intercept_`

어쨌든 이게 끝이다. 이렇게 하면 새로운 `X` 값을 넣어 `y`값을 예측할 수 있게 된다.

```python
y_predicted = line_fitter.predict(X)
```

만약 기울기와 절편을 알고 싶다면 `line_fitter.coef_` , `line_fitter.intercept_`를 직접 찍어보면 된다.

쉽다.

아, 그리고 수렴할 때까지 얼마나 반복할 것인지(`num_iterations`), 얼마나 꼼꼼히 학습할 것인지(`learning_rate`) 이런 것들을 정해줄 수도 있는데, 여기서는 굳이 복잡하게 고려하지 않아도 괜찮다. 싸이킷런 단순 선형회귀에서는 [최소제곱법(Ordinary Least Squares)](https://ko.wikipedia.org/wiki/%EC%B5%9C%EC%86%8C%EC%A0%9C%EA%B3%B1%EB%B2%95)을 활용하기 때문이다.

## sklearn LinearRegression 실전 예시

일단 필요한 라이브러리를 불러온다.

sklearn 외에도 데이터를 불러올 때 필요한 pandas, 배열을 바꿀 때 필요한 numpy, 시각화를 위한 matplotlib를 함께 불러왔다.

```python
from sklearn.linear_model import LinearRegression
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
```

일단 나는 csv 파일로 키와 몸무게가 들어있는 파일을 준비했다. ([데이터 다운로드](https://www.kaggle.com/datasets/burnoutminer/heights-and-weights-dataset))

pandas로 윗 부분만 찍어보고,

```python
df = pd.read_csv("./SOCR-HeightWeight.csv")
df.head()
```

matplotlib으로 시각화를 해보니 대충 우상향 하는 패턴이 보인다.

```python
X = df["Height(Inches)"]
y = df["Weight(Pounds)"]
plt.plot(X, y, 'o')
plt.show()
```

이제 위에서 배운대로 모델을 생성하고 데이터를 fit 시킨다.

```python
line_fitter = LinearRegression()
line_fitter.fit(X.values.reshape(-1,1), y)
```

여기서 주의해야 할 점은 `X`데이터를 넣을 때 `.values.reshape(-1,1)`를 해줬다는 거다. 왜냐하면 X는 2차원 array 형태여야 하기 때문이다. 이런 식으로 `[[x1], [x2], [x3], ... , [xn]]` . (이렇게 넣는 이유는 X 변수가 하나가 아니라 여러개일 때 다중회귀분석을 실시하기 위함인데, 이는 다른 글에서 소개해야겠다.)

아무튼 이렇게 하면 끝이다.

이제 한 번 예측을 해보자. 키가 70인치인 사람을 예측한다고 치면 이렇게.

```python
line_fitter.predict([[70]])
```

133.26760811를 돌려준다. 몸무게가 133파운드 정도 되나보다.

그렇다면 기울기를 알려달라고 해보자.

```python
line_fitter.coef_
```

3.08347645를 돌려준다.

이번엔 절편을 알려달라고 해보자.

```python
line_fitter.intercept_
```

-82.57574306454093라고 한다.

이번엔 기존 X 값으로 y를 예측하게 해서 그래프를 그려보자. 당연히 선이 나올 거다.

```python
plt.plot(X, y, 'o')
plt.plot(X,line_fitter.predict(X.values.reshape(-1,1)))
plt.show()
```

예시는 여기까지.