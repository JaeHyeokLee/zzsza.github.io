---
layout: post
title:  "BigQuery UNNEST, ARRAY, STRUCT 사용 방법"
subtitle: "BigQuery UNNEST, ARRAY, STRUCT 사용 방법"
categories: gcp
tags: BigQuery
comments: true
---

- BigQuery Unnest, Array, Struct 사용 방법에 대해 작성한 글입니다
- 목차
	- [들어가며](#들어가며)
	- [BigQuery ARRAY](#bigquery-array)
	- [BigQuery STRUCT](#bigquery-struct)
	- [BigQuery UNNEST](#bigquery-unnest)
	- [응용](#응용)
	- [정리](#정리)


---

### 들어가며
- BigQuery는 SQL 문법을 사용하고 있기 때문에, 많은 사람들이 처음에 쉽게 접할 수 있음
	- 그러나 자주보기 힘든 ARRAY, STRUCT, UNNEST를 만나면 많은 사람들이 어려워함
	- 특히 Google Analytics 데이터나 Firebase 데이터 작업시 Cannot access field name on a value with type ARRAY 이런 Error를 접함
	- 자료형들에 대한 설명과 이런 데이터를 쿼리할 때 어떻게 사용해야 하는지에 대해 정리하려고 함
- 참고로, BigQuery는 비정규화되었을 때 성능이 가장 뛰어남 
	- 비정규화를 위해 중첩(Nested) 및 반복(Repeated) 열을 사용함
	- 중첩된 레코드를 사용할 때 장점
		- 빅쿼리 퍼포먼스 개선
		- 데이터 저장 용량의 효율
		- 스키마가 바뀌어도 유연하게 대응 가능
	- 이 내용은 [BigQuery Document](https://cloud.google.com/bigquery/docs/nested-repeated)에 나옴



<br />

---

<br />

### BigQuery ARRAY
- [배열 함수 Document](https://cloud.google.com/bigquery/docs/reference/standard-sql/array_functions?hl=ko#generate_date_array)
- BigQuery는 데이터 유형이 동일한 값으로 구성된 목록을 ARRAY(배열)라 부름
	- 파이썬의 List와 유사함(완전히 동일하진 않음)
	- 하나의 행에 데이터 타입이 동일한 여러 값이 저장됨
	- 빅쿼리 UI에서 배열을 보여줄 때 세로로 나열됨

	```
	SELECT 
		[1,2,3] AS array_sample, 1 AS int_value
	UNION ALL
	SELECT 
		[3,5,7] AS array_sample, 2 AS int_value
	```
	
	- 쿼리 결과
		- <img src="https://www.dropbox.com/s/nnb5c1z7u7u5wfk/Screenshot%202020-04-12%2013.15.58.png?raw=1">
		- **행을 보면 1과 2만 있고, 1 안에 array\_sample에 1, 2, 3이 세로로 출력**됨. 반면에 단순 숫자값을 나열한 int\_value는 1개만 보임
		- 행 하나에 딸려있는 경우 ARRAY!
		- array\_sample이 ARRAY(int_value는 INT64)
  	
- ARRAY 생성 방법
	- 1) 대괄호(`[`, `]`) 사용
	
		```
		SELECT [1, 2, 3] AS array_sample
		```
		
	- 2) ARRAY<> 사용
		- ARRAY<타입>을 작성하고 괄호 사용
	
		```
		SELECT ARRAY<INT64>[1,2,3] AS int_array
		```
	
	- 3) GENERATE 함수 사용
		- GENERATE\_ARRAY, GENERATE\_DATE\_ARRAY, GENERATE\_TIMESTAMP\_ARRAY 등 사용
		- GENERATE\_ARRAY(시작, 종료, 간격) : python에서 range(start, end, step)과 동일함
		
		```
		SELECT GENERATE_ARRAY(1, 10, 2) AS generate_array_data
		```
	
		- <img src="https://www.dropbox.com/s/b7myu91lmmyznlo/Screenshot%202020-04-12%2014.23.48.png?raw=1">
		- GENERATE\_DATE\_ARRAY도 GENERATE\_ARRAY와 동일

		```
		SELECT GENERATE_DATE_ARRAY('2020-01-01', '2020-02-01', INTERVAL 1 WEEK) AS generate_date_array_data
		```
		
		- <img src="https://www.dropbox.com/s/fup6oyrwwhkmf0c/Screenshot%202020-04-12%2014.26.10.png?raw=1">
		
	- 4) ARRAY_AGG 사용
		- ARRAY\_AGG, ARRAY\_CONCAT\_AGG 등 사용
		- Table에 저장된 데이터를 SELECT하고 ARRAY로 묶고싶은 경우 사용

		```
		WITH programming_languages AS
		    (SELECT "python" AS programming_language
		     UNION ALL SELECT "go" AS programming_language
		     UNION ALL SELECT "scala" AS programming_language)
		     
		SELECT ARRAY_AGG(programming_language) AS programming_languages_array
		FROM programming_languages
		```
		
		- <img src="https://www.dropbox.com/s/j8k2cdf3arhyhpz/Screenshot%202020-04-12%2014.35.10.png?raw=1">
		- 여기서 SELECT ["python", "go", "scala"] as programming_language로 가능한거 아니냐고 생각할 수 있는데, 데이터를 만드는 과정이라면 둘 다 가능하지만, Table에 저장된 데이터를 SELECT해서 집계할 땐 이렇게 해야함
		- SELECT ARRAY[programming\_language] AS programming\_languages\_array 이런 쿼리를 직접 돌려보면 배열이 아닌 행에 하나의 값만 저장되는 것을 볼 수 있음. ARRAY_AGG을 사용!
		- 만약 배열 안에 NULL이 하나라도 있는 경우, ARRAY\_AGG의 결과는 NULL이 나온다
			- 이 경우 IGNORE NULLS을 추가하면 널을 제외하고 연산함

		```
		SELECT ARRAY_AGG(x IGNORE NULLS) FROM UNNEST([1, NULL, 2, 4, 6]) x
		```
		
		- ARRAY\_AGG의 옵션 절에 궁금하면 [ARRAY_AGG](https://cloud.google.com/bigquery/docs/reference/standard-sql/functions-and-operators?hl=ko#array_agg)를 참고하면 된다(DISTINCT, IGNORE NULLS, ORDER BY 등을 사용할 수 있음)
		
- 배열 내 접근
	- 배열의 N번째 값을 가져오고 싶은 경우 OFFSET, ORDINAL을 사용할 수 있음
	- OFFSET : 0부터 시작
	- ORDINAL : 1부터 시작
	- 존재하지 않는 N을 지정하면 에러가 발생하는데, 이럴 경우 SAFE\_를 앞에 붙여주면(SAFE\_OFFSET, SAFE\_ORDINAL) 에러가 발생하지 않고 NULL이 return됨

	```
	WITH programming_languages AS
	    (SELECT "python" AS programming_language
	     UNION ALL SELECT "go" AS programming_language
	     UNION ALL SELECT "scala" AS programming_language)
	     
	SELECT 
	  ARRAY_AGG(programming_language)[OFFSET(0)] AS programming_languages_array,
	  ARRAY_AGG(programming_language)[ORDINAL(1)] AS programming_languages_array2
	FROM programming_languages
	```
	
	- <img src="https://www.dropbox.com/s/b3tnyhjxnu2hyxe/Screenshot%202020-04-12%2014.56.10.png?raw=1">

- 배열을 역순으로 반환하고 싶은 경우
	- ARRAY\_REVERSE 함수 사용

	```
	WITH programming_languages AS
	    (SELECT "python" AS programming_language
	     UNION ALL SELECT "go" AS programming_language
	     UNION ALL SELECT "scala" AS programming_language)
	     
	SELECT 
	  ARRAY_REVERSE(ARRAY_AGG(programming_language)) AS programming_languages_array_reverse
	FROM programming_languages
	```
	
- 배열의 길이(배열에 있는 요소들의 개수)가 궁금한 경우
	- ARRAY\_LENGTH 사용
	
	```
	WITH example_data AS(
	SELECT 
	  'kyle' AS name,
	  ['Python', 'SQL', 'R', 'Julia', 'Go'] AS preferred_language, 
	  31 AS age
	UNION ALL
	SELECT 
	  'max' AS name,
	  ['Python', 'SQL', 'Scala', 'Java', 'Kotlin'] AS preferred_language, 
	  29 AS age
	UNION ALL
	SELECT
	  'yun' AS name,
	  ['Python', 'SQL'] AS preferred_language, 
	  28 AS age
	)
	
	SELECT 
	  *,
	  ARRAY_LENGTH(preferred_language) AS preferred_language_len
	FROM example_data
	```	
	
	- <img src="https://www.dropbox.com/s/lpuheks993zutfu/Screenshot%202020-04-12%2015.00.28.png?raw=1" width="300" height="350">


<br />

---

<br />

### BigQuery STRUCT
- [데이터 유형 Document](https://cloud.google.com/bigquery/docs/reference/standard-sql/data-types?hl=ko#struct-type)
- 구조체로, BigQuery UI에서 RECORD로 표현됨
- 각각 유형(필수)과 필드 이름(선택사항)이 있는 정렬된 필드의 컨테이너
	- C의 Struct
	- Python 3.7에 나온 Data Class와 유사. [참고 Stackoverflow](https://stackoverflow.com/questions/35988/c-like-structures-in-python)
	- 처음엔 Python의 Dict과 유사한 느낌(절대 같지 않음!!!)이라 생각하면 편함
	- Python에서 Dict in List, List in Dict, List in List 처럼 BigQuery의 ARRAY와 STRUCT도 Array in Struct, Struct in Array, Struct in Struct 등이 가능함
	- 따라서 ARRAY를 접하다보면 STRUCT도 알아야 할 경우가 생김(그러나 ARRAY가 더 많이 사용됨)
	- Firebase에서 저장된 데이터를 볼 때 특히!
- STRUCT 생성 방법
	- 1) 소괄호(`(`, `)`) 사용 
	```
	SELECT (1,2,3) AS struct_test
	```
	
	- <img src="https://www.dropbox.com/s/5v8fczm5ulyum9v/Screenshot%202020-04-12%2015.36.47.png?raw=1">
	
	- 2) STRUCT<> 사용
		- <> 안에 타입을 지정해서 사용		
		```
		SELECT STRUCT<INT64, INT64, STRING>(1, 2, 'HI') AS struct_test
		```
		
		- <img src="https://www.dropbox.com/s/yh7arsaklttwrjm/Screenshot%202020-04-12%2016.02.39.png?raw=1">
		- 타입 앞에 이름을 지정할 수 있음

		```
		SELECT STRUCT<hi INT64, hello INT64, awesome STRING>(1, 2, 'HI') AS struct_test
		```
		
		- <img src="https://www.dropbox.com/s/1egac6ac9xu0eq1/Screenshot%202020-04-12%2016.05.03.png?raw=1">
		- 또는 타입을 지정하지 않고, AS로 이름을 지정할 수 있음
		
		```
		SELECT STRUCT(1 as hi, 2 as hello, 'HI' as awesome) AS struct_test
		```
		
		
	- 3) `STRUCT<x STRUCT<y INT64, z STRING>>`
		- 이런 표현을 종종 볼 수 있는데, 해석하면 바깥 STRUCT 안에 x라는 이름을 가진 STRUCT가 있고, 그 STRUCT엔 INT64 타입인 값이 1개, STRING 1개가 저장된 경우
		
		```
		SELECT STRUCT<struct_example STRUCT<y INT64, z STRING>>((2, 'HI')) AS struct_test
		```
		
		- 위 예시에선 바깥 STRUCT 이름은 AS struct_test로 정의함
		- <img src="https://www.dropbox.com/s/le1bspwc7r2vddv/Screenshot%202020-04-12%2016.08.25.png?raw=1">
	- 4) ARRAY 안에 여러 STRUCT를 사용하고 싶은 경우
		- ARRAY(SELECT AS STRUCT) 이런 형태로 사용
	
		```
		SELECT
		  ARRAY(
		    SELECT AS STRUCT 1 as hi, 2, 3
		    UNION ALL 
		    SELECT AS STRUCT 4 as hi, 5, 6
		  ) AS new_array
		```
		
		- <img src="https://www.dropbox.com/s/l4e62wty9isxuc5/Screenshot%202020-04-12%2016.38.40.png?raw=1">
	
<br />

---

<br />

### BigQuery UNNEST
- [UNNEST Document](https://cloud.google.com/bigquery/docs/reference/standard-sql/query-syntax?hl=ko#unnest)
- 예시를 위해 사람들이 선호하는 프로그래밍 언어와 나이가 저장된 데이터가 있다고 가정
- 배열에 통째로 접근하고 싶은 경우엔 평소처럼 컬럼을 지정
	- preferred_language

	```
	WITH example_data AS(
		SELECT 
		  'kyle' AS name,
		  ['Python', 'SQL', 'R', 'Julia', 'Go'] AS preferred_language, 
		  31 AS age
		UNION ALL
		SELECT 
		  'max' AS name,
		  ['Python', 'SQL', 'Scala', 'Java', 'Kotlin'] AS preferred_language, 
		  29 AS age
		UNION ALL
		SELECT
		  'yun' AS name,
		  ['Python', 'SQL'] AS preferred_language, 
		  28 AS age
		)
		
	SELECT 
	  name, preferred_language, age
	FROM example_data
	```
	
	- 결과는 다음과 같다
		- <img src="https://www.dropbox.com/s/ysa4erzbmbyhane/Screenshot%202020-04-12%2017.22.56.png?raw=1" width="300" height="400">
	
- 만약, Julia 언어를 선호하는 사람을 추출하고 싶다면 어떻게 해야할까?
	- WHERE preferred\_language = 'Julia'을 사용하면 아래와 같은 오류가 발생함
		- <img src="https://www.dropbox.com/s/atskki5jn7q6l0a/Screenshot%202020-04-12%2016.43.09.png?raw=1">
		- No matching signature for operator = for argument types: ARRAY<STRING>, STRING. Supported signatures: ANY = ANY at [21:7]
		- 이 오류는 preferred\_language는 ARRAY<STRING>인데, Julia(STRING)랑 비교하려니 타입이 맞지않아 생기는 오류임
	- 이런 상황에 **배열을 평면화(Flatten)해서 배열에 있는 값을 펴줘야 함**
	- 배열을 펴줄 때 사용하는 것은 UNNEST로, Nest한 데이터를 UNNEST하게 만드는 것
	- UNNEST 연산자는 ARRAY를 입력으로 받고 ARRAY의 각 요소에 대한 행이 한 개씩 포함된 테이블을 return함
	- Nested는 [중첩 및 반복 열 지정 Document](https://cloud.google.com/bigquery/docs/nested-repeated?hl=ko)에 더 자세한 설명을 볼 수 있음
- UNNEST() 사용하는 방법
	- UNNEST한 결과와 Table을 CROSS JOIN함
	
	```
	SELECT
		alias_name.value
	FROM Table_A CROSS JOIN UNNEST(ARRAY 데이터) as alias_name
	```
	
	- CROSS JOIN을 명시하지 않고 쉼표를 사용해도 동일함

	```
	SELECT
		alias_name.value
	FROM Table_A, UNNEST(ARRAY 데이터) as alias_name
	```
	
- 더 직관적인 이해를 위해 [How to use the UNNEST function in BigQuery to analyze event parameters in Analytics](https://medium.com/firebase-developers/using-the-unnest-function-in-bigquery-to-analyze-event-parameters-in-analytics-fb828f890b42) 글에 나온 예시를 참고하자
	- 아래와 같은 데이터가 있다
		- <img src="https://miro.medium.com/max/1400/1*Wx8OSBUETOVmCO7zn8Hmrw.png">
	- crew = "Zoe"를 추출하고 싶어 UNNEST를 사용
	
	```
	SELECT * FROM `spaceships` CROSS JOIN UNNEST(crew) as crew_member
	```
	
	- 이 과정을 시각화하면 아래와 같음
		- <img src="https://miro.medium.com/max/1200/1*ymQwpVJkJmrD_uhZODyiKw.gif">
	- 이제 WHERE 조건에 crew_member = "Zoe"를 사용할 경우 어떤 흐름으로 되는지 보자
		- <img src="https://miro.medium.com/max/1200/1*Ow1cPp2X5djsp2OtOizEhg.gif">
	- UNNEST를 사용하면 각 행에 배열의 값을 뿌려준다(=평면화, 펴준다)

- 다시 돌아와서, Julia를 선호하는 사람을 추출하는 사람을 찾기 위해 UNNEST를 사용해보자(필터링은 아직 하지말고)

	```
	WITH example_data AS(
		SELECT 
		  'kyle' AS name,
		  ['Python', 'SQL', 'R', 'Julia', 'Go'] AS preferred_language, 
		  31 AS age
		UNION ALL
		SELECT 
		  'max' AS name,
		  ['Python', 'SQL', 'Scala', 'Java', 'Kotlin'] AS preferred_language, 
		  29 AS age
		UNION ALL
		SELECT
		  'yun' AS name,
		  ['Python', 'SQL'] AS preferred_language, 
		  28 AS age
		)
		
	SELECT 
	  name, prefer_lang, age
	FROM example_data, UNNEST(example_data.preferred_language) as prefer_lang
	```	
	
	- FROM 절을 보면 FROM example_data, UNNEST(example\_data.preferred\_language) as prefer\_lang로 명시함
		- 즉, example\_data의 preferred\_language ARRAY를 UNNEST로 펴주고, prefer\_lang으로 alias함
	- 결과는 다음과 같다
		- <img src="https://www.dropbox.com/s/ivvme4gu14jtemn/Screenshot%202020-04-12%2017.25.00.png?raw=1" width="200" height="300">
	- UNNEST 전에 그냥 ARRAY 접근했던 경우와 비교하자
		- 이제 UNNEST 전 후에 어떻게 되는지 주의깊게 보자. 행이 왼쪽은 3, 오른쪽은 12!
		- <img src="https://www.dropbox.com/s/rmoeri44o5ylkyw/Screenshot%202020-04-12%2017.27.png?raw=1">
		
- UNNEST 하고 prefer\_lang = 'Julia' 조건을 준 쿼리는 아래와 같음

	```
	WITH example_data AS(
		SELECT 
		  'kyle' AS name,
		  ['Python', 'SQL', 'R', 'Julia', 'Go'] AS preferred_language, 
		  31 AS age
		UNION ALL
		SELECT 
		  'max' AS name,
		  ['Python', 'SQL', 'Scala', 'Java', 'Kotlin'] AS preferred_language, 
		  29 AS age
		UNION ALL
		SELECT
		  'yun' AS name,
		  ['Python', 'SQL'] AS preferred_language, 
		  28 AS age
		)
		
	SELECT 
	  name, prefer_lang, age
	FROM example_data, UNNEST(example_data.preferred_language) as prefer_lang
	WHERE prefer_lang = 'Julia'
	```	
	
### 응용	
- ARRAY에 특정 값이 있는지 확인하고 싶은 경우
	- WHERE 절에서 UNNEST로 풀고, IN 사용
	- Scala를 선호하는 사람을 찾고 싶은 경우

	```
	WITH example_data AS(
			SELECT 
			  'kyle' AS name,
			  ['Python', 'SQL', 'R', 'Julia', 'Go'] AS preferred_language, 
			  31 AS age
			UNION ALL
			SELECT 
			  'max' AS name,
			  ['Python', 'SQL', 'Scala', 'Java', 'Kotlin'] AS preferred_language, 
			  29 AS age
			UNION ALL
			SELECT
			  'yun' AS name,
			  ['Python', 'SQL'] AS preferred_language, 
			  28 AS age
			)
			
	SELECT 
	  name, preferred_language, age
	FROM example_data
	WHERE 'Scala' IN UNNEST(example_data.preferred_language)	
	```
	
	- 맨 아래에 WHERE 'Scala' IN UNNEST(example_data.preferred_language) 이렇게 필터링함
	- <img src="https://www.dropbox.com/s/3ggbx4cx5rvj1gj/Screenshot%202020-04-12%2017.46.32.png?raw=1">

- ARRAY에 특정 조건이 일치하는 값을 검색하고 싶은 경우
	- EXISTS 사용	
		- IN 조건이 여러개 있는 경우에도 사용 가능
		- Go나 Scala를 선호하는 사람을 찾고싶은 경우

	
	```
	WITH example_data AS(
	    SELECT 
	      'kyle' AS name,
	      ['Python', 'SQL', 'R', 'Julia', 'Go'] AS preferred_language, 
	      31 AS age
	    UNION ALL
	    SELECT 
	      'max' AS name,
	      ['Python', 'SQL', 'Scala', 'Java', 'Kotlin'] AS preferred_language, 
	      29 AS age
	    UNION ALL
	    SELECT
	      'yun' AS name,
	      ['Python', 'SQL'] AS preferred_language, 
	      28 AS age
	    )
	
	SELECT 
	  name, preferred_language, age
	FROM example_data
	WHERE EXISTS 
	  (
	    SELECT * 
	    FROM UNNEST(example_data.preferred_language) as prefer_lang
	    WHERE prefer_lang IN ('Go','Scala')
	  )
	```
	
	- 아래에 EXISTS ~ 부분 참고
	- <img src="https://www.dropbox.com/s/hc2sp4aba4hw2sk/Screenshot%202020-04-12%2017.46.05.png?raw=1">
	
- Firebase Analytics에 저장된 데이터 추출하기
	- [Firebase의 BigQuery Export
 Document](https://support.google.com/analytics/answer/7030014?hl=ko) 
	- BigQuery에서 Firebase 데이터 샘플을 제공함. [https://bigquery.cloud.google.com/dataset/firebase-public-project:analytics](https://bigquery.cloud.google.com/dataset/firebase-public-project:analytics)
	- 스키마는 다음과 같음
		- <img src="https://www.dropbox.com/s/dq2exjp5ypajheh/Screenshot%202020-04-12%2017.55.png?raw=1">
	- 데이터를 미리보면 다음과 같음
		- <img src="https://www.dropbox.com/s/2np0ueeg666ig38/Screenshot%202020-04-12%2017.55.43.png?raw=1">
	- 우리가 아까 봤던 ARRAY보다 조금 더 복잡함
		- event\_params는 유저가 행동한 이벤트가 저장되고, user\_properties는 유저의 고유 정보가 저장됨
		- 두 RECORD 모두 형태는 비슷하지만, 저장된 파라미터가 다르기 때문에 2개의 UDF를 만들어서 사용함
			- 회사라면 Persistent UDF 만들어서 활용하면 됨
		- UDF에 대해 궁금한 경우 [BigQuery UDF 사용하기](https://zzsza.github.io/gcp/2019/09/20/bigquery-udf/) 참고

	```
	# UDF for event parameters
	CREATE TEMP FUNCTION paramValueByKey(k STRING, params ARRAY<STRUCT<key STRING, value STRUCT<string_value STRING, int_value INT64, float_value FLOAT64, double_value FLOAT64 >>>) AS (
	  (SELECT x.value FROM UNNEST(params) x WHERE x.key=k)
	);
	
	# UDF for user properties
	CREATE TEMP FUNCTION propertyValueByKey(k STRING, properties ARRAY<STRUCT<key STRING, value STRUCT<string_value STRING, int_value INT64, float_value FLOAT64, double_value FLOAT64, set_timestamp_micros INT64 >>>) AS (
	  (SELECT x.value FROM UNNEST(properties) x WHERE x.key=k)
	);
	
	SELECT 
	  user_pseudo_id,
	  event_name,
	  event_timestamp,
	  propertyValueByKey('num_levels_available', user_properties).string_value AS num_levels_available,
	  paramValueByKey('board', event_params).string_value AS board,
	  paramValueByKey('firebase_screen_class', event_params).string_value as firebase_screen_class
	from `firebase-public-project.analytics_153293282.events_20181003` 
	LIMIT 10
	``` 
	
	- 아래의 결과가 나타남
		- screen\_view event_name에 board가 null인 이유는 screen\_view 이벤트엔 board 파라미터가 없기 때문! 
	- <img src="https://www.dropbox.com/s/80ut0fgjxqymhif/Screenshot%202020-04-12%2017.58.33.png?raw=1">

<br />

---

<br />

### 정리
- BigQuery ARRAY : 데이터 유형이 동일한 값으로 구성된 목록
- BigQuery STRUCT : 각각 유형(필수)과 필드 이름(선택사항)이 있는 정렬된 필드의 컨테이너
- ARRAY을 평면화(Flatten)하고 싶은 경우 UNNEST 사용
	- UNNEST 전/후를 보면 이해하기 수월
	- 과정이 이해가 안되면 GIF 참고!
- Firebase Analytics에 저장되는 데이터는 UDF를 만들어서 활용!

<br />

---

<br />

### Reference
- [How to use the UNNEST function in BigQuery to analyze event parameters in Analytics](https://medium.com/firebase-developers/using-the-unnest-function-in-bigquery-to-analyze-event-parameters-in-analytics-fb828f890b42)
- [BigQuery Tip: The UNNEST Function](https://firebase.googleblog.com/2017/03/bigquery-tip-unnest-function.html)
- [BigQuery 중첩 및 반복 열 지정](https://cloud.google.com/bigquery/docs/nested-repeated)
- [BigQuery 배열 다루기](https://cloud.google.com/bigquery/docs/reference/standard-sql/arrays)
- [Google BigQuery 쉽게 쓰게 하기 (feat. ARRAY)](https://cojette.github.io/bigqueryandarray/) : 갓꼬젯님의 글..!