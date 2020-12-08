---
layout: post
title:  "Full Stack Deep Learning Bootcamp 정리"
subtitle: "Full Stack Deep Learning Bootcamp 정리"
categories: mlops
tags: basic
comments: true
---

- [Spring 2019 Full Stack Deep Learning Bootcamp](https://fullstackdeeplearning.com/march2019)의 영상을 보고 정리한 내용입니다
	- Lab(실습), Guest Lecture는 정리하지 않았습니다

	
### 부트캠프 후기
- 오프라인에서 참석한 것은 아니지만, 강의를 듣고 후기를 남김
- 일단 강의가 매우 고퀄!!!!!!(감동)
- 국내에선 이런 내공을 담은 강의를 거의 보지 못했음
- 프로젝트를 어떻게 해야되는가, 프로젝트 우선순위를 선정하는 부분도 알려줘서 매우 좋았음. 실제 현업에서도 매번 고민하는 이슈들(Lecture 2)	
- 데이터를 어떻게 다룰까에 대해 말해주는 점이 좋았음. 단순 로컬/클라우드만 하는게 아니라 Database도 말해줌(lecture 6)
- 팀 구성이 어떻게 되는지, 직군이 무엇이 있는지 알려준 점이 좋았음(lecture 7)
- 딥러닝 트러블 슈팅에 대해 알려줘서 좋았음. 언더피팅, 오버피팅일 때의 전략을 제공함(lecture 10)
- 모델의 성능 개선할 때 결과를 어떻게 봐야하는지에 대한 직관을 얻게 해줌(lecture 10)
- 이 부트캠프는 단순히 강의만 듣는게 끝이 아니고, 꼭 Lab(실습)을 해야함!! 코드를 보면 현업에서 사용할 소재들이 꽤 많음(Lambda 배포라던가) => [Github](https://github.com/full-stack-deep-learning/fsdl-text-recognizer-project) 참고
- 앞으로 어떤 분야를 연구할 것인가에 대해 흥미로운 주제를 던져주는 부분도 좋았음(lecture 13)
- 단, Serving하는 부분에 대해 엄청 깊게 알려주는건 아니고, 부트캠프답게 큰 그림을 그려주고 실습을 같이 하며 하나를 구현해봄 => 이 부분은 결국 키워드를 캐치해서 스스로 해보는게 좋을듯..!
	- MLOps는 [Awesome Production Machine Learning](https://github.com/EthicalML/awesome-production-machine-learning) 자료가 매우 풍부하며, Facebook [MLOps KR](https://www.facebook.com/groups/MLOpsKR/) 그룹도 있습니다 :)

### 어떤 사람에게 이 강의가 좋을까요?
- 어느정도 머신러닝/딥러닝 공부를 한 후, 실제 서비스 구현에 흥미있는 분
- 개발 베이스에서 머신러닝/딥러닝 공부를 하셨던 분
- 프로젝트를 어떻게 하면 좋을지 고민되는 분
- 딥러닝 프로젝트 트러블슈팅에 대해 고민되는 분
- Production 과정 전반에 대해 관심있는 분

---


### Bootcamp의 목적
- 개발자가 딥러닝에 친숙해지기 위한 Hands-on 프로그램
- 모델 학습은 딥러닝 Production의 일부분이고, 이 코스에선 Production화하기 위한 모든 것들을 가르칠 예정
	- **Problem**을 명확히하고 프로젝트의 **cost**를 측정
	- **Data**를 찾고, 전처리하고, 라벨링
	- 적절한 **Framework**와 **Infra**를 선정
	- 학습의 **reproducibility** 관련 트러블슈팅
	- 대규모 모델 **Deploy**
- 컴퓨터 비전과 자연어 처리 시스템을 프러덕션 환경에 배포하는 프로젝트를 진행
	- 선택적 필기 시험으로 지식을 테스트

<img src="https://www.dropbox.com/s/c2lco1wr5q9szvq/Screenshot%202019-10-05%2013.20.27.png?raw=1">

- 프로젝트
	- <img src="https://www.dropbox.com/s/3e8q7z0q5ms0mv5/Screenshot%202019-10-05%2013.20.42.png?raw=1">	

---

### Lecture 1 : Introduction
- [발표 자료](https://fullstackdeeplearning.com/assets/slides/fsdl_1_intro.pdf)
- Organizer
	- Pieter Abbeel : 버클리 교수님, Covariant.AI  Scientist
	- Sergey Karayev : STEM AI Head
	- Josh Tobin : Research Scientist at OpenAI	
- Object Detection in Computer Vision
	- 2012년까지의 대세
		- Image => Hand-engineered features(SIFT, HOG, DAISY) => SVM => 분류
	- 딥러닝 이후
		- Image => 8 레이어 뉴럴넷 => 분류
- 딥러닝이 발전한 예시를 보여줌(다양한 업계)
- Google, OpenAI, Facebook, Uber 등 다양한 딥러닝 엔지니어와 리더들과 이야기해서 컨텐츠를 만듬


---

### Lecture 2 : Machine Learning Projects
- 목표
	- 머신러닝 프로젝트를 이해하기 위한 프레임워크 도입
	- 머신러닝 프로젝트 Best Practices
- 머신러닝 프로젝트 Lifecycle
	- 1) Planning & Project setup
		- 어떤 프로젝트를 하기로 했는지
		- 요구사항, 목표 설정
		- 리소스 할당 등
	- 2) Data Collection & Labeling
		- 데이터 수집
		- 센서 설치
		- annotation 노가다 등
		- 그러나 데이터를 얻기 너무 어려움
	- 3) Training & debugging
		- OpenCV로 베이스라인 구현
		- SoTA 모델 찾고 구현
		- 구현체 디버깅
		- Task에 맞도록 모델 개선
		- 잘 안되면
			- 데이터를 더 수집
			- labeling이 신뢰할 수 없음을 깨달음
			- Task가 어려운 것을 깨닫고 각각의 trade off를 비교해 어떤 것이 제일 중요한가 고민
	- 4) Deploying & testing
		- 프로토타입
		- 테스트하고 프러덕션화
		- 그러나 프러덕션에서 작동 안될 수 있음
			- training data와 deployment의 데이터의 mismatching을 고치고
			- 데이터를 더 수집 
	- Metric & Performance
		- 선택한 Metric이 사용자 행동을 유지하지 않음 => 다시 고민
		- 프러덕션시 Performance가 좋지 않음(더 빠르거나 정확해야 할까?
	- 더 알아야 하는 것
		- 도메인에서 SOTA가 뭔지 확인
			- 가능하면 이해하고
			- 다음에 무엇을 시도할지 알아야 함  
	- <img src="https://www.dropbox.com/s/lw56aw0jm7fn74y/Screenshot%202019-10-05%2014.17.53.png?raw=1">
	- <img src="https://www.dropbox.com/s/oco7mwpicoxabb7/Screenshot%202019-10-05%2014.18.11.png?raw=1">
	
- 강의의 나머지 개요
	- 프로젝트 우선 순위 지정 및 목표 선택
	- 모델을 평가할 지표 선택
	- 비교의 기준이 될 베이스라인 선택    
- 프로젝트 우선 순위를 정하는 방법
	- 1) 영향력이 큰 ML 문제
		- 파이프라인의 복잡한 부분이 있는가
		- 간단한 예측이 가치가 있는가
		- <img src="https://www.dropbox.com/s/2vncihdy51pvefq/Screenshot%202019-10-05%2020.01.38.png?raw=1">
		- <img src="https://www.dropbox.com/s/g9tjvw3a0dm3kan/Screenshot%202019-10-05%2020.03.01.png?raw=1">
		- Feasibility(실행 가능성)
			- 데이터 사용 유무, 정확도 요구사항, 문제의 어려움이 모여 Cost를 만듬
			- <img src="https://www.dropbox.com/s/hqayevkl47sha10/Screenshot%202019-10-05%2020.05.40.png?raw=1">
	- 2) ML 프로젝트의 Cost는 데이터의 가용성에 의해 결정되지만 정확성 요구도 큰 역할을 함(예 : 95%의 정확도는 가져야 해!)  
		- Accuracy requirements가 중요한 이유
			- 높은 성능까지 가려면 cost가 지수함수처럼 증가함
			- <img src="https://www.dropbox.com/s/6c7qc2chvvbresd/Screenshot%202019-10-05%2020.21.38.png?raw=1">
			- 제품 설계로 이런 Accuracy 요구를 줄일 수 있음(=목적이나 활용 방법에 따라 다르게 할 수 있다는 뜻)
	- <img src="https://www.dropbox.com/s/9p85w0c7h4nh1xj/Screenshot%202019-10-05%2020.24.04.png?raw=1">
	- <img src="https://www.dropbox.com/s/zr0jc4s97g7urt1/Screenshot%202019-10-05%2020.24.53.png?raw=1">
	- 정리
		- 1) 영향력이 큰 ML 문제를 찾으려면, 파이프라인의 복잡한 부분과 cheap prediction이 가치있는 곳을 찾아보기 
		- 2) ML 프로젝트이 cost는 데이터의 가용성 + 정확도 요구사항에 의해 결정됨
