---
title: 구글 스프레드시트에서 조건 필터링 참조하기 (IMPORTRANGE, QUERY, FILTER)
date: 2022-07-05
categories: [WORK, Excel]
tags: [Excel]
---

최근에 다른 구글 스프레드시트에서 값을 필터링해서 참조할 일이 있었는데, 생각보다 까다롭더라. 어쨌든 구글링을 해서 어찌저찌 구현하게 됐다. `IMPORTRANGE`, `QUERY`, `FILTER` 이렇게 3개 함수를 조합하면 웬만한 값은 다 불러올 수 있더라.

내가 나중에도 쓸 일이 있을 것 같아서 기록용으로 남기는 포스팅.

## 1. IMPORTRANGE

일단 가장 기본이 되는 `IMPORTRANGE` 함수는 다른 구글시트에서 값을 참조할 때 쓰는 거다. (공식 [도움말](https://support.google.com/docs/answer/3093343?hl=ko) 참고)

사용법은 아주 간단하다.

```
=IMPORTRANGE("스프레드시트URL", "참조범위")
```

## 2. QUERY

구글스프레드시트에서는 `QUERY`라는 함수를 통해 SQL에서 쿼리를 짜는 기능을 구현할 수 있다. 데이터베이스에서 검색한 값만 가져올 수 있다는 뜻이다. (공식 [도움말](https://support.google.com/docs/answer/3093343?hl=ko) 참고)

만약 참조할 시트 A:D열까지 이런 데이터가 있다고 해보자.

| |**A**|**B**|**C**|**D**|
|---|---|---|---|---|
|1|이름|나이|직업|주소|
|2|홍길동|23|도적|장성|
|3|배트맨|32|자경단|고담|
|4|슈퍼맨|34|직업|메트로폴리스|
|5|아이언맨|40|사업가|뉴욕|

여기서 이름과 직업, 즉 A열과 C열만 가져오고 싶다면 이런 식으로 함수를 짜면 된다.

```
=QUERY(IMPORTRANGE("스프레드시트URL", "시트1!A:D"),  "select Col1, Col3")
```

`QUERY(데이터, 쿼리)`와 같은 형식으로 함수를 작성하게 되는데, 일단 데이터는 `IMPORTRANGE`를 통해 가져온 범위를 입력하면 된다. 그리고 이어서 쿼리를 작성해야 하는데, 이 쿼리는 [Google Visualization API 쿼리 언어](https://developers.google.com/chart/interactive/docs/querylanguage)로 작성해야 한다. 그러나 SQL과 문법이 유사하기 때문에 너무 걱정할 필요 없다. 위 예제의 경우 1번 열, 3번 열만 가져온다는 의미로 `"select Col1, Col3"`라고 적어주었다.

## 3. FILTER

`FILTER` 함수는 특정 범위에서 조건을 걸어 필터링을 해주는 함수다. 엑셀에서도 지원하는 함수. 인수로는 데이터 범위와 조건을 넣어주면 된다. (공식 [도움말](https://support.google.com/docs/answer/3093197?hl=ko) 참고)

위 예제를 다시 보자.

| |**A**|**B**|**C**|**D**|
|---|---|---|---|---|
|1|이름|나이|직업|주소|
|2|홍길동|23|도적|장성|
|3|배트맨|32|자경단|고담|
|4|슈퍼맨|34|직업|메트로폴리스|
|5|아이언맨|40|사업가|뉴욕|

### 예제 1)

C열의 직업이 “사업가”인 경우만 필터링 하고 싶다면 이런 식으로 작성할 수 있다.

```
=FILTER(
IMPORTRANGE("스프레드시트URL", "시트1!A:D"), 
IMPORTRANGE(("스프레드시트URL","시트1!C:C")="사업가")
```

데이터 인수로는 전체 범위를 import하고, 이어서 조건 인수로는 특정 열의 조건을 걸어서 import하면 된다.

### 예제 2)

만약 이때 조건으로 C열의 직업이 “사업가”인 경우만 필터링 하면서 A열과 C열만 가져오고 싶다면 이런 식으로 query함수를 조합하면 된다.

```
=FILTER(
QUERY(IMPORTRANGE("스프레드시트URL", "시트1!A:D"),  "select Col1, Col3"),
IMPORTRANGE(("스프레드시트URL","시트1!C:C")="사업가")
```

### 예제 3)

filter 함수에서 조건을 여러개 중첩해서 사용할 경우 연산자를 활용할 수 있다. 예를 들어 예제 데이터에서 나이가 30 이상 40 미만인 값만 필터링 해서, A열과 C열만 가져오고 싶다면 이렇게.

```
=FILTER(
QUERY(IMPORTRANGE("스프레드시트URL", "시트1!A:D"),  "select Col1, Col3"),
(IMPORTRANGE(("스프레드시트URL","시트1!B:B") >=30) * (IMPORTRANGE(("스프레드시트URL","시트1!B:B") <40))
```

각 조건을 연산자로 묶어서 표현하는 방식이다. AND 조건은 `*`, OR 조건은 `+` 연산자로 표현하면 된다.