---
layout: post
title:  "파이썬 중급 - Python Sequence 사용법"
subtitle: "파이썬 중급 - Python Sequence 사용법"
categories: development
tags: python
comments: true
---

- Python Sequence 사용법에 대해 작성한 글입니다



### Sequence
- 순서가 있음
- list, mutable, 재할당, 삭제, 원소에 추가 가능
- 컨테이너 vs 플랫형
	- Container : 서로 다른 자료형 저장
		- list, tuple, collections.deque
	- Flat : 한 개의 자료형
		- str, bytes, bytesarray, array.array, memoryview
- 가변형 vs 불변형
	- 가변형 : list, bytearray, array.array, memoryview, deque
	- 불변형 : tuple, str, bytes




### List Comprehension

- 아래 코드는 실행할 수 없음

	```
	chars = '+_)$%^&*&*)'
	chars[1] = 2
	```

- 아래처럼 작성하면 실행 가능

	```
	chars = '+_)$%^&*&*)'
	test_list = []
	
	for s in chars:
	    test_list.append(ord(s))
	```

- 이걸 List Comprehension으로 바꾸면

	```
	test_list2 = [ord(s) for s in chars]
	```

- Map, Filter를 사용해 필터링하기
	- Filter 함수는 익명 함수를 받아서 어떤 것을 하는지 작성함
	
	```	
	test_list3 = [ord(s) for s in chars if ord(s) > 99]
	test_list4 = list(filter(lambda x : x > 99, map(ord, chars)))
	
	```

### Generator
- Local state를 유지하고 정확히 언제 불릴지 아는 함수
- 이터레이터의 일종
	- `__iter__`가 구현됨. next()로 다음 값을 가져올 수 있음
- 한 번에 한 개의 항목을 생성(메모리 유지하지 않음)
- array 사용



```
import array 
generators = (ord(s) for s in chars)

array_generators = array.array('K',  (ord(s) for s in chars))

print(type(generators))
print(next(generators))
print(type(array_generators))
print(array_generators())
```	

- 리스트 사용할 때 주의할 점
	- 깊은 복사와 얕은 복사

```
test_values1 = [['^'] * 5 for n in range(5)]
test_values2 = [['^'] * 5] * 5
```

- 현재 출력 결과는 동일함
	- 그러나 수정하려고 하면

	```
	test_values1[0][1] = 'X' # 하나만 바뀜
	test_values2[0][1] = 'X' # 모두 바뀜
	```	
	
- 첫번째는 id가 모두 다르고, 두번째는 똑같은게 4개 출력된거라 id가 같음





### 시퀀스 실습

- Tuple Advanced
	- Unpacking

	```
	b, a = a, b
	
	print(divmod(100, 9)) # (11, 1)
	print(divmod(*(100, 9))) # (11, 1)
	print(*(divmod(100, 9))) # 11, 1
	```

	```	
	x, y, *rest, = range(10)
	print(x, y, rest)
	# 1, 2, [3, 4, 5, 6, 7, 8, 9]
	```

	```
	x, y, *rest = range(2)
	print(x, y, rest)
	# 1, 2, []
	```
	
	```
	x, y, *rest = 1, 2, 3, 4, 5
	print(x, y, rest)
	# 1, 2, [3, 4, 5]
	```
	
- Mutable(가변) vs Immutable(불변)
	- 튜플!

```
a = (1, 2, 3)
b = [1, 2, 3]

print(a, id(a))
print(b, id(b))

a = a * 4
b = b * 4

print(a, id(a))
print(b, id(b)) 
# id 값이 다름

a *= 4
b *=4
```	

- Tuple은 불변이라 내부적으로 재할당. 계속 바뀜
	- List는 계속 같은 값에 할당

	
- Sort vs Sorted
	- sorted : 정렬 후 새로운 객체 반환
	- sort : 정렬 후 객체 직접 변경


```
animal_list = ["dog", "cat", "pig", "cow"]

print('sorted', sorted(animal_list))
print('sorted', sorted(animal_list, reverse=True))
print('sorted', sorted(animal_list, key=len))
print('sorted -', sorted(animal_list, key=lambda x: x[-1])) # 키 
print('sorted -', sorted(animal_list, key=lambda x: x[-1], reverse=True))
```	
	
- sort
	- 이 대상을 sort!
	- return None	
	
