---
title: GitHub 블로그 네이버 서치어드바이저 등록하기
date: 2024-02-09
categories: [LIFE, Blog]
tags: [블로그, GitHub]
---

깃허브(GitHub) 페이지 기능으로 만든 내 블로그가 네이버 검색 엔진에 노출되도록 하는 방법.

## 사이트 등록 (소유권 확인)

우선 네이버 [웹마스터도구](https://searchadvisor.naver.com/console/board)에 내가 소유한 사이트를 등록해야 한다.

내 사이트 URL을 입력해주면, 소유권 확인할 수 있는 방법 두 가지를 제안하는데,
1. HTML 파일 업로드 : 네이버에서 제공한 HTML 파일을 내 사이트 루트 디렉토리에 업로드하기
2. HTML 태그 : 메타 태그를 내 사이트 `<head>` 섹션에 포함시키기

나는 1번으로 진행했다. 네이버에서 제공한 HTML 파일을 다운로드 받아 내 리포지토리 루트 디렉토리에 올려두고 "소유확인" 누르면 끝이다.

내가 소유한 사이트라는 게 확인되면, 해야 할 일이 두 가지 더 있다.

### 1) 사이트맵 제출

[사이트맵](https://en.wikipedia.org/wiki/Sitemaps)은 검색로봇이 내 사이트에 방문했을 때, 이 사이트의 전체적인 구조와 수집해야 할 페이지들을 정리해놓은 문서이다. 그래서 사이트맵을 네이버에 제출해야 검색로봇이 내 사이트의 콘텐츠를 더 잘 수집해갈 수 있다.

내 블로그의 경우 Jekyll로 만들어진 사이트인데, 이 경우 루트 디렉토리에 `sitemap.xml`이라는 사이트맵이 존재한다. ([Jekyll 공식문서 참고](https://jekyllrb.com/tutorials/convert-site-to-jekyll/#11-add-a-sitemap))

네이버 웹마스터도구 "요청" 탭에 가면 "사이트맵 제출"이라는 메뉴가 있다. 여기 가서 내 사이트맵 URL [https://hleecaster.github.io/sitemap.xml](https://hleecaster.github.io/sitemap.xml)를 넣어주면 끝. 

### 2) RSS 제출

RSS는 어떤 사이트에 새로운 콘텐츠가 올라왔을 때 해당 사이트에 직접 방문하지 않더라도 리더를 통해 그 콘텐츠를 받아보는 개념이다. 그래서 네이버에 RSS 피드를 제출해놓으면 네이버가 내 사이트를 구독한다는 의미.

Jekyll로 만든 깃허브 블로그는 애초에 루트 디렉토리에 `feed.xml`라는 RSS 파일을 제공한다. ([Jekyll 공식문서 참고](https://jekyllrb.com/tutorials/convert-site-to-jekyll/#10-rss-feed)) 

그런데 약간의 문제가 있다. Jekyll에서 기본으로 제공하는 RSS는 Atom이라는 형식의 RSS 피드인데 반해, 네이버는 RSS 2.0버전의 방식의 피드만 지원하기 때문이다. 그래서 RSS 2.0 버전의 피드 문서를 새로 만들어서 그걸로 네이버에 등록해줘야 한다.

다행히 [이 곳](https://jekyllcodex.org/without-plugin/rss-feed/)에서 Jekyll이 안내하는 RSS 2.0 피드 문서 형식을 확인하고 다운로드 받을 수 있다. 

나는 애초에 생성되어있는 Atom 형식의 `feed.xml`을 유지하고 싶어서, 새로 내려받은 문서를 `rss.xml`라고 이름 지어 루트 디렉토리에 넣어주었다. 그리고 문서 내용 중 `feed.xml`이라고 되어 있는 부분만 `rss.xml`로 수정해주었다. (네이버에 제출할 RSS 2.0 버전 피드를 별도로 만들어놓은 셈.)

아래 둘을 비교해보면 형식이 미묘하게 다른 걸 알 수 있다.
- Atom: https://hleecaster.github.io/feed.xml (jekyll 기본 제공)
- RSS 2.0 : https://hleecaster.github.io/rss.xml

아무튼 네이버에 RSS 제출할 때는 RSS 2.0 버전으로 제출해주면 정상적으로 등록이 잘 된다.
