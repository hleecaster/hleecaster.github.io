---
title: GitHub 블로그 쉽게 만들기
date: 2024-02-06
categories: [LIFE, 📝 Blog]
tags: [블로그, GitHub]
---

그동안 워드프레스와 카페24 호스팅 조합으로 블로그를 하고 있었는데, 어느 순간 관리가 버겁다는 생각이 들었다. 워드프레스와 php 버전은 계속 업데이트되니 그에 따라 덕지덕지 붙어 있는 플러그인들도 호환성도 체크해줘야 되고, https 적용도 까다롭고. 아무튼 여러모로 불편하더라.

그래서 이 기회에 Github(깃허브) 블로그로 글을 다 옮겼다. 모든 글을 표준적인 마크다운 문법으로 글을 작성하는 것이 좋겠다는 생각이 들었고, 콘텐츠 백업 걱정 할 필요도 없으며, 언제든 다른 템플릿으로 손쉽게 갈아탈 수 있기 때문에 별 고민 없이 깃허브를 선택했다.

깃허브 블로그는 어느 정도 프로그래밍을 할 줄 알아야 만들 수 있다고 생각했는데, 막상 직접 해보니 웹페이지에서 버튼 몇 개 누르고 설정만 변경해주니 바로 완성되더라. (굳이 Ruby를 설치하거나 Git을 사용하지도 않았다.)

혹시 나중에 이 작업을 반복할 일이 있을 수도 있으니 아예 깃허브에 블로그 만든 과정을 간략히 남겨두기로 했다.

## 1. jekyll 테마 고르기

대다수의 깃허브 블로그는 [jekyll(지킬)](https://jekyllrb.com/)이라는 사이트 생성기를 통해 작성된다. 나도 많은 사람들이 선택하는 방식을 따르기로 했다.

jekyll로 블로그를 만들 때 적용할 테마를 선택해야 하는데, [이 곳](https://github.com/topics/jekyll-theme)에 들어가면 어떤 테마가 가장 인기 있는지 알 수 있다. 

가장 인기가 많은 것은 [Minimal Mistakes](https://github.com/mmistakes/minimal-mistakes)이지만 이름에서 느껴지는 기대와는 달리 별로 미니멀하게 보이지 않았다. 오히려 이것저것 굳이 손대지 않아도 기본 설정이 깔끔한 [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy)가 마음에 들어서 이걸로 골랐다.

## 2. 테마 설치 (Chirpy)

내가 고른 [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) 테마는 [Getting Started](https://chirpy.cotes.page/posts/getting-started/)에 설치 방법이 매우 친절하게 적혀 있어서 이대로만 따라하면 된다. (v6.4.2 기준)

두 가지 방법이 있는데,
1. Using the Chirpy Starter
2. GitHub Fork

나는 쉽게 설치하려고 그냥 1번으로 했다.

>1. [Chirpy Starter](https://github.com/cotes2020/chirpy-starter)  
>2. `Use this template`
>3. `Create a new repository`
>4. Repository name에 `GitHubID.github.io` 입력
>5. `public` 선택 후 `Create repository`

이로써 사실상 설치는 끝난 거다.

## 3. 기본 설정

### 1) `_config.yml`파일 수정

리포지토리에 가면 `_config.yml`라는 파일이 있다. 이 파일의 내용을 아래와 같이 수정해주면 된다. 

> - lang: ko
> - timezone: Asia/Seoul
> - title: 사이트 제목
> - tagline: 사이트 부제목
> - description: 사이트 설명 (SEO 키워드)
> - url: 사이트 주소
> - github.username: 본인 깃허브 아이디
> - twitter.username: 본인 트위터 아이디
> - social.name: 본인 깃허브 아이디
> - social.email: 본인 이메일 주소
> - avatar: 사이트에 표시되는 아바타 이미지 URL

이 블로그의 [`_config.yml`](https://github.com/hleecaster/hleecaster.github.io/blob/main/_config.yml) 파일 참고.

### 2) `about.md`파일 수정

리포지토리에 가면 `_tabs` 디렉토리 내에 `about.md`라는 파일이 있다. 블로그 혹은 본인을 소개하는 내용으로 파일을 수정하면 된다.

### 2) Favicon 이미지 파일 업로드

Favicon(파비콘)은 브라우저 주소표시줄이나 북마크 같은 곳에 사용되는 아이콘인데, 이 이미지 파일을 올려줘야 한다. Chirpy 테마에서 안내하는 [도움말](https://chirpy.cotes.page/posts/customize-the-favicon/) 보고 그대로 따라하면 된다.

>1. [Real Favicon Generator](https://realfavicongenerator.net/) 접속
>2. `Select your Favicon image` 클릭하여 이미지 파일 업로드
>3. 모든 옵션 기본 값 `Generate your Favicons and HTML code` 클릭
>4. `Favicon package` 다운로드
>5. 다운로드 받은 파일 압축 풀고 `browserconfig.xml`, `site.webmanifest` 파일 삭제
>6. 나머지 파일들(`.PNG`, `.ICO`) 깃허브 리포지토리 `assets/img/favicons/`에 업로드

기본적인 설정도 끝이다.

## 4. 추가 설정

### 1) 구글 서치 콘솔 등록

우선 검색엔진이 내 사이트를 크롤링하도록 허용한다고 명시해주는 `robots.txt`가 필요하한데, 아마 위 방법대로 진행했다면 기본 값으로 생성되어 있을 거다.

이 블로그의 [`robots.txt`](https://hleecaster.github.io/robots.txt) 파일 참고.

이제 [구글 Search Console](https://search.google.com/search-console)에 내 사이트 소유권을 등록하기만 하면 된다.

Chirpy 테마는 `_config.yml` 파일에 내 서치콘솔 ID를 추가하는 것만으로 설정이 가능하다.

>1. [구글 Search Console](https://search.google.com/search-console) 속성 추가
>2. "URL 접두어"에 사이트 URL 입력
>3. "다른 확인 방법" 중 "HTML 태그" 클릭
>4. `<meta name="google-site-verification" content="{ID}"` 복사
>5. 리포지토리 `_config.yml` 파일에서 `google_site_verification`에 ID 붙여넣기

위 방법으로 서치 콘솔에서 사이트 소유권에 대한 대한 확인을 완료되었다면 "Sitemaps"에 들어가서 `sitemap.xml`라고 사이트맵을 등록해주면 된다.

### 2) 구글 애널리틱스 등록

[구글 애널리틱스](https://analytics.google.com/)에서는 사이트 방문자의 유입 경로나 접속 후 행동 패턴에 대한 데이터를 확인할 수 있다.

Chirpy 테마는 `_config.yml` 파일에 내 구글 애널리틱스 ID를 추가하는 것만으로 설정이 가능하다.

>1. [구글 애널리틱스](https://analytics.google.com/) 속성 생성
>2. (`G-`로 시작하는) `측정 ID` 복사
>3. 리포지토리 `_config.yml` 파일에서 `google_analytics`에 ID 붙여넣기


깃허브 블로그 만들기 끝.