---
layout: post
title:  "Docker와 쿠버네티스의 이해"
subtitle: "Docker와 쿠버네티스의 이해"
categories: development
tags: linux
comments: true
---
[네이버 AI 해커톤](https://github.com/naver/ai-hackathon-2018)에 참여하다가 빠르게 도커 사용법을 익혀야해서 찾아본 방법 및 IBM developerWorks 밋업에서 진행한 도커와 쿠버네티스, 두 마리 토끼를 잡자!을 들으며 기록한 Docker, Kubernetes를 정리한 문서입니다

# Contents
- [Docker](#docker)
- [Kubernetes](#kubernetes)

## Docker
- Docker : 컨테이너 기반의 오픈소스 가상화 플랫폼
- Images : 컨테이너 실행에 필요한 파일과 설정값 등을 포함하는 것으로, 상태값을 가지지 않고 변하지 않습니다(Immutable)
	- tar 파일을 묶어놓은 file system으로 Template같은 친구들
- Container : 이미지를 실행한 상태이며 추가되거나 변하는 값은 컨테이너에 저장됩니다
	- 단, 컨테이너를 삭제하면 내부의 데이터가 삭제됩니다. 이를 해결하기 위해 ```Volumn```을 사용합니다 
- [서비큐라님 Docker 글](https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html) : 꼭 읽어보기!! 추천!!!
- [서비큐라님 Kubernetes 글](https://subicura.com/2019/05/19/kubernetes-basic-1.html)
- [IBM Document](http://docker-dwmeetup.mybluemix.net/docker1.html)

---

### 도커가 등장하기 전 서버 운영
- 자체 서버 운영 ⇒ 설정 관리 도구 등장(ansible, chef 등) ⇒ 가상머신 등장 ⇒ 클라우드 등장 ⇒ PaaS 등장 ⇒ 도커 등장 ⇒ 쿠버네티스 등장 ⇒ 서비스메시 등장
    - 서비스메시
        - 요소마다 프록시 서버가 떠서 네트워크 감시 ⇒ 네트워크
- 가상 머신 등장
    - immutable 변하지 않는
    - mutable vs immutable
        - 서버에 설치된 애플리케이션을 새로운 버전으로 업데이트하면 mutable
            - 업그레이드하다 네트워크 이슈로 타임아웃날 수 있음. 논리적으론 맞지만 오류가 날 수 있음
        - 새로운 버전이 설치된 서버의 상태를 이미지로 만들고 교체하면 immutable
        - 개념이 단순해짐
            - 기존 상태를 고려할 필요 없이 통째로 서버를 교체
        - 생각보다 어렵고 느리고 특정 회사의 제품을 써야함
- 클라우드 이미지 단점
    - 어떻게 만들었는지 모름
    - 생각보다 쓰기 어려움
- Platform as a service
    - 서버 운영이 매우 복잡하고 어려움
    - 소스 코드만으로 배포가 가능함
    - 일반화된 프로비저닝으로 사용 가능
    - 스타트업도 초반에 Paas를 씀
    - 단점
        - 애플리케이션을 PaaS 방식에 맞게 작성
        - 서버에 대한 원격 접속 시스템을 제공하지 않음. 새로 띄우면 날라가니 s3를 써야함
        - 배포 패러다임을 바꿔야 함
        - 크론잡, 데이터 분석, 로그 분석, 애플리케이션 성능 모니터링, AB Test, 카나리 배포, 네트워크 스토리 설정을 PaaS 업체에서 제공하지 않으면 사용할 수 없음
- Docker
    - VM vs Docker
    - VM : OS 위에 OS가 또 올라감. Docker아 OS 위에 격리만 함
    - 도커의 특징
    - 1) 확장성
        - 도커가 설치되었으면 어디서든 컨테이너 실행 가능
        - 특정 회사/서비스에 종속적이지 않음
        - 개발 서버, 테스트 서버 쉽게 만들 수 있음
    - 2) 표준성
        - 배포 방식이 다 다르지만 run이라는 것 하나로 가능
    - 3) 이미지
    - 4) 설정
        - 환경 변수를 넣어서 관리
    - 5) 자원
        - 첨부 파일을 s3 같은 곳에 업로드
    - paas처럼 제한이 없지만 클라우드 이미지보다 관리 쉬움
    - 빌드 기록이 남음
    - 복잡한 기술 몰라도 됨
    - 오픈소스
    - 모든 것을 컨테이너로! ⇒ 도커이미지 있으면 사용
    - Blue - Green 배포
        - 애플리케이션을 업데이트하기 위해 컨테이너를 교체하는 방식 사용
        - proxy nginx를 사용해서 기존 컨테이너 보다가 새 컨테이너 바라보고 기존꺼 죽임
    - 서비스 디스커버리
        - 컨테이너를 마구 띄우다보니 IP가 뭔지 알아내서 자동으로 연결하고 싶어함
        - key value storage를 사용해 쓸 수 있음
    - 서버가 2대가 있고, 하나가 추가된 경우 기존 2개의 ip만 알고 있음.
        - key value에 web3에 새 ip와 포트 추가하면 nginx manager가 변경될 경우 이벤트로 감지하고 nginx 설정 파일 새로 만들고 재시작함
    - docker-gen
