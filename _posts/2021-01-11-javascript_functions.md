---
title: 자바스크립트 공부 - 함수
date: 2021-01-11
categories: [WORK, 🌐 HTML & JavaScript]
tags: [JavaScript]
---

프로그래밍에서는 반복 작업을 하기 위해 함수를 만들어서 사용하곤 한다. 함수를 한 번 만들어놓으면 (필요하면 인수를 넣고) 그걸 실행해서 결과를 바로바로 내주기 때문에 언제든 사용할 수 있다.

자바스크립트에서 함수를 사용하는 법을 알아보자.

## 자바스크립트에서 함수 선언하고 실행하기

예제 보면 한 눈에 이해가 갈 거다.

```javascript
function sayHello() {
  console.log('Hello World!');
}

sayHello()
```

sayHello라는 함수를 만들어서 바로 실행하면 콘솔에 ‘Hello World!’가 찍히는 결과물을 볼 수 있다.

`function`이라는 키워드로 시작한 후, `함수이름()` 형식으로 써준다. 여기서 괄호 안에는 필요할 경우 인수를 받을 수 있도록 매개변수를 지정해놓을 수 있다. 이후 `{}`를 통해함수가 실행할 내용을 작성하면 끝.

## 매개변수(Parameter)와 인수(Argument)

흔히 매개변수와 인수를 혼동하여 사용하는 경우가 있는데, 개념적인 차이가 있다. 함수를 선언할 때 외부로부터 받고자 하는 임의의 값은 매개변수(Parameter), 실제로 함수를 호출할 때 사용되는 구체적인 값을 인수(Argument)라고 부른다.

함수를 만들 때 `function 함수명(매개변수1, 매개변수2, ...) {실행코드}`와 같은 형식으로 작성하면 된다. 그리고 함수를 호출할 때는 `함수명(인수)` 형식으로 호출하게 된다.

예제 보면 바로 이해가 될 거다.

```javascript
function sayThanks(name) {
  console.log(`Thanks, ${name}!`);
}

sayThanks('홍길동')
```

name이라는 매개변수를 받는 함수를 만들고 실행하니 Thanks, 홍길동!이라는 문자열이 출력되는 걸 확인할 수 있다.

만약 함수를 호출할 때 인수가 비어있다면 어떻게 처리할 수 있을까? 이건 매개변수 설정할 때 기본값을 지정함으로써 해결이 가능하다. 함수 선언할 때 `function 함수명(매개변수=기본값`) `{실행코드}` 형태로 넣어주면 된다.

```javascript
function sayThanks(name='아무개') {
  console.log(`Thanks, ${name}!`);
}

sayThanks('홍길동')
sayThanks()
```

함수 호출시에 인수를 넣어주지 않았더니 알아서 기본값인 ‘아무개’를 넣고 실행한다.

## 함수의 결과물 return

자바스크립트의 함수는 언제나 return 값을 가진다. 함수를 선언할 때 return을 명시를 하지 않으면 기본값은 `undefined`다.

예를 들어 너비와 높이를 곱해 사각형 넓이를 구하는 함수를 작성했는데 return 을 빠뜨렸다고 치자.

```javascript
function calculateArea(width, height) {
  let area = width * height;
}

console.log(calculateArea(3, 2))
```

위 코드를 실행하면 콘솔에 undefined라고 출력될 거다.

원래 의도는 함수의 결과물로 area를 가져오는 것이기 때문에 만약 제대로 작성하려면 `{}` 괄호 안에 return 값을 적어줘야 한다.

```javascript
function calculateArea(width, height) {
  let area = width * height;
  return area;
}

console.log(calculateArea(3, 2))
```

이러면 제대로 6이 출력된다.

사실 아래와 같이 return 값에 바로 수식을 적어도 된다.

```javascript
function calculateArea(width, height) {
  return width * height;
}
```

당연히 함수 안에서는 if 조건문을 활용할 수 있다.

```javascript
function calculateArea(width, height) {
  if (width < 0 || height < 0) {
    return '음수 입력 불가';
  }
  return width * height;
}
```

새로운 함수를 선언할 때 미리 정의된 다른 함수를 빌려와 사용하는 것도 가능하다.

## 함수 표현식 (Function Expression)