- 지표 선택의 핵심
	- 1) 현실 세계는 지저분하고, 일반적으로 많은 측정 항목에 관심있음
	- 2) 그러나 머신러닝 시스템은 단일 숫자를 최적화할 때 가장 잘 작동함
	- 3) 결과적으로 metric 결합해서 공식을 만들어야 함
	- 4) 공식은 변경될 수 있음
	- 어떤 Metric이 좋은가?
		- <img src="https://www.dropbox.com/s/mt5moo1gh29y5vw/Screenshot%202019-10-05%2020.32.00.png?raw=1">
	- Metric Combine
		- Simple average, weighted average
			- <img src="https://www.dropbox.com/s/zwl37f6aopssdc8/Screenshot%202019-10-05%2020.33.42.png?raw=1"> 
		- threshold n-1 metric, n번째 평가 등
			- <img src="https://www.dropbox.com/s/3w0yg4x3qob6b5u/Screenshot%202019-10-05%2020.33.59.png?raw=1">
			- <img src="https://www.dropbox.com/s/xh0noyhyh93v18e/Screenshot%202019-10-05%2020.34.27.png?raw=1"> 
		- 복잡하고 도메인 특화된 공식
			- Average precision(AP) = curve의 아래 지역
			- mAP = mean AP
			- <img src="https://www.dropbox.com/s/ogvjyh66l0tkqhc/Screenshot%202019-10-05%2020.34.45.png?raw=1">
			- <img src="https://www.dropbox.com/s/o19d04r4igdpvte/Screenshot%202019-10-05%2020.35.06.png?raw=1">
	- Pose estimation Metric 고르는 예시
		- <img src="https://www.dropbox.com/s/42br20980u46d6w/Screenshot%202019-10-05%2020.38.22.png?raw=1">
		- Evaluate current performance
			- Train a few models
			- 현재 모델로 어떤 문제가 있는지 파악할 수 있음
		- <img src="https://www.dropbox.com/s/t27ej7kwqejypcd/Screenshot%202019-10-05%2020.39.16.png?raw=1">
		- <img src="https://www.dropbox.com/s/pw2zda0g656t8z8/Screenshot%202019-10-05%2020.39.28.png?raw=1">

- 베이스라인 선택의 핵심
	- 1) 베이스라인은 모델 성능에 대한 하한선을 제공(최소한 이거 이상은 해야한다)
	- 2) 하한선이 엄격할수록 더 유용함(출판된 내용, 사람보다 좋다는 기준 등)
	- 베이스라인이 중요한 이유
		- 같은 모델이 다른 베이스라인을 가지면 다음 행동이 달라짐
		- <img src="https://www.dropbox.com/s/cwa1u1nj5hyt8ri/Screenshot%202019-10-05%2020.44.47.png?raw=1">
	- 베이스라인을 찾을 수 있는 곳
		- <img src="https://www.dropbox.com/s/fwqlem5aakbm9zi/Screenshot%202019-10-05%2020.47.23.png?raw=1">
		- Scripted baseline의 예시 : OpenCV 스크립트, Rule based 방법 등
		- Simple ML baseline의 예시 : 일반적인  feature based model(bag-of-words classifier), linear classifier, Basic neural network model(vgg without batch norm, weight norm) 등
	- 사람 관련 베이스라인을 어떻게 만들 수 있을까
		- <img src="https://www.dropbox.com/s/9qttjlnauxycpc7/Screenshot%202019-10-05%2020.48.52.png?raw=1">

