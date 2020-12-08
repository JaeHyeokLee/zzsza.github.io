---
layout: post
title:  "파이썬 중급 - Python 1급 함수 사용법"
subtitle: "파이썬 중급 - Python 1급 함수 사용법"
categories: development
tags: python
comments: true
---

- Python 1급 함수에 대해 작성한 글입니다


---


### 일급 함수
- 일급 함수를 가지고 있으면, 함수형 프로그래밍을 할 수 있음
	- 함수형 프로그래밍은 코드를 간결하게 작성해 개발 시간을 단축하고, 사이드 이펙트를 허용하지 않는 순수 함수(Pure Function)를 지향
	- 쓰레드에서 문제 없이 동작하는 함수
- 파이썬 함수 특징
	- 런타임시 초기화
	- 변수 할당 가능
	- 함수 인수 전달 가능
	- 함수 결과 반환 가능(return)
- 5!

```
def factorial(n):
    """Factorial Functio -> n: int"""
    if n == 1:
        return 1
    return n * factorial(n-1)
    
class A:
    pass
    

print(factorial(5))
print(type(factorial)) # class function
print(type(A)) # class 'type'

print(dir(factorial)) # 함수인데 __repr, __lt__ 등이 있음..! 객체처럼 진행함

print(set(sorted(dir(factorial))) - set(Sorted(dir(A))))
# 함수만 가지고 있는 것들이 나옴. __closure__, __call__
print(factorial.__name__)
print(factorial.__code__) # 파일 위치와 정의한 부분이 보임     
```

- 변수에 할당 가능

```
var_func = factorial

print(var_func) # function factorial 
print(var_func(10)) # 실행이 됨
print(list(map(var_func, range(1, 11))))
```

- 함수 인수 전달 및 함수로 결과 반환 => 고위 함수(Higher-order function)
	- map, filter, reduce
	- 두 코드가 동일한 결과를 나타냄
	
	```
	print(list(map(var_func, filter(lambda x: x % 2, range(1, 6)))))
	print([var_func(i) for i in range(1, 6) if i % 2])
	```

	- reduce

	```
	from functools import reduce
	from operator import add
	
	print(reduce(add, range(1, 11)))
	print(sum(range(1, 11)))
	```

	- 익명함수(Lambda)
		- 가급적 주석 작성하고, 진짜 필요할 때만 사용하기
		- 일반 함수로 리팩토링 권장

	- reduce

	```
	print(reduce(lambda x, t: x + t, range(1, 11)))
	```

- Callable
	- 호출 연산자 => 메소드 형태로 호출이 가능한지 확인

	```
	print(callable(str))
	print(callable(A))
	print(callable(var_func))
	print(callable(factorial))
	print(callable(3.14))
	```	

- Inspect
	- 라이브 객체 검사
	- 메소드 소스를 보고 싶을 경우

	```
	import inspect
	inspect.getsource(add)
	```
	
- Partial
	- 인수 고정 => 콜백 함수 사용
	- 앞에 꺼는 고정되고, 뒤 값만 바뀔 경우 활용 가능

```
from operator import mul
from functools import partial

print(mul(10, 10))

# 인수 고정
# 함수를 인자로 받고, 할당함
five = partial(mul, 5)

six = partial(five, 6)

print(five(10))

print(six()) # 이미 a, b 고정됨

print([five(i) for i in range(1, 11)])
print(list(map(five, range(1, 11))))
```


### 클로저
- 클로저 기초
	- 외부에서 호출된 함수의 변수값, 상태(레퍼런스) 복사 후 저장 => 후에 접근(액세스) 가능
- 파이썬 변수 범위(Scope)
	- 예제 1

	```
	def func_v1(a):
	    print(a)
	    print(b) # Error 발생
	```
	
	- 예제 2
		- 함수 안에 있는 변수는 local 변수, b는 global 변수

	```
	b = 20
	
	def func_v2(a):
	    print(a)
	    print(b) # Error 발생 X
	```
	
	- 예제 3
		- 함수 안에 같은 변수 이름이 있을 땐, local 변수로 인식함

	```
	c = 30
	
	def func_v3(a):
	    print(a)
	    print(c) # Unbounded Error
	    c = 40
	```
	
	- 예제 3-1
		- 바깥 c는 30이 출력됨

	```
	c = 30
	def func_v3(a):
	    c = 40
	    print(a)
	    print(c)
	    
    print('>>', c)
    func_v3(10)
    ```
	
	- 예제 3-2
		- global 키워드 이해하기
		
	```
	c = 30
	def func_v3(a):
	    global c
	    print(a)
	    print(c) # 30
	    c = 40
	    
    print('>>', c) # 30
    func_v3(10)	
    print('>>>', c) # 40
	```
	
- 클로저 사용 이유
	- remember value enclosing scopes
	- 스코프가 닫혀서 값을 기억함
	- 변수 값이 소멸되지 않고, 기억!
	- 서버 프로그래밍 => 동시성(Concurrency) 제어 => 메모리 공간에 여러 자원이 접근 => 교착 상태(Dead Lock)
	- 파이썬에선 메모리를 공유하지 않고 메세지 전달로 처리하기 위해 => Erlang 등
	- 클로저는 공유하되 변경되지 않는(Immutable, Read Only) 적극적으로 사용 => 함수형 프로그래밍
	- 클로저는 불변자료구조 및 atom, STM => 멀티스레드(Coroutine) 프로그래밍에 강점
- Class -> Closure 구현

