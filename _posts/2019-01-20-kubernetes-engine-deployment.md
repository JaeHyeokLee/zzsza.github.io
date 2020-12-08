---
layout: post
title:  "Kubernetes Engine을 사용한 배포 관리(with Jenkins)"
subtitle: "Kubernetes Engine을 사용한 배포 관리"
categories: development
tags: kubernetes
comments: true
---

- 2019 클라우드 스터디 잼 입문반에서 진행하는 Kubernetes in the Google Cloud 퀵랩을 듣고 정리한 내용입니다

---

## Intro
- 지속적 배포, Blue Green 배포, 카나리 배포 같은 배포 시나리오에 대해 배울 예정
- kubectl 도구 연습
- 배포 yaml 파일 생성
- 배포 실행, 업데이트, 확장
- 배포 및 배포 스타일 업데이트 연습


### 배포
- 서로 다른 2개 이상의 인프라 환경, 지역을 연결해 기술, 운영상의 요구를 해결
- 배포 세부 사항에 따라 하이브리드, 멀티 클라우드, 공개-비공개 배포라고도 부름
- 배포가 단일 환경, 지역으로 제한될 경우 발생하는 이슈
	- 리소스 한도 도달
	- 제한된 지리적 범위
		- 단일 환경이면 멀리 떨어진 사용자들이 모두 하나의 배포에 액세스해야됨 
	- 제한된 가용성
	- 제공업체 종속
	- 경직된 리소스
- 일회성 배포가 아닌 절차를 가진 배포를 설계해야 함
	- 반복 가능하고 프로비저닝, 구성, 유지보수에 있어 검증된 방식을 사용해야 함
- 시나리오는 대표적으로 멀티 클라우드 배포, 프론트엔드 내부 데이터, 지속적 통합/지속적 배포 프로세스


### 실습
- 샘플 코드 clone

	```
	git clone https://github.com/googlecodelabs/orchestrate-with-kubernetes.git
	cd orchestrate-with-kubernetes/kubernetes
	```

- 5개의 n1-standard-1 노드를 갖는 클러스터 생성

	```
	gcloud config set compute/zone us-central1-a
	gcloud container clusters create bootcamp --num-nodes 5 --scopes "https://www.googleapis.com/auth/projecthosting,storage-rw"
	```	

### 배포 객체 알아보기
- 배포 객체 살펴보기

	```
	kubectl explain deployment
	```
	
- 모든 필드를 보려면 `--recursive` 옵션 추가

	```
	kubectl explain deployment --recursive
	```	
	
- 배포 객체의 구조와 개별 필드 이해

	```
	kubectl explain deployment.metadata.name
	```	
	
### 배포 만들기
- image 변경

	```
	vi deployments/auth.yaml
	i
	
	containers:
	- name: auth
	  image: kelseyhightower/auth:1.0.0
	```
- 배포 객체 생성

	```
	kubectl create -f deployments/auth.yaml
	```	

- 배포 확인

	```
	kubectl get deployments
	```
	
- 배포 복제본 생성

	```
	kubectl get replicasets
	```	

- 복제본 세트가 생성되면 단일 포드 생성

	```
	kubectl get pods
	```	
	
- 인증 배포 서비스 생성

	```
	kubectl create -f services/auth.yaml
	```
	
- hello 배포 만들고 노출

	```
	kubectl create -f deployments/hello.yaml
	kubectl create -f services/hello.yaml
	```	

- fronted 배포 생성하고 노출	
	
	```
	kubectl create secret generic tls-certs --from-file tls/
	kubectl create configmap nginx-frontend-conf --from-file=nginx/frontend.conf
	kubectl create -f deployments/frontend.yaml
	kubectl create -f services/frontend.yaml
	```
	
- 외부 IP를 선택한 후 curl로 상호 작용	

	```
	kubectl get services frontend
	curl -ks https://<EXTERNAL-IP>
	```

