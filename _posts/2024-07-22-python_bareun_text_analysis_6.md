---
title: 파이썬 형태소분석기 '바른'을 활용한 텍스트 분석 – (6) 문서 분류
date: 2024-07-22
categories: [WORK, Data]
tags: [Python, 데이터분석]
---

## 문서 분류란

문서 분류(Classification)는 주어진 문서에 대하여 미리 정해진 하나 혹은 복수의 부류(class, label)을 부여하는 절차를 말한다. 

가장 대표적인 예는 스팸 메일 분류기이다. 스팸인지 아닌지 참 또는 거짓, 0 또는 1 등 두 개의 값을 가지는 경우는 이진 분류(Binary classification)를 수행한다. 반면, 클래스/레이블이 여러 개인 경우 다항 분류(Multiclass classification)를 할 수도 있다. 

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


## 나이브 베이즈 문서 분류

### 1) 문서 준비

우선 공백으로 연결된 문서 형식으로 변환해서 넣어줘야 하기 때문에, 데이터를 준비하자.

```python
'''주요 품사 정의'''
주요품사 = ["NNG", "NNP", "NP" "VV", "VA", "MAG", "XR"]
용언품사 = ["VV", "VA"]

'''" "으로 구분되는 문서 형식으로 변경'''
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

'''공백으로 연결된 문서 준비'''
df["문서"] = df["형태소분석결과"].apply(to_document_format)
```

### 2) 벡터라이저~분류기 파이프라인 생성

이제 문서의 벡터화, 이후 적용할 나이브 베이즈 분류 모델까지 담은 파이프라인을 생성한다. 파이프라인을 사용하는 이유는 분류 성능을 확인하여 파라미터 튜닝할 때 편리하기 때문이다.

```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.naive_bayes import MultinomialNB
from sklearn.pipeline import Pipeline


# 파라미터 튜닝은 나중에 GridSearchCV를 활용. 일단 테스트로 생성해보기
vectorizer = TfidfVectorizer()
clf = MultinomialNB()

pipeline = Pipeline([('vect', vectorizer),('clf', clf)])
pipeline.fit(df["문서"], df["레이블"])
```

추후에 분류기의 성능을 높이기 위해서는 `TfidfVectorizer()`와 `MultinomialNB()` 안에 이런저런 파라미터를 튜닝해야 하는데, 지금은 그냥 기본값으로 생성했다. (추후에 파이프라인을 사용해서 최적의 파라미터 조합을 찾아볼 예정이다.)

우선 벡터화 과정에서 생성에 사용한 단어와 모든 문서에서 등장한 빈도를 확인해볼 수 있다.

```python
# 각 단어의 등장 빈도 확인
pipeline.named_steps['vect'].vocabulary_
```

그리고 물론 이 상태에서 바로 레이블 예측도 가능하다.

```python
# 분류 예측
print(pipeline.predict(["회의 시간 비효율 보고 업무"]))
```

### 3) 그리드서치를 활용한 최적의 파라미터 찾기

`GridSearchCV`를 사용하면 다양한 조합으로 모델을 생성하여 비교해보고 최고의 성능을 내는 파라미터가 무엇인지 돌려준다.

학습 데이터로 확인해보자.

```python
from sklearn.model_selection import GridSearchCV
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score


# 학습 데이터 분리
train_documents, test_documents, train_labels, test_labels = train_test_split(df["문서"], df["레이블"])

# 파라미터 옵션
parameters = {
    "vect__max_features": (1000, 1500, 2000, 2500, 3000, ), 
    "vect__ngram_range": ((1,1), (1,2), (1,3)), # ((1,1) , (1,2), (1,3))
    "vect__binary": (True, False), # (True, False)
    "vect__use_idf": (True, False), # (True, False)
    "vect__smooth_idf": (True, False), # (True, False)
    "vect__sublinear_tf": (True, False), # (True, False)
    "vect__norm": (None, "l1", "l2"), # (None, "l1", "l2")
    "clf__alpha": (0.0, 0.5, 1.0), # (0.0, 0.5, 1.0)
}

# 그리드서치
grid_search = GridSearchCV(pipeline, parameters, 
                           scoring="accuracy", # 성능지표 metrics 지정
                           cv=5, # 교차검증 횟수
                           verbose=1, # 정보 출력
                           n_jobs=-1, # 모든 CPU 코어를 사용한 병렬 처리
                           )
grid_search.fit(train_documents, train_labels)
```

이제 최고 점수는 몇 점을 받았는지, 이 때 파라미터는 무엇인지 확인해보자.

```python
# 최고 점수
print(f"Best score: {grid_search.best_score_:.2}")
# 최적의 파라미터 조합
print(f"Best parameter set: {grid_search.best_params_}")
```

이렇게 생성한 모델로 평가 데이터를 예측하여 정확도를 확인해보자.

```python
# 예측 테스트
pred_labels = grid_search.predict(test_documents)
print(f"Accurary: {accuracy_score(test_labels, pred_labels):.2}")
```

### 4) 최적의 파라미터 조합으로 모델 생성

위에서 그리드서치를 통해 얻은 파라미터를 그대로 적용해서 다시 한 번 분류기를 생성해보자.

```python
vectorizer = TfidfVectorizer(max_features=grid_search.best_params_["vect__max_features"], 
                             ngram_range=grid_search.best_params_["vect__ngram_range"], 
                             binary=grid_search.best_params_["vect__binary"],
                             use_idf=grid_search.best_params_["vect__use_idf"], 
                             smooth_idf=grid_search.best_params_["vect__smooth_idf"], 
                             sublinear_tf=grid_search.best_params_["vect__sublinear_tf"],
                             norm=grid_search.best_params_["vect__norm"], 
                             )

clf = MultinomialNB(alpha=grid_search.best_params_["clf__alpha"])


pipeline = Pipeline([('vect', vectorizer),('clf', clf)])
pipeline.fit(df["문서"], df["레이블"])
```

### 5) 분류 모델 저장하기/불러오기

위에서 생성한 분류기는 `joblib`을 통해 저장하거나 불러와서 바로 써먹을 수 있다. 

```python
import joblib

# 모델 저장
joblib.dump(pipeline, "./output/나이브베이즈 문서 분류기.model")

# 모델 불러오기/사용하기
pipeline = joblib.load("./output/나이브베이즈 문서 분류기.model")
print(pipeline.predict(["회의 시간 비효율 보고 업무"]))

```

