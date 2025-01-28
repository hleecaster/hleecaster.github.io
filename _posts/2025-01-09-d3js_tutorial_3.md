---
title: D3.js로 인터랙티브 시각화 구현하기
date: 2025-01-09
categories: [WORK,  🌐 HTML & JavaScript]
tags: [데이터시각화, JavaScript, D3.js]
---


## 이벤트 핸들링 기초

D3.js는 DOM 이벤트를 쉽게 처리할 수 있는 방법을 제공한다.

### 1. 기본 이벤트 리스너

```javascript
// 기본 이벤트 바인딩
d3.select('circle')
    .on('mouseover', function(event, d) {
        // event: 이벤트 객체
        // d: 바인딩된 데이터
        d3.select(this)
            .style('fill', 'red');
    })
    .on('mouseout', function(event, d) {
        d3.select(this)
            .style('fill', 'steelblue');
    });

// 여러 요소에 이벤트 바인딩
svg.selectAll('circle')
    .data(data)
    .join('circle')
    .on('click', function(event, d) {
        console.log('클릭된 데이터:', d);
    });
```

### 2. 이벤트 전파와 기본 동작 제어

```javascript
// 이벤트 전파 중단
.on('click', function(event, d) {
    event.stopPropagation();
    // 처리 로직
});

// 기본 동작 방지
.on('click', function(event, d) {
    event.preventDefault();
    // 처리 로직
});
```

## 동적 요소 업데이트

### 1. 트랜지션(Transition) 사용

```javascript
// 기본 트랜지션
d3.select('circle')
    .transition()
    .duration(1000)  // 1초
    .attr('r', 30)
    .style('fill', 'red');

// 트랜지션 체이닝
d3.select('circle')
    .transition()
    .duration(1000)
    .attr('r', 30)
    .transition()
    .duration(500)
    .attr('r', 10);

// 지연 효과
d3.selectAll('circle')
    .transition()
    .delay((d, i) => i * 100)  // 순차적 애니메이션
    .duration(1000)
    .attr('r', d => d.value);
```

### 2. 데이터 기반 업데이트

```javascript
function updateChart(newData) {
    // 데이터 업데이트
    const circles = svg.selectAll('circle')
        .data(newData);
    
    // 새로운 요소 추가
    circles.enter()
        .append('circle')
        .attr('r', 0)
        .merge(circles)  // 기존 요소와 병합
        .transition()
        .duration(1000)
        .attr('cx', d => xScale(d.x))
        .attr('cy', d => yScale(d.y))
        .attr('r', 5);
    
    // 제거될 요소
    circles.exit()
        .transition()
        .duration(1000)
        .attr('r', 0)
        .remove();
}
```

## 툴팁 구현

```javascript
// 1. 툴팁 div 생성
const tooltip = d3.select('body')
    .append('div')
    .attr('class', 'tooltip')
    .style('position', 'absolute')
    .style('visibility', 'hidden')
    .style('background-color', 'white')
    .style('border', '1px solid #ddd')
    .style('padding', '10px')
    .style('border-radius', '3px')
    .style('box-shadow', '0 2px 4px rgba(0,0,0,0.1)')
    .style('font-size', '12px');

// 2. 차트 요소에 툴팁 이벤트 추가
svg.selectAll('circle')
    .data(data)
    .join('circle')
    .attr('cx', d => xScale(d.x))
    .attr('cy', d => yScale(d.y))
    .attr('r', 5)
    .on('mouseover', function(event, d) {
        // 요소 하이라이트
        d3.select(this)
            .transition()
            .duration(200)
            .attr('r', 8)
            .style('fill', 'red');
        
        // 툴팁 표시
        tooltip
            .style('visibility', 'visible')
            .html(`
                <strong>${d.category}</strong><br>
                X: ${d.x}<br>
                Y: ${d.y}<br>
                값: ${d.value}
            `);
    })
    .on('mousemove', function(event) {
        // 마우스 움직임에 따라 툴팁 위치 업데이트
        tooltip
            .style('top', (event.pageY - 10) + 'px')
            .style('left', (event.pageX + 10) + 'px');
    })
    .on('mouseout', function() {
        // 요소 원래대로
        d3.select(this)
            .transition()
            .duration(200)
            .attr('r', 5)
            .style('fill', 'steelblue');
        
        // 툴팁 숨기기
        tooltip.style('visibility', 'hidden');
    });
```

## Zoom과 Pan 구현

```javascript
// 줌 행동 정의
const zoom = d3.zoom()
    .scaleExtent([0.5, 5])  // 최소/최대 줌 레벨
    .on('zoom', zoomed);

// SVG에 줌 적용
svg.call(zoom);

// 줌 이벤트 핸들러
function zoomed(event) {
    // 현재 변환 정보
    const transform = event.transform;
    
    // 요소들 변환
    svg.selectAll('circle')
        .attr('transform', transform);
    
    // 축 업데이트
    svg.select('.x-axis')
        .call(xAxis.scale(transform.rescaleX(xScale)));
    svg.select('.y-axis')
        .call(yAxis.scale(transform.rescaleY(yScale)));
}

// 특정 영역으로 줌
function zoomTo(x, y, k) {
    svg.transition()
        .duration(750)
        .call(zoom.transform, d3.zoomIdentity
            .translate(x, y)
            .scale(k));
}
```

## 실전 예제: 인터랙티브 산점도

```javascript
// 데이터
const data = [
    {x: 10, y: 20, category: 'A', value: 15},
    {x: 15, y: 25, category: 'B', value: 22},
    // ... 더 많은 데이터
];

// 스케일 설정
const xScale = d3.scaleLinear()
    .domain(d3.extent(data, d => d.x))
    .range([margin.left, width - margin.right]);

const yScale = d3.scaleLinear()
    .domain(d3.extent(data, d => d.y))
    .range([height - margin.bottom, margin.top]);

// 차트 생성
const svg = d3.select('#chart')
    .append('svg')
    .attr('width', width)
    .attr('height', height);

// 툴팁 생성
const tooltip = createTooltip(svg);

// 점 그리기
const circles = svg.selectAll('circle')
    .data(data)
    .join('circle')
    .attr('cx', d => xScale(d.x))
    .attr('cy', d => yScale(d.y))
    .attr('r', 5)
    .style('fill', 'steelblue')
    .style('cursor', 'pointer')
    // 마우스 이벤트
    .on('mouseover', function(event, d) {
        d3.select(this)
            .transition()
            .duration(200)
            .attr('r', 8)
            .style('fill', 'red');
        
        updateTooltip(tooltip, d, [
            xScale(d.x) + 10,
            yScale(d.y) - 10
        ]);
    })
    .on('mouseout', function() {
        d3.select(this)
            .transition()
            .duration(200)
            .attr('r', 5)
            .style('fill', 'steelblue');
        
        tooltip.style('display', 'none');
    })
    .on('click', function(event, d) {
        // 클릭 시 줌
        const k = 2;  // 줌 레벨
        const x = xScale(d.x);
        const y = yScale(d.y);
        zoomTo(width/2 - x*k, height/2 - y*k, k);
    });

// 줌 기능 추가
svg.call(zoom);
```

