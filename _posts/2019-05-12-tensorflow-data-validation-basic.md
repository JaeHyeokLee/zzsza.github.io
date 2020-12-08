---
layout: post
title:  "Tensorflow Data Validation 사용하기"
subtitle: "Tensorflow Data Validation 사용하기"
categories: mlops
tags: tfx
comments: true
---

- TFX에서 Data Validation을 담당하고 있는 TensorFlow Data Validation(TFDV)에 대한 글입니다


### TFDV
- [Github Repo](https://github.com/tensorflow/data-validation)
- TensorFlow Data Validation는 데이터를 더 쉽게 이해하고 점검할 수 있도록 도와주는 라이브러리
- Google에서 매일 페타바이트의 데이터를 분석하고 점검할 때 사용
- Train, Test Data의 요약 통계, 분포를 쉽게 비교 가능
- 필수 값, 데이터에 대한 기대치를 의미하는 데이터 스키마 생성
- 지정된 조건을 벗어나는 경우(예시 : int 타입을 예상했는데 float이 들어오는 경우) 예외를 식별
- Python 2.7, 3.5만 지원
- Mac OS(시에라) 이상, Ubuntu 16.04 이상 지원
	- 윈도우는 추후 지원 예정 
- 내부적으로 [Facet](https://github.com/PAIR-code/facets), [Apache Beam](https://beam.apache.org/) 사용
- 회사 업무시, 데이터 경진대회(캐글 등)에서 유용할 듯
- 이 글은 0.13.1 버전 기준으로 작성했습니다. 추후 계속 수정될 예정입니다
- 코드는 모두 Github에 올렸습니다. [Nbviewer](https://nbviewer.jupyter.org/github/zzsza/tfx-tutorial/blob/master/data-validation/All-Features-Example.ipynb?flush_cache=true)로 확인 부탁드려요!

### 기능
- Statistics 생성 및 시각화
	- Train과 Test셋 데이터 분포 동시에 확인
- 스키마 추론
- 데이터 검증
- Drift와 Skew 체크


### 설치
```
pip install tensorflow-data-validation
```

- 설치한 후, 확인

	```
	import sys, os
	import tempfile, urllib, zipfile
	import tensorflow_data_validation as tfdv
	print('TFDV version: {}'.format(tfdv.version.__version__))
	```
	
### 데이터 준비
- 시카고 택시 데이터를 다운로드 후, 압축 해제

	```
	BASE_DIR = tempfile.mkdtemp()
	DATA_DIR = os.path.join(BASE_DIR, 'data')
	OUTPUT_DIR = os.path.join(BASE_DIR, 'chicago_taxi_output')
	TRAIN_DATA = os.path.join(DATA_DIR, 'train', 'data.csv')
	EVAL_DATA = os.path.join(DATA_DIR, 'eval', 'data.csv')
	SERVING_DATA = os.path.join(DATA_DIR, 'serving', 'data.csv')
	
	zip, headers = urllib.urlretrieve('https://storage.googleapis.com/tfx-colab-datasets/chicago_data.zip')
	zipfile.ZipFile(zip).extractall(BASE_DIR)
	zipfile.ZipFile(zip).close()
	```
	
### Statistics 생성 및 시각화
- csv, dataframe, tfrecord을 통해 Statistics를 생성할 수 있음

	```
	[methods for methods in dir(tfdv) if "generate" in methods]
	>>> ['generate_statistics_from_csv',
	 	 'generate_statistics_from_dataframe',
		 'generate_statistics_from_tfrecord']
	```
	
- Statistics 생성

	```
	train_stats = tfdv.generate_statistics_from_csv(data_location=TRAIN_DATA)
	# method : tfdv.generate_statistics_from_csv(data_location, column_names=None, delimiter=',', output_path=None, stats_options=<tensorflow_data_validation.statistics.stats_options.StatsOptions object at 0x14436a110>, pipeline_options=None)
	```

	- train_stats를 확인해보면 feature별로 다양한 통계치를 가지고 있음
	
- 시각화

	```
	tfdv.visualize_statistics(train_stats)
	# method : tfdv.visualize_statistics(lhs_statistics, rhs_statistics=None, lhs_name='lhs_statistics', rhs_name='rhs_statistics)
	```	
	
	<img src="https://www.dropbox.com/s/wuudyhmvisv9d3e/Screenshot%202019-05-12%2013.44.27.png?raw=1">
	
	- Interactive하게 직접 만져보고 싶으신 분은 웹에서 제 [Github nbviewer](https://nbviewer.jupyter.org/github/zzsza/tfx-tutorial/blob/master/data-validation/All-Features-Example.ipynb?flush_cache=true) 참고!
	- Numeric Features
		- 맨 왼쪽에 Sort By 옵션
			- Non-uniformity 
			- <img src="https://www.dropbox.com/s/gv4a1ljc3a2whww/Screenshot%202019-05-12%2013.50.49.png?raw=1">
			- Amount missing/zero
			- <img src="https://www.dropbox.com/s/e3odgkz2fom61v1/Screenshot%202019-05-12%2013.51.11.png?raw=1"> 	
		- 오른쪽에 Chart to show은 분포, Quantiles, Value list length 등으로 볼 수 있고, 클릭으로 log로 변환 가능
	- Categorical Feature도 아래처럼 시각화됨
		- <img src="https://www.dropbox.com/s/3v0v13mx83kxt06/Screenshot%202019-05-12%2013.54.21.png?raw=1">
		- SHOW RAW DATA를 누르면 카테고리 데이터 count 결과 보여줌
- Train과 Eval Data 동시에 시각화
	- Kaggle에서 매우 유용할 듯 
	
	```
	eval_stats = tfdv.generate_statistics_from_csv(data_location=EVAL_DATA)
	tfdv.visualize_statistics(lhs_statistics=eval_stats, rhs_statistics=train_stats,
                          lhs_name='EVAL_DATASET', rhs_name='TRAIN_DATASET')
	```	
	
	<img src="https://www.dropbox.com/s/yp05oqr5pz5mcag/Screenshot%202019-05-12%2013.57.04.png?raw=1">
	
	- 이제 파란색은 Eval data, 주황색은 Train data
	- 우측 Charts to show에서 percentages를 클릭시 Eval과 Train의 비율을 포개서 보여줌
		- <img src="https://www.dropbox.com/s/p9uzhb7b6xauwgr/Screenshot%202019-05-12%2013.59.00.png?raw=1">


### 스키마 추론
- `tfdv.infer_schema`로 스키마를 추론한 후, `tfdv.display_shcema`로 출력
- infer schema 결과 스키마 Protocol buffer가 생성됨

	```
	schema = tfdv.infer_schema(statistics=train_stats)
	# method : tfdv.infer_schema(statistics, infer_feature_shape=True, max_string_domain_size=100)
	
	tfdv.display_schema(schema=schema)
	```	
	
	<img src="https://www.dropbox.com/s/pukezj5i0v7tp3g/Screenshot%202019-05-12%2014.02.39.png?raw=1">
	
	- 각 Feature별 Type, Presence, Valency, Domain 출력 
- Feautre의 속성을 바꾸고 싶을 경우 `tfdv.get_feature`로 가져온 후 수정

	```
	tfdv.get_feature(schema, 'payment_type').name = "oh"
	schema 
	# find name: 'oh'
	```
	
	- 다시 복구

	```
	tfdv.get_feature(schema, 'oh').name = "payment_type"
	```
	

### 데이터 검증
- Validation 기능
- 예를 들어 Train엔 없는 Categorical Value가 Test에 존재한다면? 

	```
	anomalies = tfdv.validate_statistics(statistics=eval_stats, schema=schema)
	tfdv.display_anomalies(anomalies)
	```
	
	<img src="https://www.dropbox.com/s/mqhkt513q5ufwcp/Screenshot%202019-05-12%2014.51.21.png?raw=1">
	
	- company, payment type feature에 예상하지 못한 값이 있음
	- 문제 해결을 위해 min_domain_mass 제약 조건을 추가하고, value를 수동으로 추가함

	```
	# Relax the minimum fraction of values that must come from the domain for feature company.
	company = tfdv.get_feature(schema, 'company')
	company.distribution_constraints.min_domain_mass = 0.9
	
	# Add new value to the domain of feature payment_type.
	payment_type_domain = tfdv.get_domain(schema, 'payment_type')
	payment_type_domain.value.append('Prcard')
	
	# Validate eval stats after updating the schema 
	updated_anomalies = tfdv.validate_statistics(eval_stats, schema)
	tfdv.display_anomalies(updated_anomalies)
	```
	
- Serving시 데이터 검증
	- requirements를 정의해 특정 문제가 발생하면 자동으로 수정될 수 있게 보조해주는 기능도 존재
	
	```
	serving_stats = tfdv.generate_statistics_from_csv(SERVING_DATA)
	serving_anomalies = tfdv.validate_statistics(serving_stats, schema)

	tfdv.display_anomalies(serving_anomalies)
	``` 

	<img src="https://www.dropbox.com/s/lmdgwnk79ii3n5s/Screenshot%202019-05-12%2015.04.57.png?raw=1">
	
	- tips 컬럼은 완벽하게 missing이고 trip_seconds는 FLOAT 타입을 예상했는데 INT 타입이 들어옴
	- 타입 문제 해결 방법
		- `infer_type_from_schema=True`인 StatsOptions를 생성한 후, generate_statistics_from_csv에 추가
		
		```
		options = tfdv.StatsOptions(schema=schema, infer_type_from_schema=True)
		serving_stats = tfdv.generate_statistics_from_csv(SERVING_DATA, stats_options=options)
		serving_anomalies = tfdv.validate_statistics(serving_stats, schema)

		tfdv.display_anomalies(serving_anomalies)
``` 

	- tips feature가 serving 데이터에 없는 문제 해결 방법
		- 스키마 환경을 TRAINING과 SERVING으로 생성한 후, SERVING시 tips 데이터가 없는 것을 명시
		
		```
		# All features are by default in both TRAINING and SERVING environments.
		schema.default_environment.append('TRAINING')
		schema.default_environment.append('SERVING')
		
		# Specify that 'tips' feature is not in SERVING environment.
		tfdv.get_feature(schema, 'tips').not_in_environment.append('SERVING')
		
		serving_anomalies_with_env = tfdv.validate_statistics(
		    serving_stats, schema, environment='SERVING')
		
		tfdv.display_anomalies(serving_anomalies_with_env)
		```
	
### Drift와 Skew 체크
- `tfdv.validate_statistics()` method를 사용해 체크
- Drift
	- Drift detection은 Categorical 데이터 및 데이터의 연속 기간(N, N+1) 사이(예를 들면 서로 다른 날의 훈련 데이터 사이)에서 지원
	- [L-infinity distnace](https://en.wikipedia.org/wiki/Chebyshev_distance)로 Drift를 표현하고 허용값보다 높으면 경고를 받을 수 있음
	- 정확한 거리를 설정하는 것은 도메인 지식과 실험을 필요로하는 반복 프로세스
- Skew
	- Schema Skew
		- 같은 스키마를 가지지 않을 때
	- Feature Skew
		- Feature 생성 로직이 변경될 때
	- Distribution Skew
		- Train, Serving 데이터 분포가 다를 경우 

	```
	# Add skew comparator for 'payment_type' feature.
	payment_type = tfdv.get_feature(schema, 'payment_type')
	payment_type.skew_comparator.infinity_norm.threshold = 0.01
	
	# Add drift comparator for 'company' feature.
	company=tfdv.get_feature(schema, 'company')
	company.drift_comparator.infinity_norm.threshold = 0.001
	
	skew_anomalies = tfdv.validate_statistics(train_stats, schema,
	                                          previous_statistics=eval_stats,
	                                          serving_statistics=serving_stats)
	
	tfdv.display_anomalies(skew_anomalies)
	```

<img src="https://www.dropbox.com/s/2gsxvyl6rke62iq/Screenshot%202019-05-12%2015.18.46.png?raw=1">

- payment_type이 train / serving시 거리가 매우 큼
- company가 과거와 현재의 거리가 매우 큼
- 위 예시에선 의도적으로 threshold값을 낮게 설정해 오류가 발생하도록 한 것이기 때문에 따로 수정하지 않음


### 스키마 저장
- 스키마를 검토하고 큐레이션이 되었으니 "frozen"(고정된) 상태를 반영하도록 Protocol Buffer에 저장

	```
	from tensorflow.python.lib.io import file_io
	from google.protobuf import text_format
	
	file_io.recursive_create_dir(OUTPUT_DIR)
	schema_file = os.path.join(OUTPUT_DIR, 'schema.pbtxt')
	tfdv.write_schema_text(schema, schema_file)
	
	!cat {schema_file}
	```

### 데이터 형태가 다를 경우
- 현재 TFRecord, CSV, Dataframe만 지원하는데 그 외의 데이터 타입을 사용한다면 Apache Beam의 `PTransform`을 사용해 데이터를 가공할 수 있음
- [링크](https://www.tensorflow.org/tfx/data_validation/get_started#writing_custom_data_connector) 참고하면 예시가 나와있음


### 구글 클라우드에서 사용하기
- 설치

	```
	pip download tensorflow_data_validation \
	--no-deps \
	--platform manylinux1_x86_64 \
	--only-binary=:all:
	```

- 예시 코드 snippet

	```
	import tensorflow_data_validation as tfdv
	from apache_beam.options.pipeline_options import PipelineOptions, GoogleCloudOptions, StandardOptions, SetupOptions
	
	PROJECT_ID = ''
	JOB_NAME = ''
	GCS_STAGING_LOCATION = ''
	GCS_TMP_LOCATION = ''
	GCS_DATA_LOCATION = ''
	# GCS_STATS_OUTPUT_PATH is the file path to which to output the data statistics
	# result.
	GCS_STATS_OUTPUT_PATH = ''
	
	PATH_TO_WHL_FILE = ''
	
	
	# Create and set your PipelineOptions.
	options = PipelineOptions()
	
	# For Cloud execution, set the Cloud Platform project, job_name,
	# staging location, temp_location and specify DataflowRunner.
	google_cloud_options = options.view_as(GoogleCloudOptions)
	google_cloud_options.project = PROJECT_ID
	google_cloud_options.job_name = JOB_NAME
	google_cloud_options.staging_location = GCS_STAGING_LOCATION
	google_cloud_options.temp_location = GCS_TMP_LOCATION
	options.view_as(StandardOptions).runner = 'DataflowRunner'
	
	setup_options = options.view_as(SetupOptions)
	# PATH_TO_WHL_FILE should point to the downloaded tfdv wheel file.
	setup_options.extra_packages = [PATH_TO_WHL_FILE]
	
	tfdv.generate_statistics_from_tfrecord(GCS_DATA_LOCATION,
	                                       output_path=GCS_STATS_OUTPUT_PATH,
	                                       pipeline_options=options)
	```                                       
	                                       
### 언제 TFDV를 사용해야할까?
- 갑자기 이상한 feature가 들어오는지 확인하고 싶을 경우
- Decision surface에서 모델이 훈련 잘되었는지 확인하고 싶을 경우
- feature engineering 실수 방지하고 싶을 경우

---

## Facets
- 사실 TFDV의 데이터 분포 보여주는 부분은 [Facets](https://pair-code.github.io/facets/)으로 이루어져 있음
- Facets은 현재 2가지 시각화를 제공하는데, 1) FACETS OVERVIEW가 위에서 TFDV가 보여준 시각화고 2) FACETS DIVE로 다양한 양의 데이터를 한번에 인터랙티브하게 시각화해줌
- FACETS DIVE는 아래처럼 시각화됨
	- <img src="https://www.dropbox.com/s/rb6nllzw1oqamun/Screenshot%202019-05-12%2015.34.51.png?raw=1">
	- 데이터를 연령대별 직업별로 보여줌
	- Quick Draw 데이터셋을 인터렉티브하게 보려면 [링크](https://pair-code.github.io/facets/quickdraw.html) 참고
- Colab에서 사용하는 예제는 [Github](https://github.com/PAIR-code/facets/blob/master/colab_facets.ipynb)에 나와있음
	- 데이터를 데이터프레임으로 불러온 후, to_json을 사용해 json으로 변경
	- 그 후 HTML Template을 사용해 노트북에서 볼 수 있도록 출력
	
	```
	# Load UCI census train and test data into dataframes.
	import pandas as pd
	features = ["Age", "Workclass", "fnlwgt", "Education", "Education-Num", "Marital Status",
	            "Occupation", "Relationship", "Race", "Sex", "Capital Gain", "Capital Loss",
	            "Hours per week", "Country", "Target"]
	train_data = pd.read_csv(
	    "https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.data",
	    names=features,
	    sep=r'\s*,\s*',
	    engine='python',
	    na_values="?")
	test_data = pd.read_csv(
	    "https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.test",
	    names=features,
	    sep=r'\s*,\s*',
	    skiprows=[0],
	    engine='python',
	    na_values="?")
	    
    # Display the Dive visualization for the training data.
	from IPython.core.display import display, HTML
	
	jsonstr = train_data.to_json(orient='records')
	HTML_TEMPLATE = """
	        <script src="https://cdnjs.cloudflare.com/ajax/libs/webcomponentsjs/0.7.24/webcomponents-lite.js"></script>
	        <link rel="import" href="https://raw.githubusercontent.com/PAIR-code/facets/master/facets-dist/facets-jupyter.html">
	        <facets-dive id="elem" height="600"></facets-dive>
	        <script>
	          var data = {jsonstr};
	          document.querySelector("#elem").data = data;
	        </script>"""
	html = HTML_TEMPLATE.format(jsonstr=jsonstr)
	display(HTML(html))
	``` 	

### Refenrece
- [TFDV Github](https://github.com/tensorflow/data-validation)
- [TFDV Document](https://www.tensorflow.org/tfx/data_validation/get_started)
- [Facets Github](https://github.com/PAIR-code/facets)
