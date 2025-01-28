---
title: 파이썬 퀀트 기초 – 단기 투자 지표 계산하기
date: 2022-02-17
categories: [WORK, 🐍 Python]
tags: [데이터분석, Python]
---

주식 투자 그 자체에 대해서는 워낙 방송이나 책, 유튜브, 블로그에서 온갖 전문가들이 말을 하고 있으니 내가 더 말을 얹을 건 없을 것 같다.

흔히 재무제표나 성장 가능성 등 기업의 가치를 보고 투자하는 장기투자와 주가 흐름이 달라지는 패턴을 보고 하는 단기투자로 구분하는데, 내가 이번에 파이썬으로 시도하려고 하는 건 단기투자 중에서도 오로지 숫자로만 판단하는 "퀀트"다. Quantitative의 약어.

물론 퀀트를 통해 기업의 재무제표나 언론, 대중의 반응 등을 분석하는 것도 가능하지만, 일단 좀 더 단순하게 주식의 가격 변화만 보고 의사결정하는 단기투자 퀀트부터 시도해보기로 했다.

시중에는 퀀트 관련 자료들이 꽤 있은데, 그 중 입문하는 입장에서 가장 유용했던 건 〈미국 주식으로 시작하는 슬기로운 퀀트투자〉라는 책과 여기 등장한 파이썬 코드들이다. 저자가 직접 `finterstellar`라는 파이썬 패키지를 만들어서 올려놓기도 했는데 코드도 양이 얼마 안 되어서 직접 뜯어볼 수 있어 좋았다.

어쨌든 쉬운 것들부터 일단 시작.

## 주가 데이터 내려받기

일단 야후 파이낸스나 구글 파이낸스에서 주가를 내려받자. (참고로 국내 주식은 네이버를 많이들 사용하는데 API 지원이 깔끔하지 않다. 만약 네이버 쓰고 싶다면 다른 사람들이 크롤링 코드를 많이 올려놓았으니 그걸 참고하자.)

파이썬 코드로 함수를 만들면 다음과 같이 쓸 수 있다.

```python
import numpy as np
import pandas as pd
import pandas_datareader.data as web
from datetime import datetime
from dateutil.relativedelta import relativedelta

def get_price(ticker, start_date=None, end_date=None):
    end_date = datetime.strptime(end_date, "%Y-%m-%d").date() if end_date else datetime.today().date()
    start_date = datetime.strptime(start_date, "%Y-%m-%d").date() if start_date else end_date - relativedelta(months=12)
    df = web.DataReader(ticker, 'yahoo', start=start_date, end=end_date)
    return df
```

날짜는 YYYY-MM-DD 형식의 문자열로 입력하는데, end_date는 생략시 오늘, start_date는 생략시 end_date로부터 1년 전으로 지정된다.

내려받은 데이터프레임을 보면 시가, 고가, 저가, 종가를 포함한 데이터가 종목별로 다 들어오는 걸 알 수 있다.

특정 일자에 결측치가 있어서 이후 분석에 오류가 날 수도 있으니 `df.fillna(method='ffill', inplace=True)`로 결측치 처리를 해놓는 것도 좋다. ffill은 바로 직전 값을 가져온다는 의미다. (참고로 bfill은 다음 값을 당겨온다.)

이제 주가 데이터만으로 투자전략에 활용할 수 있는 몇가지 지표들을 직접 계산해보자.

## 투자 지표 계산하기

일단 지표를 계산에 앞서서 투자 전략을 크게 두 축으로 구분한다는 걸 알아두고 넘어가자.

- 모멘텀: 상승세의 주식을 사서 더 오르길 기대하는 전략 (오름세가 꺾이면 바로 빠져야 함)
- 평균회귀: 가격이 원래 수준보다 많이 하락한 주식을 사서 회복을 기대하는 전략 (박스권에 있는 종목이 적합함)

그리고 이제부터 계산할 지표는 다음과 같다.

- MACD
- RSI
- Envelope
- Bolinger Band
- Stochastic

