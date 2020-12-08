---
layout: post
title:  "Julia Language 프로그래밍 언어 기본 문법 정리"
subtitle: "Julia Language 프로그래밍 언어 기본 문법 정리"
categories: development
tags: julia
comments: true
---
	 
- 프로그래밍 언어인 Julia Language 기본 문법에 대한 글입니다


---


## Julia

<img src="https://www.dropbox.com/s/jz1efghzgj0u39l/Screenshot%202019-12-27%2017.58.28.png?raw=1" height="100" weight="300"> 

- Julia는 MIT에서 개발한 언어로 2012년에 처음 개발함
- Two langauge problem
	- Performance(fortan, c, asm) vs Productivity(python, ruby, matlab) vs Generality
	- 이런 문제를 해결하고자 Julia를 만듬
- looks like python, feels like lisp, runs like C 
	- Julia는 C만큼 빠름 
		- <img src="https://www.dropbox.com/s/kmybg4myv1ffds2/Screenshot%202019-12-26%2023.05.34.png?raw=1">
	- Julia는 R, Matlab, Python 같이 high level로 수치 계산하기 좋고, 일반 범용 목적의 프로그래밍도 지원함
	- 메타프로그래밍을 할 수 있음(macros, multiple dispatch) 
- 수치 해석(Numerical Analysis), 최적화(Optimization) 문제 등 고성능이 필요한 수학적 계산 처리를 주로 품
- index 숫자가 1부터 시작함
- Python에서 Julia 실행 가능 : [pyjulia](https://github.com/JuliaPy/pyjulia) 사용
- Julia에서 Python 실행 가능 : PyCall 사용


### Julia 사용 사례
- Celeste
	- Sloan Digital Sky Survey Data로 178 terabytes
	- 천문학 이미지 분석하기 위해 Julia를 활용함
	- [Parallel Supercomputing for Astronomy](https://juliacomputing.com/case-studies/celeste.html)에 자세한 내용이 나옴
- 사용하는 회사
	- 2018년 기준이고 지금은 더 많을듯
	- <img src="https://www.dropbox.com/s/hdwhw213xd3b483/Screenshot%202019-12-26%2023.27.22.png?raw=1">

<br />

---

<br />


### Julia 설치하기
- Mac OS 기준

	```
	brew cask install julia
	```

- 터미널에서 Julia를 입력한 후 아래와 같은 내용이 뜨면 성공
	- <img src="https://www.dropbox.com/s/e96v3lgc4m8mz3m/Screenshot%202019-12-26%2022.42.37.png?raw=1">

<br />

---

<br />


### Jupyter Notebook에서 Julia 사용하기
- IJulia 사용
- 터미널에서 Julia를 입력해서 Julia prompt로 이동 후 아래 명령어 입력

	```
	ENV["PYTHON"] = ""
	ENV["JUPYTER"] = ""
	```

- IJulia 설치(Julia Prompt에서 실행)

	```
	using Pkg
	Pkg.add("IJulia")
	```

- IJulia 실행(Julia Prompt에서 실행)

	```
	using IJulia
	notebook()
	```

- 이제 익숙한 Jupyter Notebook이 보이고 Julia 커널을 사용할 수 있게됨
	- 최초 실행 이후엔 Jupyter Notebook으로 실행해도 줄리아 커널이 보임
	- <img src="https://www.dropbox.com/s/qcmvn0cu006ukw7/Screenshot%202019-12-26%2022.46.49.png?raw=1" height="200" weight="300">

<br />

---

<br />


### Package 설치
- 패키지 Import는 using을 사용
- Pkg를 사용해 패키지 설치

	```
	usisg Pkg
	Pkg.add("PackageName")
	```	


<br />

---

<br />


## 기본 문법
- 함수의 Document를 보고 싶은 경우
	- 함수 앞에 ?를 붙임
	- ?println
	- <img src="https://www.dropbox.com/s/v14vr3pwljjd1oj/Screenshot%202019-12-26%2023.39.16.png?raw=1">
- 쉘 커맨드를 사용하고 싶은 경우
	- ;를 앞에 붙임
	- ;ls
- print
	- println() 사용
- Assert
	- @assert하고 식을 씀. 파이썬에서 assert a == 1 이런 느낌인데 골뱅이만 추가됨

<br />

---

<br />

### Variable	
- type 확인하기
	- typeof() 사용
- 타입 변환은 파이썬처럼 `float(365)`로 할수도 있고, `parse(Float64, "365")`로 할수도 있음
- 신기한 부분은 이모지에 변수 할당할 수 있음(Super generic)
- <img src="https://www.dropbox.com/s/ho22ch9ptifp264/Screenshot%202019-12-26%2023.41.57.png?raw=1">

<br />

---

<br />

### String
- ""나 """ """를 사용함
	- 후자의 경우 문자열 내에서 따옴표를 사용할 수 있고, 전자는 불가능(Error 발생)
	- ''는 character고 string이 아님

	```
	"Here, we get an "error" because it's ambiguous where this string ends "

	"""Look, Mom, no "errors"!!! """
	
	typeof('a')

	```

- String Interpolation
	- Python의 F string같이 값을 넣고 흘려버리기
	- 달러 기호 `$`를 사용함
	- $() 안에 연산도 할 수 있음

	```
	name = "kyle"
	num_fingers = 10
	num_toes = 10
	
	println("Hello, my name is $name.")
	println("I have $num_fingers fingers and $num_toes toes.")
	println("That is $(num_fingers + num_toes) digits in all!!")
	```

- String Concatenation
	- string끼리 합칠 경우엔 `string()`을 사용

	```
	s3 = "How many cats ";
	s4 = "is too many cats?";
	😺 = 10

	string(s3, s4)
	string("I don't know, but ", 😺, " is too few.")
	```

- String 반복
	- Python에선 "hi"*100처럼 했지만 Julia에선 "hi"^100으로 가능함

<br />

---

<br />

### Data Structures
- Tuple
	- Tuple은 생성하고 바꿀 수 없음
	- `()`로 만듬
	- 순서가 지정된 요소를 묶어 튜플로 만들 수 있음 

		```
		myfavoriteanimals = ("penguins", "cats", "sugargliders")
		
		myfavoriteanimals[1]
		```
	
	- NamedTuples
		- 각각의 요소가 이름이 있는 Tuple
		
		```
		myfavoriteanimals = (bird = "penguins", mammal = "cats", marsupial = "sugargliders")

		myfavoriteanimals[1]
		
		myfavoriteanimals.bird
		```
	
- Dictionaries
	- Python의 dictinary랑 비슷한듯. key-pair 존재
	- `Dict()`으로 만들고, =>를 사용함

		```
		myphonebook = Dict("Jenny" => "867-5309", "Ghostbusters" => "555-2368")
		myphonebook["Jenny"]
		myphonebook["Kramer"] = "555-FILK"
		```
	
	- `pop!`을 쓰면 값이 나옴(원본 dict엔 사라짐) 느낌표가 있다니 신기함

		```
		pop!(myphonebook, "Kramer")
		myphonebook
		```	
	
	- Tuple이나 Array와 다르게 dictionaries는 정렬되지 않아서 index로 접근할 수 없음

- Arrays
	- Array는 mutable하고 dict과 다르게 순서가 있음
	- `[]`로 만듬

		```
		myfriends = ["Ted", "Robyn", "Barney", "Lily", "Marshall"]
		
		mixture = [1, 1, 2, 3, "Ted", "Robyn"]
		```
	
	- `push!`와 `pop!` 사용 가능
	- ND Array도 생성할 수 있음

		```
		numbers = [[1, 2, 3], [4, 5], [6, 7, 8, 9]]
	
		rand(4, 3)
		rand(4, 3, 2)
		```
	
	- 복사는 `copy()`를 사용해 가능 
	- array의 길이는 `length()` 사용
	- array의 요소 합은 `sum()` 사용

<br />

---

<br />

### Loops
- while loops와 for loops이 있음 : 모두 end를 써야함
- while loops
	- 사용 방식
	 
		```
		while *condition*
	   		*loop body*
		end
		```
	
	- 예시 : indent에 둔감함

		```
		n = 0
		while n < 10
	   		n += 1 #  n = n + 1
	    				println(n)
		end
		n
		```
	
	- 세미 콜론(;)을 사용하면 줄바뀜 효과를 얻음
	
		```
		n = 0
		while n < 10; n += 1 
	    				println(n)
		end
		n
		```
	
		```
		myfriends = ["Ted", "Robyn", "Barney", "Lily", "Marshall"]
		
		i = 1
		while i <= length(myfriends)
		    friend = myfriends[i]
		    println("Hi $friend, it's great to see you!")
		    i += 1
		end
		```

- for loops
	- 사용 방식

		```
		for *var* in *loop iterable*
    		*loop body*
		end
		```
		
	- 예시(1:10은 python에서 range(1, 10)과 동일)

		```
		for n in 1:10
		    println(n)
		end
		```
		
		```
		myfriends = ["Ted", "Robyn", "Barney", "Lily", "Marshall"]
		
		for friend in myfriends
		    println("Hi $friend, it's great to see you!")
		end
		```	
	
	- 2중 for문을 더 쉽게 쓸 수 있음
		
		```
		m, n = 5, 5
		A = fill(0, (m, n))
		for i in 1:m
		    for j in 1:n
		        A[i, j] = i + j
		    end
		end
		A
		```

		```
		B = fill(0, (m, n))
		for i in 1:m, j in 1:n
		    B[i, j] = i + j
		end
		B
		```

	- Array comprehension(Python의 list comprehension 느낌) => Julia 스타일

		```
		[ x+y for x in 1:10, y in 2:5]
		
		C = [i + j for i in 1:m, j in 1:n]
		```

<br />

---

<br />
	
### Conditionals
- if문

	```
	if *condition 1*
	    *option 1*
	elseif *condition 2*
	    *option 2*
	else
	    *option 3*
	end
	```

	- fizzbuzz 예시
		- &&는 AND를 뜻함
		- %는 나머지를 뜻함
		
		```
		N=15
		
		if (N % 3 == 0) && (N % 5 == 0) # `&&` means "AND"; % computes the remainder after division
		    println("FizzBuzz")
		elseif N % 3 == 0
		    println("Fizz")
		elseif N % 5 == 0
		    println("Buzz")
		else
		    println(N)
		end
		```

- ternary operators(삼항 연산) : 오 신기함
	- a ? b : c
	- 이건 아래와 동의어임

		```
		if a
			b
		else
			c
		end
		```
	
	- 예시

	```
	x = 10
	y = 5
	
	if x > y
	    x
	else
	    y
	end
	
	(x > y) ? x : y
	```
	
- short-circuit evaluation
	- a && b
	- if a and b가 true일 경우 true를 return함	
		```
		false && (println("hi"); true)
		true && (println("hi"); true)
		```

	- `error()`를 사용하면 Error 발생

		```
		true || println("hi")
		```
		
	- `||` operator는 or을 뜻함
	
		```
		true || println("hi")
		false || println("hi")
		```
	
<br />

---

<br />	
	

### Functions
- 주요 Topic
	- 함수 정의하는 방법
	- Duck-typing 하는 방법 : 타입을 미리 정의하는게 아닌 실행되었을 때 타입 정의. [덕타이핑이란?](https://nesoy.github.io/articles/2018-02/Duck-Typing) 글 참고
	- Mutating vs non-mutating function
	- Some higher order functions
- 함수 정의

	```
	function sayhi(name)
   		println("Hi $name, it's great to see you!")
	end
	
	function f(x)
	    x^2
	end
	```
	
	- 1줄로 할 수도 있음
	
		```
		sayhi2(name) = println("Hi $name, it's great to see you!")
	
		f2(x) = x^2
		```
	
	- 함수 인자 타입 지정
		- `::` 사용하며 타입 선언이 없으면 기본적으로 Any 타입으로 지정됨

		```
		foo(x::Int, y::Int) = println("My inputs x and y are both integer!")
		
		foo(3, 4)
		```
	
	- anonymous(익명) 함수 : = 를 사용해서 -> 결과를 낸다. Tuple도 사용 가능

		```
		sayhi3 = name -> println("Hi $name, it's great to see you!")
	
		f3 = x -> x^2
		
		sayhi4 = (firstname, lastname) -> println("Hi $firstname $lastname, it's greate to see you!")
		```
	
- Duck-typing
	- Julia는 모든 입력이 의미가 있다고 생각함
	- string으로 들어갈 것이라 예상했던 것이 int가 들어가도 작동함

	```
	sayhi(55595472)
	
	A = rand(3, 3)
	f(A)
	f("hi")
	
	# vector 연산은 ^ 연산이 구현되지 않아서 아래 명령어는 오류 발생
	v = rand(3)
	f(v)
	```
	
- Mutating vs non-mutating functions	
	- `!`를 붙이는 차이
	- sort는 원본 데이터는 변하지 않지만, sort!는 변함. 마치 pandas에서 replace=True가 기본적으로 들어간게 ! 붙인거 같은 느낌

	```
	v = [3, 5, 2]
	sort(v)
	v
	
	sort!(v)
	v
	```	
	
- Some higher order functions(고차 함수)
	- map
		- 해당 함수를 전달한 데이터의 모든 요소에 적용함
		- map(f, [1, 2, 3]) => [f(1), f(2), f(3)] 이렇게 됨
		
		```
		map(x -> x^3, [1, 2, 3])
		```
	
	- broadcast
		- map의 일반적인 형태
		
		```
		broadcast(f, [1, 2, 3])
		```
		
		- 함수 뒤에 `.`을 붙일 경우에도 broadcast됨
		
			```
			f.([1, 2, 3])
			```

		- 연산 차이

			```
			A = [i + 3*j for j in 0:2, i in 1:3]
			
			f(A)
			# A^2 = A*A 행렬 곱
			
			f.(A)
			# 각 요소들의 제곱
			```
		
		- 아래 두 코드는 동일한 결과가 나타남

			```
			broadcast(x -> x + 2 * f(x) / x, A)
	
			A .+ 2 .* f.(A) ./ A
			```
			
<br />

---

<br />	
	
### Packages
- [https://pkg.julialang.org/docs/](https://pkg.julialang.org/docs/)에서 사용 가능한 패키지를 확인할 수 있음
- 패키지 설치(Pkg : 패키지 관리자)

	```
	using Pkg
	Pkg.add("Example")
	```

- 패키지 사용

	```
	using "package name"
	```	
	
- using을 하면 그 안에 있는 함수를 사용할 수 있음	
- Palette 사용하는 예시

	```
	Pkg.add("Colors")
	using Colors
	palette = distinguishable_colors(100)
	rand(palette, 3, 3)
	```
	
- <img src="https://www.dropbox.com/s/fszncwj4003c2eq/Screenshot%202019-12-27%2015.02.10.png?raw=1">

<br />

---

<br />

### Plotting
- 그래프를 그리는 방식은 정말 다양함(PyPlot을 부를수도 있고, Plots 사용할수도 있음)
- Plots는 다양한 백엔드를 지원하는데, GR과 UnicodePlots을 사용해볼 예정
	- [Plots Backent](https://docs.juliaplots.org/latest/backends/) 참고
	- gr을 빌드할 때 너무 오래 걸려서 [링크](https://discourse.julialang.org/t/too-much-time-taken-to-build-gr/22798/5)를 찾아봄
	- Using Plots시 Permission Error가 떠서 PyPlot을 사용함
		
	```
	import Pkg; Pkg.add("Plots")
	using Plots
	
	globaltemperatures = [14.4, 14.5, 14.8, 15.2, 15.5, 15.8]
	numpirates = [45000, 20000, 15000, 5000, 400, 17];
	
	gr()
	
	
	plot(numpirates, globaltemperatures, label="line")  
	scatter!(numpirates, globaltemperatures, label="points")
	
	
	xlabel!("Number of Pirates [Approximate]")
	ylabel!("Global Temperature (C)")
	title!("Influence of pirate population on global warming")
	
	xflip!()
	```
	
	```
	Pkg.add("PyPlot")
	using PyPlot
	
	globaltemperatures = [14.4, 14.5, 14.8, 15.2, 15.5, 15.8]
	numpirates = [45000, 20000, 15000, 5000, 400, 17];
	
	plot(numpirates, globaltemperatures, label="line") 
	scatter(numpirates, globaltemperatures, label="points", color="red")
	xlabel("Number of Pirates [Approximate]")
	ylabel("Global Temperature (C)")
	title("Influence of pirate population on global warming")
	```
	
	- <img src="https://www.dropbox.com/s/tlqsuw9omjw2l2j/Screenshot%202019-12-27%2015.32.39.png?raw=1">

<br />

---

<br />	
	
### Multiple Dispatch
- 아마 생소한 개념. [위키피디아](http://en.wikipedia.org/wiki/Multiple_dispatch) 참고
	- 함수의 인자 타입에 따라 다른 함수 호출

	```
	foo(x::String, y::String) = println("My inputs x and y are both strings!")
	
	foo("hello", "hi!")


	foo(3, 4)
	# MethodError: no method matching foo(::Int64, ::Int64)
	
	foo(x::Int, y::Int) = println("My inputs x and y are both integers!")
	foo(3, 4)
	```
	
- `method()`를 사용하면 함수에 대한 내용이 나옴
	- generic function with 2 methods : 2가지 메서드를 가진 함수 객체를 뜻함

	```
	methods(foo)
	
	>>> 2 methods for generic function foo:
	>>> foo(x::Int64, y::Int64) in Main at In[8]:1
	>>> foo(x::String, y::String) in Main at In[5]:1
	
	foo(x::Number, y::Number) = println("My inputs x and y are both numbers!")
	
	foo(x, y) = println("I accept inputs of any type!")

	v = rand(3)
	foo(v, v)
	```	
	
	- method(+)를 입력해서 + 구현을 확인해볼 수 있음

- generic function을 호출할 때 어떤 메소드가 전달되는지 확인하려면 @which 매크로를 사용할 수 있음

	```
	@which foo(3, 4)
	
	>>> foo(x::Int64, y::Int64) in Main at In[8]:1
	
	@which 3.0 + 3.0
	>>> +(x::Float64, y::Float64) in Base at float.jl:395
	```

- 간단한 내용만 본거고, 더 궁금하면 [Github](https://github.com/JuliaComputing/JuliaBoxTutorials/blob/master/introductory-tutorials/intro-to-julia/10.1%20Multiple%20dispatch.ipynb) 참고하면 좋음

<br />

---

<br />

### Dataframe
- 데이터를 조작하기 위해 [Dataframes.jl](https://juliadata.github.io/DataFrames.jl/stable/) 활용
- Pandas와 비슷한 느낌

	```
	import Pkg; Pkg.add("DataFrames")
	using DataFramesusing DataFrames
	
	df = DataFrame(A = 1:4, B = ["M", "F", "F", "M"])
	```

- `df.col` 또는 `df[!, :col]`을 통해 열에 직접 액세스 가능
	- 후자는 열 이름을 변수에 전달할 수 있어 유용함. 열 이름은 Symbol("col")
	- `names(df)`로 이름을 확인할 수 있음
	- df[!, :col]은 복사하지 않고 원본을 컨트롤하고, df[:, :col]은 사본을 얻음

- Dataframe을 만들고 하나씩 붙이는 것도 가능(Column끼리)

	```
	df = DataFrame()
	df.A = 1:8
	df.B = ["M", "F", "F", "M", "F", "M", "M", "F"]
	```

	- <img src="https://www.dropbox.com/s/c9l1ot450aacjqd/Screenshot%202019-12-27%2017.13.22.png?raw=1">

- Row끼리 합치기 : `push!` 사용
	
	```
	df = DataFrame(A = Int[], B = String[])
	push!(df, (1, "M"))
	push!(df, [2, "N"])
	push!(df, Dict(:B => "F", :A => 3))
	```
	
	- <img src="https://www.dropbox.com/s/732p2rg6ndx5vra/Screenshot%202019-12-27%2017.14.50.png?raw=1">


- 저장하기

	```
	using Pkg
	Pkg.add("CSV")
	using CSV

	CSV.write("dataframe.csv", df)
	```

- Filtering : Pandas랑 느낌 비슷한데 점을 추가함(각 요소별 연산) : .>, in. 등

	```
	df = DataFrame(A = 1:2:1000, B = repeat(1:10, inner=50), C = 1:500)
	df[df.A .> 500, :]
	```

- Describe
	- describe(df)로 가능

	```
	df = DataFrame(A = 1:4, B = ["M", "F", "F", "M"])
	describe(df)
	```
	
	- <img src="https://www.dropbox.com/s/fft3ne8kz8aj0d2/Screenshot%202019-12-27%2017.23.04.png?raw=1">
	
- Aggregate : aggregate(df, 연산)
	
	```
	df = DataFrame(A = 1:4, B = 4.0:-1.0:1.0)
	aggregate(df, sum)
	aggregate(df, [sum, prod])
	```	
	
	- <img src="https://www.dropbox.com/s/pgzuekq3v87ji0n/Screenshot%202019-12-27%2017.23.21.png?raw=1">

- 특정 값 변경하기

	```
	df.A[1] = 10
	```	

- CSV Read

	```
	using Pkg
	Pkg.add("CSV")
	using CSV
	
	DataFrame(CSV.File(input))
	```
	
- DelimitedFiles(CSV 읽기)

	```
	using DelimitedFiles
	P,H = readdlm("programminglanguages.csv",header=true)
	```	

- Join
	- join 인자 안에 kind = :inner, :left 등을 사용할 수 있음

	```
	people = DataFrame(ID = [20, 40], Name = ["John Doe", "Jane Doe"])
	jobs = DataFrame(ID = [20, 40], Job = ["Lawyer", "Doctor"])
	join(people, jobs, on = :ID)
	```
	
	- <img src="https://www.dropbox.com/s/w3prfwcsfwx1r6e/Screenshot%202019-12-27%2017.26.30.png?raw=1">

- 그 외에도 Split-apply-combine을 위해 by 등을 사용함

<br />

---

<br />

### 최적화 라이브러리
- [JuliaOpt](http://www.juliaopt.org/) 쪽에 자료 많음
- [JuMP.jl](http://www.juliaopt.org/)
	- CVX는 [Convex.jl](https://github.com/JuliaOpt/Convex.jl)


- JuMP 예시 코드

	```
	using JuMP, GLPK

	# 모델 생성
	m = Model(with_optimizer(GLPK.Optimizer))
	
	# Variable 선언
	@variable(m, 0<= x1 <=10)
	@variable(m, x2 >=0)
	@variable(m, x3 >=0)
	
	# 목적 함수 
	@objective(m, Max, x1 + 2x2 + 5x3)
	
	# 제약 조건 설정
	@constraint(m, constraint1, -x1 +  x2 + 3x3 <= -5)
	@constraint(m, constraint2,  x1 + 3x2 - 7x3 <= 10)
	
	# Optimization Model 출력
	print(m)
	
	# 최적화 문제 풀기
	JuMP.optimize!(m)

	# Optimal Solution Print
	println("Optimal Solutions:")
	println("x1 = ", JuMP.value(x1))
	println("x2 = ", JuMP.value(x2))
	println("x3 = ", JuMP.value(x3))
	
	# Optimal dual variables Print
	println("Dual Variables:")
	println("dual1 = ", JuMP.shadow_price(constraint1))
	println("dual2 = ", JuMP.shadow_price(constraint2))
	```

<br />

---

<br />

### VSCode Extension
- [julia-vscode](https://github.com/julia-vscode/julia-vscode)

<br />

---

<br />

### Reference
- 권창현님의 [Julia Programming for Operation Research](https://www.chkwon.net/julia/)
- [Julia 공식 Document](https://docs.julialang.org/en/v1/)
- [Intro to Julia tutorial (version 1.0)](https://www.youtube.com/watch?v=8h8rQyEpiZA&t=)
	- [Github](https://github.com/madeleineudell/intro-to-julia)
	- 이분의(교수되신듯) [강의](https://github.com/udellgroup/orie7191)
- [Intro to Julia](https://ucidatascienceinitiative.github.io/IntroToJulia/Html/WhyJulia)