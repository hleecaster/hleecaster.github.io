---
title: 파이썬으로 UUID (고유 식별자) 생성하는 방법
date: 2022-05-24
categories: [WORK, 🐍 Python]
tags: [Python, 업무자동화]
---

범용 고유 식별자 [UUID](https://ko.wikipedia.org/wiki/%EB%B2%94%EC%9A%A9_%EA%B3%A0%EC%9C%A0_%EC%8B%9D%EB%B3%84%EC%9E%90)는 Universally Unique Identifier의 약자로, 소프트웨어 개발에 쓰이는 식별자 표준이다.

UUID 표준을 따르면 임의의(혹은 임의로 보이는) 문자, 숫자의 조합으로 식별자를 생성할 수 있고, 중복될 가능성이 거의 없다고 인정되기 때문에 많이 사용되고 있다.

아무튼 파이썬에서는 기본적으로 [uuid](https://docs.python.org/ko/3/library/uuid.html)라는 내장 모듈을 제공하기 때문에 이걸 사용하면 식별자를 간단히 생성할 수 있다.

UUID 100개를 생성해서 텍스트 파일로 남겨보자. 예시.

```python
import uuid

with open("./uuid.txt", 'w') as file:
    for i in range(100):
        file.write(str(uuid.uuid4())+"\n")
```

위 코드를 살펴보면 `uuid.uuid4()`를 사용한 걸 알 수 있다.

UUID는 생성 방법에 따라서 일종의 버전(?)이 존재하는데, 파이썬으로 고유 ID를 얻고자 할 때는 `uuid1()` 또는 `uuid4()`를 사용하는 것이 좋다.

- `uuid1` : 컴퓨터의 네트워크 주소를 포함하여 UUID를 생성한다. (개인정보가 노출될 가능성 있음)
- `uuid4` : 무작위로 UUID를 생성한다.

혹시 나중에 참고할 일이 있을 것 같아서 남겨두는 포스팅.