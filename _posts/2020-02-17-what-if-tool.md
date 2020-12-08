---
layout: post
title:  "What If Tool 논문 리뷰 및 사용 방법?"
subtitle: "What If Tool 논문 리뷰 및 사용 방법"
categories: mlops
tags: feature
comments: true
---

- ~~~ 도구인 What If Tool(WIT) 논문 리뷰 및 사용 방법에 대해 작성한 글입니다



---

- https://www.groundai.com/project/the-what-if-tool-interactive-probing-of-machine-learning-models/1 여기서 보구 번역기 해도될듯


### What If Tool이란?
---


머신러닝 시스템을 개발하고 deploy하는 큰 문제는 input의 다양함에 따라 성능이 어떻게 되는지 이해하는지가 중요함. 이 도전 때문에 What If Tool을 만들었고, 코딩을 최소화하고 데이터를 조사하고, 시각화하고, ML 시스템을 분석한다

What if Tool을 사용하면 실무자는 가상의 상황에서 성능을 테스트하고 다양한 데이터 feature의 중요성을 분석해 여러 모델 및 input 데이터 하위 집합에서 모델 동작을 시각화할 수 있음

실무자들은 ML 공정성(fairness) 지표에 따라 시스템을 측정할 수 있음. 도구의 디자인을 설명하고 다른 조직의 실제 사용량에 대해 보고함

### 1. Introduction

머신러닝 작업에서 그들의 모델 성능이 좋은지 나쁜지 이해하고 싶어함.

이 데이터 포인트가 내 예측에 얼마나 변화를 주는가? 내 모델은 여러 그룹에서 다양한 퍼포먼스를 내는지? 

WIT 툴은 모델을 이해할 수 있는 인터랙티브한 도구임

도구를 사용하기 위해서 트레인 모델과 샘플 데이터셋만 있으면 됨. Tool은 Tensorboard의 일부로 사용할 수 있고, Jupyter나 Colab에서 사용할 수 있음

WIT는 iterative what if 탐색을 제공한다. 

유저는 counterfactual(만약 ~ 다면) 추론을 하고 dicision boundaries를 조사하고, 데이터 포인트에 대한 일반적인 변경 사항이 예측에 미치는 영향을 탐색해 모델이 어떻게 동작하는지 시뮬레이션 할 수 있음

입력 데이터 및 모델 성능의 유연한 시각화를 지원하고 사용자가 다른 관점으로 볼 수 있도록 해줌

이런 시각화의 중요한 motivation은 데이터 세트의 다른 feature를 조합해 조각화하는 것임. (여러 feature의 조합)

intersectional analysis, 교차 분석이라 부르고 이런 분석은 모델의 fairness 조사와 관련된 문제를 이해하는데 중요함

WIT는 로컬(단일 데이터 포인트에 대한 decision 분석) 및 전체(전체에 대한 모델 동작 이해)를 모두 지원함. 또한 다양한 데이터 및 모델 유형을 지원함

이 paper에선 도구의 디자인에 대해 설명하고 ML 시스템 분석에 실제로 적용되는 방법을 시나리오로 안내함. 사용자는 실제 시스템에 대한 놀라운 사실을 발견할 수 있었음. 결과는 가설 탐색을 지원하는 것이  ML 시스템의 동작을 이해하는 강력한 방법임을 제안함

### 2. Related Work
- WIT는 ML 모델 이해하는 프레임워크와 유연한 시각화 플랫폼 2개의 영역을 가지고 있음

### 2.1 Model understaging frameworks
- WIT는 black box의 범주에 속함
- 모델의 내부에 의존하지 않지만 사용자가 입력 및 출력만 probe할 수 있도록 설계됨
- 이 제한은 실제 제약 조건(모델 내부에 항상 액세스 할 수 있는 것은 아니라는)을 반영하지만 매우 일반적인 것을 의미함

다른 여러 시스템이 블랙박스 방식을 취함
- Uber의 Manifold는 WIT와 동일한 기능을 제공함. 예를 들어 두 모델을 비교할 수 있는 정교한 시각화 세트가 있음. 

<img src="https://www.dropbox.com/s/w8nyh8osh45nc5j/Screenshot%202020-02-17%2021.24.34.png?raw=1">

