---
title: 파이썬 형태소분석기 '바른'을 활용한 텍스트 분석 – (1) 어휘 빈도 분석 & 워드 클라우드 시각화
date: 2024-07-17
categories: [WORK, Data]
tags: [Python, 데이터분석, 데이터시각화]
---

그동안 파이썬 한국어 형태소 분석기로 [KoNLPy](https://konlpy.org/ko/latest/index.html) 이후에도 다양한 시도들이 있었다.

그 중 내가 최근까지 사용했던 형태소 분석기는 [Kiwi](https://bab2min.github.io/kiwipiepy/)였다. 성능도 괜찮다고 하고. 무엇보다 파이썬 pip만으로 편리하게 설치가 가능하다는 것이 가장 큰 장점이었기 때문.

그러던 와중에 이번에 [바른](https://bareun.ai/)이라는 형태소 분석기를 알게 되었다. 바른은 2022년 7월부터 2023년 1월까지 “언론사를 위한 형태소 분석기 개발 사업"을 통해 (주)바이칼에이아이와 한국언론진흥재단이 공동으로 개발했다고 한다.

아무튼 새로운 형태소 분석기를 알게 되었으니, 기본적인 사용을 해볼 예정.

- 바른 기본 사용 방법
- pandas 데이터프레임에서 형태소 분석 수행
- 형태소 분석 결과를 바탕으로 어휘 빈도 카운트
- 워드클라우드 시각화

## 바른 기본 사용 방법

자세한 내용은 [공식 사이트](https://bareun.ai/)에 또는 [GitHub](https://github.com/bareun-nlp/bareunpy)에 잘 나와 있다.

설치가 좀 귀찮긴 하지만, 어쨌든 공식 문서로 안내가 잘 되어 있기 때문에 따라서 설치할 수 있다.

그리고 회원 가입 후 API_KEY를 받아야 한다. 아무튼 그 API_KEY를 넣어 이렇게 준비하자.

```python
from bareunpy import Tagger

tagger = Tagger(API_KEY, 'localhost')
```

이후, 아래와 같이 리스트 안에 문장을 담아주면, 형태소(form)와 tag(품사)가 튜플형식으로 담긴 리스트를 돌려준다.

```python
result = tagger.tags(["안녕하세요. 반가워요!"]).pos()
print(result)
```

만약 형태소와 품사를 분리해서 확인하고 싶다면 이런 식으로 돌려볼 수 있겠다.

```python
result = tagger.tags(["안녕하세요. 반가워요!"]).pos()
for 형태소, 품사 in result:
    print(f"{형태소}\t{품사}")
```

많은 사람들이 흔히 위 작업을 ‘형태소 분석’이라고 부르는데 이는 엄밀히 말하면 정확한 표현이 아니다. 오히려 ‘품사 태깅’이라는 표현이 적절할 것이다. 구문을 형태소 단위로 분리한 후 각 형태소가 어떤 품사인지 태그를 달아주는 게 전부이기 때문이다.

그러나 이러한 품사 태깅은 텍스트의 양과 질, 그리고 분석의 목표나 따라 형태소 분리 결과 중 특정 품사만 선별적으로 활용하는 게 더 효과적인 상황이 있기 때문에 한국어 텍스트 분석에서는 반드시 필요하다. 아무튼.

## 형태소 분석

이제 파이썬에서 데이터를 다룰 때 널리 쓰이는 pandas 라이브러리를 사용하여 형태소 분석을 해보자.

엑셀 파일로부터 데이터를 가져와, 형태소 분석 람다 함수를 활용해서 새로운 열에 형태소 분석 결과를 작성하는 방법이다.

```python
import pandas as pd

df = pd.read_excel('rawdata.xlsx')

morph_analysis = lambda x: tagger.tags([x]).pos() if type(x) is str else None
df['형태소분석결과'] = df['원문'].apply(morph_analysis)
```

### 1) 형태소 분석 결과를 바탕으로 어휘 빈도 카운트

이제 품사 태깅이 된 결과가 있으니 원하는 품사들만 수집해서 많이 등장하는 형태소들의 빈도를 카운트해보자.

일단 함수를 준비했다.

```python
from collections import Counter


'''주요 품사, 용언 품사 정의'''
주요품사 = ['NNG', 'NNP', 'VV', 'VA', 'XR', 'SL']
용언품사 = ['VV', 'VA']


'''Counter를 활용해 가장 많이 나온 n개의 품사 결과를 돌려주는 pos_count() 함수'''
def pos_count(df, col, output_filename, n=100):
    카운터 = Counter()

    for index, row in df.iterrows():
        if row[col]:
            필터링결과 = [(형태소, 품사) for (형태소, 품사) in row[col] if 품사 in 주요품사]
            카운터.update(필터링결과)

    with open(output_filename, "w", encoding='utf-8-sig') as output_file:
        print("형태소,품사,개수", file=output_file)
        for (형태소, 품사), 개수 in 카운터.most_common(n):
            if 품사 in 용언품사:
                형태소 += "다"
            print(f"{형태소},{품사},{개수}", file=output_file)
```

위 코드 설명은 다음과 같다.

- 파이썬의 `collections.Counter()`를 사용하면 쉽게 빈도를 카운트할 수 있다.
- `주요품사`로 빈도를 카운트 할 품사들을 정의했다. (자세한 품사 분류 체계는 [공식 문서](https://bareun.ai/docs)에 제시된 품사 체계를 참고하자.)
- `용언품사`는 결과물에서 보기 좋도록 하기 위해 끝에다가 “다”를 붙여주기로 했다.
- `df.iterrows()`를 통해 모든 행을 반복하면서 수집 작업을 수행한다.
- 출력 결과는 csv 형식으로 저장한다. (인코딩은 윈도우 엑셀에서 오류 없이 열리도록 `'utf-8-sig'`로 지정했다.)

이제 아래와 같은 식으로 함수를 실행하면 된다.

```python
pos_count(df, '형태소분석결과', './output/형태소빈도.csv')
```

### 2) 워드클라우드 시각화

이번엔 흔히 하는 워드클라우드 시각화. 마찬가지로 함수를 작성하고 수행해보자.

```python
import math
from wordcloud import WordCloud
import matplotlib.pyplot as plt


'''주요 품사, 용언 품사 정의'''
주요품사 = ['NNG', 'NNP', 'VV', 'VA', 'XR', 'SL']
용언품사 = ['VV', 'VA']

stopwords = ['삭제할', '어휘', '모음', '리스트']
MAX_NUM = 50



'''주요품사의 형태소만 리스트 형식으로 수집한다.'''
def read_documents(df, col):
    # 등장하는 형태소를 list 형식으로 수집
    문서 = []
    for index, row in df.iterrows():
        if row[col]:
            필터링결과 = [(형태소, 품사) for (형태소, 품사) in row[col] if 품사 in 주요품사]
            필터링결과 = [형태소+"다" if 품사 in 용언품사 else 형태소 for 형태소, 품사 in 필터링결과]
            문서.extend(필터링결과)
    return 문서


'''어휘 빈도를 계수하여 돌려준다.'''
def get_word_count(문서, log=False):
    어휘빈도 = Counter(문서)
    # 로그함수로 빈도 평탄화
    if log:
        어휘빈도_평탄화 = {word: int(math.log2(count)) for word, count in 어휘빈도.items()}
        return 어휘빈도_평탄화
    else:
        return 어휘빈도


'''워드 클라우드를 생성하여 돌려준다.'''
def build_cloud(어휘빈도):
    cloud_gen = WordCloud(
        width=800, height=480,
        background_color="white",
        colormap ="viridis",
        font_path= "C:/Windows/Fonts/malgun.ttf",
        max_words=MAX_NUM,
        collocations=True,
        prefer_horizontal=1,
        margin=30,
        relative_scaling='auto'
    )
    cloud = cloud_gen.generate_from_frequencies(어휘빈도)
    return cloud


'''워드 클라우드 이미지를 그려 파일로 저장한다.'''
def save_cloud(df, col, output_filename):
    문서 = read_documents(df, col)
    어휘빈도 = get_word_count(문서)
  
    for stopword in stopwords:
        if stopword in 어휘빈도:
            어휘빈도.pop(stopword)

    cloud = build_cloud(어휘빈도)
    plt.imshow(cloud, interpolation="bilinear")
    plt.axis("off")
    plt.savefig(output_filename, dpi=300)
```

코드 설명은 다음과 같다.

- 주요 품사의 형태소만 수집한 리스트를 준비한다.
- 파이썬의 `collections.Counter()`를 사용하여, 리스트 내의 어휘 빈도를 카운트 한다.  그리고 이 때 로그함수로 빈도를 평탄화 하는 옵션을 추가했다. (형태소의 빈도는 해당 형태소의 중요도 또는 영향력을 나타내는 지표라고 할 수 있는데, 단순한 빈도로만 워드클라우드를 그리면 특정 고빈도 형태소가 과대하게 나타나는 경우가 있기 때문에 로그 스케일링을 하기도 한다.)

- `wordcloud.WordCloud()`를 사용하여 워드 클라우드를 만든다. 이 때 이런저런 파라미터를 조정할 수 있다.  이를 테면 max_words에서 최대 포함될 단어 수를 지정하거나, `relative_scaling`이라는 파라미터를 통해 (위에서 했던 로그 스케일링과 유사하게) 스케일링 할 수 있는 옵션 등이 있다.
- `matplotlib`을 통해 이미지 파일로 저장한다.  이때 stopwords에 포함된 삭제할 단어들을 처리하게 해두었다. 사실 `wordcloud.WordCloud()`가 기본적으로 stopwords 옵션을 제공하긴 하지만 한국어는 지원하지 않는다. 그래서 그냥 직접 처리하는 방식으로.

이제 아래와 같이 함수들을 순차적으로 실행하면 된다.

```python
save_cloud(df, '형태소분석결과', './output/워드클라우드.png')
```

일단 이번 포스팅은 여기까지.