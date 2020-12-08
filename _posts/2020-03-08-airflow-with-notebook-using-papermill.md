---
layout: post
title:  "Papermill을 사용해 Jupyter Notebook에 파라미터 주입하기(feat. airflow)"
subtitle: "airflow with jupyter notebook parameter using papermill"
categories: mlops
tags: basic airflow
comments: true
---

- Papermill Jupyter Notebook(노트북 파라미터화)에 대한 내용과 Airflow에서 활용하는 방법에 대해 작성한 글입니다
	- jupyter notebook run with parameter, run jupyter notebook with parameters, execute notebook with the custom arguments


<br />

---


### Papermill
- [[Github]](https://github.com/nteract/papermill), [[Document]](https://papermill.readthedocs.io/en/latest/)
- 다음과 같은 상황이 발생할 경우가 있음
	- 1) Jupyter Notebook에서 데이터 분석 또는 머신러닝 모델링
	- 2) 두 작업을 반복적으로 실행해야 할 경우, Python Script로 다시 파일을 수정
	- 3) Crontab, Airflow 등에 반복 작업을 실행
- 1)와 2) 사이에서 스크립트로 변환하는 작업에 시간이 적게 소요될 수도 있지만, 노트북 환경에 익숙한 사람은 오래 걸릴 수 있음(필요시 Class화를 해야할 필요도 있음)
	- Jupyter Notebook을 특정 값만 파라미터로 바꿔서 실행할 수 있는 것이 바로 Papermill
- 기능
	- Parameterize notebooks(노트북을 파라미터화)
	- Execute notebooks(노트북 실행)
		- Python, R, Julia, Scala 등에서 사용 가능
- 활용 예시
	- Jupyter Notebook에서 데이터 분석 레포트를 생성할 때, 일자만 파라미터로 받아서 => DB에서 그 일자를 토대로 쿼리를 날리고 =>  레포트 작성(시각화 등)
	- A Notebook을 실행하고 B Notebook을 실행하고 싶은 경우 Airflow와 같이 사용하면 Notebook끼리 workflow를 실행할 수 있음

<br />
---

### Papermill 설치
- 기본 설치

	```
	pip3 isntall papermill
	```

- AWS S3, GCP GCS, Azure 등을 사용하고 싶은 경우
	
	```
	pip3 install 'papermill[all]'
	```

- 필요한 것만 설치하고 싶다면

	```
	pip3 install 'papermill[s3]'
	```
	```
	pip3 install 'papermill[gcs]'
	```
	```
	pip3 install 'papermill[azure]'
	```

<br />
---

### 데이터 및 Notebook 파일 준비
- BigQuery Public Dataset에 있는 `austin_bikeshare.bikeshare_trips` 데이터 중 2019년 11월 1일 이후 데이터만 가져와서 사용
	- 소스코드와 csv 파일은 [Github](https://github.com/zzsza/TIL/tree/master/python)에 있음
- 데이터를 가져와서 하는 일은 다음과 같다(일종의 레포트라고 생각)
	- 특정 일자의 시간대별 사용량 line plot 그리기
	- 특정 일자의 구독자 타입(subscriber\_type) bar plot 그리기
	- 특정 일자의 start\_station\_id 사용량 bar plot 그리기
	- 특정 일자의 시간대별 start\_station\_id 사용량 heatmap 그리기
	- `papermill_example.ipynb`으로 생성하고, 이 파일을 Template Notebook이라 부를 예정

<img src="https://www.dropbox.com/s/pdfwabmk9n3dc4m/Screenshot%202020-03-08%2015.15.44-down.png?raw=1">

- In [2]은 read_csv지만 실제 활용할 땐 데이터베이스에서 데이터를 가져오는 부분
- In [3]는 시간 관련 값을 추출하는 부분
- In [4]는 date 변수를 저장
	- 이 In [4]를 파라미터화하면 된다
- In [5]는 date를 필터링하는 부분

<br />
---

### Papermill 실행하기	
- 1) 파라미터를 저장할 Cell에 Tag 달기
	- 1-1) View - Cell Toolbar - Tags 클릭
	- 1-2) Parameters라고 입력하고 Add tag
	- 이제 Parameters Tag가 달린 곳에 있는 date를 덮어쓸 수 있음
	- <img src="https://www.dropbox.com/s/xq6devyt9osyo1c/Screenshot%202020-03-08%2017.26.55.png?raw=1">
