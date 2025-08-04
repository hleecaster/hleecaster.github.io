---
title: 자바스크립트 공부 - 반복문(Loop)
date: 2021-01-29
categories: [WORK, Web]
tags: [JavaScript]
---

프로그래밍에서 똑같은 코드 여러번 작성하지 않고 특정 조건에 도달할 때까지 같은 동작을 반복하도록 하는 방법을 익히는 건 사실상 필수다.

본 포스팅에서는 JavaScript에서 사용할 수 있는 여러가지 반복문을 익혀본다. 최대한 쉽게

## for 문

가장 기본이라 할 수 있는 for 문부터 알아보자.

일단 기본 구조는 이렇다.

```javascript
for ([initialExpression]; [conditionExpression]; [incrementExpression])
  statement
```

- `initialExpression` (초기문) : 반복문 시작하기 위한 초기 조건 설정
- `conditionExpression` (조건문) : 반목문을 끝내기 위한 종료 조건을 설정 (조건이 true이면 다음 반복, false이면 종료)
- `incrementExpression` (증감문) : 반복문을 한 번 실행할 때마다 반복문에 사용되는 변수를 업데이트

그래서 예시를 보면 아래와 같다.

```javascript
for (let i = 0; i < 5; i++) {
  console.log(i);
}
```

i라는 변수를 0으로 설정한 후, i가 5보다 작을 때까지는 계속 실행하는 건데, 이 때 반복문을 돌 때마다 i는 1씩 더해진다. 그래서 위 코드를 실행하면 0, 1, 2, 3, 4가 차례로 출력된다.

만약 4부터 0까지 거꾸로 출력하고 싶다면 이렇게 응용해볼 수 있겠다.

```javascript
for (let i = 4; i >= 0; i--) {
  console.log(i);
}
```

### 반복문에서 배열(array) 활용하기

배열에 있는 항목들을 하나씩 가져와서 무언가를 하고 싶을 때도 for문이 유용하다.

예를 들면 이렇게 쓸 수 있겠다.

```javascript
const colors = ['red', 'green', 'blue'];
for (let i = 0; i < colors .length; i++){
  console.log(animals[i]);
}
```

배열의 `.length`를 활용해서 전체 반복할 횟수를 구해놓은 뒤, i라는 인덱스를 1씩 증가시키면서 처리하는 방식.

위 코드를 실행하면 배열 안에 있는 항목들이 하나씩 순서대로 출력되는 걸 확인할 수 있다.

### 반복문 중첩해서 사용하기

반복문 안에 다른 반복문을 넣어서 사용할 수도 있다. 이를 Nested Loop 라고 부르는데 아무튼…

일단 예제를 보자. 숫자들이 담긴 두 개의 배열이 있는데, 그것들을 하나씩 꺼내서 비교하다가 똑같은 숫자가 있을 경우 그것만 출력하기.

```javascript
const array1 = [5, 10, 15];
const array2 = [10, 20, 30];
for (let i = 0; i < array1.length; i++) {
  for (let j = 0; j < array2.length; j++) {
    if (array1[i] === array2[j]) {
      console.log(array[j])
    }
  }
};
```

우선 상위 반복문에서 array1의 첫번째 항목을 꺼내고, 그 안에서 하위 반복문으로 array2의 각 항목들을 차례로 꺼내 비교해본다. 그 다음 다시 array1의 두번째 항목을 꺼내고, 그 안에서 하위 반복문으로 array2 각 항목들을 차례로 꺼내 비교… 이걸 반복하는 거다.

## while 문

`while (조건문)` 형식으로 반복문을 쓰면 이 조건이 참이기만 하면 무조건 반복한다.

1부터 4까지 차례로 출력하는 for문을 while문으로 고쳐쓰면 아래 예시와 같다.

```javascript
for (let i = 0; i < 5; i++) {
  console.log(i);
}

let j = 0;
while (j < 5) {
  console.log(j);
  j++;
}
```

## do…while 문

때로는 일단 코드를 실행한 다음 특정 조건에 따라 반복할지 그만둘지 결정하는 반복문을 만들어야 할 때도 있다. 이때 필요한 게 do…while 문이다. do…while 문은 일단 작업을 한 번 수행한 후 지정된 조건이 참인지 거짓인지 확인해서 참이면 반복, 거짓이면 중단한다.

0부터 4까지 차례로 출력하는 for문을 do…while문으로 고쳐 쓰면 아래와 같을 거다.

```javascript
for (let i = 0; i < 5; i++) {
  console.log(i);
}

let j = 0;
do {
  console.log(j);
  j++;
} while (j < 5);
```

while문과의 차이점이 있다면, do…while은 일단 코드를 실행하고 그 다음에 조건을 본다는 것.

```javascript
while (true === false){
  console.log('이건 출력 안 된다.')
};

do {
 console.log('이건 출력된다.')
} while (true === false);
```

## break로 반복문 중단하기

반복문 중간에 break를 사용해서 특정 조건이 참일 경우 반복문을 중단하고 빠져나올 수 있다.

예시를 보자.

```javascript
let i = 0;
while (true) {
  console.log(i);
  i++;
  if (i === 5 ) {
    console.log("THE END");
    break;
  }
}
```

일단 while문 조건으로 true를 써줬기 때문에 무한 반복하는 반복문이다. i라는 변수를 출력하고 1 더하는 작업을 반복.

그런데 만약 중간에 만약 i가 4라면 중단하는 조건을 추가해보았다. 그러면 0,1,2,3,4까지는 제대로 반복하는데, 5차례에서는 if 조건문을 타고 가서 break를 만나고 반복문을 탈출한다.