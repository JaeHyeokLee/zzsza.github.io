---
layout: post
title:  "Kubernetes 개요 및 애플리케이션 배포"
subtitle:   "Kubernetes 개요 및 애플리케이션 배포"
categories: development
tags: kubernetes
comments: true
---

- 2019 클라우드 스터디 잼 입문반에서 진행하는 Kubernetes in the Google Cloud 퀵랩을 듣고 정리한 내용입니다

---

## Introduction to docker
- Dokcer를 사용하면 인프라에서 어플리케이션을 분리하고 인프라를 managed 어플리케이션처럼 사용할 수 있음
- Docker는 코드를 신속하게 제공하고 테스트, 배포 속도를 높이고 코드 작성과 실행 사이의 주기를 단축해줌
- Dokcer 컨테이너는 쿠버네티스에서 쉽게 사용할 수 있음
- Docker의 핵심을 배우며 쿠버네티스 및 컨테이너 응용 프로그램을 개발할 수 있음


### Hello World
- hello-world docker run!

	```
	docker run hello-world
	```
- docker 이미지 출력

	```
	docker images
	```

- docker running 컨테이너 보기

	```
	docker ps
	>>> 처음엔 아무것도 뜨지 않음
	```
	
- 모든 docker 이미지를 보려면 `-a` 추가

	```
	docker ps -a
	```	

- docker run --name [container-name] hello-world
	- container 이름을 앞의 글자 3개만 사용해도 됨!

### Build
- test 폴더 생성

	```
	mkdir test && cd test
	```
	
