---
layout: post
title:  "Terraform 소개 및 정리"
subtitle: "Terraform 소개 및 정리"
categories: development
tags: devops
comments: true
---
	 
- 코드로 인프라를 관리할 수 있는 테라폼에 대해 정리한 글입니다

---

## 코드로서의 인프라스트럭처
- "Write, Plan, and Create Infrastructure as Code"
- 인프라스트럭처를 코드로서 작성, 계획, 생성하는 도구
- 하나의 패러다임
	- Infrastructure as Code
- 인프라스트럭처의 정의 
	- 리소스들의 집합
		- 물리적 환경 : 네트워크 장비, 서버 컴퓨터 등
		- 클라우드 : 가상 컴퓨팅 자원, 매니지드 서비스 등
	- 코드로서 인프라스트럭처 IaC 도구
		- 리소스를 코드로 관리하는 도구
- 관리하는 도구들
	- 서버 스크립팅(Server Scripting)
		- 서버에서 스크립트 실행
	- 설정 관리(Configurations Management)
		- 대상 : 서버의 상태 
	- 리소스 선언 도구(Resource Management)
		- 대상 : 리소스를 정의 

### 설정 관리
- 셰프, 퍼핏, 앤서블 등
	- DSL 코드로 서버의 상태를 정의하고 관리
- 서버 명령어보다 추상회되어 있음
	- 다수의 서버와 다양한 환경을 지원
	- 완벽하지는 않지만 서버가 우리가 정의한대로 되도록 멱등성을 지원
- Chef Solo 입문 : 인프라스트럭처 자동화 프레임워크라고 되어있음


### 일반적인 소프트웨어 개발
- 코드 작성
- Git 저장소로 변경 관리
- Github로 협업
	- 이슈 관리
	- 풀 리퀘스트와 코드 리뷰
	- 지속적 통합

### 인프라스트럭처가 코드가 되면
- 소프트웨어처럼 개발할 수 있음!


### 리소스 선언 도구
- 테라폼 Terraform, 클라우드 포메이션 등
- DSL 코드로 리소스를 선언하고 관리
	- 주로 클라우드의 가상화된 리소스를 관리
	- 물리적 장비는 대상이 아님
- 코드가 곧 아키텍처  


## 테라폼
- HashiCrop에서 만든 오픈소스 인프라 관리 도구
- HCL를 사용해 인프라스트럭처를 코드로 정의
- 다양한 프로바이더를 지원
	- AWS, GCP, Azure 
- 커맨드라인 인터페이스
	- 의존성 표현도 가능
	- 어떤 것이 먼저 필요한지 테라폼이 정의해서 생성 
- 엔터프라이즈 서비스 제공

### HCL과 테라폼 기초
- HashiCorp Configurations Language
- 하시코프에서 만든 설정 전용 DSL
	- 컨설, 테라폼에서 지원
- JSON과 호환
- 변수, 조건문, 인터폴레이션 등 지원
- HCL: 리소스
	
	```
	resource "aws_instance" "bastion" {
		ami = "${ var.ami_id }"
		instance_type = "${ var.type }"
	}
	``` 

	- aws_instance : 리소스 타입
	- bastion : 리소스 이름(테라폼 내부에서 참조하기 위해 사용)
	- ami, instance_type : 속성 이름
	- ${ var. ~} : 속성 값 
	- 테라폼은 리소스를 계속 정의

### 테라폼 Workflow
- Write -> Plan -> Create(Apply)
- Write
	
	```
	resource "aws_instance" "bastion" {
		ami = "${ var.ami_id }"
		instance_type = "${ var.type }"
	}
	``` 
	
	- 로컬에 aws_instance.bastion이 있고 AWS 계정엔 아무것도 없음

- Plan
	- aws_instance.bastion이 생성 계획 정의

	```
	terraform plan
	```

- Create
	- apply를 통해 AWS에 리소스를 생성  		
- 상태를 terraform.tfstate에 저장
- 용어
	- 계획=plan
	- 적용=apply
	- 상태=tfstate
	

### 테라폼 삽질기
- 테라포밍
	- 테라폼을 적용하는 두 가지 방법
		- 새로 만드는 리소스를 테라폼으로 작성
		- 운영 중인 클라우드 자원을 테라폼 코드로 작성
- 기존의 리소스 임포트
	- 테라폼 import 명령어
		- 현재 상태만을 tfstate에 임포트
		- tf 파일은 생성되지 않음
	- tfstate에만 자원이 있다면 삭제하려고 함(위험!!!)
		- 로컬에 없으면(instance.tf) 없는걸 aws상에 동기화하려하니.. 삭제가 되는꼴 
		- 기존에 사용중인 인프라 가져오기 참고	   
- 루비의 terraforming
	- AWS 리소스를 tf / tfstate로 임포트 지원
		- 리소스 타입별로 통째로 임포트
		- 모든 리소스 타입을 지원하진 않음
	- 임포트 결과가 완벽하지 않음
		- 테스트 및 수작업으로 보정
		- 의존 관계를 표현하지 못함
- 구조
	- 서비스 단위로 분리하고, 모듈로 서비스 리소스 분리
- 프로젝트 별 리소스 공유
	- 거대한 프로젝트를 만들면 서로 리소스를 공유할 수 있음
	- 프로젝트간에는 리소스를 직접 공유할 수는 없고, 간접적으론 가능      


- 더 궁금하면 [테라폼 기초 튜토리얼](https://www.44bits.io/ko/post/terraform_introduction_infrastrucute_as_code) 참고 
	
### Reference
- [테라폼 도입기 클라우드 인프라스트럭처 코드로 재정의하기 - 김대권
](https://youtu.be/V5zW9MgvRi0)
- [테라폼 기초 튜토리얼](https://www.44bits.io/ko/post/terraform_introduction_infrastrucute_as_code)
- [Google Cloud Platform에 Terraform 설정하기](https://blog.outsider.ne.kr/1366)
