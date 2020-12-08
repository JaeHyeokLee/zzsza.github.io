---
layout: post
title: "Contextual Multi-Armed Bandits 정리"
subtitle: "Contextual Multi-Armed Bandits 정리"
categories: data
tags: mab
comments: true
---

- Multi Armed Bandits(멀티 암드 밴딧) 방법 중 하나인 Contextual Multi-Armed Bandits에 대해 정리한 글입니다


## Multi Armed Bandit
- 멀티 암드 밴딧은 비즈니스에서 엄청 좋은 효과를 거두는 방법으로 강화학습 책에서도 등장하는 내용입니다
- MAB 관련 내용은 아래 책과 두 글을 추천합니다
	- [Bandit Algorithms for Website Optimization](http://shop.oreilly.com/product/0636920027393.do) 원서 
	- 송호연님의 [브런치](https://brunch.co.kr/@chris-song/62)
	- 김우승님의 [A/B테스트를 보완하는 MAB 알고리즘](http://hub.zum.com/kimws/2586) 
- 구현 코드
	- [bandits](https://github.com/bgalbraith/bandits/blob/master/bandits/bandit.py)
	- Bandit Algorithms for Webstie Optimization의 [Github](https://github.com/johnmyleswhite/BanditsBook)
	- [BanditLib](https://github.com/huazhengwang/BanditLib)
	- [slots](https://github.com/roycoding/slots) 

---

## Contextual Multi-Armed Bandits
- Contextual MAB 방법은 단어 그대로, **맥락을 고려하는 MAB**라고 보면 됩니다
- [관련 논문](https://pdfs.semanticscholar.org/b38f/ef3fc81a3fddac14822adf7f6796f35419bd.pdf)

### Abstract
- 순서가 있는 독립적인 시도에서 online 알고리즘은 주어진 상황(부가적인 정보)에 기반해 가능한 행동 중 하나의 action을 선택해 선택된 행동의 총 지불을 극대화
	- 이전의 연구들(context-free MAB)은 부가적 정보 없이 선택된 행동에만 의존하는 모델
- 목적
	- 광고의 클릭률(CTR)을 극대화

### Introduction
- 검색 엔진들의 목표
	- 클릭 가능성을 높이고 예상 수익을 높이기 위해 사용자에게 가장 관련있는 광고를 표시
- 이 목표를 달성하기 위해 검색 엔진은 시간의 흐름에 따라 다른 검색어에 대해 가장 관련성이 높은 광고에 대해 학습해야 함
	- 현재 관련있는 광고를 expolit하고 잠재적으로 관련성이 높은 광고를 explore
	- context : 사용자 쿼리 (검색어)
	- $$\mu(x, y)$$ : 쿼리 x에서 광고 y가 클릭될 확률, CTR
- 가능한 알고리즘 : Bayes optimal
	- 주어진 쿼리에 대해 가장 높은 CTR을 가진 광고를 표시
	- 이를 위해 CTR을 알아야 하지만 CTR을 몰라도 베이즈 최적를 사용하면 점근적으로 동일
	- 모든 알고리즘 A에 대해 베이즈 최적의 클릭수와 T 쿼리에 대해 A 알고리즘이 받는 클릭수간의 예상 차이를 고려
	- 이 예상 차이를 regret of A라 부르고 $$R_A(T)$$로 표시
	- 어떤 쿼리 시퀀스에 대해 쿼리당 regret이 0에 접근하면 베이즈 최적으로 점근적임
	- Asymptotically Bayes Optimal 알고리즘의 표준 지표는 라운드당 regret이 0에 가까워지는 수렴 속도





---

추가로 읽을 자료

- [Deep Contextual MAB](https://arxiv.org/pdf/1807.09809.pdf)
- MAB [Survey](https://arxiv.org/pdf/1510.00757.pdf)

- 전상혁님 [MAB 글](http://sanghyukchun.github.io/96/) 


- 숨니의 무작정 따라하기 [블로그](http://sumniya.tistory.com/12?category=781574)



- [위키피디아](https://en.wikipedia.org/wiki/Multi-armed_bandit)








