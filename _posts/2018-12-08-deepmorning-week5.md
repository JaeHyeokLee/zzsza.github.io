---
layout: post
title:  "딥모닝 5주차. PR12-022~025"
subtitle: "딥모닝 5주차. PR12-022~025"
categories: data
tags: paper
comments: true
---

- PR12 동영상을 하루에 1개씩 보는 “딥모닝” 스터디에서 본 영상을 정리하는 글입니다
- PR-022: InfoGAN 
- PR-023: YOLO9000: Better, Faster, Stronger
- PR-024: Pixel Recurrent Neural Network
- PR-025: Learning with side information through modality hallucination
- PR-026는 생략

---

## PR-022: InfoGAN 
- 선정 이유
	- Cool idea
	- Cool 수학적 백그라운드
	- 결과도 좋음
	- Cool 구현체
	- 다른 paper들과 연결

### GAN
- Generator : noise
- Discriminator : fake 이미지 구분

### Information theory
- Entropy
	- 확률분포 p를 최적의 coding scheme으로 coding했을 때 필요한 bit의 수
		- p가 얼마나 무질서하게 퍼져있는지  
- Cross Entropy
	- true distribution p에 대한 coding scheme으로 unnatural distribution q를 coding했을 때 필요한 bit의 수
- Mutual information
	- X를 알 때 Y의 entropy가 어떻게 변할까?
	- 두 확률분포가 서로 얼마나 의존하고 있을까?
	- 서로 독립이면 $$I(X; Y) = 0$$


### InfoGAN
- Motivation
	- GAN을 학습하면 representation이 어떻게 구성되어있는지 알 수 없음(disentangled) => 활용하기 힘듬
	- 유의미한 확률 분포를 나타내도록 뽑아낼 수 있지 않을까?
- Main idea
	- GAN처럼 G, D를 학습하되 latent code c~P(c)에 대해서 c와 G(z,c)가 의존 관계에 놓이도록 학습하자!
- Variational Mutual Information Maximization
	- Q(c|x)를 가져와 근사
	- Expectation을 처리하기 위해 Lemma 5.1 사용
- Lemma 5.1
	- Random variables X, Y and function f(x,y) under suitable regularity conditions 	 
	- Reconstruction loss!
	- <img src="https://www.dropbox.com/s/et12zlihe48ci6u/Screenshot%202018-12-03%2009.14.41.png?raw=1">
- <img src="https://www.dropbox.com/s/nadf19g2ckr2i3m/Screenshot%202018-12-03%2009.18.40.png?raw=1">
- <img src="https://www.dropbox.com/s/rtvi2yq4126ak10/Screenshot%202018-12-03%2009.18.53.png?raw=1">
- Disco GAN 자료
	- <img src="https://www.dropbox.com/s/wddgnp04jywkjyz/Screenshot%202018-12-03%2009.22.40.png?raw=1">
	- reconstructure loss가 발생해서 oscillation
 	
	
---
	
## PR-023: YOLO9000: Better, Faster, Stronger
- Better (YOLOv2)
	- Batch normalization
	- High resolution classifier
	- Convolution with anchor boxes
	- Dimension clusters
	- Direct location prediction
	- Fine-grained features
	- Multi-scale training 
- Faster (YOLOv2)
	- Darknet-19
	- Training for classification
	- Training for detection 	
- Stronger (YOLO9000)
	- Hierarchical classification
	- Dataset combination with Wort-tree
	- Joint classification and detection

### Introdcution & Motivation
- Detection은 class 개수가 너무 작음
- Classification만큼 올리고 싶은데 힘든 이유가 labeling이 어렵기 때문
	- 따라서 Detection과 Classification 데이터를 잘 짬뽕해서 만들려고 함
- Joint training algorithm

### YOLO
- 성능이 떨어지는 편
- Recall이 낮음

### Better
- Batch Normalization 사용
- High Resolution Classifier
	- 448x448 fine tuning
- Convolution with Anchor Boxes
	- 앵커 박스 도입
	- 416x416 : 홀수, 13x13이면 이미지의 가운데!
	- 모든 앵커박스마다 class, objectness를 예측
- Dimension Clusters
	- 어떤 anchor 박스가 좋을까?  	
	- IoU가 제일 큰 곳으로 k means
