---
layout: post
title: "Tensorflow KR 3차 오프라인 모임 후기"
subtitle: "Tensorflow KR 3차 오프라인 모임 후기"
categories: etc
tags: lecture
comments: true
---


- Tensorflow 3차 오프라인 모임 후기입니다


### On-device ML with Tensorflow Lite
- Jason Zaman
	- twitter.com/perfinion
	- GDE Machine Learning
- On-device ML
	- Constrained environment
	- Small battery
	- Limited memory
	- Bad connectivity
- Why do on-device ML?
	- 더 많은 데이터에 접근하기 위해
	- interaction을 더 빠르게 하기 위해
	- Privacy preserving(클라우드에 올라가면 이슈) 
- Model Conversion
	- 모델을 실행시키는 방법들
		- Demo App
		- Pretrained models
		- Retrained models
		- Build from scratch  
- TF Lite까지 가는 Conversion flow
	- TensorFlow -> Saved Model -> TF Lite Converter -> TF Lite Model
- Model optimization
	- [loadmap link](https://www.tensorflow.org/model_optimization/guide/roadmap)
- TFLite conversion flow
- Sample Code
- Inference with TFLite Interpreter 
	- 생각보다 간단
	
	```
	import numpy as np
	import tensorflow as tf
	
	# Load TFLite model and allocate tensors.
	interpreter = tf.lite.Interpreter(model_path="converted_model.tflite")
	interpreter.allocate_tensors()
	
	# Get input and output tensors.
	input_details = interpreter.get_input_details()
	output_details = interpreter.get_output_details()
	
	# Test model on random input data.
	input_shape = input_details[0]['shape']
	input_data = np.array(np.random.ran
	dom_sample(input_shape), dtype=np.float32)
	interpreter.set_tensor(input_details[0]['index'], input_data)
	
	interpreter.invoke()
	
	# The function `get_tensor()` returns a copy of the tensor data.
	# Use `tensor()` in order to get a pointer to the tensor.
	output_data = interpreter.get_tensor(output_details[0]['index'])
	print(output_data)
	```
	 
- C++에서도 쉽게 가능
- Incredible inference performancee를 보임
- Interpreter Core는 Operation Kernels와 Accelerator Delegate, Operation Kernels로 이루어져 있음
	- [Document](https://www.tensorflow.org/lite/performance/delegates)
- Android Neural Network API delegate도 있음. 안드로이드에서도 딥러닝?
- Optimization
	- Quantization
		- Fixed point vs Floating point
		- Post training quantization vs Quantization-aware training  
		- 진행하면 모델 사이즈가 4배 작아지고, 속도 개선됨
		- TensorFlow => Saved Model + Calibration Data => TF Lite Converter => TF Lite Model
- EdgeTPU
	- 4 Trillion Operations per Second(TOPS)
	- 어마어마한 친구 ㄷㄷ
	- Fixed point only
	- Must fully quantize
	- Compile .tflite file for the EdgeTPU 
- Quantize 해야하는 이유
	- 에너지 절약



---


### Auto Scalable한 Deep Learning Production을 위한 AI Service Infra 구성 및 AI DevOps Cycle
- Feat. Docker로 1만 TPS inference 구축해보기
- SKT AI Center, 김훈동님 박찬엽님
- Production AI Serving Infra 구성 및 방법론
	- Pain Point 1
		- <img src="https://www.dropbox.com/s/7gmjlnk5hwtdfxa/2019-10-20%2013.44.41.jpg?raw=1">
		- Tensorflow Serving : 멀티 딥러닝 프레임워크 환경이라 커버리지가 높은 방법을 찾음
		- PaaS : 매우 비쌈
		- Flask : Python이 느리고, 험난한 Engineering Art가 필요
		- TensorRT  
		- Cloud serverless + NoSQL : 커버리지는 낮을 수 있음. 케바케
	- 다양한 프레임워크 지원을 위해 다른 방법으로 접근
	- 머신러닝 모델들 => PMML 파일로 컨버전해서 상대적으로 성능이 좋은 자바를 통해 inference 진행
		- <img src="https://www.dropbox.com/s/6u3hl3iydoiw3ef/2019-10-20%2013.47.31.jpg?raw=1"> 	
	- 진행한 방법
		- Flask, TensorRT 
		- 무거운 모델이면 TensorRT
	- 3번 Flask
		- 초 저렴 vCPU Docker 많은 인스턴스(월 4만원) vs 어벤져스급 GPU Docker(월 1000만원)
		- 저글링 vs 시즈탱크 => 일반인과 프로게이머의 차이
	- DL Serving에 대한 고려 사항
		- <img src="https://www.dropbox.com/s/7nwwq35mm8823tq/2019-10-20%2013.50.47.jpg?raw=1"> 
		- 배치사이즈 큰 경우, 작은 사이즈
		- Python Thread 처리는 최악
		- 처리량과 정확도의 Trade Off
		- 모델 압축 
	- GPU의 오해
		- 항상 왜 성능 그래프를 ResNet가지고 비교하지?
		- Real World에서 많이 쓰는 모델들 중 MLP, LSTM은 GPU가 안좋은 경우도 있음
		- 실제로 실험해본 결과 다 다름
		- CPU로 할 경우 250 TPS => 데이터 다 전역에 올리고 로그 못찍고 하면 가능했음(BiLSTM)
		- GPU로 할 경우 16TPS 
	- TensorRT 사용하는 방법
		- TensorRT를 이용하는 Restful API 사용하기
		- Flask로 TensorRT Engine 호출하기 => 여전히 싱글 프로세스라 여전히 병목이 생길 수 있음(플라스크 앞단에서)
	- Pain Point 2 - Poor Python Performance
		- Python -> node로 바꿔도 성능이 개선됨
		- 자바 버텍스 쓰니 성능 80배 올랐음
	- Pain Point 2 - 해결 팁
		- <img src="https://www.dropbox.com/s/stglx1hpuafrycw/2019-10-20%2013.56.43.jpg?raw=1">
		- <img src="https://www.dropbox.com/s/j1i56sajafgn3wl/2019-10-20%2013.59.15.jpg?raw=1">
		- Pandas를 빼고 순수 파이썬으로 쓴다
		- 함수형 언어처럼 가상 함수를 사용(Data copy 병목 줄이기) => Python에서 Map, Lambda 사용! functionstools.partial 
		- Python Thread를 쓰지 않음 => 고루틴처럼 쓸거면 멀티프로세스가 낫고.. Go의 Goroutin 쓰듯 하려면 cotyledon
		- MemoryView 활용
			- Data 핸들시 pointer 접근하듯
			- 대용량 Data Memory 복사 방지
			- C언어로 데이터 핸들링할 때 병목이 생기는 부분은 메모리카피 => 줄이기 위한 포인터
		- Microservice로 잘게 쪼개고
		- 모든 것은 비동기로
		- PMML + Java 컨버전 
	- 훈동님이 마소에서 발표한 사례를 실제 프러덕션에 적용하진 못함. gunicorn을 사용해 안정성을 잡음
	- <img src="https://www.dropbox.com/s/90cccgn9smznivi/2019-10-20%2014.00.17.jpg?raw=1">
	- WSGI -> ASGI로 큰 도움이 됨
	- Pooling, streaming, web hook 등의 비동기 방법이 있는데 어떤 라이브러리 쓸지 정의해서 컨버전함
	- 싱글톤을 데코레이터로 감싸기
	- CPU는 메모리가 더 많아서 멀티프로세스에 강함
	- 어디서 병목이 생기는지 잘 봐야하고, Low Level Debug 필요 : CProfile, kCacheGrind 등을 사용함
- Production AI Open Source Eco System
	- Pandas UDF 
		- 스파크를 깔고 pandas로
		- 배치에 최적화
	- Horovod
		- 분산, 스파크로 메모리 처리 가능
	- Petastorm
		- 엄청 큰 데이터로 할 경우 장비에 로딩이 안되면 하둡에 수백기가 접근할 경우 사용
	- Horizon
		- 강화학습시
		- 파이토치로 하고 onnx
	- ML WorkFlow
		- Airflow, Kubeflow
	- TFX
		- TensorFlow 모델을 쓰면 좋음
	- mlflow
		- MLOps 솔루션으로 스파크쪽으로 만드는 것
	- Serving at SCale : Seldon
	- Rapids.ai : GPU를 cuda 사용
	- Clipper.ai 딥러닝 쿼리 캐싱 
	- ONNX : 압축 관련쪽에서 사용
- Production AI DevOps
	- 지속적 통합과 배포
		- 도커
		- 개발환경 깔끔하게 하기 / 다른 곳에서 재현 가능
	- 마이크로서비스
		- Stateless하게 사용
		- 딥러닝은 state가 없음 
		- 멀티톤 고려하면 지옥이..
	- 커뮤니케이션
		- 많은 팀의 사람들이 각자 모델 만듬 
	- 코드형 인프라스트럭쳐
		- 테라폼 => 클라우드 서비스를 코드 레벨로 관리
		- 인프라 생성이 아닌 쿠버네티스로 한번에 다 처리함
	- 쿠버네티스
		- Pod을 관리하는 상위 개념들
			- 컨테이너일수도 있고, 여러 개를 관리하기 위한 개념 단위
			- Replicaset, Deployment 정도 집중
		- TargetCPUUtilizationPercentage를 지정하면 그 기준으로 처리함
	- 배포는 Azure Devops의 pipelines의 기능을 활용
		- Audit log : 누가 뭘 했다
		- 새로운 이미지 배포를 위해 latest 태그 만드는 것을 지양..! 환경/설정이 변경된 것이 아니라 배포가 되지 않음
		- Kubectl 명령을 devops 내에서만 실행
	- Rollback
		- 배포 기록 관리해주고, 기록 기반 롤백이 됨
		- Azure에서다 해준다
	- 코드 내에서 env 호출로 사용하지만 왠만하면 환경 변수 안쓰는게 좋음
		- db에 접근하는 서비스를 만들고 진행해야 함 
	- 모델 서빙을 위한 패턴
		- One fat image
			- 600k면 도커 이미지 올리는게 좋을숟도
			- 10기가면... 이미지에 먼저 올린다? 빌드하는데 30분 넘음. 불가능한 이야기~
			- 빌드할 떄 모델을 넣음
		- Model puller sidecar
			- Pod에 sidecar 개념이 있음. 다른 컨테이너를 붙여줌 
			- 볼륨을 공유하고 있는 웹 서버 + 스토리지 체크하는 친구만들어서 나눔
			- 자연어는 전처리 후처리 코드를 만들까 사이드카를 넣을까? 고민 중
		- Attached volumn 
			- 클라우드 기능 사용
			- 자신 컴퓨터의 하드디스크처럼 mount
			- 모델 경로 여기에 넣어주세요~ 이런 느낌


---



### 프러덕션 환경에서 연구하기
- 하이퍼커넥트 AI lab 하성주님
- 프러덕션(서비스 중인/될 제품)과 연구(불확실한 기술 개발)의 이야기
- AI Lab
	- 모바일 환경에서 실시간으로 이미지 다루는 것에 고민함
	- 기존 팀의 포커스는 모바일 환경에서 실시간으로 이미지 다루기였음(MMNet)
	- 이제 뭐할까? 워크샵
		- 10개 학회 => 3700편 논문
		- 유저 니즈/비즈니스 트렌드 기반 아이디어 브레인스토밍
		- 300가지 정도의 잠재적인 활용처 고민
		- 1년간 로드맵 구성
- 프로젝트 선정
	- 실현 가능성
	- 임팩트
	- 기술적 중요도
	- 트렌드
- Keyword Spotting
	- 특정 핵심어가 발성되었는지 검출하는 문제
	- 고려 사항
		- 도메인 확장
			- CV 외 도메인으로 확장
		- 난이도
			- 분류 문제라 상대적으로 쉬움
		- 기존 전문성
			- 경량 모델에서 모바일 배포를 많이 해서 좋은 성과를 금방 거둘 수 있다 판단     
- Literature Survey
	- 수백편을 목록으로 뽑고, 관력있을 것 같은 것들을 뽑음
	- 데이터 오픈, 코드 정리 유무 등
	- 스프레드시트 진행
- Baseline
	- 비교 대상이 되는 모델이 있어야 개선이 의미 있음
	- 점진적으로 비교할 수 있는 모델을 늘려가며 다양한 컴포넌트를 확보
	- 프로덕션에는 이미 연구된 모델을 구현하는 것이 충분할 수도 있음
- Baseline Selection
	- 합리적 성능이 나오는지? (SotA와 비교)
	- 구현 난이도 및 공식 코드 공개 여부
		- 재현이 까다로운 경우가 자주 있음
	- 프러덕션에서 적용될 제약 조건을 얼마나 만족하는지?
		- 모바일 CPU에서 실시간 수행 가능한가?
- Data
	- 공개 데이터셋
		- 오픈된 데이터를 바탕으로 리포팅
		- 학계에 없으면 줄 수 없다고 많이 말함
	- 비공개 데이터셋
		- 내가 관심있는 도메인의 모델 성능은 다를 수 있음         
		- 데이터 수집에 대한 고민
			- 어노테이션, 정합성 확인
		- 데이터 탐색이 필수
- PoC
	- <img src="https://www.dropbox.com/s/n2yp7gw858a1lun/2019-10-20%2014.30.11.jpg?raw=1">
	- 일단 처음엔 큰 모델을 만들어서 우리가 원하는 정확도가 나오는지 파악함
	- 중간 산출물이 프러덕션 활용될 수 있도록 고민함
- Process
	- <img src="https://www.dropbox.com/s/08ndrguwa2fghrk/2019-10-20%2014.31.16.jpg?raw=1">
	- 일단 한바퀴 돌면 어디가 문제인지 알 수 있어서 어느정도 휴리스틱도 괜찮음
	- 오디오 처음이라 컴포넌트 만듬
- Evaluation
	- <img src="https://www.dropbox.com/s/xwijd2is6gv9352/2019-10-20%2014.32.19.jpg?raw=1">
	- 모델 리포팅 결과가 재현 안되는 경우가 있음
	- 모델 최적화하는 사람의 역량에 따라 다를 수 있음    
	- Flops 연산량을 뽑아줌 => 모바일에선 약한 상관관계가 있음 
	- 제품을 어떻게 사용할지에 대해 고민
- Research
	- <img src="https://www.dropbox.com/s/g86a36pc02jsyl0/2019-10-20%2014.35.08.jpg?raw=1">
	- 유망한 모델을 재현하며 아이디어 얻기
	- 다른 도메인의 아이디어 훔쳐오기
	- 팀원과 토론 
- KWS Research Progress
	- 오리지널 REsNet과 다른 구조
	- 최대한 맞추니 결과가 좀 좋아짐. 하지만 속도가 빠르지 않아 가속할 방법 고민
		- 기존 모바일 컴퓨터 비전 전문성을 지렛대로 활용
- Audio Processing
	- <img src="https://www.dropbox.com/s/xnsyueqon98dwgr/2019-10-20%2014.37.19.jpg?raw=1">
- CNN Based KWS
	- 네트워크가 깊어져야 함
- Temporal Convolution
	- <img src="https://www.dropbox.com/s/hd1c20jbtv1owga/2019-10-20%2014.38.08.jpg?raw=1">
	- 모바일에서 만들면 1d가 매우 빠름(캐시 친화적인 이슈)
- TC-ResNet
	- <img src="https://www.dropbox.com/s/zi8owl743sm8y6b/2019-10-20%2014.39.12.jpg?raw=1">
	- 2D -> 1D로 변환 
- Result
	- <img src="https://www.dropbox.com/s/4ofjigqbmtrbism/2019-10-20%2014.39.50.jpg?raw=1">
	- 정확도 11% 개선, 모델 대비 385배 빠름 
- Publishing
	- <img src="https://www.dropbox.com/s/cb1k9zze7lhdfvm/2019-10-20%2014.40.21.jpg?raw=1">
	- Ablation 테스트를 통해 불필요한 컴포넌트를 이해
- Ablation Test
	- <img src="https://www.dropbox.com/s/qgnrqeqx6lykofb/2019-10-20%2014.41.16.jpg?raw=1">
	- 프러덕션땐 잘되면 그냥 냅두지만 논문 쓰다보면 제거할 수 있음 => 결국 다시 프러덕션때도 제거
- Retrospection
	- 3개월만에 진행
	- 기계학습 전문성 > 도메인 전문성이었떤 예
	- 기존 전문성을 잘 활용함
- Production + Research
	- 제작을 중심으로 하는 회사에서 성공적인 기계학습 조직 운영하려면
		- 서로 기대를 맞추고
		- 서로 윈윈할 수 있어야 함
- Expectation Management
	- <img src="https://www.dropbox.com/s/65m6efryci5lajj/2019-10-20%2014.42.49.jpg?raw=1">      
- Positive Sum Game
	- <img src="https://www.dropbox.com/s/txind7rfmc65n9r/2019-10-20%2014.44.14.jpg?raw=1">
- Ownership
	- <img src="https://www.dropbox.com/s/kbinhnk24zolg2e/2019-10-20%2014.45.03.jpg?raw=1">  


---


### 당근마켓 추천 시스템
- 당근마켓, 전무익님
- 초기 타겟은 여성 육아 맘이였는데 요샌 10대~60대 다양한 사람들이 생김
- 첫 화면, 홈 피드에서 추천을 제공하고 있음
- 큰 목적이 없어도 피드를 사용함
	- 동네 사람들의 제품 구경, 득템하는 재미
- 첫 단계
	- <img src="https://www.dropbox.com/s/3f0bao2qbtc1ir2/2019-10-20%2014.51.21.jpg?raw=1">
	- 처음부터 완전한 피드 개인화 불가
		- 소규모 스타트업
		- 최신 글의 중요성 고려
	- 추천 글을 피드 사이에 노출  
- 유튜브 추천 시스템 논문을 활용
- 추천 시스템 구조
	- 사용자 정보(봤던 글, 검색 키워드) => 추천 모델 => 다음 볼 글 예측 
	- 실시간 추천
		- <img src="https://www.dropbox.com/s/oh89g3akxkk7kmv/2019-10-20%2014.52.31.jpg?raw=1">
	- Two Stage
		- 후보 모델 + 랭킹 모델
		- 후보 모델만 구현함
- 학습 데이터
	- <img src="https://www.dropbox.com/s/1o8tigg8409u8po/2019-10-20%2014.53.33.jpg?raw=1">
- 학습 네트워크
	- <img src="https://www.dropbox.com/s/1ppevq60ckehrkg/2019-10-20%2014.54.00.jpg?raw=1">
- 후보 모델의 역할
	- 전체 글 중 사용자가 좋아할만한 200개 찾기
	- 추천 후보 pool을 빠르게 준비
	- 단일 모델론 수많은 글 중 단 하나를 맞추는건 쉽지 않아 이런 구조로 진행
- 측정 결과
	- <img src="https://www.dropbox.com/s/dod5jkmuqhq98tn/2019-10-20%2014.55.24.jpg?raw=1">
- 실시간 처리
	- Articel vectors를 학습해 인덱스 구축
	- user vector와 모든 article vectors에 대한 dot product 연산 대체
	- 매우 빠른 유사 벡터 검색이 가능하고, faiss 라이브러리 활용함
- Nearest neighbor index 준비
	- <img src="https://www.dropbox.com/s/6pzzxkgsvi5gj08/2019-10-20%2014.56.29.jpg?raw=1">
- Inference
	- <img src="https://www.dropbox.com/s/9m20wjz03ufnehz/2019-10-20%2014.57.03.jpg?raw=1">
	- CPU 0.01s 이하
- 추천 시스템 구축
	- 데이터 수집
		- <img src="https://www.dropbox.com/s/8fgriwxcrads011/2019-10-20%2014.57.57.jpg?raw=1">
	- 학습 Examples
		- <img src="https://www.dropbox.com/s/znafdjf66riqsoq/2019-10-20%2014.58.32.jpg?raw=1"> 
		- 이전에 본 글을 BigQuery ARRAY_AGG 사용함
	- CSV 추출
		- <img src="https://www.dropbox.com/s/h1fivpuliojo7u4/2019-10-20%2014.59.19.jpg?raw=1">
	- 데이터 전처리
		- <img src="https://www.dropbox.com/s/rvv3usqm93u1u0g/2019-10-20%2015.00.19.jpg?raw=1">
	- 병렬 분산 처리
		- 데이터가 많을수록 데이터가 선형적으로 시간 소요
		- Tensorflow Transform 활용
	- TF Transform
		- <img src="https://www.dropbox.com/s/cdylbaxf51vrcx8/2019-10-20%2015.01.19.jpg?raw=1">  
	- Training-serving skew
		- <img src="https://www.dropbox.com/s/gwdx12lhrt47emg/2019-10-20%2015.01.45.jpg?raw=1">
	- Cloud Dataflow 사용
		- <img src="https://www.dropbox.com/s/9yq242bwrmall0x/2019-10-20%2015.02.34.jpg?raw=1">
	- 모델 학습
		- 지속적으로 안정적 학습이 중요
		- 인프라 관리가 부담, 클라우드 관리형 서비스 사용    
	- Cloud AI Platform
		- Tensorflow Estimator로 모델 작성
	- 모델 서빙
		- <img src="https://www.dropbox.com/s/a5xyo3ypkmliwqq/2019-10-20%2015.04.19.jpg?raw=1">
	- 시간이 지나며 새로운 글을 추천하기 위해 지속적 학습, 업데이트
	- 파이프라인 시스템
		- 워크플로우 작업 실행
		- 실행 로그/결과 확인
		- 정해진 일정에 맞춰 실행
		- 직접 구현하기보다 오픈소스 활용
		- Kubeflow pipelines
			- 컨테이너 기반
				- 워브플로우 작업 정의 편리
				- 개발과 실행 환경을 동일하게 유지할 수 있음   
		- 파이프라인 작성
			- <img src="https://www.dropbox.com/s/b9ytw24wqzcdxlv/2019-10-20%2015.06.25.jpg?raw=1">
		- 파이프라인 배포
			- <img src="https://www.dropbox.com/s/njf6q0lxwf66k1z/2019-10-20%2015.07.05.jpg?raw=1">
		- 파이프라인 배포 코드
			- <img src="https://www.dropbox.com/s/lmec44r71j30qzn/2019-10-20%2015.07.34.jpg?raw=1">
		- Output Viewer
			- 예측 결과를 쉽게 볼 수 있음   
- 홈 피드 목록 중간에 넣고 있음
- 피드 개인화 추천 효과
	- 글 보기 +6%
	- 방문 시간 +5%

	
---

### 라이트닝 토크
- Machina Black
	- 기술로 법률 시장을 혁신  
	- NLP + Legal
	- 인공지능 변호사 : 계약서 자동 검토, 계약서 데이터 활용
	- 인공지능 판사 : 판결 예측, 송무 데이터 활용
	- Legal Translation
	- 법률 번역 => 영어도 잘하고 법도 잘 알아야 함
	- 연말에 베타 서비스
- Art LAB
	- AI & Robotics + Technology
	- Transformation : ART, 변혁으로 삶에 뿌리내림 
	- Team : 친구이자 동료가 됨
	- Beauty AI
		- 아름다움, 관리, 힐링 => 나를 아끼고 존중하며 소중히 여김 
		- 기획 AI : 소비자 니즈 센싱, 맞춤형 추천, 신제품 기획
		- 개발 AI : 원료-제품 관계, 재고 관리, 업무 효율화
- Lomin
	- 컴퓨터 비전 집중
	- DeepFake 검출
	- 무상에서 개인 정보 비식별화
	- 문자 인식 솔루션 개발 중
	- Visual Identity Protector : 개인 정보
- Nota
	- 경량화 기술 연구
	- 클라우드를 없애고 다른 Edge device에 모델이 돌아가도록 진행
	- Medical Device 회사와 의료 영상, 생채 진행
	- Security Company와 같이 CCTV 모델 연구 
- 에스아이에이
	- RNN을 이용한 전력 가격 예측 Github 
	- 위성 데이터로 판별
	- 위성 영상으로 지구에 뭐가 있는지 판단
	- 다양한 논문 연구하고 제출함
	- 경진대회 장려 중ㅋㅋㅋ
	- Object Detection, Ship Detection, Explainable AI, Vehicle Detection 등 집중
- Hutom
	- 수술 중 발생하는 문제를 데이터 기반으로 해결
	- 암 수술은 기구를 통해서 진행하고 있음
	- 로봇 팔이 들어감
	- 데이터
		- SEE : Stereo Camera
		- DO : Controller 	
		- 키네마틱 데이터
- DACON
	- 경진 대회
	- 교육에 활용
	- 국방 + 쎄트렉아이와 함께해서 곧 대회 열림
	- 위성 이미지 객체 추출 
	- 비즈니스 데이터 Fit, 비즈니스 대회 Fit, 운영 자동화(치팅, 자동 리포팅 등), 고도화 및 적용
- PR12 
	- 매주 일요일 밤 10시에 매주 2편씩 논문 읽고 발표 영상 녹화해 공유
	- 여태 누적 200편
	- 컴퓨터 비전 위주로 진행
	- ㅋㅋㅋㅋㅋㅋㅋㅋ 아침에 영상 보는 모임 나옴
	- 총 View : 431,766
	- Like : 3,863
	- Unlike : 238
	- 최다 조회수 3만회 => Faster RCNN
	- 외국인들이 영어 번역해달라고 하기도 함
	- 진원님 최초 기수부터 마지막까지 계속 하고 계심! 파이팅!!

---


### Kafka 스트림을 위한 멀티프로세스 딥러닝 추론
- 네이버 쇼핑플랫폼
- 이성철님
- 쇼핑 카테고리 분류
	- <img src="https://www.dropbox.com/s/0zzdd7eacmp6zwq/2019-10-20%2016.50.54.jpg?raw=1">
- 과거엔 관리자가 쇼핑몰 카테고리를 직접 매핑했으나, 요새는 딥러닝 기반 카테고리 분류를 하고 있음
- Kafka 스트림을 위한 추론 시스템 개발
	- <img src="https://www.dropbox.com/s/o5fjuh4h6dyf1fu/2019-10-20%2016.52.49.jpg?raw=1">
	- 메세지 기반, 썸네일 만들고 카테고리 분류 절차 등을 카프카에 태움 
- Raw Data
	- <img src="https://www.dropbox.com/s/yh8ra6l6u0ahqcu/2019-10-20%2016.53.48.jpg?raw=1">
- 텍스트 전처리 => Numpy Array 만듬
- 이미지 전처리
	- <img src="https://www.dropbox.com/s/q3phju2uzyi0wf2/2019-10-20%2016.55.59.jpg?raw=1"> 
	- 디스크 IO가 없어야 함(메모리에서 올림)
	- 나누기는 GPU에서 처리 
		- 나누기는 CPU consuming한 연산
- Multi-process inference
	- <img src="https://www.dropbox.com/s/x534ktohfvtwu54/2019-10-20%2016.57.14.jpg?raw=1">
	- Consumer : 토픽에서 메세지를 받아오는 역할
	- Preprocessor : 받아온 메세지를 모델에 입력할 수 있도록 데이터 변환
	- Classifier : GPU 모델을 로딩해서 들어오는 데이터를 계속 inference 
	- GPU를 최대로 쓰기 위해 Classifier보다 더 많은 Preprocessor가 필요(이렇게 하면 분류기 앞에 배치 큐에 많은 것들이 담김)
- Inference performance
	- <img src="https://www.dropbox.com/s/1mgehwg5bhgtg26/2019-10-20%2016.59.20.jpg?raw=1">
- Multi process Class(Consumer의 예)
	- <img src="https://www.dropbox.com/s/owrhafy0ezu4ny6/2019-10-20%2017.00.29.jpg?raw=1">
	- Multiprocessing.Process 상속하고 잘 종료하기 위해 stop() 함수 작성하고
	- run() 함수 필수 작성
		- 결과 quere가 full이면 대기
	- Multiprocessing의 Array가 있구나
- main.py
	- Process Start / Stop
	- <img src="https://www.dropbox.com/s/h5v9reitc6jcdn6/2019-10-20%2017.01.25.jpg?raw=1">
- 이미지와 텍스트 학습 데이터 준비
	- Training 데이터셋 만들기
		- 이미지와 텍스트를 같이 담을 수 있어야 하고
		- Spark Tensorflow 사이에서 자유롭게 사용 가능해야 함
		- TFRecords가 가장 적합   
	- 이미지 URL만 아닌 바이너리 파일
		- <img src="https://www.dropbox.com/s/cxjnoc5lxcjy8iw/2019-10-20%2017.03.51.jpg?raw=1">
		- 100 ~ 200 MB가 적절
		- 파티션 개수 조절!! 
	- Load data using tf.data
		- <img src="https://www.dropbox.com/s/06zl67tqaj70pcq/2019-10-20%2017.05.32.jpg?raw=1">
		- 런타임 shuffle은 미리 하고 저장


---


### Graph Neural Networks
- 류성옥님
- 분자가 그래프로 표현됨
- Inductive Biases in Neural networks
	- 파라미터라이즈를 할 수 있음
	- 뉴럴넷은 연속함수를 근사할 수 있단 사실 때문에 사용
	- MLP에서 더 나아가서 도메인에 맞게 학습
		- 이미지 : CNN
		- Autoregressive : RNN
	- 의도적으로 bias를 걸어주는 것을 inductive bias라 표현함
	- Social Graph, 3D Mesh, Molecular Graph는 illegular함. 물분자와 에탄올의 그래프가 모두 다름
	- 주어진 그래프에 다르게 inductive bias를 걸 수 있을까?에 적합한 Graph neural network
- Node feature updates in GNNs
	- Graph Convolution networks
		- <img src="https://www.dropbox.com/s/ja9m0hc051c367q/2019-10-20%2017.13.21.jpg?raw=1">
		- Feature들이 벡터로 표현
		- 노드가 어떻게 업데이트가 되는지를 표현하는 기본적인 식 
		- <img src="https://www.dropbox.com/s/tvllcles7vgbbzz/2019-10-20%2017.14.45.jpg?raw=1">
		- 주변 사람과 connection을 의미하는 adjacency matrix
	- Graph Attention Networks
		- <img src="https://www.dropbox.com/s/irsjjmzfglugiee/2019-10-20%2017.15.54.jpg?raw=1"> 
		- 관계를 더 고려해 feature update 
	- self-attention, positonal encoding을 주로 사용함 => 그래프 어텐션은 주변의 관계를 묘사할 때 어텐션을 사용
- Message Passing Neural Networks
	- <img src="https://www.dropbox.com/s/tvx6musidnxyakm/2019-10-20%2017.18.53.jpg?raw=1">
	- GRU
	- Edge passing이 있을 때 사용 가능
- Learning tasks with GNN
	- Node-level prediction
		- <img src="https://www.dropbox.com/s/lxna3k8cfzkypzt/2019-10-20%2017.20.32.jpg?raw=1">
		- 노드 단위 예측
	- Edge-level predictions
		- <img src="https://www.dropbox.com/s/bhad5dzmpt508w9/2019-10-20%2017.20.46.jpg?raw=1">
		- 유저, 아이템 노드가 있을 경우
		- 주로 본 사람은 누구인가? => 누가 뭘 봤다라는 connection 레이어를 예측하면 아이템의 관계를 inference 할 수 있음
		- Relational inference
- Graph-level prediction
	- <img src="https://www.dropbox.com/s/6lhouzaecquotco/2019-10-20%2017.22.24.jpg?raw=1">
	- 이 그래프 구조가 이 병을 해결할 수 있는가?
- Graph generations
	- <img src="https://www.dropbox.com/s/tbefh98741nip15/2019-10-20%2017.24.16.jpg?raw=1">
	- 노드를 추가할 것인지?
	- 엣지를 추가할 것인지?
- Learning physical dynamics
	- <img src="https://www.dropbox.com/s/2ztf1zwlzmk62vp/2019-10-20%2017.25.45.jpg?raw=1">
	- 각자 다른 공들이 파티클이고 움직임(무슨 법칙이 있음) 
	- 움직임을 예측
	- <img src="https://www.dropbox.com/s/552fokopox9t94b/2019-10-20%2017.26.38.jpg?raw=1">
- 더 궁금하시면 [Graph Neural Networks Github](https://github.com/SeongokRyu/Graph-neural-networks)	

---	

### 나만의 코퍼스는 없다? 자연어처리 연구 데이터의 구축, 검증 및 정제에 관하여
- 조원익님
- 무엇을 위해 데이터를 만드는가?
	- <img src="https://www.dropbox.com/s/yj1nagwclth75zm/2019-10-20%2017.39.15.jpg?raw=1">
- 코퍼스의 종류
	- <img src="https://www.dropbox.com/s/lk5ztlu255pdmhj/2019-10-20%2017.41.34.jpg?raw=1">
- Annotation의 종류
	- 통사(syntax)에서 의미(semantics)를 넘어 화용(pragmatics)가지!
	- 이모션 태그, 다른 문장을 표현할 수 있는지? 말장난인지 아닌지 등
	- 사실 영역의 경계가 점점 흐려지고 있음  
- MATTER cycle
	- <img src="https://www.dropbox.com/s/abxlgo40iyjd730/2019-10-20%2017.45.25.jpg?raw=1">
- MAMA portion
	- <img src="https://www.dropbox.com/s/qylwub4d3qdws6k/2019-10-20%2017.46.43.jpg?raw=1">
- 어노테이션은 2명 이상, 많을수록 좋음
	- 주석자간 의견 일치도도 고려 
		- Cohen's Kappa
- 주석자
	- <img src="https://www.dropbox.com/s/xm2kt61068nyeds/2019-10-20%2017.51.27.jpg?raw=1">
	- 주석자들이 얼마나 언어학적 훈련을 받았는지 가늠을 해서 배정해야 함
	- 언어 배경, L2 이상
- 케이스 스터디 1
	- <img src="https://www.dropbox.com/s/pybno0sci1tz592/2019-10-20%2017.55.09.jpg?raw=1">
	- 호출어 없이 알아서 반응하는 음성 대화 서비스
	- <img src="https://www.dropbox.com/s/sr1poncg162v5oy/2019-10-20%2017.55.46.jpg?raw=1">
	- <img src="https://www.dropbox.com/s/axx4ttgz17qd5xa/2019-10-20%2017.57.00.jpg?raw=1">
	- 가이드라인 작성
		- <img src="https://www.dropbox.com/s/d804msdgmp985hc/2019-10-20%2017.57.50.jpg?raw=1">
- 케이스 스터디 2
	- Keyphrase extraction
	- <img src="https://www.dropbox.com/s/98drw07xyffbusq/2019-10-20%2017.59.45.jpg?raw=1">
	- <img src="https://www.dropbox.com/s/c9i1nw4jkentuon/2019-10-20%2017.59.54.jpg?raw=1">
- 데이터
	- <img src="https://www.dropbox.com/s/ifn2x6fvppdkbhi/2019-10-20%2018.04.22.jpg?raw=1">   
- 정리
	- <img src="https://www.dropbox.com/s/fxip05ihrpsaszt/2019-10-20%2018.05.52.jpg?raw=1"> 


---


### 행사 후기
- 기술적으로 깊은 이야기를 많이 진행해서 만족스러웠습니다
	- 회사에서 사용할 수 있는 키워드도 꽤 얻었습니다! 
- 오랜만에 만났던 분들도 모두 반가웠습니다 :)
- 100분 넘는 분들이 일요일까지 오셔서 열정을 보여주셔서 자극받고 갑니다..!
- 그리고 Art Lab에서 이벤트하셨는데 제가 당첨되서 엄태웅님의 책을 받았습니다!!
	- [대학원생 때 알았더라면 좋았을 것들](http://www.yes24.com/Product/Goods/72231788) 책 너무 좋으니 관심있으시면 꼭 보셔요 :) 
- 다음엔 타임 테이블을 미리 공지해주셔도 좋을 것 같아요 /ㅁ/











  