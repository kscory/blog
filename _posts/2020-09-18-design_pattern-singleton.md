---
layout: post
comments: true
title:  "[Design Pattern] Singleton 패턴"
date:   2020-09-18
author: Cory
categories: Design_Pattern
permalink : /dev/design-pattern/singleton
tags: design pattern
---

__Singleton Pattern__ 이란 오직 한 개의 클래스 인스턴스만을 갖도록 보장하고, 이에대한 전역적인 접근점을 제공하도록 하는 패턴을 의미합니다. (출처: [gof 디자인 패턴](http://www.yes24.com/Product/Goods/17525598))

가장 흔히 발견할 수 있는 디자인 패턴중 하나일 것으로 생각됩니다만, 결국 어떤 클래스에서 접근하더라도 같은 인스턴스를 사용하고 싶다는 뜻입니다.

![](https://lh3.googleusercontent.com/pw/ACtC-3dmDdy7ru9oN7z0UvONTq2XnfjTB2XG3eSM_0_4oBpfBOCM7LIfDKw2XRnLki9xujHaJ4XzmMReGG6Yy0w73fhFYxBloBRU9ocD3n0bqeRc9WipkZsV8nLjXenhyPwVEXEXDLUxR_cdLccR29uS-wVt=w1028-h316-no?authuser=0)
<div style="text-align: center;">출처: https://en.wikipedia.org/wiki/Singleton_pattern</div><br>

사실상 구성요소는 Singleton 하나만 존재하므로 단순합니다

* __Singleton__`: Insatance() 연산을 정의하여 유일한 인스턴스로 접근할 수 있도록 한다. 

싱글턴을 구현할 일이 생각보다 많아서 언어마다 제공하는 특징들이 다릅니다. 예를 들어 자바의 경우에는 __멀티쓰레드__ 기반이기 때문에 싱글턴을 사용하기 위해 instance() 연산시에 synchronized 사용하거나 LazyHolder 등을 이용해서 쓰레드들 간의 인스턴스를 동기화시키곤 합니다.

```java
// 기본적인 싱글턴 생성
public class JavaSingleton {
    private static JavaSingleton INSTANCE = null;

    private JavaSingleton() {
    }

    public static JavaSingleton getInstance() {
        if (INSTANCE == null)
            INSTANCE = new JavaSingleton();
        return INSTANCE;
    }
}
```

```java
// synchronized 사용
public class JavaSingleton {
    private static JavaSingleton INSTANCE = null;

    private JavaSingleton() {
    }

    public synchronized static JavaSingleton getInstance() {
        if (INSTANCE == null)
            INSTANCE = new JavaSingleton();
        return INSTANCE;
    }
}
```

```java
// LazyHolder 이용
class JavaSingleton {
    private JavaSingleton() {}

    public static JavaSingleton getInstance() {
        return LazyHolder.INSTANCE;
    }

    private static class LazyHolder {
        private static final JavaSingleton INSTANCE = new JavaSingleton();
    }
}
```

Kotlin 의 경우 단순히 object 라는 키워드를 사용하면 싱글턴으로 클래스를 생성해줍니다. 하지만 결국 자바로 컴파일될때는 synchronized 를 사용하지 않은 형태와 비슷하게 만들어지게 됩니다. 즉, synchronized 등을 고민해야 한다면 즉각 쓸 수는 없고 한번 더 고민해봐야 한다는 점입니다.

```kotlin
object KotlinSingleton {
}
```

컴파일 시 아래와 같은 형태로 컴파일 됩니다.

```java
public final class KotlinSingleton {
    public static final KotlinSingleton INSTANCE;

    private KotlinSingleton() {
    }

    static {
        KotlinSingleton var0 = new KotlinSingleton();
        INSTANCE = var0;
    }
}
```

그런데 javascript 같은 언어의 경우 __싱글 쓰레드__ 기반으로 돌아가고 있습니다. 그래서 따로 synchronized 등의 작업을 하지 않아도 어차피 객체는 하나의 객체로 접근이 된다는 소리입니다. 그래서 javascript 에서는 getInstance 함수만 모듈로 빼서 간단하게 싱글턴 객체를 만들 수 있습니다. 

```javascript
class JavascriptSingleton {
    
}

let INSTANCE;
export const getInstance = () => {
  if (!INSTANCE) {
      INSTANCE = new JavascriptSingleton();
  }
  return INSTANCE;
};
```

### 사용하는 이유

싱글턴 패턴을 사용하는 이유는 앞에서도 이야기 했지만 유일하게 생성되는 단일체 인스턴스를 사용하고 싶기 때문입니다. 데이터를 저장한 저장소 (Registry) 의 경우 다른 클래스에서 사용하더라도 동기화 될 필요가 있을 것 입니다. 예를 들어 재가 내 "닉네임을 Cory 로 바꿔줘" 라고 요청해서 성공했는데 B 라는 사람이 와서 제 닉네임을 보았을 때 Cory 가 아닌 다른 닉네임이 있으면 안된다는 소리입니다.

또한 싱글턴 패턴은 Api Request 에도 주로 사용되는데 누가 보낸 Request 가 클래스를 지날때마다 다른 인스턴스가 되버리면 제대로 된 로직을 수행할 수 없을 것입니다. 

### 장점과 단점

그럼 Singleton 패턴의 장단점을 알아보겠습니다.

장점
1. 유일하게 존재하는 인스턴스로의 접근을 통제합니다. (사용자가 언제, 어떻게 __인스턴스__ 에 접글할 수 있는지 제어 가능)
2. name space 를 좁힐 수 있습니다. (전역 변수를 사용하면서 이름을 망칠 수 있는 걸 막아 줄 수 있습니다.)
3. 연산 및 표현의 정제를 허용합니다. (상속을 한 후 서브클래싱을 통해 새로운 인스턴스를 만들 수 있습니다.)
4. 인스턴스의 갯수를 변경하기가 자유롭습니다. (즉, 인스턴스를 한개만 생성한다. 두개만 생성한다 등의 행동을 하기 편합니다.)

단점
1. 위에서 이야기했듯이 싱글턴으로 하나의 인스턴스를 유지할 수 있도록 유지하는 것이 어렵습니다.

### 실제 사용 예제

실제로 서버에서는 대부분의 클래스들을 싱글턴으로 관리합니다. 클래스라기 보다는 스프링에서 __bean__ 이라 불리는 인스턴스들은 주로 싱글턴 스코프로 관리하곤 합니다. 또한 제가 주로 사용하는 Nest.js 도 클래스 스코프를 어떻게 지정할 지 정할 수 있습니다. 

하지만 제가 가장 즐겨썼던 싱글턴이라 하면 js 를 사용했을 때 logger 가 생각나서 가져와봤습니다. 이 예제에서는 logger 를 단 하나의 인스턴스로 관리하면서 새로운 모듈로거가 나왔을 때 이 싱글턴 인스턴스를 사용하는 예제입니다.

```javascript
export const logger = createLogger({
    exitOnError: false,
    transports: [consoleTransport],
});

export const moduleLogger = (context = 'default') => {
    const setMessage = (message) =>
        context? `${context} - ${message}` : message;

    return {
        verbose: (message) => {
            return logger.verbose(setMessage(message));
        },
        debug: (message) => {
            return logger.debug(setMessage(message));
        },
        info: (message) => {
            return logger.info(setMessage(message));
        },
        warn: (message) => {
            return logger.warn(setMessage(message));
        },
        error: (message, trace) => {
            return logger.error(setMessage(message), {trace});
        },
    };
};
```

### 마무리

이렇게 해서 싱글턴 패턴에 대해서 알아봤습니다. 

싱글턴 패턴은 서버, aos, ios 가리지 않고 어플리케이션 개발자들이 가장 많이 사용하는 패턴이라 할 수 있을 것 같습니다. 저는 주로 Nestjs 프레임워크를 사용해서 개발하는데 당연하게 Singleton scope 를 넣고 보는 경향이 있는 것 같습니다. 아무리 당연하게 쓰더라도 한번쯤은 왜 쓰는지, 싱글턴 패턴(스코프) 가 무엇인지는 한번쯤 고민해 볼 필요가 있는 것 같습니다.