- Direction Location Prediction
	- 이미지 전체로 이동할 수 있음
- Fine-Grained Features
	- 13x13 feature maps
- Multi-Scale Training

### Faster
- Darknet-19
	- Mostly 3x3 filter
	- Global average pooling 사용
		- 파라미터 획기적으로 줄일 수 있음
- Training for classification
	- ImageNet 100 class
	- 448x448 fine-tuning 
- Training for detection
	- Adding 3x3 conv layer  
	- 5 boxese with 5 coordinates each and 20 classes per box

### Stronger
- YOLO 9000
- Hierarchical classification
	- ImageNEt labels are pulled from WordNet
	- structured as a directed graph, not a tree
	- conditional probabilities를 예측
- Join Classification
	- 9000 classes from the full ImageNet release
	- 앵커박스 3개
	- classification loss만 backpropagation
- Dataset Combination with WordTree
	- COCO : general concepts
	- ImageNet : specific concepts

### 결론
- YOLOv2는 빠르고 정확함
- YOLO9000은 데이터셋을 합치는 것이 의의 있음  	
	
	
---

## PR-024: Pixel Recurrent Neural Network	
- Generative Model의 한 종류
- 이미지 픽셀을 Recurrent Neural network로!

### Generative Model
<img src="https://www.dropbox.com/s/lhwgrs8ql4lfgmq/Screenshot%202018-12-05%2023.33.07.png?raw=1">

- Generative Model도 다양한 계보가 있음!
	- Data distribution을 어떻게 모사할까?가 Pixel RNN쪽

### Intuition
- 픽셀이 많아지면 이들의 관계를 어떻게 모델링할 것인가?
- Sequential Model로 Generate!
	- 픽셀 하나가 주어졌을 때, 다음 픽셀을 예측
	- i에서 i-1 ... 1까지 픽셀을 사용해 원하는 p(x)를 구함 
- Autoregressive Model
	- 자기 자신을 두고 자기 자신을 나타내는 모델
	- 단순하고 직관적, 학습이 잘됨
	- latent value가 없음
	- tractable likelihood
- <img src="https://www.dropbox.com/s/j9owqdf4jd3x2ou/Screenshot%202018-12-05%2023.48.03.png?raw=1">

### Mask
- 미래의 픽셀은 정보를 받지 않기 위해, 계산을 할 때 마스크를 씀
- 미래 픽셀은 0
- 채널이 여러개일 경우
	- R부터 시작
	- Mask A, B

### Receptive Fields
<img src="https://www.dropbox.com/s/0gqwmf5mm3c8za9/Screenshot%202018-12-06%2000.06.34.png?raw=1">

### Architecture
<img src="https://www.dropbox.com/s/yn6xsffacuv152o/Screenshot%202018-12-06%2000.07.45.png?raw=1">

- Residual 붙여서 더 깊게!
- Row LSTM
- Diagonal LSTM 

### PixelCNN
- LSTM이 너무 느리니.. (성능은 좋지만) 개선!
- CNN으로 레이어를 여러번 쌓음
- 256 classes

### KL Divergence
- 데이터를 제대로 이해하고 있으면 compress할 때 완벽하게 알고 있기 때문에 추가적인 정보가 필요가 없음
	- compress할 때 얼마나 데이터가 필요한가로 설명할 수 있음
- Negative Likelihood




---

## PR-025: Learning with side information through modality hallucination
- hallucination : 환각, 복사
- Learning with side information
	- multi modal
	- RGB, Depth 등등 다양한 것을 input
	- Test를 위해서도 똑같이 필요한데 이게 문제..

### Missing Input during test
- Zero padding
	- 잘 안될 수 있음
- Generate해서 RGB를 동시에 넣음
	- Generate가 어려움
- Feature Space를 hallucination


### Hallucination
<img src="https://www.dropbox.com/s/fethr4k8pkcckaf/Screenshot%202018-12-07%2000.07.07.png?raw=1">

- 빨간색을 카피할 수 있는 파란색

### Loss function
<img src="https://www.dropbox.com/s/i78k9okzqvetssl/Screenshot%202018-12-07%2000.26.54.png?raw=1">

### Several issues
- First train the RGB & D-Net, copy the D Nte to H-Net
- Pool5 layer가 hallucinate







