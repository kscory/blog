---
layout: post
comments: true
title:  "Collections - Array & Set & Dictionary"
date:   2018-06-30
author: Cory
categories: Swift
permalink : /dev/swift/collections
tags: swift ios
---
Collection 이란 하나 이상의 데이터를 보관할 수 있는 특수한 자료구조를 의미합니다. Swift 에서의 Collection 은 특징에 따라 두가지로 나눌 수 있는데 <br>
첫째, 자료구조를 담는 방식에 따라 __Array__ (순서가 있는 Collections), __Set__ (순서가 없고 값이 중복되지 않음), __Dictionary__(key-value 형식의 자료구조) 로 나눌 수 있으며<br>
자료구조의 가변성에 따라 __Mutable Collection__, __Immutable Collection__ 으로 나눌 수 있습니다. 일반 변수와 상수를 선언할 때와 마찬가지로 `var` 로 선언하면 Mutable 한 Collection 이, `let` 으로 선언하면 Immutable 한 Collection 이 생성됩니다. 주로 Mutable Collection 이 쓰이지만, Immutable Collection 이 조금 더 성능이 좋기 때문에 변경할 필요가 없는 Collection 의 경우 `let` 으로 선언하는 것을 추천합니다.

## Array(배열)
Array 란 같은 타입의 순서가 있는 list 를 저장하는 자료구조를 의미합니다. Array 내에서는 중복 값을 넣을 수 있다는 특징을 가지고 있습니다..(ex> [2,2,2,3,4])

### 타입 지정 및 초기화
Array 에서 타입 을 지정하는 방식은 두가지가 존재합니다. <br>
먼저 `Array<Element>` 와 같이 Array 를 선언하고 Type을 지정해주는 full syntax 방식이 있으며, <br>
`[Element]` 와 같이 간단하게 Type을 선언해주는 shorthand syntax 방식이 있습니다.<br>
참고로 아래와 같이 타입만 따로 지정한 경우 __emptyArray__ 로 초기화됩니다.

```swift
var someIntsFull = Array<Int>() // full syntax
var someInts = [Int]() // shorthand syntax
```

### 선언
Array 를 선언하는 방식은 상수 혹은 변수와 마찬가지로 타입을 자동으로 추정해주는 __Type Inference__ 방식과 타입을 지정해주는 __Type Annotation__ 방식을 사용하게 됩니다. 여기서 Type Annotation 을 이용할 경우 _shorthand syntax_ 방식을 사용하면 됩니다.
```swift
var shoppingList = ["Eggs", "Milk", "Bacon"]
var shoppingList : [String] = ["Eggs", "Milk", "Bacon"]
```

### 접근
Swift 에서의 Array 도 다른 언어와 동일하게 특정 index 를 주어 참조할 수 있습니다. 여기서 뒷장에서 설명할 Range Operator(`...`, `..<` 과 같은 형식) 방식으로 배열에 접근할 수 있습니다.<br>
또한 배열에 접근한 뒤 만약 Mutable 한 Collection 으로 선언하였다면 단순히 값을 대입하여 특정 인덱스의 인자를 변경할 수 있습니다.
```swift
// 접근
shoppingList[0] // 0번째 index 인 "Egg" 리턴 (참고>print 를 할 경우 찍어서 확인 가능)
shoppingList[0...2] // 0번째에서 2번째 인덱스의 배열을 리턴

// 배열 내용 변경
shoppingList[1] = "Juice" // 첫번째 인자 "Milk" 를 "Juice" 로 변경
shoppingList[0...2] = ["Bread", "Jam", "Coffee"] // 0~2 번째 배열의 값을 정해진 값으로 변경
```

### 배열 메서드
배열에도 많은 메서드가 존재하지만 많이 쓰이는 몇가지를 알아보도록 하겠습니다. <br>
- `count` : 배열의 갯수를 리턴
- `isEmpty` : 배열이 비어있는지 확인
- `append(_:)` : 배열의 맨 마지막에 값을 추가 ( `+=` operator 를 활용하는 것과 동일합니다.)
- `insert(_:at:)` : "at" 번째 index 에 특정 값을 추가
- `remove(at:)` : "at" 번째 index 값을 제거
- `removeLast()` : 배열의 마지막 인자를 제거

```swift
var shoppingList : [String] = ["Eggs", "Milk"]

// 배열의 카운트 및 empty 확인
print(shoppingList.count) // 2
print(shoppingList.isEmpty) // false

// 배열의 마지막에 값 추가
shoppingList.append("Bread")
shoppingList += ["Cheese", "Butter"]
print(shoppingList) // shoppingList = ["Eggs", "Milk", "Bread", "Cheese", "Butter"]

// 배열의 특정 인덱스에 값 추가
shoppingList.insert("Bacon", 1) // 1번째 인덱스에 "Bacon" 추가
print(shoppingList) // shoppingList = ["Eggs", "Bacon", "Milk", "Bread", "Cheese", "Butter"]

// 배열에서 특정 값 제거
shoppingList.remove(1) // "Bacon 제거"
shoppingList.removeLast() // "Butter" 제거
print(shoppingList) // shoppingList = ["Eggs", "Milk", "Bread", "Cheese"]
```

## Set
Set 이란 중복되지 않는 값들의 순서가 없는 list 라고 생각할 수 있습니다.

