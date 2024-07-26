---
title: 파이썬 딕셔너리 key 또는 value 기준으로 정렬하기
date: 2022-10-26
categories: [WORK, Python]
tags: [Python]
---

파이썬 3.6 이전에는 딕셔너리에서 순서, 정렬이라는 개념이 없었다. [해시 테이블](https://en.wikipedia.org/wiki/Hash_table) 자료구조였기 때문이다.

그래서 만약 순서를 보존하는 딕셔너리를 사용하려면 [collections](https://docs.python.org/ko/3/library/collections.html) 모듈의 `OrderedDict`를 사용해야 했다. 아니면 키-값의 쌍의 튜플 리스트로 만들어서 다루는 방법을 쓰기도 했다.

그러는 파이썬 3.7 부터는 딕셔너리를 키 또는 값 기준으로 정렬할 수 있다. (엄밀히 얘기하면 딕셔너리의 순서를 재배열 하는 건 아니다. 실제로 딕셔너리를 순서를 재배열하려면 `del` 키워드를 사용해서 해당 항목을 삭제한 다음 다시 추가해야 한다. 삭제했다가 다시 추가하면 키-값 쌍이 끝으로 온전히 이동한다. 이에 대해 자세한 설명은 생략한다. 넘어가자.)

## 파이썬 딕셔너리 정렬 방법 – 결론

파이썬에서 딕셔너리를 정렬한다고 하면 키 또는 값을 정렬 기준으로 하게 될 거다.

방법은 아래와 같다.

```python
people = {3: "Jim", 2: "Jack", 4: "Jane", 1: "Jill"}

# Sort by key
dict(sorted(people.items()))
# output: {1: 'Jill', 2: 'Jack', 3: 'Jim', 4: 'Jane'}

# Sort by value
dict(sorted(people.items(), key=lambda item: item[1]))
# output: {2: 'Jack', 4: 'Jane', 1: 'Jill', 3: 'Jim'}
```

보다시피 파이썬 내장함수 `sorted()`를 사용한다. 이 함수 사용법에 대해 좀 더 살펴보자.

### `sorted()` 함수 기본 사용법

`sorted()` 함수는 iterable을 기본 인수로 사용한다. 이런 식으로.

```python
numbers = [5, 3, 4, 3, 6, 7, 3, 2, 3, 4, 1]
sorted(numbers)
# output: [1, 2, 3, 3, 3, 3, 4, 4, 5, 6, 7]

words = ["aa", "ab", "ac", "ba", "cb", "ca"]
sorted(words)
# output: ['aa', 'ab', 'ac', 'ba', 'ca', 'cb']
```

`sorted()` 함수는 두 개의 파라미터 key, reverse 를 선택적으로 사용할 수 있다.

- `key`는 어떤 값을 기준으로 정렬할 것인가
- `reverse`는 정렬 순서를 역순으로 할 것인지 (True 또는 False)

여기서 사용하는 파라미터 key는 sorted 함수의 정렬 기준을 설정하는 것이므로 딕셔너리의 키와 헷갈리지 말자.

참고로 `sorted()`말고 `.sort()` 메서드를 사용할 수도 있는데, `sorted()`는 원본을 유지한 채 새로 정렬된 결과를 돌려주는 반면, `.sort()`는 원본을 직접 수정/정렬한다.

### `sorted()` 함수를 딕셔너리에 적용하는 방법

`sorted()` 함수를 딕셔너리에 바로 적용하면 이렇게 키 값만 리스트 형식으로 돌려준다.

```python
people = {3: "Jim", 2: "Jack", 4: "Jane", 1: "Jill"}

sorted(people)
# output: [1, 2, 3, 4]
```

딕셔너리의 키를 가져와 정렬하고 키 리스트만 반환하는 거다. 아마도 우리가 예상한 결과가 아니다.

#### 1) `.items()` 를 사용한다.

결국 딕셔너리의 모든 정보를 보존하려면 iterable 인수로 `딕셔너리.items()`를 사용해야 한다. 이렇게.

```python
sorted(people.items())
# output: [(1, 'Jill'), (2, 'Jack'), (3, 'Jim'), (4, 'Jane')]
```

그런데 이렇게 처리하면 딕셔너리의 키를 기준으로 정렬된, 키-값 튜플 리스트를 돌려준다.

#### 2) `sorted()` 함수의 key 파라미터로 정렬 기준을 지정한다.

딕셔너리의 값을 기준으로 정렬하고 싶다면 key 파라미터로 함수를 만들어서 작성해야 한다.

```python
def value_getter(item):
	return item[1]

sorted(people.items(), key=value_getter)
# output: [(2, 'Jack'), (4, 'Jane'), (1, 'Jill'), (3, 'Jim')]
```

콜백함수(다른 함수에 인수로 전달하는 함수)를 사용하는 방식이다. 위 예시의 경우 sorted()의 정렬 기준로 미리 만들어둔, 즉 튜플의 두 번째 값을 호출하는 함수를 전달했다.

코드를 더 예쁘게 작성하고 싶다면 lambda 함수를 사용하면 된다.

```python
sorted(people.items(), key=lambda item: item[1])
# output: [(2, 'Jack'), (4, 'Jane'), (1, 'Jill'), (3, 'Jim')]
```