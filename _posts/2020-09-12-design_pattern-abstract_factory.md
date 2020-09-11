---
layout: post
comments: true
title:  "[Design Pattern] Abstract Factory 패턴"
date:   2019-09-12
author: Cory
categories: Design_Pattern
permalink : /dev/design-pattern/abstract_factory
tags: design pattern
---

__Abstract Factory Pattern__ 이란 상세화된 서브클래스를 정의하지 않고 서로 관련성이 있거나 독립적이 여러 객체의 군을 생성하기 위한 인터페이스를 제공하는 패턴을 의미합니다. (출처: [gof 디자인 패턴](http://www.yes24.com/Product/Goods/17525598)) 

단순하게 이야기하면 클라이언트 입장에서 실제 구현 클래스를 알 필요 없이 인터페이스만으로 시스템을 조작할 수 있도록 한다는 뜻입니다.

구성 요소에는 다음과 같은 요소가 있습니다.

![](https://lh3.googleusercontent.com/pw/ACtC-3c_YTc3pL2C5rLmZQWddQmP5wg4mbNMZKhKlF_nC6Wi7cIilXxjGQt2fRdVdVvK5vFG00Q_R4Q-utK2WCLlGt_xDIPW5m_YC44loF-TtTZ44Ie7pgsJ4WScAzNJ3GSv3z6trnq6HHC7vwdPfdyU1Y4m=w1680-h1114-no?authuser=0)
<div style="text-align: center;">출처: https://en.wikipedia.org/wiki/Abstract_factory_pattern</div><br>

출처: [위키피디아](https://en.wikipedia.org/wiki/Abstract_factory_pattern)

* `AbstractFactory`: 팩토리 인터페이스로, 만들고자 하는 객체를 생성하는 연산을 정의한다.
* `ConcreteFactory`: AbstractFactory 인터페이스를 구현하는 클래스로 구체적인 요소가 들어간다.
* `AbstractProduct`: AbstractFactory 에서 만들려고 했던 그 객체에 대한 인터페이스를 정의한다.
* `ConcreteProduct`: 팩토리에서 생성할 실제 객체를 정의하고 AbstractProduct 의 인터페이스를 정의한다.
* `Client`: 생성된 객체를 인터페이스만을 이용해 사용한다. (AbstractFactory 와 AbstractProduct 만을 사용한다.)

### 사용하는 이유

추상 팩토리 패턴은 실제 객체가 무엇인지 알지 못해도 객체를 생성하고 조작할 수 있도록 하여 객체가 생성되거나 구성, 표현되는 방식과 무관하게 시스템을 독립적으로 만들 수 있게 도와줄 수 있어 사용하게 됩니다. 

이 추상 팩토리 패턴은 자바의 Collection 에 적용되어 있다 하니 간단하게 한번 알아보도록 하겠습니다. (원래 클래스는 복잡하게 되어 있지만 저는 예시에 맞춰서 축소시키도록 하겠습니다.)

먼저 AbstractFactory 인 `List` 인터페이스 입니다.

```java
public interface List<E> extends Collection<E> {
    ListIterator<E> listIterator();
    int size();
    boolean isEmpty();
}
```

보면 알겠지만 __Collection__ (AbstractFactory) 의 listIterator() 함수는 __ListIterator__ (AbstractProduct) 를 생성할 수 있도록 정의하였습니다. 그리고 이를 상속받은 ConcreteFactory 인 `ArrayList` 클래스는 아래와 같이 인터페이스를 구현하고 있습니다.

```java
public class ArrayList<E> implements List<E> {

    private int size;

    // ...

    public ListIterator<E> listIterator() {
      return new ArrayListItr(0);
    }

    public int size() {
      return this.size;
    }

    public boolean isEmpty() {
      return size == 0;
    };
}
```

여기서 주목해야 할 점은 listIterator() 를 구현하면서 `ArrayListItr(0)` (ConcreteProduct)를 통해 ListIterator (AbstractProduct) 를 생성하고 있다는 점입니다. 다음으로는 AbstractProduct 인 `ListIterator` 인터페이스를 살펴보겠습니다. 

```java
public interface ListIterator<E> extends Iterator<E> {
  boolean hasNext();
  E next();
}
```

그리고 이를 구현한 ConcreteProduct 인 `ArrayListItr` 클래스 입니다.

```java
class ArrayListItr implements ListIterator<E> {

  int cursor; 
  int lastRet = -1
  Object[] elementData;

  public boolean hasNext() {
    return cursor != size;
  }

  public E next() {
    int i = cursor;
    cursor = i + 1;
    return (E) elementData[lastRet = i];
  }
}
```

저희는 이렇게 정의되어 있는 클래스와 인터페이스를 가지고 저희는 아래와 같이 사용하기 위해서 util 성 클래스인 Printer 함수를 만들 수 있을 것입니다.

```java
class Printer {

  public void print(List<String> list) {
    Iterator<String> i = list.iterator();
    while (i.hasNext()) {
      String content = i.next();
      System.out.println(content);
    }
  }

}
```

그런데 여기서 잘 보시면 Client 에서는 __ArrayList__ 와 __ListItr__ 클래스를 직접 사용하지 않은 것을 볼 수 있습니다. 즉, 만약 내가 `LinkedList` 를 사용하고 싶다면 이에 대한 코드를 정의하고 __Printer__ 클래스의 __print()__ 메서드에 LinkedList 를 넘겨주면 됩니다. 단, LinkedList 에서는 __iterator()__ 를 새로이 구현해야 합니다.

```java
public class LinkedList<E> implements List<E> {

    private int size;

    // ...

    public ListIterator<E> listIterator() {
      return new LinkedListItr(0);
    }

    public int size() {
      return this.size;
    }

    public boolean isEmpty() {
      return size == 0;
    };
}

class LinkedListItr implements ListIterator<E> {

  private Node<E> lastReturned = null;
  private Node<E> next;
  private int nextIndex;

  public boolean hasNext() {
    return nextIndex < size;
  }

  public E next() {
    lastReturned = next;
    next = next.next;
    nextIndex++;
    return lastReturned.item;
  }
}
```

즉, 클라이언트에서는 ArrayList 이던, LinkedList 이던지 상관없이 __List 라는 인터페이스만__ 을 알고 있고 이 안에는 __ListIterator 인터페이스를 생성하는 iterator()__ 함수가 구현되어 있어 이를 사용한 인터페이스만을 사용하게 됩니다.

### 장점과 단점

그럼 Abstract Factory 패턴의 장단점을 알아보겠습니다.

장점
1. 구체적인 클래스를 분리시킴으로써 재사용성을 증진시킬 수 있습니다.
2. List 와 Iterator 는 강하게 연결되어 있다는 점을 고려해보면 제품의 일관성을 증진시킬 수 있습니다. (List 를 쓸때 Iterator 는 언제나 사용 가능)

단점
1. 확장성이 떨어집니다. 예를 들어 List 에서 Iterator 다른 인터페이스를 사용하고 싶다면 이를 상속한 클래스인 ArrayList, LinkedList 모두 구현해 주어야 합니다. 혹은 ArrayList 에서는 Iterator 인터페이스가 아닌 다른 인터페이스를 사용하기에는 어려움이 따를 수 있습니다.

### 실제 사용 예제

만약 소셜 로그인을 사용한다고 생각해보겠습니다. 생각해보면 Google, facebook, kakao, apple 등 모두 소셜 로그인을 지원하면서 각자의 계정에 맞는 user 정보를 api 로 내려주게 됩니다. 이에 맞춰 아래처럼 클래스와 인터페이스를 정의했습니다.

* `AbstractFactory`: OAuthAPI
* `ConcreteFactory`: KakaoApi, GoogleApi
* `AbstractProduct`: OAuthUser
* `ConcreteProduct`: KakaoUser, GoogleUser
* `Client`: 로그인하는 Client

구현은 자바가 아니라 제가 요즘 주로 사용하는 __typescript__ 로 구현했습니다.

```typescript
// 내부에서 사용할 서비스 유저 (위에는 포함되지 않습니다.)
class ServiceUser {
    id: string;
    name: string;
    email: string;

    constructor(id: string, name: string, email: string) {
        this.id = id;
        this.name = name;
        this.email = email;
    }
}

// AbstractProduct 로 뒤에서 AbstractFactory 가 반환할 인터페이스
interface OAuthUser {
    toServiceUser(): ServiceUser;
}

// ConcreteProduct 로 Client 에서 여기 있는 toServiceUser() 를 사용해서 db 에 저장하게 됩니다.
class KakaoUser implements OAuthUser {
    readonly id: number;
    readonly properties: object;
    readonly kakao_account: object;

    constructor({id, properties, kakaoAccount}) {
        this.id = id;
        this.properties = properties;
        this.kakao_account = kakaoAccount;
    }

    public toServiceUser(): ServiceUser {
        return new ServiceUser(
            this.id.toString(),
            this.properties.nickname,
            this.properties.email,
        );
    }
}

// AbstractFactory 로 AbstractProduct 인 OAuthUser 를 생성하는 연산 정의
interface OAuthAPI {
    getUser(): Promise<OAuthUser>
}

// ConcreteFactory 로 ConcreteProduct 를 생성하는 함수를 구현
class KakaoApi implements OAuthAPI{

    constructor(readonly token: string) {}

    public async getUser(): Promise<OAuthUser> {
        const result = await axios.get("https://kapi.kakao.com/v2/user/me", {
            headers: { Authorization: `Bearer ${this.token}` }
        }); // result 에 name 이 있다 가정

        // set & call kakao url with token
        return new KakaoUser(result.data);
    }

}

// Client 에서는 login 작업을 수행하는데 
// 1. kakao 에서 유저를 가지고와서 (abstract factory 이용)
// 2. 이를 서비스 유저로 변경해서 (abstract product 이용)
// 3. DB 에 저장하고 이를 반환해줍니다.
class Client {

    db: IDBDatabase;

    public async login(api: OAuthAPI): Promise<ServiceUser> {
        const user = await api.getUser();
        const serviceUser = user.toServiceUser();
        await this.db.insert(serviceUser);
        return serviceUser
    }
}
```

보시면 알겠지만 Client 에서는 어떤 소셜로그인이던 상관 없이 `OAuthAPI` 와 
`OAuthUser` 만을 가지고 로그인 처리를 진행하고 있습니다. 다시 말하면 다른 로그인으로 확장할 수 있다는 이야기입니다. 또한 `OAuthAPI` 와 `OAuthUser` 는 밀접하게 연관되어 있는 정보들이기 때문에 추상 팩터리 패턴에 적합하다고 생각하였습니다.

여기서 만약 __Google Login__ 을 구현해야 겠다 싶으면 `ConcreteFactory` 와 `ConcreteProduct` 를 아래와 같이 새로 구현해주면 됩니다. (구글 api 는 실제 api 가 아닙니다.)

```typescript
class GoogleUser implements OAuthUser{
    readonly id: number;
    readonly email: string;
    readonly name: string;
    
    constructor({id, email, name}) {
        this.id = id;
        this.email = email;
        this.name = name;
    }

    toServiceUser(): ServiceUser {
        return new ServiceUser(
            this.id.toString(),
            this.email,
            this.name,
        );
    }
}

class GoogleApi implements OAuthAPI {

    public async getUser(): Promise<OAuthUser> {
        const result = await axios.get("google api 를 넣습니다.");

        // set & call kakao url with token
        return new GoogleUser(result.data);
    }
}
```

이렇게 해서 Abstract Factory 패턴에 대해서 알아봤습니다. 

이번 패턴을 공부하면서 굳이 이렇게 까지 써야하나 싶기도 했습니다. 제가 든 소셜 로그인 예시도 오히려 개발하기 어려워지지 않을까? 하는 생각도 듭니다. 하지만 개인적으로는 __알고 안쓰는 것과 모르고 못쓰는 것__ 에는 차이가 있다고 생각한다는 말과 함께 포스팅 글을 마치도록 하겠습니다.
