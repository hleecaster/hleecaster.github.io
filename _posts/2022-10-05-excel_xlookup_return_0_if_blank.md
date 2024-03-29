---
title: 엑셀 XLOOKUP 함수가 빈 셀을 0으로 가져오는 문제 해결 방법
date: 2022-10-05
categories: [WORK, Excel]
tags: [Excel]
---

 엑셀 좀 써본 사람들은 `VLOOKUP` 함수를 대부분 알 거다. 그런데 이제 더이상 `VLOOKUP`을 사용할 이유가 없어졌다. 
 
 `XLOOKUP`이라는 새로 나온 함수가 `VLOOKUP`을 너무나 편안하고 완벽하게 대체하기 때문에. 심지어 구글 스프레드시트에서도 `XLOOKUP`을 지원한다.

그런데 최근에 문제를 발견했다.

## XLOOKUP에서 값을 0으로 가져오는 문제

`XLOOKUP`을 사용하다 보면 반환할 배열에서 빈 셀을 가져올 때 0으로 값을 가져오는 문제가 발생한다. 예를 들어 A열에서 값을 찾아 B열의 값을 반환하고자 할 경우 이렇게 작성해주는데,

```
=XLOOKUP("찾을값",A:A,B:B)
```

A열에서 값을 찾아 해당 행의 B열을 조회했는데 셀이 비어있을 경우, 위와 같이 함수를 작성하면 0을 가져오게 된다. 이건 문제다. 그 셀은 0이 아닌데 0이라고 가져오는 거니까. (참고로 이 문제는 구글 스프레드 시트에서는 발생하지 않는다.)

아무튼 이 문제를 해결하는 방법은 크게 두 가지가 있다.

## 해결 방법 1. IF 함수 사용

일단 `IF` 함수를 사용해서 다소 무식하게(?) 처리하는 방법이 있다.

찾아보니 가져올 값이 공백 `""`일 경우 공백을 반환하고, 그렇지 않다면 값을 찾아 가져오는 원리.

```
=IF(XLOOKUP("찾을값",A:A,B:B)="","", XLOOKUP("찾을값",A:A,B:B))
```

그런데 이 방법은 굳이 XLOOKUP 함수를 두 번 작성해야 해서 비효율적이다.

## 해결 방법 2. LET 함수 사용

`LET` 함수를 사용하면 조금 더 간단히 처리할 수 있다. (LET 함수는 계산 결과에 이름을 할당해서, 수식 중간에서 그 값을 처리할 수 있도록 도와주는 함수다. 자세한 사용 방법을 알고 싶다면 [공식 도움말](https://support.microsoft.com/ko-kr/office/let-%ED%95%A8%EC%88%98-34842dd8-b92b-4d3f-b325-b8b8f9908999)을 참고하자.)

아무튼 아래와 같이 LET 함수 안에 XLOOKUP를 작성해서 처리할 수 있다.

```
=LET(x, XLOOKUP("찾을값", A:A,B:B), IF(x="", "", x))
```

x라는 값을 정의하여 처리하는 방식.