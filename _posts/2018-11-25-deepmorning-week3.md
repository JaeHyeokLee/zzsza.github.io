---
layout: post
title:  "딥모닝 3주차. PR12-012~016"
subtitle: "딥모닝 3주차. PR12-012~016"
categories: data
tags: paper
comments: true
---

- PR12 동영상을 하루에 1개씩 보는 “딥모닝” 스터디에서 본 영상을 정리하는 글입니다
- PR-012 : Faster R-CNN
- PR-013 : Domain Adversarial Training of Neural Network
- PR-014 : On Human Motion Prediction using RNNs 
- PR-016 : Yon Only Look Once(YOLO): Unified, Real-Time Object Detection

---

## PR-012 : Faster R-CNN
- 블로그 [포스팅](https://zzsza.github.io/data/2018/05/09/Faster-RCNN-review/) 참고

## PR-013 : Domain Adversarial Training of Neural Network
- Source 데이터를 받고 실제 Test 데이터가 들어왔을 때 잘 classification했으면 좋겠음
	- 문제를 단순히 봐서 라벨이 2개밖에 없는 binary classification 문제로 설명
- empirical error를 정의
- 모든 데이터의 true error가 우리가 가진 sample 데이터의 empirical error + 모델의 복잡도로 upper bound된다고 증명
	- empirical 에러를 줄일 때, 모든 unseen 데이터에 대해 bound가 성립
	- Occam razor principle
- 일반적인 supervised learning의 setting : Training과 test의 domain이 같다고 가정
- 예측할 때 새로운 domain에 적용하는 경우가 많음
	- high quality - low quality
	- posed - in the wild
- Domain Adaptation
	- 소스 도메인에만 label
	- 새로 올 target 도메인은 다를 수 있음

### 3 main class
- 샘플 데이터와 타겟 데이터들의 분포가 bias가 있으면 bias를 조절해서 서로 맞춤
- feature 도메인을 학습 잘 해서, new representation space를 찾음
	- 여기를 주로 파악할 예정
- Ajustement/Iterative 방법 
	- pseudo-labeld 정보로 통합

### 예시
<img src="https://www.dropbox.com/s/sw9zwo5zkonzkdb/Screenshot%202018-11-20%2009.26.19.png?raw=1">

- 도메인이 다른 곳에서 사용
- 공통적으로 잘 작동할 feature를 찾기

<img src="https://www.dropbox.com/s/rbd3t5h3che1rfl/Screenshot%202018-11-20%2009.28.55.png?raw=1">

- 1 step

### Error
- 기존 전략 : 최대한 적은 파라미터로 training error가 최소인 모델을 찾자
- DANN에선 불가능



---

## PR-014: On Human Motion Prediction using RNNs 
- 선정 이유
	- RNN을 사용한 Human Motion을 공부하기 위해

### Motion forecasting
- 쉽게 말하면 Generation
	- Given을 주고 예측
	- 풀 스윙이 있으면 그 다음 모델이 어떤 모습일지?
	- Senetence completion과 유사한 듯
		- a high-dimensional and nonlinear version of sentence completion

### Background
- RNN
	- 뉴럴넷에서 히든 뉴런에 recurrent한 뉴런이 존재
	- Gradient vanishing / exploding 문제 존재
- LSTM/GRU
	- 게이트 유닛
	- 그 전의 값을 pass할지 넘길지 등을 담당
	- 4배의 weight와 파라미터가 들어감
	- GRU : LSTM보다 계산이 간단한데 비슷한 성능
- 다양하게 사용되는 RNN
	- one to one
	- one to many
	- many to one
	- many to many
	- many to many  	

### 간단한 접근
- joint angle 데이터를 모두 LSTM에 적용
	- Recurrent Network Models for Human Dynamics 논문
	- ERD : Encoder-Recurrent-Decoder
	- gradually add noise 
		- 발이 삐끗하면 새로운 지역이라 이상한 행동을 할텐데, 애초에 학습시 noise를 추가해 보정
		- 단, noise를 잘 넣어주는게 어려움
- Structural-rnn : Deep learning on spatio-temporal graphs
	- 머리, 다리, 팔 패턴을 다르게 학습하자! (위 방법은 모든 것을 한번에 넣었음)
- Hierarchical RNN

### Motion Forecasting using RNN
- (short term) given ground truth랑 예측된 것의 앵글 포인트를 에러 측정 
- (long term) 사람이 눈으로 봐서 그럴듯한지?
- 기존 RNN은 long term에서 이상하게 되는 문제, short term은 50까지 학습했으면 51을 할 때 jump가 생김
- Noise 스케쥴링
	- 테스트할 때 노출될 수 있는 에러를 미리 예측, 에러도 누적
	- 점점 노이즈도 누적 
	- 일종의 curriculum learning
		- 쉬운 예제부터 어려운 예제까지
	- 이 논문에선 노이즈 스케쥴링 하지 않음(Residual에서 튀는 값들이 없어짐)
	- 더 심플한 모델 제안하고 모든 데이터를 넣었음  

### Proposed solution
- Sequence to sequence 아키텍쳐
- Residual 아키텍쳐
- 마지막 프레임에 안 움직이는거랑 비교하면 어떨까? (신선한 아이디어)

### 결론
<img src="https://www.dropbox.com/s/26rcwu9upqnbusv/Screenshot%202018-11-21%2009.33.18.png?raw=1">




---

## PR-015


---

## PR-016 : Yon Only Look Once(YOLO): Unified, Real-Time Object Detection

- 1번만 봄, 여태까지 나온 것을 다 합쳤다, 실시간이다
- Faster RCNN에서 성능을 줄이더라도 속도를 늘리겠다!

### Main Concept
- Detection 문제를 regression 문제로 바꿈
- 1번의 feed forward(이미지 전체를 한번 본다)
- 다른 도메인에서도 잘 된다(General representation)

### Object Detection as regression problem
- 과거
	- window moving으로 점점 찾고, 합치는 방식
- 기존
	- 한번에 찾고 끝내보자
	- Unified Detection
		- 이미지를 S x S 그리드로 나눔
		- grid cell, bounding box를 x,y,w,h로 표현
		- 물체가 많이 포함할 것 같은 곳이 더 찐함 
		- <img src="https://www.dropbox.com/s/h3uiw3vmgvq7oh6/Screenshot%202018-11-23%2009.09.35.png?raw=1">

### Network Design
- Inception v1
- Modified GoogLeNet
- 1x1 reduction layer

### Training
<img src="https://www.dropbox.com/s/h5qseb54u20zhh6/Screenshot%202018-11-23%2009.15.57.png?raw=1">

- Feature Extractor, Object Classifier로 구성
- 1) ImageNet 1000개 데이터로 pretrain
- 2) Network on Convolutional Feature Maps
- 3) 7x7x30 텐서가 나옴
- 그리드 셀
	- <img src="https://www.dropbox.com/s/7u5x7fdqqry730m/Screenshot%202018-11-23%2009.16.56.png?raw=1">
	- <img src="https://www.dropbox.com/s/xxb9ldvf3aojqvh/Screenshot%202018-11-23%2009.18.04.png?raw=1">  


### Loss Function
<img src="https://www.dropbox.com/s/jx0y74yq6okqy4y/Screenshot%202018-11-23%2009.21.17.png?raw=1">

<img src="https://www.dropbox.com/s/19q2fxv7r8gcerz/Screenshot%202018-11-23%2009.22.30.png?raw=1">

- SSE(sum-squared-error)를 사용해 단순화

### Limitation of YOLO
<img src="https://www.dropbox.com/s/y62ov0m3a4c4gvq/Screenshot%202018-11-23%2009.26.17.png?raw=1">

