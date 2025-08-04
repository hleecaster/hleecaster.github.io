---
title: D3.js로 노드 시뮬레이션 구현하기
date: 2025-01-09
categories: [WORK,  Web]
tags: [데이터시각화, JavaScript, D3.js]
---


## Force Simulation 이해하기

Force Simulation은 입자들 간의 힘과 운동을 시뮬레이션하는 D3.js의 기능이다. 
노드와 링크로 구성된 네트워크 데이터를 자동으로 레이아웃하는 데 주로 사용된다.

### 기본 개념

```javascript
// 기본 시뮬레이션 생성
const simulation = d3.forceSimulation()
    .force('charge', d3.forceManyBody())  // 노드 간 인력/척력
    .force('center', d3.forceCenter(width / 2, height / 2))  // 중심력
    .force('link', d3.forceLink())  // 노드 간 연결
```

### 주요 Force 타입

1. `forceManyBody()`: 노드 간 인력 또는 척력
2. `forceCenter()`: 전체 노드를 중심으로 끌어당기는 힘
3. `forceLink()`: 연결된 노드 간의 힘
4. `forceCollide()`: 노드 간 충돌 방지
5. `forceX()`, `forceY()`: 특정 x, y 좌표로 끌어당기는 힘


### 시뮬레이션 제어: alpha, alphaTarget, restart

force simulation은 alpha라는 값을 사용하여 시뮬레이션의 진행 상태를 관리한다.

- `alpha()`: 현재 시뮬레이션의 alpha 값을 반환한다. 초기값은 1이며, 시뮬레이션이 진행됨에 따라 점차 0으로 감소한다. alpha 값이 0에 가까워지면 시뮬레이션이 안정화되었다고 판단한다.
- `alphaTarget([target])`: 시뮬레이션의 목표 alpha 값을 설정한다. 이 값을 설정하면 시뮬레이션은 현재 alpha 값에서 목표 alpha 값으로 점진적으로 변화한다. 드래그 이벤트와 같이 사용자의 인터랙션이 발생했을 때 시뮬레이션을 다시 활성화하는 데 유용하다.
- `alphaMin([min])`: 시뮬레이션이 멈추는 최소 alpha 값을 설정한다. alpha 값이 이 값 아래로 내려가면 시뮬레이션이 자동으로 멈춘다. 기본값은 0.001이다.
- `alphaDecay([decay])`: alpha 값이 감소하는 비율을 설정한다. 값이 클수록 alpha 값이 빠르게 감소하여 시뮬레이션이 빠르게 안정화된다. 기본값은 0.0228이다.
- `velocityDecay([decay])`: 노드의 속도가 감소하는 비율을 설정한다. 마찰력과 유사한 역할을 하며, 값이 클수록 노드가 빠르게 멈춘다. 기본값은 0.4이다.
- `restart()`: 시뮬레이션을 다시 시작. alpha 값을 초기값(1)으로 설정하고 시뮬레이션을 재개한다. 주로 alphaTarget()과 함께 사용하여 사용자의 인터랙션에 따라 시뮬레이션을 다시 활성화한다.

아래는 드래그 이벤트에서 이 속성들을 활용하는 예시이다.

```javaScript
function drag(simulation) {
    function dragstarted(event) {
        if (!event.active) simulation.alphaTarget(0.3).restart(); // 드래그 시작 시 alphaTarget을 0.3으로 설정하고 시뮬레이션 재시작
        event.subject.fx = event.subject.x;
        event.subject.fy = event.subject.y;
    }

    function dragged(event) {
        event.subject.fx = event.x;
        event.subject.fy = event.y;
    }

    function dragended(event) {
        if (!event.active) simulation.alphaTarget(0); // 드래그 종료 시 alphaTarget을 0으로 설정
        event.subject.fx = null;
        event.subject.fy = null;
    }

    return d3.drag()
        .on('start', dragstarted)
        .on('drag', dragged)
        .on('end', dragended);
}
```

## 네트워크 그래프 구현

```javascript
// 1. 데이터 준비
const nodes = [
    { id: 'A', group: 1 },
    { id: 'B', group: 1 },
    { id: 'C', group: 2 },
    { id: 'D', group: 2 }
];

const links = [
    { source: 'A', target: 'B', value: 1 },
    { source: 'B', target: 'C', value: 2 },
    { source: 'C', target: 'D', value: 1 }
];

// 2. SVG 생성
const svg = d3.select('#chart')
    .append('svg')
    .attr('width', width)
    .attr('height', height);

// 3. 시뮬레이션 설정
const simulation = d3.forceSimulation(nodes)
    .force('link', d3.forceLink(links)
        .id(d => d.id)
        .distance(100))
    .force('charge', d3.forceManyBody()
        .strength(-200))
    .force('center', d3.forceCenter(width / 2, height / 2))
    .force('collision', d3.forceCollide().radius(30));

// 4. 링크 그리기
const link = svg.append('g')
    .selectAll('line')
    .data(links)
    .join('line')
    .style('stroke', '#999')
    .style('stroke-width', d => Math.sqrt(d.value));

// 5. 노드 그리기
const node = svg.append('g')
    .selectAll('circle')
    .data(nodes)
    .join('circle')
    .attr('r', 20)
    .style('fill', d => d.group === 1 ? 'red' : 'blue')
    .call(drag(simulation));  // 드래그 기능 추가

// 6. 시뮬레이션 업데이트 함수
simulation.on('tick', () => {
    link
        .attr('x1', d => d.source.x)
        .attr('y1', d => d.source.y)
        .attr('x2', d => d.target.x)
        .attr('y2', d => d.target.y);

    node
        .attr('cx', d => d.x)
        .attr('cy', d => d.y);
});
```