- 질문
	- 머신러닝 모델이 잘못되었다는 것을 어떻게 방지할 수 있을까?
		- 좋은 질문. 모델의 confidence를 사용
		- 그러나 머신러닝에서 여전히 어려운 문제. 모델은 자주 잘못되었는지 모름
		- 매우 적대적인 사례를 넣어서 어떻게 나오는지 보는 방법도 있고, 고민이 필요

---


### Lecture 3 : Intro to the Text Reecognizer Project
- [발표 자료](https://fullstackdeeplearning.com/assets/slides/fsdl_3_project_intro.pdf)
- 프로젝트 Architecture
	- <img src="https://www.dropbox.com/s/x39mv56l4v489ti/Screenshot%202019-10-05%2021.28.33.png?raw=1">
	- POST request => 이미지 Decode => Line Detector => Line Text Recognizer => Encode Response의 흐름
	- LineDetector와 LineTextRecognizer가 필요
- 실습 개요
	- Lab 1: Codebase walkthrough
	- Lab 2: Single-character prediction. Before predicting full lines, try a simpler problem
	- Lab 3: LineTextRecognizer. Build a synthetic dataset. LSTM + CTC loss model
	- Lab 4: Tools for experimentation. Weights & Biases, parallel experiment running
	- Lab 5. Experimentation. Try some things and run some things overnight
	- Lab 6. LineDetector. Train the line detection model
	- Lab 7. Data. Managing data, including label and versioning
	- Lab 8. Continuous integration. Testing your model
	- Lab 9. Deployment. Put the model into production
- Project Structure
	- Web backend : Serving predictions, provisioning
	
	```
	api/                        # Code for serving predictions as a REST API.
	    tests/test_app.py           # Test that predictions are working
	    Dockerfile                  # Specificies Docker image that runs the web server.
	    __init__.py
	    app.py                      # Flask web server that serves predictions.
	    serverless.yml              # Specifies AWS Lambda deployment of the REST API.
	```  
	
	- Data

	```
	data/                            # Training data lives here
    	raw/
       	 emnist/metadata.toml     # Specifications for downloading data
	```
	
	- Experimentation : 평가나 여러 노트북 파일

	```
    evaluation/                     # Scripts for evaluating model on eval set.
        evaluate_character_predictor.py

    notebooks/                  # For snapshots of initial exploration, before solidfying code as proper Python files.
        01-look-at-emnist.ipynb
	```

	- Convenience scripts

	```
    tasks/
        # Deployment
        build_api_docker.sh
        deploy_api_to_lambda.sh

        # Code quality
        lint.sh

        # Tests
        run_prediction_tests.sh
        run_validation_tests.sh
        test_api.sh

        # Training
        train_character_predictor.sh
	```
	
	- Main model and training code

	```
    text_recognizer/                # Package that can be deployed as a self-contained prediction system
        __init__.py

        character_predictor.py      # Takes a raw image and obtains a prediction
        line_predictor.py

        datasets/                   # Code for loading datasets
            __init__.py
            dataset.py              # Base class for datasets - logic for downloading data
            emnist_dataset.py
            emnist_essentials.json
            dataset_sequence.py

        models/                     # Code for instantiating models, including data preprocessing and loss functions
            __init__.py
            base.py                 # Base class for models
            character_model.py

        networks/                   # Code for building neural networks (i.e., 'dumb' input->output mappings) used by models
            __init__.py
            mlp.py

        tests/
            support/                        # Raw data used by tests
            test_character_predictor.py     # Test model on a few key examples

        weights/                            # Weights for production model
            CharacterModel_EmnistDataset_mlp_weights.h5

        util.py

    training/                       # Code for running training experiments and selecting the best model.
        gpu_util_sampler.py
        run_experiment.py           # Parse experiment config and launch training.
        util.py                     # Logic for training a model with a given config
	```

- LineTextRecognizer Model architecture
	- <img src="https://www.dropbox.com/s/c77bl3xrih5a8rr/Screenshot%202019-10-05%2023.20.34.png?raw=1">
	- <img src="https://www.dropbox.com/s/waomkflnvpv804p/Screenshot%202019-10-05%2023.21.16.png?raw=1">
	- <img src="https://www.dropbox.com/s/3koxo1tn5oevbek/Screenshot%202019-10-05%2023.22.58.png?raw=1">

---

### Lecture 4 : Infrastructure and Tooling
- [발표 자료](https://fullstackdeeplearning.com/assets/slides/fsdl_4_infra_tooling.pdf)
- 이상과 현실
	- <img src="https://www.dropbox.com/s/uj2khr8fpy6grnv/Screenshot%202019-10-05%2023.30.19.png?raw=1">
- [Hidden Technical Debt in Machine Learning Systems](https://zzsza.github.io/data/2018/01/28/hidden-technical-debt-in-maching-learning-systems/) 이야기함
	- 논문 리뷰 링크 참고
- 다양한 도구들
	- <img src="https://www.dropbox.com/s/5bi7seqkqludsqa/Screenshot%202019-10-05%2023.35.35.png?raw=1">
- Deep Learning Frameworks
	- Tensorflow가 Production에 사용하기 편함
	- 특별한 이유가 없으면 Tensorflw/Keras or PyTorch 사용
	- 둘 다 동일한 포인트로 수렴
		- define by run으로 쉽게 개발
		- execution graph를 다양한 플랫폼에서 최적화
	- 일화로, 사람들이 파이토치로 바꿀때 행복해함
- Development, Training/Evaluation
	- 하드웨어
		- <img src="https://www.dropbox.com/s/vxqennollh324a9/Screenshot%202019-10-05%2023.42.09.png?raw=1">
	- GPU Basics
		- NVIDIA가 유일한 선택이였던 시절이 있음
			- Google TPU가 현재 제일 빠름 
			- Intel, AMD가 곧 시작될 예정
		- NVIDIA가 매년 새로운 아키텍쳐를 공개함
		- <img src="https://www.dropbox.com/s/wsd9j7srvskre15/Screenshot%202019-10-05%2023.47.05.png?raw=1">
		- <img src="https://www.dropbox.com/s/m89xjn9wnllwcq5/Screenshot%202019-10-05%2023.47.20.png?raw=1">
		- <img src="https://www.dropbox.com/s/yc7urww5oaz6b8l/Screenshot%202019-10-05%2023.47.34.png?raw=1">
		- <img src="https://www.dropbox.com/s/73n4fw29fsz5u9w/Screenshot%202019-10-05%2023.47.56.png?raw=1">
	- Cloud Providers
		- AWS, GCP, Azure의 3파전
		- AWS가 제일 비쌈
			- Spot 요금을 사용하면 많이 할인되지만 갑자기 꺼질 수 있음
			- 하이퍼 파라미터 서치 실험엔 적합하지만, 실패를 처리하려면 인프라가 필요 
		- GCP는 TPU도 사용할 수 있음
			- GCP도 Spot이 있음, 거의 AWS보다 저렴함
		- Azure
			- AWS와 꽤 비슷
			- Spot 없음
	- On-prem Options
		- 4개까진 꽤 쉬움
		- 사전 구축된 것을 구매
			- Lambda Labs, NVIDIA, Supermicro, Cirrascale etc
		- <img src="https://www.dropbox.com/s/hhrq08lzr1ljlz5/Screenshot%202019-10-05%2023.55.25.png?raw=1">
		- <img src="https://www.dropbox.com/s/svt75hm893vp261/Screenshot%202019-10-05%2023.55.39.png?raw=1">
	- Cost Analysis도 했음
	- 다른 고려 사항
		- Data location
			- 클라우드에 데이터가 1TB 이상 있따면 compute하기 수월
		- Data privacy
			- 데이터를 클라우드에 올릴 수 없다면 on-prem
		- Security
	- 추천
		- <img src="https://www.dropbox.com/s/dgvk500ctkeislf/Screenshot%202019-10-06%2000.01.20.png?raw=1">
- Resource Management
	- 많은 사람들이 여러 GPU를 사용하고, 격리된 환경을 가져야 함
	- 바라는 것
		- 실험하기 쉽고, 적절한 의존성과 리소스 할당
	- Solutions
		- 스프레드시트
			- <img src="https://www.dropbox.com/s/w8kwezah8e0v38e/Screenshot%202019-10-06%2000.07.05.png?raw=1">
		- 파이썬 스크립트
			- <img src="https://www.dropbox.com/s/8wer2frze3tmsjm/Screenshot%202019-10-06%2000.07.24.png?raw=1">
		- SLURM
			- <img src="https://www.dropbox.com/s/qxmfx0vo5oywc8i/Screenshot%202019-10-06%2000.07.41.png?raw=1"> 
		- Docker + Kubernetes
			- <img src="https://www.dropbox.com/s/t13gseke38fpef4/Screenshot%202019-10-06%2000.07.53.png?raw=1">
			- <img src="https://www.dropbox.com/s/p0e3sxjfys3npfb/Screenshot%202019-10-06%2000.08.09.png?raw=1">
			- <img src="https://www.dropbox.com/s/saxtqyhttof0grg/Screenshot%202019-10-06%2000.08.45.png?raw=1">
			- [Kubernetes-GPU-Guide](https://github.com/Langhalsdino/Kubernetes-GPU-Guide) 참고
			- 최근 Kubeflow도 열심히 만들고 있음 
		- Software specialized for ML use cases  
- Distributed Training
	- 모델을 학습할 때 여러 GPU를 사용해야할 경우가 있음
	- 단순히 병렬로 실행하는 것은 덜 유용했으나, 데이터가 커지며 프레임워크의 병렬화가 좋아지고 있음
	- Data vs Model Parallelism
		- Iteration time이 너무 길어지면, 데이터를 parallel하게 학습해야 함
		- 모델 병렬처리는 복잡성을 증가시키며 거의 가치가 없음
		- <img src="https://www.dropbox.com/s/vqfumyaz4kzshku/Screenshot%202019-10-06%2001.15.07.png?raw=1">
	- Tensorflow Distributed
		- 코드를 크게 재구성해야 함  
		- 점점 좋아지고 있음
	- Horovod
		- Tensorflow, Keras, PyTorch 지원
		- MPI를 사용해 Tensorflow Distributed보다 덜 복잡함
- Experiment Management
	- 실험할 때 코드, 매개변수, 데이터셋을 추적하기 어려움
	- 여러 실험을 하면 더 어려움
	- 모델을 자동으로 저장하는 것도 좋음
	- Tensorboard
		- 단일 실험에서 좋은 솔루션
		- 다양한 실험을 커버하진 못함
	- Losswise, Comet.ml 등
	- Weights & Biases : 실습때 활용할 예정, [Document](https://github.com/wandb/client)
	- Sacred도 좋아요
- Hyperparameter Optimization
	- 하이퍼파라미터 서치할 때 유용
	- 간단하게 정의해서 사용할 수 있음
	- keras와 잘 맞는 것들
	- talos
	- Hyperas
	- Hyperopt
		- 머신러닝의 일반적 패키지와 다 맞음
		- Non-bayesian 알고리즘
	- SIGOPT : Hyperparameter 서비스 제공
	- Weights & Biases도 제공
	- Microsoft의 nni도 좋음
- All-in-one Solutions  
	- 최근 트렌드 : 하나의 시스템에 모두 넣음
	- 개발(hosted notebook)
	- AutoML을 사용해 실험을 여러 컴퓨터로 스케일링
	- 버전 관리 및 결과 review
	- 모델 배포
	- 성능 모니터링
	- FBLearner Flow
		- <img src="https://www.dropbox.com/s/ud4981l85b1sca6/Screenshot%202019-10-06%2001.24.24.png?raw=1"> 
		- <img src="https://www.dropbox.com/s/65dkmk7peruu660/Screenshot%202019-10-06%2001.26.21.png?raw=1">
	- Michelangelo
		- <img src="https://www.dropbox.com/s/g7qiy86lclt8sb0/Screenshot%202019-10-06%2001.25.52.png?raw=1"> 
		- <img src="https://www.dropbox.com/s/wlo8r3kafyqjjic/Screenshot%202019-10-06%2001.26.07.png?raw=1">
		- <img src="https://www.dropbox.com/s/53mmymswoqje9px/Screenshot%202019-10-06%2001.26.44.png?raw=1">
	- Cloud ML Engine(AI Platform)
		- AI Platform의 일부가 됨
		- <img src="https://www.dropbox.com/s/94qsxuds8ch18vg/Screenshot%202019-10-06%2001.27.41.png?raw=1">
	- TFX
		- <img src="https://www.dropbox.com/s/5j2bg8tzjrdonr0/Screenshot%202019-10-06%2001.28.32.png?raw=1">
	- Kubeflow
		- <img src="https://www.dropbox.com/s/94se75dalngx3d2/Screenshot%202019-10-06%2001.28.48.png?raw=1">
	- Amazon SageMaker
		- <img src="https://www.dropbox.com/s/huggsjpp5h7ur0k/Screenshot%202019-10-06%2001.29.20.png?raw=1">
	- Neptune
		- <img src="https://www.dropbox.com/s/6uexjt4h1tzzbmr/Screenshot%202019-10-06%2001.29.34.png?raw=1">
	- FLOYD
	- Paperspace
	- <img src="https://www.dropbox.com/s/lkm09yqih6h6330/Screenshot%202019-10-06%2001.30.00.png?raw=1"> 

---

### Lecture 5: Tooling and Experimentation Labs
- 코드로 실습함
- Weights & Biases
	- <img src="https://www.dropbox.com/s/lxzg1g5uy0ivlcf/Screenshot%202019-10-06%2001.39.23.png?raw=1">


---

### Lecture 6 : Data Management
- [발표 자료](https://fullstackdeeplearning.com/assets/slides/fsdl_6_data.pdf)
- <img src="https://www.dropbox.com/s/aevefth4udm6vuw/Screenshot%202019-10-06%2001.41.19.png?raw=1">
- Introduction
	- 대부분 딥러닝은 레이블된 많은 데이터가 필요함
		- 스스로 플레이하는 RL이나 GAns은 제외하고 => 아직 실용적이지 않음
	- 공공 데이터셋은 경쟁 우위를 가질 수 없음
		- 그러나 스타트 포인트로는 괜찮음 
- Roadmap
	- 1) Data Labeling
	- 2) Data Storage
		- Data(images, sound) and metadata(labels, user activity)
	- 3) Data versioning
		- 데이터셋은 사용자 활동, 추가 레이블을 통해 업데이트되며 모델에 영향을 미침
	- 4) Data workflow
		- 원본 데이터와 메타 데이터를 학습 가능한 데이터로 집계 및 변환
- Data Labeling
	- User Interfaces
		- HIVE 사이트를 통해 bounding box, segmentation, keypoints, cuboids 등을 뽑음
		- <img src="https://www.dropbox.com/s/t195sv08fkogmnd/Screenshot%202019-10-06%2010.28.10.png?raw=1">
		- Annotator는 매우 중요하고, 퀄리티 보장이 되야함
		- <img src="https://www.dropbox.com/s/kw8ulpe4dsfxzo0/Screenshot%202019-10-06%2010.28.50.png?raw=1"> 
	- Sources of labor
		- 사람을 고용함, 퀄리티 품질을 관리할 수 있음
			- 그러나 비싸고 스케일업이 느림
		- 크라우드 소싱 
	- Service companies 
		- 기간 선정
		- 좋은 사례(Godl standard)를 직접 라벨링
		- 여러 경쟁 업체와 샘플 요청      
	- Software
		- 풀 서비스 데이터 라벨링은 비쌈
		- 일부 회사는 소프트웨어 제공, 오픈소스도 있음(Prodigy)
		- <img src="https://www.dropbox.com/s/7rbdho3hmx9nza1/Screenshot%202019-10-06%2010.32.05.png?raw=1">  
		- <img src="https://www.dropbox.com/s/i9gei7cn747dazp/Screenshot%202019-10-06%2010.32.19.png?raw=1">
- Data Storage
	- <img src="https://www.dropbox.com/s/jesaz31dry1y9f9/Screenshot%202019-10-06%2010.35.09.png?raw=1">
	- Filesystem
		- Storage의 기초 layer
		- 기본 단위는 텍스트나 binary가 가능한 "file"이고 버전이 지정되지 않고 쉽게 덮어씀
		- 네트워크로 연결 가능(ex: NFS) : 여러 머신이 네트워크를 통해 액세스
		- 분산 가능(ex: HDFS) 	: 여러 곳에 저장하고 액세스 가능
		- Access 패턴이 주의! 빠르지만 병렬을 아님
	- Objest Storage
		- S3, Google Storage
		- API로 파일에 접근 가능, 직접 데이터 저장하지 않아도 됨
		- 기본 단위는 "object", 보통 binary, image, sound
		- versioning이나 redundancy가 서비스에서 가능함
		- parallel이 가능하지만 빠르지 않음
	- Database
		- 구조적인 데이터에 지속적이고, 빠르고, 확장 가능함
		- 실제로 모든 것이 RAM에 있지만, software가 디스크에 모든 것을 기록하고 유실되지 않도록 함
		- 기본 단위는 "row", unique id, rows, columns
		- binary 데이터가 아님
		- Postgres가 거의 90% 이상 좋을 수 있음, 비정형 JSON도 지원
	- Data Lake
		- 다양한 소스에서 데이터를 가공해 저장
		- Redshift, BigQuery 등    
	- 정리하면
		- <img src="https://www.dropbox.com/s/8rwddxz89x2mhb7/Screenshot%202019-10-06%2010.42.08.png?raw=1">
- Data versioning
	- <img src="https://www.dropbox.com/s/v0bz5dn0htu6tgb/Screenshot%202019-10-06%2010.43.06.png?raw=1">  
	- <img src="https://www.dropbox.com/s/ugqfmdoam4n05r5/Screenshot%202019-10-06%2010.44.05.png?raw=1"> 
	- <img src="https://www.dropbox.com/s/gfwyoq2l4gan7td/Screenshot%202019-10-06%2010.44.21.png?raw=1">
	- <img src="https://www.dropbox.com/s/ukk604oxfj65rbs/Screenshot%202019-10-06%2010.44.38.png?raw=1">
	- <img src="https://www.dropbox.com/s/ldrymm5lhv1fwej/Screenshot%202019-10-06%2010.44.50.png?raw=1">
	- DVC
		- Data versioning에 특화된 솔루션
		- <img src="https://www.dropbox.com/s/rzjsh3wx421ktiw/Screenshot%202019-10-06%2010.45.57.png?raw=1"> 
- Data workflows
	- Train시 아래와 같은 데이터를 가져와야 함
		- metadata : posting time, title, location 
		- log
	- 그리고 classifiers를 돌려서 결과 도출
	- Task Dependencies
		- A가 끝나고 B 작업을 해야하고, B 작업 이후에 C, D, E를 하고 C, D, E가 완료되면 F 진행하는 이런 의존 관계가 있음
	- Makefiles
		- Makefile에서 파이프라인 연산 가능
	- Luigi and Airflow
	- Distributing work  

---


### Lecture 7 : ML Teams
- [발표 자료](https://fullstackdeeplearning.com/assets/slides/fsdl_7_teams.pdf__vUzdzk%2FAxRhOPagFTz0ERFg28e7e20c61988c6abe0cfdd887bde6e4)
- Job Role
	- <img src="https://www.dropbox.com/s/nranvigqpwcatv7/Screenshot%202019-10-06%2010.51.52.png?raw=1"> 
	- <img src="https://www.dropbox.com/s/nhb0loh9eoy91kx/Screenshot%202019-10-06%2010.52.11.png?raw=1">
	- ML DevOps
		- 주로 소프트웨어 개발자 Role이고, SWE 파이프라인을 구축
	- Data Engineer
		- ML Team과 적극적으로 SWE
	- ML Engineer(머신러닝 엔지니어)
		- ML 기술 + SWE 기술의 Mix
		- 보통 SWE로 일하시던 분들이나 공부하고 발전
	- ML Researcher
		- ML 전문가
- ML team structures
	- 대부분 팀은 SWE + ML 스킬이 혼합됨
	- 팀의 모든 사람들이 어느정도 SWE 기술을 가져야 함
	- ML Researcher의 다른 견해
		- SWE와 통합하기 어려움
		- 빠르게 움직이기 위해 알아야 한다
	- Data Engineer의 다른 견해
		- 일부 조직은 ML팀과 같이함
		- 별도의 팀이어야 함
- Managing ML Teams
	- Task가 쉬울지 어려울지 미리 말하는건 매우 어려움
	- <img src="https://www.dropbox.com/s/m4aaifd1wjwebzu/Screenshot%202019-10-06%2010.58.26.png?raw=1">
	- ML progress는 nonlinear
		- 무엇이 효과있을지 불명확해 계획이 어렵긴 함
		- 프로젝트 타임라인 추정이 매우 어려움
		- Production ML은 research와 engineering 사이에 있음
	- research와 engineering의 문화 gap이 존재
- 면접 질문의 큰 틀
	- <img src="https://www.dropbox.com/s/c5e3fwjlvyndgig/Screenshot%202019-10-06%2011.01.23.png?raw=1">
	- Why does the Residual Block in the ResNet architecture help with the vanishing gradient problem?
	- <img src="https://www.dropbox.com/s/d2d559bmky3c71z/Screenshot%202019-10-06%2011.01.50.png?raw=1">

	
---


### Lecture 10 : Troubleshooting
- [발표 자료](https://fullstackdeeplearning.com/assets/slides/fsdl_10_troubleshooting.pdf)
- 딥러닝에서 디버깅은 매우 어렵지만, 중요한 주제
- 왜 어려울까? 당신의 퍼포먼스가 좋지 않은 이유
	- <img src="https://www.dropbox.com/s/zxkh3qu2xn2h204/Screenshot%202019-10-06%2011.48.29.png?raw=1">
	- 흔한 Dataset 구조 이슈
		- 충분하지 않은 데이터
		- Class imbalances
		- Noisy labels
		- Train / Test 데이터셋의 분포가 다름 
- Key mindset for DL troubleshooting
	- Pessimism(비관적으로 보자)
- 트러블슈팅 전략
	- <img src="https://www.dropbox.com/s/rud5idogr9tsot9/Screenshot%202019-10-06%2011.51.16.png?raw=1">
- Start simple
	- 1) 간단한 아키텍쳐 선택
		- <img src="https://www.dropbox.com/s/mhr4mymm6et358v/Screenshot%202019-10-06%2011.56.51.png?raw=1">
	- 2) 일반적으로 좋은 default 사용
		- <img src="https://www.dropbox.com/s/jyv7gj8k1fxvams/Screenshot%202019-10-06%2011.58.43.png?raw=1">
		- <img src="https://www.dropbox.com/s/it9i162yn9n37ud/Screenshot%202019-10-06%2011.59.04.png?raw=1">
	- 3) Input 정규화
	- 4) 문제를 간단히하기 
		- <img src="https://www.dropbox.com/s/xql8rzo8774i0e8/Screenshot%202019-10-06%2012.01.01.png?raw=1">
	- <img src="https://www.dropbox.com/s/g83b7xq3p082wmv/Screenshot%202019-10-06%2012.03.02.png?raw=1">
