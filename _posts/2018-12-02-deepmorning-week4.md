---
layout: post
title:  "딥모닝 4주차. PR12-017~021"
subtitle: "딥모닝 4주차. PR12-017~021"
categories: data
tags: paper
comments: true
---

- PR12 동영상을 하루에 1개씩 보는 “딥모닝” 스터디에서 본 영상을 정리하는 글입니다
- PR-017: Neural Architecture Search with Reinforcement Learning
- PR-018: A Simple Neural Network Module for Relational Reasoning (DeepMind)
- PR-019: Continuous Control with Deep Reinforcement Learning
- PR-020: Delving Deep into Rectifiers: Surpassing Human-Level Performance on ImageNet Classification
- PR-021: Batch Normalization 

---

## PR-017: Neural Architecture Search with Reinforcement Learning
- 2016년 논문

### Motivation for Architecture Search
- 뉴럴 네트워크를 디자인하는 것은 힘들고 튜닝할 때 노력과 시간이 많이 걸림
- Feature Engineering =>  Neural Network Engineering 패러다임으로 변화
- 딥러닝 구조를 만드는 딥러닝 구조

### Neural Architecture Search
- 핵심은 Configuration string
	- 한 layer의 configuration : Filter Width: 5, Filter Height : 3, Num Filters : 24
- RNN(controller)를 사용해 아키텍쳐를 명시하는 string 생성
- RNN을 수렴하게 학습해서, string의 정확도를 알 수 있음
- <img src="https://www.dropbox.com/s/w9pn495nxco8y2q/Screenshot%202018-11-26%2009.20.18.png?raw=1">

- RNN
	- sequence to sequence
- Training with REINFORCE
	- <img src="https://www.dropbox.com/s/xnonxo86iqql3d4/Screenshot%202018-11-26%2009.22.14.png?raw=1">
- Distributed Training 사용

### 아키텍쳐 세부
- Generated Convolutional Network from Neural Architecture Search
	- Skip Connection을 너무 좋아함
	- 직사각형 필터를 좋아함(7x4)
	- 첫 convolution 이후 모든 layer와 연결하는 것을 좋아함
- Recurrent Cell Prediction Method
	- LSTM, GRU와 비슷한 RNN cell을 찾기 위해 search space 생성



---

## PR-018: A Simple Neural Network Module for Relational Reasoning (DeepMind)

- RN이라고도 불림
- 키워드
	- Plug and Play Module : 그냥 꽂으면 된다
	- Achieved SOTA, super-human performance : 사람보다 더 잘한다

### Reasoning the Relations
- 관계를 reasoning하는 것이 중요 
	- 키 차이가 가장 큰 나무는?
	- 나무간의 관계를 잘 알아야 함

### The difficulty of reasoning relations
- Symbolic approaches
	- 관계를 로직 language로 정의해서 수식적으로 풀이
	- 정리하기 쉽지 않고 많은 문제에선 힘듬. 확장성 부족
- Statistical learning
	- 어텐션같은 방법
	- data-poor problem이 존재

### Relation Networks
<img src="https://www.dropbox.com/s/bq1zkhdfn3wweo3/Screenshot%202018-11-27%2008.14.49.png?raw=1">

- Object들이 있고, g라는 relation 함수가 존재
	- 모든 조합을 함수에 통과시키고, Sum
	- 그 후 f라는 함수에 통과
- <img src="https://www.dropbox.com/s/8rrkjbzebxkdgyj/Screenshot%202018-11-27%2008.15.49.png?raw=1">

### RN의 장점
- 관계를 잘 추론(Infer)
	- 실제 관계를 몰라도 됨
	- smantic 몰라도 됨
	- 의미 몰라도 됨
	- All-to-all이 아닌 일부만 해도 됨
- RN은 g 함수 하나로 통과시켜서 compute도 좋음
- Order invariant함

### Model - for VisualQA
<img src="https://www.dropbox.com/s/lvjlv1i1tp51wtg/Screenshot%202018-11-27%2008.21.10.png?raw=1">

### Model - for Natural Language
<img src="https://www.dropbox.com/s/8hx1rrwr1zeen4q/Screenshot%202018-11-27%2008.24.05.png?raw=1"> 
	

### Thoughts
- Simple components to apply
- Try with other existing models
- DeepCoding with RN? 가능할까?


---

## PR-019: Continuous Control with Deep Reinforcement Learning

- Deep Q러닝의 뒷 이야기

### Introduction
- Deep Q Network
	- High-dimensional observation spaces
	- discrete and low-dimensional action spaces  
- High-dimensional continuous action space에 Deep RL을 어떻게 구현할 수 있을까?
- Q-Learning
	- discrete한 것에 맞는 방법

	
### Background
<img src="https://www.dropbox.com/s/l24xmf40iwrno09/Screenshot%202018-11-28%2008.29.12.png?raw=1">

- DQN
	- <img src="https://www.dropbox.com/s/12934uzd1k8sfrh/Screenshot%202018-11-28%2008.30.38.png?raw=1">

	
### Algorithm
- DQN을 continuous action space에 바로 적용이 가능할까?
	- No!
	- argmax로 주어지는데, action space가 high dimensional에서 optimal을 찾느 느린 문제가 됨
- Actor-Critic Approach based on Deterministic Poligy Gradient를 사용!
	- Critic Network (Value) : DQN처럼 구성
	- Actor Network (Policy) : loss function을 2번으로 나눔. update를 2개의 곱으로 표현(chain rule)

- 전체 네트워크 구성
	- <img src="https://www.dropbox.com/s/4topd8ufrxos3yz/Screenshot%202018-11-28%2008.38.02.png?raw=1">
	
---
	
## PR-020: Delving Deep into Rectifiers: Surpassing Human-Level Performance on ImageNet Classification

- Parameter ReLU
- Weight 초기화를 다른 방법으로!

### Definition
<img src="https://www.dropbox.com/s/odmp8wbnocq5j0g/Screenshot%202018-11-29%2021.55.23.png?raw=1">

- a값을 학습!
- no weight decay
	- decay를 주면 일반 ReLU처럼 나온다
- 초기 a값을 0.25로 시작

### Weight Initialization
- Zero는 매우 나쁨
- Constant도 매우 나쁨
- Use small random values!
	- varinace를 어떻게 둘 것인가?
- 이 논문이 나오기 전엔 Xavier Initialization을 많이 씀
	- Variance를 2/(n_in+n_out)

### Initialization in PReLU Case
<img src="https://www.dropbox.com/s/e8b7webho14fqvc/Screenshot%202018-11-29%2022.34.28.png?raw=1">

### Summary
- PReLU 제안
- Initialization method 제안 
	
	
---

## PR-021: Batch Normalization 
- DNN : 학습이 어려움
	- 많은 파라미터 때문
	- weights의 작은 변화가 upper layer에 다른 값을 전달하게 됨 	
	- 이런 현상을 Interval Covariate Shift라고 부름
	- Training, Testing 분포가 다르면 학습이 잘 안되는것처럼 input이 달라지면 학습이 잘 안됨
- Batch Normalization
	- 분포의 변화가 적으면 학습이 잘 될것이다 라는 가정
	- Scale을 조절 => 기존 분포와 유사하게 됨
	- Dropout을 쓸 필요가 없음

### 결론
- Internal covariate shift를 해결하기 위해 제안
- 장점
	- 빠른 속도
	- Less careful initialization
	- Regularization effect

### Other methods
- ELU는 batch norm이 없어도 됨
- SELU(Self-Normalizing Neural network) 	
	

	
	  
