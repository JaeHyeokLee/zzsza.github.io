---
layout: post
title:  "BigQuery non-partition Table을 partition Table로 옮기기"
subtitle: "Migrating BigQuery non-partition Table to partition Table"
categories: gcp
tags: BigQuery
comments: true
---

- BigQuery의 non-partitioned Table을 partition Table로 옮기는 방법에 대해 작성한 글입니다


---

### BigQuery Partitioned Tables
- BigQuery엔 파티션을 기반으로 저장하는 Table이 있음
- 데이터를 보다 쉽게 관리하고, 쿼리할 수 있게 해줌
- 파티션을 추가하는 경우 장점은 다음과 같음
	- Query 탐색 범위를 줄임(=쿼리 탐색 비용 감소)
		- BigQuery는 쿼리가 데이터를 탐색하는 양만큼 비용을 부과함
		- 파티션이 걸린 경우 해당 파티션 범위만 탐색하기 때문에 비용이 덜 부과될 수 있음
	- Query 속도가 빨라짐(=쿼리 성능 증가)
		- BigQuery에서 데이터의 양에 따라 쿼리 소요 시간이 달라짐
		- 데이터의 양이 적으면 더 빠르게 결과가 나타남
- Partition 나누는 기준
	- 1) Ingestion time(수집 시간) : 데이터 수집(로드) 날짜를 기준으로 파티션 나눔
	- 2) DATE/TIMESTAMP : Date나 Timestamp로 파티션을 나눔
	- 3) Integer(정수 범위) : 정수 값을 기준으로 파티션을 나눔
- 테이블 샤딩
	- 날짜/타임스탬프로 파티션을 나눈 테이블이 아닌 Table 이름에 suffix를 붙여서 테이블을 샤딩할 수 있음
	- 예: firebase 데이터가 저장되는 analytics_XXX 데이터셋의 Table들(analytics_XXX.events_20200211 등)
	- 날짜/타임스탬프로 파티션을 나눈 테이블이 샤딩된 테이블보다 성능이 뛰어남
	- 샤딩 테이블을 만들 땐 BigQuery가 각 테이블의 스키마 및 메타데이터 복사본을 유지해야 하고, 각 쿼리 대상 테이블의 권한을 확인해야 함
		- 쿼리 오버헤드가 추가되고 쿼리 성능에 영향을 미침
	- 따라서 샤딩보다 날짜/타임스탬프로 파티션을 나눈 테이블 사용하는 것이 좋음
	- 샤딩된 테이블은 쿼리시 최대 1,000개의 테이블만 참조할 수 있고, 날짜/타임스탬프로 파티션을 나눈 테이블은 최대 4,000개의 파티션을 가질 수 있음

<br />

---

### Non-partitioned Table을 Partitioned Table로 복사하기
- 특정 Task를 반복해서 수행하는 경우
	- 예를 들어 서비스의 Raw 데이터를 가공해 테이블에 적재하는 경우
	- 처음부터 만든다면 아예 Partitioned Table로 만들어서 Airflow 등에서 실행시키면 됨
	- 단, 이미 서비스가 어느정도 운영한 후에 특정 시점에 데이터를 Migration하고 그 다음 날부터 Airflow DAG을 돌리고 싶은 경우 과거 데이터를 한번에 복사해야 함
- 과거 데이터를 복사할 때, BigQuery UI에서 쿼리를 날리고 Table로 저장하는 방법이 있음
	- 그러나 "Incompatible table partitioning specification when copying to the column partitioned table" Error가 발생함
	- Partition Spec이 호환되지 않아 발생한 문제
- 터미널에서 `bq query`를 사용함
	- bq query가 길어질 것 같아 쉘스크립트 작성(query_to_partition_table.sh)
	- bq query는 Query의 결과를 Table로 바로 저장해주고, 파티션 옵션도 설정할 수 있음
	- destination_table의 뒤를 수정하고 맨 아래 SELECT 부분을 수정하면 됨
	- time_partitioning_field는 date_kr이고, require_partition_filter=True를 주면 쿼리할 때 항상 WHERE 절에 파티션 필터를 걸어야 함
		- BigQuery를 안정적으로 운영하기 위해 이 옵션은 꼭 주는 것을 추천



```
#!/bin/bash
bq --location=US query \
--destination_table {dataset}.{table} \
--time_partitioning_field date_kr \
--require_partition_filter=True \
--use_legacy_sql=false \
'
SELECT 
  *, date_kr
FROM Table
'	
```

- 실행
	
	```
	bash query_to_partition_table.sh
	```


- 혹시 Error가 발생한다면
	- bq command가 제대로 작동하는지?
		- 작동하지 않으면 Cloud SDK를 설치
		- [공식 문서](https://cloud.google.com/sdk/docs/?hl=ko) 참고해서 설치하면 됨
	- gcloud에 원하는 프로젝트 설정이 되어있는지?
		- 아래 명령어를 통해 현재 로컬에 설치된 gcloud 설정을 확인
		```
		gcloud config configurations list
		```
		
		- 원하는 프로젝트의 설정이 없다면 gcloud init을 통해 새 설정을 추가
	- SELECT 쿼리에서 ''을 사용하는 경우
		- 예 : SELECT * FROM Table WHERE event_name='user_engagement' 
		- ""로 수정해야 함. 쉘 스크립트에선 ''와 ""이 다름
		- 스택오버플로우의 [Difference between single and double quotes in Bash](https://stackoverflow.com/questions/6697753/difference-between-single-and-double-quotes-in-bash) 글에 자세히 나옴

	
<br />
	
---	
	
### Reference
- Google Cloud Platform Document
	- [Managing partitioned tables](https://cloud.google.com/bigquery/docs/managing-partitioned-tables#copying_partitioned_tables)


<br />

