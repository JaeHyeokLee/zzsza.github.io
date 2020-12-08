---
layout: post
title:  "Uber Kepler.gl : 지리 데이터 시각화 도구"
subtitle: "Uber kepler.gl : 지리 데이터 시각화 도구"
categories: data
tags: mobility
comments: true
---

- Uber의 데이터 시각화 도구인 Kepler.gl에 대한 글입니다

---

### Kepler.gl
- 우버에서는 쏟아지는 GPS 데이터를 분석하기 위해 데이터 시각화팀을 만듬
- 이 시각화 팀은 [vis.gl](vis.gl)라는 홈페이지를 운영하고 있는데, 다양한 프레임워크를 제공하고 있음
	- Kepler.gl, [Movement](https://movement.uber.com/), [AVS](http://avs.auto/) 등
- csv, json, geojson 데이터 포맷을 사용할 수 있고, GPU 지원 덕분에 대용량 데이터도 쉽게 렌더링됨
	- 단, 대용량(250MB 이상) 데이터를 업로드하려면 사파리를 사용하는 것을 권장하고 있음 

### 기능
- 지원하는 기능은 Arc, Line, Hexagon, Point, Heatmap, GeoJSON, Buildings 등이 있음
- Arc
	- <img src="https://www.dropbox.com/s/36gak1ise6tmxxw/Screenshot%202019-03-31%2014.20.04.png?raw=1" width="400" height="350">
- Line
	- <img src="https://www.dropbox.com/s/0rkymltbu4h9cxx/Screenshot%202019-03-31%2014.20.21.png?raw=1" width="400" height="350">
- Hexagon
	- <img src="https://www.dropbox.com/s/bcs55gpd5e71lok/Screenshot%202019-03-31%2014.21.33.png?raw=1" width="400" height="350">
- Point
	- <img src="https://www.dropbox.com/s/0cdqwtr565hw6re/Screenshot%202019-03-31%2014.21.48.png?raw=1" width="400" height="350">
- Heatmap
	- <img src="https://www.dropbox.com/s/303atrade22s7nu/Screenshot%202019-03-31%2014.22.03.png?raw=1" width="400" height="350">
- GeoJSON
	- <img src="https://www.dropbox.com/s/3t2902bx7yc209l/Screenshot%202019-03-31%2014.22.17.png?raw=1" width="400" height="300"> 
- Buildings
	- <img src="https://www.dropbox.com/s/dzw21i1l72729cz/Screenshot%202019-03-31%2014.22.32.png?raw=1" width="400" height="350">

---

### 사용 방법
- 홈페이지 메인에서 GET STARTED 클릭
	- 혹은 URL : [https://kepler.gl/demo](https://kepler.gl/demo)로 접속

#### 데이터 추가
<img src="https://www.dropbox.com/s/ns282b255ylf972/Screenshot%202019-03-31%2015.42.12.png?raw=1">

- 데이터를 추가하는 부분
- 만약 자신의 데이터가 있다면, (적절히 가공한 후) 업로드하면 됨
- 데이터가 없다면 우선 Sample Data를 클릭

<img src="https://www.dropbox.com/s/r1ffr880r122pew/Screenshot%202019-03-31%2015.43.19.png?raw=1" width="400" height="500">

- 택시 데이터가 마음에 들기 때문에, NYC taxi trips 클릭

#### 기본 UI
<img src="https://www.dropbox.com/s/9dq5r95qwr9sy8d/Screenshot%202019-03-31%2015.47.57.png?raw=1">

- 총 6가지 Component로 나눠봤는데(=제 마음대로), 하나씩 설명하면
- 1) Layers, Filters, Interactions, Base map 선택 창
	- Layers는 일단 Pass 
	- Filters 
		- <img src="https://www.dropbox.com/s/bfw9ipcvu3lav6y/Screenshot%202019-03-31%2015.56.50.png?raw=1" width="400">
		- 여기서 Filter를 설정할 수 있음
		- 시간 관련 Filter를 걸면 4)가 생김
		- int나 float 컬럼을 선택하면 값을 조절할 수 있는 bar가 생김
		- string 컬럼은 Search box가 생김
	- Interactions
		- <img src="https://www.dropbox.com/s/p1mxwdvct21pz8l/Screenshot%202019-03-31%2016.01.51.png?raw=1" width="400">
		- Tooltip은 마우스온할 때 어떤 값들이 추가적으로 나오는지 설정하는 부분으로 쉽게 추가하거나 뺄 수 있음
		- Brush는 커서로 영역을 강조 표시 할 수 있음. 브러쉬가 켜지면 모든 레이어가 어두워짐
			- 마우스로 커서를 올려 놓은 부분만 밝아지고 특히 아크 레이어와 잘 작동
		- Tooltip or Brush 중 하나만 선택 가능
	- Base map
		- <img src="https://www.dropbox.com/s/dozvzg3jz60l9ht/Screenshot%202019-03-31%2016.06.00.png?raw=1" width="400">
		- Map Style을 선택할 수 있고, May Layers도 설정 가능
		- Label, Road, Building, Water 등을 ON/OFF 할 수 있고 레이어 순서를 결정할 수 있음
		- Map Style을 커스텀하고 싶으면 mapbox에서 자신만의 map style을 publish한 후, access token을 입력하면 사용 가능
