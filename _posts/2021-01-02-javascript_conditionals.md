---
title: 자바스크립트 공부 - 조건문
date: 2021-01-02
categories: [WORK, HTML & JavaScript]
tags: [JavaScript]
---

프로그래밍 언어를 배울 때 가장 기초가 되는 건 자료형, 조건문, 반복문이 아닐까.

본 포스팅에서는 자바스크립트에서 조건문을 다루는 방법을 알아본다.

## if…

예제를 보면 한 방에 이해가 된다.

```javascript
if (true) {
  console.log('괄호 () 안의 조건이 참이면 이 문장을 출력한다.'); 
}
// true이기 때문에 해당 문장이 출력된다.
```

if 키워드 뒤에 조건문 괄호`()`가 오고, 이어서 실행할 코드를 중괄호 `{ }`로 표현한다. 괄호 `()` 안의 조건이 참이면 중괄호 `{ }` 안에 있는 코드를 실행하고, 거짓이면 실행하지 않는다.

## if… else…

이것도 예제를 보면 바로 이해가 될 거다.

```javascript
if (false) {
  console.log('괄호 () 안의 조건이 참이면 이 문장을 출력한다.');
} else {
  console.log('그렇지 않으면, 즉 거짓이면 이 문장을 출력한다.');
}
// false이기 때문에 else 문의 문장이 출력된다.
```

`if (조건) {참일 때}` 구문 뒤에 `else {거짓일 때}` 구문을 추가로 적어주면 끝이다.

## if… else if… else…

조건문 여러개 중첩해서 사용하는 것도 가능하다. (파이썬 elif와 같은 거)

```javascript
let stopLight = '노란불';
 
if (stopLight === '빨간불') {
  console.log('멈춤!');
} else if (stopLight === '노란불') {
  console.log('천천히');
} else if (stopLight === '초록불') {
  console.log('통과');
} else {
  console.log('모르겠음...');
}
```

쉽다.

## 비교 연산자 (Comparison Operator)

if 문 안에 들어가는 조건문을 작성할 때는 부등호(`<`,  `>`, `<=`, `>=`) 뿐만 아니라 `===`, `!==`, `==`, `!=` 등의 비교 연산자를 사용할 수 있다.

여기서 등호를 포함한 연산자는 3개 짜리와 2개 짜리가 있는데 살짝 다르다. 3개 짜리는 자료형까지 정확히 일치해야만 하고(엄격 비교), 2개짜리는 자료형이 다르더라도 그 값을 같은 값으로 볼 수 있으면 같다고 처리한다(추상비교).

예시를 보자.

```javascript
1 ==  1           // true
"1" ==  1         // true
1 == '1'          // true
0 == false        // true
0 == null         // false

0 == undefined    // false
null == undefined // true

1 != 2            // true
1 != "1"          // false
1 != '1'          // false
1 != true         // false
0 != false        // false

3 === 3           // true
3 === '3'         // false

3 !== '3'         // true
4 !== 3           // true
```

머리 아프면 일단 `===`, `!==`부터 익혀 사용해도 괜찮다.

## 논리 연산자 (Logical Operator)

논리 연산자는 and(`&&`), or(`||`)로 여러개 조건문을 연결해서 판단하는 거.

예시를 보면 바로 이해가 된다. 이를 테면 or를 활용해서 만약 토요일이거나 일요일이면 주말이라고 판단하기.

```javascript
if (day === 'Saturday' || day === 'Sunday') {
  console.log('주말이다!');
} else {
  console.log('일하자......');
}
```

그리고 not도 있다. `!` 기호를 사용하면 된다. (true는 false로, false는 true로 바꿔준다.) 아래와 같이 사용한다는 걸 주목하자.

```javascript
let myCondition1= true;
console.log(!myCondition1); // Prints false
 
let myCondition2= false;
console.log(!myCondition2); // Prints true
```

## 변수의 참과 거짓(Truthy and Falsy)

if 뒤의 조건문에는 명제를 적어 참, 거짓을 판단하여 않더라도 변수 자체만의 참과 거짓을 판단하는 기준이 있다.

일단 변수가 제대로(?) 존재하기만 하면 참으로 본다.

아래와 같이 문자열이 있는지 확인하는 조건문 예제를 실행하면 해당 문자열이 출력된다.

```javascript
let myVariable = '문자열';
 
if (myVariable) {
   console.log(myVariable)
} else {
   console.log('변수가 존재하지 않는다.')
}
```

그러나 변수를 거짓으로 판단하는 경우가 있다.

- `0`
- `''` 또는 `""`(빈 문자열)
- `null` (값이 완전히 없음)
- `undefined` (값이 정의되지 않음)
- `NaN` (숫자가 아님)

그래서 아래와 같은 코드를 실행하면 ‘거짓’이 출력된다.

```javascript
let myVariable = 0;
 
if (myVariable ){
   console.log('참');
} else {
   console.log('거짓');
}
```

그래서 이렇게 변수에 값이 제대로 할당되어 있는지 확인하는 조건문은 여러모로 유용하다. 아래 예제를 보자.

```javascript
let defaultName;
if (username) {
  defaultName = username;
} else {
  defaultName = '방문자';
}
```

미리 선언된 username이 없다면 ‘방문자’라는 이름으로 할당하도록 조건을 걸어주는 거다.

그런데 이 조건문을 논리 연산자를 통해 이렇게 한 줄로 쓸 수 있다.

```javascript
let defaultName = username || '방문자';
```

이렇게 표현하는 걸 단축 평가 (short-circuit evaluation)라 부른다.

## 삼항 연산자 (Ternary Operator)

조건문을 간결하게 작성하기 위한 삼항 연산자에 대해서도 알아보자.

예를 들어 아래와 같은 코드가 있다면

```javascript
let isRed = true;
 
if (isRed) {
  console.log('빨갛다!');
} else {
  console.log('빨갛지 않다!');
}
```

아래와 같이 삼항 연산자(`?`, `:`)를 사용해서 한 줄로 간결하게 표현할 수 있다.

```javascript
isRed ? console.log('빨갛다!') : console.log('빨갛지 않다!');
```

`조건 ? true일 때 : false일 때` 이런 식으로 사용하면 된다.

## switch 문

if… else if… 를 중첩해서 조건문을 사용하면 일일이 구문을 다 적어줘야해서 귀찮고 코드 가독성도 떨어진다. 이 때 유용한 게 스위치 문이다.

예를 들어 아래와 같은 if 문이 있다고 하면

```javascript
let rank = '2위';
 
if (rank === '1위') {
  console.log('금메달');
} else if (rank === '2위') {
  console.log('은메달');
} else if (rank === '3위') {
  console.log('동메달');
} else {
  console.log('메달 획득 실패');
}
```

switch 문을 통해 아래와 같이 쓸 수 있다.

```javascript
let rank = '2위';
 
switch (rank) {
  case '1위':
    console.log('금메달');
    break;
  case '2위':
    console.log('은메달');
    break;
  case '3위':
    console.log('동메달');
    break;
  default:
    console.log('메달 획득 실패');
    break;
}
```

`switch (변수) { }` 이렇게 적고, { } 괄호 안에는 `case 비교대상 :` 이라고 표현하여 대상이 일치할 때 실행할 코드를 적으면 된다.

이 때 각 코드 뒤에 `break;`라고 명시해야 한다. 이게 없으면 일치하는 대상을 찾은 후로 모든 case에 해당하는 코드도 다 실행하기 때문이다.

맨 끝에 `default:` 를 적었는데, 이건 비교대상을 찾지 못했을 때 실행할 코드다.