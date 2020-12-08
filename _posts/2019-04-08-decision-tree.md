---
layout: post
title: "Decision Tree 정리"
subtitle: "Decision Tree 정리"
categories: data
tags: ml
comments: true
---

- Decision Tree에 대해 정리한 글입니다


### Decision Tree
- 데이터에 있는 규칙을 자동으로 찾아내는 트리 기반의 알고리즘
- 분류와 회귀에 모두 사용 가능
- 비모수 모형의 효과적인 방법

### 구조
<img src="https://www.dropbox.com/s/nqrpj60a0jcf8k4/Screenshot%202019-04-08%2020.41.02.png?raw=1">

- Root node
- Decision node : 조건
- Leaf node : 클래스 값

### Tree Split
- 정보 균일도가 높은 데이터세트를 선택할 수 있는 규칙 생성
- 균일도를 측정하는 대표적인 방법 2가지
	- 1) 엔트로피를 이용한 정보 이득, Information Gain
	- 2) 지니 계수

### Information Gain
<img src="https://cdn-images-1.medium.com/max/1600/1*ehBCUTusa2fep2xaklKWXA.png">

- 엔트로피
	- 주어진 데이터 집합의 혼잡도
	- 서로 다른 값이 섞여 있으면 엔트로피가 높고, 같은 값이 석여 있으면 엔트로피가 낮음 	 




## Reference

