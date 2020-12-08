---
layout: post
title: "if kakao 2019 2일차 후기"
subtitle: "이프카카오 2019 2일차 후기"
categories: etc
tags: lecture
comments: true
---

- if kakao 2019 2일차 세션을 들으며 메모한 글입니다

---

### 금융사기 잡는 카카오뱅크의 데이터 사이언스
- 전상현님
- 금융사기를 어떻게 잡는지, 시도했던 것들을 소개
- 카카오뱅크의 데이터 사이언스 소개
	- Anomaly Detection, Topological, Network Analysis 등
	- 7월에 고객 1000만명 돌파
	- 2030세대 전체의 45%가 카카오뱅크 이용
	- 카카오택시 내역을 신용평가에 반영하고 있음
	- 은행 업무 중 발생하는 데이터 문제를 해결
		- 데이터에서 인상이트 찾기
			- 예 : 모임통장 이후 카뱅의 변화 분석
		- 기계학습/딥러닝 모형 개발
			- 보이스피싱 탐지 모형 개발
			- 이상거래 탐지 모형 개발    
- 금융사기와 FDS
	- 보이스피싱은 점점 늘고 있고, 금융사기도 지능화되고 있음
	- 스미싱, 보이스피싱, 파밍, 대포통장 등의 방법
	- FDS(이상금융거래 탐지 시스템)
		- 앱 원격 조종?
			- 기기 정보 / 앱 실행 정보 / 악성 앱 설치 여부 
		- 거래가 없던 계좌에 큰 금액 이체?  
			- 거래 내역 
- 카카오뱅크는 금융사기 문제는 어떻게 접근하고 있나?
	- 카카오뱅크는 전자금융 FDS와 체크카드 FDS가 존재
	- 전자금융
		- 자체 개발
		- 인터넷 은행, 카뱅만의 상품, 활동적인 고객이 존재
		- 빠른 사기 탐지, 더 정확한 탐지, 신종 사기 대응해야 함
		- 더 맞는 규칙 기반 모형 + 머신러닝 모형
	- 머신러닝
		- 지도학습
			- 극단적인 데이터 불균형, 정상 99.9% 사기 0.1%
				- 데이터 불균형이 심하면 사기 예측 모형이 동작하지 않음
				- 고객 정보, 금융 활동, 앱 활동을 토대로 DNN => 모든게 정상이라 예측함. 사기를 잡을 수 없음
				- 따라서 언더샘플링과 오버샘플링을 통한 정상/사기 비율 개선
				- 정상은 언더, 사기는 오버
				- 딥러닝 사용시 정상과 사기의 Loss 비율을 조절, 정상 loss : 사기 loss = 1:10
				- <img src="https://www.dropbox.com/s/vhlwfkzwrt3pl3c/2019-08-30%2011.08.11.jpg?raw=1">
				- ANN을 통해 정상 근처의 데이터만 샘플링
				- 사기 데이터 생성을 위해 VAE 사용(그냥 늘리거나 통계적으로 할 수 있지만 VAE 사용)  
			- 시계열 이벤트 데이터 처리 : 앱 활동 => 로그인 => 간편이체 => 대출금조회 => 마이너스대출 => 로그아웃 
				- 앱 활동은 FastText로 벡터화
				- 계좌이체, 간편이체, 계자조회(잔고<0), 계자조회(잔고>0), 해외송금, 계좌개설, 마이너스대출, 신용대출 등
				- 벡터화한 이벤트는 1D CNN을 통과시킴, 속도 때문에 1D CNN씀 => 정상과 사기 비율 맞춤
		- 비지도학습
			- Auto-Encoder를 통한 이상거래 탐지
				- <img src="https://www.dropbox.com/s/nazq98ge8y5qne7/2019-08-30%2011.12.32.jpg?raw=1">
				- 원본 데이터를 넣으면 z로 압축하고, 그 압축된 데이터를 복원하는 모형
				- 정상 데이터로 학습한 뒤, 이상 데이터가 들어오면 X와 X'의 차이가 커지는 것으로 이상 거래를 탐지    
			- z를 같이 고려한 Deep Autoencoding Gaussian Mixture Model for Unsupervised Anomaly Detection
				- 보다 세밀하게 이상거래를 탐지할 수 있지만 충분하진 않음
				- 실제 정상적인 행동이지만 사기도 많음
		- 성능 개선 정도
			- 데이터 샘플링, Loss 조절, 이벤트 벡터화, 1D CNN, Auto Encoder를 통해 Recall 50% 상승, Precision 20% 상승
				- Recall은 쉽지만 Precision 올리는게 어려워서 더 개선할 예정