### 타입 지정 및 선언
Set 도 동일하게 두가지 방식으로 타입 지정이 가능하며, Set 을 초기화 하고 싶은 경우 `Set<Element>` 방식으로 초기화 할 수 있습니다. 단 주의할 점은 "AnyObject" 타입은 가질 수 없다는 점입니다.
```swift
// 타입 선언
var favoriteGenres: Set<String> = ["Rock", "Classical", "Hip hop"]
var favoriteGenres: Set = ["Rock", "Classical", "Hip hop"]

// 초기화
var students = Set<String>()
```

### Set 메서드
Set 에는 unique 하면서 순서가 없는 집합이기 때문에 값을 넣거나 제거할 때 index 가 아닌 값으로 직접 참조한다는 특징이 있습니다.
- `count` : Set 의 갯수를 리턴
- `isEmpty` : Set 이 비어있는지 확인
- `insert(_:)` : 특정 값을 추가
- `contains(_:)` : 특정값이 있는지 확인
- `sorted()` : 순서대로 나열

또한 집합이라는 특성에 맞게 `union`, `intersection` 등의 메서드가 존재합니다.
- `intersection(Set)` : 교집합
- `union(Set)` : 합집합
- `subtracting(Set)` : 집합 차이(- 한 것)
- `symmetricDifference(Set)` : 합집합 - 교집합

```swift
let oddDigits: Set = [1, 3, 5, 7, 9]
let evenDigits: Set = [0, 2, 4, 6, 8]
let singleDigitPrimeNumbers: Set = [2, 3, 5, 7]

oddDigits.union(evenDigits).sorted() // [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
oddDigits.intersection(evenDigits).sorted() // []
oddDigits.subtracting(singleDigitPrimeNumbers).sorted() // [1, 9]
oddDigits.symmetricDifference(singleDigitPrimeNumbers).sorted() // [1, 2, 9]
```

마지막으로 집합간의 관계를 표시하는 메서드도 존재합니다.
- `SetA.isSubset(of: SetB)` : SetA 가 SetB 의 subset(부분집합) 이면 true 를 리턴
- `SetA.isSuperset(of: SetB)` : SetA 가 SetB 의 superset 이면 true 를 리턴
- `SetA.isStrictSubset(of: SetB)` : 부분집합이지만 동일한것을 허용하지 않는것
- `SetA.isStrictSuperset(of: SetB)` : 비슷하게 동일한 것을 허용하지 않는 isSuperset
- `SetA.isDisjoint(with: SetB)` : 서로소 집합인지 확인

```swift
let houseAnimals: Set = ["🐶", "🐱"]
let farmAnimals: Set = ["🐮", "🐔", "🐑", "🐶", "🐱"]
let cityAnimals: Set = ["🐦", "🐭"]

houseAnimals.isSubset(of: farmAnimals) // true
farmAnimals.isSuperset(of: houseAnimals) // true
farmAnimals.isDisjoint(with: cityAnimals) // true
```

## Dictionary
dictionary 란 key:value 의 형태를 가지고 있으면서, 같은 타입을 보관하는 자료구조를 의미합니다. dictionary 또한 Set 과 마찬가지로 순서를 가지고 있지 않는 것에 주의해야 합니다.

### 타입 지정 및 초기화
Dictionary 는 Array 와 마찬가지로 두가지의 타입지정 방식이 존재합니다. <br>
먼저 `Dictionary<Key, Value>` 와 같이 Dictionary 를 선언하고 Key-Value 의 타입을 지정해주는 full syntax 방식이 있으며, <br>
`[Key: Value]` 와 같이 간단하게 타입을 선언해주는 shorthand syntax 방식이 있습니다.<br>
참고로 full syntax 의 경우 `,` 로, shorthand syntax 에서는 `:` 으로 key-value 를 구분하며, 이렇게 선언한 경우 __EmptyDictionary__ 로 초기화하게 됩니다.

```swift
var emptyDictionary = Dictionary<String, Int>()
var emptyDictionary = [String: Int]()
```

### 선언
Dictionary 또한 __Type Inference__ 방식과 타입을 지정해주는 __Type Annotation__ 방식을 이용해서 선언할 수 있습니다.

```swift
var animalLegs = ["ant":6, "snake":0, "cheetah":4]
var animalLegs: [String, Int] = ["ant":6, "snake":0, "cheetah":4]
```

### 접근 및 추가, 변경, 제거
`dictionary[key]` 로 dictionary 에 있는 Value 를 가져올 수 있습니다. 다만, dictionary의 경우 뒤에서 다룰 optional value(nil 이 포함될 수 있는 value) 를 리턴한다는 것에 주의해야 할 것 같습니다.(만약 원하지 않을 경우 `!` 를 써서 nil 이 아님을 강제하면 됩니다..)

```swift
print(animalLegs["ant"]) // 6
```

값을 변경하고 싶은 경우 간단하게 접근한 값에 인자를 대입해주면 됩니다. 물론 여기서 `updateValue(_: forKey:)` 메서드를 이용하여 변경할 수도 있습니다.

```swift
animalLegs["snake"] = 4
animalLegs.updateValue(6, forKey: "snake")
```

여기서 만약 key 에 해당하는 value 값이 존재하지 않는 경우에는 dictionary 에 값을 추가하게 됩니다.

```swift
animalLegs["human"] = 2 // "human" key 가 없기 때문에 human:2 를 추가하게 된다.
```

마지막으로 dictionary 의 값을 제거하고 싶은 경우 `removeValue(forKey:)` 메서드를 이용해서 제거할 수 있으며, 제거된 값을 리턴해주므로 이를 받고 싶다면 받을 수 있습니다.

```swift
let removeValue = animalLegs.removeValue(forKey: "snake")
print(removeValue!) // snake (!의 경우 nil 이 아니라고 강제한 것)
```
