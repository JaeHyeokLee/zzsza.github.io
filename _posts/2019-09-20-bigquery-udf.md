---
layout: post
title:  "BigQuery UDF 사용하기"
subtitle: "BigQuery UDF 사용하기"
categories: gcp
tags: BigQuery
comments: true
---

- Google Cloud Platform의 BigQuery에서 udf 사용하는 방법에 대해 작성한 글입니다
	- 이 글은 2019년 9월에 작성되었습니다
	- BigQuery temp udf, BigQuery persistent udf에 대해 다룹니다
	- 아직 persistent UDF는 pre-release이므로 추후에 기능이나 방식이 변경될 가능성이 있습니다

---

	
### BigQuery UDF	
- UDF
	- UDF는 User Define Function의 약자로 사용자가 정의한 함수
	- Python 같은 프로그래밍 언어에서 함수를 만드는 것처럼, SQL에서 함수를 만드는 것을 UDF라고 표현하기도 함
- UDF의 종류
	- Temp UDF : 쿼리문 위에 정의해서 사용하는 방식
	- Persistent UDF : BigQuery의 Dataset에 저장해서 사용하는 방식으로 여러 사람과 사용할 경우 유용
- UDF로 사용할 수 있는 언어
	- SQL 표현식
	- 자바스크립트
