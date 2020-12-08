---
layout: post
title:  "MLOps NYC19 Conference 정리"
subtitle: "MLOps NYC19 Conference 정리"
categories: mlops
tags: basic
comments: true
---

- [MLOps NYC19](https://www.mlopsnyc.com/) 영상을 정리한 글입니다
- 시청한 영상
	- MLOps in the Newsroom
	- Netflix Presents: A Human-Friendly Approach to MLOps
	- The Architecture That Powers Twitter’s Feature Store 
	- Serverless for ML Pipelines from A to Z
	- Deep Learning on Business Data at Uber
	- The Growth and Future of Kubeflow for ML
	- Stateless ML Pipelines: Achieve reproducibility and automation while simplifying the pipeline
- Training session은 영상이 없지만, [Review](https://toddmorrill.github.io/blog/2019/10/05/MLOps-tooling)를 통해 간접적으로 내용을 볼 수 있습니다

---


### MLOps in the Newsroom
- Information Platforms and the Rise of the Data Scientist(2009)라는 책에선 R / Hadoop을 사용했다고 하는데 NYTimes에선 TF / GCP를 사용함 
- NYTimes의 DS Software Stack
	- <img src="https://www.dropbox.com/s/yfoecbi8dttrq0r/Screenshot%202019-10-26%2021.03.48.png?raw=1">
- Deployment at The New York Times
	- <img src="https://www.dropbox.com/s/4nyham5sw4qsa5n/Screenshot%202019-10-26%2021.04.17.png?raw=1">  
- 뉴스
	- 20세기엔 church + state였다면
	- 21세기엔 church + state + data(데이터가 앞 2가지 요소에 영향을 미침)
- 모델링
	- Descriptive modeling
		- Readerscope 
		- 데이터를 설명하는 패턴을 찾음
		- 현재 발생하는 상황에 대해 더 정교하고 실시간 통찰력이 필요했음
		- 누가 무엇을 읽고 있는지? 그리고 어디에 있는가?
		- 마케터가 drill down으로 LA에서 무슨 일이 있는지 등을 알 수 있음
		- Contextual bandit
	- Predictive modeling
		- 누가 구독할 것인가, 누가 떠날 것인가를 예측(퍼널 단계 단계를 예측)
		- 리스크를 위해 성능과 해석 가능성을 중시
		- 광고주가 광고할 때 어떤 사람에게 효과가 있는지? 관련있는 context를 제시 => 우리 광고는 Travel 부분과 digital 세대에 영향을 많이 미칠 것이다 등
		- 텍스트 기반해서 Labeling(Inspired, Happiness, Sadness, Love 등)
	- Prescriptive modeling  
		- 추천을 어떻게 할 것인가
		- 톰슨 샘플링 & 밴딧 사용
		- [Modeling Social Data](http://modelingsocialdata.org/) 
- IDEA
	- <img src="https://www.dropbox.com/s/bkgiq5exkxlu5ti/Screenshot%202019-10-26%2021.51.26.png?raw=1">
	- best AI is AI + IRL
		- recpect for craft
		- recpect for collaborators  

---

### Netflix Presents: A Human-Friendly Approach to MLOps
- 넷플릭스는 출시 전에 매일 프로그램 시청자의 예상 크기(estimated size)를 알고 싶어함
- The Life of a Model
	- EDA
		- 프로젝트를 잡고, 노트북에서 잡음
		- correlation를 찾고, scatter plot을 그림
		- 2주 정도 진행
	- Prototyping
		- 다양한 실험을 하고, Feature 추가, 모델링 등등을 진행
		- 6~8주  
	- Productionalize
		- Ship Model to Production(v1)
		- Scale & Deploy
		- ETL / Feature Engineering / Model Traing / Model Hoasting / Batch Scoring / Live Scoring / Audits / Scheduling etc
- Metaflow
	- 빠른 프로토 타이핑을 위해 만듬
	- <img src="https://www.dropbox.com/s/m5oyy7p0tihcv7t/Screenshot%202019-10-26%2022.31.05.png?raw=1">
	- 코드의 구조는 next로 다음 행동을 지정할 수 있음
		- <img src="https://www.dropbox.com/s/4sed61hnbujgaen/Screenshot%202019-10-26%2022.34.40.png?raw=1">
	- <img src="https://www.dropbox.com/s/t3vqibm2tldcojc/Screenshot%202019-10-26%2022.35.50.png?raw=1">
	- 프로토타입 할 때 특정 부분만 안되면 resume 명령어로 다시 실행
		- <img src="https://www.dropbox.com/s/ijkufviq5ezc2gs/Screenshot%202019-10-26%2022.37.56.png?raw=1">  
	- 컴퓨팅 파워는 titus에게 말하면 됨
		- <img src="https://www.dropbox.com/s/lfkkt14rwtt2vmn/Screenshot%202019-10-26%2022.39.48.png?raw=1">
	- 분산 처리
		- <img src="https://www.dropbox.com/s/q84o74li5siiakg/Screenshot%202019-10-26%2022.40.55.png?raw=1">
- Production 배포시 Meson 사용, meson create
	 - [넷플릭스 테크 블로그 : Meson: Workflow Orchestration for Netflix Recommendations](https://medium.com/netflix-techblog/meson-workflow-orchestration-for-netflix-recommendations-fc932625c1d9)
	 - <img src="https://www.dropbox.com/s/acramereyt3w82o/Screenshot%202019-10-26%2022.42.16.png?raw=1">
- Real time Scoring
	- <img src="https://www.dropbox.com/s/69zaxl8beh7mlui/Screenshot%202019-10-26%2022.45.04.png?raw=1">
- 몇달이 지난 후
- The Life of a Model
	- Maintenance
		- 모델 유지하고 version 2 빌드
		- v1를 안전하게 복사
		- 새로운 feature를 추가해 진행 
- inspect & debug
	- <img src="https://www.dropbox.com/s/7c9bf6kdph8k5y5/Screenshot%202019-10-26%2022.48.50.png?raw=1">
- Pick up & iterate
	- 다른 사람의 실험도 돌릴 수 있음
		- <img src="https://www.dropbox.com/s/pazegy5n9i18waw/Screenshot%202019-10-26%2023.39.13.png?raw=1">   
	- 태그도 가능
		- <img src="https://www.dropbox.com/s/wbzhy9xhnzc9uq5/Screenshot%202019-10-26%2023.39.56.png?raw=1">
- Metaflow at scale
	- 도입 후 정말 많은 프로젝트가 생기고 있음
	- <img src="https://www.dropbox.com/s/j1rype7shji2hke/Screenshot%202019-10-26%2023.43.01.png?raw=1"> 
	- <img src="https://www.dropbox.com/s/n4weq63h8p9ryj0/Screenshot%202019-10-26%2023.43.19.png?raw=1">

---
	

### The Architecture That Powers Twitter’s Feature Store 
- 많은 사람들이 모델링하면 겹치는 Feature Engineering이 있음
	- 이미 많이 진행한 팀, 이제 막 머신러닝을 도입하려는 팀 등
	- 이미 진행한 팀꺼를 fork하거나 밑바닥부터 만들 수 있음
	- 전사에서 사용할 라이브러리 생성 
- Featre Store는 library
- 오.. 트위터껀 아니지만 Gojek이 만든 [feast](https://github.com/gojek/feast)가 있음
	- <img src="https://github.com/gojek/feast/raw/master/docs/architecture.png"> 
- Share
	- Feature Catalog
	- Succinct, declarative Definitions 
- Datasets
	- 온라인/오프라인 접근이 가능
	- 동영상에서 화질이 너무 낮아 알아보기 힘듬 ㅠ
	- 추가할 Feature를 정의 
- Offline Integration
	- 스칼라 사용
	- joinFeatures
- Online Integration
	- <img src="https://www.dropbox.com/s/5c9shgg9mt5n5br/Screenshot%202019-10-27%2010.54.54.png?raw=1">
- Feature Store Client
	- <img src="https://www.dropbox.com/s/7b3pewkkceqg8nj/Screenshot%202019-10-27%2010.55.45.png?raw=1">
- Strato
	- FeatureStore Client에서 Strato로 데이터 보내고, 캐싱하거나 DB에 넣거나 서비스에 쓰거나 하는듯
	- <img src="https://www.dropbox.com/s/ezwfvjwr6gn5uyn/Screenshot%202019-10-27%2010.56.26.png?raw=1">
	- <img src="https://www.dropbox.com/s/xyeseqjs45ynx8g/Screenshot%202019-10-27%2010.57.01.png?raw=1">
- 영상은 10분만에 끝남. 딱히 인상 깊진 않음    

---

### Serverless for ML Pipelines from A to Z
- Code / Model Development is Just the First Step
	- <img src="https://www.dropbox.com/s/2xny5vio2f6ysz0/Screenshot%202019-10-27%2010.59.00.png?raw=1">
- 파이프라인 예시
	- Weather 정보도 추가
	- 여기서도 Feature Store란 단어가 나옴
	- <img src="https://www.dropbox.com/s/klajbcfhxu0ld49/Screenshot%202019-10-27%2010.59.46.png?raw=1">  
- Nuclio를 사용해 ETL과 Streaming을 가속
	- [Nuclio Github](https://github.com/nuclio/nuclio)
	- <img src="https://www.dropbox.com/s/coijvb4sg66zaj8/Screenshot%202019-10-27%2011.02.59.png?raw=1">  
- Nuclio를 사용해 Serving
	- [Nuclio Jupyter Github](https://github.com/nuclio/nuclio-jupyter)
	- <img src="https://www.dropbox.com/s/firi83gflx0a7pc/Screenshot%202019-10-27%2011.05.01.png?raw=1"> 
- Buidling ML Pipelines From (Serverless) Functions
	- Feature Store가 있군!
	- <img src="https://www.dropbox.com/s/yg0rxoxfj9aifiz/Screenshot%202019-10-27%2011.06.45.png?raw=1"> 
- Demo
	- KFServing을 쓰는듯
	- <img src="https://www.dropbox.com/s/qvfh1ym7co0cfch/Screenshot%202019-10-27%2011.12.05.png?raw=1"> 




### Deep Learning on Business Data at Uber
- 왜 딥러닝인가?
	- 기존에 사용하던 알고리즘보다 딥러닝이 더 좋은 성능을 보이고 있음
	- 특정 도메인에선 압도적인 성능(vision)
	- 기존에 사용하던 트리 모델과 결합해 하이브리드 모델을 만듬
- 딥러닝 In Uber
	- <img src="https://www.dropbox.com/s/f4l210de6l1ds2t/Screenshot%202019-10-27%2011.24.35.png?raw=1">
	- <img src="https://www.dropbox.com/s/46q5qf1pf24fc26/Screenshot%202019-10-27%2011.25.07.png?raw=1">
	- <img src="https://www.dropbox.com/s/1cbo9ksvjys9oit/Screenshot%202019-10-27%2011.26.00.png?raw=1">
- 이런 시스템을 어떻게 구현할까?
	- Option A : TFX
	- Option B : Apache Spark (이걸 사용)
		- Powerful ETL
		- Easy integration with XGBoost
		- 이미 스파크를 사용하는 시스템이 있었음
- 1) Feature Store
	- Real time과 Batch를 통합 
	- <img src="https://www.dropbox.com/s/5qxi8pl7bu24d4t/Screenshot%202019-10-27%2011.27.31.png?raw=1">   
- 2) Model Training
	- Apache Spark에서 딥러닝을 어떻게 합칠까?
	- Preprocessing
		- <img src="https://www.dropbox.com/s/4si8hp8jyhw59u7/Screenshot%202019-10-27%2011.29.15.png?raw=1">
	- SparkML Pipelines
		- Estimator, Trnasformer, Pipeline
		- <img src="https://www.dropbox.com/s/1mklfr4xpl7okle/Screenshot%202019-10-27%2011.30.11.png?raw=1">
	- Distributed Training
		- <img src="https://www.dropbox.com/s/aaz7ryhgd4udr6a/Screenshot%202019-10-27%2011.31.09.png?raw=1">  
	- Petastorm : 딥러닝 학습을 위한 데이터 접근
		- Parquet
		- <img src="https://www.dropbox.com/s/qwevz08n6guz6u0/Screenshot%202019-10-27%2011.31.46.png?raw=1">
	- End-to-end Training Architecture
		- [Petastorm Blog](http://eng.uber.com/petastorm)
		- <img src="https://www.dropbox.com/s/0yj3we2us2pkfrb/Screenshot%202019-10-27%2011.33.02.png?raw=1"> 
- 3) Prediction Service
	- 자바와 딥러닝 프레임워크를 같이 실행시켜야 함
	- <img src="https://www.dropbox.com/s/j4akgulln6vajtj/Screenshot%202019-10-27%2011.34.16.png?raw=1">
- 4) Authoring
	- 데이터 사이언티스트들은 쥬피터 노트북을 좋아함
	- 하나의 노트북에서 아이데이션, 학습, 평가, 딥러닝 모델 배포 등을 할 수 있을까?
	- Data Access
		- <img src="https://www.dropbox.com/s/rvn5h7gj6jl9ifs/Screenshot%202019-10-27%2011.37.30.png?raw=1">
	- Data Preparation
		- <img src="https://www.dropbox.com/s/mmp1krwr8g94j5h/Screenshot%202019-10-27%2011.39.06.png?raw=1">
	- Model Construction
		- <img src="https://www.dropbox.com/s/7vryec8adgpjz8y/Screenshot%202019-10-27%2011.39.23.png?raw=1">
	- Train the Model
		- <img src="https://www.dropbox.com/s/33iiyucyv06e3m2/Screenshot%202019-10-27%2011.39.39.png?raw=1">
	- Deploy the Model
		- <img src="https://www.dropbox.com/s/grjfymmq0yx14f6/Screenshot%202019-10-27%2011.40.04.png?raw=1">  
- 5) Don't know Deep Learning?
	- 딥러닝을 몰라도 Ludwig 활용
	- [Ludwig 홈페이지](https://uber.github.io/ludwig/), [Ludwig Github](https://github.com/uber/ludwig)
	- <img src="https://www.dropbox.com/s/3y69bx9smagaj5o/Screenshot%202019-10-27%2011.40.24.png?raw=1">
- Recap(요약)
	- 거대한 데이터셋을 가진 회사에서 딥러닝을 사용하면 powerful한 모델을 만들 수 있음
	- Uber의 딥러닝 시스템 아키텍쳐와 E2E DL 파이프라인을 정의하기 위해 노트북 친화적으로 만든 API를 떠올리기
	- Apache Spark, Horovod, Petastorm을 사용함
	- Ludwig에서 코드 없이 딥러닝 모델을 만듬      	
---

### The Growth and Future of Kubeflow for ML
- ML 구성은 매우 복잡함(이거 진짜 모든 MLOps 세미나에서 나오는듯...ㅋㅋㅋ)
- MLOps Team이 당면한 문제
	- <img src="https://www.dropbox.com/s/3jdmkhn65b1h3k9/Screenshot%202019-10-27%2011.46.29.png?raw=1">
	- 10배 넘게 생산성을 가지도록 하는 방법은?
- Kubeflow
	- <img src="https://www.dropbox.com/s/6bcohn3vivnowwi/Screenshot%202019-10-27%2011.48.07.png?raw=1">
- Vibrant(활기찬) Ecosystem of Kubeflow
	- 엄청 활발하게 발전되고 있는 에코시스템
	- <img src="https://www.dropbox.com/s/tl0k49uafd42apj/Screenshot%202019-10-27%2011.49.29.png?raw=1"> 
- Deploy & Manage
	- Composable, Scalable, Portable
	- <img src="https://www.dropbox.com/s/9b8vdb8yen8ke3j/Screenshot%202019-10-27%2011.50.55.png?raw=1">
- 쿠버네티스가 MLOps에 좋은 이유
	- <img src="https://www.dropbox.com/s/3bhobpdpr9wp49k/Screenshot%202019-10-27%2011.52.07.png?raw=1">
- Kubeflow 0.6
	- Metadata
		- 인공물을 저장하고 스키마 정의 가능
	- Deployment
		- Kustomize가 ksonnet을 대체함
	- Multi user support
	- Pipelines
		- API와 UI 개선
	- <img src="https://www.dropbox.com/s/480fza1vdjmyjjy/Screenshot%202019-10-27%2011.52.51.png?raw=1">
- Anthos가 MLOps에 좋은 이유
	- [Google Developer의 Anthos 소개 글](https://developers-kr.googleblog.com/2019/06/new-platform-for-managing-applications-in-todays-multi-cloud-world.html) 
	- 흠 Anthos는 규모에 따라 이득일지 아닐지가 나뉠듯..?
	- <img src="https://www.dropbox.com/s/wegm6aeiowf6do0/Screenshot%202019-10-27%2011.55.41.png?raw=1"> 
	- <img src="https://www.dropbox.com/s/8gw1yyklg6hwhcq/Screenshot%202019-10-27%2012.05.37.png?raw=1">    


### Stateless ML Pipelines: Achieve reproducibility and automation while simplifying the pipeline
- 나이키
- 데이터 사이언티스트와 팀은 모델 파이프라인부터 프러덕션까지 할 수 있어야 하고, 모델의 전체 lifecycle을 알아야 함
- What Stateless Pipelines Changed
	- Airflow 사용 -> 실패하면 알람
	- 이제 모델 파이프라인을 몇분만에 만듬
	- <img src="https://www.dropbox.com/s/ilpknrtapcmzbyz/Screenshot%202019-10-27%2012.09.48.png?raw=1">
- Lifecycle of an ML Project
	- CI/CD 어떻게 하는지 궁금 
	- <img src="https://www.dropbox.com/s/z0cls1c1z6vp6u6/Screenshot%202019-10-27%2012.13.14.png?raw=1"> 
- 파이프라인
	- 기존 에어플로 설정을 더 간소화함
	- <img src="https://www.dropbox.com/s/2ifepht6idg6ab8/Screenshot%202019-10-27%2012.18.07.png?raw=1">
	- <img src="https://www.dropbox.com/s/q1kj72aiqbbgca5/Screenshot%202019-10-27%2012.19.38.png?raw=1">
	- 아래는 뭐로 한건지 모르겠음. dsp?
	- <img src="https://www.dropbox.com/s/kuajdntxeh3yhws/Screenshot%202019-10-27%2012.20.11.png?raw=1">
- 모델 실행 파이프라인 예시
	- <img src="https://www.dropbox.com/s/bw8aeyd1pe4rwm7/Screenshot%202019-10-27%2012.22.27.png?raw=1">  
- Providing Paths
	- Dev / Test / Prod가 저장되는 폴더가 다름
	- <img src="https://www.dropbox.com/s/ixhpj53ic8e55uf/Screenshot%202019-10-27%2012.23.54.png?raw=1">
- Metrics
	- <img src="https://www.dropbox.com/s/j652btdah2p6d50/Screenshot%202019-10-27%2012.24.16.png?raw=1">
- Standard CI/CD pipeline
	- 젠킨스파일을 가짐
	- <img src="https://www.dropbox.com/s/01xvl0l7vf8adsh/Screenshot%202019-10-27%2012.24.43.png?raw=1">
- Result
	- <img src="https://www.dropbox.com/s/vvkydjq0dgpiivi/Screenshot%202019-10-27%2012.25.03.png?raw=1">    


---


### Reference
- [MLOps NYC 2019 Youtube](https://www.youtube.com/playlist?list=PLH8M0UOY0uy6d_n3vEQe6J_gRBUrISF9m)


