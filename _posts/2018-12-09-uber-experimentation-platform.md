---
layout: post
title:  "Uber Experimentation Platform(XP) 이해"
subtitle: "Uber 실험 플랫폼 이해"
categories: data
tags: engineering
comments: true
---

- 우버 테크 블로그에 있는 [Under the Hood of Uber's Experimentation Platform](https://eng.uber.com/xp/) 을 읽고 정리한 글입니다
	- 제가 관심있는 부분 위주로 정리했기 때문에 자세한 이해를 하고 싶은 분들은 원문을 읽어보길 추천합니다! 


---

## Uber's Experimentation Platform
- 우버에선 실험을 통해 고객 경험(UX)를 향상
	- 새로운 기능 테스트 및 앱 디자인 향상시 사용
	- 새로운 아이디어, 제품 feature, 마케팅 캠페인, promotion 및 기계학습 모델의 효과를 측정, 디버깅, 측정 및 모니터링 가능
- Uber Rider, Ubet Eats, uber Freight 앱 전반에서 지원
	- A/B/N Test, 인과 분석, Multi-armed Bandit(MAB)에 기반한 지속적 실험

<img src="https://www.dropbox.com/s/1l6haiwszmrw0vn/Screenshot%202018-12-09%2010.14.29.png?raw=1">

- 통계 방법을 크게 4가지로 사용
    - 1) 고정된 horizon A/B/N Test(t-test, 카이제곱, rank-sum test)
    - 2) Sequential probability ratio tests(SPRT)
    - 3) causal inference test (synthetic control and diff-in-diff tests)
    - 4) continuous A/B/N test using bandit algorithms(톰슨 샘플링, upper confidence bounds, contextual multi armed bandit test를 통한 베이지안 최적화)
    - 유형 1, 유형 2 오류의 확률을 계산시 편차 보정을 측정하는 회귀 방법을 사용하고, standard error를 측정하기 위해 block bootstrap와 delta methods를 사용

### Classic AB Test
<img src="https://www.dropbox.com/s/fh3lblrpm8gxi4u/Screenshot%202018-12-09%2010.18.23.png?raw=1">

- 실험군과 대조군을 설정한 후, 그룹간 metric을 측정해 실험 효과를 측정
	- Feature release 실험시 주로 사용

<img src="https://www.dropbox.com/s/b0l6pt7kevdv6l6/Screenshot%202018-12-09%2010.19.05.png?raw=1">

- 측정값의 평균, lift(treatment effect)가 중요한지, 샘플 사이즈는 충분히 큰지 등을 나타냄
	- (역자) 이런 기능은 통계적 모델링에서 항상 필요하기 때문에, 한번 만들면 계속 재사용 가능! 이 부분 추가 학습하기


### 통계 엔진
<img src="https://www.dropbox.com/s/j7kngctastxz8lw/Screenshot%202018-12-09%2010.24.22.png?raw=1">

- Data validation -> check metric type -> Data preprocessing -> 각 방법론 적용 -> P value calculation -> A/B/N test -> Lift 계산 -> 검정력, sample 사이즈 계산
- 무작위 실험할 때 첫 단계 : 의사 결정(decision) metric 결정
	- decision metric 예 : 라이더 총 예약 수
	- 측정 항목에 따라 Google 통계 엔진은 다양한 통계 가설 테스트 절차를 적용해 보고서 생성
- (역자) 아키텍쳐 참고할 수 있을듯
    - 데이터가 들어와서 각 과정을 check
    - 이렇게 거대하지 않아도 되고, simple하게 먼저 구축 시작해도 좋을듯

### 통계적 방법의 핵심 구성요소
- 데이터를 수집하고 2가지 주요 문제를 감지
	- 표본 크기의 불균형
	    - 실험군과 대조군의 표본 크기 비율이 예상한 것과 크게 다를 경우
	    - 이런 시나리오는 무작위 추출 메커니즘을 2번 확인해야 함
	- Flickers(깜빡임?)
	    - 실험군에서 대조군으로 전환된 경우
	    - 예 : 안드로이드에서 아이폰으로 바꾼 사람
	    - 이럴 경우 우버는 분석에서 제외한다고 함
	    - (역자) 아예 실험군/대조군을 전환될 수 없도록 설정하면 될 듯
- User level의 metrics
    - 1) Continuous metrics
        - 유저당 예약 비율같은 1가지 숫자로 나타낼 수 있는 지표
    - 2) Proportion metrics
        - 회원가입을 하고 어떤 여행을 완료한 비율을 Test, binary
    - 3) Ratio metrics
        - 2개의 숫자 값을 사용한 metric
        - 예를 들면 (총 트립 요구수/완료된 트립의 수)
