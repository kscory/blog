---
layout: post
comments: true
title:  "String"
date:   2018-06-29
author: Cory
categories: Swift
permalink : /dev/swift/string
tags: swift ios
---
String 이란 다른 언어들과 비슷하게 Character 들의 의 배열로 생각할 수 있습니다.

### String Literals
String 을 사용하는 방법인데요. 다른 언어와 동일하게 `" "` 로 값을 선언해 주면 됩니다.

```swift
let something = "Some string literal value"
```

### Multiline String Literals
Swift 에서는 여러줄을 한번에 넣을 수 있도록 큰따옴표 세 개(`"""`) 로 감싸게 되면 여러줄의 line 을 하나의 String 값으로 넣을 수 있게 됩니다. <br>
단 __시작과 끝은 반드시 new line__ 으로 해야 한다는 점을 주의해야 합니다.

```swift
let sampleString = """
The White Rabbit put on his spectacles
"이렇게 여러줄 넣고 따옴표도 넣는당"
Start Swift example right now!!
"""
```

### Initializing an Empty String
Swift 에서 String을 초기화 하고 싶다면 두가지 방식 중 한가지를 사용하면 됩니다. 첫째, literal 방식으로 할당하는 방법, 둘째, initialize syntax 를 사용하는 방법입니다. 어느 것을 사용하셔도 무방합니다.<br>
이 때 string 이 빈 값인지 확인하고 싶다면 String API 인 `isEmpty` 을 사용하여 확인할 수 있습니다.

```Swift
var emptyString = ""
var anotherEmptyString = String()

if emptyString.isEmpty {
    print("Nothing to see here")
}
if anotherEmptyString.isEmpty {
    print("Nothing to see here")
}
// Nothing to see here
// Nothing to see here
```

### concatenating String and Characters
여러 문자열을 합치고 싶은 경우에는 `+` 연산자를 이용하여 합칠 수 있습니다. 만약 `+=` 연산자를 이용한다면, 문자열을 합치고 값에 대입할수도 있습니다.

```swift
let string1 = "hello"
let string2 = " there"

var welcome = string1 + string2
print(welcome) // hello there

var instruction = "look over"
instruction += string2 // look over there
```

### Counting Characters
`count` API 를 이용하여 문자열 내의 문자 갯수를 반환할 수 있습니다.

```swift
let unusualMenagerie = "Koala 🐨, Snail 🐌, Penguin 🐧, Dromedary 🐪"
print(unusualMenagerie.count) // 40
```

### String indices
Swift 에서 기본적으로 `index`, `startIndex`, `endIndex` 를 제공합니다. 말 그대로, `index` 의 경우 찾고자 하는 index 의 첫번째 index를, `startIndex` 의 경우 가장 첫번째 index 를 리턴하지만, `endIndex` 의 경우 마지막 인덱스의 값의 다음번째!! 를 리턴하기 때문에 바로 print 할 경우 에러가 발생할 수 있습니다. 여기서 index 의 경우 nil 값이 포함될 수 있으므로 뒤에서 이야기하겠지만, optional value 로 들어가게 됩니다.<br>
여기서 `index` Api 의 경우 첫번째 인자는 `of, before, after` 를 넣을 수 있으며 각각이 의미하는 바는 말 그대로, 그 인덱스, 이전 인덱스, 다음 인덱스를 의미합니다.  

```swift
let greeting = "Guten Tag!"

print(greeting[greeting.startIndex]) // G
print(greeting[greeting.endIndex]) // 에러 발생 (index의 범위를 넘어 섬)
print(greeting[greeting.index(before: greeting.endIndex)]) // g
print(greeting[greeting.index(after: greeting.startIndex)]) // u

let temp = greeting.index(of: "G")!
print(greeting[temp]) // G
let index = greeting.index(greeting.startIndex, offsetBy: 7)
print(greeting[index]) // a
```  

### Special Character
다른 언어와 마찬가지로 Swift 에서도 특수한 문자들을 아래와 같이 사용할 수 있는데요. 그 문자는 아래와 같습니다.
- `\0` (null character)
- `\\` (backslash)
- `\t` (horizontal tab)
- `\n` (line feed)
- `\r` (carriage return)
- `\"` (double quotation mark)
- `\'` (single quotation mark)

#### 그 외에도 여러가지 String Api 가 존재하지만, 너무 많기 때문에 그때 그때 찾는 것을 추천합니다.
