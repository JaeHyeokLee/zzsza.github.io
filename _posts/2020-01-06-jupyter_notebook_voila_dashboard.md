---
layout: post
title:  "Voila를 사용해 Jupyter Notebook Dashboard 만들기"
subtitle: "jupyter notebook에서 voila를 사용해 대시보드 만들기"
categories: development
tags: python
comments: true
---

- Jupyter notebook에서 voila를 사용해 대시보드 만드는 방법에 대해 작성한 글입니다
	- R의 Shiny처럼 Python Jupyter Notebook에선 Voila를 사용하면 빠르게 웹에 대시보드를 띄울 수 있습니다


---

### Voila를 사용하게 된 이유
- 대시보드는 다양한 대체 수단이 많음
	- Tableau, 스프레드시트, Superset, Metabase 등
	- 대부분의 경우엔 BI 도구를 활용해도 충분함
- 종종 발생하는 경우
	- 1) 지도에 데이터를 뿌려야 할 경우, 지도 표현을 잘 제공한다면 문제되지 않지만 지도를 잘 지원해주는 BI 도구는 적음
	- 2) 파이썬으로 전처리를 해야할 경우
		- 위 두가지 경우 Jupyter Notebook에 코드를 작성하고 주기적으로 html으로 저장할 수 있음
		- 그러나 html이 아닌 ipywidget 등을 사용해 특정 시점의 데이터를 볼 수 있도록 만들려면 노트북에서 결과를 한번 실행해야 함(html 추출시 ipywidget은 같이 추출되지 않음)
	- 3) 노트북에서 시각화, 지표 확인을 모두 하고싶은 경우
- Voila를 사용하면 ipywidget과 노트북을 같이 렌더링해줌
	- [Dash](https://plot.ly/dash/)나 [julyterlab-dash](https://github.com/plotly/jupyterlab-dash)도 있지만 쉬운 사용성은 아니라 생각해 Voila를 선택함
- [블로그 글](https://blog.jupyter.org/voil%C3%A0-is-now-an-official-jupyter-subproject-87d659583490)에 따르면 Jupyter의 하위 프로젝트로 통합되었다고 함
	- 아직 0.1.20 버전이지만 추후 더 발전할 가능성이 있다고 생각

<br />

---

<br />


### Voila의 특징
- 1) Jupyter Notebook 결과를 쉽게 웹에 띄울 수 있음
- 2) Ipywidget, Ipyleaflet 등 사용 가능
- 3) Jupyter Notebook의 Extension 있음(=노트북에서 바로 대시보드로 변환 가능)
- 4) Python, Julia, C++ 코드 지원
- 5) 고유의 템플릿을 생성 가능
	
<br />

---

<br />


### 설치
- Mac OS 기준
- jupyter client 업그레이드
	- cannot import name 'secure\_write' from 'jupyter\_core.paths' 에러가 발생할 경우 jupyter\_client 업그레이드

	```
	pip3 install --upgrade jupyter_client
	```

- pip로 설치	

	```
	pip3 install voila
	```

- JupyterLab preview extension 설치(JupyterLab 사용시만)

	```
	jupyter labextension install @jupyter-voila/jupyterlab-preview 
	```
	
	- 혹시 `jupyter-labextension` not found Error가 발생한다면 jupyterlab을 설치하지 않은 것

		```
		pip3 install jupyterlab
		```

- notebook이나 jupyter_server extension 설치

	```
	jupyter serverextension enable voila --sys-prefix
	```

- 아래 명령어로 voila/extension이 enabled인지 확인

	```
	jupyter nbextension list
	
	# jupyter_server의 경우
	jupyter serverextension list
	```

<br />

---

<br />

### 사용 방법
- 간단한 Jupyter Notebook 파일 생성(이름 : voila_test)

	```
	import ipywidgets as widgets
	
	slider = widgets.FloatSlider(description='$x$', value=4)
	text = widgets.FloatText(disabled=True, description='$x^2$')
	
	def compute(*ignore):
	    text.value = str(slider.value ** 2)
	
	slider.observe(compute, 'value')
	
	widgets.VBox([slider, text])
	
	
	import pandas as pd
	
	iris = pd.read_csv('https://raw.githubusercontent.com/mwaskom/seaborn-data/master/iris.csv')
	iris.tail()
	```
	
	- <img src="https://www.dropbox.com/s/j860wjyczof67qn/Screenshot%202020-01-05%2023.35.54.png?raw=1">

- 1) voila로 노트북 렌더링(코드 안보임)

	```
	voila voila_test.ipynb
	```
	
	- localhost:8866로 접근하면 아래 결과를 확인할 수 있음
	- <img src="https://www.dropbox.com/s/nwadtjn90g91y10/Screenshot%202020-01-05%2023.38.04.png?raw=1">

- 2) voila로 노트북 렌더링(코드 보임)
	- `--strip_sources=False` 조건을 주면 기존 노트북의 코드도 보임(셀 실행은 불가능)

	```
	voila voila_test.ipynb --strip_sources=False
	```
	
	- <img src="https://www.dropbox.com/s/9kpk06i1iopb8p5/Screenshot%202020-01-05%2023.50.44.png?raw=1">

