---
layout: post
title: "CS224W - Machine Learning with Graphs 1강 정리"
subtitle: "CS224W - Machine Learning with Graphs 1강 정리"
categories: data
tags: ml graph
comments: true
---


- Stanford CS224W : Machine Learning with Graphs 1강을 듣고 정리한 글입니다


---


### CS224W : Machine Learning with Graphs
- 예전 강의에선 Analytics, Social Network, Analytics graph에 집중했으나, 최근엔 ML에 집중
- Why Networks?
	- 네트워크는 상호 작용하는 엔티티의 복잡한 시스템을 설명하기 위한 일반적인 언어임
- Two Types of Networks/Graphs
	- Network(Natural Graphs)
		- 사회는 70억개 이상의 개인의 모음
		- 전자 장치를 연결하는 통신 시스템
		- 유전자, 단백질의 상호 작용으로 생명 조절
		- 우리의 생각은 뇌의 수십억 개의 뉴런으로 이루어짐 
	- Information Graphs
		- 정보, 지식이 구성되고 연결됨
		- Scene graphs : 장면의 객체와 관계
		- 유사성 네트워크 : 데이터 수집, 유사한 지점을 연결함 
	- 종종 크게 구별이 가지 않음   
- 많은 데이터가 네트워크임
	- <img src="https://www.dropbox.com/s/c6hlwkws5t04zq3/Screenshot%202019-12-02%2023.19.55.png?raw=1">
	- <img src="https://www.dropbox.com/s/zkerkrhdjqfl10p/Screenshot%202019-12-02%2023.52.08.png?raw=1">
	- 이 시스템은 어떻게 구성되어 있는가?
	- 이것들의 design property는 무엇인가?- 많은 시스템 뒤엔 구성 요소간의 상호 작용을 적용하는 다이어그램, 네트워크가 있음
	- 네트워크를 이해하지 않으면 이런 시스템을 모델링하고 예측할 수 없음 
- 더 나은 예측을 위해 Relational Structure를 어떻게 이용할까?
- Graphs : Machine Learning
	- 복잡한 도메인(텍스트, 이미지 등)은 관계형 그래프로 표현할 수 있는 구조를 가지고 있음
	- 관계를 명시적으로 모델링해 더 나은 성능을 달성함
- 네트워크에 관심가져야 하는 이유
	- 복잡한 데이터를 설명하기 위한 범용 언어
		- 과학, 자연, 기술 네트워크는 예상보다 비슷함
	- 분야간 단어 공유
		- 컴퓨터 과학, 사회 과학, 물리학, 경제학, 통계학, 생물학
	- 데이터 가용성, 계산 이슈
		- 웹/모바일, 바이오, 건강, 의료
	- Impact
		- 소셜 네트워크, 약물 설계, AI 추론 등   


<br />

---

<br />

### Networks and Applications
- Node classification
	- 주어진 노드의 type, color 예측- Link prediction
	- 두 노드가 연결되어 있는지 예측- Community detection
	- 밀도있게 연결된 노드 클러스터 구분- Network similarity
	- 두 노드, 네트워크의 유사도 측정
