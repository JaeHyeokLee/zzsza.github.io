---
layout: post
title:  "Discrete Optimization 입문"
subtitle: "Discrete Optimization의 입문"
categories: data
tags: optimization
comments: true
---

- Discrete Optimization 입문에 대해 작성한 글입니다


---

### Discrete Optimization
- 냅색 문제
	- NP hard, 다루기 어려운 문제
	- 대신 근사 알고리즘(Approximate)이 존재함
- Optimization
	- 컴퓨터 사이언스에서 어려운 문제중 하나
	- Supply chains, sport scheduling, logistics, eletrical power system, manufacturing 등에서 사용됨
- NP-Complete 문제의 2가지 속성
	- 답을 매우 빠르게 확인할 수 있음
	- NP-Complete 문제 하나를 빠르게 푼다면, 모든 것을 풀 수 있음
- Exponential time이 가장 worst case라고 믿음 
- 항구
	- 항구의 각각 요소가 계속 움직임 => 최적화 문제
	- 일반적으로 매일 해결
- Energy
	- 에너지는 load와 demand가 계속 만남
	- 이걸 잘 매칭시키고 싶어함
- Sport Scheduling
	- 스포츠 스케쥴링
	- 누가 누구랑 대결할 것인가
	- 여러 제약조건
		- 같은 경기장을 사용할 수 없음
		- 베이스볼, 축구 등
		- 시즌도 맞춰야 함
- Exponential Runtime
	- 지수로 상승함
	- 문제 사이즈가 커지면서 점점 풀리지 않음
- Optimization Problems
	- 어디에나 있음
	- 엄청나게 풀기 어려움
	- 그러나 풀어야 함
	- 재미있음
	- 푸는 것이 중요함
- 예시
	- Kidney(신장) Exchanges
		- Basic Facts
			- 우리는 1개의 신장이 필요
			- 80,000명의 환자
			- 미국에서 매년 4,000명이 죽음
			- 호환 이슈가 존재
		- 주는 사람, 받는 사람의 그래프가 생김
			- 사이클이 생김
			- <img src="https://www.dropbox.com/s/u2rrup6rel6tc5o/Screenshot%202019-08-12%2021.26.55.png?raw=1">
		- 이건 매우 작은 문제지만, 가지수가 많아지면 풀기 어려워짐
	- Disaster Management
		- 허리케인의 Damages 금액을 산정
		- 어떤 지역에 문제가 있고, 그 이후 빠르게 복원(예를 들면 전기)
		- 이럴 경우 정전의 크기를 줄이려고 노력하고, 빠르게 복구하고 싶음
		- <img src="https://www.dropbox.com/s/t50pz8w1dta1p76/Screenshot%202019-08-12%2021.30.41.png?raw=1">
		- Routing Aspect 관점(어디부터 복구할 것인가)과 Power Flow Aspect 관점(blackout size를 줄임)
		- <img src="https://www.dropbox.com/s/yxv8j5exdkk3z0x/Screenshot%202019-08-12%2021.31.56.png?raw=1">



	
### Reference
- Coursera [Discrete Optimization](https://www.coursera.org/learn/discrete-optimization)
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	