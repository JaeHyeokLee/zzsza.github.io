---
layout: post
title:  "Mac, Linux에서 Zip 파일에 password(암호) 설정하는 방법"
subtitle: "Mac, Linux Zip 파일에 password(암호) 설정하는 방"
categories: development
tags: linux
comments: true
---
	 

- Mac OS와 Linux(Ubuntu)에서 Zip 파일에 암호를 설정하는 방법에 대해 작성한 글입니다
	- 별도의 프로그램 설치 없이 터미널에서 진행하는 방법입니다


---


### Mac OS에서 압축시 암호 걸기
- Mac Zip 파일로 압축할 때, 암호 설정하는 방법(Terminal)
- zip 명령어를 날릴 때 `-e` 옵션을 주면 암호를 설정할 수 있음
	- 약자의 의미는 encrypt(암호화)를 뜻함
	- 입력하면 Enter password: 가 나오고 암호를 입력하면 됨(Verify password에서 1번 더 입력)
	

	```
	zip -e 압축_파일.zip 압축에_사용될_파일
	```	


- 특정 확장자로 끝나는 모든 파일을 압축하고 싶은 경우엔 *를 사용하면 됨

	```
	zip -e 압축_파일.zip *.json
	```
	
- 폴더째 압축하고 싶은 경우 `-r` 옵션	을 주면 됨(암호까지 주려면 `-er`)

	```
	zip -er zip_file.zip ./
	```
	
<img src="https://www.dropbox.com/s/xewt1gmz55550y7/Screenshot%202019-05-08%2021.47.50.png?raw=1">


- zip에 대해 궁금하면 zip을 치면 관련 명령어가 나옴

<img src="https://www.dropbox.com/s/2fwegq5f4x0bdxw/Screenshot%202019-05-08%2021.52.56.png?raw=1">

### Mac OS에서 압축 풀기
- unzip 명령어를 통해 압축을 풀 수 있음
	- 암호가 설정되어 있으면 암호를 물어봄 

	```
	unzip zip_file.zip
	```
	
- unzip을 치면 관련 명령어가 나옴

<img src="https://www.dropbox.com/s/sn688ds59n75z3e/Screenshot%202019-05-08%2021.54.35.png?raw=1">	


---


### Linux(Ubuntu)에서 압축시 암호 걸기
- 설치

	```
	apt-get update
	apt-get install zip
	```

- zip 명령어 사용시 `-P` 옵션 사용하면 암호를 설정할 수 있음
	- 그러나 보안에 취약하기 때문에 비추천

	```	
	zip -P 암호 압축_파일.zip 압축에_사용될_파일
	```
	
- Mac과 동일하게 `-e` 옵션을 주면 암호를 설정할 수 있음

	```
	zip -e 압축_파일.zip 압축에_사용될_파일
	```	
	
### Linux(Ubuntu)에서 압축 풀기
- 설치

	```
	apt-get update
	apt-get install unzip
	```	
	
- Mac OS와 동일하게 사용

	```
	unzip 압축_파일.zip	
	```
