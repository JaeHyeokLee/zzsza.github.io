---
layout: post
title:  "Google Spreadsheets(Sheets)에서 BigQuery 사용하기"
subtitle: "스프레드시트에서 BigQuery 사용하기"
categories: gcp
tags: BigQuery
comments: true
---

- 스프레드시트에서 BigQuery를 사용해 데이터를 추출하는 방법에 대해 작성한 글입니다
	- OWOX를 사용하는 방법
	- Data Connectors를 사용하는 방법 


---


### BigQuery
- BigQuery는 Google Cloud Platform의 Data Warehouse 제품
- 관련된 내용은 [BigQuery의 모든 것](https://www.slideshare.net/zzsza/bigquery-147073606) 발표 자료에 많이 나와있음


---

### Spreadsheet
- 스프레드시트는 구글의 제품으로 우리가 친숙한 엑셀과 비슷한 구조를 가지고 있음
- 스프레드시트에서 간단한 시각화, 피벗 테이블 등 데이터를 가공할 수 있음

---

### BigQuery + Spreadsheet
- 매일 특정 쿼리를 날려 데이터를 추출하고 싶을 경우 BigQuery의 스케쥴 쿼리를 사용하거나, Python를 사용해 데이터를 추출할 수 있음
	- 개발에 친화적인 사람이라면 위 방법도 편리하지만, 개발에 친화적이지 않은 사람들은 스프레드시트를 사용하는 방법이 더 친숙
- 2가지 방법
	- OWOX를 사용하는 방법 (부가기능, Add-ons)
	- Data Connectors를 사용하는 방법
		- G Suite Business 고객만 사용할 수 있음
		- 결과를 10,000행까지 받을 수 있음


---

### OWOX 사용 방법
- 1) 스프레드시트에서 부가기능(Add-ons) 추가
	- [sheet.new](sheet.new)로 새로운 스프레드시트로 접속한 후, 상단의 부가기능을 클릭
	- 부가기능 검색창에 `OWOX`를 검색
	- <img width="1015" alt="스크린샷 2019-07-20 13 52 39" src="https://user-images.githubusercontent.com/18207755/61578383-15dc0a00-ab31-11e9-8c99-a152de0f8baa.png">
	- OWOX BI BigQuery Reports의 오른쪽에 있는 추가 버튼 클릭(+ 무료)
	- <img width="1010" alt="스크린샷 2019-07-20 13 53 15" src="https://user-images.githubusercontent.com/18207755/61578387-23918f80-ab31-11e9-9f5c-b5c2fe1bba66.png">
	- Google 로그인 후, OWOX BI BigQuery Reports이(가) 내 Google 계정에 액세스하려고 합니다 라는 부분에서 허용을 클릭
		- 만약 BigQuery API가 활성화되어 있지 않다면 확인창이 추가로 뜨는데, 확인 누르면 됨

- 2) 부가기능 활성화
	- 스프레드시트에서 부가기능 - 부가기능 관리 클릭
	- 부가기능 - OWOX BI BigQuery Reports이 보인는지 확인
	- <img width="854" alt="스크린샷 2019-07-20 13 55 18" src="https://user-images.githubusercontent.com/18207755/61578394-49b72f80-ab31-11e9-8fc6-bf8273e8f5ab.png">

- 3) 쿼리 날리기(=데이터 추출하기)
	- 부가기능 - OWOX BI BigQuery Reports - Add a report 클릭 
	- <img width="647" alt="스크린샷 2019-07-20 20 06 17" src="https://user-images.githubusercontent.com/18207755/61578399-52a80100-ab31-11e9-912c-7a1eb28be497.png">
	- create 클릭
	- <img width="848" alt="스크린샷 2019-07-20 20 08 11" src="https://user-images.githubusercontent.com/18207755/61578400-53d92e00-ab31-11e9-829d-ab9534b9838e.png">
	- 이 부분에 쿼리를 작성! 단, 쿼리를 작성할 때 #stadardSQL인지 #legacySQL인지 명시해줘야 함
	- Save & Run 클릭
	- 결과가 스프레드시트의 새로운 시트로 저장됨
	- 쿼리의 결과를 Report라고 부름

- 4) 쿼리 수정하기
	- Select a query에 있는 쿼리를 선택한 후 Edit 클릭해 수정할 수 있음
	- Revision history를 확인하면 예전에 사용한 쿼리를 볼 수 있음