- 정리
	- 탐지 모형 개발은 금융 사기 탐지의 극히 일부분
	- 복잡한 딥러닝 모형이 답을 주진 않음
	- 모형도 중요하지만 변수 선택 및 전처리가 더 주용
	- 서비스 반영은 또 다른 큰 산 
- 요새 실험중인 기법들
	- <img src="https://www.dropbox.com/s/ddlzeiyc1tfmc86/2019-08-30%2011.15.20.jpg?raw=1">
	- TDA(Topoglocial Data Analysis)
		- 이상탐지, 기계학습 모형 설명, 시각화
	- Graph Deep Learning
		- 그래프 데이터, 노드와 엣지의 특성 반영
	- Label Propagation
		- 준지도학습, 라벨의 일부만 알 때 
- TDA
	- 2007년 mapper 알고리즘 발표 후 주목됨
	- 이상탐지에 많이 사용되고, 의학/금융 분야에서 활용
	- AYASDI라는 회사가 많은 특허 보유함
	- Google에 TDA 치면 한국분 1분 나오는데, 카뱅분임
	- 핵심
		- Topological - Data - Analysis
		- 데이터가 갖는 모양으로 모양의 의미를 분석
		- <img src="https://www.dropbox.com/s/keg7pmoba33l1ok/2019-08-30%2011.17.55.jpg?raw=1">
		- 테이블 데이터가 가운데 이미지처럼 그래프로 나오고, 어느쪽은 정상 1형 당뇨 등등이 구분됨
		- <img src="https://www.dropbox.com/s/9b0s4b0jkdyrnfu/2019-08-30%2011.18.51.jpg?raw=1">
		- <img src="https://www.dropbox.com/s/b44uznz2jtdfjhk/2019-08-30%2011.20.05.jpg?raw=1">
		- 원본 데이터를 가로축 따라 데이터 나누고(데이터 영역이 겹침), 그 데이터를 kmeans로 클러스터링 => Topology 구성
			- 노드 : 각 영역의 데이터 군집
			- 연결 : 공통 데이터가 존재하는 인접한 군집
		- k-means와 topological 비교
			- <사진>
			- 데이터 특성을 잘 표현하는 군집과 관계를 찾아냄
			- Outlier 또한 군집으로 잘 찾아냄
- TDA for Credit Card Fraud Detection
	- 신용카드 데이터에 대해 TDA 해봄
	- <img src="https://www.dropbox.com/s/qb2v0qv5f9fp3j1/2019-08-30%2011.21.19.jpg?raw=1">
	- 색이 진할수록 군집에 사기가 많다는 것
	- 뉴럴넷은 사기가 있다 없다만 알 수 있는데, 몇가지의 사기냐는 어려움. TDA는 어떤 형태의 사기가 있다는 것을 파악할 수 있음
	- 풀리커넥티드 들어가기 전 데이터로 TDA를 해봤는데, 색이 진할수록 사기가 많음  
	- 이제 더 분석해서 결과를 얻을 예정
- 질문
	- 새로운 사기 대응은 어떻게 하는가?
		- 비지도로 탐지할 수도 있고, 평상시에 데이터 분석하며 할 수 있고, 새로운 사기가 올라오면 금융권끼리 사례 공유가 됨
	- Autoencoder의 쓰레솔드 어떻게 줬는지?
		- 실제로 오토인코더로 탐지하지 않음. 정상과 사기가 잘 분리가 안됨. 탐지는 안되고 feature 정도로만 사용
		- 쓰레솔드를 다 조절해봤는데, 오탐이 너무 높았음 
		- 이걸 통해 정상이나 아니다 나누진 않음
- 후기
	- Anoamly Detection하는 방법을 깔끔하게 말씀해주셔서 만족스러운 발표!
	- TDA란 것도 알게되서 유익했음 

---


