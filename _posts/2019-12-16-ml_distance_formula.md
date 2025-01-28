---
title: 머신러닝 공부 - 두 점 사이의 거리 공식(Distance Formula) 쉽게 이해하기
date: 2019-12-16
categories: [WORK, 📊 Data]
tags: [데이터분석, MachineLearning, Python]
math: true
---

본 포스팅에서는 두 점 사이의 거리를 구하는 여러가지 방법을 알아본다.

그런데 거리(distance)를 왜 구해야 할까? 거리는 일종의 유사도(Similarity) 개념이기 때문이다. 거리가 가까울수록 그 특성(feature)들이 비슷하다는 뜻이니까. 그래서 머신러닝 알고리즘에서도 매우 널리 사용된다. 예를 들면 K-최근접 이웃(K-Nearest Neighbor) 알고리즘 같은 데에서.

아무튼 그래서 거리 구하는 방법은 알고 있어야 한다. 수학적으로 막 깊이 파고들 필요는 없어도 개념은 이해하고 가야 알고리즘을 이해하고 사용할 수 있다.

일단 들어가기에 앞서 코드를 통해 점의 위치를 표현하는 방법을 알아야지. 뭐 별 건 아니다.

예를 들어, 흔히 말하는 x, y축만 있는 2차원에 점을 나타내고 싶다면 `[5, 8]`와 같이 쓸 수 있을 거다. 물론 꼭 2차원으로만 있는 건 아니다. 5차원 점은 `[4, 8, 15, 16, 23]`과 같이 표현하면 된다.

자, 이제 파이썬으로 두 점 사이의 거리를 찾는 함수를 작성해볼 거다. 예를 들면 이런 식으로.

```python
distance([1, 2, 3], [5, 8, 9])
```

아, 그리고 여기서 두 점이 가진 차원의 개수는 당연히 같아야 한다.

이제부터 두 점 사이의 거리를 구하는 방법을 총 3가지 소개하려 한다.

1. 유클리드 거리 (Euclidean Distance)
2. 맨하탄 거리 (Manhattan Distance)
3. 해밍 거리 (Hamming Distance)

물론 더 많지만 일단 이 3개만 알고 가자.

## 1. 유클리드 거리 (Euclidean Distance)

‘유클리디안 거리’라고 영어 단어를 그대로 읽기도 하는데, 아무튼 가장 널리 쓰이는 거리 계산 방법이다.

예를 들어 아래와 같이 2차원에 있는 점 p와 q의 거리를 구한다면 