## 드래그 기능 구현

```javascript
// 드래그 함수 정의
function drag(simulation) {
    function dragstarted(event) {
        if (!event.active) simulation.alphaTarget(0.3).restart();
        event.subject.fx = event.subject.x;
        event.subject.fy = event.subject.y;
    }

    function dragged(event) {
        event.subject.fx = event.x;
        event.subject.fy = event.y;
    }

    function dragended(event) {
        if (!event.active) simulation.alphaTarget(0);
        event.subject.fx = null;
        event.subject.fy = null;
    }

    return d3.drag()
        .on('start', dragstarted)
        .on('drag', dragged)
        .on('end', dragended);
}
```

## 실전 예제: 인터랙티브 네트워크 그래프

```javascript
// 1. 데이터 설정
const data = {
    nodes: [
        { id: '1', name: 'Node 1', group: 1, size: 10 },
        { id: '2', name: 'Node 2', group: 1, size: 15 },
        { id: '3', name: 'Node 3', group: 2, size: 20 },
        // ... 더 많은 노드
    ],
    links: [
        { source: '1', target: '2', weight: 1 },
        { source: '2', target: '3', weight: 2 },
        // ... 더 많은 링크
    ]
};

// 2. 색상 스케일 설정
const colorScale = d3.scaleOrdinal(d3.schemeCategory10);

// 3. 시뮬레이션 생성
const simulation = d3.forceSimulation(data.nodes)
    .force('link', d3.forceLink(data.links)
        .id(d => d.id)
        .distance(d => 100 / d.weight))
    .force('charge', d3.forceManyBody()
        .strength(d => -50 * d.size))
    .force('center', d3.forceCenter(width / 2, height / 2))
    .force('collision', d3.forceCollide()
        .radius(d => d.size * 2));

// 4. 줌 기능 추가
const zoom = d3.zoom()
    .scaleExtent([0.1, 4])
    .on('zoom', zoomed);

svg.call(zoom);

function zoomed(event) {
    container.attr('transform', event.transform);
}

const container = svg.append('g');

// 5. 링크 그리기
const link = container.append('g')
    .selectAll('line')
    .data(data.links)
    .join('line')
    .style('stroke', '#999')
    .style('stroke-opacity', 0.6)
    .style('stroke-width', d => Math.sqrt(d.weight));

// 6. 노드 그리기
const node = container.append('g')
    .selectAll('g')
    .data(data.nodes)
    .join('g')
    .call(drag(simulation));

// 노드 원 추가
node.append('circle')
    .attr('r', d => d.size)
    .style('fill', d => colorScale(d.group))
    .style('stroke', '#fff')
    .style('stroke-width', 1.5);

// 노드 레이블 추가
node.append('text')
    .text(d => d.name)
    .style('font-size', '12px')
    .attr('x', 0)
    .attr('y', d => d.size + 15)
    .style('text-anchor', 'middle');

// 7. 툴팁 추가
const tooltip = d3.select('body')
    .append('div')
    .attr('class', 'tooltip')
    .style('opacity', 0);

node.on('mouseover', function(event, d) {
    tooltip.transition()
        .duration(200)
        .style('opacity', .9);
    
    tooltip.html(`
        이름: ${d.name}<br/>
        그룹: ${d.group}<br/>
        크기: ${d.size}
    `)
    .style('left', (event.pageX + 10) + 'px')
    .style('top', (event.pageY - 10) + 'px');
})
.on('mouseout', function() {
    tooltip.transition()
        .duration(500)
        .style('opacity', 0);
});

// 8. 시뮬레이션 업데이트
simulation.on('tick', () => {
    link
        .attr('x1', d => d.source.x)
        .attr('y1', d => d.source.y)
        .attr('x2', d => d.target.x)
        .attr('y2', d => d.target.y);

    node
        .attr('transform', d => `translate(${d.x},${d.y})`);
});
```

## 시뮬레이션 파라미터 조정

```javascript
// 시뮬레이션 강도 조정
simulation
    .force('charge')
    .strength(-100);  // 척력 조정

// 링크 거리 조정
simulation
    .force('link')
    .distance(150);   // 링크 길이 조정

// 시뮬레이션 속도 조정
simulation
    .velocityDecay(0.3)  // 마찰력 (0~1)
    .alphaMin(0.001)     // 최소 알파값
    .alphaDecay(0.0228); // 알파값 감소율
    .alphaTarget(0.1);   // 천천히 안정화되도록 설정
```

