---
layout: post
title:  "pandas-gbq에서 인증(Authentication) 설정하기"
subtitle:  "pandas-gbq에서 인증(Authentication) 설정하기"
categories: gcp
tags: basic
comments: true
---

- pandas-gbq에서 인증(authentication) 하는 방법에 대해 정리한 글입니다
	- pandas-gbq는 pandas에서 Google BigQuery의 데이터를 쉽게 조회할 수 있는 파이썬 라이브러리입니다

---

## Authentication
- 1) Service Account Key 사용한 인증
- 2) User Account를 통한 인증

---

### Service Account Key
- Service Account Key는 [Creation Page](https://console.cloud.google.com/apis/credentials/serviceaccountkey)에서 쉽게 만들 수 있음
	- [공식 문서](https://cloud.google.com/docs/authentication/getting-started) 
- JSON key type을 선택해 다운로드
- 장점
	- 간편하게 사용 가능
	- Remote 서버에서 사용할 경우 매우 유용
		- Remote 서버를 위한 Key를 발급한 후, IAM을 특정 권한만 설정
- 단점
	- 처음엔 사용하기 쉬우나 회사에서 이 key를 통해 많은 사람들이 쿼리를 날린다면, 보안의 문제가 있을 수 있음
	- JSON key를 잘 관리할 수 있는지?가 핵심 이슈
		- 이 이슈를 위해 2) User Account를 사용
- 사용 예시
	- private key 파라미터를 사용할 경우
		- deprecated 예정
		 
	```
	import pandas as pd
	
	query = "SELECT * FROM `dataset.table`"
	pd.read_gbq(query=query, project_id="project_id", private_key="path/to/key.json", dialect='standard')
	```	
	
	- credentials 파라미터를 사용할 경우
		- pandas 0.24.0, pandas-gbq 0.8.0 버전부터 사용 가능
	
	```
	from google.oauth2 import service_account
	import pandas as pd
	
	credentials = service_account.Credentials.from_service_account_file('path/to/key.json')

	query = "SELECT * FROM `dataset.table`"
	df = pd.read_gbq(query=query, project_id="project_id", credentials=credentials, dialect='standard')
	```     

### User Account
- 이 방식은 Colab에서 Google drive 권한 획득하는 것처럼 웹을 띄운 후, G Suite 또는 Gmail 로그인으로 인증하는 방식
	- `pydata_google_auth` 라이브러리 사용
- 장점
	- Key가 아닌 User Account로 인증하기 때문에 GCP의 IAM으로 관리할 수 있음
- 단점
	- 캐시된 인증이 끝난 후, 다시 작업해야 하는 점
- 사용 예시
	
	```
	import pandas as pd
	import pydata_google_auth

	SCOPES = [
	    'https://www.googleapis.com/auth/cloud-platform',
	    'https://www.googleapis.com/auth/drive',
	    'https://www.googleapis.com/auth/bigquery'
	]
	
	credentials = pydata_google_auth.get_user_credentials(
    	SCOPES, auth_local_webserver=True)
	
	query = "SELECT * FROM `dataset.table`"
	df = pd.read_gbq(query=query, project_id="project_id", credentials=credentials, dialect='standard')
	```
	 	
- 혹은 pd.read_gbq() 인자로 `reauth=True`, `auth_local_webserver=True` 를 넣어도 가능함

	
### 결론
- 어떤 방식이 좋은지는 "상황"에 따라 다름
- 각 방식의 차이점이 무엇인지 인지하고, 적절하게 사용하는 것이 핵심!
- 공식 google-cloud-bigquery에서 인증하는 방법이 궁금하면 [공식 문서](https://cloud.google.com/bigquery/docs/reference/libraries) 참고  	



### Reference
- [pandas_gbq](https://pandas-gbq.readthedocs.io/en/latest/howto/authentication.html)