[EnsembleMatrix](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/12/EnsembleMatrix-final.pdf)는 앙상블을 구성하는 모델을 비교하기 위해 고안된 반면 [RuleMatrix](https://arxiv.org/abs/1807.06228)는 간단한 규칙으로 ML 모델을 설명하려고 시도함

<img src="https://www.dropbox.com/s/41wmac0ygvxc7n5/Screenshot%202020-02-17%2021.23.59.png?raw=1">


[ModelTracker](https://www.microsoft.com/en-us/research/publication/modeltracker-redesigning-performance-analysis-tools-for-machine-learning/)는 WIT와 유사한 시스템으로, 샘플 데이터에서 모델 동작을 풍부하게 시각화함

<img src="https://www.dropbox.com/s/opur8mgxixh4442/Screenshot%202020-02-17%2021.23.24.png?raw=1">

[Procpector](https://josuakrause.github.io/info/prospector.html)도 WIT와 유사한데, 블랙박스 모델을 눈으로 검사할 수 있는 도구. 특정 데이터 포인트를 드릴 다운해 feature 값을 조작해 모델 예측에 미치는 영향을 확인할 수 있음. Prospector는 단일 클래스 예측 모델에 대한 input feature의 직교성에 의존하지만, WIT는 잠재적으로 상효 연관되거나 혼동된 여러 기능에 대한 교차 분석을 제공함

<img src="https://www.dropbox.com/s/88qjx9ksjfbis3g/Screenshot%202020-02-17%2021.22.29.png?raw=1">

기타 유사한 시스템은 [GAMut](https://www.microsoft.com/en-us/research/uploads/prod/2019/01/19_gamut_chi.pdf)

<img src="https://www.dropbox.com/s/44gam0fgxcsvmb0/Screenshot%202020-02-17%2021.21.41.png?raw=1">

<img src="https://www.dropbox.com/s/ctdd3mqa9bycfok/Screenshot%202020-02-17%2021.22.01.png?raw=1">

툴의 중요한 motivation은 모델 이해와 사전 시각화 작업을 일반화하려는 것임
WIT의 주요 기능은 모델에서 mL 공정성 메트릭을 계산할 수 있음. IBM Ai Fairness 360, Audit AI, GAMut 도 유사한 기능을 제공함. 인터랙티브하게 최적화 과정을 적용해 사후 훈련 분류 임계값을 조정해 메트릭을 향상시킴


### 2.2 Flexibla visualization platform
- WIT는 DAtapoint Editor tab이 있음. 이 부분은 Facets Dive으로 구성됨. 이걸 사용하면 여러 속성의 교차를 탐색해 input 데이터와 모델 결과의 사용자가 원하는대로 볼 수 있음

X축, Y축 색상에 대한 인코딩을 빠르게 보여주는 것은 tableau에서 제공하는 일부 기능의 단순한 버전이고, 동일한 기능을 부드러운 애니메이션을 사용해 사용자가 인코딩간 전환을 이해하는데 도움이 되는 Micrsoft pivot tool도 있음

Facets Dive는 local에 의존해 inmemory storage와 계산 기반으로 함. 민감한 데이터를 보호할 수 있음. 액세스가 제한되어 분석을 위해 원격 서버로 전송하는 것은 불가능함. 원활한 탐색도 가능

### 3. BACKGROUND AND OVERALL DESIGN
#### 3.1 User Needs
- 1) 최소한의 코드로 다양한 가설을 테스트하고 싶음
- 2) 시각화를 모델 이해를 위한 매개체로 사용하고 싶음
- 3) 모드의 내부 작업에 액세스하지 않고 가상 테스트
	- WIT는 모델을 블랙박스로 다루어 "나이의 가치를 높이는 것이 모델의 예측 점수에 어떤 영향을 미칠까?" 또는 "데이터 포인트에서 다른 결과를 위해 무엇을 변경해야 하는가?"와 같은 질문에 답하기 위해 가상의 엔드투엔드 모델 행동에 대한 설명을 생성하도록 도와야 한다. 가설로 사용자는 하나 이상의 지정된 치수를 따라 데이터 지점의 동요에 대한 모델 성능을 테스트할 수 있다. 모델 내부를 이용할 수 없다면, 가설을 사용하여 생성된 설명은 모델에 구애받지 않고 동요에 일반화된다. 이는 설명과 표현의 유연성을 증가시킨다. 특히 평가되는 모델이 매우 복잡하기 때문에 의미 있게 해석할 수 없는 경우 [27]. 가설은 새로운 추측과 What-If 시나리오에 대한 모델 추론 결과의 조건 테스트 시 특히 강력하다.
- 4) 모델 성능에 대한 탐색적 교차 분석 수행
- 사용자는 종종 모형이 예기치 않게 수행되는 데이터의 하위 집합에 관심을 갖는다. 예를 들어, Buolamwini와 Gebru[9]는 특징만 아닌 교차로 부분군의 정확도를 조사하였고, 상업적으로 이용할 수 있는 이미지 분류기 3개가 시험 데이터에서 어두운 여성 부분군에서 가장 낮은 성능을 나타냈다고 밝혔다. 모델이 하위 그룹에서 높은 정확도를 달성하더라도, 잘못된 양의 비율과 거짓된 음의 비율은 크게 달라서 실제 결과를 초래할 수 있다[7]. 이러한 하위 집합을 정의하는 방법은 여러 가지가 있으므로 탐색 데이터 분석을 위한 시각화는 입력 데이터 유형, 모델 작업 및 최적화 전략에 대한 유연성, 확장성 및 사용자 지정을 유지해야 한다. 인스턴스(instance), 형상(feature), 부분 집합(subset) 및 결과 수준의 보완적인 뷰는 동일한 데이터 집합에서 여러 모델의 성능을 비교할 때 특히 통찰력이 뛰어나다.
- 5) 여러 모델에 대한 잠재적 성능 개선 평가
- 모델 개발에서는 분류 임계값을 변경하는 등 변화의 영향을 추적하기 어려울 수 있다. 예를 들어, 인구통계학적 패리티에 대한 임계값을 최적화하면 모든 그룹이 "유익한" 분류의 동일한 부분을 받도록 보장할 수 있으며, 반면 진정한 양의 비율이 낮은 하위 그룹은 불리하게 작용할 수 있다[13]. 훈련 데이터나 모델 하이퍼파라미터의 성능을 향상시키기 위해 변경하기 전에 다양한 비용에 대한 다양한 최적화 전략을 현지 및 전 세계적으로 테스트하고자 할 수 있다. 사용자는 분석을 오프라인으로 전환할 필요 없이 소규모 데이터 세트의 탐색 공간에서 바람직하지 않은 모델 동작을 완화하는 전략을 테스트하여 모델 성능을 대화형으로 디버그할 수 있어야 한다. 사용자가 모델의 변경을 시험하는 밀접하게 관련된 방법은 "제어" 모델의 성능을 벤치마크의 "실험" 버전에 비교하는 것이다.



