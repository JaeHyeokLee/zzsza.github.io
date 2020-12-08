---
layout: post
title:  "Flask에 인증(Auth) 붙이는 방법"
subtitle: "Flask에 인증(Auth) 붙이는 방법"
categories: development
tags: web python
comments: true
---
	 
- Flask에 인증을 붙이는 방법(Flask with auth)를 작성한 글입니다
- 특히 사용자의 비밀번호 암호화에 대해 자세히 알아볼 예정
	
---

## 인증
- 많은 API에서 인증(Authentication)은 공통적으로 구현되는 엔드포인트들 중 하나
	- Private한 API나 Public한 API도 기본적 인증을 요구함
	- Private API
		- 사용할 수 있는 사용자나 클라이언트를 제한
	- Public API
		- 사용자나 클라이언트를 제한하진 않지만 사용 횟수 제한, 남용 방지, 사용자 통계 등의 이유로 인증을 대부분 필요로 함
- 인증은 사용자의 신원을 확인하는 절차로 일반적으로 웹사이트에서 사용자가 로그인해 아이디와 비번을 확인하는 절차를 이야기함
	- 즉, 로그인 기능을 구현하는 것이 인증 엔드포인트임

### 로그인 기능 구현 절차
- 사용자 가입 절차를 진행해 사용자의 ID, 비밀번호를 생성해야 함
- 가입한 사용자의 ID, 비밀번호를 데이터베이스에 저장하고 사용자의 비밀번호는 암호화해서 저장
- 사용자가 로그인할 때 본인의 ID, 비밀번호를 입력
- 사용자가 입력한 비밀번호를 암화한 후, 암호화되어서 DB에 저장된 비밀번호와 비교
- 비밀번호가 일치하면 로그인 성공
- 로그인에 성공하면 API 서버는 access token을 프론트엔드 혹은 클라이언트에 전송
- 프론트엔드 서버는 로그인 성공 후 해당 사용자의 access token을 첨부해 request를 서버에 전송해 매번 로그인하지 않아도 되도록 함

### 사용자 비밀번호 암호화	
- 사용자의 비밀번호를 암호화해서 저장해야 하는 이유
	- 1) 외부 해킹 공격에 의해 데이터베이스가 노출되었을 경우를 대비
	- 2) 내부 인력에 의해 데이터베이스가 노출되었을 경우에 대비
- 사용자의 비밀번호를 암화할 때는 단방향 해시 함수(one way hash function)가 일반적으로 쓰임
	- 단방향 해시 함수는 복호화를 할 수 없는 알고리즘
- 단방향 해시 함수 구현

	```
	import hashlib
	m = hashlib.sha256()
	m.update(b"test password")
	m.hexdigest()
	``` 
	
### bcrypt 암호 알고리즘
- 단방향 해시 암호 알고리즘도 충분히 해킹할 수 있음
	- 대표적으로 rainbow attack
	- 미리 해시 값들을 계산해놓은 테이블을 생성하고 해시 함수값을 역추적해서 본래 값을 찾음
- 이런 해시 함수의 취약점을 보완하기 위해 2가지 방법을 사용
	- 1) salting
		- 음식에 간을 맞추기 위해 소금을 더하듯 비밀번호에 추가적으로 랜덤 데이터를 더해 해시 값을 계산
		- 기존 비밀번호 + 특정 스트링을 붙이고 해싱 진행 
	- 2) key stretching	
		- 단방향 해시 값을 계산하고 또 해시하고, 여러번 반복하는 방법
		- 해시 알고리즘의 실행 속도가 너무 빠르기 때문에 이런 방식을 사용
- 2가지 방법을 구현한 해시 함수 중 널리 사용되는 것이 bcrypt
- 설치

	```
	pip3 install bcrypt
	``` 
	
- 암호화

	```
	import bcrypt
	bcrypt.hashpw(b"password", bcrypt.gensalt())
	bcrypt.hashpw(b"password", bcrypt.gensalt()).hex()
	```	

### Access Token
- HTTP는 stateless라 이전에 어떤 HTTP 통신들이 실행됬는지 알지 못함
	- 이전에 인증되었는지 알지 못하게 됨
- 로그인 정보를 HTTP 요청에 첨부해서 보내야 API 서버에서 로그인된 상태를 처리
	- 이런 로그인 정보를 담고있는 것이 access token
- access token을 생성하는 방법
	- 대표적으로 쓰이는 기술이 JWT(JSON Web Token)
	- JSON 데이터를 token으로 변환하는 방식
	- 유저가 로그인 요청하면 API 서버가 인증 확인 후, access token을 떨굼
	- 그 토큰을 쿠키 등에 저장했다가 요청할 때 사용
- 가짜 JWT를 전송할 경우 백엔드 API에서 자신이 생성한 JWT인지 아닌지 확인함


### JWT 구조
```
xxxxx.yyyyy.zzzzz
``` 

- header : x
	- 토큰 타입과 사용되는 해시 알고리즘을 지정

	```
	{
	 "alg": "HS256",
	 "typ": "JWT"
	}
	```
	 
- payload : y
	- JWT를 통해 실제로 서버 간에 전송하고자 하는 데이터
	- HTTP 메세제의 body와 비슷 