```
a = 100
print(a + 100)
print(a + 1000)

# 결과 누적(함수 사용)
print(sum(Range(1, 51)))

print(sum(range(51, 101)))

10 = 1.0
10 = 2
10 = 3

# 누적해서 평균값을 구하는걸 클래스로 만들자
```
	
- 누적해서 평균값을 구하는 클래스 만들기
	- 클래스로 구현해서 값을 저장해둠
	- 클로저는 자유 영역에 있는 값을 함수가 끝나더라도 기억하는 것!


```
class Averager():
    def __init__(self):
        self._series = []
        
    def __call__(self, v):
        self._series.append(v)
        print('inner >> {} / {}'.format(self._seriez, len(self._series)))
        return sum(self._series) / len(self._series)
        
인스턴스 생성
averager_cls = Averager()

# __call__이 보임
print(dir(Averager_cls))

# 누적
print(averager_cls(10))
print(averager_cls(20))
print(averager_cls(30))  
print(averager_cls(3123))    
  
```	



### 클로저 심화(3-1)
- callable로 구현한 걸 클로저로 구현할 예정
	- 여러 값을 담으면 Class가 더 좋을 수 있고, 아닌 경우 클로저도 좋고

```
def closure_ex1():
    # Free Variable
    # 클로저 영역
    series = []
    def averager(v):
        series.append(v)
        print('inner >> {} / {}'.format(series, len(series)))
        return sum(series) / len(series)  
    return averager
    
avg_closure1 = closure_ex1()

print(avg_closure1)
# <local> averages 함수라고 나옴
print(avg_closure(10))
print(avg_closure(30))
print(avg_closure(50))
```

- function inspection

```
print(dir(avg_closure1))
print(dir(avg_closure1.__code__))
# co_xxx, co_freevars 등이 나옴

print(avg_closure1.__code__.co_freevars
# series가 출력됨

print(dir(avg_closure1.__closure__))
# List로 되어있음

print(avg_closure1.__closure__[0].cell_contents)
# [10, 30, 50]이 저장됨
```

- (고민) 그럼 클로저에서 값 언제 소멸될까?

- 잘못된 클로저 사용


```
def closure_ex2():
    # Free variable
    cnt = 0
    total = 0
    
    def averager(v):
        cnt += 1
        total += v
        return total / cnt
    return averager
    
avg_closure2 = closure_ex2()
print(avg_closure2(10))
# locabl variable 'cnt' referenced before assignment
# Free variable쪽에 먼저 정의됨
    
```

- 실행되게 설정
	- nonlocal 키워드 추가
	
```
def closure_ex3():
    # Free variable
    cnt = 0
    total = 0
    
    def averager(v):
        nonlocal ct, total
        cnt += 1
        total += v
        return total / cnt
    return averager
    
avg_closure3 = closure_ex3()
print(avg_closure3(15))
print(avg_closure3(35))
```


- 함수 내에서 global 쓰는건 실수할 수 있으므로, 좋은 코드는 아니라고 생각함


### 데코레이터
- 본질은 클로저
- 사용했을 때와 안했을 때 비교할거임
- 알아야 하는 내용
	- 클로저
	- 일급 함수
	- 가변 인자
	- 인자 풀기
- 데코레이터의 장점
	- 1) 중복 제거, 코드 간결, 공통 함수 작성
	- 2) 로깅, 프레임워크, 유효성 체크 => 공통 기능
		- 실행 시간!
- 데코레이터의 단점
	- 1) 가독성 감소?
	- 2) 특정 기능에 한정된 함수는 단일 함수에 작성하는 것이 유리
	- 3) 디버깅 불편
- classmethod, staticmethod 모두 데코레이터임
- 사용하긴 직관적이고 쉬운데, 만드는 것은 어려울 수 있음
- 데코레이터 실습(실행 시간 측정)

```
import time

def perf_clock(func):
    def perf_clocked(*args):
        st = time.perf_counter()
        result = func(*args)
        et = time.perf_counter() - st
        
        name = func.__name__
        arg_str = ', '.join(repr(arg) for arg in args)
        print('[%0.5fs] %s(%s) -> %r' % (et, name, arg_str, result))
        return result
    return perf_clocked

def time_func(seconds):
    time.sleep(seconds)
    
def sum_func(*numbers):
    return sum(numbers)
    
    
```

- 데코레이터를 사용하지 않고 실행하기

```
none_deco1 = perf_clot(time_func)
none_deco2 = per_cloc(sum_func)


print(none_deco1, non_deco1.__code__.co_freevars)
print(none_deco2, non_deco2.__code__.co_freevars)

print('-' * 40, 'Called None Decorator -> time_func')
non_deco1(1.5)
print('-' * 40, 'Called none Decorator -> sum_func)
non_deco2(100, 200, 300, 400, 500)
```


- 데코레이터 사용하기

```
import time

def perf_clock(func):
    def perf_clocked(*args):
        st = time.perf_counter()
        result = func(*args)
        et = time.perf_counter() - st
        
        name = func.__name__
        arg_str = ', '.join(repr(arg) for arg in args)
        print('[%0.5fs] %s(%s) -> %r' % (et, name, arg_str, result))
        return result
    return perf_clocked

@perf_clock
def time_func(seconds):
    time.sleep(seconds)
    
@perf_clock
def sum_func(*numbers):
    return sum(numbers)
    
print('-' * 40, 'Called Decorator -> time_func')
time_func(1.5)

print('-' * 40, 'Called Decorator -> sum_func)
sum_func(100, 200, 300, 400, 500)
```





