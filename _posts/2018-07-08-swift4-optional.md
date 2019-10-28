---
layout: post
comments: true
title:  "Optional"
date:   2018-07-08
author: Cory
categories: Swift
permalink : /dev/swift/optional
tags: swift ios
---
swift 는 nil safety 한 언어입니다. 따라서 nil 이 들어갈 수 있을 것 같은 값에는 optional 을 사용하게 됩니다. 그래서 null 체크를 다른 언어보다 편리하게 할 수 있다는 장점이 존재합니다. 저는 해보지 못했지만.. 이를 이용하면 nullPointException 발생률을 거의 0% 까지도 줄일 수 있다고 합니다.

## optional 사용하기
optional 은 두 가지 상황에서 사용됩니다. 간단하게 말하자면 _값이 없을 지도 모르는 경우_ 와 _값이 없는 경우_ 입니다. 후자는 몰라도 전자와 같은 경우는 자주 발생합니다. 예를 들어 고객에게 나이를 입력하도록 설정했는데 _26_ 으로 입력하는 사람과 _스물 여섯_ 이라고 입력하는 사람이 있을 수 있습니다. 물론, 숫자만 입력하게끔 설정하겠지만, 이 때 _String_ 으로 들어 온 값을 _Int_ 형으로 생각하고 변환시켜라 한다면? 에러가 발생할 수 있습니다. 이런 경우 optional 을 사용해서 값을 사용할 수 있는지 체크해야 할 것입니다. (참고> Int 변환의 경우 반드시 optional value 여야 합니다.) <br>
optional 을 사용하기 위해서는 변수 뒤에 `?` 를 선언해 주면 됩니다. 이렇게 optional value 를 사용하면 초기화 시 `nil` 값을 자동으로 넣어주므로 따로 값을 선언해주지 않아도 됩니다.

```swift
let possibleAge = "26"
let convertAge = Int(possibleAge) // 에러! 여기서 possibleAge 이 String 인지 아닌지 모르기 때문 따라서 optional int 를 넣어주어야 한다.

// nil 을 넣으려면 ? 가 필요
var serverResponseCode: Int? = 404
serverResponseCode = nil

// 따로 초기화를 하지 않아도 nil 로 초기화 해준다.
var surveyAnswer: String?
```

만약 nil 이 아님을 알고 그냥 사용하고 싶다면 `!` 를 사용하면 됩니다. 이는 forced unwrapping 이라 불리는데 말 그대로 강제로 optional value 를 벗겨낸다는 의미입니다. 원래대로라면 이 뒤 파트에서 이야기 할 optional binding 이라던가 앞에서 이야기했던 `guard` 등의 문법을 이용할 수 있습니다.

```swift
let optionalValue: Int? = 5
print(optionalValue!) // 강제로 벗겨내서 사용한다.
```

## optional binding
optional 에서 제공하는 특이한 것이 바로 optional binding 입니다. 이는 다른 언어에서 자주 사용하고 있는 __if~else 로 nil(null)을 체크하는 구문과 nil 이 아니라면 값을 대입하는__ 구문을 합친 구문입니다. 또한 unwrapping 도 자동으로 해줍니다. optional binding 은 `if let constantName = someOptional { statements }` 와 같이 구현할 수 있습니다. 이는 constantName 에 nil 이 들어가지 않는 경우 constantName 에 someOptional 를 대입하고, statements 실행합니다. 아니면 실행하지 않으며 `else` 를 통해 nil 일 경우 실행시킬 수 있습니다.

```swift
let possibleAge = "26"
if let convertAge = Int(possibleAge) {
    print(possibleNumber)
} else {
    print("could not be converted to an integer")
}
// 26 출력
```

nil 체크를 여러번 해야 되는 경우라면 아래 처럼 optional binding 을 한번에 여러개 쓸 수 있습니다. 그 뿐만 아니라 if 문처럼 조건문을 더 걸수도 있습니다.

```swift
if let firstNumber = Int("4"), let secondNumber = Int("42"), firstNumber < secondNumber && secondNumber < 100 {
    print("\(firstNumber) < \(secondNumber) < 100")
}
// Prints "4 < 42 < 100"
```

위를 풀어서 보면 아래와 같습니다.

```swift
// 위를 풀어서 쓰면 아래와 같이 사용할 수 있다.
if let firstNumber = Int("4") {
    if let secondNumber = Int("42") {
        if firstNumber < secondNumber && secondNumber < 100 {
            print("\(firstNumber) < \(secondNumber) < 100")
        }
    }
}
// Prints "4 < 42 < 100
```

만약 단순히 nil 체크 후 대입을 하고 싶다면 `??` 문법을 이용할 수 있습니다. 이는 nil 이라면 전자를 대입하고 아니라면 후자를 대입한다는 의미입니다.

```swift
var optionalValue1: Int? = nil
let wrappedValue1 = optionalValue1 ?? 0
print(wrappedValue1) // 0

let optionalValue2: Int? = 5
let wrappedValue2 = optionalValue2 ?? 0
print(wrappedValue2) // 5
```

## optional chaining
nil 체크를 하고 메서드를 실행하고 싶다면 어떻게 해야 될까요? 이 경우 optional chaining 을 이용하면 간단하게 이용할 수 있습니다. 원래대로라면 nil 체크 후 메서드 실행을 해야하지만 단순하게 `?.` 을 이용할 수 있습니다.

```Swift
// 원래 코드
let array: [String]? = []
if let array = array { // array 가 nil 이 아니라면
  array.isEmptyArray
}

// optional chaining
array?.isEmptyArray
```

위 코드에서 array 가 nil 이라면 메서드를 실행하지 않고, nil 이 아니라면 isEmptyArray 를 실행합니다. <br>
