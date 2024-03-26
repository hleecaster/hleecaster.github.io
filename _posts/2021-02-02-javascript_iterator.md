---
title: 자바스크립트 공부 - 반복자(Iterator)
date: 2021-02-02
categories: [WORK, HTML & JavaScript]
tags: [JavaScript]
---

자바스크립트 반복문(Loop)에 대해 다룬 바 있다. for 문, while 문 같은 걸 활용해서 특정 조건에 도달할 때까지 같은 동작을 반복하도록 하는 문법을 익힌 거다.

그런데 이렇게 하지 않고도 반복자(Iterator, 그냥 ‘이터레이터’라 읽음)를 사용하는 방법도 있다. 자바스크립트에서 애초에 반복할 수 있는 개체, 즉 배열과 같은 것들을 굳이 반복문을 작성하지 않고도 쉽게 다룰 수 있도록 내장된 메서드를 제공하기 때문이다.

본 포스팅에서는 가장 널리 쓰이는, 유용한 이터레이터 4개만 익혀보자.

## .forEach()

배열 안에 있는 걸 하나씩 꺼내서 동일한 코드를 실행하도록 해주는 `.forEach()` 메서드.

`배열이름.forEach(배열항목 => {배열항목을 포함한 실행코드});` 와 같은 식으로 화살표 함수를 활용해 편하게 작성할 수 있다.

예제를 보면 바로 이해가 감.

```javascript
const heroes = ['슈퍼맨', '배트맨', '아이언맨', '엑스맨'];

// 방법 1
artists.forEach(hero => {
  console.log(`제 영웅은 ${hero}입니다.`);
});

// 방법 2
function printHeroes(hero){
  console.log(`제 영웅은 ${hero}입니다.`);
}
 
heroes.forEach(printHeroes);
```

콜백 함수 개념을 활용한 건데, 처음부터 너무 깊게 들어가지 말자. 일단 함수를 만들어서 각 아이템에 적용한다는 걸로 이해하면 편하다.

방법 1에 익숙해지자.

## .map()

`.map()`메서드는 콜백 함수의 인수를 받아 새로운 배열을 반환해준다.(?) 설명이 어렵다면 그냥 바로 예제를 보자.

예를 들어 어떤 배열의 각 항목에 2를 곱한 새로운 배열을 얻고 싶다면 이렇게 하면 된다.

```javascript
const numbers = [1, 2, 3, 4, 5]; 
 
const doubleNumbers = numbers.map(number => {
  return number * 2;
});

console.log(doubleNumbers); // [10, 20, 30, 40, 50]
```

이번엔 문자열을 예시로 살펴보자. 예를 들어 배열의 모든 항목에서 첫 글자만 모으고 싶을 때.

```javascript
const words= ['apple', 'banana', 'carrot'];

const firstLetters = words.map(word=> {
  return word[0];
});

console.log(firstLetters ); // ['a', 'b', 'c']
```

## .filter()

`.filter()`도 `.map()`과 마찬가지로 새 배열을 반환한다. 그러나 `.filter()`는 이름처럼 원래 배열에서 특정 항목을 필터링 한 후 남은 것들만 반환한다. 즉, 전달된 항목에 따라 true 또는 false를 판별한다는 뜻이고, 콜백 함수가 true를 반환하는 항목만 새 배열에 들어올 수 있게 된다.

예시를 보자. 예를 들어 문자열이 있는 배열에서 글자수 6자 미만의 항목만 남기고 싶다면 이렇게.

```javascript
const words= ['apple', 'banana', 'carrot', 'dog'];
 
const shortWords = words.filter(word => {
  return word.length < 6;
});

console.log(shortWords); // ['apple', 'dog'];
```

return 값으로 true나 false로 판별될 조건을 적어주면 된다.

## .findIndex()

`.findIndex()`는 이름 그대로 배열에서 위치를 찾아주는 메서드. 이것도 true나 false로 판별될 조건을 적어주는 방식으로 처리하게 되는데, true로 판별되는 가장 첫번째 값의 위치를 반환한다.

예제를 보자. 예를 들어 숫자 10보다 작은 값의 위치(인덱스)를 돌려주는 예시.

```javascript
const numbers = [213, 52, 348, 7, 99]; 
 
const lessThanTen = numbers.findIndex(num => {
  return num < 10;
});

console.log(lessThanTen); // 3 
```

이외에도 자바스크립트에서 배열을 다룰 수 있는 수많은 반복자 메서드(이터레이터)들이 존재한다. 더 자세한 내용은 [MDN 공식 문서 – Array](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array)를 살펴보자.