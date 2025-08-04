---
title: 파이썬 형태소분석기 '바른'을 활용한 텍스트 분석 – (2) 동시출현 단어 & 네트워크 시각화
date: 2024-07-18
categories: [WORK, Data]
tags: [Python, 데이터분석, 데이터시각화]
---

한국어 형태소 분석기 바른으로 형태소 분석 수행 후, 동시 출현 (Co-occurence) 단어 행렬을 만들어보고 이를 네트워크 형태로 시각화 하는 코드 예시.

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

## 동시출현 단어 분석 (어휘 공기 행렬 생성)

동시출현은 영어로 Co-occurence. 공기(共起)라 표현하기도 한다.

우선 함수부터 준비하자.

```python
from sklearn.feature_extraction.text import CountVectorizer


'''주요 품사, 용언 품사 정의'''
주요품사 = ["NNG", "NNP", "VV", "VA", "XR", "SL"]
용언품사 = ["VV", "VA" ]


'''형태소 분석 결과를 읽어서 원하는 형태소만 띄어쓰기한 결과를 수집한다.'''
def read_documents(df, col):
    문서리스트 = []
    for index, row in df.iterrows(): 
        if row[col]:
            필터링결과 = [(형태소, 품사) for (형태소, 품사) in row[col] if 품사 in 주요품사]
            필터링결과 = [형태소+"다" if 품사 in 용언품사 else 형태소 for 형태소, 품사 in 필터링결과]
        # 띄어쓰기 형식으로 수집
        문서 = " ".join(필터링결과)
        문서리스트.append(문서)
    return 문서리스트


'''문서 array로부터 문서-어휘 행렬을 생성한다.'''
def build_doc_term_mat(문서리스트):
    vectorizer = CountVectorizer(tokenizer=str.split, binary=True, max_features=50)
    문서어휘행렬 = vectorizer.fit_transform(문서리스트)
    어휘리스트 = vectorizer.get_feature_names_out()
    # 어휘 리스트 함께 돌려준다.
    return 문서어휘행렬, 어휘리스트


'''문서-어휘 행렬의 축을 바꾼 후 원래 행렬에 곱해서 어휘 공기 행렬을 생성한다.'''
def build_word_cooc_mat(문서어휘행렬):
    어휘공기행렬 = 문서어휘행렬.T * 문서어휘행렬
    # 행렬의 대각선 값을 0으로
    어휘공기행렬.setdiag(0)
    return 어휘공기행렬.toarray()
```

- `sklearn` 의 `CountVectorizer`를 활용한다.
- 용언품사는 읽기 편하도록 형태소 뒤에 “-다”를 붙여줬다.
- 문서-어휘 행렬을 생성할 때 포함할 어휘 최대 개수는 50으로 설정했다. 이때 어휘리스트는 별도로 저장해두어야 한다. 나중에 어휘공기행렬을 작성할 때 필요하기 때문에.

이제 위 이 함수를 아래와 같이 실행할 수 있다.

```python
문서리스트 = read_documents(df, '형태소분석결과')
문서어휘행렬, 어휘리스트 = build_doc_term_mat(문서리스트)
어휘공기행렬 = build_word_cooc_mat(문서어휘행렬)

'''데이터프레임 변환 후 csv 저장'''
df_어휘공기행렬 = pd.DataFrame(어휘공기행렬, columns=어휘리스트, index=어휘리스트)
df_어휘공기행렬.to_csv("./어휘공기행렬.csv", encoding="utf-8-sig", mode='w')
```

### 동시출현 단어 네트워크 시각화

위에서 구한 어휘공기행렬을 네트워크로 시각화하는 방법을 소개한다.

각 문서의 길이가 짧고, 문서 수가 충분히 많을 경우에는 이렇게 때 동시출현 빈도(어휘 공기 행렬)를 직접 사용해서 네트워크를 그려줄 수 있지만, 만약 문서의 길이가 길다면 차라리 어휘 간의 유사도를 계산해서 이를 시각화 하는 게 낫다.