- signature : z
	- JWT가 원본 그대로라는 것을 확인할 떄 사용되는 부분
	- Base64URL 코드화된 header, payload, JWT Secret을 헤더에 지정된 암호 알고리즘으로 암호화해 전송
	- 프론트엔드가 JWT를 백엔드 API로 전송하면 서버에서 JWT signature 부분을 복호화해 서버에서 생성했는지 확인
	- 누구나 원본 데이터를 볼 수 있는 부분(Base64URL 코드화)이 라 민감한 데이터는 저장하지 않도록 해야 함

### PyJWT
- 파이썬에서 JWT를 구현할 떄 사용할 수 있는 라이브러리
- 설치

	```
	pip3 install PyJWT
	```
	
- 사용법

	```
	import jwt
	data_to_encode = {"some": "payload"}
	encryption_secret = "secrete"
	algorithm = "HS256"
	encoded = jwt.encode(data_to_encode, encryption_secre, algorithm=algorithm)
	jwt.decode(encoded, encryption_secret, algorithms=[algorithm])
	``` 
	
### 엔드포인트 구현
- /sign-up 수정 : 암호화해서 비밀번호 저장

	```
    @app.route("/sign-up", methods=["POST"])
    def sign_up():
        new_user = request.json
        new_user['password'] = bcrypt.hashpw(new_user['password'].encode('UTF-8'), bcrypt.gensalt())
        
        new_user_id = app.database.execute(text("""
            INSERT INTO users (
              name,
              email,
              profile,
              hashed_password
            ) VALUES (
              :name,
              :email,
              :profile,
              :password
            )
        """), new_user).lastrowid

        row = app.database.execute(text("""
            SELECT
                id,
                name,
                email,
                profile
            FROM users
            WHERE id = :user_id
        """), {
            "user_id": new_user_id
        }).fetchone()

        create_user = {
            "id": row["id"],
            "name": row["name"],
            "email": row["email"],
            "profile": row["profile"]
        } if row else None

        return jsonify(create_user)
	```

- 인증 엔드포인트 수정
- HTTP Post request에 JSON 데이터로 사용자의 아이디와 비밀번호를 전송받아 데이터베이스에 저장된 사용자의 비밀번호와 동일한지 확인

	```
    @app.route("/login", methods=["POST"])
    def login():
        credential = request.json
        email = credential["email"]
        password = credential["password"]

        row = app.database.execute(text("""
            SELECT
                id,
                hashed_password
            FROM users
            WHERE email = :email
        """), {'email': email}).fetchone()

        if row and bcrypt.checkpw(password.encode("UTF-8"), row["hashed_password"].encode("UTF-8")):
            user_id = row["id"]
            payload = {
                "user_id": user_id,
                "exp": datetime.utcnow() + timedelta(seconds=60 * 60 * 24)
            }

            token = jwt.encode(payload, app.config["JWT_SECRET_KEY"], "HS256")

            return jsonify({
                "access_token" : token.decode("UTF-8")
            })
        else:
            return '', 401
	```
	
### 인증 절차를 다른 엔드포인트에 적용하기
- tweet, follow, unfollow 엔드포인트에 인증을 적용
- 공통적인 기능을 필요로 하는 경우 파이썬의 데코레이터를 사용
- functools의 wraps를 사용해 데코레이터 생성
- 인증 decorator 함수는 전송된 HTTP 요청에서 Authorization 헤더 값을 읽어 JWT access token을 읽고 복호화해서 사용자 아이디를 읽음 => 로그인 여부 결정

- 인증 Decorator 함수

	```
	def login_required(f):
	    @wraps(f)
	    def decorated_function(*args, **kwargs):
	        access_token = request.headers.get("Authorization")
	        if access_token is not None:
	            try:
	                payload = jwt.decode(access_token, current_app.config["JWT_SECRET_KEY"], "HS256")
	            except jwt.InvalidTokenError:
	                payload = None
	
	            if payload is None:
	                return Response(status=401)
	
	            user_id = payload["user_id"]
	            g.user_id = user_id
	            g.user = get_user_info(user_id) if user_id else None
	        else:
	            return Response(status=401)
	
	        return f(*args, **kwargs)
	    return decorated_function
	```	

- 인증 Decorator 적용하기
	- def tweet, def follow, def unfollow 위에 아래처럼 명시하면 됨
	
	```
	@app.route("/tweet", methods=["POST"])
	@login_required
	def tweet():
		~~~~
	``` 

- 확인
	- 이제 먼저 로그인을 해야 함, 로그인하지 않고 아래 request를 날리면 오류 발생

	```
	http -v POST localhost:5000/tweet tweet="Hi!"
	``` 
	
	- 액세스 토큰 생성

	```
	http -v POST localhost:5000/login email=zzsza@naver.com password=1234
	```

	- access_token을 복사한 후 아래와 같이 요청

	```
	http -v POST localhost:5000/tweet tweet="Hi!" "Authorization:eyJ0e~~~~~~~~~"
	```

- 기존엔 tweet할 때 id도 붙여야 했는데, 이젠 인증으로 처리해서 id도 필요 없음. 코드 살짝 수정

	```
    @app.route("/tweet", methods=["POST"])
    @login_required
    def tweet():
        user_tweet = request.json
        user_tweet["id"] = g.user_id # 이 부분
        tweet = user_tweet["tweet"]


        if len(tweet) > 300:
            return "300자를 초과했습니다", 400

        app.database.execute(text("""
            INSERT INTO tweets (
              user_id,
              tweet
            ) VALUES (
              :id,
              :tweet
            )
        """), user_tweet)

        return "", 200
    ```


	
### Reference
- [깔끔한 파이썬 탄탄한 백엔드](http://www.yes24.com/Product/goods/68713424)