- Useful UDF 모음집
	- [bigquery-utils](https://github.com/GoogleCloudPlatform/bigquery-utils/tree/master/udfs/community)
	- [bigquery-jslibs](https://github.com/jatorre/bigquery-jslibs)

---

### Temp UDF(SQL)
- 일반 UDF는 Temp udf로 쿼리문을 작성할 때 함께 작성해야 함

	```
	CREATE  { TEMPORARY | TEMP }  FUNCTION function_name ([named_parameter[, ...]])
	[RETURNS data_type]
	AS (function_definition);
	```
	
	- RETURNS 뒤엔 리턴될 데이터의 타입(생략 가능하지만 명시적으로 적는 것을 추천)
	- AS 뒤엔 함수 정의
	- 정의 후 `;`를 꼭 붙여야 함
	
- SQL 표현식을 사용한 예시
	
	```
	CREATE TEMP FUNCTION add_three_and_divide(x INT64, y INT64) 
	RETURNS FLOAT64
	AS ((x + 3) / y);
	
	# 밑에는 Sample Data
	WITH numbers AS
	  (SELECT 1 as val
	  UNION ALL
	  SELECT 4 as val)
	SELECT val, add_three_and_divide(val, 2) AS result
	FROM numbers;
	```	
	
### Temp UDF(Javascript)
- Javascript UDF 외부 javascript 파일을 참고하거나 직접 javascript 문법을 사용할 수 있음
- 심플한 문법을 사용하면 " 하나만 사용해도 되고, snippet이거나 여러 줄이면 triple-quoted(""") 사용
- Best 사례나 한계점은 [링크](https://cloud.google.com/bigquery/docs/reference/standard-sql/user-defined-functions?#best-practices-for-javascript-udfs) 참고
- 자바스크립트 문법을 사용할 경우

	```
	CREATE  { TEMPORARY | TEMP }  FUNCTION function_name ([named_parameter[, ...]])
	RETURNS data_type
	LANGUAGE js 
	AS """body"""];
	```
	
	- 예시

	```
	CREATE TEMP FUNCTION customGreeting(a STRING)
	RETURNS STRING
	LANGUAGE js AS """
	  var d = new Date();
	  if (d.getHours() < 12) {
	    return 'Good Morning, ' + a + '!';
	  } else {
	    return 'Good Evening, ' + a + '!';
	  }
	  """;
	  
	SELECT customGreeting(names) as everyone
	FROM UNNEST(["Hannah", "Max", "Jakob"]) AS names;
	```
	
- 외부 자바스크립트 파일을 참고할 경우
	- Google Storage에 라이브러리 파일을 넣은 후 사용
	- 해당 라이브러리에 있는 함수를 AS 뒤에 넣어서 사용

	```
	CREATE  { TEMPORARY | TEMP }  FUNCTION function_name ([named_parameter[, ...]])
	RETURNS data_type
	LANGUAGE js 
	OPTIONS (
		library=['gs://bucket/library.js']
	)
	AS """librarys_method()"""];
	```	


---

### Persistent UDF
- Temp UDF는 쿼리를 작성할 때마다 위에서 정의해야 하는 불편함이 있음
	- 따라서 이런 정의를 저장해두고, 활용하고 싶을 경우 Persistent UDF 사용 
- Dataset 아래에 UDF로 저장해서 활용함
- CREATE 문 뒤에 TEMP 없이 바로 FUNCTION을 작성

### Persistent UDF 생성하기(SQL)
- BigQuery udf 데이터셋 생성
- multiply_inputs 함수 생성

	```
	CREATE OR REPLACE FUNCTION udf.multiply_inputs(x FLOAT64, y FLOAT64)
	RETURNS FLOAT64
	AS (x * y);
	```

- udf 데이터셋 확인
	- 언어, 반환 유형, 정의, 인수  
	- <img src="https://www.dropbox.com/s/op2xlsfodp3j93p/Screenshot%202019-09-21%2017.33.39.png?raw=1">

- 쿼리에서 UDF 사용하기
	- `Dataset.function_name`으로 사용 => 여기선 udf.multiply_inputs

	```
	WITH numbers AS
	  (SELECT 1 AS x, 5 as y
	  UNION ALL
	  SELECT 2 AS x, 10 as y
	  UNION ALL
	  SELECT 3 as x, 15 as y)
	SELECT x, y, udf.multiply_inputs(x, y) as product
	FROM numbers
	```	


### Persistent UDF 생성하기(Javascript)
- SQL에서 하는 방식과 동일
- BigQuery udf 데이터셋 생성(이미 존재하면 생략)
- custom_greeting 함수 생성

	```
	CREATE OR REPLACE FUNCTION udf.custom_greeting(a STRING)
	RETURNS STRING
	LANGUAGE js AS """
	  var d = new Date();
	  if (d.getHours() < 12) {
	    return 'Good Morning, ' + a + '!';
	  } else {
	    return 'Good Evening, ' + a + '!';
	  }
	  """;
	```  

- udf 데이터셋 확인
	- <img src="https://www.dropbox.com/s/rkimy3xsup7vlvo/Screenshot%202019-09-21%2017.41.33.png?raw=1">

- 쿼리에서 UDF 사용하기
	
	```
	SELECT udf.custom_greeting("BYEON") AS result
	```
	
- 외부 자바스크립트 파일을 활용할 경우
	- temp UDF처럼 Google Storage에 저장하고 사용
	- Uber의 H3 함수를 사용해보는 예시
	- [Github](https://github.com/jatorre/bigquery-jslibs/blob/master/libs/h3/)에서 자바스크립트 파일 다운로드
		- Raw 클릭 후 다른 이름으로 저장
	- Google Storage에 해당 파일 업로드
		- 저는 geultto-udf라는 곳에 저장
		- <img src="https://www.dropbox.com/s/7v0f8n1m7qfysts/Screenshot%202019-09-21%2017.53.45.png?raw=1">
	- BigQuery에서 함수 정의
		
		```
		CREATE OR REPLACE FUNCTION udf.geo_to_h3(lat FLOAT64, lng FLOAT64, resolution INT64)
		RETURNS STRING 
		LANGUAGE js 
		AS
		'''return h3.geoToH3(lat, lng, resolution)''' 
		OPTIONS (library=["gs://geultto-udf/h3-js.umd.3.5.0.js"]);
		```  	
	
	- udf 데이터셋 확인
		- <img src="https://www.dropbox.com/s/uvscxluv12j79lx/Screenshot%202019-09-21%2017.55.16.png?raw=1">
	- 쿼리 예시
		
		```
		SELECT udf.geo_to_h3(36.123123, 128.123123, 8) as h3
		```

---

### 정리
- CREATE TEMP FUNCTION을 사용하면 TEMP UDF 생성
- CREATE FUNCTION을 사용하면 Persistent UDF 생성
	- Dataset 아래에 저장 
- CREATE OR REPLACE FUNCTION는 기존에 함수가 저장할 경우 수정함
- UDF는 SQL, Javascript로 생성 가능
	- Javascript 활용시 단순 문법으로 생성 가능하고, 자바스크립트 라이브러리 파일을 사용해 생성 가능
	- [공식 문서](https://cloud.google.com/bigquery/docs/reference/standard-sql/user-defined-functions#best-practices-for-javascript-udfs) : 권장 사항, 한도, 제한사항이 있으니 꼭 확인!

---

### Reference
- [Standard SQL User-Defined Functions](https://cloud.google.com/bigquery/docs/reference/standard-sql/user-defined-functions)
- [New in BigQuery: Persistent UDFs](https://medium.com/@hoffa/new-in-bigquery-persistent-udfs-c9ea4100fd83)
- [Persistent UDFs + BQ GIS = ♥](https://medium.com/@mentin/persistent-udfs-bq-gis-66c8db903e4f)
- [bigquery-utils](https://github.com/GoogleCloudPlatform/bigquery-utils/tree/master/udfs/community)
- [bigquery-jslibs](https://github.com/jatorre/bigquery-jslibs)