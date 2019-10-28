---
layout: post
comments: true
title:  "TDD 적용하기"
date:   2018-07-14
author: Cory
categories: Web
permalink : /dev/web/tdd-basic-practice
tags: tdd javascript node.js web
---
> 인프런의 김정환 개발자님의 강의인 [견고한 소프트웨어 만들기](https://www.inflearn.com/course/tdd-견고한-소프트웨어-만들기/) 를 듣고 정리한 글입니다. 문제시 삭제하도록 하겠습니다.

## 화면에 보이지 않는 모듈 (클릭카운터 모듈)
__ClickCounter__ 는 카운터 데이터를 다루는 모듈로, 전역 공간에 있는 counter 변수를 ClickCounter 안에서 관라하도록 할 것입니다. 이를 TDD 방식으로 진행해 보겠습니다.

### 스펙1 - ClickCounter 모듈의 getValue() 는 카운터 값을 반환한다.
현재 코드 정의는 전혀 없고 단순히 모듈의 스펙만을 정의했습니다. 이 테스트 코드를 작성하면 아래와 같이 작성할 수 있습니다. 아래 코드는 테스트 코드를 작성하느 파일인 ClickCounter.spec.js_ 애 작성합니다.

```javascript
describe('App.ClickCounter', () => {
    describe('getValue()', () => {
        it('초기값이 0인 카운터 값을 반환한다', () => {
            const counter = App.ClickCounter() // 먼저 ClickCounter 모듈을 설정
            expect(counter.getValue()).toBe(0) // getValue 가 0이라는 것을 검증하는 코드
        })
    })
})
```

이 코드를 검증하면 당연히 실패하게 되고 이 단계가 바로 __Red(적색)__ 단계입니다. 이제 이를 통과할 수 있는 코드를 작성합니다. 이 코드는 ClickCounter.js 파일에 작성합니다.

```javascript
// App 이라는 NameSpace 를 생성
var App = App || {}

// App 에 ClickCounter 모듈을 생성
App.ClickCounter = () => {
    return {
        getValue() {
            return 0 // getValue 에는 0 이라는 값만 테스트 하므로 return 을 0으로 해준다.
        }
    }
}
```

 이를 적용했으면 이제 테스트를 통과할 수 있는 __Green(녹색)__ 단계로 들어섰을 것입니다. 하지만 0을 직접 반환하면 안되고 특정 값을 반환해야 할 것 같다는 생각이 드실 것 같습니다. 따라서 코드를 한번 더 수정해보았습니다.

```javascript
var App = App || {}

App.ClickCounter = () => {
    let value = 0 // value 를 선언
    return {
        getValue() {
            return value // 이를 리턴
        }
    }
}
```

이제 변수도 사용하고 리턴도 바로 0으로 안합니다. 테스트도 동과할 수 있구요. 즉, 이렇게 테스트를 통과하는 로직을 짜면서 코드를 고쳐나가는 것이 __Refactor__ 단계 입니다. 이제 다음 스펙에 대해 알아볼까요?

### 스펙2 - ClickCounter 모듈의 increase() 는 카운터 값을 1만큼 증가한다.
위와 같은 순서로 진행하겠습니다. 먼저 테스트 코드를 작성할 수 있습니다. 이는 unit 테스트의 준비, 실행, 단언 단계에 따라 작성할 수 있습니다.

```javascript
describe('App.ClickCounter', () => {
    describe('getValue()', () => {
        it('초기값이 0인 카운터 값을 반환한다', () => {
            const counter = App.ClickCounter()
            expect(counter.getValue()).toBe(0)
        })
    })

    describe('increase()', () => {
        it('카운터를 1 올린다', () => {
            // 준비 단계
            const counter = App.ClickCounter()

            // 살행 단계
            counter.increase()

            // 단언 단계
            expect(counter.getValue().toBe(1))
        })
    })
})
```

이제 테스트에 통과하도록 ClickCounter 모듈의 increase() 함수를 구현하겠습니다.

```javascript
var App = App || {}

App.ClickCounter = () => {
    let value = 0

    return {
        getValue() {
            return value
        },
        increase() {
            value ++ // 단순히 1 증가하도록 구현
        }
    }
}
```

이제 테스트를 통과할 수 있는 코드를 작성하였습니다.<br>
그런데 테스트 코드를 한번 보게 되면 모듈을 선언하는 `const counter = App.ClickCounter()` 부분이 중복되어 있습니다. 이와 같은 중복코드를 it 함수 호출 직접에 실행되는 자스민 함수인 `beforeEach` (참고> afterEach 함수는 it 뒤에 실행됩니다.) 로 옮겨보겠습니다. 이는 TDD 방법을 설명하기 때문에 함수에 대해 자세히는 말하지 않겠습니다.

```javascript
describe('App.ClickCounter', () => {
    let counter // 중복된 모듈을 선언

    beforeEach(() => {
        counter = App.ClickCounter() // 중복코드를 beforeEach 로 뺀다.
    })
    describe('getValue()', () => {
        it('초기값이 0인 카운터 값을 반환한다', () => {
            expect(counter.getValue()).toBe(0)
        })
    })

    describe('increase()', () => {
        it('카운터를 1 올린다', () => {
            counter.increase()
            expect(counter.getValue().toBe(1))
        })
    })
})
```

이는 이제 DRY 한 코드가 되었습니다. 즉, 중복 코드를 제거했습니다. 그런데 이 테스트 코드에서 만약 초기값이 0이 아니라면 increase 의 결과가 1 이 아닐수도 있습니다. 따라서 이를 수정해보겠습니다.

```javascript
describe('App.ClickCounter', () => {
    let counter

    beforeEach(() => {
        counter = App.ClickCounter()
    })
    describe('getValue()', () => {
        it('초기값이 0인 카운터 값을 반환한다', () => {
            expect(counter.getValue()).toBe(0)
        })
    })

    describe('increase()', () => {
        it('카운터를 1 올린다', () => {
            const initialValue = counter.getValue() // getValue 로 초기값을 가져오고
            counter.increase()
            expect(counter.getValue().toBe(initialValue +1)) // 그것보다 1 이 큰지를 체크
        })
    })
})
```

이러면 테스트 코드도 완성 되었고, 이를 통과하는 코드도 작성하였습니다. 이제 이를 보여주는 View 코드를 작성하겠습니다.

## 화면에 보이는 모듈 (클릭카운트뷰 모듈)
__ClickCountView__ 카운터 데이터를 돔(DOM)에 반영하는 모듈로, 데이터를 출력하고 이벤트 핸들러를 바인딩하는 역할을 담당합니다. 이 때 데이터를 조회할 ClickCounter 와 데이터를 출력할 DOM Element 를 객체를 만들어 파라미터로 전달받을 수 있습니다. 즉, TDD 방식으로 사고하다 보면 모듈을 주입받아 사용하는 경향이 생기고 이에 따라 하나의 기능 단위로 모듈을 분리할 수 있어 단일 책임 원칙을 지킬 수 있게 됩니다.

### 스펙1 - ClickCountView 모듈의 updateView() 는 카운트 값을 출력한다.
앞에서 했던 방식대로 테스트 코드를 먼저 작성합니다.

```javascript
describe('App.ClickCountView', () => {
    // 공통으로 사용될 것 같은 코드를 beforeEach 에 미리 정의
    let updateEl, clickCounter, view
    beforeEach(() => {
        clickCounter = App.ClickCounter()
        updateEl = document.createElement('span')
        view = App.ClickCountView(clickCounter, updateEl)
    })

    describe('updateView()', () => {
        it('ClickCounter의 getValue() 값을 출력한다', () => {
            const counterValue = clickCounter.getValue() // countValue 를 가져오고
            view.updateView() // view 를 업데이트 한 뒤
            expect(updateEl.innerHTML).toBe(counterValue.toString()) // 동일한지 비교 그런데 String 이므로 String 을 비교
        })
    })
})
```

이는 당연히 실패하는 Red(적색) 단계 입니다. 이제 녹색(Green) 단계로 들어가기 위해 View 를 작성하겠습니다.

```javascript
var App = App || {}

App.ClickCountView = (clickCounter, updateEl) => {
    return {
        updateView() {
            updateEl.innerHTML = clickCounter.getValue()
        }
    }
}
```

그런데 여기서 ClickCountView 에 의존성 주입이 되었다고 보장할 수 없습니다. 따라서 이를 보장할 수 있는 테스트 코드를 작성해야 합니다. 먼저 clickCounter 주입에 대한 코드를 작성하겠습니다.

```javascript
describe('App.ClickCountView', () => {
    let updateEl, clickCounter, view
    beforeEach(() => {
        clickCounter = App.ClickCounter()
        updateEl = document.createElement('span')
        view = App.ClickCountView(clickCounter, updateEl)
    })

    it('clickCounter를 주입하지 않으면 에러를 던진다', () => {
        const clickCounter = null
        const updateEl = document.createElement('span')
        const actual = () => App.ClickCountView(clickCounter, updateEl) // 에러가 발생하는 코드
        expect(actual).toThrowError() // 에러를 발생하는지 확인
    })

    describe('updateView()', () => {
        it('ClickCounter의 getValue() 값을 출력한다', () => {
            const counterValue = clickCounter.getValue()
            view.updateView()
            expect(updateEl.innerHTML).toBe(counterValue.toString())
        })
    })
})
```

그러면 이는 에러를 던지지 않기 때문에 실패하게 됩니다. 즉, 적색단계 입니다. 이제 이를 통과하도록 모듈을 수정하겠습니다. 그 뒤 updateEl 에 대한 코드도 동일한 방법으로 진행합니다.

```javascript
var App = App || {}

App.ClickCountView = (clickCounter, updateEl) => {
    if (!clickCounter) throw Error('clickCounter') // 주입되지 않았으면 에러를 던진다.

    return {
        updateView() {
            updateEl.innerHTML = clickCounter.getValue()
        }
    }
}
```

### 스펙2 - ClickCountView 모듈의 increaseAndUpdateView() 는 카운트 값을 증가하고 그 값을 출력한다.
카운트 값을 증가하고 그 값을 출력한다라는 말은 두 가지의 기능이 들어가 있다는 뜻입니다. 첫째로 _카운트 값 증가_ 둘째로 _그 값을 출력_ 이라는 두 가지 기능입니다. 즉, 테스트 코드를 둘로 분리하는 것이 좋습니다. 이런 경우 이전에 이야기한 __테스트 더블__ 이라는 단위 테스트 패턴을 이용할 수 있습니다. jasmine 에서는 테스트 더블을 이용하기 위한 `spyOn()`, `createSpy()` 함수를 제공합니다. spyOn 을 이용해서 테스트 코드를 작성해 보도록 하겠습니다. 먼저 _카운트 값 증가_ 에 관한 테스트 코드를 작성하겠습니다,

```javascript
describe('increaseAndUpdateView()는', () => {
    it('ClickCounter의 increase 를 실행한다', () => {
        spyOn(clickCounter, 'increase') // clickCounter 모듈의 increase 함수를 감시하도록 설정
        view.increaseAndUpdateView() // 함수를 실행(특정 행동을 취한다)
        expect(clickCounter.increase).toHaveBeenCalled() // 감시한 함수가 실행되었는지 체크
    })
})
```

그러면 이제 적색단계로 들어섰을 테고, 녹색단계로 진입하기 위해 increaseAndUpdateView 함수에 increase 를 실행시키는 코드를 넣어줍니다.

```javascript
var App = App || {}

App.ClickCountView = (clickCounter, updateEl) => {
    if (!clickCounter) throw new Error(App.ClickCountView.messages.noClickCounter)
    if (!updateEl) throw new Error(App.ClickCountView.messages.noUpdateEl)

    return {
        updateView() {
            updateEl.innerHTML = clickCounter.getValue()
        },
        increaseAndUpdateView() {
            clickCounter.increase() // clickCounter 모듈의 increase 를 실행시킨다.
        }
    }
}
```

그럼 이제 통과가 되었습니다. _그 값을 출력_ 하는 테스트도 동일한 방식으로 진행할 것이며 이는 생략하도록 하겠습니다.

### 스펙3 - 클릭 이벤트가 발생하면 increaseAndUpdateView() 를 실행한다.
여기서도 테스트 더블 단위 테스트 패턴을 이용할 것입니다. 그런데 이때 _클릭이벤트_ 의 경우 돔 엘리먼트가 필요합니다. 이는 앞에서 `updateEl` 엘리먼트를 주입받았듯이 클릭 이벤트를 바인딩할 DOM 엘리먼트 `triggerEl` 을 주입받도록 하겠습니다. 그런데 여기서 객체 형태로 돔을 주입받았으므로 이에 대한 테스트 코드도 변경해 주어야 합니다.

```javascript
let updateEl, clickCounter, view, triggerEl

beforeEach(() => {
    updateEl = document.createElement('span')
    triggerEl = document.createElement('button')
    clickCounter = App.ClickCounter();
    view = App.ClickCountView(clickCounter, {updateEl, triggerEl}) // 돔은 객체 형태로 주입받았습니다.
})

it('ClickCounter를 주입하지 않으면 에러를 던진다', () => {
    const actual = () => App.ClickCountView(null, {updateEl, triggerEl}) // 객체 형태로 던지도록 변경
    expect(actual).toThrowError(App.ClickCountView.messages.noClickCounter)
})

it('updateEl를 주입하지 않으면 에러를 던진다', () => {
    const actual = () => App.ClickCountView(clickCounter, {triggerEl}) // triggerEl 만 넘긴다.
    expect(actual).toThrowError(App.ClickCountView.messages.noUpdateEl)
})

// 같은 방식으로 진행
it('triggerEl를 주입하지 않으면 에러를 던진다', () => {
    const actual = () => App.ClickCountView(clickCounter, {updateEl})
    expect(actual).toThrowError(App.ClickCountView.messages.noTriggerEl)
})

// 같은 방식으로 진행합니다.
it('클릭 이벤트가 발생하면 increaseAndUpdateView() 를 실행한다.', () => {
    spyOn(view, 'increaseAndUpdateView')
    triggerEl.click()
    expect(view.increaseAndUpdateView).toHaveBeenCalled()
})
```

그런데 코드를 조금 변경 (모듈에 객체를 주입받는 형식으로 변경 등) 되었으므로 이를 리펙터링 해야 합니다. view 파일을 리펙터링 하면서 테스트 코드를 통과할 수 있도록 변경해보겠습니다.

```javascript
var App = App || {}

App.ClickCountView = (clickCounter, options) => { // 객체형태로 받으므로 options 로 변경
    if (!clickCounter) throw new Error(App.ClickCountView.messages.noClickCounter)
    if (!options.updateEl) throw new Error(App.ClickCountView.messages.noUpdateEl) // options 내의 updateEl 로 변경
    if (!options.triggerEl) throw new Error(App.ClickCountView.messages.noTriggerEl) // trigger 주입 코드 추가

    // increaseAndUpdateView 를 객체를 click 이벤트로 받기 위해 view 객체로 변경
    const view = {
        updateView() {
            options.updateEl.innerHTML = clickCounter.getValue() // options 내의 객체로 변경
        },
        increaseAndUpdateView() {
            clickCounter.increase()
            this.updateView()
        }
    }

    // click 이벤트 발생 시 view 객체의 increaseAndUpdateView 이벤트 실행
    options.triggerEl.addEventListener('click', () => {
        view.increaseAndUpdateView()
    })

    return view
}
```

## 모듈을 이용해서 화면 만들기
이제 이 모듈들을 화면에 어떻게 붙이는지를 알아보겠습니다. 이제는 javascript 가 아닌 html 파일에서 붙이겠습니다. 이는 단순하게 작성한 코드를 붙이면 됩니다.

```HTML
<html>
  <body>
    <span id="counter-display"></span>
    <button id="btn-increase">Increase</button>

    <script src="ClickCounter.js"></script>
    <script src="ClickCountView.js"></script>

    <script>
      (() => {
          const clickCounter = App.ClickCounter()
          const updateEl = document.querySelector('#counter-display')
          const triggerEl = document.querySelector('#btn-increase')
          const view = App.ClickCountView(clickCounter, {updateEl, triggerEl})
          view.updateView()
      })()
    </script>
  </body>
</html>
```

## 요구사항 변경하기 (clickCounter 모듈)
감소하는 기능이 필요할 수도 있고 또 다른 요구사항이 변경될 수 있습니다. 따라서 코드를 리펙토링 하면서 요구사항을 쉽게 반영할 수 있는 코드를 작성해야 합니다. 이런 요구사항을 만족할 수 있도록 리펙터링 하도록 하겠습니다.

### 스펙3 - ClickCounter 모듈을 데이터를 주입 받는다.
먼저 테스트 코드로 데이터를 주입받지 않는다면 에러를 던지는 코드와 데이터를 주입받도록 테스트 코드를 변경합니다.

```javascript
let counter, data

// 초기값 주입 테스트 코드 작성
it('초기값을 주입하지 않으면 에러를 던진다.', () => {
    const actual = () => App.ClickCounter()
    expect(actual).toThrowError(App.ClickCounter.messages.noInitialValue)
});

beforeEach(() => {
    // 데이터 변수 생성 및 인자로 넘기기
    data = {value: 0}
    counter = App.ClickCounter(data)
})
```

이렇게 적색단계가 완료되었으면 코드를 통과할 수 있도록 설정합니다.

```javascript
App.ClickCounter = (_data) => {
    if(!_data) throw Error(App.ClickCounter.messages.noInitialValue) // 주입받지 않으면 에러를 발생
    const data = _data // 데이터를 받는다.
    data.value = data.value || 0 // 데이터의 value 가 존재하면 받고 아니면 0으로 초기화 (객체 복사 방지)

    return {
        getValue() {
            return data.value // 주입받은 데이터를 리턴
        },
        increase() {
            data.value++ // 주입받은 데이터를 증가
        }
    }
}
```

그런데 아마 View 의 테스트 코드를 변경하지 않아 에러가 발생할 것입니다. View 테스트 코드에도 위와 같이 데이터를 주입받도록 변경해야 합니다.

```javascript
beforeEach(() => {
    // 데이터 변수 넘기기
    const data = {value: 0}
    clickCounter = App.ClickCounter(data)
    updateEl = document.createElement('span')
    triggerEl = document.createElement('button')
    view = App.ClickCountView(clickCounter, {updateEl, triggerEl})
})
```

그러면 이제 모든 테스트를 성공하면서 녹색 단계로 진입하게 됩니다.


### 스펙4 - ClickCounter 모듈의 increase 함수는 대체될 수 있다.
단순히 증가하는 기능이 아니라 감소,증가 모든 기능을 수행하 수 있는 함수로 변경하기를 원할 수도 있습니다. 따라서 이를 만족하도록 작성하겠습니다. increase() 함수를 count() 로 변경하고 작성하겠습니다. 내용은 생략하겠습니다.(동영상 강좌를 참고해주세요) 먼저 테스트 코드 입니다.

```javascript
describe('setCountFn()', () => {
    it('인자로 함수를 넘기면 count() 함수를대체한다.', () => {
        // 실행 준비 단계
        const add2 = value => value + 2 // 함수를 정의
        const expected = add2(data.value) // 예상 값 설정

        // 실행 단계
        counter.setCountFn(add2).count() // 함수를 변경
        const actual = counter.getValue() // 실제 값 설정

        // 단언 단계
        expect(actual).toBe(expected) // 실제값과 예상값이 동일한지 확인
    });
})
```

테스트에 통과하도록 작성합니다.

```javascript
App.ClickCounter = (_data) => {
    if(!_data) throw Error(App.ClickCounter.messages.noInitialValue) // 주입받지 않으면 에러를 발생

    const data = _data // 데이터를 받는다.

    data.value = data.value || 0 // 데이터의 value 가 존재하면 받고 아니면 0으로 초기화 (객체 복사 방지)

    return {
        getValue() {
            return data.value
        },
        count() {
            data.value++
        },
        setCountFn(fn) {
            this.count = function () {
                return data.value = fn(data.value)
            }
            return this
        }

    }
}
```

### html 작성하기
이제 화면에 보이도록 html 코드에 작성하면 됩니다.

```HTML
<html>
  <body>
    <button id="btn-desc">-</button>
    <span id="counter-display"></span>
    <button id="btn-inc">+</button>

    <script src="ClickCounter.js"></script>
    <script src="ClickCountView.js"></script>

    <script>
      (() => {
          const data = {value:0} // 초기값 설정
          const counterDesc = App.ClickCounter(data).setCountFn(v => v -1) // 감소 카운트 설정
          const counterInc = App.ClickCounter(data).setCountFn(v => v + 1) // 증가 카운트 설정
          // const counterInc = App.ClickCounter(data).setCountFn(v => v + 2) // 카운트에 대한 함수를 변경할수도 있다.

          const btnDesc = document.querySelector('#btn-desc') // 감소 버튼
          const btnInc = document.querySelector('#btn-inc') // 증가 버튼
          const updateEl = document.querySelector('#counter-display')

          const descCounterView = App.ClickCountView(counterDesc, {updateEl, triggerEl: btnDesc}) // 감소 view 생성
          const incCounterView = App.ClickCountView(counterInc, {updateEl, triggerEl: btnInc}) // 증가 view 생성

          descCounterView.updateView() // 초기값을 업데이트하기 위해 한개의 view 만 넘김
      })()
    </script>
  </body>
</html>
```