- 컨테이너 오케스트레이션
    - 여러 대의 서버와 여러 개의 서비스를 관리해주는 서비스
    - 스케줄링
        - 적당한 서버에 배포
    - 클러스터링
        - 여러 개의 서버를 하나의 서버처럼 사용
    - 서비스 디스커버리
        - key value에 저장할 필요 없이 바로 가져올 수 있음
    - 로깅, 모니터링으로 중앙에서 관리 가능
- docker swarm
    - docker에서 만든 컨테이너 오케스트레이션 도구
    - 업데이트가 잘 안되고 있음
- Kubernetes
    - 대규모에 적합, 다양한 생태계

---

### Docker Install
- json parser인 jq 설치

	```
	sudo apt install -y jq
	```

- Docker 설치
 
	```
	curl -fsSL https://get.docker.com/ | sudo sh
	sudo usermod -aG docker $USER
	
	sudo curl -L "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
	sudo chmod +x /usr/local/bin/docker-compose
	
	# check (re-login)
	docker version
	docker-compose version
	
	# reboot
	sudo reboot
	```

---

### Docker 기본 명령어
```docker run hello-world```, ```docker container run hello-world``` : 기존에 명령어를 전자같이 사용했으나, 17년 초중반부터 명령어가 후자같이 세분화되고 있습니다

### docker images 
- 현재 사용 가능한 image 목록을 출력. ```-a``` 옵션을 주면 모든 것을 보여줌

### docker ps
- 현재 사용 가능한 컨테이너 목록을 출력. ```-a``` 옵션을 주면 모든 것을 보여줌

