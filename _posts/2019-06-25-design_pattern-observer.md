---
layout: post
comments: true
title:  "[Design Pattern] Observer 패턴"
date:   2019-06-25
author: Cory
categories: Design_Pattern
permalink : /dev/design-pattern/observer
tags: design pattern
---

__Observer Pattern__ 이란 객체의 상태 변화를 관찰하는 Observer 들의 목록을 객체에 등록하여 상태 변화가 있을 때마다 메서드 등을 통해 객체가 직접 목록의 각 옵저버에게 통지하도록 하는 디자인 패턴입니다. (by [wikipedia](https://ko.wikipedia.org/wiki/%EC%98%B5%EC%84%9C%EB%B2%84_%ED%8C%A8%ED%84%B4))

즉, 상태를 가지고 있는 주체 객체와 상태의 변경을 알아야 하는 관찰 객체가 존재하며 이들의 관계는 1:1 혹은 1:N 이 될 수 있는데 이 때, 객체들 사이에서 다양한 처리를 할 수 있도록 해주는 패턴을 의미합니다. 이 때, 한 객체의 상태가 바뀌면 그 객체에 의존하는 다른 객체들한테 연락이 가고 자동으로 내용이 갱신되는 방식으로 one-to-many 의존성을 정의합니다.

<img src="/assets/design/observer/observer-01.png" alt="observer-01">

(사진 출처: [wikipedia](https://upload.wikimedia.org/wikipedia/commons/thumb/8/8d/Observer.svg/1920px-Observer.svg.png))

간단하게 사용하는 방식에 대해서 알아보겠습니다.

아래 사진을 보면 알겠지만, __Observer__ 를 구현할 인터페이스를 정의하고 각각의 __Client__ 는 이를 상속받으면서 하고 싶은 작업을 정의합니다. 그리고 __Subject__(server) 에서 Observer 에 이벤트가 발생했음을 통히합니다.

<img src="/assets/design/observer/observer-02.png" alt="observer-02">

이제 코드로 구현해보겠습니다.

먼저 인터페이스를 정의하고 이를 상속받아 client 를 구현합니다.
```java
public interface IObserver {
    void noti();
}

class Client1 implements IObserver {
    private String title;
    public Client1(String title) {
        this.title = title;
    }
    @Override
    public void noti() {
        System.out.println("클라이언트 " + title + "에 변경사항이 반영됨");
    }
}

class Client2 implements IObserver{
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

그 뒤 Subject 클래스를 Thread 를 상속받아 생성합니다.

```java
public class Subject extends Thread {
    List<IObserver> clients = new ArrayList<>();

    public void run() {
        Random random = new Random();

        while(true) {
            for(IObserver observer : clients) {
                observer.noti();
            }
            System.out.println("[Subject] 메세지를 전송하였습니다.");

            // 비주기적 갱신을 위한 테스트 코드
            try {
                Thread.sleep((random.nextInt(10)+1)*1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

그리고 클라이언트 등록을 위한 데몬 클래스를 정의합니다.

```java
class ClientDaemon extends Thread{
    private Subject subject;
    public ClientDaemon(Subject subject) {
        this.subject = subject;
    }
    public void run() {
        int count = 0;
        while(true) {
            if(count %2==0) {
                subject.clients.add(new Client1(count+""));
            } else {
                subject.clients.add(new Client2(count+""));
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
public class DPMain {

	public static void main(String[] args) {
		Subject subject = new Subject();
		subject.start();

		ClientDeamon deamon = new ClientDeamon(subject);
		deamon.start();
	}

}
```

이를 실행하면 아래와 같은 결과를 얻을 수 있습니다.

<img src="/assets/design/observer/observer-03.png" alt="observer-03">

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

### 리액티브 프로그래밍??
-> 나중에 따로 작성하도록 하겠습니다.

이렇게 해서 Observer 패턴에 대해서 알아봤습니다. 

요즘 마이크로 서비스가 유행함에 따라 observer 패턴이 기본이 되는 Event-based 프로그래밍 방식이 큰 인기를 끌고 있는데, 사실 저자의 경우 스프링 프레임워크 내에서는 아직 이를 접해보지는 못했습니다. 조금 더 익숙해지면 이를 한번 더 다뤄보는 시간을 가져볼까 합니다.