- Implement & debug
	- 자주 발생하는 실수
		- Tensor의 shape가 안맞는 경우
		- 전처리 실수(정규화를 잊거나 너무 많이하거나)
		- loss function의 부정확함
		- train/eval 모드 설정을 까먹음
		- 수치적 불안정 : inf, NaN
	- 모델 구현을 위한 일반적인 조언
		- 가벼운 구현
			- v1은 가능하면 간소하게
			- 200줄 미만
		- 이미 구현된 component 사용
			- tf.nn.relu(tf/matmul(W, x)) 대신 tf.layers.dense!
			- tf.losses.cross_entropy
		- 복잡한 데이터 파이프라인은 나중에! 일반 메모리에 올려서 진행
	- 1) 모델 Run
		- <img src="https://www.dropbox.com/s/cwpi2rkjro59t11/Screenshot%202019-10-06%2012.09.36.png?raw=1">
		- Debuggers for DL code
			- Pytorch : 쉬움, ipdb 사용
			- tensorflow : trick(허나 2.0이 나왔으니 이 방법은 안해도?)
				- graph session 만들고, training loop 갖고, tfdb 사용
		- Shape mismatch
			- 와 대박 꿀팁
			- <img src="https://www.dropbox.com/s/8fo6tm4wl9u0aa1/Screenshot%202019-10-06%2012.12.29.png?raw=1"> 
		- Casting issue
			- <img src="https://www.dropbox.com/s/5lfca53staw3gj8/Screenshot%202019-10-06%2012.13.14.png?raw=1">
		- OOM
			- <img src="https://www.dropbox.com/s/s3klr3phs9yix5w/Screenshot%202019-10-06%2012.13.30.png?raw=1">
			- Tensor가 너무 크거나, 데이터가 많거나, operation에 중복이 있거나 등
		- Other common errors
			- <img src="https://www.dropbox.com/s/1xh7i58ym2xak8q/Screenshot%202019-10-06%2012.13.58.png?raw=1">  
	- 2) single batch에 오버피팅
		- <img src="https://www.dropbox.com/s/g1p5tbtwd4ysnlo/Screenshot%202019-10-06%2012.15.30.png?raw=1"> 
	- 3) 알고있던 결과와 비교  
		- <img src="https://www.dropbox.com/s/urslbq88br6oeq6/Screenshot%202019-10-06%2012.16.48.png?raw=1">
