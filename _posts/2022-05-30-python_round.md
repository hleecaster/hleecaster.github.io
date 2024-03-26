---
title: 파이썬에서 사사오입으로 반올림 처리하기 (엑셀처럼 반올림)
date: 2022-05-30
categories: [WORK, Python]
tags: [데이터분석, Python, Excel]
---

파이썬에서 반올림 함수 `round`를 쓰다 보면 이상한 현상을 마주하게 된다. 내가 원하는 건 그저 소수점 1자리로 반올림하는 그 단순한 계산이었는데, 엑셀에서 구한 값과 파이썬으로 계산한 값의 결과가 다른 걸 발견하고, 이 문제를 해결하기 위해 온갖 구글링을 했다.

어쨌든 원인도 해결 방법도 모두 알아냈으니, 나중에 혹시 또 이런 일이 있으면 코드 써먹으려고 남겨두는 포스팅.

## 문제 1) 사사오입 vs 오사오입

일단 파이썬에서 단순히 반올림할 때 일어나는 일을 보자.

```python
print(round(0.5))   # 0 ?
print(round(1.5))   # 2
print(round(2.5))   # 2 ?
print(round(3.5))   # 4
print(round(4.5))   # 4 ?
print(round(5.5))   # 6
print(round(6.5))   # 6 ?
print(round(7.5))   # 8
print(round(8.5))   # 8 ?
print(round(9.5))   # 10
print(round(10.5))  # 10 ?
```

우리가 일반적으로 알고 있는 반올림과 결과가 다르다는 걸 알 수 있다. 우리가 흔히 알고 있는 반올림 방법은 “사사오입“, 즉 4 이하의 숫자는 내림, 5 이상의 숫자는 올리는 방법이다.

그러나 파이썬에서는 “오사오입“을 적용한다. 5 미만의 숫자는 내림, 5 초과의 숫자는 올림, 그리고 반올림할 자릿수가 5일때는 5의 앞자리가 홀수인 경우 올림, 짝수인 경우 내린다.

엄밀히 따지면 오사오입이 더 오차가 적은 반올림 방법이라고도 할 수 있는데, 자세히 알고 싶다면 [위키피디아](https://ko.wikipedia.org/wiki/%EB%B0%98%EC%98%AC%EB%A6%BC)나 혹은 다른 문서를 참고하자. 아무튼 반올림 문제는 생각보다 간단하지 않다.

## 문제 2) 부동 소수점

이번엔 소수점 둘째 자리에서 첫째 자리로 반올림하는 경우를 보자.

```python
print(round(0.05, 1))   # 0.1
print(round(0.15, 1))   # 0.1 ?
print(round(0.25, 1))   # 0.2 ?
print(round(0.35, 1))   # 0.3 ?
print(round(0.45, 1))   # 0.5
print(round(0.55, 1))   # 0.6
print(round(0.65, 1))   # 0.7
print(round(0.75, 1))   # 0.8
print(round(0.85, 1))   # 0.8 ?
print(round(0.95, 1))   # 0.9 ?
print(round(1.05, 1))   # 1.1
```

역시나 뭔가 결과가 이상하다. 그런데 패턴을 보면 위에서 얘기한 사사오입 vs 오사오입 규칙으로도 설명이 안 된다.

이건 파이썬이라는 프로그래밍 언어가 실수 자료형 `float`를 다룰 때 발생하는 [부동소수점](https://ko.wikipedia.org/wiki/%EB%B6%80%EB%8F%99%EC%86%8C%EC%88%98%EC%A0%90) 문제다. 이건 문제가 더 어려우니 자세한 설명은 생략한다.

## 해결 방법 1) decimal 표준 라이브러리 모듈 사용하기

[decimal](https://docs.python.org/ko/3/library/decimal.html)은 부동소수점 문제를 해결하기 위한 파이썬 표준 라이브러리이지만, 사사오입 vs 오사오입 문제를 해결하진 못하기 때문에 엄밀히 말하면 이건 해결책이 아니다.

## 해결 방법 2) 함수 만들어서 사용하기

결국 [stackoverflow](https://stackoverflow.com/a/38239574)에서 구원을 찾았다.

```python
def roundTraditional(val, digits):
    return round(val+10(-len(str(val))-1), digits)

print(roundTraditional(0.5, 0)) # 1.0
print(roundTraditional(1.5, 0)) # 2.0
print(roundTraditional(2.5, 0)) # 3.0
print(roundTraditional(3.5, 0)) # 4.0
print(roundTraditional(4.5, 0)) # 5.0
print(roundTraditional(5.5, 0)) # 6.0
print(roundTraditional(6.5, 0)) # 7.0
print(roundTraditional(7.5, 0)) # 8.0
print(roundTraditional(8.5, 0)) # 9.0
print(roundTraditional(9.5, 0)) # 10.0

print(roundTraditional(0.05, 1))    # 0.1
print(roundTraditional(0.15, 1))    # 0.2
print(roundTraditional(0.25, 1))    # 0.3
print(roundTraditional(0.35, 1))    # 0.4
print(roundTraditional(0.45, 1))    # 0.5
print(roundTraditional(0.55, 1))    # 0.6
print(roundTraditional(0.65, 1))    # 0.7
print(roundTraditional(0.75, 1))    # 0.8
print(roundTraditional(0.85, 1))    # 0.9
print(roundTraditional(0.95, 1))    # 1.0
```

엑셀에서 반올림 표현하는 것과 똑같이 사사오입으로 값을 내주고, 부동소수점 문제도 발생하지 않는다.