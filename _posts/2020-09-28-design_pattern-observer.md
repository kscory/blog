---
layout: post
comments: true
title:  "[Design Pattern] Observer 패턴"
date:   2020-09-28
author: Cory
categories: Design_Pattern
permalink : /dev/design-pattern/observer
tags: design pattern
---

__Observer Pattern__ 이란 객체의 상태 변화를 관찰하는 Observer 들의 목록을 객체에 등록하여 상태 변화가 있을 때마다 메서드 등을 통해 객체가 직접 목록의 각 옵저버에게 통지하도록 하는 디자인 패턴입니다. (by [wikipedia](https://ko.wikipedia.org/wiki/%EC%98%B5%EC%84%9C%EB%B2%84_%ED%8C%A8%ED%84%B4))

즉, 상태를 가지고 있는 주체 객체와 상태의 변경을 알아야 하는 관찰 객체가 존재하며 이들의 관계는 1:1 혹은 1:N 이 될 수 있는데 이 때, 객체들 사이에서 다양한 처리를 할 수 있도록 해주는 패턴을 의미합니다. 이 때, 한 객체의 상태가 바뀌면 그 객체에 의존하는 다른 객체들한테 연락이 가고 자동으로 내용이 갱신되는 방식으로 one-to-many 의존성을 정의합니다.

![](https://lh3.googleusercontent.com/pw/ACtC-3e5X4ct9X0RBI82e4JiEhQC55T7eqTGYLP8px5NQtsZ91cmxVT-m0wgOTF9-LgDLtjnNa5MS28qIrTkefIPk8sj-eByK68y_j4yR_EXi7Dj2G-fO7YY6Q7IGPJZQacAsJ6ay2dcMY13BpXDfIGX2nR-=w899-h372-no?authuser=0)
<div style="text-align: center;">출처: https://en.wikipedia.org/wiki/Observer_pattern</div><br>

구성 요소에는 다음과 같은 요소가 있습니다.

* __Subject__`: Observer 를 알고 있는 주체입니다. 이는 Observer 를 등록하고 제거하는 데 필요한 인터페이스를 정의합니다.
* __Observer__: Subject 에서 변화했다고 알렸을때 갱신해야하는데 필요한 인터페이스를 정의합니다.
* __ConcreteSubject__: 객체에게 (Observer 들에게) 알려줘야할 상태를 저장하고, notify 해야할 함수를 만들도록 합니다.
* __ConcreteObserver__: noti 를 받았을때 행동할 로직을 작성합니다. 

간단하게 사용하는 방식에 대해서 알아보겠습니다.

아래 사진을 보면 알겠지만, __Observer__ 를 구현할 인터페이스를 정의하고 각각의 __Client__ 는 이를 상속받으면서 하고 싶은 작업을 정의합니다. 그리고 __Subject__(server) 에서 Observer 에 이벤트가 발생했음을 통히합니다.

![](https://lh3.googleusercontent.com/pw/ACtC-3dvgOHqJAdfaoE61F3_wcnbFkGOGTPu0QXdEfid_UyXxddOdUHkKruc-tytJSBEnxe2plEXeoRx2L87AFWrZCzqXsBx9vUgrmaq5mAqG-AgkxpnIxHzo5MdpHfkBoZyCGPMcnLQaLn2xRXrDEeFklpU=w756-h439-no?authuser=0)

이제 코드로 구현해보겠습니다.

먼저 인터페이스를 정의하고 이를 상속받아 client 를 구현합니다.
```java
// Observer
// Observer
public interface Observer {
    void noti();
}

// ConcreteObserver
class Client1 implements Observer {
    private String title;
    public Client1(String title) {
        this.title = title;
    }

    @Override
    public void noti() {
        System.out.println("클라이언트 " + title + "에 변경사항이 반영됨");
    }
}

// ConcreteObserver
class Client2 implements Observer{
    private String title;
    public Client2(String title) {
        this.title = title;
    }

    @Override
    public void noti() {
        System.out.println("클라이언트 " + title + "에 변경사항이 반영됨");
    }
}
```

그 뒤 Subject 클래스를 생성합니다. 여기서 Observer 들을 가지고 있는 subject 가 있습니다.

```java
// Subject
public interface Subject {
    void attach(Observer observer);
    void detach(Observer observer);
    void notifyObserver();
}

// ConcreteSubject
class ConcreteSubject implements Subject {
    List<Observer> clients = new ArrayList<>();

    @Override
    public void attach(Observer observer) {
        this.clients.add(observer);
    }

    @Override
    public void detach(Observer observer) {
        this.clients.remove(observer);
    }

    @Override
    public void notifyObserver() {
        for(Observer observer : clients) {
            observer.noti();
        }
        System.out.println("[Subject] 메세지를 전송하였습니다.");
    }
}
```

그리고 클라이언트 등록을 위한 데몬 클래스를 정의합니다. 이 클래스는 디자인 패턴 속성이 아니라 단순히 테스트를 위한 용도이므로 간단하게 확인하고 넘어가면 됩니다. 단순히 observer 에 notify 해주는 기능, 등록하는 기능이 있습니다.

```java
public class ClientDaemon extends Thread{
    private Subject subject;
    Random random = new Random();
    public ClientDaemon(Subject subject) {
        this.subject = subject;
    }
    public void run() {
        int count = 0;
        while(true) {
            // 랜덤으로 notify 해주도록 설정
            if ((random.nextInt(10)+1) % 3 == 0) {
                subject.notifyObserver();
            }

            // 카운트마다 다르게 client 를 attach !
            if(count %2==0) {
                subject.attach(new Client1(count+""));
            } else {
                subject.attach(new Client2(count+""));
            }
            count++;

            try {
                Thread.sleep(5000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

이제 만든 클래스를 실행합니다.

```java
public class Application {
    public static void main(String[] args) {
        Subject subject = new ConcreteSubject();
        ClientDaemon daemon = new ClientDaemon(subject);
        daemon.start();
    }
}

```

이를 실행하면 아래와 같은 결과를 얻을 수 있습니다.

![](https://lh3.googleusercontent.com/pw/ACtC-3csdH4dVyygIEgzw8rlAZWuM80bqVMJEpibcxjMyruoVkCoi2w0t4aHuExk2hagiDKjeChnBwHuDfUThvsEc8lsrqIulCMt6BRTYqLbib15mtHJ5Gt3FD8oXhsbNAGqavDVhGA3g_knPaX6w93XpKaU=w1680-h538-no?authuser=0)

### 사용하는 이유

옵저버 패턴을 사용하면, subject 와 observer 가 느슨하게 결합되어 있는 객체 디자인을 제공하 수 있습니다. 그럼으로 인해 옵저버의 추가, 제거가 자유로워지고, 새로운 형식의 옵저버를 추가하기도 쉬워집니다. 또한 한 옵저버에서 변경이 일어났다고 해서 다른 옵저버에 영향을 주는 일이 사라지게 됩니다.

사실 대부분의 이벤트들이 옵저버 패턴 방식으로 사용되고 있습니다. 예를 들면 __Android 의 Event Listenr__, __node.js 의 event Loop__, __브라우저의 Event Handler__ 등이 그 예입니다. 

예를 들어 Button 이 클릭되었다 할때, 거기에 Event 가 달리고 버튼이 클릭될때마다 특정 행동을 하도록 Observer 에게 알리는 패턴이 사용되곤 합니다.

### 장점과 단점

그럼 Observer 패턴의 장단점을 알아보겠습니다.

장점
1. 느슨한 결합으로 인하여 시스템이 유연해지고, 객체간의 의존성을 제거할 수 있습니다.
2. pull 방식이 아닌 push 방식을 사용함으로써 직관적으로 이해하기 쉽습니다.

단점
1. Thread safe 하지 않아 구독을 신청/취소하는 동안 원하는 결괏값을 얻기 힘들수도 있습니다.
2. observer 를 제때 제거해주지 않으면 메모리 누수가 일어날 수 있습니다.
3. 너무 많이 사용하게 되면, 상태 관리가 힘들 수 있습니다.
4. 비동기 방식이기 때문에 이벤트 구독을 원하는 순서대로 받지 못할 수 있습니다.

### 실제 사용 예제

Node.js 의 이벤트 루프를 활용하면 기존 콜백패턴과는 다른 방법으로 개발할 수 있습니다. 특히, Node.js 에서는 많은 모듈들을 사용하는데 이 모듈(클래스)간의 결합도를 끊을 수 있어 관심사의 분리 원칙을 깔끔하게 지키도록 설계할 수 있습니다. 

우선 Observer 패턴을 사용하지 않았을 경우 어떤 식으로 개발되었을지 설명드리겠습니다. 언어는 타입스크립트 기반이고 유저가 회원가입 한다는 가정을 하도록 하겠습니다.

```typescript
class TrackingService {
    async track(user: User) {
        gaAnalytics.event('user_signup', user);

        eventTracker.track('user_signup', user);
    }
}

class EmailService {
    async sendEmail(user: User) {
        await EmailClient.send('user_signup', user);
    }
}

class SignUpService {

    constructor(
        private readonly emailService: EmailService,
        private readonly trackingService: TrackingService,
    ) {}

    async signup(name: string, nickname: string): Promise<void> {
        const user = new User(name, nickname);
        await user.save();
        
        await this.emailService.sendEmail(user);
        await this.trackingService.track(user);
    };

}
```

사실 단순하게 본다면 알아보기 쉬울 수 있지만 이 클래스는 "email 을 보내는 서비스", "tracking 을 하는 서비스" 와 같은 signup 을 위한 로직과는 동떨어져 있는 서비스를 이용하고 있는 것을 알 수 있습니다. 즉, SignUp 에만 집중된 클래스들이 아니라 다른 서비스 클래스들을 사용하고 있다는 점에서 이 클래스는 __단일 책임 원칙__ 에 위반된 클래스라고 생각할 수 있습니다.

따라서 여기서 저희가 원하는 작업은 `SignUpService` 에서 `TrackingService` 와 `EmailService` 를 분리하는 일입니다. 이를 위해 아래처럼 구성요소들을 정의해봤습니다.

* __Subject__: EmitterSubject
* __Observer__: EventEmitter (Nodejs 에 내장되어 있습니다.)
* __ConcreteSubject__: EmitterSubjectImpl
* __ConcreteObserver__: GAEmitter, NotificationEmitter

그럼 이를 코드로 나타내게 되면 (하나하나 클래스(파일) 별로 분리해서 작성하도록 하겠습니다.)

우선 tracking 하는 서비스 클래스는

```typescript
const TrackingEmitter = new EventEmitter();

TrackingEmitter.on('user_signup', (user: User) => {
    gaAnalytics.event('user_signup', user);

    eventTracker.track('user_signup', user);
});
```

email 을 담당하는 서비스 클래스는

```typescript
const EmailEmitter = new EventEmitter();

EmailEmitter.on('user_signup', async (user: User) => {
    await EmailClient.send('user_signup', user);
});
```

마지막으로 이 emitter 들을 담고 한번에 푸시하도록 해주는 subject 클래스는 아래와 같습니다. (여기서 interface 를 만들 필요가 있는지는 아직 잘 모르겠습니다.)

```typescript
interface EmitterSubject {
    emit(event: string): void;
}

export class EmitterSubjectImpl implements EmitterSubject {
    readonly emitters: EventEmitter[];

    constructor(emitters: EventEmitter[]) {
        this.emitters = emitters;
    }

    emit(event: string): void {
        for (const emitter of this.emitters) {
            emitter.emit('user_signup')
        }
    }
}

export default new EmitterSubjectImpl([
    EmailEmitter,
    TrackingEmitter,
])
```

이렇게 하면 이제 SingupServce 는 다음과 같이 작성할 수 있습니다.

```typescript
class SignUpService {

    constructor(
        private readonly emitterSubject: EmitterSubjectImpl
    ) {}

    async signup(name: string, nickname: string): Promise<void> {
        const user = new User(name, nickname);
        await user.save();

        // 이벤트를 이용해서 관심사의 분리를 적용
        this.emitterSubject.emit('user_signup');
    };

}
```

코드는 조금 더 많아졌을지도 모르지만 SignUpService 에서는 이제 EmailService, TrackingService 에서 어떤 일을 하는지 모르는 상태가 됩니다. 즉, 관심사의 분리가 잘 적용되어 있다는 점을 알 수 있습니다. 

만약, 요구사항이 "회원가입때 유저에게 메일뿐 아니라 문자도 보내주세요" 라고 바뀌게 되었다고 생각해보겠습니다. 옵저버 패턴을 적용하지 않았을 경우에는 `SignUpService` 에서 `EmailService` 뿐만 아니라 `SmsService` 같은 또다른 클래스를 상속받아서 문자를 보내는 로직을 작성해야 할 것입니다. __하지만__ 옵저버 패턴을 적용하게 되면 이메일을 보내던, 문자를 보내던, 둘 다 보내던 상관없이 `SignUpService` 에서는 그냥 __user_signup__ 이라는 이벤트만 방출하고 다른 클래스에서 이 이벤트를 받아서 처리하기만 하면 된다는 점입니다. 또한 `SignUpService` 가 아닌 다른 클래스도 __user_signup__ 이벤트가 어디서 온 이벤트인지는 모르고 "이 이벤트에는 이런 로직을 진행할거다" 라고 작성하기만 하면 된다는 뜻입니다.

다만, 주의할 점은 Node.js 에서 이 패턴을 사용할 때 반드시 __에러처리__ 에 유념해서 개발하셔야 합니다. 비동기로 일어나는 에러기 때문에 api 에서는 response 로써 다루기가 까다로울 수 있습니다.

### 마무리

이렇게 해서 Observer 패턴에 대해서 알아봤습니다. 

Observer 패턴은 정말 많은 프레임워크에 녹아들어가 있는 패턴인 것 같습니다. 특히나 웹, 모바일의 경우 이 패턴을 정말 많이 사용하곤 합니다. 또한 최근에는 Single Thread 프레임워크들이 유행하면서 Event Loop 기반으로 옵저버 패턴들이 더 많이 사용되는 것 같습니다. 

하지만 옵저버 패턴이 만능은 아니고, 오히려 더 복잡하게 프로그램을 짜도록 만들게 될 수 있다는 점 유의하면서 Happy Hacking 하시길 바라겠습니다 ^^
