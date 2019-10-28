---
layout: post
comments: true
title:  "Functional Programming 이란"
date:   2018-07-07 12:41:10
author: Cory
categories: Functional_Programming
permalink : /dev/functional/concept
tags: functional kotlin swift
---
최근에 함수형 프로그래밍에 대한 관심이 증가하고 있습니다. 저도 최근 들어 함수형 프로그래밍에 대해 조금씩 들여다 보고 있는데요, 제가 공부하던 자료를 정리해보았습니다. 인프런에서 유인동 강사님의 [자바스크립트로 알아보는 함수형 프로그래밍](https://www.inflearn.com/course/함수형-프로그래밍/), 황성현님께서 드로이드 나이츠에서 발표하신 [Practical FP in Kotlin](https://www.youtube.com/watch?v=-n7ISJ-tfiU&index=7&list=PLu8dnNjU2FmvlfKqqJn-5TUQy45oQo3-b), 송치원님의 [Functional Programming이 뭐하는 건가요?](https://www.youtube.com/watch?v=HZkqMiwT-5A) 와 그 외 여러 자료들을 참고하여 작성하였음을 밝힙니다.

## 함수형 프로그래밍 이란
함수형 프로그래밍이란이란 성공적인 프로그래밍을 위해 부수 효과(Side Effect)를 최소화하고 조합성을 최대화 하는 선언형(Declarative) 프로그래밍 패러다임입니다. 이는 어떤 장점을 가지고 있을까요? 당연한 말이지만 부수 효과를 줄이게 되면 오류를 줄이고 안정성을 높일 수 있습니다. 또한 조합성을 최대화 하면 모듈화 수준을 높여야 하고 이는 생산성을 높일 수 있는 장점이 있습니다. <br>
그럼 함수형 프로그래밍이란 무엇일까요? 보통 함수형 프로그래밍을 이야기할 때 아래의 세 가지를 이야기 하는 경우가 많습니다.
- Language Features
  - _Immutable Data_, _First Class Function_, _Tail Call Optimization_ 등 에 대한 이야기 인데 이는 언어가 지원하는 특징이지 함수형 프로그래밍이라 할 수 없을 것 같습니다.
- Programming Techniques
  - _map_, _filter_, _reduce_ 등 에 대한 이야기인데 이는 프로그래밍을 함에 있어서 도움을 주는 스킬들이지 이것이 함수형 프로그래밍 특징이라 하기에는 조금 애매한 부분이 있습니다.
- Advantages
  - _Parallelization_, _Lazy Evaluation_, _Determinism_ 등은 Functional Programming 을 함에 있어서 오는 장점입니다. 즉, 원인과 결과를 반대로 이것이 함수형 프로그래밍이다 라고 하는 것은 조금 애매한 부분이 있습니다.

그렇다면 함수형 프로그래밍은 무엇으로 정의할 수 있을까요? 마이클 포거스는 함수형 프로그래밍을 __어플리케이션, 함수의 구성요소, 더 나아가서 언어 자체를 함수처럼 여기도록 만들고, 이러한 함수 개념을 가장 우선순위로 생각__ 하는 프로그래밍이라 이야기 합니다. 즉 문제를 __함수로 구성__ 하여 해결해 나가려고 노력하는 사고방식으로 생각할 수 있습니다.<br>
사실 가장 간단하게 설명하자면 함수형 프로그래밍이란 다들 많이 들어본 __pure function__ 을 최대화 하는 프로그래밍으로 생각할 수 있습니다. 아래에서는 함수형 프로그래밍을 이야기 할때 나오는 용어에 대해서 살펴보겠습니다.

## Imperative Programming vs Declarative Programming
함수형 프로그래밍에 대해 이야기할 때 항상 나오는 이야기입니다. 명령형, 선언형??? 도대체 어떤 것을 의미하는 것일까요? 아래 사진과 같이 설거지를 한다 할때 이 두 패러다임은 서로 어떤 식으로 생각하는지 참고하겠습니다.

<img src="/assets/functional/concept/concept1.png" title="functional Imperative & Declarative">

위를 프로그래밍 관점으로 생각해보면 __Imperative Programming__ 은 __How__ 의 개념으로 어떻게 결과를 얻을 것인지 생각하면서 프로그래밍하는 것이라고 생가할 수 있습니다. 즉, 어떤 연산을 수행할지 미리 정해주는 방식입니다. 따라서 명령형 프로그래밍은 __상태(State)__ 를 가지고 이 상태롤 절차적으로 주어진 연산에 따라 변화하면서 수행하게 됩니다. 그런데 상태를 가지게 되면 함수형 프로그래밍을 이야기할 때 자주 언급되는 __Side Effect__ 가 발생할 가능성이 커지는 단점이 존재합니다.<br>
반면 __Declarative Programming__ 은 __What__ 의 개념으로 어떤 결과를 얻고 싶은지를 생각하면서 프로그래밍하는 것으로 생각할 수 있습니다. 즉, 값들을 명시하는 방식으로 프로그래밍을 하게 됩니다.

아래는 피보나치에 대한 코드로 예시를 들어보았습니다. 코드는 Swift 로 작성하였습니다.
```swift
// 명령형
func fibonacciA(n: Int) -> (Int) {
    if n <= 2 {
        return 1
    }

    var a = 1
    var b = 1
    for _ in 2...n {
        let c = a + b
        a = b
        b = c
    }

    return b
}

// 선언형
func fibonacciB(n: Int) -> (Int) {
    if n <= 2 {
        return 1
    } else {
        return fibonacciB(n: n-1) + fibonacciB(n: n-2)
    }
}
```

## 순수함수
함수형 프로그래밍은 순수함수를 의미하는데 이는 Side Effect 가 없어야 하며, 참조 투명해야 한다는 것을 의미합니다. 또한 일급객체를 지원할 수도 있습니다. 하나씩 알아보도록 하겠습니다.

### Side Effect
Side Effect 란 외부에 영향을 받는 함수를 이야기힙니다. Side Effect 가 없기 위해서는 input 이 들어왔을 때 외부에 존재하는 상태를 건드리지 않고 output 을 내보내면 됩니다. 즉, scope 밖의 무언가를 건드리지 않는 함수입니다. 예를 들어 아래와 같은 코드를 생각할 수 있습니다. 위에서 Swift 로 했으니 아래 코드는 Kotlin 으로 작성하였습니다.

```Kotlin
// Side Effect 발생
val k = 2
fun addSideEffect(b:Int) : Int {
    return k+b
}

// Side Effect 발생하지 않음
fun addPure(a: Int, b:Int) : Int {
    return a+b
}
```

### 참조 투명성
참조 투명성이란 입력이 같다면 결과가 동일하게 나와야 한다는 것을 의미합니다. 아래의 코드에서 `createLottoNumbers` 함수는 인자인 `seed` 가 동일해도 매번 다른 값을 리턴합니다. 반면, `add` 함수는 같은 인자가 들어오면 항상 같은 값을 리턴합니다. 아래 코드는 Kotlin 으로 작성하였습니다.

```Kotlin
// 참조 투명하지 않는 함수
fun createLottoNumbers(seed: Int) : MutableList<Int> {
    val numbers : MutableList<Int> = arrayListOf()
    for (i in 1 until 6) {
        val number = (Random().nextInt(45) + seed) % 45
        numbers.add(number)
    }
    return numbers
}

// 참조 투명한 함수
fun add(a: Int, b:Int) : Int {
    return a+b
}
```

### 일급 객체
일급 객체란 인자로 함수를 받을 수 있고, 리턴으로도 보내고, 변수처럼 저장도 할 수 있는 객체입니다. 아래 예제에서 `add3AndOperate` 는 operation 에 함수를 받을 수 있도록 설정하였습니다. `addMaker` 는 함수를 리턴하도록 만들었으며, 그에 따라 `add1` 을 출력하면 함수가 리턴됩니다.<br>
즉, 일급 객체를 간단하게 설명하면 함수의 조합으로 나타낼 수 있다는 뜻으로 수학시간에 배웠던 _f(g(x))_ 와 같은 함수로 나타낼 수 있어야 한다는 것을 의미합니다.

```kotlin
// 함수를 인자로 받아서 리턴
fun add3AndOperate(a: Int, b:Int, operation: (Int, Int) -> Int) : Int {
    val x = a+3
    return operation(x,b)
}
// 사용하기
val subTemp = fun(a:Int, b:Int) = if(a>b) a-b else b-a  
println(add3AndOperate(1,2, addTemp)) // 2 출력

// 리턴값이 함수인 경우
fun addMaker(a:Int) : (Int) -> (Int) {
    return fun(b:Int) : Int {
        return a+b
    }
}
// 사용하기
val add1 = addMaker(2)
println(add1) // (kotlin.Int) -> kotlin.Int 출력
println(add1(5)) // 7 출력
println(addMaker(2)(4)) // 6 출력
```

## 객체 기준과 함수 기준
함수형 프로그래밍을 말할때 객체형와 함수형의 차이에 대해 많이 소개합니다. 그렇다면 도대체 객체 기준과 함수 기준은 도대체 무엇을 의미할까요? 하나의 예를 들어 말씀드리겠습니다. 우리가 코틀린이나 swift 에서 자주 사용하는 `filter` 는 collection 에서 사용되는 __메서드__ 입니다. 즉, 함수적 사고로 생각할 수 없습니다. 아래 예를 통해서 `filter` 를 객체형으로 표혔한 것과 함수형으로 표현한 것이 어떻게 다르게 표현되는지 알아보겠습니다. 아래 예도 코틀린으로 작성하였습니다.

```kotlin
// 확인을 위한 데이터클래스 선언
data class User(val id:Int, val name:String, val age:Int)
val users : List<User> = listOf(
        User(1, "Jo", 36),
        User(2, "Choi", 32),
        User(3, "Park", 32),
        User(4, "Kim", 27),
        User(5, "Bang", 25),
        User(7, "Jung", 31),
        User(6, "Lee", 26),
        User(8, "Shin", 24)
)

// 함수형을 위한 함수 선언 (객체형은 만들어져 있음)
fun <T> filterFunction(list: Iterable<T>, predicate: (T) -> Boolean): ArrayList<T> {
    val newList = arrayListOf<T>()
    for (element in list) if (predicate(element)) newList.add(element)
    return newList
}

// 객체형인 경우
val usersAgeLessThan30 = users.filter({ user: User ->  user.age < 30 }) // 30 살 이하인 사람들만 가져옴

// 함수형인 경우
val userAgeMoreThan30 = filterFunction(users, { user: User ->  user.age < 30 }) // 30 살 이상인 사람들만 가져옴
```

위처럼 객체형의 경우는 `X.method` 형태로 나타나며 함수형의 경우는 `function(X)` 의 형태로 나타납니다. 사실 타입을 지정해야 하는 코틀린이나 스위프트에서는 filter 나 map 같은 경우는 어차피 collection 에서만 거의 쓰입니다. 하지만 자바스크립트 같은 경우는 다형성을 엄청나게 지원하기 때문에 Array 는 아니지만 Array 같은 객체로 불리는 HTML 태그와 같은 객체들도 하나의 함수로 설정할 수 있어 매우 편리하게 사용 가능합니다. 하지만 재사용성의 면에서는 함수형으로 작성하는 것이 어느 언어에서든지 우수하다고 생각할 수 있을 것 같습니다.

## 마무리하며
함수형이란 무엇인지 생각해 볼 수 있는 글을 나름 작성해보았습니다. 생각보다 개념 자체는 간단하지만 pure 한 function 이 일급객체로 계속 묶이게 되면 조금 복잡해지는 경향도 있습니다. 또한 프로그래밍 특성상 모든 프로그래밍을 함수형으로 짤 수는 없습니다. 특히, 안드로이드나 IOS 같은 경우 UI 를 불러야 할 때와 같이 어쩔 수 없는 경우도 많이 생깁니다. 이 이야기에 대해서는 다음에 다시 다루도록 하겠습니다.
