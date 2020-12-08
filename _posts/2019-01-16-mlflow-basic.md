---
layout: post
title:  "MLflow 소개 및 Tutorial"
subtitle: "MLflow 소개 및 Tutorial"
categories: mlops
tags: mlflow
comments: true
---

- 머신러닝 라이프 사이클을 관리할 수 있는 오픈소스인 MLflow에 대한 소개 및 간단한 Tutorial에 대한 글입니다

---

## MLflow
- MLflow는 End to End로 머신러닝 라이프 사이클을 관리할 수 있는 오픈소스
	- 데이터브릭스에서 만듬
	- [데이터브릭스 hosted version](https://databricks.com/mlflow) 
- 주요 기능
	- 1) MLflow Tracking
		- 파라미터와 결과를 비교하기 위해 실험 결과를 저장
	- 2) MLflow Projects
		- 머신러닝 코드를 재사용 가능하고 재현 가능한 형태로 포장
		- 포장된 형태를 다른 데이터 사이언티스트가 사용하거나 프러덕션에 반영 
	- 3) MLflow Models
		- 다양한 ML 라이브러리에서 모델을 관리하고 배포, Serving, 추론
- REST API, CLI를 통해 모든 기능에 액세스 할 수 있기 때문에 모든 라이브러리, 프로그래밍 언어에서 사용 가능
- API는 Python, R, Java 존재


### 설치 및 예제 코드 준비
- virtualenv 설정
	
	```
	virtualenv env
	source env/bin/activate
	```
- 설치
	
	```
	pip3 install mlflow
	# virtualenv 없이 pip3 install mlflow하니 [Errno 13] Permission denied: '/usr/local/man' 발생
	```
		
- 예제 코드 clone

	```
	git clone https://github.com/mlflow/mlflow
	cd mlflow/examples
	```	
	
