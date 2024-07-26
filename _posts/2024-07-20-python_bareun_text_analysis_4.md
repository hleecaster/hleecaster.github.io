---
title: 파이썬 형태소분석기 '바른'을 활용한 텍스트 분석 – (4) 차별어 분석
date: 2024-07-20
categories: [WORK, Data]
tags: [Python, 데이터분석]
---

## 차별어 분석이란

이번에는 두 문서에서 사용되는 차별적인 어휘를 살펴보는 (예를 들면, 네이버 영화 리뷰 중 긍정 리뷰와 부정 리뷰에 각각 차별적으로 많이 등장하는 어휘가 무엇인지 파악하는) 분석을 해보려 한다.

이러한 차별어 분석에는 여러가지 방법을 사용할 수 있다. 흔히 [odds ratio](https://en.wikipedia.org/wiki/Odds_ratio)(오즈비)라는 걸 활용하는데, 본 포스팅에서는 [KL 발산(Kullback-Leibler divergence)](https://en.wikipedia.org/wiki/Kullback%E2%80%93Leibler_divergence)을 사용하는 방법을 소개하려 한다. 개인적으로 이 방법으로 분석 돌렸을 때 결과가 더 마음에 들더라.

## pandas 데이터프레임에서 형태소 분석 수행

pandas 데이터프레임 형식에서 바로 형태소 분석. 형태소 분석 적용할 때는 람다 함수를 활용했다.

```python
from bareunpy import Tagger
import pandas as pd

tagger = Tagger(API_KEY, 'localhost')

df = pd.read_excel('rawdata.xlsx')

morph_analysis = lambda x: tagger.tags([x]).pos() if type(x) is str else None
df['형태소분석결과'] = df['원문'].apply(morph_analysis)
```

## 차별어 분석 – KL 발산 (Kullback-Leibler divergence)

일단 함수 형식으로 코드를 작성했다. [KL 발산(Kullback-Leibler divergence)](https://en.wikipedia.org/wiki/Kullback%E2%80%93Leibler_divergence)에 대해 자세히 알고 싶으면 위키피디아를 참고하자.

```python
import math
from collections import Counter

주요품사 = ["NNG", "NNP", "VV", "VA", "XR"]
용언품사 = ["VV", "VA"]


'''주어진 입력 파일에서 긍부정 리뷰별 형태소 빈도를 계수하여 돌려준다."'''
def get_morph_counters(df, col, group, group_label):
    카운터_a = Counter()
    카운터_b = Counter()

    for index, row in df.iterrows(): 
        if row[col]:
            필터링결과 = [(token.form, token.tag) for token in row[col] if token.tag in 주요품사]
            필터링결과 = [form+"다" if tag in 용언품사 else form for form, tag in 필터링결과]
            if row[group]==group_label[0]:
                for form in 필터링결과:
                    카운터_a[form] += 1
            if row[group]==group_label[1]:
                for form in 필터링결과:
                    카운터_b[form] += 1
    return 카운터_a, 카운터_b


'''형태소 빈도에서 형태소 출현 확률을 구하여 돌려준다.'''
def get_morph_probs(카운터):
    확률카운터 = Counter()
    빈도합 = sum(카운터.values())
    for 형태소, 빈도 in 카운터.items():
        확률카운터[형태소] = 빈도 / 빈도합
    return 확률카운터


'''주어진 형태소 출현 빈도들로부터 KL 발산을 구하여 돌려준다.'''
def get_kl_divs(확률카운터_a, 확률카운터_b):
    KL_a = Counter()
    KL_b = Counter()
    확률모음 = set(확률카운터_a.keys()) | set(확률카운터_b.keys())
    for 형태소 in 확률모음:
        등장확률_a = 확률카운터_a[형태소]
        등장확률_b = 확률카운터_b[형태소]
        등장확률_평균 = (등장확률_a + 등장확률_b) / 2
        # 형태소 발현 확률이 0이면 KL 발산을 음의 무한대로 정의한다.
        if 등장확률_a == 0.0:
            KL_a[형태소] = -math.inf
        else:
            KL_a[형태소] = 등장확률_a * math.log(등장확률_a / 등장확률_평균)
        if 등장확률_b== 0.0:
            KL_b[형태소] = -math.inf
        else:
            KL_b[형태소] = 등장확률_b * math.log(등장확률_b / 등장확률_평균)
    return KL_a, KL_b


'''긍부정 리뷰 차별어를 출력 파일에 기록한다.'''
def write_kl_divs(KL_a, KL_b, output_file):
    print(f"그룹[0],확률,그룹[1],확률", file=output_file)
    for (형태소_a, KL_a), (형태소_b, KL_b) in zip(KL_a.most_common(1000), KL_b.most_common(1000)):
        print(f"{형태소_a},{KL_a},{형태소_b},{KL_b}", file=output_file)
```

분석에 사용할 주요 품사는 알아서 잘(?) 골라주어야 한다. 이를 테면 네이버 영화 리뷰처럼 느낌표, 물음표와 같은 문장부호가 유의미한 결과를 가져올 수도 있기 때문이다.

아무튼 위에 작성된 함수들을 실행하려면 아래와 같이 적어줄 수 있다. `group`에는 ‘긍정’ 또는 ‘부정’이라는 레이블이 달린 열(column) 이름, `group_label`에는 레이블을 리스트 형식으로 넣어주면 된다.

```python
카운터_a, 카운터_b = get_morph_counters(df, '형태소분석결과', group, group_label)
확률카운터_a = get_morph_probs(카운터_a)
확률카운터_b = get_morph_probs(카운터_b)
KL_a, KL_b = get_kl_divs(확률카운터_a, 확률카운터_b)

'''csv 저장'''
output_file_name = "./output/차별어분석.csv"
with open(output_file_name, "w", encoding="utf-8-sig") as output_file:
    write_kl_divs(KL_a, KL_b, output_file)
```

## 차별어 분석 결과로 워드 클라우드 그리기

KL발산 값은 어휘 등장 확률이기 때문에 이를 빈도로 간주하여 워드클라우드를 그려줄 수 있다.

```python
from wordcloud import WordCloud
import matplotlib.pyplot as plt

'''워드 클라우드를 생성하여 돌려준다.'''
def build_cloud(카운터, n=50):
    cloud_gen = WordCloud(
        width=800, height=480, 
        background_color="white", 
        colormap ="viridis", 
        font_path= "C:/Windows/Fonts/malgun.ttf", 
        max_words=n,
        collocations=True, 
        prefer_horizontal=1, 
        margin=30, 
        relative_scaling='auto'
    )
    cloud = cloud_gen.generate_from_frequencies(카운터)
    return cloud

def save_cloud(카운터, output_filename, stopwords=None):

    for stopword in stopwords:
        if stopword in 카운터:
            카운터.pop(stopword)
            
    cloud = build_cloud(카운터, 50)
    plt.imshow(cloud, interpolation="bilinear")
    plt.axis("off")
    plt.savefig(f"./{output_filename}", dpi=300)
```

예를 들어 긍정 문서들의 차별어를 가지고, 그린다고 하면 이렇게 작성할 수 있겠다.

```python
카운터 = KL_a
output_filename = "./output/워드클라우드(차별어).png"
stopwords = ['삭제할', '어휘', '모음', '리스트']

save_cloud(카운터, output_filename, stopwords)
```

오늘 포스팅은 여기까지.