- 2) Papermill 실행하기
	- 1) Python API를 사용하는 방법과 2) CLI에서 하는 방법 2가지가 있음
	- 1) Python API
		- 별도의 Notebok(또는 스크립트 파일)에서 아래 코드 실행
		 
		```
		import papermill as pm

		pm.execute_notebook(
		   'papermill_example.ipynb', # Template Notebook
		   'papermill_example_output.ipynb', # Template를 사용해 Output이 저장되는 Notebook
		   parameters = dict(date='2019-11-07') # 파라미터 주입
		)
		```
		
		- <img src="https://www.dropbox.com/s/s9ye6hksywtapkm/Screenshot%202020-03-08%2014.39.25.png?raw=1">
		- 만약 Template notebook에 parameters tag가 없다면(태그를 지워보고 실행해보길), Warning 메세지가 출력된다(그래도 그냥 실행된다)
		- <img src="https://www.dropbox.com/s/gw7sx2lctce1vu8/Screenshot%202020-03-08%2014.13.33.png?raw=1">
	- 2) CLI 
		
		```
		papermill papermill_example.ipynb papermill_example_output.ipynb -p date '2019-11-07'
		```
		
	- 실행하고, Output Notebook(`papermill_example_output.ipynb`)을 보면 다음과 같이 되어있다
		- <img src="https://www.dropbox.com/s/mnz2hxrhkngwika/Screenshot%202020-03-08%2014.42.47.png?raw=1">
		- injected-parameters Tag가 있는 Cell이 추가되었고, In [7]을 보면 start_date가 2019-11-07임을 알 수 있다

<br />
---

