---
title: 파이썬으로 주가 예측하기 (시계열 예측 라이브러리 prophet)
date: 2022-11-01
categories: [WORK, 🐍 Python]
tags: [Python, 데이터분석, 데이터시각화]
---

[Prophet](https://github.com/facebook/prophet)은 메타(페이스북)에서 공개한 시계열 예측 라이브러리인데, 정확도가 높은 편이고 각종 파라미터로 모델 수정하기 쉽다는 장점을 갖고 있다.

이 라이브러리를 활용해서 미래의 주가를 예측해보자. 파이썬 코드 20줄이 채 안 된다.

>물론 예측 모델에 대한 반박 시 당신 말이 맞습니다.

## 0. 라이브러리 설치

야후 파이낸스 데이터를 가져오기 위한 [yfinance](https://github.com/ranaroussi/yfinance), 예측 모델링 및 시각화를 위한 [prophet](https://github.com/facebook/prophet), 이렇게 두 개면 된다. pip로 바로 설치.

```
pip install yfinance
pip install prophet
```

그런데 이 상태에서 Prophet을 불러오면 “Importing plotly failed. Interactive plots will not work.” 라는 에러 메시지가 뜰 수 있다. Prophets는 [Plotly](https://plotly.com/python/) 기반으로 데이터를 시각화하기 때문에 이것도 미리 설치하자.

```
pip install plotly
```

이제부터 본격적인 파이썬 코드 예제.

## 1. 데이터 불러오기

```python
import yfinance as yf

df = yf.download("META", start=None, end=None)
df.reset_index(inplace=True)
```

yfinace에서 데이터를 내려받을 때 내려받을 종목(티커)와 함께 기간을 설정할 수 있다. 시작(start)과 끝(end) 날짜에 “2015-01-01″과 같은 문자열 형식으로 넣어주면 된다. None으로 하면 처음부터 최종 값까지 모두 가져온다.

내려받은 데이터를 `df.head()`나 `df.tail()`로 확인해보자.

참고로 Close(종가)는 시장이 마감되기 전 마지막으로 거래된 주가, Adj Close(수정 종가)는 분할(splits), 배당금 분배(dividend distributions) 등 주가에 영향을 미칠 수 있는 기업의 활동을 반영한 후의 종가를 뜻한다.

일반적으로는 주가 정보를 활용할 때는 그냥 Close(종가)를 직접 사용한다.

## 2. Prophet으로 주가 데이터 학습하여 예측 모델 생성

필요한 정보는 날짜와 주가, 이렇게 딱 2개뿐이다. 이걸 가지고 Prophet 모델을 통해 학습을 시켜보자.

Prophet에서는 모델 학습을 시키기 전에 시계열에 해당하는 변수를 `ds`, 예측할 값을 `y`로 지정해줘야 한다.

```python
from prophet import Prophet

df_train = df[['Date', 'Close']]
df_train = df_train.rename(columns={"Date": "ds", "Close": "y"})

m = Prophet()
m.fit(df_train)
```

이게 끝이다. 이제 예측을 해보자.

## 3. 기간 설정하여 주가 예측

```python
future = m.make_future_dataframe(periods=365)
forecast = m.predict(future)
```

`.make_future_dataframe()`에서 periods에 날짜 수를 넣어주면 기존 데이터에 해당 기간을 추가한 데이터프레임을 돌려받는다.

그리고 그 결과를 그대로 `.predict()`로 넣어주면 예측된 데이터 프레임을 얻을 수 있다. 다음과 같은 다양한 정보를 제공한다.

```
'ds', 'trend', 'yhat_lower', 'yhat_upper', 'trend_lower', 'trend_upper', 'additive_terms', 'additive_terms_lower', 'additive_terms_upper', 'weekly', 'weekly_lower', 'weekly_upper', 'yearly', 'yearly_lower', 'yearly_upper', 'multiplicative_terms', 'multiplicative_terms_lower', 'multiplicative_terms_upper', 'yhat'
```

복잡하니까 그냥 바로 시각화 해서 추세를 확인해보자.

## 4. 주가 예측 시각화

```python
fig1 = m.plot(forecast)
fig2 = m.plot_components(forecast)
```

`.plot()`으로 그리면 전체 기간에 대한 추세선을 보여준다.

`.plot_components()`으로 그리면 연도별, 요일별, 월별 경향성을 확인할 수 있다.

끝.