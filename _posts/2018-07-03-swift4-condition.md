---
layout: post
comments: true
title:  "Condition"
date:   2018-07-03
author: Cory
categories: Swift
permalink : /dev/swift/condition
tags: swift ios
---
Swift 에서는 다른 언어와 특이하게 Switch 문을 매우 다양하게 사용할 수 있습니다. 오히려 if 문은 다른 언어랑 많은 차이가 없습니다. 물론 조건문 또한 앞에서 이야기한 반복문과 동일하게 `()` 를 사용하지 않는 다는 점을 주의해야 합니다.

## if
if 문은 다른 언어와 동일하게 사용됩니다. if 문에 논리식을 사용하고 이것이 true 라면 if 에 해당하는 코드 블럭을 사용합니다.

```swift
let check = 30
if check < 32 {
  print(check)
}
// 30 이 출력
```

더 많은 조건을 넣고 싶다면 `else if` 를 사용합니다. 여기서도 논리식을 이용할 수 있습니다. else if 는 계속 해서 조건을 추가하면서 사용할 수 있습니다.

```swift
temperatureInFahrenheit = 90
if temperatureInFahrenheit <= 32 {
  print("It's very cold. Consider wearing a scarf.")
} else if temperatureInFahrenheit >= 86 {
  print("It's really warm. Don't forget to wear sunscreen.")
}

// It's really warm. Don't forget to wear sunscreen. 출력
```

마지막으로 모든 조건이 성립하지 않을 때 default 로 사용하게 하고 싶은 경우 `else` 를 이용합니다. else 문에는 조건이 들어가지 않습니다.

```swift
temperatureInFahrenheit = 40
if temperatureInFahrenheit <= 32 {
  print("It's very cold. Consider wearing a scarf.")
} else if temperatureInFahrenheit >= 86 {
  print("It's really warm. Don't forget to wear sunscreen.")
} else {
  print("It's not that cold. Wear a t-shirt.")
}
// It's not that cold. Wear a t-shirt. 출력
```

## switch
앞에서 이야기한 대로 swift 는 switch 문을 다양하게 사용할 수 있습니다. 예를 들어 tuple 과 함께 이용한다거나, 여러 조건을 하나의 case 에 넣는다던가 하는 작업을 진행할수 있습니다. 다만 주의해야 할 점은 __반드시 default__ 문을 적어줘야 한다는 점과, case 문 마지막에는 자동으로 `break` 가 있다는 점입니다. 여기서 C 나 Java 가 break 로 case searching 을 중지시키는 것과 다르게 오히려 `fallthrough` 를 걸어주면 다음 case 로 자동으로 넘겨주는 기능을 수행할 수 있습니다.

### 기본 작성방법
기본적인 switch-case 작성방법은 다른 언어와 동일하게 switch 로 조건을 맞출 변수를 넣고 case 에 변수에 따른 조건을 넣고 그 조건에 맞다면 실행시킨다는 것입니다. default 는 모든 조건이 성립하지 않을 경우 실행할 수 있습니다. 여기서 만약 case 문을 조건만 두고 코드를 작성하지 않을 경우애는 컴파일 에러가 발생하므로 주의해야 합니다.

```swift
let someCharacter: Character = "z"
switch someCharacter {
case "a":
    print("The first letter of the alphabet")
case "z":
    print("The last letter of the alphabet")
default:
    print("Some other character")
}
// Prints "The last letter of the alphabet

switch someCharacter {
case "a": // 비어있는 경우 에러 발생
case "A":
    print("The last letter of the alphabet")
default:
    print("Some other character")
}
```

### Closed range Operator / Half-Open Operator
Case 문에 Range Operator 를 함께 사용하할 수 있습니다. 만약 `1..<5` 라면 특정 변수가 1~4 인경우 이 조건문을 실행하게 됩니다. 아래 예제를 보면 이해할 수 있을 것입니다.

```swift
let approximateCount = 62
let countedThings = "moons orbiting Saturn"
let naturalCount: String
switch approximateCount {
case 0:
    naturalCount = "no"
case 1..<5:
    naturalCount = "a few"
case 5..<12:
    naturalCount = "several"
case 12...100:
    naturalCount = "dozens of" // 여기 조건에 성립한다.
default:
    naturalCount = "many"
}
print("There are \(naturalCount) \(countedThings).") // There are dozens of ~~ 출력
```