### Buffalo: Open Source Project for Recommender System
- 김광섭님
- 버팔로의 특징
	- 지원 알고리즘
	- 뛰어난 생싼성과 성능
	- 편의 기능
		- 실험할 때 필요한 기능들
- 추천시스템과 버팔로의 관계
	- 왜 오픈소스를 개발했는가?
	- 추천시스템을 보통 협업 필터링, 무비렌즈 데이터에 대해 주로 이야기함
	- 사용자에게 콘텐츠 혹은 정보를 소비하는 경험을 제공하는 기술
- 카카오 추천시스템 토로스의 일부 기능
	- <img src="https://www.dropbox.com/s/gc1zsea56fb34tc/2019-08-30%2012.04.06.jpg?raw=1">
- 개발 배경
	- 2010년 초반에 오픈 소스 프로젝트가 거의 없었음
	- 특히, 원하는 크기의 데이터를 적절한 시간에 효과적인 자원으로 분석할 수 있는 프로젝트가 없었음 
	- 초기엔 Matrix Factorization 모듈
	- 쓰기 편하고 실용적이고 스케일러블, 만족스러운 품질의 결과를 줄 라이브러리
- 6년정도 된 프로젝트인데 이제 공개한 것
- 버팔로 알고리즘
	- Alternating Least Square
	- Bayesian Personalized ranking matrxi factorization
	- Word2vec
	- CoFactors 
- Matrix Factorization
	- 행렬로 표현된 데이터를 더 작은 차원의 행렬로 분해하는 방법
	- 장점
		- 데이터의 압축
		- 숨겨진 특성의 활용 
- 4개의 MF 알고리즘을 제공하는데 왜 4개나 필요한가?
	- 행렬 분해할 때 목적 함수의 정의, 데이터의 구성과 변형에 따라 서로 다른 성질의 은닉 벡터를 구할 수 있음
	- 여러 행렬 분해 알고리즘을 사용해 다양한 특질을 확보할 수 있음
	- 버팔로는 원하는 크기의 데이터를 적절한 시간에 효과적인 자원으로 수행할 수 있는 알고리즘 위주로 선택하며, 품질보다 확장성이 더 중요함
	- 그러나 scalabiltiy > quality가 항상 성립하는 것은 아님. 대부분 문제는 Scalability는 타협 가능하지 않고 Quality는 타협 가능함
- Scalability가 중요한 이유
	- <img src="https://www.dropbox.com/s/n7bmjrf6jyhomwh/2019-08-30%2012.14.08.jpg?raw=1">
	- NNZ 20M : 0이 아닌 값이 이만큼 있다
- 품질은 어디까지 타협해야 할까?
	- 다양한 알고리즘을 실험한 결과 체감상 차이가 미미하고 시스템 성능에 미치는 영향도 차이가 크지 않음
	- 초반엔 조금만해도 성능이 오르지만 점점 하다보면.. 시간대비 효율이 안나옴
	- 투자 대비 효율로 전환
		- 긴 파이프라인으로 구성된 시스템
		- Ceiling Analysis  
 - 다른 알고리즘과 비교
 	- implicit, gmf, pyspark랑 비교
 	- 대비해서 빠름
 	- 자원을 효과적으로 쓰고 있는지도 테스트해봄 => latent vector 크기 고정하고 cpu core 하나씩 늘리며 얼마나 빨라지는지 봄)
	- batch learning으로 잘라서 사용 가능
- 높은 생산성
	- 유사 콘텐츠 추천과 개인화 추천에 대한 병렬처리 기능 제공함
	
	```
	from buffalo.algo.ali import ALS
	from buffalo.parallel.base import ParALS
	als = ALS.new('./ml20m.bin')
	
	als.most_similar('Starwars')
	
	#parallel
	par = ParALS(als)
	par.num_worker = 8
	par.most_similar(['Starwars', 'Startrek'])
	``` 

- 진행한 노력들
	- Python/C++ 코드 베이스와 병렬처리를 효과적으로 구현
	- 쓰레드 작업할 때 job으로 쓰레드를 나누는데 job의 크기가 다 다름
	- 병목이 누적되며 차이를 만듬
	- 데이터 사이즈를 고려해 job을 배분하면 전체적 속도가 월등히 빨라짐 
	- 청크화된 데이터베이스를 사용해서, SSD에 내려둠
 	- 자세한 것은 Github repo
