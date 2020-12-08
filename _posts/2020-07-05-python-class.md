---
layout: post
title:  "파이썬 중급 - Python Class 예제 및 사용법"
subtitle: "파이썬 중급 - Python Class 예제 및 사용법"
categories: development
tags: python
comments: true
---

- Python Class 예제 및 Python Class 사용법에 대해 작성한 글입니다
- 키워드 : Python Class, Python Class Example, Python Class Use Case
- 목차
	- [객체 지향 프로그래밍](#객체-지향-프로그래밍)
	- [절차 지향 프로그래밍 예시](#절차-지향-프로그래밍-예시)
	- [객체 지향 프로그래밍 살펴보기](#객체-지향-프로그래밍-살펴보기)
	- [메소드의 종류](#메소드의-종류)
	- [상속](#상속)
	- [Setter와 Getter, Property](#setter와-getter-property)
	- [추상 메소드](#추상-메소드)
	- [slots](#slots)

	
---

### Python Class
- 파이썬을 처음 공부하면 함수나 라이브러리 import 등은 나름 수월하게 진행함(훈련을 통해서)
- 단, Class 부분부터 약간 어려움이 존재할 수 있음
	- 붕어빵 이야기, 붕어빵 틀 이야기 등으로 쉽게 설명을 들어도 이해가 안되었던 경험이 있음
- 이번에 하나씩 정리하려는 목적으로 글을 작성
- 쥬피터 노트북 등을 사용해서 처음 파이썬을 익히면 일종의 흐름 단위로 프로그래밍을 함

<br />

---

<br />


### 객체 지향 프로그래밍
- Object-Oriented Programming
- 프로그램을 단순히 데이터와 처리 방법으로 나누지 않고, 프로그램을 객체 단위로 생각하고 객체끼리 상호작용을 표현하는 방식
	- 코드의 재사용, 코드 중복 방지, 유지보수 등을 위해 사용함
- 과거엔 규모가 큰 프로젝트는 함수 중심으로 개발함
	- 데이터가 방대해지고 복잡해져서 추후 개선이 어려움
- 항상 객체 지향이 최선은 아니며 프로젝트의 규모 등에 따라 다름


	
### 절차 지향 프로그래밍 예시
- 절차 지향 프로그래밍
	- 순차적으로 진행되는 프로그래밍
- 먼저 절차 지향 방식으로 구현하고, 점점 객체 지향으로 구현해보기
- 일반적인 코딩
	- 객체 없이 그냥 변수를 생성해서 만듬. 추가 요소를 만들 때는 숫자와 값만 변경하고 계속 증가시킴
	- 스마트폰과 스마트폰 정보를 출력하려면 두 변수를 같이 프린트해야 함

	```
	smartphone_1 = 'Iphone'
	smartphone_information_1 = [
	    {'color' : 'White'},
	    {'price': 10000}
	]
	
	print(smartphone_1)
	print(smartphone_information_1)
	```
	
	
- Dictionary를 사용해 스마트폰과 스마트폰 정보를 저장하는 방법
	- 딕셔너리의 Key는 중복 불가능한 문제가 존재
	- 정렬 문제 => OrderedDict을 사용하면 해결
	- Key가 만약 없을 경우 예외 처리를 해야 함 => DefaultDict을 사용하면 해결
	- 스마트폰, 스마트폰 정보를 출력할 경우엔 딕셔너리만 출력하면 됨

	```
	smartphone_dicts = [
	    {'brand': 'Iphone', 'smartphone_information': {'color' : 'White', 'price': 10000}},
	    {'brand': 'Galaxy', 'smartphone_information': {'color' : 'Blue', 'price': 8000}},
	]
	
	print(smartphone_dicts)
	```	
	
<br />
<br />

---
	
### 객체 지향 프로그래밍 살펴보기
- 코드의 중복 사용이 최소화되서 재사용성이 증가
- 클래스의 매직 메소드를 사용할 수 있음
- 네임스페이스
	- 프로그래밍 언어에서 객체를 구분할 수 있는 공간
	- 변수를 객체에 할당하면, 해당 객체는 딕셔너리 형태로 연결되서 네임스페이스에 저장됨
	- 파이썬의 클래스는 새로운 타입(객체)을 정의하기 위해 사용되고, 모듈과 마찬가지로 하나의 네임스페이스를 가짐
	- `globals()`를 사용하면 전역 네임스페이스를 확인할 수 있음
	- 함수는 전역과 별개의 네임스페이스를 가짐
	
	```
	temp_variable = a
	print(globals()) 
	```
	
- `__init__` : 클래스 인스턴스 생성시 초기화하며 실행되는 부분
- class의 값을 보고 싶으면 `__dict__`을 사용
- python class magic method - str
	- `__str__` 매직 메소드가 구현되어 있지 않은 상태에서 인스턴스를 print하면 object가 나옴
	- print() 또는 str() 함수가 호출될 때 사용	- 기본적으로 str 메소드가 먼저 실행되며, str 메소드가 없으면 repr 메소드를 실행함
- python class magic method - repr
	- `__repr__` : str과 비슷
	- 개발, 엔지니어 레벨에서 객체의 엄격한 타입을 표현시 사용
	- 객체 표현을 반환함
	- repr() 함수가 호출될 때 사용
- python class magic method - dir
	- 해당 인스턴스가 가진 모든 attribute를 list 형태로 보여줌(값을 보여주진 않음)
- python class magic method - dict
	- `__dict__`
	- 특정 네임스페이스만 보고 싶다면, `__dict__`를 사용한 후, 특정 네임스페이스에 접근하면 됨
- python class magic method - doc
	- `__doc__`
	- docstring을 출력


```
class Smartphone:
	"""
	Smartphone class
	"""
    def __init__(self, brand, informations):
        self._brand = brand
        self._informations = informations

    def __str__(self):
        return f'str : {self._brand} - {self._informations}'

    def __repr__(self):
        return f'repr : {self._brand} - {self._informations}'
    

Smartphone1 = Smartphone('Iphone', {'color' : 'White', 'price': 10000})
Smartphone2 = Smartphone('Galaxy', {'color' : 'Black', 'price': 8000})

print(Smartphone1)
print(Smartphone1.__dict__)

print(Smartphone1._brand == Smartphone2._brand)
print(Smartphone1 is Smartphone2)

print(Smartphone.__doc__)    
```
	
	
- self란?
	- self는 자기 자신을 뜻함. 인스턴스 자신
		- 인스턴스 : 클래스에 의해 만들어진 객체
	- self가 있는 것이 인스턴스 변수
	- 인자에 self를 받는 것은 인스턴스 메소드 : get_information 함수
- 인스턴스의 `__class__`는 사용된 클래스를 출력함


```
class Smartphone:
    """
    Smartphone Class
    """

    def __init__(self, brand, details):
        self._brand = brand
        self._infomations = infomations

    def __str__(self):
        return f'str : {self._brand} - {self._ infomations}'

    def __repr__(self):
        return f'repr : {self._brand} - {self._ infomations}'

    def get_infomation(self):
        print(f'Current Id : {id(self)}')
        print(f'Smartphone Detail Info : {self._brand} {self._infomations.get("price")}')

        Smartphone1 = Smartphone('Iphone', {'color': 'White', 'price': 10000})
        Smartphone2 = Smartphone('Galaxy', {'color': 'Black', 'price': 8000})
        
        Smartphone1.detail_info()

        print(Smartphone1.__class__, Smartphone2.__class__)
        # 부모는 같음
        print(id(Smartphone1.__class__) == id(Smartphone2.__class__))
```	

- 클래스 변수
	- 클래스 내부에 선언된 변수
	- 클래스 변수는 클래스의 네임스페이스에 위치함
	- 모든 인스턴스가 공유하는 변수
	- `Smartphone1.__dict__`를 출력하면 클래스 변수는 보이지 않음
	- `dir(Smartphone1)`를 출력할 때는 클래스 변수가 보임
- 인스턴스 변수
	- self.name 같이 self가 붙은 변수
	- 인스턴스 변수는 인스턴스의 네임스페이스에 위치함
	- 인스턴스 네임스페이스에서 없으면 상위에서 검색
	- 즉, 동일한 이름으로 변수 생성 가능(인스턴스 검색 후 => 상위 클래스, 부모 클래스 변수)


```
class Smartphone:
    """
    Smartphone Class
    """
    # 클래스 변수
    smartphone_count = 0
    
    def __init__(self, brand, infomations):
        self._brand = brand
        self._infomations = infomations
        Smartphone.smartphone_count += 1

    def __str__(self):
        return f'str : {self._brand} - {self._infomations}'

    def __repr__(self):
        return f'repr : {self._brand} - {self._infomations}'

    def infomation(self):
        print(f'Current Id : {id(self)}')
        print(f'Smartphone Detail Info : {self._brand} {self._infomations.get('price'))}'

    def __del__(self):
        Smartphone.smartphone_count -= 1
    
Smartphone1 = Smartphone('Iphone', {'color' : 'White', 'price': 10000})
Smartphone2 = Smartphone('Galaxy', {'color' : 'Black', 'price': 8000})
	
	
print(Smartphone.__dict__)
print(Smartphone1.__dict__)
print(Smartphone2.__dict__)
print(dir(Smartphone1))
	
print(Smartphone1.smartphone_count)
print(Smartphone.smartphone_count)
```		

<br />
<br />

---

### 메소드의 종류
- 클래스 메소드(Python Class Method)
	- @classmethod 데코레이터를 사용
	- cls 인자를 받음
	- cls는 Smartphone를 뜻함(인스턴스 말고 클래스)
	- 클래스 변수 컨트롤할 때 사용
- 인스턴스 메소드(Python Instance Method)
	- Self가 들어간 경우
	- 객체의 고유한 속성 값을 사용
- 스태틱 메소드(Python Static Method)
	- 아무것도 인자를 받지 않음(self, cls 등)
	- 유연하게 사용함
	- [Meaning of @classmethod and @staticmethod for beginner?](https://stackoverflow.com/questions/12179271/meaning-of-classmethod-and-staticmethod-for-beginner) 참고
	
	
```
class Smartphone:
    """
    Smartphone Class
    """
    Smartphone_count = 0
    
    def __init__(self, brand, informations):
        self._brand = brand
        self._informations = informations
        Smartphone.smartphone_count += 1

    def __str__(self):
        return f'str : {self._brand} - {self._informations}'

    def __repr__(self):
        return f'repr : {self._brand} - {self._informations}'

    def detail_info(self):
        print(f'Current Id : {id(self)}')
        print(f'Smartphone Detail Info : {self._brand} {self._informations.get('price'))}'

    def get_price(self):
        return f'Before Smartphone Price -> brand : {self._brand}, price : {self._informations.get('price')}'

    def get_price_culc(self):
        return f'After Smartphone Price -> brand : {self._brand}, price : {self._informations.get('price') * Smartphone.price_per_raise}'

    @classmethod
    def raise_price(cls, per):
        if per <= 1:
            print('Please Enter 1 or More')
            return
        cls.price_per_raise = per
        return 'Succeed! price increased.'

    @staticmethod
    def is_iphone(inst):
        if inst._brand == 'Iphone':
            return f'OK! This Smartphone is {inst._brand}.'
        return 'Sorry. This Smartphone is not Iphone.'
    
    
Smartphone1 = Smartphone('Iphone', {'color' : 'White', 'price': 10000})
Smartphone2 = Smartphone('Galaxy', {'color' : 'Black', 'price': 8000})
# 기본 정보
print(Smartphone1)
print(Smartphone2)

Smartphone1.information()
Smartphone2.information()

print(Smartphone1.get_price())
print(Smartphone2.get_price())

# 가격 인상(클래스 메소드 미사용)
# 이렇게 직접 접근은 좋지 않아요
Smartphone.price_per_raise = 1.2

# 가격 정보
print(Smartphone1.get_price_culc())
print(Smartphone2.get_price_culc())

# 가격 인상
Smartphone.raise_price(1.6)

# 가격 정보
print(Smartphone1.get_price_culc())
print(Smartphone2.get_price_culc())

def is_iphone(inst):
    if inst._brand == 'Iphone':
        return f'OK! This Smartphone is {inst._brand}.'
    return 'Sorry. This Smartphone is not Iphone.'

print(is_iphone(Smartphone2))

print('Static : ', Smartphone.is_iphone(Smartphone1))
print('Static : ', Smartphone.is_iphone(Smartphone2))

```	
	
<br />
<br />

---
	
### 상속	
- Class는 상속을 통해 자식 클래스에게 부모 클래스의 속성과 메소드를 물려줌
	- 예를 들어 Smartphone Class가 있고, Iphone Class, Galaxy Class 등이 있는 상황
	- Iphone과 Galaxy가 가치는 속성(attribute)는 다를 수 있음
	- 다중 상속도 가능함

```
class Smartphone:
    def __init__(self, brand, price):
        self._brand = brand
        self._price = price
      
    
    def __str__(self):
        return f'str : {self._brand} - {self._price}'

class Galaxy(Smartphone):
    def __init__(self, brand, price, country):
        self._brand = brand
        self._price = price
        self._country = country
 
    def __str__(self):
        return f'str : {self.__class__.__name__} 스마트폰은 {self._brand}에서 출시되었고, {self._country}에서 생산되었습니다. 가격은 {self._price}입니다'
    
iphone = Smartphone('IPhone', 7000)
print(iphone)
galaxy = Galaxy('Galaxy', 5000, 'South Korea')
print(galaxy)    
```

<br />
<br />

---

### Setter와 Getter, Property
- 객체의 속성(attribute)를 다룰 때 사용
- getter, setter는 객체의 속성을 읽고 변경할 때 사용함
	- 참고로 객체(Object)는 속성(Attribute)와 Method로 구현
- 자바 같은 객체 지향 언어에서 외부에서 바로 접근할 수 없는 private 객체 속성을 지원함
	- 이런 언어에선 private 속성의 값을 읽고(get) 변경(set)하기 위해 getter와 setter를 사용함
	- 파이썬은 모든 메서드가 public이기 때문에 getter와 setter 메소드가 없지만, 사용할 수는 있음

```
class Smartphone:
    def __init__(self, brand, price):
        self._brand = brand
        self._price = price
        
    def get_price(self):
        return self._price
        
    def set_price(self, price):
        self._price = price
    	
```

- property
	- 파이썬은 속성에 직접 접근을 막기 위해 property를 사용함
	- 데코레이터로 감싸서 사용함

```
class Smartphone:
    def __init__(self, brand, price):
        self._brand = brand
        self._price = price
 
    @property
    def price(self):
        return self._price
	    
    @price.setter
    def price(self, price):
        print(f"변경 전 가격 : {self._price}")
        self._price = price
        print(f"변경 후 가격 : {self._price}")

Smartphone1 = Smartphone("Iphone", 1000)
Smartphone1.price = 10000
```

- property를 사용하면 value의 제한을 들 수 있음
	- 예를 들면, Smartphone class에서 가격이 0원 미만일 경우 에러를 발생시킬 수 있음(property가 아니고 get, set으로 구현하면 에러가 발생하진 않음)
- 사용하는 목적
	- 변수를 변경할 때 제한사항을 두고 싶은 경우
	- getter, setter 함수를 만들지 않고 간단히 접근하기 위함

```
class Smartphone:
    def __init__(self, brand, price):
        self._brand = brand        self._price = price
	    
    @property
    def price(self):        return self._price
	    
    @price.setter
    def price(self, price):
        if price < 0:
            raise ValueError("Price below 0 is not possible")
        print(f"변경 전 가격 : {self._price}")
        self._price = price
        print(f"변경 후 가격 : {self._price}")

Smartphone1 = Smartphone("Iphone", 1000)
Smartphone1.price = 10000   
Smartphone1.price = -1000
```	



<br />
<br />

---

### 추상 메소드
- Class를 만들었다면 Class에서 정의된 메소드를 그대로 사용하지 않을 수 있음
	- 상속받고, 어떤 메소드는 추가하고 어떤 메소드는 오버라이드할 수 있음
	- 통일된 Class 체계를 구축하며 확장 기능을 가능하게 만드는 것이 Class
- 이런 개념을 토대로, Class를 만들 때 반드시 구현해야 할 메소드를 명시할 수 있음
- 아래 코드는 Sonata에서 func1 함수를 구현하지 않아 오류가 발생함

```
class Smartphone:
    def func1(cls):
        raise NotImplementedError()

class Iphone(Smartphone):
    pass
    
    
iphone = Iphone()
iphone.func1() # Error 발생
```

- 조금 더 Strict하고 세련된 방법은 `@abc.abstractmethod`를 사용하는 방법
	- abc는 abstract base class의 약자
	- Class의 인자로 metaclass=abc.ABCMeta를 지정하고,
		- Class에 데코레이터로 abc.abstractmethod를 사용하면, class가 호출되는 순간 구현해야 하는 메소드가 구현되었는지를 확인함
		- 참고 : metaclass는 단순히 클래스를 만들 때 사용됨. 클래스의 클래스
	- 바로 에러가 발생하기 때문에 인스턴스화시키지 않고, 추상화에 이점이 존재

```
import abc

class Smartphone(metaclass=abc.ABCMeta):
    @abc.abstractmethod
    def func1(cls):
        raise NotImplementedError()

class Iphone(Smartphone):
    def func2(self):
        pass


iphone = Iphone() # Error 발생
```


<br />
<br />

---

### slots
- 파이썬의 클래스들은 인스턴스 속성을 저장함
- 파이썬에서 객체의 인스턴스 속성을 저장하기 위해 dictionary를 사용함
	- 이런 방식은 속성을 추가하거나 삭제할 때 유용함
	- `obj.__dict__`으로 접근하는 경우
- 그러나 dictonary 자료 구조는 메모리를 많이 낭비함
	- 파이썬 객체 생성시 모든 속성을 메모리에 할당하려고 함
	- 메모리 할당량이 많으면 많은 RAM을 낭비하게 됨
- 이런 문제를 해결하기 위해 `__slots__`를 사용해 특정 속성에만 메모리를 할당하도록 함
- [ipython memory usage](https://github.com/ianozsvald/ipython_memory_usage)를 사용하면 `__slots__`를 사용해서 메모리 사용량이 얼마나 개선되는지 확인할 수 있음

- `__slots__` 없이 짠 코드

```
class Smartphone:
    def __init__(self, brand, price):
        self._brand = brand
        self._price = price
        self.set_up()
	    
    # 코드 생략
    # set_up은 init시 실행하는 함수라고 생각
```

- `__slots__`를 사용해 짠 코드

```
class Smartphone:
    __slots__ = ['_brand', '_price']
   
    def __init__(self, brand, price):        self._brand = brand        self._price = price
        self.set_up()
	    
    # 코드 생략
    # set_up은 init시 실행하는 함수라고 생각
```    



- 추후 추가적으로 다룰 이야기
	- MetaClass


<br />
<br />

---

### Reference	
- [인프런 파이썬 중급 프로그래밍](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A4%91%EA%B8%89-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)
- [Python Class Document](https://docs.python.org/ko/3/tutorial/classes.html)