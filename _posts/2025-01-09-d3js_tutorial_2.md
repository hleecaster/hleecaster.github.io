---
title: D3.js로 차트 그리기
date: 2025-01-09
categories: [WORK,  Web]
tags: [데이터시각화, JavaScript, D3.js]
---


## 데이터 로딩하기

### 1. CSV 데이터 로딩

```javascript
// CSV 파일 구조
// date,value
// 2024-01-01,10
// 2024-01-02,15
// 2024-01-03,12

d3.csv('data.csv').then(data => {
    // 데이터 파싱
    const parsedData = data.map(d => ({
        date: new Date(d.date),
        value: +d.value  // 문자열을 숫자로 변환
    }));
    createChart(parsedData);
}).catch(error => {
    console.error('Error loading data:', error);
});
```

### 2. JSON 데이터 로딩

```javascript
// JSON 파일 구조
// [
//   {"date": "2024-01-01", "value": 10},
//   {"date": "2024-01-02", "value": 15}
// ]

d3.json('data.json').then(data => {
    const parsedData = data.map(d => ({
        date: new Date(d.date),
        value: d.value
    }));
    createChart(parsedData);
});
```

## 스케일 함수 이해하기

스케일 함수는 데이터 값을 화면 픽셀 값으로 변환한다. D3.js는 다양한 유형의 스케일을 제공한다.

### 1. 선형 스케일 (Linear Scale)

연속적인 수치 데이터를 위한 가장 기본적인 스케일이다.

```javascript
// 기본 사용법
const yScale = d3.scaleLinear()
    .domain([0, 100])         // 데이터 범위
    .range([height, 0]);      // 픽셀 범위

// 고급 사용법
const yScale = d3.scaleLinear()
    .domain([0, d3.max(data, d => d.value)])  // 데이터의 최대값 사용
    .range([height - margin.bottom, margin.top])
    .nice();  // 도메인을 적절한 값으로 조정

// 사용 예시
const y = yScale(15);  // 데이터 값 15를 픽셀 값으로 변환
```

### 2. 밴드 스케일 (Band Scale)

범주형 데이터를 일정한 너비의 밴드로 매핑할 때 사용한다. 주로 막대 차트에서 활용된다.

```javascript
// 기본 사용법
const xScale = d3.scaleBand()
    .domain(['A', 'B', 'C', 'D'])  // 카테고리 배열
    .range([0, width])             // 픽셀 범위
    .padding(0.1);                 // 밴드 간 여백 (0~1)

// 실제 데이터 활용
const xScale = d3.scaleBand()
    .domain(data.map(d => d.category))
    .range([margin.left, width - margin.right])
    .paddingInner(0.1)    // 밴드 내부 여백
    .paddingOuter(0.2);   // 밴드 외부 여백

// 사용 예시
const x = xScale('A');           // 카테고리 'A'의 시작 위치
const bandWidth = xScale.bandwidth();  // 밴드의 너비
```

### 3. 포인트 스케일 (Point Scale)

범주형 데이터를 동일한 간격의 점으로 매핑할 때 사용한다. 산점도나 라인 차트의 점 위치에 주로 사용된다.

```javascript
// 기본 사용법
const xScale = d3.scalePoint()
    .domain(['A', 'B', 'C', 'D'])  // 카테고리 배열
    .range([0, width])             // 픽셀 범위
    .padding(0.5);                 // 양끝 여백

// 실제 데이터 활용
const xScale = d3.scalePoint()
    .domain(data.map(d => d.category))
    .range([margin.left, width - margin.right]);

// Band Scale과의 차이점
// 1. bandwidth() 메서드가 없음
// 2. 각 카테고리가 하나의 점으로 매핑됨
// 3. padding은 전체 범위의 비율로 적용

// 사용 예시
const x = xScale('A');  // 카테고리 'A'의 점 위치
const step = xScale.step();  // 점 사이의 간격
```

### 4. 시간 스케일 (Time Scale)

시간 데이터를 다룰 때 사용하는 특별한 형태의 선형 스케일이다.

```javascript
// 기본 사용법
const xScale = d3.scaleTime()
    .domain([new Date('2024-01-01'), new Date('2024-12-31')])
    .range([0, width]);

// 실제 데이터 활용
const xScale = d3.scaleTime()
    .domain(d3.extent(data, d => d.date))  // 데이터의 시작일과 종료일
    .range([margin.left, width - margin.right])
    .nice(d3.timeMonth);  // 월 단위로 도메인 조정

// 날짜 포맷팅과 함께 사용
const xAxis = d3.axisBottom(xScale)
    .ticks(d3.timeMonth.every(1))  // 매월 눈금 표시
    .tickFormat(d3.timeFormat('%Y-%m'));  // YYYY-MM 형식
```

### 스케일 활용 예제: 복합 차트

