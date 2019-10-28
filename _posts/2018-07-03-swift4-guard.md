---
layout: post
comments: true
title:  "Condition - guard, defer, available"
date:   2018-07-03
author: Cory
categories: Swift
permalink : /dev/swift/guard_defer
tags: swift ios
---
swift 에는 switch 문의 여러가지 말고도 특이한 조건문들이 존재합니다. 그 중 대표적인 것이 바로 `guard` 와 `defer` 입니다. 이 둘을 함께 설명하는 이유는 이 둘이 에러가 발생할 것 같은 상황에서 자주 쓰일 것 같아 보이기 때문입니다. 추가적으로 Swift 는 apple 에서 사용되는 언어다 보니 ios 라던지 MacOS 의 API 컴파일 버전을 체크할수 있는 `available` 이 존재합니다.

## guard
`guard` 는 조건이 true 라고 생각되어 지는 구문에 if 대신해서 let 으로 변수를 함께 선언하면서 사용합니다. 문서상에는 Early Exit 으로 소개하고 있는데 그 이유는 나와있지 않지만 아마도 if 를 사용할때와 비교해서 코드블럭이 존재하지 않기 때문으로 생각합니다. <br>
이를 사용하는 방법은 간단한데 guard let 을 통해 조건이 걸린 변수르 선언하고 조건이 틀릴 경우 실행할 `else` 구문을 만들어 주면됩니다.

```swift
let urlString = "www.naver.com"
// URL 을 만들 경우
guard let baseUrl = URL(string: urlString) else { // url이 true 라고 생각하지만 아닐수도 있으므로 guard 를 통해 조건을 걸어줍니다.
    print("URL error")
    return
}
```

위를 보면 결국 뒤에서 배울 `optional` 이란 부분을 제거하기 위함입니다. 참고로 optional 이란 nil 값을 사용할 수도 있는 부분에서 사용됩니다. 위에서는 guard 에 한가지의 조건과 변수를 선언했지만 아래처림 여러개를 한번에 선언할 수도 있습니다.

```swift
func someFunc(first: Int?, second: String?, third: Bool?) -> Bool {
  guard let firstValue = first, let secondValue = second else {
    return false
  }
}
```

만약 예외처리와 함께 사용된다면? 아래처럼 에러를 던져줄 수 있을 것 같습니다.

```swift
let urlString = "www.naver.com"

guard let baseUrl = URL(string: urlString) else { // url이 true 라고 생각하지만 아닐수도 있으므로 guard 를 통해 조건을 걸어줍니다.
  throw Error
}
```

## defer
`defer` 도 조금 특이하게 사용됩니다. 결론만 말씀드리면 defer 는 호출되었을 때 바로 defer 를 실행하지 않고 속해있는 코드블럭이 종료 될 때 호출됩니다. 아마 파일시스텡이나 DB 와 같이 close 가 들어가야만 하는 문에서 반드시 실행되어야 할 때 실행시킬 수 있을 것 같습니다.

```swift
func processFile(filename: String) throws {
  if exists(filename) {
    let file = open(filename)
    defer {
      close(file)
    }

    while let line = try file.readline() {
      /* Work with the file. */
    }

    // close(file) 이 코드가 끝나는 이 부분에서 defer 에 의해 호출된다.
  }
}
```

위와 같은 경우에 defer 는 file 에 대한 처리가 끝난 후에 처리되므로 안전하게 close 할 수 있을 것으로 생각합니다. 하지만 주의할 점은 defer 문 앞에서 오류가 발생하면 defer 또한 실행되지 않으니 defer 가 실행되어야만 한다면 오류가 날 것 같은 로직보다는 앞에서 써주어야 합니다.

```swift
let urlString? = nil

guard let baseUrl = URL(string: urlString) else {
  print("URL error")
  return
}

defer {
  print("defer") // 여기는 위에서 return 되었으므로 실행되지 않는다.
}
```

## available
available 은 간단하게 if 조건문과 함께 사용할 수 있습니다. 아마 공식문서에 나와 있는 코드를 보면 바로 이해할 수 있을 것으로 생각합니다.

```swift
if #available(iOS 10, macOS 10.12, *) {
    // Use iOS 10 APIs on iOS, and use macOS 10.12 APIs on macOS
} else {
    // Fall back to earlier iOS and macOS APIs
}
```
