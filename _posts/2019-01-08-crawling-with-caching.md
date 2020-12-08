---
layout: post
title:  "디스크 캐싱과 레디스를 활용한 크롤링"
subtitle:   "디스크 캐싱과 레디스를 활용한 크롤링"
categories: development
tags: python
comments: true
---

- 캐싱을 사용해 크롤링하는 방법에 대해 작성한 글입니다
	- 디스크 캐싱부터 레디스까지 다룹니다 
- 관련 코드는 [Github](https://github.com/zzsza/TIL/tree/master/python/crawling)에 있습니다

---

### 캐싱을 사용해야 하는 경우
- 캐싱 오프라인 상태(데이터 분석 또는 개발 목적)에서 웹 페이지에 접근할 수 있도록 하기 때문에 사용자에게 도움이 됨
	- 가장 최신 정보나 현재 정보를 얻는 것이 최우선이면 캐싱이 적합하지 않을 수 있음
	- 대규모 크롤링이나 반복적인 크롤링을 계획하지 않으면 매번 페이지를 스크래핑하고 싶음
- 캐싱을 구현하기 전에 얼마나 자주 페이지를 스크래핑할지, 얼마나 자주 새로운 페이지를 스크래핑할지, 얼마나 자주 캐싱을 삭제해야 할지 등에 대해 생각해야 함


### 링크 크롤러에 캐싱 기능 추가하기
- URL을 다운로드하기 전에 캐싱을 확인
	- download 함수 안, 다운로드할 경우 쓰로틀링(Throtting)을 조절
	- 매번 다운로드할 때 매개 변수를 전달할 필요가 없도록 클래스로 구현
- 설명
	- `__call__` 메소드
		- 이 클래스의 객체가 함수처럼 호출되면 실행되는 함수 
		- 다운로드 전에 캐싱을 확인(캐싱은 딕셔너리)

```
# throttle.py
from urllib.parse import urlparse
import time


class Throttle:
    """ Add a delay between downloads to the same domain
    """
    def __init__(self, delay):
        # amount of delay between downloads for each domain
        self.delay = delay
        # timestamp of when a domain was last accessed
        self.domains = {}

    def wait(self, url):
        domain = urlparse(url).netloc
        last_accessed = self.domains.get(domain)

        if self.delay > 0 and last_accessed is not None:
            sleep_secs = self.delay - (time.time() - last_accessed)
            if sleep_secs > 0:
                # domain has been accessed recently
                # so need to sleep
                time.sleep(sleep_secs)
        # update the last accessed time
        self.domains[domain] = time.time()
``` 

```
from random import choice
import requests

from throttle import Throttle


class Downloader:
    def __init__(self, delay=5, user_agent='wswp', proxies=None, cache={}, timeout=60):
        self.throttle = Throttle(delay)
        self.user_agent = user_agent
        self.proxies = proxies
        self.cache = cache
        self.num_retries = None
        self.timeout = timeout

    def __call__(self, url, num_retries=2):
        self.num_retries = num_retries
        try:
            result = self.cache[url]
            print('Loaded from cache:', url)
        except KeyError:
            result = None
        if result and self.num_retries and 500 <= result['code'] < 600:
            # 캐싱 결과가 서버 에러면 무시하고 다운로드를 재시도            
            result = None
        if result is None:
		     # 캐싱에서 결과를 로드하지 않아 다운로드
            self.throttle.wait(url)
            proxies = choice(self.proxies) if self.proxies else None
            headers = {'User-Agent': self.user_agent}
            result = self.download(url, headers, proxies)
            self.cache[url] = result
        return result['html']

    def download(self, url, headers, proxies):
        print('Downloading:', url)
        try:
            resp = requests.get(url, headers=headers, proxies=proxies,
                                timeout=self.timeout)
            html = resp.text
            if resp.status_code >= 400:
                print('Download error:', resp.text)
                html = None
                if self.num_retries and 500 <= resp.status_code < 600:
                    # recursively retry 5xx HTTP errors
                    self.num_retries -= 1
                    return self.download(url, headers, proxies)
        except requests.exceptions.RequestException as e:
            print('Download error:', e)
            return {'html': None, 'code': 500}
        return {'html': html, 'code': resp.status_code}
``` 

### 디스크 캐싱
- `urlsplit` 함수를 사용해 URL 파싱
	
	```
	from urllib.parse import urlsplit
	component = urlsplit("https://www.google.com/webhp")
	print(component)
	>>> SplitResult(scheme='https', netloc='www.google.com', path='/webhp', query='', fragment='')
	print(component.path)
	>>> /webhp
	``` 
- filename 지정
	
	```
	if not path.enswith('/'):
		path += '/index.html'
	elif path.enswith('/'):
		path += 'index.html'
	filename = component.netloc + path + components.query
	```	
	
- `__getitem__` 메소드
	- item을 get
- `__setitem__` 메소드
	- item을 set
	
### 캐싱 테스트하기
- diskcache.py, advanced_linke_cralwer

	```
	from diskcache import DiskCache
	from advanced_link_crawler import link_crawler
	
	link_crawler('http://example.webscraping.com/places/default', '.*/(index|view)/.*', cache=DiskCache())
	```	
	
- 첫 실행시 2분정도 소요되지만 다시 실행하면 1초만에 종료! (캐싱!)
- 디스크 공간 절약하기 위해 압축(zlib 사용)


### 오래된 캐싱 데이터 만료하기
- 디스크 캐시의 현재 버전은 키 값을 디스크에 저장한 후 나중에 이 키를 요청할 때마다 다 값을 리턴
- 웹 페이지 컨텐츠가 변경되면 캐싱 데이터가 만료돼 적합하지 않을 수 있음
	- `__set__` 메소드는 만료 타임 스탬프를 키로 result 딕셔너리에 저장하고 `__get__` 메소드는 현재 UTC 시간을 만료 시간과 비교

	
### DiskCache의 단점
- URL에서 지원되지 않는 문자를 사용할 경우 다른 URL이 동일한 파일 이름으로 매핑됨
- 서로 다른 255자가 넘는 긴 URL의 단축 버전은 동일한 파일 이름으로 매핑
	- URL 길이 제한을 피하기 위해 URL의 해시를 얻은 후 해당 해시를 파일 이름으로 사용
	- 파일시스템이 가지는 문제, FAT32 파일시스템이면 디렉터리당 허용되는 최대 파일 수는 65,535개, 이럴 경우 디렉터리를 분할

## 레디스를 사용한 캐싱
### key-value 저장소 캐싱
- 크롤링할 때 대량의 데이터를 캐싱해야 할 경우 key-value store를 사용
- 파이썬 딕셔너리와 매우 흡사
	- 저장소의 각 엘리먼트에는 key, value가 있음
- 레디스 : REmote DIctionary Server의 약자
- 트위터에서 레디스를 대용량 스토리지로 사용
- 크롤링하고 추가 정보나 검색이 필요하면 ElasticSearch 또는 MongoDB 사용하는 것을 추천


### 레디스 설치
```
# In mac
brew install redis
# python library
pip3 install redis
```

- 레디스 서버 실행
	
	```
	redis-server
	```	

- 레디스 사용하기
	- 레디스 3.0부터 dict 타입을 바로 넣을 수 없고, byte나 string으로 변경해야 들어감 
	
	```
	import redis
	import json
	
	r = redis.StrictRedis(host='localhost', port=6379, db=0)
	r.set('test', 'answer')
	>>> b'True
	r.get('test')
	>>> b'answer'
	url = 'http://example.webscraping.com/places/default/view/United-Kingdom-239'
	html = '...'
	
	results = {'html': html, 'code': 200}
	results = json.dumps(results).encode('utf-8')
	r.set(url, results)
	r.get(url)
	```	
	
- 컨텐츠 업데이트하기

	```
	r.set(url, b'{"html": "new html", "code": 200}')
	r.get(url)
	```
	
- 컨텐츠 살펴보고 삭제
	
	```
	r.keys()
	r.delete('test')
	r.keys()
	```
	
### 레디스 캐싱 구현
- `__getitem__`
	
	```
    def __getitem__(self, url):
        record = self.client.get(url)
        if record:
            if self.compress:
                record = zlib.decompress(record)
            return json.loads(record.decode(self.encoding))
        else:
            raise KeyError(url + ' does not exist')	
	```
	
- `__setitem__`

	```
    def __setitem__(self, url, result):
        data = bytes(json.dumps(result), self.encoding)
        if self.compress:
            data = zlib.compress(data)
        self.client.setex(url, self.expires, data)
    ```	
		
- `setex` 메소드
	- datetime.timedelta 또는 초 값을 받음
	- 지정된 시간 내 레코드를 자동으로 삭제할 수 있는 메소드
- 캐싱 테스트
	
	```
	from rediscache import RedisCache
	from advanced_link_crawler import link_crawler
	
	link_crawler('http://example.webscraping.com/places/default', '.*/(index|view)/.*', cache= RedisCache())
	```		

- requests-cache 탐색
	- 경우에 따라 내부적으로 requests를 사용하는 라이브러리를 캐싱하거나 캐싱 클래스를 직접 처리하고 싶지 않을 경우 사용
	- requests를 통해 URL에 접근하는 모든 get 요청은 먼저 캐싱 확인하고 캐싱이 없는 경우에만 페이지 요청
	- 설치 
		
		```
		pip3 install requests-cache
		```
	- 캐싱 여부 확인 및 만료 설정
	
		```
		import requests_cache
		import requests
		from datetime import timedelta
		requests_cache.install_cache(backend='redis')
		requests_cache.clear()
		
		url = 'http://example.webscraping.com/places/default/view/239'
		resp = requests.get(url)
		resp.from_cache
		>>> False
		resp = requests.get(url)
		resp.from_cache
		>>> True
		# 만료 설정
		requests_cache.install_cache(backend='redis', expire_after=timedelta(days=30))
		```
	
- 레디스는 메세지를 주고받는 PubSub도 지원하니 참고하면 좋을듯 :)


	 
