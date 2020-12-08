---
layout: post
title:  "Google Cloud Build를 사용한 CI/CD"
subtitle:  "Google Cloud Build를 사용한 CI/CD"
categories: gcp
tags: basic
comments: true
---

- Google Cloud Platform의 Cloud Build 서비스를 사용하는 방법에 대해 정리한 글입니다


---

### CI/CD
- CI
    - Continuous Integration
    - 테스트와 빌드를 자동으로 진행하는 프로세스
- CD
    - Continuous Deploy, Continuous Delivery
    - 배포 자동화
- 전략에 따라 CI, CD를 하나의 도구에서 할 수 있고, CI 따로 CD 따로 설정할 수 있음
- 대표적인 도구
	- Jenkins
	- Travis CI
	- CircleCI
	- Github Action
	- Buddy works
	- Google Cloud Build
	- 이외에도 정말 다양한 도구들이 있음
	- 여기선 Google의 Cloud Build에 대해 설명함


<br />

---

<br />

### Cloud Build
- Cloud Build는 Google Cloud Platform의 인프라에서 빌드를 실행하는 서비스
- 다양한 저장소, 클라우드 스토리지 공간에서 소스 코드 가져오고, 사양에 맞게 선택
- 특징
	- Docker 지원
	- 매일 120분의 무료 빌드와 최대 10회 동시 빌드 지원
	- 빌드 과정 모니터링 제공
	- 컨테이너 이미지의 패키지 취약점 자동으로 파악
	- 로컬 또는 클라우드에서 빌드(로컬에서 테스트 가능)
- 다른 도구들과 비교는 사용할 상황에 따라 다르고, 비용이나 필요한 기능을 고려하면 좋음
	- Cloud Build는 Google Cloud Platform에서 사용하기에 간단하고, 가벼운 어플리케이션 구축시 사용하면 좋아서 선택함


### 비용
- 시기에 따라 달라질 수 있으니, [문서](https://cloud.google.com/cloud-build/pricing?hl=ko)를 참고하면 좋음
- (20년 5월 16일 기준) 매일 120분의 무료 빌드 시간이 제공 중(n1-standard-1 옵션)
- 빌드가 큐에 있는 동안엔 빌드 시간이 계산되지 않음
- 머신 유형(n1-standard-1)은 $0.003/빌드 시간(분)
- 머신 유형(n1-highcpu-8)은 $0.016/빌드 시간(분)
- 머신 유형(n1-highcpu-32)은 $0.064/빌드 시간(분)


<br />

---

<br />
	
### Build Cofig(빌드 구성) 단계
- Build Config 파일을 작성함
	- 이 파일에는 Unit Test, Docker 이미지 빌드, 패키징 등이 가능함
	- Docker, gradle, maven, bazel 같은 빌드 도구로 artifact 생성 가능
	- cloudbuild.yaml
- yaml 파일 또는 json으로 작성 가능
- 여러 Build Steps(빌드 단계를 묶어서 하나의 파일에 저장함
	- 각 Build Step은 Docker 컨테이너에서 실행됨
	- Build Step에서 사용하는 Builder 이미지는 Cloud Build에서 제공하는 이미지와 커뮤니티에서 제공한 이미지, 커스텀 이미지 등이 있음
		- 참고로 컨테이너 이미지를 Cloud builder라고 부름
	- Cloud Build 제공한 이미지 : [https://github.com/GoogleCloudPlatform/cloud-builders](https://github.com/GoogleCloudPlatform/cloud-builders)
		- bazel, curl, docker, gcloud, git, go, gsutil, javac, kubectl, mvn, npm, wget, yarn 등
	- 커뮤니티에서 제공한 이미지 : [https://github.com/GoogleCloudPlatform/cloud-builders-community](https://github.com/GoogleCloudPlatform/cloud-builders-community)
		- airflow, ansible, awscli, bq, composer, dataflow, docker-compose, helm, hugo, make 등

<br />

---

<br />

	
### Cloud Build 사용 흐름
- 1) Google Cloud Platform 프로젝트 생성 및 결제 확인
- 2) [Cloud Build API 사용 설정](https://console.cloud.google.com/flows/enableapi?apiid=cloudbuild.googleapis.com&redirect=https%3A%2F%2Fcloud.google.com%2Fcloud-build%2Fdocs%2Fquickstart-build&hl=ko&_ga=2.211218646.273633348.1588992492-151274966.1565535538)
- 3) 로컬에서 Cloud SDK 설치
- 4) CI/CD 구성 고민
- 5) cloudbuild.yaml 파일 작성
- 6) Staging Trigger 생성
	- 어떤 조건이 충족될 때 실행될지
	- Github와 연결
