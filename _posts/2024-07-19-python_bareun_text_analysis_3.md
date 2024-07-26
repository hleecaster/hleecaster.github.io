---
title: 파이썬 형태소분석기 '바른'을 활용한 텍스트 분석 – (3) 토픽 모델링
date: 2024-07-19
categories: [WORK, Data]
tags: [Python, 데이터분석]
---

한국어 형태소 분석기 바른으로 형태소 분석 수행 후에 토픽 모델링을 해보는 코드 예시.

## 토픽 모델링이란

보통 하나의 문서는 여러 가지 토픽(주제)을 동시에 포함하고 있기 때문에 다양한 분류가 가능하다. 예를 들어, 파이썬 언어로 웹 응용 프로그램을 구현하는 내용의 문서가 있다고 한다면, 이 파이썬 관련 문서들과 묶일 수도 있고 웹 응용프로그램 관련 문서들과 엮일 수도 있다.

![토픽 모델링](https://chaelist.github.io/assets/images/ml_applied/topic_modeling.jpeg)

이와 같은 상황에서 유용한 기법이 토픽 모델링이다. 토픽 모델링은 문서들을 완전히 분리된 집단으로 나누는 것이 아니라 문서들과 몇 개의 토픽(주제)들을 연계하기 때문이다. 물론 이 토픽들은 문서들로부터 학습된 결과로 자동 생성된다. 이 때 각 문서의 특성에 따라 각 토픽과의 연계 정도, 혹은 중요도가 다르게 매겨진다. 그래서 토픽 모델링을 통해 우리는 문서 집합 내에 포함된 주요 주제, 혹은 정리된 개념들을 추출할 수 있다.

토픽 모델링에서는 [잠재 디리클레 할당(Latent Dirichlet Allocation, LDA)](https://ko.wikipedia.org/wiki/%EC%9E%A0%EC%9E%AC_%EB%94%94%EB%A6%AC%ED%81%B4%EB%A0%88_%ED%95%A0%EB%8B%B9)이라는 확률 모형을 많이 사용하는데, 깊게 들어가면 어려워서 나도 잘 모르니까 본 포스팅에서 자세한 설명은 생략한다. 궁금하면 위키피디아 읽고 공부하자.

어쨌든 이번에는 파이썬을 활용하여 토픽 모델링을 수행하는 코드 예시.

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

## 토픽 모델링

토픽 모델링은 `sklearn`의 `LatentDirichletAllocation`을 사용해서 수행할 수도 있지만, 본 예시에서는 사용법이 조금 더 쉬운 `gensim`을 사용했다. (만약 sklearn을 사용한 토픽 모델링 예시를 보고 싶다면 [이 글](https://wikidocs.net/40710)을 참고하자.)

```python
from gensim import corpora, models

'''주요 품사 정의'''
주요품사 = ["NNG", "NNP", "VV", "VA", "XR", "SL"]
용언품사 = ["VV", "VA" ]

'''형태소 분석 결과를 읽어서 주요 품사만 수집한 문서 리스트를 돌려준다.'''
def read_documents(df, col):
    문서리스트 = []
    for index, row in df.iterrows(): 
        if row[col]:
            필터링결과 = [(형태소, 품사) for (형태소, 품사) in row[col] if 품사 in 주요품사]
            필터링결과 = [형태소+"다" if 품사 in 용언품사 else 형태소 for 형태소, 품사 in 필터링결과]
        문서리스트.append(필터링결과)
    return 문서리스트


'''주어진 문서 집합으로 문서-어휘 행렬을 만들어 돌려준다.'''
def build_doc_term_mat(문서리스트):
    dictionary = corpora.Dictionary(문서리스트)
    corpus = [dictionary.doc2bow(문서) for 문서 in 문서리스트]
    return corpus, dictionary


'''문서-어휘 행렬을 TF-IDF 문서-단어 행렬로 변환한다.'''
def build_corpus_tfidf(corpus):
    tfidf = models.TfidfModel(corpus)
    corpus_tfidf = tfidf[corpus]
    return corpus_tfidf 


'''토픽별 주요 단어들을 화면에 인쇄한다.'''
def print_topic_words(model):
    NUM_TOPIC_WORDS = 10
    for topic_id in range(model.num_topics):
        topic_word_probs = model.show_topic(topic_id, NUM_TOPIC_WORDS)
        print(f"Topic ID: {topic_id}")
        for topic_word, prob in topic_word_probs:
            print(f"\t{topic_word}\t{prob:.3f}")
    print("\n")
```

이제 아래와 같이 토픽 개수를 지정하고, 아래와 같이 실행하면 결과를 얻을 수 있다.

```python
토픽개수 = 5

문서리스트 = read_documents(df, "형태소분석결과")
corpus, dictionary = build_doc_term_mat(문서리스트)
corpus_tfidf = build_corpus_tfidf(corpus)
model = models.ldamodel.LdaModel(corpus_tfidf, num_topics=토픽개수, id2word=dictionary, alpha=1)

print_topic_words(model)
```

만약 TF-IDF를 사용하고 싶지 않다면, 즉 그냥 단순한 문서-어휘 행렬을 사용하고 싶다면, `models.ldamodel.LdaModel()`를 수행할 때 corpus_tfidf가 아니라 corpus를 바로 넣어주면 된다.

어쨌든 오늘 포스팅은 여기까지.