- Evaluate
	- Test error = irreducible error + bias + variance + val overfitting 
		- 이건 train, val, test가 모두 같은 분포를 가진다는 가정을 가짐. 진짜 그럴까?
		- 2개의 Validation 구성해보기
			- 하나는 Train에서 샘플링, 하나는 Test에서 샘플링
	- 결과 분석
		- <img src="https://www.dropbox.com/s/ruoflpo36mjift5/Screenshot%202019-10-06%2012.49.45.png?raw=1">
		- <img src="https://www.dropbox.com/s/lf8i0arapxr3e4r/Screenshot%202019-10-06%2012.49.58.png?raw=1">
		- <img src="https://www.dropbox.com/s/wqwp8sfdsnj8rwc/Screenshot%202019-10-06%2012.50.08.png?raw=1">
	- 정리하면, Test error = irreducible error + bias + variance + distribution shift + val overfitting
- Improve model/data
	- <img src="https://www.dropbox.com/s/2zoiynbq876jf92/Screenshot%202019-10-06%2012.51.00.png?raw=1">
	- 1) 언더피팅인 경우
		- <img src="https://www.dropbox.com/s/s10m0nzqgcnpnhy/Screenshot%202019-10-06%2012.52.01.png?raw=1">
		- <img src="https://www.dropbox.com/s/3dli0ya7hsgxx0p/Screenshot%202019-10-06%2012.52.25.png?raw=1">
		- ConvNet, ResNet, 파라미터 튜닝 등을 함
	- 2) 오버피팅인 경우
		- <img src="https://www.dropbox.com/s/zc5pd9yhvwr50u7/Screenshot%202019-10-06%2012.54.17.png?raw=1"> 
		- H ~ J는 추천하지 않음
		- <img src="https://www.dropbox.com/s/o9z3at5557r5qvv/Screenshot%202019-10-06%2012.56.16.png?raw=1">
		- 데이터 추가하고, weight decay 추가, data augmentation도 했음
	- 3) 분포 이동
		- <img src="https://www.dropbox.com/s/86r628r5msyt7x7/Screenshot%202019-10-06%2012.58.05.png?raw=1">
		- Error analysis
			- <img src="https://www.dropbox.com/s/5zdrrjl5981fkvv/Screenshot%202019-10-06%2012.59.04.png?raw=1">
			- <img src="https://www.dropbox.com/s/i8wmaf8rj1d36dx/Screenshot%202019-10-06%2012.59.18.png?raw=1">
			- <img src="https://www.dropbox.com/s/fbracbimpedemw5/Screenshot%202019-10-06%2012.59.57.png?raw=1">
			- <img src="https://www.dropbox.com/s/98mc6mmbujjk5jp/Screenshot%202019-10-06%2013.00.11.png?raw=1">
		- Domain adaptation
			- 레이블이 없는 데이터 또는 제한된 레이블이 있는 데이터가 있는 상황에서 source 분포를 학습해 또다른 target 데이터를 일반화하는 기술 
			- 레이블된 test 데이터가 적을 경우, 유사 데이터가 존재할 경우 유용
			- <img src="https://www.dropbox.com/s/y6ga3d5zz921lay/Screenshot%202019-10-06%2013.04.57.png?raw=1">
	- 4) Re-balance datasets
		- test set보다 val이 더 좋아보이면, 오버피팅일 수 있음
		- 보통 작은 val set 또는 많은 하이퍼 파라미터 튜닝에서 발생함
		- 그렇다면, val data를 다시 수집해야 함
