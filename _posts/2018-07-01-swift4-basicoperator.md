---
layout: post
comments: true
title:  "Basic Operator"
date:   2018-07-01
author: Cory
categories: Swift
permalink : /dev/swift/basicoperator
tags: swift ios
---
Swift 에서는 다른 언어와 마찬가지로 기본 연산자들이 존재합니다. 하지만 특이한 연산자들이 존재하는데 Range 와 관련된 연산자들이 존재합니다.

### 기본 연산자
Swift 에서도 `+`, `-`, `/`, `%`, `*`, `=`, `==`, `+=` 등 다른 언어에서 쓰이는 여러 연산자와 `?` 를 이용한 삼항 연산자를 사용할 수 있습니다.

### Closed Range Operator
Closed Range Operator 란 시작과 끝 값이 포함된 Range 를 리턴하는 Operator 이며, `...` 의 syntax 를 사용해서 나타낼 수 있습니다. 그런데 `a...b` 와 같이 나타낼 경우 a<b 의 값을 항상 유지해야 함에 주의해야 합니다.<br>
이 Closed Range Operator 는 뒤에서 배울 for-loop 와 함께 사용하면 매우 유용하게 사용할 수 있습니다.

```swift
for index in 1...5 {
  print("\(index) times 5 is \(index * 5)")
} // 1 부터 5 까지 반복
```

### Half-Open Range Operator
Half-Open Range Operator 란 시작과 끝 값 중 마지막 값을 제외한 Range 를 리턴하는 Operator 이며 `..<` 와 같은 syntax 를 이용하여 나타냅니다. 즉, `a..<b` 와 같이 나타나게 되며, __a ~ b-1__ 까지를 포함하는 값을 가지게 됩니다. 만약 배열에서 count와 함께 사용하면 강력해질 수 있을 것 같습니다.

```swift
let names = ["Anna", "Alex", "Brian", "Jack"]
let count = names.count
for i in 0..<count {
    print("Person \(i + 1) is called \(names[i])")
}
```

### One-Sided Range
One-Sided Range 란 특정 배열 내에서 a 인덱스부터 끝까지 혹은 처음부터 a 인덱스 까지의 Range 만 리턴하는 Operator 이며 `[a...]` 혹은 `[...a]` 와 같이 나타내어 어느 방향으로 나타낼 지 정할 수 있습니다.

```swift
let names = ["Anna", "Alex", "Brian", "Jack"]
for name in names[2...] {
    print(name)
}
// Brian
// Jack

for name in names[...2] {
    print(name)
}
// Anna
// Alex
// Brian
```
