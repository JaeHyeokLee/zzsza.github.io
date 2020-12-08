---
layout: post
title:  "머신러닝 Experiment Management 쉽게 하기(feat. neptune ai)"
subtitle: "Machine Learning experiment management using neptune ai"
categories: mlops
tags: experiment
comments: true
---

- neptune ai를 활용해 머신러닝 실험 관리를 더 편하게 하는 방법에 대해 작성한 글입니다
	- 키워드 : Machine learning experiment management

---

### 머신러닝 실험 관리
- 머신러닝 실험 관리
	- 모델링을 할 때, 파라미터를 바꿔가며 실험함
	- 1) 초반엔 스프레드시트에 적어두기도 하지만, 실험이 많아지고 복잡해지면 노트북에서 돌린 내용을 스프레드시트에 누락할 수도 있고, 잘못 기록할 수 있음
	- 2) 필요할 경우 각 실험에서 나온 이미지 파일을 따로 저장해둬야 함
	- 3) 여러 모델을 한번에 비교하고 싶은 경우 1)이나 2)를 직접 눈으로 보고 비교해야 함
	- 이런 작업을 좀 더 수월하게 할 수 있도록 Machine Learning Experiment Management Tool들이 개발됨
- 머신러닝 실험 관리에 대한 내용은 제 블로그의 [머신러닝 실험을 도와줄 Python Sacred 소개](https://zzsza.github.io/mlops/2019/07/21/python-sacred/)에 간단히 정리해둠
	- Sacred는 직접 설치하고, mongodb 연결하는 등 약간의 개발 지식이 필요함
	- 기타 도구들은 [reddit](https://www.reddit.com/r/MachineLearning/comments/bx0apm/d_how_do_you_manage_your_machine_learning/)에 많이 나옴
- 이번에 소개할 neptune ai는 **개발 지식이 전혀 필요없고, 코드 몇줄만 추가하면 끝남**

<br />
---

### neptune ai
- [홈페이지](https://neptune.ai/)
- "Keep all of your ML stuff organized to advance faster"
	- 모든 머신러닝 재료들을 저장하고 빠르게 접근할 수 있도록 만듬
	- 모든 것들이 백업되고, 다른 사람과 공유할 수 있음
- 주요 기능 : 밑에서 자세히 다룸
	- 1) 실험 과정에서 나오는 각종 데이터 저장
	- 2) 실험 버전 관리, 실험 비교
	- 3) 노트북 버전 관리, Diff 확인 가능
	- 4) 협업을 위한 위키 기능
- 단, 서비스를 제공하고 오픈소스는 아님
- 여러 Framework와 도구와 결합 가능
	- sklearn, pytorch, keras, tf, fast.ai, sacred, mlflow, lgbm 등 다양한 라이브러리 지원
	- <img src="https://www.dropbox.com/s/r5tk0vej7ti80pp/Screenshot%202020-03-22%2000.10.13.png?raw=1">
- 가격
	- 오픈소스가 아닌 서비스를 제공하고 있기 때문에 가격을 확인할 필요가 있음
	- 좋은 점은 개인 목적(Individual)인 경우 Free로 사용할 수 있음. 따라서 개인 프로젝트 또는 Kaggle을 할 때 유용할 듯
	- 팀에서 사용할 경우 프로젝트 1개만 제공하고, 저장 용량이 10GB임. 실험 관리용으로만 쓴다면 충분히 괜찮은 용량이고 구입하기 전 테스트할 목적으로 사용해도 좋음
	- Team Pro는 월 79달러
	- <img src="https://www.dropbox.com/s/25sqhu0v8fajk97/Screenshot%202020-03-21%2012.13.30.png?raw=1">
	- 스타트업에겐 50%의 할인을 제공하고 있고, 연구자나 비영리적 조직에서 무료로 사용할 수 있음
	- <img src="https://www.dropbox.com/s/9g9nvzu7957bnk8/Screenshot%202020-03-21%2012.17.50.png?raw=1">
