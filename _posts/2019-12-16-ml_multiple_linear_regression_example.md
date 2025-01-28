---
title: 머신러닝 공부 - 다중선형회귀(Multiple Linear Regression) 파이썬 코드 예시
date: 2019-12-16
categories: [WORK, 📊 Data]
tags: [데이터분석, MachineLearning, Python]
math: true
---

이전에 선형회귀에 대한 개념을 소개한 바 있다. X의 값에 따라 Y값이 어떻게 달라질지 예측하는, 기울기와 절편만 있는 단순한 1차 방정식 `y = m*X + b`로 설명했는데, 이건 그냥 단순선형회귀라고 부른다.

아무튼 우리가 사는 세상은 단순선형회귀로 설명할 수 있을만큼 그렇게 단순하지 않다. 고려해야 하는 변수 X가 하나가 아니라는 의미다.

예를 들어 주택 임대료를 예측한다고 했을 때, 주택의 면적만 고려해서 예측하면 단순회귀가 되겠지만 지어진지 얼마나 오래 되었는지, 지하철 역과 거리가 얼마나 가까운지 등 다양한 요소의 영향을 받는다. 결국 주택 임대료 y를 예측하려면 여러 개의 변수 x를 포함해야 하는 거다. 이를 다중선형회귀(Multiple Linear Regression)라고 하며, 방정식으로 나타내면 아래와 같다.

$$ Y_{i}=\beta _{0}+\beta _{1}X_{i1}+\beta _{2}X_{i2}+\ldots +\beta _{p}X_{ip}+\epsilon _{i} $$

식으로 보면 쉽지만 변수가 많기 때문에 시각화를 해놓으면 인간의 인지 능력으로는 이해할 수 없어진다. x 변수를 두 개만 넣고 시각화 해도 이런 식으로 나오는데…

