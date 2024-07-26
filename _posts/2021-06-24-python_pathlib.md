---
title: 파이썬으로 파일/폴더 경로 다루기 (os.path 대신 pathlib 사용하기)
date: 2021-06-24
categories: [WORK, Python]
tags: [Python]
---

내 PC에서 간단한 자동화를 위해 파이썬으로 파일이나 폴더의 경로를 가져오거나 작성해야 하는 상황, 그리고 폴더 내에서 파일들을 반복해서 다룰 일이 많았기 때문에 매번 파일 경로 다루다가 이런저런 삽질을 하곤 했다.

어쨌든 그동안 파이썬에 내장된 `os.path`를 사용했는데, 이것보다 `pathlib`를 사용하면 훨씬 더 직관적이고 편리하다는 걸 깨달았다. (참고: [os와 pathlib 비교 – 파이썬 공식 문서](https://docs.python.org/ko/3/library/pathlib.html#correspondence-to-tools-in-the-os-module))

이 기회에 파이썬에서 `pathlib` 와 `shutil`을 사용해 파일/폴더 다루는 문법을 남겨본다.


## 절대경로, 상대경로 이해하기

절대경로는 흔히 드라이브 내에서의 파일 위치를 의미한다. 예를 들어 C드라이브에 존재하는 파일 절대경로는 “C:/”로 시작한다. 절대경로를 작성할 때 Mac, Linux 운영체제에서는 폴더 구분자로 `/`를 사용하지만, 윈도우 운영체제의 경우 `\`(백슬래시)를 사용한다.

윈도우 기준으로 파이썬 코딩을 할 때 문자열로 절대경로를 작성하려면 구분자를 쓸 때 `\\`와 같이 백슬래시 2개를 연달아 사용해야 한다. (백슬래시를 1개만 사용하면 이스케이프 문자를 표현하는 문법으로 사용되기 때문이다. 예를 들어 `\n`이라는 표현은 줄바꿈을 의미한다.)

상대경로는 현재 작업폴더를 기준으로 삼는다. 상대경로를 작성할 때는 `/`(슬래시) 문자로 구분하여 하위 폴더를 탐색할 수 있다.

## 파일의 경로와 이름 다루기

파이썬에 내장된 [pathlib](https://python.flowdas.com/library/pathlib.html)모듈들을 활용한다.

대표적인 함수를 사용해 파일의 절대/상대경로, 파일명, 확장자를 출력해보자.

- 현재폴더의 절대경로 가져오기 : `Path.cwd()`
- 특정파일의 절대경로 가져오기 : `Path(파일경로).resolve()`
- 파일명 가져오기 : `Path(파일경로).name()`
    - (확장자 없이) 파일명만 가져오기 : `Path(파일경로).stem`
    - (파일명 없이) 확장자만 가져오기 : `Path(파일경로).suffix`

이런 식으로 사용하면 된다.

```python
from pathlib import Path

print(Path.cwd())
print(Path('main.py').resolve())
print(Path('C:\\Users\\USERNAME\\Desktop\\작업폴더\\main.py').name)
print(Path('main.py').stem)
print(Path('main.py').suffix)
```

`Path.joinpath()`라는 함수도 널리 쓰인다. 현재 작업폴더를 의미하는 `Path.cwd()`와 함께 하위 폴더명이나 파일 이름을 순서대로 적어주면, 해당 문자열을 합쳐서 절대경로의 형태로 돌려준다.

```python
from pathlib import Path

print(Path.joinpath(Path.cwd(), "files", "sample.txt"))
```

## 파일 목록 얻기

파이썬으로 어떤 폴더 안에 있는 파일 목록을 얻는 가장 쉬운 방법은 `Path(폴더경로).iterdir()` 를 사용하는 것이다. 이렇게 하면 반복/순회가 가능한(iterable) 객체를 얻을 수 있기 때문에 곧바로 for문을 사용할 수 있다.

```python
from pathlib import Path

for 파일 in Path("files").iterdir():
    print(파일)
```

여기에 `Path(폴더경로).glob(조건)`를 사용하면 파일명에서 특정 조건을 만족하는 파일만 필터링 할 수 있다.

와일드카드 `*`를 활용하면 글자수에 상관 없이 조건을 걸어줄 수 있다. 예를 들면 .xlsx 확장자를 가진 파일만 고르고 싶다면 이렇게.

```python
from pathlib import Path

for 파일 in Path("files").glob("*.xlsx"):
    print(파일)
```

와일드카드 `?`를 활용하면 문자 개수를 세서 필터링 할 수 있다. 예를 들어 파일명이 세 글자인 .txt 파일만 찾고 싶다면 이렇게

```python
from pathlib import Path

for 파일 in Path("files").glob("???.txt"):
    print(파일)
```

## 새 폴더 만들기

`Path(폴더경로).mkdir(exist_ok=True)` 함수를 사용한다. `exist_ok=True`는 해당 폴더가 없을 경우에만 생성한다는 뜻이다. (만약 같은 이름을 가진 폴더가 이미 존재하는 상태에서, 이를 명시하지 않고 코드를 실행하면 “파일이 이미 있으므로 만들 수 없습니다”라는 에러가 발생한다.)

```python
from pathlib import Path

Path("새 폴더").mkdir(exist_ok=True)
```

## 파일/폴더 이름 바꾸기 & 이동하기

`Path(원래파일경로).rename(새파일경로)` 함수를 사용한다. 파일이나 폴더의 절대경로, 상대경로 모두 가능하다.

```python
from pathlib import Path

Path("files/test.txt").rename("files/새 이름.txt")
Path("files/test.txt").rename("files(2)/test.txt")
```

그리고 이름을 바꾸는 게 곧 경로를 바꾸는 원리이기 때문에 파일을 이동을 시키는 것도 가능하다.

## 파일/폴더 삭제하기

파일의 삭제는 휴지통(recycle bin)으로 옮겨가는 것이 아니라 완전한 삭제를 의미하기 때문에 신중하게 사용하는 것이 좋다. 만약 휴지통으로 옮기는 기능을 수행하고 싶다면 [Send2Trash](https://pypi.org/project/Send2Trash/)와 같은 라이브러리를 별도로 설치해서 사용해야 한다.

- 파일 삭제 : `Path(파일경로).unlink()`
- 폴더 삭제 : `shutil.rmtree(폴더경로)`

```python
from pathlib import Path

Path("files/12345.txt").unlink()
```

폴더의 삭제는 `Path(폴더경로).unlink()` 함수로는 수행이 불가능하다. 대신 [shutil](https://python.flowdas.com/library/shutil.html) 모듈을 이용한다.

```python
import shutil

shutil.rmtree("새 폴더")
```

이 또한 휴지통으로 이동 시키는 것이 아니라 완전한 삭제를 의미한다.

## 파일/폴더 복사하기

마찬가지로 [shutil](https://python.flowdas.com/library/shutil.html) 모듈을 이용한다.

- 파일 복사 : `shutil.copyfile(파일경로)`
- 폴더 복사 : `shutil.copytree(폴더경로)`

```python
import shutil

shutil.copyfile("files/test.txt", "files/test - 복사본.txt")
shutil.copytree("files", "files - 복사본")
```