![Euclidean Distance](https://upload.wikimedia.org/wikipedia/commons/5/55/Euclidean_distance_2d.svg)

이렇게 나타낼 수 있다. 피타고라스의 정리가 떠오를 거다. 중학교 때 다 배웠던 거다.

$$ d(p,q)={\sqrt {(p_{1}-q_{1})^{2}+(p_{2}-q_{2})^{2}}} $$

그리고 위 예제는 2차원이지만 만약 n차원에서 두 점 사이의 거리를 구한다면 이렇게 나타낼 수 있겠다. 각 차원의 차를 제곱해서 모두 더한 값의 제곱근이다.

$$ d(p,q)={\sqrt {(p_{1}-q_{1})^{2}+(p_{2}-q_{2})^{2}+ \cdots +(p_{n}-q_{n})^{2}}} $$

이걸 파이썬 함수로 구현하면 아래와 같다.

```python
def euclidean_distance(pt1, pt2):
  distance = 0
  for i in range(len(pt1)):
    distance += (pt1[i] - pt2[i])  2
  return distance  0.5

# print(euclidean_distance([5, 4, 3], [1, 7, 9]))
```

## 2. 맨하탄 거리(Manhattan Distance)

맨하탄 거리도 유클리드 거리와 유사하긴 한데, 각 차원의 차를 제곱해서 사용하는 게 아니라 그냥 절대값을 바로 합산하는 거다.

아래 그림과 같은 개념으로 이해하면 쉽다. 실제로 맨하탄 거리라는 이름도 도시의 골목길을 걸을 때의 모습과 유사하기 때문에 붙은 거라고 한다. (몇 블록 이동하는지 상상해보자.)

![Manhattan Distance](https://upload.wikimedia.org/wikipedia/commons/thumb/0/08/Manhattan_distance.svg/1920px-Manhattan_distance.svg.png)

그림에서도 알 수 있듯이 맨하탄 거리는 항상 유클리드 거리보다 크거나 같다.

2차원에서 맨하탄 거리는 이렇게 구하게 될 거고,

$$ d(p,q)=\left|p_{1}-q_{1}\right|+\left|p_{2}-q_{2}\right| $$

만약 n차원에서 두 점 사이의 거리를 구한다면 이렇게 나타낼 수 있겠다.

$$ d(p,q)=\left|p_{1}-q_{1}\right|+\left|p_{2}-q_{2}\right| + \cdots + \left|p_{n}-q_{n}\right| $$

이걸 파이썬 함수로 구현하면 아래와 같다.

```python
def manhattan_distance(pt1, pt2):
  distance = 0
  for i in range(len(pt1)):
    distance += abs(pt1[i] - pt2[i])
  return distance

# print(manhattan_distance([5, 4, 3], [1, 7, 9]))
```


## 3. 해밍 거리 (Hamming Distance)

해밍 거리는 앞서 설명한 유클리드 거리, 맨하탄 거리와는 좀 다른 개념이다.

해밍 거리에서는 각 차원마다 차이를 찾는 게 아니라 ‘정확히 같은지’ 여부만 고려한다. 그래서 주로 맞춤법 검사와 같은 알고리즘에 많이 사용된다.

예를 들어, 단어 “there”와 오타 “thete”사이의 해밍 거리는 1인 셈이다. 각 문자가 차원이며 이 예시에서 각 차원은 네 번째 문자 “r”과 “t”를 제외하고 동일한 값을 갖고 있기 때문이다.

예를 들어, 단어 “there”와 오타 “thete”사이의 해밍 거리는 1이다. 각 문자가 곧 차원이고, 이 예시에서는 네 번째 문자 “r”과 “t”가 다르기 때문이다. 결국 서로 다른 글자 개수만 세주는 셈이다.

해밍 거리 구하는 방법을 파이썬 함수로 구현하면 아래와 같다.

```python
def hamming_distance(pt1, pt2):
  distance = 0
  for i in range(len(pt1)):
    if pt1[i] != pt2[i]:
      distance += 1
  return distance

# print(hamming_distance([5, 4, 3], [1, 7, 9]))
```


## SciPy를 통해 두 점 사이의 거리 구하기

파이썬의 라이브러리 Scipy를 사용하면 위에서 소개한 거리를 매우 쉽게 구할 수 있다.

- 유클리드 거리: `.euclidean()`
- 맨하탄 거리: `.cityblock()`
- 해밍 거리: `.hamming()`

(맨하탄 거리는 city block처럼 몇 블록 떨어져 있는지 구하는 꼴이기 때문에 아예 그렇게 이름이 붙어 있는 걸 볼 수 있다.)

그리고 위에서 소개한 해밍 거리를 Scipy에서는 0과 1사이의 값으로 돌려준다. 몇 개 차원에서 차이가 나는지 합산한 다음에 총 차원의 개수로 나누기 때문이다. 그래서 만약 위에서 직접 작성한 함수에서 `[1, 2, 3]`과 `[7, 2, -10]`사이의 해밍 거리를 구하면 2이지만, scipy에서는 2/3으로 나온다.

아무튼 scipy에서는 이렇게 거리를 바로 구할 수 있다.

```python
from scipy.spatial import distance

print(distance.euclidean([1, 2], [4, 0]))
print(distance.cityblock([1, 2], [4, 0]))
print(distance.hamming([5, 4, 9], [1, 7, 9]))
```

이렇게 거리 구하는 방법을 이해했다면 KNN 알고리즘을 이해할 수 있게 된 거다.