---
layout: post
title:  "Hash 함수를 사용한 AB Test Sampling"
subtitle: "Hash 함수를 사용한 AB Test Sampling"
categories: data
tags: analytics
comments: true
---

- Hash 함수를 사용한 AB Test Sampling에 대한 글입니다

---

### AB Test
- AB Test는 통계적 가설 검정의 한 형태로, 단일 변수에 대한 두 가지 버전을 비교하는 방법
	- A와 B는 사용자의 행동에 영향을 미칠 수 있는 하나의 변형을 제외하면 동일
	- Push 메세지를 보낼 때 메세지의 워딩을 다르게 하는 것도 AB Test의 일종으로 볼 수 있고, 웹페이지에서 배너의 위치에 따른 클릭률을 비교하는 것도 AB Test로 볼 수 있음
- 참고 자료
	- 강규영님의 [A/B 테스팅이란](https://boxnwhis.kr/2015/01/29/a_b_testing.html) : AB 테스팅의 단점 언급
		- 1) 테스트를 많이 할 경우 단기적으로 손해 발생 가능
		- 2) AB Test 결과는 시간의 흐름에 따라 바뀔 수 있음
		- 3) AB Test만 해선 Local minima에 머물게 될 위험이 있음 
	- 박장시님의 [A/B 테스트에서 p-value에 휘둘리지 않기](https://boxnwhis.kr/2016/04/15/dont_be_overwhelmed_by_pvalue.html)
	- 위키피디아 [AB 테스트](https://ko.wikipedia.org/wiki/A/B_%ED%85%8C%EC%8A%A4%ED%8A%B8)


<br/>
<br/>

### AB Test Sampling
- 보통 실무에서 AB Test를 할 경우 user_id를 가지고 하곤 함
	- 예 : 1의 자리가 홀수인 그룹 A, 1의 자리가 짝수인 그룹 B
		- 단, 그룹을 나누기 전에 사용자들의 분포를 확인해야 함
- 이런 방법은 실험이 많지 않을 경우엔 유용할 수 있지만, 다른 실험을 또 설계할 경우에 이슈가 발생
	- 예 : 그럼 이번엔 10의 자리가 홀수인 그룹을 A, 10의 자리가 짝수인 그룹을 B!
- 이렇게 계속 만들다 보면 어느덧 user_id의 자리수를 모두 채우고, 점점 조건이 애매할 수 있음
- **그래서 생각한 방법이 Hash 함수를 사용해 user_id를 숫자로 만들고, 해당 숫자를 어떤 기준점 기준으로 A와 B로 나누는 방법**

<br/>
<br/>

### Hash 함수
- 해시 함수는 특정 길이의 데이터를 고정된 길이의 데이터로 매핑하는 함수
- 암호학적 해시 함수와 비암호학적 해시함수로 구분   
	- 암호학적 해시 함수 : MD5, SHA계열
	- 비암호학적 해시 함수 : CRC32
- Salt
	- 솔트는 단방향 해시 함수에서 추가하는 임의의 문자열로, 원본 메세지(여기선 user_id) 뒤에 문자 열을 추가함
	- 실험의 관리성을 위해 실험의 이름을 솔트로 설정
- 이런 함수를 사용할 때, 백엔드쪽과 분석쪽에서 같은 결과가 나는지 꼭 확인해야 함
	- 백엔드에서 user_id -> hash -> A/B에 따라 다른 API
	- 데이터가 쌓인 곳에서 user_id -> hash -> A/B로 파악
- Hash 함수를 사용하면 균일하게 나눠질 것이라고 생각

<br/>
<br/>

### 코드
- 파이썬에서 user_id를 Hash하는 코드
	- sha1을 사용
- Python
	
	```
	def hash_user_id(user_id, salt, ratio_threshold):
		"""
		user_id(int) : user_id
		salt(str) : salt 문자열
		ratio_threshold(float) : 기준 ratio. 예 : 0.5
		"""
		id_and_salt = str(user_id) + salt
		
		h = hashlib.sha1()
		h.update(id_and_salt.encode('utf-8'))
		
		res_hash = h.hexdigest()
		total = pow(16, len(res_hash))
		hash_to_int = int(res_hash, 16)
		ratio = hash_to_int / total

		if ratio >= ratio_threshold:
			return 'A'
		else:
			return 'B'
	```

- SQL에서 필요할 경우, 대부분 Hash 함수를 지원하기 때문에 쉽게 user_id를 Hash할 수 있음(없다면 UDF 만들어서 사용)

<br/>
<br/>

### Salt 설정
- AB Test마다 일정한 규칙 안에서 salt를 설정
	- 예: 날짜-이름의 규칙을 가진다고 하면 20190223-test 이런 형식 
- 그리고 특정 문서에 꾸준히 기록!


### Reference
- 강규영님의 [해시 기반 샘플링](https://boxnwhis.kr/2014/12/13/Hash-based_sampling.html)
- [Deterministic A/B tests via the hashing trick](https://medium.com/simpl-under-the-hood/deterministic-a-b-tests-via-the-hashing-trick-d1ea49483202)
- 김재석님의 [A/B Testing에 대한 기초적인 정보들](https://spoqa.github.io/2012/05/15/ab-testing-basic.html)
- 버즈빌의 [Sampling부터 Interpretation까지](https://www.buzzvil.com/ko/2018/06/14/tech-industry-a-b-testing-sampling%EB%B6%80%ED%84%B0-interpretation%EA%B9%8C%EC%A7%80/)
