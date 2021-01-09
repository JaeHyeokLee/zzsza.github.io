---
layout: post
title:  "Python Ray 사용법 - Python 병렬처리, 분산처리"
subtitle: "Python Ray 사용법 - Python 병렬처리, 분산처리"
categories: mlops
tags: ray
comments: true
---

- 파이썬 병렬처리를 위한 Python Ray 사용법에 대한 글입니다
	- 키워드 : Python Ray for multiprocessing, Python Parallel, Distributed Computing, Python Ray Core, Python Ray for loop, Python ray example
	- 해당 글은 병렬처리에 초점을 맞춰 작성했습니다. 쿠버네티스를 활용한 Ray 분산처리 글은 추후에 업로드할 예정입니다
	- 혹시 글에 이상한 부분이 있으면 언제든 말씀해주세요 :)
- [Ray Github](https://github.com/ray-project/ray)

---


- 목차
	- Ray
	- Ray의 장점
	- Ray 구성 요소
	- Ray 사용법
	- 자주 사용하는 API 정리
	- Ray Dashboard
	- 처음 사용하는 사람들을 위한 Tip
	- 딥러닝 프레임워크에서 활용하는 Ray
	- Ray 활용 방식, 겪었던 Error
	- Ray 관련 컨텐츠
	- 마치며


---


### Ray
- Python에서 병렬처리할 경우 multiprocessing을 주로 사용함
	- 하지만 multiprocessing 라이브러리를 사용하기 위해 기존에 작성한 코드를 수정해야 함(Pool 생성해서 넘기기 등)
	- multiprocessing은 여러 클러스터에서 분산처리하기엔 초심자 입장에서 어려움
- Ray는 버클리 대학의 RISE 연구실에서 만들고, 최근에 연구실분들이 [AnyScale](https://www.anyscale.com/)라는 회사를 설립해 Ray를 사용한 프러덕트를 만들고 있음 
	- [Apache Arrow](https://github.com/apache/arrow)를 사용해 데이터를 효율적으로 처리함
- Ray는 최근에 핫한 라이브러리로 머신러닝/딥러닝을 위해 개발되었지만 다양하게 활용할 수 있음
   - Ray provides a simple, universal API for building distributed applications.
   - 분산 어플리케이션을 만들기 위해 단순하고 범용적인 API를 제공하는 라이브러리
   - **기존에 작성한 코드를 조금만 추가해서 병렬처리를 할 수 있는 점이 큰 장점!**
   - Process 기반으로 분산처리, 병렬처리를 진행함
- 로컬 환경, 클라우드의 쿠버네티스(AWS, GCP, Azure) 환경, 온프레미스 쿠버네티스 등 다양한 환경에서 사용할 수 있음
	- 단, 아직 Widnow에선 아직 Alpha 단계
	- 더 궁금하시면 [Windows Support](https://docs.ray.io/en/master/installation.html#windows-support)를 참고
- 회사에서 Ray를 사용해 프로젝트 3개를 진행했는데, 성능도 우수하고 사용감도 매우 좋아 적극적으로 추천하는 중

<br />

<img src="https://www.dropbox.com/s/zv098zqyuw3g3t2/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202021-01-02%20%EC%98%A4%ED%9B%84%208.21.17.png?raw=1">

- 벤치마크 결과 48 물리 코어를 가지는 환경에서 Ray가 멀티프로세싱보다 9배 빠르고, 싱글스레드 환경보단 28배가 빠르다고 함
- Ray의 3가지 미션
	- 1) 분산 어플리케이션을 빌드, 실행하기 위한 기본 요소 제공
	- 2) 아주 적은 코드 변경으로 병렬화할 수 있음
	- 3) Ray Core를 기반으로 여러 애플리케이션 존재. 대규모 에코 시스템 구축


<br />

<img src="https://www.dropbox.com/s/brx31ag2z4qydpu/스크린샷 2020-11-08 오후 9.45.20.png?raw=1">


- 에코 시스템
    - Native 라이브러리
        - RLLIB
        - Ray Tune
        - Ray Serve
        - Ray SGD
    - 서드파티 라이브러리
        - Hyperopt
        - Optuna
        - Spacy
        - Horovod, Pytorch
        - Dask
        - Cloud ML platform : Amazone Sagemaker, Azure Machine Learning
        - Weights & Biases
        - Seldon
        - Modin 등




<br />

---

### Ray의 장점
- 1) 기존 코드에서 약간의 수정만으로 병렬 처리 가능 : 쉽고 범용적인 사용성
	- 처음엔 병렬처리 생각하지 않고 코드 구현 후, 마지막에 병렬로 구현
	- 함수에 데코레이터로 `@ray.remote`로 감싸기
	- 클래스도 `@ray.remote`로 감싸기
		- 혹은 ray.remote(클래스)로 사용할 수도 있음
