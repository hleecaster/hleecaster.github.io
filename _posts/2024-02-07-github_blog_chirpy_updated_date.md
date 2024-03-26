---
title: GitHub 블로그에서 게시물 최종 수정 날짜 숨기는 방법 (Chirpy)
date: 2024-02-07
categories: [LIFE, Blog]
tags: [블로그, GitHub]
---

깃허브 블로그에 글을 쓰다 보면 내가 원하는 대로 양식이 적용이 안 됐다거나 사소한 오타를 발견해 나중에 글을 수정해야 하는 경우가 있다.

나는 [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) 테마를 [Chirpy Starter](https://github.com/cotes2020/chirpy-starter) 방식으로 설치해서 사용하고 있는데, 이 테마는 글을 수정하면 Updated라고 해서 수정 게시된 날짜를 함께 표시해준다. (v6.4.2 기준)

오래 전에 작성한 글인데, 그렇다고 본문 내용을 현 시점 기준으로 업데이트 한 것도 아닌데 굳이 사소한 수정을 했다는 이유 때문에 수정 게시된 날짜가 표시되는 게 보기 좋지 않아서 이걸 숨김 처리 할 방법을 찾아보았다.

## Jekyll Chirpy 테마에서 게시물 최종 수정 날짜 숨기는 방법

리포지토리에 설치된 내용을 살펴보니 `_plugins` 디렉토리 안의 `posts-lastmod-hook.rb` 파일에서 수정 날짜 관련 내용을 설정하고 있었다. (아마 테마 초기 개발 단계에서는 포함되지 않았던 코드였으나 추후에 추가된 기능이라 플러그인 형식으로 적용된 게 아닐까 싶다.) 

```rb
#
# Check for changed posts

Jekyll::Hooks.register :posts, :post_init do |post|

  commit_num = `git rev-list --count HEAD "#{ post.path }"`

  if commit_num.to_i > 1
    lastmod_date = `git log -1 --pretty="%ad" --date=iso "#{ post.path }"`
    post.data['last_modified_at'] = lastmod_date
  end

end
```

코드를 살펴보니 Ruby로 작성된 거 같은데, 만약 commit 횟수가 1 보다 클 경우, 최종 수정 날짜는 파일이 최종적으로 commit된 날짜를 따르는 것으로 보인다.

그러면 리포지토리에서 commit 내역을 모두 지우면 되지 않겠냐는 질문이 따를 수 있는데, 그건 생각보다 간단한 문제가 아니다. 

그냥 여기 있는 코드를 통째로 주석 처리 해주자. 그러면 게시물을 수정하더라도 더 이상 "Updated"라는 수정 날짜가 보이지 않게 된다.