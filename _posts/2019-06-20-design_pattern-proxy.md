---
layout: post
comments: true
title:  "[Design Pattern] Proxy 패턴"
date:   2019-06-20
author: Cory
categories: Design_Pattern
permalink : /dev/design-pattern/proxy
tags: design pattern
---

__Proxy Pattern__ 이란 말 그대로 객체를 '대리자(proxy)' 객체를 통해 접근하는 패턴입니다.

즉, __Real Subject__ 는 실제 기능을 수행하는 객체가 되며, 이는 __Subject__ 인터페이스를 상속받아서 구현됩니다. 여기서 __Proxy__ 객체 또한 __Subject__ 인터페이스를 상속받음으로써 __RealSubject__ 와 상호작용할 수 있게 됩니다. 그 때, Proxy 객체는 Real Subject 를 그냥 'by pass' 로 사용하여 대신 사용하는 것처럼 구현하게 됩니다.

<img src="/assets/design/proxy/proxy-01.jpg" alt="proxy-01">

예를 들면 아래와 같이 코딩할 수 있을 것입니다.

```java
// Subject
public interface SampleSubject {
    void request();
}

// Real Subject
public class RealSampleSubject implements SampleSubject {
    @Override
    public void request() {
        System.out.println("Request 발생!!!");
    }
}

// Proxy
public class ProxySubject implements SampleSubject {

    SampleSubject sampleSubject;

    public ProxySubject(SampleSubject sampleSubject) {
        this.sampleSubject = sampleSubject;
    }

    @Override
    public void request() {
        // proxy
        sampleSubject.request();
    }
}

// Client
public class Client {

    SampleSubject sampleSubjectProxy;

    public Client() {
        SampleSubject sampleSubject = new RealSampleSubject();
        sampleSubjectProxy = new ProxySubject(sampleSubject);
    }

    public void callRequest() {
        sampleSubjectProxy.request();
    }
}
```

즉, RealSubject 를 Proxy 에서 가지고 있으면서 단순히 by pass 로 대리로 실행시켜 주는 것을 볼 수 있습니다.

### 사용하는 이유

보통 프록시를 쓰는 이유는 보통 두 가지 이유로 사용된니다.

1. lazy instantiation, 필요할 때 객체를 추가시키고 싶을 때 (Virtual Proxy)
2. 보안 문제로 인하여 특정 메서드의 접근을 제어하고 싶지 않을 때 (Protection Proxy)

먼저 __lazy instantiation__ 에 대해서 말씀드리겠습니다. lazy instantitation 의 가장 좋은 예는 아마 데이터베이스에서 query 를 날릴때 일것 같습니다.

아래와 같이 User, Board entity 가 있다고 생각해 보겠습니다. (JPA 를 사용했습니다.)

```java
// User
@Data
@Entity
public class User implements UserDetails {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Email(message = "Username needs to be an email")
    @NotBlank(message = "username is required")
    @Column(unique = true)
    private String username;

    @NotBlank(message = "Password field is required")
    private String password;

    @OneToMany
    private List<Boards> boards = new ArrayList<>();
}

// Board
@Data
@Entity
public class Board {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;

    private String content;

    @ManyToOne
    private User user;
}
```

이 때 아래와 같은 프로필을 불러 올때, Board 테이블과 join 해서 Board 정보를 가져올 필요가 있을까요??

<img src="/assets/design/proxy/proxy-02.jpg" alt="proxy-02" style="width:60%;">

아마 없을 것입니다. 그리고 이 사람의 게시글을 보고 싶을때, 그 때 불러오면 될 것입니다.

그럼 여기서 User 클래스의 Board 객체를 아래와 같이 변경하면 Board 가 필요한 곳에서 가져오게 됩니다.

```java
@OneToMany(fetch = FetchType.LAZY) // 사실 default 값이 lazy 입니다.
private List<Boards> boards = new ArrayList<>();
```

JPA 에서 이렇게 지연로딩을 실행할 때 Proxy 를 사용하고 있습니다.

`※ JPA 에서 fetch 를 'FetchType.LAZY' 로 하면 lazy 로 가져오고, 'FetchType.Eager' 로 하면 한번에 가져오게 됩니다.`

그럼 다음으로 Protection Proxy(보호 프록시) 에 대해서 알아보겠습니다. 아래는 'React' 로 작성된 Router 관련 코드입니다.

이를 보면 유효한 인증이면 그대로 원래대로 Proxy 를 만들어내며, 유효하지 않은 경우 login 페이지로 넘겨버리는 코드입니다.

```javascript
import React from "react";
import {Redirect, Route, RouteProps} from "react-router-dom";
import {RootState} from "../reducers";

const SecuredRoute = ({component: Component, isValid, ...otherProps}) => (
    <Route
        {...otherProps}
        render={props =>
            isValid ? (
                <Component {...props}/>
            ) : (
                <Redirect to="/login"/>
            )
        }
    />
);
```

즉, 특정한 상황에서 외부에 공개하고 싶지 않을 때, Proxy 를 사용해서 방어할 수 있게 됩니다.

### 장점과 단점

그럼 Proxy 패턴의 장단점을 알아보겠습니다.

장점
1. lazy instantiation 을 이용하여 성능을 최적화 시킬 수 있습니다.
2. 기존 클래스의 변경없이 유연하게 기능을 확장할 수 있습니다. (ocp 원칙)

단점
1. 이미 객체가 생성되었을 때, Proxy 를 통한 접근은 오히려 오베헤드가 될 가능성이 있으므로 주의해서 개발해야 합니다.
2. 너무 많은 proxy 들은 코드 가독성을 오히려 망칠 수 있습니다.

### Decorator 패턴과의 차이점??

Decorator 패턴과 Proxy 패턴은 큰 차이점은 존재하지 않습니다. 다만, 사용하는 목적이 달라질 뿐입니다.

Decorator 패턴의 경우 주요 기능에 __무언가를 추가__ 하고 싶을때, 사용됩니다. 이전에서 예를 들었듯이, Log 를 찍고 싶다던가, 함수가 실행되는 시간을 계산하고 싶다던가 할때 사용합니다.

반면 Proxy 패턴의 목적은 주요기능을 어떻게 __컨트롤__ 할 것인가에 있습니다. 같은 기능을 수행하지만, 특정 유저는 막고싶은 경우(Protection), 똑같이 호출하지만 필요할때, 나중에 호출하고 싶은 경우(Virtual) 등입니다.

AOP 의 경우를 생각해볼까요?? 만약, 바로 전 예처럼 ([데코레이터 패턴](https://lee-kyungseok.github.io/dev/design-pattern/decorator)) 시간을 측정하고 싶다? 하면 데코레이터 패턴을 사용하게 된 것입니다.

하지만 lazy 하게  Transaction 을 발생시킬거다? 하면 프록시 패턴을 사용한 것입니다.

애매하긴 하지만, 같은 형식이라도 쓰이는 목적에 따라 달라질 수 있습니다.

이렇게 해서 Proxy 패턴에 대해서 알아봤습니다. 

이번에 공부하면서 느낀점은.. 디자인 패턴은 코딩을 잘 하기 위해서 만들어진 건데, 이로 인해서 코딩을 하기 더 힘들어진다? 하면 너무 목매여서 하지 않아도 될 것 같다는 생각이 들었습니다. 디자인 패턴은 말 그대로 패턴일 뿐, 자신의 서비스에 맞는 패턴 혹은 팀원끼리 함께 이해할 수 있는 패턴이 있다면 그걸 사용하는게 더 낫지 않을까 합니다.