- kubectl의 출력 템플릿을 사용해 curl을 한 줄로 사용할 수 있음

	```
	curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`
	```
	
### 배포 확장
- `spec.replicas` 필드를 업데이트해 확장
- 필드 설명 확인

	```
	kubectl explain deployment.spec.replicas
	```
	
- `kubectl scale` 명령을 사용해 replicas 필드 업데이트 가능

	```
	kubectl scale deployment hello --replicas=5
	```	

- 실행 중인 인증 포드 5개인지 확인

	```
	kubectl get pods | grep hello- | wc -l
	```
	
- 애플리케이션 축소 후 확인

	```
	kubectl scale deployment hello --replicas=3
	kubectl get pods | grep hello- | wc -l
	```	

### 지속적 업데이트
- 이미지가 새 버전으로 업데이트 되도록 지원
- 배포가 새 버전으로 업데이트되면 새로운 복제본 세트가 생성되고 기존 복제본 세트의 복제본이 줄어들며 새 복제본 세트의 복제본 수가 서서히 늘어남
- 배포 업데이트

	```
	kubectl edit deployment hello
	```

- image를 kelseyhightower/hello:2.0.0로 변경
- 편집기에서 저장하면 업데이트된 배포가 클러스터에 저장되고 쿠버네티스에서 지속적 업데이트 시작
- 새 복제본 세트 조회

	```
	kubectl get replicaset
	```
	
- 롤아웃 내역에서 확인

	```
	kubectl rollout history deployment/hello
	```
	
### 지속적 업데이트 일시중지

```
kubectl rollout pause deployment/hello
```	

- 롤아웃 현재 상태 확인
	
	```
	kubectl rollout status deployment/hello
	```
	
- 포드에서 확인
	
	```
	kubectl get pods -o jsonpath --template='{range .items[*]}{.metadata.name}{"\t"}{"\t"}{.spec.containers[0].image}{"\n"}{end}'
	```	

### 지속적 업데이트 재개
- 롤아웃이 일시중지되면 일부만 새 버전이고 나머지는 기존 버전으로 유지(`resume` 명령 사용)

	```
	kubectl rollout resume deployment/hello
	```

- 상태 확인

	```
	kubectl rollout status deployment/hello
	```

### 업데이트 롤백
- 새 버전에서 버그가 발견된 경우 새로운 포드에 연결된 사용자들이 문제를 겪음
- 이전 버전으로 롤백해 조사하고 다시 수정된 버전을 릴리즈
- `rollout` 명령을 사용해 롤백

	```
	kubectl rollout undo deployment/hello
	```

- 롤백 확인

	```	
	kubectl rollout history deployment/hello
	```
	
- 모든 포드가 이전 버전으로 롤백되었는지 확인

	```
	kubectl get pods -o jsonpath --template='{range .items[*]}{.metadata.name}{"\t"}{"\t"}{.spec.containers[0].image}{"\n"}{end}'
	```
	
### 카나리 배포(Canary Releases)
- [설명 글](http://jason-lim.tistory.com/3)
- 사용자 하위 집합을 사용해 프러덕션의 새 배포를 테스트를 사용하고싶은 경우 선택하는 방법
- 소수의 사용자에 변경 사항을 릴리즈

### 카나리 배포 만들기
- 새 버전의 별도 배포와 안정적인 일반 배포 및 카나리 배포로 이루어짐
- 새 버전에 해당하는 카나리 배포 생성

	```
	cat deployments/hello-canary.yaml
	kubectl create -f deployments/hello-canary.yaml
	```

- 확인

	```
	kubectl get deployments
	```
	
- curl

	```
	curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version
	```
	
### Blue-Green 배포
- 지속적 업데이트는 최소한 오버헤드, 성능 영향, 다운타임으로 천천히 배포할 수 있어서 이상적
	- 완전히 배포된 후 새 버전을 가리키도록 부하 분산기를 수정하는 편이 유용할 경우가 존재하는데, 이 경우 Blue-Green 배포 사용
- 기존 Blue 버전과 새로운 Green 버전의 배포를 각각 하나씩 생성해 구현
	- 새로운 Green 버전이 실행되면 서비스를 업데이트해 이 버전을 사용하도록 전환
- 단점
	- 클러스터의 리소스가 2배 이상 필요
- 서비스 업데이트

	```
	kubectl apply -f services/hello-blue.yaml
	```  
	
- Green 배포 버전 라벨 및 이미지 경로 업데이트

	```
	apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: hello-green
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: hello
        track: stable
        version: 2.0.0
    spec:
      containers:
        - name: hello
          image: kelseyhightower/hello:2.0.0
          ports:
            - name: http
              containerPort: 80
            - name: health
              containerPort: 81
          resources:
            limits:
              cpu: 0.2
              memory: 10Mi
          livenessProbe:
            httpGet:
              path: /healthz
              port: 81
              scheme: HTTP
            initialDelaySeconds: 5
            periodSeconds: 15
            timeoutSeconds: 5
          readinessProbe:
            httpGet:
              path: /readiness
              port: 81
              scheme: HTTP
            initialDelaySeconds: 5
            timeoutSeconds: 1
    ```

- Green 배포 생성

	```
	kubectl create -f deployments/hello-green.yaml
	```	

- 현재 버전 사용하는지 확인

	```
	curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version
	```
	
- 서비스 업데이트

	```
	kubectl apply -f services/hello-green.yaml
	```

- 서비스가 업데이트되는 즉시 Green 배포가 사용됨

	```
	curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version
	```
	
### Blue-Green 롤백	
- 기존 버전으로 업데이트

	```
	kubectl apply -f services/hello-blue.yaml
	```

- 서비스를 업데이트하면 롤백 성공

	```
	curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version
	```	
	

## Kubernetes Engine에서 Jenkins를 사용한 지속적 통합(Conitnuous Delivery)
<img src="https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/d5bfbd0f6d109df463005f1ceb195799ec68b7b4c370eb18cec470a7f1f812b1.png">

- 젠킨스를 사용해 지속적 통합을 진행
	- 빌드, 테스트, 배포의 파이프라인을 통합 
- 미리 선행해야 하는 일
	- 쿠버네티스 엔진 클러스터에 젠킨스 Provision
	- 젠킨스에 Helm Package Manager 설정
	- Feature들 탐험
	- 젠킨스 파이프라인 생성하고 테스트
- Kubernetes Engine
	- GCP의 Managed Kbernetes 
- Jenkins
	- 빌드, 테스트, 배포 파이프라인을 유연하게 조절할 수 있는 도구
	- 프로젝트를 신속하게 반복할 수 있도록 지원
- Repo 준비

	```
	gcloud config set compute/zone us-central1-f
	git clone https://github.com/GoogleCloudPlatform/continuous-deployment-on-kubernetes.git
	cd continuous-deployment-on-kubernetes
	```

### Provisioning Jenkins
- Kubernetes 클러스터 생성

	```
	gcloud container clusters create jenkins-cd \