- 편의 기능
	- Validation
		- Stream 데이터에서 Matrix Market 데이터로 변환 가능
		- 데이터 순서 기록을 고려해 최근 데이터만 추가하는 등
	- Evaluation
		- 어쩧게 할지 지정할 수 있음
	- Tensorboard
		- 텐서보드 옵션을 지정하면 버팔로가 넘김
	- Hyper-parameter optimization
		- 연구 목적 + 서비스 개발에도 중요한 부분
		- 모든 머신러닝 알고리즘에서 하이퍼파라미터가 가장 중요하다고 해도 과언이 아닌데, 이런 기능을 내장하고 있음
		- hyperopt를 wrapping
			- min, max가 이슈가 있어서 조금 수정한 것은 있지만 거의 hyperopt와 유사 
		- 모델 deploy도 됨
- 요약
	- C++/Python
	- CPU 최적화, GPU 지원
	- 효과적인 시스템 자원 활용
	- 연구/개발 목적의 편의 기능 제공
	- 벤치마크 표준화
		- 예측, 유사도
		- 맥락 고려
		- 다양한 데이터
		- 양질의 데이터 등
	- 지속적인 오픈소스 활동
		- 신규 작업: 실시간 추천 어플리케이션 서버
		- 기존 성과물 개선 작업 : n2, buffalo    
 	
- 후기
	- 추천할 때 한번 써봐야겠음
	- 오픈소스에 기여하는 카카오 추천팀 멋짐..!
	- 루카스님 리스펙 


### TensorRT를 이용한 카카오 OCR 모델 Inference 성능 최적화
- 이현수님
- 딥러닝 모델의 Trade off
	- 연산량 증가하면 정확도 향상됨
	- 정확도와 속도는 반비례
- Inference 성능 향상을 위한 방법들
	- 네트워크 구조 개선
		- 정확도도 좋고, 연산도 효율적인 모델 학습
		- MobileNet, EfficientNet, Parallel decoding
	- 학습된 모델의 inference 과정 최적화
		- compression
		- quantization + a
			- TensorRT Framework 활용   
- OCR Model 구조
	- Detection Model + Recognition Model로 구성
	- Text Detection Model
		- Convolution + Unpool layer, unet 
		- <img src="https://www.dropbox.com/s/eqh9w6nx1mkh3xa/2019-08-30%2014.03.42.jpg?raw=1">
	- Text Recognition Model
		- CNN Feature Extraction + Self Attention(transformer)
		- Connextionist Temporal Classification
		- <img src="https://www.dropbox.com/s/tnd58b8bq8dkglc/2019-08-30%2014.04.14.jpg?raw=1">
- TensorRT
	- 딥러닝 inference 최적화를 위한 NVIDIA 플랫폼
	- 학습된 모델을 TensorRT로 가져와 Inference할 엔진 생성
	- 최적화 방법
		- Precision Quantization
			- 35 bit floating point -> 16 bit / 8 bit
			- inference 효율성 증가
				- 데이터 전송 효율 증가
				- GPU 가속을 통한 연산 속도 향상
			- infenrece 정확도 감소
				- Quantization-aware Training
				- Network Calibration(INT8)    
		- Layer & Tensor Fusion
			- 공유된 메모리로 연산 => 하나로 합침
			- Dependency가 없는 연산도 합쳐서 수행 
			- <img src="https://www.dropbox.com/s/q1gy7ak45ha095l/2019-08-30%2014.07.32.jpg?raw=1">
		- Kernel Auto-Tuning
			- GPU 종류, 배치 사이즈, Tensor shape 등을 기반으로 가장 효율적인 GPU 커널 선택
			- 직접 Operation 구현해서 최적화 가능
- TensorRT 사용 방법
	- 학습된 모델 가져오기
		- Createing a network definition from scratch
			- weight 파일 매핑
		- importing a model
			- 모델을 파싱, 파싱할 수 있는 형태로 네트워크 구조 수정해야 함
			- 이 부분 위주로 설명
