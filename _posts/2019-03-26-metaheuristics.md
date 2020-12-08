---
layout: post
title:  "메타휴리스틱 기법과 탐색 방법, Metaheuristics and Search Technique"
subtitle: "메타휴리스틱 기법과 탐색 방법, Metaheuristics and Search Technique"
categories: data
tags: optimization
comments: true
---

- 메타휴리스틱 이론, 메타휴리스틱 기법에 대해 작성한 글입니다

---

## Overview
<img src="https://www.dropbox.com/s/cnwnbmfvz1lbarq/Screenshot%202019-03-26%2017.06.44.png?raw=1">

## Operations Research
- OR
- 수학적 모델링이나 통계 분석, 최적화 기법 등을 이용해 복잡한 **의사결정** 문제에서 최적해 혹은 근사최적해를 찾아내 이익, 성능, 수익 등을 최대화하거나 손실, 위험, 비용 등을 최소화하는 현실적인 문제를 해결할 때 사용
- 2차 세계 대전에서 기원
	- Scheduling
	- Transportation
	- Inventory management
	- Warehousing
	- Facility allocation
	- Energy distribution
- 예제
	- 의자는 15BF, 6시간 노동력이 필요하고 식탁은 24BF, 5시간의 노동력이 필요
	- Maximize profit => z = 12X + 10Y
		- Chair : 12달러, Table : 10달러 
	- Subjected to
		- 15X + 24Y <= 300
		- 6X + 5Y <= 120   

### OR 구성 요소
- 목적 함수(Objective function)
- 결정 변수(Decision variables)
	- 목적 함수식이나 제약조건에서 미지수로 나타나는 변수 
- 제약 조건(Constraints)

### Types of Solutions and Constraints
- Solutions
	- Infeasible : 모든 제약조건에 만족하는 solution이 없는 경우
	- Feasible
	- Optimal
	- Near-Optimal
- Constraints
	- Hard constraints : 필수 제약조건
	- Soft constraints  

## Continuous vs Combinatorial
### Continuous
- 연속적 문제
- 결정 변수가 연속적인 문제


### Combinatorial
- 결정 변수가 이산적인 문제
- 이산적 문제, 조합 최적화 문제
- 정수 계획법(Integer Programming : 결정 변수가 정수인 최적화 문제)이 대표적
- 예시
	- Traveling Salesman Problem(TSP)
	- Vehicle Routing Problem(VRP)
	- Knapsack PRoblem(KP)
	- Quadratic Assignment Problem(QAP)

### P vs NP Problems
- P problem : 짧은 다항식 문제
- NP problem : 짧은 non-deterministic 문제


## 메타휴리스틱
- 휴리스틱
	- 합리적인 계산 비용으로 최적 또는 거의 최적의 솔루션을 찾는 기술 
- 메타휴리스틱
	- 특정 문제에 특화되지 않고 자연에서 영감을 얻은 경험적 방법 
- NP Problem은 문제가 커지면서 점점 더 어려워짐
- 자주 사용되는 메타휴리스틱 방법
	- Genetic Algorithm(GA)
	- Tabu Search(TS)
	- Ant Colony Optimization(ACO)
	- Partical Swarm Optimization(PSO)
	- Simulated Annealing(SA)

	
## Search Techniques
- Local search vs global search
	- local : 이웃에 기반함, 그리드 서치
	- global : search space 
- Deterministic vs stochastic 
	- deterministic : non-random
- [참고 링크](https://www.researchgate.net/post/Difference_between_stochastic_and_deterministic_optimization_model)
- Continuous Problem
	- 특정 인풋으로부터 어떤 output이 나오는 함수
	- Minimization or maximization 문제
	- Himmelblau's function  
- Combination Problem
	- discrete elements의 조합 문제
	- Minimization or maximization 문제
	- TSP 문제
		- 5개의 도시 : A, B, C, D, E
		- minimum route : D-A-E-C-B (최소 거리)
		- 다른 것도 가능 : A-D-C-E-B

### Neighborhood vs Population Search
- Neighborhood search
	- <img src="https://www.dropbox.com/s/pb45r9rcnld5d8c/Screenshot%202019-03-26%2018.16.50.png?raw=1"> 
	- local search
	- 계산적으로 어려운 최적화 문제를 해결하는 휴리스틱 방법론
	- 잠재적 솔루션을 취하고 주변 환경(일종의 이웃)을 체크
	- Simulated Annealing and Tabu Search    
- Population Based Search
	- 각 iteration에서 솔루션의 population를 사용하는 방법
	- 잠재성이 있는 솔루션을 Evaluate
	- 랜덤하게 다른 솔루션을 생산하기 위한 솔루션을 선택
	- Genetic Aglorithm, Evolutionary Strategies, Particle Swarm Optimization 


### 메타휴리스틱은 어떻게 작동하는가?
- Random initial solution
- Neighborhood search
	- 초기 솔루션부터 근처를 돌아다니며 실행
	- 모든 스텝을 evaluate
	- 각 스텝의 목적 값(objective value)를 추적
- Population-Based search
	- 랜덤 초기 솔루션을 여러개 생성
	- 더 "나은" 솔루션을 생성
	- Diverse 



### Reference
- [휴리스틱 이론 - 위키피디아](https://ko.wikipedia.org/wiki/%ED%9C%B4%EB%A6%AC%EC%8A%A4%ED%8B%B1_%EC%9D%B4%EB%A1%A0)
