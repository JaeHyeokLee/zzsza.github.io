---
layout: post
title:  "크롬 개발자 도구 Console에서 Google Play Review 크롤링하기"
subtitle: "크롬 개발자 도구 Console에서 Google Play Review 크롤링하기"
categories: development
tags: web 
comments: true
---
	
- Crawling Google Play App Review with Developer Tools
- 개발자 도구를 사용해 Google Play App 리뷰 크롤링하는 과정에 대해 작성한 글입니다(정말 빠르고 쉽게 크롤링하기!)
- 부록으로 Word Cloud 그리는 Colab도 첨부했습니다

---

## Crawling Google Play Review
- 구글플레이 Review 데이터를 크롤링하는 방법은 많음
	- Requests와 BeautifulSoup을 사용하는 방법
	- Selenium을 사용하는 방법
- 이런 방법들은 모두 "파이썬 스크립트"를 작성해, 파이썬을 실행해야 함
	- 스크립트 언어라 간단한 편이지만 그래도 더 간단히 크롤링하는 방법이 있음
- 크롬 개발자 도구를 사용한 구글 플레이 리뷰 데이터 크롤링!
- 이 방법이 적절한 경우 (주관적 생각)
	- 시간이 없을 경우
	- 리뷰 데이터가 생각보다 적은 경우

	
### URL 확인
- [Google Play](https://play.google.com/store)에 접속한 후, 리뷰를 보고 싶은 앱을 검색
	- 요새 미세먼지가 너무 심해서, 많이 다운로드될 것 같은 `미세미세`를 검색

### 리뷰 확인
<img src="https://www.dropbox.com/s/7bdb69ufwwpq2ia/Screenshot%202019-03-11%2023.56.52.png?raw=1">

- 리뷰 모두 보기 클릭
- URL은 [https://play.google.com/store/apps/details?id=cheehoon.ha.particulateforecaster&showAllReviews=true](https://play.google.com/store/apps/details?id=cheehoon.ha.particulateforecaster&showAllReviews=true)

### 스크롤 내리기
- 개발자 도구 열고 Console로 이동
	- Mac OS에서 단축키는 command + option + i 
- 처음엔 리뷰가 모두 보이진 않음
- 리뷰를 계속 내리거나 "더보기" 버튼을 클릭해야 함
- 스페이스를 계속 내리기 위해 아래 코드를 Console에서 실행
	- 코드 실행하지 않고 스페이스 계속 누르고 더보기 클릭하는 수작업도 나름 빠름 

	```
	window.scrollTo(0, document.body.scrollHeight || document.documentElement.scrollHeight);
	``` 	

- 4번 스크롤을 내리면 "더보기" 버튼이 생기는데, 클릭하는 코드

	```
	document.getElementsByClassName("RveJvd snByac")[0].click()
	```


- 만약 리뷰가 너무 길 경우 "전체 리뷰" 버튼이 생기는데, 클릭하는 코드 ([0]에서 숫자값을 조절해야 할 수 있음)

	```
	document.getElementsByClassName("LkLjZd ScJHi OzU4dc  ")[0].click()
	```

### 크롤링
- 어느 정도 리뷰를 끝까지 내렸다고 가정하고 Console에서 아래 코드 실행
	- 참고로 별점이랑 이름, 리뷰 등을 따로 가져올 순 있지만 한방에 HTML로 가져옴 

	```
	var reviews = document.querySelectorAll('div[class="d15Mdf bAhLNe"]')
	
	var data = []
	reviews.forEach(v => data.push({body: v.outerHTML}))
	```

- 이제 콘솔에서 data하고 엔터를 치면 아래와 같이 나옴

	```
	(840) [{…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, …]
	```

	- 현재 리뷰 840개가 저장되었다는 뜻
- `copy(data)`를 통해 클립보드에 데이터를 저장
- 이제 일단 데이터 크롤링 끝!

### 파일 생성
- 이제 터미널에서 아래 코드 실행

	```
	vi review.json
	``` 
	
- 수정 모드를 위해 i를 한번 입력한 후, command + v
- 리뷰가 많다면 시간이 꽤 소요될 수 있음. 커피 마시고 오는거 추천
- 다 되었으면 ESC + :wq 로 저장하고 나오기	


### 파이썬으로 데이터 전처리
- 이미 크롤링은 끝났지만, 전처리를 쉽게 하고싶은 분들을 위한 부분
- 아래 코드로 데이터 전처리

	```
	import pandas as pd
	df = pd.read_json("review.json")
	
	p = re.compile(r'\d+')
	def parser(body):
	    bs = BeautifulSoup(body, 'html.parser')
	    user_name = bs.find('span', class_='X43Kjb').text
	    date = bs.find('span', class_='p2TkOb').text
	    rating = bs.find('div', {'role':'img'})['aria-label']
	    rating = p.findall(rating)[-1]
	    review_text = bs.find('span', {'jsname':'bN97Pc'}).text
	    return user_name, date, rating, review_text
	    
	df['user_name'], df['date'], df['rating'], df['review_text'] = zip(*df['body'].map(parser))
	del df["body"]
	```

- 이제 user_name, date, rating, review\_text를 가지는 데이터프레임이 생성됨
- 아래 코드로 date 처리

	```
	df['date'] = pd.to_datetime(df['date'], format='%Y년 %m월 %d일')
	```
	
- rating 개수 그래프 그리기

	```
	import seaborn as sns 
	sns.factorplot('rating',kind='count',data=df)
	```	
	
### Word Cloud 그리기
- 부록으로 Word Cloud 그린 [Colab](https://colab.research.google.com/drive/1Lr-tdDJFz_Aln-SXcoy4fEn-c8ofR7L4) 링크 공유 :)
	- 전체 코드가 작성되어 있음 
- 5점 평점의 Word Cloud
	- <img src="https://www.dropbox.com/s/dsdt45dtz8uqdvo/Screenshot%202019-03-12%2001.11.19.png?raw=1">
- 2점 이하 평점의 Word Cloud
	- <img src="https://www.dropbox.com/s/24s12qytd4p59m6/Screenshot%202019-03-12%2001.11.47.png?raw=1">
	

### 마무리
- 약간 생소하지만 간단한 개발자 도구를 통해 데이터를 수집해봤습니다!
- 도구는 선택의 문제이니 모두 상황에 맞는 적절한 도구를 찾으면 좋겠습니다!
	- 개발자 도구로 할 수 있는 일이 더 궁금하신 분들은 [링크](https://flaviocopes.com/chrome-devtools-tips/)를 참고해주세요 :)  	