### dokcer pull
- ```docker pull <아이디>/<이미지 이름>:<태그>```
- [docker hub](https://hub.docker.com/)에 있는 이미지를 가지고 옴

### docker 이미지로 container 실행하고 싶은 경우
- ```docker run -it <아이디>/<이미지 이름>:<태그> /bin/bash``` : ```-it``` 옵션과 함께 실행하면 실행한 명령이 Console에 붙어서 진행됩니다. i는 interactive, t는 tty를 의미합니다
- ```docker container run <container 이름> ls -l``` : ls -l 명령어를 실행하며 컨테이너를 실행해라!
- ```docker container run -it --name <container 별명> <image 이름> /bin/ash``` : ```--name```을 통해 container 이름을 부여합니다. container 이름을 부여하지 않으면 랜덤하게 생성됩니다

### exit된 docker container 접속
- ```docker container start <container ID>``` 

### docker container에서 명령어 실행
- ```docker container exec <container ID> ls``` : ```exec```은 container에서 명령어를 실행! ```exec```로 실행하면 ```ps auxf```에 살아있습니다

### docker diff <container ID>
- 컨테이너가 부모 이미지와 파일 변경 사항을 확인할 수 있는 명령어

### docker container commit
- ```docker commit <container ID> <아이디>/<이미지 이름>:<태그>```
- 새로운 도커 이미지 생성

### docker push image
- ```docker push <아이디>/\<이미지 이름\>:<태그>```
- docker hub에 이미지 업로드

### docker build
- ```docker build --tag <아이디>/\<이미지 이름\>:<태그> .```
- ```Dockerfile```에 있는 곳에서 명령어를 치면 파일에 나온대로 이미지 생성

---


### Dockerfile 형식
```
FROM ubuntu:14.04
MAINTAINER Foo Bar <foo@bar.com>

RUN apt-get update
RUN apt-get install -y nginx
RUN echo "\ndaemon off;" >> /etc/nginx/nginx.conf
RUN chown -R www-data:www-data /var/lib/nginx
EXPOSE 8080 

WORKDIR /etc/nginx

CMD ["nginx"]
COPY app.js
```

- ```FROM``` : 어떤 이미지를 기반으로 할지 설정
- ```MAINTAINER``` : 메인테이너 정보
- ```RUN``` : 쉘 스크립트 혹은 명령 실행
	- 이미지 생성 중에는 사용자 입력을 받을 수 있어서, apt-get install에서 ```-y``` 옵션을 꼭 넣어줘야 함. 안 넣으면 Fail
	- ```RUN```은 한줄이 이미지 하나로 빌드됨
- ```EXPOSE``` : 포트 노출
- ```CMD``` : 컨테이너가 시작되었을 때 실행할 실행 파일 또는 쉘 스크립트
- ```WORKDIR``` : CMD에서 설정한 실행 파일이 실행될 디렉터리 
- ```COPY``` : 말 그대로 복사

### Docker Container Stop
- ```docker container rm <container ID/Name>``` : container 삭제! 돌아가고 있는 container는 삭제가 되지 않습니다. 먼저 중지한 후, 삭제해야 합니다! 그러나 ```-f``` 조건을 주면 바로 삭제할 수 있습니다

### Delete all containers
- ```docker rm $(docker container ls -a -q)```
- ```docker container ls -a -q```는 container의 id만 출력합니다

### Delete all images
- ```docker rmi $(docker images -q)```
- ```docker image ls -q | xargs docker image rm``` : docker image들을 삭제! ```xargs```는 앞의 결과를 인자로 받습니다


### Docker port and volume
- 순서대로 따라해보면 됩니다!
- ```docker container run -d --name mynginx nginx``` : ```-d``` 조건을 줘서 데몬으로 실행
- ```docker containers ls``` : 현재 PORT가 나옵니다!
- ```docker container exec mynginx hostname -I``` : hostname-IP 출력하면 여러 IP가 나옵니다
- ```ifconfig docker0``` : docker가 네트워크도 격리된 환경을 만들었습니다!! IP가 유사하면 서로 접근할 수 있습니다
- ```curl http://<container IP>``` 하면 접근을 한 것을 알 수 있습니다
- ```docker container run -d --name mynginx2 -p 8080:80 nginx:alpine``` : alpine 리눅스 기반으로 호스트의 8080 포트를 컨테이너의 80으로 설정합니다
- ```docker container ls```를 하면 PORTS에 0.0.0.0:8080->80/tcp가 나타납니다! 컨테이너 안에있던 친구들이 외부로 노출된 것을 알 수 있습니다!
- ```cd ~/workspace```한 후, ```docker container cp mynginx2:/etc/nginx/conf.d/default.conf .``` 를 하면 컨테이너 안에 있던 친구들을 호스트로 꺼내왔습니다! 파일에 location을 보면 root : /usr/share/nginx/html이라고 나와있습니다
- ```echo "hello world!" > index.html```을 하신 후, ```docker container cp index.html mynginx2:/usr/share/nginx/html/index.html``` 하면 파일이 바뀜을 알 수 있습니다
- ```docker container run -d --name mynginx3 -p 8083:80 -v /home/ibmcloud/workspace:/usr/share/nginx/html nginx:alpine``` : workspace 디렉토리를 연결해서 실행해보니 8083도 위에서 만든 8080가 동일합니다! 여기서 ```-v``` 옵션을 줘서 다양하게 디렉터리를 공유할 수 있습니다!
- ```docker system prune``` : 멈춰있는 container를 모두 지우고, 태그안된 image 등을 깔끔하게 삭제합니다

---

### Build Docker image
```
git clone https://github.com/IBM/container-service-getting-started-wt
cd container-service-getting-started-wt/Lab\ 1
cat package.json
cat app.js
cat Dockerfile
```

- ```Dockerfile```은 이미지를 만드는 방법을 나타냅니다  

```
EXPOSE 8080 # 8080 포트 노출
CMD node app.js # app.js 실행!
COPY app.js
```

- ```app.js```를 마지막으로 하면 빌드 과정에서 달라집니다!(COPY package.json 같은 것들이 app.js에 종속됨) 이미지의 순서가 정말 중요합니다
- ```docker image build -t hello-world:1 .``` : docker 빌드
- ```docker image inspect hello-world:1``` : 도커 이미지를 검사합니다
- ```docker container run -d -p 8080:8080 --name hello-world-a hello-world:1``` : 방금 만든 이미지를 사용해 container를 실행합니다
- ```docker container logs <container name>``` : 로그를 확인할 수 있습니다!
- ```docker container exec hello-world-a hostname ip addr |grep inet``` , ```docker container exec hello-world-b hostname ip addr |grep inet``` 해보면 각자의 Network가 생성되어 있습니다! 
- ```docker container stop hello-world-a hello-world-b``` : c를 제외하고 삭제합니다
- ```ps auxf |grep app.js |grep node```해서 process 번호를 확인하고 ```sudo kill <PID>```를 하면 컨테이너가 종료됩니다. c는 kill signal을 주고 로그가 남습니다! 이 죽은 애들을 살려주는 곳이 쿠버네티스!


---

## Kubernetes
- Docker는 Host에 배포하는 방식
    - 보통 socket 파일로 되는데, 포트로 오픈
    - 내부망에 포트를 오픈하고 ip docker run
    - centurion이란 툴을 만듬
    - 2~3대라면 이정도도 괜찮음
- 그러나 서버가 많아지면?
    - 컨테이너 관리도구 춘추전국시대
    - 그러나 쿠버네티스가 평정
- Kubernetes Native Platform!
- Cloud Support
- 컨테이너 플랫폼
    - Knative : Serverless
    - Istio : Service Mesh
    - Kubeflow : Machine Learning
- 기본 기능
    - 상태 관리 : 상태를 선언하고 선언한 상태를 유지 / 노드가 죽거나 컨테이너 응답이 없을 경우 자동으로 복구
    - 스케줄링 : 클러스터의 여러 노드 중 조건에 맞는 노드를 찾아 컨테이너를 배치
    - 클러스터 : 가상 네트워크를 통해 하나의 서버에 있는 것처럼 통신
    - 서비스 디스커버리 : 서로 다른 서비스를 쉽게 찾고 통신할 수 있음. mysql라고 띄우면 mysql로 부를 수 있음
    - 리소스 모니터링 : cAdvisor를 통한 리소스 모니터링
    - 스케일링 : 리소스에 따라 자동으로 서비스를 조정함
    - RollOut/RollBack : 배포/롤백 및 버전 관리
- 빠른 업데이트
- 다양한 배포 방식
    - Daemon Set
    - Replica Set ⇒ deployment
    - Stateful sets
    - Job
    - Replication Controller ⇒ 거의 deprecated, replica set으로 변함
- Ingress 설정
    - domain path를 작성하면 쉽게 설정할 수 있음
- Namespace & Label
- RBAC
    - 내부적 롤을 서브젝트, 리소스, 오퍼레이션으로 관리함
- Kubernetes Object
    - The Illustrated Children’s Guide to Kubernetes
    - [https://www.cncf.io/the-childrens-illustrated-guide-to-kubernetes/](https://www.cncf.io/the-childrens-illustrated-guide-to-kubernetes/)
    - 컨테이너를 바로 쓰지 않고 Pod으로 감싸서 사용
    - ReplicaSet : Pod을 복제해서 쓰는 개념
    - Servuce : 일종의 로드 밸런서
    - Volume : 저장소
    - Namespace : 전체를 묶음
- Object Spec
    - YAML 파일을 사용
    - 원하는 상태를 다양한 오브젝트에 라벨을 붙여 정의(yaml)하고 사용
- Server - Agent
    - Master : API server
        - 확장성을 고려해 다양한 모듈이 기능별로 쪼개져 있음
        - 마스터가 죽으면 API server를 관리할 수 없어서 보통 3개 정도 사용함
        - 마스터가 죽어도 노드는 살아있음, update를 못할뿐
    - Node : 컨테이너가 돔, kubelet
        - 마스터 서버와 통신하며 필요한 pod을 생성하고 네트워크와 볼륨 설정
        - kubectl 라는 명령도구 사용함
- 단점
    - 복잡한 개념
        - 공부할게 너무 많고, 구성 관리하려면 알아야할 내용이 많아서 배보다 배꼽이 더 크다고 생각할 수 있음
    - 복잡한 설치
        - 클라우드를 이용합시다
    - 무거운 환경
        - 아무것도 안띄어져 있어도 기본 리소스 사용이 많은편
    - 복잡한 설정파일
        - 모든 설정이 마이크로서비스 스럽게 나뉘어져 있어서 길고 복잡함
- 앱을 만들면 Container로 실행합니다. 이걸 쿠버네티스에서 ```pod``` (포드, 팟)이라고 부릅니다. 각각의 pod는 1개의 IP를 가집니다! 모이면 리플리카셋이 되고, 이것들을 deployment라고 배포합니다. 이런 친구들은 워커 노드에서 돌아갑니다. 
	- Master Node : 스케쥴링
	- Worker Node : 실제 일을 하는 친구들
- 쿠버네티스는 마스터를 다 관리해줍니다! 요새 IBM, GCP 등에 마스터를 알아서 관리해주고 워커만 저희가 보면 됩니다
- 중요한 개념
    - 내부적 엔진의 역할은 딱 1개
        - 현재 상태(current state)와 관리자가 원하는 상태를 계속 비교해서(diff) 원하는 상태로 계속 바꿔줌(act)
        - storage 체크하는 애는 storaeg만, 네트워크 체크하는 애는 네트워크만 계속 체크함
- 쿠버네티스는 컨테이너를 pod이라는 개념으로 감싸서 씀
    - 개발자 입장에서 짜는 프로그램을 만든다 하면 if문을 써서 어떤 노드가 있고, 빈 공간이 있는지 체크해서 cpu 메모리가 적은 곳에 할당한다 이럴텐데
    - 스토리
        - 쿠버네티스는 pod을 생성 ⇒ 생성 요청된 것을 감시하다가 있으면 할당 ⇒ 실제 노드 서버에 컨테이너를 띄우는 할당이 아니라 이 pod은 3번 노드에 띄우겠다 정보만 올림 ⇒ 할당은 되었지만 실행은 안됨 ⇒ 3번 노드 입장에서 자신에게 할당되었지만 실행 안된거가 있는지 확인 ⇒ 있다면 도커로 띄우고 ⇒ Pod의 상태를 알려주고 ⇒ 아 이제 다 할당이 되었구나! 이런 흐름
    - 5개의 모듈이 분리가 되서 사용됨
        - 결국 모듈이 많지만 같은 메커니즘으로 운영됨
- cloud native 사이트에 가면 소속된 레플리케이션이 많이 있음
    - cncf cloud native interactive landscape
    - [https://landscape.cncf.io/](https://landscape.cncf.io/)
    - 이 레플리케이션이 쿠버네티스와 밀접한 관계를 가짐. 쿠버네티스 쓰면 레플리케이션이 잘 돌아감
- 개발자가 기능 개발을 할 경우
    - 브랜치 따고 테스트하고 자동화를 함
    - 젠킨스가 빌드를 해서 이미지 올리고, 빌드한 후 또다른 git branch에 배포하라는 명령어를 push함
    - 소스 ⇒ 빌드 ⇒ 쿠버 클러스터에 이 브런치를 띄워줘라는 push
        - 쿠버네티스는 상태를 만들도록 노력함
    - application에 feature가 3개 있으면 3개가 모두 뜸
        - 미묘한 차이 : 띄워라고 명령을 보내는게 아니라 이 3개의 컨테이너가 있으면 좋겠어! 라고 싱크함
        
### 쿠버네티스의 장점
- 스케쥴링을 잘해줌
- 죽어도 스스로 잘 살려줌
- 확장성
- 로드 밸런싱
- 롤아웃/롤백이 자동으로 진행
- 설정을 관리

---

### Service
- 서비스의 종류
    - [https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0](https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0)
    - 총 4개
    - 1) ClusterIP
        - 쿠버네티스 클러스터에 팟이 3개 있고, 팟을 통하기 위해 서비스 하나가 있음
        - 이 서비스가 클러스터 IP임
        - redis를 만들고 두개의 팟을 나누고, 레디스 접근하기 위해 서비스 오픈이 되어야 함
        - 여태한 것은 컨테이너 띄우기만 했지 접근을 할 수 없음
        - 컨테이너 접근하기 위해 ClusterIP를 만들어야 함
        - 내부에서 사용하는 작은 로드 밸런서라고 생각하면 됨
        - Pod을 하나만 만들어도 Service가 하나 생성됨 ⇒ IP를 가지고 있음
    - 2) NodePort
        - 이 클러스터 IP는 클러스터에서 사용하는 대표 IP인데 외부에서 사용할 수 없고, 내부 통신만 가능
        - 노드포트로 오픈하면 외부에서 오픈할 수 있는 것이 모든 노드에 열림. 하나의 노드에 연결하면 클러스터 IP에 연결됨
        - 모든 노드에 열리다보니 더 좋은 것이 로드밸런서에 더 좋음
    - 3) LoadBalancer
        - 클라우드에서만 존재하는 개념
        - 회사에 서버 3대를 띄우고 쿠버 설정을 하면 로드밸런서 설정을 해야함
        - 이게 엄청 추상적 개념인데, 일반적 조립 서버에선 로드 밸런서가 없음
        - 모든 노드의 포트가 아니라 로드밸런서를 통해 클러스터 IP로 접근
        - 이 친구의 단점은 바라보는 서비스는 1대임. 서비스 10개 띄우면 10개를 봐야함 ⇒ 이걸 위해 Ingress
    - 4) Ingress
        - nginx가 있어서 다 80 포트로 받지만 path에 따라 서비스를 분기할 수 있음

