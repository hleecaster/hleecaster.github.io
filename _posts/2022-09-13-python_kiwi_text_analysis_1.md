---
title: 파이썬 형태소분석기 Kiwi를 활용한 텍스트 분석 – (1) 어휘 빈도 분석 & 워드 클라우드 시각화
date: 2022-09-13
categories: [WORK, Data]
tags: [Python, 데이터분석, 데이터시각화]
---

그동안 파이썬을 활용해 한국어 형태소 분석을 하려면 [KoNLPy](https://konlpy.org/ko/latest/index.html) 이외에는 마땅한 대안이 없었다고 해도 과언이 아니다.

물론 KoNLPy 패키지는 무려 5개의 형태소 태깅 클래스(Kkma, Hannanum, Komoran, Mecab, Okt)를 포함하고 있었기 때문에 원하는 성능을 보여주는 것을 골라 사용할 수 있는 장점이 있긴 했다.

그러나 이것들은 대부분 〈21세기 세종계획〉이라는 국가 주도 프로젝트의 결과물인 세종 말뭉치를 학습 데이터로 사용하고 있었는데, 이 프로젝트도 사실 1998년부터 2007년까지 10년 동안 시행된, 꽤나 오래된 것이었다.

게다가 KoNLPy의 가장 큰 단점이면 바로 자바 의존성이다. 윈도우 운영체제에서 KoNLPy를 설치하려면 사용자 PC에 JDK가 깔려 있어야 한다는 것. 그러나 파이썬 생태계에서는 다른 대안이 없었기 때문에 결국 많은 사람들이 KoNLPy를 사용할 수밖에 없었다.

그러던 와중 최근(?)에 나온 한국어 형태소 분석기 [Kiwi](https://bab2min.github.io/kiwipiepy/)가 꽤 괜찮은 성능을 발휘한다는 소식을 접했다. 게다가 Kiwi는 (소개 글에 나온 바와 같이) C++로 작성되었고 다른 패키지에 의존성이 없으므로 C++ 컴파일이 가능한 환경이라면 어디에서나 사용 가능하다는 장점이 있다. 즉, JDK 같은 거 설치할 필요 없이 그냥 파이썬 pip만으로 설치가 가능하다는 거다.

그래서 이번에 한국어 텍스트 분석할 일이 있을 때 [Kiwi](https://bab2min.github.io/kiwipiepy/)를 써봤고, 기왕 쓴 김에 남기는 코드 예시.

목차 다음과 같음.

- kiwi 기본 사용 방법
- pandas 데이터프레임에서 형태소 분석 수행
- 형태소 분석 결과를 바탕으로 어휘 빈도 카운트
- 워드클라우드 시각화

## kiwi 기본 사용 방법

자세한 내용은 [공식 문서](https://bab2min.github.io/kiwipiepy/)에 또는 [GitHub](https://github.com/bab2min/kiwipiepy)에 잘 나와 있다.

```python
from kiwipiepy import Kiwi
kiwi = Kiwi()
kiwi.load_user_dictionary('user_dictionary.txt')
kiwi.prepare()
```

이렇게 준비하면 되는데, `kiwi.load_user_dictionary()`는 사용자 사전을 불러와서 사용한다는 의미다.

이후 `tokenize`를 사용해서 형태소 분석을 수행하면, 각 형태소가 token이라는 형식으로 담긴 list를 돌려준다.

```python
kiwi.tokenize('테스트입니다.')
```

만약 token에서 형태소(form)와 tag(품사)를 분리해서 확인하고 싶다면 이런 식으로 돌려볼 수 있겠다.

```python
result = kiwi.tokenize('테스트입니다.')
for token in result:
	print(f"{token.form}\t{token.tag}")
```

많은 사람들이 흔히 위 작업을 ‘형태소 분석’이라고 부르는데 이는 엄밀히 말하면 정확한 표현이 아니다. 오히려 ‘품사 태깅’이라는 표현이 적절할 것이다. 구문을 형태소 단위로 분리한 후 각 형태소가 어떤 품사인지 태그를 달아주는 게 전부이기 때문이다.

그러나 이러한 품사 태깅은 텍스트의 양과 질, 그리고 분석의 목표나 따라 형태소 분리 결과 중 특정 품사만 선별적으로 활용하는 게 더 효과적인 상황이 있기 때문에 한국어 텍스트 분석에서는 반드시 필요하다. 아무튼.

## pandas 데이터프레임에서 형태소 분석 수행

이제 파이썬에서 데이터를 다룰 때 널리 쓰이는 pandas 라이브러리를 사용하여 형태소 분석을 해보자.

엑셀 파일로부터 데이터를 가져와, 형태소 분석 람다 함수를 활용해서 새로운 열에 형태소 분석 결과를 작성하는 방법이다.

```python
import pandas as pd

df = pd.read_excel('rawdata.xlsx')

morph_analysis = lambda x: kiwi.tokenize(x) if type(x) is str else None
df['형태소분석결과'] = df['원문'].apply(morph_analysis)
```

## 형태소 분석 결과를 바탕으로 어휘 빈도 카운트

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
            필터링결과 = [(token.form, token.tag) for token in row[col] if token.tag in 주요품사]
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
- `주요품사`로 빈도를 카운트 할 품사들을 정의했다. (자세한 품사 분류 체계는 [여기](https://github.com/bab2min/kiwipiepy#%ED%92%88%EC%82%AC-%ED%83%9C%EA%B7%B8)를 참고하자.)
- `용언품사`는 결과물에서 보기 좋도록 하기 위해 끝에다가 “다”를 붙여주기로 했다.
- `df.iterrows()`를 통해 모든 행을 반복하면서 수집 작업을 수행한다.
- 출력 결과는 csv 형식으로 저장한다. (인코딩은 윈도우 엑셀에서 오류 없이 열리도록 `'utf-8-sig'`로 지정했다.)

이제 아래와 같은 식으로 함수를 실행하면 된다.

```python
pos_count(df, '형태소분석결과', './주요어휘빈도.csv')
```

## 워드클라우드 시각화

이번엔 흔히 하는 워드클라우드 시각화. 마찬가지로 함수를 작성하고 수행해보자.

```python
import math
from wordcloud import WordCloud
import matplotlib.pyplot as plt


'''주요 품사, 용언 품사 정의'''
주요품사 = ['NNG', 'NNP', 'VV', 'VA', 'XR', 'SL']
용언품사 = ['VV', 'VA']
stopwords = ['삭제할', '어휘', '모음', '리스트']


'''주요품사의 형태소만 리스트 형식으로 수집한다.'''
def read_documents(df, col):
    # 등장하는 형태소를 list 형식으로 수집
    문서 = []
    for index, row in df.iterrows(): 
        if row[col]:
            필터링결과 = [(token.form, token.tag) for token in row[col] if token.tag in 주요품사]
            필터링결과 = [form+"다" if tag in 용언품사 else form for form, tag in 필터링결과]
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
        max_words=50,
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
save_cloud(df, '형태소분석결과', './워드클라우드.png')
```

일단 이번 포스팅은 여기까지.