- 2) 클러스터 환경에서 구축 가능
	- AWS, GCP, Azure, 쿠버네티스 등 설정 제공함
	- [Ray Autoscaler Github](https://github.com/ray-project/ray/tree/master/python/ray/autoscaler)
- 3) Dashboard가 존재해서 성능, 로그 등을 확인 가능
- 4) 멀티프로세싱보다 빠른 성능
- 5) 머신러닝/딥러닝에서 활용하기 최적
	- 머신러닝/딥러닝 외에서도 활용 가능(크롤링 병렬처리, 최적화 병렬처리 등)
- 6) 풍부한 생태계
	- 강화학습을 위한 RLLib
	- 하이퍼파라미터 튜닝을 위한 Ray Tune
	- 분산 학습을 위한 Ray SGD
	- Serving을 위한 Ray Serve
	- 그 외에 Dask, Horovod, Hugging Face, Modin, PyCaret, Scikit Learn, Spacy, XGBoost, Seldon Alibi 등 다른 라이브러리와 활발하게 성장 중
- 7) Nested하게 실행 가능
	- Ray Task의 호출 결과로 여러 Task가 생성될 수 있음
	



<br />

---

### Ray 구성 요소
- 처음 Ray를 접할 때는 크게 Task, Actor, Object, Driver, Job 정도만 이해해도 충분함
	- 그 이후에 내부가 궁금하면 Node, Worker, Scheduler, Global Control Store 등에 대해 파악해보기
- Task(Remote Function)
	- 호출하는 곳과 다른 프로세스에서 실행되는 함수
	- 함수를 @ray.remote로 감싼 경우를 Task라 지칭
		- Remote Function이라고 부르는 경우도 있음
	- 호출할 경우 Task가 비동기(asynchoronously)하게 실행됨
	- `.remote()`를 호출하면 Future 객체(ObjectRef)를 반환
	- `ray.get(ObjectRef)`를 할 경우 Task가 실행됨
	- @ray.remote로 감싼 함수의 경우 stateless하고, @ray.remote로 감싼 클래스(Actor)의 Task는 stateful함
	- 사용 방식
		- 함수를 작성하고(예 : say_hello 함수)
		- 함수에 데코레이터로 @ray.remote로 감싸고
		- 함수 호출은 say_hello.remote()
		- 값 반환은 ray.get(say_hello.remote())
- Actor
	- Stateful한 워커
	- @ray.remote로 감싼 파이썬 클래스 인스턴스
- Object
	- 어플리케이션의 value
	- Task를 통해 반환되거나 `ray.put()`을 통해 생성
		- 사용 예시 :  Pandas 데이터프레임을 Object로 만들어서 Ray 워커에서 사용하곤 함
	- Object는 immutable하며 한번 생성되면 수정되지 않음(스파크의 RDD처럼)
- Driver
	- 프로그램의 메인 루트
	- `ray.init()`을 호출하면 실행
- Job
	- 동일한 드라이버에서 발생한 Task, Actor, Object의 컬렉션

<br />

---

