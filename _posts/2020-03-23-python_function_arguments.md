---
title: 파이썬 함수 이해하기 (parameter, keyword arguments 다루기)
date: 2020-03-23
categories: [WORK, Python]
tags: [Python]
---

파이썬으로 코드를 짜다 보면 함수를 작성해서 사용할 일이 많아진다.

함수를 짜는 방법은 쉬우니 넘어가고, 그 외에 추가적으로 알고 있으면 좋은 내용들을 메모 차 남겨본다. 내용은 아래와 같다.

- return을 명시하지 않았을 때 기본값은 None이다.
- 함수는 인수(매개변수)를 선택적으로 받을 수 있고, 기본 값 설정이 가능하다.
- 함수의 매개변수(parameter)가 많다면 키워드 인수(keyword arguments)를 활용하면 된다.
- 리스트와 같은 자료형을 인수로 지정할 때는 기본값을 None으로 지정하는 것이 좋다.
- 함수의 return으로 여러 개의 값을 반환하면, 그 값들을 쉼표로 구분하여 변수에 저장할 수 있다.
- 함수가 입력 받을 매개변수의 개수가 정해져 있지 않을 때는 `*` 1개를 사용한다.
- `**`를 사용하여 "키워드 인수"를 받으면 딕셔너리 형태로 그 값을 사용할 수 있다.
- 다양한 종류의 매개변수를 입력 받기 위해서는 지정된 순서를 따른다.

주로 매개변수(parameters), 인수(arguments)에 관한 내용이다. 하나씩 살펴보자.

## `return`을 명시하지 않았을 때 기본값은 `None`이다.

함수를 후딱 만들어서 테스트 해보면 알 수 있다.

`return` 없이 그냥 문자열만 출력하는 함수를 작성해봤다. 함수의 결과 값을 변수에 담아 그 변수를 출력해보자.

```python
def no_return():
  print("노리턴")

test = no_return()

print(test)
```

`None`이라고 출력되는 걸 볼 수 있다. (함수에서 return 뒤를 비워 놓아도 마찬가지다.)

`if` 조건문과 `None`을 잘 활용하면 일종의 예외처리 같은 걸 쉽게 할 수 있다.

## 함수는 인수(매개변수)를 선택적으로 받을 수 있고, 기본 값 설정이 가능하다.

함수를 정의할 때 한 개 이상의 인수를 처리하는 작업을 한다면, def 구문 끝 괄호 안에 매개변수를 설정해야 한다.

>함수를 정의할 땐 매개변수(parameter, 파라미터), 실제로 사용할 땐 인수(argument)라고 칭하는 게 옳으나 대부분 용어를 명확히 구분하지 않고 쓰긴 한다.

매개변수는 ‘필수’ 또는 ‘선택’을 지정할 수 있다. ‘선택’인 경우에는 그 인수를 넣지 않았을 때 적용할 기본값을 설정하면 되기 때문이다.

예를 들면 이렇게

```python
def test(a, b, c=False):
    return
```

주의할 점은 def 구문에서 ‘필수’ 매개변수를 먼저, ‘선택’적인 매개변수는 나중에 써줘야 한다는 거다. 함수를 사용할 때는 괄호 안에 적힌 순서대로 인수를 받아 처리하기 때문이다.

## 함수의 매개변수(parameter)가 많다면 키워드 인수(keyword arguments)를 활용하면 된다.

애초에 너무 많은 선택적 매개변수를 가진 함수가 있다면

```python
def test(a=1, b=2, c=False, d=True):
    return
```

‘키워드 인수’를 활용하게 된다. 함수를 사용할 때 직접 매개변수를 명시해주는 거다.

```python
test(a=2, d=False)
```

## 리스트와 같은 자료형을 인수로 지정할 때는 기본값을 `None`으로 지정하는 것이 좋다.

아래 코드를 보자. current_order라는 선택적 인수를 받는 함수다.

```python
def update_order(new_item, current_order=[]):
    current_order.append(new_item)
    return current_order
```

그런데 함수의 결과로 current_order라는 값을 돌려주기 때문에, 이 값을 다른 변수에 저장하더라도 계속 current_order라는 리스트가 따라다니면서 함수를 새로 사용할 때도 계속 적용되는 문제가 있다.

그래서 함수의 인수로 리스트, 딕셔너리, 튜플과 같은 형태의 자료형을 선택적으로 받고자 한다면 기본값을 `None`으로 지정하는 것이 좋다. 이렇게.

