---
title: 파이썬 클래스(Class) 이해하기
date: 2020-03-09
categories: [WORK, 🐍 Python]
tags: [Python]
---

프로그래밍 입문 언어로 파이썬을 공부하게 되면 우선 여러가지 자료형, 조건문, 반복문, 함수를 익혀 어느정도 기초를 다지는 커리큘럼을 따른다.

그런데 그 다음에 클래스(Class)라는 개념을 접하고 여기서부터 뭔가 어려워서 이해를 포기하게 된다. 나도 그랬다. 그래서 이번 기회에 클래스에 대해 좀 공부한 걸 최대한 쉽게 남겨보려 한다.

본 포스팅의 목차는 아래와 같다.

- 자료형(type) 복습하기
- 클래스(Class)란 무엇인가
- 인스턴스 생성하기 (Instantiation)
	- 객체 지향 프로그래밍 (Object-Oriented Programming)
- 클래스 변수 (Class Variables)
- 매서드 (Methods)
- 생성자 (Constructor)
- 인스턴스 변수 (Instance Variables)
- 인스턴스 변수의 활용, `self`
- 파이썬에서는 모든 게 다 객체(Object)다

순서대로 잘 따라와야 이해할 수 있다.

## 자료형(type) 복습하기

일단 본격적인 클래스의 개념을 익히기에 앞서 자료형에 대해 다시 한 번 복기하는 시간을 가져야겠다.

`type()` 함수를 통해 문자열(string)과 정수(integer)의 자료형을 출력해보자.

```python
print(type("string"))
print(type(1))
```

각각 `<class 'str'>`, `<class 'int'>`라고 출력되는 걸 알 수 있다.

이런 변수의 자료형이 왜 중요하냐면, 이 자료형이 무엇인지에 따라 이 녀석을 가지고 뭘 할 수 있을지 결정되기 때문이다.

예를 들어 integer에는 `.get()` 함수를 쓸 수가 없다. dictionary는 두 개 있을 때 그걸 합치기 위해 +같은 연산자를 사용할 수 없다.

이렇게 뭘 할 수 있고 뭘 할 수 없는지에 관한 조건들은 모두 자료형 수준에서 정의되는 거다.

## 클래스(Class)란 무엇인가

클래스는 자료형을 위한 일종의 템플릿이라고 생각하면 된다. 클래스 안에는 다양한 유형의 정보가 담겨 있고, 우리가 자료형과 어떻게 상호작용할 것인지 적혀 있다.

클래스는 그냥 이렇게 class 키워드를 사용해서 정의하면 된다.

```python
class TestClass:
  pass
```