### Ray 사용법
- 이 파트에서 사용한 코드는 [Github](https://github.com/zzsza/zzsza.github.io/blob/master/_codes/python-ray/python-ray-basic.ipynb)에 저장되어 있음
- 설치

	```
	pip install ray
	```

- 현재 1.2.0.dev0까지 나왔고, 이 글에선 1.0.1.post1 버전 사용
- 사용법
	- 1) 함수 또는 클래스 생성
	- 2) 함수 또는 클래스에 `@ray.remote` 데코레이터로 감싸기
	- 3) `ray.init()`으로 Ray 드라이버 실행
	- 4) obj_id = 함수.remote()로 함수 호출(비동기)
	- 5) `ray.get(obj_id)`으로 결과 받기(동기)

- 라이브러리 import 및 버전 확인

	```
	import ray
	import datetime
	import time
	
	print(ray.__version__)
	# 1.0.1.post1
	```

- 비교를 위해 일반 파이썬 함수 정의

	```
	def print_current_datetime():
	    time.sleep(0.3)
	    current_datetime = datetime.datetime.now()
	    print(current_datetime)
	    return current_datetime
	```    

- 일반 파이썬 함수 호출

	```
	print_current_datetime()
	```


- Ray 실행

	```
	ray.init()
	```

- 실행시 아래와 같은 값들이 출력됨
	- `http://127.0.0.1:8265`(localhost:8265)로 접근하면 대시보드를 확인할 수 있음
	- 대시보드 내용은 밑에서 따로 다룸

	```
	2021-01-03 14:58:12,051	INFO services.py:1090 -- View the Ray dashboard at http://127.0.0.1:8265
	
	
	{'node_ip_address': '192.168.25.44',
	 'raylet_ip_address': '192.168.25.44',
	 'redis_address': '192.168.25.44:6379',
	 'object_store_address': '/tmp/ray/session_2021-01-03_01-07-23_960786_88070/sockets/plasma_store',
	 'raylet_socket_name': '/tmp/ray/session_2021-01-03_01-07-23_960786_88070/sockets/raylet',
	 'webui_url': '127.0.0.1:8265',
	 'session_dir': '/tmp/ray/session_2021-01-03_01-07-23_960786_88070',
	 'metrics_export_port': 59624,
	 'node_id': '6c2fd924e47e4b95dc850e802afed7eaaea312c5'}
	``` 
 
 
- Ray Task(Remote Function) 정의
	- 기존 함수에서 @ray.remote만 추가됨

	```
	# Ray Task    
	@ray.remote
	def print_current_datetime():
	    time.sleep(0.3)
	    current_datetime = datetime.datetime.now()
	    print(current_datetime)
	    return current_datetime
	```

- 일반 파이썬 함수 호출처럼 실행해보면 TypeError 발생

	```
	print_current_datetime()
	```

	- 아래와 같은 메세지가 출력됨. Remote 함수는 직접적으로 호출할 수 없고, .remote()를 추가하라고 함
	
		```
		Remote functions cannot be called directly. Instead of running '__main__.print_current_datetime()', try '__main__.print_current_datetime.remote()'.
		```

- print_current_datetime.remote()를 사용해 Task 호출
	- ObjectRef가 반환됨

		```
		print_current_datetime.remote()
		# ObjectRef(df5a1a828c9685d3ffffffff0100000001000000)
		```

	- 다시 또 실행하면 다른 id를 반환함

		```
		print_current_datetime.remote()
		ObjectRef(cb230a572350ff44ffffffff0100000001000000)
		```
	
- `ray.get()`을 사용해 값 받기

	```
	ray.get(print_current_datetime.remote())
	```

- 동시에 4번 실행하기
	- List Comprehension을 사용해 futures를 생성한 후, 출력하면 4개의 ObjectRef를 확인할 수 있음
	- ray.get을 통해 값 받기

	```
	futures = [print_current_datetime.remote() for i in range(4)]
	
	print(futures)
	
	# [ObjectRef(bd37d2621480fc7dffffffff0100000001000000), 
	# ObjectRef(88866c7daffdd00effffffff0100000001000000), 
	# ObjectRef(d251967856448cebffffffff0100000001000000), 
	# ObjectRef(3bf0c856ace5a4d8ffffffff0100000001000000)] 
	
	
	ray.get(futures)
	
	# [datetime.datetime(2021, 1, 3, 15, 3, 38, 389308),
	# datetime.datetime(2021, 1, 3, 15, 3, 38, 390788),
	# datetime.datetime(2021, 1, 3, 15, 3, 38, 390788),
	# datetime.datetime(2021, 1, 3, 15, 3, 38, 390882)]
	```

