---
title: 파이썬으로 여러 개의 엑셀 파일에서 필요한 값들만 한 번에 가져오기
date: 2019-06-25
categories: [WORK, Python]
tags: [Python, Excel, 업무자동화]
---

회사에서 일을 하다 보면 엑셀 파일로 작성된 템플릿(양식)을 여러 사람에게 배포하고, 양식에 따라 작성한 뒤 제출하도록 안내하는 경우가 있다. 그 결과를 수집해서 보려면 파일을 하나씩 열어서 살펴봐야 하는 단순 반복 업무가 발생한다. 게다가 그렇게 작성된 값들을 한 곳에 모으려면 수고가 이만저만이 아니다.

물론 좋은 성능의 PC와 빠른 복붙의 손놀림을 가졌다면 얼마 안 되는 양은 금방 해결할 수 있다. 그러나 만약 그 양이 방대하다면 어떻게 해결하는 게 좋을까?

그래서 파이썬으로 동일한 양식의 엑셀파일에서 필요한 값만 긁어 모아 새로운 엑셀파일로 저장하는 코드를 짜보았다. 나는 이 과정을 ‘엑셀 크롤링‘이라고 부르기로 하겠다. 이런 작업을 일컫는 구체적인 표현이 있는지는 모르겠지만, 어쨌든 여러 엑셀 파일에서 값을 긁어온다는 의미로 그렇게 부르기로 한다.

이제 단계별로 하나씩 따라해보자.

## 1. 엑셀 파일을 한 곳에 모으고 공통된 양식을 확인한다.

본 예제에 사용하기 위해 프로젝트 폴더 안에 `files`라는 하위폴더를 생성하여 그 안에 `a.xlsx`, `b.xlsx`, `c.xlsx`,라는 이름으로 각각 엑셀파일을 만들었다.

그리고 각 엑셀 파일에는 다음과 같이 파일마다 다른 값이 입력되어 있다. (B1에는 이름, B2에는 성별, D1에는 직업, D2에는 나이)

> `a.xlsx`
> 
> | |`A`|`B`|`C`|`D`|
> |---|---|---|---|---|
> |`1`|이름|배트맨|직업|백수|
> |`2`|성별|남성|나이|23|

> `b.xlsx`
> 
> | |`A`|`B`|`C`|`D`|
> |---|---|---|---|---|
> |`1`|이름|엑스맨|직업|싸움꾼|
> |`2`|성별|남성|나이|38|

> `c.xlsx`
> 
> | |`A`|`B`|`C`|`D`|
> |---|---|---|---|---|
> |`1`|이름|슈퍼맨|직업|영웅|
> |`2`|성별|남성|나이|32|

이 작업의 목표는 다음과 같은 값들을 한 곳에 모아 다른 엑셀 파일에 저장하는 것이다.

> `output.xlsx`
> 
> | |`A`|`B`|`C`|`D`|`E`|
> |---|---|---|---|---|---|
> |`1`|a.xlsx|배트맨|남성|백수|23|
> |`2`|b.xlsx|엑스맨|남성|싸움꾼|38|
> |`3`|c.xlsx|슈퍼맨|남성|영웅|32|

## 2. files 폴더에 저장된 모든 파일명을 리스트로 저장한다.

일단 파이썬 os 모듈을 활용해서 하위폴더 files에 저장된 모든 파일들의 이름을 리스트로 저장하고 출력하는 코드를 작성한다.

```python
import os

path = "./files"
file_list = os.listdir(path)
print(file_list)

# ['a.xlsx', 'b.xlsx', 'c.xlsx']
```

## 3. openpyxl을 활용해 엑셀 파일의 값들을 리스트의 리스트로 저장한다.