### Tracking
- [Tracking Document](https://mlflow.org/docs/latest/tracking.html#tracking)
- Code Version
	- Git commit hash used to execute the run, if it was executed from an MLflow Project.
- Start & End Time
	- Start and end time of the run
- Source
	- Name of the file executed to launch the run, or the project name and entry point for the run if the run was executed from an MLflow Project.
Parameters
	- Key-value input parameters of your choice. Both keys and values are strings.
- Metrics
	- Key-value metrics where the value is numeric. Each metric can be updated throughout the course of the run (for example, to track how your model’s loss function is converging), and MLflow will record and let you visualize the metric’s full history.
Artifacts
	- Output files in any format. For example, you can record images (for example, PNGs), models (for example, a pickled scikit-learn model), or even data files (for example, a Parquet file) as artifacts.


- 예제 코드

	```
	# mlflow_tracking.py
	import os
	from random import random, randint
	
	from mlflow import log_metric, log_param, log_artifacts
	
	if __name__ == "__main__":
	    print("Running mlflow_tracking.py")
	
	    log_param("param1", randint(0, 100))
	
	    log_metric("foo", random())
	    log_metric("foo", random() + 1)
	    log_metric("foo", random() + 2)
	
	    if not os.path.exists("outputs"):
	        os.makedirs("outputs")
	    with open("outputs/test.txt", "w") as f:
	        f.write("hello world!")
	
	    log_artifacts("outputs")
	```
	
- 실행

	```
	python3 mlflow_tracking.py
	>>> Running mlflow_tracking.py
	```	
	
- 웹 UI 실행
	- 기본적으로 프로그램을 실행할 때마다 tracing API가 mlruns 디렉토리에 파일을 작성. 그 후 웹 UI 실행 가능 	

		```
		mlflow ui
		```

	<img src="https://www.dropbox.com/s/jzrxbe2h3z6mwvh/Screenshot%202019-01-16%2000.18.20.png?raw=1">

	
### MLflow Project 실행
- [MLflow Projects Document](https://mlflow.org/docs/latest/projects.html) 
- default 설정은 디펜던시가 conda에 있음
- conda를 사용하고 싶지 않을 경우 `--no-conda` 추가

	```
	mlflow run --no-conda git@github.com:mlflow/mlflow-example.git -P alpha=5
	>>> Elasticnet model (alpha=5.000000, l1_ratio=0.100000):
	RMSE: 0.8594260117338262
	MAE: 0.6480675144220314
	R2: 0.046025292604596424
  
	mlflow run tutorial -P alpha=0.5
	```

- Experiments list 확인

	```
	mlflow experiments list
	```


### Saving and Serving Model
- [MLflow Models Document](https://mlflow.org/docs/latest/models.html)
- 학습
	
	```
	python3 sklearn_logistic_regression/train.py
	```
	
	```
	# train.py
	import numpy as np
	from sklearn.linear_model import LogisticRegression

	import mlflow
	import mlflow.sklearn
	
	if __name__ == "__main__":
	    X = np.array([-2, -1, 0, 1, 2, 1]).reshape(-1, 1)
	    y = np.array([0, 0, 1, 1, 1, 0])
	    lr = LogisticRegression()
	    lr.fit(X, y)
	    score = lr.score(X, y)
	    print("Score: %s" % score)
	    mlflow.log_metric("score", score)
	    mlflow.sklearn.log_model(lr, "model")
	    print("Model saved in run %s" % mlflow.active_run().info.run_uuid)
	```


- Serving	
	- train.py를 실행하면 RUN ID가 나오는데, 그걸 토대로 아래처럼 serving
	
	```
	mlflow pyfunc serve -r <RUN_ID> -m model --no-conda --port 1234
	```
	
- curl
	
	```
	curl -d '{"columns":["x"], "data":[[1], [-1]]}' -H 'Content-Type: application/json; format=pandas-split' -X POST localhost:1234/invocations
	```	

---

## Tutorial
- linear regression 모델 학습
- 재사용, 재생산 가능하도록 패키징
- 간단한 HTTP 서버에 배포

### 모델 학습
- `examples/sklearn_elasticnet_wine/train.py <alpha> <l1_ratio>` 여러번 테스트
	
	```
	import os
	import sys
	
	import pandas as pd
	import numpy as np
	from sklearn.metrics import mean_squared_error, mean_absolute_error, r2_score
	from sklearn.model_selection import train_test_split
	from sklearn.linear_model import ElasticNet
	
	import mlflow
	import mlflow.sklearn
	# Run from the root of MLflow
	# Read the wine-quality csv file
	wine_path = os.path.join(os.path.dirname(os.path.abspath(__file__)), "wine-quality.csv")
	data = pd.read_csv(wine_path)
	
	# Split the data into training and test sets. (0.75, 0.25) split.
	train, test = train_test_split(data)
	
	# The predicted column is "quality" which is a scalar from [3, 9]
	train_x = train.drop(["quality"], axis=1)
	test_x = test.drop(["quality"], axis=1)
	train_y = train[["quality"]]
	test_y = test[["quality"]]
	
	alpha = float(sys.argv[1]) if len(sys.argv) > 1 else 0.5
	l1_ratio = float(sys.argv[2]) if len(sys.argv) > 2 else 0.5
	
	with mlflow.start_run():
	    lr = ElasticNet(alpha=alpha, l1_ratio=l1_ratio, random_state=42)
	    lr.fit(train_x, train_y)

    predicted_qualities = lr.predict(test_x)

    (rmse, mae, r2) = eval_metrics(test_y, predicted_qualities)

    print("Elasticnet model (alpha=%f, l1_ratio=%f):" % (alpha, l1_ratio))
    print("  RMSE: %s" % rmse)
    print("  MAE: %s" % mae)
    print("  R2: %s" % r2)

    mlflow.log_param("alpha", alpha)
    mlflow.log_param("l1_ratio", l1_ratio)
    mlflow.log_metric("rmse", rmse)
    mlflow.log_metric("r2", r2)
    mlflow.log_metric("mae", mae)

    mlflow.sklearn.log_model(lr, "model")
    ```

- 웹 서버 접속
	
	```
	mlflow ui
	```
	
- 웹 서버 탐색
	- <img src="https://www.dropbox.com/s/n5b3yb8gzqfgm3s/Screenshot%202019-01-16%2001.40.42.png?raw=1">

### 학습 코드 패키징
- `MLproject`란 파일 생성해서 아래와 같이 설정

	```
	name: tutorial
	
	conda_env: conda.yaml
	
	entry_points:
	  main:
	    parameters:
	      alpha: float
	      l1_ratio: {type: float, default: 0.1}
	    command: "python train.py {alpha} {l1_ratio}"
	```	
- `conda.yaml` 작성 (pip로 할 경우 사용하지 못하는가?)

	```
	name: tutorial
	channels:
	  - defaults
	dependencies:
	  - numpy=1.14.3
	  - pandas=0.22.0
	  - scikit-learn=0.19.1
	  - pip:
	    - mlflow
	```	
- `mlflow run tutorial -P alpha=0.42`으로 실행
- 만약 Github에서 올라간 소스를 바로 실행하고 싶으면 아래와 같이 사용 가능	

	```
	mlflow run git@github.com:mlflow/mlflow-example.git -P alpha=0.42
	```
	
	
### Serving the Model
- REST API를 통한 실시간 서비스 또는 Apache Spark의 배치 inference 같은 다양한 도구에서 사용할 수 있는 형식이 존재
- 예제 코드에선 선형회귀 모델을 학습한 후 MLflow 함수가 모델을 아티팩트로 저장함

	```
	mlflow.sklearn.log_model(lr, "model")
	```
	
- 웹 UI에서 확인하면 설정 확인 가능
- 아래 명령어를 통해 serving

	```
	mlflow pyfunc serve /Users/mlflow/mlflow-prototype/mlruns/0/7c1a0d5c42844dcdb8f5191146925174/artifacts/model -p 1234
	```	
	
- API Test
	
	```
	curl -X POST -H "Content-Type:application/json; format=pandas-split" --data '{"columns":["alcohol", "chlorides", "citric acid", "density", "fixed acidity", "free sulfur dioxide", "pH", "residual sugar", "sulphates", "total sulfur dioxide", "volatile acidity"],"data":[[12.8, 0.029, 0.48, 0.98, 6.2, 29, 3.33, 1.2, 0.39, 75, 0.66]]}' http://127.0.0.1:1234/invocations
	>>> {"predictions": [6.379428821398614]}
	```	

### 클라우드에서 배포
- AzureML
	
	```
	mlflow azureml export -m <model path> -o test-output
	```
- SageMaker

	```
	mlflow sagemaker build-and-push-container
	mlflow sagemaker deploy <parameters>
	```	
		
	
### Reference
- [MLflow Document](https://mlflow.org/docs/latest/index.html)
- [이동진님의 mlflow](https://speakerdeck.com/dongjin/introducing-mlflow)
