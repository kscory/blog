---
layout: post
comments: true
title:  "Constant & Variable"
date:   2018-06-28
author: Cory
categories: Swift
permalink : /dev/swift/variable
tags: swift ios
---
swift 에서는 다른 언어와 마찬가지로 상수와 변수를 가지고 있습니다. 먼저 __상수(constant)__ 란 한번 선언되면 바꿀 수 없는 값을 의미하며, __변수(variable)__ 란 선언을 한 뒤에도 바꿀 수 없는 값을 의미합니다. <br>
예를 들어 `a=10` 으로 값을 선언 한 후 `a=8` 로 값을 변경하고 싶은 경우 __변수__ 로 선언을 해주어야 하며, __상수__ 로 선언을 한 경우에는 에러가 발생하게 됩니다.

### 선언
아래와 같이 상수의 경우 `let` 이란 키워드로 선언을 하며, 변수의 경우 `var` 이란 키워드로 선언 하게 됩니다. (자바스크립트를 배운 경우에는 `let` 이 변수, `const`가 상수이기 때문에 조금 헤깔리실 수도 있겠습니다.)

```swift
var str = "Hello, playground"
var version = 1.0
let year = 2015
let handsome = true
```

### Type Inference 와 Type Annotations
Swift 의 경우 __Type Inference__ 와 __Type Annotation__ 이라는 방식으로 타입을 지정하게 됩니다. <br>
__Type Inference__ 란 타입을 지정해주지 않아도 자동으로 타입을 캐스팅 해주는 기능을 의미하는데, 위에서 아무런 타입을 선언하지 않으면 Type Inference 에 의해 자동으로 타입이 지정되는 방식을 뜻합니다. <br>
반면 __Type Annotations__ 란 직접 타입을 지정해주는 방식을 뜻하며 변수명 뒤에 세미콜론(`:`) 을 붙여 타입을 지정하는 방식을 뜻합니다. (참고> Kotlin 과 동일한 방식을 취하고 있습니다.)

```swift
// Type Inference
var name = "Cory"
var age = 25
var height = 178.3
var isMilitary = true

// Type Annotations
var name: String = "Cory"
var age: Int = 25
var height: Double = 178.3
var isMilitary: Bool = true
```

### Naming
Swift 에서는 아래와 같이 특수한 문자도 변수로 취급할 수 있습니다. (그러나 잘 사용하지 않을 것 같다는 생각이 드네요 ㅎㅎㅎ)
\
```swift
let 😠😠😠 = "Happy!"
```

### Printing constant & variable
그럼 이제 이 변수들은 어떻게 출력할 수 있을까요? swift 에서도 물론 print 함수를 가지고 있기 때문에 이를 사용하면 됩니다. 이 함수는 `print(someValue, terminator: "")` 의 형태로 되어 있으며, default 로는 개행 문자인 `\n` 을 마지막에 넣어주기 때문에 한줄 띄게 됩니다.<br>
만약, 문자열에서 상수 혹은 변수함께 사용하기 위해서는 `\(변수)` 의 형태로 binding 해줄 수 있습니다.

```swift
var friendlyWelcome = "Hello"

print(welcomeMessage) // "Hello"
print("\(friendlyWelcome) , My name is Cory") // Hello, My name is Cory
```

### 주석
조금 다른 방향이 된 것 같지만... 주석을 다는 방법도 말씀드리겠습니다. 한줄 주석의 경우 `\\` 을 사용할 수 있으며, 여러줄에 주석을 설정할 경우에는 `/* */` 의 방식으로 주석을 달 수 있습니다. 여기서 __여러줄을 한번에 주석처리__ 하고 싶은 경우에는 `command + /` 의 단축키로 지정할 수 있습니다.

### 타입 체크
상수 혹은 변수에 대한 타입을 체크하기 위해서는 `type(of: 변수명)` 을 사용하여 type 을 확인할 수 있습니다.

```swift
var str = "Hello, playground"
print("str : \(type(of: str))") // str : String
```

### 타입 변환
타입 변환은 `as` 를 이용하여 변환할 수 있습니다.

```swift
let choco as Dog
```

### Tuple
Python 에서 많이 쓰였었던, tuple 이 Swift 에도 존재합니다. tuple 여러 변수를 하나의 값으로 지정할 수 잇으며, tuple 안에서는 어떤 타입도 함께 들어 갈 수 있습니다. <br>
간단하게 이야기하면, 배열과 유사하지만, 수정이 불가능한 배열이라고 생각할 수도 있을 것 같습니다. <br>
tuple 은 `()` 안에 값을 넣어 생성할 수 있습니다. 그러나 python 과 다르게 swift에서의 tuple 은변수명을 선언해준 경우 그 안의 값들은 dot(`.`) 을 통해 변수명 혹은 index로 꺼내 쓸 수 있습니다.

```swift
let http404Error = (404, "Not Found")
let (statusCode, statusMessage) = http404Error
print(statusCode, statusMessage) // 404 Not Found
print(http404Error.0, http404Error.1) // 404 Not Found

let http200Status = (statusCode: 200, description: "OK", data: "cory's blog")
print(http200Status.statusCode, http200Status.description, http200Status.data) // 200 OK cory's blog
```

나중에 이야기 하겠지만 튜플은 멀티 리턴값을 만들 수 있다는 장점이 존재합니다. 예를 들면 아래와 같이 만들 수 있을 것입니다.

```swift
func minMax(array: [Int]) -> (min: Int, max: Int) {
      var currentMin = array[0]
      var currentMax = array[0]
      for value in array[1..<array.count] {
          if value < currentMin {
              currentMin = value
          } else if value > currentMax {
              currentMax = value
          }
      }
      return (currentMin, currentMax)
  }
```
