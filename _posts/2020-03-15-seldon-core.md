---
layout: post
title:  "Seldon Core의 모든 것"
subtitle: "Seldon Core의 모든 것"
categories: mlops
tags: serving
comments: true
---

- Seldon Core의 모든 것

- Alibi + Seldon Core + BigQuery

---


### Seldon Core
- Seldon Core는 쿠버네티스 클러스터에 머신러닝 모델을 배포하기 위한 오픈소스 플랫폼
	- 클라우드 또는 온프레미스에 머신러닝 모델 배포
	- 실행 중인 머신러닝 모델에 대한 Metric을 수집
	- 여러 구성 요소로 구성된 강력한 Inference Graph 생성
	- ML Toolkit을 사용해 빌드된 모델에 일관된 서빙 Layer를 제공함
- 시작하기
	- [Simple Model Serving](https://docs.seldon.io/projects/seldon-core/en/latest/servers/overview.html)
	- [Advanced Custom Serving](https://docs.seldon.io/projects/seldon-core/en/latest/workflow/README.html)

- Simple Model Serving
	- 쿠버네티스 패키지 매니징 도구인 Helm을 설치
	- Prepackaged Model Server
		- sklearn 서버, xgboost 서버, 텐서플로우 서빙, mlflow 서버
		- 그냥 modlUri에 저장하고 implementation 지정하면 됨

- Workflow
	- 설치
	- Model 래핑
	- 인퍼런스 그래프 생성
	- 모델 배포
	- 모델 테스트
	- Serve requests
	- 트러블슈팅
	- 사용 레포트
	- 업그레이드
- 설치
	- 쿠버네티스 클러스터(>=1.12) 필요
	- Helm 또는 Kustomize 지원
	- Seldon Core Helm Install
		```
		kubectl create namespace seldon-system
		```
	











### Reference
- [Seldon Core live deployment demo! AB test and multi-armed bandit deployment](https://www.youtube.com/watch?v=BG3_bJr98BE)
- [Manage ML Deployments Like A Boss: Deploy Your First AB Test With Sklearn, Kubernetes and Seldon-core using Only Your Web Browser & Google Cloud](https://medium.com/analytics-vidhya/manage-ml-deployments-like-a-boss-deploy-your-first-ab-test-with-sklearn-kubernetes-and-b10ae0819dfe)
- [Polyaxon, Argo and Seldon for Model Training, Package and Deployment in Kubernetes](https://medium.com/analytics-vidhya/polyaxon-argo-and-seldon-for-model-training-package-and-deployment-in-kubernetes-fa089ba7d60b)