- 7) Github Push
- 8) Production Trigger 생성
- 1)과 3)은 이 글에선 생략하지만, 처음 접하실 경우엔 [프로젝트 만들기 및 관리](https://cloud.google.com/resource-manager/docs/creating-managing-projects?hl=ko), [Google Cloud SDK 설치](https://cloud.google.com/sdk/install?hl=ko)를 참고!

<br />

---

<br />

### 2) Cloud Build API 사용 설정
- [Cloud Build API 사용 설정](https://console.cloud.google.com/flows/enableapi?apiid=cloudbuild.googleapis.com&redirect=https%3A%2F%2Fcloud.google.com%2Fcloud-build%2Fdocs%2Fquickstart-build&hl=ko&_ga=2.211218646.273633348.1588992492-151274966.1565535538)을 클릭해 애플리케이션 등록
	- <img src="https://www.dropbox.com/s/j709ubykowreims/Screenshot%202020-05-09%2013.43.27.png?raw=1">

<br />

---

<br />
	
### Cloud Build UI 살펴보기
- [Cloud Build](https://console.cloud.google.com/cloud-build/builds)쪽에 접근하면 총 4개의 항목이 있음
	- 대시보드(Dashboard)
	- 기록(History)
	- 트리거(Triggers)
	- 설정(Settings)
- 대시보드
	- 트리거된 최근 빌드 결과가 표시됨
	- 빌드 트리거를 설정해야 보임
	- 최근 빌드가 얼마나 진행되었고, 평균 지속 시간, 빌드 성공/실패 비율 등을 보여줌
	- <img src="https://www.dropbox.com/s/b2538pyvro3jxeb/Screenshot%202020-05-09%2016.48.34.png?raw=1">
- 기록
	- 빌드 기록들을 모두 보여줌
	- 대시보드와 마찬가지로 빌드 트리거를 설정해야 보임
	- <img src="https://www.dropbox.com/s/iqgy8b84iz6seh4/Screenshot%202020-05-09%2016.48.54.png?raw=1">
- 트리거
	- 어떤 상황에 빌드가 실행되는지가 나타남
	- 수동으로 트리거 실행할 수 있음
	- <img src="https://www.dropbox.com/s/2oivs53vppq31gv/Screenshot%202020-05-09%2016.49.12.png?raw=1">
- 설정
	- 서비스 계정 권한에 대해 설정할 수 있음
	- <img src="https://www.dropbox.com/s/t0w9c7shl71m10h/Screenshot%202020-05-09%2016.49.31.png?raw=1">


<br />

---

<br />



### 4) CI/CD 구성 고민
- CI CD 구성할 때, Git Branch 전략을 먼저 파악해야 함
	- 어떤 브랜치로 Merge 또는 Pull Request일 때, 태그가 달렸을 때 등 어떤 상황에 배포하는지를 고민해야 함
	- 배포도 Rolling, Canary, BlueGreen 등의 전략을 사용함
	- 배포 관련해서 레이니스트의 [폐쇄망 환경의 배포 시스템 개발기](https://blog.banksalad.com/tech/how-we-have-built-alice/) 추천
- 이 글에선 CI는 하지 않고, 간단하게 Cloud Storage에 파일을 전송하면 CD가 되는 상황을 가정함
	- Cloud Composer에서 배포하는 방식과 유사함(2개의 Composer, Staging/Production이 있는 경우)
- 진행할 부분을 정리하면 다음과 같음
	- release branch로 Pull Request가 요청시 2가지 작업
		- ㄱ) echo "hello"
		- ㄴ) Staging Cloud Storage Bucket으로 파일이 전송
	- master branch로 Merge 후 수동으로 Production Cloud Storage 전송하기
		- Production도 자동화할 수 있지만, 수동으로 배포하는 방식도 있는 것을 보여주기 위해 이렇게 진행

<br />

---

<br />

### 5) cloudbuild.yaml 파일 작성
- Build Step 파일을 생성한다
- Cloud Build Trigger 설정시 기본적으로 cloudbuild.yaml을 받아서 실행함(파일명 수정 가능)
- 파일 예시

    ```bash
    steps:
    - name: 'gcr.io/cloud-builders/git'
      args: ['clone', 'https://github.com/GoogleCloudPlatform/cloud-builders']
      env: ['PROJECT_ROOT=hello']
    - name: 'gcr.io/cloud-builders/docker'
      args: ['build', '-t', 'gcr.io/my-project-id/myimage', '.']
    ```
    
- yaml 파일은 크게 아래와 같이 구성됨
	- steps: 클라우드 빌드가 수행해야 하는 작업들
		- name : 어떤 cloud builder를 사용할지 지정
		- args : cloud builder의 파라미터 지정, 띄어쓰기로 구분된다
			- name에서 나오는 것과 args이 연결된다고 보면 됨
			- 예 : name에 'gcr.io/cloud-builders/git'을 지정하고 args에 ['clone', 'https://github.com/GoogleCloudPlatform/cloud-builders']을 지정하면 git clone하는 작업이 진행됨
		- env : step이 실행될 때 사용할 환경 변수
		- dir : working directory 지정
		- timeout : 각 step의 실행 시간 제한
		- id : build step의 고유 식별자
		- waitFor : 먼저 실행해야 하는 step 지정, id 입력
		- entrypoint : 실행될 명령. Docker의 entrypoint와 유사함
		- secretEnv : Cloud KMS 암호화 키 사용해 암호화된 환경 변수. 자세한 설명은 [Using secrets and credentials](https://cloud.google.com/cloud-build/docs/securing-builds/use-encrypted-secrets-credentials) 참고
		- volumns : Docker 컨테이너 볼륨에 마운트
		- logsBucket : 로그 저장할 스토리지 지정
	- 더 자세한 내용은 [Build configuration overview](https://cloud.google.com/cloud-build/docs/build-config) 문서 참고

    
- VS Code나 IntelliJ를 사용하면, [Cloud Code for VS Code](https://cloud.google.com/code/docs/vscode/yaml-editing), [Cloud Code for IntelliJ](https://cloud.google.com/code/docs/intellij/yaml-editing) 참고하면 더욱 쉽게 생성할 수 있음
- cloudbuild.yaml 파일 생성
	- ubuntu에서 echo hello하고
	- gsutil을 사용해 github의 composer\/dags\/에 있는 파일들을 Google Cloud Storage로 보냄(COMPOSER BUCKET은 Trigger쪽에서 설정함)
	- init 후 airflow dag deploy to cloud storage를 실행함
	
	```
	steps:
	  - id: 'init'
	    name: 'ubuntu'
	    args: ['echo', 'hello']
	    waitFor: ['-']
	  - id: 'airflow dag deploy to cloud storage'
	    name: 'gcr.io/cloud-builders/gsutil'
	    args: ['-m', 'rsync', '-p', '-r', './composer/dags/', 'gs://${_COMPOSER_BUCKET}/dags']
	    waitFor: ['init']
	```

- 이제 cloudbuild.yaml을 github에 push
	- 참고로 [mlops-examples-on-gcp](https://github.com/zzsza/mlops-examples-on-gcp)란 repo에서 작업했음
	- composer/dags 폴더에 더미 파일도 넣어둠

<br />

---

<br />


### 6) Staging Trigger 생성
- Cloud Build에서 Github Repo와 연결
	- Cloud Build 트리거 - 저장소 연결 클릭
		- <img src="https://www.dropbox.com/s/onl7td8b4a4tan9/Screenshot%202020-05-09%2016.13.44.png?raw=1">
	- 소스에 Github 선택 - 인증 - 저장소에 Github 앱 설치(Google Cloud Build 설치 버튼 클릭)
	- 여기선 사용할 Repo만 지정(Only select repositories)하지만, 회사에서 사용시 모든 repositories를 선택할 수 있음
		- <img src="https://www.dropbox.com/s/7u96drcta48bovr/Screenshot%202020-05-09%2016.16.26.png?raw=1">
	- 저장소 선택
- Trigger 만들기
	- 푸시 트리거 만들기 클릭
		- <img src="https://www.dropbox.com/s/0zie77aauyle8rv/Screenshot%202020-05-09%2016.17.25.png?raw=1">
	- default-push-trigger-1이 만들어지는데, 클릭 후 수정
	- 이름 : Staging-Deploy
	- 이벤트 : pull 요청
	- 기본 분기 : release
	- 빌드 구성에 Cloud Build 구성 파일 확인 
	- 변수쪽에 `_COMPOSER_BUCKET`에 bucket 이름 설정. Cloud Storage Bucket을 만들어서 이름을 넣어준다
	- <img src="https://www.dropbox.com/s/w4rnrf9251mrpa8/Screenshot%202020-05-09%2016.38.21.png?raw=1">
- 이제 Cloud Build의 설정쪽에서 Compute Engine의 상태에 사용 중지됨을 사용 설정됨으로 수정
	- 이 설정을 해야 빌드시 service account를 사용함

<br />

---

<br />

### 7) Github Push
- `feature/cloud_build_test` 브랜치에서 파일을 수정하고 release branch로 pull request를 날림
	- [Pull Request](https://github.com/zzsza/mlops-examples-on-gcp/pull/2)
	- <img src="https://www.dropbox.com/s/e30zawa53oljxz4/Screenshot%202020-05-09%2016.28.55.png?raw=1">
	- 노란색 상태는 현재 Queue에 들어가서 작업을 하고 있는 상태
	- 시간이 지나서 모두 성공하면 초록색으로 표시됨
		- <img src="https://www.dropbox.com/s/5sv9il1rglolldz/Screenshot%202020-05-09%2016.32.08.png?raw=1">
	- Show all checks를 클릭하고 Details를 누르고 View more details on Google Cloud Build를 클릭하면 Cloud Build에서 세부 정보를 볼 수 있음
		- <img src="https://www.dropbox.com/s/4wuu4t94wu3by7k/Screenshot%202020-05-09%2016.32.50.png?raw=1">
- Cloud Build의 기록쪽으로 이동해 빌드 세부 정보를 볼 수 있음
	- <img src="https://www.dropbox.com/s/ok6nau442e0jsn2/Screenshot%202020-05-09%2016.34.49.png?raw=1">
- 만약 Cloud Build가 실패했다면, 이 창에서 에러 메세지를 볼 수 있음
- Cloud Storage Bucket에 가니 파일이 이동된 것을 확인할 수 있음
	- 이 mlops-examples는 Staging이라 생각하면 됨
	- <img src="https://www.dropbox.com/s/gpm2953tzaukujy/Screenshot%202020-05-09%2016.36.24.png?raw=1">

<br />

---

<br />


### 8) Production Trigger 생성
- 6)에서 만든 Trigger를 복제한 후, 환경 변수 `_COMPOSER_BUCKET`만 Production Bucket으로 지정
	- 그리고 자동 배포가 아닌, 수동 배포로 설정
- Cloud Build의 트리거로 이동해서 STaging-Deploy의 맨 우측 점 3개 아이콘 클릭 - 복제 클릭
	- <img src="https://www.dropbox.com/s/d8md2a5qg6c040e/Screenshot%202020-05-09%2016.42.22.png?raw=1">
- 이번엔 브랜치로 푸시로 설정하고, 브랜치에 master로 적는다
- 변수 쪽에 `_COMPOSER_BUCKET`에 mlops-examples-production으로 저장
- 현재 Trigger는 자동으로 실행되도록 설정됨
	- Production Deploy 우측 상태에 사용 설정됨을 사용 중지됨으로 변경
		- <img src="https://www.dropbox.com/s/6ml3b6iuaoqm3wm/Screenshot%202020-05-09%2016.44.26.png?raw=1">
- release => master 브랜치로 PR
- Cloud Build의 트리거에서 "트리거 실행" 버튼 클릭
	- <img src="https://www.dropbox.com/s/ejzwul0p72jmi4v/Screenshot%202020-05-09%2016.46.31.png?raw=1">
- 기록에 보면 정상적으로 작동된 것을 확인할 수 있음
- Google Cloud Storage의 mlops-examples-production 버켓에도 파일이 저장됨
	- <img src="https://www.dropbox.com/s/q8lpnqddujpqulx/Screenshot%202020-05-09%2016.47.50.png?raw=1">


<br />

---

<br />

### 추가 자료
- 사실 이 문서는 CI/CD 개념이 낯설고, Cloud Build에 대해 처음 접한 분들을 쉽게 설명하기 위해 CI 없이 정말 간단한 CD(사실 CD라 하기에도 민망한)를 설정함
- 더 심화되고 구체적인 자료를 궁금하신 분들을 위해 링크 추가함
- [공식 문서](https://cloud.google.com/cloud-build/docs/)
- Build
	- [컨테이너 이미지 빌드](https://cloud.google.com/cloud-build/docs/building/build-containers) : 목차에 보면 Node.js, 자바, Go, 빌드 아티팩트 저장이 나와있음
- Deploy
	- [GKE에 배포](https://cloud.google.com/cloud-build/docs/deploying-builds/deploy-gke), [Cloud Run에 배포](https://cloud.google.com/cloud-build/docs/deploying-builds/deploy-cloud-run)
	- 공식 문서에 잘 나와있음
- 빌드 속도를 개선하고 싶은 경우
	- [빌드 속도 향상을 위한 권장사항](https://cloud.google.com/cloud-build/docs/speeding-up-builds) 확인
- 컨테이너 빌드에 대한 권장사항
	- [Google Solution](https://cloud.google.com/solutions/best-practices-for-building-containers?hl=ko)
	- 컨테이너 빌드의 Best Practice에 작성된 글
- 빌드 이벤트시 알림 전송
	- 이 부분은 아직 아쉬운 부분. Pub/Sub으로 직접 구현해야 함
	- [빌드 알림 보내기](https://cloud.google.com/cloud-build/docs/send-build-notifications)에 설명이 있음
	- 차라리 Slack에 Github 통합시키면 채널에 빌드가 성공했는지 실패했는지 알 수 있음. Slack 연동 추천
- Micro Service에 배포하는 예시 
	- [Github](https://github.com/GoogleCloudPlatform/cloudbuild-integration-testing)
- 이정운님의 GKE에 Cloud Build를 통한 CI/CD 글
	- [Medium](https://medium.com/@jwlee98/gcp-gke-%EC%B0%A8%EA%B7%BC-%EC%B0%A8%EA%B7%BC-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0-5%ED%83%84-cloud-build-%EB%A5%BC-%ED%86%B5%ED%95%9C-ci-cd-47f1486937e4)
- Composer에서 테스트하고 배포하는 가이드라인 글
	- [Google Solution](https://cloud.google.com/solutions/cicd-pipeline-for-data-processing)
	- <img src="https://www.dropbox.com/s/motcjk1n9upnbwt/Screenshot%202020-05-09%2016.57.54.png?raw=1"> 
