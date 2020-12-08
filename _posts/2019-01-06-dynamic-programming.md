---
layout: post
title:  "Reinforcement Learning 3강. Planning by Dynamic Programming"
subtitle: "David Silver-Reinforcement Learning 3강"
categories: data
tags: rl
comments: true
---

- David Silver의 Reinforcement Learning 강의를 한국어로 해설해주는 팡요랩 영상을 보고 메모한 자료입니다


---

- Planning
	- MDP에 대한 모든 정보를 알 때(=Environment, State) 더 나은 policy를 찾아가는 과정
- Policy Evaluation
	- policy가 고정되었을 때 value function을 찾는 것

## Dynamic Programming
- 복잡한 문제를 푸는 방법
	- 큰 문제를 작은 문제로 나누고 작은 문제에 대해 솔루션을 찾고 다 모아서 큰 문제를 품 
- 강화 학습
	- Model Free : environment가 어떤 것을 던져줄지 모를 경우(완전한 정보가 없을 경우)
	- Model based : environment에 대한 모델이 있는 경우
		- 이 문제를 해결할 때 Planning, dynamic programming이 쓰임 
- Dynamic Programming의 조건 
	- 1) Optimal substructure 
		- 작은 문제로 나뉠 수 있어야 함 
	- 2) Overlapping subproblems
		- 한 서브 문제를 풀고 나온 솔루션을 저장해(cached) 다시 사용할 수 있음
	- MDP는 이 조건을 만족함
		- Bellman 방정식이 recursive
		- value function이 작은 문제들의 해

### Planning by Dynamic Programming
- DP는 MDP에 대해 모두 알고 있다고 가정함
	- State transaction, reward, ... 
- 2가지 문제가 있음
- 1) For prediction
	- value function을 학습하는 것
	- MDP가 있고 policy가 있을 때 그 policy를 따를 경우의 value function
	- poliy evaluation
- 2) For control	
	- optimal policy를 찾는 것
	- MDP만 있고 optimal policy를 찾음
	- policy iteration, value iteration

## Policy Evaluation
- 이 policy를 따라갔을 때, return을 얼마 받는가?
	- policy를 평가함 즉, value function을 찾는 문제
	- Bellman expectation backup을 사용해서 계속 적용 
	- backup
		- 메모리에 저장
		- synchronous backup 
- 예시
	- <img src="https://www.dropbox.com/s/i3mbzubjmar4np2/Screenshot%202019-01-06%2020.43.19.png?raw=1">
	- MDP가 있고 policy가 있을 떄 value를 찾는 prediction 문제
	- <img src="https://www.dropbox.com/s/lkrwgtfzmtcatip/Screenshot%202019-01-06%2020.50.03.png?raw=1">
	- 주어진 바보같은 policy를 평가만 했을 뿐인데 평가된 value에서 greedy하게 움직이면 optimal policy가 찾을 수 있음
	- 모든 문제에서 이게 됨! 신기
	- <img src="https://www.dropbox.com/s/ibqizz7r1gqyfyu/Screenshot%202019-01-06%2020.50.12.png?raw=1"> 		
	- 무한하게 갈 필요가 없음! 평가하게 greedy하게 움직이는 것을 만들자

## Policy Iteration
- Evaluate the policy(value function을 찾고)
- Improve the policy by acting reddily(value function에 대해 greedy하게 움직이는 새로운 policy를 만들면)
- 이 Evaluate, Improve를 반복하면 converge됨
- <img src="https://www.dropbox.com/s/bmhwna2bolulukt/Screenshot%202019-01-06%2020.59.53.png?raw=1">
- 예제
	- Jack's Car Rental
	- 좋은 문제인진 모름..
	- 최대 20곳의 차가 있을 수 있음
	- A는 포아송 분포로 차가 옴
	- A에서 B로 B에서 A로 자꾸 차를 옮겨야 함
	- 하나 빌릴때 10달러
	- policy를 추측할 수 있음
	- b는 수요가 더 많음
	- a에서 차가 적어도 어쩔 경우엔 b로 옮기는 것이 나을 수 있음
	- <img src="https://www.dropbox.com/s/cjvoof54qgcfzz6/Screenshot%202019-01-06%2021.03.06.png?raw=1">
	- x축 : b 지점에 있는 차의 수, y축 : a 지점에 있는 차의 수 
	- iteration하면 수렴한다! 정도의 감만 잡으면 ok
- 증명
	- 무조건 이전 policy보다 좋은가?
		- <img src="https://www.dropbox.com/s/9p7vbp1beduchf2/Screenshot%202019-01-06%2021.06.54.png?raw=1">
	- 수렴 포인트는 optimal  
		- <img src="https://www.dropbox.com/s/twxdck9vnx9xwvb/Screenshot%202019-01-06%2021.16.09.png?raw=1">
- Modified Policy Iteration
	- 꼭 수렴할 때까지 해야되는가? 일찍 끝내면 안되는가
	- k번만 하고 evaluation, improve해도 되지 않는가?
	- 이렇게 해도 합리적임!

## Value Iteration
- Principle of Optimality
- Deterministic Value Iteration
	- 서브 프러블럼의 솔루션을 알면 Bellman Optimality Equation을 이용해 s에서의 솔루션을 구할 수 있음
	- 바로 이전 점에서 목적지까지 최단 거리를 구함. 그 전에서 구함.. 반복
- value만 가지고 놈(value만 이터레이티브하게 update)
	- policy 없음
- 예시
	- <img src="https://www.dropbox.com/s/3j137c7ircelb0n/Screenshot%202019-01-06%2021.30.38.png?raw=1">
	- 한 스텝들은 bellman optimality equation을 이용해 품
	- 끝이란 것을 알 수 없어서 모두 다 돌아야 함
- <img src="https://www.dropbox.com/s/olfkhs2ru5u9luf/Screenshot%202019-01-06%2021.34.19.png?raw=1">


### 잠시 정리
- <img src="https://www.dropbox.com/s/9mm63y9c5ro56a7/Screenshot%202019-01-06%2021.37.19.png?raw=1">

## Asynchronous Dynamic Programming
- 여태 나온 DP 방법들은 모든 stete들이 parallel되게 하는 synchronous backup을 사용했음
- 어떤 state만 하거나 순서를 다르게 하는 asynchronous한 방법을 이용하면 computation을 줄일 수 있음
- 모든 state가 골고루 뽑히면 수렴	
- 방법론
	- In-place dynamic programming
		- 코딩 테크닉
		- state가 n개 있으면 table이 있어야 함
			- 이전 step의 테이블 정보와 새로운 table
		- inplace는 하나만 가지고 있음!
	- Proritised sweeping
		- 순서를 중요한 친구 먼저!
		- 중요한 정의 : bellman error가 큰 것
	- Real-time dynamic programming  	
		- state space가 넓고 agent가 가는 곳은 한정적일 경우 agent를 움직이게 하고 정해진 곳을 방문하면 update
- Full-width Backups
	- DP는 full-width backup을 사용
	- 큰 문제에선 DP를 사용할 수 없음(차원의 저주..)
- Sample Backups
	- state가 많아도 고정된 cost로 할 수 있음
	- model free에서도 할 수 있음
		- 오늘은 model based를 생각했었음. 
		- model free는 어디로 갈 지 모르는 상황, 액션을 하며 샘플!  	



## Reference
- [팡요랩](https://www.youtube.com/watch?v=wYgyiCEkwC8)
