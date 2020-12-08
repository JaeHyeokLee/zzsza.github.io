---
layout: post
title:  "Constraint Programming의 이해"
subtitle: "Constraint Programming의 이해"
categories: data
tags: optimization
comments: true
---

- Constraint Programming에 대해 작성한 글입니다


---

### Constraint Programming
- 2가지 흥미로운 포인트
	- Computational paradigm
		- 제약 조건을 사용해 각각의 변수가 취할 수 있는 값들의 집합을 줄임(=Search Space를 줄임)
		- 어떤 솔루션에도 나타나지 않을 값을 제거  
	- Modeling methodology
		- 문제의 구조를 가능한한 명확하게 전달
		- 문제의 세부구조를 표현
		- 가능하면 solver에게 많은 정보를 제공 
- 구체적 예시
	- 체스판에서 8개의 퀸이 있고 서로 공격함
	- 같은 column, row, diagonal에 있으면 서로 공격
	- upper and lowd diaagonal로 이동 가능
	- 여왕이 점점 배치되며 feasible solution이 없는 것을 볼 수 있음, 다른 곳에 두었어야 함
- Choice란?
	- 엄청 많은 선택이 있음
	- 일단 처음에 우리가 다루는 선택은 특정 값을 할당하는 것
	- 선택이 잘못될 수 있음
		- 최적화에서 자주 잘못되곤 함
		- solver가 역추적해서 다른 값을 선택해야 함
- Coloring a Map
	- 인접한 두 지역에 같은 색을 받지 않도록 지도에 색을 칠하기 
	- 4개의 Color
		- 모든 map은 4가지 색으로 칠해져야 함
		- 컴퓨터로 입증된 최초의 정리
	- constraint programming을 어떻게 적용할까?
		- Decision variable를 선택
		- Decision variable의 constraints를 표현
	- Decision variables : 각각의 나라에 색칠된 색
	- Decision variables의 도메인
		- 4 color
	- Constraint
		- 인접한 나라에 같은 색을 사용할 수 없음
- Branch and prune
	- pruning
		- 가능한만큼 search space를 줄임       
	- branching
		- 문제를 하위 문제로 분해하고 하위 문제를 탐색 
- Constraint의 역할
	- feasibility checking
	- pruning
- The propagation engine
	- constraint programming의 core
	- 간단한 알고리즘
	- 너 feasible이니? => T/F
	- 서치 스페이스 줄임  
- Feasibility checking
	- 변수의 값이 주어지면 제약 조건을 충족시킬 수 있는지 확인   
- Pruning
	- 만족할 경우 도메인의 값을 결정
- 알고리즘은 각 제약조건마다 전용 알고리즘이 존재
- Send More Money Problem
	- 복면산은 문자나 그림으로 표현된 숫자를 맞히는 것
	- 숫자는 겹치지 않고, 맨 앞자리 숫자는 0이 아님
	- Decision Variables
		- 글자 값을 나타내는 각 글자들을 변수로 지정
		- carry마다 변수 존재
	- <img src="https://www.dropbox.com/s/bwvexeo55ql3xai/Screenshot%202019-08-27%2022.17.45.png?raw=1">
	- <img src="https://www.dropbox.com/s/o0acbs6bzgp6zkd/Screenshot%202019-08-27%2022.40.36.png?raw=1">
	- Propagation engine
		- constraint programming solver의 핵심
		- 간단한 고정된 알고리즘
		- <img src="https://www.dropbox.com/s/30spxepayq0ph6e/Screenshot%202019-08-27%2022.42.35.png?raw=1">
	- <img src="https://www.dropbox.com/s/vtu1spqcyaox6ds/Screenshot%202019-08-27%2022.44.27.png?raw=1">
	- <img src="https://www.dropbox.com/s/4onlqx3tw1uk1lz/Screenshot%202019-08-27%2022.44.48.png?raw=1"> 

### Global Constraint
- Constraint programming의 중요한 특징
	- 다양한 어플리케이션에서 발생하는 하위 조합을 표현
- Global constraint도 마찬가지로 하위 조합 표현
- Global Constraint가 존재하는 이유
	- constraint c(x1, ...xn)
	- x1 in D1, ... xn in Dn
	- 1) Feasibility testing
		- 제약 조건이 유지되도록 변수 도메인에서 값을 찾을 수 있는지 테스트
	- 2) Pruning
		- D\_{i}의 v\_{i}가 주어졌을 때, x\_{i}=v\_{i}를 만족하는 솔루션이 있는가?
		- constraint를 만족하는 variable domain을 찾을 수 있는가? 이런 질문과 함께 search space 정리함  