- TensorFlow 모델을 Import
	- TensorFlow GRaph -> UFF Network -> TensorRT Engine -> Engine을 이용한 Inference
	- TensorFlow Model을 inference하기
		- <img src="https://www.dropbox.com/s/7i6jlvuggcyfsie/2019-08-30%2014.10.18.jpg?raw=1">
	- 모델 parsing을 위해서 확인할 사항들
		- <img src="https://www.dropbox.com/s/qw0bfupfsjkd2k5/2019-08-30%2014.10.47.jpg?raw=1">
- TensorFlow 모델 그래프 확인
	- TensorRT에서 사용할 수 있는지 확인
	- shape  연산의 파라미터를 상수로 고정
	- 즉, 가변 길이 Input이 아니라 고정 크기의 input
- 연산의 format 및 shape 확인
	- channel first format 사용
	- Dense / Conv1D 사용 힘듬 	
- Plugin Layer를 이용한 연산 구현
	- TensorRT에서 사용 가능한 연산 구현해 대체
- 후기
	- 중간에 잠시 일이 있어서 나갔다 왔지만, 발표의 흐름이나 정보 획득 관점에서 유익했음!
	- 나중에 직접 사용할 때 발표 자료 보면 더 도움이 될 듯

---

### 비주얼 컴퓨팅을 활용한 카카오맵
- 박영욱님
- <img src="https://www.dropbox.com/s/yl8ud9ezfpneiuu/2019-08-30%2015.08.54.jpg?raw=1">
- <img src="https://www.dropbox.com/s/n5y3osphmcjqrie/2019-08-30%2015.11.28.jpg?raw=1">
- <img src="https://www.dropbox.com/s/v2kyhzgkrhidx6y/2019-08-30%2015.12.46.jpg?raw=1">
- <img src="https://www.dropbox.com/s/nm65rj7482lb2f8/2019-08-30%2015.19.44.jpg?raw=1">
- <img src="https://www.dropbox.com/s/ghy7minb4ed2et8/2019-08-30%2015.20.56.jpg?raw=1">
- <img src="https://www.dropbox.com/s/lomsggv5tnh6cqu/2019-08-30%2015.24.54.jpg?raw=1">
- <img src="https://www.dropbox.com/s/nzcp2gubzfu9192/2019-08-30%2015.27.37.jpg?raw=1">
- <img src="https://www.dropbox.com/s/bmdgdlm9nbykz4s/2019-08-30%2015.28.11.jpg?raw=1">
- <img src="https://www.dropbox.com/s/yd9tbj9a6if8fhp/2019-08-30%2015.34.34.jpg?raw=1">
- 후기
	- 맥북 배터리 문제로 메모하진 못했지만, 친절한 목소리톤이라 듣기 좋았음
	- 카카오맵에 이런 다양한 기술이 있구나! 싶었던 세션


---

### 카카오 대리 시공간 데이터를 이용한 강화학습 및 최적화
- 조창민님
- 어떻게 문제해결을 했는지 그 과정을 공유하려고 함
- 카카오모빌리티 AI 파트 소개
	- 엔지니어링 스킬이 중요
		- 실서비스, ML, 통계 등
		- 실 서비스 장애 0건, 0.1초 미만 Latency
	- 서비스 고도화는 곧 성과
		- 매출 얼마나 높이고, 전환율을 높여달라
	- 딥러닝은 하나의 방법이고 항상 최고의 방법은 아님
	- 실제 서비스에 모델을 적용하고 불가능한 KPI를 달성하는게 목표. 모델링의 정확도 뿐만 아니라 안정적인 서비스가 목표
- 카카오 T 대리 서포터즈
	- 카카오 T에서 대리 서비스
	- 승객의 호출이 자동 배정 되는, 기사님을 위한 서비스
	- 승객에겐 보다 빠른 연결
- 이동 요청
	- 어느 위치에서 어디까지 가달라
- 이 서비스가 좋은 이유
	- 초보기사분들에겐 판단이 어려움. 계속 콜을 봐야되고.. 집으로 가는 것도 고려해서 콜을 받아야 함
- 해결해야 될 문제
	- 어떻게 이동 추천을 하지?
	- 대리 기사님당 콜 수행 n건 이상 수익은 얼마 이상!
	- 집 방향으로 이동을 어떻게 해야하지?