- Tune hyper-parameters
	- <img src="https://www.dropbox.com/s/g7yu5unucd9kaf5/Screenshot%202019-10-06%2013.06.58.png?raw=1">
	- <img src="https://www.dropbox.com/s/qxm2oseypzifuhe/Screenshot%202019-10-06%2013.07.10.png?raw=1">
	- 어떤 파라미터가 민감한지 작성해보고 조정
	- Method 1: manual 하이퍼파라미터 최적화
		- <img src="https://www.dropbox.com/s/pt12lhgzrdq72lz/Screenshot%202019-10-06%2013.07.48.png?raw=1">
	- Method 2: grid search
		- <img src="https://www.dropbox.com/s/9kd4lasmg89nkm4/Screenshot%202019-10-06%2013.08.17.png?raw=1">
	- Method 3: random search
		- <img src="https://www.dropbox.com/s/euyzoglo6p2kzej/Screenshot%202019-10-06%2013.08.37.png?raw=1">
	- Method 4: coarse-to-fine
		- <img src="https://www.dropbox.com/s/wxxe424jluggv8m/Screenshot%202019-10-06%2013.09.50.png?raw=1">
	- Method 5: Bayesian hyperparam opt
		- <img src="https://www.dropbox.com/s/xzpgtqrapjov0ou/Screenshot%202019-10-06%2013.10.30.png?raw=1">