![다중선형회귀](https://miro.medium.com/v2/resize:fit:560/1*HLN6FxlXrzDtYN0KAlom4A.png)

그러니 기계의 힘을 빌리자.

이제부터 파이썬으로 다중선형회귀 분석하는 방법을 코드 예시와 함께 살펴보자.

## 데이터 불러오기

pandas를 통해 일단 csv로 저장된 데이터를 불러와봤다. 예시 데이터는 뉴욕 맨해튼의 주택 임대료와 관련한 테이블이다. ([데이터 다운로드](https://www.kaggle.com/datasets/zohaib30/streeteasy-dataset))

```python
import pandas as pd

df = pd.read_csv("./manhattan.csv")
df.head()
```

맨 왼쪽에 “rent”라는 항목이 있는데, 이게 주택의 임대료다. 이걸 예측하는 게 우리의 목표다. 

그 외의 항목들은 침실이 몇개 있는지, 지하철 역에서 몇 분 거리에 있는지, 엘리베이터가 있는지 등 주택에 대한 다양한 정보를 담고 있는데, 아래와 같이 총 14개의 정보를 분석에 사용할 거다.

```
['bedrooms', 'bathrooms', 'size_sqft', 'min_to_subway', 'floor', 'building_age_yrs', 'no_fee', 'has_roofdeck', 'has_washer_dryer', 'has_doorman', 'has_elevator', 'has_dishwasher', 'has_patio', 'has_gym']
```

## 데이터 세트 분리하기 (Training & Test)

가지고 있는 데이터를 모두 사용해서 다중선형회귀 모델을 만들 수도 있지만, 우리는 실제로 생성한 모델이 잘 예측하는지 테스트를 해보기 위해 학습(train) 데이터와 시험(test) 데이터를 분리해보자. (모든 데이터에 주택 임대료 “rent” 정답이 있으니, 시험 데이터를 예측 모델에 넣어서 실제 정답을 잘 맞추는지 보기 위함이다.)

데이터 세트 분리는 `sklearn`에서 `train_test_split`을 통해 손쉽게 할 수 있다.

아래와 같이 8:2 정도의 비율로 나눠보자.

```python
from sklearn.model_selection import train_test_split

x = df[['bedrooms', 'bathrooms', 'size_sqft', 'min_to_subway', 'floor', 'building_age_yrs', 'no_fee', 'has_roofdeck', 'has_washer_dryer', 'has_doorman', 'has_elevator', 'has_dishwasher', 'has_patio', 'has_gym']]
y = df[['rent']]

x_train, x_test, y_train, y_test = train_test_split(x, y, train_size=0.8, test_size=0.2)
```

다 된 거다.

## 모델 생성하기

이제 모델을 생성하자. 당연히 학습 데이터를 가지고 모델을 생성한다.

방법은 단순선형회귀와 똑같다.

```python
from sklearn.linear_model import LinearRegression

mlr = LinearRegression()
mlr.fit(x_train, y_train) 
```

끝난 거다.

이제 만약 내가 주택에 대한 14개 항목값 넣어주면 주택 임대료를 예측해준다.

```python
my_apartment = [[1, 1, 620, 16, 1, 98, 1, 0, 1, 0, 0, 1, 1, 0]]
my_predict = mlr.predict(my_apartment)
print(my_predict)
# [[2434.46128953]]
```

이제 x 시험 데이터 `x_test`를 넣어 예측한 y 값들을 `y_predict`라고 저장해보자. 추후에 시험 데이터에 있는 실제 정답, 즉 `y_test`와 비교해보기 위함이다.

```python
y_predict = mlr.predict(x_test)
```

matplotlib의 시각화를 통해 간단히 확인하고 넘어가보자.

x축은 실제 임대료, y축은 예측한 임대료다. 만약 정답을 맞춘다면 정확히 선으로 일치되어 나올 거다.

```python
import matplotlib.pyplot as plt

plt.scatter(y_test, y_predict, alpha=0.4)
plt.xlabel("Actual Rent")
plt.ylabel("Predicted Rent")
plt.title("MULTIPLE LINEAR REGRESSION")
plt.show()
```

임대료가 낮은 항목들은 꽤 가깝게 맞추는데, 높은 임대료에서는 오차가 좀 생기는 걸 확인할 수 있다.

## 회귀계수와 상수 확인하기

위에서도 언급했지만 다중회귀는 아래와 같은 방정식으로 표현할 수 있다.

$$ Y_{i}=\beta _{0}+\beta _{1}X_{i1}+\beta _{2}X_{i2}+\ldots +\beta _{p}X_{ip}+\epsilon _{i} $$

단순선형회귀는 우리가 직접 그릴 수 있는 1차 방정식으로 설명이 되기 때문에 기울기와 절편이라는 표현으로 식을 구성하는 값들을 확인할 수 있었다.

그런데 다중회귀에서는 변수가 많기 때문에 조금 표현을 달리해야한다. 모든 변수 x마다 각각의 베타값이 있는데, 이것들을 회귀계수라고 부른다.

그리고 다중회귀에서도 단순회귀와 똑같이 이 값들을 확인할 수 있다. 모델 뒤에 `.coef_` , `.intercept_`라고 써주면 끝이다.

```python
print(mlr.coef_)
```

```
[[-338.66808472 1106.00671884 5.14199537 -18.76126614 23.30615803 -7.49886499 -97.64915749 -44.80926408 147.6023034 -141.30694501 85.36579753 5.87587263 -88.66994401 -2.44388725]]
```

위 모델에서 총 14개의 변수를 사용했기 때문에 회귀계수도 당연히 14개가 존재한다.

그런데 이렇게 보면 대체 어떤 변수가 중요한 건지 감이 안 온다.

## 상관 살펴보기

그럴 땐 matplotlib에서 scatter 플롯을 그려서 변수들과 주택 임대료의 상관을 살펴보자

```python
# 주택의 면적 'size_sqft'과 가격 'rent'
plt.scatter(df[['size_sqft']], df[['rent']], alpha=0.4)
plt.show()
```

```python
# 주택이 얼마나 오래 전에 지어졌는지 'building_age_yrs'와 가격 'rent'
plt.scatter(df[['building_age_yrs']], df[['rent']], alpha=0.4)
plt.show()
```

일단 이 두 개의 그림만 봐도 주택 면적은 상관이 있는데 얼마나 오래된 건물인지는 딱히 상관이 나타나지 않는 걸 알 수 있다.

## 모델의 정확도(Accuracy) 평가하기

위에서 데이터 세트를 나눠놨기 때문에 학습 시킨 모델을 테스트 할 수 있다.

다중선형회귀 모델의 정확도를 평가할 때는 잔차를 가지고 분석을 하게 된다. (잔차는 실제 값 y와 예측된 값 ŷ의 차를 의미한다.)

`sklearn`의 `linear_model.LinearRegression`를 사용해서 모델을 생성하면 `.score()`라는 메서드를 사용할 수 있는데 R²라고 하는 결정계수(coefficient of determination)를 돌려준다. 

결정계수 R²은 이렇게 표현할 수 있다.

$$ R^{2}=1-{SS_{\rm {res}} \over SS_{\rm {tot}}} $$

이게 뭐냐… 개념적으로만 일단 이해해보자.

일단 분자는 잔차의 제곱들의 합 RSS(residual sum of square), 즉 직선이 미처 Y에 대해 설명하지 못한 변화량을 의미한다.

```python
((y - y_predict)  2).sum()
```

분모에 있는 v는 TSS(total sum of squares), y값의 총 변화량으로 이해하면 된다.

```python
((y - y.mean())  2).sum()
```

결국 결정계수 R²는 전체에서 직선이 미처 설명하지 못한 부분의 비율을 뺀 거다.

아무튼 어려우면 다 잊고 그냥…….

결정계수 R²가 클수록 실제값과 예측값이 유사함을 의미하며, 데이터를 잘 설명한다고 이해하자. 그러니 모델이 얼마나 정확한지 평가할 때도 이 결정계수 R²가 기준이 될 수 있는 거다.

예를 들어, 주택 사이즈(“size_sqft”)와 침실 개수(“bedrooms”)를 기준으로 임대료를 예측하는 모델의 R²이 0.72인 경우 그 2개의 변수들이 함께 임대료 변동의 72%를 설명한다는 뜻이다.

여기에 다른 x 변수, 주택이 얼마나 오래 전에 지어졌는지(“building_age_yrs”)를 모델에 추가하면 새로운 R²가 0.95로 증가했다고 하자. 그럼 주택 사이즈(“size_sqft”), 침실 개수(“bedrooms”), 얼마나 오래 전에 지어졌는지(“building_age_yrs”) 이 3개의 변수가 함께 임대료 변동의 95%를 설명한다는 거다.

당연히 최선의 R²는 1이겠지만 그건 말이 안 되고, 일반적으로 0.7 정도면 양호한 것으로 간주한다.

위 예제에서 14개의 변수를 넣고 생성한 모델의 결정계수를 확인해보자.

```python
print(mlr.score(x_train, y_train))
```

```
0.7818789468737146
```

꽤 높다. 주택 임대료의 78%를 저 14개의 항목으로 설명할 수 있다는 의미다.