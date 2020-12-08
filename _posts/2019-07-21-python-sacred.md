---
layout: post
title:  "머신러닝 실험을 도와줄 Python Sacred 소개"
subtitle: "머신러닝 실험을 도와줄 Python Sacred 소개"
categories: mlops
tags: experiment
comments: true
---

- 머신러닝 실험에서 사용되는 Config, Parameter 등을 더 손쉽게 저장할 수 있도록 도와주는 Python Library Sacred에 대한 글입니다
	- Sacred 대시보드 관련 내용은 [Sacred와 Omniboard를 활용한 로그 모니터링](https://zzsza.github.io/mlops/2019/07/22/sacred-with-omniboard/)에 작성했습니다! 

---

### Python Sacred 
- 머신러닝을 하다보면 각종 파라미터에 따라 결과가 어떻게 나오는지 기록해야함
    - 처음엔 .log 파일에 작성해두다가, 점점 방대해지는 모습을 보게됨
- Sacred는 환경 설정을 다시 reproduce할 수 있도록 도와주는 도구
    - 실험의 모든 파라미터 추적
    - 여러 설정에 대해 쉽게 실험을 할 수 있음
    - DB에 각 실행의 설정을 저장
    - 결과를 reproduce
- Omniboard 등을 통해 저장된 값을 시각화할 수 있음
	- Omniboard란?
	    - Sacred에서 저장된 파라미터를 더 쉽게 볼 수 없을까?에 대한 고민으로 시작된 프로젝트
	    - Sacred에서 저장된 파라미터를 Table로 보여주고, 성능도 알려줌
	    - Sacred + Omniboard 조합을 사용하는 것을 매우 추천!
	    - **Omniboard 설치 및 실행은 [Sacred와 Omniboard를 활용한 로그 모니터링](https://zzsza.github.io/mlops/2019/07/22/sacred-with-omniboard/) 참고 :)**
- 공식 문서에 작성된 내용

        Every experiment is sacred
        Every experiment is great
        If an experiment is wasted
        God gets quite irate


- Sacred의 Main mechanisms
    - ConfigScopes : 함수의 local 변수를 편리하게 다룰 수 있음.`@ex.config` 데코레이터로 사용
    - Config Injection : 모든 함수에 있는 설정을 접근할 수 있음
    - Command-line interface : 커맨드 라인으로 파라미터를 바꿔서 실행할 수 있음
    - Observers : 실험의 모든 정보를 Observers에게 제공해 저장함. 이것들은 MongoDB에 저장
    - Automatic seeding : 실험의 무작위를 컨트롤할 때 도와줌

- 예시
    - <img src="https://www.dropbox.com/s/29dqic2lhohl60g/Screenshot 2019-07-21 14.31.20.png?raw=1">

- 설치

        pip3 install sacred

- Dependency 설치

        pip3 install numpy, pymongo

### First Experiment

- 샘플 코드를 통해 Sacred에 대해 알아보기
    - first_config.py 파일 생성
	
	```
	from sacred import Experiment
	    
	ex = Experiment('hello_config')
	# Notebook일 경우 
	# ex = Experiment('hello_config', interactive=True)
	    
	@ex.config
	def my_config():
	    recipient = "world"
	    message = "Hello %s!" % recipient
	    
	@ex.automain
	def my_main(message):
		print(message)
	```

    - ex = Experiment('실험 이름')
        - `@ex.config`로 파라미터를 저장함
    - `@ex.automain` 을 사용해 command line으로 실행할 수 있도록 설정
    - Jupyter Notebook에선 `@ex.main`을 사용하고, 실험을 만들 때 interactive=True로 설정
- 실행

	```
	python3 first_config.py
	```
	
- 터미널에서 config 출력
	
	```
   python3 first_config.py print_config
	```
    - 여러번 실행하면 seed값이 달라지는 것을 알 수 있음
    - <img src="https://www.dropbox.com/s/t6ygfinmwmn4vlc/Screenshot 2019-07-21 14.43.30.png?raw=1">

- Command Line에서 파라미터 injection
    - with [파라미터]="value" 로 넣을 수 있음
    	
    	```
    	python3 first_config.py with recipient="that is cool"
    	```

    - <img src="https://www.dropbox.com/s/llwsv70ieq1tbve/Screenshot%202019-07-21%2014.51.27.png?raw=1">

- Experiment를 import해서 실행하는 방법

        from first_config import ex
        
        r = ex.run()

- Python에서 파라미터 injection
    - run할 때 `config_updates` 를 설정

		```
		from first_config import ex
		    
		r = ex.run(config_updates={'recipient': 'awesome'})
		```
		
- Capture Functions

        from sacred import Experiment
        ex = Experiment('my_experiment')
        
        
        @ex.config
        def my_config():
            foo = 42
            bar = 'baz'
        
        
        @ex.capture
        def some_function(a, foo, bar=10):
            print(a, foo, bar)
        
        
        @ex.automain
        def my_main():
            some_function(1, 2, 3)  # 1 2 3
            some_function(1)        # 1 42 'baz'
            some_function(1, bar=12)  # 1 42 12
            some_function()  # TypeError : missing value for 'a'

    - <img src="https://www.dropbox.com/s/276kmssacwu1dp5/Screenshot 2019-07-21 15.12.58.png?raw=1">

### Experiment 관찰하기

- Experiments는 실행할 때 다양한 정보를 수집함
    - 시작 시간, 중단 시간
    - 사용된 환경 설정
    - 발생한 에러나 결과물
    - 머신의 기본 information
    - 그 버전과 의존적인 패키지
    - import한 모든 local source files
    - `ex.open_resource`로 file을 오픈하고 `ex.add_artifact`로 파일을 추가함
- Observer는 MongoObserver, File Storage Observer, TinyDB Observer, SQL observer 등이 있습니다
    - [공식 문서](https://sacred.readthedocs.io/en/latest/observers.html)
- MongoObserver에 연결하기

        from sacred import Experiment
        from sacred.observers import MongoObserver
        
        ex = Experiment('hello_config')
        ex.observers.append(MongoObserver.create(url='your_server:27017',
                                                 db_name='My_DB'))
        
        @ex.config
        def my_config():
            recipient = "world"
            message = "Hello %s!" % recipient
        
        @ex.automain
        def my_main(message):
            print(message)

- FileStorageObserver 연결하기

        from sacred import Experiment
        from sacred.observers import FileStorageObserver
        ex = Experiment('hello_config')
        ex.observers.append(FileStorageObserver.create('my_runs'))
        
        @ex.config
        def my_config():
            recipient = "world"
            message = "Hello %s!" % recipient
        
        @ex.automain
        def my_main(message):
            print(message)

- Auth를 붙이고 싶은 경우 [authentication protocol](https://api.mongodb.com/python/current/examples/authentication.html)을 참고
- Docker로 설정하고 싶은 경우 [Docker Setup](https://sacred.readthedocs.io/en/latest/examples.html#docker-setup) 참고

### Seed 관리

- Automatic Seed

        python3 first_config.py with seed=123

- Global Seed
    - 글로벌 시드 random, np.random이 설정되있는 경우 그걸 자동으로 설정
    - main 함수 안에서 수동으로 seed를 설정하면 모두 적용됨
- Special Arguments
    - `_rnd` 와 `_seed`  인자를 통해 random number를 생성할 수 있음
	
	```	
    @ex.capture
    def do_random_stuff(_rnd, _seed):
        print(_seed)
        print(_rnd.randint(1, 100))
	```


### Logging

- Sacred는 Python logging 모듈을 사용해 정보를 저장함
- Command Line에서 실행할 때 -l ERROR 를 추가해 실행하면 ERROR 아래 로그를 출력하지 않음
    - CRITICAL, ERROR, WARNING, INFO, DEBUG 등이 있음
    - <img src="https://www.dropbox.com/s/him64bdm46dr3vk/Screenshot 2019-07-21 15.50.54.png?raw=1">
- Experiment에 로그 추가하기
    - 실험에 로그를 추가하고 싶을 경우 `_log` 인자를 사용해 로그를 추가할 수 있음

		```
        from sacred import Experiment
        from sacred.observers import FileStorageObserver
        ex = Experiment('hello_config')
        ex.observers.append(FileStorageObserver.create('my_runs'))
        
        @ex.config
        def my_config():
            recipient = "world"
            message = "Hello %s!" % recipient
        
        @ex.capture
        def some_function(_log):
            _log.info('Custom Info message!')
        
        @ex.automain
        def my_main(message):
            some_function()
            print(message)
		```
		
- Logger 커스텀하기
    - Logger를 커스텀하고 싶은 경우, experiment 정의한 후, logger를 재정의하면 됨

		```
        # custom_logger.py
        import logging
        from sacred import Experiment
        
        ex = Experiment('log_example')
        
        # set up a custom logger
        logger = logging.getLogger('mylogger')
        logger.handlers = []
        ch = logging.StreamHandler()
        formatter = logging.Formatter('[%(levelname).1s] %(name)s >> "%(message)s"')
        ch.setFormatter(formatter)
        logger.addHandler(ch)
        logger.setLevel('INFO')
        
        ex.logger = logger
        
        @ex.config
        def my_config():
            recipient = "world"
            message = "Hello %s!" % recipient
        
        @ex.capture
        def some_function(_log):
            _log.info('Custom Info message!')
        
        @ex.automain
        def my_main(message):
            some_function()
            print(message)
		```
		
    - 실행

            python3 custom_logger.py

    - <img src="https://www.dropbox.com/s/u6dmhkckvb3c6ho/Screenshot 2019-07-21 16.03.17.png?raw=1">

### Ingredients

- 어떤 Task는 다양한 실험을 실행해야 할 수 있음
- 코드 중복을 피하는 방법은 함수를 추출해 가져오는 것
    - 이런 작업을 구성해야 하면 구성 값을 모든 실험에 복사해야 함
- Ingredients는 연관된 함수의 설정을 정의하고 다른 실험에 재사용할 수 있는 방법
    - 특정 hook을 사용해 실험을 실행시킬 수도 있음
- 예제

        from sacred import Ingredient, Experiment
        
        # ================== Dataset Ingredient =======================================
        # could be in a separate file
        
        data_ingredient = Ingredient('dataset')
        
        
        @data_ingredient.config
        def cfg1():
            filename = 'my_dataset.npy'  
            normalize = True 
        
        
        @data_ingredient.capture
        def load_data(filename, normalize):
            print("loading dataset from '{}'".format(filename))
            if normalize:
                print("normalizing dataset")
                return 1
            return 42
        
        
        @data_ingredient.command
        def stats(filename, foo=12):
            print('Statistics for dataset "{}":'.format(filename))
            print('mean = 42.23')
            print('foo=', foo)
        
        
        # ================== Experiment ===============================================
        
        @data_ingredient.config
        def cfg2():
            filename = 'foo.npy'
        # 이 config가 덮어쓰기됨
        
        # add the Ingredient while creating the experiment
        ex = Experiment('my_experiment', ingredients=[data_ingredient])
        
        @ex.config
        def cfg3():
            a = 12
            b = 42
        
        @ex.named_config
        def fbb():
            a = 22
            dataset = {"filename": "AwwwJiss.py"}
        
        @ex.automain
        def run():
            data = load_data()  
        		# foo.npy
            print('data={}'.format(data))

### Custom command

- Command 실행에 따라 다른 함수가 실행되도록 할 수 있음
    - `@ex.command` 로 함수를 감싸주면 됨
		
		```
        # my_command.py
        from sacred import Experiment
        
        ex = Experiment('my_commands')
        
        
        @ex.config
        def cfg():
            name = 'kyle'
        
        
        @ex.command
        def greet(name):
            print('Hello {}! Nice to greet you!'.format(name))
        
        
        @ex.command
        def shout():
            print('WHAZZZUUUUUUUUUUP!!!????')
        
        
        @ex.automain
        def main():
            print('This is just the main command. Try greet or shout.')
		```
		
    - 실행

            python3 my_command.py greet
            # Hello kyle! Nice to greet you!
            
            python3 my_command.py shout
            # WHAZZZUUUUUUUUUUP!!!????

### 실전 적용
- 위에서 나온 Sacred 예제는 모두 공식 홈페이지를 참고했는데, 이번엔 직접 머신러닝 예제를 구현
	- 코드는 [Github](https://github.com/zzsza/TIL/tree/master/python/sacred/actual_use)에 존재
- Folder Structure

        ├── experiments : 실험 스크립트가 존재하는 폴더
        │   ├── random_forest.py
        │   └── svc.py
        └── main.py : 실험을 실행하는 main.py

- random_forest.py

        from sklearn import datasets, model_selection
        from sklearn.ensemble import RandomForestClassifier
        from sacred import Experiment
        from sacred.observers import FileStorageObserver
        from sklearn import metrics
        
        ex = Experiment('rf')
        ex.observers.append(FileStorageObserver.create('my_runs'))
        
        
        @ex.config
        def cfg():
            n_estimators=100
            seed = 42
        
        
        @ex.capture
        def get_model(n_estimators):
            return RandomForestClassifier(n_estimators)
        
        
        @ex.automain
        def run(_log):
            X, y = datasets.load_breast_cancer(return_X_y=True)
            _log.info("[INFO] Now split dataset in RF")
            X_train, X_test, y_train, y_test = model_selection.train_test_split(X, y, test_size=0.2)
            clf = get_model()
            clf.fit(X_train, y_train)
            y_pred = clf.predict(X_test)
            _log.info("[INFO] End Predict!")
        
            return metrics.accuracy_score(y_test, y_pred)

- svc.py

        from sklearn import svm, datasets, model_selection
        from sacred import Experiment
        from sacred.observers import FileStorageObserver
        
        ex = Experiment('svc')
        ex.observers.append(FileStorageObserver.create('my_runs'))
        
        
        @ex.config
        def cfg():
            C = 1.0
            gamma = 0.7
            kernel = "rbf"
            seed = 42
        
        
        @ex.capture
        def get_model(C, gamma, kernel):
            return svm.SVC(C=C, kernel=kernel, gamma=gamma)
        
        
        @ex.automain
        def run(_log):
            X, y = datasets.load_breast_cancer(return_X_y=True)
            _log.info("[INFO] Now split dataset")
            X_train, X_test, y_train, y_test = model_selection.train_test_split(X, y, test_size=0.2)
            clf = get_model()
            clf.fit(X_train, y_train)
            _log.info("[INFO] End Fit!")
            return clf.score(X_test, y_test)

- main.py

        from experiments.random_forest import ex as rf_ex
        from experiments.svc import ex as svc_ex
        
        rf_run = rf_ex.run()
        print(rf_run.config)
        print(rf_run.result)
        print(rf_run.info.keys())
        print("New experiment run!")
        rf_run2 = rf_ex.run(config_updates={"n_estimators": 10})
        print(rf_run2.config)
        print(rf_run2.result)
        
        svc_run = svc_ex.run()
        print(svc_run.config)
        print(svc_run.result)

- 실행

        python3 main.py

    - <img src="https://www.dropbox.com/s/x80agaw90m018ig/Screenshot 2019-07-21 17.25.00.png?raw=1">

- Metric
    - \_run.log\_scalar(metric_name, value, step) 이런식으로 metric을 생성해 추가할 수 있음
        - 자세한 내용은 [Metrics API](https://sacred.readthedocs.io/en/latest/collected_information.html#metrics-api) 참고
- 다음 글은 Omniboard와 연결해 설정 및 파라미터 시각화 하는 방법에 대해 소개하겠습니다 :)

### Reference
- [Sacred 공식 Github](https://github.com/IDSIA/sacred/tree/master/examples)
- 많은 실험을 저장해 사용한 예제는 [Kaggle telstra Github](https://github.com/gereleth/kaggle-telstra) 참고
- Pytorch에서 사용한 예제는 [Github](https://github.com/maartjeth/sacred-example-pytorch/blob/master/train_nn.py) 참고
- [Sacred_Deep_Learning](https://github.com/AAbercrombie0492/Sacred_Deep_Learning)