```javascript
// 여러 스케일을 조합한 차트 예제
const chart = {
    // 카테고리형 X축 (Point Scale)
    xScale: d3.scalePoint()
        .domain(data.map(d => d.category))
        .range([margin.left, width - margin.right])
        .padding(0.5),
    
    // 수치형 Y축 (Linear Scale)
    yScale: d3.scaleLinear()
        .domain([0, d3.max(data, d => d.value)])
        .range([height - margin.bottom, margin.top])
        .nice(),
    
    // 시간 기반 부차적 X축 (Time Scale)
    timeScale: d3.scaleTime()
        .domain(d3.extent(data, d => d.date))
        .range([margin.left, width - margin.right])
};

// 산점도 그리기
svg.selectAll('circle')
    .data(data)
    .join('circle')
    .attr('cx', d => chart.xScale(d.category))
    .attr('cy', d => chart.yScale(d.value))
    .attr('r', 5);

// 연결선 그리기 (Line Generator 사용)
const line = d3.line()
    .x(d => chart.xScale(d.category))
    .y(d => chart.yScale(d.value));

svg.append('path')
    .datum(data)
    .attr('d', line)
    .attr('fill', 'none')
    .attr('stroke', 'steelblue');
```


## 축 만들기

```javascript
// X축 생성
const xAxis = d3.axisBottom(xScale)
    .ticks(5)  // 눈금 개수 지정
    .tickFormat(d3.timeFormat('%Y-%m-%d'));  // 날짜 형식 지정

// X축 그리기
svg.append('g')
    .attr('class', 'x-axis')
    .attr('transform', `translate(0,${height - margin.bottom})`)
    .call(xAxis);

// Y축 생성
const yAxis = d3.axisLeft(yScale)
    .tickSize(-width + margin.left + margin.right)  // 그리드 라인
    .ticks(5);

// Y축 그리기
svg.append('g')
    .attr('class', 'y-axis')
    .attr('transform', `translate(${margin.left},0)`)
    .call(yAxis);

// 축 스타일링
svg.selectAll('.y-axis line')
    .style('stroke', '#ddd');
```

## 실전 예제: 라인 차트 만들기

```javascript
// 데이터 예시
const data = [
    { date: '2024-01-01', value: 10 },
    { date: '2024-01-02', value: 15 },
    { date: '2024-01-03', value: 12 },
    { date: '2024-01-04', value: 18 },
    { date: '2024-01-05', value: 14 }
];

// 1. 차트 크기 설정
const width = 800;
const height = 400;
const margin = {top: 20, right: 30, bottom: 30, left: 40};

// 2. SVG 생성
const svg = d3.select('#chart')
    .append('svg')
    .attr('width', width)
    .attr('height', height);

// 3. 데이터 파싱
const parsedData = data.map(d => ({
    date: new Date(d.date),
    value: +d.value
}));

// 4. 스케일 설정
const xScale = d3.scaleTime()
    .domain(d3.extent(parsedData, d => d.date))
    .range([margin.left, width - margin.right]);

const yScale = d3.scaleLinear()
    .domain([0, d3.max(parsedData, d => d.value)])
    .range([height - margin.bottom, margin.top]);

// 5. 라인 생성기
const line = d3.line()
    .x(d => xScale(d.date))
    .y(d => yScale(d.value))
    .curve(d3.curveMonotoneX);  // 부드러운 곡선

// 6. 라인 그리기
svg.append('path')
    .datum(parsedData)
    .attr('class', 'line')
    .attr('d', line)
    .style('fill', 'none')
    .style('stroke', 'steelblue')
    .style('stroke-width', 2);

// 7. 데이터 포인트 추가
svg.selectAll('circle')
    .data(parsedData)
    .join('circle')
    .attr('cx', d => xScale(d.date))
    .attr('cy', d => yScale(d.value))
    .attr('r', 4)
    .style('fill', 'steelblue');

// 8. 축 추가
const xAxis = d3.axisBottom(xScale);
const yAxis = d3.axisLeft(yScale);

svg.append('g')
    .attr('class', 'x-axis')
    .attr('transform', `translate(0,${height - margin.bottom})`)
    .call(xAxis);

svg.append('g')
    .attr('class', 'y-axis')
    .attr('transform', `translate(${margin.left},0)`)
    .call(yAxis);
```

## 스타일링과 차트 꾸미기

```javascript
// 1. CSS 스타일링
svg.selectAll('.line')
    .style('stroke-dasharray', '5,5');  // 점선 스타일

// 2. 그리드 라인 추가
svg.selectAll('g.y-axis g.tick')
    .append('line')
    .attr('x2', width - margin.left - margin.right)
    .style('stroke', '#ddd')
    .style('stroke-dasharray', '2,2');

// 3. 차트 제목 추가
svg.append('text')
    .attr('x', width / 2)
    .attr('y', margin.top)
    .style('text-anchor', 'middle')
    .text('차트 제목');

// 4. 축 레이블 추가
svg.append('text')
    .attr('x', width / 2)
    .attr('y', height - 5)
    .style('text-anchor', 'middle')
    .text('시간');

svg.append('text')
    .attr('transform', 'rotate(-90)')
    .attr('x', -height / 2)
    .attr('y', 15)
    .style('text-anchor', 'middle')
    .text('값');
```