<br />

---

### 자주 사용하는 API 정리
- 위에서 설명하진 않았지만 알아두면 좋은 `ray.put()`, `ray.wait()`, `ray.shutdown()`도 정리함
- 참고로 ray.put()는 큰 배열을 공유 메모리에 저장하고 복사본을 만들지 않고 모든 작업자에 프로세스에 액세스 할 수 있음
    - 복사를 하지 않으니 메모리를 아낄 수 있음

<img src="https://www.dropbox.com/s/1pnke3phden1f70/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202021-01-03%20%EC%98%A4%ED%9B%84%204.17.36.png?raw=1">

<br />

---

### Ray Dashboard
- Ray의 대시보드의 역할
	- 클러스터 지표 확인
		- 클러스터 이상을 감지하고 디버깅
	- 로그 및 오류를 확인할 수 있음
	- 여러 머신의 로그를 한번에 확인할 수 있음
	- Ray 메모리 사용률, 디버그 메모리 오류들을 확인할 수 있음
	- Actor별 리소스 사용량, 실행된 Task, 로그 등을 확인
	- Actor를 Kill하고 Ray Job을 프로파일링

- Ray 사용법에서 나온 코드를 실행한 후, `localhost:8265`로 이동하면 대시보드가 보임
	- 포트를 수정하고 싶은 경우 ray.init()에서 dashboard_port 인자 값을 수정하면 됨

<img src="https://www.dropbox.com/s/ng8lbi4x4c1cmn4/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202021-01-03%20%EC%98%A4%ED%9B%84%204.19.59.png?raw=1">

- Ray Dashboard 구성
	- Machine View : 리소스 사용량 확인, 로그/에러 확인
	- Logical View : Ray 클러스터에서 실행 중인 Actor 모니터링(클러스터가 아닌 경우 아무것도 뜨지 않음)
	- Memory : Ray Object Store에 저장된 정보를 볼 수 있음
	- Ray config : Ray 클러스터 설정
	- TUNE(이건 Ray Core만 실행할 시 보이지 않음)

