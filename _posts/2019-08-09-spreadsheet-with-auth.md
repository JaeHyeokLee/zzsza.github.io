---
layout: post
title:  "Python에서 Auth를 사용해 Google Spreadsheets 연동하기"
subtitle: "Auth를 사용해 Google Spreadsheets 연동하기"
categories: development
tags: python
comments: true
---

- Python에서 Google Spreadsheets 연동하는 방법을 작성한 글입니다
- 구유림님의 [gspread를 이용하여 Python에서 구글 시트 연동하기
](https://yurimkoo.github.io/python/2019/07/20/link-with-googlesheets-for-Python.html)에 gspread를 사용하는 방법에 대해 자세히 작성되어 있습니다 :)
	- 이번 글은 Service Account Key 없이 Web Auth를 이용해 구글 시트를 연동하는 방법을 다룹니다

	
---

### Google Sheets	
- 사용할 라이브러리
	- gspread
	- pydata_google_auth
- pydata_google_auth
	- [Github](https://pypi.org/project/pydata-google-auth/)
	- 이 라이브러리는 Google API 인증을 위한 헬퍼 패키지
	- BigQuery를 사용할 경우에도 사용함 : [[pandas-gbq에서 인증(Authentication) 설정하기](https://zzsza.github.io/gcp/2019/03/17/pandas-gbq-auth/)] 참고

- 원리
	- 원리는 생각보다 간단
	- pydata_google_auth를 통해 credentials를 생성
	- 거기에 존재하는 access_token을 통해 gspread에 인증
	- 단, pydata의 access_token은 `token`에 저장되고 gspread는 `access_token`이 필요함
	- 이 부분을 덮어쓰는 코드 추가

### 코드
```
import pydata_google_auth
import gspread

SCOPES = [
  'https://www.googleapis.com/auth/drive',
  'https://www.googleapis.com/auth/spreadsheets'
]

credentials = pydata_google_auth.get_user_credentials(SCOPES, auth_local_webserver=True)
credentials.access_token = credentials.token

gc = gspread.authorize(credentials)

gc.list_spreadsheet_files()
```	

### 참고
- 한번만 인증하면 그 다음은 웹이 뜨지 않아서 왜 그런가? 하고 소스를 뜯어봄
- (Mac 기준) `~/.config/pydata/pydata_google_credentials.json`에 전에 사용한 credentials를 저장하기 때문
	- [공식 문서](https://pydata-google-auth.readthedocs.io/en/latest/api.html#pydata_google_auth.default)의 credentials_cache를 참고하면 저장함
- 만약 다른 계정으로 로그인하고 싶으면 `$HOME/.config/pydata/pydata_google_credentials.json`(Mac) 또는 `$APPDATA/.config/pydata/pydata_google_credentials.json`(윈도우)를 삭제


---


## Reference
- [pydata-google-auth 공식 문서](https://pydata-google-auth.readthedocs.io/en/latest/index.html)
- [gspread를 이용하여 Python에서 구글 시트 연동하기
](https://yurimkoo.github.io/python/2019/07/20/link-with-googlesheets-for-Python.html)
- [pandas-gbq에서 인증(Authentication) 설정하기
](https://zzsza.github.io/gcp/2019/03/17/pandas-gbq-auth/)