---
layout: post
title:  "Sacred와 Omniboard를 활용한 실험 및 로그 모니터링"
subtitle: "Sacred with Omniboard"
categories: mlops
tags: experiment
comments: true
---

- Python Sacred를 더 잘 활용하기 위해 Omniboard를 붙이는 과정을 작성한 글입니다
	- Sacred는 [머신러닝 실험을 도와줄 Python Sacred 소개](https://zzsza.github.io/mlops/2019/07/21/python-sacred/)를 참고하시면 좋을 것 같습니다 

---

### Omniboard
- Omniboard는 머신러닝 실험 관리 도구인 Sacred를 위한 웹 대시보드
- MongoDB에 연결해 Sacred의 실험, Metric, 로그를 시각화해줌
- React, Node.js, Express와 Bootstrap을 사용해 만들어짐
- 특징
	- Experiment management
		- 모든 실험을 Tabular 형태로 저장
		- 특정 컬럼을 show / hide 가능
		- Rolled up metric value를 컬럼으로 추가 가능(minimum validation loss, maximum training accuracy 등) 
		- 각종 실험에 Tag나 Note를 작성할 수 있음
		- Metric column을 제외한 모든 컬럼으로 필터 가능
	- Experiment Drill Down
		- Metric Graph를 볼 수 있음
		- Console Output을 보여줌
		- 모든 런타임 라이브러리 dependencies를 보여줌
		- 소스 파일을 보거나 다운로드 가능
		- Artifacts(인공 산물? 파일물?)을 보거나 다운로드 가능
		- 호스트의 하드웨어 스펙을 볼 수 있음
		- Git hash/version control 정보를 볼 수 있음

---

### 스크린샷
- Tabular 형태의 데이터
	- Tags, 메모, 각종 파라미터 저장됨 
	- <img src="https://raw.githubusercontent.com/vivekratnavel/omniboard/master/docs/assets/screenshots/table.png">
 
- Metric Plot
	- <img src="https://raw.githubusercontent.com/vivekratnavel/omniboard/master/docs/assets/screenshots/metric-graphs.png">

- Metric Column 관리
	- <img src="https://raw.githubusercontent.com/vivekratnavel/omniboard/master/docs/assets/screenshots/adding-metrics.png"> 

- Output Log
	- <img src="https://raw.githubusercontent.com/vivekratnavel/omniboard/master/docs/assets/screenshots/console.png">

- Experiment Details
	- <img src="https://raw.githubusercontent.com/vivekratnavel/omniboard/master/docs/assets/screenshots/experiment-details.png">

- Host Info
	- <img src="https://raw.githubusercontent.com/vivekratnavel/omniboard/master/docs/assets/screenshots/host-info.png">

---

### 설치
- MongoDB 설치
	- Mac
		
		```
		brew install mongodb
		```
   	
	- Linux(Ubuntu 16.04)
   		
		```
		sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 9DA31620334BD75D9DCB49F368818C72E52529D4
		echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/4.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.0.list
		sudo apt-get update
		sudo apt-get install -y mongodb-org
		echo "mongodb-org hold" | sudo dpkg --set-selections
		echo "mongodb-org-server hold" | sudo dpkg --set-selections
		echo "mongodb-org-shell hold" | sudo dpkg --set-selections
		echo "mongodb-org-mongos hold" | sudo dpkg --set-selections
		echo "mongodb-org-tools hold" | sudo dpkg --set-selections
		``` 

- MongoDB 실행
	- Mac
		
		```
		brew services start mongodb
		``` 	
	
	- Linux(Ubuntu 16.04)

		```
		sudo service mongod start
		```

- pymongo 설치

	```
	pip3 install pymongo
	```	
	
- omniboard 설치
	- npm이 설치되어 있어야 함(Node.js v8 이후 버전) 

	```
	npm install -g omniboard
	```	

- Docker나 Docker Composer를 사용하는 방법은 [공식 문서](https://vivekratnavel.github.io/omniboard/#/quick-start?id=docker) 참조!

---

### Omniboard 실행
- omniboard -m hostname:port:database -u username:password:secret
로 실행

	```
	omniboard -m localhost:27017:sacred
	```	
	
- Advanced connection properties를 셋팅하려면 `--mu` 옵션을 사용하면 됨
	- [MongoDB connection URI](https://docs.mongodb.com/manual/reference/connection-string/)
	
	```
	omniboard --mu "mongodb://<username>:<password>@<host>/<database>[?options]"
	```	
	
- Omniboard 대시보드 실행
	- Omniboard is listening on port 9000! 라는 멘트가 나오면 웹브라우저에서 `localhost:9000`을 입력하면 대시보드가 실행됨
	- 처음엔 실험을 하지 않아서 아무 창이 뜨지 않으나, 실험을 하면 하나씩 추가됨

	
---
	
### Sacred Experiment 실행
- [머신러닝 실험을 도와줄 Python Sacred 소개](https://zzsza.github.io/mlops/2019/07/21/python-sacred/)의 예제에 있던 rf, svc 실험을 FileStorageObserver가 아닌 MongoObserver로 변경해 다시 실행함

	```
	from sacred.observers import MongoObserver

	ex = Experiment('svc')
	ex.observers.append(MongoObserver.create(url='localhost:27017', db_name='sacred'))
	```

- 그 후 Metric Plot을 그리기 위해 [Sacred Example Code Pytorch](https://github.com/maartjeth/sacred-example-pytorch)를 Clone 후 실행함

	```
	git clone https://github.com/maartjeth/sacred-example-pytorch.git
	cd sacred-example-pytorch
	# train_nn.py의 DATABASE_NAME 변수를 sacred로 수정한 후 실행
	python3 train_nn.py
	```	
	
- 대시보드로 이동(localhost:9000)
	- 실험 이름, Tags, 메모 등이 보임
		- Tags나 Notes는 그냥 입력하면 됨
		- <img src="https://www.dropbox.com/s/wby7a3581htrrt8/Screenshot%202019-07-21%2023.09.46.png?raw=1">
	- 우측엔 Result나 각종 파라미터가 저장됨
		- 빈칸인 공간은 해당 실험이 그 파라미터를 사용하지 않아서!
		- <img src="https://www.dropbox.com/s/zjgaz1r0nteqc32/Screenshot%202019-07-21%2023.10.45.png?raw=1">
- 맨 좌측의 파란색 삼각형 클릭하면 자세한 정보가 나옴
	- Metrics Plot을 보면 Steps이 지나며 loss가 감소하는 것을 볼 수 있음
	- <img src="https://www.dropbox.com/s/82jq9gwatvrb02f/Screenshot%202019-07-21%2023.15.40.png?raw=1">
- Captured Out
	- Print한 로그가 나타나는 공간
	- <img src="https://www.dropbox.com/s/ds4nsks9ygd2tnj/Screenshot%202019-07-21%2023.17.09.png?raw=1">
- Experiment Details
	- 실험 디테일 정보가 저장되는 곳
	- <img src="https://www.dropbox.com/s/uchvng4rspq77yl/Screenshot%202019-07-21%2023.17.48.png?raw=1">
- Host Info
	- OS, CPU, Python version 등을 볼 수 있음 
	- <img src="https://www.dropbox.com/s/gkyda7ozr1silkc/Screenshot%202019-07-21%2023.18.39.png?raw=1">
- Config
	- Config 설정값을 볼 수 있음
	- <img src="https://www.dropbox.com/s/1t9rf2ewkk6zeu4/Screenshot%202019-07-21%2023.19.02.png?raw=1">  	

---

### 활용 방안
- 사용하고 있는 머신과 MongoDB를 연결해, 다양한 실험을 돌리고 Omniboard로 결과를 모니터링할 수 있음
- 이 문서는 local에 설치했지만, Docker를 사용해 설치하면 더 편리할 것 같음


---
	
### Reference
- [Sacred 공식 Github](https://github.com/IDSIA/sacred/tree/master/examples)
- [Sacred-example-pytorch](https://github.com/maartjeth/sacred-example-pytorch/blob/master/train_nn.py)
- [Omniboard Document](https://vivekratnavel.github.io/omniboard/)
