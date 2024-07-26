---
title: 파이썬 문법 꿀팁 100선
date: 2022-11-03
categories: [WORK, Python]
tags: [Python]
---

[100 Helpful Python Tips You Can Learn Before Finishing Your Morning Coffee](https://towardsdatascience.com/100-helpful-python-tips-you-can-learn-before-finishing-your-morning-coffee-eb9c39e68958)의 번역 글. (모닝 커피 마시는 동안 배울 수 있는 100개의 파이썬 팁…?)

이해를 돕기 위해 일부 내용은 수정/보완했고, 내가 옮겨 적으면서 기억하려고 남기는 글이기에, 개인적으로 유용한 것에는 ⭐ 표시를 붙였다.

## 1. for-else 문

보통 프로그래밍 언어에서 ‘else’라고 하면 if와 함께 오는 경우가 대부분이다. 하지만 파이썬에서는 for 문과도 함께 쓸 수도 있다.

for문이 중간에 break 등으로 끊기지 않고 끝까지 수행 되었을 때 수행하는 코드를 담으면 된다. else는 for와 같은 수준으로 들여쓰기 해야 한다.

```
numbers = [2, 4, 1, 8, 6]

for number in numbers:
    if number % 2 == 1:
        print(number)
        break
else:
    print("No odd numbers")
```

위 예시는 리스트에 홀수가 있으면 반복문을 중단하는 코드. 홀수가 없으면 문자열이 출력되지만, 이 경우에는 홀수 1이 출력되고, 반복문이 중단된다.

for-else는 반복문이 중간에 break 되었는지 판별해야 하는 경우에 유용하다.

## 2. 변수명을 나열하여 리스트에서 요소들을 각각 가져오기

```
my_list = [1, 2, 3, 4, 5]
one, two, three, four, five = my_list
```

## 3. `heapq`를 사용하여 리스트에서 n개의 가장 큰/작은 요소를 가져오기⭐

`heapq`는 파이썬 내장 모듈이다.

```
import heapq

scores = [51, 33, 64, 87, 91, 75, 15, 49, 33, 82]

print(heapq.nlargest(3, scores))  # [91, 87, 82]
print(heapq.nsmallest(5, scores))  # [15, 33, 33, 49, 51]
```

이걸 응용하면 딕셔너리에서도 key나 value를 기준으로 가장 큰, 가장 작은 값을 가져올 수 있다.

```
scores = {"a": 51, "b": 33, "c": 64, "d": 87, "e": 91, "f": 75, "g": 15, "h": 49, "i": 33, "j": 82}

# sort by keys
print(heapq.nlargest(3, scores))  # ['j', 'i', 'h']
print(heapq.nsmallest(3, scores))  # ['a', 'b', 'c']

# sort by values
print(heapq.nlargest(3, scores.items(), key=lambda item: item[1]))  # [('e', 91), ('d', 87), ('j', 82)]
print(heapq.nsmallest(3, scores.items(), key=lambda item: item[1]))  # [('g', 15), ('b', 33), ('i', 33)]
```

## 4. 리스트에 `*` 사용하여 처리하기

`*`를 사용하여 리스트의 모든 요소를 추출할 수 있다.

```
my_list = [1, 2, 3, 4]
print(my_list)  # [1, 2, 3, 4]

print(*my_list)  # 1 2 3 4
```

이 방법은 리스트의 모든 요소를 메서드 인수로 전달하려는 상황에서 유용하다.

```
def sum_of_elements(*arg):
    total = 0
    for i in arg:
        total += i

    return total


result = sum_of_elements(*[1, 2, 3, 4])
print(result)  # 10
```

## 5. 리스트 중간 있는 요소 가져오기

리스트에서 위치를 기준으로 앞이나, 뒤의 요소들을 잘라서 가져오는 신기한 방법.

`_`와 `*`를 사용한다.

```
_, *elements_in_the_middle, _ = [1, 2, 3, 4, 5, 6, 7, 8]
print(elements_in_the_middle)  # [2, 3, 4, 5, 6, 7]

_, _, *elements_in_the_middle, _,  _ = [1, 2, 3, 4, 5, 6, 7, 8]
print(elements_in_the_middle)  # [3, 4, 5, 6]
```

## 6. 한 줄에서 변수 여러개 할당

```
one, two, three, four = 1, 2, 3, 4
```

## 7. 리스트 컴프리헨션 ⭐

리스트 컴프리헨션은 반복문을 한 줄로 처리할 수 있는 우아한 문법이다.

```
numbers = [1, 2, 3, 4, 5, 6, 7, 8]

even_numbers = [number for number in numbers if number % 2 == 0]

print(even_numbers)  # [2, 4, 6, 8]
```

리스트 컴프리헨션은 딕셔너리에서도 활용할 수 있다.

```
dictionary = {'first_element': 1, 'second_element': 2,
              'third_element': 3, 'fourth_element': 4}
odd_value_elements = {key: num for (key, num) in
                      dictionary.items() if num % 2 == 1}
print(odd_value_elements)  # {'first_element': 1, 'third_element': 3}
```

## 8. `enum`을 사용해 동일한 개념의 항목 반복하기

[파이썬 공식문서 enum 설명](https://docs.python.org/3/howto/enum.html)

An `Enum`(https://docs.python.org/ko/3/library/enum.html#enum.Enum) is a set of symbolic names bound to unique values. They are similar to global variables, but they offer a more useful `repr()`(https://docs.python.org/ko/3/library/functions.html#repr), grouping, type-safety, and a few other features.

```
from enum import Enum

class Status(Enum):
    NO_STATUS = -1
    NOT_STARTED = 0
    IN_PROGRESS = 1
    COMPLETED = 2

print(Status.IN_PROGRESS.name)  # IN_PROGRESS
print(Status.COMPLETED.value)  # 2
```

## 9. 연산자 `*` 활용하여 문자열 반복하기

```
string = "Abc"

print(string * 5)  # AbcAbcAbcAbcAbc
```

## 10. 숫자 3개 한 줄에서 비교하기

파이썬에서는 _1 < x and x < 10_ 과 같은 비교를 한 줄로 처리할 수 있다.

```
x = 3
print(1 < x < 10)   # True
```

## 11. `|`를 사용하여 딕셔너리 병합하기 ⭐

파이썬 3.9부터 사용 가능한 문법.

```
first_dictionary = {'name': 'Fatos', 'location': 'Munich'}
second_dictionary = {'name': 'Fatos', 'surname': 'Morina', 'location': 'Bavaria, Munich'}

result = first_dictionary | second_dictionary
print(result)  
# {'name': 'Fatos', 'location': 'Bavaria, Munich', 'surname': 'Morina'}
```

## 12. 튜플에서 특정 요소의 인덱스 찾기

```
books = ('Atomic habits', 'Ego is the enemy', 'Outliers', 'Mastery')

print(books.index('Mastery'))   # 3
```

## 13. 문자열 형식의 리스트를 진짜 리스트로 변환하기

이렇게 리스트처럼 생긴 문자열을 `[1,2,3]` 진짜 리스트 `[1,2,3]`로 바꾸고 싶을 때… 생각보다 간단하지 않다.

물론 복잡한 정규식을 쓸 수도 있겠지만, `ast` 모듈에서 `literal_eval` 메서드를 사용하면 편하게 처리할 수 있다.

```
import ast

def string_to_list(string):
    return ast.literal_eval(string)

string = "[[1, 2, 3],[4, 5, 6]]"
my_list = string_to_list(string)
print(my_list)  # [[1, 2, 3], [4, 5, 6]]
```

## 14. 매개변수 이름을 활용하여 사소한 실수 줄이기

아래와 같이 subtract라는 함수를 만들어서 사용하는 데, 함수 안에 넣어줄 인수의 순서가 바뀌면 괜히 실수가 날 수 있다.

```
def subtract(a, b):
    return a - b

print((subtract(1, 3)))  # -2
print((subtract(3, 1)))  # 2
```

아래와 같이 매개변수 이름을 정확히 적어서 실수를 줄이자.

```
def subtract(a, b):
    return a - b


print((subtract(a=1, b=3)))  # -2
print((subtract(b=3, a=1)))  # -2
```

## 15. print 한 줄로 처리하기

```
print(1, 2, 3, "a", "z", "this is here", "here is something else")
```

## 16. print 여러 줄로 처리하더라도 한 줄에 출력되도록

`end` 파라미터를 사용하면 된다.

```
print("Hello", end="")
print("World")  # HelloWorld
print("Hello", end=" ")
print("World")  # Hello World
print('words',   'with', 'commas', 'in', 'between', sep=', ')
# words, with, commas, in, between
```

## 17. print 여러 개 출력할 때 사용자 정의 구분자 `sep` ⭐

`sep` 파라미터를 사용하면 더 예쁘게 출력할 수 있다.

```
print("29", "01", "2022", sep="/")  # 29/01/2022
print("name", "domain.com", sep="@")  # name@domain.com
```

## 18. 파이썬 변수명 맨 앞에 숫자는 올 수 없음

```
four_letters = “abcd” # this works
4_letters = “abcd” # this doesn’t work
```

## 19. 파이썬 변수명 맨 앞에 연산자가 올 수 없음

```
+variable = “abcd”  # this doesn’t work
```

## 20. 숫자 맨 앞 자리는 0이 될 수 없음 ⭐

```
number = 0110 # this doesn't work
```

만약 문자열로 “0110” 같은 형식의 숫자를 만들고 싶다면 [zfill](https://docs.python.org/3/library/stdtypes.html?highlight=zfill#str.zfill) 메서드를 활용하면 된다.

## 21. 언더스코어 “_”는 변수명 어디에도 사용할 수 있음

```
a______b = "abcd"  # this works
_a_b_c_d = "abcd"  # this also works
```

## 22. 자리수가 큰 숫자는 언더스코어 “_”를 사용하여 읽기 편하게 처리하기

숫자 사이에 언더스코어 `_` 1개가 들어오는 건 무시된다. (자리수 구분, 가독성을 위해 자유롭게 사용할 수 있다는 뜻)

```
print(1_000_000_000)  # 1000000000
print(1_234_567)  # 1234567

a = 1_000_000
b = 3_000
print(a + b)  # 1003000
```

## 23. 리스트 순서 뒤집기

```
my_list = ['a', 'b', 'c', 'd']

my_list.reverse()

print(my_list)  # ['d', 'c', 'b', 'a']
```

## 24. 문자열 건너뛰어 슬라이싱

```
my_string = "This is just a sentence"
print(my_string[0:5])  # This

# Take N steps forward
print(my_string[0:10:1])  # This is ju
print(my_string[0:10:2])  # Ti sj
print(my_string[0:10:3])  # Tssu
```

## 25. 문자열 순서 뒤집어서 슬라이싱

```
my_string = "This is just a sentence"
print(my_string[10:0:-1])  # suj si sih

# Take N steps forward
print(my_string[10:0:-1])  # suj si sih
print(my_string[10:0:-2])  # sjs i
print(my_string[10:0:-3])  # s  h
```

## 26. 슬라이싱 할 때 시작/끝 인덱스는 생략 가능

```
my_string = "This is just a sentence"
print(my_string[4:])  # is just a sentence

print(my_string[:3])  # Thi
```

## 27. 나누기 후 소수점 이하 버리기

```
print(3/2)  # 1.5
print(3//2)  # 1
```

## 28. `==` 와 `is` 의 차이

`==`는 이 두 개체가 가진 값이 일치하는지 비교하는 반면, `is`는 두 변수가 메모리의 동일한 객체를 가리키는지 확인한다.

```
first_list = [1, 2, 3]
second_list = [1, 2, 3]

# ==는 값이 같은지 확인
print(first_list == second_list)  # True

# is는 메모리 내의 동일한 객체를 가리키는지 확인
print(first_list is second_list)  
# False

third_list = first_list

print(third_list is first_list)  
# True
```

실제로 변수의 id를 확인해보면 first_list와 third_list는 동일한 ID를 갖고 있지만, second_list의 id는 다르다는 걸 확인할 수 있다.

```
print(id(first_list))
print(id(second_list))
print(id(third_list))
```

## 29. 딕셔너리 쉽게 병합하기 ⭐

```
dictionary_one = {"a": 1, "b": 2}
dictionary_two = {"c": 3, "d": 4}

merged = {dictionary_one, dictionary_two}

print(merged)  # {'a': 1, 'b': 2, 'c': 3, 'd': 4}
```

## 30. 부등호 연산자를 사용하여 문자열 비교하기

문자열에 부등호를 사용하면, 해당 문자열을 ascii 숫자로 변환해서 맨 앞의 문자부터 비교한다.

```
first = "abc"
second = "def"
print(first < second)  # True
second = "ab"
print(first < second)  # False
```

아스키코드표를 따르면, 혹은 `ord`함수를 이용하면 이렇게 몇 번째 유니코드 정수 값을 구할 수 있다. (39번 참고)

```
print(ord('a'))  # 97
print(ord('z'))  # 122
```

## 31. 문자열이 특정 문자로 시작하는지 확인하기 ⭐

인덱싱 하지 않고 `startswith`로 처리하는 방법.

```
my_string = "abcdef"
print(my_string.startswith("b"))  # False
```

## 32. id()를 사용하여 변수의 고유 ID 찾기

```
print(id(1))  # 4325776624
print(id(2))  # 4325776656
print(id("string"))  # 4327978288
```

## 33. 정수, 실수, 문자열, 불리언, 튜플은 수정 불가(immutable)

정수, 부동소수점, 문자열, 부울, 튜플처럼 수정 불가(immutable)한 객체에 변수를 할당하면 이 변수는 메모리의 객체를 가리킨다.

그래서 만약 해당 변수에 다른 값을 할당하는 경우 원래의 객체는 여전히 메모리에 있지만 이를 가리키는 변수는 손실된다.

```
number = 1
print(id(number))  # 4325215472
print(id(1))  # 4325215472

number = 3
print(id(number))  # 4325215536
print(id(1))  # 4325215472
```

## 34. 문자열과 튜플은 수정 불가 (immutable)

바로 위에서 언급한 내용이지만 너무나 중요한 내용이기 때문에 다시 한 번 강조한다…?

```
name = "Fatos"
print(id(name))  # 4422282544
name = "fatos"
print(id(name))  # 4422346608

my_tuple = (1, 2, 3, 4)
print(id(my_tuple))  # 4499290128
my_tuple = ('a', 'b')
print(id(my_tuple))  # 4498867584
```

## 35. 리스트, 집합, 딕셔너리는 수정 가능(mutable)

이는 바인딩된 객체를 잃지 않고도 해당 변수를 변경할 수 있음을 의미한다.

```
cities = ["Munich", "Zurich", "London"]
print(id(cities))  # 4482699712
cities.append("Berlin")
print(id(cities))  # 4482699712

my_set = {1, 2, 3, 4}
print(id(my_set))  # 4352726176
my_set.add(5)
print(id(my_set))  # 4352726176
```

## 36. set을 수정 불가(immutable)하도록 변경하기

`frozenset`을 사용하면 immutable하도록 바꿀 수 있다.

```
my_set = frozenset(['a', 'b', 'c', 'd'])
my_set.add("a")    # AttributeError: 'frozenset' object has no attribute 'add'
```

## 37. if-elif 블록 끝에 else는 생략 가능

그러나 `elif`는 `if` 없이는 단독으로 쓰일 수 없다.

```
def check_number(number):
    if number > 0:
        return "Positive"
    elif number == 0:
        return "Zero"

    return "Negative"


print(check_number(1))  # Positive
```

## 38. `sorted()`를 사용하여 2개의 문자열이 어구전철인지 확인

[어구전철(語句轉綴) 또는 애너그램(anagram)](https://ko.wikipedia.org/wiki/%EC%96%B4%EA%B5%AC%EC%A0%84%EC%B2%A0)은 단어나 문장을 구성하고 있는 문자의 순서를 바꾼 거다.

구성하는 문자가 순서 상관 없이 일치하는지 확인하는 방법.

```
def check_if_anagram(first_word, second_word):
    first_word = first_word.lower()
    second_word = second_word.lower()
    return sorted(first_word) == sorted(second_word)
print(check_if_anagram("testinG", "Testing"))  # True
print(check_if_anagram("Here", "Rehe"))  # True
print(check_if_anagram("Know", "Now"))  # False
```

## 39. 문자에 해당하는 유니코드 값 얻기

`ord`는 해당 문자에 해당하는 유니코드 정수를 반환한다.

```
print(ord("A"))  # 65
print(ord("B"))  # 66
print(ord("C"))  # 66
print(ord("a"))  # 97
```

## 40. 딕셔너리 key만 가져오기

```
dictionary = {"a": 1, "b": 2, "c": 3}

keys = dictionary.keys()

print(list(keys))  # ['a', 'b', 'c']
```

## 41. 딕셔너리 value만 가져오기

```
dictionary = {"a": 1, "b": 2, "c": 3}

values = dictionary.values()

print(list(values))  # [1, 2, 3]
```

## 42. 딕셔너리에서 key, value 바꿔치기

리스트 컴프리헨션을 사용하면 간단히 처리할 수 있다.

```
dictionary = {"a": 1, "b": 2, "c": 3}

reversed_dictionary = {j: i for i, j in dictionary.items()}

print(reversed)  # {1: 'a', 2: 'b', 3: 'c'}
```

## 43. boolean 값을 숫자 1 또는 0으로 변환하기

```
print(int(True))  # 0
print(int(False))  # 0

print(float(True))  # 1.0
print(float(False))  # 0.0
```

## 44. boolean 값을 산술 연산에 사용하기

True는 1, False는 0이니까…

```
x = 10
y = 12
result = (x - False)/(y * True)
print(result)  # 0.8333333333333334
```

## 45. 모든 자료형은 boolean으로 표현 가능

```
print(bool(3))  # True
print(bool(.0))  # False

print(bool([1,2,3]))  # True
print(bool([]))  # False

print(bool("string"))  # True
print(bool(""))  # False
print(bool(" "))  # True
```

## 46. 복소수, 2진수/8진수/16진수 표현하기

복소수 변환은 `complex()` 함수를 사용한다.

```
print(complex(10, 2))  # (10+2j)
```

이 외에도 `bin()` , `oct()`, `hex()` 함수를 사용하면 숫자를 각각 2진수, 8진수, 16진수 형태의 문자열로 변환할 수 있다.

```
print(bin(11))  # 0b1011
print(oct(11))  # 0o13
print(hex(11))  # 0xb
```

파이썬에서는 기본적으로 10진수 형태로 숫자를 표현하기 때문에 다른 진수의 형태로 숫자를 표현하려면 다음과 같이 숫자 앞에 접두어를 붙여준다.

- 2진수: `0b`
- 8진수: `0o`
- 16진수: `0x`

## 47. 리스트 맨 앞에 요소 추가하기

리스트에 append()를 사용하면 맨 마지막에 해당 값를 추가한다. 만약 원하는 위치에 요소를 추가하고 싶다면 insert()를 사용하면 된다. (인덱스, 삽입할 요소 순으로 작성)

```
my_list = [3, 4, 5]

my_list.append(6)
my_list.insert(0, 2)
print(my_list)  # [2, 3, 4, 5, 6]
```

## 48. lambda 함수는 반드시 한 줄로 작성

lambda 함수는 한 줄로만 작성해야 한다.

여러 줄로 작성하면 SyntaxError 에러가 날 거다.

```
comparison = lambda x: if x > 3:
                    print("x > 3")
                else:
                    print("x is not greater than 3")
```

## 49. lambda 함수의 조건문에는 반드시 `else`를 포함하기

lambda 함수에 if 조건문을 이런 식으로 작성하면 SyntaxError 에러가 난다. (람다 자체의 문제가 아니라 조건문의 특성이다.)

```
comparison = lambda x: "x > 3" if x > 3
```

## 50. `filter()`를 사용하여 새로운 객체 돌려받기 ⭐

`filter()`는 lambda 함수와 조합하여 사용할 수 있다.

```
my_list = [1, 2, 3, 4]

odd = filter(lambda x: x % 2 == 1, my_list)

print(list(odd))   # [1, 3]
print(my_list)  # [1, 2, 3, 4]
```

## 51. `map()`을 사용하여 새로운 객체 돌려받기

```
my_list = [1, 2, 3, 4]

squared = map(lambda x: x  2, my_list)

print(list(squared))   # [1, 4, 9, 16]
print(my_list)  # [1, 2, 3, 4]
```

## 52. `range()`에서 사용할 수 있는 매개변수 `step`

```
for number in range(1, 10, 3):
    print(number, end=" ")
# 1 4 7
```

## 53. `range()`는 기본적으로 0부터 시작하므로 생략

그래서 0부터 시작할 거면 0을 그냥 생략하면 된다.

```
def range_with_zero(number):
    for i in range(0, number):
        print(i, end=' ')

def range_with_no_zero(number):
    for i in range(number):
        print(i, end=' ')

range_with_zero(3)  # 0 1 2
range_with_no_zero(3)  # 0 1 2
```

## 54. 자료형의 길이 `len()`는 0과 비교할 필요가 없음

길이가 0보다 크면 기본적으로 True이므로 0과 비교할 필요가 없다.

```
def get_element_with_comparison(my_list):
    if len(my_list) > 0:
        return my_list[0]

def get_first_element(my_list):
    if len(my_list):
        return my_list[0]

elements = [1, 2, 3, 4]
first_result = get_element_with_comparison(elements)
second_result = get_element_with_comparison(elements)

print(first_result == second_result)  # True
```

## 55. 동일한 scope 내에서 동일한 이름의 함수를 여러 개 정의할 수 있음

그러나 가장 마지막에 작성된 함수가 우선하기 때문에 마지막 항목만 호출된다.

```
def get_address():
    return "First address"

def get_address():
    return "Second address"

def get_address():
    return "Third address"

print(get_address())  # Third address
```

## 56. scope 밖에서 해당 속성에 접근하기

```
class Engineer:
    def __init__(self, name):
        self.name = name
        self.__starting_salary = 62000

dain = Engineer('Dain')
print(dain._Engineer__starting_salary)  # 62000
```

## 57. 특정 개체의 메모리 사용량 확인하기

```
import sys

print(sys.getsizeof("bitcoin"))  # 56
```

## 58. 함수를 정의할 때 매개변수의 개수 제한 없애기

`*`을 사용한다.

```
def get_sum(*arguments):
    result = 0
    for i in arguments:
        result += i
    return result

print(get_sum(1, 2, 3))  # 6
print(get_sum(1, 2, 3, 4, 5))  # 15
print(get_sum(1, 2, 3, 4, 5, 6, 7))  # 28
```

## 59. `super()` 또는 클래스 이름을 사용하여 부모클래스의 생성자를 호출하기

`super()`를 사용하여 부모클래스 이니셜라이저 호출하기

```
class Parent:
    def __init__(self, city, address):
        self.city = city
        self.address = address

class Child(Parent):
    def __init__(self, city, address, university):
        super().__init__(city, address)
        self.university = university

child = Child('Zürich', 'Rämistrasse 101', 'ETH Zürich')
print(child.university)  # ETH Zürich
```

부모클래스의 이름을 사용하여 이니셜라이저 호출하기

```
class Parent:
    def __init__(self, city, address):
        self.city = city
        self.address = address

class Child(Parent):
    def __init__(self, city, address, university):
        Parent.__init__(self, city, address)
        self.university = university

child = Child('Zürich', 'Rämistrasse 101', 'ETH Zürich')
print(child.university)  # ETH Zürich
```

`__init__()` 및 `super()`를 사용하는 부모 이니셜라이저에 대한 호출은 자식 클래스의 이니셜라이저 내에서만 사용할 수 있다.

## 60. 클래스 내에서 `+` 연산자 재정의하기

`+`는 `__add__()`함수를 통해 재정의 할 수 있다.

```
class Expenses:
    def __init__(self, rent, groceries):
        self.rent = rent
        self.groceries = groceries

    def __add__(self, other):
        return Expenses(self.rent + other.rent,
                        self.groceries + other.groceries)


april_expenses = Expenses(1000, 200)
may_expenses = Expenses(1000, 300)

total_expenses = april_expenses + may_expenses
print(total_expenses.rent)  # 2000
print(total_expenses.groceries)  # 500
```

## 61. 클래스 내에서 `<`, `==` 등의 연산자 재정의하기

부등호 `<`는 `__lt__()`를 재정의 하면 된다.

```
class Game:
    def __init__(self, score):
        self.score = score

    def __lt__(self, other):
        return self.score < other.score

first = Game(1)
second = Game(2)

print(first < second)  # True
```

`==`는 `__eq__()`를 재정의하면 된다.

```
class Journey:
    def __init__(self, location, destination, duration):
        self.location = location
        self.destination = destination
        self.duration = duration

    def __eq__(self, other):
        return ((self.location == other.location) and
                (self.destination == other.destination) and
                (self.duration == other.duration))

first = Journey('Location A', 'Destination A', '30min')
second = Journey('Location B', 'Destination B', '30min')

print(first == second)
```

클래스 내에서 연산자를 재정의하기 위해서는 아래와 같은 함수를 다뤄주면 된다.

- + : `__add__()`
- – : `__sub__()`
- * : `__mul__()`
- / : `__truediv__()`
- == : `__eq__()`
- != : `__ne__()`
- > : `__gt__()`
- < : `__lt__()`
- >= : `__ge__()`
- <= : `__le__()`

## 62. 클래스 내에서 개체에 대한 print 표현형을 사용자 정의하기

클래스 내에서 `__repr__()`를 조작하면 print를 통해 출력했을 때 어떻게 표현할지 직접 정의할 수 있다.

```
class Rectangle:
    def __init__(self, a, b):
        self.a = a
        self.b = b

    def __repr__(self):
        return repr('Rectangle with area=' + str(self.a * self.b))

print(Rectangle(3, 4))  # 'Rectangle with area=12'
```

## 63. 문자열 대문자/소문자 바꾸기

문자열에 대해 `swapcase()`를 사용하면 대문자/소문자를 바꿔준다.

```
string = "This is just a sentence."
result = string.swapcase()
print(result)  # tHIS IS JUST A SENTENCE.
```

## 64. 문자열이 공백인지 확인하기

문자열에 대해 `isspace()`를 사용하면 모든 문자가 공백문자인지 확인한다.

```
string = "     "
result = string.isspace()
print(result)  # True

string = ""
result = string.isspace()
print(result)  # False
```

## 65. 문자열의 모든 문자가 알파벳 또는 숫자인지 확인하기

문자열에 대해 `isalnum()`을 사용하면 모든 문자가 알파벳과 숫자로만 이루어져있는지 확인한다.

```
name = "Password"
print(name.isalnum())  # True, because all characters are alphabets

name = "Secure Password "
print(name.isalnum())  # False, because it contains whitespaces

name = "S3cur3P4ssw0rd"
print(name.isalnum())  # True

name = "133"
print(name.isalnum())  # True, because all characters are numbers
```

## 66. 문자열의 모든 문자가 알파벳인지 확인하기

문자열에 대해 `isalpha()`를 사용하면 모든 문자가 알파벳으로만 이루어져있는지 확인한다.

```
string = "Name"
print(string.isalpha())  # True

string = "Firstname Lastname"
print(string.isalpha())  # False, because it contains whitespace

string = “P4ssw0rd”
print(string.isalpha())  # False, because it contains numbers
```

## 67. 문자열 양쪽에서 특정 문자 제거하기

문자열에 대해 `strip([chars])`, `lstrip([chars])`, `rstrip([chars])`과 같은 함수를 사용할 때 인자로 문자열을 넣어주면 해당 문자를 처음과 끝에서 찾아 제거한다. 그냥 strip은 양쪽에서, l은 왼쪽, r은 오른쪽에서 찾아 제거.

```
string = "This is a sentence with       "
print(string.rstrip())  # "This is a sentence with"

string = "this here is a sentence…..,,,,aaaaasd"
print(string.rstrip(".,dsa"))  # "this here is a sentence"

string = "ffffffffFirst"
print(string.lstrip("f"))  # First
```

## 68. 문자열이 숫자 형식인지 확인하기 ⭐

문자열에 `isdigit()`을 사용하면 해당 문자열이 숫자 형식인지 확인한다.

```
string = "seven"
print(string.isdigit())  # False

string = "1337"
print(string.isdigit())  # True

string = "5a"
print(string.isdigit())  # False, because it contains the character 'a'

string = "25"
print(string.isdigit())  # False
```

## 69. 문자열이 중국어로 숫자인지 확인하기

문자열에 `isnumeric()`을 사용하면 숫자 표현에 해당하는 문자열인지 확인한다.

중국어뿐만 아니라 제곱근, 분수, 거듭제곱 등의 특수문자가 있더라도 `isnumeric()` 함수는 True를 반환한다.

```
# 42673 in Arabic numerals
string = "四二六七三"

print(string.isdigit())  # False
print(string.isnumeric())  # True
```

## 70. 문자열의 모든 단어 첫 글자가 대문자인지 확인하기

문자열에 `istitle()`을 사용하면 영어에서 제목을 표현할 때처럼 모든 단어 첫 글자가 문자열인지 확인한다.

```
string = "This is a sentence"
print(string.istitle())  # False

string = "10 Python Tips"
print(string.istitle())  # True

string = "How to Print A String in Python"
print(string.istitle())  # False, because of the first characters being lowercase in "to" and "in"

string = "PYTHON"
print(string.istitle())  # False. It's titlelized version is "Python"
```

## 71. 튜플에 음수 인덱스 사용 가능

```
numbers = (1, 2, 3, 4)

print(numbers[-1])  # 4
print(numbers[-4])  # 1
```

## 72. 튜플 내부에 리스트와 튜플 둘 다 담는 것도 가능

```
mixed_tuple = (("a"*10, 3, 4), ['first', 'second', 'third'])

print(mixed_tuple[1])  # ['first', 'second', 'third']
print(mixed_tuple[0])  # ('aaaaaaaaaa', 3, 4)
```

## 73. 리스트에 조건을 만족하는 요소가 있는지 카운트 하기

리스트에 `count()`를 사용하면 빠르게 해당 요소가 몇 개 있는지 확인할 수 있다.

```
names = ["Besim", "Albert", "Besim", "Fisnik", "Meriton"]

print(names.count("Besim"))  # 2
```

## 74. `slice()`를 사용하여 마지막 n개의 요소 슬라이싱하기

인덱싱을 하는 것과 다르다.

```
my_list = [1, 2, 3, 4, 5, 6]
slicing = slice(-3, None)

# Getting the last 3 elements from the list
print(my_list[slicing])  # [4, 5, 6]
# Getting only the third element starting from the right
print(my_list[-3])  # 4

string = "Data Science"
# start = 1, stop = None (don't stop anywhere), step = 1
# contains 1, 3 and 5 indices
slice_object = slice(5, None)
print(string[slice_object])   # Science
```

## 75. 튜플에 조건을 만족하는 요소가 있는지 카운트 하기

튜플에 `count()`를 사용하면 빠르게 해당 요소가 몇 개 있는지 확인할 수 있다. (73번과 같은 내용인데 굳이…?)

```
my_tuple = ('a', 1, 'f', 'a', 5, 'a')

print(my_tuple.count('a'))  # 3
```

## 76. 튜플에서 특정 요소의 인덱스 추출하기

튜플에 `index()`를 사용하면 빠르게 해당 요소가 몇 번째에 위치했는지 인덱스를 확인할 수 있다.

```
my_tuple = ('a', 1, 'f', 'a', 5, 'a')
print(my_tuple.index('f'))  #  2
```

## 77. 튜플에서 특정 간격으로 건너뛰어 요소 추출하기

```
my_tuple = (1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
print(my_tuple[::3])  # (1, 4, 7, 10)
```

## 78. 튜플에서 특정 인덱스부터 끝까지 요소 추출하기

```
my_tuple = (1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
print(my_tuple[3:])  # (4, 5, 6, 7, 8, 9, 10)
```

## 79. 리스트, 집합, 딕셔너리에서 모든 요소 제거하기

`clear()`를 사용하면 된다.

```
my_list = [1, 2, 3, 4]
my_list.clear()
print(my_list)  # []

my_set = {1, 2, 3}
my_set.clear()
print(my_set)  # set()

my_dict = {"a": 1, "b": 2}
my_dict.clear()
print(my_dict)  # {}
```

## 80. 두 개의 집합 합치기 (합집합 구하기)

`union()`을 사용하면 된다.

```
first_set = {1, 4, 5, 6}
second_set = {1, 2, 3, 7}

print(first_set.union(second_set))  # {1, 2, 3, 4, 5, 6, 7}
```

아니면 두 번째 집합의 요소를 첫 번째 집합에 삽입하는 `update()`를 사용할 수도 있다.

```
first_set = {1, 4, 5, 6}
second_set = {1, 2, 3, 7}

first_set.update(second_set)
print(first_set)  # {1, 2, 3, 4, 5, 6, 7}
```

## 81. print문 안에 if 조건문 사용하기 ⭐

```
def is_positive(number):
    print("Positive" if number > 0 else "Negative")  # Positive

is_positive(-3) # Negative
is_positive(4)  # Positive
```

## 82. 하나의 if 안에서 모든 조건을 만족하는지 확인하기 ⭐

리스트 안에 검사할 조건을 다 넣어두고, `all()`을 사용하는 방식. 처음 알았다.

```
math_points = 51
biology_points = 78
physics_points = 56
history_points = 72

my_conditions = [
    math_points > 50, 
    biology_points > 50,
    physics_points > 50, 
    history_points > 50
    ]

if all(my_conditions):
    print("Congratulations! You have passed all of the exams.")
else:
    print("I am sorry, but it seems that you have to repeat at least one exam.")
    
# Congratulations! You have passed all of the exams.
```

## 83. 하나의 if 안에서 여러 조건 중 하나라도 만족하는지 확인하기⭐

리스트 안에 검사할 조건을 다 넣어두고, `any()`를 사용하는 방식. 이것도 처음 알았다.

```
math_points = 41
biology_points = 58
physics_points = 36
history_points = 42

my_conditions = [
    math_points > 50, 
    biology_points > 50,
    physics_points > 50, 
    history_points > 50
    ]

if any(my_conditions):
    print("Congratulations! You have passed all of the exams.")
else:
    print("I am sorry, but it seems that you have to repeat at least one exam.")

# Congratulations! You have passed all of the exams.
```

## 84. 비어있지 않은 모든 문자열은 True

```
print(bool("Non empty"))  # True
print(bool(" "))  # True
print(bool(""))  # False
```

## 85. 비어있지 않은 모든 리스트, 튜플, 딕셔너리는 True

```
print(bool([]))  # False
print(bool(set([])))  # False
print(bool({}))  # False
print(bool({"a": 1}))  # True
```

## 86. None, False, 0은 False

```
print(bool(False))  # False
print(bool(None))  # False
print(bool(0))  # False
print(bool("False"))  # True
```

## 87. 함수 내에서는 전역 변수의 값을 변경할 수 없음

```
string = "string"

def do_nothing():
  string = "inside a method"

do_nothing()

print(string)  # string
```

만약 함수 내에서 전역 변수에 접근하고 싶다면 `global`을 사용해야 한다.

```
string = "string"

def do_nothing():
    global string
    string = "inside a method"

do_nothing()

print(string)  # inside a method
```

## 88. `collections`의 `Counter`를 사용해 문자열이나 리스트에서 요소 개수 카운트 하기 ⭐

`Counter()`는 정말 유용하다.

```
from collections import Counter

result = Counter("Banana")
print(result)  # Counter({'a': 3, 'n': 2, 'B': 1})

result = Counter([1, 2, 1, 3, 1, 4, 1, 5, 1, 6])
print(result)  # Counter({1: 5, 2: 1, 3: 1, 4: 1, 5: 1, 6: 1})
```

## 89. `collections`의 `Counter`를 사용해 두 문자열이 애너그램인지 확인하기

[어구전철(語句轉綴) 또는 애너그램(anagram)](https://ko.wikipedia.org/wiki/%EC%96%B4%EA%B5%AC%EC%A0%84%EC%B2%A0)은 단어나 문장을 구성하고 있는 문자의 순서를 바꾼 것.

`Counter()`를 사용해서 확인할 수 있다.

```
from collections import Counter

def check_if_anagram(first_string, second_string):
    first_string = first_string.lower()
    second_string = second_string.lower()
    return Counter(first_string) == Counter(second_string)


print(check_if_anagram('testinG', 'Testing'))  # True
print(check_if_anagram('Here', 'Rehe'))  # True
print(check_if_anagram('Know', 'Now'))  # False
```

`sorted()`를 사용해서 확인하는 방법도 있다.

```
def check_if_anagram(first_word, second_word):
    first_word = first_word.lower()
    second_word = second_word.lower()
    return sorted(first_word) == sorted(second_word)

print(check_if_anagram("testinG", "Testing"))  # True
print(check_if_anagram("Here", "Rehe"))  # True
print(check_if_anagram("Know", "Now"))  # False
```

## 90. `itertools`의 `count`를 사용해 리스트에서 요소 개수 카운트 하기

```
from itertools import count

my_vowels = ['a', 'e', 'i', 'o', 'u', 'A', 'E', 'I', 'O', 'U']

current_counter = count()

string = "This is just a sentence."

for i in string:
    if i in my_vowels:
        print(f"Current vowel: {i}")
        print(f"Number of vowels found so far: {next(current_counter)}")
```

## 91. 문자열, 리스트의 요소를 빈도 순으로 정렬하기

기본적으로 `collections.Counter()`는 빈도에 따라 정렬해주진 않지만, `most_common()`을 사용해주면 쉽게 해결된다.

```
from collections import Counter

result = Counter([1, 2, 3, 2, 2, 2, 2])
print(result)  # Counter({2: 5, 1: 1, 3: 1})
print(result.most_common())  # [(2, 5), (1, 1), (3, 1)]
```

## 92. 리스트에서 가장 많은 요소 쉽게 찾기

`max()`와 `set()`을 조합하는 방법

```
my_list = ['1', 1, 0, 'a', 'b', 2, 'a', 'c', 'a']

print(max(set(my_list), key=my_list.count))  # a
```

## 93. `copy()`와 `deepcopy()`의 차이점

[파이썬 공식 문서](https://docs.python.org/3/library/copy.html)에는 이렇게 써있다.

- A shallow copy constructs a new compound object and then (to the extent possible) inserts references into it to the objects found in the original.
- A deep copy constructs a new compound object and then, recursively, inserts copies into it of the objects found in the original.

이게 `copy()`의 예시

```
first_list = [[1, 2, 3], ['a', 'b', 'c']]
second_list = first_list.copy()
first_list[0][2] = 831

print(first_list)  # [[1, 2, 831], ['a', 'b', 'c']]
print(second_list)  # [[1, 2, 831], ['a', 'b', 'c']]
```

이게 `deepcopy()`의 예시

```
import copy

first_list = [[1, 2, 3], ['a', 'b', 'c']]
second_list = copy.deepcopy(first_list)
first_list[0][2] = 831

print(first_list)  # [[1, 2, 831], ['a', 'b', 'c']]
print(second_list)  # [[1, 2, 3], ['a', 'b', 'c']]
```

## 94. 딕셔너리에 존재하지 않는 키 접근할 때 오류 방지 ⭐

딕셔너리에서 존재하지 않는 키에 접근하면 KeyError 오류가 발생한다.

```
my_dictonary = {"name": "Name", "surname": "Surname"}
print(my_dictonary["age"])  # KeyError: 'age'
```

`collections`의 `defaultdict()`를 사용하면 딕셔너리 키 에러 문제를 방지할 수 있다.

```
from collections import defaultdict

my_dictonary = defaultdict(str)
my_dictonary['name'] = "Name"
my_dictonary['surname'] = "Surname"

print(my_dictonary["age"]) 
```

## 95. 직접 나만의 반복자(iterator)를 만들기

```
class OddNumbers:
    def __iter__(self):
        self.a = 1
        return self

    def __next__(self):
        x = self.a
        self.a += 2
        return x


odd_numbers_object = OddNumbers()
iterator = iter(odd_numbers_object)

print(next(iterator))  # 1
print(next(iterator))  # 3
print(next(iterator))  # 5
```

## 96. 리스트에서 중복 항목 제거하기

`set()`을 활용한다.

```
my_set = set([1, 2, 1, 2, 3, 4, 5])
print(list(my_set))  # [1, 2, 3, 4, 5]
```

## 97. 모듈 위치 확인/출력하기

```
import torch

print(torch)  # <module 'torch' from '/Users/...'
```

## 98. “not in”을 사용해 리스트에 있는지 확인하기

```
odd_numbers = [1, 3, 5, 7, 9]
even_numbers = []

for i in range(9):
    if i not in odd_numbers:
        even_numbers.append(i)

print(even_numbers)  # [0, 2, 4, 6, 8]
```

## 99. sort()와 sorted()의 차이점

`sort()`는 원본 목록을 정렬하는 반면, `sorted()`는 새로운 정렬된 목록을 반환해준다.

```
groceries = ['milk', 'bread', 'tea']

new_groceries = sorted(groceries)
# new_groceries = ['bread', 'milk', 'tea']

print(new_groceries)

# groceries = ['milk', 'bread', 'tea']
print(groceries)

groceries.sort()

# groceries = ['bread', 'milk', 'tea']
print(groceries)
```

## 100. `uuid` 모듈을 사용해 고유 ID 생성하기 ⭐

[UUID](https://ko.wikipedia.org/wiki/%EB%B2%94%EC%9A%A9_%EA%B3%A0%EC%9C%A0_%EC%8B%9D%EB%B3%84%EC%9E%90)는 Universally Unique Identifier의 약자. 한국어로 범용 고유 식별자. 이 표준에 따라 ID를 부여하면 실제 사용상에서 중복될 가능성이 거의 없다고 인정되기 때문에 많이 사용되고 있다.

파이썬 내장 모듈 `uuid`가 있다.

```
import uuid

# Generate a UUID from a host ID, sequence number, and the current time
print(uuid.uuid1())  # 308490b6-afe4-11eb-95f7-0c4de9a0c5af

# Generate a random UUID
print(uuid.uuid4())  # 93bc700b-253e-4081-a358-24b60591076a
```