---
title: 파이썬 리스트 컴프리헨션(List Comprehension) 이해하기
date: 2019-08-23
categories: [WORK, Python]
tags: [Python]
---

파이썬을 공부하다가 리스트 컴프리헨션(List Comprehension)을 접하게 되었다. 처음 접할 땐 살짝 어색하지만 막상 익히고 나면 정말 유용하다. 이 기회에 복습할 겸 간략히 개념 설명을 남겨두려 한다.

개인적으로 데이터를 직접 다룰 일이 많은데 그때 리스트를 요리조리 잘 변형할 수 있어서 특히 자주 사용한다.

## 파이썬 리스트 컴프리헨션 기초

예를 들어 이런 리스트가 있는데

```python
list_old = [-2, -1, 0, 1, 2, 3, 4, 5]
```

이 리스트 원소들에 각각 10을 더해서 새로운 리스트를 만들고 싶다고 하자. 이렇게.

```python
list_new = [8, 9, 10, 11, 12, 13, 14, 15]
```

어떻게 하면 될까?

가장 먼저 떠오르는 방법은 빈 리스트를 만들어 놓은 뒤, for를 활용한 반복문으로 기존 리스트에서 하나씩 불러온 값에 20을 더한 후 그걸 새로운 리스트에 하나씩 추가하는 방식이다.

이렇게

```python
list_new = []
for i in list_old:
    list_new.append(i + 10)
```

그런데 리스트 컴프리헨션은 그걸 한 줄로 작성할 수 있게 해준다.

```python
list_new = [i + 10 for i in list_old]
```

for 반복문을 뒤에 붙여 쓰는 방식으로 해결한 거다.

## 1. `if` 활용하기

for 반복문 뒤에 if를 써서 조건에 만족하는 것들만 담아 리스트를 만들 수도 있다.

예를 들어 위 예시에서 기존 리스트 중에 짝수만 10을 더해 새로운 리스트로 저장하려면 이렇게 하면 된다.

```python
list_new = [i + 10 for i in list_old if i % 2 == 0]
```

## 2. `zip()` 활용하기

`zip()`은 원소 개수가 같은 두 개 이상의 자료형을 묶어주는 기능을 한다.

만약 `list_a`와 `list_b`라는 두 리스트를 묶어 새로운 리스트를 만들고 싶다면

```python
list_a = [1, 2, 3]
list_b = [100, 2, 3]

# 원하는 결과
list_c= [[1, 100], [2, 200], [3, 300]]
```

이렇게 하면 된다.

```python
list_zipped = zip(list_a, list_b)
```

리스트 컴프리헨션에서도 `zip()`을 활용할 수 있다.

만약 `list_a`와 `list_b`에 있는 값을 쌍으로 더해서 새로운 리스트를 만들고 싶다고 하면 이렇게 하면 된다.

```python
list_sum = [x1 + x2 for (x1, x2) in zip(list_a, list_b)]
```

>리스트 컴프리헨션 쓰다 보면 너무 편해서 계속 쓰게 된다.