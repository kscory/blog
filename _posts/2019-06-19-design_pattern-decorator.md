---
layout: post
comments: true
title:  "[Design Pattern] Decorator 패턴"
date:   2019-06-19
author: Cory
categories: Design_Pattern
permalink : /dev/design-pattern/decorator
tags: design pattern
---

__Decorator Pattern__ 이란 주어진 상황 및 용도에 따라 어떤 객체에 책임을 덧붙이는 패턴입니다.

즉, 런타임 시에 동적으로 객체에 새로운 기능을 추가하거나 변화시켜서 기능을 유연하게 확장할 수 있게 해줍니다. 여기서 데코레이터 패턴은 상속 대신 합성을 사용하기 때문에 서브클래싱 대안으로 사용할 수 있습니다.

여기서 __Component__ 는 Concrete Component 와 Decorator 에서 공통적으로 제공될 기능이 정의된, 클라이언트에서 호출하게 되는 역할을 하며, __Concrete Componenet__ 는 Componenet 를 상속받아 그 기능들을 구현한 클래스입니다. __Decorator__ 는 Component 의 인터페이스를 따르지만 Component 를 래핑해서 새롭게 기능을 제공하며, __Concrete Decorator__ 는 개별적인 기능을 추가하는 기능을 합니다. 

여기서 ConcreteDecorator 클래스는 ConcreteComponent 객체에 대한 참조가 필요한데 Decorator 클래스에서 Component 클래스로의 __합성(composition) 관계__ 를 통해 표현하게 됩니다.

이를 이용하게 되면 디자인 원칙 중 OCP (Open-Closed Principle - 클래스는 확장에 대해서는 열려 있어야 하지만 코드 변경에 대해서는 닫혀 있어야 한다) 를 만족하게 디자인 할 수 있습니다.


<img src="/assets/design/decorator/decorator-01.png" alt="decorator-01">

### 사용하는 이유

사실 디자인 패턴을 공부하다보면 계속해서 나오는 이야기가 '상속' 계층 구조를 사용하여 새로운 기능을 추가하지 않는다는 것입니다. 

또한 종종 런타임 전에 객체가 어떻게 동작해야 하는지 알지 못하는 경우가 있습니다. 가장 쉬운 예가 config 설정인 경우인데, config 파일에 기능에 대한 양식이 정해져 있을 수 있습니다. 

이런 경우 Decorator 패턴을 사용하면 해결할 수 있습니다.

간단하게 상속 구조를 이용한 예시와 데코레이터 패턴을 사용했을 때의 예시를 비교하여 어떻게 바뀌는지 한번 알아보겠습니다.

먼저 기본 기능들을 정의한 인터페이스 입니다.
```java
// Component
interface UserService {
    User setUser(String name, String password);
    User getUser(String name);
    User updateUser(String name, String birth);
}
```

이를 구현한 Concrete Componenet 입니다.

```java
public class UserServiceImpl implements UserService {
    
    UserDao userDao = new UserDao();

    @Override
    public User setUser(String name, String password) {
        User user = new User();
        user.setUsername(name);
        user.setPassword(password);

        User userSaved = userDao.saveUSer(user);
        return userSaved;
    }

    @Override
    public User getUser(String name) {
        User user = userDao.findUserByName(name);
        return user;
    }

    @Override
    public User updateUser(String name, String birth) {
        User user = userDao.updateUserByName(name, birth);
        return user;
    }
}
```

여기서 만약 함수가 실행될때마다 로그를 찍어야 하는 경우가 생겼습니다. 이 때 상속 구조를 이용하면 

```java
public class UserLogs extends UserServiceImpl {

    @Override
    public User setUser(String name, String password) {
        System.out.println("setUser Service invoked!");
        return super.setUser(name, password);
    }

    @Override
    public User getUser(String name) {
        System.out.println("getUser Service invoked!");
        return super.getUser(name);
    }

    @Override
    public User updateUser(String name, String birth) {
        System.out.println("updateUser Service invoked!");
        return super.updateUser(name, birth);
    }
}
```

가 될 것입니다.

그런데 속도 문제로 인하여 로직마다 시간을 체크해야 하는 경우가 생겨 Time 을 찍는 클래스를 하나 더 만들게 되었습니다.

```java
public class UserTimeChecker extends UserLogs {

    @Override
    public User setUser(String name, String password) {
        Long dateStart = System.currentTimeMillis();
        User user = super.setUser(name, password);
        System.out.println(System.currentTimeMillis() - dateStart);
        return user;
    }

    @Override
    public User getUser(String name) {
        Long dateStart = System.currentTimeMillis();
        User user = super.getUser(name);
        System.out.println(System.currentTimeMillis() - dateStart);
        return user;
    }

    @Override
    public User updateUser(String name, String birth) {
        Long dateStart = System.currentTimeMillis();
        User user = super.updateUser(name, birth);
        System.out.println(System.currentTimeMillis() - dateStart);
        return user;
    }
}
```

그런데 갑자기 로그관련한 코드들은 제거해야 하는 경우가 생겼습니다. 그렇다면?? UserTimeCheckerWihoutLogs??? 와 같이 상속을 제거한 클래스를 또 만들어야 할 것입니다. 만약 이런 클래스가 10개가 상속되어 있다?? 하면 유지보수가 힘들 것입니다.

이를 데코레이터 패턴을 사용하게 되면

먼저 Decorator 를 정의합니다.
```java
abstract class UserDecorator implements UserService {

    UserService userService;

    UserDecorator(UserService userService) {
        this.userService = userService;
    }

    @Override
    public User setUser(String name, String password) {
        return userService.setUser(name, password);
    }

    @Override
    public User getUser(String name) {
        return userService.getUser(name);
    }

    @Override
    public User updateUser(String name, String birth) {
        return userService.updateUser(name, birth);
    }
}
```