- Global Constraint의 종류
	- 1) AllDifferent
		- 모든 값이 다른 제약 조건 
		- <img src="https://www.dropbox.com/s/bpj7wz88d5zcxw8/Screenshot%202019-08-31%2013.47.37.png?raw=1">
		- <img src="https://www.dropbox.com/s/abb5oh69gumslg6/Screenshot%202019-08-31%2013.54.13.png?raw=1">
	- 2) Table Constraints
		- 가능한 집합을 Table로 만들고, 특정 조건을 만족하는 경우를 찾음
		- 모든 변수에 대한 카테시안 곱의 하위 집합을 명시
		- <img src="https://www.dropbox.com/s/5balccombtfpwii/Screenshot%202019-09-01%2000.14.03.png?raw=1">
	- 패러다임 도식화
		- 여러 Constraint
			- <img src="https://www.dropbox.com/s/dh5vtkuu1i8vw0f/Screenshot%202019-08-31%2023.24.10.png?raw=1">
		- Global Constraint
			- <img src="https://www.dropbox.com/s/rvdrx6qd01kpjmn/Screenshot%202019-08-31%2023.33.01.png?raw=1">
- 스도쿠
	- Constraint Programming을 활용해 스도쿠를 풀 수 있음
	- <img src="https://www.dropbox.com/s/tkh5ckzcenvv5fc/Screenshot%202019-08-31%2023.46.20.png?raw=1">  
- Graph Coloring
	- <img src="https://www.dropbox.com/s/8end8gdp7a7zmey/Screenshot%202019-09-01%2000.16.13.png?raw=1">
- Constraint Programming에서 집중하는 것
	- Feasibility
	- Optimize 하는 방법
		- Solve a sequence of satisfaction problems
		- Find a solution
		- impose a constraint that the next solution must be better
	- 최적의 솔루션을 찾도록 보장하는 경우
		- 이론적으론 알지만 실제로 찾는 시간이 너무 오래 걸림
		- 새로운 제약 조건이 search space를 감소할 때 효과적(스케쥴링 문제가 그 예시) 

### Constraint Programming modeling techniques
- Symmetry breaking
	- 많은 문제들이 대칭적임
	- 검색 공간의 대칭 부분을 탐색하는 것은 쓸모없음
	- symmetries의 종류
		- variable symmetries
		- value symmetries
	- Balanced Incomplete Block Designs(BIBDs)
		- Input : (v,b,r,k,l)
		- Output : 0/1로 구성된 행렬
		- Constraint : row의 수는 r, 컬럼의 수는 k, 스칼라곱은 I
		- Why BIBDs?
			- combinatorial design 예제
			- variable symmetries 예제
		- <img src="https://www.dropbox.com/s/vda51rltrme68du/Screenshot%202019-09-01%2009.51.31.png?raw=1">
		- 행과 열을 swap할 수 있음 => 대칭
			- <img src="https://www.dropbox.com/s/2jjcwwprhj3sz3s/Screenshot%202019-09-01%2009.58.52.png?raw=1">  
		- variable symmetries를 해결하는 방법
			- 변수에 순서를 정함
		- row symmetries를 고려
			- 사전(lexicographic) 제약을 추가함  
		- lexicographic ordering
			- 첫 값을 비교해서 어떤 것이 큰지? 같으면 그 다음값 
			- a: 0 1 1 0 0 1 0 / b: 1 0 1 0 1 0 0 => a<=b
			- a: 1 1 1 0 0 1 0 / b: 1 0 1 0 1 0 0 => a>=b
		- <img src="https://www.dropbox.com/s/ehmovqtl50csqkm/Screenshot%202019-09-01%2010.07.27.png?raw=1">
		- <img src="https://www.dropbox.com/s/wkp5ql9k3604pvz/Screenshot%202019-09-01%2010.09.46.png?raw=1">
	- Scene Allocation
		- 영화의 scene을 찍음
			- 배우들은 scene에서 연기하며, 하루에 k개의 scnes을 찍을 수 있음
			- 배우들 고용시 하루마다 비용이 나감
		- Objective
			- minimize the total cost
		- <img src="https://www.dropbox.com/s/evhyfoojstf7sle/Screenshot%202019-09-01%2010.19.10.png?raw=1">
		- Value symmetries
			- day는 교환 가능
			- 1일차랑 2일차 장면을 모두 바꿔도 됨
			- s가 solution일 때, p(s)는 s의 날짜가 순열 p에 의해 순서가 배열된 솔루션임
		- 이 symmetries를 없애려면?
			- 하루에 1개식만 생각
			- 월요일 화요일 등 모두 같음(단, 급여가 달라지면 다른 방법..)
			- <img src="https://www.dropbox.com/s/rth57wdqpgwtofe/Screenshot%202019-09-01%2010.44.42.png?raw=1">
		- <img src="https://www.dropbox.com/s/cqe6f8pnyko3gz7/Screenshot%202019-09-01%2010.44.53.png?raw=1">