해당 지표를 보고 전략에 따라 어떤 기준으로 투자할 것인지 판단하면 된다.

### 1. MACD

이동평균수렴확산지수 MACD(Moving Average Convergence Divergence)는 제럴드 아펠(Gerald Apped)이라는 사람이 개발한 지표다. 기간이 서로 다른 이동평균선 사이 관계에서 추세변화를 찾아낸다.

[이동평균](https://ko.wikipedia.org/wiki/%EC%9D%B4%EB%8F%99%ED%8F%89%EA%B7%A0)은 범위를 계속 이동하면서 평균을 계산하는 걸 의미하는데, 단순이동평균이라 함은 그냥 최근 w일간의 평균이란 뜻이다.

MACD에서는 단순이동평균으로 주가 추이를 관찰하게 되면 실제 주가가 움직이는 것보다 한발 늦다는 점을 보완하기 위해 지수이동평균 EMA(Exponential Moving Average)를 사용한다. 지수이동평균은 주가 추이를 좀 더 민감하게 감지하기 위해 최근값에 더 높은 가중치를 두고 평균을 구하는 방식이라 이해하자.

어쨌든 기간이 짧은 이동평균선이 기간이 긴 이동평균선보다 위에 있으면 상승 추세라고 판단하는 것이 MACD의 핵심이다. 이후 MACD의 지수이동평균을 다시 구해서 시그널로 사용해 보조 지표로 활용하기도 한다. 계산 방법을 요약하면 다음과 같다.

1. 단기 12일, 장기 26일 기준으로 지수이동평균을 각각 구해놓는다.
2. MACD = EMA(12) – EMA(26)  
    MACD 값이 양수일 경우, 즉 주가의 단기평균이 장기평균보다 위에 있으면 상승 추세라고 판단할 수 있다.
3. MACD Signal = EMA(MACD, 9)  
    MACD가 다소 후행할 수 있기 때문에 MACD의 9일간 EMA를 구한다.
4. MACD Oscillator = MACD – MACD Signal  
    매수세/매도세 간 힘의 차이를 파악하기 위한 보조지표로 MACD에서 MACD Signal을 뺀 값을 사용한다.

계산에 사용된 단기, 장기, 시그널 기간은 모두 지표를 개발한 제럴드 아펠이 추천한 값이다.

이걸 파이썬으로 계산해보자. pandas에서 지수이동평균을 구하는 함수 `ewm()`를 지원하기 때문에 코드가 매우 간단하다.

```python
def macd(df, short=12, long=26, signal=9):
    df['ema_short'] = df['Close'].ewm(span=short).mean()
    df['ema_long'] = df['Close'].ewm(span=long).mean()
    df['macd'] = df['ema_short'] - df['ema_long']
    df['macd_signal'] = df['macd'].ewm(span=signal).mean()
    df['macd_oscillator'] = df['macd'] - df['macd_signal']
    return df
```

MACD를 활용하는 것은 상승세에 매수하고, 하락세에 매도하는 모멘텀 전략에 해당한다.

>MACD 또는 MACD Oscillator가 0보다 크면 매수, 0보다 작으면 매도

### 2. RSI

상대강도지수 RSI(Relative Strength Index)는 월레스 와일더(Welles Wilder)라는 사람이 개발한 지표다. 구가의 상승 또는 하락의 강도를 나타낸다.

1. 일별 가격 차이를 계산한다. 전날 대비 변동폭을 의미한다.
2. 일단 w일 간 상승폭과 하락폭의 이동평균을 구해놓는다.
3. 직전값에는 (w-1)/w, 당일값에는 1/w의 가중치를 부여한 AU, AD를 계산한다.
4. RSI = AU / (AU+AD) * 100

이걸 파이썬으로 계산해보자. pandas에서 각 행 값의 차이를 계산하는 함수 `diff()`, 이동평균을 계산하는 `rolling()` 함수를 지원한다.

```python
def rsi(df, w=14):
    df['diff'] = df['Close'].diff()
    df['au'] = df['diff'].where(df['diff']>0, 0).rolling(w).mean()
    df['ad'] = df['diff'].where(df['diff']<0, 0).rolling(w).mean().abs()
    for r in range(w+1, len(df)):
        df['au'][r] = ( df['au'][r-1]*(w-1) + df['diff'].where(df['diff']>0,0)[r] ) / w
        df['ad'][r] = ( df['ad'][r-1]*(w-1) + df['diff'].where(df['diff']<0,0).abs()[r] ) / w
    df['rsi'] = df['au'] / (df['au'] + df['ad']) * 100
    return df
```

투자 전략이 갈릴 수 있는데, 예를 들면 아래와 같다.

> - 모멘텀 전략 : RSI 70 이상 매수, 50 이하 매도
> - 평균회귀 전략 : RSI 30 이하 매수, 70 이상 매도

지표를 개발한 웰레스 와일더는 평균회귀를 지지한 것으로 보인다. RSI 70 이상이면 과매수(Over Bought), 30 이하이면 과매도(Over Sold)라는 표현을 사용해 구간을 분류했기 때문이다.

### 3. Envelope

Envelope는 이동평균을 이용한 간단한 지표다. 현재 가격이 이동평균보다 몇 % 벌어져있는지 경계선을 그리고, 각 구간에 진입할 경우 투자 판단을 내린다. 이동평균보다 n% 높은 지점을 저항선, n% 낮은 선을 지지선이라고 부르는데 용어는 크게 중요치 않고, 그냥 밴드를 그려 이해하면 편하다.

- A : 상한선 위
- B : 이동평균에서 상한선 사이
- C : 이동평균에서 하한선 사이
- D : 하한선 아래

이 때 이동평균 기간과 밴드의 스프레드를 결정해야 하는데, 일단 기본값으로는 50일과 5% 범위를 잡아보자.

그리고 이걸 파이썬으로 계산하는 건 매우 쉽다.

```python
def envelope(df, w=50, spread=.05):
    df['center'] = df['Close'].rolling(w).mean()
    df['ub'] = df['center']*(1+spread)
    df['lb'] = df['center']*(1-spread)
    return df
```

이 엔벨로프를 보고도 투자 전략이 갈릴 수가 있다.

> - 모멘텀 전략 : A에서 매수, B에서 매도
> - 평균회귀 전략 : D에서 매수, B에서 매도

박스권에서 움직이는 주식이라면 평균회귀 전략이 적절하겠지만, 성장 일변도의 주식이라면 모멘텀 전략이 성과가 더 좋을 거다.

### 4. Bolinger Band

볼린저 밴드는 존 볼린저(John Bolinger)라는 사람이 개발한 전략이다.

이동평균을 중심으로 밴드의 상단과 하단을 결정해 투자 판단을 내린다는 점에서 엔벨로프와 상당히 유사하다.

다만, 밴드를 만들 때 엔벨로프는 이동평균의 몇 %인지 계산하지만, 볼린저 밴드는 이동평균에서 몇 표준편차만큼 떨어져있는지 계산한다. 이동 표준편차가 포함되기 때문에 변동성이 반영되었다고 볼 수 있다.

존 볼린저는 이동평균 기간 20일, 밴드 사이즈 2표준편차를 제안했다.

다만, 볼린저 밴드는 단기투자자 사이에서 인기가 매우 높은 지표라 BB(20, 2)을 투자 시그널로 사용하는 사람이 너무 많다 보니 이 세팅으로는 시장에서는 사실상 기회가 없을 정도라고 한다. 본인이 나름대로 세팅을 바꿔보면서 테스트를 해보는 게 좋다.

어쨌든 볼린저 밴드를 파이썬으로 계산하면 다음과 같다. pandas에서 표준편차를 계산하는 `std()` 함수를 모두 지원하니 간단하다.

```python
def bollinger(df, w=20, k=2):
    df['center'] = df['Close'].rolling(w).mean()
    df['sigma'] = df['Close'].rolling(w).std()
    df['ub'] = df['center'] + k*df['sigma']
    df['lb'] = df['center'] - k*df['sigma']
    return df
```

볼린저 밴드를 통해서도 당연히 투자 전략은 갈릴 수가 있다.

> - 모멘텀 전략 : A에서 매수, B에서 매도
> - 평균회귀 전략 : D에서 매수, B에서 매도

다만, 볼린저 밴드의 창시자 존 볼린저는 모멘텀 전략으로 이용할 것을 더 강력히 추천했다.

### 5. Stochastic

스토캐스틱은 1950년대 조지 래인(George Lane)이 알린 지표인데, 우리나라에서는 고승덕 변호사가 〈고변호사의 주식강의〉라는 책을 통해 적극적으로 홍보하면서 유명해졌다고 한다.

현재 주가가 최근 n일 간 놓고 봤을 때 얼마나 높이 있는지 파악하는 것이 스토캐스틱의 핵심이다. 계산 방법을 요약하면 다음과 같다.

1. 스토캐스틱 = (당일 종가 – N일간 최저가) / (N일 최고가 – N일간 최저가) * 100
2. 스토캐스틱 값을 평탄화 한 이동평균 Slow K를 구해 지표로 활용한다.
3. 다시 Slow K의 이동평균 Slow D를 구해 지표로 활용한다.

따라서 최초 스토캐스틱을 계산하는 기간, Slow K와 Slow D를 구하기 위한 이동평균 계산 기간이 각각 필요한데, 이를 STO(N, m, t)로 표현한다. 이 지표를 고안한 래인이 제시한 기본값은 STO(14, 3, 3)이다.

파이썬 코드로 구현하면 다음과 같이 간단히 끝난다.

```python
def stochastic(df, n=14, m=3, t=3):
    df['fast_k'] = ( df['Close'] - df['Low'].rolling(n).min() ) / ( df['High'].rolling(n).max() - df['Low'].rolling(n).min() ) * 100
    df['slow_k'] = df['fast_k'].rolling(m).mean()
    df['slow_d'] = df['slow_k'].rolling(t).mean()
    return df
```

스토캐스틱 또한 모멘텀과 평균회귀 전략을 둘 다 사용할 수 있는데, 래인이 직접 제시한 전략은 다음과 같다.

> - 모멘텀 전략 : Slow K – Slow D가 0보다 크면 매수, 0보다 작으면 매도
> - 평균회귀 전략 : Slow K가 20 이하 매수, 80 이상 매도

## 투자 지표 시각화하기

이번엔 지표를 보고 매수/매도 포지션을 산출하기 위한 함수를 만들고, 주가 데이터를 지표 데이터와 함께 차트로 그려서 시각화 해보는 코드를 추가로 남겨보았다.

`finterstellar` 라이브러리에 있는 함수의 코드를 좀 더 쉽게 수정했다.

```python
def indicator_to_signal(df, factor, buy, sell):
    df['trade'] = np.nan
    if buy >= sell:
        df['trade'].mask(df[factor]>buy, 'buy', inplace=True)
        df['trade'].mask(df[factor]<sell, 'zero', inplace=True)
    else:
        df['trade'].mask(df[factor]<buy, 'buy', inplace=True)
        df['trade'].mask(df[factor]>sell, 'zero', inplace=True)
    df['trade'].fillna(method='ffill', inplace=True)
    df['trade'].fillna('zero', inplace=True)
    
    df['position_chart'] = 0
    df['position_chart'].mask(df['trade']=='buy', 1, inplace=True)

    return df

def band_to_signal(df, buy, sell):
    df['trade'] = np.nan
    # buy
    if buy == 'A':
        df['trade'].mask(df['Close']>df['ub'], 'buy', inplace=True)
    elif buy == 'B':
        df['trade'].mask((df['ub']>df['Close']) & (df['Close']>df['center']), 'buy', inplace=True)
    elif buy == 'C':
        df['trade'].mask((df['center']>df['Close']) & (df['Close']>df['lb']), 'buy', inplace=True)
    elif buy == 'D':
        df['trade'].mask((df['lb']>df['Close']), 'buy', inplace=True)
    # zero
    if sell == 'A':
        df['trade'].mask(df['Close']>df['ub'], 'zero', inplace=True)
    elif sell == 'B':
        df['trade'].mask((df['ub']>df['Close']) & (df['Close']>df['center']), 'zero', inplace=True)
    elif sell == 'C':
        df['trade'].mask((df['center']>df['Close']) & (df['Close']>df['lb']), 'zero', inplace=True)
    elif sell == 'D':
        df['trade'].mask((df['lb']>df['Close']), 'zero', inplace=True)
    df['trade'].fillna(method='ffill', inplace=True)
    df['trade'].fillna('zero', inplace=True)
    
    df['position_chart'] = 0
    df['position_chart'].mask(df['trade']=='buy', 1, inplace=True)

    return df
```

`indicator_to_signal()`의 경우 투자지표와 함께 해당 값을 몇에 사고 몇에 팔지 파라미터로 지정해주면 된다. `band_to_signal()`은 엔벨로프나 볼린저밴드처럼 종가(가격) 밴드 구간 A, B, C, D 중 어디에 들어왔을 때 사고 팔 것인지 문자열로 넣어주면 된다.

그러면 “trade” 열에는 “buy” 또는 “zero” 값이 들어오고, 이걸 시각화 하기 위해 “buy”일 때 1, “zero”일 때 0 값인 “position_chart” 열이 추가 된다.

아래에는 주가나 지표 데이터, 그리고 포지션까지 하나의 차트에 표현하기 위한 시각화 코드다.

```python
import matplotlib.pyplot as plt
plt.rcParams['figure.figsize'] = (12, 6)
plt.style.use('bmh')

# x축을 공유하는 subplot으로 겹쳐 그리기 (좌우에 각각 y축 설정)
def draw_chart(df, left: list=None, right: list=None, bottom: str=None):
    
    fig, ax1 = plt.subplots()
    x = df.index

    # left
    if left:
        for col in left:
            ax1.plot(x, df[col], label=col)
    else:
        # left가 없다면 y축 안 보이도록 설정 (안 해줄 경우 0~1로 나타남)        
        ax1.yaxis.set_visible(False)
        
    # right
    if right:
        # right가 있을 경우 left 눈금선은 생략
        ax1.grid(False, axis='y')
        ax2 = ax1.twinx()
        # left와 색상 안 겹치도록
        ax2._get_lines.prop_cycler = ax1._get_lines.prop_cycler
        for col in right:
            ax2.plot(x, df[col], label=col)
            
    # bottom
    if bottom:            
        ax3 = ax1.twinx()
        ax3.fill_between(x, 0, df[bottom], color='red', alpha=.5)
        ax3.set_ylim(0, 10)
        ax3.axes.yaxis.set_visible(False)
            
    fig.legend()
```

x 축을 공유하는 subplot을 겹쳐 그리기 위해 `twinx()` 함수를 사용했고, 좌우 y축을 기준으로 할 열 이름을 리스트로 각각 left와 right에 각각 넣어주면 된다. bottom은 positon_chart 값을 표현하기 위한 거다. 최소 0 , 최대 10이기 때문에 1 높이만큼 색깔이 칠해지는 방식.

참고로 `_get_lines.prop_cycler`는 팔레트에서 이전에 사용한 색의 다음 색부터 가져오도록 해주는 옵션이다. 이 처리를 안 해주면 각 서브플롯의 색상 팔레트가 서로 겹쳐버린다.

## 마무리

어쨌든 이렇게 finterstellar라는 파이썬 라이브러리를 직접 뜯어보면서 파이썬으로 주가 데이터를 내려받아 단기투자 지표를 구하고 시각화까지 해보았다.

사실 코딩으로 퀀트를 하는 더 중요한 이유는 이런저런 지표를 결합한 자기만의 모델들을 만들고, 백테스트를 통해 투자 성과를 분석하기 위한 것이지만…

오늘은 일단 여기까지!