---
title: 머신러닝 공부 - 의사결정 나무(Decision Tree) 쉽게 이해하기
date: 2020-01-15
categories: [WORK, Data]
tags: [데이터분석, MachineLearning, Python]
math: true
---

의사결정 나무(Decision Tree)는 각 데이터들이 가진 속성들로부터 패턴을 찾아내서 분류 과제를 수행할 수 있도록 하는 지도학습 머신러닝 모델이다. 일단 이 모델의 개념만 최대한 쉽게 설명해본다.

## 의사결정 나무란 무엇인가

시험에서 A를 받은 데이터를 초록색 동그라미로 표현했다고 하자.

![decision tree](https://s3.amazonaws.com/codecademy-content/programs/data-science-path/decision-trees/tree_gif.gif)

의사결정 나무는 대체 어떤 사람들이 그 A를 받았는지 나름의 기준이나 체크리스트 같은 걸 만들어준다.

그래서 새로운 데이터가 들어오면 그 체크리스트를 바탕으로 하나씩 질문하고 (예를 들면 잠을 몇시간 잤냐, 공부는 몇 시간 했냐 등) 거기서 나온 답에 대한 다음 나무 줄기를 따라가다가 결국에는 ‘이건 어떤 레이블이겠구나’라고 분류를 해줄 수 있게 되는 거다.

일종의 스무고개 같은 거라 생각하면 편하다. 그러면 질문을 잘 설계하는 게 중요하겠지. 이를 위해 고려해야 할 개념들을 하나씩 살펴보자.

## 지니 불순도 (Gini Impurity)

어떤 질문이 좋은 질문일까.

아래 두 그림을 보면 답이 나온다.

![Gini Impurity 1](https://s3.amazonaws.com/codecademy-content/programs/data-science-path/decision-trees/comparison_1.svg)

![Gini Impurity 2](https://s3.amazonaws.com/codecademy-content/programs/data-science-path/decision-trees/comparison_2.svg)

일단 위 그림은 데이터 분할이 깔끔하지 않다. 우리의 목표는 아래 그림처럼 불순물(?) 없이 완전 깔끔하게 골라내는 거다.

여기서 불순물이라는 표현을 썼는데, 이 불순물이 어느정도 포함되어 있는지 확인할 수 있는 지표로 지니 불순도(Gini Impurity) 값을 확인해볼 수 있다.

계산법은 꽤 간단하다. `1`에서 ‘전체 데이터 개수 중 각 레이블이 차지하는 개수의 비율’을 제곱해서 빼주면 된다.

예를 들어 총 4개 데이터 세트에서 레이블 A가 3개, B가 1개 포함되어 있다면 이렇게 계산하면 된다.

$$ 1 - ( { {3}\over{4} } )^2 - ( { {1}\over{4} } )^2 = 0.375 $$

만약 단 하나의 레이블로 퓨어하게 구성되어 있다면 지니 불순도 값은 0이 될 거다.

결국 좋은 질문을 거쳐 분할된 데이터 세트는 지니 불순도 값이 작다는 걸 알 수 있다.

## 정보 획득량 (Information Gain)

지니 불순도를 구할 수 있다면, 이를 통해 어떤 질문에서 얻을 수 있는 정보 획득량(Information Gain)을 계산할 수 있다.

![Information Gain](https://s3.amazonaws.com/codecademy-content/programs/data-science-path/decision-trees/info.svg)

위처럼 불순도 0.5의 데이터 세트를 불순도 각각 0, 0.375, 0을 가진 3개의 데이터 세트로 분할했을 때, 여기서 얻은 정보 획득량은 다음과 같이 계산할 수 있다.

Information Gain = 0.5 – (0 + 0.375 + 0) = 0.125

그냥 이전 단계 불순도에서 다음 단계의 불순도 합을 빼주는 거다. 즉, 분할된 데이터 세트들의 불순도가 작을수록 정보 획득량은 증가한다.

그러나 이렇게 하위 데이터 세트들의 불순도만으로는 정보 획득량을 설명하기 부족한 측면이 있다. 그래서 가중치(weight)를 적용하곤 한다.

#### Weighted Information Gain

아래 그림을 보자.

![Weighted Information Gain 1](https://s3.amazonaws.com/codecademy-content/programs/data-science-path/decision-trees/impurity-0.svg)

두 개의 데이터 세트 모두 불순도는 0이지만, 오른쪽 데이터 세트가 더 의미있는 것처럼 보인다. 그 이유가 뭘까. 데이터 개수가 충분히 많고, 따라서 이 분류가 우연이 아니라고 확신할 수 있기 때문이다.

아무튼 이제 단순한 불순도뿐만 아니라 데이터 세트의 크기도 중요하다는 걸 알았으니 생성된 데이터 세트의 크기에 따라 가중치가 적용된 정보 획득량(Weighted Information Gain)을 계산해볼 수 있을 거다.

계산 법은 간단하다.

![Weighted Information Gain 2](https://s3.amazonaws.com/codecademy-content/programs/data-science-path/decision-trees/weighted_info.svg)

이렇게 분할하기 전 데이터에 비해 분할 후 생성된 데이터의 크기(비율)에 따라 가중치를 구해놓고 이를 불순도에 곱해서 정보 획득량을 구하면 된다.



Information Gain = 0.5 – ((2/10)*0.5 + (5/10)*0.48 + (3/10)*0.44) = 0.026

데이터 세트의 크기가 작을 수록 그 불순도의 영향력도 작아진다.

## 재귀적(Recursive) 트리 빌딩

의사결정 트리에서는 정보 획득량(Information Gain)이 큰 순서대로 질문을 배치하는 게 중요하다. (상식적인 거다. 스무고개를 하더라도 크리티컬한 질문을 먼저 하는 게 좋지 않은가.)

그리고 그 속성에 대해 어느 기준으로 나누는 게 좋을지 반복적으로 적용해보면서 최적의 트리를 찾게 된다. 이를 재귀(recursive) 알고리즘이라고 하는데 너무 깊이 들어가진 말자.

아무튼 이렇게 반복하면서 찾으면서 더 이상 정보 획득량이 없을 때 재귀(recursion)를 중단하게 된다. 더 이상은 순수한 하위 집합을 만드는 방법을 찾을 수 없을 때까지 하는 거다.

## scikit-learn 사용법

파이썬 라이브러리 scikit-learn을 사용하면 쉽게 트리를 만들 수 있다.

```python
from sklearn.tree import DecisionTreeClassifier

classifier = DecisionTreeClassifier()
classifier.fit(training_points, training_labels)
```

학습 데이터를 기반으로 트리를 생성할 때는 `.fit()` 메서드를 사용하면 끝이다. 다만, 의사결정 트리에서는 만약 데이터가 문자열로 이루어져 있다면 이걸 숫자로 map해서 넣어주는 게 좋다. 예를 들어` {“low”: 1, “mid”: 2, “high”: 3}` 이런 식으로.

이제 새로운 데이터가 있을 때 예측을 해볼 수도 있고,

```python
predictions = classifier.predict(test_data)
```

미리 테스트 세트를 나눠놓았다면 분류 정확도를 확인해볼 수도 있다.

```python
print(classifier.score(test_data, test_labels))
```

## 의사결정 나무의 한계

지금까지 의사결정 나무를 만들고 사용하는 방법을 간략히 소개했으니 이제는 몇가지 제한점, 한계에 대해 짚고 넘어가야겠다.

#### 한계 1.

일단 트리를 빌딩, 생성하는 방식에서 한계가 있다. 의사결정 나무는 지금 상황에서 어떤 속성을 가지고 분할해야 정보 획득량이 가장 클 것인지 확인해서, 그 속성에 따라 데이터를 분할한다. 즉, 데이터를 먼저 이렇게 나누었을 때 나타날 파급 효과를 고려하지 않는 거다.

그 순간만 보면 최선의 분할이 아니지만 이어서 나타나는 속성들에서 더 나은 분할을 발견하면 결과적으로 더 좋은 트리를 만들 수도 있는데, 순간의 선택 때문에 그 가능성을 배제해버린다는 뜻이다.

이런 방법은 ‘greedy‘라고 부른다. 여러 경우 중 하나를 결정해야 할 때마다 그 순간에 최적이라고 생각되는 것을 선택해나가는 방식으로 진행해서 최종적인 해답에 도달하는 일명 ‘탐욕 알고리즘‘.

그럼 처음부터 가장 최적의 트리를 찾지 않고 왜 이렇게 할까?

최적의 트리를 찾는 건 매우 어려운 작업이고, 이 탐욕스러운(?) 방식을 사용하는 것도 충분히 합리적이고 좋은 대안이라는 합의가 있기 때문이다.

#### 한계 2.

또 다른 문제는 의사결정 나무가 학습 데이터에 오버피팅 한다는 거다. 트리의 구조가 훈련 데이터에 너무 의존적이라 현실의 데이터를 정확하게 나타내지 못한다는 뜻이다. 일반적으로 나무가 커질수록 학습 데이터에 알맞게 모델이 만들어져서 현실 데이터로 일반화(generalization)하기 어려워지는 경향이 있다.

이걸 해결하려면 결국 나무를 잘라내서 크기를 줄여야 한다. 일명 가지치기(pruning). 가지치기 전략에도 여러가지가 있는데 여기서는 자세히 설명하지 않으려 한다. 어려우니까. 파이썬 라이브러리 scikit-learn에서도 가지치기는 안 하는 게 기본 값이다. 직접 하려면 코드를 약간 파고들어야 된다. 일단 너무 깊이 들어가지 말자.

대신 트리의 크기를 확인하거나 제한하는 방법만 간략히 소개해보면… 모델 학습 후 `classifier.tree_.max_depth`를 출력해보면 몇 단계까지 가는지 알 수 있다. 그리고 모델을 불러올 때 아예 `DecisionTreeClassifier(max_depth = n)` 이런 식으로 트리의 최대 크기를 지정할 수도 있다.

## 요약

- 어떤 클래스에 속하는 데이터 포인트들이 동일한 레이블을 가질 때 순도가 높다고 표현한다. 순도 높은 이파리(leaf)가 많아야 좋은 의사결정 나무다.
- 의사결정 나무는 현 상태에서 가장 높은 정보 획득량(information gain)을 제공하는 속성을 우선적으로 선택하고 그 기준으로 데이터를 분할하는 탐욕 알고리즘(greedy algorithm)을 사용하여 트리를 만든다.
- 탐욕 알고리즘(greedy algorithm)은 언제나 최적의 트리를 찾아주진 못하는 한계가 있다. 그러나 최적의 트리를 찾는 건 어려운 일이다.
- 의사결정 나무는 오버피팅이 자주 발생한다. 가지치기(pruning)를 하면 트리를 작게 만들어 일반화 할 수 있고 현실의 데이터에 적용했을 때 정확도를 높일 수 있다.