```
# 반환 값 확인(None)
print('sort -', animal_list.sort(), f_list)
print('sort -', animal_list.sort(reverse=True), f_list)
print('sort -', animal_list.sort(key=len), f_list)
print('sort -', animal_list.sort(key=lambda x: x[-1]), f_list)
print('sort -', animal_list.sort(key=lambda x: x[-1], reverse=True), animal_list)
```	


- List vs Array
	- 리스트 기반 : 융통성, 다양한 자료형, 범용적 사용
	- 숫자 기반: 배열(리스트와 거의 호환) 예를 들어 numpy

	
	
### 해시 테이블
- 키가 중복될 경우 어떻게 처리하는가? 인터뷰에 자주 나오는 질문
- 해시 테이블은 Key에 Value를 저장하는 구조
	- 작은 데이터로 많은 데이터를 효율적으로 관리
- 파이썬 dict이 해쉬 테이블의 예시
- 왜 쓰는가?
	- 키 값의 연산 결과에 따라 직접 접근이 가능한 구조
- 키 값을 기준으로 해싱 함수 => 해시 주소가 나오고 = key에 대한 value 참조	

- 해시 값 확인
	- 고유하단 뜻!
	- 바꿀 수 없음
	- 리스트는 hash 값이 없음
	- 불변형이여야 함

```
value1 = (1, 2, (3, 4, 5))
value2 = (1, 2, [3, 4, 5])

print(hash(value1)
print(hash(value2) 
```	



- Dict Setdefault
	- 아래 값을 Dict으로 바꾸고 싶다면?
	- 앞글자는 중복이 있어서 안됨
	
	
	
```
sourcevalue = (('a1', 'val1'),
               ('a1', 'val2'),
               ('a2', 'val3'),
               ('a2', 'val4'),
               ('a2', 'val5'))
               
```


- setdefault 사용 X

```
new_dict1 = {}
new_dict2 = {}

for k, v in sourcevalue:
    if k in new_dict1:
        new_dict1[k].append(v)
    else:
        new_dict1[k] = [v]
```

- setdefault 사용


```
new_dict1 = {}
new_dict2 = {}

for k, v in sourcevalue:
    new_dict2.setdefault(k, []).append(v)

print(new_dict2)   
```	

- 주의 사항
	- dict comprehension은 안됨! 키가 중복되면 나중 값이 저장됨

	
	
### 시퀀스 4-1
- 딕셔너리 mutable! 값을 수정할 수 있음
- 그러나 read 전용 dict이 있음
- 해시테이블(hashtable) -> 적은 리소스로 많은 데이터를 효율적으로 관리
- Dict -> Key 중복 허용 X, Set -> 중복 허용 X
- Dict 및 Set 심화
	
	
	
- immutable Dict
	- types의 MappingProxyType을 활용할 수 있음

	```
	from types import MappingProxyType

	dict_example = {'key1': 'value1'}

	dict_example_frozen = MappingProxyType(dict_example)
	
	print(dict_example, id(dict_example))
	
	print(dict_example_frozen, id(dict_example_frozen), hash(dict_example_frozen))
	
	dict_example_frozne['key2'] = 'value2' # Error 발생, 수정 불가
	dict_example['key2'] = 'value2' # 값 수정
	```
	
	
	
- 집합 자료형
	- 선언하는 방법
		
	```
	animal_set1 = {"dog", "cat", "pig", "cow"}
	animal_set2 = set(["dog", "cat", "pig", "cow"])
	animal_set3 = {3}
	animal_set4 = set() # {}라고 하면 Dict이 됨
	animal_set5 = frozeset({"dog", "cat", "pig", "cow"})
	```	
	
	- 값을 추가할 땐 animal_set1.add("rabbit")
	- frozenset은 추가할 수 없음

- 선언 최적화
	- 파이썬은 실행할 때, 바이트 코드를 실행함 => 파이썬 인터프리터가 실행
	- dis를 보면 바이트 코드 순서를 볼 수 있음
	
	
	```
	from dis import dis
	
	print('------')
	print(dis('{5}'))
	
	print('------')
	print(dis('set([5])'))
```	

	- `{}`로 정의하는거 미세하게 빠름


- 지능형 집합(Comprehending Set)
	
	```
	from unicodedata import name

	print('------')
	
	print({name(chr(i), '') for i in range(0,256)})
	```