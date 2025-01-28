---
title: 엑셀 VLOOKUP 함수의 완벽한 대체자 XLOOKUP 함수
date: 2019-10-04
categories: [WORK, MS Office]
tags: [Excel]
---

 엑셀 좀 사용하는 사람이라면 `VLOOKUP` 함수에 대해 모를 수가 없다. 그런데 앞으로 `VLOOKUP`은 역사속으로 사라질지도 모른다.

사실 `VLOOKUP`은 1985년에 발표된 〈Excel 1〉부터 포함된 조상급 함수다. 실제로 엑셀에서 `SUM`, `AVERAGE` 다음으로 많이 사용되는 함수라고 한다.

그런데 2019년 8월 28일에 Microsoft 공식 블로그에서 `VLOOKUP`을 완벽히 대체할 수 있는 새로운 함수 `XLOOKUP`을 소개했다.

링크: [https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-XLOOKUP/ba-p/811376](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-XLOOKUP/ba-p/811376)

## `XLOOKUP` 함수의 기능

`vlookup`은 특정 값을 범위에서 찾아 그 값이 있는 행 또는 열의 항목을 참조한다. 글로 쓰려니 설명이 어려워지는데, 그냥 `vlookup`(열에서 찾기), `hlookup`(행에서 찾기) 다 된다고 보면 된다.

```
`XLOOKUP(lookup_value,lookup_array,return_array)`
```

- `lookup_value`: 무엇을 찾을지
- `lookup_array`: 어디서 찾을지
- `return_array`: 무엇을 가져올지

범위만 잘 설정하면 끝이다. 감이 안 온다면 아래 영상을 참고하자.

![158453_EXCEL_XLOOKUP_GIF_V08.gif](https://gxcuf89792.i.lithium.com/t5/image/serverpage/image-id/127917iF0B67B064B07CA2D/image-size/large?v=1.0&px=999)

여기에 추가로 `match_mode`와 `search_mode`를 인자로 넣어줄 수도 있다.

더 자세한 사용법이 궁금하면 [도움말](https://support.office.com/ko-kr/article/xlookup-%ED%95%A8%EC%88%98-b7fd680e-6d10-43e6-84f9-88eae8bf5929)을 읽어보자.

그렇다면 엑셀에서 `XLOOKUP`이라는 새로운 함수를 내놓은 이유가 뭘까? 당연히 `VLOOKUP`을 사용할 때 이런저런 문제, 사용자가 주의해야 할 점이 있었기 때문이다. (그래서 울며 겨자먹기 식으로 `INDEX`와 `MATCH`를 사용한 복잡한 함수를 써야 할 때도 있었다.)

그러나 이제 `XLOOLUP`이 우릴 구원하러 왔다.

## XLOOKUP의 장점 (VLOOKUP의 문제점 해결)

`XLOOKUP`은 그동안 `VLOOKUP`을 사용할 때 겪는 고질적 문제들을 완벽히 해결해준다. 

1. `VLOOKUP`은 항상 첫 번째 열을 검색하고 그것의 오른쪽에 위치한 값을 반환한다. 왼쪽에 있는 값을 가져올 방법이 없는 거다. 그러나 `XLOOKUP`에서는 상하좌우 관계 없이 아무데서나 참조할 수 있다.
2. `VLOOKUP`은 반환하고자 하는 열의 번호를 숫자로 입력해야 한다. 그리고 이게 항상 문제가 된다. 왜냐하면 참조 범위 안에서 열을 삽입하거나 삭제하는 경우 `VLOOKUP` 함수 내에서도 열 번호를 맞추기 위해 직접 수정해줘야 하기 때문이다. 그러나 `XLOOKUP` 함수는 애초에 범위를 지정해서 참조하므로 참조 범위가 조정되더라도 함수를 직접 손 볼 일이 없다.
3. `VLOOKUP`은 값을 찾을 때 기본적으로 ‘정확히 일치’하지 않더라도 유사한 값을 찾는다. 그래서 반드시 마지막에 인자로 FALSE를 넣어주어야 한다. 만약 이걸 빠뜨리면 잘못된 참조를 가져올 수 있다. 그러나 `XLOOKUP`은 ‘정확히 일치’하는 값을 찾는 게 기본값이다. 그래서 실수 할 일이 없다.
4. `VLOOKUP`은 불필요하게 많은 셀을 참조해야 한다. (예를 들면 첫 번째 열에서 값을 찾아서, 저~ 오른쪽 끝에 있는 100번째 열의 값을 가져오려면 그 사이에 있는 98개의 열들도 다 참조 범위에 포함해야 한다.이 얼마나 무식한 짓인가.) 엑셀이 몇 줄 안되면 상관 없지만 만약 데이터가 커지면 괜히 불필요한 계산이 발생하는 셈이기 때문에 괜히 스프레드 시트 성능이 저하될 수 있다. 그러나 `XLOOKUP`은 검색할 열(또는 행), 참조할 열(또는 행)을 각각 필요한 부분만 지정하면 되기 때문에 이런 문제가 없다.

이 외에도 `VLOOKUP`은 값을 찾을 때에도 몇 가지 제약사항이 있었는데(예를 들면 같은 값이 있으면 무조건 맨 위의 값으로 처리한다든가), `XLOOKUP`의 `match_mode`와 `search_mode`를 활용하면 다양한 옵션으로 값을 찾아 가져올 수 있다.

참고로 `XLOOKUP`뿐만 아니라 `MATCH` 함수의 업그레이드 버전인 `XMATCH`도 나왔다. ([참고](https://support.office.com/ko-kr/article/xmatch-%ed%95%a8%ec%88%98-d966da31-7a6b-4a13-a1c6-5a33ed6a0312?ui=ko-KR&rs=ko-KR&ad=KR))

> 이제 `VLOOKUP`은 그만 쓰고, `XLOOKUP`으로 광명 찾자.
