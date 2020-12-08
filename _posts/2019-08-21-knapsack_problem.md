---
layout: post
title:  "최적화로 바라본 Knapsack Problem"
subtitle: "최적화로 바라본 Knapsack Problem"
categories: data
tags: optimization
comments: true
---

- 최적화(Optimization) 관점에서 본 Knapsack 문제를 정리한 글입니다
	- Cousera Discrete Optimization 2주차 강의 내용입니다 


---


### Greedy Algorithms
- 가장 귀중한 아이템을 먼저 가져가고 다음 배낭에 들어갈 수 있는 다음 것을 가져가는 방식
- Idea : Take the most valuable items first
- 다른 Greedy 알고리즘과 비교
	- 디자인하는 방식에 따라 매우 복잡할 수 있고, 품질이 달라질 수 있음
- 실현 가능한(feasible) 솔루션을 쉽게 구축할 수 있다고 가정 
- 한번에 하나의 항목을 선택
- 여기서 Greedy의 의미를 다르게 할 수 있음
	- 1) 무게에 기반해, 작은 물건부터 가져감
	- 2) 가치가 있는 것부터 가져감 
	- 3) 가치의 밀도를 측정. dollars per kilogram
- 하나의 문제에 다양한 greedy 알고리즘이 존재할 수 있음
	- 어떤 것이 다른 것보다 나을 수 있음
	- input에 의존적
- 장점
	- 빠르게 구현 가능
	- 매우 빠를 수 있음
- 단점
	- 보통 퀄리티를 보장할 수 없음
	- 퀄리티가 input에 따라 크게 다를 수 있음
	- 문제의 feasibility는 쉬워야 함
- 항상 greedy로 시작할 수 있음
- 그 후
	- Constraint Programming
	- Local Search
	- mixed Integer Programming
- 방법
	- feasible solution을 확실하게 찾기
	- high quality 솔루션을 확실하게 만들기
		- 다른 input에 로버스트한(이상치에 영향을 덜 받는) 솔루션
	- 이상적으로 해당 솔루션이 최고임을 입증  

---

### Knapsack Modeling
- Knapsack 문제를 수학적 모델로 공식화하는 방법
- item 조합 I이 있을 때
	- weight $$w_{i}$$
	- value $$v_{i}$$
	- capacity K for a Knapsack 
- 다음 조건을 만족하는 부분 집합 찾기
	- maximum value를 갖는
	- 그러나 Knapsack의 capacity를 초과하지 않아야 함
- 모델링하는 방법
	- Decision Variable(결정 변수) 선택
		- 의사 결정의 대상
		- 목적함수 식이나 제약조건 식에서 미지수로 나타나는 변수
	- 이 변수들의 constraint(제약 조건)을 표현
		- 문제에 구체적으로 정의되어 있음
	- Objective function(목적 함수)을 표현   
		- 각 해답의 퀄리티가 결정됨
- Optimization model의 결과
	- declarative formulation
		- how가 아니라 what을 명시
	- 최적화엔 많은 모델링 방법이 가능함(오픈 마인드!)
- Decision Variables
	- $$x_{i}$$
		- item i가 선택되었는지 아닌지(1이 선택, 0은 미선택)
- Problem constraint
	- 선택된 item은 knapsack의 capacity를 초과할 수 없음
	- $$\sum_{i\in I}w_{i}x_{i} \le K$$   
- Objective function
	- 선택된 것들의 total value 추적
	- $$\sum_{i\in I}v_{i}x_{i}$$ 
- <img src="https://www.dropbox.com/s/qosgz63v0lxceh4/Screenshot%202019-08-19%2001.21.41.png?raw=1">
- Exponential Growth
	- 가능한 솔루션이 얼마나 있는지?
	- Search space(Solution space)
	- 모든 것이 feasible하진 않음(실현가능하진 않음)
		- Knapsack의 capacity를 초과할 수 없음
	- How many are they? 2^∣I∣
	- 탐색하는데 드는 시간은?
		- ∣I∣=50일 때 1,285,273,866 centuries  
		- 너무 오래걸림

