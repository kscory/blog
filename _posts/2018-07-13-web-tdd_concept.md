---
layout: post
comments: true
title:  "TDD 이론 및 패턴"
date:   2018-07-13
author: Cory
categories: Web
permalink : /dev/web/tdd-concept
tags: tdd javascript node.js web
---
> 인프런의 김정환 개발자님의 강의인 [견고한 소프트웨어 만들기](https://www.inflearn.com/course/tdd-견고한-소프트웨어-만들기/) 를 듣고 정리한 글입니다. 문제시 삭제하도록 하겠습니다.

## TDD
TDD 는 특정 기능을 수행하는 함수를 바로 만드는 것이 아니라 테스트 코드를 먼저 짜고 이에 맞게 함수를 작성하는 것을 의미합니다. 즉, 아래와 같이 진행한다고 생각할 수 있습니다.
1. 기능을 수행할 수 있는 테스트 코드를 만든다 (Unit Test)
  - 처음의 테스트 코드는 함수가 없으니 실패할 것입니다. 이 단계를 적색(Red) 단계라고 합니다
2. 테스트를 통과할 수 있는 단순한 함수를 작성한다.
  - 그럼 이제 테스트를 통과할 것인데 이 단계를 녹색(Green) 단계라고 합니다.
3. 기존 테스트를 통과할 수 있도록 로직을 유지하면서 기능을 추상화하면서 구조를 짜아 나간다.(관심사의 분리)
  - 이렇게 테스트를 통과할 수 있도록 만들면서 코드의 관심사를 분리하는 단계를 리펙터(Refactor) 단계 라고 합니다. (블루 단계)

이를 보게 되면 TDD 란 Red -> Blue -> Green 의 주기를 반복하면서 코드의 품질을 높이는 방법론이라 생각할 수 있습니다. 이렇게 테스트를 하게 되면 험수가 하나의 기능만을 수행할 수 있도록 도와줄 수 있어 코드의 품질이 높아질 수 있습니다. 또한 요즘에 각광받는 함수형 패러다임과도 어느정도 맞닿아 있다고 볼 수 있습니다.

### Unit Test (단위 테스트)
__Unit__(단위) 란 특정 조건에서 어떻게 작동해야 할 지 정의한 것을 의미합니다. 즉, 특정 input 에 어떤 output 이 나와야 하는지 정의하는 것을 의미합니다. 따라서 대부분 함수를 테스트하게 됩니다. 정리하자면 함수를 테스트 하는 것을 단위 테스트라고 합니다.<br>
이는 input 을 준비하는 단계(arrange), 로직을 실행하는 단계(act), 결과를 검증하는 단계(assert) 로 이루어져 있습니다.

### jasmine 으로 테스트 코드 테스트 하기
아래와 같이 구성하여 테스트 할 수 있습니다. jasmine 의 경우 standalone 방식과 karma 와 함께 설치하는 방법이 있는데 대부분은 karma 와 함께 설치합니다.

- 테스트 꾸러미 (Test Suite)
- 테스트 스펙 (Test Spec)
- 기대식과 매쳐 (expect(결과 값).toBe(기대하는 값))
- 스파이 (spyOn(감시할 객체, 감시할 메서드))
  - 테스트 더블을 위한 함수로 뒤에서 다시 설명하겠습니다.

```javascript
describe('hello world', ()=> { // 테스트 스윗: 테스트 유닛들의 모음
  it('true is true', ()=> { // 테스트 유닛: 테스트 단위
    expect(true).toBe(true) // 매쳐: 검증자
  })

  it('check spyOn', () => {
    spyOn(MyApp, 'foo') // MyApp 모듈의 foo 함수를 감시
    bar() // 특정 함수를 실행 (이 부분에서 foo 함수를 실행할 것으로 예상)
    expect(MyApp.foo).toHaveBeenCalled() // 감시하고 있는 "foo" 함수가 실행되었는지 체크
  })
})
```

### 테스트할 수 없는 코드
front-end 코드 테스트는 비교적 어렵습니다. 아래와 같은 경우를 생각해 볼 수 있습니다.

```html
<button onclick="counter++; countDisplay()">증가</button>
<span id="counter-display">0</span>

<script>
  var counter = 0;

  function countDisplay() {
    document.getElementById('counter-display').innerHTML = counter;
  }
</script>
```

위 코드는 작게는 두 가지, 크게는 세 가지의 문제가 있다고 생각할 수 있습니다.
1. 관심사의 분리
```html
<button onclick="counter++; countDisplay()">증가</button>
```
위와 같은 경우 클릭 이벤트 처리기를 인라인 형태로 정의했습니다. 즉, 함수가 너무 많은 일을 하고 있습니다. 즉, __단일 책임의 원칙__ 을 위배하고 있습니다.

2. 전역 변수의 충돌 (재사용의 어려움)
```javascript
var counter = 0;
```
위와 같이 전역변수를 선언하는 경우 다른 코드와 충돌할 여지가 있습니다. 즉, pure 한 함수로 만드는 것이 가장 좋습니다. 이 부분의 경우 전역변수가 아니어도 되는데 구지 써서 전역공간을 어지럽히고 있습니다.

3. 특정 프로퍼티에 종속 (재사용의 어려움)
```javascript
function countDisplay() {
  document.getElementById('counter-display').innerHTML = counter;
}
```
위와 같은 함수는 `counter-display` 에 종속되어 있습니다. 즉, 정확하게 한군데서만 사용할 수 있는 코드가 되었습니다. 인자로 id 를 받아도 되는데 함수에서 그냥 실행하고 있는 문제가 있습니다.

### 테스트 할 수 있도록 만드는 방법
테스트 할 수 있는 코드를 짜기 위해서는 먼저 __코드를 UI 에서 완전히 분리__ 해야 합니다. HTML 에서 JS 코드를 떼어낸다면 비즈니스 로직만 따로 테스트할 수 있게 됩니다. 그리고 __자바스크립트를 별도 파일로 분리__ 하는 것입니다. 이를 수행하면 다른 곳에서도 재사용할 수 있고 테스트성도 좋아지게 됩니다.

## 모듈 패턴이란?
자바스크립트에서 문제 해결에 가장 많이 사용되는 패턴중에 하나로 _함수로 데이터를 감추고, 모듈 API를 담고 있는 객체를 반환하는 형태_ 의 패턴을 의미합니다. 모듈 패턴에는 아래와 같이 두가지의 모듈이 존재합니다.
- 임의 함수를 호출하여 생성하는 모듈
- 즉시 실행 함수(IIFE) 기반의 모듈

### 임의 모듈 패턴
먼저 임의 모듈 패턴은 아래와 같이 작성할 수 있습니다. 클로저를 활용하였으며, 특정 함수만 노출시킬 수 있습니다.
```javascript
// 이름 공간으로 활용
var App = App || {}

// 이름공간에 함수를 추가, 의존성있는 GOD 함수를 주입
App.Person = function(God) {
  var name = God.makeName()

  // API 를 노출
  return {
    getName: function() { return name },
    setName: function(newName) { name = newName }
  }
}
```

이는 아래와 같이 사용 가능합니다.
```javascript
const person = App.Person(God)
person.getName()
```

### 즉시 실행 함수 모듈 패턴 (싱글턴 인스턴스)
임의 모듈과 다른점은 객체를 생성한 즉시에 함수를 실행한다는 점입니다. 즉, 보통 싱글턴인 경우에 사용할 수 있습니다.

```javascript
// 이름 공간으로 활용
var App = App || {}

// 이름공간에 함수를 추가, 의존성있는 GOD 함수를 주입
App.Person = (function() {
  let name = ""

  // API 를 노출
  return {
    getName(God) {
      name = name || God.makeName()
      return name
    },
    setName(newName) { name = newName }
  }
})() // 함수 선언 즉시 실행 (싱글턴)
```

싱글턴의 경우 아래와 같이 생성자 없이 바로 함수에 접근할 수 있습니다.
```javascript
App.person.getName(God)
```

### 모듈 생성 원칙
위와 같이 모듈 패턴을 사용하려면 두 가지의 원칙을 반드시 지켜야 합니다. 이 원칙을 지킨다면 테스트하기 쉬운 코드를 작성할 수 있을 것입니다.

1. 단일 책임 원칙에 따라 모듈은 한 가지 역할만 해야 한다.
  - 한 가지 역할에 집중함으로써 모듈을 더욱 튼튼하게 만들 수 있습니다.
2. 모듈 자신이 사용할 객체가 있다면 의존성 주입(혹은 팩토리 주입) 형태로 제공한다.

## 테스트 더블이란?
테스트하기 곤란한 컴포넌트를 대체하여 테스트하는 것으로 특정한 동작을 흉내만 내는 것을 의미합니다. 이는 아래의 5가지를 통칭하고 있습니다.

- dummy (더미)
  - 함수 인자를 채우기 위해 사용합니다.
- sturb (스텁)
  - 더미에서 조금 발전한 개념으로 `return` 값이 있는 것을 의미합니다.
- spy (스파이)
  - 스텁과 유사하지만 내부적으로 기록을 남기는 추가기능을 수행합니다.
- fake (페이크)
  - 스텁의 리턴값이 하드코딩 되어 있는 것이라면 이는 실제 값을 반환하는 것으로 생각할 수 있습니다.
  - 하지만 실제 운영에서는 사용할 수 없습니다.
- mock (목)
  - 더미, 스텁, 스파이를 혼합한 상태를 의미합니다.
