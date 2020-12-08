---
layout: post
title:  "신입사원을 위한 웹서비스 확장 전략"
subtitle: "신입사원을 위한 웹서비스 확장 전략"
categories: development
tags: web
comments: true
---
강대명님의 [신입사원을 위한 웹서비스 확장 전략](https://www.slideshare.net/charsyam2/webservice-scaling-for-newbie) 발표 자료를 보며 정리 및 모르는 내용을 작성한 글입니다

## 주제
- 웹서비스를 확장하기 위해 알아야할 방법들
- 돈 많으면 서버를 빵빵하게 사용하면 끝ㅋ 그러나 항상.. 회사에선 비용 절감을 하려고 함

## 대규모 서비스 개발 노하우의 필요성
- 대규모 서비스 사례
	- 2011년 bit.ly : 초당 1216 clicks, 하루에 1억 5백만 클릭
	- 2011년 Netflix : 1년 사이에 트래픽 20배 증가
	- 배틀 그라운드 : 3개월만에 5배
- 이런 서비스 트래픽 증가를 버틸려면?
- 첫번째 고려 사항
	- 이 정도의 서비스 증가가 일어나면 어떻게 해야할까?
	- 한동안 서비스가 안된다면 어떤 일이 벌어질까?

### 대규모 서비스의 특징
- Elastic Resiliency 
- Elastic
	- 트래픽이나 상황에 따라서 서버의 추가/제거가 쉬워야 함
- Resiliency
	- 특정 장비의 장애 등은 자동으로 복구가 되어야 함
		- 서버가 복구되는 건 아님
		- 해당 장비의 장애로 인해 다른 쪽이 영향을 받지 않아야 함   
- Scale Up & Scale Out
	- 초당 1000 TPS 상황이라면
	- Scale Up : 3배 처리 가능한 서버를 투입
	- Scale Out : 서버의 개수를 늘려서 처리
- SPOF : Single Point Of Failure
	- 시스템의 구성 요소 중, 동작하지 않으면 전체 시스템이 중단되는 요소



- LB : Load Balancer	
	- 여러 Server에서 Traffic을 분산시켜줌
	- NAT(Netwrok Address Translation 
		- IP 주소를 공인 IP로 바꿀 때 사용하는 주소 변조 기능
	- Tunneling 
	- DSR(Dynamic Source Routing Protocol)
	- [로드 밸런서(Load Balancer)란?](https://nesoy.github.io/articles/2018-06/Load-Balancer) 참고

## 사례로 보는 확장 아이디어
<img src="https://www.dropbox.com/s/thss3nrms56pfs7/Screenshot%202018-07-27%2019.28.59.png?raw=1" width="500" height="250">

- 다시 돌아와서 어디를 확장해야 할까?
- API 서버
	- API 서버에만 부하가 몰리는 작업은?  
		- 파일 IO가 많은 정적 파일 서빙
		- 웹 스크래핑
		- 독립적인 작업이 가능한데, CPU나 다른 작업이 많이 필요한 MO 게임서버
		- 이미지의 영상 인식, 전처리
- DB 서버
	- 카톡방의 대화는?
	- 페이스북의 글, 댓글, 친구 관계는?
	- 유튜브 등에 올라가는 비디오나 댓글
	- 우리가 아는 대부분이 여기에 부하를 줌!
- Stateless한 서버는
	- API 서버는 비즈니스 로직만 가짐
	- 크고 안정적인 디비가 필요
	- 장점
		- 추가/삭제가 간단
		- 사용하는 쪽에서 주소만 추가하거나 제거해서 사용
		- Or 로드밸런서에 추가하거나 제거하면 끝
	- 단점
		- 결국 데이터의 저장이 필요하므로, 뒤에 책임을 떠넘기는 구조
		- 개별 성능은 Stateful한 경우보다 떨어질 수 있음
	- 이 방식을 사용한 이유
		- stateless한 서버에 신경쓰지 말고
		- 중요한 DB(Storage)에 집중하는 것이 더 좋다고 판단
		- 두 마리 토끼를 쫓지 말고 한 놈만 팬다
- Shard Nothing
- 일반적인 DB 서버는 Read에 부하가 많음
	- 읽기 분배 : Master에서만 Write, Slave에선 Only Read
	- 각 DB 서버를 2대로 늘림 => Slave 장비를 추가해도 계속 성능이 증가하진 않음
- Database Partitioning
	- Vertical
	- Horizontal
- Sharding Horizontal Partitioning
	- [Database의 샤딩(Sharding)이란?](https://nesoy.github.io/articles/2018-05/Database-Shard) 참고 
	- 특정 Key를 저장하는 방법
		- Range : 특정 범위대역으로 나누기 (User 1~100은 Server #1) => 데이터의 이동이 심해짐
		- Modular : 서버 대수로 나누기 (0%2=0, Server #1, 1%2=1면 Server #2, 3대가 되면 2가 아니라 3으로 나눔)
		- Inedxed : 특정 데이터의 위치를 가리키는 서버가 존재
		-  
	- 특정 Key를 찾는 방법	
	
	
	
- consistent hashing
- 	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