### Dynamic Programming
- 어떻게 최고의 Knapsack 솔루션을 찾을 수 있을까
	- 다이나믹 프로그래밍을 이용해서!
- 최적화 테크닉으로 많이 사용됨
	- 다양한 종류의 문제에서 잘 작동하며 특히 생물학에 계산에서 잘 작동
- 기본 원칙
	- 분할하고 정복한다
	- bottom부터 계산한다
- Convetions and notations
	- I = {1,2,...n}
	- O(k,j)는 capacity k and items [1..j]의 optimal solution을 뜻함
	- <img src="https://www.dropbox.com/s/tquerxj2ej277kf/Screenshot%202019-08-19%2013.07.24.png?raw=1">
- Recurrence Relations (Bellman 방정식)
	- 우리가 해결하는 방법을 안다고 가정
	- O(k,j)를 풀고 싶음
	- 만약 $$w_{j} \le k$$일 때, 2가지 케이스가 있음
		- j를 선택하지 않으면 O(k, j-1)가 best solution
		- j를 선택하면 best solution은 v\_{j} + O(k-w\_{j}, j-1) 
	- summary
		- O(k,j) = max(O(k, j-1), v\_{j} + O(k-w\_{j}, j-1)) if w\_{j} <= k 
		- O(k,j) = O(k,j-1) otherwise
	- 당연히 O(k,0) = O for all k
- Simple program
	- <img src="https://www.dropbox.com/s/is0cebi2s3m17w1/Screenshot%202019-08-19%2022.58.26.png?raw=1">
	- recursive
- 피보나치 수를 찾기 위해 간단한 프로그램을 짤 수 있음
	- fib(n-1)은 fib(n-2)가 이미 풀려있어야 함
- Dynamic Programming
	- recursive 식으로 bottom up으로 연산함
		- start with zero items
		- continue with one item
		- then two tiems
		- then all items
	- <img src="https://www.dropbox.com/s/dba17ggqbq4orzn/Screenshot%202019-08-19%2023.13.56.png?raw=1">
- 이 알고리즈의 복잡도
	- 테이블을 모두 채워야 함 O(K n)
- polynomial?
	- how many bits does K need to be represented on a computer?


---
 
### Relaxation, branch and bound
- Introduce branch and bound
- The value of relaxation
- Exhausitive Search(완전 탐색)
	- 가능한 방법을 전부 만들어보는 알고리즘
	- Brute Force Search라고도 함
	- 총 몇가지 나오는지 파악 => 너무 많으면 탐색 시간이 부족
	- 경우의 세를 세어보고 완전 탐색해야 함
