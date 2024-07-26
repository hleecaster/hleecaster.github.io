---
title: 자바스크립트 공부 - 변수
date: 2021-01-01
categories: [WORK, HTML & JavaScript]
tags: [JavaScript]
---

프로그래밍에서 변수(variable)는 값을 담아놓는 곳을 의미한다. 컴퓨터 메모리에 있는 정보의 컨테이너라고 생각하면 된다. 어떤 값에 레이블링을 해놓은 뒤 그 값을 저장하거나 업데이트 할 수 있다.

본 포스팅에서는 자바스크립트에서 변수를 만드는 방법, 그리고 그 변수를 다루는 방법을 알아본다.

## 자바스크립트에서 변수 생성하는 방법

### 1. var

아래와 같이 `var` 키워드 뒤에서 `변수명 = 값`을 써주면 된다. 해당 변수를 콘솔에 출력하면 값이 나타나는 걸 확인할 수 있다.

```javascript
var myName = '홍길동';
console.log(myName);
```

여기서 변수명을 잘 보자. `myName`이라고 my를 소문자로 시작하다가 뒤에 나오는 Name의 첫글자는 대문자로 적었다. 각 단어의 첫문자를 대문자로 표기하고 붙여쓰되 맨처음 문자는 소문자로 표기하고 중간에 언더바(`_`)는 사용하지 않는다는 표기법을 따른 거다. 이건 자바스크립트에서 변수명이나 함수명을 작성하는 관행인데 카멜 케이스([Camel case)](https://en.wikipedia.org/wiki/Camel_case)라고 부른다.


낙타 모양과 유사하여 이름 붙여진 카멜 케이스
![카멜 케이스](https://upload.wikimedia.org/wikipedia/commons/thumb/c/c8/CamelCase_new.svg/330px-CamelCase_new.svg.png)

>참고로 카멜 케이스 이외에 각 단어 첫글자를 대문자로 사용하는 파스칼 케이스(Pascal case), 각 단어를 언더바(`_`)로 연결하는 스네이크 케이스(Snake case) 등의 방식도 있다.

그리고 자바스크립트에서 변수명을 지을 때는 아래와 같은 규칙을 기억해야 한다.

- 변수명은 숫자로 시작할 수 없다.
- 변수명은 대문자와 소문자를 구분한다. (예를 들어 `myName`과 `myname`은 다른 변수다.)
- 변수명은 키워드와 같을 수 없다. (자바스크립트에서 사용하는 키워드 목록은 이 [문서](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Lexical_grammar)를 참고하자.)

아무튼 `var`로 변수를 생성하면 뭐 이런저런 이유로 단점이 좀 있다. 그래서 2015년에 JavaScript ES6 버전을 통해 이런저런 업데이트가 있었는데, 그 중 하나가 바로 변수를 선언하기 위한 새로운 키워드 `let`이나 `const`이다. (ES6 이전에는 변수를 선언할 때 `var`만 사용할 수 있었다.)

### 2. let

`let`도 `var`과 마찬가지로 사용법은 동일하다. 차이가 있다면

`let`으로는 변수를 선언한 후, 나중에 다른 값으로 할당(변경)하는 것도 가능하다. 아래 예시를 보자.

```javascript
let hero = '슈퍼맨';
console.log(hero); // Output: 슈퍼맨
hero = '배트맨';
console.log(hero); // Output: 배트맨
```

그리고 또 하나 알아야 할 게 있다면, `let`으로 변수명만 선언 하고 값은 나중에 할당하는 것도 가능하다는 것. 값을 할당하지 않으면 자동으로 `undefined` 값이 들어간다.

```javascript
let price;
console.log(price); // Output: undefined
price = 20000;
console.log(price); // Output: 20000
```

### 3. const

`const`는 상수(constant)의 약자다. 변수를 선언하고 값을 할당하는 방법은 `var`, `let`과 동일하다.

```javascript
const hero = '슈퍼맨';
console.log(hero); // Output: 슈퍼맨
```

그러나 중요한 차이점이 있다. `const`는 상수이기 때문에 한 번 선언한 후에는 값을 다시 할당할 수가 없다. const 변수를 다른 값으로 다시 할당하려고 하면 TypeError가 발생한다.

또한, `const`는 변수명을 선언할 때 값도 함께 할당해야 한다. 만약 (`let`으로 가능한 것처럼) 값 없이 변수를 선언하려고 하면 SyntaxError가 발생한다.

### ※ 결론

변수를 선언할 때는 나중에 그 변수에 값을 다시 할당할 일이 있는지 고려해야 한다. 변수에 값을 다시 할당해야 할 경우 `let`을 사용하고, 고정된 값을 할당하려면 `const`를 사용하자.

## 자바스크립트에서 변수 다루기

### 1. 숫자 계산하기

할당된 변수들은 계산을 하고, 값을 업데이트 할 수 있다.

예를 들면 이렇게.

```javascript
let a = 3;
a = a + 1;
 
console.log(a); // Output: 4
```

a 변수를 3이라는 숫자로 선언한 후, a에 1을 더해 값을 업데이트 했다. 그래서 최종 출력되는 결과는 4.

이렇게 값 업데이트 하는 연산은 좀 더 간편하게 할 수 있다. a = a + 1 대신 a += 1이라고 쓰는 것도 가능하다. (파이썬에도 이런 식으로 쓸 수 있다.) 더하기 이외의 다른 연산도 마찬가지로 아래와 같이 모두 가능하다.

```javascript
let x = 15;
x -= 5;
console.log(x); // Output: 10
 
let y = 20;
y *= 2;
console.log(y); // Output: 40
 
let z = 8;
z /= 4;
console.log(z); // Output: 2
```

자바스크립트에서는 증감 연산자(`++` 또는 `--`)를 사용하여 1씩 더하거나 빼주는 것도 가능하다. 아래 예시를 보자.

```javascript
let a = 5;
a++;
console.log(a); // Output: 6

let b = 10;
b--;
console.log(b); // Output: 9
```

증감 연산자를 사용하면 1을 더하거나 뺀 값으로 값이 업데이트 된다. 편리함.

### 2. 문자열 연결하기

여러개의 문자열을 연결 할 때 + 연산자를 사용하는 것도 가능하다.

```javascript
let myHero = '슈퍼맨';
console.log('Hello ' + myHero + '!'); 
// Output: 'Hello 슈퍼맨!'
```

그런데 이렇게 +로 문자열을 덕지덕지 연결하면 코드가 복잡해질 수 있다.

그래서 자바스크립트 ES6 버전부터는 템플릿 리터럴(template literal)이라는 걸 사용할 수 있게 되었다. 예시를 보자.

```javascript
const myHero = '슈퍼맨';
console.log(`I love ${myHero}!`);
// Output: I love 슈퍼맨!
```

문자열을 쓸 때 따옴표(`'`)가 아닌, 키보드 숫자 1 왼쪽에 있는 백틱(`` ` ``)을 사용하면 문자열 템플릿을 생성할 수 있고, 그 문자열 안에 `${변수명}` 형태로 변수를 그대로 가져올 수가 있게 된다. (파이썬에서 문자열 포매팅할 때 사용하는 f-string 방식과 똑같다.) 이렇게 하면 코드 가독성이 훨씬 높아진다.

## 변수 타입 (자료형) 확인하기

변수의 자료형을 확인하고 싶다면 변수명 앞에 `typeof` 를 적어주면 된다. 띄어쓰기 필수.

예시를 보자.

```javascript
const a = 'hello';
console.log(typeof a); // Output: string
 
const b= 10;
console.log(typeof b); // Output: number
 
const c= true; 
console.log(typeof c); // Output: boolean
```