클래스 이름 TestClass는 일부러 대문자로 작성했다. 이게 클래스임을 누구나 쉽게 알아볼 수 있도록 하기 위해 [PEP 8 Style Guide for Python Code](https://www.python.org/dev/peps/pep-0008/#class-names)가 권고하는 방식이다. (함수를 정의할 땐 대문자를 안 쓴다.)

클래스의 본문에 pass라고 썼는데, 예시를 위해 의도적으로 비운 거다. 이렇게 안 하면 IndentationError가 발생하기 때문. 일단 넘어가자.

## 인스턴스 생성하기 (Instantiation)

클래스를 정의하고 나면 그걸 불러와서 사용하는 법을 알아야겠지. 클래스를 이용해 인스턴스(instance)를 만들어주면 된다. “임시로 어떤 템플릿을 불러와서 그걸 다른 이름의 객체로 저장한다”고 생각하면 편하다.

```python
test_instance = TestClass()
```

클래스 이름에 괄호를 추가하여 인스턴스를 만들고, 그걸 변수 test_instance에 할당하는 방식이다.

물론 수많은 인스턴스를 호출하는 것도 가능하다. 어차피 클래스는 템플릿이고, 이걸 각기 다른 용도로 사용할 수 있기 때문에.

```python
test_instance1 = TestClass()
test_instance2 = TestClass()
test_instance3 = TestClass()
```

### 객체 지향 프로그래밍 (Object-Oriented Programming)

클래스 인스턴스는 객체(object)라고도 하며, 이렇게 클래스를 정의하고 객체를 만드는 패턴을 [객체 지향 프로그래밍(OOP)](https://ko.wikipedia.org/wiki/%EA%B0%9D%EC%B2%B4_%EC%A7%80%ED%96%A5_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D)이라고 한다. 너무 깊이 들어가진 말고 이런 방식으로 프로그래밍 하는 패턴이 있다는 것만 알고 넘어가자.

인스턴스를 불러온다는 건 클래스를 가져와 객체로 바꿔준다는 건데, `type()` 함수를 사용하는 건 그 반대라고 생각하면 된다. 객체의 type을 확인해보면 해당 객체가 어떤 클래스의 인스턴스인지를 확인할 수 있다.

```python
print(type(test_instance))
# <class '__main__.TestClass'>
```

test_instance라는 객체는 TestClass의 인스턴스임을 확인할 수 있다.

파이썬에서 `__main__`은 “현재 실행 중인 파일”을 의미하므로, 위 출력 결과는 “이 스크립트에서 정의된, 현재 실행중인 TestClass클래스”를 의미한다고 생각하면 된다.

## 클래스 변수 (Class Variables)

클래스를 정의할 때 그 안에서 변수를 정의하면 모든 인스턴스에서 동일한 데이터를 사용할 수 있다. 이를 클래스 변수라고 부르는데, 이렇게 정의된 클래스 변수는 object.variable 구문을 사용하여 액세스 할 수 있다.

별 거 아니니 예시를 보면 쉽게 이해할 수 있다.

예를 들어 Musician이라는 클래스 안에 title이라는 변수를 정의했다고 해보자.

```python
class Musician:
  title = "Rockstar"
```

그러면 각각 다른 인스턴스를 호출하더라도 각 인스턴스 안에 있는 title이라는 변수는 언제나 똑같다.

```python
drummer = Musician()
guitarist = Musician()

print(drummer.title)
print(guitarist .title)
```

모두 “Rockstar”라는 값이 출력될 거다.

## 매서드 (Methods)

메서드는 클래스 안에서 정의된 함수라고 생각하면 된다.

일반적인 함수를 정의할 때와 차이가 있다면 메서드를 정의할 때는 첫 번째 인수를 항상 self로 지정해야 한다는 것 정도.

아래 예시를 보자.

```python
class Musician:
    title = "Rockstar"

    def explanation(self):
        print("I am a {}!".format(self.title))

drummer = Musician()
drummer.explanation()
#I am a Rockstar!
```

단순히 클래스 변수 title을 사용해서 문자열을 출력하는 메서드를 정의해하고, 그걸 불러와서 사용했다.

메서드를 작성할 때 다른 인수는 필요 없었기 때문에 self만 써주었고, 함수 안에서 클래스 변수를 사용할 때는 `self.title`과 같은 형식으로 불러왔다. 쉽다.

이제 다른 인수가 있는 메서드도 작성해보자.

```python
class Circle():
    pi = 3.14
  
    def area(self, radius):
        return self.pi * radius ** 2
  
circle = Circle()

pizza_area = circle.area(6)
table_area = circle.area(18)
```

Circle이라는 클래스에서 π(pi) 값은 3.14로 넣어주고, 원의 넓이 구하는 공식을 area라는 메서드로 정의했다. 원의 넓이는 π * 반지름²이다. 그리고 메서드를 정의할 때 반지름을 인수로 받아야 하기 때문에 area의 인수로 self와 radius를 넣어줬다. (메서드의 첫 번째 인수는 언제나 self다. 잊지 말자.)

이제 반지름이 6인 원의 넓이는 circle.area(6), 반지름이 18인 원의 넓이는 circle.area(18)로 불러올 수 있게 되는 거다.

여기까지 이해하면 클래스를 만들어서 사용하는 기본적인 방법은 다 익힌 셈이다.

## 생성자 (Constructor)

클래스를 호출할 때 자동으로 어떤 함수를 실행하거나 값을 호출하고 싶다면 생성자(Constructor)라는 걸 정의하면 된다.

메서드 정의할 때와 똑같긴 한데 메서드 이름으로 `__init__`을 사용하면 된다.

예를 들어 이렇게 해주면

```python
class Shouter:
    def __init__(self):
        print("HELLO?!")

shout = Shouter()
```

Shouter라는 클래스를 객체로 불러올 때마다 “HELLO?!”라는 문자열이 즉시 출력된다.

물론 클래스를 호출할 때 애초에 다른 인수(매개변수)를 받는 방식으로 정의하는 것도 가능하다. 이렇게.

```python
class Circle:
    pi = 3.14

    def __init__(self, radius):
        print("New circle with radius: {}".format(radius))

table = Circle(18)
```

Circle이라는 클래스의 생성자 `__init__`을 잘 살펴보면 self 말고도 radius라는 인수를 받는다. 그래서 객체를 생성할 때 괄호 안에 인수를 넣어주어야 한다.

만약 괄호 안에 18을 넣어 radius가 18인 table이라는 객체를 생성하면 바로 “New circle with radius: 18″이라는 문자열이 출력될 거다.

## 인스턴스 변수 (Instance Variables)

지금까지 객체가 클래스의 인스턴스라는 것을 배웠지만, 각 객체가 메서드와 클래스 변수만으로 이루어져 있다면 객체들을 구별해야 할 필요가 있을까?

객체를 구별해서 인스턴스를 생성하는 이유는 각 인스턴스가 다른 종류의 데이터, 즉 인스턴스 변수를 보유할 수 있기 때문이다.

이렇게 각 객체가 보유한 데이터인 인스턴스 변수는 해당 클래스의 모든 인스턴스가 공유하는 게 아니다. (이게 클래스 변수와의 가장 큰 차이점이다.)

예를 보자. 일단 빈 클래스를 하나 정의해놓고, 두 개의 인스턴스(객체)를 불러오자.

```python
class FakeDict:
    pass

fake_dict1 = FakeDict()
fake_dict2 = FakeDict()
```

그리고 각 객체에 fake_key라는 인스턴스 변수로 각각 다른 값을 넣어주면,

```python
fake_dict1.fake_key = "This works!"
fake_dict2.fake_key = "This too!"
```

이걸 별도로 사용할 수 있다.

```python
working_string = "{} {}".format(fake_dict1.fake_key, fake_dict2.fake_key)

print(working_string)
# "This works! This too!"
```

어려운 게 아니다. 그냥 그동안 사용했던 변수의 개념이라고 생각하면 편하다.

## 인스턴스 변수의 활용, `self`

왜 굳이 인스턴스 변수를 사용할까? 그냥 변수로 써도 문제 없는데?

인스턴스를 생성할 때 작동하는 생성자(constructor)를 활용해서 인스턴스 변수를 생성하고 관리하면 너무나 편리하기 때문이다.

예시를 보자.

```python
class AutoEmail:
    def __init__(self, name):
        self.name = name

mail_to_park = AutoEmail("PARK")
mail_to_kim = AutoEmail("KIM")

print(mail_to_park.name)
# "PARK"

print(mail_to_kim.name)
# "KIM"
```

인스턴스를 만들 때 애초에 이름을 넣어주었기 때문에 각 인스턴스의 name에서 그 이름을 그대로 가지고 있게 된다.

그러면 이걸 활용해서 메서드를 작성할 수도 있다.

```python
class AutoEmail:
    intro = "안녕하세요"

    def __init__(self, name):
        self.name = name

    def say_hello(self):
        return "{intro} {name} 님".format(intro=self.intro, name=self.name)

mail_to_park = AutoEmail("PARK")
mail_to_kim = AutoEmail("KIM")

print(mail_to_park.say_hello())
# "안녕하세요 PARK 님"

print(mail_to_kim.say_hello())
# "안녕하세요 KIM 님"
```

클래스 변수와 인스턴스 변수를 모두 자유롭게 사용할 수 있게 되는 거다.

이게 바로 객체 지향 프로그램 작성의 장점일 거다. 클래스를 작성하여 필요한 데이터를 구조화하고, 해당 데이터와 의미있는 방식으로 상호 작용하는 메서드를 작성하는 것. 구조를 잘 짜는 게 중요하다.

## 파이썬에서는 모든 게 다 객체(Object)다

일단 클래스에 대해서는 어느정도 익혔으니 다시 개념적으로 살펴보기 위해 아래 구문을 실행해보자.

```python
print(dir([]))
```


비어있는 리스트에 대해 `dir()`이라는 파이썬 내장 함수를 적용한 거다.

`dir()`은 어떤 객체를 인자로 넣어주면 해당 객체가 어떤 메서드를 가지고 있는지 돌려준다.

그래서 파이썬 자료형 리스트를 인자로 넣어주면 내용이 출력될 거다.

```
['__add__', '__class__', '__contains__', '__delattr__', '__delitem__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__gt__', '__hash__', '__iadd__', '__imul__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__reversed__', '__rmul__', '__setattr__', '__setitem__', '__sizeof__', '__str__', '__subclasshook__', 'append', 'clear', 'copy', 'count', 'extend', 'index', 'insert', 'pop', 'remove', 'reverse', 'sort']
```

보다시피 리스트 자료형을 다룰 수 있는 append, pop, remone, sort 같은 익숙한 함수 이름이 등장한다.

자, 이제 본 포스팅 맨 처음에 type() 함수를 활용해서 자료형을 확인하면 `<class 'str'>`, `<class 'int'>`과 같은 식으로 출력된 걸 다시 기억해보자.

그렇다. 우리가 파이썬에서 다루는 모든 자료형은 모두 int, float, str, list, dict와 같은 이름을 가진 클래스의 인스턴스인 셈이다.