---
title: D3.js 시작하기 (기초)
date: 2025-01-09
categories: [WORK,  Web]
tags: [데이터시각화, JavaScript, D3.js]
---


## D3.js란?

[D3.js](https://github.com/d3/d3)(Data-Driven Documents)는 웹 브라우저에서 데이터를 시각화하기 위한 자바스크립트 라이브러리다. HTML, SVG, CSS를 사용해 데이터를 시각적으로 표현하며, 강력한 시각화 컴포넌트들을 제공한다.

주요 특징:
- 웹 표준 기술 사용 (HTML, SVG, CSS)
- 유연한 데이터 바인딩
- 다양한 상호작용 구현 가능
- 애니메이션 지원

## D3.js 기본 문법

### 1. 선택과 수정 (Selection)

D3.js는 CSS 선택자를 사용하여 DOM 요소를 선택하고 수정한다.

```javascript
// 요소 선택하기
d3.select('#chart')      // id로 선택
d3.selectAll('.bar')     // class로 선택

// 속성 설정
d3.select('#chart')
    .attr('width', 500)   // 속성 설정
    .style('color', 'red')// 스타일 설정

// 콘텐츠 수정
d3.select('text')
    .text('Hello D3!')    // 텍스트 내용 변경
```

### 2. 메서드 체이닝

D3.js는 메서드 체이닝 패턴을 사용한다. 각 메서드는 선택된 요소를 반환하므로 연속적으로 메서드를 호출할 수 있다.

```javascript
d3.select('#chart')
    .append('svg')        // SVG 요소 추가
    .attr('width', 500)   // 너비 설정
    .attr('height', 300)  // 높이 설정
    .style('background', '#f0f0f0'); // 배경색 설정
```

## 개발 환경 설정

### 1. HTML 파일 생성

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>D3.js 시작하기</title>
    <!-- D3.js 라이브러리 추가 -->
    <script src="https://d3js.org/d3.v7.min.js"></script>
</head>
<body>
    <!-- 시각화를 위한 컨테이너 -->
    <div id="chart"></div>
    <script src="app.js"></script>
</body>
</html>
```

### 2. JavaScript 파일 생성 (app.js)

```javascript
// D3.js 버전 확인
console.log('D3 Version:', d3.version);

// 기본 SVG 컨테이너 생성
const svg = d3.select('#chart')
    .append('svg')
    .attr('width', 600)
    .attr('height', 400);
```

## SVG 기초

### SVG 요소의 기본 속성

```javascript
// rect (사각형) 요소의 주요 속성
svg.append('rect')
    .attr('x', 50)        // x 좌표
    .attr('y', 50)        // y 좌표
    .attr('width', 100)   // 너비
    .attr('height', 100)  // 높이
    .style('fill', 'steelblue')    // 채우기 색상
    .style('stroke', 'black')      // 테두리 색상
    .style('stroke-width', 2);     // 테두리 두께

// circle (원) 요소의 주요 속성
svg.append('circle')
    .attr('cx', 250)      // 중심 x 좌표
    .attr('cy', 100)      // 중심 y 좌표
    .attr('r', 50)        // 반지름
    .style('fill', 'coral');

// line (선) 요소의 주요 속성
svg.append('line')
    .attr('x1', 0)        // 시작점 x 좌표
    .attr('y1', 0)        // 시작점 y 좌표
    .attr('x2', 100)      // 끝점 x 좌표
    .attr('y2', 100)      // 끝점 y 좌표
    .style('stroke', 'black');     // 선 색상
```

## 데이터 바인딩

D3.js의 데이터 바인딩은 데이터 배열의 각 항목을 DOM 요소와 연결하는 과정이다.

### 1. 기본 데이터 바인딩

```javascript
const data = [10, 20, 30, 40, 50];

// 데이터 바인딩의 기본 패턴
svg.selectAll('circle')   // 아직 존재하지 않는 circle 요소들을 선택
    .data(data)          // 데이터 배열을 바인딩
    .join('circle')      // circle 요소 생성 및 업데이트
    .attr('cx', (d, i) => i * 100 + 50)  // d: 데이터값, i: 인덱스
    .attr('cy', 200)
    .attr('r', d => d)   // 데이터값을 반지름으로 사용
```

### 2. enter, update, exit 패턴

```javascript
// 상세한 데이터 바인딩 패턴
const circles = svg.selectAll('circle')
    .data(data);

// enter: 새로운 데이터에 대한 처리
const enterCircles = circles.enter()
    .append('circle')
    .attr('cx', (d, i) => i * 100 + 50)
    .attr('cy', 200)
    .attr('r', d => d);

// update: 기존 데이터 업데이트
circles
    .attr('r', d => d)
    .style('fill', 'blue');

// exit: 제거될 요소 처리
circles.exit()
    .style('fill', 'red')
    .transition()
    .duration(1000)
    .remove();
```

## 실습: 데이터가 있는 막대 그래프 만들기

```javascript
// 데이터 준비
const data = [
    {name: 'A', value: 10},
    {name: 'B', value: 20},
    {name: 'C', value: 15},
    {name: 'D', value: 25}
];

// SVG 컨테이너 생성
const width = 600;
const height = 400;
const margin = {top: 20, right: 20, bottom: 30, left: 40};

const svg = d3.select('#chart')
    .append('svg')
    .attr('width', width)
    .attr('height', height);

// 막대 그래프 그리기
const bars = svg.selectAll('rect')
    .data(data)
    .join('rect')
    // 각 속성에 대한 설명
    .attr('x', (d, i) => i * 100 + margin.left)  // 막대 x위치
    .attr('y', d => height - margin.bottom - d.value * 10)  // 막대 y위치
    .attr('width', 80)  // 막대 너비
    .attr('height', d => d.value * 10)  // 막대 높이
    .style('fill', 'steelblue');  // 막대 색상

// 레이블 추가
svg.selectAll('text')
    .data(data)
    .join('text')
    .text(d => d.name)
    .attr('x', (d, i) => i * 100 + margin.left + 40)
    .attr('y', height - margin.bottom + 20)
    .style('text-anchor', 'middle');
```