```python
from scipy.spatial.distance import pdist
from scipy.spatial.distance import squareform
from itertools import combinations
from operator import itemgetter
import matplotlib.pyplot as plt
import networkx as nx



'''어휘 공기 행렬을 어휘 유사도 행렬로 변환한다.'''
def get_word_sim_mat(어휘공기행렬):
    어휘유사도행렬 = pdist(어휘공기행렬, metric='correlation')
    어휘유사도행렬 = squareform(어휘유사도행렬)
    return 어휘유사도행렬


'''어휘 유사도 행렬(또는 어휘 공기 행렬)을 정렬하여 리스트로 돌려준다.'''
def get_sorted_word_sims(어휘유사도행렬, 어휘리스트):
    sims = []
    for i, j in combinations(range(len(어휘리스트)), 2):
        if 어휘유사도행렬[i, j] == 0:
            continue
        sims.append((어휘리스트[i], 어휘리스트[j], 어휘유사도행렬[i, j]))
    행렬리스트변환 = sorted(sims, key=itemgetter(2), reverse=True)
    return 행렬리스트변환


'''네트워크를 생성하여 돌려준다.'''
def build_word_sim_network(행렬리스트변환, minimum_span=False):
    G = nx.Graph()
    NUM_MAX_WORDS = 30
    
    for word1, word2, sim in 행렬리스트변환[:NUM_MAX_WORDS]:
        G.add_edge(word1, word2, weight=sim)

    # 최소 신장 트리
    if minimum_span:
        return nx.minimum_spanning_tree(G)
    else:
        return G


'''네트워크를 시각화하여 저장한다.'''
def draw_network(G, output_filename):

    # layout 선택
    pos = nx.spring_layout(G, seed=0)
    pos = nx.kamada_kawai_layout(G)

    # nodes
    nx.draw_networkx_nodes(G, pos, node_size=800, node_color="blue")

    # edges (굵기 설정)
    weights = nx.get_edge_attributes(G,'weight').values()
    width = [ (weight-min(weights)) / (max(weights)-min(weights)) for weight in weights]
    nx.draw_networkx_edges(G, pos, width=width)

    # labels
    nx.draw_networkx_labels(G, pos,
                            font_size=7,
                            font_color='white',
                            font_weight='bold',
                            font_family="malgun gothic")

    plt.axis("off")
    plt.savefig(output_filename, dpi=300)
```

위 코드 설명은 다음과 같다.

- 어휘 공기 행렬을 바탕으로 유사도를 구할 때는 `scipy.spatial.distance`의 `pdist`를 사용한다. `metric`으로는 `correlation`, `cosine`, `euclidean` 등을 사용할 수 있다. (scipy [공식문서](https://docs.scipy.org/doc/scipy/reference/generated/scipy.spatial.distance.pdist.html)를 참고하자.)
- `get_sorted_word_sims()` 함수는 행렬의 값들을 정렬하여 리스트 형식으로 변환하는 건데, 여기서 첫 번째 인자로 어휘 유사도 행렬이 아니라 어휘 공기 행렬을 직접 사용할 수도 있다. 각 문서의 길이가 짧을 경우에는 이 방법이 더 나은 결과를 보여주기도 한다.
- 파이썬 네트워크 시각화는 보통 `networkx`라는 라이브러리를 사용한다. `NUM_MAX_WORDS로` 표현 개수를 설정했다.
- node(어휘) 간 edge(동시출현빈도)가 너무 많아서 네트워크가 복잡해질 때는 edge 수를 적절히 생략해야 하는데, 이 때 사용할 수 있는 방법 중 하나가 최소 신장 트리(Minimum Spanning Tree)다. 모든 노드를 최소의 비용으로 연결 가능하도록 링크를 최소화하여 트리를 구성한다. 필요에 따라 사용하자.
- 네트워크를 그릴 때 edge의 굵기를 0~1 사이로 설정할 수 있다.

아래와 같이 실행할 수 있다.

```python
어휘유사도행렬 = get_word_sim_mat(어휘공기행렬)
행렬리스트변환 = get_sorted_word_sims(어휘유사도행렬, 어휘리스트)
G = build_word_sim_network(행렬리스트변환)
draw_network(G, './output/어휘네트워크.png')
```

오늘 포스팅은 여기까지.