### Papermill 기타 옵션 및 작동 방식
- 기타 옵션
	- 공식 문서엔 잘 작성되지 않은 것들이 있음
	- Python API와 CLI에서 사용할 수 있는 옵션이 살짝 다름
		- 예를 들면 CLI에선 파라미터를 yaml으로 받을 수 있고, Python API는 불가능(2020.3.8 기준)
	- Python API 기타 옵션
		- [Github](https://github.com/nteract/papermill/blob/master/papermill/execute.py#L14)
		
		```
		input_path : str
		        Path to input notebook
	    output_path : str
	        Path to save executed notebook
	    parameters : dict, optional
	        Arbitrary keyword arguments to pass to the notebook parameters
	    engine_name : str, optional
	        Name of execution engine to use
	    request_save_on_cell_execute : bool, optional
	        Request save notebook after each cell execution
	    autosave_cell_every : int, optional
	        How often in seconds to save in the middle of long cell executions
	    prepare_only : bool, optional
	        Flag to determine if execution should occur or not
	    kernel_name : str, optional
	        Name of kernel to execute the notebook against
	    progress_bar : bool, optional
	        Flag for whether or not to show the progress bar.
	    log_output : bool, optional
	        Flag for whether or not to write notebook output to the configured logger
	    start_timeout : int, optional
	        Duration in seconds to wait for kernel start-up
	    report_mode : bool, optional
	        Flag for whether or not to hide input.
	    cwd : str, optional
	        Working directory to use when executing the notebook
		```
		
	- CLI 기타 옵션
		- [Github](https://github.com/nteract/papermill/blob/master/papermill/cli.py)
		
		```
		Options:
		  -p, --parameters TEXT...        Parameters to pass to the parameters cell.
		  -r, --parameters_raw TEXT...    Parameters to be read as raw string.
		  -f, --parameters_file TEXT      Path to YAML file containing parameters.
		  -y, --parameters_yaml TEXT      YAML string to be used as parameters.
		  -b, --parameters_base64 TEXT    Base64 encoded YAML string as parameters.
		  --inject-input-path             Insert the path of the input notebook as
		                                  PAPERMILL_INPUT_PATH as a notebook
		                                  parameter.
		  --inject-output-path            Insert the path of the output notebook as
		                                  PAPERMILL_OUTPUT_PATH as a notebook
		                                  parameter.
		  --inject-paths                  Insert the paths of input/output notebooks
		                                  as
		                                  PAPERMILL_INPUT_PATH/PAPERMILL_OUTPUT_PATH
		                                  as notebook parameters.
		  --engine TEXT                   The execution engine name to use in
		                                  evaluating the notebook.
		  --request-save-on-cell-execute / --no-request-save-on-cell-execute
		                                  Request save notebook after each cell
		                                  execution
		  --autosave-cell-every INTEGER   How often in seconds to autosave the
		                                  notebook during long cell executions (0 to
		                                  disable)
		  --prepare-only / --prepare-execute
		                                  Flag for outputting the notebook without
		                                  execution, but with parameters applied.
		  -k, --kernel TEXT               Name of kernel to run.
		  --cwd TEXT                      Working directory to run notebook in.
		  --progress-bar / --no-progress-bar
		                                  Flag for turning on the progress bar.
		  --log-output / --no-log-output  Flag for writing notebook output to the
		                                  configured logger.
		  --stdout-file FILENAME          File to write notebook stdout output to.
		  --stderr-file FILENAME          File to write notebook stderr output to.
		  --log-level [NOTSET|DEBUG|INFO|WARNING|ERROR|CRITICAL]
		                                  Set log level
		  --start-timeout, --start_timeout INTEGER
		                                  Time in seconds to wait for kernel to start.
		  --execution-timeout INTEGER     Time in seconds to wait for each cell before
		                                  failing execution (default: forever)
		  --report-mode / --no-report-mode
		                                  Flag for hiding input.
		  --version                       Flag for displaying the version.
		  -h, --help                      Show this message and exit.
		```

- 작동 방식
	- parameters 셀을 찾고, 그 밑 셀에 injected-parameters라는 Tag를 가지는 새 셀을 삽입함(사용자가 지정한 값이 저장됨)
	- injected parameters 아래 셀을 실행
	- 주의할 점 : 주입되는 파라미터만 저장되기 때문에, 파라미터 저장하는 셀과 실행하는 셀을 나누는 것이 좋음
		- parameters Tag Cell에 파라미터 저장과 연산이 같이 있으면 안된다는 의미		
		- Notebook에서 date='2019-11-05'와 `filter_df = df[df['start_date'].astype(str) == date]`을 같은 Cell에 두고 Papermill을 실행시켜보면 왜 나눠야 하는지 알 수 있음
- 정리
	- Notebook 파일 작성 
	- Notebook에 파라미터만 저장하는 Cell 지정
	- Cell에 Tag 추가
	- Papermill 실행
	- [Voila](https://zzsza.github.io/development/2020/01/06/jupyter_notebook_voila_dashboard/)와 결합해 사용할 수 있을듯
	- Notebook으로 모델링하는 파일과 그걸 받아 Serving을 쉽게 할 수 있도록 만들 수 있을듯

<br />
---
	
### 궁금해서 시도한 내용	
- 궁금한 점
	- 1) 다양한 Tag로 작성해도 파라미터가 주입되나?
		- X. 첫 parameters만 사용 가능
	- 2) 파일 저장하지 않고 노트북만 실행시킬 수 있는지?
		- Notebook 안에서 모델을 훈련해서 저장하는 작업만 있다면 추가적으로 Notebook을 생성하지 않아도 됨
		- X. output_path를 지정하지 않으면 Error 발생
		- 로그 확인용으로 datetime으로 저장시키거나, 로깅을 따로 한다면 하나의 notebook 파일로 저장하면 될 듯

<br />
---

### Papermill 기타
- Conda를 사용할 경우 kernel이 없다고 오류가 발생할 수 있음
	- [Trouble Shooting](https://papermill.readthedocs.io/en/latest/troubleshooting.html) 참고
- Jupyter Lab에서 사용할 경우엔 위에서 설명한 방식을 사용하지 않음
	- Metadata를 수정
	- [Document](https://papermill.readthedocs.io/en/latest/troubleshooting.html)에 나와있음
- Cloud Storage 지원
	- CLI에서 `s3://`, `gs://`, `adl://`, `abs://` 사용 가능
	- [Github](https://github.com/nteract/papermill/tree/master#supported-name-handlers) 참고
	- 참고로 gcs는 [gcsfc](https://gcsfs.readthedocs.io/en/latest/)를 내부적으로 활용함 

<br />
---
	
	
### Airflow에서 Papermill으로 Notebook 실행하기
- Airflow에 대해 궁금하다면 [Apache Airflow - Workflow 관리 도구(1)](https://zzsza.github.io/data/2018/01/04/airflow-1/) 참고
- Apache Airflow 1.10.6부터 PapermillOperator가 추가됨
	- Airflow에 익숙하면, Operator 사용은 어렵지 않음
	- PapermillOperator 활용
	- [예제 파일](https://github.com/apache/airflow/tree/master/airflow/providers/papermill/example_dags)

	```
	from airflow import DAG
	from airflow.providers.papermill.operators.papermill import PapermillOperator
	
	default_args = {
	    'owner': 'kyle',
	    'start_date': '2019-11-05',
	    'end_date': '2019-11-10'
	}
	
	with DAG(
	    dag_id='example_papermill_operator',
	    default_args=default_args,
	    schedule_interval='0 0 * * *',
	) as dag:
		run_this = PapermillOperator(
		    task_id="run_example_notebook",
		    input_nb="papermill_example.ipynb",
			output_nb="papermill_example_output_{{ execution_date }}.ipynb",
		    parameters={"date": "{{ execution_date }}" }
		)
	```

<br />
---	
	
### 참고 자료
- [Papermill Github](https://github.com/nteract/papermill)
	- [Document](https://papermill.readthedocs.io/en/latest/)
- [Notebooks as Functions with Papermill, Netflix Video](https://www.youtube.com/watch?v=3FmBJ847_y8) : Youtube 영상
- [Apache Airflow - Workflow 관리 도구(1)](https://zzsza.github.io/data/2018/01/04/airflow-1/)
- [Ai pipelines powered by jupyter notebooks](https://www.slideshare.net/luckbr1975/ai-pipelines-powered-by-jupyter-notebooks)	
- [Introduction to Papermill](https://towardsdatascience.com/introduction-to-papermill-2c61f66bea30)
- [Automated reports with Jupyter Notebooks (using Jupytext and Papermill)](https://medium.com/capital-fund-management/automated-reports-with-jupyter-notebooks-using-jupytext-and-papermill-619e60c37330)