---
title: 파이썬 웹 크롤링 기초 (BeautifulSoup 사용 방법)
date: 2019-12-13
categories: [WORK, Python]
tags: [Python, 업무자동화]
---

본 포스팅에서는 파이썬으로 누구나 따라할 수 있는 웹 크롤링 방법을 소개한다.

## 들어가기에 앞서

일단 웹 사이트라는 걸 이해해야 한다. 기본적으로 웹사이트는 문서다. HTML이라는 형식으로 쓰여진 문서.

그래서 우리는 HTML 문서에 담긴 내용을 가져 오도록 request(요청) 해야 한다. 파이썬에는 애초에 `requests`라는 라이브러리로 편리하게 사용이 가능하다. (만약 설치가 안 되어 있다면 pip를 통해 설치하고 사용하자.)

이제 아래와 같이 `requests.get()`안에 url을 넣어서 사용할 수 있다. 예를 들어 [당근마켓 인기 중고 매물](https://www.daangn.com/hot_articles)을 가져오고 싶다면 이렇게.

```python
import requests

webpage = requests.get("https://www.daangn.com/hot_articles")
print(webpage.text)
```

코드를 실행하면 당근마켓 홈 화면의 HTML 문서 전체를 긁어서 출력해준다.

물론 HTML 문서를 제대로 읽으려면 문법을 이해하고 있어야 하겠지만, 너무 깊이 들어가진 말자. 어차피 여기서 필요한 부분만 뽑아내는 방법을 익히면 되는데, 여기서부터가 본격적인 크롤링이다.

## 1. BeautifulSoup 시작하기

요청한 HTML을 프린트 해보면 매우 길고 지저분하다. 그래서 결국 라이브러리의 힘을 빌려야 한다. HTML 문서를 탐색해서 원하는 부분만 쉽게 뽑아낼 수 있는 파이썬 라이브러리 BeautifulSoup를 사용해보자.

아래와 같이 코드를 추가해봤다.

```python
import requests
from bs4 import BeautifulSoup

webpage = requests.get("https://www.daangn.com/hot_articles")
soup = BeautifulSoup(webpage.content, "html.parser")

print(soup)
```

일단 `from bs4 import BeautifulSoup`로 라이브러리를 불러올 수 있다.

그리고 웹페이지를 요청한 뒤, 여기서 받아낸 문서를 `.content`로 지정한 후 `BeautifulSoup`를 통해 soup라는 객체로 저장하면 된다.

여기서 뒤에 `"html.parser"`라고 덧붙였다. 이외에도 `"lxml"`, `"html5lib"` 등의 옵션을 사용할 수 있으며 각각의 장단점이 있는데, 어쨌든 자세한 내용은 생략하고 (사실 나도 잘 모르니까) 그냥 일단 html로 하자.

## 2. 태그(Tag) 탐색하기

HTML 문서를 잘 보면 `<`, `>` 기호를 활용해서 다양한 태그를 사용한다. HTML 문법까지 설명하면 너무 깊어지니 우선 넘어가자.

어쨌든 p 태그만 찾아서 출력해보자. 이렇게 하면 첫번째 p 태그를 찾아 준다.

```python
print(soup.p)
```

```
<p>당근마켓 앱에서 따뜻한 거래를 직접 경험해보세요!</p>
```

만약 태그 속성들은 빼고 그 안에 있는 텍스트만 깔끔하게 가져오고 싶다면 이렇게 써주면 된다.

print(soup.p.string)

```
동네 주민들과 가깝고 따뜻한 거래를 지금 경험해보세요.
```

이제 h1 태그도 한 번 출력해보자.

```python
print(soup.h1)
```

```
<h1 id="fixed-bar-logo-title">
<a href="https://www.daangn.com/">
<span class="sr-only">당근마켓</span>
<img alt="당근마켓" class="fixed-logo" src="https://d1unjqcospf8gs.cloudfront.net/assets/home/base/header/logo-basic-00b7e471b721ce9db8b0758c05a84684413c8aef1ad54caa0f3fcbe7328c947f.svg"/>
</a> </h1>
```

그리고 태그는 보통 트리구조로 위계가 있기 때문에 하위 항목을 모두 뽑아오고 싶다면 `.children`을 사용하면 된다. 예를 들어 ul 태그 안에 리스트가 있다면 이렇게.

```python
for child in soup.ul.children:
    print(child)
```

```
<li class="footer-list-item"><a href="http://policy.daangn.com/terms.html" target="_blank">이용약관</a></li>
<li class="footer-list-item"><a href="http://policy.daangn.com/privacy.html" target="_blank">개인정보 취급방침</a></li>
<li class="footer-list-item"><a href="https://policy.daangn.com/location.html" target="_blank">위치기반서비스 이용약관</a></li>
<li class="footer-list-item text-bold"><a href="https://ad.daangn.com/" target="_blank">광고주센터</a></li>
<li class="footer-list-item"><a href="http://team.daangn.com" target="_blank">ABOUT US</a></li>
```

당연히 지정된 태그의 상위 항목을 가져올 수도 있다. 이건 `.parents`를 사용한다. 이건 ul 상위에 있는 body 태그를 출력한 후, 전체 html 까지 추가로 출력한다. 계속 상위로 타고 올라가는 거라 생각하면 된다.

```python
for parent in soup.ul.parents:
    print(parent)
```

이번엔 div 태그 하위에 있는 요소들을 하나씩 출력하려면 이렇게 하면 된다.

```python
for d in soup.div.children:
    print(d)
```

## 3. 웹사이트 구조 분석하기 (개발자 도구)

위와 같이 태그만으로 텍스트를 뽑아서 웹사이트 구조를 확인하는 건 너무 피곤한 일이다. 그래서 그걸 간편하게 도와줄 개발자 도구를 사용하는 게 좋다. 거의 모든 웹 브라우저는 개발자 도구를 사용할 수 있도록 되어 있다.

크롬에서는 그냥 F12 키를 누르면 바로 창이 뜬다. 내가 마우스를 어디에 갖다대는지에 따라 HTML 문서에서 해당하는 부분을 보여준다. 그러니 우리는 이걸 통해 웹사이트에 포함된 태그들을 확인하면 된다.

## 4. `find_all`을 통해 원하는 부분 가져오기

지금까지 태그를 확인하는 감을 익혔다면 이번엔 원하는 부분을 모두 가져올 수 있는 `.find_all()`을 익힐 차례다.

예를 들어 h2 태그를 모두 찾고 싶다고 하면 이렇게.

```python
print(soup.find_all("h2"))
```

그러면 결과 값을 리스트로 돌려준다. 이런 식으로

```
[<h2 class="card-title">컴퓨터.에어컨.프린터.냉장고.책상.의자.서랍장</h2>,
 <h2 class="card-title">20키로 쌀</h2>,
 <h2 class="card-title">서랍장 내놓습니다</h2>,
 <h2 class="card-title">샤넬향수랑 겔랑이딜 각3만원</h2>,
 <h2 class="card-title">김치냉장고</h2>,
 .
 .
 .
 <h2 class="card-title">샤넬 신상 클러치, 프레쉬 블랙티 코르셋크림, 이브퀵 40정, 벤진블록 S플러스</h2>,
 <h2 class="card-title">삼성 노트북</h2>,
 <h2 class="card-title">일괄 구매하실분</h2>,
 <h2 class="card-title">노스페이스 패딩</h2>,
 <h2 class="card-title">이케아 철제 선반</h2>]
```

싹 다 긁어오는 `.find_all()`이 유용한 가장 큰 이유는 정규식, html 속성, 함수 등을 사용해서 좀 더 스마트하게 내가 원하는 부분을 추출할 수 있기 때문이다.

### 1) 정규식 활용하기

예를 들어 `<ol>`이든 `<ul>`이든 뭐든 포함된 리스트를 긁어오고 싶다면 이렇게.

```python
import re
soup.find_all(re.compile("[ou]l"))
```

예를 들어 h1부터 h9까지 헤딩만 다 긁어오고 싶다면 이렇게.

```python
import re
soup.find_all(re.compile("h[1-9]"))
```

### 2) 리스트 활용하기

`.find_all()`를 사용하면 리스트로 원하는 태그들을 지정해서 뽑을 수도 있다. 예를 들어 h1, p 태그만 보고 싶다면 이렇게.

```python
soup.find_all(['h1', 'p'])
```

### 3) html 속성 활용하기

html 속성을 지정해서 뽑을 수도 있다. `.find_all()` 괄호 안에 attrs 파라미터를 지정하면 된다. 딕셔너리 형태로.

만약 introduction-detail이라는 이름의 class만 긁어오고 싶다면 이렇게.

```python
soup.find_all(attrs={'class':'card-title'})
```

당연히 속성을 여러개 지정하는 것도 가능하다.

```python
soup.find_all(attrs={'class':'footer-list', 'id':'footer-address-list'})
```

```
[<ul class="footer-list" id="footer-address-list">
<li class="footer-list-item">사업자 등록번호 : 375-87-00088</li>
<li class="footer-list-item"><address>서울특별시 강남구 테헤란로4길 14 미림타워 14층</address></li>
<li class="footer-list-item"><a href="mailto:team@daangn.com">team@daangn.com</a></li>
</ul>]
```

### 4) 함수 활용하기

원하는 부분을 가져오고 싶은데 의외로 조건이나 규칙을 지정하기 까다로울 수도 있다. 그럴 땐 함수를 만들어서 활용할 수 있다.

```python
def search_function(tag):
    return tag.attr('class') == "card-title" and tag.string == "Hello World"

soup.find_all(search_function)
```

## 5. CSS 선택자를 통해 원하는 부분 가져오기

지금까진 html 태그를 중심으로 원하는 부분을 살펴봤지만 이게 생각보다 어려울 수 있다. 해보면 안다. (나만 어렵나.)

그래서 `select()`를 통해 CSS 선택자를 활용하는 방법이 더 수월할 수도 있다.

#### 1) class 앞에는 .(점)을 찍어주기

예를 들어 card-region-name이라는 클래스를 가진 요소들만 긁어오고 싶다면 이렇게

```python
soup.select(".card-region-name")
```

```
[<div class="card-region-name">    
        경기도 수원시 팔달구 인계동
      </div>,
 <div class="card-region-name">    
        경기도 수원시 팔달구 화서동
      </div>,
 <div class="card-region-name">
        서울 마포구 용강동
      </div>,
 .
 .
 .
 <div class="card-region-name">
        서울 마포구 상암동
      </div>,
 <div class="card-region-name">
        서울 성동구 상왕십리동
      </div>,
 <div class="card-region-name">
        대구 북구 침산동
      </div>]
```

#### 2) id앞에는 #(샵)을 찍어주기

예를 들어 hot-articles-go-download라는 id를 가진 요소들만 긁어오고 싶다면 이렇게

```python
soup.select("#hot-articles-go-download")
```

아무튼 좀 까다롭긴 한데 어떻게든 긁어올 수는 있다.

## 6. 텍스트만 읽어오기

위에서 학습한 방법으로 어떤 식으로든 내가 원하는 부분을 뒤적거려서 긁어올 순 있는데, 앞 뒤로 태그가 덕지덕지 붙어 있어서 도무지 읽고 처리할 수 있는 형태가 아닐 수도 있다.

정작 내가 원하는 부분은 실제로 쓰여진 텍스트이기 때문에 그 부분만 추출하려면 `.get_text()`를 사용해야 한다.

아까 위에서 `soup.select(".card-title")`이라는 걸 통해 class가 card-title인 것들만 리스트로 모아놓았었다. 거기서 텍스트를 뽑아보고 싶다면 이렇게 하면 되겠다. 앞에 10개만 출력해보자.

```python
for x in range(0,10):
    print(soup.select(".card-title")[x].get_text())
```

일단 오늘은 여기까지.

여전히 많은 사람들이 크롤링 할 때 BeautifulSoup를 많이 쓰는 것 같긴 한데…

이제 와서 고백하자면 사실 난 이거 안 쓴다. 너무 어렵다. 애초에 내가 웹페이지의 구조를 잘 몰라서 그런 걸 수도 있지만.

대신 웹 브라우저를 아예 동작시킬 수 있는 Selenium(셀레니움)이라는 라이브러리를 사용한다. 

> BeautifulSoup가 어렵다면 Selenium으로 갈아타세요.