- Dockerfile 생성
	- 더 궁금할 경우 [Docker command reference](https://docs.docker.com/engine/reference/builder/#run) 참고
	
	```
	cat > Dockerfile <<EOF
	# Use an official Node runtime as the parent image
	FROM node:6 # 이미지 지정
	# Set the working directory in the container to /app
	WORKDIR /app # 컨테이너 작업 디렉토리 설정
	# Copy the current directory contents into the container at /app
	ADD . /app # 현재 디렉토리 내용을 컨테이너에 추가
	# Make the container's port 80 available to the outside world
	EXPOSE 80 # 80 포트를 연결을 허용
	# Run app.js using node when the container launches
	CMD ["node", "app.js"] # node 명령을 실행해 프로그램 시작
	EOF
	```
	
- app.js 생성

	```
	cat > app.js <<EOF
	const http = require('http');
	
	const hostname = '0.0.0.0';
	const port = 80;
	
	const server = http.createServer((req, res) => {
	    res.statusCode = 200;
	      res.setHeader('Content-Type', 'text/plain');
	        res.end('Hello World\n');
	});
	
	server.listen(port, hostname, () => {
	    console.log('Server running at http://%s:%s/', hostname, port);
	});
	
	process.on('SIGINT', function() {
	    console.log('Caught interrupt signal and will exit');
	    process.exit();
	});
	EOF
	```

- build

	```
	docker build -t node-app:0.1 .
	# Dockerfile이 있는 디렉토리에서 명령어 실행
	# -t는 이미지에 태그, name:tag 
	```
	
- docker images 확인


### Run
- Run
	
	```
	docker run -p 4000:80 --name my-app node-app:0.1
	```	

- 다른 터미널에서 아래 명령어 입력
	
	```
	curl http://localhost:4000
	>>> Hello World
	```
	
- docker stop and remove
	
	```
	docker stop my-app && docker rm my-app
	```

- 다시 run
	
	```
	docker run -p 4000:80 --name my-app -d node-app:0.1
	docker ps
	```

- docker logs
	
	```
	docker logs [container_id]
	```

- 호스트의 8080 포트를 컨테이너의 80포트로 연결

	```
	docker run -p 8080:80 --name my-app-2 -d node-app:0.2
	docker ps
	```	
	
### Debug
- 작동중 container 로그 출력
	
	```
	docker logs -f [container_id]
	```

- Interactive Bash session을 시작해 container로 진입
	
	```
	docker exec -it [container_id] bash
	```

- inspect
	- metadata 검사
	
	```
	docker inspect [container_id]
	```

	- `--format`을 사용하면 특정 필드를 json타입으로 리턴
	
	```
	docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' [container_id]
	```


### Publish
- 자신의 이미지를 [Google Container Registry](https://cloud.google.com/container-registry/)에 push!
- gcr이 호스팅하는 비공개 레지스트리에 이미지를 푸시
	- 형식 : [hostname]/[project-id]/[image]:[tag]
- Project ID 확인
	
	```
	gcloud config list project
	```

- docker tag
	
	```
	docker tag node-app:0.2 gcr.io/[project-id]/node-app:0.2
	```

- push
	
	```
	gcloud docker -- push gcr.io/[project-id]/node-app:0.2
	```	
		
- 모든 컨테이너 stop and remove
	
	```	
	docker stop $(docker ps -q)
	docker rm $(docker ps -aq)
	```

- 컨테이너 이미지 삭제

	```
	docker rmi node-app:0.2 gcr.io/[project-id]/node-app node-app:0.1
	docker rmi node:6
	docker rmi $(docker images -aq) # remove remaining images
	docker images
	```	
	
- gcloud docker pull
	
	```
	gcloud docker -- pull gcr.io/[project-id]/node-app:0.2
	
	```	

## Hello Node Kubernetes
<img src="https://run-qwiklab-website-prod.s3.amazonaws.com/instructions/documents/93937/original/img/ba830277f2d92e04.png">

- 쿠버네티스
	- Notebook, 멀티노드 클러스터, 클라우드에서 내부 배포할 수 있고 다양한 환경에서 실행할 수 있는 오픈소스
	- Kubernetes Engine(Google의 Managed Service)를 사용해 인프라 설정보다 쿠버네티스를 경험하는 것에 집중
	- [kubectl document](https://kubernetes.io/docs/reference/kubectl/overview/)
- Node.js 애플리케이션 만들기
	- 쿠버네티스 엔진에 배포할 애플리케이션 작성
	- server.js
	
	```
	var http = require('http');
	var handleRequest = function(request, response) {
	  response.writeHead(200);
	  response.end("Hello World!");
	}
	var www = http.createServer(handleRequest);
	www.listen(8080);
	```  

	- node server.js
- 이걸 docker 컨테이너로 패키징
	- vi Dockerfile
	
	```
	FROM node:6.9.2 # docker 허브의 node 이미지로 시작
	EXPOSE 8080 # 포트 8080 노출
	COPY server.js . # 사용자가 만든 server.js를 이미지로 복사
	CMD node server.js # node 서버 시작
	``` 
	
	- 이미지 빌드
	
	```
	docker build -t gcr.io/PROJECT_ID/hello-node:v1 .
	```
	
	- Docker 컨테이너 이미지에서 포트 8080로 데몬 실행
		- [Docker run](https://docs.docker.com/engine/reference/run/) 
		- `-d` : Detached mode

	```
	docker run -d -p 8080:8080 gcr.io/PROJECT_ID/hello-node:v1
	```
	
	- Docker 컨테이너 중지
		- docker ps에 나오는 컨테이너 중지
	- Google Container Registry로 이미지 Push
		- Container Registry는 Google Cloud 프로젝트에서 액세스 가능
		
		```
		gcloud docker -- push gcr.io/PROJECT_ID/hello-node:v1
		```
		
		- 이제 쿠버네티스가 접근할 수 있는 도커 이미지 만들어짐

- 클러스터 만들기
	- Container Engine 클러스터 생성
	- 하나의 클러스터는 쿠버네티스 마스터 API 서버와 노드로 구성, 노드는 Computer Engine 가상 머신 
	- Kubernetes Engine에 접속

	```
	gcloud container clusters create hello-world \
                --num-nodes 2 \
                --machine-type n1-standard-1 \
                --zone us-central1-f
    ```

- 포드 만들기
	- 포드는 관리 및 네트워킹 용도로 서로 연결된 컨테이너 그룹
	- 포드는 하나 또는 여러 개의 컨테이너를 포함할 수 있음    
	
	```
	kubectl run hello-node \
    --image=gcr.io/PROJECT_ID/hello-node:v1 \
    --port=8080
	```
	
	- deployment "hello-node" created란 문구가 나오면 배포 개체를 만든 것!
	- 포드를 만들고 확장할 땐 배포를 사용하는게 좋음
	- 배포 확인(마치 docker ps 같음)
	
		```
		kubectl get deployments
		```
	
	- 배포 만들어진 포드 확인
	
		```
		kubectl get pods
		```
		
	- 클러스터 정보 확인
		
		```
		kubectl cluster-info
		```
		
	- 클러스터 config 확인
		
		```
		kubectl config view
		```
	
	- pod 로그 확인

		```
		kubectl get events
		kubectl logs <pod name>
		```
	
### 외부 트래픽 허용	
- Pod는 클러스터에 포함된 내부 IP로만 액세스 가능
	- 가상 네트워크 외부에서 컨테이너에 액세스 하려면 포드를 쿠버네티스 서비스로 노출시켜야 함
	- kubectl expose 명령에 --type="LoadBalancer" 플래그를 사용!
	
		```
		kubectl expose deployment hello-node --type="LoadBalancer"
		```
	- 직접 노출시키는 것이 아닌 배포를 노출시킴!!!!
	- 서비스가 이 배포에서 관리하는 모든 포드에 걸쳐 트래픽의 부하를 분산
- IP 주소 확인
	- 외부 부하 분산 IP 1개 보임
	
	```
	kubectl get services
	```
		
### 서비스 규모 확장하기
<img src="https://run-qwiklab-website-prod.s3.amazonaws.com/instructions/documents/93937/original/img/587f7f0a097aaa2.png">

- 쿠버네티스의 강력한 기능은 애플리케이션을 간편하게 확장할 수 있는 것
	- 갑자기 용량이 필요해졌을 경우 복제 컨트롤러에 새로운 복제를 관리하라고 명령할 수 있음
	
		```
		kubectl scale deployment hello-node --replicas=4
		``` 
	
	- 배포에 관한 설명 확인
	
		```
		kubectl get deployment
		kubectl get pods
		``` 	

### 서비스의 업그레이드 롤아웃
- 배포한 애플리케이션에 버그 수정이나 추가 기능을 적용해야 할 경우
	- server.js 하단에 response.end("Hello Kubernetes World!"); 추가
	- 빌드 및 push
	
		```
		docker build -t gcr.io/PROJECT_ID/hello-node:v2 . 
		gcloud docker -- push gcr.io/PROJECT_ID/hello-node:v2
		```
	- 실행 중인 컨테이너의 이미지 라벨을 기존 hello-node-deployment를 수정해야 함
		- `gcr.io/PROJECT_ID/hello-node:v1 => gcr.io/PROJECT_ID/hello-node:v2`
		- 이를 위해 `kubectl edit` 명령 사용 
		- kubectl edit deployment hello-node
		- containers의 image를 수정
	- 새로운 이미지 배포
		
		```
		kubectl get deployments
		```

### 그래픽 대시보드
- kubernetes 클러스터 대시보드에 액세스 하려면 아래 명령어 실행

	```
	gcloud container clusters get-credentials hello-world \
    --zone us-central1-f --project <PROJECT_ID>
    kubectl proxy --port 8081
	```	

- URL에서 ?authuser=0을 삭제하고 끝이 /ui를 붙임
	- 근데 대시보드 안나오는데요..? 


## Kubernetes를 통한 클라우드 조정
- kubectl을 사용해 Docker 컨테이너를 배포하고 관리
- 하나의 애플리케이션을 마이크로 서비스로 나눔
- 영역 설정

	```
	gcloud config set compute/zone us-central1-b
	```
	
- 클러스터 설정(시간이 좀 소요됨)
	
	```
	gcloud container clusters create io
	``` 	

- 코드 clone
	
	```
	git clone https://github.com/googlecodelabs/orchestrate-with-kubernetes.git
	cd orchestrate-with-kubernetes/kubernetes
	```

### 쿠버네티스 데모
- Kubernetes 시작하는 가장 쉬운 방법 : `kubectl run`
- ngninx 컨테이너 인스턴스 실행
	
	```
	kubectl run nginx --image=nginx:1.10.0
	```
- 실행 중인 nginx 컨테이너 보기
	
	```
	kubectl get pods
	```	
	
- Kubernetes 외부로 노출
	- 공개 IP 주소로 도달한 클라이언트는 서비스 뒤에 있는 포드로 라우팅
	
	```
	kubectl expose deployment nginx --port 80 --type LoadBalancer
	```	

- 서비스 나열
	
	```
	kubectl get services
	```	
	
- curl http://<External IP>:80 로 확인

## Pod
<img src="https://run-qwiklab-website-prod.s3.amazonaws.com/instructions/documents/92971/original/img/56124565adb6c28b.png">

- 쿠버네티스의 핵심 요소
	- 하나 이상의 컨테이너가 포함된 집합
	- 종속도가 높은 여러 컨테이너가 있을 경우 컨테이너들은 단일 포드로 패키징
- 볼륨
	- 포드가 활성화되어 있어야 사용 가능한 데이터 디스크
	- 포드는 컨텐츠에 대한 공유 네임스페이스를 제공
	- 예제 포드에 속한 두 컨테이너가 서로 통신할 수 있으며 연결된 볼륨도 공유함 
- 포드는 네트워크 네임스페이스도 공유
	- 포드마다 IP 주소 하나씩 존재

### 포드 만들기
```
pods/monolith.yaml
```

- 구성 파일 출력

	```
	apiVersion: v1
	kind: Pod
	metadata:
	  name: monolith
	  labels:
	    app: monolith
	spec:
	  containers:
	    - name: monolith
	      image: kelseyhightower/monolith:1.0.0
	      args:
	        - "-http=0.0.0.0:80"
	        - "-health=0.0.0.0:81"
	        - "-secret=secret"
	      ports:
	        - name: http
	          containerPort: 80
	        - name: health
	          containerPort: 81
	      resources:
	        limits:
	          cpu: 0.2
	          memory: "10Mi"
  ```
  
  - 포드는 하나의 컨테이너로 구성
  - 시작할 때 몇 개의 인수를 컨테이너에 전달
  - http 트래픽을 위해 포트 80을 개방
- kubectl을 사용해 모놀리식 포드 생성
	
	```
	kubectl create -f pods/monolith.yaml
	```
- kubectl get pods를 사용해 포드 나열
- 모놀리식 포드 정보 요약
	
	```
	kubectl describe pods monolith
	``` 

### 포드 상호 작용
- 포드는 기본적으로 비공개 IP 주소가 할당되고 클러스터 외부에 도달할 수 없음
- `kubectl port-forward` 명령을 사용해 로컬 포트를 모놀리식 포드의 포트로 매핑
- (새 터미널에서) 포트 전달
	
	```
	kubectl port-forward monolith 10080:80
	```
- 통신 확인
	
	```
	curl http://127.0.0.1:10080
	```	
- 로그인
	
	```
	curl -u user http://127.0.0.1:10080/login
	```
	
	- 비밀번호 : password를 입력하면 토큰이 생성됨! 복사해서 사용	
	
	```
	TOKEN=$(curl http://127.0.0.1:10080/login -u user|jq -r '.token')
	```
	
- 안전한 엔드포인트 도달
	
	```
	curl -H "Authorization: Bearer $TOKEN" http://127.0.0.1:10080/secure
	```
	
- 모놀리식 포드 로그 조회
	
	```
	kubectl logs monolith
	```
	
- 새 터미널을 열고 실시간 로그 스트림 받기
	
	```
	kubectl logs -f monolith
	```	
	
- 쿠버네티스 포드의 대화형 셸 실행하기

	```
	kubectl exec monolith --stdin --tty -c monolith /bin/sh
	```	

### 서비스
<img src="https://run-qwiklab-website-prod.s3.amazonaws.com/instructions/documents/92971/original/img/b8b7a35f34a77aec.png">

- [서비스 문서](https://kubernetes.io/docs/concepts/services-networking/service/)
- 포드는 영구적인 것이 아님
	- 활성 여부나 준비 확인의 실패 등 여러 이유로 중단되거나 시작될 수 있음
- 포드 집합과 통신하려고 하면?
	- 다시 시작될 때는 다른 IP 주소가 할당될 수도 있는데, 이 떄 서비스를 사용함
- 서비스는 포드를 위한 안정적인 엔드포인트를 제공함  
- 서비스는 라벨을 사용해 서비스가 수행될 포드를 판단
	- 포드에 올바른 라벨이 있으면 서비스에서 자동으로 포드를 선택해 노출
- 서비스가 포드 집합에 제공하는 액세스 수준은 서비스 유형에 따라 다름
	- `ClusterIP`(내부) : 클러스터 내부에서만 볼 수 있음
	- `NodePort` : 클러스터의 각 노드에 외부 액세스가 가능한 IP 제공
	- `LoadBalancer`

### 서비스 만들기
- 서비스를 만들기 전에 https 트래픽을 처리할 수 있는 안전한 포드 생성
	
	```
	cd ~/orchestrate-with-kubernetes/kubernetes
	cat pods/secure-monolith.yaml
	``` 
	
	```
	kubectl create secret generic tls-certs --from-file tls/
	kubectl create configmap nginx-proxy-conf --from-file nginx/proxy.conf
	kubectl create -f pods/secure-monolith.yaml
	```

- 서비스 구성 파일 탐색
	
	```
	cat services/monolith.yaml
	```
	
	```
	kind: Service
	apiVersion: v1
	metadata:
	  name: "monolith"
	spec:
	  selector: # 포드를 자동으로 찾아 노출
	    app: "monolith"
	    secure: "enabled"
	  ports:
	    - protocol: "TCP"
	      port: 443
	      targetPort: 443
	      nodePort: 31000 # 31000에서 443로 외부 트래픽 전송
	  type: NodePort
	```

- 모놀리식 서비스 생성
	
	```
	kubectl create -f services/monolith.yaml
	```

- service "monolith" created라는 출력은 포트를 사용해 서비스를 노출하고 있다는 의미	
- 노출된 포트에서 트래픽 허용
	
	```
	gcloud compute firewall-rules create allow-monolith-nodeport \
  --allow=tcp:31000
  ```

- curl	
	
	```
	curl -k https://<EXTERNAL_IP>:31000
	```
	
	- 시간이 초과됨

### 포드에 라벨 추가
- 현재 위 모놀리식 서비스에는 엔드포인트가 없음
- `kubectl get pods` 명령과 라벨 쿼리를 사용해 문제를 해결할 수 있음
	
	```
	kubectl get pods -l "app=monolith"
	```
- secure=enabled 추가
	
	```
	kubectl get pods -l "app=monolith,secure=enabled"
	```	
	
- 누락된 secure=enabled 라벨을 포드에 추가

	```
	kubectl label pods secure-monolith 'secure=enabled'
	kubectl get pods secure-monolith --show-labels
	```	

- 모놀리식 서비스의 엔드포인트 조회

	```
	kubectl describe services monolith | grep Endpoints
	```	

- 이제 접근 가능
		
	```
	curl -k https://<EXTERNAL_IP>:31000
	```
	

### Kubernetes를 사용한 애플리케이션 배포
- 프러덕션 단계에서 컨테이너를 확장하고 관리하는 방법에 대해 배움
- 실행 중인 포드의 수가 사용자가 지정한 포드 개수와 일치하도록 보장
- 앱의 세 부분
	- 1) 인증 : 인증된 사용자의 JWT 토큰 생성
	- 2) 환영 : 인증된 사용자에게 인사
	- 3) 프론트엔드 : 인증 및 환영 서비스로 트래픽 라우팅
- 서비스마다 배포를 하나씩 생성
	- 내부 서비스와 프론트엔드 배포의 외부 서비스를 정의할 예정
	- 완료되면 모놀리식 앱에서처럼 마이크로 서비스와 상호 작용할 수 있음
	- 각 부분의 독립적 확장 및 배포가 가능해짐
- 배포 객체 생성

	```
	kubectl create -f deployments/auth.yaml
	```

- 인증 배포 서비스 생성

	```
	kubectl create -f services/auth.yaml
	```	

- 환영 배포를 만들고 노출

	```
	kubectl create -f deployments/hello.yaml
	kubectl create -f services/hello.yaml
	```
	
- 프론트엔드 배포 및 노출

	```
	kubectl create configmap nginx-frontend-conf --from-file=nginx/frontend.conf
	kubectl create -f deployments/frontend.yaml
	kubectl create -f services/frontend.yaml
	```
	
- 외부 IP 확인 후 curl
	
	```
	kubectl get services frontend
	curl -k https://<EXTERNAL-IP>
	``` 	

### 정리
- 리소스 정리
	
	```
	cat cleanup.sh
	chmod +x cleanup.sh
	./cleanup.sh
	
	gcloud container clusters delete io --zone 
	```
	
	
## Reference
- [Kubernetes in the Google Cloud Qwiklabs](https://google.qwiklabs.com/quests/29?qlcampaign=1s-seoul-0219)