- 5) 파라미터 설정하기
	- [How to add parameters?](https://support.owox.com/hc/en-us/articles/217491007=no_parameters)를 클릭하면 파라미터를 동적으로 사용하는 방법에 대해 알려줌
	- Input 입력하는 방식, 날짜를 선택하는 방식, 리스트에서 선택하는 방식이 가능

- 6) 이미 작성된 레포트를 수정하고 싶은 경우
	- Edit current report 클릭 후 수정
	- <img width="945" alt="스크린샷 2019-07-20 21 20 29" src="https://user-images.githubusercontent.com/18207755/61578574-42ddec00-ab34-11e9-800b-a9639781834d.png">

- 7) 데이터를 최신화하고 싶은 경우
	- Refresh currernt report 클릭 (현재 레포트만 수정)
	- Refresh all reports 클릭 (모든 레포트 수정)

- 8) 쿼리를 주기적으로 돌리고 싶은 경우(스케쥴링)
	- Schedule reports 클릭
	- Enable reports to run automatically 클릭 
	- Schedule reports to run `every day` between `4 am - 5am` 에서 빈도와 시간대를 설정
		- 이 시간대는 스프레드시트의 시간대에 따라 달라지는데, 파일 - 스프레드시트 설정을 통해 현재 스프레드시트의 시간대를 확인할 수 있음
	- Execute all configured reports 클릭을 해제하면 여러 Report 중 어떤 것을 스케쥴링할지 선택할 수 있음
	- Receive email notification을 실패할 때 메일 받거나 매번 메일을 받는 옵션을 설정할 수 있음
	- I understand Google BigQuery query pricing policy를 체크해야 함
		- BigQuery는 쿼리를 날릴 때마다 비용이 부과되기 때문에 이 점을 꼭 유의해야함
	- <img width="694" alt="스크린샷 2019-07-20 20 44 37" src="https://user-images.githubusercontent.com/18207755/61578584-6acd4f80-ab34-11e9-8fbe-540977afa47b.png">


---


### Data Connector 사용 방법
- 이건 일반 계정에선 보이지 않고 G Suite Business를 사용하는 계정에서만 보임(19.7.20 기준이며, 추후에 변경될 수 있습니다)
- 1) 데이터 연결
	- 데이터 - 데이터 커넥터 클릭
	- <img width="734" alt="스크린샷 2019-07-20 20 46 45" src="https://user-images.githubusercontent.com/18207755/61578408-5dfb2c80-ab31-11e9-9691-864facacc5ff.png">

- 2) 쿼리 날리기(=데이터 추출하기)
	- 데이터 연결할 프로젝트 선택한 후, 쿼리 작성 클릭
	- <img width="871" alt="스크린샷 2019-07-20 20 49 21" src="https://user-images.githubusercontent.com/18207755/61578407-5dfb2c80-ab31-11e9-84e7-df1e7f1f2460.png">
	- 결과 미리보기를 진행한 후, 결과 삽입을 클릭하면 데이터가 추가됨

- 3) 쿼리 수정
	- 데이터 하단에 있는 새로고침이나 버튼을 클릭하면 수정할 수 있음
	- <img width="680" alt="스크린샷 2019-07-20 20 51 19" src="https://user-images.githubusercontent.com/18207755/61578409-5e93c300-ab31-11e9-9d04-9c274c1827e5.png">


---

### 뭘 사용할까?
- 취향의 차이가 존재할 수 있으나 아직(19.07.20 기준) OWOX가 조금 더 기능이 우세(스케쥴링 기능, 메일 전송)
- Data Connector는 추출할 Row 수가 아직 최대 10,000개까지만 가능한 점이 아쉬움(OWOX는 메모리 상황에 따라 다르지만 10,000개 이상도 가능) 
	- 또한 Data Connector는 G Suite를 사용해야 생기는 기능
- 따라서 보통의 경우엔 OWOX를 사용하는 것을 추천드립니다 :)


## Reference
- [Connecting BigQuery and Google Sheets to help with hefty data analysis](https://cloud.google.com/blog/products/g-suite/connecting-bigquery-and-google-sheets-to-help-with-hefty-data-analysis)
- [BigQuery Data Connector for Google Sheets: how to get more than 10,000 rows](https://blog.sheetgo.com/google-sheets-features/bigquery-data-connector-get-more-than-10000-rows/)