[openpyxl](https://openpyxl.readthedocs.io/en/stable/)은 엑셀 파일을 손쉽게 다룰 수 있는 파이썬 라이브러리다. 가장 널리 쓰이는 라이브러리이며, 다른 엑셀 관련 라이브러리보다 가볍고 편리해서 좋다. 자세한 내용은 홈페이지를 참조하자. 설치는 아래와 같은 pip 명령어로 쉽게 진행할 수 있다.

```
pip install openpyxl
```

이제 openpyxl의 load_workbook이라는 모듈을 이용할 것이다. for 반복문을 활용해 아까 저장한 파일 이름을 찾아 하나씩 열고, 아래와 같은 구체적인 단계를 통해 값들을 리스트의 리스트로 저장한다.

1. `results`라는 이름의 빈 리스트를 생성한다. (이곳에 최종적으로 각 파일에서 수집한 값들을 리스트의 형태로 저장할 것이다.)
2. `result`라는 이름의 빈 리스트를 생성한다. (하나의 파일에서 수집된 값을 담는 임시 저장소로 활용한다.)
3. 각 파일을 열고 그 파일에서 원하는 값을 찾아 `result`에 순서대로 추가한다.
4. `result`를 `results`에 추가한다.
5. `for` 반복문을 활용해 다음 파일을 열고 위 단계를 반복한다.

구체적인 코드는 아래와 같다.

```python
from openpyxl import load_workbook

results = []

for file_name_raw in file_list:

    file_name = "./files/" + file_name_raw
    wb = load_workbook(filename=file_name, data_only=True)
    ws = wb.active

    result = []
    result.append(file_name_raw)
    result.append(ws['B1'].value)
    result.append(ws['B2'].value)
    result.append(ws['D1'].value)
    result.append(ws['D2'].value)

    results.append(result)

print(results)
```

`wb.active`는 현재 활성화된 워크시트를 말한다. 여기서 `ws['B1'].value`와 같은 방식으로 셀B1의 값을 찾아오는 것이다.

출력하면 아래와 같은 결과 값을 얻을 수 있다. (리스트 첫번째 값으로는 파일명을 추가했다.)

```
[['a.xlsx', '배트맨', '남성', '백수', 23], ['b.xlsx', '엑스맨', '남성', '싸움꾼', 38], ['c.xlsx', '슈퍼맨', '남성', '영웅', 32]]
```

## 4. 수집한 값들을 하나의 엑셀파일에 쓰고 저장한다.

이번엔 `Workbook`이라는 모듈을 활용한다. `ws.append()`를 활용하면 하나의 행에 하나의 리스트를 추가하는 방식으로 편리하게 사용할 수 있다.

```python
from openpyxl import Workbook

wb = Workbook()
ws = wb.active

for i in results:
    ws.append(i)

wb.save("output.xlsx")
```

위에 나열된 코드 묶음 세 개를 이어서 실행하면 다음과 같이 output.xlsx라는 엑셀 파일이 생성되고 그 안에 값이 정리되어 있음을 볼 수 있다. 짠!

> `output.xlsx`
> 
> | |`A`|`B`|`C`|`D`|`E`|
> |---|---|---|---|---|---|
> |`1`|a.xlsx|배트맨|남성|백수|23|
> |`2`|b.xlsx|엑스맨|남성|싸움꾼|38|
> |`3`|c.xlsx|슈퍼맨|남성|영웅|32|

## 파이썬 코드 예시 (전체)

```python
import os

path = "./files"
file_list = os.listdir(path)
print(file_list)


from openpyxl import load_workbook

results = []

for file_name_raw in file_list:

    file_name = "./files/" + file_name_raw
    wb = load_workbook(filename=file_name, data_only=True)
    ws = wb.active

    result = []
    result.append(file_name_raw)
    result.append(ws['B1'].value)
    result.append(ws['B2'].value)
    result.append(ws['D1'].value)
    result.append(ws['D2'].value)

    results.append(result)

print(results)


from openpyxl import Workbook
wb = Workbook()
ws = wb.active

for i in results:
    ws.append(i)

wb.save("output.xlsx")
```

아무튼 파이썬을 잘 쓰면 일이 편해진다.