- Branch and Bound(분기 한정법)
	- [참고 자료](http://www.aistudy.com/heuristic/branch_and_bound.htm)
	- Iterative two steps
		- branching
			- 문제를 여러 하위 문제로 쪼갬 
			- 여러개의 작은 feasible subregion로 구성하고 그 과정이 subregion 각각에 대해 재귀 반복을 거쳐 tree 구조를 형성
		- bounding 
			- 하위 문제의 best solution을 찾고 optimistic estimate함
			- feasible subregion 내에서 최적해를 찾기 위해 upper and low bound를 빠르게 찾는 방법
			- maximization : upper bound
			- minimization : lower bound
	- Relaxation 
		- optimistic estimate를 찾는 방법
		- Optimization is the art of relaxation
		- capacity constraint를 relax할 수 있음
		- 어려운 문제를 해결하기 쉬운 문제로 근사한 것
		- [Wikipedia : Linear programming relaxation](https://en.wikipedia.org/wiki/Linear_programming_relaxation)
- Depth-First branch and Bound
	- <img src="https://www.dropbox.com/s/gcb3dotbndzuudq/Screenshot%202019-08-20%2023.18.58.png?raw=1">
	- 왼쪽 테이블에서 하나씩 선택하고 안하고를 분기해서 답을 찾아감 
		
- A Knapsack Model
	- 초콜렛을 조각낼 수 있음
	- 특정 결정 변수의 fractional value를 취할 수 있음
	- Linear relaxation
		- 나중 수업에서 배울 예정
		- Integrality 요구 조건을 완화함 
		- 모든 value를 정수(integer number)로 바꾸고 그것들의 분수로 이완
	- 푸는 방법
		- 용량이 소진되지 않은 상태에서 아이템 선택
		- 마지막 아이템의 분수 선택 
		- <img src="https://www.dropbox.com/s/0vburwxjrpggbvi/Screenshot%202019-08-20%2023.44.21.png?raw=1">
		- <img src="https://www.dropbox.com/s/2zrwvwf79371auw/Screenshot%202019-08-20%2023.46.20.png?raw=1">
		- linear relaxation이 문제의 해답은 아님
- <img src="https://www.dropbox.com/s/k6avcle36xfqc4y/Screenshot%202019-08-21%2000.07.25.png?raw=1">
	- relaxation으로 9 / 6 / 11.7 

---

### Search Strategies
- Branch and bound를 위한 탐색 전략 
- Search Strategies
	- 트리를 탐색하기 위한 다양한 전략
	- Depth-first, best-first, least-discrepancy
	- many others 
- Depth-first
	- 노드 추정이 best found solution보다 나쁠때 처리
	- Go deep
	- when does is prune?
		- 여태 찾은 best solution보다 나쁠 경우
	- is it memory efficient?
		- exaggerate(과장됨) 
- Best-First
	- best 추정일 때 node를 선택
	- <img src="https://www.dropbox.com/s/zbqusjvq1exkl29/Screenshot%202019-08-21%2013.27.43.png?raw=1">
	- go for the best
	- when does is prune?
		- 여태 찾은 solution보다 모든 노드들이 나쁠 경우 
	- is it memory efficient?
		- exaggerate(과장됨) 
		- 모든 Tree를 탐색 => eponential time, space
		- worst 사례
- Least-Discrepancy
	- Greedy heuristic을 신뢰
	- Good heuristic을 추정하면
		- 실수가 적은
		- search tree가 binary
		- 휴리스틱할 경우 branching이 왼쪽으로 분기
		- 휴리스틱이 잘못되면 branching이 오른쪽으로 분기 
	- Limited Discrepancy Search(LDS)
		- 실수를 줄인다
		- 실수로 증가하는 search space 탐색
		- 휴리스틱을 더 신뢰
	- Explores the search space in waves
		- no mistake
		- one mistake
		- two mistakes..
		- 이럴 때마다 search space가 줄어듬
	- <img src="https://www.dropbox.com/s/5sf95hximtarho9/Screenshot%202019-08-21%2013.39.01.png?raw=1">  
	- <img src="https://www.dropbox.com/s/hnmnus3pjra3rgg/Screenshot%202019-08-21%2013.40.57.png?raw=1">
	- trust the greedy heuristics
	- when does it prune?
		- best-first와 동일
	- is it memory efficient?
		- depth-first와 best-first 대비 
- Relaxation and Search
	- Discrete optimization heaven이 만든 match
	- relaxation을 어떻게 하냐에 따라 속도가 개선될지 결정

### Assignments Getting started
<img src="https://www.dropbox.com/s/hy4mc5mv537ev0x/Screenshot%202019-08-21%2019.43.18.png?raw=1">

- 최적화에 silver bullet은 없음
	- 다양하게 접근 
	- High quality : 10\*4+3\*2=46
	- Scalable, lower quality solution 7\*6=42

---

### Reference
- Coursera [Discrete Optimization](https://www.coursera.org/learn/discrete-optimization)
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	