---


### Lecture 12 : Testing and Deployment
- [발표 자료](https://fullstackdeeplearning.com/assets/slides/fsdl_12_testing.pdf)
- <img src="https://www.dropbox.com/s/72rf1pwvdlg0pvx/Screenshot%202019-10-06%2013.13.40.png?raw=1">
- <img src="https://www.dropbox.com/s/r6q1w8ai1qlqbl0/Screenshot%202019-10-06%2013.13.55.png?raw=1">
- ML Test Score
	- <img src="https://www.dropbox.com/s/k7xk3hefbdbaa04/Screenshot%202019-10-06%2013.55.00.png?raw=1">
	- <img src="https://www.dropbox.com/s/9gcpmm407dic5xm/Screenshot%202019-10-06%2013.55.23.png?raw=1">
- Testing / CI
	- Unit / Integration Tests
		- 각각의 모듈과 전체 시스템을 테스트
	- Continuous Integration
		- 테스트는 항상 모델이 deploy되기 전에 새로운 코드로 repository에 푸시되야 함
	- Saas for CI
		- CircleCI, Travis, Jenkins
	- Containerization(via docker)
		- 테스트를 위한 격리된 환경
	- <img src="https://www.dropbox.com/s/zep1fw3lnpuk26z/Screenshot%202019-10-06%2013.58.28.png?raw=1">
	- CircleCI / Travis
		- Saas for continuous integration
		- repository와 통합되서 테스트
		- docker container의 command로 job을 정의할 수 있고, 리뷰 결과를 저장할 수 있음
		- No GPU
		- CircleCI has a free plan
	- Jenkins / Buidkite
		- 개인 하드웨어, 클라우드 등에서 CI하기에 좋은 옵션
		- GPU 사용하기 좋음
		- Very flexible
- Docker
	- 도커에 이해하면 좋음
	- Docker vs VM
		- <img src="https://www.dropbox.com/s/4rcwwp3d2f2dqa0/Screenshot%202019-10-06%2014.01.53.png?raw=1">
	- Dockerfile and layer-based images   
		- <img src="https://www.dropbox.com/s/rrraq2zs7xzmqmk/Screenshot%202019-10-06%2014.02.17.png?raw=1">
	- Dockerhub and the ecosystem
		- <img src="https://www.dropbox.com/s/7j9rwd73dw0q26l/Screenshot%202019-10-06%2014.02.29.png?raw=1"> 
	- Kubernetes and other orchestrators
		- <img src="https://www.dropbox.com/s/tmcw7wk9kb4tnyf/Screenshot%202019-10-06%2014.03.07.png?raw=1">
- Web Deployment
	- REST API
		- HTTP requests - response를 사용해 serving
		- 웹서버가 띄워져 있고, prediction system을 call
	- Option
		- VM에 deploy
		- Container에 deploy
		- Serverless function에 deploy   
	- <img src="https://www.dropbox.com/s/jsw485tifmkd38m/Screenshot%202019-10-06%2014.05.04.png?raw=1">
	- Cloud instance에 배포
		- <img src="https://www.dropbox.com/s/1vaz8wqhjwp8f44/Screenshot%202019-10-06%2014.05.23.png?raw=1">
		- 단점
			- 프로비저닝이 힘듬
			- 인스턴스는 사용하지 않는 경우에도 비용 지불
	- Container에 배포
		- <img src="https://www.dropbox.com/s/uk2grew7wf6639n/Screenshot%202019-10-06%2014.06.18.png?raw=1">
		- 단점
			- 컴퓨팅 시간이 아닌 자체 서버 가동 시간에 비용 지불
	- Serverless function으로 배포
		- <img src="https://www.dropbox.com/s/5pmln43totco37k/Screenshot%202019-10-06%2014.07.10.png?raw=1"> 
		- 단점
			- 전체 배포 패키지는 500MB, 5분 이내 실행, 3기가 메모리 이하 등을 만족해야 함(AWS Lambda)
			- CPU 연산만 가능
- Model Serving
	- 머신러닝 모델에 특화된 Web development options
	- 종류
		- Tensorflow Serving(Google)
		- Model Server for MXNet(Amazon) 
		- Clipper(Berkely RISE lab)
		- Saas solutions like ALgorithmia
	- Tensorflow Serving
		- <img src="https://www.dropbox.com/s/zd4e07dy5vgkzsy/Screenshot%202019-10-06%2014.10.15.png?raw=1">
	- MXNet Model Server
		- <img src="https://www.dropbox.com/s/hywjhqke0syd3hi/Screenshot%202019-10-06%2014.10.40.png?raw=1">
	- Clipper
		- <img src="https://www.dropbox.com/s/xc80kjx42epsfs3/Screenshot%202019-10-06%2014.11.18.png?raw=1">
	- Algorithmia
		- <img src="https://www.dropbox.com/s/380ddm9ggb2xfjg/Screenshot%202019-10-06%2014.11.29.png?raw=1">
- 핵심 정리
	- CPU inference를 수행할 경우, 더 많은 서버를 사용하거나 서버리스로 가는 방법이 있음
		- Dream : Lambda처럼 Docker를 쉽게 배포
		- 모델의 요구 사항에 따라 Lambda 또는 Docker가 제일 좋음
	- GPU inference를 수행할 경우, TF Serving 및 Clipper 같은 adaptive batching 방법이 유용함      
- Monitoring
	- <img src="https://www.dropbox.com/s/paq95fmjfq5v1ag/Screenshot%202019-10-06%2014.15.30.png?raw=1">
	- 무언가 잘못되면 알람해줘야 함
	- Cloud provider는 최근에 모니터링 솔루션을 가짐
	- 기록할 수 있는 모든 것을 모니터링할 수 있어야 함(예 : Data Skew)
	- 실습에서 보여줌
- Interchange
	- ONNX
	- <img src="https://www.dropbox.com/s/oe24uofv1ov0mgp/Screenshot%202019-10-06%2014.19.30.png?raw=1">
- Hardware / Mobile
	- Problems
		- 모바일 기기는 메모리가 작아서 연산하기 느림
			- network size를 줄이거나, trick, quantize weight해야 함
		- 모바일 딥러닝 프레임워크는 full version보다 덜 발전함
			- 네트워크 구조를 바꿔야할 수 있음
	- <img src="https://www.dropbox.com/s/1fauo080jov41mh/Screenshot%202019-10-06%2014.21.03.png?raw=1">
	- Tensorflow Options
		- Tensorflow Lite
			- 최근 솔루션으로 사용됨
			- operator가 제한되긴 함
		- Tensorflow Mobile
			- 더 많은(그러나 전체는 아닌) operator가 있음
	- Mobile
		- <img src="https://www.dropbox.com/s/9d8g4hfgwhkr4r8/Screenshot%202019-10-06%2014.22.09.png?raw=1">
	- NVIDIA for Embedded       
		- <img src="https://www.dropbox.com/s/s070zi3vppob26x/Screenshot%202019-10-06%2014.22.48.png?raw=1"> 

---


### Lecture 13 : Research Directions
- [발표 자료](https://fullstackdeeplearning.com/assets/slides/fsdl_13_research.pdf)
- 연구 방향성에 대해 알려주려는 시간
- <img src="https://www.dropbox.com/s/53v83cyoawnsjjd/Screenshot%202019-10-06%2014.33.25.png?raw=1">
- 다양한 연구 분야에 대해 쉽게 설명해주고, 참고하면 좋을 논문들을 제공했음
- 이 부분은 정리하기보다, 키워드를 캐치하고 추후에 따로 정리하는게 더 좋을듯


---

	
### Reference
- [Full Stack Deep Learning Homepage](https://fullstackdeeplearning.com/march2019)
- [Full Stack Deep Learning Labs Github](https://github.com/full-stack-deep-learning/fsdl-text-recognizer-project)