--num-nodes 2 \
--machine-type n1-standard-2 \
--scopes "https://www.googleapis.com/auth/projecthosting,cloud-platform"
	```

- 완료된 Task 테스트

	```
	gcloud container clusters list
	gcloud container clusters get-credentials jenkins-cd
	kubectl cluster-info
	>>> is running at ~
	```

- Helm 설치
	
	```
	wget https://storage.googleapis.com/kubernetes-helm/helm-v2.9.1-linux-amd64.tar.gz
	tar zxfv helm-v2.9.1-linux-amd64.tar.gz
cp linux-amd64/helm .
	# permission
	kubectl create clusterrolebinding cluster-admin-binding --clusterrole=cluster-admin --user=$(gcloud config get-value account)
	# cluster-admin role
	kubectl create serviceaccount tiller --namespace kube-system
kubectl create clusterrolebinding tiller-admin-binding --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
	./helm init --service-account=tiller
	./helm update
	# version 확인
	./helm version
	```
	
- 젠킨스 설정 및 설치
	- 설정 Deploy
		
		```
		./helm install -n cd stable/jenkins -f jenkins/values.yaml --version 0.16.6 --wait
		```
	
	- 젠킨스 pod 확인

		```
		kubectl get pods
		```
	
	- 젠킨스 UI에서 클라우드 쉘로 포워딩 설정

		```
		export POD_NAME=$(kubectl get pods -l "component=cd-jenkins-master" -o jsonpath="{.items[0].metadata.name}")
kubectl port-forward $POD_NAME 8080:8080 >> /dev/null &
		```
	
	- 젠킨스 서비스 확인

		```
		kubectl get svc
		>>> cd-jenkins         ClusterIP   10.11.255.161   <none>        8080/TCP    4m
	cd-jenkins-agent   ClusterIP   10.11.251.179   <none>        50000/TCP   4m
	kubernetes         ClusterIP   10.11.240.1     <none>        443/TCP     27m
		```

- 젠킨스 마스터가 요청할 때 빌더 노드가 필요할 때 자동으로 실행되도록 쿠버네티스 플러그인을 사용
	- 작업이 완료되면 자동으로 해제되고 리소스가 클러스터 리소스 풀에 다시 추가됨
	 
### 젠킨스 연결
- 실행

	```
	printf $(kubectl get secret cd-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
	```

- 포트 8080 생성	
	- ID : admin, PW : 위에서 나온 것을 복붙

	
### 배포
- Production : Live
- Canary : samller-capacity site

	```
	cd sample-app
	kubectl create ns production
	kubectl apply -f k8s/production -n production
	kubectl apply -f k8s/canary -n production
	kubectl apply -f k8s/services -n production
	```

- Production 환경 scale up

	```
	kubectl scale deployment gceme-frontend-production -n production --replicas 4
	```
	
- fronted 설정

	```
	kubectl get pods -n production -l app=gceme -l role=frontend
	```
	
- backend 설정

	```
	kubectl get pods -n production -l app=gceme -l role=backend
	```	

- 외부 IP 노출

	```
	kubectl get service gceme-frontend -n production
	```
	
- Export

	```
	export FRONTEND_SERVICE_IP=$(kubectl get -o jsonpath="{.status.loadBalancer.ingress[0].ip}" --namespace=production services gceme-frontend)
	```
	
- Curl
	
	```
	curl http://$FRONTEND_SERVICE_IP/version
	```	
	
### 젠킨스 파이프라인 생성
- gceme 복사하고 Cloud Source Repository에 Push

	```
	gcloud alpha source repos create default
	```

- Init

	```
	git init
	git config credential.helper gcloud.sh
	git remote add origin https://source.developers.google.com/p/$DEVSHELL_PROJECT_ID/r/default
	```	

- config

	```
	git config --global user.email "[EMAIL_ADDRESS]"
	git config --global user.name "[USERNAME]"
	```

- add, commit, push

	```
	git add .
	git commit -m "Initial commit"
	git push origin master
	```
	
	
## Reference
- [Kubernetes in the Google Cloud Qwiklabs](https://google.qwiklabs.com/quests/29?qlcampaign=1s-seoul-0219)