- 수요 예측 모델
	- 대리 데이터 EDA
		- 연도 상관없이 비슷하고, 월에 따라 분포 차이. 요일에 따라 콜 차이 심함, 시간의 영향도 받음
		- 오늘 휴일인지 아닌지는 큰 차이가 없음
		- 다음날이 휴일이냐에 따라 콜이 확 달라짐  
			- <img src="https://www.dropbox.com/s/2b3ixargdzlpt41/2019-08-30%2016.07.34.jpg?raw=1">
		- 지역
		- Kmeans로 클러스터링
		- 콜의 분포에 따라 지역을 나눔
		- Auto correlation을 보니 1시간 30분 이전까지 사용하면 예측률이 오르겠구나!를 얻음
			- <img src="https://www.dropbox.com/s/8ne0tf8w8u75j6b/2019-08-30%2016.09.16.jpg?raw=1">
	- 일단 다양한 모델 다 돌림
		- <img src="https://www.dropbox.com/s/mr0w0c2rqmhhjs8/2019-08-30%2016.10.06.jpg?raw=1">
	- 10분 단위 과거 콜 데이터로 예측
- 갑자기 서비스 담당자분이 다른 쪽으로 이동...
- 어쩌다보니 이걸 다 함
- 시뮬레이터 RL 환경 만들기
	- 예측은 그냥 있는 데이터 쓰면 됨
	- 하지만 누군가 어디로 이동시킬 때 더 잘나올까?는 예측할 수 없음
	- 우리 지도를 싹 그리고 대리 시장 그리고..! 대리 기사님처럼 뿅! (타다에도 이렇게 진행하고 있음)
	- 행정구역단위의 시뮬레이터
		- 읍면동 같은 것.. 행정 구역으로 기사님 보내기엔 너무.. 작고 등등
		- 택시 또는 지하철이 끊길땐?
		- 바로 옆에 조금만 가면 되는데 굳이 강남으로?
	- Geohash 기반 시뮬레이터
		- 사각형으로 자를 수 있음 => 모두 일정함
		- 대리 수요에 따라 서비스지역 한정
		- Geohash 기반 시뮬레이터 각 거리가 좌우는 같음, 대각선은 기존보다 더 이동함
	- H3 기반 시뮬레이터
		- 어느 방향으로 가나 길이가 동일함
		- 현재 이거로 만드는 중
		- 랜덤하게 대리 기사를 출근. 어느 위치에
		- 이동을 시킴. 여기로 가세요!  
		- 반경 검색! (몽고DB 사용)
		- 그 후 배정
		- 승객에게 가면 자동차를 몰고 목적지로 이동
		- 가상 환경에선 과거 30분 수요 데이터, 미래 30분안에 도착 예정 Agent
		- Agent의 현재 위치 등등을 모두 가지고 있음
	- 모델링보다 시뮬레이터가 100배 더 어려움
- Monte Carlo Tree Search
	- 대리가 콜 받고 도착해서 또 콜받고.. 핵심은 콜 받는거 아닌가?
	- 강릉까지 가서 내리면 집에 어케오지..? 택시타면 마이너스
	- 계속 이어지도록 하는게 중요함
	- Tree구조
		- <img src="https://www.dropbox.com/s/pw4itkyqk7r3sdd/2019-08-30%2016.17.40.jpg?raw=1">
	- Tic Tac Toe 게임 만들었는데 비겼으면 비겼지 절대 지지 않음
	- 문제는 경우의 수가 많아지면 학습 속도가 너무 느려짐
		- <img src="https://www.dropbox.com/s/foqr18jxhjlnwmf/2019-08-30%2016.19.38.jpg?raw=1">

	- 실제 적용까지 너무 힘듬.. 차원이 너무 많음
- Tabular Learning
	- State가 있고 Action 존재
	- <img src="https://www.dropbox.com/s/e7fxjeighznm8ub/2019-08-30%2016.21.56.jpg?raw=1">
	- random이랑 비교함
	- Q Learning 결론 : H3 위치만 넣었는데 의외로 잘됨
	- 단, 많은 기사님들이 들어올 떄 argmax를 취하기 떄문에 몰려버림
	- 이걸 해결해야 함