- 현재 프로세스가 8개가 보이는데, 이는 제 실행 환경의 CPU가 8 Core라서 이렇게 보임(ray.init시 CPU 갯수를 설정할 수 있음)
- 프로세스별 CPU, Memory, GPU 사용량을 볼 수 있음
	- Plasma는 위에서 설명하지 않았지만, Object를 저장해두는 Object Store
	- 더 궁금할 경우 [The Plasma In-Memory Object Store](https://arrow.apache.org/docs/python/plasma.html#the-plasma-in-memory-object-store) 참고


- 우측에 Logs를 보면 전체 로그와 프로세스별 로그를 확인할 수 있음
	- 아래 이미지는 View all logs를 클릭한 경우

	<img src="https://www.dropbox.com/s/bhqfw2efh3zo167/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202021-01-03%20%EC%98%A4%ED%9B%84%204.25.20.png?raw=1">
	
	- 하나의 프로세스 로그를 클릭하면 다음과 같이 나옴

	<img src="https://www.dropbox.com/s/8py8v1tzzmq28fg/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202021-01-03%20%EC%98%A4%ED%9B%84%204.27.15.png?raw=1">
	
- 4개의 프로세스에만 로그가 남아있는 이유는 위에서 실행한 다음 코드가 4개의 프로세스에 분배

	```
	futures = [print_current_datetime.remote() for i in range(4)]
	ray.get(futures)
	```

	- 8개의 프로세스에 병렬처리할 경우

		```
		futures = [print_current_datetime.remote() for i in range(8)]
		ray.get(futures)
		```

	- 8개의 프로세스 log가 생성됨(=8개로 나눠서 작업함)

- 16개로 분배할 경우

	```
	futures = [print_current_datetime.remote() for i in range(16)]
	ray.get(futures)
	```

- 8개의 프로세스가 있으므로 각 프로세스에 2번씩 실행됨

<img src="https://www.dropbox.com/s/kjsu267o6nw1tga/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202021-01-03%20%EC%98%A4%ED%9B%84%204.31.28.png?raw=1">


- 더 자세한 정보는 [공식 문서 Ray Dashboard](https://docs.ray.io/en/latest/ray-dashboard.html) 참고


<br />

---

### 처음 사용하는 사람들을 위한 Tip
- 공식 문서 [Tips for first-time users](https://docs.ray.io/en/master/auto_examples/tips-for-first-time.html) 부분을 핵심 위주로 간단히 정리한 내용
	- 해당 문서는 꼭 읽어보시는 것을 추천!!!
- 1) `ray.get()`은 실행 횟수 파악하기(가능하면 늦게 호출)
	- 다음과 같이 remote를 하며 호출하는 경우는 느림
		- 예시(List Comprehension하며 ray.get을 반복 호출)
	
			```
			results = [ray.get(do_some_work.remote(x)) for x in range(4)]
			```
	
	- 빠른 예시(List Comprehension 결과를 ray.get으로 받아 1번만 호출)
	
		```
		results = ray.get([do_some_work.remote(x) for x in range(4)])
		```
		
- 2) CPU가 적거나 클러스터가 적은 상황에선 성능 개선이 크지 않을 수 있음
	- 테스트는 여러 환경에서 할 수 있도록 Test 자동화 필요
- 3) 작은 함수를 모두 Task로 만들 필요 없음
	- 스케줄링, 내부 프로세스 커뮤니케이션 등에 오버헤드가 걸릴 수 있음
	- 큰 Task를 생성해서 작은 함수를 여러번 실행하도록 만들어서 성능 개선할 수 있음

- 4) 반복되고 큰 데이터의 경우 ray.put으로 저장한 후 활용하기
	- 단 1번만 object store에 저장해서 반복적으로 접근하지 않음
	- 데이터를 그냥 사용하는 경우

		```
		import time
		import numpy as np
		import ray
		
		ray.init(num_cpus = 4)
		
		@ray.remote
		def no_work(a):
		    return
		
		start = time.time()
		a = np.zeros((5000, 5000))
		result_ids = [no_work.remote(a) for x in range(10)]
		results = ray.get(result_ids)
		print("duration =", time.time() - start)
		# duration = 1.0837509632110596
		```
	
	- 데이터를 ray.put으로 저장한 후 사용하는 경우
	
		```
		import time
		import numpy as np
		import ray
		
		ray.init(num_cpus = 4)
		
		@ray.remote
		def no_work(a):
		    return
		
		start = time.time()
		a_id = ray.put(np.zeros((5000, 5000)))
		result_ids = [no_work.remote(a_id) for x in range(10)]
		results = ray.get(result_ids)
		print("duration =", time.time() - start)
		# duration = 0.132796049118042
		```	
	


<br />

---


### 딥러닝 프레임워크에서 활용하는 Ray
- PyTorch, Tensorflow에서 사용하는 방식도 크게 다르지 않음
	- ray.init()
	- @ray.remote로 Task/Actor 생성
	- ray.get으로 결과 받기
	- Tensorflow는 pickling 이슈가 있을 수 있는데, 함수 안에서 import tensorflow하면 해결됨
	- 자세한 코드는 아래의 공식 문서 예제 참고!