- AB Test의 퍼포먼스를 증대시키기 위한 전처리
    - 1) Outlier detection
        - clustering-based 알고리즘을 사용해 아웃라이어를 탐지하고 제거
    - 2) Variance reduction
        - 분산 감소는 가설 테스트의 통계 능력을 향상시킬 때 도움이 됨
        - 사용자가 적거나 정확성을 희생하지 않고 조기에 실험 끝낼 경우 유용
        - [CUPED 방법](https://www.exp-platform.com/Documents/2013-02-CUPED-ImprovingSensitivityOfControlledExperiments.pdf) 사용
    - 3) Pre-experiment bias
        - 다양한 유저가 존재하기 때문에 그룹간 실험 편향을 교정할 필요가 있음
        - diff-in-diff를 사용
            - 어떤 내용인지는 설명 안나옴
- P value 계산이 핵심
    - p value는 결과가 중요한지 여부를 판별
    	- p value와 오 탐지율(Type 1 오류, 0.05)을 비교
    - 상황마다 다른 방법을 활용
	    - 1) Welch's t test
	        - 연속 측정과 같은 기본 Test
	        - 예 : 완료된 트립
	    - 2) Mann-Whitney U test
	        - 데이터의 극심한 skewness를 탐지할 때 사용되는 rank sum에서 효과적(?)
	    - 3) Chi-squared test
	        - 라이더 리텐션, propertion metric을 사용할 경우
	    - 4) Delta method and bootstrap methods
	        - 샘플 크기가 작거나 ratio metrics 실험일 경우
	        - standard error estimation
	        - 좀 더 검색 필요
- 검정력(power)를 위해 2종 오류도 check

### Metrics management
- 사용 가능한 메트릭 추천
	- 1000개 이상의 측정 항목이 있음. 사용자가 측정 항목 결정하는게 점점 어려워짐 
- Item-item collabortive filtering 방법 사용
	- 2가지 score를 측정해서 제일 높은 것을 추천 
	- Popularity score
		- 함께 선택될수록 해당 측정 항목에 대한 점수가 높아짐 
		- [Jaccard_index](https://en.wikipedia.org/wiki/Jaccard_index) 사용
	- Absolute score
		- 피어슨 상관 점수

### Sequential testing
<img src="https://www.dropbox.com/s/tx6406uraltem6o/Screenshot%202018-12-09%2010.34.37.png?raw=1">

- 전통적인 AB Test는 샘플을 반복적으로 가져와 1종 오류를 부추김
	- 순차적 테스트는 핵심 비즈니스 메트릭을 지속적으로 모니터링 하는 방법 제공
	- 1종 오류를 부풀리지 않으며 confidence를 조절
	- 진행중인 실험에 대한 모니터링

### Methodologies
- Metrics 모니터링 목적으로 [mSPRT](https://en.wikipedia.org/wiki/Sequential_probability_ratio_test)(mixed Sequential Probability Ratio Test)와 FDR을 사용한 분산 추정을 위한 테스팅을 활용
- Mixture Sequential Probability Ratio Test
    - 모니터링할 때 가장 보편적으로 사용하는 방법
    - (역자) 추가 공부 필요
- Variance estimation with FDR control
    - 순차 테스트를 올바르게 적용하기 위해 가능한 정확한 분산을 추정해야 함
    - 비교군 대조군 그룹의 누적 차이를 모니터링
    - delete-a-group jackknife variance estimation과 block bootstrap 방법을 사용해 연관된 데이터에서 mSPRT 테스트를 일반화

### Continuous experiments
<img src="https://www.dropbox.com/s/l09k7sxzrthvbl8/Screenshot%202018-12-09%2010.38.28.png?raw=1">

- driver, rider, eater, restaurant, delivery-partner 경험을 지속적 실험을 통해 최적화하려고 함
	- bandit과 최적화에 초점을 둔 강화학습 방법을 구현해 실험
	- 최근 컨텐츠 최적화를 위한 기술을 사용해 고객 engagement(참여)를 향상


### Case Study
<img src="https://www.dropbox.com/s/c5c3bg3zf2g6goo/Screenshot%202018-12-09%2010.39.09.png?raw=1">

<img src="https://www.dropbox.com/s/mv48kvw8ut85rz2/Screenshot%202018-12-09%2010.39.23.png?raw=1">


### Moving forward
- 점점 더 지능적인 실험 플랫폼을 구축하려고 함
- (역자) 아마 지금은 더 좋아졌을 것 같음
	- 머신러닝 관련은 글에 안나와서 아쉬움
	- 다른 [블로그 글](https://eng.uber.com/experimentation-platform/) 도 읽어볼만함! 단, 2017년 글 



### Reference
- [우버 테크 블로그 글](https://eng.uber.com/xp/)




