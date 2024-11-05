---
title: 파이썬으로 JSON 데이터 다루기
date: 2024-11-05
categories: [WORK, Data]
tags: [데이터분석, Python]
---

JSON은 정보 교환의 주요 표준으로 빠르게 부상했다. 
API로 데이터를 전송하거나 문서 데이터베이스에 정보를 저장할 때 주로 JSON을 접하게 될 거다. 

요즘엔 JSON이 데이터 배포에 가장 널리 사용되는 형식이지만, 
XML과 YAML도 비슷한 목적으로 사용할 수는 있다.


아무튼 본 포스팅에서는파이썬으로 JSON 데이터 다루는 방법 소개. 
([Real Python](https://realpython.com/)에 기고된 [Working With JSON Data in Python
](https://realpython.com/python-json/)의 요약 번역)


목차는 다음과 같다.

- JSON 이란
    - JSON 문법 알아보기
    - JSON 문법에서 주의할 점
- 파이썬으로 JSON 작성하기
    - 딕셔너리를 JSON으로 변환하기
    - 다른 데이터 형식을 JSON으로 직렬화하기(Serialize)
    - 외부에 JSON 파일 쓰기
- 파이썬으로 JSON 불러오기
    - JSON을 딕셔너리로 변환하기
    - JSON 데이터 형식을 역직렬화하기(Deserialize) 
    - 외부 JSON 파일 읽기
-  터미널에서 JSON 검증하고 출력하기



## JSON이란

JSON은 JavaScript Object Notation의 약자다.
이름에서 알 수 있듯 JavaScript에서 유래되긴 했으나, 
현재는 언어에 구애받지 않고 데이터 교환의 표준으로 인식되고 있다.

JSON은 자바스크립트가 지원되기 웹 브라우저 상에서 손쉽게 파싱이 가능하다. 
게다가 기본적인 문법도 단순하기에 컴퓨터뿐만 아니라 사람도 JSON 데이터를 손쉽게 읽고 쓸 수 있다.

JSON은 텍스트 기반이기 때문에 누구나 JSON 파일을 만들 수 있다. 
파일 확장자를 `.json`으로 지정하기만 하면 되고, 
웬만한 텍스트 에디터들은 JSON 데이터를 문법에 따라 읽기 쉽게 띄워줄 거다.


### JSON 문법 알아보기

JSON은 파이썬 딕셔너리와 유사한 구조를 지니고 있다.

> [key-value 쌍과 배열]은 보편적인 데이터 구조다.  
> 사실상 모든 현대 프로그래밍 언어는 어떤 형태로든 이를 지원한다.  
> 프로그래밍 언어와 호환되는 데이터 형식도 이러한 구조를 기반으로 한다는 것은 당연한 일이다.  
> ([출처](https://www.json.org/json-en.html))  


예시를 보자.

```json
{
    "name": "Frieda",
  "isDog": true,
  "hobbies": ["eating", "sleeping", "barking"],
  "age": 8,
  "address": {
      "work": null,
    "home": ["Berlin", "Germany"]
  },
  "friends": [
      {
          "name": "Philipp",
      "hobbies": ["eating", "sleeping", "reading"]
    },
    {
        "name": "Mitch",
      "hobbies": ["running", "snacking"]
    }
  ]
}
```

JSON에서 공백이 중요하지 않지만 
들여쓰기를 할 때는 일반적으로 공백 2개 또는 4개를 사용한다. 
JSON 파일 크기를 줄이려면 일부러 공백을 제거하여 용량을 최소화하는 방법도 고려할 수도 있다.

JSON 객체 내부에는 콜론(:)을 사용하여, 
콜론의 왼쪽에는 key, 오른쪽에 value를 정의한다. 

key는 큰따옴표(")로 묶인 문자열이다. 
파이썬과 달리 JSON 문자열은 작은따옴표(')를 지원하지 않는다. 

value로는 다음과 같은 형식이 가능하다. 

- object: 중괄호 `{}`로 묶인 key-value 쌍의 컬렉션
- array: 대괄호 `[]` 로 묶인 리스트 
- string: 큰따옴표 `""`로 묶인 텍스트
- number: 정수 또는 부동 소수점 숫자
- boolean: `true` 또는 `false`
- null: null 값을 나타낼 때 `null`이라고 표현

파이썬의 딕셔너리나 리스트와 마찬가지로 JSON 객체와 배열에도 데이터를 중첩할 수 있다. 
예를 들어, 객체를 객체의 값으로(JSON 내의 JSON) 포함할 수 있다. 

참고로 파이썬 사용자들은 boolean 값에 주의를 기울여야 한다. JSON은 boolean 소문자 JavaScript 스타일의 `true`, `false`를 사용한다.
(파이썬에서는 첫 글자를 대문자로 하여 `True`, `False`로 표현한다.)


### JSON 문법에서 주의할 점

JSON에서는 '주석', '문자열에 작은 따옴표', 'trailing commas(맨 마지막에 콤마)'를 허용하지 않는다. 
(파이썬 딕셔너리나 자바스크립트 객체에 익숙한 사람들에게는 혼란스러울 수 있다.)

아래에 잘못된 JSON 예시를 보자.

```json
{
  "name": 'Frieda',
  "address": {
    "work": null, // Doesn't pay rent either
    "home": "Berlin",
  },
  "friends": [
    {
      "name": "Philipp",
      "hobbies": ["eating", "sleeping", "reading",]
    }
  ]
}
```

- 2번째 줄: 문자열을 작은따옴표로 묶었다.
- 4번째 줄: 인라인 주석을 사용했다.
- 5번째 줄: 마지막 키-값 쌍 뒤에 쉼표가 있다.
- 10번째 줄: 배열 끝에 쉼표가 있다.

이런 제약이 있다 보니 대안으로 
[Human JSON(Hjson)](https://hjson.github.io/) 또는 
[주석이 있는 JSON(JSONC)](https://github.com/Microsoft/node-jsonc-parser)를 사용하기도 한다.

보통 코드 에디터들이 JSON 문법 오류를 점검해주기도 하고,
온라인에도 무료 JSON 검증 도구들이 여럿 있긴 하다.



## 파이썬으로 JSON 작성하기

파이썬 기본 모듈 `json`을 사용하면 
JSON으로 포매팅된 문자열을 읽고 쓸 수 있다. 
파이썬 데이터를 JSON 데이터로 변환할 수 있으며, 그 반대도 가능하다.

데이터를 JSON 형식으로 변환하는 행위를 직렬화(Serialize),
반대로 JSON 형식의 데이터를 파이썬에서 사용 가능한 형태로 디코딩하는 것을 역직렬화(Deserialize)의미한다.


### 딕셔너리를 JSON으로 변환하기

`json` 모듈의 `.dumps()`를 사용하면 
파이썬 딕셔너리를 JSON 형식의 문자열로 변환할 수 있다.

```python
>>> import json
>>> food_ratings = {"organic dog food": 2, "human food": 10}
>>> json.dumps(food_ratings)
'{"organic dog food": 2, "human food": 10}'
```

JSON 문법에서는 키가 반드시 문자열이어야 하기 때문에
만약 딕셔너리 키에 정수가 있을 경우 다음과 같이 문자열로 변환된다는 사실을 기억하자.

```python
>>> numbers_present = {1: True, 2: True, 3: False}
>>> json.dumps(numbers_present)
'{"1": true, "2": true, "3": false}'
```

이렇게 JSON 형식으로 알아서 잘 바꿔준다.

마찬가지로 파이썬 딕셔너리에 `True`, `False` 와 같은 boolean이 있을 경우,
JSON 형식으로 변환하면 `true`, `false`와 같이 JSON 문법에 따라 소문자로 바뀐다.


### 다른 데이터 형식을 JSON으로 직렬화하기(Serialize)

`json` 모듈을 사용하면 
다른 파이썬 데이터들도 JSON으로 변환할 수 있다. 

|Python|JSON|
|-|-|
|dict|object|
|list|array|
|tuple|array|
|str|string|
|int|number|
|float|number|
|True|true|
|False|false|
|None|null|

boolean 이나 리스트를 변환하면 이런 식이 되겠다.

```python
>>> json.dumps(True)
'true'

>>> json.dumps(["eating", "sleeping", "barking"])
'["eating", "sleeping", "barking"]'
```

그리고 `json.dumps()`로 JSON 포매팅을 할 때는
매개변수 `sort_keys`를 통해 key 값을 기준으로 정렬하는 것도 가능하다.

```python
>>> toy_conditions = {"chew bone": 7, "ball": 3, "sock": -1}
>>> json.dumps(toy_conditions, sort_keys=True)
'{"ball": 3, "chew bone": 7, "sock": -1}'
```

들여쓰기를 예쁘게 해서 작성하려면 
`indent`를 넣어줄 수도 있다.

```python
>>> import json
>>> dog_friend = {
...     "name": "Mitch",
...     "age": 6.5,
... }

>>> print(json.dumps(dog_friend, indent=2))
{
  "name": "Mitch",
  "age": 6.5
}

>>> print(json.dumps(dog_friend, indent=4))
{
    "name": "Mitch",
    "age": 6.5
}
```

### 외부에 JSON 파일 쓰기

JSON 형식의 데이터를 파이썬 외부에서 따로 사용할 일이 있을 경우, 
`json.dump()`를 사용하여 작성하면 된다.
(앞서 살펴본 `.dumps()`와 달리 's'가 빠져있음에 주의하자.)

```python
import json

dog_data = {
  "name": "Frieda",
  "is_dog": True,
  "hobbies": ["eating", "sleeping", "barking",],
  "age": 8,
  "address": {
    "work": None,
    "home": ("Berlin", "Germany",),
  },
  "friends": [
    {
      "name": "Philipp",
      "hobbies": ["eating", "sleeping", "reading",],
    },
    {
      "name": "Mitch",
      "hobbies": ["running", "snacking",],
    },
  ],
}

with open("hello_frieda.json", mode="w", encoding="utf-8") as write_file:
    json.dump(dog_data, write_file)
```

`json.dump()`를 사용할 때는 '쓰고자 하는 객체', 이어서 '작성할 파일'을 인수로 넣어주어야 한다.
여기에 `json.dumps()`와 마찬가지로 `sort_keys`, `indent`와 같은 추가적인 매개변수를 지정할 수 있다.



## 파이썬으로 JSON 불러오기

이제 JSON 데이터를 파이썬으로 다시 로드하는 방법을 알아보자.

`json.dumps()`, `.json.dump()`와 정확히 대응되는 함수 
`json.loads()`, `json.load()`를 사용하면 된다. 

다만, 역직렬화는 직렬화 프로세스의 정반대라고 볼 수는 없다. 
왜냐하면 JSON의 key는 항상 문자열이고, 
모든 파이썬 데이터가 JSON 데이터로 변환될 수 있는 것은 아니기 때문이다. 
(파이썬 객체가 직렬화되고 다시 역직렬화를 거칠 때 원래 형식을 유지하지 못할 수 있음) 


### JSON을 딕셔너리로 변환하기

우선 JSON을 생성하고,
다시 파이썬 딕셔너리로 변환해서 비교해보면,


```python
>>> import json
>>> dog_registry = {1: {"name": "Frieda"}}
>>> dog_json = json.dumps(dog_registry)
>>> dog_json
'{"1": {"name": "Frieda"}}'

>>> new_dog_registry = json.loads(dog_json)
>>> new_dog_registry == dog_registry
False

>>> new_dog_registry
{'1': {'name': 'Frieda'}}

>>> dog_registry
{1: {'name': 'Frieda'}}
```

key가 문자열로 바뀌었음에 주의하자.
(JSON의 key는 언제나 문자열이어야 한다.)


### JSON 데이터 형식을 역직렬화하기(Deserialize) 

파이썬 딕셔너리를 JSON으로 dumps한 후 
다시 loads하면 어떤 결과가 나오는지 살펴보자.

```python
>>> dog_data = {
...   "name": "Frieda",
...   "is_dog": True,
...   "hobbies": ["eating", "sleeping", "barking",],
...   "age": 8,
...   "address": {
...     "work": None,
...     "home": ("Berlin", "Germany",),
...   },
... }

>>> dog_data_json = json.dumps(dog_data)
>>> dog_data_json
'{"name": "Frieda", "is_dog": true, "hobbies": ["eating", "sleeping", "barking"],
"age": 8, "address": {"work": null, "home": ["Berlin", "Germany"]}}'

>>> new_dog_data = json.loads(dog_data_json)
>>> new_dog_data
{'name': 'Frieda', 'is_dog': True, 'hobbies': ['eating', 'sleeping', 'barking'],
'age': 8, 'address': {'work': None, 'home': ['Berlin', 'Germany']}}
```

거의 모든 데이터가 그대로 역직렬화(Deserialize)하여 복원되는데,
예외가 있는 게 바로 '튜플'이다. 
`dog_data["address"]["home"]`는 원래 튜플이었는데, 
역직렬화 과정에서 리스트로 형식이 바뀌었다.


### 외부 JSON 파일 읽기

`with`문 내부에서 `json.load()`를 사용하면
외부 JSON 파일을 읽어올 수 있다.
(앞서 살펴본 `.loads()`와 달리 's'가 빠져있음에 주의하자.)

```python
>>> import json
>>> with open("hello_frieda.json", mode="r", encoding="utf-8") as read_file:
...     frie_data = json.load(read_file)
...
>>> type(frie_data)
<class 'dict'>
```

파이썬 딕셔너리 형식으로 역직렬화 된다.


## 터미널에서 JSON 검증하고 출력하기

파이썬의 `json.tool`을 사용하면 JSON 파일이 유효한지 바로 검증이 가능하다.

```shell
$ python -m json.tool dog_friend.json
{
    "name": "Mitch",
    "age": 6.5
}
```

위와 같이 명령어를 넣었을 때, 
JSON 파일이 유효하다면 그 결과를 바로 터미널에 출력한다.

출력할 때 들여쓰기 수준을 조절하고 싶다면 
명령어 맨 뒤에 `--indent 2`와 같은 식으로 붙여주면 된다.
(참고로 기본 들여쓰기 값은 4다.)

그리고 만약 JSON 파일이 유효하지 않다면
다음과 같이 오류 메시지가 출력된다.

```shell
$ python -m json.tool dog_friend.json
Expecting ',' delimiter: line 3 column 5 (char 26)
```
