---
title: AI와 효과적으로 협업하는 법 (Anthropic의 AI Fluency)
date: 2026-03-13
categories: [Work, AI]
tags: [AI]
---


Anthropic Academy에서 제공한
[AI Fluency: Framework & Foundations](https://anthropic.skilljar.com/ai-fluency-framework-foundations) 내용 정리 


---


## AI Fluency란 무엇인가

AI Fluency는 AI 시스템과 효과적이고(effective) 효율적이며(efficient) 윤리적이고(ethical) 안전한 방식으로(safe) 상호작용하는 능력을 뜻한다.  
단순히 프롬프트를 잘 쓰는 기술이 아니라,  
실용적 기술(skills), 지식(knowledge), 통찰(insights), 가치관(values)을 모두 포괄하는 종합적인 역량이다.

이 프레임워크의 핵심은 4D Framework라는 4개 역량으로 구성된다. 

1. Delegation (위임)
2. Description (기술)
3. Discernment (분별)
4. Diligence (책임)

이 역량들은 서로 독립적이지 않고, 긴밀하게 연결되어 있다.


---


### Deep Dive 1: 생성형 AI 이해하기

4D Framework에 들어가기 전에, 먼저 생성형 AI(Generative AI)가 무엇인지 기초부터 알아보자.


### 전통적 AI vs 생성형 AI

- 전통적 AI: 기존 데이터를 분석하는 데 초점을 맞춘다. (예: 받은 이메일을 스팸인지 아닌지 분류하기)
- 생성형 AI: 완전히 새로운 콘텐츠를 생성할 수 있다. (예: 새로운 이메일을 통째로 작성하기)


### 생성형 AI를 가능하게 한 세 가지 기둥

- 알고리즘의 진화: 2017년 등장한 Transformer 아키텍처가 긴 텍스트의 처리 방식을 혁신했다.
- 데이터 폭증: 웹사이트, 코드 저장소 등 방대한 디지털 텍스트 데이터가 학습 재료가 됐다.
- 컴퓨팅 파워의 비약적 성장: GPU 같은 고성능 칩 덕분에 이 모든 데이터를 학습시키는 게 가능해졌다.


### AI가 만들어지는 과정

1. 사전 학습(Pre-training): 수십억 개의 텍스트 예시를 분석하며, 다음에 올 단어를 예측하는 방식으로 패턴을 학습한다.
2. 파인튜닝(Fine-tuning): 지시를 따르고, 유용하게 응답하며, 유해한 콘텐츠를 생성하지 않도록 추가 학습을 진행한다.
3. 배포(Deployment): 사용자가 프롬프트를 제공하면, 모델이 학습된 패턴을 기반으로 응답을 생성한다.


### 주요 능력과 한계

- 능력: 다양한 언어 처리, 범용적 문제 해결, 예시를 통한 학습, 외부 도구 및 데이터와의 연결
- 한계: 학습 데이터 이후의 지식 부재(Knowledge cutoff), 정보를 그럴듯하지만 부정확하게 생성하는 경향(Hallucination), 한 번에 처리할 수 있는 정보량 제한(Context window), 복잡한 추론 및 수학에서의 어려움


---


## 4D Framework: 1. Delegation (위임)

Delegation은 어떤 작업을 인간이 할지, AI가 할지, 혹은 함께 할지를 전략적으로 결정하고 업무를 분배하는 역량이다.


### 세 가지 하위 역량
  
- Problem Awareness (문제 인식): AI를 활용하기 전에, 먼저 자신이 무엇을 달성하려는지, 작업의 본질이 무엇인지를 명확히 이해하는 것이다. "내가 지금 하려는 게 정확히 뭐지?"라는 질문에서 시작한다.
- Platform Awareness (플랫폼 인식): 다양한 AI 시스템의 능력과 한계를 이해하는 것이다. 모든 AI가 같은 작업에 적합하지 않다. 어떤 도구가 어떤 강점을 가지고 있는지 파악해야 한다.
- Task Delegation (작업 위임): 인간과 AI 각각의 강점을 활용해 업무를 사려 깊게 분배하는 것이다. 효과적인 위임에는 해당 분야에 대한 전문성과 AI의 능력에 대한 이해가 모두 필요하다.


### 세 가지 AI 상호작용 모드

위임을 할 때 어떤 모드로 AI를 활용할지도 함께 고려해야 한다.

- 자동화(Automation): AI가 인간의 구체적 지시에 따라 특정 작업을 실행한다. 인간이 무엇을 할지 정의하고, AI가 그대로 수행한다.
- 증강(Augmentation): 인간과 AI가 사고 파트너로서 협업한다. 반복적인 대화를 주고받으며 둘 다 결과물에 기여한다.
- 에이전시(Agency): 인간이 AI의 지식과 행동 패턴을 설정하면, AI가 독립적으로 작업을 수행한다. 구체적 행동 지시가 아닌, 동작 방식 자체를 구성하는 것이다.


---


## 4D Framework: 2. Description (기술)

Description은 생산적인 협업 환경을 만들기 위해 AI와 효과적으로 소통하는 능력이다. 단순히 질문을 던지는 게 아니라, 원하는 결과를 구체적으로 전달하는 게 핵심이다.


### 세 가지 하위 역량

- Product Description (결과물 기술): 원하는 출력물의 형식, 대상 독자, 스타일 등을 구체적으로 정의한다. "무엇을 받고 싶은가"에 초점을 맞춘다.
- Process Description (과정 기술): AI가 요청에 접근하는 방식을 정의한다. 예를 들어 단계별 지시를 제공하거나, 특정 방법론을 따르도록 안내하는 것이다.
- Performance Description (행동 기술): 협업 과정에서 AI가 보여야 할 태도나 커뮤니케이션 스타일을 정의한다. 간결하게 답할지 상세하게 설명할지, 도전적으로 반응할지 지지적으로 응답할지를 지정할 수 있다. 예를 들면 "소크라테스식 튜터처럼 행동해줘"와 같은 지시다.

> AI와 미리 명확하게 소통하면 시간을 절약하고 더 나은 결과를 얻을 수 있다.


---


### Deep Dive 2: 효과적인 프롬프트 엔지니어링 6가지 기법

Description 역량을 실전에 적용하기 위한 구체적 테크닉이다.

#### 1. 맥락을 제공하라 (Provide Context)

범위, 지역, 시간대 등 구체적인 세부 사항을 포함해서 요청해야 한다.

- 🔴 "기후 변화에 대해 알려줘"
- 🟢 "열대 지역 농업에 대한 기후 변화의 주요 영향 3가지를, 최근 10년간의 사례와 함께 설명해줘"


#### 2. 좋은 예시를 보여줘라 (Show Examples)

원하는 패턴, 스타일, 형식을 예시로 제시하면 장황한 설명보다 효과적이다.  
전문 용어를 쉽게 바꾸는 작업이라면, 기대하는 변환 결과를 2~3개 먼저 보여주는 식이다.


#### 3. 출력 조건을 명시하라 (Specify Output Constraints)

원하는 형식, 길이, 구조, 기타 특성을 프롬프트 안에서 구체적으로 지정해야 한다.

- 🔴 "포트폴리오 웹사이트를 만들어줘"
- 🟢 "Hero, About Me, Skills, Portfolio, Experience, Contact 섹션이 있는 단일 페이지 포트폴리오를 만들어줘"


#### 4. 복잡한 작업을 단계로 나눠라 (Break Complex Tasks into Steps)

복잡한 분석 요청이라면, AI에게 따라야 할 단계를 명시적으로 안내해야 한다.

> 예: "이 분기별 매출 데이터를 분석해줘. 먼저 (1) 최고 실적 제품을 파악하고, (2) 전 분기와 비교하고, (3) 이상 패턴을 강조하고, (4) 원인을 추정해줘."


#### 5. 먼저 생각하도록 요청하라 (Ask It to Think First)

답변하기 전에 문제를 깊이 생각하도록 요청하면, 더 신중하고 포괄적인 답변을 이끌어낼 수 있다.


#### 6. AI의 역할을 정의하라 (Define the AI's Role)

AI에게 특정 역할, 전문성 수준, 커뮤니케이션 스타일을 부여하면 된다.

> 예: "과학에 관심 있는 10살 아이에게 무지개가 어떻게 생기는지 설명하는 베테랑 과학 선생님 관점에서 설명해줘"


#### 💡팁: AI에게 프롬프트 도움을 요청하라

어떻게 물어봐야 할지 모르겠을 때, AI에게 직접 효과적인 프롬프트를 만들어달라고 요청할 수 있다. 이게 아마 가장 강력한 기법일 거다.


---


## 4D Framework: 3. Discernment (분별)

Discernment는 AI가 생성한 것, 생성한 방법, 그리고 상호작용 중 AI의 태도를 비판적으로 평가하는 능력이다.


### 세 가지 하위 역량
- Product Discernment (결과물 분별): AI가 생산한 결과물의 품질을 평가한다. 정확성(accuracy), 적절성(appropriateness), 일관성(coherence), 관련성(relevance)을 기준으로 검토한다.
- Process Discernment (과정 분별): AI가 결과에 도달한 과정을 평가한다. 논리적 오류, 주의력 부족, 부적절한 추론 단계가 있는지 살펴본다.
- Performance Discernment (행동 분별): 상호작용 중 AI의 행동을 평가한다. AI의 커뮤니케이션 스타일이 자신의 필요에 효과적인지를 판단한다.


### Description과 Discernment의 피드백 루프

이 두 역량은 지속적인 피드백 루프를 형성한다.  
Description으로 AI에게 지시하고  
→ Discernment로 결과를 평가한 후  
→ 다시 Description을 개선하는 반복 과정을 통해 점점 더 좋은 결과를 얻을 수 있다.  
이게 AI와의 협업에서 가장 실용적인 핵심 패턴이다.


---


## 4D Framework: 4. Diligence (책임)

Diligence는 AI를 사용하면서 하는 것과 하는 방법에 대해 책임을 지는 역량이다. 윤리적이고 책임감 있는 AI 활용의 핵심이다.


### 세 가지 하위 역량

- Creation Diligence (생성 책임): 어떤 AI 시스템을 사용할지, 어떻게 상호작용할지를 신중하게 선택하는 것이다. 모든 AI 도구가 동일하지 않고, 사용 맥락에 따라 적절한 선택이 달라진다.
- Transparency Diligence (투명성 책임): 작업에서 AI의 역할을 알아야 하는 모든 사람에게 정직하게 공개하는 것이다. "이 문서에서 Claude 3.7을 사용했습니다"와 같은 명시적 공개가 이에 해당한다.
- Deployment Diligence (배포 책임): 사용하거나 공유하는 AI 출력물을 검증하고, 그 결과에 대해 책임지는 것이다. AI가 생성했더라도, 최종적으로 사용하거나 배포하는 건 인간의 책임이다.


### 맥락에 따른 기대치

개인적, 학술적, 전문적 맥락에 따라 공개(disclosure)와 검증(verification)에 대한 기대치가 다르다.  
블로그 글에 AI 도움을 받는 것과, 학술 논문이나 법률 문서에서 AI를 사용하는 것은 요구되는 투명성의 수준이 다르다.


---


## 주요 용어 정리

- LLM (Large Language Model): 방대한 텍스트 데이터로 학습된 대규모 언어 모델
- Parameters: 모델이 정보를 처리하는 방식을 결정하는 수학적 값. 현대 LLM은 수십억 개의 파라미터를 보유
- Context Window: AI가 한 번에 고려할 수 있는 정보의 양 (대화 기록 + 공유 문서 포함)
- Hallucination: AI가 그럴듯하지만 실제로는 틀린 정보를 자신 있게 말하는 오류
- Temperature: AI 응답의 무작위성을 조절하는 설정. 높으면 창의적, 낮으면 예측 가능한 응답
- RAG (Retrieval Augmented Generation): AI를 외부 지식 소스에 연결하여 정확도를 높이고 할루시네이션을 줄이는 기법
- Few-shot Learning: 원하는 입출력 패턴의 예시를 보여주며 AI를 가르치는 방법
- Chain-of-thought Prompting: 복잡한 문제를 단계별로 나누어 풀도록 유도하는 프롬프팅 기법
- Role/Persona Definition: AI가 특정 캐릭터, 전문성, 커뮤니케이션 스타일을 채택하도록 지정하는 것


---


## 마무리: 왜 AI Fluency가 중요한가

AI는 도구이고, 도구를 잘 쓰려면 기술이 필요하다. 하지만 AI Fluency는 단순한 기술 이상이다.  

무엇을 AI에게 맡기고 무엇을 직접 할지 판단하는 Delegation,  
AI와 명확하게 소통하는 Description,  
결과를 비판적으로 평가하는 Discernment,  
그리고 책임감 있게 사용하는 Diligence  

이 네 가지는 기술적 역량이자 동시에 윤리적 판단력이다.

AI 기술은 계속 진화하겠지만, 
이 4D 프레임워크가 제시하는 사고 방식은 어떤 AI 시스템을 사용하든 유효하다.  
특정 도구의 사용법이 아니라, 
AI와 협업하는 근본적인 접근 방식을 다루기 때문이다.


⭐**AI Fluency: 4D Framework**
- Delegation (위임)
	- Problem Awareness (문제 인식)
	- Platform Awareness (플랫폼 인식
	- Task Delegation (작업 위임)
- Description (기술)
	- Product Description (결과물 기술)
	- Process Description (과정 기술)
	- Performance Description (행동 기술)
- Discernment (분별)
	- Product Discernment (결과물 분별)
	- Process Discernment (과정 분별)
	- Performance Discernment (행동 분별)
- Diligence (책임)
	- Creation Diligence (생성 책임)
	- Transparency Diligence (투명성 책임)
	- Deployment Diligence (배포 책임)


⭐**효과적인 프롬프트 엔지니어링 기법**
- 맥락을 제공하라 (Provide Context)
- 좋은 예시를 보여줘라 (Show Examples)
- 출력 조건을 명시하라 (Specify Output Constraints)
- 복잡한 작업을 단계로 나눠라 (Break Complex Tasks into Steps)
- 먼저 생각하도록 요청하라 (Ask It to Think First)
- AI의 역할을 정의하라 (Define the AI's Role)

---


출처: [AI Fluency: Framework & Foundations](https://anthropic.skilljar.com/ai-fluency-framework-foundations)