- Reinforce Method
	- 강화학습이 아닌 다른 알고리즘
	- softmax를 넣어 확률값으로 방향마다 나옴
		- <img src="https://www.dropbox.com/s/oud6gm3g1zasg69/2019-08-30%2016.24.11.jpg?raw=1"> 
	- Policy learning에서 q value의 구현에 따라 알고리즘이 바뀜
	- <img src="https://www.dropbox.com/s/att0zjl5oncelhs/2019-08-30%2016.24.59.jpg?raw=1">
	- <img src="https://www.dropbox.com/s/qlra0zcb3lsbhnp/2019-08-30%2016.25.12.jpg?raw=1">
	- 10.59 gradient가 클수록 학습을 많이 해야함. 상황은 좋은데 왼쪽으로 갈 확률은 적음  
	- 인싸이트는 얻었지만 모델의 업데이타 너무 느림! 게임이 끝날 때까지 기다려야 함
	- 그리고 Blackbox
- Reinforcement with convex optimization
	- 문제점
		- 서비스 초기엔 딥러닝이 애매함
		- 서비스 중후반엔 딥러닝 쓰고 AB Test 가능함
		- 딥러닝이 디버깅이 힘듬 
	- 서비스 초기엔 더 명확한 것을 가지고, 빠르게 튜닝 가능한 알고리즘이 필요함
	- 현실은 알 수 없음. 기사님들의 행동은?
	- 시급제라 기사님들이 존버함 (ㅋㅋㅋㅋㅋㅋ아..진짜 똑같네...)
	- RL에서 사용하는 Future Discounted Reward의 개념을 사용하고 싶지만 딥러닝은 사용하고 싶지 않음
		- 리워드는 그대로 사용하되 방법만 변경
	- Convex Optimization! 수리적 모델
		- <img src="https://www.dropbox.com/s/wwt07myqwtumap4/2019-08-30%2016.30.53.jpg?raw=1"> 
	- 컨벡스는 마주치는 부분이 없어야 함
	- 직선이면 리니어 프로그래밍
	- Simplex Optimization
		- 인접한 근단점들의 목적함수의 값들을 계속 검토하며 최적해 구하는 방법
		- 그냥 단순하게 최적점으로 이동하며 optimize
	- <img src="https://www.dropbox.com/s/5yo5ln8pj000rpy/2019-08-30%2016.31.48.jpg?raw=1">
	- 목표는 q value, 하루 끝났을 때 최대 수익을 maximize. 그걸 convex optimization
- 최종 공식
	- <img src="https://www.dropbox.com/s/f517maeii8dnc6y/2019-08-30%2016.32.18.jpg?raw=1">
	- 특정 지역의 콜 평균 - 해당 지역의 Agent 수 - 이동 거리 => 기사님들의 하루 수입을 최대화
	- beta들을 다 곱하고 beta를 최적화
	- 콜이 나오는 지역으로 유도하고 다른 기사님들과 겹치지 않도록 최적화
- 시뮬레이션 결과
	- 2배 이상, 실제로도 늘었다고 함
	- <img src="https://www.dropbox.com/s/7u1zi8z5to05qps/2019-08-30%2016.33.32.jpg?raw=1">
- N-Step A3C
	- 앞으로 방향은?
	- N-Step
		- 바로 앞단의 이익이 어떻게 되는가?
	- Temporal Difference 
	- <img src="https://www.dropbox.com/s/os1fhdliqyygsu8/2019-08-30%2016.34.35.jpg?raw=1"> 
	- n번째까지 진행
	- 이 알고리즘으로 슈퍼마리오 구현
	- N-step 64로, 64 프레임 이후에 떨어져 죽느냐? 등등에 따라 값이 변함
	- 이제 A3C, Curiosity 등을 적용할 예정
- 질문
	- 시뮬레이터 생성시 노드 단위까지 가는걸 했는지, geohash간 이동 거리 등으로 했는지 => geohash간
- 후기
	- 제일 만족스러웠고, 재미있었던 세션
	- 제가 회사에서 진행하고 있는 업무들이랑 너무 비슷하고, 문제 푸는 방식도 유사해서 잘 하고 있구나 생각했음. 따로 여쭤보니 시뮬레이터 만들대 SimPy 쓰셨다고 하셔서 동질감도.. (여기도 시급제라 이슈가..) 명함 주고받았는데 나중에 대화 한번 해봐도 좋을 것 같음












 