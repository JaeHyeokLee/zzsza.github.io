---
layout: post
title:  "Machine Learning의 Feature Store란?"
subtitle: "머신러닝의 Feature Store"
categories: mlops
tags: feature
comments: true
---


- 머신러닝의 Feature Store에 대해 작성한 글입니다
	- Feature Store가 왜 필요한지?와 어떤 종류들이 있는지? 등에 대해 작성했습니다


---


### 머신러닝의 모델링 과정
- 머신러닝 모델링 업무의 큰 흐름은 다음과 같음
	- 데이터 적재
	- 데이터 EDA 및 분석
	- Feature Engineering
	- 모델링(모델 선정, 하이퍼 파라미터 튜닝, metric 선정 과정 포함)
	- Test Set에 성능 개선되었는지 확인

- 모델이 완성되면, 그 후에 실제 서비스에 활용하기 위해 아래와 같은 과정을 거침(Production, Serving 과정)
	- 실시간 데이터 적재 확인
	- 실시간 데이터에 기반한 Feature Engineering
	- 모델 학습
	- 필요시 모델 업데이트
	- Inference

<br />

---


### Production시 발생할 수 있는 문제
- 1) 데이터 스트리밍 파이프라인 존재 유무
	- 데이터 ETL 파이프라인은 초반엔 배치성으로 생성해 관리하는 경우가 있음(더 쉽고, 빠르게 가능) 
	- 이 경우, 모델을 Production화하기 위해 데이터를 실시간으로 적재해야 함
	- Kafka, Redis, GCP Pub/Sub, REST API 등을 활용해 데이터를 적재
	- 단, 예측하려는 문제에 따라 실시간보다 배치성으로 예측해도 괜찮은 경우가 있음
- 2) Feature Engineering 소요되는 시간
	- 실시간으로 생성되는 데이터를 기반으로 Feature Engineering 수행
	- Spark나 BigQuery 같은 SQL 기반으로 전처리할 수도 있고, Python의 Pandas 등을 사용할 수도 있음
	- 그러나 Feature Engneering의 연산이 복잡한 경우, Feature를 생성하는 시간이 오래 걸릴 수 있음
- 3) 오프라인과 온라인에서 사용한 Feature의 차이
	- 오프라인 : 실시간이 아닌 데이터를 사용하는 환경(주로 배치성)
	- 온라인 : 실시간으로 데이터가 스트리밍되는 환경
	- 오프라인은 주로 배치성으로 적재되기 때문에, 그 과정에서 미리 복잡한 연산을 해서 저장할 수 있음. 그러나 온라인 적재는 복잡한 연산을 하지 못하거나(혹은 개발 리소스의 부족으로) 따로 처리해야할 수 있음
	- 같은 로직으로 처리하려 했으나, 처리하는 시간에 따라 값이 다르게 계산되는 경우가 존재함 
- 4) 데이터 프로토콜의 부재
	- 데이터를 실시간으로 적재하는 부분은 데이터 엔지니어 직군이, 모델링을 위해 데이터 전처리하는 부분은 데이터 사이언티스트(혹은 머신러닝 엔지니어)가 진행하는데, 두 조직의 프로토콜이 없는 경우도 있음
- 5) 서비스에서 많은 머신러닝 모델이 생기는 경우
	- 머신러닝 모델이 소수인 경우(1-2개) 단일 모델을 위해 시스템을 구축하는 것은 비효율적이라 판단할 수 있음
	- 하지만 모델이 많아지고, 동일한 Feature를 사용할 경우엔 각각의 모델링 과정에서 Feature Engineering을 진행하면 모델의 개수만큼 연산을 진행해야 함
	- 즉, 재사용성이 떨어짐
- 6) 과거 Feature를 사용해야 하는 경우
	- 보통 시간과 관련된 Feature들이 많은데, 이 Feature들은 시간이 지나며 점점 값이 바뀜(1월 1일 시점에 최근 1시간 전 데이터와 1월 5일 시점에 최근 1시간 전 데이터는 다름)
	- 이럴 경우 index가 있거나, Backfill 기능이 있어야 함
		- 혹시 잠시 Feature 관련 코드가 장애날 경우에 과거 값을 계산해야할 수 있음
		- Backfill : 간단히 말해서 예전 데이터를 계산
	

<br />

---



### Feature Store
- 발생할 수 있는 문제들을 위해 Feature Store를 구축
- Feature Store Layer를 추가한다고 표현함
	- Feature Store의 오픈소스는 Hopsworks와 feast가 존재
- 데이터를 Train, Serving 할 때 모두 사용할 수 있도록 통합해서 저장함


<br />

---