- 3) voila 폴더 실행
	
	```
	voila
	```
	
	- 현재 폴더에 있는 파일을 voila로 실행할 수 있음
	- <img src="https://www.dropbox.com/s/x9qxh3ng2m5vggh/Screenshot%202020-01-05%2023.49.17.png?raw=1">

- 4) Notebook의 Extension 클릭
	- 노트북 실행 후 Voila 클릭하면 1)과 같은 결과가 바로 보임
	- <img src="https://www.dropbox.com/s/197ea45xswots7y/Screenshot%202020-01-05%2023.45.18.png?raw=1">
	

- 옵션이 더 궁금하다면 터미널에서 아래 명령어 입력

	```
	voila --help
	```

<br />

---

<br />

### 사용하며 체득한 Tip
- Voila 실행시 셀 타임아웃 제한이 있음
	- 기본적으로 한 셀을 실행하는데 30초 소요되면 Timeout Error 발생(터미널 로그에만 남고 웹은 계속 진행중 처럼 나옴)
	- voila 실행시 아래 명령어를 사용해 타임아웃을 늘릴 수 있음
	- [참고한 Github Pull request](https://github.com/voila-dashboards/voila/pull/240
)

	```
	voila --ExecutePreprocessor.timeout=180
	```

- 보안 이슈 해결하기
	- 해당 [이슈](https://github.com/voila-dashboards/voila/issues/216)를 보면 oauth 기능을 추가할 예정인듯
	- [oauthenticator](https://github.com/jupyterhub/oauthenticator)를 사용해 직접 만들까 고민하다 Notebook의 암호를 사용하는 방법을 생각함
	- 암호 생성
		- 터미널에서 Python3 실행 후, 아래 커맨드 입력

		```
		from IPython.lib import passwd
		passwd()
		```
		
		- 'sha1:~~' 복사하기
		
	- jupyter\_notebook\_config.py 생성

		```
		vi ~/.jupyter/jupyter_notebook_config.py
		
		c = get_config()
		c.NotebookApp.password = 'sha1:~~~ 붙여넣기'
		```
		
	- notebook을 재실행하면 암호 입력하게 됨

- 특정 nbextension이 404 발생하는 경우
	- voila로 직접 노트북 파일 실행시 특정 nbextension(pydeck쪽)이 404가 발생해서 아래 명령어로 해결

	```
	voila your_notebook.ipynb --enable_nbextensions=True
	```
	
	- jupyter notebook extension에서 voila를 실행할 경우엔 인자를 줄 수 없고, notebook 실행시 옵션을 줘야함. [[참고 문서]](https://voila.readthedocs.io/en/stable/customize.html#configure-voila-for-the-jupyter-server)

	```
	jupyter notebook --ExecutePreprocessor.timeout=180 --VoilaConfiguration.enable_nbextensions=True
	```

<br />

---

<br />


### 사용 예시
- [Voila Gallery](https://voila-gallery.org/services/gallery/)에 여러 예시가 존재함

<img src="https://www.dropbox.com/s/jssv5xfyznzb2yz/Screenshot%202020-01-06%2000.05.26.png?raw=1">

- [machine-learning-interactive-visualization](https://github.com/dhaitz/machine-learning-interactive-visualization)이 눈에 띄어서 소스를 clone한 후 실행해봄
	- <img src="https://www.dropbox.com/s/fe3wsipsq6vkttq/Screenshot%202020-01-06%2000.35.21.gif?raw=1">

- [bqplot](https://github.com/bloomberg/bqplot)도 Voila를 사용해 쉽게 대시보드화 할 수 있음
- [volia-vuetify](https://github.com/voila-dashboards/voila-vuetify)엔 VuetifyJS를 사용한 대시보드 템플릿을 볼 수 있음
	- <img src="https://user-images.githubusercontent.com/46192475/59274938-9c144f00-8c5b-11e9-961e-c33854b6e50a.gif"> 


<br />

---

<br />


### 배포
- [공식 문서](https://voila.readthedocs.io/en/stable/deploy.html)를 보면 [mybinder.org](mybinder.org), Heroku, Google App Engine 등에서 배포하는 방법이 간단히 나와있음


<br />

---

<br />

### Voila Architecture
<img src="https://www.dropbox.com/s/28pggskzariqlb5/Screenshot%202020-01-05%2023.54.31.png?raw=1">

<br />



---

## Reference
- [Voila Github](https://github.com/voila-dashboards/voila)
- [And voila](https://blog.jupyter.org/and-voil%C3%A0-f6a2c08a4a93)
- [Voilà is now a Jupyter subproject](https://blog.jupyter.org/voil%C3%A0-is-now-an-official-jupyter-subproject-87d659583490)
- [voila demo](https://github.com/maartenbreddels/voila-demo)
- [Dashboarding with Jupyter Notebooks, Voila and Widgets](https://www.youtube.com/watch?v=VtchVpoSdoQ)
- [Turn any Notebook into a Deployable Dashboard](https://www.youtube.com/watch?v=L91rd1D6XTA) : 이건 Panel 이야기긴한데, 비교할만한 대상인듯