### tuple 사용하기
tuple 을 조건에 대핮 변수로 사용할 수 있습니다. 예를 들어 case (0, 0) 인 경우 switch 의 변수가 (0, 0) 인 조건만 허용하겠다는 뜻이고, (\_, 0) 으로 underscore 와 함께 사용할 경우 \_ 는 상관하지 않고 두번째 인자가 0 인지만 확인하겠다는 뜻입니다. 즉, \_ 의 의미는 __어떤 값이 들어가도 상관 없다__ 는 뜻입니다. 또한 각 인자에 range operator 를 함께 이용하여 인자마다 특정 범위로 조건을 줄 수 있습니다. 여기서 중요한 것은 tuple 에서 조건을 만족하려면 __각 인자에 대한 조건을 주어야 한다__ 는 점과 __모든 인자에 대한 조건이 만족해야 한다__ 는 점입니다.

```swift
let somePoint = (1, 1)
switch somePoint {
case (0, 0):
    print("\(somePoint) is at the origin")
case (_, 0):
    print("\(somePoint) is on the x-axis")
case (0, _):
    print("\(somePoint) is on the y-axis")
case (-2...2, -2...2):
    print("\(somePoint) is inside the box") // (1,1) 은 조건의 범위에 포함되므로 여기가 출력
default:
    print("\(somePoint) is outside of the box")
}
```

### value binding
조건문에 변수를 만들어 조건에 대한 value 를 가져올 수 있습니다. 물론 단일 인자의 경우에도 가능하지만 대부분은 tuple 에서 사용할 것으로 생각합니다. tuple 의 경우 한 인자의 조건을 만족하면 다른 인자값을 가져올 수 있는 장점이 있습니다.

```swift
let age2 = 22
switch age2 {
case 0..<3:
    print("Baby")
case 3...19:
    print("Child")
case let x:
    print("Adult : \(x)") // x 에 switch 의 값인 22 를 가져올 수 있습니다.

// tuple 의 경우
let anotherPoint = (2, 0)
switch anotherPoint {
case (let x, 0):
    print("on the x-axis with an x value of \(x)")
case (0, let y):
    print("on the y-axis with a y value of \(y)")
case let (x, y):
    print("somewhere else at (\(x), \(y))")
}
// Prints "on the x-axis with an x value of 2
```

### where 절과 함께 사용
value binding 을 이용하여 변수를 가져오고 이 변수를 이용하여 또다른 조건을 줄 수 있습니다. 즉, where 절은 __추가적인 조건을 걸기 위해__ 사용합니다.

```swift
let yetAnotherPoint = (1, -1)
switch yetAnotherPoint {
case let (x, y) where x == y:
    print("(\(x), \(y)) is on the line x == y")
case let (x, y) where x == -y:
    print("(\(x), \(y)) is on the line x == -y")
case let (x, y):
    print("(\(x), \(y)) is just some arbitrary point")
}
// Prints "(1, -1) is on the line x == -y
```

### Compound Cases
만약, where 와 같이 변수가 아니라 일반적인 조건을 하나의 case 에 여러개 사용할 수 있습니다. case 문에 단순히 `,` 를 이용하여 조건을 계속 주면 됩니다. 만약 value binding 을 함께 이용한다면 모든 조건이 만족할 경우 변수를 가져오는 방법으로도 사용할 수 있게 됩니다. (사실은 일반적인 방법이 더 많이 사용될 거 같습니다.)

```swift
// 모음, 자음 구분하기
let someCharacter: Character = "e"
switch someCharacter {
case "a", "e", "i", "o", "u":
    print("\(someCharacter) is a vowel")
case "b", "c", "d", "f", "g", "h", "j", "k", "l", "m",
     "n", "p", "q", "r", "s", "t", "v", "w", "x", "y", "z":
    print("\(someCharacter) is a consonant")
default:
    print("\(someCharacter) is not a vowel or a consonant")
}

// tuple 과 value binding 이용
let stillAnotherPoint = (9, 0)
switch stillAnotherPoint {
case (let distance, 0), (0, let distance):
    print("On an axis, \(distance) from the origin")
default:
    print("Not on an axis")
}
// "On an axis, 9 from the origin 출력
```
