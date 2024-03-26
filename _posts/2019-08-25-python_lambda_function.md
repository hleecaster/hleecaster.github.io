---
title: 파이썬 람다(Lambda) 이해하기
date: 2019-08-25
categories: [WORK, Python]
tags: [Python]
---

파이썬 스크립트를 짜다 보면 여러번 사용할 기능은 함수로 코드를 짜는 경우가 많은데, 잠깐 쓰고 버릴 것들은 람다(Lambda) 함수를 사용하는 게 훨씬 간편하다.

처음 파이썬 공부할 때 lambda라는 게 좀 생소했는데 막상 쓰다보니 간편해서 자주 쓰게 되더라. 이 기회에 복습할 겸 메모로 개념 설명과 예시만 간단히 남겨본다.

## Lambda 함수 기초

람다는 그냥 일반적인 함수를 좀 짧게 쓴다 생각하면 편하다.

예를 들어 `x` 인풋에 10을 더해서 값을 반환하는 함수를 짠다고 하면 이렇게 써야 하는데

```python
def add_ten(x):
    return x+10
```

람다로 가볍게 한 줄로 쓸 수 있다.

```python
add_ten = lambda x: x+10
```

사용 방법은 함수와 동일하다. `add_ten(1)`이라고 하면 결과값 `11`을 반환한다.

## `if` 활용하기

`if`를 써서 조건을 걸어서 값을 반환할 수도 있다.

예를 들어 60 이상이면 ‘pass’ 그렇지 않으면 ‘fail’을 반환하는 코드를 함수로 짜면 이렇게 써야 하는데

```python
def check_score(x):
    if x>=60:
        return 'pass'
    else:
        return 'fail'
```

람다로 가볍게 한 줄로 쓸 수 있다.

```python
check_score= lambda x: 'pass' if x>=60 else 'fail'
```


 if 조건을 만족할 때 반환할 값을 먼저 쓰고, if 조건문을 쓴 후 맨 마지막에는 else 뒤에 조건을 만족하지 않을 때 반환할 값을 작성하면 된다.

> 굳이 함수를 만들지 않고 해결하고 싶을 때 일회성으로 람다를 활용하면 좋다.