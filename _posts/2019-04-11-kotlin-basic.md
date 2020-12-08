---
layout: post
title:  "Kotlin 기본 문법 정리"
subtitle: "Kotlin 기본 문법 정리"
categories: development
tags: kotlin
comments: true
---
	 
- 코틀린 기본 문법에 대한 글입니다

---

### Kotlin
- 코틀린은 안드로이드나 서버 개발을 할 때 사용할 수 있는 언어
- [kotlin server overview](https://kotlinlang.org/docs/reference/server-overview.html) 참고하면 서버 사이드에서 장점을 알 수 있음
	- 자바에서 많이 쓰이는 Spring도 사용 가능
- [VCNC 시스템 아키텍쳐](http://engineering.vcnc.co.kr/2019/01/tada-system-architecture/)를 보면 서버 언어를 코틀린으로 쓰는 회사가 점점 증가하는듯!
	- 이 코드를 읽기 위해 코틀린을 공부합니다..! 


### Basic Syntax

- 패키지 정의
	
        package my.demo
        
        import java.util.*

- 함수 정의
    - fun 키워드로 정의

        fun sum(a: Int, b: Int): Int {
            return a + b
        }

    - 함수 몸체가 식인 경우 return 생략 가능, return type이 추론됨

        fun sum(a: Int, b: Int) = a + b

    - 리턴할 값이 없는 경우 Unit(Object)으로 리턴
        - 자바의 void 역할

        fun printKotlin(): Unit {
        	println("Hello Kotlin")
        }

    - Unit은 생략 가능

            fun printKotlin() {
            	println("Hellok Kotlin")
            }

- 변수 정의
    - val : 읽기전용 변수
    - 값의 할당이 1회만 가능, 자바의 final과 유사

            val a: Int = 1
            val b = 2 // int type 추론
            val c: Int // 컴파일 오류, 초기화 필요(값 할당 안함)
            c = 3 // 컴파일 오류, 읽기 전용이라 추후에 할당 불가

    - var : Mutable 변수

            var x = 5
            x += 1

- 주석
    - 자바와 자바스크립트와 동일
    - // : 한줄 추석
    - /* */ : 여러 줄 주석(여러개 중첩 가능)

- 문자열 템플릿
    - String Interpolation(문자열 보간법)

            var a = 1
            val s1 = "a is $a"
            
            a = 2
            val s2 = "${s1.replace('is', 'was')}, but now is $a"

- 조건문

        fun maxOf(a: Int, b: Int): Int {
        	if (a > b) {
        		return a
        	} else {
        		return b
        	}
        }

    - 조건식으로 사용 가능

            fun maxOf(a: Int, b: Int) = if (a>b) a else b

- nullable
    - 값이 null일 수 있는 경우 타입에 nullable 마크를 명시

            func parseInt(str: String): Int?{
            // 정수가 아니면 null 리턴
            }

    - nullable 타입의 변수를 접근할 땐 반드시 null 체크를 해야 함
    - 그렇지 않으면 컴파일 오류 발생

            fun printProduct(arg1: String, arg2: String) {
            	val x: Int? = parseInt(arg1)
            	val y: Int? = parseInt(args2)
            
            	if (x != null && y!= null) {
            		println(x*y)
            	} else {
            		println("eithe '$arg1' or '$arg2' is not a number")
            }

- 자동 타입 변환
    - 타입 체크만 해도 자동으로 타입 변환!!!

        obj: Any는 object의 최상위 객체

            fun getStringLength(obj: Any): Int?{
            	if (obj is String) {
            			// obj가 자동으로 string 타입으로 변환
            			return obj.length
            	}
            	return null
            }

- while loop

        val items = listOf("apple", "banana", "kiwi")
        var index = 0
        while (index < items.size) {
        	println("item at $index is ${items[index]}")
        	index ++
        }

- when expression

        fun describe(obj: Any): String =
        		when (obj) {
        			1 -> "One"
        			"Hello" -> "Greeting"
        			is Long -> "Long"
        			!is String -> "Not a string"
        			else -> "Unknown"
        		}

- ranges
    - In 연산자를 이용해 숫자 범위 체크 가능

            val x = 3
            if (x in 1..10) {
            	println("fits in range")
            }

    - range를 이용한 for loop

            for (x in 1..5) {
            	println(x)
            }

- collections
    - 컬렉션도 in으로 loop 가능

            val items = listOf("apple", "banana", "kiwi")
            for (item in items) {
            	println(item)
            }

    - in으로 해당 값이 collection에 포함되는지 체크 가능

            val items = setOf("apple", "banan", "kiwi")
            when {
            	"orange" in items -> println("juicy")
            	"apple" in items -> println("apple is fine too")
            }

    - 람다식을 이용해 컬렉션에 filter, map 연산 가능

            val fruits = listOf("banana", "avocado", "apple", "kiwi")
            fruits
            			.filter { it.startsWith"a") }
            			.sortedBy { it }
            			.map { it.toUpperCase() }
            			.forEach { println(it) }

### Basic Types

- 기본 타입
    - 코틀린은 모두 객체
    - 모든 것에 멤버 함수나 프로퍼티를 호출 가능!
- 숫자
    - Java의 숫자와 비슷
    - Kotlin에서 Number는 클래스
    - Java에서 숫자형이던 char가 kotlin에선 숫자형이 아님
    - 리터럴(Literal)
        - 10진수 (Int, Short)
        - Long
        - Double
        - Float
        - 2진수
        - 8진수는 미지원
        - 16진수
    - Underscore in numeric literals
        - 언더스코어를 사용해 표현 가능

                val oneMillon = 1_000_000
                val creditCardNumber = 1234_5678_9012_3456L

- Representation
    - Java 플랫폼에서 숫자형은 JVM primitive type으로 저장
    - Nullable이나 제네릭의 경우엔 박싱
    - 박싱된 경우엔 identity를 유지하지 않음
    - show bytecode → decompile하면 자바 코드로 변환
- Explicit Conversions
    - 작은 타입은 큰 타입의 하위 타입이 아님
    - 즉, 작은 타입에서 큰 타입으로 대입이 안됨
        - 숫자끼리 변화할 땐 명시적으로 변환해야 함

            val i: Int = b.toInt()
            // toByte(), toShort(), toInt() ...

- 문자
    - Char는 숫자로 취급되지 않음

        fun check(c: Char) {
        	if (c == 'a')
        }

- 배열
    - 배열은 Array 클래스로 표현
    - get, set ([] 연산자 오버로딩됨)
    - size 등 유용한 멤버 함수 포함

            var array: Array<String> = arrayOf("코틀린", "강좌")
            println(array.get(0))
            println(array[0])
            println(array.size)

    - 배열 생성
        - Array의 팩토리 함수 사용
        - arrayOf() 등의 라이브러리 함수 이용

	            val b = Array(5, { i -> i.toString() })
	            
	            val a = arrayOf("0", "1", "2", "3", "4")

    - 특별한 Array 클래스
        - primitive 타입의 박싱 오버헤드를 없애기 위한 배열
        - IntArray, ShortArray, IntArray
        - Array를 상속한 클래스는 아니지만 Array와 같은 메소드, 프로퍼티를 가짐
        - size 등 유용한 멤버 함수 포함

                val x: IntArray = intArrayOf(1, 2, 3)
                x[0] = 7
                println(x.get(0))
                println(x[0])
                println(x.size)

- 문자열
    - 문자열은 String 클래스로 구현
    - String은 characters로 구성
    - s[i] 같은 방식으로 접근 가능
    - immutable이므로 변경은 불가

            var x: String = "Kotlin"
            println(x.get(0))
            println(x.length)

    - 문자열 리터럴
        - escape string ("Kotlin")
            - 전통적인 방식으로 Java String과 비슷
            - Backslash를 사용해 escaping 처리
        - raw string ("""Kotlin""")
            - escaping 처리 필요 없음
            - 개행이나 어떤 문자 포함 가능

		            val s = "Hello, world₩n"
		            
		            val s = """
		            '''이것은 코틀린의
		             raw String
		            입니다.'''
		            """ 
	
### Reference
- [코틀린 공식 문서](https://kotlinlang.org/docs)
- [인프런, 코틀린 강의](https://www.inflearn.com/course/코틀린-강좌-새차원)