- Redundant constraints(중복 제약)
	- Motivation
		- Semantically redundant
			- do not exclude any solution
		- computationally significant
			- reduce the search space
	- redundant constraint를 어떻게 찾을까?
		- 모델에 포착되지 않는 솔루션의 속성을 나타냄     
	- 모델에 constraint을 더 많이 넣을수록 컴파일 속도가 더 빨라짐
	- Constraint Programming의 중요한 측면
	- Magic Series
		- <img src="https://www.dropbox.com/s/9vy4gutbdyilgbo/Screenshot%202019-09-01%2011.11.02.png?raw=1">
		- Decision variables는 발생 횟수를 나타내고 발생 횟수가 제한됨
		- 중간 값이 얼마인지 알면 더 강한 제약조건을 나타낼 수 있음
			- <img src="https://www.dropbox.com/s/xd2854qqm8tonuh/Screenshot%202019-09-01%2011.12.34.png?raw=1"> 
			- <img src="https://www.dropbox.com/s/9gfvaq207u2tlwk/Screenshot%202019-09-01%2011.15.58.png?raw=1">
	- Redundant constraints의 역할
		- 1) First
			- solution의 property 표현
			- 다른 제약조건의 전파를 강화 
		- 2) Second
			- gloval view 제공
			- 존재하는 제약조건 결합
			- 커뮤니케이션 증가	
	- Market Split Problems
		- Surrogate constraint
			- 존재하는 제약조건 결합
			- <img src="https://www.dropbox.com/s/gxxzn407wljn8o3/Screenshot%202019-09-01%2011.25.00.png?raw=1">
		- <img src="https://www.dropbox.com/s/8zvu779biphpp4s/Screenshot%202019-09-01%2011.24.46.png?raw=1">
- Car Sequencing
	- redundant constraint이 존재
	- 조립 공정에서 일정 순서가 있고, 특정 옵션이 있음
	- 생산 유닛당 capacity constraint가 존재 : 5대 중 최대 2대는 moonroof가 필요할 수 있음
	- capacity constraint이 충족되도록 자동차 생산
	- <img src="https://www.dropbox.com/s/e1fu15j8b66szex/Screenshot%202019-09-01%2011.30.55.png?raw=1">
	- <img src="https://www.dropbox.com/s/1ag346w8xbopn86/Screenshot%202019-09-01%2011.31.15.png?raw=1">
	- <img src="https://www.dropbox.com/s/04n2zrin222qyyk/Screenshot%202019-09-01%2011.39.08.png?raw=1">
	- <img src="https://www.dropbox.com/s/4jegf069satu27h/Screenshot%202019-09-01%2011.39.31.png?raw=1">
	- 차를 몇대 생산할 수 있는지 추론가능하고, 10대에서 2대 생산 => 이제 8대 => 이렇게 계속 실제 자동차수를 제한할 수 있음. 본질적으로 알고있는 제약임
		- <img src="https://www.dropbox.com/s/dfmvu08dyea09f9/Screenshot%202019-09-01%2012.19.53.png?raw=1">
- Dual Modeling
	- 문제를 모델링할 때 다양한 방법이 존재할 수 있음
		- 같은 decision variable을 사용하지 않아도 됨
	- 두 모델은 서로 보완하는 강점을 가질 수 있음
		- 그들 사이에 선택하는게 어려움  
		- 어떤 모델은 특정 제약조건을 잘 표현하고, 다른 모델은 다른 제약조건을 잘 표현
	- 따라서 dual modeling은 문제의 여러 모델을 설명하고 제약 조건과 연결하는 아이디어에서 시작
	- <img src="https://www.dropbox.com/s/bon3miuuwe8nuhe/Screenshot%202019-09-01%2012.26.49.png?raw=1">
	- <img src="https://www.dropbox.com/s/vjc4ph5zhlojwbi/Screenshot%202019-09-01%2012.28.22.png?raw=1">

	
CP 8	
	
---

### Reference
- Coursera [Discrete Optimization](https://www.coursera.org/learn/discrete-optimization)
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	