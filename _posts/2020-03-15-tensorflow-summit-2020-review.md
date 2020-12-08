---
layout: post
title:  "Tensorflow Summit 2020 세션 요약"
subtitle: "Tensorflow Summit 2020 세션 요약"
categories: mlops
tags: serving
comments: true
---


- Tensorflow Summit 2020 세션을 보고 정리한 글입니다





### Tensorflow Summit 2020
- 영상 볼 수 있는 곳 : [Youtube](https://www.youtube.com/playlist?list=PLQY2H8rRoyvzuJw20FG82Lgm2SZjTdIXU)
- 코로나로 인해 온라인으로 진행




### Keynote
- 사례 공유
	- Tensorflow js를 활용해골절 이미지 분류 모델 생성함
	- <img src="https://www.dropbox.com/s/h2n9uynmkhkqb8g/Screenshot%202020-03-14%2017.44.43.png?raw=1">
	- 그 외에도 [Disaster Watch](https://blog.tensorflow.org/2019/09/disaster-watch-crisis-mapping-platform.html)도 있음(자연 재해 예측하는 위기 매핑 플랫폼)
- Tensorflow의 지표
	- <img src="https://www.dropbox.com/s/i82ax0hniaqck00/Screenshot%202020-03-14%2017.46.23.png?raw=1">
- 2점대에서 더 쉽게 사용할 수 있도록 노력함
- Tensorflow 2.2
	- 속도와 성능 향상
	- Tensorflow 생태계와 호환성
	- 핵심 라이브러리의 안정성 향상
- TensorFlow Ecosytem
	- <img src="https://www.dropbox.com/s/gljxgttstb3vwig/Screenshot%202020-03-14%2017.52.50.png?raw=1">
	- <img src="https://www.dropbox.com/s/30y4pj8s19j2ttm/Screenshot%202020-03-14%2017.53.14.png?raw=1">
- Tensorflow 주요 기능
	- tensorflow.dev : 실험 결과를 URL로 변환
		- <img src="https://www.dropbox.com/s/br1bwz5ovxgin41/Screenshot%202020-03-14%2017.57.57.png?raw=1">
	- tensorflow data set 
	- 각종 추가 기능 
		- <img src="https://www.dropbox.com/s/6g4w1v5x60v0908/Screenshot%202020-03-14%2017.58.36.png?raw=1">
- Production
	- 프러덕션 변환하는 것을 어렵지 않게 만드려고 함
	- TensorFlow Hub에 많은 모델이 저장됨
	- TFX를 사용해 프러덕션화
	- Google Cloud AI Platform Pieplines 추가
		- <img src="https://www.dropbox.com/s/wp00rwfam95ydvl/Screenshot%202020-03-14%2018.01.09.png?raw=1">
	- TPU 지원
- Deployed everywhere
	- Tensorflow Lite 성능 개선
	- 안드로이드 스튜디오에서 쉽게 tf lite 모델의 자바 클래스 생성
	- TFRT : 최고의 성능을 제공하기 위해 노력
		- <img src="https://www.dropbox.com/s/s04f98ikj2xp5c1/Screenshot%202020-03-14%2018.05.23.png?raw=1">
- Responsible AI
	- 아래 5가지를 정의함
	- <img src="https://www.dropbox.com/s/jsayvnxmaomptex/Screenshot%202020-03-14%2018.07.56.png?raw=1">
	- 고민 포인트
		- <img src="https://www.dropbox.com/s/bo1hpogmtxwneuh/Screenshot%202020-03-14%2018.12.58.png?raw=1">
	- 다양한 도구들
		- 앞에서 정의한 5가지를 알 수 있도록 도와주는 도구들. 이미 아는 것들도 있고, 새로운 것들도 있음. 추후 체크 필요
		- <img src="https://www.dropbox.com/s/uk8unkrwua2opjo/Screenshot%202020-03-14%2018.13.43.png?raw=1">
- 커뮤니티
	- 커뮤니티에 대한 이야기와 TensorFlow 팀과 관계를 맺을 수 있는 기회 설명함
	- 다양한 교육을 마련하고 있음. AI 대중화에 신경쓰는듯
	- 자격증
		- [링크](http://tensorflow.org/certificate)에 자세히 나와있음. 아마 많은 분들이 시도하실듯
		- <img src="https://www.dropbox.com/s/a2ckaihwsfxw2cm/Screenshot%202020-03-14%2018.17.17.png?raw=1">

---


### Tensorflow Hub: Making model discovery easy
- 즉시 활용할 수 있도록 여러 딥러닝 모델을 저장해둠
- 이미지, 텍스트, 비디오, 오디오 관련 자료들이 저장됨
- 개인 데이터에 Transfer learning 하도록 프리트레인 모델을 제공하고 어디서든 deploy할 수 있도록 지원
- 각종 설명서와 스니펫이 저장됨. [링크](https://tfhub.dev/s?subtype=module,placeholder)에서 확인할 수 있음
	- <img src="https://www.dropbox.com/s/dlvfqrjowfs9zo2/Screenshot%202020-03-14%2018.22.37.png?raw=1">
- 예시
	- 정말 간단히 모델 사용할 수 있음
	- <img src="https://www.dropbox.com/s/787thnr7zudvv3s/Screenshot%202020-03-14%2018.23.14.png?raw=1">
- 검색 기능 강화함
- tfhub.dev에 기여하려면 [Github](https://github.com/tensorflow/hub/tree/master/tfhub_dev) 참고


---

### Collaborative ML with TensorBoard.dev
- [공식 문서](https://tensorboard.dev/)
- 머신러닝에서 협업이 필요한 이야기를 하며, 더 협업을 잘 하는 방법에 대해 말함
- 기존 텐서보드는 그림으로만 공유할 수 있었음. 이젠 TensorBoard.dev에 실험 결과를 업로드하고 쉽게 공유할 수 있음
- [Offically Supported TensorFlow 2.1 Models on Cloud TPU](https://github.com/tensorflow/models/blob/master/official/README-TPU.md)에 가면 Tensorboard.dev metric을 볼 수 있도록 링크걸어둠
- 이제 논문 실험 결과를 공유하는 문화도 생길듯
	- <img src="https://www.dropbox.com/s/0quutm5nk90r8c2/Screenshot%202020-03-14%2018.36.03.png?raw=1">
- 로그를 찍고 Upload하는 방법
	- 간단함
	- <img src="https://www.dropbox.com/s/7zsxntbspf8ace4/Screenshot%202020-03-14%2018.36.50.png?raw=1">
- 보안 관련한 옵션은 지금 없는건지 궁금


---


### Performance profiling in TF 2
- 퍼포먼스 프로파일링해주는 도구에 대한 이야기
- 기능
	- <img src="https://www.dropbox.com/s/023kfm8c149gpug/Screenshot%202020-03-14%2018.40.50.png?raw=1">
- 텐서보드에 결합됨
- Overall Analysis
	- 어떤 부분에 얼마나 걸렸는지 알려줌
	- <img src="https://www.dropbox.com/s/o3zkk1x3mgfw5xy/Screenshot%202020-03-14%2018.41.35.png?raw=1">
- 그 외에 input time을 줄이기 위해 사용해보면 도구인 input_pipeline_analyzer, trace_viewer를 알려줌
- Input Pipeline Recommendation
	- <img src="https://www.dropbox.com/s/gflt5yn5ty32w83/Screenshot%202020-03-14%2018.44.29.png?raw=1">


### Research with TF
- 텐서플로우를 사용해 연구하는 방법
- controllable, flexible, composable
- Controlling state
	- `tf.variable_creator_scope`
		- context manager
		- <img src="https://www.dropbox.com/s/63auqp71jfy0v4p/Screenshot%202020-03-14%2019.36.48.png?raw=1">
		- 그 후 `tf.register_tensor_conversion_function`에서 사용
		- scope 정의
		- scope를 인자로 넣고 `tf.variable_creator_scope` 사용
	- 자신이 만든 레이어의 파라미터를 획득할 수 있음
- Compilation
	- 



### Differentiable convex optimization layers
- 스탠포드 박사님
- [Github](https://github.com/cvxgrp/cvxpylayers)
- Convex Optimization
	- 목표 : 제약 조건을 만족시키며 cost function을 minimze하는 variable x를 찾기
- Convex Optimization은 빠르게 풀리고, 정확하고 믿을만함
- [CVXPY](https://www.cvxpy.org/) 라이브러리가 최적화를 쉽게 해줌
	- Variable을 만들고, Objective를 만들고 Constraint을 주고 품
- 많은 곳에서 응용할 수 있음
	- 자율 주행 차량이 궤도를 생성하거나 실시간으로 경로를 추적할 때 사용
	- 우주선에서 로켓 착륙 궤도 생성함
	- 비행기 및 기타 물리적 구조물 설계에도 활용됨
- 지금까진 텐서플로우에서 convex optimization이 매우 어려웠음
	- 파라미터를 직접 선택하고 조정함
	- 하이퍼 파라미터 튜닝이라고 부를 수도 있음
	- 그라디언트 디센트 기반 튜닝을 선호해야 함
- 예시 코드
	- <img src="https://www.dropbox.com/s/3sou50cuipm4i18/Screenshot%202020-03-18%2023.20.57.png?raw=1">
- 자율주행 차 제어하는 예시
	- [논문](https://arxiv.org/abs/1912.09529)을 짧게 이야기해주심
- 참고 문서
	- [Does convex optimization belong to linear or nonlinear programming?](https://math.stackexchange.com/questions/833472/does-convex-optimization-belong-to-linear-or-nonlinear-programming)





### Scaling Tensorflow data processing with tf.data


### Making the most of Colab


### MLIR 


### TFRT

### TFX

### Tensorflow Enterprise