- 여러 예시
	- <img src="https://www.dropbox.com/s/bz9bgqddzyuw74t/Screenshot%202019-12-03%2000.56.36.png?raw=1">
	- <img src="https://www.dropbox.com/s/h77a5wt3fx9j7pb/Screenshot%202019-12-03%2000.56.52.png?raw=1">
	- <img src="https://www.dropbox.com/s/gxxb0sg1lkflwd3/Screenshot%202019-12-03%2001.01.36.png?raw=1">
		- 네트워크 구조가 시스템의 robustness에 어떤 영향을 미치는지 이해해야 함
		- 네트워크 구조와 네트워크의 동적 프로세스 간의 상호 작용 및 장애에 대한 영향을 평가하기 위한 정량적 도구 개발
		- 현실에서 실패가 재현할 수 있는 법칙을 따르고, 네트워크 도구를 사용해 정량화하고 예측할 수 있음을 배움
	- <img src="https://www.dropbox.com/s/sq55tfzpe0fdjbe/Screenshot%202019-12-03%2001.12.03.png?raw=1">
	- <img src="https://www.dropbox.com/s/ts73wq6oji4zejs/Screenshot%202019-12-03%2001.12.23.png?raw=1">   
	- <img src="https://www.dropbox.com/s/1d6d20v5oc4mtg7/Screenshot%202019-12-03%2001.13.03.png?raw=1">
		- 오 추천에도 이렇게 link prediction 사용할 수 있구나
	- <img src="https://www.dropbox.com/s/h3nmvz4zh9dauzg/Screenshot%202019-12-03%2001.22.40.png?raw=1">
		- 노드를 d차원 임베딩에 매핑해서 유사한 네트워크 환경을 가진 노드가 서로 가까이에 되도록 설정
	- <img src="https://www.dropbox.com/s/samckayvkz0hr3g/Screenshot%202019-12-03%2001.23.40.png?raw=1">
	- <img src="https://www.dropbox.com/s/tj940a4o2hpr01m/Screenshot%202019-12-03%2001.24.07.png?raw=1">
	- <img src="https://www.dropbox.com/s/0du7dyyxxsxta72/Screenshot%202019-12-03%2001.24.26.png?raw=1">
	- <img src="https://www.dropbox.com/s/ahgtgh5418sif93/Screenshot%202019-12-03%2001.24.46.png?raw=1"> 
	- <img src="https://www.dropbox.com/s/25aeswxcgkrjqm5/Screenshot%202019-12-03%2001.25.27.png?raw=1">
	- <img src="https://www.dropbox.com/s/fu69lihvk7t2d4z/Screenshot%202019-12-03%2001.26.06.png?raw=1">
	- <img src="https://www.dropbox.com/s/ejdas5q0qbmvowf/Screenshot%202019-12-03%2001.26.36.png?raw=1">
	- <img src="https://www.dropbox.com/s/yo6r0ppromkdddc/Screenshot%202019-12-03%2001.27.10.png?raw=1">
		- 오 사이드이펙트 예측! 약의 pari가 주어지고 사이드 이펙트 예측하기 신기함
	- <img src="https://www.dropbox.com/s/94jvwp5lg8usmi6/Screenshot%202019-12-03%2001.27.50.png?raw=1">
	- <img src="https://www.dropbox.com/s/7f1kxcrylzyytfk/Screenshot%202019-12-03%2001.28.53.png?raw=1"> 
		- A와 B를 같이 사용할 때 근육을 분해할 가능성은?


- Network Analysis Tools
	- snap.py
	- snap c++
	- NetworkX, graph-tool
	- (facebook의 big-graph도 있음)

	
<br />

---

<br />

### Structure of Graphs
- 네트워크의 구조
	- Network는 object 쌍이 링크로 연결된 object 모음
	- 네트워크 구조는 무엇일까?
- Network의 구성 요소
	- Object : nodes, vertics, entity
		- N  
	- Interactions : links, edges
		- E
	- System : network, graph
		- G(N, E)
- Networks와 Graph의 차이
	- Network는 실제 시스템을 의미함
		- Web, Social network
		- Language : Network, node, link
	- Graph는 network의 수학적 표현
		- Web graph, Social graph
		- Language : Graph, vertex, edge  
	- 필요할 땐 구별하겠지만, 대부분 두 용어를 번갈아 사용할 예정
- <img src="https://www.dropbox.com/s/1wedzrc3cueixpx/Screenshot%202019-12-03%2021.02.49.png?raw=1">
- Choosing Proper Representations
	- 협력하는 개인을 연결하면 professional network
	- sexual 관계를 연결하면 sexual network
	- 인용하는 과학 논문을 연결하면 citation network
- Network를 정의하는 방법
	- Graph 작성
		- Node란 무엇인가?
		- Edge란 무엇인가?
	- 적절한 네트워크 representation 선택
		- 주어진 도메인, 문제에서 네트워크를 성공적으로 사용하기 위해 중요한 결정
	- 어떤 경우엔 독특하고 명확한 표현이 있음
	- 다른 경우엔 표현이 unique하지 않음
	- 링크를 할당하는 방법에 따라 공부할 내용이 다양함

