---
title: 파이썬 pprint를 사용해서 데이터 출력하기
date: 2022-01-07
categories: [WORK, 🐍 Python]
tags: [Python]
---

프로그래밍 하면서 제일 먼저 배우는 게 바로 출력문이다. 내가 프로그래밍한 결과가 정상적으로 작동하는지 확인해야 하니까. 그래서 무슨 언어를 배우든 일단 Hello World부터 찍고 시작하게 되는 거겠지.

아무튼 파이썬에서도 print문을 사용하면 기본적인 출력을 할 수 있지만 많은 양의 데이터를 다룰 때, 이를 테면 중첩된 리스트나 딕셔너리, API요청 결과, JSON을 print문으로 출력하게 되면 그 결과를 직접 눈으로 읽고 확인하기가 쉽지 않다.

이때 유용한 게 파이썬 기본 모듈 [pprint](https://docs.python.org/3/library/pprint.html)다. pprint는 Pretty Print의 약자고, 당연히 예쁘게 출력하는 게 그 사용 목적이다.

그동안 pprint 기능을 한 번 정리해서 블로그에 올려놓고 싶다는 생각을 하고 있었는데 마침 [Prettify Your Data Structures With Pretty Print in Python](https://realpython.com/python-pretty-print/)라는 글을 발견해서 이 기회에 간략히 요약해보았다.

## pprint 기본 사용법

일단 데이터 다룰 때 가장 많이 사용하는 json파일을 예시로 가져와보자.

```python
from urllib import request
import json

response = request.urlopen("https://jsonplaceholder.typicode.com/users")
json_response = response.read()
users = json.loads(json_response)
```

이제 pprint모듈에서 pprint함수를 불러와서 출력해보자. (줄여서 쓰고 싶으면 pprint를 pp라고 불러오기도 한다.)

```python
from pprint import pprint
pprint(users)
```

출력을 해보면 아름답게 들여쓰기가 되는 걸 확인할 수 있다. 속이 다 시원하다.

```
[{'address': {'city': 'Gwenborough',
              'geo': {'lat': '-37.3159', 'lng': '81.1496'},
              'street': 'Kulas Light',
              'suite': 'Apt. 556',
              'zipcode': '92998-3874'},
  'company': {'bs': 'harness real-time e-markets',
              'catchPhrase': 'Multi-layered client-server neural-net',
              'name': 'Romaguera-Crona'},
  'email': 'Sincere@april.biz',
  'id': 1,
  'name': 'Leanne Graham',
  'phone': '1-770-736-8031 x56442',
  'username': 'Bret',
  'website': 'hildegard.org'},
 ...

 {'address': {'city': 'Aliyaview',
              'geo': {'lat': '-14.3990', 'lng': '-120.7677'},
              'street': 'Ellsworth Summit',
              'suite': 'Suite 729',
              'zipcode': '31428-2261'},
  'company': {'bs': 'target end-to-end models',
              'catchPhrase': 'Centralized empowering task-force',
              'name': 'Hoeger LLC'},
  'email': 'Rey.Padberg@karina.biz',
  'id': 10,
  'name': 'Clementina DuBuque',
  'phone': '024-648-3804',
  'username': 'Moriah.Stanton',
  'website': 'ambrose.net'}]
```

## pprint 출력 옵션 지정하기

pprint는 기능이 참 많지만 이 중에서 많이 쓰이는 옵션들만 골라보았다.

1. depth (중첩된 데이터의 출력 수준)
2. indent (들여쓰기)
3. width (줄 길이 제한)
4. sort_dicts (딕셔너리 키 정렬)
5. stream (파일에 스트림 출력)

예시를 통해 하나씩 알아보자.

### 1. depth

데이터가 중첩된 경우 depth를 통해 몇 번째 수준까지 출력할 것인지 지정할 수 있다. 더 하위 수준은 생략된다. 이렇게 depth를 통해 출력하면 데이터 구조가 공유하는 상위 레벨의 키값을 편하게 확인할 수 있다.

```python
pprint(users, depth=1)
```

```
[{...}, {...}, {...}, {...}, {...}, {...}, {...}, {...}, {...}, {...}]
```

```python
pprint(users, depth=2)
```

```
[{'address': {...},
  'company': {...},
  'email': 'Sincere@april.biz',
  'id': 1,
  'name': 'Leanne Graham',
  'phone': '1-770-736-8031 x56442',
  'username': 'Bret',
  'website': 'hildegard.org'},

  ...


 {'address': {...},
  'company': {...},
  'email': 'Rey.Padberg@karina.biz',
  'id': 10,
  'name': 'Clementina DuBuque',
  'phone': '024-648-3804',
  'username': 'Moriah.Stanton',
  'website': 'ambrose.net'}]
```

### 2. indent

들여쓰기 간격도 조정할 수 있다. 기본값은 1이지만 이걸 4로 늘려서 출력하면 아래와 같이 나온다.

```python
pprint(users[0], depth=2, indent=4)
```

```
{   'address': {   'city': 'Gwenborough',
                   'geo': {...},
                   'street': 'Kulas Light',
                   'suite': 'Apt. 556',
                   'zipcode': '92998-3874'},
    'company': {   'bs': 'harness real-time e-markets',
                   'catchPhrase': 'Multi-layered client-server neural-net',
                   'name': 'Romaguera-Crona'},
    'email': 'Sincere@april.biz',
    'id': 1,
    'name': 'Leanne Graham',
    'phone': '1-770-736-8031 x56442',
    'username': 'Bret',
    'website': 'hildegard.org'}
```

### 3. width

한 줄에 출력할 최대 길이를 제한하는 옵션. 기본값은 80자다.

길이를 늘여가며 비교해보자.

```python
pprint(users[0])
```

```
{'address': {'city': 'Gwenborough',
             'geo': {'lat': '-37.3159', 'lng': '81.1496'},
             'street': 'Kulas Light',
             'suite': 'Apt. 556',
             'zipcode': '92998-3874'},
 'company': {'bs': 'harness real-time e-markets',
             'catchPhrase': 'Multi-layered client-server neural-net',
             'name': 'Romaguera-Crona'},
 'email': 'Sincere@april.biz',
 'id': 1,
 'name': 'Leanne Graham',
 'phone': '1-770-736-8031 x56442',
 'username': 'Bret',
 'website': 'hildegard.org'}
```

```python
pprint(users[0], width=160)
```

```
{'address': {'city': 'Gwenborough', 'geo': {'lat': '-37.3159', 'lng': '81.1496'}, 'street': 'Kulas Light', 'suite': 'Apt. 556', 'zipcode': '92998-3874'},
 'company': {'bs': 'harness real-time e-markets', 'catchPhrase': 'Multi-layered client-server neural-net', 'name': 'Romaguera-Crona'},
 'email': 'Sincere@april.biz',
 'id': 1,
 'name': 'Leanne Graham',
 'phone': '1-770-736-8031 x56442',
 'username': 'Bret',
 'website': 'hildegard.org'}
```

```python
pprint(users[0], width=500)
```

```
{'address': {'city': 'Gwenborough', 'geo': {'lat': '-37.3159', 'lng': '81.1496'}, 'street': 'Kulas Light', 'suite': 'Apt. 556', 'zipcode': '92998-3874'}, 'company': {'bs': 'harness real-time e-markets', 'catchPhrase': 'Multi-layered client-server neural-net', 'name': 'Romaguera-Crona'}, 'email': 'Sincere@april.biz', 'id': 1, 'name': 'Leanne Graham', 'phone': '1-770-736-8031 x56442', 'username': 'Bret', 'website': 'hildegard.org'}
```

### 4. sort_dicts

pprint는 딕셔너리를 키값에 따라 알파벳 순으로 알아서 정렬해서 출력해주는 게 기본값이다. 따라서 이를 방지하려면 `sort_dicts=False`를 따로 적어주어야 한다.

```python
pprint(users[0], sort_dicts=False)
```

### 5. stream

텍스트 파일에 실시간 로그를 남길 때 스트림 출력을 사용할 수 있다.

```python
with open("output.txt", mode="w") as file_object:
    pprint(users, stream=file_object)
```

앞으로 json형식의 데이터를 출력할 때는 웬만하면 pprint를 사용하자.