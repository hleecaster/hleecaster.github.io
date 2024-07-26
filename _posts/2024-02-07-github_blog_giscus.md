---
title: GitHub 블로그에 댓글 기능 추가하기 (giscus)
date: 2024-02-07
categories: [LIFE, Blog]
tags: [블로그, GitHub]
---

깃허브 블로그에 댓글 기능을 추가해보기로 했다. 

후보는 disqus, utterances, giscus 이렇게 셋. 이 중 뭘 쓸까 고민했는데,

- [disqus](https://disqus.com/) : 조잡하고 광고 있음. 탈락
- [utterances](https://github.com/utterance/utterances) : 깔끔하고 광고 없음. 깃허브 `Issues` 기능 활용. (후보1)
- [giscus](https://github.com/apps/giscus) : utterances의 상위 호환. 깃허브 `Discussions` 기능 활용. (후보2)

결국 giscus를 택했다. 

깃허브 아이디로 로그인을 해야만 댓글을 달 수 있다는 게 아쉬운 점이긴 하지만 어쩔 수 없지.

## 깃허브 블로그에 giscus 추가하기

### 1. 리포지토리 Discussions 활성화

깃허브 리포지토리 "Settings" 메뉴에 들어가면 "Features" 부분에 "Discussions" 항목이 있는데, 이를 체크해주면 [Discussions](https://github.com/hleecaster/hleecaster.github.io/discussions) 기능이 활성화 된다. (giscus에서는 댓글이 달리면 여기로 들어온다.)

이후 Discussions에 가보면 카테고리(유형) 설정이 가능한데, 나는 댓글 용도로 "Comments"라는 이름의 카테고리를 추가했다. 참고로 댓글을 위한 카테고리를 추가할 때 Discussion Format은 `Announcement`로 지정해주어야 한다.

### 2. 리포지토리에 giscus 앱 설치

[https://github.com/apps/giscus](https://github.com/apps/giscus)에 접속하여 "Install" 클릭하고, "Only select repositories" 에서 내 블로그 리포지토리를 선택하면 된다.

### 3. giscus 정보 확인

[https://giscus.app/ko](https://giscus.app/ko)에 접속하여 "설정" 아래 부분 확인을 설정하면 되는데,

- 저장소: 내 리포지토리 주소 입력하여 검증
- Discussion 카테고리: 댓글 기능으로 사용할 카테고리 선택

이것만 잘 설정하고 나머지는 기본값으로 두면 된다. 

이후 스크롤을 내려 "giscus 사용"에서 `<script>` 태그 내용을 확인하자. 추후 `data-repo`, `data-repo-id`, `data-category`, `data-category-id` 이 네 개 정보를 사용하게 된다.

#### STEP 4. `_config.yml` 파일 수정

리포지토리에 가면 `_config.yml`라는 파일이 있는데, 위에서 확인한 `<script>` 태그 정보를 가지고 아래 내용을 채워주면 된다.

> - comments:
> 	- active: 'giscus'
> 	- giscus:
> 		- repo: `data-repo`
> 		- repo_id: `data-repo-id`
> 	    - category: `data-category`
> 	    - category_id: `data-category-id`

지금 이 블로그에 설정된 [`_config.yml`](https://github.com/hleecaster/hleecaster.github.io/blob/main/_config.yml) 파일 참고.

깃허브 블로그 댓글 기능 추가하기 끝.