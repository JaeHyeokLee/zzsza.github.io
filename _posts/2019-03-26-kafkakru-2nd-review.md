---
layout: post
title:  "KafkaKRU(Kafka 한국사용자 모임) 2회 미니밋업 후기"
subtitle:   "KafkaKRU(Kafka 한국사용자 모임) 2회 미니밋업 후기"
categories: etc
tags: lecture
comments: true
---

- 카프카 한국자 사용자모임에서 진행한 2회 미니밋업 후기입니다


---


## 행사 정보
- [Facebook Event URL](https://www.facebook.com/events/2222351638018632/)

	```
	1. 제목: KafkaKRU(Kafka 한국사용자 모임) 제2회 미니밋업
	
	2. 신청: https://www.onoffmix.com/event/171028
	
	3. 날짜: 3월 26일 화요일 오후 6시 50분
	
	4. 장소: 서울특별시 송파구 올림픽로35길 125 삼성SDS 잠실 Campus 동관 지하2층 다목적홀
	
	5. 인원: 35명
	
	6. 후원:
	(1) 삼성 SDS: 장소 및 다과
	(2) 카카오: 카카오 스티커, 카카오 프렌즈 상품
	(3) 책만 출판사: <대시보드 설계와 데이터 시각화> 3권, <블록체인 기업으로 가는 길> 3권
	(4) 젯브레인
	
	7. 일정
	18:50 ~ 19:00 인사 + 네트워킹
	19:00 ~ 19:20 Kafka를 활용한 캐시 로그 처리 - 김현준(카카오)
	19:20 ~ 19:40 Kafka를 활용한 Elasticsearch 실무프로젝트 소개 - 이은학(메가존)
	19:40 ~ 19:50 브레이크 타임
	19:50 ~ 20:10 Kafka를 활용한 rabbitmq 로그 처리 - 정원빈(카카오)
	20:10 ~ 20:30 Kafka를 Microservice Architecture에 활용하기 - 이동진(Apache Software Foundation)
	20:30 ~ 20:50 Kafka 프로듀서 & 컨슈머 - 강한구(카카오 모빌리티)
	21:00 ~ 뒷풀이
	```

## 인사
<img src="https://www.dropbox.com/s/171e1o759mlbadb/2019-03-26%2018.52.45.jpg?raw=1">

- 발표 자료 : [https://github.com/kafkakru/meetup](https://github.com/kafkakru/meetup)
- 나중에 스티커를 만든다고 하심!

## Kafka를 활용한 캐시 로그 처리 사례
- 카카오 김현준님
- 1도 모르는 사람이 Kafka & ELK로 캐시로그 처리해보기
- 캐시 시스템과 실시간 로그분석의 필요성
    - 캐시 시스템
        - 오픈소스를 활용한 구성(nginx + ATS)
        - 자체 개발 캐시서버
        - img/static image(css/js), 동영상 등을 처리하기 위한 캐시 시스템
        - 1차 Cache ⇒ 2차 Cache ⇒ 원본(Origin)
    - 실시간 로그 분석 필요성
        - Image size, 404, 응답 지연 등 ⇒ 하루 전 데이터를 가지고 모니터링
- 캐시 로그 분석 시스템 구축
    - 운영의 니즈를 반영하고자 시스템 구축 시작
    - ELK로 시작, 1차 Cache에서 access_log를 그림
        - 요청이 많고, 트래픽이 많음 ⇒ 로그도 많음
        - 로컬 캐시 서버에 로그를 쌓기 싫어서, nginx에서 바로 쏨
- 문제 인지와 Kafka 도입 그리고 Lag
    - 키바나 그래프를 보다보니 로그가 너무 조금만 들어오고 있음!?
    - 분석 시작
    - Syslog 전송 과정에서 누락? Logstash 성능?
    - 주변에 물어보니 대용량이나 많은 트래픽은 logstash 앞단에 큐 하나!
    - redis나 rabbitmq를 찾아보다가 승범님이 카프카를 쓰길 권함
    - lag이 계속 쌓임...! lag은 처리하지 못한 수치
        - 파티션을 쪼개던지, 컨슈머를 늘리거나 등등
        - 로그 분석이 돈 버는 것은 아니라 컨슈머를 늘리기엔 눈치가 보이고.. 파티션 조정해도 효과가 적음
    - logstash 설정이 잘못된 것 같으니 다시! ⇒ 모두 동일..
    - auto.commit.interval.ms=5000과 auto.commit= true
        - interval을 줄이니 lag이 사라짐
- 현재는 수백대의 cache 서버 로그를 초당 15만건 이상 처리
    - 대략 30테라?


---

<br/><br/>

## Kafka를 활용한 Elasticsearch 실무프로젝트 소개
- 메가존 이은학님
- 빅데이터 플랫폼 구축 및 활용솔루션 개발 중
- 프로젝트
    - 카드사
    - 비정형 / 정형 / 외부 데이터 ⇒ 분산되어 있는데 통합!
    - 빅데이터 허브 : 엘라스틱 서치 + 하둡
    - 사이언스 플랫폼
- 프로젝트에서 Elastic Stack 활용 영역
    - 비정형 데이터 분석
        - Google analytics
        - 고객 상담데이터(STT) ⇒ RESTFul 한글 형태소 분석기(Nori)
        - 웹/앱 로그
- 아키텍쳐
	- <img src="https://www.dropbox.com/s/1jpa38uk5qyvmho/2019-03-26%2019.28.17.jpg?raw=1"> 
	- 스트리밍 데이터(24시간 발생하는 웹, 고객 상담) ⇒ 카프카
	- GA 데이터 ⇒ Batch로 logstash
- 카프카 사용 이유
    - 데이터가 많고, 다른 곳에서 사용
- 카드사라 고객 상담 데이터 중 `탈퇴`라는 단어나 `카드론`은 민감 ⇒ 이 단어가 나오면 해당 부서에 Push ⇒ 대응(카드론은 수익에 연결된 단어)
- 프로젝트 이슈
    - 사용자 쿼리 기록해야 함 ⇒ 금융감독원 감사
    - 6.5.1부터 UserID를 남길 수 있음
    - xpack.security.audit.enabled; true
    - xpack.security.audit.outputs: logfile
    - 통신 노드도 남음 ⇒ 제외할 ID를 설정해야함
        - kibana, 다른 id
- Audit 활용 사례
    - 건수 확인 (curl)
    - 조회(페이징)
        - 주의 사항 : 엘라스틱은 기본적으로 10000건까지만 반환함. 설정 필요
            - max_result_window를 설정하고 조회한 후, 해제(null) ⇒ 해제 안하면 부담이 될 수 있음
- 개인정보 비식별화
    - 비정형 데이터 특정상 개인정보의 식별범위 모호
    - 처음엔 할 수 없다고 했는데, 생각해보니 몇 패턴이 있음
        - 고객 상담을 전문으로 하는 분들은 단어 선택을 또박 또박하게 함 ⇒ 음성 분석 파일을 분석하니 ~~ 회원님 ~~님이라고 정확하게 발음하는 것을 확인함 ⇒ 로직 처리
        - 카드 번호, 주민번호 : 연속되는 숫자 체크
        - 치환 : 주소
    - Ruby Filter(str.rb) 작성 후, str.conf(logstash 적용)
- 빅데이터 에코시스템 연계
    - Elasticsearch → Hadoop
        - ES Hadoop 사용
        - 일별을 index로 지정
        - N번 실행시 중복 방지하도록 overwriting table
    - Hadoop → Elasticsearch
    - 명사 별도 적재 사례
        - 적재된 한글 문장 데이터 중 명사만 별도 field로 적재 필요
        - 이유 : Nori 형태소 분석 결과를 타 빅데이터 시스템에서 활용
            - REST Call이 힘드니 하둡에 적재
        - 아이디어1 : 인덱스 데이터는 날짜별로 쌓임
        - 아이디어2 : logstash를 통해 reindex가 가능
        - 아이디어3 : Cron 형태의 배치가 가능
        - INDEX 조회시 source와 토큰을 동시 조회한 후, 동일 INDEX에 source와 토큰 업데이트!
- 소스도 올라와있음!

---

<br/><br/>

## Kafka를 활용한 RabbitMQ 로그 처리
- 카카오, 정원빈님
- RabbitMQ
    - erlang으로 구현된 AMQP 메시지 브로커
    - Advanced Message Queue Protocol
    - AMQP
        - 퍼블리셔가 Exchange → 큐에 전달(메세지를 저장하는 저장소) → Consumer에서 push/pull 방식으로 가져감
    - Exchange, queue가 VHOST 논리 단위로 생성
    - 퍼블리셔가 제대로 보냈는지, 컨슈머가 제대로 받았는지 확인할 수도 있음
    - TCP 연결 체크하는 로직
- RabbitMQ 모니터링
    - Server resource
        - 메모리를 모니터링
    - RabbitMQ LOG
    - AMQP OPERATION
    - 밑에 2개를 카프카를 사용함
    - Kafka + Elastic Search로 로그 처리 시스템을 개발
- Rabbit MQ에서 Filebeat/Worker에서 → APP Topic, AMQP Topic을 카프카로 전송 → Elastic Search로 저장 → Kibana
    - 토픽 중 특정 단어가 포함되었거나 AMQP 중 확인하고 싶은 오퍼레이션을 파싱, 구별해서 관리자에게 알람이 가도록 구성
- RabbitMQ? Kafka?
    - 둘다 메세지 브로커
        - RabbitMQ도 멀티컨슈머가 가능하긴 한데, 추가될 때마다 큐를 추가적으로 생성해야 함
            - Exchange는 큐의 메세지만큼 복사해서 전달
        - Kafka는 Consumer를 추가하면 그냥 추가하면 끝
            - 하나의 동일한 메세지를 여러개의 컨슈머가 동시에 할 수 있어서 기능 확장이 용이함
    - 메시지 관점에서 상세 비교
        - RabbitMQ는 큐 기반이고 메모리에 저장됨
            - Consumer가 메세지 수신시 즉시 삭제
        - Kafka는 로그 기반이고 디스크에 저장
            - 리텐션 설정하면 그 기간동안 상시 보관
    - 메세지 처리 방식도 다름
        - RabbitMQ는 발송 확인 / 수신 확인 가능
            - Prefetch Count : Consumer에 이슈가 있을 때(제대로 수신하지 못할 떄) 다른 consumer나 다시 전송하도록 하는 설정
            - 메모리 사용률이 높을 때 차단
            - Exchange를 통해 특정 큐에 라우팅 가능
        - Kafka는 발송 확인 가능 / 수신 확인 불가능
            - Prefetch나 메모리 사용률 높을때 차단하는 설정 없음
- RabbitMQ는 똑똑하지만 조금 느린 브로커, Kafka는 게으르지만 매우 효율적인 브로커
- 서비스에 따라 적당한 것이 따로 있다고 생각함
    - 동시에 모두 사용도 가능
- RabbitMQ는 제이피모건에서 많이 사용함


---

<br/><br/>


## Kafka Streams : Interactive Queries
<img src="https://www.dropbox.com/s/4ykqy84awjik760/2019-03-26%2020.38.05.jpg?raw=1">

- Apache Software Foundation, 이동진님
- Kafka Topic에 저장된 내용을 Microservice에서 참조하고 싶은데, 어떻게 해야 하나요?
    - Redis 같은데 저장해서 써야 하나요?
        - Producer → Kafka → Consumer → Redis → Microservice?
    - 이런 방법이 아니라!
        - Interactive Query 기능 또는 Queryable Store 기능!
- Kafka Streams
    - Kafka 0.10에 도입된 Stream 처리 library
    - Kafka Topic을 실시간으로 받아서 처리하는 루틴을 간편하게 정의 가능
        - High Level DSL : KStream, KTable
        - Low Level API도 지원
    - 장점
        - Task 관리가 필요 없음
            - 필요한만큼(=파티션 수) 알아서 작업(task)을 생성하고 thread pool에 분배
            - 하나의 작업이 하나의 (topic, partition)에 대한 처리 전담
        - consumer group 기능을 사용해서 구현됨 → coordination이 필요 없음
        - 프레임워크가 아닌 라이브러리 ⇒ 갖다 쓰면 됨
- wordcount 예제
    - props와 topology 객체를 넣어주면 카프카 스트림즈 객체가 됨
    - 스트림즈에 start method를 실행하면 지정한 로직대로 진행
- Key-value storage
    - Kafka Streams가 처리의 효율성을 위해 내부적으로 만든 저장소
    - RocksDB로 구현됨(Facebook에서 만든 inmemory db)
    - 직접 생성할 수도 있고, 사용자가 임의로 생성할 수도 있음
    - KTable 객체를 생성하면 반드시 하나가 함께 생김
- Interactive Query
    - Kafka Streams 내부에 생성된 key value storage의 내용과 위치를 조회할 수 있도록 해주는 기능
    - Queryable Store : interactive query 기능이설정된 key value storage
    - 수정은 안됨
- Interactive Query 예제
	- <img src="https://www.dropbox.com/s/252ca0sjobh2isq/2019-03-26%2020.45.29.jpg?raw=1">
	- <img src="https://www.dropbox.com/s/smn7utvu9vzg3q0/2019-03-26%2020.46.08.jpg?raw=1">
	- <img src="https://www.dropbox.com/s/nk8s2kpjzzmvhd5/2019-03-26%2020.48.37.jpg?raw=1">

- 결론
    - KTable을 사용하면 Kafka Topic의 내용을 표 형태로 읽어올 수 있다
        - 그 내용을 Interactive Query를 사용해서 열어볼 수 있음
    - kafka Streams가 해주는 것
        - 현재 프로세스에서 잡고 있는 partition에 포함된 key에 대한 value 값
        - 현재 프로세스에서 잡고 있는 partition에 포함된 포트 값
- Spark Streaming은 언제 쓸까?
    - 여러 데이터 소스에서 읽고 복잡한 쿼리를 사용한다면 Spark Streaming

    
---

<br/><br/>    

## Kafka 프로듀서 & 컨슈머
- 카카오 모빌리티, 강한구님
- AB Test 플랫폼을 만들 때 Flink, Vertical Service에서 ETL을 담당
- 예전엔 카프카 쓰세요! Default만 해도 된다고 했는데 요새는 더 깊게 알아야 할 것 같아 자료를 만듬
- Producer
    - 메세지를 전송
    - producer객체를 생성할 때
        - Accumulator와 Network Thread가 생성됨
            - Accumulator : 사용자가 send한 reocrd를 메모리(recordbatch)에 차곡차곡 쌓아줌
            - Network Thread : RecordBatch를 브로커로 전송
    - For문을 돌 때
        - buffer.size, batch.size로 지정
        - 브로커로 전송하는 속도보다 쌓이는 속도가 많으면 내부적으로 어큐물레이터에 쌓이게 될테고, 버퍼 메모리만큼 차게 되면 어플리케이션에서 블락 됨
        - Network Thread가 별도의 쓰레드가 떠서 계속 쉴 틈 없이 작업을 수행
            - 해당 레코드 배치가 어떤 브로커로 갈지
            - 브로커를 상태 확인
            - 브로커별 레코드 배치를 재배열
            - 데이터 전송
            - linger.ms → send 속도가 너무 느려서 레코드 배치에 쌓이는 것이 별로 없을 때 이 시간만큼 기다렸다가 가져감
            - max.request.size → 바이크에 탈 사람의 수로 비유
            - max.in.flights.requests.per.connection
            - (발표 자료가 아래쪽으로 되어있어서 정확히 못봄..! 자료 나중에 공개되면 확인)
- Broker
    - 메세지를 저장
        - [Topic name] - [partition] 폴더 구조
        - confluent 블로그에서 본 내용인데, 하드 4테라 1개보다 1테라 하드 4개가 더 좋다!
        - Segment 단위로 파일 저장
            - \*.index, \*.log, \*.timeindex
            - 첫 offset이 파일명이 됨
- Consumer
    - 메세지를 가지고 옴
    - fetcher와 coordinator
    - Fetcher
        - poll 함수가 실행되면 적절한 크기의 record 리턴하고, 내부에 record가 없다면 브로커에게 record드를 요청하고 저장. 그리고 적절한 크기의 record 리턴하는 역할
    - Coordinator
        - fetcher가 열심히 일할 수 있도록 정보 제공
        - 어떤 토픽, 파티션을 consume할지
        - Broker의 group coordinator와 통신
        - Heartbeat, offset, commit, consumer group join도 함
    - 코드 예제를 통해 설명해주셨는데, 천천히 보면 좋을듯
    - Fetcher에 레코드가 있는 경우
        - 바로 리턴
        - max.poll.records : 한번에 가져올 레코드 수
        - max.poll.interval.ms : poll이란 함수를 호출하는 최대 시간
    - Fetcher에 레코드가 없는 경우
        - max.partition.fetch.bytes
        - fetch.min.bytes
        - fetch.max.wait.ms
- 부록
    - Consumer Rebalance
    - Consumer Group offset
        - 0.9 미만에선 zookeeper에 consumer offset을 저장 ⇒ 껏다가 켰다가 ⇒ 무리가 감
        - 0.9 이상에선 __consumer\_offset 토픽을 사용

    
---
    
### 후기
- 카프카를 많이 사용하진 않고, GCP에서 Pub/Sub 정도만 사용했지만 꾸준히 관심을 가져온 카프카! 새로운 시각을 얻을 수 있었음
- 잠실의 삼성 SDS에서 행사가 진행되었는데, SDS 건물 처음 들어가서 신기했음..!
- 행사장에 가니 서브웨이 샌드위치도 준비해주시고 + 중간에 피자도 주셔서 배부르게 먹었음..! 감사합니다
- Kafka는 ElasticSearch와 쓰는 조합이 정말 많은 것 같은데, 진짜일까? 확인해보기
- Kafka Streams와 Machine Learning을 합쳐서 사용하는 사례도 발견 : [kafka-streams-machine-learning-examples](https://github.com/kaiwaehner/kafka-streams-machine-learning-examples)
- 오랜만에 만난 분들과 인사해서 좋았음!
- [Kafka 한국 사용자 모임](https://www.facebook.com/groups/kafka.kru/) 가입 안하셨다면 가입 필수 :)
- 마지막 Google 설문지 링크는 bit.ly 같은 것으로 줄여서 bit.ly/kafkakru-미니밋업-2회-설문 이렇게 하셨으면 더 좋았을 것 같음..! (사실 설문하려고 했는데 수많은 텍스트 문자 있고, 뒤에서 안보여서 시도했다가 포기..OTL)
