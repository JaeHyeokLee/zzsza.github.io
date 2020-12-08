---
layout: post
title:  "Google Cloud Next 19 정리 및 후기"
subtitle:  "Google Cloud Next 19 정리 및 후기"
categories: gcp
tags: basic
comments: true
---

- Cloud Next 19의 영상들을 보고 정리한 글입니다
	- 다음엔 저도 현장에 가서 이런 내용들을 듣고 싶네요!(잘 정리하고 전파할 수 있는데..!)
- 정리한 영상
	- ML Ops Best Practices on Google Cloud
	- Accelerating Machine Learning App Development with Kubeflow Pipelines
	- What's New with BigQuery ML and Using it to Assess Data Quality
	- Data Processing in Google Cloud: Hadoop, Spark, and Dataflow
	- Rethinking Business: Data Analytics With Google Cloud
	- AI Hub: The One Place for Everything AI

	
---	
	
## 메모
- 영상 볼 수 있는 곳
    - [https://cloud.withgoogle.com/next/sf/next-onair](https://cloud.withgoogle.com/next/sf/next-onair)
    - 다양한 분야 중 Analyze & Learn archive를 중점적으로 봄
- Google Cloud 블로그 글
	- [All 29 AI announcements from Google Next ‘19: the smartest laundry list](https://cloud.google.com/blog/products/ai-machine-learning/all-ai-announcements-from-google-next19-the-smartest-laundry-list) : AI/인공지능 기술에 대한 총 정리 글
	- [News to build on: 122+ announcements from Google Cloud Next ‘19](https://cloud.google.com/blog/topics/inside-google-cloud/100-plus-announcements-from-google-cloud-next19) : Cloud Next 19에서 나온 모든 새로운 것 정리
	- [Cloud Next 1일차 정리](https://cloud.google.com/blog/topics/inside-google-cloud/next19-recap-day1)
	- [Cloud Next 2일차 정리](https://cloud.google.com/blog/topics/inside-google-cloud/day-2-next-19-working-smarter-better-and-more-securely-cloud)
	- [Cloud Next 3일차 정리](https://cloud.google.com/blog/topics/inside-google-cloud/next19-recap-day3)
- 이상훈님의 [2일차 정리글](https://www.facebook.com/phoenixlee.sh/posts/2634407856601132)
- 총평
	- 우선 구글 클라우드의 데이터 처리 도구들(Pub/Sub, Dataflow, Dataproc, BigQuery)는 정말 강력하고 더 쉽게 사용할 수 있게 됨
		- 진입 장벽이 낮아지는 중
		- 미래엔 데이터 엔지니어링 스킬이 기본적으로 있는 머신러닝 엔지니어들도 많이 나올 것 같음
	- AI Platform, Kubeflow, TFX, TFDV, TFMA 등을 보면 MLOps도 활발하게 진행되고 있는 듯?
		- Kubeflow 정복할 예정! 이걸 위해 쿠버네티스 공부중.. 
		- Tensorflow보다 PyTorch가 상승하는 것 같지만 프러덕션에선 대부분 Tensorflow를 써야할 듯
			- 지향점이 다른 느낌
			- 그냥 토치 텐서 다 상관없이 해야할 듯? 
	- 회사에서 BigQuery쪽은 적극적으로 사용하고 있기 때문에 최신 기능을 모두 팔로업하고 있는데, 다른 도구들도 조금씩 테스트해보고 좋은 것은 바로 사용해볼 예정!
	- 서울 리전!

---

### ML Ops Best Practices on Google Cloud
- [https://www.youtube.com/watch?v=20h_RTHEtZI](https://www.youtube.com/watch?v=20h_RTHEtZI)
- MLOps Challenges
	- <img src="https://www.dropbox.com/s/kqwjascfp2tnq6c/Screenshot%202019-04-12%2022.57.22.png?raw=1">
- Data Validation
    - [TFDV](https://www.tensorflow.org/tfx/data_validation/get_started)
    - <img src="https://www.dropbox.com/s/8vl3uvr56wa29kb/Screenshot%202019-04-12%2023.13.47.png?raw=1">
    - <img src="https://www.dropbox.com/s/14kjzk2tsokql9y/Screenshot%202019-04-12%2023.14.11.png?raw=1"> 
- Model Analyze
    - [TFMA](https://www.tensorflow.org/tfx/model_analysis/get_started)
    - Model 정확도가 시간이 지나며 점점 낮아지고 지속적 모니터링이 불가능한 상황이 자주 오는데, 이럴 때 사용하면 좋음
    - <img src="https://www.dropbox.com/s/kvvs6o4rmnwsr3c/Screenshot%202019-04-12%2023.22.21.png?raw=1">
    - <img src="https://www.dropbox.com/s/uaixck53fkfm208/Screenshot%202019-04-12%2023.24.27.png?raw=1">
    - <img src="https://www.dropbox.com/s/0ifgpk12qdlqpzf/Screenshot%202019-04-12%2023.26.50.png?raw=1">
    - trip\_start\_hour가 특정 값일때 정확도를 알려줌
    	- 예시 : 현재 모델이 주간엔 정확도가 높고 야간엔 낮다 이런 결론이 나올 수 있음
- Kubeflow
    - Kubernetes-native OSS Platform to Develop, Deploy and Manage Scalable and End-to-End ML Workloads
- Cloud AI Platform
    - A code-based intergrated development environment for data science and machine learning inside the GCP console
- <img src="https://www.dropbox.com/s/ni6y9e6tasw9ma5/Screenshot%202019-04-13%2000.07.22.png?raw=1">
- <img src="https://www.dropbox.com/s/pl2qcm80z02etme/Screenshot%202019-04-13%2000.12.19.png?raw=1">
- <img src="https://www.dropbox.com/s/05tnudp8mg5axna/Screenshot%202019-04-13%2000.12.44.png?raw=1">
- 아키텍쳐
	- <img src="https://www.dropbox.com/s/9fbqtejea5ki416/Screenshot%202019-04-13%2000.14.25.png?raw=1">
	- <img src="https://www.dropbox.com/s/0t5enhuy9q39hyv/Screenshot%202019-04-13%2000.15.50.png?raw=1">

---

### Accelerating Machine Learning App Development with Kubeflow Pipelines 
- [https://www.youtube.com/watch?v=TZ1lGrJLEZ0](https://www.youtube.com/watch?v=TZ1lGrJLEZ0)
- [Hidden Technical Debt in ML Systems](https://zzsza.github.io/data/2018/01/28/hidden-technical-debt-in-maching-learning-systems/)를 보면 모델링이 차지하는 부분은 매우 작음
- [Kubeflow](https://www.kubeflow.org/)
	- A Kubernetes-native open source platform to develop, deploy and manage, scalable ML Workloads 
- ML Workflow Orchestration
	- <img src="https://www.dropbox.com/s/odt09jlkibamud7/Screenshot%202019-04-13%2001.14.16.png?raw=1">
	- <img src="https://www.dropbox.com/s/rwfdrgj7fd04ad7/Screenshot%202019-04-13%2001.19.02.png?raw=1">
	- 코드 예제
		- <img src="https://www.dropbox.com/s/sb5bzxzxqj8i01m/Screenshot%202019-04-13%2001.19.54.png?raw=1"> 
- Share, Re-use & Compose
	- <img src="https://www.dropbox.com/s/6icgiyldirc4xz6/Screenshot%202019-04-13%2001.21.21.png?raw=1">
	- zip 파일로 올림(람다에서도 이렇게 진행)
	- AI Hub도 사용 가능
		- <img src="https://www.dropbox.com/s/y473qnjfrdblqh2/Screenshot%202019-04-13%2001.22.00.png?raw=1">
	- 코드 예제
		- <img src="https://www.dropbox.com/s/f8xon3c5kg4s50y/Screenshot%202019-04-13%2001.22.18.png?raw=1">  
- Rapid Reliable Experimentation
	- <img src="https://www.dropbox.com/s/0f2vu12npoqd7cz/Screenshot%202019-04-13%2001.23.12.png?raw=1">
	- <img src="https://www.dropbox.com/s/g3vet8pecqxzj3p/Screenshot%202019-04-13%2001.23.22.png?raw=1">
		- MlFlow랑 비교해서 어떤 우위가 있는지 궁금
	- <img src="https://www.dropbox.com/s/4fow7szw0umzq7b/Screenshot%202019-04-13%2001.23.54.png?raw=1">
	- <img src="https://www.dropbox.com/s/6dt7713assf2p3s/Screenshot%202019-04-13%2001.24.39.png?raw=1">
- GOJEK
	- 싱가포르, 인도네시아, 베트남, 태국에서 교통, 물류 등의 서비스를 하고 있는 기업
	- <img src="https://www.dropbox.com/s/8hd7foe2s1sh9u4/Screenshot%202019-04-13%2001.27.01.png?raw=1">
	- <img src="https://www.dropbox.com/s/hlc0rq4txo3j15v/Screenshot%202019-04-13%2001.27.17.png?raw=1">
	- Airflow를 사용했는데, 아래 이유로 Kubeflow 도입
		- 실험하기 어려움
		- 엔지니어링 무거움
		- Low traceability and reproducibility
	- Data pipeline은 Airflow를 쓰고, ML pipeline은 Kubeflow 사용
		- <img src="https://www.dropbox.com/s/fhhps7re6g949jx/Screenshot%202019-04-13%2001.30.10.png?raw=1">
		- <img src="https://www.dropbox.com/s/gkjzprcsd7eumbb/Screenshot%202019-04-13%2001.30.55.png?raw=1">  
		- roc 커브도 그려줌
		- <img src="https://www.dropbox.com/s/3l5a7gxtjao9qeg/Screenshot%202019-04-13%2001.32.51.png?raw=1">
	- TFX Taxi 예제
		- <img src="https://www.dropbox.com/s/hlxdsw5vsnb9cof/Screenshot%202019-04-13%2001.33.27.png?raw=1">
		- <img src="https://www.dropbox.com/s/2uf0du393jt7jrg/Screenshot%202019-04-13%2001.33.57.png?raw=1">
		- <img src="https://www.dropbox.com/s/nlixphmj86t9cau/Screenshot%202019-04-13%2001.34.39.png?raw=1">
		- TFMA를 Static HTML로 생성해줌

---

### What's New with BigQuery ML and Using it to Assess Data Quality
- [https://www.youtube.com/watch?v=DnlG4frLKmw](https://www.youtube.com/watch?v=DnlG4frLKmw)
- [BigQuery ML](https://zzsza.github.io/gcp/2018/08/03/bigqueryml/)의 새로운 기능에 대해 말해줌
- <img src="https://www.dropbox.com/s/2j41htu4m7qalgz/Screenshot%202019-04-13%2001.38.35.png?raw=1">
- New BigQuery UI
	- <img src="https://www.dropbox.com/s/4yh9n5ln4c15bi6/Screenshot%202019-04-13%2001.38.58.png?raw=1">
	- 현기증 납니다.. 빨리 나와주세요
- Matrix Factorization
	- <img src="https://www.dropbox.com/s/p82vj7wp49q6v2l/Screenshot%202019-04-13%2001.39.28.png?raw=1">
- NCAA 농구 3점슛 예측
	- <img src="https://www.dropbox.com/s/rmldqf4v8fxxdy1/Screenshot%202019-04-13%2001.39.59.png?raw=1">
- TensorFlow
	- <img src="https://www.dropbox.com/s/dcu87vkywus6nj5/Screenshot%202019-04-13%2001.40.29.png?raw=1">
- Feautre pre-processing function
	- <img src="https://www.dropbox.com/s/z072npxtlpzw48o/Screenshot%202019-04-13%2001.40.52.png?raw=1">
	- BUCKETIZE, POLYNOMIAL\_EXPAND, FEATURE\_CROSS 등
- 시연
	- [QueryItSmart](https://github.com/groovenauts/QueryItSmart) Github 참고
	- BigQuery + Cloud ML engine을 사용한 웹 데모
- K means clustering
	- <img src="https://www.dropbox.com/s/daa97kxjc03q8rw/Screenshot%202019-04-13%2001.46.33.png?raw=1">
- Booking.com의 시연
	- 빅쿼리로 kmenas 진행(클러스터 개수 21개)한 후 데이터 스튜디오로 시각화
	- <img src="https://www.dropbox.com/s/lizg8uw38l6urol/Screenshot%202019-04-13%2001.48.30.png?raw=1">
	- <img src="https://www.dropbox.com/s/ptdopkb7kv04v7l/Screenshot%202019-04-13%2001.49.58.png?raw=1">

---

### Data Processing in Google Cloud: Hadoop, Spark, and Dataflow
- [https://www.youtube.com/watch?v=GRP-cGbJSCs](https://www.youtube.com/watch?v=GRP-cGbJSCs)
- 이미 많이 알던 내용들인데, 새롭게 알게된 내용 위주로 캡쳐
- <img src="https://www.dropbox.com/s/849mvs0cuc4evrh/Screenshot%202019-04-13%2001.00.17.png?raw=1">
- <img src="https://www.dropbox.com/s/7vwqbypxxsipykj/Screenshot%202019-04-13%2001.00.34.png?raw=1">
- <img src="https://www.dropbox.com/s/zidbjll9urrxkfm/Screenshot%202019-04-13%2001.00.48.png?raw=1">
- Dataflow
	- <img src="https://www.dropbox.com/s/tyb9x1499xe0c5j/Screenshot%202019-04-13%2001.01.10.png?raw=1">
	- 1년 사이에 템플릿이 정말 많아짐
	- <img src="https://www.dropbox.com/s/0y2mqdo8ud4g515/Screenshot%202019-04-13%2001.03.50.png?raw=1">
- Dataproc
	- <img src="https://www.dropbox.com/s/64ukdqwcnewieum/Screenshot%202019-04-13%2001.04.24.png?raw=1">
	- Web interface에서 제플린 선택 가능  

---

### Rethinking Business: Data Analytics With Google Cloud
- [https://www.youtube.com/watch?v=DpngHc31a5Y](https://www.youtube.com/watch?v=DpngHc31a5Y)
- Platform
	- <img src="https://www.dropbox.com/s/wkgkax05ww11oy6/Screenshot%202019-04-13%2001.54.08.png?raw=1">
- Data Funsion
	- Google [Data Fusion](https://cloud.google.com/data-fusion/)
	- <img src="https://www.dropbox.com/s/yy98uamifj5hyl4/Screenshot%202019-04-13%2001.54.37.png?raw=1">
	- 코드 없이 파이프라인 생성
	- <img src="https://www.dropbox.com/s/rwjb56x4dmsxdth/Screenshot%202019-04-13%2001.56.45.png?raw=1">
- BigQuery flat-rate with Reservations
	- 예약제 요금제?
	- <img src="https://www.dropbox.com/s/pppf9nul4xkdj2i/Screenshot%202019-04-13%2001.57.52.png?raw=1">
- BigQuery Storage API
	- <img src="https://www.dropbox.com/s/flavb24cnyv3dgb/Screenshot%202019-04-13%2001.58.22.png?raw=1">
- BI Engine
	- <img src="https://www.dropbox.com/s/lpgu8ytcklbhh4q/Screenshot%202019-04-13%2001.59.06.png?raw=1">
	- 시각화하는 도구 같은데, Tableau를 대체할 수 있을지 궁금
- Dataproc and Composer
	- <img src="https://www.dropbox.com/s/tj2ruljdtnyyryp/Screenshot%202019-04-13%2002.00.33.png?raw=1">
	- Composer는 Managed Airflow인데, 사용하다보면 약간 불편한 점도 있긴함    
- Stream 데이터 처리하는 파이프라인
	- <img src="https://www.dropbox.com/s/126rs7ebz6xh9ij/Screenshot%202019-04-13%2002.01.40.png?raw=1">
- Dataflow SQL
	- <img src="https://www.dropbox.com/s/389dqh15qlgg1wr/Screenshot%202019-04-13%2002.01.57.png?raw=1"> 
	- <img src="https://www.dropbox.com/s/aiylt1wu3hi09vd/Screenshot%202019-04-13%2002.07.18.png?raw=1">
	- 빅쿼리 콘솔에서 Dataflow Engine으로 설정한 후, 아래 쿼리 날림
	- TUMBLE_START는 처음 보는데 신기.. 
	- 쿼리 날리면 내부적으로 Beam이 데이터 읽고 전처리하는듯

		
	```
	SELECT sr.sales_region, TUMBLE_START("INTERVAL 5 SECOND") AS period_start, SUM(tr.payload.amount) as amount
	FROM `pubsub.dataflow-sql.transactions` as tr
		INNER JOIN `bigquery.dataflow-sql.opsdb.us_state_salesregions` AS sr
		ON tr.payload.state = sr.state_code
	GROUP BY sr.sales_region, TUMBLE(tr.event_timestamp, "INTERVAL 5 SECOND")
	```
	
	- <img src="https://www.dropbox.com/s/mluaams5n0o31yz/Screenshot%202019-04-13%2002.05.47.png?raw=1">
	- pubsub 토픽을 바로 넣어서 쓰는듯..?
	- <img src="https://www.dropbox.com/s/637pcfwpwj51u79/Screenshot%202019-04-13%2002.08.08.png?raw=1">
	
- Data Catalog
	- <img src="https://www.dropbox.com/s/z4fegch5hwi7a2v/Screenshot%202019-04-13%2001.53.05.png?raw=1">
	- <img src="https://www.dropbox.com/s/5r7h4my6cq57ybi/Screenshot%202019-04-13%2001.53.40.png?raw=1">

---	
	 
### AI Hub: The One Place for Everything AI
- [https://www.youtube.com/watch?v=QMTT2ngnj9Q](https://www.youtube.com/watch?v=QMTT2ngnj9Q)
- Google [AI Hub](https://cloud.google.com/ai-hub/)
- AI Hub에 대한 설명, 다른 세션과 겹치는 부분이 있는 느낌
- Kubeflow, TFX에 대해 이야기함