```python
def update_order(new_item, current_order=None):
    if current_order is None:
        current_order = []
    current_order.append(new_item)
    return current_order
```

## 함수의 `return`으로 여러 개의 값을 반환하면, 그 값들을 쉼표로 구분하여 변수에 저장할 수 있다.

아래와 같이 `return` 구문에서 쉼표를 사용하면 여러 값을 반환할 수 있다,

```python
def multiple_returns(num1, num2):
    sum_nums = num1 + num2
    div_nums = num1 / num2
    return sum_nums, div_nums
```

함수의 결과 값을 그냥 가져오면 튜플 형태로 괄호 안에서 값들이 쉼표로 구분된다.

```python
sum_and_div = multiple_returns(20, 10)

print(sum_and_div)
# "(30, 2)"
print(sum_and_div[0])
# "30"
```

물론 인덱싱을 통해 개별 값에 접근할 수도 있지만, 애초에 함수 결과 값을 변수에 저장할 때 쉼표로 구분해주면 편하다.

```python
result_sum, result_div = sum_and_div(18, 9)

print(result_sum)
# "27"
print(result_div)
# "2"
```

## 함수가 입력 받을 매개변수의 개수가 정해져 있지 않을 때는 `*` 1개를 사용한다.

함수의 입력 값으로 리스트(엄밀히 말하면 튜플)을 받아 처리하는 거라 생각하면 편하다.

```python
def shout_strings(*args):
  for argument in args:
    print(argument.upper())

shout_strings("a", "b", "c")
# "A"
# "B"
# "C"
```

함수 정의할 때 매개변수에 `*args`라고 별 하나를 사용하는 게 바로 이러한 기능을 한다.

## `**` 2개를 사용하여 "키워드 인수"를 받으면 딕셔너리 형태로 그 값을 사용할 수 있다.

이건 무엇인고 하니… 일단 문자열 포맷, 포매팅을 생각해보자.

```python
print("My name is {name} and I'm feeling {feeling}.".format(name="Rick", feeling="confused"))

# "My name is Rick and I'm feeling confused."
```

`.format()`을 사용하면 원하는 ‘키’에 원하는 ‘값’을 지정해줄 수 있다.

함수에서도 마찬가지다. 매개변수 앞에 별표 두 개(`**`)를 사용하면 딕셔너리 형태로 인수를 받아 처리한다는 뜻이다. 예시를 보자.

```python
def create_products(**kwargs):
    for name, price in kwargs.items():
        print(name, price)

create_products(Baseball=3, Shirt=14, Guitar=70)

# "Baseball 3"
# "Shirt 14"
# "Guitar 70"
```

함수에서 이런 방식으로 입력 받는 인수를 `kwargs` (Keywords Argument, 키워드 인수)라고 부른다.

별표 두 개를 사용하면 애초에 딕셔너리로 지정된 값들을 함수의 인수로 처리하는 기능으로도 사용할 수 있다. 이런 식으로.

```python
def create_products(**products_dict):
    for name, price in products_dict.items():
        create_product(name, price)

test = {'Apple': 1, 'Ice Cream': 3, 'Chocolate': 5}

create_products(**test)
```

## 다양한 종류의 매개변수를 입력 받기 위해서는 지정된 순서를 따른다.

위에서 소개한 내용을 종합해보면 결국 파이썬 매개변수는 아래와 같은 순서에 따라 입력을 받아야 한다.

- 미리 정의된 매개변수
- 이름과 개수가 정의되어 있지 않은 매개변수 (*args) – 리스트, 튜플
- 미리 정의된 키워드 매개변수 – 딕셔너리
- 이름과 개수가 정의되어 있지 않은 키워드 매개변수 (*kwargs) – 딕셔너리

예를 들어 텍스트 파일 본문에서 특정 단어들은 동일한 단어로(예를 들면 빈 문자열””로) 대체 혹은 삭제하고, 특정 단어들은 각각 지정된 다른 단어로 대체하는 함수를 만든다고 하면 이렇게 써볼 수 있겠다.

```python
def remove(filename, *args, **kwargs):
    with open(filename) as file_obj:
        text = file_obj.read()
    for arg in args:
        text = text.replace(arg, "")
    for kwarg, replacement in kwargs.items():
        text = text.replace(kwarg, replacement)
    return text

print(remove("text.txt", "generous", "gallant", fond="amused by", Robin="Mr. Robin"))
```

오늘 포스팅은 여기까지.