- 2) Layers
	- <img src="https://www.dropbox.com/s/few0q422z4wcrck/Screenshot%202019-03-31%2016.08.36.png?raw=1" width="400">
	- 이 부분엔 데이터의 타입(Point, Arc, Line, Grid, Cluster, Icon, Heatmap, H3, Polygon 등등)을 설정 가능
	- 설정값에 따라 옵션이 세부적으로 조절 가능
	- 레이어를 ON/OFF 가능하고 Label이 없는 경우엔 Point로 표현 가능하고, Label도 붙일 수 있음
		- <img src="https://www.dropbox.com/s/ucuzk0ga5000kva/Screenshot%202019-03-31%2016.11.21.png?raw=1">
	- 이 부분은 백번 보는 것보다 직접 실행하는 것이 좋음   
- 3) Layer Blending
	- 레이어를 어떻게 섞을지, additive, normal, subtractive가 존재
- 4) Time Playback
	- <img src="https://www.dropbox.com/s/rf53v4jom97nof4/Screenshot%202019-03-31%2016.13.49.png?raw=1">
	- 시간 Filter가 걸릴 경우 활성화
	- Bound를 조절한 후, 재생 버튼을 누르면 시간순으로 데이터를 볼 수 있음
	- 또한 재생 속도도 조절 가능
	- <img src="https://www.dropbox.com/s/9di8t3vjxoa3i7e/Screenshot%202019-03-31%2016.20.49.gif?raw=1">  
- 5) Map View mode
	- dual map view, 3D Map, show legend 가능
	- Dual Map view를 한 후, visible layers를 다르게 설정할 수 있음
	- <img src="https://www.dropbox.com/s/m4oguf9kap8n8wn/Screenshot%202019-04-22%2020.42.59.png?raw=1">
- 6) Share
	- Share 옵션으로 Export Image, Export Data, Export Config, Export Map, Share Public URL(Dropbox)를 사용할 수 있음
	- config를 export해서 재사용 가능

### 2019 로드맵
- 기능에 대한 로드맵은 아래 Github 참고
	- [Kepler.gl 2019 Roadmap](https://github.com/uber/kepler.gl/wiki/Kepler.gl-2019-Roadmap)
	
### FAQ
- Video File로 export할 수 있는지?
	- kepler에선 불가능, Quicktime Player나 Giffy 등을 사용하면 됨 	
	- 참고 링크 : [Giphy Capture](https://it-talktalk.tistory.com/164)
- 지도에 추가할 수 있는 데이터 세트의 수에 제한이 있는지?
	- 제한은 없지만 많을수록 성능이 저하될 가능성이 큼
	- 레이어도 동일
- 최대 파일 업로드 크기는 얼마인지?
	- 250mb 이하 파일을 허용함. 사파리에선 더 큰 파일을 로드할 수는 있지만 성능은 제한됨 
	

### Tutorial
- [Vis.gl 블로그](https://medium.com/vis-gl)에 글이 많음
- [Animating 40 years of California Earthquakes](https://medium.com/vis-gl/animating-40-years-of-california-earthquakes-e4ffcdd4a289) 
- [Mapping the Parisian trees](https://medium.com/vis-gl/mapping-the-parisian-trees-6dc30f6aabc7)
- [Visualizing Unemployment for U.S. Counties with kepler.gl](https://medium.com/vis-gl/visualizing-u-s-county-unemployment-with-kepler-gl-c5f2ed31c71)
- [Using Kepler.gl and Movement to Visualize Traffic Effects of a Rainstorm](https://medium.com/vis-gl/movement-in-kepler-d00e843f464d)


---

### 자체 서버에 빌드하기
- 별도로 빌드해서 사용해야 하는 분들을 위해 작성한 부분
- Git, Node.js, Yarn이 설치되어 있어야 함
- MapboxAccessToken 있어야 함
	 - [홈페이지](https://account.mapbox.com/) 가입 후 발급
	 - 역시 이런 것은 유료.. 월 499달러라..


```
git clone https://github.com/uber/kepler.gl.git
cd kepler.gl
yarn --ignore-engines
export MapboxAccessToken=<insert_your_token>
npm start
```

- 완료된 후, `localhost:8080`에 가면 아래 같은 화면이 뿅!

<img src="https://www.dropbox.com/s/4i3uv5r19avptal/Screenshot%202019-03-31%2016.40.48.png?raw=1">



### Reference
- [kepler.gl Github](https://github.com/uber/kepler.gl)
- [공식 홈페이지](https://kepler.gl/)
- [Kepler User Guide](https://github.com/uber/kepler.gl/tree/master/docs/user-guides)
- [Developing Kepler.gl](https://github.com/uber/kepler.gl/blob/master/contributing/DEVELOPERS.md)
- [Vis.gl Medium](https://medium.com/vis-gl)