이를 이용하여 각각의 기능을 Decorator 만을 이용항 만들게 됩니다.

```java
public class UserLogs extends UserDecorator {

    UserLogs(UserService userService) {
        super(userService);
    }

    @Override
    public User setUser(String name, String password) {
        System.out.println("setUser Service invoked!");
        return super.setUser(name, password);
    }

    @Override
    public User getUser(String name) {
        System.out.println("getUser Service invoked!");
        return super.getUser(name);
    }

    @Override
    public User updateUser(String name, String birth) {
        System.out.println("updateUser Service invoked!");
        return super.updateUser(name, birth);
    }
}
```

만약 시간을 재고 싶다?? 하면 데코레이터를 상속해서 이것만 만들어 줍니다.

```java
public class UserTimeChecker extends UserDecorator {

    UserTimeChecker(UserService userService) {
        super(userService);
    }

    @Override
    public User setUser(String name, String password) {
        Long dateStart = System.currentTimeMillis();
        User user = super.setUser(name, password);
        System.out.println(System.currentTimeMillis() - dateStart);
        return user;
    }

    @Override
    public User getUser(String name) {
        Long dateStart = System.currentTimeMillis();
        User user = super.getUser(name);
        System.out.println(System.currentTimeMillis() - dateStart);
        return user;
    }

    @Override
    public User updateUser(String name, String birth) {
        Long dateStart = System.currentTimeMillis();
        User user = super.updateUser(name, birth);
        System.out.println(System.currentTimeMillis() - dateStart);
        return user;
    }
}
```

만약 로그와 time check 를 같이하고 싶다면?? Client 에서 아래를 실행시키면 됩니다.

```java
// Log TimeChecker 둘다 필요한 경우
UserService userService = new UserTimeChecker(new UserLogs(new UserServiceImpl()));

// TimeChecker 만 필요한 경우
UserService userService1 = new UserTimeChecker(new UserServiceImpl());

// Log 만 필요한 경우
UserService userService2 = new UserLogs(new UserServiceImpl());
```

즉, Clinet 에서 동적으로 원하는 기능들을 추가시킬 수 있게 됩니다.

### 장점과 단점

그럼 Decorator 패턴의 장단점을 알아보겠습니다.

장점
1. 기존 클래스의 변경없이 유연하게 기능을 확장할 수 있습니다. (ocp 원칙)
2. 클래스 계층 구조의 크기와 복잡도가 줄어들며 확장이 용이해질 수 있습니다.
3. 런타임에 부가적인 기능을 부가하기 때문에 런타임 설정의 문제를 해결할 수 있습니다.

단점
1. 각각 데코레이터 클래스들이 어떤 기능을 수행하는지 알고 있어야 에러 없이 사용할 수 있습니다.
2. 잡다한 여러 클래스들이 많이 생길 수 있습니다.
3. 코드를 짜다보면 Decorator가 적용되는 순서에 민감해지는 경우가 생길 수 있습니다. (java io 의 경우 순서대로 Decorating 을 해야 합니다)

### 실제 사용 예제

사실 Spring 에서는 데코레이터 패턴을 활용한 스프링 AOP 기능이 있습니다. 위의 로그 혹은 Time Check 을 Check 하는 기능을 spring aop 를 이용해서 쉽게 나타낼 수 있습니다. Time 을 체크하는 예제만 간단하게 보겠습니다.

먼저 사용할 객체를 만듭니다.
```java
@Component
public class UserServiceImpl implements UserService {

    @Autowired
    UserDao userDao;

    @Override
    public User setUser(String name, String password) {
        User user = new User();
        user.setUsername(name);
        user.setPassword(password);

        User userSaved = userDao.saveUSer(user);
        return userSaved;
    }

    @Override
    public User getUser(String name) {
        User user = userDao.findUserByName(name);
        return user;
    }

    @Override
    public User updateUser(String name, String birth) {
        User user = userDao.updateUserByName(name, birth);
        return user;
    }
}
```

그 후 Aspect 를 만듭니다.

```java
@Component
@Aspect
public class UserAspect {

    @Around("execution(* com.kyung.projectboard.sample.*.UserServiceImpl.*(..))")
    public Object logPerf(ProceedingJoinPoint pjp) throws Throwable{
        long begin = System.currentTimeMillis();
        Object retVal = pjp.proceed();
        System.out.println(System.currentTimeMillis() - begin);
        return retVal;
    }
}
```

정말 간단해 졌습니다!! 위의 Aspect 의 경우 UserService 의 모든 메서드에 적용시키겠다는 뜻이며, Around 를 사용할 경우 메서드 전후로 기능을 쓰겠다는 겁니다. AOP 를 이야기하면 사실 더 길어질 것 같으니 여기까지 하겠습니다.

참고로 프런트에서 이 패턴을 적극(?) 적용한 프레임워크가 'React' 일텐데요 바로 __High Order Component__ 입니다. 궁금하신 분들은 한번 찾아보시면 좋을 것 같습니다. ㅎㅎ

이렇게 해서 Decorator 패턴에 대해서 알아봤습니다. 

사실 AOP 를 이렇게 막 적용하면 안됩니다. 왜냐하면 협업시 어떻게 적용되는지 서로 다른 부서간에 모를 수도 있기 때문입니다. 데코레이터 패턴도 마찬가지라고 생각됩니다. 

개인적으로 디자인 패턴은 협업을 잘 하기위한 일종의 규약(?) 하지만 선조(?) 들의 지혜가 담겨있는 철학이라고 할 수 있을 것 같습니다. 말하고 싶은 것은... 너무 디자인 패턴에 목매여서 개발진행을 어렵게 하지 않았으면 하는 바램입니다.
