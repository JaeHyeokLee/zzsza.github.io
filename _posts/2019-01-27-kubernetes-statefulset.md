---
layout: post
title:  "Kubernetes StatefulSet를 사용해 MongoDB 실행하기"
subtitle: "Kubernetes StatefulSet를 사용해 MongoDB 실행하기"
categories: development
tags: kubernetes
comments: true
---

- 2019 클라우드 스터디 잼 입문반에서 진행하는 Kubernetes in the Google Cloud 퀵랩을 듣고 정리한 내용입니다

---

## Basic
- Stateless Application의 특징
	- 디스크에 중요한 데이터가 없음
	- 필요한 만큼 여러 똑같은 컨테이너를 시작, 종료할 수 있음
	- 컨테이너가 죽으면 보관중 데이터는 사라짐
- Stateful Application의 특징
	- Container-specific
	- 암호, 인증키, 설정값을 Instance에 개별 할당 
- Stateful 정의
	- Stateful means the computer or program keeps track of the state of interaction, usually by setting values in a storage field designated for that purpose
	- 컴퓨터, 프로그램이 스토리지 필드에 값을 설정하고 상호 작용에 대한 상태를 지속적으로 기록
	- StatefulSet은 stateful한 애플리케이션을 관리하기 위해 사용하는 workload
	- Pod 각각에 대해 sticky identity를 유지
- StatefulSet의 복제본은 배포, 크기 조정, 업그레이드 및 종료에 대한 정상적이고 순차적인 접근 방식을 따름
	- StatefulSet을 사용하면 복제본이 다시 예약될 때 명명 규칙, 네트워크 이름 및 저장소가 그대로 유지

### 기본 설정
- 컴퓨터 영역 설정
	
	```
	gcloud config set compute/zone us-central1-f
	```

- 새 클러스터 생성
	- 3개의 노드(가상 머신)가 있는 새 클러스터 생성

	```
	gcloud container clusters create hello-world
	```	

### 저장소 등급
- 저장소 등급 설정
	- MongoDB 복제본 세트를 설정하기 위해 StorageClass, Headless Service, StatefulSet 설정
	- 예제 구성 파일 복제

	```
	git clone https://github.com/thesandlord/mongo-k8s-sidecar.git
cd ./mongo-k8s-sidecar/example/StatefulSet/
	```
	
- 저장소 등급 생성
	- 저장소 등급 구성 확인(SSD와 하드디스크)

	```
	cat googlecloud_ssd.yaml
	```	

- 저장소 등급 배포(kubectl apply 명령어 사용)

	```
	kubectl apply -f googlecloud_ssd.yaml
	```
	
### Headless Service 및 StatefulSet 배포
- Headless Serice 구성 확인
	- clusterIP가 'None'으로 설정되어 있어서 Headless Service를 확인할 수 있음
	- StatefulSet과 함께 사용시 포등 ㅔ직접 액세스할 수 있는 DNS 주소를 제공받을 수 있음
	
	```
	cat mongo-statefulset.yaml
	```

- StatefulSet 확인
	
	```
	cat mongo-statefulset.yaml
	```	
	
	```
	apiVersion: apps/v1beta1
	kind: StatefulSet
	metadata:
	name: mongo
	spec:
	serviceName: "mongo"
	replicas: 3
	template:
	   metadata:
	     labels:
	       role: mongo
	       environment: test
	   spec:
	     terminationGracePeriodSeconds: 10
	     containers:
	       - name: mongo
	         image: mongo
	         command:
	           - mongod
	           - "--replSet"
	           - rs0
	           - "--smallfiles"
	           - "--noprealloc"
	         ports:
	           - containerPort: 27017
	         volumeMounts:
	           - name: mongo-persistent-storage
	             mountPath: /data/db
	       - name: mongo-sidecar
	         image: cvallance/mongo-k8s-sidecar
	         env:
	           - name: MONGO_SIDECAR_POD_LABELS
	             value: "role=mongo,environment=test"
	volumeClaimTemplates:
	- metadata:
	     name: mongo-persistent-storage
	     annotations:
	       volume.beta.kubernetes.io/storage-class: "fast"
	   spec:
	     accessModes: [ "ReadWriteOnce" ]
	     resources:
	       requests:
	         storage: 100Gi
	```
	
	- StatefulSet 객체에 대한 설명, 메타데이터 부분은 레이블과 복제본의 수를 지정
	- spec 부분엔 포드 사양을 작성. name이 mongo와 mongo-sidecar가 존재하는데, 사이드카 컨테이너가 복제본 세트를 자동으로 구성
	- volumnClaimTemplates는 전에 생성한 StorageClass를 호출해 볼륨을 프로비저닝하고 MongoDB 복제본당 100GB 디스크를 프로비저닝함
- 배포

	```
	kubectl apply -f mongo-statefulset.yaml
	```
	
### MongoDB 복제본 세트에 연결
- 아래 명령어를 출력해 DESIRED와 CURRENT 수가 동일한지 확인

	```
	kubectl get statefulset
	```

- 클러스터 3개 포드 확인

	```
	kubectl get pods
	```
	
- 첫 복제본 세트 멤버에 연결

	```
	kubectl exec -ti mongo-0 mongo
	```
	
- MongoDB REPL에 연결됨. 기본 구성으로 복제본 세트 시작	
	```
	rs.initiate()
	```
	
- 복제본 세트 구성 인쇄

	```
	rs.conf()
	```	
	
- 종료

	```
	exit
	```
	
### MongoDB 복제본 세트 확장
- 복제본을 3개에서 5개로 늘리기

	```
	kubectl scale --replicas=5 statefulset mongo
	```	
	
- 아래 명령어로 확인

	```
	kubectl get pods
	```
	
- 5개에서 3개로 줄이기

	```
	kubectl scale --replicas=3 statefulset mongo
	```

- 아래 명령어로 확인

	```
	kubectl get pods
	```		
	
### MongoDB 복제본 세트 활용
- Headless Serivce가 지원하는 StatefulSet의 각 포드엔 안정된 DNS 이름이 존재. <Pod name>.<Service Name>의 형식
- 3개의 복제본 세트의 DNS 이름은 아래와 같음

	```
	mongo-0.mongo
	mongo-1.mongo
	mongo-2.mongo
	```	
	
- 연결 문자열 URI는 아래와 같음

	```
	"mongodb://mongo-0.mongo,mongo-1.mongo,mongo-2.mongo:27017/dbname_?"
	```
	
### 삭제하기
- StatefulSet 삭제

	```
	kubectl delete statefulset mongo
	```

- Headless Service 삭제

	```
	kubectl delete svc mongo
	```	
	
- 볼륨 삭제
	
	```
	kubectl delete pvc -l role=mongo
	```
	
- 클러스터 삭제

	```
	gcloud container clusters delete "hello-world"
	```	
 
## Reference
- [Kubernetes in the Google Cloud Qwiklabs](https://google.qwiklabs.com/quests/29?qlcampaign=1s-seoul-0219)
- [Kubernetes Tutorial](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
- [Kubernetes StatefulSet 개요 & Nginx Web Cluster(1/5)](https://bryan.wiki/298)
- [Azure cluster workloads](https://docs.microsoft.com/ko-kr/azure/aks/concepts-clusters-workloads)