---

### Install kubectl cli
```	
sudo apt update && sudo apt install -y apt-transport-https	
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -	
echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" |sudo tee /etc/apt/sources.list.d/kubernetes.list	
sudo apt update	
sudo apt install -y kubectl	
```

- kubectl을 설치해야 합니다!(단 처음 설치하면 tab이 안됨)
- ```kubectl version``` : 쿠버네티스 버전 명시(처음엔 Client만 나올겁니다)
- ```source <(kubectl completion bash)``` : 자동완성 가능하도록 설정
- ```kubectl completion bash |sudo tee /etc/bash_completion.d/kubectl``` : 종료해도 자동완성 가능하도록 설정!	


---

### Deploying apps into clusters
- [문서](http://docker-dwmeetup.mybluemix.net/k8s1.html)

- ```bx login``` : ```au-syd``` 선택!   
- ```bx cr region-set``` : 개인 저장소를 사용하기 위한 명령어. container repository의 약자( ```cs```) ```ap-south``` 선택!  
- ```bx cr namespace-list``` : 이름을 출력!  
- ```export MY_REGISTRY_NAMESPACE=<namespace>``` : 환경설정 추가
- ```bx cr namespace-add $MY_REGISTRY_NAMESPACE``` : 네임스페이스 추가!!
- ```bx cr login``` : ```cat ~/.docker/config.json```을 사용해 로그인합니다
- ```docker image ls``` : 현재 로컬에 있는 docker 이미지 출력
- ```docker image tag hello-world:1 registry.au-syd.bluemix.net/$MY_REGISTRY_NAMESPACE/hello-world:1``` : 같은 Image ID를 가지는 image가 생성됩니다
- ```docker image push registry.au-syd.bluemix.net/$MY_REGISTRY_NAMESPAC/hello-world:1``` : 이미지가 IBM 개인 저장소로 push!
- ```bx cr image-list``` : 개인 저장소에 있는 image를 출력합니다


```
docker image tag hello-world:2 registry.au-syd.bluemix.net/$MY_REGISTRY_NAMESPACE/hello-world:2
docker image push registry.au-syd.bluemix.net/$MY_REGISTRY_NAMESPAC/hello-world:2
``` 
hello-world:2도 올려줍니다!


- ```bx cs clusters``` : 명령어가 ```cs```로 바뀌었습니다! container service
- ```export MY_CLUSTER_NAME=<cluster 이름>```
- ```bx cs workers $MY_CLUSTER_NAME``` : worker들의 설정을 보여줍니다
- ```bx cs cluster-config $MY_CLUSTER_NAME``` : export KUBECONFIG ~를 그대로 복사해서 터미널에서 실행! 해당 config에 쿠버네티스 정보가 나타납니다
- ```kubectl version``` : 이제 Server도 출력됩니다!
- ```kubectl version --short``` : 짧게 Version 출력


### Worker 올리는 부분
- ```kubectl run hello-world-deployment --image=registry.au-syd.bluemix.net/$MY_REGISTRY_NAMESPACE/hello-world:1``` : 해당 image를 가지고 와서 쿠버네티스 실행!
- ```kubectl get pod``` : pod 생성되었는지 확인
- ```kubectl get deployment``` : 방금 deploy한 친구의 정보가 나옵니다. deployment 뒤에 이름을 붙여서 볼수도 있음
- ```kubectl get deployment hello-world-deployment -o yaml``` : ```-o yaml```을 사용해 더 상세한 데이터를 보여줍니다
	- replicas : 1개로 되어있음
	- rollingUpdate : deployment 정책! 1개씩만 올리고 내림
- ```kubectl describe deployment hello-world-deployment``` : 현재 상태를 보여줍니다
- ```kubectl get replicaset``` : replicaset이 나타납니다
- ```kubectl get replicaset -o yaml``` : 특정 replicaset 정보를 가지고 옵니다(여기서 pod 관리 정책이 있음)
- ```kubectl get pod``` : pod이 생겼습니다!!
- ```kubectl get pod -o yaml``` : pod은 특정 ip를 가지고 있습니다! hostIP도 나타나며 다른 정보들도 포함되어 있습니다
	
### pod 외부에서 확인하고 싶을 경우	
- 외부에서 확인하기 위해선 cluster ip, node port 열어주기 등의 방법이 필요합니다. 여기선 node port를 열어보겠습니다
- ```kubectl expose deployment/hello-world-deployment --type=NodePort --port=8080 --name=hello-world-service --target-port=8080
``` 
- ```kubectl describe service hello-world-service``` : 해당 서비스 설명 출력됩니다. 8080:30384/TCP가 되어있습니다!
- ```bx cs workers $MY_CLUSTER_NAME``` : 해당 IP를 확인한 후, nodePORT를 연결하면 접속이 됩니다!
- ```kubectl edit deployment hello-world-deployment``` : deployment를 수정합니다! replicas의 수를 수정할 수 있습니다
- ```kubectl scale --replicas=5 deployment hello-world-deployment``` : replicas 개수를 조절할 수 있습니다
- ```kubectl get pod``` : 하면 개수가 증가됨을 볼 수 있습니다
- ```kubectl rollout status deployment hello-world-deployment``` : rollout 상태를 보여줍니다
- ```kubectl describe replicaset```를 입력하면 4개가 추가됨을 볼 수 있습니다
- ```watch -n 1 curl http://워커아이피:노드포트/``` : 1초마다 해당 내용이 가져오는데, 어떤 팟을 쓰는지 볼 수 있습니다


### Version 오류 발생시 대처가 어떻게 되는지?
- ```export MY_REGISTRY_NAMESPACE=<ID>```
- ```kubectl set image deployment hello-world-deployment hello-world-deployment=registry.au-syd.bluemix.net/$MY_REGISTRY_NAMESPACE/hello-world:3``` : 아직 3을 안만들었지만 생성됨
- ```kubectl rollout status deployment hello-world-deployment``` : 2개가 업데이트되고 진행이 안됩니다
- ```kubectl describe pod``` : 상태가 다릅니다
- ```kubectl get pod``` : 이미지를 PULL하다 에러가 뜸!
- ```kubectl rollout undo deployment hello-world-deployment``` : 롤백!!!!!
- ```kubectl get pod``` : 멀쩡한 것을 볼 수 있습니다


---

## Reference
- [서비큐라님 블로그](https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html) 
- [도커 튜토리얼 : 깐 김에 배포까지](http://blog.nacyot.com/articles/2014-01-27-easy-deploy-with-docker/)
- [Docker에서 apt-get update가 실패할 때](http://interp.blog/docker%EC%97%90%EC%84%9C-apt-get-update%EA%B0%80-%EC%8B%A4%ED%8C%A8%ED%95%A0-%EB%95%8C/)
- [Docker 컨테이너 이미지 생성](http://forum.falinux.com/zbxe/index.php?document_srl=806457&mid=lecture_tip)
- [Image doe not contain 'sudo'](https://github.com/tianon/docker-brew-ubuntu-core/issues/48)
- [IBM developerWorks 밋업, 도커와 쿠버네티스, 두 마리 토끼를 잡자!](https://www.facebook.com/groups/developerWorksKRUG/permalink/2020459798166854/)
- [IBM Document](http://docker-dwmeetup.mybluemix.net/docker1.html)


