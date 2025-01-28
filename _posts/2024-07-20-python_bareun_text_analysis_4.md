---
title: 파이썬 형태소분석기 '바른'을 활용한 텍스트 분석 – (4) 문서 군집화
date: 2024-07-20
categories: [WORK, 📊 Data]
tags: [Python, 데이터분석]
---

한국어 형태소 분석기 바른으로 형태소 분석 수행 후에 문서들을 군집화 해보는 코드 예시.

## 문서 군집화란

문서 군집화(Clustering)는 여러 문서들이 있을 때 속성이 유사한 문서들끼리 집단(군집)화하는 절차를 말한다. 이때 문서의 속성과 문서의 유사성을 어떻게 정의할지가 매우 중요한데, 일반적으로 어휘 벡터 모델을 활용하여 유사도를 판단한다.

계층적 군집화(hierarchicnal clustering)와 비계층적 군집화(non-hierarchicnal clustering)로 나눌 수 있는데, 본 예제는 비계층적 군집화에 관한 실습이다. 


이번에는 문서 분류 과제에서 제법 좋은 성능을 보이는 것으로 알려진 나이브 베이즈(Naive Bayes) 모델을 이용해 문서 분류기를 구현해보자. 텍스트 원문과 레이블이 준비되어 있으면 된다.

## 형태소 분석

pandas 데이터프레임 형식에서 바로 형태소 분석. 형태소 분석 적용할 때는 람다 함수를 활용했다.

```python
from bareunpy import Tagger
import pandas as pd

tagger = Tagger(API_KEY, 'localhost')

df = pd.read_excel('rawdata.xlsx')

morph_analysis = lambda x: tagger.tags([x]).pos() if type(x) is str else None
df['형태소분석결과'] = df['원문'].apply(morph_analysis)
```


## 문서 군집화

### 1) 문서 준비

우선 공백으로 연결된 문서 형식으로 변환해서 넣어줘야 하기 때문에, 데이터를 준비하자.

```python
주요품사 = ["NNG", "NNP", "NP" "VV", "VA", "MAG", "XR"]
용언품사 = ["VV", "VA"]

# " "으로 구분되는 문서 형식으로 변경
def to_document_format(형태소분석결과):
    if 형태소분석결과:
        # 주요품사만 골라내기
        필터링결과 = [(형태소, 품사) for (형태소, 품사) in 형태소분석결과 if 품사 in 주요품사]
        # 용언품사에 "-다" 붙이기
        필터링결과 = [형태소+"다" if 품사 in 용언품사 else 형태소 for (형태소, 품사) in 필터링결과]
        문서 = " ".join(필터링결과)
        return 문서
    else:
        return ""

df["문서"] = df["형태소분석결과"].apply(to_document_format)
```


### 2) 문서-단어 행렬 생성

이제 군집화 수행을 위해 일단 문서-단어 행렬을 만들어야 한다.

```python
from sklearn.feature_extraction.text import TfidfVectorizer

# 벡터화
vectorizer = TfidfVectorizer(tokenizer=str.split, 
							 binary=True, 
							 use_idf=True, 
							 norm=None, 
							 ngram_range=(1,1), 
							 max_features=1000)
							 
문서단어행렬 = vectorizer.fit_transform(df["문서"])
```

만약 이런저런 파라미터를 조정하고 싶다면 `TfidfVectorizer()`를 수행할 때 넣어주면 된다. (예를 들어 TF-IDF를 사용하고 싶지 않다면, `use_idf` 파라미터를 조정해줄 수 있다.)

벡터화 과정에서 생성에 사용한 단어와 모든 문서에서 등장한 빈도를 확인해보자.

```python
# 각 단어의 등장 빈도 확인
vectorizer.vocabulary_
```

벡터화에 사용된 단어들은 따로 리스트로 저장하자. (추후 군집화 결과를 출력할 때 활용한다.)

```python
# 전체 단어 리스트 저장 (추후 군집화 결과 출력에 활용)
전체단어리스트 = vectorizer.get_feature_names_out()
print(f"어휘 개수: {len(전체단어리스트)}")
print(전체단어리스트[:10])
```

문서-단어 행렬을 확인해보자.

```python
# 문서-단어 행렬 확인
pd.DataFrame(문서단어행렬.toarray(), columns=전체단어리스트)
```

### 3) `KMeans` 군집화

K-means는 같은 군집에 속한 자료 항목들은 서로 거리가 ”가깝다”라는 간단한 가정에서 시작한다. 군집에는 중심(centroid)을 하나씩 설정하고, 이로부터 각각의 자료와의 거리는 비용(cost)으로 정의한다. 결국 K-mean는 이와 같이 정의한 비용을 최소화하는 군집을 찾는 알고리즘이다.

1. 임의로 K 개의 클러스터 중심을 설정한다.
2. 문서들을 가장 가까운 중심을 가진 클러스터에 소속시킨다.
3. 새로 만들어진 클러스터들의 중심을 다시 계산하여 설정한다.
4. 오류 제곱합의 감소가 최소가 될 때가지 위의 두 과정을 반복한다.

이를 간단히 수행하기 위해 `sklearn.cluster`의 `KMeans`를 사용하면 된다.

```python
from sklearn.cluster import KMeans

군집개수 = 30
km = KMeans(n_clusters=군집개수)
km.fit(문서단어행렬)
```

군집 개수를 설정해서 `KMeans()`를 수행할 때다른  파라미터 값들도 튜닝할 수 있다.

이제 결과를 출력해보자.

```python
# 군집 번호
군집_번호 = km.labels_

# 각 군집의 문서 개수 계산
군집_문서개수 = pd.Series(군집_번호).value_counts().sort_index()

# 중심단어 추출
전체단어리스트 = vectorizer.get_feature_names_out()
중심단어리스트 = []
중심단어개수 = 10
for i in range(군집개수):
    # 중심 벡터에서 가장 근접한 단어 10개
    중심단어인덱스 = km.cluster_centers_[i].argsort()[-중심단어개수:][::-1]
    중심단어 = [전체단어리스트[idx] for idx in 중심단어인덱스]
    중심단어리스트.append(중심단어)

# 결과 출력
for i in range(군집개수):
    print(f"군집 #{i + 1} ({군집_문서개수[i]}개)")
    print(f"{' '.join(중심단어리스트[i])}")
    print()
```