- Python이 아닌 R에서도 사용할 수 있음. [How to track and collaborate on R experiments with Neptune](https://medium.com/neptune-ai/how-to-track-and-collaborate-on-r-experiments-with-neptune-85d4b8c19596) 참고
- 이제 주요 기능을 neptune ai에서 제공하는 샘플 프로젝트를 보며 설명

<br />
---

### 1) 실험 과정에서 나오는 각종 데이터 저장
- 프로젝트 메인 페이지
	- [credit default prediction](https://ui.neptune.ai/neptune-ai/credit-default-prediction/experiments?viewId=standard-view) 참고함
	- 각종 실험에 대한 정보가 저장됨. 실험에서 사용한 노트북도 볼 수 있음
	- <img src="https://www.dropbox.com/s/g9nzmu0boejya8h/Screenshot%202020-03-21%2012.32.07.png?raw=1">
- 커스텀해서 보고 싶은 지표를 앞에 둘 수 있고, 특정 태그만 있는 실험을 검색할 수 있음
	- <img src="https://www.dropbox.com/s/kzpnv1632ww3364/Screenshot%202020-03-21%2012.35.14.png?raw=1">
- 실험 CRED-81을 클릭하면 아래와 같이 나옴
	- 왼쪽에 보면 Charts, Logs, Monitoring, Artifacts, Source code, Parameters 등이 있음
	- <img src="https://www.dropbox.com/s/zxuh4gcqe79ky7j/Screenshot%202020-03-21%2012.37.30.png?raw=1"> 

- 각종 데이터 저장
	- Metric
		- Chart에 저장
	- Hyperparameters
		- Parameters에 저장
	- Data versions
		- Artifacts에 저장
	- Model files
		- Artifacts에 저장
	- Images
		- Logs에 저장
	- Source Code
		- Source Code에 저장

- Charts
	- 사용자가 지정한 iter별 auc, production precision을 볼 수 있음
	- <img src="https://www.dropbox.com/s/zxuh4gcqe79ky7j/Screenshot%202020-03-21%2012.37.30.png?raw=1">	
- Logs
	- 각종 로그가 저장됨
	- 좋다고 생각한 부분은 이미지도 저장할 수 있는 점. matplotlib의 figure를 넘기면 저장됨
	- <img src="https://www.dropbox.com/s/zuc7741ycvf8lya/Screenshot%202020-03-21%2012.40.25.png?raw=1">
- Monitoring은 CPU, RAM 사용량, stderr, stdout이 보임
- Artifacts
	- 사용자가 저장한 모델 pkl 파일, train, valid data 파일이 저장됨
	- <img src="https://www.dropbox.com/s/yx0qt321z0cylct/Screenshot%202020-03-21%2012.41.37.png?raw=1">
- Source code
	- 소스 코드를 선택하면 저장된 파일을 확인할 수 있음
	- <img src="https://www.dropbox.com/s/m7ipw7oqgkr121r/Screenshot%202020-03-21%2012.42.27.png?raw=1">
- Parameters
	- 사용자가 초기에 세팅한 파라미터가 저장됨
	- <img src="https://www.dropbox.com/s/jqcv9y1f9yciuah/Screenshot%202020-03-21%2012.42.55.png?raw=1">
- Details
	- 실험 관련 메타 데이터가 저장됨
	- <img src="https://www.dropbox.com/s/sahg1fvxz7qh9lt/Screenshot%202020-03-21%2012.43.22.png?raw=1">

<br />


### 2) 실험 버전 관리, 실험 비교
- Experiment 메인에서 여러 실험을 체크하고 Compare 클릭
	- <img src="https://www.dropbox.com/s/pd2dfeznh9kysqz/Screenshot%202020-03-21%2022.34.13.png?raw=1">
- Metric과 Chart를 확인할 수 있음
	- <img src="https://www.dropbox.com/s/r78ibmqg36huyy5/Screenshot%202020-03-21%2022.41.24.png?raw=1"> 
	- <img src="https://www.dropbox.com/s/li32oerhrtryw8i/Screenshot%202020-03-21%2022.40.58.png?raw=1">

<br />


### 3) 노트북 버전 관리, Diff 확인 가능
- 프로젝트의 Notebooks를 클릭하면 아래와 같이 업로드한 노트북이 보임
	- 여러 노트북 파일을 업로드하고, 노트북 파일을 버전 관리할 수 있음
	- <img src="https://www.dropbox.com/s/n594szht9dfmakw/Screenshot%202020-03-21%2022.43.01.png?raw=1">
- Checkpoint로 버전 관리
	- <img src="https://www.dropbox.com/s/czetbovx1gqvdka/Screenshot%202020-03-21%2022.55.59.png?raw=1">
- 노트북 파일 Diff 확인
	- <img src="https://www.dropbox.com/s/6orhspcb7h9ldfq/Screenshot%202020-03-21%2022.57.30.png?raw=1">

<br />

### 4) 협업을 위한 위키 기능
- 프로젝트의 Wiki를 클릭하면 아래와 같이 작성한 문서를 확인할 수 있음
	- <img src="https://www.dropbox.com/s/ghknbpb2od8byui/Screenshot%202020-03-21%2022.58.33.png?raw=1">


<br />

---

## 사용 방법

### 회원 가입 및 프로젝트 확인
- [링크](https://neptune.ai/register)를 통해 회원 가입
- 기본적으로 생성된 프로젝트가 있음
	- sandbox
	- <img src="https://www.dropbox.com/s/4w7tuys4ddze0vg/Screenshot%202020-03-21%2023.03.29.png?raw=1">
- sandbox를 클릭하면 실험 및 노트북 등을 볼 수 있음
	- <img src="https://www.dropbox.com/s/qxll2fpvf1745yb/Screenshot%202020-03-21%2023.05.58.png?raw=1">
- Show me how를 클릭해서 사용 방법 확인
	- 1) Neptune client library 설치

	```
	pip3 install neptune-client
	```
	
	- 2) API Token 확인
		- API Key를 기억하거나, 환경 변수에 추가하기 
		- <img src="https://www.dropbox.com/s/iolnh5fkssbed7h/Screenshot%202020-03-21%2023.07.41.png?raw=1">

<br />
---

### Notebook 작성	
- neptune init
	
	```
	import neptune

	PARAMS = {
	    'num_threads': 4,
	    'seed':777,
	    'metric':'auc'
	}
	
	neptune.init('user_id/sandbox', api_token='토큰 값 입력')
	 ```
- 실험 생성
	- 실험 객체 생성시 https://ui.neptune.ai/~~~ 이런 URL이 출력됨
	
	```
	neptune.create_experiment(name='ml_modeling', params=PARAMS)
	neptune.append_tag('test_tag')
	```	
	
- 코드 중간 중간에 저장하고 싶은 metric, artifact 등을 저장

	```
	figure = plt.figure()
	
	plt.plot(loss, label="loss")
	plt.xlabel('epoch')
	plt.ylabel('loss')
	
	neptune.log_metric('loss', loss)
	neptune.log_image('loss_figure', figure)
	neptune.log_artifact('model_weights.h5')
	neptune.set_property(‘data_version’, sha1(data_train))
	```	

- 실험을 닫기(종료)

	```
	neptune.stop()
	```
	
- 실험 객체 생성, 닫는게 귀찮으면 with로 감싸주면 됨

	```
	with neptune.create_experiment(name='ml_modeling', params=PARAMS) as exp:
		experiment code
		neptune.log_metric('loss', loss)
		neptune.log_image('loss_figure', figure)
		neptune.log_artifact('model_weights.h5')
	```


<br />
---

### neptune ai에 노트북 업로드하기(notebook 확장 프로그램 설치)
- jupyter notebook 파일을 쉽게 업로드할 수 있도록 Jupyter Extension을 만듬
- 설치

	```
	pip3 install neptune-notebooks
	```

- Enable

	```
	jupyter nbextension enable --py neptune-notebooks
	```

- Jupyter Notebook 켜서 확인
	- Configure 클릭
	- <img src="https://www.dropbox.com/s/bvwytxqez6ocwex/Screenshot%202020-03-21%2023.59.07.png?raw=1">
- API Token 복사 후 붙여넣기
- 이제 Upload, Download가 활성화됨
	- <img src="https://www.dropbox.com/s/4rg3qepap81p29g/Screenshot%202020-03-22%2000.00.15.png?raw=1">
- Upload 클릭 후 체크포인트 생성
	- <img src="https://www.dropbox.com/s/i47lw6qenm39p5k/Screenshot%202020-03-22%2000.01.15.png?raw=1">
- neptune ai UI에서 확인 가능
	- <img src="https://www.dropbox.com/s/hp1aiv4ch0le43s/Screenshot%202020-03-22%2000.02.03.png?raw=1">
- 아까 업로드한 노트북에서 print를 수정해서 다시 upload함
	- 동일한 이름에서 checkpoint 업로드
	- <img src="https://www.dropbox.com/s/yp4ujjmajqvadyp/Screenshot%202020-03-22%2000.03.26.png?raw=1">
- 다시 UI에 가보면 체크포인트 2개를 확인할 수 있음
	- <img src="https://www.dropbox.com/s/91hwj2kegxd2k0w/Screenshot%202020-03-22%2000.04.04.png?raw=1">
- Compare 클릭 후 2개를 비교
	- <img src="https://www.dropbox.com/s/dgsagyflc9ihr1x/Screenshot%202020-03-22%2000.04.40.png?raw=1">


<br />

### 결론 및 정리
- neptune ai는 머신러닝 실험 관리할 때 유용함
- 꼭 머신러닝이 아니여도 데이터 기반해서 여러 시뮬레이션을 돌릴 때 활용하면 좋음
- 오픈소스가 아니지만, 개인 계정은 무료로 사용 가능
	- Kaggle 등의 대회에 참여할 때 활용해보길 추천
- 노트북 업로드, 모델 파일 저장, 이미지 저장, 실험 비교 등 다양한 기능을 제공함
	- Production 환경에 가기 전, 여러 실험을 돌릴 때 사용하면 좋은 도구
- 설치가 필요없고 간단한 코드 몇 줄만 추가하면 되서 사용성이 좋음
- 회사에서 사용한다면, Team Plan으로 테스트해본 후 가입하면 좋을듯
	- 실험 플랫폼을 만들어줄 엔지니어링팀이 있다면 이 프로그램을 먼저 사용해보고 아이디어를 얻을 수 있을듯




<br />

---
### Reference
- [Neptune AI Document](https://docs.neptune.ai/)
- [Colab에서 neptune 사용하는 코드](https://colab.research.google.com/github/neptune-ai/neptune-colab-examples/blob/master/neptune_test_run.ipynb)
- [Kaggle ieee fraud detection 대회를 neptune 사용해 파라미터 관리한 코드](https://github.com/neptune-ai/kaggle-ieee-fraud-detection)
- neptune ai 블로그에 좋은 글이 많음
	- [15 Best Tools for Tracking Machine Learning Experiments](https://neptune.ai/blog/best-ml-experiment-tracking-tools)
	- [Experiment Management: How to Organize Your Model Development Process](https://neptune.ai/blog/experiment-management)
	- [optuna vs hyperopt](https://neptune.ai/blog/optuna-vs-hyperopt)
	- [Scikit Optimize: Bayesian Hyperparameter Optimization in Python](https://neptune.ai/blog/scikit-optimize)