<br />

---

<br />

### Network 표현 방식
- Undirected
	- 방향성이 없는 그래프
	- Links : undirected, Symmetrical, reciprocal 
	- 예시 : Collaborations, 페이스북 친구 관계
- Directed 
	- 방향성이 있는 그래프 
	- Links : directed, arcs
	- 예시 : 트위터의 팔로잉
- 노드의 차수(Node Degrees)
	- Undirected
		- Node degree, $$k_{i}$$ : 노드 i에 인접한 edge의 수 
		- Average degree => 어디에 쓰지?
	- Directed
		- in-degree : 해당 노드로 향하는, out-degree : 해당 노드가 향하는 
		- source : $$k^{in}=0$$
		- sink : $$k^{out}=0$$
	- <img src="https://www.dropbox.com/s/u7n620pjrnodp6g/Screenshot%202019-12-03%2022.41.10.png?raw=1">
- 완전 그래프(Complete Graph)
	- 서로 다른 두 개의 꼭짓점이 반드시 하나의 변으로 연결된 그래프
	- 서로 다른 두 개의 vertex가 반드시 하나의 edge로 연결
	- $$E = E_{max}$$일 경우 complete graph
	- average degree = N-1
	- <img src="https://www.dropbox.com/s/dkqmghcgd44pisi/Screenshot%202019-12-03%2022.44.42.png?raw=1">