흠 모델 하나 만들어봤는데 잘 안되네.. OTL


- 논문
    - [https://arxiv.org/pdf/1907.04135.pdf](https://arxiv.org/pdf/1907.04135.pdf)
- Github
    - [https://github.com/PAIR-code/what-if-tool](https://github.com/PAIR-code/what-if-tool)
- 아직 xgboost, sklearn 같은 곳에 직접 할 순 없음. ML Engine을 사용해야 할듯
- 공식 홈페이지의 What if tool 설명
    - [https://www.tensorflow.org/tensorboard/what_if_tool](https://www.tensorflow.org/tensorboard/what_if_tool)
- ML Engine에서 What if Tool 사용하기
    - [https://cloud.google.com/ml-engine/docs/using-what-if-tool?hl=ko](https://cloud.google.com/ml-engine/docs/using-what-if-tool?hl=ko)
- Video
    - [https://www.youtube.com/watch?time_continue=6&v=hpYl8WLYeKo&feature=emb_title](https://www.youtube.com/watch?time_continue=6&v=hpYl8WLYeKo&feature=emb_title)
    - 송호연님 영상
        - [https://www.youtube.com/watch?v=ZAS2FwlLTNE](https://www.youtube.com/watch?v=ZAS2FwlLTNE)
- Notebook에서 사용하는 예제
    - [https://github.com/tensorflow/tensorboard/blob/master/tensorboard/plugins/interactive_inference/README.md#notebook-mode-details](https://github.com/tensorflow/tensorboard/blob/master/tensorboard/plugins/interactive_inference/README.md#notebook-mode-details)

- Google 블로그 글
    - [https://ai.googleblog.com/2018/09/the-what-if-tool-code-free-probing-of.html](https://ai.googleblog.com/2018/09/the-what-if-tool-code-free-probing-of.html)

- 블로그 글
    - [https://towardsdatascience.com/using-what-if-tool-to-investigate-machine-learning-models-913c7d4118f](https://towardsdatascience.com/using-what-if-tool-to-investigate-machine-learning-models-913c7d4118f)

- 모델 비교 Colab
    - [https://colab.research.google.com/github/PAIR-code/what-if-tool/blob/master/WIT_Toxicity_Text_Model_Comparison.ipynb](https://colab.research.google.com/github/PAIR-code/what-if-tool/blob/master/WIT_Toxicity_Text_Model_Comparison.ipynb)

- 예제
    - [https://pair-code.github.io/what-if-tool/uci.html](https://pair-code.github.io/what-if-tool/uci.html)

