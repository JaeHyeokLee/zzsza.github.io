---
layout: post
title:  "Google Cloud Functions과 Cloud Scheduler를 사용해 Python 스크립트 주기적으로 실행하기"
subtitle:  "Google Cloud Functions과 Cloud Scheduler를 사용해 Python 스크립트 주기적으로 실행하기"
categories: gcp
tags: cloud-functions
comments: true
---

- Google Cloud Functions과 Cloud Scheduler를 사용해 Python 스크립트를 주기적으로 실행하는 방법에 대해 설명한 글입니다
- [Google Cloud SDK](https://cloud.google.com/sdk/install?authuser=2&hl=ko)를 설치한 후 아래 글을 읽어주세요

---


### 사용할 도구
- Cloud Functions으로 함수를 클라우드에 올리고, 그 함수를 Cloud Scheduler로 주기적으로 돌릴 예정
- [Google Cloud Functions](https://cloud.google.com/functions/)
	- 클라우드에서 코드를 간단히 실행할 수 있음
	- AWS의 Lambda와 유사함
	- 과거엔 자바스크립트(Node.js 런타임)만 있었으나, 최근에 Go(1.11), Python(3.7)도 추가됨 : 2019년 7월 6일 기준
	- [HTTP 함수](https://cloud.google.com/functions/docs/writing/http?hl=ko)와 [백그라운드 함수](https://cloud.google.com/functions/docs/writing/background?hl=ko)로 나뉨
		- 전자는 HTTP를 통해 함수를 직접 호출
		- 후자는 Google Cloud Pub/Sub의 메세지 또는 Google Cloud Storage 버킷의 변경 사항을 통해 Cloud Functions를 간접적으로 호출 
	- 가격은 호출 횟수, 코드가 실행되는 컴퓨팅 리소스 사용 시간, 네트워크 사용량에 따라 결정되며 무료 사용량은 월 200만회까지 무료, 리소스 사용은 메모리는 400,000GB-seconds, 컴퓨팅 시간은 200,000 GHz까지 무료이며 자세한 내용은 [Pricing 문서](https://cloud.google.com/functions/pricing) 참고  
	- Reference 코드를 보고싶은 경우엔 [python-docs-samples](https://github.com/GoogleCloudPlatform/python-docs-samples/tree/master/functions) 참고
	- 용도
		- <img src="https://www.dropbox.com/s/xbka8brnlf1w23h/Screenshot%202019-07-06%2000.51.11.png?raw=1">
- [Cloud Scheduler](https://cloud.google.com/scheduler/)
	- App Engine 트리거, Pub/Sub을 통해 메세지 보내기, Compute Engine에 HTTP 엔드포인트 호출 등 다양하게 가능
	- crontab보다 쉽게 사용 가능
	- AWS의 클라우드 워치와 유사 
	- 월 3개의 Job까지 무료(횟수가 아니고 Job의 개수에 비용 부과 : Job이 20번 돌아도 상관 없음)
	- 단, 기본적으로 1분에 최대 60개의 Cron이 가능하고 하루 최대 만건의 CronJob 실행이 가능한데 더 필요할 경우 Quota Request를 해두면 됨

---

### 작업 흐름
- Cloud Functions 포맷에 맞게 Python으로 함수를 만든다
- Cloud Functions에 배포한다
- Cloud Scheduler에서 주기적으로 Cloud Functions의 함수를 실행시킨다

---

### Hello World
- 우선 아무것도 모르는 상태에서 Hello World를 찍어보며 진행하겠습니다
- main.py 작성
	
	```
	def hello_world(request):
		return 'Hello, World!\n'
	```

- 배포
	
	```
	gcloud beta functions deploy hello_world --runtime python37 --trigger-http
	```

- 배포시 아래 메세지가 나타남
	- <img src="https://www.dropbox.com/s/edzm4hod0fbds1b/Screenshot%202019-07-06%2001.24.03.png?raw=1">
- [Cloud Functions Console](https://console.cloud.google.com/functions/)에서 확인해보면 아래와 같이 배포된 것을 알 수 있음
	- <img src="https://www.dropbox.com/s/moxbvlof7uhnklz/Screenshot%202019-07-06%2001.27.28.png?raw=1">
- 최근 배포 시간 우측의 세로로 3개의 점을 클릭한 후, 함수 테스트 클릭하면 아래와 같이 나옴
	- 함수 테스트 버튼을 통해 테스트 가능
	- 위에서 배포를 한번 더 진행해서 버전: 2로 나옴
	- <img src="https://www.dropbox.com/s/9s32qljoszgfhpy/Screenshot%202019-07-06%2001.32.34.png?raw=1"> 

---	

### Python으로 Cloud Functions 만들기
- 간단하게 로컬에서 파이썬 스크립트를 만든 후, gcloud functions를 사용해서 올리면 끝!
- Python에서 함수를 만들 경우엔 아래와 같은 구조로 생성
	
	```
	folder/
	├── main.py : main.py 아래에 있는 함수를 Cloud Functions에서 사용 가능
	└── requirements.txt : 설치할 패키지
	```

- 만약 개인이 만든 패키지도 같이 올리고 싶은 경우
	
	```
	folder/
	├── main.py
	├── requirements.txt 	
	└── mypackage/
	    ├── __init__.py
	    └── myscript.py
	```

- Cloud Functions의 Python 3 Runtime에 설치된 시스템 패키지는 [링크](https://cloud.google.com/functions/docs/reference/python-system-packages?hl=ko)를 참고하면 알 수 있음 : git, wget 등이 설치되어 있음

---

### Cloud Functions 배포하기
- 배포는 다양한 방식이 존재하는데 크게 아래와 같이 나눌 수 있음
	- 1) 로컬에서 파일을 업로드한 후, 배포하는 경우
	- 2) Github나 Bitbucket 같은 소스 저장소의 코드를 배포하는 경우
	- 두 방법 모두 gcloud 도구를 사용할 예정이며, 콘솔에서 진행하고 싶으면 [Console 문서](https://cloud.google.com/functions/docs/deploying/console?hl=ko) 참고
- 여기선 1) 위주로 진행
- gcloud 명령어 구조
	
	```
	gcloud beta functions deploy <NAME> <TRIGGER> --stage-bucket <STORAGE_BUCKET> --entry-point <FUNCTION_NAME>
	```

	- <NAME> : Cloud Functions의 이름으로 문자, 숫자, 밑줄, 하이프만 포함할 수 있음. entry-point 옵션을 지정하지 않는한 모듈에서 같은 이름의 함수를 내보내야 함
	- <TRIGGER> : 트리거의 종류로, 자세한 내용은 [링크](https://cloud.google.com/functions/docs/calling/?hl=ko) 참고. `--trigger-http`, `--trigger-topic`, `--trigger-bucket`, `--trigger-event`, `--trigger-resource` 등이 있음
	- `--stage-bucket`나 `--entry-point`는 선택적 인자! (없어도 상관 없음)
		- --stage-bucket : 함수의 코드를 저장하는 Google Storage 버킷. 이 인자를 생략할 경우 별도의 storage에 저장해서 활용
		- --entry-point : 배포시 사용하는 <NAME>과 다른 이름을 가지는 경우 사용
	- 그 외에도 `--allow-unauthenticated` 등의 인자도 있음
	- 더 자세한 명령어는 [gcloud beta functions deploy 링크](https://cloud.google.com/sdk/gcloud/reference/beta/functions/deploy?hl=ko) 참고
- 예시
	- 위에서 처음 진행할 때 사용한 코드
	
	```
	gcloud beta functions deploy helloworld --trigger-http
	```
	
	- entry point를 지정한 경우 
		- Cloud Functions의 이름은 hello고, 함수 사용시엔 helloworld라는 이름으로 사용함
	```
	gcloud beta functions deploy hello --entry-point helloworld --trigger-http
	```

---

### Cloud Scheduler로 주기적으로 실행하기
- 콘솔에서 [Cloud Scheduler](https://console.cloud.google.com/cloudscheduler?project=geultto)로 이동
- 작업 만들기 클릭
	- 지역 설정. Cloud Functions에서 만든 Region과 동일하게 설정
		- <img src="https://www.dropbox.com/s/6r1xu8dybcvdocy/Screenshot%202019-07-06%2011.56.06.png?raw=1"> 
	- 작업 만들기
		- <img src="https://www.dropbox.com/s/r4pwhx154havkm7/Screenshot%202019-07-06%2012.00.03.png?raw=1">
		- 빈도는 crontab 형식으로 작성
		- 시간대는 기준 시간대를 설정. 대한민국도 존재
		- 대상에 HTTP를 설정 
		- Cloud Functions에서 나온 URL을 입력
			- <img src="https://www.dropbox.com/s/tki7pepqbvtvaez/Screenshot%202019-07-06%2012.01.02.png?raw=1"> 
			- 예시 : https://us-central1-{project}.cloudfunctions.net/{entry_point}
		- Body가 필요하면 작성한 후, 만들기  
- 생성한 후, Cloud Scheduler 화면을 보면 아래와 같이 스케쥴 Job들이 나타남
	- <img src="https://www.dropbox.com/s/gy7v6bbdsvqvxfn/Screenshot%202019-07-06%2012.04.20.png?raw=1"> 
- gcloud로도 가능한데, [gcloud beta scheduler jobs create http](https://cloud.google.com/sdk/gcloud/reference/beta/scheduler/jobs/create/http)를 참고하면 볼 수 있음
	
	```
	gcloud beta scheduler jobs create http
	``` 

---


### 앞으로 고민할 내용들
- 앞에서 진행한 Cloud Functions는 앞에 인증하는 과정이 없기 때문에 아무나 request할 수 있음. 이 점은 실제 서비스엔 치명적이기 때문에 보안을 신경써야 함
	- AWS에선 GATEWAY를 사용해 API_KEY 값을 같이 물고 들어가는 방식으로 사용했는데, 아직 Cloud Functions는 그런 작업이 잘 발달하지 않고, 레퍼런스도 적은 느낌(제가 잘못 알고있다면 제보 부탁드립니다!)
	- Firebase를 사용하는 예시가 있긴한데, 굳이? 라는 생각이 들고, Cloud Endpoints 를 사용한다는 말도 있는데, Python 예제는 거의 없음
	- [Wrapping Google Cloud Functions HTTP Triggers in Endpoints](https://medium.com/google-cloud/wrapping-google-cloud-functions-http-triggers-in-endpoints-666e141e5e3b)
- CloudSQL를 어떻게 연결할 수 있는지?
	- 우선 [공식 문서](https://cloud.google.com/functions/docs/sql?hl=ko)에 나와있긴 함 
- Terraform을 사용해 인프라를 코드로 관리


### Reference
- [Python-docs-samples Github](https://github.com/GoogleCloudPlatform/python-docs-samples/tree/master/functions)
- [Cloud Functions 공식 Document](https://cloud.google.com/functions/docs/)
- [Serverless Architecture with Cloud Function](https://www.slideshare.net/calmlake79/serverless-architecture-with-cloud-function)
- [Cloud 스케줄러로 Compute 인스턴스 예약하기](https://cloud.google.com/scheduler/docs/start-and-stop-compute-engine-instances-on-a-schedule)
- [Google Cloud Functions Tutorial : Using the Cloud Scheduler to trigger your functions](https://rominirani.com/google-cloud-functions-tutorial-using-the-cloud-scheduler-to-trigger-your-functions-756160a95c43)
