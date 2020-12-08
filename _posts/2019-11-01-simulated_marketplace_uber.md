---
layout: post
title: "Gaining Insights in a Simulated Marketplace with Machine Learning at Uber 번역"
subtitle: "Gaining Insights in a Simulated Marketplace with Machine Learning at Uber 번역"
categories: data
tags: mobility simulation
comments: true
---


- Uber Tech blog의 [Gaining Insights in a Simulated Marketplace with Machine Learning at Uber](https://eng.uber.com/simulated-marketplace/) 번역 & 정리입니다
- 개인 학습 목적으로 번역했으며, 오역이나 의역이 있을 수 있습니다 :)
	- 글을 보다 내용과 관련된 링크를 찾아 추가한 부분도 있습니다

<br />
<br />

---


- Uber에선 Marketplace 알고리즘을 사용해 드라이버와 라이더를 연결함
	- 알고리즘이 전 세계적으로 출시되기 전에 Uber는 알고리즘을 완벽하게 테스트하고 평가해 핵심 [Marketplace](https://marketplace.uber.com/) 원칙에 맞는 최적의 사용자 경험을 만듬

- Uber Marketplace Simulation팀은 제품 테스트를 더욱 안전하고 쉽게 실행할 수 있도록 현실 세계에 있을 상황을 모방한 driver와 rider을 시뮬레이션할 수 있는 플랫폼을 구축
	- Uber marketplace 엔지니어와 데이터 과학자는 에이전트 기반의 discrete event 시뮬레이터를 활용해 리스크가 없는 환경에서 새로운 기능과 가설을 신속하게 프로토 타이핑하고 테스트할 수 있음
- 시뮬레이터는 이력(historical) 데이터를 활용해 marketplace 서비스 및 사용자 행동 모델(user behavior model)을 만듬
	- Marketplace 서비스는 이런 인사이트를 활용해 Dispatch 결정을 내림
	- 사용자 행동 모델은 simulation context를 사용해 trip 취소나 navigation 선택 같은 rider와 driver의 행동을 결정함
- Uber Marketplace Simulation팀은 모델 배포와 학습을 더 쉽고 정확하게 할 수 있도록 시뮬레이션 플랫폼에서 모델을 완벽하게 빌드하고, 학습, serving할 수 있는 머신러닝 프레임워크를 구축함
	- ML은 시뮬레이션 정확도와 현실성을 향상시키기 때문에 시뮬레이션 플랫폼에서 사용자 행동을 예측할 때 중요한 역할을 함
	- 실제로 플랫폼에서 가장 시뮬레이션된 사용자 행동은 모델 기반이며 집계된 과거의 익명 데이터로 훈련됨 
	- 시뮬레이션은 ML 모델 개발 및 개선에도 도움이 됨
	- 새로운 모델은 시뮬레이션 플랫폼에 탑재되어 실제 서비스에 적용되기 전에 신속하게 반복할 수 있음
- ML 모델 학습 프레임워크를 설계해 사용자가 시뮬레이션 플랫폼에서 모델을 신속하게 빌드, 배포하고 시뮬레이션을 통해 ML 모델을 개선할 수 있도록 함

<br />
<br />


---

### Simulation platform machine learning framework
- ML은 시뮬레이션 플랫폼에서 점점 중요한 기능의 핵심에 있음
	- ML 프레임워크를 시뮬레이션 플랫폼에 도입하기 전에 ML 개발, 학습, serving은 주로 재사용할 수 없는 임시 솔루션(ad-hoc)으로 구성됨
	- 예를 들어 ML 개발자는 시뮬레이터에서 ML 모델을 직접 구현하고 시뮬레이터가 실행될 때 모델을 학습함
- 이런 일회성 솔루션은 개발자가 여러 모델을 구현하고 모델 버전을 추가하며 시뮬레이터 내에서 복잡성을 누적시킴
	- 시뮬레이터를 유지 관리하기 어려워졌고, 모델을 학습하기 위해 많은 RAM과 CPU를 사용해 시뮬레이터 성능이 저하됨
	- 이런 유형의 시나리오에선 custom top-to-bottom 학습과 serving 코드를 커스텀한 top-to-bottom에 가질 수 없음
- 시뮬레이션 팀은 시뮬레이션 플랫폼에서 실행되는 대부분의 ML 학습, workload를 효율적으로 재사용 가능한 단일 프레임워크를 만듬. 프레임워크는 오픈소스와 우버에서 만든 컴포넌트들을 섞어 만들었음. 4개의 레이어에서 작동함
- Layer
	- 시뮬레이션 플랫폼에서 ML 모델을 구성하는 API 및 module을 구성하는 ML 라이브러리
	- 모델을 학습하고 binary 파일로 저장하는 자동 학습 파이프라인
	- 모든 모델의 메타데이터를 관리하는 백엔드 시스템
	- 선택한 모델을 시뮬레이션에 load하는 시뮬레이터의 중앙 모듈

- 시뮬레이션 ML 프레임워크 학습 workflow에서 학습 파이프라인은 ML 라이브러리를 가져오고 Apaceh Spark를 사용해 Apache Hive의 데이터를 가져오고 처리하고 과거 시계열 데이터를 기반으로 모델을 자동으로 학습함
	- 파이프라인이 학습이 끝나면, 시뮬레이션 데이터베이스 및 checkpoint model instance가 생성됨. 그 후, Uber의 storage service에 binary file로 저장됨 
- 시뮬레이션 백엔드 서비스는 시뮬레이션 생성 요청을 받으면 유저의 셋팅을 기반으로 관련 ML 모델의 메타 데이터를 사용해 모델 checkpoint를 local disk로 저장함
	- 그 후, 시뮬레이션 백엔드 서비스는 model factory가 있는 시뮬레이터를 실행함
	- 그 후, 모델 팩토리는 Python ML 라이브러리를 가져와 로컬 디스크에서 checkpoint를 스캔해 관련 모델을 인스턴스화하고, 인스턴스를 시뮬레이션 core flow로 출력함
- ML 프레임워크의 Train / Serving Workflow
	- <img src="https://www.dropbox.com/s/znokglqgothuitb/Screenshot%202019-11-01%2022.02.11.png?raw=1">
		- 그림 1. Automatic Training Pieplein(가운데)은 Spark(위)를 사용해 Hive에서 raw 데이터를 가져오고 simulation ML 모델(왼쪽)을 사용해 Storage 서비스(오른쪽) 및 SImulation Database(아래)에 데이터를 저장함
	- <img src="https://www.dropbox.com/s/8jbl93mxdcn2ioa/Screenshot%202019-11-01%2022.05.02.png?raw=1">
		- 그림 2. 시뮬레이션을 생성하기 위해 먼저 시뮬레이션 request를 보냄(왼쪽 상단) 그 후 백엔드 서비스 모델 메타 데이터를 가져와 데이터베이스에 입력함. 그 후, 데이터베이스는 메타 데이터를 기반으로 checkpoint를 저장소에서 가져오고 디스크로 다운로드함. 그 후, checkpoint는 시뮬레이텨에서 Simulation ML Model(상단)과 결합해 Model Factory가 모델을 인스턴스화함

		
- 이 프레임워크은 Training과 Serving workflow를 분리함
	- 이 변화는 개발자가 지속적으로 모델을 개선시킬 때 필요한 유연한 모델을 제공함
	- 이 시스템에서 개발자가 사용자 행동 모델을 변경하려면 Python ML 라이브러리에서 구현물을 업데이트하면 됨
	- 그 다음 자동으로 train 파이프라인을 트리거하고 최신 모델을 모든 시뮬레이터에 추가함
- 시뮬레이터에서 Training workflow를 제거해 시뮬레이터의 복잡성을 단순화해 RAM, CPU 사용량을 크게 개선함
	- 이 변경은 또한 시뮬레이터의 처리량을 증가시킴
	- 이런 간소화가 하드웨어 리소스를 보존하고 시스템의 효율성을 향상시킴
- Marketplace Simulation 팀은 이 프레임워크를 사용해 표준화된 방식으로 여러 사용자 행동 및 기타 ML 모델을 시뮬레이션 플랫폼에 통합함

<br />
<br />


---


### How supply movement models improve marketplace simulation
- [최근 연구](https://dl.acm.org/citation.cfm?id=3186924)에서 제안한 것처럼 ride-sharing 시물레이션에서 운전자의 움직임은 시뮬레이션의 정확도와 리얼리즘에 중요한 factor임
	- 시뮬레이션 플랫폼에서 실험적인 marketplace 알고리즘은 실제 결과와 다른 결과를 가져옴
	- 예를 들어 시뮬레이션의 드라이버는 현실과 다른 rider와 매칭될 수 있음
	- 이 경우에 시뮬레이터는 경로, 주행 시간 및 하차 위치를 시뮬레이션하기 위해 historical driver 이동 데이터를 재사용할 수 없음
	- 이 문제를 해결하기 위해 운전자의 움직임을 정확하게 시뮬레이션하는 모델을 구축해야 했음
	- 적절한 movement 모델을 사용하면 라이더 행동 모델(예 : 라이더 취소 모델)을 향상시키고 매칭 및 가격 알고리즘에서 노이즈를 줄일 수 있음
- 시뮬레이션 된 세계는 제한된 과거 데이터에서 실행되고 교통 정보, 날씨, 지리적 데이터와 같이 사용자 행동에 영향을 줄 수 있는 많은 요소가 없기 때문에 운전자의 움직임을 정확하게 시뮬레이션하는 것은 매우 어려운 작업임
- 다행히 대부분의 marketplace 알고리즘에서 단일 운전자의 움직임은 알고리즘 결과에 큰 영향을 미치지 않음
	- 이런 알고리즘들은 특히 rider-driver 매칭이나 가격 알고리즘들은 개별 운전자의 움직임보다 집계된(aggregated) 드라이버 분포를 활용함
	- 이런 이유로 모든 운전자의 움직임을 완벽하게 시뮬레이션하지 않고 운전자 분포를 정확하게 시뮬레이션하는 모델을 구축함

- 시뮬레이션된 세계에서 목표를 달성하기 위해 online driver movement 동작을 on-trip과 off-trip으로 나눔
	- 2가지 state에서 다른 드라이버 행동을 예측하는 하이브리드 모델을 만들어 uber의 marketplace 수요를 더 잘 예측함 
	- <img src="https://www.dropbox.com/s/zcz866zxap94e0v/Screenshot%202019-11-01%2022.26.54.png?raw=1">
		- 그림 3. Driver movement 하이브리드 모델은 시뮬레이션하는 운전자 요청으로 시작해 예/아니오 분기를 통해 결론에 도달하고, 운전자 정보를 라우팅 엔진에 보내고, route를 통해 speed와 이동을 측정함
 

- On-trip 드라이버를 위한 룰 베이스 모델을 적용함
	- 시뮬레이션된 driver과 trip을 수락하면 모델은 라우팅 엔진을 사용해 경로를 따라 운전자를 미리 정해진 목적지로 안내함
- Off-trip 드라이버 분포를 시뮬레이션하는 것은 매우 어려움
	- 전에 언급한 것처럼, 시뮬레이션된 세상이 historical driver movement를 재현할 수 없음
	- 이런 이유로 ML 모델을 사용해 집계된 off-trip 드라이버의 이동을 예측함
	- 생산적인 알고리즘을 사용해 이 모델은 드라이버 분포를 과거 분포에 가깝게 시뮬레이션함
	- Off-trip 드라이버 분포를 정확하게 시뮬레이션하는 목표를 달성하기 위해 트리 기반 확률 모델을 훈련함
	- <img src="https://www.dropbox.com/s/5l3worskp69xsfb/Screenshot%202019-11-01%2022.33.22.png?raw=1">
		- 그림 4. off-trip 드라이버 분포 시뮬레이션을 위한 stochastic 모델의 decision tree 중 하나


- 시뮬레이션은 off-trip 드라이버의 목적지를 예측하려고 할 때, 그림 4와 같이 드라이버 정보(위치, 타임스탬프 등)를 사용해 Tree 모델에서 관련된 리프 노드를 가져옴
	- 리프 노드에는 전이 행렬(transition)이 포함됨
		- 시뮬레이션된 운전자의 미래 움직임을 예측하는 매트릭스  
	- 시뮬레이션 플랫폼에서 육각형 계층적 공간 인덱스인 H3을 사용해 지구의 영역을 식별 가능한 그리드 셀로 분할함
	- 이런 식별 가능한 그리드 셀을 사용해 아래 그림 5와 같이 전이 매트릭스를 정의함
	- <img src="https://www.dropbox.com/s/mh3ypx5lup07qfn/Screenshot%202019-11-01%2022.35.52.png?raw=1">
		- 그림 5. 이 표는 지구상의 위치를 나타내는 다양한 그리드 셀의 확률값을 보여줌 

- Transition 행렬의 값은 드라이버가 현재 그리드 셀 X에 있는 경우 드라이버가 그리드 셀 Y로 이동할 확률을 나타냄
- 이런 transition matrix를 사용해 시뮬레이션은 open 드라이버가 다음 셀로 이동할 가능성이 있는 셀을 예측할 수 있음
	- 그런 다음 시뮬레이션은 해당 셀 내부의 위치를 임의로 선택해 open 드라이버의 목적지로 할당함. 아래 그림 6은 이 프로세스를 보여줌
	- <img src="https://www.dropbox.com/s/42tmanj53f7y3rx/Screenshot%202019-11-01%2022.39.13.png?raw=1">
		- 그림 6. 이 Flow 차트는 리프 노드에서 시작하고 맵을 사용해 식별 가능한 그리드 셀을 가져와 드라이버를 찾음. 시뮬레이션이 전이 행렬로 드라이버 이동 확률을 계산하는 방법을 보여줌(좌측 밑) 시뮬레이션에서 운전자의 목적지 위치를 선택하는 방법을 우측 지도에서 보여줌. 운전자 대상을 예측하기 위한 트리 기반 확률 모델임

- 시뮬레이터가 open driver의 목적지를 예측하면 운전자의 경로와 속도를 추정한 다음 운전자를 목적지로 옮김
	- on-trip 및 off-trip 드라이버의 주요 차이점은 open 드라이버가 목적지로 운전하는 동안 상태를 변경할 수 있음(offline/dispatchied)  
- Marketplace Simualtion Platform은 하이브리드 driver movement 모델을 사용해 실제 운전자 분포를 정확하게 근사함
	- 동일한 알고리즘을 사용해 분포를 실제 분포와 비교했음. 아래 그림 7은 피크 시간 드라이버의 분포를 보여줌
	- <img src="https://www.dropbox.com/s/uozqc6xi5tdtcx8/Screenshot%202019-11-01%2022.42.21.png?raw=1">
		- 그림 7. 왼쪽 지도는 실제 세계에서 운전자가 분포하는 영역이고, 오른쪽 지도는 시뮬레이션 결과. 근접함

<br />
<br />

---


### How simulation helps us experiment with matching algorithms
- Uber 플랫폼은 ETA, Routing, 사용자 위치, trip 선호도 및 가격을 포함한 다양한 요소에 최적화된 매칭 알고리즘을 사용해 rider와 driver를 실시간으로 연결함
- [최근 연구](https://arxiv.org/pdf/1302.6666.pdf)와 [강의 자료](http://www.columbia.edu/~cs2035/courses/ieor4405.S17/p14.pdf)에 따르면 ride-sharing 프로그램은 종종 large-scale 조합 알고리즘을 사용해 사용자 매칭을 최적화함
	- 그러나 사업을 다양화하며, 이 알고리즘은 더 많은 요소를 고려해야 함
	- 점점 더 복잡해지는 매칭을 수용하기 위해 머신러닝 모델을 최적화 알고리즘에 통합해 시뮬레이션 처리량을 높임
	- 추천 시스템과 maximum bipartite matching을 조합함
	- 아래 그림 8에 전체 workflow가 나옴
	- <img src="https://www.dropbox.com/s/6gcv6qh2r8okymj/Screenshot%202019-11-01%2022.49.57.png?raw=1">
		- 그림 8. 상단 절반은 추천 시스템을 나타내며 몇 단계를 거쳐 드라이버를 좁히고 순위를 매김. 추천 시스템의 순서는 각 라이더에 대해 모든 드라이버를 찾고, 드라이버 옵션을 수백으로 좁히고, 링크 생성, 드라이버 옵션을 수십으로 좁히고 10보다 적을 경우 순위를 매김. 이 프로세스가 완료되면 화살표는 권장 사항이 일치 단계로 이동함. 처음에 이 알고리즘은 여러 라이더를 단일 드라이버에 연결할 수 있지만 maximum bipartite 매칭 알고리즘을 사용해 개별 드라이버와 단일 라이더를 페어링함

		
- 매칭 솔루션에서 추천 시스템은 각 pari의 점수를 가진 라이더당 몇 명의 드라이버를 제안함
	- 추천 결과를 기반으로 가중치가 있는 bipartite graph를 만듬
	- 이 그래프는 한 명의 rider와 한 명의 driver를 매칭시킴
	- 이 문제를 효율적으로 해결하기 위해 maximum bipartite matching algorithm을 사용함
	- [이분 매칭(Bipartite Matching)](http://kks227.blog.me/220807541506) 
- rider와 driver 위치만 사용해 추천 시스템을 개발하는 것은 어려운 일임
	- 다른 맥락이 없으면 feature의 부족으로 협업 필터링, Factorization 머신, 딥 뉴럴넷을 사용하기 어려움
	- 이 문제를 해결하기 위해 대규모 소셜 네트워크 추천을 살펴봄
		- [Representation Learning on Graphs: Methods and Applications](https://arxiv.org/abs/1709.05584)   
		- [Parallel Clustered Low-Rank Approximation of Graphs and Its Application to Link Prediction](https://link.springer.com/chapter/10.1007/978-3-642-37658-0_6)
		- [Recommender Systems for Large-Scale Social Networks: A review of challenges and solutions](https://www.sciencedirect.com/science/article/pii/S0167739X17319684)

- 종합적인 연구를 한 후, 소셜 네트워크에서 구현된 솔루션을 사용해 추천 시스템을 만들기로 결정함
	- [Representation Learning on Graphs: Methods and Applications](https://arxiv.org/abs/1709.05584)
	- [The link prediction problem for social networks](https://dl.acm.org/citation.cfm?id=956972) 	
	- 그림 8에 표시된 것처럼 rider와 driver 위치를 기준으로 꼭지점을 갖는 그래프를 구성함
	- 운전자 ETA나 rider와 driver 사이의 거리 같은 규칙 기반 제약 조건을 사용해 그래프의 Edge를 생성함
	- 시뮬레이터가 그래프를 구성한 후, historical dispatch 결과를 기반으로 각 edge의 점수를 예측할 수 있음

- 그러나 각 node와 edg에 대한 정보는 위치와 경로만 포함하기 때문에 매우 제한적인 정보를 가지고 있음
	- [GraphceSAGE](http://snap.stanford.edu/graphsage/#motivation)와 같은 graph representation learning 방법에 영감을 받아, 아래 그림 9와 같이 이웃에서 정보를 집계해 각 node에 충분한 feature를 추출함
	- <img src="https://www.dropbox.com/s/k0tf4fo8roe8hc1/Screenshot%202019-11-01%2023.05.28.png?raw=1">
		- 그림 9. rider node의 외부 원은 이웃의 집계된 feature 정보를 사용해 더 작은 원 내의 rider node를 driver node로 이동시킴. 그런 다음 다시 집계되어 하나의 rider node로 좁아짐

- 이런 Feature는 각 노드에 대한 근처 네트워크 구조 정보를 반영함
	- 또한 continuouse feature를 normalized하고 모델을 일반화하기 위해 feature vector에 임베딩함
	- [Deep Neural Networks for YouTube Recommendations](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/45530.pdf)를 모방함
- 결국 각 쌍의 점수를 예측하기 위해 정착한 모델은 [DART : Gradient Boosting Tree with dropouts](https://arxiv.org/abs/1505.01866)
	- 학습 데이터의 상당 부분에서 음수 쌍(driver와 rider가 일치하지 않음)이 발생하기 때문에 모델을 훈련하기 전에 음수 상을 다운 샘플링하고 양수 쌍(driver와 rider가 일치한)을 샘플링함

- 모델을 학습한 후, 모델을 사용해 각 쌍의 점수를 기준으로 각 rider의 top-k potential driver를 예측함
	- 이 경우 예측 결과에 따르면, 각 rider는 여러 driver와 연결되며, driver는 여러 rider와 연결될 수 있음
	- 그런 다음 링크를 기반으로 bipartite graph를 구성하고 maximum bipartite matching algorithm을 적용해 bipartite matching 문제를 해결하고 최소 평균 드라이버 ETA를 달성함

<br />
<br />

	
---


### Moving forward
- ML 프레임워크를 사용해 ML 모델을 시뮬레이션 플랫폼에 점진적으로 통합해 시뮬레이션에서 더 많은 사용자 행동과 실험적인 marketplace 알고리즘, 기능을 테스트할 수 있음
	- 이 시스템은 Uber가 개발 프로세스를 가속화하고 안전하고 안정적인 transportation product를 제공할 수 있도록 도와줌
- 앞으로는 다음 기능을 통해 시뮬레이션 ML 프레임워크를 향상시킬 계획
	- Hyperparameter automatic tuning
		- ML 모델에는 learning rate, tree depth 같은 많은 하이퍼 파라미터가 포함됨
		- 이런 하이퍼 파라미터를 조정하면 시간이 많이 걸리고 오류가 발생하기 쉬움
		- 하이퍼 파라미터 튜닝 툴을 모델 학습 파이프라인에 통합해 모델 개발자의 생산성을 높이고 엔지니어의 작업을 더 쉽게 만들 계획
	- Online model performance monitoring
		- 시뮬레이션 플랫폼의 대부분 ML 모델은 시간 및 위치와 같은 환경적 요인에 직접 영향을 받음
		- 시간이 지나며 시뮬레이션의 환경(예 : 교통 패턴)이 변경되므로 모델 성능이 저하됨
		- 이런 변화에도 모델을 정확하게 유지하려면 새로운 데이터로 모델을 재학습해야 함
		- 온라인 모델 성능 모니터링 시스템은 model serving 퍼포먼스를 모니터링하고 성능이 특정 임계값보다 낮아지면 학습 파이프라인을 트리거할 수 있음
	- Distributed machine learning serving
		- Uber의 엔지니어는 더 많은 ML 모델을 시뮬레이션 플랫폼에 통합하고 있음
		- 현재 프레임워크에는 컴퓨팅 리소스 부족이 발생해 예측 대기 시간(latency for predictions)이 크게 증가함
		- 따라서 시뮬레이션 처리량이 악화됨
		- 고성능 분산 ML 서비스가 이 문제를 해결할 때 도움이 될 수 있음
	- Configurable machine learning model development 
		- 시뮬레이션 플랫폼의 대부분 ML 모델은 Data processing logic 및 기본 머신러닝 알고리즘과 같은 공통 component를 공유함
		- 대부분의 사용자를 위해 모델 개발 프로세스를 단순화하기 위해 configurations driven model 개발 프레임워크를 만들 예정
		- Input config를 기반으로 모델을 구성할 수 있음


