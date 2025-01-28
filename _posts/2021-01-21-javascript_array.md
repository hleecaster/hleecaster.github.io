---
title: 자바스크립트 공부 - 배열(Array)
date: 2021-01-21
categories: [WORK, 🌐 HTML & JavaScript]
tags: [JavaScript]
---

프로그래밍에서 데이터 다루는 법은 정말 기본 중의 기본. 본 포스팅에서는 자바스크립트의 가장 기본적인 데이터 구조인 배열(Array) 다루는 법에 대해 최대한 쉽게 정리해보았다.

## 배열(Array) 만들기

흔히 ‘어레이’라고도 부르는 배열(Array)이란 일종의 자료 목록이다. 배열(array) 안에 담긴 자료들은 요소(element) 혹은 항목(item)이라 부른다. 파이썬의 리스트와 똑같다고 생각하면 깔끔하다.

배열을 만드는 가장 간편한 방법은 대괄호 `[]`로 자료들을 묶어주는 거다. 이렇게.

```javascript
let colorPalette = ['red', 'green', 'blue'];
```

배열 안에는 문자열, 숫자, true 또는 false, 심지어 배열 안에 또 다른 배열(nested array) 등 어떤 자료형이든 저장할 수가 있다. 그리고 하나의 배열에 여러 종류의 자료형이 동시에 담길 수 있다. 항목의 중복도 가능하다. 그리고 배열의 또 한가지 중요한 특징은 순서가 지정된다는 것. 배열 안의 각 항목에는 번호가 매겨진 위치가 지정된 셈이다.

배열을 선언할 때 `let` 키워드와 `const` 키워드 둘 다 사용할 수 있다. 일반적으로 const 키워드로 선언된 변수는 재할당을 할 수 없다는 차이가 있다. `const`로 배열을 선언하면 그 변수 안에 새 배열이나 다른 값을 다시 할당할 수는 없더라도 그 안의 항목들은 변경, 업데이트가 가능하다. (뒤에서 다시 다뤄보자.)

## 배열 안의 요소/항목 다루기

### 항목 인덱싱

어떤 배열이 있을 때 위치를 인덱싱해서 그 안에 있는 특정 항목에 접근할 수 있다. 파이썬 인덱싱과 똑같다.

`배열이름[인덱스]` 방식으로 접근한다. 인덱스는 0부터 시작.

```javascript
let colorPalette = ['red', 'green', 'blue'];
console.log(colorPalette[1]);  // green
console.log(colorPalette[3]);  // undefined
```

인덱싱이 배열의 범위를 벗어나면 `undefined`을 돌려준다.

참고로 (배열은 아니지만) 문자열 인덱싱 하는 것도 파이썬이랑 똑같다. 이렇게.

```javascript
const hello = 'Hello World';
console.log(hello[6]);  // W
```

### 항목 업데이트

인덱싱을 통해 배열의 특정 위치에 접근하면 그 값을 업데이트 할 수도 있다.

```javascript
let colorPalette = ['red', 'green', 'blue'];
colorPalette[1] = 'yellow';
console.log(colorPalette);  // ['red', 'yellow', 'blue']
```

### 배열의 길이 확인 `.length`

배열은 `.length`라는 내장된 속성을 사용해서 그 배열에 들어 있는 항목의 개수를 가져올 수 있다. (문자열 길이 가져올 때 사용하는 것과 똑같다.)

```javascript
let colorPalette = ['red', 'green', 'blue'];
console.log(colorPalette.length);  // 3
```

### 배열에 항목 추가 `.push`

기존에 있던 배열에 항목을 추가하려면 `.push`라는 속성을 사용하면 된다. (파이썬 append와 똑같다.)

```javascript
let colorPalette = ['red', 'green', 'blue'];
colorPalette.push('yellow', 'purple');
console.log(colorPalette);  // ['red', 'green', 'blue', 'yellow', 'purple']
```

### 배열 마지막 항목 제거 `.pop`

배열 맨 마지막 항목을 제거하려면 `.pop`이라는 속성을 사용하면 된다. 파이썬 pop과 똑같다. `배열이름.pop()`과 같은 식으로 써주면 되고 인자는 필요없다.

특이한 건 이렇게 `.pop()`으로 제거한 항목을 새로운 변수에 담을 수도 있다는 것.

```javascript
let colorPalette = ['red', 'green', 'blue'];
const removed = colorPalette.pop();
console.log(colorPalette);  // ['red', 'green']
console.log(removed); // 'blue'
```

### 이외에도 배열을 다루는 다양한 방법들

`.join()`, `.slice()`, `.splice()`, `.shift()`, `.unshift()`, `.concat()` 등 다양한 방법이 있다.

자세한 건 [Mozilla Developer Network (MDN)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array) 문서를 참고하자.

## 함수로 배열 다루기

함수를 통해 배열 다루는 것도 당연히 가능하다. 예시.

```javascript
let colorPalette = ['red', 'green', 'blue'];

function addHello(arr) {
  arr.push('HELLO!');
}

addHello(colorPalette);
console.log(colorPalette); // ['red', 'green', 'blue', 'HELLO!']
```

배열을 함수에 전달하면 함수 내에서 배열이 변경되고, 이 변경사항은 함수 외부에서도 계속 유지되는 걸 알 수 있다.