### Feature Store 예시
- 다양한 회사들이 자신들이 구축한 Feature Store에 대해 발표함
	- [featurestore.org](http://featurestore.org/)에 다양한 Feature Store에 대한 영상들이 있음
	- <img src="https://www.dropbox.com/s/pv1z1likpl2rred/Screenshot%202020-02-02%2021.04.36.png?raw=1">
	- 많은 회사들이 비슷한 개념으로 개발함
- Hopsworks의 Feature Store
	- [Document](https://www.logicalclocks.com/featurestorepage)
	- [WHITE PAPER](https://uploads-ssl.webflow.com/5c9b9758feba5a6f9e8a6dda/5d92b35b15962a46c7ce9c5f_feature%20store%20whitepaper%201-0.pdf)
	- <img src="https://www.dropbox.com/s/cpd34cfddgof93j/Screenshot%202020-02-02%2020.15.05.png?raw=1">
	- White Paper(백서)를 참고하면 어떤 구조로 만들었는지 나옴
	- <img src="https://www.dropbox.com/s/89pqyiyholerbpq/Screenshot%202020-02-02%2020.10.16.png?raw=1">
	- Offline Features와 Online Features를 저장함
	- <img src="https://www.dropbox.com/s/yduxbp05nyuljlm/Screenshot%202020-02-02%2020.16.16.png?raw=1">
	- Hopsworks의 End to End System
- Gojek의 Feast
	- [Github](https://github.com/gojek/feast)
	- <img src="https://www.dropbox.com/s/vmcs1codtvxoncm/Screenshot%202020-02-02%2020.17.58.png?raw=1">
	- Offline은 BigQuery에 저장하고, Online은 BigTable에 저장함
	- Google Cloud Platform과 공동으로 개발한 프로그램
		- [Introducing Feast: an open source feature store for machine learning](https://cloud.google.com/blog/products/ai-machine-learning/introducing-feast-an-open-source-feature-store-for-machine-learning) 글에 나와있음
- Uber의 Michelangelo
	- 우버의 머신러닝 플랫폼으로 Feature Store 및 다양한 기능이 제공됨
	- Uber Techblog [Meet Michelangelo: Uber’s Machine Learning Platform](https://eng.uber.com/michelangelo/), [Evolving Michelangelo Model Representation for Flexibility at Scale](https://eng.uber.com/michelangelo-model-representation/)에 나와있음
	- <img src="https://www.dropbox.com/s/dumuvgwgcl1u4f1/Screenshot%202020-02-02%2020.21.04.png?raw=1">
- AirBnB의 Zipline
	- [Zipline: Airbnb’s Machine Learning Data Management Platform](https://databricks.com/session/zipline-airbnbs-machine-learning-data-management-platform)
	- <img src="https://www.dropbox.com/s/e7qc7yqy8htub6f/Screenshot%202020-02-02%2020.25.46.png?raw=1">
- Netflix의 Metaflow
	- [Github](https://github.com/Netflix/metaflow)
	- <img src="https://www.dropbox.com/s/qu09q2o1wts59li/Screenshot%202020-02-02%2020.28.53.png?raw=1">
	- <img src="https://www.dropbox.com/s/d87xc5zn2xe8a37/Screenshot%202020-02-02%2020.29.16.png?raw=1">
	- Airflow의 Dependency를 정의하는 것을 코드로 진행하는듯
	- <img src="https://www.dropbox.com/s/i5y6j70ldyijj0z/Screenshot%202020-02-02%2020.39.18.png?raw=1">
- [featurestore.org](https://featurestore.org)에 나와있는 Feature Store Comparison 정리한 표
	- <img src="https://www.dropbox.com/s/qtencu283usl0m6/Screenshot%202020-02-02%2021.03.27.png?raw=1">

<br />

---


### Hopsworks
- [Homepage](https://www.logicalclocks.com/)
	- Hopsworks는 전체 프레임워크 단위고, Feature Store가 그 중에 한 부분
- [링크](https://hopsworks.readthedocs.io/en/latest/getting_started/gettingstarted.html)에 AWS, GCP, 단일 인스턴스에 설치하는 방법에 대해 나와있음
- Feature Store
	- Feature Store와 함께 프로젝트 생성
		- <img src="https://www.dropbox.com/s/jmhbzgfmgwhht7t/Screenshot%202020-02-02%2020.46.02.png?raw=1">
	- 등록된 Feature Groups을 볼 수 있음. Cached, On Demand 타입인지, Version, 분포(파란색 버튼) 등을 볼 수 있음
		- <img src="https://www.dropbox.com/s/st7qg91mvi33h29/Screenshot%202020-02-02%2020.48.22.png?raw=1">
	- Feature Unit Testing Page
		- <img src="https://www.dropbox.com/s/foc0t1nqfo6ufqj/Screenshot%202020-02-02%2020.51.32.png?raw=1">
		- <img src="https://www.dropbox.com/s/291dsicqwclvogf/Screenshot%202020-02-02%2020.52.53.png?raw=1">
		- Feature의 Constraint를 추가할 수 있음. 이 조건들로 데이터 Validation을 실행함
- Feature 추가하기

	```
	from hops import featurestore
	featurestore.insert_into_featuregroup(features_df, featuregroup_name)
	```

- Feature 가져오기

	```
	from hops import featurestore
	features_df = featurestore.get_features(["average_attendance", "average_player_age"])
	```

- Airflow를 사용해 Validation 후, 작업하기도 함
	- [Github](https://github.com/logicalclocks/hops-examples/tree/master/airflow) 참고

	```
	validation = HopsworksLaunchOperator(dag=dag,
	                                     project_name=PROJECT_NAME,
	                                     # Arbitrary task name
	                                     task_id="validation_job",
	                                     job_name=VALIDATION_JOB_NAME)
		
	# Fetch validation result
	result = HopsworksFeatureValidationResult(dag=dag,
	                                          project_name=PROJECT_NAME,
	                                          # Arbitrary task name
	                                          task_id="parse_validation",
	                                          feature_group_name=FEATURE_GROUP_NAME)
		
	# Run first the validation job and then evaluate the result
	validation >> result
	```


- 그 외에도 hopsworks는 데이터 공유하기, 노트북 환경 생성, 제플린, 텐서보드 등 다양한 기능을 제공함
	- <img src="https://www.dropbox.com/s/z7jhxbr23i3vftc/Screenshot%202020-02-02%2020.56.19.png?raw=1">


<br />

---


### Gojek의 feast
- [공식 문서](https://docs.feast.dev/)
- 사용 예시
	- <img src="https://www.dropbox.com/s/d2p211o55bky7v3/Screenshot%202020-02-02%2020.57.42.png?raw=1">
- <img src="https://www.dropbox.com/s/qefoqo5f0ksnj16/Screenshot%202020-02-02%2020.58.42.png?raw=1">
- 설치는 Docker Composer, Google Kubernetes Engine(GKE)에서 하는 방법에 대해 [공식 문서](https://docs.feast.dev/installing-feast/overview)에 나와있음
	- BigQuery, Dataflow, Cloud Storage 등 GCP 친화적으로 보임

<br />

---


### 어떻게 해야할까?
- 현재 주어진 요구사항에 따라 다른 접근이 필요
- 처음부터 Feature Store를 구축하면 좋으나, 지속적으로 모델링하며 어떤 요구사항이 있을지 알 수 없음
- 따라서 처음엔 나이브하게 시작하는 것도 좋다고 생각
	- 자주 사용하는 Feature를 Airflow 등에서 스케쥴링
	- 1시간별 수요를 Table로 N분 단위로 스케줄링해서 특정 Table에 저장
	- 그러다가 점점 모델이 많아질 때 고도화하면 좋을듯
- Feature Store도 중요하지만, Data가 들어올 때 Validation 체크하는 것도 매우 중요함
- Hopsworks를 직접 실행시켜서 아이디어를 얻은 후, 직접 개발하는 것이 더 좋을 수 있음(Hopsworks는 엄청 큰데, 모두 회사에 필요할까?란 고민이 필요)
- 다음 글은 Hopsworks를 직접 띄워서, 어떤 구성 요소가 있고 어떻게 활용할지에 대해 작성하겠습니다 :)


---


### Reference
- [featurestore.org](http://featurestore.org/)
- [The Feature Store - Jim Dowling](https://youtu.be/EI2QisCvEM4) : Hopsworks에 대한 영상
- [Accelerating Machine Learning with the Feature Store Service](https://technology.condenast.com/story/accelerating-machine-learning-with-the-feature-store-service)
- [Introducing Feast: an open source feature store for machine learning](https://cloud.google.com/blog/products/ai-machine-learning/introducing-feast-an-open-source-feature-store-for-machine-learning)
- [Feature Stores: Components of a Data Science Factory](https://towardsdatascience.com/feature-stores-components-of-a-data-science-factory-f0f1f73d39b8)
- [Feature Store: The missing data layer in ML pipelines?](https://www.logicalclocks.com/blog/feature-store-the-missing-data-layer-in-ml-pipelines)
- [Rethinking Feature Stores](https://medium.com/@changshe/rethinking-feature-stores-74963c2596f0)
-[gojek feast](https://github.com/gojek/feast)