지금까지는 함수를 작성할 때 function 키워드를 가장 앞에 사용하는 함수 선언문 방식으로 해왔지만, 함수를 작성하는 또 다른 방식이 있는데, 바로 함수 표현식(Function Expression)이다.

아래 예제를 보자.

```javascript
const calculateArea = function(width, height) {
  if (width < 0 || height < 0) {
    return '음수 입력 불가';
  } else {
    return width * height;
  }
};
```

사용 방법은 의외로(어쩌면 더?) 심플하다. 이런 형식으로 적어주면 된다. `const 함수명= function(매개변수) { 실행코드; return 리턴값; };` 일종의 변수를 만드는 전략? 변수 이름이 함수의 이름 또는 식별자가 되도록 변수를 선언하는 방식이다. (ES6 출시 이후 변수를 선언하는 키워드로는 `const`를 사용하는 것이 일반적이라고 한다.)

물론 if… else… 조건문을 안에 넣어주는 것도 당연히 가능하다. `const 함수명= function(매개변수) { if (조건) {return 리턴값;} else {return 리턴값;} };` 과 같은 식으로 써주면 된다.

### ※ 함수선언문과 함수표현식의 차이

함수선언문은 코드상 어디에서 선언하든 일단 최상단으로 끌어올려진다. 이를 호이스팅(hoisting)이라고 하는데, 함수표현식은 호이스팅(hoisting) 되지 않으므로 정의되기 전에 호출할 수가 없다. 즉, 코드에서 상단에 표현식으로 먼저 함수를 만들고 나서야 아래에서 그 함수를 사용할 수 있다는 의미다. (호이스팅 개념은 좀 복잡하니 일단 본 포스팅에서는 넘어가자.)

## 화살표 함수 (Arrow Function)

화살표함수는 함수표현식을 더 간편하게 표현하기 위해 ES6에서 소개된 방법이다.

화살표 함수는 함수를 생성해야 할 때마다 `funtion` 키워드를 입력할 필요 없이 매개변수를 적어주고 이어서 `=>`를 사용하면 된다.

```javascript
// 함수 표현식
const calculateArea = function(width, height) {
  let area = width * height;
  return area;
};

// 화살표 함수
const rectangleArea = (width, height) => {
  let area = width * height;
  return area;
};
```

게다가 화살표 함수를 쓰면 코드를 더 간결하게 쓸 수 있는 추가적인 방법들이 존재한다.

### 화살표 함수를 더 간결하게… 방법 1

매개변수를 1개만 사용하는 함수는 괄호를 생략할 수 있다. 그러나 매개변수가 아예 없거나 0개 또는 여러 개의 매개 변수를 사용하는 경우 반드시 괄호가 필요하다.

- 매개변수 0개  
    `const 함수명 = ( ) => {실행코드};`
- 매개변수 1개  
    `const 함수명 = 매개변수 => {실행코드};`
- 매개변수 2개 이상  
    `const 함수명 = (매개변수1, 매개변수2) => {실행코드};`

이럴 거면 그냥 맘 편히 괄호 쓰게 강제 하는 게 낫지 않았을까.

### 화살표 함수를 더 간결하게… 방법 2

한 줄 블록으로 작성한 함수 본문에는 중괄호 `{ }`를 생략할 수 있다. 즉, 중괄호가 없으면 그 줄이 계산하는 값을 자동으로 return 값으로 반환한다. 블록 내용은 화살표 => 바로 뒤에 와야하며 return 키워드 생략할 수 있다.) 이를 암시적/암묵적 반환 (_implicit return_)이라고합니다.

```javascript
// 여러줄로 쓸 때는 괄호 {}를 사용해서 return을 명시하지만
const square  = (num) => {
  return num * num;
};

// 한줄로 쓸 때는 괄호 {} 없이 return 값을 바로 써줄 수 있다.
const square  = num => num * num;
```

뭐 이렇게 방법이 많은지… 실제로 코딩을 할 때는 이것저것 다양한 방법을 섞어쓰기보다는 일관된 방법으로 써주는 게 더 중요하긴 할 텐데, 아무튼 이런저런 방식으로 함수를 만들 수 있다는 걸 알아둘 필요는 있으니까 기억하고 넘어가자.