- 이분 그래프(Bipartite Graph)
	- 핀터레스트에서 사용
	- 이분 그래프는 모든 링크가 U의 노드를 V의 노드에 연결하도록 노드를 U,V로 나눌 수 있는 그래프, U와 V는 독립
	- U끼리는 연결되지 않고, V끼리도 연결되지 않음
	- 인접한 정점끼리 다른 색으로 칠하고 모든 정점을 두 가지 색으로만 칠할 수 있는 그래프
	- 예시
		- Authors to Papers
		- Actors to Movies
		- Users to Movies
		- Recipes to Ingredients
	- "Folded" networks
		- Author collaborations networks
		- Movie co-rating networks 
	- BFS, DFS 탐색 이용하면 확인할 수 있음
	- [이분 그래프(Bipartite Graph)란](https://gmlwjd9405.github.io/2018/08/23/algorithm-bipartite-graph.html) 참고
	- <img src="https://www.dropbox.com/s/mqxudp9rre5qgb6/Screenshot%202019-12-03%2022.52.43.png?raw=1">
		- B와 C는 5와 공통적으로 연결됨  
- Representing Graphs : 인접 행렬(Adjacency matrix)
	- node i와 j로 가는 링크가 있다면 1, 아니면 0 
	- 인접행렬 : 그래프에서 어느 꼭짓점들이 변으로 연결되었는지 나타내는 행렬
	- undirected graph는 대칭 행렬이지만, directed graph(오른쪽)의 경우 대칭 행렬이 아님
	- <img src="https://www.dropbox.com/s/des3cl902gow4zs/Screenshot%202019-12-03%2022.57.06.png?raw=1">
	- <img src="https://www.dropbox.com/s/9yybz1vrgrdxjow/Screenshot%202019-12-03%2022.58.28.png?raw=1">
- Representing Graphs : Edge list
	- 엣지 리스트 : 연결된 엣지들의 집합
	- <img src="https://www.dropbox.com/s/g944aslxyyghlm5/Screenshot%202019-12-03%2023.06.43.png?raw=1">
- Representing Graphs : 인접 리스트(Adjancency list)
	- 그래프의 한 꼭짓점에서 연결되어 있는 꼭짓점들을 하나의 연결 리스트로 표현
	- 인접 행렬에 비해 edge가 희소한 그래프에서 효과적임
	- <img src="https://www.dropbox.com/s/fxcilsiz4i3rgan/Screenshot%202019-12-03%2023.08.58.png?raw=1">
- Networks are Sparse Graphs
	- 현실 네트워크는 Sparse
	- 따라서 인접 행렬은 0으로 채워짐!
- Edge Attribute
	- 가능한 옵션
		- Weight (예 : 커뮤니케이션의 빈도)
		- Ranking (예 : Best friend, Second best freind)
		- Type (Frient, relactive, co-worker)
		- Sign : Friend vs Foe, Trust vs Distrust
		- Propeties depending on the structure of the reset of the graph : 공통 친구 수 
- More Types of Graphs
	- Unweighted, Weighted(undirected)
		- <img src="https://www.dropbox.com/s/ltsz03qx42a5zgb/Screenshot%202019-12-03%2023.14.26.png?raw=1">
	- Self-edges(self-loops), Multigraph
		- <img src="https://www.dropbox.com/s/ittwwh229ulxqj8/Screenshot%202019-12-03%2023.14.45.png?raw=1">
- Connectivity of Undirected Graphs
	- 두 정점은 경로로 연결될 수 있음
	- 연결이 끊어진 그래프는 둘 이상의 연결된 구성 요소로 구성됨
	- <img src="https://www.dropbox.com/s/qr61lm4s95oyisd/Screenshot%202019-12-03%2023.16.25.png?raw=1">
	- <img src="https://www.dropbox.com/s/2b4nt1m9sykqdmk/Screenshot%202019-12-03%2023.16.43.png?raw=1">
	- 여러 컴포너튼가 있는 네트워크의 인접 행렬은 block-diagonal 형태로 작성할 수 있어서 0이 아닌 요소는 사각형으로 제한되고 아니면 모두 0임
- Connectivity of directed Graphs
	- strongly connected(강하게 연결된) directed graph
		- 각 노드에서 다른 모든 노드로 경로가 있고, 반대도 마찬가지 
		- SCC를 식별할 수 있지만 모든 노드가 SCC는 아님
		- <img src="https://www.dropbox.com/s/uwfflcz6datfnrl/Screenshot%202019-12-03%2023.25.27.png?raw=1">
	- weakly connected(약하게 연결된) directed graph 
		- 가장 자리 방향을 무시하면 연결됨
	- <img src="https://www.dropbox.com/s/1rq9en9ix083eyg/Screenshot%202019-12-03%2023.19.37.png?raw=1"> 
- Network Representations
	- Email network => directed multigraph with self-edges
	- Facebook friendship => undirected, unweighted
	- Citation networks => unweighted, directed, acyclic
	- Collaboration networks => undirected multigraph of weighted graph
	- Mobile phone calss => directed, (weighted?) multigraph 
	- Protein Interactions => undirected, unweighted with self interactions

<br />

---

<br />


### Readings
- P. Erdos, A. Renyi. [On Random Graphs](http://snap.stanford.edu/class/cs224w-readings/erdos59random.pdf) I. Publ. Math. Debrecen,1959.- P. Erdos, A. Renyi. [On the evolution of random graphs](http://snap.stanford.edu/class/cs224w-readings/erdos60random.pdf). Magyar Tud.Akad. Mat. Kutato Int. Koezl., 1960.- B. Bollobas. [Random Graphs](http://www2.math.uu.se/~svante/talks/2014stockholm.pdf). Cambridge University Press.- M.E.J. Newman, S. H. Strogatz and D.J. Watts. [Random graphs with arbitrary degree distributions and their applications](http://math.uchicago.edu/~shmuel/Network-course-readings/Random-Graphs-with-Arbitrary-Degree-Distributions-and-Their-Applications.pdf). Phys. Rev. E64, 026118, 2001.- R. Milo, N. Kashtan, S. Itzkovitz, M.E.J. Newman, U. Alon. [On the uniform generation of random graphs with prescribed degree](https://arxiv.org/abs/cond-mat/0312028)sequences. Arxiv, 2004.- D. Ellis. [The expansion of random regular graphs. Lecture notes](http://snap.stanford.edu/class/cs224w-readings/ellis11expansion.pdf) from Algebraic methods in combinatorics, Cambridge University, 2011.- S. Arora, S. Rao and U. Vazirani. [Expander Flows, Geometric Embeddings and Graph Partitioning](http://snap.stanford.edu/class/cs224w-readings/arora04expansion.pdf). In proc. STOC '04, 2004.





 