- [Best Practices: Ray with PyTorch](https://docs.ray.io/en/master/using-ray-with-pytorch.html)
- [Best Practices: Ray with Tensorflow](https://docs.ray.io/en/master/using-ray-with-tensorflow.html)


<br />

---


### Ray 활용 방식, 겪었던 Error
- 제가 Ray를 사용했던 방식
	- 선형 계획법(Linear Programming, Constraint Programming)에서 활용 : 단일 인스턴스에서 여러 프로세스로 병럴 처리해서 많은 양을 빠르게 처리
	- 딥러닝 모델 Inference : 딥러닝 모델을 인퍼런스시 CPU Inference로 병렬 처리
	- 병렬 크롤링 : 빠르게 데이터를 수집할 수 있음
	- Tune을 사용한 하이퍼파라미터 튜닝
	- 쿠버네티스 환경에에 Ray 클러스터 운영하기(현재 계속 테스트 중)
- 느낀 점
	- Ray를 사용할 경우 AWS의 경우 Spot, GCP의 경우 선점형 인스턴스를 활용하면 저렴하게 활용할 수 있음 => Apache Spark에서 고성능 머신 하나로 전처리하는 것과 유사한 방식
		- 단, 선점형 인스턴스는 클라우드사에 의해 종료될 수 있으니 이 대응책 필요
	- 데이터를 가지고 오는 부분은 초반에 한번만 진행하고, CPU 갯수만큼 데이터프레임을 쪼개서 분배
		- Dataframe을 ray.put으로 저장시켜서 활용하기
	- 작업이 실패할 경우 대비하기
	- CPU 갯수를 확인해서 넣고 싶은 경우 다음처럼 사용
	
	```
	import psutil
	num_logical_cpus = psutil.cpu_count()
	ray.init(num_cpus= num_logical_cpus)
	```
	
- Error
	- 노드의 메모리 95%를 사용할 경우 Ray가 종료됨(RayOutOfMemoryError)
		- Memory 사용량을 확인할 수 있도록 코드를 수정했고 가비지 콜렉터 실행		
	- ray.init시 OSError: [Errno 99] error while attempting to bind on address ('::1', 8265, 0, 0): cannot assign requested address 오류
		- [Github Issue](https://github.com/ray-project/ray/issues/7084)
		- ray.init(webui_host='127.0.0.1')로 해결
- 커뮤니티에서 작성한 [Ray Design Patterns](https://docs.google.com/document/d/167rnnDFIVRhHhK4mznEIemOtj63IOhtIPvSYaPgI4Fg/edit#heading=h.crt5flperkq3)을 보며 Ray를 사용하는 다양한 방식을 익혀도 좋을듯!(안티패턴 피하기)	


<br />

---
    
    
### Ray 관련 컨텐츠
- Ray 공식 문서
	- [Ray Blog](https://medium.com/distributed-computing-with-ray)
	- [Ray Design Patterns](https://docs.google.com/document/d/167rnnDFIVRhHhK4mznEIemOtj63IOhtIPvSYaPgI4Fg/edit#heading=h.crt5flperkq3) : Ray를 사용한 다양한 Design Pattern에 대해 작성된 문서
	- 공식 문서의 [Tutorial and Examples](https://docs.ray.io/en/latest/auto_examples/overview.html) : 사실 튜토리얼보다 여러 팁 모음이라 생각하는 것이 좋음. 읽으면 좋음
	- 공식 문서의 [Advanced Usage](https://docs.ray.io/en/latest/advanced.html#advanced-usage) : Ray 기본을 모두 파악한 후 문서를 보면 좋음
	- 공식 문서의 [Launching Cloud Clusters with Ray](https://docs.ray.io/en/latest/cluster/launcher.html) : 쿠버네티스 환경에서 Ray를 실행하고 싶은 경우 읽으면 좋음
	- [Ray 1.0.0 릴리즈 문서](https://github.com/ray-project/ray/releases/tag/ray-1.0.0), [블로그 글](https://www.anyscale.com/blog/announcing-ray-1-0) : 1.0에서 어떤 점이 변했는지 알 수 있음
- 코드, Tutorial
	- Anyscale의 [Academy Github](https://github.com/anyscale/academy) : Ray Tutorial 가득! 코드 참고하면 매우 유익
		- [Anyscale 유튜브](https://www.youtube.com/watch?v=2dgIEwkGYGI&ab_channel=Anyscale)에 가면 영상을 볼 수 있음
		- Ray 공식 [Tutorial Github](https://github.com/ray-project/tutorial) : 위에 Anyscale이 최신본이고 이 레포는 그 이전 버전인데, exercises가 자세히 나와있어 유익함
- 영상 또는 발표 자료
	- [Ray Summit 2020](https://www.youtube.com/watch?v=8GTd8Y_JGTQ&list=PLzTswPQNepXlr2L75tZt7l8tyVZkO-RVt&ab_channel=anyscale) : 강력 추천! 
	- [Ray: A System for Scalable Python and ML](https://youtu.be/XIu8ZF7RSkw) : 유튜브에 Ray를 검색하면 Nishihara님이 발표해주신 영상이 꽤 있는데, 최신 영상 위주로 보면 좋음(내용은 비슷한 편)
	- [Ray: A General Purpose Serverless Substrate?](https://static.sched.com/hosted_files/ray2020/a4/Ray-%20A%20General%20Purpose%20Serverless%20Substrate_%20-%20Eric%20Liang%2C%20Anyscale.pdf) :  서버리스와 Ray를 연결해서 설명한 발표 자료
	- [Ray Serve: Scalable ML Serving](https://static.sched.com/hosted_files/ray2020/83/Introducing%20Ray%20Serve_%20Scalable%20and%20Programmable%20ML%20Serving%20Framework.pdf) : Ray Serve에 대한 발표 자료
- 백서(WhitePaper)
	- [Ray 1.0 Architecture Whitepaper](https://docs.google.com/document/d/1lAy0Owi-vPz2jEqBSaHNQcy2IBSDEHyXNOQZlGuj93c/preview#heading=h.lu4bab1e48st) : Ray 1.0의 세부 구조에 대해 자세히 나와있음. 처음 접하는 경우는 Ray에 익숙해지고 보시는 것을 추천
	- [Ray 아키텍쳐 백서(번역)](https://brunch.co.kr/@chris-song/106) : 1.0 백서는 아니지만 한국어 자료라 추천!
	- [RLlib Paper](https://arxiv.org/abs/1712.09381)
	- [Ray Tune Paper](https://arxiv.org/abs/1807.05118)
- 추천 글
	- [10x Faster Parallel Python Without Python Multiprocessing](https://towardsdatascience.com/10x-faster-parallel-python-without-python-multiprocessing-e5017c93cce1)   
		- [한국어 번역본](https://data-newbie.tistory.com/415)
	- [Modern Parallel and Distributed Python: A Quick Tutorial on Ray](https://towardsdatascience.com/modern-parallel-and-distributed-python-a-quick-tutorial-on-ray-99f8d70369b8)
		- 마틴님의 [한국어 번역본](https://dl4ab.github.io/2020/09/01/20200902-A-Quick-Tutorial-on-Ray/)
	- [How to Scale Up Your FastAPI Application Using Ray Serve](https://medium.com/distributed-computing-with-ray/how-to-scale-up-your-fastapi-application-using-ray-serve-c9a7b69e786)
- Ray Tune 자료
	- 공식 문서의 Ray Tune [Reference Materials](https://docs.ray.io/en/latest/tune/index.html#reference-materials) 참고
- 기타
	- [Ray community Slack](https://forms.gle/9TSdDYUgxYs8SA9e8) : Ray 커뮤니티 슬랙! 질문하면 답변 잘 해주심
	- [xgboost_ray](https://github.com/ray-project/xgboost_ray) : Ray를 사용해 분산 학습하는 Repo


<br />

---

### 마치며
- Ray가 아직 생소하실 수 있지만, 한번 익혀두시면 매우 유용할거라 생각됩니다!
- 제가 봤던 여러 컨텐츠를 잘 정리해두었으니, 조금씩 익혀보세요!
- Ray Core를 익히시고 여러 라이브러리(Tune, RLlib 등)을 학습해보세요!
	- 그 후엔 쿠버네티스 환경에서 띄워보세요!
	- 저도 위 내용을 진행하고 있는데, 추후 정리해서 공유드릴게요 :)




